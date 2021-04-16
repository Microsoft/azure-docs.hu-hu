---
title: 'Rövid útmutató: CI-/CD-folyamat létrehozása Javához – Azure DevOps Starter'
description: Megtudhatja, hogyan állíthat be egy folyamatos integrációs (CI) és folyamatos kézbesítési (CD) folyamatot Java-alkalmazáshoz az Azure Pipelinesban az Azure DevOps Starter használatával.
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
- seo-java-july2019
- seo-java-august2019
- seo-java-september2019
- devx-track-java
- mode-api
ms.openlocfilehash: 30ee97e0928f7b2995bd7bf02bec15bb8a46f7e2
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107535437"
---
# <a name="set-up-a-cicd-pipeline-for-a-java-app-with-azure-devops-starter"></a>CI-/CD-folyamat beállítása Java-alkalmazáshoz az Azure DevOps Starter használatával

Ebben a rövid útmutatóban az Azure DevOps Starter egyszerűsített használatával állíthat be egy folyamatos integrációs (CI) és folyamatos kézbesítési (CD) folyamatot Java-alkalmazáshoz az Azure Pipelinesban. Az Azure DevOps Starter használatával mindent beállíthat, amire szüksége lehet az alkalmazás fejlesztéséhez, üzembe helyezéséhez és monitorozáshoz. 

## <a name="prerequisites"></a>Előfeltételek

