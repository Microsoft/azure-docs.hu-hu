---
title: Használja az eseményközpont alkalmazásból Apache kafka platformmal – Azure Event Hubs |} A Microsoft Docs
description: Ez a cikk az Azure Event Hubs által az Apache Kafka támogatási információkat biztosít.
services: event-hubs
documentationcenter: .net
author: shvija
manager: timlt
ms.service: event-hubs
ms.topic: article
ms.custom: seodec18
ms.date: 12/06/2018
ms.author: bahariri
ms.openlocfilehash: 8bf381e7c66e06bbaa140ed865f0f7c9b4f001af
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60821697"
---
# <a name="use-azure-event-hubs-from-apache-kafka-applications"></a>Az Azure Event Hubs használata az Apache Kafka-alkalmazásokból
Az Event Hubs egy Kafka-végpontot, amely segítségével biztosít a meglévő által a Kafka-alapú alkalmazások futtatása a saját Kafka-fürt helyett. Az Event Hubs támogatja [Apache Kafka protokoll 1.0-s és újabb verziók](https://kafka.apache.org/documentation/), és együttműködik a meglévő Kafka-alkalmazásokkal, beleértve a MirrorMaker.  

## <a name="what-does-event-hubs-for-kafka-provide"></a>Mit nyújt a Kafka az Event Hubs?

Az Event Hubs, Kafka szolgáltatáshoz biztosít egy protokoll a fő Azure Event Hubs, Kafka 1.0-s verziójával, és később a olvasása és írása a Kafka-témakörökhöz bináris kompatibilis felett. Nincs kód megváltoztatására, de a minimális konfigurációs változást előfordulhat, hogy a Kafka-végpont az alkalmazások használatához. Frissítse a kapcsolati karakterláncot a konfigurációk, a Kafka-végpontra mutat a Kafka-fürt helyett az eseményközpont által elérhetővé tett mutasson. Ezt követően megkezdheti az Event hubsba a Kafka-protokollt használó alkalmazások az események streamelése. Ez az integráció is keretrendszereket támogat, mint [Kafka csatlakozás](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/connect), amely jelenleg előzetes verzióban érhető el. 

Elméleti szinten a Kafka és az Event Hubs majdnem azonos: mindkét particionált naplók tervezve a folyamatos átviteli adatok. Az alábbi táblázat a fogalmak közötti Kafka és az Event Hubs képezi le.

### <a name="kafka-and-event-hub-conceptual-mapping"></a>A Kafka és az Eseményközpont elméleti leképezését

| A Kafka fogalom | Event Hubs fogalma|
| --- | --- |
| Fürt | Névtér |
| Témakör | Eseményközpont |
| Partíció | Partíció|
| Fogyasztói csoport | Fogyasztói csoport |
| Eltolás | Eltolás|

### <a name="key-differences-between-kafka-and-event-hubs"></a>A Kafka és az Event Hubs közötti fő különbségeket

Miközben [Apache Kafka](https://kafka.apache.org/) szoftver, amely is futtathatja, bárhol is választja, hasonló az Azure Blob Storage egy felhőalapú szolgáltatás Azure Event Hubs szolgáltatás. Nincsenek kiszolgálók vagy hálózatok kezelése és konfigurálása nélkül közvetítők. Hozzon létre egy névteret, amely egy teljes Tartománynevet, amelyben a témakörök élő, és hozzon létre az Event Hubs vagy az adott névtérben témakörök. Az Event Hubs és a névterek kapcsolatos további információkért lásd: [Event Hubs-szolgáltatások](event-hubs-features.md#namespace). Felhőalapú szolgáltatás, az Event Hubs egyetlen stabil virtuális IP-címet használ végpontként, így az ügyfelek nem kell tudnia a közvetítők vagy fürtön belüli gépek. 

Az Event Hubs méretezhető szabályozott szerint hány átviteli egységet vásárol, az egyes kapacitásegységek, amely feljogosítja arra, 1 MB / másodperc, vagy 1000 esemény szolgálhat. Alapértelmezés szerint az Event Hubs méretezhető átviteli egységek Ha egyenlege eléri a korlátot, az a [automatikus feltöltésről](event-hubs-auto-inflate.md) funkció; Ez a funkció is működik az Event Hubs Kafka szolgáltatáshoz. 

### <a name="security-and-authentication"></a>Biztonság és hitelesítés

Az Azure Event Hubs minden kommunikáció SSL vagy TLS igényel, és a közös hozzáférésű Jogosultságkódok (SAS) használja a hitelesítéshez. Ez a követelmény egy Kafka-végponton, az Event Hubs belül is igaz. Kompatibilitás érdekében a Kafka az Event Hubs használ a SASL egyszerű a hitelesítéshez és az SASL SSL átviteli biztonsághoz. Az Event Hubs biztonságával kapcsolatos további információkért lásd: [az Event Hubs hitelesítési és biztonsági](event-hubs-authentication-and-security-model-overview.md).

## <a name="other-event-hubs-features-available-for-kafka"></a>Más Kafka elérhető Event Hubs-szolgáltatásokról

Az Event Hubs, Kafka funkció lehetővé teszi egy protokoll írása, és a egy másik, olvassa el, hogy az aktuális Kafka gyártók továbbra is a közzététel a Kafka-n keresztül, és az Event hubs szolgáltatással, például az Azure Stream Analytics vagy az Azure Functions olvasók is hozzáadhat. Emellett az Event Hubs funkciói, például [rögzítése](event-hubs-capture-overview.md) és [Geo-vészhelyreállítás](event-hubs-geo-dr.md) együttműködnek az Event Hubs, Kafka szolgáltatáshoz.

## <a name="features-that-are-not-yet-supported"></a>Még nem támogatott funkciók 

Itt látható a listában, a Kafka-funkciók még nem támogatottak:

*   Idempotens producer
*   Tranzakció
*   Tömörítés
*   Méret-alapú megőrzése
*   Tömörítési naplózása
*   A partíciók hozzáadása egy meglévő témakörhöz
*   HTTP Kafka API támogatása
*   A Kafka Streams

## <a name="next-steps"></a>További lépések

Ez a cikk az Event Hubs bemutatása Kafka biztosítunk. További információkért lásd az alábbi hivatkozásokat:

- [Kafka-kompatibilis eseményközpont létrehozása](event-hubs-create-kafka-enabled.md)
- [Streamelés az Event Hubsba a Kafka-alkalmazásokból](event-hubs-quickstart-kafka-enabled-event-hubs.md)
- [Kafka-közvetítő tükrözése egy Kafka-kompatibilis eseményközpontba](event-hubs-kafka-mirror-maker-tutorial.md)
- [Az Apache Spark csatlakoztatása egy Kafka-kompatibilis eseményközponthoz](event-hubs-kafka-spark-tutorial.md)
- [Az Apache Flink csatlakoztatása egy Kafka-kompatibilis eseményközponthoz](event-hubs-kafka-flink-tutorial.md)
- [A Kafka Connect integrálása egy Kafka-kompatibilis eseményközponttal](event-hubs-kafka-connect-tutorial.md)
- [Az Akka Streams csatlakoztatása egy Kafka-kompatibilis eseményközponthoz](event-hubs-kafka-akka-streams-tutorial.md)
- [További példák a GitHubon](https://github.com/Azure/azure-event-hubs-for-kafka)


