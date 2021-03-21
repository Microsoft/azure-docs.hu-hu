---
title: Az Azure IoT SDK-k ismertetése | Microsoft Docs
description: Fejlesztői útmutató – a különböző Azure IoT-eszközökre és-szolgáltatásokra mutató hivatkozásokat tartalmaz, amelyek segítségével eszközöket és háttérbeli alkalmazásokat hozhat létre.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/14/2020
ms.custom:
- mqtt
- 'Role: IoT Device'
- 'Role: Cloud Development'
ms.openlocfilehash: d35535c87ca20bfc573995bf15f79bc149619776
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102611589"
---
# <a name="understand-and-use-azure-iot-hub-sdks"></a>Az Azure IoT Hub SDK-k ismertetése és használata

A szoftverfejlesztői készletek (SDK-k) két kategóriába sorolhatók a IoT Hub:

* A **IoT hub eszköz SDK** -k lehetővé teszik a IoT-eszközökön futó alkalmazások kiépítését az ügyfél vagy a modul ügyfélprogramjának használatával. Ezek az alkalmazások telemetriát küldenek az IoT Hubnak, és opcionálisan üzeneteket, feladatokat, metódusokat vagy ikereszköz-frissítéseket fogadhatnak az IoT Hubból. Ezekkel az SDK-k használatával az [Azure IoT Plug and Play](../iot-pnp/overview-iot-plug-and-play.md) -konvenciókat és-modelleket használó eszközöket hozhat létre, hogy meghirdesse képességeiket a Plug and Play-kompatibilis alkalmazások IoT. A modul-ügyfél használatával [Azure IoT Edge futtatókörnyezethez](../iot-edge/about-iot-edge.md)is készíthet [modulokat](../iot-edge/iot-edge-modules.md) .

* A **IoT hub Service SDK** -k lehetővé teszik, hogy háttérbeli alkalmazásokat építsen ki az IoT hub felügyeletéhez, és szükség esetén üzeneteket küldjön, feladatokat ütemezzen, közvetlen metódusokat kérjen, vagy a kívánt tulajdonságokat a IoT-eszközökre vagy-modulokra küldje el.

Emellett az [eszközök kiépítési szolgáltatásával](../iot-dps/about-iot-dps.md)való együttműködéshez is biztosítunk SDK-kat.

* Az **eszköz SDK** -k kiépítése lehetővé teszi a IoT-eszközökön futó alkalmazások kiépítését az eszköz kiépítési szolgáltatásával való kommunikációra.

* A **kiépítési szolgáltatás SDK** -k lehetővé teszik, hogy háttérbeli alkalmazásokat építsen ki a regisztrációk kezeléséhez az eszköz kiépítési szolgáltatásában.

