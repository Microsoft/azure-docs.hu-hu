---
author: Blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 01/28/2020
ms.author: larryfr
ms.openlocfilehash: a03f71adc99063fee4374b1436b08adf5bab783d
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/09/2021
ms.locfileid: "102510827"
---
A dokumentumban szereplő bejegyzések a `inferenceconfig.json` [InferenceConfig](/python/api/azureml-core/azureml.core.model.inferenceconfig) osztály paramétereinek felelnek meg. A következő táblázat ismerteti a JSON-dokumentum entitásai közötti leképezést, valamint a metódus paramétereit:

| JSON-entitás | Metódus paramétere | Leírás |
| ----- | ----- | ----- |
| `entryScript` | `entry_script` | A rendszerképhez futtatandó kódot tartalmazó helyi fájl elérési útja. |
| `sourceDirectory` | `source_directory` | Választható. Az összes fájlt tartalmazó mappák elérési útja a rendszerkép létrehozásához, amely megkönnyíti a mappában vagy almappában található fájlok elérését. A webszolgáltatások függőségeiként feltölthet egy teljes mappát a helyi gépről. Megjegyzés: a entry_script, a conda_file és a extra_docker_file_steps útvonalak relatív elérési utak az source_directory elérési úthoz. |
| `environment` | `environment` | Választható.  Azure Machine Learning [környezet](/python/api/azureml-core/azureml.core.environment.environment).|

Azure Machine Learning [környezet](/python/api/azureml-core/azureml.core.environment.environment) teljes specifikációit a következtetési konfigurációs fájlban is megadhatja. Ha ez a környezet nem létezik a munkaterületen, akkor a Azure Machine Learning létrehozza azt. Ellenkező esetben a Azure Machine Learning szükség esetén frissíti a környezetet. A következő JSON egy példa:

```json
{
    "entryScript": "score.py",
    "environment": {
        "docker": {
            "arguments": [],
            "baseDockerfile": null,
            "baseImage": "mcr.microsoft.com/azureml/base:intelmpi2018.3-ubuntu16.04",
            "enabled": false,
            "sharedVolumes": true,
            "shmSize": null
        },
        "environmentVariables": {
            "EXAMPLE_ENV_VAR": "EXAMPLE_VALUE"
        },
        "name": "my-deploy-env",
        "python": {
            "baseCondaEnvironment": null,
            "condaDependencies": {
                "channels": [
                    "conda-forge"
                ],
                "dependencies": [
                    "python=3.6.2",
                    {
                        "pip": [
                            "azureml-defaults",
                            "azureml-telemetry",
                            "scikit-learn==0.22.1",
                            "inference-schema[numpy-support]"
                        ]
                    }
                ],
                "name": "project_environment"
            },
            "condaDependenciesFile": null,
            "interpreterPath": "python",
            "userManagedDependencies": false
        },
        "version": "1"
    }
}
```

Meglévő Azure Machine Learning- [környezetet](/python/api/azureml-core/azureml.core.environment.environment) is HASZNÁLHAT külön CLI-paraméterekben, és eltávolíthatja a "környezet" kulcsot a következtetési konfigurációs fájlból. Használja az-e nevet a környezet neveként, a--EV pedig a környezet verzióját. Ha nem ad meg--EV-t, a rendszer a legújabb verziót fogja használni. Íme egy példa a következtetési konfigurációs fájlra:

```json
{
    "entryScript": "score.py",
    "sourceDirectory": null
}
```

A következő parancs bemutatja, hogyan helyezhet üzembe egy modellt a korábbi következtetési konfigurációs fájl (myInferenceConfig.json) használatával. 

Egy meglévő Azure Machine Learning- [környezet](/python/api/azureml-core/azureml.core.environment.environment) legújabb verzióját is használja (AzureML-minimal néven).

```azurecli-interactive
az ml model deploy -m mymodel:1 --ic myInferenceConfig.json -e AzureML-Minimal --dc deploymentconfig.json
```