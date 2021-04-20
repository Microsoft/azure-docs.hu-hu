---
title: Riasztások létrehozása SQL-elemzésekkel (előzetes verzió)
description: Riasztások létrehozása SQL-elemzésekkel a Azure Monitor
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/12/2021
ms.openlocfilehash: bb42f74f6ac8487a93479bdf980c66ef87e8e742
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/19/2021
ms.locfileid: "107726861"
---
# <a name="create-alerts-with-sql-insights-preview"></a>Riasztások létrehozása SQL-elemzésekkel (előzetes verzió)
Az SQL-elemzések olyan riasztásiszabály-sablonokat tartalmaznak, amelyek segítségével riasztási [szabályokat hozhat létre a Azure Monitor](../alert/../alerts/alerts-overview.md) SQL-problémákhoz. Az SQL-elemzések riasztási szabályai naplóriasztások, amelyek a naplónaplókBan található *InsightsMetrics* táblában tárolt teljesítményadatokon Azure Monitor alapulnak.  

> [!NOTE]
> Ha Resource Manager-sablonnal hoz létre riasztást az SQL-elemzésekhez, tekintse meg az [SQL-Resource Manager sablonmintákat.](resource-manager-sql-insights.md#create-an-alert-rule-for-sql-insights)


> [!NOTE]
> Ha további SQL Insights riasztásiszabály-sablonokkal kapcsolatos kérései vannak, küldjön visszajelzést a lap alján található hivatkozással vagy az SQL-elemzések visszajelzési hivatkozásával a Azure Portal.

## <a name="enable-alert-rules"></a>Riasztási szabályok engedélyezése 
Az alábbi lépésekkel engedélyezheti a riasztásokat a Azure Monitor a Azure Portal.A létrehozott riasztási szabályok hatóköre a kiválasztott figyelési profilban figyelt összes SQL-erőforrásra kiterjed.  Ha egy riasztási szabály aktiválódik, az adott SQL-példányon vagy -adatbázison aktiválódik.

> [!NOTE]
> Egyéni naplóriasztásokat is [létrehozhat,](../alerts/alerts-log.md) ha lekérdezéseket futtat az *InsightsMetrics* táblában található adatkészleten, majd riasztási szabályként menti ezeket a lekérdezéseket. 

Válassza **az SQL (előzetes verzió)** lehetőséget a Azure Monitor menüjének Elemzések Azure Portal.  Kattintson **a Riasztások elemre.**

:::image type="content" source="media/sql-insights-alerts/alerts-button.png" alt-text="Riasztások gomb":::

Megnyílik **a** Riasztások panel az oldal jobb oldalán. Alapértelmezés szerint a már létrehozott riasztási szabályok alapján a kiválasztott figyelési profilban megjelennek az SQL-erőforrásokra vonatkozó riasztások. Válassza **a Riasztási sablonok** lehetőséget, amely megjeleníti a riasztási szabály létrehozásához használható elérhető sablonok listáját.

:::image type="content" source="media/sql-insights-alerts/alert-templates.png" alt-text="Riasztási sablonok":::

A **Riasztási szabály létrehozása lapon** tekintse át a szabály alapértelmezett beállításait, és szükség szerint szerkessze őket. Kiválaszthat egy [műveletcsoportot is,](../alerts/action-groups.md) hogy értesítéseket és műveleteket hozzon létre a riasztási szabály aktiválódása után. Miután **ellenőrizte az** összes tulajdonságát, kattintson a Riasztási szabály engedélyezése elemre a riasztási szabály létrehozásához.


:::image type="content" source="media/sql-insights-alerts/alert-rule.png" alt-text="Riasztási szabályok lap":::

A riasztási szabály azonnali üzembe helyezéséhez kattintson a **Riasztási szabály telepítése elemre.** Kattintson **a Sablon megtekintése** elemre, ha a ténylegesen üzembe helyezés előtt meg szeretné tekinteni a szabálysablont.

:::image type="content" source="media/sql-insights-alerts/alert-rule-deploy.png" alt-text="Riasztási szabály üzembe helyezése":::

Ha úgy dönt, hogy  megtekinti a sablonokat, a riasztási szabály létrehozásához válassza a Sablon lap Üzembe helyezés elemét.

:::image type="content" source="media/sql-insights-alerts/view-template-deploy.png" alt-text="Üzembe helyezés nézetsablonból":::


## <a name="next-steps"></a>Következő lépések

További információ a [riasztásokkal kapcsolatban a Azure Monitor.](../alerts/alerts-overview.md)

