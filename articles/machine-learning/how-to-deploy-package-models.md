---
title: Csomagmodellek
titleSuffix: Azure Machine Learning
description: Modell becsomagolása. A modelleket Docker-képként is lehet csomagolni, amelyet aztán le is tölthet, vagy létrehozhat egy Docker, és felhasználhatja a rendszerkép létrehozásához.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: gopalv
author: gvashishtha
ms.date: 07/31/2020
ms.topic: conceptual
ms.reviewer: larryfr
ms.custom: deploy
ms.openlocfilehash: 299eb316b534ddc9d5eee934cc15eae841276038
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "93312631"
---
# <a name="how-to-package-a-registered-model-with-docker"></a>Regisztrált modell csomagolása a Docker használatával

Ez a cikk bemutatja, hogyan lehet becsomagolni egy regisztrált Azure Machine Learning modellt a Docker használatával.

## <a name="prerequisites"></a>Előfeltételek

Ez a cikk feltételezi, hogy már betanított és regisztrált egy modellt a Machine learning-munkaterületen. Ha szeretné megismerni, hogyan lehet betanítani és regisztrálni a scikit-modelleket, [kövesse ezt az oktatóanyagot](how-to-train-scikit-learn.md).


## <a name="package-models"></a>Csomagmodellek

Bizonyos esetekben előfordulhat, hogy létre kell hoznia egy Docker-rendszerképet a modell üzembe helyezése nélkül (Ha például telepíteni szeretné a [Azure app Service](how-to-deploy-app-service.md)). Vagy előfordulhat, hogy le szeretné tölteni a rendszerképet, és egy helyi Docker-telepítésre kell futtatnia. Előfordulhat, hogy még le szeretné tölteni a rendszerkép létrehozásához használt fájlokat, megvizsgálhatja őket, módosíthatja azokat, és manuálisan is felépítheti a rendszerképet.

A modell csomagolása lehetővé teszi ezeket a dolgokat. Minden olyan eszközt becsomagol, amely a modell webszolgáltatásként való üzemeltetéséhez szükséges, és lehetővé teszi egy teljesen felépített Docker-rendszerkép vagy a létrehozásához szükséges fájlok letöltését. A modell csomagolásának két módja van:

**Csomagolt modell letöltése:** Töltsön le egy Docker-rendszerképet, amely tartalmazza a modellt és más, a webszolgáltatásként való üzemeltetéséhez szükséges fájlokat.

**Docker létrehozása:** A Docker-rendszerkép létrehozásához szükséges Docker, modell, bejegyzési parancsfájl és egyéb eszközök letöltése. Ezután megvizsgálhatja a fájlokat, vagy módosíthatja a módosításokat a rendszerkép helyi létrehozása előtt.

Mindkét csomag használható helyi Docker-rendszerkép lekérésére.

> [!TIP]
> A csomagok létrehozása hasonló a modellek üzembe helyezéséhez. Egy regisztrált modellt és egy következtetési konfigurációt használ.

