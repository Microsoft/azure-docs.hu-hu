---
title: Üzenet-átadási felület beállítása HPC-Azure Virtual Machineshoz | Microsoft Docs
description: Ismerje meg, hogyan állíthatja be az MPI-t a HPC számára az Azure-ban.
author: vermagit
ms.service: virtual-machines
ms.subservice: hpc
ms.topic: article
ms.date: 08/06/2020
ms.author: amverma
ms.reviewer: cynthn
ms.openlocfilehash: 9804ed23da4cb9ccbb7515cec03fcc9b4147f749
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/02/2021
ms.locfileid: "101673266"
---
# <a name="set-up-message-passing-interface-for-hpc"></a>Üzenet küldési felületének beállítása HPC-hez

A [Message Passing Interface (MPI)](https://en.wikipedia.org/wiki/Message_Passing_Interface) egy nyílt könyvtár és egy de facto standard az elosztott memória párhuzamos. Általában számos HPC-munkaterheléshez használják. A HPC-munkaterhelések a RDMA-t [támogató](../../sizes-hpc.md#rdma-capable-instances) [H-sorozatú](../../sizes-hpc.md) és [N sorozatú](../../sizes-gpu.md) virtuális gépeken az MPI használatával kommunikálhatnak az alacsony késésű és a nagy sávszélességű InfiniBand hálózaton.

Az SR-IOV engedélyezett virtuálisgép-méretek az Azure-ban (HBv2, HB, HC, NCv3, NDv2) lehetővé teszik szinte bármely MPI-íz használatát a Mellanox OFED-mel. A nem SR-IOV-kompatibilis virtuális gépeken támogatott MPI-implementációk a Microsoft Network Direct (ND) felületet használják a virtuális gépek közötti kommunikációhoz. Ezért csak a Microsoft MPI (MS-MPI) 2012 R2 vagy újabb, illetve az Intel MPI 5. x verziói támogatottak. Az Intel MPI runtime library újabb verziói (2017, 2018) vagy esetleg nem kompatibilisek az Azure RDMA-illesztőprogramokkal.

Az SR-IOV-kompatibilis RDMA-kompatibilis [virtuális gépek](../../sizes-hpc.md#rdma-capable-instances)esetében a [CentOS-HPC 7,6-es vagy újabb](https://techcommunity.microsoft.com/t5/Azure-Compute/CentOS-HPC-VM-Image-for-SR-IOV-enabled-Azure-HPC-VMs/ba-p/665557) verziójú virtuálisgép-lemezképek optimalizáltak és előre betöltve vannak a RDMA és a különböző gyakran használt MPI-kódtárak és tudományos számítástechnikai csomagok OFED-illesztőprogramjaival, és a lehető legegyszerűbbek a kezdéshez.

Bár a példák a RHEL/CentOS-re vonatkoznak, de a lépések általánosak, és bármilyen kompatibilis linuxos operációs rendszerhez használhatók, mint például az Ubuntu (16,04, 18,04 19,04, 20,04) és a SLES (12 SP4 és 15). További példák a más MPI-implementációk más disztribúciókban való beállítására a [azhpc-lemezképek](https://github.com/Azure/azhpc-images/blob/master/ubuntu/ubuntu-18.x/ubuntu-18.04-hpc/install_mpis.sh)tárházában.

> [!NOTE]
> Az IOV-kompatibilis virtuális gépeken futó MPI-feladatok futtatásához a partíciós kulcsokat (p-Keys) kell beállítani a bérlőn az elkülönítés és a biztonság érdekében. A p-Key értékek meghatározásához és az MPI-feladatok helyes beállításához kövesse a [partíciós kulcsok felderítése](#discover-partition-keys) című szakasz lépéseit.

## <a name="ucx"></a>UCX

Az [egységes kommunikációs X (UCX)](https://github.com/openucx/ucx) a HPC kommunikációs API-jai keretrendszere. A InfiniBand-en keresztüli MPI-kommunikációra van optimalizálva, és számos MPI-implementációval működik, mint például a OpenMPI és a MPICH.

```bash
wget https://github.com/openucx/ucx/releases/download/v1.4.0/ucx-1.4.0.tar.gz
tar -xvf ucx-1.4.0.tar.gz
cd ucx-1.4.0
./configure --prefix=<ucx-install-path>
make -j 8 && make install
```

## <a name="hpc-x"></a>HPC-X

A [HPC-X szoftver eszközkészlete](https://www.mellanox.com/products/hpc-x-toolkit) UCX és HCOLL tartalmaz.

```bash
HPCX_VERSION="v2.6.0"
HPCX_DOWNLOAD_URL=https://azhpcstor.blob.core.windows.net/azhpc-images-store/hpcx-v2.6.0-gcc-MLNX_OFED_LINUX-5.0-1.0.0.0-redhat7.7-x86_64.tbz
get --retry-connrefused --tries=3 --waitretry=5 $HPCX_DOWNLOAD_URL
tar -xvf hpcx-${HPCX_VERSION}-gcc-MLNX_OFED_LINUX-5.0-1.0.0.0-redhat7.7-x86_64.tbz
mv hpcx-${HPCX_VERSION}-gcc-MLNX_OFED_LINUX-5.0-1.0.0.0-redhat7.7-x86_64 ${INSTALL_PREFIX}
HPCX_PATH=${INSTALL_PREFIX}/hpcx-${HPCX_VERSION}-gcc-MLNX_OFED_LINUX-5.0-1.0.0.0-redhat7.7-x86_64
```

HPC-X futtatása

```bash
${HPCX_PATH}mpirun -np 2 --map-by ppr:2:node -x UCX_TLS=rc ${HPCX_PATH}/ompi/tests/osu-micro-benchmarks-5.3.2/osu_latency
```

## <a name="openmpi"></a>OpenMPI

Telepítse a UCX a fent leírtak szerint. A HCOLL a [HPC-X szoftver eszközkészletének](https://www.mellanox.com/products/hpc-x-toolkit) része, és nem igényel speciális telepítést.

Telepítse a OpenMPI a tárházban elérhető csomagokból.

```bash
sudo yum install –y openmpi
```

OpenMPI létrehozása.

```bash
OMPI_VERSION="4.0.3"
OMPI_DOWNLOAD_URL=https://download.open-mpi.org/release/open-mpi/v4.0/openmpi-${OMPI_VERSION}.tar.gz
wget --retry-connrefused --tries=3 --waitretry=5 $OMPI_DOWNLOAD_URL
tar -xvf openmpi-${OMPI_VERSION}.tar.gz
cd openmpi-${OMPI_VERSION}
./configure --prefix=${INSTALL_PREFIX}/openmpi-${OMPI_VERSION} --with-ucx=${UCX_PATH} --with-hcoll=${HCOLL_PATH} --enable-mpirun-prefix-by-default --with-platform=contrib/platform/mellanox/optimized && make -j$(nproc) && make install
```

Futtassa a OpenMPI.

```bash
${INSTALL_PREFIX}/bin/mpirun -np 2 --map-by node --hostfile ~/hostfile -mca pml ucx --mca btl ^vader,tcp,openib -x UCX_NET_DEVICES=mlx5_0:1  -x UCX_IB_PKEY=0x0003  ./osu_latency
```

Tekintse meg a fentiekben említett partíciós kulcsot.

## <a name="intel-mpi"></a>Intel MPI

Töltse le az [Intel MPI](https://software.intel.com/mpi-library/choose-download)választható verzióját. Módosítsa a I_MPI_FABRICS környezeti változót a verziótól függően. Az Intel MPI 2018 esetében használja a `I_MPI_FABRICS=shm:ofa` és a for 2019 használatát `I_MPI_FABRICS=shm:ofi` .

### <a name="non-sr-iov-vms"></a>Nem SR-IOV virtuális gépek
A nem SR-IOV virtuális gépek esetében az 5. x futtatókörnyezet [ingyenes próbaverziójának](https://registrationcenter.intel.com/en/forms/?productid=1740) letöltésére példa a következő:
```bash
wget http://registrationcenter-download.intel.com/akdlm/irc_nas/tec/9278/l_mpi_p_5.1.3.223.tgz
```
A telepítési lépésekért tekintse meg az [Intel MPI-könyvtár telepítési útmutatóját](https://registrationcenter-download.intel.com/akdlm/irc_nas/1718/INSTALL.html?lang=en&fileExt=.html).
Szükség esetén érdemes lehet engedélyezni a ptrace a nem gyökérszintű, nem hibakereső folyamatokhoz (az Intel MPI legújabb verzióihoz szükséges).
```bash
echo 0 | sudo tee /proc/sys/kernel/yama/ptrace_scope
```

### <a name="suse-linux"></a>SUSE Linux
SUSE Linux Enterprise Server VM-lemezképfájlok esetében – SLES 12 SP3 a HPC számára, SLES 12 SP3 a HPC-hoz (prémium), SLES 12 SP1 HPC-hez, SLES 12 SP1 HPC-hez (prémium), SLES 12 SP4 és SLES 15, a RDMA illesztőprogramok telepítve vannak, és az Intel MPI-csomagok terjesztése a virtuális gépen történik. Telepítse az Intel MPI-t a következő parancs futtatásával:
```bash
sudo rpm -v -i --nodeps /opt/intelMPI/intel_mpi_packages/*.rpm
```

## <a name="mpich"></a>MPICH

Telepítse a UCX a fent leírtak szerint. MPICH létrehozása.

```bash
wget https://www.mpich.org/static/downloads/3.3/mpich-3.3.tar.gz
tar -xvf mpich-3.3.tar.gz
cd mpich-3.3
./configure --with-ucx=${UCX_PATH} --prefix=${INSTALL_PREFIX} --with-device=ch4:ucx
make -j 8 && make install
```

MPICH futtatása.

```bash
${INSTALL_PREFIX}/bin/mpiexec -n 2 -hostfile ~/hostfile -env UCX_IB_PKEY=0x0003 -bind-to hwthread ./osu_latency
```

Tekintse meg a fentiekben említett partíciós kulcsot.

## <a name="mvapich2"></a>MVAPICH2

MVAPICH2 létrehozása.

```bash
wget http://mvapich.cse.ohio-state.edu/download/mvapich/mv2/mvapich2-2.3.tar.gz
tar -xv mvapich2-2.3.tar.gz
cd mvapich2-2.3
./configure --prefix=${INSTALL_PREFIX}
make -j 8 && make install
```

MVAPICH2 futtatása.

```bash
${INSTALL_PREFIX}/bin/mpirun_rsh -np 2 -hostfile ~/hostfile MV2_CPU_MAPPING=48 ./osu_latency
```

## <a name="platform-mpi-community-edition"></a>Platform MPI Community Edition

Telepítse a szükséges csomagokat a platform MPI-hez.

```bash
sudo yum install libstdc++.i686
sudo yum install glibc.i686
Download platform MPI at https://www.ibm.com/developerworks/downloads/im/mpi/index.html 
sudo ./platform_mpi-09.01.04.03r-ce.bin
```

Kövesse a telepítési folyamatot.

## <a name="osu-mpi-benchmarks"></a>OSU MPI-referenciaértékek

Töltse le a [OSU MPI-referenciaértékeket](http://mvapich.cse.ohio-state.edu/benchmarks/) és a untar.

```bash
wget http://mvapich.cse.ohio-state.edu/download/mvapich/osu-micro-benchmarks-5.5.tar.gz
tar –xvf osu-micro-benchmarks-5.5.tar.gz
cd osu-micro-benchmarks-5.5
```

Teljesítménytesztek létrehozása egy adott MPI-könyvtár használatával:

```bash
CC=<mpi-install-path/bin/mpicc>CXX=<mpi-install-path/bin/mpicxx> ./configure 
make
```

Az MPI-referenciaértékek a `mpi/` mappában találhatók.


## <a name="discover-partition-keys"></a>Partíciós kulcsok felderítése

Az azonos bérlőn (rendelkezésre állási csoporton vagy virtuálisgép-méretezési csoporton) belüli más virtuális gépekkel való kommunikációhoz használható partíciós kulcsok (p-Keys) felderítése.

```bash
/sys/class/infiniband/mlx5_0/ports/1/pkeys/0
/sys/class/infiniband/mlx5_0/ports/1/pkeys/1
```

A kettő közül a nagyobb a bérlői kulcs, amelyet MPI-vel kell használni. Példa: Ha a következő a p-Keys, a 0x800b-t MPI-vel kell használni.

```bash
cat /sys/class/infiniband/mlx5_0/ports/1/pkeys/0
0x800b
cat /sys/class/infiniband/mlx5_0/ports/1/pkeys/1
0x7fff
```

Használja az alapértelmezett (0x7fff) partíciós kulcstól eltérő partíciót. A UCX a p-Key MSB törlését igényli. Például állítsa be a UCX_IB_PKEY as 0x000b for 0x800b.

Azt is vegye figyelembe, hogy amennyiben a bérlő (rendelkezésre állási csoport vagy virtuálisgép-méretezési csoport) létezik, a PKEYs változatlan marad. Ez akkor is igaz, ha csomópontok lettek hozzáadva/törölve. Az új bérlők különböző PKEYs kapnak.


## <a name="set-up-user-limits-for-mpi"></a>Felhasználói korlátok beállítása MPI-re

Felhasználói korlátok beállítása MPI-re.

```bash
cat << EOF | sudo tee -a /etc/security/limits.conf
*               hard    memlock         unlimited
*               soft    memlock         unlimited
*               hard    nofile          65535
*               soft    nofile          65535
EOF
```

## <a name="set-up-ssh-keys-for-mpi"></a>SSH-kulcsok beállítása MPI-hez

Állítsa be az SSH-kulcsokat az azt igénylő MPI-típusokhoz.

```bash
ssh-keygen -f /home/$USER/.ssh/id_rsa -t rsa -N ''
cat << EOF > /home/$USER/.ssh/config
Host *
    StrictHostKeyChecking no
EOF
cat /home/$USER/.ssh/id_rsa.pub >> /home/$USER/.ssh/authorized_keys
chmod 600 /home/$USER/.ssh/authorized_keys
chmod 644 /home/$USER/.ssh/config
```

A fenti szintaxis azt feltételezi, hogy egy megosztott kezdőkönyvtár, más. ssh könyvtárat kell átmásolni az egyes csomópontokra.

## <a name="next-steps"></a>Következő lépések

- További információ az [InfiniBand-t támogató](../../sizes-hpc.md#rdma-capable-instances) [H-sorozatú](../../sizes-hpc.md) és [N sorozatú](../../sizes-gpu.md) virtuális gépekről
- Tekintse át a [HB-sorozat áttekintését](hb-series-overview.md) és a [HC-sorozat áttekintését](hc-series-overview.md) , amelyből megismerheti a számítási feladatok optimális konfigurálását a teljesítmény és a méretezhetőség érdekében.
- Olvassa el a legújabb bejelentéseket és néhány HPC-példát, valamint az eredményeket az [Azure számítási technikai Közösség blogjában](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute).
- A HPC-munkaterhelések futtatásának magasabb szintű építészeti áttekintését lásd: [nagy teljesítményű számítástechnika (HPC) az Azure](/azure/architecture/topics/high-performance-computing/)-ban.
