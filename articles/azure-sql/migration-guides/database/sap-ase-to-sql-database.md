---
title: 'SAP-Azure SQL Database: áttelepítési útmutató'
description: Ez az útmutató bemutatja, hogyan telepítheti át az SAP-Azure SQL Database az SAP-adapterek Server Enterprise SQL Server Migration Assistant használatával.
ms.service: sql-database
ms.subservice: migration-guide
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MashaMSFT
ms.author: mathoma
ms.date: 03/19/2021
ms.openlocfilehash: 81956a16142f314f54afd9d5a1b9055a559e906c
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "103565082"
---
# <a name="migration-guide-sap-ase-to-azure-sql-database"></a>Áttelepítési útmutató: SAP-Azure SQL Database
[!INCLUDE[appliesto-sqldb-sqlmi](../../includes/appliesto-sqldb.md)]

Ez az útmutató bemutatja, hogyan telepítheti át az SAP-Azure SQL Database az SAP-adapterek Server Enterprise SQL Server Migration Assistant használatával.

Más áttelepítési útmutatókért lásd: [adatbázis-áttelepítés](https://datamigration.microsoft.com/). 

## <a name="prerequisites"></a>Előfeltételek 

Ha az SAP SE-adatbázist át szeretné telepíteni Azure SQL Databasere, a következőkre lesz szüksége:

- annak ellenőrzéséhez, hogy a forrás-környezet támogatott-e. 
- [SQL Server Migration Assistant az SAP adaptív Server Enterprise (korábbi nevén SAP Sybase-központú)](https://www.microsoft.com/en-us/download/details.aspx?id=54256). 

## <a name="pre-migration"></a>A migrálás előtt

Az előfeltételek teljesítése után készen áll a környezet topológiájának felderítésére és az áttelepítés megvalósíthatóságának értékelésére.

### <a name="assess"></a>Kiértékelés

[SQL Server Migration Assistant (SSMA) használata az SAP adaptív kiszolgálói vállalat számára (formálisan SAP Sybase-](https://www.microsoft.com/en-us/download/details.aspx?id=54256) elősegítő) az adatbázis-objektumok és-adatelemzések áttekintéséhez, az adatbázisok áttelepítéséhez való áttelepítéséhez, a Sybase-adatbázis objektumainak áttelepítése Azure SQL Databasere, majd az adatáttelepítés Azure SQL Database További információ: [SQL Server Migration Assistant for Sybase (SybaseToSQL)](/sql/ssma/sybase/sql-server-migration-assistant-for-sybase-sybasetosql).

Az értékelés létrehozásához kövesse az alábbi lépéseket: 

1. Nyissa meg **a SSMA a Sybase számára**. 
1. Válassza a **fájl** , majd az **új projekt** lehetőséget. 
1. Adja meg a projekt nevét, a kívánt helyet a projekt mentéséhez, majd válassza a Azure SQL Database az áttelepítési célként lehetőséget a legördülő menüből. Válassza az **OK** lehetőséget.
1. A **Kapcsolódás a Sybase-hoz** párbeszédpanelen adja meg az SAP-kapcsolat részleteinek értékét. 
1. Kattintson a jobb gombbal az áttelepíteni kívánt SAP-adatbázisra, majd válassza a **jelentés létrehozása** parancsot. Ez HTML-jelentést hoz létre.
1. A HTML-jelentés áttekintésével megismerheti a konverziós statisztikákat és az esetleges hibákat vagy figyelmeztetéseket. A jelentés az Excelben is megnyitható a DB2-objektumok leltárának beszerzéséhez, valamint a séma átalakításához szükséges erőfeszítésekhez. A jelentés alapértelmezett helye a SSMAProjects belüli jelentési mappában található.

   Példa: `drive:\<username>\Documents\SSMAProjects\MyDB2Migration\report\report_<date>`. 


### <a name="validate-type-mappings"></a>Típus-hozzárendelések ellenőrzése

A séma átalakításának elvégzése előtt ellenőrizze az alapértelmezett adattípus-hozzárendeléseket, vagy módosítsa azokat a követelmények alapján. Ezt úgy teheti meg, hogy az **eszközök** menüre navigál, és a **Project Settings (beállítások** ) elemre kattint, vagy megváltoztathatja az egyes táblák típus-hozzárendelését, ha kijelöli a táblázatot az SAP betekintő **metaadatok Explorerben**


### <a name="convert-schema"></a>Séma konvertálása

A séma konvertálásához kövesse az alábbi lépéseket:

1. Választható Dinamikus vagy alkalmi lekérdezések konvertálásához kattintson a jobb gombbal a csomópontra, majd válassza az **utasítás hozzáadása** parancsot. 
1. Válassza a **kapcsolódás Azure SQL Database** lehetőséget a felső szintű navigációs sávon, és adja meg Azure SQL Database részleteit. Választhat, hogy meglévő adatbázishoz csatlakozik, vagy új nevet ad meg, ebben az esetben a rendszer létrehoz egy adatbázist a célkiszolgálón.
1. Kattintson a jobb gombbal a **Sybase metaadat-kezelőben** a SAP bekerülő sémára, és válassza a **séma konvertálása** lehetőséget. Azt is megteheti, hogy kijelöli a **séma konvertálása** lehetőséget a felső vonal navigációs sávján. 
1. Hasonlítsa össze és tekintse át a séma struktúráját a lehetséges problémák azonosításához. 

   A séma átalakítása után a projekt helyileg menthető egy offline séma szervizelési gyakorlatához. Válassza a **projekt mentése** lehetőséget a **fájl** menüből. Ez lehetőséget nyújt arra, hogy a forrás-és a célként megadott sémákat offline állapotba hozza, és szervizelést végezzen, mielőtt közzé tudja tenni a sémát a Azure SQL Database.

További információ: [séma konvertálása](/sql/ssma/sybase/converting-sybase-ase-database-objects-sybasetosql)


## <a name="migrate"></a>Migrate 

Miután elvégezte a szükséges előfeltételeket, és végrehajtotta az **áttelepítés előtti** fázishoz kapcsolódó feladatokat, készen áll a séma és az adatok áttelepítésére.

A séma közzétételéhez és az adatáttelepítés végrehajtásához kövesse az alábbi lépéseket: 

1. Kattintson a jobb gombbal az adatbázisra **Azure SQL Database metaadat-kezelőben** , majd válassza az **adatbázissal való szinkronizálás** lehetőséget.  Ez a művelet közzéteszi az SAP-t a Azure SQL Database példányon.
1. Kattintson a jobb gombbal az SAP-alapú adatközponti séma elemre az **SAP** -ben, és válassza az **adatok áttelepíteni** lehetőséget.  Választhatja azt is, hogy az **adatok áttelepíthetők** a legfelső szintű navigációs sávon.  
1. Az áttelepítés befejezése után tekintse meg az **adatáttelepítési jelentést**: 
1. Ellenőrizze az áttelepítést úgy, hogy áttekinti a Azure SQL Database példányon található adatnézetet és sémát Azure SQL Database Management Studio (SSMS) használatával.


## <a name="post-migration"></a>A migrálás után 

Miután sikeresen elvégezte az **áttelepítési** szakaszt, át kell haladnia az áttelepítés utáni feladatok sorozatán, hogy minden a lehető legzökkenőmentesen és hatékonyan működjön.

### <a name="remediate-applications"></a>Alkalmazások szervizelése

Miután áttelepítette az adatátvitelt a cél környezetbe, az összes olyan alkalmazásnak, amely korábban használta a forrást, el kell kezdenie a cél felhasználását. Ennek elvégzése bizonyos esetekben szükségessé teszi az alkalmazások módosítását.

### <a name="perform-tests"></a>Tesztek végrehajtása

Az adatbázis-áttelepítés tesztelési módszere a következő tevékenységek elvégzését tartalmazza:

1. **Ellenőrzési tesztek fejlesztése**. Az adatbázis-áttelepítés teszteléséhez SQL-lekérdezéseket kell használnia. Létre kell hoznia az érvényesítési lekérdezéseket a forrás-és a célként megadott adatbázisokon való futtatáshoz. Az érvényesítési lekérdezéseknek le kell fedniük a definiált hatókört.

2. **Tesztkörnyezet beállítása**. A tesztkörnyezet a forrás-adatbázis és a céladatbázis másolatát is tartalmazza. Ügyeljen arra, hogy elkülönítse a tesztkörnyezet.

3. **Ellenőrzési tesztek futtatása**. Futtassa az ellenőrző teszteket a forráson és a célon, majd elemezze az eredményeket.

4. **Teljesítménytesztek futtatása**. Futtasson teljesítménytesztet a forráson és a célon, majd elemezze és hasonlítsa össze az eredményeket.

### <a name="optimize"></a>Optimalizálás

Az áttelepítés utáni fázis elengedhetetlen az adatok pontosságával kapcsolatos problémák összeegyeztetéséhez és a teljesség ellenőrzéséhez, valamint a számítási feladatok teljesítményével kapcsolatos problémák kezeléséhez.

> [!NOTE]
> További információ ezekről a problémákról és az azok enyhítésére szolgáló konkrét lépésekről: [áttelepítés utáni érvényesítés és optimalizálási útmutató](/sql/relational-databases/post-migration-validation-and-optimization-guide).


## <a name="next-steps"></a>Következő lépések

- A Microsoft és a harmadik féltől származó szolgáltatások és eszközök egyik mátrixa, amely a különböző adatbázis-és adatáttelepítési forgatókönyvek, valamint a speciális feladatok elvégzéséhez nyújt segítséget, lásd: [szolgáltatás és eszközök az adatok áttelepítéséhez](../../../dms/dms-tools-matrix.md).

- Ha többet szeretne megtudni Azure SQL Database lásd:
   - [A SQL Database áttekintése](../../database/sql-database-paas-overview.md)
   - [Az Azure teljes tulajdonlási költsége kalkulátor](https://azure.microsoft.com/pricing/tco/calculator/) 


- Ha többet szeretne megtudni a Felhőbeli Migrálás keretrendszerével és bevezetési ciklusával kapcsolatban, olvassa el a következőt:
   -  [Felhőbevezetési keretrendszer az Azure-hoz](/azure/cloud-adoption-framework/migrate/azure-best-practices/contoso-migration-scale)
   -  [Ajánlott eljárások az Azure-ra való áttéréssel kapcsolatos díjszabási és méretezési feladatok elvégzéséhez](/azure/cloud-adoption-framework/migrate/azure-best-practices/migrate-best-practices-costs) 

- Az alkalmazás-hozzáférési réteg értékeléséhez lásd: [adathozzáférés áttelepítési eszközkészlete (előzetes verzió)](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit)
- Az A/B típusú adatelérési réteg végrehajtásával kapcsolatos további információkért lásd: [Database Experimentation Assistant](/sql/dea/database-experimentation-assistant-overview).
