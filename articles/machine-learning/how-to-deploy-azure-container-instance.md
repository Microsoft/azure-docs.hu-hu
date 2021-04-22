---
title: Modellek üzembe helyezése a Azure Container Instances
titleSuffix: Azure Machine Learning
description: Megtudhatja, hogyan helyezheti üzembe Azure Machine Learning modelleket webszolgáltatásként a Azure Container Instances.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to, deploy
ms.author: jordane
author: jpe316
ms.reviewer: larryfr
ms.date: 06/12/2020
ms.openlocfilehash: 845a146d9e3f920f3313a80f1bb8c845cb781f37
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2021
ms.locfileid: "107875538"
---
# <a name="deploy-a-model-to-azure-container-instances"></a>Modell üzembe helyezése az Azure Container Instances szolgáltatásban

Megtudhatja, hogyan helyezhet Azure Machine Learning egy modellt webszolgáltatásként a Azure Container Instances (ACI) szolgáltatásban. Akkor Azure Container Instances, ha az alábbi feltételek egyike teljesül:

- Gyorsan üzembe kell helyeznie és ellenőriznie kell a modellt. Nem kell előre ACI-tárolókat létrehoznia. Ezek az üzembe helyezési folyamat részeként vannak létrehozva.
- Egy fejlesztés alatt áll modellt tesztel. 

Az ACI kvótáiról és régiónkénti rendelkezésre állásról további információt a Kvóták és régiók rendelkezésre állása [a](../container-instances/container-instances-quotas.md) Azure Container Instances cikkben talál.

> [!IMPORTANT]
> A webszolgáltatásban való üzembe helyezés előtt javasoljuk a helyi hibakeresést. További információ: [Helyi hibakeresés](./how-to-troubleshoot-deployment-local.md)
>
> További információ: Azure Machine Learning – [Üzembe helyezés helyi notebookba](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/deployment/deploy-to-local)

## <a name="prerequisites"></a>Előfeltételek

- Egy Azure Machine Learning-munkaterület. További információ: [Create an Azure Machine Learning workspace](how-to-manage-workspace.md)(Munkaterület Azure Machine Learning létrehozása).

- A munkaterületen regisztrált gépi tanulási modell. Ha még nem regisztrált modellel, tekintse meg a modellek üzembe helyezésének [mikéntjéhez és helyéhez lásd:](how-to-deploy-and-where.md).

- Az [Azure CLI-bővítmény Machine Learning szolgáltatáshoz,](reference-azure-machine-learning-cli.md) [Azure Machine Learning Python SDK-hoz](/python/api/overview/azure/ml/intro)vagy a [Azure Machine Learning Visual Studio Code-bővítményhez.](tutorial-setup-vscode-extension.md)

- A __cikkben__ a Python-kódrészletek feltételezik, hogy a következő változók vannak beállítva:

    * `ws` – Állítsa be a munkaterületet.
    * `model` – Állítsa be a regisztrált modellt.
    * `inference_config` – Állítsa be a modell következtetési konfigurációját.

    A változók beállításával kapcsolatos további információkért lásd: [Modellek üzembe](how-to-deploy-and-where.md)helyezése.

- A __cikkben__ a CLI-kódrészletek feltételezik, hogy létrehozott egy `inferenceconfig.json` dokumentumot. További információ a dokumentum létrehozásáról: [Modellek üzembe](how-to-deploy-and-where.md)helyezése.

## <a name="limitations"></a>Korlátozások

* Ha virtuális Azure Container Instances használ, a virtuális hálózatnak ugyanabban az erőforráscsoportban kell lennie, mint a Azure Machine Learning munkaterületen.
* Ha virtuális Azure Container Instances belül használ, a munkaterület Azure Container Registry (ACR) nem lehet a virtuális hálózatban.

