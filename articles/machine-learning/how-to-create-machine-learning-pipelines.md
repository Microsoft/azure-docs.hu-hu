---
title: ML-folyamatok létrehozása és futtatása
titleSuffix: Azure Machine Learning
description: Hozzon létre és futtasson gépi tanulási folyamatokat a gépi tanulási (ML) fázisokkal együtt összevarró munkafolyamatok létrehozásához és kezeléséhez.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.reviewer: sgilley
ms.author: nilsp
author: NilsPohlmann
ms.date: 03/02/2021
ms.topic: conceptual
ms.custom: how-to, devx-track-python, contperf-fy21q1
ms.openlocfilehash: 188df9564905443b8f975eb743b24885b5d03c32
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102618202"
---
# <a name="create-and-run-machine-learning-pipelines-with-azure-machine-learning-sdk"></a>Gépi tanulási folyamatokat hozhat létre és futtathat Azure Machine Learning SDK-val

Ebből a cikkből megtudhatja, hogyan hozhat létre és futtathat [gépi tanulási folyamatokat](concept-ml-pipelines.md) a [Azure Machine learning SDK](/python/api/overview/azure/ml/intro)használatával. A **ml** -folyamatok használatával létrehozhat egy olyan munkafolyamatot, amely különböző ml fázisokat egyesít. Ezt követően tegye közzé ezt a folyamatot későbbi hozzáféréshez vagy megosztáshoz másokkal. A ML-folyamatok nyomon követésével megtekintheti a modell teljesítményét a valós világban, valamint az adateltolódás észlelését. A ML-folyamatok ideálisak kötegelt pontozási forgatókönyvekhez, különböző számítások használatával, az újbóli Futtatás helyett a lépések végrehajtásával, valamint az ML-munkafolyamatok másokkal való megosztásával.

Ez a cikk nem oktatóanyag. Az első folyamat létrehozásával kapcsolatos útmutatásért tekintse meg a következőt [: oktatóanyag: Azure Machine learning folyamat létrehozása batch-pontozási célra](tutorial-pipeline-batch-scoring-classification.md) , vagy [automatikus ML használata egy Azure Machine learning-folyamaton a Pythonban](how-to-use-automlstep-in-pipelines.md). 

