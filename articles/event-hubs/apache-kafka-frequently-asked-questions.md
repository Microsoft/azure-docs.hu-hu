---
title: Gyakori kérdések – Azure Event Hubs a Apache Kafka
description: Ez a cikk az Azure Event Hubs Apache Kafka által másutt nem tárgyalt ügyfelek támogatásával kapcsolatban feltett gyakori kérdésekre ad választ.
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: dc6a12b2098a1fdf33adda92b4347f91ab4e5489
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "91828111"
---
# <a name="frequently-asked-questions---event-hubs-for-apache-kafka"></a>Gyakori kérdések – Event Hubs Apache Kafka 
Ez a cikk a Apache Kafka Event Hubs való áttelepítésével kapcsolatos gyakori kérdésekre adott válaszokat tartalmaz.

## <a name="does-azure-event-hubs-run-on-apache-kafka"></a>Az Azure Event Hubs fut Apache Kafkaon?

Nem. Az Azure Event Hubs egy felhőalapú többrétegű közvetítő, amely több, a Microsoft által fejlesztett és karbantartott protokollt támogat, és nem használ semmilyen Apache Kafka kódot. Az egyik támogatott protokoll a Kafka RPC protokoll a Kafka-ügyfél fogyasztói és termelői API-jai számára. A Event Hubs számos meglévő Kafka-alkalmazással működik. További információ: [Event Hubs Apache Kafkahoz](event-hubs-for-kafka-ecosystem-overview.md). Mivel a Apache Kafka és az Azure Event Hubs fogalmai nagyon hasonlóak (de nem azonosak), az Azure-Event Hubs páratlan megbízhatóságát kínáljuk meglévő Apache Kafka befektetésekkel rendelkező ügyfeleknek. 

## <a name="event-hubs-consumer-group-vs-kafka-consumer-group"></a>Event Hubs fogyasztói csoport és a Kafka fogyasztói csoport
Mi a különbség az Event hub fogyasztói csoport és a Kafka fogyasztói csoport között Event Hubs? A Event Hubs Kafka fogyasztói csoportjai teljesen különböznek a standard Event Hubs fogyasztói csoportoktól.

**Event Hubs fogyasztói csoportok**

- Ezek a létrehozási, lekérdezési, frissítési és törlési (szifilisz) műveletekkel kezelhetők a portálon, az SDK-ban vagy a Azure Resource Manager-sablonokon keresztül. Event Hubs fogyasztói csoportok nem hozhatók létre.
- Az Event hub gyermek entitásai. Ez azt jelenti, hogy ugyanaz a fogyasztói csoport neve is felhasználható ugyanabban a névtérben lévő Event hubok között, mert ezek különálló entitások.
- Nem használhatók eltolások tárolására. A AMQP-használat a külső Eltolásos tárolással történik, például az Azure Storage-ban.

**Kafka fogyasztói csoportok**

- Ezek önmagukban jönnek létre.  A Kafka-csoportok a Kafka fogyasztói csoport API-jai segítségével kezelhetők.
- Az eltolásokat a Event Hubs szolgáltatásban tárolhatják.
- Ezek kulcsként használatosak a mi valójában egy Eltolásos kulcs-érték tároló. A és a rendszer egyedi párjaként `group.id` `topic-partition` egy eltolást tárolunk az Azure Storage-ban (3x-os replikálás). Event Hubs a felhasználók nem számítanak fel további tárolási költséget a Kafka-eltolások tárolásához. Az eltolások a Kafka fogyasztói csoport API-manipulálható keresztül érhetők el, de az Eltolásos tárolási *fiókok* nem közvetlenül láthatók vagy manipulálható az Event hub felhasználói számára.  
- Egy névteret ölelnek fel. Ha ugyanazt a Kafka-csoportnevet használja több alkalmazáshoz több témakörben, az azt jelenti, hogy minden alkalmazás és a Kafka-ügyfél újra lesz egyenlítve, amikor csak egyetlen alkalmazásnak kell kiegyensúlyoznia.  Adja meg a csoportok nevét okosan.
- Teljesen eltérnek Event Hubs fogyasztói csoportoktól. **Nincs** szükség a (z) "$default" használatára, és nem kell aggódnia, hogy a Kafka-ügyfelek AMQP-munkaterhelésekkel ütköznek.
- Nem láthatók a Azure Portalban. A fogyasztói csoport adatai a Kafka API-kon keresztül érhetők el.

## <a name="next-steps"></a>Következő lépések
Ha többet szeretne megtudni a Kafka Event Hubséről és Event Hubsról, tekintse meg a következő cikkeket:  

- [Apache Kafka fejlesztői útmutató Event Hubs](apache-kafka-developer-guide.md)
- [A Event Hubs Apache Kafka áttelepítési útmutatója](apache-kafka-migration-guide.md)
- [Event Hubs Apache Kafka hibaelhárítási útmutatója](apache-kafka-troubleshooting-guide.md)
- [Ajánlott konfigurációk](apache-kafka-configurations.md)

