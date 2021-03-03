---
title: Figyelés és naplózás – Azure
description: Ez a cikk áttekintést nyújt a IoT Edge élő videó-elemzések monitorozásáról és naplózásáról.
ms.topic: reference
ms.date: 04/27/2020
ms.openlocfilehash: e81b1e98fb30bb8876c78c8c911585f5448db8f2
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2021
ms.locfileid: "101730242"
---
# <a name="monitoring-and-logging"></a>Monitorozás és naplózás

Ebből a cikkből megtudhatja, hogyan fogadhat eseményeket a távoli monitorozáshoz a IoT Edge modul Live Video Analytics szolgáltatásában. 

Azt is megtudhatja, hogyan vezérelheti a modul által generált naplókat.

## <a name="taxonomy-of-events"></a>Az események osztályozása

Az élő videó-elemzések IoT Edge a következő besorolás szerint bocsátanak ki eseményeket vagy telemetria-adatelemzést:

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/telemetry-schema/taxonomy.png" alt-text="Az események besorolását ábrázoló diagram.":::

* Működés: egy felhasználó műveletei által vagy egy [adathordozó-gráf](media-graph-concept.md) végrehajtása során létrehozott események
   
   * Kötet: várhatóan alacsony (néhány percenként vagy akár kevesebb)
   * Angol nyelvű Példák:

      - Rögzítés elindítva (az alábbi példában látható)
      - Rögzítés leállítva
      
      ```
      {
        "body": {
          "outputType": "assetName",
          "outputLocation": "sampleAssetFromEVR-LVAEdge-20200512T233309Z"
        },
        "applicationProperties": {
          "topic": "/subscriptions/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/resourceGroups/<my-resource-group>/providers/microsoft.media/mediaservices/<ams-account-name>",
          "subject": "/graphInstances/Sample-Graph-2/sinks/assetSink",
          "eventType": "Microsoft.Media.Graph.Operational.RecordingStarted",
          "eventTime": "2020-05-12T23:33:10.392Z",
          "dataVersion": "1.0"
        }
      }
      ```
