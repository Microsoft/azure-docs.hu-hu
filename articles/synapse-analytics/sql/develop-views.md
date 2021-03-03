---
title: SQL-készleteket használó T-SQL-nézetek
description: Tippek a T-SQL-nézetek használatához és a megoldások fejlesztése dedikált SQL-készlettel és kiszolgáló nélküli SQL-készlettel az Azure szinapszis Analyticsben.
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 04/15/2020
ms.author: stefanazaric
ms.reviewer: jrasnick
ms.openlocfilehash: 9f52c3fd1284ce7e55680d051c5292361067fad9
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/02/2021
ms.locfileid: "101674001"
---
# <a name="t-sql-views-with-dedicated-sql-pool-and-serverless-sql-pool-in-azure-synapse-analytics"></a>T-SQL-nézetek dedikált SQL-készlettel és kiszolgáló nélküli SQL-készlettel az Azure szinapszis Analyticsben

Ebből a cikkből megtudhatja, hogyan használhatja a T-SQL-nézeteket, és hogyan fejleszthet megoldásokat dedikált SQL-készlettel és kiszolgáló nélküli SQL-készlettel az Azure szinapszis Analytics szolgáltatásban.

## <a name="why-use-views"></a>A nézetek használata

A nézetek számos különböző módon használhatók a megoldás minőségének javítására.  Ez a cikk néhány példát mutat be arra, hogyan gazdagíthatja a megoldásait nézetekkel, és tartalmazza a megfontolandó korlátozásokat.

### <a name="sql-pool---create-view"></a>SQL-készlet – nézet létrehozása

> [!NOTE]
> Ez a cikk nem tárgyalja a létrehozás nézet szintaxisát. További információ: [create View](/sql/t-sql/statements/create-view-transact-sql?view=azure-sqldw-latest&preserve-view=true) dokumentáció.

## <a name="architectural-abstraction"></a>Építészeti absztrakció

Gyakori alkalmazási minta a táblák újbóli létrehozása a [CREATE TABLE as Select](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?view=azure-sqldw-latest&preserve-view=true) (CTAS) használatával, amelyet egy objektum átnevezési mintája követ az adatbetöltése során.

A következő példa új dátum rekordokat vesz fel egy dátum dimenzióba. Vegye figyelembe, hogy egy új tábla, DimDate_New, először jön létre, majd átnevezve lett a tábla eredeti verziójának helyére.

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

Ne feledje, hogy ez a megközelítés azt eredményezheti, hogy a táblák megjelenhetnek és eltűnnek a felhasználó nézetében, és a "táblázat nem létezik" hibaüzenetek jelennek meg. A nézetek segítségével konzisztens megjelenítési réteget biztosíthat a felhasználók számára, miközben az alapul szolgáló objektumok átnevezve lesznek.

Azáltal, hogy a nézetekben hozzáférést biztosít az adathozzáféréshez, a felhasználóknak nincs szükségük a mögöttes táblák láthatóságára. Az egységes felhasználói élmény mellett ez a réteg biztosítja, hogy az elemzési tervezők is fejlődjön az adatmodellben. Az alapul szolgáló táblák fejlesztése azt jelenti, hogy a tervezők a CTAS használatával maximalizálják a teljesítményt az betöltési folyamat során.

## <a name="performance-optimization"></a>Teljesítményoptimalizálás

A nézetek a táblázatok közötti teljesítményre optimalizált illesztések betartatására is használhatók. Egy nézet például belefoglalhatja a redundáns terjesztési kulcsot a csatlakozás feltételeinek részeként az adatáthelyezés csökkentése érdekében.

Egy adott lekérdezés kényszerítése vagy a tipphez való csatlakozás másik előnye a T-SQL-nézetek használata. Így a nézetek funkció biztosítja, hogy az illesztések mindig optimális módon legyenek elvégezve. Ne feledje, hogy a felhasználóknak emlékezniük kell a megfelelő szerkezetre az illesztésekhez.

## <a name="limitations"></a>Korlátozások

A szinapszis SQL nézetei csak metaadatokként vannak tárolva. Ennek következtében a következő beállítások nem érhetők el:

* Nincs séma-kötési beállítás
* Az alaptáblák nem frissíthetők a nézeten keresztül
* Ideiglenes táblákon nem hozhatók létre nézetek
* A kibontási/kibontási útmutatók nem támogatottak
* Nincsenek indexelt nézetek a szinapszis SQL-ben

## <a name="next-steps"></a>Következő lépések

További fejlesztési tippek: a [SZINAPSZIS SQL-fejlesztés áttekintése](develop-overview.md).



