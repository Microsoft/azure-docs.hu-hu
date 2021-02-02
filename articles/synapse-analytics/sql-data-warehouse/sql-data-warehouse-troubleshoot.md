---
title: Dedikált SQL-készlet (korábban SQL DW) hibaelhárítása
description: A dedikált SQL-készlet (korábban SQL DW) hibaelhárítása Az Azure szinapszis Analyticsben.
services: synapse-analytics
author: kevinvngo
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 11/13/2020
ms.author: kevin
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: de6c7e47af3019d034d457f43334aad1aeafabdc
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/02/2021
ms.locfileid: "99254274"
---
# <a name="troubleshooting-dedicated-sql-pool-formerly-sql-dw-in-azure-synapse-analytics"></a>Dedikált SQL-készlet (korábban SQL DW) hibaelhárítása Az Azure szinapszis Analyticsben

Ez a cikk az Azure szinapszis Analytics dedikált SQL-készletének (korábban SQL DW) gyakori hibaelhárítási problémáit sorolja fel.

## <a name="connecting"></a>Csatlakozás

| Probléma                                                        | Feloldás                                                   |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| Az „NT AUTHORITY\ANONYMOUS LOGON” felhasználó bejelentkezése nem sikerült. (Microsoft SQL Server, hiba: 18456) | Ez a hiba akkor fordul elő, amikor egy Azure AD-felhasználó megpróbál csatlakozni a Master adatbázishoz, de nem rendelkezik a főkiszolgálóval.  A probléma megoldásához adja meg azt a dedikált SQL-készletet (korábban SQL DW), amelyhez csatlakozni szeretne, vagy adja hozzá a felhasználót a Master adatbázishoz.  További részletekért tekintse meg a [Biztonság áttekintése](sql-data-warehouse-overview-manage-security.md) című cikket. |
| A „MyUserName” kiszolgálói tag a jelenlegi biztonsági környezetben nem tud hozzáférni a „master” adatbázishoz. Nem lehet megnyitni a felhasználói alapértelmezett adatbázist. A bejelentkezés sikertelen volt. A következő felhasználó bejelentkezése nem sikerült: „MyUserName”. (Microsoft SQL Server, hiba: 916) | Ez a hiba akkor fordul elő, amikor egy Azure AD-felhasználó megpróbál csatlakozni a Master adatbázishoz, de nem rendelkezik a főkiszolgálóval.  A probléma megoldásához adja meg azt a dedikált SQL-készletet (korábban SQL DW), amelyhez csatlakozni szeretne, vagy adja hozzá a felhasználót a Master adatbázishoz.  További részletekért tekintse meg a [Biztonság áttekintése](sql-data-warehouse-overview-manage-security.md) című cikket. |
| CTAIP hiba                                                  | Ez a hiba akkor fordulhat elő, ha a SQL Database Master adatbázisban létrehozott egy bejelentkezési azonosítót, de nem az adott SQL-adatbázisban.  Ha ezt a hibát tapasztalja, tekintse meg a [biztonsági áttekintést ismertető](sql-data-warehouse-overview-manage-security.md) cikket.  Ez a cikk bemutatja, hogyan hozhat létre egy felhasználónevet és egy felhasználót a Master adatbázisban, majd hogyan hozhat létre felhasználót egy SQL-adatbázisban. |
| A tűzfal blokkolja                                          | A dedikált SQL-készletet (korábbi nevén SQL DW) tűzfalak védik, hogy csak az ismert IP-címek férhessenek hozzá egy adatbázishoz. Alapértelmezés szerint a tűzfalak biztonságosak, ami azt jelenti, hogy a kapcsolat megkezdése előtt explicit módon engedélyeznie kell és meg kell adnia az IP-címet vagy a címtartományt.  A tűzfal hozzáférésre való konfigurálásához kövesse a [kiszolgáló tűzfal-hozzáférés konfigurálása az ügyfél IP](create-data-warehouse-portal.md) -címére című témakör lépéseit a [létesítési utasításokban](create-data-warehouse-portal.md). |
| Nem lehet kapcsolatot létesíteni az eszközzel vagy az illesztőprogrammal                           | A dedikált SQL-készlet (korábbi nevén SQL DW) a [SSMS](/sql/ssms/download-sql-server-management-studio-ssms?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true), [a SSDT for Visual Studio](sql-data-warehouse-install-visual-studio.md)vagy a [Sqlcmd](sql-data-warehouse-get-started-connect-sqlcmd.md) használatát javasolja az adatai lekérdezéséhez. További információ az illesztőprogramokról és az Azure Szinapszishoz való csatlakozásról: [Az Azure szinapszis-illesztőprogramok](sql-data-warehouse-connection-strings.md) és [Az Azure szinapszis-cikkek csatlakoztatása](sql-data-warehouse-connect-overview.md) . |