- Aktív előfizetéssel rendelkezik egy Azure-fiók. [Hozzon létre egy ingyenes fiókot.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) 
- [Egy Azure DevOps-fiók](https://azure.microsoft.com/services/devops/) és -szervezet.

## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra

A DevOps Starter létrehoz egy CI-/CD-folyamatot az Azure Pipelinesban. Létrehozhat egy új Azure DevOps-szervezetet, vagy használhat egy meglévőt. A DevOps Starter azure-erőforrásokat is létrehoz a választott Azure-előfizetésben.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

1. A keresőmezőbe írja be a **DevOps Starter**, majd a lehetőséget. Kattintson a **Hozzáadás gombra** egy új létrehozásához.

    ![A DevOps Starter irányítópultja](_img/azure-devops-starter-aks/search-devops-starter.png)

## <a name="select-a-sample-application-and-azure-service"></a>Mintaalkalmazás és Azure-szolgáltatás kiválasztása

1. Válassza a Java mintaalkalmazást. A Java-minták esetén több alkalmazási keretrendszer közül választhat.

1. Az alapértelmezett mintakeretrendszer a Spring. Hagyja meg az alapértelmezett beállítást, majd válassza a **Tovább lehetőséget.**  Az alapértelmezett üzembehelyezési cél a Web App for Containers. A korábban kiválasztott alkalmazás-keretrendszer határozza meg az Itt elérhető Azure-szolgáltatások üzembe helyezési céltípusát. 

2. Hagyja meg az alapértelmezett szolgáltatást, majd válassza a **Tovább lehetőséget.**
 
## <a name="configure-azure-devops-and-an-azure-subscription"></a>Az Azure DevOps és egy Azure-előfizetés konfigurálása 

1. Hozzon létre egy új Azure DevOps Services-szervezetet, vagy válasszon ki egy meglévő szervezetet. 
   
   1. Válasszon egy nevet a projektnek. 
   
   1. Válassza ki azure-előfizetését és helyét, válasszon nevet az alkalmazásnak, majd válassza a Kész **lehetőséget.**  
   Néhány perc múlva megjelenik a DevOps Starter irányítópultja a Azure Portal. A rendszer beállít egy mintaalkalmazást az Azure DevOps-szervezet adattárában, végrehajt egy buildet, és üzembe helyez egy alkalmazást az Azure-ban. Ez az irányítópult biztosítja a kódtár, a CI-/CD-folyamat és az Azure-beli alkalmazás láthatóságát.
   
2. Válassza **a Tallózás lehetőséget** a futó alkalmazás megtekintéséhez.
   
   ![Alkalmazás irányítópultjának megtekintése a Azure Portal](_img/azure-devops-project-java/azure-devops-application-dashboard.png) 

A DevOps Starter automatikusan konfigurált egy CI-buildet és egy kiadási eseményindítót.  Most már készen áll, hogy egy csapattal közösen dolgozzon a Java-alkalmazáson egy olyan CI-/CD-folyamat használatával, amely automatikusan üzembe helyezi a legújabb munkáját a webhelyen.

## <a name="commit-code-changes-and-execute-cicd"></a>Kódmódosítások véglegesítése és a CI/CD végrehajtása

A DevOps Starter létrehoz egy Git-adattárat az Azure Reposban vagy a GitHubon. Az adattár megtekintéséhez és az alkalmazás kódon való módosításaihoz tegye a következőket:

1. A DevOps Starter irányítópult bal oldalon válassza a főág hivatkozását. Ez a hivatkozás megnyitja az újonnan létrehozott Git-adattár nézetét.

1. Az adattár klón URL-címének megtekintéséhez válassza a **klónozás** lehetőséget a böngésző jobb felső sarokban. A Git-adattárat klónozhatja például a kedvenc IDE-környezetébe. A következő néhány lépésben a webböngésző használatával közvetlenül a főágban is végre lehet majd véglegesítést tenni a kódban.

1. A böngésző bal oldalán kattintson az **src/main/webapp/index.html fájlra.**

1. Válassza **a Szerkesztés** lehetőséget, majd módosítsa a szöveg egy része.
    Módosítsa például az egyik div címkén belüli szöveget.

1. Válassza **a Véglegesítés** lehetőséget, majd mentse a módosításokat.

1. A böngészőben menjen a DevOps Starter irányítópultra.   
Most már látnia kell egy buildet. Az előbb végrehajtott módosítások automatikusan létrejönnek és üzembe kerülnek egy CI-/CD-folyamaton keresztül.

## <a name="examine-the-cicd-pipeline"></a>A CI-/CD-folyamat vizsgálata

 Az előző lépésben a DevOps Starter automatikusan konfigurált egy teljes CI-/CD-folyamatot. Vizsgálja meg és szükség szerinti szabja testre a folyamatot. Az alábbi lépésekkel ismerkedhet meg a build- és kiadási folyamatokkal.

1. A DevOps Starter irányítópultjának tetején válassza a **Build Pipelines (Folyamatok létrehozása) lehetőséget.** Ez a hivatkozás megnyit egy böngészőlapot és az új projekt buildjének folyamatát.

1. Mutasson az **Állapot mezőre,** majd válassza a három pontot (...). Ez a művelet megnyit egy menüt, ahol több tevékenységet is elindíthat, például egy új build sorba állítását, szüneteltetheti a buildet, és szerkesztheti a build folyamatot.

1. Válassza a **Szerkesztés** elemet.

1. Ezen a panelen megvizsgálhatja a build-folyamat különböző feladatait. A build különböző feladatokat hajt végre, például forrásokat hív le a Git-adattárból, visszaállítja a függőségeket, és közzéteszi az üzembe helyezéshez használt kimeneteket.

1. A buildelési folyamat tetején válassza a buildelési folyamat nevét.

1. Módosítsa a build-folyamat nevét valami leíróbbra, válassza a **Save & queue**(Üzenetsor mentése) lehetőséget, majd kattintson a Save (Mentés) **gombra.**

1. A buildelési folyamat neve alatt válassza az **Előzményeket**.   
Az Előzmények **panelen** a build legutóbbi módosításainak naplózása látható.  Az Azure Pipelines nyomon követi a build-folyamaton végrehajtott módosításokat, és lehetővé teszi a verziók összehasonlítását.

1. Válassza **az Eseményindítók lehetőséget.**  A DevOps Starter automatikusan létrehozott egy CI-eseményindítót, és az adattárban való minden véglegesítés egy új buildet indít el.  Lehetősége van belefoglalni az ágakat, vagy kizárni őket a CI-folyamatból.

1. Válassza a **Megtartást**. A forgatókönyvtől függően szabályzatokat is megadhat bizonyos számú build megtartáshoz vagy eltávolításához.

1. Válassza **a Build and Release (Build és kiadás)** lehetőséget, majd a **Releases (Kiadások) lehetőséget.**  
 A DevOps Starter létrehoz egy kiadási folyamatot az Azure-ban üzemelő példányok kezeléséhez.

1. A bal oldalon válassza a kiadási folyamat melletti három pont (...), majd a Szerkesztés **lehetőséget.** A kiadási folyamat tartalmaz egy folyamatot, amely meghatározza a kiadási folyamatot.  
    
12. Az **Összetevők** alatt válassza az **Elvetést**. Az előző lépésekben megvizsgált build-folyamat az összetevőhez használt kimenetet állítja elő. 

1. A Drop **(Elvetés) ikon** mellett válassza a **Continuous deployment trigger (Folyamatos üzembe helyezés) eseményindítót.** Ez a kiadási folyamat egy engedélyezett CD-eseményindítóval rendelkezik, amely minden alkalommal futtat egy üzembe helyezést, amikor új build-összetevő érhető el. Ha szeretné, letilthatja az eseményindítót, így az üzemelő példányok manuális végrehajtást igényelnek. 

1. A bal oldalon válassza a **Feladatok lehetőséget.** A feladatok az üzembe helyezési folyamat által végzett tevékenységek. Ebben a példában egy feladatot hoztunk létre a Azure App Service.

1. A jobb oldalon válassza a **Kiadások megtekintése lehetőséget.** Ebben a nézetben a kiadások előzményei jelennek meg.

1. Kattintson az egyik kiadás melletti három pontra (...), majd a **Megnyitás gombra.** Több menüt is meg kell vizsgálnia, például a kiadás összegzését, a társított munkaelemeket és a teszteket.

1. Válassza a **Véglegesítéseket**. Ez a nézet az adott üzemelő példányhoz társított kód véglegesítéseket jeleníti meg. 

1. Válassza a **Naplók lehetőséget.** A naplók hasznos információkat tartalmaznak az üzembehelyezési folyamattal kapcsolatban. Ezek az üzembe helyezések alatt és után is megtekinthetők.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Törölheti a Azure App Service és más kapcsolódó erőforrásokat, ha már nincs rájuk szüksége. Használja **a Törlés funkciót** a DevOps Starter irányítópultján.

## <a name="next-steps"></a>Következő lépések

A CI-/CD-folyamat konfigurálásakor a build- és kiadási folyamatok automatikusan létrejöttek. A csapat igényeihez igazodva módosíthatja ezt a buildet és a kiadási folyamatokat. További információ a CI/CD-folyamatról:

> [!div class="nextstepaction"]
> [CD-folyamat testreszabása](/azure/devops/pipelines/release/define-multistage-release-process)
