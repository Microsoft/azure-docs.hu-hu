---
title: Adatrendezés Apache Spark készletekkel (előzetes verzió)
titleSuffix: Azure Machine Learning
description: Megtudhatja, hogyan csatolhat és indíthat Apache Spark készleteket az adatrendezéshez a Azure Synapse Analytics és Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: nibaccam
author: nibaccam
ms.reviewer: nibaccam
ms.date: 03/02/2021
ms.custom: how-to, devx-track-python, data4ml, synapse-azureml
ms.openlocfilehash: 1852bfdb4bf8513aaa5d43993e2b6ff804808dbd
ms.sourcegitcommit: d3bcd46f71f578ca2fd8ed94c3cdabe1c1e0302d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107575649"
---
# <a name="attach-apache-spark-pools-powered-by-azure-synapse-analytics-for-data-wrangling-preview"></a>Csatolja Apache Spark (Azure Synapse Analytics) adatrendezéshez (előzetes verzió)

Ebből a cikkből megtudhatja, hogyan csatolhat [](../synapse-analytics/overview-what-is.md) Apache Spark Azure Synapse Analytics-alapú virtuálisgép-készletet az Azure Machine Learning-munkaterülethez, hogy elindítsa azt, és nagy méretekben végezzen adatrendezést. 

Ez a cikk útmutatást nyújt az adatrendezési feladatok interaktív végrehajtásához egy Jupyter notebook dedikált Synapse-munkamenetében az [Azure Machine Learning Python SDK használatával.](/python/api/overview/azure/ml/) Ha inkább a Azure Machine Learning használatát szeretné használni, tekintse meg a Apache Spark (Azure Synapse Analytics által működtetett) használatának a gépi tanulási folyamatban [(előzetes verzió) való használatát.](how-to-use-synapsesparkstep.md)

Ha útmutatásra van szüksége a Azure Synapse Analytics Synapse-munkaterülettel való használatával, tekintse meg a [Azure Synapse Analytics első lépések sorozatot.](../synapse-analytics/get-started.md)

