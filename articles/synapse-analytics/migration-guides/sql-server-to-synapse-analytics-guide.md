---
title: 'SQL Server az Azure szinapszis Analytics szolgáltatáshoz: áttelepítési útmutató'
description: Az alábbi útmutató segítségével áttelepítheti az SQL-adatbázisokat az Azure szinapszis Analytics SQL-készletbe.
ms.service: synapse-analytics
ms.subservice: sql
ms.topic: conceptual
author: julieMSFT
ms.author: jrasnick
ms.reviewer: jrasnick
ms.date: 03/10/2021
ms.openlocfilehash: 9a7888d3ccf7e033f15f184227c65c746780aa12
ms.sourcegitcommit: df1930c9fa3d8f6592f812c42ec611043e817b3b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/13/2021
ms.locfileid: "103418028"
---
# <a name="migration-guide-sql-server-to-a-dedicated-sql-pool-in-azure-synapse-analytics"></a>Áttelepítési útmutató: SQL Server egy dedikált SQL-készlethez az Azure szinapszis Analyticsben 
A következő szakaszokban áttekintheti, hogy mi történik a meglévő SQL Server adattárház-megoldásnak az Azure szinapszis Analytics SQL-készletbe való áttelepítésével kapcsolatban.

## <a name="overview"></a>Áttekintés
Az áttelepítés előtt ellenőriznie kell, hogy az Azure szinapszis Analytics a legjobb megoldás-e a számítási feladatokhoz. Az Azure szinapszis Analytics egy elosztott rendszer, amely a nagyméretű adatok elemzését végzi. Az Azure szinapszis Analytics szolgáltatásba való Migrálás olyan tervezési változásokat igényel, amelyek nem nehezen érthetőek, de a megvalósítás eltarthat egy ideig. Ha vállalata nagyvállalati szintű adattárházat igényel, az előnyök hasznosak. Ha azonban nincs szüksége az Azure szinapszis Analytics teljesítményére, akkor a [SQL Server](/sql/sql-server/) vagy [Azure SQL Database](/azure/azure-sql/database/sql-database-paas-overview)használata költséghatékonyabb.

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

## <a name="prerequisites"></a>Előfeltételek
A SQL Server az Azure szinapszis Analytics szolgáltatásba való áttelepítéséhez ellenőrizze, hogy rendelkezik-e az alábbi előfeltételekkel: 

