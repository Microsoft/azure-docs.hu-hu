---
author: dominicbetts
ms.author: dobett
ms.service: iot-pnp
ms.topic: include
ms.date: 11/20/2020
ms.openlocfilehash: b7b0cfa20257ad07d8418c39af68724d613adf41
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2021
ms.locfileid: "107821009"
---
Ez a rövid útmutató bemutatja, hogyan építhet ki egy IoT Plug and Play-eszközalkalmazást, hogyan csatlakoztathatja azt az IoT Hubhoz, és hogyan használhatja az Azure IoT Explorer eszközt az által küldött telemetria megtekintéséhez. A mintaalkalmazás C# nyelven íródott, és szerepel a C# Azure IoT-mintáiban. A megoldáskészítők az Azure IoT Explorer eszközzel megértheti az IoT Plug and Play-eszközök képességeit anélkül, hogy eszközkódot kellene megtekinteniük.

[![Kód tallózása](../articles/iot-central/core/media/common/browse-code.svg)](https://github.com/Azure-Samples/azure-iot-samples-csharp/tree/master/iot-hub/Samples/device/PnpDeviceSamples/Thermostat)

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [iot-pnp-prerequisites](iot-pnp-prerequisites.md)]

A windowsos rövid útmutató befejezéséhez a következő szoftvernek kell telepítve a fejlesztői gépen:

* [Visual Studio (Community, Professional vagy Enterprise)](https://visualstudio.microsoft.com/downloads/).
* [Git](https://git-scm.com/download/): .

## <a name="download-the-code"></a>A kód letöltése

Ebben a rövid útmutatóban előkészít egy fejlesztési környezetet, amely segítségével klónozhatja és elkészítheti az Azure IoT-mintákat a C#-adattárhoz.

Nyisson meg egy parancssort egy ön által választott mappában. Az alábbi parancs futtatásával klónozza [a Microsoft Azure IoT-mintákat a C# (.NET)](https://github.com/Azure-Samples/azure-iot-samples-csharp) GitHub-adattárba a következő helyre:

```cmd
git clone  https://github.com/Azure-Samples/azure-iot-samples-csharp.git
```

## <a name="build-the-code"></a>A kód létrehozása

Most már felépítheti a mintát a Visual Studio és futtathatja hibakeresési módban.

1. Nyissa meg az *azure-iot-samples-csharp\iot-hub\Samples\device\PnpDeviceSamples\Thermostat\Thermostat.csproj* projektfájlt a 2019 Visual Studio ban.

1. A Visual Studio a Debug (Hibakeresés) **> a Project > (Termosztát > tulajdonságai) elemre.** Ezután adja hozzá a következő környezeti változókat a projekthez:

    | Name | Érték |
    | ---- | ----- |
    | IOTHUB_DEVICE_SECURITY_TYPE | Dps |
    | IOTHUB_DEVICE_DPS_ENDPOINT | global.azure-devices-provisioning.net |
    | IOTHUB_DEVICE_DPS_ID_SCOPE | Az érték, amelyről feljegyzett a Környezet [beállítása folyamat befejezésekor](../articles/iot-pnp/set-up-environment.md) |
    | IOTHUB_DEVICE_DPS_DEVICE_ID | my-pnp-device |
    | IOTHUB_DEVICE_DPS_DEVICE_KEY | Az érték, amelyről feljegyzett a Környezet [beállítása folyamat befejezésekor](../articles/iot-pnp/set-up-environment.md) |

Most már felépítheti a mintát a Visual Studio futtathatja hibakeresési módban.

## <a name="run-the-device-sample"></a>Az eszközminta futtatása

A Windowson Visual Studio kódvégrehajtás követéséhez adjon hozzá egy töréspontot a függvényhez a `main` program.cs fájlban.

Az eszköz most már készen áll a parancsok és a tulajdonságfrissítések fogadására, és megkezdte a telemetriai adatok küldését a hubra. A következő lépésekben tartsa futni a mintát.

## <a name="use-azure-iot-explorer-to-validate-the-code"></a>A kód érvényesítése az Azure IoT Explorerrel

Az eszköz ügyfélmintája elindulás után az Azure IoT Explorer eszközzel ellenőrizze, hogy működik-e.

[!INCLUDE [iot-pnp-iot-explorer.md](iot-pnp-iot-explorer.md)]

## <a name="review-the-code"></a>A kód áttekintése

Ez a minta egy egyszerű IoT-Plug and Play implementálja. A minta által implement használt modell nem használ IoT-Plug and Play [összetevőket.](../articles/iot-pnp/concepts-modeling-guide.md) A Digital Twins eszköz [definíciós nyelvének (DTDL)](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/Thermostat.json) modellfájlja határozza meg az eszköz által implementált telemetriai adatokat, tulajdonságokat és parancsokat.

Az eszközkód a standard metódussal csatlakozik az IoT `CreateFromConnectionString` Hubhoz. Az eszköz elküldi a kapcsolatkérésben implementált DTDL-modell modellazonosítóját. A modellazonosítót elküldő eszköz egy IoT-Plug and Play eszköz:

```csharp
private static void InitializeDeviceClientAsync()
{
  var options = new ClientOptions
  {
    ModelId = ModelId,
  };
  s_deviceClient = DeviceClient.CreateFromConnectionString(s_deviceConnectionString, TransportType.Mqtt, options);
  s_deviceClient.SetConnectionStatusChangesHandler((status, reason) =>
  {
     s_logger.LogDebug($"Connection status change registered - status={status}, reason={reason}.");
  });
}
```

A modellazonosító a kódban van tárolva, az alábbi kódrészletben látható módon:

```csharp
private const string ModelId = "dtmi:com:example:Thermostat;1";
```

A tulajdonságokat frissülő, a parancsokat kezelő és a telemetriát továbbító kód megegyezik egy olyan eszköz kódján, amely nem használja az IoT Plug and Play konvencióit.

A minta egy JSON-kódtárat használ az IoT Hubról küldött hasznos adatban lévő JSON-objektumok elemezéséhez:

```csharp
using Newtonsoft.Json;

...

DateTime since = JsonConvert.DeserializeObject<DateTime>(request.DataAsJson);
```
