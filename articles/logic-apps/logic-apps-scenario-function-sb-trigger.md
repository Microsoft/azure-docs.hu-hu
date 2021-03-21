---
title: Logikai alkalmazások meghívása Azure Functions-függvényekkel
description: Logikai alkalmazások meghívása vagy elindítása Azure Functions és Azure Service Bus használatával
services: logic-apps
ms.suite: integration
ms.reviewer: jehollan, klam, logicappspm
ms.topic: article
ms.date: 11/08/2019
ms.custom: devx-track-csharp
ms.openlocfilehash: a7df9ba1318f40de8af392cfaedbe51d7a5df755
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98784933"
---
# <a name="call-or-trigger-logic-apps-by-using-azure-functions-and-azure-service-bus"></a>Logikai alkalmazások meghívása vagy elindítása Azure Functions és Azure Service Bus használatával

A [Azure functions](../azure-functions/functions-overview.md) használatával aktiválhat egy logikai alkalmazást, ha hosszú ideig futó figyelőt vagy feladatot kell telepítenie. Létrehozhat például egy olyan függvényt, amely egy [Azure Service Bus](../service-bus-messaging/service-bus-messaging-overview.md) várólistán figyeli, és azonnal leküldéses eseményindítóként egy logikai alkalmazást indít el.

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, [regisztráljon egy ingyenes Azure-fiókra](https://azure.microsoft.com/free/).

* Egy Azure Service Bus névtér. Ha nem rendelkezik névtérrel, [először hozza létre a névteret](../service-bus-messaging/service-bus-create-namespace-portal.md).

* Function-alkalmazás, amely a függvények tárolója. Ha nem rendelkezik Function alkalmazással, [először hozza létre a Function alkalmazást](../azure-functions/functions-get-started.md), és győződjön meg arról, hogy a .NET-et futtatókörnyezeti veremként választja.

* Alapvető ismeretek a [logikai alkalmazások létrehozásáról](../logic-apps/quickstart-create-first-logic-app-workflow.md)

## <a name="create-logic-app"></a>Logikai alkalmazás létrehozása

Ebben a forgatókönyvben egy olyan függvény fut, amely minden olyan logikai alkalmazást futtat, amelyet el szeretne indítani. Először hozzon létre egy logikai alkalmazást, amely egy HTTP-kérelem triggerével kezdődik. A függvény meghívja ezt a végpontot, amikor üzenetsor-üzenetet fogad.

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com), és hozzon létre üres logikai alkalmazást.

   Ha most ismerkedik a Logic apps szolgáltatással, tekintse át a gyors útmutató [: az első logikai alkalmazás létrehozása](../logic-apps/quickstart-create-first-logic-app-workflow.md)című útmutatót.

1. A keresőmezőbe írja be a `http request` kifejezést. Az eseményindítók listából válassza ki a **http-kérelem fogadásának időpontját** .

   ![Trigger kiválasztása](./media/logic-apps-scenario-function-sb-trigger/when-http-request-received-trigger.png)

   A kérelem triggerrel megadhat egy JSON-sémát, amelyet az üzenetsor-üzenethez használhat. A JSON-sémák segítenek a Logic app designernek megérteni a bemeneti adatok szerkezetét, és megkönnyíti a kimenetek használatát a munkafolyamatban.

1. Séma megadásához írja be a sémát a **kérelem törzse JSON-sémája** mezőbe, például:

   ![JSON-séma meghatározása](./media/logic-apps-scenario-function-sb-trigger/when-http-request-received-trigger-schema.png)

   Ha nem rendelkezik sémával, de JSON formátumú minta-adattartalommal rendelkezik, létrehozhat egy sémát ebből a hasznos adatból.

   1. A kérelem triggerben válassza a **minta hasznos adatok használata a séma létrehozásához** lehetőséget.

   1. Az **írja be vagy illessze be a minta JSON-adattartalmat** területen adja meg a minta hasznos adatait, majd kattintson a **kész** gombra.

      ![Adja meg a minta hasznos adatait](./media/logic-apps-scenario-function-sb-trigger/enter-sample-payload.png)

   Ez a minta hasznos adatokat generáló séma az triggerben jelenik meg:

   ```json
   {
      "type": "object",
      "properties": {
         "address": {
            "type": "object",
            "properties": {
               "number": {
                  "type": "integer"
               },
               "street": {
                  "type": "string"
               },
               "city": {
                  "type": "string"
               },
               "postalCode": {
                  "type": "integer"
               },
               "country": {
                  "type": "string"
               }
            }
         }
      }
   }
   ```

1. Adja meg az üzenetsor-üzenet fogadása után futtatni kívánt egyéb műveleteket.

   Küldhet például egy e-mailt az Office 365 Outlook Connector használatával.

