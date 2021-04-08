---
title: Felhasználó által definiált sémák a szinapszis SQL-en belül
description: Az alábbi részekben különböző tippeket talál a T-SQL felhasználó által definiált sémák használatával az Azure szinapszis Analytics szinapszis SQL képességével rendelkező megoldások fejlesztéséhez.
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 04/15/2020
ms.author: stefanazaric
ms.reviewer: jrasnick
ms.openlocfilehash: 6920d6cd190927747a69fc4dc0f0791278665abe
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96451689"
---
# <a name="user-defined-schemas-within-synapse-sql"></a>Felhasználó által definiált sémák a szinapszis SQL-en belül

Az alábbi szakaszban különböző tippeket talál a T-SQL felhasználó által definiált sémák használatával a szinapszis SQL-en belüli megoldások fejlesztéséhez.

## <a name="schemas-for-application-boundaries"></a>Az alkalmazás határaihoz tartozó sémák

A hagyományos elemzési architektúra gyakran külön adatbázisokat használ az alkalmazások határainak a munkaterhelés, a tartomány vagy a biztonság alapján történő létrehozásához. Előfordulhat például, hogy egy hagyományos SQL Server elemzési infrastruktúra tartalmaz egy átmeneti adatbázist, egy elemzési adatbázist és data mart-adatbázist. Ebben a topológiában minden adatbázis munkaterhelésként és biztonsági határként működik az architektúrában.

Ehelyett a szinapszis SQL egy adatbázison belül futtatja a teljes elemzési munkafolyamatot. Az adatbázison keresztüli illesztések nem engedélyezettek. A szinapszis SQL elvárja, hogy a raktár által használt összes tábla az egy adatbázisban legyen tárolva.

> [!NOTE]
> A dedikált SQL-készletek nem támogatják a különböző típusú adatbázisok lekérdezéseit. Ennek következtében az ezt a mintát használó elemzési implementációkat felül kell vizsgálni. A kiszolgáló nélküli SQL-készlet támogatja az adatbázison keresztüli lekérdezéseket.

## <a name="user-defined-schema-recommendations"></a>Felhasználó által definiált séma-javaslatok

A következőket tartalmazza a számítási feladatok, a biztonság, a tartomány és a funkcionális határok konszolidálása felhasználó által definiált sémák használatával:

- Egyetlen adatbázis használatával futtathatja a teljes elemzési munkaterhelést.
- Egyesítse meglévő elemzési környezetét egyetlen adatbázis használatára.
- **Felhasználó által definiált sémák** használata a korábban adatbázisokkal megvalósított határ biztosításához.

Ha a felhasználó által definiált sémákat korábban nem használták, akkor tiszta lappal rendelkezik. Használja a régi adatbázis nevét a felhasználó által definiált sémák alapjául a szinapszis SQL Database-ben.

Ha a sémák már használatban vannak, akkor néhány lehetőség közül választhat:

- Távolítsa el a régi sémák nevét, és kezdje el a frissen
- A régi séma nevének megtartása előre függőben a régi séma nevének a tábla nevére
- Megtarthatja a régi sémák nevét úgy, hogy a táblázat egy további sémán belüli nézeteit implementálja, ami újra létrehozza a régi séma-struktúrát.

> [!NOTE]
> Az első vizsgálat során a 3. lehetőség úgy tűnik, hogy a leginkább vonzó választás. A nézetek csak a szinapszis SQL-ben olvashatók. Az alaptáblán minden adat-vagy tábla-módosítást el kell végezni. A 3. lehetőség is bevezeti a nézet rétegét a rendszerbe. Érdemes lehet ezt néhány további gondolatot megadni, ha már használja a nézeteket az architektúrában.
> 
> 

### <a name="examples"></a>Példák

Felhasználó által definiált sémák implementálása az adatbázisok nevei alapján.

```sql
CREATE SCHEMA [stg]; -- stg previously database name for staging database
GO
CREATE SCHEMA [edw]; -- edw previously database name for the analytics
GO
CREATE TABLE [stg].[customer] -- create staging tables in the stg schema
(       CustKey BIGINT NOT NULL
,       ...
);
GO
CREATE TABLE [edw].[customer] -- create analytics tables in the edw schema
(       CustKey BIGINT NOT NULL
,       ...
);
```

Tartsa meg a régi sémák nevét a tábla nevére előre függőben. Sémák használata a munkaterhelés határához.

```sql
CREATE SCHEMA [stg]; -- stg defines the staging boundary
GO
CREATE SCHEMA [edw]; -- edw defines the analytics boundary
GO
CREATE TABLE [stg].[dim_customer] --pre-pend the old schema name to the table and create in the staging boundary
(       CustKey BIGINT NOT NULL
,       ...
);
GO
CREATE TABLE [edw].[dim_customer] --pre-pend the old schema name to the table and create in the analytics boundary
(       CustKey BIGINT NOT NULL
,       ...
);
```

Megőrzi a régi sémák nevét a nézetek használatával.

```sql
CREATE SCHEMA [stg]; -- stg defines the staging boundary
GO
CREATE SCHEMA [edw]; -- stg defines the analytics boundary
GO
CREATE SCHEMA [dim]; -- edw defines the legacy schema name boundary
GO
CREATE TABLE [stg].[customer] -- create the base staging tables in the staging boundary
(       CustKey    BIGINT NOT NULL
,       ...
)
GO
CREATE TABLE [edw].[customer] -- create the base analytics tables in the analytics boundary
(       CustKey    BIGINT NOT NULL
,       ...
)
GO
CREATE VIEW [dim].[customer] -- create a view in the legacy schema name boundary for presentation consistency purposes only
AS
SELECT  CustKey
,       ...
FROM    [edw].customer
;
```

> [!NOTE]
> A séma-stratégiában bekövetkező változásoknak az adatbázis biztonsági modelljének felülvizsgálatára van szükségük. Sok esetben lehet, hogy a séma szintjén rendeli hozzá az engedélyeket a biztonsági modell egyszerűsítéséhez.

Ha további részletességi engedélyek szükségesek, akkor használhatja az adatbázis-szerepköröket. Az adatbázis-szerepkörökkel kapcsolatos további információkért tekintse meg az [adatbázis-szerepkörök és-felhasználók kezelése](../../analysis-services/analysis-services-database-users.md) című cikket.

## <a name="next-steps"></a>Következő lépések

További fejlesztési tippek: a [SZINAPSZIS SQL-fejlesztés áttekintése](develop-overview.md).
