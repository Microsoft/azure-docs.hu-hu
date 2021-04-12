---
title: 'Azure szinapszis Analytics: áttelepítési útmutató'
description: Ezt az útmutatót követve migrálhatja adatbázisait egy Azure szinapszis Analytics dedikált SQL-készletbe.
ms.service: synapse-analytics
ms.subservice: ''
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: julieMSFT
ms.author: jrasnick
ms.reviewer: jrasnick
ms.date: 03/10/2021
ms.openlocfilehash: 8304064e62ea3996e2ee6be6e12885cb853c9375
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2021
ms.locfileid: "106278779"
---
# <a name="migrate-a-data-warehouse-to-a-dedicated-sql-pool-in-azure-synapse-analytics"></a>Adattárház migrálása dedikált SQL-készletbe az Azure szinapszis Analyticsben

A következő szakaszokban áttekintheti, hogy mi a teendő egy meglévő adattárház-megoldás Azure szinapszis Analytics dedikált SQL-készletbe való áttelepítésével kapcsolatban.

## <a name="overview"></a>Áttekintés

Az áttelepítés megkezdése előtt győződjön meg arról, hogy az Azure szinapszis Analytics a legjobb megoldás a számítási feladatokhoz. Az Azure szinapszis Analytics egy elosztott rendszer, amely a nagyméretű adatok elemzését végzi. Az Azure szinapszis Analytics szolgáltatásba való Migrálás olyan tervezési változásokat igényel, amelyek nem nehezen érthetőek, de a megvalósítás eltarthat egy ideig. Ha vállalata nagyvállalati szintű adattárházat igényel, az előnyök hasznosak. Ha azonban nincs szüksége az Azure szinapszis Analytics teljesítményére, akkor a [SQL Server](https://docs.microsoft.com/sql/sql-server/) vagy [Azure SQL Database](https://docs.microsoft.com/azure/azure-sql/)használata költséghatékonyabb.

Vegye fontolóra az Azure szinapszis Analytics használatát, ha:

- Legalább egy terabájt adattal rendelkezik.
- Jelentős mennyiségű adattal tervezze meg az elemzést.
- A számítás és a tárolás méretezésének lehetősége szükséges.
- A számítási erőforrások felfüggesztésével szeretné menteni a költségeket, ha nincs rá szükség.

Az Azure szinapszis Analytics helyett érdemes megfontolni az operatív (OLTP) számítási feladatokra vonatkozó egyéb lehetőségeket:

- Nagy gyakoriságú olvasások és írások.
- Nagy számú egyszeri kijelölés.
- Nagy mennyiségű egysoros lapkák.
- Sorok szerinti feldolgozási igények.
- Nem kompatibilis formátumok (például JSON és XML).

## <a name="azure-synapse-pathway"></a>Azure Synapse Pathway

Az egyik kritikus blokkoló ügyfelek arca lefordítja az adatbázis-objektumokat az egyik rendszerről a másikra való Migrálás során. Az [Azure szinapszis útvonala](https://docs.microsoft.com/sql/tools/synapse-pathway/azure-synapse-pathway-overview) segítséget nyújt a modern adattárház-platformra való frissítéshez a meglévő adattárház objektumok fordításának automatizálásával. Ez egy ingyenes, intuitív és könnyen használható eszköz, amely automatizálja a kód fordítását, így lehetővé téve az Azure szinapszis Analytics szolgáltatásba való gyorsabb áttelepítést.

## <a name="prerequisites"></a>Előfeltételek

# <a name="migrate-from-sql-server"></a>[Migrálás az SQL Serverről](#tab/migratefromSQLServer)

Ha át szeretné telepíteni a SQL Server-adattárházat az Azure szinapszis Analytics szolgáltatásba, győződjön meg arról, hogy teljesítette a következő előfeltételeket:

- Adattárház-vagy elemzési számítási feladattal rendelkezik.
- Töltse le az [Azure szinapszis-útvonal](https://www.microsoft.com/en-us/download/details.aspx?id=102787) legújabb verzióját, hogy áttelepítse SQL Server objektumokat az Azure szinapszis-objektumokra.
- [DEDIKÁLT SQL-készlettel](../get-started-create-workspace.md) rendelkezik egy Azure szinapszis-munkaterületen.

# <a name="migrate-from-netezza"></a>[Áttelepítés a Netezza](#tab/migratefromNetezza)

A Netezza-adattárház Azure szinapszis Analyticsbe való áttelepítéséhez győződjön meg arról, hogy teljesítette a következő előfeltételeket:

- Töltse le az [Azure szinapszis-útvonal](https://www.microsoft.com/en-us/download/details.aspx?id=102787) legújabb verzióját, hogy áttelepítse SQL Server objektumokat az Azure szinapszis-objektumokra.
- [DEDIKÁLT SQL-készlettel](../get-started-create-workspace.md) rendelkezik egy Azure szinapszis-munkaterületen.

További információ: [Azure szinapszis analitikai megoldások és Migrálás a Netezza-](https://docs.microsoft.com/azure/cloud-adoption-framework/migrate/azure-best-practices/analytics/analytics-solutions-netezza)hoz.

# <a name="migrate-from-snowflake"></a>[Migrálás a hópehely-ból](#tab/migratefromSnowflake)

A hópehely-adattárház Azure szinapszis Analyticsbe való áttelepítéséhez győződjön meg arról, hogy teljesítette a következő előfeltételeket:

- Töltse le az [Azure szinapszis-útvonal](https://www.microsoft.com/en-us/download/details.aspx?id=102787) legújabb verzióját a hópehely objektumok Azure szinapszis-objektumokra való átmigrálása érdekében.
- [DEDIKÁLT SQL-készlettel](../get-started-create-workspace.md) rendelkezik egy Azure szinapszis-munkaterületen.

# <a name="migrate-from-oracle"></a>[Áttelepítés Oracle-ből](#tab/migratefromOracle)

Az Oracle-adattárház Azure szinapszis Analyticsbe való áttelepítéséhez győződjön meg arról, hogy teljesítette a következő előfeltételeket:

- Adattárház-vagy elemzési számítási feladattal rendelkezik.
- Töltse le SQL Server Migration Assistant az Oracle-hez, hogy Oracle-objektumokat Konvertáljon SQL Serverba. További információ: Oracle- [adatbázisok áttelepítése SQL Serverra (OracleToSQL)](https://docs.microsoft.com/sql/ssma/oracle/migrating-oracle-databases-to-sql-server-oracletosql).
- Töltse le az [Azure szinapszis-útvonal](https://www.microsoft.com/download/details.aspx?id=102787) legújabb verzióját, hogy áttelepítse SQL Server objektumokat az Azure szinapszis-objektumokra.
- [DEDIKÁLT SQL-készlettel](../get-started-create-workspace.md) rendelkezik egy Azure szinapszis-munkaterületen.

További információ: [Azure szinapszis analitikai megoldások és Migrálás egy Oracle-adattárházban](https://docs.microsoft.com/azure/cloud-adoption-framework/migrate/azure-best-practices/analytics/analytics-solutions-exadata).

---

## <a name="pre-migration"></a>A migrálás előtt

Miután eldöntötte, hogy meglévő megoldást telepít át az Azure szinapszis Analyticsre, meg kell terveznie az áttelepítést az első lépések megkezdése előtt. A tervezés elsődleges célja annak biztosítása, hogy az adatai, a táblák sémái és a kódok kompatibilisek legyenek az Azure szinapszis Analytics szolgáltatással. A jelenlegi rendszer és az Azure szinapszis Analytics számos kompatibilitási különbséggel rendelkezik, amelyekkel megkerülheti a munkát. Emellett a nagy mennyiségű adatok áttelepítése az Azure-ba időt vesz igénybe. A gondos tervezés felgyorsítja az Azure-ba való adatszerzés folyamatát.

A tervezés egy másik fő célja, hogy a tervezést úgy állítsa be, hogy a megoldás teljes mértékben kihasználja az Azure szinapszis Analytics által biztosított nagy lekérdezési teljesítményt. Az adattárházak méretezésének megtervezése egyedi tervezési mintákat vezet be, így a hagyományos megközelítések nem mindig a legjobbak. Míg az áttelepítés után egyes tervezési módosítások elvégezhető, a folyamat korábbi módosításait később is megtakaríthatja.

## <a name="migrate"></a>Migrate

A sikeres áttelepítés végrehajtásához át kell telepítenie a tábla sémáit, kódját és adatait. A témakörökkel kapcsolatos részletesebb útmutatásért tekintse meg a következő cikkeket:

- [A tábla kialakításának megfontolása](https://docs.microsoft.com/azure/synapse-analytics/sql-data-warehouse/sql-data-warehouse-tables-overview)
- [A kód módosításának megfontolása](https://docs.microsoft.com/azure/synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-develop#development-recommendations-and-coding-techniques)
- [Adatok áttelepítése](https://docs.microsoft.com/azure/synapse-analytics/sql-data-warehouse/design-elt-data-loading)
- [A számítási feladatok kezelésének megfontolása](https://docs.microsoft.com/azure/synapse-analytics/sql-data-warehouse/sql-data-warehouse-workload-management)

## <a name="more-resources"></a>További erőforrások

Az ügyfél-tanácsadó csapatnak van néhány nagyszerű Azure szinapszis Analytics (korábban Azure SQL Data Warehouse) útmutatója, amely blogbejegyzésként lett közzétéve. Az áttelepítéssel kapcsolatos további információkért lásd: [adatok áttelepítése Azure SQL Data Warehousera a gyakorlatban](https://docs.microsoft.com/archive/blogs/sqlcat/migrating-data-to-azure-sql-data-warehouse-in-practice).

## <a name="migration-assets-from-real-world-engagements"></a>Áttelepítési eszközök a valós idejű engagements szolgáltatásból

Az áttelepítési forgatókönyv végrehajtásával kapcsolatos további segítségért tekintse meg a következő forrásokat. Egy valós migrációs projekt bevonásának támogatásával lettek kifejlesztve.

| Cím/hivatkozás                              | Leírás                                                                                                                       |
| --------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------- |
| [Adatmunkaterhelés-felmérési modell és eszköz](https://github.com/Microsoft/DataMigrationTeam/tree/master/Data%20Workload%20Assessment%20Model%20and%20Tool) | Ez az eszköz a javasolt "legmegfelelőbb" cél platformot, a felhő készültségét, valamint az alkalmazás vagy az adatbázis szervizelési szintjét biztosítja egy adott munkaterhelés esetében. Egyszerű, egykattintásos számítási és jelentéskészítési lehetőséget kínál, amely lehetővé teszi a nagyméretű ingatlan-értékelések felgyorsítását azáltal, hogy automatizált és egységes célzott platformra vonatkozó döntési folyamatot biztosít. |
| [Adatkódolási problémák kezeléséhez az Azure szinapszis Analytics szolgáltatásba való betöltés során](https://azure.microsoft.com/en-us/blog/handling-data-encoding-issues-while-loading-data-to-sql-data-warehouse/) | Ez a blogbejegyzés betekintést nyújt néhány olyan adatkódolási problémával, amely akkor fordulhat elő, amikor a Base használatával betölti az adatok SQL Data Warehouseba való betöltését. Ez a cikk néhány olyan lehetőséget is kínál, amelyek segítségével elháríthatja az ilyen problémákat, és betöltheti az adatgyűjtést. |
| [Táblázatok méretének beolvasása az Azure szinapszis Analytics dedikált SQL-készletében](https://github.com/Microsoft/DataMigrationTeam/blob/master/Whitepapers/Getting%20table%20sizes%20in%20SQL%20DW.pdf) | Az egyik kulcsfontosságú feladat, amelyet az építésznek végre kell hajtania az új környezettel kapcsolatos mérőszámok beszerzése az áttelepítés után. Ilyenek például a helyszíni és a felhőbe betöltési idő gyűjtése, valamint a rendszerbe való betöltési idő begyűjtése. Az egyik legfontosabb feladat az, hogy meghatározza a SQL Data Warehouse tárolási méretét az ügyfél aktuális platformjának megfelelően. |
| [A helyszíni SQL Server bejelentkezések az Azure szinapszis Analytics szolgáltatásba való áthelyezésére szolgáló segédprogram](https://github.com/Microsoft/DataMigrationTeam/tree/master/IP%20and%20Scripts/MoveLogins) | Egy PowerShell-szkript létrehoz egy T-SQL-parancsfájlt a bejelentkezések újbóli létrehozásához és az adatbázis-felhasználók kiválasztásához a SQL Server helyszíni példányáról egy Azure SQL platform szolgáltatásként (Péter). Az eszköz lehetővé teszi a Windows Server Active Directory fiókjainak automatikus leképezését Azure Active Directory fiókokhoz, vagy a helyszíni Windows Server-Active Directoryokhoz tartozó UPN-kereséseket is végezhet. Az eszköz opcionálisan áthelyezi SQL Server natív bejelentkezéseket is. Az egyéni kiszolgáló-és adatbázis-szerepkörök parancsfájlokkal vannak ellátva, a szerepkör-tagsággal, az adatbázis-szerepkörrel és a felhasználói engedélyekkel együtt. A foglalt adatbázisok nem támogatottak, és csak a lehetséges SQL Server engedélyek egy részhalmaza van parancsfájlban. További információk érhetők el a támogatási dokumentumban, és a parancsfájlban megjegyzések találhatók a könnyű megértéshez. |

Az adatsql mérnöki csapat fejlesztette ezeket az erőforrásokat. A csapat alapszintű alapokmánya az adatplatform-áttelepítési projektek a Microsoft Azure-beli adatplatformra való feltiltásának és felgyorsításának feloldása.

## <a name="videos"></a>Videók

Figyelje meg, hogyan [telepítette át a Walgreens a kiskereskedelmi leltározási 100 rendszerét](https://www.youtube.com/watch?v=86dhd8N1lH4) a Netezza-ról az Azure szinapszis Analytics szolgáltatásba a rekord időben.
