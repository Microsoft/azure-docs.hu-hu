---
title: Folyamatos videofelvétel a felhőbe és a lejátszás a Felhőbeli oktatóanyagból – Azure
description: Ebből az oktatóanyagból megtudhatja, hogyan használhatja az Azure Live Video Analytics szolgáltatást Azure IoT Edgeon, hogy folyamatosan rögzítsen videókat a felhőbe, és a videó bármely részét továbbítsa a Azure Media Services használatával.
ms.topic: tutorial
ms.date: 05/27/2020
ms.openlocfilehash: 6956539595c5997569dc92b5c62e2396cb5fdd9c
ms.sourcegitcommit: ea822acf5b7141d26a3776d7ed59630bf7ac9532
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/03/2021
ms.locfileid: "99524397"
---
# <a name="tutorial-continuous-video-recording-to-the-cloud-and-playback-from-the-cloud"></a>Oktatóanyag: folyamatos videofelvétel a felhőbe és a felhőből való lejátszás

Ebből az oktatóanyagból megtudhatja, hogyan használhatja az Azure Live Video Analytics szolgáltatást Azure IoT Edge a [folyamatos videofelvételek](continuous-video-recording-concept.md) (CVR) elvégzéséhez a felhőben, és a videó bármely részének továbbítását a Azure Media Services használatával. Ez a képesség olyan forgatókönyvek esetén hasznos, mint például a biztonság és a megfelelőség, ahol a felvételek archívumát kell fenntartani egy kamerából napok vagy hetek számára. 

Ebben az oktatóanyagban a következőket fogja elsajátítani:

> [!div class="checklist"]
> * Állítsa be a megfelelő erőforrásokat.
> * Vizsgálja meg a CVR végrehajtó kódot.
> * Futtassa a kódot.
> * Vizsgálja meg az eredményeket, és tekintse meg a videót.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="suggested-pre-reading"></a>Javasolt előzetes olvasás  

A Kezdés előtt olvassa el a következő cikkeket:

* [Élő videó-elemzések IoT Edge áttekintése](overview.md)
* [Élő videó-elemzések IoT Edge terminológiában](terminology.md)
* [A Media Graph alapfogalmai](media-graph-concept.md) 
* [Folyamatos videó-rögzítési forgatókönyvek](continuous-video-recording-concept.md)

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag előfeltételei a következők:

