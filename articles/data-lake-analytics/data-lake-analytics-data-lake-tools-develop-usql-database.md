---
title: U-SQL adatbázis-projekt fejlesztése – Azure Data Lake
description: Ismerje meg, hogyan fejleszthet U-SQL-adatbázist a Visual studióhoz készült Azure Data Lake Tools használatával.
author: liudan66
ms.author: liud
ms.reviewer: jasonh
ms.service: data-lake-analytics
ms.topic: how-to
ms.date: 07/03/2018
ms.openlocfilehash: 706457a602e20dd37e64e5f389948b351ac8ebcf
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96015247"
---
# <a name="use-a-u-sql-database-project-to-develop-a-u-sql-database-for-azure-data-lake"></a>U-SQL Database-projekt használata a Azure Data Lake U-SQL-adatbázis fejlesztéséhez

Az U-SQL-adatbázis strukturált nézeteket biztosít strukturálatlan és strukturált adatokhoz a táblákban. Emellett általános metaadat-katalógust is biztosít a strukturált adatok és az egyéni kódok rendszerezéséhez. Az adatbázis az a fogalom, amely ezeket a kapcsolódó objektumokat csoportosítja.

További információ az [U-SQL Database és az adatdefiníciós nyelvről (DDL)](/u-sql/data-definition-language-ddl-statements).

A U-SQL Database projekt egy olyan projekt, amely a Visual Studióban segít a fejlesztőknek az U-SQL-adatbázisok gyors és egyszerű fejlesztésében, kezelésében és üzembe helyezésében.

## <a name="create-a-u-sql-database-project"></a>U-SQL adatbázis-projekt létrehozása

Azure Data Lake Tools for Visual Studio hozzáadott egy új, U-SQL Database-projekt nevű Project-sablont a 2.3.3000.0 verzió után. U-SQL-projekt létrehozásához válassza a **fájl > új > projekt** lehetőséget. Az U-SQL Database projekt a **Azure Data Lake > u-SQL csomópont** alatt található.

![Data Lake eszközök a Visual studióhoz – U-SQL Database-projekt létrehozása](./media/data-lake-analytics-data-lake-tools-develop-usql-database/data-lake-tools-create-usql-database-project-creation.png)

## <a name="develop-u-sql-database-objects-by-using-a-database-project"></a>U-SQL Database-objektumok fejlesztése adatbázis-projekt használatával

Kattintson a jobb gombbal a U-SQL adatbázis-projektre. Válassza a **hozzáadás > új elem** lehetőséget. Az új **elem hozzáadása** varázslóban megtekintheti az összes új támogatott objektumtípust.

Egy nem szerelvényből álló objektum esetében (például egy táblázat értékű függvény) új U-SQL-szkript jön létre új elem hozzáadása után. A szerkesztőben megkezdheti az adott objektumhoz tartozó DDL-utasítás fejlesztését.

Egy szerelvény-objektum esetében az eszköz egy felhasználóbarát FELHASZNÁLÓIFELÜLET-szerkesztőt biztosít, amely segítséget nyújt a szerelvény regisztrálásához és a DLL-fájlok és egyéb további fájlok telepítéséhez. A következő lépések bemutatják, hogyan adhat hozzá egy szerelvény-definíciót a U-SQL adatbázis-projekthez:

1. Adjon hozzá hivatkozásokat a C#-projekthez, amely tartalmazza a U-SQL Database projekthez tartozó UDO/UDAG/UDF-t.

   ![A Visual Studio Data Lake eszközei – hivatkozás hozzáadása](./media/data-lake-analytics-data-lake-tools-develop-usql-database/data-lake-tools-add-project-reference.png)

   ![Data Lake Tools for Visual Studio – az U-SQL Database-projekt referenciájának hozzáadása](./media/data-lake-analytics-data-lake-tools-develop-usql-database/data-lake-tools-add-project-reference-wizard.png)

