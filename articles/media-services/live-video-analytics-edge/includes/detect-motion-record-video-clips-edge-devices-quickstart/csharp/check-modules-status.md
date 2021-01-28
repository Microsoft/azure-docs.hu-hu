---
ms.openlocfilehash: 53052097fa6616f889b710c58488a9f7a616168d
ms.sourcegitcommit: 4e70fd4028ff44a676f698229cb6a3d555439014
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/28/2021
ms.locfileid: "98956289"
---
A [IoT Edge üzembe helyezési jegyzékfájljának előállítása és telepítése](../../../detect-motion-emit-events-quickstart.md#generate-and-deploy-the-deployment-manifest) lépésben, a Visual Studio Code-ban bontsa ki a **LVA-Sample-Device** CSOMÓPONTOT az **Azure IoT hub** alatt (a bal alsó szakaszban). A következő modulokat kell látnia:

* A (z) nevű Live Video Analytics-modul `lvaEdge`
* A `rtspsim` modul, amely egy élő videó-hírcsatorna forrásaként szolgáló RTSP-kiszolgálót szimulál

  ![Modulok](../../../media/quickstarts/lva-sample-device-node.png)

> [!NOTE]
> A fenti lépések feltételezik, hogy a telepítési parancsfájl által létrehozott virtuális gépet használja. Ha ehelyett saját Edge-eszközt használ, lépjen a peremhálózati eszközre, és futtassa az alábbi parancsokat **rendszergazdai jogosultságokkal** a rövid útmutatóhoz használt minta videofájl lekéréséhez és tárolásához:  

```
mkdir /home/lvaadmin/samples
mkdir /home/lvaadmin/samples/input    
curl https://lvamedia.blob.core.windows.net/public/camera-300s.mkv > /home/lvaadmin/samples/input/camera-300s.mkv  
chown -R lvaadmin /home/lvaadmin/samples/  
```
