---
title: Az Azure SQL felügyelt példányainak áttelepítéséhez SQL Server vonatkozó értékelési szabályok
description: Értékelési szabályok a forrás SQL Server példányával kapcsolatos problémák azonosításához, amelyeket az Azure SQL felügyelt példányain való áttelepítés előtt meg kell oldani.
ms.service: sql-managed-instance
ms.subservice: migration-guide
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: MashaMSFT
ms.author: mathoma
ms.reviewer: MashaMSFT
ms.date: 12/15/2020
ms.openlocfilehash: fc8959d44fbacd90916a045d23db4bee872c4670
ms.sourcegitcommit: a8ff4f9f69332eef9c75093fd56a9aae2fe65122
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2021
ms.locfileid: "105026036"
---
# <a name="assessment-rules-for-sql-server-to--azure-sql-managed-instance-migration"></a>Az Azure SQL felügyelt példányainak áttelepítéséhez SQL Server vonatkozó értékelési szabályok
[!INCLUDE[appliesto--sqldb](../../includes/appliesto-sqldb.md)]

Az áttelepítési eszközök ellenőrzik a forrás SQL Server példányát úgy, hogy számos értékelési szabályt futtatnak a SQL Server-adatbázis felügyelt példányra történő áttelepítése előtt megoldandó problémák azonosításához. 

Ez a cikk a SQL Server-adatbázis Azure SQL felügyelt példányra való áttelepítésének megvalósíthatósági felmérésére szolgáló szabályokat tartalmazza. 

## <a name="analysiscommand-job"></a>AnalysisCommand-feladatok<a id="AnalysisCommandJob"></a>

**Title: a AnalysisCommand Job lépés nem támogatott az Azure SQL felügyelt példányain.**   
**Kategória**: figyelmeztetés   


**Leírás**   
Ez egy Analysis Services parancsot futtató feladatütemezés. Az Azure SQL felügyelt példányain nem támogatott a AnalysisCommand-feladatok lépése.

**Ajánlás**     
Tekintse át a Azure Migrate érintett objektumok szakaszát az Analysis Service parancs-feladat lépéseit használó összes feladat megjelenítéséhez, és értékelje ki, hogy a feladat lépését vagy az érintett objektumot el lehet-e távolítani. Másik lehetőségként telepítse át SQL Server Azure-beli virtuális gépre.

