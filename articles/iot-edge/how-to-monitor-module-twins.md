---
title: Ikermodulok figyelése – Azure IoT Edge
description: Az ikereszköz és a modulikrek értelmezése a kapcsolat és az állapot meghatározásához.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 05/29/2020
ms.topic: conceptual
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: a5a31e15c88cef588c93f44c8fe5303d930b5b2c
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2021
ms.locfileid: "107479372"
---
# <a name="monitor-module-twins"></a>Ikermodulok monitorozása

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

A Azure IoT Hub ikermodulok lehetővé teszik az üzemelő példányok kapcsolatának és IoT Edge monitorozását. Az ikermodulok hasznos információkat tárolnak az IoT Hubban a futó modulok teljesítményéről. A [IoT Edge ügynök](iot-edge-runtime.md#iot-edge-agent) és a [IoT Edge-modul](iot-edge-runtime.md#iot-edge-hub) az ikermodulokat ( és ) tartja `$edgeAgent` `$edgeHub` fenn:

* `$edgeAgent` A állapot- és kapcsolatadatokat tartalmaz a IoT Edge ügynökről és IoT Edge hub-IoT Edge moduljairól, valamint az egyéni modulokról. A IoT Edge ügynök feladata a modulok üzembe helyezése, monitorozása és a kapcsolati állapot jelentése az Azure IoT Hubnak.
* `$edgeHub` az eszközön futó IoT Edge és az Azure IoT Hub közötti kommunikációval kapcsolatos adatokat tartalmaz. Ez magában foglalja az lefelé irányuló eszközökről érkező bejövő üzenetek feldolgozását is. IoT Edge-központ felelős a Azure IoT Hub és a IoT Edge eszközök és modulok közötti kommunikáció feldolgozásáért.

Az adatok metaadatokba, címkékbe, valamint a modulikrek JSON-szerkezetében található kívánt és jelentett tulajdonságkészletekbe vannak rendezve. A fájlban megadott tulajdonságokat deployment.jsa rendszer az ikermodulba másolja. A IoT Edge ügynök és a IoT Edge központ frissítik a modulok jelentett tulajdonságait.

Hasonlóképpen, az deployment.jsfájlban található egyéni modulokhoz megadott kívánt tulajdonságokat a rendszer átmásolja az ikermodulba, de a megoldás feladata a jelentett tulajdonságértékek biztosítása.

Ez a cikk az ikermodulok áttekintését ismerteti a Azure Portal, az Azure CLI és a Visual Studio Code-ban. Az eszközök központi telepítésének figyelésével kapcsolatos információkért lásd: Figyelés IoT Edge [központi telepítések.](how-to-monitor-iot-edge-deployments.md) A modulikrek fogalmának áttekintését lásd: [Modulikrek](../iot-hub/iot-hub-devguide-module-twins.md)a IoT Hub.

> [!TIP]
> A futásidejű modulok jelentett tulajdonságai elavultak, ha egy IoT Edge az IoT Hubról. A modul [pingelése](how-to-edgeagent-direct-method.md#ping) `$edgeAgent` segítségével megállapíthatja, hogy megszakadt-e a kapcsolat.

## <a name="monitor-runtime-module-twins"></a>Futásidejű ikermodulok figyelése

Az üzembehelyezés csatlakozási problémáinak elhárításához tekintse át a IoT Edge-ügynököt és IoT Edge hub-modul ikermoduljait, majd tekintse át a többi modult.

### <a name="monitor-iot-edge-agent-module-twin"></a>Az IoT Edge ikermodul figyelése

Az alábbi JSON-ban az Visual Studio Code modulikre látható, a `$edgeAgent` legtöbb JSON-szakasz összecsukva.

```json
{
  "deviceId": "Windows109",
  "moduleId": "$edgeAgent",
  "etag": "AAAAAAAAAAU=",
  "deviceEtag": "NzgwNjA1MDUz",
  "status": "enabled",
  "statusUpdateTime": "0001-01-01T00:00:00Z",
  "connectionState": "Disconnected",
  "lastActivityTime": "0001-01-01T00:00:00Z",
  "cloudToDeviceMessageCount": 0,
  "authenticationType": "sas",
  "x509Thumbprint": {
    "primaryThumbprint": null,
    "secondaryThumbprint": null
  },
  "version": 53,
  "properties": {
    "desired": { "···" },
    "reported": {
      "schemaVersion": "1.0",
      "version": { "···" },
      "lastDesiredStatus": { "···" },
      "runtime": { "···" },
      "systemModules": {
        "edgeAgent": { "···" },
        "edgeHub": { "···" }
      },
      "lastDesiredVersion": 5,
      "modules": {
        "SimulatedTemperatureSensor": { "···" }
      },
      "$metadata": { "···" },
      "$version": 48
    }
  }
}
```

A JSON-t a következő szakaszok ismertetik felülről kezdve:

* Metaadatok – Kapcsolati adatokat tartalmaz. Érdekes módon a IoT Edge ügynök kapcsolati állapota mindig leválasztott állapotban van: `"connectionState": "Disconnected"` . A kapcsolat állapota az eszközről a felhőbe (D2C) küldött üzenetekre vonatkozik, és a IoT Edge ügynök nem küld D2C-üzeneteket.
* Tulajdonságok – A és `desired` `reported` alszakaszokat tartalmazza.
* Properties.desired – (összecsukott) Az operátor által a fájlon deployment.jsvárt tulajdonságértékek.
* Properties.reported – A IoT Edge által jelentett legújabb tulajdonságértékek.

A és a szakasz is hasonló struktúrával rendelkezik, és további metaadatokat tartalmaz a sémával, a verzióval és `properties.desired` `properties.reported` a futásidővel kapcsolatos információkhoz. Emellett tartalmazza az egyéni modulok (például ) szakaszát, valamint a és a `modules` `SimulatedTemperatureSensor` futásidejű modulok `systemModules` `$edgeAgent` `$edgeHub` szakaszát is.

Ha összehasonlítja a jelentett tulajdonságértékeket a kívánt értékekkel, meghatározhatja az eltéréseket, és azonosíthatja a leválasztásokat, amelyek segíthetnek a problémák elhárításában. Az összehasonlítások során ellenőrizze a szakaszban jelentett értéket a vizsgált `$lastUpdated` `metadata` tulajdonsághoz.

A következő tulajdonságokat fontos megvizsgálni a hibaelhárításhoz:

* **exitcode** – A nullán kívül bármely érték azt jelzi, hogy a modul leállt egy hiba miatt. A 137-es vagy 143-as hibakódok azonban akkor használatosak, ha egy modul szándékosan leállított állapotra lett beállítva.

* **lastStartTimeUtc** –  A tároló utolsó indítási dátumának dátuma. Ez az érték 0001-01-01T00:00:00Z, ha a tároló nem indult el.

* **lastExitTimeUtc** – Azt a **dateTime-t jeleníti** meg, amely után a tároló utoljára befejeződött. Ez az érték 0001-01-01T00:00:00Z, ha a tároló fut, és soha nem lett leállítva.

* **runtimeStatus** – A következő értékek egyike lehet:

    | Érték | Leírás |
    | --- | --- |
    | Ismeretlen | Alapértelmezett állapot az üzembe helyezés létrejötte előtt. |
    | le- és leépítés | A modul úgy van ütemezve, hogy elindul, de jelenleg nem fut. Ez az érték akkor hasznos, ha egy modul újraindításkor állapotváltozáson megy keresztül. Ha egy sikertelen modul az újraindításra vár a cool-off időszak alatt, a modul le lesz kapcsolva. |
    | Futás | Azt jelzi, hogy a modul jelenleg fut. |
    | Egészségtelen | Azt jelzi, hogy egy állapot-mintavétel ellenőrzése meghiúsult vagy időkorrelált. |
    | Megállt | Azt jelzi, hogy a modul sikeresen kilépt (nulla kilépési kóddal). |
    | sikertelen | Azt jelzi, hogy a modul hiba kilépési kóddal (nem nulla) lépett ki. A modul az érvényben található újraindítási szabályzattól függően vissza tud állni erről az állapotról. Ez az állapot azt jelezheti, hogy a modul nem állítható vissza hibát észlelt. Hiba akkor fordul elő, ha a Microsoft Monitoring Agent (MMA) már nem tudja újrahasználni a modult, ami új üzembe helyezést igényel. |

Részletekért [lásd: EdgeAgent reported properties (Az EdgeAgent jelentett](module-edgeagent-edgehub.md#edgeagent-reported-properties) tulajdonságai).

### <a name="monitor-iot-edge-hub-module-twin"></a>A IoT Edge ikermodul figyelése

Az alábbi JSON-ban a Code Visual Studio ikermodulja látható, a `$edgeHub` legtöbb JSON-szakasz összecsukva.

```json
{
  "deviceId": "Windows109",
  "moduleId": "$edgeHub",
  "etag": "AAAAAAAAAAU=",
  "deviceEtag": "NzgwNjA1MDU2",
  "status": "enabled",
  "statusUpdateTime": "0001-01-01T00:00:00Z",
  "connectionState": "Connected",
  "lastActivityTime": "0001-01-01T00:00:00Z",
  "cloudToDeviceMessageCount": 0,
  "authenticationType": "sas",
  "x509Thumbprint": {
    "primaryThumbprint": null,
    "secondaryThumbprint": null
  },
  "version": 102,
    "properties": {
      "desired": { "···" },
      "reported": {
        "schemaVersion": "1.0",
        "version": { "···" },
      "lastDesiredVersion": 5,
      "lastDesiredStatus": { "···" },
      "clients": {
        "Windows109/SimulatedTemperatureSensor": {
          "status": "Disconnected",
          "lastConnectedTimeUtc": "2020-04-08T21:42:42.1743956Z",
          "lastDisconnectedTimeUtc": "2020-04-09T07:02:42.1398325Z"
        }
      },
      "$metadata": { "···" },
      "$version": 97
    }
  }
}

```

A JSON-t a következő szakaszok ismertetik felülről kezdve:

* Metaadatok – Kapcsolati adatokat tartalmaz.

* Tulajdonságok – A és `desired` `reported` alszakaszokat tartalmazza.
* Properties.desired – (összecsukott) Az operátor által a fájlon deployment.jsvárt tulajdonságértékek.
* Properties.reported – A központ által jelentett IoT Edge értékek.

Ha problémákat tapasztal az lefelé irányuló eszközökkel kapcsolatban, akkor ezeket az adatokat kell megvizsgálnia.

## <a name="monitor-custom-module-twins"></a>Egyéni ikermodulok figyelése

Az egyéni modulok kapcsolati adatai az ügynök ikermodul IoT Edge ban vannak fenntartva. Az egyéni modul ikermodulját elsősorban a megoldás adatainak fenntartására használják. A modul ikermoduljában megjelennek a fájlban deployment.jsmegadott kívánt tulajdonságok, és a modul szükség szerint frissítheti a jelentett tulajdonságértékeket.

A modulikre jelentett tulajdonságértékek frissítéséhez használhatja az ön által előnyben részesített programozási nyelvet az Azure IoT Hub Eszköz-SZOFTVERK-ekkel, a modul alkalmazáskódja alapján. [](../iot-hub/iot-hub-devguide-sdks.md#azure-iot-hub-device-sdks) A következő eljárás a .NET-hez készült Azure SDK-t használja erre a [SimulatedTemperatureSensor modul kódját](https://github.com/Azure/iotedge/blob/dd5be125df165783e4e1800f393be18e6a8275a3/edge-modules/SimulatedTemperatureSensor/src/Program.cs) használva:

1. Hozza létre a [ModuleClient egy példányát](/dotnet/api/microsoft.azure.devices.client.moduleclient) a [CreateFromEnvironmentAysnc metódussal.](/dotnet/api/microsoft.azure.devices.client.moduleclient.createfromenvironmentasync)

1. Szerezze be a modul ikertulajdonságának gyűjteményét a [GetTwinAsync metódussal.](/dotnet/api/microsoft.azure.devices.client.moduleclient.gettwinasync)

1. A [SetDesiredPropertyUpdateCallbackAsync](/dotnet/api/microsoft.azure.devices.client.deviceclient.setdesiredpropertyupdatecallbackasync) metódussal hozzon létre egy figyelőt (visszahívás átadásával), amely a kívánt tulajdonságok módosításait szeretné elérni.

1. A visszahívási metódusban frissítse a moduliker jelentett tulajdonságait az [UpdateReportedPropertiesAsync](/dotnet/api/microsoft.azure.devices.client.moduleclient) metódussal, és adja át a beállítani kívánt tulajdonságértékek [TwinCollection](/dotnet/api/microsoft.azure.devices.shared.twincollection) elemét.

## <a name="access-the-module-twins"></a>Az ikermodulok elérése

A modulikrek JSON-ját a Azure IoT Hub, az Visual Studio Code-ban és az Azure CLI-vel is áttekintheti.

### <a name="monitor-in-azure-iot-hub"></a>Figyelése a Azure IoT Hub

Az ikermodul JSON-ját a következővel lehet megtekinteni:

1. Jelentkezzen be a [Azure Portal,](https://portal.azure.com) és lépjen az IoT Hubra.
1. Válassza **IoT Edge** panel bal oldali menüjében az Újak lehetőséget.
1. A IoT Edge **eszközök lapon** válassza ki az eszköz **eszközazonosítóját** a figyelni kívánt modulokkal.
1. Válassza ki a modul nevét a **Modulok lapon,** majd válassza a modul **ikeridentitása** lehetőséget a felső menüsávon.

  ![Válasszon ki egy ikermodult a Azure Portal](./media/how-to-monitor-module-twins/select-module-twin.png)

Ha a "Modulidentitás nem létezik ehhez a modulhoz" üzenet jelenik meg, ez a hiba azt jelzi, hogy a háttérmegoldás már nem érhető el, amely eredetileg létrehozta az identitást.

### <a name="monitor-module-twins-in-visual-studio-code"></a>Ikermodulok figyelése a Visual Studio Code-ban

Modulikon áttekintése és szerkesztése:

1. Ha még nincs telepítve, telepítse a [Azure IoT Tools Code-Visual Studio](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) bővítményt.
1. Az **Explorerben** bontsa ki **a Azure IoT Hub,** majd bontsa ki az eszközt a figyelni kívánt modullal.
1. Kattintson a jobb gombbal a modulra, és válassza **a Modulikon szerkesztése lehetőséget.** A rendszer letölti a modulikre egy ideiglenes fájlt a számítógépre, és megjelenik a Visual Studio Code-ban.

  ![Modulikre lekért szerkesztése a Visual Studio Code-ban](./media/how-to-monitor-module-twins/edit-module-twin-vscode.png)

Ha módosításokat eszközl, a szerkesztőben található kód felett válassza az **Ikermodul** frissítése lehetőséget a módosítások IoT Hubba való mentéséhez.

  ![Modulikon frissítése a Visual Studio Code-ban](./media/how-to-monitor-module-twins/update-module-twin-vscode.png)

### <a name="monitor-module-twins-in-azure-cli"></a>Ikermodulok figyelése az Azure CLI-ban

Ha meg IoT Edge, hogy fut-e, használja [az az iot hub invoke-module-method](how-to-edgeagent-direct-method.md#ping) metódust a IoT Edge pingel.

Az [az iot hub module-twin](/cli/azure/iot/hub/module-twin) structure az alábbi parancsokat biztosítja:

* **az iot hub module-twin show** – Ikermodul-definíció megjelenítése.
* **az iot hub module-twin update** – Modul ikerdefiníciójának frissítése.
* **az iot hub module-twin replace** – A modul ikerdefiníciójának cseréje cél JSON-re.

## <a name="next-steps"></a>Következő lépések

Ismerje meg, hogyan [kommunikálhat az EdgeAgent-sel beépített közvetlen metódusok használatával.](how-to-edgeagent-direct-method.md)
