---
title: Oktatóanyag – eszköz állapotának szinkronizálása az Azure IoT Hubból | Microsoft Docs
description: Oktatóanyag – megtudhatja, hogyan konfigurálhatja az eszközöket a felhőből, és hogyan fogadhatja el az eszközök állapotát és megfelelőségi adatait.
services: iot-hub
author: wesmc7777
ms.author: wesmc
ms.service: iot-hub
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 06/21/2019
ms.custom:
- mvc
- mqtt
- 'Role: Cloud Development'
- 'Role: IoT Device'
- devx-track-js
- devx-track-azurecli
ms.openlocfilehash: 7dbc0404679927bcef1647dfdf46ce3360216a79
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98733300"
---
<!-- **TODO** Update publish config with repo paths before publishing! -->

# <a name="tutorial-configure-your-devices-from-a-back-end-service"></a>Oktatóanyag: Eszközök konfigurálása háttérszolgáltatásból

Az eszközökről érkező telemetriai adatok fogadása mellett háttérszolgáltatásból végrehajtott eszközkonfigurációra is szükség lehet. A kívánt konfiguráció eszközökre történő elküldését követően hasznos, ha az állapotra és a megfelelőségi frissítésekre vonatkozó visszajelzést kap az érintett eszközöktől. Előfordulhat például, hogy működési hőmérsékletre vonatkozó céltartományt szeretne beállítani egy eszközhöz, vagy össze szeretné gyűjteni az eszközök belső vezérlőprogramjainak verzióadatait.

Az állapotinformációk eszköz és IoT-központ közötti szinkronizálásához használjon _ikereszközöket_. Az [ikereszköz](iot-hub-devguide-device-twins.md) egy adott eszközzel társított JSON-dokumentum, melyet az IoT Hub a felhőben tárol, és onnan [le is kérdezhető](iot-hub-devguide-query-language.md). Az ikereszköz _kívánt tulajdonságokból_, _jelentett tulajdonságokból_ és _címkékből_ áll. A kívánt tulajdonságot egy háttéralkalmazás állítja és egy eszköz olvassa be. A jelentett tulajdonságot egy eszköz állítja és egy háttéralkalmazás olvassa be. A címkét egy háttéralkalmazás állítja be, és a rendszer soha nem küldi el az eszközre. A címkék az eszközök rendszerezésére szolgálnak. Ez az oktatóanyag bemutatja, hogyan használhatók a kívánt és jelentett tulajdonságok az állapotinformációk szinkronizálására:

![Az ikereszközök összefoglalása](media/tutorial-device-twins/DeviceTwins.png)

Az oktatóanyagban az alábbi feladatokat fogja végrehajtani:

> [!div class="checklist"]
> * Létrehoz egy IoT-központot, és hozzáad egy teszteszközt az identitásjegyzékhez.
> * A kívánt tulajdonságok használatával állapotinformációt küld a szimulált eszközre.
> * A jelentett tulajdonságok használatával állapotinformációt fogad a szimulált eszközről.

Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

A rövid útmutatóban futtatott két mintaalkalmazás a Node.js használatával készült. A fejlesztői gépen Node.js v10. x. x vagy újabb verzió szükséges.

