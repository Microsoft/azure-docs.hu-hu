---
title: Az InifinBand engedélyezése HPC virtuális gépeken – Azure Virtual Machines | Microsoft Docs
description: Megtudhatja, hogyan engedélyezheti az InfiniBandet az Azure HPC virtuális gépeken.
author: vermagit
ms.service: virtual-machines
ms.subservice: hpc
ms.topic: article
ms.date: 03/18/2021
ms.author: amverma
ms.reviewer: cynthn
ms.openlocfilehash: dba336c8690bba2bb388a8b9ab2d52b651166da5
ms.sourcegitcommit: 950e98d5b3e9984b884673e59e0d2c9aaeabb5bb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2021
ms.locfileid: "107599604"
---
# <a name="enable-infiniband"></a>Infiniband engedélyezése

[Az RDMA-kompatibilis](../../sizes-hpc.md#rdma-capable-instances) [H-](../../sizes-hpc.md) és [N-sorozatú](../../sizes-gpu.md) virtuális gépek kis késéssel és nagy sávszélességű InfiniBand-hálózattal kommunikálnak. Az ilyen összekapcsoláson keresztüli RDMA-képesség kritikus fontosságú az elosztott csomópontos HPC- és AI-számítási feladatok skálázhatóságának és teljesítményének növelése érdekében. Az InfiniBand-kompatibilis H- és N-sorozatú virtuális gépek egy nem blokkoló, fat fában vannak csatlakoztatva, alacsony szintű kialakítással az optimalizált és konzisztens RDMA-teljesítmény érdekében.

Az InfiniBand többféleképpen engedélyezhető a megfelelő virtuálisgép-méretekhez.

## <a name="vm-images-with-infiniband-drivers"></a>InfiniBand-illesztőprogramokkal együtt használható virtuálisgép-rendszerképek
A [](configure.md#vm-images) Marketplace-en elérhető támogatott virtuálisgép-rendszerképek listájáért tekintse meg a virtuálisgép-rendszerképeket, amelyek előre be vannak töltve InfiniBand-illesztőprogramokkal (SR-IOV vagy nem SR-IOV virtuális gépek esetén), vagy konfigurálhatóak az [RDMA-kompatibilis](../../sizes-hpc.md#rdma-capable-instances)virtuális gépek megfelelő illesztőprogramjaival.
- Az első lépésekhez a [Marketplace-en elérhető CentOS-HPC](configure.md#centos-hpc-vm-images) virtuálisgép-rendszerképek a legegyszerűbbek.
- Az [Ubuntu VM-rendszerképek](configure.md#ubuntu-vm-images) a megfelelő IB-illesztőprogramokkal konfigurálhatóak.

## <a name="infiniband-driver-vm-extensions"></a>InfiniBand-illesztőprogram virtuálisgép-bővítményei
Linux rendszeren az [InfiniBandDriverLinux](../../extensions/hpc-compute-infiniband-linux.md) virtuálisgép-bővítmény használatával telepíthetők a Mellanox OFED-illesztőprogramok, és engedélyezhető az InfiniBand az SR-IOV-kompatibilis H és N sorozatú virtuális gépeken.

Windows rendszeren az [InfiniBandDriverWindows vm](../../extensions/hpc-compute-infiniband-windows.md) bővítmény Windows Network Direct-illesztőprogramokat (nem SR-IOV virtuális gépeken) vagy Mellanox OFED illesztőprogramokat (SR-IOV virtuális gépeken) telepít az RDMA-kapcsolatokhoz. Az A8- és A9-példányok bizonyos üzemelő példányai esetében a HpcVmDrivers bővítmény hozzáadása automatikusan megtörténik. Vegye figyelembe, hogy a HpcVmDrivers virtuálisgép-bővítmény elavult; nem fog frissülni.

A virtuálisgép-bővítmény virtuális géphez való hozzáadásához használhatja a [Azure PowerShell](/powershell/azure/) parancsmagokat. További információ: [Virtuálisgép-bővítmények és -funkciók.](../../extensions/overview.md) A klasszikus üzembe helyezési modellben üzembe helyezett virtuális gépek bővítményei [is használhatók.](/previous-versions/azure/virtual-machines/windows/classic/agents-and-extensions-classic)

## <a name="manual-installation"></a>Manuális telepítés
[A Mellanox OpenFabrics-illesztőprogramok (OFED)](https://www.mellanox.com/products/InfiniBand-VPI-Software) manuálisan telepíthetők az [SR-IOV-kompatibilis](../../sizes-hpc.md#rdma-capable-instances) [H-](../../sizes-hpc.md) és [N-sorozatú](../../sizes-gpu.md) virtuális gépekre.

### <a name="linux"></a>Linux
A [Linuxhoz használható OFED-illesztőprogramok](https://www.mellanox.com/products/infiniband-drivers/linux/mlnx_ofed) az alábbi példával telepíthetők. Bár a példa itt az RHEL/CentOS-re mutat, a lépések általánosak, és bármely kompatibilis Linux operációs rendszerhez használhatók, például az Ubuntuhoz (16.04, 18.04 19.04, 20.04) és az SLES-hez (12 SP4 és 15). További példák más disztribúciókra: [az azhpc-images repo](https://github.com/Azure/azhpc-images/blob/master/ubuntu/ubuntu-18.x/ubuntu-18.04-hpc/install_mellanoxofed.sh). A beérkezett üzenetek illesztőprogramjai is működnek, de a Mellanox OFED illesztőprogramok több funkciót biztosítanak.

```bash
MLNX_OFED_DOWNLOAD_URL=http://content.mellanox.com/ofed/MLNX_OFED-5.0-2.1.8.0/MLNX_OFED_LINUX-5.0-2.1.8.0-rhel7.7-x86_64.tgz
# Optionally verify checksum
wget --retry-connrefused --tries=3 --waitretry=5 $MLNX_OFED_DOWNLOAD_URL
tar zxvf MLNX_OFED_LINUX-5.0-2.1.8.0-rhel7.7-x86_64.tgz

KERNEL=( $(rpm -q kernel | sed 's/kernel\-//g') )
KERNEL=${KERNEL[-1]}
# Uncomment the lines below if you are running this on a VM
#RELEASE=( $(cat /etc/centos-release | awk '{print $4}') )
#yum -y install http://olcentgbl.trafficmanager.net/centos/${RELEASE}/updates/x86_64/kernel-devel-${KERNEL}.rpm
yum install -y kernel-devel-${KERNEL}
./MLNX_OFED_LINUX-5.0-2.1.8.0-rhel7.7-x86_64/mlnxofedinstall --kernel $KERNEL --kernel-sources /usr/src/kernels/${KERNEL} --add-kernel-support --skip-repo
```

### <a name="windows"></a>Windows
Windows rendszeren töltse le és telepítse a [Mellanox OFED for Windows-illesztőprogramokat.](https://www.mellanox.com/products/adapter-software/ethernet/windows/winof-2)

## <a name="enable-ip-over-infiniband-ib"></a>Ip-cím engedélyezése InfiniBand (IB) használatával
Ha MPI-feladatokat tervez futtatni, általában nincs szüksége IPoIB-fájlra. Az MPI-kódtár a verbs interfészt fogja használni az IB-kommunikációhoz (kivéve, ha ön kifejezetten az MPI-kódtár TCP/IP-csatornáját használja). Ha azonban olyan alkalmazással rendelkezik, amely TCP/IP protokollt használ a kommunikációhoz, és IB-n keresztül szeretne futni, használhatja az IPoIB-t az IB-felületen keresztül. Az alábbi parancsokkal (RHEL/CentOS) engedélyezheti az InfiniBanden keresztüli IP-címeket.

```bash
sudo sed -i -e 's/# OS.EnableRDMA=n/OS.EnableRDMA=y/g' /etc/waagent.conf
sudo systemctl restart waagent
```

## <a name="next-steps"></a>Következő lépések

- További információ a különböző támogatott [MPI-kódtárak](setup-mpi.md) virtuális gépeken való telepítéséről és futtatásáról.
- Tekintse át a [HBv3 sorozat áttekintését és](hbv3-series-overview.md) [a HC sorozat áttekintését.](hc-series-overview.md)
- Olvassa el a legújabb közleményeket, HPC számítási feladatok példáit és a teljesítményeredményeket a [Azure Compute Tech Community Blogon.](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute)
- A HPC számítási feladatok futtatásának magasabb szintű architekturális nézetét lásd: Nagy teljesítményű [számítástechnika (HPC) az Azure-ban.](/azure/architecture/topics/high-performance-computing/)
