---
title: Az Apache Spark-készletekkel rendelkező huzavona (előzetes verzió)
titleSuffix: Azure Machine Learning
description: Ismerje meg, hogyan csatlakoztathat és indíthat el Apache Spark készleteket az Azure szinapszis Analytics és a Azure Machine Learning használatával történő huzavona.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: nibaccam
author: nibaccam
ms.reviewer: nibaccam
ms.date: 03/02/2021
ms.custom: how-to, devx-track-python, data4ml, synapse-azureml
ms.openlocfilehash: 96917b805640f0cfe38f28ba263e2e7ce55bce7f
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/31/2021
ms.locfileid: "106066160"
---
# <a name="attach-apache-spark-pools-powered-by-azure-synapse-analytics-for-data-wrangling-preview"></a>Az Azure szinapszis Analytics szolgáltatással rendelkező Apache Spark-készletek csatlakoztatása a huzavona (előzetes verzió)

Ebből a cikkből megtudhatja, hogyan csatolhat és indíthat el egy olyan Apache Spark-készletet, amely az [Azure szinapszis Analytics](../synapse-analytics/overview-what-is.md) segítségével huzavona az adatmennyiséget. 

Ez a cikk útmutatást nyújt az adathuzavona-feladatok interaktív végrehajtásához egy Jupyter-jegyzetfüzetben lévő dedikált szinapszis-munkameneten belül. Ha Azure Machine Learning-folyamatokat szeretne használni, tekintse [meg a Machine learning-folyamat (előzetes verzió) Apache Spark (az Azure szinapszis Analytics által működtetett) használatát](how-to-use-synapsesparkstep.md)ismertető témakört.

