---
title: IoT Plug and Play jelenlegi kiadás | Microsoft Docs
description: Megtudhatja, mit tartalmaz a jelenlegi IoT Plug and Play kiadás.
author: dominicbetts
ms.author: dobett
ms.date: 10/01/2020
ms.topic: overview
ms.custom: mvc
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 7b6669ca55dc9b94dc5d702e54b42011120b8812
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "99831875"
---
# <a name="what-is-in-the-current-iot-plug-and-play-release"></a>Mi a jelenlegi IoT Plug and Play kiadás?

Ez a cikk az aktuális IoT Plug and Play kiadását támogató eszközöket, SDK-kat és API-kat foglalja össze. A megjelenített verziószámok azt jelzik, hogy az időponthoz tartozó IoT Plug and Play általánosan elérhetővé vált. A verziók száma a kiadás után megnövelhető.

## <a name="modeling-language"></a>Modellezési nyelv

[Digitális Twins Definition Language (DTDL) v2](https://github.com/Azure/opendigitaltwins-dtdl).

Ha többet szeretne megtudni arról, hogy a IoT Plug and Play-eszközök hogyan működnek a DTDL-mel, tekintse meg a [IoT Plug and Play konvenciókat](concepts-convention.md).

## <a name="tools-and-utilities"></a>Eszközök és segédprogramok

- Az Azure IoT Explorer 0.12.0.

    További információt az [Azure IoT Explorer telepítése és használata](howto-use-iot-explorer.md)című témakörben talál.

- VS Code Extension 1.0.0.

    További információ: [install and use The DTDL authoring Tools](howto-use-dtdl-authoring-tools.md).

- Visual Studio 2019 kiterjesztés 1.0.0.

    További információ: [install and use The DTDL authoring Tools](howto-use-dtdl-authoring-tools.md).

- Azure CLI-IoT bővítmény 0.10.0.

    Az Azure IoT bővítmény olyan parancsokat tartalmaz, amelyek segítségével hitelesítheti az eszközöket. Lásd: `az iot product -h`.

## <a name="libraries-and-sdks"></a>Kódtárak és SDK-k

További információ a kódtárak és SDK-k használatáról: [Microsoft SDK-k a IoT Plug and Play](libraries-sdks.md).

- C Device SDK [vcpkg 1.3.9](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/setting_up_vcpkg.md)
- Beágyazott C Device SDK [GitHub](https://github.com/Azure/azure-sdk-for-c/)
- .NET-eszközök SDK- [NuGet 1.31.0](https://www.nuget.org/packages/Microsoft.Azure.Devices.Client)
- Java-eszköz SDK [Maven 1.26.0](https://mvnrepository.com/artifact/com.microsoft.azure.sdk.iot/iot-device-client)
- Python Device SDK [pip 2.3.0](https://pypi.org/project/azure-iot-device/)
- Node.js Device SDK [NPM 1.17.2](https://www.npmjs.com/package/azure-iot-device)
- .NET – IoT Hub szolgáltatás [NuGet 1.27.1](https://www.nuget.org/packages/Microsoft.Azure.Devices )
- Java – IoT Hub Service [Maven 1.26.0](https://mvnrepository.com/artifact/com.microsoft.azure.sdk.iot/iot-service-client/1.26.0)
- Node.js-IoT Hub Service [NPM 1.13.0](https://www.npmjs.com/package/azure-iothub)
- Python-IoT Hub/digitális Twins szolgáltatás [pip 2.2.3](https://pypi.org/project/azure-iot-hub)
- DTDL Model Parser [NuGet](https://www.nuget.org/packages/Microsoft.Azure.DigitalTwins.Parser).

## <a name="rest-apis"></a>REST API-k

REST API [2020-09-30](/rest/api/iothub).

További információ: [IoT Plug and Play fejlesztői útmutató](concepts-developer-guide-service.md).

## <a name="iot-hub"></a>IoT Hub

A IoT Plug and Play az összes régióban IoT Hub támogatja. A IoT Plug and Play csak a standard vagy az ingyenes szint IoT-hubok támogatják.

## <a name="announcements"></a>Közlemények

Az aktuális és az előző IoT Plug and Play hirdetményekben tekintse meg a következő blogbejegyzéseket:

- [Nyilvános előzetes verzió frissítése (kifüggesztett augusztus 29., 2020)](https://techcommunity.microsoft.com/t5/internet-of-things/add-quot-plug-and-play-quot-to-your-iot-solutions/ba-p/1548531)
- [Készítse elő és tanúsítsa az eszközeit a IoT Plug and Play (közzétették: augusztus 26., 2020)](https://azure.microsoft.com/blog/prepare-and-certify-your-devices-for-iot-plug-and-play/)
- [A IoT Plug and Play már előzetes verzióban érhető el (közzétették: augusztus 22., 2019)](https://azure.microsoft.com/blog/iot-plug-and-play-is-now-available-in-preview/)
- [Build az Azure IoT Central és a IoT Plug and Play (2019. május 7.)](https://azure.microsoft.com/blog/build-with-azure-iot-central-and-iot-plug-and-play/)

## <a name="next-steps"></a>Következő lépések

A javasolt következő lépés a [IoT Plug and Playének áttekintése?](overview-iot-plug-and-play.md).