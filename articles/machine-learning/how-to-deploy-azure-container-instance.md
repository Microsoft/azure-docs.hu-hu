---
title: Modellek üzembe helyezése Azure Container Instances
titleSuffix: Azure Machine Learning
description: Megtudhatja, hogyan helyezheti üzembe a Azure Machine Learning modelleket webszolgáltatásként Azure Container Instances használatával.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to, deploy
ms.author: jordane
author: jpe316
ms.reviewer: larryfr
ms.date: 06/12/2020
ms.openlocfilehash: a41af6e949dc568a263d4b1d62aa99e8a1c9ece5
ms.sourcegitcommit: f7eda3db606407f94c6dc6c3316e0651ee5ca37c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2021
ms.locfileid: "102215255"
---
# <a name="deploy-a-model-to-azure-container-instances"></a>Modell üzembe helyezése az Azure Container Instances szolgáltatásban

Ismerje meg, hogyan helyezhet üzembe egy modellt webszolgáltatásként a Azure Container Instances (ACI) Azure Machine Learning használatával. Azure Container Instances használata, ha a következő feltételek egyike igaz:

- Gyorsan üzembe kell helyeznie és ellenőriznie kell a modellt. Előre nem kell létrehoznia ACI-tárolókat. Ezek a telepítési folyamat részeként jönnek létre.
- A fejlesztés alatt álló modellt tesztel. 

Az ACI-ra vonatkozó kvóta-és területi elérhetőséggel kapcsolatos információkért lásd: a [kvóták és a régió rendelkezésre állása Azure Container instances](../container-instances/container-instances-quotas.md) cikkben.

> [!IMPORTANT]
> Javasoljuk, hogy a webszolgáltatásba való üzembe helyezés előtt helyileg végezzen hibakeresést, további információ: [helyi hibakeresés](./how-to-troubleshoot-deployment-local.md)
>
> További információ: Azure Machine Learning – [Üzembe helyezés helyi notebookba](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/deployment/deploy-to-local)

## <a name="prerequisites"></a>Előfeltételek

- Egy Azure Machine Learning-munkaterület. További információ: [Azure Machine learning munkaterület létrehozása](how-to-manage-workspace.md).

- A munkaterületen regisztrált gépi tanulási modell. Ha nem rendelkezik regisztrált modellel, tekintse meg a [modellek üzembe helyezésének módját és helyét](how-to-deploy-and-where.md).

- Az [Azure CLI-bővítmény Machine learning szolgáltatáshoz](reference-azure-machine-learning-cli.md), [Azure Machine learning Python SDK](/python/api/overview/azure/ml/intro?preserve-view=true&view=azure-ml-py)-hoz vagy a [Azure Machine learning Visual Studio Code bővítményhez](tutorial-setup-vscode-extension.md).

- A cikkben szereplő __Python__ -kódrészletek azt feltételezik, hogy a következő változók vannak beállítva:

    * `ws` – Állítsa be a munkaterületre.
    * `model` – Állítsa be a regisztrált modellt.
    * `inference_config` – Állítsa be a modellre vonatkozó következtetési konfigurációt.

    A változók beállításával kapcsolatos további információkért lásd: [how és How to Deploy models (modellek üzembe helyezése](how-to-deploy-and-where.md)).

- A cikkben szereplő __CLI__ -kódrészletek azt feltételezik, hogy létrehozott egy `inferenceconfig.json` dokumentumot. A dokumentum létrehozásával kapcsolatos további információkért lásd: [how és How to Deploy models (modellek üzembe helyezése](how-to-deploy-and-where.md)).

## <a name="limitations"></a>Korlátozások

* Ha a virtuális hálózatban Azure Container Instancest használ, a virtuális hálózatnak ugyanabban az erőforráscsoporthoz kell tartoznia, mint a Azure Machine Learning-munkaterületnek.
* Ha a virtuális hálózaton belül Azure Container Instancest használ, a munkaterület Azure Container Registry (ACR) nem lehet a virtuális hálózatban is.

