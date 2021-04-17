---
title: 'Rövid útmutató: CI-/CD-folyamat létrehozása Pythonhoz az Azure DevOps Starter használatával'
description: A DevOps Starter megkönnyíti az Azure használatának első lépését. A segítségével néhány gyors lépésben elindíthat egy alkalmazást a választott Azure-szolgáltatásban.
services: vsts
documentationcenter: vs-devops-build
author: mlearned
ms.author: mlearned
manager: gwallace
ms.date: 03/24/2020
ms.prod: devops
ms.technology: devops-cicd
ms.topic: quickstart
ms.workload: web
ms.tgt_pltfrm: na
ms.custom:
- mvc
- devx-track-python
- mode-api
ms.openlocfilehash: d236eadd4c1c3bbda5a781d00de43e4ed3dc88b9
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107533273"
---
# <a name="create-a-cicd-pipeline-for-python-with-azure-devops-starter"></a>CI/CD-folyamat létrehozása Pythonhoz az Azure DevOps Starter használatával

Ebben a rövid útmutatóban az Azure DevOps Starter egyszerűsített használatával állíthat be egy folyamatos integrációs (CI) és folyamatos kézbesítési (CD) folyamatot Python-alkalmazáshoz az Azure Pipelinesban. Az Azure DevOps Starter használatával mindent beállíthat, amire szüksége lehet az alkalmazás fejlesztéséhez, üzembe helyezéséhez és monitorozáshoz. 

## <a name="prerequisites"></a>Előfeltételek

- Aktív előfizetéssel rendelkezik egy Azure-fiók. [Hozzon létre egy ingyenes fiókot.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) 
- [Egy Azure DevOps-fiók](https://azure.microsoft.com/services/devops/) és -szervezet.

## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra

A DevOps Starter létrehoz egy CI-/CD-folyamatot az Azure Pipelinesban. Létrehozhat egy új Azure DevOps-szervezetet, vagy használhat egy meglévőt. A DevOps Starter azure-erőforrásokat is létrehoz a választott Azure-előfizetésben.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com). 

1. A keresőmezőbe írja be a **DevOps Starter**, majd a lehetőséget. Kattintson a **Hozzáadás gombra** egy új létrehozásához.

    ![A DevOps Starter irányítópultja](_img/azure-devops-starter-aks/search-devops-starter.png) 

## <a name="select-a-sample-application-and-azure-service"></a>Mintaalkalmazás és Azure-szolgáltatás kiválasztása

1. Válassza ki a Python mintaalkalmazást. A Python-minták esetén több alkalmazási keretrendszer közül választhat.

1. Az alapértelmezett mintakeretrendszer a Django. Hagyja meg az alapértelmezett beállítást, majd válassza a **Tovább lehetőséget.** Az alapértelmezett üzembehelyezési cél a Web App for Containers. A korábban kiválasztott alkalmazás-keretrendszer határozza meg az Itt elérhető Azure-szolgáltatástelepítési cél típusát. 

3. Hagyja meg az alapértelmezett szolgáltatást, majd válassza a **Tovább lehetőséget.**
 
## <a name="configure-azure-devops-and-an-azure-subscription"></a>Az Azure DevOps és egy Azure-előfizetés konfigurálása 

1. Hozzon létre egy új Azure DevOps Services-szervezetet, vagy válasszon ki egy meglévő szervezetet. 

    1. Adja meg a projekt nevét az Azure DevOpsban.  

    1. Válassza ki azure-előfizetését és helyét, adja meg az alkalmazás nevét, majd válassza a Kész **lehetőséget.**  
    
     Néhány perc múlva megjelenik a Kezdő irányítópult a Azure Portal. A rendszer beállít egy mintaalkalmazást az Azure DevOps-szervezet adattárában, végrehajt egy buildet, és üzembe helyez egy alkalmazást az Azure-ban. Ez az irányítópult biztosítja a kódtár, a CI-/CD-folyamat és az Azure-beli alkalmazás láthatóságát.  
    
2. Válassza **a Tallózás lehetőséget** a futó alkalmazás megtekintéséhez.

    ![Irányítópult nézet](_img/azure-devops-project-python/dashboardnopreview.png) 
    
   DevOps Projects automatikusan konfigurálja a CI-buildet és a kiadási eseményindítót. Most már készen áll arra, hogy egy csapattal közösen dolgozzon egy Python-alkalmazáson egy OLYAN CI/CD-folyamat használatával, amely automatikusan üzembe helyezheti a legújabb munkáját a webhelyén.

## <a name="commit-code-changes-and-execute-cicd"></a>Kódmódosítások véglegesítése és a CI/CD végrehajtása

A DevOps Starter létrehoz egy Git-adattárat az Azure Reposban vagy a GitHubon. Az adattár megtekintéséhez és az alkalmazás kódon való módosításaihoz tegye a következőket: 

1. A DevOps Starter irányítópult bal oldalán válassza a főág hivatkozását. Ez a hivatkozás megnyitja az újonnan létrehozott Git-adattár nézetét.

1. Az adattárklón URL-címének megtekintéséhez válassza a **Klónozás** lehetőséget a böngésző jobb felső részén. A Git-adattárat klónozhatja például a kedvenc IDE-környezetébe. A következő néhány lépésben a webböngészővel közvetlenül a főágban is végre lehet majd végleges változtatni a kódokat.

