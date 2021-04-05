---
title: Rugalmasan méretezhető Cassandra APIekkel Azure Cosmos DB
description: Ismerje meg az Azure Cosmos DB Cassandra API-fiók méretezésének lehetőségeit, valamint azok előnyeit/hátrányait
author: TheovanKraay
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: conceptual
ms.date: 07/29/2020
ms.author: thvankra
ms.openlocfilehash: aad2e80598146be7b45a8a7b8a02cfe050163102
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "93340957"
---
# <a name="elastically-scale-an-azure-cosmos-db-cassandra-api-account"></a>Azure Cosmos DB Cassandra API fiók rugalmas skálázása
[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

Számos lehetőség áll rendelkezésre a Cassandra Azure Cosmos DB API rugalmas jellegének megismerésére. A Azure Cosmos DB hatékony méretezésének megismeréséhez fontos tisztában lennie azzal, hogyan kell kiépíteni a megfelelő mennyiségű (RU/s) kérést a rendszer teljesítményére vonatkozó igényeknek megfelelően. Ha többet szeretne megtudni a kérelmekkel kapcsolatos egységekről, tekintse meg a [kérelmek egységeit](request-units.md) ismertető cikket. 

A Cassandra API esetében a [.net-és Java SDK-](./find-request-unit-charge-cassandra.md)k használatával kérheti le a kérések egységét az egyes lekérdezésekhez. Ez hasznos lehet a szolgáltatásban kiépíthető RU/s mennyiségének meghatározásához.

:::image type="content" source="./media/request-units/request-units.png" alt-text="Adatbázis-műveletek felhasználásának kérelmezési egységei" border="false":::

## <a name="handling-rate-limiting-429-errors"></a>Kezelési sebesség korlátozása (429 hiba)

A Azure Cosmos DB a korlátozott számú (429) hibát eredményez, ha az ügyfelek több erőforrást használnak (RU/s), mint a kiépített mennyiség. A Azure Cosmos DB Cassandra API lefordítja ezeket a kivételeket a Cassandra Native protokollon túlterhelt hibák esetén. 

Ha a rendszer nem érzékeny a késésre, akkor elegendő lehet az átviteli sebesség korlátozására az újrapróbálkozások használatával. Tekintse meg a Java- [kódrészletet](https://github.com/Azure-Samples/azure-cosmos-cassandra-java-retry-sample) , amely bemutatja, hogyan kezelheti a ráta-korlátozásokat a Java-ban található [Cassandra újrapróbálkozási szabályzat](https://docs.datastax.com/en/developer/java-driver/4.4/manual/core/retries/) [Azure Cosmos db-bővítményének](https://github.com/Azure/azure-cosmos-cassandra-extensions) használatával. A [Spark-bővítményt](https://mvnrepository.com/artifact/com.microsoft.azure.cosmosdb/azure-cosmos-cassandra-spark-helper) is használhatja a ráta korlátozására.

## <a name="manage-scaling"></a>Skálázás kezelése

Ha csökkentenie kell a késést, számos lehetőség áll rendelkezésre a méretezés és a kiépítési teljesítmény (RUs) kezelésére a Cassandra APIban:

* [Manuálisan a Azure Portal használatával](#use-azure-portal)
* [Programozott módon a vezérlő síkja funkcióinak használatával](#use-control-plane)
* [Programozott módon egy adott SDK-val rendelkező CQL-parancsok használatával](#use-cql-queries)
* [Dinamikusan az autoscale használatával](#use-autoscale)

A következő szakaszokban az egyes megközelítések előnyeit és hátrányait ismertetjük. Ezután eldöntheti, hogy a legmegfelelőbb stratégia a rendszer skálázási igényeinek, a megoldás általános költségeit és hatékonysági igényeit is kiegyensúlyozza.

## <a name="use-the-azure-portal"></a><a id="use-azure-portal"></a>A Azure Portal használata

A Azure Portal használatával méretezheti Azure Cosmos DB Cassandra API fiók erőforrásait. További információ: az [átviteli sebesség kiépítése a tárolók és adatbázisok](set-throughput.md)számára. Ez a cikk az átviteli sebességnek a Azure Portal [adatbázisban](set-throughput.md#set-throughput-on-a-database) vagy [tároló](set-throughput.md#set-throughput-on-a-container) szintjén való beállításának relatív előnyeit ismerteti. Az ezekben a cikkekben említett "adatbázis" és "tároló" kifejezések a Cassandra API a "space" és a "Table" kifejezéssel jelennek meg.

Ennek a módszernek az az előnye, hogy ez egy egyszerű kulcsrakész módszer az adatátviteli kapacitás kezelésére az adatbázison. Azonban az a hátránya, hogy sok esetben a skálázási módszer a költséghatékony és a nagy teljesítményű automatizálás bizonyos szintjeire is szükség lehet. A következő szakasz ismerteti a kapcsolódó forgatókönyveket és metódusokat.

## <a name="use-the-control-plane"></a><a id="use-control-plane"></a>A vezérlési sík használata

A Cassandra Azure Cosmos DB API-ját lehetővé teszi az átviteli sebesség programozott módon történő módosítását a különböző vezérlő-sík funkciók használatával. Útmutatásért és példákért tekintse meg a [Azure Resource Manager](./templates-samples-cassandra.md), a [PowerShell](powershell-samples.md)és az [Azure CLI](cli-samples.md) -cikkeket.

Ennek a módszernek az az előnye, hogy automatizálhatja az erőforrások vertikális fel-vagy leskálázását egy időzítő alapján a csúcsérték-tevékenységhez vagy az alacsony aktivitású időszakokhoz. Tekintse meg [a mintát a Azure functions és a PowerShell](https://github.com/Azure-Samples/azure-cosmos-throughput-scheduler) használatával.

Ennek a megközelítésnek a hátránya az lehet, hogy valós időben nem lehet reagálni a kiszámíthatatlan változó méretezési igényekre. Ehelyett előfordulhat, hogy az alkalmazás kontextusát kell használnia a rendszeren, az ügyfél/SDK szintjén, vagy az [autoscale](provision-throughput-autoscale.md)használatával.

## <a name="use-cql-queries-with-a-specific-sdk"></a><a id="use-cql-queries"></a>CQL-lekérdezések használata egy adott SDK-val

A rendszer dinamikusan méretezhető a kóddal úgy, hogy végrehajtja a [CQL Alter parancsait](cassandra-support.md#keyspace-and-table-options) az adott adatbázishoz vagy tárolóhoz.

Ennek a megközelítésnek az az előnye, hogy lehetővé teszi, hogy dinamikusan és egyéni módon válaszoljon a méretezési igényekre. Ezzel a megközelítéssel továbbra is kihasználhatja a standard RU/s díját és díjszabását. Ha a rendszer méretezési igényei többnyire előre jelezhető (körülbelül 70% vagy több), az SDK és a CQL használata költséghatékonyabb módszer lehet, mint az automatikus méretezés használata. Ennek a megközelítésnek a hátránya, hogy az újrapróbálkozások megvalósítása meglehetősen bonyolult lehet, miközben a díjszabás megnövelheti a késést.

## <a name="use-autoscale-provisioned-throughput"></a><a id="use-autoscale"></a>Az autoscale kiosztott átviteli sebessége

A standard (manuális) vagy az átviteli sebesség programozott módján kívül az Azure Cosmos-tárolókat is konfigurálhatja az Automatikus méretezéssel kiosztott átviteli sebességben. Az automatikus méretezés automatikusan és azonnal méretezhető a megadott RU-tartományokon belül, a SLA-kat nem veszélyeztetve. További információ: [Azure Cosmos-tárolók és-adatbázisok létrehozása az autoscale](provision-throughput-autoscale.md) cikkben.

Ennek a megközelítésnek az az előnye, hogy ez a legegyszerűbb módszer a méretezési igények kezelésére a rendszeren. **A beállított ru-tartományokon belül** nem fogja alkalmazni a díjszabási korlátozásokat. A hátránya az, hogy ha a rendszer skálázási igénye előre jelezhető, az automatikus skálázás kevésbé költséghatékony módja a skálázási igények kezelésének, mint a fent említett, a testre szabott vezérlési sík vagy az SDK-szintű megoldások használata.

Ha a maximális átviteli sebességet (RUs) az CQL használatával szeretné beállítani vagy módosítani, használja a következőt (a térköz/táblanév helyett):

```Bash
# to set max throughput (RUs) for autoscale at keyspace level:
create keyspace <keyspace name> WITH cosmosdb_autoscale_max_throughput=5000;

# to alter max throughput (RUs) for autoscale at keyspace level:
alter keyspace <keyspace name> WITH cosmosdb_autoscale_max_throughput=4000;

# to set max throughput (RUs) for autoscale at table level:
create table <keyspace name>.<table name> (pk int PRIMARY KEY, ck int) WITH cosmosdb_autoscale_max_throughput=5000;

# to alter max throughput (RUs) for autoscale at table level:
alter table <keyspace name>.<table name> WITH cosmosdb_autoscale_max_throughput=4000;
```

## <a name="next-steps"></a>Következő lépések

- Ismerkedés a [Cassandra API-fiókok, -adatbázisok és -táblák létrehozásával](create-cassandra-api-account-java.md) Java-alkalmazás használatával