* A [Visual Studio Code](https://code.visualstudio.com/) a fejlesztői gépen az [Azure IoT-eszközökkel](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) és a [C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) -bővítménnyel.

    > [!TIP]
    > Előfordulhat, hogy a rendszer a Docker telepítését kéri. Hagyja figyelmen kívül ezt a kérést.
* [.Net Core 3,1 SDK](https://dotnet.microsoft.com/download/dotnet-core/thank-you/sdk-3.1.201-windows-x64-installer) a fejlesztői gépen.
* Fejezze be az [élő videó elemzési erőforrásainak telepítési parancsfájlját](https://github.com/Azure/live-video-analytics/tree/master/edge/setup).

Ezen lépések végén az Azure-előfizetésében üzembe helyezett Azure-erőforrásokkal fog rendelkezni:

* Azure IoT Hub
* Azure Storage-fiók
* Azure Media Services fiók
* Linux rendszerű virtuális gép az Azure-ban, telepített [IoT Edge futtatókörnyezettel](../../iot-edge/how-to-install-iot-edge.md)

> [!TIP]
> Ha a létrehozott Azure-erőforrásokkal kapcsolatos problémákba ütközik, tekintse meg a **[hibaelhárítási útmutatót](troubleshoot-how-to.md#common-error-resolutions)** a gyakran előforduló problémák megoldásához.

## <a name="concepts"></a>Alapelvek

Ahogy az a [Media Graph koncepciójában](media-graph-concept.md) is látható, a Media Graph segítségével meghatározhatja a következőket:

- Az adathordozót a alkalmazásból kell rögzíteni.
- Hogyan kell feldolgozni.
- Az eredmények kézbesítésének helye. 
 
 A CVR elvégzéséhez egy RTSP-kompatibilis kameráról kell rögzítenie a videót, és folyamatosan rögzítenie kell egy [Azure Media Services eszközön](terminology.md#asset). Ez az ábra az adott adathordozó-gráf grafikus ábrázolását mutatja be.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/continuous-video-recording-tutorial/continuous-video-recording-overview.svg" alt-text="Médiagrafikon":::

Ebben az oktatóanyagban egy, a [Live555 Media Server](https://github.com/Azure/live-video-analytics/tree/master/utilities/rtspsim-live555) használatával létrehozott Edge-modult fog használni az RTSP-kamera szimulálásához. A Media Graph-ban egy [RTSP-forrás](media-graph-concept.md#rtsp-source) csomóponttal érheti el az élő hírcsatornát, és elküldheti a videót az [eszköz fogadó csomópontjára](media-graph-concept.md#asset-sink), amely rögzíti a videót az adott eszközre. Az oktatóanyagban használt videó [egy autópálya-metszeti minta videó](https://lvamedia.blob.core.windows.net/public/camera-300s.mkv).
<iframe src="https://www.microsoft.com/en-us/videoplayer/embed/RE4LTY4" width="640" height="320" allowFullScreen="true" frameBorder="0"></iframe>
> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4LTY4]

## <a name="set-up-your-development-environment"></a>A fejlesztési környezet beállítása

Mielőtt elkezdené, győződjön meg arról, hogy végrehajtotta a harmadik felsorolásjelet az [Előfeltételekben](#prerequisites). Az erőforrás-telepítési parancsfájl befejeződése után válassza ki a kapcsos zárójeleket, hogy elérhetővé tegye a mappastruktúrát. A ~/clouddrive/LVA-Sample könyvtár alatt létrehozott néhány fájl megjelenik.

![Alkalmazásbeállítások](./media/quickstarts/clouddrive.png)

Ebben az oktatóanyagban a következő fájlok szerepelnek:

* **~/clouddrive/LVA-Sample/Edge-Deployment/.env**: olyan tulajdonságokat tartalmaz, amelyeket a Visual Studio Code használ a modulok peremhálózati eszközre való telepítéséhez.
* **~/clouddrive/lva-sample/appsettings.json**: a mintakód futtatásához a Visual Studio Code használatos.

Ehhez a következő lépésekhez szüksége lesz a fájlokra:

1. A tárház klónozása a GitHub-hivatkozásról https://github.com/Azure-Samples/live-video-analytics-iot-edge-csharp .
1. Indítsa el a Visual Studio Code-ot, és nyissa meg azt a mappát, ahová a tárházat letöltötte.
1. A Visual Studio Code-ban keresse meg a src/Cloud-to-Device-Console-app mappát, és hozzon létre egy **appsettings.js** nevű fájlt. Ez a fájl tartalmazza a program futtatásához szükséges beállításokat.
1. Másolja a tartalmat a ~/clouddrive/LVA-Sample/appsettings.jsfájlból. A szövegnek a következőhöz hasonlóan kell kinéznie:
    ```
    {  
        "IoThubConnectionString" : "HostName=xxx.azure-devices.net;SharedAccessKeyName=iothubowner;SharedAccessKey=XXX",  
        "deviceId" : "lva-sample-device",  
        "moduleId" : "lvaEdge"  
    }
    ```
    A IoT Hub kapcsolódási karakterlánc lehetővé teszi, hogy a Visual Studio Code használatával parancsokat küldjön az Edge-moduloknak az Azure IoT Hub segítségével.
    
1. Ezután keresse meg a src/Edge mappát, és hozzon létre egy **. env** nevű fájlt.
1. Másolja a tartalmat a ~/clouddrive/LVA-Sample/Edge-Deployment/.env fájlból. A szövegnek a következőhöz hasonlóan kell kinéznie:

    ```
    SUBSCRIPTION_ID="<Subscription ID>"  
    RESOURCE_GROUP="<Resource Group>"  
    AMS_ACCOUNT="<AMS Account ID>"  
    IOTHUB_CONNECTION_STRING="HostName=xxx.azure-devices.net;SharedAccessKeyName=iothubowner;SharedAccessKey=xxx"  
    AAD_TENANT_ID="<AAD Tenant ID>"  
    AAD_SERVICE_PRINCIPAL_ID="<AAD SERVICE_PRINCIPAL ID>"  
    AAD_SERVICE_PRINCIPAL_SECRET="<AAD SERVICE_PRINCIPAL ID>"  
    VIDEO_INPUT_FOLDER_ON_DEVICE="/home/lvaadmin/samples/input"  
    VIDEO_OUTPUT_FOLDER_ON_DEVICE="/home/lvaadmin/samples/output"  
    APPDATA_FOLDER_ON_DEVICE="/var/local/mediaservices"
    CONTAINER_REGISTRY_USERNAME_myacr="<your container registry username>"  
    CONTAINER_REGISTRY_PASSWORD_myacr="<your container registry username>"      
    ```

## <a name="examine-the-sample-files"></a>A mintaadatok vizsgálata

A Visual Studio Code-ban nyissa meg a következőt: src/Edge/deployment.template.js. Ez a sablon határozza meg, hogy mely peremhálózati modulok lesznek üzembe helyezhetők a peremhálózati eszközön (az Azure Linux virtuális gépen). A **modulok** szakasz két bejegyzést tartalmaz a következő nevekkel:

* **lvaEdge**: az élő videó Analytics IoT Edge modulon.
* **rtspsim**: ez az RTSP-szimulátor.

Ezután keresse meg a src/Cloud-to-Device-Console-app mappát. Itt láthatja a appsettings.jsa létrehozott fájlon, néhány további fájllal együtt:

* **C2D-Console-app. csproj**: a Project fájl a Visual Studio Code-hoz.
* **operations.jsbekapcsolva**: Ez a fájl felsorolja a futtatott különböző műveleteket.
* **Program.cs**: a minta program kódja, amely:
    * Betölti az alkalmazás beállításait.
    * Az élő videó Analytics IoT Edge modulban elérhető közvetlen metódusokat hívja meg. A modul segítségével elemezheti az élő videó streameket a [közvetlen metódusok](direct-methods.md)meghívásával.
    * Szünetelteti, hogy megvizsgálja a program kimenetét a **terminál** ablakban, valamint a **kimenet** ablakban a modul által generált eseményeket.
    * Közvetlen metódusokat hív meg az erőforrások törléséhez.

## <a name="generate-and-deploy-the-iot-edge-deployment-manifest"></a>Az IoT Edge telepítési jegyzék előállítása és üzembe helyezése 

Az üzembe helyezési jegyzék meghatározza, hogy milyen modulok vannak üzembe helyezve egy peremhálózati eszközön és a modulok konfigurációs beállításaiban. Kövesse az alábbi lépéseket egy jegyzékfájl létrehozásához a sablonból, majd telepítse azt a peremhálózati eszközre.

1. A Visual Studio Code elindítása.
1. Állítsa be a IoT Hub a kapcsolódási karakterláncot a bal alsó sarokban található **Azure IOT hub** panel melletti **További műveletek** ikonra kattintva. Másolja a karakterláncot a src/Cloud-to-Device-Console-app/appsettings.jsfájlból. 

    ![IoT Hub-kapcsolatok karakterláncának beállítása](./media/quickstarts/set-iotconnection-string.png)
1. Kattintson a jobb gombbal a fájlhoz tartozó src/Edge/deployment.template.jselemre, és válassza a **IoT Edge üzembe helyezési jegyzék előállítása** lehetőséget. A Visual Studio Code a. env fájl értékeit használja a központi telepítési sablonban található változók cseréjéhez. Ez a művelet egy jegyzékfájlt hoz létre az **deployment.amd64.js** nevű src/Edge/config mappában.

   ![IoT Edge üzembe helyezési jegyzék előállítása](./media/quickstarts/generate-iot-edge-deployment-manifest.png)
1. Kattintson a jobb gombbal az src/Edge/config/deployment.amd64.jsfájlra, és válassza a **központi telepítés létrehozása egyetlen eszközhöz** lehetőséget.

   ![Központi telepítés létrehozása egyetlen eszközhöz](./media/quickstarts/create-deployment-single-device.png)
1. Ezután **egy IoT hub eszköz kiválasztását** kéri. Válassza ki a LVA-Sample-Device elemet a legördülő listából.
1. Körülbelül 30 másodperc alatt frissítse az Azure IoT Hubt a bal alsó szakaszban. Látnia kell, hogy a peremhálózati eszközön telepítve vannak a következő modulok:
    * Élő video Analytics IoT Edge (modul neve **lvaEdge**)
    * RTSP-szimulátor (modul neve **rtspsim**)
 
    ![IoT Hub](./media/continuous-video-recording-tutorial/iot-hub.png)

## <a name="prepare-to-monitor-the-modules"></a>Felkészülés a modulok figyelésére 

Ha a Live Video Analytics szolgáltatást használja IoT Edge modulban az élő videó stream rögzítéséhez, akkor az eseményeket a IoT Hub küldi. Az események megtekintéséhez kövesse az alábbi lépéseket:

1. Nyissa meg az Explorer panelt a Visual Studio Code-ban, és keresse meg az **Azure IoT hub** a bal alsó sarokban.
1. Bontsa ki az **eszközök** csomópontot.
1. Kattintson a jobb gombbal a LVA-Sample-Device fájlra, és válassza a **figyelés beépített esemény végpontja** lehetőséget.

    ![A beépített esemény-végpont figyelésének megkezdése](./media/quickstarts/start-monitoring-iothub-events.png)

> [!NOTE]
> Előfordulhat, hogy a rendszer megkéri, hogy adjon meg egy beépített végponti információt a IoT Hub számára. Az információk lekéréséhez Azure Portalban navigáljon a IoT Hub, és keresse meg a **beépített végpontok** lehetőséget a bal oldali navigációs panelen. Kattintson ide, és az Event hub-kompatibilis **végpont** szakaszban keresse meg az **Event hub-kompatibilis végpontot** . Másolja ki és használja a szövegmezőben található szöveget. A végpont így fog kinézni:  
    ```
    Endpoint=sb://iothub-ns-xxx.servicebus.windows.net/;SharedAccessKeyName=iothubowner;SharedAccessKey=XXX;EntityPath=<IoT Hub name>
    ```

## <a name="run-the-program"></a>A program futtatása 

1. A Visual Studio Code-ban nyissa meg a **bővítmények** lapot (vagy nyomja le a CTRL + SHIFT + X billentyűkombinációt), és keressen rá az Azure IoT hubra.
1. Kattintson a jobb gombbal, és válassza a **bővítmény beállításai** lehetőséget.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/run-program/extensions-tab.png" alt-text="Bővítmény beállításai":::
1. Keresse meg és engedélyezze a "részletes üzenet megjelenítése" lehetőséget.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/run-program/show-verbose-message.png" alt-text="Részletes üzenet megjelenítése":::
1. Lépjen a src/Cloud-to-Device-Console-app/operations.jselemre.
1. A **GraphTopologySet** csomópont alatt szerkessze a következőket:

    `"topologyUrl" : "https://raw.githubusercontent.com/Azure/live-video-analytics/master/MediaGraph/topologies/cvr-asset/2.0/topology.json" `
1. Ezután a **GraphInstanceSet** és az **GraphTopologyDelete** csomópont alatt ellenőrizze, hogy a **topologyName** értéke megegyezik-e az előző Graph-topológia **Name (név** ) tulajdonságának értékével:

    `"topologyName" : "CVRToAMSAsset"`  
1. Nyissa meg a [topológiát](https://raw.githubusercontent.com/Azure/live-video-analytics/master/MediaGraph/topologies/cvr-asset/2.0/topology.json) egy böngészőben, és tekintse meg a következőt: assetNamePattern. Annak érdekében, hogy rendelkezzen egy egyedi névvel rendelkező eszközzel, érdemes lehet módosítani a Graph-példány nevét a fájl operations.jsjában (a minta-Graph-1 alapértelmezett értékről).

    `"assetNamePattern": "sampleAsset-${System.GraphTopologyName}-${System.GraphInstanceName}"`    
1. Indítsa el a hibakeresési munkamenetet az F5 billentyű kiválasztásával. Néhány üzenet jelenik meg a **terminál** ablakban.
1. A fájl operations.jselindul a GraphTopologyList és a GraphInstanceList hívásával. Ha az előző rövid útmutatók vagy oktatóanyagok után törölte az erőforrásokat, ez a művelet üres listát ad vissza, majd szünetelteti az **ENTER billentyűt**, amint az alábbi ábrán látható:

    ```
    --------------------------------------------------------------------------
    Executing operation GraphTopologyList
    -----------------------  Request: GraphTopologyList  --------------------------------------------------
    {
      "@apiVersion": "2.0"
    }
    ---------------  Response: GraphTopologyList - Status: 200  ---------------
    {
      "value": []
    }
    --------------------------------------------------------------------------
    Executing operation WaitForInput
    Press Enter to continue
    ```

1. Miután kiválasztotta az **ENTER billentyűt** a **terminál** ablakban, a következő közvetlen metódus-hívások is létrejönnek:
   * A GraphTopologySet hívása az előző topologyUrl használatával
   * A GraphInstanceSet hívása a következő törzs használatával
     
     ```
     {
       "@apiVersion": "2.0",
       "name": "Sample-Graph-1",
       "properties": {
         "topologyName": "CVRToAMSAsset",
         "description": "Sample graph description",
         "parameters": [
           {
             "name": "rtspUrl",
             "value": "rtsp://rtspsim:554/media/camera-300s.mkv"
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
     ```
   * A Graph-példány elindítására és a videó folyamatának elindítására irányuló GraphInstanceActivate-hívás
   * Egy második hívás a GraphInstanceList, amely azt mutatja, hogy a Graph-példány fut állapotban van 
1. A **terminál** -ablak kimenete most szünetelteti az ENTER billentyűt a **folytatáshoz** . Most ne válassza az **ENTER billentyűt** . Görgessen felfelé, és tekintse meg a meghívott közvetlen metódusok JSON-válaszának hasznos adatait.
1. Ha most átvált a **kimeneti** ablakra a Visual Studio Code-ban, akkor a IoT Edge modul Live Video Analytics IoT hub által küldött üzeneteket fogja látni.

   Ezeket az üzeneteket a következő szakaszban tárgyaljuk.
1. A Graph-példány továbbra is fut, és rögzíti a videót. Az RTSP-szimulátor megtartja a forrás videóját. A rögzítés leállításához lépjen vissza a **Terminálablak** ablakába, és válassza az **ENTER billentyűt**. A következő hívási sorozat az erőforrások törlését végzi a használatával:

   * A Graph-példány inaktiválására irányuló GraphInstanceDeactivate hívása.
   * A példány törlésére irányuló GraphInstanceDelete-hívás.
   * A topológia törlésére irányuló GraphTopologyDelete-hívás.
   * A GraphTopologyList végső hívása annak bemutatására, hogy a lista már üres.

## <a name="interpret-the-results"></a>Az eredmények értelmezése 

A Media Graph futtatásakor az élő videó Analytics IoT Edge modulban bizonyos diagnosztikai és műveleti eseményeket küld az IoT Edge hubhoz. Ezek az események a Visual Studio Code **kimeneti** ablakában látható üzenetek. A törzs szakaszt és egy applicationProperties szakaszt tartalmaznak. A következő fejezeteinek megismeréséhez lásd: [IoT hub üzenetek létrehozása és olvasása](../../iot-hub/iot-hub-devguide-messages-construct.md).

A következő üzenetekben az alkalmazás tulajdonságait és a törzs tartalmát az élő videó elemzési modulja határozza meg.

## <a name="diagnostics-events"></a>Diagnosztikai események 

### <a name="mediasession-established-event"></a>MediaSession-létrehozási esemény

Ha a Graph-példány aktiválva van, az RTSP-forrás csomópont megpróbál csatlakozni a rtspsim modulban futó RTSP-kiszolgálóhoz. Ha a művelet sikeres, a következő eseményt nyomtatja ki:

```
[IoTHubMonitor] [9:42:18 AM] Message received from [lvaedgesample/lvaEdge]:
{
  "body": {
    "sdp": "SDP:\nv=0\r\no=- 1586450538111534 1 IN IP4 XXX.XX.XX.XX\r\ns=Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\ni=media/camera-300s.mkv\r\nt=0 0\r\na=tool:LIVE555 Streaming Media v2020.03.06\r\na=type:broadcast\r\na=control:*\r\na=range:npt=0-300.000\r\na=x-qt-text-nam:Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\na=x-qt-text-inf:media/camera-300s.mkv\r\nm=video 0 RTP/AVP 96\r\nc=IN IP4 0.0.0.0\r\nb=AS:500\r\na=rtpmap:96 H264/90000\r\na=fmtp:96 packetization-mode=1;profile-level-id=4D0029;sprop-parameter-sets=XXXXXXXXXXXXXXXXXXXXXX\r\na=control:track1\r\n"
  },
  "applicationProperties": {
    "dataVersion": "1.0",
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{name}/providers/microsoft.media/mediaservices/hubname",
    "subject": "/graphInstances/Sample-Graph-1/sources/rtspSource",
    "eventType": "Microsoft.Media.MediaGraph.Diagnostics.MediaSessionEstablished",
    "eventTime": "2020-04-09T09:42:18.1280000Z"
  }
}
```

* Az üzenet egy diagnosztikai esemény (MediaSessionEstablished). Azt jelzi, hogy az RTSP-forrás csomópontja (a tárgy) létrehozta a kapcsolódást az RTSP-szimulátorral, és megkezdte a (szimulált) élő adatcsatorna fogadását.
* A applicationProperties tárgy szakasza a Graph-topológia azon csomópontjára hivatkozik, amelyről az üzenet létrejött. Ebben az esetben az üzenet az RTSP forrás csomópontból származik.
* A applicationProperties eventType szakasza azt jelzi, hogy ez egy diagnosztikai esemény.
* A eventTime szakasz azt az időpontot jelzi, amikor az esemény bekövetkezett.
* A Body (törzs) szakasz a diagnosztikai eseménnyel kapcsolatos adatokat tartalmaz, amelyek ebben az esetben a [SDP](https://en.wikipedia.org/wiki/Session_Description_Protocol) adatai.

## <a name="operational-events"></a>Működési események 

### <a name="recordingstarted-event"></a>RecordingStarted esemény

Amikor az eszköz elfogadó csomópontja elkezdi a videó rögzítését, a **Microsoft. Media. Graph. Operational. RecordingStarted** típusú eseményt bocsát ki:

```
[IoTHubMonitor] [9:42:38 AM] Message received from [lva-sample-device/lvaEdge]:
{
  "body": {
    "outputType": "assetName",
    "outputLocation": "sampleAsset-CVRToAMSAsset-Sample-Graph-1"
  },
  "applicationProperties": {
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{resource-group-name}/providers/microsoft.media/mediaservices/{ams-account-name}",
    "subject": "/graphInstances/Sample-Graph-1/sinks/assetSink",
    "eventType": "Microsoft.Media.Graph.Operational.RecordingStarted",
    "eventTime": "2020-04-09T09:42:38.1280000Z",
    "dataVersion": "1.0"
  }
}
```

A applicationProperties tárgy szakasza a gráfban található, az üzenetet generáló objektum fogadó csomópontjára hivatkozik.

A Body (törzs) szakasz a kimeneti helyről tartalmaz információkat. Ebben az esetben annak a Azure Media Services-objektumnak a neve, amelybe a videó rögzítve lesz. Jegyezze fel ezt az értéket.

### <a name="recordingavailable-event"></a>RecordingAvailable esemény

Ahogy a neve is sugallja, a rendszer a RecordingStarted eseményt küldi el a rögzítés megkezdése után, de előfordulhat, hogy a videó nem lett feltöltve az eszközre. Amikor az eszköz fogadó csomópontja feltöltötte a videós adatmennyiséget az eszközre, a **Microsoft. Media. Graph. Operational. RecordingAvailable** típusú eseményt bocsát ki:

```
[IoTHubMonitor] [[9:43:38 AM] Message received from [lva-sample-device/lvaEdge]:
{
  "body": {
    "outputType": "assetName",
    "outputLocation": "sampleAsset-CVRToAMSAsset-Sample-Graph-1"
  },
  "applicationProperties": {
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{resource-group-name}/providers/microsoft.media/mediaservices/{ams-account-name}",
    "subject": "/graphInstances/Sample-Graph-1/sinks/assetSink",
    "eventType": "Microsoft.Media.Graph.Operational.RecordingAvailable",
    "eventTime": "2020-04-09T09:43:38.1280000Z",
    "dataVersion": "1.0"
  }
}
```

Ez az esemény azt jelzi, hogy a játékosok vagy ügyfelek számára elegendő mennyiségű adattal rendelkező eszköz lett írva a videó lejátszásának megkezdéséhez.

A applicationProperties tárgy szakasza a gráf AssetSink csomópontjára hivatkozik, amely létrehozta ezt az üzenetet.

A Body (törzs) szakasz a kimeneti helyről tartalmaz információkat. Ebben az esetben annak a Azure Media Services-objektumnak a neve, amelybe a videó rögzítve lesz.

### <a name="recordingstopped-event"></a>RecordingStopped esemény

Ha inaktiválja a Graph-példányt, az eszköz fogadó csomópontja leállítja a videó rögzítését az eszközre. Ezt a **Microsoft. Media. Graph. Operational. RecordingStopped** típusú eseményt bocsátja ki:

```
[IoTHubMonitor] [11:33:31 PM] Message received from [lva-sample-device/lvaEdge]:
{
  "body": {
    "outputType": "assetName",
    "outputLocation": "sampleAsset-CVRToAMSAsset-Sample-Graph-1"
  },
  "applicationProperties": {
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{resource-group-name}/providers/microsoft.media/mediaservices/{ams-account-name}",
    "subject": "/graphInstances/Sample-Graph-1/sinks/assetSink",
    "eventType": "Microsoft.Media.Graph.Operational.RecordingStopped",
    "eventTime": "2020-04-10T11:33:31.051Z",
    "dataVersion": "1.0"
  }
}
```

Ez az esemény azt jelzi, hogy a rögzítés leállt.

A applicationProperties tárgy szakasza a gráf AssetSink csomópontjára hivatkozik, amely létrehozta ezt az üzenetet.

A Body (törzs) szakasz a kimeneti helyről tartalmaz információkat, amely ebben az esetben annak a Azure Media Services-eszköznek a neve, amelybe a videó rögzítve van.

## <a name="media-services-asset"></a>Media Services eszköz  

Megvizsgálhatja a Media Graph által létrehozott Media Services adategységet, ha bejelentkezik a Azure Portalba, és megtekinti a videót.

1. Nyissa meg a webböngészőt, és lépjen a [Azure Portal](https://portal.azure.com/). Adja meg a hitelesítő adatait a Portalra való bejelentkezéshez. Az alapértelmezett nézet a szolgáltatási irányítópult.
1. Keresse meg Media Services-fiókját az előfizetésében található erőforrások között, és nyissa meg a fiók ablaktáblát.
1. Válassza az **eszközök** lehetőséget a **Media Services** listában.

    ![Eszközök Media Services](./media/continuous-video-recording-tutorial/assets.png)
1. Itt található egy sampleAsset-CVRToAMSAsset-Sample-Graph-1 nevű eszköz. Ez a Graph-topológiai fájlban kiválasztott elnevezési minta.
1. Válassza ki az objektumot.
1. Az eszköz adatai lapon válassza az **új létrehozása** lehetőséget a **streaming URL-cím** szövegmezőben.

    ![Új eszköz](./media/continuous-video-recording-tutorial/new-asset.png)

1. A megnyíló varázslóban fogadja el az alapértelmezett beállításokat, majd válassza a **Hozzáadás** lehetőséget. [További információ: videolejátszás](video-playback-concept.md).

    > [!TIP]
    > Ellenőrizze, hogy [fut-e a folyamatos átviteli végpont](../latest/streaming-endpoint-concept.md).
1. A lejátszónak be kell töltenie a videót. Kattintson a **Lejátszás** gombra a megtekintéshez.

> [!NOTE]
> Mivel a videó forrása egy kamera-hírcsatornát szimuláló tároló volt, a videóban szereplő időbélyegek a Graph-példány aktiválásakor és az inaktiválásakor kapcsolódnak. Ha szeretné megtudni, hogyan böngészhet a multiday, és hogyan tekintheti meg az Archívum egyes részeit, tekintse meg a [Többnapos felvételek lejátszását](playback-multi-day-recordings-tutorial.md) ismertető oktatóanyagot. Ebben az oktatóanyagban a képernyőn megjelenő videó időbélyegeit is láthatja.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha szeretné kipróbálni a többi oktatóanyagot, tartsa be a létrehozott erőforrásokat. Ellenkező esetben lépjen a Azure Portal, keresse meg az erőforráscsoportot, válassza ki azt az erőforráscsoportot, amelyben az oktatóanyagot futtatta, és törölje az erőforráscsoportot.

## <a name="next-steps"></a>Következő lépések

* Használjon olyan [IP-kamerát](https://en.wikipedia.org/wiki/IP_camera) , amely támogatja az RTSP-t az RTSP-szimulátor használata helyett. Az ONVIF-kompatibilis [termékek lapon](https://www.onvif.org/conformant-products/) megkeresheti az RTSP-támogatással rendelkező IP-kamerákat a G, S vagy T profiloknak megfelelő eszközök keresésével.
* AMD64 vagy x64 Linux rendszerű eszköz használata (Azure Linux rendszerű virtuális gép használata). Az eszköznek ugyanabban a hálózaton kell lennie, mint az IP-kamerának. Kövesse a következő témakör utasításait: [Install Azure IoT Edge Runtime on Linux](../../iot-edge/how-to-install-iot-edge.md). Ezután kövesse az [első IoT Edge modul üzembe helyezése virtuális Linux-eszközre című](../../iot-edge/quickstart-linux.md) rövid útmutatót az eszköz Azure IoT hub való regisztrálásához.