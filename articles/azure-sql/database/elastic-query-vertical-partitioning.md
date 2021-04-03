---
title: Több felhőalapú adatbázis lekérdezése különböző sémákkal
description: több adatbázison alapuló lekérdezések beállítása függőleges partíciókon
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: MladjoA
ms.author: mlandzic
ms.reviewer: sstein
ms.date: 01/25/2019
ms.openlocfilehash: c507a4c618713ba83d25b9defa918092db1a3c8e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "92792089"
---
# <a name="query-across-cloud-databases-with-different-schemas-preview"></a>Különböző sémákkal rendelkező felhőalapú adatbázisok lekérdezése (előzetes verzió)
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

![Lekérdezés különböző adatbázisok táblái között][1]

A függőlegesen particionált adatbázisok különböző táblákat használnak különböző adatbázisokon. Ez azt jelenti, hogy a séma eltér a különböző adatbázisokon. Például a leltárhoz tartozó összes tábla egy adatbázison található, míg az összes nyilvántartással kapcsolatos tábla egy második adatbázisban található.

## <a name="prerequisites"></a>Előfeltételek

* A felhasználónak rendelkeznie kell a külső adatforrásra vonatkozó engedély megváltoztatásával. Ez az engedély az ALTER DATABASE engedély részét képezi.
* Az alapul szolgáló adatforrásra való hivatkozáshoz minden külső ADATFORRÁS engedélyére van szükség.

## <a name="overview"></a>Áttekintés

> [!NOTE]
> A horizontális particionálástól eltérően ezek a DDL-utasítások nem függenek az adatrétegnek a rugalmas adatbázis-ügyfél-függvénytáron keresztüli szegmenses leképezéssel való definiálásával.
>

1. [FŐKULCS LÉTREHOZÁSA](/sql/t-sql/statements/create-master-key-transact-sql)
2. [ADATBÁZIS-HATÓKÖRŰ HITELESÍTŐ ADAT LÉTREHOZÁSA](/sql/t-sql/statements/create-database-scoped-credential-transact-sql)
3. [KÜLSŐ ADATFORRÁS LÉTREHOZÁSA](/sql/t-sql/statements/create-external-data-source-transact-sql)
4. [KÜLSŐ TÁBLA LÉTREHOZÁSA](/sql/t-sql/statements/create-external-table-transact-sql)

## <a name="create-database-scoped-master-key-and-credentials"></a>Adatbázis-hatókörű főkulcs és hitelesítő adatok létrehozása

A rugalmas lekérdezés a hitelesítő adatokat használja a távoli adatbázisokhoz való kapcsolódáshoz.  

```sql
CREATE MASTER KEY ENCRYPTION BY PASSWORD = 'master_key_password';
CREATE DATABASE SCOPED CREDENTIAL <credential_name>  WITH IDENTITY = '<username>',  
SECRET = '<password>'
[;]
```

> [!NOTE]
> Győződjön meg arról, hogy a nem `<username>` tartalmazza a **" \@ servername"** utótagot.

## <a name="create-external-data-sources"></a>Külső adatforrások létrehozása

Szintaxis:

<External_Data_Source>:: = külső ADATFORRÁS létrehozása <data_source_name> a (TYPE = RDBMS, LOCATION = ' <fully_qualified_server_name> ', DATABASE_NAME = ' <remote_database_name> ',  
    HITELESÍTő adatok = <credential_name>) [;]

> [!IMPORTANT]
> A TYPE paramétert **RDBMS** értékre kell beállítani.

### <a name="example"></a>Példa

Az alábbi példa a CREATE utasítás külső adatforrásokra való használatát szemlélteti.

```sql
CREATE EXTERNAL DATA SOURCE RemoteReferenceData
   WITH
      (
         TYPE=RDBMS,
         LOCATION='myserver.database.windows.net',
         DATABASE_NAME='ReferenceData',
         CREDENTIAL= SqlUser
      );
```

Az aktuális külső adatforrások listájának beolvasása:

```sql
select * from sys.external_data_sources;
```

### <a name="external-tables"></a>Külső táblák

Szintaxis:

KÜLSŐ tábla létrehozása [database_name. [schema_name]. | schema_name. ] table_name  
    ({<column_definition>} [,... n]) {a következővel: (<rdbms_external_table_options>)}) [;]

<rdbms_external_table_options>:: = DATA_SOURCE = <External_Data_Source>, [SCHEMA_NAME = N ' nonescaped_schema_name ',] [OBJECT_NAME = N ' nonescaped_object_name ',]

