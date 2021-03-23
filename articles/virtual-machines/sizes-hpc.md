---
title: Azure-beli VM-méretek – HPC | Microsoft Docs
description: Felsorolja a nagy teljesítményű számítástechnikai virtuális gépek számára elérhető különböző méreteket az Azure-ban. A vCPU, adatlemezek és hálózati adapterek számával, valamint az ebben a sorozatban lévő méretek sávszélességével kapcsolatos információkat sorolja fel.
author: vermagit
ms.service: virtual-machines
ms.subservice: vm-sizes-hpc
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 03/19/2021
ms.author: amverma
ms.reviewer: jushiman
ms.openlocfilehash: a41dce28427db40dfd19879e4ada95add64009c3
ms.sourcegitcommit: 2c1b93301174fccea00798df08e08872f53f669c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/22/2021
ms.locfileid: "104772433"
---
# <a name="high-performance-computing-vm-sizes"></a>Nagy teljesítményű számítástechnikai VM-méretek

Az Azure H-sorozatú virtuális gépek (VM-EK) úgy vannak kialakítva, hogy vezetői szintű teljesítményt, méretezhetőséget és költséghatékonyságot nyújtsanak a különböző valós HPC-számítási feladatokhoz.

[HBv3 sorozat](hbv3-series.md) A virtuális gépek olyan HPC-alkalmazásokra vannak optimalizálva, mint például a Fluid Dynamics, a explicit és az implicit véges elemek elemzése, az időjárási modellezés, a szeizmikus feldolgozás, a tározó szimulálása és az RTL szimuláció A HBv3 virtuális gépek akár 120 AMD EPYC™ 7003 sorozatú (Milan) CPU-magot, 448 GB RAM-ot és feleznie nem. A HBv3 sorozatú virtuális gépek a memória sávszélességének 350 GB/s-os 3,675 sebességét is biztosítják, amely akár 32 MB/s-os gyorsítótár/mag is lehet. 

Az összes HBv3 sorozatú virtuális gép 200 GB/s-os HDR-InfiniBand tartalmaz az NVIDIA Hálózatkezelésből a nagyszámítógépi szintű MPI-munkaterhelések lehetővé tételéhez. Ezek a virtuális gépek az optimalizált és konzisztens RDMA teljesítmény érdekében nem blokkoló FAT-fában vannak csatlakoztatva. A HDR InfiniBand-háló az adaptív útválasztást és a dinamikus csatlakoztatott átvitelt is támogatja (a standard RC és UD DCT kívül). Ezek a szolgáltatások javítják az alkalmazások teljesítményét, méretezhetőségét és konzisztenciáját, és a használatuk erősen ajánlott.

[HBv2 sorozat](hbv2-series.md) A virtuális gépek a memória sávszélessége által vezérelt alkalmazások számára vannak optimalizálva, mint például a Fluid Dynamics, a véges elemek elemzése és a tározó szimulálása. A HBv2 virtuális gépek szolgáltatás 120 AMD EPYC 7742 processzor-mag, 4 GB RAM/CPU mag, és nem egyidejű többszálú. Minden HBv2-alapú virtuális gép akár 340 GB/s memóriát, valamint akár 4 teraFLOPS FP64-számítást biztosít.

A HBv2 virtuális gépek 200 GB/s Mellanox HDR-InfiniBand rendelkeznek, míg a HB és a HC sorozatú virtuális gépek 100 GB/s Mellanox EDR InfiniBand rendelkeznek. Ezen virtuálisgép-típusok mindegyike egy nem blokkoló FAT-fában csatlakozik az optimalizált és konzisztens RDMA teljesítmény érdekében. A HBv2 virtuális gépek támogatják az adaptív útválasztást és a dinamikus csatlakozású átvitelt (DCT a standard RC és UD átviteleken kívül). Ezek a szolgáltatások javítják az alkalmazások teljesítményét, méretezhetőségét és konzisztenciáját, és a használatuk erősen ajánlott.

