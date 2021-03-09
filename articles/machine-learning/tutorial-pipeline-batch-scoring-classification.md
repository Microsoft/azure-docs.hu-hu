---
title: 'Oktatóanyag: a Batch pontozásának ML-folyamatai'
titleSuffix: Azure Machine Learning
description: Ebben az oktatóanyagban egy gépi tanulási folyamatot hoz létre a Batch-pontozás végrehajtásához. Az infrastruktúra és az automatizálás helyett a gépi tanulásra koncentrálhat.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: lobrien
ms.author: laobri
ms.reviewer: laobri
ms.date: 10/13/2020
ms.custom: contperf-fy20q4, devx-track-python
ms.openlocfilehash: 570bfed5ae5fc6fafea36b9ed1f2673a0daae22b
ms.sourcegitcommit: 956dec4650e551bdede45d96507c95ecd7a01ec9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/09/2021
ms.locfileid: "102521509"
---
# <a name="tutorial-build-an-azure-machine-learning-pipeline-for-batch-scoring"></a>Oktatóanyag: Azure Machine Learning folyamat létrehozása a Batch-pontozáshoz

Ebben a fejlett oktatóanyagban megtudhatja, hogyan hozhat létre egy [Azure Machine learning](concept-ml-pipelines.md) folyamatot egy batch-pontozási feladatok futtatásához. A gépi tanulási folyamatok gyorsabbá, hordozhatósággal és újrafelhasználással optimalizálja a munkafolyamatot, így az infrastruktúra és az automatizálás helyett a gépi tanulásra is koncentrálhat. A folyamat létrehozása és közzététele után beállíthat egy REST-végpontot, amely segítségével bármely platformon bármely HTTP-könyvtárból aktiválhatja a folyamatot. 

