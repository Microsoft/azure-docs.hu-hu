---
title: CI/CD-folyamat létrehozása a GatsbyJS és az Azure DevOps Starter-vel rendelkező PWA
description: Ismerje meg, hogyan hozhat létre NodeJS progresszív webalkalmazást (PWA) a GatsbyJS és az egyszerűsített Azure DevOps Starter-létrehozási felület használatával.
ms.prod: devops
ms.technology: devops-cicd
documentationcenter: vs-devops-build
author: mijacobs
ms.author: mijacobs
ms.workload: web
ms.topic: quickstart
ms.date: 03/24/2020
ms.custom: mvc, devx-track-js
ms.openlocfilehash: d6d8a10dffa3801fe343b3b479265fac5612b021
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "100556561"
---
# <a name="create-a-cicd-pipeline-in-azure-pipelines-for-nodejs-with-azure-devops-starter"></a>CI/CD-folyamat létrehozása Azure-folyamatokban Node.js Azure DevOps Starter-vel

Ebben a rövid útmutatóban létrehoz egy NodeJS progresszív webalkalmazást (PWA) a [GatsbyJS](https://www.gatsbyjs.org/) és az egyszerűsített Azure DevOps Starter-létrehozási felület használatával. Ha elkészült, folyamatos integrációs (CI) és folyamatos továbbítási (CD) folyamattal rendelkezik az Azure-folyamatok PWA. Az Azure DevOps a fejlesztéshez, üzembe helyezéshez és figyeléshez szükséges igényeket határozza meg.

## <a name="prerequisites"></a>Előfeltételek

- Aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egy fiókot ingyenesen](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio). 
- Egy [Azure DevOps](https://azure.microsoft.com/services/devops/) -szervezet.

## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra

A DevOps Starter egy CI/CD-folyamatot hoz létre az Azure-folyamatokban. Létrehozhat egy új Azure DevOps-szervezetet, vagy használhat egy meglévő céget is. A DevOps Starter Azure-erőforrásokat is létrehoz az Ön által választott Azure-előfizetésben.

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com), és a bal oldali ablaktáblán válassza az **erőforrás létrehozása** lehetőséget. 

   ![Azure-erőforrás létrehozása Azure Portal](_img/azure-devops-project-nodejs/create-azure-resource.png)

1. A keresőmezőbe írja be a **DevOps Starter** kifejezést, majd válassza a elemet. Kattintson a **Hozzáadás** gombra egy új létrehozásához.

    ![A DevOps Starter irányítópultja](_img/azure-devops-starter-aks/search-devops-starter.png)

## <a name="select-a-sample-application-and-azure-service"></a>Mintaalkalmazás és Azure-szolgáltatás kiválasztása

1. Válassza a Node.js mintaalkalmazást.   

    ![Node.js minta kiválasztása](_img/azure-devops-project-nodejs/select-nodejs.png) 

1. Az alapértelmezett mintakeretrendszer az **Express.js**. Módosítsa a kijelölést **egyszerű Node.js alkalmazásra** , majd válassza a **tovább** lehetőséget. 

    ![Válassza ki az egyszerű Node.js alkalmazást](_img/azure-devops-project-nodejs/select-nodejs-framework.png) 

1. Az ebben a lépésben elérhető központi telepítési célokat a 2. lépésben kiválasztott alkalmazás-keretrendszer határozza meg. Ebben a példában a **Windows Web App** az alapértelmezett telepítési cél. Hagyja **Web App for containers** a beállítást, és válassza a **tovább** lehetőséget.

    ![Telepítési cél kiválasztása](_img/azure-devops-project-nodejs/select-web-server.png)

## <a name="configure-a-project-name-and-an-azure-subscription"></a>Adja meg a projekt nevét és az Azure-előfizetést

1. Az DevOps Starter-létrehozási munkafolyamat utolsó lépéseként rendeljen hozzá egy projekt nevét, válasszon ki egy Azure-előfizetést, majd válassza a **kész** lehetőséget.  

    ![Projekt nevének kijelölése és előfizetés kiválasztása](_img/azure-devops-project-nodejs/assign-project-name.png)

