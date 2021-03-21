---
title: Oktatóanyag – IoT Hub-események használata az aktiváláshoz Azure Logic Apps
description: Ez az oktatóanyag bemutatja, hogyan használhatja a Azure Event Grid esemény-útválasztási szolgáltatását, és hogyan hozhat létre automatizált folyamatokat IoT Hub események alapján végzett Azure Logic Apps műveletek végrehajtásához.
services: iot-hub, event-grid
author: philmea
ms.service: iot-hub
ms.topic: tutorial
ms.date: 09/14/2020
ms.author: philmea
ms.custom: devx-track-azurecli
ms.openlocfilehash: 3772988e9bf4f733323adae6c0527c8b811c4d04
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98624456"
---
# <a name="tutorial-send-email-notifications-about-azure-iot-hub-events-using-event-grid-and-logic-apps"></a>Oktatóanyag: Azure IoT Hub-eseményekkel kapcsolatos e-mail-értesítések küldése az Event Grid és a Logic Apps használatával

Az Azure Event Griddel reagálhat az IoT Hub eseményeire a folyamatok későbbi szakaszában használt üzleti alkalmazásokban kiváltott műveletekkel.

Ez a cikk egy IoT Hub és Event Grid használó minta-konfigurációt mutat be. A végén egy Azure Logic apps van beállítva, amely értesítő e-mailt küld minden alkalommal, amikor egy eszköz csatlakozik vagy leválasztja az IoT hubot. A Event Grid segítségével időben értesítést kaphat a kritikus fontosságú eszközök leválasztásáról. A metrikák és a diagnosztika több (azaz 20 vagy akár több) is eltarthat, bár a naplók/riasztások között percek alatt megjelenhetnek. Előfordulhat, hogy a kritikus infrastruktúra nem fogadható el.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Előfeltételek

* A Azure Logic Apps által támogatott e-mail-szolgáltatótól származó e-mail-fiók, például Office 365 Outlook vagy Outlook.com. Ez az e-mail-fiók küldi majd az eseményértesítéseket.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

## <a name="create-an-iot-hub"></a>IoT Hub létrehozása

Az Azure Cloud Shell terminál használatával gyorsan létrehozhat egy új IoT hubot a portálon.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com). 

1. Az oldal jobb felső sarkában kattintson a Cloud Shell gombra.

   ![Cloud Shell gomb](./media/publish-iot-hub-events-to-logic-apps/portal-cloud-shell.png)

1. Futtassa a következő parancsot új erőforráscsoport létrehozásához:

   ```azurecli
   az group create --name {your resource group name} --location westus
   ```
    
1. Futtassa a következő parancsot egy IoT hub létrehozásához:

   ```azurecli
   az iot hub create --name {your iot hub name} --resource-group {your resource group name} --sku S1 
   ```

1. Csökkentse a Cloud Shell terminált. Az oktatóanyag későbbi részében vissza fog térni a rendszerhéjhoz.

## <a name="create-a-logic-app"></a>Logikai alkalmazás létrehozása

Ezután hozzon létre egy logikai alkalmazást, és adjon hozzá egy HTTP Event Grid-eseményindítót, amely az IoT hub kéréseit dolgozza fel. 

### <a name="create-a-logic-app-resource"></a>Logikai alkalmazás erőforrás létrehozása

