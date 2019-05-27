---
title: T-SQL-nézetek használata az Azure SQL Data Warehouse |} A Microsoft Docs
description: Tippek a T-SQL-nézetek használata az Azure SQL Data Warehouse használható megoldások fejlesztéséhez.
services: sql-data-warehouse
author: XiaoyuL-Preview
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: development
ms.date: 04/17/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.openlocfilehash: d4321f8aef6e754d8a1c5b16ac82b4fa62c40949
ms.sourcegitcommit: 4c2b9bc9cc704652cc77f33a870c4ec2d0579451
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/17/2019
ms.locfileid: "65873611"
---
# <a name="views-in-azure-sql-data-warehouse"></a>Az Azure SQL Data Warehouse nézetek
Tippek a T-SQL-nézetek használata az Azure SQL Data Warehouse használható megoldások fejlesztéséhez. 

## <a name="why-use-views"></a>Nézetek miért érdemes használni?
Nézetek használható számos különböző módon, hogy a megoldás minőségének javítása érdekében.  Ez a cikk bemutatja, hogyan bővítheti megoldását-nézetek, valamint a korlátozásokat, amelyek figyelembe kell venni néhány példa emeli ki.

> [!NOTE]
> A CREATE VIEW szintaxisa nem a cikkben leírtak szerint. További információkért lásd: a [NÉZET létrehozása](/sql/t-sql/statements/create-view-transact-sql) dokumentációját.
> 
> 

## <a name="architectural-abstraction"></a>Architekturális absztrakciós
Gyakori alkalmazásminta az táblák használatával hozzon létre TABLE AS SELECT (CTAS) egy objektum minta átnevezése, miközben az adatok betöltése után hozza létre újból.

A következő példa új dátumrekord hozzáadja a dátum dimenzió. Vegye figyelembe, hogyan DimDate_New, új tábla létrehozásakor és majd lecseréli az eredeti verzió a tábla neve.

```sql
CREATE TABLE dbo.DimDate_New
WITH (DISTRIBUTION = ROUND_ROBIN
, CLUSTERED INDEX (DateKey ASC)
)
AS
SELECT *
FROM   dbo.DimDate  AS prod
UNION ALL
SELECT *
FROM   dbo.DimDate_stg AS stg
;

RENAME OBJECT DimDate TO DimDate_Old;
RENAME OBJECT DimDate_New TO DimDate;

```

Ez a megközelítés azonban eredményezhet táblák jelennek meg, és a egy felhasználó megtekintése, valamint a "tábla nem létezik" hibaüzenet eltűnik. Nézetek segítségével biztosíthatja a felhasználók számára egy egységes megjelenítési réteg végrehajtása közben a rendszer átnevezi a mögöttes objektumokat. Azáltal, hogy a nézetek adatokhoz való hozzáférését, a felhasználóknak nem kell az alapul szolgáló táblák láthatóságát. Ez a réteg egységes felhasználói élmény biztosítása, hogy az adatraktár tervezők tovább lehessen fejleszteni az adatmodell biztosít. Képes való összpontosításnak köszönhetően az alapul szolgáló táblák azt jelenti, hogy a tervezők a CTAS segítségével maximalizálhatja a teljesítményt az Adatbetöltési folyamat során.   

## <a name="performance-optimization"></a>Teljesítmény optimalizálása
Nézetek kényszerítéséhez teljesítményre optimalizált illesztések táblák között is fel lehet használni. Ha például nézet beépítheti a redundáns terjesztési kulcs adatáthelyezés minimalizálása érdekében a csatlakozó feltétel részeként. Egy másik előnye, hogy a nézet egy adott lekérdezés vagy csatlakozó mutató kényszerített lehet. Nézetek használata ezen a módon kikapcsolja az garantálja, hogy összekapcsolások mindig megtörténik a felhasználók számára ne felejtse el a megfelelő szerkezet számára az illesztések nem kell optimális el.

## <a name="limitations"></a>Korlátozások
Az SQL Data Warehouse nézetek csak a metaadatokat, tárolódnak. Ennek következtében a következő beállítások nem érhetők el:

* Nem kötelező beállítani a séma
* Alaptáblát nem lehet frissíteni a nézeten keresztül
* Nézetek nem hozható létre az ideiglenes táblák
* Nem támogatott a a KIBONTÁS / NOEXPAND mutató
* Nincsenek nem indexelt nézetek az SQL Data warehouse-bA

## <a name="next-steps"></a>További lépések
További fejlesztési tippek: [SQL Data Warehouse fejlesztői áttekintés](sql-data-warehouse-overview-develop.md).


