---
title: A Jenkins és az Azure áttekintése
description: A Jenkins-buildelés üzemeltetésére és üzembe helyezése az Azure-ban automatizáló kiszolgáló, és a folyamatos integráció és készregyártás (CI/CD) folyamatok kiterjesztése az Azure számítási és tárolási erőforrások használatával.
ms.service: jenkins
keywords: jenkins, azure, devops, áttekintés
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.topic: overview
ms.date: 07/25/2018
ms.openlocfilehash: 86d32726280cce12888f125c65254a7b02166704
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60641245"
---
# <a name="azure-and-jenkins"></a>Az Azure és a Jenkins

A [Jenkins](https://jenkins.io/) egy népszerű, nyílt forráskódú automatizálási kiszolgáló, amely a szoftverprojektek folyamatos integrációs és teljesítési (CI-/CD-) folyamatainak beállítására használható. Üzembe helyezhet egy Jenkins üzemi környezetet az Azure-ban, vagy az Azure-erőforrások használatával kibővítheti meglévő Jenkins-konfigurációját. Az Azure-alkalmazások CI-/CD-folyamatainak egyszerűsítésére Jenkins beépülő modulok is használhatók.

Ez a cikk bemutatja az Azure Jenkinsszel való használatának alapjait, és részletesen ismerteti a Jenkins-felhasználók számára rendelkezésre álló főbb Azure-szolgáltatásokat. A saját Jenkins-kiszolgáló Azure-ban való használatának első lépéseit a [Jenkins-kiszolgáló Azure-ban való létrehozásával kapcsolatos](install-jenkins-solution-template.md) cikk ismerteti.

## <a name="host-your-jenkins-servers-in-azure"></a>Jenkins-kiszolgálók üzemeltetése az Azure-ban

A Jenkins Azure-ban való üzemeltetésével központosíthatja a buildek automatizálását, valamint a szoftverprojektek igényeinek megfelelően kibővítheti üzemi környezetét. A következő lehetőségei vannak a Jenkins Azure-ban való üzembe helyezésére:
 
- Az Azure Marketplace-en elérhető [Jenkins-megoldássablonnal](install-jenkins-solution-template.md)
- [Azure-beli virtuális gépekkel](/azure/virtual-machines/linux/overview) A Jenkins-példány virtuális gépen történő létrehozásának lépéseit lásd az [oktatóanyagban](/azure/virtual-machines/linux/tutorial-jenkins-github-docker-cicd).
- Az [Azure Container Service](/azure/container-service/kubernetes/container-service-kubernetes-walkthrough) szolgáltatásban futó Kubernetes-fürt esetében lásd a [használati útmutatót](/azure/container-service/kubernetes/container-service-kubernetes-jenkins).

Figyelése és felügyelete az Azure a Jenkins üzembe helyezési használatával [naplózza az Azure Monitor](/azure/log-analytics/log-analytics-overview) és a [Azure CLI-vel](/cli/azure).

## <a name="scale-your-build-automation-on-demand"></a>A buildautomatizálás igény szerinti skálázása

Buildügynökök hozzáadásával a buildek számának, illetve a feladatok és folyamatok összetettségének növekedéséhez igazodva skálázhatja a Jenkins-build kapacitását a meglévő Jenkins üzemi környezetben. Azure-beli virtuális gépeken a buildügynököket az [Azure-beli virtuálisgép-ügynökhöz tartozó beépülő modul](jenkins-azure-vm-agents.md) használatával futtathatja. További részleteket az [oktatóanyagban](/azure/jenkins/jenkins-azure-vm-agents) talál.

Az [Azure-szolgáltatásnévvel](/azure/azure-resource-manager/resource-group-overview) való konfigurálást követően a Jenkins-feladatok és -folyamatok a következőkre használhatják a hitelesítő adatokat:

- Buildösszetevők biztonságos tárolása és archiválása az [Azure Storage-ban](/azure/storage/common/storage-introduction) az [Azure Storage beépülő moduljának](https://plugins.jenkins.io/windows-azure-storage) használatával. A további részleteket a [Jenkins-tároló használati útmutatója](/azure/storage/common/storage-java-jenkins-continuous-integration-solution) tartalmazza.
- Azure-erőforrások kezelése és konfigurálása az [Azure CLI](/azure/jenkins/execute-cli-jenkins-pipeline) használatával.

## <a name="deploy-your-code-into-azure-services"></a>A kód üzembe helyezése Azure-szolgáltatásokban

A Jenkins beépülő moduljai használatával a Jenkins CI-/CD-folyamatok részeként helyezheti üzembe alkalmazásait az Azure-ban. Az [Azure App Service](/azure/app-service/) és [Azure Container Service](/azure/container-service/kubernetes/) szolgáltatásokban való üzembe helyezés révén az alapul szolgáló infrastruktúra kezelése nélkül készíthet elő, tesztelhet és adhat ki frissítéseket az alkalmazásaihoz.

 A beépülő modulokkal a következő szolgáltatások és környezetek helyezhetők üzembe:

- [Linuxon futó Azure App Service](/azure/app-service/containers/app-service-linux-intro). Az első lépésekhez kapcsolódó információkat az [oktatóanyag](java-deploy-webapp-tutorial.md) tartalmazza.
- [Az Azure App Service](/azure/app-service/overview). Az első lépésekhez kapcsolódó információkat a [használati útmutató](deploy-Jenkins-app-service-plugin.md) tartalmazza.