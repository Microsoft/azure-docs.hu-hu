---
title: Azure Machine Learning modellek folyamatos üzembe helyezése
titleSuffix: Azure Machine Learning
description: Ismerje meg, hogyan helyezhet üzembe folyamatosan modelleket a Azure Machine Learning DevOps bővítménnyel. Az új modell-verziók automatikus keresése és üzembe helyezése.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: gopalv
author: gvashishtha
ms.date: 08/03/2020
ms.topic: conceptual
ms.reviewer: larryfr
ms.custom: how-to, tracking-python, deploy
ms.openlocfilehash: 9de971639e22f9656ea75dc64993ac5881efbffb
ms.sourcegitcommit: d135e9a267fe26fbb5be98d2b5fd4327d355fe97
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/10/2021
ms.locfileid: "102609413"
---
# <a name="continuously-deploy-models"></a>Modellek folyamatos üzembe helyezése

Ez a cikk bemutatja, hogyan használhatja a folyamatos üzembe helyezést az Azure DevOps-ben a regisztrált modellek új verzióinak automatikus vizsgálatához és az új modellek éles környezetbe küldéséhez.

## <a name="prerequisites"></a>Előfeltételek

Ez a cikk azt feltételezi, hogy már regisztrált egy modellt a Azure Machine Learning munkaterületen. Tekintse meg [ezt az oktatóanyagot](how-to-train-scikit-learn.md) , amely egy scikit modell betanítását és regisztrálását szemlélteti.

## <a name="continuously-deploy-models"></a>Modellek folyamatos üzembe helyezése

A modellek folyamatos üzembe helyezéséhez használja az [Azure DevOps](https://azure.microsoft.com/services/devops/)Machine learning-bővítményét. Az Azure DevOps Machine Learning bővítményével elindíthat egy központi telepítési folyamatot, amikor egy új gépi tanulási modell van regisztrálva egy Azure Machine Learning munkaterületen.

1. Regisztráljon az [Azure-folyamatokra](/azure/devops/pipelines/get-started/pipelines-sign-up), ami lehetővé teszi az alkalmazás folyamatos integrálását és megvalósítását bármilyen platformon vagy felhőben. (Vegye figyelembe, hogy az Azure-folyamatok nem egyeznek meg [Machine learning folyamatokkal](concept-ml-pipelines.md#compare).)

1. [Hozzon létre egy Azure DevOps-projektet.](/azure/devops/organizations/projects/create-project)

1. Telepítse az [Azure-folyamatok Machine learning-bővítményét](https://marketplace.visualstudio.com/items?itemName=ms-air-aiagility.vss-services-azureml&targetId=6756afbe-7032-4a36-9cb6-2771710cadc2&utm_source=vstsproduct&utm_medium=ExtHubManageList).

1. A szolgáltatási kapcsolatok szolgáltatással hozzon létre egy egyszerű szolgáltatásnevet a Azure Machine Learning munkaterülethez, hogy hozzáférjen az összetevőkhöz. Nyissa meg a projekt beállításait, válassza a **szolgáltatás kapcsolatai** lehetőséget, majd válassza a **Azure Resource Manager**:

    [![Azure Resource Manager kiválasztása](media/how-to-deploy-and-where/view-service-connection.png)](media/how-to-deploy-and-where/view-service-connection-expanded.png)

1. A **hatókör szintje** listán válassza a **AzureMLWorkspace** lehetőséget, majd adja meg a többi értéket:

    ![AzureMLWorkspace kiválasztása](media/how-to-deploy-and-where/resource-manager-connection.png)

1. A gépi tanulási modell Azure-folyamatokkal való folyamatos üzembe helyezéséhez a folyamatok területen válassza a **kiadás** lehetőséget. Adjon hozzá egy új összetevőt, majd válassza ki a **AzureML-modell** összetevőt és a korábban létrehozott szolgáltatási kapcsolatokat. A telepítés elindításához válassza ki a modellt és a verziót:

    [![AzureML modell kiválasztása](media/how-to-deploy-and-where/enable-modeltrigger-artifact.png)](media/how-to-deploy-and-where/enable-modeltrigger-artifact-expanded.png)

1. Engedélyezze a modell-triggert a modell-összetevőn. Ha bekapcsolja a triggert, minden alkalommal, amikor a modell megadott verziója (azaz a legújabb verziója) regisztrálva van a munkaterületen, az Azure DevOps kiadási folyamat aktiválódik.

    [![Modell-trigger engedélyezése](media/how-to-deploy-and-where/set-modeltrigger.png)](media/how-to-deploy-and-where/set-modeltrigger-expanded.png)

## <a name="next-steps"></a>Következő lépések

Tekintse meg az alábbi projekteket a GitHubon, ahol további példákat talál az ML-modellek folyamatos üzembe helyezésére.

* [Microsoft/MLOps](https://github.com/Microsoft/MLOps)
* [Microsoft/MLOpsPython](https://github.com/microsoft/MLOpsPython)