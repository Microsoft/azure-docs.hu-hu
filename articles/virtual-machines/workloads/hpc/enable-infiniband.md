---
title: InifinBand engedélyezése HPC virtuális gépeken – Azure Virtual Machines | Microsoft Docs
description: Ismerje meg, hogyan engedélyezheti a InfiniBand az Azure HPC virtuális gépeken.
services: virtual-machines
documentationcenter: ''
author: vermagit
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 08/01/2020
ms.author: amverma
ms.reviewer: cynthn
ms.openlocfilehash: 88f1c120ac4578e077e1c51f59bcaf53b1de2083
ms.sourcegitcommit: 8def3249f2c216d7b9d96b154eb096640221b6b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/03/2020
ms.locfileid: "87538897"
---
# <a name="enable-infiniband"></a>Infiniband engedélyezése

A [RDMA képes](../../sizes-hpc.md#rdma-capable-instances) [H-sorozatú](../../sizes-hpc.md) és [N sorozatú](../../sizes-gpu.md) virtuális gépek kommunikációja az alacsony késésű és a nagy sávszélességű InfiniBand-hálózaton keresztül történik. Az RDMA képesség kritikus fontosságú a elosztott csomópontok és az AI-munkaterhelések méretezhetőségének és teljesítményének növelése érdekében. Az InfiniBand enabled H-sorozatú és N sorozatú virtuális gépek egy nem blokkoló FAT-fában vannak csatlakoztatva, amely alacsony átmérőjű kialakítást biztosít az optimalizált és konzisztens RDMA teljesítményhez.

A InfiniBand a képes virtuálisgép-méretekhez többféleképpen is engedélyezhető.

## <a name="vm-images-with-infiniband-drivers"></a>VM-rendszerképek InfiniBand-illesztőprogramokkal
Tekintse meg a virtuálisgép- [rendszerképeket](configure.md#vm-images) a piactéren támogatott virtuálisgép-rendszerképek listájáért, amely a InfiniBand-illesztőprogramokkal (SR-IOV vagy nem SR-IOV virtuális gépekhez) előre betöltve, vagy a megfelelő illesztőprogramok használatával konfigurálható.
Az SR-IOV-kompatibilis RDMA-kompatibilis [virtuális gépek](../../sizes-hpc.md#rdma-capable-instances)esetében a [CentOS-HPC 7,6-es vagy újabb](https://techcommunity.microsoft.com/t5/Azure-Compute/CentOS-HPC-VM-Image-for-SR-IOV-enabled-Azure-HPC-VMs/ba-p/665557) verziójú virtuálisgép-lemezképek a legkönnyebben megkezdhetik a piactéren.
Az Ubuntu VM-lemezképek a megfelelő illesztőprogramokkal konfigurálhatók az SR-IOV és a nem SR-IOV engedélyezve lévő virtuális gépekhez az [itt leírt utasítások](https://techcommunity.microsoft.com/t5/azure-compute/configuring-infiniband-for-ubuntu-hpc-and-gpu-vms/ba-p/1221351)alapján.

## <a name="infiniband-driver-vm-extensions"></a>InfiniBand-illesztőprogram virtuálisgép-bővítményei
Linux rendszeren a InfiniBandDriverLinux virtuálisgép- [bővítmény](../../extensions/hpc-compute-infiniband-linux.md) használható a Mellanox OFED-illesztőprogramok telepítésére és a InfiniBand engedélyezésére az SR-IOV enabled H-és N-sorozatú virtuális gépeken.

Windows rendszeren a InfiniBandDriverWindows virtuálisgép- [bővítmény](../../extensions/hpc-compute-infiniband-windows.md) telepíti a Windows Network Direct-illesztőprogramokat (nem SR-IOV virtuális gépeken) vagy a Mellanox OFED-ILLESZTŐPROGRAMOKAT (SR-IOV virtuális gépeken) a RDMA-kapcsolathoz. Az A8-as és A9-es példányok bizonyos telepítései esetében a HpcVmDrivers-bővítmény automatikusan hozzáadódik. Vegye figyelembe, hogy a HpcVmDrivers VM-bővítmény elavult; nem lesz frissítve.

A virtuálisgép-bővítmény virtuális géphez való hozzáadásához [Azure PowerShell](/powershell/azure/) parancsmagokat használhat. További információ: [virtuálisgép-bővítmények és-szolgáltatások](../../extensions/overview.md). A [klasszikus üzemi modellben](/previous-versions/azure/virtual-machines/windows/classic/agents-and-extensions-classic)üzembe helyezett virtuális gépek bővítményei is használhatók.

## <a name="manual-installation"></a>Manuális telepítés
A [Mellanox OpenFabrics-illesztőprogramok (OFED)](https://www.mellanox.com/products/InfiniBand-VPI-Software) manuálisan telepíthetők az [SR-IOV enabled](../../sizes-hpc.md#rdma-capable-instances) [H-sorozatú](../../sizes-hpc.md) és [N sorozatú](../../sizes-gpu.md) virtuális gépekre.

### <a name="linux"></a>Linux
A [Linux rendszerhez készült OFED-illesztőprogramok](https://www.mellanox.com/products/infiniband-drivers/linux/mlnx_ofed) az alábbi példával telepíthetők. Bár ez a példa a RHEL/CentOS-re mutat, de a lépések általánosak, és bármilyen kompatibilis Linux operációs rendszerhez használhatók, például Ubuntu (16,04, 18,04 19,04, 20,04) és SLES (12 SP4 és 15). A beérkezett fájlok illesztőprogramjai is működnek, de a Mellanox OFED-illesztőprogramok további funkciókat biztosítanak.

```bash
MLNX_OFED_DOWNLOAD_URL=http://content.mellanox.com/ofed/MLNX_OFED-5.0-2.1.8.0/MLNX_OFED_LINUX-5.0-2.1.8.0-rhel7.7-x86_64.tgz
# Optinally verify checksum
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
Windows rendszeren töltse le és telepítse a [Windows-illesztőprogramok MELLANOX OFED](https://www.mellanox.com/products/adapter-software/ethernet/windows/winof-2).

## <a name="enable-ip-over-infiniband-ib"></a>IP-InfiniBand (IB) engedélyezése
Ha MPI-feladatok futtatását tervezi, általában nincs szükség IPoIB. Az MPI-könyvtár az IB-kommunikációhoz tartozó műveletek felületet fogja használni (kivéve, ha explicit módon használja az MPI-könyvtár TCP/IP-csatornáját). Ha azonban olyan alkalmazással rendelkezik, amely a TCP/IP protokollt használja a kommunikációhoz, és az IB-t szeretné futtatni, a IPoIB-t az IB felületen keresztül használhatja. A következő parancsokkal (RHEL/CentOS) engedélyezheti az IP-címek InfiniBand való használatát.

```bash
sudo sed -i -e 's/# OS.EnableRDMA=y/OS.EnableRDMA=y/g' /etc/waagent.conf
sudo systemctl restart waagent
```

## <a name="next-steps"></a>További lépések

- További információ a különböző [támogatott MPI-könyvtárak](setup-mpi.md) telepítéséről és az optimális konfigurációról a virtuális gépeken.
- Tekintse át a [HB-sorozat áttekintését](hb-series-overview.md) és a [HC-sorozat áttekintését](hc-series-overview.md) , amelyből megismerheti a számítási feladatok optimális konfigurálását a teljesítmény és a méretezhetőség érdekében.
- Olvassa el a legújabb bejelentéseket és néhány HPC-példát, valamint az eredményeket az [Azure számítási technikai Közösség blogjában](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute).
- A HPC-munkaterhelések futtatásának magasabb szintű építészeti áttekintését lásd: [nagy teljesítményű számítástechnika (HPC) az Azure](/azure/architecture/topics/high-performance-computing/)-ban.
