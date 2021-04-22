---
title: Rövid útmutató – Telemetria küldése a Azure IoT Hub (CLI) rövid útmutatója
description: Ez a rövid útmutató bemutatja a fejlesztőknek IoT Hub hogyan hozhatnak létre IoT Hubot, küldhetnek telemetriát és megtekinthetik az eszközök és a központ közötti üzeneteket az Azure CLI használatával.
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
ms.openlocfilehash: 843db24707b8c826fe48e9d50aa7ec5bc135399f
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2021
ms.locfileid: "107863604"
---
# <a name="quickstart-send-telemetry-from-a-device-to-an-iot-hub-and-monitor-it-with-the-azure-cli"></a>Rövid útmutató: Telemetria küldése egy eszközről IoT Hubra, és monitor az Azure CLI használatával

[!INCLUDE [iot-hub-quickstarts-1-selector](../../includes/iot-hub-quickstarts-1-selector.md)]

Az IoT Hub olyan Azure-szolgáltatás, amely lehetővé teszi nagy mennyiségű telemetria betöltését egy IoT-eszközről a felhőbe tárolás vagy feldolgozás céljából. Ebben a rövid útmutatóban az Azure CLI használatával létrehoz egy IoT Hub-t és egy szimulált eszközt, eszköz-telemetriát küld a hubra, és egy felhőből egy eszközre küldött üzenetet küld. A vizualizációs Azure Portal eszközmetrikák megjelenítésére is használhatja. Ez egy alapszintű munkafolyamat a CLI-t egy alkalmazással IoT Hub interakciókhoz.

## <a name="prerequisites"></a>Előfeltételek
- Ha nem rendelkezik Azure-előfizetéssel, a kezdés előtt [hozzon](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) létre egyet ingyenesen.
- Azure CLI. Ebben a rövid útmutatóban az összes parancsot futtathatja a böngészőben Azure Cloud Shell interaktív PARANCSSORi felület használatával. Ha a Cloud Shell, semmit sem kell telepítenie. Ha inkább helyileg szeretné használni a CLI-t, ehhez a rövid útmutatóhoz az Azure CLI 2.0.76-os vagy újabb verziójára lesz szükség. A verzió megkereséséhez futtassa a következő parancsot: az --version. Telepítéshez vagy frissítéshez: [Az Azure CLI telepítése]( /cli/azure/install-azure-cli).

## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra
Jelentkezzen be az Azure Portalra a https://portal.azure.com webhelyen.

Függetlenül attól, hogy a CLI-t helyileg vagy a Cloud Shell futtatja, a portált nyissa meg a böngészőben.  Ezt a rövid útmutató későbbi, későbbi, használatával is felhasználhatja.

