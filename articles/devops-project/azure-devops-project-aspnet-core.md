---
title: 'Rövid útmutató: CI/CD-folyamat létrehozása a .NET-hez az Azure DevOps Starter használatával'
description: Az Azure DevOps Starter megkönnyíti az Azure-ban való ismerkedést. A segítségével néhány gyors lépéssel elindíthat egy .NET-alkalmazást a választott Azure-szolgáltatásban.
ms.prod: devops
ms.technology: devops-cicd
services: azure-devops-project
documentationcenter: vs-devops-build
author: mlearned
manager: gwallace
editor: ''
ms.assetid: ''
ms.workload: web
ms.tgt_pltfrm: na
ms.topic: quickstart
ms.date: 02/23/2021
ms.author: mlearned
ms.custom: devx-track-csharp, mvc
ms.openlocfilehash: 59f8d721c23cbb033f295f2ef3045fc96f08ba96
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2021
ms.locfileid: "101733183"
---
# <a name="create-a-cicd-pipeline-for-net-with-azure-devops-starter"></a>CI/CD-folyamat létrehozása .NET-hez az Azure DevOps Starterrel

Konfigurálja a folyamatos integrációt (CI) és a folyamatos szállítást (CD) a .NET Core-vagy ASP.NET-alkalmazáshoz a DevOps Starter használatával. A DevOps Starter leegyszerűsíti az Azure-folyamatok létrehozási és kiadási folyamatának kezdeti konfigurációját.

