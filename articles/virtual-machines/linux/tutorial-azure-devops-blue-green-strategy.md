---
title: Oktatóanyag – az Azure Linux rendszerű virtuális gépekre vonatkozó Kanári-telepítések konfigurálása
description: Ebből az oktatóanyagból megtudhatja, hogyan állíthatja be a folyamatos üzembe helyezés (CD) folyamatát. Ez a folyamat az Azure Linux rendszerű virtuális gépek egy csoportját frissíti a kék-zöld telepítési stratégia használatával.
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
ms.openlocfilehash: 4545891cce926f049673cd2c2380a8309f2e71a1
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102552587"
---
# <a name="tutorial---configure-the-blue-green-deployment-strategy-for-azure-linux-virtual-machines"></a>Oktatóanyag – az Azure Linux rendszerű virtuális gépekhez készült Blue-Green üzembe helyezési stratégia konfigurálása

## <a name="infrastructure-as-a-service-iaas---configure-cicd"></a>Infrastruktúra-szolgáltatás (IaaS) – CI/CD konfigurálása

Az Azure-folyamatok teljes körű CI/CD Automation-eszközöket biztosítanak a virtuális gépekhez való üzembe helyezéshez. Az Azure-beli virtuális gépek folyamatos kézbesítési folyamatát a Azure Portal is konfigurálhatja.

Ez a cikk bemutatja, hogyan állíthat be egy olyan CI/CD-folyamatot, amely a MultiMachine-környezetekben a kék-zöld stratégiát használja. A Azure Portal más stratégiákat is támogat, mint például a [Rolling](./tutorial-devops-azure-pipelines-classic.md) és a [Canary](./tutorial-azure-devops-canary-strategy.md).

### <a name="configure-cicd-on-virtual-machines"></a>CI/CD konfigurálása virtuális gépeken

A virtuális gépeket célként adhatja hozzá egy [központi telepítési csoporthoz](/azure/devops/pipelines/release/deployment-groups). Ezután megcélozhatja őket a MultiMachine-frissítésekhez. A gépekre való központi telepítés után megtekintheti a telepítési **előzményeket** egy központi telepítési csoportban. Ez a nézet lehetővé teszi a virtuális gép és a folyamat közötti nyomkövetést, majd a véglegesítés elvégzését.

### <a name="blue-green-deployments"></a>Kék-zöld üzembe helyezések

A kék-zöld telepítés csökkenti az állásidőt egy azonos készenléti környezettel. Egyszerre csak egy környezet él.

Az új kiadásra való felkészülés során végezze el a tesztelés utolsó szakaszát a zöld környezetben. Miután a szoftver működik a zöld környezetben, váltson át a forgalomra, hogy minden bejövő kérelem a zöld környezetbe lépjen. A kék környezet inaktív.

A folyamatos kézbesítés beállítás használatával a Azure Portal a kék-zöld környezeteket a virtuális gépekre is konfigurálhatja. Itt látható a lépésenkénti útmutató:

1. Jelentkezzen be a Azure Portalba, és navigáljon a virtuális géphez.
1. A virtuális gép beállításainak bal szélső paneljén válassza a **folyamatos kézbesítés** lehetőséget. Ezután válassza a **Konfigurálás** lehetőséget.

   ![A folyamatos kézbesítés panel a configure (Konfigurálás) gombbal](media/tutorial-devops-azure-pipelines-classic/azure-devops-configure.png)

1. A konfiguráció panelen válassza az **Azure DevOps Organization** lehetőséget egy meglévő fiók kiválasztásához, vagy hozzon létre újat. Ezután válassza ki azt a projektet, amelyben be szeretné állítani a folyamatot.  

   ![A folyamatos kézbesítés panel](media/tutorial-devops-azure-pipelines-classic/azure-devops-rolling.png)

