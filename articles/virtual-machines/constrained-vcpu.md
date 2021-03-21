---
title: Korlátozott vCPU méretek
description: Felsorolja azokat a virtuálisgép-méreteket, amelyek képesek korlátozott számú vCPU.
author: mimckitt
ms.service: virtual-machines
ms.subservice: sizes
ms.topic: conceptual
ms.date: 03/09/2018
ms.author: mimckitt
ms.openlocfilehash: 7faeec8494a908b9aab00be9b63904354b5e0994
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102557080"
---
# <a name="constrained-vcpu-capable-vm-sizes"></a>Korlátozott vCPU képességgel rendelkező VM-méretek

Egyes adatbázis-munkaterhelések, például az SQL Server vagy az Oracle, nagy mennyiségű memóriát, tárterületet és I/O-sávszélességet igényelnek, de nem magas alapszám. Sok adatbázis-munkaterhelés nem CPU-igényes. Az Azure bizonyos virtuálisgép-méreteket biztosít, amelyekkel korlátozható a virtuális gépek vCPU száma, így csökkenthető a szoftverek licencelésének díja, miközben ugyanaz a memória, a tárterület és az I/O-sávszélesség is fennmarad.

A vCPU száma a virtuális gép eredeti méretének egy vagy több negyedére korlátozható. Ezek az új virtuálisgép-méretek egy utótaggal rendelkeznek, amely meghatározza, hogy az aktív vCPU hányan könnyebben azonosíthatók legyenek.

A virtuális gép jelenlegi Standard_GS5 mérete például a következő: 32 vCPU, 448 GB RAM, 64 lemez (akár 256 TB), valamint 80 000 IOPs vagy 2 GB/s az I/O-sávszélesség. Az új virtuálisgép-méretek a Standard_GS5-16 és a Standard_GS5-8 esetében 16 – 8 aktív vCPU, a Standard_GS5 a memória, a tárterület és az I/O-sávszélesség további specifikációinak fenntartása mellett.