> [!IMPORTANT]
> Egy teljesen felépített rendszerkép letöltéséhez vagy a rendszerkép helyi létrehozásához telepítenie kell a [Docker](https://www.docker.com) -t a fejlesztői környezetbe.

### <a name="download-a-packaged-model"></a>Csomagolt modell letöltése

Az alábbi példa létrehoz egy rendszerképet, amely regisztrálva van az Azure Container registryben a munkaterülethez:

```python
package = Model.package(ws, [model], inference_config)
package.wait_for_creation(show_output=True)
```

Miután létrehozta a csomagot, a használatával lekérheti `package.pull()` a rendszerképet a helyi Docker-környezetbe. A parancs kimenete megjeleníti a rendszerkép nevét. Például: 

`Status: Downloaded newer image for myworkspacef78fd10.azurecr.io/package:20190822181338`. 

A modell letöltése után a `docker images` parancs használatával listázhatja a helyi rendszerképeket:

```text
REPOSITORY                               TAG                 IMAGE ID            CREATED             SIZE
myworkspacef78fd10.azurecr.io/package    20190822181338      7ff48015d5bd        4 minutes ago       1.43 GB
```

Ha egy helyi tárolót szeretne elindítani a rendszerkép alapján, a következő parancs használatával indítson el egy nevesített tárolót a rendszerhéjból vagy a parancssorból. Cserélje le az `<imageid>` értéket a parancs által visszaadott rendszerkép-azonosítóra `docker images` .

```bash
docker run -p 6789:5001 --name mycontainer <imageid>
```

Ez a parancs elindítja a nevű rendszerkép legújabb verzióját `myimage` . Leképezi a 6789-es helyi portot a webszolgáltatást figyelő tárolóban lévő portra (5001). Emellett a nevet `mycontainer` a tárolóhoz rendeli, amely megkönnyíti a tároló leállítását. A tároló elindítása után elküldheti a kérelmeket a következőnek: `http://localhost:6789/score` .

### <a name="generate-a-dockerfile-and-dependencies"></a>Docker és függőségek létrehozása

Az alábbi példa bemutatja, hogyan töltheti le a rendszerképek helyi létrehozásához szükséges Docker, modellt és egyéb eszközöket. A `generate_dockerfile=True` paraméter azt jelzi, hogy a fájlokat nem teljesen felépített képként szeretné megadni.

```python
package = Model.package(ws, [model], inference_config, generate_dockerfile=True)
package.wait_for_creation(show_output=True)
# Download the package.
package.save("./imagefiles")
# Get the Azure container registry that the model/Dockerfile uses.
acr=package.get_container_registry()
print("Address:", acr.address)
print("Username:", acr.username)
print("Password:", acr.password)
```

Ez a kód letölti a rendszerképnek a könyvtárba való felépítéséhez szükséges fájlokat `imagefiles` . A mentett fájlokban található Docker egy Azure Container registryben tárolt alaprendszerképre hivatkozik. Amikor létrehoz egy rendszerképet a helyi Docker-telepítéshez, a-beállításjegyzékben való hitelesítéshez a címnek, a felhasználónevet és a jelszót kell használnia. A következő lépésekkel hozhatja létre a rendszerképet egy helyi Docker-telepítés használatával:

1. Egy rendszerhéjból vagy parancssori munkamenetből a következő paranccsal hitelesítheti a Docker-t az Azure Container Registry használatával. Cserélje `<address>` le `<username>` a, a és a `<password>` értéket a által lekért értékekre `package.get_container_registry()` .

    ```bash
    docker login <address> -u <username> -p <password>
    ```

2. A rendszerkép létrehozásához használja a következő parancsot. A helyére írja be annak a `<imagefiles>` könyvtárnak az elérési útját, amelyben `package.save()` a fájlokat mentette.

    ```bash
    docker build --tag myimage <imagefiles>
    ```

    Ezzel a paranccsal állítható be a rendszerkép neve `myimage` .

A rendszerkép felépítésének ellenőrzéséhez használja az `docker images` parancsot. A képet a következő `myimage` listában kell látni:

```text
REPOSITORY      TAG                 IMAGE ID            CREATED             SIZE
<none>          <none>              2d5ee0bf3b3b        49 seconds ago      1.43 GB
myimage         latest              739f22498d64        3 minutes ago       1.43 GB
```

Ha új tárolót szeretne elindítani a rendszerkép alapján, használja a következő parancsot:

```bash
docker run -p 6789:5001 --name mycontainer myimage:latest
```

Ez a parancs elindítja a nevű rendszerkép legújabb verzióját `myimage` . Leképezi a 6789-es helyi portot a webszolgáltatást figyelő tárolóban lévő portra (5001). Emellett a nevet `mycontainer` a tárolóhoz rendeli, amely megkönnyíti a tároló leállítását. A tároló elindítása után elküldheti a kérelmeket a következőnek: `http://localhost:6789/score` .

### <a name="example-client-to-test-the-local-container"></a>Példa az ügyfélre a helyi tároló teszteléséhez

A következő kód egy olyan Python-ügyfélre mutat példát, amely a tárolóval használható:

```python
import requests
import json

# URL for the web service.
scoring_uri = 'http://localhost:6789/score'

# Two sets of data to score, so we get two results back.
data = {"data":
        [
            [ 1,2,3,4,5,6,7,8,9,10 ],
            [ 10,9,8,7,6,5,4,3,2,1 ]
        ]
        }
# Convert to JSON string.
input_data = json.dumps(data)

# Set the content type.
headers = {'Content-Type': 'application/json'}

# Make the request and display the response.
resp = requests.post(scoring_uri, input_data, headers=headers)
print(resp.text)
```

Más programozási nyelveken található ügyfelek esetében lásd: [webszolgáltatásként üzembe helyezett modellek felhasználása](how-to-consume-web-service.md).

### <a name="stop-the-docker-container"></a>A Docker-tároló leállítása

A tároló leállításához használja a következő parancsot egy másik rendszerhéjból vagy parancssorból:

```bash
docker kill mycontainer
```

## <a name="next-steps"></a>Következő lépések

* [Sikertelen üzembe helyezés hibaelhárítása](how-to-troubleshoot-deployment.md)
* [Üzembe helyezés az Azure Kubernetes Service-ben](how-to-deploy-azure-kubernetes-service.md)
* [Ügyfélalkalmazások létrehozása webszolgáltatások felhasználásához](how-to-consume-web-service.md)
* [Webszolgáltatás frissítése](how-to-deploy-update-web-service.md)
* [Modell üzembe helyezése egyéni Docker-rendszerkép használatával](how-to-deploy-custom-docker-image.md)
* [TLS használata webszolgáltatás védelméhez az Azure Machine Learning szolgáltatás segítségével](how-to-secure-web-service.md)
* [A Azure Machine Learning modellek monitorozása a Application Insights](how-to-enable-app-insights.md)
* [Adatok gyűjtése a termelési modellekhez](how-to-enable-data-collection.md)
* [Esemény-riasztások és eseményindítók létrehozása a modell üzembe helyezéséhez](how-to-use-event-grid.md)
