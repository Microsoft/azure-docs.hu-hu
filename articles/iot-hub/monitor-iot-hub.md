---
title: Az Azure IoT Hub figyelése
description: Itt megtudhatja, hogyan figyelheti az Azure IoT Hub
author: robinsh
ms.author: robinsh
ms.topic: conceptual
ms.service: iot-hub
ms.custom: subject-monitoring
ms.date: 11/06/2020
ms.openlocfilehash: c9e6107e981d2bae96c5d9f368c8122d3ce245f0
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "100570950"
---
# <a name="monitoring-azure-iot-hub"></a>Az Azure IoT Hub figyelése

Ha kritikus fontosságú alkalmazásokat és üzleti folyamatokat kíván használni az Azure-erőforrásokon, figyelnie kell ezeket az erőforrásokat a rendelkezésre állással, a teljesítménnyel és a művelettel kapcsolatban. Ez a cikk az Azure IoT Hub által létrehozott figyelési információkat ismerteti, valamint azt, hogy miként használhatók a Azure Monitor funkciói az adatelemzésre és a riasztásra.

## <a name="monitor-overview"></a>Figyelő áttekintése

Az egyes IoT-hubok Azure Portal **áttekintő** lapja olyan diagramokat tartalmaz, amelyek bizonyos használati metrikákat biztosítanak, például a felhasznált üzenetek számát és az IoT hub-hoz csatlakoztatott eszközök számát.

:::image type="content" source="media/monitor-iot-hub/overview-portal.png" alt-text="Alapértelmezett metrikai diagramok az IoT hub – áttekintés oldalon.":::

Vegye figyelembe, hogy az üzenetek száma 1 percenként késleltethető, és a IoT Hub szolgáltatás-infrastruktúrával kapcsolatos okok miatt előfordulhat, hogy az érték időnként a magasabb és az alacsonyabb értékek között is ugrál a frissítéskor. Ez a számláló csak az elmúlt percben felhalmozott értékek esetében lehet helytelen.

Az Áttekintés panelen megjelenő információk hasznosak, de csak kis mennyiségű figyelési adatot jelentenek az IoT hub számára. Bizonyos megfigyelési adatok gyűjtése automatikusan történik, és az IoT hub létrehozása után azonnal elérhető az elemzéshez. További típusú adatgyűjtést is engedélyezhet néhány konfigurációval.

## <a name="what-is-azure-monitor"></a>Mi az Azure Monitor?

Az Azure IoT Hub a [Azure monitor](../azure-monitor/overview.md)használatával hozza létre a figyelési adatait, amely az Azure teljes körű figyelési szolgáltatása, amely teljes körű funkciókat biztosít az Azure-erőforrások figyeléséhez más Felhőbeli és helyszíni erőforrások mellett.

A következő fogalmakat ismerteti az [Azure-erőforrások Azure Monitorával való monitorozásával foglalkozó](../azure-monitor/essentials/monitor-azure-resource.md)cikkből:

- Mi az Azure Monitor?
- A figyeléshez kapcsolódó költségek
- Az Azure-ban összegyűjtött adatok figyelése
- Adatgyűjtés konfigurálása
- Szabványos eszközök az Azure-ban a figyelési adatok elemzéséhez és riasztásához

Az alábbi részekben az Azure IoT Hub összegyűjtött adatok leírásával, valamint az adatok gyűjtésének konfigurálására és az Azure-eszközökkel történő elemzésére vonatkozó példákat ismertetünk.

## <a name="monitoring-data"></a>Adatok monitorozása

