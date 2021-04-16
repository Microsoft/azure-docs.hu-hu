---
title: A maximális párhuzamosság fokának konfigurálása (MAXDOP)
titleSuffix: Azure SQL Database
description: Ismerje meg a maximális párhuzamosság fokát (MAXDOP).
ms.date: 04/12/2021
services: sql-database
dev_langs:
- TSQL
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: tsql
ms.topic: conceptual
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.reviewer: ''
ms.openlocfilehash: c9b8e916c82a42df7addb3c49b4452c0eb403023
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107536904"
---
# <a name="configure-the-max-degree-of-parallelism-maxdop-in-azure-sql-database"></a>A maximális párhuzamossági fok (MAXDOP) konfigurálása az Azure SQL Database-ben
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

  Ez a cikk a maximális **párhuzamosság (MAXDOP)** konfigurációs beállítást ismerteti a Azure SQL Database. 

> [!NOTE]
> **Ez a tartalom a tartalommal Azure SQL Database.** Azure SQL Database az Microsoft SQL Server adatbázismotor legújabb, stabil verzióján alapul, ezért a tartalom nagy része hasonló annak ellenére, hogy a hibaelhárítási és konfigurációs lehetőségek eltérnek. További információ a MAXDOP-SQL Server: Configure the max degree of parallelism Server Configuration Option (A párhuzamosság maximális fokának konfigurálása [kiszolgáló-konfigurációs beállítás).](/sql/database-engine/configure-windows/configure-the-max-degree-of-parallelism-server-configuration-option)

## <a name="overview"></a>Áttekintés
  A MAXDOP vezérli a lekérdezésen belüli párhuzamosságokat az adatbázismotorban. A magasabb MAXDOP-értékek általában több párhuzamos szálat eredményeznek lekérdezésenként, és gyorsabb lekérdezés-végrehajtást eredményeznek. 

  A Azure SQL Database alapértelmezett MAXDOP-beállítás minden egyes új és rugalmaskészlet-adatbázishoz 8. Ez az alapértelmezett beállítás megakadályozza a felesleges erőforrás-használatot, miközben az adatbázismotor továbbra is lehetővé teszi a lekérdezések gyorsabb végrehajtását párhuzamos szálak használatával. A MAXDOP-t általában nem szükséges további Azure SQL Database számítási feladatokban, bár ez a speciális teljesítmény-finomhangolási gyakorlatként előnyös lehet.

