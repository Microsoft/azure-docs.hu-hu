---
title: InfiniBand-kompatibilis H- és N-sorozatú Azure-Virtual Machines
description: Megtudhatja, hogyan konfigurálja és optimalizálja az InfiniBand-kompatibilis H- és N-sorozatú virtuális gépeket a HPC-hez.
author: vermagit
ms.service: virtual-machines
ms.subservice: hpc
ms.topic: article
ms.date: 03/18/2021
ms.author: amverma
ms.reviewer: cynthn
ms.openlocfilehash: 470d5efae68366b5cc96243bab4ebb8552771650
ms.sourcegitcommit: 950e98d5b3e9984b884673e59e0d2c9aaeabb5bb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2021
ms.locfileid: "107600879"
---
# <a name="configure-and-optimize-vms"></a>Virtuális gépek konfigurálása és optimalizálása

Ez a cikk az InfiniBand-kompatibilis [H-](../../sizes-hpc.md) és [N-sorozatú](../../sizes-gpu.md) virtuális gépek HPC-hez való konfigurálásához és optimalizálásához nyújt útmutatást.

## <a name="vm-images"></a>VM-lemezképek
Az InfiniBand-kompatibilis virtuális gépeken az RDMA engedélyezéséhez a megfelelő illesztőprogramok szükségesek.
- A [Marketplace-en elérhető CentOS-HPC](#centos-hpc-vm-images) virtuálisgép-rendszerképek előre konfigurálva vannak a megfelelő IB-illesztőprogramokkal, és ezek a legegyszerűbben elindíthatóak.
- Az [Ubuntu VM-rendszerképek](#ubuntu-vm-images) a megfelelő IB-illesztőprogramokkal konfigurálhatóak. Javasoljuk, hogy hozzon létre [egyéni virtuálisgép-rendszerképeket](../../linux/tutorial-custom-images.md) a megfelelő illesztőprogramokkal és konfigurációval, és használja újra ezeket az ismétlődően.

[GPU-kompatibilis N-sorozatú](../../sizes-gpu.md) virtuális gépeken a megfelelő GPU-illesztőprogramok is szükségesek, amelyeket a virtuálisgép-bővítményeken keresztül vagy [manuálisan](../../extensions/hpccompute-gpu-linux.md) lehet [hozzáadni.](../../linux/n-series-driver-setup.md) A Marketplace-en elérhető egyes virtuálisgép-rendszerképek is előre telepítve vannak az Nvidia GPU-illesztőprogramokkal, köztük az Nvidia egyes virtuálisgép-rendszerképekkel is.

### <a name="centos-hpc-vm-images"></a>CentOS-HPC virtuálisgép-rendszerképek

#### <a name="sr-iov-enabled-vms"></a>SR-IOV-kompatibilis virtuális gépek
Az SR-IOV-kompatibilis [RDMA-kompatibilis](../../sizes-hpc.md#rdma-capable-instances)virtuális gépekhez a Marketplace 7.6-os vagy újabb verziójában elérhető [CentOS-HPC virtuálisgép-rendszerképek](https://azuremarketplace.microsoft.com/marketplace/apps/openlogic.centos-hpc?tab=Overview) megfelelőek. Ezek a virtuálisgép-rendszerképek az RDMA OFED-illesztőprogramjaival, valamint a különböző gyakran használt MPI-kódtárakkal és tudományos számítási csomagokkal vannak előre feltöltve, és ezek a legegyszerűbben elindíthatóak.
- A CentOS-HPC 7.6-os és újabb verziójú virtuálisgép-lemezképek által tartalmazott információkról a [TechCo új](https://techcommunity.microsoft.com/t5/Azure-Compute/CentOS-HPC-VM-Image-for-SR-IOV-enabled-Azure-HPC-VMs/ba-p/665557)cikkben olvashat.
- A CentOS-HPC 7.6-os vagy újabb verziójú virtuálisgép-rendszerképének alap CentOS Marketplace-rendszerképből való létrehozásához használt szkriptek az [azhpc-images kódtáraban találhatóak.](https://github.com/Azure/azhpc-images/tree/master/centos)
  
> [!NOTE] 
> A legújabb Azure HPC Marketplace-rendszerképek a Mellanox OFED 5.1-es vagy annál magasabb verziójával vannak, amelyek nem támogatják ConnectX3-Pro InfiniBand-kártyákat. Az SR-IOV-kompatibilis, FDR InfiniBandet (például NCv3 vagy régebbi) NDR-t engedélyező virtuálisgép-méretek a következő CentOS-HPC virtuálisgép-rendszerképet vagy a Marketplace régebbi verzióit tudják használni:
>- OpenLogic:CentOS-HPC:7.6:7.6.2020062900
>- OpenLogic:CentOS-HPC:7_6gen2:7.6.2020062901
>- OpenLogic:CentOS-HPC:7.7:7.7.2020062600
>- OpenLogic:CentOS-HPC:7_7-gen2:7.7.2020062601
>- OpenLogic:CentOS-HPC:8_1:8.1.2020062400
>- OpenLogic:CentOS-HPC:8_1-gen2:8.1.2020062401

#### <a name="non-sr-iov-enabled-vms"></a>Nem SR-IOV-kompatibilis virtuális gépek
Nem SR-IOV-kompatibilis [RDMA-kompatibilis](../../sizes-hpc.md#rdma-capable-instances)virtuális gépek esetén a CentOS-HPC 6.5-ös vagy újabb verziója esetén a Marketplace-en 7.4-es verzióig megfelelőek. [H16 sorozatú](../../h-series.md)virtuális gépek esetén például a 7.1-es és 7.4-es verziók használata ajánlott. Ezek a virtuálisgép-rendszerképek előre be vannak töltve az RDMA és az Intel MPI 5.1-es verziójának Közvetlen hálózati illesztőprogramjaival.

> [!NOTE]
> Ezeken a CentOS-alapú HPC-rendszerképeken a nem SR-IOV-kompatibilis virtuális gépek esetében a kernelfrissítések le vannak tiltva a **yum konfigurációs** fájlban. Ennek az az oka, hogy a NetworkDirect Linux RDMA-illesztőprogramok RPM-csomagként vannak elosztva, és előfordulhat, hogy az illesztőprogram-frissítések nem működnek a kernel frissítésekor.

### <a name="rhelcentos-vm-images"></a>RHEL/CentOS virtuálisgép-rendszerképek
A Marketplace RHEL- vagy CentOS-alapú nem HPC virtuálisgép-rendszerképei konfigurálhatóak az SR-IOV-kompatibilis RDMA-kompatibilis virtuális gépeken való [használatra.](../../sizes-hpc.md#rdma-capable-instances) További információ az [InfiniBand engedélyezéséről](enable-infiniband.md) és az [MPI](setup-mpi.md) virtuális gépeken való beállításról.
- A CentOS-HPC 7.6-os vagy újabb verziójú virtuálisgép-rendszerképének az [azhpc-images repoból](https://github.com/Azure/azhpc-images/tree/master/centos) származó alap CentOS Marketplace-rendszerképből való létrehozásához használt szkriptek is használhatók.
  
> [!NOTE]
> A Mellanox OFED 5.1-es vagy annál magasabb nem támogatja az ConnectX3-Pro InfiniBand-kártyákat az SR-IOV-kompatibilis, FDR InfiniBandet (például NCv3) támogató N-sorozatú virtuális gépeken. Használja a LTS Mellanox OFED 4.9-0.1.7.0-s vagy újabb verzióját az N sorozatú virtuális gépeken ConnectX3-Pro kártyákkal. További részleteket itt [talál.](https://www.mellanox.com/products/infiniband-drivers/linux/mlnx_ofed)

### <a name="ubuntu-vm-images"></a>Ubuntu VM-rendszerképek
Az Ubuntu Server 16.04 LTS, 18.04 LTS és 20.04 LTS virtuálisgép-rendszerképek a Marketplace-en mind az SR-IOV, mind a nem SR-IOV [RDMA-kompatibilis](../../sizes-hpc.md#rdma-capable-instances)virtuális gépek esetén támogatottak. További információ az [InfiniBand engedélyezéséről](enable-infiniband.md) és az [MPI](setup-mpi.md) virtuális gépeken való beállításról.
- Az InfiniBand Ubuntu vm-rendszerképeken való engedélyezésére vonatkozó utasítások a TechCo új cikkben [olvashatók.](https://techcommunity.microsoft.com/t5/azure-compute/configuring-infiniband-for-ubuntu-hpc-and-gpu-vms/ba-p/1221351)
- Az Ubuntu 18.04-es és 20.04 LTS-alapú HPC VM-rendszerképek alap Ubuntu Marketplace-rendszerképből való létrehozásához használt szkriptek az [azhpc-images repo-on találhatóak.](https://github.com/Azure/azhpc-images/tree/master/ubuntu)

### <a name="suse-linux-enterprise-server-vm-images"></a>SUSE Linux Enterprise Server virtuálisgép-rendszerképek
Az SLES 12 SP3 for HPC, az SLES 12 SP3 for HPC (Prémium), az SLES 12 SP1 for HPC, az SLES 12 SP1 for HPC (Prémium), az SLES 12 SP4 és az SLES 15 VM rendszerképek támogatottak a Marketplace-en. Ezek a virtuálisgép-rendszerképek előre be vannak töltve az RDMA és az Intel MPI 5.1-es verziójának Közvetlen hálózati illesztőprogramjaival. További információ az [MPI virtuális gépeken](setup-mpi.md) való beállításával kapcsolatban.

## <a name="optimize-vms"></a>Virtuális gépek optimalizálása

Az alábbiakban néhány optimalizálási beállítás található, amelyek javítják a virtuális gép teljesítményét.

### <a name="update-lis"></a>LIS frissítése

Ha a működéshez vagy a teljesítményhez szükséges, a [Linux Integration Services- (LIS-)](../../linux/endorsed-distros.md) illesztőprogramok telepíthetők vagy frissíthetők a támogatott operációsrendszer-disztribúciókon, különösen az egyéni rendszerkép vagy egy régebbi operációsrendszer-verzió, például a CentOS/RHEL 6.x vagy a 7.x korábbi verziójának használatával.

```bash
wget https://aka.ms/lis
tar xzf lis
pushd LISISO
./upgrade.sh
```

### <a name="reclaim-memory"></a>Memória felszabadítás

Javíthatja a teljesítményt, ha automatikusan visszaveszi a memóriát a távoli memória-hozzáférés elkerülése érdekében.

```bash
echo 1 >/proc/sys/vm/zone_reclaim_mode
```

Annak érdekében, hogy ez a virtuális gép újraindítása után is megmaradjon:

```bash
echo "vm.zone_reclaim_mode = 1" >> /etc/sysctl.conf sysctl -p
```

### <a name="disable-firewall-and-selinux"></a>Tűzfal és SELinux letiltása

```bash
systemctl stop iptables.service
systemctl disable iptables.service
systemctl mask firewalld
systemctl stop firewalld.service
systemctl disable firewalld.service
iptables -nL
sed -i -e's/SELINUX=enforcing/SELINUX=disabled/g' /etc/selinux/config
```

### <a name="disable-cpupower"></a>Cpupower letiltása

```bash
service cpupower status
if enabled, disable it:
service cpupower stop
sudo systemctl disable cpupower
```

### <a name="configure-walinuxagent"></a>A WALinuxAgent konfigurálása

```bash
sed -i -e 's/# OS.EnableRDMA=y/OS.EnableRDMA=y/g' /etc/waagent.conf
```
Ha szükséges, a WALinuxAgent le is tiltható feladat előtti lépésként, és engedélyezhető a feladat utáni feladat utáni feladat a HPC számítási feladat virtuálisgép-erőforrás számára való maximális rendelkezésre állása érdekében.


## <a name="next-steps"></a>Következő lépések

- További információ az [InfiniBand infiniBand-kompatibilis](enable-infiniband.md) [H-](../../sizes-hpc.md) és [N-sorozatú](../../sizes-gpu.md) virtuális gépeken való engedélyezéséről.
- További információ a különböző támogatott [MPI-kódtárak](setup-mpi.md) virtuális gépeken való telepítéséről és futtatásáról.
- Tekintse át a [HBv3 sorozat áttekintését és](hbv3-series-overview.md) [a HC sorozat áttekintését.](hc-series-overview.md)
- Olvassa el a legújabb közleményeket, HPC számítási feladatok példáit és a teljesítményeredményeket a [Azure Compute Tech Community Blogon.](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute)
- A HPC számítási feladatok futtatásának magasabb szintű architekturális nézetét lásd: Nagy teljesítményű [számítástechnika (HPC) az Azure-ban.](/azure/architecture/topics/high-performance-computing/)