Habár az [Azure-folyamatokat](/azure/devops/pipelines/targets/azure-machine-learning?context=azure%2fmachine-learning%2fservice%2fcontext%2fml-context&tabs=yaml) más típusú folyamatokkal is használhatja, amelyek az ml-feladatok CI/CD-automatizálására szolgálnak, az adott típusú folyamat nem tárolódik a munkaterületen. [Hasonlítsa össze ezeket a különböző folyamatokat](concept-ml-pipelines.md#which-azure-pipeline-technology-should-i-use).

A létrehozott ML-folyamatok a Azure Machine Learning [munkaterület](how-to-manage-workspace.md)tagjai számára láthatók. 

A (z) ML-folyamatok számítási célokon futnak (lásd: [Mik a számítási célok a Azure Machine learning](./concept-compute-target.md)). A folyamatok a támogatott [Azure Storage](../storage/index.yml) -helyekről származó és onnan származó adatok olvasására és írására használhatók.

Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy ingyenes fiókot. Próbálja ki a [Azure Machine learning ingyenes vagy fizetős verzióját](https://aka.ms/AMLFree).

## <a name="prerequisites"></a>Előfeltételek

* Hozzon létre egy [Azure Machine learning munkaterületet](how-to-manage-workspace.md) az összes folyamat erőforrásának tárolásához.

* [Állítsa be a fejlesztési környezetet](how-to-configure-environment.md) a Azure Machine learning SDK telepítéséhez, vagy használjon egy [Azure Machine learning számítási példányt](concept-compute-instance.md) az SDK-val, amely már telepítve van.

Első lépésként csatolja a munkaterületet:

```Python
import azureml.core
from azureml.core import Workspace, Datastore

ws = Workspace.from_config()
```

## <a name="set-up-machine-learning-resources"></a>Gépi tanulási erőforrások beállítása

Hozzon létre egy ML-folyamat futtatásához szükséges erőforrásokat:

* Állítson be egy adattárolót, amely a folyamat lépéseiben szükséges információ elérésére szolgál.

* Konfiguráljon egy `Dataset` objektumot úgy, hogy olyan állandó adatértékre mutasson, amely egy adattárban él vagy elérhető. Konfiguráljon egy `OutputFileDatasetConfig` objektumot a folyamat lépései között átadott ideiglenes adatmennyiséghez. 

* Állítsa be azokat a [számítási célokat](concept-azure-machine-learning-architecture.md#compute-targets) , amelyeken a folyamat lépései futni fognak.

### <a name="set-up-a-datastore"></a>Adattár beállítása

Az adattár tárolja a folyamathoz való hozzáférést. Az egyes munkaterületek alapértelmezett adattárral rendelkeznek. Több adattárolót is regisztrálhat. 

A munkaterület létrehozásakor a [Azure Files](../storage/files/storage-files-introduction.md) és az [Azure Blob Storage](../storage/blobs/storage-blobs-introduction.md) a munkaterülethez van csatolva. Az Azure Blob Storage-hoz való kapcsolódáshoz egy alapértelmezett adattár van regisztrálva. További információ: [a Azure Files, az Azure-blobok és az Azure-lemezek használatának](../storage/common/storage-introduction.md)meghatározása. 

```python
# Default datastore 
def_data_store = ws.get_default_datastore()

# Get the blob storage associated with the workspace
def_blob_store = Datastore(ws, "workspaceblobstore")

# Get file storage associated with the workspace
def_file_store = Datastore(ws, "workspacefilestore")

```

A lépések általában adatokat használnak, és kimeneti adatokat hoznak létre. A lépések olyan adattípusokat hozhatnak létre, mint például a modell, a modellekkel és a függő fájlokkal rendelkező könyvtárak vagy az ideiglenes adatmennyiség. Ezek az adatkészletek a folyamat későbbi szakaszaiban is elérhetők. Ha többet szeretne megtudni a folyamat adataihoz való csatlakoztatásáról, tekintse meg a cikkek [elérését](how-to-access-data.md) és az [adatkészletek regisztrálását](how-to-create-register-datasets.md)ismertető cikket. 

### <a name="configure-data-with-dataset-and-outputfiledatasetconfig-objects"></a>Az és az `Dataset` `OutputFileDatasetConfig` objektumok konfigurálása

Az adatfolyamatok adatátvitelének előnyben részesített módja egy [adatkészlet](/python/api/azureml-core/azureml.core.dataset.Dataset) -objektum. Az `Dataset` objektum olyan adatokra mutat, amelyek vagy egy adattárból vagy egy webes URL-címről érhetők el. Az `Dataset` osztály absztrakt, ezért létre kell hoznia egy `FileDataset` (egy vagy több fájlra hivatkozó) vagy egy olyan példányát, amelyet egy `TabularDataset` vagy több, tagolt oszlopokkal rendelkező fájlból hozott létre.

Hozzon létre egy olyan `Dataset` metódust, mint például a [from_files](/python/api/azureml-core/azureml.data.dataset_factory.filedatasetfactory#from-files-path--validate-true-) vagy a [from_delimited_files](/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory#from-delimited-files-path--validate-true--include-path-false--infer-column-types-true--set-column-types-none--separator------header-true--partition-format-none--support-multi-line-false-).

```python
from azureml.core import Dataset

my_dataset = Dataset.File.from_files([(def_blob_store, 'train-images/')])
```

A közbenső adatokat (vagy egy lépés kimenetét) egy [OutputFileDatasetConfig](/python/api/azureml-pipeline-core/azureml.data.output_dataset_config.outputfiledatasetconfig) objektum jelképezi. `output_data1` egy lépés kimenete. Opcionálisan az adatokat adatkészletként is regisztrálhatja a hívásával `register_on_complete` . Ha `OutputFileDatasetConfig` egy lépésben létrehoz egy lépést, és bemenetként használja egy másik lépéshez, a lépések közötti függőségek egy implicit végrehajtási sorrendet hoznak létre a folyamatban.

`OutputFileDatasetConfig` az objektumok egy könyvtárat adnak vissza, és alapértelmezés szerint a munkaterülethez tartozó alapértelmezett adattárba írja a kimenetet.

```python
from azureml.data import OutputFileDatasetConfig

output_data1 = OutputFileDatasetConfig(destination = (datastore, 'outputdataset/{run-id}'))
output_data_dataset = output_data1.register_on_complete(name = 'prepared_output_data')

```

> [!IMPORTANT]
> `OutputFileDatasetConfig`Az Azure nem törli automatikusan az adattárolást használó köztes adatkészletet.
> Ha programozott módon törölni kívánja a közbenső adatok egy folyamat futásának végén, használjon egy rövid adatmegőrzési szabályzattal rendelkező adattárat, vagy rendszeresen végezzen manuális karbantartást.

> [!TIP]
> Csak az adott feladathoz kapcsolódó fájlokat töltsön fel. A rendszer az adatkönyvtárban található fájlok változásait úgy tekinti meg, hogy a folyamat következő futtatásakor még akkor is futtassa a lépést, ha az újbóli használat meg van adva. 

## <a name="set-up-a-compute-target"></a>Számítási cél beállítása


Azure Machine Learning a __számítási__ (vagy __számítási cél__) kifejezés a gépi tanulási folyamat számítási lépéseit végrehajtó gépekre vagy fürtökre vonatkozik.   A számítási célok teljes listájáért tekintse meg a [modell betanításához szükséges számítási célokat](concept-compute-target.md#train) , és [hozzon létre számítási célokat](how-to-create-attach-compute-studio.md) a munkaterülethez való létrehozásához és csatolásához.   A számítási cél létrehozásához és csatolásához szükséges folyamat ugyanaz, mint a modell betanítása vagy egy folyamat lépésének futtatása. A számítási cél létrehozása és csatolása után használja az objektumot a `ComputeTarget` [folyamat lépéseiben](#steps).

> [!IMPORTANT]
> A számítási célokra irányuló felügyeleti műveletek végrehajtása nem támogatott távoli feladatokból. Mivel a gépi tanulási folyamatokat távoli feladatként küldi el a rendszer, ne használjon a számítási célokra irányuló felügyeleti műveleteket a folyamatból.

### <a name="azure-machine-learning-compute"></a>Azure Machine Learning számítás

A lépések futtatásához létrehozhat egy Azure Machine Learning számítást. A más számítási célok kódja hasonló, kis mértékben eltérő paraméterekkel, a típustól függően. 

```python
from azureml.core.compute import ComputeTarget, AmlCompute

compute_name = "aml-compute"
vm_size = "STANDARD_NC6"
if compute_name in ws.compute_targets:
    compute_target = ws.compute_targets[compute_name]
    if compute_target and type(compute_target) is AmlCompute:
        print('Found compute target: ' + compute_name)
else:
    print('Creating a new compute target...')
    provisioning_config = AmlCompute.provisioning_configuration(vm_size=vm_size,  # STANDARD_NC6 is GPU-enabled
                                                                min_nodes=0,
                                                                max_nodes=4)
    # create the compute target
    compute_target = ComputeTarget.create(
        ws, compute_name, provisioning_config)

    # Can poll for a minimum number of nodes and for a specific timeout.
    # If no min node count is provided it will use the scale settings for the cluster
    compute_target.wait_for_completion(
        show_output=True, min_node_count=None, timeout_in_minutes=20)

    # For a more detailed view of current cluster status, use the 'status' property
    print(compute_target.status.serialize())
```

## <a name="configure-the-training-runs-environment"></a>A betanítási Futtatás környezetének konfigurálása

A következő lépés annak biztosítása, hogy a távoli képzések futtatása a betanítási lépések által igényelt összes függőséget megtegye. A függőségek és a futásidejű környezet egy objektum létrehozásával és konfigurálásával állítható be `RunConfiguration` . 

```python
from azureml.core.runconfig import RunConfiguration
from azureml.core.conda_dependencies import CondaDependencies
from azureml.core import Environment 

aml_run_config = RunConfiguration()
# `compute_target` as defined in "Azure Machine Learning compute" section above
aml_run_config.target = compute_target

USE_CURATED_ENV = True
if USE_CURATED_ENV :
    curated_environment = Environment.get(workspace=ws, name="AzureML-Tutorial")
    aml_run_config.environment = curated_environment
else:
    aml_run_config.environment.python.user_managed_dependencies = False
    
    # Add some packages relied on by data prep step
    aml_run_config.environment.python.conda_dependencies = CondaDependencies.create(
        conda_packages=['pandas','scikit-learn'], 
        pip_packages=['azureml-sdk', 'azureml-dataprep[fuse,pandas]'], 
        pin_sdk_version=False)
```

A fenti kód két lehetőséget mutat a függőségek kezelésére. Ahogy az a `USE_CURATED_ENV = True` esetében is látható, a konfiguráció egy kurátori környezetben alapul. A "besütött környezetek" a közös, egymástól függő könyvtárakkal rendelkeznek, és gyorsabban online állapotba helyezhetők. A kurátori környezetek előre összeállított Docker-rendszerképekkel rendelkeznek a [Microsoft Container Registryban](https://hub.docker.com/publishers/microsoftowner). További információkért tekintse meg a [Azure Machine learning kurátori környezetek](resource-curated-environments.md)című témakört.

Az elérési út, ha úgy módosítja `USE_CURATED_ENV` , hogy `False` a függőségek explicit beállítására szolgáló mintázatot jeleníti meg. Ebben az esetben egy új egyéni Docker-rendszerkép jön létre és lesz regisztrálva az erőforráscsoport egy Azure Container Registryjában (lásd: [Bevezetés az Azure-beli privát Docker-jegyzékbe](../container-registry/container-registry-intro.md)). A rendszerkép kiépítése és regisztrálása néhány percet is igénybe vehet.

## <a name="construct-your-pipeline-steps"></a><a id="steps"></a>A folyamat lépéseinek megépítése

Miután létrehozta a számítási erőforrást és a környezetet, készen áll a folyamat lépéseinek definiálására. A Azure Machine Learning SDK-n keresztül számos beépített lépés érhető el, ahogy a [ `azureml.pipeline.steps` csomag dokumentációjában](/python/api/azureml-pipeline-steps/azureml.pipeline.steps)is látható. A legrugalmasabb osztály a [PythonScriptStep](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.python_script_step.pythonscriptstep), amely egy Python-szkriptet futtat.

```python
from azureml.pipeline.steps import PythonScriptStep
dataprep_source_dir = "./dataprep_src"
entry_point = "prepare.py"
# `my_dataset` as defined above
ds_input = my_dataset.as_named_input('input1')

# `output_data1`, `compute_target`, `aml_run_config` as defined above
data_prep_step = PythonScriptStep(
    script_name=entry_point,
    source_directory=dataprep_source_dir,
    arguments=["--input", ds_input.as_download(), "--output", output_data1],
    compute_target=compute_target,
    runconfig=aml_run_config,
    allow_reuse=True
)
```

A fenti kód egy tipikus kezdeti folyamat lépését jeleníti meg. Az adatelőkészítési kód egy alkönyvtárban található (ebben a példában `"prepare.py"` a címtárban `"./dataprep.src"` ). A folyamat-létrehozási folyamat részeként a rendszer kicsomagolja és feltölti a könyvtárat, `compute_target` és a lépés futtatja a paraméterként megadott parancsfájlt `script_name` .

Az `arguments` értékek határozzák meg a lépés bemeneteit és kimeneteit. A fenti példában az alapértékek az `my_dataset` adatkészlet. A rendszer letölti a megfelelő adatokkal a számítási erőforrást, mivel a kód azt adja meg `as_download()` . A szkript `prepare.py` minden olyan Adatátalakítási feladatot megtesz, amely megfelel a feladatnak, és a következő típusú adatokat adja eredményül: `output_data1` `OutputFileDatasetConfig` . További információkért lásd: [adatok áthelyezése a következőre és a között: ml-folyamat lépései (Python)](how-to-move-data-in-out-of-pipelines.md). A lépés a által definiált gépen fog futni a `compute_target` konfiguráció használatával `aml_run_config` . 

A korábbi eredmények ( `allow_reuse` ) használata kulcsfontosságú, ha a folyamatokat együttműködésen alapuló környezetben használják, mivel a szükségtelen ismétlések kiküszöbölése nem biztosít rugalmasságot. Az újrafelhasználás az alapértelmezett viselkedés, ha a script_name, a bemenetek és a lépés paramétereinek változatlanok maradnak. Ha engedélyezve van az újbóli használat, az előző Futtatás eredményei azonnal elküldhetők a következő lépésre. Ha a `allow_reuse` értéke `False` , akkor a folyamat végrehajtása során a rendszer mindig új futtatást hoz létre ehhez a lépéshez.

Egyetlen lépéssel létrehozhat egy folyamatot, de szinte mindig választhatja a teljes folyamat felosztását több lépésre. Előfordulhat például, hogy az adatelőkészítéssel, a képzéssel, a modell összehasonlításával és az üzembe helyezéssel kapcsolatos lépések szükségesek. Elképzelhető például, hogy a `data_prep_step` fentiek után a következő lépés a képzés:

```python
train_source_dir = "./train_src"
train_entry_point = "train.py"

training_results = OutputFileDatasetConfig(name = "training_results",
    destination = def_blob_store)

    
train_step = PythonScriptStep(
    script_name=train_entry_point,
    source_directory=train_source_dir,
    arguments=["--prepped_data", output_data1.as_input(), "--training_results", training_results],
    compute_target=compute_target,
    runconfig=aml_run_config,
    allow_reuse=True
)
```

A fenti kód hasonló az adatelőkészítési lépés kódjához. A betanítási kód egy olyan címtárban található, amely az adatok előkészítési kódjától eltér. Az `OutputFileDatasetConfig` adat-előkészítési lépés kimenete a `output_data1` betanítási lépés _bemenete_ lesz. Egy új `OutputFileDatasetConfig` objektum `training_results` jön létre, amely egy későbbi összehasonlító vagy központi telepítési lépés eredményét fogja tárolni. 

További Példákért lásd: [kétlépéses ml-folyamat létrehozása](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/work-with-data/datasets-tutorial/pipeline-with-datasets/pipeline-for-image-classification.ipynb) és az [adattárolók visszaírása a Futtatás befejezését követően](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/work-with-data/datasets-tutorial/scriptrun-with-data-input-output/how-to-use-scriptrun.ipynb).

A lépések meghatározása után a folyamatokat a fenti lépések némelyikével vagy mindegyikével kell felépíteni.

> [!NOTE]
> A lépések megadása vagy a folyamat összeállítása során a rendszer nem tölt fel fájlt vagy Azure Machine Learning. A rendszer a [kísérlet. Submit ()](/python/api/azureml-core/azureml.core.experiment.experiment#submit-config--tags-none----kwargs-)hívásakor feltölti a fájlokat.

```python
# list of steps to run (`compare_step` definition not shown)
compare_models = [data_prep_step, train_step, compare_step]

from azureml.pipeline.core import Pipeline

# Build the pipeline
pipeline1 = Pipeline(workspace=ws, steps=[compare_models])
```

### <a name="use-a-dataset"></a>Adatkészlet használata 

Az Azure Blob Storage-ból, Azure Filesból, Azure Data Lake Storage Gen1ból, Azure Data Lake Storage Gen2ból, Azure SQL Databaseból és Azure Database for PostgreSQLból létrehozott adatkészletek a folyamat bármely lépéséhez bemenetként is használhatók. Kimenetet írhat egy [DataTransferStep](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.datatransferstep), [DatabricksStep](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.databricks_step.databricksstep), vagy ha adatokat szeretne írni egy adott adattárhoz, használja a [OutputFileDatasetConfig](/python/api/azureml-pipeline-core/azureml.data.outputfiledatasetconfig). 

> [!IMPORTANT]
> A kimeneti adatokat az adattár használatával való visszaírás `OutputFileDatasetConfig` csak az Azure Blob, az Azure file share, a ADLS Gen 1 és a Gen 2 adattárolók esetében támogatott. 

```python
dataset_consuming_step = PythonScriptStep(
    script_name="iris_train.py",
    inputs=[iris_tabular_dataset.as_named_input("iris_data")],
    compute_target=compute_target,
    source_directory=project_folder
)
```

Ezután lekéri a folyamat adatkészletét a [Run.input_datasets](/python/api/azureml-core/azureml.core.run.run#input-datasets) szótár használatával.

```python
# iris_train.py
from azureml.core import Run, Dataset

run_context = Run.get_context()
iris_dataset = run_context.input_datasets['iris_data']
dataframe = iris_dataset.to_pandas_dataframe()
```

A sort `Run.get_context()` érdemes kiemelni. Ez a függvény `Run` az aktuális kísérleti futtatást jelképező értéket kérdezi le. A fenti példában egy regisztrált adatkészlet beolvasására használjuk. Az objektum egy másik gyakori használata a `Run` kísérlet és a kísérlet alapjául szolgáló munkaterület beolvasása: 

```python
# Within a PythonScriptStep

ws = Run.get_context().experiment.workspace
```

További részletekért, beleértve az adatok továbbításának és elérésének alternatív módjait is, lásd: adatok áthelyezése a következőre és az adatátviteli [folyamat lépései (Python)](how-to-move-data-in-out-of-pipelines.md).

## <a name="caching--reuse"></a>Gyorsítótárazási & újrafelhasználása  

A folyamatok működésének optimalizálásához és testreszabásához hajtson végre néhány dolgot a gyorsítótárazás és az újbóli használat érdekében. Például a következőket teheti:
+ **Állítsa le a kimenet futtatásának alapértelmezett újrafelhasználását** a `allow_reuse=False` [lépés definíciója](/python/api/azureml-pipeline-steps/)szerint. Az újrafelhasználás a kulcs, ha a folyamatokat együttműködésen alapuló környezetben használja, mivel a szükségtelen futtatások kiiktatása rugalmasságot biztosít. Azonban letilthatja az ismételt használatot.
+ **A kimenet újragenerálásának kényszerítése a futtatási folyamat összes lépése esetében**`pipeline_run = exp.submit(pipeline, regenerate_outputs=False)`

Alapértelmezés szerint `allow_reuse` a lépések engedélyezve vannak, és a `source_directory` lépés definíciójában megadott érték kivonatolásra kerül. Tehát ha egy adott lépés parancsfájlja ugyanazokat ( `script_name` , bemeneteket és paramétereket) tartalmaz, és semmi más nem ` source_directory` módosult, az előző lépés futtatásának kimenete újra felhasználható, a feladatot a rendszer nem küldi el a számításba, és az előző Futtatás eredményei azonnal elérhetővé válnak a következő lépéshez.

```python
step = PythonScriptStep(name="Hello World",
                        script_name="hello_world.py",
                        compute_target=aml_compute,
                        source_directory=source_directory,
                        allow_reuse=False,
                        hash_paths=['hello_world.ipynb'])
```

## <a name="submit-the-pipeline"></a>A folyamat elküldése

A folyamat elküldésekor Azure Machine Learning ellenőrzi az egyes lépésekhez tartozó függőségeket, és feltölti a megadott forrás-könyvtár pillanatképét. Ha nincs megadva a forrás könyvtára, a rendszer feltölti az aktuális helyi könyvtárat. A pillanatkép a munkaterületen a kísérlet részeként is tárolódik.

> [!IMPORTANT]
> [!INCLUDE [amlinclude-info](../../includes/machine-learning-amlignore-gitignore.md)]
>
> További információ: [Pillanatképek](concept-azure-machine-learning-architecture.md#snapshots).

```python
from azureml.core import Experiment

# Submit the pipeline to be run
pipeline_run1 = Experiment(ws, 'Compare_Models_Exp').submit(pipeline1)
pipeline_run1.wait_for_completion()
```

A folyamat első futtatásakor Azure Machine Learning:

* Letölti a projekt pillanatképét a számítási célra a munkaterülethez társított blob Storage-ból.
* Létrehoz egy Docker-rendszerképet a folyamat egyes lépéseinek megfelelően.
* Letölti az egyes lépésekhez tartozó Docker-rendszerképet a számítási célra a tároló-beállításjegyzékből.
* A és az objektumok elérését konfigurálja `Dataset` `OutputFileDatasetConfig` . `as_mount()`Hozzáférési mód esetén a rendszer a biztosítékot használja a virtuális hozzáférés biztosításához. Ha a csatlakoztatás nem támogatott, vagy ha a felhasználó a hozzáférési jogosultságot adja `as_upload()` meg, az adatok a számítási célra lesznek másolva.

* Futtatja a lépést a lépés definíciójában megadott számítási célként. 
* Olyan összetevőket hoz létre, mint például a naplók, az stdout és az stderr, a metrikák és a lépés által megadott kimenet. Ezeket az összetevőket a rendszer feltölti és megőrzi a felhasználó alapértelmezett adattárában.

![Kísérlet folyamatként való futtatásának ábrája](./media/how-to-create-your-first-pipeline/run_an_experiment_as_a_pipeline.png)

További információ: [kísérlet osztály](/python/api/azureml-core/azureml.core.experiment.experiment) referenciája.

## <a name="use-pipeline-parameters-for-arguments-that-change-at-inference-time"></a>A viszonyítási időpontnál megjelenő argumentumok esetén használja a folyamat paramétereit

Időnként a folyamaton belüli egyes lépésekhez tartozó argumentumok a fejlesztési és képzési időszakra vonatkoznak: például a betanítási arányok és a lendület, illetve az adatvagy konfigurációs fájlok elérési útjai. A modell telepítésekor érdemes lehet dinamikusan átadni azokat az argumentumokat, amelyekre hivatkozik (azaz azt a lekérdezést, amelyet a modell megválaszolt!). Az ilyen típusú argumentumok folyamatának paramétereit kell megtennie. Ehhez a Pythonban használja a `azureml.pipeline.core.PipelineParameter` osztályt, ahogy az a következő kódrészletben látható:

```python
from azureml.pipeline.core import PipelineParameter

pipeline_param = PipelineParameter(name="pipeline_arg", default_value="default_val")
train_step = PythonScriptStep(script_name="train.py",
                            arguments=["--param1", pipeline_param],
                            target=compute_target,
                            source_directory=project_folder)
```

### <a name="how-python-environments-work-with-pipeline-parameters"></a>A Python-környezetek működése a folyamat paramétereivel

Ahogy korábban már említettük, [a betanítási Futtatás környezete, a](#configure-the-training-runs-environment)környezeti állapot és a Python-függvénytár függőségei egy objektum használatával vannak megadva `Environment` . Általában megadhat egy meglévőt, amely `Environment` hivatkozik a nevére, és opcionálisan egy verziót is:

```python
aml_run_config = RunConfiguration()
aml_run_config.environment.name = 'MyEnvironment'
aml_run_config.environment.version = '1.0'
```

Ha azonban úgy dönt, hogy az `PipelineParameter` objektumok használatával dinamikusan állítja be a változókat a folyamat lépésein, akkor nem használhatja a meglévőre hivatkozó módszert `Environment` . Ehelyett, ha objektumokat szeretne használni `PipelineParameter` , be kell állítania a `environment` mező értékét `RunConfiguration` egy `Environment` objektumra. Az Ön felelőssége annak biztosítása, hogy az ilyen `Environment` függőségek a külső Python-csomagokhoz megfelelően legyenek beállítva.


## <a name="view-results-of-a-pipeline"></a>Folyamat eredményeinek megtekintése

Tekintse meg az összes folyamat listáját és a futtatásuk részleteit a Studióban:

1. Jelentkezzen be [Azure Machine learning studióba](https://ml.azure.com).

1. [Megtekintheti a munkaterületet](how-to-manage-workspace.md#view).

1. A bal oldalon válassza a **folyamatok** lehetőséget az összes folyamat futtatásának megtekintéséhez.
 ![Machine learning-folyamatok listája](./media/how-to-create-your-first-pipeline/pipelines.png)
 
1. Válasszon ki egy adott folyamatot a Futtatás eredményeinek megtekintéséhez.

### <a name="git-tracking-and-integration"></a>Git-követés és-integráció

Ha olyan képzést indít el, ahol a forrás könyvtára helyi git-tárház, a rendszer a tárház adatait a futtatási előzményekben tárolja. További információ: git- [integráció Azure Machine Learninghoz](concept-train-model-git-integration.md).

## <a name="next-steps"></a>Következő lépések

- A folyamat munkatársaival vagy ügyfeleivel való megosztását lásd: [Machine learning-folyamatok közzététele](how-to-deploy-pipelines.md)
- [Ezeket a Jupyter notebookokat a githubon](https://aka.ms/aml-pipeline-readme) használhatja a gépi tanulási folyamatok további megismeréséhez
- Tekintse meg a [azureml-pipeline-Core](/python/api/azureml-pipeline-core/) csomag és a azureml-folyamatok – [STEPs](/python/api/azureml-pipeline-steps/) csomag SDK-referenciáját.
- A folyamatok hibakeresésével és hibaelhárításával kapcsolatos tippekért tekintse [meg a következő](how-to-debug-pipelines.md) témakört:.
- [A szolgáltatás megismerése Jupyter-notebookok használatával](samples-notebooks.md) cikk útmutatását követve megtanulhatja, hogyan futtathat notebookokat.