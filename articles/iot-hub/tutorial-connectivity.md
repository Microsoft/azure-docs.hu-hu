---
title: Oktatóanyag – az eszköz kapcsolatának ellenőrzése az Azure IoT Hub
description: Oktatóanyag – IoT Hub-eszközök használata a IoT-hubhoz való fejlesztés során felmerülő problémák elhárításához.
services: iot-hub
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.custom:
- mvc
- amqp
- mqtt
- 'Role: Cloud Development'
- 'Role: IoT Device'
- devx-track-js
- devx-track-azurecli
ms.date: 02/22/2019
ms.topic: tutorial
ms.service: iot-hub
ms.openlocfilehash: d9ca9dd345be7fb8f1a2b613386bdca7bdcd835a
ms.sourcegitcommit: dd24c3f35e286c5b7f6c3467a256ff85343826ad
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2021
ms.locfileid: "99070381"
---
# <a name="tutorial-use-a-simulated-device-to-test-connectivity-with-your-iot-hub"></a>Oktatóanyag: Szimulált eszköz használata az IoT Hub-kapcsolat ellenőrzéséhez

Ebben az oktatóanyagban az Azure IoT Hub portál eszközeivel és az Azure CLI parancsaival teszteli az eszközkapcsolatot. Az oktatóanyag emellett egy egyszerű eszközszimulátort is használ, amelyet az asztali gépén futtat.

Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:
> [!div class="checklist"]
> * Az eszközhitelesítés ellenőrzése
> * Az eszközről a felhőbe irányuló kapcsolat ellenőrzése
> * A felhőből az eszközre irányuló kapcsolat ellenőrzése
> * Ikereszköz-szinkronizálás ellenőrzése

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

