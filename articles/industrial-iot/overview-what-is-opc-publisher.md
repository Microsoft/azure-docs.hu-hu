---
title: Microsoft OPC-közzétevő
description: Ez a cikk az OPC-közzétevő peremhálózati moduljának áttekintését tartalmazza.
author: jehona-m
ms.author: jemorina
ms.service: industrial-iot
ms.topic: conceptual
ms.date: 3/22/2021
ms.openlocfilehash: 3a44bdbadfe6ecd86a1b98fb7002f2d75c23bb6a
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "104800533"
---
# <a name="what-is-the-opc-publisher"></a>Mi az OPC-közzétevő?

Az OPC-közzétevő egy teljes mértékben támogatott Microsoft-termék, amely az iparági eszközök és a Microsoft Azure-felhő közötti szakadék áthidalása. Így az OPC UA-kompatibilis eszközökhöz vagy ipari kapcsolati szoftverekhez való csatlakozással, valamint a telemetria-adatoknak az Azure-IoT Hub különböző formátumokban való közzétételével, beleértve az IEC62541 OPC UA közzététel standard formátumot (az 2,6-es verziótól kezdve).

Modulként vagy egyszerű Docker-tárolóként Azure IoT Edge fut. Mivel a .NET platformfüggetlen futtatókörnyezetet használja, natív módon fut a Linux és a Windows 10 rendszereken is.

Az OPC-közzétevő egy olyan hivatkozás, amely a következőket mutatja be:

- Kapcsolódjon meglévő OPC UA-kiszolgálókhoz.
- Az OPC UA-kiszolgálókról származó, a JSON-ban kódolt telemetria adatok közzététele a JSON-adattartalom használatával, az Azure IoT Hub.

Az Azure IoT Hub Client SDK által támogatott átviteli protokollok bármelyike használható: HTTPS, AMQP és MQTT.

A hivatkozás implementációja az alábbiakat tartalmazza:

- OPC UA- *ügyfél* a hálózaton lévő meglévő OPC ua-kiszolgálókhoz való csatlakozáshoz.
- Egy OPC UA- *kiszolgáló* a 62222-as porton, amely a közzétett és IoT hub közvetlen metódusok használatával felügyelhető.

Az [OPC-közzétevői referenciát](https://github.com/Azure/iot-edge-opc-publisher) letöltheti a githubról.

Az alkalmazás a .NET Core Technology használatával valósul meg, és a .NET Core által támogatott bármely platformon futtatható.

## <a name="what-does-the-opc-publisher-do"></a>Mit tesz az OPC-közzétevő?

Az OPC-közzétevő az újrapróbálkozási logikát implementálja olyan végpontok kapcsolatainak létrehozásához, amelyek nem válaszolnak bizonyos számú életben tartási kérelemre. Ha például egy OPC UA-kiszolgáló áramszünet miatt nem válaszol.

Az alkalmazás minden egyes különböző közzétételi intervallum esetében egy OPC UA-kiszolgálóhoz hoz létre egy külön előfizetést, amely felett az adott közzétételi időközt tartalmazó összes csomópont frissül.

Az OPC-közzétevő támogatja a hálózati terhelés csökkentése érdekében IoT Hub elküldett adatfeldolgozást. Ez a kötegelt feldolgozás csak akkor küld IoT Hub csomagot, ha elérte a beállított csomagméret méretét.

Ez az alkalmazás az OPC Foundation OPC UA Reference stacket használja NuGet-csomagként. Lásd [https://opcfoundation.org/license/redistributables/1.3/](https://opcfoundation.org/license/redistributables/1.3/) a licencelési feltételeket.

## <a name="next-steps"></a>Következő lépések
Most, hogy megismerte az OPC-közzétevőt, megtudhatja, hogyan helyezheti üzembe:

> [!div class="nextstepaction"]
> [OPC-közzétevő üzembe helyezése önálló módban](tutorial-publisher-deploy-opc-publisher-standalone.md)
