---
title: Oktatóanyag – Azure Linux rendszerű virtuális gépek működés közbeni üzembe helyezésének konfigurálása
description: Ebből az oktatóanyagból megtudhatja, hogyan állíthatja be a folyamatos üzembe helyezés (CD) folyamatát. Ez a folyamat fokozatosan frissíti az Azure Linux rendszerű virtuális gépek egy csoportját a működés közbeni üzembe helyezési stratégia használatával.
author: moala
manager: jpconnock
tags: azure-devops-pipelines
ms.assetid: ''
ms.service: virtual-machines
ms.collection: linux
ms.topic: tutorial
ms.tgt_pltfrm: azure-pipelines
ms.workload: infrastructure
ms.date: 4/10/2020
ms.author: moala
ms.custom: devops
ms.openlocfilehash: dd47250989be5c31d5f0ade2b602b9d6af535d83
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/10/2021
ms.locfileid: "102563999"
---
# <a name="tutorial---configure-the-rolling-deployment-strategy-for-azure-linux-virtual-machines"></a>Oktatóanyag – az Azure Linux rendszerű virtuális gépek működés közbeni üzembe helyezési stratégiájának konfigurálása

Az Azure DevOps egy beépített Azure-szolgáltatás, amely minden Azure-erőforráshoz automatizálja a DevOps folyamat egyes részeit. Akár virtuális gépeket, webalkalmazásokat, Kubernetes vagy bármilyen más erőforrást használ, az infrastruktúrát kódként (IaC), folyamatos integrációt, folyamatos tesztelést, folyamatos szállítást és folyamatos monitorozást hajthat végre az Azure és az Azure DevOps használatával.

![A szolgáltatások területen kiválasztott Azure DevOps Azure Portal](media/tutorial-devops-azure-pipelines-classic/azdevops-view.png)

## <a name="infrastructure-as-a-service-iaas---configure-cicd"></a>Infrastruktúra-szolgáltatás (IaaS) – CI/CD konfigurálása

Az Azure-folyamatok teljes körű CI/CD Automation-eszközöket biztosítanak a virtuális gépekhez való üzembe helyezéshez. Az Azure-beli virtuális gépek folyamatos kézbesítési folyamatát a Azure Portal is konfigurálhatja.

Ez a cikk bemutatja, hogyan állíthat be egy CI/CD-folyamatot a Azure Portal MultiMachine történő üzembe helyezéséhez. A Azure Portal más stratégiákat is támogat, mint például a [Kanári](./tutorial-azure-devops-canary-strategy.md) és a [kék-zöld](./tutorial-azure-devops-blue-green-strategy.md).

### <a name="configure-cicd-on-virtual-machines"></a>CI/CD konfigurálása virtuális gépeken

A virtuális gépeket célként adhatja hozzá egy [központi telepítési csoporthoz](/azure/devops/pipelines/release/deployment-groups). Ezután megcélozhatja őket a MultiMachine-frissítésekhez. A gépekre való központi telepítés után megtekintheti a telepítési **előzményeket** egy központi telepítési csoportban. Ez a nézet lehetővé teszi a virtuális gép és a folyamat közötti nyomkövetést, majd a véglegesítés elvégzését.

### <a name="rolling-deployments"></a>Működés közbeni üzembe helyezések

Az egyes iterációkban a működés közbeni központi telepítés az alkalmazás korábbi verziójának példányait váltja fel. Lecseréli azokat az új verzió példányaira egy rögzített gépen (gördülő készlet). A következő lépés bemutatja, hogyan konfigurálhatja a működés közbeni frissítést a virtuális gépekre.

A folyamatos kézbesítés beállítás használatával a Azure Portalon belül konfigurálhatja a virtuális gépek működés közbeni frissítéseit. Itt látható a lépésenkénti útmutató:

1. Jelentkezzen be a Azure Portalba, és navigáljon a virtuális géphez.
1. A virtuális gép beállításainak bal szélső paneljén válassza a **folyamatos kézbesítés** lehetőséget. Ezután válassza a **Konfigurálás** lehetőséget.

   ![A folyamatos kézbesítés panel a configure (Konfigurálás) gombbal](media/tutorial-devops-azure-pipelines-classic/azure-devops-configure.png)

1. A konfiguráció panelen válassza az **Azure DevOps Organization** lehetőséget egy meglévő fiók kiválasztásához, vagy hozzon létre újat. Ezután válassza ki azt a projektet, amelyben be szeretné állítani a folyamatot.  

   ![A folyamatos kézbesítés panel](media/tutorial-devops-azure-pipelines-classic/azure-devops-rolling.png)

