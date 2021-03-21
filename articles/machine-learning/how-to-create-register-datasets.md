---
title: Azure Machine Learning-adathalmazok létrehozása
titleSuffix: Azure Machine Learning
description: Megtudhatja, hogyan hozhat létre Azure Machine Learning adatkészleteket a gépi tanulási kísérletekhez szükséges adateléréshez.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to, contperf-fy21q1, data4ml
ms.author: sihhu
author: MayMSFT
manager: cgronlun
ms.reviewer: nibaccam
ms.date: 07/31/2020
ms.openlocfilehash: 54b1fd14f97855dd42afde9a4bb34795373ff229
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "103417637"
---
# <a name="create-azure-machine-learning-datasets"></a>Azure Machine Learning-adathalmazok létrehozása

Ebből a cikkből megtudhatja, hogyan hozhat létre Azure Machine Learning adatkészleteket a helyi vagy távoli kísérletek adataihoz való hozzáféréshez a Azure Machine Learning Python SDK-val. Annak megismeréséhez, hogy az adatkészletek hogyan illeszkednek Azure Machine Learning összesített adatelérési munkafolyamataihoz, tekintse meg a [biztonságos hozzáférés adatai](concept-data.md#data-workflow) című cikket.

Az adathalmazok létrehozásával az adatforrások helyére mutató hivatkozásokat és a metaadatok másolatait is létrehozza. Mivel az adatok a meglévő helyükön maradnak, nem számítunk fel extra tárolási költséget, és nem kockáztatjuk az adatforrások integritását. Az adathalmazokat is kiértékeli a rendszer, ami a munkafolyamatok teljesítményének sebességét segíti elő. Adatkészleteket létrehozhat adattárolók, nyilvános URL-címek és [Azure Open-adatkészletek](../open-datasets/how-to-create-azure-machine-learning-dataset-from-open-dataset.md)használatával.

Alacsony kódú felhasználói felület esetén [hozzon létre Azure Machine learning adatkészleteket a Azure Machine learning Studióval.](how-to-connect-data-ui.md#create-datasets)

Azure Machine Learning adatkészletek esetében a következőket teheti:

* Az adatkészletek által hivatkozott adatokat egyetlen példányban tárolja a tárolóban.

* Zökkenőmentesen férhet hozzá az adatmodell-képzéshez a kapcsolati karakterláncok vagy az adatelérési utak miatt [További információ az adatkészletek betanításáról](how-to-train-with-datasets.md).

* Az adatmegosztás és együttműködés más felhasználókkal.

## <a name="prerequisites"></a>Előfeltételek

Az adatkészletek létrehozásához és működéséhez a következőkre lesz szüksége:

* Azure-előfizetés. Ha még nincs előfizetése, hozzon létre egy ingyenes fiókot, mielőtt hozzákezd. Próbálja ki a [Azure Machine learning ingyenes vagy fizetős verzióját](https://aka.ms/AMLFree).

* Egy [Azure Machine learning munkaterület](how-to-manage-workspace.md).

* A [Azure Machine learning SDK for Python telepítve](/python/api/overview/azure/ml/install), amely tartalmazza a azureml-adatkészletek csomagot.

    * Hozzon létre egy [Azure Machine learning számítási példányt](how-to-create-manage-compute-instance.md), amely egy teljesen konfigurált és felügyelt fejlesztői környezet, amely integrált jegyzetfüzeteket és már telepített SDK-t tartalmaz.

    **OR**

    * Saját Jupyter notebookján dolgozhat, és saját kezűleg is telepítheti az SDK-t [ezekkel az utasításokkal](/python/api/overview/azure/ml/install).

> [!NOTE]
> Egyes adatkészlet-osztályok függőségekkel rendelkeznek a [azureml-adatelőkészítés](/python/api/azureml-dataprep/) csomagon, amely csak a 64 bites Python rendszerrel kompatibilis. A Linux-felhasználók esetében ezek az osztályok csak a következő disztribúciókban támogatottak: Red Hat Enterprise Linux (7, 8), Ubuntu (14,04, 16,04, 18,04), Fedora (27, 28), Debian (8, 9) és CentOS (7). Ha nem támogatott disztribúciókat használ, kövesse [ezt az útmutatót](/dotnet/core/install/linux) a .net Core 2,1 telepítéséhez a folytatáshoz. 

## <a name="compute-size-guidance"></a>Számítási méretre vonatkozó útmutató

Adatkészlet létrehozásakor tekintse át a számítási feldolgozási teljesítményt és az adatok méretét a memóriában. A tárolóban tárolt adatai mérete nem egyezik meg a dataframe lévő adatmérettel. Például a CSV-fájlokban lévő adat akár 10x-re is kiterjeszthető egy dataframe, így egy 1 GB-os CSV-fájl 10 GB-nyi lehet egy dataframe. 

Ha tömöríti az adatait, tovább bővíthető. a tömörített parketta-formátumban tárolt 20 GB-os viszonylag ritka adat a memóriában is kiterjeszthető ~ 800 GB-ra. Mivel a Parquet-fájlok oszlopos formátumban tárolják az adattárolást, ha csak az oszlopok felét kell megadnia, akkor a memóriában csak a ~ 400 GB-ot kell betölteni.

[További információ az adatfeldolgozás optimalizálásáról Azure Machine Learningban](concept-optimize-data-processing.md).

## <a name="dataset-types"></a>Adathalmaz-típusok

Kétféle adatkészlet van, attól függően, hogy a felhasználók hogyan használják őket a képzésben. FileDatasets és TabularDatasets. Mindkét típus használható Azure Machine Learning betanítási munkafolyamatokban, beleértve a becslések, a AutoML, a hyperDrive és a folyamatokat is. 

### <a name="filedataset"></a>FileDataset

A [FileDataset](/python/api/azureml-core/azureml.data.file_dataset.filedataset) egy vagy több fájlt hivatkozik az adattárolókban vagy a nyilvános URL-címeken. Ha az adatok már meg vannak tisztítva, és készen állnak a betanítási kísérletekben való használatra, [letöltheti vagy csatlakoztathatja](how-to-train-with-datasets.md#mount-vs-download) a fájlokat a számítási FileDataset objektumként. 

Javasoljuk, hogy a gépi tanulási munkafolyamatokhoz FileDatasets, mivel a forrásfájlok bármilyen formátumban lehetnek, ami lehetővé teszi a gépi tanulási forgatókönyvek szélesebb körének használatát, beleértve a mély tanulást is.

Hozzon létre egy FileDataset a [PYTHON SDK](#create-a-filedataset) -val vagy a [Azure Machine learning Studióval](how-to-connect-data-ui.md#create-datasets) .
### <a name="tabulardataset"></a>TabularDataset

A [TabularDataset](/python/api/azureml-core/azureml.data.tabulardataset) táblázatos formátumban jeleníti meg az adatokat a megadott fájl vagy fájlok listájának elemzésével. Ez lehetővé teszi az adat egy Panda vagy Spark DataFrame való megadását, így a jól ismert adatelőkészítési és-betanítási könyvtárakkal dolgozhat anélkül, hogy el kellene hagynia a jegyzetfüzetet. Létrehozhat egy `TabularDataset` objektumot. csv,. TSV,. Parque,. JSON fájlokból és [SQL-lekérdezések eredményeiből](/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory#from-sql-query-query--validate-true--set-column-types-none--query-timeout-30-).

A TabularDatasets megadhat egy időbélyeget az adatok egyik oszlopában, vagy bárhonnan, ahol a rendszer az elérésiút-minta adatait tárolja egy idősorozat-tulajdonság engedélyezéséhez. Ez az előírás lehetővé teszi az egyszerű és hatékony szűrést az idő alapján. Példaként tekintse meg a [táblázatos idősorozattal kapcsolatos API-bemutatót a NOAA időjárási adatainak](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/work-with-data/datasets-tutorial/timeseries-datasets/tabular-timeseries-dataset-filtering.ipynb)használatával.

Hozzon létre egy TabularDataset [a PYTHON SDK](#create-a-tabulardataset) -val vagy a [Azure Machine learning Studióval](how-to-connect-data-ui.md#create-datasets).

>[!NOTE]
> A Azure Machine Learning Studio használatával generált [AUTOMATIZÁLT ml](concept-automated-ml.md) -munkafolyamatok jelenleg csak a TabularDatasets támogatják. 

## <a name="access-datasets-in-a-virtual-network"></a>Adatkészletek elérése egy virtuális hálózaton

Ha a munkaterület virtuális hálózatban található, az érvényesítés kihagyása érdekében konfigurálnia kell az adatkészletet. Az adattárolók és adatkészletek virtuális hálózatban való használatáról további információt a [munkaterület és a kapcsolódó erőforrások biztonságossá](how-to-secure-workspace-vnet.md#secure-datastores-and-datasets)tételét ismertető témakörben talál.

<a name="datasets-sdk"></a>

## <a name="create-datasets-from-datastores"></a>Adatkészletek létrehozása adattárolóból

Ahhoz, hogy az adatok elérhetők legyenek Azure Machine Learning számára, az adatkészleteket [Azure Machine learning](how-to-access-data.md) adattárolók vagy webes URL-címek elérési útjaiból kell létrehozni. 

> [!TIP] 
> Létrehozhat adatkészleteket közvetlenül a Storage URL-címekről az identitás-alapú adateléréssel. További információ: a [Kapcsolódás a Storage szolgáltatáshoz identitás-alapú adathozzáféréssel (előzetes verzió)](how-to-identity-based-data-access.md)<br><br>
Ez a funkció egy [kísérleti](/python/api/overview/azure/ml/#stable-vs-experimental) előzetes verzió, és bármikor megváltozhat. 

 
Adatkészletek létrehozása adattárból a Python SDK-val:

1. Ellenőrizze, hogy rendelkezik `contributor` -e a `owner` regisztrált Azure Machine learning adattárának mögöttes tárolási szolgáltatásával, vagy hogy rendelkezik-e hozzáféréssel. [Győződjön meg arról, hogy a Storage-fiók engedélyei szerepelnek a Azure Portalban](../role-based-access-control/check-access.md).

1. Hozza létre az adatkészletet az adattár elérési útjaira hivatkozó hivatkozásokkal. Több adattárból is létrehozhat adatkészletet több adattárolóban. Az adatkészletek létrehozásához használt fájlok vagy adatméretek száma nem korlátozott. 

> [!NOTE]
> Minden egyes adatelérési útvonal esetében néhány kérelmet küld a rendszer a Storage szolgáltatásnak, hogy meggyőződjön arról, hogy egy fájlra vagy mappára mutat. Ez a terhelés csökkentheti a teljesítményt vagy a hibát. Egy 1000-fájlon belüli, egy mappára hivatkozó adatkészlet egy adatútvonalra hivatkozik. Javasoljuk, hogy az optimális teljesítmény érdekében az adattárolók kevesebb mint 100 útvonalára hivatkozó adatkészletet hozzon létre.

### <a name="create-a-filedataset"></a>FileDataset létrehozása

A [`from_files()`](/python/api/azureml-core/azureml.data.dataset_factory.filedatasetfactory#from-files-path--validate-true-) osztály metódusának használatával `FileDatasetFactory` bármilyen formátumban betöltheti a fájlokat, és nem regisztrált FileDataset hozhat létre. 

Ha a tárterület virtuális hálózat vagy tűzfal mögött van, állítsa a paramétert a `validate=False` `from_files()` metódusban. Ezzel megkerüli a kezdeti ellenőrzési lépést, és gondoskodik arról, hogy az adatkészletet ezekből a biztonságos fájlokból lehessen létrehozni. További információ az adattárolók [és adatkészletek virtuális hálózatban való használatáról](how-to-secure-workspace-vnet.md#secure-datastores-and-datasets).

```Python
# create a FileDataset pointing to files in 'animals' folder and its subfolders recursively
datastore_paths = [(datastore, 'animals')]
animal_ds = Dataset.File.from_files(path=datastore_paths)

# create a FileDataset from image and label files behind public web urls
web_paths = ['https://azureopendatastorage.blob.core.windows.net/mnist/train-images-idx3-ubyte.gz',
             'https://azureopendatastorage.blob.core.windows.net/mnist/train-labels-idx1-ubyte.gz']
mnist_ds = Dataset.File.from_files(path=web_paths)
```
Az adatkészletek újbóli felhasználása és megosztása a munkaterületen a kísérlet során: [regisztrálja az adatkészletet](#register-datasets). 

> [!TIP] 
> Töltse fel a fájlokat egy helyi könyvtárból, és hozzon létre egy FileDataset egyetlen metódusban a nyilvános előnézet metódussal, [upload_directory ()](/python/api/azureml-core/azureml.data.dataset_factory.filedatasetfactory#upload-directory-src-dir--target--pattern-none--overwrite-false--show-progress-true-). Ez a módszer egy [kísérleti](/python/api/overview/azure/ml/#stable-vs-experimental) előzetes funkció, és bármikor megváltozhat. 
> 
>  Ezzel a módszerrel feltöltheti az adatokat a mögöttes tárolóba, így a tárolási költségek is felmerülhetnek. 

### <a name="create-a-tabulardataset"></a>TabularDataset létrehozása

Az osztályban lévő metódus használatával olvassa be a [`from_delimited_files()`](/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory) `TabularDatasetFactory` . csv vagy a. TSV formátumú fájlokat, és hozzon létre egy nem regisztrált TabularDataset. Ha több fájlt olvas be, az eredmények egy táblázatos ábrázolásba lesznek összesítve. 

Ha a tárterület virtuális hálózat vagy tűzfal mögött van, állítsa a paramétert a `validate=False` `from_delimited_files()` metódusban. Ezzel megkerüli a kezdeti ellenőrzési lépést, és gondoskodik arról, hogy az adatkészletet ezekből a biztonságos fájlokból lehessen létrehozni. További információ az adattárolók [és adatkészletek virtuális hálózatban](how-to-secure-workspace-vnet.md#secure-datastores-and-datasets)való használatáról.

A következő kód beolvassa a meglévő munkaterületet és a kívánt adattárat név szerint. Ezután átadja az adattár és a fájl helyét a `path` paraméternek egy új TabularDataset létrehozásához `weather_ds` .

```Python
from azureml.core import Workspace, Datastore, Dataset

datastore_name = 'your datastore name'

# get existing workspace
workspace = Workspace.from_config()
    
# retrieve an existing datastore in the workspace by name
datastore = Datastore.get(workspace, datastore_name)

# create a TabularDataset from 3 file paths in datastore
datastore_paths = [(datastore, 'weather/2018/11.csv'),
                   (datastore, 'weather/2018/12.csv'),
                   (datastore, 'weather/2019/*.csv')]

weather_ds = Dataset.Tabular.from_delimited_files(path=datastore_paths)
```
### <a name="set-data-schema"></a>Adatséma beállítása

Alapértelmezés szerint a TabularDataset létrehozásakor a rendszer automatikusan kikövetkezteti az oszlop adattípusait. Ha a késleltetett típusok nem felelnek meg az elvárásainak, az adatkészlet-sémát az alábbi kóddal rendelkező oszlopok típusának megadásával frissítheti. A paraméter `infer_column_type` csak tagolt fájlokból létrehozott adatkészletek esetében alkalmazható. [További információ a támogatott adattípusokról](/python/api/azureml-core/azureml.data.dataset_factory.datatype).


```Python
from azureml.core import Dataset
from azureml.data.dataset_factory import DataType

# create a TabularDataset from a delimited file behind a public web url and convert column "Survived" to boolean
web_path ='https://dprepdata.blob.core.windows.net/demo/Titanic.csv'
titanic_ds = Dataset.Tabular.from_delimited_files(path=web_path, set_column_types={'Survived': DataType.to_bool()})

# preview the first 3 rows of titanic_ds
titanic_ds.take(3).to_pandas_dataframe()
```

|Index|Utasazonosító|Túlélte|Pclass|Name|szex|Életkor|SibSp|Parch|Ticket|Legnagyobb légitársasága|Kabin|Megkezdte
-|-----------|--------|------|----|---|---|-----|-----|------|----|-----|--------|
0|1|Hamis|3|Braund, Mr. Owen Harris|male|22,0|1|0|A/5 21171|7,2500||S
1|2|Igaz|1|Cumings, Mrs. John Bradley (Florence Briggs th...|female|38,0|1|0|PC 17599|71,2833|C85|C
2|3|Igaz|3|Heikkinen, Miss. Laina 's|female|26,0|0|0|STON/O2. 3101282|7,9250||S

Az adatkészletek a munkaterületen végzett kísérletek közötti újrafelhasználásához és megosztásához [regisztrálja az adatkészletet](#register-datasets).

## <a name="wrangle-data"></a>Vitatkozik-adathalmazok
Az adatkészlet létrehozása és [regisztrálása](#register-datasets) után a modell betanítása előtt betöltheti a notebookba az adathuzavona és a [felderítési](#explore-data) szolgáltatásba. 

Ha nincs szükség semmilyen adat huzavona vagy feltárására, olvassa el a következő témakört: adatkészletek használata a betanítási parancsfájlokban ML kísérletek beküldéséhez a [vonaton adatkészletekkel](how-to-train-with-datasets.md).

### <a name="filter-datasets-preview"></a>Adatkészletek szűrése (előzetes verzió)
A szűrési képességek az adatkészlet típusától függenek. 
> [!IMPORTANT]
> Az adatkészletek nyilvános előzetes módszerrel történő szűrése [`filter()`](/python/api/azureml-core/azureml.data.tabulardataset#filter-expression-) [kísérleti](/python/api/overview/azure/ml/#stable-vs-experimental) előzetes verzióként érhető el, és bármikor megváltozhat. 
> 
A **TabularDatasets** a [keep_columns ()](/python/api/azureml-core/azureml.data.tabulardataset#keep-columns-columns--validate-false-) és a [drop_columns ()](/python/api/azureml-core/azureml.data.tabulardataset#drop-columns-columns-) metódussal rendelkező oszlopokat is megtarthatja vagy eltávolíthatja.

Ha egy TabularDataset egy adott oszlop értéke alapján szeretné kiszűrni a sorokat, használja a [Filter ()](/python/api/azureml-core/azureml.data.tabulardataset#filter-expression-) metódust (előzetes verzió). 

A következő példák egy nem regisztrált adatkészletet adnak vissza a megadott kifejezések alapján.

```python
# TabularDataset that only contains records where the age column value is greater than 15
tabular_dataset = tabular_dataset.filter(tabular_dataset['age'] > 15)

# TabularDataset that contains records where the name column value contains 'Bri' and the age column value is greater than 15
tabular_dataset = tabular_dataset.filter((tabular_dataset['name'].contains('Bri')) & (tabular_dataset['age'] > 15))
```

**A FileDatasets-ben** minden sor egy fájl elérési útjához tartozik, ezért az oszlop érték szerinti szűrés nem hasznos. A sorokat azonban a metaadatok (például a CreationTime, a méret stb. [) alapján szűrheti](/python/api/azureml-core/azureml.data.filedataset#filter-expression-) .

A következő példák egy nem regisztrált adatkészletet adnak vissza a megadott kifejezések alapján.

```python
# FileDataset that only contains files where Size is less than 100000
file_dataset = file_dataset.filter(file_dataset.file_metadata['Size'] < 100000)

# FileDataset that only contains files that were either created prior to Jan 1, 2020 or where 
file_dataset = file_dataset.filter((file_dataset.file_metadata['CreatedTime'] < datetime(2020,1,1)) | (file_dataset.file_metadata['CanSeek'] == False))
```

Az [adatcímkéző projektekben](how-to-create-labeling-projects.md) létrehozott **címkézett adatkészletek** speciális esetek. Ezek az adatkészletek képfájlokból álló TabularDataset-típusok. Az ilyen típusú adatkészletek esetében a metaadatok alapján [szűrheti a ()](/python/api/azureml-core/azureml.data.tabulardataset#filter-expression-) lemezképeket, valamint a (z) és az oszlop értékeit `label` `image_details` .

```python
# Dataset that only contains records where the label column value is dog
labeled_dataset = labeled_dataset.filter(labeled_dataset['label'] == 'dog')

# Dataset that only contains records where the label and isCrowd columns are True and where the file size is larger than 100000
labeled_dataset = labeled_dataset.filter((labeled_dataset['label']['isCrowd'] == True) & (labeled_dataset.file_metadata['Size'] > 100000))
```

## <a name="explore-data"></a>Adatok feltárása

Miután végzett az huzavona, [regisztrálhatja](#register-datasets) az adatkészletet, majd betöltheti a notebookba az adatfeltáráshoz a modell betanítása előtt.

A FileDatasets **csatlakoztathatja** vagy **letöltheti** az adatkészletet, és alkalmazhatja azokat a Python-kódtárakat, amelyeket általában az adatfeltáráshoz használ. [További információ a Mount vs letöltésről](how-to-train-with-datasets.md#mount-vs-download).

```python
# download the dataset 
dataset.download(target_path='.', overwrite=False) 

# mount dataset to the temp directory at `mounted_path`

import tempfile
mounted_path = tempfile.mkdtemp()
mount_context = dataset.mount(mounted_path)

mount_context.start()
```

A TabularDatasets a metódus használatával [`to_pandas_dataframe()`](/python/api/azureml-core/azureml.data.tabulardataset#to-pandas-dataframe-on-error--null---out-of-range-datetime--null--) tekintheti meg az adatait egy dataframe. 

```python
# preview the first 3 rows of titanic_ds
titanic_ds.take(3).to_pandas_dataframe()
```

|Index|Utasazonosító|Túlélte|Pclass|Name|szex|Életkor|SibSp|Parch|Ticket|Legnagyobb légitársasága|Kabin|Megkezdte
-|-----------|--------|------|----|---|---|-----|-----|------|----|-----|--------|
0|1|Hamis|3|Braund, Mr. Owen Harris|male|22,0|1|0|A/5 21171|7,2500||S
1|2|Igaz|1|Cumings, Mrs. John Bradley (Florence Briggs th...|female|38,0|1|0|PC 17599|71,2833|C85|C
2|3|Igaz|3|Heikkinen, Miss. Laina 's|female|26,0|0|0|STON/O2. 3101282|7,9250||S

## <a name="create-a-dataset-from-pandas-dataframe"></a>Adatkészlet létrehozása a pandák dataframe

Ha TabularDataset szeretne létrehozni egy memóriából származó pandák dataframe, írja az adatokat egy helyi fájlba, például egy CSV-fájlba, és hozza létre az adatkészletet a fájlból. A következő kód mutatja be ezt a munkafolyamatot.

```python
# azureml-core of version 1.0.72 or higher is required
# azureml-dataprep[pandas] of version 1.1.34 or higher is required

from azureml.core import Workspace, Dataset
local_path = 'data/prepared.csv'
dataframe.to_csv(local_path)

# upload the local file to a datastore on the cloud

subscription_id = 'xxxxxxxxxxxxxxxxxxxxx'
resource_group = 'xxxxxx'
workspace_name = 'xxxxxxxxxxxxxxxx'

workspace = Workspace(subscription_id, resource_group, workspace_name)

# get the datastore to upload prepared data
datastore = workspace.get_default_datastore()

# upload the local file from src_dir to the target_path in datastore
datastore.upload(src_dir='data', target_path='data')

# create a dataset referencing the cloud location
dataset = Dataset.Tabular.from_delimited_files(path = [(datastore, ('data/prepared.csv'))])
```

> [!TIP]
> TabularDataset létrehozása és regisztrálása a memóriában lévő Spark-vagy pandák-dataframe egyetlen metódussal, nyilvános előzetes módszerekkel [`register_spark_dataframe()`](/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory#methods) és [`register_pandas_dataframe()`](/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory#methods) . Ezek a regisztrálási módszerek [kísérleti](/python/api/overview/azure/ml/#stable-vs-experimental) előzetes funkciók, és bármikor megváltozhatnak. 
> 
>  Ezek a módszerek adatokat töltenek fel a mögöttes tárolóba, és ennek eredményeképpen tárolási költségekkel járnak. 

## <a name="register-datasets"></a>Adatkészletek regisztrálása

A létrehozási folyamat befejezéséhez regisztrálja az adatkészleteket egy munkaterületen. Az [`register()`](/python/api/azureml-core/azureml.data.abstract_dataset.abstractdataset#&preserve-view=trueregister-workspace--name--description-none--tags-none--create-new-version-false-) adatkészletek munkaterülethez való regisztrálásához használja a metódust, hogy megossza őket másokkal, és újra felhasználja őket a munkaterületen lévő kísérletek között:

```Python
titanic_ds = titanic_ds.register(workspace=workspace,
                                 name='titanic_ds',
                                 description='titanic training data')
```

## <a name="create-datasets-using-azure-resource-manager"></a>Adatkészletek létrehozása Azure Resource Manager használatával

Az [https://github.com/Azure/azure-quickstart-templates/tree/master/101-machine-learning-dataset-create-*](https://github.com/Azure/azure-quickstart-templates/tree/master/) adatkészletek létrehozásához több sablon is használható.

További információ a sablonok használatáról: [Azure Resource Manager sablon használata munkaterületek létrehozásához Azure Machine learning számára](how-to-create-workspace-template.md).


## <a name="create-datasets-from-azure-open-datasets"></a>Adatkészletek létrehozása az Azure Open-adatkészletekben

Az [Azure Open-adatkészletek](https://azure.microsoft.com/services/open-datasets/) olyan beszerzett nyilvános adatkészletek, amelyekkel pontosabb modelleket adhat hozzá a gépi tanulási megoldásokhoz. Az adatkészletek olyan nyilvános tartományi adatokat foglalnak magukban, mint az időjárás, a népszámlálás, az ünnepnapok, a közbiztonság és a gépi tanulási modellek betanítását és a prediktív megoldások bővítését segítő hely. A nyílt adatkészletek a felhőben vannak Microsoft Azure és az SDK-ban és a Studióban is szerepelnek.

Megtudhatja, hogyan hozhat létre [Azure Machine learning adatkészleteket az Azure Open-adatkészletekben](../open-datasets/how-to-create-azure-machine-learning-dataset-from-open-dataset.md). 

## <a name="train-with-datasets"></a>Betanítás adathalmazok használatával

Az adatkészleteket a gépi tanulási kísérletekben használhatja a ML-modellek betanításához. [További információ az adatkészletek betanításáról](how-to-train-with-datasets.md).

## <a name="version-datasets"></a>Verziók adatkészletei

Egy új verzió létrehozásával regisztrálhat egy új adatkészletet ugyanazon a néven. Az adatkészlet verziója lehetővé teszi az adathalmazok könyvjelzővel való megjelölését, hogy az adatkészlet egy adott verzióját a kísérletezéshez vagy a későbbi sokszorosításhoz lehessen alkalmazni. További információ az [adatkészletek verzióiról](how-to-version-track-datasets.md).
```Python
# create a TabularDataset from Titanic training data
web_paths = ['https://dprepdata.blob.core.windows.net/demo/Titanic.csv',
             'https://dprepdata.blob.core.windows.net/demo/Titanic2.csv']
titanic_ds = Dataset.Tabular.from_delimited_files(path=web_paths)

# create a new version of titanic_ds
titanic_ds = titanic_ds.register(workspace = workspace,
                                 name = 'titanic_ds',
                                 description = 'new titanic training data',
                                 create_new_version = True)
```

## <a name="next-steps"></a>Következő lépések

* Ismerje meg, [Hogyan lehet betanítani az adatkészleteket](how-to-train-with-datasets.md).
* Az automatizált gépi tanulást a TabularDatasets-mel való [betanításhoz](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-energy-demand/auto-ml-forecasting-energy-demand.ipynb)használhatja.
* További példákat az adatkészlet betanításával kapcsolatban a [minta notebookok](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/work-with-data/)című témakörben talál.
