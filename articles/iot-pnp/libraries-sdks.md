---
title: IoT Plug and Play kódtárak és SDK-k
description: Információk a IoT-Plug and Play engedélyezett megoldások fejlesztéséhez rendelkezésre álló eszköz-és szolgáltatási könyvtárakról.
author: rido-min
ms.author: rmpablos
ms.date: 07/22/2020
ms.topic: reference
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: 31e06777a2f2e26f6ef546e60fd0bf4428d272c2
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102503810"
---
# <a name="microsoft-sdks-for-iot-plug-and-play"></a>Microsoft SDK-k a IoT Plug and Play

A IoT Plug and Play könyvtárak és SDK-k lehetővé teszik a fejlesztők számára, hogy több platformon különböző programozási nyelvek használatával IoT megoldásokat építsenek. A következő táblázat a kezdéshez segítséget nyújtó mintákra és gyors útmutatóra mutató hivatkozásokat tartalmaz:

## <a name="device-sdks"></a>Eszköz SDK-k

| Nyelv | Csomag | Adattár | Példák | Gyorsútmutató | Referencia |
|---|---|---|---|---|---|
| C – eszköz | [vcpkg 1.3.9](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/setting_up_vcpkg.md) | [GitHub](https://github.com/Azure/azure-iot-sdk-c) | [Példák](https://github.com/Azure/azure-iot-sdk-c/tree/master/iothub_client/samples/pnp) | [Csatlakozás az IoT Hubhoz](quickstart-connect-device.md) | [Referencia](/azure/iot-hub/iot-c-sdk-ref/) |
| .NET – eszköz | [NuGet 1.31.0](https://www.nuget.org/packages/Microsoft.Azure.Devices.Client) | [GitHub](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/) | [Példák](https://github.com/Azure-Samples/azure-iot-samples-csharp/tree/master/iot-hub/Samples/device/PnpDeviceSamples) | [Csatlakozás az IoT Hubhoz](quickstart-connect-device.md) | [Referencia](/dotnet/api/microsoft.azure.devices.client) |
| Java – eszköz | [Maven 1.26.0](https://mvnrepository.com/artifact/com.microsoft.azure.sdk.iot/iot-device-client) | [GitHub](https://github.com/Azure/azure-iot-sdk-java/tree/master/) | [Példák](https://github.com/Azure/azure-iot-sdk-java/tree/master/device/iot-device-samples/pnp-device-sample) | [Csatlakozás az IoT Hubhoz](quickstart-connect-device.md) | [Referencia](/java/api/com.microsoft.azure.sdk.iot.device) |
| Python – eszköz | [pip 2.3.0](https://pypi.org/project/azure-iot-device/) | [GitHub](https://github.com/Azure/azure-iot-sdk-python/tree/master/) | [Példák](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device/samples/pnp) | [Csatlakozás az IoT Hubhoz](quickstart-connect-device.md) | [Referencia](/python/api/azure-iot-device/azure.iot.device) |
| Csomópont – eszköz | [NPM 1.17.2](https://www.npmjs.com/package/azure-iot-device)  | [GitHub](https://github.com/Azure/azure-iot-sdk-node/tree/master/) | [Példák](https://github.com/Azure/azure-iot-sdk-node/tree/master/device/samples/pnp) | [Csatlakozás az IoT Hubhoz](quickstart-connect-device.md) | [Referencia](/javascript/api/azure-iot-device/) |
| Beágyazott C-eszköz | N/A | [GitHub](https://github.com/Azure/azure-sdk-for-c/)| [Példák](howto-use-embedded-c.md#samples) | [A beágyazott C használata](howto-use-embedded-c.md) | N/A

## <a name="service-sdks"></a>Szolgáltatási SDK-k

| Platform  | Csomag | Adattár | Példák | Gyorsútmutató | Referencia |
|---|---|---|---|---|---|
| .NET – IoT Hub szolgáltatás | [NuGet 1.27.1](https://www.nuget.org/packages/Microsoft.Azure.Devices ) | [GitHub](https://github.com/Azure/azure-iot-sdk-csharp) | [Példák](https://github.com/Azure-Samples/azure-iot-samples-csharp/tree/master/iot-hub/Samples/service/PnpServiceSamples) | N/A | [Referencia](/dotnet/api/microsoft.azure.devices) |
| Java – IoT Hub szolgáltatás | [Maven 1.26.0](https://mvnrepository.com/artifact/com.microsoft.azure.sdk.iot/iot-service-client/1.26.0) | [GitHub](https://github.com/Azure/azure-iot-sdk-java) | [Példák](https://github.com/Azure/azure-iot-sdk-java/tree/master/service/iot-service-samples/pnp-service-sample) | N/A | [Referencia](/java/api/com.microsoft.azure.sdk.iot.service) |
| Csomópont-IoT Hub szolgáltatás | [NPM 1.13.0](https://www.npmjs.com/package/azure-iothub) | [GitHub](https://github.com/Azure/azure-iot-sdk-node) | [Példák](https://github.com/Azure/azure-iot-sdk-node/tree/master/service/samples) | N/A | [Referencia](/javascript/api/azure-iothub/) |
| Python – digitális Twins szolgáltatás | [pip 2.2.3](https://pypi.org/project/azure-iot-hub) | [GitHub](https://github.com/Azure/azure-iot-sdk-python) | [Példák](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-hub/samples) | [Interakció a IoT Hub digitális Twins API-val](quickstart-service.md) | N/A |
| Csomópont – digitális Twins szolgáltatás | [NPM 1.13.0](https://www.npmjs.com/package/azure-iot-digitaltwins-service) | [GitHub](https://github.com/Azure/azure-iot-sdk-node) | [Példák](https://github.com/Azure/azure-iot-sdk-node/tree/master/service/samples/javascript) | [Interakció a IoT Hub digitális Twins API-val](quickstart-service.md) | N/A |

## <a name="next-steps"></a>Következő lépések

Az SDK-k és a kódtárak kipróbálásához tekintse meg a  [fejlesztői útmutatót](concepts-developer-guide-device.md) , valamint az [eszköz](quickstart-connect-device.md) rövid útmutatóját és a [szolgáltatás](quickstart-service.md)rövid útmutatóját.