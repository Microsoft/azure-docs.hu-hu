---
title: Az Azure IoT Hub 500xxx belső hibáinak elhárítása
description: A 500xxx belső hibáinak elhárítása
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.openlocfilehash: 7f3f5177e084693c45bed1088a4e1d091be100ed
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "84688908"
---
# <a name="500xxx-internal-errors"></a>500xxx Belső hibák

Ez a cikk a **belső hibák 500xxx**okait és megoldásait ismerteti.

## <a name="symptoms"></a>Probléma

Az IoT Hubre irányuló kérelme egy, a 500-as és/vagy valamilyen "kiszolgálóhiba" típusú hibával meghiúsul. Néhány lehetőség:

* **500001 ServerError**: IoT hub egy kiszolgálóoldali hibába ütközött.

* **500008 GenericTimeout**: a IoT hub időtúllépés előtt nem tudta befejezni a kapcsolatkérelem-kérést.

* **ServiceUnavailable (nincs hibakód)**: IoT hub belső hibát észlelt.

* **InternalServerError (nincs hibakód)**: IoT hub belső hibát észlelt.

## <a name="cause"></a>Ok

500xxx-hiba esetén számos oka lehet. Minden esetben a probléma valószínűleg átmeneti. Habár a IoT Hub csapat keményen dolgozik [Az SLA](https://azure.microsoft.com/support/legal/sla/iot-hub/)fenntartásában, IoT hub csomópontok kis részhalmaza időnként átmeneti hibákat tapasztalhat. Ha az eszköz egy problémával rendelkező csomóponthoz próbál csatlakozni, ezt a hibaüzenetet kapja.

## <a name="solution"></a>Megoldás

A 500xxx hibák enyhítéséhez adjon meg egy újrapróbálkozást az eszközről. Az [újrapróbálkozások automatikus kezeléséhez](./iot-hub-reliability-features-in-sdks.md#connection-and-retry)győződjön meg arról, hogy az [Azure IoT SDK](./iot-hub-devguide-sdks.md)-k legújabb verzióját használja. Az átmeneti hibák kezelésével és az újrapróbálkozásokkal kapcsolatos ajánlott eljárásokért lásd: [átmeneti hibák kezelésére](https://docs.microsoft.com/azure/architecture/best-practices/transient-faults).  Ha a probléma továbbra is fennáll, tekintse meg a [Resource Health](./iot-hub-monitor-resource-health.md#use-azure-resource-health) és az [Azure állapotát](https://status.azure.com/) , és ellenőrizze, hogy az IoT hub ismert problémával rendelkezik-e. Használhatja a [manuális feladatátvétel funkciót](./tutorial-manual-failover.md)is. Ha nincsenek ismert problémák, és a probléma továbbra is fennáll, [forduljon az ügyfélszolgálathoz](https://azure.microsoft.com/support/options/) a további vizsgálathoz.
