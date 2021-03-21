---
title: Azure IoT Hub Operations monitoring (elavult) | Microsoft Docs
description: Az Azure IoT Hub Operations monitoring használatával valós időben figyelheti a IoT hub műveleteinek állapotát.
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 03/11/2019
ms.author: robinsh
ms.custom: amqp, devx-track-csharp
ms.openlocfilehash: 045d5693c4388c6285bc6983ac2a385ceac9f6d0
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "94408124"
---
# <a name="iot-hub-operations-monitoring-deprecated"></a>IoT Hub Operations monitoring (elavult)

A IoT Hub Operations monitoring lehetővé teszi, hogy valós időben figyelje a IoT hub műveleteinek állapotát. IoT Hub az eseményeket több különböző kategóriába tartozó műveletben követi nyomon. Ha egy vagy több kategóriából szeretne eseményeket küldeni, az IoT hub egy végpontjának feldolgozására is választhatja. A hibák figyelése és az adatmintázatok alapján összetettebb feldolgozás is beállítható.

>[!NOTE]
>**Az IoT hub Operations monitoring elavult, és a rendszer eltávolította a IoT hubból a 2019. március 10-én**. A IoT Hub működésének és állapotának figyeléséhez tekintse meg a [IoT hub figyelése](monitor-iot-hub.md)című témakört. Az elavult idővonalról további információt az [Azure IoT-megoldások monitorozása Azure monitor és Azure Resource Health](https://azure.microsoft.com/blog/monitor-your-azure-iot-solutions-with-azure-monitor-and-azure-resource-health)című témakörben talál.

A IoT Hub hat kategóriába tartozó eseményt figyeli:

* Eszköz-identitási műveletek
* Eszköz telemetria
* Felhőből az eszközre irányuló üzenetek
* Kapcsolatok
* Fájlfeltöltés
* Üzenetek útválasztása

> [!IMPORTANT]
> IoT Hub műveletek figyelése nem garantálja az események megbízható vagy rendezett kézbesítését. Az alapul szolgáló infrastruktúra IoT Hub függően előfordulhat, hogy egyes események elvesznek, vagy kézbesítésük megszakad. A műveletek figyelésével riasztásokat állíthat elő a hibák, például a sikertelen csatlakozási kísérletek vagy a nagy gyakoriságú leválasztások alapján bizonyos eszközök esetében. Az eszközök állapotának egységes tároló létrehozásához, például egy eszközhöz csatlakoztatott vagy leválasztott eszközök állapotának létrehozásához Ne támaszkodjon az Operations monitoring eseményeire. 

## <a name="how-to-enable-operations-monitoring"></a>Az Operations monitoring engedélyezése

1. Hozzon létre egy IoT hubot. Az IoT hub létrehozásával kapcsolatos utasításokat az első [lépések](quickstart-send-telemetry-dotnet.md) útmutatóban találja.

2. Nyissa meg az IoT hub paneljét. Innen kattintson az **Operations monitoring** elemre.

    ![Hozzáférési műveletek figyelésének konfigurációja a portálon](./media/iot-hub-operations-monitoring/enable-OM-1.png)

3. Válassza ki a figyelni kívánt figyelési kategóriákat, majd kattintson a **Mentés** gombra. Az események a **figyelési beállítások** részben felsorolt Event hub-kompatibilis végpontról olvashatók. A rendszer a IoT Hub végpontot hívja meg `messages/operationsmonitoringevents` .

    ![Az IoT hub Operations monitoringjának konfigurálása](./media/iot-hub-operations-monitoring/enable-OM-2.png)

> [!NOTE]
> Ha a **kapcsolatok** kategóriához tartozó **részletes** figyelés lehetőséget választja, a IoT hub további diagnosztikai üzenetek létrehozásához. Az összes többi kategória esetében a **részletes** beállítás módosítja az egyes hibaüzenetekben IoT hub információ mennyiségét.

## <a name="event-categories-and-how-to-use-them"></a>Az események kategóriái és használati módjai

Az egyes műveletek figyelési kategóriái a IoT Hub eltérő típusú interakciókat követnek, és minden figyelési kategória tartalmaz egy sémát, amely meghatározza, hogy az adott kategóriába tartozó események hogyan legyenek strukturálva.

### <a name="device-identity-operations"></a>Eszköz-identitási műveletek

Az Eszközállapot-üzemeltetési kategória azokat a hibákat követi, amelyek akkor jelentkeznek, amikor megpróbál létrehozni, frissíteni vagy törölni egy bejegyzést az IoT hub Identity registryben. A kategória követése hasznos lehet a kiépítési forgatókönyvek esetében.

```json
{
    "time": "UTC timestamp",
    "operationName": "create",
    "category": "DeviceIdentityOperations",
    "level": "Error",
    "statusCode": 4XX,
    "statusDescription": "MessageDescription",
    "deviceId": "device-ID",
    "durationMs": 1234,
    "userAgent": "userAgent",
    "sharedAccessPolicy": "accessPolicy"
}
```

### <a name="device-telemetry"></a>Eszköz telemetria

Az eszköz telemetria kategóriája nyomon követi az IoT hub-ban előforduló hibákat, és a telemetria-folyamathoz kapcsolódik. Ez a kategória olyan hibákat tartalmaz, amelyek a telemetria-események (például a szabályozás) és a telemetria-események (például a jogosulatlan olvasók) küldésekor fordulnak elő. Ez a kategória nem képes az eszközön futó kód által okozott hibák megfogására.

```json
{
    "messageSizeInBytes": 1234,
    "batching": 0,
    "protocol": "Amqp",
    "authType": "{\"scope\":\"device\",\"type\":\"sas\",\"issuer\":\"iothub\"}",
    "time": "UTC timestamp",
    "operationName": "ingress",
    "category": "DeviceTelemetry",
    "level": "Error",
    "statusCode": 4XX,
    "statusType": 4XX001,
    "statusDescription": "MessageDescription",
    "deviceId": "device-ID",
    "EventProcessedUtcTime": "UTC timestamp",
    "PartitionId": 1,
    "EventEnqueuedUtcTime": "UTC timestamp"
}
```

### <a name="cloud-to-device-commands"></a>Felhő–eszköz irányú parancsok

A felhőből az eszközre irányuló parancsok kategóriája nyomon követi az IoT-központban előforduló hibákat, és a felhőből az eszközre irányuló üzenet folyamatához kapcsolódik. Ez a kategória olyan hibákat tartalmaz, amelyek a felhőből az eszközre irányuló üzenetek (például jogosulatlan küldő) küldésére, a felhőből az eszközre irányuló üzenetek fogadására (például a kézbesítések számának meghaladta) és a felhőből az eszközre irányuló üzenetek fogadására (például a visszajelzés lejártára) vonatkozó hibák. Ez a kategória nem gyűjti az olyan eszközök hibáit, amelyek nem kezelik a felhőből az eszközre irányuló üzeneteket, ha a felhőből az eszközre irányuló üzenet kézbesítése sikeres volt.

```json
{
    "messageSizeInBytes": 1234,
    "authType": "{\"scope\":\"hub\",\"type\":\"sas\",\"issuer\":\"iothub\"}",
    "deliveryAcknowledgement": 0,
    "protocol": "Amqp",
    "time": " UTC timestamp",
    "operationName": "ingress",
    "category": "C2DCommands",
    "level": "Error",
    "statusCode": 4XX,
    "statusType": 4XX001,
    "statusDescription": "MessageDescription",
    "deviceId": "device-ID",
    "EventProcessedUtcTime": "UTC timestamp",
    "PartitionId": 1,
    "EventEnqueuedUtcTime": "UTC timestamp"
}
```

### <a name="connections"></a>Kapcsolatok

A kapcsolatok kategória nyomon követi a hibákat, amelyek akkor fordulnak elő, amikor az eszközök csatlakoznak vagy lekapcsolódnak az IoT hub-ról. Ennek a kategóriának a követése hasznos lehet a jogosulatlan kapcsolódási kísérletek azonosításához, valamint annak követéséhez, hogy a kapcsolat megszakadt-e a gyenge kapcsolatú területeken lévő eszközök esetében

```json
{
    "durationMs": 1234,
    "authType": "{\"scope\":\"hub\",\"type\":\"sas\",\"issuer\":\"iothub\"}",
    "protocol": "Amqp",
    "time": " UTC timestamp",
    "operationName": "deviceConnect",
    "category": "Connections",
    "level": "Error",
    "statusCode": 4XX,
    "statusType": 4XX001,
    "statusDescription": "MessageDescription",
    "deviceId": "device-ID"
}
```

### <a name="file-uploads"></a>Fájlfeltöltés

A fájlfeltöltés kategóriája nyomon követi az IoT-központban előforduló hibákat, és a fájlfeltöltés funkcióhoz kapcsolódik. Ez a kategória a következőket tartalmazza:

* A SAS URI-val kapcsolatos hibák, például amikor lejárnak, mielőtt egy eszköz értesíti a befejezett feltöltési központ központi telepítéséről.

* Az eszköz által jelentett feltöltések sikertelenek.

* Hibák, amelyek akkor jelentkeznek, ha egy fájl nem található a tárolóban IoT Hub értesítési üzenet létrehozásakor.

Ez a kategória nem tudja felfogni azokat a hibákat, amelyek közvetlenül bekövetkeznek, miközben az eszköz feltölt egy fájlt a tárolóba.

```json
{
    "authType": "{\"scope\":\"hub\",\"type\":\"sas\",\"issuer\":\"iothub\"}",
    "protocol": "HTTP",
    "time": " UTC timestamp",
    "operationName": "ingress",
    "category": "fileUpload",
    "level": "Error",
    "statusCode": 4XX,
    "statusType": 4XX001,
    "statusDescription": "MessageDescription",
    "deviceId": "device-ID",
    "blobUri": "http//bloburi.com",
    "durationMs": 1234
}
```

### <a name="message-routing"></a>Üzenetek útválasztása

Az üzenet-útválasztási kategória nyomon követi az üzenetek útvonalának kiértékelése és a végpont állapota során az IoT Hub által észlelt hibákat. Ez a kategória olyan eseményeket tartalmaz, mint például amikor egy szabály "nem definiált" értékre van kiértékelve, amikor IoT Hub a végpontot elhaltként jelöli meg, és a végponttól kapott esetleges hibákat. Ez a kategória nem tartalmaz konkrét hibákat az üzenetekről (például az eszközök szabályozásával kapcsolatos hibákról), amelyek az "eszköz telemetria" kategóriában vannak bejelentve.

```json
{
    "messageSizeInBytes": 1234,
    "time": "UTC timestamp",
    "operationName": "ingress",
    "category": "routes",
    "level": "Error",
    "deviceId": "device-ID",
    "messageId": "ID of message",
    "routeName": "myroute",
    "endpointName": "myendpoint",
    "details": "ExternalEndpointDisabled"
}
```

## <a name="connect-to-the-monitoring-endpoint"></a>Kapcsolódás a figyelési végponthoz

Az IoT hub figyelési végpontja egy Event hub-kompatibilis végpont. A Event Hubs használható bármely mechanizmust használhat a végpontról származó figyelési üzenetek olvasásához. Az alábbi minta egy alapszintű olvasót hoz létre, amely nem alkalmas nagy teljesítményű telepítésre. Az Event Hubs-üzenetek feldolgozásával kapcsolatos további információkért lásd [az Event Hubs használatának első lépéseit](../event-hubs/event-hubs-dotnet-standard-getstarted-send.md) ismertető oktatóanyagot.

A figyelési végponthoz való kapcsolódáshoz szükség van egy kapcsolati sztringre és a végpont nevére. A következő lépések bemutatják, hogyan keresheti meg a szükséges értékeket a portálon:

1. A portálon navigáljon a IoT Hub Resource (erőforrás) panelre.

2. Válassza az **Operations monitoring** lehetőséget, és jegyezze fel az **Event hub-kompatibilis nevet** és az **Event hub-kompatibilis végponti** értékeket:

    ![Az Event hub-kompatibilis végpont értékei](./media/iot-hub-operations-monitoring/monitoring-endpoint.png)

3. Válassza a **megosztott hozzáférési házirendek**, majd a **szolgáltatás** elemet. Jegyezze fel az **elsődleges kulcs** értékét:

    ![Szolgáltatás megosztott hozzáférési házirendjének elsődleges kulcsa](./media/iot-hub-operations-monitoring/service-key.png)

A következő C#-kódrészletet egy Visual Studio **Windows klasszikus asztali** C# konzol alkalmazásból készítettük. A projekthez telepítve van a **WindowsAzure. ServiceBus** NuGet-csomag.

* Cserélje le a kapcsolati karakterlánc helyőrzőjét egy olyan kapcsolati karakterláncra, amely az **Event hub-kompatibilis végpontot** és a szolgáltatásban korábban feljegyzett **elsődleges kulcs** értékeit használja az alábbi példában látható módon:

    ```csharp
    "Endpoint={your Event Hub-compatible endpoint};SharedAccessKeyName=service;SharedAccessKey={your service primary key value}"
    ```

* Cserélje le a figyelési végpont neve helyőrzőt az **Event hub-kompatibilis Name** értékre, amelyet korábban feljegyzett.

```csharp
class Program
{
    static string connectionString = "{your monitoring endpoint connection string}";
    static string monitoringEndpointName = "{your monitoring endpoint name}";
    static EventHubClient eventHubClient;

    static void Main(string[] args)
    {
        Console.WriteLine("Monitoring. Press Enter key to exit.\n");

        eventHubClient = EventHubClient.CreateFromConnectionString(connectionString, monitoringEndpointName);
        var d2cPartitions = eventHubClient.GetRuntimeInformation().PartitionIds;
        CancellationTokenSource cts = new CancellationTokenSource();
        var tasks = new List<Task>();

        foreach (string partition in d2cPartitions)
        {
            tasks.Add(ReceiveMessagesFromDeviceAsync(partition, cts.Token));
        }

        Console.ReadLine();
        Console.WriteLine("Exiting...");
        cts.Cancel();
        Task.WaitAll(tasks.ToArray());
    }

    private static async Task ReceiveMessagesFromDeviceAsync(string partition, CancellationToken ct)
    {
        var eventHubReceiver = eventHubClient.GetDefaultConsumerGroup().CreateReceiver(partition, DateTime.UtcNow);
        while (true)
        {
            if (ct.IsCancellationRequested)
            {
                await eventHubReceiver.CloseAsync();
                break;
            }

            EventData eventData = await eventHubReceiver.ReceiveAsync(new TimeSpan(0,0,10));

            if (eventData != null)
            {
                string data = Encoding.UTF8.GetString(eventData.GetBytes());
                Console.WriteLine("Message received. Partition: {0} Data: '{1}'", partition, data);
            }
        }
    }
}
```

## <a name="next-steps"></a>Következő lépések

A IoT Hub figyelésének Azure Monitor való további megismeréséhez lásd:

* [Az IoT Hub monitorozása](monitor-iot-hub.md)

* [Áttelepítés IoT Hub Operations monitoringból a Azure Monitorba](iot-hub-migrate-to-diagnostics-settings.md)