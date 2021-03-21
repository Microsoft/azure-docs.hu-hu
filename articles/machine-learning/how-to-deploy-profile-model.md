---
title: Profil-modell memória-és CPU-használat
titleSuffix: Azure Machine Learning
description: Az üzembe helyezés előtt megismerheti a modell profilját. A profilkészítés meghatározza a modell memória-és CPU-használatát.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: gopalv
author: gvashishtha
ms.date: 07/31/2020
ms.topic: conceptual
zone_pivot_groups: aml-control-methods
ms.reviewer: larryfr
ms.custom: deploy
ms.openlocfilehash: b9ae40b3d2673961f9b84ed702f18b25b79b6d0c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "93320404"
---
# <a name="profile-your-model-to-determine-resource-utilization"></a>A modell profiljának meghatározása az erőforrások kihasználtságának meghatározásához

Ez a cikk bemutatja, hogyan lehet profilt felvenni a gépi tanulás modelljébe annak meghatározásához, hogy mekkora CPU és memória szükséges a modell kiosztásához webszolgáltatásként való üzembe helyezéskor.

## <a name="prerequisites"></a>Előfeltételek

Ez a cikk feltételezi, hogy betanított és regisztrált egy modellt Azure Machine Learningsal. Tekintse meg a [minta oktatóanyagot](how-to-train-scikit-learn.md) , amely egy scikit-tanulási modell betanítását és regisztrálását Azure Machine learning.

## <a name="limitations"></a>Korlátozások

* A profilkészítés nem fog működni, ha a munkaterület Azure Container Registry (ACR) egy virtuális hálózat mögött van.

## <a name="run-the-profiler"></a>A Profiler futtatása

Miután regisztrálta a modelljét, és előkészítette az üzembe helyezéséhez szükséges egyéb összetevőket, megadhatja, hogy a telepített szolgáltatásnak milyen PROCESSZORral és memóriával kell rendelkeznie. A profilkészítés a modellt futtató szolgáltatást teszteli, és olyan információkat ad vissza, mint például a CPU-használat, a memóriahasználat és a válasz késése. Emellett javaslatot tesz a processzor és a memória számára az erőforrás-használat alapján.

A modell profiljának elvégzéséhez a következőkre lesz szüksége:
* Egy regisztrált modell.
* A beléptetési parancsfájl és a környezeti környezet definíciója alapján megjelenő következtetési konfiguráció.
* Egyoszlopos táblázatos adatkészlet, amelyben minden sor tartalmaz egy minta típusú kérelmeket jelölő karakterláncot.

> [!IMPORTANT]
> Ezen a ponton csak olyan szolgáltatások profilkészítését támogatjuk, amelyek a kérési adatok karakterláncnak számítanak, például: karakterlánc szerializált JSON, szöveg, karakterlánc szerializált rendszerkép stb. Az adatkészlet (string) egyes sorainak tartalmát a rendszer a HTTP-kérelem törzsébe helyezi el, és elküldi a modellnek a pontozást tartalmazó szolgáltatásnak.

> [!IMPORTANT]
> A ChinaEast2 és a USGovArizona régióban csak 2 processzort támogatunk.

Az alábbi példa bemutatja, hogyan hozhat létre egy bemeneti adatkészletet olyan szolgáltatás profiljához, amely a beérkező kérések adatait szerializált JSON-ként kívánja tartalmazni. Ebben az esetben létrehoztunk egy adatkészleten alapuló 100-példányt az azonos kérelem adattartalmából. A valós forgatókönyvekben azt javasoljuk, hogy használjon nagyobb adatkészleteket, amelyek különböző bemeneteket tartalmaznak, különösen akkor, ha a modell Erőforrás-kihasználtsága/viselkedése a bemenettől függ.

::: zone pivot="py-sdk"

```python
import json
from azureml.core import Datastore
from azureml.core.dataset import Dataset
from azureml.data import dataset_type_definitions

input_json = {'data': [[1, 2, 3, 4, 5, 6, 7, 8, 9, 10],
                       [10, 9, 8, 7, 6, 5, 4, 3, 2, 1]]}
# create a string that can be utf-8 encoded and
# put in the body of the request
serialized_input_json = json.dumps(input_json)
dataset_content = []
for i in range(100):
    dataset_content.append(serialized_input_json)
dataset_content = '\n'.join(dataset_content)
file_name = 'sample_request_data.txt'
f = open(file_name, 'w')
f.write(dataset_content)
f.close()

# upload the txt file created above to the Datastore and create a dataset from it
data_store = Datastore.get_default(ws)
data_store.upload_files(['./' + file_name], target_path='sample_request_data')
datastore_path = [(data_store, 'sample_request_data' +'/' + file_name)]
sample_request_data = Dataset.Tabular.from_delimited_files(
    datastore_path, separator='\n',
    infer_column_types=True,
    header=dataset_type_definitions.PromoteHeadersBehavior.NO_HEADERS)
sample_request_data = sample_request_data.register(workspace=ws,
                                                   name='sample_request_data',
                                                   create_new_version=True)
```

Ha már rendelkezik a mintavételi adatokat tartalmazó adatkészlettel, hozzon létre egy következtetési konfigurációt. A következtetési konfiguráció a score.py és a környezeti definíción alapul. Az alábbi példa bemutatja, hogyan hozhatja létre a következtetési konfigurációt, és hogyan futtathatja a profilkészítést:

```python
from azureml.core.model import InferenceConfig, Model
from azureml.core.dataset import Dataset


model = Model(ws, id=model_id)
inference_config = InferenceConfig(entry_script='path-to-score.py',
                                   environment=myenv)
input_dataset = Dataset.get_by_name(workspace=ws, name='sample_request_data')
profile = Model.profile(ws,
            'unique_name',
            [model],
            inference_config,
            input_dataset=input_dataset)

profile.wait_for_completion(True)

# see the result
details = profile.get_details()
```

::: zone-end

::: zone pivot="cli"


A következő parancs bemutatja, hogyan lehet profilt felvenni a parancssori felület használatával:

```azurecli-interactive
az ml model profile -g <resource-group-name> -w <workspace-name> --inference-config-file <path-to-inf-config.json> -m <model-id> --idi <input-dataset-id> -n <unique-name>
```

> [!TIP]
> A profilkészítés által visszaadott adatok megőrzéséhez használja a modell címkéit vagy tulajdonságait. A címkék vagy tulajdonságok használatával a modell beállításjegyzékében tárolja az adatait. Az alábbi példák azt mutatják be, hogyan adhat hozzá olyan új címkét, amely tartalmazza a `requestedCpu` és a `requestedMemoryInGb` információkat:
>
> ```python
> model.add_tags({'requestedCpu': details['requestedCpu'],
>                 'requestedMemoryInGb': details['requestedMemoryInGb']})
> ```
>
> ```azurecli-interactive
> az ml model profile -g <resource-group-name> -w <workspace-name> --i <model-id> --add-tag requestedCpu=1 --add-tag requestedMemoryInGb=0.5
> ```

::: zone-end

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

