---
title: Az Azure Managed Disks teljesítményének módosítása a Azure Portal használatával
description: Megtudhatja, hogyan módosíthatja az új és a meglévő felügyelt lemezek teljesítményi szintjeit a Azure Portal használatával.
author: roygara
ms.service: virtual-machines
ms.topic: how-to
ms.date: 01/05/2021
ms.author: rogarana
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: 625fb1e3dd0b433da6b60f995aa6b380c23ec9ce
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "97901025"
---
# <a name="change-your-performance-tier-using-the-azure-portal"></a>A teljesítmény szintjeinek módosítása a Azure Portal használatával

[!INCLUDE [virtual-machines-disks-performance-tiers-intro](../../includes/virtual-machines-disks-performance-tiers-intro.md)]

## <a name="restrictions"></a>Korlátozások

[!INCLUDE [virtual-machines-disks-performance-tiers-restrictions](../../includes/virtual-machines-disks-performance-tiers-restrictions.md)]

## <a name="getting-started"></a>Első lépések

### <a name="new-disks"></a>Új lemezek

A következő lépések bemutatják, hogyan módosíthatja a lemez teljesítményének szintjét a lemez első létrehozásakor:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
1. Navigáljon arra a virtuális gépre, amelyhez új lemezt szeretne létrehozni.
1. Az új lemez kiválasztásakor először válassza ki a szükséges lemez méretét.
1. A méret kiválasztása után válasszon egy másik teljesítményszint, hogy megváltoztassa a teljesítményét.
1. A lemez létrehozásához kattintson **az OK gombra** .

:::image type="content" source="media/disks-performance-tiers-portal/new-disk-change-performance-tier.png" alt-text="Képernyőkép a lemez-létrehozási panelről, a lemez ki van emelve, és a teljesítmény szintje legördülő menüből kiemelve jelenik meg." lightbox="media/disks-performance-tiers-portal/performance-tier-settings.png":::


## <a name="existing-disks"></a>Meglévő lemezek

A következő lépések azt ismertetik, hogyan lehet módosítani egy meglévő lemez teljesítményének szintjét:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
1. Navigáljon a módosítani kívánt lemezt tartalmazó virtuális gépre.
1. Vagy szabadítson fel a virtuális gépet, vagy válassza le a lemezt.
1. Válassza ki a lemezt
1. Válassza a **méret + teljesítmény** lehetőséget.
1. A **teljesítményszint** legördülő menüben válasszon egy, a lemez aktuális teljesítményi szintjétől eltérő szintet.
1. Válassza ki az **Átméretezés** lehetőséget.

:::image type="content" source="media/disks-performance-tiers-portal/change-tier-existing-disk.png" alt-text="Képernyőkép a size + Performance (teljesítmény) panelről, a teljesítmény szintje ki van emelve." lightbox="media/disks-performance-tiers-portal/performance-tier-settings.png":::

## <a name="next-steps"></a>Következő lépések

Ha át kell méreteznie egy lemezt a magasabb teljesítményszint kihasználásához, tekintse meg a következő cikkeket:

- [Virtuális merevlemezek kibontása Linux rendszerű virtuális GÉPEN az Azure CLI-vel](linux/expand-disks.md)
- [Windows rendszerű virtuális géphez csatolt felügyelt lemez kibontása](windows/expand-os-disk.md)
