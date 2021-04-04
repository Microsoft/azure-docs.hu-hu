---
title: Az eszköz csatlakoztatási eseményeinek megrendelése az Azure IoT Hub w/Azure Cosmos DB
description: Ez a cikk azt ismerteti, hogyan lehet az Azure-IoT Hub eszköz-kapcsolódási eseményeket megrendelni és rögzíteni Azure Cosmos DB használatával a legújabb kapcsolódási állapot fenntartása érdekében
services: iot-hub
ms.service: iot-hub
author: ash2017
ms.topic: conceptual
ms.date: 04/11/2019
ms.author: asrastog
ms.custom: devx-track-azurecli
ms.openlocfilehash: 90b7b6aebfce1c37bef76d371d829048d755e39e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "92147265"
---
# <a name="order-device-connection-events-from-azure-iot-hub-using-azure-cosmos-db"></a>Eszközkapcsolati események megrendelése az Azure IoT Hubról az Azure Cosmos DB használatával

Azure Event Grid segít az eseményvezérelt alkalmazások létrehozásában és a IoT-események egyszerű integrálásában üzleti megoldásaiban. Ez a cikk végigvezeti egy olyan telepítőn, amellyel nyomon követheti és tárolhatja a legújabb eszköz-kapcsolódási állapotot Cosmos DBban. A csatlakoztatott eszközön elérhető sorszámot és az eszköz leválasztott eseményeit fogjuk használni, és a legújabb állapotot Cosmos DB tároljuk. Egy tárolt eljárást fogunk használni, amely egy Cosmos DB gyűjteményében végrehajtott alkalmazási logika.

A sorozatszám egy hexadecimális szám karakterláncos ábrázolása. A nagyobb szám azonosításához karakterlánc-összehasonlítást használhat. Ha a sztringet hexadecimális értékre konvertálja, akkor a szám 256 bites szám lesz. A sorozatszám szigorúan növekszik, és a legutóbbi esemény nagyobb számú lesz, mint a többi esemény. Ez akkor hasznos, ha gyakran csatlakoztatja az eszközt, és leválasztja a kapcsolatot, és szeretné biztosítani, hogy csak a legújabb esemény legyen használatban egy alsóbb rétegbeli művelet elindításához, mivel Azure Event Grid nem támogatja az események rendezését.

## <a name="prerequisites"></a>Előfeltételek

