---
title: HBv3 sorozat – Azure Virtual Machines
description: A HBv3 sorozatú virtuális gépek specifikációi.
author: vermagit
ms.service: virtual-machines
ms.subservice: vm-sizes-hpc
ms.topic: conceptual
ms.date: 03/12/2021
ms.author: amverma
ms.reviewer: cynthn
ms.openlocfilehash: 21576b6714c05e5a0ed703c7a1452103c2c4b355
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "103472839"
---
# <a name="hbv3-series"></a>HBv3 sorozat

A HBv3 sorozatú virtuális gépek olyan HPC-alkalmazásokra vannak optimalizálva, mint például a Fluid Dynamics, a explicit és az implicit véges elemek elemzése, az időjárási modellezés, a szeizmikus feldolgozás, a tározó szimulálása és az RTL szimuláció. A HBv3 virtuális gépek akár 120 AMD EPYC™ 7003 sorozatú (Milan) CPU-magot, 448 GB RAM-ot és feleznie nem. A HBv3 sorozatú virtuális gépek a memória sávszélességének 350 GB/s-os 3,675 sebességét is biztosítják, amely akár 32 MB/s-os gyorsítótár/mag is lehet. 

Az összes HBv3 sorozatú virtuális gép 200 GB/s-os HDR-InfiniBand tartalmaz az NVIDIA Hálózatkezelésből a nagyszámítógépi szintű MPI-munkaterhelések lehetővé tételéhez. Ezek a virtuális gépek az optimalizált és konzisztens RDMA teljesítmény érdekében nem blokkoló FAT-fában vannak csatlakoztatva. A HDR InfiniBand-háló az adaptív útválasztást és a dinamikus csatlakoztatott átvitelt is támogatja (a DCT a standard RC és UD átvitelek esetében). Ezek a szolgáltatások javítják az alkalmazások teljesítményét, méretezhetőségét és konzisztenciáját, és a használatuk erősen ajánlott.

[Premium Storage](premium-storage-performance.md): támogatott<br>
[Premium Storage gyorsítótárazás](premium-storage-performance.md): támogatott<br>
[Élő áttelepítés](maintenance-and-updates.md): nem támogatott<br>
[Memória-megőrzési frissítések](maintenance-and-updates.md): nem támogatott<br>
[VM-létrehozási támogatás](generation-2.md): 1. és 2. generáció<br>
[Gyorsított hálózat](../virtual-network/create-vm-accelerated-networking-cli.md): hamarosan elérhető<br>
[Ideiglenes operációsrendszer-lemezek](ephemeral-os-disks.md): nem támogatott <br>


|Méret |vCPU |Processzor |Memória (GiB) |Memória sávszélessége GB/s |Alapszintű CPU-gyakoriság (GHz) |Teljes magok gyakorisága (GHz, csúcs) |Egymagos gyakoriság (GHz, csúcs) |RDMA teljesítmény (GB/s) |MPI-támogatás |Ideiglenes tároló (GiB) |Adatlemezek max. száma |Maximális Ethernet-Vnic |
|----|----|----|----|----|----|----|----|----|----|----|----|----|
|Standard_HB120rs_v3    |120 |AMD EPYC 7V13 |448 |350 |2.45 |3,1 |3,675 |200 |Mind |2 * 960 |32 |8 |
|Standard_HB120 – 96rs_v3 |96  |AMD EPYC 7V13 |448 |350 |2.45 |3,1 |3,675 |200 |Mind |2 * 960 |32 |8 |
|Standard_HB120 – 64rs_v3 |64  |AMD EPYC 7V13 |448 |350 |2.45 |3,1 |3,675 |200 |Mind |2 * 960 |32 |8 |
|Standard_HB120 – 32rs_v3 |32  |AMD EPYC 7V13 |448 |350 |2.45 |3,1 |3,675 |200 |Mind |2 * 960 |32 |8 |
|Standard_HB120 – 16rs_v3 |16  |AMD EPYC 7V13 |448 |350 |2.45 |3,1 |3,675 |200 |Mind |2 * 960 |32 |8 |


További információk:
- Alapul szolgáló [architektúra és VM-topológia](./workloads/hpc/hbv3-series-overview.md)
- [Támogatott szoftveres verem](./workloads/hpc/hbv3-series-overview.md#software-specifications) , beleértve a támogatott operációs rendszert
- A HBv3 sorozatú virtuális gép várt [teljesítménye](./workloads/hpc/hbv3-performance.md) .

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