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
ms.openlocfilehash: 824308b66803d2dfa05383ff06ce97c48626619d
ms.sourcegitcommit: de98cb7b98eaab1b92aa6a378436d9d513494404
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/17/2021
ms.locfileid: "100557589"
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

```:::image type="content" source="media/howto-create-custom-rules/event-hubs-namespace.png" alt-text="Screenshot of Event Hubs namespace." border="false":::

## Define the function

This solution uses an Azure Functions app to send an email notification when the Stream Analytics job detects a stopped device. To create your function app:

1. In the Azure portal, navigate to the **App Service** instance in the **DetectStoppedDevices** resource group.
1. Select **+** to create a new function.
1. Select **HTTP Trigger**.
1. Select **Add**.

    :::image type="content" source="media/howto-create-custom-rules/add-function.png" alt-text="Image of the Default HTTP trigger function"::: 

## Edit code for HTTP Trigger

The portal creates a default function called **HttpTrigger1**:

```:::image type="content" source="media/howto-create-custom-rules/default-function.png" alt-text="Screenshot of Edit HTTP trigger function.":::

1. Replace the C# code with the following code:

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

    You may see an error message until you save the new code.
1. Select **Save** to save the function.

## Add SendGrid Key

To add your SendGrid API Key, you need to add it to your **Function Keys** as follows:

1. Select **Function Keys**.
1. Choose **+ New Function Key**.
1. Enter the *Name* and *Value* of the API Key you created before.
1. Click **OK.**

    :::image type="content" source="media/howto-create-custom-rules/add-key.png" alt-text="Screenshot of Add Sangrid Key.":::


## Configure HttpTrigger function to use SendGrid

To send emails with SendGrid, you need to configure the bindings for your function as follows:

1. Select **Integrate**.
1. Choose **Add Output** under **HTTP ($return)**.
1. Select **Delete.**
1. Choose **+ New Output**.
1. For Binding Type, then choose **SendGrid**.
1. For SendGrid API Key Setting Type, click New.
1. Enter the *Name* and *Value* of your SendGrid API key.
1. Add the following information:

| Setting | Value |
| ------- | ----- |
| Message parameter name | Choose your name |
| To address | Choose the name of your To Address |
| From address | Choose the name of your From Address |
| Message subject | Enter your subject header |
| Message text | Enter the message from your integration |

1. Select **OK**.

    :::image type="content" source="media/howto-create-custom-rules/add-output.png" alt-text="Screenshot of Add SandGrid Output.":::


### Test the function works

To test the function in the portal, first choose **Logs** at the bottom of the code editor. Then choose **Test** to the right of the code editor. Use the following JSON as the **Request body**:

```json
[{"deviceid":"test-device-1","time":"2019-05-02T14:23:39.527Z"},{"deviceid":"test-device-2","time":"2019-05-02T14:23:50.717Z"},{"deviceid":"test-device-3","time":"2019-05-02T14:24:28.919Z"}]
```

A függvények naplójának üzenetei a **naplók** panelen jelennek meg:

```:::image type="content" source="media/howto-create-custom-rules/function-app-logs.png" alt-text="Function log output":::

After a few minutes, the **To** email address receives an email with the following content:

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

Az [Azure IoT Central Application Manager](https://aka.ms/iotcentral) webhelyén navigáljon a létrehozott IoT Central alkalmazáshoz.

Ebben a szakaszban úgy konfigurálja az alkalmazást, hogy a szimulált eszközökről az telemetria továbbítsa az alkalmazást. Az Exportálás konfigurálása:

1. Navigáljon az **adatexportálás** lapra, válassza az **+ új**, majd az **Azure Event Hubs** elemet.
1. Az Exportálás konfigurálásához használja a következő beállításokat, majd válassza a **Mentés** lehetőséget: 

    | Beállítás | Érték |
    | ------- | ----- |
    | Megjelenítendő név | Exportálás Event Hubsba |
    | Engedélyezve | Be |
    | Exportálandó adattípusok | Telemetria |
    | Modellbővítések | Adja meg a kívánt kulcs/érték értékét, hogyan szeretné rendszerezni az exportált adatmennyiséget | 
    | Cél | Hozzon létre új adatokat, és adja meg az adatok exportálásának helyét. |

    :::image type="content" source="media/howto-create-custom-rules/cde-configuration.png" alt-text="Képernyőkép az adatexportálásról.":::

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
