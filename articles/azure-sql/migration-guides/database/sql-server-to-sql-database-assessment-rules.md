---
title: A Migrálás SQL Database SQL Server vonatkozó értékelési szabályok
description: Értékelési szabályok a forrás SQL Server példányával kapcsolatos problémák azonosításához, amelyeket a Azure SQL Databaseba való Migrálás előtt meg kell oldani.
ms.service: sql-database
ms.subservice: migration-guide
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: MashaMSFT
ms.author: mathoma
ms.reviewer: MashaMSFT
ms.date: 12/15/2020
ms.openlocfilehash: bf825572226bf5d7432fd3ad825f2f3a13355c53
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/04/2021
ms.locfileid: "102054730"
---
# <a name="assessment-rules-for-sql-server-to-sql-database-migration"></a>A Migrálás SQL Database SQL Server vonatkozó értékelési szabályok
[!INCLUDE[appliesto--sqldb](../../includes/appliesto-sqldb.md)]

Az áttelepítési eszközök számos értékelési szabály futtatásával ellenőrzik a forrás SQL Server példányát, hogy azonosítsa azokat a problémákat, amelyeket a SQL Server-adatbázis Azure SQL Databasere való áttelepítése előtt meg kell oldani. 

Ez a cikk felsorolja azokat a szabályokat, amelyek alapján megállapítható, hogy a SQL Server-adatbázis áttelepítésének megvalósíthatósága Azure SQL Databasera történik-e. 


## <a name="bulk-insert"></a>Tömeges beszúrás<a id="BulkInsert"></a>

**Title: a nem Azure Blob-adatforrással rendelkező BULK INSERT Azure SQL Database nem támogatott.**   
**Kategória**: probléma   

**Leírás**   
Azure SQL Database nem fér hozzá a fájlmegosztás vagy a Windows-mappákhoz. Tekintse meg az "érintett objektumok" szakaszt BULK INSERT olyan utasítások adott felhasználási módjainál, amelyek nem hivatkoznak Azure-blobra. A (z) "BULK INSERT" objektummal rendelkező objektumok, amelyekben a forrás nem az Azure Blob Storage szolgáltatás nem fog működni, miután áttelepíti a Azure SQL Database. 


**Ajánlás**   
A helyi fájlokat vagy fájlmegosztást használó BULK INSERT utasítások konvertálásával az Azure Blob Storage-ból származó fájlokat kell használnia, amikor a rendszer áttelepíti a Azure SQL Database. Másik lehetőségként telepítse át SQL Server Azure-beli virtuális gépre.

## <a name="compute-clause"></a>Számítási záradék<a id="ComputeClause"></a>

**Title: a számítási záradék megszűnt, és el lett távolítva.**   
**Kategória**: figyelmeztetés   

**Leírás**   
A számítási záradék az eredményhalmaz végén további összesítő oszlopként megjelenő összesítéseket hoz létre. Ez a záradék azonban már nem támogatott a Azure SQL Databaseban. 


**Ajánlás**   
A T-SQL-modult a kumulatív operátor használatával kell újraírni. Az alábbi kód azt mutatja be, hogyan cserélhető ki a számítás a kumulatív módon: 

```sql 
USE AdventureWorks 
GO;  

SELECT SalesOrderID, UnitPrice, UnitPriceDiscount 
FROM Sales.SalesOrderDetail 
ORDER BY SalesOrderID COMPUTE SUM(UnitPrice), SUM(UnitPriceDiscount) BY SalesOrderID GO; 

SELECT SalesOrderID, UnitPrice, UnitPriceDiscount,SUM(UnitPrice) as UnitPrice , 
SUM(UnitPriceDiscount) as UnitPriceDiscount 
FROM Sales.SalesOrderDetail 
GROUP BY SalesOrderID, UnitPrice, UnitPriceDiscount WITH ROLLUP; 
```

