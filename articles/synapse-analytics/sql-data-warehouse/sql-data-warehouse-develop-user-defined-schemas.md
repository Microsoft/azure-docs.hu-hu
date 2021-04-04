---
title: Felhasználó által definiált sémák használata
description: Tippek a T-SQL felhasználó által definiált sémák használatához a dedikált SQL-készletek megoldásainak fejlesztéséhez az Azure szinapszis Analyticsben.
services: synapse-analytics
author: MSTehrani
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 04/17/2018
ms.author: emtehran
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: 3204c77dd076d9aac6eb5a60b489280caefcbf4b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96460458"
---
# <a name="user-defined-schemas-for-dedicated-sql-pools-in-azure-synapse-analytics"></a>Felhasználó által definiált sémák dedikált SQL-készletekhez az Azure szinapszis Analyticsben
Ez a cikk a T-SQL felhasználó által definiált sémák használatának számos tippjét ismerteti a dedikált SQL-készletben lévő megoldások fejlesztéséhez.

## <a name="schemas-for-application-boundaries"></a>Az alkalmazás határaihoz tartozó sémák

A hagyományos adattárházak gyakran külön adatbázisokat használnak az alkalmazások határainak a munkaterhelés, a tartomány vagy a biztonság alapján történő létrehozásához. 

Egy hagyományos SQL Server adattárház például tartalmazhat átmeneti adatbázist, adattárház-adatbázist és néhány data mart adatbázist. Ebben a topológiában minden adatbázis munkaterhelésként és biztonsági határként működik az architektúrában.

Ezzel szemben a dedikált SQL-készlet egy adatbázison belül futtatja a teljes adattárház-munkafolyamatot. Az adatbázison keresztüli illesztések nem engedélyezettek. A dedikált SQL-készlet arra vár, hogy a raktár által használt összes tábla egy adatbázison belül legyen tárolva.

> [!NOTE]
> Az SQL-készlet nem támogatja a bármilyen típusú adatbázisok lekérdezését. Ennek következtében az ezt a mintát használó adatraktár-implementációkat felül kell vizsgálni.
> 
> 

## <a name="recommendations"></a>Javaslatok
A következő javaslatok a számítási feladatok, a biztonság, a tartomány és a funkcionális határok konszolidálása felhasználó által definiált sémák használatával:

- Egy dedikált SQL-készletben lévő adatbázis használatával futtathatja a teljes adattárház-munkafolyamatot.
- Konszolidálja meglévő adattárház-környezetét egy dedikált SQL Pool-adatbázis használatához.
- **Felhasználó által definiált sémák** használata a korábban adatbázisokkal megvalósított határ biztosításához.

Ha korábban nem használták a felhasználó által definiált sémákat, akkor tiszta lappal rendelkezik. Használja a régi adatbázis nevét a felhasználó által definiált sémák alapjául a dedikált SQL Pool-adatbázisban.

Ha a sémák már használatban vannak, akkor néhány lehetőség közül választhat:

- Távolítsa el a régi sémák nevét, és kezdje el a frissen.
- Tartsa meg a régi séma nevét az örökölt séma neve alapján a tábla nevére.
- Tartsa meg a régi sémák nevét úgy, hogy egy további sémában lévő nézeteket implementál, hogy újra létrehozza a régi séma-struktúrát.

> [!NOTE]
> Előfordulhat, hogy a 3. első ellenőrzési lehetőség a leginkább vonzó megoldásnak tűnik. Az ördög azonban részletesen szerepel. A nézetek csak a dedikált SQL-készletben olvashatók. Az alaptáblán minden adat-vagy tábla-módosítást el kell végezni. A 3. lehetőség is bevezeti a nézet rétegét a rendszerbe. Érdemes lehet ezt néhány további gondolatot használni, ha már használja a nézeteket az architektúrában.
> 
> 

### <a name="examples"></a>Angol nyelvű Példák:
Felhasználó által definiált sémák implementálása az adatbázis neve alapján:

```sql
CREATE SCHEMA [stg]; -- stg previously database name for staging database
GO
CREATE SCHEMA [edw]; -- edw previously database name for the data warehouse
GO
CREATE TABLE [stg].[customer] -- create staging tables in the stg schema
(       CustKey BIGINT NOT NULL
,       ...
);
GO
CREATE TABLE [edw].[customer] -- create data warehouse tables in the edw schema
(       CustKey BIGINT NOT NULL
,       ...
);
```

A régi séma neveit a táblázat nevére való előre függőben tarthatja. Sémák használata a munkaterhelés határához:

```sql
CREATE SCHEMA [stg]; -- stg defines the staging boundary
GO
CREATE SCHEMA [edw]; -- edw defines the data warehouse boundary
GO
CREATE TABLE [stg].[dim_customer] --pre-pend the old schema name to the table and create in the staging boundary
(       CustKey BIGINT NOT NULL
,       ...
);
GO
CREATE TABLE [edw].[dim_customer] --pre-pend the old schema name to the table and create in the data warehouse boundary
(       CustKey BIGINT NOT NULL
,       ...
);
```

Örökölt sémák nevének megtartása nézetek használatával:

```sql
CREATE SCHEMA [stg]; -- stg defines the staging boundary
GO
CREATE SCHEMA [edw]; -- stg defines the data warehouse boundary
GO
CREATE SCHEMA [dim]; -- edw defines the legacy schema name boundary
GO
CREATE TABLE [stg].[customer] -- create the base staging tables in the staging boundary
(       CustKey    BIGINT NOT NULL
,       ...
)
GO
CREATE TABLE [edw].[customer] -- create the base data warehouse tables in the data warehouse boundary
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
> A séma-stratégiában bekövetkező változásoknak át kell tekinteniük az adatbázis biztonsági modelljét. Sok esetben lehet, hogy a séma szintjén rendeli hozzá az engedélyeket a biztonsági modell egyszerűsítéséhez. Ha további részletességi engedélyekre van szükség, akkor használhatja az adatbázis-szerepköröket.
> 
> 

## <a name="next-steps"></a>Következő lépések
További fejlesztési tippek: a [fejlesztés áttekintése](sql-data-warehouse-overview-develop.md).

