---
title: Első lépések az időbeli táblázatokkal
description: Ismerje meg, hogyan kezdheti el a Azure SQL Database és az Azure SQL felügyelt példányának időbeli tábláival való használatának első lépéseit.
services: sql-database
ms.service: sql-db-mi
ms.subservice: development
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: how-to
author: bonova
ms.author: bonova
ms.reviewer: sstein
ms.date: 06/26/2019
ms.openlocfilehash: ea037d12417c8fad9d80b77df69285ed2c8df31b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "91618658"
---
# <a name="getting-started-with-temporal-tables-in-azure-sql-database-and-azure-sql-managed-instance"></a>Első lépések a Azure SQL Database és az Azure SQL felügyelt példányainak időbeli tábláival
[!INCLUDE[appliesto-sqldb-sqlmi](includes/appliesto-sqldb-sqlmi.md)]

Az időbeli táblázatok a Azure SQL Database és az Azure SQL felügyelt példányának programozható funkciója, amely lehetővé teszi az adatváltozások teljes előzményeinek nyomon követését és elemzését anélkül, hogy egyéni kódolásra lenne szükség. Az ideiglenes táblák az időkörnyezettel szoros kapcsolatban tartanak adatokat, így a tárolt tények csak a megadott időszakon belül érvényesek. Az időbeli táblázatok ezen tulajdonsága lehetővé teszi a hatékony időalapú elemzést, és az adatok evolúciójának beolvasását.

## <a name="temporal-scenario"></a>Időbeli forgatókönyv

Ez a cikk bemutatja az időbeli táblázatok alkalmazási forgatókönyvekben való felhasználásának lépéseit. Tegyük fel, hogy nyomon szeretné követni a felhasználói tevékenységet egy olyan új webhelyen, amelyet a rendszer teljesen kifejleszt, vagy egy meglévő webhelyre, amelyet a felhasználói tevékenység elemzésével kíván kiterjeszteni. Ebben az egyszerűsített példában feltételezzük, hogy a meglátogatott weblapok száma egy adott időszakban egy olyan kijelző, amelyet a Azure SQL Database vagy az Azure SQL felügyelt példányán üzemeltetett webhely-adatbázisban kell rögzíteni és figyelni. A felhasználói tevékenység korábbi elemzésének célja, hogy beolvassa a webhelyre való áttervezéshez szükséges adatokat, és jobb élményt nyújtson a látogatóknak.

Az ehhez a forgatókönyvhöz tartozó adatbázis-modell nagyon egyszerű – a felhasználói tevékenység mérőszáma egyetlen egész mezőből áll, a **PageVisited**, és a felhasználói profil alapinformációi mellett rögzítve. Emellett az időalapú elemzések esetében az egyes felhasználók sorait is megtarthatja, ahol minden sor az adott időszakon belül egy adott felhasználó által meglátogatott lapok számát jelöli.

![Séma](./media/temporal-tables/AzureTemporal1.png)

