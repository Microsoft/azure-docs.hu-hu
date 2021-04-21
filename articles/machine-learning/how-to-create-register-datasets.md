---
title: Azure Machine Learning-adathalmazok létrehozása
titleSuffix: Azure Machine Learning
description: Megtudhatja, hogyan hozhat létre Azure Machine Learning adatkészleteket az adatok eléréséhez a gépi tanulási kísérletek futtatásaihoz.
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
ms.openlocfilehash: f47d610a24de2cfc8f1131f61afc8c8173a34376
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107786620"
---
# <a name="create-azure-machine-learning-datasets"></a>Azure Machine Learning-adathalmazok létrehozása

Ebből a cikkből megtudhatja, hogyan hozhat létre Azure Machine Learning adatkészleteket a helyi vagy távoli kísérletek adatainak eléréséhez az Azure Machine Learning Python SDK-val. Annak ért, hogy az adatkészletek Azure Machine Learning adatelérési munkafolyamatában hol férnek el, tekintse meg az Adatok [biztonságos elérése cikket.](concept-data.md#data-workflow)

Az adathalmazok létrehozásával az adatforrások helyére mutató hivatkozásokat és a metaadatok másolatait is létrehozza. Mivel az adatok a meglévő helyen maradnak, nem jár további tárolási költséggel, és nem kockáztatja az adatforrások integritását. Az adatkészletek kiértékelése is lazán történik, ami segít a munkafolyamatok teljesítményének sebességében. Adattárakból, nyilvános URL-címekből és a [Azure Open Datasets.](../open-datasets/how-to-create-azure-machine-learning-dataset-from-open-dataset.md)

Az alacsony kódú felhasználói élmény érdekében hozzon [létre Azure Machine Learning adatkészleteket a Azure Machine Learning stúdió.](how-to-connect-data-ui.md#create-datasets)

A Azure Machine Learning adatkészletekkel a következőre van képes:

* Az adatok egyetlen másolatát őrizzék meg a tárolóban, amelyekre az adatkészletek hivatkoznak.

* Zökkenőmentesen hozzáférhet az adatokhoz a modell betanítása során anélkül, hogy aggódnia kellene a kapcsolati sztringek vagy az adatútvonalak miatt. [További információ az adatkészletekkel való betanításról.](how-to-train-with-datasets.md)

* Adatok megosztása és együttműködés más felhasználókkal.

## <a name="prerequisites"></a>Előfeltételek

Az adatkészletek létrehozásához és a velük való munkához a következőre van szükség:

* Azure-előfizetés. Ha még nincs előfizetése, hozzon létre egy ingyenes fiókot, mielőtt hozzákezd. Próbálja ki [a Azure Machine Learning.](https://aka.ms/AMLFree)

* Egy [Azure Machine Learning munkaterület.](how-to-manage-workspace.md)

* Az [Azure Machine Learning SDK for Python telepítve van,](/python/api/overview/azure/ml/install)amely tartalmazza az azureml-datasets csomagot.

    * Hozzon [létre Azure Machine Learning számítási példányt,](how-to-create-manage-compute-instance.md)amely egy teljes körűen konfigurált és felügyelt fejlesztési környezet, amely integrált jegyzetfüzeteket és a már telepített SDK-t tartalmazza.

    **OR**

    * Dolgozzon a saját Jupyter-notebookon, és telepítse saját maga az SDK-t az [alábbi utasításokkal.](/python/api/overview/azure/ml/install)

> [!NOTE]
> Egyes adatkészletosztályok az [azureml-dataprep](https://pypi.org/project/azureml-dataprep/) csomagtól függnek, amely csak a 64 bites Pythonnal kompatibilis. Linux-felhasználók esetén ezek az osztályok csak a következő disztribúciókban támogatottak: Red Hat Enterprise Linux (7, 8), Ubuntu (14.04, 16.04, 18.04), Fedora (27, 28), Debian (8, 9) és CentOS (7). Ha nem támogatott disztribúciókat használ, a folytatáshoz kövesse ezt az útmutatót a .NET Core 2.1 telepítéséhez. [](/dotnet/core/install/linux) 

## <a name="compute-size-guidance"></a>Útmutató a számítási mérethez

Adatkészlet létrehozásakor tekintse át a számítási feldolgozási teljesítményt és a memóriában az adatok méretét. A tárolóban az adatok mérete nem ugyanaz, mint az adatkeretben. A CSV-fájlokban lévő adatok például akár 10-edikére is bővíthetők egy adatkeretben, így egy 1 GB-os CSV-fájl adatkeretben 10 GB-ra is bővíthető. 

Ha az adatok tömörítve van, tovább bővítheti őket; A tömörített parquet formátumban tárolt, viszonylag ritka 20 GB-os adat a memóriában ~800 GB-ra bővíthető. Mivel a Parquet-fájlok oszlopos formátumban tárolják az adatokat, ha csak az oszlopok felére van szüksége, akkor csak ~400 GB-ot kell betöltenie a memóriába.

[További információ az adatfeldolgozás optimalizálásáról a Azure Machine Learning.](concept-optimize-data-processing.md)

## <a name="dataset-types"></a>Adathalmaz-típusok

Két adatkészlettípus létezik, az alapján, hogy a felhasználók hogyan fogyasztják őket a betanítás során; FileDatasets és TabularDatasets. Mindkét típus használható a Azure Machine Learning munkafolyamatokban, beleértve a becslőket, az AutoML-t, a HyperDrive-ot és a folyamatokét. 

### <a name="filedataset"></a>FileDataset (Fájladatkészlet)

A [FileDataset adatkészlet](/python/api/azureml-core/azureml.data.file_dataset.filedataset) egy vagy több fájlra hivatkozik az adattárban vagy a nyilvános URL-címeken. Ha az adatok már megtisztítva vannak, és készen [](how-to-train-with-datasets.md#mount-vs-download) állnak a betanítás kísérletekben való használatra, letöltheti vagy csatlakoztathatja a fájlokat a számításhoz FileDataset objektumként. 

A FileDatasets használatát javasoljuk a gépi tanulási munkafolyamatokhoz, mivel a forrásfájlok bármilyen formátumban használhatók, ami a gépi tanulási forgatókönyvek szélesebb körét teszi lehetővé, beleértve a mély tanulást is.

Hozzon létre egy FileDataset adatokat a [Python SDK-val](#create-a-filedataset) vagy a [Azure Machine Learning stúdió.](how-to-connect-data-ui.md#create-datasets)
### <a name="tabulardataset"></a>TabularDataset (Táblázatos adatkészlet)

A [TabularDataset táblázatos](/python/api/azureml-core/azureml.data.tabulardataset) formában jelöli az adatokat a megadott fájl vagy fájllista alapján. Ez lehetővé teszi, hogy az adatokat pandas- vagy Spark DataFrame-keretbe materializálja, így a jegyzetfüzet elhagyása nélkül dolgozhat a jól ismert adat-előkészítési és -betanítási kódtárakkal. A `TabularDataset` .csv, .tsv, [.parquet,](/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory#from-parquet-files-path--validate-true--include-path-false--set-column-types-none--partition-format-none-) [.jsonl](/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory#from-json-lines-files-path--validate-true--include-path-false--set-column-types-none--partition-format-none--invalid-lines--error---encoding--utf8--)fájlok és SQL-lekérdezések eredményeiből hozhat létre [objektumot.](/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory#from-sql-query-query--validate-true--set-column-types-none--query-timeout-30-)

A TabularDatasets használatával időbélyeget is megadhat az adatok egyik oszlopából, vagy attól a helytől, ahol az elérésiút-minta adatai tárolva vannak, hogy lehetővé tegye az idősorozat-jellemzőt. Ez a specifikáció lehetővé teszi az idő alapján való egyszerű és hatékony szűrést. Példa: Táblázatos idősorozattal kapcsolatos [API-bemutató NOAA időjárási adatokkal.](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/work-with-data/datasets-tutorial/timeseries-datasets/tabular-timeseries-dataset-filtering.ipynb)

Hozzon létre egy TabularDataset készletet a [Python SDK-val](#create-a-tabulardataset) vagy [Azure Machine Learning stúdió.](how-to-connect-data-ui.md#create-datasets)

>[!NOTE]
> [Az automatizált gépi](concept-automated-ml.md) tanulási munkafolyamatok a Azure Machine Learning stúdió jelenleg csak a TabularDatasets-t támogatják. 

## <a name="access-datasets-in-a-virtual-network"></a>Adatkészletek elérése virtuális hálózatban

Ha a munkaterület egy virtuális hálózaton található, úgy kell konfigurálnia az adatkészletet, hogy kihagyja az ellenőrzést. Az adattárolók és adatkészletek virtuális hálózatokban való használatával kapcsolatos további információkért lásd: Munkaterület és [társított erőforrások biztonságossáése.](how-to-secure-workspace-vnet.md#secure-datastores-and-datasets)

<a name="datasets-sdk"></a>

## <a name="create-datasets-from-datastores"></a>Adattárak létrehozása adattárakból

Ahhoz, hogy az adatok elérhetők legyenek Azure Machine Learning adattárakban vagy webes [](how-to-access-data.md) URL-Azure Machine Learning elérési útjaiból kell létrehozni. 

> [!TIP] 
> Adatkészleteket közvetlenül a tároló URL-címeiből hozhat létre identitásalapú adateléréssel. További információ: [Csatlakozás tárolóhoz identitásalapú adateléréssel (előzetes verzió)](how-to-identity-based-data-access.md)<br><br>
Ez a funkció egy kísérleti [előzetes verziójú](/python/api/overview/azure/ml/#stable-vs-experimental) funkció, és bármikor megváltozhat. 

 
Adatkészletek létrehozása adattárból a Python SDK-val:

1. Ellenőrizze, hogy rendelkezik-e a regisztrált adattár mögöttes tárolási szolgáltatásának Azure Machine Learning `contributor` `owner` hozzáféréssel. [Ellenőrizze a tárfiók engedélyeinek a Azure Portal.](../role-based-access-control/check-access.md)

1. Az adattárban található elérési utakra való hivatkozásokkal hozza létre az adatkészletet. Több elérési útból is létrehozhat adatkészletet több adattárban. Az adatkészletek létrehozásához használt fájlok vagy adatméretek száma nem korlátozva van. 

> [!NOTE]
> Minden adatútvonal esetén néhány kérés lesz elküldve a tárolási szolgáltatásnak, amely ellenőrzi, hogy egy fájlra vagy mappára mutat-e. Ez a többletterhelés csökkent teljesítményt vagy meghibásodást okozhat. Egy 1000 fájlt tartalmazó mappára hivatkozó adatkészlet egy adatútvonalra hivatkozik. Az optimális teljesítmény érdekében javasoljuk, hogy hozzon létre egy adattárban kevesebb mint 100 útvonalra hivatkozó adattárat.

### <a name="create-a-filedataset"></a>FileDataset létrehozása

Használja a osztály metódusát fájlok bármilyen formátumban való betöltéséhez és egy nem regisztrált [`from_files()`](/python/api/azureml-core/azureml.data.dataset_factory.filedatasetfactory#from-files-path--validate-true-) `FileDatasetFactory` FileDataset létrehozásához. 

Ha a tároló virtuális hálózat vagy tűzfal mögött van, állítsa be a paramétert a `validate=False` `from_files()` metódusban. Ez megkerüli a kezdeti ellenőrzési lépést, és biztosítja, hogy létre tudja hozni az adatkészletet ezekből a biztonságos fájlokból. További információ az adattárak [és adatkészletek virtuális hálózatokban való használatával kapcsolatban.](how-to-secure-workspace-vnet.md#secure-datastores-and-datasets)

```Python
# create a FileDataset pointing to files in 'animals' folder and its subfolders recursively
datastore_paths = [(datastore, 'animals')]
animal_ds = Dataset.File.from_files(path=datastore_paths)

# create a FileDataset from image and label files behind public web urls
web_paths = ['https://azureopendatastorage.blob.core.windows.net/mnist/train-images-idx3-ubyte.gz',
             'https://azureopendatastorage.blob.core.windows.net/mnist/train-labels-idx1-ubyte.gz']
mnist_ds = Dataset.File.from_files(path=web_paths)
```
Ha újra fel szeretne használni és megosztani adatkészleteket a munkaterületen lévő kísérletben, [regisztrálja az adatkészletet.](#register-datasets) 

> [!TIP] 
> Töltsön fel fájlokat egy helyi könyvtárból, és hozzon létre egy FileDataset adattárat egyetlen metódusban a [nyilvános előzetes verzió metódusával( upload_directory()](/python/api/azureml-core/azureml.data.dataset_factory.filedatasetfactory#upload-directory-src-dir--target--pattern-none--overwrite-false--show-progress-true-). Ez a módszer egy [kísérleti előzetes](/python/api/overview/azure/ml/#stable-vs-experimental) verziójú funkció, és bármikor megváltozhat. 
> 
>  Ez a módszer feltölti az adatokat a mögöttes tárolóba, ami tárolási költségekkel jár. 

### <a name="create-a-tabulardataset"></a>TabularDataset létrehozása

A osztály metódusával .csv vagy .tsv formátumban olvashatja be a fájlokat, és létrehozhat egy nem regisztrált [`from_delimited_files()`](/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory) `TabularDatasetFactory` TabularDataset adatokat. A fájlok .parquet formátumból való beolvassa a [`from_parquet_files()`](/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory#from-parquet-files-path--validate-true--include-path-false--set-column-types-none--partition-format-none-) metódust. Ha több fájlból olvas, az eredmények egyetlen táblázatos megjelenítésben lesznek összesítve. 

A támogatott fájlformátumokkal, szintaxissal és kialakítási mintákkal kapcsolatos információkért tekintse meg a [TabularDatasetFactory](/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory) referenciadokumentációt. 

Ha a tároló virtuális hálózat vagy tűzfal mögött van, állítsa be a paramétert a `validate=False` `from_delimited_files()` metódusban. Ez megkerüli a kezdeti ellenőrzési lépést, és biztosítja, hogy létre tudja hozni az adatkészletet ezekből a biztonságos fájlokból. További információ az adattárak és [adatkészletek virtuális hálózatokban való használatával kapcsolatban.](how-to-secure-workspace-vnet.md#secure-datastores-and-datasets)

A következő kód név alapján lekérte a meglévő munkaterületet és a kívánt adattárat. Ezután továbbítja az adattár és a fájl helyét a paraméternek egy új `path` TabularDataset létrehozásához. `weather_ds`

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

A TabularDataset létrehozásakor a rendszer alapértelmezés szerint automatikusan kikövetkeztet oszlop adattípusokat. Ha a kikövetkeztett típusok nem határozzák meg az elvárásait, az alábbi kóddal megadhatja az oszloptípusokat, így frissítheti az adatkészlet sémáját. A paraméter csak tagolt fájlokból létrehozott adatkészletek `infer_column_type` esetén alkalmazható. [További információ a támogatott adattípusokkal kapcsolatban:](/python/api/azureml-core/azureml.data.dataset_factory.datatype).


```Python
from azureml.core import Dataset
from azureml.data.dataset_factory import DataType

# create a TabularDataset from a delimited file behind a public web url and convert column "Survived" to boolean
web_path ='https://dprepdata.blob.core.windows.net/demo/Titanic.csv'
titanic_ds = Dataset.Tabular.from_delimited_files(path=web_path, set_column_types={'Survived': DataType.to_bool()})

# preview the first 3 rows of titanic_ds
titanic_ds.take(3).to_pandas_dataframe()
```

|(Index)|Utasazonosító|Túlélte|Pclass (Titkos besorolás)|Name|szex|Életkor|SibSp|Parch (Perch)|Ticket|Viteldíj|Kabin|Belekezdett
-|-----------|--------|------|----|---|---|-----|-----|------|----|-----|--------|
0|1|Hamis|3|Braund, Mr. Owen Harris|male|22,0|1|0|A/5 21171|7.2500||S
1|2|Igaz|1|Cumings, Mrs. John Th.|female|38.0|1|0|17599-es pc|71.2833|C85|C
2|3|Igaz|3|Heikkinen, Miss. Laina|female|26,0|0|0|STON/O2. 3101282|7.9250||S

Ha a munkaterületen lévő kísérletek között szeretne újra felhasználni és megosztani adatkészleteket, regisztrálja [az adatkészletet.](#register-datasets)

## <a name="wrangle-data"></a>Adatok rendbe rendezése
Az adatkészlet [létrehozása](#register-datasets) és regisztrálása után betöltheti azt a jegyzetfüzetbe [](#explore-data) adatrendezéshez és adatfeltáráshoz a modell betanítása előtt. 

Ha nincs szüksége adatfeltárásra vagy adatfeltárásra, tekintse meg a Betanítás adatkészletekkel oktatószkletek adatkészleteinek a betanítási szkriptekbe való beküldéséhez való használatával kapcsolatos részt. [](how-to-train-with-datasets.md)

### <a name="filter-datasets-preview"></a>Adatkészletek szűrése (előzetes verzió)

A szűrési képességek az adatkészlet típusától függnek. 
> [!IMPORTANT]
> Az adatkészletek előzetes verziós módszerrel való szűrése kísérleti előzetes [`filter()`](/python/api/azureml-core/azureml.data.tabulardataset#filter-expression-) verziójú funkció, és bármikor megváltozhat. [](/python/api/overview/azure/ml/#stable-vs-experimental) 
> 
**A TabularDatasets esetében** a [keep_columns()](/python/api/azureml-core/azureml.data.tabulardataset#keep-columns-columns--validate-false-) és a [drop_columns()](/python/api/azureml-core/azureml.data.tabulardataset#drop-columns-columns-) metódussal tarthatja meg vagy távolíthatja el az oszlopokat.

Ha egy TabularDataset adott oszlopértéke alapján szűri a sorokat, használja a [filter()](/python/api/azureml-core/azureml.data.tabulardataset#filter-expression-) metódust (előzetes verzió). 

Az alábbi példák egy nem regisztrált adatkészletet adnak vissza a megadott kifejezések alapján.

```python
# TabularDataset that only contains records where the age column value is greater than 15
tabular_dataset = tabular_dataset.filter(tabular_dataset['age'] > 15)

# TabularDataset that contains records where the name column value contains 'Bri' and the age column value is greater than 15
tabular_dataset = tabular_dataset.filter((tabular_dataset['name'].contains('Bri')) & (tabular_dataset['age'] > 15))
```

**A FileDatasets (Fájladatkészletek)** esetében minden sor egy fájl elérési útjának felel meg, ezért az oszlopértékek szűrése nem hasznos. A sorokat azonban [szűrheti()](/python/api/azureml-core/azureml.data.filedataset#filter-expression-) olyan metaadatok alapján, mint például a CreationTime, a Size stb.

Az alábbi példák egy nem regisztrált adatkészletet adnak vissza a megadott kifejezések alapján.

```python
# FileDataset that only contains files where Size is less than 100000
file_dataset = file_dataset.filter(file_dataset.file_metadata['Size'] < 100000)

# FileDataset that only contains files that were either created prior to Jan 1, 2020 or where 
file_dataset = file_dataset.filter((file_dataset.file_metadata['CreatedTime'] < datetime(2020,1,1)) | (file_dataset.file_metadata['CanSeek'] == False))
```

**Az adatcímkéző** projektekből [](how-to-create-labeling-projects.md) létrehozott címkével jelölt adatkészletek speciális esetnek nak nak. Ezek az adatkészletek a képfájlokból készült TabularDataset típusúak. Az ilyen típusú adatkészletek esetében [](/python/api/azureml-core/azureml.data.tabulardataset#filter-expression-) a képeket metaadatok, valamint olyan oszlopértékek alapján szűrheti,mint a `label` és a `image_details` .

```python
# Dataset that only contains records where the label column value is dog
labeled_dataset = labeled_dataset.filter(labeled_dataset['label'] == 'dog')

# Dataset that only contains records where the label and isCrowd columns are True and where the file size is larger than 100000
labeled_dataset = labeled_dataset.filter((labeled_dataset['label']['isCrowd'] == True) & (labeled_dataset.file_metadata['Size'] > 100000))
```

### <a name="partition-data-preview"></a>Adatok particionálása (előzetes verzió)

Az adatkészlet particionálható úgy, hogy a paramétert adja meg `partitions_format` a TabularDataset vagy FileDataset létrehozásakor. 

> [!IMPORTANT]
> Az adatkészlet-partíciók [](/python/api/overview/azure/ml/#stable-vs-experimental) létrehozása kísérleti előzetes verziójú funkció, és bármikor megváltozhat. 

Egy adatkészlet particionálása esetén az egyes fájlútvonalak partícióinformációi a megadott formátum alapján oszlopokba vannak kibontva. A formátumnak az első partíciókulcs pozíciójától a fájl elérési útjának végéig kell kezdődni. 

Ha például a partíció elérési útja a részleg neve és ideje szerint van megadva, a létrehoz egy "Részleg" sztringoszlopot "Accounts" értékkel, és egy `../Accounts/2019/01/01/data.jsonl` `partition_format='/{Department}/{PartitionDate:yyyy/MM/dd}/data.jsonl'` "PartitionDate" datetime oszlopot `2019-01-01` értékkel.

Ha az adatok már meglévő partíciókat tartalmaznak, és meg szeretné őrizni ezt a formátumot, a FileDataset létrehozásához foglalja bele `partitioned_format` [`from_files()`](/python/api/azureml-core/azureml.data.dataset_factory.filedatasetfactory#from-files-path--validate-true--partition-format-none-) a paramétert a metódusba. 

A meglévő partíciókat megőrzött TabularDataset létrehozásához adja meg a paramétert a `partitioned_format` [from_parquet_files()](/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory#from-parquet-files-path--validate-true--include-path-false--set-column-types-none--partition-format-none-) vagy a [from_delimited_files() metódusban.](/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory#from-delimited-files-path--validate-true--include-path-false--infer-column-types-true--set-column-types-none--separator------header-true--partition-format-none--support-multi-line-false--empty-as-string-false--encoding--utf8--)

A következő példa:
* Létrehoz egy FileDataset adatkészletet particionált fájlokból.
* Lekérte a partíciókulcsokat
* Új, indexelt FileDataset létrehozása a használatával
 
```Python

file_dataset = Dataset.File.from_files(data_paths, partition_format = '{userid}/*.wav')
ds.register(name='speech_dataset')

# access partition_keys
indexes = file_dataset.partition_keys # ['userid']

# get all partition key value pairs should return [{'userid': 'user1'}, {'userid': 'user2'}]
partitions = file_dataset.get_partition_key_values()


partitions = file_dataset.get_partition_key_values(['userid'])
# return [{'userid': 'user1'}, {'userid': 'user2'}]

# filter API, this will only download data from user1/ folder
new_file_dataset = file_dataset.filter(ds['userid'] == 'user1').download()
```

A tabularDatasets adattitkok új partíciószerkezetét is létrehozhatja a [partitions_by() metódussal.](/python/api/azureml-core/azureml.data.tabulardataset#partition-by-partition-keys--target--name-none--show-progress-true--partition-as-file-dataset-false-)

```Python

 dataset = Dataset.get_by_name('test') # indexed by country, state, partition_date

# call partition_by locally
new_dataset = ds.partition_by(name="repartitioned_ds", partition_keys=['country'], target=DataPath(datastore, "repartition"))
partition_keys = new_dataset.partition_keys # ['country']
```

>[!IMPORTANT]
> A TabularDataset partíciók a Azure Machine Learning is alkalmazhatók a ParallelRunStep bemeneteként számos modellalkalmazásban. Tekintsen meg egy példát a [Többmodelles gyorsító dokumentációjában.](https://github.com/microsoft/solution-accelerator-many-models/blob/master/01_Data_Preparation.ipynb)

## <a name="explore-data"></a>Adatok feltárása

Miután végzett az adatok rendszerezésével, [](#register-datasets) regisztrálhatja az adatkészletet, majd betöltheti őket a jegyzetfüzetbe adatfeltárásra a modell betanítása előtt.

A FileDatasets adatkészletek  esetében  csatlakoztathatja vagy letöltheti az adatkészletet, és alkalmazhatja az adatfeltáráshoz általában használt Python-kódtárakat. [További információ a csatlakoztatásról és a letöltésről.](how-to-train-with-datasets.md#mount-vs-download)

```python
# download the dataset 
dataset.download(target_path='.', overwrite=False) 

# mount dataset to the temp directory at `mounted_path`

import tempfile
mounted_path = tempfile.mkdtemp()
mount_context = dataset.mount(mounted_path)

mount_context.start()
```

A TabularDatasets esetében használja a metódust az [`to_pandas_dataframe()`](/python/api/azureml-core/azureml.data.tabulardataset#to-pandas-dataframe-on-error--null---out-of-range-datetime--null--) adatok adatkeretben való megtekintéséhez. 

```python
# preview the first 3 rows of titanic_ds
titanic_ds.take(3).to_pandas_dataframe()
```

|(Index)|Utasazonosító|Túlélte|Pclass (Titkos besorolás)|Name|szex|Életkor|SibSp|Persely|Ticket|Viteldíj|Kabin|Belekezdett
-|-----------|--------|------|----|---|---|-----|-----|------|----|-----|--------|
0|1|Hamis|3|Braund, Mr. Owen Harris|male|22,0|1|0|A/5 21171|7.2500||S
1|2|Igaz|1|Cumings, Mrs. John Th.|female|38.0|1|0|17599-es számítógép|71.2833|C85|C
2|3|Igaz|3|Heikkinen, Miss. Laina|female|26,0|0|0|STON/O2. 3101282|7.9250||S

## <a name="create-a-dataset-from-pandas-dataframe"></a>Adatkészlet létrehozása pandas-adatkeretből

Ha tabularDatasetet szeretne létrehozni egy memóriabeli pandas-adatkeretből, írja az adatokat egy helyi fájlba, például egy CSV-fájlba, és hozza létre az adatkészletet ebből a fájlból. Az alábbi kód ezt a munkafolyamatot mutatja be.

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
> Hozzon létre és regisztráljon egy TabularDataset adatkészletet egy memória-sparkban vagy pandas-adatkeretben egyetlen metódussal nyilvános előzetes verziójú metódusokkal, [`register_spark_dataframe()`](/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory#methods) és [`register_pandas_dataframe()`](/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory#methods) . Ezek a regisztrálási módszerek [kísérleti előzetes](/python/api/overview/azure/ml/#stable-vs-experimental) verziójú funkciók, és bármikor megváltozhatnak. 
> 
>  Ezek a metódusok feltöltik az adatokat a mögöttes tárolóba, ami tárolási költségekkel jár. 

## <a name="register-datasets"></a>Adatkészletek regisztrálása

A létrehozási folyamat befejezéséhez regisztrálja az adatkészleteket egy munkaterületen. Az metódussal regisztrálhat adatkészleteket a munkaterületen, így megoszthatja őket másokkal, és újra felhasználhatja őket a munkaterületen lévő [`register()`](/python/api/azureml-core/azureml.data.abstract_dataset.abstractdataset#&preserve-view=trueregister-workspace--name--description-none--tags-none--create-new-version-false-) kísérletekben:

```Python
titanic_ds = titanic_ds.register(workspace=workspace,
                                 name='titanic_ds',
                                 description='titanic training data')
```

## <a name="create-datasets-using-azure-resource-manager"></a>Adatkészletek létrehozása Azure Resource Manager

A számos sablonja [https://github.com/Azure/azure-quickstart-templates/tree/master/101-machine-learning-dataset-create-*](https://github.com/Azure/azure-quickstart-templates/tree/master/) használható adatkészletek létrehozásához.

További információ a sablonok használatával kapcsolatban: Munkaterület létrehozása Azure Resource Manager sablon használatával [a Azure Machine Learning.](how-to-create-workspace-template.md)


## <a name="create-datasets-from-azure-open-datasets"></a>Adatkészletek létrehozása Azure Open Datasets

[Azure Open Datasets](https://azure.microsoft.com/services/open-datasets/) olyan össze összecsukott nyilvános adatkészletek, amelyek segítségével forgatókönyv-specifikus funkciókat adhat a gépi tanulási megoldásokhoz a pontosabb modellek érdekében. Az adatkészletek közé tartoznak az időjárással, nép népekkel, ünnepekkel, közbiztonsággal és helymeghatározással kapcsolatos nyilvános adatok, amelyek segítenek a gépi tanulási modellek betanításában és a prediktív megoldások gazdagításában. Open Datasets a felhőben vannak Microsoft Azure, és az SDK-ban és a Studióban is megtalálhatóak.

Ismerje meg, hogyan hozhat [létre Azure Machine Learning adatkészleteket a Azure Open Datasets.](../open-datasets/how-to-create-azure-machine-learning-dataset-from-open-dataset.md) 

## <a name="train-with-datasets"></a>Betanítás adathalmazok használatával

Az adatkészleteket gépi tanulási kísérletekben használhatja gépi tanulási modellek betanítása érdekében. [További információ az adatkészletekkel való betanításról.](how-to-train-with-datasets.md)

## <a name="version-datasets"></a>Verzióadatkészletek

Új verzió létrehozásával regisztrálhat egy új adatkészletet ugyanazon a néven. Az adatkészlet-verziók az adatok állapotának könyvjelzőzését jelentik, így az adatkészlet egy adott verzióját alkalmazhatja kísérletezésre vagy későbbi többszörözésre. További információ [az adatkészletek verzióiról.](how-to-version-track-datasets.md)
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

* Ismerje [meg, hogyan betanítható adatkészletekkel.](how-to-train-with-datasets.md)
* Automatizált gépi tanulás használata [a TabularDatasets használatával történő betanításhoz.](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-energy-demand/auto-ml-forecasting-energy-demand.ipynb)
* Az adatkészletek betanításával kapcsolatos további példákért tekintse meg a [mintajegyzetfüzeteket.](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/work-with-data/)