>[!IMPORTANT]
> A Azure Machine Learning és Azure Synapse Analytics integrációja előzetes verzióban érhető el. A cikkben bemutatott képességek a kísérleti előzetes verziójú funkciókat tartalmazó csomagot alkalmazzák, `azureml-synapse` amely bármikor [](/python/api/overview/azure/ml/#stable-vs-experimental) megváltozhat.

## <a name="azure-machine-learning-and-azure-synapse-analytics-integration-preview"></a>Azure Machine Learning és Azure Synapse Analytics (előzetes verzió)

Az Azure Synapse Analytics (előzetes verzió) Azure Machine Learning integrációja lehetővé teszi egy Apache Spark-készlet csatolását Azure Synapse adatok interaktív feltárása és előkészítése érdekében. Ezzel az integrációval dedikált számítási kapacitást használhat nagy méretekben való adatrendezéshez, mindezt a gépi tanulási modellek betanítása során használt Python-jegyzetfüzeten belül.

## <a name="prerequisites"></a>Előfeltételek

* A [Azure Machine Learning Python SDK telepítve van.](/python/api/overview/azure/ml/install) 

* [Hozzon létre egy Azure Machine Learning munkaterületet.](how-to-manage-workspace.md?tabs=python)

* [Hozzon létre egy Azure Synapse Analytics munkaterületet a Azure Portal.](../synapse-analytics/quickstart-create-workspace.md)

* [Hozzon Apache Spark készletet Azure Portal, webes eszközök vagy Synapse Studio](../synapse-analytics/quickstart-create-apache-spark-pool-portal.md)

* [Konfigurálja a fejlesztési környezetet](how-to-configure-environment.md) a Azure Machine Learning SDK telepítéséhez, vagy használjon egy Azure Machine Learning számítási példányt, amely már telepítve van az SDK-val. [](concept-compute-instance.md#create) 

* Telepítse a `azureml-synapse` csomagot (előzetes verzió) a következő kóddal:

  ```python
  pip install azureml-synapse
  ```

* [Csatolja Azure Machine Learning munkaterületet a Azure Synapse Analytics munkaterülethez.](how-to-link-synapse-ml-workspaces.md)

## <a name="get-an-existing-linked-service"></a>Meglévő csatolt szolgáltatás lekérte
Ahhoz, hogy dedikált számítási kapacitást csatoljon az adatszervezéshez, egy Azure Synapse Analytics-munkaterülethez csatolt GÉPI-munkaterülettel kell lennie. Ezt csatolt szolgáltatásnak nevezzük. 

Egy meglévő összekapcsolt szolgáltatás  lekéréséhez és használatához Felhasználói vagy Közreműködői engedélyre van szükség a Azure Synapse Analytics munkaterületen.

Az összes megtekintése gépi tanulási munkaterülethez társított társított szolgáltatásokat. 

```python
from azureml.core import LinkedService

LinkedService.list(ws)
```

Ez a példa lekér egy meglévő összekapcsolt szolgáltatást () a `synapselink1` munkaterületről `ws` a [`get()`](/python/api/azureml-core/azureml.core.linkedservice#get-workspace--name-) metódussal.
```python
from azureml.core import LinkedService

linked_service = LinkedService.get(ws, 'synapselink1')
```
 
## <a name="attach-synapse-spark-pool-as-a-compute"></a>Synapse Spark-készlet csatolása számításként

A csatolt szolgáltatás lekérése után csatoljon egy Synapse-Apache Spark-készletet dedikált számítási erőforrásként az adatcsoport-elosztási feladatokhoz. 

A készleteket a Apache Spark csatolhatja a(
* Azure Machine Learning Studio
* [Azure Resource Manager- (ARM-) sablonok](https://github.com/Azure/azure-quickstart-templates/blob/master/101-machine-learning-linkedservice-create/azuredeploy.json)
* Az Azure Machine Learning Python SDK 

### <a name="attach-a-pool-via-the-studio"></a>Készlet csatolása a studióban
Kövesse az alábbi lépéseket: 

1. Jelentkezzen be a [Azure Machine Learning stúdió.](https://ml.azure.com/)
1. A **bal oldali panel** Kezelés szakaszában válassza a Linked Services (Csatolt szolgáltatások) lehetőséget. 
1. Válassza ki a Synapse-munkaterületet.
1. A **bal felső sarokban válassza** a Csatolt Spark-készletek lehetőséget. 
1. Válassza a **Csatolás lehetőséget.** 
1. Válassza ki a Apache Spark a listából, és adjon meg egy nevet.  
    1. Ez a lista azonosítja a számítási kapacitáshoz csatolható rendelkezésre álló Synapse Spark-készleteket. 
    1. Új Synapse Spark-készlet létrehozásához lásd: Create [Apache Spark pool with the Synapse Studio](../synapse-analytics/quickstart-create-apache-spark-pool-portal.md)
1. Válassza a **Kijelölt csatolás lehetőséget.** 

### <a name="attach-a-pool-with-the-python-sdk"></a>Készlet csatolása a Python SDK-val

A Python **SDK** használatával is csatolhat egy Apache Spark készletet. 

A következő kód: 
1. A konfigurálása a [`SynapseCompute`](/python/api/azureml-core/azureml.core.compute.synapsecompute) következővel:

   1. Az előző lépésben létrehozott vagy [`LinkedService`](/python/api/azureml-core/azureml.core.linkedservice) `linked_service` lekért . 
   1. A csatolni kívánt számítási cél típusa, `SynapseSpark`
   1. A Apache Spark neve. Ennek egyeznie kell egy meglévő Apache Spark, amely a saját munkaterületén Azure Synapse Analytics található.
   
1. Gépi tanulást hoz [`ComputeTarget`](/python/api/azureml-core/azureml.core.computetarget) létre a következő átadással: 
   1. A használni kívánt Machine Learning-munkaterület, `ws`
   1. Az a név, amely a számítási erőforrás munkaterületén Azure Machine Learning hivatkozni. 
   1. A attach_configuration Synapse Compute konfigurálásakor megadott érték.
       1. A ComputeTarget.attach() hívása aszinkron, ezért a mintablokkok addig maradnak, amíg a hívás be nem fejeződik.

```python
from azureml.core.compute import SynapseCompute, ComputeTarget

attach_config = SynapseCompute.attach_configuration(linked_service, #Linked synapse workspace alias
                                                    type='SynapseSpark', #Type of assets to attach
                                                    pool_name=synapse_spark_pool_name) #Name of Synapse spark pool 

synapse_compute = ComputeTarget.attach(workspace= ws,                
                                       name= synapse_compute_name, 
                                       attach_configuration= attach_config
                                      )

synapse_compute.wait_for_completion()
```

Ellenőrizze, hogy a Apache Spark-készlet csatlakoztatva van-e.

```python
ws.compute_targets['Synapse Spark pool alias']
```

## <a name="launch-synapse-spark-pool-for-data-wrangling-tasks"></a>Synapse Spark-készlet indítása adatrendezési feladatokhoz

Az adatok előkészítésének a Apache Spark készletben való megkezdéséhez adja meg Apache Spark készlet nevét:

> [!IMPORTANT]
> Az adatkészlet használatának folytatásához meg kell Apache Spark, hogy melyik számítási erőforrást kell használnia az adatcsoport-elosztási tevékenységekben az egyetlen sornyi kódhoz és több `%synapse` `%%synapse` sorhoz. 

```python
%synapse start -c SynapseSparkPoolAlias
```

A munkamenet indítása után ellenőrizheti a munkamenet metaadatait.

```python
%synapse meta
```

Megadhatja a [Azure Machine Learning munkamenet](concept-environments.md) során használni kívánt Apache Spark környezetet. Csak a környezetben megadott Conda-függőségek lépnek életbe. A Docker-rendszerkép nem támogatott.

>[!WARNING]
>  A Környezet Conda-függőségei környezetben megadott Python-függőségek nem támogatottak Apache Spark készletekben. Jelenleg csak a rögzített Python-verziók támogatottak. Ellenőrizze a Python-verziót a szkriptbe  `sys.version_info` való beíróval.

A következő kód létrehozza a környezetet ( ) , amely a munkamenet kezdete előtt telepíti az `myenv` `azureml-core` 1.20.0-s és `numpy` az 1.17.0-s verziót. Ezt a környezetet aztán felveheti a Apache Spark `start` munkamenet-utasításba.

```python

from azureml.core import Workspace, Environment

# creates environment with numpy and azureml-core dependencies
ws = Workspace.from_config()
env = Environment(name="myenv")
env.python.conda_dependencies.add_pip_package("azureml-core==1.20.0")
env.python.conda_dependencies.add_conda_package("numpy==1.17.0")
env.register(workspace=ws)
```

Az adatok előkészítésének a Apache Spark és az egyéni környezetben való megkezdéséhez adja meg a Apache Spark-készlet nevét és azt a környezetet, amelyet a Apache Spark használni. Emellett meg is használhatja az előfizetés azonosítóját, a Machine Learning-munkaterület erőforráscsoportját, valamint a gépi tanulási munkaterület nevét.

```python
%synapse start -c SynapseSparkPoolAlias -e myenv -s AzureMLworkspaceSubscriptionID -r AzureMLworkspaceResourceGroupName -w AzureMLworkspaceName
```
## <a name="load-data-from-storage"></a>Adatok betöltése a tárolóból

A Apache Spark után olvassa be az előkészíteni kívánt adatokat. Az adatok betöltése az Azure Blob Storage és a Azure Data Lake Storage 1. és 2. generáció esetén támogatott.

Két módon lehet adatokat betölteni ezekből a tárolási szolgáltatásokból: 

* Közvetlenül betöltheti az adatokat a tárolóból a Hadoop elosztott fájlrendszer (HDFS) elérési útjának használatával.

* Beolvassa az adatokat egy [meglévő Azure Machine Learning adatkészletből.](how-to-create-register-datasets.md)

A tárolási szolgáltatások eléréséhez **Storage-blobadatok** olvasói engedélyekkel kell rendelkeznie. Ha adatokat tervez visszaírni ezekbe a tárolási szolgáltatásokba, **Storage-blobadatok közreműködője engedélyekkel** kell rendelkeznie. [További információ a tárolási engedélyekről és szerepkörökről.](../storage/common/storage-auth-aad-rbac-portal.md#azure-roles-for-blobs-and-queues)

### <a name="load-data-with-hadoop-distributed-files-system-hdfs-path"></a>Adatok betöltése a Hadoop elosztott fájlrendszer (HDFS) útvonalával

Ahhoz, hogy adatokat töltsön be és olvasson be a tárolóból a megfelelő HDFS-útvonalon, rendelkeznie kell az adatokhoz való hozzáféréshez szükséges hitelesítő adatokkal. Ezek a hitelesítő adatok a tároló típusától függően eltérőek.  

A következő kód bemutatja, hogyan olvashatja be az **adatokat** egy Azure Blob Storage-ból egy Spark-adatkeretbe a közös hozzáférésű jogosultságkód (SAS) jogkivonatával vagy hozzáférési kulcsával. 

```python
%%synapse

# setup access key or SAS token
sc._jsc.hadoopConfiguration().set("fs.azure.account.key.<storage account name>.blob.core.windows.net", "<access key>")
sc._jsc.hadoopConfiguration().set("fs.azure.sas.<container name>.<storage account name>.blob.core.windows.net", "<sas token>")

# read from blob 
df = spark.read.option("header", "true").csv("wasbs://demo@dprepdata.blob.core.windows.net/Titanic.csv")
```

A következő kód bemutatja, hogyan olvashatja be az **1. generációs Azure Data Lake Storage (ADLS Gen 1)** adatait a szolgáltatásnév hitelesítő adataival. 

```python
%%synapse

# setup service principal which has access of the data
sc._jsc.hadoopConfiguration().set("fs.adl.account.<storage account name>.oauth2.access.token.provider.type","ClientCredential")

sc._jsc.hadoopConfiguration().set("fs.adl.account.<storage account name>.oauth2.client.id", "<client id>")

sc._jsc.hadoopConfiguration().set("fs.adl.account.<storage account name>.oauth2.credential", "<client secret>")

sc._jsc.hadoopConfiguration().set("fs.adl.account.<storage account name>.oauth2.refresh.url",
"https://login.microsoftonline.com/<tenant id>/oauth2/token")

df = spark.read.csv("adl://<storage account name>.azuredatalakestore.net/<path>")

```

A következő kód bemutatja, hogyan olvashatja be a 2. generációs Azure Data Lake Storage **(ADLS Gen 2)** adatait a szolgáltatásnév hitelesítő adataival. 

```python
%%synapse

# setup service principal which has access of the data
sc._jsc.hadoopConfiguration().set("fs.azure.account.auth.type.<storage account name>.dfs.core.windows.net","OAuth")
sc._jsc.hadoopConfiguration().set("fs.azure.account.oauth.provider.type.<storage account name>.dfs.core.windows.net", "org.apache.hadoop.fs.azurebfs.oauth2.ClientCredsTokenProvider")
sc._jsc.hadoopConfiguration().set("fs.azure.account.oauth2.client.id.<storage account name>.dfs.core.windows.net", "<client id>")
sc._jsc.hadoopConfiguration().set("fs.azure.account.oauth2.client.secret.<storage account name>.dfs.core.windows.net", "<client secret>")
sc._jsc.hadoopConfiguration().set("fs.azure.account.oauth2.client.endpoint.<storage account name>.dfs.core.windows.net",
"https://login.microsoftonline.com/<tenant id>/oauth2/token")

df = spark.read.csv("abfss://<container name>@<storage account>.dfs.core.windows.net/<path>")

```

### <a name="read-in-data-from-registered-datasets"></a>Adatok beolvasása regisztrált adatkészletből

Le is kaphat egy meglévő regisztrált adatkészletet a munkaterületen, és elvégezheti rajta az adatok előkészítését, ha spark-adatkeretekké konvertálja őket.

Az alábbi példa hitelesíti magát a munkaterületen, lekért egy regisztrált TabularDataset adatkészletet, amely a Blob Storage-ban lévő fájlokra hivatkozik, és `blob_dset` spark-adatkeretké konvertálja azt. Amikor az adatkészleteket Spark-adatkeretekké alakítja, adatfeltárási és -előkészítési `pyspark` kódtárakat is kihasználhat.  

``` python
%%synapse

from azureml.core import Workspace, Dataset

subscription_id = "<enter your subscription ID>"
resource_group = "<enter your resource group>"
workspace_name = "<enter your workspace name>"

ws = Workspace(workspace_name = workspace_name,
               subscription_id = subscription_id,
               resource_group = resource_group)

dset = Dataset.get_by_name(ws, "blob_dset")
spark_df = dset.to_spark_dataframe()
```

## <a name="perform-data-wrangling-tasks"></a>Adatrendezési feladatok végrehajtása

Az adatok lekérése és feltárása után adatrendezési feladatokat hajthat végre.

A következő kód kibontja az előző szakaszban található HDFS-példát, és szűri az adatokat a Spark-adatkeretben () a Oszlop oszlop és az Életkor szerint listolt `df` csoportok **alapján.** 

```python
%%synapse

from pyspark.sql.functions import col, desc

df.filter(col('Survived') == 1).groupBy('Age').count().orderBy(desc('count')).show(10)

df.show()

```

## <a name="save-data-to-storage-and-stop-spark-session"></a>Adatok mentése a tárolóba és a Spark-munkamenet befejezése

Az adatfeltárás és -előkészítés befejezése után tárolja az előkészített adatokat későbbi használatra az Azure-tárfiókban.

A következő példában az előkészített adatokat a rendszer visszaírja az Azure Blob Storage-ba, és felülírja az eredeti fájlt a `Titanic.csv` `training_data` könyvtárban. A tárolóba való visszaíráshoz **Storage-blobadatok** közreműködője engedélyekkel kell rendelkeznie. [További információ a tárolási engedélyekről és szerepkörökről.](../storage/common/storage-auth-aad-rbac-portal.md#azure-roles-for-blobs-and-queues)

```python
%% synapse

df.write.format("csv").mode("overwrite").save("wasbs://demo@dprepdata.blob.core.windows.net/training_data/Titanic.csv")
```

Ha befejezte az adatok előkészítését, és mentette az előkészített adatokat a tárolóba, az alábbi paranccsal ne használja Apache Spark a tárolókészletet.

```python
%synapse stop
```

## <a name="create-dataset-to-represent-prepared-data"></a>Előkészített adatokat képviselő adatkészlet létrehozása

Ha készen áll az előkészített adatok használatára a modell betanítása érdekében, csatlakozzon [a](how-to-access-data.md)tárolóhoz egy Azure Machine Learning-adattár használatával, és adja meg, hogy melyik fájl(ak)t szeretné használni egy Azure Machine Learning-adatkészlethez. [](how-to-create-register-datasets.md)

Az alábbi példakód:

* Azt feltételezi, hogy már létrehozott egy adattárat, amely csatlakozik ahhoz a tárolási szolgáltatáshoz, ahová az előkészített adatokat mentette.  
* Lekérte a meglévő adattárat () `mydatastore` a `ws` munkaterületről a get() metódussal.
* Létrehoz egy [FileDataset (Fájladatkészlet)](how-to-create-register-datasets.md#filedataset)adatkészletet, amely a könyvtárában található előkészített `train_ds` `training_data` adatfájlokra `mydatastore` hivatkozik.  
* Létrehozza a változót, amellyel később elérhetővé teszi az adatcsoport adatfájlját `input1` `train_ds` egy számítási cél számára.

```python
from azureml.core import Datastore, Dataset

datastore = Datastore.get(ws, datastore_name='mydatastore')

datastore_paths = [(datastore, '/training_data/')]
train_ds = Dataset.File.from_files(path=datastore_paths, validate=True)
input1 = train_ds.as_mount()

```
## <a name="use-a-scriptrunconfig-to-submit-an-experiment-run-to-a-synapse-spark-pool"></a>Kísérlet `ScriptRunConfig` futtatásának elküldése Synapse Spark-készletbe egy használatával

A korábban számítási célként csatolt [Synapse](#attach-a-pool-with-the-python-sdk) Spark-fürtöt is használhatja a [ScriptRunConfig](/python/api/azureml-core/azureml.core.scriptrunconfig) objektummal végzett kísérletfutatok beküldése érdekében.

```Python
from azureml.core import RunConfiguration
from azureml.core import ScriptRunConfig 
from azureml.core import Experiment

run_config = RunConfiguration(framework="pyspark")
run_config.target = synapse_compute_name

run_config.spark.configuration["spark.driver.memory"] = "1g" 
run_config.spark.configuration["spark.driver.cores"] = 2 
run_config.spark.configuration["spark.executor.memory"] = "1g" 
run_config.spark.configuration["spark.executor.cores"] = 1 
run_config.spark.configuration["spark.executor.instances"] = 1 

run_config.environment.python.conda_dependencies = conda_dep

script_run_config = ScriptRunConfig(source_directory = './code',
                                    script= 'dataprep.py',
                                    arguments = ["--tabular_input", input1, 
                                                 "--file_input", input2,
                                                 "--output_dir", output],
                                    run_config = run_config)
```

Miután `ScriptRunConfig` beállította az objektumot, elküldheti a futtatás.

```python
from azureml.core import Experiment 

exp = Experiment(workspace=ws, name="synapse-spark") 
run = exp.submit(config=script_run_config) 
run
```
További részletekért, például a példában `dataprep.py` használt szkriptért tekintse meg a [példajegyzetfüzetet.](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/azure-synapse/spark_session_on_synapse_spark_pool.ipynb)

## <a name="example-notebooks"></a>Példajegyzetfüzetek

Ebben a [példajegyzetfüzetben](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/azure-synapse/spark_session_on_synapse_spark_pool.ipynb) további fogalmakat és bemutatókat láthat a Azure Synapse Analytics és Azure Machine Learning integrációs képességekről.

## <a name="next-steps"></a>Következő lépések

* [Modell betanítás.](how-to-set-up-training-targets.md)
* [Betanítás Azure Machine Learning adatkészlet alapján](how-to-train-with-datasets.md)
