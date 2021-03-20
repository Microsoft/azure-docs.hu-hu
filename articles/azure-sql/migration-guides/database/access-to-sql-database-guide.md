---
title: 'Hozzáférés a Azure SQL Databasehoz: áttelepítési útmutató'
description: Ez az útmutató bemutatja, hogyan telepítheti át a Microsoft Access-adatbázisokat a Azure SQL Database SQL Server Migration Assistant használatával (hozzáférés SSMA).
ms.service: sql-database
ms.subservice: migration-guide
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MashaMSFT
ms.author: mathoma
ms.date: 03/19/2021
ms.openlocfilehash: e323b1c15d78da4e8c1a82ae8848df7f59b0dd87
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "104657273"
---
# <a name="migration-guide-access-to-azure-sql-database"></a>Áttelepítési útmutató: hozzáférés a Azure SQL Databasehoz

Ez az áttelepítési útmutató a Microsoft Access-adatbázisok Azure SQL Database való áttelepítését mutatja be a SQL Server Migration Assistant használatával.

Más áttelepítési útmutatókért lásd: [adatbázis-áttelepítés](https://datamigration.microsoft.com/). 

## <a name="prerequisites"></a>Előfeltételek

Ha át szeretné telepíteni az Access-adatbázist Azure SQL Databasere, a következőkre lesz szüksége:

- annak ellenőrzéséhez, hogy a forrás-környezet támogatott-e. 
- [SQL Server Migration Assistant a hozzáféréshez](https://www.microsoft.com/download/details.aspx?id=54255). 

## <a name="pre-migration"></a>A migrálás előtt

Az előfeltételek teljesítése után készen áll a környezet topológiájának felderítésére és az áttelepítés megvalósíthatóságának értékelésére.


### <a name="assess"></a>Kiértékelés 

Hozzon létre egy értékelést [SQL Server Migration Assistant használatával a hozzáféréshez](https://www.microsoft.com/download/details.aspx?id=54255). 

Az értékelés létrehozásához kövesse az alábbi lépéseket: 

1. Nyissa meg SQL Server Migration Assistant a hozzáféréshez. 
1. Válassza a **fájl** , majd az **új projekt** lehetőséget. Adja meg az áttelepítési projekt nevét. 

   ![Új projekt kiválasztása](./media/access-to-sql-database-guide/new-project.png)

1. Válassza az **adatbázisok hozzáadása** lehetőséget, és válassza ki az új projekthez hozzáadni kívánt adatbázisokat. 

   ![Válassza az adatbázisok hozzáadása lehetőséget](./media/access-to-sql-database-guide/add-databases.png)

1. Az **Access metadata Explorerben** kattintson a jobb gombbal az adatbázisra, majd válassza a **jelentés létrehozása** parancsot. 

   ![Kattintson a jobb gombbal az adatbázisra, és válassza a jelentés létrehozása lehetőséget.](./media/access-to-sql-database-guide/create-report.png)

1. Tekintse át a mintavétel értékelését. Például: 

   ![A minta jelentés értékelésének áttekintése](./media/access-to-sql-database-guide/sample-assessment.png)

### <a name="validate-data-types"></a>Adattípusok ellenőrzése

Érvényesítse az alapértelmezett adattípus-leképezéseket, és szükség esetén módosítsa azokat a követelmények alapján. Ehhez kövesse az alábbi lépéseket:

1. Válassza az **eszközök** lehetőséget a menüből. 
1. Válassza a **projekt beállításai** lehetőséget. 
1. Válassza a **típus-hozzárendelések** lapot. 

   ![Típus-hozzárendelések](./media/access-to-sql-database-guide/type-mappings.png)

1. Az egyes táblák típus-hozzárendelését az **Access metadata Explorerben** található táblázat kiválasztásával módosíthatja.


### <a name="convert-schema"></a>Séma konvertálása

Az adatbázis-objektumok átalakításához kövesse az alábbi lépéseket: 

1. Válassza **a kapcsolódás Azure SQL Database** lehetőséget, és adja meg a kapcsolat részleteit.

   ![Kapcsolódás az Azure SQL Database-hez](./media/access-to-sql-database-guide/connect-to-sqldb.png)

1. Kattintson a jobb gombbal az adatbázisra az **Access metadata Explorerben** , és válassza a **séma konvertálása** lehetőséget. Azt is megteheti, hogy a felső navigációs sávon a **séma konvertálása** lehetőséget választja az adatbázis kiválasztása után.

   ![Kattintson a jobb gombbal az adatbázisra, és válassza a séma konvertálása parancsot.](./media/access-to-sql-database-guide/convert-schema.png)

   Konvertált lekérdezések összehasonlítása az eredeti lekérdezésekkel: 

   ![A konvertált lekérdezések összehasonlíthatók a forráskód használatával](./media/access-to-sql-database-guide/query-comparison.png)

   Konvertált objektumok összehasonlítása az eredeti objektumokkal: 

   ![A konvertált objektumokat a forrással lehet összehasonlítani](./media/access-to-sql-database-guide/table-comparison.png)

1. Választható Egy adott objektum konvertálásához kattintson a jobb gombbal az objektumra, és válassza a **séma konvertálása** parancsot. A konvertált objektumok félkövérrel jelennek meg a **hozzáférési metaadatok Explorerben**: 

   ![A metaadat-kezelőben lévő félkövér objektumok konvertálása megtörtént](./media/access-to-sql-database-guide/converted-items.png)
 
1. Válassza az **eredmények áttekintése** lehetőséget a kimenet ablaktáblán, és tekintse át a hibákat a hibák **listája** ablaktáblán. 


## <a name="migrate"></a>Migrate

Miután elvégezte az adatbázisok értékelését és az eltérések kezelését, a következő lépés az áttelepítési folyamat végrehajtása. Az adatáttelepítés egy tömeges betöltési művelet, amely az adatsorokat Azure SQL Databaseba helyezi a tranzakciókban. Az egyes tranzakciók Azure SQL Databaseba betöltendő sorok száma a projekt beállításaiban van konfigurálva.

Az alábbi lépéseket követve áttelepítheti az SSMA for Access használatával: 

1. Ha még nem tette meg, kattintson **a kapcsolódás Azure SQL Database** elemre, és adja meg a kapcsolat részleteit. 
1. Kattintson a jobb gombbal az adatbázisra a **Azure SQL Database metadata Explorerben** , majd válassza az **adatbázissal való szinkronizálás** lehetőséget. Ez a művelet közzéteszi a MySQL-sémát a Azure SQL Database.

   ![Szinkronizálás adatbázissal](./media/access-to-sql-database-guide/synchronize-with-database.png)

   Tekintse át a forrás projekt és a cél közötti leképezést:

   ![Az adatbázissal való szinkronizálás áttekintése](./media/access-to-sql-database-guide/synchronize-with-database-review.png)

1. Az **Access metadata Explorer** használatával tekintse meg az áttelepíteni kívánt elemek melletti jelölőnégyzeteket. Ha a teljes adatbázist át szeretné telepíteni, jelölje be az adatbázis melletti jelölőnégyzetet. 
1. Kattintson a jobb gombbal az áttelepíteni kívánt adatbázisra vagy objektumra, majd válassza az **adatáttelepítés** lehetőséget. 
   Ha egy teljes adatbázisra szeretné áttelepíteni az adatátvitelt, jelölje be az adatbázis neve melletti jelölőnégyzetet. Ha az adatok áttelepíthetők az egyes táblákból, bontsa ki az adatbázist, majd a táblák csomópontot, és jelölje be a tábla melletti jelölőnégyzetet. Ha az adatok kihagyása az egyes táblákból, törölje a jelölőnégyzet jelölését.

    ![Az adatáttelepítés](./media/access-to-sql-database-guide/migrate-data.png)

    Tekintse át az áttelepített adatfájlokat: 

    ![Áttelepíteni az adatellenőrzést](./media/access-to-sql-database-guide/migrate-data-review.png)

1. Kapcsolódjon a Azure SQL Databasehoz a [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) használatával, és ellenőrizze az áttelepítést az adatelemzés és a séma áttekintésével.

   ![Érvényesítés a SSMA](./media/access-to-sql-database-guide/validate-data.png)



## <a name="post-migration"></a>A migrálás után 

Miután sikeresen elvégezte az **áttelepítési** szakaszt, át kell haladnia az áttelepítés utáni feladatok sorozatán, hogy minden a lehető legzökkenőmentesen és hatékonyan működjön.

### <a name="remediate-applications"></a>Alkalmazások szervizelése

Miután áttelepítette az adatátvitelt a cél környezetbe, az összes olyan alkalmazásnak, amely korábban használta a forrást, el kell kezdenie a cél felhasználását. Ennek elvégzése bizonyos esetekben szükségessé teszi az alkalmazások módosítását.

### <a name="perform-tests"></a>Tesztek végrehajtása

Az adatbázis-áttelepítés tesztelési módszere a következő tevékenységek elvégzését tartalmazza:

  1. **Ellenőrzési tesztek fejlesztése**. Az adatbázis-áttelepítés teszteléséhez SQL-lekérdezéseket kell használnia. Létre kell hoznia az érvényesítési lekérdezéseket a forrás-és a célként megadott adatbázisokon való futtatáshoz. Az érvényesítési lekérdezéseknek le kell fedniük a definiált hatókört.

  2. **Tesztkörnyezet beállítása**. A tesztkörnyezet a forrás-adatbázis és a céladatbázis másolatát is tartalmazza. Ügyeljen arra, hogy elkülönítse a tesztkörnyezet.

  3. **Ellenőrzési tesztek futtatása**. Futtassa az ellenőrző teszteket a forráson és a célon, majd elemezze az eredményeket.

  4. **Teljesítménytesztek futtatása**. Futtasson teljesítménytesztet a forráson és a célon, majd elemezze és hasonlítsa össze az eredményeket.

### <a name="optimize"></a>Optimalizálás

Az áttelepítés utáni fázis elengedhetetlen az adatok pontosságával kapcsolatos problémák összeegyeztetéséhez és a teljesség ellenőrzéséhez, valamint a számítási feladatok teljesítményével kapcsolatos problémák kezeléséhez.

További információ ezekről a problémákról és az azok enyhítésére szolgáló konkrét lépésekről: [áttelepítés utáni érvényesítés és optimalizálási útmutató](/sql/relational-databases/post-migration-validation-and-optimization-guide).

## <a name="migration-assets"></a>Áttelepítési eszközök 

Ha további segítségre van az áttelepítési forgatókönyv végrehajtásával kapcsolatban, tekintse meg a következő forrásokat, amelyek a valós idejű migrációs projektek támogatásában lettek kifejlesztve.

| **Cím/hivatkozás**                                                                                                                                          | **Leírás**                                                                                                                                                                                                                                                                                                                              |
| ------------------------------------------------------------------------------------------------------------------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [Adatmunkaterhelés-felmérési modell és eszköz](https://github.com/Microsoft/DataMigrationTeam/tree/master/Data%20Workload%20Assessment%20Model%20and%20Tool) | Ez az eszköz a javasolt "legmegfelelőbb" cél platformot, a felhő készültségét, valamint az alkalmazások/adatbázisok szervizelési szintjét biztosítja egy adott munkaterhelés esetében. Egyszerű, egykattintásos számítási és jelentéskészítési lehetőséget kínál, amely nagy mértékben segíti a nagyméretű ingatlanok értékelését azáltal, hogy biztosítja és automatizálja a célzott platformra vonatkozó döntési folyamatokat. |


Ezek az erőforrások az Azure adatcsoport-mérnöki csapat által szponzorált adatsql ninja program részeként lettek kifejlesztve. Az adatelemzési program alapszintű alapokmánya az, hogy feloldja az összetett modernizációt, és az adatplatform-migrációs lehetőségeket a Microsoft Azure-beli adatplatformján is felgyorsítja. Ha úgy gondolja, hogy a szervezete szeretne részt venni az adatsql ninja programban, forduljon a fiókhoz, és kérje meg, hogy küldje el a jelölést.

## <a name="next-steps"></a>Következő lépések

- A Microsoft és a harmadik féltől származó szolgáltatások és eszközök egyik mátrixa, amely a különböző adatbázis-és adatáttelepítési forgatókönyvek, valamint a speciális feladatok elvégzéséhez nyújt segítséget, lásd: [szolgáltatás és eszközök az adatok áttelepítéséhez](../../../dms/dms-tools-matrix.md).

- Ha többet szeretne megtudni Azure SQL Database lásd:
   - [A SQL Database áttekintése](../../database/sql-database-paas-overview.md)
   - [Az Azure teljes tulajdonlási költsége kalkulátor](https://azure.microsoft.com/pricing/tco/calculator/) 


- Ha többet szeretne megtudni a Felhőbeli Migrálás keretrendszerével és bevezetési ciklusával kapcsolatban, olvassa el a következőt:
   -  [Felhőbevezetési keretrendszer az Azure-hoz](/azure/cloud-adoption-framework/migrate/azure-best-practices/contoso-migration-scale)
   -  [Ajánlott eljárások az Azure-ra való áttéréssel kapcsolatos díjszabási és méretezési feladatok elvégzéséhez](/azure/cloud-adoption-framework/migrate/azure-best-practices/migrate-best-practices-costs) 

- Az alkalmazás-hozzáférési réteg értékeléséhez lásd: [adathozzáférés áttelepítési eszközkészlete (előzetes verzió)](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit)
- Az A/B típusú adatelérési réteg végrehajtásával kapcsolatos további információkért lásd: [Database Experimentation Assistant](/sql/dea/database-experimentation-assistant-overview).