Ismerje meg az [Azure IoT SDK-k használatával történő fejlesztés előnyeit](https://azure.microsoft.com/blog/benefits-of-using-the-azure-iot-sdks-in-your-azure-iot-solution/).

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

## <a name="os-platform-and-hardware-compatibility"></a>Operációs rendszer platformja és hardveres kompatibilitása

Az SDK-k által támogatott platformok az [Azure IoT SDK-platform támogatásában](iot-hub-device-sdk-platform-support.md)találhatók.

Az SDK-kompatibilitással kapcsolatos további információkért tekintse meg az [Azure Certified for IoT Device Catalog](https://catalog.azureiotsolutions.com/) vagy az egyes Tárházak című témakört.

## <a name="azure-iot-hub-device-sdks"></a>Azure IoT Hub eszköz SDK-k

A Microsoft Azure IoT Device SDK-k olyan kódot tartalmaznak, amely megkönnyíti az Azure IoT Hub Services által felügyelt és az által kezelt alkalmazások kiépítése.

Azure IoT Hub-eszköz SDK a .NET-hez: 

* Letöltés a [NuGet](https://www.nuget.org/packages/Microsoft.Azure.Devices.Client/).  A névtér a Microsoft. Azure. Devices. clients, amely IoT Hub eszköz-ügyfeleket (DeviceClient, ModuleClient) tartalmaz.
* [Forráskód](https://github.com/Azure/azure-iot-sdk-csharp)
* [API-leírások](/dotnet/api/microsoft.azure.devices)
* [Modul-hivatkozás](/dotnet/api/microsoft.azure.devices.client.moduleclient)


Azure IoT Hub-eszköz SDK a Embedded C-hez (ANSI C-C99):
* [A beágyazott C SDK létrehozása](https://github.com/Azure/azure-sdk-for-c/tree/master/sdk/docs/iot#build)
* [Forráskód](https://github.com/Azure/azure-sdk-for-c)
* [Méretezési diagram](https://github.com/Azure/azure-sdk-for-c/tree/master/sdk/docs/iot#size-chart) a korlátozott eszközökhöz.
* [API-leírások](https://azuresdkdocs.blob.core.windows.net/$web/dotnet/Azure.Identity/1.0.0/api/index.html)


Azure IoT Hub Device SDK C-hez (ANSI C-C99):

* Telepítés [apt-get, MBED, ARDUINO ide vagy iOS](https://github.com/Azure/azure-iot-sdk-c/blob/master/readme.md#packages-and-libraries) rendszerből
* [Forráskód](https://github.com/Azure/azure-iot-sdk-c)
* [A C Device SDK fordítása](https://github.com/Azure/azure-iot-sdk-c/blob/master/iothub_client/readme.md#compiling-the-c-device-sdk)
* [API-leírások](/azure/iot-hub/iot-c-sdk-ref/)
* [Modul-hivatkozás](/azure/iot-hub/iot-c-sdk-ref/iothub-module-client-h)
* [A C SDK portolása más platformokra](https://github.com/Azure/azure-c-shared-utility/blob/master/devdoc/porting_guide.md)
* [Fejlesztői dokumentáció](https://github.com/Azure/azure-iot-sdk-c/tree/master/doc) a fordítással, a különböző platformokon való ismerkedéssel kapcsolatos információkért stb.
* [Azure IoT Hub C SDK erőforrás-felhasználási információk](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/c_sdk_resource_information.md)

A Javához készült Azure IoT Hub Device SDK:

* Hozzáadás a [Maven](https://github.com/Azure/azure-iot-sdk-java/blob/master/doc/java-devbox-setup.md#for-the-device-sdk) projekthez
* [Forráskód](https://github.com/Azure/azure-iot-sdk-java)
* [API-leírások](/java/api/com.microsoft.azure.sdk.iot.device)
* [Modul-hivatkozás](/java/api/com.microsoft.azure.sdk.iot.device.moduleclient)

Az Azure IoT Hub eszközoldali SDK for Node.js:

* Telepítés a [NPM](https://www.npmjs.com/package/azure-iot-device)
* [Forráskód](https://github.com/Azure/azure-iot-sdk-node)
* [API-leírások](/javascript/api/azure-iot-device/)
* [Modul-hivatkozás](/javascript/api/azure-iot-device/moduleclient)

A Pythonhoz készült Azure IoT Hub Device SDK:

* Telepítés a [pip](https://pypi.org/project/azure-iot-device/) -ből
* [Forráskód](https://github.com/Azure/azure-iot-sdk-python)
* [API-leírások](/python/api/azure-iot-device)

Azure IoT Hub iOS-eszközökhöz készült SDK:

* Telepítés a [CocoaPod](https://cocoapods.org/pods/AzureIoTHubClient)
* [Példák](https://github.com/Azure-Samples/azure-iot-samples-ios)
* API-hivatkozás: lásd a [C API-referenciát](/azure/iot-hub/iot-c-sdk-ref/)

## <a name="azure-iot-hub-service-sdks"></a>Azure IoT Hub Service SDK-k

Az Azure IoT Service SDK-k olyan kódot tartalmaznak, amely megkönnyíti az olyan alkalmazások létrehozását, amelyek az eszközök és a biztonság kezeléséhez IoT Hub közvetlenül kommunikálnak.

Azure IoT Hub Service SDK a .NET-hez:

* Letöltés a [NuGet](https://www.nuget.org/packages/Microsoft.Azure.Devices/).  A névtér a Microsoft. Azure. Devices, amely IoT Hub szolgáltatás-ügyfeleket (RegistryManager, ServiceClients) tartalmaz.
* [Forráskód](https://github.com/Azure/azure-iot-sdk-csharp)
* [API-leírások](/dotnet/api/microsoft.azure.devices)

A Javához készült Azure IoT Hub Service SDK:

* Hozzáadás a [Maven](https://github.com/Azure/azure-iot-sdk-java/blob/master/doc/java-devbox-setup.md#for-the-service-sdk) projekthez
* [Forráskód](https://github.com/Azure/azure-iot-sdk-java)
* [API-leírások](/java/api/com.microsoft.azure.sdk.iot.service)

Azure IoT Hub Service SDK a Node.jshoz:

* Letöltés a [NPM](https://www.npmjs.com/package/azure-iothub)
* [Forráskód](https://github.com/Azure/azure-iot-sdk-node)
* [API-leírások](/javascript/api/azure-iothub/)

Azure IoT Hub Service SDK a Pythonhoz:

* Letöltés a [pip](https://pypi.python.org/pypi/azure-iot-hub/) -ből
* [Forráskód](https://github.com/Azure/azure-iot-sdk-python/tree/master)
* [API-leírások](/python/api/azure-iot-hub)

Azure IoT Hub Service SDK a C-hez:

A C-hez készült Azure IoT Service SDK már nem aktív fejlesztés alatt áll.
Továbbra is kijavítjuk a kritikus hibákat, például az összeomlásokat, az adatsérüléseket és a biztonsági réseket. NEM adunk hozzá új funkciót, vagy kijavítjuk azokat a hibákat, amelyek nem kritikus fontosságúak.

Az Azure IoT Service SDK támogatása magasabb szintű nyelveken ([C#](https://github.com/Azure/azure-iot-sdk-csharp), [Java](https://github.com/Azure/azure-iot-sdk-java), [Node](https://github.com/Azure/azure-iot-sdk-node), [Python](https://github.com/Azure/azure-iot-sdk-python)) érhető el.

* Letöltés [apt-get, MBED, ARDUINO ide vagy NuGet](https://github.com/Azure/azure-iot-sdk-c/blob/master/readme.md)
* [Forráskód](https://github.com/Azure/azure-iot-sdk-c)

Azure IoT Hub Service SDK iOS rendszerhez:

* Telepítés a [CocoaPod](https://cocoapods.org/pods/AzureIoTHubServiceClient)
* [Példák](https://github.com/Azure-Samples/azure-iot-samples-ios)

> [!NOTE]
> Tekintse meg a GitHub-Tárházak readme-fájljait a nyelvi és platform-specifikus csomagkezelő használatával a bináris fájlok és függőségek telepítéséhez a fejlesztői gépen.

## <a name="microsoft-azure-provisioning-sdks"></a>Microsoft Azure SDK-k üzembe helyezése

Az **Microsoft Azure kiépítési SDK** -k lehetővé teszik az eszközök kiépítését a IoT hub az [eszköz kiépítési szolgáltatásával](../iot-dps/about-iot-dps.md).

Azure-beli kiépítési eszköz és szolgáltatás SDK-k C#-hoz:

* Töltse le az [eszköz SDK](https://www.nuget.org/packages/Microsoft.Azure.Devices.Provisioning.Client/) -ból és a [Service SDK](https://www.nuget.org/packages/Microsoft.Azure.Devices.Provisioning.Service/) -ból a NuGet-ból.
* [Forráskód](https://github.com/Azure/azure-iot-sdk-csharp/)
* [API-leírások](/dotnet/api/microsoft.azure.devices.provisioning.client)

Azure-beli kiépítési eszköz és szolgáltatás SDK-k a C-hez:

* Telepítés [apt-get, MBED, ARDUINO ide vagy iOS](https://github.com/Azure/azure-iot-sdk-c/blob/master/readme.md#packages-and-libraries) rendszerből
* [Forráskód](https://github.com/Azure/azure-iot-sdk-c/blob/master/provisioning_client)
* [API-leírások](/azure/iot-hub/iot-c-sdk-ref/)

Azure-beli üzembe helyezési eszköz és szolgáltatás SDK-k Javához:

* Hozzáadás a [Maven](https://github.com/Azure/azure-iot-sdk-java/blob/master/doc/java-devbox-setup.md#for-the-service-sdk) projekthez
* [Forráskód](https://github.com/Azure/azure-iot-sdk-java/blob/master/provisioning)
* [API-leírások](/java/api/com.microsoft.azure.sdk.iot.provisioning.device)

Azure-beli kiépítési eszköz és szolgáltatás SDK-k Node.jshoz:

* [Forráskód](https://github.com/Azure/azure-iot-sdk-node/tree/master/provisioning)
* [API-leírások](/javascript/api/overview/azure/iothubdeviceprovisioning)
* Az eszközoldali [SDK](https://badge.fury.io/js/azure-iot-provisioning-device) és a [Service SDK](https://badge.fury.io/js/azure-iot-provisioning-service) letöltése a NPM-ből

Azure-beli kiépítési eszköz és szolgáltatás SDK-k a Pythonhoz:

* [Forráskód](https://github.com/Azure/azure-iot-sdk-python)
* Az [eszköz SDK](https://pypi.org/project/azure-iot-device/) és a [Service SDK](https://pypi.org/project/azure-iothub-provisioningserviceclient/) letöltése a pip-ből

## <a name="next-steps"></a>Következő lépések

Az Azure IoT SDK-k olyan eszközöket is biztosítanak, amelyek segítik a fejlesztést:

* [iothub-Diagnostics](https://github.com/Azure/iothub-diagnostics): többplatformos parancssori eszköz a IoT Hubsal való kapcsolattal kapcsolatos problémák diagnosztizálásához.
* [Azure-IOT-Explorer](https://github.com/Azure/azure-iot-explorer): többplatformos asztali alkalmazás a IoT hubhoz való kapcsolódáshoz, valamint az IOT-eszközökkel való hozzáadáshoz/felügyelethez/kommunikációhoz.

Az Azure IoT SDK-k használatával történő fejlesztéshez kapcsolódó releváns docs:

* Ismerje meg [, hogyan kezelheti a kapcsolatot és a megbízható üzenetkezelést](iot-hub-reliability-features-in-sdks.md) az IoT hub SDK-k használatával.
* Ismerje meg, hogyan [fejleszthet a mobil platformokra](iot-hub-how-to-develop-for-mobile-devices.md) , például az iOS-re és az Androidra.
* [Azure IoT SDK-platform támogatása](iot-hub-device-sdk-platform-support.md)

A IoT Hub Fejlesztői útmutatóban szereplő további témakörök a következők:

* [IoT Hub-végpontok](iot-hub-devguide-endpoints.md)
* [Az ikrek, a feladatok és az üzenet-útválasztás IoT Hub lekérdezési nyelve](iot-hub-devguide-query-language.md)
* [Kvóták és szabályozás](iot-hub-devguide-quotas-throttling.md)
* [IoT Hub MQTT-támogatás](iot-hub-mqtt-support.md)
* [IoT Hub REST API-hivatkozás](/rest/api/iothub/)
