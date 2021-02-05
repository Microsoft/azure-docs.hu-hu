---
title: Mozgás észlelése, videó rögzítése Azure Media Services
description: Ebből a rövid útmutatóból megtudhatja, hogyan használhatja a IoT Edge élő videós elemzéseket, hogy felderítse az élő videó streamben való mozgást, és videoklipeket rögzítsen a Azure Media Services.
ms.topic: quickstart
ms.date: 04/27/2020
ms.openlocfilehash: 135e68e2630d74dace6c3a6b70bb3666f77aad89
ms.sourcegitcommit: 1f1d29378424057338b246af1975643c2875e64d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/05/2021
ms.locfileid: "99575534"
---
# <a name="quickstart-detect-motion-record-video-to-media-services"></a>Gyors útmutató: mozgás észlelése, videó rögzítése Media Services

Ez a cikk végigvezeti az élő videó-elemzések IoT Edgeon való használatának lépésein az [Event-alapú rögzítéshez](event-based-video-recording-concept.md). Egy linuxos virtuális gépet használ az Azure-ban IoT Edge eszközként és szimulált élő videó streamként. A videó streamet a rendszer a mozgó objektumok jelenlétére elemzi. Ha a rendszer mozgást észlel, az eseményeket az Azure IoT Hubba küldik el, és a videó stream megfelelő részét a rendszer adategységként rögzíti Azure Media Servicesban.

Ez a cikk a [első lépések](get-started-detect-motion-emit-events-quickstart.md)rövid útmutatóján alapul.

## <a name="prerequisites"></a>Előfeltételek

* Aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egy fiókot ingyenesen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
  > [!NOTE]
  > Szüksége lesz egy Azure-előfizetésre, amely az egyszerű szolgáltatások létrehozásához szükséges engedélyekkel rendelkezik (ez a **tulajdonosi szerepkör** ). Ha nem rendelkezik a megfelelő engedélyekkel, lépjen kapcsolatba a fiók rendszergazdájával, és adja meg a megfelelő engedélyeket. 
