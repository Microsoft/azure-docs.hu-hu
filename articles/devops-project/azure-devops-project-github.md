---
title: 'Oktatóanyag: CI/CD-folyamat létrehozása meglévő kódhoz az Azure DevOps Starter használatával'
description: Az Azure DevOps Starter megkönnyíti az Azure-ban való ismerkedést. Néhány gyors lépéssel a DevOps Projects a saját kód és a GitHub-tárház használatát teszi lehetővé az alkalmazások Azure-szolgáltatásokban való indításához.
services: vsts
documentationcenter: vs-devops-build
ms.author: mlearned
ms.manager: gwallace
ms.workload: web
ms.prod: devops
ms.technology: devops-cicd
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 03/24/2020
author: mlearned
ms.custom: mvc
ms.openlocfilehash: c03ba657ac264b72d035f28956354398421ed2f9
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102566600"
---
# <a name="create-a-cicd-pipeline-for-github-repo-using-azure-devops-starter"></a>CI/CD-folyamat létrehozása a GitHub-tárházhoz az Azure DevOps Starter használatával

Az Azure DevOps Starter egy egyszerűsített folyamatot biztosít a folyamatos integráció (CI) és a folyamatos átviteli (CD) folyamatok Azure-hoz történő létrehozásához. Használhatja a meglévő kódot és a git-tárházat, vagy kijelölhet egy minta alkalmazást is.

Az alábbiakat fogja elvégezni:

> [!div class="checklist"]
> * A DevOps Starter használata CI/CD-folyamat létrehozásához
> * A GitHub-tárházhoz való hozzáférés konfigurálása és a keretrendszer kiválasztása
> * Azure-DevOps és Azure-előfizetés konfigurálása 
> * Módosítások elvégzése a GitHubon, és automatikus üzembe helyezése az Azure-ban
> * Az Azure-folyamatok CI/CD-folyamatának vizsgálata
> * Az erőforrások eltávolítása

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Létrehozhat egy ingyenes fiókot a [Visual Studio Dev Essentials](https://visualstudio.microsoft.com/dev-essentials/) segítségével.
* Hozzáférés egy olyan GitHubhoz vagy külső git-tárházhoz, amely .NET-, Java-, PHP-, Node.js-, Python-vagy statikus webes kódot tartalmaz.

## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra

Az Azure DevOps Starter egy CI/CD-folyamatot hoz létre az Azure-folyamatokban. Létrehozhat egy új Azure DevOps-szervezetet, vagy használhat egy meglévő céget is. Az Azure DevOps Starter Azure-erőforrásokat is létrehoz az Ön által választott Azure-előfizetésben.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

1. A keresőmezőbe írja be a **DevOps Starter** kifejezést, majd válassza a elemet. Kattintson a **Hozzáadás** gombra egy új létrehozásához.

    ![A DevOps Starter irányítópultja](_img/azure-devops-starter-aks/search-devops-starter.png)
    
1. Válassza **a saját kód** használata lehetőséget, majd kattintson a **tovább** gombra.

## <a name="configure-access-to-your-github-repo-and-select-a-framework"></a>A GitHub-tárházhoz való hozzáférés konfigurálása és a keretrendszer kiválasztása

1. Válassza a **GitHub** vagy egy külső **git** -kód tárház lehetőséget. Ebben az oktatóanyagban válassza a **GitHub** lehetőséget. Előfordulhat, hogy először hitelesítenie kell a GitHubot, hogy az Azure hozzáférjen a GitHub-tárházhoz.

1. Válasszon egy **tárházat** és egy **ágat**, majd válassza a **tovább** lehetőséget.

1. Ha Docker-tárolókat használ, módosítsa **az alkalmazás Dockerized** **Igen** értékre. Ebben az oktatóanyagban hagyja a **nincs** kiválasztva lehetőséget, majd kattintson a **tovább** gombra. A Docker-tárolók használatával kapcsolatos további információkért vigye az egérmutatót az **i** ikon fölé.

   ![Az alkalmazás-keretrendszer kiválasztása a legördülő menüben](_img/azure-devops-project-github/appframework.png)

1. A legördülő menüben válasszon ki egy alkalmazás- **futtatókörnyezetet** és egy **alkalmazás-keretrendszert**, majd válassza a **tovább** lehetőséget. Az alkalmazás-keretrendszer az elérhető Azure Service-telepítési cél típusát diktálja.

1. Válasszon ki egy **Azure-szolgáltatást** az alkalmazás telepítéséhez, majd kattintson a **tovább** gombra.

## <a name="configure-azure-devops-and-an-azure-subscription"></a>Azure-DevOps és Azure-előfizetés konfigurálása

1. Adja meg a **projekt nevének** nevét.

1. Hozzon létre egy új ingyenes céget az **Azure DevOps-szervezetben** , vagy válasszon ki egy meglévő szervezetet a legördülő menüből.

1. Válassza ki az előfizetését az **Azure-előfizetésben**, és adjon meg egy nevet a **webalkalmazásban** , vagy használja az alapértelmezett értéket. Válasszon ki egy **helyet**, majd kattintson a **kész** gombra. Néhány perc elteltével a DevOps Starter Deployment áttekintése megjelenik a Azure Portal.

1. Válassza az **Ugrás az erőforráshoz** lehetőséget a DevOps Starter irányítópultjának megtekintéséhez. A jobb felső sarokban rögzítse a **projektet** az irányítópulton a gyors hozzáférés érdekében. Az Azure DevOps Starter automatikusan konfigurálja a CI-Build és a kiadási triggert. A kód a GitHub-tárházban vagy egy másik külső tárházban marad, és egy minta alkalmazás van beállítva egy adattárban az **Azure DevOps-szervezetben**. Az Azure DevOps Starter elindítja a buildet, és üzembe helyezi az alkalmazást az Azure-ban.

   ![Azure DevOps Projects irányítópult nézet](_img/azure-devops-project-github/projectsdashboard.png)

1. Az irányítópulton a kód tárháza, a CI/CD-folyamat és az Azure-beli alkalmazás látható. A jobb oldalon az Azure-erőforrások területen válassza a **Tallózás** lehetőséget a futó alkalmazás megtekintéséhez.

## <a name="commit-changes-to-github-and-automatically-deploy-them-to-azure"></a>Módosítások elvégzése a GitHubon, és automatikus üzembe helyezése az Azure-ban

Most már készen áll az alkalmazásban való együttműködésre a csapattal. A CI/CD-folyamat automatikusan üzembe helyezi a legújabb munkát a webhelyén. A GitHub-tárház minden módosítása elindítja az Azure DevOps, és egy CD-folyamat futtatja az Azure-ba való üzembe helyezést.

1. A DevOps Starter irányítópultján válassza a **tárak** lehetőséget. Megnyílik a GitHub-tárház egy új böngésző lapon. Módosítsa az alkalmazást, majd válassza a **módosítások elvégzése** lehetőséget.

1. Néhány pillanat elteltével a buildek az Azure-folyamatokban kezdődnek. A Build állapotát a DevOps Starter irányítópultján figyelheti. Az Azure DevOps-szervezetben is figyelheti azt, ha a DevOps Starter irányítópultján a **folyamatok összeállítása** lapot választja.

## <a name="examine-the-azure-pipelines-cicd-pipeline"></a>Az Azure-folyamatok CI/CD-folyamatának vizsgálata

Az Azure DevOps Starter automatikusan konfigurálja a CI/CD-folyamatokat az Azure-folyamatokban. Vizsgálja meg és szükség szerinti szabja testre a folyamatot. A build és a Release folyamat megismeréséhez tegye a következőket:

1. A DevOps Starter irányítópultján válassza a **folyamatok létrehozása** lehetőséget.

1. Az **Azure-folyamatok** lap megnyitása után megjelenik a legutóbbi buildek előzményei, valamint az egyes buildek állapota.

   ![Az Azure-folyamatok buildek lapja](_img/azure-devops-project-github/pipelinesbuildpage.png)

1. **A builds oldal jobb** felső sarkában a **Szerkesztés** lehetőségre kattintva módosíthatja az aktuális Build, a **várólista** új Build hozzáadását, vagy a függőleges három pont (**&#8942;**) gombot egy menü megnyitásához a további beállításokkal. Válassza a **Szerkesztés** elemet.

1. A build különböző feladatokat hajt végre, például a források beolvasása a tárházból, a függőségek visszaállítása és a kimenetek közzététele a központi telepítésekhez. A jobb oldalon a **név** területen módosítsa a build-folyamat nevét egy ennél több leíróra. Válassza a **mentés & üzenetsor** lehetőséget, majd kattintson a **Mentés** gombra. Írjon be egy megjegyzést, majd kattintson ismét a **Save (Mentés** ) gombra.

   ![Az Azure DevOps builds oldala](_img/azure-devops-project-github/buildpage.png)

1. Ha meg szeretné tekinteni a Build legutóbbi változásainak naplózását, válassza az **Előzmények** fület.  Az Azure DevOps nyomon követi a felépítési folyamaton végrehajtott módosításokat, és lehetővé teszi a verziók összehasonlítását.

1. Válassza az **Eseményindítók** fület. Azure DevOps projects automatikusan létrehoz egy CI-eseményindítót néhány alapértelmezett beállítással. Olyan eseményindítókat is beállíthat, mint például a **folyamatos integráció lehetővé tétele** , hogy minden egyes kód módosításakor futtasson egy buildet. Az eseményindítókat beállíthatja úgy is, hogy az adott időpontokban futtatandó buildek ütemezhetők legyenek.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szüksége Azure App Servicera és az oktatóanyagban létrehozott kapcsolódó erőforrásokra, törölheti őket. Használja a **delete** funkciót a DevOps projects irányítópulton.

## <a name="next-steps"></a>Következő lépések

Ha ebben az oktatóanyagban konfigurálta a CI/CD-folyamatot, a Azure DevOps Projects-ben automatikusan létrehozta a létrehozási és kiadási folyamatokat. A csapat igényeihez igazodva módosíthatja ezt a buildet és a kiadási folyamatokat.

A CI/CD folyamattal kapcsolatos további tudnivalókért tekintse meg a következőt:

> [!div class="nextstepaction"]
> [A többfázisú folyamatos üzembe helyezés (CD) folyamatának meghatározása](/azure/devops/pipelines/release/define-multistage-release-process)

Az alkalmazások figyeléséről további információt a következő témakörben talál:
  
 > [!div class="nextstepaction"]
 > [Mi az Azure monitor?](../azure-monitor/overview.md)