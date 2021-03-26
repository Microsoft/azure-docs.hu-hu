---
title: Apache Spark használata gépi tanulási folyamatokban (előzetes verzió)
titleSuffix: Azure Machine Learning
description: Csatolja az Azure szinapszis Analytics-munkaterületet az Azure Machine learning-folyamathoz, hogy Apache Spark használjon az adatkezeléshez.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: laobri
author: lobrien
ms.date: 03/04/2021
ms.topic: conceptual
ms.custom: how-to, synapse-azureml
ms.openlocfilehash: b03915608c6143a9e205ba1a1e08e411b8aa9093
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/23/2021
ms.locfileid: "104868647"
---
# <a name="how-to-use-apache-spark-powered-by-azure-synapse-analytics-in-your-machine-learning-pipeline-preview"></a>Az Azure szinapszis Analytics által működtetett Apache Spark használata a Machine learning-folyamatban (előzetes verzió)

Ebből a cikkből megtudhatja, hogyan használhatók az Azure szinapszis Analytics szolgáltatást használó Apache Spark-készletek a Azure Machine Learning folyamat adatelőkészítési lépéseinek számítási céljaként. Megtudhatja, hogyan használhat egy adott folyamat a számítási erőforrásokat az adott lépéshez, például az adatok előkészítéséhez vagy betanításához. Látni fogja, hogyan készüljön fel az adatgyűjtés a Spark-lépéshez, és hogyan történik a következő lépéshez. 

[!INCLUDE [preview disclaimer](../../includes/machine-learning-preview-generic-disclaimer.md)]

## <a name="prerequisites"></a>Előfeltételek

* Hozzon létre egy [Azure Machine learning munkaterületet](how-to-manage-workspace.md) az összes folyamat erőforrásának tárolásához.

* [Állítsa be a fejlesztési környezetet](how-to-configure-environment.md) a Azure Machine learning SDK telepítéséhez, vagy használjon egy [Azure Machine learning számítási példányt](concept-compute-instance.md) az SDK-val, amely már telepítve van.

* Hozzon létre egy Azure szinapszis Analytics-munkaterületet és Apache Spark készletet (lásd: gyors útmutató [: kiszolgáló nélküli Apache Spark-készlet létrehozása a szinapszis Studio használatával](../synapse-analytics/quickstart-create-apache-spark-pool-studio.md)). 

## <a name="link-your-azure-machine-learning-workspace-and-azure-synapse-analytics-workspace"></a>Azure Machine Learning munkaterület és az Azure szinapszis Analytics-munkaterület összekapcsolása 

A Apache Spark-készleteket Azure szinapszis Analytics-munkaterületen hozhatja létre és felügyelheti. Apache Spark készlet Azure Machine Learning munkaterülettel való integrálásához [Az Azure szinapszis Analytics-munkaterületre kell hivatkoznia](how-to-link-synapse-ml-workspaces.md). 

A **társított szolgáltatások** lapon Azure Machine learning Studio felhasználói felületen keresztül Apache Spark készletet is csatolhat. Azt is megteheti, hogy a számítási oldalon a **számítás csatolása** lehetőséggel elvégezte a **számítást** .