1. A központi telepítési csoport a központi telepítési célszámítógépek logikai készlete, amely a fizikai környezeteket jelképezi. Példák a fejlesztési, tesztelési, ellenőrzését és éles környezetekre. Létrehozhat egy új központi telepítési csoportot, vagy kijelölhet egy meglévőt is.
1. Válassza ki a Build folyamatot, amely közzéteszi a virtuális gépre telepítendő csomagot. A közzétett csomagnak rendelkeznie kell egy deploy.ps1 vagy deploy.sh nevű telepítési parancsfájllal a csomag gyökérkönyvtárában található deployscripts mappában. A folyamat futtatja ezt az üzembe helyezési parancsfájlt.
1. A **központi telepítési stratégia** területen válassza a **gördülő** lehetőséget.
1. Szükség esetén az egyes gépeket felcímkézheti a szerepkörével. A "web" és az "db" címke példákat tartalmaz. Ezek a címkék segítenek kizárólag adott szerepkörrel rendelkező virtuális gépek célzásában.
1. A folyamatos kézbesítési folyamat konfigurálásához kattintson **az OK gombra** .
1. A konfigurálás befejeződése után folyamatos kézbesítési folyamattal rendelkezik, amely a virtuális gépre való üzembe helyezésre van konfigurálva.  

   ![Az üzembe helyezési előzményeket bemutató folyamatos kézbesítési panel](media/tutorial-devops-azure-pipelines-classic/azure-devops-deployment-history.png)

1. Megjelenik a virtuális gép központi telepítési adatai. Kiválaszthatja a folyamatot a folyamathoz, a **Release-1** elemre kattintva megtekintheti az üzemelő példányt, vagy **szerkesztheti** a kiadási folyamat definíciójának módosítását.

1. Ha több virtuális gépet konfigurál, ismételje meg a 2 – 4. lépést az üzembe helyezési csoportba felvenni kívánt egyéb virtuális gépek esetében. Ha olyan központi telepítési csoportot választ, amely már rendelkezik egy folyamat futtatásával, a rendszer csak a virtuális gépeket adja hozzá a központi telepítési csoporthoz. Nem jön létre új folyamat.
1. Miután végzett a konfigurálással, válassza ki a folyamat definícióját, navigáljon az Azure DevOps-szervezethez, és válassza a **Szerkesztés** lehetőséget a kiadási folyamathoz.

   ![A működés közbeni folyamat szerkesztése](media/tutorial-devops-azure-pipelines-classic/azure-devops-rolling-pipeline.png)

1. Válasszon **1 feladatot, 1 feladatot** a **fejlesztői** fázisban. Válassza ki az **üzembe helyezési** fázist.

   ![Működés közbeni feldolgozási feladatok a kiválasztott üzembe helyezési feladattal](media/tutorial-devops-azure-pipelines-classic/azure-devops-rolling-pipeline-tasks.png)

1. A jobb oldali konfiguráció panelen megadhatja, hogy az egyes iterációkban mely gépeket szeretné párhuzamosan telepíteni. Ha egyszerre több gépre szeretne üzembe helyezni, a csúszka használatával megadhatja a számítógépek számát százalékosan.  

1. Az üzembe helyezési parancsfájl végrehajtása feladat alapértelmezés szerint végrehajtja az üzembe helyezési parancsfájlt deploy.ps1 vagy deploy.sh. A parancsfájl a közzétett csomag gyökérkönyvtárában lévő deployscripts mappában található.

   ![A deployscripts mappában található deploy.sh mutató összetevők ablaktábla](media/tutorial-deployment-strategy/package.png)

## <a name="other-deployment-strategies"></a>Egyéb központi telepítési stratégiák

- [A Kanári-telepítési stratégia konfigurálása](./tutorial-azure-devops-canary-strategy.md)
- [A kék-zöld telepítési stratégia konfigurálása](./tutorial-azure-devops-blue-green-strategy.md)

## <a name="azure-devops-projects"></a>Azure DevOps Projects

Az Azure-t egyszerűen megteheti. A Azure DevOps Projects használatával a következő parancs kiválasztásával megkezdheti az alkalmazás futtatását bármely Azure-szolgáltatásban mindössze három lépésben:

- Alkalmazás nyelve
- Egy futtatókörnyezet
- Azure-szolgáltatás
 
[További információk](https://azure.microsoft.com/features/devops-projects/).
 
## <a name="additional-resources"></a>További források

- [Üzembe helyezés az Azure-beli virtuális gépeken Azure DevOps Projects használatával](../../devops-project/azure-devops-project-vms.md)
- [Az alkalmazás folyamatos üzembe helyezésének megvalósítása egy Azure virtuálisgép-méretezési csoportba](/azure/devops/pipelines/apps/cd/azure/deploy-azure-scaleset)