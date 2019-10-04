---
title: Azure Logic Apps-műveletek aktiválása IoT Hub-eseményekkel | Microsoft Docs
description: Az Azure Event Grid esemény-útválasztó szolgáltatásával automatikus folyamatokat hozhat létre, amelyek IoT Hub-események alapján hajtanak végre Azure Logic Apps-műveleteket.
services: iot-hub
documentationcenter: ''
author: kgremban
manager: philmea
editor: ''
ms.service: iot-hub
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/07/2018
ms.author: kgremban
ms.openlocfilehash: 9c84e1a62ad8b67e398c62074c390711f4b0be28
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60823771"
---
# <a name="tutorial-send-email-notifications-about-azure-iot-hub-events-using-logic-apps"></a>Oktatóanyag: Azure IoT Hub-eseményekkel kapcsolatos e-mail-értesítések küldése a Logic Apps használatával

Az Azure Event Griddel reagálhat az IoT Hub eseményeire a folyamatok későbbi szakaszában használt üzleti alkalmazásokban kiváltott műveletekkel.

A cikk végigvezeti egy, az IoT Hubot és az Event Gridet használó mintakonfiguráción. Az oktatóanyag végére egy Azure-beli logikai alkalmazással rendelkezik majd, amely e-mail-értesítést küld minden alkalommal, ha egy új eszköz csatlakozik az IoT-központra. 

## <a name="prerequisites"></a>Előfeltételek

* Az Azure Logic Apps által támogatott bármely e-mail-szolgáltató (például Office 365 Outlook, Outlook.com vagy Gmail) által üzemeltetett e-mail-fiók. Ez az e-mail-fiók küldi majd az eseményértesítéseket. A támogatott Logic Apps-összekötők teljes listáját az [összekötők áttekintésében](https://docs.microsoft.com/connectors/) találja.
* Aktív Azure-fiók. Ha még nincs fiókja, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/pricing/free-trial/).
* Egy IoT Hub az Azure-ban. Ha még nem hozott létre központot, [az IoT Hub első lépéseit](../iot-hub/iot-hub-csharp-csharp-getstarted.md) ismertető cikkben talál útmutatást. 

## <a name="create-a-logic-app"></a>Logikai alkalmazás létrehozása

Először hozzon létre egy logikai alkalmazást, és adjon hozzá egy Event Grid-triggert, amely a virtuális gép erőforráscsoportját monitorozza. 

### <a name="create-a-logic-app-resource"></a>Logikai alkalmazás erőforrás létrehozása

