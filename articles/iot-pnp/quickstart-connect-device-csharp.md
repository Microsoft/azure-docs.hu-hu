---
title: IoT csatlakoztatása Plug and Play C#-eszköz kódja a IoT Hubhoz | Microsoft Docs
description: Hozzon létre és futtasson IoT Plug and Play minta-eszköz kódját olyan Windows rendszeren, amely egy IoT hubhoz csatlakozik. Az Azure IoT Explorer eszköz használatával megtekintheti az eszköz által a hubhoz továbbított adatokat.
author: ericmitt
ms.author: ericmitt
ms.date: 07/14/2020
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: da1ac77ad2716abf964c835634d049895fe88654
ms.sourcegitcommit: 2e72661f4853cd42bb4f0b2ded4271b22dc10a52
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/14/2020
ms.locfileid: "92044547"
---
# <a name="quickstart-connect-a-sample-iot-plug-and-play-device-application-running-on-windows-to-iot-hub-c"></a>Gyors útmutató: Windows rendszeren futó IoT Plug and Play-eszköz csatlakoztatása IoT Hub (C#)

[!INCLUDE [iot-pnp-quickstarts-device-selector.md](../../includes/iot-pnp-quickstarts-device-selector.md)]

Ebből a rövid útmutatóból megtudhatja, hogyan hozhat létre IoT Plug and Play-alkalmazást, hogyan csatlakoztathatja az IoT hubhoz, és az Azure IoT Explorer eszköz használatával megtekintheti az általa küldött telemetria. A minta alkalmazás C# nyelven íródott, és a C#-hoz készült Azure IoT-minták tartalmazzák. A megoldás-szerkesztő az Azure IoT Explorer eszköz használatával képes értelmezni egy IoT Plug and Play eszköz képességeit anélkül, hogy meg kellene tekintenie az eszköz kódját.

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [iot-pnp-prerequisites](../../includes/iot-pnp-prerequisites.md)]

A rövid útmutató Windows rendszeren való elvégzéséhez a következő szoftverekre van szükség a fejlesztői gépen:

* [Visual Studio (Közösség, Professional vagy Enterprise)](https://visualstudio.microsoft.com/downloads/).
* [Git](https://git-scm.com/download/).

## <a name="download-the-code"></a>A kód letöltése

Ebben a rövid útmutatóban egy fejlesztési környezetet készít elő, amellyel klónozott és felépítheti az Azure IoT-mintákat a C#-tárházhoz.

Nyisson meg egy parancssort egy tetszőleges mappában. Futtassa az alábbi parancsot a [c# (.net) GitHub-tárház Microsoft Azure IoT-mintáinak](https://github.com/Azure-Samples/azure-iot-samples-csharp) klónozásához a következő helyre:

```cmd
git clone  https://github.com/Azure-Samples/azure-iot-samples-csharp.git
```

## <a name="build-the-code"></a>A kód létrehozása

Most már létrehozhatja a mintát a Visual Studióban, és hibakeresési módban futtathatja.

1. Nyissa meg a *Azure-IOT-Samples-csharp\iot-hub\Samples\device\PnpDeviceSamples\Thermostat\Thermostat.csproj* projektfájlt a Visual Studio 2019-ben.

1. A Visual Studióban navigáljon a **Project > termosztát tulajdonságai > hibakeresés**elemre. Ezután adja hozzá a következő környezeti változókat a projekthez:

    | Név | Érték |
    | ---- | ----- |
    | IOTHUB_DEVICE_SECURITY_TYPE | DPS |
    | IOTHUB_DEVICE_DPS_ENDPOINT | global.azure-devices-provisioning.net |
    | IOTHUB_DEVICE_DPS_ID_SCOPE | Az érték, amelyet a [környezet beállításakor](set-up-environment.md) jegyzett készített |
    | IOTHUB_DEVICE_DPS_DEVICE_ID | saját PnP-eszköz |
    | IOTHUB_DEVICE_DPS_DEVICE_KEY | Az érték, amelyet a [környezet beállításakor](set-up-environment.md) jegyzett készített |

Most már létrehozhatja a mintát a Visual Studióban, és hibakeresési módban futtathatja.

## <a name="run-the-device-sample"></a>Az eszköz mintájának futtatása

A Visual Studióban a Windowson a kód végrehajtásának nyomon követéséhez adjon hozzá egy töréspontot a `main` függvényhez a program.cs fájlban.

Az eszköz most már készen áll a parancsok és a tulajdonságok frissítéseinek fogadására, és megkezdte a telemetria adatok küldését a központba. A következő lépések elvégzése közben tartsa a mintát.

## <a name="use-azure-iot-explorer-to-validate-the-code"></a>A kód érvényesítése az Azure IoT Explorer használatával

Az ügyfél mintájának elindítása után az Azure IoT Explorer eszköz használatával ellenőrizze, hogy működik-e.

[!INCLUDE [iot-pnp-iot-explorer.md](../../includes/iot-pnp-iot-explorer.md)]

## <a name="review-the-code"></a>A kód áttekintése

Ez a példa egy egyszerű IoT Plug and Play termosztátos eszközt implementál. A minta által megvalósított modell nem használ IoT Plug and Play [összetevőket](concepts-components.md). A [termosztátos eszközhöz tartozó digitális Twins Definition Language (DTDL) modell](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/Thermostat.json) az eszköz által megvalósított telemetria, tulajdonságokat és parancsokat határozza meg.

Az eszköz kódja a standard metódus használatával csatlakozik az IoT hubhoz `CreateFromConnectionString` . Az eszköz elküldi a DTDL modell AZONOSÍTÓját a kapcsolatkérelem számára. Egy IoT Plug and Play eszköz a modell AZONOSÍTÓját küldő eszköz:

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

A modell AZONOSÍTÓját a kód a következő kódrészletben látható módon tárolja:

```csharp
private const string ModelId = "dtmi:com:example:Thermostat;1";
```

A tulajdonságokat frissítő, a parancsokat kezelő és a telemetria küldő kód megegyezik egy olyan eszköz kódjával, amely nem használja a IoT Plug and Play konvencióit.

A minta egy JSON-függvénytárat használ a JSON-objektumok elemzéséhez az IoT hub által eljuttatott hasznos adatokban:

```csharp
using Newtonsoft.Json;

...

DateTime since = JsonConvert.DeserializeObject<DateTime>(request.DataAsJson);
```

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban megtanulta, hogyan csatlakoztathat egy IoT Plug and Play-eszközt egy IoT hubhoz. Ha többet szeretne megtudni arról, hogyan hozhat létre olyan megoldást, amely együttműködik a IoT Plug and Play eszközökkel, tekintse meg a következőt:

> [!div class="nextstepaction"]
> [Útmutató: Kapcsolódás az eszközhöz](./quickstart-service-node.md)