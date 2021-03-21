---
title: Azure-beli VM-méretek – HPC | Microsoft Docs
description: Felsorolja a nagy teljesítményű számítástechnikai virtuális gépek számára elérhető különböző méreteket az Azure-ban. A vCPU, adatlemezek és hálózati adapterek számával, valamint az ebben a sorozatban lévő méretek sávszélességével kapcsolatos információkat sorolja fel.
author: vermagit
ms.service: virtual-machines
ms.subservice: vm-sizes-hpc
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 12/09/2020
ms.author: amverma
ms.reviewer: jushiman
ms.openlocfilehash: bfc49cf7c9f5489248aeba7465a88e97ad16abd8
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102554396"
---
# <a name="high-performance-computing-vm-sizes"></a>Nagy teljesítményű számítástechnikai VM-méretek

Az Azure H-sorozatú virtuális gépek (VM-EK) úgy vannak kialakítva, hogy vezetői szintű teljesítményt, méretezhetőséget és költséghatékonyságot biztosítanak a különböző valós HPC-munkaterhelésekhez.

[HBv2 sorozat](hbv2-series.md) A virtuális gépek a memória sávszélessége által vezérelt alkalmazások számára vannak optimalizálva, mint például a Fluid Dynamics, a véges elemek elemzése és a tározó szimulálása. A HBv2 virtuális gépek szolgáltatás 120 AMD EPYC 7742 processzor-mag, 4 GB RAM/CPU mag, és nem egyidejű többszálú. Minden HBv2-alapú virtuális gép akár 340 GB/s memóriát, valamint akár 4 teraFLOPS FP64-számítást biztosít.

A HBv2 virtuális gépek 200 GB/s Mellanox HDR-InfiniBand rendelkeznek, míg a HB és a HC sorozatú virtuális gépek 100 GB/s Mellanox EDR InfiniBand rendelkeznek. Ezen virtuálisgép-típusok mindegyike egy nem blokkoló FAT-fában csatlakozik az optimalizált és konzisztens RDMA teljesítmény érdekében. A HBv2 virtuális gépek támogatják az adaptív útválasztást és a dinamikus csatlakoztatott átvitelt (a DCT a standard RC és UD átvitelek esetében). Ezek a funkciók növelik az alkalmazások teljesítményét, méretezhetőségét és konzisztenciáját, és használata erősen ajánlott.

[HB sorozat](hb-series.md) A virtuális gépek a memória sávszélessége által vezérelt alkalmazások számára vannak optimalizálva, például a Fluid Dynamics, a explicit véges elemek elemzése és az időjárási modellezés. A HB-beli virtuális gépeken 60 AMD EPYC 7551 processzor-mag, 4 GB RAM/CPU mag, és nincs feleznie. Az AMD EPYC platform több mint 260 GB/s memóriát biztosít.

[HC sorozat](hc-series.md) A virtuális gépek a sűrű számítások által vezérelt alkalmazások számára vannak optimalizálva, például implicit, véges elemek elemzése, molekuláris dinamika és számítási kémia. A HC-alapú virtuális gépek a 44 Intel Xeon Platinum 8168 processzor-magot, a CPU-mag 8 GB RAM-ot, a feleznie pedig nem rendelkeznek. Az Intel Xeon Platinum platform támogatja az Intel olyan szoftverek gazdag ökoszisztémáját, mint az Intel Math kernel Library.

[H sorozat](h-series.md) A virtuális gépeket magas CPU-gyakorisággal vagy nagy memóriával rendelkező alkalmazások számára optimalizálták. A H-sorozatú virtuális gépek 8 vagy 16 Intel Xeon E5 2667 v3 processzorral, 7 vagy 14 GB RAM memóriával rendelkeznek, és nem feleznie. A H-sorozat 56 GB/s Mellanox FDR InfiniBand a konzisztens RDMA teljesítményének nem blokkoló FAT-konfigurációjában. A H-sorozatú virtuális gépek támogatják az Intel MPI 5. x és az MS-MPI használatát.

> [!NOTE]
> Minden HBv2, HB és HC sorozatú virtuális gép kizárólagos hozzáféréssel rendelkezik a fizikai kiszolgálókhoz. A fizikai kiszolgálókon csak 1 virtuális gép érhető el, és a virtuálisgép-méretekhez nem áll rendelkezésre megosztott, több virtuális gépre kiterjedő megosztás.

