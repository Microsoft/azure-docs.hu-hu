---
title: A Microsoft OPC-közzétevő konfigurálása
description: Ebből az oktatóanyagból megtudhatja, hogyan konfigurálhatja az OPC-közzétevőt önálló módban.
author: jehona-m
ms.author: jemorina
ms.service: industrial-iot
ms.topic: tutorial
ms.date: 3/22/2021
ms.openlocfilehash: 4d4f9c90fd96365216480164f29f08fad92eb9d0
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "104787713"
---
# <a name="tutorial-configure-the-opc-publisher"></a>Oktatóanyag: az OPC-közzétevő konfigurálása

Ebben az oktatóanyagban az OPC-közzétevő konfigurációjával kapcsolatos információk találhatók. Több interfész is használható a konfigurálásához.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Az OPC-közzétevő konfigurálása konfigurációs fájl használatával
> * Az OPC-közzétevő konfigurálása parancssori argumentumok használatával
> * Az OPC-közzétevő konfigurálása IoT Hub közvetlen metódusok használatával
> * Az OPC-közzétevő konfigurálása felhőalapú, társ REST-szolgáltatás használatával

## <a name="configuring-security"></a>Biztonság konfigurálása

A IoT Edge az OPC-közzétevőt az IoT Hub automatikus elérésére szolgáló biztonsági konfigurációval biztosítja. Az OPC-közzétevő önálló Docker-tárolóként is futtatható egy eszköz-hozzáférési karakterlánc megadásával a IoT Hub eléréséhez a `dc` parancssori paraméter használatával. A rendszer létrehoz egy eszközt a IoT Hubhoz, és a kapcsolati karakterláncot a Azure Portal használatával kérdezi le.

Az OPC UA-kompatibilis eszközök eléréséhez X. 509 tanúsítványokat és a hozzájuk társított titkos kulcsokat az OPC UA használja. Ezt az OPC ua-alkalmazás-hitelesítésnek, valamint az OPC UA felhasználói hitelesítésen kívül kell megnevezni. Az OPC-közzétevő fájlrendszer alapú tanúsítványtárolót használ az összes alkalmazás tanúsítványának kezeléséhez. Indításkor az OPC-közzétevő ellenőrzi, hogy van-e olyan tanúsítvány, amely használható ebben a tanúsítványtárolóban, és létrehoz egy új önaláírt tanúsítványt és egy új társított titkos kulcsot, ha nincs ilyen. Az önaláírt tanúsítványok gyenge hitelesítést biztosítanak, mivel azokat nem megbízható hitelesítésszolgáltató írta alá, de legalább az OPC UA-kompatibilis eszközkel való kommunikáció is titkosítható.

A biztonsági beállítás engedélyezve van a konfigurációs fájlban a `"UseSecurity": true,` jelzőn keresztül. Az OPC UA-kiszolgálókon elérhető legbiztonságosabb végpont automatikusan ki van választva az OPC-közzétevőhöz való csatlakozáshoz.
Alapértelmezés szerint az OPC-közzétevő névtelen felhasználói hitelesítést használ (a fent ismertetett alkalmazás-hitelesítésen felül). Az OPC-közzétevő azonban a felhasználónévvel és jelszóval is támogatja a felhasználói hitelesítést. Ezt megadhatja a REST API konfigurációs felületen (lásd alább) vagy a konfigurációs fájlt a következőképpen:
```
"OpcAuthenticationMode": "UsernamePassword",
"OpcAuthenticationUsername": "usr",
"OpcAuthenticationPassword": "pwd",
```
Emellett az OPC-közzétevő 2,5-es és újabb verziója titkosítja a felhasználónevet és a jelszót a konfigurációs fájlban. A 2,6-es és újabb verzió csak a felhasználónevet és a jelszót támogatja egyszerű szöveges formátumban. Ez az OPC-közzétevő következő verziójában fog javulni.

Ha meg szeretné őrizni az OPC-közzétevő biztonsági konfigurációját az újraindítások között, a tanúsítványtároló könyvtárában található tanúsítványt és titkos kulcsot le kell képezni a IoT Edge gazda operációsrendszer-fájlrendszerre. Lásd a fenti "tároló-létrehozási beállítások megadása a Azure Portalban" című részt.

## <a name="configuration-via-configuration-file"></a>Konfigurálás konfigurációs fájlon keresztül

