---
title: Adatbetöltés az Azure Data Factoryvel
titleSuffix: Azure Machine Learning
description: Megtudhatja, hogyan hozhat létre adatfeldolgozási folyamatot Azure Data Factory és az egyes lehetőségek előnyeivel.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: iefedore
author: eedorenko
manager: davete
ms.reviewer: larryfr
ms.date: 01/26/2021
ms.topic: conceptual
ms.custom: how-to, devx-track-python, data4ml
ms.openlocfilehash: b842934ea6bb458a59a53ea7068aa9ece98aacf1
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98796147"
---
# <a name="data-ingestion-with-azure-data-factory"></a>Adatbetöltés az Azure Data Factoryvel

Ebből a cikkből megtudhatja, hogyan hozhat létre adatfeldolgozási folyamatot [Azure Data Factorysal](../data-factory/introduction.md). Ez a Azure Data Factoryi folyamat a [Azure Machine learning](overview-what-is-azure-ml.md)használatával történő adatbevitelre szolgál. Data Factory lehetővé teszi az adatok egyszerű kinyerését, átalakítását és betöltését (ETL). Az adatgyűjtés és a tárolóba való betöltés után felhasználhatja a gépi tanulási modellek betanítását Azure Machine Learning.

Az egyszerű adatátalakítás a natív Data Factory tevékenységekkel és eszközökkel, például az [adatfolyamokkal](../data-factory/control-flow-execute-data-flow-activity.md)kezelhető. Bonyolultabb forgatókönyvek esetén az adatok feldolgozhatók egyéni kódokkal is. Például: Python vagy R code.

## <a name="compare-azure-data-factory-data-ingestion-pipelines"></a>Azure Data Factory adatfeldolgozási folyamatok összehasonlítása 
Számos gyakori módszer áll rendelkezésre a Data Factory az adatfeldolgozás során történő átalakítására. Minden technikának vannak előnyei és hátrányai, amelyek segítségével megállapíthatja, hogy az megfelelő-e az adott használati esethez:

| Módszer | Előnyök | Hátrányok |
| ----- | ----- | ----- |
| Data Factory + Azure Functions | <li> Kis késleltetés, kiszolgáló nélküli számítás<li>Állapot-nyilvántartó függvények<li>Újrafelhasználható függvények | Csak rövid ideig futó feldolgozás esetén jó |
| Data Factory + egyéni összetevő | <li>Nagyméretű párhuzamos számítástechnika<li>Nagy teljesítményű algoritmusokhoz | <li>A kód sortörését igényli egy végrehajtható fájlba<li>A függőségek és az i/o kezelési bonyolultsága |
| Data Factory + Azure Databricks notebook |<li> Apache Spark<li>Natív Python-környezet |<li>Költséges lehet<li>A fürtök létrehozása kezdetben időt vesz igénybe, és késleltetést okoz

## <a name="azure-data-factory-with-azure-functions"></a>Azure Data Factory az Azure functions szolgáltatással

Azure Functions lehetővé teszi, hogy kis mennyiségű kódot (functions) futtasson anélkül, hogy az alkalmazás-infrastruktúrával kellene foglalkoznia. Ebben a beállításban az Azure-függvénybe becsomagolt egyéni Python-kóddal dolgozza fel az adatfeldolgozást. 

A függvényt a [Azure Data Factory Azure Function tevékenység](../data-factory/control-flow-azure-function-activity.md)hívja meg. Ez a megközelítés jó választás az egyszerűsített adatátalakításokhoz. 

![Az ábrán egy Azure Data Factory folyamat látható, amely az Azure Function funkcióval és a ML-folyamat futtatásával, valamint egy Azure Machine Learning folyamattal, a betanítási modellel, valamint a nyers és előkészített adattal való interakcióval foglalkozik.](media/how-to-data-ingest-adf/adf-function.png)



* Előnyök:
    * Az adatok feldolgozása kiszolgáló nélküli számításban történik, viszonylag alacsony késéssel
    * Data Factory folyamat olyan [tartós Azure-függvényt](../azure-functions/durable/durable-functions-overview.md) hívhat meg, amely kifinomult Adatátalakítási folyamatot valósíthat meg 
    * Az adatok átalakításának részleteit az Azure-függvény elküldi, amely a többi helyről újra felhasználható és meghívható
* Hátrányai
    * Az ADF-sel való használat előtt létre kell hozni a Azure Functions.
    * Azure Functions csak rövid ideig futó adatfeldolgozás esetén jó

## <a name="azure-data-factory-with-custom-component-activity"></a>Azure Data Factory egyéni összetevő tevékenységgel

