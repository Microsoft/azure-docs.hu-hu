---
title: 'Képbesorolási oktatóanyag: Modellek betanítása'
titleSuffix: Azure Machine Learning
description: A Azure Machine Learning képbesorolási modell betanítása a scikit-learn használatával Python-Jupyter Notebook. Ez az oktatóanyag a második rész első része.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: sdgilley
ms.author: sgilley
ms.date: 09/28/2020
ms.custom: seodec18, devx-track-python
ms.openlocfilehash: 6c5691759983d8ec40598834e5dbcd507ccf00cf
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2021
ms.locfileid: "107816871"
---
# <a name="tutorial-train-image-classification-models-with-mnist-data-and-scikit-learn"></a>Oktatóanyag: Képosztályozási modellek betanítása MNIST-adatok és a scikit-learn segítségével 


Ebben az oktatóanyagban egy gépi tanulási modellt fog betanítni távoli számítási erőforrásokon. A Python-környezetben a betanítás és az Azure Machine Learning munkafolyamatot Jupyter Notebook.  Ezután a notebookot sablonként használhatja a saját gépi tanulási modelljének saját adatokkal való betanításához. Ez az oktatóanyag **egy kétrészes sorozat első része**.  

Ez az oktatóanyag egy egyszerű logisztikai regressziót tanul meg [az MNIST-adatkészlet](http://yann.lecun.com/exdb/mnist/) és a [scikit-learn](https://scikit-learn.org) és a Azure Machine Learning. Az MNIST egy 70 000 szürkeárnyalatos képből álló, népszerű adathalmaz. Minden kép egy 28 x 28 képpontos, kézzel írt számjegy, amely egy 0 és 9 között álló számot képvisel. A cél egy többosztályos besoroló létrehozása, amely képes azonosítani az egyes képeken látható számjegyeket.

Ismerje meg a következő műveleteket:

> [!div class="checklist"]
> * A fejlesztési környezet beállítása.
> * Az adatok elérése és vizsgálata.
> * Egy egyszerű logisztikai regressziós modell betanítása egy távoli fürtön.
> * Tekintse át a betanítás eredményeit, és regisztrálja a legjobb modellt.

Az oktatóanyag második részében megtudhatja, hogyan választhatja ki és helyezheti [üzembe a modellt.](tutorial-deploy-models-with-aml.md)

Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy ingyenes fiókot. Próbálja ki a Azure Machine Learning [ingyenes vagy Azure Machine Learning](https://aka.ms/AMLFree) verzióját.

>[!NOTE]
> A cikkben említett kód az SDK 1.13.0 Azure Machine Learning verziójával lett tesztelve. [](/python/api/overview/azure/ml/intro)

## <a name="prerequisites"></a>Előfeltételek

* Az [Oktatóanyag: Első lépések az első Azure ML-kísérlet létrehozásához lépésekkel:](tutorial-1st-experiment-sdk-setup.md)
    * Munkaterület létrehozása
    * Klónozza az oktatóanyagok jegyzetfüzetét a munkaterület mappájába.
    * Felhőalapú számítási példány létrehozása.

* A klónozott *oktatóanyagok/image-classification-mnist-data* mappában nyissa meg az *img-classification-part1-training.ipynb jegyzetfüzetet.* 


Az oktatóanyag és **a hozzá tartozó utils.py** a [GitHubon](https://github.com/Azure/MachineLearningNotebooks/tree/master/tutorials) is elérhető, ha a saját helyi [környezetében szeretné használni.](how-to-configure-environment.md#local) Az `pip install azureml-sdk[notebooks] azureml-opendatasets matplotlib` oktatóanyag függőségeinek telepítéséhez futtassa a következőt: .

> [!Important]
> A cikk többi része ugyanazt a tartalmat tartalmazza, mint a jegyzetfüzetben.  
>
> Váltson a Jupyter Notebook most, ha olvasni szeretné a kód futtatása miközben futtatja a kódot. 
> Ha egyetlen kódcellát szeretne futtatni egy jegyzetfüzetben, kattintson a kódcellára, majd nyomja le a **Shift+Enter billentyűkombinációt.** Vagy futtassa a teljes jegyzetfüzetet a felső eszköztár **Összes** futtatása gombjára kattintva.

## <a name="set-up-your-development-environment"></a><a name="start"></a>A fejlesztési környezet beállítása

Egy Python-notebookban a fejlesztési munka összes beállítása megadható. A telepítő a következő műveleteket tartalmazza:

* Python-csomagok importálása.
* Csatlakozzon egy munkaterülethez, hogy a helyi számítógép kommunikálni tud a távoli erőforrásokkal.
* Hozzon létre egy kísérletet az összes futtatás nyomon követéséhez.
* Hozzon létre egy távoli számítási célt a betanításhoz.

### <a name="import-packages"></a>Csomagok importálása

Importálja azokat a Python-csomagokat, amelyekre ebben a munkamenetben szüksége lesz. A Azure Machine Learning SDK verzióját is megjeleníti:

```python
%matplotlib inline
import numpy as np
import matplotlib.pyplot as plt

import azureml.core
from azureml.core import Workspace

# check core SDK version number
print("Azure ML SDK Version: ", azureml.core.VERSION)
```

### <a name="connect-to-a-workspace"></a>Csatlakozás munkaterülethez

Hozzon létre egy munkaterület-objektumot a meglévő munkaterületről. `Workspace.from_config()` beolvassaconfig.js **fájlt,** és betölti a részleteket egy nevű `ws` objektumba:

```python
# load workspace configuration from the config.json file in the current folder.
ws = Workspace.from_config()
print(ws.name, ws.location, ws.resource_group, sep='\t')
```

>[!NOTE]
> Előfordulhat, hogy az alábbi kód első futtatásakor a rendszer a hitelesítést kéri a munkaterületen. Kövesse a képernyőn megjelenő utasításokat.

### <a name="create-an-experiment"></a>Kísérlet létrehozása

Hozzon létre egy kísérletet a munkaterületen végrehajtott futtatások nyomon követéséhez. Egy munkaterületen több kísérlet is lehet:

```python
from azureml.core import Experiment
experiment_name = 'Tutorial-sklearn-mnist'

exp = Experiment(workspace=ws, name=experiment_name)
```

### <a name="create-or-attach-an-existing-compute-target"></a>Meglévő számítási cél létrehozása vagy csatolása

A Azure Machine Learning Compute használatával az adattudósok gépi tanulási modelleket képeznek azure-beli virtuális gépek fürtjéhez. Ilyenek például a GPU-támogatással támogatott virtuális gépek. Ebben az oktatóanyagban egy számítási Azure Machine Learning hoz létre betanító környezetként. Az oktatóanyag későbbi, a virtuális gépen való futtatáshoz elküldheti a Python-kódot. 

Az alábbi kód létrehozza a számítási fürtöt, ha azok még nem léteznek a munkaterületen. Beállítja azt a fürtöt, amely 0-ra leméretez, ha nincs használatban, és legfeljebb 4 csomópontig skálázhat fel. 

 **A számítási cél létrehozása körülbelül öt percet vesz igénybe.** Ha a számítási erőforrás már a munkaterületen van, a kód ezt használja, és kihagyja a létrehozási folyamatot.

```python
from azureml.core.compute import AmlCompute
from azureml.core.compute import ComputeTarget
import os

# choose a name for your cluster
compute_name = os.environ.get("AML_COMPUTE_CLUSTER_NAME", "cpu-cluster")
compute_min_nodes = os.environ.get("AML_COMPUTE_CLUSTER_MIN_NODES", 0)
compute_max_nodes = os.environ.get("AML_COMPUTE_CLUSTER_MAX_NODES", 4)

# This example uses CPU VM. For using GPU VM, set SKU to STANDARD_NC6
vm_size = os.environ.get("AML_COMPUTE_CLUSTER_SKU", "STANDARD_D2_V2")


if compute_name in ws.compute_targets:
    compute_target = ws.compute_targets[compute_name]
    if compute_target and type(compute_target) is AmlCompute:
        print('found compute target. just use it. ' + compute_name)
else:
    print('creating a new compute target...')
    provisioning_config = AmlCompute.provisioning_configuration(vm_size=vm_size,
                                                                min_nodes=compute_min_nodes,
                                                                max_nodes=compute_max_nodes)

    # create the cluster
    compute_target = ComputeTarget.create(
        ws, compute_name, provisioning_config)

    # can poll for a minimum number of nodes and for a specific timeout.
    # if no min node count is provided it will use the scale settings for the cluster
    compute_target.wait_for_completion(
        show_output=True, min_node_count=None, timeout_in_minutes=20)

    # For a more detailed view of current AmlCompute status, use get_status()
    print(compute_target.get_status().serialize())
```

Most már rendelkezésre állnak a modell felhőben történő betanításához szükséges csomagok és számítási erőforrások. 

## <a name="explore-data"></a>Adatok feltárása

A modell betanítása előtt meg kell értenie a betanításhoz használt adatokat. Ebben a szakaszban megtanulhatja a következőket:

* Töltse le a MNIST-adathalmaz.
* Jelenítsen meg néhány mintaképet.

### <a name="download-the-mnist-dataset"></a>Az MNIST-adathalmaz letöltése

A Azure Open Datasets a nyers MNIST-adatfájlokat. [Azure Open Datasets](../open-datasets/overview-what-are-open-datasets.md) olyan össze szabott nyilvános adatkészletek, amelyek segítségével forgatókönyv-specifikus funkciókat adhat a gépi tanulási megoldásokhoz a pontosabb modellek érdekében. Minden adatkészletnek van egy megfelelő osztálya, amely ebben az esetben az adatok különböző `MNIST` módokon való lekéréséhez szükséges.

Ez a kód objektumként olvassa be az `FileDataset` adatokat, amely a alosztálya. `Dataset` Egy vagy több fájlra hivatkozik, bármilyen formátumú adattárban vagy `FileDataset` nyilvános URL-címben. A osztály lehetővé teszi a fájlok letöltését vagy csatlakoztatását a számításhoz az adatforrás helyére való hivatkozás létrehozásával. Emellett regisztrálja az Adatkészletet a munkaterületen a betanítás során való egyszerű lekérés érdekében.

Az [adatkészletekkel](how-to-create-register-datasets.md) és azok SDK-ban való használatával kapcsolatos további információkért kövesse az útmutatót.

```python
from azureml.core import Dataset
from azureml.opendatasets import MNIST

data_folder = os.path.join(os.getcwd(), 'data')
os.makedirs(data_folder, exist_ok=True)

mnist_file_dataset = MNIST.get_file_dataset()
mnist_file_dataset.download(data_folder, overwrite=True)

mnist_file_dataset = mnist_file_dataset.register(workspace=ws,
                                                 name='mnist_opendataset',
                                                 description='training and test dataset',
                                                 create_new_version=True)
```

### <a name="display-some-sample-images"></a>Mintaképek megjelenítése

Töltse be a tömörített fájlokat `numpy` tömbökbe. Ezután a `matplotlib` használatával ábrázoljon 30 véletlenszerű képet az adathalmazból, felettük a hozzájuk tartozó címkével. Ehhez a `load_data` lépéshez egy fájlban található `utils.py` függvényre van szükség. Ezt a fájlt a mintamappa tartalmazza. Győződjön meg arról, hogy a jegyzetfüzettel azonos mappába van helyezve. A függvény egyszerűen umpy-tömbökbe elemezi a `load_data` tömörített fájlokat.

```python
# make sure utils.py is in the same directory as this code
from utils import load_data
import glob


# note we also shrink the intensity values (X) from 0-255 to 0-1. This helps the model converge faster.
X_train = load_data(glob.glob(os.path.join(data_folder,"**/train-images-idx3-ubyte.gz"), recursive=True)[0], False) / 255.0
X_test = load_data(glob.glob(os.path.join(data_folder,"**/t10k-images-idx3-ubyte.gz"), recursive=True)[0], False) / 255.0
y_train = load_data(glob.glob(os.path.join(data_folder,"**/train-labels-idx1-ubyte.gz"), recursive=True)[0], True).reshape(-1)
y_test = load_data(glob.glob(os.path.join(data_folder,"**/t10k-labels-idx1-ubyte.gz"), recursive=True)[0], True).reshape(-1)


# now let's show some randomly chosen images from the traininng set.
count = 0
sample_size = 30
plt.figure(figsize=(16, 6))
for i in np.random.permutation(X_train.shape[0])[:sample_size]:
    count = count + 1
    plt.subplot(1, sample_size, count)
    plt.axhline('')
    plt.axvline('')
    plt.text(x=10, y=-10, s=y_train[i], fontsize=18)
    plt.imshow(X_train[i].reshape(28, 28), cmap=plt.cm.Greys)
plt.show()
```

A véletlenszerű képminta a következőket jeleníti meg:

![Véletlenszerű képminta](./media/tutorial-train-models-with-aml/digits.png)

Most már van elképzelése arról, hogy néznek ki ezek a képek, és milyen előrejelzési eredmény várható.

## <a name="train-on-a-remote-cluster"></a>Betanítás távoli fürtön

Ehhez a feladathoz el kell küldenünk a feladatot, hogy a korábban beállított távoli betanító fürtön fusson.  A feladat elküldésének menete:
* Könyvtár létrehozása
* Betanító szkript létrehozása
* Szkript futtatási konfigurációjának létrehozása
* Feladat küldése

### <a name="create-a-directory"></a>Könyvtár létrehozása

Hozzon létre egy kódtárat, amellyel eljuttathatja a szükséges kódot a számítógépéről a távoli erőforrásra.

```python
import os
script_folder = os.path.join(os.getcwd(), "sklearn-mnist")
os.makedirs(script_folder, exist_ok=True)
```

### <a name="create-a-training-script"></a>Betanító szkript létrehozása

A feladatnak a fürtre való elküldéséhez először hozzon létre egy betanító szkriptet. A `train.py` nevű betanító szkript imént létrehozott könyvtárban történő létrehozásához futtassa a következő kódot.

```python
%%writefile $script_folder/train.py

import argparse
import os
import numpy as np
import glob

from sklearn.linear_model import LogisticRegression
import joblib

from azureml.core import Run
from utils import load_data

# let user feed in 2 parameters, the dataset to mount or download, and the regularization rate of the logistic regression model
parser = argparse.ArgumentParser()
parser.add_argument('--data-folder', type=str, dest='data_folder', help='data folder mounting point')
parser.add_argument('--regularization', type=float, dest='reg', default=0.01, help='regularization rate')
args = parser.parse_args()

data_folder = args.data_folder
print('Data folder:', data_folder)

# load train and test set into numpy arrays
# note we scale the pixel intensity values to 0-1 (by dividing it with 255.0) so the model can converge faster.
X_train = load_data(glob.glob(os.path.join(data_folder, '**/train-images-idx3-ubyte.gz'), recursive=True)[0], False) / 255.0
X_test = load_data(glob.glob(os.path.join(data_folder, '**/t10k-images-idx3-ubyte.gz'), recursive=True)[0], False) / 255.0
y_train = load_data(glob.glob(os.path.join(data_folder, '**/train-labels-idx1-ubyte.gz'), recursive=True)[0], True).reshape(-1)
y_test = load_data(glob.glob(os.path.join(data_folder, '**/t10k-labels-idx1-ubyte.gz'), recursive=True)[0], True).reshape(-1)

print(X_train.shape, y_train.shape, X_test.shape, y_test.shape, sep = '\n')

# get hold of the current run
run = Run.get_context()

print('Train a logistic regression model with regularization rate of', args.reg)
clf = LogisticRegression(C=1.0/args.reg, solver="liblinear", multi_class="auto", random_state=42)
clf.fit(X_train, y_train)

print('Predict the test set')
y_hat = clf.predict(X_test)

# calculate accuracy on the prediction
acc = np.average(y_hat == y_test)
print('Accuracy is', acc)

run.log('regularization rate', np.float(args.reg))
run.log('accuracy', np.float(acc))

os.makedirs('outputs', exist_ok=True)
# note file saved in the outputs folder is automatically uploaded into experiment record
joblib.dump(value=clf, filename='outputs/sklearn_mnist_model.pkl')
```

Figyelje meg, hogyan kéri le a szkript az adatokat, és menti a modelleket:

+ A betanító szkript beolvas egy argumentumot, és megkeresi az adatokat tartalmazó könyvtárat. Amikor később elküldi a feladatot, az adattárban a következő argumentumra kell mutatnia: ```parser.add_argument('--data-folder', type=str, dest='data_folder', help='data directory mounting point')```

+ A betanító szkript egy **outputs** nevű könyvtárba menti a modellt. Az ebbe a könyvtárba írt összes fájl automatikusan fel lesz töltve a munkaterületére. A modellt ebből a könyvtárból az oktatóanyag későbbi leckéiben is elérheti. `joblib.dump(value=clf, filename='outputs/sklearn_mnist_model.pkl')`

+ A betanító szkript megköveteli, hogy a fájl megfelelően töltse be `utils.py` az adatkészletet. Az alábbi kód a fájlba másol, hogy a fájl a távoli erőforráson található betanító szkripttel `utils.py` `script_folder` együtt elérhető legyen.

  ```python
  import shutil
  shutil.copy('utils.py', script_folder)
  ```

### <a name="configure-the-training-job"></a>A betanítás feladatának konfigurálása

Hozzon létre [egy ScriptRunConfig](/python/api/azureml-core/azureml.core.scriptrunconfig) objektumot a betanítási feladat konfigurációs részleteinek megadásához, beleértve a betanítási szkriptet, a használni kívánt környezetet és a futtatni kívánt számítási célt. Konfigurálja a ScriptRunConfig parancsprogramot a következő megadásával:

* A szkripteket tartalmazó könyvtár. Az ebben a könyvtárban található összes fájl fel lesz töltve a fürtcsomópontokra végrehajtás céljából.
* A számítási cél. Ebben az esetben a létrehozott Azure Machine Learning számítási fürtöt használja.
* A betanító szkript neve , **train.py.**
* A szkript futtatásához szükséges kódtárakat tartalmazó környezet.
* A betanító szkripthez szükséges argumentumok.

Ebben az oktatóanyagban ez a cél az AmlCompute. A szkriptmappában lévő összes fájl fel lesz töltve a fürtcsomópontokra futtatásra. A **--data_folder** az adatkészlet használatára van beállítva.

Először hozza létre a következő környezeteket: a scikit-learn kódtárat, az adatkészlet eléréséhez szükséges azureml-dataset-runtime- és az azureml-defaults-t, amely a metrikák naplózásának függőségeit tartalmazza. Az azureml-defaults az oktatóanyag 2. részében tartalmazza a modell webszolgáltatásként való üzembe helyezéséhez szükséges függőségeket is.

A környezet definiálása után regisztrálja a munkaterületen az oktatóanyag 2. részében való újrahasználathoz.

```python
from azureml.core.environment import Environment
from azureml.core.conda_dependencies import CondaDependencies

# to install required packages
env = Environment('tutorial-env')
cd = CondaDependencies.create(pip_packages=['azureml-dataset-runtime[pandas,fuse]', 'azureml-defaults'], conda_packages=['scikit-learn==0.22.1'])

env.python.conda_dependencies = cd

# Register environment to re-use later
env.register(workspace=ws)
```

Ezután hozza létre a ScriptRunConfig szkriptet a betanító szkript, a számítási cél és a környezet megadásával.

```python
from azureml.core import ScriptRunConfig

args = ['--data-folder', mnist_file_dataset.as_mount(), '--regularization', 0.5]

src = ScriptRunConfig(source_directory=script_folder,
                      script='train.py', 
                      arguments=args,
                      compute_target=compute_target,
                      environment=env)
```

### <a name="submit-the-job-to-the-cluster"></a>Feladat küldése a fürtnek

Futtassa a kísérletet a ScriptRunConfig objektum küldésvel:

```python
run = exp.submit(config=src)
run
```

Mivel a hívás aszinkron, a feladat  indulása után egy **Előkészítés** vagy Futó állapotot ad vissza.

## <a name="monitor-a-remote-run"></a>Távoli futtatás monitorozása

Az első futtatás összesen körülbelül **10 percet vesz igénybe.** Az ezt követő futtatásokkal azonban, amíg a szkript függőségei nem változnak, a rendszer ugyanazt a rendszerképet használja újra. Így a tároló indítási ideje sokkal gyorsabb.

Mi történik a várakozás közben:

- **Rendszerkép létrehozása:** Létrejön egy Docker-rendszerkép, amely megfelel az Azure ML-környezet által megadott Python-környezetnek. A rendszerkép feltöltődik a munkaterületre. A rendszerkép létrehozása és feltöltése **körülbelül öt percet vesz igénybe.**

  Ez a szakasz egyszer történik meg minden Python-környezetben, mert a tároló a későbbi futtatásokat gyorsítótárazza. A kép létrehozása során a rendszer a futási előzményekbe streameli a naplókat. A rendszerkép létrehozásának előrehaladását ezekkel a naplókval követheti nyomon.

- **Skálázás:** Ha a távoli fürt a jelenleg elérhetőnél több csomópontot igényel a futtatáshoz, a rendszer automatikusan további csomópontokat ad hozzá. A skálázás általában **körülbelül öt percet vesz igénybe.**

- **Futtatva:** Ebben a szakaszban a szükséges szkripteket és fájlokat a rendszer elküldi a számítási célnak. Ezután a rendszer csatlakoztatja vagy átmásolja az adattárolókat. Ezután futtassa **entry_script** a következőt: . Amíg a feladat fut, az **stdout és** **a ./logs** könyvtár a futtatás előzményeibe lesz streamelve. A futtatás előrehaladását ezekkel a naplókval figyelheti.

- **Utófeldolgozás:** A rendszer átmásolja a futtatás **./outputs** könyvtárát a munkaterület futtatáselőzményeibe, így hozzáférhet ezekhez az eredményekhez.

A futó feladat előrehaladását többféleképpen ellenőrizheti. Ez az oktatóanyag egy Jupyter-vezérlőt és egy `wait_for_completion` metódust használ.

### <a name="jupyter-widget"></a>Jupyter-vezérlő

Figyelje meg a futtatás előrehaladását egy [Jupyter-widgettel.](/python/api/azureml-widgets/azureml.widgets) A futtatás beküldéséhez hasonló a widget aszinkron, és 10–15 másodpercenként élő frissítéseket biztosít a feladat befejezéséig:

```python
from azureml.widgets import RunDetails
RunDetails(run).show()
```

A widget a betanítás végén a következőhöz lesz hasonló:

![Jegyzetfüzet widget](./media/tutorial-train-models-with-aml/widget.png)

Ha le kell mondania egy futtatásból, kövesse az [alábbi utasításokat.](./how-to-track-monitor-analyze-runs.md)

### <a name="get-log-results-upon-completion"></a>Naplóeredmények lekérése a befejezéskor

A modell betanítása és monitorozása a háttérben zajlik. További kód futtatása előtt várja meg, amíg a modell betanítás befejeződött. A `wait_for_completion` használatával megmutatja, hogy mikor fejeződött be a modell betanítás:

```python
run.wait_for_completion(show_output=False)  # specify True for a verbose log
```

### <a name="display-run-results"></a>Futtatás eredményeinek megjelenítése

Most már rendelkezik egy távoli fürtön betanított modellel. Kérje le a modell pontosságát:

```python
print(run.get_metrics())
```

A kimenet azt mutatja, hogy a távoli modell pontossága 0,9204:

`{'regularization rate': 0.8, 'accuracy': 0.9204}`

A következő oktatóanyagban részletesebben is megismerheti ezt a modellt.

## <a name="register-model"></a>Modell regisztrálása

A betanító szkript utolsó lépése a fájlt egy nevű könyvtárba írta annak a fürtnek a virtuális gépében, ahol a `outputs/sklearn_mnist_model.pkl` `outputs` feladat fut. `outputs` A egy speciális könyvtár abban az esetben, ha a könyvtárban lévő összes tartalom automatikusan fel lesz töltve a munkaterületre. Ez a tartalom a futtatásrekordban jelenik meg a kísérletben a munkaterületén. A modellfájl így már a munkaterületen is elérhető.

A futtatáshoz társított fájlokat láthatja:

```python
print(run.get_file_names())
```

Regisztrálja a modellt a munkaterületen, így Ön vagy más közreműködők később lekérdezheti, megvizsgálhatja és üzembe helyezhetik ezt a modellt:

```python
# register model
model = run.register_model(model_name='sklearn_mnist',
                           model_path='outputs/sklearn_mnist_model.pkl')
print(model.name, model.id, model.version, sep='\t')
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [aml-delete-resource-group](../../includes/aml-delete-resource-group.md)]

Törölheti csak a számítási Azure Machine Learning is. Az automatikus skálázás azonban be van kapcsolva, és a fürt minimálisan előírt minimális skálázása nulla. Ezért ez az adott erőforrás nem számít fel további számítási díjakat, ha nincs használatban:

```python
# Optionally, delete the Azure Machine Learning Compute cluster
compute_target.delete()
```

## <a name="next-steps"></a>Következő lépések

Ebben a Azure Machine Learning oktatóanyagban a Pythont használta a következő feladatokhoz:

> [!div class="checklist"]
> * A fejlesztési környezet beállítása.
> * Az adatok elérése és vizsgálata.
> * Több modell betanítása egy távoli fürtön a népszerű scikit-learn gépi tanulási kódtár használatával
> * Tekintse át a betanítás részleteit, és regisztrálja a legjobb modellt.

Készen áll a regisztrált modell üzembe helyezésére az oktatóanyag-sorozat következő részében található utasítások alapján:

> [!div class="nextstepaction"]
> [2. oktatóanyag – Modellek üzembe helyezése](tutorial-deploy-models-with-aml.md)
