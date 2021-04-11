---
title: Élő videó elemzése a IoT Edge és az Azure élő videó-elemzésével Custom Vision
description: Ismerje meg, hogyan használható az Azure Custom Vision egy olyan tárolós modell kiépítéséhez, amely képes észlelni egy játék-teherautót, és hogyan használhatja az Azure Live Video Analytics AI-bővíthetőségi funkcióját Azure IoT Edge a modellnek az élő videó streamből való észlelésére.
ms.topic: tutorial
ms.date: 09/08/2020
zone_pivot_groups: ams-lva-edge-programming-languages
ms.openlocfilehash: 1abf123883a89bb41909e8aa67aedfadffc3d37e
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105561202"
---
# <a name="tutorial-analyze-live-video-with-live-video-analytics-on-iot-edge-and-azure-custom-vision"></a>Oktatóanyag: élő videó elemzése élő videó-elemzéssel IoT Edge és az Azure-on Custom Vision

Ebből az oktatóanyagból megtudhatja, hogyan használható az Azure [Custom Vision](https://azure.microsoft.com/services/cognitive-services/custom-vision-service/) egy olyan tárolós modell kiépítéséhez, amely képes észlelni egy játék-teherautót, és az Azure Live Video Analytics [AI-bővíthetőségi funkcióját](analyze-live-video-concept.md#analyzing-video-using-a-custom-vision-model) használja Azure IoT Edge a modellnek az élő videó streamből való észlelésére.

Bemutatjuk, hogyan lehet egyesíteni az Custom Vision hatékonyságát, hogy egy számítógépes jövőkép-modellt építsen és tanítson egy pár kép feltöltésével és címkézésével. Nincs szüksége az adatelemzés, a gépi tanulás vagy a mesterséges intelligencia ismeretére. Emellett megismerheti az élő videók elemzésének képességeit is, így egyszerűen üzembe helyezhet egy egyéni modellt tárolóként a peremhálózat szélén, és elemezheti a szimulált élő videó-hírcsatornát.

::: zone pivot="programming-language-csharp"
[!INCLUDE [header](includes/custom-vision-tutorial/csharp/header.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [header](includes/custom-vision-tutorial/python/header.md)]
::: zone-end

Ez az oktatóanyag a következőket mutatja be:

> [!div class="checklist"]
> * Állítsa be a megfelelő erőforrásokat.
> * Hozzon létre egy Custom Vision modellt a felhőben a játékszer-teherautók észleléséhez és a peremen való üzembe helyezéséhez.
> * Hozzon létre és helyezzen üzembe egy olyan adathordozó-diagramot, amely egy Custom Vision modellhez HTTP-bővítménnyel rendelkezik.
> * Futtassa a kódot.
> * Vizsgálja meg és értelmezze az eredményeket.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="suggested-pre-reading"></a>Javasolt előzetes olvasás  

A Kezdés előtt olvassa el a következő cikkeket:

* [Élő videó-elemzések IoT Edge áttekintése](overview.md)
* [Az Azure Custom Vision áttekintése](../../cognitive-services/custom-vision-service/overview.md)
* [Élő videó-elemzések IoT Edge terminológiában](terminology.md)
* [A Media Graph alapfogalmai](media-graph-concept.md)
* [Live Video Analytics videófelvétel nélkül](analyze-live-video-concept.md)
* [Élő videó-elemzés futtatása saját modellel](use-your-model-quickstart.md)
* [Oktatóanyag: IoT Edge modul fejlesztése](../../iot-edge/tutorial-develop-for-linux.md)
* [Az üzembe helyezés szerkesztése. * .template.jsbekapcsolva](https://github.com/microsoft/vscode-azure-iot-edge/wiki/How-to-edit-deployment.*.template.json)

## <a name="prerequisites"></a>Előfeltételek

::: zone pivot="programming-language-csharp"
[!INCLUDE [prerequisites](includes/custom-vision-tutorial/csharp/prerequisites.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [prerequisites](includes/custom-vision-tutorial/python/prerequisites.md)]
::: zone-end

> [!IMPORTANT]
> Ez a Custom Vision modul csak **Intel x86 és amd64** architektúrákat támogat. A folytatás előtt ellenőrizze a peremhálózati eszköz architektúráját.

## <a name="review-the-sample-video"></a>A minta videó áttekintése

Ez az oktatóanyag egy, az élő stream szimulálása érdekében egy [Toy Car-következtetést tartalmazó videót](https://lvamedia.blob.core.windows.net/public/t2.mkv) használ. Megvizsgálhatja a videót egy alkalmazással, például a [VLC Media Player](https://www.videolan.org/vlc/)használatával. Válassza a **CTRL + N billentyűkombinációt**, majd illesszen be egy hivatkozást a [Toy Car következtetési videóra](https://lvamedia.blob.core.windows.net/public/t2.mkv) a lejátszás megkezdéséhez. A videó megtekintésekor vegye figyelembe, hogy a videóban megjelenik a 36-Second marker an Toy Truck. Az egyéni modell ki lett tanítva, hogy észlelje az adott Toy Truck-t. 

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4LPwK]

Ebben az oktatóanyagban élő videó-elemzéseket fog használni a IoT Edgeon az ilyen játékszer teherautók észleléséhez és a társított következtetési események közzétételéhez a IoT Edge hubhoz.

## <a name="overview"></a>Áttekintés

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/custom-vision-tutorial/topology-custom-vision.svg" alt-text="A Custom Vision áttekintését bemutató diagram.":::

Ez az ábra az oktatóanyagban szereplő jelek folyamatát mutatja be. Az [Edge-modul](https://github.com/Azure/live-video-analytics/tree/master/utilities/rtspsim-live555) szimulál egy Real-Time Streaming Protocol-(RTSP-) kiszolgálót futtató IP-kamerát. Az [RTSP-forrás](media-graph-concept.md#rtsp-source) csomópontja lekéri a videó csatornáját a kiszolgálóról, és a [http-bővítmény processzor](media-graph-concept.md#http-extension-processor) -csomópontjára küldi a videó kereteket.

A HTTP-bővítmény csomópont egy proxy szerepét játssza le.  Ez a mező alapján a beérkező képkockákat is megtekinti, `samplingOptions` és a képkockákat a megadott képtípusra alakítja. Ezt követően továbbítja a képet a REST-ben egy másik Edge-modulba, amely egy HTTP-végpont mögötti AI-modellt futtat. Ebben a példában az Edge-modul a Custom Vision használatával létrehozott Toy Truck detektor modell. A HTTP-bővítmény processzor-csomópontja összegyűjti az észlelés eredményeit, és közzéteszi az eseményeket az [Azure IoT hub](media-graph-concept.md#iot-hub-message-sink) fogadó csomópontban. A csomópont ezután elküldi ezeket az eseményeket az [IoT Edge hubhoz](../../iot-fundamentals/iot-glossary.md#iot-edge-hub).

## <a name="build-and-deploy-a-custom-vision-toy-detection-model"></a>Custom Vision Toy Detection-modell létrehozása és üzembe helyezése 

Ahogy a neve Custom Vision sugallja, használhatja a saját egyéni objektum-Kiderítő vagy a Felhőbeli besorolás létrehozásához. Egyszerű, könnyen használható és intuitív kezelőfelületet biztosít a felhőben vagy az Edge-n keresztül üzembe helyezhető Custom Vision modellek kiépítéséhez.

A Toy Truck-detektor létrehozásához kövesse a rövid útmutató [: Object detektor létrehozása a Custom Vision webhelyről](../../cognitive-services/custom-vision-service/get-started-build-detector.md)című témakör lépéseit.

> [!IMPORTANT]
> Ez a Custom Vision modul csak az **Intel x86 és az AMD64** architektúrát támogatja. A folytatás előtt ellenőrizze a peremhálózati eszköz architektúráját.

További megjegyzések:
 
* Ebben az oktatóanyagban ne használja a rövid útmutató cikk [előfeltételeinek részeként](../../cognitive-services/custom-vision-service/get-started-build-detector.md#prerequisites)megadott mintaképeket. Ehelyett egy olyan rendszerkép-készletet használtunk, amely egy Toy detektor Custom Vision modellt hoz létre. [Ezeket a lemezképeket](https://lvamedia.blob.core.windows.net/public/ToyCarTrainingImages.zip) akkor használja, ha a rövid útmutatóban a [betanítási lemezképek kiválasztására](../../cognitive-services/custom-vision-service/get-started-build-detector.md#choose-training-images) kéri.
* A gyors üzembe helyezési kép szakaszban győződjön meg arról, hogy a képen látható Truck a "Delivery Truck" címkével van megjelölve.

A művelet befejezése után exportálhatja a modellt egy Docker-tárolóba a **teljesítmény** lap **Exportálás** gombjával. Győződjön meg arról, hogy a Linux lehetőséget választja a tároló platform típusaként. Ez az a platform, amelyen a tároló futni fog. A tárolót letöltő gép lehet Windows vagy Linux. Az alábbi utasítások a Windows rendszerű gépre letöltött tároló fájlon alapulnak.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/custom-vision-tutorial/docker-file.png" alt-text="Képernyő, amely a kijelölt Docker jeleníti meg.":::
 
1. Egy zip-fájllal kell letöltenie a nevű helyi gépre `<projectname>.DockerFile.Linux.zip` . 
1. Ellenőrizze, hogy telepítve van-e a Docker. Ha nem, telepítse a [Docker](https://docs.docker.com/get-docker/) -t a Windows asztalára.
1. Csomagolja ki a letöltött fájlt egy tetszőleges helyre. A parancssor használatával lépjen a kibontott mappa könyvtárába.
    
    Futtassa az alábbi parancsot:
    
    1. `docker build -t cvtruck` 
    
        Ez a parancs sok csomagot tölt le, létrehozza a Docker-rendszerképet, és címkézi azt `cvtruck:latest` .
    
        > [!NOTE]
        > Ha a művelet sikeres, a következő üzeneteknek kell megjelennie: `Successfully built <docker image id>` és `Successfully tagged cvtruck:latest` . Ha a Build parancs sikertelen, próbálkozzon újra. Néha a függőségi csomagok nem töltik le az első körül.
    1. `docker  image ls`

        Ez a parancs ellenőrzi, hogy az új rendszerkép a helyi beállításjegyzékben található-e.
    1. `docker run -p 127.0.0.1:80:80 -d cvtruck`
    
        A parancsnak közzé kell tennie a Docker számára elérhető portot (80) a helyi gép portjára (80).
    1. `docker container ls`
    
        Ez a parancs ellenőrzi a port-hozzárendeléseket, és ha a Docker-tároló sikeresen fut a gépen. A kimenetnek a következőhöz hasonlónak kell lennie:

        ```
        CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                      NAMES
        8b7505398367        cvtruck             "/bin/sh -c 'python …"   13 hours ago        Up 25 seconds       127.0.0.1:80->80/tcp   practical_cohen
        ```
      1. `curl -X POST http://127.0.0.1:80/image -F imageData=@<path to any image file that has the toy delivery truck in it>`
            
            Ez a parancs teszteli a tárolót a helyi gépen. Ha a rendszerkép ugyanazzal a szállítóval rendelkezik, mint a modell betanítása, a kimenetnek az alábbi példához hasonlónak kell lennie. Azt javasolja, hogy a kézbesítési kamion a 90,12%-os valószínűséggel lett észlelve.
    
            ```
            {"created":"2020-03-20T07:10:47.827673","id":"","iteration":"","predictions":[{"boundingBox":{"height":0.66167289,"left":-0.03923762,"top":0.12781593,"width":0.70003178},"probability":0.90128148,"tagId":0,"tagName":"delivery truck"},{"boundingBox":{"height":0.63733053,"left":0.25220079,"top":0.0876643,"width":0.53331227},"probability":0.59745145,"tagId":0,"tagName":"delivery truck"}],"project":""}
            ```

<!--## Create and deploy a media graph with http extension to custom vision model-->

## <a name="examine-the-sample-files"></a>A mintaadatok vizsgálata

::: zone pivot="programming-language-csharp"
[!INCLUDE [examine-sample-files](includes/custom-vision-tutorial/csharp/examine-sample-files.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [examine-sample-files](includes/custom-vision-tutorial/python/examine-sample-files.md)]
::: zone-end

## <a name="generate-and-deploy-the-deployment-manifest"></a>Az üzembe helyezési jegyzék előállítása és üzembe helyezése

1. A Visual Studio Code-ban lépjen a src/Cloud-to-Device-Console-app/operations.jselemre.

1. `GraphTopologySet`A területen győződjön meg arról, hogy a következők teljesülnek:<br/>`"topologyUrl" : "https://raw.githubusercontent.com/Azure/live-video-analytics/master/MediaGraph/topologies/httpExtension/2.0/topology.json"`
1. A területen `GraphInstanceSet` Győződjön meg arról, hogy:
    1. `"topologyName" : "InferencingWithHttpExtension"`
    1. Adja hozzá a következőt a paraméterek tömb elejéhez: `{"name": "inferencingUrl","value": "http://cv:80/image"},`
    1. Módosítsa a `rtspUrl` paraméter értékét a következőre: `"rtsp://rtspsim:554/media/t2.mkv"` .
1. A területen `GraphTopologyDelete` Győződjön meg róla `"name": "InferencingWithHttpExtension"` .
1. Kattintson a jobb gombbal a fájlhoz tartozó src/Edge/deployment.customvision.template.jselemre, és válassza a **IoT Edge üzembe helyezési jegyzék előállítása** lehetőséget.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/custom-vision-tutorial/deployment-template-json.png" alt-text="A IoT Edge üzembe helyezési jegyzék létrehozását bemutató képernyőkép.":::
  
    Ehhez a művelethez létre kell hoznia egy jegyzékfájlt a deployment.customvision.amd64.jsnevű src/Edge/config mappában.
1. Nyissa meg a src/Edge/deployment.customvision.template.jsfájlt, és keresse meg a `registryCredentials` JSON-blokkot. Ebben a blokkban megtalálja az Azure Container Registry-t, valamint annak felhasználónevét és jelszavát.
1. A parancssorban a következő lépésekkel küldje le a helyi Custom Vision tárolót a Azure Container Registry-példányba:

    1. Jelentkezzen be a beállításjegyzékbe a következő parancs végrehajtásával:
    
        `docker login <address>`
    
        Adja meg a felhasználónevet és a jelszót, amikor a rendszer a hitelesítést kéri.
        
        > [!NOTE]
        > A jelszó nem látható a parancssorban.
    1. A rendszerkép címkézése a következő paranccsal: <br/>`docker tag cvtruck   <address>/cvtruck`.
    1. A rendszerkép leküldése a következő paranccsal: <br/>`docker push <address>/cvtruck`.

        Ha a művelet sikeres, a `Pushed` parancssorban a rendszerképhez tartozó SHA-vel együtt kell megjelennie.
    1. Azt is megerősítheti, hogy ellenőrzi a Azure Container Registry példányát a Azure Portal. Itt láthatja az adattár nevét a címkével együtt.
1. Állítsa be a IoT Hub a kapcsolódási karakterláncot a bal alsó sarokban található **Azure IOT hub** panel melletti **További műveletek** ikonra kattintva. A karakterláncot a appsettings.jsfájlból másolhatja. (Íme egy másik ajánlott módszer, amellyel biztosíthatja, hogy a megfelelő IoT hub legyen konfigurálva a Visual Studio Code-ban a [Select IoT hub parancs](https://github.com/Microsoft/vscode-azure-iot-toolkit/wiki/Select-IoT-Hub)használatával.)

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/custom-vision-tutorial/connection-string.png" alt-text="Képernyőkép, amely a set IoT Hub a kapcsolatok karakterláncát mutatja.":::
1. Ezután kattintson a jobb gombbal az src/Edge/config/deployment.customvision.amd64.jselemre, majd válassza a **központi telepítés létrehozása egyetlen eszközhöz** lehetőséget.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/custom-vision-tutorial/deployment-amd64-json.png" alt-text="Képernyőkép, amely egy eszköz létrehozási példányának létrehozását mutatja be.":::
1. Ezután meg kell adnia egy IoT Hub eszköz kiválasztását. Válassza ki a **LVA-Sample-Device** elemet a legördülő listából.
1. Körülbelül 30 másodperc alatt frissítse az Azure IoT hubot a bal alsó szakaszban. A peremhálózati eszköznek a következő telepített modulokkal kell rendelkeznie:

    * Az élő videó Analytics IoT Edge nevű moduljában `lvaEdge` .
    * Egy nevű modul `rtspsim` , amely egy élő videó-hírcsatorna forrásaként szolgáló RTSP-kiszolgálót szimulál.
    * Egy nevű modul `cv` , amely a neve is sugallja, a Custom Vision Toy Truck észlelési modell, amely a lemezképekre Custom Vision vonatkozik, és több kódelemet ad vissza. (A modell csak egy címkére, egy szállítási teherautóra lett betanítva.)

## <a name="prepare-for-monitoring-events"></a>Felkészülés a figyelési eseményekre

Kattintson a jobb gombbal az élő videó elemzési eszközre, és válassza a **figyelés beépített esemény végpontjának elindítása** lehetőséget. Erre a lépésre szüksége lesz a Visual Studio Code **kimeneti** ablakának IoT hub eseményeinek figyeléséhez.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/custom-vision-tutorial/start-monitoring.png" alt-text="A beépített esemény végpontját bemutató képernyőkép.":::

## <a name="run-the-sample-program"></a>A minta program futtatása

Ha megnyitja az oktatóanyaghoz tartozó Graph-topológiát egy böngészőben, látni fogja, hogy a értéke a következőre `inferencingUrl` lett beállítva: `http://cv:80/image` . Ez a beállítás azt jelenti, hogy a következtetési kiszolgáló az élő videóban a játék-teherautók észlelése után az eredményeket fogja visszaadni.

1. A Visual Studio Code-ban nyissa meg a **kiterjesztések** lapot (vagy válassza a **CTRL + SHIFT + X** billentyűkombinációt), és keresse meg az Azure IoT hub.
1. Kattintson a jobb gombbal, és válassza a **bővítmény beállításai** lehetőséget.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/run-program/extensions-tab.png" alt-text="A bővítmény beállításait megjelenítő képernyőkép.":::
1. Keresés és engedélyezés a **részletes üzenet megjelenítése**.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/run-program/show-verbose-message.png" alt-text="A részletes üzenetet bemutató képernyőkép.":::
1. A hibakeresési munkamenet indításához válassza az **F5** billentyűt. A **terminál** ablakban láthatók a kinyomtatott üzenetek.
1. A kód operations.jsa közvetlen metódusok hívásával indul el `GraphTopologyList` `GraphInstanceList` . Ha az előző rövid útmutatók befejezése után törölte az erőforrásokat, akkor ez a folyamat üres listát ad vissza, majd szünetelteti. A folytatáshoz válassza az **ENTER** billentyűt.
    
   A **terminál** ablak a közvetlen metódusok következő készletét jeleníti meg:
    
   * `GraphTopologySet`Az előzőt használó hívás `topologyUrl` .
   * A `GraphInstanceSet` következő törzset használó hívás:
        
   ```
        {
          "@apiVersion": "2.0",
          "name": "Sample-Graph-1",
          "properties": {
            "topologyName": "CustomVisionWithHttpExtension",
            "description": "Sample graph description",
            "parameters": [
              { 
                "name": "inferencingUrl",
                "value": "http://cv:80/image"
              },
              {
                "name": "rtspUrl",
                "value": "rtsp://rtspsim:554/media/t2.mkv"
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
    
   * Egy hívás, `GraphInstanceActivate` amely elindítja a Graph-példányt és a videó áramlását.
   * Egy második hívás, `GraphInstanceList` amely azt mutatja, hogy a Graph-példány fut állapotban van.
    
1. A **terminál** ablakban lévő kimenet szünetelteti az ENTER billentyűt a **folytatáshoz** . Ne jelölje be az **ENTER billentyűt** . Görgessen felfelé, és tekintse meg a meghívott közvetlen metódusok JSON-válaszának hasznos adatait.
1. Váltson a **kimeneti** ablakra a Visual Studio Code-ban. Láthatja, hogy az élő videó Analytics IoT Edge-modulban az IoT hub-ra küld üzenetet. Az oktatóanyag következő szakasza ezeket az üzeneteket tárgyalja.
1. A Media Graph továbbra is fut, és kinyomtatja az eredményeket. Az RTSP-szimulátor megtartja a forrás videóját. A Media Graph leállításához térjen vissza a **terminál** ablakába, és válassza az **ENTER billentyűt**.
A hívások következő sorozata törli az erőforrásokat:
    
   * A Graph- `GraphInstanceDeactivate` példány inaktiválására irányuló hívás.
   * A `GraphInstanceDelete` példány törlésére irányuló hívás.
   * A `GraphTopologyDelete` topológia törlésére irányuló hívás.
   * Egy végső hívás, `GraphTopologyList` amely azt jelzi, hogy a lista üres.
    
## <a name="interpret-the-results"></a>Az eredmények értelmezése

A Media Graph futtatásakor a HTTP-bővítmény processzor-csomópontjának eredményei áthaladnak a IoT Hub fogadó csomóponton az IoT hubhoz. A **kimeneti** ablakban látható üzenetek törzs szakaszt és `applicationProperties` szakaszt tartalmaznak. További információ: [IoT hub üzenetek létrehozása és olvasása](../../iot-hub/iot-hub-devguide-messages-construct.md).

A következő üzenetekben az élő videó elemzési modulja az alkalmazás tulajdonságait és a törzs tartalmát határozza meg.

### <a name="mediasessionestablished-event"></a>MediaSessionEstablished esemény

Az adathordozó-diagramok példányainak létrehozásakor az RTSP-forrás csomópontja megpróbál csatlakozni az rtspsim-LIVE555 tárolón futó RTSP-kiszolgálóhoz. Ha a kapcsolat sikeres, a következő esemény lesz kinyomtatva. Az esemény típusa **Microsoft. Media. MediaGraph. Diagnostics. MediaSessionEstablished**.

```
{
  "body": {
    "sdp": "SDP:\nv=0\r\no=- 1599412849822466 1 IN IP4 172.18.0.3\r\ns=Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\ni=media/t2.mkv\r\nt=0 0\r\na=tool:LIVE555 Streaming Media v2020.04.12\r\na=type:broadcast\r\na=control:*\r\na=range:npt=0-78.357\r\na=x-qt-text-nam:Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\na=x-qt-text-inf:media/t2.mkv\r\nm=video 0 RTP/AVP 96\r\nc=IN IP4 0.0.0.0\r\nb=AS:500\r\na=rtpmap:96 H264/90000\r\na=fmtp:96 packetization-mode=1;profile-level-id=42C01F;sprop-parameter-sets=Z0LAH9kAUAW6EAAAAwAQAAADAwDxgySA,aMuBcsg=\r\na=control:track1\r\nm=audio 0 RTP/AVP 97\r\nc=IN IP4 0.0.0.0\r\nb=AS:96\r\na=rtpmap:97 MPEG4-GENERIC/44100/2\r\na=fmtp:97 streamtype=5;profile-level-id=1;mode=AAC-hbr;sizelength=13;indexlength=3;indexdeltalength=3;config=121056E500\r\na=control:track2\r\n"
  },
  "applicationProperties": {
    "topic": "/subscriptions/35c2594a-23da-4fce-b59c-f6fb9513eeeb/resourceGroups/lvaavi_0827/providers/microsoft.media/mediaservices/lvasampleulf2rv2x5msy2",
    "subject": "/graphInstances/ GRAPHINSTANCENAMEHERE /sources/rtspSource",
    "eventType": "Microsoft.Media.Graph.Diagnostics.MediaSessionEstablished",
    "eventTime": "2020-09-06T17:20:49.823Z",
    "dataVersion": "1.0"
  }
```

Ebben az üzenetben a következő információkat láthatja:

* Az üzenet egy diagnosztikai esemény. `MediaSessionEstablished` azt jelzi, hogy az RTSP-szimulátorhoz csatlakoztatott RTSP-forrás csomópont (a tárgy) megkezdte a szimulált élő adatcsatorna fogadását.
* A `applicationProperties` ben `subject` azt jelzi, hogy az üzenet a Media Graph RTSP-forrás csomópontjában lett létrehozva.
* A-ben `applicationProperties` az esemény típusa azt jelzi, hogy ez az esemény egy diagnosztikai esemény.
* Az esemény időpontja az esemény előfordulásának időpontját jelzi.
* A törzs a diagnosztikai eseménnyel kapcsolatos információkat tartalmaz. Ebben az esetben az adatok a [Session Description Protocol (SDP)](https://en.wikipedia.org/wiki/Session_Description_Protocol) adatait tartalmazzák.

### <a name="inference-event"></a>Következtetési esemény

A HTTP-bővítmény processzor-csomópontja a Custom Vision tárolóból származtatott eredményeket fogad, és az eredményeket a IoT Hub fogadó csomóponton keresztül bocsátja ki következtetési eseményként.

```
{
  "body": {
    "created": "2020-05-18T01:08:34.483Z",
    "id": "",
    "iteration": "",
    "predictions": [
      {
        "boundingBox": {
          "height": 0.06219418,
          "left": 0.14977954,
          "top": 0.65847444,
          "width": 0.01879117
        },
        "probability": 0.69806677,
        "tagId": 0,
        "tagName": "delivery truck"
      },
      {
        "boundingBox": {
          "height": 0.1148454,
          "left": 0.77430711,
          "top": 0.54488315,
          "width": 0.11315252
        },
        "probability": 0.29394844,
        "tagId": 0,
        "tagName": "delivery truck"
      },
      {
        "boundingBox": {
          "height": 0.03187029,
          "left": 0.62644471,
          "top": 0.59640052,
          "width": 0.01582896
        },
        "probability": 0.14435098,
        "tagId": 0,
        "tagName": "delivery truck"
      },
      {
        "boundingBox": {
          "height": 0.11421723,
          "left": 0.72737214,
          "top": 0.55907888,
          "width": 0.12627538
        },
        "probability": 0.1141128,
        "tagId": 0,
        "tagName": "delivery truck"
      },
      {
        "boundingBox": {
          "height": 0.04717135,
          "left": 0.66516746,
          "top": 0.34617995,
          "width": 0.03802613
        },
        "probability": 0.10461298,
        "tagId": 0,
        "tagName": "delivery truck"
      },
      {
        "boundingBox": {
          "height": 0.20650843,
          "left": 0.56349564,
          "top": 0.51482571,
          "width": 0.35045707
        },
        "probability": 0.10056595,
        "tagId": 0,
        "tagName": "delivery truck"
      }
    ],
    "project": ""
  },
  "applicationProperties": {
    "topic": "/subscriptions/35c2594a-23da-4fce-b59c-f6fb9513eeeb/resourceGroups/lsravi/providers/microsoft.media/mediaservices/lvasamplerc3he6a7uhire",
    "subject": "/graphInstances/GRAPHINSTANCENAMEHERE/processors/httpExtension",
    "eventType": "Microsoft.Media.Graph.Analytics.Inference",
    "eventTime": "2020-05-18T01:08:34.038Z"
  }
}
```

Jegyezze fel a következő információkat az előző üzenetekben:

* A tulajdonos azon `applicationProperties` a csomóponton hivatkozik arra a MediaGraph, amelyről az üzenetet létrehozták. Ebben az esetben az üzenet a HTTP-bővítmény processzoráról származik.
* Az esemény típusa `applicationProperties` azt jelzi, hogy ez egy elemzési következtetési esemény.
* Az esemény időpontja az esemény előfordulásának időpontját jelzi.
* A törzs az elemzési eseményre vonatkozó információkat tartalmaz. Ebben az esetben az esemény egy következtetési esemény, így a törzs az előrejelzések elnevezésű következtetések tömbjét tartalmazza.
* Az előrejelzések szakasz azon előrejelzések listáját tartalmazza, amelyekben a játék kézbesítési Teherautója (a "szállítási kamion" címke) megtalálható a keretben. A "Delivery Truck" kifejezés azt az egyéni címkét adja vissza, amelyet a Toy Truck számára a saját betanított modelljéhez adott meg. A modell a bemeneti videóban található és a különböző valószínűségi megbízhatósági pontszámokkal azonosított játék-teherautót azonosítja.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha más oktatóanyagokat vagy gyors útmutatókat szeretne kipróbálni, tartsa be a létrehozott erőforrásokat. Ellenkező esetben lépjen a Azure Portalra, keresse meg az erőforráscsoportot, válassza ki azt az erőforráscsoportot, amelyben az oktatóanyagot futtatta, és törölje az összes erőforrást.

## <a name="next-steps"></a>Következő lépések

Tekintse át a speciális felhasználókra vonatkozó további kihívásokat:

* Használjon olyan [IP-kamerát](https://en.wikipedia.org/wiki/IP_camera) , amely támogatja az RTSP-t az RTSP-szimulátor használata helyett. Megkeresheti az RTSP-t támogató IP-kamerákat a [ONVIF-megfelelőséggel](https://www.onvif.org/conformant-products/) rendelkező termékek oldalon. Keresse meg azokat az eszközöket, amelyek megfelelnek a G, S vagy T profiloknak.
* Az Azure Linux rendszerű virtuális gépek helyett AMD64-vagy x64-es Linux-eszközt használjon. Az eszköznek ugyanabban a hálózaton kell lennie, mint az IP-kamerának. A [Azure IoT Edge futtatókörnyezet telepítése Linux rendszeren](../../iot-edge/how-to-install-iot-edge.md)című témakör útmutatását követheti.

Ezután regisztrálja az eszközt az Azure IoT Hub az [első IoT Edge modul üzembe helyezése virtuális Linux-eszközre](../../iot-edge/quickstart-linux.md)című témakör útmutatását követve.