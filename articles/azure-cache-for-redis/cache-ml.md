---
title: Gépi tanulási modell üzembe helyezése az Azure cache for Redis Azure Functions
description: Ebben a cikkben egy modellt fog üzembe helyezni a Azure Machine Learning alkalmazásban, Azure Functions egy Azure cache for Redis-példányt használva. Az Azure cache for Redis rendkívül nagy teljesítményű és skálázható – ha Azure Machine Learning modellel párosítja, alacsony késést és nagy adatátviteli sebességet biztosít az alkalmazásában.
author: curib
ms.author: cauribeg
ms.service: cache
ms.topic: conceptual
ms.date: 09/30/2020
ms.openlocfilehash: ec8943bc73cac2020350dd4916f040f031cd842b
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/09/2021
ms.locfileid: "102499696"
---
# <a name="deploy-a-machine-learning-model-to-azure-functions-with-azure-cache-for-redis"></a>Gépi tanulási modell üzembe helyezése az Azure cache for Redis Azure Functions 

Ebben a cikkben egy modellt fog üzembe helyezni a Azure Machine Learning alkalmazásban, Azure Functions egy Azure cache for Redis-példányt használva.  

Az Azure cache for Redis rendkívül nagy teljesítményű és skálázható – ha Azure Machine Learning modellel párosítja, alacsony késést és nagy adatátviteli sebességet biztosít az alkalmazásában. Néhány forgatókönyv, ahol a gyorsítótár különösen hasznos, amikor az adatokra hivatkozik, és a modell tényleges következtetéseit eredményezi. Mindkét esetben a metaadatokat vagy az eredményeket memóriában tárolja a rendszer, ami nagyobb teljesítményt eredményez. 

> [!NOTE]
> Noha a Azure Machine Learning és az Azure Functions általánosan elérhető, az Machine Learning Service for functions-modell csomagjának lehetősége előzetes verzióban érhető el.  
>