Ebben a beállításban az adatfeldolgozást egyéni Python-kóddal, végrehajtható fájlba csomagolja a rendszer. A rendszer egy [ Azure Data Factory egyéni összetevő-tevékenységgel](../data-factory/transform-data-using-dotnet-custom-activity.md)hívja meg. Ez a megközelítés jobban illeszkedik az előző technikánál nagyobb méretű adatmennyiséghez.

![Az ábrán egy Azure Data Factory folyamat látható, amely egy egyéni összetevővel és az M L folyamat futtatásával, valamint egy Azure Machine Learning folyamattal, valamint a betanítási modellel, valamint a nyers és az előkészített adattal való interakcióval.](media/how-to-data-ingest-adf/adf-customcomponent.png)

* Előnyök:
    * Az adat [Azure batch](../batch/batch-technical-overview.md) készletre lesz feldolgozva, amely nagy léptékű párhuzamos és nagy teljesítményű számítástechnikai lehetőségeket biztosít
    * Nehéz algoritmusok futtatására és jelentős adatmennyiségek feldolgozására is használható.
* Hátrányok:
    * Azure Batch készletet a használata előtt létre kell hozni Data Factory
    * A Python-kód egy végrehajtható fájlba való becsomagolásával kapcsolatos mérnöki tevékenység. A függőségek és a bemeneti/kimeneti paraméterek összetettsége

## <a name="azure-data-factory-with-azure-databricks-python-notebook"></a>Azure Data Factory Azure Databricks Python notebooktal