* Diagnosztika: a teljesítménnyel kapcsolatos problémák diagnosztizálását segítő események

   * Kötet: lehet magas (percenként többször)
   * Angol nyelvű Példák:
   
      - Az RTSP [SDP](https://en.wikipedia.org/wiki/Session_Description_Protocol) kapcsolatos információk (a következő példában látható) 
      - Hiányosságok a bejövő videó adatcsatornájában

      ```
      {
        "body": {
          "sdp": "SDP:\nv=0\r\no=- 1589326384077235 1 IN IP4 XXX.XX.XX.XXX\r\ns=Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\ni=media/lots_015.mkv\r\nt=0 0\r\na=tool:LIVE555 Streaming Media v2020.04.12\r\na=type:broadcast\r\na=control:*\r\na=range:npt=0-73.000\r\na=x-qt-text-nam:Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\na=x-qt-text-inf:media/lots_015.mkv\r\nm=video 0 RTP/AVP 96\r\nc=IN IP4 0.0.0.0\r\nb=AS:500\r\na=rtpmap:96 H264/90000\r\na=fmtp:96 packetization-mode=1;profile-level-id=640028;sprop-parameter-sets=XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\r\na=control:track1\r\n"
        },
        "applicationProperties": {
          "topic": "/subscriptions/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/resourceGroups/<my-resource-group>/providers/microsoft.media/mediaservices/<ams-account-name>",
          "subject": "/graphInstances/Sample-Graph-2/sources/rtspSource",
          "eventType": "Microsoft.Media.Graph.Diagnostics.MediaSessionEstablished",
          "eventTime": "2020-05-12T23:33:04.077Z",
          "dataVersion": "1.0"
        }
      }
      ```
* Elemzés: a videó elemzése részeként generált események

   * Kötet: lehet magas (több alkalommal egy perc vagy több)
   * Angol nyelvű Példák:
      
      - A rendszer a mozgást észlelte (az alábbi példában látható) 
      - Következtetés eredménye

   ```      
   {
     "body": {
       "timestamp": 143039375044290,
       "inferences": [
         {
           "type": "motion",
           "motion": {
             "box": {
               "l": 0.48954,
               "t": 0.140741,
               "w": 0.075,
               "h": 0.058824
             }
           }
         }
       ]
     },
     "applicationProperties": {
       "topic": "/subscriptions/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/resourceGroups/<my-resource-group>/providers/microsoft.media/mediaservices/<ams-account-name>",
       "subject": "/graphInstances/Sample-Graph-2/processors/md",
       "eventType": "Microsoft.Media.Graph.Analytics.Inference",
       "eventTime": "2020-05-12T23:33:09.381Z",
       "dataVersion": "1.0"
     }
   }
   ```

A modul által kibocsátott eseményeket a rendszer a [IoT Edge hubhoz](../../iot-edge/iot-edge-runtime.md#iot-edge-hub)továbbítja. Innen más célhelyekre is irányíthatók. 

### <a name="timestamps-in-analytic-events"></a>Az analitikus események időbélyegei

Amint azt korábban említettük, a videó elemzése részeként generált események időbélyeggel vannak társítva. Ha a gráf topológiájának részeként [rögzítette az élő videót](video-recording-concept.md) , ezek az időbélyegek segítenek megkeresni, hogy a rögzített videó hol található az adott esemény bekövetkezésekor. Az alábbi útmutatást követve megtudhatja, hogyan képezhető le egy elemzési esemény időbélyege egy [Azure Media Services](terminology.md#asset)objektumba rögzített videó idővonalára.

Először bontsa ki az `eventTime` értéket. Használja ezt az értéket egy [időtartomány-szűrőben](playback-recordings-how-to.md#time-range-filters) a rögzítés megfelelő részének lekéréséhez. Előfordulhat például, hogy olyan videót szeretne beolvasni, amely 30 másodpercet indít el az előtt `eventTime` , és 30 másodperccel később ér véget. Az előző példában, ahol a `eventTime` a 2020-05-12T23:33:09.381 z, egy HLS-jegyzékfájl iránti kérelem a 30 másodperc előtt és után a `eventTime` következőhöz hasonló:

```
https://{hostname-here}/{locatorGUID}/content.ism/manifest(format=m3u8-aapl,startTime=2020-05-12T23:32:39Z,endTime=2020-05-12T23:33:39Z).m3u8
```

Az előző URL-cím egy [fő listát](https://developer.apple.com/documentation/http_live_streaming/example_playlists_for_http_live_streaming) ad vissza, amely a média-lejátszási listák URL-címét tartalmazza. A média lista a következőhöz hasonló bejegyzéseket tartalmaz:

```
...
#EXTINF:3.103011,no-desc
Fragments(video=143039375031270,format=m3u8-aapl)
...
```
Az előző bejegyzés azt jelzi, hogy egy videó töredék elérhető, amely a `timestamp` értékével kezdődik `143039375031270` . Az `timestamp` analitikai eseményben szereplő érték ugyanazt az időkeretet használja, mint a média-lejátszási lista. Felhasználható a kapcsolódó videó részletének azonosítására és a megfelelő keretre való rákeresésre.

További információkért tekintse meg ezeket [a cikkeket a HLS-ben való pontos kereséshez](https://www.bing.com/search?q=frame+accurate+seeking+in+HLS) .

## <a name="controlling-events"></a>Az események irányítása

A következő modul két tulajdonságával szabályozhatja az élő videó Analytics által közzétett operatív és diagnosztikai eseményeket IoT Edge modulon. Ezek a tulajdonságok a [modul Twin JSON-sémájában](module-twin-configuration-schema.md)vannak dokumentálva.

- `diagnosticsEventsOutputName`: A modul diagnosztikai eseményeinek beszerzéséhez vegye fel ezt a tulajdonságot, és adjon meg egy értéket. Hagyja ki, vagy hagyja üresen, hogy leállítsa a modult a diagnosztikai események közzétételéhez.
   
- `operationalEventsOutputName`: A modul működési eseményeinek beszerzéséhez vegye fel ezt a tulajdonságot, és adjon meg egy értéket. Hagyja ki, vagy hagyja üresen a modul működési események közzétételének leállításához.
   
Az elemzési eseményeket olyan csomópontok hozza létre, mint a mozgásészlelési processzor vagy a HTTP-bővítmény processzora. Az IoT hub-fogadó az IoT Edge hubhoz küldi őket. 

Az [összes korábbi esemény útválasztását](../../iot-edge/module-composition.md#declare-routes) az `desired` `$edgeHub` üzembe helyezési jegyzékben található Twin modul tulajdonságának használatával szabályozhatja:

```
 "$edgeHub": {
   "properties.desired": {
     "schemaVersion": "1.0",
     "routes": {
       "moduleToHub": "FROM /messages/modules/lvaEdge/outputs/* INTO $upstream"
     },
     "storeAndForwardConfiguration": {
       "timeToLiveSecs": 7200
     }
   }
 }
```

Az előző JSON-ban a `lvaEdge` IoT Edge modul Live Video Analytics neve. Az útválasztási szabály a [deklarált útvonalakban](../../iot-edge/module-composition.md#declare-routes)definiált sémát követi.

> [!NOTE]
> Annak érdekében, hogy az elemzési események elérjék az IoT Edge hub-t, rendelkeznie kell egy IoT hub-fogadó csomóponttal, amely a mozgásérzékelő processzor-és/vagy bármely HTTP-bővítmény processzor-csomópontjának végén van.

## <a name="event-schema"></a>Eseményséma

Az események a peremhálózati eszközről származnak, és a peremhálózati vagy a felhőben is felhasználhatók. A IoT Edge élő videó-elemzés által generált események megfelelnek az Azure IoT Hub által létrehozott [streaming Messaging-mintának](../../iot-hub/iot-hub-devguide-messages-construct.md) . A minta a Rendszertulajdonságok, az alkalmazások tulajdonságai és a törzsből áll.

### <a name="summary"></a>Összefoglalás

A IoT Hubon keresztül megfigyelt minden esemény általános tulajdonságokkal rendelkezik:

|Tulajdonság   |Tulajdonság típusa| Adattípus   |Leírás|
|---|---|---|---|
|`message-id`   |rendszer |guid|  Egyedi eseményazonosító.|
|`topic`|   applicationProperty |sztring|    Azure Resource Manager a Azure Media Services-fiók elérési útja.|
|`subject`| applicationProperty |sztring|    Az eseményt kibocsátó entitás alútvonala.|
|`eventTime`|   applicationProperty|    sztring| Az esemény létrehozásának ideje.|
|`eventType`|   applicationProperty |sztring|    Eseménytípus azonosítója. (Lásd a következő szakaszt.)|
|`body`|body (Törzs)    |object|    Adott esemény adatai.|
|`dataVersion`  |applicationProperty|   sztring  |{Major}. Kisebb|

### <a name="properties"></a>Tulajdonságok

#### <a name="message-id"></a>üzenet-azonosító

Egy globálisan egyedi azonosító (GUID) az eseményhez.

#### <a name="topic"></a>témakör

A gráfhoz társított Azure Media Services fiókot jelöli.

`/subscriptions/{subId}/resourceGroups/{rgName}/providers/Microsoft.Media/mediaServices/{accountName}`

#### <a name="subject"></a>tárgy

Az eseményt kibocsátó entitás:

`/graphInstances/{graphInstanceName}`<br/>
`/graphInstances/{graphInstanceName}/sources/{sourceName}`<br/>
`/graphInstances/{graphInstanceName}/processors/{processorName}`<br/>
`/graphInstances/{graphInstanceName}/sinks/{sinkName}`

A `subject` tulajdonság lehetővé teszi az általános események leképezését a létrehozó modulba. Például egy érvénytelen RTSP-Felhasználónév vagy jelszó esetén a generált esemény a `Microsoft.Media.Graph.Diagnostics.ProtocolError` `/graphInstances/myGraph/sources/myRtspSource` csomóponton lenne.

#### <a name="event-types"></a>Eseménytípusok

Az események típusai a következő séma alapján vannak hozzárendelve a névtérhez:

`Microsoft.Media.Graph.{EventClass}.{EventType}`

#### <a name="event-classes"></a>Eseményosztályok

|Osztály neve|Leírás|
|---|---|
|Elemzés  |A tartalom elemzése részeként generált események.|
|Diagnosztika    |Események, amelyek segítenek a problémák és a teljesítmény diagnosztizálásában.|
|Működik    |Az erőforrás-művelet részeként generált események.|

Az események típusai az egyes események osztályokra jellemzőek.

Angol nyelvű Példák:

* `Microsoft.Media.Graph.Analytics.Inference`
* `Microsoft.Media.Graph.Diagnostics.AuthorizationError`
* `Microsoft.Media.Graph.Operational.GraphInstanceStarted`

### <a name="event-time"></a>Esemény időpontja

Az esemény időpontja ISO 8601 karakterláncban van formázva. Ez az esemény időpontját jelöli.

### <a name="azure-monitor-collection-via-telegraf"></a>Azure Monitor gyűjtemény a

Ezek a metrikák a IoT Edge modul Live Video Analytics szolgáltatásában lesznek jelezve:  

|Metrika neve|Típus|Címke|Leírás|
|-----------|----|-----|-----------|
|lva_active_graph_instances|Kijelző|iothub, edge_device, module_name, graph_topology|Az aktív gráfok teljes száma topológia szerint.|
|lva_received_bytes_total|Számláló|iothub, edge_device, module_name, graph_topology, graph_instance, graph_node|Egy csomópont által fogadott bájtok teljes száma. Csak az RTSP-források esetében támogatott.|
|lva_data_dropped_total|Számláló|iothub, edge_device, module_name, graph_topology, graph_instance, graph_node, data_kind|Az eldobott adatmennyiség (események, adathordozók stb.) számlálója.|

> [!NOTE]
> A [Prometheus-végpont](https://prometheus.io/docs/practices/naming/) a tároló 9600-es portjánál van kitéve. Ha az élő videó-elemzést IoT Edge "lvaEdge" modulban nevezi el, akkor elérheti a metrikákat egy GET kérelem küldésével http://lvaEdge:9600/metrics .   

A következő lépésekkel engedélyezheti a metrikák gyűjteményét az élő videó Analytics IoT Edge modulban:

1. Hozzon létre egy mappát a fejlesztői számítógépen, és nyissa meg a mappát.

1. A mappában hozzon létre egy `telegraf.toml` fájlt, amely a következő konfigurációkat tartalmazza:
    ```
    [agent]
        interval = "30s"
        omit_hostname = true

    [[inputs.prometheus]]
      metric_version = 2
      urls = ["http://edgeHub:9600/metrics", "http://edgeAgent:9600/metrics", "http://{LVA_EDGE_MODULE_NAME}:9600/metrics"]

    [[outputs.azure_monitor]]
      namespace_prefix = "lvaEdge"
      region = "westus"
      resource_id = "/subscriptions/{SUBSCRIPTON_ID}/resourceGroups/{RESOURCE_GROUP}/providers/Microsoft.Devices/IotHubs/{IOT_HUB_NAME}"
    ```
    > [!IMPORTANT]
    > Ügyeljen arra, hogy a. toml fájlban lévő változókat cserélje le. A változókat kapcsos zárójelek () szerint jelöli `{}` .

1. Ugyanebben a mappában hozzon létre egy Docker, amely a következő parancsokat tartalmazza:
    ```
        FROM telegraf:1.15.3-alpine
        COPY telegraf.toml /etc/telegraf/telegraf.conf
    ```

1. Docker CLI-parancsok használatával hozza létre a Docker-fájlt, és tegye közzé a rendszerképet az Azure Container registryben.
    
   További információ arról, hogy a Docker CLI-vel hogyan lehet leküldeni a tároló-beállításjegyzéket: [leküldéses és lekéréses Docker-rendszerképek](../../container-registry/container-registry-get-started-docker-cli.md). A Azure Container Registry kapcsolatos további információkért tekintse meg a [dokumentációt](../../container-registry/index.yml).


1. A leküldéses Azure Container Registry befejezését követően adja hozzá a következő csomópontot a telepítési jegyzékfájlhoz:
    ```
    "telegraf": 
    {
      "settings": 
        {
            "image": "{AZURE_CONTAINER_REGISTRY_LINK_TO_YOUR_TELEGRAF_IMAGE}"
        },
      "type": "docker",
      "version": "1.0",
      "status": "running",
      "restartPolicy": "always",
      "env": 
        {
            "AZURE_TENANT_ID": { "value": "{YOUR_TENANT_ID}" },
            "AZURE_CLIENT_ID": { "value": "{YOUR CLIENT_ID}" },
            "AZURE_CLIENT_SECRET": { "value": "{YOUR_CLIENT_SECRET}" }
        }
    ``` 
    > [!IMPORTANT]
    > Ne felejtse el lecserélni a jegyzékfájl változóit. A változókat kapcsos zárójelek () szerint jelöli `{}` .


   Azure Monitor az [egyszerű szolgáltatásnév használatával hitelesíthető](https://github.com/influxdata/telegraf/blob/master/plugins/outputs/azure_monitor/README.md#azure-authentication).
        
   A Azure Monitor Graf beépülő modul [számos hitelesítési módszert](https://github.com/influxdata/telegraf/blob/master/plugins/outputs/azure_monitor/README.md#azure-authentication)tesz elérhetővé. 

  1. Az egyszerű szolgáltatás hitelesítésének használatához állítsa be a következő környezeti változókat:  
     `AZURE_TENANT_ID`: A hitelesítést végző bérlőt adja meg.  
     `AZURE_CLIENT_ID`: Megadja a használni kívánt alkalmazás ügyfél-AZONOSÍTÓját.  
     `AZURE_CLIENT_SECRET`: A használni kívánt alkalmazás titkát adja meg.  
     
     >[!TIP]
     > Az egyszerű szolgáltatás a **figyelési metrikák közzétevői** szerepkörének megadására szolgál. Az egyszerű szolgáltatásnév létrehozásához és a szerepkör hozzárendeléséhez kövesse az **[egyszerű szolgáltatásnév létrehozása](../../azure-arc/data/upload-metrics-and-logs-to-azure-monitor.md?pivots=client-operating-system-macos-and-linux#create-service-principal)** című témakör lépéseit.

1. A modulok üzembe helyezése után a metrikák egyetlen névtér alatt jelennek meg Azure Monitorban. A metrikák nevei megegyeznek a Prometheus által kibocsátott jelekkel. 

   Ebben az esetben a Azure Portalban nyissa meg az IoT hubot, és válassza a **metrikák** lehetőséget a bal oldali ablaktáblán. Itt kell látnia a metrikákat.

### <a name="log-analytics-metrics-collection"></a>Log Analytics metrikák gyűjteménye
A [Prometheus-végpont](https://prometheus.io/docs/practices/naming/) és a [log Analytics](https://docs.microsoft.com/azure/azure-monitor/log-query/log-analytics-tutorial)használatával olyan metrikákat hozhatja ki és [figyelheti](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported) , mint például a felhasznált CPUPercent, a MemoryUsedPercent stb.   

> [!NOTE]
> Az alábbi konfiguráció nem gyűjt naplókat, **csak metrikákat**. A gyűjtő modul kiterjeszthető a naplók összegyűjtésére és feltöltésére is.

[![A metrikák gyűjteményét log Analytics használatával bemutató diagram.](./media/telemetry-schema/log-analytics.png)](./media/telemetry-schema/log-analytics.png#lightbox)

1. Tudnivalók a [metrikák gyűjtéséről](https://github.com/Azure/iotedge/tree/master/edge-modules/MetricsCollector)
1. Docker CLI-parancsok használatával hozza létre a [Docker-fájlt](https://github.com/Azure/iotedge/tree/master/edge-modules/MetricsCollector/docker/linux) , és tegye közzé a rendszerképet az Azure Container registryben.
    
   További információ arról, hogy a Docker CLI-vel hogyan lehet leküldeni a tároló-beállításjegyzéket: [leküldéses és lekéréses Docker-rendszerképek](../../container-registry/container-registry-get-started-docker-cli.md). A Azure Container Registry kapcsolatos további információkért tekintse meg a [dokumentációt](../../container-registry/index.yml).

1. A leküldéses Azure Container Registry befejezését követően a rendszer beszúrja a következőt az üzembe helyezési jegyzékbe:
    ```json
    "azmAgent": {
      "settings": {
        "image": "{AZURE_CONTAINER_REGISTRY_LINK_TO_YOUR_METRICS_COLLECTOR}"
      },
      "type": "docker",
      "version": "1.0",
      "status": "running",
      "restartPolicy": "always",
      "env": {
        "LogAnalyticsWorkspaceId": { "value": "{YOUR_LOG_ANALYTICS_WORKSPACE_ID}" },
        "LogAnalyticsSharedKey": { "value": "{YOUR_LOG_ANALYTICS_WORKSPACE_SECRET}" },
        "LogAnalyticsLogType": { "value": "IoTEdgeMetrics" },
        "MetricsEndpointsCSV": { "value": "http://edgeHub:9600/metrics,http://edgeAgent:9600/metrics,http://lvaEdge:9600/metrics" },
        "ScrapeFrequencyInSecs": { "value": "30 " },
        "UploadTarget": { "value": "AzureLogAnalytics" }
      }
    }
    ```
    > [!NOTE]
    > A modulok `edgeHub` `edgeAgent` és a `lvaEdge` telepítési jegyzékfájlban definiált modulok nevei. Győződjön meg arról, hogy a modulok nevei egyeznek.   

    A következő lépésekkel érheti el és állíthatja be az `LogAnalyticsWorkspaceId` `LogAnalyticsSharedKey` értékeket:
    1. Ugrás a Azure Portal
    1. Log Analytics munkaterületek keresésének megtekintése
    1. Miután megtalálta a Log Analytics munkaterületet, navigáljon a `Agents management` bal oldali navigációs ablaktáblán található lehetőségre.
    1. Megtalálhatja a munkaterület AZONOSÍTÓját és a használható titkos kulcsokat.

1. Ezután hozzon létre egy munkafüzetet a `Workbooks` bal oldali navigációs ablaktábla fülére kattintva.
1. A Kusto lekérdezési nyelv használatával az alábbi módon írhat lekérdezéseket, és beolvashatja a IoT Edge modulok által használt CPU-százalékarányt.
    ```kusto
    let cpu_metrics = IoTEdgeMetrics_CL
    | where Name_s == "edgeAgent_used_cpu_percent"
    | extend dimensions = parse_json(Tags_s)
    | extend module_name = tostring(dimensions.module_name)
    | where module_name in ("lvaEdge","yolov3","tinyyolov3")
    | summarize cpu_percent = avg(Value_d) by bin(TimeGenerated, 5s), module_name;
    cpu_metrics
    | summarize cpu_percent = sum(cpu_percent) by TimeGenerated
    | extend module_name = "Total"
    | union cpu_metrics
    ```

    [![Diagram, amely a Kusto-lekérdezés használatával jeleníti meg a metrikákat.](./media/telemetry-schema/metrics.png)](./media/telemetry-schema/metrics.png#lightbox)
## <a name="logging"></a>Naplózás

Más IoT Edge modulokhoz hasonlóan a peremhálózati eszközön is ellenőrizheti [a tároló naplóit](../../iot-edge/troubleshoot.md#check-container-logs-for-issues) . A naplókba írt adatokat a [következő modul két](module-twin-configuration-schema.md) tulajdonságának használatával konfigurálhatja:

* `logLevel`

   * Az engedélyezett értékek:,,, `Verbose` `Information` `Warning` `Error` és `None` .
   * Az alapértelmezett érték `Information`. A naplók hibaüzeneteket, figyelmeztetéseket és tájékoztató üzeneteket tartalmaznak.
   * Ha a értékre állítja a értéket `Warning` , a naplók hibaüzeneteket és figyelmeztető üzeneteket fognak tartalmazni.
   * Ha a értékre állítja a értéket `Error` , a naplók csak hibaüzeneteket fognak tartalmazni.
   * Ha a értéket adja meg `None` , a rendszer nem generál naplókat. (Ezt a konfigurációt nem javasoljuk.)
   * `Verbose`Csak akkor használja, ha a probléma diagnosztizálásához meg kell osztania a naplókat az Azure-támogatással.

* `logCategories`

   * Egy vagy több alábbi érték vesszővel tagolt listája: `Application` , `Events` , `MediaPipeline` .
   * Az alapértelmezett érték `Application, Events`.
   * `Application`: A modul magas szintű információi, például a modul indítási üzenetei, környezeti hibák és közvetlen metódus-hívások.
   * `Events`: Az ebben a cikkben korábban ismertetett összes esemény.
   * `MediaPipeline`: Alacsony szintű naplók, amelyek betekintést nyújthatnak a problémák elhárítása során, például az RTSP-kompatibilis kamerával való kapcsolatfelvételt okozó nehézségeket.
   
### <a name="generating-debug-logs"></a>Hibakeresési naplók generálása

Bizonyos esetekben az Azure-támogatás problémáinak elhárítása érdekében előfordulhat, hogy a korábban leírtnál részletesebb naplókat kell létrehoznia. A naplók létrehozásához:

1. [Kapcsolja össze a modul tárterületét az eszköz tárolóján](../../iot-edge/how-to-access-host-storage-from-module.md#link-module-storage-to-device-storage) keresztül `createOptions` . Ha a rövid útmutatókban egy [üzembe helyezési jegyzékfájlt tartalmazó sablont](https://github.com/Azure-Samples/live-video-analytics-iot-edge-csharp/blob/master/src/edge/deployment.template.json) tekint meg, akkor a következő kód jelenik meg:

   ```
   "createOptions": {
     …
     "Binds": [
       "/var/local/mediaservices/:/var/lib/azuremediaservices/"
     ]
    }
   ```

   Ez a kód lehetővé teszi, hogy az Edge-modul írási naplókat írjon az eszköz tárterületének elérési útjához `/var/local/mediaservices/` . 

 1. Adja hozzá a következő `desired` tulajdonságot a modulhoz:

    `"debugLogsDirectory": "/var/lib/azuremediaservices/debuglogs/",`

A modul mostantól bináris formában fogja írni a hibakeresési naplókat az eszköz tárterületének elérési útjára `/var/local/mediaservices/debuglogs/` . Ezeket a naplókat az Azure-támogatással is megoszthatja.

## <a name="faq"></a>GYIK

Ha kérdése van, tekintse meg a [figyelés és mérőszámok – gyakori kérdések](faq.md#monitoring-and-metrics)című témakört.

## <a name="next-steps"></a>Következő lépések

[Folyamatos videófelvétel](continuous-video-recording-tutorial.md)