Az SDK-n keresztül is csatolhat Apache Spark készletet (lásd alább), vagy egy ARM-sablonon keresztül (ebben a [PÉLDÁBAN ARM-sablonban](https://github.com/Azure/azure-quickstart-templates/blob/master/101-machine-learning-linkedservice-create/azuredeploy.json)). 

A parancssor segítségével követheti az ARM-sablont, hozzáadhatja a társított szolgáltatást, és csatolhatja a Apache Spark készletet a következő kóddal:

```bash
az deployment group create --name --resource-group <rg_name> --template-file "azuredeploy.json" --parameters @"azuredeploy.parameters.json"
```

> [!Important]
> Az Azure szinapszis Analytics-munkaterülethez való csatlakozáshoz a tulajdonosi szerepkörrel kell rendelkeznie az Azure szinapszis Analytics Workspace-erőforrásban. Győződjön meg arról, hogy a Azure Portal a hozzáférését.
> A társított szolgáltatás egy rendszerhez rendelt identitást (SAI) kap a létrehozásakor. Ehhez a link Service-hez hozzá kell rendelnie a "szinapszis Apache Spark Administrator" szerepkört a szinapszis studióból, hogy el tudja küldeni a Spark-feladatot (lásd: a szinapszis [RBAC szerepkör-hozzárendeléseinek kezelése a szinapszis Studióban](../synapse-analytics/security/how-to-manage-synapse-rbac-role-assignments.md)). A Azure Machine Learning munkaterület felhasználójának a "közreműködő" szerepkört is meg kell adnia a Azure Portal erőforrás-Mangement.

## <a name="create-or-retrieve-the-link-between-your-azure-synapse-analytics-workspace-and-your-azure-machine-learning-workspace"></a>Az Azure szinapszis Analytics-munkaterület és a Azure Machine Learning munkaterület közötti kapcsolat létrehozása vagy lekérése

A munkaterületen lévő társított szolgáltatásokat a következőképpen kérheti le:

```python
from azureml.core import Workspace, LinkedService, SynapseWorkspaceLinkedServiceConfiguration

ws = Workspace.from_config()

for service in LinkedService.list(ws) : 
    print(f"Service: {service}")

# Retrieve a known linked service
linked_service = LinkedService.get(ws, 'synapselink1')
```

Először is `Workspace.from_config()` elérheti Azure Machine learning munkaterületét a konfigurációjának használatával `config.json` (lásd [: oktatóanyag: Ismerkedés a Azure Machine learning a fejlesztési környezetben](tutorial-1st-experiment-sdk-setup-local.md)). Ezután a kód kinyomtatja a munkaterületen elérhető összes társított szolgáltatást. Végül `LinkedService.get()` lekéri a nevű társított szolgáltatást `'synapselink1'` . 

## <a name="attach-your-apache-spark-pool-as-a-compute-target-for-azure-machine-learning"></a>Az Apache Spark-készlet csatlakoztatása számítási célként Azure Machine Learning

Ha az Apache Spark-készletet szeretné használni a gépi tanulási folyamat egy lépésének bekapcsolásához, akkor a folyamat lépéséhez csatolnia kell azt a `ComputeTarget` következő kódban látható módon:.

```python
from azureml.core.compute import SynapseCompute, ComputeTarget

attach_config = SynapseCompute.attach_configuration(
        linked_service = linked_service,
        type="SynapseSpark",
        pool_name="spark01") # This name comes from your Synapse workspace

synapse_compute=ComputeTarget.attach(
        workspace=ws,
        name='link1-spark01',
        attach_configuration=attach_config)

synapse_compute.wait_for_completion()
```

Első lépésként konfigurálja a következőt: `SynapseCompute` . Az `linked_service` argumentum az `LinkedService` előző lépésben létrehozott vagy beolvasott objektum. Az argumentumnak a értéknek kell `type` lennie `SynapseSpark` . Az `pool_name` argumentumnak `SynapseCompute.attach_configuration()` meg kell egyeznie az Azure szinapszis Analytics-munkaterületen lévő meglévő készlettel. Az Apache Spark-készlet Azure szinapszis Analytics-munkaterületen való létrehozásával kapcsolatos további információkért lásd: gyors útmutató [: kiszolgáló nélküli Apache Spark készlet létrehozása a szinapszis Studio használatával](../synapse-analytics/quickstart-create-apache-spark-pool-studio.md). A típusa: `attach_config` `ComputeTargetAttachConfiguration` .

Miután létrehozta a konfigurációt, létrehoz egy gépi tanulást `ComputeTarget` a (z `Workspace` ), `ComputeTargetAttachConfiguration` , és azt a nevet, amellyel a Machine learning-munkaterületen található számításokra hivatkozik. A hívás `ComputeTarget.attach()` aszinkron, így a minta csak a hívás befejeződése után jelenik meg.

## <a name="create-a-synapsesparkstep-that-uses-the-linked-apache-spark-pool"></a>`SynapseSparkStep`A csatolt Apache Spark készletet használó a létrehozása

A minta notebook [Spark-feladatok az Apache Spark-készletben](https://github.com/azure/machinelearningnotebooks/blob/master/how-to-use-azureml/azure-synapse/spark_job_on_synapse_spark_pool.ipynb) egy egyszerű gépi tanulási folyamatot határoznak meg. Először a jegyzetfüzet az előző lépésben meghatározott adat-előkészítési lépést határozza meg `synapse_compute` . Ezt követően a jegyzetfüzet olyan betanítási lépést határoz meg, amely a képzéshez jobban illeszkedő számítási céllal van meghatározva. A minta notebook a Titanic Survival-adatbázist használja az adatok bevitelének és kimenetének bemutatására; valójában nem tisztítja meg az adattípust, vagy nem végez prediktív modellt. Mivel ebben a példában nincs valós képzés, a betanítási lépés egy olcsó, CPU-alapú számítási erőforrást használ.

Az adatfolyamatok egy gépi tanulási folyamatba kerülnek `DatasetConsumptionConfig` , objektumok útján, amely táblázatos adatokat vagy fájlokat tárolhat. Az adatok gyakran a munkaterület adattárában lévő blob Storage-fájlokból származnak. A következő kód néhány tipikus kódot mutat be egy Machine learning-folyamat bemenetének létrehozásához:

```python
from azureml.core import Dataset

datastore = ws.get_default_datastore()
file_name = 'Titanic.csv'

titanic_tabular_dataset = Dataset.Tabular.from_delimited_files(path=[(datastore, file_name)])
step1_input1 = titanic_tabular_dataset.as_named_input("tabular_input")

# Example only: it wouldn't make sense to duplicate input data, especially one as tabular and the other as files
titanic_file_dataset = Dataset.File.from_files(path=[(datastore, file_name)])
step1_input2 = titanic_file_dataset.as_named_input("file_input").as_hdfs()
```

A fenti kód azt feltételezi, hogy a fájl `Titanic.csv` blob Storage-ban található. A kód bemutatja, hogyan olvashatja el a fájlt a `TabularDataset` és a néven `FileDataset` . Ez a kód csak demonstrációs célokat szolgál, mivel nem zavaró lehet a bemenetek duplikálása, vagy egyetlen adatforrás értelmezése, mint a tábla és a táblázatos erőforrás, valamint egy fájl.

> [!Important]
> A `FileDataset` as bemenetként való használatához a verziójának legalább a következőnek kell `azureml-core` lennie: `1.20.0` . A következő témakörben ismertetett módon adhatja meg ezt az `Environment` osztály használatával.

Ha egy lépés befejeződik, dönthet úgy, hogy a kimeneti adatokat a következőhöz hasonló kóddal tárolja:

```python
from azureml.data import HDFSOutputDatasetConfig
step1_output = HDFSOutputDatasetConfig(destination=(datastore,"test")).register_on_complete(name="registered_dataset")
```

Ebben az esetben a rendszer a `datastore` nevű fájlban tárolja az adatfájlokat, és a `test` Machine learning-munkaterületen, a névvel együtt elérhetővé válik `Dataset` `registered_dataset` .

Az adatokon kívül a folyamat lépéseinek lépésenkénti Python-függőségei lehetnek. Az egyes `SynapseSparkStep` objektumok pontos Azure szinapszis-Apache Spark konfigurációját is meghatározhatják. Ez a következő kódban látható, amely azt határozza meg, hogy a `azureml-core` csomag verziószámának legalább a következőnek kell lennie `1.20.0` . (Ahogy korábban említettük, ez a követelmény a `azureml-core` bemenetként való használatához szükséges `FileDataset` .)

```python
from azureml.core.environment import Environment
from azureml.pipeline.steps import SynapseSparkStep

env = Environment(name="myenv")
env.python.conda_dependencies.add_pip_package("azureml-core>=1.20.0")

step_1 = SynapseSparkStep(name = 'synapse-spark',
                          file = 'dataprep.py',
                          source_directory="./code", 
                          inputs=[step1_input1, step1_input2],
                          outputs=[step1_output],
                          arguments = ["--tabular_input", step1_input1, 
                                       "--file_input", step1_input2,
                                       "--output_dir", step1_output],
                          compute_target = 'link1-spark01',
                          driver_memory = "7g",
                          driver_cores = 4,
                          executor_memory = "7g",
                          executor_cores = 2,
                          num_executors = 1,
                          environment = env)
```

A fenti kód az Azure Machine learning-folyamat egyetlen lépését határozza meg. Ez a lépés `environment` egy adott verziót határoz meg `azureml-core` , és szükség szerint további Conda vagy pip-függőségeket adhat hozzá.

A a `SynapseSparkStep` zip-t és a helyi számítógépről tölti fel az alkönyvtárat `./code` . A rendszer újra létrehozza a könyvtárat a számítási kiszolgálón, és a lépés a könyvtárból fogja futtatni a fájlt `dataprep.py` . Az `inputs` és az `outputs` adott lépés a `step1_input1` , a `step1_input2` és a `step1_output` korábban tárgyalt objektumok. A parancsfájlban lévő értékek elérésének legegyszerűbb módja a `dataprep.py` névvel ellátottak hozzárendelése `arguments` .

A konstruktor vezérlő Apache Spark következő argumentumai `SynapseSparkStep` . Az az, `compute_target` `'link1-spark01'` amit korábban a számítási célként csatoltunk. A többi paraméter a használni kívánt memóriát és magokat határozza meg.

A minta jegyzetfüzet a következő kódot használja a számára `dataprep.py` :

```python
import os
import sys
import azureml.core
from pyspark.sql import SparkSession
from azureml.core import Run, Dataset

print(azureml.core.VERSION)
print(os.environ)

import argparse
parser = argparse.ArgumentParser()
parser.add_argument("--tabular_input")
parser.add_argument("--file_input")
parser.add_argument("--output_dir")
args = parser.parse_args()

# use dataset sdk to read tabular dataset
run_context = Run.get_context()
dataset = Dataset.get_by_id(run_context.experiment.workspace,id=args.tabular_input)
sdf = dataset.to_spark_dataframe()
sdf.show()

# use hdfs path to read file dataset
spark= SparkSession.builder.getOrCreate()
sdf = spark.read.option("header", "true").csv(args.file_input)
sdf.show()

sdf.coalesce(1).write\
.option("header", "true")\
.mode("append")\
.csv(args.output_dir)
```

Ez az "adat-előkészítési" szkript nem végez valós adatátalakítást, de bemutatja, hogyan kérhet le adatgyűjtést, hogyan alakíthatja át a Spark dataframe, és hogyan végezheti el az alapvető Apache Spark-manipulációkat. A Azure Machine Learning Studio kimenetét a gyermek futtatásának megnyitásával, a **kimenetek és naplók** lapon, majd a fájl megnyitásával, az `logs/azureml/driver/stdout` alábbi ábrán látható módon keresheti meg.

:::image type="content" source="media/how-to-use-synapsesparkstep/synapsesparkstep-stdout.png" alt-text="A gyermek által futtatott StdOut lapon látható Studio képernyőképe":::

## <a name="use-the-synapsesparkstep-in-a-pipeline"></a>A használata a `SynapseSparkStep` folyamatokban

A folyamat egyéb lépései saját egyedi környezettel rendelkezhetnek, és különböző számítási erőforrásokon futnak, amelyek a feladathoz szükségesek. A minta jegyzetfüzet egy kis CPU-fürtön futtatja a "betanítási lépést":

```python
from azureml.core.compute import AmlCompute

cpu_cluster_name = "cpucluster"

if cpu_cluster_name in ws.compute_targets:
    cpu_cluster = ComputeTarget(workspace=ws, name=cpu_cluster_name)
    print('Found existing cluster, use it.')
else:
    compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_D2_V2', max_nodes=1)
    cpu_cluster = ComputeTarget.create(ws, cpu_cluster_name, compute_config)
    print('Allocating new CPU compute cluster')

cpu_cluster.wait_for_completion(show_output=True)

step2_input = step1_output.as_input("step2_input").as_download()

step_2 = PythonScriptStep(script_name="train.py",
                          arguments=[step2_input],
                          inputs=[step2_input],
                          compute_target=cpu_cluster_name,
                          source_directory="./code",
                          allow_reuse=False)
```

Ha szükséges, a fenti kód létrehozza az új számítási erőforrást. Ezt követően az `step1_output` eredmény a betanítási lépés bemenetére lesz konvertálva. A `as_download()` beállítás azt jelenti, hogy az adatok a számítási erőforrásba kerülnek, ami gyorsabb hozzáférést eredményez. Ha az adatok olyan nagyok voltak, hogy nem férnek el a helyi számítási merevlemezen, akkor az `as_mount()` adatoknak a biztosíték-fájlrendszeren keresztüli továbbítására szolgáló kapcsolót kell használnia. A `compute_target` második lépés az, hogy `'cpucluster'` nem az `'link1-spark01'` adatelőkészítési lépésben használt erőforrás. Ez a lépés egy egyszerű programot használ az `train.py` `dataprep.py` előző lépésben használt helyett. A részleteket a `train.py` minta jegyzetfüzetben tekintheti meg.

Miután meghatározta az összes lépést, létrehozhatja és futtathatja a folyamatát. 

```python
from azureml.pipeline.core import Pipeline

pipeline = Pipeline(workspace=ws, steps=[step_1, step_2])
pipeline_run = pipeline.submit('synapse-pipeline', regenerate_outputs=True)
```

A fenti kód egy olyan folyamatot hoz létre, amely az Azure szinapszis Analytics ( `step_1` ) és a betanítási lépés () által működtetett Apache Spark-készletek adatelőkészítési lépése `step_2` . Az Azure kiszámítja a végrehajtási gráfot a lépések közötti adatfüggőségek vizsgálatával. Ebben az esetben csak egy egyszerű függőségre van szükség, amely `step2_input` szükségszerűen szükséges `step1_output` .

`pipeline.submit`Ha szükséges, a rendszer meghívja a meghívott kísérletet, `synapse-pipeline` és aszinkron módon elindítja a futtatását. A folyamaton belüli egyes lépéseket a rendszer a fő Futtatás alárendelt futtatásaként futtatja, és a Studio kísérletek lapján figyelheti és ellenőrizheti.

## <a name="next-steps"></a>Következő lépések

* [Gépi tanulási folyamatok közzététele és nyomon követése](how-to-deploy-pipelines.md) 
* [Az Azure Machine Learning monitorozása](monitor-azure-machine-learning.md)
* [Automatizált ML használata Azure Machine Learning-folyamatokban a Pythonban](how-to-use-automlstep-in-pipelines.md)
