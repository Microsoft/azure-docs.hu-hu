---
title: U-SQL-szerelvények kezelése CI/CD-folyamatokban – Azure Data Lake
description: Ismerje meg az U-SQL C#-szerelvények felügyeletét a CI/CD-folyamatok Azure DevOps való kezelésével kapcsolatos ajánlott eljárásokat.
author: liudan66
ms.author: liud
ms.service: data-lake-analytics
ms.topic: how-to
ms.date: 10/30/2018
ms.openlocfilehash: e88616f45c69d33234aa35333e0d82ad8cc59bb6
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96015281"
---
# <a name="best-practices-for-managing-u-sql-assemblies-in-a-cicd-pipeline"></a>Ajánlott eljárások a U-SQL-szerelvények CI/CD-folyamatban való kezeléséhez

Ebből a cikkből megtudhatja, hogyan kezelheti a U-SQL Assembly forráskódját az újonnan bevezetett U-SQL Database-projekttel. Azt is megtudhatja, hogyan állíthatja be a folyamatos integrációs és üzembe helyezési (CI/CD) folyamatokat az Azure DevOps használatával történő szerelvény-regisztrációhoz.

## <a name="use-the-u-sql-database-project-to-manage-assembly-source-code"></a>A U-SQL adatbázis-projekt használata a szerelvény forráskódjának kezeléséhez

[A u-SQL Database projekt](data-lake-analytics-data-lake-tools-develop-usql-database.md) egy olyan projekt, amely a Visual Studióban segít a fejlesztőknek az u-SQL-adatbázisok gyors és egyszerű fejlesztésében, kezelésében és üzembe helyezésében. A U-SQL adatbázis-projekttel kezelheti az összes U-SQL Database-objektumot (kivéve a hitelesítő adatokat).

A C# Assembly-forráskód és a szerelvény-regisztrációs DDL U-SQL-parancsfájlok kezeléséhez használja a következőt:

- U-SQL Database-projekt a szerelvény-regisztráció U-SQL-szkriptek kezeléséhez.
- Osztály könyvtára (U-SQL-alkalmazáshoz) a C# forráskód és a felhasználó által definiált operátorok, függvények és gyűjtők (Udo, UDF és UDAGs) függőségeinek kezeléséhez.
- A U-SQL Database-projekt, amely az osztály könyvtára projektre hivatkozik.

Egy U-SQL Database-projekt hivatkozhat egy Class Library (U-SQL alkalmazás) projektre. Az U-SQL-adatbázisban regisztrált szerelvényeket az ebben az osztályban található (U-SQL-alkalmazás) projekthez hivatkozott C#-forráskód használatával hozhatja létre.

A következő lépések végrehajtásával hozhat létre projekteket és adhat hozzá hivatkozásokat.

