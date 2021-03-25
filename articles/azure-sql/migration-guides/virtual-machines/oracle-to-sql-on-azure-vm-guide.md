---
title: 'Oracle – SQL Server az Azure-beli virtuális gépen: áttelepítési útmutató'
description: Ez az útmutató az Oracle-sémák áttelepítését mutatja be SQL Server Azure-beli virtuális gépeken az Oracle-SQL Server Migration Assistant használatával.
ms.service: virtual-machines-sql
ms.subservice: migration-guide
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: mokabiru
ms.author: mokabiru
ms.reviewer: MashaMSFT
ms.date: 11/06/2020
ms.openlocfilehash: 5d24e056d397617c95a7ba301b58efc3631f40dd
ms.sourcegitcommit: a8ff4f9f69332eef9c75093fd56a9aae2fe65122
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2021
ms.locfileid: "105026521"
---
# <a name="migration-guide-oracle-to-sql-server-on-azure-vm"></a>Áttelepítési útmutató: Oracle SQL Server Azure-beli virtuális gépen
[!INCLUDE[appliesto-sqldb-sqlmi](../../includes/appliesto-sqldb.md)]

Ez az útmutató azt ismerteti, hogy az Oracle-sémák áttelepíthetők az Azure-beli virtuális gépen SQL Server az Oracle-SQL Server Migration Assistant használatával. 

