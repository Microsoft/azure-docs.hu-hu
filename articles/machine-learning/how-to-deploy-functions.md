---
title: Ml modellek üzembe helyezése Azure Functions alkalmazásokban (előzetes verzió)
titleSuffix: Azure Machine Learning
description: Megtudhatja, hogyan használhatja a Azure Machine Learning a modellek webszolgáltatásként való becsomagolásához és üzembe helyezéséhez egy Azure Functions alkalmazásban.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: vaidyas
author: vaidya-s
ms.reviewer: larryfr
ms.date: 03/06/2020
ms.topic: conceptual
ms.custom: how-to, racking-python, devx-track-azurecli
ms.openlocfilehash: abb38ebbdacdf4f153148fbe121e54ede88f066a
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102519265"
---
# <a name="deploy-a-machine-learning-model-to-azure-functions-preview"></a>Gépi tanulási modell üzembe helyezése Azure Functions (előzetes verzió)


Megtudhatja, hogyan helyezhet üzembe egy modellt Azure Machine Learning alkalmazásból Azure Functions.

> [!IMPORTANT]
> Noha a Azure Machine Learning és az Azure Functions általánosan elérhető, az Machine Learning Service for functions-modell csomagjának lehetősége előzetes verzióban érhető el.

A Azure Machine Learning segítségével Docker-rendszerképeket hozhat létre a képzett gépi tanulási modellekből. Azure Machine Learning mostantól az előzetes verzió funkciójának használatával felépítheti ezeket a gépi tanulási modelleket a Function appsbe, amelyek üzembe helyezhetők [a Azure Functionsban](../azure-functions/functions-deployment-technologies.md#docker-container).

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

A telepítés előtt meg kell határoznia, hogy mire van szükség a modell webszolgáltatásként való futtatásához. Az alábbi lista a központi telepítéshez szükséges alapvető elemeket ismerteti:

* Egy __bejegyzési parancsfájl__. Ez a szkript fogadja a kéréseket, a modell használatával szerzi a kérést, és visszaadja az eredményeket.

    > [!IMPORTANT]
    > A bejegyzési parancsfájl a modellre jellemző. meg kell ismernie a bejövő kérelmek adatainak formátumát, a modell által várt adatformátumot, valamint az ügyfeleknek visszaadott adatformátumot.
    >
    > Ha a kérelem adatai olyan formátumban vannak, amely nem használható a modellben, a parancsfájl elfogadható formátumba alakíthatja át. A válasz is át lehet alakítani, mielőtt visszatért az ügyfélhez.
    >
    > Alapértelmezés szerint a függvények csomagolásakor a rendszer a bemenetet szövegként kezeli. Ha érdekli a bemenet (például blob-eseményindítók) nyers bájtjainak használata, a [AMLRequest használatával fogadja el a nyers adatokat](./how-to-deploy-advanced-entry-script.md#binary-data).

További információ a beléptetési parancsfájlról: [pontozási kód definiálása](./how-to-deploy-and-where.md#define-an-entry-script)

* **Függőségek**, például segítő parancsfájlok vagy Python/Conda csomagok, amelyek a belépési parancsfájl vagy modell futtatásához szükségesek

Ezek az entitások egy __következtetési konfigurációba__ vannak ágyazva. A következtetési konfiguráció a bejegyzés parancsfájljára és további függőségekre hivatkozik.

> [!IMPORTANT]
> Ha Azure Functions-vel való használatra vonatkozó következtetési konfigurációt hoz létre, [környezeti](/python/api/azureml-core/azureml.core.environment%28class%29) objektumot kell használnia. Vegye figyelembe, hogy ha egyéni környezetet határoz meg, akkor a >= 1.0.45 verzióval rendelkező azureml kell hozzáadnia pip-függőségként. Ez a csomag tartalmazza a modell webszolgáltatásként való üzemeltetéséhez szükséges funkciókat. Az alábbi példa bemutatja, hogyan hozható létre egy környezeti objektum, és hogyan használhatja azt egy következtetési konfigurációval:
>
> ```python
> from azureml.core.environment import Environment
> from azureml.core.conda_dependencies import CondaDependencies
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
> A függvények telepítésekor nem kell létrehoznia __központi telepítési konfigurációt__.

## <a name="install-the-sdk-preview-package-for-functions-support"></a>Az SDK előzetes csomagjának telepítése a függvények támogatásához

Azure Functions csomagok létrehozásához telepítenie kell az SDK előzetes csomagját.

```bash
pip install azureml-contrib-functions
```

## <a name="create-the-image"></a>A rendszerkép létrehozása

A Azure Functions rendszerbe állított Docker-rendszerkép létrehozásához használja a [azureml... a. functions. functions. package](/python/api/azureml-contrib-functions/azureml.contrib.functions) vagy az adott Package függvényt a használni kívánt triggerhez. A következő kódrészlet azt mutatja be, hogyan hozhat létre egy új csomagot a modellből és a következtetések konfigurációjában a blob triggerrel:

> [!NOTE]
> A kódrészlet feltételezi, hogy `model` egy regisztrált modellt tartalmaz, amely `inference_config` tartalmazza a következtetési környezet konfigurációját. További információ: [modellek üzembe helyezése Azure Machine Learningsal](how-to-deploy-and-where.md).

```python
from azureml.contrib.functions import package
from azureml.contrib.functions import BLOB_TRIGGER
blob = package(ws, [model], inference_config, functions_enabled=True, trigger=BLOB_TRIGGER, input_path="input/{blobname}.json", output_path="output/{blobname}_out.json")
blob.wait_for_creation(show_output=True)
# Display the package location/ACR path
print(blob.location)
```

Ekkor `show_output=True` megjelenik a Docker-létrehozási folyamat kimenete. A folyamat befejeződése után a rendszerkép a munkaterülethez tartozó Azure Container Registryban lett létrehozva. A rendszerkép felépítése után megjelenik a Azure Container Registry helye. A visszaadott hely formátuma `<acrinstance>.azurecr.io/package@sha256:<imagename>` .

> [!NOTE]
> A függvények csomagolása jelenleg támogatja a HTTP-eseményindítókat, a blob-eseményindítókat és a Service Bus-eseményindítókat. További információ az eseményindítókkal kapcsolatban: [Azure functions kötések](../azure-functions/functions-bindings-storage-blob-trigger.md#blob-name-patterns).

> [!IMPORTANT]
> Mentse a hely adatait, ahogy azt a lemezkép telepítésekor használják.

## <a name="deploy-image-as-a-web-app"></a>Rendszerkép üzembe helyezése webalkalmazásként

1. Használja az alábbi parancsot a rendszerképet tartalmazó Azure Container Registry bejelentkezési hitelesítő adatainak beszerzéséhez. Cserélje le `<myacr>` a elemet a korábban visszaadott értékre `blob.location` : 

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

    Ebben a példában egy _Linux alapszintű_ díjszabási szintet ( `--sku B1` ) használunk.

    > [!IMPORTANT]
    > A Azure Machine Learning által létrehozott rendszerképek Linux rendszert használnak, ezért a `--is-linux` paramétert kell használnia.

1. Hozza létre a web Job Storage szolgáltatáshoz használandó Storage-fiókot, és szerezze be a kapcsolati karakterláncát. Cserélje le `<webjobStorage>` a nevet a használni kívánt névre.

    ```azurecli-interactive
    az storage account create --name <webjobStorage> --location westeurope --resource-group myresourcegroup --sku Standard_LRS
    ```
    ```azurecli-interactive
    az storage account show-connection-string --resource-group myresourcegroup --name <webJobStorage> --query connectionString --output tsv
    ```

1. A Function alkalmazás létrehozásához használja a következő parancsot. Cserélje le `<app-name>` a nevet a használni kívánt névre. Cserélje `<acrinstance>` le `<imagename>` a és a értéket a korábban visszaadott értékekre `package.location` . Cserélje le az `<webjobStorage>` elemet az előző lépésben szereplő Storage-fiók nevére:

    ```azurecli-interactive
    az functionapp create --resource-group myresourcegroup --plan myplanname --name <app-name> --deployment-container-image-name <acrinstance>.azurecr.io/package:<imagename> --storage-account <webjobStorage>
    ```

    > [!IMPORTANT]
    > Ezen a ponton a Function alkalmazás létrejött. Mivel azonban nem biztosította a blob triggerhez vagy a hitelesítő adatokhoz tartozó kapcsolódási karakterláncot a képet tartalmazó Azure Container Registryhoz, a Function alkalmazás nem aktív. A következő lépésekben meg kell adnia a kapcsolatok karakterláncát és a tároló-beállításjegyzék hitelesítési adatait. 

1. Hozza létre a blob trigger-tárolóhoz használandó Storage-fiókot, és szerezze be a kapcsolati karakterláncát. Cserélje le `<triggerStorage>` a nevet a használni kívánt névre.

    ```azurecli-interactive
    az storage account create --name <triggerStorage> --location westeurope --resource-group myresourcegroup --sku Standard_LRS
    ```
    ```azurecli-interactive
    az storage account show-connection-string --resource-group myresourcegroup --name <triggerStorage> --query connectionString --output tsv
    ```
    Jegyezze fel ezt a kapcsolódási karakterláncot a Function alkalmazás számára történő biztosításhoz. Később fogjuk használni, amikor a kért `<triggerConnectionString>`

1. Hozza létre a tárolót a bemenethez és a kimenethez a Storage-fiókban. Cserélje le `<triggerConnectionString>` a értéket a korábban visszaadott kapcsolatok sztringre:

    ```azurecli-interactive
    az storage container create -n input --connection-string <triggerConnectionString>
    ```
    ```azurecli-interactive
    az storage container create -n output --connection-string <triggerConnectionString>
    ```

1. Ha az trigger-kapcsolódási karakterláncot a Function alkalmazáshoz szeretné rendelni, használja a következő parancsot. Cserélje le a helyére a `<app-name>` Function alkalmazás nevét. Cserélje le `<triggerConnectionString>` a értéket a korábban visszaadott kapcsolatok sztringre:

    ```azurecli-interactive
    az functionapp config appsettings set --name <app-name> --resource-group myresourcegroup --settings "TriggerConnectionString=<triggerConnectionString>"
    ```
1. A létrehozott tárolóhoz tartozó címkét a következő parancs használatával kell lekérnie. Cserélje le a elemet `<username>` a korábban a tároló-beállításjegyzékből visszaadott felhasználónévre:

    ```azurecli-interactive
    az acr repository show-tags --repository package --name <username> --output tsv
    ```
    Mentse a visszaadott értéket, amelyet a `imagetag` következő lépésben fog használni.

1. A következő parancs használatával biztosíthatja a Function alkalmazást a tároló-beállításjegyzék eléréséhez szükséges hitelesítő adatokkal. Cserélje le a helyére a `<app-name>` Function alkalmazás nevét. Cserélje le a és a értéket az az `<acrinstance>` `<imagetag>` előző lépésben megadott CLI-hívás értékeire. Cserélje `<username>` le `<password>` a és a értékét a korábban beolvasott ACR bejelentkezési adatokra:

    ```azurecli-interactive
    az functionapp config container set --name <app-name> --resource-group myresourcegroup --docker-custom-image-name <acrinstance>.azurecr.io/package:<imagetag> --docker-registry-server-url https://<acrinstance>.azurecr.io --docker-registry-server-user <username> --docker-registry-server-password <password>
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
        "value": "DOCKER|myml08024f78fd10.azurecr.io/package:20190827195524"
    }
    ]
    ```

Ezen a ponton a Function alkalmazás elkezdi betölteni a rendszerképet.

> [!IMPORTANT]
> A rendszerkép betöltése előtt több percet is igénybe vehet. Az Azure Portalon nyomon követheti a folyamat előrehaladását.

## <a name="test-the-deployment"></a>Az üzemelő példány tesztelése

Miután betöltötte a rendszerképet, és az alkalmazás elérhetővé válik, a következő lépésekkel aktiválhatja az alkalmazást:

1. Hozzon létre egy szövegfájlt, amely tartalmazza azokat az adatok, amelyeket a score.py-fájl elvár. A következő példa egy olyan score.py fog működni, amely 10 számból álló tömböt vár:

    ```json
    {"data": [[1, 2, 3, 4, 5, 6, 7, 8, 9, 10], [10, 9, 8, 7, 6, 5, 4, 3, 2, 1]]}
    ```

    > [!IMPORTANT]
    > Az adatok formátuma attól függ, hogy mit vár a score.py és a modell.

2. A következő parancs használatával töltse fel ezt a fájlt a korábban létrehozott trigger Storage-blob bemeneti tárolójába. Cserélje le az `<file>` nevet az adathalmazt tartalmazó fájl nevére. Cserélje le `<triggerConnectionString>` a értéket a korábban visszaadott összekapcsolási sztringre. Ebben a példában a a `input` korábban létrehozott bemeneti tároló neve. Ha más nevet használt, cserélje le ezt az értéket:

    ```azurecli-interactive
    az storage blob upload --container-name input --file <file> --name <file> --connection-string <triggerConnectionString>
    ```

    A parancs kimenete a következő JSON-hoz hasonló:

    ```json
    {
    "etag": "\"0x8D7C21528E08844\"",
    "lastModified": "2020-03-06T21:27:23+00:00"
    }
    ```

3. A függvény által létrehozott kimenet megtekintéséhez használja az alábbi parancsot a létrehozott kimeneti fájlok listázásához. Cserélje le `<triggerConnectionString>` a értéket a korábban visszaadott összekapcsolási sztringre. Ebben a példában `output` a a korábban létrehozott kimeneti tároló neve. Ha más nevet használt, cserélje le ezt az értéket:

    ```azurecli-interactive
    az storage blob list --container-name output --connection-string <triggerConnectionString> --query '[].name' --output tsv
    ```

    A parancs kimenete hasonló a következőhöz: `sample_input_out.json` .

4. A fájl letöltéséhez és a tartalom vizsgálatához használja a következő parancsot. Cserélje le az értéket az `<file>` előző parancs által visszaadott fájlnévre. Cserélje le `<triggerConnectionString>` a értéket a korábban visszaadott kapcsolatok sztringre: 

    ```azurecli-interactive
    az storage blob download --container-name output --file <file> --name <file> --connection-string <triggerConnectionString>
    ```

    A parancs befejeződése után nyissa meg a fájlt. A modell által visszaadott adathalmazokat tartalmazza.

A blob-eseményindítók használatával kapcsolatos további információkért tekintse meg az [Azure Blob Storage által aktivált függvények létrehozását](../azure-functions/functions-create-storage-blob-triggered-function.md) ismertető cikket.

## <a name="next-steps"></a>Következő lépések

* Ismerje meg, hogyan konfigurálhatja a functions alkalmazást a [functions](../azure-functions/functions-create-function-linux-custom-image.md) dokumentációjában.
* További információ a blob Storage eseményindítók [Azure Blob Storage-kötésekről](../azure-functions/functions-bindings-storage-blob.md).
* [A modell üzembe helyezése Azure app Service](how-to-deploy-app-service.md).
* [Webszolgáltatásként üzembe helyezett ML-modell felhasználása](how-to-consume-web-service.md)
* [API-referencia](/python/api/azureml-contrib-functions/azureml.contrib.functions)
