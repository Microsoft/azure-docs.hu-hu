---
title: Eseményvezérelt videó rögzítése a felhőben és lejátszás a Felhőbeli oktatóanyagból – Azure
description: Ebből az oktatóanyagból megtudhatja, hogyan használhatja az Azure Live Video Analytics szolgáltatást a Azure IoT Edgeon, hogy rögzítse a felhőbe, és hogyan játssza vissza a felhőből.
ms.topic: tutorial
ms.date: 05/27/2020
ms.openlocfilehash: ea98b4c8981be9fffe7911e4c8402a8f522976f9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "101702317"
---
# <a name="tutorial-event-based-video-recording-to-the-cloud-and-playback-from-the-cloud"></a>Oktatóanyag: esemény-alapú videofelvétel a felhőbe és a felhőből való lejátszás

Ebből az oktatóanyagból megtudhatja, hogyan használhatja a Azure IoT Edge az Azure Live Video Analytics szolgáltatást, hogy az élő videó forrásának egyes részeit szelektíven rögzítse a felhőben Azure Media Services. Ez a használati eset az oktatóanyagban az [Event-based video Recording](event-based-video-recording-concept.md) (EVR) néven is ismert. Élő videó részeinek rögzítéséhez egy objektum-észlelési AI-modell használatával kell keresnie az objektumokat a videóban, és csak bizonyos típusú objektumok észlelésekor rögzíthet videoklipeket. Azt is megismerheti, hogyan lehet lejátszani a rögzített videoklipeket Media Services használatával. Ez a képesség számos olyan forgatókönyv esetében hasznos, ahol meg kell őrizni az érdekes videoklipek archívumát. 

Az oktatóanyagban a következőket végezheti el:

> [!div class="checklist"]
> * Állítsa be a megfelelő erőforrásokat.
> * Vizsgálja meg a EVR végrehajtó kódot.
> * Futtassa a kódot.
> * Vizsgálja meg az eredményeket, és tekintse meg a videót.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="suggested-pre-reading"></a>Javasolt előzetes olvasás  

A Kezdés előtt olvassa el a következő cikkeket:

* [Élő videó-elemzések IoT Edge áttekintése](overview.md)
* [Élő videó-elemzések IoT Edge terminológiában](terminology.md)
* [A Media Graph alapfogalmai](media-graph-concept.md) 
* [Eseményalapú videófelvétel](event-based-video-recording-concept.md)
* [Oktatóanyag: IoT Edge modul fejlesztése](../../iot-edge/tutorial-develop-for-linux.md)
* [Az üzembe helyezés szerkesztése. * .template.jsbekapcsolva](https://github.com/microsoft/vscode-azure-iot-edge/wiki/How-to-edit-deployment.*.template.json)
* Az [útvonalak deklarálása IoT Edge telepítési jegyzékben –](../../iot-edge/module-composition.md#declare-routes) szakasz

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag előfeltételei a következők:

* [Visual Studio Code](https://code.visualstudio.com/) a fejlesztői gépen az [Azure IoT-eszközökkel](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) és [C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) -bővítménnyel.

    > [!TIP]
    > Előfordulhat, hogy a rendszer a Docker telepítését kéri. Hagyja figyelmen kívül ezt a kérést.
* [.Net Core 3,1 SDK](https://dotnet.microsoft.com/download/dotnet-core/thank-you/sdk-3.1.201-windows-x64-installer) a fejlesztői gépen.
* Fejezze be az [élő videó elemzési erőforrásainak telepítési parancsfájlját](https://github.com/Azure/live-video-analytics/tree/master/edge/setup), és [állítsa be a környezetet](./detect-motion-emit-events-quickstart.md?pivots=programming-language-csharp#set-up-your-development-environment)

Ezen lépések végén az Azure-előfizetésében üzembe helyezett Azure-erőforrásokkal fog rendelkezni:

* Azure IoT Hub
* Azure Storage-fiók
* Azure Media Services fiók
* Linux rendszerű virtuális gép az Azure-ban, telepített [IoT Edge futtatókörnyezettel](../../iot-edge/how-to-install-iot-edge.md)

> [!TIP]
> Ha a létrehozott Azure-erőforrásokkal kapcsolatos problémákba ütközik, tekintse meg a **[hibaelhárítási útmutatót](troubleshoot-how-to.md#common-error-resolutions)** a gyakran előforduló problémák megoldásához.

## <a name="concepts"></a>Alapelvek

Az Event-alapú videofelvétel arra utal, hogy az esemény által aktivált videók rögzítése folyamatban van. Ez az esemény a következőből hozható létre:
- Maga a videojel feldolgozása, például amikor egy mozgó objektumot észlel a videóban.
- Egy független forrás, például egy ajtó megnyitása. 

Azt is megteheti, hogy csak akkor aktiválja a rögzítést, ha egy következtetési szolgáltatás észleli, hogy egy adott esemény történt. Ebben az oktatóanyagban egy autópályán mozgó és videós klipeket tartalmazó videót fog használni, amikor egy teherautót észlelnek.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/event-based-video-recording-tutorial/overview.svg" alt-text="Médiagrafikon":::

A diagram a [Media Graph](media-graph-concept.md) képi ábrázolása és a kívánt forgatókönyvet megvalósító további modulok. Négy IoT Edge modult érint:

* Élő videó-elemzés egy IoT Edge modulon.
* Egy Edge-modul, amely egy HTTP-végpont mögötti AI-modellt futtat. Ez az AI-modul a [YOLOv3](https://github.com/Azure/live-video-analytics/tree/master/utilities/video-analysis/yolov3-onnx) modellt használja, amely számos típusú objektumot képes felderíteni.
* Az objektumok számlálására és szűrésére szolgáló egyéni modul, amelyet a diagram objektum-számlálójának nevezünk. Ebben az oktatóanyagban létrehoz egy objektum-számlálót, és telepíti azt.
* Egy [RTSP szimulátor-modul](https://github.com/Azure/live-video-analytics/tree/master/utilities/rtspsim-live555) , amely egy RTSP-kamerát szimulál.
    
Ahogy az ábrán látható, egy RTSP- [forrás](media-graph-concept.md#rtsp-source) csomópontot fog használni a Media Graphban, hogy rögzítse a szimulált élő videót egy autópályán, és küldje el a videót két elérési útra:

* Az első útvonal egy HTTP-bővítmény csomópont. A csomópont a mező használatával a képkockákat egy beállított értékre, `samplingOptions` majd a képkockákat képként továbbítja az AI-modul YOLOv3, amely egy objektum-detektor. A csomópont fogadja az eredményeket, amelyek a modell által észlelt objektumok (a forgalomban lévő járművek). A HTTP-bővítmény csomópont ezt követően közzéteszi az eredményeket az IoT Hub Message fogadó csomóponton keresztül az IoT Edge hubhoz.
* A objectCounter modul úgy van beállítva, hogy üzeneteket fogadjon az IoT Edge hubhoz, amely tartalmazza az objektum-észlelési eredményeket (a forgalomban lévő járműveket). A modul ellenőrzi ezeket az üzeneteket, és egy adott típusú objektumokat keres, amelyeket egy beállítással konfiguráltak. Ha ilyen objektum található, a modul üzenetet küld az IoT Edge hubhoz. Az "objektum található" üzeneteket a rendszer átirányítja a Media Graph IoT Hub forrás csomópontjára. Ilyen üzenet fogadásakor a Media Graph IoT Hub forrás csomópontja elindítja a [Signal Gate processzor](media-graph-concept.md#signal-gate-processor) csomópontját. A Signal Gate processzor csomópontja ekkor megnyílik a beállított időtartamra. A videó a kapun keresztül áramlik át az eszköz fogadó csomópontjára az adott időtartamra. Az élő adatfolyam ezen részét a rendszer az [eszköz](media-graph-concept.md#asset-sink) fogadó csomópontján keresztül rögzíti a Azure Media Services fiókjában lévő egyik [eszközre](terminology.md#asset) .

## <a name="set-up-your-development-environment"></a>A fejlesztési környezet beállítása

Mielőtt elkezdené, győződjön meg róla, hogy végrehajtotta a harmadik felsorolásjelet az [Előfeltételekben](#prerequisites). Az erőforrás-telepítési parancsfájl befejeződése után válassza ki a kapcsos zárójeleket, hogy elérhetővé tegye a mappastruktúrát. A ~/clouddrive/LVA-Sample könyvtár alatt létrehozott néhány fájl megjelenik.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/quickstarts/clouddrive.png" alt-text="Alkalmazásbeállítások":::

Ebben az oktatóanyagban a következő fájlok szerepelnek:

* **~/clouddrive/LVA-Sample/Edge-Deployment/.env**: olyan tulajdonságokat tartalmaz, amelyeket a Visual Studio Code használ a modulok peremhálózati eszközre való telepítéséhez.
* **~/clouddrive/lva-sample/appsetting.json**: a mintakód futtatásához a Visual Studio Code használatos.

Ezekre a lépésekre szüksége lesz a fájlokra.

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
    VIDEO_INPUT_FOLDER_ON_DEVICE="/home/lvaedgeuser/samples/input"  
    VIDEO_OUTPUT_FOLDER_ON_DEVICE="/var/media"  
    APPDATA_FOLDER_ON_DEVICE="/var/local/mediaservices"
    CONTAINER_REGISTRY_USERNAME_myacr="<your container registry username>"  
    CONTAINER_REGISTRY_PASSWORD_myacr="<your container registry username>"      
    ```

## <a name="examine-the-template-file"></a>A sablon fájljának vizsgálata 

Az előző lépésben elindította a Visual Studio Code-ot, és megnyitotta a mintakód-t tartalmazó mappát.

A Visual Studio Code-ban keresse meg az src/Edge lehetőséget. Ekkor megjelenik a létrehozott. env fájl, és néhány központi telepítési sablon fájl. Ez a sablon határozza meg, hogy mely peremhálózati modulok lesznek üzembe helyezhetők a peremhálózati eszközön (az Azure Linux virtuális gépen). A. env fájl tartalmazza a sablonokban használt változók értékeit, például a Media Services hitelesítő adatokat.

Az src/Edge/deployment.objectCounter.template.jsmegnyitása a következőn:. A **modulok** szakasz négy bejegyzést tartalmaz, amelyek megfelelnek az előző "fogalmak" szakaszban felsorolt elemeknek:

* **lvaEdge**: az élő videó Analytics IoT Edge modulon.
* **yolov3**: Ez a YOLO v3 modell használatával létrehozott AI-modul.
* **rtspsim**: ez az RTSP-szimulátor.
* **objectCounter**: ez az a modul, amely a yolov3 eredményeiből származó adott objektumokat keresi.

A objectCounter modul esetében tekintse meg a "rendszerkép" értékhez használt karakterláncot ($ {modules. objectCounter}). Ez a IoT Edge modul fejlesztésének [oktatóanyagán](../../iot-edge/tutorial-develop-for-linux.md) alapul. A Visual Studio Code automatikusan felismeri, hogy a objectCounter modul kódja az src/Edge/modules/objectCounter. 

Olvassa el [ezt a szakaszt](../../iot-edge/module-composition.md#declare-routes) az útvonalak deklarálása a IoT Edge telepítési jegyzékben. Ezután vizsgálja meg az útvonalakat a sablon JSON-fájljában. Vegye figyelembe a következőket:

* A LVAToObjectCounter adott események küldésére szolgál egy adott végpontra a objectCounter modulban.
* A ObjectCounterToLVA egy eseményindító-eseménynek egy adott végpontra való küldésére szolgál (amely az IoT Hub forrás csomópontja) a lvaEdge modulban.
* a objectCounterToIoTHub hibakeresési eszközként használható, amely segít megtekinteni a objectCounter kimenetét az oktatóanyag futtatásakor.

> [!NOTE]
> Ellenőrizze a objectCounter modul kívánt tulajdonságait, amelyek úgy vannak beállítva, hogy a "Truck" címkével rendelkező objektumokat megkeressék legalább 50%-os megbízhatósági szinttel.

## <a name="generate-and-deploy-the-iot-edge-deployment-manifest"></a>Az IoT Edge telepítési jegyzék előállítása és üzembe helyezése 

Az üzembe helyezési jegyzék meghatározza, hogy milyen modulok vannak üzembe helyezve egy peremhálózati eszközön és a modulok konfigurációs beállításaiban. Kövesse az alábbi lépéseket egy jegyzékfájl létrehozásához a sablonból, majd telepítse azt a peremhálózati eszközre.

A Visual Studio Code használatával a Docker-be való bejelentkezéshez kövesse az [alábbi utasításokat](../../iot-edge/tutorial-develop-for-linux.md#build-and-push-your-solution) . Ezután válassza **a létrehozás és Leküldés IoT Edge megoldást**. Ehhez a lépéshez használja az src/Edge/deployment.objectCounter.template.js-t.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/event-based-video-recording-tutorial/build-push.png" alt-text="IoT Edge-megoldás létrehozása és leküldése":::

Ez a művelet létrehozza a objectCounter modult az objektumok számlálásához, és leküldi a rendszerképet a Azure Container Registry.

* Győződjön meg arról, hogy rendelkezik a környezeti változókkal CONTAINER_REGISTRY_USERNAME_myacr és CONTAINER_REGISTRY_PASSWORD_myacr a. env fájlban.

Ez a lépés létrehozza a IoT Edge üzembe helyezési jegyzéket az src/Edge/config/deployment.objectCounter.amd64.json címen. Kattintson a jobb gombbal a fájlra, és válassza **a központi telepítés létrehozása egyetlen eszközhöz** lehetőséget.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/quickstarts/create-deployment-single-device.png" alt-text="Üzemelő példány létrehozása egyetlen eszközhöz":::

Ha ez az első oktatóanyag a IoT Edge Live Video Analytics szolgáltatással, a Visual Studio Code felszólítja, hogy adja meg a IoT Hub kapcsolódási karakterláncot. A fájlt átmásolhatja a appsettings.jsfájlból.

> [!NOTE]
> Előfordulhat, hogy a rendszer megkéri, hogy adjon meg egy beépített végponti információt a IoT Hub számára. Az információk lekéréséhez Azure Portalban navigáljon a IoT Hub, és keresse meg a **beépített végpontok** lehetőséget a bal oldali navigációs panelen. Kattintson ide, és az Event hub-kompatibilis **végpont** szakaszban keresse meg az **Event hub-kompatibilis végpontot** . Másolja ki és használja a szövegmezőben található szöveget. A végpont így fog kinézni:  
    ```
    Endpoint=sb://iothub-ns-xxx.servicebus.windows.net/;SharedAccessKeyName=iothubowner;SharedAccessKey=XXX;EntityPath=<IoT Hub name>
    ```

Ezután a Visual Studio Code egy IoT Hub eszköz kiválasztását kéri. Válassza ki IoT Edge eszközét, amely LVA-minta-eszköz lehet.

Ebben a szakaszban az Edge-modulok üzembe helyezése a IoT Edge eszközön megkezdődött.
Körülbelül 30 másodperc alatt frissítse az Azure IoT Hubt a Visual Studio Code bal alsó részén. Látnia kell, hogy a lvaEdge, a rtspsim, a yolov3 és a objectCounter nevű négy modul van üzembe helyezve.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/event-based-video-recording-tutorial/iot-hub.png" alt-text="Négy modul üzembe helyezése":::

## <a name="prepare-for-monitoring-events"></a>Felkészülés a figyelési eseményekre

A objectCounter modul eseményeinek és az élő videó Analytics IoT Edge modulon való megtekintéséhez kövesse az alábbi lépéseket:

1. Nyissa meg az Explorer panelt a Visual Studio Code-ban, és keresse meg az **Azure IoT hub** a bal alsó sarokban.
1. Bontsa ki az **eszközök** csomópontot.
1. Kattintson a jobb gombbal a LVA-Sample-Device fájlra, és válassza a **figyelés beépített esemény végpontja** lehetőséget.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/quickstarts/start-monitoring-iothub-events.png" alt-text="A beépített esemény-végpont figyelésének megkezdése":::

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
1. <!--In Visual Studio Code, go-->Lépjen a src/Cloud-to-Device-Console-app/operations.jselemre.
1. A **GraphTopologySet** csomópont alatt szerkessze a következőket:

    `"topologyUrl" : "https://raw.githubusercontent.com/Azure/live-video-analytics/master/MediaGraph/topologies/evr-hubMessage-assets/2.0/topology.json"`
    
1. Ezután a **GraphInstanceSet** és az **GraphTopologyDelete** csomópont alatt szerkessze a következőt:

    `"topologyName" : "EVRtoAssetsOnObjDetect"`
1. Indítsa el a hibakeresési munkamenetet az F5 billentyű kiválasztásával. Néhány üzenet jelenik meg a **terminál** ablakban.
1. A fájl operations.jselindul a GraphTopologyList és a GraphInstanceList hívásával. Ha az előző rövid útmutatók vagy oktatóanyagok után törölte az erőforrásokat, akkor ez a művelet üres listát ad vissza, és szünetelteti az **ENTER billentyű** lenyomását, amint az alábbi ábrán látható:

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
            "topologyName": "EVRtoAssetsOnObjDetect",
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
1. A Graph-példány továbbra is fut, és rögzíti a videót. Az RTSP-szimulátor megtartja a forrás videóját. Tekintse át az üzeneteket a következő szakaszban leírtak szerint. Ezután a példány leállításához lépjen vissza a **Terminálablak** ablakába, és válassza az **ENTER billentyűt**. A következő hívási sorozat az erőforrások törlését végzi a használatával:

   * A Graph-példány inaktiválására irányuló GraphInstanceDeactivate hívása.
   * A példány törlésére irányuló GraphInstanceDelete-hívás.
   * A topológia törlésére irányuló GraphTopologyDelete-hívás.
   * A GraphTopologyList végső hívása annak bemutatására, hogy a lista már üres.

## <a name="interpret-the-results"></a>Az eredmények értelmezése 

A Media Graph futtatásakor az élő videó Analytics IoT Edge modulban bizonyos diagnosztikai és műveleti eseményeket küld az IoT Edge hubhoz. Ezek az események a Visual Studio Code **kimeneti** ablakában látható üzenetek. A törzs szakaszt és egy applicationProperties szakaszt tartalmaznak. A következő fejezeteinek megismeréséhez lásd: [IoT hub üzenetek létrehozása és olvasása](../../iot-hub/iot-hub-devguide-messages-construct.md).

A következő üzenetekben az alkalmazás tulajdonságait és a törzs tartalmát az élő videó elemzési modulja határozza meg.

## <a name="diagnostics-events"></a>Diagnosztikai események

### <a name="mediasessionestablished-event"></a>MediaSessionEstablished esemény 

Az adathordozó-diagramok példányainak létrehozásakor az RTSP-forrás csomópontja megpróbál csatlakozni az RTSP szimulátor-tárolón futó RTSP-kiszolgálóhoz. Ha a művelet sikeres, kinyomtatja ezt az eseményt. Az esemény típusa **Microsoft. Media. MediaGraph. Diagnostics. MediaSessionEstablished**.

```
[IoTHubMonitor] [5:53:17 PM] Message received from [lva-sample-device/lvaEdge]:
{
  "body": {
    "sdp&quot;: &quot;SDP:\nv=0\r\no=- 1586450538111534 1 IN IP4 XXX.XX.XX.XX\r\ns=Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\ni=media/camera-300s.mkv\r\nt=0 0\r\na=tool:LIVE555 Streaming Media v2020.03.06\r\na=type:broadcast\r\na=control:*\r\na=range:npt=0-300.000\r\na=x-qt-text-nam:Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\na=x-qt-text-inf:media/camera-300s.mkv\r\nm=video 0 RTP/AVP 96\r\nc=IN IP4 0.0.0.0\r\nb=AS:500\r\na=rtpmap:96 H264/90000\r\na=fmtp:96 packetization-mode=1;profile-level-id=4D0029;sprop-parameter-sets=XXXXXXXXXXXXXXXXXXXXXX\r\na=control:track1\r\n"
  },
  "applicationProperties": {
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{resource-group-name}/providers/microsoft.media/mediaservices/{ams-account-name}",
    "subject": "/graphInstances/Sample-Graph-1/sources/rtspSource",
    "eventType": "Microsoft.Media.Graph.Diagnostics.MediaSessionEstablished",
    "eventTime": "2020-05-17T17:53:16.981Z",
    "dataVersion": "1.0"
  }
}
```


* Az üzenet egy diagnosztikai esemény (MediaSessionEstablished). Azt jelzi, hogy az RTSP-forrás csomópontja (a tárgy) létrehozta a kapcsolódást az RTSP-szimulátorral, és megkezdte a (szimulált) élő adatcsatorna fogadását.
* A applicationProperties tárgy szakasza a Graph-topológia azon csomópontjára hivatkozik, amelyről az üzenet létrejött. Ebben az esetben az üzenet az RTSP forrás csomópontból származik.
* A applicationProperties eventType szakasza azt jelzi, hogy ez egy diagnosztikai esemény.
* A eventTime szakasz azt az időpontot jelzi, amikor az esemény bekövetkezett. Ez az az idő, amikor a forgalmi videó (MKV-fájl) megkezdte a modulnak élő streamként való megérkezését.
* A Body (törzs) szakasz a diagnosztikai eseménnyel kapcsolatos adatokat tartalmaz, amelyek ebben az esetben a [SDP](https://en.wikipedia.org/wiki/Session_Description_Protocol) adatai.


## <a name="operational-events"></a>Működési események

Ha a Media Graph egy ideig fut, akkor végül egy eseményt fog kapni a objectCounter modulból. 

```
[IoTHubMonitor] [5:53:44 PM] Message received from [lva-sample-device/objectCounter]:
{
  "body": {
    "count": 2
  },
  "applicationProperties": {
    "eventTime&quot;: &quot;2020-05-17T17:53:44.062Z"
  }
}
```

A applicationProperties szakasz az esemény időpontját tartalmazza. Ez az az idő, amikor a objectCounter modul megfigyelte, hogy a yolov3 modul eredményei (teherautók) tartalmaznak.

Előfordulhat, hogy a videóban több ilyen esemény jelenik meg, ahogy más teherautók is észlelhetők.

### <a name="recordingstarted-event"></a>RecordingStarted esemény

Szinte azonnal, miután az objektum számlálója elküldi az eseményt, a **Microsoft. Media. Graph. Operational. RecordingStarted** típusú eseményt fogja látni:

```
[IoTHubMonitor] [5:53:46 PM] Message received from [lva-sample-device/lvaEdge]:
{
  "body": {
    "outputType": "assetName",
    "outputLocation&quot;: &quot;sampleAssetFromEVR-LVAEdge-20200517T175346Z"
  },
  "applicationProperties": {
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{resource-group-name}/providers/microsoft.media/mediaservices/{ams-account-name}",
    "subject": "/graphInstances/Sample-Graph-1/sinks/assetSink",
    "eventType": "Microsoft.Media.Graph.Operational.RecordingStarted",
    "eventTime": " 2020-05-17T17:53:46.132Z",
    "dataVersion&quot;: &quot;1.0"
  }
}
```

A applicationProperties tárgy szakasza a gráfban található, az üzenetet generáló objektum fogadó csomópontjára hivatkozik. A Body (törzs) szakasz a kimeneti helyről tartalmaz információkat. Ebben az esetben annak a Azure Media Services-objektumnak a neve, amelybe a videó rögzítve lesz. Jegyezze fel ezt az értéket.

### <a name="recordingavailable-event"></a>RecordingAvailable esemény

Amikor az eszköz befogadó csomópontja videót töltött fel az objektumra, a **Microsoft. Media. Graph. Operational. RecordingAvailable** típusú eseményt bocsát ki:

```
[IoTHubMonitor] [5:54:15 PM] Message received from [lva-sample-device/lvaEdge]:
{
  "body": {
    "outputType": "assetName",
    "outputLocation&quot;: &quot;sampleAssetFromEVR-LVAEdge-20200517T175346Z"
  },
  "applicationProperties": {
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{resource-group-name}/providers/microsoft.media/mediaservices/{ams-account-name}",
    "subject": "/graphInstances/Sample-Graph-1/sinks/assetSink",
    "eventType": "Microsoft.Media.Graph.Operational.RecordingAvailable",
    "eventTime": "2020-05-17T17:54:15.808Z",
    "dataVersion&quot;: &quot;1.0"
  }
}
```

Ez az esemény azt jelzi, hogy a játékosok vagy ügyfelek számára elegendő mennyiségű adattal rendelkező eszköz lett írva a videó lejátszásának megkezdéséhez. A applicationProperties tárgy szakasza a gráf AssetSink csomópontjára hivatkozik, amely létrehozta ezt az üzenetet. A Body (törzs) szakasz a kimeneti helyről tartalmaz információkat. Ebben az esetben annak a Azure Media Services-objektumnak a neve, amelybe a videó rögzítve lesz.

### <a name="recordingstopped-event"></a>RecordingStopped esemény

Ha megvizsgálja az aktiválási beállításokat (maximumActivationTime) a [topológia](https://github.com/Azure/live-video-analytics/tree/master/MediaGraph/topologies/evr-hubMessage-assets/topology.json)Signal Gate processzor-csomópontja számára, látni fogja, hogy a kapu úgy van beállítva, hogy a videó elküldése után 30 másodperc elteltével lezáruljon. Nagyjából 30 másodperccel a RecordingStarted esemény után a **Microsoft. Media. Graph. Operational. RecordingStopped** típusú eseményt kell látnia. Ez az esemény azt jelzi, hogy az eszköz fogadó csomópontja leállította a videó rögzítését az eszközre.

```
[IoTHubMonitor] [5:54:15 PM] Message received from [lva-sample-device/lvaEdge]:
{
  "body": {
    "outputType": "assetName",
    "outputLocation&quot;: &quot;sampleAssetFromEVR-LVAEdge-20200517T175346Z"
  },
  "applicationProperties": {
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{resource-group-name}/providers/microsoft.media/mediaservices/{ams-account-name}",
    "subject": "/graphInstances/Sample-Graph-1/sinks/assetSink",
    "eventType": "Microsoft.Media.Graph.Operational.RecordingStopped",
    "eventTime": "2020-05-17T17:54:15.040Z",
    "dataVersion&quot;: &quot;1.0"
  }
}
```

Ez az esemény azt jelzi, hogy a rögzítés leállt. A applicationProperties tárgy szakasza a gráf AssetSink csomópontjára hivatkozik, amely létrehozta ezt az üzenetet. A Body (törzs) szakasz a kimeneti helyről tartalmaz információkat. Ebben az esetben annak a Azure Media Services-objektumnak a neve, amelybe a videó rögzítve lesz.

## <a name="media-services-asset"></a>Media Services eszköz  

Megvizsgálhatja a gráf által létrehozott Media Services adategységet, ha bejelentkezik a Azure Portalba, és megtekinti a videót.

1. Nyissa meg a webböngészőt, és lépjen a [Azure Portal](https://portal.azure.com/). Adja meg a hitelesítő adatait a Portalra való bejelentkezéshez. Az alapértelmezett nézet a szolgáltatási irányítópult.
1. Keresse meg Media Services-fiókját az előfizetésében található erőforrások között. Nyissa meg a fiók ablaktáblát.
1. Válassza az **eszközök** lehetőséget a **Media Services** listában.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/continuous-video-recording-tutorial/assets.png" alt-text="Folyamatos videófelvétel":::
1. Itt található egy sampleAssetFromEVR-LVAEdge-{DateTime} nevű eszköz. Ez a RecordingStarted esemény outputLocation tulajdonságában megadott név. A topológia assetNamePattern határozza meg a név létrejöttének módját.
1. Válassza ki az objektumot.
1. Az eszköz adatai lapon válassza az **új létrehozása** lehetőséget a **streaming URL-cím** szövegmezőben.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/continuous-video-recording-tutorial/new-asset.png" alt-text="Új eszköz":::
1. A megnyíló varázslóban fogadja el az alapértelmezett beállításokat, majd válassza a **Hozzáadás** lehetőséget. [További információ: videolejátszás](video-playback-concept.md).

    > [!TIP]
    > Ellenőrizze, hogy [fut-e a folyamatos átviteli végpont](../latest/streaming-endpoint-concept.md).
1. A lejátszónak be kell töltenie a videót. Kattintson a **Lejátszás** gombra a megtekintéshez.

> [!NOTE]
> Mivel a videó forrása egy kamera-hírcsatornát szimuláló tároló volt, a videóban szereplő időbélyegek a Graph-példány aktiválásakor és az inaktiválásakor kapcsolódnak. Ha a [Többnapos felvételek lejátszásához](playback-multi-day-recordings-tutorial.md) beépített lejátszási vezérlőket használja, láthatja a képernyőn megjelenő videó időbélyegeit.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha szeretné kipróbálni a többi oktatóanyagot, tartsa be a létrehozott erőforrásokat. Ellenkező esetben lépjen a Azure Portal, keresse meg az erőforráscsoportot, válassza ki azt az erőforráscsoportot, amelyben az oktatóanyagot futtatta, és törölje az erőforráscsoportot.

## <a name="next-steps"></a>Következő lépések

* Használjon olyan [IP-kamerát](https://en.wikipedia.org/wiki/IP_camera) , amely támogatja az RTSP-t az RTSP-szimulátor használata helyett. Az ONVIF-kompatibilis [termékek lapon](https://www.onvif.org/conformant-products/) megkeresheti az RTSP-támogatással rendelkező IP-kamerákat a G, S vagy T profiloknak megfelelő eszközök keresésével.
* AMD64 vagy x64 Linux rendszerű eszköz használata (Azure Linux rendszerű virtuális gép használata). Az eszköznek ugyanabban a hálózaton kell lennie, mint az IP-kamerának. Kövesse a következő témakör utasításait: [Install Azure IoT Edge Runtime on Linux](../../iot-edge/how-to-install-iot-edge.md). Ezután kövesse az [első IoT Edge modul üzembe helyezése virtuális Linux-eszközre című](../../iot-edge/quickstart-linux.md) rövid útmutatót az eszköz Azure IoT hub való regisztrálásához.