A SQL Server vagy Oracle esetében felszámított licencelési díjak az új vCPU-számra vannak korlátozva, és más termékeket az új vCPU száma alapján kell fizetni. Ennek eredményeképpen 50% – 75% növekszik a virtuálisgép-specifikációk aktív (számlázható) vCPU arányában. Ezek az új virtuálisgép-méretek lehetővé teszik az ügyfelek számára, hogy ugyanazt a memóriát, tárterületet és I/O-sávszélességet használják, miközben optimalizálja a szoftver licencelési költségeit. Jelenleg a számítási díj, amely magában foglalja az operációs rendszer licencelését, ugyanaz marad, mint az eredeti méret. További információ: Azure-beli [virtuális gépek méretei a költséghatékony adatbázis](https://azure.microsoft.com/blog/announcing-new-azure-vm-sizes-for-more-cost-effective-database-workloads/)-számítási feladatokhoz.


| Name                | vCPU | Jellemzők           |
|---------------------|------|-----------------|
| Standard_M8 – 2ms     | 2    | Ugyanaz, mint a M8ms    |
| Standard_M8 – 4ms     | 4    | Ugyanaz, mint a M8ms    |
| Standard_M16 – 4ms    | 4    | Ugyanaz, mint a M16ms   |
| Standard_M16 – 8ms    | 8    | Ugyanaz, mint a M16ms   |
| Standard_M32 – 8ms    | 8    | Ugyanaz, mint a M32ms   |
| Standard_M32 – 16ms   | 16   | Ugyanaz, mint a M32ms   |
| Standard_M64 – 32ms   | 32   | Ugyanaz, mint a M64ms   |
| Standard_M64 – 16ms   | 16   | Ugyanaz, mint a M64ms   |
| Standard_M128 – 64ms  | 64   | Ugyanaz, mint a M128ms  |
| Standard_M128 – 32ms  | 32   | Ugyanaz, mint a M128ms  |
| Standard_E4 – 2s_v3   | 2    | Ugyanaz, mint E4s_v3  |
| Standard_E8 – 4s_v3   | 4    | Ugyanaz, mint E8s_v3  |
| Standard_E8 – 2s_v3   | 2    | Ugyanaz, mint E8s_v3  |
| Standard_E16 – 8s_v3  | 8    | Ugyanaz, mint E16s_v3 |
| Standard_E16 – 4s_v3  | 4    | Ugyanaz, mint E16s_v3 |
| Standard_E32 – 16s_v3 | 16   | Ugyanaz, mint E32s_v3 |
| Standard_E32 – 8s_v3  | 8    | Ugyanaz, mint E32s_v3 |
| Standard_E64 – 32s_v3 | 32   | Ugyanaz, mint E64s_v3 |
| Standard_E64 – 16s_v3 | 16   | Ugyanaz, mint E64s_v3 |
| Standard_E4 – 2s_v4   | 2    | Ugyanaz, mint E4s_v4  |
| Standard_E8 – 4s_v4   | 4    | Ugyanaz, mint E8s_v4  |
| Standard_E8 – 2s_v4   | 2    | Ugyanaz, mint E8s_v4  |
| Standard_E16 – 8s_v4  | 8    | Ugyanaz, mint E16s_v4 |
| Standard_E16 – 4s_v4  | 4    | Ugyanaz, mint E16s_v4 |
| Standard_E32 – 16s_v4 | 16   | Ugyanaz, mint E32s_v4 |
| Standard_E32 – 8s_v4  | 8    | Ugyanaz, mint E32s_v4 |
| Standard_E64 – 32s_v4 | 32   | Ugyanaz, mint E64s_v4 |
| Standard_E64 – 16s_v4 | 16   | Ugyanaz, mint E64s_v4 |
| Standard_E4 – 2ds_v4  | 2    | Ugyanaz, mint E4ds_v4 |
| Standard_E8 – 4ds_v4  | 4    | Ugyanaz, mint E8ds_v4 |
| Standard_E8 – 2ds_v4  | 2    | Ugyanaz, mint E8ds_v4 |
| Standard_E16 – 8ds_v4 | 8    | Ugyanaz, mint E16ds_v4|
| Standard_E16 – 4ds_v4 | 4    | Ugyanaz, mint E16ds_v4|
| Standard_E32 – 16ds_v4| 16   | Ugyanaz, mint E32ds_v4|
| Standard_E32 – 8ds_v4 | 8    | Ugyanaz, mint E32ds_v4|
| Standard_E64 – 32ds_v4| 32   | Ugyanaz, mint E64ds_v4|
| Standard_E64 – 16ds_v4| 16   | Ugyanaz, mint E64ds_v4|
| Standard_E4 – 2as_v4  | 2    | Ugyanaz, mint E4as_v4 |
| Standard_E8 – 4as_v4  | 4    | Ugyanaz, mint E8as_v4 |
| Standard_E8 – 2as_v4  | 2    | Ugyanaz, mint E8as_v4 |
| Standard_E16 – 8as_v4 | 8    | Ugyanaz, mint E16as_v4|
| Standard_E16 – 4as_v4 | 4    | Ugyanaz, mint E16as_v4|
| Standard_E32 – 16as_v4| 16   | Ugyanaz, mint E32as_v4|
| Standard_E32 – 8as_v4 | 8    | Ugyanaz, mint E32as_v4|
| Standard_E64 – 32as_v4| 32   | Ugyanaz, mint E64as_v4|
| Standard_E64 – 16as_v4| 16   | Ugyanaz, mint E64as_v4|
| Standard_E96 – 48as_v4| 48   | Ugyanaz, mint E96as_v4|
| Standard_E96 – 24as_v4| 24   | Ugyanaz, mint E96as_v4|
| Standard_GS4 – 8      | 8    | Ugyanaz, mint a GS4     |
| Standard_GS4 – 4      | 4    | Ugyanaz, mint a GS4     |
| Standard_GS5 – 16     | 16   | Ugyanaz, mint a GS5     |
| Standard_GS5-8      | 8    | Ugyanaz, mint a GS5     |
| Standard_DS11 – 1_v2  | 1    | Ugyanaz, mint DS11_v2 |
| Standard_DS12 – 2_v2  | 2    | Ugyanaz, mint DS12_v2 |
| Standard_DS12 – 1_v2  | 1    | Ugyanaz, mint DS12_v2 |
| Standard_DS13 – 4_v2  | 4    | Ugyanaz, mint DS13_v2 |
| Standard_DS13 – 2_v2  | 2    | Ugyanaz, mint DS13_v2 |
| Standard_DS14 – 8_v2  | 8    | Ugyanaz, mint DS14_v2 |
| Standard_DS14 – 4_v2  | 4    | Ugyanaz, mint DS14_v2 |
| Standard_M416 – 208s_v2 | 208    | Ugyanaz, mint M416s_v2|
| Standard_M416 – 208ms_v2 | 208    | Ugyanaz, mint M416ms_v2 |

## <a name="other-sizes"></a>Egyéb méretek
- [Számításoptimalizált](./sizes-compute.md)
- [Memóriaoptimalizált](./sizes-memory.md)
- [Tároptimalizált](./sizes-storage.md)
- [GPU](./sizes-gpu.md)
- [Nagy teljesítményű számítás](./sizes-hpc.md)

## <a name="next-steps"></a>Következő lépések
További információ arról, hogy az [Azure számítási egységei (ACU)](./acu.md) hogyan segíthetnek az Azure SKU-ban a számítási teljesítmény összehasonlításában.
