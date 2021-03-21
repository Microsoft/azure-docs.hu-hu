---
ms.openlocfilehash: 3f92bae608284c8b619be34a0e08f15e831bf88e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "101749994"
---
A [IoT Edge üzembe helyezési jegyzékfájljának előállítása és telepítése](../../../detect-motion-emit-events-quickstart.md#generate-and-deploy-the-deployment-manifest) lépésben, a Visual Studio Code-ban bontsa ki a **LVA-Sample-Device** CSOMÓPONTOT az **Azure IoT hub** alatt (a bal alsó szakaszban). A következő modulokat kell látnia:

* A (z) nevű Live Video Analytics-modul `lvaEdge`
* A `rtspsim` modul, amely egy élő videó-hírcsatorna forrásaként szolgáló RTSP-kiszolgálót szimulál

  ![Modulok](../../../media/quickstarts/lva-sample-device-node.png)

> [!NOTE]
> A fenti lépések feltételezik, hogy a telepítési parancsfájl által létrehozott virtuális gépet használja. Ha ehelyett saját Edge-eszközt használ, lépjen a peremhálózati eszközre, és futtassa az alábbi parancsokat **rendszergazdai jogosultságokkal** a rövid útmutatóhoz használt minta videofájl lekéréséhez és tárolásához:  

```
mkdir /home/lvaedgeuser/samples
mkdir /home/lvaedgeuser/samples/input    
curl https://lvamedia.blob.core.windows.net/public/camera-300s.mkv > /home/lvaedgeuser/samples/input/camera-300s.mkv  
chown -R lvalvaedgeuser:localusergroup /home/lvaedgeuser/samples/  
```