2. A szerelvény tervezési nézetében válassza ki a hivatkozott szerelvényt a **szerelvény létrehozása hivatkozásból** legördülő menüből.

   ![Data Lake Tools for Visual Studio – szerelvény létrehozása hivatkozás alapján](./media/data-lake-analytics-data-lake-tools-develop-usql-database/data-lake-tools-create-assembly-from-reference.png)

3. **Felügyelt függőségek** és **További fájlok** hozzáadása, ha vannak ilyenek. Ha további fájlokat ad hozzá, az eszköz a relatív elérési utat használja annak biztosítására, hogy a helyi gépen és a Build gépen is megtalálja a szerelvényeket.

@_DeployTempDirectory egy előre definiált változó, amely az eszközt a Build kimeneti mappájára mutat. A Build kimeneti mappájában minden szerelvényhez tartozik egy nevű almappa a szerelvény nevével. Az összes DLL-fájl és a további fájlok ebben az almappában találhatók.

## <a name="build-a-u-sql-database-project"></a>U-SQL Database-projekt létrehozása

A U-SQL adatbázis-projekthez tartozó Build kimenet egy U-SQL adatbázis-telepítési csomag, amelynek neve az utótag `.usqldbpack` . A `.usqldbpack` csomag egy. zip-fájl, amely tartalmazza az összes DDL-utasítást egyetlen U-SQL-parancsfájlban a **DDL** mappában, valamint az összes DLL-t és további fájlt a szerelvényekhez a **temp** mappában.

Tudjon meg többet arról, [hogyan hozhat létre U-SQL adatbázis-projektet az MSBuild parancssorral és egy Azure DevOps Services Build feladattal](data-lake-analytics-cicd-overview.md).

## <a name="deploy-a-u-sql-database"></a>U-SQL-adatbázis üzembe helyezése

A. usqldbpack csomag egy helyi fiókra vagy egy Azure Data Lake Analytics fiókra is telepíthető a Visual Studio vagy a Deployment SDK használatával.

### <a name="deploy-a-u-sql-database-in-visual-studio"></a>U-SQL-adatbázis üzembe helyezése a Visual Studióban

U-SQL-adatbázist a Visual Studióban egy U-SQL adatbázis-projekten vagy egy. usqldbpack-csomagon keresztül telepíthet.

#### <a name="deploy-through-a-u-sql-database-project"></a>Üzembe helyezés egy U-SQL Database-projekten keresztül

1. Kattintson a jobb gombbal a U-SQL adatbázis-projektre, majd válassza a **telepítés** lehetőséget.

1. A **Deploy U-SQL Database varázslóban** válassza ki azt a **ADLA-fiókot** , amelyre telepíteni kívánja az adatbázist. A helyi fiókok és a ADLA-fiókok egyaránt támogatottak.

1. Az **adatbázis forrása** automatikusan ki van töltve, és a projekt Build kimeneti mappájában lévő. usqldbpack csomagra mutat.

1. Adatbázis létrehozásához adjon meg egy nevet az **adatbázis nevében** . Ha már létezik ilyen nevű adatbázis a cél Azure Data Lake Analytics fiókban, az adatbázis-projektben definiált összes objektum létrejön az adatbázis újbóli létrehozása nélkül.

1. Az U-SQL-adatbázis üzembe helyezéséhez válassza a **Submit (Küldés**) lehetőséget. Minden erőforrás (szerelvények és további fájlok) fel van töltve, és egy U-SQL-feladatot, amely tartalmazza az összes DDL-utasítást.

   ![Data Lake Tools for Visual Studio – az U-SQL Database-projekt üzembe helyezése](./media/data-lake-analytics-data-lake-tools-develop-usql-database/data-lake-tools-deploy-usql-database-project.png)

   ![A Visual studióhoz készült Data Lake eszközök – az U-SQL adatbázis-projekt üzembe helyezése varázsló](./media/data-lake-analytics-data-lake-tools-develop-usql-database/data-lake-tools-deploy-usql-database-project-wizard.png)

#### <a name="deploy-through-a-u-sql-database-deployment-package"></a>Üzembe helyezés egy U-SQL-adatbázis központi telepítési csomagjával