[HB sorozat](hb-series.md) A virtuális gépek a memória sávszélessége által vezérelt alkalmazások számára vannak optimalizálva, például a Fluid Dynamics, a explicit véges elemek elemzése és az időjárási modellezés. A HB-beli virtuális gépeken 60 AMD EPYC 7551 processzor-mag, 4 GB RAM/CPU mag, és nincs feleznie. Az AMD EPYC platform több mint 260 GB/s memóriát biztosít.

[HC sorozat](hc-series.md) A virtuális gépek a sűrű számítások által vezérelt alkalmazások számára vannak optimalizálva, például implicit, véges elemek elemzése, molekuláris dinamika és számítási kémia. A HC-alapú virtuális gépek a 44 Intel Xeon Platinum 8168 processzor-magot, a CPU-mag 8 GB RAM-ot, a feleznie pedig nem rendelkeznek. Az Intel Xeon Platinum platform támogatja az Intel olyan szoftverek gazdag ökoszisztémáját, mint az Intel Math kernel Library.

[H sorozat](h-series.md) A virtuális gépeket magas CPU-gyakorisággal vagy nagy memóriával rendelkező alkalmazások számára optimalizálták. A H-sorozatú virtuális gépek 8 vagy 16 Intel Xeon E5 2667 v3 processzorral, 7 vagy 14 GB RAM memóriával rendelkeznek, és nem feleznie. A H-sorozat 56 GB/s Mellanox FDR InfiniBand a konzisztens RDMA teljesítményének nem blokkoló FAT-konfigurációjában. A H-sorozatú virtuális gépek támogatják az Intel MPI 5. x és az MS-MPI használatát.

> [!NOTE]
> A HBv3, a HBv2, a HB és a HC sorozatú virtuális gépek kizárólagos hozzáféréssel rendelkeznek a fizikai kiszolgálókhoz. A fizikai kiszolgálókon csak 1 virtuális gép érhető el, és a virtuálisgép-méretekhez nem áll rendelkezésre megosztott, több virtuális gépre kiterjedő megosztás.

