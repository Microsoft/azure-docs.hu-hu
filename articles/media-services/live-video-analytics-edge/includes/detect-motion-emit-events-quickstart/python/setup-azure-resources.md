---
ms.openlocfilehash: 21b84a40621f5a529c09dc0c03e024c4ea6d5c95
ms.sourcegitcommit: b85ce02785edc13d7fb8eba29ea8027e614c52a2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/03/2021
ms.locfileid: "99531582"
---
Az oktatóanyaghoz a következő Azure-erőforrások szükségesek:

* IoT Hub
* A(z)
* Azure Media Services fiók
* Linux virtuális gép az Azure-ban, [IoT Edge futtatókörnyezettel](../../../../../iot-edge/how-to-install-iot-edge.md) telepítve

Ebben a rövid útmutatóban azt javasoljuk, hogy az Azure-előfizetésében lévő szükséges erőforrások üzembe helyezéséhez használja az [élő videó elemzési erőforrásainak telepítési parancsfájlját](https://github.com/Azure/live-video-analytics/tree/master/edge/setup) . Ehhez kövesse az alábbi lépéseket:

1. Nyissa meg az [Azure Cloud Shellt](https://ms.portal.azure.com/#cloudshell/).
    > [!div class="mx-imgBorder"]
    > :::image type="content" source="../../../media/quickstarts/cloud-shell.png" alt-text="Cloud Shell":::
1. Ha első alkalommal használja a Cloud Shell, a rendszer kérni fogja, hogy válasszon egy előfizetést egy Storage-fiók és egy Microsoft Azure-fájlmegosztás létrehozásához. Válassza a **tároló létrehozása** lehetőséget a Cloud Shell munkamenet-információhoz tartozó Storage-fiók létrehozásához. Ez a Storage-fiók elkülönül a parancsfájl által a Azure Media Services-fiókkal való használatra létrehozott fióktól.
1. A Cloud Shell ablak bal oldalán lévő legördülő menüben válassza a **bash** lehetőséget a környezetében.

    ![Környezeti választó](../../../media/quickstarts/env-selector.png)
1. Futtassa az alábbi parancsot.

    ```
    bash -c "$(curl -sL https://aka.ms/lva-edge/setup-resources-for-samples)"
    ```
    
    A szkript sikeres befejeződése után az előfizetésben szereplő összes szükséges erőforrást látnia kell. A szkript összesen 12 erőforrást állít be:
    1. **Folyamatos átviteli végpont** – ez segítséget nyújt a rögzített AMS-eszköz lejátszásában.
    1. **Virtuális gép** – ez egy virtuális gép, amely a peremhálózati eszközként fog működni.
    1. **Lemez** – ez egy olyan tárolóeszköz, amely a virtuális géphez van csatolva adathordozók és összetevők tárolásához.
    1. **Hálózati biztonsági csoport** – az Azure-beli virtuális hálózatokon lévő Azure-erőforrások felé irányuló és onnan érkező hálózati forgalom szűrésére szolgál.
    1. **Hálózati adapter** – lehetővé teszi, hogy egy Azure-beli virtuális gép kommunikáljon az internettel, az Azure-ral és más erőforrásokkal.
    1. **Megerősített kapcsolat** – ez lehetővé teszi, hogy a böngésző és a Azure Portal használatával kapcsolódjon a virtuális géphez.
    1. **Nyilvános IP-cím** – lehetővé teszi, hogy az Azure-erőforrások kommunikáljanak az internettel és a nyilvánosan elérhető Azure-szolgáltatásokkal
    1. **Virtuális hálózat** – az Azure-erőforrások, például a virtuális gépek számos típusa lehetővé teszi, hogy biztonságosan kommunikáljanak egymással, az internettel és a helyszíni hálózatokkal. További információ a [virtuális hálózatokról](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview)
    1. **IoT hub** – ez egy központi üzenetsor, amely a IoT-alkalmazás, a IoT Edge-modulok és az általa kezelt eszközök közötti kétirányú kommunikációra szolgál.
    1. **Media Service-fiók** – ez segíti az Azure-beli médiatartalmak felügyeletét és továbbítását.
    1. **Storage-fiók** – rendelkeznie kell egy elsődleges Storage-fiókkal, és tetszőleges számú másodlagos Storage-fiók társítható a Media Services-fiókjához. További információ: [Azure Storage-fiókok Azure Media Services fiókokkal](https://docs.microsoft.com/azure/media-services/latest/storage-account-concept).
    1. **Container Registry** – ez segít a privát Docker-tárolók rendszerképeinek és a kapcsolódó összetevők tárolásában és kezelésében.
1. A szkript befejeződése után válassza ki a kapcsos zárójeleket, hogy elérhetővé tegye a mappastruktúrát. A *~/clouddrive/LVA-Sample* könyvtárban néhány fájl jelenik meg. Ebben a rövid útmutatóban a következők szerepelnek:

     * ***~/clouddrive/LVA-Sample/Edge-Deployment/.env*** – ez a fájl olyan tulajdonságokat tartalmaz, amelyeket a Visual Studio Code használ a modulok peremhálózati eszközre való telepítéséhez.
     * ***~/clouddrive/lva-sample/appsetting.json*** -Visual Studio Code ezt a fájlt használja a mintakód futtatásához.
     
    Ezekre a fájlokra szüksége lesz, amikor a következő szakaszban a Visual Studio Code-ban beállítja a fejlesztési környezetet. Előfordulhat, hogy most egy helyi fájlba szeretné másolni őket.
    
    ![Alkalmazásbeállítások](../../../media/quickstarts/clouddrive.png)

> [!TIP]
> Ha a létrehozott Azure-erőforrásokkal kapcsolatos problémákba ütközik, tekintse meg a **[hibaelhárítási útmutatót](../../../troubleshoot-how-to.md#common-error-resolutions)** a gyakran előforduló problémák megoldásához.