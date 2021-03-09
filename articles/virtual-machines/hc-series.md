---
title: HC-sorozat – Azure Virtual Machines
description: A HC-sorozatú virtuális gépek specifikációi.
author: vermagit
ms.service: virtual-machines
ms.subservice: hpc
ms.topic: conceptual
ms.date: 03/05/2021
ms.author: amverma
ms.reviewer: jushiman
ms.openlocfilehash: 630d4ab1e20e92cc9dbe3881469df7f209078fba
ms.sourcegitcommit: 8d1b97c3777684bd98f2cfbc9d440b1299a02e8f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/09/2021
ms.locfileid: "102485697"
---
# <a name="hc-series"></a>HC-sorozat

A HC sorozatú virtuális gépek a sűrű számítások által vezérelt alkalmazások számára vannak optimalizálva, mint például az implicit, véges elemek elemzése, a molekuláris dinamika és a számítási kémia. A HC-alapú virtuális gépek a 44 Intel Xeon Platinum 8168 processzor-magot, a CPU-mag 8 GB RAM-ot, a feleznie pedig nem rendelkeznek. Az Intel Xeon Platinum platform támogatja az Intel olyan szoftverek gazdag ökoszisztémáját, mint az Intel Math kernel Library és a speciális vektoros feldolgozási képességek, például a AVX-512.

A HC sorozatú virtuális gépek 100 GB/s Mellanox EDR InfiniBand rendelkeznek. Ezek a virtuális gépek az optimalizált és konzisztens RDMA teljesítmény érdekében nem blokkoló FAT-fában vannak csatlakoztatva. Ezek a virtuális gépek támogatják az adaptív útválasztást és a dinamikus csatlakozású átvitelt (a DCT a standard RC és UD átvitelek esetében). Ezek a szolgáltatások javítják az alkalmazások teljesítményét, méretezhetőségét és konzisztenciáját, és a használatuk ajánlott. 

[ACU](acu.md): 297-315<br>
[Premium Storage](premium-storage-performance.md): támogatott<br>
[Premium Storage gyorsítótárazás](premium-storage-performance.md): támogatott<br>
[Élő áttelepítés](maintenance-and-updates.md): nem támogatott<br>
[Memória-megőrzési frissítések](maintenance-and-updates.md): nem támogatott<br>
[VM-létrehozási támogatás](generation-2.md): 1. és 2. generáció<br>
[Gyorsított hálózatkezelés](../virtual-network/create-vm-accelerated-networking-cli.md):[támogatott (További információ a](https://techcommunity.microsoft.com/t5/azure-compute/accelerated-networking-on-hb-hc-hbv2-and-ndv2/ba-p/2067965) teljesítményről és a lehetséges problémákról)<br>
[Ideiglenes operációsrendszer-lemezek](ephemeral-os-disks.md): támogatott <br>

<br>

| Méret | vCPU | Processzor | Memória (GiB) | Memória sávszélessége GB/s | Alapszintű CPU-gyakoriság (GHz) | Teljes magok gyakorisága (GHz, csúcs) | Egymagos gyakoriság (GHz, csúcs) | RDMA teljesítmény (GB/s) | MPI-támogatás | Ideiglenes tároló (GiB) | Adatlemezek max. száma | Maximális Ethernet-Vnic |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_HC44rs | 44 | Intel Xeon Platinum 8168 | 352 | 191 | 2.7 | 3.4 | 3.7 | 100 | Mind | 700 | 4 | 8 |

További információ a mögöttes [architektúráról, a virtuális gép topológiáról](./workloads/hpc/hc-series-overview.md) és a HC-sorozatú virtuális gép várt [teljesítményéről](./workloads/hpc/hc-series-performance.md) .

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

- Olvassa el a legújabb bejelentéseket, a HPC munkaterhelés-példákat és a teljesítmény eredményeit az [Azure számítási technikai közösségi blogokban](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute).
- A HPC-munkaterhelések futtatásának magas szintű építészeti áttekintését lásd: [nagy teljesítményű számítástechnika (HPC) az Azure](/azure/architecture/topics/high-performance-computing/)-ban.
- További információ arról, hogy az [Azure számítási egységei (ACU)](acu.md) hogyan segíthetnek az Azure SKU-ban a számítási teljesítmény összehasonlításában.
