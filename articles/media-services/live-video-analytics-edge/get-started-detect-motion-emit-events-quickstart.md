---
title: Ismerkedés a Live Video Analytics szolgáltatással IoT Edge-Azure-ban
description: Ez a rövid útmutató bemutatja, hogyan kezdheti el a IoT Edge Live Video Analytics szolgáltatást. Ismerje meg, hogyan derítheti fel a mozgást egy élő videó streamben.
ms.topic: quickstart
ms.date: 04/27/2020
ms.openlocfilehash: fde82dde7df3fa9756c145940178f35ed255ce37
ms.sourcegitcommit: 04297f0706b200af15d6d97bc6fc47788785950f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/28/2021
ms.locfileid: "98986547"
---
# <a name="quickstart-get-started---live-video-analytics-on-iot-edge"></a>Gyors útmutató: első lépések – élő videó-elemzés IoT Edge

Ez a rövid útmutató végigvezeti azon lépéseken, amelyekkel megkezdheti az élő videók elemzését IoT Edgeon. Egy Azure-beli virtuális gépet használ IoT Edge eszközként. Egy szimulált élő videó streamet is használ. 

A telepítés lépéseinek elvégzése után egy szimulált élő videó streamet futtathat egy olyan adathordozó-gráfon keresztül, amely az adott stream bármely mozgását észleli és jelenti. Az alábbi ábrán látható ábra az adott adathordozó-gráfot ábrázolja.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/analyze-live-video/motion-detection.svg" alt-text="Élő videó-elemzés a mozgásészlelés alapján":::

Az alábbi videót részletesen megtekintheti a IoT Edge élő videó-elemzéssel való ismerkedéshez:

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4Hcax]

## <a name="prerequisites"></a>Előfeltételek

* Egy aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egy fiókot ingyenesen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) , ha még nem rendelkezik ilyennel.

  > [!NOTE]
  > Szüksége lesz egy Azure-előfizetésre, amely az egyszerű szolgáltatások létrehozásához szükséges engedélyekkel rendelkezik (ez a **tulajdonosi szerepkör** ). Ha nem rendelkezik a megfelelő engedélyekkel, lépjen kapcsolatba a fiók rendszergazdájával, és adja meg a megfelelő engedélyeket.  

