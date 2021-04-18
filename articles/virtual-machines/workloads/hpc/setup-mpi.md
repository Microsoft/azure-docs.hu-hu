---
title: A HPC Message Passing Interface (MPI) beállítása – Azure Virtual Machines | Microsoft Docs
description: Megtudhatja, hogyan állíthatja be az MPI-t a HPC-hez az Azure-ban.
author: vermagit
ms.service: virtual-machines
ms.subservice: hpc
ms.topic: article
ms.date: 04/16/2021
ms.author: amverma
ms.reviewer: cynthn
ms.openlocfilehash: f43fc94174ebdcfdf447d3635a696193959849fa
ms.sourcegitcommit: 950e98d5b3e9984b884673e59e0d2c9aaeabb5bb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2021
ms.locfileid: "107600292"
---
# <a name="set-up-message-passing-interface-for-hpc"></a>A HPC Message Passing Interface beállítása

A [Message Passing Interface (MPI)](https://en.wikipedia.org/wiki/Message_Passing_Interface) egy nyílt kódtár és de facto szabvány az elosztott memória párhuzamosításához. Gyakran használják számos HPC számítási feladatban. Az [RDMA-kompatibilis](../../sizes-hpc.md#rdma-capable-instances) H- és [N-sorozatú](../../sizes-hpc.md) virtuális gépek HPC számítási feladatai [MPI-t](../../sizes-gpu.md) használhatnak a kis késésű és nagy sávszélességű InfiniBand-hálózaton keresztüli kommunikációhoz.
- Az Azure SR-IOV-kompatibilis virtuálisgép-méretei szinte bármilyen TÍPUSÚ MPI használatát lehetővé teszik a Mellanox OFED-vel.
- A nem SR-IOV-kompatibilis virtuális gépeken a támogatott MPI-implementációk a Microsoft Network Direct (ND) interfészt használják a virtuális gépek közötti kommunikációhoz. Ezért csak a Microsoft MPI (MS-MPI) 2012 R2 vagy újabb és az Intel MPI 5.x verzió támogatott. Az Intel MPI-futásidejű kódtár újabb verziói (2017, 2018) kompatibilisek lehetnek az Azure RDMA-illesztőprogramokkal.

Az SR-IOV-kompatibilis [RDMA-kompatibilis](../../sizes-hpc.md#rdma-capable-instances)virtuális gépekhez a [CentOS-HPC virtuálisgép-rendszerképek](configure.md#centos-hpc-vm-images) 7.6-os vagy újabb verziója megfelelő. Ezek a virtuálisgép-lemezképek az RDMA OFED-illesztőprogramjaival, valamint a különböző gyakran használt MPI-kódtárakkal és tudományos számítási csomagokkal vannak előre feltöltve, és ezek a legegyszerűbben elindíthatóak.

Bár az alábbi példák RHEL/CentOS rendszerre mutatnak, a lépések általánosak, és bármilyen kompatibilis Linux operációs rendszerhez használhatók, például az Ubuntuhoz (16.04, 18.04 19.04, 20.04) és az SLES-hez (12 SP4 és 15). További példák más MPI-implementációk más disztribúciókban való beállítására: [az azhpc-images repo](https://github.com/Azure/azhpc-images/blob/master/ubuntu/ubuntu-18.x/ubuntu-18.04-hpc/install_mpis.sh).

> [!NOTE]
> Az MPI-feladatok SR-IOV-kompatibilis, bizonyos MPI-kódtárakkal (például Platform MPI) rendelkező virtuális gépeken való futtatásához szükség lehet partíciókulcsok (p-kulcsok) beállítására a bérlőn az elkülönítés és a biztonság érdekében. Kövesse a Partíciókulcsok felderítése szakaszban található lépéseket a p-kulcsértékek meghatározásával és az MPI-könyvtárral végzett MPI-feladathoz való helyes beállításával kapcsolatos részletekért. [](#discover-partition-keys)

> [!NOTE]
> Az alábbi kódrészletek példák. Javasoljuk, hogy a csomagok legújabb stabil verzióját használja, vagy hivatkozik az [azhpc-images repo-re.](https://github.com/Azure/azhpc-images/blob/master/ubuntu/ubuntu-18.x/ubuntu-18.04-hpc/install_mpis.sh)

## <a name="choosing-mpi-library"></a>Az MPI-kódtár kiválasztása
Ha egy HPC-alkalmazás egy adott MPI-kódtárat javasol, először próbálja ki ezt a verziót. Ha rugalmasan dönti el, hogy melyik MPI-t választhatja, és a legjobb teljesítményt szeretné, próbálja ki a HPC-X-et. Összességében a HPC-X MPI az InfiniBand interfész UCX-keretrendszerének használatával a lehető legjobban teljesít, és kihasználja a Mellanox InfiniBand összes hardver- és szoftverképességét. Emellett a HPC-X és az OpenMPI ABI-kompatibilis, így az OpenMPI-vel készült HPC-X-alkalmazásokkal dinamikusan futtathat HPC-alkalmazásokat. Hasonlóképpen, az Intel MPI, az MVAPICH és az MPICH is ABI-kompatibilis.

Az alábbi ábra a népszerű MPI-kódtárak architektúráját mutatja be.

![Népszerű MPI-kódtárak architektúrája](./media/mpi-architecture.png)

## <a name="ucx"></a>UCX

[A Unified Communication X (UCX)](https://github.com/openucx/ucx) a HPC kommunikációs API-k keretrendszere. InfiniBanden keresztüli MPI-kommunikációra van optimalizálva, és számos MPI-implementációval működik, például az OpenMPI-hez és az MPICH-hez.

```bash
wget https://github.com/openucx/ucx/releases/download/v1.4.0/ucx-1.4.0.tar.gz
tar -xvf ucx-1.4.0.tar.gz
cd ucx-1.4.0
./configure --prefix=<ucx-install-path>
make -j 8 && make install
```

> [!NOTE]
> A UCX közelmúltbeli buildjére ki lett javítva [egy](https://github.com/openucx/ucx/pull/5965) probléma, amely miatt a megfelelő InfiniBand-felület több hálózati adapter jelenléte esetén van kiválasztva. Itt talál [további részleteket](hb-hc-known-issues.md#accelerated-networking-on-hb-hc-hbv2-and-ndv2) az MPI InfiniBand-en való futtatásáról, ha a gyorsított hálózatépítés engedélyezve van a virtuális gépen.

## <a name="hpc-x"></a>HPC-X

A [HPC-X szoftveres eszközkészlet](https://www.mellanox.com/products/hpc-x-toolkit) UCX-et és HCOLL-t tartalmaz, és a UCX-re építhető.

```bash
HPCX_VERSION="v2.6.0"
HPCX_DOWNLOAD_URL=https://azhpcstor.blob.core.windows.net/azhpc-images-store/hpcx-v2.6.0-gcc-MLNX_OFED_LINUX-5.0-1.0.0.0-redhat7.7-x86_64.tbz
get --retry-connrefused --tries=3 --waitretry=5 $HPCX_DOWNLOAD_URL
tar -xvf hpcx-${HPCX_VERSION}-gcc-MLNX_OFED_LINUX-5.0-1.0.0.0-redhat7.7-x86_64.tbz
mv hpcx-${HPCX_VERSION}-gcc-MLNX_OFED_LINUX-5.0-1.0.0.0-redhat7.7-x86_64 ${INSTALL_PREFIX}
HPCX_PATH=${INSTALL_PREFIX}/hpcx-${HPCX_VERSION}-gcc-MLNX_OFED_LINUX-5.0-1.0.0.0-redhat7.7-x86_64
```

Az alábbi parancs bemutatja a HPC-X és az OpenMPI néhány ajánlott mpirun argumentumát.
```bash
mpirun -n $NPROCS --hostfile $HOSTFILE --map-by ppr:$NUMBER_PROCESSES_PER_NUMA:numa:pe=$NUMBER_THREADS_PER_PROCESS -report-bindings $MPI_EXECUTABLE
```
ahol:

|Paraméter|Leírás                                        |
|---------|---------------------------------------------------|
|`NPROCS`   |Az MPI-folyamatok számát adja meg. Példa: `-n 16`.|
|`$HOSTFILE`|Az állomásnevet vagy IP-címet tartalmazó fájlt ad meg, amely jelzi az MPI-folyamatok futási helyét. Példa: `--hostfile hosts`.|
|`$NUMBER_PROCESSES_PER_NUMA`   |Megadja az egyes NUMA-tartományokban futó MPI-folyamatok számát. Ha például NUMA-nként négy MPI-folyamatot kell megadnia, használja a következőt: `--map-by ppr:4:numa:pe=1` .|
|`$NUMBER_THREADS_PER_PROCESS`  |A szálak MPI-folyamatonkénti számát adja meg. Ha például egy MPI-folyamatot és numa-nként négy szálat kell megadnia, használja a következőt: `--map-by ppr:1:numa:pe=4` .|
|`-report-bindings` |A magokra leképező MPI-folyamatokat nyomtatja ki, ami hasznos annak ellenőrzéséhez, hogy helyes-e az MPI-folyamat rögzítése.|
|`$MPI_EXECUTABLE`  |Az MPI-kódtárakban található, végrehajtható MPI-hivatkozásokat adja meg. Az MPI-fordító burkolói ezt automatikusan ják meg. Például: `mpicc` vagy `mpif90` .|

Az OSU késési mikrobenchmark futtatásának példája a következő:
```bash
${HPCX_PATH}mpirun -np 2 --map-by ppr:2:node -x UCX_TLS=rc ${HPCX_PATH}/ompi/tests/osu-micro-benchmarks-5.3.2/osu_latency
```

### <a name="optimizing-mpi-collectives"></a>MPI-együttesek optimalizálása

Az MPI kollektív kommunikációs primitívek rugalmas, hordozható lehetőséget kínálnak a csoportos kommunikációs műveletek megvalósítására. Széles körben használják őket különböző tudományos párhuzamos alkalmazásokban, és jelentős hatással vannak az alkalmazás teljes teljesítményére. A kollektív kommunikáció teljesítményének a HPC-X és a HCOLL-kódtár együttes kommunikációhoz való használatával való optimalizálásához szükséges konfigurációs paraméterekről a [TechCo használatában](https://techcommunity.microsoft.com/t5/azure-compute/optimizing-mpi-collective-communication-using-hpc-x-on-azurehpc/ba-p/1356740) talál részletes információt.

Ha például azt gyanítja, hogy a szorosan összetűnő MPI-alkalmazás túl sok közös kommunikációt tesz lehetővé, megpróbálhatja engedélyezni a hierarchikus gyűjtőket (HCOLL). A funkciók engedélyezéséhez használja a következő paramétereket.
```bash
-mca coll_hcoll_enable 1 -x HCOLL_MAIN_IB=<MLX device>:<Port>
```

> [!NOTE] 
> A HPC-X 2.7.4 vagy újabb verziói esetében szükség lehet az LD_LIBRARY_PATH-nak való explicit továbbhelyezresre, ha az MOFED UCX-verziója eltér a HPC-X verziójától.

## <a name="openmpi"></a>OpenMPI

Telepítse a UCX-et a fent leírtak szerint. A HCOLL a [HPC-X szoftverkészlet](https://www.mellanox.com/products/hpc-x-toolkit) része, és nem igényel különleges telepítést.

Az OpenMPI az adattárban elérhető csomagokból telepíthető.

```bash
sudo yum install –y openmpi
```

Javasoljuk, hogy az OpenMPI legújabb, stabil kiadását építse ki UCX-sel.

```bash
OMPI_VERSION="4.0.3"
OMPI_DOWNLOAD_URL=https://download.open-mpi.org/release/open-mpi/v4.0/openmpi-${OMPI_VERSION}.tar.gz
wget --retry-connrefused --tries=3 --waitretry=5 $OMPI_DOWNLOAD_URL
tar -xvf openmpi-${OMPI_VERSION}.tar.gz
cd openmpi-${OMPI_VERSION}
./configure --prefix=${INSTALL_PREFIX}/openmpi-${OMPI_VERSION} --with-ucx=${UCX_PATH} --with-hcoll=${HCOLL_PATH} --enable-mpirun-prefix-by-default --with-platform=contrib/platform/mellanox/optimized && make -j$(nproc) && make install
```

Az optimális teljesítmény érdekében futtassa az OpenMPI-t a és `ucx` a `hcoll` alkalmazással. Tekintse meg a [HPC-X példáját is.](#hpc-x)

```bash
${INSTALL_PREFIX}/bin/mpirun -np 2 --map-by node --hostfile ~/hostfile -mca pml ucx --mca btl ^vader,tcp,openib -x UCX_NET_DEVICES=mlx5_0:1  -x UCX_IB_PKEY=0x0003  ./osu_latency
```

Ellenőrizze a partíciókulcsot a fent említettek szerint.

## <a name="intel-mpi"></a>Intel MPI

Töltse le a kiválasztott [Intel MPI-verziót.](https://software.intel.com/mpi-library/choose-download) Az Intel MPI 2019-es kiadása az Open Fabrics Alliance (OFA) keretrendszerről az Open Fabrics Interfaces (OFI) keretrendszerre váltott, és jelenleg támogatja a libfabric keretrendszert. Az InfiniBand-támogatásnak két szolgáltatója van: az mlx és a verbs.
Módosítsa a I_MPI_FABRICS környezeti változót a verziótól függően.
- Intel MPI 2019 és 2021: használja a `I_MPI_FABRICS=shm:ofi` következőt: , `I_MPI_OFI_PROVIDER=mlx` . A `mlx` szolgáltató az UCX-et használja. A műveletek használata instabil és kevésbé hatékony. További részletekért [tekintse meg a TechCo új](https://techcommunity.microsoft.com/t5/azure-compute/intelmpi-2019-on-azure-hpc-clusters/ba-p/1403149) cikkét.
- Intel MPI 2018: használat `I_MPI_FABRICS=shm:ofa`
- Intel MPI 2016: használat `I_MPI_DAPL_PROVIDER=ofa-v2-ib0`

Íme néhány javasolt mpirun argumentum az Intel MPI 2019 5-ös vagy további frissítéséhez.
```bash
export FI_PROVIDER=mlx
export I_MPI_DEBUG=5
export I_MPI_PIN_DOMAIN=numa

mpirun -n $NPROCS -f $HOSTFILE $MPI_EXECUTABLE
```
ahol:

|Paraméter|Leírás                                        |
|---------|---------------------------------------------------|
|`FI_PROVIDER`  |Meghatározza, hogy melyik libfabric-szolgáltatót használja, amely hatással lesz a használt API-re, protokollra és hálózatra. A verbs egy másik lehetőség, de általában az mlx jobb teljesítményt nyújt.|
|`I_MPI_DEBUG`|Megadja az extra hibakeresési kimenet szintjét, amely részletesen meg tudja adni a folyamatok rögzítési helyének, valamint a használt protokollnak és hálózatnak a részleteit.|
|`I_MPI_PIN_DOMAIN` |A folyamatok rögzítési folyamatainak a beállítását határozza meg. Rögzíthet például magokat, szoftvercsatornákat vagy NUMA-tartományokat. Ebben a példában ezt a környezeti változót numa-ra állítva, ami azt jelenti, hogy a folyamatok NUMA-csomóponttartományokra lesznek rögzítve.|

### <a name="optimizing-mpi-collectives"></a>MPI-együttesek optimalizálása

Próbálkozhat még néhány lehetőséggel, különösen akkor, ha a kollektív műveletek jelentős mennyiségű időt fogyasztanak. Az Intel MPI 2019 5. frissítésének 5. vagy utolsó frissítése támogatja az mlx-et, és az UCX keretrendszert használja az InfiniBand-fel való kommunikációhoz. A HCOLL-t is támogatja.
```bash
export FI_PROVIDER=mlx
export I_MPI_COLL_EXTERNAL=1
```

### <a name="non-sr-iov-vms"></a>Nem SR-IOV virtuális gépek

A nem SR-IOV virtuális gépek esetén az 5.x [](https://registrationcenter.intel.com/en/forms/?productid=1740) futásidejű ingyenes próbaverzió letöltésére példa a következő:
```bash
wget http://registrationcenter-download.intel.com/akdlm/irc_nas/tec/9278/l_mpi_p_5.1.3.223.tgz
```
A telepítés lépéseit lásd: [Intel MPI Library Installation Guide (Az Intel MPI-kódtár telepítési útmutatója).](https://registrationcenter-download.intel.com/akdlm/irc_nas/1718/INSTALL.html?lang=en&fileExt=.html)
Igény szerint engedélyezheti a ptrace-t a nem gyökérszintű, nem hibakereső folyamatokhoz (ez szükséges az Intel MPI legújabb verzióihoz).
```bash
echo 0 | sudo tee /proc/sys/kernel/yama/ptrace_scope
```

### <a name="suse-linux"></a>SUSE Linux
A SUSE Linux Enterprise Server VM-rendszerképverziók esetében – HPC-hez SLES 12 SP3, HPC-hez SLES 12 SP3 (Prémium), HPC-hez SLES 12 SP1, HPC-hez SLES 12 SP4 és SLES 15 esetén az RDMA-illesztőprogramok telepítve vannak, az Intel MPI-csomagok pedig a virtuális gépen vannak elosztva. Telepítse az Intel MPI-t a következő parancs futtatásával:
```bash
sudo rpm -v -i --nodeps /opt/intelMPI/intel_mpi_packages/*.rpm
```

## <a name="mvapich"></a>MVAPICH

Az alábbiakban egy példát mutatunk be az MVAPICH2 építésre. Vegye figyelembe, hogy az alább használt verzióknál újabb verziók is elérhetők lehetnek.
```bash
wget http://mvapich.cse.ohio-state.edu/download/mvapich/mv2/mvapich2-2.3.tar.gz
tar -xv mvapich2-2.3.tar.gz
cd mvapich2-2.3
./configure --prefix=${INSTALL_PREFIX}
make -j 8 && make install
```

Az OSU késési mikrobenchmark futtatásának példája a következő:
```bash
${INSTALL_PREFIX}/bin/mpirun_rsh -np 2 -hostfile ~/hostfile MV2_CPU_MAPPING=48 ./osu_latency
```

Az alábbi lista számos ajánlott `mpirun` argumentumot tartalmaz.
```bash
export MV2_CPU_BINDING_POLICY=scatter
export MV2_CPU_BINDING_LEVEL=numanode
export MV2_SHOW_CPU_BINDING=1
export MV2_SHOW_HCA_BINDING=1

mpirun -n $NPROCS -f $HOSTFILE $MPI_EXECUTABLE
```
ahol:

|Paraméter|Leírás                                        |
|---------|---------------------------------------------------|
|`MV2_CPU_BINDING_POLICY`   |Meghatározza, hogy melyik kötési szabályzatot kell használni, amely hatással lesz a folyamatok alapvető edd-hez való rögzítésére. Ebben az esetben pontokat ad meg, így a folyamatok egyenletesen szóródnak el a NUMA-tartományok között.|
|`MV2_CPU_BINDING_LEVEL`|A folyamatok rögzítési helyének megadása. Ebben az esetben a numanode-t adja meg, ami azt jelenti, hogy a folyamatok NUMA-tartományok egységeihez vannak kitűzve.|
|`MV2_SHOW_CPU_BINDING` |Megadja, hogy szeretne-e hibakeresési információkat kapni a folyamatok rögzítési helyének adatokat.|
|`MV2_SHOW_HCA_BINDING` |Megadja, hogy szeretne-e hibakeresési információkat kapni arról, hogy az egyes folyamatok melyik gazdacsatorna-adaptert használják.|

## <a name="platform-mpi"></a>Platform MPI

Telepítse a Platform MPI Community Editionhez szükséges csomagokat.

```bash
sudo yum install libstdc++.i686
sudo yum install glibc.i686
Download platform MPI at https://www.ibm.com/developerworks/downloads/im/mpi/index.html 
sudo ./platform_mpi-09.01.04.03r-ce.bin
```

Kövesse a telepítési folyamatot.

Az alábbi parancsok az MPI pingpong és allreduce futtatására mutatnak be platform MPI-t HBv3 virtuális gépeken CentOS-HPC 7.6, 7.8 és 8.1 virtuálisgép-rendszerképekkel.

```bash
/opt/ibm/platform_mpi/bin/mpirun -hostlist 10.0.0.8:1,10.0.0.9:1 -np 2 -e MPI_IB_PKEY=0x800a  -ibv  /home/jijos/mpi-benchmarks/IMB-MPI1 pingpong
/opt/ibm/platform_mpi/bin/mpirun -hostlist 10.0.0.8:120,10.0.0.9:120 -np 240 -e MPI_IB_PKEY=0x800a  -ibv  /home/jijos/mpi-benchmarks/IMB-MPI1 allreduce -npmin 240
```


## <a name="mpich"></a>MPICH

Telepítse a UCX-et a fent leírtak szerint. Build MPICH.

```bash
wget https://www.mpich.org/static/downloads/3.3/mpich-3.3.tar.gz
tar -xvf mpich-3.3.tar.gz
cd mpich-3.3
./configure --with-ucx=${UCX_PATH} --prefix=${INSTALL_PREFIX} --with-device=ch4:ucx
make -j 8 && make install
```

Az MPICH futtatása.

```bash
${INSTALL_PREFIX}/bin/mpiexec -n 2 -hostfile ~/hostfile -env UCX_IB_PKEY=0x0003 -bind-to hwthread ./osu_latency
```

Ellenőrizze a partíciókulcsot a fent említettek szerint.

## <a name="osu-mpi-benchmarks"></a>OSU MPI-teljesítménytesztek

Töltse le [az OSU MPI-teljesítményteszteket](http://mvapich.cse.ohio-state.edu/benchmarks/) és az untar-t.

```bash
wget http://mvapich.cse.ohio-state.edu/download/mvapich/osu-micro-benchmarks-5.5.tar.gz
tar –xvf osu-micro-benchmarks-5.5.tar.gz
cd osu-micro-benchmarks-5.5
```

Build benchmarks egy adott MPI-kódtár használatával:

```bash
CC=<mpi-install-path/bin/mpicc>CXX=<mpi-install-path/bin/mpicxx> ./configure 
make
```

Az MPI-teljesítménytesztek a mappában `mpi/` találhatók.


## <a name="discover-partition-keys"></a>Partíciókulcsok felderítése

Derítsen fel partíciókulcsokat (p-kulcsokat) az ugyanazon bérlőn belüli más virtuális gépekkel való kommunikációhoz (rendelkezésre állási készlet vagy virtuálisgép-méretezési készlet).

```bash
/sys/class/infiniband/mlx5_0/ports/1/pkeys/0
/sys/class/infiniband/mlx5_0/ports/1/pkeys/1
```

A kettő közül a nagyobb az MPI-hez használt bérlőkulcs. Példa: Ha a p-kulcsok a következők, 0x800b az MPI-t kell használni.

```bash
cat /sys/class/infiniband/mlx5_0/ports/1/pkeys/0
0x800b
cat /sys/class/infiniband/mlx5_0/ports/1/pkeys/1
0x7fff
```

Használja az alapértelmezett (0x7fff) partíciókulcstól más partíciót. Az UCX megköveteli a p-kulcs MSB-nek a törlődöttét. Állítsa be például a UCX_IB_PKEY 0x000b a 0x800b.

Azt is vegye figyelembe, hogy amíg a bérlő (rendelkezésre állási csoport vagy virtuálisgép-méretezési csoport) létezik, a PKEY-k változatlanok maradnak. Ez akkor is igaz, ha csomópontokat adnak hozzá/törölnek. Az új bérlők különböző PKEY-ket kapnak.


## <a name="set-up-user-limits-for-mpi"></a>Felhasználói korlátok beállítása az MPI-hez

Felhasználói korlátok beállítása az MPI-hez.

```bash
cat << EOF | sudo tee -a /etc/security/limits.conf
*               hard    memlock         unlimited
*               soft    memlock         unlimited
*               hard    nofile          65535
*               soft    nofile          65535
EOF
```

## <a name="set-up-ssh-keys-for-mpi"></a>SSH-kulcsok beállítása az MPI-hez

Állítson be SSH-kulcsokat az azt igénylő MPI-típusokhoz.

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

A fenti szintaxis egy megosztott kezdőkönyvtárat feltételez, különben az .ssh könyvtárat minden csomópontra át kell másolni.

## <a name="next-steps"></a>Következő lépések

- Tudnivalók az [InfiniBand-kompatibilis](../../sizes-hpc.md#rdma-capable-instances) H- és [N-sorozatú](../../sizes-hpc.md) virtuális gépekről [](../../sizes-gpu.md)
- Tekintse át a [HBv3-sorozat áttekintését és](hbv3-series-overview.md) [a HC-sorozat áttekintését.](hc-series-overview.md)
- Olvassa el a legújabb közleményeket, a HPC számítási feladatok példáit és a teljesítményeredményeket a [Azure Compute Tech Community Blogon.](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute)
- A HPC számítási feladatok futtatásának magasabb szintű architekturális nézetét lásd: Nagy teljesítményű [számítástechnika (HPC) az Azure-ban.](/azure/architecture/topics/high-performance-computing/)
