---
title: Azure Service Bus üzenet-előkészítés és időbélyegek | Microsoft Docs
description: Ez a cikk azt ismerteti, hogyan lehet megőrizni a Azure Service Bus üzenetek sorrendjét és megrendelését (időbélyeg használatával).
services: service-bus-messaging
documentationcenter: ''
author: axisc
manager: timlt
editor: spelluru
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/24/2020
ms.author: aschhab
ms.openlocfilehash: 54d774c00fa650cb9608f46cc07b9d899709eaa5
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/13/2020
ms.locfileid: "79261657"
---
# <a name="message-sequencing-and-timestamps"></a>Üzenetek előkészítése és időbélyegek

Az előkészítés és az időbélyegző két olyan szolgáltatás, amely mindig engedélyezve van az összes Service Bus entitáson és felületen a fogadott vagy a tallózással ellátott üzenetek [sorszám](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.sequencenumber) és [EnqueuedTimeUtc](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.enqueuedtimeutc) tulajdonságain keresztül.

Azokban az esetekben, amelyekben az üzenetek abszolút sorrendje jelentős, és/vagy amelyekben a fogyasztónak megbízható egyedi azonosítóval kell rendelkeznie az üzenetekhez Particionált entitások esetén a sorozatszám a partícióhoz viszonyítva jelenik meg.

A **sorszám** érték egy egyedi, 64 bites egész szám, amelyet elfogad, és a közvetítő és a függvény belső azonosítójaként tárol az üzenethez. Particionált entitások esetén a legfelső 16 bit a partíció azonosítóját tükrözi. A sorozatszámok átadása nulla értékre történik, ha a 48/64 bites tartomány kimerült.

A sorozatszám lehet megbízhatóként szolgálni, mivel azt egy központi és semleges szolgáltató rendeli hozzá, és nem az ügyfelek. Emellett az érkezés igaz sorrendjét is jelenti, és az időbélyegnél sokkal pontosabb, mert az időbélyegek nem rendelkezhetnek elég nagy felbontással a szélsőséges üzenetek díjainál, és előfordulhat, hogy a közvetítő tulajdonosi átmenetek a csomópontok között.

Az abszolút megérkezési sorrend számít, például olyan üzleti forgatókönyvekben, amelyekben korlátozott számú felkínált termék érkezik a szolgáltatásba, és a szolgáltatás az utolsót is teljesíti. Példa a koncert jegyek értékesítésére.

Az időbélyegzési képesség semleges és megbízható szolgáltatóként működik, amely pontosan rögzíti az üzenet érkezésének UTC-időpontját, amely a **EnqueuedTimeUtc** tulajdonságban szerepel. Az érték akkor hasznos, ha egy üzleti forgatókönyv a határidőktől függ, például azt, hogy a munkaelemet éjfél előtt adta-e meg egy adott dátumon, de a feldolgozás messze van-e a várólista-várakozó fájloktól.

## <a name="scheduled-messages"></a>Ütemezett üzenetek

Üzeneteket küldhet egy várólistába vagy témakörbe késleltetett feldolgozás céljából. például egy olyan feladat ütemezhető, amely elérhetővé válik egy rendszer által egy adott időpontban történő feldolgozásra. Ez a funkció megbízható, elosztott időalapú ütemező szolgáltatást valósít meg.

Az ütemezett üzenetek a megadott sorba helyezni időpontig nem jelennek meg a várólistán. Ez idő előtt az ütemezett üzenetek megvonhatók. A törlés törli az üzenetet.

Az üzeneteket a [ScheduledEnqueueTimeUtc](/dotnet/api/microsoft.azure.servicebus.message.scheduledenqueuetimeutc) tulajdonság beállításával is elvégezheti, ha az üzenetet a normál küldési útvonalon keresztül küldi el, vagy explicit módon a [ScheduleMessageAsync](/dotnet/api/microsoft.azure.servicebus.queueclient.schedulemessageasync#Microsoft_Azure_ServiceBus_QueueClient_ScheduleMessageAsync_Microsoft_Azure_ServiceBus_Message_System_DateTimeOffset_) API-val. Az utóbbi azonnal visszaadja az ütemezett üzenet **sorszám**, amelyet később az ütemezett üzenet megszakítására is használhat, ha szükséges. Az ütemezett üzenetek és sorozatszámuk is felderíthető az [üzenetek tallózása](message-browsing.md)során.

Az ütemezett üzenet **sorszám** csak akkor érvényes, ha az üzenet ebben az állapotban van. Ahogy az üzenet aktív állapotba vált, az üzenetet a rendszer hozzáfűzi a várólistához, mintha a jelenlegi pillanatban várólistán lévő volna, ami magában foglalja az új **sorszám**hozzárendelését is.

Mivel a szolgáltatás egyedi üzenetekre van rögzítve, és az üzenetek csak egyszer várólistán lévő, Service Bus nem támogatja az üzenetek ismétlődő ütemezését.

## <a name="next-steps"></a>Következő lépések

Az Service Bus üzenetkezeléssel kapcsolatos további tudnivalókért tekintse meg a következő témaköröket:

* [Service Bus-üzenetsorok, -témakörök és -előfizetések](service-bus-queues-topics-subscriptions.md)
* [Bevezetés a Service Bus által kezelt üzenetsorok használatába](service-bus-dotnet-get-started-with-queues.md)
* [A Service Bus-üzenettémakörök és -előfizetések használata](service-bus-dotnet-how-to-use-topics-subscriptions.md)