1. Mentse a logikai alkalmazást, amely létrehozza az trigger visszahívási URL-címét a logikai alkalmazásban. Később ezt a visszahívási URL-címet használja a Azure Service Bus üzenetsor-trigger kódjában.

   A visszahívási URL-cím a **http post URL-cím** tulajdonságában jelenik meg.

   ![Generált visszahívási URL-cím triggerhez](./media/logic-apps-scenario-function-sb-trigger/callback-URL-for-trigger.png)

## <a name="create-a-function"></a>Függvény létrehozása

Ezután hozza létre az triggerként viselkedő függvényt, és figyelje a várólistát.

1. A Azure Portal nyissa meg és bontsa ki a Function alkalmazást, ha még nincs megnyitva. 

1. A Function alkalmazás neve alatt bontsa ki a **függvények** elemet. A **függvények** ablaktáblán válassza az **új függvény** lehetőséget.

   ![Bontsa ki a "függvények" elemet, és válassza az "új függvény" lehetőséget.](./media/logic-apps-scenario-function-sb-trigger/add-new-function-to-function-app.png)

1. Válassza ki ezt a sablont attól függően, hogy létrehozott-e egy új Function alkalmazást, ahol a .NET-et futtatókörnyezeti veremként választotta, vagy egy meglévő Function alkalmazást használ.

   * Az új Function apps esetében válassza a következő sablont: **Service Bus üzenetsor-trigger**

     ![Új Function-alkalmazás sablonjának kiválasztása](./media/logic-apps-scenario-function-sb-trigger/current-add-queue-trigger-template.png)

   * Egy meglévő Function alkalmazás esetében válassza a következő sablont: **Service Bus üzenetsor-trigger – C#**

     ![Sablon kiválasztása meglévő Function-alkalmazáshoz](./media/logic-apps-scenario-function-sb-trigger/legacy-add-queue-trigger-template.png)

1. Az **Azure Service Bus várólista-trigger** ablaktáblán adja meg az trigger nevét, majd állítsa be a **Service Bus-kapcsolatokat** a várólista számára, amely az Azure Service Bus SDK- `OnMessageReceive()` figyelőt használja, majd válassza a **Létrehozás** lehetőséget.

1. Írjon egy alapszintű függvényt a korábban létrehozott Logic app-végpont meghívásához az üzenetsor-üzenet triggerként való használatával. A függvény írása előtt tekintse át a következő szempontokat:

   * Ez a példa az `application/json` üzenet tartalmának típusát használja, de szükség szerint módosíthatja ezt a típust.
   
   * A lehetséges párhuzamosan futó függvények, nagy kötetek vagy nagy terhelések miatt ne hozza létre a [HTTPClient osztályt](/dotnet/api/system.net.http.httpclient) az `using` utasítással, és hozzon létre egy kérést közvetlenül a HTTPClient-példányok létrehozásával. További információ: [rugalmas HTTP-kérések implementálása a HttpClientFactory használatával](/dotnet/architecture/microservices/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests#issues-with-the-original-httpclient-class-available-in-net-core).
   
   * Ha lehetséges, használja újra a HTTP-ügyfelek példányát. További információ: [kapcsolatok kezelése Azure Functionsban](../azure-functions/manage-connections.md).

   Ez a példa [aszinkron](/dotnet/csharp/language-reference/keywords/async) módban használja a [ `Task.Run` metódust](/dotnet/api/system.threading.tasks.task.run) . További információ: [aszinkron programozás aszinkron módon és várakozás](/dotnet/csharp/programming-guide/concepts/async/).

   ```csharp
   using System;
   using System.Threading.Tasks;
   using System.Net.Http;
   using System.Text;

   // Can also fetch from App Settings or environment variable
   private static string logicAppUri = @"https://prod-05.westus.logic.azure.com:443/workflows/<remaining-callback-URL>";

   // Reuse the instance of HTTP clients if possible: https://docs.microsoft.com/azure/azure-functions/manage-connections
   private static HttpClient httpClient = new HttpClient();

   public static async Task Run(string myQueueItem, TraceWriter log) 
   {
      log.Info($"C# ServiceBus queue trigger function processed message: {myQueueItem}");
      var response = await httpClient.PostAsync(logicAppUri, new StringContent(myQueueItem, Encoding.UTF8, "application/json")); 
   }
   ```

1. A függvény teszteléséhez adjon hozzá egy üzenetsor-üzenetet egy olyan eszköz használatával, mint például a [Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer).

   A logikai alkalmazás azonnal elindul, miután a függvény megkapja az üzenetet.

## <a name="next-steps"></a>Következő lépések

* [Munkafolyamatok hívása, elindítása vagy beágyazása HTTP-végpontok használatával](../logic-apps/logic-apps-http-endpoint.md)