1. A központi telepítési csoport a központi telepítési célszámítógépek logikai készlete, amely a fizikai környezeteket jelképezi. Példák a fejlesztési, tesztelési, ellenőrzését és éles környezetekre. Létrehozhat egy új központi telepítési csoportot, vagy kijelölhet egy meglévőt is.
1. Válassza ki a Build folyamatot, amely közzéteszi a virtuális gépre telepítendő csomagot. A közzétett csomagnak rendelkeznie kell egy deploy.ps1 vagy deploy.sh nevű telepítési parancsfájllal a csomag gyökérkönyvtárában található deployscripts mappában. A folyamat futtatja ezt az üzembe helyezési parancsfájlt.
1. A **központi telepítési stratégia** területen válassza a **kék-zöld** lehetőséget.
1. Adjon hozzá egy "kék" vagy "zöld" címkét olyan virtuális gépekhez, amelyek a kék-zöld környezetek részét képezik. Ha egy virtuális gép készenléti szerepkörhöz kapcsolódik, a címke "zöld". Ellenkező esetben címkézse "Blue"-ként.

   ![A folyamatos kézbesítés panel, a központi telepítési stratégia értékének Blue-Green választása](media/tutorial-devops-azure-pipelines-classic/azure-devops-blue-green-configure.png)

1. Kattintson az **OK** gombra a folyamatos kézbesítési folyamat konfigurálásához a virtuális gépen való üzembe helyezéshez.

   ![A kék-zöld folyamat](media/tutorial-devops-azure-pipelines-classic/azure-devops-blue-green-pipeline.png)

1. Megjelenik a virtuális gép központi telepítési adatai. A hivatkozásra kattintva megtekintheti az Azure DevOps kiadási folyamatát. A folyamat konfigurációjának megtekintéséhez a kiadási folyamatban válassza a **Szerkesztés** lehetőséget. A folyamatnak a következő három fázisa van:

   1. Ez a fázis egy üzembe helyezési csoport fázisa. Az alkalmazások üzembe helyezése készenléti virtuális gépekre történik, amelyek "zöld" néven vannak megjelölve.
   1. Ebben a fázisban a folyamat szünetel, és megvárja a manuális beavatkozást a Futtatás folytatásához. A felhasználók akkor is folytathatják a folyamatot, ha manuálisan biztosítják a központi telepítés stabilitását a "zöld" címkével jelölt virtuális gépekre.
   1. Ez a fázis a virtuális gépek "kék" és "zöld" címkéit cseréli le. Ez biztosítja, hogy a régebbi verziójú alkalmazásokkal rendelkező virtuális gépek már "zöld" címkével legyenek megjelölve. A következő folyamat futtatásakor az alkalmazások a virtuális gépekre lesznek telepítve.

      ![A Blue-Green üzembe helyezése feladat központi telepítési csoport paneljén](media/tutorial-devops-azure-pipelines-classic/azure-devops-blue-green-tasks.png)

1. Az üzembe helyezési parancsfájl végrehajtása feladat alapértelmezés szerint futtatja az üzembe helyezési parancsfájlt deploy.ps1 vagy deploy.sh. A parancsfájl a közzétett csomag gyökérkönyvtárában lévő deployscripts mappában található. Győződjön meg arról, hogy a kiválasztott build-folyamat közzéteszi a központi telepítést a csomag gyökérkönyvtárában.

   ![A deployscripts mappában található deploy.sh mutató összetevők ablaktábla](media/tutorial-deployment-strategy/package.png)

## <a name="other-deployment-strategies"></a>Egyéb központi telepítési stratégiák

- [A működés közbeni üzembe helyezési stratégia konfigurálása](./tutorial-devops-azure-pipelines-classic.md)
- [A Kanári-telepítési stratégia konfigurálása](./tutorial-azure-devops-canary-strategy.md)

## <a name="azure-devops-projects"></a>Azure DevOps Projects

Az Azure-t egyszerűen megteheti. A Azure DevOps Projects használatával a következő parancs kiválasztásával megkezdheti az alkalmazás futtatását bármely Azure-szolgáltatásban mindössze három lépésben:

- Alkalmazás nyelve
- Egy futtatókörnyezet
- Azure-szolgáltatás

[További információ](https://azure.microsoft.com/features/devops-projects/).

## <a name="additional-resources"></a>További források

- [Üzembe helyezés az Azure-beli virtuális gépeken Azure DevOps Projects használatával](../../devops-project/azure-devops-project-vms.md)
- [Az alkalmazás folyamatos üzembe helyezésének megvalósítása egy Azure virtuálisgép-méretezési csoportba](/azure/devops/pipelines/apps/cd/azure/deploy-azure-scaleset)