## <a name="tools"></a>Eszközök

| Probléma                                                        | Feloldás                                                   |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| A Visual Studio Object Explorerből hiányzik az Azure AD-felhasználók           | Ez egy ismert probléma.  Áthidaló megoldásként tekintse meg [sys.database_principals](/sql/relational-databases/system-catalog-views/sys-database-principals-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)felhasználóit.  Lásd: az [Azure szinapszis hitelesítése](sql-data-warehouse-authentication.md) további információ a dedikált SQL-készlettel (korábban SQL DW) való Azure Active Directory használatáról. |
| A manuális parancsfájlok, a parancsfájlkezelés varázsló használata vagy a SSMS-on keresztüli csatlakozás lassú, nem válaszol, vagy hibákat állít elő. | Győződjön meg arról, hogy a felhasználók létre lettek hozva a Master adatbázisban. A parancsfájl-kezelési beállítások területen győződjön meg arról, hogy a motor kiadása "Microsoft Azure szinapszis Analytics Edition", a motor típusa pedig "Microsoft Azure SQL Database". |
| A szkriptek előállítása meghiúsul a SSMS                               | Parancsfájl létrehozása dedikált SQL-készlethez (korábban SQL DW) sikertelen, ha a "függő objektumok létrehozásához szükséges parancsfájl létrehozása" beállítás értéke "true" (igaz). Megkerülő megoldásként a felhasználóknak manuálisan kell megadniuk az **eszközök-> Options->SQL Server Object Explorer-> parancsfájlt létrehozni a függő beállításokhoz, és false** értékre kell állítani. |

## <a name="data-ingestion-and-preparation"></a>Adatfeldolgozás és -előkészítés

| Probléma                                                        | Feloldás                                                   |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| Az üres karakterláncok CETAS használatával történő exportálása NULL értéket eredményez a parketta-és az ork-fájlokban. Megjegyzés: Ha nem NULL értékű korlátozásokkal rendelkező oszlopokból exportál üres karakterláncokat, a CETAS visszautasította a rekordokat, és az Exportálás sikertelen lehet. | Távolítsa el az üres karakterláncokat vagy a jogsértő oszlopot a CETAS SELECT utasításában. |
| A 0-127-tartományon kívüli értéknek a parketta és az ork fájlformátum tinyint oszlopba való betöltése nem támogatott. | Adjon meg nagyobb adattípust a cél oszlophoz.           |

## <a name="performance"></a>Teljesítmény