Más áttelepítési útmutatókért lásd: [adatbázis-áttelepítés](https://docs.microsoft.com/data-migration). 

## <a name="prerequisites"></a>Előfeltételek 

Az Oracle-sémának az Azure-beli virtuális gépen SQL Serverre való átmigrálása a következőkre van szüksége:

- Annak ellenőrzéséhez, hogy a forrás-környezet támogatott-e.
- Az [Oracle SQL Server Migration Assistant (SSMA)](https://www.microsoft.com/en-us/download/details.aspx?id=54258)letöltése.
- A cél [SQL Server VM](../../virtual-machines/windows/sql-vm-create-portal-quickstart.md).
- Az Oracle és a [Provider](/sql/ssma/oracle/connect-to-oracle-oracletosql) [SSMA szükséges engedélyei](/sql/ssma/oracle/connecting-to-oracle-database-oracletosql) .
- Kapcsolat és megfelelő engedélyek a forrás és a cél eléréséhez. 


## <a name="pre-migration"></a>A migrálás előtt

A felhőbe való Migrálás előkészítése során ellenőrizze, hogy a forrás-környezet támogatott-e, és hogy rendelkezik-e az előfeltételekkel. Ez segít a hatékony és sikeres Migrálás biztosításában.

A folyamat ezen része magában foglalja az áttelepíteni kívánt adatbázisok leltárának elvégzését, a lehetséges áttelepítési problémák vagy blokkolók kiértékelését, valamint az esetlegesen feltárt elemek feloldását. 

### <a name="discover"></a>Felderítés

A [map Toolkit](https://go.microsoft.com/fwlink/?LinkID=316883) segítségével azonosíthatja a meglévő adatforrásokat és a vállalat által használt funkciók részleteit, így jobban megismerheti és megtervezheti az áttelepítést. Ez a folyamat a hálózat vizsgálatával azonosítja az összes szervezet Oracle-példányát, valamint a használatban lévő verziót és szolgáltatásokat.

Ha a MAP Toolkit használatával szeretne leltározási vizsgálatot végezni, kövesse az alábbi lépéseket: 

1. Nyissa meg a [Térkép eszközkészletet](https://go.microsoft.com/fwlink/?LinkID=316883).
1. Válassza az **adatbázis létrehozása/kiválasztása** lehetőséget:

   ![Adatbázis kiválasztása](./media/oracle-to-sql-on-azure-vm-guide/select-database.png)

1. Válassza a **leltár-adatbázis létrehozása** lehetőséget, adja meg az új leltár-adatbázis nevét, adjon meg egy rövid leírást, majd kattintson **az OK gombra**:

   :::image type="content" source="media/oracle-to-sql-on-azure-vm-guide/create-inventory-database.png" alt-text="Leltár-adatbázis létrehozása":::

1. Válassza a **leltári adatok gyűjtése** lehetőséget a **leltár és értékelés varázsló** megnyitásához:

   :::image type="content" source="media/oracle-to-sql-on-azure-vm-guide/collect-inventory-data.png" alt-text="Készletadatok gyűjtése":::

1. A **leltár és értékelés varázslóban** válassza az **Oracle** lehetőséget, majd kattintson a **Next (tovább**) gombra:

   ![Oracle kiválasztása](./media/oracle-to-sql-on-azure-vm-guide/choose-oracle.png)

1. Válassza ki az üzleti igényeinek és környezetének legmegfelelőbb számítógép-keresési lehetőséget, majd válassza a **Next (tovább**) gombot: 

   ![Válassza ki az üzleti igényeknek leginkább megfelelő számítógép-keresési lehetőséget](./media/oracle-to-sql-on-azure-vm-guide/choose-search-option.png)

1. Adja meg a hitelesítő adatokat, vagy hozzon létre új hitelesítő adatokat a vizsgálni kívánt rendszerekhez, majd válassza a **Next (tovább**) gombot:

    ![Hitelesítő adatok megadása](./media/oracle-to-sql-on-azure-vm-guide/choose-credentials.png)

1. Állítsa be a hitelesítő adatok sorrendjét, majd válassza a **Next (tovább**) gombot:

   ![Hitelesítő adatok sorrendjének beállítása](./media/oracle-to-sql-on-azure-vm-guide/set-credential-order.png)  

1. Határozza meg a felderíteni kívánt számítógépek hitelesítő adatait. Minden számítógépen vagy gépen egyedi hitelesítő adatokat használhat, vagy az **összes számítógép hitelesítő adatainak** listáját is használhatja:


   ![A felderíteni kívánt számítógépek hitelesítő adatainak megadása](./media/oracle-to-sql-on-azure-vm-guide/specify-credentials-for-each-computer.png)


1. Ellenőrizze a kiválasztási összegzést, majd kattintson a **Befejezés gombra**:

   ![Összefoglalás áttekintése](./media/oracle-to-sql-on-azure-vm-guide/review-summary.png)

1. A vizsgálat befejezését követően tekintse meg az **adatgyűjtés** összegző jelentését. A vizsgálat több percet is igénybe vehet, és az adatbázisok számától függ. Ha elkészült, válassza a **Bezárás** lehetőséget:

   ![Gyűjtemény összegző jelentése](./media/oracle-to-sql-on-azure-vm-guide/collection-summary-report.png)


1. Válassza a **Beállítások lehetőséget** , ha jelentést szeretne készíteni az Oracle Assessment és az adatbázis részleteiről. A jelentés létrehozásához jelölje ki mindkét beállítást (eggyel egyet).


### <a name="assess"></a>Kiértékelés

Az adatforrások azonosítása után az [oracle SQL Server Migration Assistant (SSMA)](https://www.microsoft.com/en-us/download/details.aspx?id=54258) segítségével mérje fel, hogy az Oracle-példány (ok) át legyenek-e migrálva a SQL Server VMba, hogy megértse a kettő közötti réseket. Az áttelepítési segéd segítségével áttekintheti az adatbázis-objektumokat és-adatforrásokat, kiértékelheti az áttelepítésre használt adatbázisokat, áttelepítheti az adatbázis-objektumokat SQL Serverba, majd átSQL Server telepítheti

Az értékelés létrehozásához kövesse az alábbi lépéseket: 

1. Nyissa meg az  [Oracle SQL Server Migration Assistant (SSMA)](https://www.microsoft.com/en-us/download/details.aspx?id=54258). 
1. Válassza a **fájl** , majd az **új projekt** lehetőséget. 
1. Adja meg a projekt nevét, egy helyet a projekt mentéséhez, majd válasszon egy SQL Server áttelepítési célt a legördülő menüből. Válassza **az OK gombot**:

   ![Új projekt](./media/oracle-to-sql-on-azure-vm-guide/new-project.png)

1. Válassza **a Kapcsolódás az Oracle-hoz** lehetőséget. Adja meg az Oracle-kapcsolat adatainak értékét a **Kapcsolódás az Oracle** -hez párbeszédpanelen:

   ![Kapcsolódás az Oracle-hoz](./media/oracle-to-sql-on-azure-vm-guide/connect-to-oracle.png)

   Válassza ki az áttelepíteni kívánt Oracle-sémát (ka) t: 

   ![Oracle-séma kiválasztása](./media/oracle-to-sql-on-azure-vm-guide/select-schema.png)

1. Kattintson a jobb gombbal az áttelepíteni kívánt Oracle-sémára az **Oracle metadata Explorerben**, majd válassza a **jelentés létrehozása** lehetőséget. Ekkor létrejön egy HTML-jelentés. Azt is megteheti, hogy az adatbázis kiválasztása után kiválasztja a **jelentés létrehozása** lehetőséget a navigációs sávon:

   ![Jelentés létrehozása](./media/oracle-to-sql-on-azure-vm-guide/create-report.png)

1. Az **Oracle metadata Explorerben** válassza ki az Oracle-sémát, majd válassza a **jelentés létrehozása** lehetőséget a HTML-jelentés konvertálási statisztikákkal és hibákkal/figyelmeztetésekkel való létrehozásához.
1. A HTML-jelentés áttekintésével megismerheti a konverziós statisztikákat és az esetleges hibákat vagy figyelmeztetéseket. A jelentést az Excelben is megnyithatja, hogy beolvassa az Oracle-objektumok leltárát, valamint a séma átalakításának végrehajtásához szükséges erőfeszítést. A jelentés alapértelmezett helye a SSMAProjects belüli jelentési mappában található. 

   Például: `drive:\<username>\Documents\SSMAProjects\MyOracleMigration\report\report_2016_11_12T02_47_55\`
    
   ![Átalakítási jelentés](./media/oracle-to-sql-on-azure-vm-guide/conversion-report.png)

### <a name="validate-data-types"></a>Adattípusok ellenőrzése

Érvényesítse az alapértelmezett adattípus-leképezéseket, és szükség esetén módosítsa azokat a követelmények alapján. Ehhez kövesse az alábbi lépéseket: 

1. Válassza az **eszközök** lehetőséget a menüből. 
1. Válassza a **projekt beállításai** lehetőséget. 
1. Válassza a **típus-hozzárendelések** fület:

   ![Típus-hozzárendelések](./media/oracle-to-sql-on-azure-vm-guide/type-mappings.png)

1. Az egyes táblák típus-hozzárendelését az **Oracle metadata Explorerben** található táblázat kiválasztásával módosíthatja. 

### <a name="convert-schema"></a>Séma konvertálása

A séma konvertálásához kövesse az alábbi lépéseket: 

1. Választható Dinamikus vagy alkalmi lekérdezések konvertálásához kattintson a jobb gombbal a csomópontra, majd válassza az **utasítás hozzáadása** parancsot.
1. Válassza a **kapcsolódás SQL Server** lehetőséget a legfelső szintű navigációs sávon. 
     1. Adja meg az Azure-beli virtuális gépen SQL Server kapcsolati adatait. 
     1. Válassza ki a célként megadott adatbázist a legördülő listából, vagy adjon meg egy új nevet, amely esetben a rendszer létrehoz egy adatbázist a célkiszolgálón. 
     1. Adja meg a hitelesítés részleteit. 
     1. Válassza a **Kapcsolódás** lehetőséget. 

   ![Kapcsolódás az SQL-hez](./media/oracle-to-sql-on-azure-vm-guide/connect-to-sql-vm.png)

1. Kattintson a jobb gombbal az Oracle-sémára az **Oracle metadata Explorerben** , és válassza a **séma konvertálása** lehetőséget. Azt is megteheti, hogy kijelöli a **séma konvertálása** lehetőséget a felső vonal navigációs sávján:

   ![Séma konvertálása](./media/oracle-to-sql-on-azure-vm-guide/convert-schema.png)

1. Az átalakítás befejezése után hasonlítsa össze és tekintse át az átalakított objektumokat az eredeti objektumokra a lehetséges problémák azonosításához és a javaslatok alapján történő kezeléséhez:

   ![Javaslatok áttekintése](./media/oracle-to-sql-on-azure-vm-guide/table-mapping.png)

   Hasonlítsa össze a konvertált Transact-SQL-szöveget az eredeti tárolt eljárásokkal, és tekintse át a javaslatokat: 

   ![Ajánlási kód áttekintése](./media/oracle-to-sql-on-azure-vm-guide/procedure-comparison.png)

   A projektet helyileg is mentheti egy offline séma szervizelési gyakorlatához. Ezt úgy teheti meg, ha a **fájl** menüben a **projekt mentése** lehetőséget választja. Ez lehetőséget nyújt arra, hogy a forrás-és a célként megadott sémákat offline állapotba hozza, és szervizelést végezzen, mielőtt közzé tudja tenni a sémát a SQL Server.

1. Válassza az **eredmények áttekintése** lehetőséget a kimenet ablaktáblán, és tekintse át a hibákat a hibák **listája** ablaktáblán. 
1. Mentse a projektet helyileg a kapcsolat nélküli séma szervizelési gyakorlatához. Válassza a **projekt mentése** lehetőséget a **fájl** menüből. Ez lehetőséget biztosít a forrás-és a célként megadott sémák offline állapotba helyezésére és szervizelésre, mielőtt közzé tudja tenni a sémát az Azure-beli virtuális gépen SQL Server.


## <a name="migrate"></a>Migrate

Miután elvégezte a szükséges előfeltételeket, és végrehajtotta az **áttelepítés előtti** fázishoz kapcsolódó feladatokat, készen áll a séma és az adatok áttelepítésére. Az áttelepítés két lépést tesz szükségessé – a séma közzétételét és az adatáttelepítést. 


A séma közzétételéhez és az adatáttelepítés végrehajtásához kövesse az alábbi lépéseket: 

1. Tegye közzé a sémát: kattintson a jobb gombbal az adatbázisra a **SQL Server metaadat-kezelőben**  , majd válassza az **adatbázissal való szinkronizálás** lehetőséget. Ez a művelet közzéteszi az Oracle-sémát, hogy SQL Server az Azure-beli virtuális gépen:

   ![Szinkronizálás adatbázissal](./media/oracle-to-sql-on-azure-vm-guide/synchronize-database.png)

   Tekintse át a forrás projekt és a cél közötti leképezést:

   ![Szinkronizálás állapotának áttekintése](./media/oracle-to-sql-on-azure-vm-guide/synchronize-database-review.png)


1. Migrálja az adatokat: kattintson a jobb gombbal arra az adatbázisra vagy objektumra, amelyet az **Oracle metadata Explorerben** szeretne áttelepíteni, majd válassza az **adatok áttelepíteni** lehetőséget. Választhatja azt is, hogy az **adatok áttelepíthetők** a legfelső szintű navigációs sávon. Ha egy teljes adatbázisra szeretné áttelepíteni az adatátvitelt, jelölje be az adatbázis neve melletti jelölőnégyzetet. Ha az adatok áttelepíthetők az egyes táblákból, bontsa ki az adatbázist, majd a táblák csomópontot, és jelölje be a tábla melletti jelölőnégyzetet. Ha az adatok kihagyása az egyes táblákból, törölje a jelet a jelölőnégyzetből:

   ![Az adatáttelepítés](./media/oracle-to-sql-on-azure-vm-guide/migrate-data.png)

1. Adja meg az Oracle és a SQL Server kapcsolati adatait az Azure-beli virtuális gépen a párbeszédpanelen.
1. Az áttelepítés befejezése után tekintse meg az **adatáttelepítési jelentést**:  

    ![Adatáttelepítési jelentés](./media/oracle-to-sql-on-azure-vm-guide/data-migration-report.png)

1. Kapcsolódjon a SQL Serverhoz az Azure VM-példányon [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) használatával, és ellenőrizze az áttelepítést az adatelemzés és a séma áttekintésével:

   ![Érvényesítés a SSMA](./media/oracle-to-sql-on-azure-vm-guide/validate-in-ssms.png)


A SSMA használata mellett a SQL Server Integration Services (SSIS) használatával is áttelepítheti az adatok áttelepíthetők. További információ: 
- A cikk a [SQL Server Integration Services első lépések](https://docs.microsoft.com//sql/integration-services/sql-server-integration-services).
- A tanulmány [SQL Server Integration Services: SSIS for Azure és hibrid adatáthelyezés](https://download.microsoft.com/download/D/2/0/D20E1C5F-72EA-4505-9F26-FEF9550EFD44/SSIS%20Hybrid%20and%20Azure.docx).



## <a name="post-migration"></a>A migrálás után 

Miután sikeresen elvégezte az **áttelepítési** szakaszt, át kell haladnia az áttelepítés utáni feladatok sorozatán, hogy minden a lehető legzökkenőmentesen és hatékonyan működjön.

### <a name="remediate-applications"></a>Alkalmazások szervizelése

Miután áttelepítette az adatátvitelt a cél környezetbe, az összes olyan alkalmazásnak, amely korábban használta a forrást, el kell kezdenie a cél felhasználását. Ennek elvégzése bizonyos esetekben szükségessé teszi az alkalmazások módosítását.

Az [adathozzáférés áttelepítési eszközkészlete](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit) egy olyan bővítmény a Visual Studio Code-hoz, amely lehetővé teszi a Java-forráskód elemzését és az adatelérési API-hívások és lekérdezések észlelését, és az új adatbázis-háttér támogatásához szükséges egyetlen ablaktáblából álló nézetet biztosít. További információ: [Java-alkalmazás migrálása az Oracle](https://techcommunity.microsoft.com/t5/microsoft-data-migration/migrate-your-java-applications-from-oracle-to-sql-server-with/ba-p/368727) blogból. 

### <a name="perform-tests"></a>Tesztek végrehajtása

Az adatbázis-áttelepítés tesztelési módszere a következő tevékenységek elvégzését tartalmazza:

1. **Ellenőrzési tesztek fejlesztése**. Az adatbázis-áttelepítés teszteléséhez SQL-lekérdezéseket kell használnia. Létre kell hoznia az érvényesítési lekérdezéseket a forrás-és a célként megadott adatbázisokon való futtatáshoz. Az érvényesítési lekérdezéseknek le kell fedniük a definiált hatókört.

2. **Tesztkörnyezet beállítása**. A tesztkörnyezet a forrás-adatbázis és a céladatbázis másolatát is tartalmazza. Ügyeljen arra, hogy elkülönítse a tesztkörnyezet.

3. **Ellenőrzési tesztek futtatása**. Futtassa az ellenőrző teszteket a forráson és a célon, majd elemezze az eredményeket.

4. **Teljesítménytesztek futtatása**. Futtasson teljesítménytesztet a forráson és a célon, majd elemezze és hasonlítsa össze az eredményeket.

### <a name="optimize"></a>Optimalizálás

Az áttelepítés utáni fázis elengedhetetlen az adatok pontosságával kapcsolatos problémák összeegyeztetéséhez és a teljesség ellenőrzéséhez, valamint a számítási feladatok teljesítményével kapcsolatos problémák kezeléséhez.

> [!Note]
> További információ ezekről a problémákról és az azok enyhítésére szolgáló konkrét lépésekről: [áttelepítés utáni érvényesítés és optimalizálási útmutató](/sql/relational-databases/post-migration-validation-and-optimization-guide).


## <a name="migration-assets"></a>Áttelepítési eszközök 

Ha további segítségre van az áttelepítési forgatókönyv végrehajtásával kapcsolatban, tekintse meg a következő forrásokat, amelyek a valós idejű migrációs projektek támogatásában lettek kifejlesztve.

| **Cím/hivatkozás**                                                                                                                                          | **Leírás**                                                                                                                                                                                                                                                                                                   |
| ------------------------------------------------------------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [Adatmunkaterhelés-felmérési modell és eszköz](https://github.com/Microsoft/DataMigrationTeam/tree/master/Data%20Workload%20Assessment%20Model%20and%20Tool) | Ez az eszköz a javasolt "legmegfelelőbb" cél platformot, a felhő készültségét, valamint az alkalmazások/adatbázisok szervizelési szintjét biztosítja egy adott munkaterhelés esetében. Egyszerű, egykattintásos számítási és jelentéskészítési lehetőséget kínál, amely nagy mértékben segíti a nagyméretű ingatlanok értékelését azáltal, hogy biztosítja és automatizálja a célzott platformra vonatkozó döntési folyamatokat.                                                          |
| [Oracle Inventory parancsfájl-összetevők](https://github.com/Microsoft/DataMigrationTeam/tree/master/Oracle%20Inventory%20Script%20Artifacts)                 | Ez az objektum olyan PL/SQL-lekérdezést tartalmaz, amely megkeresi az Oracle rendszertáblázatokat, és az objektumok számát a séma típusa, az Objektumtípus és az állapot szerint adja meg. Emellett az egyes sémákban a "nyers adatmennyiség", valamint az egyes sémákban lévő táblázatok méretezése is durva becslést ad, a CSV-formátumban tárolt eredményekkel együtt.                                                                                                               |
| [SSMA Oracle Assessment Collection &-összevonás automatizálása](https://github.com/microsoft/DataMigrationTeam/tree/master/IP%20and%20Scripts/Automate%20SSMA%20Oracle%20Assessment%20Collection%20%26%20Consolidation)                                             | Ez az erőforrás egy. csv-fájlt használ belépésként (sources.csv a projekt mappáiban) a SSMA-értékelés konzolos módban való futtatásához szükséges XML-fájlok létrehozásához. A source.csv az ügyfél a meglévő Oracle-példányok leltára alapján kapja meg. A kimeneti fájlok AssessmentReportGeneration_source_1.xml, ServersConnectionFile.xml és VariableValueFile.xml.|
| [SSMA általános Oracle-hibákhoz és azok javításához](https://aka.ms/dmj-wp-ssma-oracle-errors)                                                           | Az Oracle használatával a WHERE záradékban nem skaláris feltételt rendelhet hozzá. A SQL Server azonban nem támogatja ezt a típusú feltételt. Ennek eredményeképpen az Oracle SQL Server Migration Assistant (SSMA) nem konvertálja a WHERE záradékban nem skaláris feltétellel rendelkező lekérdezéseket, hanem hiba-O2SS0001 generál. Ez a tanulmány további részleteket tartalmaz a problémáról és annak megoldási módjairól.          |
| [Oracle – SQL Server áttelepítési kézikönyv](https://github.com/microsoft/DataMigrationTeam/blob/master/Whitepapers/Oracle%20to%20SQL%20Server%20Migration%20Handbook.pdf)                | Ez a dokumentum az Oracle-sémák a SQL Server legújabb verziójára való áttelepítésével kapcsolatos feladatokra koncentrál. Ha az áttelepítéshez a funkciók/funkciók módosítása szükséges, akkor az adatbázist használó alkalmazások változásainak lehetséges hatását körültekintően kell figyelembe venni.                                                     |

Ezek az erőforrások az Azure adatcsoport-mérnöki csapat által szponzorált adatsql ninja program részeként lettek kifejlesztve. Az adatelemzési program alapszintű alapokmánya az, hogy feloldja az összetett modernizációt, és az adatplatform-migrációs lehetőségeket a Microsoft Azure-beli adatplatformján is felgyorsítja. Ha úgy gondolja, hogy a szervezete szeretne részt venni az adatsql ninja programban, forduljon a fiókhoz, és kérje meg, hogy küldje el a jelölést.

## <a name="next-steps"></a>Következő lépések

- A SQL Server alkalmazandó szolgáltatások rendelkezésre állásának vizsgálatához tekintse meg az [Azure globális infrastruktúra-központot](https://azure.microsoft.com/global-infrastructure/services/?regions=all&amp;products=synapse-analytics,virtual-machines,sql-database)

- A Microsoft és a harmadik féltől származó szolgáltatások és eszközök egy olyan mátrixa, amely a különböző adatbázis-és adatáttelepítési forgatókönyvek, valamint a speciális feladatok elvégzéséhez nyújt segítséget, tekintse meg a cikk [szolgáltatás és eszközök az adatok áttelepítéséhez](../../../dms/dms-tools-matrix.md) című témakört.

- További információ az Azure SQL-ről:
   - [Üzembe helyezési lehetőségek](../../azure-sql-iaas-vs-paas-what-is-overview.md)
   - [Azure-beli virtuális gépeken futó SQL Server](../../virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview.md)
   - [Az Azure teljes tulajdonlási költsége kalkulátor](https://azure.microsoft.com/pricing/tco/calculator/) 


- Ha többet szeretne megtudni a Felhőbeli Migrálás keretrendszerével és bevezetési ciklusával kapcsolatban, olvassa el a következőt:
   -  [Felhőbevezetési keretrendszer az Azure-hoz](/azure/cloud-adoption-framework/migrate/azure-best-practices/contoso-migration-scale)
   -  [Ajánlott eljárások az Azure-ra való áttéréssel kapcsolatos díjszabási és méretezési feladatok elvégzéséhez](/azure/cloud-adoption-framework/migrate/azure-best-practices/migrate-best-practices-costs) 

- További információ a licencelésről:
   - [Saját licenc használata a Azure Hybrid Benefit](../../virtual-machines/windows/licensing-model-azure-hybrid-benefit-ahb-change.md)
   - [A SQL Server 2008 és SQL Server 2008 R2 ingyenes kibővített támogatása](../../virtual-machines/windows/sql-server-2008-extend-end-of-support.md)


- Az alkalmazás-hozzáférési réteg értékeléséhez lásd: [adathozzáférés áttelepítési eszközkészlete (előzetes verzió)](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit)
- Az A/B típusú adatelérési réteg végrehajtásával kapcsolatos további információkért lásd: [Database Experimentation Assistant](/sql/dea/database-experimentation-assistant-overview).

