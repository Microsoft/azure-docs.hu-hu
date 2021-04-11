---
title: 'SAP-Azure SQL Database: áttelepítési útmutató'
description: Ebből az útmutatóból megtudhatja, hogyan telepítheti át az SAP-adatbázisok Azure SQL Database-adatbázisba való áttelepítését az SAP-adapter Server Enterprise SQL Server Migration Assistant használatával.
ms.service: sql-database
ms.subservice: migration-guide
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MashaMSFT
ms.author: mathoma
ms.date: 03/19/2021
ms.openlocfilehash: f4648c216a0b6d06309c0166aba501d4f3f02a10
ms.sourcegitcommit: d40ffda6ef9463bb75835754cabe84e3da24aab5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/07/2021
ms.locfileid: "107027516"
---
# <a name="migration-guide-sap-ase-to-azure-sql-database"></a>Áttelepítési útmutató: SAP-Azure SQL Database

[!INCLUDE[appliesto-sqldb-sqlmi](../../includes/appliesto-sqldb.md)]

Ebből az útmutatóból megtudhatja, [hogyan telepítheti át](https://azure.microsoft.com/migration/migration-journey) az SAP-adapterek kiszolgálói nagyvállalati (bevezetési) adatbázisait egy Azure SQL Database-adatbázisba [SQL Server áttelepítési](https://azure.microsoft.com/migration/migration-journey) asszisztens használatával az SAP-adapterek Server Enterprise

Más áttelepítési útmutatókért lásd: az [Azure Database áttelepítési útmutatója](https://docs.microsoft.com/data-migration). 

## <a name="prerequisites"></a>Előfeltételek 

Mielőtt megkezdené az SAP SE-adatbázis áttelepítését az SQL-adatbázisba, tegye a következőket:

- Ellenőrizze, hogy a forrás-környezet támogatott-e. 
- Töltse le és telepítse [az SQL Server Migration Assistant for SAP adaptív Server Enterprise (korábban SAP Sybase-](https://www.microsoft.com/en-us/download/details.aspx?id=54256)elősegítő).
- Győződjön meg arról, hogy rendelkezik kapcsolattal és megfelelő engedélyekkel a forrás és a cél eléréséhez.

## <a name="pre-migration"></a>A migrálás előtt

Az előfeltételek teljesítése után már készen áll a környezet topológiájának felderítésére és az Azure-beli [Felhőbeli áttelepítés](https://azure.microsoft.com/migration)megvalósíthatóságának felmérésére.

### <a name="assess"></a>Kiértékelés

Ha [SQL Server Migration Assistant (SSMA) protokollt használ az SAP adaptív kiszolgálói vállalat számára (a formálisan SAP Sybase-nal)](https://www.microsoft.com/en-us/download/details.aspx?id=54256), áttekintheti az adatbázis-objektumokat és-adatforrásokat, kiértékelheti az áttelepítéshez szükséges adatbázis-objektumokat, áttelepítheti a Sybase-adatbázis objektumait az SQL-adatbázisba, majd áttelepítheti az További információ: [SQL Server Migration Assistant for Sybase (SybaseToSQL)](/sql/ssma/sybase/sql-server-migration-assistant-for-sybase-sybasetosql).

Az értékelés létrehozásához tegye a következőket: 

1. Nyissa meg a SSMA a Sybase számára. 
1. Válassza a **fájl**, majd az **új projekt** lehetőséget. 
1. Az **új projekt** ablaktáblán adja meg a projekt nevét és helyét, majd az **áttelepítés** legördülő listában válassza a **Azure SQL Database** lehetőséget. 
1. Válassza az **OK** lehetőséget.
1. A **Kapcsolódás a Sybase-hoz** ablaktáblán adja meg az SAP-kapcsolat részleteit. 
1. Kattintson a jobb gombbal az áttelepíteni kívánt SAP-adatbázisra, majd válassza a **jelentés létrehozása** lehetőséget. Ez HTML-jelentést hoz létre. Azt is megteheti, hogy a jobb felső sarokban a **jelentés létrehozása** lapot választja.
1. A HTML-jelentés áttekintésével megismerheti az átalakítási statisztikákat és az esetleges hibákat vagy figyelmeztetéseket. A jelentést az Excelben is megnyithatja, hogy beolvassa az SAP bevezetési objektumok leltárát, valamint a séma átalakításához szükséges erőfeszítéseket. A jelentés alapértelmezett helye a SSMAProjects belüli jelentési mappában található. Például:

   `drive:\<username>\Documents\SSMAProjects\MySAPMigration\report\report_<date>` 

### <a name="validate-the-type-mappings"></a>A típus-hozzárendelések ellenőrzése

A séma átalakításának elvégzése előtt ellenőrizze az alapértelmezett adattípus-leképezéseket, vagy módosítsa azokat a követelmények alapján. Ezt úgy teheti meg, ha kijelöli az **eszközök**  >  **projekt beállításai** lehetőséget, vagy megváltoztathatja az egyes táblák típus-hozzárendelését úgy, hogy kijelöli a táblázatot az SAP betekintő **metaadat-Explorerben**.

### <a name="convert-the-schema"></a>A séma konvertálása

A séma konvertálásához tegye a következőket:

1. Választható Dinamikus vagy speciális lekérdezések konvertálásához kattintson a jobb gombbal a csomópontra, majd válassza az **utasítás hozzáadása** parancsot. 
1. Kattintson a **kapcsolódás Azure SQL Database** lapra, majd adja meg az SQL-adatbázis részleteit. Választhat, hogy meglévő adatbázishoz csatlakozik, vagy új nevet ad meg, ebben az esetben a rendszer létrehoz egy adatbázist a célkiszolgálón.
1. A **Sybase metaadat-kezelő** ablaktábláján kattintson a jobb gombbal arra az SAP-alapú betekintő sémára, amelyet használ, majd válassza a **séma konvertálása** lehetőséget. 
1. A séma konvertálása után hasonlítsa össze és tekintse át az átalakított struktúrát az eredeti struktúrában, és azonosítsa a lehetséges problémákat. 

   A séma konvertálása után a projekt helyileg menthető egy offline séma szervizelési gyakorlatához. Ehhez válassza a **fájl**  >  **Mentés projekt** lehetőséget. Ez lehetőséget biztosít a forrás-és a célként megadott sémák offline állapotba helyezésére és szervizelésre, mielőtt közzéteszi a sémát az SQL Database-ben.

1. A **kimenet** ablaktáblán válassza az **eredmények áttekintése** lehetőséget, és tekintse át az esetleges hibákat a hibák **listája** ablaktáblán. 
1. Mentse a projektet helyileg a kapcsolat nélküli séma szervizelési gyakorlatához. Ehhez válassza a **fájl**  >  **Mentés projekt** lehetőséget. Ez lehetőséget biztosít a forrás-és a célként megadott sémák offline állapotba helyezésére és szervizelésre, mielőtt közzéteszi a sémát az SQL Database-ben.

## <a name="migrate-the-databases"></a>Az adatbázisok migrálása 

Miután elvégezte a szükséges előfeltételeket, és végrehajtotta az *áttelepítés előtti* fázishoz társított feladatokat, készen áll a séma és az adatok áttelepítésére.

A séma közzétételéhez és az adatáttelepítés elvégzéséhez tegye a következőket: 

1. Tegye közzé a sémát. A **Azure SQL Database metadata Explorer** ablaktáblán kattintson a jobb gombbal az adatbázisra, majd válassza az **adatbázissal való szinkronizálás** lehetőséget. Ez a művelet közzéteszi az SAP-beli bevonási sémát az SQL Database-ben.

1. Telepítse át az adatátvitelt. Az SAP-beadási **metaadatok Explorer** ablaktábláján kattintson a jobb gombbal az áttelepíteni kívánt SAP-adatbázisra vagy objektumra, majd válassza az **adatok áttelepíteni** lehetőséget. Másik lehetőségként kiválaszthatja az **adatáttelepítés** fület a jobb felső sarokban. 

   Ha egy teljes adatbázisra szeretné áttelepíteni az adatátvitelt, jelölje be az adatbázis neve melletti jelölőnégyzetet. Ha az adatok áttelepíthetők az egyes táblákból, bontsa ki az adatbázist, majd a **táblák** csomópontot, és jelölje be a tábla melletti jelölőnégyzetet. Ha az adatok kihagyása az egyes táblákból, törölje a jelölőnégyzet jelölését. 
1. Az áttelepítés befejezése után tekintse meg az **adatáttelepítési jelentést**. 
1. Ellenőrizze az áttelepítést az adatelemzés és a séma áttekintésével. Ehhez [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms)használatával kapcsolódjon az SQL-adatbázishoz.

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


## <a name="next-steps"></a>Következő lépések

- A Microsoft és a harmadik féltől származó szolgáltatások és eszközök egy olyan mátrixa, amely a különböző adatbázis-és adatáttelepítési forgatókönyvek és speciális feladatok ellátásához nyújt segítséget, lásd: [szolgáltatás és eszközök az adatok áttelepítéséhez](../../../dms/dms-tools-matrix.md).

- A Azure SQL Databaseról további információt a következő témakörben talál:
   - [A SQL Database áttekintése](../../database/sql-database-paas-overview.md)
   - [Az Azure teljes tulajdonlási költsége kalkulátor](https://azure.microsoft.com/pricing/tco/calculator/)  

- Ha többet szeretne megtudni a Felhőbeli Migrálás keretrendszeréről és bevezetési ciklusáról, tekintse meg a következőt:
   -  [Felhőbevezetési keretrendszer az Azure-hoz](/azure/cloud-adoption-framework/migrate/azure-best-practices/contoso-migration-scale)
   -  [Ajánlott eljárások az Azure-ba való Migrálás díjszabásához és méretezéséhez](/azure/cloud-adoption-framework/migrate/azure-best-practices/migrate-best-practices-costs) 
   -  [Erőforrások Cloud Migration](https://azure.microsoft.com/migration/resources)

- Az alkalmazás-hozzáférési réteg értékeléséhez lásd: [adathozzáférés áttelepítési eszközkészlete (előzetes verzió)](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit).
- Az A/B típusú adatelérési réteg végrehajtásával kapcsolatos további információkért lásd: [Database Experimentation Assistant](/sql/dea/database-experimentation-assistant-overview).