### <a name="example"></a>Példa

```sql
CREATE EXTERNAL TABLE [dbo].[customer]
   (
      [c_id] int NOT NULL,
      [c_firstname] nvarchar(256) NULL,
      [c_lastname] nvarchar(256) NOT NULL,
      [street] nvarchar(256) NOT NULL,
      [city] nvarchar(256) NOT NULL,
      [state] nvarchar(20) NULL,
      DATA_SOURCE = RemoteReferenceData
   );
```

Az alábbi példa bemutatja, hogyan kérhető le a külső táblák listája az aktuális adatbázisból:

```sql
select * from sys.external_tables;
```

### <a name="remarks"></a>Megjegyzések

A rugalmas lekérdezés kiterjeszti a meglévő külső tábla szintaxisát, hogy meghatározza a RDBMS típusú külső adatforrásokat használó külső táblákat. A vertikális particionáláshoz tartozó külső tábla definíciója a következő szempontokat tartalmazza:

* **Séma**: a külső tábla DDL definiál egy sémát, amelyet a lekérdezések használhatnak. A külső tábla definíciójában megadott sémának meg kell egyeznie a távoli adatbázisban a tényleges adatokat tároló táblák sémájával.
* **Távoli adatbázis-hivatkozás**: a külső tábla DDL egy külső adatforrásra hivatkozik. A külső adatforrás megadja annak a távoli adatbázisnak a nevét és az adatbázis nevét, ahol a rendszer a tényleges tábla adatokat tárolja.

Az előző szakaszban ismertetett külső adatforrás használatával a külső táblák létrehozására szolgáló szintaxis a következő:

A DATA_SOURCE záradék azt a külső adatforrást határozza meg, amely a külső táblához használatos (vertikális particionálás esetén a távoli adatbázis).  

A SCHEMA_NAME és OBJECT_NAME záradékok lehetővé teszik a külső tábla definíciójának leképezését a távoli adatbázis egy másik sémájában lévő táblára, vagy egy másik nevű táblára. Ez akkor lehet hasznos, ha külső táblát kíván definiálni a katalógus vagy a DMV számára a távoli adatbázison – vagy bármilyen más olyan helyzetben, ahol a távoli tábla neve már helyileg is megtörtént.  

A következő DDL-utasítás elveszít egy meglévő külső tábla definícióját a helyi katalógusból. Ez nem befolyásolja a távoli adatbázist.

```sql
DROP EXTERNAL TABLE [ [ schema_name ] . | schema_name. ] table_name[;]  
```

A **külső tábla létrehozásának/ELdobásának engedélyei**: a külső adatforrásokra vonatkozó engedélyek megváltoztatására van szükség a külső tábla DDL esetében, amelyre az alapul szolgáló adatforrásra is szükség van.  

## <a name="security-considerations"></a>Biztonsági szempontok

A külső táblához hozzáféréssel rendelkező felhasználók automatikusan hozzáférhetnek a mögöttes távoli táblákhoz a külső adatforrás definíciójában megadott hitelesítő adatok alatt. Ügyeljen arra, hogy a külső táblához való hozzáférést körültekintően kezelje, hogy a külső adatforrás hitelesítő adataival ne a kívánt jogosultságszint-emelési jogosultságot lehessen használni. A normál SQL-engedélyek használatával a külső táblákhoz való hozzáférés adható meg vagy vonható vissza ugyanúgy, mintha egy normál tábla lenne.  

## <a name="example-querying-vertically-partitioned-databases"></a>Példa: függőlegesen particionált adatbázisok lekérdezése

A következő lekérdezés egyirányú összekapcsolást végez a két helyi tábla között a megrendelések és a rendeléssorok, valamint a távoli tábla ügyfelek számára. Ez egy példa a rugalmas lekérdezésre vonatkozó hivatkozási adatfelhasználási esetre:

```sql
    SELECT
     c_id as customer,
     c_lastname as customer_name,
     count(*) as cnt_orderline,
     max(ol_quantity) as max_quantity,
     avg(ol_amount) as avg_amount,
     min(ol_delivery_d) as min_deliv_date
    FROM customer
    JOIN orders
    ON c_id = o_c_id
    JOIN  order_line
    ON o_id = ol_o_id and o_c_id = ol_c_id
    WHERE c_id = 100
```

## <a name="stored-procedure-for-remote-t-sql-execution-sp_execute_remote"></a>A távoli T-SQL végrehajtásának tárolt eljárása: SP \_ execute_remote

