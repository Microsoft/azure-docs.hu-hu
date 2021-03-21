---
title: Azure Monitor naplókban található Logic apps-lekérdezések megtekintése és létrehozása
description: Lekérdezések megtekintése és létrehozása Azure Monitor naplókban Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: divswa, logicappspm
ms.topic: article
ms.date: 01/30/2020
ms.openlocfilehash: 5de795811923c965a1473f8d91e3fd654aeef266
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "100574553"
---
# <a name="view-and-create-queries-for-monitoring-and-tracking-in-azure-monitor-logs-for-azure-logic-apps"></a>A Azure Monitor-naplók figyelésére és nyomon követésére szolgáló lekérdezések megtekintése és létrehozása Azure Logic Apps

Megtekintheti azokat az alapul szolgáló lekérdezéseket, amelyek a [Azure monitor naplók](../azure-monitor/logs/log-query-overview.md) eredményeit eredményezik, és olyan lekérdezéseket hozhatnak létre, amelyek az adott feltételek alapján szűrik az eredményeket. Például megkeresheti az üzeneteket egy adott adatcsere-vezérlőelem száma alapján. A lekérdezések a [Kusto lekérdezési nyelvét](/azure/data-explorer/kusto/query/)használják, amelyet szerkeszthet, ha különböző eredményeket szeretne megtekinteni. További információ: [Azure monitor log lekérdezések](/azure/data-explorer/kusto/query/).

## <a name="prerequisites"></a>Előfeltételek

* Egy Log Analytics-munkaterület. Ha nem rendelkezik Log Analytics munkaterülettel, megtudhatja, [hogyan hozhat létre log Analytics-munkaterületet](../azure-monitor/logs/quick-create-workspace.md).

* Azure Monitor naplózással beállított logikai alkalmazás, amely egy Log Analytics munkaterületre küldi ezt az információt. Ismerje meg [, hogyan állíthat be Azure monitor naplókat a logikai alkalmazáshoz](../logic-apps/monitor-logic-apps.md).

* Ha integrációs fiókot használ, győződjön meg arról, hogy beállította a fiókot Azure Monitor naplózással, hogy elküldje ezt az információt egy Log Analytics munkaterületre. Ismerje meg, hogyan [állíthat be Azure monitor naplózást az integrációs fiókjához](../logic-apps/monitor-b2b-messages-log-analytics.md).

## <a name="view-queries-behind-results"></a>Az eredmények mögötti lekérdezések megtekintése

Az eredményeket a munkaterület összegzésében előállító lekérdezés megtekintéséhez vagy szerkesztéséhez kövesse az alábbi lépéseket:

1. Bármely találati oldalon, alul az **összes megtekintése** lehetőségre kattintva.

   ![Az összes eredmény megtekintése](./media/create-monitoring-tracking-queries/logic-app-see-all.png)

   Megnyílik a naplók lap, és megjeleníti az előző eredmények oldal mögötti lekérdezést.

   ![Naplók lap – Lekérdezés nézet](./media/create-monitoring-tracking-queries/view-query-behind-results.png)

1. A **naplók** lapon a következő lehetőségek közül választhat:

   * Ha a lekérdezés eredményét táblázatként szeretné megtekinteni, a lekérdezés-szerkesztőben válassza a **tábla** lehetőséget.

   * A lekérdezés módosításához frissítse a lekérdezési karakterláncot, és válassza a **Futtatás** lehetőséget a tábla eredményeinek megtekintéséhez.

## <a name="create-your-own-query"></a>Saját lekérdezés létrehozása

Az eredmények adott tulajdonságok vagy értékek alapján történő kereséséhez vagy szűréséhez hozzon létre egy saját lekérdezést egy üres lekérdezésből, vagy használjon egy meglévő lekérdezést. További információ: Ismerkedés [a Azure monitor-naplózási lekérdezésekkel](../azure-monitor/logs/get-started-queries.md).

1. A [Azure Portal](https://portal.azure.com)keresse meg és válassza ki log Analytics munkaterületét.

1. A munkaterület menü **általános** területén válassza a **naplók** lehetőséget.

1. Egy üres lekérdezésből vagy bármely elérhető meglévő lekérdezésből indulhat.

   * Annak megállapításához, hogy elérhetők-e meglévő lekérdezések, a lekérdezés eszköztáron válassza ki a **minták lekérdezési**  >  **előzményeit**, amely megjeleníti a korábbi lekérdezési futtatások lekérdezéseit, vagy válassza a **query Explorer** elemet, amely előre elkészített lekérdezéseket jelenít meg.

     A Logic Apps B2B megoldás például a következő előre elkészített lekérdezéseket biztosítja:

     ![Kezdés "Logic Apps B2B" megoldás előre elkészített lekérdezésekkel](./media/create-monitoring-tracking-queries/b2b-prebuilt-queries.png)

   * Ha üres lekérdezésből szeretne kezdeni, kezdje el beírni a lekérdezés [Kusto lekérdezési nyelvét](/azure/data-explorer/kusto/query/) .

     ![Indítás üres lekérdezéssel](./media/create-monitoring-tracking-queries/create-query-from-blank.png)

## <a name="next-steps"></a>Következő lépések

* [AS2-követési sémák](../logic-apps/logic-apps-track-integration-account-as2-tracking-schemas.md)
* [X12-követési sémák](../logic-apps/logic-apps-track-integration-account-x12-tracking-schema.md)
* [Egyéni követési sémák](../logic-apps/logic-apps-track-integration-account-custom-tracking-schema.md)