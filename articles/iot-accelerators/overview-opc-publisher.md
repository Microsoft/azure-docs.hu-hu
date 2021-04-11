---
title: Mi az az OPC-közzétevő – Azure | Microsoft Docs
description: Ez a cikk az OPC-közzétevő funkcióinak áttekintését tartalmazza. Lehetővé teszi kódolt JSON-telemetria adatok közzétételét JSON-adattartalommal az Azure IoT Hubba.
author: dominicbetts
ms.author: dobett
ms.date: 06/10/2019
ms.topic: overview
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.custom:
- amqp
- mqtt
ms.openlocfilehash: 49ca12ed4f408e2a3fce1c8e59f541778f35311e
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105646848"
---
# <a name="what-is-opc-publisher"></a>Mi az OPC-közzétevő?

> [!IMPORTANT]
> A cikk frissítését követően tekintse meg az [Azure Industrial IoT](https://azure.github.io/Industrial-IoT/) a legfrissebb tartalomhoz című cikket.

Az OPC-közzétevő egy olyan hivatkozás, amely a következőket mutatja be:

- Kapcsolódjon meglévő OPC UA-kiszolgálókhoz.
- Az OPC UA-kiszolgálókról származó, a JSON-ban kódolt telemetria adatok közzététele a JSON-adattartalom használatával, az Azure IoT Hub.

Az Azure IoT Hub Client SDK által támogatott átviteli protokollok bármelyike használható: HTTPS, AMQP és MQTT.

A hivatkozás implementációja az alábbiakat tartalmazza:

- OPC UA- *ügyfél* a hálózaton lévő meglévő OPC ua-kiszolgálókhoz való csatlakozáshoz.
- Egy OPC UA- *kiszolgáló* a 62222-as porton, amely a közzétett és IoT hub közvetlen metódusok használatával felügyelhető.

Az [OPC-közzétevői referenciát](https://github.com/Azure/iot-edge-opc-publisher) letöltheti a githubról.

Az alkalmazás a .NET Core Technology használatával valósul meg, és a .NET Core által támogatott bármely platformon futtatható.

Az OPC-közzétevő az újrapróbálkozási logikát implementálja olyan végpontok kapcsolatainak létrehozásához, amelyek nem válaszolnak bizonyos számú életben tartási kérelemre. Ha például egy OPC UA-kiszolgáló áramszünet miatt nem válaszol.

Az alkalmazás minden egyes különböző közzétételi intervallum esetében egy OPC UA-kiszolgálóhoz hoz létre egy külön előfizetést, amely felett az adott közzétételi időközt tartalmazó összes csomópont frissül.

Az OPC-közzétevő támogatja a hálózati terhelés csökkentése érdekében IoT Hub elküldett adatfeldolgozást. Ez a kötegelt feldolgozás csak akkor küld IoT Hub csomagot, ha elérte a beállított csomagméret méretét.

Ez az alkalmazás az OPC Foundation OPC UA Reference stacket használja NuGet-csomagként. Lásd [https://opcfoundation.org/license/redistributables/1.3/](https://opcfoundation.org/license/redistributables/1.3/) a licencelési feltételeket.

## <a name="next-steps"></a>Következő lépések

Most, hogy megismerte az OPC-közzétevőt, a javasolt következő lépés az alábbiak megismerése:

[Az OPC Publisher konfigurálása](howto-opc-publisher-configure.md)