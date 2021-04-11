---
title: 'Oracle – Azure SQL felügyelt példány: áttelepítési útmutató'
description: Ebből az útmutatóból megtudhatja, hogyan telepíthet Oracle-sémákat az Azure SQL felügyelt példányaira az Oracle SQL Server Migration Assistant használatával.
ms.service: sql-managed-instance
ms.subservice: migration-guide
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: mokabiru
ms.author: mokabiru
ms.reviewer: MashaMSFT
ms.date: 11/06/2020
ms.openlocfilehash: 2bb019a692178c5b44c3589d401d3b2b34c3dccb
ms.sourcegitcommit: b0557848d0ad9b74bf293217862525d08fe0fc1d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/07/2021
ms.locfileid: "106553906"
---
# <a name="migration-guide-oracle-to-azure-sql-managed-instance"></a>Áttelepítési útmutató: Oracle – Azure SQL felügyelt példány

[!INCLUDE[appliesto-sqldb-sqlmi](../../includes/appliesto-sqlmi.md)]

Ebből az útmutatóból megtudhatja, hogyan migrálhatja Oracle-sémáit az Azure SQL felügyelt példányaira az Oracle-hez készült SQL Server Migration Assistant használatával (SSMA for Oracle).

Más áttelepítési útmutatókért lásd: az [Azure Database áttelepítési útmutatói](https://docs.microsoft.com/data-migration).

## <a name="prerequisites"></a>Előfeltételek

Mielőtt megkezdené az Oracle-séma áttelepítését az SQL felügyelt példányára:

- Ellenőrizze, hogy a forrás-környezet támogatott-e.
- [Az Oracle SSMA](https://www.microsoft.com/en-us/download/details.aspx?id=54258)letöltése.
- [SQL felügyelt példány](../../managed-instance/instance-create-quickstart.md) célját kell használni.
- Szerezze be az Oracle és a [Provider](/sql/ssma/oracle/connect-to-oracle-oracletosql) [SSMA szükséges engedélyeit](/sql/ssma/oracle/connecting-to-oracle-database-oracletosql) .
 
## <a name="pre-migration"></a>A migrálás előtt

Az előfeltételek teljesítése után készen áll a környezet topológiájának felderítésére és az áttelepítés megvalósíthatóságának értékelésére. A folyamat ezen része magában foglalja az áttelepíteni kívánt adatbázisok leltárának elvégzését, a lehetséges áttelepítési problémák vagy blokkolók kiértékelését, valamint az esetlegesen feltárt elemek feloldását.

### <a name="assess"></a>Kiértékelés

Az Oracle-SSMA használatával áttekintheti az adatbázis-objektumokat és-adatforrásokat, kiértékelheti az áttelepítéshez használt adatbázisokat, áttelepítheti az adatbázis-objektumokat a felügyelt példányokra, majd végül áttelepítheti az adatbázisba

Értékelés létrehozása:

1. [Az Oracle SSMA](https://www.microsoft.com/en-us/download/details.aspx?id=54258)megnyitása.
1. Válassza a **fájl**, majd az **új projekt** lehetőséget.
1. Adja meg a projekt nevét és helyét a projekt mentéséhez. Ezután válassza az **Azure SQL felügyelt példány** áttelepítési célként lehetőséget a legördülő listából, majd kattintson az **OK gombra**.

   ![Képernyőkép, amely az új projektet mutatja.](./media/oracle-to-managed-instance-guide/new-project.png)

1. Válassza **a Kapcsolódás az Oracle-hoz** lehetőséget. Adja meg az Oracle-kapcsolat részleteinek értékét a **Kapcsolódás az Oracle** -hez párbeszédpanelen.

   ![Az Oracle-hez való kapcsolódást bemutató képernyőkép.](./media/oracle-to-managed-instance-guide/connect-to-oracle.png)

1. Válassza ki az áttelepíteni kívánt Oracle-sémákat.

   ![Képernyőkép, amely az Oracle-séma kiválasztását mutatja.](./media/oracle-to-managed-instance-guide/select-schema.png)

1. Az **Oracle metadata Explorerben** kattintson a jobb gombbal az áttelepíteni kívánt Oracle-sémára, majd válassza a **jelentés létrehozása** lehetőséget HTML-jelentés létrehozásához. Azt is megteheti, hogy kijelöl egy adatbázist, majd kiválasztja a **jelentés létrehozása** fület.

   ![Képernyőfelvétel: jelentés létrehozása.](./media/oracle-to-managed-instance-guide/create-report.png)

1. A HTML-jelentés áttekintésével megismerheti a konverziós statisztikákat és az esetleges hibákat vagy figyelmeztetéseket. A jelentést az Excelben is megnyithatja, hogy beolvassa az Oracle-objektumok leltárát, valamint a séma átalakításának végrehajtásához szükséges erőfeszítést. A jelentés alapértelmezett helye a SSMAProjects belüli jelentési mappában található.

   Lásd például: `drive:\<username>\Documents\SSMAProjects\MyOracleMigration\report\report_2020_11_12T02_47_55\`.

   ![Az értékelési jelentést bemutató képernyőkép.](./media/oracle-to-managed-instance-guide/assessment-report.png)

### <a name="validate-the-data-types"></a>Az adattípusok ellenőrzése

Érvényesítse az alapértelmezett adattípus-leképezéseket, és szükség esetén módosítsa azokat a követelmények alapján. Ehhez kövesse az alábbi lépéseket:

1. Az Oracle SSMA válassza az **eszközök**, majd a **projekt beállításai** elemet.
1. Válassza a **típus leképezése** fület.

   ![A típus leképezését bemutató képernyőkép.](./media/oracle-to-managed-instance-guide/type-mappings.png)

1. Az egyes táblák típus-hozzárendelését úgy módosíthatja, hogy kijelöli a táblázatot az **Oracle metadata Explorerben**.

### <a name="convert-the-schema"></a>A séma konvertálása

A séma átalakítása:

1. Választható Dinamikus vagy alkalmi lekérdezéseket adhat az utasításokhoz. Kattintson a jobb gombbal a csomópontra, majd válassza az **utasítások hozzáadása** lehetőséget.
1. Válassza a **Kapcsolódás az Azure SQL felügyelt példányhoz** lapot.
    1. Adja meg a kapcsolat adatait az adatbázis **SQL Database felügyelt példányban** való összekapcsolásához.
    1. Válassza ki a célként szolgáló adatbázist a legördülő listából, vagy adjon meg egy új nevet, amely esetben a rendszer létrehoz egy adatbázist a célkiszolgálón.
    1. Adja meg a hitelesítés részleteit, és válassza a **kapcsolat** lehetőséget.

    ![A felügyelt Azure SQL-példányhoz való kapcsolódást bemutató képernyőkép.](./media/oracle-to-managed-instance-guide/connect-to-sql-managed-instance.png)

1. Az **Oracle metadata Explorerben** kattintson a jobb gombbal az Oracle-sémára, majd válassza a **séma konvertálása** lehetőséget. Azt is megteheti, hogy kijelöli a sémát, majd kiválasztja a **séma konvertálása** lapot.

   ![A konvertálási sémát bemutató képernyőkép.](./media/oracle-to-managed-instance-guide/convert-schema.png)

1. Az átalakítás befejeződése után hasonlítsa össze és tekintse át az átalakított objektumokat az eredeti objektumokra a lehetséges problémák azonosítása érdekében, és a javaslatok alapján foglalkozzon velük.

   ![Képernyőkép, amely a táblázatokkal kapcsolatos javaslatok összehasonlítását mutatja be.](./media/oracle-to-managed-instance-guide/table-comparison.png)

1. Hasonlítsa össze a konvertált Transact-SQL-szöveget az eredeti kóddal, és tekintse át a javaslatokat.

   ![Képernyőkép, amely az eljárások összehasonlítását mutatja.](./media/oracle-to-managed-instance-guide/procedure-comparison.png)

1. A kimenet ablaktáblán válassza az **eredmények áttekintése** lehetőséget, és tekintse át a hibákat a **hibalista** ablaktáblán.
1. Mentse a projektet helyileg a kapcsolat nélküli séma szervizelési gyakorlatához. A **fájl** menüben válassza a **projekt mentése** elemet. Ezzel a lépéssel kiértékelheti a forrás-és a célként megadott sémákat offline állapotba, és szervizelést hajthat végre a séma SQL felügyelt példányra való közzététele előtt.

## <a name="migrate"></a>Migrate

Miután elvégezte az adatbázisok értékelését és az eltérések kezelését, a következő lépés az áttelepítési folyamat futtatása. Az áttelepítés két lépést tesz szükségessé: a séma közzétételét és az adatáttelepítést.

A séma közzétételéhez és az adatáttelepítéshez:
1. A séma közzétételéhez kattintson a jobb gombbal az adatbázisra az **Azure SQL felügyelt példányának metaadatok Explorer** **adatbázis csomópontjában** , és válassza az **adatbázissal való szinkronizálás** lehetőséget.

   ![Képernyőkép, amely az adatbázissal való szinkronizálást mutatja.](./media/oracle-to-managed-instance-guide/synchronize-with-database.png)
   

1. Tekintse át a forrás projekt és a cél közötti leképezést.

   ![Képernyőkép, amely az adatbázis-felülvizsgálattal való szinkronizálást mutatja.](./media/oracle-to-managed-instance-guide/synchronize-with-database-review.png)

1. Telepítse át az adatokat úgy, hogy a jobb gombbal az **Oracle metadata Explorerben** áttelepíteni kívánt sémára vagy objektumra kattint, és kiválasztja az **adatok áttelepíteni** lehetőséget. Azt is megteheti, hogy kijelöli az **adatáttelepítés** fület. Ha egy teljes adatbázisra szeretné áttelepíteni az adatátvitelt, jelölje be az adatbázis neve melletti jelölőnégyzetet. Ha az adatok áttelepíthetők az egyes táblákból, bontsa ki az adatbázist, majd a **táblák** csomópontot, és jelölje be a táblák melletti jelölőnégyzeteket. Ha az adatok kihagyása az egyes táblákból, törölje a jelölőnégyzetek jelölését.

   ![Az áttelepíthető adatáttelepítést bemutató képernyőkép.](./media/oracle-to-managed-instance-guide/migrate-data.png)

1. Adja meg az Oracle és az SQL felügyelt példány kapcsolati adatait.
1. Az áttelepítés befejezése után tekintse meg az **adatáttelepítési jelentést**.

   ![Az adatáttelepítési jelentést megjelenítő képernyőkép.](./media/oracle-to-managed-instance-guide/data-migration-report.png)

1. Kapcsolódjon az SQL felügyelt példányához [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms)használatával, és ellenőrizze az áttelepítést az adatelemzés és a séma áttekintésével.

   ![Az Oracle-SSMA érvényesítését bemutató képernyőkép.](./media/oracle-to-managed-instance-guide/validate-data.png)

Azt is megteheti, hogy az áttelepítést a SQL Server Integration Services használatával is végrehajtja. További információ:

- [A SQL Server Integration Services első lépései](/sql/integration-services/sql-server-integration-services)
- [SQL Server Integration Services az Azure-hoz és a hibrid adatáthelyezéshez](https://download.microsoft.com/download/D/2/0/D20E1C5F-72EA-4505-9F26-FEF9550EFD44/SSIS%20Hybrid%20and%20Azure.docx)

## <a name="post-migration"></a>A migrálás után

Az *áttelepítési* fázis sikeres befejezése után el kell végeznie az áttelepítést követő feladatok sorozatát, hogy a lehető leggördülékenyebb és hatékony működést biztosítsa.

### <a name="remediate-applications"></a>Alkalmazások szervizelése

Miután áttelepítette az adatátvitelt a cél környezetbe, az összes olyan alkalmazásnak, amely korábban használta a forrást, el kell kezdenie a cél felhasználását. Ennek a lépésnek a végrehajtásához bizonyos esetekben módosításokat kell végrehajtani az alkalmazásokban.

Az [adathozzáférés áttelepítési eszközkészlete](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit) egy Visual Studio Code-bővítmény, amely lehetővé teszi a Java-forráskód elemzését és az adatelérési API-hívások és lekérdezések észlelését. Az eszközkészlet egy egyablakos nézetet biztosít, amely az új adatbázis-háttér támogatásához szükséges megoldást nyújtja. További információ: [Java-alkalmazás migrálása Oracle-](https://techcommunity.microsoft.com/t5/microsoft-data-migration/migrate-your-java-applications-from-oracle-to-sql-server-with/ba-p/368727) blogbejegyzésből.

### <a name="perform-tests"></a>Tesztek végrehajtása

Az adatbázis-áttelepítés tesztelési módszere a következő tevékenységekből áll:

1. **Ellenőrzési tesztek fejlesztése**: az adatbázis áttelepítésének teszteléséhez SQL-lekérdezéseket kell használnia. Létre kell hoznia az érvényesítési lekérdezéseket a forrás-és a célként megadott adatbázisokon való futtatáshoz. Az érvényesítési lekérdezéseknek le kell fedniük a definiált hatókört.
2. **Tesztkörnyezet beállítása**: a tesztkörnyezet a forrás-adatbázis és a céladatbázis másolatát is tartalmazza. Ügyeljen arra, hogy elkülönítse a tesztkörnyezet.
3. **Ellenőrzési tesztek futtatása**: ellenőrző tesztek futtatása a forráson és a célon, majd az eredmények elemzése.
4. **Teljesítménytesztek futtatása**: futtasson teljesítményteszteket a forráson és a célon, majd elemezze és hasonlítsa össze az eredményeket.

### <a name="optimize"></a>Optimalizálás

Az áttelepítés utáni fázis elengedhetetlen az adatok pontosságával kapcsolatos problémák összeegyeztetéséhez, a teljesség ellenőrzéséhez és a teljesítménnyel kapcsolatos problémák kezeléséhez.

> [!NOTE]
> További információ ezekről a problémákról és az azok enyhítésének lépéseiről: [áttelepítés utáni érvényesítés és optimalizálási útmutató](/sql/relational-databases/post-migration-validation-and-optimization-guide).

## <a name="migration-assets"></a>Áttelepítési eszközök

Az áttelepítési forgatókönyv végrehajtásával kapcsolatos további segítségért tekintse meg a következő forrásokat. Egy valós migrációs projekt bevonásának támogatásával lettek kifejlesztve.

| **Cím/hivatkozás**                                                                                                                                          | **Leírás**                                                                                                                                                                                                                                                                                                                                                                                       |
| ------------------------------------------------------------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [Adatmunkaterhelés-felmérési modell és eszköz](https://github.com/Microsoft/DataMigrationTeam/tree/master/Data%20Workload%20Assessment%20Model%20and%20Tool) | Ez az eszköz a javasolt "legmegfelelőbb" cél platformot, a felhő készültségét, valamint az alkalmazás vagy az adatbázis szervizelési szintjét biztosítja egy adott munkaterhelés esetében. Egyszerű, egykattintásos számítási és jelentéskészítési lehetőséget kínál, amely lehetővé teszi a nagyméretű ingatlan-értékelések felgyorsítását azáltal, hogy automatizált és egységes célzott platformra vonatkozó döntési folyamatot biztosít.                                                          |
| [Oracle Inventory parancsfájl-összetevők](https://github.com/Microsoft/DataMigrationTeam/tree/master/Oracle%20Inventory%20Script%20Artifacts)                 | Ez az objektum olyan PL/SQL-lekérdezést tartalmaz, amely megkeresi az Oracle rendszertáblázatokat, és az objektumok számát a séma típusa, az Objektumtípus és az állapot szerint adja meg. Emellett az egyes sémákban lévő nyers adatmennyiséget, valamint az egyes sémákban található táblák méretezését is megbecsüli, és a CSV-formátumban tárolt eredményeket is biztosítja.                                                                                                               |
| [SSMA Oracle Assessment Collection &-összevonás automatizálása](https://github.com/microsoft/DataMigrationTeam/tree/master/IP%20and%20Scripts/Automate%20SSMA%20Oracle%20Assessment%20Collection%20%26%20Consolidation)                                             | Ez a készlet a. csv-fájlt használja bejegyzésként (sources.csv a projekt mappáiban) a SSMA-értékelés konzolos módban való futtatásához szükséges XML-fájlok létrehozásához. A source.csv az ügyfél a meglévő Oracle-példányok leltára alapján kapja meg. A kimeneti fájlok AssessmentReportGeneration_source_1.xml, ServersConnectionFile.xml és VariableValueFile.xml.|
| [SSMA általános Oracle-hibákhoz és azok javításához](https://aka.ms/dmj-wp-ssma-oracle-errors)                                                           | Az Oracle használatával a WHERE záradékban hozzárendelhet egy nem Scala feltételt. A SQL Server azonban nem támogatja ezt a típusú feltételt. Ennek eredményeképpen a SSMA for Oracle nem alakítja át a lekérdezéseket a WHERE záradékban található nem Scala feltétellel. Ehelyett a O2SS0001 hibát generál. Ez a tanulmány további részleteket tartalmaz a problémáról és annak megoldási módjairól.          |
| [Oracle – SQL Server áttelepítési kézikönyv](https://github.com/microsoft/DataMigrationTeam/blob/master/Whitepapers/Oracle%20to%20SQL%20Server%20Migration%20Handbook.pdf)                | Ez a dokumentum az Oracle-sémák SQL Server adatbázis legújabb verziójára való áttelepítésével kapcsolatos feladatokra koncentrál. Ha az áttelepítéshez a szolgáltatások vagy funkciók módosítása szükséges, akkor az adatbázist használó alkalmazások változásainak lehetséges hatását körültekintően kell figyelembe venni.                                                     |

Az adatsql mérnöki csapat fejlesztette ezeket az erőforrásokat. A csapat alapszintű alapokmánya az adatplatform-áttelepítési projektek a Microsoft Azure-beli adatplatformra való feltiltásának és felgyorsításának feloldása.

## <a name="next-steps"></a>Következő lépések

- A Microsoft és a harmadik féltől származó szolgáltatások és eszközök egy olyan mátrixa, amely a különböző adatbázis-és adatáttelepítési forgatókönyvek és speciális feladatok ellátásához nyújt segítséget, lásd: [szolgáltatások és eszközök az adatok áttelepítéséhez](../../../dms/dms-tools-matrix.md).

- Az SQL felügyelt példányával kapcsolatos további tudnivalókért tekintse meg a következőt:
  - [Az Azure SQL felügyelt példányának áttekintése](../../managed-instance/sql-managed-instance-paas-overview.md)
  - [Azure teljes tulajdonlási költség (TCO) – kalkulátor](https://azure.microsoft.com/en-us/pricing/tco/calculator/)

- Ha többet szeretne megtudni a Felhőbeli Migrálás keretrendszeréről és bevezetési ciklusáról, tekintse meg a következőt:
   -  [Felhőbevezetési keretrendszer az Azure-hoz](/azure/cloud-adoption-framework/migrate/azure-best-practices/contoso-migration-scale)
   -  [Ajánlott eljárások az Azure-ba való Migrálás díjszabásához és méretezéséhez](/azure/cloud-adoption-framework/migrate/azure-best-practices/migrate-best-practices-costs)

- A videó tartalma:
    - [Az áttelepítési út, valamint az értékelés és az áttelepítés elvégzéséhez ajánlott eszközök és szolgáltatások áttekintése](https://azure.microsoft.com/resources/videos/overview-of-migration-and-recommended-tools-services/)