> [!NOTE]
> Az [A8 – A11-es virtuális gépek](./sizes-previous-gen.md#a-series---compute-intensive-instances) kivonása a 3/2021-as számú. Mostantól nem lehetséges a méretek új virtuális gépekre történő telepítése. Ha már rendelkezik meglévő virtuális gépekkel, tekintse meg a következő lépésekhez tartozó e-mail-értesítéseket, beleértve a más virtuálisgép-méretek áttelepítését a [HPC áttelepítési útmutató](https://azure.microsoft.com/resources/hpc-migration-guide/)

## <a name="rdma-capable-instances"></a>RDMA-kompatibilis példányok

A HPC-alapú virtuálisgép-méretek többsége egy hálózati adaptert tartalmaz a távoli közvetlen memória-hozzáférés (RDMA) kapcsolathoz. Az "r" jelöléssel jelölt [N-sorozatú](./nc-series.md) méretek szintén RDMA-kompatibilisek. Ez az interfész a más virtuálisgép-méretekben elérhető szabványos Azure Ethernet hálózati adapteren felül van.

Ez a másodlagos felület lehetővé teszi, hogy a RDMA-kompatibilis példányok InfiniBand (IB) hálózaton keresztül kommunikáljanak a HBv3, a HBv2, a HB, a HC, a NDv2 és a FDR sebességével, valamint a H16r, H16mr és más RDMA-kompatibilis N sorozatú virtuális gépek esetében. Ezek a RDMA-képességek növelhetik a Message Passing Interface-(MPI-) alapú alkalmazások méretezhetőségét és teljesítményét.

> [!NOTE]
> **SR-IOV-támogatás**: az Azure HPC-ben a virtuális gépek két osztálya van attól függően, hogy az SR-IOV engedélyezve van-e a InfiniBand számára. Jelenleg szinte minden újabb generációs, RDMA-kompatibilis vagy InfiniBand-kompatibilis virtuális gép az Azure-ban az SR-IOV engedélyezve van, kivéve a H16r, a H16mr és a NC24r.
> A RDMA csak a InfiniBand (IB) hálózaton keresztül engedélyezett, és minden RDMA-kompatibilis virtuális gép esetében támogatott.
> Az IB protokollon keresztüli IP-cím csak az SR-IOV-kompatibilis virtuális gépeken támogatott.
> A RDMA nincs engedélyezve az Ethernet-hálózaton keresztül.

- **Operációs rendszer** – a Linux-disztribúciók, például a CentOS, a RHEL, az Ubuntu és a SUSE általában használatban vannak. A Windows Server 2016-es és újabb verziói a HPC Series virtuális gépeken is támogatottak. A Windows Server 2012 R2 és a Windows Server 2012 is támogatott a nem SR-IOV-kompatibilis virtuális gépeken. Vegye figyelembe, hogy [a Windows Server 2012 R2 nem támogatott a HBv2-től a több mint 64 (virtuális vagy fizikai) magokkal rendelkező virtuálisgép-méreteknél](/windows-server/virtualization/hyper-v/supported-windows-guest-operating-systems-for-hyper-v-on-windows). Lásd: virtuálisgép- [lemezképek](./workloads/hpc/configure.md) a támogatott virtuálisgép-rendszerképek listáját a piactéren, valamint a megfelelő konfigurálásuk módját. A virtuális gépek méretének megfelelő lapok a szoftveres verem támogatását is felsorolják.

- **InfiniBand és illesztőprogramok** – a InfiniBand-kompatibilis virtuális gépeken a megfelelő illesztőprogramok szükségesek a RDMA engedélyezéséhez. Lásd: virtuálisgép- [lemezképek](./workloads/hpc/configure.md) a támogatott virtuálisgép-rendszerképek listáját a piactéren, valamint a megfelelő konfigurálásuk módját. Lásd még: a [InfiniBand engedélyezése](./workloads/hpc/enable-infiniband.md) a virtuálisgép-bővítmények és a InfiniBand-illesztőprogramok manuális telepítésének megismeréséhez.

- **MPI** – az SR-IOV-kompatibilis virtuálisgép-méretek az Azure-ban szinte bármely, az MPI-hez tartozó MELLANOX-OFED használható. A nem SR-IOV-kompatibilis virtuális gépeken támogatott MPI-implementációk a Microsoft Network Direct (ND) felületet használják a virtuális gépek közötti kommunikációhoz. Ezért csak az Intel MPI 5. x és a Microsoft MPI (MS-MPI) 2012 R2 vagy újabb verziói támogatottak. Előfordulhat, hogy az Intel MPI runtime library újabb verziói nem kompatibilisek az Azure RDMA-illesztőprogramokkal. További részletekért lásd: az [MPI](./workloads/hpc/setup-mpi.md) beállítása a HPC-hez az Azure-beli HPC-alapú virtuális gépeken.

  > [!NOTE]
  > **RDMA hálózati címtartomány**: az Azure-beli RDMA-hálózat fenntartja a 172.16.0.0/16 címtartomány méretét. Ha az MPI-alkalmazásokat egy Azure-beli virtuális hálózaton üzembe helyezett példányokon szeretné futtatni, győződjön meg arról, hogy a virtuális hálózati címtartomány nem fedi át a RDMA-hálózatot.

## <a name="cluster-configuration-options"></a>Fürt konfigurációs beállításai

Az Azure számos lehetőséget kínál a RDMA-hálózattal kommunikáló HPC-VM-fürtök létrehozására, beleértve a következőket: 

- **Virtual Machines**  – a RDMA-kompatibilis HPC-alapú virtuális gépeket ugyanabban a méretezési csoporton vagy rendelkezésre állási csoporton helyezheti üzembe (ha a Azure Resource Manager üzembe helyezési modellt használja). Ha a klasszikus üzemi modellt használja, telepítse a virtuális gépeket ugyanabban a felhőalapú szolgáltatásban.

- **Virtuálisgép-méretezési** csoportok – egy virtuálisgép-méretezési csoportban győződjön meg arról, hogy egyetlen elhelyezési csoportra korlátozza az üzembe helyezést a InfiniBand belüli kommunikációhoz. Például egy Resource Manager-sablonban állítsa be a tulajdonságot a következőre: `singlePlacementGroup` `true` . Vegye figyelembe, hogy a méretezési csoport maximális mérete, amely a megadható, `singlePlacementGroup=true` alapértelmezés szerint 100 virtuális gépekre van korlátozva. Ha a HPC-feladatok méretezése több, mint 100 virtuális gépen van, egyetlen bérlőn belül, a növekedéshez igénybe vehet egy [online ügyfélszolgálati kérést](../azure-portal/supportability/how-to-create-azure-support-request.md) díjmentesen. Egy méretezési csoportba tartozó virtuális gépek számának korlátozását 300-ra lehet növelni. Vegye figyelembe, hogy a virtuális gépek a rendelkezésre állási csoportokkal való telepítésekor a maximális korlát a rendelkezésre állási csoporton 200 virtuális gépenként érhető el.

  > [!NOTE]
  > **MPI a virtuális gépek között**: Ha a RDMA (például MPI-kommunikáció használata) szükséges a virtuális gépek (VM-EK) között, győződjön meg arról, hogy a virtuális gépek ugyanabban a virtuálisgép-méretezési csoporton vagy rendelkezésre állási csoporton belül vannak.

- **Azure CycleCloud** – HPC-fürt létrehozása az [Azure CycleCloud](/azure/cyclecloud/) használatával MPI-feladatok futtatásához.

- **Azure batch** – hozzon létre egy [Azure batch](../batch/index.yml) -készletet az MPI-munkaterhelések futtatásához. Ha Azure Batch használatával MPI-alkalmazások futtatásához nagy számítási igényű példányokat szeretne használni, tekintse meg a [többpéldányos feladatok használata a Message Passing Interface (MPI) alkalmazások Azure batch-ben való futtatásához](../batch/batch-mpi.md)című témakört.

- **Microsoft HPC Pack**  -  A [HPC Pack](/powershell/high-performance-computing/overview) tartalmaz egy futásidejű környezetet az MS-MPI számára, amely az Azure RDMA hálózatot használja RDMA-kompatibilis Linux virtuális gépeken való üzembe helyezéskor. Központi telepítések például: [Linux RDMA-fürt beállítása HPC-csomaggal MPI-alkalmazások futtatásához](/powershell/high-performance-computing/hpcpack-linux-openfoam).

## <a name="deployment-considerations"></a>Telepítési szempontok

- **Azure-előfizetés** – több számítási igényű példány üzembe helyezéséhez vegye fontolóra az utólagos elszámolású előfizetést vagy az egyéb vásárlási lehetőségeket. Amennyiben [ingyenes Azure-fiókot](https://azure.microsoft.com/free/) használ, csak korlátozott számú számítási magot használhat az Azure-ban.

- **Díjszabás és rendelkezésre állás** – tekintse meg a [virtuális gépek díjszabását](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) és [elérhetőségét](https://azure.microsoft.com/global-infrastructure/services/) az Azure-régiók között.

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
- Tekintse át a [HBv3-sorozat áttekintését](./workloads/hpc/hbv3-series-overview.md) és a [HC-sorozat áttekintését](./workloads/hpc/hc-series-overview.md).
- Olvassa el a legújabb bejelentéseket, a HPC számítási feladatait és a teljesítmény eredményeit az [Azure számítási technikai közösségi blogokban](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute).
- A HPC-munkaterhelések futtatásának magasabb szintű építészeti áttekintését lásd: [nagy teljesítményű számítástechnika (HPC) az Azure](/azure/architecture/topics/high-performance-computing/)-ban.
