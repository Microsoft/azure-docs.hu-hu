---
title: Eszközkapcsolati események megrendelése Azure IoT Hub/Azure Cosmos DB
description: Ez a cikk azt ismerteti, hogyan lehet eszközkapcsolati eseményeket rendelni és rögzíteni a Azure IoT Hub a Azure Cosmos DB a legfrissebb kapcsolati állapot fenntartása érdekében
services: iot-hub
ms.service: iot-hub
author: ash2017
ms.topic: conceptual
ms.date: 04/11/2019
ms.author: asrastog
ms.custom: devx-track-azurecli
ms.openlocfilehash: bcbfc0941e3c97e96ebc3746b946553e67a10f93
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2021
ms.locfileid: "107878541"
---
# <a name="order-device-connection-events-from-azure-iot-hub-using-azure-cosmos-db"></a>Eszközkapcsolati események megrendelése az Azure IoT Hubról az Azure Cosmos DB használatával

Azure Event Grid segítségével eseményalapú alkalmazásokat építhet ki, és könnyedén integrálhatja az IoT-eseményeket az üzleti megoldásokba. Ez a cikk végigvezeti egy beállításon, amellyel nyomon követheti és tárolhatja a legújabb eszközkapcsolati Cosmos DB. Az Eszközhöz csatlakoztatott és az Eszköz leválasztva eseményekben elérhető sorszámot fogjuk használni, és a legújabb állapotot a következő Cosmos DB. Egy tárolt eljárást fogunk használni, amely egy alkalmazáslogika, amelyet a rendszer egy gyűjteményen hajt végre a Cosmos DB.

A sorszám hexadecimális szám sztringes ábrázolása. A nagyobb szám azonosításához használhatja az összehasonlító sztringet. Ha hexiálisra konvertálja a sztringet, akkor a szám 256 bites szám lesz. A sorszám szigorúan növekvő, és a legutóbbi esemény száma nagyobb lesz, mint a többi esemény. Ez akkor hasznos, ha gyakran csatlakozik az eszközhöz, és leválasztja a kapcsolatot, és biztosítani szeretné, hogy csak a legújabb esemény legyen használva egy lefelé irányuló művelet aktiválásához, mivel a Azure Event Grid nem támogatja az események sorrendjét.

## <a name="prerequisites"></a>Előfeltételek

