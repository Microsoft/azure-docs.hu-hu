---
title: 'Oktatóanyag: Azure Cosmos DB-alapú Node.js-alkalmazások üzembe helyezése az Azure DevOps Starter használatával'
description: Az Azure DevOps Starter megkönnyíti az Azure-ban való ismerkedést. A DevOps Starter segítségével néhány gyors lépéssel üzembe helyezheti Node.js alkalmazást, amely Azure Cosmos DB a Windows-webalkalmazáshoz.
ms.author: mlearned
ms.manager: gwallace
ms.prod: devops
ms.technology: devops-cicd
ms.topic: tutorial
ms.date: 03/24/2020
author: mlearned
ms.custom: devx-track-js
ms.openlocfilehash: d1bdae976d5e5b7d98311d33bb38b5035f2557ca
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/10/2021
ms.locfileid: "102548699"
---
# <a name="deploy-nodejs-apps-powered-by-azure-cosmos-db-with-devops-starter"></a>Azure Cosmos DB-alapú Node.js-alkalmazások üzembe helyezése a DevOps Starter használatával

Az Azure DevOps Starter zökkenőmentesen használható élményt nyújt, amellyel folyamatos integrációs (CI) és folyamatos üzembe helyezési (CD) folyamatokat hozhat létre az Azure-ban. Ezt a meglévő kód-és git-tárház (repó) használatával, vagy egy minta alkalmazás kiválasztásával teheti meg.

A DevOps Starter is:

* Automatikusan létrehozza az Azure-erőforrásokat, például a Azure Cosmos DB, az Azure Application Insights, a Azure App Service és a App Service terveket

* CI/CD kiadási folyamat létrehozása és konfigurálása az Azure DevOps

Az oktatóanyagban a következőket végezheti el:

> [!div class="checklist"]
> * A DevOps Starter használata egy Node.js-alkalmazás üzembe helyezéséhez Azure Cosmos DB
> * Azure-DevOps és Azure-előfizetés konfigurálása
> * Azure Cosmos DB vizsgálata
> * A CI-folyamat vizsgálata
> * A CD-folyamat vizsgálata
> * A módosítások elvégzése a git-ben és az Azure-ba történő automatikus üzembe helyezése
> * Az erőforrások eltávolítása

## <a name="prerequisites"></a>Előfeltételek