## <a name="prerequisites"></a>Előfeltételek
* Azure-előfizetés – [hozzon létre egyet ingyen](https://azure.microsoft.com/free/).
* Egy Azure Machine Learning-munkaterület. További információt a [Munkaterület létrehozása](../machine-learning/how-to-manage-workspace.md) című cikkben talál.
* [Azure CLI](/cli/azure/install-azure-cli)-vel.
* A munkaterületen regisztrált, betanított gépi tanulási modell. Ha nem rendelkezik modellel, használja a [képbesorolási oktatóanyagot: a betanítási modell](../machine-learning/tutorial-train-models-with-aml.md) betanítása és regisztrálása.

> [!IMPORTANT]
> A cikkben szereplő kódrészletek azt feltételezik, hogy a következő változókat állította be:
>
> * `ws` – Azure Machine Learning munkaterület.
> * `model` – A rendszerbe állított regisztrált modell.
> * `inference_config` – A modellre vonatkozó következtetési konfiguráció.
>
> A változók beállításával kapcsolatos további információkért lásd: [modellek üzembe helyezése Azure Machine Learningsal](../machine-learning/how-to-deploy-and-where.md).

## <a name="create-an-azure-cache-for-redis-instance"></a>Azure cache létrehozása a Redis-példányhoz 
Az alapszintű, standard vagy prémium szintű gyorsítótár-példánnyal Azure Functions gépi tanulási modellt telepíthet. Gyorsítótár-példány létrehozásához kövesse az alábbi lépéseket.  

1. Lépjen a Azure Portal kezdőlapjára, vagy nyissa meg a Sidebar menüt, majd válassza az **erőforrás létrehozása** lehetőséget. 
   
1. Az **új** lapon válassza az **adatbázisok** lehetőséget, majd válassza az Azure cache lehetőséget a **Redis számára**.

    :::image type="content" source="media/cache-private-link/2-select-cache.png" alt-text="Válassza ki az Azure cache-t a Redis.":::
   
1. Az **új Redis cache** lapon adja meg az új gyorsítótár beállításait.
   
   | Beállítás      | Ajánlott érték  | Leírás |
   | ------------ |  ------- | -------------------------------------------------- |
   | **DNS-név** | Adjon meg egy globálisan egyedi nevet. | A gyorsítótár nevének 1 és 63 karakter közötti sztringnek kell lennie, amely csak számokat, betűket vagy kötőjeleket tartalmaz. A névnek számmal vagy betűvel kell kezdődnie és végződnie, és nem tartalmazhat egymást követő kötőjeleket. A gyorsítótár-példány *állomásneve* a *\<DNS name> . Redis.cache.Windows.net* lesz. | 
   | **Előfizetés** | Legördülő menüből válassza ki az előfizetését. | Az előfizetés, amely alatt létre kell hoznia ezt az új Azure cache-t a Redis-példányhoz. | 
   | **Erőforráscsoport** | Legördülő listából válassza ki az erőforráscsoportot, vagy válassza az **új létrehozása** elemet, és adjon meg egy új erőforráscsoport-nevet. | Azon erőforráscsoport neve, amelyben létre szeretné hozni a gyorsítótárat és az egyéb erőforrásokat. Az összes alkalmazás-erőforrás egy erőforráscsoporthoz való elhelyezésével könnyedén kezelheti és törölheti azokat. | 
   | **Hely** | Legördülő menüből válassza ki a helyet. | Válasszon ki egy [régiót](https://azure.microsoft.com/regions/) a többi olyan szolgáltatás közelében, amely a gyorsítótárat fogja használni. |
   | **Tarifacsomag** | Legördülő menüből válassza ki a [díjszabási szintet](https://azure.microsoft.com/pricing/details/cache/). |  A tarifacsomag határozza meg a gyorsítótár méretét, teljesítményét és elérhető funkcióit. További információ: [Azure cache for Redis – áttekintés](cache-overview.md). |

1. Válassza a **hálózatkezelés** fület, vagy kattintson a lap alján található **hálózatkezelés** gombra.

1. A **hálózatkezelés** lapon válassza ki a kapcsolati módszert.

1. Válassza a **Next (speciális** ) lapot, vagy kattintson a lap alján található **Tovább: speciális** gombra.

1. Az alapszintű vagy standard gyorsítótár-példány **speciális** lapján jelölje be a váltás engedélyezése jelölőnégyzetet, ha engedélyezni szeretné a nem TLS portot.

1. A Premium cache-példány **speciális** lapján konfigurálja a nem TLS port, a fürtözés és az adatmegőrzés beállításait.

1. Válassza a **Next: Tags (tovább** ) lapot, vagy kattintson a **Next: Tags (tovább** ) gombra a lap alján.

1. Szükség esetén a **címkék** lapon adja meg a nevet és az értéket, ha az erőforrást kategorizálni szeretné. 

1. Válassza az **Áttekintés + létrehozás** lehetőséget. A felülvizsgálat + létrehozás lapon az Azure ellenőrzi a konfigurációt.

1. Ha megjelenik az átadott zöld érvényesítés üzenet, válassza a **Létrehozás** lehetőséget.

Eltarthat egy ideig a gyorsítótár létrehozásához. Nyomon követheti a folyamat előrehaladását az Azure cache Redis **– Áttekintés** oldalon. Ha az **állapot** **futásra** mutat, a gyorsítótár készen áll a használatra. 

## <a name="prepare-for-deployment"></a>Felkészülés az üzembe helyezésre

A telepítés előtt meg kell határoznia, hogy mire van szükség a modell webszolgáltatásként való futtatásához. Az alábbi lista a központi telepítéshez szükséges alapvető elemeket ismerteti:

* Egy __bejegyzési parancsfájl__. Ez a szkript fogadja a kéréseket, a modell használatával szerzi a kérést, és visszaadja az eredményeket.

    > [!IMPORTANT]
    > A bejegyzési parancsfájl a modellre jellemző. meg kell ismernie a bejövő kérelmek adatainak formátumát, a modell által várt adatformátumot, valamint az ügyfeleknek visszaadott adatformátumot.
    >
    > Ha a kérelem adatai olyan formátumban vannak, amely nem használható a modellben, a parancsfájl elfogadható formátumba alakíthatja át. A választ is át lehet alakítani, mielőtt visszaadná azt az ügyfélnek.
    >
    > Alapértelmezés szerint a függvények csomagolásakor a rendszer a bemenetet szövegként kezeli. Ha érdekli a bemenet (például blob-eseményindítók) nyers bájtjainak használata, a [AMLRequest használatával fogadja el a nyers adatokat](../machine-learning/how-to-deploy-advanced-entry-script.md#binary-data).

A Run függvényhez ellenőrizze, hogy csatlakozik-e egy Redis-végponthoz.

```python
import json
import numpy as np
import os
import redis
from sklearn.externals import joblib

def init():
    global model
    global azrediscache
    azrediscache = redis.StrictRedis(host='<host_url>', port=6380, password="<access_key>", ssl=True)
    model_path = os.path.join(os.getenv('AZUREML_MODEL_DIR'), 'sklearn_mnist_model.pkl')
    model = joblib.load(model_path)

@input_schema('data', NumpyParameterType(input_sample))
@output_schema(NumpyParameterType(output_sample))
def run(data):
    try:
        input = azrediscache.get(data)
        result = model.predict(input)
        data = np.array(json.loads(data))
        result = model.predict(data)
        # You can return any data type, as long as it is JSON serializable.
        return result.tolist()
    except Exception as e:
        error = str(e)
        return error
```

További információ a belépési parancsfájlról: [pontozási kód definiálása.](../machine-learning/how-to-deploy-and-where.md?tabs=python#define-an-entry-script)

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
>                                                            pip_packages=['azureml-defaults', 'redis'])
> inference_config = InferenceConfig(entry_script="score.py", environment=myenv)
> ```

További információ a környezetekről: [környezetek létrehozása és kezelése képzéshez és üzembe helyezéshez](../machine-learning/how-to-use-environments.md).

További információ a konfigurációval kapcsolatban: [modellek üzembe helyezése Azure Machine Learningsal](../machine-learning/how-to-deploy-and-where.md?tabs=python#define-an-inference-configuration).

> [!IMPORTANT]
> A függvények telepítésekor nem kell létrehoznia __központi telepítési konfigurációt__.

## <a name="install-the-sdk-preview-package-for-functions-support"></a>Az SDK előzetes csomagjának telepítése a függvények támogatásához

Azure Functions csomagok létrehozásához telepítenie kell az SDK előzetes csomagját.

```bash
pip install azureml-contrib-functions
```

## <a name="create-the-image"></a>A rendszerkép létrehozása

A Azure Functions rendszerbe állított Docker-rendszerkép létrehozásához használja a [azureml... a. functions. functions. package](/python/api/azureml-contrib-functions/azureml.contrib.functions) vagy az adott Package függvényt a használni kívánt triggerhez. A következő kódrészlet bemutatja, hogyan hozhat létre egy új csomagot HTTP-triggerrel a modellből és a következtetések konfigurálásával:

> [!NOTE]
> A kódrészlet feltételezi, hogy `model` egy regisztrált modellt tartalmaz, amely `inference_config` tartalmazza a következtetési környezet konfigurációját. További információ: [modellek üzembe helyezése Azure Machine Learningsal](../machine-learning/how-to-deploy-and-where.md).

```python
from azureml.contrib.functions import package
from azureml.contrib.functions import HTTP_TRIGGER
model_package = package(ws, [model], inference_config, functions_enabled=True, trigger=HTTP_TRIGGER)
model_package.wait_for_creation(show_output=True)
# Display the package location/ACR path
print(model_package.location)
```

Ekkor `show_output=True` megjelenik a Docker-létrehozási folyamat kimenete. A folyamat befejeződése után a rendszerkép a munkaterülethez tartozó Azure Container Registryban lett létrehozva. A rendszerkép felépítése után megjelenik a Azure Container Registry helye. A visszaadott hely formátuma `<acrinstance>.azurecr.io/package@sha256:<imagename>` .

> [!NOTE]
> A függvények csomagolása jelenleg támogatja a HTTP-eseményindítókat, a blob-eseményindítókat és a Service Bus-eseményindítókat. További információ az eseményindítókkal kapcsolatban: [Azure functions kötések](../azure-functions/functions-bindings-storage-blob-trigger.md#blob-name-patterns).

> [!IMPORTANT]
> Mentse a hely adatait, ahogy azt a lemezkép telepítésekor használják.

## <a name="deploy-image-as-a-web-app"></a>Rendszerkép üzembe helyezése webalkalmazásként

1. Használja az alábbi parancsot a rendszerképet tartalmazó Azure Container Registry bejelentkezési hitelesítő adatainak beszerzéséhez. Cserélje le `<myacr>` a elemet a korábban visszaadott értékre `package.location` : 

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
    > Ezen a ponton a Function alkalmazás létrejött. Mivel azonban nem biztosította a HTTP-triggerhez vagy a hitelesítő adatokhoz tartozó kapcsolódási karakterláncot a képet tartalmazó Azure Container Registryhoz, a Function alkalmazás nem aktív. A következő lépésekben meg kell adnia a kapcsolatok karakterláncát és a tároló-beállításjegyzék hitelesítési adatait. 

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
> A rendszerkép betöltése előtt több percet is igénybe vehet. A Azure Portal használatával figyelheti a folyamat előrehaladását.

## <a name="test-azure-functions-http-trigger"></a>Azure Functions HTTP-trigger tesztelése 

Ekkor futtatjuk és teszteljük a Azure Functions HTTP-triggert.

1. Nyissa meg a Function alkalmazást a Azure Portal.
1. A fejlesztő területen válassza a **Code + test** elemet. 
1. A jobb oldalon válassza a **bevitel** lapot. 
1. Kattintson a **Futtatás** gombra a Azure functions http-trigger teszteléséhez. 

Sikeresen üzembe helyezett egy modellt az Azure Machine Learning-ból, amely a Redis-példányhoz tartozó Azure cache-t használó Function-alkalmazás. A Redis készült Azure cache-ről az alábbi szakaszban található hivatkozásokra kattintva tudhat meg többet.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha azt tervezi, hogy a következő oktatóanyaggal folytatja, megtarthatja és újból felhasználhatja az ebben a rövid útmutatóban létrehozott erőforrásokat.

Ellenkező esetben, ha elkészült a gyors üzembe helyezéssel, törölheti az ebben a rövid útmutatóban létrehozott Azure-erőforrásokat a díjak elkerülése érdekében. 

> [!IMPORTANT]
> Az erőforráscsoport törlése nem vonható vissza. Az erőforráscsoport törlésekor a benne foglalt összes erőforrás véglegesen törlődik. Figyeljen arra, hogy ne töröljön véletlenül erőforráscsoportot vagy erőforrásokat. Ha a jelen minta üzemeltetését végző erőforrásokat egy meglévő, megtartani kívánt erőforrásokat tartalmazó erőforráscsoportban hozta létre, az erőforrásokat az erőforráscsoport törlése helyett külön-külön törölheti a megfelelő panelekről.

### <a name="to-delete-a-resource-group"></a>Erőforráscsoport törlése

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com), és válassza az **Erőforráscsoportok** elemet.

2. A **Szűrés név alapján** mezőbe írja be az erőforráscsoport nevét. Az erőforráscsoport eredménylistájában válassza a **...**, majd az **Erőforráscsoport törlése** lehetőséget.

A rendszer az erőforráscsoport törlésének megerősítését kéri. A megerősítéshez írja be az erőforráscsoport nevét, és válassza a **Törlést**.

A rendszer néhány pillanaton belül törli az erőforráscsoportot és annak erőforrásait.

## <a name="next-steps"></a>Következő lépések 

* További információ a [Redis készült Azure cache](./cache-overview.md) -ről
* Ismerje meg, hogyan konfigurálhatja a Function alkalmazást [a functions dokumentációjában](../azure-functions/functions-create-function-linux-custom-image.md) .
* [API-referencia](/python/api/azureml-contrib-functions/azureml.contrib.functions) 
* [Azure cache-t használó Python-alkalmazás létrehozása a Redis-hez](./cache-python-get-started.md)