Szerencsére nem kell semmilyen erőfeszítést megtenni az alkalmazásban a tevékenység adatainak fenntartásához. Az időbeli táblázatokkal ez a folyamat automatizálható, így teljes rugalmasságot biztosít a webhelyek tervezése során, és több időt is igénybe kell vennie az adatok elemzésére. Az egyetlen teendő, hogy gondoskodjon arról, hogy a **WebSiteInfo** -tábla [időszakos rendszerverzióként](/sql/relational-databases/tables/temporal-tables#what-is-a-system-versioned-temporal-table)legyen konfigurálva. Az időbeli táblázatok ezen forgatókönyvben való felhasználásának pontos lépései alább olvashatók.

## <a name="step-1-configure-tables-as-temporal"></a>1. lépés: a táblák időbeli konfigurálása

Attól függően, hogy új fejlesztést vagy meglévő alkalmazást frissít, ideiglenes attribútumok hozzáadásával létrehozhat ideiglenes táblákat vagy módosíthatja a meglévőket is. Általánosságban elmondható, hogy a forgatókönyv a két lehetőség kombinációja lehet. Hajtsa végre ezeket a műveleteket a [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) (SSMS), a [SQL Server Data Tools](/sql/ssdt/download-sql-server-data-tools-ssdt) (SSDT), a [Azure Data Studio](/sql/azure-data-studio/download-azure-data-studio)vagy bármely más Transact-SQL fejlesztői eszköz használatával.

> [!IMPORTANT]
> Javasoljuk, hogy mindig az Management Studio legújabb verzióját használja, hogy szinkronizálva legyen a Azure SQL Database és az Azure SQL felügyelt példányának frissítéseivel. [Az SQL Server Management Studio frissítése](/sql/ssms/download-sql-server-management-studio-ssms).

### <a name="create-new-table"></a>Új tábla létrehozása

A SSMS Object Explorer helyi menü "új System-Versioned táblázat" parancsával nyissa meg a lekérdezési szerkesztőt egy időbeli táblázat sablonjának használatával, majd használja az "adja meg a sablon paramétereinek értékét" (CTRL + SHIFT + M) a sablon feltöltéséhez:

![SSMSNewTable](./media/temporal-tables/AzureTemporal2.png)

A SSDT elemnél válassza az "ideiglenes tábla (rendszerverzióval ellátott)" sablont, amikor új elemeket ad hozzá az adatbázis-projekthez. Ekkor megnyílik a Table Designer, és lehetővé teszi a táblázat elrendezésének egyszerű megadását:

![SSDTNewTable](./media/temporal-tables/AzureTemporal3.png)

A Transact-SQL-utasítások közvetlen megadásával is létrehozhat ideiglenes táblázatot, ahogy az alábbi példában is látható. Vegye figyelembe, hogy minden időbeli táblázat kötelező elemei az időszak meghatározása és a SYSTEM_VERSIONING záradék, amely egy másik felhasználói táblára hivatkozik, amely a korábbi sorok verzióját fogja tárolni:

```sql
CREATE TABLE WebsiteUserInfo
(  
    [UserID] int NOT NULL PRIMARY KEY CLUSTERED
  , [UserName] nvarchar(100) NOT NULL
  , [PagesVisited] int NOT NULL
  , [ValidFrom] datetime2 (0) GENERATED ALWAYS AS ROW START
  , [ValidTo] datetime2 (0) GENERATED ALWAYS AS ROW END
  , PERIOD FOR SYSTEM_TIME (ValidFrom, ValidTo)
 )  
 WITH (SYSTEM_VERSIONING = ON (HISTORY_TABLE = dbo.WebsiteUserInfoHistory));
```

Rendszerverzióval ellátott ideiglenes tábla létrehozásakor a rendszer automatikusan létrehozza az alapértelmezett konfigurációval rendelkező kapcsolódó előzmények táblát. Az alapértelmezett előzmények táblázat az időszak oszlopainak (Befejezés, indítás) fürtözött B-fa indexét tartalmazza, és az oldal tömörítése engedélyezve van. Ez a konfiguráció optimális az időbeli táblákat használó forgatókönyvek többsége számára, különösen az [adatok naplózása](/sql/relational-databases/tables/temporal-table-usage-scenarios#enabling-system-versioning-on-a-new-table-for-data-audit)esetén.

Ebben a konkrét esetben arra törekszünk, hogy az időalapú trendeket elemezzük hosszabb adatelőzményekkel és nagyobb adatkészletekkel, így az előzményeket tartalmazó tábla számára a fürtözött oszlopcentrikus index. A fürtözött oszlopcentrikus nagyon jó tömörítést és teljesítményt biztosít az analitikus lekérdezésekhez. Az időbeli táblázatok lehetővé teszi, hogy rugalmasan konfigurálja az indexeket az aktuális és az időszakos táblákon egymástól függetlenül.

> [!NOTE]
> A oszlopcentrikus indexek a üzletileg kritikus, a általános célú és a prémium szinteken, valamint a standard szintű, S3 és újabb verziókon érhetők el.

A következő parancsfájl azt mutatja be, hogy a History (előzmények) tábla alapértelmezett indexe hogyan változtatható meg a fürtözött oszlopcentrikus:

```sql
CREATE CLUSTERED COLUMNSTORE INDEX IX_WebsiteUserInfoHistory
ON dbo.WebsiteUserInfoHistory
WITH (DROP_EXISTING = ON);
```

Az ideiglenes táblák a Object Explorerban jelennek meg a könnyebb azonosításhoz megadott ikonnal, míg az előzmények táblázata alárendelt csomópontként jelenik meg.

![AlterTable](./media/temporal-tables/AzureTemporal4.png)

### <a name="alter-existing-table-to-temporal"></a>Meglévő táblázat módosítása időbeli értékre

Fedjük le azt az alternatív forgatókönyvet, amelyben a WebsiteUserInfo tábla már létezik, de nem a módosítások előzményeinek megtartására lett tervezve. Ebben az esetben egyszerűen kiterjesztheti a meglévő táblázatot, hogy ideiglenes legyen, ahogy az az alábbi példában is látható:

```sql
ALTER TABLE WebsiteUserInfo
ADD
    ValidFrom datetime2 (0) GENERATED ALWAYS AS ROW START HIDDEN  
        constraint DF_ValidFrom DEFAULT DATEADD(SECOND, -1, SYSUTCDATETIME())
    , ValidTo datetime2 (0)  GENERATED ALWAYS AS ROW END HIDDEN
        constraint DF_ValidTo DEFAULT '9999.12.31 23:59:59.99'
    , PERIOD FOR SYSTEM_TIME (ValidFrom, ValidTo);

ALTER TABLE WebsiteUserInfo  
SET (SYSTEM_VERSIONING = ON (HISTORY_TABLE = dbo.WebsiteUserInfoHistory));
GO

CREATE CLUSTERED COLUMNSTORE INDEX IX_WebsiteUserInfoHistory
ON dbo.WebsiteUserInfoHistory
WITH (DROP_EXISTING = ON);
```

## <a name="step-2-run-your-workload-regularly"></a>2. lépés: a számítási feladatok rendszeres futtatása

Az időbeli táblázatok legfőbb előnye, hogy a változások követéséhez semmilyen módon nem kell módosítania vagy módosítania a webhelyet. A létrehozást követően az ideiglenes táblák transzparens módon megőrzik az előző sorokat, valahányszor módosításokat végez az adatokon.

Az adott forgatókönyv automatikus változás-követésének kihasználása érdekében csak az oszlop **PagesVisited** frissítése minden alkalommal, amikor egy felhasználó befejezi a munkamenetét a webhelyen:

```sql
UPDATE WebsiteUserInfo  SET [PagesVisited] = 5
WHERE [UserID] = 1;
```

Fontos megjegyezni, hogy a frissítési lekérdezésnek nem kell tudnia a tényleges művelet bekövetkezett időpontját, illetve azt, hogy a későbbi elemzések során a korábbi adatok hogyan őrződnek meg. A Azure SQL Database és az Azure SQL felügyelt példánya egyaránt automatikusan kezeli mindkét szempontot. Az alábbi ábra azt szemlélteti, hogyan jönnek létre az előzmények az összes frissítésen.

![TemporalArchitecture](./media/temporal-tables/AzureTemporal5.png)

## <a name="step-3-perform-historical-data-analysis"></a>3. lépés: a korábbi adatok elemzésének végrehajtása

Most, hogy az időbeli rendszerverziószámozás engedélyezve van, a korábbi adatok elemzése csak egy lekérdezéssel érhető el. Ebben a cikkben néhány példát ismertetünk, amelyek közös elemzési forgatókönyvekkel foglalkoznak – az összes adat megismerése érdekében Ismerkedjen meg a [FOR SYSTEM_TIME](/sql/relational-databases/tables/temporal-tables#how-do-i-query-temporal-data) záradékban bemutatott különböző lehetőségekkel.

Ha szeretné megtekinteni az első 10 felhasználót a meglátogatott weblapok száma alapján egy órával ezelőtt, futtassa ezt a lekérdezést:

```sql
DECLARE @hourAgo datetime2 = DATEADD(HOUR, -1, SYSUTCDATETIME());
SELECT TOP 10 * FROM dbo.WebsiteUserInfo FOR SYSTEM_TIME AS OF @hourAgo
ORDER BY PagesVisited DESC
```

A lekérdezés egyszerűen módosítható, hogy elemezze a webhely látogatásait egy nappal ezelőtt, egy hónappal ezelőtt vagy a múltban egy adott időpontban.

Az előző nap alapvető statisztikai elemzésének végrehajtásához használja a következő példát:

```sql
DECLARE @twoDaysAgo datetime2 = DATEADD(DAY, -2, SYSUTCDATETIME());
DECLARE @aDayAgo datetime2 = DATEADD(DAY, -1, SYSUTCDATETIME());

SELECT UserID, SUM (PagesVisited) as TotalVisitedPages, AVG (PagesVisited) as AverageVisitedPages,
MAX (PagesVisited) AS MaxVisitedPages, MIN (PagesVisited) AS MinVisitedPages,
STDEV (PagesVisited) as StDevViistedPages
FROM dbo.WebsiteUserInfo
FOR SYSTEM_TIME BETWEEN @twoDaysAgo AND @aDayAgo
GROUP BY UserId
```

Egy adott felhasználó tevékenységének megkereséséhez egy adott időtartamon belül használja a TARTALMAZt záradékot:

```sql
DECLARE @hourAgo datetime2 = DATEADD(HOUR, -1, SYSUTCDATETIME());
DECLARE @twoHoursAgo datetime2 = DATEADD(HOUR, -2, SYSUTCDATETIME());
SELECT * FROM dbo.WebsiteUserInfo
FOR SYSTEM_TIME CONTAINED IN (@twoHoursAgo, @hourAgo)
WHERE [UserID] = 1;
```

A grafikai vizualizáció különösen ideális az időbeli lekérdezésekhez, mivel a trendeket és a használati mintákat intuitív módon, nagyon egyszerűen megjelenítheti:

![TemporalGraph](./media/temporal-tables/AzureTemporal6.png)

## <a name="evolving-table-schema"></a>Változó táblázat sémája

Az alkalmazások fejlesztése során általában módosítania kell az időbeli táblázat sémáját. Ehhez egyszerűen futtassa az ALTER TABLE utasításait, és Azure SQL Database vagy az Azure SQL felügyelt példánya megfelelően propagálja a módosításokat az előzmények táblába. Az alábbi szkript bemutatja, hogyan adhat hozzá további attribútumokat a követéshez:

```sql
/*Add new column for tracking source IP address*/
ALTER TABLE dbo.WebsiteUserInfo
ADD  [IPAddress] varchar(128) NOT NULL CONSTRAINT DF_Address DEFAULT 'N/A';
```

Hasonlóképpen módosíthatja az oszlop definícióját is, ha a munkaterhelés aktív:

```sql
/*Increase the length of name column*/
ALTER TABLE dbo.WebsiteUserInfo
    ALTER COLUMN  UserName nvarchar(256) NOT NULL;
```

Végül eltávolíthat egy olyan oszlopot, amelyhez már nincs szüksége.

```sql
/*Drop unnecessary column */
ALTER TABLE dbo.WebsiteUserInfo
    DROP COLUMN TemporaryColumn;
```

Azt is megteheti, hogy a legújabb [SSDT](/sql/ssdt/download-sql-server-data-tools-ssdt) használja az időbeli táblázat sémájának módosítására, miközben az adatbázishoz (online mód) vagy az adatbázis-projekt (offline mód) részeként csatlakozik.

## <a name="controlling-retention-of-historical-data"></a>A korábbi adatok megőrzésének szabályozása

A rendszerverzióval ellátott időszakos táblázatok esetében az előzmények táblázata a normál tábláknál nagyobb méretűre növelheti az adatbázis méretét. A nagy és egyre növekvő History-táblázat a tiszta tárolási költségekkel, valamint az időbeli lekérdezéssel kapcsolatos teljesítménybeli ÁFA kiszabásával jelentheti a problémát. Ezért az előzmények táblában lévő adatok kezelésére szolgáló adatmegőrzési szabályzat fejlesztése fontos szempont az egyes időbeli táblázatok életciklusának megtervezéséhez és kezeléséhez. A Azure SQL Database és az Azure SQL felügyelt példányaival a következő módszerekkel kezelheti az időszakos táblázat korábbi információit:

- [Tábla particionálása](/sql/relational-databases/tables/manage-retention-of-historical-data-in-system-versioned-temporal-tables#using-table-partitioning-approach)
- [Egyéni karbantartási parancsfájl](/sql/relational-databases/tables/manage-retention-of-historical-data-in-system-versioned-temporal-tables#using-custom-cleanup-script-approach)

## <a name="next-steps"></a>Következő lépések

- Az időbeli táblázatokkal kapcsolatos további információkért lásd: [ideiglenes táblák](/sql/relational-databases/tables/temporal-tables)megtekintése.
- Látogasson el a Channel 9 csatornára, hogy meghallgassa az [ügyfél időbeli megvalósításának sikertörténetét](https://channel9.msdn.com/Blogs/jsturtevant/Azure-SQL-Temporal-Tables-with-RockStep-Solutions) , és figyelje az [élő időbeli bemutatót](https://channel9.msdn.com/Shows/Data-Exposed/Temporal-in-SQL-Server-2016).