Szüksége van egy Azure-előfizetésre, amelyet ingyenesen kaphat a [Visual Studio dev Essentials](https://visualstudio.microsoft.com/dev-essentials/) használatával.

## <a name="use-devops-starter-to-deploy-nodejs-app"></a>Node.js alkalmazás üzembe helyezése a DevOps Starter használatával

A DevOps Starter egy CI/CD-folyamatot hoz létre az Azure-folyamatokban. Létrehozhat egy új Azure DevOps-szervezetet, vagy használhat egy meglévő céget is. A DevOps Starter Azure-erőforrásokat is létrehoz, például Azure Cosmos DB, Application Insights, App Service és App Service terveket az Ön által választott Azure-előfizetésben.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

1. A keresőmezőbe írja be a **DevOps Starter** kifejezést, majd válassza a elemet. Kattintson a **Hozzáadás** gombra egy új létrehozásához.

    ![A DevOps Starter irányítópultja](_img/azure-devops-starter-aks/search-devops-starter.png)

1. Válassza a **Node.js** lehetőséget futtatókörnyezetként, majd kattintson a **tovább** gombra. Az **alkalmazás-keretrendszer kiválasztása** területen válassza a **Express.js** lehetőséget.

1. Engedélyezze az **adatbázis hozzáadása** a **Cosmos DBhoz** szakaszt, majd kattintson a **tovább** gombra.

    ![Adatbázis hozzáadása](_img/azure-devops-project-cosmos-db/add-database.png)

    Az Azure DevOps Starter különböző alkalmazás-keretrendszereket támogat, mint például a **Express.js**, a **minta Node.js alkalmazás** és a **Sail.js**. Ebben az oktatóanyagban a **Express.jst** használjuk.

1. Válasszon ki egy Azure-szolgáltatást az alkalmazás telepítéséhez, majd kattintson a **tovább** gombra. A lehetőségek közé tartozik a Windows Web App, az Azure Kubernetes Service és az Azure Web App for Containers. Ebben az oktatóanyagban a **Windows Web APPT** használjuk.

## <a name="configure-azure-devops-and-azure-subscription"></a>Az Azure DevOps és az Azure-előfizetés konfigurálása

1. Adja meg az Azure DevOps-projekt nevét.

1. Hozzon létre egy új Azure DevOps-szervezetet, vagy válasszon ki egy meglévő szervezetet.

1. Válassza ki Azure-előfizetését.

1. További Azure-konfigurációs beállítások megtekintéséhez vagy az árképzési csomag és a hely azonosításához válassza a **További beállítások** lehetőséget. Ez a panel az Azure-szolgáltatások díjszabási szintjeinek és helyének konfigurálására szolgáló különböző lehetőségeket mutatja be.

1. Lépjen ki az Azure-beli konfigurációs területen, majd válassza a **kész** lehetőséget.

1. A folyamat néhány perc múlva befejeződik. Egy minta Node.js alkalmazás egy git-tárházban van beállítva az Azure DevOps-szervezetben. Ezt követően Azure Cosmos DB, App Service, App Service a terv és Application Insights erőforrások jönnek létre, valamint egy CI/CD-folyamat. Az alkalmazást ezután üzembe helyezi az Azure-ban.

   Az összes folyamat befejezése után az Azure DevOps Starter irányítópultja megjelenik a Azure Portalban. A DevOps Starter-irányítópultot közvetlenül a Azure Portal **összes erőforrásáról** is megtekintheti.

   Ez az irányítópult az Azure DevOps-adattárba, a CI/CD-folyamatba és a Azure Cosmos DB-adatbázisba is betekintést nyújt. Az Azure DevOps-folyamat további CI/CD-beállításokat is konfigurálhat. Az irányítópult jobb oldalán válassza a **Azure Cosmos db** lehetőséget a beállítások megtekintéséhez.

## <a name="examine-azure-cosmos-db"></a>Azure Cosmos DB vizsgálata

A DevOps Starter automatikusan konfigurálja a Azure Cosmos DB, amelyek segítségével megismerheti és testreszabhatja. A Azure Cosmos DB megismeréséhez tegye a következőket:

1. Nyissa meg a DevOps Starter irányítópultját.

    ![DevOps Projects irányítópult](_img/azure-devops-project-cosmos-db/devops-starter-dashboard.png)

1. A jobb oldalon válassza a Azure Cosmos DB lehetőséget. Megnyílik egy ablaktábla Azure Cosmos DB. Ebből a nézetből különböző műveleteket hajthat végre, például figyelési műveleteket és naplókat kereshet.

    ![Azure Cosmos DB ablaktábla](_img/azure-devops-project-cosmos-db/cosmos-db.png)

## <a name="examine-the-ci-pipeline"></a>A CI-folyamat vizsgálata

A DevOps Starter automatikusan konfigurálja a CI/CD-folyamatot az Azure DevOps-szervezetben. Megvizsgálhatja és testre szabhatja a folyamatot. A következők megismeréséhez tegye a következőket:

1. Nyissa meg a DevOps Starter irányítópultját.

1. Válassza ki a hivatkozást a **Build** alatt. Egy böngésző lap megjeleníti az új projekt összeállítási folyamatát.

    ![Létrehozás ablaktábla](_img/azure-devops-project-cosmos-db/build.png)

1. Válassza a **Szerkesztés** elemet. Ezen az ablaktáblán ellenőrizheti a felépítési folyamat különféle feladatait. A build különböző feladatokat hajt végre, mint például a forráskód beolvasása a git-tárházból, az alkalmazás létrehozása, az egységhez tartozó tesztek futtatása, valamint az üzembe helyezéshez használt kimenetek közzététele.

1. Válassza az **Eseményindítók** lehetőséget. A DevOps Starter automatikusan létrehoz egy CI-triggert, és a tárházba való minden egyes véglegesít egy új buildet indít el. Dönthet úgy is, hogy a CI-folyamatból kijelöli vagy kizárja az ágakat.

1. Válassza a **Megtartást**. A forgatókönyvtől függően megadhat olyan házirendeket, amelyek bizonyos számú buildet megtartanak vagy eltávolítanak.

1. A buildelési folyamat tetején válassza a buildelési folyamat nevét.

1. Módosítsa a felépítési folyamat nevét egy ennél több leíró értékre, **majd válassza a mentés lehetőséget** a **Mentés & üzenetsor** legördülő menüből.

1. A buildelési folyamat neve alatt válassza az **Előzményeket**. Ez az ablaktábla a Build legutóbbi változásainak naplózási nyomvonalát jeleníti meg. Az Azure DevOps nyomon követi a felépítési folyamaton végrehajtott módosításokat, és lehetővé teszi a verziók összehasonlítását.

## <a name="examine-the-cd-release-pipeline"></a>A CD-kiadási folyamat vizsgálata

A DevOps Starter automatikusan létrehozza és konfigurálja a szükséges lépéseket az Azure DevOps-szervezetből az Azure-előfizetésbe való üzembe helyezéshez. Ezek a lépések az Azure-DevOps Azure-előfizetéshez való hitelesítéséhez szükséges Azure-szolgáltatási kapcsolatok konfigurálását is tartalmazzák. Az Automation egy kiadási folyamatot is létrehoz, amely biztosítja a CD-t az Azure-hoz. Ha többet szeretne megtudni a kiadási folyamatról, tegye a következőket:

1. Nyissa meg a **folyamatokat** , és válassza a **kiadások** lehetőséget.

1. Válassza a **Szerkesztés** elemet.

1. Az **Összetevők** alatt válassza az **Elvetést**. Az előző lépések során megvizsgált összeállítási folyamat létrehozza az összetevőhöz használt kimenetet.

1. A **drop** ikontól jobbra válassza a **folyamatos üzembe helyezés trigger** lehetőséget. Ez a kiadási folyamat engedélyezte a folyamatos üzembe helyezési triggert, amely minden alkalommal végrehajt egy központi telepítést, amikor új Build-összetevő érhető el. Letilthatja az indítást úgy, hogy a központi telepítések manuálisan legyenek végrehajtva.

1. A jobb oldalon válassza a verziók **megtekintése** lehetőséget a kiadások előzményeinek megjelenítéséhez.

1. Válassza ki a kiadást, amely megjeleníti a folyamatot. Válassza ki bármelyik környezetet a kiadási összefoglalás, a véglegesítő vagy a kapcsolódó munkaelemek vizsgálatához.

1. Válassza a **Véglegesítéseket**. Ez a nézet a telepítéshez társított kód-véglegesítő kódokat jeleníti meg. Az üzembe helyezések közötti véglegesítési különbségek megtekintéséhez hasonlítsa össze a kiadásokat.

1. Válassza a **naplók megtekintése** lehetőséget. A naplók hasznos információkat tartalmaznak az üzembehelyezési folyamattal kapcsolatban. Ezeket az üzembe helyezések során és után is megtekintheti.

## <a name="commit-code-changes-and-execute-the-cicd-pipeline"></a>Kód végrehajtásának elvégzése és a CI/CD folyamat végrehajtása

> [!NOTE]
> Az alábbi eljárással a CI/CD-folyamat egy egyszerű szöveg módosításával tesztelhető.

Most már készen áll az alkalmazásban lévő csapattal való együttműködésre egy CI/CD-folyamat használatával, amely a legújabb munkáját üzembe helyezi a App Service. A git-tárház minden módosítása egy buildet indít az Azure DevOps, és egy CD-folyamat végrehajtja az Azure-ba történő üzembe helyezést. Kövesse az ebben a szakaszban leírt eljárást, vagy használjon másik technikát a tárház változásainak érvényesítéséhez. Megteheti például, hogy a git-tárházat a kedvenc eszközén vagy IDE helyezi, majd leküldi a módosításokat a tárházban.

1. Az Azure DevOps menüben válassza a **repók** , majd a **fájlok** elemet. Ezután lépjen a tárházba.

1. A tárház már tartalmaz kódot a létrehozási folyamat során kiválasztott alkalmazás nyelve alapján. Nyissa meg az **Application/views/index. mopsz** fájlt.

1. Válassza a **Szerkesztés** lehetőséget, majd végezze el a **15. számú sor** módosítását. Megváltoztathatja például a következőre: "az első központi telepítés Azure App Service powered by Azure Cosmos DB."

1. A jobb felső sarokban válassza a végrehajtás lehetőséget, majd a módosítás elküldéséhez válassza a **véglegesítés** **újra lehetőséget.**

     Néhány másodperc elteltével a buildek az Azure DevOps-ben kezdődnek, és a kiadásokat a rendszer a módosítások üzembe helyezésével hajtja végre. Figyelje a Build állapotát a DevOps Starter irányítópultján vagy a böngészőben az Azure DevOps-szervezettel.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Törölje a létrehozott kapcsolódó erőforrásokat, ha már nincs rá szüksége. Használja a **delete** funkciót a DevOps Starter irányítópultján.

## <a name="next-steps"></a>Következő lépések

A csapat igényeihez igazodva módosíthatja ezt a buildet és a kiadási folyamatokat. Ezt a CI-/CD-mintát egyéb folyamatok sablonjaként is használhatja. Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * A DevOps Starter használata egy Node.js-alkalmazás üzembe helyezéséhez Azure Cosmos DB
> * Azure-DevOps és Azure-előfizetés konfigurálása 
> * Azure Cosmos DB vizsgálata
> * A CI-folyamat vizsgálata
> * A CD-folyamat vizsgálata
> * Módosítások végrehajtása a git-ben és automatikus üzembe helyezése az Azure-ban
> * Az erőforrások eltávolítása

További információkat és további lépéseket a [többfázisú folyamatos üzembe helyezés (CD) folyamatának meghatározása](/azure/devops/pipelines/release/define-multistage-release-process) című témakörben talál.