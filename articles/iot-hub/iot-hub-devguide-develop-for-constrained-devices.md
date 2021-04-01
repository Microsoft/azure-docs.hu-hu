---
title: Az Azure IoT Hub a IoT Hub C SDK-t használó korlátozott eszközök fejlesztéséhez
description: Fejlesztői útmutató – útmutató az Azure IoT SDK-k használatával történő fejlesztéséhez a korlátozott eszközökhöz.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 05/24/2018
ms.author: robinsh
ms.custom:
- amqp
- mqtt
ms.openlocfilehash: 5a43eb2537ebc09ffcb524a4426d7a8c9bec560b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96500002"
---
# <a name="develop-for-constrained-devices-using-azure-iot-c-sdk"></a>Korlátozott eszközök fejlesztése az Azure IoT C SDK használatával

Az Azure IoT Hub C SDK-t ANSI C (C99) nyelven írták, ami lehetővé teszi, hogy a különböző platformokon, kis méretű lemezzel és memóriával is működjön. Az ajánlott RAM legalább 64 KB, de a pontos memória-lábnyom a használt protokolltól, a megnyitott kapcsolatok számától és a megcélzó platformtól függ.
> [!NOTE]
> * Az Azure IoT C SDK rendszeresen közzéteszi az erőforrás-felhasználással kapcsolatos információkat a fejlesztés elősegítése érdekében.  Látogasson el a [GitHub-adattárba](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/c_sdk_resource_information.md) , és tekintse át a legújabb teljesítménytesztet.
>