> [!NOTE]
> Az [A8 – A11-es virtuális gépek](./sizes-previous-gen.md#a-series---compute-intensive-instances) a 3/2021-es kivonulásra vannak tervezve. További információ: [HPC áttelepítési útmutató](https://azure.microsoft.com/resources/hpc-migration-guide/).

## <a name="rdma-capable-instances"></a>RDMA-kompatibilis példányok

A HPC VM-méretek többsége (HBv2, HB, HC, H16r, H16mr, A8 és A9) egy hálózati adaptert biztosít a távoli közvetlen memória-hozzáférés (RDMA) kapcsolatához. Az "r" (ND40rs_v2, ND24rs, NC24rs_v3, NC24rs_v2 és NC24r) jelölésű kijelölt [N sorozatú](./nc-series.md) méretek szintén RDMA-kompatibilisek. Ez az interfész a más virtuálisgép-méretekben elérhető szabványos Azure Ethernet hálózati adapteren felül van.

Ez az interfész lehetővé teszi, hogy a RDMA-kompatibilis példányok InfiniBand-(IB-) hálózaton keresztül kommunikáljanak, a HBv2 HDR-díjszabása, a HB, az HC, a NDv2, a FDR díjszabása pedig a H16r, a H16mr és más RDMA-kompatibilis N sorozatú virtuális gépek esetében, valamint az A8-as és A9-es VM-EK QDR-díja. Ezek a RDMA-képességek növelhetik bizonyos Message Passing Interface-(MPI-) alkalmazások méretezhetőségét és teljesítményét.

> [!NOTE]
> Az Azure HPC-ben a virtuális gépek két osztálya van attól függően, hogy az SR-IOV engedélyezve van-e a InfiniBand. Jelenleg szinte minden újabb generációs, RDMA-kompatibilis vagy InfiniBand-kompatibilis virtuális gép az Azure-ban az SR-IOV engedélyezve van, kivéve a H16r, a H16mr, a NC24r, az A8 és az A9-es verzióját.
> A RDMA csak a InfiniBand (IB) hálózaton keresztül engedélyezett, és minden RDMA-kompatibilis virtuális gép esetében támogatott.
> Az IB protokollon keresztüli IP-cím csak az SR-IOV-kompatibilis virtuális gépeken támogatott.
> A RDMA nincs engedélyezve az Ethernet-hálózaton keresztül.

- **Operációs rendszer** – a Linux a HPC-alapú virtuális gépek esetében nagyon jól támogatott; általában a CentOS, a RHEL, az Ubuntu és a SUSE rendszerű disztribúciókat használják. A Windows-támogatással kapcsolatban a Windows Server 2016-es és újabb verziói támogatottak az összes HPC sorozatú virtuális gépen. A Windows Server 2012 R2, a Windows Server 2012 is támogatott a nem SR-IOV-kompatibilis virtuális gépeken (H16r, H16mr, A8 és A9). Vegye figyelembe, hogy [a Windows Server 2012 R2 nem támogatott a HBv2 és más, több mint 64 (virtuális vagy fizikai) magokkal rendelkező virtuális gépeken](/windows-server/virtualization/hyper-v/supported-windows-guest-operating-systems-for-hyper-v-on-windows). Lásd: virtuálisgép- [lemezképek](./workloads/hpc/configure.md) a támogatott virtuálisgép-rendszerképek listáját a piactéren, valamint a megfelelő konfigurálásuk módját.

- **InfiniBand és illesztőprogramok** – a InfiniBand-kompatibilis virtuális gépeken a megfelelő illesztőprogramok szükségesek a RDMA engedélyezéséhez. Linux rendszeren az SR-IOV és a nem SR-IOV engedélyezett virtuális gépek esetében a piactéren a CentOS-HPC virtuálisgép-lemezképek előre konfigurálva vannak a megfelelő illesztőprogramokkal. Az Ubuntu-alapú virtuálisgép-lemezképek a megfelelő illesztőprogramokkal konfigurálhatók az [itt leírt utasítások](https://techcommunity.microsoft.com/t5/azure-compute/configuring-infiniband-for-ubuntu-hpc-and-gpu-vms/ba-p/1221351)alapján. A használatra kész virtuálisgép-Linux operációsrendszer-lemezképekről további információt a [virtuális gépek konfigurálása és optimalizálása Linux operációs rendszerhez](./workloads/hpc/configure.md) című témakörben talál.

   Linux rendszeren a InfiniBandDriverLinux virtuálisgép- [bővítmény](./extensions/hpc-compute-infiniband-linux.md) használható a Mellanox OFED-illesztőprogramok telepítésére és a InfiniBand engedélyezésére az SR-IOV enabled H-és N-sorozatú virtuális gépeken. További információ a InfiniBand engedélyezéséről a RDMA-kompatibilis virtuális gépeken a [HPC-munkaterhelések](./workloads/hpc/enable-infiniband.md)esetében.

   Windows rendszeren a InfiniBandDriverWindows virtuálisgép- [bővítmény](./extensions/hpc-compute-infiniband-windows.md) telepíti a Windows Network Direct-illesztőprogramokat (nem SR-IOV virtuális gépeken) vagy a Mellanox OFED-ILLESZTŐPROGRAMOKAT (SR-IOV virtuális gépeken) a RDMA-kapcsolathoz. Az A8-as és A9-es példányok bizonyos telepítései esetében a HpcVmDrivers-bővítmény automatikusan hozzáadódik. Vegye figyelembe, hogy a HpcVmDrivers VM-bővítmény elavult; nem lesz frissítve.

   A virtuálisgép-bővítmény virtuális géphez való hozzáadásához [Azure PowerShell](/powershell/azure/) parancsmagokat használhat. További információ: [virtuálisgép-bővítmények és-szolgáltatások](./extensions/overview.md). A [klasszikus üzemi modellben](/previous-versions/azure/virtual-machines/windows/classic/agents-and-extensions-classic)üzembe helyezett virtuális gépek bővítményei is használhatók.

- **MPI** – az SR-IOV-kompatibilis virtuálisgép-méretek az Azure-ban szinte bármely, az MPI-hez tartozó MELLANOX-OFED használható. A nem SR-IOV-kompatibilis virtuális gépeken támogatott MPI-implementációk a Microsoft Network Direct (ND) felületet használják a virtuális gépek közötti kommunikációhoz. Ezért csak a Microsoft MPI (MS-MPI) 2012 R2 vagy újabb, illetve az Intel MPI 5. x verziói támogatottak. Az Intel MPI runtime library újabb verziói (2017, 2018) vagy esetleg nem kompatibilisek az Azure RDMA-illesztőprogramokkal. További részletekért lásd: az [MPI](./workloads/hpc/setup-mpi.md) beállítása a HPC-hez az Azure-beli HPC-alapú virtuális gépeken.

- **RDMA hálózati címtartomány** – az Azure RDMA-hálózata fenntartja a 172.16.0.0/16 címtartomány méretét. Ha az MPI-alkalmazásokat egy Azure-beli virtuális hálózaton üzembe helyezett példányokon szeretné futtatni, győződjön meg arról, hogy a virtuális hálózati címtartomány nem fedi át a RDMA-hálózatot.

## <a name="cluster-configuration-options"></a>Fürt konfigurációs beállításai

Az Azure számos lehetőséget kínál a RDMA-hálózattal kommunikáló Windows HPC virtuális gépek fürtjének létrehozására, beleértve a következőket: 

- **Virtual Machines**  – a RDMA-kompatibilis HPC-alapú virtuális gépeket ugyanabban a méretezési csoporton vagy rendelkezésre állási csoporton helyezheti üzembe (ha a Azure Resource Manager üzembe helyezési modellt használja). Ha a klasszikus üzemi modellt használja, telepítse a virtuális gépeket ugyanabban a felhőalapú szolgáltatásban.

- **Virtuálisgép-méretezési** csoportok – egy virtuálisgép-méretezési csoportban győződjön meg arról, hogy egyetlen elhelyezési csoportra korlátozza az üzembe helyezést a InfiniBand belüli kommunikációhoz. Például egy Resource Manager-sablonban állítsa be a tulajdonságot a következőre: `singlePlacementGroup` `true` . Vegye figyelembe, hogy a (z) tulajdonsággal megadható maximális méretezési csoport mérete `singlePlacementGroup` `true` alapértelmezés szerint 100 virtuális gépre van korlátozva. Ha a HPC-feladatok méretezése több, mint 100 virtuális gépen van, egyetlen bérlőn belül, a növekedéshez igénybe vehet egy [online ügyfélszolgálati kérést](../azure-portal/supportability/how-to-create-azure-support-request.md) díjmentesen. Egy méretezési csoportba tartozó virtuális gépek számának korlátozását 300-ra lehet növelni. Vegye figyelembe, hogy a virtuális gépek a rendelkezésre állási csoportokkal való telepítésekor a maximális korlát a rendelkezésre állási csoporton 200 virtuális gépenként érhető el.

- **MPI a virtuális gépek között** – ha RDMA (például MPI-kommunikációt használ) a virtuális gépek (VM-EK) között kell lennie, ügyeljen arra, hogy a virtuális gépek ugyanabban a virtuálisgép-méretezési csoporton vagy rendelkezésre állási csoporton belül legyenek.

- **Azure CycleCloud** – HPC-fürt létrehozása az [Azure CYCLECLOUD](/azure/cyclecloud/) -ben MPI-feladatok futtatásához.

- **Azure batch** – hozzon létre egy [Azure batch](../batch/index.yml) -készletet az MPI-munkaterhelések futtatásához. Ha Azure Batch használatával MPI-alkalmazások futtatásához nagy számítási igényű példányokat szeretne használni, tekintse meg a [többpéldányos feladatok használata a Message Passing Interface (MPI) alkalmazások Azure batch-ben való futtatásához](../batch/batch-mpi.md)című témakört.

- **Microsoft HPC Pack**  -  A [HPC Pack](/powershell/high-performance-computing/overview) tartalmaz egy futásidejű környezetet az MS-MPI számára, amely az Azure RDMA hálózatot használja RDMA-kompatibilis Linux virtuális gépeken való üzembe helyezéskor. Központi telepítések például: [Linux RDMA-fürt beállítása HPC-csomaggal MPI-alkalmazások futtatásához](/powershell/high-performance-computing/hpcpack-linux-openfoam).

## <a name="deployment-considerations"></a>Telepítési szempontok

- **Azure-előfizetés** – több számítási igényű példány üzembe helyezéséhez vegye fontolóra az utólagos elszámolású előfizetést vagy az egyéb vásárlási lehetőségeket. Amennyiben [ingyenes Azure-fiókot](https://azure.microsoft.com/free/) használ, csak korlátozott számú számítási magot használhat az Azure-ban.

- **Díjszabás és rendelkezésre állás** – ezeket a virtuálisgép-méreteket a rendszer csak a standard szintű díjszabás keretében kínálja. Tekintse meg az Azure-régiókban rendelkezésre [álló régiókban elérhető termékeket](https://azure.microsoft.com/global-infrastructure/services/) .

- **Magok kvótája** – előfordulhat, hogy a magok kvótáját az alapértelmezett érték alapján kell megnövelni az Azure-előfizetésben. Előfordulhat, hogy az előfizetés bizonyos virtuálisgép-méretekben üzembe helyezhető magok számát is korlátozhatja, beleértve a H-sorozatot is. A kvóta növeléséhez [Nyisson meg egy online ügyfélszolgálati kérést](../azure-portal/supportability/how-to-create-azure-support-request.md) díjmentesen. (Az alapértelmezett korlátok az előfizetési kategóriától függően változhatnak.)

  > [!NOTE]
  > Ha nagy léptékű kapacitásra van szüksége, forduljon az Azure ügyfélszolgálatához. Az Azure-kvóták hitelkeretek, nem kapacitási garanciák. A kvótától függetlenül csak a használt magokért kell fizetnie.
  
- **Virtual Network (virtuális hálózat** ) – az Azure [virtuális hálózat](https://azure.microsoft.com/documentation/services/virtual-network/) nem szükséges a nagy számítási igényű példányok használatához. Számos központi telepítés esetében azonban szükség van legalább egy felhőalapú Azure-beli virtuális hálózatra, vagy egy helyek közötti kapcsolatra, ha a helyszíni erőforrásokhoz kell hozzáférnie. Ha szükséges, hozzon létre egy új virtuális hálózatot a példányok telepítéséhez. Ha nagy számítási igényű virtuális gépeket ad hozzá egy affinitási csoportban lévő virtuális hálózathoz, az nem támogatott.

- **Átméretezés** – a speciális hardverek miatt csak az azonos méretű (H-vagy N-sorozatú) családba tartozó számítási igényű példányokat lehet átméretezni. Például csak egy h sorozatú virtuális gépet lehet átméretezni egy H-sorozatos méretről egy másikra. Előfordulhat, hogy a InfiniBand-illesztőprogramok támogatásával és a NVMe-lemezekkel kapcsolatos további szempontokat bizonyos virtuális gépek esetében figyelembe kell venni.


## <a name="other-sizes"></a>Egyéb méretek

- [Általános célú](sizes-general.md)
- [Számításoptimalizált](sizes-compute.md)
- [Memóriaoptimalizált](sizes-memory.md)
- [Tároptimalizált](sizes-storage.md)
- [GPU-optimalizált](sizes-gpu.md)
- [Előző generációk](sizes-previous-gen.md)

## <a name="next-steps"></a>Következő lépések

- További információ a [virtuális gépek konfigurálásáról, a](./workloads/hpc/configure.md) [InfiniBand engedélyezéséről](./workloads/hpc/enable-infiniband.md), az [MPI beállításáról](./workloads/hpc/setup-mpi.md) és az Azure-hoz készült HPC-alkalmazások optimalizálásáról a [HPC-munkaterhelések](./workloads/hpc/overview.md)esetében.
- Olvassa el a legújabb bejelentéseket és néhány HPC-példát, valamint az eredményeket az [Azure számítási technikai Közösség blogjában](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute).
- A HPC-munkaterhelések futtatásának magasabb szintű építészeti áttekintését lásd: [nagy teljesítményű számítástechnika (HPC) az Azure](/azure/architecture/topics/high-performance-computing/)-ban.
