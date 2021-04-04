---
title: A Studio használata a Designerben betanított modellek üzembe helyezéséhez
titleSuffix: Azure Machine Learning
description: A gépi tanulási modellek egyetlen sor kód írása nélkül helyezhetők üzembe a Azure Machine Learning Studio használatával.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: keli19
author: likebupt
ms.reviewer: peterlu
ms.date: 10/29/2020
ms.topic: conceptual
ms.custom: how-to, deploy, studio, designer
ms.openlocfilehash: 35acfc51ae76fdacef11f03b1fbd91ad58650ae6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "97722623"
---
# <a name="use-the-studio-to-deploy-models-trained-in-the-designer"></a>A Studio használata a Designerben betanított modellek üzembe helyezéséhez

Ebből a cikkből megtudhatja, hogyan helyezhet üzembe egy tervezői modellt valós idejű végpontként Azure Machine Learning Studióban.

A regisztrálás vagy a letöltés után a tervező által betanított modellek ugyanúgy használhatók, mint bármely más modell. Az exportált modellek olyan használati esetekben telepíthetők, mint például az IoT és a helyi telepítések.

A Studióban történő üzembe helyezés a következő lépésekből áll:

1. Regisztrálja a betanított modellt.
1. Töltse le a modellhez tartozó Entry script és Conda függőségi fájlt.
1. Választható Adja meg a bejegyzési parancsfájlt.
1. A modell üzembe helyezése számítási célra.

A modelleket közvetlenül a tervezőben is üzembe helyezheti, és kihagyhatja a modell regisztrációját és a fájlok letöltési lépéseit. Ez hasznos lehet a gyors üzembe helyezéshez. További információ: [modell üzembe helyezése a tervezővel](tutorial-designer-automobile-price-deploy.md).

A tervezőben betanított modellek az SDK vagy a parancssori felület (CLI) használatával is üzembe helyezhetők. További információ: [a meglévő modell üzembe helyezése Azure Machine Learningsal](how-to-deploy-existing-model.md).

## <a name="prerequisites"></a>Előfeltételek

* [Egy Azure Machine Learning-munkaterület](how-to-manage-workspace.md)

* Egy befejezett betanítási folyamat, amely a következő modulok egyikét tartalmazza:
    - [Betanítási modell modul](./algorithm-module-reference/train-model.md)
    - [A Train rendellenesség észlelési modell modulja](./algorithm-module-reference/train-anomaly-detection-model.md)
    - [A fürtözési modell moduljának betanítása](./algorithm-module-reference/train-clustering-model.md)
    - [Pytorch-modell betanítása modul](./algorithm-module-reference/train-pytorch-model.md)
    - [SVD ajánló modul](./algorithm-module-reference/train-svd-recommender.md)
    - [Vowpal Wabbit modell moduljának betanítása](./algorithm-module-reference/train-vowpal-wabbit-model.md)
    - [A Wide & Deep Model modul betanítása](./algorithm-module-reference/train-wide-and-deep-recommender.md)

## <a name="register-the-model"></a>A modell regisztrálása

A betanítási folyamat befejeződése után regisztrálja a betanított modellt a Azure Machine Learning munkaterületen a modell más projektekben való eléréséhez.

1. Válassza ki a [Train Model modult](./algorithm-module-reference/train-model.md).
1. Válassza a **kimenet + naplók** fület a jobb oldali ablaktáblán.
1. A fogaskerék ikonra kattintva válassza a **modell regisztrálása** ikont ![ ](./media/how-to-deploy-model-designer/register-model-icon.png) .

    ![A Train Model modul jobb oldali ablaktáblájának képernyőképe](./media/how-to-deploy-model-designer/train-model-right-pane.png)

1. Adja meg a modell nevét, majd válassza a **Mentés** lehetőséget.

A modell regisztrálása után megkeresheti a Studio **modellek** eszköz lapján.
    
![Képernyőfelvétel a regisztrált modellről a modellek eszköz lapján](./media/how-to-deploy-model-designer/models-asset-page.png)

## <a name="download-the-entry-script-file-and-conda-dependencies-file"></a>A bejegyzési parancsfájl és a Conda függőségi fájl letöltése

A következő fájlokra van szükség a modell üzembe helyezéséhez Azure Machine Learning Studióban:

- **Bejegyzési parancsfájl-fájl** – betölti a betanított modellt, feldolgozza a kérések bemeneti adatait, valós idejű következtetéseket ad vissza, és visszaadja az eredményt. A tervező automatikusan létrehoz egy `score.py` bejegyzési parancsfájlt, amikor a **Train Model** modul befejeződik.

