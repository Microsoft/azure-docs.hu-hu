---
title: Üzembe helyezés egy IoT Edge Linuxon a Windows rendszeren – Azure
description: Ez a cikk útmutatást nyújt a Windows-eszközök Linux rendszerű IoT Edge való üzembe helyezéséhez.
ms.topic: how-to
ms.date: 02/18/2021
ms.openlocfilehash: d5c3d89ae7447b062714ad90be117a6426a39581
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2021
ms.locfileid: "105561083"
---
# <a name="deploy-to-an-iot-edge-for-linux-on-windows-eflow-device"></a>Üzembe helyezés egy IoT Edge for Linux on Windows (EFLOW) eszközön

Ebből a cikkből megtudhatja, hogyan helyezheti üzembe az élő videók elemzését egy olyan peremhálózati eszközön, amelyen [IoT Edge Linux rendszeren (EFLOW)](../../iot-edge/iot-edge-for-linux-on-windows.md). Miután végzett a jelen dokumentumban ismertetett lépésekkel, futtathat egy olyan [adathordozó-diagramot](media-graph-concept.md) , amely észleli a videón való mozgást, és ilyen eseményeket bocsát ki a felhőben lévő IoT hubhoz. Ezt követően a speciális forgatókönyvek esetében kikapcsolhatja a Media Graphot, és a Windows-alapú IoT Edge eszközön elérhetővé teheti az élő video-elemzési lehetőségeket.

## <a name="prerequisites"></a>Előfeltételek 

* Egy aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egy fiókot ingyenesen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) , ha még nem rendelkezik ilyennel.

    > [!NOTE]
    > Szüksége lesz egy Azure-előfizetésre, amely az egyszerű szolgáltatások létrehozásához szükséges engedélyekkel rendelkezik (ez a **tulajdonosi szerepkör** ). Ha nem rendelkezik a megfelelő engedélyekkel, forduljon a fiók rendszergazdájához, és adja meg a megfelelő engedélyeket.