* [Visual Studio Code](https://code.visualstudio.com/) a gépen az [Azure IoT Tools bővítménnyel](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools).
* Ha korábban nem végezte el a [első lépések](get-started-detect-motion-emit-events-quickstart.md) rövid útmutatót, folytassa a következő lépésekkel:
    * [Az Azure-erőforrások beállítása](get-started-detect-motion-emit-events-quickstart.md#set-up-azure-resources)
    * [Modulok üzembe helyezése](get-started-detect-motion-emit-events-quickstart.md#deploy-modules-on-your-edge-device)
    * [A Visual Studio Code konfigurálása](get-started-detect-motion-emit-events-quickstart.md#configure-the-azure-iot-tools-extension)
    > [!TIP]
    > Ha a létrehozott Azure-erőforrásokkal kapcsolatos problémákba ütközik, tekintse meg a **[hibaelhárítási útmutatót](troubleshoot-how-to.md#common-error-resolutions)** a gyakran előforduló problémák megoldásához.
## <a name="review-the-sample-video"></a>A minta videó áttekintése

Az Azure-erőforrások létrehozásához szükséges lépések részeként a rendszer egy (rövid) videót másol az Azure-beli linuxos virtuális gépre, amelyet a IoT Edge eszközként használ. Ez a videofájl az oktatóanyag élő folyamának szimulálására szolgál.

Használhat egy alkalmazást, például a [VLC Playert](https://www.videolan.org/vlc/), elindíthatja, megnyomhatja `Ctrl+N` és beillesztheti [a parkoló videó minta](https://lvamedia.blob.core.windows.net/public/lots_015.mkv) hivatkozását a lejátszás megkezdéséhez. Az 5 másodperces jel körülbelül egy fehér autó halad át a parkolón.
> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4LUbN]

Az alábbi lépések elvégzése után élő videó-elemzéseket használt IoT Edgeon az autó mozgásának észleléséhez és az 5 másodperces megjelölés körüli videoklip rögzítéséhez. Az alábbi ábra a teljes folyamat vizuális ábrázolását mutatja be.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/quickstarts/topology.svg" alt-text="Esemény-alapú videó rögzítése az eszközökön a mozgási események alapján":::

## <a name="use-direct-method-calls"></a>Közvetlen metódusú hívások használata

A modul segítségével közvetlen metódusok meghívásával elemezheti az élő videó streameket. A [IoT Edge élő videó-elemzések közvetlen módszereinek](direct-methods.md) olvasása a modul által biztosított közvetlen módszerek megismeréséhez. 

1. A Visual Studio Code-ban nyissa meg a **bővítmények** lapot (vagy nyomja le a CTRL + SHIFT + X billentyűkombinációt), és keressen rá az Azure IoT hubra.
1. Kattintson a jobb gombbal, és válassza a **bővítmény beállításai** lehetőséget.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/run-program/extensions-tab.png" alt-text="Bővítmény beállításai":::
1. Keresse meg és engedélyezze a "részletes üzenet megjelenítése" lehetőséget.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/run-program/show-verbose-message.png" alt-text="Részletes üzenet megjelenítése":::

### <a name="invoke-graphtopologylist"></a>GraphTopologyList meghívása
Ez a lépés az összes [gráf-topológiát](media-graph-concept.md#media-graph-topologies-and-instances) enumerálja a modulban.

1. Kattintson a jobb gombbal a "lvaEdge" modulra, és válassza a helyi menü "a modul közvetlen metódusának meghívása" elemét.
1. Ekkor megjelenik a Visual Studio Code ablakának felső sarkában látható pop-beviteli mező. Írja be a "GraphTopologyList" kifejezést a Szerkesztés mezőbe, majd nyomja le az ENTER billentyűt.
1. Ezután másolja és illessze be az alábbi JSON-adattartalmat a szerkesztési mezőbe, majd nyomja le az ENTER billentyűt.
    
```
{
    "@apiVersion" : "2.0"
}
```

Néhány másodpercen belül megjelenik a kimeneti ablak a Visual Studio Code felugró ablakban a következő választal
    
```
[DirectMethod] Invoking Direct Method [GraphTopologyList] to [lva-sample-device/lvaEdge] ...
[DirectMethod] Response from [lva-sample-device/lvaEdge]:
{
  "status": 200,
  "payload": {
    "value": []
  }
}
```
    
A fenti válasz várható, mivel nem jött létre gráf-topológia.

### <a name="invoke-graphtopologyset"></a>GraphTopologySet meghívása

A GraphTopologyList meghívásakor megjelenő lépések használatával meghívhatja a GraphTopologySet-t, hogy a következő JSON- [t használja a](media-graph-concept.md#media-graph-topologies-and-instances) hasznos adattartalomként. Létrehoz egy "EVRtoAssetsOnMotionDetection" nevű gráf-topológiát.

```
{
    "@apiVersion": "2.0",
    "name": "EVRtoAssetsOnMotionDetection",
    "properties": {
      "description": "Event-based video recording to Assets based on motion events",
      "parameters": [
        {
            "name": "rtspUserName",
            "type": "String",
            "description": "rtsp source user name.",
            "default": "dummyUserName"
        },
        {
            "name": "rtspPassword",
            "type": "String",
            "description": "rtsp source password.",
            "default" : "dummyPassword"
        },
        {
            "name": "rtspUrl",
            "type": "String",
            "description": "rtsp Url"
        },
        {
            "name": "motionSensitivity",
            "type": "String",
            "description": "motion detection sensitivity",
            "default" : "medium"
        },
        {
            "name": "hubSinkOutputName",
            "type": "String",
            "description": "hub sink output name",
            "default" : "iothubsinkoutput"
        }                              
    ],         

      "sources": [
        {
          "@type": "#Microsoft.Media.MediaGraphRtspSource",
          "name": "rtspSource",
          "endpoint": {
            "@type": "#Microsoft.Media.MediaGraphUnsecuredEndpoint",
            "url": "${rtspUrl}",
            "credentials": {
              "@type": "#Microsoft.Media.MediaGraphUsernamePasswordCredentials",
              "username": "${rtspUserName}",
              "password": "${rtspPassword}"
            }
          }
        }
      ],
      "processors": [
        {
          "@type": "#Microsoft.Media.MediaGraphMotionDetectionProcessor",
          "name": "motionDetection",
          "sensitivity": "${motionSensitivity}",
          "inputs": [
            {
              "nodeName": "rtspSource"
            }
          ]
        },
        {
          "@type": "#Microsoft.Media.MediaGraphSignalGateProcessor",
          "name": "signalGateProcessor",
          "inputs": [
            {
              "nodeName": "motionDetection"
            },
            {
              "nodeName": "rtspSource"
            }
          ],
          "activationEvaluationWindow": "PT1S",
          "activationSignalOffset": "PT0S",
          "minimumActivationTime": "PT30S",
          "maximumActivationTime": "PT30S"
        }
      ],
      "sinks": [
        {
          "@type": "#Microsoft.Media.MediaGraphAssetSink",
          "name": "assetSink",
          "assetNamePattern": "sampleAssetFromEVR-LVAEdge-${System.DateTime}",
          "segmentLength": "PT0M30S",
          "localMediaCacheMaximumSizeMiB": "2048",
          "localMediaCachePath": "/var/lib/azuremediaservices/tmp/",
          "inputs": [
            {
              "nodeName": "signalGateProcessor"
            }
          ]
        },
        {
          "@type": "#Microsoft.Media.MediaGraphIoTHubMessageSink",
          "name": "hubSink",
          "hubOutputName": "${hubSinkOutputName}",
          "inputs": [
            {
              "nodeName": "motionDetection"
            }
          ]
        }
      ]
    }
}
```

A fenti JSON-adattartalom egy olyan gráf-topológia létrehozását eredményezi, amely öt paramétert határoz meg (amelyek közül négy alapértelmezett értékkel rendelkezik). A topológia egyetlen forrás csomóponttal ([RTSP-forrással](media-graph-concept.md#rtsp-source)), két processzor-csomóponttal (a[mozgásérzékelő processzorral](media-graph-concept.md#motion-detection-processor) és a [Signal Gate processzorral](media-graph-concept.md#signal-gate-processor), valamint két fogadó csomóponttal (IoT hub fogadóval és adatfogadóval [) rendelkezik](media-graph-concept.md#asset-sink). A topológia vizuális ábrázolása a fent látható.

Néhány másodpercen belül a **kimenet** ablakban a következő válasz jelenik meg.

```
[DirectMethod] Invoking Direct Method [GraphTopologySet] to [lva-sample-device/lvaEdge] ...
[DirectMethod] Response from [lva-sample-device/lvaEdge]:
{
  "status": 201,
  "payload": {
    "systemData": {
      "createdAt": "2020-05-12T22:05:31.603Z",
      "lastModifiedAt": "2020-05-12T22:05:31.603Z"
    },
    "name": "EVRtoAssetsOnMotionDetection",
    "properties": {
      "description": "Event-based video recording to assets based on motion events",
      "parameters": [
        {
          "name": "rtspUserName",
          "type": "String",
          "description": "rtsp source user name.",
          "default": "dummyUserName"
        },
        {
          "name": "rtspPassword",
          "type": "String",
          "description": "rtsp source password.",
          "default": "dummyPassword"
        },
        {
          "name": "rtspUrl",
          "type": "String",
          "description": "rtsp Url"
        },
        {
          "name": "motionSensitivity",
          "type": "String",
          "description": "motion detection sensitivity",
          "default": "medium"
        },
        {
          "name": "hubSinkOutputName",
          "type": "String",
          "description": "hub sink output name",
          "default": "iothubsinkoutput"
        }
      ],
      "sources": [
        {
          "@type": "#Microsoft.Media.MediaGraphRtspSource",
          "name": "rtspSource",
          "transport": "Tcp",
          "endpoint": {
            "@type": "#Microsoft.Media.MediaGraphUnsecuredEndpoint",
            "url": "${rtspUrl}",
            "credentials": {
              "@type": "#Microsoft.Media.MediaGraphUsernamePasswordCredentials",
              "username": "${rtspUserName}",
              "password": "${rtspPassword}"
            }
          }
        }
      ],
      "processors": [
        {
          "@type": "#Microsoft.Media.MediaGraphMotionDetectionProcessor",
          "sensitivity": "${motionSensitivity}",
          "name": "motionDetection",
          "inputs": [
            {
              "nodeName": "rtspSource",
              "outputSelectors": []
            }
          ]
        },
        {
          "@type": "#Microsoft.Media.MediaGraphSignalGateProcessor",
          "activationEvaluationWindow": "PT1S",
          "activationSignalOffset": "PT0S",
          "minimumActivationTime": "PT30S",
          "maximumActivationTime": "PT30S",
          "name": "signalGateProcessor",
          "inputs": [
            {
              "nodeName": "motionDetection",
              "outputSelectors": []
            },
            {
              "nodeName": "rtspSource",
              "outputSelectors": []
            }
          ]
        }
      ],
      "sinks": [
        {
          "@type": "#Microsoft.Media.MediaGraphAssetSink",
          "localMediaCachePath": "/var/lib/azuremediaservices/tmp/",
          "localMediaCacheMaximumSizeMiB": "2048",
          "segmentLength": "PT0M30S",
          "assetNamePattern": "sampleAssetFromEVR-LVAEdge-${System.DateTime}",
          "name": "assetSink",
          "inputs": [
            {
              "nodeName": "signalGateProcessor",
              "outputSelectors": []
            }
          ]
        },
        {
          "@type": "#Microsoft.Media.MediaGraphIoTHubMessageSink",
          "hubOutputName": "${hubSinkOutputName}",
          "name": "hubSink",
          "inputs": [
            {
              "nodeName": "motionDetection",
              "outputSelectors": []
            }
          ]
        }
      ]
    }
  }
}
```

A visszaadott állapot 201, ami azt jelzi, hogy egy új gráf-topológia lett létrehozva. Próbálja ki a következő közvetlen metódusokat a következő lépések szerint:

* Indítsa újra a GraphTopologySet, és győződjön meg arról, hogy a visszaadott állapotkód 200. Az 200-as állapotkód azt jelzi, hogy egy meglévő gráf-topológia frissítése sikeresen megtörtént.
* Indítsa újra a GraphTopologySet, de módosítsa a leíró karakterláncot. Győződjön meg arról, hogy a válaszban szereplő állapotkód 200, és a Leírás frissítve lett az új értékre.
* Hívja meg a GraphTopologyList az előző szakaszban leírtak szerint, és ellenőrizze, hogy most már látható a "EVRtoAssetsOnMotionDetection" gráf-topológia a visszaadott adattartalomban.

### <a name="invoke-graphtopologyget"></a>GraphTopologyGet meghívása

Most hívja meg a GraphTopologyGet-t a következő hasznos adattartalommal
```

{
    "@apiVersion" : "2.0",
    "name" : "EVRtoAssetsOnMotionDetection"
}
```

Néhány másodpercen belül meg kell jelennie a következő válasznak a kimeneti ablakban:

```
[DirectMethod] Invoking Direct Method [GraphTopologyGet] to [lva-sample-device/lvaEdge] ...
[DirectMethod] Response from [lva-sample-device/lvaEdge]:
{
  "status": 200,
  "payload": {
    "systemData": {
      "createdAt": "2020-05-12T22:05:31.603Z",
      "lastModifiedAt": "2020-05-12T22:05:31.603Z"
    },
    "name": "EVRtoAssetsOnMotionDetection",
    "properties": {
      "description": "Event-based video recording to Assets based on motion events",
      "parameters": [
        {
          "name": "rtspUserName",
          "type": "String",
          "description": "rtsp source user name.",
          "default": "dummyUserName"
        },
        {
          "name": "rtspPassword",
          "type": "String",
          "description": "rtsp source password.",
          "default": "dummyPassword"
        },
        {
          "name": "rtspUrl",
          "type": "String",
          "description": "rtsp Url"
        },
        {
          "name": "motionSensitivity",
          "type": "String",
          "description": "motion detection sensitivity",
          "default": "medium"
        },
        {
          "name": "hubSinkOutputName",
          "type": "String",
          "description": "hub sink output name",
          "default": "iothubsinkoutput"
        }
      ],
      "sources": [
        {
          "@type": "#Microsoft.Media.MediaGraphRtspSource",
          "name": "rtspSource",
          "transport": "Tcp",
          "endpoint": {
            "@type": "#Microsoft.Media.MediaGraphUnsecuredEndpoint",
            "url": "${rtspUrl}",
            "credentials": {
              "@type": "#Microsoft.Media.MediaGraphUsernamePasswordCredentials",
              "username": "${rtspUserName}",
              "password": "${rtspPassword}"
            }
          }
        }
      ],
      "processors": [
        {
          "@type": "#Microsoft.Media.MediaGraphMotionDetectionProcessor",
          "sensitivity": "${motionSensitivity}",
          "name": "motionDetection",
          "inputs": [
            {
              "nodeName": "rtspSource",
              "outputSelectors": []
            }
          ]
        },
        {
          "@type": "#Microsoft.Media.MediaGraphSignalGateProcessor",
          "activationEvaluationWindow": "PT1S",
          "activationSignalOffset": "PT0S",
          "minimumActivationTime": "PT30S",
          "maximumActivationTime": "PT30S",
          "name": "signalGateProcessor",
          "inputs": [
            {
              "nodeName": "motionDetection",
              "outputSelectors": []
            },
            {
              "nodeName": "rtspSource",
              "outputSelectors": []
            }
          ]
        }
      ],
      "sinks": [
        {
          "@type": "#Microsoft.Media.MediaGraphAssetSink",
          "localMediaCachePath": "/var/lib/azuremediaservices/tmp/",
          "localMediaCacheMaximumSizeMiB": "2048",
          "segmentLength": "PT0M30S",
          "assetNamePattern": "sampleAssetFromEVR-LVAEdge-${System.DateTime}",
          "name": "assetSink",
          "inputs": [
            {
              "nodeName": "signalGateProcessor",
              "outputSelectors": []
            }
          ]
        },
        {
          "@type": "#Microsoft.Media.MediaGraphIoTHubMessageSink",
          "hubOutputName": "${hubSinkOutputName}",
          "name": "hubSink",
          "inputs": [
            {
              "nodeName": "motionDetection",
              "outputSelectors": []
            }
          ]
        }
      ]
    }
  }
}
```

Jegyezze fel a válasz adattartalomban a következő tulajdonságokat:

* Az állapotkód 200, ami sikeresnek jelez.
* A hasznos adatok "létrehozva" és "lastModified" időbélyegzővel rendelkeznek.

### <a name="invoke-graphinstanceset"></a>GraphInstanceSet meghívása

Ezután hozzon létre egy gráf-példányt, amely a fenti gráf-topológiára hivatkozik. Ahogy az [itt](media-graph-concept.md#media-graph-topologies-and-instances)is látható, a Graph instances lehetővé teszi, hogy több kameráról származó élő videó streameket elemezzen ugyanazzal a gráf-topológiával.

Most hívja meg a GraphInstanceSet Direct metódust a következő adattartalommal:

```
{
    "@apiVersion" : "2.0",
    "name" : "Sample-Graph-2",
    "properties" : {
        "topologyName" : "EVRtoAssetsOnMotionDetection",
        "description" : "Sample graph description",
        "parameters" : [
            { "name" : "rtspUrl", "value" : "rtsp://rtspsim:554/media/lots_015.mkv" }
        ]
    }
}
```

Vegye figyelembe a következőket:

* A fenti hasznos adat meghatározza azt a gráf-topológiát (EVRtoAssetsOnMotionDetection), amelyhez a Graph-példányt létre kell hozni.
* A hasznos adatok a "rtspUrl" paraméter értékét tartalmazzák, amely nem rendelkezett alapértelmezett értékkel a topológiai adattartalomban.

Néhány másodpercen belül a következő válasz jelenik meg a kimeneti ablakban:

```
[DirectMethod] Invoking Direct Method [GraphInstanceSet] to [lva-sample-device/lvaEdge] ...
[DirectMethod] Response from [lva-sample-device/lvaEdge]:
{
  "status": 201,
  "payload": {
    "systemData": {
      "createdAt": "2020-05-12T23:30:20.666Z",
      "lastModifiedAt": "2020-05-12T23:30:20.666Z"
    },
    "name": "Sample-Graph-2",
    "properties": {
      "state": "Inactive",
      "description": "Sample graph description",
      "topologyName": "EVRtoAssetsOnMotionDetection",
      "parameters": [
        {
          "name": "rtspUrl",
          "value": "rtsp://rtspsim:554/media/lots_015.mkv"
        }
      ]
    }
  }
}
```

Jegyezze fel a válasz adattartalomban a következő tulajdonságokat:

* Az állapotkód 201, amely azt jelzi, hogy egy új példány lett létrehozva.
* Az állapot "inaktív", ami azt jelzi, hogy a Graph-példány létrejött, de nincs aktiválva. További információ: [Media Graph](media-graph-concept.md) -állapotok.

Próbálja ki a következő közvetlen metódusokat a következő lépések szerint:

* GraphInstanceSet meghívása ugyanazzal a hasznos adattartalommal, és vegye figyelembe, hogy a visszaadott állapotkód mostantól 200.
* Ismételje meg a GraphInstanceSet újbóli meghívásával, de egy másik leírással, és jegyezze fel a válaszban szereplő frissített leírást, amely azt jelzi, hogy a Graph-példány frissítése sikerült.
* Hívja meg a GraphInstanceSet, de módosítsa a nevet a "Sample-Graph-3" névre, és figyelje meg a válasz adattartalmát. Vegye figyelembe, hogy létrejön egy új gráf-példány (azaz az állapotkód 201). Ne felejtse el törölni az ismétlődő példányokat, amikor elkészült a gyors üzembe helyezéssel.

### <a name="prepare-for-monitoring-events"></a>Felkészülés a figyelési eseményekre

A létrehozott Media Graph a mozgásészlelési processzor csomópontot használja a mozgás észlelésére, és az ilyen események továbbítása a IoT Hub. Az ilyen események megfigyelésének előkészítéséhez kövesse az alábbi lépéseket

1. Nyissa meg az Explorer panelt a Visual Studio Code-ban, és keresse meg az Azure IoT Hub a bal alsó sarokban.
1. Az eszközök csomópont kibontása
1. Kattintson a jobb gombbal a LVA-Sample-Device elemre, és válassza a "beépített figyelési események figyelése" lehetőséget.

    ![Beépített események figyelésének megkezdése](./media/quickstarts/start-monitoring-iothub-events.png)
    
    > [!NOTE]
    > Előfordulhat, hogy a rendszer megkéri, hogy adjon meg egy beépített végponti információt a IoT Hub számára. Az információk lekéréséhez Azure Portalban navigáljon a IoT Hub, és keresse meg a **beépített végpontok** lehetőséget a bal oldali navigációs panelen. Kattintson ide, és az Event hub-kompatibilis **végpont** szakaszban keresse meg az **Event hub-kompatibilis végpontot** . Másolja ki és használja a szövegmezőben található szöveget. A végpont így fog kinézni:  
        ```
        Endpoint=sb://iothub-ns-xxx.servicebus.windows.net/;SharedAccessKeyName=iothubowner;SharedAccessKey=XXX;EntityPath=<IoT Hub name>
        ```
    Néhány másodpercen belül a következő üzenet jelenik meg a kimeneti ablakban:

```
[IoTHubMonitor] Start monitoring message arrived in built-in endpoint for all devices ...
[IoTHubMonitor] Created partition receiver [0] for consumerGroup [$Default]
[IoTHubMonitor] Created partition receiver [1] for consumerGroup [$Default]
[IoTHubMonitor] Created partition receiver [2] for consumerGroup [$Default]
[IoTHubMonitor] Created partition receiver [3] for consumerGroup [$Default]
```

### <a name="invoke-graphinstanceactivate"></a>GraphInstanceActivate meghívása

Most aktiválja a Graph-példányt – amely elindítja az élő videó áramlását a modulon keresztül. Hívja meg a Direct metódus GraphInstanceActivate a következő adattartalommal:

```
{
    "@apiVersion" : "2.0",
    "name" : "Sample-Graph-2"
}
```

Néhány másodpercen belül a kimenet ablakban a következő választ kell látnia

```
[DirectMethod] Invoking Direct Method [GraphInstanceActivate] to [lva-sample-device/lvaEdge] ...
[DirectMethod] Response from [lva-sample-device/lvaEdge]:
{
  "status": 200,
  "payload": null
}
```

A válasz adattartalomban szereplő 200-as állapotkód azt jelzi, hogy a Graph-példány sikeresen aktiválva lett.

### <a name="invoke-graphinstanceget"></a>GraphInstanceGet meghívása

Most hívja meg a GraphInstanceGet Direct metódust a következő adattartalommal:

```
{
    "@apiVersion" : "2.0",
    "name" : "Sample-Graph-2"
}
```

Néhány másodpercen belül a kimenet ablakban a következő választ kell látnia

```
[DirectMethod] Invoking Direct Method [GraphInstanceGet] to [lva-sample-device/lvaEdge] ...
[DirectMethod] Response from [lva-sample-device/lvaEdge]:
{
  "status": 200,
  "payload": {
    "systemData": {
      "createdAt": "2020-05-12T23:30:20.666Z",
      "lastModifiedAt": "2020-05-12T23:30:20.666Z"
    },
    "name": "Sample-Graph-2",
    "properties": {
      "state": "Active",
      "description": "Sample graph description",
      "topologyName": "EVRtoAssetsOnMotionDetection",
      "parameters": [
        {
          "name": "rtspUrl",
          "value": "rtsp://rtspsim:554/media/lots_015.mkv"
        }
      ]
    }
  }
}
```

Jegyezze fel a válasz adattartalomban a következő tulajdonságokat:

* Az állapotkód 200, ami sikeresnek jelez.
* Az állapot "aktív", ami azt jelzi, hogy a Graph-példány már "aktív" állapotban van.

## <a name="observe-results"></a>Eredmények megfigyelése

A fenti példában létrehozott és aktivált Graph-példány a mozgásészlelési processzor csomóponttal észleli a mozgást a beérkező élő videó streamben, és eseményeket küld IoT Hub fogadónak. Ezek az események ezután továbbítva lesznek a IoT Hubba, ami mostantól megfigyelhető. A kimeneti ablakban a következő üzenetek jelennek meg

```
[IoTHubMonitor] [4:33:04 PM] Message received from [lva-sample-device/lvaEdge]:
{
  "body": {
    "sdp": "SDP:\nv=0\r\no=- 1589326384077235 1 IN IP4 XXX.XX.XX.XXX\r\ns=Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\ni=media/lots_015.mkv\r\nt=0 0\r\na=tool:LIVE555 Streaming Media v2020.04.12\r\na=type:broadcast\r\na=control:*\r\na=range:npt=0-73.000\r\na=x-qt-text-nam:Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\na=x-qt-text-inf:media/lots_015.mkv\r\nm=video 0 RTP/AVP 96\r\nc=IN IP4 0.0.0.0\r\nb=AS:500\r\na=rtpmap:96 H264/90000\r\na=fmtp:96 packetization-mode=1;profile-level-id=640028;sprop-parameter-sets=XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\r\na=control:track1\r\n"
  },
  "applicationProperties": {
    "topic": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/microsoft.media/mediaservices/{amsAccountName}",
    "subject": "/graphInstances/Sample-Graph-2/sources/rtspSource",
    "eventType": "Microsoft.Media.Graph.Diagnostics.MediaSessionEstablished",
    "eventTime": "2020-05-12T23:33:04.077Z",
    "dataVersion": "1.0"
  }
}
[IoTHubMonitor] [4:33:09 PM] Message received from [lva-sample-device/lvaEdge]:
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
    "topic": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/microsoft.media/mediaservices/{amsAccountName}",
    "subject": "/graphInstances/Sample-Graph-2/processors/md",
    "eventType": "Microsoft.Media.Graph.Analytics.Inference",
    "eventTime": "2020-05-12T23:33:09.381Z",
    "dataVersion": "1.0"
  }
}
```

Jegyezze fel a következő tulajdonságokat a fenti üzenetekben

* Minden üzenet tartalmaz egy "Body" szakaszt és egy "applicationProperties" szakaszt. Ha szeretné megismerni, hogy mit jelentenek ezek a csoportok, olvassa el [IoT hub üzenet létrehozása és olvasása](../../iot-hub/iot-hub-devguide-messages-construct.md)című cikket.
* Az első üzenet egy diagnosztikai esemény, a MediaSessionEstablished, amely azt jelzi, hogy az RTSP-forrás csomópont (tárgy) képes volt kapcsolatot létesíteni az RTSP-szimulátorral, és elkezdi fogadni a (szimulált) élő hírcsatornát.
* A "tárgy" a applicationProperties hivatkozik a Graph-topológia azon csomópontjára, amelyről az üzenet létrejött. Ebben az esetben az üzenet az RTSP forrás csomópontból származik.
* a applicationProperties "eventType" értéke azt jelzi, hogy ez egy diagnosztikai esemény.
* a "eventTime" érték azt az időpontot jelzi, amikor az esemény bekövetkezett.
* a "Body" a diagnosztikai eseményre vonatkozó információkat tartalmaz – ez a [SDP](https://en.wikipedia.org/wiki/Session_Description_Protocol) -üzenet.
* A második üzenet egy elemzési esemény. Azt is megteheti, hogy a MediaSessionEstablished-üzenet után nagyjából 5 másodperccel küldi el a rendszer, amely a videó kezdete és a parkolóban lévő autós meghajtók közötti késésnek felel meg.
* A "tárgy" a applicationProperties hivatkozik a mozgás észlelése processzor csomópontjára a gráfban, amely létrehozta ezt az üzenetet
* Az esemény egy következtetési esemény, ezért a törzs "Timestamp" és "következtetéseket" tartalmaz.
* a "következtetések" szakasz azt jelzi, hogy a "type" a "Motion", és további információkat tartalmaz a "Motion" eseményről.

A következő üzenet jelenik meg.

```
[IoTHubMonitor] [4:33:10 PM] Message received from [lva-sample-device/lvaEdge]:
{
  "body": {
    "outputType": "assetName",
    "outputLocation": "sampleAssetFromEVR-LVAEdge-20200512T233309Z"
  },
  "applicationProperties": {
    "topic": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/microsoft.media/mediaservices/{amsAccountName}",
    "subject": "/graphInstances/Sample-Graph-2/sinks/assetSink",
    "eventType": "Microsoft.Media.Graph.Operational.RecordingStarted",
    "eventTime": "2020-05-12T23:33:10.392Z",
    "dataVersion": "1.0"
  }
}
```

* A harmadik üzenet egy működési esemény. Azt is megteheti, hogy az azonnali észlelési üzenet után szinte azonnal el lesz küldve, amely a rögzítés elindítására szolgáló triggerként működött.
* A "tárgy" a applicationProperties hivatkozik a diagramban található, az üzenetet létrehozó objektum fogadó csomópontjára.
* A törzs információt tartalmaz a kimeneti helyről, amely ebben az esetben annak az Azure Media Service-eszköznek a neve, amelybe a videó rögzítve van. Jegyezze fel ezt az értéket – később a rövid útmutatóban fogja használni.

A topológiában a Signal Gate processzor csomópontja 30 másodperces aktiválási idővel lett konfigurálva, ami azt jelenti, hogy a Graph-topológia körülbelül 30 másodpercet fog rögzíteni az objektumba. A videó rögzítése közben a mozgásészlelési processzor csomópontja továbbra is kibocsátja a következtetéseket, amelyek megjelennek a kimeneti ablakban. Némi idő elteltével a következő üzenet jelenik meg.

```
[IoTHubMonitor] [4:33:31 PM] Message received from [lva-sample-device/lvaEdge]:
{
  "body": {
    "outputType": "assetName",
    "outputLocation": "sampleAssetFromEVR-LVAEdge-20200512T233309Z"
  },
  "applicationProperties": {
    "topic": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/microsoft.media/mediaservices/{amsAccountName}",
    "subject": "/graphInstances/Sample-Graph-2/sinks/assetSink",
    "eventType": "Microsoft.Media.Graph.Operational.RecordingAvailable",
    "eventTime": "2020-05-12T23:33:31.051Z",
    "dataVersion": "1.0"
  }
}
```

* Ez az üzenet szintén működési esemény. Az esemény, a RecordingAvailable azt jelzi, hogy az eszköznek elegendő adattal kell írnia, hogy a játékosok/ügyfelek a videó lejátszását kezdeményezzenek.
* A "tárgy" a applicationProperties hivatkozik a gráfban található objektum-fogadó csomópontra, amely a következő üzenetet hozta létre:
* A törzs információt tartalmaz a kimeneti helyről, amely ebben az esetben annak az Azure Media Service-eszköznek a neve, amelybe a videó rögzítve van.

Ha azt szeretné, hogy a Graph-példány továbbra is fusson, akkor ez az üzenet jelenik meg.

```
[IoTHubMonitor] [4:33:40 PM] Message received from [lva-sample-device/lvaEdge]:
{
  "body": {
    "outputType": "assetName",
    "outputLocation": "sampleAssetFromEVR-LVAEdge-20200512T233309Z"
  },
  "applicationProperties": {
    "topic": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/microsoft.media/mediaservices/{amsAccountName}",
    "subject": "/graphInstances/Sample-Graph-2/sinks/assetSink",
    "eventType": "Microsoft.Media.Graph.Operational.RecordingStopped",
    "eventTime": "2020-05-12T23:33:40.014Z",
    "dataVersion": "1.0"
  }
}
```

* Ez az üzenet szintén működési esemény. A RecordingStopped esemény azt jelzi, hogy a rögzítés leállt.
* Vegye figyelembe, hogy a RecordingStarted esemény óta körülbelül 30 másodperc telt el a Signal Gate processzor-csomópont aktiválási idejének értékével.
* A "tárgy" a applicationProperties hivatkozik a diagramban található, az üzenetet létrehozó objektum fogadó csomópontjára.
* A törzs információt tartalmaz a kimeneti helyről, amely ebben az esetben annak az Azure Media Service-eszköznek a neve, amelybe a videó rögzítve van.

Ha azt szeretné, hogy a Graph-példány továbbra is fusson, az RTSP-szimulátor eléri a videofájl végét, és leállítja/leválasztja azt. Az RTSP-forrás csomópontja újra csatlakozik a szimulátorhoz, és a folyamat megismétlődik.
    
## <a name="invoke-additional-direct-method-calls-to-clean-up"></a>További közvetlen metódusok meghívása a tisztításhoz

A közvetlen metódusok meghívásával inaktiválhatja és törölheti a Graph-példányt (ebben a sorrendben).

### <a name="invoke-graphinstancedeactivate"></a>GraphInstanceDeactivate meghívása

Hívja meg a GraphInstanceDeactivate Direct metódust a következő adattartalommal:

```
{
    "@apiVersion" : "2.0",
    "name" : "Sample-Graph-2"
}
```

Néhány másodpercen belül a kimenet ablakban kell megjelennie a következő válasznak.

```
[DirectMethod] Invoking Direct Method [GraphInstanceDeactivate] to [lva-sample-device/lvaEdge] ...
[DirectMethod] Response from [lva-sample-device/lvaEdge]:
{
  "status": 200,
  "payload": null
}
```

Az 200-es állapotkód azt jelzi, hogy a Graph-példány inaktiválása sikeres volt.

A következő lépésekkel próbálkozzon a következőkkel:

* Hívja meg a GraphInstanceGet a korábbi szakaszokban jelzett módon, és figyelje meg az "állapot" értéket.

### <a name="invoke-graphinstancedelete"></a>GraphInstanceDelete meghívása

A közvetlen metódus GraphInstanceDelete meghívása a következő adattartalommal

```
{
    "@apiVersion" : "2.0",
    "name" : "Sample-Graph-2"
}
```

Néhány másodpercen belül a következő választ kell látnia a kimeneti ablakban:

```
[DirectMethod] Invoking Direct Method [GraphInstanceDelete] to [lva-sample-device/lvaEdge] ...
[DirectMethod] Response from [lva-sample-device/lvaEdge]:
{
  "status": 200,
  "payload": null
}
```

A válaszban szereplő 200-as állapotkód azt jelzi, hogy a gráf példányának törlése sikerült.

### <a name="invoke-graphtopologydelete"></a>GraphTopologyDelete meghívása

Hívja meg a GraphTopologyDelete Direct metódust a következő adattartalommal:

```
{
    "@apiVersion" : "2.0",
    "name" : "EVRtoAssetsOnMotionDetection"
}
```

Néhány másodpercen belül a kimenet ablakban a következő választ kell látnia

```
[DirectMethod] Invoking Direct Method [GraphTopologyDelete] to [lva-sample-device/lvaEdge] ...
[DirectMethod] Response from [lva-sample-device/lvaEdge]:
{
  "status": 200,
  "payload": null
}
```

Az 200-es állapotkód azt jelzi, hogy a MediaGraph-topológiát sikerült törölni.

Próbálja ki a következő közvetlen metódusokat a következő lépések szerint:

* Hívja meg a GraphTopologyList, és figyelje meg, hogy nincsenek gráf-topológiák a modulban.
* Hívja meg a GraphInstanceList ugyanazzal a hasznos adattartalommal, mint a GraphTopologyList, és figyelje meg, hogy a rendszer nem sorolja fel a diagramokat.

## <a name="playing-back-the-recorded-video"></a>A rögzített videó lejátszása

Ezután használhatja a Azure Portal a rögzített videó lejátszásához.

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com/), és írja be a "Media Services" kifejezést a keresőmezőbe.
1. Keresse meg Azure Media Services-fiókját, és nyissa meg.
1. Keresse meg és válassza ki az eszközök bejegyzést a Media Services listájában.

    ![Az adategységek bejegyzése a Media Services listájában](./media/quickstarts/asset-entity.png)
1. Ha ez a rövid útmutató a Azure Media Services első használata, akkor csak az ebből a rövid útmutatóból generált eszközök szerepelnek a felsorolásban, és kiválaszthatja a legrégebbi elemet.
1. Más esetben használja a fenti operatív események outputLocation megadott eszköz nevét.
1. A megnyíló Részletek lapon kattintson az "új létrehozása" hivatkozásra közvetlenül a streaming URL szövegmező alatt.

    ![A folyamatos átviteli URL-cím](./media/quickstarts/asset-streaming-url.png)
1. Az "adatfolyam-kereső hozzáadása" ablakban megjelenő panelen fogadja el az alapértelmezett értékeket, és nyomja le az "add" (Hozzáadás) lehetőséget az alján.
1. Az eszköz adatai lapon a videó lejátszónak most be kell töltenie a videó első keretét, és a lejátszás gombra is kattinthat. Ellenőrizze, hogy megjelenik-e a videó azon része, ahol az autó a parkolóban található.

    ![Play](./media/quickstarts/asset-playback.png)

> [!NOTE]
> Mivel a szimulált élő videó elindul a gráf aktiválása után, a napi értékek nem relevánsak, és nem jelennek meg a lejátszó parancsikonján keresztül. A folyamatos videofelvételek és lejátszási útmutató bemutatja, hogyan jelenítheti meg az időbélyegeket.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha nem folytatja az alkalmazás használatát, törölje az ebben a rövid útmutatóban létrehozott erőforrásokat.

## <a name="next-steps"></a>Következő lépések

* Megtudhatja, hogyan hívhat meg programozott módon az élő videó-elemzéseket IoT Edge [közvetlen módszerekkel](direct-methods.md) .
* További információ a diagnosztikai üzenetekről.    
