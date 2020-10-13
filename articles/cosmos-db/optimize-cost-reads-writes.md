---
title: Az olvasási és írási díj optimalizálása Azure Cosmos DB
description: Ez a cikk azt ismerteti, hogyan csökkenthető a Azure Cosmos DB költségek az olvasási és írási műveletek végrehajtásakor az adatokon.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/24/2020
ms.openlocfilehash: 38084bf30df2a597e7a7bc46ba4c52cf371c3c7e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "87318249"
---
# <a name="optimize-reads-and-writes-cost-in-azure-cosmos-db"></a>Olvasási és írási díjak optimalizálása Azure Cosmos DB

Ez a cikk azt ismerteti, hogy a rendszer hogyan számítja ki a Azure Cosmos DB adatok olvasásához és írásához szükséges költségeket. Olvasási műveletek: [pont olvasások és lekérdezések](sql-query-getting-started.md). Az írási műveletek közé tartozik az elemek beszúrása, cseréje, törlése és upsert.  

## <a name="cost-of-reads-and-writes"></a>Olvasási és írási díj

Azure Cosmos DB garantálja a kiszámítható teljesítményt az átviteli sebesség és a késés tekintetében egy kiépített átviteli sebességi modell használatával. A kiépített átviteli sebesség a [kérelmek](request-units.md) száma másodpercenként, vagy ru/s. A kérési egység (RU) olyan számítási erőforrások (például CPU, memória, IO stb.) logikai absztrakciója, amelyek szükségesek a kérelem végrehajtásához. A kiépített átviteli sebesség (RUs) be van állítva, és a tárolóra vagy az adatbázisra van beállítva, hogy kiszámítható teljesítményt és késést biztosítson. A kiépített átviteli sebesség lehetővé teszi, hogy a Azure Cosmos DB kiszámítható és konzisztens teljesítményt, garantált alacsony késést és magas rendelkezésre állást biztosítson bármilyen méretben. A kérelmek egységei a normalizált pénznemet jelentik, amely leegyszerűsíti az alkalmazás által igényelt erőforrások számának okát.

Az olvasási és írási műveletek között nem kell megkülönböztetnie a kérelmek egységeit. A kérelmek egységének egyesített pénznem-modellje a beolvasások és az írások esetében is ugyanazt az átviteli kapacitást használja. Az alábbi táblázat a pontok olvasását és írását mutatja be az RU/s értéknél az 1 KB-os és 100 KB méretű elemek esetében.

|**Elemek mérete**  |**Egy pont beolvasott díja** |**Egy írás díja**|
|---------|---------|---------|
|1 KB |1 RU |5 RUs |
|100 KB |10 RU |50 RUs |

Egy olyan pont olvasásakor, amely 1 KB méretű, egy RU-t vesz igénybe. Egy 1 KB-os, öt RUs értékű elemek írása. Az olvasási és írási költségek akkor érvényesek, ha az alapértelmezett munkamenet- [konzisztenciai szintet](consistency-levels.md)használja.  A következő szempontokat kell figyelembe venni: az elemek mérete, a tulajdonságok száma, az adatkonzisztencia, az indexelt tulajdonságok, az indexelés és a lekérdezési minták.

A [pont olvasási](sql-query-getting-started.md) díja jóval kevesebb, mint a lekérdezések. A pontok olvasási, a lekérdezésekkel ellentétben nem kell a lekérdezési motor használatával hozzáférni az adatelérési ponthoz. Az RU-díj lekérdezése a lekérdezés összetettségétől és a betöltéshez szükséges elemek számától függ.

## <a name="optimize-the-cost-of-writes-and-reads"></a>Az írási és olvasási díj optimalizálása

Írási műveletek végrehajtásakor elegendő kapacitást kell kiépíteni a másodpercenkénti írások számának támogatásához. Az írások végrehajtása előtt növelheti a kiépített átviteli sebességet az SDK, a portál, a CLI használatával, majd az írások befejezése után csökkentheti az átviteli sebességet. Az írási időszakra vonatkozó átviteli sebesség a megadott adatokhoz szükséges minimális átviteli sebesség, valamint a munkaterhelések beszúrásához szükséges átviteli sebesség, feltéve, hogy más számítási feladatok nem futnak.

Ha egyidejűleg más munkaterheléseket futtat, például lekérdezés/olvasás/frissítés/törlés, adja hozzá a műveletekhez szükséges további kérési egységeket is. Ha az írási műveletek száma korlátozott, az újrapróbálkozási/leállítási szabályzatot Azure Cosmos DB SDK-k használatával szabhatja testre. Megnövelheti például a terhelést, amíg a kisebb kérelmek aránya korlátozott. Ha a díjszabási korlát történik, az ügyfélalkalmazás a megadott újrapróbálkozási időintervallumra vonatkozó kérelmek korlátozására vonatkozó kérések miatt vissza kell térnie. Az újrapróbálkozások megkezdése előtt az újrapróbálkozások között minimális idő alatt kell állnia. Az újrapróbálkozási szabályzat támogatása az SQL .NET, a Java, a Node.js és a Python SDK-k, valamint a .NET Core SDK-k összes támogatott verziója része.

Az adatok tömegesen szúrhatók be a Azure Cosmos DBba, vagy bármely támogatott forrásból származó adatok másolása a [Azure Data Factory](../data-factory/connector-azure-cosmos-db.md)használatával Azure Cosmos db. A Azure Data Factory natív módon integrálható a Azure Cosmos DB tömeges API-val a legjobb teljesítmény biztosításához, amikor adatírást végez.

## <a name="next-steps"></a>További lépések

A következő cikkekben további tudnivalókat talál a Azure Cosmos DB a Cost optimizationról:

* További információ a [fejlesztés és a tesztelés optimalizálásáról](optimize-dev-test.md)
* További információ [a Azure Cosmos db-számla megismeréséről](understand-your-bill.md)
* További információ az [átviteli sebesség optimalizálásáról](optimize-cost-throughput.md)
* További információ a [tárolási díjak optimalizálásáról](optimize-cost-storage.md)
* További információ [a lekérdezések díjszabásának optimalizálásáról](optimize-cost-queries.md)
* További információ [a több régióból álló Azure Cosmos-fiókok díjainak optimalizálásáról](optimize-cost-regions.md)