További információ: [megszűnt adatbázismotor-funkció SQL Server ](/previous-versions/sql/2014/database-engine/discontinued-database-engine-functionality-in-sql-server-2016#Denali)

## <a name="change-data-capture-cdc"></a>Adatváltozások rögzítése (CDC)<a id="CDC"></a>

**Title: az adatváltozások rögzítése (CDC) nem támogatott Azure SQL Database**   
**Kategória**: probléma   


**Leírás**   
Azure SQL Database nem támogatja az adatváltozások rögzítését (CDC). Értékelje ki, hogy Change Tracking használható-e.  Másik megoldásként áttelepítheti az Azure SQL felügyelt példányát vagy SQL Server az Azure Virtual Machines-on. 


**Ajánlás**   
Azure SQL Database nem támogatja az adatváltozások rögzítését (CDC). Értékelje ki, hogy Change Tracking használható-e, vagy fontolja meg a felügyelt Azure SQL-példányra való áttelepítést.

További információ: az [Azure SQL Change Tracking engedélyezése](https://social.technet.microsoft.com/wiki/contents/articles/2976.azure-sql-how-to-enable-change-tracking.aspx)

## <a name="clr-assemblies"></a>CLR-szerelvények<a id="ClrAssemblies"></a>

**Title: az SQL CLR-szerelvények nem támogatottak Azure SQL Database**   
**Kategória**: probléma   


**Leírás**   
A Azure SQL Database nem támogatja az SQL CLR-szerelvényeket. 


**Ajánlás**   
Jelenleg nem érhető el a Azure SQL Database. Az ajánlott alternatív megoldásoknál az alkalmazás kódja és az adatbázis módosítása csak a Azure SQL Database által támogatott szerelvények használatára lesz szükséges. Alternatív megoldásként migrálhat az Azure SQL felügyelt példányára vagy SQL Server az Azure-beli virtuális gépen

További információ: nem [támogatott Transact-SQL-különbségek SQL Database](../../database/transact-sql-tsql-differences-sql-server.md#transact-sql-syntax-not-supported-in-azure-sql-database)

## <a name="cryptographic-provider"></a>Kriptográfiai szolgáltató<a id="CryptographicProvider"></a>

**Title: A titkosítási szolgáltató létrehozása vagy az ALTER titkosítási szolgáltató használata nem támogatott Azure SQL Database**   
**Kategória**: probléma   

**Leírás**   
A Azure SQL Database nem támogatja a titkosítási szolgáltató utasításait, mert nem fér hozzá a fájlokhoz. Tekintse meg az érintett objektumok szakaszt a KRIPTOGRÁFIAi SZOLGÁLTATÓi utasítások adott felhasználási módjaihoz. A ( `CREATE CRYPTOGRAPHIC PROVIDER` vagy `ALTER CRYPTOGRAPHIC PROVIDER` ) rendszerű objektumok nem fognak megfelelően működni a Azure SQL Databaseba való Migrálás után.  


**Ajánlás**   
Objektumok áttekintése a `CREATE CRYPTOGRAPHIC PROVIDER` vagy a-val `ALTER CRYPTOGRAPHIC PROVIDER` . A szükséges összes ilyen objektum esetében távolítsa el a szolgáltatások használatát. Másik lehetőségként telepítse át SQL Server Azure-beli virtuális gépen

## <a name="cross-database-references"></a>Adatbázisok közötti hivatkozások<a id="CrossDataseReferences"></a>

**Title: a Azure SQL Database nem támogatja az adatbázisok közötti lekérdezéseket**   
**Kategória**: probléma   

**Leírás**   
Az ezen a kiszolgálón található adatbázisok több adatbázison keresztüli lekérdezéseket használnak, amelyek Azure SQL Database nem támogatottak. 


**Ajánlás**   
A Azure SQL Database nem támogatja az adatbázisok közötti lekérdezéseket. A következő műveletek ajánlottak: 
- Telepítse át a függő adatbázis (oka) t az Azure SQL Databasere, és használja Elastic Database lekérdezés (jelenleg előzetes verzió) funkcióit az Azure SQL Database-adatbázisok lekérdezéséhez. 
- Helyezze át a függő adatkészleteket más adatbázisokból az áttelepítendő adatbázisba. 
- Migrálás az Azure SQL felügyelt példányára.
- Migrálás SQL Serverre az Azure-beli virtuális gépen. 

További információ: [Azure SQL Database rugalmas adatbázis-lekérdezés megtekintése (előzetes verzió)](../../database/elastic-query-overview.md)

## <a name="database-compatibility"></a>Adatbázis-kompatibilitás<a id="DbCompatLevelLowerThan100"></a>

**Title: a Azure SQL Database nem támogatja a 100 alatti kompatibilitási szinteket.**   
**Kategória**: figyelmeztetés   

**Leírás**   
Az adatbázis-kompatibilitási szint értékes eszköz az adatbázis-modernizáció támogatásához azáltal, hogy lehetővé teszi az SQL Server adatbázismotor frissítését, miközben az alkalmazások működési állapotát az azonos frissítés előtti adatbázis-kompatibilitási szinten tartja. A Azure SQL Database nem támogatja a 100 alatti kompatibilitási szinteket. 


**Ajánlás**   
Értékelje ki, hogy az alkalmazás funkciói sértetlenek-e, ha az adatbázis kompatibilitási szintje 100-re van frissítve az Azure SQL felügyelt példányán. Másik lehetőségként telepítse át SQL Server Azure-beli virtuális gépen

## <a name="database-mail"></a>Adatbázisbeli e-mail<a id="DatabaseMail"></a>

**Title: a Database Mail Azure SQL Database nem támogatott.**   
**Kategória**: figyelmeztetés   

**Leírás**   
Ez a kiszolgáló a Database Mail funkciót használja, amely Azure SQL Database nem támogatott.


**Ajánlás**   
Érdemes lehet áttelepíteni a Database Mailt támogató Azure SQL felügyelt példányra.  Azt is megteheti, hogy az Azure functions és a Sendgrid használatával teszi elérhetővé a levelezés funkciót Azure SQL Databaseon.

További információ: [E-mail küldése Azure SQL Databaseról Azure functions parancsfájl használatával](https://github.com/microsoft/DataMigrationTeam/tree/master/IP%20and%20Scripts/AF%20SendMail)


## <a name="database-principal-alias"></a>Adatbázis egyszerű aliasneve<a id="DatabasePrincipalAlias"></a>

**Title: SYS. A DATABASE_PRINCIPAL_ALIASES megszűnt, és el lett távolítva.**   
**Kategória**: probléma   

**Leírás**   
SYS. DATABASE_PRINCIPAL_ALIASES megszűnt, és el lett távolítva a Azure SQL Database.  


**Ajánlás**   
Aliasok helyett szerepköröket használjon.

További információ: [megszűnt adatbázismotor-funkció SQL Server](/previous-versions/sql/2014/database-engine/discontinued-database-engine-functionality-in-sql-server-2016#Denali)


## <a name="disable_def_cnst_chk-option"></a>DISABLE_DEF_CNST_CHK lehetőség<a id="DisableDefCNSTCHK"></a>

**Title: a SET beállítás DISABLE_DEF_CNST_CHK megszűnt, és el lett távolítva.**   
**Kategória**: probléma   

**Leírás**   
A SET beállítás DISABLE_DEF_CNST_CHK megszűnt, és el lett távolítva a Azure SQL Database.  


További információ: [megszűnt adatbázismotor-funkció SQL Server](/previous-versions/sql/2014/database-engine/discontinued-database-engine-functionality-in-sql-server-2016#Denali)

## <a name="fastfirstrow-hint"></a>FASTFIRSTROW mutató<a id="FastFirstRowHint"></a>

**Title: a FASTFIRSTROW-lekérdezési mutató megszűnt, és el lett távolítva.**   
**Kategória**: figyelmeztetés   

**Leírás**   
A FASTFIRSTROW-lekérdezési mutató megszűnt, és el lett távolítva a Azure SQL Database.  


**Ajánlás**   
Ahelyett, hogy FASTFIRSTROW a lekérdezési tipp használata lehetőséget (FAST n).

További információ: [megszűnt adatbázismotor-funkció SQL Server](/previous-versions/sql/2014/database-engine/discontinued-database-engine-functionality-in-sql-server-2016#Denali)

## <a name="filestream"></a>FileStream<a id="FileStream"></a>

**Title: a FileStream nem támogatott Azure SQL Database**   
**Kategória**: probléma   

**Leírás**   
A FileStream funkció, amely lehetővé teszi a strukturálatlan adatmennyiségek, például a szöveges dokumentumok, a képek és a videók NTFS fájlrendszerbeli tárolására való tárolását Azure SQL Database nem támogatja. 


**Ajánlás**   
Töltse fel a strukturálatlan fájlokat az Azure Blob Storage-ba, és tárolja a fájlokkal kapcsolatos metaadatokat (név, típus, URL-cím, tárolási kulcs stb.) a Azure SQL Database. Előfordulhat, hogy újra kell telepítenie az alkalmazást, hogy lehetővé váljon a folyamatos átviteli Blobok Azure SQL Database. Másik lehetőségként telepítse át SQL Server Azure-beli virtuális gépre.

További információ: [streaming Blobok az Azure SQL blogba és onnan](https://azure.microsoft.com/blog/streaming-blobs-to-and-from-sql-azure/)


## <a name="linked-server"></a>Társított kiszolgáló<a id="LinkedServer"></a>

**Title: a csatolt kiszolgálói funkciók nem támogatottak Azure SQL Database**   
**Kategória**: probléma   

**Leírás**   
A csatolt kiszolgálók lehetővé teszik a SQL Server adatbázismotor számára a SQL Server példányán kívüli OLE DB adatforrásokra vonatkozó parancsok végrehajtását. 


**Ajánlás**   
A Azure SQL Database nem támogatja a csatolt kiszolgáló funkcióit. A következő műveletek ajánlottak a csatolt kiszolgálók szükségességének elkerülése érdekében: 
- Azonosítsa a távoli SQL-kiszolgálóktól függő adatkészleteket, és vegye át ezeket az áttelepíteni kívánt adatbázisba. 
- Telepítse át a függő adatbázis (oka) t az Azure-ba, és használja a Elastic Database Query (előzetes verzió) funkciót a Azure SQL Database adatbázisainak lekérdezéséhez. 

További információ: [Azure SQL Database rugalmas lekérdezés (előzetes verzió)](../../database/elastic-query-overview.md) 

## <a name="ms-dtc"></a>MS DTC<a id="MSDTCTransactSQL"></a>

**Title: az ELOSZTOTT tranzakciók megkezdése Azure SQL Database nem támogatott.**   
**Kategória**: probléma   

**Leírás**   
A Transact SQL BEGIN DISTRIBUTed TRANSACTION által indított és a Microsoft Distributed Transaction Coordinator (MS DTC) által felügyelt elosztott tranzakció nem támogatott a Azure SQL Databaseban.  


**Ajánlás**   
Tekintse át a Azure Migrate érintett objektumok szakaszát, és tekintse meg az összes objektumot a BEGIN DISTRUBUTED tranzakció használatával. Érdemes lehet áttelepíteni a résztvevő adatbázisait az Azure SQL felügyelt példányára, ahol a több példány közötti elosztott tranzakciók támogatottak (jelenleg előzetes verzióban). Másik lehetőségként telepítse át SQL Server Azure-beli virtuális gépre.

További információ: [több kiszolgáló közötti tranzakciók az Azure SQL felügyelt példányaihoz ](../../database/elastic-transactions-overview.md#transactions-across-multiple-servers-for-azure-sql-managed-instance)


## <a name="openrowset-bulk"></a>OPENROWSET (tömeges)<a id="OpenRowsetWithNonBlobDataSourceBulk"></a>

**Title: a nem Azure Blob Storage-adatforrással való tömeges műveletben használt OpenRowSet Azure SQL Database nem támogatott.**   
**Kategória**: probléma   

**Leírás** A OPENROWSET támogatja a tömeges műveleteket egy olyan beépített TÖMEGES szolgáltatón keresztül, amely lehetővé teszi, hogy egy fájlból származó adatok beolvasása és visszaadása sorhalmazként történjen. A nem Azure Blob Storage-adatforrással rendelkező OPENROWSET Azure SQL Database nem támogatott.


**Ajánlás**   
Azure SQL Database nem fér hozzá a fájlmegosztás és a Windows-mappákhoz, ezért a fájlokat importálni kell az Azure Blob Storage-ból. Ezért a OPENROWSET függvény csak a blob típusú adatforrásokat támogatja. Másik lehetőségként telepítse át SQL Server Azure-beli virtuális gépen

További információ: [Transact-SQL különbségek feloldása az áttelepítés során SQL Database](../../database/transact-sql-tsql-differences-sql-server.md#transact-sql-syntax-not-supported-in-azure-sql-database)


## <a name="openrowset-provider"></a>OPENROWSET (szolgáltató)<a id="OpenRowsetWithSQLAndNonSQLProvider"></a>

**Title: az SQL-vagy nem SQL-szolgáltatóval rendelkező OpenRowSet Azure SQL Database nem támogatott.**   
**Kategória**: probléma   

**Leírás**   
Az SQL-vagy nem SQL-szolgáltatóval történő OpenRowSet a csatolt kiszolgálók tábláihoz való hozzáférés alternatívája, amely egy egyszeri, alkalmi módszer a távoli adatok OLE DB használatával történő csatlakoztatásához és eléréséhez. Azure SQL Database nem támogatja az SQL-vagy nem SQL-szolgáltatóval való OpenRowSet.


**Ajánlás**   
A Azure SQL Database csak az Azure Blob Storage-ból való importálást támogatja a OPENROWSET. Másik lehetőségként telepítse át SQL Server Azure-beli virtuális gépen

További információ: [Transact-SQL különbségek feloldása az áttelepítés során SQL Database](../../database/transact-sql-tsql-differences-sql-server.md#transact-sql-syntax-not-supported-in-azure-sql-database)


## <a name="non-ansi-left-outer-join"></a>Nem ANSI bal külső illesztés<a id="NonANSILeftOuterJoinSyntax"></a>

**Title: a nem ANSI stílusú bal oldali külső illesztés megszűnt, és el lett távolítva.**   
**Kategória**: figyelmeztetés   

**Leírás**   
A nem ANSI stílusú bal oldali külső illesztés megszűnt, és el lett távolítva a Azure SQL Database. 


**Ajánlás**   
Használjon ANSI illesztési szintaxist.

További információ: [megszűnt adatbázismotor-funkció SQL Server](/previous-versions/sql/2014/database-engine/discontinued-database-engine-functionality-in-sql-server-2016#Denali)


## <a name="non-ansi-right-outer-join"></a>Nem ANSI jobb oldali külső illesztés<a id="NonANSIRightOuterJoinSyntax"></a>

**Title: nem ANSI stílusú jobb oldali külső illesztés megszűnt, és el lett távolítva.**   
**Kategória**: figyelmeztetés   

**Leírás**   
Nem ANSI stílusú jobb oldali külső illesztés megszűnt, és el lett távolítva a Azure SQL Database. 


**Ajánlás**   
Használjon ANSI illesztési szintaxist.

További információ: [megszűnt adatbázismotor-funkció SQL Server](/previous-versions/sql/2014/database-engine/discontinued-database-engine-functionality-in-sql-server-2016#Denali)

## <a name="next-column"></a>Következő oszlop<a id="NextColumn"></a>

**Title: a NEXT nevű táblák és oszlopok Azure SQL Database hibát eredményeznek.**   
**Kategória**: probléma   

**Leírás**   
A rendszer a következő nevű táblákat vagy oszlopokat észlelte. A Microsoft SQL Serverban bevezetett, az ANSI standard NEXT VALUE FOR függvény használata. Ha egy tábla vagy egy oszlop neve NEXT, és az oszlop ÉRTÉKEként aliasként van megadva, és ha az alapértelmezett ANSI-szabvány hiányzik, akkor az eredményül kapott utasítás hibát okozhat.  


**Ajánlás**   
Írja át az utasításokat, hogy az ANSI standard AS kulcsszót adja meg egy tábla vagy oszlop aliasakor. Ha például egy oszlop neve NEXT, és az oszlop ÉRTÉKEként aliasként van megjelölve, akkor a lekérdezés `SELECT NEXT VALUE FROM TABLE` hibát okoz, és a tábla értékeként válassza a tovább lehetőséget. Hasonlóképpen, ha a tábla neve NEXT (tovább), a tábla pedig ÉRTÉKként van aliasként, a lekérdezés `SELECT Col1 FROM NEXT VALUE` hibát okoz, és újra kell írni `SELECT Col1 FROM NEXT AS VALUE` .

## <a name="raiserror"></a>RAISERROR<a id="RAISERROR"></a>

**Title: az örökölt stílusú RAISERROR-hívásokat modern megfelelővel kell helyettesíteni.**   
**Kategória**: figyelmeztetés   

**Leírás**   
Az alábbi példához hasonló RAISERROR-hívások örökölt stílusúnak minősülnek, mivel nem tartalmazzák a vesszőket és a zárójeleket. `RAISERROR 50001 'this is a test'`. A RAISERROR meghívására szolgáló metódus megszűnt és el lett távolítva a Azure SQL Databaseban. 


**Ajánlás**   
Írja át az utasítást az aktuális RAISERROR szintaxissal, vagy értékelje ki, hogy a modern megközelítés `BEGIN TRY { }  END TRY BEGIN CATCH {  THROW; } END CATCH` megvalósítható-e.

További információ: [megszűnt adatbázismotor-funkció SQL Server](/previous-versions/sql/2014/database-engine/discontinued-database-engine-functionality-in-sql-server-2016#Denali)

## <a name="server-audits"></a>Kiszolgálók naplózása<a id="ServerAudits"></a>

**Title: a kiszolgálók naplózásának lecserélése Azure SQL Database naplózási funkciók használatával**   
**Kategória**: figyelmeztetés   

**Leírás**   
Azure SQL Database nem támogatja a kiszolgálók naplózását.


**Ajánlás**   
A kiszolgálók naplózásának cseréjéhez érdemes Azure SQL Database naplózási szolgáltatásokat.  Az Azure SQL támogatja a naplózást, és a funkciók gazdagabbak, mint a SQL Server. Az Azure SQL Database különböző adatbázis-műveleteket és eseményeket tud naplózni, például az adathozzáférést, a séma módosításait (DDL), az adatmódosításokat (DML), a fiókokat, a szerepköröket és az engedélyeket (DCL, biztonsági kivételek. Azure SQL Database a naplózás növeli a szervezet azon képességét, hogy mélyebb betekintést nyerjen az adatbázisán belüli eseményekre és változásokra, beleértve az adatfrissítéseket és a lekérdezéseket is. Alternatív megoldásként áttelepítheti az Azure SQL felügyelt példányát vagy SQL Server az Azure-beli virtuális gépen.

További információ: [Azure SQL Database naplózása ](../../database/auditing-overview.md)

## <a name="server-credentials"></a>Kiszolgáló hitelesítő adatai<a id="ServerCredentials"></a>

**Title: a kiszolgálói hatókörön belüli hitelesítő adatok nem támogatottak Azure SQL Database**   
**Kategória**: figyelmeztetés   

**Leírás**   
A hitelesítő adatok egy olyan rekord, amely a SQL Serveron kívüli erőforráshoz való kapcsolódáshoz szükséges hitelesítési adatokat (hitelesítő adatokat) tartalmazza. Azure SQL Database támogatja az adatbázis hitelesítő adatait, de nem a SQL Server hatókörében létrehozott fájlokat.   


**Ajánlás**   
Azure SQL Database támogatja az adatbázis-hatókörű hitelesítő adatokat. A kiszolgálói hatókörű hitelesítő adatok konvertálása adatbázis-hatókörű hitelesítő adatokra. Alternatív megoldásként migrálhat az Azure SQL felügyelt példányára vagy SQL Server az Azure-beli virtuális gépen

További információ: [adatbázis-hatókörű hitelesítő adatok létrehozása](/sql/t-sql/statements/create-database-scoped-credential-transact-sql)

## <a name="service-broker"></a>Szolgáltatásközvetítő<a id="ServiceBroker"></a>

**Title: a Service Broker funkció nem támogatott Azure SQL Database**   
**Kategória**: probléma   

**Leírás**   
A SQL Server Service Broker natív támogatást biztosít az üzenetkezelési és a üzenetsor-kezelési alkalmazások számára az SQL Server adatbázismotor számára. A Service Broker funkció nem támogatott a Azure SQL Databaseban.


**Ajánlás**   
A Service Broker funkció nem támogatott a Azure SQL Databaseban. Érdemes lehet áttelepíteni a felügyelt Azure SQL-példányra, amely támogatja a Service Broker szolgáltatást ugyanazon a példányon belül. Másik lehetőségként telepítse át SQL Server Azure-beli virtuális gépre. 

## <a name="server-scoped-triggers"></a>Kiszolgáló hatókörű eseményindítók<a id="ServerScopedTriggers"></a>

**Title: a kiszolgáló hatókörű trigger nem támogatott Azure SQL Database**   
**Kategória**: figyelmeztetés   

**Leírás**   
Az trigger egy speciális tárolt eljárás, amely egy táblázat bizonyos műveleteire reagál, például Beszúrás, törlés vagy az Adatfrissítés. A kiszolgálói hatókörű eseményindítók nem támogatottak Azure SQL Databaseban. A Azure SQL Database nem támogatja az eseményindítók következő beállításait: BEJELENTKEZÉShez, TITKOSÍTÁShoz, HOZZÁFŰZÉShez, nem REPLIKÁLÁShoz, külső név beállítás (nincs külső metódus támogatása), az összes kiszolgáló beállítás (DDL-eseményindító), eseményindító egy bejelentkezési eseményen (bejelentkezési eseményindító), Azure SQL Database nem támogatja a CLR-eseményindítókat.


**Ajánlás**   
Használja helyette az adatbázis szintű triggert. Alternatív megoldásként migrálhat az Azure SQL felügyelt példányára vagy SQL Server az Azure-beli virtuális gépen

További információ: [Transact-SQL különbségek feloldása az áttelepítés során SQL Database](../../database/transact-sql-tsql-differences-sql-server.md#transact-sql-syntax-not-supported-in-azure-sql-database)


## <a name="sql-agent-jobs"></a>SQL-ügynök feladatai<a id="AgentJobs"></a>

**Title: SQL Server Agent feladatok nem érhetők el Azure SQL Database**   
**Kategória**: figyelmeztetés   

**Leírás**   
A SQL Server Agent egy olyan Microsoft Windows-szolgáltatás, amely ütemezett felügyeleti feladatokat hajt végre, amelyek a SQL Server feladatok nevűek. SQL Server Agent feladatok nem érhetők el Azure SQL Databaseokban. 


**Javaslat** Használjon rugalmas feladatokat (előzetes verzió), amelyek a Azure SQL Database SQL Server Agent feladatainak pótlására szolgálnak. A Azure SQL Database rugalmas adatbázis-feladatok lehetővé teszi, hogy megbízhatóan hajtson végre több adatbázisra kiterjedő T-SQL-parancsfájlokat, miközben automatikusan újrapróbálkozik, és az esetleges befejezési garanciákat biztosít Érdemes lehet áttelepíteni az Azure SQL felügyelt példányára vagy SQL Server az Azure Virtual Machines-ra.

További információ: [Bevezetés a rugalmas adatbázis-feladatok használatába (előzetes verzió) ](../../database/elastic-jobs-overview.md)

## <a name="sql-database-size"></a>SQL Database mérete<a id="SQLDBDatabaseSize"></a>

**Cím: a Azure SQL Database nem támogatja az 100 TB-nál nagyobb adatbázis-méretet.**   
**Kategória**: probléma   

**Leírás**   
Az adatbázis mérete nagyobb, mint a maximálisan támogatott 100 TB-os méret. 


**Ajánlás**   
Értékelje ki, hogy az adatok archiválása vagy tömörítése több adatbázisba is elvégezhető-e. Másik lehetőségként telepítse át SQL Server Azure-beli virtuális gépre. 

További információ: [virtuális mag erőforrás-korlátok](../../database/resource-limits-vcore-single-databases.md) 

## <a name="sql-mail"></a>SQL-levelezés<a id="SqlMail"></a>

**Title: az SQL-levelezés megszűnt.**   
**Kategória**: figyelmeztetés   

**Leírás**   
Az SQL-levelezés megszűnt, és el lett távolítva a Azure SQL Database.


**Ajánlás**   
Érdemes lehet migrálni az Azure SQL felügyelt példányára vagy SQL Server az Azure Virtual Machines és a Database Mail használatára.

További információ: [megszűnt adatbázismotor-funkció SQL Server](/previous-versions/sql/2014/database-engine/discontinued-database-engine-functionality-in-sql-server-2016#Denali)

## <a name="systemprocedures110"></a>SystemProcedures110<a id="SystemProcedures110"></a>

**Title: észlelt utasítások, amelyek az eltávolított rendszerszintű tárolt eljárásokra hivatkoznak, amelyek nem érhetők el Azure SQL Databaseban.**   
**Kategória**: figyelmeztetés   

**Leírás**   
A nem támogatott rendszer és a kiterjesztett tárolt eljárások nem használhatók az Azure SQL Database-ben –,,,, és `sp_dboption` `sp_addserver` `sp_dropalias` `sp_activedirectory_obj` `sp_activedirectory_scp` `sp_activedirectory_start` .


**Ajánlás**    
Távolítsa el azokat a nem támogatott rendszereljárásokra mutató hivatkozásokat, amelyeket a rendszer eltávolított Azure SQL Database.

További információ: [megszűnt adatbázismotor-funkció SQL Server](/previous-versions/sql/2014/database-engine/discontinued-database-engine-functionality-in-sql-server-2016#Denali)

## <a name="trace-flags"></a>Nyomkövetési jelzők<a id="TraceFlags"></a>

**Title: a Azure SQL Database nem támogatja a nyomkövetési jelzőket**   
**Kategória**: figyelmeztetés   

**Leírás**   
A nyomkövetési jelzők az adott kiszolgáló jellemzőinek ideiglenes beállítására, illetve egy adott viselkedés kikapcsolására szolgálnak. A nyomkövetési jelzők gyakran használatosak a teljesítménnyel kapcsolatos problémák diagnosztizálásához, illetve a tárolt eljárások vagy a bonyolult számítógépes rendszerek hibakereséséhez. Azure SQL Database nem támogatja a nyomkövetési jelzőket. 


**Ajánlás**   
Tekintse át a Azure Migrate érintett objektumok szakaszát az összes olyan nyomkövetési jelző megjelenítéséhez, amely nem támogatott a Azure SQL Databaseben, és értékelje ki, hogy el lehet-e távolítani. Másik megoldásként áttelepítheti az Azure SQL felügyelt példányát, amely korlátozott számú globális nyomkövetési jelzőt és SQL Server Azure-beli virtuális gépen is támogat.

További információ: [Transact-SQL különbségek feloldása az áttelepítés során SQL Database](../../database/transact-sql-tsql-differences-sql-server.md#transact-sql-syntax-not-supported-in-azure-sql-database)


## <a name="windows-authentication"></a>Windows-hitelesítés<a id="WindowsAuthentication"></a>

**Title: a Windows-hitelesítéssel (integrált biztonság) leképezett adatbázis-felhasználók Azure SQL Database nem támogatottak.**   
**Kategória**: figyelmeztetés   

**Leírás**   
Azure SQL Database két hitelesítési típust támogat 
- SQL-hitelesítés: felhasználónevet és jelszót használ 
- Azure Active Directory hitelesítés: a Azure Active Directory által felügyelt identitásokat használ, és a felügyelt és integrált tartományok támogatják. 

A Windows-hitelesítéssel (integrált biztonsággal) leképezett adatbázis-felhasználók Azure SQL Database nem támogatottak. 



**Ajánlás**   
A helyi Active Directory összevonása Azure Active Directory. Ezután a Windows-identitás helyettesíthető az egyenértékű Azure Active Directory identitásokkal. Másik lehetőségként telepítse át SQL Server Azure-beli virtuális gépre.

További információ: [SQL Database biztonsági képességek](../../database/security-overview.md#authentication)

## <a name="xp_cmdshell"></a>XP_cmdshell<a id="XpCmdshell"></a>

**Title: a xp_cmdshell Azure SQL Database nem támogatott.**   
**Kategória**: probléma   

**Leírás**   
xp_cmdshell, amely a Windows parancs-rendszerhéjt, és egy karakterláncban továbbítja a végrehajtáshoz a Azure SQL Database nem támogatja. 


**Ajánlás**   
Tekintse át a Azure Migrate érintett objektumok szakaszát, hogy megjelenjenek a xp_cmdshell használó összes objektum, és értékelje ki, hogy az xp_cmdshellre vagy a befolyásolt objektumra mutató hivatkozás el lehet-e távolítani. Érdemes megfontolni a felhőalapú automatizálási és konfigurációs szolgáltatást kínáló Azure Automation felfedezését is. Másik lehetőségként telepítse át SQL Server Azure-beli virtuális gépre. 

## <a name="next-steps"></a>Következő lépések

A SQL Server Azure SQL Databasere való áttelepítésének megkezdéséhez tekintse meg az [SQL Server SQL Database áttelepítési útmutatót](sql-server-to-sql-database-guide.md).

- A Microsoft és a harmadik féltől származó szolgáltatások és eszközök egyik mátrixa, amely a különböző adatbázis-és adatáttelepítési forgatókönyvek, valamint a speciális feladatok elvégzéséhez nyújt segítséget, lásd: [szolgáltatás és eszközök az adatok áttelepítéséhez](../../../dms/dms-tools-matrix.md).

- Ha többet szeretne megtudni SQL Database lásd:
   - [A Azure SQL Database áttekintése](../../database/sql-database-paas-overview.md)
   - [Az Azure teljes tulajdonlási költsége kalkulátor](https://azure.microsoft.com/pricing/tco/calculator/) 

- Ha többet szeretne megtudni a Felhőbeli Migrálás keretrendszerével és bevezetési ciklusával kapcsolatban, olvassa el a következőt:
   -  [Felhőbevezetési keretrendszer az Azure-hoz](/azure/cloud-adoption-framework/migrate/azure-best-practices/contoso-migration-scale)
   -  [Ajánlott eljárások az Azure-ra való áttéréssel kapcsolatos díjszabási és méretezési feladatok elvégzéséhez](/azure/cloud-adoption-framework/migrate/azure-best-practices/migrate-best-practices-costs) 


- Az alkalmazás-hozzáférési réteg értékeléséhez lásd: [adathozzáférés áttelepítési eszközkészlete (előzetes verzió)](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit)
- Az A/B típusú adatelérési réteg végrehajtásával kapcsolatos további információkért lásd: [Database Experimentation Assistant](/sql/dea/database-experimentation-assistant-overview).
