---
title: Azure Service Bus – üzenet halasztás
description: Ez a cikk azt ismerteti, hogyan halasztja el a Azure Service Bus kézbesítését. Az üzenet az üzenetsorban vagy az előfizetésben marad, de fel van állítva.
ms.topic: conceptual
ms.date: 04/21/2021
ms.openlocfilehash: 171fc6e1a3c779802747d34ac631d265e8c8926f
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2021
ms.locfileid: "107869490"
---
# <a name="message-deferral"></a>Üzenetek halasztása
Ha egy üzenetsor vagy előfizetési ügyfél olyan üzenetet kap, hogy hajlandó feldolgozni, de a feldolgozás speciális körülmények miatt jelenleg nem lehetséges, lehetősége van az üzenet lekérésének egy későbbi időpontra történő "elhalasztása" is. Az üzenet az üzenetsorban vagy az előfizetésben marad, de fel van állítva.

> [!NOTE]
> A késleltetett üzenetek nem lesznek automatikusan áthelyezve a holtbetűs üzenetsorba [a lejáratuk után.](./service-bus-dead-letter-queues.md#time-to-live) Ez a viselkedés a tervezésből ad vissza.

## <a name="sample-scenarios"></a>Mintaforgatókönyvek
A Deferral egy kifejezetten munkafolyamat-feldolgozási forgatókönyvekhez létrehozott szolgáltatás. A munkafolyamat-keretrendszerek megkövetelhetik bizonyos műveletek adott sorrendben való feldolgozását. Előfordulhat, hogy egyes fogadott üzenetek feldolgozását el kell halaszta előttük, amíg más üzenetek által tájékoztatott, előírt előzetes munka be nem fejeződik.

Egy egyszerű szemléltető példa egy rendelésfeldolgozási folyamat, amelyben egy külső fizetési szolgáltatótól származó fizetési értesítés megjelenik a rendszerben, mielőtt a rendszer propagálta volna az egyező rendelést az áruházból a teljesítési rendszerbe. Ebben az esetben a teljesítő rendszer elhalaszthatja a fizetési értesítés feldolgozását, amíg nem kap egy rendelést, amellyel társíthatja. Olyan szinkronizálási forgatókönyvekben, ahol a különböző forrásokból származó üzenetek egy munkafolyamatot továbbítanak, a valós idejű végrehajtási sorrend valóban helyes lehet, de az eredményeket tükröző üzenetek sorrendje helytelen lehet.

Végső soron a halasztás segít az üzeneteknek az érkezési sorrendből a feldolgozásuk sorrendjébe való átrendezésében, miközben ezeket az üzeneteket biztonságosan hagyja az üzenettárban, amelyre a feldolgozást el kell halasztani.

Ha egy üzenetet nem lehet feldolgozni, mert az üzenet kezelésére egy adott erőforrás átmenetileg nem érhető el, de az üzenetfeldolgozást nem szabad összefoglalóan felfüggeszteni, [](message-sequencing.md) akkor az üzenet néhány percig úgy tehetők oldalra, hogy az ütemezett üzenetben néhány percen belül meg kell jegyezni a sorszámot, és újra le kellkérni a késleltetett üzenetet, amikor az ütemezett üzenet megérkezik. Ha egy üzenetkezelő az összes művelethez egy adatbázistól függ, és az adatbázis átmenetileg nem érhető el, nem használhat halasztásokat, hanem teljesen felfüggesztheti az üzenetek fogadását, amíg az adatbázis újra elérhetővé nem válik. 

## <a name="retrieving-deferred-messages"></a>Késleltetett üzenetek leolvasása
A késleltetett üzenetek a fő üzenetsorban maradnak az összes többi aktív üzenettel együtt (ellentétben az alsorok között élő holtbetűs üzenetekkel), de a továbbiakban nem fogadhatóak a normál fogadási műveletekkel. A késleltetett üzenetek [](message-browsing.md) az üzenetek tallózásával felderíthetőek, ha egy alkalmazás nem követi nyomon őket.

Késleltetett üzenet lekérése esetén a tulajdonos felelős a sorszám megfejléséért, amikor az töredezettségmentessé kerül. Ha egy címzett ismeri a késleltetett üzenetek sorozatszámát, később olyan fogadási metódusok használatával fogadhatja az üzenetet, amelyek paraméterként a sorszámot használják. A sorozatszámokkal kapcsolatos további információkért lásd: [Üzenet-sorrend és időbélyegek.](message-sequencing.md)

## <a name="next-steps"></a>Következő lépések
Próbálja ki a mintákat a választott nyelven, és fedezze fel Azure Service Bus funkcióit. 

- [Azure Service Bus java-ügyféloldali kódtárminták](/samples/azure/azure-sdk-for-java/servicebus-samples/)
- [Azure Service Bus Pythonhoz készült](/samples/azure/azure-sdk-for-python/servicebus-samples/) ügyféloldali kódtárminták – lásd a **receive_deferred_message_queue.py mintát.** 
- [Azure Service Bus JavaScripthez való ügyféloldali](/samples/azure/azure-sdk-for-js/service-bus-javascript/) kódtárminták – lásd a **speciális/deferral.js** mintát. 
- [Azure Service Bus TypeScript ügyféloldali](/samples/azure/azure-sdk-for-js/service-bus-typescript/) kódtárminái – lásd **az advanced/deferral.ts mintát.** 
- [Azure.Messaging.ServiceBus-minták a .NET-hez](/samples/azure/azure-sdk-for-net/azuremessagingservicebus-samples/) – Lásd **a Üzenetkezelés mintát.** 

Az alábbiakban a régebbi .NET- és Java-ügyfélkódtárakhoz találhat mintákat:
- [Microsoft.Azure.ServiceBus-minták a .NET-hez](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.Azure.ServiceBus/) – Lásd a **Deferral mintát.** 
- [azure-servicebus-minták Javához](https://github.com/Azure/azure-service-bus/tree/master/samples/Java/azure-servicebus/MessageBrowse)