> [!Note]
>   2020 szeptemberében a Azure SQL Database szolgáltatás MAXDOP 8-as telemetriai évei [](https://techcommunity.microsoft.com/t5/azure-sql/changing-default-maxdop-in-azure-sql-database-and-azure-sql/ba-p/1538528)alapján az alapértelmezett érték lett az új adatbázisok esetében, amely az ügyfelek számítási feladatainak legszélesebb választékának optimális értéke. Ez az alapértelmezett beállítás segített megelőzni a túlzott párhuzamosság miatti teljesítményproblémákat. Ezt megelőzően az új adatbázisok alapértelmezett beállítása a MAXDOP 0 volt. A MAXDOP nem módosult automatikusan a 2020 szeptembere előtt létrehozott meglévő adatbázisokon.

  Általánosságban elmondható, hogy ha az adatbázismotor párhuzamosság használatával hajt végre lekérdezést, a végrehajtási idő gyorsabb lesz. A felesleges párhuzamosság azonban további processzor-erőforrásokat is felhasználhatja a lekérdezési teljesítmény javítása nélkül. Nagy méretekben a túlzott párhuzamosság negatívan befolyásolhatja az ugyanazon az adatbázismotor-példányon végrehajtható összes lekérdezés lekérdezési teljesítményét. A párhuzamosság felső határának beállítása hagyományosan gyakori teljesítmény-finomhangolási gyakorlat volt a SQL Server esetében.

  A következő táblázat ismerteti az adatbázismotor viselkedését különböző MAXDOP-értékekkel való lekérdezések végrehajtásakor:

| MAXDOP | Működés | 
|--|--|
| = 1 | Az adatbázismotor egyetlen soros szálat használ a lekérdezések végrehajtásához. A párhuzamos szálak nem használhatók. | 
| > 1 | Az adatbázismotor a párhuzamos [](https://docs.microsoft.com/sql/relational-databases/thread-and-task-architecture-guide#sql-server-task-scheduling) szálak által használt további ütemezők számát a MAXDOP értékre vagy a logikai processzorok teljes számára állítja be ( amelyik kisebb). |
| = 0 | Az adatbázismotor a párhuzamos [](https://docs.microsoft.com/sql/relational-databases/thread-and-task-architecture-guide#sql-server-task-scheduling) szálak által használt további ütemezők számát a logikai processzorok teljes számára vagy 64-re állítja, amelyik kisebb. | 
| | |

> [!Note]
> Minden lekérdezés legalább egy ütemezővel és egy feldolgozószálal fut le az ütemezőn.
>
> A párhuzamosságsal végrehajtható lekérdezések további ütemezőket és további párhuzamos szálakat használnak. Mivel ugyanazon az ütemezőn több párhuzamos szál is futhat, a lekérdezés végrehajtásához használt szálak teljes száma magasabb lehet a megadott MAXDOP-értéknél vagy a logikai processzorok teljes számában. További információ: Párhuzamos tevékenységek [ütemezése.](/sql/relational-databases/thread-and-task-architecture-guide#scheduling-parallel-tasks)

##  <a name="considerations"></a><a name="Considerations"></a> Megfontolások  

-   A Azure SQL Database a MAXDOP alapértelmezett értékét módosíthatja:
    -   A lekérdezés szintjén használja a **MAXDOP lekérdezési** [tippet.](/sql/t-sql/queries/hints-transact-sql-query)     
    -   Az adatbázis szintjén használja a **MAXDOP-adatbázis** [hatókörű konfigurációját.](/sql/t-sql/statements/alter-database-scoped-configuration-transact-sql)

-   A hosszú SQL Server MAXDOP-szempontok [és](/sql/database-engine/configure-windows/configure-the-max-degree-of-parallelism-server-configuration-option#Guidelines) -javaslatok a Azure SQL Database. 

-   Az indexet létrehozására vagy újraépítésére, illetve fürtözött indexet elejtő indexműveletek erőforrás-igényesek is lehetnek. Az indexműveletekkel kapcsolatos MAXDOP adatbázis-érték felülírható a MAXDOP index beállítás az `CREATE INDEX` or utasításban való megadásával. `ALTER INDEX` A MAXDOP érték a végrehajtáskor lesz alkalmazva az utasításra, és nem az index metaadataiban van tárolva. További információ: [Párhuzamos indexműveletek konfigurálása.](/sql/relational-databases/indexes/configure-parallel-index-operations)  
  
-   A lekérdezések és indexműveletek mellett a MAXDOP adatbázisra vonatkozó konfigurációs beállítása más, párhuzamos végrehajtást is használható utasítások párhuzamosságát szabályozza, például: DBCC CHECKTABLE, DBCC CHECKDB és DBCC CHECKFILEGROUP. 

##  <a name="recommendations"></a><a name="Recommendations"></a> Ajánlások  

  Az adatbázis MAXDOP-nak módosítása nagy hatással lehet a lekérdezési teljesítményre és az erőforrás-kihasználtságra, pozitív és negatív is lehet. Nincs azonban egyetlen MAXDOP érték, amely minden számítási feladathoz optimális lenne. A [](/sql/database-engine/configure-windows/configure-the-max-degree-of-parallelism-server-configuration-option#Guidelines) MAXDOP beállításának javaslatai árnyaltak, és számos tényezőtől függnek. 

  Egyes egyidejű számítási feladatok csúcsterhelése jobban működhet más MAXDOP-val, mint a többi. A megfelelően konfigurált MAXDOP-nak csökkentenie kell a teljesítménybeli és rendelkezésre állási incidensek kockázatát, és bizonyos esetekben csökkentheti a költségeket azáltal, hogy elkerüli a szükségtelen erőforrás-használatot, és így alacsonyabb szolgáltatási célkitűzésre szűkül le.

### <a name="excessive-parallelism"></a>Túlzott párhuzamosság

  A nagyobb MAXDOP gyakran csökkenti a processzorigényes lekérdezések időtartamát. A túlzott párhuzamosság azonban ronthatja az egyidejű számítási feladatok teljesítményét, mivel a processzor- és feldolgozószál-erőforrások egyéb lekérdezései kiesnek. Szélsőséges esetben a túlzott párhuzamosság az összes adatbázis- vagy rugalmaskészlet-erőforrást felhasználhatja, ami lekérdezési időtúllépéseket, hibákat és alkalmazáskimaradásokat okoz. 

> [!Tip]
> Javasoljuk, hogy az ügyfelek akkor se állják meg a MAXDOP 0-t, ha az jelenleg nem okoz problémát.

  A túlzott párhuzamosság akkor válik a legproblémásabbá, ha több egyidejű kérés van, mint amennyit a szolgáltatási célkitűzés által biztosított CPU- és feldolgozószál-erőforrások támogatnak. Kerülje a MAXDOP 0-t, hogy csökkentse a túlzott párhuzamosság miatti lehetséges jövőbeli problémák kockázatát, ha egy adatbázist felskálák, vagy ha a Azure SQL Database jövőbeli hardvergenerációi több magot biztosítanak ugyanannak az adatbázis-szolgáltatási célkitűzésnek.

### <a name="modifying-maxdop"></a>A MAXDOP módosítása 

  Ha úgy dönt, hogy az alapértelmezetttől eltérő MAXDOP-beállítás optimális a Azure SQL Database számítási feladathoz, használhatja a `ALTER DATABASE SCOPED CONFIGURATION` T-SQL-utasítást. Példákat az alábbi [Példák a Transact-SQL használatára](#examples) című szakaszban talál. Ha a MAXDOP-t nem alapértelmezett értékre szeretne módosítani minden létrehozott új adatbázishoz, adja hozzá ezt a lépést az adatbázis üzembe helyezési folyamatához.

  Ha a nem alapértelmezett MAXDOP csak a lekérdezések egy kis részkészletét használja a számítási feladatban, felülbírálhatja a MAXDOP-t a lekérdezés szintjén az OPTION (MAXDOP) tipp hozzáadásával. Példákat az alábbi [Példák a Transact-SQL használatára](#examples) című szakaszban talál. 

  Alaposan tesztelje a MAXDOP-konfiguráció módosításait a valósághű egyidejű lekérdezésterheléseket tartalmazó terheléstesztekkel. 

  Az elsődleges és másodlagos replikák MAXDOP-beállításai egymástól függetlenül konfigurálhatók, ha a MAXDOP különböző beállításai optimálisak az írási és olvasási számítási feladatokhoz. Ez az olvasási [Azure SQL Database,](read-scale-out.md)a [](active-geo-replication-overview.md)georeplikációra és a másodlagos [hiperméretű](service-tier-hyperscale.md) replikákra vonatkozik. Alapértelmezés szerint az összes másodlagos replika örökli az elsődleges replika MAXDOP konfigurációját.

## <a name="security"></a><a name="Security"></a> Biztonsági  
  
###  <a name="permissions"></a><a name="Permissions"></a> Engedélyek  
  Az `ALTER DATABASE SCOPED CONFIGURATION` utasítást kiszolgálói rendszergazdaként, az adatbázis-szerepkör tagjaként vagy olyan felhasználóként kell végrehajtani, aki engedélyt `db_owner` `ALTER ANY DATABASE SCOPED CONFIGURATION` kapott.
 
## <a name="examples"></a>Példák   

  Ezek a példák a legújabb **AdventureWorksLT** mintaadatbázist használják, ha a lehetőséget egy új, adatbázisból `SAMPLE` Azure SQL Database.

### <a name="powershell"></a>PowerShell

#### <a name="maxdop-database-scoped-configuration"></a>A MAXDOP adatbázis hatókörű konfigurációja   

  Ez a példa bemutatja, hogyan használható az [ALTER DATABASE SCOPED CONFIGURATION](/sql/t-sql/statements/alter-database-scoped-configuration-transact-sql) utasítás a konfiguráció `MAXDOP` `2` beállítására. A beállítás azonnal életbe lép az új lekérdezések esetén. Az [Invoke-SqlCmd PowerShell-parancsmag](/powershell/module/sqlserver/invoke-sqlcmd) végrehajtja a beállított T-SQL-lekérdezéseket, és visszaadja a MAXDOP-adatbázis hatókörű konfigurációját. 

```powershell
$dbName = "sample" 
$serverName = <server name here>
$serveradminLogin = <login here>
$serveradminPassword = <password here>
$desiredMAXDOP = 8

$params = @{
    'database' = $dbName
    'serverInstance' =  $serverName
    'username' = $serveradminLogin
    'password' = $serveradminPassword
    'outputSqlErrors' = $true
    'query' = 'ALTER DATABASE SCOPED CONFIGURATION SET MAXDOP = ' + $desiredMAXDOP + ';
     SELECT [value] FROM sys.database_scoped_configurations WHERE [name] = ''MAXDOP'';'
  }
  Invoke-SqlCmd @params
```

Ez a példa olyan adatbázisokkal Azure SQL, [](read-scale-out.md)amelyeken engedélyezve vannak az olvasási felskálás replikák, a [georeplikáció](active-geo-replication-overview.md)és a Azure SQL Database másodlagos [replikák.](service-tier-hyperscale.md) Az elsődleges replika például egy másik alapértelmezett MAXDOP-re van beállítva másodlagos replikaként, és arra számít, hogy különbségek lehetnek az írási és olvasási számítási feladatok között.

```powershell
$dbName = "sample" 
$serverName = <server name here>
$serveradminLogin = <login here>
$serveradminPassword = <password here>
$desiredMAXDOP_primary = 8
$desiredMAXDOP_secondary_readonly = 1
 
$params = @{
    'database' = $dbName
    'serverInstance' =  $serverName
    'username' = $serveradminLogin
    'password' = $serveradminPassword
    'outputSqlErrors' = $true
    'query' = 'ALTER DATABASE SCOPED CONFIGURATION SET MAXDOP = ' + $desiredMAXDOP + ';
    ALTER DATABASE SCOPED CONFIGURATION FOR SECONDARY SET MAXDOP = ' + $desiredMAXDOP_secondary_readonly + ';
    SELECT [value], value_for_secondary FROM sys.database_scoped_configurations WHERE [name] = ''MAXDOP'';'
  }
  Invoke-SqlCmd @params
```

### <a name="transact-sql"></a>Transact-SQL
  
  A T-SQL Azure Portal lekérdezések a SQL Server Management Studio [(SSMS)](/sql/ssms/download-sql-server-management-studio-ssms)vagy a [Azure Data Studio](/sql/azure-data-studio/download-azure-data-studio) használatával hajthat végre T-SQL-lekérdezéseket a Azure SQL Database. [](connect-query-portal.md)

1.  Nyisson meg egy új lekérdezési ablakot.

2.  Csatlakozzon ahhoz az adatbázishoz, ahol módosítani szeretné a MAXDOP-t. A master adatbázisban nem módosíthatja az adatbázisra vonatkozó hatókörrel kapcsolatos konfigurációkat.
  
3.  Másolja és illessze be az alábbi példát a lekérdezési ablakba, majd válassza a **Végrehajtás lehetőséget.** 

#### <a name="maxdop-database-scoped-configuration"></a>A MAXDOP adatbázis hatókörű konfigurációja

  Ez a példa bemutatja, hogyan határozható meg az aktuális adatbázis MAXDOP-adatbázishoz hatókörrel sys.database_scoped_configurations konfigurációja a rendszerkatalógus [nézetben.](/sql/relational-databases/system-catalog-views/sys-database-scoped-configurations-transact-sql)

```sql
SELECT [value] FROM sys.database_scoped_configurations WHERE [name] = 'MAXDOP';
```

  Ez a példa bemutatja, hogyan használható az [ALTER DATABASE SCOPED CONFIGURATION](/sql/t-sql/statements/alter-database-scoped-configuration-transact-sql) utasítás a konfiguráció `MAXDOP` `8` beállítására. A beállítás azonnal életbe lép.  
  
```sql  
ALTER DATABASE SCOPED CONFIGURATION SET MAXDOP = 8;
```  

Ez a példa olyan adatbázisokkal Azure SQL, [](read-scale-out.md)amelyeken engedélyezve vannak az olvasási, a [georeplikációs](active-geo-replication-overview.md)és a [hyperscale](service-tier-hyperscale.md) másodlagos replikák. Az elsődleges replika például egy másik MAXDOP-re van beállítva, mint a másodlagos replika, és arra számít, hogy különbségek lehetnek az írási és olvasási számítási feladatok között. Minden utasítás az elsődleges replikán lesz végrehajtva. A `value_for_secondary` oszlopa `sys.database_scoped_configurations` tartalmazza a másodlagos replika beállításait.

```sql
ALTER DATABASE SCOPED CONFIGURATION SET MAXDOP = 8;
ALTER DATABASE SCOPED CONFIGURATION FOR SECONDARY SET MAXDOP = 1;
SELECT [value], value_for_secondary FROM sys.database_scoped_configurations WHERE [name] = 'MAXDOP';
```

#### <a name="maxdop-query-hint"></a>MAXDOP lekérdezési tipp

  Ez a példa bemutatja, hogyan hajthat végre egy lekérdezést a lekérdezési tipp használatával, hogy a metódust a következőre `max degree of parallelism` kényszeríti: `2` .  

```sql 
SELECT ProductID, OrderQty, SUM(LineTotal) AS Total  
FROM SalesLT.SalesOrderDetail  
WHERE UnitPrice < 5  
GROUP BY ProductID, OrderQty  
ORDER BY ProductID, OrderQty  
OPTION (MAXDOP 2);    
GO
```
#### <a name="maxdop-index-option"></a>MAXDOP index beállítás

  Ez a példa bemutatja, hogyan lehet újraépíteni egy indexet az index beállításával, amely a következőre `max degree of parallelism` kényszeríti: `12` .  

```sql 
ALTER INDEX ALL ON SalesLT.SalesOrderDetail 
REBUILD WITH 
   (     MAXDOP = 12
       , SORT_IN_TEMPDB = ON
       , ONLINE = ON);
```

## <a name="see-also"></a>Lásd még  
* [ALTER DATABASE SCOPED CONFIGURATION &#40;Transact-SQL&#41;](/sql/t-sql/statements/alter-database-scoped-configuration-transact-sql)        
* [sys.database_scoped_configurations (Transact-SQL)](/sql/relational-databases/system-catalog-views/sys-database-scoped-configurations-transact-sql)
* [Párhuzamos indexműveletek konfigurálása](/sql/relational-databases/indexes/configure-parallel-index-operations)    
* [Lekérdezési tippek &#40;Transact-SQL-&#41;](/sql/t-sql/queries/hints-transact-sql-query)     
* [Indexbeállítások megadása](/sql/relational-databases/indexes/set-index-options)     
* [A problémák Azure SQL Database és megoldása](understand-resolve-blocking.md)

## <a name="next-steps"></a>Következő lépések

* [Monitor és hangolás a teljesítményhez](/sql/relational-databases/performance/monitor-and-tune-for-performance)
