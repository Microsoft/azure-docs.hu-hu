---
title: Ml modellek üzembe helyezése Azure App Service (előzetes verzió)
titleSuffix: Azure Machine Learning
description: Megtudhatja, hogyan helyezhet üzembe egy betanított ML-modellt egy webalkalmazásban Azure App Service használatával a Azure Machine Learning használatával.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 06/23/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python, deploy, devx-track-azurecli
ms.openlocfilehash: 3b1b416f3fec9e40261a82c88260c041918c1424
ms.sourcegitcommit: 956dec4650e551bdede45d96507c95ecd7a01ec9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/09/2021
ms.locfileid: "102522002"
---
# <a name="deploy-a-machine-learning-model-to-azure-app-service-preview"></a>Gépi tanulási modell üzembe helyezése Azure App Service (előzetes verzió)


Megtudhatja, hogyan helyezhet üzembe modellt Azure Machine Learning webalkalmazásként a Azure App Serviceban.

> [!IMPORTANT]
> Noha a Azure Machine Learning és az Azure App Service is általánosan elérhető, a modell üzembe helyezésének lehetősége a Machine Learning szolgáltatásból a App Service előzetes verzióban érhető el.

A Azure Machine Learning segítségével Docker-rendszerképeket hozhat létre a képzett gépi tanulási modellekből. Ez a rendszerkép olyan webszolgáltatást tartalmaz, amely fogadja az adatok fogadását, elküldi a modellbe, majd visszaadja a választ. A Azure App Service használható a lemezkép üzembe helyezéséhez, és a következő funkciókat biztosítja:

* Speciális [hitelesítés](../app-service/configure-authentication-provider-aad.md) a fokozott biztonsághoz. A hitelesítési módszerek közé tartozik a Azure Active Directory és a multi-Factor Auth is.
* Az [autoskálázást](../azure-monitor/autoscale/autoscale-get-started.md?toc=%2fazure%2fapp-service%2ftoc.json) anélkül, hogy újra kellene telepíteni.
* [TLS-támogatás](../app-service/configure-ssl-certificate-in-code.md) az ügyfelek és a szolgáltatás közötti biztonságos kommunikációhoz.

A Azure App Service által nyújtott szolgáltatásokkal kapcsolatos további információkért tekintse meg a [app Service áttekintését](../app-service/overview.md).

> [!IMPORTANT]
> Ha szüksége van az üzembe helyezett modellel használt pontozási-adatnapló vagy a pontozás eredményeinek naplózására, ehelyett üzembe kell helyeznie az Azure Kubernetes Service-t. További információ: [adatgyűjtés az éles modelleken](how-to-enable-data-collection.md).

## <a name="prerequisites"></a>Előfeltételek

* Egy Azure Machine Learning-munkaterület. További információt a [Munkaterület létrehozása](how-to-manage-workspace.md) című cikkben talál.
* Az [Azure CLI](/cli/azure/install-azure-cli)-vel.
* A munkaterületen regisztrált, betanított gépi tanulási modell. Ha nem rendelkezik modellel, használja a [képbesorolási oktatóanyagot: a betanítási modell](tutorial-train-models-with-aml.md) betanítása és regisztrálása.

    > [!IMPORTANT]
    > A cikkben szereplő kódrészletek azt feltételezik, hogy a következő változókat állította be:
    >
    > * `ws` – Azure Machine Learning munkaterület.
    > * `model` – A rendszerbe állított regisztrált modell.
    > * `inference_config` – A modellre vonatkozó következtetési konfiguráció.
    >
    > A változók beállításával kapcsolatos további információkért lásd: [modellek üzembe helyezése Azure Machine Learningsal](how-to-deploy-and-where.md).

## <a name="prepare-for-deployment"></a>Felkészülés az üzembe helyezésre

A telepítés előtt meg kell határoznia, hogy mire van szükség a modell webszolgáltatásként való futtatásához. Az alábbi lista a központi telepítéshez szükséges fő elemeket ismerteti:

