---
title: Hibák és kivételek kezelése a munkafolyamatokban
description: Megtudhatja, hogyan kezelheti a Azure Logic Apps használatával létrehozott automatizált feladatokban és munkafolyamatokban előforduló hibákat és kivételeket.
services: logic-apps
ms.suite: integration
author: dereklee
ms.author: deli
ms.reviewer: estfan, logicappspm, azla
ms.date: 02/18/2021
ms.topic: article
ms.openlocfilehash: fbe797937021763bb97ca09e1da792d9a7010f9a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "101702504"
---
# <a name="handle-errors-and-exceptions-in-azure-logic-apps"></a>Hibák és kivételek kezelése az Azure Logic Appsben

Az integrációs architektúra megfelelő módon kezeli az állásidőt vagy a függő rendszerek által okozott problémákat. A problémák és hibák zökkenőmentes kezelésére szolgáló robusztus és rugalmas integrációk létrehozásához Logic Apps a hibák és kivételek kezelésének első osztályú élményét nyújtja.

<a name="retry-policies"></a>

## <a name="retry-policies"></a>Újrapróbálkozási szabályzatok

A legalapvetőbb kivételek és hibakezelés érdekében az *újrapróbálkozási szabályzatot* bármilyen művelet vagy trigger esetében használhatja, ahol ez támogatott, például: [http-művelet](../logic-apps/logic-apps-workflow-actions-triggers.md#http-trigger). Az újrapróbálkozási szabályzat meghatározza, hogy a művelet vagy az indítás hogyan próbálkozik újra egy kéréssel, amikor az eredeti kérelem időtúllépése vagy meghibásodása meghiúsul, ami 408, 429 vagy 5xx választ eredményez. Ha nincs más újrapróbálkozási szabályzat, a rendszer az alapértelmezett házirendet használja.

Az újrapróbálkozási szabályzatok típusai:

| Típus | Leírás |
|------|-------------|
| **Alapértelmezett** | Ez a szabályzat legfeljebb négy újrapróbálkozást küld el *exponenciálisan növekvő* intervallumokban, amelyek mérete 7,5 másodperc, de 5 – 45 másodpercre van korlátozva. |
| **Exponenciális időköz**  | Ez a szabályzat egy exponenciálisan növekvő tartományból érkező véletlenszerű intervallumot vár a következő kérelem elküldése előtt. |
| **Rögzített időközű**  | Ez a házirend a következő kérelem elküldése előtt megvárja a megadott intervallumot. |
| **Nincs**  | Ne küldje újra a kérelmet. |
|||

További információ az újrapróbálkozási házirend korlátairól: [Logic apps korlátok és konfiguráció](../logic-apps/logic-apps-limits-and-config.md#http-limits).

### <a name="change-retry-policy"></a>Újrapróbálkozási szabályzat módosítása

Másik újrapróbálkozási házirend kiválasztásához kövesse az alábbi lépéseket:

1. Nyissa meg a logikai alkalmazást a Logic app Designerben.

1. Nyisson meg egy művelet vagy trigger **beállításait** .

1. Ha a művelet vagy az aktiválás támogatja az újrapróbálkozási szabályzatokat, az **újrapróbálkozási házirend** területen válassza ki a kívánt típust.

Vagy manuálisan is megadhatja az újrapróbálkozási házirendet az `inputs` újrapróbálkozási szabályzatokat támogató művelet vagy trigger szakaszban. Ha nem ad meg újrapróbálkozási házirendet, a művelet az alapértelmezett házirendet használja.

```json
"<action-name>": {
   "type": "<action-type>",
   "inputs": {
      "<action-specific-inputs>",
      "retryPolicy": {
         "type": "<retry-policy-type>",
         "interval": "<retry-interval>",
         "count": <retry-attempts>,
         "minimumInterval": "<minimum-interval>",
         "maximumInterval": "<maximum-interval>"
      },
      "<other-action-specific-inputs>"
   },
   "runAfter": {}
}
```

*Kötelező*

| Érték | Típus | Leírás |
|-------|------|-------------|
| <*újrapróbálkozás – házirend-típus*> | Sztring | A használni kívánt újrapróbálkozási szabályzat típusa: `default` ,,, `none` `fixed` vagy `exponential` |
| <*újrapróbálkozási időköz*> | Sztring | Az újrapróbálkozási időköz, amelyben az értéknek [ISO 8601 formátumot](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations)kell használnia. Az alapértelmezett minimális időköz, `PT5S` a maximális intervallum `PT1D` . Az exponenciális időközi szabályzat használatakor különböző minimális és maximális értékeket adhat meg. |
| <*újrapróbálkozás – kísérletek*> | Egész szám | Az újrapróbálkozási kísérletek száma, amelynek 1 és 90 között kell lennie |
||||

*Nem kötelező*

| Érték | Típus | Leírás |
|-------|------|-------------|
| <*minimális időköz*> | Sztring | Az exponenciális időközi házirend esetében a véletlenszerűen kiválasztott időköz ( [ISO 8601 formátumban](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations) ) legkisebb intervalluma |
| <*maximális időköz*> | Sztring | Az exponenciális időközi házirend esetében a véletlenszerűen kiválasztott időköz ( [ISO 8601 formátumban](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations) ) legnagyobb intervalluma |
||||

További információ a különböző házirend-típusokról.

<a name="default-retry"></a>

### <a name="default"></a>Alapértelmezett

Ha nem ad meg újrapróbálkozási házirendet, a művelet az alapértelmezett házirendet használja, amely tulajdonképpen egy [exponenciális időközi házirend](#exponential-interval) , amely akár négy újrapróbálkozást is küld, exponenciálisan növekvő időközönként, 7,5 másodpercek között. Az intervallum 5 és 45 másodperc közötti értékre van korlátozva.

Bár a művelet vagy trigger explicit módon nincs definiálva, az alapértelmezett házirend egy példa HTTP-műveletben a következőképpen viselkedik:

```json
"HTTP": {
   "type": "Http",
   "inputs": {
      "method": "GET",
      "uri": "http://myAPIendpoint/api/action",
      "retryPolicy" : {
         "type": "exponential",
         "interval": "PT7S",
         "count": 4,
         "minimumInterval": "PT5S",
         "maximumInterval": "PT1H"
      }
   },
   "runAfter": {}
}
```

### <a name="none"></a>Nincsenek

Annak megadásához, hogy a művelet vagy az aktiválás Ne próbálkozzon újra a sikertelen kérelmekkel, állítsa be a <*újrapróbálkozási házirend típusú> a következőre* : `none` .

### <a name="fixed-interval"></a>Rögzített időközű

Annak megadásához, hogy a művelet vagy az indítás a következő kérelem elküldése előtt megvárja-e a megadott intervallumot, állítsa be a <*újrapróbálkozási-házirend típusú*> `fixed` .

*Példa*

Ez az újrapróbálkozási szabályzat az első sikertelen kérelem után még két alkalommal kísérli meg a legfrissebb híreket, az egyes kísérletek közötti 30 másodperces késleltetéssel:

```json
"Get_latest_news": {
   "type": "Http",
   "inputs": {
      "method": "GET",
      "uri": "https://mynews.example.com/latest",
      "retryPolicy": {
         "type": "fixed",
         "interval": "PT30S",
         "count": 2
      }
   }
}
```

<a name="exponential-interval"></a>

### <a name="exponential-interval"></a>Exponenciális időköz

Annak megadásához, hogy a művelet vagy az indítás a következő kérelem elküldése előtt megvárja-e a véletlenszerű intervallumot, állítsa be a <*újrapróbálkozási házirend típusú*> a értékre `exponential` . A véletlenszerű intervallum egy exponenciálisan növekvő tartományból van kiválasztva. Igény szerint felülbírálhatja az alapértelmezett minimális és maximális intervallumot is a saját minimális és maximális intervallumának megadásával.

**Véletlenszerű változók tartományai**

Ez a táblázat azt mutatja be, hogy a Logic Apps hogyan generál egységes véletlenszerű változót a megadott tartományban az újrapróbálkozások számára, beleértve az újrapróbálkozások számát is:

| Újrapróbálkozás száma | Minimális időköz | Maximális időköz |
|--------------|------------------|------------------|
| 1 | Max (0, <*minimális intervallum*>) | min (intervallum, <*maximális intervallum*>) |
| 2 | Max (intervallum, <*minimális intervallum*>) | min (2 * időköz, <*maximális intervallum*>) |
| 3 | Max (2 * intervallum, <*minimális intervallum*>) | min (4 * intervallum, <*maximális intervallum*>) |
| 4 | Max (4 * intervallum, <*minimális intervallum*>) | min (8 * időköz, <*maximális intervallum*>) |
| .... | .... | .... |
||||

<a name="control-run-after-behavior"></a>

## <a name="catch-and-handle-failures-by-changing-run-after-behavior"></a>A hibák elfogása és kezelése a "Futtatás utáni" viselkedés módosításával

Ha a Logic app Designerben műveleteket ad hozzá, a műveletek futtatásához implicit módon deklarálja a használati sorrendet. Egy művelet futásának befejeződése után a művelet a következő állapottal van megjelölve:,, `Succeeded` `Failed` `Skipped` vagy `TimedOut` . Az egyes műveletek definíciójában a `runAfter` tulajdonság határozza meg azt a megelőző műveletet, amely az első befejezést és a megelőző művelet futtatása előtt engedélyezett állapotokat határozza meg. Alapértelmezés szerint a Designerben megadott művelet csak azt követően fut, hogy a megelőző tevékenység állapota megtörtént `Succeeded` .

Ha egy művelet nem kezelt hibát vagy kivételt jelez, a művelet meg van jelölve `Failed` , és minden követő művelet meg van jelölve `Skipped` . Ha ez egy párhuzamos ágakat tartalmazó művelet esetében fordul elő, akkor a Logic Apps motor a többi elágazást követi a befejezési állapotuk meghatározásához. Ha például egy ág egy `Skipped` művelettel végződik, az adott ág befejezési állapota a kihagyott művelet megelőző állapotán alapul. A logikai alkalmazás futtatásának befejeződése után a motor meghatározza a teljes Futtatás állapotát az összes ág állapotának kiértékelésével. Ha bármelyik ág meghibásodik, a teljes logikai alkalmazás futtatása meg van jelölve `Failed` .

![A futtatási állapotok kiértékelésének módját bemutató példák](./media/logic-apps-exception-handling/status-evaluation-for-parallel-branches.png)

Annak ellenőrzéséhez, hogy egy művelet továbbra is futtatható-e az előd állapota ellenére, [testre szabhatja a művelet "Futtatás utáni" viselkedését](#customize-run-after) , hogy az előd sikertelen állapotait kezelje.

<a name="customize-run-after"></a>

### <a name="customize-run-after-behavior"></a>A "Futtatás utáni" viselkedés testreszabása

Testre szabhatja egy művelet "Futtatás utáni" viselkedését, hogy a művelet akkor fusson, amikor az előd állapota:,, `Succeeded` `Failed` `Skipped` , `TimedOut` vagy bármelyik ilyen állapot. Ha például egy e-mailt szeretne küldeni az Excel online `Add_a_row_into_a_table` elődje művelet jelölése után, a `Failed` `Succeeded` következő lépésekkel módosítsa a "Futtatás után" viselkedést:

* A Tervezés nézetben válassza a három pontot (**..**.), majd válassza a **Futtatás a konfigurálás után** lehetőséget.

  ![A "Futtatás utáni" viselkedés konfigurálása művelethez](./media/logic-apps-exception-handling/configure-run-after-property-setting.png)

  A művelet alakzat a megelőző művelethez szükséges alapértelmezett állapotot jeleníti meg, amely **egy sort ad hozzá** a példában szereplő táblázathoz:

  ![Alapértelmezett "Futtatás utáni" viselkedés egy művelethez](./media/logic-apps-exception-handling/change-run-after-property-status.png)

  Módosítsa a "Futtatás után" viselkedést a kívánt állapotra, ami a következő példában **nem sikerült** :

  ![A "Futtatás utáni" viselkedés módosítása a következőre: "sikertelen"](./media/logic-apps-exception-handling/run-after-property-status-set-to-failed.png)

  Annak megadásához, hogy a művelet fusson-e a megelőző műveletben `Failed` , `Skipped` vagy `TimedOut` válassza ki a többi állapotot:

  ![A "Futtatás utáni" viselkedés módosítása más állapotra](./media/logic-apps-exception-handling/run-after-property-multiple-statuses.png)

* A kód nézetben a művelet JSON-definíciójában szerkessze a `runAfter` tulajdonságot, amely a következő szintaxist követi:

  ```json
  "<action-name>": {
     "inputs": {
        "<action-specific-inputs>"
     },
     "runAfter": {
        "<preceding-action>": [
           "Succeeded"
        ]
     },
     "type": "<action-type>"
  }
  ```

  Ehhez a példához módosítsa a `runAfter` tulajdonságot a következőre `Succeeded` `Failed` :

  ```json
  "Send_an_email_(V2)": {
     "inputs": {
        "body": {
           "Body": "<p>Failed to&nbsp;add row to &nbsp;@{body('Add_a_row_into_a_table')?['Terms']}</p>",,
           "Subject": "Add row to table failed: @{body('Add_a_row_into_a_table')?['Terms']}",
           "To": "Sophia.Owen@fabrikam.com"
        },
        "host": {
           "connection": {
              "name": "@parameters('$connections')['office365']['connectionId']"
           }
        },
        "method": "post",
        "path": "/v2/Mail"
     },
     "runAfter": {
        "Add_a_row_into_a_table": [
           "Failed"
        ]
     },
     "type": "ApiConnection"
  }
  ```

  Annak megadásához, hogy a művelet fusson-e a megelőző műveletben `Failed` , `Skipped` vagy `TimedOut` adja hozzá a többi állapotot:

  ```json
  "runAfter": {
     "Add_a_row_into_a_table": [
        "Failed", "Skipped", "TimedOut"
     ]
  },
  ```

<a name="scopes"></a>

## <a name="evaluate-actions-with-scopes-and-their-results"></a>Hatókörökkel és azok eredményeivel kapcsolatos műveletek kiértékelése

Az egyes műveleteknek a tulajdonsággal való elvégzése után a `runAfter` műveletek egy [hatókörön](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md)belül csoportosíthatók. Hatóköröket akkor használhat, ha logikailag csoportosítja a műveleteket, felméri a hatókör összesített állapotát, és végrehajtja a műveleteket az adott állapot alapján. A hatókör összes műveletének futása után a hatókör maga kapja meg a saját állapotát.

A hatókör állapotának ellenőrzését a logikai alkalmazás futtatási állapotának (például `Succeeded` , stb.) ellenőrzési feltételeivel is használhatja `Failed` .

Alapértelmezés szerint, ha a hatókör összes művelete sikeres, a hatókör állapota meg van jelölve `Succeeded` . Ha egy hatókör utolsó művelete a vagy a értékre mutat `Failed` `Aborted` , a hatókör állapota megjelölve lesz `Failed` .

Ha egy hatókörben kivételeket szeretne kifogni `Failed` , és olyan műveleteket futtat, amelyek kezelik ezeket a hibákat, használhatja az `runAfter` adott hatókör tulajdonságát `Failed` . Így ha a hatókör *bármelyik* művelete meghiúsul, és az `runAfter` adott hatókörhöz tartozó tulajdonságot használja, létrehozhat egyetlen műveletet a hibák kifogásához.

A hatókörök korlátozásait lásd: [korlátok és konfiguráció](../logic-apps/logic-apps-limits-and-config.md).

<a name="get-results-from-failures"></a>

### <a name="get-context-and-results-for-failures"></a>A hibák kontextusának és eredményének beolvasása

Bár a hibák egy hatókörből való kifogása hasznos, előfordulhat, hogy olyan kontextust is szeretne, amely segít megérteni, hogy pontosan milyen műveletek sikertelenek, valamint a visszaadott hibák vagy állapotkódok. A [ `result()` függvény](../logic-apps/workflow-definition-language-functions-reference.md#result) egy hatókörrel rendelkező művelet legfelső szintű műveleteiből származó eredményeket adja vissza, ha egyetlen paramétert fogad el, amely a hatókör neve, és olyan tömböt ad vissza, amely az első szintű műveletek eredményét tartalmazza. Ezek a műveleti objektumok ugyanazokat az attribútumokat tartalmazzák, mint a függvény által visszaadott attribútumok `actions()` , például a művelet kezdési időpontja, a Befejezés időpontja, az állapot, a bemenetek, a korrelációs azonosítók és a kimenetek. 

> [!NOTE]
> A `result()` függvény *csak* az első szintű műveletek eredményét adja vissza, és nem a mélyebb beágyazott műveletekből, például a kapcsoló vagy a feltétel műveletből.

A hatókörben meghiúsult műveletek kontextusának beszerzéséhez használhatja a `@result()` kifejezést a hatókör nevével és a `runAfter` tulajdonsággal. Ha a visszaadott tömböt állapottal rendelkező műveletekre szeretné szűrni `Failed` , adja hozzá a [ **tömb szűrése** műveletet](logic-apps-perform-data-operations.md#filter-array-action). Ha műveletet szeretne futtatni egy visszaadott sikertelen művelethez, hajtsa végre a visszaadott szűrt tömböt, és használja az a értéket [ **minden** hurokhoz](../logic-apps/logic-apps-control-flow-loops.md).

Az alábbi példát követve részletes magyarázatot talál, amely egy HTTP POST-kérelmet küld a válasz törzsének a "My_Scope" nevű hatókör-műveleten belül sikertelen műveletekhez:

```json
"Filter_array": {
   "type": "Query",
   "inputs": {
      "from": "@result('My_Scope')",
      "where": "@equals(item()['status'], 'Failed')"
   },
   "runAfter": {
      "My_Scope": [
         "Failed"
      ]
    }
},
"For_each": {
   "type": "foreach",
   "actions": {
      "Log_exception": {
         "type": "Http",
         "inputs": {
            "method": "POST",
            "body": "@item()['outputs']['body']",
            "headers": {
               "x-failed-action-name": "@item()['name']",
               "x-failed-tracking-id": "@item()['clientTrackingId']"
            },
            "uri": "http://requestb.in/"
         },
         "runAfter": {}
      }
   },
   "foreach": "@body('Filter_array')",
   "runAfter": {
      "Filter_array": [
         "Succeeded"
      ]
   }
}
```

Íme egy részletes útmutató, amely leírja, hogy mi történik ebben a példában:

1. A "My_Scope" összes műveletének eredményének lekéréséhez a **tömb szűrése** művelet ezt a szűrési kifejezést használja: `@result('My_Scope')`

1. A **szűrő tömb** feltétele bármely olyan `@result()` elem, amely egyenlő állapottal rendelkezik `Failed` . Ez az állapot szűri azt a tömböt, amely az összes művelet eredményét "My_Scope" értékről egy olyan tömbre szűri, amely csak a sikertelen művelet eredményét eredményezi.

1. Hajtson végre `For_each` hurkos műveletet a *szűrt tömb* kimenetén. Ez a lépés végrehajt egy műveletet a korábban szűrt összes sikertelen művelet eredményéhez.

   Ha a hatókörben egyetlen művelet meghiúsul, a `For_each` hurok műveletei csak egyszer futnak. Több sikertelen művelet hibát okoz egy művelet végrehajtásakor.

1. Küldjön egy HTTP-BEJEGYZÉST az `For_each` elem válasz törzsén, amely a `@item()['outputs']['body']` kifejezés.

   Az `@result()` elem alakzata megegyezik az `@actions()` alakzattal, és ugyanúgy elemezhető.

1. Adjon meg két egyéni fejlécet a sikertelen művelet nevével ( `@item()['name']` ) és a sikertelen Futtatás ügyfél-követési azonosítóval ( `@item()['clientTrackingId']` ).

Hivatkozásként Íme egy példa egyetlen `@result()` elemre, amely az `name` `body` előző példában elemzett,, és `clientTrackingId` tulajdonságokat mutatja. A `For_each` műveleten kívül az `@result()` objektumok tömbjét adja vissza.

```json
{
   "name": "Example_Action_That_Failed",
   "inputs": {
      "uri": "https://myfailedaction.azurewebsites.net",
      "method": "POST"
   },
   "outputs": {
      "statusCode": 404,
      "headers": {
         "Date": "Thu, 11 Aug 2016 03:18:18 GMT",
         "Server": "Microsoft-IIS/8.0",
         "X-Powered-By": "ASP.NET",
         "Content-Length": "68",
         "Content-Type": "application/json"
      },
      "body": {
         "code": "ResourceNotFound",
         "message": "/docs/folder-name/resource-name does not exist"
      }
   },
   "startTime": "2016-08-11T03:18:19.7755341Z",
   "endTime": "2016-08-11T03:18:20.2598835Z",
   "trackingId": "bdd82e28-ba2c-4160-a700-e3a8f1a38e22",
   "clientTrackingId": "08587307213861835591296330354",
   "code": "NotFound",
   "status": "Failed"
}
```

A különböző kivételek kezelésére szolgáló mintázatok végrehajtásához használhatja a cikkben korábban ismertetett kifejezéseket. Dönthet úgy is, hogy egyetlen kivétel-kezelési műveletet hajt végre a hatókörön kívül, amely elfogadja a hibák teljes szűrt tömbjét, és eltávolítja a `For_each` műveletet. A válaszból más hasznos tulajdonságokat is megadhat a `\@result()` korábban leírtaknak megfelelően.

## <a name="set-up-azure-monitor-logs"></a>Azure Monitor naplók beállítása

Az előző minták nagyszerű módon kezelik a hibákat és a kivételeket egy futtatáson belül, de a futtatástól függetlenül is azonosíthatók és reagálnak a hibákra. A [Azure monitor](../azure-monitor/overview.md) egyszerű módot biztosít az összes munkafolyamat-esemény, beleértve a futtatási és a műveleti állapotok elküldését egy [log Analytics-munkaterületre](../azure-monitor/logs/data-platform-logs.md), [Azure Storage-fiókra](../storage/blobs/storage-blobs-overview.md)vagy [Azure-Event Hubsre](../event-hubs/event-hubs-about.md).

A futtatási állapotok kiértékeléséhez nyomon követheti a naplókat és a metrikákat, vagy közzéteheti azokat bármely figyelési eszközön. Az egyik lehetséges lehetőség az, hogy az összes eseményt Event Hubs-ba [Azure stream Analyticsba](https://azure.microsoft.com/services/stream-analytics/)továbbítsa. Stream Analytics élő lekérdezéseket írhat a diagnosztikai naplókból származó rendellenességek, átlagok vagy hibák alapján. A Stream Analytics használatával adatokat küldhet más adatforrásoknak, például várólistákat, témaköröket, SQL-, Azure Cosmos DB-vagy Power BI.

## <a name="next-steps"></a>Következő lépések

* [Megtudhatja, hogy az ügyfél hogyan épít a hibakezelés során Azure Logic Apps](../logic-apps/logic-apps-scenario-error-and-exception-handling.md)
* [További Logic Apps példák és forgatókönyvek](../logic-apps/logic-apps-examples-and-scenarios.md)
