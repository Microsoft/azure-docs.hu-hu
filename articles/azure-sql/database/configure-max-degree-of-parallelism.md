---
title: A maximális párhuzamossági fok (MAXDOP) beállítása
titleSuffix: Azure SQL Database
description: Ismerje meg a maximális párhuzamossági fokot (MAXDOP).
ms.date: 03/29/2021
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
ms.openlocfilehash: 31ddf15975abdce70ea02b5de64ea5611e7e72b3
ms.sourcegitcommit: 5fd1f72a96f4f343543072eadd7cdec52e86511e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/01/2021
ms.locfileid: "106111815"
---
# <a name="configure-the-max-degree-of-parallelism-maxdop-in-azure-sql-database"></a>A maximális párhuzamossági fok (MAXDOP) beállítása Azure SQL Database
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

  Ez a cikk a **maximális párhuzamossági fokot (MAXDOP)** mutatja Azure SQL Databaseban, és hogy miként lehet konfigurálni. 

> [!NOTE]
> **Ez a tartalom Azure SQL Databasere összpontosít.** A Azure SQL Database a Microsoft SQL Server adatbázismotor legújabb stabil verziójára épül, így a tartalom nagy része hasonló, bár a hibaelhárítás és a konfigurációs beállítások eltérnek. A SQL Server MAXDOP kapcsolatos további információkért tekintse meg [a maximális párhuzamossági fok kiszolgáló konfigurációjának konfigurálása beállítást](/sql/database-engine/configure-windows/configure-the-max-degree-of-parallelism-server-configuration-option).

## <a name="overview"></a>Áttekintés
  A Azure SQL Database az egyes új önálló adatbázisok és a rugalmas készlet-adatbázisok alapértelmezett MAXDOP-beállítása 8. Ez azt jelenti, hogy az adatbázismotor több szál használatával is végrehajthat lekérdezéseket. A SQL Servertól eltérően, ahol az alapértelmezett kiszolgáló szintű MAXDOP 0 (korlátlan), alapértelmezés szerint a Azure SQL Database új adatbázisai a MAXDOP 8 értékre vannak állítva. Ez az alapértelmezett érték megakadályozza a szükségtelen erőforrás-használatot, és biztosítja az egységes felhasználói élményt. Ez általában nem szükséges a MAXDOP további konfigurálásához Azure SQL Database számítási feladatokban, de előnyt biztosíthat az összetett teljesítmény-hangolási gyakorlatnak.

