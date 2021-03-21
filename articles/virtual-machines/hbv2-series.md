---
title: HBv2 sorozat – Azure Virtual Machines
description: A HBv2 sorozatú virtuális gépek specifikációi.
author: vermagit
ms.service: virtual-machines
ms.subservice: vm-sizes-hpc
ms.topic: conceptual
ms.date: 03/08/2021
ms.author: amverma
ms.reviewer: jushiman
ms.openlocfilehash: 1abc05cf1486651b87094f40777f3679d234a34b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "103009337"
---
# <a name="hbv2-series"></a>HBv2 sorozat

A HBv2 sorozatú virtuális gépek olyan alkalmazásokra vannak optimalizálva, amelyek memória-sávszélességen alapulnak, például a Fluid Dynamics, a véges elemek elemzése és a tározó szimulálása. A HBv2 virtuális gépek szolgáltatás 120 AMD EPYC 7742 processzor-mag, 4 GB RAM/CPU mag, és nem egyidejű többszálú. Minden HBv2-alapú virtuális gép akár 340 GB/s memóriát, valamint akár 4 teraFLOPS FP64-számítást biztosít.

A HBv2 sorozatú virtuális gépek 200 GB/s Mellanox, HDR-InfiniBand rendelkeznek. Ezek a virtuális gépek az optimalizált és konzisztens RDMA teljesítmény érdekében nem blokkoló FAT-fában vannak csatlakoztatva. Ezek a virtuális gépek támogatják az adaptív útválasztást és a dinamikus csatlakozású átvitelt (DCT, a standard RC és UD átviteleken kívül). Ezek a szolgáltatások javítják az alkalmazások teljesítményét, méretezhetőségét és konzisztenciáját, és a használatuk ajánlott.

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
| Standard_HB120rs_v2 | 120 | AMD EPYC 7V12 | 456 | 350 | 2.45 | 3,1 | 3.3 | 200 | Mind | 480 + 960 | 8 | 8 |

További információk:
- Alapul szolgáló [architektúra és VM-topológia](./workloads/hpc/hbv2-series-overview.md)
- [Támogatott szoftveres verem](./workloads/hpc/hbv2-series-overview.md#software-specifications) , beleértve a támogatott operációs rendszert
- A HBv2 sorozatú virtuális gép várt [teljesítménye](./workloads/hpc/hbv2-performance.md) .

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