Az OPC-közzétevő konfigurálásának legegyszerűbb módja egy konfigurációs fájlon keresztül történik. Az ebben a tárházban található fájlon keresztül egy példaként szolgáló konfigurációs fájl, valamint a hozzá tartozó formátumra vonatkozó dokumentáció is elérhető [`publishednodes.json`](https://raw.githubusercontent.com/Azure/iot-edge-opc-publisher/master/opcpublisher/publishednodes.json) .
A konfigurációs fájl szintaxisa módosult az idő múlásával, és az OPC-közzétevő továbbra is képes a régi formátumok olvasására, de a konfiguráció megőrzésekor a legújabb formátumra alakítja át őket.

Egy alapszintű konfigurációs fájl a következőképpen néz ki:
```
[
  {
    "EndpointUrl": "opc.tcp://testserver:62541/Quickstarts/ReferenceServer",
    "UseSecurity": true,
    "OpcNodes": [
      {
        "Id": "i=2258",
        "OpcSamplingInterval": 2000,
        "OpcPublishingInterval": 5000,
        "DisplayName": "Current time"
      }
    ]
  }
]
```

Az OPC UA-eszközök a hálózati sávszélességet úgy optimalizálja, hogy az adatok megváltozásakor csak az OPC-közzétevő adatváltozásait küldi el. Ha az adatváltozásokat gyakrabban vagy rendszeres időközönként közzé kell tenni, az OPC-közzétevő minden olyan konfigurált adatelem esetében támogatja a szívverést, amely a HeartbeatInterval kulcsának az adatelem konfigurációjában való megadásával is engedélyezhető. Az intervallum másodpercben van megadva:
```
 "HeartbeatInterval": 3600,
```

Az OPC UA-eszköz mindig az adattétel aktuális értékét küldi el, amikor az OPC-közzétevő először csatlakozik hozzá. Ha meg szeretné akadályozni, hogy az ilyen típusú adatközzététel IoT Hub, a SkipFirst kulcs az adatelem konfigurációjában is megadható:
```
 "SkipFirst": true,
```

Mindkét beállítás globálisan is engedélyezhető parancssori kapcsolók használatával.

## <a name="configuration-via-command-line-arguments"></a>Konfigurálás parancssori argumentumok használatával

Több parancssori argumentum is használható az OPC-közzétevő globális beállításainak megadására. Ezekről [itt](reference-command-line-arguments.md)olvashat.


## <a name="configuration-via-the-built-in-opc-ua-server-interface"></a>Konfigurálás a beépített OPC UA-kiszolgáló felületén keresztül

>[!NOTE] 
> Ez a funkció csak az OPC-közzétevő 2,5-es és újabb verzióiban érhető el. * *

Az OPC-közzétevő egy beépített OPC UA-kiszolgálóval rendelkezik, amely az 62222-as porton fut. Három OPC UA-metódust valósít meg:

  - PublishNode
  - UnpublishNode
  - GetPublishedNodes

Ez az interfész egy OPC UA-ügyfélalkalmazás használatával érhető el, például: [ua Expert](https://www.unified-automation.com/products/development-tools/uaexpert.html).

## <a name="configuration-via-iot-hub-direct-methods"></a>Konfigurálás IoT Hub közvetlen metódusok használatával

>[!NOTE] 
> Ez a funkció csak az OPC-közzétevő 2,5-es és újabb verzióiban érhető el. * *

Az OPC-közzétevő a következő [IoT hub közvetlen metódusokat](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-direct-methods)valósítja meg, amelyek meghívhatók egy alkalmazásból (a világ bármely pontján) a [IoT hub Device SDK](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-sdks)kihasználása révén:

  - PublishNodes
  - UnpublishNodes
  - UnpublishAllNodes
  - GetConfiguredEndpoints
  - GetConfiguredNodesOnEndpoint
  - GetDiagnosticInfo
  - GetDiagnosticLog
  - GetDiagnosticStartupLog
  - ExitApplication
  - GetInfo

Rendelkezünk egy [minta konfigurációs alkalmazással](https://github.com/Azure-Samples/iot-edge-opc-publisher-nodeconfiguration) , valamint egy alkalmazással, amely az OPC-közzétevő nyílt forráskódú, a felületet kihasználó [diagnosztikai információk olvasására](https://github.com/Azure-Samples/iot-edge-opc-publisher-diagnostics) szolgál.

## <a name="configuration-via-cloud-based-companion-rest-microservice"></a>Konfigurálás felhőalapú, társ-REST-szolgáltatáson keresztül

>[!NOTE] 
> Ez a funkció csak az OPC-közzétevő 2,6-es és újabb verzióiban érhető el.

A REST-illesztőfelülettel rendelkező felhőalapú, kiegészítő webszolgáltatás leírását és elérhetőségét [itt](https://github.com/Azure/Industrial-IoT/blob/master/docs/services/publisher.md)találja. Az OPC-közzétevőt OpenAPI-kompatibilis felületen keresztül is konfigurálhatja, például a hencegés használatával.

## <a name="configuration-of-the-simple-json-telemetry-format-via-separate-configuration-file"></a>Az egyszerű JSON-telemetria formátumának konfigurációja külön konfigurációs fájl használatával

>[!NOTE] 
> Ez a funkció csak az OPC-közzétevő 2,5-es és újabb verzióiban érhető el.

Az OPC-közzétevő lehetővé teszi a nem szabványosított, egyszerű telemetria-formátum részeinek szűrését egy külön konfigurációs fájlon keresztül, amely a TC parancssori kapcsolón keresztül adható meg. Ha nincs megadva konfigurációs fájl, a rendszer elküldi a teljes JSON-telemetria formátumot IoT Hub. A külön telemetria konfigurációs fájl formátumát [itt](reference-opc-publisher-telemetry-format.md#opc-publisher-telemetry-configuration-file-format)találja.

## <a name="next-steps"></a>Következő lépések
Most, hogy konfigurálta az OPC-közzétevőt, a következő lépés az Edge-modul teljesítményének és memóriájának finomhangolása:

> [!div class="nextstepaction"]
> [Teljesítmény-és memória-hangolás](tutorial-publisher-performance-memory-tuning-opc-publisher.md)