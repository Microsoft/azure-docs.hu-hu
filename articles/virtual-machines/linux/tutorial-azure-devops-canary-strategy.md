---
title: Oktatóanyag – az Azure Linux Virtual Machines Kanári-telepítések konfigurálása
description: Ebből az oktatóanyagból megtudhatja, hogyan állíthatja be a folyamatos üzembe helyezés (CD) folyamatát. Ez a folyamat az Azure Linux rendszerű virtuális gépek egy csoportját frissíti a Kanári-telepítési stratégia használatával.
author: moala
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
ms.openlocfilehash: bbfe6571cf075b2ce4930eea91bfd1e239470c5a
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/10/2021
ms.locfileid: "102552507"
---
# <a name="tutorial---configure-the-canary-deployment-strategy-for-azure-linux-virtual-machines"></a>Oktatóanyag – a Kanári üzembe helyezési stratégia konfigurálása az Azure Linux Virtual Machines

## <a name="infrastructure-as-a-service-iaas---configure-cicd"></a>Infrastruktúra-szolgáltatás (IaaS) – CI/CD konfigurálása

Az Azure-folyamatok teljes körű CI/CD Automation-eszközöket biztosítanak a virtuális gépekhez való üzembe helyezéshez. Az Azure-beli virtuális gépek folyamatos kézbesítési folyamatát a Azure Portal is konfigurálhatja.

Ez a cikk bemutatja, hogyan állíthat be egy olyan CI/CD-folyamatot, amely a MultiMachine-alapú üzembe helyezésre vonatkozó Kanári-stratégiát használja. A Azure Portal más olyan stratégiákat is támogat, mint például a [Rolling](./tutorial-devops-azure-pipelines-classic.md) és a [Blue-Green](./tutorial-azure-devops-blue-green-strategy.md).

### <a name="configure-cicd-on-virtual-machines"></a>CI/CD konfigurálása virtuális gépeken

A virtuális gépeket célként adhatja hozzá egy [központi telepítési csoporthoz](/azure/devops/pipelines/release/deployment-groups). Ezután megcélozhatja őket a MultiMachine-frissítésekhez. A gépekre való központi telepítés után megtekintheti a telepítési **előzményeket** egy központi telepítési csoportban. Ez a nézet lehetővé teszi a virtuális gép és a folyamat közötti nyomkövetést, majd a véglegesítés elvégzését.

### <a name="canary-deployments"></a>Kanári-telepítések

A Kanári-környezetek csökkentik a kockázatot a felhasználók kis részhalmazának változásainak lassan történő kiépítésével. Az új verzió megbízhatóságának megszerzése után kiszabadíthatja azt az infrastruktúra több kiszolgálójára, és több felhasználót is átirányíthat.

A folyamatos kézbesítés lehetőséggel a Azure Portal a Kanári-telepítéseket a virtuális gépekre is konfigurálhatja. Itt látható a lépésenkénti útmutató:

1. Jelentkezzen be a Azure Portalba, és navigáljon a virtuális géphez.
1. A virtuális gép beállításainak bal szélső paneljén válassza a **folyamatos kézbesítés** lehetőséget. Ezután válassza a **Konfigurálás** lehetőséget.

   ![A folyamatos kézbesítés panel a configure (Konfigurálás) gombbal](media/tutorial-devops-azure-pipelines-classic/azure-devops-configure.png)

1. A konfiguráció panelen válassza az **Azure DevOps Organization** lehetőséget egy meglévő fiók kiválasztásához, vagy hozzon létre újat. Ezután válassza ki azt a projektet, amelyben be szeretné állítani a folyamatot.  

   ![A folyamatos kézbesítés panel](media/tutorial-devops-azure-pipelines-classic/azure-devops-rolling.png)

1. A központi telepítési csoport a központi telepítési célszámítógépek logikai készlete, amely a fizikai környezeteket jelképezi. Példák a fejlesztési, tesztelési, ellenőrzését és éles környezetekre. Létrehozhat egy új központi telepítési csoportot, vagy kijelölhet egy meglévőt is.
1. Válassza ki a Build folyamatot, amely közzéteszi a virtuális gépre telepítendő csomagot. A közzétett csomagnak rendelkeznie kell egy deploy.ps1 vagy deploy.sh nevű telepítési parancsfájllal a csomag gyökérkönyvtárában található deployscripts mappában. A folyamat futtatja ezt az üzembe helyezési parancsfájlt.
1. A **központi telepítési stratégia** területen válassza a **Kanári** lehetőséget.
1. Vegyen fel egy "Kanári" címkét a Kanári-környezetek részét képező virtuális gépekhez. Adjon hozzá egy "Prod" címkét a virtuális gépekhez, amelyek a Kanári üzembe helyezése után végrehajtott központi telepítések részét képezik. A címkék segítségével csak az adott szerepkörrel rendelkező virtuális gépeket célozza meg.

   ![A folyamatos kézbesítési panel, a központi telepítési stratégia értékének kiválasztása](media/tutorial-devops-azure-pipelines-classic/azure-devops-configure-canary.png)

1. Kattintson az **OK** gombra a folyamatos kézbesítési folyamat konfigurálásához a virtuális gépen való üzembe helyezéshez.

   ![A Kanári-folyamat](media/tutorial-devops-azure-pipelines-classic/azure-devops-canary-pipeline.png)

1. Megjelenik a virtuális gép központi telepítési adatai. A hivatkozásra kattintva megtekintheti az Azure DevOps kiadási folyamatát. A folyamat konfigurációjának megtekintéséhez a kiadási folyamatban válassza a **Szerkesztés** lehetőséget. A folyamatnak a következő három fázisa van:

   1. Ez a fázis egy üzembe helyezési csoport fázisa. Az alkalmazások a "Kanári" címkével rendelkező virtuális gépekre vannak telepítve.
   1. Ebben a fázisban a folyamat szünetel, és megvárja a manuális beavatkozást a Futtatás folytatásához.
   1. Ez egy üzembe helyezési csoport fázisa. A frissítés már telepítve van a "Prod" címkével ellátott virtuális gépekre.

      ![A Kanári üzembe helyezése feladat központi telepítési csoport paneljén](media/tutorial-devops-azure-pipelines-classic/azure-devops-canary-task.png)

1. A folyamat futásának folytatása előtt győződjön meg arról, hogy legalább egy virtuális gép "Prod" címkével van megjelölve. A folyamat harmadik fázisában az alkalmazások csak azokra a virtuális gépekre lesznek telepítve, amelyek rendelkeznek a "Prod" címkével.

1. Az üzembe helyezési parancsfájl végrehajtása feladat alapértelmezés szerint futtatja az üzembe helyezési parancsfájlt deploy.ps1 vagy deploy.sh. A parancsfájl a közzétett csomag gyökérkönyvtárában lévő deployscripts mappában található. Győződjön meg arról, hogy a kiválasztott build-folyamat közzéteszi a központi telepítést a csomag gyökérkönyvtárában.

   ![A deployscripts mappában található deploy.sh mutató összetevők ablaktábla](media/tutorial-deployment-strategy/package.png)

## <a name="other-deployment-strategies"></a>Egyéb központi telepítési stratégiák
- [A működés közbeni üzembe helyezési stratégia konfigurálása](./tutorial-devops-azure-pipelines-classic.md)
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