- **Conda függőségeinek fájlja** – meghatározza, hogy mely pip-és Conda-csomagok függenek a webszolgáltatástól. A tervező automatikusan létrehoz egy `conda_env.yaml` fájlt, amikor a **Train Model** modul befejeződik.

A következő két fájlt letöltheti a **betanítási modell** moduljának jobb oldali paneljén:

1. Válassza ki a **Train Model** modult.
1. A **kimenetek és naplók** lapon válassza ki a mappát `trained_model_outputs` .
1. Töltse le a `conda_env.yaml` fájlt és a `score.py` fájlt.

    ![Képernyőfelvétel: fájlok letöltése a központi telepítéshez a jobb oldali panelen](./media/how-to-deploy-model-designer/download-artifacts-in-right-pane.png)

Azt is megteheti, hogy a modell regisztrálása után letölti a fájlokat a **modellek** eszközcsoport oldaláról:

1. Navigáljon a **modellek** eszközcsoport oldalára.
1. Válassza ki a telepíteni kívánt modellt.
1. Válassza ki **az összetevők** lapot.
1. Jelölje ki az `trained_model_outputs` mappát.
1. Töltse le a `conda_env.yaml` fájlt és a `score.py` fájlt.  

    ![Képernyőkép a fájlok letöltéséről az üzembe helyezéshez a modell részletei lapon](./media/how-to-deploy-model-designer/download-artifacts-in-models-page.png)

