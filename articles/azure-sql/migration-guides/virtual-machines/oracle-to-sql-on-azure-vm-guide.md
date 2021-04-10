---
title: 'Oracle to SQL Server on Azure Virtual Machines: áttelepítési útmutató'
description: Ez az útmutató bemutatja, hogyan telepítheti át az Oracle-sémákat az Azure-Virtual Machines SQL Server az Oracle SQL Server Migration Assistant használatával.
ms.service: virtual-machines-sql
ms.subservice: migration-guide
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: mokabiru
ms.author: mokabiru
ms.reviewer: MashaMSFT
ms.date: 11/06/2020
ms.openlocfilehash: d4fb33e8e904d12e242f7eeaf9c2dc50a02eff4d
ms.sourcegitcommit: edc7dc50c4f5550d9776a4c42167a872032a4151
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105961251"
---
# <a name="migration-guide-oracle-to-sql-server-on-azure-virtual-machines"></a>Áttelepítési útmutató: Oracle to SQL Server on Azure Virtual Machines
[!INCLUDE[appliesto-sqldb-sqlmi](../../includes/appliesto-sqldb.md)]

Ez az útmutató bemutatja, hogyan telepítheti át az Oracle-sémákat az Azure-Virtual Machines SQL Server az Oracle SQL Server Migration Assistant használatával. 