1. A [Azure Portal](https://portal.azure.com)válassza az **erőforrás létrehozása** lehetőséget, majd írja be a "Logic app" kifejezést a keresőmezőbe, és válassza a Return (visszatérés) lehetőséget. Válassza ki a **logikai alkalmazást** az eredmények közül.

   ![Logikai alkalmazás létrehozása](./media/publish-iot-hub-events-to-logic-apps/select-logic-app.png)

1. A következő képernyőn válassza a **Létrehozás** elemet. 

1. Adjon az előfizetésben egyedi nevet a logikai alkalmazásnak, majd válassza ki ugyanazt az előfizetést, erőforráscsoportot és helyet, ahová az IoT-központ tartozik. 

   ![A logikai alkalmazás létrehozására szolgáló mezők](./media/publish-iot-hub-events-to-logic-apps/create-logic-app-fields.png)

1. Válassza az **Áttekintés + létrehozás** lehetőséget.

1. Ellenőrizze a beállításokat, majd válassza a **Létrehozás** lehetőséget.

1. Az erőforrás létrehozása után válassza az **Ugrás erőforráshoz** lehetőséget. 

1. A Logic Apps Designer lapon lefelé a **sablonok** megtekintéséhez. Válassza az **üres logikai alkalmazás** lehetőséget, hogy teljesen új logikai alkalmazást lehessen létrehozni.

### <a name="select-a-trigger"></a>Eseményindító kiválasztása

A trigger a logikai alkalmazást elindító konkrét esemény. Ebben az oktatóanyagban a munkafolyamatot aktiváló trigger HTTP-kapcsolaton keresztül fogad egy kérést.  

1. Írja be a **HTTP** kifejezést az összekötők és triggerek keresősávjába.

1. Görgessen végig az eredmények között, és válassza a **kérelem – ha egy HTTP-kérést kap** triggerként. 

   ![HTTP-kérés trigger kiválasztása](./media/publish-iot-hub-events-to-logic-apps/http-request-trigger.png)

1. Válassza a **Séma létrehozása hasznosadat-minta használatával** lehetőséget. 

   ![Minta hasznos adatok használata](./media/publish-iot-hub-events-to-logic-apps/sample-payload.png)

1. Illessze be az *eszközhöz csatlakoztatott Event Schema* JSON-t a szövegmezőbe, majd válassza a **kész** lehetőséget:

   ```json
     [{  
      "id": "f6bbf8f4-d365-520d-a878-17bf7238abd8",
      "topic": "/SUBSCRIPTIONS/<subscription ID>/RESOURCEGROUPS/<resource group name>/PROVIDERS/MICROSOFT.DEVICES/IOTHUBS/<hub name>",
      "subject": "devices/LogicAppTestDevice",
      "eventType": "Microsoft.Devices.DeviceConnected",
      "eventTime": "2018-06-02T19:17:44.4383997Z",
      "data": {
          "deviceConnectionStateEventInfo": {
            "sequenceNumber":
              "000000000000000001D4132452F67CE200000002000000000000000000000001"
          },
        "hubName": "egtesthub1",
        "deviceId": "LogicAppTestDevice",
        "moduleId" : "DeviceModuleID"
      }, 
      "dataVersion": "1",
      "metadataVersion": "1"
    }]
   ```

   Ez az esemény akkor tesz közzé, amikor egy eszköz IoT-hubhoz csatlakozik.

> [!NOTE]
> Egy előugró értesítés jelenhet meg a következő üzenettel: **Ne felejtsen el egy alkalmazás/JSON értékű Content-Type fejlécet beszúrni a kérésbe**. A javaslatot nyugodtan figyelmen kívül hagyhatja, és továbbléphet a következő szakaszra. 

### <a name="create-an-action"></a>Művelet létrehozása

A műveletek azok a lépések, azután mennek végbe, hogy a trigger elindítja a logikai alkalmazás munkafolyamatát. Ebben az oktatóanyagban a művelet egy e-mail-értesítés küldése az e-mail-szolgáltatóról. 

1. Válassza az **Új lépés** lehetőséget. Ekkor megnyílik egy ablak, amely **kiválasztja a műveletet**.

1. Keresse meg az **Outlookot**.

1. Az e-mail-szolgáltató alapján keresse meg és válassza ki a megfelelő összekötőt. Ez az oktatóanyag az **Outlook.com**-t használja. Az egyéb e-mail-szolgáltatókra vonatkozó lépések is hasonlók. 

   ![E-mail-szolgáltató összekötőjének kiválasztása](./media/publish-iot-hub-events-to-logic-apps/outlook-step.png)

1. Válassza az **E-mail küldése (v2)** műveletet. 

1. Válassza a **Bejelentkezés** lehetőséget, és jelentkezzen be az e-mail-fiókjába. Válassza az **Igen** lehetőséget, ha szeretné, hogy az alkalmazás hozzáférjen az adataihoz.

1. Készítse el az e-mail-sablont. 

   * **Címzett**: Adja meg az értesítési e-maileket fogadó e-mail-címet. Ehhez az oktatóanyaghoz olyan e-mail-fiókot használjon, amelyhez hozzáfér majd a tesztelés során. 

   * **Tárgy**: Írja be a tárgy szövegét. Ha a tárgy szövegmezőre kattint, kiválaszthatja a felvenni kívánt dinamikus tartalmat is. Ez az oktatóanyag például a következőt használja: `IoT Hub alert: {eventType}` . Ha nem látja a dinamikus tartalmat, válassza a **dinamikus tartalom hozzáadása** hiperhivatkozást. Ez a beállítás be-és kikapcsolható.

   * **Törzs**: Írja meg az e-mail szövegét. A választó eszközről választott JSON-tulajdonságokkal az esemény adataira alapuló dinamikus tartalmat illeszthet be. Ha nem látja a dinamikus tartalmat, válassza a **dinamikus tartalom hozzáadása** hiperhivatkozást a **szövegtörzs** szövegmezőben. Ha nem jeleníti meg a kívánt mezőket, a dinamikus tartalom képernyőn kattintson a *továbbiak* lehetőségre, hogy az előző műveletből származó mezőket is tartalmazza.

   Az e-mail-sablon a következő példához hasonló lehet:

   ![E-mail-adatok kitöltése](./media/publish-iot-hub-events-to-logic-apps/email-content.png)

1. A Logic Apps Designerben válassza a **Mentés** elemet.  

### <a name="copy-the-http-url"></a>A HTTP URL-cím másolása

Mielőtt kilép a Logic Apps Designerből, másolja ki az URL-címet, amelyen a logikai alkalmazás figyeli a triggereket. Erre az URL-címre az Event Grid konfigurálásához lesz szükség. 

1. Kattintással bontsa ki a **HTTP-kérés fogadásakor** triggerkonfigurációs mezőt. 

1. Másolja ki a **HTTP POST URL** értékét a mellette lévő másolás gombra kattintva. 

   ![A HTTP POST URL másolása](./media/publish-iot-hub-events-to-logic-apps/copy-url.png)

1. Mentse ezt az URL-címet, hogy a következő szakaszban hivatkozhasson majd rá. 

## <a name="configure-subscription-for-iot-hub-events"></a>Az IoT Hub eseményeire való előfizetés beállítása

Ebben a szakaszban konfiguráljuk az IoT-központot, hogy közzétegye a bekövetkező eseményeket. 

1. Az Azure Portalon keresse meg az IoT-központot. Ehhez válassza az **erőforráscsoportok** lehetőséget, majd jelölje ki az oktatóanyaghoz tartozó erőforráscsoportot, majd válassza ki az IoT hubot az erőforrások listájából.

1. Válassza az **Események** elemet.

   ![Az Event Grid-adatok megnyitása](./media/publish-iot-hub-events-to-logic-apps/event-grid.png)

1. Válassza az **Eseményfeliratkozás** elemet. 

   ![Új esemény-előfizetés létrehozása](./media/publish-iot-hub-events-to-logic-apps/event-subscription.png)

1. Hozza létre az esemény-előfizetést a következő értékekkel: 

   1. Az **ESEMÉNYFELIRATKOZÁS RÉSZLETEI** szakaszban:
      1. Adja meg az esemény-előfizetés **nevét** . 
      2. Válassza ki **Event Grid sémát** az **eseményvezérelt sémához**. 
   2. A **TÉMAKÖR RÉSZLETEI** szakaszban:
      1. Erősítse meg, hogy a **Témakör típusa** az **IoT Hub** értékre van állítva. 
      2. Erősítse meg, hogy a **Forrás-erőforrás** mező értékeként az IoT-központ neve van megadva. 
      3. Adja meg annak a **rendszertémakörnek** a nevét, amelyet létre fog hozni Önnek. A rendszertémakörökről a [rendszertémakörök áttekintésében](system-topics.md)talál további információt.
   3. Az **ESEMÉNYTÍPUSOK** szakaszban:
      1. Válassza a **Szűrés eseménytípusra** legördülő listát.
      1. Törölje a **létrehozott eszközt** és az **eszköz törölte** jelölőnégyzeteket, és csak a **csatlakoztatott eszközt** és az **eszköz leválasztott** jelölőnégyzeteit hagyja ki.

         ![feliratkozási eseménytípusok választása](./media/publish-iot-hub-events-to-logic-apps/subscription-event-types.png)
   
   4. A **VÉGPONT ADATAI** szakaszban: 
       1. Válassza ki a **végpont típusát** **webhookként**.
       2. Kattintson a **végpont kiválasztása** elemre, illessze be a logikai alkalmazásból kimásolt URL-címet, és erősítse meg a választását.

         ![végpont url-jének kiválasztása](./media/publish-iot-hub-events-to-logic-apps/endpoint-webhook.png)

         Ha végzett, a panelnek az alábbi példához kell hasonlítania: 

         ![Esemény-előfizetési űrlapminta](./media/publish-iot-hub-events-to-logic-apps/subscription-form.png)

1.  Válassza a **Létrehozás** lehetőséget.

## <a name="simulate-a-new-device-connecting-and-sending-telemetry"></a>Telemetria-csatlakozást és-küldést szolgáló új eszköz szimulálása

Tesztelje a logikai alkalmazást úgy, hogy az Azure CLI használatával gyorsan szimulálja az eszköz csatlakoztatását. 

1. Kattintson a Cloud Shell gombra a terminál újbóli megnyitásához.

1. Futtassa a következő parancsot egy szimulált eszköz identitásának létrehozásához:
    
     ```azurecli 
    az iot hub device-identity create --device-id simDevice --hub-name {YourIoTHubName}
    ```

1. A következő parancs futtatásával szimulálhatja az eszköz csatlakoztatását IoT Hub és telemetria küldéséhez:

    ```azurecli
    az iot device simulate -d simDevice -n {YourIoTHubName}
    ```

1. Amikor a szimulált eszköz csatlakozik a IoT Hubhoz, egy e-mailt fog kapni, amely értesíti Önt egy "DeviceConnected" eseményről.

1. A szimuláció befejezése után egy e-mailt kap, amely értesíti Önt egy "DeviceDisconnected" eseményről. 

    ![Riasztási e-mail – példa](./media/publish-iot-hub-events-to-logic-apps/alert-mail.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ebben az oktatóanyagban olyan erőforrásokat használtunk, amelyek költségekkel terhelik az Azure-előfizetését. Ha befejezte az oktatóanyag kipróbálását és az eredmények tesztelését, tiltsa le vagy törölje azokat az erőforrásokat, amelyeket nem szeretne megőrizni. 

Az oktatóanyagban létrehozott összes erőforrás törléséhez törölje az erőforráscsoportot. 

1. Válassza ki az **erőforráscsoportok** elemet, majd válassza ki az oktatóanyaghoz létrehozott erőforráscsoportot.

2. Az erőforráscsoport ablaktáblán válassza az **erőforráscsoport törlése** elemet. A rendszer felszólítja az erőforráscsoport nevének megadására, majd törölheti azt. A benne foglalt összes erőforrást is eltávolítja.

## <a name="next-steps"></a>Következő lépések

* További információ [az IoT Hub-eseményekre való válaszadást az Event Griddel kiváltott műveletek használatával](../iot-hub/iot-hub-event-grid.md).
* [Ismerje meg, hogyan rendezheti az eszközhöz csatlakoztatott és nem csatlakoztatott eseményeket](../iot-hub/iot-hub-how-to-order-connection-state-events.md)
* Ismerje meg részletesebben az [Event Grid](overview.md) által kínált lehetőségeket.

A támogatott Logic apps-összekötők teljes listáját lásd: 

> [!div class="nextstepaction"]
> Az [Összekötők áttekintése](/connectors/).