> [!NOTE]
> A `score.py` fájl szinte ugyanazokat a funkciókat biztosítja, mint a **pontszám modell** moduljai. Azonban egyes modulok, például a [score SVD ajánló](./algorithm-module-reference/score-svd-recommender.md), a [pontszám széles és mély ajánlása](./algorithm-module-reference/score-wide-and-deep-recommender.md), és a [score Vowpal Wabbit modell](./algorithm-module-reference/score-vowpal-wabbit-model.md) paraméterekkel rendelkezik a különböző pontozási módokhoz. A paramétereket a bejegyzési parancsfájlban is módosíthatja.
>
>A paraméterek a fájlban való beállításával kapcsolatos további információkért `score.py` tekintse meg a [bejegyzési parancsfájl konfigurálását](#configure-the-entry-script)ismertető szakaszt.

## <a name="deploy-the-model"></a>A modell üzembe helyezése

A szükséges fájlok letöltése után már készen áll a modell üzembe helyezésére.

1. A **modellek** eszköz lapon válassza ki a regisztrált modellt.
1. Válassza a **telepítés** gombot.
1. A konfiguráció menüben adja meg a következő adatokat:

    - Adja meg a végpont nevét.
    - Válassza a modell üzembe helyezését az [Azure Kubernetes Service](how-to-deploy-azure-kubernetes-service.md) vagy az [Azure Container instance](how-to-deploy-azure-container-instance.md)szolgáltatásban.
    - Töltse fel a `score.py` **bejegyzést a bejegyzési parancsfájlba**.
    - Töltse fel a `conda_env.yml` **Conda-függőségek fájlját**. 

    >[!TIP]
    > A **speciális** beállításban beállíthatja a CPU/memória kapacitását és egyéb paramétereket az üzembe helyezéshez. Ezek a beállítások bizonyos modellek, például a PyTorch modellek esetében fontosak, amelyek jelentős mennyiségű mémeket használnak (körülbelül 4 GB).

1. Válassza a **telepítés** lehetőséget a modell valós idejű végpontként való üzembe helyezéséhez.

    ![Képernyőkép a modell üzembe helyezéséről a modell-eszközről lapon](./media/how-to-deploy-model-designer/deploy-model.png)

## <a name="consume-the-real-time-endpoint"></a>A valós idejű végpont felhasználása

Az üzembe helyezés sikeres végrehajtása után megkeresheti a valós idejű végpontot a **végpontok** eszköz lapon. Itt talál egy REST-végpontot, amelyet az ügyfelek a valós idejű végpontnak küldött kérések küldésére használhatnak. 

> [!NOTE]
> A tervező egy minta-adatjson-fájlt is létrehoz a teszteléshez, `_samples.json` a **trained_model_outputs** mappában is letölthető.

A következő mintakód használatával valós idejű végpontot használhat fel.

```python

import json
from pathlib import Path
from azureml.core.workspace import Workspace, Webservice
 
service_name = 'YOUR_SERVICE_NAME'
ws = Workspace.get(
    name='WORKSPACE_NAME',
    subscription_id='SUBSCRIPTION_ID',
    resource_group='RESOURCEGROUP_NAME'
)
service = Webservice(ws, service_name)
sample_file_path = '_samples.json'
 
with open(sample_file_path, 'r') as f:
    sample_data = json.load(f)
score_result = service.run(json.dumps(sample_data))
print(f'Inference result = {score_result}')
```

### <a name="consume-computer-vision-related-real-time-endpoints"></a>Számítógépes jövőképtel kapcsolatos valós idejű végpontok felhasználása

A számítógép-megjelenítéssel kapcsolatos valós idejű végpontok használatakor a lemezképeket bájtra kell konvertálnia, mivel a webszolgáltatás csak bemenetként fogadja a karakterláncot. A következő mintakód:

```python
import base64
import json
from copy import deepcopy
from pathlib import Path
from azureml.studio.core.io.image_directory import (IMG_EXTS, image_from_file, image_to_bytes)
from azureml.studio.core.io.transformation_directory import ImageTransformationDirectory

# image path
image_path = Path('YOUR_IMAGE_FILE_PATH')

# provide the same parameter setting as in the training pipeline. Just an example here.
image_transform = [
    # format: (op, args). {} means using default parameter values of torchvision.transforms.
    # See https://pytorch.org/docs/stable/torchvision/transforms.html
    ('Resize', 256),
    ('CenterCrop', 224),
    # ('Pad', 0),
    # ('ColorJitter', {}),
    # ('Grayscale', {}),
    # ('RandomResizedCrop', 256),
    # ('RandomCrop', 224),
    # ('RandomHorizontalFlip', {}),
    # ('RandomVerticalFlip', {}),
    # ('RandomRotation', 0),
    # ('RandomAffine', 0),
    # ('RandomGrayscale', {}),
    # ('RandomPerspective', {}),
]
transform = ImageTransformationDirectory.create(transforms=image_transform).torch_transform

# download _samples.json file under Outputs+logs tab in the right pane of Train Pytorch Model module
sample_file_path = '_samples.json'
with open(sample_file_path, 'r') as f:
    sample_data = json.load(f)

# use first sample item as the default value
default_data = sample_data[0]
data_list = []
for p in image_path.iterdir():
    if p.suffix.lower() in IMG_EXTS:
        data = deepcopy(default_data)
        # convert image to bytes
        data['image'] = base64.b64encode(image_to_bytes(transform(image_from_file(p)))).decode()
        data_list.append(data)

# use data.json as input of consuming the endpoint
data_file_path = 'data.json'
with open(data_file_path, 'w') as f:
    json.dump(data_list, f)
```

## <a name="configure-the-entry-script"></a>A bejegyzés parancsfájljának konfigurálása

A tervező néhány modulja, például a [score SVD ajánló](./algorithm-module-reference/score-svd-recommender.md), a [széles körű és mély ajánló](./algorithm-module-reference/score-wide-and-deep-recommender.md), valamint a [score Vowpal Wabbit-modell](./algorithm-module-reference/score-vowpal-wabbit-model.md) paraméterekkel rendelkezik a különböző pontozási módokhoz. 

Ebből a szakaszból megtudhatja, hogyan frissítheti ezeket a paramétereket a bejegyzés parancsfájl-fájljába.

Az alábbi példa egy gyakorlott, **széles körben & részletes ajánló** modell alapértelmezett viselkedését frissíti. Alapértelmezés szerint a `score.py` fájl közli a webszolgáltatással, hogy megjósolja a felhasználók és az elemek közötti minősítést. 

Módosíthatja a bejegyzés parancsfájlját, hogy javaslatokat tegyen az elemekre, és a paraméter módosításával visszaadja az ajánlott elemeket `recommender_prediction_kind` .


```python
import os
import json
from pathlib import Path
from collections import defaultdict
from azureml.studio.core.io.model_directory import ModelDirectory
from azureml.designer.modules.recommendation.dnn.wide_and_deep.score. \
    score_wide_and_deep_recommender import ScoreWideAndDeepRecommenderModule
from azureml.designer.serving.dagengine.utils import decode_nan
from azureml.designer.serving.dagengine.converter import create_dfd_from_dict

model_path = os.path.join(os.getenv('AZUREML_MODEL_DIR'), 'trained_model_outputs')
schema_file_path = Path(model_path) / '_schema.json'
with open(schema_file_path) as fp:
    schema_data = json.load(fp)


def init():
    global model
    model = ModelDirectory.load(load_from_dir=model_path)


def run(data):
    data = json.loads(data)
    input_entry = defaultdict(list)
    for row in data:
        for key, val in row.items():
            input_entry[key].append(decode_nan(val))

    data_frame_directory = create_dfd_from_dict(input_entry, schema_data)

    # The parameter names can be inferred from Score Wide and Deep Recommender module parameters:
    # convert the letters to lower cases and replace whitespaces to underscores.
    score_params = dict(
        trained_wide_and_deep_recommendation_model=model,
        dataset_to_score=data_frame_directory,
        training_data=None,
        user_features=None,
        item_features=None,
        ################### Note #################
        # Set 'Recommender prediction kind' parameter to enable item recommendation model
        recommender_prediction_kind='Item Recommendation',
        recommended_item_selection='From All Items',
        maximum_number_of_items_to_recommend_to_a_user=5,
        whether_to_return_the_predicted_ratings_of_the_items_along_with_the_labels='True')
    result_dfd, = ScoreWideAndDeepRecommenderModule().run(**score_params)
    result_df = result_dfd.data
    return json.dumps(result_df.to_dict("list"))
```

A **Wide & Deep ajánló** és a **Vowpal Wabbit** modellek esetében a következő módszerekkel konfigurálhatja a pontozási mód paraméterét:

- A paraméterek nevei a [pontszámok Vowpal Wabbit modellje](./algorithm-module-reference/score-vowpal-wabbit-model.md) és a [pontszám széles és mély ajánlása](./algorithm-module-reference/score-wide-and-deep-recommender.md).
- A Mode Type paraméter értéke a megfelelő beállítások neveinek karakterlánca. A fenti kódokban használja az **Ajánlói előrejelző típust** példaként, az érték lehet `'Rating Prediction'` vagy `'Item Recommendation'` . Más értékek nem engedélyezettek.

A **SVD ajánló** betanított modell esetében előfordulhat, hogy a paraméterek nevei és értékei kevésbé nyilvánvalóak, és az alábbi táblázatokból megtudhatja, hogyan állíthatja be a paramétereket.

| Paraméter neve a [score SVD ajánlóban](./algorithm-module-reference/score-svd-recommender.md)                           | A paraméter neve a bejegyzés parancsfájljában |
| ------------------------------------------------------------ | --------------------------------------- |
| Ajánlói előrejelző típus                                  | prediction_kind                         |
| Javasolt elem kiválasztása                                   | recommended_item_selection              |
| Az ajánlási készlet minimális mérete egyetlen felhasználó számára    | min_recommendation_pool_size            |
| A felhasználók számára ajánlott elemek maximális száma               | max_recommended_item_count              |
| Azt határozza meg, hogy az elemek előre jelzett minősítését szeretné-e visszaadni a feliratokkal együtt | return_ratings                          |

Az alábbi kód azt mutatja be, hogyan lehet paramétereket beállítani egy SVD-ajánlóhoz, amely mind a hat paramétert felhasználva értékelt minősítésekhez kapcsolódó, becsült értékű elemeket javasol.

```python
score_params = dict(
        learner=model,
        test_data=DataTable.from_dfd(data_frame_directory),
        training_data=None,
        # RecommenderPredictionKind has 2 members, 'RatingPrediction' and 'ItemRecommendation'. You
        # can specify prediction_kind parameter with one of them.
        prediction_kind=RecommenderPredictionKind.ItemRecommendation,
        # RecommendedItemSelection has 3 members, 'FromAllItems', 'FromRatedItems', 'FromUndatedItems'.
        # You can specify recommended_item_selection parameter with one of them.
        recommended_item_selection=RecommendedItemSelection.FromRatedItems,
        min_recommendation_pool_size=1,
        max_recommended_item_count=3,
        return_ratings=True,
    )
```


## <a name="next-steps"></a>Következő lépések

* [Modell betanítása a tervezőben](tutorial-designer-automobile-price-train-score.md)
* [Modellek üzembe helyezése Azure Machine Learning SDK-val](how-to-deploy-and-where.md)
* [Sikertelen üzembe helyezés hibaelhárítása](how-to-troubleshoot-deployment.md)
* [Üzembe helyezés az Azure Kubernetes Service-ben](how-to-deploy-azure-kubernetes-service.md)
* [Ügyfélalkalmazások létrehozása webszolgáltatások felhasználásához](how-to-consume-web-service.md)
* [Webszolgáltatás frissítése](how-to-deploy-update-web-service.md)
