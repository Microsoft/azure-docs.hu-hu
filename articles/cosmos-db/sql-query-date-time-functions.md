---
title: Dátum és idő függvények Azure Cosmos DB lekérdezési nyelven
description: A dátum-és időértékek az SQL rendszerfunkcióinak megismerése Azure Cosmos DB a DateTime és az timestamp művelet végrehajtásához.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/18/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: a7f19a7c3cc7337f0e1c53cf387abb35b96d1e38
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/30/2020
ms.locfileid: "93100320"
---
# <a name="date-and-time-functions-azure-cosmos-db"></a>Dátum és idő függvények (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

A dátum-és időfüggvények lehetővé teszik a DateTime és timestamp típusú műveletek végrehajtását Azure Cosmos DBban.

## <a name="functions-to-obtain-the-date-and-time"></a>A dátum és idő beszerzéséhez szükséges függvények

A következő skaláris függvények lehetővé teszik az aktuális UTC dátum és idő lekérését három formában: egy olyan karakterláncot, amely megfelel az ISO 8601 formátumnak, egy numerikus időbélyegnek, amelynek értéke a UNIX EPOCH óta eltelt ezredmásodpercek száma, illetve numerikus osztásjelek száma, amelynek értéke a UNIX-kor óta eltelt 100 ns. :

* [GetCurrentDateTime](sql-query-getcurrentdatetime.md)
* [GetCurrentTimestamp](sql-query-getcurrenttimestamp.md)
* [GetCurrentTicks](sql-query-getcurrentticks.md)

## <a name="functions-to-work-with-datetime-values"></a>Függvények a DateTime értékekkel való munkavégzéshez

A következő függvények segítségével egyszerűen módosíthatja a DateTime, a timestamp és a Tick értékeket:

* [DateTimeAdd](sql-query-datetimeadd.md)
* [DateTimeDiff](sql-query-datetimediff.md)
* [DateTimeFromParts](sql-query-datetimefromparts.md)
* [DateTimePart](sql-query-datetimepart.md)
* [DateTimeToTicks](sql-query-datetimetoticks.md)
* [DateTimeToTimestamp](sql-query-datetimetotimestamp.md)
* [TicksToDateTime](sql-query-tickstodatetime.md)
* [TimestampToDateTime](sql-query-timestamptodatetime.md)

## <a name="next-steps"></a>Következő lépések

- [Rendszerfunkciók Azure Cosmos DB](sql-query-system-functions.md)
- [Az Azure Cosmos DB bemutatása](introduction.md)
- [Felhasználó által definiált függvények](sql-query-udfs.md)
- [Összesítések](sql-query-aggregates.md)
