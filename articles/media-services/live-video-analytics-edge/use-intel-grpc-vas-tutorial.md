---
title: Élő videó elemzése az Intel OpenVINO™ DL stream – Edge AI bővítménnyel az gRPC-n keresztül
description: Ebből az oktatóanyagból megtudhatja, hogyan használhatja az Intel OpenVINO™ DL stream-Edge AI-bővítményt az Intel-ból, hogy elemezze a (szimulált) IP-kamerából származó élő videó-hírcsatornát.
ms.topic: tutorial
ms.date: 02/04/2021
ms.service: media-services
ms.author: faneerde
author: faneerde
ms.openlocfilehash: 8679e3d2fc2459e5775c525a0d5dadd4ba42ebb0
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2021
ms.locfileid: "101748062"
---
# <a name="tutorial-analyze-live-video-by-using-intel-openvino-dl-streamer--edge-ai-extension"></a>Oktatóanyag: élő videó elemzése Intel OpenVINO™ DL stream – Edge AI bővítmény használatával 

Ebből az oktatóanyagból megtudhatja, hogyan használhatja az Intel OpenVINO™ DL stream-Edge AI-bővítményt az Intel-ból, hogy elemezze a (szimulált) IP-kamerából származó élő videó-hírcsatornát. Láthatja, hogy ez a következtetési kiszolgáló Hogyan férhet hozzá különböző modellekhez az objektumok (egy személy, egy jármű vagy egy kerékpár), az objektum besorolása (a jármű kiosztása) és az objektumok nyomon követésére szolgáló modell (személy, jármű és kerékpár) észleléséhez. A gRPC modullal való integráció lehetővé teszi, hogy képkockákat küldjön a mesterséges intelligenciára szolgáló kiszolgálónak. Ezután a rendszer elküldi az eredményeket az IoT Edge hubhoz. Ha a következtetési szolgáltatást ugyanazon a számítási csomóponton futtatja, mint az élő videó elemzését, kihasználhatja a videós adatok megosztott memórián keresztüli küldésének előnyeit. Ez lehetővé teszi, hogy az élő videó hírcsatornájának (például 30 keret/mp). 

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

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4LUbN]

Ebben a rövid útmutatóban élő videó-elemzéseket fog használni IoT Edge az Intel OpenVINO™ DL streaming – Edge AI bővítmény az Inteltől az olyan objektumok észleléséhez, mint például a járművek, a járművek besorolása, illetve a járművek, személyek vagy kerékpárok nyomon követése. Az eredményül kapott események közzétételét IoT Edge hubhoz teszi közzé.

## <a name="overview"></a>Áttekintés

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/use-intel-openvino-tutorial/grpc-vas-extension-with-vino.svg" alt-text="A LVA-MediaGraph áttekintése":::

