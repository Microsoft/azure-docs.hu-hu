---
title: A Apache Spark használata gépi tanulási folyamatban (előzetes verzió)
titleSuffix: Azure Machine Learning
description: Csatolja Azure Synapse Analytics munkaterületét az Azure Machine Learning-folyamathoz, hogy Apache Spark adatok kezeléséhez használja.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: laobri
author: lobrien
ms.date: 03/04/2021
ms.topic: conceptual
ms.custom: how-to, synapse-azureml
ms.openlocfilehash: 2c69ec853cdeeed6f9e28fb9f2884053580ce08e
ms.sourcegitcommit: d3bcd46f71f578ca2fd8ed94c3cdabe1c1e0302d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107576380"
---
# <a name="how-to-use-apache-spark-powered-by-azure-synapse-analytics-in-your-machine-learning-pipeline-preview"></a>A gépi Apache Spark (Azure Synapse Analytics) használata a gépi tanulási folyamatban (előzetes verzió)

Ebből a cikkből megtudhatja, hogyan használhatja az Apache Spark-alapú Azure Synapse Analytics számítási célként egy adatelőkészítési lépéshez egy Azure Machine Learning folyamatban. Megtudhatja, hogyan használhatja egyetlen folyamat az adott lépéshez megfelelő számítási erőforrásokat, például az adatok előkészítését vagy betanítását. Látni fogja, hogyan készíti elő a rendszer az adatokat a Spark-lépéshez, és hogyan lesz átkedve a következő lépéshez. 

[!INCLUDE [preview disclaimer](../../includes/machine-learning-preview-generic-disclaimer.md)]

## <a name="prerequisites"></a>Előfeltételek

* Hozzon [létre Azure Machine Learning munkaterületet](how-to-manage-workspace.md) az összes folyamaterőforráshoz.

* [Konfigurálja a fejlesztési környezetet](how-to-configure-environment.md) a Azure Machine Learning SDK telepítéséhez, vagy használjon egy Azure Machine Learning számítási példányt, amely már telepítve van az SDK-val. [](concept-compute-instance.md)

* Hozzon létre egy Azure Synapse Analytics-munkaterületet és Apache Spark-készletet (lásd: Rövid útmutató: Kiszolgáló nélküli Apache Spark létrehozása a [Synapse Studio).](../synapse-analytics/quickstart-create-apache-spark-pool-studio.md) 

## <a name="link-your-azure-machine-learning-workspace-and-azure-synapse-analytics-workspace"></a>A saját Azure Machine Learning és Azure Synapse Analytics csatolása 

A készleteket egy Apache Spark munkaterületen hozhatja létre és Azure Synapse Analytics felügyelni. Ha egy Apache Spark integrálni egy Azure Machine Learning-munkaterülettel, hivatkozni kell a Azure Synapse Analytics [munkaterületre.](how-to-link-synapse-ml-workspaces.md) 

Az összekapcsolt szolgáltatások Apache Spark a Azure Machine Learning stúdió felhasználói felületén keresztül csatolhat **egy készletet.** Ezt a Számítás lapon, a Számítás csatolása **lehetőséggel is meg** lehet tenni. 

