---
title: HBv3 sorozatú virtuális gépek áttekintése, architektúra, topológia – Azure Virtual Machines | Microsoft Docs
description: Ismerje meg az Azure-beli HBv3-sorozatú virtuális gépek méretét.
services: virtual-machines
author: vermagit
tags: azure-resource-manager
ms.service: virtual-machines
ms.subservice: workloads
ms.workload: infrastructure-services
ms.topic: article
ms.date: 03/25/2021
ms.author: amverma
ms.reviewer: cynthn
ms.openlocfilehash: f78420a65cd9c2402266eb9ba973eabe758d7ee5
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105608242"
---
# <a name="hbv3-series-virtual-machine-overview"></a>A HBv3 sorozatú virtuális gépek áttekintése 

A [HBv3 sorozatú](../../hbv3-series.md) kiszolgáló 2 * 64-Core EPYC 7V13 CPU-t tartalmaz összesen 128 fizikai "Zen3" maggal. Az egyidejű többszálú (SMT) le van tiltva a HBv3. Ezek a 128-es magok 16 szakaszra vannak osztva (8/szoftvercsatorna), és mindegyik szakasz 8 processzoros magot tartalmaz, és egységes hozzáféréssel rendelkezik egy 32 MB-os gyorsítótárhoz. Az Azure HBv3-kiszolgálók a következő AMD BIOS-beállításokat is futtatják:

```bash
Nodes per Socket (NPS) = 2
L3 as NUMA = Disabled
NUMA domains within VM OS = 4
C-states = Enabled
```

Ennek eredményeképpen a kiszolgáló 4 NUMA-tartománnyal (2 szoftvercsatorna) indul el, és minden 32-mag mérete megtörténik. Minden NUMA közvetlen hozzáférést biztosít 4 csatornához, amely 3200 MT/s-ban üzemel.

Ha az Azure Hypervisort úgy kívánja biztosítani, hogy a virtuális gép beavatkozása nélkül működjön, a kiszolgáló 8 fizikai magot foglal le.

## <a name="vm-topology"></a>VM-topológia

Az alábbi ábrán a kiszolgáló topológiája látható. Ezeket a 8 hypervisoros gazdagép-magot (sárga) a CPU-szoftvercsatornán szimmetrikusan tartjuk fenn, így az első 2 mag az egyes NUMA-tartományokon (CCD) a HBv3 sorozatú virtuális géphez (zöld) marad.

![A HBv3-sorozat kiszolgálójának topológiája](./media/architecture/hbv3/hbv3-topology-server.png)

Vegye figyelembe, hogy a CCD határa nem felel meg a NUMA-határnak. A HBv3-on négy egymást követő (4) CCD-csoport van konfigurálva NUMA-tartományként, mind a gazdagépek, mind a vendég virtuális gépen. Így az összes HBv3 VM-méret 4 NUMA-tartományt tesz elérhetővé, amelyek egy operációs rendszerre és alkalmazásra mutatnak, ahogyan az alább látható, 4 egységes NUMA-tartomány, amelyek mindegyike különböző számú maggal rendelkezik az adott [HBv3 virtuális gép méretétől](../../hbv3-series.md)függően.

![A HBv3 sorozatú virtuális gép topológiája](./media/architecture/hbv3/hbv3-topology-vm.png)

Az egyes HBv3 virtuálisgép-méretek hasonlóak az AMD EPYC 7003 sorozatának fizikai elrendezésében, szolgáltatásaiban és teljesítményében, az alábbiak szerint:

| HBv3 sorozatú virtuális gép mérete             | NUMA-tartományok | Magok száma NUMA-tartományban  | Hasonlóság az AMD EPYC         |
|---------------------------------|--------------|------------------------|----------------------------------|
Standard_HB120rs_v3               | 4            | 30                     | Kettős szoftvercsatorna EPYC 7713            |
Standard_HB120r – 96s_v3            | 4            | 24                     | Kettős szoftvercsatorna EPYC 7643            |
Standard_HB120r – 64s_v3            | 4            | 16                     | Kettős szoftvercsatorna EPYC 7543            |
Standard_HB120r – 32s_v3            | 4            | 8                      | Kettős szoftvercsatorna EPYC 7313            |
Standard_HB120r – 16s_v3            | 4            | 4                      | Kettős szoftvercsatorna EPYC 72F3            |

