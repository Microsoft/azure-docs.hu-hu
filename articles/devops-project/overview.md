---
title: Az Azure-hoz készült DevOps Starter áttekintése | Microsoft Docs
description: A DevOps Starter értékének megismerése
services: devops-project
documentationcenter: ''
author: mlearned
manager: gwallace
editor: mlearned
ms.assetid: ''
ms.service: az-devops-project
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: ''
ms.date: 03/24/2020
ms.author: mlearned
ms.openlocfilehash: 7c36539ef5be503b2cb7e14047e596522ef8e962
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "92330648"
---
# <a name="overview-of-devops-starter"></a>A DevOps Starter áttekintése

 A DevOps Starter megkönnyíti az Azure használatának első lépéseit a GitHub-műveletek vagy az Azure DevOps használatával. A Azure Portalból néhány gyors lépéssel elindíthatja kedvenc alkalmazásait az Ön által választott Azure-szolgáltatásban. 

 A DevOps Starter minden, amire szüksége lehet az alkalmazás fejlesztéséhez, üzembe helyezéséhez és figyeléséhez. A DevOps alapszintű irányítópultján megfigyelheti a kód véglegesítő, épít és üzemelő példányait a Azure Portal egyetlen nézetében.

## <a name="advantages-of-using-devops-starter"></a>A DevOps Starter használatának előnyei

  A DevOps Starter the következő két CI/CD-szolgáltatót támogat az üzemelő példányok automatizálásához
  * [GitHub Actions](https://github.com/features/actions)
  * [Azure DevOps](https://azure.microsoft.com/services/devops)

  A DevOps Starter automatizálja a teljes folyamatos integráció (CI) és a folyamatos kézbesítés (CD) beállítását az Azure-ban.  Megkezdheti a meglévő kódok használatát, vagy használhatja a megadott minta alkalmazások egyikét is. Ezután gyorsan üzembe helyezheti az alkalmazást különböző Azure-szolgáltatásokra, például a Virtual Machinesre, a App Servicera, az Azure Kubernetes Services (ak), a Azure SQL Database és az Azure Service Fabricra.  

  A DevOps Starter minden olyan munkát tartalmaz, amely a DevOps-folyamat kezdeti konfigurálásához szükséges, beleértve a kezdeti git-tárház beállítását, a CI/CD-folyamat konfigurálását, a figyeléshez Application Insights erőforrás létrehozását, valamint a teljes megoldás egyetlen nézetét, amely egy DevOps Starter-irányítópultot hoz létre a Azure Portal.

A DevOps Starter használatával a következőket végezheti el:

* Az alkalmazások gyors üzembe helyezése az Azure-ban
* A CI/CD-munkafolyamatok vagy-folyamatok telepítésének automatizálása
* A CI/CD-munkafolyamatok és-folyamatok megfelelő beállításának megtekintése és ismertetése
* A kiadási folyamatok további testreszabása az adott forgatókönyvek alapján

## <a name="how-to-use-devops-starter"></a>Az DevOps Starter használata

  A DevOps Starter a Azure Portal érhető el. A DevOps kezdő erőforrásait ugyanúgy hozza létre, mint bármely más Azure-erőforrást a portálon. DevOps Projects a különböző konfigurációs beállításokhoz nyújt lépésenkénti varázslót.  

A kezdeti beállítás során számos konfigurálási lehetőség közül választhat. Ezek a lehetőségek a következők:

* Válassza ki a megfelelő CI/CD-szolgáltatót
* A megadott minta alkalmazás használata vagy saját kód (csak az Azure DevOps esetében)
* Alkalmazás nyelvének kiválasztása
* Alkalmazás-keretrendszer kiválasztása nyelv alapján
* Azure-szolgáltatás kiválasztása (telepítési cél)
* Válassza ki a GitHub vagy az Azure DevOps-szervezetét
* Az Azure-előfizetés kiválasztása
* Az Azure-szolgáltatások helyének kiválasztása
* Az Azure-szolgáltatások különböző díjszabási szintjei közül választhat

A DevOps kezdő létrehozása után a következőket teheti:

* A GitHub-munkafolyamat vagy az Azure DevOps folyamat testreszabása
* Lekérési kérelmek használata a kódfolyamat kezeléséhez és a minőség magas szinten tartásához
* Minden véglegesítés tesztelése és felépítése a kódok egyesítése előtt a minőség javítása érdekében

A DevOps Starter használata után törölheti is az összes erőforrást egyetlen helyről a Azure Portal Starter irányítópultján.

## <a name="devops-starter-and-github-integration"></a>DevOps Starter és GitHub-integráció

A DevOps Starter mostantól támogatja a GitHub-műveleteket CI/CD-szolgáltatóként. Automatizálja az összes olyan munkát, amelyet a GitHubon a GitHub-műveletek használatával a CI/CD-munkafolyamatok létrehozásához szükséges. Egy GitHub-tárházat hoz létre egy meglévő GitHub-szervezeten belül, majd egy minta alkalmazást véglegesít az új GitHub-tárházba.  

Az Automation létrehoz egy triggert is a munkafolyamathoz, hogy minden új kód véglegesítse a munkafolyamaton belüli Build és Deploy feladatot. Az alkalmazás üzembe helyezése az Ön által választott Azure-szolgáltatáson történik. A GitHub-munkafolyamat további forgatókönyvekhez is testreszabható. 

## <a name="devops-starter-and-azure-devops-integration"></a>A DevOps Starter és az Azure DevOps integrációja

Az Azure DevOps-t használó DevOps automatizálja az Azure-folyamatokban a CI/CD-folyamatok beállításához szükséges összes munkát. Git-tárházat hoz létre egy új vagy meglévő Azure DevOps-szervezetben, majd egy minta alkalmazást vagy a meglévő kódot egy új git-tárházba véglegesíti.  

Az Automation egy CI-triggert is létrehoz a buildhez, hogy minden új kód véglegesítse a buildet. A DevOps Starter létrehoz egy CD-triggert, és minden új sikeres buildet üzembe helyez a választott Azure-szolgáltatásban.  

A build és a kiadási folyamatok a további forgatókönyveknek megfelelően testre szabhatók. Emellett a fordítási és a kiadási folyamatokat is klónozott más projektekben való használatra.

## <a name="getting-started-with-devops-starter"></a>Első lépések a DevOps Starter-vel

* [Ismerkedés a DevOps Starter-szel](./azure-devops-project-github.md)

##  <a name="devops-starter-videos"></a>DevOps Starter-videók

* [CI/CD létrehozása az Azure DevOps Starter-vel](https://www.youtube.com/watch?v=NuYDAs3kNV8)
