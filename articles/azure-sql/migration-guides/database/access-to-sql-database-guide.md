---
title: 'Hozzáférés a Azure SQL Databasehoz: áttelepítési útmutató'
description: Ebből az útmutatóból megtudhatja, hogyan migrálhatja a Microsoft Access-adatbázisokat egy Azure SQL Database-adatbázisba SQL Server Migration Assistant használatával (hozzáférés SSMA).
ms.service: sql-database
ms.subservice: migration-guide
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MashaMSFT
ms.author: mathoma
ms.date: 03/19/2021
ms.openlocfilehash: 73ee4d4be16284880b10df4a52b422a08c04c6a0
ms.sourcegitcommit: c6a2d9a44a5a2c13abddab932d16c295a7207d6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/09/2021
ms.locfileid: "107284187"
---
# <a name="migration-guide-access-to-azure-sql-database"></a>Áttelepítési útmutató: hozzáférés a Azure SQL Databasehoz

Ebből az útmutatóból megtudhatja, [hogyan telepítheti át](https://azure.microsoft.com/migration/migration-journey) Microsoft Access-adatbázisát egy Azure SQL Database-adatbázisba [SQL Server áttelepítési](https://azure.microsoft.com/en-us/migration/sql-server/) asszisztenssel (SSMA) való hozzáféréshez.

Más áttelepítési útmutatókért lásd: az [Azure Database áttelepítési útmutatója](https://docs.microsoft.com/data-migration). 

## <a name="prerequisites"></a>Előfeltételek

Az Access-adatbázis SQL-adatbázisba való áttelepítésének megkezdése előtt tegye a következőket:

- Ellenőrizze, hogy a forrás-környezet támogatott-e. 
- Töltse le és telepítse [a SQL Server Migration Assistantt a hozzáféréshez](https://www.microsoft.com/download/details.aspx?id=54255).
- Győződjön meg arról, hogy rendelkezik kapcsolattal és megfelelő engedélyekkel a forrás és a cél eléréséhez.

## <a name="pre-migration"></a>A migrálás előtt

Az előfeltételek teljesítése után már készen áll a környezet topológiájának felderítésére és az Azure-beli [Felhőbeli áttelepítés](https://azure.microsoft.com/migration)megvalósíthatóságának felmérésére.


### <a name="assess"></a>Kiértékelés 

A SSMA használatával tekintheti át az adatbázis-objektumokat és az adatforrásokat, és értékelheti az adatbázisok áttelepítését. 

Az értékelés létrehozásához tegye a következőket: 

1. Nyissa meg [a SSMA a hozzáféréshez](https://www.microsoft.com/download/details.aspx?id=54255). 
1. Válassza a **fájl**, majd az **új projekt** lehetőséget. 
1. Adja meg a projekt nevét és helyét a projekthez, majd a legördülő listában válassza a **Azure SQL Database** lehetőséget az áttelepítési célként. 
1. Válassza az **OK** lehetőséget. 

   ![Képernyőkép az "új projekt" panelről az áttelepítési projekt nevének és helyének megadásához.](./media/access-to-sql-database-guide/new-project.png)

1. Válassza az **adatbázisok hozzáadása** lehetőséget, majd válassza ki az új projekthez hozzáadni kívánt adatbázisokat. 

   ![Képernyőkép az "adatbázisok hozzáadása" lapról a SSMA alkalmazásban.](./media/access-to-sql-database-guide/add-databases.png)

1. Az **Access metadata Explorer** ablaktáblán kattintson a jobb gombbal az adatbázisra, majd válassza a **jelentés létrehozása** lehetőséget. Azt is megteheti, hogy a jobb felső sarokban a **jelentés létrehozása** lapot választja.

   ![Képernyőkép a "jelentés létrehozása" parancsról az Access metadata Explorerben.](./media/access-to-sql-database-guide/create-report.png)

1. A HTML-jelentés áttekintésével megismerheti az átalakítási statisztikákat és az esetleges hibákat vagy figyelmeztetéseket. A jelentést az Excelben is megnyitva beolvashatja a hozzáférési objektumok leltárát, és megtudhatja, milyen erőfeszítést kell végrehajtani a séma átalakításához. A jelentés alapértelmezett helye a SSMAProjects belüli jelentési mappában található. Például:

   `drive:\<username>\Documents\SSMAProjects\MyAccessMigration\report\report_<date>`

   ![Képernyőkép egy példa adatbázis-jelentés értékeléséről a SSMA-ben.](./media/access-to-sql-database-guide/sample-assessment.png)

### <a name="validate-the-data-types"></a>Az adattípusok ellenőrzése

Érvényesítse az alapértelmezett adattípus-leképezéseket, és szükség esetén módosítsa azokat a követelmények alapján. Ehhez tegye a következőket:

1. A SSMA területen válassza az **eszközök**, majd a **projekt beállításai** lehetőséget. 
1. Válassza a **típus leképezése** fület. 

   ![Képernyőkép a SSMA a "típus leképezése" panelről a hozzáféréshez.](./media/access-to-sql-database-guide/type-mappings.png)

1. Az egyes táblák típus-hozzárendelését úgy módosíthatja, hogy a **hozzáférési metaadatok Explorer** ablaktábláján kiválasztja a tábla nevét.


### <a name="convert-the-schema"></a>A séma konvertálása

Az adatbázis-objektumok átalakításához tegye a következőket: 

1. Kattintson a **kapcsolódás Azure SQL Database** lapra, majd tegye a következőket:

   a. Adja meg az SQL-adatbázishoz való csatlakozás részleteit.  
   b. A legördülő listában válassza ki a cél SQL-adatbázist. Vagy megadhat egy új nevet is, amely esetben a rendszer létrehoz egy adatbázist a célkiszolgálón.  
   c. Adja meg a hitelesítés részleteit.   
   d. Válassza a **Kapcsolódás** lehetőséget.

   ![A kapcsolat részleteinek megadásához a "kapcsolódás Azure SQL Databasehoz" panel képernyőképe.](./media/access-to-sql-database-guide/connect-to-sqldb.png)

1. Az **Access metadata Explorer** ablaktáblán kattintson a jobb gombbal az adatbázisra, majd válassza a **séma konvertálása** lehetőséget. Azt is megteheti, hogy kijelöli az adatbázist, majd kiválasztja a **séma konvertálása** lapot.

   ![Képernyőkép a "séma konvertálása" parancsról a "hozzáférés a metaadat-kezelőhöz" panelen.](./media/access-to-sql-database-guide/convert-schema.png)

1. Az átalakítás befejezése után hasonlítsa össze a konvertált objektumokat az eredeti objektumokkal a lehetséges problémák azonosítása érdekében, és a javaslatok alapján foglalkozzon a problémákkal.

   ![Képernyőfelvétel: a konvertált objektumok összehasonlítása a forrásoldali objektumokkal.](./media/access-to-sql-database-guide/table-comparison.png)

    Hasonlítsa össze a konvertált Transact-SQL-szöveget az eredeti kóddal, és tekintse át a javaslatokat.

   ![Az átalakított lekérdezések a forráskódhoz viszonyított összehasonlítását ábrázoló képernyőkép.](./media/access-to-sql-database-guide/query-comparison.png) 

1. Választható Egy adott objektum konvertálásához kattintson a jobb gombbal az objektumra, majd válassza a **séma konvertálása** lehetőséget. A konvertált objektumok félkövér szövegben jelennek meg az **Access metadata Explorerben**: 

   ![Képernyőfelvétel: az Access metadata Explorer objektumainak konvertálása.](./media/access-to-sql-database-guide/converted-items.png)
 
1. A **kimenet** ablaktáblán válassza az **eredmények áttekintése** ikont, és tekintse át a hibákat a hibák **listája** ablaktáblán. 
1. Mentse a projektet helyileg a kapcsolat nélküli séma szervizelési gyakorlatához. Ehhez válassza a **fájl**  >  **Mentés projekt** lehetőséget. Ez lehetőséget nyújt arra, hogy kiértékelje a forrás-és a célként megadott sémákat offline állapotba, és szervizelést végezzen, mielőtt közzéteszi őket az SQL Database-ben.

## <a name="migrate-the-databases"></a>Az adatbázisok migrálása

Miután elvégezte az adatbázisok értékelését és az eltérések megcímzését, futtathatja az áttelepítési folyamatot. Az adatáttelepítés egy tömeges betöltési művelet, amely az adatsorokat egy Azure SQL Database-adatbázisba helyezi át tranzakciókban. Az SQL-adatbázisba az egyes tranzakciókban betöltendő sorok száma a projekt beállításaiban van konfigurálva.

Ha közzé szeretné tenni a sémát, és az SSMA használatával kívánja áttelepíteni az adatait, tegye a következőket: 

1. Ha még nem tette meg, válassza **a kapcsolódás Azure SQL Database** lehetőséget, és adja meg a kapcsolat részleteit. 

1. Tegye közzé a sémát. A **Azure SQL Database metadata Explorer** ablaktáblán kattintson a jobb gombbal arra az adatbázisra, amelyen dolgozik, majd válassza az **adatbázissal való szinkronizálás** lehetőséget. Ez a művelet közzéteszi a MySQL-sémát az SQL Database-ben.

1. A **szinkronizálás az adatbázissal** ablaktáblán tekintse át a forrás projekt és a cél közötti leképezést:

   ![Képernyőkép: "szinkronizálás az adatbázissal" ablaktábla a szinkronizálás az adatbázissal való áttekintéséhez.](./media/access-to-sql-database-guide/synchronize-with-database-review.png)

1. Az **Access metadata Explorer** ablaktáblán jelölje be az áttelepíteni kívánt elemek melletti jelölőnégyzeteket. Ha a teljes adatbázist át szeretné telepíteni, jelölje be az adatbázis melletti jelölőnégyzetet. 

1. Telepítse át az adatátvitelt. Kattintson a jobb gombbal az áttelepíteni kívánt adatbázisra vagy objektumra, majd válassza az **adatáttelepítés** lehetőséget. Másik lehetőségként kiválaszthatja az **adatáttelepítés** fület a jobb felső sarokban.  

   Ha egy teljes adatbázisra szeretné áttelepíteni az adatátvitelt, jelölje be az adatbázis neve melletti jelölőnégyzetet. Ha az adatok áttelepíthetők az egyes táblákból, bontsa ki az adatbázist, majd a **táblák** csomópontot, és jelölje be a tábla melletti jelölőnégyzetet. Ha az adatok kihagyása az egyes táblákból, törölje a jelölőnégyzet jelölését.

    ![Képernyőkép az "adatok átmigrálása" parancsról a "hozzáférés a metaadat-kezelőhöz" panelen.](./media/access-to-sql-database-guide/migrate-data.png)

1. Az áttelepítés befejezése után tekintse meg az **adatáttelepítési jelentést**.  

    ![Képernyőkép az "Adatjelentés átmigrálása" panelről, amely egy példaként szolgáló jelentést mutat be véleményezésre.](./media/access-to-sql-database-guide/migrate-data-review.png)

1. Kapcsolódjon az Azure SQL Database-hez [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms)használatával, és ellenőrizze az áttelepítést az adatelemzés és a séma áttekintésével.

   ![Képernyőkép a SQL Server Management Studio Object Explorerról a SSMA-ben történő áttelepítés ellenőrzéséhez.](./media/access-to-sql-database-guide/validate-data.png)

## <a name="post-migration"></a>A migrálás után 

Az *áttelepítési* fázis sikeres befejezése után el kell végeznie az áttelepítést követő feladatok sorozatát, hogy a lehető leggördülékenyebb és hatékony működést biztosítsa.

### <a name="remediate-applications"></a>Alkalmazások szervizelése

Miután áttelepítette az adatátvitelt a cél környezetbe, az összes olyan alkalmazásnak, amely korábban használta a forrást, el kell kezdenie a cél felhasználását. Ennek elvégzése bizonyos esetekben szükségessé teszi az alkalmazások módosítását.

### <a name="perform-tests"></a>Tesztek végrehajtása

Az adatbázis-áttelepítés tesztelési módszere a következő tevékenységekből áll:

1. **Ellenőrzési tesztek fejlesztése**: az adatbázis áttelepítésének teszteléséhez SQL-lekérdezéseket kell használnia. Létre kell hoznia az érvényesítési lekérdezéseket, amelyek a forrás-és a célként megadott adatbázison futnak. Az érvényesítési lekérdezéseknek le kell fedniük a definiált hatókört.

1. **Tesztkörnyezet beállítása**: a tesztkörnyezet a forrás-adatbázis és a céladatbázis másolatát is tartalmazza. Ügyeljen arra, hogy elkülönítse a tesztkörnyezet.

1. **Ellenőrzési tesztek futtatása**: ellenőrző tesztek futtatása a forráson és a célon, majd az eredmények elemzése.

1. **Teljesítménytesztek futtatása**: futtasson teljesítményteszteket a forráson és a célon, majd elemezze és hasonlítsa össze az eredményeket.


### <a name="optimize"></a>Optimalizálás

Az áttelepítés utáni fázis elengedhetetlen az adatok pontosságával kapcsolatos problémák összeegyeztetéséhez, a teljesség ellenőrzéséhez és a teljesítménnyel kapcsolatos problémák kezeléséhez.

További információ ezekről a problémákról és az azok enyhítésének lépéseiről: [áttelepítés utáni érvényesítés és optimalizálási útmutató](/sql/relational-databases/post-migration-validation-and-optimization-guide).

## <a name="migration-assets"></a>Áttelepítési eszközök 

Az áttelepítési forgatókönyv végrehajtásával kapcsolatos további segítségért tekintse meg a következő erőforrást. A fejlesztést egy valós idejű migrációs projekt keretében hozták létre.

| Cím | Leírás |
| --- | --- |
| [Adatmunkaterhelés-felmérési modell és eszköz](https://github.com/Microsoft/DataMigrationTeam/tree/master/Data%20Workload%20Assessment%20Model%20and%20Tool) | A a megadott számítási feladatokhoz javasolt "legmegfelelőbb" cél platformot, a felhő készültségét, valamint az alkalmazások/adatbázisok szervizelési szintjét biztosítja. Egyszerű, egykattintásos számítást és jelentéskészítési lehetőséget kínál, amely lehetővé teszi a nagyméretű ingatlanok értékelésének gyorsítását egy automatizált, egységes cél-platform döntési folyamat biztosításával. |

Az adatsql mérnöki csapat fejlesztette ezeket az erőforrásokat. A csapat alapszintű alapokmánya az adatplatform-áttelepítési projektek a Microsoft Azure-beli adatplatformra való feltiltásának és felgyorsításának feloldása.

## <a name="next-steps"></a>Következő lépések

- A Microsoft és a harmadik féltől származó szolgáltatások és eszközök egy olyan mátrixa, amely a különböző adatbázis-és adatáttelepítési forgatókönyvek és speciális feladatok ellátásához nyújt segítséget, lásd: [szolgáltatás és eszközök az adatok áttelepítéséhez](../../../dms/dms-tools-matrix.md).

- Ha többet szeretne megtudni Azure SQL Database lásd:
   - [A SQL Database áttekintése](../../database/sql-database-paas-overview.md)
   - [Az Azure teljes tulajdonlási költsége kalkulátor](https://azure.microsoft.com/pricing/tco/calculator/) 


- Ha többet szeretne megtudni a Felhőbeli Migrálás keretrendszeréről és bevezetési ciklusáról, tekintse meg a következőt:
   -  [Felhőbevezetési keretrendszer az Azure-hoz](/azure/cloud-adoption-framework/migrate/azure-best-practices/contoso-migration-scale)
   -  [Ajánlott eljárások az Azure-ba való Migrálás díjszabásához és méretezéséhez](/azure/cloud-adoption-framework/migrate/azure-best-practices/migrate-best-practices-costs) 
   -  [Erőforrások Cloud Migration](https://azure.microsoft.com/migration/resources)


- Az alkalmazás-hozzáférési réteg értékeléséhez lásd: [adathozzáférés áttelepítési eszközkészlete (előzetes verzió)](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit).
- További információ az A/B típusú adatelérési réteg végrehajtásáról: [Database Experimentation Assistant áttekintése](/sql/dea/database-experimentation-assistant-overview).
