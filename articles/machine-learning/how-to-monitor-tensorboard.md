---
title: Kísérletek megjelenítése a TensorBoard használatával
titleSuffix: Azure Machine Learning
description: Indítsa el a TensorBoard a kísérleti futtatási előzmények megjelenítéséhez, és azonosítsa a hiperparaméter hangolásának és átképzésének lehetséges területeit.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
author: minxia
ms.author: minxia
ms.date: 02/27/2020
ms.topic: conceptual
ms.custom: how-to
ms.openlocfilehash: 2af82734fb9e1571242eec016f36f691411a8f2e
ms.sourcegitcommit: 956dec4650e551bdede45d96507c95ecd7a01ec9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/09/2021
ms.locfileid: "102518755"
---
# <a name="visualize-experiment-runs-and-metrics-with-tensorboard-and-azure-machine-learning"></a>A kísérlet futtatásának és metrikáinak megjelenítése TensorBoard és Azure Machine Learning


Ebből a cikkből megtudhatja, hogyan tekintheti meg a kísérlet futtatását és mérőszámait a TensorBoard a fő Azure Machine Learning SDK-ban található [ `tensorboard` csomag](/python/api/azureml-tensorboard/) használatával. Miután megtekintette a kísérlet futtatását, jobban beállíthatja és áttaníthatja a gépi tanulási modelleket.

