---
title: Webszolgáltatások frissítése
titleSuffix: Azure Machine Learning
description: Megtudhatja, hogyan frissíthet egy olyan webszolgáltatást, amely már telepítve van a Azure Machine Learning. Frissítheti a beállításokat, például a modellt, a környezetet és a bejegyzési szkriptet.
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: larryfr
ms.author: gopalv
author: gvashishtha
ms.date: 07/31/2020
ms.custom: deploy
ms.openlocfilehash: 098dc4be33c82faff1b85a5b7224ecaf1be64944
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2021
ms.locfileid: "107875466"
---
# <a name="update-a-deployed-web-service"></a>Üzembe helyezett webszolgáltatás frissítése

Ebből a cikkből megtudhatja, hogyan frissíthet egy olyan webszolgáltatást, amely a Azure Machine Learning.

## <a name="prerequisites"></a>Előfeltételek

Ez az oktatóanyag feltételezi, hogy már üzembe helyezett egy webszolgáltatást a Azure Machine Learning. Ha meg kell tanulnia, hogyan helyezhet üzembe egy webszolgáltatást, [kövesse az alábbi lépéseket.](how-to-deploy-and-where.md)

## <a name="update-web-service"></a>Webszolgáltatás frissítése

Webszolgáltatás frissítéséhez használja a `update` metódust. Frissítheti a webszolgáltatást, hogy új modellt, új bejegyzési szkriptet vagy új függőségeket használjon, amelyek meghatározhatóak egy következtetési konfigurációban. További információt a [Webservice.update dokumentációjában talál.](/python/api/azureml-core/azureml.core.webservice.webservice.webservice#update--args-)

Lásd: [AKS-szolgáltatásfrissítési módszer.](/python/api/azureml-core/azureml.core.webservice.akswebservice#update-image-none--autoscale-enabled-none--autoscale-min-replicas-none--autoscale-max-replicas-none--autoscale-refresh-seconds-none--autoscale-target-utilization-none--collect-model-data-none--auth-enabled-none--cpu-cores-none--memory-gb-none--enable-app-insights-none--scoring-timeout-ms-none--replica-max-concurrent-requests-none--max-request-wait-time-none--num-replicas-none--tags-none--properties-none--description-none--models-none--inference-config-none--gpu-cores-none--period-seconds-none--initial-delay-seconds-none--timeout-seconds-none--success-threshold-none--failure-threshold-none--namespace-none--token-auth-enabled-none-)

Lásd: [ACI-szolgáltatásfrissítési módszer.](/python/api/azureml-core/azureml.core.webservice.aci.aciwebservice#update-image-none--tags-none--properties-none--description-none--auth-enabled-none--ssl-enabled-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--ssl-cname-none--enable-app-insights-none--models-none--inference-config-none-)

> [!IMPORTANT]
> Amikor egy modell új verzióját hozza létre, manuálisan kell frissítenie minden használni kívánt szolgáltatást.
>
> Nem használhatja az SDK-t az Azure Machine Learning által közzétett webszolgáltatás frissítésére.

**Az SDK használata**

A következő kód bemutatja, hogyan frissítheti egy webszolgáltatás modelljét, környezetét és bejegyzési szkriptét az SDK használatával:

```python
from azureml.core import Environment
from azureml.core.webservice import Webservice
from azureml.core.model import Model, InferenceConfig

# Register new model.
new_model = Model.register(model_path="outputs/sklearn_mnist_model.pkl",
                           model_name="sklearn_mnist",
                           tags={"key": "0.1"},
                           description="test",
                           workspace=ws)

# Use version 3 of the environment.
deploy_env = Environment.get(workspace=ws,name="myenv",version="3")
inference_config = InferenceConfig(entry_script="score.py",
                                   environment=deploy_env)

service_name = 'myservice'
# Retrieve existing service.
service = Webservice(name=service_name, workspace=ws)



# Update to new model(s).
service.update(models=[new_model], inference_config=inference_config)
service.wait_for_deployment(show_output=True)
print(service.state)
print(service.get_logs())
```

**A CLI használata**

A webszolgáltatást az ML CLI használatával is frissítheti. Az alábbi példa bemutatja egy új modell regisztrálását, majd egy webszolgáltatás frissítését az új modell használatára:

```azurecli
az ml model register -n sklearn_mnist  --asset-path outputs/sklearn_mnist_model.pkl  --experiment-name myexperiment --output-metadata-file modelinfo.json
az ml service update -n myservice --model-metadata-file modelinfo.json
```

> [!TIP]
> Ebben a példában egy JSON-dokumentummal adhatja át a regisztrációs parancs modellinformációját az update parancsnak.
>
> Ha frissítenie kell a szolgáltatást egy új [](./reference-azure-machine-learning-cli.md#inference-configuration-schema) bejegyzési szkript vagy környezet használatára, hozzon létre egy következtetési konfigurációs fájlt, és adja meg a `ic` paraméterrel.

További információt az [az ml service update dokumentációjában](/cli/azure/ml/service#az_ml_service_update) talál.

## <a name="next-steps"></a>Következő lépések

* [Sikertelen üzembe helyezés hibaelhárítása](how-to-troubleshoot-deployment.md)
* [Üzembe helyezés az Azure Kubernetes Service-ben](how-to-deploy-azure-kubernetes-service.md)
* [Ügyfélalkalmazások létrehozása webszolgáltatásokhoz](how-to-consume-web-service.md)
* [Modell üzembe helyezése egyéni Docker-rendszerkép használatával](how-to-deploy-custom-docker-image.md)
* [TLS használata webszolgáltatás védelméhez az Azure Machine Learning szolgáltatás segítségével](how-to-secure-web-service.md)
* [Monitor a Azure Machine Learning modelleket a Application Insights](how-to-enable-app-insights.md)
* [Adatok gyűjtése éles modellekhez](how-to-enable-data-collection.md)
* [Eseményriasztás és eseményindítók létrehozása a modell üzembe helyezéséhez](how-to-use-event-grid.md)
