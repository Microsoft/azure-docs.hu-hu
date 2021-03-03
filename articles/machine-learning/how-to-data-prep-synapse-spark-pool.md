---
title: Adat-előkészítés Spark-készletekkel (előzetes verzió)
titleSuffix: Azure Machine Learning
description: Ismerje meg, hogyan csatolhat Spark-készleteket adatelőkészítéshez az Azure szinapszis és a Azure Machine Learning használatával
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: nibaccam
author: nibaccam
ms.reviewer: nibaccam
ms.date: 03/02/2021
ms.custom: how-to, devx-track-python, data4ml
ms.openlocfilehash: 87e03b6aee122c5a26d4388ca8b570aa6cdf7b55
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/02/2021
ms.locfileid: "101662306"
---
# <a name="attach-synapse-spark-pools-for-data-preparation-with-azure-synapse-preview"></a>Szinapszis Spark-készletek csatlakoztatása adatelőkészítéshez az Azure szinapszis (előzetes verzió) szolgáltatással

Ebből a cikkből megtudhatja, hogyan csatolhat és indíthat el egy [Azure szinapszis](/synapse-analytics/overview-what-is.md) által támogatott Apache Spark-készletet az adatelőkészítéshez. 

>[!IMPORTANT]
> A Azure Machine Learning és az Azure szinapszis-integráció előzetes verzióban érhető el. A cikkben bemutatott képességek olyan csomagot alkalmaznak `azureml-synapse` , amely a [kísérleti](/python/api/overview/azure/ml/?preserve-view=true&view=azure-ml-py#stable-vs-experimental) előzetes verzió funkcióit tartalmazza, amelyek bármikor megváltozhatnak.

## <a name="azure-machine-learning-and-azure-synapse-integration-preview"></a>Azure Machine Learning és az Azure szinapszis integrációja (előzetes verzió)

Az Azure szinapszis Azure Machine Learning (előzetes verzió) szolgáltatással való integrációja lehetővé teszi, hogy az Azure szinapszis által támogatott Apache Spark-készletet az interaktív adatelemzéshez és-előkészítéshez csatolja. Ezzel az integrációval dedikált számítási feladattal rendelkezhet az adatok előkészítéséhez a skálán, mindezt a gépi tanulási modellek betanításához használt Python-jegyzetfüzetben.

## <a name="prerequisites"></a>Előfeltételek

* [Hozzon létre egy Azure Machine learning munkaterületet](how-to-manage-workspace.md?tabs=python).

* [Hozzon létre egy szinapszis-munkaterületet Azure Portal](../synapse-analytics/quickstart-create-workspace.md).

* [Apache Spark-készlet létrehozása a Azure Portal, a web Tools vagy a szinapszis Studio használatával](../synapse-analytics/quickstart-create-apache-spark-pool-portal.md)

* [Telepítse a Azure Machine learning PYTHON SDK](/python/api/overview/azure/ml/install?preserve-view=true&view=azure-ml-py) -t, amely tartalmazza a `azureml-synapse` csomagot (előzetes verzió). 
    * Telepítheti saját magát is, de csak az SDK 1,20-es vagy újabb verziójával kompatibilis. 
        ```python
        pip install azureml-synapse
        ```

## <a name="link-machine-learning-workspace-and-synapse-assets"></a>A Machine learning-munkaterület és a szinapszis-eszközök összekapcsolása

Mielőtt a szinapszis Spark-készletet csatolja az adatelőkészítéshez, az Azure Machine Learning-munkaterületnek kapcsolódnia kell az Azure szinapszis-munkaterületéhez. 

A ML-munkaterületet és a szinapszis-munkaterületet a [PYTHON SDK](#link-sdk) -val vagy a [Azure Machine learning Studióval](#link-studio)is összekapcsolhatja. 

> [!IMPORTANT]
> A szinapszis munkaterülethez való sikeres hivatkozáshoz a szinapszis munkaterület **tulajdonosi** szerepkörét kell megadnia. Győződjön meg arról, hogy a [Azure Portal](https://ms.portal.azure.com/)a hozzáférését.
>
> Ha nem **tulajdonosa** a szinapszis munkaterületnek, de meglévő társított szolgáltatást szeretne használni, tekintse meg [a meglévő társított szolgáltatás beszerzése](#get-an-existing-linked-service)című témakört.


<a name="link-sdk"></a>
### <a name="link-workspaces-with-the-python-sdk"></a>Munkaterületek összekapcsolása a Python SDK-val

A következő kód a [`LinkedService`](/python/api/azureml-core/azureml.core.linked_service.linkedservice?preserve-view=true&view=azure-ml-py) és osztályokat alkalmazza a alkalmazásra [`SynapseWorkspaceLinkedServiceConfiguration`](/python/api/azureml-core/azureml.core.linked_service.synapseworkspacelinkedserviceconfiguration?preserve-view=true&view=azure-ml-py) , 

* Kapcsolja össze a Machine learning-munkaterületet az Azure szinapszis-munkaterülettel `ws` . 
* A szinapszis-munkaterület regisztrálása a Azure Machine Learning társított szolgáltatásként.

``` python
import datetime  
from azureml.core import Workspace, LinkedService, SynapseWorkspaceLinkedServiceConfiguration

# Azure Machine Learning workspace
ws = Workspace.from_config()

#link configuration 
synapse_link_config = SynapseWorkspaceLinkedServiceConfiguration(
    subscription_id=ws.subscription_id,
    resource_group= 'your resource group',
    name='mySynapseWorkspaceName')

# Link workspaces and register Synapse workspace in Azure Machine Learning
linked_service = LinkedService.register(workspace = ws,              
                                            name = 'synapselink1',    
                                            linked_service_config = synapse_link_config)
```
> [!IMPORTANT] 
> Felügyelt identitás `system_assigned_identity_principal_id` jön létre az egyes társított szolgáltatásokhoz. Ezt a felügyelt identitást meg kell adni a szinapszis munkaterülethez tartozó **szinapszis Apache Spark rendszergazdai** szerepkörnek, mielőtt elkezdené a szinapszis-munkamenetet. [Rendelje hozzá a szinapszis Apache Spark rendszergazdai szerepkört a kezelt identitáshoz a szinapszis Studióban](../synapse-analytics/security/how-to-manage-synapse-rbac-role-assignments.md).
>
> Egy adott társított szolgáltatás megkereséséhez használja a következőt: `system_assigned_identity_principal_id` `LinkedService.get('<your-mlworkspace-name>', '<linked-service-name>')` .

<a name="link-studio"></a>
### <a name="link-workspaces-via-studio"></a>Munkaterületek összekapcsolása a Studio használatával

Kapcsolja össze a Machine learning-munkaterületet és a szinapszis-munkaterületet a Azure Machine Learning Studio használatával a következő lépésekkel: 

1. Jelentkezzen be a [Azure Machine learning studióba](https://ml.azure.com/).
1. A bal oldali ablaktábla **kezelés** szakaszában válassza a **társított szolgáltatások** elemet.
1. Válassza az **integráció hozzáadása** elemet.
1. A **hivatkozás munkaterület** űrlapon töltse ki a mezőket

   |Mező| Leírás    
   |---|---
   |Név| Adja meg a társított szolgáltatás nevét. Ezt a nevet fogja használni az adott társított szolgáltatásra való hivatkozáshoz.
   |Előfizetés neve | Válassza ki a Machine learning-munkaterülethez társított előfizetés nevét. 
   |Szinapszis-munkaterület | Válassza ki azt a szinapszis-munkaterületet, amelyhez hivatkozni kíván. 
   
1. Kattintson a **tovább** gombra a **Spark-készletek kiválasztása (opcionális)** űrlap megnyitásához. Ezen az űrlapon kiválaszthatja, hogy melyik szinapszis Spark-készletet szeretné csatolni a munkaterülethez

1. A **tovább** gombra kattintva nyissa meg a **felülvizsgálati** űrlapot, és jelölje ki a kívánt beállításokat. 
1. Válassza a **Létrehozás** lehetőséget a társított szolgáltatás-létrehozási folyamat befejezéséhez.

## <a name="get-an-existing-linked-service"></a>Meglévő társított szolgáltatás beszerzése

Meglévő társított szolgáltatás lekéréséhez és használatához **felhasználói vagy közreműködői** engedély szükséges a szinapszis munkaterülethez.

Ez a példa egy meglévő társított szolgáltatást kér le a `synapselink1` munkaterületről a `ws` [`get()`](/python/api/azureml-core/azureml.core.linkedservice?preserve-view=true&view=azure-ml-py#get-workspace--name-) metódussal.
```python
linked_service = LinkedService.get(ws, 'synapselink1')
```

### <a name="manage-linked-services"></a>Társított szolgáltatások kezelése

A munkaterületek leválasztásához használja a `unregister()` metódust.

``` python
linked_service.unregister()
```

A Machine learning-munkaterülethez kapcsolódó összes társított szolgáltatás megtekintése. 

```python
LinkedService.list(ws)
```
 
## <a name="attach-synapse-spark-pool-as-a-compute"></a>Szinapszis Spark-készlet csatlakoztatása számítási feladatokhoz

A munkaterületek összekapcsolását követően csatoljon egy szinapszis Spark-készletet dedikált számítási erőforrásként az adat-előkészítési feladatokhoz. 

Szinapszis Spark-készleteket a-n keresztül csatolhat,
* Azure Machine Learning Studio
* [Azure Resource Manager- (ARM-) sablonok](https://github.com/Azure/azure-quickstart-templates/blob/master/101-machine-learning-linkedservice-create/azuredeploy.json)
* A Python SDK 

Az alábbi lépéseket követve csatolhat egy szinapszis Spark-készletet a studióhoz. 

1. Jelentkezzen be a [Azure Machine learning studióba](https://ml.azure.com/).
1. A bal oldali ablaktábla **kezelés** szakaszában válassza a **társított szolgáltatások** elemet.
1. Válassza ki a szinapszis munkaterületet.
1. Kattintson a bal felső sarokban található **csatolt Spark-készletek** elemre. 
1. Válassza a **csatolás** lehetőséget. 
1. Válassza ki a szinapszis Spark-készletet a listából, és adjon meg egy nevet.  
    1. Ez a lista azonosítja az elérhető szinapszis Spark-készleteket, amelyek a számítási feladatokhoz csatlakoztathatók. 
    1. Új szinapszis Spark-készlet létrehozásával kapcsolatban lásd: [Apache Spark készlet létrehozása a szinapszis Studióval](../synapse-analytics/quickstart-create-apache-spark-pool-portal.md)
1. Válassza a **kijelölt csatolás** lehetőséget. 


A **PYTHON SDK** -t is használhatja a szinapszis Spark-készlet csatolásához. 

A kód követése, 
1. A SynapseCompute konfigurálja a rel,

   1. Az `linked_service` előző lépésben létrehozott vagy beolvasott LinkedService. 
   1. A csatolni kívánt számítási cél típusa `SynapseSpark`
   1. A szinapszis Spark-készlet neve. Ennek egyeznie kell egy meglévő, a szinapszis-munkaterületen található Apache Spark-készlettel.
   
1. Egy gépi tanulási ComputeTarget hoz létre a beadásával, 
   1. A használni kívánt Machine learning-munkaterület `ws`
   1. A Machine learning-munkaterületen belüli számításra hivatkozó név. 
   1. A SynapseCompute konfigurálásakor megadott attach_configuration.
       1. A ComputeTarget. Attach () hívása aszinkron módon történik, ezért a rendszer a hívás befejeződéséig blokkolja a mintákat.

```python
from azureml.core.compute import SynapseCompute, ComputeTarget

attach_config = SynapseCompute.attach_configuration(linked_service, #Linked synapse workspace alias
                                                    type='SynapseSpark', #Type of assets to attach
                                                    pool_name="<Synapse Spark pool name>") #Name of Synapse spark pool 

synapse_compute = ComputeTarget.attach(workspace= ws,                
                                       name='<Synapse Spark pool alias in Azure ML>', 
                                       attach_configuration=attach_config
                                      )

synapse_compute.wait_for_completion()
```

Ellenőrizze, hogy a szinapszis Spark-készlet csatolva van-e.

```python
ws.compute_targets['Synapse Spark pool alias']
```

## <a name="launch-synapse-spark-pool-for-data-preparation-tasks"></a>A szinapszis Spark-készlet elindítása adatelőkészítési feladatokhoz

Megadhat egy [Azure Machine learning környezetet](concept-environments.md) , amelyet a szinapszis-munkamenet során használhat. Csak a környezetben megadott Conda-függőségek lépnek életbe. A Docker-rendszerkép nem támogatott.

>[!WARNING]
>  A környezeti Conda-függőségekben megadott Python-függőségek nem támogatottak a szinapszis Spark-készletekben. Jelenleg csak a rögzített Python-verziók támogatottak. A Python-verzió ellenőrzéséhez  `sys.version_info` adja meg a szkriptjét is.

A következő kód létrehozza a környezetet, `myenv` amely a munkamenet megkezdése előtt telepíti a `azureml-core` 1.20.0 és `numpy` a Version 1.17.0 verziót. Ezt a környezetet ezután felveheti a szinapszis munkamenet- `start` utasításba.

```python

from azureml.core import Workspace, Environment

# creates environment with numpy and azureml-core dependencies
ws = Workspace.from_config()
env = Environment(name="myenv")
env.python.conda_dependencies.add_pip_package("azureml-core==1.20.0")
env.python.conda_dependencies.add_conda_package("numpy==1.17.0")
env.register(workspace=ws)
```

Az adatelőkészítés a szinapszis Spark-készlettel való megkezdéséhez adja meg a szinapszis Spark-készlet nevét, és adja meg az előfizetés AZONOSÍTÓját, a Machine learning-munkaterület erőforráscsoportot, a Machine learning-munkaterület nevét, valamint azt, hogy melyik környezetet kell használni a szinapszis-munkamenet során. 

> [!IMPORTANT]
> A szinapszis Spark-készlet használatának folytatásához jeleznie kell, hogy melyik számítási erőforrást kell használnia az adatelőkészítési feladatokban az `%synapse` egyetlen sornyi kóddal és `%%synapse` több sorba. 

```python
%synapse start -c SynapseSparkPoolAlias -s AzureMLworkspaceSubscriptionID -r AzureMLworkspaceResourceGroupName -w AzureMLworkspaceName -e myenv
```

A munkamenet megkezdése után megtekintheti a munkamenet metaadatait.

```python
%synapse meta
```

## <a name="load-data-from-storage"></a>Adatok betöltése a tárolóból

A szinapszis Spark-munkamenetének megkezdése után olvassa el az előkészíteni kívánt adatait. Az Azure Blob Storage és a Azure Data Lake Storage Generations 1. és 2. generációja támogatja az betöltést.

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
sc._jsc.hadoopConfiguration().set("fs.azure.sas.<container name>.<storage account name>.blob.core.windows.net", "sas token")

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

A következő példa egy regisztrált TabularDataset kap, `blob_dset` amely a blob Storage-ban lévő fájlokra hivatkozik, és egy Spark-dataframe alakítja át. Amikor egy Spark-dataframe alakítja át az adatkészleteket, kihasználhatja az `pyspark` adatfeltárási és-előkészítési kódtárakat.  

``` python

%%synapse
from azureml.core import Workspace, Dataset

dset = Dataset.get_by_name(ws, "blob_dset")
spark_df = dset.to_spark_dataframe()
```

## <a name="perform-data-preparation-tasks"></a>Adat-előkészítési feladatok végrehajtása

Az adatok lekérése és vizsgálata után adatelőkészítési feladatokat végezhet el.

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

Amikor befejezte az adatelőkészítést, és mentette az előkészített adatait a Storage-ba, ne használja a szinapszis Spark-készletet a következő paranccsal.

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

Tekintse [meg ezt a végpontok közötti jegyzetfüzetet](../synapse-analytics/overview-what-is.md) , amelyből megtudhatja, hogyan végezheti el az adatok előkészítését és a modell betanítását egyetlen jegyzetfüzetből az Azure szinapszis és a Azure Machine learning használatával.

## <a name="next-steps"></a>Következő lépések

* [Modell betanítása](how-to-set-up-training-targets.md).
* [Betanítás Azure Machine Learning adatkészlettel](how-to-train-with-datasets.md)
* [Hozzon létre egy Azure Machine learning-adatkészletet](how-to-create-register-datasets.md).

