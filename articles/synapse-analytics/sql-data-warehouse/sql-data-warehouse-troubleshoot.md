---
title: Dedikált SQL-készlet (korábban SQL DW) hibaelhárítása
description: Dedikált SQL-készlet (korábban SQL DW) hibaelhárítása a Azure Synapse Analytics.
services: synapse-analytics
author: julieMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 11/13/2020
ms.author: jrasnick
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: af653585ec1b57b5fd697dc755e495a96e04e677
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107565406"
---
# <a name="troubleshooting-dedicated-sql-pool-formerly-sql-dw-in-azure-synapse-analytics"></a>Dedikált SQL-készlet (korábban SQL DW) hibaelhárítása a Azure Synapse Analytics

Ez a cikk a dedikált SQL-készlet (korábban SQL DW) gyakori hibaelhárítási problémáit sorolja fel a Azure Synapse Analytics.

## <a name="connecting"></a>Csatlakozó

| Probléma                                                        | Feloldás                                                   |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| Az „NT AUTHORITY\ANONYMOUS LOGON” felhasználó bejelentkezése nem sikerült. (Microsoft SQL Server, Hiba: 18456) | Ez a hiba akkor fordul elő, ha egy Azure AD-felhasználó megpróbál csatlakozni a master adatbázishoz, de nem rendelkezik felhasználóval a master adatbázisban.  A probléma megoldásához adja meg azt a dedikált SQL-készletet (korábbi nevén SQL DW-t), amelyhez a kapcsolódáskor csatlakozni szeretne, vagy adja hozzá a felhasználót a master adatbázishoz.  További [részleteket a](sql-data-warehouse-overview-manage-security.md) Biztonsági áttekintés cikkben talál. |
| A „MyUserName” kiszolgálói tag a jelenlegi biztonsági környezetben nem tud hozzáférni a „master” adatbázishoz. Nem lehet megnyitni a felhasználói alapértelmezett adatbázist. A bejelentkezés sikertelen volt. A következő felhasználó bejelentkezése nem sikerült: „MyUserName”. (Microsoft SQL Server, Hiba: 916) | Ez a hiba akkor fordul elő, ha egy Azure AD-felhasználó megpróbál csatlakozni a master adatbázishoz, de nem rendelkezik felhasználóval a master adatbázisban.  A probléma megoldásához adja meg azt a dedikált SQL-készletet (korábbi nevén SQL DW-t), amelyhez a kapcsolódáskor csatlakozni szeretne, vagy adja hozzá a felhasználót a master adatbázishoz.  További [részleteket a](sql-data-warehouse-overview-manage-security.md) Biztonsági áttekintés cikkben talál. |
| CTAIP-hiba                                                  | Ez a hiba akkor fordulhat elő, ha a főadatbázisban létrehozott SQL Database, de nem az adott SQL-adatbázisban.  Ha ezzel a hibával találkozik, nézze meg a Biztonság áttekintése [cikket.](sql-data-warehouse-overview-manage-security.md)  Ez a cikk bemutatja, hogyan hozhat létre bejelentkezést és felhasználót a master adatbázisban, és hogyan hozhat létre felhasználót egy SQL-adatbázisban. |
| A tűzfal blokkolja                                          | A dedikált SQL-készletet (korábban SQL DW) tűzfalak védik, hogy csak az ismert IP-címek férnek hozzá az adatbázishoz. A tűzfalak alapértelmezés szerint biztonságosak, ami azt jelenti, hogy a csatlakozás előtt explicit módon engedélyeznie kell az IP-címet vagy a címtartományt.  A tűzfal hozzáférésre való konfiguráláshoz kövesse a Kiépítési utasítások Kiszolgálói tűzfal-hozzáférés konfigurálása az ügyfél [IP-címéhez](create-data-warehouse-portal.md) [lépéseit.](create-data-warehouse-portal.md) |
| Nem lehet csatlakozni az eszközzel vagy illesztővel                           | A dedikált SQL-készlet (korábban SQL DW) az [SSMS,](/sql/ssms/download-sql-server-management-studio-ssms?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) [az SSDT](sql-data-warehouse-install-visual-studio.md)és Visual Studio, vagy az [sqlcmd](sql-data-warehouse-get-started-connect-sqlcmd.md) használatát javasolja az adatok lekérdezéséhez. További információ az illesztőprogramokkal és a Azure Synapse való csatlakozásról: Illesztőprogramok a Azure Synapse [és](sql-data-warehouse-connection-strings.md) Csatlakozás Azure Synapse [cikkekhez.](sql-data-warehouse-connect-overview.md) |

## <a name="tools"></a>Eszközök