Az Azure IoT Hub ugyanolyan típusú figyelési adatokat gyűjt, mint az [Azure-erőforrások monitorozásával](../azure-monitor/essentials/monitor-azure-resource.md#monitoring-data)kapcsolatos további Azure-erőforrások.

Az Azure IoT Hub által létrehozott metrikákkal és naplókkal kapcsolatos részletes információkért lásd: az [azure IoT hub-adatreferenciájának monitorozása](monitor-iot-hub-reference.md) .

> [!IMPORTANT]
> A IoT Hub szolgáltatás által a Azure Monitor erőforrás-naplók használatával kibocsátott események nem garantáltak megbízhatónak vagy rendezettnek. Előfordulhat, hogy egyes események elvesznek, vagy kézbesítésük megtörtént. Az erőforrás-naplók nem valódi időpontot jelentenek, és több percet is igénybe vehet, hogy az események a kiválasztott célhelyre legyenek naplózva.

## <a name="collection-and-routing"></a>Gyűjtés és Útválasztás

A platform metrikáit és a tevékenység naplóját a rendszer automatikusan összegyűjti és tárolja, de a diagnosztikai beállítások segítségével más helyekre is átirányíthatja őket.

Az erőforrás-naplók gyűjtése és tárolása addig nem történik meg, amíg létre nem hozza a diagnosztikai beállításokat, és egy vagy több helyre irányítja őket.

A metrikák és naplók több helyen is átirányíthatók, többek között:
- A Azure Monitor naplók tárolása egy társított Log Analytics-munkaterületen keresztül. Ezeket a Log Analytics használatával lehet elemezni.
- Azure Storage archiválásra és offline elemzésre 
- Egy Event Hubs-végpont, ahol külső alkalmazások, például harmadik féltől származó SIEM-eszközök is olvashatók.

Azure Portal a IoT hub bal oldali paneljén a **figyelés** elemre kattintva, majd a diagnosztikai beállítások **hozzáadása** lehetőséggel **kiválaszthatja** az IoT hub által kibocsátott naplókra és platform-metrikára kiterjedő diagnosztikai beállításokat.

Az alábbi képernyőfelvételen egy diagnosztikai beállítás látható az erőforrás-napló típusának *kapcsolódási műveleteinek* és minden platform metrikájának egy log Analytics munkaterületre történő átirányításához.

:::image type="content" source="media/monitor-iot-hub/diagnostic-setting-portal.png" alt-text="Az IoT hub diagnosztikai beállítások panelje.":::

A diagnosztikai beállításoknak a Azure Portal, a CLI vagy a PowerShell használatával történő létrehozásával kapcsolatos részletes folyamatért lásd: [diagnosztikai beállítás létrehozása a platform-naplók és-metrikák összegyűjtéséhez az Azure-ban](../azure-monitor/essentials/diagnostic-settings.md) . Diagnosztikai beállítás létrehozásakor meg kell adnia, hogy a rendszer milyen típusú naplókat gyűjtsön. Az Azure IoT Hub kategóriái a [monitoring azure IoT hub adathivatkozása alatt találhatók az erőforrás-naplók](monitor-iot-hub-reference.md#resource-logs)területen.

Ha más helyszínekre IoT Hub a platform metrikáit, vegye figyelembe a következőket:

- A következő platform-mérőszámok nem exportálhatók a diagnosztikai beállítások használatával: *csatlakoztatott eszközök (előzetes verzió)* és *összes eszköz (előzetes verzió)*.

- A többdimenziós metrikák, például bizonyos [útválasztási mérőszámok](monitor-iot-hub-reference.md#routing-metrics)jelenleg egy dimenzió értékekben összesítve összesített, egydimenziós mérőszámként lesznek exportálva. További részletekért lásd: [platform metrikáinak exportálása más helyszínekre](../azure-monitor/essentials/metrics-supported.md#exporting-platform-metrics-to-other-locations).

## <a name="analyzing-metrics"></a>Mérőszámok elemzése

Az Azure IoT Hub mérőszámait más Azure-szolgáltatásokból származó metrikákkal elemezheti **a metrikák** explorerrel a **Azure monitor** menüből. Az eszköz használatával kapcsolatos részletekért lásd: az [Azure Metrikaböngésző használatának első lépései](../azure-monitor/essentials/metrics-getting-started.md) .

Azure Portal a IoT hub bal oldali paneljén a **figyelés** elemre kattintva kiválaszthatja a **metrikákat** a metrikák Explorer hatókörön belül, alapértelmezés szerint az IoT hub által kibocsátott platform mérőszámokra:

:::image type="content" source="media/monitor-iot-hub/metrics-portal.png" alt-text="Metrikák Explorer lapja egy IoT hubhoz.":::

Az Azure IoT Hub gyűjtött platform-metrikák listáját az [Azure Monitoring IoT hub adathivatkozása című témakör metrikái](monitor-iot-hub-reference.md#metrics)című részében tekintheti meg. Az összes Azure-szolgáltatáshoz összegyűjtött platform-metrikák listáját a [Azure monitor támogatott mérőszámai](../azure-monitor/essentials/metrics-supported.md)című témakörben tekintheti meg.

A darabszámú egységekben összegyűjtött IoT Hub platform metrikák esetében előfordulhat, hogy egyes összesítések nem lesznek elérhetők vagy használhatók. További információ: [támogatott összesítések a monitoring Azure IoT hub adatreferenciában](monitor-iot-hub-reference.md#supported-aggregations).

Egyes IoT Hub mérőszámok, például [útválasztási metrikák](monitor-iot-hub-reference.md#routing-metrics), többdimenziós. Ezekben a mérőszámokban [szűrőket](../azure-monitor/essentials/metrics-charts.md#filters) alkalmazhat, és egy dimenzió alapján [feloszthatja](../azure-monitor/essentials/metrics-charts.md#apply-splitting) a diagramokat.

## <a name="analyzing-logs"></a>Naplók elemzése

Azure Monitor naplókban lévő, az egyes táblákban található, egyedi tulajdonságokkal rendelkező táblázatokban tárolt adathalmazok. A táblázatok adatai Log Analytics munkaterülethez vannak társítva, és Log Analyticsban kérhetők le. Az Azure Monitor naplókról további információt a Azure Monitor dokumentációjának [Azure monitor naplók áttekintése](../azure-monitor/logs/data-platform-logs.md) című részében talál. 

Az adatok Azure Monitor naplókba való továbbításához létre kell hoznia egy diagnosztikai beállítást, amely erőforrás-naplókat vagy platform-metrikákat küld egy Log Analytics munkaterületre. További információ: [gyűjtemény és útválasztás](#collection-and-routing).

Azure Portal a IoT hub bal oldali paneljén  található **naplók** lehetőség kiválasztásával elvégezheti log Analytics lekérdezések hatókörét, alapértelmezés szerint az IoT hub Azure monitor naplóiban gyűjtött naplókhoz és metrikához.

:::image type="content" source="media/monitor-iot-hub/logs-portal.png" alt-text="Az IoT hub naplóinak lapja.":::

A Azure Monitor naplók és a Log Analytics által lekérdezhető táblák listáját a következő témakörben találhatja meg: [Azure monitor logs Tables in the monitoring Azure IoT hub adathivatkozás](monitor-iot-hub-reference.md#azure-monitor-logs-tables).

Azure Monitor összes erőforrás-naplója ugyanazokkal a mezőkkel rendelkezik, amelyeket a szolgáltatás-specifikus mezők követnek. Az általános séma [Azure monitor erőforrás-naplózási sémában](../azure-monitor/essentials/resource-logs-schema.md#top-level-common-schema)szerepel. Az Azure-IoT Hub gyűjtött erőforrás-naplók sémája és kategóriái a [monitoring azure IoT hub-adathivatkozásban](monitor-iot-hub-reference.md#resource-logs)találhatók.

A [műveletnapló](../azure-monitor/essentials/activity-log.md) egy Azure-beli platform-napló, amely betekintést nyújt az előfizetési szintű eseményekre. Megtekintheti egymástól függetlenül, vagy átirányíthatja Azure Monitor naplókba, ahol a Log Analytics használatával jóval összetettebb lekérdezéseket végezhet.  

Amikor útválasztást IoT Hub a platform metrikáit Azure Monitor naplókba, vegye figyelembe a következőket:

- A következő platform-mérőszámok nem exportálhatók a diagnosztikai beállítások használatával: *csatlakoztatott eszközök (előzetes verzió)* és *összes eszköz (előzetes verzió)*.

- A többdimenziós metrikák, például bizonyos [útválasztási mérőszámok](monitor-iot-hub-reference.md#routing-metrics)jelenleg egy dimenzió értékekben összesítve összesített, egydimenziós mérőszámként lesznek exportálva. További részletekért lásd: [platform metrikáinak exportálása más helyszínekre](../azure-monitor/essentials/metrics-supported.md#exporting-platform-metrics-to-other-locations).

A IoT Hubekkel kapcsolatos gyakori lekérdezésekért lásd: [Sample Kusto lekérdezések](#sample-kusto-queries). Log Analytics lekérdezések használatával kapcsolatos részletes információkért lásd: [Azure monitor-lekérdezések áttekintése](../azure-monitor/logs/log-query-overview.md).

### <a name="sdk-version-in-iot-hub-logs"></a>SDK-verzió a IoT Hub-naplókban

IoT Hub erőforrás-naplók néhány művelete egy `sdkVersion` tulajdonságot ad vissza az `properties` objektumban. Ezen műveletek esetében, ha egy eszköz-vagy háttér-alkalmazás az Azure IoT SDK-k egyikét használja, ez a tulajdonság a használt SDK-val, az SDK-verzióval és az SDK-t futtató platformmal kapcsolatos információkat tartalmaz. Az alábbi példa a `sdkVersion` művelethez kibocsátott tulajdonságot mutatja be [`deviceConnect`](monitor-iot-hub-reference.md#connections) a Node.js Device SDK használatakor: `"azure-iot-device/1.17.1 (node v10.16.0; Windows_NT 10.0.18363; x64)"` . Íme egy példa a .NET (C#) SDK számára kibocsátott értékre: `".NET/1.21.2 (.NET Framework 4.8.4200.0; Microsoft Windows 10.0.17763 WindowsProduct:0x00000004; X86)"` .

A következő táblázat a különböző Azure IoT SDK-k által használt SDK-nevet mutatja be:

| Az SDK neve a sdkVersion tulajdonságban | Nyelv |
|----------|----------|
| .NET | .NET (C#) |
| Microsoft. Azure. Devices | .NET (C#) Service SDK |
| Microsoft. Azure. Devices. Client | .NET (C#) eszköz SDK |
| iothubclientről | C vagy Python v1 (elavult) eszköz SDK |
| iothubserviceclient | C vagy Python v1 (elavult) Service SDK |
| Azure-IOT-Device-iothub-, | Python-eszköz SDK |
| azure-iot-device | Node.js eszköz SDK |
| azure-iothub | Node.js Service SDK |
| com. microsoft. Azure. iothub-Java-Client | Java-eszköz SDK |
| com. microsoft. Azure. iothub. Service. SDK | Java Service SDK |
| com. microsoft. Azure. SDK. IOT. IOT-Device-Client | Java-eszköz SDK |
| com. microsoft. Azure. SDK. IOT. IOT-Service-Client | Java Service SDK |
| C | Beágyazott C |
| C + (OSSimplified = Azure RTOS) | Azure RTOS |

Ha IoT Hub erőforrás-naplókon végez lekérdezéseket, kinyerheti az SDK Version tulajdonságát. A következő lekérdezés például kinyeri az SDK Version tulajdonságát (és az eszköz AZONOSÍTÓját) a kapcsolatok műveletek által visszaadott tulajdonságokból. Ez a két tulajdonság a művelet időpontjával és annak az IoT hub erőforrás-azonosítójával együtt íródik, amelyhez az eszköz csatlakozik.

```kusto
// SDK version of devices
// List of devices and their SDK versions that connect to IoT Hub
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.DEVICES" and ResourceType == "IOTHUBS"
| where Category == "Connections"
| extend parsed_json = parse_json(properties_s) 
| extend SDKVersion = tostring(parsed_json.sdkVersion) , DeviceId = tostring(parsed_json.deviceId)
| distinct DeviceId, SDKVersion, TimeGenerated, _ResourceId
```

### <a name="sample-kusto-queries"></a>Példa Kusto-lekérdezésekre

> [!IMPORTANT]
> Amikor kiválasztja a **naplók** lehetőséget az IoT hub menüjében, log Analytics megnyílik az aktuális IoT hub lekérdezési hatókörével. Ez azt jelenti, hogy a naplók lekérdezése csak az adott erőforrás adatait fogja tartalmazni. Ha olyan lekérdezést szeretne futtatni, amely más IoT hubokból vagy más Azure-szolgáltatásokból származó adatokból származó adatokkal rendelkezik, válassza a **naplók** lehetőséget a **Azure monitor** menüből. Részletekért lásd: [a naplózási lekérdezés hatóköre és időbeli tartománya Azure Monitor log Analytics](../azure-monitor/logs/scope.md) .

A következő lekérdezések segítségével figyelheti az IoT hubot.

- Csatlakozási hibák: azonosítsa az eszköz csatlakozási hibáit.

    ```kusto
    AzureDiagnostics
    | where ResourceProvider == "MICROSOFT.DEVICES" and ResourceType == "IOTHUBS"
    | where Category == "Connections" and Level == "Error"
    ```

- Szabályozási hibák: azonosíthatja azokat az eszközöket, amelyek a legtöbb kérelmet elvégezték a hibák szabályozása miatt.

    ```kusto
    AzureDiagnostics
    | where ResourceProvider == "MICROSOFT.DEVICES" and ResourceType == "IOTHUBS"
    | where ResultType == "429001"
    | extend DeviceId = tostring(parse_json(properties_s).deviceId)
    | summarize count() by DeviceId, Category, _ResourceId
    | order by count_ desc
    ```

- Halott végpontok: azonosítsa az elhalt vagy nem megfelelő állapotú végpontokat a probléma számának megjelölése alapján, valamint annak okát, hogy miért.

    ```kusto
    AzureDiagnostics
    | where ResourceProvider == "MICROSOFT.DEVICES" and ResourceType == "IOTHUBS"
    | where Category == "Routes" and OperationName in ("endpointDead", "endpointUnhealthy")
    | extend parsed_json = parse_json(properties_s)
    | extend Endpoint = tostring(parsed_json.endpointName), Reason = tostring(parsed_json.details) 
    | summarize count() by Endpoint, OperationName, Reason, _ResourceId
    | order by count_ desc
    ```

- Hiba összegzése: a hibák száma az összes művelet típus szerint.

    ```kusto
    AzureDiagnostics
    | where ResourceProvider == "MICROSOFT.DEVICES" and ResourceType == "IOTHUBS"
    | where Level == "Error"
    | summarize count() by ResultType, ResultDescription, Category, _ResourceId
    ```

- Legutóbb csatlakoztatott eszközök: azoknak az eszközöknek a listája, amelyek a megadott időszakban IoT Hub látták a kapcsolatot.

    ```kusto
    AzureDiagnostics
    | where ResourceProvider == "MICROSOFT.DEVICES" and ResourceType == "IOTHUBS"
    | where Category == "Connections" and OperationName == "deviceConnect"
    | extend DeviceId = tostring(parse_json(properties_s).deviceId)
    | summarize max(TimeGenerated) by DeviceId, _ResourceId
    ```

- Eszközök SDK-verziója: eszközök és SDK-verziók listája az eszközök kapcsolataihoz vagy az eszközről a Cloud Twin műveletekhez.

    ```kusto
    AzureDiagnostics
    | where ResourceProvider == "MICROSOFT.DEVICES" and ResourceType == "IOTHUBS"
    | where Category == "Connections" or Category == "D2CTwinOperations"
    | extend parsed_json = parse_json(properties_s)
    | extend SDKVersion = tostring(parsed_json.sdkVersion) , DeviceId = tostring(parsed_json.deviceId)
    | distinct DeviceId, SDKVersion, TimeGenerated, _ResourceId
    ```

### <a name="read-logs-from-azure-event-hubs"></a>Naplók beolvasása az Azure Event Hubs

Miután beállította az eseménynaplózást a diagnosztikai beállításokon, létrehozhat olyan alkalmazásokat, amelyek beolvasják a naplókat, így a bennük található információk alapján műveleteket hajthat végre. Ez a mintakód egy Event hub naplóit kérdezi le:

```csharp
class Program
{ 
    static string connectionString = "{your AMS eventhub endpoint connection string}";
    static string monitoringEndpointName = "{your AMS event hub endpoint name}";
    static EventHubClient eventHubClient;
    //This is the Diagnostic Settings schema
    class AzureMonitorDiagnosticLog
    {
        string time { get; set; }
        string resourceId { get; set; }
        string operationName { get; set; }
        string category { get; set; }
        string level { get; set; }
        string resultType { get; set; }
        string resultDescription { get; set; }
        string durationMs { get; set; }
        string callerIpAddress { get; set; }
        string correlationId { get; set; }
        string identity { get; set; }
        string location { get; set; }
        Dictionary<string, string> properties { get; set; }
    };

    static void Main(string[] args)
    {
        Console.WriteLine("Monitoring. Press Enter key to exit.\n");
        eventHubClient = EventHubClient.CreateFromConnectionString(connectionString, monitoringEndpointName);
        var d2cPartitions = eventHubClient.GetRuntimeInformationAsync().PartitionIds;
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
                var deserializer = new JavaScriptSerializer();
                //deserialize json data to azure monitor object
                AzureMonitorDiagnosticLog message = new JavaScriptSerializer().Deserialize<AzureMonitorDiagnosticLog>(result);
            }
        }
    }
}
```

## <a name="alerts"></a>Riasztások

Azure Monitor riasztások proaktívan értesítik Önt, ha fontos feltételek találhatók a megfigyelési adataiban. Lehetővé teszik a rendszeren észlelt problémák azonosítását és megoldását, mielőtt az ügyfelek bejelentkeznek. Riasztásokat állíthat be a [metrikák](../azure-monitor/alerts/alerts-metric-overview.md), [naplók](../azure-monitor/alerts/alerts-unified-log.md)és a [tevékenység naplójában](../azure-monitor/alerts/activity-log-alerts.md). A különböző típusú riasztásokhoz előnyök és hátrányok tartoznak.

A platform metrikái alapján létrehozott riasztási szabályok létrehozásakor vegye figyelembe, hogy a darabszámban összegyűjtött IoT Hub platform metrikák esetében előfordulhat, hogy egyes összesítések nem lesznek elérhetők vagy használhatók. További információ: [támogatott összesítések a monitoring Azure IoT hub adatreferenciában](monitor-iot-hub-reference.md#supported-aggregations).

## <a name="monitor-per-device-disconnects-with-event-grid"></a>Az eszközön lévő leválasztások figyelése Event Grid

A Azure Monitor metrikát, *csatlakoztatott eszközöket* biztosít, amelyekkel figyelheti a IoT hubhoz csatlakoztatott eszközök számát, és riasztást indíthat, ha a csatlakoztatott eszközök száma a küszöbérték alá csökken. Habár ez bizonyos helyzetekben elegendő lehet, [Azure Event Grid](../event-grid/index.yml) egy kis késésű, eszközönkénti figyelési megoldást biztosít, amellyel nyomon követheti az eszközök kapcsolatait a kritikus fontosságú eszközök és infrastruktúra számára.

A Event Grid segítségével előfizethet a IoT Hub [ **DeviceConnected** és a **DeviceDisconnected** eseményekre](iot-hub-event-grid.md#event-types) a riasztások elindításához és az eszköz kapcsolódási állapotának figyeléséhez. A Event Grid sokkal alacsonyabb az esemény késése, mint Azure Monitor, és a csatlakoztatott eszközök teljes száma helyett a figyelést eszközönkénti alapon végezheti el. Ezek a tényezők Event Grid az előnyben részesített módszert a kritikus fontosságú eszközök és infrastruktúra kapcsolatainak figyelésére. Javasoljuk, hogy a Event Grid használatával figyelje az eszközök kapcsolatait éles környezetekben.

További információ a Event Grid és Azure Monitor eszközök kapcsolatának figyeléséről: az [Azure IoT hub figyelése, diagnosztizálása és megoldásainak leválasztása](iot-hub-troubleshoot-connectivity.md).

## <a name="next-steps"></a>Következő lépések

- A [szolgáltatásnév] által létrehozott mérőszámok, naplók és egyéb fontos értékek hivatkozását lásd: az [Azure IoT hub adatreferenciájának figyelése](monitor-iot-hub-reference.md) .

- Az Azure-erőforrások figyelésével kapcsolatos részletekért lásd: az [Azure-erőforrások figyelése Azure monitorokkal](../azure-monitor/essentials/monitor-azure-resource.md) .