Más áttelepítési útmutatókért lásd: [adatbázis-áttelepítés](https://docs.microsoft.com/data-migration). 

## <a name="prerequisites"></a>Előfeltételek 

Ha át szeretné telepíteni az Oracle-sémát az Azure Virtual Machines SQL Serverre, a következőkre lesz szüksége:

- Egy támogatott forrásoldali környezet.
- [SQL Server Migration Assistant (SSMA) Oracle-hez](https://www.microsoft.com/en-us/download/details.aspx?id=54258).
- A cél [SQL Server VM](../../virtual-machines/windows/sql-vm-create-portal-quickstart.md).
- Az Oracle és a [szolgáltató](/sql/ssma/oracle/connect-to-oracle-oracletosql) [SSMA szükséges engedélyek](/sql/ssma/oracle/connecting-to-oracle-database-oracletosql) .
- Kapcsolat és megfelelő engedélyek a forrás és a cél eléréséhez. 


## <a name="pre-migration"></a>A migrálás előtt

A felhőbe való Migrálás előkészítéséhez ellenőrizze, hogy a forrás-környezet támogatott-e, és hogy van-e valamilyen előfeltétele. Ez segít a hatékony és sikeres Migrálás biztosításában.

A folyamat ezen része a következőket foglalja magában: 
- Az áttelepíteni kívánt adatbázisok leltárának elvégzése.
- Ezeknek az adatbázisoknak a kiértékelése lehetséges migrációs problémák vagy blokkolók esetén. 
- Oldja fel a feltárt problémákat. 

### <a name="discover"></a>Felderítés

A [map Toolkit](https://go.microsoft.com/fwlink/?LinkID=316883) segítségével azonosíthatja a meglévő adatforrásokat és a vállalat által használt funkciók részleteit. Így jobban megismerheti az áttelepítést, és segítséget nyújt a tervezésben. Ez a folyamat a szervezet Oracle-példányainak, valamint az Ön által használt verziók és szolgáltatások azonosítására szolgáló hálózat vizsgálatát foglalja magában.

Ha a Térkép eszközkészletet szeretné használni leltár vizsgálatához, kövesse az alábbi lépéseket: 


1. Nyissa meg a [Térkép eszközkészletet](https://go.microsoft.com/fwlink/?LinkID=316883).


1. Válassza az **adatbázis létrehozása/kiválasztása** lehetőséget:

   ![Az adatbázis létrehozása/kiválasztása lehetőséget megjelenítő képernyőkép.](./media/oracle-to-sql-on-azure-vm-guide/select-database.png)

1. Válassza **a leltár-adatbázis létrehozása** lehetőséget. Adja meg a létrehozandó új leltári adatbázis nevét, adjon meg egy rövid leírást, majd kattintson **az OK gombra**: 

   :::image type="content" source="media/oracle-to-sql-on-azure-vm-guide/create-inventory-database.png" alt-text="Képernyőfelvétel a leltári adatbázisok létrehozásához szükséges felületet bemutató képernyőképről.":::

1. Válassza a **leltári adatok gyűjtése** lehetőséget a **leltár és értékelés varázsló** megnyitásához:

   :::image type="content" source="media/oracle-to-sql-on-azure-vm-guide/collect-inventory-data.png" alt-text="A leltári adatok összegyűjtése hivatkozást megjelenítő képernyőkép.":::


1. A **leltár és értékelés varázslóban** válassza az **Oracle** lehetőséget, majd válassza a **Next (tovább**) gombot:

   ![A leltár és értékelés varázsló leltározási helyzetek lapját bemutató képernyőkép.](./media/oracle-to-sql-on-azure-vm-guide/choose-oracle.png)

1. Válassza ki az üzleti igényeknek és környezetnek legmegfelelőbb számítógép-keresési lehetőséget, majd válassza a **Next (tovább**) gombot: 

   ![A leltár és értékelés varázsló felderítési módszerek lapját bemutató képernyőkép.](./media/oracle-to-sql-on-azure-vm-guide/choose-search-option.png)

1. Adja meg a hitelesítő adatokat, vagy hozzon létre új hitelesítő adatokat a vizsgálni kívánt rendszerekhez, majd válassza a **Next (tovább**) gombot:

    ![A leltár és értékelés varázsló összes számítógép hitelesítő adatok lapját megjelenítő képernyőkép.](./media/oracle-to-sql-on-azure-vm-guide/choose-credentials.png)


1. Állítsa be a hitelesítő adatok sorrendjét, majd válassza a **Next (tovább**) gombot: 

   ![A leltár és értékelés varázsló hitelesítő adatok sorrend lapját bemutató képernyőkép.](./media/oracle-to-sql-on-azure-vm-guide/set-credential-order.png)  


1. Adja meg a felderíteni kívánt számítógépek hitelesítő adatait. Minden számítógépen vagy gépen egyedi hitelesítő adatokat használhat, vagy használhatja az összes számítógép hitelesítő adatait tartalmazó listát is.  

   ![A leltár és értékelés varázsló számítógépek és hitelesítő adatok megadása lapján látható képernyőkép.](./media/oracle-to-sql-on-azure-vm-guide/specify-credentials-for-each-computer.png)


1. Ellenőrizze a beállításokat, majd kattintson a **Befejezés gombra**:

   ![A leltár és értékelés varázsló összefoglalás lapját bemutató képernyőkép.](./media/oracle-to-sql-on-azure-vm-guide/review-summary.png)


1. A vizsgálat befejeződése után tekintse meg az **adatgyűjtés** összegzését. A vizsgálat az adatbázisok számától függően néhány percet is igénybe vehet. Ha elkészült, válassza a **Bezárás** lehetőséget: 

   ![Az adatgyűjtés összegzését bemutató képernyőkép.](./media/oracle-to-sql-on-azure-vm-guide/collection-summary-report.png)


1. Válassza a **Beállítások lehetőséget** , ha jelentést szeretne készíteni az Oracle Assessment és az adatbázis részleteiről. A jelentés létrehozásához egyszerre válassza a két lehetőséget.


### <a name="assess"></a>Kiértékelés

Az adatforrások azonosítása után a [SQL Server Migration Assistant for Oracle](https://www.microsoft.com/en-us/download/details.aspx?id=54258) használatával mérje fel, hogy az Oracle-példányok áttelepítése a SQL Server VM. A Segéd segít megérteni a forrás-és a cél-adatbázisok közötti különbségeket. Áttekintheti az adatbázis-objektumokat és-adatforrásokat, kiértékelheti az áttelepítésre használt adatbázisokat, áttelepítheti az adatbázis-objektumokat SQL Serverba, majd átSQL Server telepítheti

Az értékelés létrehozásához kövesse az alábbi lépéseket: 


1. [Az Oracle SQL Server Migration Assistant](https://www.microsoft.com/en-us/download/details.aspx?id=54258)megnyitása. 
1. A **fájl** menüben válassza az **új projekt** lehetőséget. 
1. Adja meg a projekt nevét és helyét a projekthez, majd válasszon ki egy SQL Server áttelepítési célt a listából. Válassza **az OK gombot**: 

   ![Képernyőkép, amely az új projekt párbeszédpanelt jeleníti meg.](./media/oracle-to-sql-on-azure-vm-guide/new-project.png)


1. Válassza **a Kapcsolódás az Oracle-hoz** lehetőséget. Adja meg az Oracle-kapcsolat értékeit a **Kapcsolódás az Oracle** -hez párbeszédpanelen:

   ![Az Oracle kapcsolódása párbeszédpanelt megjelenítő képernyőkép.](./media/oracle-to-sql-on-azure-vm-guide/connect-to-oracle.png)

   Válassza ki az áttelepíteni kívánt Oracle-sémákat: 

   ![Képernyőkép, amely az áttelepíthető Oracle-sémák listáját jeleníti meg.](./media/oracle-to-sql-on-azure-vm-guide/select-schema.png)


1. Az **Oracle metadata Explorerben** kattintson a jobb gombbal arra az Oracle-sémára, amelyet át szeretne telepíteni, majd válassza a **jelentés létrehozása** lehetőséget. Ekkor létrejön egy HTML-jelentés. Azt is megteheti, hogy kijelöli az adatbázist, majd a felső menüben kiválasztja a **jelentés létrehozása** lehetőséget.

   ![Képernyőkép, amely bemutatja, hogyan hozhat létre egy jelentést.](./media/oracle-to-sql-on-azure-vm-guide/create-report.png)

1. Tekintse át az átalakítási statisztikák, a hibák és a figyelmeztetések HTML-jelentését. Elemezheti a konverziós problémák és a megoldások értelmezését.

    A jelentést az Excelben is megnyithatja, hogy beolvassa az Oracle-objektumok leltárát, és a séma átalakításának befejezéséhez szükséges erőfeszítést is. A jelentés alapértelmezett helye a SSMAProjects található jelentés mappája. 

   Például: `drive:\<username>\Documents\SSMAProjects\MyOracleMigration\report\report_2016_11_12T02_47_55\`


   ![Képernyőkép, amely egy konverziós jelentést jelenít meg.](./media/oracle-to-sql-on-azure-vm-guide/conversion-report.png)


### <a name="validate-data-types"></a>Adattípusok ellenőrzése

Ellenőrizze az alapértelmezett adattípus-hozzárendeléseket, és szükség esetén módosítsa azokat a követelmények alapján. Ehhez kövesse az alábbi lépéseket: 


1. Az **eszközök** menüben válassza a **projekt beállításai** lehetőséget. 
1. Válassza a **típus-hozzárendelések** lapot. 

   ![A típus-hozzárendelések lapot megjelenítő képernyőkép.](./media/oracle-to-sql-on-azure-vm-guide/type-mappings.png)

1. Az egyes táblák típus-hozzárendelését úgy módosíthatja, hogy kijelöli a táblázatot az **Oracle metadata Explorerben**. 

### <a name="convert-the-schema"></a>A séma konvertálása

A séma konvertálásához kövesse az alábbi lépéseket: 

1. Választható Dinamikus vagy alkalmi lekérdezések konvertálásához kattintson a jobb gombbal a csomópontra, és válassza az **Add utasítás** elemet.

1. A felső menüben kattintson a **kapcsolódás SQL Server** lehetőségre. 
     1. Adja meg az Azure-beli virtuális gépen SQL Server kapcsolati adatait. 
     1. Válassza ki a kívánt adatbázist a listából, vagy adjon meg egy új nevet. Ha új nevet ad meg, a rendszer létrehoz egy adatbázist a célkiszolgálón. 
     1. Adja meg a hitelesítés részleteit. 
     1. Válassza a **Kapcsolódás** lehetőséget. 


   ![Képernyőkép, amely bemutatja, hogyan csatlakozhat a SQL Serverhoz.](./media/oracle-to-sql-on-azure-vm-guide/connect-to-sql-vm.png)

1. Kattintson a jobb gombbal az Oracle-sémára az **Oracle metadata Explorerben** , és válassza a **séma konvertálása** lehetőséget. Azt is megteheti, hogy a felső menüben a **séma konvertálása** lehetőségre kattint:

   ![A séma átalakítását bemutató képernyőkép.](./media/oracle-to-sql-on-azure-vm-guide/convert-schema.png)


1. A séma átalakításának befejeződése után tekintse át a konvertált objektumokat, és hasonlítsa össze azokat az eredeti objektumokkal a lehetséges problémák azonosítása érdekében. A javaslatok a következő problémák megoldására használhatók:

   ![A két séma összehasonlítását bemutató képernyőkép.](./media/oracle-to-sql-on-azure-vm-guide/table-mapping.png)

   Hasonlítsa össze a konvertált Transact-SQL-szöveget az eredeti tárolt eljárásokkal, és tekintse át a javaslatokat: 

   ![Képernyőfelvétel: Transact-SQL, tárolt eljárások és figyelmeztetés.](./media/oracle-to-sql-on-azure-vm-guide/procedure-comparison.png)

   A projektet helyileg is mentheti egy offline séma szervizelési gyakorlatához. Ehhez válassza a **fájl** menü **projekt mentése** parancsát. A projekt helyi mentése lehetővé teszi a forrás-és a célként megadott sémák offline kiértékelését és szervizelését, mielőtt közzéteszi a sémát a SQL Server.

1. Válassza az **eredmények áttekintése** lehetőséget a **kimenet** ablaktáblán, majd tekintse át a hibákat a hibák **listája** ablaktáblán. 
1. Mentse a projektet helyileg a kapcsolat nélküli séma szervizelési gyakorlatához. A **fájl** menüben válassza a **projekt mentése** elemet. Ez lehetőséget biztosít a forrás-és a célként megadott sémák offline kiértékelésére és szervizelésre, mielőtt közzéteszi a sémát az Azure Virtual Machines SQL Server.


## <a name="migrate"></a>Migrate

Miután elvégezte a szükséges előfeltételeket, és végrehajtotta az áttelepítés előtti fázishoz kapcsolódó feladatokat, készen áll a séma és az adatok áttelepítésére. Az áttelepítés két lépést tesz szükségessé: a séma közzétételét és az adatáttelepítést. 


A séma közzétételéhez és az adatáttelepítés végrehajtásához kövesse az alábbi lépéseket: 

1. Tegye közzé a sémát: kattintson a jobb gombbal az adatbázisra **SQL Server metaadat-kezelőben** , majd válassza az **adatbázissal való szinkronizálás** lehetőséget. Ez teszi közzé az Oracle-sémát, hogy SQL Server az Azure Virtual Machines. 

   ![Képernyőkép, amely megjeleníti a szinkronizálás az adatbázissal parancsot.](./media/oracle-to-sql-on-azure-vm-guide/synchronize-database.png)

   Tekintse át a forrás projekt és a cél közötti leképezést:

   ![A szinkronizálási állapotot bemutató képernyőkép.](./media/oracle-to-sql-on-azure-vm-guide/synchronize-database-review.png)



1. Migrálja az adatokat: kattintson a jobb gombbal arra az adatbázisra vagy objektumra, amelyet az **Oracle metadata Explorerben** szeretne áttelepíteni, majd válassza az **adatok áttelepíteni** lehetőséget. Azt is megteheti, hogy a felső menüben kijelöli az **adatáttelepítés** lehetőséget.

   Ha egy teljes adatbázisra szeretné áttelepíteni az adatátvitelt, jelölje be az adatbázis neve melletti jelölőnégyzetet. Ha az adatok áttelepíthetők az egyes táblákból, bontsa ki az adatbázist, majd a **táblák** csomópontot, és jelölje be a tábla melletti jelölőnégyzetet. Ha az adatok kihagyása az egyes táblákból, törölje a jelet a megfelelő jelölőnégyzetből.

   ![Az adatmigrálás parancsot megjelenítő képernyőkép.](./media/oracle-to-sql-on-azure-vm-guide/migrate-data.png)

1. Adja meg az Oracle és a SQL Server kapcsolati adatait az Azure Virtual Machines a párbeszédpanelen.
1. Az áttelepítés befejeződése után tekintse meg az **adatáttelepítési jelentést**:

    ![Az adatáttelepítési jelentést bemutató képernyőkép.](./media/oracle-to-sql-on-azure-vm-guide/data-migration-report.png)

1. Kapcsolódjon a SQL Server az Azure Virtual Machines-példányon [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms)használatával. Ellenőrizze az áttelepítést az adatelemzés és a séma áttekintésével:


   ![A SSMA SQL Server példányát bemutató képernyőkép.](./media/oracle-to-sql-on-azure-vm-guide/validate-in-ssms.png)

A SSMA használata helyett a SQL Server Integration Services (SSIS) használatával áttelepítheti az adatátvitelt. További információ: 
- A cikk [SQL Server Integration Services](https://docs.microsoft.com//sql/integration-services/sql-server-integration-services).
- A tanulmányi [SSIS az Azure-hoz és a hibrid adatáthelyezéshez](https://download.microsoft.com/download/D/2/0/D20E1C5F-72EA-4505-9F26-FEF9550EFD44/SSIS%20Hybrid%20and%20Azure.docx).


## <a name="post-migration"></a>A migrálás után 

Az áttelepítési fázis befejezése után el kell végeznie az áttelepítés utáni feladatok sorozatát, hogy minden a lehető legzökkenőmentesen és hatékonyan fusson.

### <a name="remediate-applications"></a>Alkalmazások szervizelése

Miután áttelepítette az adatátvitelt a cél környezetbe, az összes olyan alkalmazásnak, amely korábban a forrást használta, el kell kezdenie a cél felhasználását. A módosítások végrehajtásához szükség lehet az alkalmazások módosítására.

Az [adathozzáférés áttelepítési eszközkészlete](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit) egy bővítmény a Visual Studio Code-hoz. Lehetővé teszi a Java-forráskód elemzését és az adatelérési API-hívások és lekérdezések észlelését. Az eszközkészlet egy egyablakos nézetet biztosít arról, hogy mit kell megcélozni az új adatbázis-háttér támogatásához. További információ: [Java-alkalmazás migrálása az Oracle-ből](https://techcommunity.microsoft.com/t5/microsoft-data-migration/migrate-your-java-applications-from-oracle-to-sql-server-with/ba-p/368727). 

### <a name="perform-tests"></a>Tesztek végrehajtása

Az adatbázis áttelepítésének teszteléséhez hajtsa végre a következő tevékenységeket:

1. **Ellenőrzési tesztek fejlesztése**. Az adatbázis-áttelepítés teszteléséhez SQL-lekérdezéseket kell használnia. Hozza létre azokat az ellenőrzési lekérdezéseket, amelyek a forrás-és a célként megadott adatbázison futnak. Az érvényesítési lekérdezéseknek le kell fedniük a definiált hatókört.

2. **Tesztelési környezet beállítása**. A tesztkörnyezet a forrás-adatbázis és a céladatbázis másolatát is tartalmazza. Ügyeljen arra, hogy elkülönítse a tesztkörnyezet.

3. **Ellenőrzési tesztek futtatása**. Futtassa az ellenőrző teszteket a forráson és a célon, majd elemezze az eredményeket.

4. **Teljesítménytesztek futtatása**. Futtasson teljesítménytesztet a forráson és a célon, majd elemezze és hasonlítsa össze az eredményeket.

### <a name="optimize"></a>Optimalizálás

Az áttelepítés utáni fázis elengedhetetlen az adatok pontosságával kapcsolatos problémák összeegyeztetéséhez és a teljesség ellenőrzéséhez. Emellett kritikus fontosságú a számítási feladatok teljesítményével kapcsolatos problémák kezelése is.

> [!Note]
> További információ ezekről a problémákról és az azok enyhítésére szolgáló konkrét lépésekről: [áttelepítés utáni érvényesítés és optimalizálási útmutató](/sql/relational-databases/post-migration-validation-and-optimization-guide).


## <a name="migration-resources"></a>Migrálási erőforrások 

Ha további segítségre van az áttelepítési forgatókönyv végrehajtásához, tekintse meg a következő, a valós áttelepítési projekt támogatására kifejlesztett forrásokat.

| **Cím/hivatkozás**                                                                                                                                          | **Leírás**                                                                                                                                                                                                                                                                                                   |
| ------------------------------------------------------------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [Adatmunkaterhelés-felmérési modell és eszköz](https://github.com/Microsoft/DataMigrationTeam/tree/master/Data%20Workload%20Assessment%20Model%20and%20Tool) | Ez az eszköz az adott számítási feladathoz javasolt legjobban illeszkedő cél platformokat, a felhő készültségét, valamint az alkalmazások/adatbázisok szervizelési szintjeit biztosítja. Egyszerű, egykattintásos számítási és jelentéskészítési folyamattal segíti a nagyméretű ingatlanok értékelését azáltal, hogy automatizált és egységes cél-platform döntéshozatali folyamatot biztosít.                                                          |
| [Oracle Inventory parancsfájl-összetevők](https://github.com/Microsoft/DataMigrationTeam/tree/master/Oracle%20Inventory%20Script%20Artifacts)                 | Ez az objektum egy olyan PL/SQL-lekérdezést tartalmaz, amely az Oracle-rendszertáblákat célozza meg, és az objektumok számát a séma típusa, az Objektumtípus és az állapot alapján adja meg. Emellett az egyes sémákban lévő nyers adatmennyiséget, valamint az egyes sémákban található táblák méretezését is megbecsüli, és a CSV-formátumban tárolt eredményeket is biztosítja.                                                                                                               |
| [SSMA Oracle Assessment Collection &-összevonás automatizálása](https://github.com/microsoft/DataMigrationTeam/tree/master/IP%20and%20Scripts/Automate%20SSMA%20Oracle%20Assessment%20Collection%20%26%20Consolidation)                                             | Ez a készlet a. csv-fájlt használja bejegyzésként (sources.csv a projekt mappáiban), hogy létrehozzák azokat az XML-fájlokat, amelyekre szükség van a SSMA-értékelés konzolos módban való futtatásához. A source.csv fájlt a meglévő Oracle-példányok leltározásával adja meg. A kimeneti fájlok AssessmentReportGeneration_source_1.xml, ServersConnectionFile.xml és VariableValueFile.xml.|
| [Az Oracle-adatbázisok áttelepítése során felmerülő problémák és lehetséges SSMA](https://aka.ms/dmj-wp-ssma-oracle-errors)                                                           | Az Oracle használatával a WHERE záradékban nem skaláris feltételt rendelhet hozzá. A SQL Server nem támogatja az ilyen típusú feltételt. Így a SSMA for Oracle nem konvertál olyan lekérdezéseket, amelyek nem skaláris feltételt tartalmaznak a WHERE záradékban. Ehelyett a következő hibát generálja: O2SS0001. Ez a tanulmány részletesen ismerteti a problémát és a megoldásának módszereit.          |
| [Oracle – SQL Server áttelepítési kézikönyv](https://github.com/microsoft/DataMigrationTeam/blob/master/Whitepapers/Oracle%20to%20SQL%20Server%20Migration%20Handbook.pdf)                | Ez a dokumentum az Oracle-sémák a SQL Server legújabb verziójára való áttelepítésével kapcsolatos feladatokra koncentrál. Ha az áttelepítéshez a szolgáltatások/funkciók módosítása szükséges, gondosan mérlegelje az egyes módosítások lehetséges hatását az adatbázist használó alkalmazások esetében.                                                     |


Az adatsql mérnöki csapat fejlesztette ezeket az erőforrásokat. A csapat alapszintű alapokmánya az adatplatform-áttelepítési projektek Microsoft Azure adatplatformon való összevonásának feloldása és felgyorsítása.


## <a name="next-steps"></a>Következő lépések

- A SQL Serverra vonatkozó szolgáltatások rendelkezésre állásának vizsgálatához tekintse meg az [Azure globális infrastruktúra-központját](https://azure.microsoft.com/global-infrastructure/services/?regions=all&amp;products=synapse-analytics,virtual-machines,sql-database).

- A Microsoft és a harmadik féltől származó szolgáltatások és eszközök egyik mátrixa, amely a különböző adatbázis-és adatáttelepítési forgatókönyvek és speciális feladatok ellátásához nyújt segítséget, lásd: [szolgáltatások és eszközök az adatok áttelepítéséhez](../../../dms/dms-tools-matrix.md).

- Az Azure SQL-ről további információt a következő témakörben talál:
   - [Üzembe helyezési lehetőségek](../../azure-sql-iaas-vs-paas-what-is-overview.md)
   - [SQL Server az Azure Virtual Machines szolgáltatásban](../../virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview.md)
   - [Az Azure teljes tulajdonlási költsége kalkulátor](https://azure.microsoft.com/pricing/tco/calculator/)


- Ha többet szeretne megtudni a Felhőbeli Migrálás keretrendszeréről és bevezetési ciklusáról, tekintse meg a következőt:
   -  [Felhőbevezetési keretrendszer az Azure-hoz](/azure/cloud-adoption-framework/migrate/azure-best-practices/contoso-migration-scale)
   -  [Ajánlott eljárások az Azure-ba migrált számítási feladatokhoz és méretekhez](/azure/cloud-adoption-framework/migrate/azure-best-practices/migrate-best-practices-costs) 

- A licenceléssel kapcsolatos további információkért lásd:
   - [Saját licenc használata a Azure Hybrid Benefit](../../virtual-machines/windows/licensing-model-azure-hybrid-benefit-ahb-change.md)
   - [A SQL Server 2008 és SQL Server 2008 R2 ingyenes kibővített támogatása](../../virtual-machines/windows/sql-server-2008-extend-end-of-support.md)

- Az alkalmazás-hozzáférési réteg értékeléséhez használja az [adathozzáférés áttelepítési eszközkészletének előzetes](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit)verzióját.
- Az "A/B" adatelérési réteg tesztelésével kapcsolatos részletekért lásd: [Database Experimentation Assistant áttekintése](/sql/dea/database-experimentation-assistant-overview).


