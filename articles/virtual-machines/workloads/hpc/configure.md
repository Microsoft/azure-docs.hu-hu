---
title: A H-sorozat és az N-sorozat Azure-Virtual Machines InfiniBand beállítása és optimalizálása
description: Ismerje meg, hogyan konfigurálhatja és optimalizálja a InfiniBand-t támogató H-sorozatú és N sorozatú virtuális gépeket a HPC számára.
author: vermagit
ms.service: virtual-machines
ms.subservice: workloads
ms.topic: article
ms.date: 10/23/2020
ms.author: amverma
ms.reviewer: cynthn
ms.openlocfilehash: b1686b08608e4f1c49cd918e86e8149f0fe2a21c
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/20/2020
ms.locfileid: "94963368"
---
# <a name="configure-and-optimize-vms"></a>Virtuális gépek konfigurálása és optimalizálása

Ez a cikk ismert technikákat használ a InfiniBand-kompatibilis [H-sorozat](../../sizes-hpc.md) és az [N sorozatú](../../sizes-gpu.md) virtuális gépek HPC-hoz történő konfigurálásához és optimalizálásához.

## <a name="vm-images"></a>VM-lemezképek
A InfiniBand-kompatibilis virtuális gépeken a megfelelő illesztőprogramok szükségesek a RDMA engedélyezéséhez. Linux rendszeren a piactéren elérhető CentOS-HPC virtuálisgép-lemezképek előre konfigurálva vannak a megfelelő illesztőprogramokkal. Az Ubuntu-alapú virtuálisgép-lemezképek a megfelelő illesztőprogramokkal konfigurálhatók az [itt leírt utasítások](https://techcommunity.microsoft.com/t5/azure-compute/configuring-infiniband-for-ubuntu-hpc-and-gpu-vms/ba-p/1221351)alapján. Azt is javasoljuk, hogy hozzon létre egyéni virtuálisgép- [rendszerképeket](../../linux/tutorial-custom-images.md) a megfelelő illesztőprogramokkal és konfigurációval, és használja azokat ismétlődően.

> [!NOTE]
> A GPU-t használó [N sorozatú](../../sizes-gpu.md) virtuális gépeken a megfelelő GPU-illesztőprogramokat is meg kell adni, amelyek hozzáadhatók a virtuálisgép- [bővítményekben](../../extensions/hpccompute-gpu-linux.md) vagy [manuálisan](../../linux/n-series-driver-setup.md). A piactéren néhány virtuálisgép-rendszerkép is előre telepítve van az NVIDIA GPU-illesztőprogramokkal.

### <a name="centos-hpc-vm-images"></a>CentOS-HPC VM-rendszerképek

#### <a name="non-sr-iov-enabled-vms"></a>Nem SR-IOV-kompatibilis virtuális gépek
A RDMA-kompatibilis [virtuális gépek](../../sizes-hpc.md#rdma-capable-instances), a CentOS-HPC 6,5-es vagy újabb verziójának használata esetén a piactéren legfeljebb 7,5-ig használhatók a piactéren. A [H16 sorozatú virtuális gépek](../../h-series.md)esetében például a 7,1 és a 7,5 verziók használata javasolt. Ezek a virtuálisgép-rendszerképek előre be vannak töltve a RDMA és az Intel MPI 5,1-es verziójának közvetlen hálózati illesztőprogramjaival.

> [!NOTE]
> Ezen CentOS-alapú HPC-rendszerképeken a nem SR-IOV-kompatibilis virtuális gépeken a kernel frissítései le vannak tiltva a **yum** konfigurációs fájlban. Ennek az az oka, hogy a NetworkDirect Linux RDMA-illesztőprogramok RPM-csomagként vannak elosztva, és előfordulhat, hogy az illesztőprogram frissítései nem működnek, ha a kernel frissül.

#### <a name="sr-iov-enabled-vms"></a>SR-IOV-kompatibilis virtuális gépek
  Az SR-IOV-kompatibilis RDMA-kompatibilis [virtuális gépek](../../sizes-hpc.md#rdma-capable-instances), a [CentOS-HPC 7,6-es vagy újabb](https://techcommunity.microsoft.com/t5/Azure-Compute/CentOS-HPC-VM-Image-for-SR-IOV-enabled-Azure-HPC-VMs/ba-p/665557) verziójú virtuálisgép-lemezképek alkalmasak a piactéren. Ezek a virtuálisgép-rendszerképek optimalizáltak és előre betöltve vannak a OFED-illesztőprogramokkal a RDMA és a különböző gyakran használt MPI-kódtárak és tudományos számítástechnikai csomagok számára, és a legegyszerűbb módszer a kezdéshez.

  Példa a CentOS-HPC 7,6-es és újabb verziójú virtuálisgép-lemezképek létrehozásához használt parancsfájlokra a [azhpc-lemezképek](https://github.com/Azure/azhpc-images/tree/master/centos)tárházában.
  
  > [!NOTE] 
  > A legújabb Azure HPC Marketplace-lemezképek Mellanox OFED 5,1-es vagy újabb verziójúak, amelyek nem támogatják ConnectX3-Pro InfiniBand kártyákat. Az SR-IOV által engedélyezett N sorozatú virtuálisgép-méretek a FDR InfiniBand (pl. NCv3) a következő CentOS-HPC VM-rendszerképeket vagy régebbi verziókat használhatják:
  >- OpenLogic: CentOS-HPC: 7.6:7.6.2020062900
  >- OpenLogic: CentOS-HPC: 7_6gen2:7.6.2020062901
  >- OpenLogic: CentOS-HPC: 7.7:7.7.2020062600
  >- OpenLogic: CentOS-HPC: 7_7-Gen2:7.7.2020062601
  >- OpenLogic: CentOS-HPC: 8_1:8.1.2020062400
  >- OpenLogic: CentOS-HPC: 8_1-Gen2:8.1.2020062401


### <a name="rhelcentos-vm-images"></a>RHEL/CentOS VM-lemezképek
A piactéren a RHEL vagy CentOS-alapú, nem HPC-alapú virtuálisgép-lemezképek konfigurálhatók az SR-IOV-kompatibilis RDMA-kompatibilis [virtuális gépeken](../../sizes-hpc.md#rdma-capable-instances)való használatra. További információ a [InfiniBand engedélyezéséről](enable-infiniband.md) és az MPI-nek a virtuális gépeken való [beállításáról](setup-mpi.md) .

  Példa a CentOS-HPC 7,6-es és újabb verziójú virtuálisgép-lemezképek létrehozásához használt parancsfájlokra a [azhpc-lemezképek](https://github.com/Azure/azhpc-images/tree/master/centos)tárházában.
  
  > [!NOTE]
  > A Mellanox OFED 5,1-es és újabb verziók nem ConnectX3-Pro támogatják az SR-IOV engedélyezett N sorozatú virtuálisgép-méreteket a FDR InfiniBand (például NCv3). Az N sorozatú virtuális gépek ConnectX3-Pro-kártyán az LTS Mellanox OFED 4.9-0.1.7.0 vagy régebbi verzióját használja. További részletek [itt](https://www.mellanox.com/products/infiniband-drivers/linux/mlnx_ofed)találhatók.

### <a name="ubuntu-vm-images"></a>Ubuntu VM-lemezképek
Az Ubuntu Server 16,04 LTS, 18,04 LTS és 20,04 LTS virtuálisgép-lemezképek a piactéren az SR-IOV és a nem SR-IOV [RDMA-kompatibilis virtuális gépek](../../sizes-hpc.md#rdma-capable-instances)esetében is támogatottak. További információ a [InfiniBand engedélyezéséről](enable-infiniband.md) és az MPI-nek a virtuális gépeken való [beállításáról](setup-mpi.md) .

  Az Ubuntu 18,04 LTS-alapú HPC VM-rendszerképek létrehozásához használható szkriptek például a [azhpc-lemezképek](https://github.com/Azure/azhpc-images/tree/master/ubuntu/ubuntu-18.x/ubuntu-18.04-hpc)tárházán érhetők el.

### <a name="suse-linux-enterprise-server-vm-images"></a>VM-rendszerképek SUSE Linux Enterprise Server
SLES 12 SP3 for HPC, SLES 12 SP3 for HPC (prémium), SLES 12 SP1 for HPC, SLES 12 SP1 for HPC (prémium), SLES 12 SP4 és SLES 15 VM-lemezképek a piactéren támogatottak. Ezek a virtuálisgép-rendszerképek előre be vannak töltve a RDMA és az Intel MPI 5,1-es verziójának közvetlen hálózati illesztőprogramjaival. További információ az [MPI beállításáról](setup-mpi.md) a virtuális gépeken.

## <a name="optimize-vms"></a>Virtuális gépek optimalizálása

A virtuális gépen a jobb teljesítmény érdekében a következő választható optimalizálási beállítások érhetők el.

### <a name="update-lis"></a>LIS frissítése

Ha a funkcionalitáshoz vagy a teljesítményhez szükséges, a [Linux Integration Services (lis) illesztőprogramjai](../../linux/endorsed-distros.md) telepíthetők vagy frissíthetők a támogatott operációsrendszer-disztribúciókban, különösen az egyéni lemezkép vagy egy régebbi operációsrendszer-verzió (például a CentOS/RHEL 6. x vagy a 7. x korábbi verziója) használatával telepíthetők.

```bash
wget https://aka.ms/lis
tar xzf lis
pushd LISISO
./upgrade.sh
```

### <a name="reclaim-memory"></a>Memória visszaigénylése

A teljesítmény javítása a távoli memória-hozzáférés elkerülése érdekében automatikusan visszaállítja a memóriát.

```bash
echo 1 >/proc/sys/vm/zone_reclaim_mode
```

A virtuális gépek újraindítása után tegye meg a következőket:

```bash
echo "vm.zone_reclaim_mode = 1" >> /etc/sysctl.conf sysctl -p
```

### <a name="disable-firewall-and-selinux"></a>Tűzfal-és SELinux letiltása

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

### <a name="configure-walinuxagent"></a>WALinuxAgent konfigurálása

```bash
sed -i -e 's/# OS.EnableRDMA=y/OS.EnableRDMA=y/g' /etc/waagent.conf
```
Ha szükséges, a WALinuxAgent letilthatja a feladat előtti lépésként, és engedélyezheti a feladat-visszavételt a virtuális gépek maximális rendelkezésre állása érdekében a HPC munkaterheléshez.


## <a name="next-steps"></a>Következő lépések

- További információ a [InfiniBand engedélyezéséről](enable-infiniband.md) a InfiniBand-kompatibilis [H-](../../sizes-hpc.md) és [N-](../../sizes-gpu.md) sorozatú virtuális gépeken.
- További információ a különböző [támogatott MPI-könyvtárak](setup-mpi.md) telepítéséről és az optimális konfigurációról a virtuális gépeken.
- Tekintse át a [HB-sorozat áttekintését](hb-series-overview.md) és a [HC-sorozat áttekintését](hc-series-overview.md) , amelyből megismerheti a számítási feladatok optimális konfigurálását a teljesítmény és a méretezhetőség érdekében.
- Olvassa el a legújabb bejelentéseket és néhány HPC-példát, valamint az eredményeket az [Azure számítási technikai Közösség blogjában](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute).
- A HPC-munkaterhelések futtatásának magasabb szintű építészeti áttekintését lásd: [nagy teljesítményű számítástechnika (HPC) az Azure](/azure/architecture/topics/high-performance-computing/)-ban.