További információkért lásd: [How to secure deferencing with virtual networks (A](how-to-secure-inferencing-vnet.md#enable-azure-container-instances-aci)következtetés biztonságossákolása virtuális hálózatokkal).

## <a name="deploy-to-aci"></a>Üzembe helyezés az ACI-ban

A modell üzembe helyezéséhez a Azure Container Instances hozzon létre egy üzembe helyezési konfigurációt, amely leírja __a__ szükséges számítási erőforrásokat. Például a magok száma és a memória. Emellett szükség van egy __következtetési__ konfigurációra is, amely leírja a modell és a webszolgáltatás gazdagépéhez szükséges környezetet. A következtetési konfiguráció létrehozásával kapcsolatos további információkért lásd: [Modellek](how-to-deploy-and-where.md)üzembe helyezése.

> [!NOTE]
> * Az ACI csak 1 GB-nál kisebb méretű kis modellekhez alkalmas. 
> * Nagyobb modellek fejlesztéshez és teszteléshez egycsomópontos AKS használatát javasoljuk.
> * Az üzembe helyezendő modellek száma üzemelő példányonként legfeljebb 1000 modell lehet (tárolónként). 

### <a name="using-the-sdk"></a>Az SDK használata

```python
from azureml.core.webservice import AciWebservice, Webservice
from azureml.core.model import Model

deployment_config = AciWebservice.deploy_configuration(cpu_cores = 1, memory_gb = 1)
service = Model.deploy(ws, "aciservice", [model], inference_config, deployment_config)
service.wait_for_deployment(show_output = True)
print(service.state)
```

A példában használt osztályokkal, metódusokkal és paraméterekkel kapcsolatos további információkért tekintse meg a következő referenciadokumentumokat:

* [AciWebservice.deploy_configuration](/python/api/azureml-core/azureml.core.webservice.aciwebservice#deploy-configuration-cpu-cores-none--memory-gb-none--tags-none--properties-none--description-none--location-none--auth-enabled-none--ssl-enabled-none--enable-app-insights-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--ssl-cname-none--dns-name-label-none--primary-key-none--secondary-key-none--collect-model-data-none--cmk-vault-base-url-none--cmk-key-name-none--cmk-key-version-none-)
* [Model.deploy](/python/api/azureml-core/azureml.core.model.model#deploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-)
* [Webservice.wait_for_deployment](/python/api/azureml-core/azureml.core.webservice%28class%29#wait-for-deployment-show-output-false-)

### <a name="using-the-azure-cli"></a>Az Azure CLI-vel

A parancssori felület használatával való üzembe helyezéshez használja a következő parancsot. Cserélje `mymodel:1` le a helyére a regisztrált modell nevét és verzióját. Cserélje `myservice` le a helyére a szolgáltatás nevét:

```azurecli-interactive
az ml model deploy -m mymodel:1 -n myservice -ic inferenceconfig.json -dc deploymentconfig.json
```

[!INCLUDE [deploymentconfig](../../includes/machine-learning-service-aci-deploy-config.md)]

További információért tekintse meg az [az ml model deploy reference (az az ml model deploy referencia)](/cli/azure/ml/model#az_ml_model_deploy) referenciát. 

## <a name="using-vs-code"></a>A VS Code használata

Lásd: [Modellek üzembe helyezése a VS Code-kóddal.](tutorial-train-deploy-image-classification-model-vscode.md#deploy-the-model)

> [!IMPORTANT]
> A teszteléshez nem szükséges ACI-tárolót létrehoznia. Az ACI-tárolók szükség szerint létrejönnek.

> [!IMPORTANT]
> Kivonatolt munkaterület-azonosítót fűzünk az összes létrehozott mögöttes ACI-erőforráshoz, és az ugyanannak a munkaterületnek az összes ACI-neve ugyanaz lesz az utótagja. A Azure Machine Learning service neve továbbra is ugyanaz az ügyfél által megadott "service_name" lesz, és az SDK API Azure Machine Learning felé néző összes felhasználónak nincs szüksége módosításra. A mögöttes erőforrások nevére nem vállalunk garanciát.

## <a name="next-steps"></a>Következő lépések

* [Modell üzembe helyezése egyéni Docker-rendszerkép használatával](how-to-deploy-custom-docker-image.md)
* [Üzembehelyezés hibaelhárítása](how-to-troubleshoot-deployment.md)
* [A webszolgáltatás frissítése](how-to-deploy-update-web-service.md)
* [TLS használata webszolgáltatás védelméhez az Azure Machine Learning szolgáltatás segítségével](how-to-secure-web-service.md)
* [Webszolgáltatásként üzembe helyezett gépi tanulási modell felhasználta](how-to-consume-web-service.md)
* [Monitor a Azure Machine Learning modelleket a Application Insights](how-to-enable-app-insights.md)
* [Adatok gyűjtése éles modellekhez](how-to-enable-data-collection.md)