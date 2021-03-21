---
title: 'Oracle – SQL Database: áttelepítési útmutató'
description: Ez az útmutató bemutatja, hogyan telepítheti át Oracle-sémáját Azure SQL Database az Oracle-SQL Server Migration Assistant (SSMA for Oracle) használatával.
ms.service: sql-database
ms.subservice: migration-guide
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MashaMSFT
ms.author: mathoma
ms.date: 08/25/2020
ms.openlocfilehash: f00740de5a327858fd250a0cb561b07c32f3b726
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "104655488"
---
# <a name="migration-guide-oracle-to-azure-sql-database"></a>Áttelepítési útmutató: Oracle – Azure SQL Database
[!INCLUDE[appliesto-sqldb-sqlmi](../../includes/appliesto-sqldb.md)]

Ez az útmutató bemutatja, hogyan migrálhatja Oracle-sémáit Azure SQL Database az Oracle-SQL Server Migration Assistant használatával.

Más áttelepítési útmutatókért lásd: [adatbázis-áttelepítés](https://datamigration.microsoft.com/). 

## <a name="prerequisites"></a>Előfeltételek

Az Oracle-séma átSQL Databaseéhez a következőkre lesz szüksége: 

- Annak ellenőrzéséhez, hogy a forrás-környezet támogatott-e. 
- Az [Oracle SQL Server Migration Assistant (SSMA)](https://www.microsoft.com/en-us/download/details.aspx?id=54258)letöltése. 
- A cél [Azure SQL Database](../../database/single-database-create-quickstart.md). 
- Az Oracle és a [Provider](/sql/ssma/oracle/connect-to-oracle-oracletosql) [SSMA szükséges engedélyei](/sql/ssma/oracle/connecting-to-oracle-database-oracletosql) .
 

## <a name="pre-migration"></a>A migrálás előtt

Az előfeltételek teljesítése után készen áll a környezet topológiájának felderítésére és az áttelepítés megvalósíthatóságának értékelésére. A folyamat ezen része magában foglalja az áttelepíteni kívánt adatbázisok leltárának elvégzését, a lehetséges áttelepítési problémák vagy blokkolók kiértékelését, valamint az esetlegesen feltárt elemek feloldását.



### <a name="assess"></a>Kiértékelés 


Az Oracle SQL Server Migration Assistant (SSMA) segítségével áttekintheti az adatbázis-objektumokat és-adatforrásokat, kiértékelheti az áttelepítéshez szükséges adatbázisokat, áttelepítheti az adatbázis-Azure SQL Database objektumokat, és végül áttelepítheti az adatbázisba. 


Az értékelés létrehozásához kövesse az alábbi lépéseket: 


1. [Az Oracle SQL Server Migration Assistant](https://www.microsoft.com/en-us/download/details.aspx?id=54258)megnyitása. 
1. Válassza a **fájl** , majd az **új projekt** lehetőséget. 
1. Adja meg a projekt nevét, a kívánt helyet a projekt mentéséhez, majd válassza a Azure SQL Database az áttelepítési célként lehetőséget a legördülő menüből. Válassza az **OK** lehetőséget.

   ![Új projekt](./media/oracle-to-sql-database-guide/new-project.png)


1. Válassza **a Kapcsolódás az Oracle-hoz** lehetőséget. Adja meg az Oracle-kapcsolat adatainak értékét a **Kapcsolódás az Oracle** -hez párbeszédpanelen.

   ![Kapcsolódás az Oracle-hoz](./media/oracle-to-sql-database-guide/connect-to-oracle.png)

   Válassza ki az áttelepíteni kívánt Oracle-sémát (ka) t: 

   ![Oracle-séma kiválasztása](./media/oracle-to-sql-database-guide/select-schema.png)

1. Kattintson a jobb gombbal az áttelepíteni kívánt Oracle-sémára az **Oracle metadata Explorerben**, majd válassza a **jelentés létrehozása** lehetőséget. Ekkor létrejön egy HTML-jelentés. Azt is megteheti, hogy az adatbázis kiválasztása után kiválasztja a **jelentés létrehozása** lehetőséget a navigációs sávon.

   ![Jelentés létrehozása](./media/oracle-to-sql-database-guide/create-report.png)

1. A HTML-jelentés áttekintésével megismerheti a konverziós statisztikákat és az esetleges hibákat vagy figyelmeztetéseket. A jelentést az Excelben is megnyithatja, hogy beolvassa az Oracle-objektumok leltárát, valamint a séma átalakításának végrehajtásához szükséges erőfeszítést. A jelentés alapértelmezett helye a SSMAProjects belüli jelentési mappában található.

   Például: `drive:\<username>\Documents\SSMAProjects\MyOracleMigration\report\report_2020_11_12T02_47_55\`

   ![Értékelő jelentés](./media/oracle-to-sql-database-guide/assessment-report.png) 



### <a name="validate-data-types"></a>Adattípusok ellenőrzése

Érvényesítse az alapértelmezett adattípus-leképezéseket, és szükség esetén módosítsa azokat a követelmények alapján. Ehhez kövesse az alábbi lépéseket:

1. Válassza az **eszközök** lehetőséget a menüből. 
1. Válassza a **projekt beállításai** lehetőséget. 
1. Válassza a **típus-hozzárendelések** lapot. 

   ![Típus-hozzárendelések](./media/oracle-to-sql-database-guide/type-mappings.png)

1. Az egyes táblák típus-hozzárendelését az **Oracle metadata Explorerben** található táblázat kiválasztásával módosíthatja.

### <a name="convert-schema"></a>Séma konvertálása

A séma konvertálásához kövesse az alábbi lépéseket: 

1. Választható Dinamikus vagy alkalmi lekérdezéseket adhat az utasításokhoz. Kattintson a jobb gombbal a csomópontra, majd válassza az **utasítások hozzáadása** parancsot.
1. Válassza **a kapcsolódás Azure SQL Database** lehetőséget. 
    1. Adja meg a kapcsolat adatait az adatbázis Azure SQL Database-ben való összekapcsolásához.
    1. Válassza ki a cél SQL Database a legördülő menüből.
    1. Válassza a **Kapcsolódás** lehetőséget.

    ![Csatlakozás SQL Database-adatbázishoz](./media/oracle-to-sql-database-guide/connect-to-sql-database.png)


1. Kattintson a jobb gombbal az Oracle-sémára az **Oracle metadata Explorerben** , majd válassza a **séma konvertálása** parancsot. Azt is megteheti, hogy a séma kiválasztása után kiválasztja a **séma konvertálása** lehetőséget a felső navigációs sávon.

   ![Séma konvertálása](./media/oracle-to-sql-database-guide/convert-schema.png)

1. Az átalakítás befejezése után hasonlítsa össze és tekintse át az átalakított objektumokat az eredeti objektumokra a lehetséges problémák azonosítása érdekében, és a javaslatok alapján foglalkozzon velük.

   ![Az ajánlások sémájának áttekintése](./media/oracle-to-sql-database-guide/table-mapping.png)

   Hasonlítsa össze a konvertált Transact-SQL-szöveget az eredeti tárolt eljárásokkal, és tekintse át a javaslatokat. 

   ![Javaslatok áttekintése](./media/oracle-to-sql-database-guide/procedure-comparison.png)

1. Mentse a projektet helyileg a kapcsolat nélküli séma szervizelési gyakorlatához. Válassza a **projekt mentése** lehetőséget a **fájl** menüből.

## <a name="migrate"></a>Migrate

Miután elvégezte az adatbázisok értékelését és az eltérések kezelését, a következő lépés az áttelepítési folyamat végrehajtása. Az áttelepítés két lépést tesz szükségessé – a séma közzétételét és az adatáttelepítést. 

A séma közzétételéhez és az adatáttelepítés elvégzéséhez kövesse az alábbi lépéseket:

1. Tegye közzé a sémát: kattintson a jobb gombbal az adatbázisra az **Azure SQL Database metadata Explorer** **adatbázisok** csomópontjában, majd válassza az **adatbázissal való szinkronizálás** lehetőséget.

   ![Szinkronizálás adatbázissal](./media/oracle-to-sql-database-guide/synchronize-with-database.png)

   Tekintse át a forrás projekt és a cél közötti leképezést:

   ![Szinkronizálás adatbázis-ellenőrzéssel](./media/oracle-to-sql-database-guide/synchronize-with-database-review.png)


1. Migrálja az adatokat: kattintson a jobb gombbal a sémára az **Oracle metadata Explorerben** , és válassza az **adatok áttelepíteni** lehetőséget. Választhatja azt is, hogy az **adatok áttelepíthetők** a felső vonal navigációs sávján a séma kiválasztása után. 

   ![Az adatáttelepítés](./media/oracle-to-sql-database-guide/migrate-data.png)

1. Adja meg az Oracle és a Azure SQL Database kapcsolati adatait.
1. Tekintse meg az **adatáttelepítési jelentést**.

   ![Adatáttelepítési jelentés](./media/oracle-to-sql-database-guide/data-migration-report.png)

1. Kapcsolódjon a Azure SQL Databasehoz a [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) használatával, és ellenőrizze az áttelepítést az adatelemzés és a séma áttekintésével.

   ![Érvényesítés a SSMA](./media/oracle-to-sql-database-guide/validate-data.png)

Azt is megteheti, hogy a SQL Server Integration Services (SSIS) használatával hajtja végre az áttelepítést. További információ: 

- [SQL Server Migration Assistant: a nem a Microsofttól származó adatplatformokról származó adatok felmérése és áttelepítése SQL Server](https://blogs.msdn.microsoft.com/datamigration/2016/11/16/sql-server-migration-assistant-how-to-assess-and-migrate-databases-from-non-microsoft-data-platforms-to-sql-server/)
- [Első lépések a SQL Server Integration Services](https://docs.microsoft.com/sql/integration-services/sql-server-integration-services)
- [SQL Server Integration Services: SSIS az Azure-hoz és a hibrid adatáthelyezéshez](https://download.microsoft.com/download/D/2/0/D20E1C5F-72EA-4505-9F26-FEF9550EFD44/SSIS%20Hybrid%20and%20Azure.docx)


## <a name="post-migration"></a>A migrálás után 

Miután sikeresen elvégezte az **áttelepítési** szakaszt, át kell haladnia az áttelepítés utáni feladatok sorozatán, hogy minden a lehető legzökkenőmentesen és hatékonyan működjön.

### <a name="remediate-applications"></a>Alkalmazások szervizelése

Miután áttelepítette az adatátvitelt a cél környezetbe, az összes olyan alkalmazásnak, amely korábban használta a forrást, el kell kezdenie a cél felhasználását. Ennek elvégzése bizonyos esetekben szükségessé teszi az alkalmazások módosítását.

Az [adathozzáférés áttelepítési eszközkészlete](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit) egy olyan bővítmény a Visual Studio Code-hoz, amely lehetővé teszi a Java-forráskód elemzését és az adatelérési API-hívások és lekérdezések észlelését, és az új adatbázis-háttér támogatásához szükséges egyetlen ablaktáblából álló nézetet biztosít. További információ: [Java-alkalmazás migrálása az Oracle](https://techcommunity.microsoft.com/t5/microsoft-data-migration/migrate-your-java-applications-from-oracle-to-sql-server-with/ba-p/368727) blogból. 



### <a name="perform-tests"></a>Tesztek végrehajtása

Az adatbázis-áttelepítés tesztelési módszere a következő tevékenységek elvégzését tartalmazza:

1.  **Ellenőrzési tesztek fejlesztése**. Az adatbázis-áttelepítés teszteléséhez SQL-lekérdezéseket kell használnia. Létre kell hoznia az érvényesítési lekérdezéseket a forrás-és a célként megadott adatbázisokon való futtatáshoz. Az érvényesítési lekérdezéseknek le kell fedniük a definiált hatókört.

2.  **Tesztkörnyezet beállítása**. A tesztkörnyezet a forrás-adatbázis és a céladatbázis másolatát is tartalmazza. Ügyeljen arra, hogy elkülönítse a tesztkörnyezet.

3.  **Ellenőrzési tesztek futtatása**. Futtassa az ellenőrző teszteket a forráson és a célon, majd elemezze az eredményeket.

4.  **Teljesítménytesztek futtatása**. Futtasson teljesítménytesztet a forráson és a célon, majd elemezze és hasonlítsa össze az eredményeket.


### <a name="optimize"></a>Optimalizálás

Az áttelepítés utáni fázis elengedhetetlen az adatok pontosságával kapcsolatos problémák összeegyeztetéséhez és a teljesség ellenőrzéséhez, valamint a számítási feladatok teljesítményével kapcsolatos problémák kezeléséhez.

> [!NOTE]
> További információ ezekről a problémákról és az azok enyhítésére szolgáló konkrét lépésekről: [áttelepítés utáni érvényesítés és optimalizálási útmutató](/sql/relational-databases/post-migration-validation-and-optimization-guide).


## <a name="migration-assets"></a>Áttelepítési eszközök 

Ha további segítségre van az áttelepítési forgatókönyv végrehajtásával kapcsolatban, tekintse meg a következő forrásokat, amelyek a valós idejű migrációs projektek támogatásában lettek kifejlesztve.

| **Cím/hivatkozás**                                                                                                                                          | **Leírás**                                                                                                                                                                                                                                                                                                                                                                                       |
| ------------------------------------------------------------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [Adatmunkaterhelés-felmérési modell és eszköz](https://github.com/Microsoft/DataMigrationTeam/tree/master/Data%20Workload%20Assessment%20Model%20and%20Tool) | Ez az eszköz a javasolt "legmegfelelőbb" cél platformot, a felhő készültségét, valamint az alkalmazások/adatbázisok szervizelési szintjét biztosítja egy adott munkaterhelés esetében. Egyszerű, egykattintásos számítási és jelentéskészítési lehetőséget kínál, amely nagy mértékben segíti a nagyméretű ingatlanok értékelését azáltal, hogy biztosítja és automatizálja a célzott platformra vonatkozó döntési folyamatokat.                                                          |
| [Oracle Inventory parancsfájl-összetevők](https://github.com/Microsoft/DataMigrationTeam/tree/master/Oracle%20Inventory%20Script%20Artifacts)                 | Ez az objektum olyan PL/SQL-lekérdezést tartalmaz, amely megkeresi az Oracle rendszertáblázatokat, és az objektumok számát a séma típusa, az Objektumtípus és az állapot szerint adja meg. Emellett az egyes sémákban a "nyers adatmennyiség", valamint az egyes sémákban lévő táblázatok méretezése is durva becslést ad, a CSV-formátumban tárolt eredményekkel együtt.                                                                                                               |
| [SSMA Oracle Assessment Collection &-összevonás automatizálása](https://github.com/microsoft/DataMigrationTeam/tree/master/IP%20and%20Scripts/Automate%20SSMA%20Oracle%20Assessment%20Collection%20%26%20Consolidation)                                             | Ez az erőforrás egy. csv-fájlt használ belépésként (sources.csv a projekt mappáiban) a SSMA-értékelés konzolos módban való futtatásához szükséges XML-fájlok létrehozásához. A source.csv az ügyfél a meglévő Oracle-példányok leltára alapján kapja meg. A kimeneti fájlok AssessmentReportGeneration_source_1.xml, ServersConnectionFile.xml és VariableValueFile.xml.|
| [SSMA általános Oracle-hibákhoz és azok javításához](https://aka.ms/dmj-wp-ssma-oracle-errors)                                                           | Az Oracle használatával a WHERE záradékban nem skaláris feltételt rendelhet hozzá. A SQL Server azonban nem támogatja ezt a típusú feltételt. Ennek eredményeképpen az Oracle SQL Server Migration Assistant (SSMA) nem konvertálja a WHERE záradékban nem skaláris feltétellel rendelkező lekérdezéseket, hanem hiba-O2SS0001 generál. Ez a tanulmány további részleteket tartalmaz a problémáról és annak megoldási módjairól.          |
| [Oracle – SQL Server áttelepítési kézikönyv](https://github.com/microsoft/DataMigrationTeam/blob/master/Whitepapers/Oracle%20to%20SQL%20Server%20Migration%20Handbook.pdf)                | Ez a dokumentum az Oracle-sémák a SQL Server Base legújabb verziójára való áttelepítésével kapcsolatos feladatokra koncentrál. Ha az áttelepítéshez a funkciók/funkciók módosítása szükséges, akkor az adatbázist használó alkalmazások változásainak lehetséges hatását körültekintően kell figyelembe venni.                                                     |

Ezek az erőforrások az Azure adatcsoport-mérnöki csapat által szponzorált adatsql ninja program részeként lettek kifejlesztve. Az adatelemzési program alapszintű alapokmánya az, hogy feloldja az összetett modernizációt, és az adatplatform-migrációs lehetőségeket a Microsoft Azure-beli adatplatformján is felgyorsítja. Ha úgy gondolja, hogy a szervezete szeretne részt venni az adatsql ninja programban, forduljon a fiókhoz, és kérje meg, hogy küldje el a jelölést.

## <a name="next-steps"></a>Következő lépések

- A Microsoft és a harmadik féltől származó szolgáltatások és eszközök egy olyan mátrixa, amely a különböző adatbázis-és adatáttelepítési forgatókönyvek, valamint a speciális feladatok elvégzéséhez nyújt segítséget, tekintse meg a cikk [szolgáltatás és eszközök az adatok áttelepítéséhez](https://docs.microsoft.com/azure/dms/dms-tools-matrix)című témakört.

- A Azure SQL Databaseról további információt a következő témakörben talál: 
  - [A Azure SQL Database áttekintése](../../database/sql-database-paas-overview.md)
  - [Azure teljes tulajdonlási költség (TCO) – kalkulátor](https://azure.microsoft.com/en-us/pricing/tco/calculator/)


- Ha többet szeretne megtudni a Felhőbeli Migrálás keretrendszerével és bevezetési ciklusával kapcsolatban, olvassa el a következőt:
   -  [Felhőbevezetési keretrendszer az Azure-hoz](/azure/cloud-adoption-framework/migrate/azure-best-practices/contoso-migration-scale)
   -  [Ajánlott eljárások az Azure-ra való áttéréssel kapcsolatos díjszabási és méretezési feladatok elvégzéséhez](/azure/cloud-adoption-framework/migrate/azure-best-practices/migrate-best-practices-costs) 

- A videó tartalma: 
    - [Az áttelepítési út és a szükséges eszközök/szolgáltatások áttekintése az értékelés és a Migrálás elvégzéséhez](https://azure.microsoft.com/resources/videos/overview-of-migration-and-recommended-tools-services/)