* A [Visual Studio Code](https://code.visualstudio.com/) a fejlesztői gépen. Győződjön meg arról, hogy rendelkezik az [Azure IoT Tools bővítménnyel](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools).
* Olvassa el [a mi az EFLOW](../../iot-edge/iot-edge-for-linux-on-windows.md).

## <a name="deployment-steps"></a>A központi telepítés lépései

A következő ábra a dokumentum teljes folyamatát ábrázolja, és 5 egyszerű lépéssel az élő videó-elemzések futtatására van beállítva egy olyan Windows-eszközön, amely EFLOW rendelkezik:

:::image type="content" source="./media/deploy-iot-edge-linux-on-windows/eflow.png" alt-text="IoT Edge Linux-on Windows (EFLOW) diagramon":::

1. [Telepítse a EFLOW](../../iot-edge/how-to-install-iot-edge-on-windows.md) a Windows-eszközre. 

    1. Ha Windows rendszerű SZÁMÍTÓGÉPét használja, akkor a [Windows felügyeleti központ](/windows-server/manage/windows-admin-center/overview) kezdőlapján, a kapcsolatok listájában megjelenik egy helyi gazdagép-kapcsolat, amely a Windows felügyeleti központot futtató számítógépet jelképezi. 
    1. Az Ön által kezelt további kiszolgálók, számítógépek vagy fürtök itt is megjelennek.
    1. A Windows felügyeleti központ használatával telepítheti és kezelheti az Azure-EFLOW a helyi eszközön vagy a távoli felügyelt eszközökön. Ebben az útmutatóban a helyi gazdagép-kapcsolatok a Windows rendszerű Linux rendszerhez készült Azure IoT Edge központi telepítéséhez célként használt eszközként szolgálnak. Ezért láthatja, hogy a localhost IoT Edge eszközként is szerepel.

    :::image type="content" source="./media/deploy-iot-edge-linux-on-windows/windows-admin-center.png" alt-text="Központi telepítések lépései – Windows felügyeleti központ":::
1. Kattintson a IoT Edge eszközre a kapcsolódáshoz, és megjelenik az Áttekintés és a parancs-rendszerhéj lap. A parancs-rendszerhéj lapon adhatók ki parancsok a peremhálózati eszközön.
 
    :::image type="content" source="./media/deploy-iot-edge-linux-on-windows/azure-iot-edge-manager.png" alt-text="Üzembe helyezési lépések – Azure IoT Edge Manager":::
1. Nyissa meg a parancssort, és írja be a következő parancsot:
    
    ```bash
    bash -c "$(curl -sL https://aka.ms/lva-edge/prep_device)"
    ```

    Az élő video Analytics-modul a peremhálózati eszközön fut, nem Kiemelt [helyi felhasználói fiókkal](deploy-iot-edge-device.md#create-and-use-local-user-account-for-deployment). Emellett [szükség van bizonyos helyi mappákra](deploy-iot-edge-device.md#granting-permissions-to-device-storage) az alkalmazás-konfigurációs adattároláshoz. Végezetül, ebben a útmutatóban egy olyan [RTSP-szimulátort](https://github.com/Azure/live-video-analytics/tree/master/utilities/rtspsim-live555) használunk, amely valós időben közvetít egy videó-hírcsatornát, hogy LVA a modult az elemzéshez. Ez a szimulátor bemenetként előre rögzített videofájlokat használ a bemeneti könyvtárból. 
    
    A fenti előkészítő eszköz parancsfájlja automatizálja ezeket a feladatokat, így egyetlen parancsot futtathat, és minden megfelelő bemeneti és konfigurációs mappát, videó bemeneti fájlt és felhasználói fiókot zökkenőmentesen hozhat létre. A parancs sikeres befejeződése után a peremhálózati eszközön létrehozott következő mappáknak kell megjelennie. 
    
    * `/home/lvaedgeuser/samples`
    * `/home/lvaedgeuser/samples/input`
    * `/var/lib/azuremediaservices`
    * `/var/media`
    
    Jegyezze fel a/Home/lvaedgeuser/Samples/input mappában található videofájlokat (*. mkv), amely az elemezni kívánt bemeneti fájlokként szolgál. 
    
    :::image type="content" source="./media/deploy-iot-edge-linux-on-windows/azure-iot-edge-manager-analysis.png" alt-text="Elemzés":::
1. Most, hogy beállította a peremhálózati eszközt, regisztrálva van a központban, és sikeresen fut a megfelelő mappastruktúrát, a következő lépés az Azure-erőforrások alábbi további erőforrásainak beállítása és a LVA modul üzembe helyezése. 

    * Tárfiók
    * Azure Media Services fiók

    Ezért javasoljuk, hogy az [élő videó elemzési erőforrásainak telepítési parancsfájlját](https://github.com/Azure/live-video-analytics/tree/master/edge/setup) használva telepítse a szükséges erőforrásokat az Azure-előfizetésében. Ehhez kövesse az alábbi lépéseket:

    1. Nyissa meg az [Azure Cloud Shellt](https://ms.portal.azure.com/#cloudshell/).

        :::image type="content" source="./media/deploy-iot-edge-linux-on-windows/azure-resources-cloud-shell.png" alt-text="Cloud Shell":::
    1. Ha első alkalommal használja a Cloud Shell, a rendszer kérni fogja, hogy válasszon egy előfizetést egy Storage-fiók és egy Microsoft Azure-fájlmegosztás létrehozásához. Válassza a **tároló létrehozása** lehetőséget a Cloud Shell munkamenet-információhoz tartozó Storage-fiók létrehozásához. Ez a Storage-fiók elkülönül a parancsfájl által a Azure Media Services-fiókkal való használatra létrehozott fióktól.
    1. A Cloud Shell ablak bal oldalán lévő legördülő menüben válassza a bash lehetőséget a környezetében.

        :::image type="content" source="./media/deploy-iot-edge-linux-on-windows/bash.png" alt-text="Bash-környezet":::
    1. Futtassa az alábbi parancsot.

        ```bash
        bash -c "$(curl -sL https://aka.ms/lva-edge/setup-resources-for-samples)"
        ```
        
        Ügyeljen arra, hogy válassza az **Y** lehetőséget, ha a rendszer rákérdez, hogy kiválassza a saját peremhálózati eszközét IoT Edge eszközként, mert az eszközt és a IoT hub korábban is létrehozta. A rendszer kérni fogja a IoT Hub nevét és IoT Edge eszköz azonosítóját is. Mindkettőt bejelentkezve bejelentkezhet a Azure Portalba, és rákattinthat a IoT Hubra, majd az IoT Edge lehetőségre a IoT Hub portál paneljén.

        :::image type="content" source="./media/deploy-iot-edge-linux-on-windows/iot-edge-devices.png" alt-text="Lásd: IoT Edge eszközök":::

    Ha elkészült, lépjen vissza a IoT Edge eszköz parancs-rendszerhéjba, és futtassa a következő parancsot.
    
    `sudo iotedge list`
    
    A peremhálózati eszközön üzembe helyezett és futó következő négy modulnak kell megjelennie. Vegye figyelembe, hogy az erőforrás-létrehozási parancsfájl üzembe helyezi a LVA modult IoT Edge modulokkal (edgeAgent és edgeHub) és egy RTSP szimulátorral, amely biztosítja a szimulált RTSP videó-hírcsatornát.
    
    :::image type="content" source="./media/deploy-iot-edge-linux-on-windows/running.png" alt-text="Állapot megtekintése":::
1. A telepített és beállított modulok esetében készen áll az első LVA Media Graph futtatására a EFLOW-on. Az alábbi lépéseket követve futtathat egy egyszerű mozgásészlelési gráfot, és megjelenítheti az eredményeket az alábbi lépések végrehajtásával:

    :::image type="content" source="./media/analyze-live-video/motion-detection.svg" alt-text="Mozgásészlelési gráf":::

    1. [Konfigurálja](get-started-detect-motion-emit-events-quickstart.md#configure-the-azure-iot-tools-extension) az Azure IoT Tools bővítményt.
    
        > [!Note]
        > Használja a következő elérési utat: `~/clouddrive/lva_byod/appsettings.json. - instead of ~/clouddrive/lva-sample/appsettings.json` .
    1. Állítsa be a topológiát, hozzon létre egy gráfot, és aktiválja ezeket a [közvetlen metódus-hívásokkal](get-started-detect-motion-emit-events-quickstart.md#use-direct-method-calls).
    1. [Figyelje meg az eredményeket](get-started-detect-motion-emit-events-quickstart.md#observe-results) a központban.
    1. A [tisztítási módszerek](get-started-detect-motion-emit-events-quickstart.md#invoke-graphinstancedeactivate)meghívása.
    1. Ha még nem szükséges, törölje az erőforrásokat.

        > [!IMPORTANT]
        > A nem törölt erőforrások továbbra is aktívak, és Azure-költségekkel járnak.
    
## <a name="next-steps"></a>Következő lépések

* Próbálja ki a mozgásérzékelőket a felhőben a kapcsolódó videók rögzítésével együtt. Kövesse a [mozgás észlelése, videoklipek rögzítése Media Services](detect-motion-record-video-clips-media-services-quickstart.md#review-the-sample-video) rövid útmutató lépéseit.
* [AI futtatása élő videón](use-your-model-quickstart.md#overview) (kihagyhatja az előfeltételként megadott beállítást, mert már a fentiekben is megtörtént)
* A [vs Code bővítmény](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.live-video-analytics-edge) használatával további adathordozó-diagramokat jeleníthet meg.
* Használjon olyan [IP-kamerát](https://en.wikipedia.org/wiki/IP_camera)  , amely támogatja az RTSP-t az RTSP-szimulátor használata helyett. Az RTSP-t támogató IP-kamerákat a [ONVIF-megfelelőségi termékek](https://www.onvif.org/conformant-products/) lapon találja. Keresse meg azokat az eszközöket, amelyek megfelelnek a G, S vagy T profiloknak.