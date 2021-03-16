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
ms.openlocfilehash: f9b6dea216e05bb645daf5fdd041cec692821af8
ms.sourcegitcommit: 18a91f7fe1432ee09efafd5bd29a181e038cee05
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/16/2021
ms.locfileid: "103565027"
---
# <a name="migration-guide-oracle-to-sql-server-on-azure-vm"></a>Áttelepítési útmutató: Oracle SQL Server Azure-beli virtuális gépen
[!INCLUDE[appliesto-sqldb-sqlmi](../../includes/appliesto-sqldb.md)]

Ez az útmutató azt ismerteti, hogy az Oracle-sémák áttelepíthetők az Azure-beli virtuális gépen SQL Server az Oracle-SQL Server Migration Assistant használatával. 

Más forgatókönyvek esetén tekintse meg az [adatbázis áttelepítési Útmutatóját](https://datamigration.microsoft.com/).

## <a name="prerequisites"></a>Előfeltételek 

Az Oracle-sémának az Azure-beli virtuális gépen SQL Serverre való átmigrálása a következőkre van szüksége:

- Annak ellenőrzéséhez, hogy a forrás-környezet támogatott-e.
- Az [Oracle SQL Server Migration Assistant (SSMA)](https://www.microsoft.com/en-us/download/details.aspx?id=54258)letöltése.
- A cél [SQL Server VM](../../virtual-machines/windows/sql-vm-create-portal-quickstart.md).
- Az Oracle és a [Provider](/sql/ssma/oracle/connect-to-oracle-oracletosql) [SSMA szükséges engedélyei](/sql/ssma/oracle/connecting-to-oracle-database-oracletosql) .

## <a name="pre-migration"></a>A migrálás előtt

A felhőbe való Migrálás előkészítése során ellenőrizze, hogy a forrás-környezet támogatott-e, és hogy rendelkezik-e az előfeltételekkel. Ez segít a hatékony és sikeres Migrálás biztosításában.

A folyamat ezen része magában foglalja az áttelepíteni kívánt adatbázisok leltárának elvégzését, a lehetséges áttelepítési problémák vagy blokkolók kiértékelését, valamint az esetlegesen feltárt elemek feloldását. 

### <a name="discover"></a>Felderítés

A [map Toolkit](https://go.microsoft.com/fwlink/?LinkID=316883) segítségével azonosíthatja a meglévő adatforrásokat és a vállalat által használt funkciók részleteit, így jobban megismerheti és megtervezheti az áttelepítést. Ez a folyamat a hálózat vizsgálatával azonosítja az összes szervezet Oracle-példányát, valamint a használatban lévő verziót és szolgáltatásokat.

Ha a MAP Toolkit használatával szeretne leltározási vizsgálatot végezni, kövesse az alábbi lépéseket: 

1. Nyissa meg a [Térkép eszközkészletet](https://go.microsoft.com/fwlink/?LinkID=316883).
1. Válassza az **adatbázis létrehozása/kiválasztása** lehetőséget.
1. Válassza a **leltár-adatbázis létrehozása** lehetőséget, adja meg a létrehozandó új leltár-adatbázis nevét, adjon meg egy rövid leírást, majd kattintson **az OK gombra**. 
1. Válassza a **leltári adatok gyűjtése** lehetőséget a **leltár és értékelés varázsló** megnyitásához. 
1. A **leltár és értékelés varázslóban** válassza az **Oracle** lehetőséget, majd kattintson a **Tovább gombra**. 
1. Válassza ki az üzleti igényeinek és környezetének legmegfelelőbb számítógép-keresési lehetőséget, majd válassza a **Next (tovább**) gombot: 
1. Adja meg a hitelesítő adatokat, vagy hozzon létre új hitelesítő adatokat a vizsgálni kívánt rendszerekhez, majd kattintson a **tovább** gombra.
1. Állítsa be a hitelesítő adatok sorrendjét, majd kattintson a **tovább** gombra. 
1. Határozza meg a felderíteni kívánt számítógépek hitelesítő adatait. Minden számítógépen vagy gépen egyedi hitelesítő adatokat használhat, vagy az **összes számítógép hitelesítő adatainak** listáját is használhatja.  
1. Ellenőrizze a kiválasztási összegzést, majd kattintson a **Befejezés gombra**.
1. A vizsgálat befejezését követően tekintse meg az **adatgyűjtés** összegző jelentését. A vizsgálat eltarthat néhány percig, és az adatbázisok számától függ. Ha elkészült, válassza a **Bezárás** lehetőséget. 
1. Válassza a **Beállítások lehetőséget** , ha jelentést szeretne készíteni az Oracle Assessment és az adatbázis részleteiről. A jelentés létrehozásához jelölje ki mindkét beállítást (eggyel egyet).


### <a name="assess"></a>Kiértékelés

Az adatforrások azonosítása után az [oracle SQL Server Migration Assistant (SSMA)](https://www.microsoft.com/en-us/download/details.aspx?id=54258) segítségével mérje fel, hogy az Oracle-példány (ok) át legyenek-e migrálva a SQL Server VMba, hogy megértse a kettő közötti réseket. Az áttelepítési segéd segítségével áttekintheti az adatbázis-objektumokat és-adatforrásokat, kiértékelheti az áttelepítésre használt adatbázisokat, áttelepítheti az adatbázis-objektumokat SQL Serverba, majd átSQL Server telepítheti

Az értékelés létrehozásához kövesse az alábbi lépéseket: 

1. Nyissa meg az  [Oracle SQL Server Migration Assistant (SSMA)](https://www.microsoft.com/en-us/download/details.aspx?id=54258). 
1. Válassza a **fájl** , majd az **új projekt** lehetőséget. 
1. Adja meg a projekt nevét, egy helyet a projekt mentéséhez, majd válasszon egy SQL Server áttelepítési célt a legördülő menüből. Válassza az **OK** lehetőséget. 
1. Adja meg az Oracle-kapcsolat adatainak értékét a **Kapcsolódás az Oracle** -hez párbeszédpanelen.
1. Kattintson a jobb gombbal az áttelepíteni kívánt Oracle-sémára az **Oracle metadata Explorerben**, majd válassza a **jelentés létrehozása** lehetőséget. Ekkor létrejön egy HTML-jelentés. Azt is megteheti, hogy az adatbázis kiválasztása után kiválasztja a **jelentés létrehozása** lehetőséget a navigációs sávon.

1. Az **Oracle metadata Explorerben** válassza ki az Oracle-sémát, majd válassza a **jelentés létrehozása** lehetőséget a HTML-jelentés konvertálási statisztikákkal és hibákkal/figyelmeztetésekkel való létrehozásához.
1. Tekintse át az átalakítási statisztikák HTML-jelentését, valamint a hibákat és a figyelmeztetéseket. Elemezze, hogy megértse az átalakítási problémákat és a megoldásokat.

   Ez a jelentés a SSMA projects mappából is elérhető az első képernyőn kiválasztott módon. A fenti példában keresse meg a report.xml fájlt a következő helyről: 

   `drive:\<username>\Documents\SSMAProjects\MyOracleMigration\report\report_2016_11_12T02_47_55\`

    majd nyissa meg az Excelben, hogy beolvassa az Oracle-objektumok leltárát, és hogy milyen erőfeszítést kell végrehajtani a séma átalakításához.


### <a name="validate-data-types"></a>Adattípusok ellenőrzése

Érvényesítse az alapértelmezett adattípus-leképezéseket, és szükség esetén módosítsa azokat a követelmények alapján. Ehhez kövesse az alábbi lépéseket: 

1. Válassza az **eszközök** lehetőséget a menüből. 
1. Válassza a **projekt beállításai** lehetőséget. 
1. Válassza a **típus-hozzárendelések** lapot. 
1. Az egyes táblák típus-hozzárendelését az **Oracle metadata Explorerben** található táblázat kiválasztásával módosíthatja. 



### <a name="convert-schema"></a>Séma konvertálása

A séma konvertálásához kövesse az alábbi lépéseket: 

1. Választható Dinamikus vagy alkalmi lekérdezések konvertálásához kattintson a jobb gombbal a csomópontra, majd válassza az **utasítás hozzáadása** parancsot.
1. Válassza a **Kapcsolódás a SQL Server** a legfelső szintű navigációs sávon lehetőséget, és adja meg az Azure-beli virtuális gépen található SQL Server kapcsolati adatait. Választhat, hogy meglévő adatbázishoz csatlakozik, vagy új nevet ad meg, ebben az esetben a rendszer létrehoz egy adatbázist a célkiszolgálón.
1. Kattintson a jobb gombbal a sémára, és válassza a **séma konvertálása** lehetőséget.
1. Miután a séma elkészült, hasonlítsa össze és tekintse át a séma struktúráját a lehetséges problémák azonosításához.

   A projektet helyileg is mentheti egy offline séma szervizelési gyakorlatához. Ezt úgy teheti meg, ha a **fájl** menüben a **projekt mentése** lehetőséget választja. Ez lehetőséget nyújt arra, hogy a forrás-és a célként megadott sémákat offline állapotba hozza, és szervizelést végezzen, mielőtt közzé tudja tenni a sémát a SQL Server.


## <a name="migrate"></a>Migrate

Miután elvégezte a szükséges előfeltételeket, és végrehajtotta az **áttelepítés előtti** fázishoz kapcsolódó feladatokat, készen áll a séma és az adatok áttelepítésére. Az áttelepítés két lépést tesz szükségessé – a séma közzétételét és az adatáttelepítést. 


A séma közzétételéhez és az adatáttelepítés végrehajtásához kövesse az alábbi lépéseket: 

1. Kattintson a jobb gombbal az adatbázisra a **SQL Server metadata Explorerben**  , majd válassza az **adatbázissal való szinkronizálás** lehetőséget. Ez a művelet közzéteszi az Oracle-sémát, hogy SQL Server az Azure-beli virtuális gépen. 
1. Kattintson a jobb gombbal az Oracle-sémára az **Oracle metadata Explorerben** , és válassza az **adatok áttelepíteni** lehetőséget. Azt is megteheti, hogy az adatok áttelepíthetők a legfelső szintű navigációs sávon.
1. Adja meg az Oracle és a SQL Server kapcsolati adatait az Azure-beli virtuális gépen a párbeszédpanelen.
1. Az áttelepítés befejezése után tekintse meg az adatáttelepítési jelentést:
1. Kapcsolódjon SQL Server Azure-beli virtuális gépen az [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) segítségével, és tekintse át az SQL Server-példány adatait és sémáját. 


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
| [Oracle – SQL Server áttelepítési kézikönyv](https://github.com/microsoft/DataMigrationTeam/blob/master/Whitepapers/Oracle%20to%20SQL%20Server%20Migration%20Handbook.pdf)                | Ez a dokumentum az Oracle-sémák az SQL serverbase legújabb verziójára való áttelepítésével kapcsolatos feladatokra koncentrál. Ha az áttelepítéshez a funkciók/funkciók módosítása szükséges, akkor az adatbázist használó alkalmazások változásainak lehetséges hatását körültekintően kell figyelembe venni.                                                     |

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