- Adattárház-vagy elemzési munkaterhelés 
- Az [Azure szinapszis](https://www.microsoft.com/en-us/download/details.aspx?id=102787) -eszköz legújabb verziója, amellyel áttelepíthet SQL Server objektumokat az Azure szinapszis-objektumokra.
- Egy [DEDIKÁLT SQL-készlet](../get-started-create-workspace.md) az Azure szinapszis munkaterületen. 

## <a name="pre-migration"></a>A migrálás előtt
Miután elvégezte egy meglévő megoldás áttelepítését az Azure szinapszis Analytics szolgáltatásba, fontos, hogy megtervezze az áttelepítést az első lépések előtt. A tervezés elsődleges célja annak biztosítása, hogy az adatai, a táblák sémái és a kódok kompatibilisek legyenek az Azure szinapszis Analytics szolgáltatással. A jelenlegi rendszer és SQL Data Warehouse között bizonyos kompatibilitási különbségek vannak, amelyeket érdemes megkerülni. Emellett nagy mennyiségű adattal is áttelepíthet az Azure-ba. A gondos tervezés felgyorsítja az Azure-ba való adatszerzés folyamatát. A tervezés egy másik fő célja, hogy a tervezést úgy állítsa be, hogy a megoldás teljes mértékben kihasználja az Azure szinapszis Analytics által biztosított nagy lekérdezési teljesítményt. Az adattárházak méretezésének megtervezése egyedi tervezési mintákat vezet be, így a hagyományos megközelítések nem mindig a legjobbak. Míg az áttelepítés után egyes tervezési módosítások elvégezhető, a folyamat korábbi módosításait később is megtakaríthatja.

## <a name="azure-synapse-pathway"></a>Az Azure szinapszis útja
Az egyik kritikus blokkoló ügyfeleinek az egyik rendszerről a másikra történő áttelepítésekor az SQL-kód fordítása történik. Az [Azure szinapszis útvonala](/sql/tools/synapse-pathway/azure-synapse-pathway-overview) segítséget nyújt a modern adattárház-platformra való frissítéshez a meglévő adattárház kód fordításának automatizálásával. Ez egy ingyenes, intuitív és könnyen kezelhető eszköz, amely automatizálja a kód fordítását, ami lehetővé teszi az Azure szinapszis Analytics-re való gyorsabb áttelepítést.

## <a name="migrate"></a>Migrate
A sikeres áttelepítés végrehajtásához át kell telepítenie a tábla sémáit, kódját és adatait. A témakörökkel kapcsolatos részletesebb útmutatásért lásd:
- A cikk [áttelepíti a sémákat](https://docs.microsoft.com/azure/synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-develop).
- A cikk [áttelepíti a kódot](https://docs.microsoft.com/azure/synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-develop).
- A cikk [áttelepíti az adatait](https://docs.microsoft.com/azure/synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-develop).

## <a name="additional-resources"></a>További források 
- A CAT (Customer tanácsadó csapat) tartalmaz néhány nagyszerű Azure szinapszis Analytics-(korábban SQL Data Warehouse) útmutatót, amelyet blogbejegyzésként tettek közzé. Ügyeljen arra, hogy az áttelepítéssel kapcsolatos további útmutatásért tekintse meg a cikket, és [telepítse át az Azure SQL Data Warehouse a gyakorlatban](https://docs.microsoft.com/archive/blogs/sqlcat/migrating-data-to-azure-sql-data-warehouse-in-practice).
- További információért és javaslatokért tekintse meg az [adatbázis áttelepítési útvonalát az Azure-ba kiválasztó](https://azure.microsoft.com/mediahandler/files/resourcefiles/choosing-your-database-migration-path-to-azure/Choosing_your_database_migration_path_to_Azure.pdf) tanulmányt.
- A Microsoft és a harmadik féltől származó szolgáltatások és eszközök egy olyan mátrixa, amely a különböző adatbázis-és adatáttelepítési forgatókönyvek, valamint a speciális feladatok elvégzéséhez nyújt segítséget, tekintse meg a cikk [szolgáltatás és eszközök az adatok áttelepítéséhez](https://docs.microsoft.com/azure/dms/dms-tools-matrix)című témakört. 

## <a name="migration-assets-from-real-world-engagements"></a>Áttelepítési eszközök a valós idejű engagements szolgáltatásból
Ha további segítségre van az áttelepítési forgatókönyv végrehajtásával kapcsolatban, tekintse meg a következő forrásokat, amelyek a valós idejű migrációs projektek támogatásában lettek kifejlesztve.

| Cím/hivatkozás                              | Leírás                                                                                                                       |
| --------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------- |
| [Adatmunkaterhelés-felmérési modell és eszköz](https://github.com/Microsoft/DataMigrationTeam/tree/master/Data%20Workload%20Assessment%20Model%20and%20Tool) | Ez az eszköz a javasolt "legmegfelelőbb" cél platformot, a felhő készültségét, valamint az alkalmazások/adatbázisok szervizelési szintjét biztosítja egy adott munkaterhelés esetében. Egyszerű, egykattintásos számítási és jelentéskészítési lehetőséget kínál, amely nagy mértékben segíti a nagyméretű ingatlanok értékelését azáltal, hogy biztosítja és automatizálja a célzott platformra vonatkozó döntési folyamatokat. |
| [Adatkódolási problémák kezeléséhez az Azure szinapszis Analytics szolgáltatásba való betöltés során](https://azure.microsoft.com/en-us/blog/handling-data-encoding-issues-while-loading-data-to-sql-data-warehouse/) | Ez a blog arra szolgál, hogy betekintést nyújtson néhány olyan adatkódolási problémára, amely akkor fordulhat elő, amikor az adatok SQL Data Warehouseba való betöltéséhez használhat. Ez a cikk néhány olyan lehetőséget is kínál, amelyek segítségével elháríthatja az ilyen problémákat, és betöltheti az adatgyűjtést. |
| [A táblázatok méretének beolvasása az Azure szinapszis Analytics SQL-készletben](https://github.com/Microsoft/DataMigrationTeam/blob/master/Whitepapers/Getting%20table%20sizes%20in%20SQL%20DW.pdf) | Az egyik kulcsfontosságú feladat, amelyet az építésznek végre kell hajtania, hogy metrikákat kapjon egy új környezetről a Migrálás után: a helyszíni rendszerből a felhőbe betöltött betöltési idő gyűjtése, a számláló betöltésének időpontja stb. Ezen feladatok közül az egyik legfontosabb az, hogy meghatározza a tárterület méretét SQL Data Warehouse az ügyfél aktuális platformjának megfelelően. |
| [A helyszíni SQL Server bejelentkezések az Azure szinapszis Analytics szolgáltatásba való áthelyezésére szolgáló segédprogram](https://github.com/Microsoft/DataMigrationTeam/tree/master/IP%20and%20Scripts/MoveLogins) | Egy olyan PowerShell-parancsfájl, amely létrehoz egy T-SQL-parancsfájlt a bejelentkezések újbóli létrehozásához és az adatbázis-felhasználók kiválasztásához a "helyszíni" SQL Server egy Azure SQL Péter szolgáltatásba. Az eszköz lehetővé teszi a Windows AD-fiókok automatikus hozzárendelését az Azure AD-fiókokhoz, vagy az egyes bejelentkezésekhez UPN-keresést végezhet a helyszíni Windows Active Directory. Az eszköz opcionálisan áthelyezi SQL Server natív bejelentkezéseket is. Az egyéni kiszolgáló-és adatbázis-szerepkörök parancsfájlokkal, valamint a szerepkör-tagsággal és az adatbázis-szerepkörrel és a felhasználói engedélyekkel rendelkeznek. A foglalt adatbázisok még nem támogatottak, és csak a lehetséges SQL Server engedélyek egy részhalmaza van parancsfájlban. az engedélyek megadása a támogatással nem támogatott (összetett engedélyekkel rendelkező fák). További részletek a támogatási dokumentumban olvashatók, és a parancsfájlnak megjegyzésekkel kell rendelkeznie a könnyű megértéshez. |

> [!NOTE]
> Ezek a fenti erőforrások a adatáttelepítési Jumpstart program (DM Jumpstart) részeként lettek kifejlesztve, amelyet az Azure adatcsoport-mérnöki csapata szponzorál. A DM Jumpstart alapszabálya az, hogy feloldja és felgyorsítja az összetett modernizációt, és a Microsoft Azure-adatplatformján keresztül versengi az adatplatform-migrációs lehetőségeket. Ha úgy gondolja, hogy a szervezete érdekli a DM Jumpstart programban való részvétel, forduljon a fiókhoz, és kérje meg, hogy adjon meg egy jelölést.

## <a name="videos"></a>Videók
- Az Azure Database áttelepítési útmutató és a benne található információk áttekintéséhez tekintse meg az [adatbázis-áttelepítési útmutató használatát](https://azure.microsoft.com/resources/videos/how-to-use-the-azure-database-migration-guide/)ismertető videót.
- Az áttelepítési folyamat fázisainak részletes ismertetését, valamint az értékelés és az áttelepítés elvégzéséhez ajánlott eszközök és szolgáltatások részleteit itt tekintheti meg: az [áttelepítési út és a szükséges eszközök/szolgáltatások áttekintése](https://azure.microsoft.com/resources/videos/overview-of-migration-and-recommended-tools-services/).