---
ms.openlocfilehash: 1b5dd2fb4ef8cb3f6fd169477d9ee82e912c146e
ms.sourcegitcommit: 4e70fd4028ff44a676f698229cb6a3d555439014
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/28/2021
ms.locfileid: "98956296"
---
### <a name="examine-and-edit-the-sample-files"></a>A mintaadatok vizsgálata és szerkesztése

Az előfeltételek részeként letöltötte a mintakód egy mappába. A mintaadatok vizsgálatához és szerkesztéséhez kövesse az alábbi lépéseket.

1. A Visual Studio Code-ban lépjen az *src/Edge* elemre. Ekkor megjelenik a *. env* fájl és néhány központi telepítési sablon fájl.

    A központi telepítési sablon a peremhálózati eszköz üzembe helyezési jegyzékére hivatkozik. Tartalmaz néhány helyőrző értéket. A *. env* fájl tartalmazza a változók értékeit.

1. Lépjen a *src/Cloud-to-Device-Console-app* mappába. Itt láthatja a *appsettings.js* fájlt, és néhány további fájlt:

    * ***C2D-Console-app. csproj** _ – a Project fájl a Visual Studio Code-hoz.
    _ ***operations.json** _ – azoknak a műveleteknek a listája, amelyeket futtatni kíván a program.
    _ ***Program.cs** _ – a minta programkódja. Ez a kód:

        _ Betölti az alkalmazás beállításait.
        * Közvetlen metódusokat hív meg, amelyeket az élő videó Analytics IoT Edge modulban tesz elérhetővé. A modul segítségével elemezheti az élő videó streameket a [közvetlen metódusok](../../../direct-methods.md)meghívásával.
        * Szünetelteti, hogy megvizsgálhatja a program kimenetét a **terminál** ablakban, és megvizsgálhatja a modul által a **kimeneti** ablakban generált eseményeket.
        * Közvetlen metódusokat hív meg az erőforrások törléséhez.


1. Szerkessze a *operations.js* fájlt:
    * Módosítsa a Graph-topológiára mutató hivatkozást:

        `"topologyUrl" : "https://raw.githubusercontent.com/Azure/live-video-analytics/master/MediaGraph/topologies/httpExtension/2.0/topology.json"`

    * A (z) alatt `GraphInstanceSet` szerkessze a gráf topológiájának nevét, hogy az megfeleljen az előző hivatkozásban szereplő értéknek:

      `"topologyName" : "InferencingWithHttpExtension"`

    * `GraphTopologyDelete`A alatt szerkessze a nevet:

      `"name": "InferencingWithHttpExtension"`

### <a name="generate-and-deploy-the-iot-edge-deployment-manifest"></a>Az IoT Edge telepítési jegyzék előállítása és üzembe helyezése

1. Kattintson a jobb gombbal a fájlon a *src/Edge/deployment.yolov3.template.js* elemre, majd válassza a **IoT Edge üzembe helyezési jegyzék előállítása** lehetőséget.

    ![IoT Edge üzembe helyezési jegyzék előállítása](../../../media/quickstarts/generate-iot-edge-deployment-manifest-yolov3.png)  

    A manifest-fájl *deployment.yolov3.amd64.js* a *src/Edge/config* mappában jön létre.

