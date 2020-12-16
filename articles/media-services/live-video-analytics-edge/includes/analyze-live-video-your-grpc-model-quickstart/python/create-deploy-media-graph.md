---
ms.openlocfilehash: 8a1baa34565e9939db54b44f3d196c927b35385e
ms.sourcegitcommit: 63d0621404375d4ac64055f1df4177dfad3d6de6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/15/2020
ms.locfileid: "97532161"
---
### <a name="examine-and-edit-the-sample-files"></a>A mintaadatok vizsgálata és szerkesztése

Az előfeltételek részeként letöltötte a mintakód egy mappába. A mintaadatok vizsgálatához és szerkesztéséhez kövesse az alábbi lépéseket.

1. A Visual Studio Code-ban lépjen az *src/Edge* elemre. Ekkor megjelenik a *. env* fájl és néhány központi telepítési sablon fájl.

    A deployment.grpcyolov3icpu.template.jsa peremhálózati eszköz üzembe helyezési jegyzékére hivatkozik. Tartalmaz néhány helyőrző értéket. A. env fájl tartalmazza a változók értékeit.
1. Lépjen a *src/Cloud-to-Device-Console-app* mappába. Itt láthatja a *appsettings.js* fájlt, és néhány további fájlt:
    
    * operations.json – a program futtatásához használni kívánt műveletek listája.
    * main.py – a minta programkódja. Ez a kód:

        * Betölti az alkalmazás beállításait.
        * Közvetlen metódusokat hív meg, amelyeket az élő videó Analytics IoT Edge modulban tesz elérhetővé. A modul segítségével elemezheti az élő videó streameket a közvetlen metódusok meghívásával.
        * Szünetelteti, hogy megvizsgálhatja a program kimenetét a **terminál** ablakban, és megvizsgálhatja a modul által a **kimeneti** ablakban generált eseményeket.
        * Közvetlen metódusokat hív meg az erőforrások törléséhez.
1. Szerkessze a *operations.js* fájlt:
 
    * Módosítsa a Graph-topológiára mutató hivatkozást:
    * `"topologyUrl"` : `"https://raw.githubusercontent.com/Azure/live-video-analytics/master/MediaGraph/topologies/grpcExtension/2.0/topology.json"`
    * A GraphInstanceSet alatt szerkessze a gráf topológiájának nevét, hogy az megfeleljen az előző hivatkozásban szereplő értéknek:
    * `"topologyName"` : `"InferencingWithGrpcExtension"`
    * A GraphTopologyDelete alatt szerkessze a nevet:
    * `"name"` : `"InferencingWithGrpcExtension"`

> [!NOTE]
> <p>
> <details>
> <summary>Bontsa ki ezt, és tekintse meg, hogyan valósítja meg a MediaGraphGrpcExtension csomópontot a topológiában</summary>
> <pre><code>
> {
>   "@type": "#Microsoft.Media.MediaGraphGrpcExtension",
>   "name": "grpcExtension",
>   "endpoint": {
>       "@type": "#Microsoft.Media.MediaGraphUnsecuredEndpoint",
>       "url": "${grpcExtensionAddress}",
>       "credentials": {
>           "@type": "#Microsoft.Media.MediaGraphUsernamePasswordCredentials",
>           "username": "${grpcExtensionUserName}",
>           "password": "${grpcExtensionPassword}"
>       }
>   },
>   "dataTransfer": {
>       "mode": "sharedMemory",
>       "SharedMemorySizeMiB": "5"
>   },
>   "image": {
>       "scale": {
>           "mode": "${imageScaleMode}",
>           "width": "${frameWidth}",
>           "height": "${frameHeight}"
>       },
>       "format": {
>           "@type": "#Microsoft.Media.MediaGraphImageFormatEncoded",
>           "encoding": "${imageEncoding}",
>           "quality": "${imageQuality}"
>       }
>   },
>   "inputs": [
>       {
>           "nodeName": "motionDetection"
>       }
>   ]
> }          
> </code></pre>
> </details>    
> </p>
    
### <a name="generate-and-deploy-the-iot-edge-deployment-manifest"></a>Az IoT Edge telepítési jegyzék előállítása és üzembe helyezése