* Egy __bejegyzési parancsfájl__. Ez a szkript fogadja a kéréseket, a modell használatával szerzi a kérést, és visszaadja az eredményeket.

    > [!IMPORTANT]
    > A bejegyzési parancsfájl a modellre jellemző. meg kell ismernie a bejövő kérelmek adatainak formátumát, a modell által várt adatformátumot, valamint az ügyfeleknek visszaadott adatformátumot.
    >
    > Ha a kérelem adatai olyan formátumban vannak, amely nem használható a modellben, a parancsfájl elfogadható formátumba alakíthatja át. A válasz is át lehet alakítani, mielőtt visszatért az ügyfélhez.

    > [!IMPORTANT]
    > A Azure Machine Learning SDK nem biztosít lehetőséget a webszolgáltatás számára az adattár vagy az adatkészletek elérésére. Ha a központi telepítésen kívül tárolt adatokat szeretné elérni az üzembe helyezett modellel, például egy Azure Storage-fiókban, egyéni kódot kell létrehoznia a megfelelő SDK használatával. Például a [Pythonhoz készült Azure Storage SDK](https://github.com/Azure/azure-storage-python)-t.
    >
    > Egy másik alternatíva, amely a forgatókönyv esetében is működhet, a [Batch-előrejelzések](./tutorial-pipeline-batch-scoring-classification.md), amelyek a pontozáskor hozzáférést biztosítanak az adattárolóhoz.

    A beléptetési parancsfájlokkal kapcsolatos további információkért lásd: [modellek üzembe helyezése Azure Machine Learningsal](how-to-deploy-and-where.md).

* **Függőségek**, például segítő parancsfájlok vagy Python/Conda csomagok, amelyek a belépési parancsfájl vagy modell futtatásához szükségesek

Ezek az entitások egy __következtetési konfigurációba__ vannak ágyazva. A következtetési konfiguráció a bejegyzés parancsfájljára és további függőségekre hivatkozik.

> [!IMPORTANT]
> Ha Azure App Service-vel való használatra vonatkozó következtetési konfigurációt hoz létre, [környezeti](/python/api/azureml-core/azureml.core.environment(class)) objektumot kell használnia. Vegye figyelembe, hogy ha egyéni környezetet határoz meg, akkor a >= 1.0.45 verzióval rendelkező azureml kell hozzáadnia pip-függőségként. Ez a csomag tartalmazza a modell webszolgáltatásként való üzemeltetéséhez szükséges funkciókat. Az alábbi példa bemutatja, hogyan hozható létre egy környezeti objektum, és hogyan használhatja azt egy következtetési konfigurációval:
>
> ```python
> from azureml.core.environment import Environment
> from azureml.core.conda_dependencies import CondaDependencies
> from azureml.core.model import InferenceConfig
>
> # Create an environment and add conda dependencies to it
> myenv = Environment(name="myenv")
> # Enable Docker based environment
> myenv.docker.enabled = True
> # Build conda dependencies
> myenv.python.conda_dependencies = CondaDependencies.create(conda_packages=['scikit-learn'],
>                                                            pip_packages=['azureml-defaults'])
> inference_config = InferenceConfig(entry_script="score.py", environment=myenv)
> ```

További információ a környezetekről: [környezetek létrehozása és kezelése képzéshez és üzembe helyezéshez](how-to-use-environments.md).

További információ a konfigurációval kapcsolatban: [modellek üzembe helyezése Azure Machine Learningsal](how-to-deploy-and-where.md).

> [!IMPORTANT]
> A Azure App Service való telepítésekor nem kell létrehoznia __központi telepítési konfigurációt__.

## <a name="create-the-image"></a>A rendszerkép létrehozása

A Azure App Service üzembe helyezett Docker-rendszerkép létrehozásához használja a [Model. package csomagot](/python/api/azureml-core/azureml.core.model.model). A következő kódrészlet bemutatja, hogyan hozhat létre egy új rendszerképet a modellből és a következtetések konfigurálásával:

> [!NOTE]
> A kódrészlet feltételezi, hogy `model` egy regisztrált modellt tartalmaz, amely `inference_config` tartalmazza a következtetési környezet konfigurációját. További információ: [modellek üzembe helyezése Azure Machine Learningsal](how-to-deploy-and-where.md).

```python
from azureml.core import Model

package = Model.package(ws, [model], inference_config)
package.wait_for_creation(show_output=True)
# Display the package location/ACR path
print(package.location)
```

Ekkor `show_output=True` megjelenik a Docker-létrehozási folyamat kimenete. A folyamat befejeződése után a rendszerkép a munkaterülethez tartozó Azure Container Registryban lett létrehozva. A rendszerkép felépítése után megjelenik a Azure Container Registry helye. A visszaadott hely formátuma `<acrinstance>.azurecr.io/package@sha256:<imagename>` . Például: `myml08024f78fd10.azurecr.io/package@sha256:20190827151241`.

> [!IMPORTANT]
> Mentse a hely adatait, ahogy azt a lemezkép telepítésekor használják.

## <a name="deploy-image-as-a-web-app"></a>Rendszerkép üzembe helyezése webalkalmazásként

1. Használja az alábbi parancsot a rendszerképet tartalmazó Azure Container Registry bejelentkezési hitelesítő adatainak beszerzéséhez. Cserélje le `<acrinstance>` a elemet a korábban visszaadott értékre `package.location` :

    ```azurecli-interactive
    az acr credential show --name <myacr>
    ```

    A parancs kimenete a következő JSON-dokumentumhoz hasonló:

    ```json
    {
    "passwords": [
        {
        "name": "password",
        "value": "Iv0lRZQ9762LUJrFiffo3P4sWgk4q+nW"
        },
        {
        "name": "password2",
        "value": "=pKCxHatX96jeoYBWZLsPR6opszr==mg"
        }
    ],
    "username": "myml08024f78fd10"
    }
    ```

    Mentse a __Felhasználónév__ és az egyik __jelszó__ értékét.

1. Ha még nem rendelkezik erőforráscsoport-vagy app Service-csomaggal a szolgáltatás telepítéséhez, a következő parancsok azt mutatják be, hogyan hozható létre egyszerre:

    ```azurecli-interactive
    az group create --name myresourcegroup --location "West Europe"
    az appservice plan create --name myplanname --resource-group myresourcegroup --sku B1 --is-linux
    ```

    Ebben a példában egy __alapszintű__ árképzési szintet ( `--sku B1` ) használunk.

    > [!IMPORTANT]
    > A Azure Machine Learning által létrehozott rendszerképek Linux rendszert használnak, ezért a `--is-linux` paramétert kell használnia.

1. A webalkalmazás létrehozásához használja a következő parancsot. Cserélje le `<app-name>` a nevet a használni kívánt névre. Cserélje `<acrinstance>` le `<imagename>` a és a értéket a korábban visszaadott értékek közül `package.location` :

    ```azurecli-interactive
    az webapp create --resource-group myresourcegroup --plan myplanname --name <app-name> --deployment-container-image-name <acrinstance>.azurecr.io/package@sha256:<imagename>
    ```

    Ez a parancs a következő JSON-dokumentumhoz hasonló adatokat ad vissza:

    ```json
    {
    "adminSiteName": null,
    "appServicePlanName": "myplanname",
    "geoRegion": "West Europe",
    "hostingEnvironmentProfile": null,
    "id": "/subscriptions/0000-0000/resourceGroups/myResourceGroup/providers/Microsoft.Web/serverfarms/myplanname",
    "kind": "linux",
    "location": "West Europe",
    "maximumNumberOfWorkers": 1,
    "name": "myplanname",
    < JSON data removed for brevity. >
    "targetWorkerSizeId": 0,
    "type": "Microsoft.Web/serverfarms",
    "workerTierName": null
    }
    ```

    > [!IMPORTANT]
    > Ezen a ponton a webalkalmazás létrejött. Mivel azonban nem biztosította a hitelesítő adatokat a rendszerképet tartalmazó Azure Container Registry számára, a webalkalmazás nem aktív. A következő lépésben megadja a tároló-beállításjegyzék hitelesítési adatait.

1. A következő parancs használatával biztosíthatja a webalkalmazásnak a tároló-beállításjegyzék eléréséhez szükséges hitelesítő adatokat. Cserélje le `<app-name>` a nevet a használni kívánt névre. Cserélje `<acrinstance>` le `<imagename>` a és a értéket a korábban visszaadott értékekre `package.location` . Cserélje `<username>` le `<password>` a és a értékét a korábban beolvasott ACR bejelentkezési adatokra:

    ```azurecli-interactive
    az webapp config container set --name <app-name> --resource-group myresourcegroup --docker-custom-image-name <acrinstance>.azurecr.io/package@sha256:<imagename> --docker-registry-server-url https://<acrinstance>.azurecr.io --docker-registry-server-user <username> --docker-registry-server-password <password>
    ```

    Ez a parancs a következő JSON-dokumentumhoz hasonló adatokat ad vissza:

    ```json
    [
    {
        "name": "WEBSITES_ENABLE_APP_SERVICE_STORAGE",
        "slotSetting": false,
        "value": "false"
    },
    {
        "name": "DOCKER_REGISTRY_SERVER_URL",
        "slotSetting": false,
        "value": "https://myml08024f78fd10.azurecr.io"
    },
    {
        "name": "DOCKER_REGISTRY_SERVER_USERNAME",
        "slotSetting": false,
        "value": "myml08024f78fd10"
    },
    {
        "name": "DOCKER_REGISTRY_SERVER_PASSWORD",
        "slotSetting": false,
        "value": null
    },
    {
        "name": "DOCKER_CUSTOM_IMAGE_NAME",
        "value": "DOCKER|myml08024f78fd10.azurecr.io/package@sha256:20190827195524"
    }
    ]
    ```

Ezen a ponton a webalkalmazás elkezdi betölteni a rendszerképet.

> [!IMPORTANT]
> A rendszerkép betöltése előtt több percet is igénybe vehet. A folyamat figyeléséhez használja a következő parancsot:
>
> ```azurecli-interactive
> az webapp log tail --name <app-name> --resource-group myresourcegroup
> ```
>
> Miután betöltötte a rendszerképet, és a hely aktív, a napló megjeleníti az állapotot jelző üzenetet `Container <container name> for site <app-name> initialized successfully and is ready to serve requests` .

A lemezkép központi telepítése után a következő paranccsal keresheti meg a gazdagépet:

```azurecli-interactive
az webapp show --name <app-name> --resource-group myresourcegroup
```

Ez a parancs a következő állomásnévhez hasonló adatokat ad vissza: `<app-name>.azurewebsites.net` . Használja ezt az értéket a szolgáltatás __alap URL-címének__ részeként.

## <a name="use-the-web-app"></a>A webalkalmazás használata

A modellre irányuló kérelmeket továbbító webszolgáltatás a következő helyen található: `{baseurl}/score` . Például: `https://<app-name>.azurewebsites.net/score`. A következő Python-kód azt mutatja be, hogyan lehet beküldeni az adott URL-címet és megjeleníteni a választ:

```python
import requests
import json

scoring_uri = "https://mywebapp.azurewebsites.net/score"

headers = {'Content-Type':'application/json'}

test_sample = json.dumps({'data': [
    [1,2,3,4,5,6,7,8,9,10],
    [10,9,8,7,6,5,4,3,2,1]
]})

response = requests.post(scoring_uri, data=test_sample, headers=headers)
print(response.status_code)
print(response.elapsed)
print(response.json())
```

## <a name="next-steps"></a>Következő lépések

* Megtudhatja, hogyan konfigurálhatja a webalkalmazást a Linux dokumentációjának [app Service](/azure/app-service/containers/) .
* További információ az automatikus skálázás az [Azure](../azure-monitor/autoscale/autoscale-get-started.md?toc=%2fazure%2fapp-service%2ftoc.json)-ban való használatáról.
* [Használjon TLS/SSL-tanúsítványt a Azure app Service](../app-service/configure-ssl-certificate-in-code.md).
* [Konfigurálja a app Service alkalmazást Azure Active Directory bejelentkezés használatára](../app-service/configure-authentication-provider-aad.md).
* [Webszolgáltatásként üzembe helyezett ML-modell felhasználása](how-to-consume-web-service.md)