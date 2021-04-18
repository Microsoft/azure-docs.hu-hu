---
title: 'Db2–SQL Server Azure-beli virtuális gépen: Migrálási útmutató'
description: Ez az útmutató az IBM Db2-adatbázisok Azure-beli virtuális SQL Server való áttelepítését tanítja be az SQL Server Migration Assistant for Db2 használatával.
ms.custom: ''
ms.service: virtual-machines-sql
ms.subservice: migration-guide
ms.devlang: ''
ms.topic: how-to
author: markjones-msft
ms.author: markjon
ms.reviewer: mathoma
ms.date: 11/06/2020
ms.openlocfilehash: 43eff2bea6f6d95291e9ba9650ff42187e39fc70
ms.sourcegitcommit: 950e98d5b3e9984b884673e59e0d2c9aaeabb5bb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2021
ms.locfileid: "107600165"
---
# <a name="migration-guide-ibm-db2-to-sql-server-on-azure-vm"></a>Migrálási útmutató: Az IBM Db2 SQL Server Azure-beli virtuális gépen
[!INCLUDE[appliesto--sqlmi](../../includes/appliesto-sqlvm.md)]

Ez az útmutató a felhasználói adatbázisok IBM Db2-ről Azure-beli virtuális gépen SQL Server virtuális gépre való áttelepítését az SQL Server Migration Assistant for Db2 használatával. 