> [!NOTE]
> A korlátozott magok virtuálisgép-méretek csak a virtuális géphez elérhető fizikai magok számát csökkentik. Minden globális megosztott eszköz (RAM, memória-sávszélesség, L3 gyorsítótár, GMI és xGMI kapcsolat, InfiniBand, Azure Ethernet-hálózat, helyi SSD) állandó marad. Ez lehetővé teszi, hogy az ügyfél olyan virtuálisgép-méretet válasszon, amely a legmegfelelőbb munkaterhelés-vagy szoftverlicenc-igényekhez igazodik.

Az egyes HBv3 virtuálisgép-méretek virtuális NUMA-leképezése a mögöttes fizikai NUMA-topológiára van leképezve. A hardveres topológia nem lehet megtévesztő absztrakt. 

A [HBv3 virtuális gép különböző méretének](../../hbv3-series.md) pontos topológiája a következőképpen jelenik meg a [lstopo](https://linux.die.net/man/1/lstopo)kimenetének használatával:
```bash
lstopo-no-graphics --no-io --no-legend --of txt
```
<br>
<details>
<summary>Ide kattintva megtekintheti Standard_HB120rs_v3 lstopo kimenetét</summary>

![lstopo-kimenet HBv3-120 virtuális géphez](./media/architecture/hbv3/hbv3-120-lstopo.png)
</details>

<details>
<summary>Ide kattintva megtekintheti az Standard_HB120rs-96_v3 lstopo-kimenetét</summary>

![lstopo-kimenet HBv3-96 virtuális géphez](./media/architecture/hbv3/hbv3-96-lstopo.png)
</details>

<details>
<summary>Ide kattintva megtekintheti az Standard_HB120rs-64_v3 lstopo-kimenetét</summary>

![lstopo-kimenet HBv3-64 virtuális géphez](./media/architecture/hbv3/hbv3-64-lstopo.png)
</details>

<details>
<summary>Ide kattintva megtekintheti az Standard_HB120rs-32_v3 lstopo-kimenetét</summary>

![lstopo-kimenet HBv3-32 virtuális géphez](./media/architecture/hbv3/hbv3-32-lstopo.png)
</details>

<details>
<summary>Ide kattintva megtekintheti az Standard_HB120rs-16_v3 lstopo-kimenetét</summary>

![lstopo-kimenet HBv3-16 virtuális géphez](./media/architecture/hbv3/hbv3-16-lstopo.png)
</details>

## <a name="infiniband-networking"></a>InfiniBand hálózatkezelés
A HBv3 VM-EK emellett az NVIDIA Mellanox HDR InfiniBand hálózati adaptereket (ConnectX-6) is üzemeltetik, amelyek akár 200 Gigabit/s-ban üzemelnek. A hálózati ADAPTERt a CSATOLÓJÁHOZ nincs SR-en keresztül továbbítják a virtuális géphez, amely lehetővé teszi a hálózati forgalom számára a hypervisor megkerülését. Ennek eredményeképpen az ügyfelek a standard szintű Mellanox OFED a HBv3 virtuális gépeken, mivel azok operációs rendszer nélküli környezetben történnek.

A HBv3 VM-EK támogatják az adaptív útválasztást, a dinamikus csatlakoztatott átvitelt (a DCT-t, amely a standard RC és a UD átvitelt is támogatja), valamint az MPI-ket a ConnectX-6 adapter bevezetési feldolgozójának a hardveres alapú kiszervezését. Ezek a funkciók növelik az alkalmazások teljesítményét, méretezhetőségét és konzisztenciáját, és használata erősen ajánlott.

## <a name="temporary-storage"></a>Ideiglenes tároló
A HBv3 virtuális gépek 3 fizikailag helyi SSD-eszközt is tartalmaz. Az egyik eszköz úgy van formázva, hogy lapozófájlként jelenjen meg, és a virtuális gépen egy általános "SSD" eszközként jelenjen meg.

Két másik, a nagyobb SSD-k formázatlan blokkos NVMe-eszközökként vannak megadva a NVMeDirect-n keresztül. Mivel a blokk NVMe-eszköz megkerüli a Hypervisort, magasabb sávszélességgel, magasabb IOPS és IOP kisebb késéssel fog rendelkezni.

Csíkozott tömbben párosítva a NVMe SSD legfeljebb 7 GB/s olvasási és 3 GB/s írást biztosít, valamint akár 186 000 IOPS (olvasás) és 201 000 IOPS (írás) a mély üzenetsor-mélységekhez.

## <a name="hardware-specifications"></a>Hardverspecifikációk 

| Hardverspecifikációk          | HBv3 sorozatú virtuális gépek              |
|----------------------------------|----------------------------------|
| Cores                            | 120, 96, 64, 32 vagy 16 (SMT letiltva)               | 
| CPU                              | AMD EPYC 7V13                   | 
| CPU-gyakoriság (nem AVX)          | 3,1 GHz (minden mag), 3,675 GHz (legfeljebb 10 mag)    | 
| Memória                           | 448 GB (a RAM/mag a virtuális gép méretétől függ)         | 
| Helyi lemez                       | 2 * 960 GB NVMe (blokk), 480 GB SSD (lapozófájl) | 
| InfiniBand                       | 200 GB/s Mellanox ConnectX-6 HDR-InfiniBand | 
| Network (Hálózat)                          | 50 GB/s Ethernet (40 GB/s használható) Azure második Gen SmartNIC | 

## <a name="software-specifications"></a>Szoftverek specifikációi 

| Szoftverek specifikációi        | HBv3 sorozatú virtuális gépek                                            | 
|--------------------------------|-----------------------------------------------------------|
| MPI-feladatok maximális mérete               | 36 000 mag (300 virtuális gép egyetlen virtuálisgép-méretezési készletben, singlePlacementGroup = true) |
| MPI-támogatás                    | HPC-X, Intel MPI, OpenMPI, MVAPICH2, MPICH  |
| További keretrendszerek          | UCX, libfabric, PGAS                  |
| Azure Storage-támogatás          | Standard és prémium szintű lemezek (maximális 32 lemez)              |
| Operációs rendszer támogatása a CSATOLÓJÁHOZ nincs SR RDMA      | CentOS/RHEL 7.6 +, Ubuntu 18.04 +, SLES 12 SP4 +, WinServer 2016 +           |
| Ajánlott operációs rendszer a teljesítményhez | CentOS 8,1, Windows Server 2019 +
| Orchestrator-támogatás           | Azure CycleCloud, Azure Batch, AK; [fürt konfigurációs beállításai](../../sizes-hpc.md#cluster-configuration-options)                      | 

> [!NOTE] 
> A Windows Server 2012 R2 nem támogatott a HBv3 és más, több mint 64 (virtuális vagy fizikai) magokkal rendelkező virtuális gépeken. További információt [itt](https://docs.microsoft.com/windows-server/virtualization/hyper-v/supported-windows-guest-operating-systems-for-hyper-v-on-windows) talál.

## <a name="next-steps"></a>Következő lépések

- Olvassa el a legújabb bejelentéseket, a HPC számítási feladatait és a teljesítmény eredményeit az [Azure számítási technikai közösségi blogokban](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute).
- A HPC-munkaterhelések futtatásának magasabb szintű építészeti áttekintését lásd: [nagy teljesítményű számítástechnika (HPC) az Azure](/azure/architecture/topics/high-performance-computing/)-ban.
