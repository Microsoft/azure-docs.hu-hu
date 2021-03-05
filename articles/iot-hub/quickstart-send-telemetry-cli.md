---
title: Rövid útmutató – telemetria küldése az Azure IoT Hub (CLI) gyors üzembe helyezéséhez
description: Ebből a rövid útmutatóból megtudhatja, hogyan kezdheti meg a IoT Hub fejlesztőket, hogy az Azure CLI használatával hozzon létre egy IoT hubot, küldjön telemetria, és megtekintse az üzeneteket egy eszköz és a központ között.
ms.service: iot-hub
ms.topic: quickstart
ms.custom:
- iot-send-telemetry-cli
- iot-p0-scenario
- 'Role: Cloud Development'
- devx-track-azurecli
ms.author: timlt
author: timlt
ms.date: 11/06/2019
ms.openlocfilehash: 4671880490a9ce9e29f49ede0e7687bdcf639a7e
ms.sourcegitcommit: dda0d51d3d0e34d07faf231033d744ca4f2bbf4a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2021
ms.locfileid: "102199798"
---
# <a name="quickstart-send-telemetry-from-a-device-to-an-iot-hub-and-monitor-it-with-the-azure-cli"></a>Gyors útmutató: telemetria küldése egy eszközről egy IoT-hubhoz, és az Azure CLI-vel való figyelése

[!INCLUDE [iot-hub-quickstarts-1-selector](../../includes/iot-hub-quickstarts-1-selector.md)]

Az IoT Hub olyan Azure-szolgáltatás, amely lehetővé teszi nagy mennyiségű telemetria betöltését egy IoT-eszközről a felhőbe tárolás vagy feldolgozás céljából. Ebben a rövid útmutatóban az Azure CLI használatával létrehoz egy IoT Hub és egy szimulált eszközt, elküldheti az eszköz telemetria a központba, és elküldheti a felhőből az eszközre irányuló üzenetet. Az eszköz metrikáinak megjelenítéséhez a Azure Portal is használhatja. Ez egy alapszintű munkafolyamat azon fejlesztők számára, akik a CLI használatával kommunikálnak egy IoT Hub alkalmazással.

## <a name="prerequisites"></a>Előfeltételek
- Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egyet ingyen a](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) Kezdés előtt.
- Azure CLI. Ebben a rövid útmutatóban az összes parancsot futtathatja a böngészőben futtatott interaktív CLI-rendszerhéj Azure Cloud Shellával. Ha a Cloud Shell használja, semmit nem kell telepítenie. Ha a parancssori felület helyi használatát szeretné használni, ehhez a rövid útmutatóhoz az Azure CLI 2.0.76 vagy újabb verziójára lesz szükség. A verzió megkereséséhez futtassa a következő parancsot: az --version. Telepítéshez vagy frissítéshez: [Az Azure CLI telepítése]( /cli/azure/install-azure-cli).

## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra
Jelentkezzen be az Azure Portalra a https://portal.azure.com webhelyen.

Függetlenül attól, hogy helyileg futtatta-e a CLI-t, vagy a Cloud Shellban megnyitotta a portált a böngészőben.  Ezt a rövid útmutató későbbi részében használhatja.