További információ: [SQL Server Agent különbségek az Azure SQL felügyelt példányában ](../../managed-instance/transact-sql-tsql-differences-sql-server.md#sql-server-agent)

## <a name="analysisquery-job"></a>AnalysisQuery-feladatok<a id="AnalysisQueryJob"></a>

**Title: a AnalysisQuery Job lépés nem támogatott az Azure SQL felügyelt példányain.**   
**Kategória**: figyelmeztetés   

**Leírás**   
Ez egy Analysis Services lekérdezést futtató feladatütemezés. Az Azure SQL felügyelt példányain nem támogatott a AnalysisQuery-feladatok lépése.


**Ajánlás**   
Tekintse át a Azure Migrate érintett objektumok szakaszát az Analysis Service lekérdezési feladat lépéseit használó összes feladat megjelenítéséhez, és értékelje ki, hogy a feladat lépését vagy az érintett objektumot el lehet-e távolítani. Másik lehetőségként telepítse át SQL Server Azure-beli virtuális gépre.

További információ: [SQL Server Agent különbségek az Azure SQL felügyelt példányában ](../../managed-instance/transact-sql-tsql-differences-sql-server.md#sql-server-agent)


## <a name="assembly-from-file"></a>Szerelvény fájlból<a id="AssemblyFromFile"></a>

**Title: a "ASSEMBLY létrehozása" és az "ALTER ASSEMBLY" paraméter nem támogatott az Azure SQL felügyelt példányában.**   
**Kategória**: figyelmeztetés   

**Leírás**   
Az Azure SQL felügyelt példánya nem fér hozzá a fájlmegosztási vagy a Windows-mappákhoz. Tekintse meg az "érintett objektumok" szakaszt BULK INSERT olyan utasítások adott felhasználási módjainál, amelyek nem hivatkoznak Azure-blobra. A (z) "BULK INSERT" objektummal rendelkező objektumok, amelyekben a forrás nem az Azure Blob Storage szolgáltatás nem fog működni az Azure SQL felügyelt példányra való Migrálás után.


**Ajánlás**   
Az Azure Blob Storage-ba való Migrálás során helyi fájlokat vagy fájlmegosztást használó BULK INSERT-utasításokat kell konvertálnia, ha az Azure SQL felügyelt példányára végez áttelepítést. Másik lehetőségként telepítse át SQL Server Azure-beli virtuális gépre. 

További információ: [CLR-beli különbségek az Azure SQL felügyelt példányában ](../../managed-instance/transact-sql-tsql-differences-sql-server.md#clr)

## <a name="bulk-insert"></a>Tömeges beszúrás<a id="BulkInsert"></a>

**Title: a nem Azure-beli blob-adatforrással rendelkező BULK INSERT az Azure SQL felügyelt példányain nem támogatott.**   
**Kategória**: probléma   

**Leírás**   
Az Azure SQL felügyelt példánya nem fér hozzá a fájlmegosztási vagy a Windows-mappákhoz. Tekintse meg az "érintett objektumok" szakaszt BULK INSERT olyan utasítások adott felhasználási módjainál, amelyek nem hivatkoznak Azure-blobra. A (z) "BULK INSERT" objektummal rendelkező objektumok, amelyekben a forrás nem az Azure Blob Storage szolgáltatás nem fog működni az Azure SQL felügyelt példányra való Migrálás után.


**Ajánlás**   
Az Azure Blob Storage-ba való Migrálás során helyi fájlokat vagy fájlmegosztást használó BULK INSERT-utasításokat kell konvertálnia, ha az Azure SQL felügyelt példányára végez áttelepítést.

További információ: [tömeges beszúrási és OpenRowset különbségek az Azure SQL felügyelt példányában ](../../managed-instance/transact-sql-tsql-differences-sql-server.md#bulk-insert--openrowset)


## <a name="clr-security"></a>CLR-beli biztonság<a id="ClrStrictSecurity"></a>

**Title: BIZTONSÁGOSként vagy EXTERNAL_ACCESSként megjelölt CLR-szerelvények nem BIZTONSÁGOSNAK minősülnek**   
**Kategória**: probléma   

**Leírás**   
A CLR szigorú biztonsági üzemmódját a felügyelt Azure SQL-példányon kell kikényszeríteni. Ez a mód alapértelmezés szerint engedélyezve van, és bevezeti a felhasználó által definiált CLR-szerelvényeket tartalmazó adatbázisok feltörésének változásait a biztonságos vagy EXTERNAL_ACCESS jelöléssel.


**Ajánlás**   
A CLR kód-hozzáférési biztonságot (CAS) használ a .NET-keretrendszerben, amely már nem támogatott biztonsági határként. A SQL Server 2017 (14. x) adatbázismotortől kezdve `sp_configure` a CLR-beli szerelvények biztonságának növelése érdekében be van állítva egy CLR szigorú biztonsági megoldás. A CLR szigorú biztonsági beállításai alapértelmezés szerint engedélyezve vannak, és a biztonságos és EXTERNAL_ACCESS CLR-szerelvényeket úgy kezeli, mintha nem biztonságosak voltak megjelölve. Ha a CLR szigorú biztonsága le van tiltva, akkor a PERMISSION_SET = SAFE használatával létrehozott CLR-szerelvény hozzáférhet a külső rendszererőforrásokhoz, a nem felügyelt kódok meghívásához és a sysadmin jogosultságok beolvasásához. A szigorú biztonság engedélyezése után a nem aláírt szerelvények betöltése sikertelen lesz. Emellett, ha egy adatbázis biztonságos vagy EXTERNAL_ACCESS szerelvényekkel rendelkezik, az adatbázis-utasítások visszaállítása vagy CSATOLÁSa elvégezhető, de a szerelvények nem tölthetők be. A szerelvények betöltéséhez meg kell változtatnia vagy el kell távolítania, majd újra létre kell hoznia az egyes szerelvényeket, hogy azok egy olyan tanúsítvánnyal vagy aszimmetrikus kulccsal legyenek aláírva, amely rendelkezik a kiszolgálón a nem biztonságos SZERELVÉNYre vonatkozó engedéllyel.

További információ: [CLR Strict Security](/sql/database-engine/configure-windows/clr-strict-security)

## <a name="compute-clause"></a>Számítási záradék<a id="ComputeClause"></a>

**Title: a számítási záradék megszűnt, és el lett távolítva.**   
**Kategória**: figyelmeztetés   

**Leírás**   
A számítási záradék az eredményhalmaz végén további összesítő oszlopként megjelenő összesítéseket hoz létre. Ez a záradék azonban már nem támogatott az Azure SQL felügyelt példányain.



**Ajánlás**   
A T-SQL-modult a kumulatív operátor használatával kell újraírni. Az alábbi kód azt mutatja be, hogyan cserélhető ki a számítás a kumulatív módon: 

```sql
USE AdventureWorks GO;  

SELECT SalesOrderID, UnitPrice, UnitPriceDiscount 
FROM Sales.SalesOrderDetail 
ORDER BY SalesOrderID COMPUTE SUM(UnitPrice), SUM(UnitPriceDiscount) 
BY SalesOrderID GO; 

SELECT SalesOrderID, UnitPrice, UnitPriceDiscount,SUM(UnitPrice) as UnitPrice , 
SUM(UnitPriceDiscount) as UnitPriceDiscount 
FROM Sales.SalesOrderDetail 
GROUP BY SalesOrderID, UnitPrice, UnitPriceDiscount WITH ROLLUP;
```

További információ: [megszűnt adatbázismotor-funkció SQL Server](/previous-versions/sql/2014/database-engine/discontinued-database-engine-functionality-in-sql-server-2016#Denali)

## <a name="cryptographic-provider"></a>Kriptográfiai szolgáltató<a id="CryptographicProvider"></a>

**Title: az Azure SQL felügyelt példányain nem támogatott a titkosítási szolgáltató létrehozása vagy az ALTER titkosítási szolgáltató használata.**   
**Kategória**: probléma   

**Leírás**   
Az Azure SQL felügyelt példánya nem támogatja a KRIPTOGRÁFIAi SZOLGÁLTATÓi utasításokat, mert nem fér hozzá a fájlokhoz. Tekintse meg az érintett objektumok szakaszt a KRIPTOGRÁFIAi SZOLGÁLTATÓi utasítások adott felhasználási módjaihoz. A "titkosítási szolgáltató létrehozása" vagy "ALTER KRIPTOGRÁFIAi szolgáltató" művelettel rendelkező objektumok nem fognak megfelelően működni az Azure SQL felügyelt példányra való Migrálás után.


**Ajánlás**   
Tekintse át az objektumokat a "titkosítási szolgáltató létrehozása" vagy a "titkosítási szolgáltató módosítása" művelettel. A szükséges összes ilyen objektum esetében távolítsa el a szolgáltatások használatát. Másik lehetőségként telepítse át SQL Server Azure-beli virtuális gépre. 

További információ: [titkosítási szolgáltatói különbségek az Azure SQL felügyelt példányában ](../../managed-instance/transact-sql-tsql-differences-sql-server.md#cryptographic-providers)

## <a name="database-compatibility"></a>Adatbázis-kompatibilitás<a id="DbCompatLevelLowerThan100"></a>

**Title: a 100 alatti adatbázis-kompatibilitási szint nem támogatott**   
**Kategória**: figyelmeztetés   

**Leírás**   
Az adatbázis-kompatibilitási szint értékes eszköz az adatbázis-modernizáció támogatásához azáltal, hogy lehetővé teszi az SQL Server adatbázismotor frissítését, miközben az alkalmazások működési állapotát az azonos frissítés előtti adatbázis-kompatibilitási szinten tartja. Az Azure SQL felügyelt példánya nem támogatja a 100 alatti kompatibilitási szinteket. Ha az Azure SQL felügyelt példányán a 100 alatti kompatibilitási szinttel rendelkező adatbázis helyreáll, a kompatibilitási szint a 100-re frissül. 


**Javaslat**... Értékelje ki, hogy az alkalmazás funkciói sértetlenek-e, ha az adatbázis kompatibilitási szintje 100-re van frissítve az Azure SQL felügyelt példányán. Másik lehetőségként telepítse át SQL Server Azure-beli virtuális gépre.

További információ: [a felügyelt Azure SQL-példány támogatott kompatibilitási szintjei ](../../managed-instance/transact-sql-tsql-differences-sql-server.md#compatibility-levels)

## <a name="database-principal-alias"></a>Adatbázis egyszerű aliasneve<a id="DatabasePrincipalAlias"></a>

**Title: SYS. A DATABASE_PRINCIPAL_ALIASES megszűnt, és el lett távolítva.**   
**Kategória**: probléma   

**Leírás**   
SYS. A DATABASE_PRINCIPAL_ALIASES megszűnt, és el lett távolítva az Azure SQL felügyelt példányában.


**Ajánlás**   
Aliasok helyett szerepköröket használjon.

További információ: [megszűnt adatbázismotor-funkció SQL Server](/previous-versions/sql/2014/database-engine/discontinued-database-engine-functionality-in-sql-server-2016#Denali)

## <a name="disable_def_cnst_chk-option"></a>DISABLE_DEF_CNST_CHK lehetőség<a id="DisableDefCNSTCHK"></a>

**Title: a SET beállítás DISABLE_DEF_CNST_CHK megszűnt, és el lett távolítva.**   
**Kategória**: probléma   

**Leírás**   
A SET beállítás DISABLE_DEF_CNST_CHK megszűnt, és el lett távolítva az Azure SQL felügyelt példányában.


További információ: [megszűnt adatbázismotor-funkció SQL Server](/previous-versions/sql/2014/database-engine/discontinued-database-engine-functionality-in-sql-server-2016#Denali)

## <a name="fastfirstrow-hint"></a>FASTFIRSTROW mutató<a id="FastFirstRowHint"></a>

**Title: a FASTFIRSTROW-lekérdezési mutató megszűnt, és el lett távolítva.**   
**Kategória**: figyelmeztetés   

**Leírás**   
A FASTFIRSTROW-lekérdezési mutató megszűnt, és el lett távolítva az Azure SQL felügyelt példányában.


**Ajánlás**   
Ahelyett, hogy FASTFIRSTROW a lekérdezési tipp használata lehetőséget (FAST n).

További információ: [megszűnt adatbázismotor-funkció SQL Server](/previous-versions/sql/2014/database-engine/discontinued-database-engine-functionality-in-sql-server-2016#Denali)

## <a name="filestream"></a>FileStream<a id="FileStream"></a>

**Title: a FileStream és a filé nem támogatott az Azure SQL felügyelt példányain.**   
**Kategória**: probléma   

**Leírás**   
A FileStream funkció lehetővé teszi, hogy az Azure SQL felügyelt példányain nem használhatók strukturálatlan adatmennyiségek, például szöveges dokumentumok, képek és videók tárolására az NTFS fájlrendszerben. **Ez az adatbázis nem telepíthető át, mert a FileStream fájlcsoportok tartalmazó biztonsági másolat nem állítható vissza az Azure SQL felügyelt példányain.**


**Ajánlás**   
Töltse fel a strukturálatlan fájlokat az Azure Blob Storage-ba, és tárolja a fájlokkal kapcsolatos metaadatokat (név, típus, URL-cím, tárolási kulcs stb.) az Azure SQL felügyelt példányában. Előfordulhat, hogy újra kell telepítenie az alkalmazást, hogy a stream-Blobok engedélyezve legyenek az Azure SQL felügyelt példányain. Másik lehetőségként telepítse át SQL Server Azure-beli virtuális gépre.

További információ: [SQL Azure blogon lévő és onnan beérkező Blobok továbbítása](https://azure.microsoft.com/en-in/blog/streaming-blobs-to-and-from-sql-azure/)

## <a name="heterogeneous-ms-dtc"></a>Heterogén MS DTC<a id="MIHeterogeneousMSDTCTransactSQL"></a>

**Title: az ELOSZTOTT tranzakciók megkezdése nem SQL Server távoli kiszolgálóval nem támogatott az Azure SQL felügyelt példányain.**   
**Kategória**: probléma   

**Leírás**   
A Transact SQL megkezdi az ELOSZTOTT tranzakciót, és a Microsoft Distributed Transaction Coordinator (MS DTC) által felügyelt elosztott tranzakció nem támogatott az Azure SQL felügyelt példányain, ha a távoli kiszolgáló nem SQL Server. 


**Ajánlás**   
Tekintse át a Azure Migrate érintett objektumok szakaszát, és tekintse meg az összes objektumot a BEGIN DISTRUBUTED tranzakció használatával. Érdemes lehet áttelepíteni a résztvevő adatbázisait az Azure SQL felügyelt példányára, ahol a több példány közötti elosztott tranzakciók támogatottak (jelenleg előzetes verzióban). Másik lehetőségként telepítse át SQL Server Azure-beli virtuális gépre.

További információ: [több kiszolgáló közötti tranzakciók az Azure SQL felügyelt példányaihoz ](../../database/elastic-transactions-overview.md#transactions-across-multiple-servers-for-azure-sql-managed-instance)

## <a name="homogenous-ms-dtc"></a>Homogén MS DTC<a id="MIHomogeneousMSDTCTransactSQL"></a>

**Title: az ELOSZTOTT tranzakciók megkezdése több kiszolgáló között támogatott az Azure SQL felügyelt példányain.**   
**Kategória**: probléma   

**Leírás**   
A Transact SQL BEGIN DISTRIBUTed Transaction által indított elosztott tranzakció, amelyet a Microsoft Distributed Transaction Coordinator (MS DTC) felügyel, több kiszolgálón is támogatott az Azure SQL felügyelt példányain.


**Ajánlás**   
Tekintse át a Azure Migrate érintett objektumok szakaszát, és tekintse meg az összes objektumot a BEGIN DISTRUBUTED tranzakció használatával. Érdemes lehet áttelepíteni a résztvevő adatbázisait az Azure SQL felügyelt példányára, ahol a több példány közötti elosztott tranzakciók támogatottak (jelenleg előzetes verzióban). Másik lehetőségként telepítse át SQL Server Azure-beli virtuális gépre. 

További információ: [több kiszolgáló közötti tranzakciók az Azure SQL felügyelt példányaihoz](../../database/elastic-transactions-overview.md#transactions-across-multiple-servers-for-azure-sql-managed-instance)


## <a name="linked-server-non-sql-provider"></a>Csatolt kiszolgáló (nem SQL-szolgáltató)<a id="LinkedServerWithNonSQLProvider"></a>

**Title: a nem SQL Server szolgáltatóval rendelkező csatolt kiszolgáló nem támogatott az Azure SQL felügyelt példányain.**   
**Kategória**: probléma   

**Leírás**   
A csatolt kiszolgálók lehetővé teszik a SQL Server adatbázismotor számára a SQL Server példányán kívüli OLE DB adatforrásokra vonatkozó parancsok végrehajtását. A nem SQL Server szolgáltatóval rendelkező csatolt kiszolgáló nem támogatott az Azure SQL felügyelt példányain. 


**Ajánlás**   
Az Azure SQL felügyelt példánya nem támogatja a csatolt kiszolgáló funkcióit, ha a távoli kiszolgáló szolgáltatója nem SQL Server, például Oracle, Sybase stb. 

A következő műveletek ajánlottak a csatolt kiszolgálók szükségességének elkerülése érdekében: 
- Azonosítsa a függő adatbázis (oka) t a távoli nem SQL-kiszolgálókról, és vegye figyelembe, hogy ezeket a rendszer áthelyezi az áttelepíteni kívánt adatbázisba. 
- Telepítse át a függő adatbázis (oka) t a támogatott célokba (például SQL felügyelt példány, SQL Database, Azure szinapszis SQL és SQL Server példányok). 
- Érdemes lehet csatolt kiszolgálót létrehozni az Azure SQL felügyelt példánya és a SQL Server az Azure virtuális gépen (SQL VM).  Ezután az SQL-alapú virtuális gépről hozzon létre csatolt kiszolgálót Oracle, Sybase stb. Ez a megközelítés két ugrást tartalmaz, de ideiglenes megoldásként is használható.  
- Másik lehetőségként telepítse át SQL Server Azure-beli virtuális gépre.

További információ: [a csatolt kiszolgálókkal kapcsolatos különbségek az Azure SQL felügyelt példányában](../../managed-instance/transact-sql-tsql-differences-sql-server.md#linked-servers)

## <a name="merge-job"></a>Egyesítési feladatok<a id="MergeJob"></a>

**Title: az Azure SQL felügyelt példányain nem támogatott az egyesítési feladatok lépése.**   
**Kategória**: figyelmeztetés   

**Leírás**   
Ez egy olyan lépés, amely aktiválja a replikálási egyesítési ügynököt. A replikálási egyesítési ügynök egy segédprogram végrehajtható fájlja, amely az adatbázis tábláiban tárolt kezdeti pillanatképet alkalmazza az előfizetőknek. Emellett egyesíti a közzétevőn a kezdeti pillanatkép létrehozása után bekövetkezett növekményes adatváltozásokat, és összehangolja az ütközéseket a konfigurált szabályoknak vagy egy Ön által létrehozott egyéni feloldónak megfelelően. Az egyesítési feladatok lépése nem támogatott az Azure SQL felügyelt példányain.


**Ajánlás**   
Tekintse át a Azure Migrate érintett objektumok szakaszát, és tekintse meg az összes olyan feladatot, amely Merge Job lépést használ, és értékelje ki, hogy a feladat lépése vagy az érintett objektum eltávolítható-e. Másik lehetőségként telepítse át SQL Server Azure-beli virtuális gépen

További információ: [SQL Server Agent különbségek az Azure SQL felügyelt példányában ](../../managed-instance/transact-sql-tsql-differences-sql-server.md#sql-server-agent)


## <a name="mi-database-size"></a>MI adatbázis mérete<a id="MIDatabaseSize<"></a>

**Title: az Azure SQL felügyelt példánya nem támogatja a 8 TB-nál nagyobb adatbázis-méretet.**   
**Kategória**: probléma   

**Leírás**   
Az adatbázis mérete nagyobb, mint a példányok maximálisan lefoglalt tárolója. **Ez az adatbázis nem választható át áttelepítésre, mert a méret túllépte az engedélyezett korlátot.**


**Ajánlás**   
Annak kiértékelése, hogy az adatok archiválva tömörítettek-e, vagy több adatbázisba vannak-e osztva. Másik lehetőségként telepítse át SQL Server Azure-beli virtuális gépre.

További információ: [a felügyelt Azure SQL-példány hardver-létrehozási jellemzői ](../../managed-instance/resource-limits.md#hardware-generation-characteristics)



## <a name="mi-instance-size"></a>MI példány mérete<a id="MIInstanceSize<"></a>

**Title: a példányok maximális tárolási mérete az Azure SQL felügyelt példányain nem lehet nagyobb 8 TB-nál.**   
**Kategória**: figyelmeztetés   

**Leírás**   
Az összes adatbázis mérete nagyobb, mint a példányok maximálisan lefoglalt tárolója.  


**Ajánlás**   
Érdemes lehet áttelepíteni az adatbázisokat különböző Azure SQL felügyelt példányokra, vagy SQL Server az Azure-beli virtuális gépen, ha az összes adatbázisnak ugyanabban a példányban kell lennie. 

További információ: [a felügyelt Azure SQL-példány hardver-létrehozási jellemzői ](../../managed-instance/resource-limits.md#hardware-generation-characteristics)


## <a name="multiple-log-files"></a>Több naplófájl<a id="MultipleLogFiles<"></a>

**Title: az Azure SQL felügyelt példánya nem támogatja több naplófájl használatát.**   
**Kategória**: probléma   

**Leírás**   
SQL Server lehetővé teszi, hogy az adatbázis több fájlba jelentkezzen be. Ez az adatbázis több naplófájlt tartalmaz, amelyek nem támogatottak az Azure SQL felügyelt példányain. * * Ez az adatbázis nem telepíthető át, mert a biztonsági mentés nem állítható vissza az Azure SQL felügyelt példányain. 
**

**Ajánlás**   
Az Azure SQL felügyelt példánya csak egyetlen naplót támogat az adatbázison. Az adatbázis Azure-ba való áttelepítése előtt törölnie kell az összes naplófájlt, 

```sql
ALTER DATABASE [database_name] REMOVE FILE [log_file_name]
```

További információ: [a felügyelt Azure SQL-példány nem támogatott adatbázis-beállításai  ](../../managed-instance/transact-sql-tsql-differences-sql-server.md#database-options)



## <a name="next-column"></a>Következő oszlop<a id="NextColumn"></a>

**Title: a NEXT nevű táblák és oszlopok hibát jeleznek az Azure SQL felügyelt példányaiban.**   
**Kategória**: probléma   

**Leírás**   
A rendszer a következő nevű táblákat vagy oszlopokat észlelte. A Microsoft SQL Serverban bevezetett, az ANSI standard NEXT VALUE FOR függvény használata. Ha egy tábla vagy egy oszlop neve NEXT, és az oszlop ÉRTÉKEként aliasként van megadva, és ha az alapértelmezett ANSI-szabvány hiányzik, akkor az eredményül kapott utasítás hibát okozhat.


**Ajánlás**   
Írja át az utasításokat, hogy az ANSI standard AS kulcsszót adja meg egy tábla vagy oszlop aliasakor. Ha például egy oszlop neve NEXT, és az oszlop ÉRTÉKként van aliasa, a lekérdezés a tábla következő ÉRTÉKének kiválasztásakor hibát okoz, és a tábla ÉRTÉKEként válassza a NEXT (tovább) lehetőséget. Hasonlóképpen, ha a tábla neve NEXT (tovább), és a tábla ÉRTÉKEként aliasként van megjelölve, a lekérdezés a következő Col1 válassza ki a hibát, és a következő értékből válassza a Col1 lehetőséget.



## <a name="non-ansi-style-left-outer-join"></a>Nem ANSI stílusú bal oldali külső illesztés<a id="NonANSILeftOuterJoinSyntax"></a>

**Title: a nem ANSI stílusú bal oldali külső illesztés megszűnt, és el lett távolítva.**   
**Kategória**: figyelmeztetés   

**Leírás**   
A nem ANSI stílusú bal oldali külső illesztés megszűnt, és el lett távolítva az Azure SQL felügyelt példányában. 


**Ajánlás**   
Használjon ANSI illesztési szintaxist.

További információ: [megszűnt adatbázismotor-funkció SQL Server](/previous-versions/sql/2014/database-engine/discontinued-database-engine-functionality-in-sql-server-2016#Denali)

## <a name="non-ansi-style-right-outer-join"></a>Nem ANSI stílusú jobb oldali külső illesztés<a id="NonANSIRightOuterJoinSyntax"></a>

**Title: nem ANSI stílusú jobb oldali külső illesztés megszűnt, és el lett távolítva.**   
**Kategória**: figyelmeztetés   

**Leírás**   
Nem ANSI stílusú jobb oldali külső illesztés megszűnt, és el lett távolítva az Azure SQL felügyelt példányában. 



További információ: [megszűnt adatbázismotor-funkció SQL Server](/previous-versions/sql/2014/database-engine/discontinued-database-engine-functionality-in-sql-server-2016#Denali)

**Ajánlás**   
Használjon ANSI illesztési szintaxist.

## <a name="databases-exceed-100"></a>Az adatbázisok mérete meghaladja a 100 <a id="NumDbExceeds100"></a>

**Title: az Azure SQL felügyelt példánya egy példányon legfeljebb 100 adatbázist támogat.**   
**Kategória**: figyelmeztetés   

**Leírás**   
Az Azure SQL felügyelt példányában támogatott adatbázisok maximális száma 100, kivéve, ha elérte a példány tárolási méretének korlátját.



**Ajánlás**   
Érdemes lehet áttelepíteni az adatbázisokat különböző Azure SQL felügyelt példányokra, vagy SQL Server az Azure-beli virtuális gépen, ha az összes adatbázisnak ugyanabban a példányban kell lennie. 

További információ: [Azure SQL felügyelt példányok erőforrás-korlátai ](../../managed-instance/resource-limits.md#service-tier-characteristics)

## <a name="openrowset-non-blob-data-source"></a>OPENROWSET (nem blob típusú adatforrás)<a id="OpenRowsetWithNonBlobDataSourceBulk"></a>

**Title: a nem Azure Blob Storage-adatforrással való tömeges műveletben használt OpenRowSet az Azure SQL felügyelt példányain nem támogatott.**   
**Kategória**: probléma   

**Leírás**   
A OPENROWSET támogatja a tömeges műveleteket egy olyan beépített TÖMEGES szolgáltatón keresztül, amely lehetővé teszi, hogy egy fájlból származó adatok beolvasása és visszaadása sorhalmazként történjen. A nem Azure Blob Storage-adatforrással rendelkező OPENROWSET nem támogatottak az Azure SQL felügyelt példányain. 



**Ajánlás**   
A OPENROWSET függvény csak SQL Server példányokon (felügyelt, helyszíni vagy Virtual Machines) található lekérdezések végrehajtására használható. Szolgáltatóként csak a SQLNCLI, a SQLNCLI11 és a SQLOLEDB értékek támogatottak. Ezért a javasolt művelet az, hogy azonosítsa a függő adatbázis (oka) t a távoli nem SQL-kiszolgálókról, és érdemes áthelyezni ezeket az áttelepíteni kívánt adatbázisba. Másik lehetőségként telepítse át SQL Server Azure-beli virtuális gépen

További információ: [tömeges beszúrási és OpenRowset különbségek az Azure SQL felügyelt példányában ](../../managed-instance/transact-sql-tsql-differences-sql-server.md#bulk-insert--openrowset)

## <a name="openrowset-non-sql-provider"></a>OPENROWSET (nem SQL-szolgáltató)<a id="OpenRowsetWithNonSQLProvider"></a>

**Title: a nem SQL-szolgáltatóval rendelkező OpenRowSet az Azure SQL felügyelt példányain nem támogatott.**   
**Kategória**: probléma   

**Leírás**   
Ez a módszer a csatolt kiszolgáló tábláihoz való hozzáférés alternatívája, amely egy egyszeri, ad hoc módszer a távoli adatok OLE DB használatával történő csatlakoztatásához és eléréséhez. A nem SQL-szolgáltatóval rendelkező OpenRowSet az Azure SQL felügyelt példányain nem támogatott. 



**Ajánlás**   
A OPENROWSET függvény csak SQL Server példányokon (felügyelt, helyszíni vagy Virtual Machines) található lekérdezések végrehajtására használható. Szolgáltatóként csak a SQLNCLI, a SQLNCLI11 és a SQLOLEDB értékek támogatottak. Ezért a javasolt művelet az, hogy azonosítsa a függő adatbázis (oka) t a távoli nem SQL-kiszolgálókról, és érdemes áthelyezni ezeket az áttelepíteni kívánt adatbázisba.

További információ: [tömeges beszúrási és OpenRowset különbségek az Azure SQL felügyelt példányában ](../../managed-instance/transact-sql-tsql-differences-sql-server.md#bulk-insert--openrowset)


## <a name="powershell-job"></a>PowerShell-feladatok<a id="PowerShellJob"></a>

**Title: a PowerShell-feladatok lépése nem támogatott az Azure SQL felügyelt példányain.**   
**Kategória**: figyelmeztetés   

**Leírás**   
Egy PowerShell-parancsfájlt futtató feladatütemezés. A PowerShell-feladatok lépése nem támogatott az Azure SQL felügyelt példányain. 



**Ajánlás**   
Tekintse át a Azure Migrate érintett objektumok szakaszát, és tekintse meg az összes feladatot a PowerShell-feladat lépésével, és értékelje ki, hogy a feladat lépése vagy az érintett objektum eltávolítható-e.  Értékelje ki, hogy Azure Automation használható-e. Másik lehetőségként telepítse át SQL Server Azure-beli virtuális gépen

További információ: [SQL Server Agent különbségek az Azure SQL felügyelt példányában ](../../managed-instance/transact-sql-tsql-differences-sql-server.md#sql-server-agent)

## <a name="queue-reader-job"></a>Üzenetsor-olvasó feladata<a id="QueueReaderJob"></a>

**Title: a várólista-olvasó feladata nem támogatott az Azure SQL felügyelt példányain.**   
**Kategória**: figyelmeztetés   

**Leírás**   
Ez egy olyan feladattípus, amely aktiválja a replikálási sorolvasó ügynök. A replikációs sorolvasó ügynök egy végrehajtható fájl, amely egy Microsoft SQL Server-várólistában vagy egy Microsoft Message-várólistában tárolt üzeneteket olvas be, majd ezeket az üzeneteket a közzétevőre alkalmazza. A sorolvasó ügynök olyan pillanatkép-és tranzakciós kiadványokkal használható, amelyek engedélyezik a várólistán lévő frissítést. A várólista-olvasó feladata nem támogatott az Azure SQL felügyelt példányain.


**Ajánlás**   
Tekintse át a Azure Migrate érintett objektumok szakaszát az összes olyan feladat megjelenítéséhez, amely a várólista-olvasó feladat lépését használja, és értékelje ki, hogy a feladat lépése vagy az érintett objektum eltávolítható-e. Másik lehetőségként telepítse át SQL Server Azure-beli virtuális gépre.

További információ: [SQL Server Agent különbségek az Azure SQL felügyelt példányában ](../../managed-instance/transact-sql-tsql-differences-sql-server.md#sql-server-agent)


## <a name="raiserror"></a>RAISERROR<a id="RAISERROR"></a>

**Title: az örökölt stílusú RAISERROR-hívásokat modern megfelelővel kell helyettesíteni.**   
**Kategória**: figyelmeztetés   

**Leírás**   
Az alábbi példához hasonló RAISERROR-hívások örökölt stílusúnak minősülnek, mivel nem tartalmazzák a vesszőket és a zárójeleket. RAISERROR 50001 "Ez egy teszt". A RAISERROR meghívására szolgáló metódus megszűnt, és el lett távolítva az Azure SQL felügyelt példányain.



**Ajánlás**   
Írja át az utasítást az aktuális RAISERROR szintaxissal, vagy értékelje ki, hogy a modern megközelítés `BEGIN TRY { }  END TRY BEGIN CATCH {  THROW; } END CATCH` megvalósítható-e.

További információ: [megszűnt adatbázismotor-funkció SQL Server](/previous-versions/sql/2014/database-engine/discontinued-database-engine-functionality-in-sql-server-2016#Denali)

## <a name="service-broker"></a>Service Broker<a id="ServiceBrokerWithNonLocalAddress"></a>

**Title: Service Broker funkció részlegesen támogatott az Azure SQL felügyelt példányában.**   
**Kategória**: probléma   

**Leírás**   
A SQL Server Service Broker natív támogatást biztosít az üzenetkezelési és a üzenetsor-kezelési alkalmazások számára az SQL Server adatbázismotor számára. Ez az adatbázis több példányban Service Broker engedélyezve, ami nem támogatott az Azure SQL felügyelt példányain. 


**Ajánlás**   
Az Azure SQL felügyelt példánya nem támogatja a több példányból álló Service Broker használatát, azaz ha a címe nem helyi. Az adatbázis Azure-ba való áttelepítése előtt le kell tiltania Service Broker a következő paranccsal: `ALTER DATABASE [database_name] SET DISABLE_BROKER` ; Emellett előfordulhat, hogy az Service Broker végpontot is el kell távolítania vagy le kell állítania, hogy megakadályozza az üzenetek érkezését az SQL-példányban. Miután az adatbázist áttelepítette az Azure-ba, megtekintheti Azure Service Bus funkciót, hogy Service Broker helyett egy általános, felhőalapú üzenetkezelési rendszert implementáljon. Másik lehetőségként telepítse át SQL Server Azure-beli virtuális gépre. 

További információ: [Service Broker különbségek az Azure SQL felügyelt példányában ](../../managed-instance/transact-sql-tsql-differences-sql-server.md#service-broker)

## <a name="sql-mail"></a>SQL-levelezés<a id="SqlMail"></a>

**Title: az SQL-levelezés megszűnt.**   
**Kategória**: figyelmeztetés   


**Leírás**   
Az SQL-levelezés megszűnt, és el lett távolítva az Azure SQL felügyelt példányain.



**Ajánlás**   
Database Mail használata.

További információ: [megszűnt adatbázismotor-funkció SQL Server](/previous-versions/sql/2014/database-engine/discontinued-database-engine-functionality-in-sql-server-2016#Denali)


## <a name="systemprocedures110"></a>SystemProcedures110<a id="SystemProcedures110"></a>


**Title: az észlelt utasítások, amelyek az Azure SQL felügyelt példányaiban nem elérhető, a rendszer által tárolt eljárásokra hivatkoznak.**   
**Kategória**: figyelmeztetés   

**Leírás**   
A nem támogatott rendszer és a kiterjesztett tárolt eljárások nem használhatók az Azure SQL felügyelt példányaiban –,,,, `sp_dboption` `sp_addserver` `sp_dropalias` `sp_activedirectory_obj` `sp_activedirectory_scp` és `sp_activedirectory_start` . 




**Ajánlás**   
Távolítsa el a nem támogatott rendszereljárásokra mutató hivatkozásokat, amelyek el lettek távolítva az Azure SQL felügyelt példányában.

További információ: [megszűnt adatbázismotor-funkció SQL Server](/previous-versions/sql/2014/database-engine/discontinued-database-engine-functionality-in-sql-server-2016#Denali)

## <a name="transact-sql-job"></a>Transact-SQL-feladatok<a id="TransactSqlJob"></a>

**Title: a TSQL-feladattípus nem támogatott parancsokat tartalmaz az Azure SQL felügyelt példányában**   
**Kategória**: figyelmeztetés   


**Leírás**   
Ez egy olyan lépés, amely ütemezett időpontban futtat TSQL-parancsfájlokat. A TSQL-feladattípus olyan nem támogatott parancsokat tartalmaz, amelyeket az Azure SQL felügyelt példányai nem támogatnak.



**Ajánlás**   
Tekintse át a Azure Migrate érintett objektumok szakaszát az összes olyan feladat megjelenítéséhez, amely nem támogatott parancsokat tartalmaz az Azure SQL felügyelt példányain, és értékelje ki, hogy a feladat lépését vagy az érintett objektumot el lehet-e távolítani. Másik lehetőségként telepítse át SQL Server Azure-beli virtuális gépre.

További információ: [SQL Server Agent különbségek az Azure SQL felügyelt példányában ](../../managed-instance/transact-sql-tsql-differences-sql-server.md#sql-server-agent)


## <a name="trace-flags"></a>Nyomkövetési jelzők<a id="TraceFlags"></a>

**Title: az Azure SQL felügyelt példányain nem támogatott nyomkövetési jelzők találhatók**   
**Kategória**: figyelmeztetés   


**Leírás**   
Az Azure SQL felügyelt példánya csak korlátozott számú globális nyomkövetési jelzőt támogat. A munkamenet nyomkövetési jelzői nem támogatottak.



**Ajánlás**   
Tekintse át a Azure Migrate érintett objektumok szakaszát, és tekintse meg az Azure SQL felügyelt példányain nem támogatott nyomkövetési jelzőket, és értékelje ki, hogy el lehet-e távolítani. Másik lehetőségként telepítse át SQL Server Azure-beli virtuális gépre.

További információ: [nyomkövetési jelzők](/sql/t-sql/database-console-commands/dbcc-traceon-trace-flags-transact-sql#trace-flags)


## <a name="windows-authentication"></a>Windows-hitelesítés<a id="WindowsAuthentication"></a>

**Title: az Azure SQL felügyelt példányain nem támogatottak a Windows-hitelesítéssel (integrált biztonsággal) leképezett adatbázis-felhasználók**   
**Kategória**: figyelmeztetés   


**Leírás**   
Az Azure SQL felügyelt példánya két hitelesítési típust támogat: 
- A felhasználónevet és jelszót használó SQL-hitelesítés
- Az Azure Active Directory-alapú hitelesítést, amely az Azure Active Directory által felügyelt identitásokat használ, és a felügyelt és integrált tartományok is támogatják. 

Az Azure SQL felügyelt példányain nem támogatottak a Windows-hitelesítéssel (integrált biztonsággal) leképezett adatbázis-felhasználók. 


**Ajánlás**   
A helyi Active Directory összevonása Azure Active Directory. Ezután a Windows-identitás helyettesíthető az egyenértékű Azure Active Directory identitásokkal. Másik lehetőségként telepítse át SQL Server Azure-beli virtuális gépre.

További információ: az [SQL felügyelt példányának biztonsági képességei](../../database/security-overview.md#authentication)


## <a name="xp_cmdshell"></a>XP_cmdshell<a id="XpCmdshell"></a>

**Title: a xp_cmdshell az Azure SQL felügyelt példányain nem támogatott.**   
**Kategória**: probléma   


**Leírás**   
Xp_cmdshell, amely a Windows parancs-rendszerhéjat, és egy karakterláncot ad át a végrehajtáshoz, az Azure SQL felügyelt példányaiban nem támogatott. 



**Ajánlás**   
Tekintse át a Azure Migrate érintett objektumok szakaszát, hogy megjelenjenek a xp_cmdshell használó összes objektum, és értékelje ki, hogy az xp_cmdshellre vagy a befolyásolt objektumra mutató hivatkozás el lehet-e távolítani. Vegye fontolóra Azure Automation, amely felhőalapú automatizálási és konfigurációs szolgáltatást nyújt. Másik lehetőségként telepítse át SQL Server Azure-beli virtuális gépre.

További információ: [tárolt eljárásbeli különbségek az Azure SQL felügyelt példányain](../../managed-instance/transact-sql-tsql-differences-sql-server.md#stored-procedures-functions-and-triggers)

## <a name="next-steps"></a>Következő lépések

Az SQL Server Azure SQL felügyelt példányra való áttelepítésének megkezdéséhez tekintse meg a [SQL Server az SQL felügyelt példány áttelepítési Útmutatóját](sql-server-to-managed-instance-guide.md).

- A Microsoft és a harmadik féltől származó szolgáltatások és eszközök egyik mátrixa, amely a különböző adatbázis-és adatáttelepítési forgatókönyvek, valamint a speciális feladatok elvégzéséhez nyújt segítséget, lásd: [szolgáltatás és eszközök az adatok áttelepítéséhez](../../../dms/dms-tools-matrix.md).

- Az Azure SQL felügyelt példányával kapcsolatos további tudnivalókért tekintse meg a következőt:
   - [Szolgáltatási szintek az Azure SQL felügyelt példányában](../../managed-instance/sql-managed-instance-paas-overview.md#service-tiers)
   - [SQL Server és az Azure SQL felügyelt példányai közötti különbségek](../../managed-instance/transact-sql-tsql-differences-sql-server.md)
   - [Az Azure teljes tulajdonlási költsége kalkulátor](https://azure.microsoft.com/pricing/tco/calculator/) 


- Ha többet szeretne megtudni a Felhőbeli Migrálás keretrendszerével és bevezetési ciklusával kapcsolatban, olvassa el a következőt:
   -  [Felhőbevezetési keretrendszer az Azure-hoz](/azure/cloud-adoption-framework/migrate/azure-best-practices/contoso-migration-scale)
   -  [Ajánlott eljárások az Azure-ra való áttéréssel kapcsolatos díjszabási és méretezési feladatok elvégzéséhez](/azure/cloud-adoption-framework/migrate/azure-best-practices/migrate-best-practices-costs) 

- Az alkalmazás-hozzáférési réteg értékeléséhez lásd: [adathozzáférés áttelepítési eszközkészlete (előzetes verzió)](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit)
- Az A/B típusú adatelérési réteg végrehajtásával kapcsolatos további információkért lásd: [Database Experimentation Assistant](/sql/dea/database-experimentation-assistant-overview).