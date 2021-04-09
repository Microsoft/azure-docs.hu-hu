---
title: H-sorozat – Azure Virtual Machines
description: A H-sorozatú virtuális gépek specifikációi.
author: vermagit
ms.service: virtual-machines
ms.subservice: vm-sizes-hpc
ms.topic: conceptual
ms.date: 10/09/2020
ms.author: amverma
ms.reviewer: jushiman
ms.openlocfilehash: b3e3beb51256bbf22d29d74b51c52ca3a6bee0c4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "104774779"
---
# <a name="h-series"></a>H-sorozat

A H-sorozatú virtuális gépeket nagy CPU-gyakorisággal vagy nagy memóriával rendelkező, alapvető követelményekkel rendelkező alkalmazások számára optimalizáltuk. A H-sorozatú virtuális gépek 8 vagy 16 Intel Xeon E5 2667 v3 processzorral rendelkeznek, CPU Core-onként akár 14 GB RAM-mal, és nem feleznie. A H-sorozat 56 GB/s Mellanox FDR InfiniBand a konzisztens RDMA teljesítményének nem blokkoló FAT-konfigurációjában. A H-sorozatú virtuális gépek jelenleg nem engedélyezettek az SR-IOV, és támogatják az Intel MPI 5. x és MS-MPI használatát.

[ACU](acu.md): 290-300<br>
[Premium Storage](premium-storage-performance.md): nem támogatott<br>
[Premium Storage gyorsítótárazás](premium-storage-performance.md): nem támogatott<br>
[Élő áttelepítés](maintenance-and-updates.md): nem támogatott<br>
[Memória-megőrzési frissítések](maintenance-and-updates.md): nem támogatott<br>
[VM-generáció támogatása](generation-2.md): 1. generáció<br>
[Gyorsított hálózatkezelés](../virtual-network/create-vm-accelerated-networking-cli.md): nem támogatott<br>
[Ideiglenes operációsrendszer-lemezek](ephemeral-os-disks.md): nem támogatott <br>
<br>

| Méret | vCPU | Processzor | Memória (GiB) | Memória sávszélessége GB/s | Alapszintű CPU-gyakoriság (GHz) | Teljes magok gyakorisága (GHz, csúcs) | Egymagos gyakoriság (GHz, csúcs) | RDMA teljesítmény (GB/s) | MPI-támogatás | Ideiglenes tároló (GiB) | Adatlemezek max. száma | Lemezek max. teljesítménye: IOPS | Maximális Ethernet-Vnic |
| --- | --- |--- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_H8   | 8  | Intel Xeon E5 2667 v3 | 56 | 40 | 3.2 | 3.3 | 3,6 | - | Intel 5. x, MS-MPI | 1000 | 32 | 32x500 | 2 |
| Standard_H16  | 16 | Intel Xeon E5 2667 v3 | 112 | 80 | 3.2 | 3.3 | 3,6 | - | Intel 5. x, MS-MPI | 2000 | 64 | 64x500 | 4 |
| Standard_H8m  | 8  | Intel Xeon E5 2667 v3 | 112 | 40 | 3.2 | 3.3 | 3,6 | - | Intel 5. x, MS-MPI | 1000 | 32 | 32x500 | 2 |
| Standard_H16m | 16 | Intel Xeon E5 2667 v3 | 224 | 80 | 3.2 | 3.3 | 3,6 | - | Intel 5. x, MS-MPI | 2000 | 64 | 64x500 | 4 |
| Standard_H16r <sup>1</sup>  | 16 | Intel Xeon E5 2667 v3 | 112 | 80 | 3.2 | 3.3 | 3,6 | 56 | Intel 5. x, MS-MPI | 2000 | 64 | 64x500 | 4 |
| Standard_H16mr <sup>1</sup> | 16 | Intel Xeon E5 2667 v3 | 224 | 80 | 3.2 | 3.3 | 3,6 | 56 | Intel 5. x, MS-MPI | 2000 | 64 | 64x500 | 4 |

<sup>1</sup> az MPI-alkalmazások esetében a dedikált RDMA háttérrendszer-hálózatot a FDR InfiniBand hálózata engedélyezte.

> [!NOTE]
> A [RDMA-kompatibilis virtuális gépek](sizes-hpc.md#rdma-capable-instances)között a H-sorozat nem engedélyezett az SR-IOV. Ezért a támogatott virtuálisgép- [rendszerképek](./workloads/hpc/configure.md#vm-images), a [InfiniBand-illesztőprogram](./workloads/hpc/enable-infiniband.md) követelményei és a támogatott MPI- [kódtárak](./workloads/hpc/setup-mpi.md) eltérnek az SR-IOV-kompatibilis virtuális gépektől.

## <a name="software-specifications"></a>Szoftverek specifikációi

| Szoftverek specifikációi     |HC sorozatú virtuális gép           |
|-----------------------------|-----------------------|
| MPI-feladatok maximális mérete            | 4800 mag (300 virtuális gép egyetlen virtuálisgép-méretezési készletben, singlePlacementGroup = true)  |
| MPI-támogatás                 | Intel MPI 5. x, MS-MPI  |
| Operációs rendszer támogatása nem CSATOLÓJÁHOZ nincs SR RDMA   | CentOS/RHEL 6,5-7,4, SLES 12 SP4 +, WinServer 2012-2016  |
| Orchestrator-támogatás        | CycleCloud, batch, AK  |

[!INCLUDE [hpc-include](./workloads/hpc/includes/hpc-include.md)]

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="other-sizes"></a>Egyéb méretek

- [Általános célú](sizes-general.md)
- [Memóriaoptimalizált](sizes-memory.md)
- [Tároptimalizált](sizes-storage.md)
- [GPU-optimalizált](sizes-gpu.md)
- [Nagy teljesítményű számítás](sizes-hpc.md)
- [Előző generációk](sizes-previous-gen.md)

## <a name="next-steps"></a>Következő lépések

- Olvassa el a legújabb bejelentéseket, a HPC számítási feladatait és a teljesítmény eredményeit az [Azure számítási technikai közösségi blogokban](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute).
- A HPC-munkaterhelések futtatásának magasabb szintű építészeti áttekintését lásd: [nagy teljesítményű számítástechnika (HPC) az Azure](/azure/architecture/topics/high-performance-computing/)-ban.
- További információ arról, hogy az [Azure számítási egységei (ACU)](acu.md) hogyan segíthetnek az Azure SKU-ban a számítási teljesítmény összehasonlításában.