| Probléma                                                        | Feloldás                                                   |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| Visual Studio az Object Explorerből hiányoznak Azure AD-felhasználók           | Ez egy ismert probléma.  Áthidaló megoldásként tekintse meg a felhasználókat a [sys.database_principals.](/sql/relational-databases/system-catalog-views/sys-database-principals-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)  További [információ a Azure Synapse](sql-data-warehouse-authentication.md) SQL Azure Active Directory készletekkel (korábban SQL DW) való használatával kapcsolatban: Authentication to Azure Synapse (Hitelesítés az adatbázishoz). |
| A manuális parancsprogramozás, a parancsfájl-kezelési varázsló használata vagy az SSMS-en keresztüli csatlakozás lassú, nem válaszol vagy hibákat ad vissza | Győződjön meg arról, hogy a felhasználók létrejöttek a master adatbázisban. A parancsprogram-beállítások között győződjön meg arról, hogy a motor kiadása "Microsoft Azure Synapse Analytics Edition" (kiadás) , a motor típusa pedig "Microsoft Azure SQL Database". |
| A szkriptek létrehozása meghiúsul az SSMS-ban                               | A dedikált SQL-készlethez (korábban SQL DW) való szkript létrehozása meghiúsul, ha a "Szkript létrehozása függő objektumokhoz" beállítás "True" (Igaz) beállításra van állítva. Áthidaló megoldásként a felhasználóknak manuálisan kell a **Tools -> Options ->SQL Server Object Explorer -> Generate script for dependent options** (Parancsfájl létrehozása függő beállításokhoz) lehetőséghez, és false (hamis) értéket kell megadniuk |

## <a name="data-ingestion-and-preparation"></a>Adatfeldolgozás és -előkészítés

| Probléma                                                        | Feloldás                                                   |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| Az üres sztringek CETAS használatával történő exportálása NULL értékeket eredményez a Parquet- és ORC-fájlokban. Vegye figyelembe, hogy ha ÜRES sztringeket exportál a NOT NULL korlátozású oszlopokból, a CETAS elutasított rekordokat eredményez, és az exportálás meghiúsulhat. | Távolítsa el az üres sztringeket vagy a sértő oszlopot a CETAS SELECT utasításában. |
| A 0 és 127 tartományon kívüli érték a Parquet- és ORC-fájlformátumok tinyint oszlopába való betöltése nem támogatott. | Adjon meg egy nagyobb adattípust a céloszlophoz.           |

## <a name="performance"></a>Teljesítmény