1. Megjelenik egy összefoglaló oldal a projekt létrehozásakor, és az alkalmazás üzembe helyezése az Azure-ban történik. Egy rövid időszak után létrejön egy projekt az [Azure DevOps-szervezetben](https://dev.azure.com/) , amely tartalmaz egy git-tárházat, egy Kanban-táblát, egy üzembe helyezési folyamatot, egy tesztelési tervet, valamint az alkalmazás által igényelt összetevőket.  

## <a name="managing-your-project"></a>A projekt kezelése

1. Keresse meg az **összes erőforrást** , és keresse meg a DevOps kezdőjét. Válassza ki a **DevOps kezdőjét**.

    ![Azure DevOps-irányítópult az erőforrás-listában](_img/azure-devops-project-nodejs/devops-starter-list.png)

1. Olyan irányítópultra van irányítva, amely megtekinti a projekt kezdőlapját, a kódlap, a CI/CD folyamatot, valamint a futó alkalmazásra mutató hivatkozást. Válassza ki a **projekt kezdőlapját** az alkalmazás **Azure DevOps** való megtekintéséhez, majd egy másik böngésző lapon válassza ki az **alkalmazás-végpontot** az élő minta alkalmazás megtekintéséhez. Ezt a mintát később a GatsbyJS által generált PWA használatára módosítjuk.

    ![Azure DevOps-irányítópult](_img/azure-devops-project-nodejs/devops-projects-dashboard.png) 

1. Az Azure DevOps-projektből meghívhatja a csapattagokat, hogy működjenek együtt egy Kanban-testülettel a munka nyomon követésének megkezdéséhez. További információ: [itt](/azure/devops/user-guide/what-is-azure-devops).

![Az Azure DevOps áttekintése](_img/azure-devops-project-nodejs/azure-devops-overview.png)

## <a name="clone-the-repo-and-install-your-gatsby-pwa"></a>A tárház klónozása és a Gatsby-PWA telepítése

A DevOps Starter létrehoz egy git-tárházat az Azure Reposben vagy a GitHubban. Ez a példa egy Azure-tárházat hozott létre. A következő lépés a tárház klónozása és a módosítások elvégzése.

1. Válassza ki a **DevOps-projektben** a **repókat** , majd kattintson a **klónozás** elemre.  A git-tárház klónozása az asztalra különböző mechanizmusokkal történik.  Válassza ki a fejlesztési élményhez illőt.  

    ![Az adattár klónozása](_img/azure-devops-project-nodejs/clone-the-repo.png)

1. Miután a tárház klónozása megtörtént az asztalról, hajtson végre néhány módosítást az alapszintű sablonban. Először telepítse a GatsbyJS CLI-t a terminálról.

   ```powershell
    npm install -g gatsby
   ```

1. A terminálban navigáljon a tárház gyökeréhez. A következő három mappát kell tartalmaznia, amely így néz ki:

    ```powershell
    Mode                LastWriteTime         Length Name
    ----                -------------         ------ ----
    d-----        2/23/2020  10:42 PM                Application
    d-----        2/23/2020   3:05 PM                ArmTemplates
    d-----        2/23/2020   3:05 PM                Tests
    ```
    
1. Az alkalmazás mappájában nem szeretnénk az összes fájlt használni, mert egy Gatsby-indítóval fogunk lecserélni. A levágáshoz futtassa a következő parancsokat a sorozatokban.
    
    ```powershell
    cp .\Application\Dockerfile .
    rmdir Application
    ```

1. Minta PWA létrehozásához használja a Gatsby CLI-t. Futtassa a `gatsby new` parancsot a terminálról a PWA varázsló elindításához, és válassza ki a `gatsby-starter-blog` kezdő sablonját. Ehhez a példához hasonlónak kell lennie:

    ```powershell
    c:\myproject> gatsby new
    √ What is your project called? ... my-gatsby-project
    ? What starter would you like to use? » - Use arrow-keys. Return to submit.
        gatsby-starter-default
        gatsby-starter-hello-world
    >   gatsby-starter-blog
        (Use a different starter)
    ```
    
1. Most már rendelkezik egy nevű mappával `my-gatsby-project` . Nevezze át, `Application` és másolja a `Dockerfile` ba.
    
    ```powershell
    mv my-gatsby-project Application
    mv Dockerfile Application
    ```
    
1. A kedvenc szerkesztőjében nyissa meg a Docker, és módosítsa az első sort a verzióról a verzióra `FROM node:8` `FROM node:12` . Ez a változás biztosítja, hogy a tároló a 8. x verzió helyett a 12. x Node.js használja. A GatsbyJS Node.js modern verzióját igénylik.

1. Ezután nyissa meg a package.jsfájlt az alkalmazás mappájában, és szerkessze a [parancsfájlok mezőt](https://docs.npmjs.com/files/package.json#scripts) annak ellenőrzéséhez, hogy a fejlesztési és üzemi kiszolgálók figyelik-e az összes elérhető hálózati adaptert (például 0.0.0.0) és a 80-es portot. Ezen beállítások nélkül a Container app Service nem tudja átirányítani a forgalmat a tárolón belül futó Node.js-alkalmazásba. A `scripts` mezőnek az alábbihoz hasonlónak kell lennie. Pontosabban a, a és a célokat szeretné módosítani az `develop` `serve` `start` alapértelmezett beállítások alapján.

    ```json
      "scripts": {
        "build": "gatsby build",
        "develop": "gatsby develop  -H 0.0.0.0 -p 80",
        "format": "prettier --write \"**/*.{js,jsx,json,md}\"",
        "start": "npm run serve",
        "serve": "npm run build && gatsby serve -H 0.0.0.0 -p 80",
        "clean": "gatsby clean",
        "test": "echo \"Write tests! -> https://gatsby.dev/unit-testing\" && exit 1"
      }
    ```
    
## <a name="edit-your-cicd-pipelines"></a>A CI/CD-folyamatok szerkesztése

1. Mielőtt véglegesíti a kódot az előző szakaszban, hajtson végre néhány módosítást a build és a Release folyamaton. Szerkessze a "folyamat létrehozása" lehetőséget, és frissítse a csomópont-feladatot Node.js 12. x verzió használatára. Állítsa a **feladat verziója** mezőt 1. x értékre, a **Version (verzió** ) mezőre pedig 12. x.

    ![Node.js frissítése 12. x-re](_img/azure-devops-project-nodejs/build-pipeline-update-node.png)

1. Ebben a rövid útmutatóban nem hozunk létre egység-teszteket, és letiltjuk ezeket a lépéseket a Build-folyamatban. Tesztek írásakor újra engedélyezheti ezeket a lépéseket. Kattintson a jobb gombbal, és válassza ki a **teszt függőségeinek telepítése** és az **egységek futtatása tesztek** és a Letiltás műveletek elemet.

    ![Build tesztek letiltása](_img/azure-devops-project-nodejs/disable-build-unittests.png)

1. A kiadási folyamat szerkesztése.

    ![A kiadási folyamat szerkesztése](_img/azure-devops-project-nodejs/edit-release-pipeline.png)

1. A Build folyamathoz hasonlóan módosítsa a csomópont feladatát 12. x használatára, és tiltsa le a két teszt feladatot. A kiadásnak hasonlónak kell lennie a képernyőképhez.

    ![Befejezett kiadási folyamat](_img/azure-devops-project-nodejs/release-pipeline-complete.png)

1. A böngésző bal oldalán lépjen a **views/index. mopsz** fájlra.

1. Válassza a **Szerkesztés** lehetőséget, majd módosítsa a H2-fejlécet.  Írja be például, hogy az első **lépések azonnal elérhetők legyenek az Azure DevOps Starter** vagy más módosítással.

1. Válassza **a** végrehajtás lehetőséget, majd mentse a módosításokat.

1. A böngészőben nyissa meg a DevOps Starter irányítópultját.   
Ekkor megjelenik egy Build folyamatban. Az elvégzett módosításokat a rendszer automatikusan felépíti és telepíti a CI/CD-folyamaton keresztül.

## <a name="commit-your-changes-and-examine-the-azure-cicd-pipeline"></a>Véglegesítse a módosításokat, és vizsgálja meg az Azure CI/CD folyamatát

Az előző két lépésben hozzáadott egy Gatsby létrehozott PWA a git-tárházhoz, és szerkesztette a folyamatokat a kód összeállításához és üzembe helyezéséhez. Véglegesítheti a kódot, és megtekintheti az előrehaladást a build és a Release folyamaton keresztül.

1. A projekt git-tárházának gyökeréből egy terminálon futtassa a következő parancsokat a kód Azure DevOps-projektbe való leküldéséhez:

    ```powershell
    git add .
    git commit -m "My first Gatsby PWA"
    git push
    ```
    
1. A buildek a befejezést követően kezdődnek `git push` . Az **Azure DevOps irányítópultján** követheti a folyamat előrehaladását.

3. Néhány perc elteltével a létrehozási és kiadási folyamatokat be kell fejezni, és a PWA üzembe kell helyezni egy tárolón. Kattintson az **alkalmazás-végpont** hivatkozásra a fenti irányítópulton, és egy Gatsby Starter-projektet kell megjelennie a blogokhoz.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Törölheti Azure App Service és az egyéb kapcsolódó erőforrásokat, amelyeket akkor hozott létre, amikor már nincs szüksége az erőforrásokra. Használja a **delete** funkciót a DevOps Starter irányítópultján.

## <a name="next-steps"></a>Következő lépések

A CI/CD folyamat konfigurálásakor a rendszer automatikusan létrehozza a létrehozási és kiadási folyamatokat. Ezeket a build és kiadási folyamatokat a csapat igényeinek megfelelően módosíthatja. A CI/CD folyamattal kapcsolatos további tudnivalókért tekintse meg a következőt:

> [!div class="nextstepaction"]
> [CD-folyamat testreszabása](/azure/devops/pipelines/release/define-multistage-release-process)
