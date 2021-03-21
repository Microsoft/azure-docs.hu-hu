---
title: Webszolgáltatások frissítése
titleSuffix: Azure Machine Learning
description: Megtudhatja, hogyan frissíthet egy olyan webszolgáltatást, amely már telepítve van Azure Machine Learningban. Frissítheti a beállításokat, például a modellt, a környezetet és a bejegyzési parancsfájlt.
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: larryfr
ms.author: gopalv
author: gvashishtha
ms.date: 07/31/2020
ms.custom: deploy
ms.openlocfilehash: da018f212844e1587ad0e434902e49765f72e521
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102520097"
---
# <a name="update-a-deployed-web-service"></a>Központilag telepített webszolgáltatás frissítése

Ebből a cikkből megtudhatja, hogyan frissítheti a Azure Machine Learning használatával üzembe helyezett webszolgáltatásokat.

## <a name="prerequisites"></a>Előfeltételek

Ez az oktatóanyag feltételezi, hogy már telepített egy webszolgáltatást a Azure Machine Learning. Ha meg szeretné ismerni, hogyan helyezhet üzembe egy webszolgáltatást, [kövesse az alábbi lépéseket](how-to-deploy-and-where.md).

## <a name="update-web-service"></a>Webszolgáltatás frissítése

Webszolgáltatások frissítéséhez használja a `update` metódust. Frissítheti a webszolgáltatást egy új modell, egy új bejegyzési parancsfájl vagy egy következtetési konfigurációban megadható új függőségek használatára. További információkért tekintse meg a [webszolgáltatások dokumentációját. frissítés](/python/api/azureml-core/azureml.core.webservice.webservice.webservice#update--args-).

Lásd: [AK Service Update metódus.](/python/api/azureml-core/azureml.core.webservice.akswebservice#update-image-none--autoscale-enabled-none--autoscale-min-replicas-none--autoscale-max-replicas-none--autoscale-refresh-seconds-none--autoscale-target-utilization-none--collect-model-data-none--auth-enabled-none--cpu-cores-none--memory-gb-none--enable-app-insights-none--scoring-timeout-ms-none--replica-max-concurrent-requests-none--max-request-wait-time-none--num-replicas-none--tags-none--properties-none--description-none--models-none--inference-config-none--gpu-cores-none--period-seconds-none--initial-delay-seconds-none--timeout-seconds-none--success-threshold-none--failure-threshold-none--namespace-none--token-auth-enabled-none-)

Lásd: [ACI Service Update metódus.](/python/api/azureml-core/azureml.core.webservice.aci.aciwebservice#update-image-none--tags-none--properties-none--description-none--auth-enabled-none--ssl-enabled-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--ssl-cname-none--enable-app-insights-none--models-none--inference-config-none-)

> [!IMPORTANT]
> A modell új verziójának létrehozásakor manuálisan kell frissítenie az összes használni kívánt szolgáltatást.
>
> Az SDK-val nem lehet frissíteni a Azure Machine Learning Designer alkalmazásban közzétett webszolgáltatásokat.

**Az SDK használata**

A következő kód bemutatja, hogyan használható az SDK a webszolgáltatások modelljének, környezetének és bejegyzési parancsfájljának frissítéséhez:

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

**A parancssori felület használata**

A webszolgáltatás a ML parancssori felület használatával is frissíthető. Az alábbi példa bemutatja egy új modell regisztrálását, majd egy webszolgáltatás frissítését az új modell használatára:

```azurecli
az ml model register -n sklearn_mnist  --asset-path outputs/sklearn_mnist_model.pkl  --experiment-name myexperiment --output-metadata-file modelinfo.json
az ml service update -n myservice --model-metadata-file modelinfo.json
```

> [!TIP]
> Ebben a példában egy JSON-dokumentum segítségével továbbítja a modell adatait a regisztrációs parancsból az Update parancsba.
>
> Ha a szolgáltatást új bejegyzési parancsfájl vagy környezet használatára szeretné frissíteni, hozzon létre egy [következtetési konfigurációs fájlt](./reference-azure-machine-learning-cli.md#inference-configuration-schema) , és adja meg a `ic` paramétert.

További információt az az [ml Service Update](/cli/azure/ext/azure-cli-ml/ml/service#ext-azure-cli-ml-az-ml-service-update) dokumentációjában talál.

## <a name="next-steps"></a>Következő lépések

* [Sikertelen üzembe helyezés hibaelhárítása](how-to-troubleshoot-deployment.md)
* [Üzembe helyezés az Azure Kubernetes Service-ben](how-to-deploy-azure-kubernetes-service.md)
* [Ügyfélalkalmazások létrehozása webszolgáltatások felhasználásához](how-to-consume-web-service.md)
* [Modell üzembe helyezése egyéni Docker-rendszerkép használatával](how-to-deploy-custom-docker-image.md)
* [TLS használata webszolgáltatás védelméhez az Azure Machine Learning szolgáltatás segítségével](how-to-secure-web-service.md)
* [A Azure Machine Learning modellek monitorozása a Application Insights](how-to-enable-app-insights.md)
* [Adatok gyűjtése a termelési modellekhez](how-to-enable-data-collection.md)
* [Esemény-riasztások és eseményindítók létrehozása a modell üzembe helyezéséhez](how-to-use-event-grid.md)
