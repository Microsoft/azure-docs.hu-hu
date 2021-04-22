---
title: Az Azure IoT Central kiterjesztése egyéni szabályokkal és értesítésekkel | Microsoft Docs
description: Megoldásfejlesztőként konfiguráljon egy IoT Central, hogy e-mailes értesítéseket küldjön, amikor egy eszköz már nem küld telemetriai adatokat. Ez a megoldás a Azure Stream Analytics, Azure Functions és a SendGridet használja.
author: philmea
ms.author: philmea
ms.date: 02/09/2021
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.custom: mvc, devx-track-csharp
manager: philmea
ms.openlocfilehash: a65d9dbaed4d197c2e0843e73ff3f45b8678017e
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2021
ms.locfileid: "107864216"
---
# <a name="extend-azure-iot-central-with-custom-rules-using-stream-analytics-azure-functions-and-sendgrid"></a>Az Azure IoT Central kibővítése egyéni szabályokkal a Stream Analytics, az Azure Functions és a SendGrid használatával

Ez az útmutató bemutatja megoldásfejlesztőként, hogyan bővítheti IoT Central alkalmazásait egyéni szabályokkal és értesítésekkel. A példa azt mutatja be, hogyan küld egy értesítést egy operátornak, ha egy eszköz már nem küld telemetriai adatokat. A megoldás [egy](../../stream-analytics/index.yml) Azure Stream Analytics lekérdezéssel észleli, ha egy eszköz már nem küld telemetriai adatokat. Az Stream Analytics feladat a [Azure Functions](../../azure-functions/index.yml) küld értesítő e-maileket a [SendGrid használatával.](https://sendgrid.com/docs/for-developers/partners/microsoft-azure/)

Ez az útmutató bemutatja, hogyan bővítheti a IoT Central a beépített szabályokkal és műveletekkel már meglévő műveleteken túlra.

Ez az útmutató a következő lehetőségekkel ismerkedik meg:

* Streamelési telemetria egy IoT Central folyamatos *adatexportációval.*
* Hozzon létre Stream Analytics lekérdezést, amely észleli, ha egy eszköz már nem küld adatokat.
* E-mail-értesítés küldése a Azure Functions SendGrid-szolgáltatásokkal.

## <a name="prerequisites"></a>Előfeltételek

Az útmutató lépéseit egy aktív Azure-előfizetéssel kell végrehajtania.

Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

### <a name="iot-central-application"></a>IoT Central alkalmazás

Hozzon létre IoT Central alkalmazásokat a [Azure IoT Central alkalmazáskezelő webhelyén](https://aka.ms/iotcentral) a következő beállításokkal:

| Beállítás | Érték |
| ------- | ----- |
| Díjszabási csomag | Standard |
| Alkalmazássablon | Áruházbeli elemzés – feltételfigyelés |
| Alkalmazásnév | Fogadja el az alapértelmezett nevet, vagy válasszon saját nevet |
| URL-cím | Fogadja el az alapértelmezett url-címet, vagy válasszon saját egyedi URL-előtagot |
| Címtár | A Azure Active Directory bérlője |
| Azure-előfizetés | Az Azure-előfizetése |
| Region | Az Ön legközelebbi régiója |

A cikkben található példák és képernyőképek a Egyesült Államok **használják.** Válasszon egy közeli helyet, és győződjön meg arról, hogy az összes erőforrást ugyanabban a régióban hozza létre.

Ez az alkalmazássablon két szimulált termosztátos eszközt tartalmaz, amelyek telemetriát küldenek.

### <a name="resource-group"></a>Erőforráscsoport

A Azure Portal [hozzon](https://portal.azure.com/#create/Microsoft.ResourceGroup) létre egy **DetectStoppedDevices** nevű erőforráscsoportot, amely tartalmazza a többi létrehozott erőforrást. Hozza létre az Azure-erőforrásokat a saját alkalmazásával IoT Central helyen.

### <a name="event-hubs-namespace"></a>Event Hubs-névtér

A Azure Portal [hozzon létre egy](https://portal.azure.com/#create/Microsoft.EventHub) Event Hubs a következő beállításokkal:

| Beállítás | Érték |
| ------- | ----- |
| Név    | A névtér nevének kiválasztása |
| Tarifacsomag | Alapszintű |
| Előfizetés | Az Ön előfizetése |
| Erőforráscsoport | DetectStoppedDevices (Észlelt hibák észlelése) |
| Hely | USA keleti régiója |
| Átviteli egységek | 1 |

### <a name="stream-analytics-job"></a>Stream Analytics feladat

A Azure Portal [hozzon létre egy Stream Analytics feladatot](https://portal.azure.com/#create/Microsoft.StreamAnalyticsJob)  a következő beállításokkal:

| Beállítás | Érték |
| ------- | ----- |
| Név    | A feladat nevének kiválasztása |
| Előfizetés | Az Ön előfizetése |
| Erőforráscsoport | DetectStoppedDevices (Észlelt hibák észlelése) |
| Hely | USA keleti régiója |
| Üzemeltetési környezet | Felhőbeli |
| Streamelési egységek | 3 |

### <a name="function-app"></a>Függvényalkalmazás

A Azure Portal [hozzon létre egy függvényalkalmazást a](https://portal.azure.com/#create/Microsoft.FunctionApp) következő beállításokkal:

| Beállítás | Érték |
| ------- | ----- |
| Alkalmazás neve    | A függvényalkalmazás nevének kiválasztása |
| Előfizetés | Az Ön előfizetése |
| Erőforráscsoport | DetectStoppedDevices (Észlelt hibák észlelése) |
| Operációs rendszer | Windows |
| Szolgáltatási csomag | Használatalapú csomag |
| Hely | USA keleti régiója |
| Futtatókörnyezet verme | .NET |
| Tárolás | Új létrehozása |

### <a name="sendgrid-account-and-api-keys"></a>SendGrid-fiók és API-kulcsok

Ha még nincs Sendgrid-fiókja, kezdés előtt hozzon létre egy [ingyenes](https://app.sendgrid.com/) fiókot.

1. A bal oldali menü Sendgrid Irányítópult beállításai menüjében válassza az **API-kulcsok lehetőséget.**
1. Kattintson **az API-kulcs létrehozása elemre.**
1. Az új API-kulcsnak nevezze **el az AzureFunctionAccess nevet.**
1. Kattintson **a Create & View (Nézet &) elemre.**

    :::image type="content" source="media/howto-create-custom-rules/sendgrid-api-keys.png" alt-text="Képernyőkép a SendGrid API-kulcs létrehozásáról.":::

Ezt követően egy API-kulcsot kap. Mentse ezt a sztringet későbbi használatra.

## <a name="create-an-event-hub"></a>Eseményközpont létrehozása

Konfigurálhat egy IoT Central, hogy folyamatosan exportálja a telemetriát egy eseményközpontba. Ebben a szakaszban egy eseményközpontot hoz létre, amely telemetriát fogad a IoT Central alkalmazásból. Az eseményközpont továbbítja a telemetriát a Stream Analytics feldolgozásra.

1. A Azure Portal lépjen a saját Event Hubs, és válassza **az + Eseményközpont lehetőséget.**
1. Adja az eseményközpontnak **a centralexportálás nevet,** majd válassza a **Létrehozás lehetőséget.**

A Event Hubs a következő képernyőképhez hasonló: 

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

A függvény naplóüzenetei a Naplók **panelen jelennek** meg:

```:::image type="content" source="media/howto-create-custom-rules/function-app-logs.png" alt-text="Function log output":::

After a few minutes, the **To** email address receives an email with the following content:

```txt
The following device(s) have stopped sending telemetry:

Device ID    Time
test-device-1    2019-05-02T14:23:39.527Z
test-device-2    2019-05-02T14:23:50.717Z
test-device-3    2019-05-02T14:24:28.919Z
```

## <a name="add-stream-analytics-query"></a>Lekérdezés Stream Analytics hozzáadása

Ez a megoldás egy Stream Analytics lekérdezéssel észleli, ha egy eszköz 120 másodpercnél tovább nem küld telemetriai adatokat. A lekérdezés az eseményközpontból származó telemetriát használja bemenetként. A feladat elküldi a lekérdezés eredményeit a függvényalkalmazásnak. Ebben a szakaszban a következő feladat Stream Analytics konfigurálni:

1. A Azure Portal keresse meg a Stream Analytics feladatot, a Feladatok **topológiája** alatt válassza a **Bemenetek** lehetőséget, válassza a **+ Streambemenet** hozzáadása lehetőséget, majd válassza az **Eseményközpont lehetőséget.**
1. Az alábbi táblázatban található információk segítségével konfigurálja a bemenetet a korábban létrehozott eseményközpont használatával, majd válassza a **Mentés lehetőséget:**

    | Beállítás | Érték |
    | ------- | ----- |
    | Bemeneti alias | centraltelemetry (központi elemetria) |
    | Előfizetés | Az Ön előfizetése |
    | Event Hubs-névtér | Az Eseményközpont-névtér |
    | Eseményközpont neve | Meglévő használata – **központiexportálás** |

1. A **Jobs topology (Feladatok topológiája) alatt** válassza a **Outputs (Kimenetek)** lehetőséget, válassza **a + Add (+ Hozzáadás)** lehetőséget, majd az **Azure Function (Azure-függvény) lehetőséget.**
1. Az alábbi táblázatban található információk segítségével konfigurálja a kimenetet, majd válassza a Mentés **lehetőséget:**

    | Beállítás | Érték |
    | ------- | ----- |
    | Kimeneti alias | e-mail-értesítés |
    | Előfizetés | Az Ön előfizetése |
    | Függvényalkalmazás | A függvényalkalmazás |
    | Függvény  | HttpTrigger1 |

1. A **Feladatok topológiája alatt válassza** a Lekérdezés **lehetőséget,** és cserélje le a meglévő lekérdezést a következő SQL-kiszolgálóra:

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
1. A feladat Stream Analytics válassza az **Áttekintés,** majd a **Start**, most **,** majd a Start **lehetőséget:**

    :::image type="content" source="media/howto-create-custom-rules/stream-analytics.png" alt-text="Képernyőkép a Stream Analytics.":::

## <a name="configure-export-in-iot-central"></a>Exportálás konfigurálása a IoT Central 

Az [alkalmazáskezelő Azure IoT Central nyissa](https://aka.ms/iotcentral) meg a létrehozott IoT Central alkalmazást.

Ebben a szakaszban úgy konfigurálja az alkalmazást, hogy a szimulált eszközeiről streamelni tudja a telemetriát az eseményközpontba. Az exportálás konfigurálása:

1. Lépjen az **Adatexportál lapra,** válassza **az + Új** lehetőséget, majd kattintson a **Azure Event Hubs.**
1. Konfigurálja az exportálást a következő beállításokkal, majd válassza a **Mentés lehetőséget:** 

    | Beállítás | Érték |
    | ------- | ----- |
    | Megjelenítendő név | Exportálás Event Hubs |
    | Engedélyezve | Be |
    | Az exportálni szükséges adatok típusa | Telemetria |
    | Enrichments (Gazdagítások) | Adja meg a kívánt kulcsot / Az exportált adatok rendszerezésének kívánt értékét | 
    | Cél | Új létrehozása és az adatok exportálási helyéhez szükséges információk megszabadása |

    :::image type="content" source="media/howto-create-custom-rules/cde-configuration.png" alt-text="Képernyőkép az adatexportról.":::

A folytatás előtt várja meg, amíg az exportálás **állapota Fut.**

## <a name="test"></a>Tesztelés

A megoldás teszteléséhez letilthatja a folyamatos adatexportált a IoT Central leállított eszközökre:

1. A saját IoT Central nyissa meg az **Adatexportáció** oldalt, és válassza az **Exportálás exportálási** Event Hubs konfigurálást.
1. Állítsa az **Engedélyezve beállítást** **Ki,** majd válassza a **Mentés lehetőséget.**
1. Legalább két perc múlva a **Címzett** e-mail-cím egy vagy több olyan e-mailt kap, amely az alábbi példatartalomhoz hasonlít:

    ```txt
    The following device(s) have stopped sending telemetry:

    Device ID         Time
    Thermostat-Zone1  2019-11-01T12:45:14.686Z
    ```

## <a name="tidy-up"></a>Tidy up

Ha ezt az eljárás után szeretné rendezetten tudni, és el szeretné kerülni a szükségtelen költségeket, törölje a **DetectStoppedDevices** erőforráscsoportot a Azure Portal.

A felügyeleti IoT Central az alkalmazás **Felügyelet** lapján törölheti.

## <a name="next-steps"></a>Következő lépések

Ebben az útmutatóban a következőt sajátta el:

* Streamelési telemetria egy IoT Central folyamatos *adatexportációval.*
* Hozzon létre Stream Analytics lekérdezést, amely észleli, ha egy eszköz már nem küld adatokat.
* E-mail-értesítés küldése a Azure Functions SendGrid-szolgáltatásokkal.

Most, hogy már tudja, hogyan hozhat létre egyéni szabályokat és értesítéseket, a javasolt következő lépés a Azure IoT Central [egyéni elemzésekkel való kiterjesztése.](howto-create-custom-analytics.md)
