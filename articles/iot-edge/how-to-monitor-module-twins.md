---
title: Modulokat figyelő ikrek – Azure IoT Edge
description: A kapcsolat és az állapot megállapítása az eszközök ikrek és a modulok összekapcsolásával.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 05/29/2020
ms.topic: conceptual
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 0b7013979199eefa873a651d99e87dc8b2c47856
ms.sourcegitcommit: 5f32f03eeb892bf0d023b23bd709e642d1812696
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/12/2021
ms.locfileid: "103201602"
---
# <a name="monitor-module-twins"></a>Ikermodulok monitorozása

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

Az Azure-ban található modulok IoT Hub lehetővé teszik a IoT Edge üzemelő példányok kapcsolatának és állapotának figyelését. Az ikrek modul hasznos információkat tárol a IoT hub-ban a futó modulok teljesítményéről. A [IoT Edge ügynök](iot-edge-runtime.md#iot-edge-agent) és a [IoT Edge hub](iot-edge-runtime.md#iot-edge-hub) futtatókörnyezeti modulok mindegyike megtartja a modul ikreket, `$edgeAgent` illetve a `$edgeHub` következőket:

* `$edgeAgent` a IoT Edge ügynökkel és IoT Edge hub futtatókörnyezeti modulokkal és az egyéni modulokkal kapcsolatos állapot-és kapcsolati információkat tartalmazza. A IoT Edge ügynök feladata a modulok üzembe helyezése, monitorozása és a kapcsolatok állapotának jelentése az Azure IoT hub-ban.
* `$edgeHub` az eszközön és az Azure IoT hub-on futó IoT Edge hub közötti kommunikációval kapcsolatos adatokat tartalmaz. Ez magában foglalja az alárendelt eszközökről érkező üzenetek feldolgozását. IoT Edge hub feladata az Azure IoT Hub és a IoT Edge eszközök és modulok közötti kommunikáció feldolgozása.

Az adatok metaadatokba, címkékbe, valamint a kívánt és jelentett tulajdonságokra vannak rendezve a modul ikrek JSON-struktúrájában. A deployment.jsfájlon megadott kívánt tulajdonságokat a rendszer az ikrek modulba másolja. A IoT Edge ügynök és a IoT Edge hub mindegyike frissíti a modulok jelentett tulajdonságait.

Hasonlóképpen, az egyéni modulokhoz megadott kívánt tulajdonságokat a rendszer a (z) deployment.jsfájlba másolja a modulba, de a megoldás feladata a jelentett tulajdonságértékek megadása.

Ez a cikk bemutatja, hogyan tekintheti át a modul ikreket a Azure Portalban, az Azure CLI-ben és a Visual Studio Code-ban. További információ arról, hogy az eszközök Hogyan kapják meg az üzemelő példányokat, lásd: IoT Edge üzemelő [példányok](how-to-monitor-iot-edge-deployments.md)monitorozása. Az ikrek modul koncepciójának áttekintését lásd: a [modulban található ikrek megismerése és használata IoT hub](../iot-hub/iot-hub-devguide-module-twins.md).

> [!TIP]
> Egy futásidejű modul jelentett tulajdonságai elavultak lehetnek, ha egy IoT Edge eszköz le lesz választva az IoT hub-ról. A modul [pingelésével](how-to-edgeagent-direct-method.md#ping) `$edgeAgent` megállapíthatja, hogy a kapcsolat megszakadt-e.

## <a name="monitor-runtime-module-twins"></a>A monitor futtatókörnyezeti moduljának ikrek

Az üzembe helyezés kapcsolódási problémáinak elhárításához tekintse át a IoT Edge Agent és IoT Edge hub Runtime Module ikreket, majd a többi modulra.

### <a name="monitor-iot-edge-agent-module-twin"></a>Figyelő IoT Edge Agent modul Twin

A következő JSON a `$edgeAgent` Visual Studio Code-ban található Twin modult mutatja, és a legtöbb JSON-szakasz összecsukott állapotban van.

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

A JSON a következő szakaszokban látható:

* Metaadatok – a kapcsolódási adatokat tartalmazza. Érdekes módon a IoT Edge ügynök kapcsolati állapota mindig leválasztott állapotban van: `"connectionState": "Disconnected"` . A kapcsolódási állapot az eszközről a felhőbe (D2C) érkező üzenetekre vonatkozik, és a IoT Edge ügynök nem küld D2C üzeneteket.
* Tulajdonságok – a `desired` és `reported` alszakaszokat tartalmazza.
* Properties. desired – a várt tulajdonság-értékeket az operátor határozza meg a fájl deployment.js.
* Tulajdonságok. jelentett – IoT Edge ügynök által jelentett legújabb tulajdonságértékek.

A `properties.desired` és a `properties.reported` rész is hasonló struktúrával rendelkezik, és további metaadatokat tartalmaz a séma, a verzió és a futtatókörnyezet adataihoz. Emellett az `modules` Egyéni modulok (például) szakasza `SimulatedTemperatureSensor` , valamint a `systemModules` `$edgeAgent` és a `$edgeHub` futásidejű modulok szakasza is szerepel.

A jelentett tulajdonságértékek a kívánt értékekkel való összehasonlításával meghatározhatja az eltéréseket, és azonosíthatja a leválasztásokat, amelyek segíthetnek a hibák elhárításában. Az összehasonlítások végrehajtása során tekintse `$lastUpdated` át a jelentett értéket a `metadata` vizsgálni kívánt tulajdonság szakaszában.

A következő tulajdonságok fontosak a hibaelhárításhoz:

* **ExitCode** – a nullától eltérő érték azt jelzi, hogy a modul hibával leállt. Azonban a 137-es vagy a 143-es hibakódok akkor használatosak, ha egy modult szándékosan leállított állapotra állítottak be.

* **lastStartTimeUtc** – **megjeleníti a tároló** utolsó indításának dátumát. Ez az érték 0,001-01-01T00:00:00Z, ha a tároló nem indult el.

* **lastExitTimeUtc** – megjeleníti a tároló utolsó befejezésének **datetime** értékét. Ez az érték 0,001-01-01T00:00:00Z, ha a tároló fut, és soha nem állt le.

* **runtimeStatus** – a következő értékek egyike lehet:

    | Érték | Leírás |
    | --- | --- |
    | ismeretlen | Az alapértelmezett állapot, amíg a telepítés nem jön létre. |
    | leállítási | A modul indításra van ütemezve, de jelenleg nem fut. Ez az érték akkor hasznos, ha az újraindításkor a modul állapota megváltozik. Ha egy hibás modul a leállási időszakban újraindításra vár, a modul leállítási állapotba kerül. |
    | fut | Azt jelzi, hogy a modul jelenleg fut. |
    | sérült | Azt jelzi, hogy az állapot-mintavételi vizsgálat sikertelen vagy időtúllépés miatt megszakadt. |
    | megállt | Azt jelzi, hogy a modul sikeresen kilépett (nulla kilépési kóddal). |
    | sikertelen | Azt jelzi, hogy a modul kilépett a hiba kilépési kódjával (nem nulla). A modul visszatérhet a leállítási az adott állapotból attól függően, hogy milyen újraindítási házirend van érvényben. Ez az állapot azt jelezheti, hogy a modul helyreállíthatatlan hibát észlelt. Hiba akkor fordul elő, ha a Microsoft monitoring Agent (MMA) már nem tudja felépíteni a modult új központi telepítés megkövetelésével. |

A részletekért lásd: [EdgeAgent jelentett tulajdonságai](module-edgeagent-edgehub.md#edgeagent-reported-properties) .

### <a name="monitor-iot-edge-hub-module-twin"></a>IoT Edge hub-modul (Twin) figyelése

A következő JSON a `$edgeHub` Visual Studio Code-ban található Twin modult mutatja, és a legtöbb JSON-szakasz összecsukott állapotban van.

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

A JSON a következő szakaszokban látható:

* Metaadatok – a kapcsolódási adatokat tartalmazza.

* Tulajdonságok – a `desired` és `reported` alszakaszokat tartalmazza.
* Properties. desired – a várt tulajdonság-értékeket az operátor határozza meg a fájl deployment.js.
* Tulajdonságok. jelentett – IoT Edge hub által jelentett legújabb tulajdonságértékek.

Ha problémákat tapasztal az alsóbb rétegbeli eszközökkel kapcsolatban, érdemes lehet megvizsgálni, hogy az ilyen típusú eszközök elindulnak-e.

## <a name="monitor-custom-module-twins"></a>Egyéni modulok figyelése – ikrek

Az egyéni modulok kapcsolatával kapcsolatos információkat a IoT Edge Agent modul Twin. Az egyéni modulhoz tartozó külön modult elsősorban a megoldáshoz tartozó adatkezeléshez használják. A deployment.jsfájlon megadott kívánt tulajdonságok a különálló modulban szerepelnek, és a modul szükség szerint frissítheti a jelentett tulajdonságértékeket.

Az [Azure IoT hub Device SDK](../iot-hub/iot-hub-devguide-sdks.md#azure-iot-hub-device-sdks) -k használatával az előnyben részesített programozási nyelvet használhatja a modulban szereplő, a modulhoz tartozó alkalmazás kódjának megfelelően frissíteni kívánt tulajdonságértékek frissítéséhez. Az alábbi eljárás a .NET-hez készült Azure SDK-t használja erre a [SimulatedTemperatureSensor](https://github.com/Azure/iotedge/blob/dd5be125df165783e4e1800f393be18e6a8275a3/edge-modules/SimulatedTemperatureSensor/src/Program.cs) modul kódjának használatával:

1. Hozza létre a [ModuleClient](/dotnet/api/microsoft.azure.devices.client.moduleclient) egy példányát a [CreateFromEnvironmentAysnc](/dotnet/api/microsoft.azure.devices.client.moduleclient.createfromenvironmentasync) metódussal.

1. Szerezze be a modul Twin tulajdonságainak gyűjteményét a [GetTwinAsync](/dotnet/api/microsoft.azure.devices.client.moduleclient.gettwinasync) metódussal.

1. Hozzon létre egy figyelőt (visszahívás átadásával) a kívánt tulajdonságok a [SetDesiredPropertyUpdateCallbackAsync](/dotnet/api/microsoft.azure.devices.client.deviceclient.setdesiredpropertyupdatecallbackasync) metódussal való megváltoztatásához.

1. A visszahívási módszernél frissítse a [UpdateReportedPropertiesAsync](/dotnet/api/microsoft.azure.devices.client.moduleclient) metódussal rendelkező modulban található jelentett tulajdonságokat, és adja át a beállítani kívánt [TwinCollection](/dotnet/api/microsoft.azure.devices.shared.twincollection) .

## <a name="access-the-module-twins"></a>Az ikrek modul elérése

Áttekintheti a JSON-modult az Azure IoT Hub, a Visual Studio Code és az Azure CLI használatával.

### <a name="monitor-in-azure-iot-hub"></a>Figyelő az Azure IoT Hub

A modulhoz tartozó JSON megtekintéséhez:

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com) , és navigáljon az IoT hubhoz.
1. A bal oldali ablaktábla menüjében válassza a **IoT Edge** lehetőséget.
1. A **IoT Edge eszközök** lapon válassza ki az eszköz **azonosítóját** a figyelni kívánt modulokkal.
1. Válassza ki a modul nevét a **modulok** lapon, majd válassza ki a **modul identitása Twin** elemet a felső menüsorból.

  ![Válassza ki a Azure Portal megtekinteni kívánt modult](./media/how-to-monitor-module-twins/select-module-twin.png)

Ha "A modul identitása nem létezik ehhez a modulhoz" üzenet jelenik meg, ez a hiba azt jelzi, hogy a háttérbeli megoldás már nem érhető el, amely eredetileg létrehozta az identitást.

### <a name="monitor-module-twins-in-visual-studio-code"></a>A Visual Studio Code-ban található ikrek figyelése

Két modul áttekintéséhez és szerkesztéséhez:

1. Ha még nincs telepítve, telepítse az [Azure IoT Tools bővítményt](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) a Visual Studio Code-hoz.
1. Az **Explorerben** bontsa ki az **Azure IoT hub** elemet, majd bontsa ki az eszközt a figyelni kívánt modullal.
1. Kattintson a jobb gombbal a modulra, és válassza a **modul-dupla szerkesztés** lehetőséget. A rendszer letölti a különálló modul ideiglenes fájlját a számítógépre, és megjeleníti a Visual Studio Code-ban.

  ![Modul beszerzése a Visual Studio Code-ban való szerkesztéshez](./media/how-to-monitor-module-twins/edit-module-twin-vscode.png)

Ha módosítja a módosításokat, válassza a szerkesztőben a kód fölötti **modul frissítése** elemet a IoT hub módosításainak mentéséhez.

  ![Két modul frissítése a Visual Studio Code-ban](./media/how-to-monitor-module-twins/update-module-twin-vscode.png)

### <a name="monitor-module-twins-in-azure-cli"></a>Modul ikrek monitorozása az Azure CLI-ben

Ha szeretné megtekinteni, hogy a IoT Edge fut-e, használja az az [IoT hub meghívó-Module-Method](how-to-edgeagent-direct-method.md#ping) parancsot az IoT Edge-ügynök pingeléséhez.

Az az [IOT hub Module-Twin](/cli/azure/ext/azure-iot/iot/hub/module-twin) Structure a következő parancsokat biztosítja:

* **az IOT hub Module-Twin show** – modul kettős definíciójának megjelenítése.
* **az IOT hub Module-Twin Update** – modul kettős definíciójának frissítése.
* **az IOT hub Module-Twin replace** -Replace a Module Twin definition with a TARGET JSON.

## <a name="next-steps"></a>Következő lépések

Ismerje meg, hogyan [kommunikálhat a EdgeAgent a beépített közvetlen metódusok használatával](how-to-edgeagent-direct-method.md).
