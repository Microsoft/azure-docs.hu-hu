---
title: Szolgáltatás-fejlesztői útmutató – IoT Plug and Play | Microsoft Docs
description: A IoT Plug and Play leírása a szolgáltatás-fejlesztőknek
author: dominicbetts
ms.author: dobett
ms.date: 10/01/2020
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
zone_pivot_groups: programming-languages-set-ten
ms.openlocfilehash: a5889be88dfd0870a2eee868c97787ff354cff68
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "104582731"
---
# <a name="iot-plug-and-play-service-developer-guide"></a>IoT Plug and Play Service – fejlesztői útmutató

A IoT Plug and Play lehetővé teszi, hogy intelligens eszközöket hozzon létre, amelyek az Azure IoT alkalmazásaiban hirdetik ki képességeiket. A IoT Plug and Play-eszközökhöz nincs szükség manuális konfigurálásra, ha az ügyfél a Plug and Play-kompatibilis alkalmazások IoT csatlakoztatja őket.

A IoT Plug and Play lehetővé teszi, hogy olyan eszközöket használjon, amelyeken bejelentette a modell AZONOSÍTÓját az IoT hub-ban. Az eszköz tulajdonságait és parancsait például közvetlenül elérheti.

Ha az IoT hubhoz csatlakoztatott Plug and Play IoT-eszközt szeretne használni, akkor a IoT Service SDK-k egyike:

## <a name="service-sdks"></a>Szolgáltatási SDK-k

Használja az Azure IoT Service SDK-kat a megoldásban az eszközök és modulok használatához. Használhatja például a szolgáltatás SDK-kat a Twin tulajdonságok olvasására és frissítésére, valamint a parancsok meghívására. A támogatott nyelvek közé tartozik a C#, a Java, a Node.js és a Python.

A szolgáltatás SDK-k lehetővé teszik, hogy egy megoldásról, például egy asztali vagy webalkalmazásból férhessenek hozzá az eszköz adataihoz. A szolgáltatás SDK-k két névteret és objektummodell-modellt tartalmaznak, amelyekkel lekérheti a modell AZONOSÍTÓját:

- IOT hub szolgáltatás ügyfelének. Ez a szolgáltatás a modell AZONOSÍTÓját a Device Twin tulajdonságként teszi elérhetővé.

- Digitális Twins-ügyfél. Az új digitális Twins API a [digitális Twins Definition Language (DTDL)](concepts-digital-twin.md) modellen alapuló szerkezeteket (például összetevőket, tulajdonságokat és parancsokat) üzemeltet. A digitális Twin API-k megkönnyítik a megoldás-építők számára, hogy IoT Plug and Play megoldásokat hozzanak létre.

:::zone pivot="programming-language-csharp"

[!INCLUDE [iot-pnp-service-devguide-csharp](../../includes/iot-pnp-service-devguide-csharp.md)]

:::zone-end

:::zone pivot="programming-language-java"

[!INCLUDE [iot-pnp-service-devguide-java](../../includes/iot-pnp-service-devguide-java.md)]

:::zone-end

:::zone pivot="programming-language-javascript"

[!INCLUDE [iot-pnp-service-devguide-node](../../includes/iot-pnp-service-devguide-node.md)]

:::zone-end

:::zone pivot="programming-language-python"

[!INCLUDE [iot-pnp-service-devguide-python](../../includes/iot-pnp-service-devguide-python.md)]

:::zone-end

## <a name="next-steps"></a>Következő lépések

Most, hogy megismerte az eszközök modellezését, íme néhány további erőforrás:

- [A digitális ikerpéldányok definíciós nyelve (DTDL)](https://github.com/Azure/opendigitaltwins-dtdl)
- [C eszköz SDK](/azure/iot-hub/iot-c-sdk-ref/)
- [IoT REST API](/rest/api/iothub/device)
- [IoT Plug and Play modellezési útmutató](concepts-modeling-guide.md)
- [A DTDL authoring Tools telepítése és használata](howto-use-dtdl-authoring-tools.md)