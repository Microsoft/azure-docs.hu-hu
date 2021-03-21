---
title: A T-SQL-különbségek feloldása – áttelepítés
description: A Azure SQL Databaseban teljes mértékben támogatott Transact-SQL-utasítások.
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: reference
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 12/03/2018
ms.openlocfilehash: 1e286b2329cb98d580bbf64071ff8767db304a00
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96461886"
---
# <a name="resolving-transact-sql-differences-during-migration-to-sql-database"></a>Transact-SQL különbségek feloldása az áttelepítés során SQL Database

Az adatbázis SQL Serverról Azure SQL Databaseba való [áttelepítésekor](migrate-to-database-from-sql-server.md) előfordulhat, hogy a SQL Server-adatbázis áttelepítése előtt némi újratervezésre van szükség. Ez a cikk útmutatást nyújt az újratervezés végrehajtásához és a szükséges tervezési okok megismeréséhez. Az inkompatibilitás észleléséhez használja a [Data Migration Assistant (DMA)](https://www.microsoft.com/download/details.aspx?id=53595).

## <a name="overview"></a>Áttekintés

Az alkalmazások által használt legtöbb Transact-SQL-szolgáltatás teljes mértékben támogatott Microsoft SQL Server és Azure SQL Database egyaránt. Például az alapvető SQL-összetevők, például az adattípusok, a kezelők, a karakterláncok, a aritmetikai, a logikai és a kurzor függvények azonos módon működnek SQL Server és SQL Database. Van azonban néhány, a DDL (adatdefiníciós nyelv) és a DML (adatmanipulációs nyelv) elemeinek néhány T-SQL-eltérése, ami csak részben támogatott T-SQL-utasításokat és-lekérdezéseket eredményez (amit a cikk későbbi részében tárgyalunk).

Emellett vannak olyan szolgáltatások és szintaxisok is, amelyek egyáltalán nem támogatottak, mert Azure SQL Database úgy lett kialakítva, hogy elkülönítse a szolgáltatásokat a főadatbázis és az operációs rendszer függőségeitől. Ennek megfelelően a legtöbb kiszolgálói szintű tevékenység nem megfelelő a SQL Database számára. A T-SQL-utasítások és-beállítások nem érhetők el, ha a kiszolgálói szintű beállításokat, az operációs rendszer összetevőit vagy a fájlrendszer konfigurációját konfigurálja. Ha ilyen képességekre van szükség, a megfelelő alternatíva gyakran elérhető más módon, SQL Database vagy egy másik Azure-szolgáltatásból vagy-szolgáltatásból.

A magas rendelkezésre állás például az [Always On rendelkezésre állási csoportokhoz](/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server)hasonló technológiával építhető Azure SQL Databaseba. A rendelkezésre állási csoportokkal kapcsolatos T-SQL-utasítások SQL Database nem támogatottak, és az Always On rendelkezésre állási csoportokkal kapcsolatos dinamikus felügyeleti nézetek szintén nem támogatottak.

A SQL Database által támogatott és nem támogatott szolgáltatások listáját lásd: [Azure SQL Database szolgáltatások összehasonlítása](features-comparison.md). Az ezen az oldalon található lista kiegészíti az irányelvek és szolgáltatások című cikket, és a Transact-SQL-utasításokra koncentrál.

## <a name="transact-sql-syntax-statements-with-partial-differences"></a>Transact-SQL szintaxisi utasítások részleges eltérésekkel

Az alapszintű DDL (adatdefiníciós nyelv) utasítások érhetők el, de néhány DDL-utasításhoz a lemez elhelyezésével és a nem támogatott funkciókkal kapcsolatos kiterjesztések tartoznak.

- Az adatbázis-utasítások létrehozása és módosítása több mint három tucat lehetőséget tartalmaz. Az utasítások közé tartoznak a fájlok elhelyezése, a FILESTREAM és a Service Broker olyan beállításai, amelyek csak SQL Serverra vonatkoznak. Ez nem számít, ha adatbázisokat hoz létre az áttelepítés előtt, de ha olyan T-SQL-kódot telepít át, amely adatbázisokat hoz létre, össze kell hasonlítani a [Create Database (Azure SQL Database)](/sql/t-sql/statements/create-database-transact-sql) parancsot a [create Database (SQL Server Transact-SQL)](/sql/t-sql/statements/create-database-transact-sql) SQL Server szintaxissal, hogy az összes használt lehetőség támogatott legyen. A Azure SQL Database ADATBÁZISának létrehozása olyan szolgáltatás-és rugalmas méretezési lehetőségekkel is rendelkezik, amelyek csak a SQL Databasera vonatkoznak.
- A CREATE és ALTER TABLE utasítások nem használhatók SQL Database, mert a FILESTREAM nem támogatott.
- A bejelentkezési utasítások létrehozása és módosítása támogatott, de SQL Database nem biztosít minden lehetőséget. Az adatbázis hordozhatóvé tételéhez SQL Database a bejelentkezett adatbázis felhasználóinak használatát javasolja a bejelentkezések helyett, amikor csak lehetséges. További információt a bejelentkezések [létrehozása/módosítása](/sql/t-sql/statements/alter-login-transact-sql) és a [bejelentkezések és felhasználók kezelése](logins-create-manage.md)című témakörben talál.

## <a name="transact-sql-syntax-not-supported-in-azure-sql-database"></a>A Transact-SQL szintaxisa nem támogatott Azure SQL Database

A [Azure SQL Database funkció-összehasonlításban](features-comparison.md)ismertetett nem támogatott funkciókhoz kapcsolódó Transact-SQL-utasítások mellett a következő utasítások és csoportok nem támogatottak. Ennek megfelelően, ha az áttelepíteni kívánt adatbázis a következő funkciók bármelyikét használja, a T-SQL-T a t-SQL-ben a T-SQL funkcióinak és utasításának eltávolításához.

- Rendszerobjektumok rendezése
- Kapcsolattal kapcsolatos: végponti utasítások. A SQL Database nem támogatja a Windows-hitelesítést, de támogatja a hasonló Azure Active Directory-hitelesítést. Bizonyos hitelesítési típusokhoz az SSMS legújabb verziója szükséges. További információ: [csatlakozás SQL Database vagy Azure Azure szinapszis Analytics szolgáltatáshoz Azure Active Directory hitelesítéssel](authentication-aad-overview.md).
- Adatbázisközi lekérdezések három vagy négy résznévvel. (A csak olvasható adatbázisközi lekérdezéseket a [rugalmas adatbázis lekérdezése](elastic-query-overview.md) funkció támogatja.)
- Tulajdonjog adatbázisközi láncolása, `TRUSTWORTHY` beállítása
- Az `EXECUTE AS LOGIN` helyett használja az 'EXECUTE AS USER' utasítást.
- A bővíthető kulcskezelés kivételével a titkosítás funkció támogatott
- Esemény: események, esemény-értesítések, lekérdezési értesítések
- Fájl elhelyezése: az adatbázis-fájl elhelyezésével, méretével és a Microsoft Azure által automatikusan felügyelt adatbázis-fájlokkal kapcsolatos szintaxis.
- Magas rendelkezésre állás: a magas rendelkezésre álláshoz kapcsolódó szintaxis, amelyet a Microsoft Azure fiókja kezel. Ide tartozik a biztonsági mentéshez, visszaállításhoz, Always On funkcióhoz, adatbázis-tükrözéshez, naplóküldéshez, helyreállítási módokhoz kapcsolódó szintaxis.
- Napló-olvasó: a napló-olvasóra támaszkodó szintaxis, amely nem érhető el SQL Database: leküldéses replikáció, adatváltozások rögzítése. Az SQL Database a leküldéses replikációs tétel előfizetője lehet.
- Funkciók: `fn_get_sql`, `fn_virtualfilestats`, `fn_virtualservernodes`
- Hardver: a hardverrel kapcsolatos kiszolgáló beállításaival kapcsolatos szintaxis: például memória, munkavégző szálak, CPU-affinitás, nyomkövetési jelzők. Használja helyette a szolgáltatási szintek és a számítási méretek használatát.
- `KILL STATS JOB`
- `OPENQUERY`, `OPENROWSET` , `OPENDATASOURCE` és négy részből álló nevek
- .NET-keretrendszer: CLR-integráció SQL Server
- Szemantikus keresés
- Kiszolgáló hitelesítő adatai: használja helyette az [adatbázis-hatókörű hitelesítő adatokat](/sql/t-sql/statements/create-database-scoped-credential-transact-sql) .
- Kiszolgáló szintű elemek: kiszolgálói szerepkörök `sys.login_token` . `GRANT`a, a `REVOKE` és `DENY` a kiszolgálói szintű engedélyek nem érhetők el, bár néhányat adatbázis-szintű engedélyek váltottak ki. Néhány hasznos kiszolgálószintű dinamikus felügyeleti nézet rendelkezik egyenértékű adatbázisszintű dinamikus felügyeleti nézettel.
- `SET REMOTE_PROC_TRANSACTIONS`
- `SHUTDOWN`
- `sp_addmessage`
- `sp_configure` lehetőségek és `RECONFIGURE`. Egyes lehetőségek elérhetők az [ALTER DATABASE SCOPED CONFIGURATION](/sql/t-sql/statements/alter-database-scoped-configuration-transact-sql) (adatbázis-hatókörű konfiguráció változtatása) utasítással.
- `sp_helpuser`
- `sp_migrate_user_to_contained`
- SQL Server Agent: a SQL Server Agent vagy a MSDB adatbázisra támaszkodó szintaxis: riasztások, operátorok, központi felügyeleti kiszolgálók. Használjon helyette parancsfájlkezelőt, mint például az Azure PowerShell.
- SQL Server naplózás: használjon SQL Database naplózást.
- SQL Server-nyomkövetés
- Nyomkövetési jelzők: egyes nyomkövetési jelző elemek át lettek helyezve a kompatibilitási módokba.
- Transact-SQL-hibakeresés
- Eseményindítók: kiszolgálói hatókörű vagy bejelentkezési eseményindítók
- `USE` utasítás: Ha másik adatbázissal szeretné felváltani az adatbázis-környezetet, új kapcsolódást kell létesítenie az új adatbázissal.

## <a name="full-transact-sql-reference"></a>A Transact-SQL teljes leírása

A Transact-SQL-szintaxisról és használatáról további információk és példák találhatók az SQL Server Online könyvek [A Transact-SQL leírása (adatbázismotor)](/sql/t-sql/language-reference) című részében.

### <a name="about-the-applies-to-tags"></a>Az „Érvényes” címkék

A Transact-SQL-hivatkozás a jelen SQL Server 2008-es verzióra vonatkozó cikkeket tartalmaz. A cikk címe alatt látható egy ikon, amely felsorolja a négy SQL Server platformot, és jelzi az alkalmazhatóságot. A rendelkezésre állási csoportok például az SQL Server 2012-ben jelentek meg. A [rendelkezésre állási csoport létrehozása](/sql/t-sql/statements/create-availability-group-transact-sql) című cikk azt jelzi, hogy az utasítás **SQL Serverra vonatkozik (a 2012-től kezdve)**. Az utasítás nem vonatkozik a SQL Server 2008, SQL Server 2008 R2, a Azure SQL Database, az Azure Azure szinapszis Analytics vagy a Parallel adattárházra.

Bizonyos esetekben a cikk általános tárgya felhasználható egy termékben, de a termékek között kisebb különbségek vannak. A különbségeket a cikk középpontjában kell megadni, ha szükséges. Bizonyos esetekben a cikk általános tárgya felhasználható egy termékben, de a termékek között kisebb különbségek vannak. A különbségeket a cikk középpontjában kell megadni, ha szükséges. Az TRIGGER létrehozása című cikk például elérhető SQL Databaseban. A kiszolgálói szintű eseményindítók esetében azonban a **minden kiszolgáló** beállítás azt jelzi, hogy a kiszolgálói szintű eseményindítók nem használhatók SQL Databaseban. Használjon inkább adatbázis-szintű eseményindítókat.

## <a name="next-steps"></a>Következő lépések

A SQL Database által támogatott és nem támogatott szolgáltatások listáját lásd: [Azure SQL Database szolgáltatások összehasonlítása](features-comparison.md). Az ezen az oldalon található lista kiegészíti az irányelvek és szolgáltatások című cikket, és a Transact-SQL-utasításokra koncentrál.