További migrálási útmutatókért lásd az [Azure Database migrálási útmutatóját.](https://docs.microsoft.com/data-migration) 

## <a name="prerequisites"></a>Előfeltételek

A Db2-adatbázis áttelepítése a SQL Server a következőre lesz szüksége:

- Annak ellenőrzéséhez, hogy a [forráskörnyezet támogatott-e.](/sql/ssma/db2/installing-ssma-for-Db2-client-Db2tosql#prerequisites)
- [SQL Server Migration Assistant (SSMA) a Db2-hez.](https://www.microsoft.com/download/details.aspx?id=54254)
- [Kapcsolat](../../virtual-machines/windows/ways-to-connect-to-sql.md) a forráskörnyezet és a SQL Server VM Azure-ban. 
- Célként [SQL Server Azure-beli virtuális gépen.](../../virtual-machines/windows/create-sql-vm-portal.md) 

## <a name="pre-migration"></a>A migrálás előtt

Az előfeltételek teljesültével készen áll arra, hogy felfedezze a környezet topológiáját, és felmérje a migrálás megvalósíthatóságát. 

### <a name="assess"></a>Kiértékelés 

Az SSMA for DB2 használatával áttekintheti az adatbázis-objektumokat és adatokat, és kiértékelheti az adatbázisokat az áttelepítéshez. 

Értékelés létrehozásához kövesse az alábbi lépéseket:

1. Nyissa meg [az SSMA for Db2-t.](https://www.microsoft.com/download/details.aspx?id=54254) 
1. Válassza **a Fájl Új** projekt  >  **lehetőséget.** 
1. Adja meg a projekt nevét és a projekt mentésének helyét. Ezután válasszon ki egy SQL Server áttelepítési célt a legördülő listából, majd kattintson az **OK gombra.**

   :::image type="content" source="media/db2-to-sql-on-azure-vm-guide/new-project.png" alt-text="A megadni kívánt projekt részleteit bemutató képernyőkép.":::


1. A **Csatlakozás a Db2-hez oldalon** adja meg a Db2 kapcsolati adatok értékeit.

   :::image type="content" source="media/db2-to-sql-on-azure-vm-guide/connect-to-Db2.png" alt-text="Képernyőkép a Db2-példányhoz való csatlakozás lehetőségeiről.":::


1. Kattintson a jobb gombbal az átemelni kívánt Db2 sémára, majd válassza a **Jelentés létrehozása lehetőséget.** Ezzel létrehoz egy HTML-jelentést. A séma kiválasztása  után a navigációs sáv jelentés létrehozása lehetőségét is választhatja.

   :::image type="content" source="media/db2-to-sql-on-azure-vm-guide/create-report.png" alt-text="Képernyőkép a jelentés létrehozásáról.":::

1. Tekintse át a HTML-jelentést a konverziós statisztikák, valamint az esetleges hibák és figyelmeztetések áttekintéséhez. A jelentést az Excelben is megnyithatja, így leltárt kaphat a Db2-objektumokról és a sémakonverziók végrehajtásához szükséges munkamennyiségről. A jelentés alapértelmezett helye a jelentésmappában található az *SSMAProjects projektben.*

   Példa: `drive:\<username>\Documents\SSMAProjects\MyDb2Migration\report\report_<date>`. 

   :::image type="content" source="media/db2-to-sql-on-azure-vm-guide/report.png" alt-text="A hibák vagy figyelmeztetések azonosításához áttekintett jelentés képernyőképe.":::


### <a name="validate-data-types"></a>Adattípusok ellenőrzése

Ellenőrizze az alapértelmezett adattípus-leképezéseket, és szükség esetén módosítsa őket a követelmények alapján. Ehhez kövesse az alábbi lépéseket: 

1. A **menüben** válassza az Eszközök lehetőséget. 
1. Válassza a **Project Settings (Projektbeállítások) lehetőséget.** 
1. Válassza a **Típusleképezések** lapot.

   :::image type="content" source="media/db2-to-sql-on-azure-vm-guide/type-mapping.png" alt-text="Képernyőkép a séma és típusleképezés kiválasztásáról.":::

1. Az egyes tábla típusleképezését a Db2 Metaadat-kezelő táblázatának **kiválasztásával módosíthatja.** 

### <a name="convert-schema"></a>Séma konvertálása 

A séma átalakításához kövesse az alábbi lépéseket:

1. (Nem kötelező) Dinamikus vagy alkalmi lekérdezések hozzáadása utasításokhoz. Kattintson a jobb gombbal a csomópontra, majd válassza az **Utasítások hozzáadása lehetőséget.** 
1. Válassza **a Csatlakozás SQL Server.** 
    1. Adja meg a kapcsolati adatokat az Azure-beli virtuális SQL Server példányhoz való csatlakozáshoz. 
    1. Választhat, hogy a célkiszolgálón lévő meglévő adatbázishoz csatlakozik, vagy új nevet ad meg egy új adatbázis létrehozásához a célkiszolgálón. 
    1. Adja meg a hitelesítési adatokat. 
    1. Válassza a **Kapcsolódás** lehetőséget.

    :::image type="content" source="../../../../includes/media/virtual-machines-sql-server-connection-steps/rm-ssms-connect.png" alt-text="Képernyőkép az Azure-beli virtuális gépen SQL Server adatokról.":::

1. Kattintson a jobb gombbal a sémára, majd válassza a **Séma átalakítása lehetőséget.** A séma kiválasztása  után a felső navigációs sáv Séma konvertálása lehetőségét is választhatja.

   :::image type="content" source="media/db2-to-sql-on-azure-vm-guide/convert-schema.png" alt-text="Képernyőkép a séma kiválasztásáról és átalakításáról.":::

1. Az átalakítás befejezése után hasonlítsa össze és tekintse át a séma struktúráját a lehetséges problémák azonosítása érdekében. A problémák megoldása a javaslatok alapján. 

   :::image type="content" source="media/db2-to-sql-on-azure-vm-guide/compare-review-schema-structure.png" alt-text="Képernyőkép a séma szerkezetének összehasonlításával és áttekintésével a lehetséges problémák azonosítása érdekében.":::

1. A Kimenet **panelen** válassza az Eredmények **áttekintése lehetőséget.** A **Hibalista panelen** tekintse át a hibákat. 
1. Mentse helyileg a projektet egy offline séma-szervizelési gyakorlathoz. A Fájl **menüben** válassza a **Projekt mentése lehetőséget.** Ez lehetővé teszi a forrás- és célsémák offline kiértékelését és szervizelését, mielőtt közzéteheti a sémát SQL Server Azure-beli virtuális gépen.

## <a name="migrate"></a>Migrate

Miután befejezte az adatbázisok kiértékelését és az eltéréseket, a következő lépés a migrálási folyamat végrehajtása.

A séma közzétételéhez és az adatok áttelepítéséhez kövesse az alábbi lépéseket:

1. Tegye közzé a sémát. A **SQL Server Metaadat-kezelőben** kattintson a jobb gombbal az **adatbázisra** az Adatbázisok csomópontban. Ezután válassza **a Szinkronizálás adatbázissal lehetőséget.**

   :::image type="content" source="media/db2-to-sql-on-azure-vm-guide/synchronize-with-database.png" alt-text="Képernyőkép az adatbázissal való szinkronizálás lehetőségről.":::

1. Az adatok áttelepítése. Kattintson a jobb gombbal az átemelni kívánt adatbázisra vagy objektumra a **Db2** Metaadat-kezelőben, majd válassza az **Adatok áttelepítése lehetőséget.** Másik lehetőségként választhatja az **Adatok áttelepítése lehetőséget a** navigációs sávon. Egy teljes adatbázis adatainak áttelepítéséhez jelölje be az adatbázis neve melletti jelölőnégyzetet. Az adatok egyes táblákból való áttelepítéséhez bontsa ki az adatbázist, bontsa ki a **Táblák** gombra, majd jelölje be a táblázat melletti jelölőnégyzetet. Az egyes táblák adatainak kihagyása érdekében törölje a jelölést.

   :::image type="content" source="media/db2-to-sql-on-azure-vm-guide/migrate-data.png" alt-text="Képernyőkép a séma kiválasztásáról és az adatok áttelepítésének kiválasztásáról.":::

1. Adja meg a Db2 és a SQL Server kapcsolati adatait. 
1. A migrálás befejezése után tekintse meg az **adatáttelepítési jelentést:**  

   :::image type="content" source="media/db2-to-sql-on-azure-vm-guide/data-migration-report.png" alt-text="Az adatáttelepítési jelentés áttekintési helyének képernyőképe.":::

1.  Csatlakozzon az Azure-beli virtuális SQL Server példányhoz az [SQL Server Management Studio.](/sql/ssms/download-sql-server-management-studio-ssms) A migrálás ellenőrzéséhez tekintse át az adatokat és a sémát.

   :::image type="content" source="media/db2-to-sql-on-azure-vm-guide/compare-schema-in-ssms.png" alt-text="Képernyőkép a séma összehasonlításával a SQL Server Management Studio.":::

## <a name="post-migration"></a>A migrálás után 

A migrálás befejezése után a migrálást követő feladatok sorát kell elvégeznie, hogy minden a lehető zökkenőmentesen és hatékonyan működjön.

### <a name="remediate-applications"></a>Alkalmazások szervize 

Az adatok célkörnyezetbe való migrálása után az összes olyan alkalmazásnak, amely korábban a forrást felhasználta, el kell kezdenie a cél fogyasztását. Ennek megvalósítása bizonyos esetekben az alkalmazások módosításait igényli.

### <a name="perform-tests"></a>Tesztek végrehajtása

A tesztelés a következő tevékenységekből áll:

1. **Ellenőrző tesztek fejlesztése:** Az adatbázis-migrálás tesztelésére SQL-lekérdezéseket kell használnia. Létre kell hoznia az érvényesítési lekérdezéseket, hogy a forrás- és a céladatbázison is fusson. Az érvényesítési lekérdezésnek ki kell fedi a megadott hatókört.
1. **A tesztkörnyezet beállítása:** A tesztkörnyezetnek tartalmaznia kell a forrásadatbázis és a céladatbázis másolatát. Mindenképpen különítse el a tesztkörnyezetet.
1. **Ellenőrző tesztek futtatása:** Futtassa az érvényesítési teszteket a forráson és a célon, majd elemezze az eredményeket.
1. **Teljesítményttesztek futtatása:** Futtatassa a teljesítményteszteket a forráson és a célon, majd elemezze és hasonlítsa össze az eredményeket.

## <a name="migration-assets"></a>Migrálási eszközök 

További segítségért tekintse meg a következő forrásforrásokat, amelyeket egy valós migrálási projekthez fejlesztettek ki:

|Objektum  |Description  |
|---------|---------|
|[Adatterhelés-értékelési modell és eszköz](https://github.com/Microsoft/DataMigrationTeam/tree/master/Data%20Workload%20Assessment%20Model%20and%20Tool)| Ez az eszköz javasolt "legmegfelelőbb" célplatformokat, felhőbeli készenlétet és alkalmazás-/adatbázis-szervizelési szintet biztosít egy adott számítási feladathoz. Egyszerű, egykattintásos számítást és jelentés-készítést kínál, amely segít felgyorsítani a nagy tulajdonnal kapcsolatos értékeléseket azáltal, hogy automatizált és egységes célplatform-döntési folyamatot biztosít.|
|[Db2 zOS-adateszközök felderítési és értékelési csomagja](https://github.com/microsoft/DataMigrationTeam/tree/master/DB2%20zOS%20Data%20Assets%20Discovery%20and%20Assessment%20Package)|Miután futtatta az SQL-szkriptet egy adatbázison, exportálhatja az eredményeket egy fájlba a fájlrendszeren. Számos fájlformátum támogatott, például a *.csv fájl, így külső eszközökben, például táblázatokban rögzítheti az eredményeket. Ez a módszer akkor lehet hasznos, ha könnyen meg szeretné osztani az eredményeket olyan csapatokkal, amelyeken nincs telepítve a Workbench.|
|[IBM Db2 LUW leltárszk szkriptek és összetevők](https://github.com/microsoft/DataMigrationTeam/tree/master/IBM%20DB2%20LUW%20Inventory%20Scripts%20and%20Artifacts)|Ez az objektum tartalmaz egy SQL-lekérdezést, amely eléri az IBM Db2 LUW 11.1-es verziójú rendszertábláit, és séma és objektumtípus szerint számolja az objektumok számát, az egyes sémákban található "nyers adatok" becsült becslését, valamint az egyes sémákban lévő táblák méretezését CSV formátumban tárolt eredményekkel.|
|[Db2 LUW tisztán skálázva az Azure-ban – beállítási útmutató](https://github.com/microsoft/DataMigrationTeam/blob/master/Whitepapers/DB2%20PureScale%20on%20Azure.pdf)|Ez az útmutató a Db2-megvalósítási terv kiindulási pontjaként szolgál. Bár az üzleti követelmények eltérőek lesznek, ugyanez az alapvető minta érvényes. Ez az architekturális minta OLAP-alkalmazásokhoz is használható az Azure-ban.|

Ezeket az erőforrásokat az SQL mérnöki csapata fejlesztette ki. Ennek a csapatnak az alapvető csomagja az adatplatform-migrálási projektek összetett modernizálásának feloldása és felgyorsítása a Microsoft Azure-adatplatformjára.

## <a name="next-steps"></a>Következő lépések

A migrálás után tekintse át a migrálás utáni [érvényesítési és optimalizálási útmutatót.](/sql/relational-databases/post-migration-validation-and-optimization-guide) 

A Különböző adatbázis- és adatáttelepítési forgatókönyvekhez rendelkezésre álló Microsoft- és külső szolgáltatásokról és eszközökről lásd: Adatáttelepítési [szolgáltatások és eszközök.](../../../dms/dms-tools-matrix.md)

Videótartalmakért lásd: [A migrálási folyamat áttekintése.](https://azure.microsoft.com/resources/videos/overview-of-migration-and-recommended-tools-services/)
