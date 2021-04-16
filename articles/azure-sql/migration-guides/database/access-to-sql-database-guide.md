---
title: 'Hozzáférés a Azure SQL Database: Áttelepítési útmutató'
description: Ebből az útmutatóból megtudhatja, hogyan mi egyik Azure SQL-adatbázisba mi egyik Microsoft Access-adatbázist a SQL Server Migration Assistant for Access (SSMA for Access) használatával.
ms.service: sql-database
ms.subservice: migration-guide
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: MashaMSFT
ms.author: mathoma
ms.date: 03/19/2021
ms.openlocfilehash: 137adbb045a4c449193f9029b9c72f09ddc439b1
ms.sourcegitcommit: aa00fecfa3ad1c26ab6f5502163a3246cfb99ec3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2021
ms.locfileid: "107388465"
---
# <a name="migration-guide-access-to-azure-sql-database"></a>Migrálási útmutató: Hozzáférés a Azure SQL Database

Ebből az útmutatóból [](https://azure.microsoft.com/migration/migration-journey) megtudhatja, hogyan migrálható Microsoft Access-adatbázisa egy Azure SQL-adatbázisba a SQL Server Migration Assistant for Access (SSMA for [Access)](https://azure.microsoft.com/en-us/migration/sql-server/) használatával.

További migrálási útmutatókért lásd az [Azure Database migrálási útmutatóját.](https://docs.microsoft.com/data-migration) 

## <a name="prerequisites"></a>Előfeltételek

Az Access-adatbázis SQL-adatbázisba való áttelepítésének megkezdése előtt tegye a következőket:

- Ellenőrizze, hogy a forráskörnyezet támogatott-e. 
- Töltse le és telepítse [a SQL Server Migration Assistant az Accesshez.](https://www.microsoft.com/download/details.aspx?id=54255)
- Ellenőrizze, hogy rendelkezik-e kapcsolattal, és rendelkezik-e a forráshoz és a célhoz való hozzáféréshez szükséges engedélyekkel.

## <a name="pre-migration"></a>A migrálás előtt

Az előfeltételek teljesültével készen áll arra, hogy felfedezze a környezet topológiáját, és felmérje az Azure-felhőbe való [migrálás megvalósíthatóságát.](https://azure.microsoft.com/migration)


### <a name="assess"></a>Kiértékelés 

Az SSMA for Access használatával áttekintheti az adatbázis-objektumokat és adatokat, és kiértékelheti az adatbázisokat az áttelepítéshez. 

Értékelés létrehozásához tegye a következőket: 

1. Nyissa meg [az SSMA-t a hozzáféréshez.](https://www.microsoft.com/download/details.aspx?id=54255) 
1. Válassza **a File (Fájl)** lehetőséget, majd a **New Project (Új projekt) lehetőséget.** 
1. Adja meg a projekt nevét és helyét, majd a legördülő listában válassza a Azure SQL Database **lehetőséget** a migrálási célként. 
1. Válassza az **OK** lehetőséget. 

   ![Képernyőkép a migrálási projekt nevének és helyének megadásával kapcsolatos "Új projekt" panelről.](./media/access-to-sql-database-guide/new-project.png)

1. Válassza **az Adatbázisok hozzáadása** lehetőséget, majd válassza ki az új projekthez hozzáadni kívánt adatbázisokat. 

   ![Képernyőkép az SSMA for Access "Adatbázisok hozzáadása" lapról.](./media/access-to-sql-database-guide/add-databases.png)

1. A **Metaadat-kezelő elérése panelen** kattintson a jobb gombbal egy adatbázisra, majd válassza a **Jelentés létrehozása lehetőséget.** Másik lehetőségként válassza a Jelentés létrehozása **lapot** a jobb felső sarokban.

   ![Az Access Metadata Explorer "Jelentés létrehozása" parancsának képernyőképe.](./media/access-to-sql-database-guide/create-report.png)

1. Tekintse át a HTML-jelentést a konverziós statisztikák, valamint az esetleges hibák és figyelmeztetések áttekintéséhez. A jelentést az Excelben is megnyithatja, hogy leltárt kap a Hozzáférési objektumokról, és megértse a sémakonverziók végrehajtásához szükséges munkát. A jelentés alapértelmezett helye az SSMAProjects jelentésmappában található. Például:

   `drive:\<username>\Documents\SSMAProjects\MyAccessMigration\report\report_<date>`

   ![Képernyőkép egy példaadatbázis-jelentés értékeléséről az SSMA-ban.](./media/access-to-sql-database-guide/sample-assessment.png)

### <a name="validate-the-data-types"></a>Az adattípusok ellenőrzése

Ellenőrizze az alapértelmezett adattípus-leképezéseket, és szükség esetén módosítsa őket a követelmények alapján. Ehhez tegye a következőket:

1. Az SSMA for Accessben válassza a **Tools (Eszközök)** lehetőséget, majd a **Project Settings (Projektbeállítások) lehetőséget.** 
1. Válassza a **Típusleképezés lapot.** 

   ![Képernyőkép az SSMA "Típusleképezés" panelről a hozzáféréshez.](./media/access-to-sql-database-guide/type-mappings.png)

1. Az egyes táblák típusleképezését úgy módosíthatja, hogy kiválasztja a táblázat nevét az **Access Metadata Explorer (Metaadat-kezelés elérése) panelen.**


### <a name="convert-the-schema"></a>A séma konvertálása

Az adatbázis-objektumok konvertálásához tegye a következőket: 

1. Válassza a **Csatlakozás Azure SQL Database** lapot, majd tegye a következőket:

   a. Adja meg az SQL-adatbázishoz való csatlakozás részleteit.  
   b. A legördülő listában válassza ki a célKÉNT kiválasztott SQL-adatbázist. Vagy új nevet is beírhat, amely esetben a rendszer létrehoz egy adatbázist a célkiszolgálón.  
   c. Adja meg a hitelesítési adatokat.   
   d. Válassza a **Kapcsolódás** lehetőséget.

   ![Képernyőkép a "Csatlakozás Azure SQL Database" panelről a kapcsolatadatok bevitelére.](./media/access-to-sql-database-guide/connect-to-sqldb.png)

1. Az **Access Metadata Explorer (Metaadat-kezelő elérése) panelen** kattintson a jobb gombbal az adatbázisra, majd válassza a **Convert Schema (Séma konvertálása) lehetőséget.** Másik lehetőségként kiválaszthatja az adatbázist, majd a Séma **átalakítása lapot.**

   ![Képernyőkép a "Séma konvertálása" parancsról a "Metaadat-kezelés elérése" panelen.](./media/access-to-sql-database-guide/convert-schema.png)

1. Az átalakítás befejezése után hasonlítsa össze a konvertált objektumokat az eredeti objektumokkal a lehetséges problémák azonosítása érdekében, és a javaslatok alapján megoldja a problémákat.

   ![A konvertált objektumok és a forrásobjektumok összehasonlítását bemutató képernyőkép.](./media/access-to-sql-database-guide/table-comparison.png)

    Hasonlítsa össze a konvertált Transact-SQL szöveget az eredeti kóddal, és tekintse át a javaslatokat.

   ![A konvertált lekérdezések és a forráskód összehasonlítását bemutató képernyőkép.](./media/access-to-sql-database-guide/query-comparison.png) 

1. (Nem kötelező) Egyéni objektum konvertálásához kattintson a jobb gombbal az objektumra, majd válassza a **Séma átalakítása lehetőséget.** A konvertált objektumok félkövér szövegben jelennek meg az **Access Metadata Explorerben:** 

   ![Képernyőkép az Access Metadata Explorer objektumai átalakításáról.](./media/access-to-sql-database-guide/converted-items.png)
 
1. A Kimenet **panelen** válassza az **Eredmények** áttekintése ikont, és tekintse át a hibákat a **Hibalista panelen.** 
1. Mentse helyileg a projektet egy offline séma-szervizelési gyakorlathoz. Válassza a Fájl **Projekt**  >  **mentése lehetőséget.** Ez lehetővé teszi a forrás- és célsémák offline kiértékelését és szervizelését, mielőtt közzétenné őket az SQL-adatbázisban.

## <a name="migrate-the-databases"></a>Adatbázisok áttelepítése

Az adatbázisok értékelése és az eltérések elhárítása után futtathatja a migrálási folyamatot. Az adatok áttelepítése egy tömeges betöltési művelet, amely az adatsorokat tranzakciók Azure SQL egy adatbázisba. Az SQL-adatbázisba az egyes tranzakciók során betölthető sorok száma a projektbeállításokban van konfigurálva.

A séma közzétételéhez és az adatok az SSMA for Access használatával való áttelepítéséhez tegye a következőket: 

1. Ha még nem tette meg, válassza a Csatlakozás a Azure SQL Database lehetőséget, **és** adja meg a kapcsolati adatokat. 

1. Tegye közzé a sémát. A Azure SQL Database **Metaadat-kezelő panelen** kattintson a jobb gombbal arra az adatbázisra, amelyvel dolgozik, majd válassza a **Szinkronizálás adatbázissal lehetőséget.** Ez a művelet közzéteszi a MySQL-sémát az SQL-adatbázisban.

1. A Szinkronizálás **az adatbázissal panelen** tekintse át a forrásprojekt és a cél közötti leképezést:

   ![Képernyőkép a "Szinkronizálás az adatbázissal" panelről az adatbázissal való szinkronizálás áttekintésére.](./media/access-to-sql-database-guide/synchronize-with-database-review.png)

1. Az **Access Metadata Explorer (Metaadat-kezelő** elérése) panelen jelölje be a jelölőnégyzeteket az átemelni kívánt elemek mellett. A teljes adatbázis áttelepítéshez jelölje be az adatbázis melletti jelölőnégyzetet. 

1. Az adatok áttelepítése. Kattintson a jobb gombbal az átemelni kívánt adatbázisra vagy objektumra, majd válassza az **Adatok áttelepítése lehetőséget.** Másik lehetőségként a jobb felső **sarokban** található Adatok áttelepítése lapot is választhatja.  

   Egy teljes adatbázis adatainak áttelepítéséhez jelölje be az adatbázis neve melletti jelölőnégyzetet. Az adatok egyes táblákból való áttelepítéséhez bontsa ki az adatbázist, bontsa ki a **Táblák** gombra, majd jelölje be a tábla melletti jelölőnégyzetet. Az egyes táblák adatainak kihagyása érdekében törölje a jelölést.

    ![Képernyőkép az "Adatok áttelepítése" parancsról az "Access Metadata Explorer" (Metaadat-kezelő elérése) panelen.](./media/access-to-sql-database-guide/migrate-data.png)

1. Az áttelepítés befejezése után tekintse meg az **adatáttelepítési jelentést.**  

    ![Képernyőkép az "Adatjelentés áttelepítése" panelről, amely egy áttekintésre példajelentést mutat be.](./media/access-to-sql-database-guide/migrate-data-review.png)

1. Csatlakozzon a Azure SQL adatbázisához az [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms)használatával, és ellenőrizze a migrálást az adatok és a séma áttekintésének segítségével.

   ![Képernyőkép az SQL Server Management Studio Object Explorer SSMA-ban való migrálást érvényesítő folyamatról.](./media/access-to-sql-database-guide/validate-data.png)

## <a name="post-migration"></a>A migrálás után 

Miután sikeresen befejezte a migrálási szakaszt, több migrálás utáni feladatot kell elvégeznie annak érdekében, hogy minden a lehető zökkenőmentesen és leghatékonyabban működjön. 

### <a name="remediate-applications"></a>Alkalmazások szervize

Az adatok célkörnyezetbe való migrálása után az összes olyan alkalmazásnak, amely korábban a forrást felhasználta, el kell kezdenie a cél fogyasztását. Ennek megvalósítása bizonyos esetekben az alkalmazások módosításait igényli.

### <a name="perform-tests"></a>Tesztek végrehajtása

Az adatbázis-migrálás tesztelési megközelítése a következő tevékenységekből áll:

1. **Ellenőrző tesztek fejlesztése:** Az adatbázis-migrálás tesztelésére SQL-lekérdezéseket kell használnia. Létre kell hoznia az érvényesítési lekérdezéseket, hogy a forrás- és a céladatbázison is fusson. Az érvényesítési lekérdezések a megadott hatókört fedik le.

1. **Tesztkörnyezet beállítása:** A tesztkörnyezetnek tartalmaznia kell a forrásadatbázis és a céladatbázis másolatát. Mindenképpen különítse el a tesztkörnyezetet.

1. **Ellenőrző tesztek futtatása:** Érvényesítési tesztek futtatása a forráson és a célon, majd az eredmények elemzése.

1. **Teljesítményttesztek futtatása:** Futtatassa a teljesítményteszteket a forráson és a célon, majd elemezze és hasonlítsa össze az eredményeket.


### <a name="optimize"></a>Optimalizálás

A migrálás utáni fázis elengedhetetlen az adatpontossági problémák egyeztetéséhez, a teljesség ellenőrzéséhez és a számítási feladat teljesítményével kapcsolatos problémák megoldásához.

Ezekről a problémákról és a megoldásuk lépéseiről a migrálás utáni ellenőrzési és optimalizálási [útmutatóban található további információ.](/sql/relational-databases/post-migration-validation-and-optimization-guide)

## <a name="migration-assets"></a>Migrálási eszközök 

A migrálási forgatókönyv elvégzésével kapcsolatos további segítségért tekintse meg a következő forrást. Egy valós migrálási projekt támogatásaként lett kifejlesztve.

| Cím | Leírás |
| --- | --- |
| [Adatterhelés-értékelési modell és eszköz](https://github.com/Microsoft/DataMigrationTeam/tree/master/Data%20Workload%20Assessment%20Model%20and%20Tool) | Ajánlott "legmegfelelőbb" célplatformokat, felhőalapú készenlétet és alkalmazás-/adatbázis-szervizelési szinteket biztosít a megadott számítási feladatokhoz. Egyszerű, egykattintásos számítást és jelentés-készítést kínál, amely automatizált, egységes célplatform-döntési folyamat biztosításával segít felgyorsítani a nagy tulajdonnal kapcsolatos értékeléseket. |

Ezeket az erőforrásokat az SQL mérnöki csapata fejlesztette ki. Ennek a csapatnak az alapvető csomagja a Microsoft Azure-adatplatformjára irányuló adatplatform-migrálási projektek komplex modernizálásának feloldása és felgyorsítása.

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
- További információ az A/B adatelérési réteg tesztelésének elvégzéséről: [Overview of Database Experimentation Assistant](/sql/dea/database-experimentation-assistant-overview).