Ha nem rendelkezik Azure-előfizetéssel, létrehozhat egy ingyenes fiókot a [Visual Studio Dev Essentials](https://visualstudio.microsoft.com/dev-essentials/) segítségével.

## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra

A DevOps Starter egy CI/CD-folyamatot hoz létre az Azure DevOps. Létrehozhat egy új Azure DevOps-szervezetet, vagy használhat egy meglévő céget is. A DevOps Starter Azure-erőforrásokat is létrehoz az Ön által választott Azure-előfizetésben.

1. Jelentkezzen be a [Microsoft Azure Portalra](https://portal.azure.com).

1. A keresőmezőbe írja be a **DevOps Starter** kifejezést, majd válassza a elemet. Kattintson a **Hozzáadás** gombra egy új létrehozásához. 

    ![A DevOps Starter irányítópultja](_img/azure-devops-starter-aks/search-devops-starter.png)

## <a name="select-a-sample-application-and-azure-service"></a>Mintaalkalmazás és Azure-szolgáltatás kiválasztása

1. Válassza a **.NET** mintaalkalmazást. A .NET-minták esetén a nyílt forráskódú ASP.NET- vagy a platformfüggetlen .NET Core-keretrendszer közül választhat.

   ![.NET-keretrendszer](_img/azure-devops-project-aspnet-core/select-dotnet.png)
   
   > [!NOTE]
   > A DevOps Starter beállításának alapértelmezett beállítása a **GitHub**, de ez a beállítás a varázslóból is módosítható.
2. Ez a minta egy ASP.NET Core MVC-alkalmazás. Válassza ki a **.net Core** alkalmazás-keretrendszert, majd kattintson a **tovább** gombra.    
    
3. Válassza a **Windows Web App** központi telepítési cél lehetőséget, majd kattintson a **tovább** gombra. Igény szerint további Azure-szolgáltatásokat is kiválaszthat a telepítéshez. Az alkalmazás-keretrendszer, amelyet korábban választott, megdiktálja az Azure-szolgáltatások telepítési céljának itt elérhető típusát.

## <a name="configure-azure-devops-and-an-azure-subscription"></a>Azure-DevOps és Azure-előfizetés konfigurálása 

1. Adja meg a **projekt nevét**.

2. Hozzon létre egy új ingyenes **Azure DevOps-szervezetet** , vagy válasszon ki egy meglévő szervezetet a legördülő listából.

3. Válassza ki az **Azure-előfizetését**, adjon meg egy nevet a **webalkalmazásnak** , vagy használja az alapértelmezett értéket, majd válassza a **kész** lehetőséget. Néhány perc elteltével a DevOps Starter Deployment áttekintése megjelenik a Azure Portal. 

4. Válassza az **Ugrás az erőforráshoz** lehetőséget a DevOps Starter irányítópultjának megtekintéséhez. A jobb felső sarokban rögzítse a **projektet** az irányítópulton a gyors hozzáférés érdekében. Egy minta alkalmazás van beállítva az **Azure DevOps-szervezetben** lévő tárházban. A rendszer végrehajtja a buildet, és az alkalmazást üzembe helyezi az Azure-ban.

5. Az irányítópulton látható a kód tárháza, a CI/CD-folyamat és az Azure-beli alkalmazás. Az Azure-erőforrások alatt kattintson a **Tallózás** gombra a futó alkalmazás megtekintéséhez.

   ![Irányítópult nézet](_img/azure-devops-project-aspnet-core/dashboardnopreview.png) 

## <a name="commit-code-changes-and-execute-cicd"></a>Kódmódosítások véglegesítése és a CI/CD végrehajtása

Az DevOps Starter létrehozta a git-tárházat az Azure Reposben vagy a GitHubban. Az adattár megtekintéséhez és a kód módosításának végrehajtásához tegye a következőket:

1. A DevOps Starter irányítópult bal oldalán válassza ki a **fő** ág hivatkozását. Ez a hivatkozás megnyitja az újonnan létrehozott Git-adattár nézetét.

2. A következő néhány lépésben a webböngészővel teheti meg és véglegesítheti a kód módosításait közvetlenül a **fő** ágra. A git-tárházat kedvenc IDE klónozásával is elvégezheti a tárház oldal jobb felső részén található **klónozás** lehetőség kiválasztásával. 

3. A bal oldalon navigáljon az alkalmazás-fájl struktúrában az **Application/ASPNET-Core-DotNet-Core/Pages/index. cshtml** elemre.

4. Válassza a **Szerkesztés** lehetőséget, majd módosítsa a H2-fejlécet. Írja be például a következőt: **első lépések azonnal az Azure DevOps Starter,** vagy végezze el a többi módosítást.

      ![Kódszerkesztések](_img/azure-devops-project-aspnet-core/codechange.png)

5. Válassza a végrehajtás lehetőséget, hagyja meg a megjegyzést, **és válassza a** végrehajtás **újra lehetőséget.**

6. A böngészőben nyissa meg az Azure DevOps Starter irányítópultját.  Látni fogja, hogy folyamatban van egy build. Az elvégzett módosításokat a rendszer automatikusan felépíti és telepíti a CI/CD-folyamaton keresztül.

## <a name="examine-the-cicd-pipeline"></a>A CI/CD-folyamat vizsgálata

Az előző lépésben az Azure DevOps Starter automatikusan konfigurált egy teljes CI/CD-folyamatot. Vizsgálja meg és szükség szerinti szabja testre a folyamatot. Az alábbi lépéseket követve Ismerkedjen meg az Azure DevOps Build és Release folyamatával.

1. A DevOps alapszintű irányítópultjának tetején válassza a **folyamatok létrehozása** lehetőséget. Ez a hivatkozás egy böngésző fület és az Azure DevOps Build folyamatát nyitja meg az új projekthez.

1. Válassza a három pontot (...).  Ez a művelet egy menüt nyit meg, ahol számos tevékenységet indíthat el, például egy új Build kiépítését, egy Build szüneteltetését és a build folyamat szerkesztését.

1. Válassza a **Szerkesztés** elemet.

    ![Buildelési folyamat](_img/azure-devops-project-aspnet-core/builddef.png)

1. Ezen az ablaktáblán ellenőrizheti a felépítési folyamat különféle feladatait. A build különböző feladatokat hajt végre, például a git-tárházból beolvassa a forrásokat, visszaállítja a függőségeket, és közzéteszi az üzembe helyezéshez használt kimeneteket.

1. A buildelési folyamat tetején válassza a buildelési folyamat nevét.

1. Módosítsa a felépítési folyamat nevét egy ennél több leíróra, válassza a **mentés & üzenetsor** lehetőséget, majd kattintson a **Mentés** gombra.

1. A buildelési folyamat neve alatt válassza az **Előzményeket**.   
Az **Előzmények** ablaktáblán megjelenik a Build legutóbbi változásainak naplózási nyomvonala.  Az Azure-folyamatok nyomon követik a felépítési folyamaton végrehajtott módosításokat, és lehetővé teszik a verziók összehasonlítását.

1. Válassza az **Eseményindítók** lehetőséget. A DevOps Starter automatikusan létrehozta a CI-triggert, és az adattárba való minden kötelezettség új buildet indít el. Lehetősége van belefoglalni az ágakat, vagy kizárni őket a CI-folyamatból.

1. Válassza a **Megtartást**. A forgatókönyvtől függően megadhat olyan házirendeket, amelyek bizonyos számú buildet megtartanak vagy eltávolítanak.

1. Válassza a **Létrehozás és kiadás**, majd a **kiadások** lehetőséget.  
A DevOps Starter egy kiadási folyamatot hoz létre az Azure-ban üzemelő példányok kezeléséhez.

1.  A bal oldalon válassza a kiadási folyamat melletti három pontot (...), majd válassza a **Szerkesztés** lehetőséget. A kiadási folyamat tartalmaz egy folyamatot, amely meghatározza a kiadási folyamatot.  

1. Az **Összetevők** alatt válassza az **Elvetést**. Az előző lépésekben megvizsgált buildelési folyamat létrehozza a munkadarabhoz használt kimenetet. 

1. A **drop** ikon mellett válassza ki a **folyamatos üzembe helyezési triggert**. Ez a kiadási folyamat egy engedélyezett CD-triggerrel rendelkezik, amely minden alkalommal futtat egy központi telepítést, amikor új Build-összetevő érhető el. Igény szerint letilthatja az indítást, hogy a központi telepítések manuális végrehajtást igényeljenek.  

1. A bal oldalon válassza a **feladatok** lehetőséget.  A feladatok azok a tevékenységek, amelyeket a telepítési folyamat végrehajt. Ebben a példában egy feladatot hoztak létre a Azure App Serviceba való üzembe helyezéshez.

1. A jobb oldalon válassza a **kiadások megtekintése** lehetőséget. Ebben a nézetben a kiadások előzményei jelennek meg.

1. Válassza ki az egyik kiadás melletti három pontot (...), majd kattintson a **Megnyitás** gombra. Több menüt is megvizsgálhat, például a kiadás összegzését, a kapcsolódó munkaelemeket és teszteket.

1. Válassza a **Véglegesítéseket**. Ez a nézet az adott központi telepítéshez társított kód-véglegesítő kódokat jeleníti meg. 

1. Válassza a **naplók** lehetőséget. A naplók hasznos információkat tartalmaznak az üzembehelyezési folyamattal kapcsolatban. Ezek az üzembe helyezések alatt és után is megtekinthetők.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Törölheti Azure App Service és az egyéb kapcsolódó erőforrásokat, amelyeket akkor hozott létre, ha már nincs rá szükség. Használja a **delete** funkciót a DevOps Starter irányítópultján.

## <a name="next-steps"></a>Következő lépések

Ha arra vonatkozó információkra van szüksége, hogyan lehet módosítani a buildet és a kiadási folyamatokat a csapat igényei szerint, tekintse át az alábbi oktatóanyagot:

> [!div class="nextstepaction"]
> [CD-folyamat testreszabása](/azure/devops/pipelines/release/define-multistage-release-process?view=vsts)

## <a name="videos"></a>Videók

> [!VIDEO https://www.youtube.com/embed/itwqMf9aR0w]
