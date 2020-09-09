---
ms.openlocfilehash: 055448eacf1cc12c6d021c6571a0478cb35442ba
ms.sourcegitcommit: d0541eccc35549db6381fa762cd17bc8e72b3423
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/09/2020
ms.locfileid: "89566905"
---
Az üzembe helyezési jegyzék meghatározza, hogy a rendszer mely modulokat telepíti egy peremhálózati eszközre. Emellett meghatározza a modulok konfigurációs beállításait is. 

Kövesse az alábbi lépéseket a jegyzékfájlnak a sablonból való létrehozásához, majd a peremhálózati eszközre történő telepítéséhez.

1. Nyissa meg a Visual Studio Code-ot.
1. Az **Azure IOT hub** panel mellett válassza a **További műveletek** ikont a IoT hub kapcsolódási karakterlánc beállításához. A karakterláncot a *src/Cloud-to-Device-Console-app/appsettings.js* fájlból másolhatja. 

    ![IOT-kapcsolatok karakterláncának beállítása](../../../media/quickstarts/set-iotconnection-string.png)
1. Kattintson a jobb gombbal **az src/Edge/deployment.template.js** elemre, és válassza a **IoT Edge üzembe helyezési jegyzék előállítása**lehetőséget.

    ![A IoT Edge üzembe helyezési jegyzékének előállítása](../../../media/quickstarts/generate-iot-edge-deployment-manifest.png)

    Ehhez a művelethez létre kell hoznia egy *deployment.amd64.js* nevű jegyzékfájlt az *src/Edge/config* mappában.
1. Kattintson a jobb gombbal az **src/Edge/config/deployment.amd64.js**elemre, válassza a **központi telepítés létrehozása egyetlen eszközhöz**lehetőséget, majd válassza ki a peremhálózati eszköz nevét.

    ![Központi telepítés létrehozása egyetlen eszközhöz](../../../media/quickstarts/create-deployment-single-device.png)

1. Amikor a rendszer rákérdez a IoT Hub eszköz kiválasztására, válassza a **LVA-minta-eszköz** lehetőséget a legördülő menüből.
1. Körülbelül 30 másodperc elteltével frissítse az Azure IoT Hubt az ablak bal alsó sarkában. A peremhálózati eszköz mostantól a következő központilag telepített modulokat mutatja:

    * Élő videó-elemzés a IoT Edgeon (modul neve `lvaEdge` )
    * Valós idejű Streaming Protocol (RTSP) szimulátor (modul neve `rtspsim` )

Az RTSP-szimulátor modul egy élő videó streamet szimulál egy olyan videofájl használatával, amelyet a peremhálózati eszközre másoltak, amikor futtatta az [élő videó elemzési erőforrásainak telepítési parancsfájlját](https://github.com/Azure/live-video-analytics/tree/master/edge/setup). 

> [!NOTE]
> Ha saját peremhálózati eszközt használ a telepítési parancsfájlból kiépített helyett, nyissa meg a peremhálózati eszközét, és futtassa az alábbi parancsokat **rendszergazdai jogosultságokkal**az ehhez a rövid útmutatóhoz használt minta videofájl lekéréséhez és tárolásához:  

```
mkdir /home/lvaadmin/samples      
mkdir /home/lvaadmin/samples/input    
curl https://lvamedia.blob.core.windows.net/public/camera-300s.mkv > /home/lvaadmin/samples/input/camera-300s.mkv  
chown -R lvaadmin /home/lvaadmin/samples/  
```
Ebben a szakaszban a modulok üzembe vannak helyezve, de nincsenek aktív adathordozó-diagramok.