| Probléma                                                        | Feloldás                                                   |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| Lekérdezési teljesítmény hibaelhárítása                            | Ha egy adott lekérdezés hibaelhárításával próbálkozik, kezdje a [lekérdezések figyelésének elsajátításával.](sql-data-warehouse-manage-monitor.md#monitor-query-execution) |
| TempDB-területekkel kapcsolatos problémák | [A TempDB lemezterület-használatának](sql-data-warehouse-manage-monitor.md#monitor-tempdb) figyelése.  A TempDB-tárhelyből való el fogyó gyakori okok a következőek:<br>– Nincs elég lefoglalt erőforrás a lekérdezéshez, ezért az adatok túlcsomódnak a TempDB-be.  Lásd: Számítási [feladatok kezelése](resource-classes-for-workload-management.md) <br>– A statisztikák hiányoznak vagy elavultak, ami túlzott adatmozgást okoz.  A [statisztikák létrehozásáról a Táblastatisztikák](sql-data-warehouse-tables-statistics.md) karbantartása oldalon talál további információt<br>– A TempDB-terület szolgáltatásszintenként van lefoglalva.  [A dedikált SQL-készlet (korábban SQL DW)](sql-data-warehouse-manage-compute-overview.md#scaling-compute) magasabb DWU-beállításra való skálázása több TempDB-helyet foglal le.|
| A gyenge lekérdezési teljesítmény és tervek gyakran a hiányzó statisztikák miatt fordulnak elő | A gyenge teljesítmény leggyakoribb oka a táblák statisztikáinak hiánya.  A [statisztikák létrehozásáról](sql-data-warehouse-tables-statistics.md) és a teljesítmény szempontjából kritikus okukról a Táblastatisztikák fenntartása oldalon talál további információt. |
| Alacsony egyidejűség/várólistán lévő lekérdezések                             | A [számítási feladatok felügyeletének](resource-classes-for-workload-management.md) megértése azért fontos, hogy megértsük, hogyan lehet egyensúlyba hozni a memóriakiosztást az egyidejűség között. |
| Ajánlott eljárások megvalósítása                              | A legjobb hely a lekérdezési teljesítmény javítására szolgáló módszerek elsajátítása a dedikált [SQL-készlet (korábban SQL DW) ajánlott eljárásait beíró](sql-data-warehouse-best-practices.md) cikk. |
| A teljesítmény javítása méretezéssel                      | Néha a teljesítmény javítására szolgáló megoldás az, ha egyszerűen további számítási teljesítményt ad hozzá a lekérdezésekhez a dedikált [SQL-készlet (korábban SQL DW) skálázása segítségével.](sql-data-warehouse-manage-compute-overview.md) |
| Gyenge lekérdezési teljesítmény az index gyenge minősége miatt     | Bizonyos esetekben a lekérdezések a gyenge oszlopcentrikus [indexminőség miatt lelassulnak.](sql-data-warehouse-tables-index.md#causes-of-poor-columnstore-index-quality)  A szegmensminőség javítása érdekében tekintse meg ezt a cikket, és tekintse meg az [indexek újraépítését.](sql-data-warehouse-tables-index.md#rebuilding-indexes-to-improve-segment-quality) |

## <a name="system-management"></a>Rendszerkezelés

| Probléma                                                        | Feloldás                                                   |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| Msg 40847: Nem lehetett végrehajtani a műveletet, mert a kiszolgáló túllépné az adatbázis-tranzakciós egység engedélyezett 45000-es kvótáját. | Csökkentse a létrehozni próbált adatbázis [DWU-ját,](what-is-a-data-warehouse-unit-dwu-cdwu.md) vagy kérjen [kvótaemelést.](sql-data-warehouse-get-started-create-support-ticket.md) |
| A terület kihasználtságának vizsgálata                              | A [rendszer](sql-data-warehouse-tables-overview.md#table-size-queries) helykihasználtságának ért lásd: Táblaméretek. |
| Segítség a táblák kezeléséhez                                    | A táblák [kezelésével kapcsolatos](sql-data-warehouse-tables-overview.md) segítségért tekintse meg a Táblák áttekintése cikket.  Ez a cikk olyan részletesebb témakörökre mutató hivatkozásokat is tartalmaz, mint [a](sql-data-warehouse-tables-distribute.md)Tábla adattípusok, a Tábla terjesztése, [a](sql-data-warehouse-tables-index.md)tábla indexelése, a tábla particionálása, [a](sql-data-warehouse-tables-partition.md)táblastatisztikák és az [ideiglenes táblák karbantartása.](sql-data-warehouse-tables-temporary.md) [](sql-data-warehouse-tables-data-types.md) [](sql-data-warehouse-tables-statistics.md) |
| A transzparens adattitkosítás (TDE) folyamatjelző sávja nem frissül a Azure Portal | A TDE állapotát a [PowerShell](/powershell/module/az.sql/get-azsqldatabasetransparentdataencryption?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)használatával tudja megtekinteni. |

## <a name="differences-from-sql-database"></a>Eltérések a SQL Database

| Probléma                                 | Feloldás                                                   |
| :------------------------------------ | :----------------------------------------------------------- |
| Nem támogatott SQL Database funkciók     | Lásd: [Nem támogatott tábla funkciói.](sql-data-warehouse-tables-overview.md#unsupported-table-features) |
| Nem támogatott SQL Database adattípusok   | Lásd: [Nem támogatott adattípusok.](sql-data-warehouse-tables-data-types.md#identify-unsupported-data-types)        |
| Tárolt eljárások korlátozásai          | A [tárolt eljárások egyes](sql-data-warehouse-develop-stored-procedures.md#limitations) korlátozásaiért tekintse meg a tárolt eljárásokra vonatkozó korlátozásokat. |
| Az UDF-ek nem támogatják a SELECT utasításokat | Ez azudF-ek aktuális korlátozása.  A [támogatott szintaxist lásd: CREATE FUNCTION.](/sql/t-sql/statements/create-function-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) |
| sp_rename (előzetes verzió) az oszlopokhoz nem működik a dbo-n kívüli *sémákban* | Ez a Synapse-sp_rename [(előzetes verzió) aktuális korlátozása az oszlopokra.](/sql/relational-databases/system-stored-procedures/sp-rename-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)  A *dbo-séma* részét nem tartalmazó objektumok oszlopai egy CTAS-n keresztül nevezhetők át egy új táblára. |

## <a name="next-steps"></a>Következő lépések

Ha további segítségre van szüksége a probléma megoldásához, íme néhány további erőforrás, amit kipróbálhat.

* [Blogok](https://azure.microsoft.com/blog/tag/azure-sql-data-warehouse/)
* [Funkciókérések](https://feedback.azure.com/forums/307516-sql-data-warehouse)
* [Videók](https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse)
* [Támogatási jegy létrehozása](sql-data-warehouse-get-started-create-support-ticket.md)
* [Microsoft Q&A kérdésoldal](/answers/topics/azure-synapse-analytics.html)
* [Stack Overflow fórum](https://stackoverflow.com/questions/tagged/azure-sqldw)
* [Twitter](https://twitter.com/hashtag/SQLDW)