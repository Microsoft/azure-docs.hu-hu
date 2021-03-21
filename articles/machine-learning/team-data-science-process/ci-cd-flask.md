---
title: CI/CD-folyamat létrehozása Azure-adatcsatornákkal – csoportos adatelemzési folyamat
description: Hozzon létre folyamatos integrációs és folyamatos átviteli folyamatot a mesterséges intelligencia (AI) alkalmazások számára a Docker és a Kubernetes használatával.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=jainr, previous-ms.author=jainr
ms.openlocfilehash: af7900faff18d526686b80f23b9de3b3bce5ec5c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "93309610"
---
# <a name="create-cicd-pipelines-for-ai-apps-using-azure-pipelines-docker-and-kubernetes"></a>CI/CD-folyamatok létrehozása AI-alkalmazásokhoz az Azure-folyamatok, a Docker és a Kubernetes használatával

A mesterséges intelligencia (AI) alkalmazás olyan alkalmazási kód, amely egy előképzéssel ellátott Machine learning (ML) modellel van beágyazva. Egy AI-alkalmazásnak mindig két streamje van: az adatszakértők kiépítik a ML modellt, az alkalmazások fejlesztői pedig felépítik az alkalmazást, és teszik elérhetővé a végfelhasználók számára. Ez a cikk azt ismerteti, hogyan valósítható meg egy folyamatos integrációs és folyamatos teljesítési (CI/CD) folyamat egy olyan AI-alkalmazáshoz, amely az ML-modellt az alkalmazás forráskódjában ágyazza be. A mintakód és az oktatóanyag egy Python-lombik webalkalmazást használ, és egy saját Azure Blob Storage-fiókból beolvassa az előképzésen alapuló modellt. Egy AWS S3 Storage-fiókot is használhat.

> [!NOTE]
> A következő folyamat a CI/CD-k számos módszerének egyike. Az eszközökre és az előfeltételekre vonatkozó alternatívák is rendelkezésre állnak.

## <a name="source-code-tutorial-and-prerequisites"></a>Forráskód, oktatóanyag és előfeltételek

A GitHubról a [forráskódot](https://github.com/Azure/DevOps-For-AI-Apps) és a [részletes oktatóanyagot](https://github.com/Azure/DevOps-For-AI-Apps/blob/master/Tutorial.md) is letöltheti. Kövesse az oktatóanyag lépéseit egy CI/CD-folyamat megvalósításához a saját alkalmazásához.

A letöltött forráskód és oktatóanyag használatához a következő előfeltételek szükségesek: 

- A GitHub-fiókhoz tartozó [forráskód-tárház](https://github.com/Azure/DevOps-For-AI-Apps)
- [Azure DevOps-szervezet](/azure/devops/organizations/accounts/create-organization-msa-or-work-student)
- [Azure CLI](/cli/azure/install-azure-cli)
- [Kubernetes-(ak-) fürt Azure Container Service](/previous-versions/azure/container-service/kubernetes/container-service-tutorial-kubernetes-deploy-cluster)
- [Kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl/) parancsok futtatása és a konfiguráció beolvasása az AK-fürtből 
- Egy [Azure Container Registry (ACR) fiók](../../container-registry/container-registry-get-started-portal.md)

## <a name="cicd-pipeline-summary"></a>CI/CD-folyamat összegzése

Minden új git-véglegesít kiindul a Build folyamatból. A Build biztonságosan lekéri a legújabb ML-modellt egy blob Storage-fiókból, és egyetlen tárolóban csomagolja azt az alkalmazás kódjába. Az alkalmazásfejlesztés és az adatelemzési workstreams leválasztása biztosítja, hogy az éles alkalmazás mindig a legújabb, a legújabb ML-modellel rendelkező kódot futtassa. Ha az alkalmazás teszteli a tesztelést, a folyamat biztonságosan tárolja a Build-képet egy Docker-tárolóban az ACR-ben. A kiadási folyamat ezután a tárolót az AK használatával helyezi üzembe. 

## <a name="cicd-pipeline-steps"></a>CI/CD-folyamat lépései

Az alábbi ábra és lépések a CI/CD folyamat architektúráját írják le:

![CI/CD-folyamat architektúrája](./media/ci-cd-flask/architecture.png)

1. A fejlesztők az alkalmazás kódjában dolgoznak a választott IDE-ben.
2. A fejlesztők véglegesítik a kódot az Azure Repos, a GitHub vagy más git-forrás vezérlőelem-szolgáltató számára. 
3. Az adatszakértők külön dolgoznak a ML-modellek fejlesztésén.
4. Az adatszakértők a befejezett modellt a modell adattárában teszik közzé, ebben az esetben egy blob Storage-fiókkal. 
5. Az Azure-folyamatok elindítanak egy buildet a git-véglegesítés alapján.
6. A build folyamat lekéri a legújabb ML-modellt a blob Storage-ból, és létrehoz egy tárolót.
7. A folyamat leküldi a Build rendszerképet a privát rendszerkép-tárházba az ACR-ben.
8. A kiadási folyamat a sikeres kiépítés alapján indul el.
9. A folyamat lekéri a legújabb képet az ACR-ből, és üzembe helyezi azt a Kubernetes-fürtön az AK-on keresztül.
10. Az alkalmazásra vonatkozó felhasználói kérések a DNS-kiszolgálón haladnak át.
11. A DNS-kiszolgáló továbbítja a kéréseket egy terheléselosztó számára, és visszaküldi a válaszokat a felhasználóknak.

## <a name="see-also"></a>Lásd még

- [Csoportos adatelemzési folyamat (TDSP)](./index.yml)
- [Azure Machine Learning (AML)](../index.yml)
- [Azure DevOps](https://azure.microsoft.com/services/devops/)
- [Azure Kubernetes Services (AKS)](../../aks/intro-kubernetes.md)