A rugalmas lekérdezés egy tárolt eljárást is bevezet, amely közvetlen hozzáférést biztosít a távoli adatbázishoz. A tárolt eljárás neve [SP \_ Execute \_ Remote](/sql/relational-databases/system-stored-procedures/sp-execute-remote-azure-sql-database) , és használható távoli tárolt eljárások vagy T-SQL-kód végrehajtásához a távoli adatbázisban. A következő paramétereket veszi figyelembe:

* Adatforrás neve (nvarchar): a RDBMS típusú külső adatforrás neve.
* Query (nvarchar): a távoli adatbázison végrehajtandó T-SQL-lekérdezés.
* Paraméter deklarációja (nvarchar) – nem kötelező: karakterlánc a lekérdezési paraméterben használt paraméterekhez (például sp_executesql) tartozó adattípus-definíciókkal.
* Paraméter értékének listája – nem kötelező: a paraméterek értékeinek vesszővel tagolt listája (például sp_executesql).

Az SP \_ Execute \_ Remote a Meghívási paraméterekben megadott külső adatforrást használja a távoli adatbázis megadott T-SQL-utasításának végrehajtásához. A távoli adatbázishoz való kapcsolódáshoz a külső adatforrás hitelesítő adatait használja.  

Példa:

```sql
    EXEC sp_execute_remote
        N'MyExtSrc',
        N'select count(w_id) as foo from warehouse'
```

## <a name="connectivity-for-tools"></a>Eszközökhöz való kapcsolódás

A hagyományos SQL Server kapcsolati karakterláncokkal csatlakoztathatja a BI-és adatintegrációs eszközöket a kiszolgálón található adatbázisokhoz, amelyeken engedélyezve van a rugalmas lekérdezés és a külső táblák definiálva. Győződjön meg arról, hogy a SQL Server támogatott adatforrásként az eszköz számára. Ezután tekintse meg a rugalmas lekérdezési adatbázist és a külső táblázatokat ugyanúgy, mint bármely más SQL Server adatbázishoz, amelyhez csatlakozni szeretne az eszközéhez.

## <a name="best-practices"></a>Ajánlott eljárások

* Győződjön meg arról, hogy a rugalmas lekérdezési végpont adatbázisa hozzáférést kapott a távoli adatbázishoz azáltal, hogy engedélyezi az Azure-szolgáltatások hozzáférését az Azure SQL Database tűzfal konfigurációjában. Győződjön meg arról is, hogy a külső adatforrás-definícióban megadott hitelesítő adat sikeresen bejelentkezhet a távoli adatbázisba, és rendelkezik a távoli tábla eléréséhez szükséges engedélyekkel.  
* A rugalmas lekérdezés a legmegfelelőbb a lekérdezésekhez, ahol a számítások többsége a távoli adatbázisokon végezhető el. A lehető legjobb lekérdezési teljesítményt a szelektív szűrési predikátumokkal érheti el, amelyek kiértékelése a távoli adatbázisokon, illetve az olyan illesztéseken végezhető el, amelyek teljes mértékben a távoli adatbázisban végezhetők el. Előfordulhat, hogy más lekérdezési minták nagy mennyiségű adat betöltését végzik a távoli adatbázisból, és nem is gyengék.

## <a name="next-steps"></a>Következő lépések

* A rugalmas lekérdezés áttekintését lásd: [rugalmas lekérdezés áttekintése](elastic-query-overview.md).
* A vertikális particionálással kapcsolatos oktatóanyagért lásd: [Bevezetés az adatbázisok közötti lekérdezéssel (vertikális particionálás)](elastic-query-getting-started-vertical.md).
* A horizontális particionálással (skálázással) kapcsolatos oktatóanyagért lásd: az [első lépések a rugalmas lekérdezéssel a horizontális particionáláshoz](elastic-query-getting-started.md).
* A horizontálisan particionált információk szintaxisát és mintáit lásd: [vízszintesen particionált adatlekérdezés](elastic-query-horizontal-partitioning.md)
* Lásd: az [SP \_ \_ távoli futtatása](/sql/relational-databases/system-stored-procedures/sp-execute-remote-azure-sql-database) olyan tárolt eljáráshoz, amely Transact-SQL-utasítást hajt végre egyetlen távoli Azure SQL Database vagy egy horizontális particionálási sémában szegmensként szolgáló adatbázis-készleten.

<!--Image references-->
[1]: ./media/elastic-query-vertical-partitioning/verticalpartitioning.png

<!--anchors-->