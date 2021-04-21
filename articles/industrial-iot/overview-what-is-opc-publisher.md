---
title: Microsoft OPC Publisher
description: Ez a cikk áttekintést nyújt az OPC Publisher Edge modulról.
author: v-condav
ms.author: jemorina
ms.service: industrial-iot
ms.topic: conceptual
ms.date: 3/22/2021
ms.openlocfilehash: 6df39c93e9bcfca522ac61a863c87269216cc592
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2021
ms.locfileid: "107816169"
---
# <a name="what-is-the-opc-publisher"></a>Mi az az OPC-közzétevő?

Az OPC Publisher egy teljes mértékben támogatott Microsoft-termék, amely áthidalja az ipari eszközök és a felhő Microsoft Azure közötti különbséget. Ezt az OPC UA-kompatibilis eszközök vagy ipari csatlakozási szoftverek a felhőhöz való csatlakoztatásával Microsoft Azure teszi. A telemetriai adatokat különböző formátumokban teszi közzé az Azure IoT Hub-ban, beleértve az IEC62541 OPC UA PubSub standard formátumot (a 2.6-os vagy újabb verziótól kezdve). Az OPC-közzétevő Azure IoT Edge modulként vagy egyszerű Docker-tárolóként fut a tárolóként. Mivel a platformfüggetlen .NET-alapú futásidőt használja, natív módon fut Linuxon és Windows 10.

Az OPC Publisher egy referencia-implementáció, amely bemutatja a következő műveleteket:

- Csatlakozás meglévő OPC UA-kiszolgálókhoz.
- OPC UA Pub/Sub formátumban, JSON-kódban kódolt telemetriai adatokat tehet közzé OPC UA Pub/Sub formátumban egy JSON-adat használatával egy Azure IoT Hub.

Az ügyféloldali SDK által támogatott átviteli protokollok bármelyikét Azure IoT Hub, például a HTTPS, az AMQP és az MQTT protokollt.

A referencia-implementáció a következőket tartalmazza.

- OPC *UA-ügyfél* a hálózaton meglévő OPC UA-kiszolgálókhoz való csatlakozáshoz.
- Egy OPC *UA-kiszolgáló* a 62222-es porton, amely a közzétett adatok kezelésére használható, és IoT Hub közvetlen metódusokat kínál erre.

Az OPC-közzétevő [referencia-implementációját a](https://github.com/Azure/iot-edge-opc-publisher) GitHubról töltheti le.

Az alkalmazás .NET Core technológiával van megvalósítva, és a .NET Core által támogatott bármely platformon futtatható.

## <a name="what-does-the-opc-publisher-do"></a>Mire való az OPC-közzétevő?

Az OPC-közzétevő újrapróbálkozási logikát alkalmaz azon végpontok kapcsolatainak létrehozására, amelyek nem válaszolnak bizonyos számú életben tartás kérésre. Ha például egy OPC UA-kiszolgáló áramkimaradás miatt nem válaszol.

Az OPC UA-kiszolgáló minden különálló közzétételi időköze esetén az alkalmazás egy külön előfizetést hoz létre, amelyen az ezzel a közzétételi időközrel frissített összes csomópont frissül.

Az OPC Publisher támogatja a IoT Hub számára küldött adatok kötegelhetőségét a hálózati terhelés csökkentése érdekében. Ez a kötegolás csak akkor küld csomagot a IoT Hub, ha eléri a konfigurált csomagméretet.

Ez az alkalmazás az OPC Foundation OPC UA-referenciakészletet használja NuGet-csomagokként. A [https://opcfoundation.org/license/redistributables/1.3/](https://opcfoundation.org/license/redistributables/1.3/) licencelési feltételeket lásd: .

## <a name="next-steps"></a>Következő lépések
Most, hogy már tudja, mi az AZ OPC-közzétevő, elkezdheti üzembe helyezni:

> [!div class="nextstepaction"]
> [Az OPC Publisher üzembe helyezése önálló módban](tutorial-publisher-deploy-opc-publisher-standalone.md)