1. Hozzon létre egy Class Library-projektet (U-SQL-alkalmazáshoz), és válassza a **fájl**  >  **új**  >  **projekt** lehetőséget. A projekt a **Azure Data Lake > U-SQL** csomópont alatt található.

   ![Data Lake Tools for Visual Studio – C# Class Library-projekt létrehozása](./media/data-lake-analytics-cicd-manage-assemblies/create-c-sharp-class-library-project.png)

1. Adja hozzá a felhasználó által megadott C#-kódot a Class Library (U-SQL alkalmazás) projekthez.

1. Hozzon létre egy U-SQL-projektet a **fájl**  >  **új**  >  **projekt** lehetőség kiválasztásával. A projekt a **Azure Data Lake**  >  **U-SQL** csomópont alatt található.

   ![Data Lake eszközök a Visual studióhoz – U-SQL Database-projekt létrehozása](media/data-lake-analytics-cicd-manage-assemblies/create-u-sql-database-project.png)

1. Adjon hozzá egy hivatkozást a C# Class Library-projekthez az U-SQL Database projekthez.

   ![A Visual Studio Data Lake eszközei – hivatkozás hozzáadása](./media/data-lake-analytics-cicd-manage-assemblies/data-lake-tools-add-project-reference.png)

   ![Data Lake Tools for Visual Studio – az U-SQL Database-projekt referenciájának hozzáadása](./media/data-lake-analytics-cicd-manage-assemblies/data-lake-tools-add-project-reference-wizard.png)

1. Hozzon létre egy összeállítási parancsfájlt a U-SQL adatbázis-projektben, és kattintson a jobb gombbal a projektre, és válassza az **új elem hozzáadása** lehetőséget.

   ![A Visual Studio Data Lake eszközei – szerelvény-parancsfájl hozzáadása](media/data-lake-analytics-cicd-manage-assemblies/add-assembly-script.png)

1. Nyissa meg a szerelvény-parancsfájlt a szerelvény tervezési nézetében. Válassza ki a hivatkozott szerelvényt a **szerelvény létrehozása hivatkozásból** legördülő menüből.

   ![Data Lake Tools for Visual Studio – szerelvény létrehozása hivatkozás alapján](./media/data-lake-analytics-cicd-manage-assemblies/data-lake-tools-create-assembly-from-reference.png)

1. **Felügyelt függőségek** és **További fájlok** hozzáadása, ha vannak ilyenek. Ha további fájlokat ad hozzá, az eszköz a relatív elérési utat használja annak biztosítására, hogy a helyi gépen és a Build gépen később is megtalálja a szerelvényeket.

Az alján található szerkesztő ablak **\@ _DeployTempDirectory** egy előre definiált változó, amely az eszközt a Build kimeneti mappájára mutat. A Build kimeneti mappájában minden szerelvényhez tartozik egy nevű almappa a szerelvény nevével. Az összes DLL-fájl és a további fájlok ebben az almappában találhatók.

## <a name="build-a-u-sql-database-project"></a>U-SQL Database-projekt létrehozása

A U-SQL adatbázis-projekthez tartozó Build kimenet egy U-SQL adatbázis-telepítési csomag. A neve az utótaggal együtt `.usqldbpack` . A `.usqldbpack` csomag egy. zip-fájl, amely tartalmazza az összes DDL-utasítást egyetlen U-SQL-parancsfájlban a DDL mappában. Az összes beépített. dll fájl és a szerelvények további fájljai a temp mappában találhatók.

## <a name="deploy-a-u-sql-database"></a>U-SQL-adatbázis üzembe helyezése

A `.usqldbpack` csomag telepíthető helyi fiókra vagy Azure Data Lake Analytics-fiókra. Használja a Visual studiót vagy a Deployment SDK-t.

### <a name="deploy-a-u-sql-database-in-visual-studio"></a>U-SQL-adatbázis üzembe helyezése a Visual Studióban

A U-SQL-adatbázist egy U-SQL adatbázis-projekt vagy egy `.usqldbpack` , a Visual Studióban található csomag használatával helyezheti üzembe.

#### <a name="deploy-by-using-a-u-sql-database-project"></a>Üzembe helyezés egy U-SQL Database-projekt használatával

1. Kattintson a jobb gombbal a U-SQL adatbázis-projektre, majd válassza a **telepítés** lehetőséget.

1. A **Deploy U-SQL Database** varázslóban válassza ki azt a **ADLA-fiókot** , amelyre telepíteni kívánja az adatbázist. A helyi fiókok és a ADLA-fiókok egyaránt támogatottak.

1. Az **adatbázis forrása** automatikusan kitöltődik. A projekt Build output mappájában a. usqldbpack csomagra mutat.

1. Adatbázis létrehozásához adjon meg egy nevet az **adatbázis nevében** . Ha már létezik ilyen nevű adatbázis a cél Azure Data Lake Analytics fiókban, az adatbázis-projektben definiált összes objektum létrejön az adatbázis újbóli létrehozása nélkül.

1. Az U-SQL-adatbázis üzembe helyezéséhez válassza a **Submit (Küldés**) lehetőséget. Az összes erőforrás, például szerelvények és további fájlok feltöltése történik. Egy U-SQL-feladatot, amely tartalmazza az összes DDL-utasítást.

   ![Data Lake Tools for Visual Studio – az U-SQL Database-projekt üzembe helyezése](./media/data-lake-analytics-cicd-manage-assemblies/data-lake-tools-deploy-usql-database-project.png)

   ![A Visual studióhoz készült Data Lake eszközök – az U-SQL adatbázis-projekt üzembe helyezése varázsló](./media/data-lake-analytics-cicd-manage-assemblies/data-lake-tools-deploy-usql-database-project-wizard.png)

### <a name="deploy-a-u-sql-database-in-azure-devops"></a>U-SQL-adatbázis üzembe helyezése az Azure DevOps

`PackageDeploymentTool.exe` az U-SQL-adatbázisok üzembe helyezését megkönnyítő programozási és parancssori felületeket biztosít. Az SDK-t a [U-SQL SDK Nuget csomag](https://www.nuget.org/packages/Microsoft.Azure.DataLake.USQL.SDK/)tartalmazza, amely a következő helyen található: `build/runtime/PackageDeploymentTool.exe` .

Az Azure DevOps parancssori feladattal és az SDK-val egy automatizálási folyamatot állíthat be a U-SQL-adatbázis frissítéséhez. [További információ az SDK-ról és az U-SQL Database üzembe helyezéséhez szükséges CI/CD-folyamatok beállításáról](data-lake-analytics-cicd-overview.md#deploy-u-sql-database-through-azure-pipelines).

## <a name="next-steps"></a>Következő lépések

- [CI/CD-folyamat beállítása Azure Data Lake Analyticshoz](data-lake-analytics-cicd-overview.md)
- [A Azure Data Lake Analytics kód tesztelése](data-lake-analytics-cicd-test.md)
- [U-SQL-szkript futtatása a helyi gépen](data-lake-analytics-data-lake-tools-local-run.md)
