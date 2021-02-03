---
title: Élő videók elemzése Computer Vision térbeli elemzéshez – Azure
description: Ebből az oktatóanyagból megtudhatja, hogyan használhatja a Live Video Analytics szolgáltatást az Azure Cognitive Services Computer Vision térbeli Analysis AI funkciójának használatával egy élő videó-hírcsatorna (szimulált) IP-kamerából való elemzéséhez.
ms.topic: tutorial
ms.date: 09/08/2020
ms.openlocfilehash: f8a828d27b69fedefe7ccbc2ad7290bf300afc16
ms.sourcegitcommit: ea822acf5b7141d26a3776d7ed59630bf7ac9532
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/03/2021
ms.locfileid: "99526789"
---
# <a name="analyze-live-video-with-computer-vision-for-spatial-analysis-preview"></a>Élő videó elemzése a Computer Vision for térbeli Analysis (előzetes verzió)

Ebből az oktatóanyagból megtudhatja, hogyan használhatja az élő videók elemzését [Computer Vision az Cognitive Services Azure-beli térbeli elemzési AI szolgáltatáshoz](https://azure.microsoft.com/services/cognitive-services/computer-vision/) , hogy elemezze a (szimulált) IP-kamera élő videós csatornáját. Láthatja, hogy ez a következtetési kiszolgáló hogyan elemezheti a streaming videót, hogy megértse az emberek és a fizikai térben való mozgás közötti térbeli kapcsolatokat.  A rendszer elküldi a videó-hírcsatornában található keretek egy részhalmazát, és az eredményeket elküldjük IoT Edge hubhoz, és bizonyos feltételek teljesülése esetén a videoklipek rögzítése és tárolása Azure Media Services eszközként történik.

Ebben az oktatóanyagban a következőket fogja elsajátítani:

> [!div class="checklist"]
> * Erőforrások beállítása.
> * Vizsgálja meg a kódot.
> * Futtassa a kódot.
> * Események figyelése.
 
[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]
  > [!NOTE]
  > Szüksége lesz egy Azure-előfizetésre, amely az egyszerű szolgáltatások létrehozásához szükséges engedélyekkel rendelkezik (ez a **tulajdonosi szerepkör** ). Ha nem rendelkezik a megfelelő engedélyekkel, lépjen kapcsolatba a fiók rendszergazdájával, és adja meg a megfelelő engedélyeket. 
## <a name="suggested-pre-reading"></a>Javasolt előzetes olvasás

A Kezdés előtt olvassa el a következő cikkeket:

* [Élő videó-elemzések IoT Edge áttekintése](overview.md)
* [Élő videó-elemzések IoT Edge terminológiában](terminology.md)
* [A Media Graph alapfogalmai](media-graph-concept.md)
* [Eseményalapú videófelvétel](event-based-video-recording-concept.md)
* [Oktatóanyag: IoT Edge modul fejlesztése](../../iot-edge/tutorial-develop-for-linux.md)
* [Élő video Analytics üzembe helyezése Azure Stack Edge-ben](deploy-azure-stack-edge-how-to.md) 

## <a name="prerequisites"></a>Előfeltételek

Az alábbiak a térbeli elemzési modul élő video Analytics-modulhoz való csatlakoztatásának előfeltételei.

* A [Visual Studio Code](https://code.visualstudio.com/) a fejlesztői gépen. Győződjön meg arról, hogy rendelkezik az [Azure IoT Tools bővítménnyel](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools).
    * Győződjön meg arról, hogy a fejlesztői számítógép hálózata csatlakozik a speciális üzenetsor-kezelési protokoll engedélyezéséhez a 5671-es porton keresztül. Ez a telepítő lehetővé teszi az Azure IoT-eszközök számára az Azure IoT Hub való kommunikációt.
* [Azure stack Edge](https://azure.microsoft.com/products/azure-stack/edge/) GPU-gyorsítással.  
    Azt javasoljuk, hogy a Azure Stack Edge-t GPU-gyorsítással használja, azonban a tároló bármely más eszközön fut egy [NVIDIA Tesla T4 GPU](https://www.nvidia.com/en-us/data-center/tesla-t4/)-val. 
* Az [Azure kognitív szolgáltatás Computer Vision tároló](https://azure.microsoft.com/services/cognitive-services/computer-vision/) a térbeli elemzéshez.  
    A tároló használatához rendelkeznie kell egy Computer Vision erőforrással a társított **API-kulcs** és egy **végponti URI** beszerzéséhez. Az API-kulcs a Azure Portal Computer Vision áttekintés és kulcsok oldalain érhető el. A tároló indításához a kulcs és a végpont szükséges.

## <a name="overview"></a>Áttekintés

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/spatial-analysis-tutorial/overview.png" alt-text="Térbeli elemzés – áttekintés":::
 
Ez az ábra az oktatóanyagban szereplő jelek folyamatát mutatja be. Az [Edge-modul](https://github.com/Azure/live-video-analytics/tree/master/utilities/rtspsim-live555) szimulál egy Real-Time Streaming Protocol-(RTSP-) kiszolgálót futtató IP-kamerát. Az [RTSP-forrás](media-graph-concept.md#rtsp-source) csomópontja lekéri a videó csatornáját a kiszolgálóról, és képkockákat küld a `MediaGraphCognitiveServicesVisionExtension` processzor-csomópontnak.

A MediaGraphCognitiveServicesVisionExtension csomópont egy proxy szerepét játssza le. A képkockákat a megadott képtípusra konvertálja. Ezt követően továbbítja a rendszerképet a **megosztott memórián** keresztül egy másik peremhálózati modulhoz, amely egy gRPC-végpont MÖGÖTTi AI-műveleteket futtat. Ebben a példában ez a peremhálózati modul a térbeli elemzési modul. A MediaGraphCognitiveServicesVisionExtension processzor csomópontja két dolgot tesz:

* Összegyűjti az eredményeket, és közzéteszi az eseményeket a [IoT hub](media-graph-concept.md#iot-hub-message-sink) fogadó csomópontban. A csomópont ezután elküldi ezeket az eseményeket [IoT Edge hubhoz](../../iot-edge/iot-edge-glossary.md#iot-edge-hub). 
* Egy 30 másodperces videoklipet is rögzít az RTSP-forrásról a [Signal Gate-processzor](media-graph-concept.md#signal-gate-processor) használatával, és Media Services eszközként tárolja azt.

## <a name="create-the-computer-vision-resource"></a>A Computer Vision erőforrás létrehozása

Létre kell hoznia egy Computer Vision típusú Azure-erőforrást [Azure Portal](../../iot-edge/how-to-deploy-modules-portal.md) vagy az Azure CLI-n keresztül. Az erőforrást akkor fogja tudni létrehozni, ha a tárolóhoz való hozzáférésre vonatkozó kérelmét jóváhagyták, és az Azure-előfizetési azonosítót regisztrálták. A https://aka.ms/csgate használati eset és az Azure-előfizetés azonosítójának elküldéséhez nyissa meg a következőt:.  Létre kell hoznia az Azure-erőforrást ugyanazzal az Azure-előfizetéssel, mint amelyet a hozzáférési űrlapra vonatkozó kérelem biztosít.

### <a name="gathering-required-parameters"></a>A szükséges paraméterek összegyűjtése

Három elsődleges paraméter van az összes szükséges Cognitive Services tárolóhoz, beleértve a térbeli elemzési tárolót is. A végfelhasználói licencszerződést (EULA) az Accept értékkel kell megadnia. Emellett szükség van egy végponti URI-ra és egy API-kulcsra is.

### <a name="keys-and-endpoint-uri"></a>Kulcsok és végpont URI-ja

A rendszer egy kulcsot használ a térbeli elemzési tároló elindításához, és a `Keys and Endpoint` megfelelő kognitív szolgáltatási erőforrás Azure Portal lapján érhető el. Keresse meg a lapot, és keresse meg a kulcsokat és a végpont URI-JÁT.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/spatial-analysis-tutorial/keys-endpoint.png" alt-text="Végpont URI-ja":::

## <a name="set-up-azure-stack-edge"></a>Azure Stack Edge beállítása

Az alábbi [lépéseket](../../databox-online/azure-stack-edge-gpu-deploy-prep.md) követve állíthatja be az Azure stack Edge-t, és folytassa az alábbi lépések végrehajtásával az élő videó-elemzés és a térbeli elemzési modulok üzembe helyezéséhez.

## <a name="set-up-your-development-environment"></a>A fejlesztési környezet beállítása

1. A tárház klónozása erről a helyről: https://github.com/Azure-Samples/live-video-analytics-iot-edge-csharp .
1. A Visual Studio Code-ban nyissa meg azt a mappát, ahol a tárház le lett töltve.
1. A Visual Studio Code-ban nyissa meg a src/Cloud-to-Device-Console-app mappát. Ott hozzon létre egy fájlt, és nevezze el appsettings.js. Ez a fájl a program futtatásához szükséges beállításokat fogja tartalmazni.
1. Az alábbi lépéseket követve szerezze be a IotHubConnectionString a Azure Stack szélétől:

    * a bal oldali navigációs panelen kattintson a IoT Hub a Azure Portal és a elemre `Shared access policies` .
    * Kattintson a `iothubowner` közös elérésű kulcsok beolvasása elemre.
    * Másolja a vágólapra,  `Connection String – primary key` és illessze be a VSCode beviteli mezőjébe.
    
        A következőhöz hasonló lesz a kapcsolatok karakterlánca: <br/>`HostName=xxx.azure-devices.net;SharedAccessKeyName=iothubowner;SharedAccessKey=xxx`
1. Másolja az alábbi tartalmat a fájlba. Győződjön meg arról, hogy lecserélte a változókat.
    
    ```json
    {
        "IoThubConnectionString" : "HostName=<IoTHubName>.azure-devices.net;SharedAccessKeyName=iothubowner;SharedAccessKey=<SharedAccessKey>",
        "deviceId" : "<your Azure Stack Edge name>",
        "moduleId" : "lvaEdge"
    } 
    ```
1. Nyissa meg az src/Edge mappát, és hozzon létre egy. env nevű fájlt.
1. Másolja a/clouddrive/LVA-Sample/Edge-Deployment/.env fájl tartalmát. A szövegnek a következő kódhoz hasonlóan kell kinéznie.

    ```env
    SUBSCRIPTION_ID="<Subscription ID>"  
    RESOURCE_GROUP="<Resource Group>"  
    AMS_ACCOUNT="<AMS Account ID>"  
    IOTHUB_CONNECTION_STRING="HostName=xxx.azure-devices.net;SharedAccessKeyName=iothubowner;SharedAccessKey=xxx"  
    AAD_TENANT_ID="<AAD Tenant ID>"  
    AAD_SERVICE_PRINCIPAL_ID="<AAD SERVICE_PRINCIPAL ID>"  
    AAD_SERVICE_PRINCIPAL_SECRET="<AAD SERVICE_PRINCIPAL ID>"  
    VIDEO_INPUT_FOLDER_ON_DEVICE="/home/lvaadmin/samples/input"  
    VIDEO_OUTPUT_FOLDER_ON_DEVICE="/var/media"
    APPDATA_FOLDER_ON_DEVICE="/var/local/mediaservices"
    CONTAINER_REGISTRY_USERNAME_myacr="<your container registry username>"  
    CONTAINER_REGISTRY_PASSWORD_myacr="<your container registry password>"   
    ```
    
## <a name="set-up-deployment-template"></a>Telepítési sablon beállítása  

Keresse meg a telepítési fájlt a/src/Edge/deployment.spatialAnalysis.template.json címen. A sablonban a lvaEdge modul, a rtspsim modul és a térbeli elemzési modul található.

Az üzembe helyezési sablon fájljában néhány dolgot kell figyelnie:

1. Állítsa be a port kötését.
    
    ```
    "PortBindings": {
        "50051/tcp": [
            {
                "HostPort": "50051"
            }
        ]
    },
    ```
1. `IpcMode` a lvaEdge és a térbeli elemzési modul createOptions azonosnak kell lennie, és a gazdagépre kell beállítani.
1. Az RTSP-szimulátor működéséhez győződjön meg arról, hogy beállította a kötet határait. További információ: [Setup Docker Volume mounts](deploy-azure-stack-edge-how-to.md#optional-setup-docker-volume-mounts).

    1. [Kapcsolódjon az SMB-megosztáshoz](../../databox-online/azure-stack-edge-deploy-add-shares.md#connect-to-an-smb-share) , és másolja a [minta buldózer-videofájl](https://lvamedia.blob.core.windows.net/public/bulldozer.mkv) a helyi megosztásba.  
        > [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4Mesi]  
    1. Ellenőrizze, hogy a rtspsim modul a következő konfigurációval rendelkezik-e:
        ```
        "createOptions": {
                            "HostConfig": {
                              "Mounts": [
                                {
                                  "Target": "/live/mediaServer/media",
                                  "Source": "<your Local Docker Volume Mount name>",
                                  "Type": "volume"
                                }
                              ],
                              "PortBindings": {
                                "554/tcp": [
                                  {
                                    "HostPort": "554"
                                  }
                                ]
                              }
                            }
                          }
        ```
        

## <a name="generate-and-deploy-the-deployment-manifest"></a>Az üzembe helyezési jegyzék előállítása és üzembe helyezése

Az üzembe helyezési jegyzék meghatározza, hogy a rendszer mely modulokat telepíti egy peremhálózati eszközre. Emellett meghatározza a modulok konfigurációs beállításait is.

Kövesse az alábbi lépéseket a jegyzékfájlnak a sablonból való létrehozásához, majd a peremhálózati eszközre történő telepítéséhez.

1. Nyissa meg a Visual Studio Code-ot.
1. Az AZURE IOT HUB panel mellett válassza a további műveletek ikont a IoT Hub kapcsolódási karakterlánc beállításához. A karakterláncot a `src/cloud-to-device-console-app/appsettings.json` fájlból másolhatja.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/spatial-analysis-tutorial/connection-string.png" alt-text="Térbeli elemzés: a kapcsolatok karakterlánca":::
1. Kattintson a jobb gombbal `src/edge/deployment.spatialAnalysis.template.json` , és válassza a IoT Edge üzembe helyezési jegyzék előállítása lehetőséget.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/spatial-analysis-tutorial/deployment-template-json.png" alt-text="Térbeli elemzés: üzembe helyezés amd64 JSON":::
    
    Ehhez a művelethez létre kell hoznia egy deployment.amd64.jsnevű jegyzékfájlt az src/Edge/config mappában.
1. Kattintson a jobb gombbal `src/edge/config/deployment.spatialAnalysis.amd64.json` , válassza a központi telepítés létrehozása egyetlen eszközhöz lehetőséget, majd válassza ki a peremhálózati eszköz nevét.
    
    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/spatial-analysis-tutorial/deployment-amd64-json.png" alt-text="Térbeli elemzés: üzembe helyezési sablon JSON":::   
1. Amikor a rendszer rákérdez a IoT Hub eszköz kiválasztására, válassza ki a Azure Stack Edge nevét a legördülő menüből.
1. Körülbelül 30 másodperc elteltével frissítse az Azure IoT Hubt az ablak bal alsó sarkában. A peremhálózati eszköz mostantól a következő központilag telepített modulokat mutatja:
    
    * Élő video Analytics IoT Edge (modul neve lvaEdge).
    * Real-Time Streaming Protocol (RTSP) szimulátor (modul neve rtspsim).
    * Térbeli elemzés (modul neve spatialAnalysis).
    
Ha a telepítés sikeresen megtörtént, a KIMENETben a következőhöz hasonló üzenet jelenik meg:

```
[Edge] Start deployment to device [<Azure Stack Edge name>]
[Edge] Deployment succeeded.
```

Ezután megkeresheti `lvaEdge` , `rtspsim` `spatialAnalysis` és `rtspsim` modulokat használhat az eszközök/modulok területen, és az állapotuknak "fut" állapotúnak kell lennie.

## <a name="prepare-to-monitor-events"></a>Felkészülés az események figyelésére

Az események megtekintéséhez kövesse az alábbi lépéseket:

1. A Visual Studio Code-ban nyissa meg a **bővítmények** lapot (vagy nyomja le a CTRL + SHIFT + X billentyűkombinációt), és keressen rá az Azure IoT hubra.
1. Kattintson a jobb gombbal, és válassza a **bővítmény beállításai** lehetőséget.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/run-program/extensions-tab.png" alt-text="Bővítmény beállításai":::
1. Keresse meg és engedélyezze a "részletes üzenet megjelenítése" lehetőséget.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/run-program/show-verbose-message.png" alt-text="Részletes üzenet megjelenítése":::
1. Nyissa meg az Explorer ablaktáblát, és keresse meg az Azure IoT Hub a bal alsó sarokban.
1. Bontsa ki az eszközök csomópontot.
1. Kattintson a jobb gombbal az Azure Stack Edge-re, és válassza a figyelés beépített esemény végpontja lehetőséget.
    
    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/spatial-analysis-tutorial/start-monitoring.png" alt-text="Térbeli elemzés: figyelés indítása":::
     
## <a name="run-the-program"></a>A program futtatása

Létezik egy program.cs, amely a közvetlen metódusokat hívja meg a src/Cloud-to-Device-Console-app/operations.json. Be kell állítania a operations.jst, és meg kell adnia egy topológiát a Media Graph használatához.  

operations.json:

* A következőhöz hasonló topológiát állítsa be:

```json
{
    "opName": "GraphTopologySet",
    "opParams": {
        "topologyUrl": "https://raw.githubusercontent.com/Azure/live-video-analytics/master/MediaGraph/topologies/lva-spatial-analysis/2.0/topology.json"
    }
},
```

* Hozzon létre gráf-példányt ehhez hasonló módon, itt állítsa be a paramétereket a topológiában:

```json
{
    "opName": "GraphInstanceSet",
    "opParams": {
        "name": "Sample-Graph-1",
        "properties": {
            "topologyName": "InferencingWithCVExtension",
            "description": "Sample graph description",
            "parameters": [
                {
                    "name": "rtspUrl",
                    "value": " rtsp://rtspsim:554/media/bulldozer.mkv"
                },
                {
                    "name": "rtspUserName",
                    "value": "testuser"
                },
                {
                    "name": "rtspPassword",
                    "value": "testpassword"
                }
            ]
        }
    }
},
```

>[!Note]
Tekintse meg a MediaGraphRealTimeComputerVisionExtension használatát a térbeli elemzési modullal való kapcsolódáshoz. Állítsa be a $ {grpcUrl} értéket **TCP://spatialAnalysis: <PORT_NUMBER>**, például TCP://spatialAnalysis:50051

```json
{
    "@type": "#Microsoft.Media.MediaGraphCognitiveServicesVisionExtension",
    "name": "computerVisionExtension",
    "endpoint": {
        "@type": "#Microsoft.Media.MediaGraphUnsecuredEndpoint",
        "url": "${grpcUrl}",
        "credentials": {
            "@type": "#Microsoft.Media.MediaGraphUsernamePasswordCredentials",
            "username": "${spatialanalysisusername}",
            "password": "${spatialanalysispassword}"
        }
    },
    "image": {
        "scale": {
            "mode": "pad",
            "width": "1408",
            "height": "786"
        },
        "format": {
            "@type": "#Microsoft.Media.MediaGraphImageFormatRaw",
            "pixelFormat": "bgr24"
        }
    },
    "samplingOptions": {
        "skipSamplesWithoutAnnotation": "false",
        "maximumSamplesPerSecond": "20"
    },
    "inputs": [
        {
            "nodeName": "rtspSource",
            "outputSelectors": [
                {
                    "property": "mediaType",
                    "operator": "is",
                    "value": "video"
                }
            ]
        }
    ]
}
```

Futtasson hibakeresési munkamenetet, és kövesse a **terminál** utasításait, majd állítsa be a topológiát, állítsa be a Graph-példányt, aktiválja a Graph-példányt, és végül törölje az erőforrásokat.

## <a name="interpret-results"></a>Eredmények értelmezése

Ha egy adathordozó-gráfot hoz létre, a "MediaSessionEstablished" eseményt kell megjelennie, itt egy [példa MediaSessionEstablished eseményt](detect-motion-emit-events-quickstart.md#mediasessionestablished-event).

A térbeli elemzési modul a mesterséges intelligenciát bemutató eseményeket is küldi az élő videó-elemzésbe, majd a IoTHub a **kimenetben** is megjelenik. Az entitás az észlelési objektumok, az esemény pedig spaceanalytics eseményeket. Ezt a kimenetet a rendszer átadja a Live Video Analyticsnek.

Minta kimenete a personZoneEvent (a cognitiveservices. vízió. spatialanalysis-personcrossingpolygon. livevideoanalytics műveletből):

```
{
  "body": {
    "timestamp": 143810610210090,
    "inferences": [
      {
        "type": "entity",
        "subtype": "",
        "inferenceId": "a895c86824db41a898f776da1876d230",
        "relatedInferences": [],
        "entity": {
          "tag": {
            "value": "person",
            "confidence": 0.66026187
          },
          "attributes": [],
          "box": {
            "l": 0.26559368,
            "t": 0.17887735,
            "w": 0.49247605,
            "h": 0.76629657
          }
        },
        "extensions": {},
        "valueCase": "entity"
      },
      {
        "type": "event",
        "subtype": "",
        "inferenceId": "995fe4206847467f8dfde83a15187d76",
        "relatedInferences": [
          "a895c86824db41a898f776da1876d230"
        ],
        "event": {
          "name": "personZoneEvent",
          "properties": {
            "status": "Enter",
            "metadataVersion": "1.0",
            "zone": "polygon0",
            "trackingId": "a895c86824db41a898f776da1876d230"
          }
        },
        "extensions": {},
        "valueCase": "event"
      }
    ]
  },
  "applicationProperties": {
    "topic": "/subscriptions/81631a63-f0bd-4f35-8344-5bc541b36bfc/resourceGroups/lva-sample-resources/providers/microsoft.media/mediaservices/lvasamplea4bylcwoqqypi",
    "subject": "/graphInstances/Sample-Graph-1/processors/spatialanalysisExtension",
    "eventType": "Microsoft.Media.Graph.Analytics.Inference",
    "eventTime": "2020-08-20T03:54:29.001Z",
    "dataVersion": "1.0"
  }
}
```

## <a name="next-steps"></a>Következő lépések

Próbálkozzon a modul által kínált különböző műveletekkel, `spatialAnalysis` például a **personCount** és a **personDistance** . ehhez a telepítési jegyzékfájl Graph csomópontjában az "enabled" jelzőt kell bekapcsolni.
>[!Tip]
> Használjon egy [minta videofájlokat](https://lvamedia.blob.core.windows.net/public/2018-03-07.16-50-00.16-55-00.school.G421.mkv) , amelynek több személye van a keretben.

> [!NOTE]
> Egyszerre csak egy műveletet futtathat. Ezért győződjön meg arról, hogy csak egy jelző van beállítva **igaz** értékre, a többi pedig **false (hamis**) értékre van állítva.