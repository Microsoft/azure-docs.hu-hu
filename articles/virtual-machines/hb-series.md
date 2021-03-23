---
title: HB-sorozat
description: A HB sorozatú virtuális gépek specifikációi.
author: vermagit
ms.service: virtual-machines
ms.subservice: vm-sizes-hpc
ms.topic: conceptual
ms.date: 03/22/2021
ms.author: amverma
ms.reviewer: jushiman
ms.openlocfilehash: 92a46c7f9d7cf86787bb1707c41ae0e44c37fc60
ms.sourcegitcommit: ba3a4d58a17021a922f763095ddc3cf768b11336
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/23/2021
ms.locfileid: "104800907"
---
# <a name="hb-series"></a>HB-sorozat

A HB sorozatú virtuális gépek olyan alkalmazásokra vannak optimalizálva, amelyek memória-sávszélességen alapulnak, például a Fluid Dynamics, a explicit véges elemek elemzése és az időjárási modellezés. A HB-beli virtuális gépeken 60 AMD EPYC 7551 processzor-mag, 4 GB RAM/CPU mag, egyidejű többszálúség nélkül. A HB virtuális gépek akár 260 GB/s memóriát is biztosítanak a memória sávszélességéhez.

A HB sorozatú virtuális gépek 100 GB/s Mellanox EDR InfiniBand rendelkeznek. Ezek a virtuális gépek az optimalizált és konzisztens RDMA teljesítmény érdekében nem blokkoló FAT-fában vannak csatlakoztatva. Ezek a virtuális gépek támogatják az adaptív útválasztást és a dinamikus csatlakozású átvitelt (DCT, a standard RC és UD átviteleken kívül). Ezek a szolgáltatások javítják az alkalmazások teljesítményét, méretezhetőségét és konzisztenciáját, és a használatuk ajánlott.

[ACU](acu.md): 199-216<br>
[Premium Storage](premium-storage-performance.md): támogatott<br>
[Premium Storage gyorsítótárazás](premium-storage-performance.md): támogatott<br>
[Élő áttelepítés](maintenance-and-updates.md): nem támogatott<br>
[Memória-megőrzési frissítések](maintenance-and-updates.md): nem támogatott<br>
[VM-létrehozási támogatás](generation-2.md): 1. és 2. generáció<br>
[Gyorsított hálózatkezelés](../virtual-network/create-vm-accelerated-networking-cli.md):[támogatott (További információ a](https://techcommunity.microsoft.com/t5/azure-compute/accelerated-networking-on-hb-hc-hbv2-and-ndv2/ba-p/2067965) teljesítményről és a lehetséges problémákról) <br>
[Ideiglenes operációsrendszer-lemezek](ephemeral-os-disks.md): nem támogatott <br>
<br>

| Méret | vCPU | Processzor | Memória (GiB) | Memória sávszélessége GB/s | Alapszintű CPU-gyakoriság (GHz) | Teljes magok gyakorisága (GHz, csúcs) | Egymagos gyakoriság (GHz, csúcs) | RDMA teljesítmény (GB/s) | MPI-támogatás | Ideiglenes tároló (GiB) | Adatlemezek max. száma | Maximális Ethernet-Vnic |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_HB60rs | 60 | AMD EPYC 7551 | 228 | 263 | 2.0 | 2.55 | 2.55 | 100 | Mind | 700 | 4 | 8 |

További információ:
- [Architektúra és VM-topológia](./workloads/hpc/hb-series-overview.md)
- Támogatott [szoftveres verem](./workloads/hpc/hb-series-overview.md#software-specifications) , beleértve a támogatott operációs rendszert
- A HB sorozatú virtuális gép várt [teljesítménye](./workloads/hpc/hb-series-performance.md)

[!INCLUDE [hpc-include.md](./workloads/hpc/includes/hpc-include.md)]

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
