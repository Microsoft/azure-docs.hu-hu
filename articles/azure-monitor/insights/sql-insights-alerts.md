---
title: Riasztások létrehozása SQL-adatáttekintéssel (előzetes verzió)
description: Riasztások létrehozása az SQL-alapú adatáttekintéssel Azure Monitor
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/12/2021
ms.openlocfilehash: 5fe853ee0f7a113bfb8b0511744d9087f67927c4
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "104609902"
---
# <a name="create-alerts-with-sql-insights-preview"></a>Riasztások létrehozása SQL-adatáttekintéssel (előzetes verzió)
Az SQL-adatellenőrzések olyan riasztási szabályt tartalmazó sablonokat tartalmaznak, amelyekkel a gyakori SQL-problémákhoz [Azure Monitor riasztási szabályokat](../alert/../alerts/alerts-overview.md) hozhat létre. Az SQL-elemzésekben a riasztási szabályok a Azure Monitor naplók *InsightsMetrics* táblájában tárolt teljesítményadatok alapján naplózzák a riasztási szabályokat.  

> [!NOTE]
> Ha további SQL-információkkal kapcsolatos riasztási szabályt kér, küldjön visszajelzést az oldal alján található hivatkozásra kattintva, vagy használja a Azure Portalban található SQL-áttekintési visszajelzési hivatkozást.

## <a name="enable-alert-rules"></a>Riasztási szabályok engedélyezése 
A következő lépésekkel engedélyezheti a riasztásokat a Azure Portal Azure Monitor.A létrehozott riasztási szabályok a kiválasztott figyelési profilban figyelt összes SQL-erőforrásra érvényesek lesznek.  Riasztási szabály kiváltásakor a rendszer elindítja az adott SQL-példányt vagy-adatbázist.

> [!NOTE]
> Egyéni [naplózási riasztási szabályokat](../alerts/alerts-log.md) is létrehozhat, ha lekérdezéseket futtat a *InsightsMetrics* táblában lévő adatkészleteken, majd riasztási szabályként menti ezeket a lekérdezéseket. 

Válassza az **SQL (előzetes verzió)** lehetőséget a Azure Portal Azure monitor menüjének **elemzése szakaszában.** Kattintson a **riasztások** elemre.

:::image type="content" source="media/sql-insights-alerts/alerts-button.png" alt-text="Riasztások gomb":::

Ekkor megnyílik a **riasztások** panel a lap jobb oldalán. Alapértelmezés szerint a rendszer a korábban létrehozott riasztási szabályok alapján megjeleníti a kiválasztott figyelési profilban található SQL-erőforrások kilőtt riasztásait. Válassza ki a **riasztási sablonok** lehetőséget, amely megjeleníti a riasztási szabály létrehozásához használható elérhető sablonok listáját.

:::image type="content" source="media/sql-insights-alerts/alert-templates.png" alt-text="Riasztási sablonok":::

A **riasztási szabály létrehozása** lapon tekintse át a szabály alapértelmezett beállításait, és szükség szerint szerkessze azokat. Kiválaszthat egy [műveleti csoportot](../alerts/action-groups.md) is, amellyel értesítések és műveletek hozhatók létre a riasztási szabály indításakor. Kattintson a **riasztási szabály engedélyezése** lehetőségre a riasztási szabály létrehozásához, miután ellenőrizte az összes tulajdonságát.


:::image type="content" source="media/sql-insights-alerts/alert-rule.png" alt-text="Riasztási szabályok lap":::

A riasztási szabály azonnali üzembe helyezéséhez kattintson a **riasztási szabály telepítése** elemre. Kattintson a **sablon megtekintése** elemre, ha szeretné megtekinteni a szabály sablonját, mielőtt ténylegesen telepítené.

:::image type="content" source="media/sql-insights-alerts/alert-rule-deploy.png" alt-text="Riasztási szabály telepítése":::

Ha úgy dönt, hogy megtekinti a sablonokat, válassza a **telepítés** lehetőséget a sablon lapon a riasztási szabály létrehozásához.

:::image type="content" source="media/sql-insights-alerts/view-template-deploy.png" alt-text="Üzembe helyezés a sablon megtekintésekor":::


## <a name="next-steps"></a>Következő lépések

További információ a [Azure monitor riasztásokról](../alerts/alerts-overview.md).

