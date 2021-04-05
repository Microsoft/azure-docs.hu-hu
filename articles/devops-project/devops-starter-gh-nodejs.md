---
title: 'Gyors útmutató: CI/CD-munkafolyamatok létrehozása az Node.js-DevOps Starter for GitHub használatával az Azure-ba való üzembe helyezéshez'
description: A DevOps Starter megkönnyíti az Azure használatának megkezdését a GitHub-műveletekkel.
author: ninallam
editor: ninallam
ms.assetid: ''
ms.devlang: na
ms.service: az-devops-project
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: ''
ms.date: 08/24/2020
ms.author: ninallam
ms.openlocfilehash: e3c72ce6a15f90f0dbe08bbff10db0ca5f6b5c8f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "97588811"
---
# <a name="set-up-cicd-for-a-nodejs-app-with-devops-starter-using-github-actions"></a>CI/CD beállítása Node.js alkalmazáshoz a DevOps Starter használatával GitHub-műveletekkel

Ebben a rövid útmutatóban az egyszerűsített DevOps alapszintű felülettel hozhat létre egy folyamatos integrációs (CI) és folyamatos kézbesítési (CD) munkafolyamatot a Node.js alkalmazáshoz a GitHub-műveletek használatával. Az DevOps Starter használatával beállíthatja az alkalmazás fejlesztéséhez, üzembe helyezéséhez és monitorozásához szükséges mindent. 

## <a name="prerequisites"></a>Előfeltételek

- Aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egy fiókot ingyenesen](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio). 
- Egy [GitHub](https://github.com/)-fiók.

## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra

A DevOps Starter egy CI/CD-munkafolyamatot hoz létre a GitHub-műveletek használatával. A DevOps Starter Azure-erőforrásokat is létrehoz az Ön által választott Azure-előfizetésben.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

1. A keresőmezőbe írja be a **DevOps Starter** kifejezést, majd válassza a elemet. Kattintson a **Hozzáadás** gombra egy új létrehozásához.

    ![A DevOps Starter irányítópultja](_img/azure-devops-starter-aks/search-devops-starter.png)

## <a name="select-a-sample-application-and-azure-service"></a>Mintaalkalmazás és Azure-szolgáltatás kiválasztása

1. Kattintson a **DevOps Starter beállítása a githubon** lehetőségre a jobb oldali szalagcímen.

    ![landing_page](_img/azure-devops-project-nodejs/landing-page.png)

1. Győződjön meg arról, hogy a CI/CD-szolgáltató **GitHub-műveletként** van kiválasztva.

    ![CICD_provider](_img/azure-devops-project-nodejs/provider-selection.png)

1. Válassza a **Node.js** mintaalkalmazást. A Node.js-minták esetén több alkalmazási keretrendszer közül választhat.

1. Az alapértelmezett mintakeretrendszer az **Express.js**. Hagyja meg az alapértelmezett beállítást, majd kattintson a **tovább** gombra.   

2. A Windows Web App az alapértelmezett telepítési cél. Az alkalmazás-keretrendszer, amelyet korábban választott, az itt elérhető Azure-szolgáltatás telepítési célhelyét diktálja. Hagyja meg az alapértelmezett szolgáltatást, majd válassza a **tovább** lehetőséget.
 
## <a name="configure-github-account-and-an-azure-subscription"></a>GitHub-fiók és Azure-előfizetés konfigurálása 

1. Hitelesítés a GitHub használatával.

   1. Kattintson az **Engedélyezés** gombra. 
   
   1. Jelentkezzen be a GitHubba. Ha nem rendelkezik GitHub-fiókkal, itt is regisztrálhat.

2. Válasszon egy meglévő **GitHub-szervezetet**. 
   
   1. Válassza ki a GitHub-tárház nevét. 
   
   1. Válassza ki az Azure-előfizetést és-helyet, válasszon egy nevet az alkalmazásnak, majd válassza a **kész** lehetőséget.
    
       ![Enter_GH_details](_img/azure-devops-project-nodejs/gh-details.png)


    Néhány perc elteltével a DevOps Starter irányítópultja megjelenik a Azure Portal. Egy minta alkalmazás van beállítva az Azure DevOps-szervezetben lévő adattárban, egy GitHub-munkafolyamatot indít el, és az alkalmazást üzembe helyezi az Azure-ban. Ez az irányítópult a tárházba, a GitHub-munkafolyamatba és az Azure-beli alkalmazásba is betekintést nyújt.
   
3. A futó alkalmazás megtekintéséhez válassza a **Tallózás** lehetőséget.
    
    Az irányítópult a GitHub-munkafolyamatok és az Azure-erőforrások részleteit tartalmazza. Ha meg szeretné tekinteni a GitHub-munkafolyamatok részleteit, például a legújabb Futtatás, a véglegesítés és a feladatok állapotát, akkor engedélyeznie kell a **githubot**.
   
   ![Authorize_dashboard](_img/azure-devops-project-nodejs/authenticate-dashboard.png)

A DevOps Starter automatikusan beállította a GitHub-munkafolyamatot a létrehozási és üzembe helyezési feladatokkal GitHub-műveletek Most már készen áll, hogy egy csapattal közösen dolgozzon a Node.js-alkalmazáson egy olyan CI-/CD-folyamat használatával, amely automatikusan üzembe helyezi a legújabb munkáját a webhelyen.

   ![dashooard_view](_img/azure-devops-project-nodejs/full-dashboard.png)

## <a name="commit-code-changes-and-execute-cicd"></a>Kódmódosítások véglegesítése és a CI/CD végrehajtása

A DevOps Starter létrehoz egy tárházat a GitHubban. Az adattár megtekintéséhez és a kód módosításának végrehajtásához tegye a következőket:

1. A DevOps Starter irányítópult bal oldalán válassza ki a fő ág hivatkozását. Ez a hivatkozás egy nézetet nyit meg az újonnan létrehozott GitHub-tárházban.

1. A tárház klónozási URL-címének megtekintéséhez válassza a böngésző jobb felső sarkában található **klónozás** elemet. A Git-adattárat klónozhatja például a kedvenc IDE-környezetébe. A következő néhány lépésben a webböngészővel teheti meg és véglegesítheti a kód módosításait közvetlenül a fő ágra.

1. A böngésző bal oldalán lépjen a **/Application/views/index.PUG** fájlra.

1. Válassza a **Szerkesztés** lehetőséget, majd végezze el a módosítást egy bizonyos szövegre.
    Módosíthatja például az egyik címke szövegét.

1. Válassza **a** végrehajtás lehetőséget, majd mentse a módosításokat.

1. A böngészőben nyissa meg a DevOps Starter irányítópultját.   
Ekkor meg kell jelennie a GitHub-munkafolyamat felépítési feladatainak. Az elvégzett módosításokat a GitHub-munkafolyamatok automatikusan építették és telepítik.

## <a name="view-the-github-workflow"></a>A GitHub-munkafolyamat megtekintése

Az előző lépésben a DevOps Starter automatikusan egy teljes GitHub-munkafolyamatot konfigurált. Szükség szerint vizsgálja meg és szabja testre a munkafolyamatot. A következő lépésekkel Ismerkedjen meg a munkafolyamattal.

1. A DevOps Starter irányítópult bal oldalán válassza a **GitHub-munkafolyamat** elemet. Ez a hivatkozás egy böngésző fület és a GitHub-munkafolyamatot nyitja meg az új projekthez.
    > [!NOTE]
    > Ne nevezze át a munkafolyamat-fájlt. A munkafolyamat-fájl nevének **devops-Starter-workflow. YML** kell lennie ahhoz, hogy az irányítópult tükrözze a módosításokat

1. A munkafolyamat YAML fájlja tartalmazza az alkalmazás létrehozásához és üzembe helyezéséhez szükséges összes GitHub-műveletet. Kattintson a **fájl szerkesztése** lehetőségre a munkafolyamat-fájl testreszabásához.

1. A tárház **kód** lapján kattintson a **véglegesítés** elemre. Ez a nézet az adott központi telepítéshez társított kód-véglegesítő kódokat jeleníti meg.

1. A tárház **műveletek** lapján megtekintheti az adattár összes munkafolyamat-futtatásának előzményeit.

1. A **legutóbbi Futtatás** lehetőség kiválasztásával megtekintheti a munkafolyamatban futtatott összes feladatot.

1. A **feladatokra** kattintva megtekintheti a munkafolyamat-Futtatás részletes naplóit. A naplók hasznos információkat tartalmaznak az üzembehelyezési folyamattal kapcsolatban. Ezek az üzembe helyezések alatt és után is megtekinthetők.

1. Kattintson a **pull-kérelem** lapra a tárházban lévő összes lekéréses kérelem megtekintéséhez

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szüksége rájuk, törölheti Azure App Service és az egyéb kapcsolódó erőforrásokat is. Használja a **delete** funkciót a DevOps Starter irányítópultján.

## <a name="next-steps"></a>Következő lépések

A CI/CD folyamat beállításakor a rendszer automatikusan létrehozta a GitHub-munkafolyamatot. Ezt a munkafolyamatot úgy módosíthatja, hogy az megfeleljen a csapat igényeinek. A GitHub-műveletekkel és-munkafolyamatokkal kapcsolatos további tudnivalókért tekintse meg a következőt:

> [!div class="nextstepaction"]
> [A GitHub-munkafolyamat testreszabása](https://docs.github.com/actions/configuring-and-managing-workflows/configuring-and-managing-workflow-files-and-runs)
