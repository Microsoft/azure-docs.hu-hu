---
title: Oktatóanyag – eszköz belső vezérlőprogram frissítése az Azure IoT Hub használatával | Microsoft Docs
description: Oktatóanyag – Ismerje meg, hogyan valósítható meg az eszköz belső vezérlőprogram-frissítési folyamata, amely az IoT hub-hoz csatlakoztatott háttér-alkalmazásból indítható el.
services: iot-hub
author: wesmc7777
ms.author: wesmc
ms.service: iot-hub
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 06/28/2019
ms.custom:
- mvc
- mqtt
- 'Role: Cloud Development'
- 'Role: IoT Device'
- devx-track-js
- devx-track-azurecli
ms.openlocfilehash: 3fc257ff192ccb1bb05b233c6ac802696ece0054
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "99575721"
---
# <a name="tutorial-implement-a-device-firmware-update-process"></a>Oktatóanyag: Eszközök belsővezérlőprogram-frissítési folyamatának implementálása

Előfordulhat, hogy frissíteni kell az IoT Hubhoz csatlakoztatott eszközök belső vezérlőprogramját. Például előfordulhat, hogy új funkciókat szeretne hozzáadni a belső vezérlőprogramhoz vagy biztonsági javításokat szeretne alkalmazni. Számos IoT-forgatókönyvben nem célszerű fizikailag megkeresni, majd manuálisan alkalmazni az eszközök belsővezérlőprogram-frissítéseit. Ez az oktatóanyag bemutatja, hogyan indítható el és monitorozható távolról a belsővezérlőprogram-frissítési folyamat a Hubhoz csatlakoztatott háttéralkalmazásból.

A belsővezérlőprogram-frissítési folyamat létrehozásához és monitorozásához az oktatóanyagban szereplő háttéralkalmazás létrehoz egy _konfigurációt_ az IoT Hubban. Az IoT Hub [automatikus eszközkezelése](./iot-hub-automatic-device-management.md) ezt a konfigurációt használja az _ikereszköz kívánt tulajdonságainak_ frissítéséhez minden hűtőeszközön. A kívánt tulajdonságok adják meg a szükséges belsővezérlőprogram-frissítés részleteit. Amíg a hűtőeszközök a belsővezérlőprogram-frissítési folyamatot futtatják, jelentést küldenek az állapotukról a háttéralkalmazásnak az _ikereszköz jelentett tulajdonságainak_ használatával. A háttéralkalmazás a konfigurációval monitorozza az eszközről küldött jelentett tulajdonságokat, és nyomon követi a belsővezérlőprogram-frissítési folyamatot a befejezésig:

![A belsővezérlőprogram-frissítési folyamat](media/tutorial-firmware-update/Process.png)

Az oktatóanyagban az alábbi feladatokat fogja végrehajtani:

> [!div class="checklist"]
> * Létrehoz egy IoT Hubot, és hozzáad egy teszteszközt az eszközidentitás-jegyzékhez.
> * Létrehoz egy konfigurációt a belsővezérlőprogram-frissítés meghatározásához.
> * Szimulálja a belsővezérlőprogram-frissítési folyamatot egy eszközön.
> * Állapotfrissítéseket fogad az eszközről a belsővezérlőprogram-frissítés előrehaladtával.

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

Az oktatóanyag teljesítéséhez az Azure-előfizetésnek rendelkeznie kell egy olyan IoT Hubbal, amelyhez tartoznia kell egy, az eszközidentitás-jegyzékhez hozzáadott eszköznek. Az eszköz identitásjegyzékében található bejegyzés teszi lehetővé az oktatóanyagban futtatott szimulált eszköz csatlakozását a központhoz.

Ha az előfizetésében még nincs IoT-központ, a következő CLI-szkripttel beállíthat egyet. Ez a szkript a **tutorial-iot-hub** nevet használja az IoT-központhoz, de futtatáskor a nevet le kell cserélni az Ön által megadott egyedi névre. A szkript az erőforráscsoportot és a központot az **USA középső régiójában** hozza létre, de ez módosítható egy Önhöz közelebb eső régióra. A szkript lekéri az IoT Hub szolgáltatás kapcsolati sztringjét, amellyel a mintául szolgáló háttéralkalmazásban csatlakozhat az IoT Hubhoz:

```azurecli-interactive
hubname=tutorial-iot-hub
location=centralus

# Install the IoT extension if it's not already installed
az extension add --name azure-iot

# Create a resource group
az group create --name tutorial-iot-hub-rg --location $location

# Create a free-tier IoT Hub. You can have only one free IoT Hub per subscription. Free tier hubs can have only 2 partitions.
az iot hub create --name $hubname --location $location --resource-group tutorial-iot-hub-rg --partition-count 2 --sku F1

# Make a note of the service connection string, you need it later
az iot hub connection-string show --name $hubname --policy-name service -o table

```

Ez az oktatóanyag egy **MyFirmwareUpdateDevice** nevű szimulált eszközt használ. Az alábbi szkript hozzáadja ezt az eszközt az eszközidentitás-jegyzékhez, beállít egy címkeértéket, és lekéri a kapcsolati sztringet:

```azurecli-interactive
# Set the name of your IoT hub
hubname=tutorial-iot-hub

# Create the device in the identity registry
az iot hub device-identity create --device-id MyFirmwareUpdateDevice --hub-name $hubname --resource-group tutorial-iot-hub-rg

# Add a device type tag
az iot hub device-twin update --device-id MyFirmwareUpdateDevice --hub-name $hubname --set tags='{"device type":"chiller"}'

# Retrieve the device connection string, you need this later
az iot hub device-identity connection-string show --device-id MyFirmwareUpdateDevice --hub-name $hubname --resource-group tutorial-iot-hub-rg -o table

```

> [!TIP]
> Ha ezeket a parancsokat Windows-parancssorban vagy PowerShell-parancssorban futtatja, tekintse meg az [azure-iot-cli-extension tippek](https://github.com/Azure/azure-iot-cli-extension/wiki/Tips) oldalt a JSON-sztringek idézésével kapcsolatos tudnivalókért.

## <a name="start-the-firmware-update"></a>A belsővezérlőprogram-frissítés elindítása

Hozzon létre egy [automatikus Eszközkezelő-konfigurációt](iot-hub-automatic-device-management.md#create-a-configuration) a háttérbeli alkalmazásban a belső vezérlőprogram frissítési folyamatának elindításához minden eszközön, amely a Chiller **típussal** van megjelölve. Ez a szakasz a következőket ismerteti:

* Konfiguráció létrehozása háttéralkalmazásból.
* A feladat monitorozása a befejeződéséig.

### <a name="use-desired-properties-to-start-the-firmware-upgrade-from-the-back-end-application"></a>A kívánt tulajdonságok használata a belsővezérlőprogram-frissítés elindítására a háttéralkalmazásból

A konfigurációt létrehozó háttéralkalmazásának kódjának megtekintéséhez keresse meg az **iot-hub/Tutorials/FirmwareUpdate** mappát a letöltött Node.js mintaprojektben. Ezután nyissa meg a ServiceClient.js fájlt egy szövegszerkesztőben.

A háttéralkalmazás az alábbi konfigurációt hozza létre:

[!code-javascript[Automatic device management configuration](~/iot-samples-node/iot-hub/Tutorials/FirmwareUpdate/ServiceClient.js?name=configuration "Automatic device management configuration")]

A konfiguráció a következő szakaszokat tartalmazza:

* A `content` megadja a belső vezérlőprogram a kiválasztott eszközöknek küldött, kívánt tulajdonságait.
* A(z) `metrics` megadja a belsővezérlőprogram-frissítés állapotát jelentő lekérdezéseket.
* A(z) `targetCondition` kiválasztja a belsővezérlőprogram-frissítést fogadó eszközöket.
* A(z) `priorty` beállítja a konfiguráció más konfigurációkhoz viszonyított relatív prioritását.

A háttéralkalmazás a következő kódot használja a konfiguráció létrehozásához, amellyel megadhatók a kívánt tulajdonságok:

[!code-javascript[Create configuration](~/iot-samples-node/iot-hub/Tutorials/FirmwareUpdate/ServiceClient.js?name=createConfiguration "Create configuration")]

A konfiguráció létrehozása után az alkalmazás monitorozza a belsővezérlőprogram-frissítést:

[!code-javascript[Monitor firmware update](~/iot-samples-node/iot-hub/Tutorials/FirmwareUpdate/ServiceClient.js?name=monitorConfiguration "Monitor firmware update")]

A konfiguráció két mérőszámtípusról küld jelentést:

* Rendszermérőszámok: jelentik, hány eszköz kapja meg a frissítést és hány eszközre lett alkalmazva a frissítés.
* Egyéni mérőszámok: a konfigurációhoz hozzáadott lekérdezések hozzák létre. Ebben az oktatóanyagban a lekérdezések jelentik, hogy hány eszköz található a frissítési folyamat egyes fázisaiban.

### <a name="respond-to-the-firmware-upgrade-request-on-the-device"></a>Válasz a belső vezérlőprogram frissítési kérelmére az eszközön

A belső vezérlőprogram a háttéralkalmazásból küldött kívánt tulajdonságait kezelő szimulálteszköz-kód megtekintéséhez keresse meg az **iot-hub/Tutorials/FirmwareUpdate** mappát a letöltött Node.js-mintaprojektben. Ezután nyissa meg a SimulatedDevice.js fájlt egy szövegszerkesztőben.

A szimulálteszköz-alkalmazás létrehoz egy kezelőt a **properties.desired.firmware** kívánt tulajdonságok frissítéseihez az ikereszközön. A kezelőben elvégez néhány alapvető ellenőrzést a kívánt tulajdonságokon a frissítési folyamat futtatása előtt:

[!code-javascript[Handle desired property update](~/iot-samples-node/iot-hub/Tutorials/FirmwareUpdate/SimulatedDevice.js?name=initiateUpdate "Handle desired property update")]

## <a name="update-the-firmware"></a>A belső vezérlőprogram frissítése

Az **initiateFirmwareUpdateFlow** függvény futtatja a frissítést. Ez a függvény a **waterfall** függvénnyel futtatja a frissítési folyamat fázisait egymás után. Ebben a példában a belsővezérlőprogram-frissítés négy fázisból áll. Az első fázis letölti a rendszerképet, a második egy ellenőrzőösszeg használatával ellenőrzi, a harmadik fázis alkalmazza a rendszerképet, az utolsó pedig újraindítja az eszközt:

[!code-javascript[Firmware update flow](~/iot-samples-node/iot-hub/Tutorials/FirmwareUpdate/SimulatedDevice.js?name=firmwareupdateflow "Firmware update flow")]

A frissítési folyamat során a szimulált eszköz jelentést készít a folyamatról a jelentett tulajdonságok használatával:

[!code-javascript[Reported properties](~/iot-samples-node/iot-hub/Tutorials/FirmwareUpdate/SimulatedDevice.js?name=reportedProperties "Reported properties")]

A következő kódrészlet a letöltési fázis implementálását mutatja be. A letöltési fázis során a szimulált eszköz a jelentett tulajdonságok használatával állapotinformációkat küld a háttéralkalmazásnak:

[!code-javascript[Download image phase](~/iot-samples-node/iot-hub/Tutorials/FirmwareUpdate/SimulatedDevice.js?name=downloadimagephase "Download image phase")]

## <a name="run-the-sample"></a>Minta futtatása

Ebben a szakaszban két mintaalkalmazás futtatásával figyelhető meg, hogyan hozza létre a háttéralkalmazás a konfigurációt a belsővezérlőprogram-frissítési folyamat kezeléséhez a szimulált eszközön.

A szimulált eszköz és a háttéralkalmazások futtatásához szükség lesz az eszköz és a szolgáltatás kapcsolati sztringjére. Ezt a sztringet az oktatóanyag elején, az erőforrások létrehozásánál jegyezte fel.

A szimulálteszköz-alkalmazás futtatásához nyisson meg egy felületet vagy parancssori ablakot, és lépjen a letöltött Node.js-projektben az **iot-hub/Tutorials/FirmwareUpdate** mappához. Ezután futtassa le a következő parancsokat:

```cmd/sh
npm install
node SimulatedDevice.js "{your device connection string}"
```

A háttéralkalmazás futtatásához nyisson meg egy újabb kezelőfelületet vagy parancssori ablakot. Keresse meg a letöltött Node.js-projektben az **iot-hub/Tutorials/FirmwareUpdate** mappát. Ezután futtassa le a következő parancsokat:

```cmd/sh
npm install
node ServiceClient.js "{your service connection string}"
```

A következő képernyőkép a szimulálteszköz-alkalmazás kimenetét mutatja, és azt, hogyan válaszol a belső vezérlőprogram kívánt tulajdonságinak frissítésére a háttéralkalmazásból:

![Szimulált eszköz](./media/tutorial-firmware-update/SimulatedDevice.png)

A következő képernyőkép a háttéralkalmazás kimenetét mutatja, és kiemeli, hogyan hozza létre a konfigurációt a belső vezérlőprogram kívánt tulajdonságainak frissítéséhez:

![Képernyőkép, amely a háttérbeli alkalmazás kimenetét jeleníti meg.](./media/tutorial-firmware-update/BackEnd1.png)

A következő képernyőkép a háttéralkalmazás kimenetét mutatja, és kiemeli, hogyan monitorozza a belsővezérlőprogram-frissítési mérőszámokat a szimulált eszközről:

![Háttéralkalmazás](./media/tutorial-firmware-update/BackEnd2.png)

Mivel az automatikus eszköz-konfigurációk a létrehozáskor futnak, majd öt percenként nem láthatók a háttérbeli alkalmazásnak elküldett összes állapot-frissítés. A mérőszámokat a portálon is megtekintheti az IoT Hub **Automatikus eszközkezelés -> IoT-eszköz konfigurációja** szakaszában:

![A konfiguráció megtekintése a portálon](./media/tutorial-firmware-update/portalview.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha a következő oktatóanyagot is el kívánja végezni, ne távolítsa el az erőforráscsoportot és az IoT-központot, mert ezeket később újból használhatja.

Ha már nincs szüksége az IoT Hubra, az erőforráscsoporttal együtt törölje a Portalon. Ehhez válassza ki az IoT-központot tartalmazó **tutorial-iot-hub-rg** erőforráscsoportot, majd kattintson a **Törlés** gombra.

Ha szeretné, a CLI-t is használhatja:

```azurecli-interactive
# Delete your resource group and its contents
az group delete --name tutorial-iot-hub-rg
```

## <a name="next-steps"></a>Következő lépések

Ez az oktatóanyag bemutatta, hogyan implementálható a csatlakoztatott eszközök belsővezérlőprogram-frissítési folyamata. Folytassa a következő oktatóanyaggal, amely azt ismerteti, hogyan használható az Azure IoT Hub Portal-eszközök és az Azure CLI-parancsok az eszközök kapcsolatának teszteléséhez.

> [!div class="nextstepaction"]
> [Szimulált eszköz használata az IoT Hub-kapcsolat ellenőrzéséhez](tutorial-connectivity.md)
