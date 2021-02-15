---
title: Az Azure IoT Central kiterjesztése egyéni szabályokkal és értesítésekkel | Microsoft Docs
description: Megoldás fejlesztőként konfiguráljon egy IoT Central alkalmazást e-mail-értesítések küldéséhez, amikor egy eszköz leállítja a telemetria küldését. Ez a megoldás Azure Stream Analytics, Azure Functions és SendGrid használ.
author: TheJasonAndrew
ms.author: v-anjaso
ms.date: 02/09/2021
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.custom: mvc, devx-track-csharp
manager: philmea
ms.openlocfilehash: 7e3292a9070e6676faad15e73d357e7f6875b5f4
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/14/2021
ms.locfileid: "100371672"
---
# <a name="extend-azure-iot-central-with-custom-rules-using-stream-analytics-azure-functions-and-sendgrid"></a>Az Azure IoT Central kibővítése egyéni szabályokkal a Stream Analytics, az Azure Functions és a SendGrid használatával

Ez az útmutató bemutatja, hogyan bővítheti IoT Central alkalmazását egyéni szabályokkal és értesítésekkel. A példa egy értesítés küldését mutatja be egy operátornak, amikor egy eszköz leállítja a telemetria küldését. A megoldás egy [Azure stream Analytics](../../stream-analytics/index.yml) lekérdezést használ annak észlelésére, hogy egy eszköz leállította-e a telemetria küldését. A Stream Analytics-feladatokhoz [Azure functions](../../azure-functions/index.yml) kell küldenie az értesítő e-maileket a [SendGrid](https://sendgrid.com/docs/for-developers/partners/microsoft-azure/)használatával.

Ez a útmutató bemutatja, hogyan bővítheti IoT Central a beépített szabályokkal és műveletekkel, hogy mit tehet.

Ebben a útmutatóban a következőket sajátíthatja el:

* Stream telemetria egy IoT Central alkalmazásból *folyamatos adatexportálás* használatával.
* Hozzon létre egy Stream Analytics-lekérdezést, amely észleli, ha egy eszköz leállította az adatküldést.
* Értesítés küldése e-mailben a Azure Functions és a SendGrid Services használatával.

## <a name="prerequisites"></a>Előfeltételek

A jelen útmutató lépéseinek végrehajtásához aktív Azure-előfizetésre van szükség.

Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

### <a name="iot-central-application"></a>IoT Central alkalmazás

Hozzon létre egy IoT Central alkalmazást az [Azure IoT Central Application Manager](https://aka.ms/iotcentral) webhelyén a következő beállításokkal:

| Beállítás | Érték |
| ------- | ----- |
| Díjszabási csomag | Standard |
| Alkalmazássablon | Áruházbeli elemzés – állapot figyelése |
| Alkalmazásnév | Fogadja el az alapértelmezett értéket, vagy válassza ki a saját nevét |
| URL-cím | Fogadja el az alapértelmezett értéket, vagy válassza ki a saját egyedi URL-előtagját |
| Címtár | Azure Active Directory bérlő |
| Azure-előfizetés | Az Azure-előfizetése |
| Region | A legközelebbi régió |

A cikkben szereplő példák és Képernyőképek a **Egyesült Államok** régiót használják. Válasszon egy helyet az Ön számára, és győződjön meg róla, hogy az összes erőforrást ugyanabban a régióban hozza létre.

Ez az alkalmazás sablon két szimulált termosztátos eszközt tartalmaz, amelyek telemetria küldenek.

### <a name="resource-group"></a>Erőforráscsoport

A [Azure Portal használatával hozzon létre egy](https://portal.azure.com/#create/Microsoft.ResourceGroup) **DetectStoppedDevices** nevű erőforráscsoportot a létrehozott egyéb erőforrások tárolására. Hozza létre az Azure-erőforrásokat a IoT Central alkalmazással megegyező helyen.

### <a name="event-hubs-namespace"></a>Event Hubs-névtér

A [Azure Portal használatával hozzon létre egy Event Hubs névteret](https://portal.azure.com/#create/Microsoft.EventHub) a következő beállításokkal:

| Beállítás | Érték |
| ------- | ----- |
| Név    | Adja meg a névtér nevét |
| Tarifacsomag | Alapszintű |
| Előfizetés | Az Ön előfizetése |
| Erőforráscsoport | DetectStoppedDevices |
| Hely | USA keleti régiója |
| Átviteli egységek | 1 |

### <a name="stream-analytics-job"></a>Stream Analytics feladatok

A [Azure Portal használatával hozzon létre egy stream Analytics feladatot](https://portal.azure.com/#create/Microsoft.StreamAnalyticsJob)  a következő beállításokkal:

| Beállítás | Érték |
| ------- | ----- |
| Név    | Válassza ki a feladatokhoz tartozó nevet |
| Előfizetés | Az Ön előfizetése |
| Erőforráscsoport | DetectStoppedDevices |
| Hely | USA keleti régiója |
| Üzemeltetési környezet | Felhőbeli |
| Streamelési egységek | 3 |

### <a name="function-app"></a>Függvényalkalmazás

A [Azure Portal használatával hozzon létre egy Function alkalmazást](https://portal.azure.com/#create/Microsoft.FunctionApp) a következő beállításokkal:

| Beállítás | Érték |
| ------- | ----- |
| Alkalmazás neve    | Válassza ki a Function alkalmazás nevét |
| Előfizetés | Az Ön előfizetése |
| Erőforráscsoport | DetectStoppedDevices |
| Operációs rendszer | Windows |
| Szolgáltatási csomag | Használatalapú csomag |
| Hely | USA keleti régiója |
| Futtatókörnyezet verme | .NET |
| Tárolás | Új létrehozása |

### <a name="sendgrid-account-and-api-keys"></a>SendGrid-fiók és API-kulcsok

Ha nem rendelkezik Sendgrid-fiókkal, hozzon létre egy [ingyenes fiókot](https://app.sendgrid.com/) a Kezdés előtt.

1. A bal oldali menüben a Sendgrid-irányítópult beállításainál válassza az **API-kulcsok** elemet.
1. Kattintson az **API-kulcs létrehozása** lehetőségre.
1. Nevezze el az új API-kulcs **AzureFunctionAccess.**
1. Kattintson a **létrehozás & nézet** elemre.

    :::image type="content" source="media/howto-create-custom-rules/sendgrid-api-keys.png" alt-text="Képernyőfelvétel a SendGrid API-kulcs létrehozásáról.":::

Ezt követően egy API-kulcsot fog kapni. Mentse ezt a karakterláncot későbbi használatra.

## <a name="create-an-event-hub"></a>Eseményközpont létrehozása

IoT Central alkalmazást úgy konfigurálhatja, hogy folyamatosan exportálja a telemetria egy Event hubhoz. Ebben a szakaszban egy Event hub-t hoz létre, amely telemetria fogad a IoT Central alkalmazásból. Az Event hub a telemetria az Stream Analytics-feladatokhoz továbbítja a feldolgozáshoz.

1. A Azure Portal navigáljon a Event Hubs névtérhez, és válassza a **+ Event hub** elemet.
1. Nevezze el az Event hub- **centralexport**, majd válassza a **Létrehozás** lehetőséget.

A Event Hubs névtere a következő képernyőképre hasonlít: 

    :::image type="content" source="media/howto-create-custom-rules/event-hubs-namespace.png" alt-text="Screenshot of Event Hubs namespace." border="false":::

## <a name="define-the-function"></a>A függvény megadása

Ez a megoldás egy Azure Functions alkalmazást használ e-mail-értesítés küldéséhez, ha a Stream Analytics feladatokban leállított eszköz észlelve. A Function-alkalmazás létrehozása:

1. A Azure Portal navigáljon a **DetectStoppedDevices** erőforráscsoport **app Service** példányához.
1. **+** Új függvény létrehozásához válassza a lehetőséget.
1. Válassza a **http-trigger** lehetőséget.
1. Válassza a **Hozzáadás** lehetőséget.

    :::image type="content" source="media/howto-create-custom-rules/add-function.png" alt-text="Az alapértelmezett HTTP-trigger függvény képe"::: 

## <a name="edit-code-for-http-trigger"></a>Kód szerkesztése HTTP-triggerhez

A portál létrehoz egy **HttpTrigger1** nevű alapértelmezett függvényt:

    :::image type="content" source="media/howto-create-custom-rules/default-function.png" alt-text="Screenshot of Edit HTTP trigger function.":::

1. Cserélje le a C#-kódot a következő kódra:

    ```csharp
    #r "Newtonsoft.Json"
    #r "SendGrid"
    using System;
    using SendGrid.Helpers.Mail;
    using Microsoft.Azure.WebJobs.Host;
    using Microsoft.AspNetCore.Mvc;
    using Microsoft.Extensions.Primitives;
    using Newtonsoft.Json;

    public static SendGridMessage Run(HttpRequest req, ILogger log)
    {
        string requestBody = new StreamReader(req.Body).ReadToEnd();
        log.LogInformation(requestBody);
        var notifications = JsonConvert.DeserializeObject<IList<Notification>>(requestBody);

        SendGridMessage message = new SendGridMessage();
        message.Subject = "Contoso device notification";

        var content = "The following device(s) have stopped sending telemetry:<br/><br/><table><tr><th>Device ID</th><th>Time</th></tr>";
        foreach(var notification in notifications) {
            log.LogInformation($"No message received - Device: {notification.deviceid}, Time: {notification.time}");
            content += $"<tr><td>{notification.deviceid}</td><td>{notification.time}</td></tr>";
        }
        content += "</table>";
        message.AddContent("text/html", content);  

        return message;
    }

    public class Notification
    {
        public string deviceid { get; set; }
        public string time { get; set; }
    }
    ```

    Előfordulhat, hogy az új kód mentésekor hibaüzenet jelenik meg.
1. A függvény mentéséhez válassza a **Mentés** lehetőséget.

## <a name="add-sendgrid-key"></a>SendGrid kulcs hozzáadása

A SendGrid API-kulcs hozzáadásához hozzá kell adnia azt a **funkcióbillentyűk** számára a következő módon:

1. Válassza a **funkcióbillentyűk** lehetőséget.
1. Válassza az **+ új funkcióbillentyű** lehetőséget.
1. Adja meg a korábban létrehozott API-kulcs *nevét* és *értékét* .
1. Kattintson **az OK gombra.**

    :::image type="content" source="media/howto-create-custom-rules/add-key.png" alt-text="Képernyőkép a Sangrid-kulcs hozzáadásáról.":::


## <a name="configure-httptrigger-function-to-use-sendgrid"></a>A HttpTrigger függvény konfigurálása a SendGrid használatára

Az e-mailek SendGrid való elküldéséhez a következő módon kell konfigurálnia a függvény kötéseit:

1. Válassza az **Integrálás** lehetőséget.
1. Válassza a **kimenet hozzáadása** **http-ben ($Return)** lehetőséget.
1. Válassza a **Törlés lehetőséget.**
1. Válassza az **+ új kimenet** lehetőséget.
1. A kötés típusa beállításnál válassza a **SendGrid** lehetőséget.
1. A SendGrid API-kulcs beállításának típusa területen kattintson az új elemre.
1. Adja meg a SendGrid API-kulcs *nevét* és *értékét* .
1. Adja meg a következő információkat:

| Beállítás | Érték |
| ------- | ----- |
| Üzenet-paraméter neve | Válassza ki a nevét |
| A címe | Válassza ki a címnek a nevét |
| Feladó címe | Válassza ki a feladó címe nevet |
| Üzenet tárgya | Adja meg a tárgy fejlécét |
| Szöveges üzenet | Adja meg az integrációs üzenetet |

1. Válassza az **OK** lehetőséget.

    :::image type="content" source="media/howto-create-custom-rules/add-output.png" alt-text="Képernyőkép a SandGrid-kimenet hozzáadásáról.":::


### <a name="test-the-function-works"></a>A függvény működésének tesztelése

A függvénynek a portálon való teszteléséhez először a Kódszerkesztő alján válassza a **naplók** lehetőséget. Ezután válassza a **teszt** elemet a Kódszerkesztő jobb oldalán. A következő JSON-t használja a **kérelem törzse**:

```json
[{"deviceid":"test-device-1","time":"2019-05-02T14:23:39.527Z"},{"deviceid":"test-device-2","time":"2019-05-02T14:23:50.717Z"},{"deviceid":"test-device-3","time":"2019-05-02T14:24:28.919Z"}]
```

A függvények naplójának üzenetei a **naplók** panelen jelennek meg:

    :::image type="content" source="media/howto-create-custom-rules/function-app-logs.png" alt-text="Function log output":::

Néhány perc elteltével az e- **mail-címe** megkapja az alábbi tartalommal ellátott e-mailt:

```txt
The following device(s) have stopped sending telemetry:

Device ID    Time
test-device-1    2019-05-02T14:23:39.527Z
test-device-2    2019-05-02T14:23:50.717Z
test-device-3    2019-05-02T14:24:28.919Z
```

## <a name="add-stream-analytics-query"></a>Stream Analytics lekérdezés hozzáadása

Ez a megoldás egy Stream Analytics lekérdezést használ annak észlelésére, hogy egy eszköz nem küld-e telemetria több mint 120 másodpercig. A lekérdezés az Event hub telemetria használja bemenetként. A feladat elküldi a lekérdezés eredményeit a Function alkalmazásnak. Ebben a szakaszban a Stream Analytics feladatot konfigurálja:

1. A Azure Portal navigáljon a Stream Analytics feladathoz a **feladatok topológia** területen válassza a **bemenetek** lehetőséget, válassza a **+ stream-bemenet hozzáadása** lehetőséget, majd válassza az **Event hub** elemet.
1. A következő táblázatban található információk segítségével konfigurálja a bemenetet a korábban létrehozott Event hub használatával, majd válassza a **Mentés** lehetőséget:

    | Beállítás | Érték |
    | ------- | ----- |
    | Bemeneti alias | centraltelemetry |
    | Előfizetés | Az Ön előfizetése |
    | Event Hubs-névtér | Az Event hub-névtér |
    | Eseményközpont neve | Meglévő **centralexport** használata |

1. A **feladatok topológia** területen válassza a **kimenetek**, majd a **+ Hozzáadás**, majd az **Azure-függvény** lehetőséget.
1. A következő táblázatban található információk segítségével konfigurálja a kimenetet, majd válassza a **Mentés** lehetőséget:

    | Beállítás | Érték |
    | ------- | ----- |
    | Kimeneti alias | emailnotification |
    | Előfizetés | Az Ön előfizetése |
    | Függvényalkalmazás | A Function alkalmazás |
    | Függvény  | HttpTrigger1 |

1. A **feladatok topológia** területen válassza a **lekérdezés** lehetőséget, és cserélje le a meglévő lekérdezést a következő SQL-re:

    ```sql
    with
    LeftSide as
    (
        SELECT
        -- Get the device ID from the message metadata and create a column
        GetMetadataPropertyValue([centraltelemetry], '[EventHub].[IoTConnectionDeviceId]') as deviceid1, 
        EventEnqueuedUtcTime AS time1
        FROM
        -- Use the event enqueued time for time-based operations
        [centraltelemetry] TIMESTAMP BY EventEnqueuedUtcTime
    ),
    RightSide as
    (
        SELECT
        -- Get the device ID from the message metadata and create a column
        GetMetadataPropertyValue([centraltelemetry], '[EventHub].[IoTConnectionDeviceId]') as deviceid2, 
        EventEnqueuedUtcTime AS time2
        FROM
        -- Use the event enqueued time for time-based operations
        [centraltelemetry] TIMESTAMP BY EventEnqueuedUtcTime
    )

    SELECT
        LeftSide.deviceid1 as deviceid,
        LeftSide.time1 as time
    INTO
        [emailnotification]
    FROM
        LeftSide
        LEFT OUTER JOIN
        RightSide 
        ON
        LeftSide.deviceid1=RightSide.deviceid2 AND DATEDIFF(second,LeftSide,RightSide) BETWEEN 1 AND 120
        where
        -- Find records where a device didn't send a message 120 seconds
        RightSide.deviceid2 is NULL
    ```

1. Kattintson a **Mentés** gombra.
1. A Stream Analytics-feladatok elindításához válassza az **Áttekintés**, majd a **Start** **, majd a** Start lehetőséget, majd **indítsa** el a következőt:

    :::image type="content" source="media/howto-create-custom-rules/stream-analytics.png" alt-text="Képernyőkép a Stream Analyticsról.":::

## <a name="configure-export-in-iot-central"></a>Az Exportálás konfigurálása IoT Central

Az [Azure IoT Central Application Manager](https://aka.ms/iotcentral) webhelyén navigáljon a contoso-sablonból létrehozott IoT Central alkalmazáshoz. Ebben a szakaszban úgy konfigurálja az alkalmazást, hogy a szimulált eszközökről az telemetria továbbítsa az alkalmazást. Az Exportálás konfigurálása:

1. Navigáljon az **adatexportálás** lapra, válassza az **+ új**, majd az **Azure Event Hubs** elemet.
1. Az Exportálás konfigurálásához használja a következő beállításokat, majd válassza a **Mentés** lehetőséget: 

    | Beállítás | Érték |
    | ------- | ----- |
    | Megjelenítendő név | Exportálás Event Hubsba |
    | Engedélyezve | Be |
    | Event Hubs-névtér | Az Event Hubs névtér neve |
    | Eseményközpont | centralexport |
    | Mérések | Be |
    | Eszközök | Ki |
    | Eszközök sablonjai | Ki |

    :::image type="content" source="media/howto-create-custom-rules/cde-configuration.png" alt-text="Képernyőkép a folyamatos adatexportálási konfigurációról.":::

A folytatás előtt várjon, amíg az Exportálás állapota **fut** .

## <a name="test"></a>Tesztelés

A megoldás teszteléséhez letilthatja a folyamatos adatexportálást IoT Centralról szimulált leállított eszközökre:

1. A IoT Central alkalmazásban navigáljon az **adatexportálás** lapra, és válassza az **Exportálás Event Hubs** exportálási konfigurációt.
1. Válassza **ki** az **engedélyezve** lehetőséget, majd kattintson a **Mentés** gombra.
1. Legalább két perc elteltével az e-mail-cím egy vagy **több olyan e** -mailt kap, amely az alábbi példához hasonlóan néz ki:

    ```txt
    The following device(s) have stopped sending telemetry:

    Device ID         Time
    Thermostat-Zone1  2019-11-01T12:45:14.686Z
    ```

## <a name="tidy-up"></a>Kitakarítás

A fenti útmutató és a szükségtelen költségek elkerülése érdekében törölje a **DetectStoppedDevices** erőforráscsoportot a Azure Portal.

A IoT Central alkalmazást a **felügyeleti** lapról törölheti az alkalmazáson belül.

## <a name="next-steps"></a>Következő lépések

Ebben a útmutatóban megtanulta, hogyan végezheti el a következőket:

* Stream telemetria egy IoT Central alkalmazásból *folyamatos adatexportálás* használatával.
* Hozzon létre egy Stream Analytics-lekérdezést, amely észleli, ha egy eszköz leállította az adatküldést.
* Értesítés küldése e-mailben a Azure Functions és a SendGrid Services használatával.

Most, hogy már tudja, hogyan hozhat létre egyéni szabályokat és értesítéseket, a javasolt következő lépés az [Azure-IoT Central egyéni elemzésekkel való bővítésének](howto-create-custom-analytics.md)megismerése.