1. A bal oldalon kattintson az **app/templates/app/index.html fájlra.**

1. Válassza a **Szerkesztés** elemet, és módosítson a szöveg valamely részén. Módosítsa például az egyik div címkén belüli szöveget.

1. Válassza **a Véglegesítés** lehetőséget, majd mentse a módosításokat.

1. A böngészőben menjen a DevOps Starter irányítópultra. Most már látnia kell egy buildet. Az előbb végrehajtott módosítások automatikusan létrejönnek és üzembe kerülnek egy CI-/CD-folyamaton keresztül.

## <a name="examine-the-cicd-pipeline"></a>A CI-/CD-folyamat vizsgálata

Az előző lépésben a DevOps Starter automatikusan konfigurált egy teljes CI-/CD-folyamatot. Vizsgálja meg és szükség szerinti szabja testre a folyamatot. A build- és kiadási folyamatok megismerésük érdekében tegye a következőket:

1. A DevOps Starter irányítópultjának tetején válassza a **Build Pipelines (Folyamatok létrehozása) lehetőséget.** Egy böngészőlapon megjelenik az új projekt buildjének folyamat.

1. Mutasson az **Állapot mezőre,** majd válassza a három **pontot** (...). A menüben több lehetőség is megjelenik, például egy új build várólistára kerülése, egy build szüneteltetése vagy a build-folyamat szerkesztése.

1. Válassza a **Szerkesztés** elemet.

1. Ezen a panelen megvizsgálhatja a build-folyamat különböző feladatait. A build különböző feladatokat hajt végre, például lekéri a forrásokat a Git-adattárból, visszaállítja a függőségeket, és közzéteszi az üzemelő példányok kimeneteit.

1. A buildelési folyamat tetején válassza a buildelési folyamat nevét.

1. Módosítsa a build-folyamat nevét valami leíróbbra, válassza a **Save & queue**(Üzenetsor mentése) lehetőséget, majd kattintson a Save (Mentés) **gombra.**

1. A buildelési folyamat neve alatt válassza az **Előzményeket**. Ekkor megjelenik a build legutóbbi módosításainak naplója. Az Azure DevOps nyomon követi a build-folyamat módosításait, és lehetővé teszi a verziók összehasonlítását.

1. Válassza **az Eseményindítók lehetőséget.** A DevOps Starter automatikusan létrehoz egy CI-eseményindítót, és az adattár minden véglegesítése új buildet indít el. Lehetősége van belefoglalni az ágakat, vagy kizárni őket a CI-folyamatból.

1. Válassza a **Megtartást**. A forgatókönyvtől függően szabályzatokat is megadhat bizonyos számú build megtartáshoz vagy eltávolításához.

1. Válassza **a Build and Release (Build és kiadás)** lehetőséget, majd a **Releases (Kiadások) lehetőséget.**   
 DevOps Projects létrehoz egy kiadási folyamatot az Azure-ban üzemelő példányok kezeléséhez.

1. Válassza a kiadási folyamat melletti három pontokat, majd a **Szerkesztés lehetőséget.** A kiadási folyamat határozza meg a kiadási folyamatot.  
        
12. Az **Összetevők** alatt válassza az **Elvetést**. Az előző lépésekben megvizsgált build-folyamat az összetevőhez használt kimenetet állítja elő. 

1. A Drop **(Elvetés) ikon** mellett válassza a **Continuous deployment trigger (Folyamatos üzembe helyezés) eseményindítót.** A kiadási folyamat rendelkezik egy engedélyezett CD-eseményindítóval, amely minden alkalommal futtat egy üzembe helyezést, amikor új build-összetevő érhető el. Ha szeretné, letilthatja az eseményindítót, így az üzemelő példányok manuális végrehajtást igényelnek. 

1. A bal oldalon válassza a **Feladatok lehetőséget.** A feladatok az üzembe helyezési folyamat által végzett tevékenységek. Ebben a példában egy feladatot hoztunk létre a Azure App Service.

1. A jobb oldalon válassza a **Kiadások megtekintése lehetőséget** a kiadások előzményeinek megjelenítéséhez.  
        
1. Kattintson az egyik kiadás melletti három pontra (...), majd a **Megnyitás gombra.** Ebben a nézetben több menüt is megtekinthet, például a kiadás összegzését, a társított munkaelemeket és teszteket.

1. Válassza a **Véglegesítéseket**. Ez a nézet az adott üzemelő példányhoz társított kód véglegesítéseket jeleníti meg. 

1. Válassza a **Naplók lehetőséget.** A naplók hasznos információkat tartalmaznak az üzembehelyezési folyamattal kapcsolatban. Az üzembe helyezés során és után is megtekintheti őket.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Törölheti a Azure App Service és a kapcsolódó erőforrásokat, ha már nincs rájuk szüksége. Használja a **Törlés funkciót** a DevOps Starter irányítópultján.

## <a name="next-steps"></a>Következő lépések

A CI-/CD-folyamat konfigurálásakor a build- és kiadási folyamatok automatikusan létrejöttek. A csapat igényeihez igazodva módosíthatja ezt a buildet és a kiadási folyamatokat. További információ a CI/CD-folyamatról:

> [!div class="nextstepaction"]
> [CD-folyamat testreszabása](/azure/devops/pipelines/release/define-multistage-release-process)
