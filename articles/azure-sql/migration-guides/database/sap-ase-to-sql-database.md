---
title: 'SAP ASE Azure SQL Database: Migrálási útmutató'
description: Ebből az útmutatóból megtudhatja, hogyan miheti át SAP ASE-adatbázisát egy Azure SQL-adatbázisba az SAP Adapter Server Enterprise SQL Server Migration Assistant használatával.
ms.service: sql-database
ms.subservice: migration-guide
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: MashaMSFT
ms.author: mathoma
ms.date: 03/19/2021
ms.openlocfilehash: 0538071ffb9d244fb8b3493d6b63b27c6b56a726
ms.sourcegitcommit: aa00fecfa3ad1c26ab6f5502163a3246cfb99ec3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2021
ms.locfileid: "107388533"
---
# <a name="migration-guide-sap-ase-to-azure-sql-database"></a>Migrálási útmutató: SAP ASE Azure SQL Database

[!INCLUDE[appliesto-sqldb-sqlmi](../../includes/appliesto-sqldb.md)]

Ebből az útmutatóból [](https://azure.microsoft.com/migration/migration-journey) megtudhatja, hogyan migrálhatóak az SAP Adapter Server Enterprise (ASE) adatbázisai egy Azure SQL-adatbázisba az [SQL Server Migration](https://azure.microsoft.com/en-us/migration/sql-server/) Assistant for SAP Adapter Server Enterprise használatával.

További migrálási útmutatókért lásd az [Azure Database migrálási útmutatóját.](https://docs.microsoft.com/data-migration) 

## <a name="prerequisites"></a>Előfeltételek 

Az SAP SE-adatbázis SQL-adatbázisba való áttelepítésének megkezdése előtt tegye a következőket:

- Ellenőrizze, hogy a forráskörnyezet támogatott-e. 
- Töltse le és [telepítse SQL Server Migration Assistant SAP Adaptive Server Enterprise (korábban SAP Sybase ASE) rendszerhez.](https://www.microsoft.com/en-us/download/details.aspx?id=54256)
- Győződjön meg arról, hogy rendelkezik kapcsolattal és megfelelő engedélyekkel mind a forráshoz, mind a célhoz való hozzáféréshez.

## <a name="pre-migration"></a>A migrálás előtt

Az előfeltételek teljesültével készen áll arra, hogy felfedezze a környezet topológiáját, és felmérje az Azure-felhőbe való [migrálás megvalósíthatóságát.](https://azure.microsoft.com/migration)

### <a name="assess"></a>Kiértékelés

Az SAP Adaptive Server Enterprise -hez [(hivatalosan SAP Sybase ASE) használható SQL Server Migration Assistant (SSMA)](https://www.microsoft.com/en-us/download/details.aspx?id=54256)használatával áttekintheti az adatbázis-objektumokat és adatokat, kiértékelheti az adatbázisokat a migráláshoz, Sybase-adatbázisobjektumokat migrálhat az SQL-adatbázisba, majd adatokat migrálhat az SQL-adatbázisba. További információ: [SQL Server Migration Assistant Sybase (SybaseToSQL) szolgáltatáshoz.](/sql/ssma/sybase/sql-server-migration-assistant-for-sybase-sybasetosql)

Értékelés létrehozásához tegye a következőket: 

1. Nyissa meg az SSMA-t a Sybase-hez. 
1. Válassza **a File (Fájl)** lehetőséget, majd a **New Project (Új projekt) lehetőséget.** 
1. A **New Project (Új projekt)** panelen adja meg a projekt nevét és **helyét,** majd a **Migrate To** (Áttelepítés ide) legördülő listában válassza a Azure SQL Database. 
1. Válassza az **OK** lehetőséget.
1. A Csatlakozás **a Sybase-hez panelen** adja meg az SAP-kapcsolat adatait. 
1. Kattintson a jobb gombbal az átemelni kívánt SAP-adatbázisra, majd válassza a **Jelentés létrehozása lehetőséget.** Ez létrehoz egy HTML-jelentést. Másik lehetőségként válassza a jelentés létrehozása **lapot** a jobb felső sarokban.
1. Tekintse át a HTML-jelentést a konverziós statisztikák, valamint az esetleges hibák és figyelmeztetések áttekintéséhez. A jelentést az Excelben is megnyithatja, hogy leltárt kap az SAP ASE-objektumokról és a sémakonverziók végrehajtásához szükséges munkamennyiségről. A jelentés alapértelmezett helye az SSMAProjects jelentésmappában található. Például:

   `drive:\<username>\Documents\SSMAProjects\MySAPMigration\report\report_<date>` 

### <a name="validate-the-type-mappings"></a>A típusleképezések ellenőrzése

Sémakonverzió végrehajtása előtt ellenőrizze az alapértelmezett adattípus-leképezéseket, vagy módosítsa azokat a követelmények alapján. Ezt a **Tools** Project Settings (Eszközök projektbeállítások) lehetőség kiválasztásával, vagy az egyes tábla típusleképezésének módosításához válassza ki a táblát az  >   **SAP ASE Metadata Explorerben.**

### <a name="convert-the-schema"></a>A séma konvertálása

A séma átalakításához tegye a következőket:

1. (Nem kötelező) Dinamikus vagy speciális lekérdezések átalakításához kattintson a jobb gombbal a csomópontra, majd válassza az **Utasítás hozzáadása lehetőséget.** 
1. Válassza a **Csatlakozás Azure SQL Database** lapot, majd adja meg az SQL-adatbázis adatait. Választhat, hogy egy meglévő adatbázishoz csatlakozik, vagy új nevet ad meg, amely esetben a rendszer létrehoz egy adatbázist a célkiszolgálón.
1. A **Sybase Metadata Explorer** panelen kattintson a jobb gombbal a használni kívánt SAP ASE-sémára, majd válassza a **Séma konvertálása lehetőséget.** 
1. A séma konvertálása után hasonlítsa össze és tekintse át a konvertált struktúrát az eredeti struktúrára, és azonosítsa a lehetséges problémákat. 

   A sémaátalakítás után helyileg mentheti ezt a projektet egy offline séma-szervizelési gyakorlathoz. Válassza a Fájl **Projekt**  >  **mentése lehetőséget.** Ez lehetővé teszi a forrás- és célsémák offline kiértékelését és szervizelését, mielőtt közzétenné a sémát az SQL-adatbázisban.

1. A Kimenet **panelen** válassza az **Eredmények** áttekintése lehetőséget, és tekintse át az esetleges hibákat a **Hibalista panelen.** 
1. Mentse helyileg a projektet egy offline séma-szervizelési gyakorlathoz. Válassza a Fájl **Projekt**  >  **mentése lehetőséget.** Ez lehetővé teszi a forrás- és célsémák offline kiértékelését és szervizelését, mielőtt közzétenné a sémát az SQL-adatbázisban.

## <a name="migrate-the-databases"></a>Adatbázisok áttelepítése 

Ha már rendelkezésre állnak a szükséges előfeltételek, és  befejezte a migrálás előtti szakaszhoz társított feladatokat, készen áll a séma és az adatok migrálásának futtatására.

A séma közzétételéhez és az adatok áttelepítéséhez tegye a következőket: 

1. Tegye közzé a sémát. A Azure SQL Database **Metaadat-kezelő panelen** kattintson a jobb gombbal az adatbázisra, majd válassza a **Szinkronizálás adatbázissal lehetőséget.** Ez a művelet közzéteszi az SAP ASE sémát az SQL-adatbázisban.

1. Az adatok áttelepítése. Az **SAP ASE Metadata Explorer** panelen kattintson a jobb gombbal az átemelni kívánt SAP ASE-adatbázisra vagy -objektumra, majd válassza az **Adatok áttelepítése lehetőséget.** Másik lehetőségként a jobb felső **sarokban** található Adatok áttelepítése lapot is választhatja. 

   Egy teljes adatbázis adatainak áttelepítéséhez jelölje be az adatbázis neve melletti jelölőnégyzetet. Az adatok egyes táblákból való áttelepítéséhez bontsa ki az adatbázist, bontsa ki a **Táblák** gombra, majd jelölje be a tábla melletti jelölőnégyzetet. Az egyes táblák adatainak kihagyása érdekében törölje a jelölést. 
1. Az áttelepítés befejezése után tekintse meg az **adatáttelepítési jelentést.** 
1. A migrálás ellenőrzéséhez tekintse át az adatokat és a sémát. A következővel csatlakozhat az SQL-adatbázishoz: [SQL Server Management Studio.](/sql/ssms/download-sql-server-management-studio-ssms)

## <a name="post-migration"></a>A migrálás után 

Miután sikeresen befejezte a migrálási szakaszt, több migrálás utáni feladatot kell elvégeznie annak érdekében, hogy minden a lehető zökkenőmentesen és leghatékonyabban működjön. 

### <a name="remediate-applications"></a>Alkalmazások szervize

Az adatok célkörnyezetbe való migrálása után az összes olyan alkalmazásnak, amely korábban a forrást felhasználta, el kell kezdenie a cél fogyasztását. Ennek megvalósítása bizonyos esetekben az alkalmazások módosításait igényli.

### <a name="perform-tests"></a>Tesztek végrehajtása

Az adatbázis-migrálás tesztelési megközelítése a következő tevékenységekből áll:

1. **Ellenőrző tesztek fejlesztése:** Az adatbázis-migrálás tesztelése SQL-lekérdezéseket kell használnia. Létre kell hoznia az érvényesítési lekérdezéseket, hogy a forrás- és a céladatbázison is fusson. Az érvényesítési lekérdezések a megadott hatókört fedik le.

1. **Tesztkörnyezet beállítása: A** tesztkörnyezetnek tartalmaznia kell a forrásadatbázis és a céladatbázis másolatát. Mindenképpen különítse el a tesztkörnyezetet.

1. **Ellenőrző tesztek futtatása:** Érvényesítési tesztek futtatása a forráson és a célon, majd az eredmények elemzése.

1. **Teljesítményttesztek futtatása:** Futtatassa a teljesítményteszteket a forráson és a célon, majd elemezze és hasonlítsa össze az eredményeket.


### <a name="optimize"></a>Optimalizálás

A migrálás utáni fázis elengedhetetlen az adatpontossági problémák egyeztetéséhez, a teljesség ellenőrzéséhez és a számítási feladat teljesítményével kapcsolatos problémák megoldásához.

Ezekről a problémákról és a megoldásukhoz szükséges lépésekről a migrálás utáni ellenőrzési és optimalizálási [útmutatóban található további információ.](/sql/relational-databases/post-migration-validation-and-optimization-guide)


## <a name="next-steps"></a>Következő lépések

- A Különböző adatbázis- és adatáttelepítési forgatókönyvekben és speciális feladatokban segítő Microsoft- és külső szolgáltatások és eszközök mátrixát lásd: Szolgáltatások és eszközök [adatáttelepítéshez.](../../../dms/dms-tools-matrix.md)

- További információ a Azure SQL Database:
   - [A SQL Database](../../database/sql-database-paas-overview.md)
   - [Az Azure teljes tulajdonlási költségének kalkulátora](https://azure.microsoft.com/pricing/tco/calculator/)  

- A felhőbe irányuló migrálás keretrendszerének és bevezetésének ciklusát a következő cikkből tudja meg:
   -  [Felhőbevezetési keretrendszer az Azure-hoz](/azure/cloud-adoption-framework/migrate/azure-best-practices/contoso-migration-scale)
   -  [Ajánlott költségszámítási és méretezési eljárások számítási feladatokhoz az Azure-ba való migráláshoz](/azure/cloud-adoption-framework/migrate/azure-best-practices/migrate-best-practices-costs) 
   -  [Cloud Migration erőforrások](https://azure.microsoft.com/migration/resources)

- Az alkalmazás-hozzáférési réteg felmérése: Data Access Migration Toolkit [(előzetes verzió)](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit).
- Az A/B adatelérési réteg tesztelésének részleteiért lásd: [Database Experimentation Assistant.](/sql/dea/database-experimentation-assistant-overview)