* Aktív Azure-fiók. Ha még nem rendelkezik ilyennel, [létrehozhat egy ingyenes fiókot](https://azure.microsoft.com/pricing/free-trial/).

* Aktív Azure Cosmos DB SQL API-fiók. Ha még nem hozott létre egyet, tekintse meg az [adatbázis-fiók létrehozása](../cosmos-db/create-sql-api-java.md#create-a-database-account) bemutatóhoz című témakört.

* Egy gyűjtemény az adatbázisban. Lásd: [gyűjtemény hozzáadása](../cosmos-db/create-sql-api-java.md#add-a-container) egy útmutatóhoz. A gyűjtemény létrehozásakor használja `/id` a () partíciót a partíciós kulcshoz.

* Egy IoT Hub az Azure-ban. Ha még nem hozott létre központot, [az IoT Hub első lépéseit](./quickstart-send-telemetry-dotnet.md) ismertető cikkben talál útmutatást.

## <a name="create-a-stored-procedure"></a>Tárolt eljárás létrehozása

Először hozzon létre egy tárolt eljárást, és állítsa be úgy, hogy olyan logikát futtasson, amely összehasonlítja a bejövő események sorszámait, és rögzíti a legújabb eseményt egy eszközön az adatbázisban.

1. A Cosmos db SQL API-ban válassza a **adatkezelő**  >  **elemek**  >  **új tárolt eljárás** elemet.

   ![Tárolt eljárás létrehozása](./media/iot-hub-how-to-order-connection-state-events/create-stored-procedure.png)

2. Adja meg a tárolt eljárás AZONOSÍTÓjának **LatestDeviceConnectionState** , és illessze be a következőt a **tárolt eljárás törzsében**. Vegye figyelembe, hogy ez a kód a tárolt eljárás törzsében lévő összes meglévő kódot lecseréli. Ez a kód az eszköz AZONOSÍTÓinak egy sorát kezeli, és a legmagasabb sorszám azonosításával rögzíti az eszköz AZONOSÍTÓjának legújabb kapcsolatok állapotát.

    ```javascript
    // SAMPLE STORED PROCEDURE
    function UpdateDevice(deviceId, moduleId, hubName, connectionState, connectionStateUpdatedTime, sequenceNumber) {
      var collection = getContext().getCollection();
      var response = {};

      var docLink = getDocumentLink(deviceId, moduleId);

      var isAccepted = collection.readDocument(docLink, function(err, doc) {
        if (err) {
          console.log('Cannot find device ' + docLink + ' - ');
          createDocument();
        } else {
          console.log('Document Found - ');
          replaceDocument(doc);
        }
      });

      function replaceDocument(document) {
        console.log(
          'Old Seq :' +
            document.sequenceNumber +
            ' New Seq: ' +
            sequenceNumber +
            ' - '
        );
        if (sequenceNumber > document.sequenceNumber) {
          document.connectionState = connectionState;
          document.connectionStateUpdatedTime = connectionStateUpdatedTime;
          document.sequenceNumber = sequenceNumber;

          console.log('replace doc - ');

          isAccepted = collection.replaceDocument(docLink, document, function(
            err,
            updated
          ) {
            if (err) {
              getContext()
                .getResponse()
                .setBody(err);
            } else {
              getContext()
                .getResponse()
                .setBody(updated);
            }
          });
        } else {
          getContext()
            .getResponse()
            .setBody('Old Event - current: ' + document.sequenceNumber + ' Incoming: ' + sequenceNumber);
        }
      }
      function createDocument() {
        document = {
          id: deviceId + '-' + moduleId,
          deviceId: deviceId,
          moduleId: moduleId,
          hubName: hubName,
          connectionState: connectionState,
          connectionStateUpdatedTime: connectionStateUpdatedTime,
          sequenceNumber: sequenceNumber
        };
        console.log('Add new device - ' + collection.getAltLink());
        isAccepted = collection.createDocument(
          collection.getAltLink(),
          document,
          function(err, doc) {
            if (err) {
              getContext()
                .getResponse()
                .setBody(err);
            } else {
              getContext()
                .getResponse()
                .setBody(doc);
            }
          }
        );
      }

      function getDocumentLink(deviceId, moduleId) {
        return collection.getAltLink() + '/docs/' + deviceId + '-' + moduleId;
      }
    }
    ```

3. Mentse a tárolt eljárást:

    ![tárolt eljárás mentése](./media/iot-hub-how-to-order-connection-state-events/save-stored-procedure.png)

## <a name="create-a-logic-app"></a>Logikai alkalmazás létrehozása

Először hozzon létre egy logikai alkalmazást, és adjon hozzá egy Event Grid-triggert, amely a virtuális gép erőforráscsoportját monitorozza.

### <a name="create-a-logic-app-resource"></a>Logikai alkalmazás erőforrás létrehozása

1. A [Azure Portal](https://portal.azure.com)válassza az **+ erőforrás létrehozása** lehetőséget, válassza az **integráció** , majd a **Logic app** elemet.

   ![Logikai alkalmazás létrehozása](./media/iot-hub-how-to-order-connection-state-events/select-logic-app.png)

2. Adjon az előfizetésben egyedi nevet a logikai alkalmazásnak, majd válassza ki ugyanazt az előfizetést, erőforráscsoportot és helyet, ahová az IoT-központ tartozik.

   ![Új logikai alkalmazás](./media/iot-hub-how-to-order-connection-state-events/new-logic-app.png)

3. Válassza a **Létrehozás** lehetőséget a logikai alkalmazás létrehozásához.

   Ezzel létrehozott egy Azure-erőforrást a logikai alkalmazás számára. Miután az Azure üzembe helyezte a logikai alkalmazást, a Logic Apps Designer gyakran használt minták sablonjait jeleníti meg, hogy megkönnyítse az első lépéseket.

   > [!NOTE]
   > A logikai alkalmazás újbóli megkereséséhez és megnyitásához válassza az **erőforráscsoportok** lehetőséget, majd válassza ki azt az erőforráscsoportot, amelyet ehhez a útmutatóhoz használ. Ezután válassza ki az új logikai alkalmazást. Ekkor megnyílik a Logic app Designer.

4. A Logic app Designerben görgessen jobbra, amíg meg nem jelenik a gyakori eseményindítók. A **sablonok** területen válassza az **üres logikai alkalmazás** lehetőséget, hogy teljesen felépítse a logikai alkalmazást.

### <a name="select-a-trigger"></a>Eseményindító kiválasztása

A trigger a logikai alkalmazást elindító konkrét esemény. Ebben az oktatóanyagban a munkafolyamatot aktiváló trigger HTTP-kapcsolaton keresztül fogad egy kérést.

1. Az összekötők és eseményindítók keresési sávján írja be a **http** kifejezést, és nyomja le az ENTER billentyűt.

2. Válassza ki a **Kérés – HTTP-kérés fogadásakor** lehetőséget triggerként.

   ![HTTP-kérés trigger kiválasztása](./media/iot-hub-how-to-order-connection-state-events/http-request-trigger.png)

3. Válassza a **Séma létrehozása hasznosadat-minta használatával** lehetőséget.

   ![Minta hasznos adatok használata séma létrehozásához](./media/iot-hub-how-to-order-connection-state-events/sample-payload.png)

4. Másolja az alábbi JSON-kódmintát a szövegmezőbe, majd kattintson a **Kész** gombra:

   ```json
   [{
    "id": "fbfd8ee1-cf78-74c6-dbcf-e1c58638ccbd",
    "topic":
      "/SUBSCRIPTIONS/DEMO5CDD-8DAB-4CF4-9B2F-C22E8A755472/RESOURCEGROUPS/EGTESTRG/PROVIDERS/MICROSOFT.DEVICES/IOTHUBS/MYIOTHUB",
    "subject": "devices/Demo-Device-1",
    "eventType": "Microsoft.Devices.DeviceConnected",
    "eventTime": "2018-07-03T23:20:11.6921933+00:00",
    "data": {
      "deviceConnectionStateEventInfo": {
        "sequenceNumber":
          "000000000000000001D4132452F67CE200000002000000000000000000000001"
      },
      "hubName": "MYIOTHUB",
      "deviceId": "48e44e11-1437-4907-83b1-4a8d7e89859e",
      "moduleId": ""
    },
    "dataVersion": "1",
    "metadataVersion": "1"
   }]
   ```

   ![Minta JSON-adattartalom beillesztése](./media/iot-hub-how-to-order-connection-state-events/paste-sample-payload.png)

5. Egy előugró értesítés jelenhet meg a következő üzenettel: **Ne felejtsen el egy alkalmazás/JSON értékű Content-Type fejlécet beszúrni a kérésbe**. A javaslatot nyugodtan figyelmen kívül hagyhatja, és továbbléphet a következő szakaszra.

### <a name="create-a-condition"></a>Feltétel létrehozása

A logikai alkalmazás munkafolyamatában a feltételek segítséget nyújtanak bizonyos műveletek futtatásához az adott feltétel átadása után. Ha a feltétel teljesült, meg lehet határozni a kívánt műveletet. Ebben az oktatóanyagban az a feltétel, hogy meggyőződjön arról, hogy az eszköz csatlakoztatva van-e a eventType vagy az eszköz le van-e választva. A művelet a tárolt eljárás végrehajtása lesz az adatbázisban.

1. Válassza az **+ új lépés** , majd **a beépített** elemet, majd keresse meg és válassza ki a **feltételt**. Kattintson a **válasszon egy értéket** , és megjelenik egy mező, amely megjeleníti a dinamikus tartalmat – a kiválasztható mezőket. Töltse ki a mezőket az alább látható módon, hogy csak a csatlakoztatott eszköz és az eszköz leválasztott eseményeihez hajtsa végre ezt:

   * Válasszon egy értéket: **EventType** – válassza ki ezt a dinamikus tartalom mezőiben, amelyek akkor jelennek meg, amikor erre a mezőre kattint.
   * A (z) "egyenlő" értékkel **végződik**.
   * Válasszon egy értéket: **csatlakozik**.

     ![Kitöltési feltétel](./media/iot-hub-how-to-order-connection-state-events/condition-detail.png)

2. A **Ha igaz** párbeszédpanelen kattintson a **művelet hozzáadása** lehetőségre.
  
   ![Művelet hozzáadása, ha igaz](./media/iot-hub-how-to-order-connection-state-events/action-if-true.png)

3. Keresse meg Cosmos DB és válassza a **Azure Cosmos db – tárolt eljárás végrehajtása**

   ![CosmosDB keresése](./media/iot-hub-how-to-order-connection-state-events/cosmosDB-search.png)

4. Töltse ki a **cosmosdb-kapcsolatok** **nevet** , és válassza ki a bejegyzést a táblában, majd válassza a **Létrehozás** lehetőséget. Megjelenik a **tárolt eljárás végrehajtása** panel. Adja meg a mezők értékeit:

   **Adatbázis-azonosító**: ToDoList

   **Gyűjtemény azonosítója**: elemek

   **Sproc-azonosító**: LatestDeviceConnectionState

5. Válassza az **új paraméter hozzáadása** lehetőséget. A megjelenő legördülő menüben jelölje be a **partíciós kulcs** és paraméterek melletti jelölőnégyzetet **a tárolt eljáráshoz**, majd kattintson a képernyő bárhol máshol elemére. hozzáadja a partíciós kulcs értékét és a tárolt eljárás paramétereinek mezőjét.

   ![A képernyőképen egy végrehajtott tárolt eljárás elem jelenik meg, amelyhez új paraméter van kiválasztva.](./media/iot-hub-how-to-order-connection-state-events/logicapp-stored-procedure.png)

6. Ezután adja meg a partíciós kulcs értékét és paramétereit az alább látható módon. Ügyeljen arra, hogy a zárójeleket és a dupla idézőjeleket az ábrán látható módon helyezze el. Előfordulhat, hogy a **dinamikus tartalom hozzáadása** lehetőségre kell kattintania az itt használható érvényes értékek lekéréséhez.

   ![A képernyőképen egy végrehajtott tárolt eljárási tétel látható paraméterekkel megadva.](./media/iot-hub-how-to-order-connection-state-events/logicapp-stored-procedure-2.png)

7. Győződjön meg **arról, hogy** a panel felső részén a **Kimenet kiválasztása az előző lépésekben** lehetőségre, majd a  **törzs** lehetőség van kiválasztva.

   ![logikai alkalmazás feltöltése – mindegyik](./media/iot-hub-how-to-order-connection-state-events/logicapp-foreach-body.png)

8. Mentse a logikai alkalmazást.

### <a name="copy-the-http-url"></a>A HTTP URL-cím másolása

Mielőtt elkezdené a Logic Apps designert, másolja ki azt az URL-címet, amelyet a logikai alkalmazás egy triggerhez figyel. Erre az URL-címre az Event Grid konfigurálásához lesz szükség.

1. Kattintással bontsa ki a **HTTP-kérés fogadásakor** triggerkonfigurációs mezőt.

2. Másolja ki a **HTTP POST URL** értékét a mellette lévő másolás gombra kattintva.

   ![A HTTP POST URL másolása](./media/iot-hub-how-to-order-connection-state-events/copy-url.png)

3. Mentse ezt az URL-címet, hogy a következő szakaszban hivatkozhasson majd rá.

## <a name="configure-subscription-for-iot-hub-events"></a>Az IoT Hub eseményeire való előfizetés beállítása

Ebben a szakaszban konfiguráljuk az IoT-központot, hogy közzétegye a bekövetkező eseményeket.

1. Az Azure Portalon keresse meg az IoT-központot.

2. Válassza az **Események** elemet.

   ![Az Event Grid-adatok megnyitása](./media/iot-hub-how-to-order-connection-state-events/event-grid.png)

3. Válassza az **+ esemény-előfizetés** lehetőséget.

   ![Új esemény-előfizetés létrehozása](./media/iot-hub-how-to-order-connection-state-events/event-subscription.png)

4. Adja meg az **esemény-előfizetés részleteit**: adjon meg egy leíró nevet, és válassza ki **Event Grid sémát**.

5. Adja meg az **események típusai** mezőket. A legördülő listában válassza a csak az **eszköz csatlakoztatva** és az **eszköz leválasztva** lehetőséget a menüből. A képernyő bezárásához és a kiválasztott elemek mentéséhez kattintson a képernyőn bárhová máshol.

   ![Keresse meg a keresett eseménytípus típusát](./media/iot-hub-how-to-order-connection-state-events/set-event-types.png)

6. A **végpont részletei** esetében válassza a végpont típusa **webes Hook** lehetőséget, majd kattintson a végpont kiválasztása elemre, és illessze be a logikai alkalmazásból másolt URL-címet, és erősítse meg a kijelölést.

   ![Végpont URL-címének kiválasztása](./media/iot-hub-how-to-order-connection-state-events/endpoint-select.png)

7. Az űrlapnak ekkor az alábbi példához hasonlóan kell kinéznie:

   ![Esemény-előfizetési űrlapminta](./media/iot-hub-how-to-order-connection-state-events/subscription-form.png)

   Az eseményfeliratkozás mentéséhez válassza a **Létrehozás** lehetőséget.

## <a name="observe-events"></a>Események megfigyelése

Most, hogy beállította az esemény-előfizetést, tesztelje egy eszköz csatlakoztatásával.

### <a name="register-a-device-in-iot-hub"></a>Eszköz regisztrálása a IoT Hubban

1. Válassza az IoT hub IoT- **eszközök** elemét.

2. A panel tetején kattintson a **+ Hozzáadás gombra** .

3. Az **Eszközazonosító** mezőbe írja be a következőt: `Demo-Device-1`.

4. Válassza a **Mentés** lehetőséget.

5. Több eszközt is hozzáadhat különböző eszközök azonosítói között.

   ![Az elosztóhoz hozzáadott eszközök](./media/iot-hub-how-to-order-connection-state-events/AddIoTDevice.png)

6. Kattintson ismét az eszközre; most a rendszer kitölti a kapcsolatok karakterláncait és kulcsait. Másolja a **kapcsolatok karakterláncát – az elsődleges kulcsot** későbbi használatra.

   ![Az eszköz ConnectionString.](./media/iot-hub-how-to-order-connection-state-events/DeviceConnString.png)

### <a name="start-raspberry-pi-simulator"></a>A málna PI szimulátor elindítása

A málna PI web Simulator használatával szimulálhatja az eszköz kapcsolatát.

[A málna PI szimulátor elindítása](https://azure-samples.github.io/raspberry-pi-web-simulator/#Getstarted)

### <a name="run-a-sample-application-on-the-raspberry-pi-web-simulator"></a>Minta alkalmazás futtatása a málna PI webes szimulátorban

Ez elindítja az eszköz csatlakoztatott eseményét.

1. A kódolás területen cserélje le a 15. sorban látható helyőrzőt az Azure IoT Hub Device-kapcsolatok karakterláncára, amelyet az előző szakasz végén mentett.

   ![Beillesztés az eszköz csatlakoztatási karakterláncában](./media/iot-hub-how-to-order-connection-state-events/raspconnstring.png)

2. Futtassa az alkalmazást a **Futtatás** lehetőség kiválasztásával.

A következő kimenethez hasonló érték jelenik meg, amely az érzékelő adatait és az IoT hub számára küldött üzeneteket jeleníti meg.

   ![Az alkalmazás futtatása](./media/iot-hub-how-to-order-connection-state-events/raspmsg.png)

   Kattintson a **Leállítás** gombra a szimulátor leállításához és az **eszköz leválasztott** eseményének elindításához.

Most futtatott egy minta alkalmazást az érzékelők adatainak összegyűjtéséhez és az IoT hub-ba való küldéséhez.

### <a name="observe-events-in-cosmos-db"></a>Események megfigyelése Cosmos DB

A végrehajtott tárolt eljárás eredményeit a Cosmos DB dokumentumban tekintheti meg. Így néz ki mindez. Minden sor a legújabb eszköz-csatlakoztatási állapotot tartalmazza eszközönként.

   ![Az eredmény](./media/iot-hub-how-to-order-connection-state-events/cosmosDB-outcome.png)

## <a name="use-the-azure-cli"></a>Az Azure parancssori felületének használata

A [Azure Portal](https://portal.azure.com)használata helyett a IoT hub lépéseket az Azure CLI használatával hajthatja végre. Részletekért lásd: Azure CLI-lapok az [esemény-előfizetések létrehozásához](/cli/azure/eventgrid/event-subscription) és [egy IoT-eszköz létrehozásához](/cli/azure/ext/azure-iot/iot/hub/device-identity#ext-azure-iot-az-iot-hub-device-identity-create).

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ebben az oktatóanyagban olyan erőforrásokat használtunk, amelyek költségekkel terhelik az Azure-előfizetését. Ha befejezte az oktatóanyag kipróbálását és az eredmények tesztelését, tiltsa le vagy törölje azokat az erőforrásokat, amelyeket nem szeretne megőrizni.

Ha nem szeretné elveszteni a logikai alkalmazásba fektetett munkáját, a törlés helyett csak tiltsa le.

1. Keresse meg a logikai alkalmazást.

2. Az **Áttekintés** panelen válassza a **Törlés** vagy a **Letiltás** lehetőséget.

    Mindegyik előfizetés egy ingyenes IoT-központtal rendelkezhet. Ha ingyenes központot hozott létre az oktatóanyaghoz, azt nem kell törölnie a költségek megelőzéséhez.

3. Keresse meg az IoT-központot.

4. Az **Áttekintés** panelen válassza a **Törlés** lehetőséget.

    Az esemény-előfizetést akkor is érdemes lehet törölnie, ha megtartja az IoT-központot.

5. Az IoT-központban válassza az **Event Grid** lehetőséget.

6. Válassza ki a törölni kívánt esemény-előfizetést.

7. Válassza a **Törlés** elemet.

Azure Cosmos DB fióknak a Azure Portalból való eltávolításához kattintson a jobb gombbal a fiók nevére, és kattintson a **fiók törlése** parancsra. [Azure Cosmos db fiók törlésének](../cosmos-db/how-to-manage-database-account.md)részletes utasításait itt tekintheti meg.

## <a name="next-steps"></a>Következő lépések

* További információ [IoT hub eseményekre való reagálásról Event Grid használatával a műveletek elindításához](../iot-hub/iot-hub-event-grid.md)

* [Próbálja ki a IoT Hub Events oktatóanyagot](../event-grid/publish-iot-hub-events-to-logic-apps.md)

* További információ arról, hogy mit tehet a [Event Grid](../event-grid/overview.md)