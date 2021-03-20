---
title: Azure Cosmos DBi egységre vonatkozó díjak kérése kulcs értékű tárolóként
description: Az egyszerű írási és olvasási műveletekre vonatkozó kérések egységének díja Azure Cosmos DB, ha kulcs/érték tárolóként van használatban.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 08/23/2019
ms.author: sngun
ms.custom: seodec18
ms.openlocfilehash: 9354ae0a22ef2e8ab4ee6a57563d3f3c4c8e4547
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "93339302"
---
# <a name="azure-cosmos-db-as-a-key-value-store--cost-overview"></a>Azure Cosmos DB Key Value Store-ban – Cost Overview
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

A Azure Cosmos DB egy globálisan elosztott, többmodelles adatbázis-szolgáltatás, amellyel könnyen készíthetők magas rendelkezésre állású, nagy méretű alkalmazások. Alapértelmezés szerint a Azure Cosmos DB automatikusan és hatékonyan indexeli az összes betöltött adatot. Ez gyors és konzisztens [SQL](./sql-query-getting-started.md) -(és [JavaScript](stored-procedures-triggers-udfs.md)-) lekérdezéseket tesz lehetővé az adatkezeléshez. 

Ez a cikk az egyszerű írási és olvasási műveletek Azure Cosmos DB költségeit ismerteti, ha kulcs/érték tárolóként van használatban. Az írási műveletek közé tartoznak az adatelemek beszúrása, cseréje, törlése és upsert. A többrégiós fiókok esetében a 99,999%-os rendelkezésre állási SLA garantálása mellett Azure Cosmos DB a esetek 99% percentilis esetében garantált <10 – MS késést biztosít az olvasási és a (indexelt) írásokhoz. 

## <a name="why-we-use-request-units-rus"></a>Miért használjuk a kérelmek egységeit (RUs)

Azure Cosmos DB teljesítmény a [kérelmek egységében](request-units.md) (ru/s) kifejezett kiépített átviteli sebességen alapul. A kiépítés második részletességgel történik, és RU/s-ben vásárolható meg ([nem tévesztendő össze az óránkénti számlázással](https://azure.microsoft.com/pricing/details/cosmos-db/)). Az RUs logikai absztrakciónak (pénznemnek) tekintendő, amely leegyszerűsíti az alkalmazás szükséges átviteli sebességének kiépítési folyamatát. A felhasználóknak nem kell meggondolnia az olvasási és írási sebesség közötti különbségtételt. Az RUs egyvalutás modellje hatékonyságot hoz létre az olvasási és írási műveletek közötti kiosztott kapacitás megosztásához. Ez a kiépített kapacitási modell lehetővé teszi, hogy a szolgáltatás **kiszámítható és konzisztens teljesítményt, garantált kis késést és magas rendelkezésre állást** biztosítson. Végül, míg az RU-modell az átviteli sebességet ábrázolja, minden egyes kiépített RU-nek meghatározott mennyiségű erőforrása van (például memória, magok/CPU és IOPS).

Globálisan elosztott adatbázisrendszer a Cosmos db az egyetlen Azure-szolgáltatás, amely átfogó SLA-kat biztosít a késés, az átviteli sebesség, a konzisztencia és a magas rendelkezésre állás érdekében. A rendszer az Ön által kiépített átviteli sebességet alkalmazza a Cosmos-fiókhoz társított összes régióra. Az olvasások Cosmos DB több, jól definiált [konzisztencia-szintet](consistency-levels.md) kínál, amelyek közül választhat. 

A következő táblázat az olvasási és írási műveletek végrehajtásához szükséges RUs számát mutatja az 1 KB-os és a 100-os Tudásbázis adatelem alapján, az alapértelmezett automatikus indexelés kikapcsolva. 

|Elemek mérete|1 olvasás|1 írás|
|-------------|------|-------|
|1 KB|1 RU|5 RUs|
|100 KB|10 RU|50 RUs|

## <a name="cost-of-reads-and-writes"></a>Olvasási és írási díj

Ha 1 000 RU/s-t hoz létre, ez a 3 600 000 RU/óra értékre vonatkozik, és az órát (az USA és Európa területén) $0,08 számítjuk fel. 1 KB méretű adatelem esetén ez azt jelenti, hogy 3 600 000 olvasási vagy 720 000 írást (3 600 000 RU/5) használhat a kiépített átviteli sebesség használatával. Az olvasások és írások normalizálása a következő lesz: $0,022/millió ($0,08/3,6) és $0.111/millió írási érték ($0,08/0,72). A Cost/millió az alábbi táblázatban látható minimális.

|Elemek mérete|1 000 000 olvasás díja|1 000 000 írási díja|
|-------------|-------|--------|
|1 KB|$0,022|$0,111|
|100 KB|$0,222|$1,111|


Az alapszintű Blobok vagy objektumok többsége a $0,40/millió olvasási tranzakció és a $5/millió írási tranzakció díjait tárolja. Optimális használat esetén Cosmos DB akár 98%-kal olcsóbb lehet, mint a többi megoldásnál (1 KB-os tranzakció esetén).

## <a name="next-steps"></a>Következő lépések

* Az [ru-kalkulátor](https://cosmos.azure.com/capacitycalculator/) használatával becsülheti meg a számítási feladatok átviteli sebességét.