A Node.js-t a [nodejs.org](https://nodejs.org) oldalról töltheti le többféle platformra.

A Node.js aktuális verzióját a következő paranccsal ellenőrizheti a fejlesztői gépen:

```cmd/sh
node --version
```

Töltse le a Node.js-mintaprojektet a https://github.com/Azure-Samples/azure-iot-samples-node/archive/master.zip címről, és bontsa ki a ZIP-archívumot.

Győződjön meg arról, hogy a 8883-es port meg van nyitva a tűzfalon. Az oktatóanyagban szereplő MQTT protokollt használ, amely a 8883-as porton keresztül kommunikál. Lehetséges, hogy ez a port bizonyos vállalati és oktatási hálózati környezetekben blokkolva van. A probléma megoldásával kapcsolatos további információkért lásd: [csatlakozás IoT hubhoz (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

## <a name="set-up-azure-resources"></a>Az Azure-erőforrások beállítása

Az oktatóanyag teljesítéséhez az Azure-előfizetésnek rendelkeznie kell egy olyan IoT-központtal, amelyhez tartoznia kell egy, az eszköz identitásjegyzékéhez hozzáadott eszköznek. Az eszköz identitásjegyzékében található bejegyzés teszi lehetővé az oktatóanyagban futtatott szimulált eszköz csatlakozását a központhoz.

Ha még nem rendelkezik az előfizetésben beállított IoT hub-vel, akkor a következő CLI-szkripttel állíthat be egyet. Ez a szkript a **tutorial-iot-hub** nevet használja az IoT-központhoz, de futtatáskor a nevet le kell cserélni az Ön által megadott egyedi névre. A szkript az erőforráscsoportot és a központot az **USA középső régiójában** hozza létre, de ez módosítható egy Önhöz közelebb eső régióra. A szkript lekéri az IoT Hub szolgáltatás kapcsolati sztringjét, amellyel a mintául szolgáló háttérrendszerben csatlakozhat az IoT-központhoz:

```azurecli-interactive
hubname=tutorial-iot-hub
location=centralus

# Install the IoT extension if it's not already installed:
az extension add --name azure-iot

# Create a resource group:
az group create --name tutorial-iot-hub-rg --location $location

# Create your free-tier IoT Hub. You can only have one free IoT Hub per subscription.
az iot hub create --name $hubname --location $location --resource-group tutorial-iot-hub-rg --partition-count 2 --sku F1

# Make a note of the service connection string, you need it later:
az iot hub connection-string show --name $hubname --policy-name service -o table

```

Ez az oktatóanyag egy **MyTwinDevice** elnevezésű szimulált eszközt használ. A következő szkript hozzáadja ezt az eszközt az identitásjegyzékhez, és lekéri a kapcsolati sztringet:

```azurecli-interactive
# Set the name of your IoT hub:
hubname=tutorial-iot-hub

# Create the device in the identity registry:
az iot hub device-identity create --device-id MyTwinDevice --hub-name $hubname --resource-group tutorial-iot-hub-rg

# Retrieve the device connection string, you need this later:
az iot hub device-identity connection-string show --device-id MyTwinDevice --hub-name $hubname --resource-group tutorial-iot-hub-rg -o table

```

## <a name="send-state-information"></a>Állapotinformációk küldése

A kívánt tulajdonságok használatával állapotinformációkat küldhet a háttéralkalmazásból az eszközre. Ez a szakasz a következőket ismerteti:

* Kívánt tulajdonságok fogadása és feldolgozása egy eszközön.
* Kívánt tulajdonságok küldése egy háttéralkalmazásból.

A kívánt tulajdonságokat fogadó szimulált eszköz mintakódjának megtekintéséhez keresse meg az **iot-hub/Tutorials/DeviceTwins** mappát a letöltött Node.js mintaprojektben. Ezután nyissa meg a SimulatedDevice.js fájlt egy szövegszerkesztőben.

A következő szakaszok azt a szimulált eszközön futtatott kódot ismertetik, amely a kívánt tulajdonságok háttéralkalmazásból küldött módosításaira válaszol:

### <a name="retrieve-the-device-twin-object"></a>Az ikereszköz-objektum lekérése

A következő kód egy eszközkapcsolati sztringgel csatlakozik az Ön IoT-központjához:

[!code-javascript[Create IoT Hub client](~/iot-samples-node/iot-hub/Tutorials/DeviceTwins/SimulatedDevice.js?name=createhubclient&highlight=2 "Create IoT Hub client")]

A következő kód lekér egy ikereszközt az ügyfélobjektumból:

[!code-javascript[Get twin](~/iot-samples-node/iot-hub/Tutorials/DeviceTwins/SimulatedDevice.js?name=gettwin&highlight=2 "Get twin")]

### <a name="sample-desired-properties"></a>Mintául szolgáló kívánt tulajdonságok

A kívánt tulajdonságokat bármilyen módon rendezheti, ahogyan az alkalmazásnak megfelel. Ez a példa egy **fanOn** elnevezésű legfelső szintű tulajdonságot használ, a többi tulajdonságot pedig külön **components** (összetevők) elemekbe csoportosítja. A következő JSON-kódrészlet az oktatóanyag által használt kívánt tulajdonságok szerkezetét jeleníti meg. A JSON a fájl desired.jstalálható.

[!code[Sample desired properties](~/iot-samples-node/iot-hub/Tutorials/DeviceTwins/desired.json "Sample desired properties")]

### <a name="create-handlers"></a>Kezelők létrehozása

A kívánt tulajdonságok frissítéséhez kezelőket hozhat lére, amelyek válaszolnak a JSON-hierarchia különböző szintjeinek frissítéseire. Ez a kezelő például látja a háttéralkalmazásból az eszközre küldött kívánt tulajdonság összes módosítását. A **delta** változó a megoldás háttérrendszeréből küldött kívánt tulajdonságokból áll:

[!code-javascript[Handle all properties](~/iot-samples-node/iot-hub/Tutorials/DeviceTwins/SimulatedDevice.js?name=allproperties&highlight=2 "Handle all properties")]

Az alábbi kezelő csak a **fanOn** kívánt tulajdonságon végrehajtott módosításokra reagál:

[!code-javascript[Handle fan property](~/iot-samples-node/iot-hub/Tutorials/DeviceTwins/SimulatedDevice.js?name=fanproperty&highlight=2 "Handle fan property")]

### <a name="handlers-for-multiple-properties"></a>Több tulajdonsághoz való kezelők

A kívánt tulajdonságok előbbi példájában a **components** alatti **climate** csomópont két tulajdonságból áll: ezek a **minTemperature** és a **maxTemperature**.

Egy eszköz helyi **ikerobjektuma** tárolja a kívánt és jelentett tulajdonságok teljes készletét. Előfordulhat, hogy a háttérrendszerből küldött **delta** változó a kívánt tulajdonságoknak csak egy részét frissíti. A következő kódrészlet, ha a szimulált eszköz frissítést kap a **minTemperature** és a **maxTemperature** tulajdonság egyikéhez, akkor az eszköz konfigurálásához a helyi ikerobjektumban szereplő értéket adja meg a másik tulajdonsághoz:

[!code-javascript[Handle climate component](~/iot-samples-node/iot-hub/Tutorials/DeviceTwins/SimulatedDevice.js?name=climatecomponent&highlight=2 "Handle climate component")]

A helyi **ikerobjektum** a kívánt és jelentett tulajdonságok teljes készletét tárolja. Előfordulhat, hogy a háttérrendszerből küldött **delta** változó a kívánt tulajdonságoknak csak egy részét frissíti.

### <a name="handle-insert-update-and-delete-operations"></a>Beszúrási, frissítési és törlési műveletek kezelése

A háttérrendszerből küldött kívánt tulajdonságok nem jelzik, hogy milyen művelet végrehajtására kerül sor egy adott kívánt tulajdonságon. A kódnak a kívánt tulajdonságok helyben, adott pillanatban tárolt készletéből és a központból küldött módosításokból kell következtetnie a műveletre.

A következő kódrészlet bemutatja, hogyan kezeli a szimulált eszköz a beszúrási, frissítési és törlési műveleteket a **components** (összetevők) listáján a kívánt tulajdonságokban. Láthatja, hogyan használja a rendszer a **null** értéket annak jelzésére, ha egy összetevő törölhető:

[!code-javascript[Handle components](~/iot-samples-node/iot-hub/Tutorials/DeviceTwins/SimulatedDevice.js?name=components&highlight=2,6,13 "Handle components")]

### <a name="send-desired-properties-to-a-device-from-the-back-end"></a>Kívánt tulajdonságok küldése egy eszközre a háttérrendszerből

Láttuk, hogyan implementál egy eszköz kezelőket a kívánt tulajdonságok frissítésének fogadására. Ez a szakasz azt mutatja be, hogyan lehet kívánttulajdonság-módosításokat küldeni egy eszközre egy háttéralkalmazásból.

A kívánt tulajdonságokat fogadó szimulált eszköz mintakódjának megtekintéséhez keresse meg az **iot-hub/Tutorials/DeviceTwins** mappát a letöltött Node.js mintaprojektben. Ezután nyissa meg a ServiceClient.js fájlt egy szövegszerkesztőben.

A következő kódrészlet azt illusztrálja, hogyan kapcsolódhat az eszköz identitásjegyzékhez és férhet hozzá egy adott eszköz ikereszközéhez:

[!code-javascript[Create registry and get twin](~/iot-samples-node/iot-hub/Tutorials/DeviceTwins/ServiceClient.js?name=getregistrytwin&highlight=2,6 "Create registry and get twin")]

A következő kódrészlet olyan különböző, kívánt tulajdonságokra vonatkozó *javításokat* mutat be, amelyeket a háttéralkalmazás küld az eszközre:

[!code-javascript[Patches sent to device](~/iot-samples-node/iot-hub/Tutorials/DeviceTwins/ServiceClient.js?name=patches&highlight=2,12,26,41,56 "Patches sent to device")]

A következő kódrészlet azt mutatja meg, hogyan küld a háttéralkalmazás kívánt tulajdonságokat egy eszközre:

[!code-javascript[Send desired properties](~/iot-samples-node/iot-hub/Tutorials/DeviceTwins/ServiceClient.js?name=senddesiredproperties&highlight=2 "Send desired properties")]

### <a name="run-the-applications"></a>Az alkalmazások futtatása

Ebben a szakaszban két mintaalkalmazás futtatásával figyelhető meg, hogyan küld egy háttéralkalmazás kívánttulajdonság-frissítéseket egy szimulálteszköz-alkalmazásnak.

A szimulált eszköz és a háttéralkalmazások futtatásához szükség lesz az eszköz és a szolgáltatás kapcsolati sztringjére. Ezt a sztringet az oktatóanyag elején, az erőforrások létrehozásánál jegyezte fel.

A szimulálteszköz-alkalmazás futtatásához nyisson meg egy kezelőfelületet vagy parancssori ablakot, és keresse meg a letöltött Node.js-projektben az **iot-hub/Tutorials/DeviceTwins** mappát. Ezután futtassa le a következő parancsokat:

```cmd/sh
npm install
node SimulatedDevice.js "{your device connection string}"
```

A háttéralkalmazás futtatásához nyisson meg egy újabb kezelőfelületet vagy parancssori ablakot. Keresse meg a letöltött Node.js-projektben az **iot-hub/Tutorials/DeviceTwins** mappát. Ezután futtassa le a következő parancsokat:

```cmd/sh
npm install
node ServiceClient.js "{your service connection string}"
```

A következő képernyőkép bemutatja a szimulálteszköz-alkalmazás kimenetét, a kiemelt rész pedig arra vonatkozik, hogyan frissíti a **maxTemperature** kívánt tulajdonságot. Látható, hogy a legfelső szintű kezelő és az időjárási összetevő (climate component) egyaránt fut:

![Képernyőkép, amely bemutatja, hogyan futnak a legfelső szintű kezelő és a klíma-összetevő kezelői.](./media/tutorial-device-twins/SimulatedDevice1.png)

A következő képernyőkép bemutatja a háttéralkalmazás kimenetét, a kiemelt rész pedig arra vonatkozik, hogyan küld frissítést a **maxTemperature** kívánt tulajdonságnak:

![Képernyőkép, amely megjeleníti a háttérbeli alkalmazás kimenetét, és kiemeli, hogyan küld egy frissítést.](./media/tutorial-device-twins/BackEnd1.png)

## <a name="receive-state-information&quot;></a>Állapotinformáció fogadása

A háttéralkalmazás jelentett tulajdonságok formájában fogadja az eszközök állapotinformációit. Az eszköz beállítja a jelentett tulajdonságokat, és elküldi őket a központnak. A háttéralkalmazás a központban tárolt ikereszközről le tudja olvasni a jelentett tulajdonságok aktuális értékét.

### <a name=&quot;send-reported-properties-from-a-device&quot;></a>Jelentett tulajdonságok küldése egy eszközről

Jelentett tulajdonságokra frissítéseket javítások formájában küldhet. A következő kódrészlet egy szimulált eszköz által küldött javítássablont mutat be. A szimulált eszköz frissíti a javítás mezőit, mielőtt elküldi a központnak:

[!code-javascript[Reported properties patches](~/iot-samples-node/iot-hub/Tutorials/DeviceTwins/SimulatedDevice.js?name=reportedpatch&highlight=2 &quot;Reported properties patches")]

A szimulált eszköz a következő függvény segítségével küldi el a jelentett tulajdonságokból álló javítást a központnak:

[!code-javascript[Send reported properties](~/iot-samples-node/iot-hub/Tutorials/DeviceTwins/SimulatedDevice.js?name=sendreportedproperties&highlight=2 "Send reported properties")]

### <a name="process-reported-properties"></a>Jelentett tulajdonságok feldolgozása

Egy háttéralkalmazás a jelentett tulajdonságok aktuális értékéhez az ikereszközön keresztül fér hozzá. A következő kódrészlet bemutatja, hogyan olvassa be a háttéralkalmazás szimulált eszköz jelentett tulajdonságait:

[!code-javascript[Display reported properties](~/iot-samples-node/iot-hub/Tutorials/DeviceTwins/ServiceClient.js?name=displayreportedproperties&highlight=2 "Display reported properties")]

### <a name="run-the-applications"></a>Az alkalmazások futtatása

Ebben a szakaszban két mintaalkalmazás futtatásával figyelhető meg, hogyan küld egy szimulált eszközalkalmazás jelentett tulajdonságra vonatkozó frissítéseket egy háttéralkalmazásnak.

A korábban futtatott két mintaalkalmazás ismételt futtatásával megtekintheti, hogyan küldi el a rendszer egy eszközre a kívánt tulajdonságokat.

A szimulált eszköz és a háttéralkalmazások futtatásához szükség lesz az eszköz és a szolgáltatás kapcsolati sztringjére. Ezt a sztringet az oktatóanyag elején, az erőforrások létrehozásánál jegyezte fel.

A szimulálteszköz-alkalmazás futtatásához nyisson meg egy kezelőfelületet vagy parancssori ablakot, és keresse meg a letöltött Node.js-projektben az **iot-hub/Tutorials/DeviceTwins** mappát. Ezután futtassa le a következő parancsokat:

```cmd/sh
npm install
node SimulatedDevice.js "{your device connection string}"
```

A háttéralkalmazás futtatásához nyisson meg egy újabb kezelőfelületet vagy parancssori ablakot. Keresse meg a letöltött Node.js-projektben az **iot-hub/Tutorials/DeviceTwins** mappát. Ezután futtassa le a következő parancsokat:

```cmd/sh
npm install
node ServiceClient.js "{your service connection string}"
```

A következő képernyőkép bemutatja a szimulált eszközalkalmazás kimenetét, a kiemelt rész pedig arra vonatkozik, hogyan küld a központnak jelentett tulajdonságra vonatkozó frissítést:

![Szimulált eszköz](./media/tutorial-device-twins/SimulatedDevice2.png)

Az alábbi képernyőfelvételen a háttérbeli alkalmazás kimenete látható, és kiemeli, hogyan fogadja és dolgozza fel egy jelentett tulajdonság frissítését egy eszközről:

![Háttéralkalmazás](./media/tutorial-device-twins/BackEnd2.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha a következő oktatóanyagot is el kívánja végezni, ne távolítsa el az erőforráscsoportot és az IoT-központot, mert ezeket később újból használhatja.

Ha már nincs szüksége az IoT Hubra, az erőforráscsoporttal együtt törölje a Portalon. Ehhez válassza ki az IoT-központot tartalmazó **tutorial-iot-hub-rg** erőforráscsoportot, majd kattintson a **Törlés** gombra.

Ha szeretné, a CLI-t is használhatja:

```azurecli-interactive
# Delete your resource group and its contents
az group delete --name tutorial-iot-hub-rg
```

## <a name="next-steps"></a>Következő lépések

Ez az oktatóanyag azt ismertette, hogyan szinkronizálhatja az állapotinformációkat az eszközök és az IoT Hub között. Folytassa a következő oktatóanyaggal, ha azt szeretné megtudni, hogyan implementálható ikereszközökkel egy belső vezérlőprogram frissítési folyamata.

> [!div class="nextstepaction"]
> [Eszközök belsővezérlőprogram-frissítési folyamatának implementálása](tutorial-firmware-update.md)
