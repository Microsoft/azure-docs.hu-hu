---
title: Élő videó elemzése a OpenVINO™ Model Server – AI bővítménnyel az Intel használatával
description: Ebben az oktatóanyagban az Intel által biztosított AI-modell-kiszolgálót fogja használni az élő videó hírcsatornájának (szimulált) IP-kamerából való elemzéséhez.
ms.topic: tutorial
ms.date: 09/08/2020
titleSuffix: Azure
ms.openlocfilehash: db018c5c8d8f3990fd465f4d586ef4dc70980542
ms.sourcegitcommit: 4e70fd4028ff44a676f698229cb6a3d555439014
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/28/2021
ms.locfileid: "98955716"
---
# <a name="tutorial-analyze-live-video-by-using-openvino-model-server--ai-extension-from-intel"></a>Oktatóanyag: élő videó elemzése a OpenVINO™ Model Server – AI bővítménnyel az Intel használatával 

Ebből az oktatóanyagból megtudhatja, hogyan használhatja a OpenVINO™ Model Server – AI bővítményt az Intel rendszerből egy élő videó-hírcsatorna (szimulált) IP-kamerából való elemzéséhez. Láthatja, hogy ez a következtetési kiszolgáló Hogyan férhet hozzá az objektumok (egy személy, egy jármű vagy egy kerékpár) észleléséhez és a járművek osztályozásához szükséges modellekhez. A rendszer elküldi az élő videó-hírcsatornában található keretek egy részhalmazát, és elküldi az eredményeket IoT Edge hubhoz.

Ez az oktatóanyag egy Azure-beli virtuális gépet használ IoT Edge eszközként, és szimulált élő videó streamet használ. Ez a C# nyelven írt mintakód alapján készült, amely az [észlelési és a kibocsátási események](detect-motion-emit-events-quickstart.md) gyors üzembe helyezésére épül.

> [!NOTE]
> Ehhez az oktatóanyaghoz egy x86-64 számítógép használatát kell használni peremhálózati eszközként.

## <a name="prerequisites"></a>Előfeltételek

* Egy aktív előfizetést tartalmazó Azure-fiók. [Hozzon létre egy fiókot ingyenesen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) , ha még nem rendelkezik ilyennel.
  > [!NOTE]
  > Szüksége lesz egy Azure-előfizetésre, amely az egyszerű szolgáltatások létrehozásához szükséges engedélyekkel rendelkezik (ez a **tulajdonosi szerepkör** ). Ha nem rendelkezik a megfelelő engedélyekkel, lépjen kapcsolatba a fiók rendszergazdájával, és adja meg a megfelelő engedélyeket. 
