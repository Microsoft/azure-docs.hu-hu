---
title: Av2-sorozat
description: A Av2 sorozatú virtuális gépek specifikációi.
author: migerdes
ms.service: virtual-machines
ms.subservice: vm-sizes-general
ms.topic: conceptual
ms.date: 02/03/2020
ms.author: jushiman
ms.openlocfilehash: 680ffe7964104f157debc64e44ac2f004ddac86d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102565512"
---
# <a name="av2-series"></a>Av2-sorozat

A Av2 sorozatú virtuális gépek számos különböző hardvereszközön és processzoron is üzembe helyezhetők. A Av2 sorozatú virtuális gépekhez a CPU-teljesítmény és a memória-konfigurációk a legmegfelelőbbek a belépési szintű munkaterhelésekhez, például a fejlesztéshez A méret szabályozása úgy történik, hogy konzisztens processzor-teljesítményt nyújtson a futó példány számára, függetlenül a telepített hardvertől. Az adott méretet futtató fizikai hardver meghatározásához kérdezze le a virtuális hardvert a virtuális gépen belülről. Ilyenek például a fejlesztési és tesztelési kiszolgálók, az alacsony forgalmú webkiszolgálók, a kis-és közepes adatbázisok, a bizonyítási fogalmak és a kódok tárházai.

[ACU](acu.md): 100<br>
[Premium Storage](premium-storage-performance.md): nem támogatott <br>
[Premium Storage gyorsítótárazás](premium-storage-performance.md): nem támogatott <br>
[Élő áttelepítés](maintenance-and-updates.md): támogatott <br>
[Memória megőrzésének frissítései](maintenance-and-updates.md): támogatott <br>
[VM-generáció támogatása](generation-2.md): 1. generáció <br>
[Gyorsított hálózatkezelés](../virtual-network/create-vm-accelerated-networking-cli.md): nem támogatott<br>
[Ideiglenes operációsrendszer-lemezek](ephemeral-os-disks.md): nem támogatott <br>
<br>

| Méret | virtuális mag | Memória: GiB | Ideiglenes tárterület (SSD) GiB | Maximális Temp Storage átviteli sebessége: IOPS/olvasási MBps/írási MBps | Adatlemezek maximális száma/átviteli sebesség: IOPS | Hálózati adapterek maximális száma | Várt hálózati sávszélesség (Mbps)
|---|---|---|---|---|---|---|---|
| Standard_A1_v2  | 1 | 2  | 10 | 1000/20/10  | 2/2x500   | 2 | 250  |
| Standard_A2_v2  | 2 | 4  | 20 | 2000/40/20  | 4/4x500   | 2 | 500  |
| Standard_A4_v2  | 4 | 8  | 40 | 4000/80/40  | 8/8x500   | 4 | 1000 |
| Standard_A8_v2  | 8 | 16 | 80 | 8000/160/80 | 16/16x500 | 8 | 2000 |
| Standard_A2m_v2 | 2 | 16 | 20 | 2000/40/20  | 4/4x500   | 2 | 500  |
| Standard_A4m_v2 | 4 | 32 | 40 | 4000/80/40  | 8/8x500   | 4 | 1000 |
| Standard_A8m_v2 | 8 | 64 | 80 | 8000/160/80 | 16/16x500 | 8 | 2000 |

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="other-sizes-and-information"></a>Egyéb méretek és információk

- [Általános célú](sizes-general.md)
- [Memóriaoptimalizált](sizes-memory.md)
- [Tároptimalizált](sizes-storage.md)
- [GPU-optimalizált](sizes-gpu.md)
- [Nagy teljesítményű számítás](sizes-hpc.md)
- [Előző generációk](sizes-previous-gen.md)

Árképzési kalkulátor: [árképzési kalkulátor](https://azure.microsoft.com/pricing/calculator/)

További információ a lemezek típusairól: [lemezek típusai](./disks-types.md#ultra-disk)

## <a name="next-steps"></a>Következő lépések

További információ arról, hogy az [Azure számítási egységei (ACU)](acu.md) hogyan segíthetnek az Azure SKU-ban a számítási teljesítmény összehasonlításában.