* Aktív Azure-fiók. Ha még nincs fiókja, létrehozhat egy ingyenes [fiókot.](https://azure.microsoft.com/pricing/free-trial/)

* Egy aktív Azure Cosmos DB SQL API-fiók. Ha még nem hozott létre egyet, tekintse meg [az](../cosmos-db/create-sql-api-java.md#create-a-database-account) adatbázisfiókok létrehozásáról készült útmutatót.

* Egy gyűjtemény az adatbázisban. Az [útmutatót lásd:](../cosmos-db/create-sql-api-java.md#add-a-container) Gyűjtemény hozzáadása. A gyűjtemény létrehozásakor használja a `/id` partíciókulcsot.

* Egy IoT Hub az Azure-ban. Ha még nem hozott létre központot, [az IoT Hub első lépéseit](./quickstart-send-telemetry-dotnet.md) ismertető cikkben talál útmutatást.

## <a name="create-a-stored-procedure"></a>Tárolt eljárás létrehozása

Először hozzon létre egy tárolt eljárást, és állítsa be egy olyan logika futtatására, amely összehasonlítja a bejövő események sorozatszámait, és eszközönként rögzíti a legutóbbi eseményt az adatbázisban.

1. A Cosmos DB SQL API-ban válassza a **Adatkezelő**  >  **elemek**  >  **új tárolt eljárás lehetőséget.**

   ![Tárolt eljárás létrehozása](./media/iot-hub-how-to-order-connection-state-events/create-stored-procedure.png)

2. A tárolt eljárásazonosítóként adja meg a **LatestDeviceConnectionState** parancsot, és illessze be a következőt a **Tárolt eljárás törzsébe.** Vegye figyelembe, hogy ennek a kódnak le kell cserélnie a tárolt eljárás törzsében lévő összes meglévő kódot. Ez a kód eszközazonosítónként egy sort tart fenn, és az eszközazonosító legfrissebb kapcsolati állapotát rögzíti a legmagasabb sorszám azonosításával.

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

1. A [Azure Portal](https://portal.azure.com)válassza **az +Erőforrás létrehozása** lehetőséget, válassza az **Integráció,** majd a **Logikai alkalmazás lehetőséget.**

   ![Logikai alkalmazás létrehozása](./media/iot-hub-how-to-order-connection-state-events/select-logic-app.png)

2. Adjon az előfizetésben egyedi nevet a logikai alkalmazásnak, majd válassza ki ugyanazt az előfizetést, erőforráscsoportot és helyet, ahová az IoT-központ tartozik.

   ![Új logikai alkalmazás](./media/iot-hub-how-to-order-connection-state-events/new-logic-app.png)

3. A **logikai alkalmazás** létrehozásához válassza a Létrehozás lehetőséget.

   Ezzel létrehozott egy Azure-erőforrást a logikai alkalmazás számára. Miután az Azure üzembe helyezte a logikai alkalmazást, a Logic Apps Designer gyakran használt minták sablonjait jeleníti meg, hogy megkönnyítse az első lépéseket.

   > [!NOTE]
   > A logikai alkalmazás megkereséhez és  megnyitásához válassza az Erőforráscsoportok lehetőséget, majd válassza ki az ehhez az alkalmazáshoz használt erőforráscsoportot. Ezután válassza ki az új logikai alkalmazást. Ez megnyitja a Logikaialkalmazás-tervezőt.

4. A Logikaialkalmazás-tervezőben görgessen jobbra, amíg meg nem látja a gyakori eseményindítókat. A **Sablonok alatt** válassza az Üres **logikai** alkalmazás lehetőséget, hogy a logikai alkalmazást az nulláról építse fel.

### <a name="select-a-trigger"></a>Eseményindító kiválasztása

A trigger a logikai alkalmazást elindító konkrét esemény. Ebben az oktatóanyagban a munkafolyamatot aktiváló trigger HTTP-kapcsolaton keresztül fogad egy kérést.

1. Az összekötők és eseményindítók keresősávba írja be a **HTTP** parancsot, majd nyomja le az Enter billentyűt.

2. Válassza ki a **Kérés – HTTP-kérés fogadásakor** lehetőséget triggerként.

   ![HTTP-kérés trigger kiválasztása](./media/iot-hub-how-to-order-connection-state-events/http-request-trigger.png)

3. Válassza a **Séma létrehozása hasznosadat-minta használatával** lehetőséget.

   ![Séma létrehozása hasznosadat-minta használatával](./media/iot-hub-how-to-order-connection-state-events/sample-payload.png)

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

   ![Hasznos JSON-mintaadatok beillesztése](./media/iot-hub-how-to-order-connection-state-events/paste-sample-payload.png)

5. Egy előugró értesítés jelenhet meg a következő üzenettel: **Ne felejtsen el egy alkalmazás/JSON értékű Content-Type fejlécet beszúrni a kérésbe**. A javaslatot nyugodtan figyelmen kívül hagyhatja, és továbbléphet a következő szakaszra.

### <a name="create-a-condition"></a>Feltétel létrehozása

A logikai alkalmazás munkafolyamatában a feltételek adott műveletek futtatását segítik az adott feltétel átadása után. Ha a feltétel teljesült, meg lehet határozni egy kívánt műveletet. Ebben az oktatóanyagban a feltétel annak ellenőrzése, hogy az eventType csatlakoztatva van-e az eszközhöz, vagy az eszköz le van-e választva. A művelet az adatbázisban tárolt eljárás végrehajtása lesz.

1. Válassza **az + Új lépés,** majd a **Beépített** lehetőséget, majd keresse meg és válassza a **Feltétel lehetőséget.** Kattintson a **Choose a value (Érték** kiválasztása) elemre, és megjelenik egy mező, amely a dinamikus tartalmat ( a kiválasztható mezőket) mutatja. Töltse ki az alább látható mezőket, hogy csak az eszközhöz csatlakoztatott és az eszköz leválasztott eseményei esetén hajtsa végre a következőt:

   * Válasszon egy értéket: **eventType** – válassza ki a mezőre kattintva megjelenő dinamikus tartalom mezői közül.
   * Módosítsa a "egyenlő" értékeket a **végződőre.**
   * Válasszon egy értéket: **nected.**

     ![Kitöltési feltétel](./media/iot-hub-how-to-order-connection-state-events/condition-detail.png)

2. A ha **igaz párbeszédpanelen** kattintson a **Művelet hozzáadása lehetőségre.**
  
   ![Művelet hozzáadása igaz esetén](./media/iot-hub-how-to-order-connection-state-events/action-if-true.png)

3. Keressen rá a Cosmos DB, és válassza **a Azure Cosmos DB – Tárolt eljárás végrehajtása lehetőséget**

   ![CosmosDB keresése](./media/iot-hub-how-to-order-connection-state-events/cosmosDB-search.png)

4. Töltse ki **a cosmosdb-connection** nevet **a Kapcsolat neve** mezőben, válassza ki a táblázatban található bejegyzést, majd válassza a Létrehozás **lehetőséget.** Adatokat láthat a **Tárolt eljárás végrehajtása panelen.** Adja meg a mezők értékeit:

   **Adatbázis-azonosító:** ToDoList

   **Gyűjtemény azonosítója:** Elemek

   **Sproc-azonosító:** LatestDeviceConnectionState

5. Válassza **az Új paraméter hozzáadása lehetőséget.** A megjelenő legördülő menüben jelölje be a  Partíciókulcs és a Paraméterek melletti jelölőnégyzeteket a tárolt eljáráshoz, majd kattintson bárhová a képernyőn; Hozzáad egy mezőt a partíciókulcs értékhez és egy mezőt a tárolt eljárás paramétereihez.

   ![Képernyőkép egy Tárolt eljárás végrehajtása elemről, az Add new parameter (Új paraméter hozzáadása) lehetőség bejelölve.](./media/iot-hub-how-to-order-connection-state-events/logicapp-stored-procedure.png)

6. Most adja meg a partíciókulcs értékét és paramétereit az alább látható módon. A zárójeleket és a dupla idézőjeleket az itt látható módon helyezze el. Előfordulhat, hogy a Dinamikus tartalom **hozzáadása** lehetőségre kell kattintanunk az itt használható érvényes értékek lekért értékeivel.

   ![Képernyőkép egy Tárolt eljárás végrehajtása elemről, megadott paraméterekkel.](./media/iot-hub-how-to-order-connection-state-events/logicapp-stored-procedure-2.png)

7. Győződjön meg arról, hogy a Panel tetején a **For Each**(Minden elemhez) szöveg látható a Select an output from previous steps (Kimenet kiválasztása **az** előző lépésekből) alatt, és győződjön meg arról, hogy a  **Body (Törzs)** van kiválasztva.

   ![logikai alkalmazás feltöltése mindegyikhez](./media/iot-hub-how-to-order-connection-state-events/logicapp-foreach-body.png)

8. Mentse a logikai alkalmazást.

### <a name="copy-the-http-url"></a>A HTTP URL-cím másolása

Mielőtt elhagyja a Logic Apps Designerből, másolja ki azt az URL-címet, amit a logikai alkalmazás figyel az eseményindítóra. Erre az URL-címre az Event Grid konfigurálásához lesz szükség.

1. Kattintással bontsa ki a **HTTP-kérés fogadásakor** triggerkonfigurációs mezőt.

2. Másolja ki a **HTTP POST URL** értékét a mellette lévő másolás gombra kattintva.

   ![A HTTP POST URL másolása](./media/iot-hub-how-to-order-connection-state-events/copy-url.png)

3. Mentse ezt az URL-címet, hogy a következő szakaszban hivatkozhasson majd rá.

## <a name="configure-subscription-for-iot-hub-events"></a>Az IoT Hub eseményeire való előfizetés beállítása

Ebben a szakaszban konfiguráljuk az IoT-központot, hogy közzétegye a bekövetkező eseményeket.

1. Az Azure Portalon keresse meg az IoT-központot.

2. Válassza az **Események** elemet.

   ![Az Event Grid-adatok megnyitása](./media/iot-hub-how-to-order-connection-state-events/event-grid.png)

3. Válassza **az + Esemény-előfizetés lehetőséget.**

   ![Új esemény-előfizetés létrehozása](./media/iot-hub-how-to-order-connection-state-events/event-subscription.png)

4. Töltse ki **az Esemény-előfizetés részletei:** Adjon meg egy leíró nevet, és válassza a **Event Grid lehetőséget.**

5. Töltse ki az **Eseménytípusok mezőket.** A legördülő listában válassza ki a menüből csak az Eszköz **csatlakoztatva** és az Eszköz **leválasztva** lehetőséget. Kattintson bárhová a képernyőn a lista bezárása és a kijelölések mentése.

   ![Eseménytípusok beállítása a következőre:](./media/iot-hub-how-to-order-connection-state-events/set-event-types.png)

6. Az **Endpoint Details (Végpont részletei)** mezőben válassza a Endpoint Type (Végpont típusa) lehetőséget **Web hook (Web hook)** beállításnál, kattintson a Select endpoint (Végpont kiválasztása) elemre, illessze be a logikai alkalmazásból kimásott URL-címet, és erősítse meg a kijelölést.

   ![Végpont URL-címének kiválasztása](./media/iot-hub-how-to-order-connection-state-events/endpoint-select.png)

7. Az űrlapnak most az alábbi példához hasonlóan kell kinéznie:

   ![Esemény-előfizetési űrlapminta](./media/iot-hub-how-to-order-connection-state-events/subscription-form.png)

   Az eseményfeliratkozás mentéséhez válassza a **Létrehozás** lehetőséget.

## <a name="observe-events"></a>Események megfigyelése

Most, hogy beállította az esemény-előfizetést, teszteljük egy eszköz csatlakoztatásával.

### <a name="register-a-device-in-iot-hub"></a>Eszköz regisztrálása a IoT Hub

1. Az IoT Hubon válassza az **IoT-eszközök lehetőséget.**

2. Válassza **a +Hozzáadás** lehetőséget a panel tetején.

3. Az **Eszközazonosító** mezőbe írja be a következőt: `Demo-Device-1`.

4. Válassza a **Mentés** lehetőséget.

5. Több eszközt is hozzáadhat különböző eszköz-adattitkokkal.

   ![Hubhoz hozzáadott eszközök](./media/iot-hub-how-to-order-connection-state-events/AddIoTDevice.png)

6. Kattintson ismét az eszközre; Most a kapcsolati sztringek és kulcsok ki lesznek töltve. Másolja ki **a kapcsolati sztringet – az elsődleges kulcsot** későbbi használatra.

   ![Eszköz ConnectionString (Kapcsolati karakterlánc)](./media/iot-hub-how-to-order-connection-state-events/DeviceConnString.png)

### <a name="start-raspberry-pi-simulator"></a>Raspberry Pi-szimulátor kezdése

Használjuk a Raspberry Pi webes szimulátort az eszközkapcsolat szimulálására.

[Raspberry Pi-szimulátor kezdése](https://azure-samples.github.io/raspberry-pi-web-simulator/#Getstarted)

### <a name="run-a-sample-application-on-the-raspberry-pi-web-simulator"></a>Mintaalkalmazás futtatása a Raspberry Pi webes szimulátoron

Ez elindít egy eszközhöz kapcsolódó eseményt.

1. A kódolási területen cserélje le a 15. sorban található helyőrzőt az Azure IoT Hub előző szakasz végén mentett kapcsolati sztringjére.

   ![Eszköz kapcsolati sztringének beillesztése](./media/iot-hub-how-to-order-connection-state-events/raspconnstring.png)

2. Futtassa az alkalmazást a Futtatás **lehetőség kiválasztásával.**

Az alábbi kimenethez hasonlót fog látni, amely az érzékelők adatait és az IoT Hubnak küldött üzeneteket jeleníti meg.

   ![Az alkalmazás futtatása](./media/iot-hub-how-to-order-connection-state-events/raspmsg.png)

   A **stop (Leállítás)** gombra kattintva állítsa le a szimulátort, és indítson eszköz **leválasztott eseményét.**

Most már futtatott egy mintaalkalmazást az érzékelőadatok gyűjtéséhez és az IoT Hubba való elküldéhez.

### <a name="observe-events-in-cosmos-db"></a>Események megfigyelése a Cosmos DB

A végrehajtott tárolt eljárás eredményeit a dokumentumban Cosmos DB láthatja. Így néz ki mindez. Minden sor a legújabb eszközkapcsolati állapotot tartalmazza eszközönként.

   ![Az eredmény](./media/iot-hub-how-to-order-connection-state-events/cosmosDB-outcome.png)

## <a name="use-the-azure-cli"></a>Az Azure parancssori felületének használata

A Azure Portal [helyett](https://portal.azure.com)az Azure CLI használatával IoT Hub el a lépéseket. Részletekért lásd az Esemény-előfizetések létrehozásáról és az [IoT-eszközök létrehozásáról az Azure CLI lapjait.](/cli/azure/iot/hub/device-identity#az_iot_hub_device_identity_create) [](/cli/azure/eventgrid/event-subscription)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ebben az oktatóanyagban olyan erőforrásokat használtunk, amelyek költségekkel terhelik az Azure-előfizetését. Ha befejezte az oktatóanyag kipróbálását és az eredmények tesztelését, tiltsa le vagy törölje a megtartani nem kívánt erőforrásokat.

Ha nem szeretné elveszteni a logikai alkalmazásba fektetett munkáját, a törlés helyett csak tiltsa le.

1. Keresse meg a logikai alkalmazást.

2. Az Áttekintés **panelen** válassza a **Törlés vagy** a **Letiltás lehetőséget.**

    Mindegyik előfizetés egy ingyenes IoT-központtal rendelkezhet. Ha ingyenes központot hozott létre az oktatóanyaghoz, azt nem kell törölnie a költségek megelőzéséhez.

3. Keresse meg az IoT-központot.

4. Az Áttekintés **panelen** válassza a **Törlés lehetőséget.**

    Az esemény-előfizetést akkor is érdemes lehet törölnie, ha megtartja az IoT-központot.

5. Az IoT-központban válassza az **Event Grid** lehetőséget.

6. Válassza ki a törölni kívánt esemény-előfizetést.

7. Válassza a **Törlés** elemet.

Ha el szeretne távolítani egy Azure Cosmos DB fiókot a Azure Portal kattintson a jobb gombbal a fiók nevére, majd kattintson a **Fiók törlése parancsra.** A fiók törlésére [vonatkozó részletes Azure Cosmos DB lásd:](../cosmos-db/how-to-manage-database-account.md).

## <a name="next-steps"></a>Következő lépések

* További információ a műveletek IoT Hub eseményekre való Event Grid [eseményekre való reagálásról](../iot-hub/iot-hub-event-grid.md)

* [Próbálja ki a IoT Hub események oktatóanyagát](../event-grid/publish-iot-hub-events-to-logic-apps.md)

* Ismerje meg, mi másra használhatja a [Event Grid](../event-grid/overview.md)