1. Kattintson a jobb gombbal a fájlon a *src/Edge/* *deployment.grpcyolov3icpu.template.js* elemre, majd válassza a **IoT Edge üzembe helyezési jegyzék előállítása** lehetőséget.

    ![IoT Edge üzembe helyezési jegyzékének előállítása](../../../media/quickstarts/generate-iot-edge-deployment-manifest-grpc.png)

    A manifest-fájl *deployment.grpcyolov3icpu.amd64.js* a *src/Edge/config* mappában jön létre.
1. Ha befejezte a [mozgás észlelése és a kibocsátó események](../../../detect-motion-emit-events-quickstart.md) rövid útmutatóját, ugorja át ezt a lépést.

    Ellenkező esetben a bal alsó sarokban található **Azure IOT hub** panel közelében válassza a **További műveletek** ikont, majd válassza a **IoT hub a kapcsolatok karakterláncának beállítása** lehetőséget. A karakterláncot a *appsettings.js* fájlból másolhatja. Vagy a megfelelő IoT hub a Visual Studio Code-ban való konfigurálásának biztosításához használja az [IoT hub kiválasztása parancsot](https://github.com/Microsoft/vscode-azure-iot-toolkit/wiki/Select-IoT-Hub).

    ![IoT Hub a kapcsolatok karakterlánca](../../../media/quickstarts/iot-hub-connection-string-grpc.png)
1. Kattintson a jobb gombbal az *src/Edge/config/* *deployment.grpcyolov3icpu.amd64.js* elemre, és válassza a **központi telepítés létrehozása egyetlen eszközhöz** lehetőséget.

    ![üzembe helyezési egyetlen eszköz létrehozása](../../../media/quickstarts/create-deployment-single-device-grpc.png)
1. Amikor a rendszer rákérdez a IoT Hub eszköz kiválasztására, válassza a **LVA-Sample-Device** elemet.
1. Körülbelül 30 másodperc elteltével frissítse az Azure IoT Hubt az ablak bal alsó sarkában. A peremhálózati eszköz mostantól a következő központilag telepített modulokat mutatja:

    * A **lvaEdge** nevű Live Video Analytics-modul.
    * A **rtspsim** modul, amely egy RTSP-kiszolgálót szimulál, és egy élő videó-hírcsatorna forrásaként működik.

        > [!NOTE]
        > Ha saját peremhálózati eszközt használ a telepítési parancsfájlból kiépített helyett, nyissa meg a peremhálózati eszközét, és futtassa az alábbi parancsokat **rendszergazdai jogosultságokkal** az ehhez a rövid útmutatóhoz használt minta videofájl lekéréséhez és tárolásához:  

        ```
        mkdir /home/lvaadmin/samples
        mkdir /home/lvaadmin/samples/input    
        curl https://lvamedia.blob.core.windows.net/public/camera-300s.mkv > /home/lvaadmin/samples/input/camera-300s.mkv  
        chown -R lvaadmin /home/lvaadmin/samples/  
        ```
    * A **lvaExtension** modul, amely a YOLOv3 objektum-észlelési modell, amely kommunikációs módszerként használja a gRPC-et, és a számítógépeket a lemezképekre alkalmazza, és az Objektumtípusok több osztályát adja vissza.
    
    ![LVA-bővítmény](../../../media/quickstarts/lvaextension-grpc.png)

### <a name="prepare-to-monitor-events"></a>Felkészülés az események figyelésére

1. A Visual Studio Code-ban nyissa meg a **bővítmények** lapot (vagy nyomja le a CTRL + SHIFT + X billentyűkombinációt), és keressen rá az Azure IoT hubra.
1. Kattintson a jobb gombbal, és válassza a **bővítmény beállításai** lehetőséget.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="../../../media/run-program/extensions-tab.png" alt-text="Bővítmény beállításai":::
1. Keresse meg és engedélyezze a "részletes üzenet megjelenítése" lehetőséget.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="../../../media/run-program/show-verbose-message.png" alt-text="Részletes üzenet megjelenítése":::
1. Kattintson a jobb gombbal a Live Video Analytics-eszközre, és válassza a **figyelés beépített esemény végpontjának elindítása** lehetőséget. Erre a lépésre szüksége lesz a Visual Studio Code **kimeneti** ablakának IoT hub eseményeinek figyeléséhez.

   ![Figyelés indítása](../../../media/quickstarts/start-monitoring-built-event-endpoint-grpc.png)

### <a name="run-the-sample-program"></a>A minta program futtatása

1. A hibakeresési munkamenet indításához válassza az F5 billentyűt. A terminál ablakban láthatók a kinyomtatott üzenetek.
1. A kód *operations.js* a közvetlen metódusok hívásával indul el `GraphTopologyList` `GraphInstanceList` . Ha az előző rövid útmutatók befejezése után törölte az erőforrásokat, akkor ez a folyamat üres listát ad vissza, majd szünetelteti. A folytatáshoz válassza az ENTER billentyűt.
    
    ```
    -------------------------------Executing operation GraphTopologyList-----------------------  
    Request: GraphTopologyList  --------------------------------------------------
    {
    "@apiVersion": "2.0"
    }
    ---------------  
    Response: GraphTopologyList - Status: 200  ---------------
    {
    "value": []
    }
    --------------------------------------------------------------------------
    Executing operation WaitForInput
    Press Enter to continue
    ```
    
    A **terminál** ablak a közvetlen metódusok következő készletét jeleníti meg:
    
    * `GraphTopologySet`Az előző topologyUrl használó hívás
    * A `GraphInstanceSet` következő törzset használó hívás:
    
    ```
    {
      "@apiVersion": "2.0",
      "name": "Sample-Graph-1",
      "properties": {
        "topologyName": "InferencingWithGrpcExtension",
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
    
    * Egy hívás, `GraphInstanceActivate` amely elindítja a Graph-példányt és a videó áramlását.
    * Egy második hívás, `GraphInstanceList` amely azt mutatja, hogy a Graph-példány fut állapotban van.
1. A **terminál** ablakban lévő kimenet szünetelteti az ENTER billentyűt a folytatáshoz. Ne jelölje be az ENTER billentyűt. Görgessen felfelé, és tekintse meg a meghívott közvetlen metódusok JSON-válaszának hasznos adatait.
1. Váltson a **kimeneti** ablakra a Visual Studio Code-ban. Láthatja, hogy az élő videó Analytics IoT Edge-modulban az IoT hub-ra küld üzenetet. A rövid útmutató következő szakasza ezeket az üzeneteket tárgyalja.
1. A Media Graph továbbra is fut, és kinyomtatja az eredményeket. Az RTSP-szimulátor megtartja a forrás videóját. A Media Graph leállításához térjen vissza a **terminál** ablakába, és válassza az ENTER billentyűt.
1. A hívások következő sorozata törli az erőforrásokat:
    
    * A Graph- `GraphInstanceDeactivate` példány inaktiválására irányuló hívás.
    * A `GraphInstanceDelete` példány törlésére irányuló hívás.
    * A `GraphTopologyDelete` topológia törlésére irányuló hívás.
    * Egy végső hívás, `GraphTopologyList` amely azt jelzi, hogy a lista üres.