Csatolhat egy Apache Spark SDK-n keresztül (az alábbiakban részletezett módon) vagy egy ARM-sablonnal (lásd ezt az [ARM-példasablont).](https://github.com/Azure/azure-quickstart-templates/blob/master/101-machine-learning-linkedservice-create/azuredeploy.json) 

A parancssor használatával követheti az ARM-sablont, hozzáadhatja a csatolt szolgáltatást, és csatolhatja a Apache Spark a következő kóddal:

```bash
az deployment group create --name --resource-group <rg_name> --template-file "azuredeploy.json" --parameters @"azuredeploy.parameters.json"
```

> [!Important]
> Ahhoz, hogy sikeresen hivatkozni Azure Synapse Analytics munkaterületre, tulajdonosi szerepkörrel kell Azure Synapse Analytics munkaterületi erőforrásban. Ellenőrizze a hozzáférését a Azure Portal.
> A társított szolgáltatás rendszer által hozzárendelt identitást (SAI) kap a létrehozásakor. A kapcsolati szolgáltatás SAI-jának hozzá kell rendelnie a "Synapse Apache Spark-rendszergazda" szerepkört a Synapse Studio-ból, hogy elküldje a Spark-feladatot (lásd: [Synapse RBAC](../synapse-analytics/security/how-to-manage-synapse-rbac-role-assignments.md)szerepkör-hozzárendelések kezelése a Synapse Studio-ban). Emellett a munkaterület felhasználójának "Közreműködő" szerepkört kell Azure Machine Learning az erőforrás-Azure Portal szerepkört.

## <a name="create-or-retrieve-the-link-between-your-azure-synapse-analytics-workspace-and-your-azure-machine-learning-workspace"></a>A kapcsolat létrehozása vagy lekérése a Azure Synapse Analytics munkaterület és a Azure Machine Learning között

A munkaterületen lévő csatolt szolgáltatásokat a következő kóddal lehet lekérni:

```python
from azureml.core import Workspace, LinkedService, SynapseWorkspaceLinkedServiceConfiguration

ws = Workspace.from_config()

for service in LinkedService.list(ws) : 
    print(f"Service: {service}")

# Retrieve a known linked service
linked_service = LinkedService.get(ws, 'synapselink1')
```

Először is a munkaterülethez Azure Machine Learning a konfigurációját `Workspace.from_config()` `config.json` használva (lásd: Oktatóanyag: Azure Machine Learning a fejlesztési [környezetben](tutorial-1st-experiment-sdk-setup-local.md)). Ezután a kód kinyomtatja a munkaterületen elérhető összes csatolt szolgáltatást. Végül `LinkedService.get()` lekér egy nevű csatolt `'synapselink1'` szolgáltatást. 

## <a name="attach-your-apache-spark-pool-as-a-compute-target-for-azure-machine-learning"></a>Az Apache Spark-készlet csatolása számítási célként a Azure Machine Learning

Ahhoz, hogy az Apache Spark-készletet használva egy lépést használhatja a gépi tanulási folyamatban, csatolnia kell azt a folyamat lépéseként, ahogy az az alábbi `ComputeTarget` kódban látható.

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

Az első lépés a `SynapseCompute` konfigurálása. A argumentum az előző lépésben létrehozott vagy lekért `linked_service` `LinkedService` objektum. A `type` argumentumnak a következőnek kell lennie: `SynapseSpark` . A argumentumának meg kell egyeznie a meglévő készlet argumentumának Azure Synapse Analytics `pool_name` `SynapseCompute.attach_configuration()` munkaterületen. Az Apache Spark-készlet a Azure Synapse Analytics-munkaterületen való létrehozásával kapcsolatos további információkért lásd: Rövid útmutató: Kiszolgáló nélküli Apache Spark létrehozása a [Synapse Studio.](../synapse-analytics/quickstart-create-apache-spark-pool-studio.md) A típusa `attach_config` `ComputeTargetAttachConfiguration` .

A konfiguráció létrehozása után gépi tanulást hozhat létre a , és annak a névnek a megszabadulása alapján, amellyel hivatkozni szeretne a gépi tanulási munkaterületen `ComputeTarget` `Workspace` belüli `ComputeTargetAttachConfiguration` számításra. A hívása aszinkron, ezért a minta blokkolja a metódust, amíg `ComputeTarget.attach()` a hívás be nem fejeződik.

## <a name="create-a-synapsesparkstep-that-uses-the-linked-apache-spark-pool"></a>Hozzon létre `SynapseSparkStep` egy et, amely a csatolt Apache Spark használja

Az [Apache Spark-készleten futó Spark-minta-feladat](https://github.com/azure/machinelearningnotebooks/blob/master/how-to-use-azureml/azure-synapse/spark_job_on_synapse_spark_pool.ipynb) egy egyszerű gépi tanulási folyamatot határoz meg. Először is a jegyzetfüzet egy adatelőkészítési lépést határoz meg, amelyet az `synapse_compute` előző lépésben meghatározottak határoznak meg. Ezután a jegyzetfüzet meghatároz egy betanításra jobban illeszkedő számítási cél által működtetett betanításlépést. A mintajegyzetfüzet a Titanic túlélési adatbázisát használja az adatbemenet és -kimenet szemléltetése érdekében; Valójában nem megtisztítja az adatokat, és nem hoz elő prediktív modellt. Mivel ebben a mintában nincs valós betanítás, a betanítás lépése egy olcsó, CPU-alapú számítási erőforrást használ.

Az adatok objektumokon keresztül áramlnak egy gépi tanulási folyamatba, amely táblázatos adatokat vagy `DatasetConsumptionConfig` fájlkészleteket is képes tartalmazni. Az adatok gyakran egy munkaterület adattárában található blobtárolóban található fájlokból származik. Az alábbi kód egy gépi tanulási folyamat bemenetének létrehozására vonatkozó tipikus kódot mutat be:

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

A fenti kód feltételezi, hogy a fájl `Titanic.csv` a Blob Storage-ban található. A kód bemutatja, hogyan olvashatja be a fájlt és `TabularDataset` `FileDataset` fájlként. Ez a kód csak bemutatási célokat szolgál, mivel zavaró lenne duplikált bemeneteket használni, vagy egyetlen adatforrást egy táblázatot tartalmazó erőforrásként és fájlként értelmezni.

> [!Important]
> A bemenetként való használathoz a verziónak legalább a `FileDataset` `azureml-core` verziónak kell `1.20.0` lennie. Ennek a osztály használatával való megadásáról `Environment` alább olvashat.

Ha egy lépés befejeződött, dönthet úgy, hogy a kimeneti adatokat a következő kód használatával tárolja:

```python
from azureml.data import HDFSOutputDatasetConfig
step1_output = HDFSOutputDatasetConfig(destination=(datastore,"test")).register_on_complete(name="registered_dataset")
```

Ebben az esetben az adatok egy nevű fájlban lesznek tárolva, és a Machine Learning-munkaterületen a következő néven érhetők `datastore` `test` `Dataset` el: `registered_dataset` .

Az adatok mellett a folyamatlépések lépésenkénti Python-függőségekkel is lehetnek. Az `SynapseSparkStep` egyes objektumok a pontos Azure Synapse Apache Spark is megadhatja. Ez az alábbi kódban látható, amely meghatározza, hogy a csomagverziónak legalább a `azureml-core` verziónak kell `1.20.0` lennie. (Ahogy korábban említettük, a követelménye az bemenetként `azureml-core` való használatának `FileDataset` követelménye.)

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

A fenti kód egyetlen lépést ad meg az Azure Machine Learning-folyamatban. Ez a lépés egy adott verziót ad meg, és szükség esetén további `environment` `azureml-core` Conda- vagy pip-függőségeket adhat hozzá.

A `SynapseSparkStep` tömörítve lesz, és feltölti a helyi számítógépről a `./code` alkönyvtárat. Ez a könyvtár újra létre lesz hozva a számítási kiszolgálón, és a lépés ebből a könyvtárból fogja `dataprep.py` futtatni a fájlt. Ennek a lépésnek a és a része a korábban tárgyalt `inputs` , és `outputs` `step1_input1` `step1_input2` `step1_output` objektum. Ezeket az értékeket a legegyszerűbben úgy lehet elérni a `dataprep.py` szkriptben, ha a nevűvel társítja `arguments` őket.

A konstruktorvezérlő következő `SynapseSparkStep` argumentumkészlete Apache Spark. A `compute_target` korábban `'link1-spark01'` számítási célként csatolt. A többi paraméter határozza meg a használni kívánt memóriát és magokat.

A mintajegyzetfüzet a következő kódot használja a-hez: `dataprep.py`

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

Ez az "adatelőkészítési" szkript nem valós adatátalakítást, hanem azt mutatja be, hogyan lehet adatokat lekérni, spark-adatkeretekké konvertálni, és bizonyos alapszintű Apache Spark műveleteket. A kimenetet a Azure Machine Learning Studióban találhatja meg, ha megnyitja a gyermekfuttassat, kiválasztja a Kimenetek **+** naplók lapot, majd megnyitja a fájlt az alábbi `logs/azureml/driver/stdout` ábrán látható módon.

:::image type="content" source="media/how-to-use-synapsesparkstep/synapsesparkstep-stdout.png" alt-text="Képernyőkép a Studio gyermekfutat stdout lapfülével":::

## <a name="use-the-synapsesparkstep-in-a-pipeline"></a>A használata `SynapseSparkStep` egy folyamatban

Az alábbi példa az előző szakaszban létrehozott `SynapseSparkStep` [kimenetet használja.](#create-a-synapsesparkstep-that-uses-the-linked-apache-spark-pool) A folyamat további lépései saját egyedi környezetekkel is lehetnek, és az adott feladatnak megfelelő különböző számítási erőforrásokon futhatnak. A mintajegyzetfüzet egy kis cpu-fürtön futtatja a "betanítás lépését":

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

A fenti kód szükség esetén létrehozza az új számítási erőforrást. Ezután a rendszer átalakítja az eredményt `step1_output` a betanítás lépésének bemeneteként. A beállítás azt jelenti, hogy az adatok `as_download()` átkerülnek a számítási erőforrásra, ami gyorsabb hozzáférést eredményez. Ha az adatok olyan nagyok, hogy nem férnek el a helyi számítási merevlemezen, akkor a FUSE fájlrendszeren keresztül streamelné az `as_mount()` adatokat. A második lépés a , nem pedig az `compute_target` `'cpucluster'` `'link1-spark01'` adat-előkészítési lépésben használt erőforrás. Ez a lépés egy egyszerű programot használ az előző lépésben `train.py` `dataprep.py` használt program helyett. A részleteit a `train.py` mintajegyzetfüzetben láthatja.

Miután meghatározta az összes lépést, létrehozhatja és futtathatja a folyamatot. 

```python
from azureml.pipeline.core import Pipeline

pipeline = Pipeline(workspace=ws, steps=[step_1, step_2])
pipeline_run = pipeline.submit('synapse-pipeline', regenerate_outputs=True)
```

A fenti kód létrehoz egy folyamatot, amely a Apache Spark ( ) által működtetett Apache Spark Azure Synapse Analytics adatelőkészítési lépésből és a betanítás `step_1` lépésből `step_2` () áll. Az Azure a lépések közötti adatfüggőségek vizsgálatával számítja ki a végrehajtási gráfot. Ebben az esetben csak egy egyszerű függőségre van szükség, amelyhez `step2_input` feltétlenül szükség `step1_output` van.

A hívása szükség esetén létrehoz egy nevű kísérletet, és aszinkron módon `pipeline.submit` `synapse-pipeline` futtatja azt. A folyamat egyes lépései a fő futtatás gyermekfuttassaként futnak, és a Studio Kísérletek lapján figyelhetőek és ellenőrizhetők.

## <a name="next-steps"></a>Következő lépések

* [Gépi tanulási folyamatok közzététele és nyomon követése](how-to-deploy-pipelines.md) 
* [Az Azure Machine Learning monitorozása](monitor-azure-machine-learning.md)
* [Automatizált gépi tanulási folyamat használata Azure Machine Learning Pythonban](how-to-use-automlstep-in-pipelines.md)