1. Az a [az Azure portal](https://portal.azure.com)válassza **erőforrás létrehozása** > **integrációs** > **logikai alkalmazás**.

   ![Logikai alkalmazás létrehozása](./media/publish-iot-hub-events-to-logic-apps/select-logic-app.png)

2. Adjon az előfizetésben egyedi nevet a logikai alkalmazásnak, majd válassza ki ugyanazt az előfizetést, erőforráscsoportot és helyet, ahová az IoT-központ tartozik. 
3. Kattintson a **Létrehozás** gombra.

4. Az erőforrás létrehozása után keresse meg a logikai alkalmazást. 

5. A Logic Apps Designerben megjelennek, gyakran használt minták sablonjait, hogy megkönnyítse az első lépéseket. A Logic App Designerben a **Sablonok** területen válassza az **Üres logikai alkalmazás** elemet, így az alapoktól építheti fel logikai alkalmazását.

### <a name="select-a-trigger"></a>Trigger kiválasztása

A trigger a logikai alkalmazást elindító konkrét esemény. Ebben az oktatóanyagban a munkafolyamatot aktiváló trigger HTTP-kapcsolaton keresztül fogad egy kérést.  

1. Írja be a **HTTP** kifejezést az összekötők és triggerek keresősávjába.
2. Válassza ki a **Kérés – HTTP-kérés fogadásakor** lehetőséget triggerként. 

   ![HTTP-kérés trigger kiválasztása](./media/publish-iot-hub-events-to-logic-apps/http-request-trigger.png)

3. Válassza a **Séma létrehozása hasznosadat-minta használatával** lehetőséget. 

   ![HTTP-kérés trigger kiválasztása](./media/publish-iot-hub-events-to-logic-apps/sample-payload.png)

4. Másolja az alábbi JSON-kódmintát a szövegmezőbe, majd kattintson a **Kész** gombra:

   ```json
   [{
     "id": "56afc886-767b-d359-d59e-0da7877166b2",
     "topic": "/SUBSCRIPTIONS/<subscription ID>/RESOURCEGROUPS/<resource group name>/PROVIDERS/MICROSOFT.DEVICES/IOTHUBS/<hub name>",
     "subject": "devices/LogicAppTestDevice",
     "eventType": "Microsoft.Devices.DeviceCreated",
     "eventTime": "2018-01-02T19:17:44.4383997Z",
     "data": {
       "twin": {
         "deviceId": "LogicAppTestDevice",
         "etag": "AAAAAAAAAAE=",
         "deviceEtag": "null",
         "status": "enabled",
         "statusUpdateTime": "0001-01-01T00:00:00",
         "connectionState": "Disconnected",
         "lastActivityTime": "0001-01-01T00:00:00",
         "cloudToDeviceMessageCount": 0,
         "authenticationType": "sas",
         "x509Thumbprint": {
           "primaryThumbprint": null,
           "secondaryThumbprint": null
         },
         "version": 2,
         "properties": {
           "desired": {
             "$metadata": {
               "$lastUpdated": "2018-01-02T19:17:44.4383997Z"
             },
             "$version": 1
           },
           "reported": {
             "$metadata": {
               "$lastUpdated": "2018-01-02T19:17:44.4383997Z"
             },
             "$version": 1
           }
         }
       },
       "hubName": "egtesthub1",
       "deviceId": "LogicAppTestDevice"
     },
     "dataVersion": "1",
     "metadataVersion": "1"
   }]
   ```

5. Egy előugró értesítés jelenhet meg a következő üzenettel: **Ne felejtsen el egy alkalmazás/JSON értékű Content-Type fejlécet beszúrni a kérésbe**. A javaslatot nyugodtan figyelmen kívül hagyhatja, és továbbléphet a következő szakaszra. 

### <a name="create-an-action"></a>Művelet létrehozása

A műveletek azok a lépések, azután mennek végbe, hogy a trigger elindítja a logikai alkalmazás munkafolyamatát. Ebben az oktatóanyagban a művelet egy e-mail-értesítés küldése az e-mail-szolgáltatóról. 

1. Válassza az **Új lépés** lehetőséget. Ez egy új ablakot nyit meg a **művelet kiválasztásához**.

2. Keresse meg az **E-mail** elemet.

3. Az e-mail-szolgáltató alapján keresse meg és válassza ki a megfelelő összekötőt. Ebben az oktatóanyagban az **Office 365 Outlookot használjuk**. Az egyéb e-mail-szolgáltatókra vonatkozó lépések is hasonlók. 

   ![E-mail-szolgáltató összekötőjének kiválasztása](./media/publish-iot-hub-events-to-logic-apps/o365-outlook.png)

4. Válassza az **E-mail küldése** műveletet. 

5. Ha a rendszer kéri, jelentkezzen be az e-mail-fiókjába. 

6. Hozza létre az e-mail-sablont. 
   * **A**: Adja meg az értesítési e-maileket kap e-mail-címe. Ehhez az oktatóanyaghoz használjon egy olyan e-mail-fiókot, amelyet a tesztelés során el tud majd érni. 
   * **Tulajdonos** és **törzs**: Írja be a szöveget az e-mailek. A választóeszközben a megfelelő JSON-tulajdonságok kiválasztásával az eseményadatokon alapuló dinamikus tartalmakat szúrhat be.  

   Az e-mail-sablon a következő példához hasonló lehet:

   ![E-mail-adatok kitöltése](./media/publish-iot-hub-events-to-logic-apps/email-content.png)

7. Mentse a logikai alkalmazást. 

### <a name="copy-the-http-url"></a>A HTTP URL-cím másolása

Mielőtt kilép a Logic Apps Designerből, másolja ki az URL-címet, amelyen a logikai alkalmazás figyeli a triggereket. Erre az URL-címre az Event Grid konfigurálásához lesz szükség. 

1. Kattintással bontsa ki a **HTTP-kérés fogadásakor** triggerkonfigurációs mezőt. 
2. Másolja ki a **HTTP POST URL** értékét a mellette lévő másolás gombra kattintva. 

   ![A HTTP POST URL másolása](./media/publish-iot-hub-events-to-logic-apps/copy-url.png)

3. Mentse ezt az URL-címet, hogy a következő szakaszban hivatkozhasson majd rá. 

## <a name="configure-subscription-for-iot-hub-events"></a>Az IoT Hub eseményeire való előfizetés beállítása

Ebben a szakaszban konfiguráljuk az IoT-központot, hogy közzétegye a bekövetkező eseményeket. 

1. Az Azure Portalon keresse meg az IoT-központot. 
2. Válassza az **Események** lehetőséget.

   ![Az Event Grid-adatok megnyitása](./media/publish-iot-hub-events-to-logic-apps/event-grid.png)

3. Válassza az **Esemény-előfizetés** lehetőséget. 

   ![Új esemény-előfizetés létrehozása](./media/publish-iot-hub-events-to-logic-apps/event-subscription.png)

4. Hozza létre az esemény-előfizetést a következő értékekkel: 
   * **Esemény típusa**: Törölje a jelet az előfizetés az összes eseménytípusra, és válassza ki **létre az eszköz** a menüből.
   * **Végpont részletei**: Válassza ki a végpont típusa szerint **Webhook** és kattintson a válassza végpont, és illessze be az URL-cím a logikai alkalmazás kimásolt és kijelölés megerősítéséhez.

     ![végpont url-jének kiválasztása](./media/publish-iot-hub-events-to-logic-apps/endpoint-url.png)

   * **Esemény-előfizetés részleteit**: Adjon meg egy leíró nevet, és válassza ki **Event Grid-séma**

   Amikor elkészült, az űrlapnak az alábbi példához hasonlóan kell kinéznie: 

    ![Esemény-előfizetési űrlapminta](./media/publish-iot-hub-events-to-logic-apps/subscription-form.png)

5. Ha most mentené az esemény-előfizetést, akkor az IoT-központban létrehozott minden eszközről értesítést kapna. A jelen oktatóanyag esetében, pedig a kötelező mezők szűréséhez használja az egyes eszközöktől. Válassza ki **további funkciók** a képernyő tetején. 

6. Hozza létre a következő szűrőket:

   * **Tulajdonos kezdődik**: Adja meg `devices/Building1_` . épület 1 eszköz események szűréséhez.
   * **Tulajdonos végződik**: Adja meg `_Temperature` hőmérséklet kapcsolódó eszköz-események szűréséhez.

5. Kattintson a **Létrehozás** gombra az esemény-előfizetés mentéséhez.

## <a name="create-a-new-device"></a>Új eszköz létrehozása

A logikai alkalmazás teszteléséhez hozzunk létre egy új eszközt, amely kivált egy eseményértesítő e-mailt. 

1. Az IoT-központban válassza az **IoT-eszközök** lehetőséget. 
2. Válassza a **Hozzáadás** lehetőséget.
3. Az **Eszközazonosító** mezőben adja meg a `Building1_Floor1_Room1_Temperature` azonosítót.
4. Kattintson a **Mentés** gombra. 
5. Több eszközt is hozzáadhat különböző eszközazonosítókkal az esemény-előfizetési szűrők teszteléséhez. Próbálja ki a következő példákat: 
   * Building1_Floor1_Room1_Light
   * Building1_Floor2_Room2_Temperature
   * Building2_Floor1_Room1_Temperature
   * Building2_Floor1_Room1_Light

Miután hozzáadott néhány eszközt az IoT-központhoz, az e-mail-fiókjában ellenőrizheti, hogy melyik eszközök aktiválták a logikai alkalmazást. 

## <a name="use-the-azure-cli"></a>Az Azure parancssori felületének használata

Az Azure Portal helyett az Azure CLI használatával is végrehajthatja az IoT Hub lépéseit. További részletekért lásd az Azure CLI [esemény-előfizetések létrehozásáról](https://docs.microsoft.com/cli/azure/eventgrid/event-subscription) és [IoT-eszközök létrehozásáról](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity) szóló lapjait.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ebben az oktatóanyagban olyan erőforrásokat használtunk, amelyek költségekkel terhelik az Azure-előfizetését. Miután végzett az oktatóanyag kipróbálásával és az eredmények tesztelésével, mindenképp tiltsa le vagy törölje azokat az erőforrásokat, amelyeket nem kíván megtartani. 

Ha nem szeretné elveszteni a logikai alkalmazásba fektetett munkáját, a törlés helyett csak tiltsa le. 

1. Keresse meg a logikai alkalmazást.
2. Az **Áttekintés** panelen válassza a **Törlés** vagy a **Letiltás** elemet. 

Mindegyik előfizetés egy ingyenes IoT-központtal rendelkezhet. Ha ingyenes központot hozott létre az oktatóanyaghoz, azt nem kell törölnie a költségek megelőzéséhez.

1. Keresse meg az IoT-központot. 
2. Az **Áttekintés** panelen válassza a **Törlés** elemet. 

Az esemény-előfizetést akkor is érdemes lehet törölnie, ha megtartja az IoT-központot. 

1. Az IoT-központban válassza az **Event Grid** lehetőséget.
2. Válassza ki a törölni kívánt esemény-előfizetést. 
3. Válassza a **Törlés** elemet. 

## <a name="next-steps"></a>További lépések

* További információ [az IoT Hub-eseményekre való válaszadást az Event Griddel kiváltott műveletek használatával](../iot-hub/iot-hub-event-grid.md).
* [Ismerje meg, hogyan rendezheti az eszközhöz csatlakoztatott és nem csatlakoztatott eseményeket](../iot-hub/iot-hub-how-to-order-connection-state-events.md)
* Ismerje meg részletesebben az [Event Grid](overview.md) által kínált lehetőségeket.


