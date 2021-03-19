---
title: 'Rendszerképek besorolása oktatóanyag: a betanítási modellek'
titleSuffix: Azure Machine Learning
description: A Azure Machine Learning használatával a képbesorolási modell betanításához a scikit-Learn egy Python-Jupyter Notebook. Ez az oktatóanyag a kettő első része.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: sdgilley
ms.author: sgilley
ms.date: 09/28/2020
ms.custom: seodec18, devx-track-python
ms.openlocfilehash: 85dea807ee09338e7f0e9e388f6b196fd3beef33
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "104588664"
---
# <a name="tutorial-train-image-classification-models-with-mnist-data-and-scikit-learn"></a>Oktatóanyag: képosztályozási modellek betanítása MNIST-adatokkal és scikit-Learn 


Ebben az oktatóanyagban egy gépi tanulási modellt fog betanítani a távoli számítási erőforrásokra. A Azure Machine Learning betanítási és üzembe helyezési munkafolyamatát egy Python-Jupyter Notebook használhatja.  Ezután a notebookot sablonként használhatja a saját gépi tanulási modelljének saját adatokkal való betanításához. Ez az oktatóanyag **egy kétrészes sorozat első része**.  

Ez az oktatóanyag egy egyszerű logisztikai regressziót mutat be a [MNIST](http://yann.lecun.com/exdb/mnist/) adatkészlettel és a [scikit-Learn](https://scikit-learn.org) Azure Machine learning használatával. Az MNIST egy 70 000 szürkeárnyalatos képből álló, népszerű adathalmaz. Minden rendszerkép egy 28 x 28 képpontos, a nulla és kilenc közötti számot jelölő, kézzel írt számjegy. A cél egy többosztályos besoroló létrehozása, amely képes azonosítani az egyes képeken látható számjegyeket.

Ismerje meg, hogyan végezheti el a következő műveleteket:

> [!div class="checklist"]
> * Állítsa be a fejlesztési környezetet.
> * Az adathozzáférés és a vizsgálat.
> * Egy egyszerű logisztikai regressziós modell betanítása egy távoli fürtön.
> * Tekintse át a képzés eredményeit, és regisztrálja a legjobb modellt.

Megtudhatja, hogyan választhatja ki a modelleket, és hogyan telepítheti azt az [oktatóanyag második részében](tutorial-deploy-models-with-aml.md).

Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy ingyenes fiókot. Próbálja ki a [Azure Machine learning ingyenes vagy fizetős verzióját](https://aka.ms/AMLFree) még ma.

>[!NOTE]
> A cikkben ismertetett kód [Azure Machine learning SDK](/python/api/overview/azure/ml/intro) 1.13.0-verzióval lett tesztelve.

## <a name="prerequisites"></a>Előfeltételek

* Fejezze be az [oktatóanyagot: első lépésként hozza létre első Azure ml-kísérletét](tutorial-1st-experiment-sdk-setup.md) :
    * Munkaterület létrehozása
    * Az oktatóanyagok notebookjának klónozása a munkaterületen lévő mappába.
    * Felhőalapú számítási példány létrehozása.

* A klónozott *oktatóanyagok/képbesorolás-mnist-adat* mappában Nyissa meg az *IMG-besorolás-part1-Training. ipynb* notebookot. 


Az oktatóanyag és a kapcsolódó **utils.py** -fájl a [githubon](https://github.com/Azure/MachineLearningNotebooks/tree/master/tutorials) is elérhető, ha a saját [helyi környezetében](how-to-configure-environment.md#local)szeretné használni. Az `pip install azureml-sdk[notebooks] azureml-opendatasets matplotlib` oktatóanyaghoz tartozó függőségek telepítéséhez futtassa a parancsot.

> [!Important]
> A cikk többi része ugyanazt a tartalmat tartalmazza, mint amit a jegyzetfüzetben lát.  
>
> Váltson a Jupyter Notebookra most, ha a kód futtatása közben szeretné olvasni. 
> Ha egyetlen kód cellát szeretne futtatni egy jegyzetfüzetben, kattintson a kód cellára, és nyomja le a **SHIFT + ENTER billentyűkombinációt**. Vagy futtassa a teljes jegyzetfüzetet úgy, hogy az **összes futtatása** lehetőséget választja a felső eszköztáron.

## <a name="set-up-your-development-environment"></a><a name="start"></a>A fejlesztési környezet beállítása

Egy Python-notebookban a fejlesztési munka összes beállítása megadható. A telepítő a következő műveleteket tartalmazza:

* Python-csomagok importálása.
* Kapcsolódjon egy munkaterülethez, hogy a helyi számítógép képes legyen kommunikálni a távoli erőforrásokkal.
* Hozzon létre egy kísérletet az összes Futtatás nyomon követéséhez.
* Hozzon létre egy távoli számítási célt, amelyet a képzéshez használhat.

### <a name="import-packages"></a>Csomagok importálása

Importálja azokat a Python-csomagokat, amelyekre ebben a munkamenetben szüksége lesz. Megjeleníti a Azure Machine Learning SDK verzióját is:

```python
%matplotlib inline
import numpy as np
import matplotlib.pyplot as plt

import azureml.core
from azureml.core import Workspace

# check core SDK version number
print("Azure ML SDK Version: ", azureml.core.VERSION)
```

### <a name="connect-to-a-workspace"></a>Kapcsolódás munkaterülethez

Hozzon létre egy munkaterület-objektumot a meglévő munkaterületről. `Workspace.from_config()` beolvassa a (z) **config.js** fájlt, és betölti a részleteket egy nevű objektumba `ws` :

```python
# load workspace configuration from the config.json file in the current folder.
ws = Workspace.from_config()
print(ws.name, ws.location, ws.resource_group, sep='\t')
```

>[!NOTE]
> A következő kód első futtatásakor előfordulhat, hogy a rendszer a munkaterületre történő hitelesítést kéri. Kövesse a képernyőn megjelenő utasításokat.

### <a name="create-an-experiment"></a>Kísérlet létrehozása

Hozzon létre egy kísérletet a munkaterületen végrehajtott futtatások nyomon követéséhez. Egy munkaterület több kísérlettel is rendelkezhet:

```python
from azureml.core import Experiment
experiment_name = 'Tutorial-sklearn-mnist'

exp = Experiment(workspace=ws, name=experiment_name)
```

### <a name="create-or-attach-an-existing-compute-target"></a>Meglévő számítási cél létrehozása vagy csatolása

A felügyelt Azure Machine Learning számítási szolgáltatással az adatszakértők a gépi tanulási modelleket az Azure-beli virtuális gépek fürtjén is betanítják. Ilyenek például a GPU-támogatással rendelkező virtuális gépek. Ebben az oktatóanyagban Azure Machine Learning számítást hoz létre képzési környezetként. Az oktatóanyag későbbi részében a virtuális gépen való futtatáshoz a Python-kódot kell elküldenie. 

Az alábbi kód létrehozza a számítási fürtöket, ha azok még nem léteznek a munkaterületen. Egy olyan fürtöt állít be, amely nincs használatban 0-ra, és legfeljebb 4 csomópontra méretezhető. 

 **A számítási cél létrehozása körülbelül öt percet vesz igénybe.** Ha a számítási erőforrás már szerepel a munkaterületen, a kód ezt használja, és kihagyja a létrehozási folyamatot.

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

A modellek betanítása előtt meg kell ismernie a betanításához használt adattípust. Ebben a szakaszban megtanulhatja a következőket:

* Töltse le a MNIST adatkészletet.
* Néhány mintakép megjelenítése.

### <a name="download-the-mnist-dataset"></a>Az MNIST-adathalmaz letöltése

A nyers MNIST-adatfájlok beszerzéséhez használja az Azure Open adatkészleteket. Az [Azure Open-adatkészletek](../open-datasets/overview-what-are-open-datasets.md) olyan beszerzett nyilvános adatkészletek, amelyekkel pontosabb modelleket adhat hozzá a gépi tanulási megoldásokhoz. Ebben az esetben minden adatkészlet rendelkezik egy megfelelő osztállyal, amely `MNIST` különböző módokon kéri le az adatokat.

Ez a kód egy objektumként kérdezi le az adatmennyiséget `FileDataset` , amely a következő alosztálya: `Dataset` . A egy `FileDataset` vagy több fájlra hivatkozik az adattárolókban vagy a nyilvános URL-címekben. Az osztály lehetővé teszi a fájlok a számításhoz való letöltését vagy csatlakoztatását az adatforrás helyére mutató hivatkozás létrehozásával. Emellett regisztrálja az adatkészletet a munkaterületen a betanítás során történő egyszerű lekéréshez.

Az adatkészletekről és azok használatáról az SDK-ban kövesse az [útmutató](how-to-create-register-datasets.md) című témakört.

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

Töltse be a tömörített fájlokat `numpy` tömbökbe. Ezután a `matplotlib` használatával ábrázoljon 30 véletlenszerű képet az adathalmazból, felettük a hozzájuk tartozó címkével. Ehhez a lépéshez egy `load_data` fájlhoz tartozó függvényre van szükség `utils.py` . Ezt a fájlt a mintamappa tartalmazza. Győződjön meg arról, hogy az a jegyzetfüzettel megegyező mappába van helyezve. A `load_data` függvény egyszerűen elemzi a tömörített fájlokat a NumPy tömbökben.

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

![Véletlenszerűen kiválasztott képek](./media/tutorial-train-models-with-aml/digits.png)

Most már van elképzelése arról, hogy néznek ki ezek a képek, és milyen előrejelzési eredmény várható.

## <a name="train-on-a-remote-cluster"></a>Betanítás távoli fürtön

Ehhez a feladathoz be kell nyújtania a feladatot a korábban beállított távoli képzési fürtön való futtatáshoz.  A feladat elküldésének menete:
* Könyvtár létrehozása
* Betanító szkript létrehozása
* Parancsfájl futtatási konfigurációjának létrehozása
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

+ A betanítási parancsfájl beolvas egy argumentumot, amely az adatokat tartalmazó könyvtárat keresi. Amikor később elküldi a feladatot, az adattárban a következő argumentumra kell mutatnia: ```parser.add_argument('--data-folder', type=str, dest='data_folder', help='data directory mounting point')```

+ A betanítási szkript egy **kimenet** nevű könyvtárba menti a modellt. Az ebbe a könyvtárba írt összes fájl automatikusan fel lesz töltve a munkaterületére. Ehhez a címtárhoz az oktatóanyagban később férhet hozzá a modellhez. `joblib.dump(value=clf, filename='outputs/sklearn_mnist_model.pkl')`

+ A betanítási parancsfájl használatához a fájlnak `utils.py` megfelelően be kell töltenie az adatkészletet. A következő kód bemásolja a `utils.py` `script_folder` fájlt, hogy a fájl elérhető legyen a távoli erőforráson lévő betanítási parancsfájllal együtt.

  ```python
  import shutil
  shutil.copy('utils.py', script_folder)
  ```

### <a name="configure-the-training-job"></a>A betanítási feladatok konfigurálása

Hozzon létre egy [ScriptRunConfig](/python/api/azureml-core/azureml.core.scriptrunconfig) objektumot a betanítási feladatok konfigurációs adatainak megadásához, beleértve a betanítási parancsfájlt, a használni kívánt környezetet és a futtatáshoz szükséges számítási célt. Konfigurálja a ScriptRunConfig az alábbiak megadásával:

* A szkripteket tartalmazó könyvtár. Az ebben a könyvtárban található összes fájl fel lesz töltve a fürtcsomópontokra végrehajtás céljából.
* A számítási cél. Ebben az esetben a létrehozott Azure Machine Learning számítási fürtöt használja.
* A betanítási szkript neve, **Train.py**.
* A parancsfájl futtatásához szükséges kódtárakat tartalmazó környezet.
* A betanítási parancsfájlhoz szükséges argumentumok.

Ebben az oktatóanyagban ez a cél a AmlCompute. A parancsfájl mappájában lévő összes fájl a fürt csomópontjaiba lesz feltöltve futtatásra. A **--data_folder** az adatkészlet használatára van beállítva.

Először hozza létre a következőt tartalmazó környezetet: a scikit-Learn Library, a azureml-DataSet-Runtime, amely az adatkészlet eléréséhez szükséges, valamint a azureml – alapértékek, amelyek a naplózási metrikákkal kapcsolatos függőségeket tartalmazzák. A azureml-alapértékek a modell webszolgáltatásként való üzembe helyezéséhez szükséges függőségeket is tartalmazzák az oktatóanyag 2. részében.

A környezet meghatározása után regisztrálja a munkaterületet, hogy újra használhassa azt az oktatóanyag 2. részében.

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

Ezután hozza létre a ScriptRunConfig a képzési parancsfájl, a számítási cél és a környezet megadásával.

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

Futtassa a kísérletet a ScriptRunConfig objektum elküldésével:

```python
run = exp.submit(config=src)
run
```

Mivel a hívás aszinkron, egy **előkészítési** vagy **futtatási** állapotot ad vissza, amint a feladatot elindítják.

## <a name="monitor-a-remote-run"></a>Távoli futtatás monitorozása

Összességében az első futtatás **körülbelül 10 percet** vesz igénybe. A későbbi futtatások esetében azonban, ha a parancsfájl függőségei nem változnak, ugyanazt a rendszerképet használja a rendszer. Így a tároló indítási ideje sokkal gyorsabb.

Mi történik a várakozás közben:

- **Rendszerkép létrehozása**: a rendszer létrehoz egy Docker-rendszerképet, amely megfelel az Azure ml-környezet által megadott Python-környezetnek. A rendszerkép feltöltődik a munkaterületre. A képek létrehozása és feltöltése **körülbelül öt percet** vesz igénybe.

  Ez a szakasz egyszer fordul elő minden Python-környezetben, mert a tároló gyorsítótárazva lesz a későbbi futtatásokhoz. A kép létrehozása során a rendszer a futási előzményekbe streameli a naplókat. A képlétrehozási folyamat nyomon követhető a naplók használatával.

- **Skálázás**: Ha a távoli fürtnek több csomópontra van szüksége a jelenleg elérhető futtatáshoz, a további csomópontok automatikusan hozzáadódnak. A skálázás általában **körülbelül öt percet** vesz igénybe.

- **Futtatás**: ebben a szakaszban a szükséges parancsfájlokat és fájlokat a rendszer elküldi a számítási célra. Az adattárolók csatlakoztatása vagy másolása megtörtént. Ezután a **entry_script** fut. Amíg a feladatok futnak, az **StdOut** és a **./logs** könyvtár a futtatási előzményekre van továbbítva. A Futtatás előrehaladását a naplók használatával figyelheti.

- **Feldolgozás után** a rendszer átmásolja a futtatáshoz tartozó **./outputs** könyvtárat a munkaterület futtatási előzményeibe, így elérheti ezeket az eredményeket.

A futó feladatok előrehaladását többféleképpen is megtekintheti. Ez az oktatóanyag egy Jupyter widgetet és egy `wait_for_completion` metódust használ.

### <a name="jupyter-widget"></a>Jupyter-vezérlő

Tekintse meg a Futtatás folyamatát egy [Jupyter widgettel](/python/api/azureml-widgets/azureml.widgets). A futtatási beküldéshez hasonlóan a widget aszinkron módon működik, és 10 – 15 másodpercig élő frissítéseket biztosít a feladatok befejezéséig:

```python
from azureml.widgets import RunDetails
RunDetails(run).show()
```

A widget a következőhöz hasonlóan fog kinézni a képzés végén:

![Jegyzetfüzet widget](./media/tutorial-train-models-with-aml/widget.png)

Ha le kell mondania egy futtatást, kövesse [az alábbi utasításokat](./how-to-manage-runs.md).

### <a name="get-log-results-upon-completion"></a>Naplóeredmények lekérése a befejezéskor

A modell betanítása és monitorozása a háttérben zajlik. Várjon, amíg a modell befejezte a képzést, mielőtt további kódokat futtasson. `wait_for_completion`A következő paranccsal jelenítheti meg, hogy mikor fejeződik be a modell betanítása:

```python
run.wait_for_completion(show_output=False)  # specify True for a verbose log
```

### <a name="display-run-results"></a>Futtatás eredményeinek megjelenítése

Most már rendelkezik egy távoli fürtön betanított modellel. Kérje le a modell pontosságát:

```python
print(run.get_metrics())
```

A kimenetben látható, hogy a távoli modell pontossága 0,9204:

`{'regularization rate': 0.8, 'accuracy': 0.9204}`

A következő oktatóanyagban részletesebben megismerheti ezt a modellt.

## <a name="register-model"></a>Modell regisztrálása

A betanítási parancsfájl utolsó lépése a fájlt `outputs/sklearn_mnist_model.pkl` egy, a fürt virtuális gépe nevű könyvtárban írta, `outputs` amelyben a feladatot futtatja. `outputs` egy speciális könyvtár, amelyben a címtárban található összes tartalom automatikusan fel lesz töltve a munkaterületre. Ez a tartalom a futtatásrekordban jelenik meg a kísérletben a munkaterületén. Így a modell fájlja már elérhető a munkaterületen is.

A futtatáshoz tartozó fájlok láthatók:

```python
print(run.get_file_names())
```

Regisztrálja a modellt a munkaterületen, hogy Ön vagy más közreműködők később is lekérdezzenek, megvizsgálják és üzembe helyezhetik a modellt:

```python
# register model
model = run.register_model(model_name='sklearn_mnist',
                           model_path='outputs/sklearn_mnist_model.pkl')
print(model.name, model.id, model.version, sep='\t')
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [aml-delete-resource-group](../../includes/aml-delete-resource-group.md)]

A Azure Machine Learning számítási fürtöt is törölheti. Az autoskálázás azonban be van kapcsolva, és a fürt minimális értéke nulla. Így az adott erőforrás nem jár további számítási költségekkel, ha nincs használatban:

```python
# Optionally, delete the Azure Machine Learning Compute cluster
compute_target.delete()
```

## <a name="next-steps"></a>Következő lépések

Ebben a Azure Machine Learning oktatóanyagban a Pythont használtuk a következő feladatokhoz:

> [!div class="checklist"]
> * Állítsa be a fejlesztési környezetet.
> * Az adathozzáférés és a vizsgálat.
> * Több modell betanítása egy távoli fürtön a népszerű scikit-Learn Machine learning-kódtár használatával
> * Tekintse át a képzés részleteit, és regisztrálja a legjobb modellt.

Készen áll a regisztrált modell üzembe helyezésére az oktatóanyag-sorozat következő részében található utasítások használatával:

> [!div class="nextstepaction"]
> [2. oktatóanyag – Modellek üzembe helyezése](tutorial-deploy-models-with-aml.md)