A példa a Tensorflow-ben megvalósított, a nem címkézett rendszerképek besorolására szolgáló, előre betanított [kezdeti és v3-](https://arxiv.org/abs/1512.00567) as, a-ben implementált neurális hálózati modellt használja. 

Az oktatóanyagban az alábbi feladatokat fogja végrehajtani:

> [!div class="checklist"]
> * Munkaterület konfigurálása 
> * Mintaadatok letöltése és tárolása
> * Adatkészlet-objektumok létrehozása a lekéréshez és a kimeneti adathoz
> * A modell letöltése, előkészítése és regisztrálása a munkaterületen
> * Számítási célok kiépítése és pontozási szkript létrehozása
> * A `ParallelRunStep` osztály használata aszinkron kötegelt pontozáshoz
> * Folyamat létrehozása, futtatása és közzététele
> * REST-végpont engedélyezése a folyamat számára

Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy ingyenes fiókot. Próbálja ki a [Azure Machine learning ingyenes vagy fizetős verzióját](https://aka.ms/AMLFree) még ma.

## <a name="prerequisites"></a>Előfeltételek

* Ha még nem rendelkezik Azure Machine Learning munkaterülettel vagy notebook virtuális géppel, fejezze be [a telepítési oktatóanyag 1. részét](tutorial-1st-experiment-sdk-setup.md).
* Amikor befejezte a telepítési oktatóanyagot, az *oktatóanyagok/Machine-learning-pipelines-Advanced/tutorial-pipeline-batch-Scoring-Classification. ipynb* notebook megnyitásához használja ugyanazt a notebook-kiszolgálót.

Ha a telepítési oktatóanyagot saját [helyi környezetben](how-to-configure-environment.md#local)szeretné futtatni, a [githubon](https://github.com/Azure/MachineLearningNotebooks/tree/master/tutorials)is elérheti az oktatóanyagot. Futtassa `pip install azureml-sdk[notebooks] azureml-pipeline-core azureml-pipeline-steps pandas requests` a parancsot a szükséges csomagok beszerzéséhez.

## <a name="configure-workspace-and-create-a-datastore"></a>Munkaterület konfigurálása és adattár létrehozása

Munkaterület-objektum létrehozása a meglévő Azure Machine Learning munkaterületről.

```python
from azureml.core import Workspace
ws = Workspace.from_config()
```

> [!IMPORTANT]
> Ez a kódrészlet arra vár, hogy a munkaterület-konfiguráció az aktuális könyvtárba vagy a szülőbe legyen mentve. A munkaterület létrehozásával kapcsolatos további információkért lásd: [Azure Machine learning munkaterületek létrehozása és kezelése](how-to-manage-workspace.md). A konfiguráció fájlra való mentésével kapcsolatos további információkért lásd: [munkaterület konfigurációs fájljának létrehozása](how-to-configure-environment.md#workspace).

## <a name="create-a-datastore-for-sample-images"></a>Adattár létrehozása minta lemezképekhez

A `pipelinedata` fiókban szerezze be a nyilvános blob-tárolóból a ImageNet kiértékelése nyilvános adatok mintáját `sampledata` . Hívja `register_azure_blob_container()` meg a nevet a munkaterület számára elérhetővé tenni kívánt adat számára `images_datastore` . Ezután állítsa be a munkaterület alapértelmezett adattárát kimeneti adattárként. A kimeneti adattárral szerzi be a folyamat kimenetét.

Az adatok elérésével kapcsolatos további információkért lásd: [az adatok elérése](./how-to-access-data.md).

```python
from azureml.core.datastore import Datastore

batchscore_blob = Datastore.register_azure_blob_container(ws, 
                      datastore_name="images_datastore", 
                      container_name="sampledata", 
                      account_name="pipelinedata", 
                      overwrite=True)

def_data_store = ws.get_default_datastore()
```

## <a name="create-dataset-objects"></a>Adatkészlet-objektumok létrehozása

A folyamatok létrehozásakor `Dataset` az objektumok a munkaterület-adattárolók adatainak beolvasására szolgálnak, és `OutputFileDatasetConfig` az objektumok a közbenső adatok átvitelére szolgálnak a folyamat lépései között.

> [!Important]
> Az oktatóanyagban szereplő batch-pontozási példa csak egy folyamat lépését használja. A több lépésből álló használati esetekben a szokásos folyamat a következő lépéseket fogja tartalmazni:
>
> 1. Az `Dataset` objektumokat *bemenetként* használhatja a nyers adatok beolvasásához, elvégezheti az átalakítást, majd *kimenetet* egy `OutputFileDatasetConfig` objektummal.
>
> 2. Használja a `OutputFileDatasetConfig` *kimeneti objektumot* az előző lépésben *bemeneti objektumként*. Ismételje meg a műveletet a következő lépésekhez.

Ebben a forgatókönyvben olyan objektumokat hoz létre, `Dataset` amelyek megfelelnek a bemeneti lemezképek és a besorolási címkék (y-test értékek) adattár-könyvtárainak. Létrehoz egy objektumot is `OutputFileDatasetConfig` a Batch pontozási kimeneti adatokat.

```python
from azureml.core.dataset import Dataset
from azureml.data import OutputFileDatasetConfig

input_images = Dataset.File.from_files((batchscore_blob, "batchscoring/images/"))
label_ds = Dataset.File.from_files((batchscore_blob, "batchscoring/labels/"))
output_dir = OutputFileDatasetConfig(name="scores")
```

Regisztrálja az adatkészleteket a munkaterületen, ha később újra szeretné használni. Ez a lépés nem kötelező.

```python

input_images = input_images.register(workspace = ws, name = "input_images")
label_ds = label_ds.register(workspace = ws, name = "label_ds")
```

## <a name="download-and-register-the-model"></a>A modell letöltése és regisztrálása

Töltse le az előképzésen átadott Tensorflow modellt egy folyamat batch-pontozási funkciójának használatához. Először hozzon létre egy helyi könyvtárat, ahol a modellt tárolja. Ezután töltse le és bontsa ki a modellt.

```python
import os
import tarfile
import urllib.request

if not os.path.isdir("models"):
    os.mkdir("models")
    
response = urllib.request.urlretrieve("http://download.tensorflow.org/models/inception_v3_2016_08_28.tar.gz", "model.tar.gz")
tar = tarfile.open("model.tar.gz", "r:gz")
tar.extractall("models")
```

Ezután regisztrálja a modellt a munkaterületen, így egyszerűen lekérheti a modellt a folyamat során. A `register()` statikus függvényben a `model_name` paraméter a modell megkereséséhez használt kulcs az SDK-ban.

```python
from azureml.core.model import Model
 
model = Model.register(model_path="models/inception_v3.ckpt",
                       model_name="inception",
                       tags={"pretrained": "inception"},
                       description="Imagenet trained tensorflow inception",
                       workspace=ws)
```

## <a name="create-and-attach-the-remote-compute-target"></a>A távoli számítási cél létrehozása és csatolása

A gépi tanulási folyamatokat nem lehet helyileg futtatni, így Felhőbeli erőforrásokon vagy *távoli számítási célokon* futtathatja őket. A távoli számítási cél egy újrafelhasználható virtuális számítási környezet, amelyben kísérleteket és gépi tanulási munkafolyamatokat futtat. 

Futtassa a következő kódot egy GPU-t támogató cél létrehozásához [`AmlCompute`](/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute) , majd csatolja azt a munkaterülethez. További információ a számítási célokról: [fogalmi cikk](./concept-compute-target.md).


```python
from azureml.core.compute import AmlCompute, ComputeTarget
from azureml.exceptions import ComputeTargetException
compute_name = "gpu-cluster"

# checks to see if compute target already exists in workspace, else create it
try:
    compute_target = ComputeTarget(workspace=ws, name=compute_name)
except ComputeTargetException:
    config = AmlCompute.provisioning_configuration(vm_size="STANDARD_NC6",
                                                   vm_priority="lowpriority", 
                                                   min_nodes=0, 
                                                   max_nodes=1)

    compute_target = ComputeTarget.create(workspace=ws, name=compute_name, provisioning_configuration=config)
    compute_target.wait_for_completion(show_output=True, min_node_count=None, timeout_in_minutes=20)
```

## <a name="write-a-scoring-script"></a>Pontozási parancsfájl írása

A pontozás végrehajtásához hozzon létre egy batch-pontozási parancsfájlt `batch_scoring.py` , amelyet aztán az aktuális könyvtárba kell írnia. A parancsfájl bemeneti képeket fogad, alkalmazza a besorolási modellt, majd az előrejelzéseket egy eredmény-fájlba írja.

A `batch_scoring.py` szkript a következő paramétereket fogadja el, amelyeket később a létrehozás után érhet el `ParallelRunStep` :

- `--model_name`: A használt modell neve.
- `--labels_dir`: A `labels.txt` fájl helye.

A folyamat-infrastruktúra a `ArgumentParser` osztály használatával továbbítja a paramétereket a folyamat lépéseibe. A következő kódban például az első argumentum `--model_name` a tulajdonság azonosítója `model_name` . A `init()` függvény a `Model.get_model_path(args.model_name)` tulajdonság elérésére szolgál.


```python
%%writefile batch_scoring.py

import os
import argparse
import datetime
import time
import tensorflow as tf
from math import ceil
import numpy as np
import shutil
from tensorflow.contrib.slim.python.slim.nets import inception_v3

from azureml.core import Run
from azureml.core.model import Model
from azureml.core.dataset import Dataset

slim = tf.contrib.slim

image_size = 299
num_channel = 3


def get_class_label_dict(labels_dir):
    label = []
    labels_path = os.path.join(labels_dir, 'labels.txt')
    proto_as_ascii_lines = tf.gfile.GFile(labels_path).readlines()
    for l in proto_as_ascii_lines:
        label.append(l.rstrip())
    return label


def init():
    global g_tf_sess, probabilities, label_dict, input_images

    parser = argparse.ArgumentParser(description="Start a tensorflow model serving")
    parser.add_argument('--model_name', dest="model_name", required=True)
    parser.add_argument('--labels_dir', dest="labels_dir", required=True)
    args, _ = parser.parse_known_args()

    label_dict = get_class_label_dict(args.labels_dir)
    classes_num = len(label_dict)

    with slim.arg_scope(inception_v3.inception_v3_arg_scope()):
        input_images = tf.placeholder(tf.float32, [1, image_size, image_size, num_channel])
        logits, _ = inception_v3.inception_v3(input_images,
                                              num_classes=classes_num,
                                              is_training=False)
        probabilities = tf.argmax(logits, 1)

    config = tf.ConfigProto()
    config.gpu_options.allow_growth = True
    g_tf_sess = tf.Session(config=config)
    g_tf_sess.run(tf.global_variables_initializer())
    g_tf_sess.run(tf.local_variables_initializer())

    model_path = Model.get_model_path(args.model_name)
    saver = tf.train.Saver()
    saver.restore(g_tf_sess, model_path)


def file_to_tensor(file_path):
    image_string = tf.read_file(file_path)
    image = tf.image.decode_image(image_string, channels=3)

    image.set_shape([None, None, None])
    image = tf.image.resize_images(image, [image_size, image_size])
    image = tf.divide(tf.subtract(image, [0]), [255])
    image.set_shape([image_size, image_size, num_channel])
    return image


def run(mini_batch):
    result_list = []
    for file_path in mini_batch:
        test_image = file_to_tensor(file_path)
        out = g_tf_sess.run(test_image)
        result = g_tf_sess.run(probabilities, feed_dict={input_images: [out]})
        result_list.append(os.path.basename(file_path) + ": " + label_dict[result[0]])
    return result_list
```

> [!TIP]
> Az oktatóanyagban szereplő folyamat csak egyetlen lépésből áll, és egy fájlba írja a kimenetet. A többlépéses folyamatok esetében azt is `ArgumentParser` megadhatja, hogy egy könyvtárat adjon meg a kimeneti adatok írásához a következő lépésekhez. Ha például a tervezési minta használatával több folyamat lépései között kívánja átadni az adatátvitelt `ArgumentParser` , tekintse meg a [jegyzetfüzetet](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines/nyc-taxi-data-regression-model-building/nyc-taxi-data-regression-model-building.ipynb).

## <a name="build-the-pipeline"></a>A folyamat összeállítása

A folyamat futtatása előtt hozzon létre egy objektumot, amely meghatározza a Python-környezetet, és létrehozza a `batch_scoring.py` parancsfájl által igényelt függőségeket. A fő függőség szükséges a Tensorflow, de a `azureml-core` `azureml-dataprep[fuse]` ParallelRunStep által igényelt, valamint a-t is telepíteni kell. Továbbá a Docker és a Docker-GPU támogatását is megadhatja.

```python
from azureml.core import Environment
from azureml.core.conda_dependencies import CondaDependencies
from azureml.core.runconfig import DEFAULT_GPU_IMAGE

cd = CondaDependencies.create(pip_packages=["tensorflow-gpu==1.15.2",
                                            "azureml-core", "azureml-dataprep[fuse]"])
env = Environment(name="parallelenv")
env.python.conda_dependencies = cd
env.docker.base_image = DEFAULT_GPU_IMAGE
```

### <a name="create-the-configuration-to-wrap-the-script"></a>A konfiguráció létrehozása a parancsfájl becsomagolásához

Hozza létre a folyamat lépéseit a parancsfájl, a környezeti konfiguráció és a paraméterek használatával. Adja meg a munkaterülethez már csatolt számítási célt.

```python
from azureml.pipeline.steps import ParallelRunConfig

parallel_run_config = ParallelRunConfig(
    environment=env,
    entry_script="batch_scoring.py",
    source_directory=".",
    output_action="append_row",
    mini_batch_size="20",
    error_threshold=1,
    compute_target=compute_target,
    process_count_per_node=2,
    node_count=1
)
```

### <a name="create-the-pipeline-step"></a>A folyamat lépésének létrehozása

A folyamat lépés egy olyan objektum, amely minden olyan objektumot magában foglal, amely a folyamat futtatásához szükséges, beleértve a következőket:

* Környezeti és függőségi beállítások
* A folyamat futtatásához használandó számítási erőforrás
* Bemeneti és kimeneti adatok, valamint bármely egyéni paraméter
* Hivatkozás a lépés során futtatandó parancsfájlra vagy SDK-logikára

Több osztály örökli a szülő osztályt [`PipelineStep`](/python/api/azureml-pipeline-core/azureml.pipeline.core.builder.pipelinestep) . Kiválaszthatja az osztályokat, hogy meghatározott keretrendszerek vagy stackek használatával hozzon létre egy lépést. Ebben a példában a `ParallelRunStep` osztály használatával határozhatja meg a lépés logikáját egyéni Python-parancsfájl használatával. Ha a parancsfájlhoz tartozó argumentum vagy a lépés kimenete vagy a lépéshez tartozó érték, akkor az argumentumot *a* `arguments` tömbben *és* a `input` vagy a paraméterben is meg kell adni `output` . 

Abban az esetben, ha több lépés is van, a tömbben lévő objektum hivatkozása egy `outputs` későbbi folyamat lépésének *bemenetként* válik elérhetővé.

```python
from azureml.pipeline.steps import ParallelRunStep
from datetime import datetime

parallel_step_name = "batchscoring-" + datetime.now().strftime("%Y%m%d%H%M")

label_config = label_ds.as_named_input("labels_input")

batch_score_step = ParallelRunStep(
    name=parallel_step_name,
    inputs=[input_images.as_named_input("input_images")],
    output=output_dir,
    arguments=["--model_name", "inception",
               "--labels_dir", label_config],
    side_inputs=[label_config],
    parallel_run_config=parallel_run_config,
    allow_reuse=False
)
```

A különböző lépésekhez használható osztályok listáját a [Steps csomagban](/python/api/azureml-pipeline-steps/azureml.pipeline.steps)tekintheti meg.

## <a name="submit-the-pipeline"></a>A folyamat elküldése

Most futtassa a folyamatot. Először hozzon létre egy `Pipeline` objektumot a munkaterület-hivatkozás és a létrehozott folyamat lépés használatával. A `steps` paraméter a lépések tömbje. Ebben az esetben a Batch pontozásnak csak egy lépése van. Több lépésből álló folyamatok létrehozásához helyezze a lépéseket sorrendben ebben a tömbben.

Ezután a függvény használatával `Experiment.submit()` küldje el a folyamatot végrehajtásra. A `wait_for_completion` függvény a folyamat létrehozása során megjeleníti a naplókat. A naplók segítségével megtekintheti az aktuális folyamatot.

> [!IMPORTANT]
> Az első folyamat futtatása körülbelül *15 percet* vesz igénybe. Minden függőséget le kell tölteni, létre kell hozni egy Docker-rendszerképet, és a Python-környezet kiépítve és létrehozva. A folyamat újbóli futtatása jelentősen kevesebb időt vesz igénybe, mivel ezek az erőforrások a létrehozás helyett újra felhasználhatók. A folyamat teljes futási ideje azonban a parancsfájlok és az egyes folyamatokban futó folyamatok mennyiségétől függ.

```python
from azureml.core import Experiment
from azureml.pipeline.core import Pipeline

pipeline = Pipeline(workspace=ws, steps=[batch_score_step])
pipeline_run = Experiment(ws, 'Tutorial-Batch-Scoring').submit(pipeline)
pipeline_run.wait_for_completion(show_output=True)
```

### <a name="download-and-review-output"></a>Kimenet letöltése és áttekintése

Futtassa a következő kódot a parancsfájlból létrehozott kimeneti fájl letöltéséhez `batch_scoring.py` . Ezután vizsgálja meg a pontozás eredményeit.

```python
import pandas as pd

batch_run = next(pipeline_run.get_children())
batch_output = batch_run.get_output_data("scores")
batch_output.download(local_path="inception_results")

for root, dirs, files in os.walk("inception_results"):
    for file in files:
        if file.endswith("parallel_run_step.txt"):
            result_file = os.path.join(root, file)

df = pd.read_csv(result_file, delimiter=":", header=None)
df.columns = ["Filename", "Prediction"]
print("Prediction has ", df.shape[0], " rows")
df.head(10)
```

## <a name="publish-and-run-from-a-rest-endpoint"></a>Közzététel és Futtatás REST-végpontról

Futtassa a következő kódot a folyamat közzétételéhez a munkaterületen. Azure Machine Learning Studio munkaterületén láthatja a folyamat metaadatait, beleértve a futtatási előzményeket és az időtartamokat is. A folyamatot manuálisan is futtathatja a studióból.

A folyamat közzététele lehetővé teszi egy REST-végpont használatát, amellyel bármilyen platformon futtathatja a folyamatot bármely HTTP-könyvtárból.

```python
published_pipeline = pipeline_run.publish_pipeline(
    name="Inception_v3_scoring", description="Batch scoring using Inception v3 model", version="1.0")

published_pipeline
```

A folyamat REST-végpontból való futtatásához szüksége van egy OAuth2-tulajdonos típusú hitelesítési fejlécre. Az alábbi példa interaktív hitelesítést használ (illusztrációs célokra), de az automatizált vagy a fej nélküli hitelesítést igénylő üzemi forgatókönyvek esetében használja az egyszerű szolgáltatás hitelesítését a [jelen cikkben leírtak](how-to-setup-authentication.md)szerint.

Az egyszerű szolgáltatás hitelesítése magában foglalja az *alkalmazás regisztrációjának* létrehozását *Azure Active Directory*-ben. Először létrehoz egy ügyfél-titkos kulcsot, majd megadja a szolgáltatás elsődleges *szerepkörének hozzáférését* a Machine learning-munkaterülethez. A [`ServicePrincipalAuthentication`](/python/api/azureml-core/azureml.core.authentication.serviceprincipalauthentication) hitelesítési folyamat kezeléséhez használja a osztályt. 

Mindkettő [`InteractiveLoginAuthentication`](/python/api/azureml-core/azureml.core.authentication.interactiveloginauthentication) és `ServicePrincipalAuthentication` öröklése a következőtől: `AbstractAuthentication` . Mindkét esetben használja a [`get_authentication_header()`](/python/api/azureml-core/azureml.core.authentication.abstractauthentication#get-authentication-header--) függvényt ugyanúgy, hogy beolvassa a fejlécet:

```python
from azureml.core.authentication import InteractiveLoginAuthentication

interactive_auth = InteractiveLoginAuthentication()
auth_header = interactive_auth.get_authentication_header()
```

A REST URL-cím beolvasása a `endpoint` közzétett folyamat objektumának tulajdonságában. A REST URL-címet a munkaterületen is megtalálhatja Azure Machine Learning Studióban. 

Hozzon létre egy HTTP POST-kérelmet a végpontnak. Adja meg a hitelesítési fejlécet a kérelemben. Adjon hozzá egy, a kísérlet nevét tartalmazó JSON-adattartalom-objektumot.

A Futtatás elindítására vonatkozó kérelem elvégzése. Adja meg a kód segítségével a kulcs elérését a `Id` Válasz szótárában a futtatási azonosító értékének lekéréséhez.

```python
import requests

rest_endpoint = published_pipeline.endpoint
response = requests.post(rest_endpoint, 
                         headers=auth_header, 
                         json={"ExperimentName": "Tutorial-Batch-Scoring",
                               "ParameterAssignments": {"process_count_per_node": 6}})
run_id = response.json()["Id"]
```

Az új Futtatás állapotának figyeléséhez használja a futtatási azonosítót. Az új Futtatás újabb 10-15 percig tart. 

Az új Futtatás az oktatóanyag korábbi részében futtatott folyamathoz hasonlóan fog kinézni. Dönthet úgy, hogy nem jeleníti meg a teljes kimenetet.

```python
from azureml.pipeline.core.run import PipelineRun
from azureml.widgets import RunDetails

published_pipeline_run = PipelineRun(ws.experiments["Tutorial-Batch-Scoring"], run_id)
RunDetails(published_pipeline_run).show()
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ne hajtsa végre ezt a szakaszt, ha más Azure Machine Learning oktatóanyagokat szeretne futtatni.

### <a name="stop-the-compute-instance"></a>A számítási példány leállítása

[!INCLUDE [aml-stop-server](../../includes/aml-stop-server.md)]

### <a name="delete-everything"></a>Mindent törölni

Ha nem tervezi a létrehozott erőforrások használatát, törölje őket, így nem számítunk fel díjat:

1. A Azure Portal bal oldali menüjében válassza az **erőforráscsoportok** lehetőséget.
1. Az erőforráscsoportok listájában válassza ki a létrehozott erőforráscsoportot.
1. Válassza az **Erőforráscsoport törlése** elemet.
1. Írja be az erőforráscsoport nevét. Ezután válassza a **Törlés** lehetőséget.

Megtarthatja az erőforráscsoportot is, de törölhet egyetlen munkaterületet is. Jelenítse meg a munkaterület tulajdonságait, majd válassza a **Törlés** lehetőséget.

## <a name="next-steps"></a>Következő lépések

Ebben a Machine learning-folyamatok oktatóanyagában a következő feladatokat végezte el:

> [!div class="checklist"]
> * Egy távoli GPU számítási erőforráson való futtatáshoz szükséges környezeti függőségekkel rendelkező folyamat létrehozása.
> * Létrehozott egy pontozási parancsfájlt a kötegelt előrejelzések futtatásához egy előre elkészített Tensorflow-modell használatával.
> * Közzétett egy folyamatot, és engedélyezte, hogy a REST-végpontról fusson.

Ha további példákat szeretne a folyamatok létrehozásához a Machine learning SDK használatával, tekintse meg a [Jegyzetfüzet-tárházat](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/machine-learning-pipelines).