A C SDK csomag formájában érhető el az apt-get, a NuGet és a MBED. A korlátozott eszközök célzásához érdemes lehet helyileg létrehozni az SDK-t a célként megadott platformon. Ez a dokumentáció bemutatja, hogyan távolíthat el bizonyos szolgáltatásokat a C SDK-nak a [CMAK](https://cmake.org/)használatával történő csökkentése érdekében. A dokumentáció továbbá ismerteti az ajánlott eljárásokat programozási modelleket a korlátozott eszközök használatához.

## <a name="building-the-c-sdk-for-constrained-devices"></a>A C SDK kiépítése a korlátozott eszközökhöz

Hozza létre a C SDK-t a korlátozott eszközökhöz.

> [!NOTE]
> A beágyazott C SDK a saját hálózat használata (BYON) megközelítését támogató korlátozott eszközök alternatívája. A IoT-fejlesztők szabadon hozhatják létre a MQTT-ügyfelet, a TLS-t és a választott szoftvercsatornát az eszköz megoldásához. [További információ a beágyazott C SDK-ról](https://github.com/Azure/azure-sdk-for-c/tree/master/sdk/docs/iot).

### <a name="prerequisites"></a>Előfeltételek

Ezt a [c SDK telepítési útmutatót](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md) követve előkészítheti a fejlesztési környezetet a c SDK kiépítéséhez. Mielőtt megkezdi a CMAK-sel való felépítési lépést, a nem használt funkciók eltávolításához a CMAK-jelzőket hívhatja.

### <a name="remove-additional-protocol-libraries"></a>További protokoll-kódtárak eltávolítása

A C SDK jelenleg öt protokollt támogat: MQTT, MQTT over WebSocket, AMQPs, AMQP over WebSocket és HTTPS. A legtöbb forgatókönyvhöz egy-egy ügyfélen futó két protokoll szükséges, ezért eltávolíthatja az SDK-ból nem használt protokoll-függvénytárat. A forgatókönyvhöz tartozó megfelelő kommunikációs protokoll kiválasztásával kapcsolatos további információkért tekintse meg a [válasszon egy IoT hub kommunikációs protokollt](iot-hub-devguide-protocols.md). Például a MQTT egy egyszerűsített protokoll, amely gyakran alkalmasabb a korlátozott eszközökhöz.

A AMQP és a HTTP-kódtárak a következő CMAK-paranccsal távolíthatók el:

```
cmake -Duse_amqp=OFF -Duse_http=OFF <Path_to_cmake>
```

### <a name="remove-sdk-logging-capability"></a>Az SDK naplózási funkciójának eltávolítása

A C SDK részletes naplózást biztosít a hibakereséshez. Az üzemi eszközök naplózási funkcióját a következő CMAK-parancs használatával távolíthatja el:

```
cmake -Dno_logging=OFF <Path_to_cmake>
```

### <a name="remove-upload-to-blob-capability"></a>Feltöltés eltávolítása a blob-képességbe

Az SDK beépített képességeivel nagyméretű fájlokat tölthet fel az Azure Storage-ba. Az Azure IoT hub egy társított Azure Storage-fiókhoz tartozó kézbesítő működik. Ezt a szolgáltatást a médiafájlok, a nagyméretű telemetria-kötegek és a naplók küldésére használhatja. A [fájlok IoT hub használatával történő feltöltésével](iot-hub-devguide-file-upload.md)kapcsolatban további információhoz juthat. Ha az alkalmazás nem igényli ezt a funkciót, akkor a következő CMAK-paranccsal távolíthatja el ezt a szolgáltatást:

```
cmake -Ddont_use_uploadtoblob=ON <Path_to_cmake>
```

### <a name="running-strip-on-linux-environment"></a>Szalag futtatása Linux-környezetben

Ha a bináris fájlok Linux rendszeren futnak, kihasználhatja a [Strip paranccsal](https://en.wikipedia.org/wiki/Strip_(Unix)) , hogy csökkentse a végső alkalmazás méretét a fordítás után.

```
strip -s <Path_to_executable>
```

## <a name="programming-models-for-constrained-devices"></a>Korlátozott eszközök programozási modelljei

Ezután tekintse meg a korlátozott eszközök programozási modelljeit.

### <a name="avoid-using-the-serializer"></a>Kerülje a szerializáló használatát

A c SDK egy opcionális [c SDK-szerializáló](https://github.com/Azure/azure-iot-sdk-c/tree/master/serializer)rendelkezik, amely lehetővé teszi a deklaratív leképezési táblázatok használatát metódusok és eszközök Twin tulajdonságainak definiálásához. A szerializáló úgy lett kialakítva, hogy leegyszerűsítse a fejlesztést, de a terhelést is növeli, ami nem optimális a korlátozott eszközök esetében. Ebben az esetben érdemes megfontolni a primitív ügyféloldali API-k használatát és a JSON elemzését egy könnyű elemző, például a [plébános](https://github.com/kgabis/parson)használatával.

### <a name="use-the-lower-layer-_ll_"></a>Az alsó réteg (_ll_) használata

A C SDK két programozási modellt támogat. Az egyik készlethez tartozik egy _ll_ Infix rendelkező API-k, amely az alsó rétegre mutat. Ez az API-k kisebb súlyt jelentenek, és nem indítják el a munkavégző szálakat, ami azt jelenti, hogy a felhasználónak manuálisan kell ellenőriznie az ütemezést. Az ügyfél esetében például az _ll_ API-k találhatók ebben a [fejlécben](https://github.com/Azure/azure-iot-sdk-c/blob/master/iothub_client/inc/iothub_device_client_ll.h). 

Az " _ll_ " index nélküli másik API-készletet a kényelmi rétegnek nevezzük, ahol a munkavégző szál automatikusan megpördült. Az eszköz ügyfelének kényelmi rétegének API-jai például a [IoT-ügyfél fejlécének fájljában](https://github.com/Azure/azure-iot-sdk-c/blob/master/iothub_client/inc/iothub_device_client.h)találhatók. Olyan korlátozott eszközök esetében, amelyeknél az egyes extra szálak a rendszererőforrások jelentős hányadát vehetik igénybe, érdemes lehet az _ll_ API-kat használni.

## <a name="next-steps"></a>Következő lépések

További információ az Azure IoT C SDK-architektúráról:
-    [Azure IoT C SDK-forráskód](https://github.com/Azure/azure-iot-sdk-c/)
-    [A C-hez készült Azure IoT eszközoldali SDK bemutatása](iot-hub-device-sdk-c-intro.md)