* A [Visual Studio Code](https://code.visualstudio.com/) a fejlesztői gépen. Győződjön meg arról, hogy rendelkezik az [Azure IoT Tools bővítménnyel](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools).
* Győződjön meg arról, hogy a fejlesztői számítógép hálózata csatlakozik a speciális üzenetsor-kezelési protokoll (AMQP) számára a kimenő forgalom 5671-as portján keresztül. Ez a telepítő lehetővé teszi az Azure IoT-eszközök számára az Azure IoT Hub való kommunikációt.

> [!TIP]
> Előfordulhat, hogy a rendszer a Docker telepítésére kéri az Azure IoT-eszközök bővítményének telepítésekor. Nyugodtan hagyja figyelmen kívül a kérdést.

## <a name="set-up-azure-resources"></a>Az Azure-erőforrások beállítása

Az oktatóanyaghoz a következő Azure-erőforrások szükségesek:

* IoT Hub
* A(z)
* Azure Media Services fiók
* Egy Linux rendszerű virtuális gép az Azure-ban, [IoT Edge futtatókörnyezettel](../../iot-edge/how-to-install-iot-edge.md) telepítve

Ebben a rövid útmutatóban azt javasoljuk, hogy az Azure-előfizetésében lévő szükséges erőforrások üzembe helyezéséhez használja az [élő videó elemzési erőforrásainak telepítési parancsfájlját](https://github.com/Azure/live-video-analytics/tree/master/edge/setup) . Ehhez kövesse az alábbi lépéseket:

1. Lépjen a [Azure Portalra](https://portal.azure.com) , és válassza a Cloud Shell ikont.
1. Ha első alkalommal használja a Cloud Shell, a rendszer kérni fogja, hogy válasszon egy előfizetést egy Storage-fiók és egy Microsoft Azure-fájlmegosztás létrehozásához. Válassza a **tároló létrehozása** lehetőséget a Cloud Shell munkamenet-információhoz tartozó Storage-fiók létrehozásához. Ez a Storage-fiók elkülönül a parancsfájl által a Azure Media Services-fiókkal való használatra létrehozott fióktól.
1. A Cloud Shell ablak bal oldalán lévő legördülő menüben válassza a **bash** lehetőséget a környezetében.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/quickstarts/env-selector.png" alt-text="Környezeti választó":::
1. Futtassa az alábbi parancsot.

    ```
    bash -c "$(curl -sL https://aka.ms/lva-edge/setup-resources-for-samples)"
    ```
    
    A szkript sikeres befejeződése után az előfizetésben szereplő összes szükséges erőforrást látnia kell. A szkript összesen 12 erőforrást állít be:
    1. **Folyamatos átviteli végpont** – ez segítséget nyújt a rögzített AMS-eszköz lejátszásában.
    1. **Virtuális gép** – ez egy virtuális gép, amely a peremhálózati eszközként fog működni.
    1. **Lemez** – ez egy olyan tárolóeszköz, amely a virtuális géphez van csatolva adathordozók és összetevők tárolásához.
    1. **Hálózati biztonsági csoport** – az Azure-beli virtuális hálózatokon lévő Azure-erőforrások felé irányuló és onnan érkező hálózati forgalom szűrésére szolgál.
    1. **Hálózati adapter** – lehetővé teszi, hogy egy Azure-beli virtuális gép kommunikáljon az internettel, az Azure-ral és más erőforrásokkal.
    1. **Megerősített kapcsolat** – ez lehetővé teszi, hogy a böngésző és a Azure Portal használatával kapcsolódjon a virtuális géphez.
    1. **Nyilvános IP-cím** – lehetővé teszi, hogy az Azure-erőforrások kommunikáljanak az internettel és a nyilvánosan elérhető Azure-szolgáltatásokkal
    1. **Virtuális hálózat** – az Azure-erőforrások, például a virtuális gépek számos típusa lehetővé teszi, hogy biztonságosan kommunikáljanak egymással, az internettel és a helyszíni hálózatokkal. További információ a [virtuális hálózatokról](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview).
    1. **IoT hub** – ez egy központi üzenetsor, amely a IoT-alkalmazás, a IoT Edge-modulok és az általa kezelt eszközök közötti kétirányú kommunikációra szolgál.
    1. **Media Service-fiók** – ez segíti az Azure-beli médiatartalmak felügyeletét és továbbítását.
    1. **Storage-fiók** – rendelkeznie kell egy elsődleges Storage-fiókkal, és tetszőleges számú másodlagos Storage-fiók társítható a Media Services-fiókjához. További információ: [Azure Storage-fiókok Azure Media Services fiókokkal](https://docs.microsoft.com/azure/media-services/latest/storage-account-concept).
    1. **Container Registry** – ez segít a privát Docker-tárolók rendszerképeinek és a kapcsolódó összetevők tárolásában és kezelésében.

A szkript kimenetében az erőforrások táblázata felsorolja az IoT hub nevét. Keresse meg az erőforrás típusát **`Microsoft.Devices/IotHubs`** , és jegyezze fel a nevet. Ezt a nevet a következő lépésben kell megadnia.  

> [!NOTE]
> A szkript a **_~/clouddrive/LVA-Sample/_* _ könyvtárban is létrehoz néhány konfigurációs fájlt. Ezekre a fájlokra később szükség lesz a rövid útmutatóban.

> [!TIP]
> Ha a létrehozott Azure-erőforrásokkal kapcsolatos problémákba ütközik, tekintse meg az _ *[hibaelhárítási útmutatót](troubleshoot-how-to.md#common-error-resolutions)** a gyakran előforduló problémák megoldásához.

## <a name="deploy-modules-on-your-edge-device"></a>Modulok üzembe helyezése a peremhálózati eszközön

Futtassa a következő parancsot a Cloud Shellról.

```
az iot edge set-modules --hub-name <iot-hub-name> --device-id lva-sample-device --content ~/clouddrive/lva-sample/edge-deployment/deployment.amd64.json
```

Ez a parancs a következő modulokat telepíti a peremhálózati eszközre, amely ebben az esetben a Linux rendszerű virtuális gép.

* Élő videó-elemzés a IoT Edgeon (modul neve `lvaEdge` )
* Real-Time Streaming Protocol (RTSP) szimulátor (modul neve `rtspsim` )

Az RTSP-szimulátor modul egy élő videó streamet szimulál egy olyan videofájl használatával, amelyet a peremhálózati eszközre másoltak, amikor futtatta az [élő videó elemzési erőforrásainak telepítési parancsfájlját](https://github.com/Azure/live-video-analytics/tree/master/edge/setup). 

A modulok üzembe helyezése mostantól megtörtént, de az adathordozó-diagramok nem aktívak.

## <a name="configure-the-azure-iot-tools-extension"></a>Az Azure IoT-eszközök bővítményének konfigurálása

Kövesse ezeket az utasításokat az IoT hubhoz való kapcsolódáshoz az Azure IoT Tools bővítmény használatával.

1. A Visual Studio Code-ban nyissa meg a **bővítmények** lapot (vagy nyomja le a CTRL + SHIFT + X billentyűkombinációt), és keressen rá az Azure IoT hubra.
1. Kattintson a jobb gombbal, és válassza a **bővítmény beállításai** lehetőséget.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/run-program/extensions-tab.png" alt-text="Bővítmény beállításai":::
1. Keresse meg és engedélyezze a "részletes üzenet megjelenítése" lehetőséget.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/run-program/show-verbose-message.png" alt-text="Részletes üzenet megjelenítése":::
1. Válassza a **View**  >  **Explorer** lehetőséget. Vagy válassza a CTRL + SHIFT + E billentyűkombinációt.
1. Az **Explorer** lap bal alsó sarkában válassza az **Azure IoT hub** elemet.
1. A helyi menü megjelenítéséhez kattintson a **További beállítások** ikonra. Ezután válassza a **IoT hub a kapcsolatok karakterláncának beállítása** lehetőséget.
1. Amikor megjelenik egy beviteli mező, adja meg IoT Hub kapcsolódási karakterláncát. Cloud Shell a (z) *~/clouddrive/lva-sample/appsettings.js*.

> [!NOTE]
> Előfordulhat, hogy a rendszer megkéri, hogy adjon meg egy beépített végponti információt a IoT Hub számára. Az információk lekéréséhez Azure Portalban navigáljon a IoT Hub, és keresse meg a **beépített végpontok** lehetőséget a bal oldali navigációs panelen. Kattintson ide, és az Event hub-kompatibilis **végpont** szakaszban keresse meg az **Event hub-kompatibilis végpontot** . Másolja ki és használja a szövegmezőben található szöveget. A végpont így fog kinézni:  
    ```
    Endpoint=sb://iothub-ns-xxx.servicebus.windows.net/;SharedAccessKeyName=iothubowner;SharedAccessKey=XXX;EntityPath=<IoT Hub name>
    ```

Ha a kapcsolatok sikeresek, megjelenik az Edge-eszközök listája. Legalább egy **LVA-Sample-Device** nevű eszközt kell látnia. Mostantól kezelheti IoT Edge eszközeit, és a helyi menüben használhatja az Azure IoT Hubt. A peremhálózati eszközön üzembe helyezett modulok megtekintéséhez a **LVA – minta-eszköz** területen bontsa ki a **modulok** csomópontot.

![LVA – minta – eszköz csomópont](./media/quickstarts/lva-sample-device-node.png)

> [!TIP]
> Ha a [Live Video Analytics szolgáltatást manuálisan telepítette IoT Edge](deploy-iot-edge-device.md) magatokat egy peremhálózati eszközön (például egy ARM64-eszközön), akkor a modul az Azure IoT hub alatt jelenik meg az eszköz alatt. Kiválaszthatja a modult, és követheti az alábbi lépéseket.

## <a name="use-direct-method-calls"></a>Közvetlen metódusú hívások használata

A modul segítségével közvetlen metódusok meghívásával elemezheti az élő videó streameket. További információ: [közvetlen módszerek az élő videók elemzéséhez IoT Edgeon](direct-methods.md). 

### <a name="invoke-graphtopologylist"></a>GraphTopologyList meghívása

A modul összes [gráf topológiájának](media-graph-concept.md#media-graph-topologies-and-instances) enumerálása:

1. A Visual Studio Code-ban kattintson a jobb gombbal a **lvaEdge** modulra, és válassza a **modul közvetlen metódusának meghívása** lehetőséget.
1. A megjelenő mezőben adja meg a *GraphTopologyList*.
1. Másolja a következő JSON-adattartalmat, majd illessze be a mezőbe. Ezután válassza ki az ENTER billentyűt.

    ```
    {
        "@apiVersion" : "2.0"
    }
    ```

    Néhány másodpercen belül a **kimeneti** ablak a következő választ jeleníti meg.

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
    
    Ez a válasz várható, mert nem jött létre gráf-topológia.
    

### <a name="invoke-graphtopologyset"></a>GraphTopologySet meghívása

Ahogy korábban is tette, `GraphTopologySet` megkezdheti a [Graph-topológia](media-graph-concept.md#media-graph-topologies-and-instances)beállítását. A következő JSON-t használja hasznos adattartalomként.

```
{
    "@apiVersion": "2.0",
    "name": "MotionDetection",
    "properties": {
        "description": "Analyzing live video to detect motion and emit events",
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
                "sensitivity": "medium",
                "inputs": [
                    {
                        "nodeName": "rtspSource"
                    }
                ]
            }
        ],
        "sinks": [
            {
                "@type": "#Microsoft.Media.MediaGraphIoTHubMessageSink",
                "name": "hubSink",
                "hubOutputName": "inferenceOutput",
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

Ez a JSON-adattartalom létrehoz egy gráf-topológiát, amely három paramétert határoz meg. Ezen paraméterek közül kettőnek van alapértelmezett értéke. A topológia egyetlen forrás (RTSP-forrás) csomóponttal, egy processzorral (mozgásérzékelő processzor) és egy fogadó (IoT Hub fogadó) csomóponttal rendelkezik.

Néhány másodpercen belül a **kimenet** ablakban a következő válasz jelenik meg.

```
[DirectMethod] Invoking Direct Method [GraphTopologySet] to [lva-sample-device/lvaEdge] ...
[DirectMethod] Response from [lva-sample-device/lvaEdge]:
{
  "status": 201,
  "payload": {
    "systemData": {
      "createdAt": "2020-05-19T07:41:34.507Z",
      "lastModifiedAt": "2020-05-19T07:41:34.507Z"
    },
    "name": "MotionDetection",
    "properties": {
      "description": "Analyzing live video to detect motion and emit events",
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
              "username": "${rtspUserName}"
            }
          }
        }
      ],
      "processors": [
        {
          "@type": "#Microsoft.Media.MediaGraphMotionDetectionProcessor",
          "sensitivity": "medium",
          "name": "motionDetection",
          "inputs": [
            {
              "nodeName": "rtspSource",
              "outputSelectors": []
            }
          ]
        }
      ],
      "sinks": [
        {
          "@type": "#Microsoft.Media.MediaGraphIoTHubMessageSink",
          "hubOutputName": "inferenceOutput",
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

A visszaadott állapot 201. Ez az állapot azt jelzi, hogy új topológia lett létrehozva. 

Próbálkozzon a következő lépésekkel:

1. Újbóli meghívása `GraphTopologySet` . A visszaadott állapotkód 200. Ez a kód azt jelzi, hogy egy meglévő topológia frissítése sikeresen megtörtént.
1. `GraphTopologySet`Ismételje meg a műveletet, de módosítsa a leíró karakterláncot. A visszaadott állapotkód 200, a leírás pedig az új értékre frissül.
1. `GraphTopologyList`Az előző szakaszban ismertetett módon hívható meg. Most láthatja a `MotionDetection` topológiát a visszaadott adattartalomban.

### <a name="invoke-graphtopologyget"></a>GraphTopologyGet meghívása

`GraphTopologyGet`A következő hasznos adatok használatával hívhat meg.

```
{
    "@apiVersion" : "2.0",
    "name" : "MotionDetection"
}
```

Néhány másodpercen belül a következő válasz jelenik meg a **kimeneti** ablakban:

```
[DirectMethod] Invoking Direct Method [GraphTopologyGet] to [lva-sample-device/lvaEdge] ...
[DirectMethod] Response from [lva-sample-device/lvaEdge]:
{
  "status": 200,
  "payload": {
    "systemData": {
      "createdAt": "2020-05-19T07:41:34.507Z",
      "lastModifiedAt": "2020-05-19T07:41:34.507Z"
    },
    "name": "MotionDetection",
    "properties": {
      "description": "Analyzing live video to detect motion and emit events",
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
              "username": "${rtspUserName}"
            }
          }
        }
      ],
      "processors": [
        {
          "@type": "#Microsoft.Media.MediaGraphMotionDetectionProcessor",
          "sensitivity": "medium",
          "name": "motionDetection",
          "inputs": [
            {
              "nodeName": "rtspSource",
              "outputSelectors": []
            }
          ]
        }
      ],
      "sinks": [
        {
          "@type": "#Microsoft.Media.MediaGraphIoTHubMessageSink",
          "hubOutputName": "inferenceOutput",
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

A válasz hasznos adataiban figyelje meg a következő adatokat:

* Az állapotkód 200, ami sikeresnek jelez.
* A hasznos adatok között szerepel az `created` időbélyegző és az időbélyegző is `lastModified` .

### <a name="invoke-graphinstanceset"></a>GraphInstanceSet meghívása

Hozzon létre egy gráf-példányt, amely az előző gráf-topológiára hivatkozik. A Graph-példányok lehetővé teszik, hogy a különböző kamerákból származó élő videó streameket ugyanazzal a gráf-topológiával elemezze. További információ: [Media Graph-topológiák és-példányok](media-graph-concept.md#media-graph-topologies-and-instances).

Hívja meg a Direct metódust a `GraphInstanceSet` következő hasznos adatok használatával.

```
{
    "@apiVersion" : "2.0",
    "name" : "Sample-Graph-1",
    "properties" : {
        "topologyName" : "MotionDetection",
        "description" : "Sample graph description",
        "parameters" : [
            { "name" : "rtspUrl", "value" : "rtsp://rtspsim:554/media/camera-300s.mkv" }
        ]
    }
}
```

Figyelje meg, hogy ez a hasznos adat:

* Meghatározza a topológia nevét ( `MotionDetection` ), amelyhez a példányt létre kell hozni.
* A (z) paraméter értékét tartalmazza `rtspUrl` , amely nem rendelkezett alapértelmezett értékkel a gráf-topológia hasznos adataiban. Ez az érték az alábbi minta videóra mutató hivatkozás:
    > [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4LTY4]
Néhány másodpercen belül a következő válasz jelenik meg a **kimeneti** ablakban:

```
[DirectMethod] Invoking Direct Method [GraphInstanceSet] to [lva-sample-device/lvaEdge] ...
[DirectMethod] Response from [lva-sample-device/lvaEdge]:
{
  "status": 201,
  "payload": {
    "name": "Sample-Graph-1",
    "properties": {
      "created": "2020-05-19T07:44:33.868Z",
      "lastModified": "2020-05-19T07:44:33.868Z",
      "state": "Inactive",
      "description": "Sample graph description",
      "topologyName": "MotionDetection",
      "parameters": [
        {
          "name": "rtspUrl",
          "value": "rtsp://rtspsim:554/media/camera-300s.mkv"
        }
      ]
    }
  }
}
```

A válasz hasznos adataiban figyelje meg, hogy:

* Az állapotkód 201, amely jelzi, hogy egy új példány lett létrehozva.
* Az állapot az `Inactive` , ami azt jelzi, hogy a Graph-példány létrejött, de nincs aktiválva. További információ: [Media Graph-állapotok](media-graph-concept.md).

Próbálkozzon a következő lépésekkel:

1. `GraphInstanceSet`Ismételje meg a műveletet ugyanazzal a hasznos adattartalommal. Figyelje meg, hogy a visszaadott állapotkód 200.
1. `GraphInstanceSet`Ismételje meg a műveletet, de használjon másik leírást. Figyelje meg a válaszban szereplő frissített leírást, amely azt jelzi, hogy a Graph-példány frissítése sikeresen megtörtént.
1. Meghívása `GraphInstanceSet` , de a név módosítása a következőre: `Sample-Graph-2` . Figyelje meg az újonnan létrehozott Graph-példányt (azaz 201-as állapotkódot) a válasz hasznos adatai között.

### <a name="invoke-graphinstanceactivate"></a>GraphInstanceActivate meghívása

Most aktiválja a Graph-példányt, hogy elindítsa az élő videó áramlását a modulon keresztül. Hívja meg a Direct metódust a `GraphInstanceActivate` következő hasznos adatok használatával.

```
{
    "@apiVersion" : "2.0",
    "name" : "Sample-Graph-1"
}
```

Néhány másodpercen belül a **kimenet** ablakban a következő válasz jelenik meg.

```
[DirectMethod] Invoking Direct Method [GraphInstanceActivate] to [lva-sample-device/lvaEdge] ...
[DirectMethod] Response from [lva-sample-device/lvaEdge]:
{
  "status": 200,
  "payload": null
}
```

Az 200-es állapotkód azt jelzi, hogy a Graph-példány sikeresen aktiválva lett.

### <a name="invoke-graphinstanceget"></a>GraphInstanceGet meghívása

Most hívja meg a közvetlen metódust `GraphInstanceGet` a következő hasznos adatok használatával.

```
 {
     "@apiVersion" : "2.0",
     "name" : "Sample-Graph-1"
 }
 ```

Néhány másodpercen belül a **kimenet** ablakban a következő válasz jelenik meg.

```
[DirectMethod] Invoking Direct Method [GraphInstanceGet] to [lva-sample-device/lvaEdge] ...
[DirectMethod] Response from [lva-sample-device/lvaEdge]:
{
  "status": 200,
  "payload": {
    "name": "Sample-Graph-1",
    "properties": {
      "created": "2020-05-19T07:44:33.868Z",
      "lastModified": "2020-05-19T07:44:33.868Z",
      "state": "Active",
      "description": "graph description",
      "topologyName": "MotionDetection",
      "parameters": [
        {
          "name": "rtspUrl",
          "value": "rtsp://rtspsim:554/media/camera-300s.mkv"
        }
      ]
    }
  }
}
```

A válasz hasznos adataiban figyelje meg a következő adatokat:

* Az állapotkód 200, ami sikeresnek jelez.
* Az állapot azt `Active` jelzi, hogy a Graph-példány már aktív.

## <a name="observe-results"></a>Eredmények megfigyelése

A létrehozott és aktivált Graph-példány a mozgásészlelési processzor csomópont használatával észleli a mozgást a beérkező élő videó streamben. Eseményeket küld a IoT Hub fogadó csomópontnak. Az események továbbítása IoT Edge hubhoz történik. 

Az eredmények megfigyeléséhez kövesse az alábbi lépéseket.

1. A Visual Studio Code-ban nyissa meg az **Explorer** ablaktáblát. A bal alsó sarokban keresse meg az **Azure IoT hub**.
2. Bontsa ki az **eszközök** csomópontot.
3. Kattintson a jobb gombbal a **LVA-Sample-Device** elemre, majd válassza a **figyelés beépített események figyelése** lehetőséget.

    ![IOT hub-események figyelésének megkezdése](./media/quickstarts/start-monitoring-iothub-events.png)
    
A **kimeneti** ablak a következő üzenetet jeleníti meg:

```
[IoTHubMonitor] [7:44:33 AM] Message received from [lva-sample-device/lvaEdge]:
{
    "body": {
    "timestamp": 143005362606360,
    "inferences": [
        {
        "type": "motion",
        "motion": {
            "box": {
            "l": 0.828452,
            "t": 0.455224,
            "w": 0.1,
            "h": 0.088889
            }
        }
        },
        {
        "type": "motion",
        "motion": {
            "box": {
            "l": 0.661088,
            "t": 0.597015,
            "w": 0.0625,
            "h": 0.051852
            }
        }
        }
    ]
    },
    "applicationProperties": {
    "topic": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/microsoft.media/mediaservices/{amsAccountName}",
    "subject": "/graphInstances/Sample-Graph-1/processors/motionDetection",
    "eventType": "Microsoft.Media.Graph.Analytics.Inference",
    "eventTime": "2020-05-19T07:45:34.404Z",
    "dataVersion": "1.0"
    }
}
```

Figyelje meg ezeket a részleteket:

* Az üzenet tartalmaz egy `body` szakaszt és egy `applicationProperties` szakaszt. További információ: [IoT hub üzenetek létrehozása és olvasása](../../iot-hub/iot-hub-devguide-messages-construct.md).
* A alkalmazásban `applicationProperties` hivatkozik arra a csomópontra, amelyben az `subject` üzenet létrejött `MediaGraph` . Ebben az esetben az üzenet a mozgásészlelési processzorról származik.
* A `applicationProperties` ben `eventType` azt jelzi, hogy ez az esemény egy elemzési esemény.
* Az `eventTime` érték az az idő, amikor az esemény bekövetkezett.
* A `body` szakasz az elemzési eseményre vonatkozó információkat tartalmaz. Ebben az esetben az esemény egy következtetési esemény, így a törzs tartalmaz és az adattartalmakat `timestamp` `inferences` .
* A `inferences` szakasz azt jelzi, hogy az a `type` `motion` . További információkat biztosít az `motion` eseményről.

Ha lehetővé teszi, hogy a Media Graph egy ideig fusson, a **kimenet** ablakban a következő üzenet jelenik meg.

```
[IoTHubMonitor] [7:47:45 AM] Message received from [lva-sample-device/lvaEdge]:
{
  "body": {
    "sdp": "SDP:\nv=0\r\no=- 1588948185746703 1 IN IP4 172.xx.xx.xx\r\ns=Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\ni=media/camera-300s.mkv\r\nt=0 0\r\na=tool:LIVE555 Streaming Media v2020.04.12\r\na=type:broadcast\r\na=control:*\r\na=range:npt=0-300.000\r\na=x-qt-text-nam:Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\na=x-qt-text-inf:media/camera-300s.mkv\r\nm=video 0 RTP/AVP 96\r\nc=IN IP4 0.0.0.0\r\nb=AS:500\r\na=rtpmap:96 H264/90000\r\na=fmtp:96 packetization-mode=1;profile-level-id=4D0029;sprop-parameter-sets={SPS}\r\na=control:track1\r\n"
  },
  "applicationProperties": {
    "dataVersion": "1.0",
    "topic": "/subscriptions/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/resourceGroups/<my-resource-group>/providers/microsoft.media/mediaservices/<ams-account-name>",
    "subject": "/graphInstances/Sample-Graph-1/sources/rtspSource",
    "eventType": "Microsoft.Media.Graph.Diagnostics.MediaSessionEstablished",
    "eventTime": "2020-05-19T07:47:45.747Z"
  }
}
```

Ebben az üzenetben a következő adatokat láthatja:

* A `applicationProperties` ben `subject` azt jelzi, hogy az üzenet a Media Graph RTSP-forrás csomópontjában lett létrehozva.
* A-ben a `applicationProperties` `eventType` azt jelzi, hogy ez az esemény a diagnosztika.
* A a `body` diagnosztikai eseménnyel kapcsolatos információkat tartalmaz. Ebben az esetben az üzenet tartalmazza a törzset, mert az esemény a következő: `MediaSessionEstablished` .

## <a name="invoke-additional-direct-methods-to-clean-up"></a>További közvetlen metódusok meghívása a tisztításhoz

Közvetlen metódusok meghívásával először inaktiválja a Graph-példányt, majd törölheti azt.

### <a name="invoke-graphinstancedeactivate"></a>GraphInstanceDeactivate meghívása

Hívja meg a Direct metódust a `GraphInstanceDeactivate` következő hasznos adatok használatával.

```
{
    "@apiVersion" : "2.0",
    "name" : "Sample-Graph-1"
}
```

Néhány másodpercen belül a következő válasz jelenik meg a **kimeneti** ablakban:

```
[DirectMethod] Invoking Direct Method [GraphInstanceDeactivate] to [lva-sample-device/lvaEdge] ...
[DirectMethod] Response from [lva-sample-device/lvaEdge]:
{
  "status": 200,
  "payload": null
}
```

Az 200-es állapotkód azt jelzi, hogy a Graph-példány inaktiválása sikeres volt.

Ezután próbálja meg meghívni a `GraphInstanceGet` jelen cikk korábbi részében jelzett módon. Figyelje meg az `state` értéket.

### <a name="invoke-graphinstancedelete"></a>GraphInstanceDelete meghívása

Hívja meg a Direct metódust a `GraphInstanceDelete` következő hasznos adatok használatával.

```
{
    "@apiVersion" : "2.0",
    "name" : "Sample-Graph-1"
}
```

Néhány másodpercen belül a következő válasz jelenik meg a **kimeneti** ablakban:

```
[DirectMethod] Invoking Direct Method [GraphInstanceDelete] to [lva-sample-device/lvaEdge] ...
[DirectMethod] Response from [lva-sample-device/lvaEdge]:
{
  "status": 200,
  "payload": null
}
```

Az 200-es állapotkód azt jelzi, hogy a Graph-példány sikeresen törölve lett.

### <a name="invoke-graphtopologydelete"></a>GraphTopologyDelete meghívása

Hívja meg a Direct metódust a `GraphTopologyDelete` következő hasznos adatok használatával.

```
{
    "@apiVersion" : "2.0",
    "name" : "MotionDetection"
}
```

Néhány másodpercen belül a **kimenet** ablakban a következő válasz jelenik meg.

```
[DirectMethod] Invoking Direct Method [GraphTopologyDelete] to [lva-sample-device/lvaEdge] ...
[DirectMethod] Response from [lva-sample-device/lvaEdge]:
{
  "status": 200,
  "payload": null
}
```

Az 200-es állapotkód azt jelzi, hogy a gráf topológiáját sikerült törölni.

Próbálkozzon a következő lépésekkel:

1. Meghívja `GraphTopologyList` és megfigyelheti, hogy a modul nem tartalmaz gráf-topológiákat.
1. `GraphInstanceList`Ugyanazzal a hasznos adattartalommal hivatkozhat `GraphTopologyList` . Figyelje meg, hogy a Graph-példányok felsorolása nem történik meg.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha nem folytatja az alkalmazás használatát, törölje az ebben a rövid útmutatóban létrehozott erőforrásokat.

## <a name="next-steps"></a>Következő lépések

* Megtudhatja, hogyan [rögzíthet videót a IoT Edge élő video Analytics használatával](continuous-video-recording-tutorial.md).
* További információ a [diagnosztikai üzenetekről](monitoring-logging.md).