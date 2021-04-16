---
title: Az Azure IoT Hub végpontok | Microsoft Docs
description: Fejlesztői útmutató – útválasztási lekérdezések használata az eszközről a felhőbe küldött üzenetek egyéni végpontokra való átirányításához.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/09/2018
ms.openlocfilehash: 4ad57473e0950f031fbeadee2302f85557ed526f
ms.sourcegitcommit: aa00fecfa3ad1c26ab6f5502163a3246cfb99ec3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2021
ms.locfileid: "107388261"
---
# <a name="use-message-routes-and-custom-endpoints-for-device-to-cloud-messages"></a>Üzenetútvonalak és egyéni végpontok használata az eszközről a felhőbe küldött üzenetekhez

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

IoT Hub [üzenet-útválasztással](iot-hub-devguide-routing-query-syntax.md) a felhasználók az eszközről a felhőbe irányítják az üzeneteket a szolgáltatás felé néző végpontokra. Az útválasztás lekérdezési képességet is biztosít az adatok szűrésére, mielőtt a végpontokra útválasztást ad. Minden konfigurált útválasztási lekérdezés a következő tulajdonságokkal rendelkezik:

| Tulajdonság      | Leírás |
| ------------- | ----------- |
| **Név**      | A lekérdezést azonosító egyedi név. |
| **Forrás**    | Az adatfolyam eredete, amely alapján el kell majd látni a megfelelőt. Például eszköz-telemetria. |
| **Feltétel** | Az üzenetalkalmazás tulajdonságain, rendszertulajdonságán, üzenet törzsén, ikereszközcímkéin és ikereszköz-tulajdonságain futtatott útválasztási lekérdezés lekérdezési kifejezése annak megállapításához, hogy az egyezés-e a végponttal. A lekérdezések felépítésével kapcsolatos további információkért lásd az üzenetek útválasztásának [lekérdezési szintaxisát.](iot-hub-devguide-routing-query-syntax.md) |
| **Végpont**  | Annak a végpontnak a neve, IoT Hub a lekérdezésnek megfelelő üzeneteket küld. Javasoljuk, hogy válasszon egy végpontot ugyanabban a régióban, mint az IoT Hub. |

Egyetlen üzenet megfelelhet több útválasztási lekérdezés feltételének, amely esetben a IoT Hub kézbesíti az üzenetet az egyes egyező lekérdezésekhez társított végpontnak. IoT Hub deduplikálja az üzenetek kézbesítését is, így ha egy üzenet több, azonos célhelytel rendelkező lekérdezésre illeszkedik, akkor a rendszer csak egyszer írja a célhelyre.

## <a name="endpoints-and-routing"></a>Végpontok és útválasztás

Az IoT Hub alapértelmezett beépített [végponttal rendelkezik.](iot-hub-devguide-messages-read-builtin.md) Egyéni végpontokat hozhat létre, amelyekre az üzenetek irányítva vannak, ha a saját előfizetésében található egyéb szolgáltatásokat a központhoz csatolja. IoT Hub jelenleg az Azure Storage-tárolókat, Event Hubs, Service Bus üzenetsorokat és Service Bus témaköröket támogatja egyéni végpontokként.

Útválasztás és egyéni végpontok használata esetén a rendszer csak akkor kézbesíti az üzeneteket a beépített végpontra, ha azok nem egyeznek egyetlen lekérdezéssel sem. Ha a beépített végpontra és egy egyéni végpontra is szeretne üzeneteket kézbesíteni, adjon hozzá egy útvonalat, amely üzeneteket küld a beépített **eseményvégpontnak.**

> [!NOTE]
> * IoT Hub csak az Azure Storage-tárolókba blobként való adatírást támogatja.
> * Service Bus munkamenetek vagy ismétlődések  észlelése  beállítással rendelkező üzenetsorok és témakörök nem támogatottak egyéni végpontokként.
> * A Azure Portal csak olyan Azure-erőforrásokhoz hozhat létre egyéni útválasztási végpontokat, amelyek ugyanabban az előfizetésben vannak, mint a központ. Létrehozhat egyéni végpontokat a tulajdonában álló más előfizetések erőforrásaihoz, de az egyéni végpontokat más módszerrel kell konfigurálni, mint a Azure Portal.

További információ az egyéni végpontok létrehozásáról a IoT Hub: [IoT Hub végpontok.](iot-hub-devguide-endpoints.md)

Az egyéni végpontok olvasásának további információiért lásd:

* Olvasás [Azure Storage-tárolókból:](../storage/blobs/storage-blobs-introduction.md).

* Olvasás a [Event Hubs.](../event-hubs/event-hubs-dotnet-standard-getstarted-send.md)

* Olvasás a [Service Bus üzenetsorból.](../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md)

* Olvasás a [Service Bus témakörökből.](../service-bus-messaging/service-bus-dotnet-how-to-use-topics-subscriptions.md)

## <a name="next-steps"></a>Következő lépések

* További információ a végpontok IoT Hub: IoT Hub [végpontok.](iot-hub-devguide-endpoints.md)

* További információ az útválasztási lekérdezések meghatározásához használt lekérdezési nyelvről: [Üzenetirányítás lekérdezési szintaxisa.](iot-hub-devguide-routing-query-syntax.md)

* A Folyamat IoT Hub az [eszközről a](tutorial-routing.md) felhőbe üzeneteket útvonalak használatával oktatóanyag bemutatja, hogyan használhatja az útválasztási lekérdezéseket és az egyéni végpontokat.