1. Nyissa meg a **Server Explorert**. Ezután bontsa ki azt a **Azure Data Lake Analytics fiókot** , amelyre telepíteni kívánja az adatbázist.

1. Kattintson a jobb gombbal az **U-SQL-adatbázisok** elemre, majd válassza az **adatbázis telepítése** lehetőséget.

1. Állítsa be az **adatbázis forrását** az U-SQL adatbázis-telepítési csomag (. usqldbpack fájl) elérési útjára.

1. Adatbázis létrehozásához adja meg az **adatbázis nevét** . Ha van olyan adatbázis, amelynek a neve már létezik a cél Azure Data Lake Analytics fiókban, az adatbázis-projektben definiált összes objektum létrehozása az adatbázis újbóli létrehozása nélkül történik.

   ![A Visual Studio Data Lake eszközei – az U-SQL Database-csomag üzembe helyezése](./media/data-lake-analytics-data-lake-tools-develop-usql-database/data-lake-tools-deploy-usql-database-package.png)

   ![A Visual studióhoz készült Data Lake eszközök – az U-SQL-adatbázis csomagjának üzembe helyezése varázsló](./media/data-lake-analytics-data-lake-tools-develop-usql-database/data-lake-tools-deploy-usql-database-package-wizard.png)
  
### <a name="deploy-u-sql-database-by-using-the-sdk"></a>U-SQL-adatbázis üzembe helyezése az SDK használatával

`PackageDeploymentTool.exe` az U-SQL-adatbázisok üzembe helyezését megkönnyítő programozási és parancssori felületeket biztosít. Az SDK-t a [U-SQL SDK Nuget csomag](https://www.nuget.org/packages/Microsoft.Azure.DataLake.USQL.SDK/)tartalmazza, amely a következő helyen található: `build/runtime/PackageDeploymentTool.exe` .

[További információ az SDK-ról, valamint a CI/CD-folyamat beállítása az U-SQL Database-telepítéshez](data-lake-analytics-cicd-overview.md).

## <a name="reference-a-u-sql-database-project"></a>U-SQL Database-projekt referenciája

Egy U-SQL-projekt hivatkozhat egy U-SQL Database-projektre. A hivatkozás két munkaterhelést érint:

- *Projekt létrehozása*: állítsa be a hivatkozott adatbázis-környezeteket a U-SQL-parancsfájlok létrehozása előtt.
- *Helyi Futtatás (helyi projekt) fiók*: a hivatkozott adatbázis-környezetek a U-SQL-szkript végrehajtása előtt a (helyi projekt) fiókra lesznek telepítve. [További információ a helyi futtatásokról, valamint a különbség (a helyi gép) és a (helyi projekt) fiók között](data-lake-analytics-data-lake-tools-local-run.md).

### <a name="how-to-add-a-u-sql-database-reference"></a>U-SQL Database-hivatkozás hozzáadása

1. Kattintson a jobb gombbal a U-SQL-projektre **megoldáskezelő**, majd válassza az **u-SQL Database-hivatkozás hozzáadása..**. lehetőséget.

    ![Data Lake Tools for Visual Studio – adatbázis-projekt hivatkozásának hozzáadása](./media/data-lake-analytics-data-lake-tools-develop-usql-database/data-lake-tools-add-database-project-reference.png)

2. Konfiguráljon egy adatbázis-referenciát egy U-SQL adatbázis-projektből az aktuális megoldásban vagy egy U-SQL Database csomagfájl-fájlban.

3. Adja meg az adatbázis nevét.

    ![Data Lake Tools for Visual Studio adatbázis-projekt-hivatkozás hozzáadása varázsló](./media/data-lake-analytics-data-lake-tools-develop-usql-database/data-lake-tools-add-database-project-reference-wizard.png)

## <a name="next-steps"></a>Következő lépések

- [CI/CD-folyamat beállítása Azure Data Lake Analyticshoz](data-lake-analytics-cicd-overview.md)
- [A Azure Data Lake Analytics kód tesztelése](data-lake-analytics-cicd-test.md)
- [U-SQL-szkript futtatása a helyi gépen](data-lake-analytics-data-lake-tools-local-run.md)
