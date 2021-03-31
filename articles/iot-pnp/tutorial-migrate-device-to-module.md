---
title: Oktatóanyag – általános Azure IoT-Plug and Play modul összekötése | Microsoft Docs
description: Oktatóanyag – példa C# IoT Plug and Play-eszköz kódjára egy általános modulban.
author: ericmitt
ms.author: ericmitt
ms.date: 9/22/2020
ms.topic: tutorial
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 33eaa1ea928cc0650c91948c70d46daf499f3b4b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "99831204"
---
# <a name="tutorial-connect-an-iot-plug-and-play-module-c"></a>Oktatóanyag: IoT Plug and Play modul összekötése (C#)

Ez az oktatóanyag bemutatja, hogyan csatlakozhat egy általános IoT Plug and Play [modulhoz](../iot-hub/iot-hub-devguide-module-twins.md).

Az eszköz egy IoT Plug and Play eszköz, ha közzéteszi a modell AZONOSÍTÓját, amikor csatlakozik egy IoT hubhoz, és megvalósítja a modell azonosítója által azonosított digitális Twins Definition Language (DTDL) modellben ismertetett tulajdonságokat és metódusokat. Ha többet szeretne megtudni arról, hogy az eszközök hogyan használják a DTDL és a modell AZONOSÍTÓját, olvassa el a következőt: [IoT Plug and Play fejlesztői útmutató](./concepts-developer-guide-device.md). A modulok ugyanúgy használják a modell-azonosítókat és a DTDL modelleket.

A IoT Plug and Play modul megvalósításának bemutatásához az oktatóanyag a következőket mutatja be:

> [!div class="checklist"]
> * Adjon hozzá egy modult a IoT hub-hoz.
> * Alakítsa át a termosztát C#-eszköz mintáját egy általános modulba.
> * Használja a Service SDK-t a modullal való interakcióhoz.

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [iot-pnp-prerequisites](../../includes/iot-pnp-prerequisites.md)]

Az oktatóanyag Windows rendszeren történő elvégzéséhez telepítse a következő szoftvereket a helyi Windows-környezetbe:

* [Visual Studio (Közösség, Professional vagy Enterprise)](https://visualstudio.microsoft.com/downloads/).
* [Git](https://git-scm.com/download/).

Az Azure IoT Explorer eszköz használatával vegyen fel egy **saját modul-eszköz** nevű új eszközt az IoT hub-ba.

Vegyen fel egy **My-Module** nevű modult a **saját modul-eszközre**:

1. Az Azure IoT Explorer eszközében navigáljon a **saját modul-eszköz** eszközre.

1. Válassza ki a **modul identitása** elemet, majd válassza a **+ Hozzáadás** lehetőséget.

1. Adja meg a **saját modult** a modul identitásának neveként, majd válassza a **Mentés** lehetőséget.

1. A modul identitások listájában válassza a **saját modul** lehetőséget. Ezután másolja ki az elsődleges kapcsolatok sztringjét. Az oktatóanyag későbbi részében ezt a modul-csatlakoztatási karakterláncot használja.

1. Válassza ki a **modul iker** fület, és figyelje meg, hogy nincsenek kívánt vagy jelentett tulajdonságok:

    ```json
    {
      "deviceId": "my-module-device",
      "moduleId": "my-module",
      "etag": "AAAAAAAAAAE=",
      "deviceEtag": "NjgzMzQ1MzQ1",
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
      "modelId": "",
      "version": 2,
      "properties": {
        "desired": {
          "$metadata": {
            "$lastUpdated": "0001-01-01T00:00:00Z"
          },
          "$version": 1
          },
          "reported": {
            "$metadata": {
              "$lastUpdated": "0001-01-01T00:00:00Z"
          },
          "$version": 1
        }
      }
    }
    ```

## <a name="download-the-code"></a>A kód letöltése

Ha még nem tette meg, akkor az Azure IoT Hub Device C# SDK GitHub-tárházat a helyi gépre klónozással:

Nyisson meg egy parancssort egy tetszőleges mappában. Használja az alábbi parancsot az [Azure IoT C#-minták](https://github.com/Azure-Samples/azure-iot-samples-csharp) GitHub-tárházának klónozásához a következő helyre:

```cmd
git clone https://github.com/Azure-Samples/azure-iot-samples-csharp.git
```

## <a name="prepare-the-project"></a>A projekt előkészítése

A minta projekt megnyitása és előkészítése:

1. Nyissa meg a *Azure-IOT-SDK-csharp\iot-hub\Samples\device\PnpDeviceSamples\Thermostat\Thermostat.csproj* projektfájlt a Visual Studio 2019-ben.

1. A Visual Studióban navigáljon a **Project > termosztát tulajdonságai > hibakeresés** elemre. Ezután adja hozzá a következő környezeti változókat a projekthez:

    | Name | Érték |
    | ---- | ----- |
    | IOTHUB_DEVICE_SECURITY_TYPE | connectionString |
    | IOTHUB_MODULE_CONNECTION_STRING | A modul-összekapcsolási karakterlánc, amelyet korábban jegyzett készített |

    A minta-konfigurációval kapcsolatos további tudnivalókért tekintse meg a [minta](https://github.com/Azure-Samples/azure-iot-samples-csharp/blob/master/iot-hub/Samples/device/PnpDeviceSamples/readme.md)információit.

## <a name="modify-the-code"></a>Módosítják a kódot

A kód módosítása eszköz helyett modulként való működésre:

1. A Visual Studióban nyissa meg a *paraméter. cs* fájlját, és módosítsa a **PrimaryConnectionString** változót az alábbiak szerint megadó sort:

    ```csharp
    public string PrimaryConnectionString { get; set; } = Environment.GetEnvironmentVariable("IOTHUB_MODULE_CONNECTION_STRING");
    ```

1. A Visual Studióban nyissa meg a *program. cs programot* , és cserélje le az osztály hét példányát `DeviceClient` az `ModuleClient` osztályra.

    > [!TIP]
    > Használja a Visual Studio Search és a Replace funkciót az **egyeztetési esettel** , és a kifejezésre való lecseréléshez engedélyezze a **teljes szót** `DeviceClient` `ModuleClient` .

1. A Visual Studióban nyissa meg a *termosztát. cs* karakterláncot, és cserélje le a osztály mindkét példányát a `DeviceClient` `ModuleClient` következőre:

1. Mentse a módosításokat a módosított fájlokba.

Ha futtatja a kódot, majd az Azure IoT Explorer használatával megtekinti a frissített modult, a frissített eszközt a modell azonosítója és a modul jelentett tulajdonsága látható:

```json
{
  "deviceId": "my-module-device",
  "moduleId": "my-mod",
  "etag": "AAAAAAAAAAE=",
  "deviceEtag": "NjgzMzQ1MzQ1",
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
  "modelId": "dtmi:com:example:Thermostat;1",
  "version": 3,
  "properties": {
    "desired": {
      "$metadata": {
        "$lastUpdated": "0001-01-01T00:00:00Z"
      },
      "$version": 1
    },
    "reported": {
      "maxTempSinceLastReboot": 5,
      "$metadata": {
        "$lastUpdated": "2020-09-28T08:53:45.9956637Z",
        "maxTempSinceLastReboot": {
          "$lastUpdated": "2020-09-28T08:53:45.9956637Z"
        }
      },
      "$version": 2
    }
  }
}
```

## <a name="interact-with-a-device-module"></a>Az eszköz modullal való interakció

A szolgáltatás SDK-k lehetővé teszik a csatlakoztatott IoT Plug and Play eszközök és modulok modell-AZONOSÍTÓjának lekérését. A szolgáltatás SDK-k segítségével az írható tulajdonságok és a hívási parancsok állíthatók be:

1. A Visual Studio egy másik példányában nyissa meg a *Azure-IOT-SDK-csharp\iot-hub\Samples\service\PnpServiceSamples\Thermostat\Thermostat.csproj* projektet.

1. A Visual Studióban navigáljon a **Project > termosztát tulajdonságai > hibakeresés** elemre. Ezután adja hozzá a következő környezeti változókat a projekthez:

    | Name | Érték |
    | ---- | ----- |
    | IOTHUB_DEVICE_ID | saját modul – eszköz |
    | IOTHUB_CONNECTION_STRING | Az érték, amelyet a [környezet beállításakor](set-up-environment.md) jegyzett készített |

    > [!TIP]
    > Az IoT hub-beli kapcsolatok sztringjét az Azure IoT Explorer eszközében is megtalálhatja.

1. Nyissa meg a *program. cs* fájlt, és módosítsa azt a sort, amely a következőképpen hívja meg a parancsot:

    ```csharp
    CloudToDeviceMethodResult result = await s_serviceClient.InvokeDeviceMethodAsync(s_deviceId, "my-module", commandInvocation);
    ```

1. A *program. cs* fájlban módosítsa azt a sort, amely az eszközt a következőképpen kéri le:

    ```csharp
    Twin twin = await s_registryManager.GetTwinAsync(s_deviceId, "my-module");
    ```

1. Győződjön meg arról, hogy a modul ügyféloldali mintája továbbra is fut, majd futtassa ezt a szolgáltatási mintát. A szolgáltatás mintájának kimenete a modell AZONOSÍTÓját mutatja az eszköz Twin és a parancs hívásával:

    ```cmd
    [09/28/2020 10:52:55]dbug: Thermostat.Program[0]
      Initialize the service client.
    [09/28/2020 10:52:55]dbug: Thermostat.Program[0]
      Get Twin model Id and Update Twin
    [09/28/2020 10:52:59]dbug: Thermostat.Program[0]
      Model Id of this Twin is: dtmi:com:example:Thermostat;1
    [09/28/2020 10:52:59]dbug: Thermostat.Program[0]
      Invoke a command
    [09/28/2020 10:53:00]dbug: Thermostat.Program[0]
      Command getMaxMinReport invocation result status is: 200
    ```

    A modul ügyfelének kimenete a parancs kezelőjének válaszát mutatja:

    ```cmd
    [09/28/2020 10:53:00]dbug: Thermostat.ThermostatSample[0]
      Command: Received - Generating max, min and avg temperature report since 28/09/2020 10:52:55.
    [09/28/2020 10:53:00]dbug: Thermostat.ThermostatSample[0]
      Command: MaxMinReport since 28/09/2020 10:52:55: maxTemp=25.4, minTemp=25.4, avgTemp=25.4, startTime=28/09/2020 10:52:56, endTime=28/09/2020 10:52:56
    ```

## <a name="convert-to-an-iot-edge-module"></a>Átalakítás IoT Edge modulba

Ha a mintát úgy szeretné átalakítani, hogy IoT Plug and Play IoT Edge modulként működjön, az alkalmazást tárolóba helyezése kell. Nincs szükség további kód módosítására. A (z) IoT Edge futtatókörnyezet az indításkor befecskendezi a kapcsolatok karakterláncának környezeti változóját. További információ: [a Visual Studio 2019 használata a modulok fejlesztéséhez és hibakereséséhez Azure IoT Edge](../iot-edge/how-to-visual-studio-develop-module.md).

A tároló modul üzembe helyezésének megismeréséhez lásd:

* [Azure IoT Edge futtatása Ubuntu-Virtual Machines](../iot-edge/how-to-install-iot-edge-ubuntuvm.md).
* [Telepítse a Azure IoT Edge futtatókörnyezetet a Debian-alapú Linux rendszereken](../iot-edge/how-to-install-iot-edge.md).

Az Azure IoT Explorer eszköz használatával a következőket tekintheti meg:

* A IoT Edge-eszköz modell-azonosítója a különálló modulban.
* Telemetria a IoT Edge eszközről.
* IoT Edge modul Twin Property frissítései a IoT Plug and Play értesítéseket aktiválják.
* A IoT Edge modul reagál a IoT Plug and Play parancsokra.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [iot-pnp-clean-resources](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban megtanulta, hogyan csatlakoztathat egy IoT Plug and Play-eszközt egy IoT hub-modulhoz. Ha többet szeretne megtudni a IoT Plug and Play eszköz modelljeiről, tekintse meg a következőt:

> [!div class="nextstepaction"]
> [IoT Plug and Play modellezési fejlesztői útmutató](./concepts-developer-guide-device.md)