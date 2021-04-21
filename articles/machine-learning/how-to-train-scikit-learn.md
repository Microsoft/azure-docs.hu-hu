---
title: Scikit-learn gépi tanulási modellek betanítása
titleSuffix: Azure Machine Learning
description: Megtudhatja, Azure Machine Learning segítségével hogyan skálázható fel egy scikit-learn betanító feladat rugalmas felhőalapú számítási erőforrásokkal.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: jordane
author: jpe316
ms.date: 09/28/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python
ms.openlocfilehash: ef64d94ed3e860895bcc81a1429008205a1a8acb
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2021
ms.locfileid: "107817106"
---
# <a name="train-scikit-learn-models-at-scale-with-azure-machine-learning"></a>Scikit-learn-modellek nagy léptékű betanítása Azure Machine Learning

Ebből a cikkből megtudhatja, hogyan futtathat scikit-learn betanító szkripteket a Azure Machine Learning.

A cikkben található példaszkekkel írisz virágú képeket osztályozunk, hogy gépi tanulási modellt készítsünk a scikit-learn [iris adatkészlete alapján.](https://archive.ics.uci.edu/ml/datasets/iris)

Függetlenül attól, hogy az alapból betanít egy gépi tanulási scikit-learn modellt, vagy egy meglévő modellt hoz létre a felhőbe, az Azure Machine Learning-t használhatja a nyílt forráskódú betanító feladatok rugalmas felhőalapú számítási erőforrások használatával történő horizontális felskálasztása érdekében. A modellekkel éles környezetben használható modelleket építhet ki, helyezhet üzembe, verziószámozhat és Azure Machine Learning.

## <a name="prerequisites"></a>Előfeltételek

Futtassa ezt a kódot az alábbi környezetek egyikében:
 - Azure Machine Learning számítási példány – nincs szükség letöltésre vagy telepítésre

    - Az [Oktatóanyag: Környezet](tutorial-1st-experiment-sdk-setup.md)  és munkaterület beállítása lépésekkel hozzon létre egy, az SDK-val és a mintaadattáraval előre feltöltött dedikált notebookkiszolgálót.
    - A notebookkiszolgáló minták betanító mappájában keressen egy befejezett és kibontott jegyzetfüzetet a következő könyvtárban: **how-to-use-azureml > ml-frameworks > scikit-learn > train-hyperparameter-tune-deploy-with-sklearn** mappa.

 - Saját Jupyter Notebook kiszolgálója

    - [Telepítse az Azure Machine Learning SDK-t](/python/api/overview/azure/ml/install) (>= 1.13.0).
    - [Hozzon létre egy munkaterület-konfigurációs fájlt.](how-to-configure-environment.md#workspace)

## <a name="set-up-the-experiment"></a>A kísérlet beállítása

Ez a szakasz a szükséges Python-csomagok betöltésével, egy munkaterület inicializálása, a betanítás környezetének definiálása és a betanítési szkript előkészítése által állítja be a betanítás kísérletét.

### <a name="initialize-a-workspace"></a>Munkaterület inicializálása

A [Azure Machine Learning munkaterület](concept-workspace.md) a szolgáltatás legfelső szintű erőforrása. Központi helyet biztosít az összes létrehozott összetevővel való munkához. A Python SDK-ban objektum létrehozásával férhet hozzá a [`workspace`](/python/api/azureml-core/azureml.core.workspace.workspace) munkaterület-összetevőkhez.

Hozzon létre egy munkaterület-objektumot `config.json` az előfeltételek szakaszban létrehozott [fájlból.](#prerequisites)

```Python
from azureml.core import Workspace

ws = Workspace.from_config()
```

### <a name="prepare-scripts"></a>Szkriptek előkészítése

Ebben az oktatóanyagban a **train_iris.py** betanító szkript már rendelkezésre [áll.](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/ml-frameworks/scikit-learn/train-hyperparameter-tune-deploy-with-sklearn/train_iris.py) A gyakorlatban bármilyen egyéni betanító szkriptet saját ként használhat, és futtathat az Azure ML-ben anélkül, hogy módosítania kellene a kódot.

Megjegyzések:
- A megadott betanító szkript bemutatja, hogyan naplózhat metrikákat az Azure ML-futtatásba `Run` a szkript objektumával.
- A megadott betanító szkript példaadatokat használ a  `iris = datasets.load_iris()` függvényből.  A saját adatok használatával és [](how-to-train-with-datasets.md) elérésével kapcsolatos információkért tekintse meg, hogyan használhatók a betanítás adatkészletekkel, hogy az adatok elérhetők legyen a betanítás során.

### <a name="define-your-environment"></a>A környezet meghatározása

A betanító [](concept-environments.md) szkript függőségeit beágyazó Azure ML-környezet meghatározásához definiálhat egy egyéni környezetet, vagy használhatja az Azure ML-ben összeszámolt környezetet.

#### <a name="use-a-curated-environment"></a>Összeforrott környezet használata
Az Azure ML előre összeállított környezeteket is biztosít, ha nem szeretné meghatározni a saját környezetét. További információ: [.](resource-curated-environments.md)
Ha egy össze összecsatolandó környezetet szeretne használni, ehelyett futtassa a következő parancsot:

```python
from azureml.core import Environment

sklearn_env = Environment.get(workspace=ws, name='AzureML-Tutorial')
```

#### <a name="create-a-custom-environment"></a>Egyéni környezet létrehozása

Saját egyéni környezetet is létrehozhat. Definiálja a Conda-függőségeket egy YAML-fájlban; Ebben a példában a fájl neve `conda_dependencies.yml` .

```yaml
dependencies:
  - python=3.6.2
  - scikit-learn
  - numpy
  - pip:
    - azureml-defaults
```

Hozzon létre egy Azure ML-környezetet ebből a Conda-környezet specifikációból. A környezet futásidőben egy Docker-tárolóba lesz csomagolva.
```python
from azureml.core import Environment

sklearn_env = Environment.from_conda_specification(name='sklearn-env', file_path='conda_dependencies.yml')
```

További információ a környezetek létrehozásáról és használatával kapcsolatban: Szoftverkörnyezetek létrehozása és használata [a Azure Machine Learning.](how-to-use-environments.md)

## <a name="configure-and-submit-your-training-run"></a>A betanítás futtatásának konfigurálása és elküldése

### <a name="create-a-scriptrunconfig"></a>ScriptRunConfig létrehozása
Hozzon létre egy ScriptRunConfig objektumot a betanítási feladat konfigurációs részleteinek megadásához, beleértve a betanítási szkriptet, a használni kívánt környezetet és a futtatni kívánt számítási célt.
A betanítási szkript minden argumentuma a parancssoron keresztül lesz átadva, ha a paraméterben meg van `arguments` adva.

Az alábbi kód egy ScriptRunConfig objektumot konfigurál a feladatnak a helyi gépen való végrehajtásra való beküldése számára.

```python
from azureml.core import ScriptRunConfig

src = ScriptRunConfig(source_directory='.',
                      script='train_iris.py',
                      arguments=['--kernel', 'linear', '--penalty', 1.0],
                      environment=sklearn_env)
```

Ha ehelyett távoli fürtön szeretné futtatni a feladatot, megadhatja a kívánt számítási célt a `compute_target` ScriptRunConfig paraméterhez.

```python
from azureml.core import ScriptRunConfig

compute_target = ws.compute_targets['<my-cluster-name>']
src = ScriptRunConfig(source_directory='.',
                      script='train_iris.py',
                      arguments=['--kernel', 'linear', '--penalty', 1.0],
                      compute_target=compute_target,
                      environment=sklearn_env)
```

### <a name="submit-your-run"></a>A futtatás elküldése
```python
from azureml.core import Experiment

run = Experiment(ws,'Tutorial-TrainIRIS').submit(src)
run.wait_for_completion(show_output=True)
```

> [!WARNING]
> Azure Machine Learning a teljes forráskönyvtár másolása által futtatja a betanító szkripteket. Ha bizalmas adatokat nem szeretne feltölteni, használjon [.ignore](how-to-save-write-experiment-files.md#storage-limits-of-experiment-snapshots) fájlt, vagy ne foglalja bele őket a forráskönyvtárba. Ehelyett egy Azure ML-adatkészlet használatával [férhet hozzá az adataihoz.](how-to-train-with-datasets.md)

### <a name="what-happens-during-run-execution"></a>Mi történik a futtatás végrehajtása során?
A futtatás végrehajtása során a következő szakaszokon megy keresztül:

- **Előkészítés:** A docker-rendszerkép a meghatározott környezetnek megfelelően jön létre. A rendszerképet feltölti a rendszer a munkaterület tárolójegyzékbe, és gyorsítótárazza későbbi futtatásra. A naplók a futtatás előzményeibe is streamelve megtekinthetők a folyamat figyelése érdekében. Ha ehelyett egy összeértett környezetet ad meg, a rendszer az adott környezethez háttérként gyorsítótárazott rendszerképet használja.

- **Skálázás:** A fürt akkor kísérl meg horizontálisan felskálát Batch AI, ha a fürt több csomópontot igényel a futtatás végrehajtásához, mint amennyit jelenleg elérhető.

- **Futtatva:** A szkriptmappában lévő összes szkript fel lesz töltve a számítási célra, az adattárak csatlakoztatva vagy másolhatók, és a `script` rendszer végrehajtja a fájlt. Az stdout és **a ./logs** mappa kimenetei a futtatás előzményeibe lesznek streamelve, és a futtatás figyelése érdekében használhatók.

- **Utófeldolgozás:** A **futtatás ./outputs** mappája át lesz mászva a futtatás előzményeibe.

## <a name="save-and-register-the-model"></a>A modell mentése és regisztrálása

A modell betanítás után mentheti és regisztrálhatja azt a munkaterületen. A modellregisztrációval a modelleket a munkaterületen tárolhatja és verziószámmal egészheti ki, így leegyszerűsítheti [a modellkezelést és az üzembe helyezést.](concept-model-management-and-deployment.md)

A modell mentéshez adja hozzá a következő kódot a train_iris.py fájlhoz. 

``` Python
import joblib

joblib.dump(svm_model_linear, 'model.joblib')
```

Regisztrálja a modellt a munkaterületen az alábbi kóddal. A , és paraméterek megadásával elérhetővé válik a kód `model_framework` `model_framework_version` nélkül modell üzembe `resource_configuration` helyezése. A kód nélkül üzemelő modell üzembe helyezése lehetővé teszi a modell webszolgáltatásként való közvetlen üzembe helyezését a regisztrált modellből, és az objektum határozza meg a webszolgáltatás számítási [`ResourceConfiguration`](/python/api/azureml-core/azureml.core.resource_configuration.resourceconfiguration) erőforrását.

```Python
from azureml.core import Model
from azureml.core.resource_configuration import ResourceConfiguration

model = run.register_model(model_name='sklearn-iris', 
                           model_path='outputs/model.joblib',
                           model_framework=Model.Framework.SCIKITLEARN,
                           model_framework_version='0.19.1',
                           resource_configuration=ResourceConfiguration(cpu=1, memory_in_gb=0.5))
```

## <a name="deployment"></a>Üzembe helyezés

Az előbb regisztrált modell pontosan ugyanúgy helyezhető üzembe, mint bármely más regisztrált modell az Azure ML-ben. Az üzembe helyezési útmutató tartalmaz egy szakaszt a modellek regisztrálása során, de közvetlenül is átugorhat [egy](how-to-deploy-and-where.md#choose-a-compute-target) számítási cél létrehozásához az üzembe helyezéshez, mivel már rendelkezik regisztrált modellel.

### <a name="preview-no-code-model-deployment"></a>(Előzetes verzió) Kód nélkül modell üzembe helyezése

A hagyományos üzembe helyezési útvonal helyett használhatja a scikit-learn kód nélkül üzembe helyezési funkcióját (előzetes verzió). A kód nélkül üzembe helyezés minden beépített scikit-learn modelltípus esetében támogatott. Ha regisztrálja a modellt a fenti , és paraméterekkel, egyszerűen használhatja a statikus függvényt a `model_framework` `model_framework_version` modell üzembe `resource_configuration` [`deploy()`](/python/api/azureml-core/azureml.core.model%28class%29#deploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-) helyezéséhez.

```python
web_service = Model.deploy(ws, "scikit-learn-service", [model])
```

MEGJEGYZÉS: Ezeket a függőségeket az előre felépített scikit-learn következtetési tároló tartalmazza.

```yaml
    - azureml-defaults
    - inference-schema[numpy-support]
    - scikit-learn
    - numpy
```

A részletes [üzembe helyezés részletes](how-to-deploy-and-where.md) Azure Machine Learning részletesen.


## <a name="next-steps"></a>Következő lépések

Ebben a cikkben betanított és regisztrált egy scikit-learn modellt, és megismerte az üzembe helyezési lehetőségeket. A további tudnivalókért tekintse meg ezeket a Azure Machine Learning.

* [Futtatás metrika nyomon követése a betanítás során](how-to-log-view-metrics.md)
* [Hiperparaméterek hangolása](how-to-tune-hyperparameters.md)