Ez az ábra azt mutatja be, hogyan áramlik be a gyors útmutatóban szereplő jelek. Az [Edge-modul](https://github.com/Azure/live-video-analytics/tree/master/utilities/rtspsim-live555) szimulál egy Real-Time Streaming Protocol-(RTSP-) kiszolgálót futtató IP-kamerát. Az [RTSP-forrás](media-graph-concept.md#rtsp-source) csomópontja lekéri a videó csatornáját a kiszolgálóról, és képkockákat küld a [gRPC-bővítmény processzor](media-graph-concept.md#grpc-extension-processor) -csomópontjára. 

A gRPC-bővítmény processzor-csomópontja dekódolású képkockákat használ bemenetként, és az ilyen képkockákat egy gRPC-kiszolgáló által közzétett [gRPC](terminology.md#grpc) -végpontra továbbítja. A csomópont támogatja az adatátvitelt a [megosztott memóriával](https://en.wikipedia.org/wiki/Shared_memory) , vagy közvetlenül ágyazza be a tartalmat a gRPC-üzenetek törzsébe. Emellett a csomópont beépített rendszerképekkel rendelkezik a képkockák méretezéséhez és kódolásához, mielőtt azok továbbítva lesznek a gRPC-végpontnak. A méretezési lehetőségekkel a képméretarány megtartható, párnázott vagy kifeszíthető. A képkódoló támogatja a JPEG, a PNG vagy a BMP formátumot. További információ [a processzorról](media-graph-extension-concept.md#grpc-extension-processor).

Az oktatóanyagban a következőket végezheti el:

1. A Media Graph üzembe helyezése.
1. Az eredmények értelmezése.
1. Az erőforrások eltávolítása.

## <a name="about-intel-openvino-dl-streamer--edge-ai-extension-module"></a>Az Intel OpenVINO™ DL Streamer – Edge AI bővítmény moduljának ismertetése


A OpenVINO™ DL streaming-Edge AI bővítmény modul egy, az Intel videó-elemzési szolgáltatásán alapuló, a OpenVINO™ DL Streamtel létrehozott video Analytics-folyamatokat kiszolgáló (VA-kiszolgáló). A fejlesztők dekódolású képkockákat küldhetnek az AI bővítmény modulba, amely az észlelést, besorolást vagy nyomon követést végzi, és visszaadja az eredményeket. Az AI bővítmény modul olyan gRPC API-kat tesz elérhetővé, amelyek kompatibilisek a video Analytics-platformokkal, például a Microsoft IoT Edge élő video Analytics szolgáltatásával. 

Összetett, nagy teljesítményű élő videós elemzési megoldások létrehozásához a IoT Edge modul élő videó-elemzését egy olyan hatékony következtetési motorral kell párosítani, amely képes kihasználni a skálát a peremen. Ebben az oktatóanyagban a következtetési kérelmeket a rendszer elküldi az [Intel OpenVINO™ DL streaming – Edge AI-bővítménynek](https://aka.ms/lva-intel-openvino-dl-streamer), amely egy Edge-modul, amely a IoT Edge élő video Analytics szolgáltatással való működésre lett tervezve. 

A következtetési kiszolgáló kezdeti kiadásában a következő [modellek](https://github.com/intel/video-analytics-serving/tree/master/samples/lva_ai_extension#edge-ai-extension-module-options)érhetők el:

- object_detection a ![ jármű person_vehicle_bike_detection objektum-észleléséhez](./media/use-intel-openvino-tutorial/object-detection.png)

- object_classification a ![ jármű vehicle_attributes_recognition objektum besorolásához](./media/use-intel-openvino-tutorial/object-classification.png)

- object_tracking a ![ személyi jármű person_vehicle_bike_tracking objektumának nyomon követéséhez](./media/use-intel-openvino-tutorial/object-tracking.png)

A szolgáltatás előre betöltött objektum-észlelési, objektum-besorolási és objektum-követési folyamatokat használ a gyors kezdéshez. Emellett az előre betöltött [személy – jármű kerékpár-észlelés – Crossroad-0078](https://github.com/openvinotoolkit/open_model_zoo/blob/master/models/intel/person-vehicle-bike-detection-crossroad-0078/description/person-vehicle-bike-detection-crossroad-0078.md) és a [Vehicle-attributes-Recognition-Barrier-0039 modellek](https://github.com/openvinotoolkit/open_model_zoo/blob/master/models/intel/vehicle-attributes-recognition-barrier-0039/description/vehicle-attributes-recognition-barrier-0039.md).

> [!NOTE]
> Az Edge-modul letöltésével és használatával: OpenVINO™ DL Streamer – Edge AI-bővítmény az Intelből és a mellékelt szoftverből elfogadja a [licencszerződésben](https://www.intel.com/content/www/us/en/legal/terms-of-use.html)foglalt feltételeket és kikötéseket.
> Az Intel elkötelezte magát az emberi jogok tiszteletben tartásával és az emberi jogok megsértésével kapcsolatos bűnrészesség elkerülésével. Lásd: az [Intel globális emberi jogi alapelvei](https://www.intel.com/content/www/us/en/policy/policy-human-rights.html). Az Intel termékei és szoftverei kizárólag olyan alkalmazásokban használhatók, amelyek nem okozzák vagy nem járulnak hozzá a nemzetközileg elismert emberi jog megsértéséhez.

A különböző folyamatok rugalmasságát az adott használati esethez egyszerűen a telepítési sablonban lévő folyamat környezeti változóinak módosításával használhatja. Ez lehetővé teszi, hogy gyorsan megváltoztassa a folyamat modelljét, és az élő videó-elemzéssel kombinálva másodpercek alatt megváltoztathatja a média folyamatát és következtetéseit.  

## <a name="create-and-deploy-the-media-graph"></a>A Media Graph létrehozása és üzembe helyezése

### <a name="examine-and-edit-the-sample-files"></a>A mintaadatok vizsgálata és szerkesztése

Az előfeltételek részeként letöltötte a mintakód egy mappába. A mintaadatok vizsgálatához és szerkesztéséhez kövesse az alábbi lépéseket.

1. A Visual Studio Code-ban lépjen az *src/Edge* elemre. Ekkor megjelenik a *. env* fájl és néhány központi telepítési sablon fájl.

    A központi telepítési sablon a peremhálózati eszköz üzembe helyezési jegyzékére hivatkozik. Tartalmaz néhány helyőrző értéket. A *. env* fájl tartalmazza a változók értékeit.

1. Lépjen a *src/Cloud-to-Device-Console-app* mappába. Itt láthatja a *appsettings.js* fájlt, és néhány további fájlt:

    * ***C2D-Console-app. csproj*** – a Project fájl a Visual Studio Code-hoz.
    * ***operations.json*** – a program futtatásához használni kívánt műveletek listája.
    * ***Program.cs*** – a minta programkódja. Ez a kód:

        * Betölti az alkalmazás beállításait.
        * Közvetlen metódusokat hív meg, amelyeket az élő videó Analytics IoT Edge modulban tesz elérhetővé. A modul segítségével elemezheti az élő videó streameket a [közvetlen metódusok](direct-methods.md)meghívásával.
        * Szünetelteti, hogy megvizsgálhatja a program kimenetét a **terminál** ablakban, és megvizsgálhatja a modul által a **kimeneti** ablakban generált eseményeket.
        * Közvetlen metódusokat hív meg az erőforrások törléséhez.


1. Szerkessze a *operations.js* fájlt:
    * Módosítsa a Graph-topológiára mutató hivatkozást:

        `"topologyUrl" : "https://raw.githubusercontent.com/Azure/live-video-analytics/master/MediaGraph/topologies/grpcExtensionOpenVINO/2.0/topology.json"`

    * A (z) alatt `GraphInstanceSet` szerkessze a gráf topológiájának nevét, hogy az megfeleljen az előző hivatkozásban szereplő értéknek:

      `"topologyName" : "InferencingWithOpenVINOgRPC"`

    * `GraphTopologyDelete`A alatt szerkessze a nevet:

      `"name": "InferencingWithOpenVINOgRPC"`

### <a name="generate-and-deploy-the-iot-edge-deployment-manifest"></a>Az IoT Edge telepítési jegyzék előállítása és üzembe helyezése

1. Kattintson a jobb gombbal a fájlon a *src/Edge/deployment.openvino.grpc.cpu.template.js* elemre, majd válassza a **IoT Edge üzembe helyezési jegyzék előállítása** lehetőséget.

    ![IoT Edge üzembe helyezési jegyzék előállítása](./media/use-intel-openvino-tutorial/generate-deployment-manifest.png)  

    A manifest-fájl *deployment.openvino.grpc.cpu.amd64.js* a *src/Edge/config* mappában jön létre.

> [!NOTE]
Emellett olyandeployment.openvino.grpc.gpu.template.jsis tartalmazott *a* sablonon, amely lehetővé teszi a GPU támogatását az Intel OpenVINO DL streaming-Edge AI bővítmény modulhoz. Ezek a sablonok az Intel Docker hub-rendszerképére mutatnak.

A fent említett sablonok az Intel Docker hub-rendszerképre mutatnak. Ha inkább egy másolatot szeretne üzemeltetni a saját Azure Container Registry, kövesse az alábbi 1. és 2. lépést:
1. SSH-t egy olyan eszközre, amely Docker CLI-eszközöket telepített (azaz az peremhálózati eszközét), és lekéri/felcímkézi vagy leküldi a tárolót a következő lépésekkel:
    * Az Intel rendszerképének lekérése a Docker hub szolgáltatásból:

        `sudo docker pull intel/video-analytics-serving:0.4.1-dlstreamer-edge-ai-extension`
    
    * Az Intel rendszerképének címkézése saját Azure Container Registry nevével. Cserélje le a {saját ACR NAME} nevet az ACR-nevére, amelyet a. env fájlban talál:

        `sudo docker image tag intel/video-analytics-serving:0.4.1-dlstreamer-edge-ai-extension {YOUR ACR NAME/video-analytics-serving:0.4.1-dlstreamer-edge-ai-extension}`
    
    * A címkézett Rendszerkép leküldése a Azure Container Registryba:

        `sudo docker push {YOUR ACR NAME/video-analytics-serving:0.4.1-dlstreamer-edge-ai-extension}`
    
2. Most szerkesztenie kell a sablonokat, hogy az Azure Container Registry-on tárolt új rendszerképre hivatkozzon.
    * Kattintson a jobb gombbal a *deployment.openvino.grpc.cpu.template.js* elemre, és lépjen a *lavExtension* modul részre, és cserélje le a következőt:

        `intel/video-analytics-serving:0.4.1-dlstreamer-edge-ai-extension`

        a következőre:

        `{YOUR ACR NAME/video-analytics-serving:0.4.1-dlstreamer-edge-ai-extension}`
    * Ismételje meg a 2. lépést a *deployment.openvino.grpc.gpu.template.js*


3. Ha befejezte a [mozgás észlelése és a kibocsátó események](detect-motion-emit-events-quickstart.md) rövid útmutatóját, ugorja át ezt a lépést. 

    Ellenkező esetben a bal alsó sarokban található **Azure IOT hub** panel közelében válassza a **További műveletek** ikont, majd válassza a **IoT hub a kapcsolatok karakterláncának beállítása** lehetőséget. A karakterláncot a *appsettings.js* fájlból másolhatja. Vagy a megfelelő IoT hub a Visual Studio Code-ban való konfigurálásának biztosításához használja az [IoT hub kiválasztása parancsot](https://github.com/Microsoft/vscode-azure-iot-toolkit/wiki/Select-IoT-Hub).
    
    ![IoT Hub-kapcsolatok karakterláncának beállítása](./media/quickstarts/set-iotconnection-string.png)

> [!NOTE]
> Előfordulhat, hogy a rendszer megkéri, hogy adjon meg egy beépített végponti információt a IoT Hub számára. Az információk lekéréséhez Azure Portalban navigáljon a IoT Hub, és keresse meg a **beépített végpontok** lehetőséget a bal oldali navigációs panelen. Kattintson ide, és az Event hub-kompatibilis **végpont** szakaszban keresse meg az **Event hub-kompatibilis végpontot** . Másolja ki és használja a szövegmezőben található szöveget. A végpont így fog kinézni:  
    ```
    Endpoint=sb://iothub-ns-xxx.servicebus.windows.net/;SharedAccessKeyName=iothubowner;SharedAccessKey=XXX;EntityPath=<IoT Hub name>
    ```

1. Kattintson a jobb gombbal *az src/Edge/config/deployment.openvino.grpc.cpu.template.js* elemre, és válassza a **központi telepítés létrehozása egyetlen eszközhöz** lehetőséget. 

    ![Központi telepítés létrehozása egyetlen eszközhöz](./media/use-intel-openvino-tutorial/deploy-manifest.png)

1. Amikor a rendszer rákérdez a IoT Hub eszköz kiválasztására, válassza a **LVA-Sample-Device** elemet.
1. Körülbelül 30 másodperc elteltével frissítse az Azure IoT Hubt az ablak bal alsó sarkában. A peremhálózati eszköz mostantól a következő központilag telepített modulokat mutatja:

    * A **lvaEdge** nevű Live Video Analytics-modul
    * Az **rtspsim** modul, amely egy RTSP-kiszolgálót szimulál, és élő videó-hírcsatorna forrásaként működik
    * A **lvaExtension** modul, amely az Intel OPENVINO™ DL Streamer – Edge AI bővítmény 

### <a name="prepare-to-monitor-events"></a>Felkészülés az események figyelésére

Kattintson a jobb gombbal a Live Video Analytics-eszközre, és válassza a **figyelés beépített esemény végpontjának elindítása** lehetőséget. Erre a lépésre szüksége lesz a Visual Studio Code **kimeneti** ablakának IoT hub eseményeinek figyeléséhez. 

![Figyelés indítása](./media/quickstarts/start-monitoring-iothub-events.png) 

### <a name="run-the-sample-program-to-detect-vehicles-persons-or-bike"></a>A minta program futtatása a járművek, személyek vagy kerékpárok észleléséhez
Ha megnyitja az oktatóanyaghoz tartozó [Graph-topológiát](https://raw.githubusercontent.com/Azure/live-video-analytics/master/MediaGraph/topologies/grpcExtensionOpenVINO/2.0/topology.json) egy böngészőben, látni fogja, hogy a értéke a következőre van beállítva: a `grpcExtensionAddress` `tcp://lvaExtension:5001` *httpExtensionOpenVINO* mintához képest nem kell módosítania az URL-címet a gRPC-kiszolgálóra. Ehelyett arra utasítja a modult, hogy a korábban említett környezeti változók alapján futtasson egy adott folyamatot. Az alapértelmezett sablonban a következőre lett beállítva: "object_detection" a "person_vehicle_bike_detection". Kísérletezhet más támogatott folyamatokkal is. 

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
             "topologyName": "InferencingWithOpenVINOgRPC",
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
  "sdp": "SDP:\nv=0\r\no=- 1612432131600584 1 IN IP4 172.18.0.6\r\ns=Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\ni=media/homes_00425.mkv\r\nt=0 0\r\na=tool:LIVE555 Streaming Media v2020.08.19\r\na=type:broadcast\r\na=control:*\r\na=range:npt=0-214.166\r\na=x-qt-text-nam:Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\na=x-qt-text-inf:media/homes_00425.mkv\r\nm=video 0 RTP/AVP 96\r\nc=IN IP4 0.0.0.0\r\nb=AS:500\r\na=rtpmap:96 H264/90000\r\na=fmtp:96 packetization-mode=1;profile-level-id=64001F;sprop-parameter-sets=Z2QAH6zZQFAFuwFsgAAAAwCAAAAeB4wYyw==,aOvhEsiw\r\na=control:track1\r\n"
}
```

Ebben az üzenetben a következő információkat láthatja:

* Az üzenet egy diagnosztikai esemény. `MediaSessionEstablished` azt jelzi, hogy az RTSP-szimulátorhoz csatlakoztatott RTSP-forrás csomópont (a tárgy) megkezdte a (szimulált) élő adatcsatorna fogadását.
* A `applicationProperties` ben `subject` azt jelzi, hogy az üzenet a Media Graph RTSP-forrás csomópontjában lett létrehozva.
* A `applicationProperties` ben `eventType` azt jelzi, hogy ez az esemény egy diagnosztikai esemény.
* A `eventTime` jelzi az esemény időpontját.
* A `body` diagnosztikai eseménnyel kapcsolatos információkat tartalmaz. Ebben az esetben az adatok a [Session Description Protocol (SDP)](https://en.wikipedia.org/wiki/Session_Description_Protocol) adatait tartalmazzák.

### <a name="inference-event"></a>Következtetési esemény

A gRPC-bővítmény processzor-csomópontja az Intel OpenVINO™ DL Streamer – Edge AI bővítményből származó eredményeket fogad. Ezután az eredményeket a IoT Hub fogadó csomóponton keresztül származtatott eseményként bocsátja ki. 

Ezekben az eseményekben a típus úgy van beállítva, `entity` hogy jelezze, hogy egy entitás, például egy autó vagy egy teherautó. Az `eventTime` érték az a UTC-idő, amikor az objektum észlelve lett. 

Az alábbi példában látható, hogy azonosított egy járművet, a jármű típusát és színét (fehér), mindezt a 0,9-nál nagyobb megbízhatósági szinttel, és az objektum-követési modell használatakor azonosítót is hozzárendelt az entitáshoz.

```
[IoTHubMonitor] [9:43:18 AM] Message received from [lva-sample-device/lvaEdge]:
{
  "timestamp": 145118912223221,
  "inferences": [
    {
      "type": "entity",
      "entity": {
        "tag": {
          "value": "vehicle",
          "confidence": 0.9605301
        },
        "attributes": [
          {
            "name": "color",
            "value": "white",
            "confidence": 0.9605301
          },
          {
            "name": "type",
            "value": "car",
            "confidence": 0.9605301
          }
        ],
        "box": {
          "l": 0.3958135,
          "t": 0.078730375,
          "w": 0.48403296,
          "h": 0.94352424
        },
        "id": "1"
      }
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
Ha más modellt szeretne használni, módosítania kell a telepítési sablont. A támogatott modellek közötti váltáshoz módosíthatja a lvaExtenstion modulban található környezeti változókat. Tekintse [meg ezt a dokumentumot a githubon](https://github.com/intel/video-analytics-serving/tree/master/samples/lva_ai_extension#edge-ai-extension-module-options) a modellek támogatott értékeinek és kombinációinak megtekintéséhez.

```
"Env":[
"PIPELINE_NAME=object_classification",
"PIPELINE_VERSION=vehicle_attributes_recognition"
],
```
> [!TIP] Másolja a sablont, és tárolja az összes lehetséges folyamat új neve alatt. Így válthat a modellek között úgy, hogy létrehoz egy új központi telepítést egy adott sablon alapján.

A változók módosítása után újra telepítheti a sablont az eszközre. Most megismételheti a fenti lépéseket a minta program újbóli futtatásához az új folyamattal. A következtetések eredményei hasonlóak lesznek (a sémában), de a választott feldolgozási modelltől függően több vagy kevesebb információ jelenik meg.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha más rövid útmutatókat vagy oktatóanyagokat szeretne kipróbálni, tartsa meg a létrehozott erőforrásokat. Ellenkező esetben lépjen a Azure Portalra, nyissa meg az erőforráscsoportot, válassza ki azt az erőforráscsoportot, amelyben az oktatóanyagot futtatta, és törölje az összes erőforrást.

## <a name="next-steps"></a>Következő lépések

Tekintse át a speciális felhasználókra vonatkozó további kihívásokat:

* Használjon olyan [IP-kamerát](https://en.wikipedia.org/wiki/IP_camera) , amely támogatja az RTSP-t az RTSP-szimulátor használata helyett. Megkeresheti az RTSP-t támogató IP-kamerákat a [ONVIF-megfelelőséggel](https://www.onvif.org/conformant-products/) rendelkező termékek oldalon. Keresse meg azokat az eszközöket, amelyek megfelelnek a G, S vagy T profiloknak.
* Azure Linux rendszerű virtuális gép helyett Intel x64 Linux-eszközt használjon. Az eszköznek ugyanabban a hálózaton kell lennie, mint az IP-kamerának. A [Azure IoT Edge futtatókörnyezet telepítése Linux rendszeren](../../iot-edge/how-to-install-iot-edge.md)című témakör útmutatását követheti. Ezután regisztrálja az eszközt az Azure IoT Hub az [első IoT Edge modul üzembe helyezése virtuális Linux-eszközre](../../iot-edge/quickstart-linux.md)című témakör útmutatását követve.