## <a name="launch-the-cloud-shell"></a>A Cloud Shell elindítása
Ebben a szakaszban a Azure Cloud Shell egy példányát indítja el. Ha helyileg használja a CLI-t, ugorjon a [két CLI-munkamenet előkészítése](#prepare-two-cli-sessions)című szakaszra.

A Cloud Shell elindítása:

1. A Azure Portal jobb felső menüsorában kattintson a **Cloud Shell** gombra. 

    ![Azure Portal Cloud Shell gomb](media/quickstart-send-telemetry-cli/cloud-shell-button.png)

    > [!NOTE]
    > Ha első alkalommal használja a Cloud Shell, a rendszer kéri, hogy hozzon létre egy tárolót, amely a Cloud Shell használatához szükséges.  Válasszon egy előfizetést, és hozzon létre egy Storage-fiókot, és Microsoft Azure a fájlok megosztását. 

2. Válassza ki az előnyben részesített CLI-környezetet a **környezet kiválasztása** legördülő menüben. Ez a rövid útmutató a **bash** -környezetet használja. Az alábbi CLI-parancsok a PowerShell-környezetben is működnek. 

    ![CLI-környezet kiválasztása](media/quickstart-send-telemetry-cli/cloud-shell-environment.png)

## <a name="prepare-two-cli-sessions"></a>Két CLI-munkamenet előkészítése

Ebben a szakaszban két Azure CLI-munkamenetet készít elő. Ha a Cloud Shell használja, a két munkamenetet külön böngésző lapokon fogja futtatni. Ha helyi CLI-ügyfelet használ, két külön CLI-példányt fog futtatni. Az első munkamenetet szimulált eszközként fogja használni, és az üzenetek figyelésére és küldésére szolgáló második munkamenetet. Ha parancsot szeretne futtatni, válassza a **Másolás** elemet a rövid útmutatóban szereplő kódrészlet másolásához, illessze be a rendszerhéj-munkamenetbe, majd futtassa.

Az Azure CLI használatához be kell jelentkeznie az Azure-fiókjába. Az Azure CLI rendszerhéj-munkamenet és az IoT hub közötti kommunikáció hitelesítése és titkosítása megtörtént. Ennek eredményeképpen ehhez a rövid útmutatóhoz nincs szükség olyan további hitelesítésre, amelyet valódi eszközzel használ, például egy kapcsolatok sztringjét.

*  Futtassa az az [Extension Add](/cli/azure/extension#az-extension-add) parancsot az Azure CLI-hez készült Microsoft Azure IoT-BŐVÍTMÉNY a CLI-rendszerhéjhoz való hozzáadásához. Az IOT bővítmény a IoT Hub, IoT Edge és IoT Device kiépítési szolgáltatás (DPS) adott parancsait hozzáadja az Azure CLI-hez.

   ```azurecli
   az extension add --name azure-iot
   ```
   
   Az Azure IOT bővítmény telepítése után nem kell újra telepítenie a Cloud Shell-munkamenetben. 

   [!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

*  Nyisson meg egy második CLI-munkamenetet.  Ha a Cloud Shell használja, válassza az **új munkamenet megnyitása** lehetőséget. Ha helyileg használja a CLI-t, nyisson meg egy második példányt. 

    >[!div class="mx-imgBorder"]
    >![Új Cloud Shell munkamenet megnyitása](media/quickstart-send-telemetry-cli/cloud-shell-new-session.png)

## <a name="create-an-iot-hub"></a>IoT Hub létrehozása
Ebben a szakaszban az Azure CLI használatával hozzon létre egy erőforráscsoportot és egy IoT Hub.  Az Azure-erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat. Az IoT Hub központi üzenetsorként szolgálnak a IoT-alkalmazás és az eszközök közötti kétirányú kommunikációhoz. 

> [!TIP]
> Igény szerint létrehozhat egy Azure-erőforráscsoportot, egy IoT Hub és más erőforrásokat a [Azure Portal](iot-hub-create-through-portal.md), a [Visual Studio Code](iot-hub-create-use-iot-toolkit.md)vagy más programozott módszer használatával.  

1. Futtassa az az [Group Create](/cli/azure/group#az-group-create) parancsot egy erőforráscsoport létrehozásához. A következő parancs létrehoz egy *MyResourceGroup* nevű erőforráscsoportot a *eastus* helyen. 

    ```azurecli
    az group create --name MyResourceGroup --location eastus
    ```

1. Futtassa az az [IOT hub Create](/cli/azure/iot/hub#az-iot-hub-create) parancsot egy IOT hub létrehozásához. Az IoT hub létrehozása eltarthat néhány percig. 

    *YourIotHubName*. Az alábbi helyőrzőt cserélje le az IoT hub számára kiválasztott névre. Az IoT hub nevének globálisan egyedinek kell lennie az Azure-ban. Ezt a helyőrzőt használjuk a rövid útmutató további részében, hogy az IoT hub nevét képviseljék.

    ```azurecli
    az iot hub create --resource-group MyResourceGroup --name {YourIoTHubName}
    ```

## <a name="create-and-monitor-a-device"></a>Eszköz létrehozása és figyelése
Ebben a szakaszban egy szimulált eszközt hoz létre az első CLI-munkamenetben. A szimulált eszköz az eszköz telemetria az IoT hubhoz küldi. A második CLI-munkamenetben figyelheti az eseményeket és a telemetria, és elküldheti a felhőből az eszközre irányuló üzeneteket a szimulált eszközre.

Szimulált eszköz létrehozása és elindítása:
1. Futtassa az az [IOT hub Device-Identity Create](/cli/azure/ext/azure-iot/iot/hub/device-identity#ext-azure-iot-az-iot-hub-device-identity-create) parancsot az első CLI-munkamenetben. Ezzel létrehozza a szimulált eszköz identitását. 

    *YourIotHubName*. Az alábbi helyőrzőt cserélje le az IoT hub számára kiválasztott névre. 

    *simDevice*. Ezt a nevet közvetlenül is használhatja a szimulált eszközhöz a rövid útmutató további részében. Igény szerint más nevet is használhat. 

    ```azurecli
    az iot hub device-identity create --device-id simDevice --hub-name {YourIoTHubName} 
    ```

1. Futtassa az az [IOT Device szimulálás](/cli/azure/ext/azure-iot/iot/device#ext-azure-iot-az-iot-device-simulate) parancsot az első CLI-munkamenetben.  Ezzel elindítja a szimulált eszközt. Az eszköz telemetria küld az IoT hubhoz, és üzeneteket fogad belőle.  

    *YourIotHubName*. Az alábbi helyőrzőt cserélje le az IoT hub számára kiválasztott névre. 

    ```azurecli
    az iot device simulate -d simDevice -n {YourIoTHubName}
    ```

Eszköz figyelése:
1. A második CLI-munkamenetben futtassa az az [IOT hub monitor-Events](/cli/azure/ext/azure-iot/iot/hub#ext-azure-iot-az-iot-hub-monitor-events) parancsot. Ez elindítja a szimulált eszköz figyelését. A kimenetben látható, hogy a szimulált eszköz az IoT hubhoz küldi a telemetria.

    *YourIotHubName*. Az alábbi helyőrzőt cserélje le az IoT hub számára kiválasztott névre. 

    ```azurecli
    az iot hub monitor-events --output table --hub-name {YourIoTHubName}
    ```

    ![Események figyelése Cloud Shell](media/quickstart-send-telemetry-cli/cloud-shell-monitor.png)

1. Miután megfigyelte a szimulált eszközt a második CLI-munkamenetben, nyomja le a CTRL + C billentyűkombinációt a figyelés leállításához. 

## <a name="use-the-cli-to-send-a-message"></a>Üzenet küldése a CLI használatával
Ebben a szakaszban a második CLI-munkamenet használatával küld üzenetet a szimulált eszköznek.

1. Az első CLI-munkamenetben ellenőrizze, hogy fut-e a szimulált eszköz. Ha az eszköz leállt, futtassa a következő parancsot az elindításához:

    *YourIotHubName*. Az alábbi helyőrzőt cserélje le az IoT hub számára kiválasztott névre. 

    ```azurecli
    az iot device simulate -d simDevice -n {YourIoTHubName}
    ```

1. A második CLI-munkamenetben futtassa az az [IOT Device C2D-Message Send](/cli/azure/ext/azure-iot/iot/device/c2d-message#ext-azure-iot-az-iot-device-c2d-message-send) parancsot. Ez egy felhőből az eszközre irányuló üzenetet küld az IoT hub-ról a szimulált eszközre. Az üzenet tartalmaz egy karakterláncot és két kulcs-érték párokat.  

    *YourIotHubName*. Az alábbi helyőrzőt cserélje le az IoT hub számára kiválasztott névre. 

    ```azurecli
    az iot device c2d-message send -d simDevice --data "Hello World" --props "key0=value0;key1=value1" -n {YourIoTHubName}
    ```
    Lehetőség van arra is, hogy a Azure Portal használatával üzeneteket küldjön a felhőből az eszközre. Ehhez keresse meg a IoT Hub áttekintés lapját, válassza ki a **IoT eszközök** elemet, válassza ki a szimulált eszközt, és válassza az **üzenet az eszköznek** lehetőséget. 

1. Az első CLI-munkamenetben ellenőrizze, hogy a szimulált eszköz fogadta-e az üzenetet. 

    ![Cloud Shell a felhőből az eszközre üzenet](media/quickstart-send-telemetry-cli/cloud-shell-receive-message.png)

1. Miután megtekintette az üzenetet, zárjuk be a második CLI-munkamenetet. Tartsa meg az első CLI-munkamenetet. Ezzel egy későbbi lépésben törli az erőforrásokat.

## <a name="view-messaging-metrics-in-the-portal"></a>Üzenetkezelési mérőszámok megtekintése a portálon
A Azure Portal lehetővé teszi a IoT Hub és az eszközök összes aspektusának kezelését. Egy tipikus IoT Hub alkalmazásban, amely az eszközökről végez telemetria, érdemes figyelni az eszközöket, vagy megtekinteni a mérőszámokat az eszköz telemetria. 

Üzenetküldési metrikák megjelenítése a Azure Portalban:
1. A portál bal oldali navigációs menüjében válassza a **minden erőforrás** elemet. Ez felsorolja az előfizetésben található összes erőforrást, beleértve a létrehozott IoT hubot is. 

1. Válassza ki a létrehozott IoT hub hivatkozását. A portál megjeleníti a központ áttekintés lapját.

1. A IoT Hub bal oldali paneljén válassza a **metrikák** lehetőséget. 

    ![Üzenetkezelési metrikák IoT Hub](media/quickstart-send-telemetry-cli/iot-hub-portal-metrics.png)

1. Adja meg az IoT hub nevét a **hatókörben**.

2. Válassza az *IOT hub standard mérőszámok* elemet a **metrikus névtérben**.

3. Válassza ki a **metrikában** *használt üzenetek teljes számát* . 

4. Vigye az egérmutatót az idősor azon területére, amelyben az eszköz üzenetet küldött. Az időponthoz tartozó üzenetek teljes száma az idősor bal alsó sarkában jelenik meg.

    ![Azure IoT Hub mérőszámok megtekintése](media/quickstart-send-telemetry-cli/iot-hub-portal-view-metrics.png)

5. Igény szerint a **metrika** legördülő menüvel más mérőszámokat jeleníthet meg a szimulált eszközön. Például a *C2d üzenetek kézbesítésének befejezése* vagy az *összes eszköz (előzetes verzió)*. 

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása
Ha már nincs szüksége az ebben a rövid útmutatóban létrehozott Azure-erőforrásokra, az Azure CLI használatával törölheti őket.

Ha folytatja a következő javasolt cikket, megtarthatja a már létrehozott erőforrásokat, és újra felhasználhatja azokat. 

> [!IMPORTANT]
> Az erőforráscsoport törlése nem vonható vissza. Az erőforráscsoport és a benne foglalt erőforrások véglegesen törlődnek. Figyeljen arra, hogy ne töröljön véletlenül erőforráscsoportot vagy erőforrásokat. 

Erőforráscsoport törlése név alapján:
1. Futtassa az az [Group delete](/cli/azure/group#az-group-delete) parancsot. Ezzel eltávolítja az erőforráscsoportot, a IoT Hub és a létrehozott eszköz regisztrációját.

    ```azurecli
    az group delete --name MyResourceGroup
    ```
1. Futtassa az az [Group List](/cli/azure/group#az-group-list) parancsot az erőforráscsoport törlésének megerősítéséhez.  

    ```azurecli
    az group list
    ```

## <a name="next-steps"></a>Következő lépések
Ebben a rövid útmutatóban az Azure CLI-t használta egy IoT hub létrehozásához, egy szimulált eszköz létrehozásához, a telemetria elküldéséhez, a telemetria figyeléséhez, a felhőből az eszközre irányuló üzenetek küldéséhez és az erőforrások törléséhez. Az eszközön az üzenetküldési metrikák megjelenítéséhez a Azure Portal használta.

Ha Ön egy eszköz fejlesztője, a javasolt következő lépés a telemetria rövid útmutatója, amely az Azure IoT Device SDK-t használja a C-hez. opcionálisan tekintse meg az egyik elérhető Azure IoT Hub telemetria gyors üzembe helyezési cikkét az Ön által választott nyelven vagy SDK-ban.

> [!div class="nextstepaction"]
> [Gyors útmutató: telemetria küldése az eszközről egy IoT-hubhoz (C)](quickstart-send-telemetry-c.md)