## <a name="launch-the-cloud-shell"></a>Indítsa el a Cloud Shell
Ebben a szakaszban elindítja a Azure Cloud Shell. Ha helyileg használja a CLI-t, ugorjon a [Két CLI-munkamenet előkészítése című szakaszra.](#prepare-two-cli-sessions)

A következő Cloud Shell:

1. Kattintson **a Cloud Shell** menüsáv jobb felső menüsávjában található Azure Portal. 

    ![Azure Portal Cloud Shell gomb](media/quickstart-send-telemetry-cli/cloud-shell-button.png)

    > [!NOTE]
    > Ha első alkalommal használja a Cloud Shell, a rendszer felkéri, hogy hozzon létre egy tárolót, amely szükséges a Cloud Shell.  Válasszon ki egy előfizetést, hogy létrehoz egy tárfiókot, és Microsoft Azure fájlokat. 

2. Válassza ki a kívánt CLI-környezetet a **Környezet kiválasztása legördülő** menüben. Ez a rövid útmutató a **Bash-környezetet** használja. Az alábbi CLI-parancsok a PowerShell-környezetben is működnek. 

    ![A CLI-környezet kiválasztása](media/quickstart-send-telemetry-cli/cloud-shell-environment.png)

## <a name="prepare-two-cli-sessions"></a>Két CLI-munkamenet előkészítése

Ebben a szakaszban két Azure CLI-munkamenetet fog előkészíteni. Ha a böngészőt használja, Cloud Shell a két munkamenetet külön böngészőlapon fogja futtatni. Ha helyi CLI-ügyfelet használ, két különálló CLI-példányt fog futtatni. Az első munkamenetet szimulált eszközként, a második munkamenetet pedig üzenetek figyelését és küldését fogja használni. Parancs futtatásához válassza a **Másolás** lehetőséget a rövid útmutatóban található kódblokk másoláshoz, illessze be a rendszerhéj-munkamenetbe, majd futtassa azt.

Az Azure CLI használatához be kell jelentkeznie az Azure-fiókjába. Az Azure CLI felületi munkamenete és az IoT Hub közötti minden kommunikáció hitelesítve és titkosítva van. Ennek eredményeképpen ehhez a rövid útmutatóhoz nincs szükség további hitelesítésre, amit valódi eszközzel használna, például egy kapcsolati sztringre.

*  Futtassa [az az extension add](/cli/azure/extension#az_extension_add) parancsot, hogy hozzáadja Microsoft Azure Azure CLI-hez Microsoft Azure IoT-bővítményt a CLI-felülethez. Az IOT-bővítmény IoT Hub, IoT Edge és IoT Device Provisioning Service-specifikus parancsokat ad hozzá az Azure CLI-hez.

   ```azurecli
   az extension add --name azure-iot
   ```
   
   Az Azure IOT-bővítmény telepítése után nem kell újra telepítenie azt egy Cloud Shell munkamenetben. 

   [!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

*  Nyisson meg egy második CLI-munkamenetet.  Ha az új munkamenetet használja, válassza Cloud Shell **Új munkamenet megnyitása lehetőséget.** Ha helyileg használja a CLI-t, nyisson meg egy második példányt. 

    >[!div class="mx-imgBorder"]
    >![Új Cloud Shell megnyitása](media/quickstart-send-telemetry-cli/cloud-shell-new-session.png)

## <a name="create-an-iot-hub"></a>IoT Hub létrehozása
Ebben a szakaszban az Azure CLI használatával fog létrehozni egy erőforráscsoportot és egy IoT Hub.  Az Azure-erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat. A IoT Hub központi üzenetközpontként működik az IoT-alkalmazás és az eszközök közötti kétirányú kommunikációhoz. 

> [!TIP]
> Ha szükséges, a Azure Portal , a [Visual Studio](iot-hub-create-through-portal.md) [Code](iot-hub-create-use-iot-toolkit.md)vagy más programozott metódusok használatával is létrehozhat Azure-erőforráscsoportot, IoT Hub-erőforráscsoportot és egyéb erőforrásokat.  

1. Futtassa [az az group create parancsot](/cli/azure/group#az_group_create) egy erőforráscsoport létrehozásához. A következő parancs létrehoz egy *MyResourceGroup* nevű erőforráscsoportot az *eastus* helyen. 

    ```azurecli
    az group create --name MyResourceGroup --location eastus
    ```

1. Futtassa [az az iot hub create parancsot](/cli/azure/iot/hub#az_iot_hub_create) egy IoT Hub létrehozásához. Az IoT Hub létrehozása eltarthat néhány percig. 

    *YourIotHubName .* Cserélje le az alábbi helyőrzőt az IoT Hubhoz választott névre. Az IoT Hub nevének globálisan egyedinek kell lennie az Azure-ban. Ez a helyőrző a rövid útmutató hátralévő részében az IoT Hub nevét képviseli.

    ```azurecli
    az iot hub create --resource-group MyResourceGroup --name {YourIoTHubName}
    ```

## <a name="create-and-monitor-a-device"></a>Eszköz létrehozása és figyelése
Ebben a szakaszban egy szimulált eszközt hoz létre az első CLI-munkamenetben. A szimulált eszköz telemetriát küld az IoT Hubnak. A második CLI-munkamenetben megfigyelheti az eseményeket és a telemetriát, és elküldheti a felhőből az eszközre küldött üzenetet a szimulált eszközre.

Szimulált eszköz létrehozása és elindítani:
1. Futtassa [az az iot hub device-identity create parancsot](/cli/azure/iot/hub/device-identity#az_iot_hub_device_identity_create) az első CLI-munkamenetben. Ez létrehozza a szimulált eszközidentitást. 

    *YourIotHubName .* Cserélje le az alábbi helyőrzőt az IoT Hubhoz választott névre. 

    *simDevice*. Ezt a nevet használhatja közvetlenül a szimulált eszközhöz a rövid útmutató hátralévő részében. Másik nevet is használhat. 

    ```azurecli
    az iot hub device-identity create --device-id simDevice --hub-name {YourIoTHubName} 
    ```

1. Futtassa [az az iot device simulate parancsot](/cli/azure/iot/device#az_iot_device_simulate) az első CLI-munkamenetben.  Ez elindítja a szimulált eszközt. Az eszköz telemetriát küld az IoT Hubnak, és üzeneteket fogad tőle.  

    *YourIotHubName*. Cserélje le az alábbi helyőrzőt az IoT Hubhoz választott névre. 

    ```azurecli
    az iot device simulate -d simDevice -n {YourIoTHubName}
    ```

Eszköz figyelése:
1. A második CLI-munkamenetben futtassa [az az iot hub monitor-events](/cli/azure/iot/hub#az_iot_hub_monitor_events) parancsot. Ez elindítja a szimulált eszköz monitorozását. A kimenet a szimulált eszköz által az IoT Hubnak küldött telemetriát jeleníti meg.

    *YourIotHubName*. Cserélje le az alábbi helyőrzőt az IoT Hubhoz választott névre. 

    ```azurecli
    az iot hub monitor-events --output table --hub-name {YourIoTHubName}
    ```

    ![Cloud Shell figyelése](media/quickstart-send-telemetry-cli/cloud-shell-monitor.png)

1. Miután a második CLI-munkamenetben figyelte a szimulált eszközt, nyomja le a Ctrl+C billentyűkombinációt a figyelés felfüggesztéséhez. 

## <a name="use-the-cli-to-send-a-message"></a>Üzenet küldése a CLI használatával
Ebben a szakaszban a második CLI-munkamenettel küld üzenetet a szimulált eszközre.

1. Az első CLI-munkamenetben ellenőrizze, hogy fut-e a szimulált eszköz. Ha az eszköz leállt, futtassa a következő parancsot az indításhoz:

    *YourIotHubName .* Cserélje le az alábbi helyőrzőt az IoT Hubhoz választott névre. 

    ```azurecli
    az iot device simulate -d simDevice -n {YourIoTHubName}
    ```

1. A második CLI-munkamenetben futtassa [az az iot device c2d-message send](/cli/azure/iot/device/c2d-message#az_iot_device_c2d-message-send) parancsot. Ez egy felhőből egy eszközre küldött üzenetet küld az IoT Hubról a szimulált eszközre. Az üzenet egy sztringet és két kulcs-érték párt tartalmaz.  

    *YourIotHubName .* Cserélje le az alábbi helyőrzőt az IoT Hubhoz választott névre. 

    ```azurecli
    az iot device c2d-message send -d simDevice --data "Hello World" --props "key0=value0;key1=value1" -n {YourIoTHubName}
    ```
    A felhőből az eszközre is küldhet üzeneteket a Azure Portal. Ehhez keresse meg az eszköz áttekintési lapját, válassza IoT Hub **IoT-eszközök** lehetőséget, válassza ki a szimulált eszközt, majd válassza az **Üzenet az eszköznek lehetőséget.** 

1. Az első CLI-munkamenetben ellenőrizze, hogy a szimulált eszköz megkapta-e az üzenetet. 

    ![Cloud Shell a felhőből az eszközre üzenet](media/quickstart-send-telemetry-cli/cloud-shell-receive-message.png)

1. Az üzenet megtekintése után zárja be a második CLI-munkamenetet. Ne nyissa meg az első CLI-munkamenetet. Egy későbbi lépésben erőforrásokat fog megtisztítani vele.

## <a name="view-messaging-metrics-in-the-portal"></a>Üzenetkezelési metrikák megtekintése a portálon
A Azure Portal lehetővé teszi a felhasználók és eszközök IoT Hub kezelését. Egy tipikus IoT Hub, amely telemetriát küld az eszközökről, érdemes lehet figyelni az eszközöket, vagy megtekinteni az eszköz telemetriával kapcsolatos metrikákat. 

Üzenetkezelési metrikák vizualizációja a Azure Portal:
1. A portál bal oldali navigációs menüjében válassza a Minden **erőforrás lehetőséget.** Ez felsorolja az előfizetésben található összes erőforrást, beleértve a létrehozott IoT Hubot is. 

1. Válassza ki a létrehozott IoT Hubon található hivatkozást. A portál megjeleníti a központ áttekintő lapját.

1. A **bal oldali panelen** válassza a Metrikák IoT Hub. 

    ![IoT Hub üzenetkezelési metrikák](media/quickstart-send-telemetry-cli/iot-hub-portal-metrics.png)

1. Adja meg az IoT Hub nevét a **hatókörben.**

2. Válassza *az Iot Hub Standard Metrics (Iot Hub Standard metrikák)* lehetőséget a **Metric Namespace (Metrikanévtér) mezőben.**

3. Válassza *a Metrika által használt üzenetek teljes* **számát.** 

4. Vigye az egérmutatót az idővonalnak arra a területére, amelyen az eszköz üzeneteket küldött. Az adott időpontban megjelenő üzenetek teljes száma az idővonal bal alsó sarkában jelenik meg.

    ![A Azure IoT Hub megtekintése](media/quickstart-send-telemetry-cli/iot-hub-portal-view-metrics.png)

5. Másik lehetőségként a **Metrika legördülő menüben** más metrikákat is megjeleníthet a szimulált eszközön. Például: *A C2d-üzenetek kézbesítése befejeződött* vagy *Az összes eszköz (előzetes verzió)*. 

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása
Ha már nincs szüksége az ebben a rövid útmutatóban létrehozott Azure-erőforrásokra, az Azure CLI használatával törölheti őket.

Ha folytatja a következő ajánlott cikkel, megtarthatja és újból felhasználhatja a már létrehozott erőforrásokat. 

> [!IMPORTANT]
> Az erőforráscsoport törlése nem vonható vissza. Az erőforráscsoport és a benne foglalt erőforrások véglegesen törlődnek. Figyeljen arra, hogy ne töröljön véletlenül erőforráscsoportot vagy erőforrásokat. 

Erőforráscsoport törlése név alapján:
1. Futtassa [az az group delete](/cli/azure/group#az_group_delete) parancsot. Ezzel eltávolítja az erőforráscsoportot, a IoT Hub és a létrehozott eszközregisztrációt.

    ```azurecli
    az group delete --name MyResourceGroup
    ```
1. Futtassa [az az group list parancsot](/cli/azure/group#az_group_list) az erőforráscsoport törlése megerősítéséhez.  

    ```azurecli
    az group list
    ```

## <a name="next-steps"></a>Következő lépések
Ebben a rövid útmutatóban az Azure CLI használatával létrehozott egy IoT Hubot, létrehozott egy szimulált eszközt, telemetriát küldött, telemetriát figyelt, felhőből az eszközre küldött üzenetet küldött, és erőforrásokat takarított meg. Az üzenetkezelési Azure Portal használt az eszköz üzenetkezelési metrikainak megjelenítéséhez.

Ha Ön eszközfejlesztő, a következő javasolt lépés a C-hez készült Azure IoT eszközoldali SDK-t használó telemetriai gyorsútmutató. Opcionálisan tekintse meg az ön által választott nyelven vagy SDK-ban elérhető Azure IoT Hub telemetriai rövid útmutató cikkeit.

> [!div class="nextstepaction"]
> [Rövid útmutató: Telemetria küldése eszközről IoT Hubra (C)](quickstart-send-telemetry-c.md)