A [Azure Databricks](https://azure.microsoft.com/services/databricks/) egy Apache Spark-alapú elemzési platform a Microsoft Cloud-ban.

Ebben a technikában az adatátalakítást egy Azure Databricks-fürtön futó [Python-jegyzetfüzet](../data-factory/transform-data-using-databricks-notebook.md)hajtja végre. Ez valószínűleg a leggyakoribb megközelítés, amely kihasználja egy Azure Databricks szolgáltatás teljes erejét. A szolgáltatás nagy léptékű elosztott adatfeldolgozásra lett tervezve.

![A diagram egy Azure Data Factory folyamatot mutat be, amely a Azure Databricks Python és az M L folyamat futtatásával, valamint egy Azure Machine Learning folyamattal, a betanítási modellel, valamint a nyers és az előkészített adattal való interakcióval foglalkozik.](media/how-to-data-ingest-adf/adf-databricks.png)

* Előnyök:
    * Az adatfeldolgozás a leghatékonyabban használt Azure-szolgáltatásra épül, amelyet Apache Spark környezet készít biztonsági mentésre
    * A Python natív támogatása az adatelemzési keretrendszerek és kódtárak mellett, beleértve a TensorFlow, a PyTorch és a scikit – Learn
    * Nincs szükség a Python-kód functions vagy végrehajtható modulokba való becsomagolására. A kód a következőképpen működik.
* Hátrányok:
    * Azure Databricks infrastruktúrát a használata előtt létre kell hozni Data Factory
    * Költséges lehet a Azure Databricks konfigurációjától függően
    * A számítási fürtök "hideg" módból való elforgatása hosszabb időt vesz igénybe, amely nagy késleltetést biztosít a megoldásnak 
    

## <a name="consume-data-in-azure-machine-learning"></a>Adatfelhasználás Azure Machine Learning 

A Data Factory folyamat elmenti az előkészített adatait a felhőalapú tárolóba (például Azure Blob vagy Azure Datalake). <br>
Használja fel az előkészített adatait a Azure Machine Learning 

* Azure Machine Learning folyamat meghívása a Data Factory folyamatból.<br>**OR**
* [Azure Machine learning adattár](how-to-access-data.md#create-and-register-datastores) és [Azure Machine learning adatkészlet](how-to-create-register-datasets.md) létrehozása későbbi használatra.

### <a name="invoke-azure-machine-learning-pipeline-from-data-factory"></a>Azure Machine Learning folyamat meghívása Data Factory

Ez a módszer [Machine learning Operations (MLOps) munkafolyamatokhoz](concept-model-management-and-deployment.md#what-is-mlops)ajánlott. Ha nem szeretne létrehozni egy Azure Machine Learning folyamatot, olvassa el az [adatok közvetlenül a tárolóból való beolvasása](#read-data-directly-from-storage)című témakört.

Minden alkalommal, amikor a Data Factory folyamat fut, 

1. Az adatmentést egy másik helyre menti a rendszer a Storage szolgáltatásban. 
1. A hely Azure Machine Learningba való átadásához a Data Factory folyamat meghívja az [Azure Machine learning](concept-ml-pipelines.md)folyamatot. A ML-folyamat meghívásakor az adatok helye és a futtatási azonosító paraméterként lesz elküldve. 
1. A ML-folyamat ezután létrehozhat egy Azure Machine Learning adattárt és adatkészletet az adathelyről. További információ: [Azure Machine learning folyamatok végrehajtása Data Factoryokban](../data-factory/transform-data-machine-learning-service.md).

![A diagram egy Azure Data Factory folyamatot és egy Azure Machine Learning folyamatot mutat be, valamint azt, hogy hogyan működnek együtt a nyers és az előkészített adatfeldolgozási folyamat. Az Data Factory folyamat az adatokat az előkészített adatadatbázisba küldi, amely egy adattárat, amely a Machine Learning munkaterületen található adatkészleteket táplálja.](media/how-to-data-ingest-adf/aml-dataset.png)

> [!TIP]
> Az adatkészletek [támogatják a verziószámozást](./how-to-version-track-datasets.md), így a ml-folyamat képes regisztrálni az adatkészlet új verzióját, amely az ADF-folyamat legutóbbi adatait mutat.

Ha az adat egy adattáron vagy adatkészleten keresztül érhető el, egy ML-modell betanítására is használható. Előfordulhat, hogy a betanítási folyamat az ADF-ből meghívott azonos ML folyamat része. Vagy lehet különálló folyamat, például kísérletezés egy Jupyter jegyzetfüzetben.

Mivel az adatkészletek támogatják a verziószámozást, és a folyamat minden futtatása új verziót hoz létre, könnyen megtudhatja, hogy az adatok melyik verzióját használták a modell betanításához.

### <a name="read-data-directly-from-storage"></a>Adatok közvetlenül a tárolóból való beolvasása

Ha nem szeretne ML-folyamatot létrehozni, közvetlenül a Storage-fiókból érheti el az adatokat, ahová az előkészített adatokat egy Azure Machine Learning adattárral és adatkészlettel menti. 

A következő Python-kód bemutatja, hogyan hozható létre egy adattár, amely az Azure DataLake 2. generációs tárolóhoz csatlakozik. [További információ az adattárolók és az egyszerű szolgáltatásnév engedélyeinek megkereséséről](how-to-access-data.md#create-and-register-datastores).

```python
ws = Workspace.from_config()
adlsgen2_datastore_name = '<ADLS gen2 storage account alias>'  #set ADLS Gen2 storage account alias in AML

subscription_id=os.getenv("ADL_SUBSCRIPTION", "<ADLS account subscription ID>") # subscription id of ADLS account
resource_group=os.getenv("ADL_RESOURCE_GROUP", "<ADLS account resource group>") # resource group of ADLS account

account_name=os.getenv("ADLSGEN2_ACCOUNTNAME", "<ADLS account name>") # ADLS Gen2 account name
tenant_id=os.getenv("ADLSGEN2_TENANT", "<tenant id of service principal>") # tenant id of service principal
client_id=os.getenv("ADLSGEN2_CLIENTID", "<client id of service principal>") # client id of service principal
client_secret=os.getenv("ADLSGEN2_CLIENT_SECRET", "<secret of service principal>") # the secret of service principal

adlsgen2_datastore = Datastore.register_azure_data_lake_gen2(
    workspace=ws,
    datastore_name=adlsgen2_datastore_name,
    account_name=account_name, # ADLS Gen2 account name
    filesystem='<filesystem name>', # ADLS Gen2 filesystem
    tenant_id=tenant_id, # tenant id of service principal
    client_id=client_id, # client id of service principal
```

Ezután hozzon létre egy adatkészletet, amely a Machine learning-feladatban használni kívánt fájl (ok) ra hivatkozik. 

A következő kód egy csv-fájlból hoz létre egy TabularDataset `prepared-data.csv` . További információ az [adatkészletek típusairól és az elfogadott fájlformátumokról](how-to-create-register-datasets.md#dataset-types). 

```python
from azureml.core import Workspace, Datastore, Dataset
from azureml.core.experiment import Experiment
from azureml.train.automl import AutoMLConfig

# retrieve data via AML datastore
datastore = Datastore.get(ws, adlsgen2_datastore)
datastore_path = [(datastore, '/data/prepared-data.csv')]
        
prepared_dataset = Dataset.Tabular.from_delimited_files(path=datastore_path)
```

Innen a használatával `prepared_dataset` hivatkozhat az előkészített adatokra, például a betanítási parancsfájlokban. Megtudhatja, hogyan lehet [modelleket betanítani Azure Machine learning adatkészletekkel](./how-to-train-with-datasets.md).

## <a name="next-steps"></a>Következő lépések

* [Databricks-jegyzetfüzet futtatása Azure Data Factory](../data-factory/transform-data-using-databricks-notebook.md)
* [Az Azure Storage-szolgáltatásokban tárolt adathozzáférés](./how-to-access-data.md#create-and-register-datastores)
* [Modellek betanítása a Azure Machine learning adatkészletekkel](./how-to-train-with-datasets.md).
* [A DevOps használata adatfeldolgozási folyamathoz](./how-to-cicd-data-ingestion.md)