>[!IMPORTANT]
> A Azure Machine Learning és az Azure szinapszis Analytics-integráció előzetes verzióban érhető el. A cikkben bemutatott képességek olyan csomagot alkalmaznak `azureml-synapse` , amely a [kísérleti](/python/api/overview/azure/ml/#stable-vs-experimental) előzetes verzió funkcióit tartalmazza, amelyek bármikor megváltozhatnak.

## <a name="azure-machine-learning-and-azure-synapse-analytics-integration-preview"></a>Azure Machine Learning és az Azure szinapszis Analytics-integrációja (előzetes verzió)

Az Azure szinapszis Analytics Azure Machine Learning (előzetes verzió) szolgáltatással való integrációja lehetővé teszi, hogy az Azure szinapszis által támogatott Apache Spark-készletet az interaktív adatelemzéshez és-előkészítéshez csatolja. Ebben az integrációban az adatok huzavona dedikált számítási kapacitással rendelkezhet, mindezt a gépi tanulási modellek betanításához használt Python-jegyzetfüzeten belül is.

## <a name="prerequisites"></a>Előfeltételek

* [Hozzon létre egy Azure Machine learning munkaterületet](how-to-manage-workspace.md?tabs=python).

* [Hozzon létre egy Azure szinapszis Analytics-munkaterületet Azure Portal](../synapse-analytics/quickstart-create-workspace.md).

* [Apache Spark-készlet létrehozása a Azure Portal, a web Tools vagy a szinapszis Studio használatával](../synapse-analytics/quickstart-create-apache-spark-pool-portal.md)

* [Állítsa be a fejlesztési környezetet](how-to-configure-environment.md) a Azure Machine learning SDK telepítéséhez, vagy használjon egy [Azure Machine learning számítási példányt](concept-compute-instance.md#create) az SDK-val, amely már telepítve van. 

* Telepítse a `azureml-synapse` csomagot (előzetes verzió) a következő kóddal:

  ```python
  pip install azureml-synapse
  ```

* [Azure Machine learning munkaterület és az Azure szinapszis Analytics-munkaterület összekapcsolása](how-to-link-synapse-ml-workspaces.md).

## <a name="get-an-existing-linked-service"></a>Meglévő társított szolgáltatás beszerzése
Ahhoz, hogy dedikált számítást lehessen csatolni az adatok huzavona, rendelkeznie kell egy, az Azure szinapszis Analytics-munkaterülethez csatolt ML-munkaterülettel, amelyet társított szolgáltatásnak nevezünk. 

Meglévő társított szolgáltatás lekéréséhez és használatához **felhasználói vagy közreműködői** engedélyek szükségesek az Azure szinapszis Analytics-munkaterülethez.

A Machine learning-munkaterülethez kapcsolódó összes társított szolgáltatás megtekintése. 

```python
LinkedService.list(ws)
```

Ez a példa egy meglévő társított szolgáltatást kér le a `synapselink1` munkaterületről a `ws` [`get()`](/python/api/azureml-core/azureml.core.linkedservice#get-workspace--name-) metódussal.
```python
linked_service = LinkedService.get(ws, 'synapselink1')
```
 
## <a name="attach-synapse-spark-pool-as-a-compute"></a>Szinapszis Spark-készlet csatlakoztatása számítási feladatokhoz

Miután beolvasta a társított szolgáltatást, csatoljon egy szinapszis Apache Spark-készletet dedikált számítási erőforrásként az adatok huzavona feladataihoz. 

Apache Spark-készleteket csatlakoztathat a használatával,
* Azure Machine Learning Studio
* [Azure Resource Manager- (ARM-) sablonok](https://github.com/Azure/azure-quickstart-templates/blob/master/101-machine-learning-linkedservice-create/azuredeploy.json)
* A Python SDK 

### <a name="attach-a-pool-via-the-studio"></a>Készlet csatolása a Studio használatával
Kövesse az alábbi lépéseket: 

1. Jelentkezzen be a [Azure Machine learning studióba](https://ml.azure.com/).
1. A bal oldali ablaktábla **kezelés** szakaszában válassza a **társított szolgáltatások** elemet.
1. Válassza ki a szinapszis munkaterületet.
1. Kattintson a bal felső sarokban található **csatolt Spark-készletek** elemre. 
1. Válassza a **csatolás** lehetőséget. 
1. Válassza ki a listából a Apache Spark készletet, és adjon meg egy nevet.  
    1. Ez a lista azonosítja az elérhető szinapszis Spark-készleteket, amelyek a számítási feladatokhoz csatlakoztathatók. 
    1. Új szinapszis Spark-készlet létrehozásával kapcsolatban lásd: [Apache Spark készlet létrehozása a szinapszis Studióval](../synapse-analytics/quickstart-create-apache-spark-pool-portal.md)
1. Válassza a **kijelölt csatolás** lehetőséget. 

### <a name="attach-a-pool-with-the-python-sdk"></a>Készlet csatolása a Python SDK-val

A **PYTHON SDK** -t a Apache Spark készlet csatlakoztatására is használhatja. 

A kód követése, 
1. A SynapseCompute konfigurálja a rel,

   1. Az `linked_service` előző lépésben létrehozott vagy beolvasott LinkedService. 
   1. A csatolni kívánt számítási cél típusa `SynapseSpark`
   1. Az Apache Spark-készlet neve. Ennek egyeznie kell egy, az Azure szinapszis Analytics-munkaterületen található meglévő Apache Spark-készlettel.
   
1. Egy gépi tanulási ComputeTarget hoz létre a beadásával, 
   1. A használni kívánt Machine learning-munkaterület `ws`
   1. A Azure Machine Learning munkaterületen belüli számításra hivatkozó név. 
   1. A szinapszis számítási beállításainak konfigurálásakor megadott attach_configuration.
       1. A ComputeTarget. Attach () hívása aszinkron módon történik, ezért a rendszer a hívás befejeződéséig blokkolja a mintákat.

```python
from azureml.core.compute import SynapseCompute, ComputeTarget

attach_config = SynapseCompute.attach_configuration(linked_service, #Linked synapse workspace alias
                                                    type='SynapseSpark', #Type of assets to attach
                                                    pool_name="<Synapse Spark pool name>") #Name of Synapse spark pool 

synapse_compute = ComputeTarget.attach(workspace= ws,                
                                       name="<Synapse Spark pool alias in Azure ML>", 
                                       attach_configuration=attach_config
                                      )

synapse_compute.wait_for_completion()
```

Ellenőrizze, hogy a Apache Spark készlet csatolva van-e.

```python
ws.compute_targets['Synapse Spark pool alias']
```

## <a name="launch-synapse-spark-pool-for-data-preparation-tasks"></a>A szinapszis Spark-készlet elindítása adatelőkészítési feladatokhoz

Az Apache Spark készlettel való adatelőkészítés megkezdéséhez adja meg a Apache Spark készlet nevét:

> [!IMPORTANT]
> A Apache Spark készlet használatának folytatásához jeleznie kell, hogy melyik számítási erőforrást kell használnia az összes adat huzavona-feladatban, `%synapse` egyetlen sornyi kóddal és `%%synapse` több sorba. 

```python
%synapse start -c SynapseSparkPoolAlias
```

A munkamenet megkezdése után megtekintheti a munkamenet metaadatait.

```python
%synapse meta
```

Megadhat egy [Azure Machine learning környezetet](concept-environments.md) , amelyet a Apache Spark-munkamenet során használhat. Csak a környezetben megadott Conda-függőségek lépnek életbe. A Docker-rendszerkép nem támogatott.

>[!WARNING]
>  A környezeti Conda-függőségekben megadott Python-függőségek Apache Spark készletekben nem támogatottak. Jelenleg csak a rögzített Python-verziók támogatottak. A Python-verzió ellenőrzéséhez  `sys.version_info` adja meg a szkriptjét is.

A következő kód létrehozza a környezetet, `myenv` amely a munkamenet megkezdése előtt telepíti a `azureml-core` 1.20.0 és `numpy` a Version 1.17.0 verziót. Ezt a környezetet a Apache Spark munkamenet-utasításban is felveheti `start` .

```python

from azureml.core import Workspace, Environment

# creates environment with numpy and azureml-core dependencies
ws = Workspace.from_config()
env = Environment(name="myenv")
env.python.conda_dependencies.add_pip_package("azureml-core==1.20.0")
env.python.conda_dependencies.add_conda_package("numpy==1.17.0")
env.register(workspace=ws)
```

Az Apache Spark készlettel és az egyéni környezettel való adatelőkészítés megkezdéséhez adja meg a Apache Spark készlet nevét, valamint azt, hogy melyik környezetet kívánja használni a Apache Spark munkamenetben. Emellett megadhatja az előfizetés AZONOSÍTÓját, a Machine learning-munkaterület erőforráscsoportot és a Machine learning-munkaterület nevét.

```python
%synapse start -c SynapseSparkPoolAlias -e myenv -s AzureMLworkspaceSubscriptionID -r AzureMLworkspaceResourceGroupName -w AzureMLworkspaceName
```
## <a name="load-data-from-storage"></a>Adatok betöltése a tárolóból

A Apache Spark-munkamenet elindítása után olvassa el az előkészíteni kívánt adatait. Az Azure Blob Storage és a Azure Data Lake Storage Generations 1. és 2. generációja támogatja az betöltést.

A tárolási szolgáltatásokból kétféleképpen tölthetők be adatok: 

* A Hadoop Distributed Files System (HDFS) útvonal használatával közvetlenül tölthetők be az adatok a tárolóból.

* Adatok olvasása meglévő [Azure Machine learning adatkészletből](how-to-create-register-datasets.md).

A tárolási szolgáltatások eléréséhez **Storage blob-Adatolvasó** engedélyekre van szükség. Ha vissza szeretné írni ezeket a tárolási szolgáltatásokat, szüksége lesz a **Storage blob adatközreműködői** engedélyeire. [További információ a tárolási engedélyekről és a szerepkörökről](../storage/common/storage-auth-aad-rbac-portal.md#azure-roles-for-blobs-and-queues).

### <a name="load-data-with-hadoop-distributed-files-system-hdfs-path"></a>Az Hadoop Distributed Files System (HDFS) elérési úttal rendelkező adatbetöltése

Az adatok a tárolóból a megfelelő HDFS elérési úttal való betöltéséhez és beolvasásához azonnal elérhetővé kell tennie az adatokhoz való hozzáférési hitelesítő adatokat. Ezek a hitelesítő adatok a tárolási típusától függően eltérőek.  

A következő kód azt mutatja be, hogyan lehet az **Azure Blob Storage** -ból származó adatok beolvasását egy Spark-dataframe a közös hozzáférésű aláírás (SAS) token vagy a hozzáférési kulcs használatával. 

```python
%%synapse

# setup access key or SAS token
sc._jsc.hadoopConfiguration().set("fs.azure.account.key.<storage account name>.blob.core.windows.net", "<access key>")
sc._jsc.hadoopConfiguration().set("fs.azure.sas.<container name>.<storage account name>.blob.core.windows.net", "<sas token>")

# read from blob 
df = spark.read.option("header", "true").csv("wasbs://demo@dprepdata.blob.core.windows.net/Titanic.csv")
```

A következő kód azt mutatja be, hogyan lehet adatokat beolvasni az **1. generációs (1. ADLS) Azure Data Lake Storage** az egyszerű szolgáltatásnév hitelesítő adataival. 

```python

# setup service principal which has access of the data
sc._jsc.hadoopConfiguration().set("fs.adl.account.<storage account name>.oauth2.access.token.provider.type","ClientCredential")

sc._jsc.hadoopConfiguration().set("fs.adl.account.<storage account name>.oauth2.client.id", "<client id>")

sc._jsc.hadoopConfiguration().set("fs.adl.account.<storage account name>.oauth2.credential", "<client secret>")

sc._jsc.hadoopConfiguration().set("fs.adl.account.<storage account name>.oauth2.refresh.url",
https://login.microsoftonline.com/<tenant id>/oauth2/token)

df = spark.read.csv("adl://<storage account name>.azuredatalakestore.net/<path>")

```

A következő kód azt mutatja be, hogyan lehet adatokat beolvasni **Azure Data Lake Storage 2. generációból (ADLS Gen 2)** az egyszerű szolgáltatásnév hitelesítő adataival. 

```python
# setup service principal which has access of the data
sc._jsc.hadoopConfiguration().set("fs.azure.account.auth.type.<storage account name>.dfs.core.windows.net","OAuth")
sc._jsc.hadoopConfiguration().set("fs.azure.account.oauth.provider.type.<storage account name>.dfs.core.windows.net", "org.apache.hadoop.fs.azurebfs.oauth2.ClientCredsTokenProvider")
sc._jsc.hadoopConfiguration().set("fs.azure.account.oauth2.client.id.<storage account name>.dfs.core.windows.net", "<client id>")
sc._jsc.hadoopConfiguration().set("fs.azure.account.oauth2.client.secret.<storage account name>.dfs.core.windows.net", "<client secret>")
sc._jsc.hadoopConfiguration().set("fs.azure.account.oauth2.client.endpoint.<storage account name>.dfs.core.windows.net",
https://login.microsoftonline.com/<tenant id>/oauth2/token)


df = spark.read.csv("abfss://<container name>@<storage account>.dfs.core.windows.net/<path>")

```

### <a name="read-in-data-from-registered-datasets"></a>Adatok beolvasása a regisztrált adatkészletekből

Egy meglévő regisztrált adatkészletet is beszerezhet a munkaterületen, és elvégezheti az adatok előkészítését egy Spark-dataframe való átalakítással.

A következő példa hitelesíti a munkaterületet, beolvas egy regisztrált TabularDataset, `blob_dset` amely a blob Storage-ban lévő fájlokra hivatkozik, és egy Spark-dataframe alakítja át. Amikor egy Spark-dataframe alakítja át az adatkészleteket, kihasználhatja az `pyspark` adatfeltárási és-előkészítési kódtárakat.  

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

## <a name="perform-data-wrangling-tasks"></a>Adatok huzavona feladatok végrehajtása

Az adatok lekérése és megismerése után huzavona-feladatokat hajthat végre.

A következő kód kibontja az előző szakaszban található HDFS-példát, és kiszűri a Spark dataframe-ben lévő, a `df` **túlélő** oszlop és az **életkor** szerint listázó csoportok alapján.

```python
%%synapse
from pyspark.sql.functions import col, desc

df.filter(col('Survived') == 1).groupBy('Age').count().orderBy(desc('count')).show(10)

df.show()

```

## <a name="save-data-to-storage-and-stop-spark-session"></a>Adattárolás és a Spark-munkamenet leállítása

Az adatfeltárás és-előkészítés befejezése után az előkészített adatait az Azure Storage-fiókjában tárolhatja későbbi használatra.

A következő példában az előkészített adatlemez visszakerül az Azure Blob Storage-ba, és felülírja az eredeti `Titanic.csv` fájlt a `training_data` címtárban. A tárterületre való visszaíráshoz **Storage blob-adatközreműködői** engedélyekre van szükség. [További információ a tárolási engedélyekről és a szerepkörökről](../storage/common/storage-auth-aad-rbac-portal.md#azure-roles-for-blobs-and-queues).

```python
%% synapse

df.write.format("csv").mode("overwrite").save("wasbs://demo@dprepdata.blob.core.windows.net/training_data/Titanic.csv")
```

Amikor befejezte az adatelőkészítést, és mentette az előkészített adatait a Storage-ba, ne használja a Apache Spark készletet a következő paranccsal.

```python
%synapse stop
```

## <a name="create-dataset-to-represent-prepared-data"></a>Adatkészlet létrehozása az előkészített adatokat képviselő adatkészletek létrehozásához

Ha készen áll arra, hogy felhasználhassa az előkészített adatokat a modell betanításához, kapcsolódjon a tárhelyhez egy [Azure Machine learning adattárral](how-to-access-data.md), és adja meg, hogy mely fájl (oka) t szeretné használni [Azure Machine learning adatkészlettel](how-to-create-register-datasets.md).

A következő mintakód például

* Feltételezi, hogy már létrehozott egy adattárt, amely kapcsolódik ahhoz a tárolási szolgáltatáshoz, ahová az előkészített adatait mentette.  
* Lekérdezi a meglévő adattárat a `mydatastore` munkaterületről `ws` a Get () metódussal.
* Létrehoz egy [FileDataset](how-to-create-register-datasets.md#filedataset), `train_ds` amely hivatkozik a mappában található előkészített adatfájlokra `training_data` `mydatastore` .  
* Létrehozza a változót `input1` , amely később is felhasználható, hogy az adatkészlet adatfájljai `train_ds` elérhetők legyenek egy számítási cél számára.

```python
from azureml.core import Datastore, Dataset

datastore = Datastore.get(ws, datastore_name='mydatastore')

datastore_paths = [(datastore, '/training_data/')]
train_ds = Dataset.File.from_files(path=datastore_paths, validate=True)
input1 = train_ds.as_mount()

```

## <a name="example-notebook"></a>Példajegyzetfüzet

Tekintse [meg ezt a végpontok közötti jegyzetfüzetet](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/azure-synapse/spark_session_on_synapse_spark_pool.ipynb) , amelyből megtudhatja, hogyan végezheti el az adatok előkészítését és a modell betanítását egyetlen jegyzetfüzetből az Azure szinapszis Analytics és a Azure Machine learning használatával.

## <a name="next-steps"></a>Következő lépések

* [Modell betanítása](how-to-set-up-training-targets.md).
* [Betanítás Azure Machine Learning adatkészlettel](how-to-train-with-datasets.md)
