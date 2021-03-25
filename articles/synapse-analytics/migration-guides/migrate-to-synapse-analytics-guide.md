---
title: 'Azure szinapszis Analytics: áttelepítési útmutató'
description: Ezt az útmutatót követve migrálhatja adatbázisait az Azure szinapszis Analytics dedikált SQL-készletbe.
ms.service: synapse-analytics
ms.subservice: ''
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: julieMSFT
ms.author: jrasnick
ms.reviewer: jrasnick
ms.date: 03/10/2021
ms.openlocfilehash: 8a76a637c4862032b100308d8b02bced76af38fe
ms.sourcegitcommit: a8ff4f9f69332eef9c75093fd56a9aae2fe65122
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2021
ms.locfileid: "105023401"
---
# <a name="migrating-a-data-warehouse-to-a-dedicated-sql-pool-in-azure-synapse-analytics"></a>Adattárház áttelepítése dedikált SQL-készletbe az Azure szinapszis Analyticsben 
A következő szakaszokban áttekintheti, hogy mi a teendő egy meglévő adattárház-megoldás Azure szinapszis Analytics dedikált SQL-készletbe való áttelepítésével kapcsolatban.

## <a name="overview"></a>Áttekintés
Az áttelepítés előtt ellenőriznie kell, hogy az Azure szinapszis Analytics a legjobb megoldás-e a számítási feladatokhoz. Az Azure szinapszis Analytics egy elosztott rendszer, amely a nagyméretű adatok elemzését végzi. Az Azure szinapszis Analytics szolgáltatásba való Migrálás olyan tervezési változásokat igényel, amelyeknek nem nehéz megértenünk, de ez eltarthat egy ideig. Ha vállalata nagyvállalati szintű adattárházat igényel, az előnyök hasznosak. Ha azonban nincs szüksége az Azure szinapszis Analytics teljesítményére, akkor a [SQL Server](https://docs.microsoft.com/sql/sql-server/) vagy [Azure SQL Database](https://docs.microsoft.com/azure/azure-sql/)használata költséghatékonyabb.

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
- Inkompatibilis formátumok (JSON, XML).

## <a name="azure-synapse-pathway"></a>Azure Synapse Pathway
Az egyik kritikus blokkoló ügyfeleinek az egyik rendszerről a másikra történő áttelepítésekor az adatbázis-objektumaikat fordítjuk le. Az [Azure szinapszis útvonala](https://docs.microsoft.com/sql/tools/synapse-pathway/azure-synapse-pathway-overview) segítséget nyújt a modern adattárház-platformra való frissítéshez a meglévő adattárház objektumok fordításának automatizálásával. Ez egy ingyenes, intuitív és könnyen kezelhető eszköz, amely automatizálja a kód fordítását, ami lehetővé teszi az Azure szinapszis Analytics-re való gyorsabb áttelepítést.

## <a name="prerequisites"></a>Előfeltételek
# <a name="migrate-from-sql-server"></a>[Migrálás az SQL Serverről](#tab/migratefromSQLServer)
Ha át szeretné telepíteni SQL Server adattárházát az Azure szinapszis Analytics szolgáltatásba, ellenőrizze, hogy rendelkezik-e az alábbi előfeltételekkel: 

- Adattárház-vagy elemzési munkaterhelés 
- Töltse le a legújabb [Azure szinapszis-útvonal](https://www.microsoft.com/en-us/download/details.aspx?id=102787) eszközt SQL Server objektumok Azure szinapszis-objektumokra való átfrissítéséhez.
- Egy [DEDIKÁLT SQL-készlet](../get-started-create-workspace.md) az Azure szinapszis munkaterületen. 

# <a name="migrate-from-netezza"></a>[Áttelepítés a Netezza](#tab/migratefromNetezza)
A Netezza-adattárház Azure szinapszis Analyticsbe való áttelepítéséhez ellenőrizze, hogy rendelkezik-e az alábbi előfeltételekkel: 

- Töltse le a legújabb [Azure szinapszis-útvonal](https://www.microsoft.com/en-us/download/details.aspx?id=102787) eszközt SQL Server objektumok Azure szinapszis-objektumokra való átfrissítéséhez.
- Egy [DEDIKÁLT SQL-készlet](../get-started-create-workspace.md) az Azure szinapszis munkaterületen.

További információért látogasson el az [Azure szinapszis elemzési megoldásaira és a Netezza-re való áttelepítésére](https://docs.microsoft.com/azure/cloud-adoption-framework/migrate/azure-best-practices/analytics/analytics-solutions-netezza).

# <a name="migrate-from-snowflake"></a>[Migrálás a hópehely-ból](#tab/migratefromSnowflake)
A hópehely-adattárház Azure szinapszis Analyticsbe való áttelepítéséhez ellenőrizze, hogy rendelkezik-e az alábbi előfeltételekkel: 

- Töltse le a legújabb [Azure szinapszis-útvonal](https://www.microsoft.com/en-us/download/details.aspx?id=102787) eszközt a hópehely objektumok Azure szinapszis-objektumokra való átmigrálása érdekében.
- Egy [DEDIKÁLT SQL-készlet](../get-started-create-workspace.md) az Azure szinapszis munkaterületen. 

# <a name="migrate-from-oracle"></a>[Áttelepítés Oracle-ből](#tab/migratefromOracle)
Az Oracle-adattárház Azure szinapszis Analytics szolgáltatásba való áttelepítéséhez ellenőrizze, hogy rendelkezik-e az alábbi előfeltételekkel: 

- Adattárház-vagy elemzési munkaterhelés 
- Töltse le a SSMA for Oracle-t, hogy Oracle-objektumokat alakítsanak SQL Serverba. További információkért lásd: [Oracle-adatbázisok áttelepítése SQL Serverra (OracleToSQL)](https://docs.microsoft.com/sql/ssma/oracle/migrating-oracle-databases-to-sql-server-oracletosql) . 
- Töltse le az [Azure szinapszis-útvonal](https://www.microsoft.com/download/details.aspx?id=102787) legújabb verzióját, hogy áttelepítse SQL Server objektumokat az Azure szinapszis-objektumokra.
- Egy [DEDIKÁLT SQL-készlet](../get-started-create-workspace.md) az Azure szinapszis munkaterületen. 

További információért látogasson el az [Azure szinapszis elemzési megoldásaira és migrálása egy Oracle-adattárházba](https://docs.microsoft.com/azure/cloud-adoption-framework/migrate/azure-best-practices/analytics/analytics-solutions-exadata).

---

## <a name="pre-migration"></a>A migrálás előtt
Miután elvégezte a meglévő megoldás áttelepítését az Azure szinapszis Analyticsre, fontos, hogy megtervezze az áttelepítést az első lépések előtt. A tervezés elsődleges célja annak biztosítása, hogy az adatai, a táblák sémái és a kódok kompatibilisek legyenek az Azure szinapszis Analytics szolgáltatással. A jelenlegi rendszer és az Azure szinapszis Analytics között bizonyos kompatibilitási különbségek vannak, amelyeket érdemes megkerülni. Emellett a nagy mennyiségű adatok áttelepítése az Azure-ba időt vesz igénybe. A gondos tervezés felgyorsítja az Azure-ba való adatszerzés folyamatát. A tervezés egy másik fő célja, hogy a tervezést úgy állítsa be, hogy a megoldás teljes mértékben kihasználja az Azure szinapszis Analytics által biztosított nagy lekérdezési teljesítményt. Az adattárházak méretezésének megtervezése egyedi tervezési mintákat vezet be, így a hagyományos megközelítések nem mindig a legjobbak. Míg az áttelepítés után egyes tervezési módosítások elvégezhető, a folyamat korábbi módosításait később is megtakaríthatja.

## <a name="migrate"></a>Migrate
A sikeres áttelepítés végrehajtásához át kell telepítenie a tábla sémáit, kódját és adatait. A témakörökkel kapcsolatos részletesebb útmutatásért lásd:
- A cikk a [tábla kialakítását vizsgálja](https://docs.microsoft.com/azure/synapse-analytics/sql-data-warehouse/sql-data-warehouse-tables-overview).
- A cikk a [kód módosítását vizsgálja](https://docs.microsoft.com/azure/synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-develop#development-recommendations-and-coding-techniques).
- A cikk [áttelepíti az adatait](https://docs.microsoft.com/azure/synapse-analytics/sql-data-warehouse/design-elt-data-loading).
- A cikk a számítási [feladatok kezelését vizsgálja](https://docs.microsoft.com/azure/synapse-analytics/sql-data-warehouse/sql-data-warehouse-workload-management).

## <a name="additional-resources"></a>További források 
- A CAT (Customer tanácsadó csapat) tartalmaz néhány nagyszerű Azure szinapszis Analytics-(korábban SQL DW-) útmutatót, amelyet blogbejegyzésként tettek közzé. Ügyeljen arra, hogy az áttelepítéssel kapcsolatos további útmutatásért tekintse meg a cikket, és [telepítse át az Azure SQL Data Warehouse a gyakorlatban](https://docs.microsoft.com/archive/blogs/sqlcat/migrating-data-to-azure-sql-data-warehouse-in-practice).

## <a name="migration-assets-from-real-world-engagements"></a>Áttelepítési eszközök a valós idejű engagements szolgáltatásból
Ha további segítségre van az áttelepítési forgatókönyv végrehajtásával kapcsolatban, tekintse meg a következő forrásokat, amelyek a valós idejű migrációs projektek támogatásában lettek kifejlesztve.

| Cím/hivatkozás                              | Leírás                                                                                                                       |
| --------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------- |
| [Adatmunkaterhelés-felmérési modell és eszköz](https://github.com/Microsoft/DataMigrationTeam/tree/master/Data%20Workload%20Assessment%20Model%20and%20Tool) | Ez az eszköz a javasolt "legmegfelelőbb" cél platformot, a felhő készültségét, valamint az alkalmazások/adatbázisok szervizelési szintjét biztosítja egy adott munkaterhelés esetében. Egyszerű, egykattintásos számítási és jelentéskészítési lehetőséget kínál, amely nagy mértékben segíti a nagyméretű ingatlanok értékelését azáltal, hogy biztosítja és automatizálja a célzott platformra vonatkozó döntési folyamatokat. |
| [Adatkódolási problémák kezeléséhez az Azure szinapszis Analytics szolgáltatásba való betöltés során](https://azure.microsoft.com/en-us/blog/handling-data-encoding-issues-while-loading-data-to-sql-data-warehouse/) | Ez a blog arra szolgál, hogy betekintést nyújtson néhány olyan adatkódolási problémára, amely akkor fordulhat elő, amikor az adatok SQL Data Warehouseba való betöltéséhez használhat. Ez a cikk néhány olyan lehetőséget is kínál, amelyek segítségével elháríthatja az ilyen problémákat, és betöltheti az adatgyűjtést. |
| [Táblázatok méretének beolvasása az Azure szinapszis Analytics dedikált SQL-készletében](https://github.com/Microsoft/DataMigrationTeam/blob/master/Whitepapers/Getting%20table%20sizes%20in%20SQL%20DW.pdf) | Az egyik kulcsfontosságú feladat, amelyet az építésznek végre kell hajtania, hogy metrikákat kapjon egy új környezetről a Migrálás után: a helyszíni rendszerből a felhőbe betöltött betöltési idő gyűjtése, a számláló betöltésének időpontja stb. Ezen feladatok közül az egyik legfontosabb az, hogy meghatározza a tárterület méretét SQL Data Warehouse az ügyfél aktuális platformjának megfelelően. |
| [A helyszíni SQL Server bejelentkezések az Azure szinapszis Analytics szolgáltatásba való áthelyezésére szolgáló segédprogram](https://github.com/Microsoft/DataMigrationTeam/tree/master/IP%20and%20Scripts/MoveLogins) | Egy olyan PowerShell-parancsfájl, amely létrehoz egy T-SQL-parancsfájlt a bejelentkezések újbóli létrehozásához és az adatbázis-felhasználók kiválasztásához a "helyszíni" SQL Server egy Azure SQL Péter szolgáltatásba. Az eszköz lehetővé teszi a Windows AD-fiókok automatikus hozzárendelését az Azure AD-fiókokhoz, vagy az egyes bejelentkezésekhez UPN-keresést végezhet a helyszíni Windows Active Directory. Az eszköz opcionálisan áthelyezi SQL Server natív bejelentkezéseket is. Az egyéni kiszolgáló-és adatbázis-szerepkörök parancsfájlokkal, valamint a szerepkör-tagsággal és az adatbázis-szerepkörrel és a felhasználói engedélyekkel rendelkeznek. A foglalt adatbázisok még nem támogatottak, és csak a lehetséges SQL Server engedélyek egy részhalmaza van parancsfájlban. az engedélyek megadása a támogatással nem támogatott (összetett engedélyekkel rendelkező fák). További részletek a támogatási dokumentumban olvashatók, és a parancsfájlnak megjegyzésekkel kell rendelkeznie a könnyű megértéshez. |

Ezek az erőforrások az Azure adatcsoport-mérnöki csapat által szponzorált adatsql ninja program részeként lettek kifejlesztve. Az adatelemzési program alapszintű alapokmánya az, hogy feloldja az összetett modernizációt, és az adatplatform-migrációs lehetőségeket a Microsoft Azure-beli adatplatformján is felgyorsítja. Ha úgy gondolja, hogy a szervezete szeretne részt venni az adatsql ninja programban, forduljon a fiókhoz, és kérje meg, hogy küldje el a jelölést.

## <a name="videos"></a>Videók
- Figyelje meg, hogyan [telepítette a Walgreens a kiskereskedelmi leltározási rendszerét](https://www.youtube.com/watch?v=86dhd8N1lH4) a Netezza és az Azure szinapszis Analytics (korábban SQL DW) 100 TB származó adatok rögzítési időpontjában. 