> [!Note]
>   Szeptember 2020-én a Azure SQL Database Service MAXDOP szolgáltatásban a 8. telemetria alapján az új adatbázisok esetében az alapértelmezett értékként [választották](https://techcommunity.microsoft.com/t5/azure-sql/changing-default-maxdop-in-azure-sql-database-and-azure-sql/ba-p/1538528) meg a rendszer a legszélesebb körű felhasználói munkaterheléseket. Ez az alapértelmezett megoldás a túlzott párhuzamosságok miatti teljesítményproblémák megelőzését segítette. Ezt megelőzően az új adatbázisok alapértelmezett beállítása MAXDOP 0 volt. A MAXDOP adatbázis-hatókörű konfigurációs beállítás nem módosult a 2020 szeptember előtt létrehozott meglévő adatbázisok esetében.

  Általánosságban elmondható, hogy ha az adatbázismotor a párhuzamosságot használó lekérdezés végrehajtását választja, a végrehajtás ideje gyorsabb. A felesleges párhuzamosság azonban a lekérdezési teljesítmény javítása nélkül is használhat felesleges processzor-erőforrásokat. A felesleges párhuzamosságok nagy mértékben befolyásolhatják a lekérdezési teljesítményt az ugyanazon adatbázismotor-példányon futó összes lekérdezés esetében, így a párhuzamosságok felső határának beállítása a SQL Server munkaterhelések általános teljesítmény-finomhangolási gyakorlata volt.

  A következő táblázat ismerteti az adatbázismotor viselkedését, amikor különböző MAXDOP-értékekkel rendelkező lekérdezéseket hajt végre:

| MAXDOP | Működés | 
|--|--|
| = 1 | Az adatbázismotor nem végez lekérdezéseket több párhuzamos szál használatával. | 
| > 1 | Az adatbázismotor a párhuzamos szálak számának felső határát állítja be. Az adatbázismotor kiválasztja a használni kívánt további feldolgozói szálak számát. A lekérdezés végrehajtásához használt munkaszálak teljes száma magasabb lehet a megadott MAXDOP értéknél. |
| = 0 | Az adatbázismotor több párhuzamos szálat is használhat, amelyek felső határa a logikai processzorok teljes számától függ. Az adatbázismotor kiválasztja a használandó párhuzamos szálak számát.| 
| | |
  
##  <a name="considerations"></a><a name="Considerations"></a> Szempontok  

-   A Azure SQL Databaseban módosíthatja az alapértelmezett MAXDOP értéket:
    -   A lekérdezési szinten a MAXDOP-  [lekérdezési](/sql/t-sql/queries/hints-transact-sql-query)mutató használatával.     
    -   Az adatbázis szintjén a **MAXDOP** - [adatbázis hatókörű konfigurációjának](/sql/t-sql/statements/alter-database-scoped-configuration-transact-sql)használatával.

-   A hosszú távú SQL Server MAXDOP kapcsolatos megfontolások és [javaslatok](/sql/database-engine/configure-windows/configure-the-max-degree-of-parallelism-server-configuration-option#Guidelines) Azure SQL Databasere alkalmazhatók. 

-   MAXDOP kényszerítve [.](/sql/relational-databases/system-dynamic-management-views/sys-dm-os-tasks-transact-sql) Nem kényszerített [kérelem](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql) vagy lekérdezés alapján. Ez azt jelenti, hogy egy párhuzamos lekérdezés-végrehajtás során egyetlen kérelem több feladatot is elindíthat a MAXDOP által meghatározott felső határral. További információ: a *párhuzamos feladatok ütemezése* című rész a [szál és a feladat architektúrájának útmutatójában](/sql/relational-databases/thread-and-task-architecture-guide). 
  
-   Az indexek létrehozásával vagy újraépítésével, illetve a fürtözött indexek eldobásával kapcsolatos műveletek erőforrás-igényesek lehetnek. Az index műveleteinél a MAXDOP index beállítás megadásával felülbírálhatja az adatbázis maximális párhuzamossági fokát a `CREATE INDEX` vagy `ALTER INDEX` utasításban. A MAXDOP értékét a rendszer végrehajtási időpontban alkalmazza az utasításra, és nem az index metaadataiban tárolja. További információ: [párhuzamos indexelési műveletek konfigurálása](/sql/relational-databases/indexes/configure-parallel-index-operations).  
  
-   A lekérdezések és az indexelési műveletek mellett a MAXDOP adatbázis-hatókörű konfigurációs beállítása is szabályozza a DBCC CHECKTABLE UTASÍTÁST, a DBCC CHECKDB UTASÍTÁST és a DBCC CHECKFILEGROUP párhuzamosságát. 

##  <a name="recommendations"></a><a name="Security"></a> Javaslatok  

  Az adatbázis MAXDOP módosítása jelentős hatással lehet a lekérdezés teljesítményére és az erőforrás-felhasználásra, mind a pozitív, mind a negatív értékre. Az összes számítási feladathoz azonban egyetlen MAXDOP érték sincs optimális. A MAXDOP beállítására vonatkozó javaslatok árnyalva vannak, és számos tényezőtől függenek. 

  Néhány csúcs egyidejű munkaterhelése jobban működhet egy másik MAXDOP, mint a többi. A megfelelően konfigurált MAXDOP csökkentenie kell a teljesítmény és a rendelkezésre állási incidensek kockázatát, és bizonyos esetekben csökkentenie kell a költségeket azzal, hogy el tudja kerülni a szükségtelen erőforrás-használatot, és így alacsonyabb szolgáltatási célnak kellene méreteznie.

### <a name="excessive-parallelism"></a>Túlzott párhuzamosság

  A magasabb MAXDOP gyakran csökkenti a CPU-igényes lekérdezések időtartamát. Azonban a túlzott párhuzamosság ronthatja más párhuzamos munkaterhelési teljesítményt, ha a processzor és a feldolgozó szál erőforrásainak más lekérdezéseit is megéhezik. Szélsőséges esetekben a túlzott párhuzamosság felhasználhatja az összes adatbázis vagy rugalmas készlet erőforrását, így lekérdezési időtúllépéseket, hibákat és az alkalmazások kimaradásait. 

  Javasoljuk, hogy az ügyfelek ne MAXDOP a 0 értéket akkor is, ha a probléma jelenleg nem okoz problémát. A túlzott párhuzamosság a legtöbb problémát okozhatja, ha a processzor és a munkavégző szálak több egyidejű kérelmet kapnak, mint amennyit a szolgáltatási cél támogatni tud. Kerülje a MAXDOP 0-ra, hogy csökkentse a lehetséges jövőbeli problémák kockázatát, ha egy adatbázis vertikális felskálázása esetén, illetve ha a Azure SQL Database további hardveres generációi több magot is biztosítanak ugyanahhoz az adatbázis-szolgáltatási célhoz.

### <a name="modifying-maxdop"></a>MAXDOP módosítása 

  Ha azt állapítja meg, hogy egy másik MAXDOP-beállítás a Azure SQL Database munkaterhelés esetében optimális, használhatja a `ALTER DATABASE SCOPED CONFIGURATION` T-SQL-utasítást. Példaként tekintse meg az alábbi, a [Transact-SQL szakaszt használó példákat](#examples) . Adja hozzá ezt a lépést a telepítési folyamathoz, hogy megváltoztassa a MAXDOP az adatbázis létrehozása után.

  Ha a nem alapértelmezett MAXDOP csak a munkaterhelésben lévő lekérdezések egy részhalmazát használja, felülbírálhatja a MAXDOP a lekérdezési szinten a OPTION (MAXDOP) mutató hozzáadásával. Példaként tekintse meg az alábbi, a [Transact-SQL szakaszt használó példákat](#examples) . 

  Alaposan tesztelje a MAXDOP-konfiguráció módosításait, és töltse ki a reális egyidejű lekérdezési terheléseket. 

  Az elsődleges és a másodlagos replikák MAXDOP egymástól függetlenül konfigurálható, hogy kihasználhassa a különböző optimális MAXDOP beállításokat az írási és olvasási feladatokhoz. Ez Azure SQL Database az [olvasási felskálázást](read-scale-out.md), a [geo-replikálást](active-geo-replication-overview.md)és a [Azure SQL Database nagy kapacitású másodlagos replikáit](service-tier-hyperscale.md)érinti. Alapértelmezés szerint az összes másodlagos replika örökli az elsődleges replika MAXDOP-konfigurációját.

## <a name="security"></a><a name="Security"></a> Biztonsági  
  
###  <a name="permissions"></a><a name="Permissions"></a> Engedélyek  
  Az `ALTER DATABASE SCOPED CONFIGURATION` utasítást a kiszolgálói rendszergazdaként kell végrehajtani, az adatbázis-szerepkör tagjaként `db_owner` vagy egy olyan felhasználóként, aki `ALTER ANY DATABASE SCOPED CONFIGURATION` engedélyt kapott.
 
## <a name="examples"></a>Példák   

  Ezek a példák a legújabb **AdventureWorksLT** -mintaadatbázis használatát mutatják be, ha a `SAMPLE` Azure SQL Database új önálló adatbázisát választja.

### <a name="powershell"></a>PowerShell

#### <a name="maxdop-database-scoped-configuration"></a>MAXDOP adatbázis-hatókörű konfiguráció   

  Ez a példa azt mutatja be, hogyan használható az [Alter Database hatókörű konfigurációs](/sql/t-sql/statements/alter-database-scoped-configuration-transact-sql) utasítása a `max degree of parallelism` beállításának konfigurálásához `2` . A beállítás azonnal érvénybe lép. A [meghívott PowerShell-parancsmag-SqlCmd](/powershell/module/sqlserver/invoke-sqlcmd) végrehajtja a T-SQL-lekérdezéseket, és visszaadja a MAXDOP-adatbázis hatókörének konfigurációját. 

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

Ez a példa az Azure SQL Database-adatbázisokkal való használatra használható az olvasási kibővített [replikákkal](read-scale-out.md), a [geo-replikálással](active-geo-replication-overview.md)és a [Azure SQL Database nagy kapacitású másodlagos replikákkal](service-tier-hyperscale.md). Az elsődleges replika például egy másik alapértelmezett MAXDOP van beállítva másodlagos replikaként, ami azt jelzi, hogy az írási és olvasási feladatok között eltérések lehetnek.

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
  
  A [Azure Portal lekérdezés-szerkesztő](connect-query-portal.md), [SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms)vagy a [Azure Data Studio](/sql/azure-data-studio/download-azure-data-studio) használatával T-SQL-lekérdezéseket hajthat végre a Azure SQL Database.

1.  Kapcsolódjon a Azure SQL Databasehoz. Az adatbázis-hatókörű konfigurációk nem módosíthatók a Master adatbázisban.
  
2.  A standard sávban válassza a **New Query (új lekérdezés**) lehetőséget.   
  
3.  Másolja és illessze be az alábbi példát a lekérdezési ablakba, és válassza a **végrehajtás** lehetőséget. 


#### <a name="maxdop-database-scoped-configuration"></a>MAXDOP adatbázis-hatókörű konfiguráció

  Ebből a példából megtudhatja, hogyan határozhatja meg az adatbázis aktuális MAXDOP adatbázis-hatókörének konfigurációját a [sys.database_scoped_configurations](/sql/relational-databases/system-catalog-views/sys-database-scoped-configurations-transact-sql) Rendszerkatalógus nézet használatával.

```sql
SELECT [value] FROM sys.database_scoped_configurations WHERE [name] = 'MAXDOP';
```

  Ez a példa azt mutatja be, hogyan használható az [Alter Database hatókörű konfigurációs](/sql/t-sql/statements/alter-database-scoped-configuration-transact-sql) utasítása a `max degree of parallelism` beállításának konfigurálásához `8` . A beállítás azonnal érvénybe lép.  
  
```sql  
ALTER DATABASE SCOPED CONFIGURATION SET MAXDOP = 8;
```  

Ez a példa az Azure SQL Database-adatbázisokkal való használatra használható az olvasási kibővített [replikákkal](read-scale-out.md), a [geo-replikálással](active-geo-replication-overview.md)és a [Azure SQL Database nagy kapacitású másodlagos replikákkal](service-tier-hyperscale.md). Az elsődleges replika például egy másik alapértelmezett MAXDOP van beállítva másodlagos replikaként, ami azt jelzi, hogy az írási és olvasási feladatok között eltérések lehetnek. A `value_for_secondary` `sys.database_scoped_configurations` másodlagos replika beállításait tartalmazó oszlop.

```sql
ALTER DATABASE SCOPED CONFIGURATION SET MAXDOP = 8;
ALTER DATABASE SCOPED CONFIGURATION FOR SECONDARY SET MAXDOP = 1;
SELECT [value], value_for_secondary FROM sys.database_scoped_configurations WHERE [name] = 'MAXDOP';
```

#### <a name="maxdop-query-hint"></a>MAXDOP-lekérdezési tipp

  Ebből a példából megtudhatja, hogyan futtathat egy lekérdezést a lekérdezési mutató használatával a alkalmazás kényszerítéséhez `max degree of parallelism` `2` .  

```sql 
SELECT ProductID, OrderQty, SUM(LineTotal) AS Total  
FROM SalesLT.SalesOrderDetail  
WHERE UnitPrice < 5  
GROUP BY ProductID, OrderQty  
ORDER BY ProductID, OrderQty  
OPTION (MAXDOP 2);    
GO
```
#### <a name="maxdop-index-option"></a>MAXDOP index beállítása

  Ez a példa azt mutatja be, hogyan lehet újraépíteni egy indexet az index kapcsoló használatával, hogy kényszerítse a `max degree of parallelism` -t `12` .  

```sql 
ALTER INDEX ALL ON SalesLT.SalesOrderDetail 
REBUILD WITH 
   (     MAXDOP = 12
       , SORT_IN_TEMPDB = ON
       , ONLINE = ON);
```

## <a name="see-also"></a>Lásd még  
* [Az adatbázis HATÓKÖRén belüli konfiguráció módosítása &#40;Transact-SQL&#41;](/sql/t-sql/statements/alter-database-scoped-configuration-transact-sql)        
* [sys.database_scoped_configurations (Transact-SQL)](/sql/relational-databases/system-catalog-views/sys-database-scoped-configurations-transact-sql)
* [Párhuzamos indexelési műveletek konfigurálása](/sql/relational-databases/indexes/configure-parallel-index-operations)    
* [Lekérdezési útmutatók &#40;Transact-SQL&#41;](/sql/t-sql/queries/hints-transact-sql-query)     
* [Index beállításainak megadása](/sql/relational-databases/indexes/set-index-options)     
* [Azure SQL Database blokkolási problémák ismertetése és megoldása](understand-resolve-blocking.md)

## <a name="next-steps"></a>Következő lépések

* [A teljesítmény figyelése és finomhangolása](/sql/relational-databases/performance/monitor-and-tune-for-performance)