1. Ha befejezte a [mozgás észlelése és a kibocsátó események](../../../detect-motion-emit-events-quickstart.md) rövid útmutatóját, ugorja át ezt a lépést. 

    Ellenkező esetben a bal alsó sarokban található **Azure IOT hub** panel közelében válassza a **További műveletek** ikont, majd válassza a **IoT hub a kapcsolatok karakterláncának beállítása** lehetőséget. A karakterláncot a *appsettings.js* fájlból másolhatja. Vagy a megfelelő IoT hub a Visual Studio Code-ban való konfigurálásának biztosításához használja az [IoT hub kiválasztása parancsot](https://github.com/Microsoft/vscode-azure-iot-toolkit/wiki/Select-IoT-Hub).
    
    ![IoT Hub-kapcsolatok karakterláncának beállítása](../../../media/quickstarts/set-iotconnection-string.png)

> [!NOTE]
> Előfordulhat, hogy a rendszer megkéri, hogy adjon meg egy beépített végponti információt a IoT Hub számára. Az információk lekéréséhez Azure Portalban navigáljon a IoT Hub, és keresse meg a **beépített végpontok** lehetőséget a bal oldali navigációs panelen. Kattintson ide, és az Event hub-kompatibilis **végpont** szakaszban keresse meg az **Event hub-kompatibilis végpontot** . Másolja ki és használja a szövegmezőben található szöveget. A végpont így fog kinézni:  
    ```
    Endpoint=sb://iothub-ns-xxx.servicebus.windows.net/;SharedAccessKeyName=iothubowner;SharedAccessKey=XXX;EntityPath=<IoT Hub name>
    ```

1. Kattintson a jobb gombbal *az src/Edge/config/deployment.yolov3.amd64.js* elemre, és válassza a **központi telepítés létrehozása egyetlen eszközhöz** lehetőséget. 

    ![Központi telepítés létrehozása egyetlen eszközhöz](../../../media/quickstarts/create-deployment-single-device.png)

1. Amikor a rendszer rákérdez a IoT Hub eszköz kiválasztására, válassza a **LVA-Sample-Device** elemet.
1. Körülbelül 30 másodperc elteltével frissítse az Azure IoT Hubt az ablak bal alsó sarkában. A peremhálózati eszköz mostantól a következő központilag telepített modulokat mutatja:

    * A **lvaEdge** nevű Live Video Analytics-modul
    * Az **rtspsim** modul, amely egy RTSP-kiszolgálót szimulál, és élő videó-hírcsatorna forrásaként működik
        > [!NOTE]
        > A fenti lépések feltételezik, hogy a telepítési parancsfájl által létrehozott virtuális gépet használja. Ha ehelyett saját Edge-eszközt használ, lépjen a peremhálózati eszközre, és futtassa az alábbi parancsokat **rendszergazdai jogosultságokkal** a rövid útmutatóhoz használt minta videofájl lekéréséhez és tárolásához:  
        
        ```
        mkdir /home/lvaadmin/samples
        mkdir /home/lvaadmin/samples/input    
        curl https://lvamedia.blob.core.windows.net/public/camera-300s.mkv > /home/lvaadmin/samples/input/camera-300s.mkv  
        chown -R lvaadmin /home/lvaadmin/samples/  
        ```
    * A **yolov3** modul, amely a yolov3 objektum-észlelési modell, amely a számítógépekre vonatkozó jövőképet alkalmaz a lemezképekre, és több objektumtípust ad vissza.
 
      ![A peremhálózati eszközön üzembe helyezett modulok](../../../media/quickstarts/yolov3.png)

### <a name="prepare-to-monitor-events"></a>Felkészülés az események figyelésére

1. A Visual Studio Code-ban nyissa meg a **bővítmények** lapot (vagy nyomja le a CTRL + SHIFT + X billentyűkombinációt), és keressen rá az Azure IoT hubra.
1. Kattintson a jobb gombbal, és válassza a **bővítmény beállításai** lehetőséget.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="../../../media/run-program/extensions-tab.png" alt-text="Bővítmény beállításai":::
1. Keresse meg és engedélyezze a "részletes üzenet megjelenítése" lehetőséget.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="../../../media/run-program/show-verbose-message.png" alt-text="Részletes üzenet megjelenítése":::
1. Kattintson a jobb gombbal a Live Video Analytics-eszközre, és válassza a **figyelés beépített esemény végpontjának elindítása** lehetőséget. Erre a lépésre szüksége lesz a Visual Studio Code **kimeneti** ablakának IoT hub eseményeinek figyeléséhez. 

   ![Figyelés indítása](../../../media/quickstarts/start-monitoring-iothub-events.png) 

### <a name="run-the-sample-program"></a>A minta program futtatása

1. A hibakeresési munkamenet indításához válassza az F5 billentyűt. A **terminál** ablakban láthatók a kinyomtatott üzenetek.
1. A kód *operations.js* a közvetlen metódusok hívásával indul el `GraphTopologyList` `GraphInstanceList` . Ha az előző rövid útmutatók befejezése után törölte az erőforrásokat, akkor ez a folyamat üres listát ad vissza, majd szünetelteti. A folytatáshoz válassza az ENTER billentyűt.

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

    A **terminál** ablak a közvetlen metódusok következő készletét jeleníti meg:

     * `GraphTopologySet`Az előzőt használó hívás`topologyUrl`
     * A `GraphInstanceSet` következő törzset használó hívás:

         ```
         {
           "@apiVersion": "2.0",
           "name": "Sample-Graph-1",
           "properties": {
             "topologyName": "InferencingWithHttpExtension",
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