| Probléma                                                        | Feloldás                                                   |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| A lekérdezés teljesítményével kapcsolatos hibaelhárítás                            | Ha egy adott lekérdezést próbál elhárítani, kezdje a [megtanulni a lekérdezések figyelését](sql-data-warehouse-manage-monitor.md#monitor-query-execution). |
| TempDB lemezterülettel kapcsolatos problémák | [Figyelje a tempdb](sql-data-warehouse-manage-monitor.md#monitor-tempdb) terület használatát.  A TempDB-területek futtatásának gyakori okai a következők:<br>– Nincs elég erőforrás a lekérdezés számára, ami az adatvesztést okozó TempDB.  Lásd: [munkaterhelés-kezelés](resource-classes-for-workload-management.md) <br>– A statisztikák hiányoznak vagy elavultak, ami túlzott adatáthelyezést okoz.  A statisztikák létrehozásával kapcsolatos részletekért tekintse meg a [táblák statisztikáinak fenntartását](sql-data-warehouse-tables-statistics.md) ismertető témakört.<br>– A TempDB terület kiosztása szolgáltatási szintenként történik.  [A DEDIKÁLT SQL-készlet (korábbi nevén SQL DW)](sql-data-warehouse-manage-compute-overview.md#scaling-compute) egy magasabb DWU-beállításra méretezése több tempdb területet foglal le.|
| A hiányos lekérdezési teljesítmény és a tervek gyakran a hiányzó statisztikák eredménye. | A gyenge teljesítmény leggyakoribb oka a táblák statisztikáinak hiánya.  A statisztikák létrehozásával és a teljesítmény szempontjából kritikus fontosságú információkkal kapcsolatos további információkért tekintse meg a [táblák statisztikáinak fenntartását](sql-data-warehouse-tables-statistics.md) ismertető témakört. |
| Alacsony Egyidejűség/lekérdezések várólistán                             | A számítási [feladatok kezelésének](resource-classes-for-workload-management.md) megismerése fontos annak megismerése érdekében, hogy miként lehet kiegyensúlyozni a memória elosztását a párhuzamosságtal. |
| Az ajánlott eljárások megvalósítása                              | A lekérdezési teljesítmény javítására szolgáló legjobb megoldás a [DEDIKÁLT SQL Pool (korábbi nevén SQL DW) ajánlott eljárások](sql-data-warehouse-best-practices.md) című cikke. |
| A teljesítmény növelése a skálázással                      | Előfordulhat, hogy a megoldás javítja a teljesítményt, így a [DEDIKÁLT SQL-készlet (korábbi nevén SQL DW) méretezésével](sql-data-warehouse-manage-compute-overview.md)egyszerűen hozzáadhat nagyobb számítási teljesítményt a lekérdezésekhez. |
| Gyenge lekérdezési teljesítmény a gyenge index minősége miatt     | Bizonyos időpontokban a lekérdezések lelassulnak a [gyenge oszlopcentrikus indexe](sql-data-warehouse-tables-index.md#causes-of-poor-columnstore-index-quality)miatt.  Ebből a cikkből megtudhatja, hogyan [hozhat létre az indexeket a szegmensek minőségének javítása érdekében](sql-data-warehouse-tables-index.md#rebuilding-indexes-to-improve-segment-quality). |

## <a name="system-management"></a>Rendszerkezelés

| Probléma                                                        | Feloldás                                                   |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| Msg 40847: nem végezhető el a művelet, mert a kiszolgáló túllépi az engedélyezett adatbázis-tranzakciós egység (45000) kvótáját. | Csökkentse a létrehozni kívánt adatbázis [DWU](what-is-a-data-warehouse-unit-dwu-cdwu.md) vagy a [kvóta növelését](sql-data-warehouse-get-started-create-support-ticket.md). |
| A terület kihasználtságának vizsgálata                              | A rendszerek lemezterület-kihasználtságának megismeréséhez tekintse meg a [táblázat méretét](sql-data-warehouse-tables-overview.md#table-size-queries) . |
| Súgó a táblázatok kezeléséhez                                    | A táblák kezelésével kapcsolatos segítségért tekintse meg a [táblázat áttekintését](sql-data-warehouse-tables-overview.md) ismertető cikket.  A cikk a részletes témakörökre mutató hivatkozásokat is tartalmaz, például a [táblázat adattípusait](sql-data-warehouse-tables-data-types.md), [a táblázatok terjesztését](sql-data-warehouse-tables-distribute.md), [a táblák indexelését](sql-data-warehouse-tables-index.md),  [a táblák particionálását, a](sql-data-warehouse-tables-partition.md)táblák [statisztikáit](sql-data-warehouse-tables-statistics.md) és az [ideiglenes táblákat](sql-data-warehouse-tables-temporary.md). |
| Az transzparens adattitkosítás (TDE) folyamatjelző sáv nem frissül a Azure Portal | A TDE állapotát a [PowerShell](/powershell/module/az.sql/get-azsqldatabasetransparentdataencryption?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)segítségével tekintheti meg. |

## <a name="differences-from-sql-database"></a>Különbségek a SQL Database

| Probléma                                 | Feloldás                                                   |
| :------------------------------------ | :----------------------------------------------------------- |
| Nem támogatott SQL Database funkciók     | Lásd: nem [támogatott táblázatos funkciók](sql-data-warehouse-tables-overview.md#unsupported-table-features). |
| Nem támogatott SQL Database adattípusok   | Lásd: nem [támogatott adattípusok](sql-data-warehouse-tables-data-types.md#identify-unsupported-data-types).        |
| Tárolt eljárások korlátai          | A tárolt eljárások korlátainak megismeréséhez tekintse meg a [tárolt eljárásokra vonatkozó korlátozásokat](sql-data-warehouse-develop-stored-procedures.md#limitations) . |
| A UDF nem támogatja a SELECT utasítások használatát | Ez a UDF jelenlegi korlátozása.  Az általunk támogatott szintaxishoz lásd: [create Function](/sql/t-sql/statements/create-function-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) . |
| az oszlopok sp_rename (előzetes verzió) nem működik a *dbo* kívüli sémákban | Ez a szinapszis [sp_rename (előzetes verzió) oszlopokra vonatkozó](/sql/relational-databases/system-stored-procedures/sp-rename-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)jelenlegi korlátozása.  A *dbo* séma részét nem képező objektumok oszlopai átnevezhető egy CTAS egy új táblába. |

## <a name="next-steps"></a>Következő lépések

Ha további segítségre van szüksége a probléma megoldásához, néhány további erőforrást is kipróbálhat.

* [Blogok](https://azure.microsoft.com/blog/tag/azure-sql-data-warehouse/)
* [Funkciókérések](https://feedback.azure.com/forums/307516-sql-data-warehouse)
* [Videók](https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse)
* [Támogatási jegy létrehozása](sql-data-warehouse-get-started-create-support-ticket.md)
* [Microsoft Q&egy kérdés oldal](/answers/topics/azure-synapse-analytics.html)
* [Stack Overflow fórum](https://stackoverflow.com/questions/tagged/azure-sqldw)
* [Twitter](https://twitter.com/hashtag/SQLDW)