* [Visual Studio Code](https://code.visualstudio.com/)a következő kiterjesztésekkel:
    * [Azure IoT-eszközök](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)
    * [C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)
* [.Net Core 3,1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1).
* Ha nem fejezte be a [mozgás észlelése és az események kibocsátása](detect-motion-emit-events-quickstart.md) című rövid útmutatót, akkor mindenképpen hajtsa végre az [Azure-erőforrások beállításának](detect-motion-emit-events-quickstart.md#set-up-azure-resources)lépéseit.

> [!TIP]
> Az Azure IoT-eszközök telepítésekor előfordulhat, hogy a rendszer a Docker telepítésére kéri. A promptot figyelmen kívül hagyhatja.

## <a name="review-the-sample-video"></a>A minta videó áttekintése

Az Azure-erőforrások beállításakor a rendszer az Azure-beli linuxos virtuális gépre másol egy rövid videót, amelyet IoT Edge eszközként használ. Ez a rövid útmutató egy élő stream szimulálására használja a videofájl használatával.

Nyisson meg egy alkalmazást, például a [VLC Media Playert](https://www.videolan.org/vlc/). Válassza a CTRL + N billentyűkombinációt, majd illessze be a [videóra](https://lvamedia.blob.core.windows.net/public/lots_015.mkv) mutató hivatkozást a lejátszás elindításához. Megtekintheti a járművek felvételeit egy parkolóban, a legtöbbjük parkolt, és egy mozgó.

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4LUbN]

Ebben a rövid útmutatóban élő videó-elemzéseket fog használni a IoT Edge, valamint a OpenVINO™ Model Server – AI bővítmény az Inteltől az objektumok, például a gépjárművek észlelése vagy a besorolása érdekében. Az eredményül kapott események közzétételét IoT Edge hubhoz teszi közzé.

## <a name="overview"></a>Áttekintés

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/use-intel-openvino-tutorial/http-extension-with-vino.svg" alt-text="Áttekintés":::

Ez az ábra azt mutatja be, hogyan áramlik be a gyors útmutatóban szereplő jelek. Az [Edge-modul](https://github.com/Azure/live-video-analytics/tree/master/utilities/rtspsim-live555) szimulál egy Real-Time Streaming Protocol-(RTSP-) kiszolgálót futtató IP-kamerát. Az [RTSP-forrás](media-graph-concept.md#rtsp-source) csomópontja lekéri a videó csatornáját a kiszolgálóról, és a [http-bővítmény processzor](media-graph-concept.md#http-extension-processor) -csomópontjára küldi a videó kereteket. 

A HTTP-bővítmény csomópont egy proxy szerepét játssza le. Az IT-csoport az Ön által beállított beérkező képkockákat is felveszi, `samplingOptions` és a képkockákat a megadott képtípusra konvertálja. Ezt követően továbbítja a képet a REST-ben egy másik peremhálózati modulba, amely egy HTTP-végpont mögötti AI-modelleket futtat. Ebben a példában ez az Edge-modul a OpenVINO™ Model Server – AI bővítmény az Inteltől. A HTTP-bővítmény processzor-csomópontja összegyűjti az észlelés eredményeit, és közzéteszi az eseményeket a [IoT hub](media-graph-concept.md#iot-hub-message-sink) fogadó csomópontban. A csomópont ezután elküldi ezeket az eseményeket [IoT Edge hubhoz](../../iot-edge/iot-edge-glossary.md#iot-edge-hub).

Az oktatóanyagban a következőket végezheti el:

1. Hozza létre és telepítse a Media Graphot, és módosítsa azt.
1. Az eredmények értelmezése.
1. Az erőforrások eltávolítása.

## <a name="about-openvino-model-server--ai-extension-from-intel"></a>A OpenVINO™ Model Server – AI bővítmény az Inteltől

A [OpenVINO™ Toolkit](https://software.intel.com/content/www/us/en/develop/tools/openvino-toolkit.html) (nyílt vizuális következtetések és neurális hálózati optimalizálás) Intel® eloszlása egy ingyenes szoftvercsomag, amely segít a fejlesztőknek és az adatszakértőknek felgyorsítani a számítógépes vizualizációk számítási feladatait, egyszerűsíteni a mélyebb tanulási következtetéseket és üzembe helyezéseket, és lehetővé teszi a könnyű és heterogén végrehajtást az Intel® platformok között a szélétől a felhőbe. Magában foglalja az Intel® Deep learning üzembe helyezési eszközkészletét a modell-optimalizáló és a következtetési motorral, valamint az [Open Model Zoo](https://github.com/openvinotoolkit/open_model_zoo) -tárházat, amely több mint 40 optimalizált, előre betanított modellt tartalmaz.

Összetett, nagy teljesítményű élő videós elemzési megoldások létrehozásához a IoT Edge modul élő videó-elemzését egy olyan hatékony következtetési motorral kell párosítani, amely képes kihasználni a skálát a peremen. Ebben az oktatóanyagban a következtetési kérelmeket a rendszer elküldi a [OpenVINO™ Model Server – AI bővítménynek az Inteltől](https://aka.ms/lva-intel-ovms), egy Edge-modulból, amely a IoT Edge élő videó-elemzéssel való működésre lett tervezve. Ez a következtetési kiszolgálói modul tartalmazza a OpenVINO™ Model Servert (OVMS), amely a OpenVINO™ Toolkit által működtetett következtetési kiszolgáló, amely a számítógép-vizualizációs számítási feladatokhoz van optimalizálva, és az Intel® architektúrák számára lett kifejlesztve. A rendszer hozzáad egy bővítményt az OVMS-hez a képkeretek egyszerű cseréjéhez és a következtetési eredményekhez a következtetési kiszolgáló és a IoT Edge modul élő videó-elemzései között, így lehetővé téve, hogy bármely OpenVINO-™ eszközkészlet által támogatott modellt futtasson (a [kód](https://github.com/openvinotoolkit/model_server/tree/master/extras/ams_wrapper)módosításával testre szabhatja a következtetési kiszolgáló modulját). Az Intel® hardverei által biztosított gyorsítási mechanizmusok széles választéka közül választhat. Ezek közé tartoznak a processzorok (atom, Core, Xeon), a FPGA, a VPUs.

A következtetési kiszolgáló kezdeti kiadásában a következő [modellek](https://github.com/openvinotoolkit/model_server/tree/master/extras/ams_models)érhetők el:

- Jármű észlelése (következtetési URL: http://{Module-Name}: 4000/vehicleDetection)
- Személy/jármű/kerékpár észlelése (következtetési URL: http://{Module-Name}: 4000/personVehicleBikeDetection)
- Jármű besorolása (következtetés URL-címe: http://{Module-Name}: 4000/vehicleClassification)
- Arcfelismerés (következtetés URL-címe: http://{Module-Name}: 4000/faceDetection)

> [!NOTE]
> Az Edge-modul letöltésével és használatával: OpenVINO™ Model Server – AI-bővítmény az Intelből és a mellékelt szoftverből elfogadja a [licencszerződésben](https://www.intel.com/content/www/us/en/legal/terms-of-use.html)foglalt feltételeket és kikötéseket.
> Az Intel elkötelezte magát az emberi jogok tiszteletben tartásával és az emberi jogok megsértésével kapcsolatos bűnrészesség elkerülésével. Lásd: az [Intel globális emberi jogi alapelvei](https://www.intel.com/content/www/us/en/policy/policy-human-rights.html). Az Intel termékei és szoftverei kizárólag olyan alkalmazásokban használhatók, amelyek nem okozzák vagy nem járulnak hozzá a nemzetközileg elismert emberi jog megsértéséhez.

## <a name="create-and-deploy-the-media-graph"></a>A Media Graph létrehozása és üzembe helyezése

### <a name="examine-and-edit-the-sample-files"></a>A mintaadatok vizsgálata és szerkesztése

Az előfeltételek részeként letöltötte a mintakód egy mappába. A mintaadatok vizsgálatához és szerkesztéséhez kövesse az alábbi lépéseket.

1. A Visual Studio Code-ban lépjen az *src/Edge* elemre. Ekkor megjelenik a *. env* fájl és néhány központi telepítési sablon fájl.

    A központi telepítési sablon a peremhálózati eszköz üzembe helyezési jegyzékére hivatkozik. Tartalmaz néhány helyőrző értéket. A *. env* fájl tartalmazza a változók értékeit.

1. Lépjen a *src/Cloud-to-Device-Console-app* mappába. Itt láthatja a *appsettings.js* fájlt, és néhány további fájlt:

    * ***C2D-Console-app. csproj** _ – a Project fájl a Visual Studio Code-hoz.
    _ ***operations.json** _ – azoknak a műveleteknek a listája, amelyeket futtatni kíván a program.
    _ ***Program.cs** _ – a minta programkódja. Ez a kód:

        _ Betölti az alkalmazás beállításait.
        * Közvetlen metódusokat hív meg, amelyeket az élő videó Analytics IoT Edge modulban tesz elérhetővé. A modul segítségével elemezheti az élő videó streameket a [közvetlen metódusok](direct-methods.md)meghívásával.
        * Szünetelteti, hogy megvizsgálhatja a program kimenetét a **terminál** ablakban, és megvizsgálhatja a modul által a **kimeneti** ablakban generált eseményeket.
        * Közvetlen metódusokat hív meg az erőforrások törléséhez.


1. Szerkessze a *operations.js* fájlt:
    * Módosítsa a Graph-topológiára mutató hivatkozást:

        `"topologyUrl" : "https://raw.githubusercontent.com/Azure/live-video-analytics/master/MediaGraph/topologies/httpExtensionOpenVINO/2.0/topology.json"`

    * A (z) alatt `GraphInstanceSet` szerkessze a gráf topológiájának nevét, hogy az megfeleljen az előző hivatkozásban szereplő értéknek:

      `"topologyName" : "InferencingWithOpenVINO"`

    * `GraphTopologyDelete`A alatt szerkessze a nevet:

      `"name": "InferencingWithOpenVINO"`

### <a name="generate-and-deploy-the-iot-edge-deployment-manifest"></a>Az IoT Edge telepítési jegyzék előállítása és üzembe helyezése

1. Kattintson a jobb gombbal a fájlon a *src/Edge/deployment.openvino.template.js* elemre, majd válassza a **IoT Edge üzembe helyezési jegyzék előállítása** lehetőséget.

    ![IoT Edge üzembe helyezési jegyzék előállítása](./media/use-intel-openvino-tutorial/generate-deployment-manifest.png)  

    A manifest-fájl *deployment.yolov3.amd64.js* a *src/Edge/config* mappában jön létre.

1. Ha befejezte a [mozgás észlelése és a kibocsátó események](detect-motion-emit-events-quickstart.md) rövid útmutatóját, ugorja át ezt a lépést. 

    Ellenkező esetben a bal alsó sarokban található **Azure IOT hub** panel közelében válassza a **További műveletek** ikont, majd válassza a **IoT hub a kapcsolatok karakterláncának beállítása** lehetőséget. A karakterláncot a *appsettings.js* fájlból másolhatja. Vagy a megfelelő IoT hub a Visual Studio Code-ban való konfigurálásának biztosításához használja az [IoT hub kiválasztása parancsot](https://github.com/Microsoft/vscode-azure-iot-toolkit/wiki/Select-IoT-Hub).
    
    ![IoT Hub-kapcsolatok karakterláncának beállítása](./media/quickstarts/set-iotconnection-string.png)

> [!NOTE]
> Előfordulhat, hogy a rendszer megkéri, hogy adjon meg egy beépített végponti információt a IoT Hub számára. Az információk lekéréséhez Azure Portalban navigáljon a IoT Hub, és keresse meg a **beépített végpontok** lehetőséget a bal oldali navigációs panelen. Kattintson ide, és az Event hub-kompatibilis **végpont** szakaszban keresse meg az **Event hub-kompatibilis végpontot** . Másolja ki és használja a szövegmezőben található szöveget. A végpont így fog kinézni:  
    ```
    Endpoint=sb://iothub-ns-xxx.servicebus.windows.net/;SharedAccessKeyName=iothubowner;SharedAccessKey=XXX;EntityPath=<IoT Hub name>
    ```

1. Kattintson a jobb gombbal *az src/Edge/config/deployment.openvino.amd64.js* elemre, és válassza a **központi telepítés létrehozása egyetlen eszközhöz** lehetőséget. 

    ![Központi telepítés létrehozása egyetlen eszközhöz](./media/use-intel-openvino-tutorial/deploy-manifest.png)

1. Amikor a rendszer rákérdez a IoT Hub eszköz kiválasztására, válassza a **LVA-Sample-Device** elemet.
1. Körülbelül 30 másodperc elteltével frissítse az Azure IoT Hubt az ablak bal alsó sarkában. A peremhálózati eszköz mostantól a következő központilag telepített modulokat mutatja:

    * A **lvaEdge** nevű Live Video Analytics-modul
    * Az **rtspsim** modul, amely egy RTSP-kiszolgálót szimulál, és élő videó-hírcsatorna forrásaként működik
    * A **openvino** modul, amely az Intel Openvino™ Model Server – AI bővítményi modulja 

### <a name="prepare-to-monitor-events"></a>Felkészülés az események figyelésére

Kattintson a jobb gombbal a Live Video Analytics-eszközre, és válassza a **figyelés beépített esemény végpontjának elindítása** lehetőséget. Erre a lépésre szüksége lesz a Visual Studio Code **kimeneti** ablakának IoT hub eseményeinek figyeléséhez. 

![Figyelés indítása](./media/quickstarts/start-monitoring-iothub-events.png) 

### <a name="run-the-sample-program-to-detect-vehicles"></a>A minta program futtatása a járművek észleléséhez
Ha megnyitja a [Graph-topológiát](https://raw.githubusercontent.com/Azure/live-video-analytics/master/MediaGraph/topologies/httpExtensionOpenVINO/topology.json) ehhez az oktatóanyaghoz egy böngészőben, látni fogja, hogy a értéke `inferencingUrl` be van állítva `http://openvino:4000/vehicleDetection` , ami azt jelenti, hogy a következtetési kiszolgáló visszaadja az eredményeket az élő videóban található járművek észlelése után.

1. A Visual Studio Code-ban nyissa meg a **bővítmények** lapot (vagy nyomja le a CTRL + SHIFT + X billentyűkombinációt), és keressen rá az Azure IoT hubra.
1. Kattintson a jobb gombbal, és válassza a **bővítmény beállításai** lehetőséget.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/run-program/extensions-tab.png" alt-text="Bővítmény beállításai":::
1. Keresse meg és engedélyezze a "részletes üzenet megjelenítése" lehetőséget.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/run-program/show-verbose-message.png" alt-text="Részletes üzenet megjelenítése":::
1. A hibakeresési munkamenet indításához válassza az F5 billentyűt. A **terminál** ablakban láthatók a kinyomtatott üzenetek.
1. A kód *operations.js* a közvetlen metódusok hívásával indul el `GraphTopologyList` `GraphInstanceList` . Ha az előző rövid útmutatók befejezése után törölte az erőforrásokat, akkor ez a folyamat üres listát ad vissza, majd szünetelteti. A folytatáshoz válassza az ENTER billentyűt.

    A **terminál** ablak a közvetlen metódusok következő készletét jeleníti meg:

     * `GraphTopologySet`Az előzőt használó hívás`topologyUrl`
     * A `GraphInstanceSet` következő törzset használó hívás:

         ```
         {
           "@apiVersion": "2.0",
           "name": "Sample-Graph-1",
           "properties": {
             "topologyName": "InferencingWithOpenVINO",
             "description": "Sample graph description",
             "parameters": [
               {
                 "name": "rtspUrl",
                 "value": "rtsp://rtspsim:554/media/lots_015.mkv"
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

     * A `GraphInstanceActivate` gráf-példányt és a videó folyamatát indító hívás
     * Egy második hívás, `GraphInstanceList` amely azt mutatja, hogy a Graph-példány fut állapotban van
1. A **terminál** ablakban lévő kimenet szünetelteti a `Press Enter to continue` kérdést. Ne jelölje be az ENTER billentyűt. Görgessen felfelé, és tekintse meg a meghívott közvetlen metódusok JSON-válaszának hasznos adatait.
1. Váltson a **kimeneti** ablakra a Visual Studio Code-ban. Láthatja, hogy az élő videó Analytics IoT Edge-modulban az IoT hub-ra küld üzenetet. A rövid útmutató következő szakasza ezeket az üzeneteket tárgyalja.
1. A Media Graph továbbra is fut, és kinyomtatja az eredményeket. Az RTSP-szimulátor megtartja a forrás videóját. A Media Graph leállításához térjen vissza a **terminál** ablakába, és válassza az ENTER billentyűt. 

    A hívások következő sorozata törli az erőforrásokat:
      * A Graph- `GraphInstanceDeactivate` példány inaktiválására irányuló hívás.
      * A `GraphInstanceDelete` példány törlésére irányuló hívás.
      * A `GraphTopologyDelete` topológia törlésére irányuló hívás.
      * Egy végső hívás, `GraphTopologyList` amely azt jelzi, hogy a lista üres.

## <a name="interpret-results"></a>Eredmények értelmezése

A Media Graph futtatásakor a HTTP-bővítmény processzor-csomópontjának eredményei áthaladnak a IoT Hub fogadó csomóponton az IoT hubhoz. A **kimeneti** ablakban látható üzenetek egy `body` szakaszt és egy `applicationProperties` szakaszt tartalmaznak. További információ: [IoT hub üzenetek létrehozása és olvasása](../../iot-hub/iot-hub-devguide-messages-construct.md).

A következő üzenetekben az élő videó elemzési modulja az alkalmazás tulajdonságait és a törzs tartalmát határozza meg. 

### <a name="mediasessionestablished-event"></a>MediaSessionEstablished esemény

Az adathordozó-diagramok példányainak létrehozásakor az RTSP-forrás csomópontja megpróbál csatlakozni az rtspsim-LIVE555 tárolón futó RTSP-kiszolgálóhoz. Ha a kapcsolat sikeres, a következő esemény lesz kinyomtatva. Az esemény típusa **Microsoft. Media. MediaGraph. Diagnostics. MediaSessionEstablished**.

```
[IoTHubMonitor] [9:42:18 AM] Message received from [lvaedgesample/lvaEdge]:
{
  "body": {
    "sdp": "SDP:\nv=0\r\no=- 1586450538111534 1 IN IP4 nnn.nn.0.6\r\ns=Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\ni=media/lots_015.mkv\r\nt=0 0\r\na=tool:LIVE555 Streaming Media v2020.03.06\r\na=type:broadcast\r\na=control:*\r\na=range:npt=0-300.000\r\na=x-qt-text-nam:Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\na=x-qt-text-inf:media/lots_015.mkv\r\nm=video 0 RTP/AVP 96\r\nc=IN IP4 0.0.0.0\r\nb=AS:500\r\na=rtpmap:96 H264/90000\r\na=fmtp:96 packetization-mode=1;profile-level-id=4D0029;sprop-parameter-sets=Z00AKeKQCgC3YC3AQEBpB4kRUA==,aO48gA==\r\na=control:track1\r\n"
  },
  "applicationProperties": {
    "dataVersion": "1.0",
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{name}/providers/microsoft.media/mediaservices/hubname",
    "subject": "/graphInstances/GRAPHINSTANCENAMEHERE/sources/rtspSource",
    "eventType": "Microsoft.Media.MediaGraph.Diagnostics.MediaSessionEstablished",
    "eventTime": "2020-07-24T16:42:18.1280000Z"
  }
}
```

Ebben az üzenetben a következő információkat láthatja:

* Az üzenet egy diagnosztikai esemény. `MediaSessionEstablished` azt jelzi, hogy az RTSP-szimulátorhoz csatlakoztatott RTSP-forrás csomópont (a tárgy) megkezdte a (szimulált) élő adatcsatorna fogadását.
* A `applicationProperties` ben `subject` azt jelzi, hogy az üzenet a Media Graph RTSP-forrás csomópontjában lett létrehozva.
* A `applicationProperties` ben `eventType` azt jelzi, hogy ez az esemény egy diagnosztikai esemény.
* A `eventTime` jelzi az esemény időpontját.
* A `body` diagnosztikai eseménnyel kapcsolatos információkat tartalmaz. Ebben az esetben az adatok a [Session Description Protocol (SDP)](https://en.wikipedia.org/wiki/Session_Description_Protocol) adatait tartalmazzák.

### <a name="inference-event"></a>Következtetési esemény

A HTTP-bővítmény processzor-csomópontja a OpenVINO™ Model Server – AI bővítmény-modulból származó következtetéseket fogad. Ezután az eredményeket a IoT Hub fogadó csomóponton keresztül származtatott eseményként bocsátja ki. 

Ezekben az eseményekben a típus úgy van beállítva, `entity` hogy jelezze, hogy egy entitás, például egy autó vagy egy teherautó. Az `eventTime` érték az a UTC-idő, amikor az objektum észlelve lett. 

A következő példában két járművet észleltek, a 0,9 feletti megbízhatósági értékekkel.

```
[IoTHubMonitor] [9:43:18 AM] Message received from [lva-sample-device/lvaEdge]:
{
  "body": {
    "inferences": [
      {
        "type": "entity",
        "subtype": "vehicleDetection",
        "entity": {
          "tag": {
            "value": "vehicle",
            "confidence": 0.9951713681221008
          },
          "box": {
            "l": 0.042635321617126465,
            "t": 0.4004564881324768,
            "w": 0.10961548984050751,
            "h": 0.07942074537277222
          }
        }
      },
      {
        "type": "entity",
        "subtype": "vehicleDetection",
        "entity": {
          "tag": {
            "value": "vehicle",
            "confidence": 0.928486168384552
          },
          "box": {
            "l": 0.2506900727748871,
            "t": 0.07512682676315308,
            "w": 0.05470699071884155,
            "h": 0.07408371567726135
          }
        }
      }
    ]
  },
  "applicationProperties": {
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{name}/providers/microsoft.media/mediaservices/hubname",
    "subject": "/graphInstances/GRAPHINSTANCENAMEHERE/processors/inferenceClient",
    "eventType": "Microsoft.Media.Graph.Analytics.Inference",
    "eventTime": "2020-07-24T16:43:18.1280000Z"
  }
}
```

Az üzenetekben figyelje meg a következő adatokat:

* A alkalmazásban a `applicationProperties` `subject` diagram topológiájában lévő csomópontra hivatkozik, amelyről az üzenet létrejött. 
* A `applicationProperties` ben `eventType` azt jelzi, hogy ez az esemény egy elemzési esemény.
* Az `eventTime` érték az az idő, amikor az esemény bekövetkezett.
* A `body` szakasz az elemzési eseményre vonatkozó információkat tartalmaz. Ebben az esetben az esemény egy következtetési esemény, így a törzs tartalmaz egy `inferences` adathalmazt.
* A `inferences` szakasz azt jelzi, hogy az a `type` `entity` . Ez a szakasz az entitással kapcsolatos további információkat tartalmaz.

## <a name="run-the-sample-program-to-detect-persons-or-vehicles-or-bikes"></a>A minta program futtatása a személyek, a járművek vagy a kerékpárok észleléséhez
Ha más modellt szeretne használni, módosítania kell a Graph-topológiát, valamint a `operations.json` fájlt is.

Másolja a [Graph-topológiát](https://raw.githubusercontent.com/Azure/live-video-analytics/master/MediaGraph/topologies/httpExtensionOpenVINO/topology.json) egy helyi fájlba, azaz a következőt: `C:\TEMP\topology.json` . Nyissa meg a másolatot, és szerkessze a értékét a következőre: `inferencingUrl` `http://openvino:4000/personVehicleBikeDetection` .

Ezután a Visual Studio Code-ban keresse meg a *src/Cloud-to-Device-Console-app* mappát, és nyissa meg a `operations.json` fájlt. A vonal szerkesztése a következővel `topologyUrl` :

```
      "topologyFile" : "C:\\TEMP\\topology.json" 
```
Most megismételheti a fenti lépéseket a minta program újbóli futtatásához az új topológiával. A következtetések eredményei a jármű észlelési modelljéhez hasonlóak lesznek (sémában), és csak a `subtype` értékre kell beállítani `personVehicleBikeDetection` .

## <a name="run-the-sample-program-to-classify-vehicles"></a>A minta program futtatása a járművek besorolásához
A Visual Studio Code-ban nyissa meg az `topology.json` előző lépésből származó helyi példányt, és szerkessze a értékét a következőre: `inferencingUrl` `http://openvino:4000/vehicleClassification` . Ha az előző példát futtatva azonosíthatja a személyeket vagy a gépjárműveket vagy a kerékpárokat, nem kell újra módosítania a `operations.json` fájlt.

Most megismételheti a fenti lépéseket a minta program újbóli futtatásához az új topológiával. A minta besorolási eredmény a következő.

```
[IoTHubMonitor] [9:44:18 AM] Message received from [lva-sample-device/lvaEdge]:
{
  "body": {
    "inferences": [
      {
        "type": "classification",
        "subtype": "color",
        "classification": {
          "tag": {
            "value": "black",
            "confidence": 0.9179772138595581
          }
        }
      },
      {
        "type": "classification",
        "subtype": "type",
        "classification": {
          "tag": {
            "value": "truck",
            "confidence": 1
          }
        }
      }
    ]
  },
  "applicationProperties": {
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{name}/providers/microsoft.media/mediaservices/hubname",
    "subject": "/graphInstances/GRAPHINSTANCENAMEHERE/processors/inferenceClient",
    "eventType": "Microsoft.Media.Graph.Analytics.Inference",
    "eventTime": "2020-07-24T16:44:18.1280000Z"
  }
}
```

## <a name="run-the-sample-program-to-detect-faces"></a>A minta program futtatása az arcok észleléséhez
A Visual Studio Code-ban nyissa meg az `topology.json` előző lépésből származó helyi példányt, és szerkessze a értékét a következőre: `inferencingUrl` `http://openvino:4000/faceDetection` . Ha az előző példát futtatva azonosíthatja a személyeket vagy a gépjárműveket vagy a kerékpárokat, nem kell újra módosítania a `operations.json` fájlt.

Most megismételheti a fenti lépéseket a minta program újbóli futtatásához az új topológiával. A minta észlelési eredmény a következő (Megjegyzés: a fentiekben használt parkoló nem tartalmaz észlelhető arcokat – a modell kipróbálásához még egy videót kell megadnia).

```
[IoTHubMonitor] [9:54:18 AM] Message received from [lva-sample-device/lvaEdge]:
{
  "body": {
    "inferences": [
      {
        "type": "entity",
        "subtype": "faceDetection",
        "entity": {
          "tag": {
            "value": "face",
            "confidence": 0.9997053742408752
          },
          "box": {
            "l": 0.2559490501880646,
            "t": 0.03403960168361664,
            "w": 0.17685115337371826,
            "h": 0.45835764706134796
          }
        }
      }
    ]
  },
  "applicationProperties": {
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{name}/providers/microsoft.media/mediaservices/hubname",
    "subject": "/graphInstances/GRAPHINSTANCENAMEHERE/processors/inferenceClient",
    "eventType": "Microsoft.Media.Graph.Analytics.Inference",
    "eventTime": "2020-07-24T16:54:18.1280000Z"
  }
}
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha más rövid útmutatókat vagy oktatóanyagokat szeretne kipróbálni, tartsa meg a létrehozott erőforrásokat. Ellenkező esetben lépjen a Azure Portalra, nyissa meg az erőforráscsoportot, válassza ki azt az erőforráscsoportot, amelyben az oktatóanyagot futtatta, és törölje az összes erőforrást.

## <a name="next-steps"></a>Következő lépések

Tekintse át a speciális felhasználókra vonatkozó további kihívásokat:

* Használjon olyan [IP-kamerát](https://en.wikipedia.org/wiki/IP_camera) , amely támogatja az RTSP-t az RTSP-szimulátor használata helyett. Megkeresheti az RTSP-t támogató IP-kamerákat a [ONVIF-megfelelőséggel](https://www.onvif.org/conformant-products/) rendelkező termékek oldalon. Keresse meg azokat az eszközöket, amelyek megfelelnek a G, S vagy T profiloknak.
* Az Azure Linux rendszerű virtuális gépek helyett AMD64-vagy x64-es Linux-eszközt használjon. Az eszköznek ugyanabban a hálózaton kell lennie, mint az IP-kamerának. A [Azure IoT Edge futtatókörnyezet telepítése Linux rendszeren](../../iot-edge/how-to-install-iot-edge.md)című témakör útmutatását követheti. Ezután regisztrálja az eszközt az Azure IoT Hub az [első IoT Edge modul üzembe helyezése virtuális Linux-eszközre](../../iot-edge/quickstart-linux.md)című témakör útmutatását követve.