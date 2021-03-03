---
title: Az Azure Managed Disks teljesítményi szintjei
description: A felügyelt lemezek teljesítményi szintjeinek megismerése.
author: roygara
ms.service: virtual-machines
ms.topic: how-to
ms.date: 03/02/2021
ms.author: rogarana
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: af1509073248b46575881beef7b9800107e7fed7
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/02/2021
ms.locfileid: "101677307"
---
# <a name="performance-tiers-for-managed-disks"></a>A felügyelt lemezek teljesítményi szintjei

Az Azure Managed Disk teljesítményének beállítása a lemez létrehozásakor a teljesítményszint formájában történik. A teljesítmény szintje határozza meg a felügyelt lemez IOPS és átviteli sebességét. A lemez kiépített méretének beállításakor a rendszer automatikusan kijelöl egy teljesítményszint-szintet. A teljesítményszint a lemez méretének módosítása nélkül módosítható az üzembe helyezéskor vagy azt követően is.

A teljesítmény szintjének módosítása lehetővé teszi, hogy előkészítse a magasabb igényeket, és ne használja a lemez kitörésének képességét. A teljesítmény szintjéhez képest költséghatékonyabb lehet, ha nem a kitörésre támaszkodik, attól függően, hogy mennyi ideig van szükség a további teljesítményre. Ez ideális olyan eseményekhez, amelyek átmenetileg igénylik a magasabb szintű teljesítményt, például az üdülési vásárlást, a teljesítmény tesztelését vagy egy képzési környezet futtatását. Ezeknek az eseményeknek a kezeléséhez használhat magasabb szintű teljesítményt, ha szüksége van rá. Ezután visszatérhet az eredeti szintjéhez, ha már nincs szüksége a további teljesítményre.

## <a name="how-it-works"></a>Működés

Amikor először telepít vagy kiépít egy lemezt, a lemez alapteljesítményi szintje a kiosztott lemez mérete alapján van beállítva. A magasabb igények kielégítése érdekében az eredeti alapkonfigurációnál nagyobb teljesítményi szintet is használhat. Ha már nincs szüksége erre a teljesítményre, visszatérhet a kezdeti teljesítmény szintjéhez.

A számlázási változások a teljesítmény szintjénél változnak. Ha például kiépít egy P10-lemezt (128 GiB), az alapteljesítmény szintje P10 (500 IOPS és 100 MBps) van beállítva. A díjat a P10 díjszabása alapján számítjuk fel. A szintet úgy frissítheti, hogy az megfeleljen a P50 teljesítményének (7 500 IOPS és 250 MBps) a lemez méretének növelése nélkül. A frissítés ideje alatt a P50 díjszabása alapján számítjuk fel a díjat. Ha már nincs szüksége a magasabb teljesítményre, visszatérhet a P10 szintjéhez. A lemez újbóli számlázása a P10 arányban történik.

| Lemezméret | Alapteljesítmény szintje | Frissíthető a következőre |
|----------------|-----|-------------------------------------|
| 4. GiB | P1 | P2, P3, P4, P6, P10, P15, P20, P30, P40, P50 |
| 8 GiB | P2 | P3, P4, P6, P10, P15, P20, P30, P40, P50 |
| 16 GiB | P3 | P4, P6, P10, P15, P20, P30, P40, P50 | 
| 32 GiB | P4 | P6, P10, P15, P20, P30, P40, P50 |
| 64 GiB | P6 | P10, P15, P20, P30, P40, P50 |
| 128 GiB | P10 | P15, P20, P30, P40, P50 |
| 256 GiB | P15 | P20, P30, P40, P50 |
| 512 GiB | P20 | P30, P40, P50 |
| 1 TiB | P30 | P40, P50 |
| 2 TiB | P40 | P50 |
| 4 TiB | P50 | Nincs |
| 8 TiB | P60 |  P70, P80 |
| 16 TiB | P70 | P80 |
| 32 tebibájt | P80 | Nincs |

Számlázási információk: a [felügyelt lemez díjszabása](https://azure.microsoft.com/pricing/details/managed-disks/).

## <a name="restrictions"></a>Korlátozások

[!INCLUDE [virtual-machines-disks-performance-tiers-restrictions](../../includes/virtual-machines-disks-performance-tiers-restrictions.md)]

## <a name="next-steps"></a>Következő lépések

A teljesítményszint módosításával kapcsolatos további információkért lásd: [portál](disks-performance-tiers-portal.md) vagy [PowerShell/CLI-](disks-performance-tiers.md) cikkek.