[!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

Az oktatóanyagban futtatott eszközszimulátor alkalmazás Node.js használatával készült. A fejlesztői gépen Node.js v10. x. x vagy újabb verzió szükséges.

A Node.js-t a [nodejs.org](https://nodejs.org) oldalról töltheti le többféle platformra.

A Node.js aktuális verzióját a következő paranccsal ellenőrizheti a fejlesztői gépen:

```cmd/sh
node --version
```

Töltse le a Node.js eszközszimulátor mintaprojektjét a https://github.com/Azure-Samples/azure-iot-samples-node/archive/master.zip címről, és bontsa ki a ZIP-archívumot.

Győződjön meg arról, hogy a 8883-es port meg van nyitva a tűzfalon. Az oktatóanyagban szereplő MQTT protokollt használ, amely a 8883-as porton keresztül kommunikál. Lehetséges, hogy ez a port bizonyos vállalati és oktatási hálózati környezetekben blokkolva van. A probléma megoldásával kapcsolatos további információkért lásd: [csatlakozás IoT hubhoz (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

## <a name="create-an-iot-hub"></a>IoT Hub létrehozása

Ha egy korábbi rövid útmutató vagy oktatóanyag során létrehozott egy ingyenes vagy standard szintű IoT Hubot, kihagyhatja ezt a lépést.

[!INCLUDE [iot-hub-tutorials-create-free-hub](../../includes/iot-hub-tutorials-create-free-hub.md)]

## <a name="check-device-authentication"></a>Eszközhitelesítés ellenőrzése

Az eszközök csak akkor végezhetnek adatcserét a hubbal, ha a hitelesítésük megtörtént. A portál **Eszközkezelés** szakaszában az **IoT-eszközök** eszközzel felügyelheti az eszközeit, és itt ellenőrizheti az általuk használt hitelesítési kulcsokat. Az oktatóanyag ezen szakaszában hozzáad egy új teszteszközt, lekéri a kulcsát, és ellenőrzi, hogy a teszteszköz képes-e csatlakozni a hubhoz. Később visszaállítja a hitelesítési kulcsot, hogy megtekintse, mi történik, amikor egy eszköz egy elavult kulcsot próbál használni. Az oktatóanyag ezen szakaszában az Azure Portal használatával létrehoz, felügyel és monitoroz egy eszközt, valamint a mintaként használt Node.js eszközszimulátort.

Jelentkezzen be a portálra, és keresse meg az IoT Hubot. Ezt követően nyissa meg az **IoT-eszközök** eszközt:

![IoT-eszközök eszköz](media/tutorial-connectivity/iot-devices-tool.png)

Új eszköz regisztrálásához kattintson a **+ Hozzáadás** gombra, az **Eszközazonosítóként** adja meg a **MyTestDevice** nevet, majd kattintson a **Mentés** gombra:

![Új eszköz hozzáadása](media/tutorial-connectivity/add-device.png)

A **MyTestDevice** kapcsolati sztringjének lekéréséhez kattintson rá az eszközlistán, majd másolja a **Kapcsolati sztring – elsődleges kulcs** értékét. A kapcsolati sztring tartalmazza az eszköz *megosztott hozzáférési kulcsát*.

![Az eszköz kapcsolati sztringjének lekérése](media/tutorial-connectivity/copy-connection-string.png)

A **MyTestDevice** által az IoT hubnak küldött telemetria szimulálásához futtassa a Node.js szimulálteszköz-alkalmazást, amelyet korábban letöltött.

A fejlesztői gépen egy terminálablakban keresse meg a letöltött Node.js-mintaprojekt gyökérmappáját. Ezután keresse meg a **IOT-hub\Tutorials\ConnectivityTests** mappát.

Futtassa az alábbi parancsokat a terminálablakban a szükséges kódtárak telepítéséhez és a szimulálteszköz-alkalmazás futtatásához. Az eszköznek a portálon való hozzáadásakor jegyezze fel az eszközhöz tartozó kapcsolódási karakterláncot.

```cmd/sh
npm install
node SimulatedDevice-1.js "{your device connection string}"
```

A terminálablak információkat jelenít meg, miközben csatlakozni próbál a hubhoz:

![Szimulált eszköz csatlakoztatása](media/tutorial-connectivity/sim-1-connected.png)

Sikeresen hitelesített egy eszközről az IoT Hub által létrehozott eszközkulcs használatával.

### <a name="reset-keys"></a>Kulcsok visszaállítása

Ebben a szakaszban visszaállítja az eszközkulcsot, és megfigyeli a hibát, amikor a szimulált eszköz csatlakozni próbál.

A **MyTestDevice** elsődleges eszközkulcsának visszaállításához futtassa a következő parancsokat:

```azurecli-interactive
# Generate a new Base64 encoded key using the current date
read key < <(date +%s | sha256sum | base64 | head -c 32)

# Requires the IoT Extension for Azure CLI
# az extension add --name azure-iot

# Reset the primary device key for MyTestDevice
az iot hub device-identity update --device-id MyTestDevice --set authentication.symmetricKey.primaryKey=$key --hub-name {YourIoTHubName}
```

A fejlesztési gépen a terminálablakban futtassa ismét a szimulálteszköz-alkalmazást:

```cmd/sh
npm install
node SimulatedDevice-1.js "{your device connection string}"
```

Ezúttal hitelesítési hibát lát, amikor az alkalmazás csatlakozni próbál:

![Csatlakozási hiba](media/tutorial-connectivity/sim-1-fail.png)

### <a name="generate-shared-access-signature-sas-token"></a>Közös hozzáférésű jogosultságkód (SAS) jogkivonat létrehozása

Ha az eszköze az IoT Hub egyik eszköz SDK-ját használja, az SDK kódtár kódja hozza létre a hubbal való hitelesítéshez használt SAS-jogkivonatot. A SAS-jogkivonat a hubja nevéből, az eszköze nevéből és az eszközkulcsból jön létre.

Bizonyos esetekben előfordulhat, például egy felhő protokollátjárójában vagy egy egyéni hitelesítési séma részeként, hogy saját kezűleg kell létrehoznia a SAS-jogkivonatot. Az SAS-létrehozási kóddal kapcsolatos problémák elhárításához hasznos lehet egy ismert, jó SAS-token létrehozása a tesztelés során.

> [!NOTE]
> A SimulatedDevice-2.js az SAS-jogkivonat SDK-val és SDK nélkül történő létrehozására is tartalmaz példákat.

Egy működő SAS-jogkivonat parancssori felületen való létrehozásához futtassa a következő parancsot:

```azurecli-interactive
az iot hub generate-sas-token --device-id MyTestDevice --hub-name {YourIoTHubName}
```

Jegyezze fel a létrehozott SAS-jogkivonat teljes szövegét. A SAS-jogkivonatok a következőképpen néznek ki: `SharedAccessSignature sr=tutorials-iot-hub.azure-devices.net%2Fdevices%2FMyTestDevice&sig=....&se=1524155307`

A fejlesztői gépen egy terminálablakban keresse meg a letöltött Node.js-mintaprojekt gyökérmappáját. Ezután keresse meg a **IOT-hub\Tutorials\ConnectivityTests** mappát.

Futtassa az alábbi parancsokat a terminálablakban a szükséges kódtárak telepítéséhez és a szimulálteszköz-alkalmazás futtatásához:

```cmd/sh
npm install
node SimulatedDevice-2.js "{Your SAS token}"
```

A terminálablak információkat jelenít meg, miközben csatlakozni próbál a hubhoz a SAS-jogkivonat használatával:

![Szimulált eszköz csatlakoztatása egy jogkivonattal](media/tutorial-connectivity/sim-2-connected.png)

Sikeresen hitelesített egy eszközről egy parancssori felületi paranccsal létrehozott tesztelési SAS-jogkivonat használatával. A **SimulatedDevice-2.js** fájl mintakódot tartalmaz, amely megmutatja, hogyan hozhat létre egy SAS-jogkivonatot a kódban.

### <a name="protocols"></a>Protokollok

Az eszközök az alábbi protokollok bármelyikével csatlakozhatnak az IoT Hubhoz:

| Protokoll | Kimenő port |
| --- | --- |
| MQTT |8883 |
| MQTT WebSocketen keresztül |443 |
| AMQP |5671 |
| AMQP WebSocketen keresztül |443 |
| HTTPS |443 |

Ha a kimenő portot blokkolja egy tűzfal, az eszköz nem tud csatlakozni:

![Port blokkolva](media/tutorial-connectivity/port-blocked.png)

## <a name="check-device-to-cloud-connectivity"></a>Az eszközről a felhőbe irányuló kapcsolat ellenőrzése

Miután egy eszköz csatlakozik, általában telemetriát próbál küldeni az IoT Hubnak. Ez a szakasz bemutatja, hogyan ellenőrizheti, hogy az eszközök által küldött telemetria eléri-e a hubot.

Először kérje le a szimulált eszköz aktuális kapcsolati sztringjét a következő paranccsal:

```azurecli-interactive
az iot hub device-identity connection-string show --device-id MyTestDevice --output table --hub-name {YourIoTHubName}
```

Az üzeneteket küldő szimulált eszköz futtatásához navigáljon a letöltött kód **IOT-hub\Tutorials\ConnectivityTests** mappájába.

Futtassa az alábbi parancsokat a terminálablakban a szükséges kódtárak telepítéséhez és a szimulálteszköz-alkalmazás futtatásához:

```cmd/sh
npm install
node SimulatedDevice-3.js "{your device connection string}"
```

A terminálablak információkat jelenít meg, miközben telemetriát küld a hubnak:

![A szimulált eszköz üzeneteket küld](media/tutorial-connectivity/sim-3-sending.png)

A portál **metrikái** segítségével ellenőrizheti, hogy a telemetria üzenetek elérik-e az IoT hubot. Válassza ki az IoT Hubot az **Erőforrás** legördülő menüből, válassza az **Elküldött telemetriai üzeneteket** metrikaként, majd állítsa az időtartományt az **Elmúlt óra** lehetőségre. A diagram a szimulált eszköz által elküldött üzenetek összegzett darabszámát jeleníti meg:

![IoT Hub metrikáinak megjelenítése](media/tutorial-connectivity/metrics-portal.png)

Néhány percig tart, amíg a metrikák elérhetővé válnak, miután elindította a szimulált eszközt.

## <a name="check-cloud-to-device-connectivity"></a>A felhőből az eszközre irányuló kapcsolat ellenőrzése

Ez a szakasz bemutatja, hogyan indíthat egy tesztelési közvetlenmetódus-hívást egy eszközre, hogy ellenőrizze a felhőből az eszközre irányuló kapcsolatot. Egy szimulált eszközt futtat a fejlesztési gépén, hogy figyelje a hubról érkező közvetlenmetódus-hívásokat.

Futtassa az alábbi parancsot egy terminálablakban a szimulálteszköz-alkalmazás futtatásához:

```cmd/sh
node SimulatedDevice-3.js "{your device connection string}"
```

Egy parancssori felületi paranccsal hívjon meg egy közvetlen metódust az eszközön:

```azurecli-interactive
az iot hub invoke-device-method --device-id MyTestDevice --method-name TestMethod --timeout 10 --method-payload '{"key":"value"}' --hub-name {YourIoTHubName}
```

A szimulált eszköz a következő üzenetet jeleníti meg a konzolon, amikor egy közvetlenmetódus-hívást fogad:

![A szimulált eszköz egy közvetlenmetódus-hívást fogad](media/tutorial-connectivity/receive-method-call.png)

Ha a szimulált eszköz sikeresen fogadja a közvetlenmetódus-hívást, nyugtázást küld vissza a hubra:

![Közvetlen metódus-visszaigazolás fogadása](media/tutorial-connectivity/method-acknowledgement.png)

## <a name="check-twin-synchronization"></a>Ikerszinkronizálás ellenőrzése

Az eszközök ikreket használnak, hogy szinkronizálják az eszköz és a hub állapotát. Ebben a szakaszban parancssori felületi parancsok használatával küld _kívánt tulajdonságokat_ egy eszközre, és elolvassa az eszköz által küldött _jelentett tulajdonságokat_.

A szakaszban használt szimulált eszköz minden indításkor elküldi a jelentett tulajdonságokat a hubnak, és a kívánt tulajdonságokat a fogadáskor minden alkalommal megjeleníti a konzolon.

Futtassa az alábbi parancsot egy terminálablakban a szimulálteszköz-alkalmazás futtatásához:

```cmd/sh
node SimulatedDevice-3.js "{your device connection string}"
```

Annak ellenőrzéséhez, hogy a hub fogadta-e a jelentett tulajdonságokat az eszközről, használja a következő parancssori felületi parancsot:

```azurecli-interactive
az iot hub device-twin show --device-id MyTestDevice --hub-name {YourIoTHubName}
```

A parancs kimenetében láthatja a **devicelaststarted** tulajdonságot a jelentett tulajdonságok szakaszban. Ez a tulajdonság a szimulált eszköz legutóbbi indításának dátumát és idejét jeleníti meg.

![Jelentett tulajdonságok megtekintése](media/tutorial-connectivity/reported-properties.png)

Annak ellenőrzéséhez, hogy a hub képes-e kívánt tulajdonságok értékeit elküldeni az eszköznek, használja a következő parancssori felületi parancsot:

```azurecli-interactive
az iot hub device-twin update --set properties.desired='{"mydesiredproperty":"propertyvalue"}' --device-id MyTestDevice --hub-name {YourIoTHubName}
```

A szimulált eszköz minden alkalommal megjelenít egy üzenetet, amikor egy kívánt tulajdonság frissítését fogadja a hubról:

![Kívánt tulajdonságok fogadása](media/tutorial-connectivity/desired-properties.png)

Amellett, hogy folyamatosan fogadja a kívánt tulajdonságok módosításait, a szimulált eszköz indításakor automatikusan ellenőrzi a kívánt tulajdonságokat is.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szüksége az IoT Hubra, az erőforráscsoporttal együtt törölje a Portalon. Ehhez válassza ki az IoT Hubot tartalmazó **tutorials-iot-hub-rg** erőforráscsoportot, majd kattintson a **Törlés** gombra.

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban megtudhatta, hogyan ellenőrizhet eszközkulcsokat, az eszközről a felhőbe irányuló kapcsolatot, a felhőből az eszközre irányuló kapcsolatot, valamint az ikereszköz-szinkronizálást. Az IoT hub monitorozásával kapcsolatos további információkért tekintse meg az IoT Hub monitorozását ismertető útmutatót.

> [!div class="nextstepaction"]
> [Az IoT Hub monitorozása](monitor-iot-hub.md)
