---
title: 'Oracle to Azure SQL Database: Migrálási útmutató'
description: Ebből az útmutatóból megtudhatja, hogyan minálható az Oracle-séma Azure SQL Database az Oracle SQL Server Migration Assistant használatával.
ms.service: sql-database
ms.subservice: migration-guide
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: MashaMSFT
ms.author: mathoma
ms.date: 08/25/2020
ms.openlocfilehash: 45fbc1f85c5d7f66716fbf69deb430ce74575435
ms.sourcegitcommit: aa00fecfa3ad1c26ab6f5502163a3246cfb99ec3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2021
ms.locfileid: "107388482"
---
# <a name="migration-guide-oracle-to-azure-sql-database"></a>Migrálási útmutató: Oracle–Azure SQL Database

[!INCLUDE[appliesto-sqldb-sqlmi](../../includes/appliesto-sqldb.md)]

Ebből az útmutatóból [](https://azure.microsoft.com/migration/migration-journey) megtudhatja, hogyan migrálható Oracle-sémái Azure SQL Database az [SQL Server Migration](https://azure.microsoft.com/en-us/migration/sql-server/) Assistant for Oracle (SSMA for Oracle) használatával.

További migrálási útmutatókért lásd az [Azure Database migrálási útmutatóját.](https://docs.microsoft.com/data-migration)

## <a name="prerequisites"></a>Előfeltételek

Mielőtt megkezdi az Oracle-séma áttelepítését a SQL Database:

- Ellenőrizze, hogy a forráskörnyezet támogatott-e.
- Töltse le [az SSMA for Oracle-t.](https://www.microsoft.com/download/details.aspx?id=54258)
- Célpéldány [SQL Database](../../database/single-database-create-quickstart.md) kell.
- Szerezze be [az Oracle-hez és a szolgáltatóhoz szükséges SSMA-engedélyeket.](/sql/ssma/oracle/connecting-to-oracle-database-oracletosql) [](/sql/ssma/oracle/connect-to-oracle-oracletosql)
 
## <a name="pre-migration"></a>A migrálás előtt

Az előfeltételek teljesültével készen áll arra, hogy felfedezze a környezet topológiáját, és felmérje az Azure-felhőbe való [migrálás megvalósíthatóságát.](https://azure.microsoft.com/migration) A folyamat ezen része magában foglalja a migrálni szükséges adatbázisok leltárának elvégzését, az adatbázisok lehetséges migrálási problémák vagy blokkok felmérését, majd az esetleg felderített elemek megoldását.

### <a name="assess"></a>Kiértékelés

Az Oracle-hez használható SSMA használatával áttekintheti az adatbázis-objektumokat és -adatokat, kiértékelheti az adatbázisokat az áttelepítéshez, migrálhatja az adatbázis-objektumokat az SQL Database-be, majd végül migrálhatja az adatokat az adatbázisba.

Értékelés létrehozása:

1. Nyissa meg [az SSMA for Oracle-t.](https://www.microsoft.com/download/details.aspx?id=54258)
1. Válassza **a File (Fájl)** lehetőséget, majd a **New Project (Új projekt) lehetőséget.**
1. Adja meg a projekt nevét és a projekt mentésének helyét. Ezután válassza **Azure SQL Database** migrálási célként lehetőséget a legördülő listából, majd kattintson az **OK gombra.**

   ![Az Oracle-hez való csatlakozást bemutató képernyőkép.](./media/oracle-to-sql-database-guide/connect-to-oracle.png)

1. Válassza **a Csatlakozás oracle-hez lehetőséget.** A Csatlakozás Oracle-hez párbeszédpanelen adja meg az **Oracle-kapcsolat részleteinek** értékeit.

1. Válassza ki az átemelni kívánt Oracle-sémákat.

   ![Képernyőkép az Oracle-séma kiválasztásáról.](./media/oracle-to-sql-database-guide/select-schema.png)

1. Az **Oracle Metadata Explorerben** kattintson a jobb gombbal az átemelni kívánt Oracle-sémára, majd válassza a **Jelentés** létrehozása lehetőséget egy HTML-jelentés létrehozásához. Másik lehetőségként kiválaszthat egy adatbázist, majd a Jelentés **létrehozása lapot.**

   ![A Jelentés létrehozása képernyőképe.](./media/oracle-to-sql-database-guide/create-report.png)

1. Tekintse át a HTML-jelentést a konverziós statisztikák, valamint az esetleges hibák és figyelmeztetések áttekintéséhez. A jelentést az Excelben is megnyithatja, hogy leltárt kap az Oracle-objektumokról és a sémakonverziók végrehajtásához szükséges munkamennyiségről. A jelentés alapértelmezett helye az SSMAProjects jelentésmappában található.

   Lásd például: `drive:\<username>\Documents\SSMAProjects\MyOracleMigration\report\report_2020_11_12T02_47_55\`.

   ![Képernyőkép egy értékelési jelentésről.](./media/oracle-to-sql-database-guide/assessment-report.png)

### <a name="validate-the-data-types"></a>Az adattípusok ellenőrzése

Ellenőrizze az alapértelmezett adattípus-leképezéseket, és szükség esetén módosítsa őket a követelmények alapján. Ehhez kövesse az alábbi lépéseket:

1. Az SSMA for Oracle-ban válassza az **Eszközök,** majd a **Projektbeállítások lehetőséget.**
1. Válassza a **Típusleképezés** lapot.

   ![A Típusleképezést bemutató képernyőkép.](./media/oracle-to-sql-database-guide/type-mappings.png)

1. Az egyes tábla típusleképezését az Oracle Metadata Explorer táblázatának **kiválasztásával módosíthatja.**

### <a name="convert-the-schema"></a>A séma konvertálása

A séma konvertálása:

1. (Nem kötelező) Dinamikus vagy alkalmi lekérdezések hozzáadása utasításokhoz. Kattintson a jobb gombbal a csomópontra, majd válassza az **Utasítások hozzáadása lehetőséget.**
1. Válassza a **Csatlakozás Azure SQL Database** lapot.
    1. A **SQL Database** adja meg a kapcsolati adatokat az adatbázis csatlakoztatásához.
    1. Válassza ki SQL Database-példányt a legördülő listából, vagy adjon meg egy új nevet, amely esetben létrejön egy adatbázis a célkiszolgálón.
    1. Adja meg a hitelesítési adatokat, majd válassza a **Csatlakozás lehetőséget.**

    ![Képernyőkép a Csatlakozás Azure SQL Database.](./media/oracle-to-sql-database-guide/connect-to-sql-database.png)

1. Az **Oracle Metadata Explorerben** kattintson a jobb gombbal az Oracle-sémára, majd válassza a **Séma konvertálása lehetőséget.** Másik lehetőségként kiválaszthatja a sémát, majd a Séma **konvertálása** lapot.

   ![A Séma konvertálása képernyőképe.](./media/oracle-to-sql-database-guide/convert-schema.png)

1. Az átalakítás befejezése után hasonlítsa össze és tekintse át az eredeti objektumokká konvertált objektumokat, hogy azonosítsa a lehetséges problémákat, és a javaslatok alapján megoldja őket.

   ![A Javaslatok áttekintése sémát bemutató képernyőkép.](./media/oracle-to-sql-database-guide/table-mapping.png)

1. Hasonlítsa össze a konvertált Transact-SQL szöveget az eredeti tárolt eljárásokkal, és tekintse át a javaslatokat.

   ![A javaslatok áttekintését bemutató képernyőkép.](./media/oracle-to-sql-database-guide/procedure-comparison.png)

1. A kimeneti panelen válassza az **Eredmények** áttekintése lehetőséget, és tekintse át a hibákat a **Hibalista panelen.**
1. Mentse helyileg a projektet egy offline séma-szervizelési gyakorlathoz. A Fájl **menüben** válassza a **Projekt mentése lehetőséget.** Ez a lépés lehetővé teszi a forrás- és célsémák offline kiértékelését és szervizelését, mielőtt közzétenné a sémát a SQL Database.

## <a name="migrate"></a>Migrate

Az adatbázisok felmérése és az eltérések elhárítása után a következő lépés a migrálási folyamat futtatása. A migrálás két lépésből áll: a séma közzétételéből és az adatok migrálásból.

A séma közzététele és az adatok áttelepítése:

1. Tegye közzé a sémát úgy,  hogy a metaadat-kezelő Adatbázis csomópontján a jobb **gombbal** az adatbázisra Azure SQL Database, majd a **Szinkronizálás adatbázissal gombra kattint.**

   ![A Szinkronizálás adatbázissal képernyőképe.](./media/oracle-to-sql-database-guide/synchronize-with-database.png)

1. Tekintse át a forrásprojekt és a cél közötti leképezést.

   ![A Szinkronizálás az adatbázissal felülvizsgálatot bemutató képernyőkép.](./media/oracle-to-sql-database-guide/synchronize-with-database-review.png)

1. Az adatok áttelepítéséhez kattintson a jobb gombbal az Oracle **Metadata Explorerben** átemelni kívánt adatbázisra vagy objektumra, majd válassza az **Adatok áttelepítése gombra.** Másik lehetőségként választhatja az Adatok **áttelepítése lapot** is. Egy teljes adatbázis adatainak áttelepítéséhez jelölje be az adatbázis neve melletti jelölőnégyzetet. Az adatok egyes táblákból való áttelepítéséhez bontsa ki az adatbázist, bontsa ki a **Táblák** gombra, majd jelölje be a táblák melletti jelölőnégyzeteket. Az egyes táblák adatainak kihagyása érdekében törölje a jelölőnégyzeteket.

   ![Az Adatok áttelepítése képernyőképe.](./media/oracle-to-sql-database-guide/migrate-data.png)

1. Adja meg az Oracle és a SQL Database.
1. Az áttelepítés befejezése után tekintse meg az **adatáttelepítési jelentést.**

   ![Az adatáttelepítési jelentést bemutató képernyőkép.](./media/oracle-to-sql-database-guide/data-migration-report.png)

1. Csatlakozzon a SQL Database-példányhoz [az SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms)használatával, és ellenőrizze a migrálást az adatok és a séma áttekintésének segítségével.

   ![Képernyőkép az ellenőrzésről a SQL Server Management Studio.](./media/oracle-to-sql-database-guide/validate-data.png)

Másik lehetőségként használhatja a SQL Server Integration Services is az áttelepítés végrehajtásához. További információ:

- [Ismerkedés a SQL Server Integration Services](/sql/integration-services/sql-server-integration-services)
- [SQL Server Integration Services Azure-hoz és a hibrid adatátmozgatáshoz](https://download.microsoft.com/download/D/2/0/D20E1C5F-72EA-4505-9F26-FEF9550EFD44/SSIS%20Hybrid%20and%20Azure.docx)

## <a name="post-migration"></a>A migrálás után

Miután sikeresen befejezte a migrálási szakaszt, több migrálás utáni feladatot kell elvégeznie annak érdekében, hogy minden a lehető zökkenőmentesen és leghatékonyabban működjön. 

### <a name="remediate-applications"></a>Alkalmazások szervize

Az adatok célkörnyezetbe való migrálása után az összes olyan alkalmazásnak, amely korábban a forrást felhasználta, el kell kezdenie a cél fogyasztását. Ennek a feladatnak a végrehajtásához bizonyos esetekben az alkalmazásokat is meg kell változtatni.

A [Data Access Migration Toolkit](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit) az Visual Studio Code bővítménye, amely lehetővé teszi a Java-forráskód elemzését és az adatelérési API-hívások és -lekérdezések észlelését. Az eszközkészlet egy egypaneles nézetet biztosít arról, hogy mit kell kezelni az új adatbázis-háttérrendszer támogatásához. További tudnivalókért tekintse meg a [Java-alkalmazások](https://techcommunity.microsoft.com/t5/microsoft-data-migration/migrate-your-java-applications-from-oracle-to-sql-server-with/ba-p/368727) Oracle-ről való áttelepítésével kapcsolatos blogbejegyzést.

### <a name="perform-tests"></a>Tesztek végrehajtása

Az adatbázis-migrálás tesztelési megközelítése a következő tevékenységekből áll:

1. **Ellenőrző tesztek fejlesztése:** Az adatbázis-migrálás tesztelése SQL-lekérdezéseket kell használnia. Létre kell hoznia az érvényesítési lekérdezéseket, hogy a forrás- és a céladatbázison is fusson. Az érvényesítési lekérdezések a megadott hatókört fedik le.
1. **Tesztkörnyezet beállítása: A** tesztkörnyezetnek tartalmaznia kell a forrásadatbázis és a céladatbázis másolatát. Mindenképpen különítse el a tesztkörnyezetet.
1. **Ellenőrző tesztek futtatása:** Érvényesítési tesztek futtatása a forráson és a célon, majd az eredmények elemzése.
1. **Teljesítményttesztek futtatása:** Futtatassa a teljesítményteszteket a forráson és a célon, majd elemezze és hasonlítsa össze az eredményeket.

### <a name="optimize"></a>Optimalizálás

A migrálás utáni fázis elengedhetetlen az adatpontossági problémák egyeztetéséhez, a teljesség ellenőrzéséhez és a számítási feladat teljesítményével kapcsolatos problémák megoldásához.

> [!NOTE]
> Ezekről a problémákról és a megoldásukhoz szükséges lépésekről a migrálás utáni ellenőrzési és optimalizálási [útmutatóban található további információ.](/sql/relational-databases/post-migration-validation-and-optimization-guide)

## <a name="migration-assets"></a>Migrálási eszközök

A migrálási forgatókönyv elvégzésével kapcsolatos további segítségért tekintse meg a következő forrásokat. Egy valós migrálási projekt támogatásaként fejlesztették ki őket.

| **Cím/hivatkozás**                                                                                                                                          | **Leírás**                                                                                                                                                                                                                                                                                                                                                                                       |
| ------------------------------------------------------------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [Adatterhelés-értékelési modell és eszköz](https://github.com/Microsoft/DataMigrationTeam/tree/master/Data%20Workload%20Assessment%20Model%20and%20Tool) | Ez az eszköz javasolt "legmegfelelőbb" célplatformokat, felhőalapú készenlétet és alkalmazás- vagy adatbázis-szervizelési szintet biztosít egy adott számítási feladathoz. Egyszerű, egykattintásos számítást és jelentés-készítést kínál, amely automatizált és egységes célplatform-döntési folyamat biztosításával segít felgyorsítani a nagy tulajdonfelméréseket.                                                          |
| [Oracle Inventory Script Artifacts](https://github.com/Microsoft/DataMigrationTeam/tree/master/Oracle%20Inventory%20Script%20Artifacts)                 | Ez az objektum tartalmaz egy PL/SQL-lekérdezést, amely az Oracle rendszertábláiból ad vissza találatokat, és sématípus, objektumtípus és állapot szerint adja meg az objektumok számát. Emellett megbecsüli az egyes sémák nyers adatait, valamint az egyes sémák tábláinak méretezését CSV formátumban tárolt eredményekkel.                                                                                                               |
| [Az SSMA Oracle Assessment Collection összevonásának & automatizálása](https://github.com/microsoft/DataMigrationTeam/tree/master/IP%20and%20Scripts/Automate%20SSMA%20Oracle%20Assessment%20Collection%20%26%20Consolidation)                                             | Ez az erőforráskészlet egy .csv-fájlt használ bejegyzésként (sources.csv a projektmappákban) az SSMA-értékelés konzol módban való futtatásához szükséges XML-fájlok előállításához. A source.csv az ügyfél a meglévő Oracle-példányok leltára alapján biztosítja. A kimeneti fájlok AssessmentReportGeneration_source_1.xml, ServersConnectionFile.xml és VariableValueFile.xml.|
| [Az SSMA for Oracle gyakori hibái és javítása](https://aka.ms/dmj-wp-ssma-oracle-errors)                                                           | Az Oracle-val nem skálázható feltételt rendelhet hozzá a WHERE záradékban. A SQL Server azonban nem támogatja ezt a feltételtípust. Ennek eredményeképpen az Orákulumhoz való SSMA nem konvertálja a WHERE záradék nem skálázható feltételű lekérdezéseit. Ehelyett az O2SS0001 hibát generálja. Ez a tanulmány további részleteket tartalmaz a problémáról és annak megoldási módjairól.          |
| [Oracle to SQL Server Migration Handbook](https://github.com/microsoft/DataMigrationTeam/blob/master/Whitepapers/Oracle%20to%20SQL%20Server%20Migration%20Handbook.pdf)                | Ez a dokumentum az Oracle-sémák a database legújabb verziójára való áttelepítésével kapcsolatos SQL Server összpontosít. Ha a migráláshoz módosítani kell a funkciókat vagy a funkciókat, az egyes módosításoknak az adatbázist kezelő alkalmazásokra gyakorolt lehetséges hatását alaposan meg kell vizsgálni.                                                     |

Ezeket az erőforrásokat az SQL mérnöki csapata fejlesztette ki. Ennek a csapatnak az alapvető csomagja a Microsoft Azure-adatplatformjára irányuló adatplatform-migrálási projektek összetett modernizálásának feloldására és felgyorsítására szolgál.

## <a name="next-steps"></a>Következő lépések

- A Különböző adatbázis- és adatáttelepítési forgatókönyvekben és speciális feladatokban segítő Microsoft- és külső szolgáltatások és eszközök mátrixát lásd: Szolgáltatások és eszközök [adatáttelepítéshez.](../../../dms/dms-tools-matrix.md)

- További információ a SQL Database:
  - [A Azure SQL Database](../../database/sql-database-paas-overview.md)
  - [Azure teljes tulajdonlási költség (TCO) kalkulátora](https://azure.microsoft.com/pricing/tco/calculator/)

- A felhőbe való migrálás keretrendszerének és bevezetésének ciklusa további tudnivalókért lásd:
   -  [Felhőbevezetési keretrendszer az Azure-hoz](/azure/cloud-adoption-framework/migrate/azure-best-practices/contoso-migration-scale)
   -  [Ajánlott költségszámítási és méretezési eljárások számítási feladatokhoz az Azure-ba való migráláshoz](/azure/cloud-adoption-framework/migrate/azure-best-practices/migrate-best-practices-costs)
   -  [Cloud Migration erőforrások](https://azure.microsoft.com/migration/resources)

- Videótartalmakért lásd:
    - [A migrálási folyamat áttekintése, valamint a felméréshez és migráláshoz javasolt eszközök és szolgáltatások](https://azure.microsoft.com/resources/videos/overview-of-migration-and-recommended-tools-services/)
