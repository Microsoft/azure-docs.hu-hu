---
title: 'Azure Synapse Analytics: Áttelepítési útmutató'
description: Kövesse ezt az útmutatót az adatbázisok egy dedikált SQL-Azure Synapse Analytics való áttelepítéshez.
ms.service: synapse-analytics
ms.subservice: sql
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: julieMSFT
ms.author: jrasnick
ms.reviewer: jrasnick
ms.date: 03/10/2021
ms.openlocfilehash: 704c30516e9daf047bf5837aa6e2ed08306193db
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107565644"
---
# <a name="migrate-a-data-warehouse-to-a-dedicated-sql-pool-in-azure-synapse-analytics"></a>Adattárház áttelepítése egy dedikált SQL-készletbe a Azure Synapse Analytics

A következő szakaszok áttekintést nyújtanak a meglévő adattárház-megoldások dedikált SQL-készletbe Azure Synapse Analytics miben szerepet biztosítanak.

## <a name="overview"></a>Áttekintés

A migrálás megkezdése előtt ellenőrizze, hogy a Azure Synapse Analytics-e a legjobb megoldás a számítási feladathoz. Azure Synapse Analytics egy nagyméretű adatok elemzésére tervezett elosztott rendszer. Az áttelepítés Azure Synapse Analytics olyan tervezési módosításokat igényel, amelyek nehezen érthetőek, de a implementálásuk némi időt is igényel. Ha a vállalatnak nagyvállalati szintű adattárházra van szüksége, az előnyöket megéri. Ha azonban nincs szüksége a Azure Synapse Analytics teljesítményre, költséghatékonyabb a SQL Server [vagy](https://docs.microsoft.com/sql/sql-server/) [Azure SQL Database.](https://docs.microsoft.com/azure/azure-sql/)

A következő Azure Synapse Analytics érdemes megfontolni:

- Egy vagy több terabájtnyi adata van.
- Jelentős mennyiségű adat elemzésének futtatását tervezi.
- Szükség van a számítási és tárolási kapacitás skálázható képességére.
- Ha nem szeretné, hogy a számítási erőforrásokat szüneteltetve költségeket takarítson meg.

Az Azure Synapse Analytics helyett fontolja meg az operatív (OLTP) számítási feladatok egyéb lehetőségeit, amelyek a következővel vannak:

- Nagy gyakoriságú olvasások és írások.
- Nagy számú egyszeres kijelölés.
- Nagy mennyiségű egysoros beszúrás.
- Sorról sorra történő feldolgozási igények.
- Inkompatibilis formátumok (például JSON és XML).

## <a name="azure-synapse-pathway"></a>Azure Synapse Pathway

Az ügyfelek számára kritikus fontosságú blokkolási problémák egyike az adatbázis-objektumok lefordítása, amikor az egyik rendszerről a másikra minkálnak. [Azure Synapse útvonal](https://docs.microsoft.com/sql/tools/synapse-pathway/azure-synapse-pathway-overview) a meglévő adattárház objektumfordításának automatizálása révén segít a modern adattárház-platformra való frissítésben. Ez egy ingyenes, intuitív és könnyen használható eszköz, amely automatizálja a kódfordítást, így gyorsabb migrálást tesz lehetővé a Azure Synapse Analytics.

## <a name="prerequisites"></a>Előfeltételek

# <a name="migrate-from-sql-server"></a>[Migrálás az SQL Serverről](#tab/migratefromSQLServer)

Az adattárház SQL Server áttelepítéséhez Azure Synapse Analytics, hogy teljesülnek-e a következő előfeltételek:

- Adattárház vagy elemzési számítási feladat.
- Töltse le a [Azure Synapse-útvonal](https://www.microsoft.com/en-us/download/details.aspx?id=102787) legújabb verzióját a SQL Server áttelepítéséhez Azure Synapse objektumokba.
- Dedikált [SQL-készlettel rendelkezik](../get-started-create-workspace.md) egy Azure Synapse munkaterületen.

# <a name="migrate-from-netezza"></a>[Áttelepítés Netezz ból](#tab/migratefromNetezza)

A Netezza-adattárház áttelepítéséhez Azure Synapse Analytics, győződjön meg arról, hogy megfelel a következő előfeltételeknek:

- Töltse le a [Azure Synapse-útvonal](https://www.microsoft.com/en-us/download/details.aspx?id=102787) legújabb verzióját a SQL Server áttelepítéséhez Azure Synapse objektumokba.
- Dedikált [SQL-készlettel rendelkezik](../get-started-create-workspace.md) egy Azure Synapse munkaterületen.

További információ: Azure Synapse Analytics [Netezzához való migrálás.](https://docs.microsoft.com/azure/cloud-adoption-framework/migrate/azure-best-practices/analytics/analytics-solutions-netezza)

# <a name="migrate-from-snowflake"></a>[Mihelystözés Snowflake-ről](#tab/migratefromSnowflake)

A Snowflake-adattárház áttelepítéséhez Azure Synapse Analytics, győződjön meg arról, hogy megfelel a következő előfeltételeknek:

- Töltse le a Azure Synapse [Útvonal](https://www.microsoft.com/en-us/download/details.aspx?id=102787) legújabb verzióját a Snowflake-objektumok áttelepítéséhez Azure Synapse objektumokba.
- Dedikált [SQL-készlettel rendelkezik](../get-started-create-workspace.md) egy Azure Synapse munkaterületen.

# <a name="migrate-from-oracle"></a>[Áttelepítés Oracle-ről](#tab/migratefromOracle)

Az Oracle-adattárház áttelepítéséhez a Azure Synapse Analytics, győződjön meg arról, hogy megfelel a következő előfeltételeknek:

- Adattárház vagy elemzési számítási feladat.
- Oracle SQL Server Migration Assistant letöltés az Oracle-hez az Oracle-objektumok SQL Server. További információ: [Oracle-adatbázisok áttelepítése SQL Server (OracleToSQL) szolgáltatásba.](https://docs.microsoft.com/sql/ssma/oracle/migrating-oracle-databases-to-sql-server-oracletosql)
- Töltse le a [Azure Synapse-útvonal](https://www.microsoft.com/download/details.aspx?id=102787) legújabb verzióját a SQL Server áttelepítéséhez Azure Synapse objektumokba.
- Dedikált [SQL-készlettel rendelkezik](../get-started-create-workspace.md) egy Azure Synapse munkaterületen.

További információkért lásd: [Azure Synapse Analytics Oracle-adattárház megoldásait és migrálását.](https://docs.microsoft.com/azure/cloud-adoption-framework/migrate/azure-best-practices/analytics/analytics-solutions-exadata)

---

## <a name="pre-migration"></a>A migrálás előtt

Miután úgy döntött, hogy egy meglévő megoldást migrál a Azure Synapse Analytics, meg kell tervezni a migrálást, mielőtt nekivág. A tervezés elsődleges célja annak biztosítása, hogy az adatok, a táblasémák és a kód kompatibilisek Azure Synapse Analytics. Van néhány kompatibilitási különbség a jelenlegi rendszer és a Azure Synapse Analytics között, amit meg kell dolgoznia. Emellett a nagy mennyiségű adat Azure-ba való áttelepítése is időt vesz igénybe. A gondos tervezés felgyorsítja az adatok Azure-ba való beszerzésének folyamatát.

A tervezés másik fő célja a kialakítás olyan beállítása, hogy a megoldás teljes mértékben kihasználja a Azure Synapse Analytics által biztosított nagy lekérdezési teljesítményt. Az adattárház méretezésre való tervezése egyedi kialakítási mintákat vezet be, így a hagyományos megközelítések nem mindig a legjobbak. Bár a migrálás után el lehet végezni bizonyos módosításokat, a folyamat korábbi módosításaival időt takaríthat meg később.

## <a name="migrate"></a>Migrate

A sikeres migráláshoz át kell migrálnia a táblasémát, a kódot és az adatokat. Az ezekkel a témakörökvel kapcsolatos részletesebb útmutatásért tekintse meg a következő cikkeket:

- [Táblatervezés megfontolva](https://docs.microsoft.com/azure/synapse-analytics/sql-data-warehouse/sql-data-warehouse-tables-overview)
- [Fontolja meg a kódváltást](https://docs.microsoft.com/azure/synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-develop#development-recommendations-and-coding-techniques)
- [Adatok áttelepítése](https://docs.microsoft.com/azure/synapse-analytics/sql-data-warehouse/design-elt-data-loading)
- [A számítási feladatok felügyeletének megfontolása](https://docs.microsoft.com/azure/synapse-analytics/sql-data-warehouse/sql-data-warehouse-workload-management)

## <a name="more-resources"></a>További erőforrások

Az ügyféltanácsadói csapatnak van néhány Azure Synapse Analytics (korábban Azure SQL Data Warehouse) útmutatója blogbejegyzésekként közzétéve. Az áttelepítéssel kapcsolatos további információkért lásd: Adatok áttelepítése Azure SQL Data Warehouse [a gyakorlatban.](https://docs.microsoft.com/archive/blogs/sqlcat/migrating-data-to-azure-sql-data-warehouse-in-practice)

## <a name="migration-assets-from-real-world-engagements"></a>Migrálási eszközök valós idejű kötelezettségvállalásból

A migrálási forgatókönyv elvégzésével kapcsolatos további segítségért tekintse meg a következő forrásokat. Egy valós migrálási projekt támogatásaként fejlesztették ki őket.

| Cím/hivatkozás                              | Description                                                                                                                       |
| --------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------- |
| [Adatterhelés-értékelési modell és eszköz](https://github.com/Microsoft/DataMigrationTeam/tree/master/Data%20Workload%20Assessment%20Model%20and%20Tool) | Ez az eszköz javasolt "legmegfelelőbb" célplatformokat, felhőalapú készenlétet és alkalmazás- vagy adatbázis-szervizelési szintet biztosít egy adott számítási feladathoz. Egyszerű, egykattintásos számítást és jelentés-készítést kínál, amely automatizált és egységes célplatform-döntési folyamat biztosításával segít felgyorsítani a nagy tulajdonfelméréseket. |
| [Adatkódolási problémák kezelése az adatok Azure Synapse Analytics](https://azure.microsoft.com/en-us/blog/handling-data-encoding-issues-while-loading-data-to-sql-data-warehouse/) | Ez a blogbejegyzés betekintést nyújt néhány adatkódolási problémába, amelyek akkor merülhetnek fel, amikor a PolyBase használatával tölt be adatokat az SQL-Data Warehouse. Ez a cikk néhány olyan lehetőséget is tartalmaz, amelyek segítségével áthidalhatja az ilyen problémákat, és sikeresen betöltheti az adatokat. |
| [Táblák méretének le Azure Synapse Analytics SQL-készletben](https://github.com/Microsoft/DataMigrationTeam/blob/master/Whitepapers/Getting%20table%20sizes%20in%20SQL%20DW.pdf) | A tervezők számára az egyik legfontosabb feladat az új környezet migrálás utáni mérőszámának lekérte. Ilyen például a helyszínről a felhőbe való betöltés, illetve a PolyBase betöltési idők gyűjtése. Az egyik legfontosabb feladat az SQL-Data Warehouse méretének meghatározása az ügyfél jelenlegi platformján. |
| [Segédprogram a helyszíni bejelentkezések SQL Server a Azure Synapse Analytics](https://github.com/Microsoft/DataMigrationTeam/tree/master/IP%20and%20Scripts/MoveLogins) | A PowerShell-szkript létrehoz egy T-SQL-parancsprogramot a bejelentkezések újra létrehozásához, és kiválasztja az adatbázis-felhasználókat az SQL Server egy helyszíni példányából egy Azure SQL-szolgáltatásként használt platformra (PaaS). Az eszköz lehetővé teszi a Windows Server Active Directory fiókok automatikus leképezését Azure Active Directory-fiókokhoz, vagy upn-kereséseket is képes az egyes bejelentkezések esetében a helyszíni Windows Server Active Directory. Az eszköz igény szerint áthelyezi SQL Server natív bejelentkezéseket is. Az egyéni kiszolgálói és adatbázis-szerepkörök parancsfájlokkal vannak megszk parancsprogrammal, valamint szerepkörtagság, adatbázis-szerepkör és felhasználói engedélyek. A tartalmazott adatbázisok nem támogatottak, és a rendszer csak a lehetséges SQL Server részkészletét szkripteli. A támogatási dokumentumban további információk érhetők el, a szkript pedig megjegyzésekkel rendelkezik a könnyebb megértés érdekében. |

Ezeket az erőforrásokat az SQL mérnöki csapata fejlesztette ki. Ennek a csapatnak az alapvető csomagja a Microsoft Azure-adatplatformjára irányuló adatplatform-migrálási projektek összetett modernizálásának feloldására és felgyorsítására szolgál.

## <a name="videos"></a>Videók

Figyelje meg, hogyan migrálta a [Walgreens](https://www.youtube.com/watch?v=86dhd8N1lH4) a kiskereskedelmi leltárrendszert körülbelül 100 TB-os adatokkal a Netezzába, Azure Synapse Analytics rekordidőben.
