---
title: Az Azure IoT Hub egyéni végpontok ismertetése | Microsoft Docs
description: Fejlesztői útmutató – az eszközről a felhőbe irányuló üzenetek átirányítására szolgáló útválasztási lekérdezések használata egyéni végpontokra.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/09/2018
ms.openlocfilehash: 8aebb5b6f6a3ac53bc49fd1d2f75de88667865fb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "92147652"
---
# <a name="use-message-routes-and-custom-endpoints-for-device-to-cloud-messages"></a>Az eszközről a felhőbe irányuló üzenetekhez tartozó üzenetek és egyéni végpontok használata

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

IoT Hub [üzenet-útválasztás](iot-hub-devguide-routing-query-syntax.md) lehetővé teszi a felhasználók számára, hogy az eszközről a felhőbe irányuló üzeneteket átirányítsák a szolgáltatás felé irányuló végpontokra. Az Útválasztás egy lekérdezési képességet is biztosít az adatszűréshez, mielőtt átirányítja azt a végpontokhoz. A konfigurált útválasztási lekérdezések a következő tulajdonságokkal rendelkeznek:

| Tulajdonság      | Leírás |
| ------------- | ----------- |
| **Név**      | A lekérdezést azonosító egyedi név. |
| **Forrás**    | Annak az adatfolyamnak a forrása, amelyre a folyamat vonatkozik. Például az eszköz telemetria. |
| **Feltétel** | Az üzenet alkalmazás tulajdonságai, a rendszer tulajdonságai, az üzenet törzse, az eszköz két címkéje és az eszköz Twin tulajdonságai között futtatott útválasztási lekérdezés lekérdezési kifejezése annak megállapításához, hogy az megfelel-e a végpontnak. A lekérdezések létrehozásával kapcsolatos további információkért tekintse meg az [üzenet-útválasztási lekérdezés szintaxisa](iot-hub-devguide-routing-query-syntax.md) című témakört. |
| **Végpont**  | Annak a végpontnak a neve, ahol a IoT Hub a lekérdezésnek megfelelő üzeneteket küld. Azt javasoljuk, hogy válasszon egy végpontot abban a régióban, ahol az IoT hub található. |

Egyetlen üzenet is megegyező lehet a több útválasztási lekérdezés feltételével, ebben az esetben a IoT Hub továbbítja az üzenetet az egyes egyeztetett lekérdezésekhez társított végpontnak. A IoT Hub automatikusan deduplikálja az üzenetek kézbesítését, így ha egy üzenet több, ugyanazzal a célhelytel rendelkező lekérdezésnek felel meg, csak egyszer kell beírni az adott célhelyre.

## <a name="endpoints-and-routing"></a>Végpontok és Útválasztás

Az IoT hub alapértelmezett [beépített végpontja](iot-hub-devguide-messages-read-builtin.md). Létrehozhat egyéni végpontokat az üzenetek átirányításához az előfizetésben található egyéb szolgáltatások összekapcsolásával az hubhoz. IoT Hub jelenleg az Azure Storage-tárolókat, Event Hubseket, Service Bus várólistákat és Service Bus témákat támogatja egyéni végpontként.

Az Útválasztás és az egyéni végpontok használatakor az üzenetek csak a beépített végpontra érkeznek, ha nem felelnek meg egyetlen lekérdezésnek sem. Ha üzeneteket szeretne küldeni a beépített végpontra és egy egyéni végpontra, adjon hozzá egy útvonalat, amely üzeneteket küld a beépített **események** végpontjának.

> [!NOTE]
> * IoT Hub csak az Azure Storage-tárolók számára blobként való adatírást támogatja.
> * A **munkamenetek** és az **ismétlődő észlelések** által engedélyezett Service Bus várólisták és témakörök nem használhatók egyéni végpontként.

Az egyéni végpontok IoT Hubban történő létrehozásával kapcsolatos további információkért lásd: [IoT hub-végpontok](iot-hub-devguide-endpoints.md).

További információ az egyéni végpontokról való olvasásról:

* Az [Azure Storage](../storage/blobs/storage-blobs-introduction.md)-tárolók beolvasása.

* Olvasás [Event Hubsról](../event-hubs/event-hubs-dotnet-standard-getstarted-send.md).

* [Service Bus várólistákból](../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md)való olvasás.

* [Service Bus témakörök](../service-bus-messaging/service-bus-dotnet-how-to-use-topics-subscriptions.md)olvasása.

## <a name="next-steps"></a>Következő lépések

* IoT Hub végpontokkal kapcsolatos további információkért lásd: [IoT hub-végpontok](iot-hub-devguide-endpoints.md).

* További információ az útválasztási lekérdezések definiálásához használt lekérdezési nyelvről: üzenet- [útválasztási lekérdezés szintaxisa](iot-hub-devguide-routing-query-syntax.md).

* Az [eszközökről a felhőbe irányuló, útvonalakat használó üzenetek IoT hub folyamata](tutorial-routing.md) az útválasztási lekérdezések és az egyéni végpontok használatát mutatja be.