További információ: [a virtuális hálózatokkal való következtetések biztonságossá tétele](how-to-secure-inferencing-vnet.md#enable-azure-container-instances-aci).

## <a name="deploy-to-aci"></a>Üzembe helyezés az ACI-ban

Azure Container Instances modell üzembe helyezéséhez hozzon létre egy __központi telepítési konfigurációt__ , amely leírja a szükséges számítási erőforrásokat. Például a magok és a memória száma. Szüksége lesz egy __következtetésre__ is, amely leírja a modell és a webszolgáltatás üzemeltetéséhez szükséges környezetet. A következtetések konfigurációjának létrehozásáról további információt a [modellek üzembe helyezésének módja és helye](how-to-deploy-and-where.md)című témakörben talál.

> [!NOTE]
> * Az ACI csak olyan kisméretű modellekhez alkalmas, amelyek mérete 1 GB. 
> * Azt javasoljuk, hogy egy egycsomópontos AK-t használjon a nagyobb modellek fejlesztéséhez.
> * A telepítendő modellek száma az üzemelő példányok esetében 1 000 modellre korlátozódik (tárolóként). 

### <a name="using-the-sdk"></a>Az SDK használata

```python
from azureml.core.webservice import AciWebservice, Webservice
from azureml.core.model import Model

deployment_config = AciWebservice.deploy_configuration(cpu_cores = 1, memory_gb = 1)
service = Model.deploy(ws, "aciservice", [model], inference_config, deployment_config)
service.wait_for_deployment(show_output = True)
print(service.state)
```

Az ebben a példában használt osztályokkal, metódusokkal és paraméterekkel kapcsolatos további információkért tekintse meg a következő dokumentációt:

* [AciWebservice.deploy_configuration](/python/api/azureml-core/azureml.core.webservice.aciwebservice?preserve-view=true&view=azure-ml-py#&preserve-view=truedeploy-configuration-cpu-cores-none--memory-gb-none--tags-none--properties-none--description-none--location-none--auth-enabled-none--ssl-enabled-none--enable-app-insights-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--ssl-cname-none--dns-name-label-none--primary-key-none--secondary-key-none--collect-model-data-none--cmk-vault-base-url-none--cmk-key-name-none--cmk-key-version-none-)
* [Modell. Deploy](/python/api/azureml-core/azureml.core.model.model?preserve-view=true&view=azure-ml-py#&preserve-view=truedeploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-)
* [Webservice.wait_for_deployment](/python/api/azureml-core/azureml.core.webservice%28class%29?preserve-view=true&view=azure-ml-py#&preserve-view=truewait-for-deployment-show-output-false-)

### <a name="using-the-cli"></a>A parancssori felület használata

A CLI használatával történő üzembe helyezéshez használja a következő parancsot. Cserélje le a `mymodel:1` nevet a regisztrált modell nevére és verziójára. Cserélje le `myservice` a nevet a következő szolgáltatáshoz:

```azurecli-interactive
az ml model deploy -m mymodel:1 -n myservice -ic inferenceconfig.json -dc deploymentconfig.json
```

[!INCLUDE [deploymentconfig](../../includes/machine-learning-service-aci-deploy-config.md)]

További információ: az [ml Model Deploy](/cli/azure/ext/azure-cli-ml/ml/model#ext-azure-cli-ml-az-ml-model-deploy) Reference. 

## <a name="using-vs-code"></a>A VS Code használata

Lásd: [modellek üzembe helyezése a vs Code](tutorial-train-deploy-image-classification-model-vscode.md#deploy-the-model)-ban.

> [!IMPORTANT]
> A teszteléshez nem kell ACI-tárolót létrehoznia. Az ACI-tárolók szükség szerint jönnek létre.

> [!IMPORTANT]
> A rendszer az összes létrehozott ACI-erőforráshoz hozzáfűzi a kivonatoló munkaterület azonosítóját, amely az azonos munkaterületről származó összes ACI-nevet ugyanazzal az utótaggal fogja ellátni. A Azure Machine Learning szolgáltatás neve továbbra is az ügyfél által megadott "service_name" lesz, és az Azure Machine Learning SDK API-khoz kapcsolódó összes felhasználónak nincs szüksége módosításra. Nem biztosítunk garanciát a létrehozott mögöttes erőforrások neveire.

## <a name="next-steps"></a>Következő lépések

* [Modell üzembe helyezése egyéni Docker-rendszerkép használatával](how-to-deploy-custom-docker-image.md)
* [Üzembe helyezés hibaelhárítása](how-to-troubleshoot-deployment.md)
* [Webszolgáltatás frissítése](how-to-deploy-update-web-service.md)
* [TLS használata webszolgáltatás védelméhez az Azure Machine Learning szolgáltatás segítségével](how-to-secure-web-service.md)
* [Webszolgáltatásként üzembe helyezett ML-modell felhasználása](how-to-consume-web-service.md)
* [A Azure Machine Learning modellek monitorozása a Application Insights](how-to-enable-app-insights.md)
* [Adatok gyűjtése a termelési modellekhez](how-to-enable-data-collection.md)