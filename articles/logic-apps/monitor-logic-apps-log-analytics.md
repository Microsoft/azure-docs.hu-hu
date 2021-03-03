---
title: Logikai alkalmazások figyelése Azure Monitor naplók használatával
description: Logikai alkalmazások hibakeresése Azure Monitor naplók beállításával és diagnosztikai adatok összegyűjtésével Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: divswa, logicappspm
ms.topic: article
ms.date: 09/24/2020
ms.openlocfilehash: d1c095be1d0525cc843c04a3b83e14bb4211b175
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2021
ms.locfileid: "101721259"
---
# <a name="set-up-azure-monitor-logs-and-collect-diagnostics-data-for-azure-logic-apps"></a>Azure Monitor naplók beállítása és diagnosztikai adatok összegyűjtése Azure Logic Apps

A logikai alkalmazásokkal kapcsolatos részletes hibakeresési információk a futtatókörnyezetben való futtatásához beállíthatja és használhatja [Azure monitor naplókat](../azure-monitor/logs/data-platform-logs.md) a futásidejű adatok és események adatainak rögzítésére és tárolására, például az események aktiválására, az események futtatására és a műveleti eseményekre egy [log Analytics munkaterületen](../azure-monitor/essentials/resource-logs.md#send-to-log-analytics-workspace). [Azure monitor](../azure-monitor/overview.md) segít a felhő és a helyszíni környezetek monitorozásában, így könnyebben megtarthatja a rendelkezésre állást és a teljesítményt. Azure Monitor naplók használatával olyan [napló-lekérdezéseket](../azure-monitor/logs/log-query-overview.md) hozhat létre, amelyek segítenek az információk gyűjtésében és áttekintésében. [A diagnosztikai adatait más Azure-szolgáltatásokkal is használhatja](#extend-data), mint például az Azure Storage és az Azure Event Hubs.

A logikai alkalmazás naplózásának beállításához [engedélyezheti a log Analytics a logikai alkalmazás létrehozásakor](#logging-for-new-logic-apps), vagy a meglévő logikai alkalmazások log Analytics munkaterületén is [telepítheti a Logic apps felügyeleti megoldást](#install-management-solution) . Ez a megoldás összesített információt biztosít a logikai alkalmazás futtatásához, és konkrét részleteket tartalmaz, például az állapotot, a végrehajtási időt, az újraküldési állapotot és a korrelációs azonosítókat. Ezután a naplózás engedélyezéséhez és a lekérdezések létrehozásához hozzon létre [Azure monitor naplókat](#set-up-resource-logs).

Ez a cikk bemutatja, hogyan engedélyezheti a Log Analyticst az új Logic apps és a meglévő Logic Apps alkalmazásokban, hogyan telepítheti és állíthatja be a Logic Apps felügyeleti megoldást, valamint hogyan állíthatja be és hozhat létre lekérdezéseket Azure Monitor naplókhoz.

## <a name="prerequisites"></a>Előfeltételek

A Kezdés előtt [log Analytics munkaterületre](../azure-monitor/essentials/resource-logs.md#send-to-log-analytics-workspace)van szükség. Ha nem rendelkezik munkaterülettel, ismerkedjen meg [log Analytics munkaterület létrehozásával](../azure-monitor/logs/quick-create-workspace.md).

<a name="logging-for-new-logic-apps"></a>

## <a name="enable-log-analytics-for-new-logic-apps"></a>Log Analytics engedélyezése új Logic apps-alkalmazásokhoz

A logikai alkalmazás létrehozásakor bekapcsolhatja a Log Analytics.

1. A [Azure Portal](https://portal.azure.com)azon a panelen, ahol a logikai alkalmazás létrehozásához szükséges adatokat adja meg, kövesse az alábbi lépéseket:

   1. A **log Analytics** alatt válassza **a** be lehetőséget.

   1. A **log Analytics munkaterület** listából válassza ki azt a munkaterületet, ahová el szeretné küldeni a logikai alkalmazás futtatását.

      ![Logikai alkalmazás adatainak megadása](./media/monitor-logic-apps-log-analytics/create-logic-app-details.png)

      Miután befejezte ezt a lépést, az Azure létrehozza a logikai alkalmazást, amely most már társítva van a Log Analytics munkaterülethez. Emellett ez a lépés automatikusan telepíti a Logic Apps felügyeleti megoldást a munkaterületen.

1. Ha elkészült, válassza a **Létrehozás** lehetőséget.

1. A logikai alkalmazás futtatása után a logikai alkalmazás futtatásának megtekintéséhez [folytassa ezeket a lépéseket](#view-logic-app-runs).

<a name="install-management-solution"></a>

## <a name="install-logic-apps-management-solution"></a>Logic Apps felügyeleti megoldás telepítése

Ha bekapcsolta Log Analytics a logikai alkalmazás létrehozásakor, hagyja ki ezt a lépést. Már telepítve van a Logic Apps felügyeleti megoldás a Log Analytics munkaterületen.

1. A [Azure Portal](https://portal.azure.com)keresési mezőjébe írja be a kifejezést `log analytics workspaces` , majd válassza **log Analytics munkaterületek** lehetőséget.

   !["Log Analytics munkaterületek" kiválasztása](./media/monitor-logic-apps-log-analytics/find-select-log-analytics-workspaces.png)

1. A **log Analytics munkaterületek** területen válassza ki a munkaterületet.

   ![Log Analytics munkaterület kiválasztása](./media/monitor-logic-apps-log-analytics/select-log-analytics-workspace.png)

1. Az **Áttekintés** ablaktáblán, az **első lépések a log Analytics** a  >  **figyelési megoldások konfigurálása** területen válassza a **megoldások megtekintése** lehetőséget.

   ![Az Áttekintés panelen válassza a "megoldások megtekintése" lehetőséget.](./media/monitor-logic-apps-log-analytics/log-analytics-workspace.png)

1. Az **Áttekintés** területen válassza a **Hozzáadás** lehetőséget.

   ![Az Áttekintés panelen új megoldás hozzáadása](./media/monitor-logic-apps-log-analytics/add-logic-apps-management-solution.png)

1. A **piactér** megnyitása után a keresőmezőbe írja be a kifejezést `logic apps management` , majd válassza a **Logic apps felügyelet** lehetőséget.

   ![A piactéren válassza a "Logic Apps kezelés" lehetőséget.](./media/monitor-logic-apps-log-analytics/select-logic-apps-management.png)

1. A megoldás leírása panelen válassza a **Létrehozás** lehetőséget.

   ![Válassza a létrehozás lehetőséget a "Logic Apps Management" megoldás hozzáadásához](./media/monitor-logic-apps-log-analytics/create-logic-apps-management-solution.png)

1. Tekintse át és erősítse meg a Log Analytics munkaterületet, ahol telepíteni szeretné a megoldást, majd válassza a **Létrehozás** újra lehetőséget.

   ![A "létrehozás" lehetőség kiválasztása a "Logic Apps Management" számára](./media/monitor-logic-apps-log-analytics/confirm-log-analytics-workspace.png)

   Miután az Azure üzembe helyezte a megoldást az Log Analytics munkaterületet tartalmazó Azure-erőforráscsoport számára, a megoldás megjelenik a munkaterület összefoglalás paneljén.

   ![Munkaterület összegzése panel](./media/monitor-logic-apps-log-analytics/workspace-summary-pane-logic-apps-management.png)

<a name="set-up-resource-logs"></a>

## <a name="set-up-azure-monitor-logs"></a>Azure Monitor naplók beállítása

Ha [Azure monitor naplókban](../azure-monitor/logs/data-platform-logs.md)lévő futtatókörnyezeti eseményekkel és adatokkal kapcsolatos információkat tárol, létrehozhat olyan [napló-lekérdezéseket](../azure-monitor/logs/log-query-overview.md) , amelyek segítségével megkeresheti és áttekintheti az információkat.

> [!NOTE]
> Miután engedélyezte a diagnosztikai beállításokat, előfordulhat, hogy a diagnosztikai adatforgalom legfeljebb 30 percet vesz igénybe a megadott célhelyen, például az Log Analytics, az Event hub vagy a Storage-fiókban. Ez a késleltetés azt jelenti, hogy az adott időszak diagnosztikai adatai nem léteznek a felülvizsgálathoz. Előfordulhat, hogy a befejezett események és a [követett tulajdonságok](#extend-data) nem jelennek meg a log Analytics munkaterületen 10-15 percen belül.

1. A [Azure Portal](https://portal.azure.com)keresse meg és válassza ki a logikai alkalmazást.

1. A logikai alkalmazás menüjének **figyelés** területén válassza a **diagnosztikai beállítások**  >  **Hozzáadás diagnosztikai beállítás** elemet.

   ![A "Figyelés" területen válassza a "diagnosztikai beállítások" > "diagnosztikai beállítás hozzáadása" lehetőséget.](./media/monitor-logic-apps-log-analytics/logic-app-diagnostics.png)

1. A beállítás létrehozásához kövesse az alábbi lépéseket:

   1. Adja meg a beállítás nevét.

   1. Válassza **a küldés log Analytics** lehetőséget.

   1. Az **előfizetés** mezőben válassza ki a log Analytics munkaterülethez társított Azure-előfizetést.

   1. **Log Analytics munkaterületen** válassza ki a használni kívánt munkaterületet.

   1. A **napló** területen válassza ki a **WorkflowRuntime** kategóriát, amely megadja a rögzíteni kívánt esemény kategóriáját.

   1. Az összes metrika kiválasztásához a **metrika** területen válassza a **AllMetrics** lehetőséget.

   1. Amikor elkészült, válassza a **Mentés** lehetőséget.

   Például:

   ![Válassza ki Log Analytics munkaterületet és az adataikat a naplózáshoz](./media/monitor-logic-apps-log-analytics/send-diagnostics-data-log-analytics-workspace.png)

<a name="view-logic-app-runs"></a>

## <a name="view-logic-app-runs-status"></a>Logikai alkalmazás futtatási állapotának megtekintése

A logikai alkalmazás futtatása után megtekintheti a futtatások adatait a Log Analytics munkaterületen.

1. A [Azure Portal](https://portal.azure.com)keresse meg és nyissa meg log Analytics munkaterületét.

1. A munkaterület menüjében válassza a **munkaterület összefoglalása**  >  **Logic apps felügyelet** lehetőséget.

   ![Logikai alkalmazás futtatásának állapota és darabszáma](./media/monitor-logic-apps-log-analytics/logic-app-runs-summary.png)

   > [!NOTE]
   > Ha a Logic Apps-felügyeleti csempe nem jeleníti meg azonnal az eredményeket egy Futtatás után, próbálkozzon a **frissítés** lehetőséggel, vagy várjon egy rövid ideig, mielőtt újra próbálkozik.

   Itt a logikai alkalmazás futtatását név vagy végrehajtási állapot szerint csoportosítjuk. Ezen a lapon a logikai alkalmazás futtatásához kapcsolódó műveletek és eseményindítók hibáinak részleteit is láthatja.

   ![A logikai alkalmazás futtatási állapotának összegzése](./media/monitor-logic-apps-log-analytics/logic-app-runs-summary-details.png)

1. Egy adott logikai alkalmazás vagy állapot összes futtatásának megtekintéséhez válassza ki a logikai alkalmazás vagy az állapot sorát.

   Az alábbi példa egy adott logikai alkalmazás összes futtatását mutatja be:

   ![Logikai alkalmazás futtatásának és állapotának megtekintése](./media/monitor-logic-apps-log-analytics/logic-app-run-details.png)

   Azokat a műveleteket, amelyeken a [követett tulajdonságokat beállította](#extend-data), megtekintheti ezeket a tulajdonságokat a **nyomon követett tulajdonságok** oszlopban a **nézet** lehetőség kiválasztásával. A követett tulajdonságok kereséséhez használja az oszlop szűrőt.

   ![Logikai alkalmazás nyomon követett tulajdonságainak megtekintése](./media/monitor-logic-apps-log-analytics/logic-app-tracked-properties.png)

1. Az eredmények szűréséhez ügyféloldali és Kiszolgálóoldali szűrés is végezhető.

   * **Ügyféloldali szűrő**: minden oszlopnál válassza ki a kívánt szűrőket, például:

     ![Példa oszlop szűrői](./media/monitor-logic-apps-log-analytics/filters.png)

   * **Kiszolgálóoldali szűrő**: adott időablak kiválasztásához vagy a megjelenő futtatások számának korlátozásához használja az oldal tetején található hatókör-vezérlőelemet. Alapértelmezés szerint egyszerre csak 1 000 rekord jelenik meg.

     ![Az időablak módosítása](./media/monitor-logic-apps-log-analytics/change-interval.png)

1. Egy adott Futtatás összes műveletének és részletes adatainak megtekintéséhez válassza ki a logikai alkalmazás futtatásának sorát.

   Az alábbi példa egy adott logikai alkalmazás futtatásának összes műveletét és eseményindítóját mutatja be:

   ![Logikai alkalmazás futtatási műveleteinek megtekintése](./media/monitor-logic-apps-log-analytics/logic-app-action-details.png)

<!-------------
   * **Resubmit**: You can resubmit one or more logic apps runs that failed, succeeded, or are still running. Select the check boxes for the runs that you want to resubmit, and then select **Resubmit**.

     ![Resubmit logic app runs](./media/monitor-logic-apps-log-analytics/logic-app-resubmit.png)
--------------->

<a name="extend-data"></a>

## <a name="send-diagnostic-data-to-azure-storage-and-azure-event-hubs"></a>Diagnosztikai adatgyűjtés küldése az Azure Storage-ba és az Azure-Event Hubs

A Azure Monitor naplókkal együtt kiterjesztheti a logikai alkalmazás diagnosztikai adatait más Azure-szolgáltatásokkal, például a következő módon:

* [Azure-beli erőforrás-naplók archiválása a Storage-fiókba](../azure-monitor/essentials/resource-logs.md#send-to-azure-storage)
* [Azure platform-naplók továbbítása az Azure Event Hubsba](../azure-monitor/essentials/resource-logs.md#send-to-azure-event-hubs)

Ezt követően valós idejű monitorozást érhet el a telemetria és az Analytics használatával más szolgáltatásokból, például a [Azure stream Analytics](../stream-analytics/stream-analytics-introduction.md) és az [Power bi](../azure-monitor/visualize/powerbi.md). Például:

* [Stream-adatok Event Hubsról Stream Analytics](../stream-analytics/stream-analytics-define-inputs.md)
* [Adatfolyamok elemzése Stream Analytics és valós idejű elemzési irányítópult létrehozása Power BI](../stream-analytics/stream-analytics-power-bi-dashboard.md)

Annak a helynek a alapján, ahol diagnosztikai adatküldést szeretne végezni, először [hozzon létre egy Azure Storage-fiókot](../storage/common/storage-account-create.md) , vagy [hozzon létre egy Azure Event hub](../event-hubs/event-hubs-create.md)-t. Ezután kiválaszthatja azokat a célhelyeket, amelyeken el szeretné küldeni ezeket az adatfájlokat. A megőrzési időszakok csak akkor érvényesek, ha Storage-fiókot használ.

![Adatküldés az Azure Storage-fiókba vagy az Event hub-ba](./media/monitor-logic-apps-log-analytics/diagnostics-storage-event-hub-log-analytics.png)

<a name="diagnostic-event-properties"></a>

## <a name="azure-monitor-diagnostics-events"></a>Diagnosztikai események Azure Monitor

Minden diagnosztikai esemény a logikai alkalmazással és az eseménysel kapcsolatos részleteket tartalmaz, például az állapotot, a kezdési időt, a befejezési időpontot és így tovább. A figyelés, a nyomon követés és a naplózás programozott beállításához ezeket REST API az információkat a [Azure Logic Appshoz](/rest/api/logic) és a [Azure monitor Rest APIához](../azure-monitor/essentials/metrics-supported.md#microsoftlogicworkflows)használhatja. Használhatja a `clientTrackingId` és a tulajdonságokat is `trackedProperties` , amelyek megjelennek a 

* `clientTrackingId`: Ha nincs megadva, az Azure automatikusan létrehozza ezt az azonosítót, és korrelálja az eseményeket egy logikai alkalmazás futása során, beleértve a logikai alkalmazásból meghívott beágyazott munkafolyamatokat is. Ezt az azonosítót manuálisan is megadhatja egy triggerben, ha egy fejlécet ad meg a `x-ms-client-tracking-id` trigger-kérelemben szereplő egyéni azonosító értékkel. Használhat kérelem-triggert, HTTP-triggert vagy webhook-triggert.

* `trackedProperties`: A diagnosztikai adatok bemenetének és kimenetének nyomon követéséhez hozzáadhat egy `trackedProperties` szakaszt egy művelethez a Logic app Designer használatával vagy közvetlenül a logikai alkalmazás JSON-definíciójában. A nyomon követett tulajdonságok csak egyetlen művelet bemeneteit és kimeneteit követhetik nyomon, de az `correlation` események tulajdonságaival korrelálhat a futtatási műveletek között. Egynél több tulajdonság nyomon követéséhez adja hozzá a `trackedProperties` szakaszt és a művelet definíciójában használni kívánt tulajdonságokat.

  Az alábbi példa bemutatja, hogyan mutatja be az **inicializálási változó** műveleti definíciója a művelet bemenetének nyomon követését, ha a bemenet tömb, nem rekord.

  ``` json
  {
     "Initialize_variable": {
        "type": "InitializeVariable",
        "inputs": {
           "variables": [
              {
                 "name": "ConnectorName", 
                 "type": "String", 
                 "value": "SFTP-SSH" 
              }
           ]
        },
        "runAfter": {},
        "trackedProperties": { 
           "myTrackedPropertyName": "@action().inputs.variables[0].value"
        }
     }
  }
  ```

  Ez a példa több követett tulajdonságot mutat be:

  ``` json
  "HTTP": {
     "type": "Http",
     "inputs": {
        "body": "@triggerBody()",
        "headers": {
           "Content-Type": "application/json"
        },
        "method": "POST",
        "uri": "http://store.fabrikam.com",
     },
     "runAfter": {},
     "trackedProperties": {
        "myActionHTTPStatusCode": "@action()['outputs']['statusCode']",
        "myActionHTTPValue": "@action()['outputs']['body']['<content>']",
        "transactionId": "@action()['inputs']['body']['<content>']"
     }
  }
  ```

Ez a példa azt szemlélteti `ActionCompleted` , hogyan tartalmazza az esemény a `clientTrackingId` és `trackedProperties` attribútumokat:

```json
{
   "time": "2016-07-09T17:09:54.4773148Z",
   "workflowId": "/subscriptions/XXXXXXXXXXXXXXX/resourceGroups/MyResourceGroup/providers/Microsoft.Logic/workflows/MyLogicApp",
   "resourceId": "/subscriptions/<subscription-ID>/resourceGroups/MyResourceGroup/providers/Microsoft.Logic/workflows/MyLogicApp/runs/<run-ID>/actions/Http",
   "category": "WorkflowRuntime",
   "level": "Information",
   "operationName": "Microsoft.Logic/workflows/workflowActionCompleted",
   "properties": {
      "$schema": "2016-06-01",
      "startTime": "2016-07-09T17:09:53.4336305Z",
      "endTime": "2016-07-09T17:09:53.5430281Z",
      "status": "Succeeded",
      "code": "OK",
      "resource": {
         "subscriptionId": "<subscription-ID>",
         "resourceGroupName": "MyResourceGroup",
         "workflowId": "<logic-app-workflow-ID>",
         "workflowName": "MyLogicApp",
         "runId": "08587361146922712057",
         "location": "westus",
         "actionName": "Http"
      },
      "correlation": {
         "actionTrackingId": "e1931543-906d-4d1d-baed-dee72ddf1047",
         "clientTrackingId": "<my-custom-tracking-ID>"
      },
      "trackedProperties": {
         "myTrackedPropertyName": "<value>"
      }
   }
}
```

## <a name="next-steps"></a>Következő lépések

* [Monitorozási és nyomkövetési lekérdezések létrehozása](../logic-apps/create-monitoring-tracking-queries.md)
* [B2B-üzenetek monitorozása Azure Monitor-naplókkal](../logic-apps/monitor-b2b-messages-log-analytics.md)
