---
title: 'Gyors útmutató: CI/CD-folyamat létrehozása a Javához – Azure DevOps Starter'
description: Ismerje meg, hogyan hozhat létre folyamatos integrációs (CI) és folyamatos továbbítási (CD) folyamatot a Java-alkalmazásokhoz az Azure-folyamatokban az egyszerűsített Azure DevOps Starter használatával.
ms.prod: devops
ms.technology: devops-cicd
services: vsts
documentationcenter: vs-devops-build
author: mlearned
manager: gwallace
ms.workload: web
ms.tgt_pltfrm: na
ms.topic: quickstart
ms.date: 03/24/2020
ms.author: mlearned
ms.custom: mvc, seo-java-july2019, seo-java-august2019, seo-java-september2019, devx-track-java
ms.openlocfilehash: d76c4206ca39518c3a09d27ee470c05db108ae6d
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/10/2021
ms.locfileid: "102551881"
---
# <a name="set-up-a-cicd-pipeline-for-a-java-app-with-azure-devops-starter"></a>CI/CD-folyamat beállítása Java-alkalmazásokhoz az Azure DevOps Starter-vel

Ebben a rövid útmutatóban az egyszerűsített Azure DevOps Starter-élményt használja a Java-alkalmazások folyamatos integrációs (CI) és folyamatos kézbesítési (CD) folyamatának beállításához az Azure-folyamatokban. Az Azure DevOps Starter használatával beállíthatja az alkalmazás fejlesztéséhez, üzembe helyezéséhez és monitorozásához szükséges mindent. 

## <a name="prerequisites"></a>Előfeltételek

- Aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egy fiókot ingyenesen](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio). 
- Egy [Azure DevOps](https://azure.microsoft.com/services/devops/) -fiók és-szervezet.

## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra

A DevOps Starter egy CI/CD-folyamatot hoz létre az Azure-folyamatokban. Létrehozhat egy új Azure DevOps-szervezetet, vagy használhat egy meglévő céget is. A DevOps Starter Azure-erőforrásokat is létrehoz az Ön által választott Azure-előfizetésben.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

1. A keresőmezőbe írja be a **DevOps Starter** kifejezést, majd válassza a elemet. Kattintson a **Hozzáadás** gombra egy új létrehozásához.

    ![A DevOps Starter irányítópultja](_img/azure-devops-starter-aks/search-devops-starter.png)

## <a name="select-a-sample-application-and-azure-service"></a>Mintaalkalmazás és Azure-szolgáltatás kiválasztása

1. Válassza a Java mintaalkalmazást. A Java-minták esetén több alkalmazási keretrendszer közül választhat.

1. Az alapértelmezett mintakeretrendszer a Spring. Hagyja meg az alapértelmezett beállítást, majd kattintson a **tovább** gombra.  Az alapértelmezett üzembehelyezési cél a Web App for Containers. Az alkalmazás-keretrendszer, amelyet korábban választott, az itt elérhető Azure-szolgáltatás telepítési célhelyét diktálja. 

2. Hagyja meg az alapértelmezett szolgáltatást, majd válassza a **tovább** lehetőséget.
 
## <a name="configure-azure-devops-and-an-azure-subscription"></a>Azure-DevOps és Azure-előfizetés konfigurálása 

1. Hozzon létre egy új Azure DevOps Services-szervezetet, vagy válasszon ki egy meglévő szervezetet. 
   
   1. Válasszon egy nevet a projektnek. 
   
   1. Válassza ki az Azure-előfizetést és-helyet, válasszon egy nevet az alkalmazásnak, majd válassza a **kész** lehetőséget.  
   Néhány perc elteltével a DevOps Starter irányítópultja megjelenik a Azure Portal. Egy minta alkalmazás az Azure DevOps-szervezet egy tárházában van beállítva, a rendszer létrehoz egy buildet, és az alkalmazást üzembe helyezi az Azure-ban. Ez az irányítópult a Code adattárba, a CI/CD-folyamatba és az Azure-beli alkalmazásba is betekintést nyújt.
   
2. A futó alkalmazás megtekintéséhez válassza a **Tallózás** lehetőséget.
   
   ![Alkalmazás-irányítópult megtekintése Azure Portal](_img/azure-devops-project-java/azure-devops-application-dashboard.png) 

A DevOps Starter automatikusan konfigurált egy CI Build-és kiadási triggert.  Most már készen áll, hogy egy csapattal közösen dolgozzon a Java-alkalmazáson egy olyan CI-/CD-folyamat használatával, amely automatikusan üzembe helyezi a legújabb munkáját a webhelyen.

## <a name="commit-code-changes-and-execute-cicd"></a>Kódmódosítások véglegesítése és a CI/CD végrehajtása

A DevOps Starter létrehoz egy git-tárházat az Azure Reposben vagy a GitHubban. Az adattár megtekintéséhez és a kód módosításának végrehajtásához tegye a következőket:

1. A DevOps Starter irányítópult bal oldalán válassza ki a fő ág hivatkozását. Ez a hivatkozás megnyitja az újonnan létrehozott Git-adattár nézetét.

1. A tárház klónozási URL-címének megtekintéséhez válassza a böngésző jobb felső sarkában található **klónozás** elemet. A Git-adattárat klónozhatja például a kedvenc IDE-környezetébe. A következő néhány lépésben a webböngészővel teheti meg és véglegesítheti a kód módosításait közvetlenül a fő ágra.

1. A böngésző bal oldalán lépjen a **src/Main/WebApp/index.html** fájlra.

1. Válassza a **Szerkesztés** lehetőséget, majd végezze el a módosítást egy bizonyos szövegre.
    Módosítsa például az egyik div címkén belüli szöveget.

1. Válassza **a** végrehajtás lehetőséget, majd mentse a módosításokat.

1. A böngészőben nyissa meg a DevOps Starter irányítópultját.   
Ekkor megjelenik egy Build folyamatban. Az imént létrehozott módosításokat a rendszer automatikusan felépíti és telepíti a CI/CD-folyamaton keresztül.

## <a name="examine-the-cicd-pipeline"></a>A CI/CD-folyamat vizsgálata

 Az előző lépésben a DevOps Starter automatikusan konfigurált egy teljes CI/CD-folyamatot. Vizsgálja meg és szükség szerinti szabja testre a folyamatot. Az alábbi lépéseket követve Ismerkedjen meg a létrehozási és a kiadási folyamatokkal.

1. A DevOps alapszintű irányítópultjának tetején válassza a **folyamatok létrehozása** lehetőséget. Ez a hivatkozás egy böngésző fület és az új projekthez tartozó Build folyamatot nyitja meg.

1. Mutasson az **állapot** mezőre, majd válassza a három pontot (...). Ez a művelet egy menüt nyit meg, ahol számos tevékenységet indíthat el, például egy új Build kiépítését, egy Build szüneteltetését és a build folyamat szerkesztését.

1. Válassza a **Szerkesztés** elemet.

1. Ezen az ablaktáblán ellenőrizheti a felépítési folyamat különféle feladatait. A Build számos feladatot hajt végre, például a git-tárházból beolvassa a forrásokat, visszaállítja a függőségeket, és közzéteszi az üzembe helyezéshez használt kimeneteket.

1. A buildelési folyamat tetején válassza a buildelési folyamat nevét.

1. Módosítsa a felépítési folyamat nevét egy ennél több leíróra, válassza a **mentés & üzenetsor** lehetőséget, majd kattintson a **Mentés** gombra.

1. A buildelési folyamat neve alatt válassza az **Előzményeket**.   
Az **Előzmények** ablaktáblán megjelenik a Build legutóbbi változásainak naplózási nyomvonala.  Az Azure-folyamatok nyomon követik a felépítési folyamaton végrehajtott módosításokat, és lehetővé teszik a verziók összehasonlítását.

1. Válassza az **Eseményindítók** lehetőséget.  A DevOps Starter automatikusan létrehozta a CI-triggert, és az adattárba való minden kötelezettség új buildet indít el.  Lehetősége van belefoglalni az ágakat, vagy kizárni őket a CI-folyamatból.

1. Válassza a **Megtartást**. A forgatókönyvtől függően megadhat olyan házirendeket, amelyek bizonyos számú buildet megtartanak vagy eltávolítanak.

1. Válassza a **Létrehozás és kiadás**, majd a **kiadások** lehetőséget.  
 A DevOps Starter egy kiadási folyamatot hoz létre az Azure-ban üzemelő példányok kezeléséhez.

1. A bal oldalon válassza a kiadási folyamat melletti három pontot (...), majd válassza a **Szerkesztés** lehetőséget. A kiadási folyamat tartalmaz egy folyamatot, amely meghatározza a kiadási folyamatot.  
    
12. Az **Összetevők** alatt válassza az **Elvetést**. Az előző lépések során megvizsgált összeállítási folyamat létrehozza az összetevőhöz használt kimenetet. 

1. A **drop** ikon mellett válassza ki a **folyamatos üzembe helyezési triggert**. Ez a kiadási folyamat egy engedélyezett CD-triggerrel rendelkezik, amely minden alkalommal futtat egy központi telepítést, amikor új Build-összetevő érhető el. Igény szerint letilthatja az indítást, hogy a központi telepítések manuális végrehajtást igényeljenek. 

1. A bal oldalon válassza a **feladatok** lehetőséget. A feladatok azok a tevékenységek, amelyeket a telepítési folyamat végrehajt. Ebben a példában egy feladatot hoztak létre a Azure App Serviceba való üzembe helyezéshez.

1. A jobb oldalon válassza a **kiadások megtekintése** lehetőséget. Ebben a nézetben a kiadások előzményei jelennek meg.

1. Válassza ki az egyik kiadás melletti három pontot (...), majd kattintson a **Megnyitás** gombra. Több menüt is megvizsgálhat, például a kiadás összegzését, a kapcsolódó munkaelemeket és teszteket.

1. Válassza a **Véglegesítéseket**. Ez a nézet az adott központi telepítéshez társított kód-véglegesítő kódokat jeleníti meg. 

1. Válassza a **naplók** lehetőséget. A naplók hasznos információkat tartalmaznak az üzembehelyezési folyamattal kapcsolatban. Ezek az üzembe helyezések alatt és után is megtekinthetők.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szüksége rájuk, törölheti Azure App Service és az egyéb kapcsolódó erőforrásokat is. Használja a **delete** funkciót a DevOps Starter irányítópultján.

## <a name="next-steps"></a>Következő lépések

A CI/CD-folyamat beállításakor a rendszer automatikusan létrehozta a létrehozási és kiadási folyamatokat. A csapat igényeihez igazodva módosíthatja ezt a buildet és a kiadási folyamatokat. A CI/CD folyamattal kapcsolatos további tudnivalókért tekintse meg a következőt:

> [!div class="nextstepaction"]
> [CD-folyamat testreszabása](/azure/devops/pipelines/release/define-multistage-release-process)