A [TensorBoard](https://www.tensorflow.org/tensorboard/r1/overview) egy webalkalmazás-csomag, amely a kísérlet szerkezetének és teljesítményének vizsgálatára és megismerésére szolgál.

A TensorBoard Azure Machine Learning kísérletekkel való elindításának módja a kísérlet típusától függ:
+ Ha a kísérlet natív módon kiírja a TensorBoard által felhasználható naplófájlokat, például a PyTorch, a Láncolter és a TensorFlow kísérleteket, akkor a [TensorBoard közvetlenül](#launch-tensorboard) a kísérlet futtatási előzményeiből indítható el. 

+ Olyan kísérletek esetén, amelyek nem rendelkeznek natív módon kimeneti TensorBoard (például a Scikit-Learn vagy a Azure Machine Learning kísérletekkel [) `export_to_tensorboard()` , a](#export) futtatási előzményeket TensorBoard-naplókként és a TensorBoard indításával exportálhatja. 

> [!TIP]
> A jelen dokumentumban található információk elsősorban olyan adatszakértők és fejlesztők számára készültek, akik figyelni szeretnék a modell betanítási folyamatát. Ha Ön olyan rendszergazda, aki az Azure Machine learning erőforrás-felhasználásának és eseményeinek figyelését érdekli, például a kvótákat, a befejezett képzések futtatását vagy az elkészült modell üzembe helyezését, tekintse meg a [figyelés Azure Machine learning](monitor-azure-machine-learning.md).

## <a name="prerequisites"></a>Előfeltételek

* A TensorBoard elindításához és a kísérlet futtatási előzményeinek megtekintéséhez a kísérleteknek előzőleg engedélyeznie kell a naplózást, hogy nyomon kövessék a metrikákat és a teljesítményt.  
* A dokumentumban szereplő kód a következő környezetek bármelyikében futtatható: 
    * Azure Machine Learning számítási példány – nincs szükség letöltésre vagy telepítésre
        * Fejezze be a következő [oktatóanyagot: telepítési környezet és munkaterület](tutorial-1st-experiment-sdk-setup.md) egy dedikált notebook-kiszolgáló létrehozásához az SDK-val és a minta adattárral.
        * A notebook-kiszolgáló Samples (minták) mappájában keresse meg a két befejezett és kibontott jegyzetfüzetet a következő könyvtárakra való navigálás során:
            * **útmutató – a azureml > Track-and-monitor-kísérletek > tensorboard > exportálás-futtatási előzmények – tensorboard > exportálás-futtatási előzmények – tensorboard. ipynb**
            * **útmutató – azureml > Track-and-monitor-kísérletek > tensorboard > tensorboard > tensorboard. ipynb**
    * Saját Juptyer notebook-kiszolgáló
       * [Telepítse a Azure Machine learning SDK](/python/api/overview/azure/ml/install) -t a `tensorboard` További
        * [Hozzon létre egy Azure Machine learning munkaterületet](how-to-manage-workspace.md).  
        * [Hozzon létre egy munkaterület-konfigurációs fájlt](how-to-configure-environment.md#workspace).

## <a name="option-1-directly-view-run-history-in-tensorboard"></a>1. lehetőség: közvetlenül a futtatási előzmények megtekintése a TensorBoard-ben

Ez a beállítás olyan kísérletekhez használható, amelyek natív módon kimeneti naplófájlokat TensorBoard, például PyTorch, láncolási és TensorFlow kísérletek. Ha ez nem igaz a kísérletre, használja helyette [a `export_to_tensorboard()` metódust](#export) .

Az alábbi mintakód a [MNIST demo kísérletet](https://raw.githubusercontent.com/tensorflow/tensorflow/r1.8/tensorflow/examples/tutorials/mnist/mnist_with_summaries.py) használja a TensorFlow adattárában egy távoli számítási célpontban, Azure Machine learning a számítást. Ezután konfigurálja és elindítja a futtatást a TensorFlow modell betanításához, majd indítsa el a TensorBoard a TensorFlow kísérlettel.

### <a name="set-experiment-name-and-create-project-folder"></a>A kísérlet nevének beállítása és a Project mappa létrehozása

Itt nevezjük el a kísérletet, és létrehozjuk a mappát. 
 
```python
from os import path, makedirs
experiment_name = 'tensorboard-demo'

# experiment folder
exp_dir = './sample_projects/' + experiment_name

if not path.exists(exp_dir):
    makedirs(exp_dir)

```

### <a name="download-tensorflow-demo-experiment-code"></a>TensorFlow demo-kísérlet kódjának letöltése

A TensorFlow adattárának MNIST-bemutatója kiterjedt TensorBoard-kialakítással rendelkezik. Nem kell módosítania a bemutató kódját, hogy működjön a Azure Machine Learning. A következő kódban letöltjük a MNIST-kódot, és elmentjük az újonnan létrehozott kísérlet mappájába.

```python
import requests
import os

tf_code = requests.get("https://raw.githubusercontent.com/tensorflow/tensorflow/r1.8/tensorflow/examples/tutorials/mnist/mnist_with_summaries.py")
with open(os.path.join(exp_dir, "mnist_with_summaries.py"), "w") as file:
    file.write(tf_code.text)
```
A MNIST-programkódban mnist_with_summaries. a. a....... `tf.summary.scalar()`  `tf.summary.histogram()` .. sorban láthatja, hogy vannak-e a hívások `tf.summary.FileWriter()` . Ezek a metódusok a kísérletek csoport, napló és címke kulcs mérőszámait futtatják a futtatási előzményekben. `tf.summary.FileWriter()`Ez különösen fontos, mivel a naplózott kísérleti mérőszámokból származó adatokat szerializálja, ami lehetővé teszi a TensorBoard számára a vizualizációk létrehozását.

 ### <a name="configure-experiment"></a>Kísérlet konfigurálása

A következőben konfiguráljuk a kísérletet, és beállítja a címtárakat a naplókhoz és az adatkezeléshez. Ezeket a naplókat a rendszer feltölti a futtatási előzményekbe, amelyek TensorBoard később hozzáférnek.

> [!Note]
> Ehhez a TensorFlow-példához telepítenie kell a TensorFlow-t a helyi gépre. A TensorBoard modulnak (azaz a TensorFlow-nek is) elérhetőnek kell lennie a notebook kerneléhez, mivel a helyi gép a TensorBoard-t futtatja.

```Python
import azureml.core
from azureml.core import Workspace
from azureml.core import Experiment

ws = Workspace.from_config()

# create directories for experiment logs and dataset
logs_dir = os.path.join(os.curdir, "logs")
data_dir = os.path.abspath(os.path.join(os.curdir, "mnist_data"))

if not path.exists(data_dir):
    makedirs(data_dir)

os.environ["TEST_TMPDIR"] = data_dir

# Writing logs to ./logs results in their being uploaded to the run history,
# and thus, made accessible to our TensorBoard instance.
args = ["--log_dir", logs_dir]

# Create an experiment
exp = Experiment(ws, experiment_name)
```

### <a name="create-a-cluster-for-your-experiment"></a>Fürt létrehozása a kísérlethez
Létrehozunk egy AmlCompute-fürtöt a kísérlethez, de a kísérletek bármilyen környezetben létrehozhatók, és továbbra is elindítható a TensorBoard a kísérlet futtatási előzményeivel. 

```Python
from azureml.core.compute import ComputeTarget, AmlCompute

cluster_name = "cpu-cluster"

cts = ws.compute_targets
found = False
if cluster_name in cts and cts[cluster_name].type == 'AmlCompute':
   found = True
   print('Found existing compute target.')
   compute_target = cts[cluster_name]
if not found:
    print('Creating a new compute target...')
    compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_D2_V2', 
                                                           max_nodes=4)

    # create the cluster
    compute_target = ComputeTarget.create(ws, cluster_name, compute_config)

compute_target.wait_for_completion(show_output=True, min_node_count=None)

# use get_status() to get a detailed status for the current cluster. 
# print(compute_target.get_status().serialize())
```

[!INCLUDE [low-pri-note](../../includes/machine-learning-low-pri-vm.md)]

### <a name="configure-and-submit-training-run"></a>Betanítási Futtatás konfigurálása és beküldése

ScriptRunConfig objektum létrehozásával konfigurálhat egy betanítási feladatot.

```Python
from azureml.core import ScriptRunConfig
from azureml.core import Environment

# Here we will use the TensorFlow 2.2 curated environment
tf_env = Environment.get(ws, 'AzureML-TensorFlow-2.2-GPU')

src = ScriptRunConfig(source_directory=exp_dir,
                      script='mnist_with_summaries.py',
                      arguments=args,
                      compute_target=compute_target,
                      environment=tf_env)
run = exp.submit(src)
```

### <a name="launch-tensorboard"></a>TensorBoard elindítása

A TensorBoard a Futtatás vagy a befejezés után indítható el. A következő példában létrehozunk egy TensorBoard, `tb` amely végrehajtja a kísérlet futtatási előzményeit a alkalmazásban `run` , majd elindítja a TensorBoard a `start()` metódussal. 
  
A [TensorBoard konstruktora](/python/api/azureml-tensorboard/azureml.tensorboard.tensorboard) futtatja a futtatások tömbjét, ezért ne feledje, hogy egyetlen elemből álló tömbként adja át azt.

```python
from azureml.tensorboard import Tensorboard

tb = Tensorboard([run])

# If successful, start() returns a string with the URI of the instance.
tb.start()

# After your job completes, be sure to stop() the streaming otherwise it will continue to run. 
tb.stop()
```

> [!Note]
> Habár ez a példa TensorFlow használ, a TensorBoard könnyen használható a PyTorch vagy a Chainer használatával. A TensorFlow-nek elérhetőnek kell lennie a TensorBoard-t futtató gépen, de nem szükséges a PyTorch vagy a láncolási számításokat végző gépen. 


<a name="export"></a>

## <a name="option-2-export-history-as-log-to-view-in-tensorboard"></a>2. lehetőség: előzmények exportálása naplóként a TensorBoard megtekintéséhez

A következő kód egy minta-kísérletet állít be, megkezdi a naplózási folyamatot a Azure Machine Learning futtatási előzmények API-k használatával, és a kísérleti futtatási előzményeket a vizualizációk számára a TensorBoard által használt naplókba exportálja. 

### <a name="set-up-experiment"></a>Kísérlet beállítása

A következő kód egy új kísérletet állít be, és a futtatási könyvtárat nevezi `root_run` . 

```python
from azureml.core import Workspace, Experiment
import azureml.core

# set experiment name and run name
ws = Workspace.from_config()
experiment_name = 'export-to-tensorboard'
exp = Experiment(ws, experiment_name)
root_run = exp.start_logging()
```

Itt betöltjük a diabétesz-adathalmazt – egy beépített, kis adatkészletet, amely a scikit-Learn szolgáltatáshoz tartozik, és a teszt-és betanítási csoportokra osztja azokat.

```Python
from sklearn.datasets import load_diabetes
from sklearn.linear_model import Ridge
from sklearn.metrics import mean_squared_error
from sklearn.model_selection import train_test_split
X, y = load_diabetes(return_X_y=True)
columns = ['age', 'gender', 'bmi', 'bp', 's1', 's2', 's3', 's4', 's5', 's6']
x_train, x_test, y_train, y_test = train_test_split(X, y, test_size=0.2, random_state=0)
data = {
    "train":{"x":x_train, "y":y_train},        
    "test":{"x":x_test, "y":y_test}
}
```

### <a name="run-experiment-and-log-metrics"></a>Kísérlet és naplók metrikáinak futtatása

Ebben a kódban egy lineáris regressziós modellt és a log Key mérőszámokat, az Alpha együtthatót, `alpha` és az azt jelenti, `mse` hogy a futtatási előzményekben.

```Python
from tqdm import tqdm
alphas = [.1, .2, .3, .4, .5, .6 , .7]
# try a bunch of alpha values in a Linear Regression (aka Ridge regression) mode
for alpha in tqdm(alphas):
  # create child runs and fit lines for the resulting models
  with root_run.child_run("alpha" + str(alpha)) as run:
 
   reg = Ridge(alpha=alpha)
   reg.fit(data["train"]["x"], data["train"]["y"])    
 
   preds = reg.predict(data["test"]["x"])
   mse = mean_squared_error(preds, data["test"]["y"])
   # End train and eval

# log alpha, mean_squared_error and feature names in run history
   root_run.log("alpha", alpha)
   root_run.log("mse", mse)
```

### <a name="export-runs-to-tensorboard"></a>A futtatások exportálása a TensorBoard

Az SDK [export_to_tensorboard ()](/python/api/azureml-tensorboard/azureml.tensorboard.export) metódusával exportálhatja az Azure Machine learning-kísérlet futtatási előzményeit a tensorboard-naplókba, így megtekintheti őket a tensorboard-on keresztül.  

A következő kódban létrehozjuk a mappát az `logdir` aktuális munkakönyvtárban. Ebben a mappában fogjuk exportálni a kísérlet futtatási előzményeit és naplóit, majd `root_run` megjelölik a futtatást befejezettként. 

```Python
from azureml.tensorboard.export import export_to_tensorboard
import os

logdir = 'exportedTBlogs'
log_path = os.path.join(os.getcwd(), logdir)
try:
    os.stat(log_path)
except os.error:
    os.mkdir(log_path)
print(logdir)

# export run history for the project
export_to_tensorboard(root_run, logdir)

root_run.complete()
```

> [!Note]
> Egy adott futtatást TensorBoard is exportálhat a Futtatás nevének megadásával.  `export_to_tensorboard(run_name, logdir)`

### <a name="start-and-stop-tensorboard"></a>TensorBoard elindítása és leállítása
A kísérlet futtatási előzményeinek exportálása után a TensorBoard a [Start ()](/python/api/azureml-tensorboard/azureml.tensorboard.tensorboard#start-start-browser-false-) metódussal indítható el. 

```Python
from azureml.tensorboard import Tensorboard

# The TensorBoard constructor takes an array of runs, so be sure and pass it in as a single-element array here
tb = Tensorboard([], local_root=logdir, port=6006)

# If successful, start() returns a string with the URI of the instance.
tb.start()
```

Ha elkészült, győződjön meg arról, hogy a TensorBoard objektum [Leállítás ()](/python/api/azureml-tensorboard/azureml.tensorboard.tensorboard#stop--) metódusát hívja meg. Ellenkező esetben a TensorBoard továbbra is futni fog, amíg le nem állítja a notebook kernelét. 

```python
tb.stop()
```

## <a name="next-steps"></a>Következő lépések

Ebben az útmutatóban két kísérletet hozott létre, és megtanulta, hogyan indíthat el TensorBoard a futtatási előzményekkel a lehetséges hangolási és újraképzési területek azonosítása érdekében. 

* Ha elégedett a modellel, látogasson el a [modell üzembe helyezésével](how-to-deploy-and-where.md) foglalkozó cikkre. 
* További információ a [hiperparaméter hangolásáról](how-to-tune-hyperparameters.md).