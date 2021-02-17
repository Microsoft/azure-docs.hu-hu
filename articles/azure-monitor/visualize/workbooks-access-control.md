---
title: Azure Monitor munkafüzetek hozzáférés-vezérlése
description: A szerepköralapú hozzáférés-vezérléssel rendelkező, előre elkészített és egyéni paraméteres munkafüzetek összetett jelentéskészítésének egyszerűbbé tétele
services: azure-monitor
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/23/2019
ms.openlocfilehash: dd01b7e57441d4e5d763a07647ee27f1eef53ea9
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/17/2021
ms.locfileid: "100613798"
---
# <a name="access-control"></a>Hozzáférés-vezérlés

A munkafüzetek hozzáférés-vezérlése két dologra utal:

* A munkafüzetben tárolt adatolvasáshoz szükséges hozzáférés. Ezt a hozzáférést a munkafüzetben használt erőforrások szabványos [Azure-szerepkörei](../../role-based-access-control/overview.md) vezérlik. A munkafüzetek nem határozzák meg és nem konfigurálhatják a hozzáférést ezekhez az erőforrásokhoz. A felhasználók általában a [figyelés olvasó](../../role-based-access-control/built-in-roles.md#monitoring-reader) szerepkörrel érhetik el ezeket az erőforrásokat.

* A munkafüzetek mentéséhez szükséges hozzáférés

    - A privát `("My")` munkafüzetek mentéséhez nincs szükség további jogosultságokra. Minden felhasználó menthet privát munkafüzeteket, és csak azok láthatják ezeket a munkafüzeteket.
    - A megosztott munkafüzetek mentésekor írási jogosultságok szükségesek egy erőforráscsoporthoz a munkafüzet mentéséhez. Ezeket a jogosultságokat általában a [figyelési közreműködő](../../role-based-access-control/built-in-roles.md#monitoring-contributor) szerepkör adja meg, de a *munkafüzetek közreműködői* szerepkörén keresztül is beállítható.
    
## <a name="standard-roles-with-workbook-related-privileges"></a>A munkafüzettel kapcsolatos jogosultságokkal rendelkező standard szerepkörök

A [monitorozási olvasó](../../role-based-access-control/built-in-roles.md#monitoring-reader) olyan szabványos/Read jogosultságokat tartalmaz, amelyeket a figyelési eszközök (például a munkafüzetek) használnak az erőforrások adatainak olvasásához.

A [figyelő közreműködői](../../role-based-access-control/built-in-roles.md#monitoring-contributor) `/write` a különböző figyelési eszközök által az elemek mentéséhez használt általános jogosultságokat tartalmazza (beleértve `workbooks/write` a megosztott munkafüzetek mentésére vonatkozó jogosultságot).
A "munkafüzetek közreműködői" felveszi a "munkafüzetek/írás" jogosultságokat egy objektumba a megosztott munkafüzetek mentéséhez.
Nincs szükség különleges jogosultságra ahhoz, hogy a felhasználók olyan privát munkafüzeteket mentsenek, amelyeket csak láthatnak.

Egyéni szerepkörök esetén:

Hozzáadás `microsoft.insights/workbooks/write` a megosztott munkafüzetek mentéséhez. További részletekért tekintse meg a [munkafüzet közreműködői](../../role-based-access-control/built-in-roles.md#monitoring-contributor) szerepkört.

## <a name="next-steps"></a>Következő lépések

* [Ismerkedjen](../platform/workbooks-overview.md#visualizations) meg a munkafüzetek számos gazdag vizualizációs lehetőségével.