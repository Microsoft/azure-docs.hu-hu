---
title: Machine Learning folyamat YAML
titleSuffix: Azure Machine Learning
description: Megtudhatja, hogyan határozhat meg gépi tanulási folyamatot egy YAML-fájl használatával. A YAML-folyamat definíciói az Azure CLI Machine learning bővítménnyel együtt használhatók.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
ms.reviewer: larryfr
ms.author: nilsp
author: NilsPohlmann
ms.date: 07/31/2020
ms.custom: devx-track-python
ms.openlocfilehash: 2a92fa8fd242482585ab3785e99f8239548ce369
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/23/2021
ms.locfileid: "104868341"
---
# <a name="define-machine-learning-pipelines-in-yaml"></a>Gépi tanulási folyamatok definiálása a YAML-ben

Ismerje meg, hogyan határozhatja meg a gépi tanulási folyamatokat a [YAML](https://yaml.org/)-ben. Ha az Azure CLI Machine learning-bővítményét használja, a folyamattal kapcsolatos számos parancs egy YAML-fájlt vár, amely meghatározza a folyamatot.

A következő táblázat felsorolja, hogy mi az, és jelenleg nem támogatott a folyamat definiálásakor a YAML-ben:

| Lépés típusa | Támogatott? |
| ----- | :-----: |
| PythonScriptStep | Igen |
| ParallelRunStep | Igen |
| AdlaStep | Igen |
| AzureBatchStep | Igen |
| DatabricksStep | Igen |
| DataTransferStep | Igen |
| AutoMLStep | Nem |
| HyperDriveStep | Nem |
| ModuleStep | Igen |
| MPIStep | Nem |
| EstimatorStep | Nem |

## <a name="pipeline-definition"></a>Folyamat definíciója

A folyamat definíciója a következő kulcsokat használja, amelyek megfelelnek a [folyamatok](/python/api/azureml-pipeline-core/azureml.pipeline.core.pipeline.pipeline) osztálynak:

| YAML kulcs | Leírás |
| ----- | ----- |
| `name` | A folyamat leírása. |
| `parameters` | Paraméter (ek) a folyamathoz. |
| `data_reference` | Azt határozza meg, hogyan és hol kell elérhetővé tenni az adathalmazt a futtatásban. |
| `default_compute` | Alapértelmezett számítási cél, amely a folyamat összes lépését futtatja. |
| `steps` | A folyamatban használt lépések. |

## <a name="parameters"></a>Paraméterek

A `parameters` szakasz a következő kulcsokat használja, amelyek megfelelnek a [PipelineParameter](/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelineparameter) osztálynak:

| YAML kulcs | Leírás |
| ---- | ---- |
| `type` | A paraméter értéktípus. Az érvényes típusok:,,, `string` `int` `float` `bool` vagy `datapath` . |
| `default` | Az alapértelmezett érték. |

Minden paraméter neve. Például a következő YAML-kódrészlet három paramétert definiál `NumIterationsParameter` :, `DataPathParameter` és `NodeCountParameter` :

```yaml
pipeline:
    name: SamplePipelineFromYaml
    parameters:
        NumIterationsParameter:
            type: int
            default: 40
        DataPathParameter:
            type: datapath
            default:
                datastore: workspaceblobstore
                path_on_datastore: sample2.txt
        NodeCountParameter:
            type: int
            default: 4
```

## <a name="data-reference"></a>Adatreferencia

A `data_references` szakasz a következő kulcsokat használja, amelyek megfelelnek a [DataReference](/python/api/azureml-core/azureml.data.data_reference.datareference):

| YAML kulcs | Leírás |
| ----- | ----- |
| `datastore` | Az adattár, amelyre hivatkozni kell. |
| `path_on_datastore` | Az adathivatkozáshoz tartozó tárolt tároló relatív elérési útja. |

Az egyes adathivatkozások egy kulcsban találhatók. Például a következő YAML-kódrészlet a nevű kulcsban tárolt adathivatkozást definiálja `employee_data` :

```yaml
pipeline:
    name: SamplePipelineFromYaml
    parameters:
        PipelineParam1:
            type: int
            default: 3
    data_references:
        employee_data:
            datastore: adftestadla
            path_on_datastore: "adla_sample/sample_input.csv"
```

## <a name="steps"></a>Lépések

A lépések meghatározzák a számítási környezetet, valamint a környezetben futtatandó fájlokat. A lépés típusának meghatározásához használja a következő `type` kulcsot:

| Lépés típusa | Leírás |
| ----- | ----- |
| `AdlaStep` | Egy U-SQL-szkriptet futtat Azure Data Lake Analytics. Megfelel a [AdlaStep](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.adlastep) osztálynak. |
| `AzureBatchStep` | A feladatokat Azure Batch használatával futtatja. Megfelel a [AzureBatchStep](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.azurebatchstep) osztálynak. |
| `DatabricsStep` | Databricks-jegyzetfüzet, Python-parancsfájl vagy JAR hozzáadására szolgál. Megfelel a [DatabricksStep](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.databricksstep) osztálynak. |
| `DataTransferStep` | Adatátvitel a tárolási lehetőségek között. Megfelel a [DataTransferStep](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.datatransferstep) osztálynak. |
| `PythonScriptStep` | Egy Python-szkriptet futtat. Megfelel a [PythonScriptStep](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.python_script_step.pythonscriptstep) osztálynak. |
| `ParallelRunStep` | Egy Python-szkriptet futtat nagy mennyiségű, aszinkron és párhuzamos feldolgozásra. Megfelel a [ParallelRunStep](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.parallel_run_step.parallelrunstep) osztálynak. |

### <a name="adla-step"></a>ADLA lépés

| YAML kulcs | Leírás |
| ----- | ----- |
| `script_name` | Az U-SQL-parancsfájl neve (a értékhez viszonyítva `source_directory` ). |
| `compute` | Az ehhez a lépéshez használt Azure Data Lake számítási cél. |
| `parameters` | A folyamathoz tartozó [Paraméterek](#parameters) . |
| `inputs` | A bemenetek lehetnek [InputPortBinding](/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.inputportbinding), [DataReference](#data-reference), [PortDataReference](/python/api/azureml-pipeline-core/azureml.pipeline.core.portdatareference), [PipelineData](/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata), [adatkészlet](/python/api/azureml-core/azureml.core.dataset%28class%29), [DatasetDefinition](/python/api/azureml-core/azureml.data.dataset_definition.datasetdefinition)vagy [PipelineDataset](/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedataset). |
| `outputs` | A kimenetek lehetnek [PipelineData](/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata) vagy [OutputPortBinding](/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.outputportbinding). |
| `source_directory` | A parancsfájlt, szerelvényeket stb. tartalmazó könyvtár |
| `priority` | Az aktuális feladathoz használandó prioritási érték. |
| `params` | Név-érték párok szótára. |
| `degree_of_parallelism` | A feladatokhoz használandó párhuzamosság foka. |
| `runtime_version` | A Data Lake Analytics motor futtatókörnyezet-verziója. |
| `allow_reuse` | Meghatározza, hogy a lépés újra fel kell-e használni az előző eredményeket, ha ugyanazokkal a beállításokkal futnak újra. |

A következő példa egy ADLA lépés definícióját tartalmazza:

```yaml
pipeline:
    name: SamplePipelineFromYaml
    parameters:
        PipelineParam1:
            type: int
            default: 3
    data_references:
        employee_data:
            datastore: adftestadla
            path_on_datastore: "adla_sample/sample_input.csv"
    default_compute: adlacomp
    steps:
        Step1:
            runconfig: "D:\\Yaml\\default_runconfig.yml"
            parameters:
                NUM_ITERATIONS_2:
                    source: PipelineParam1
                NUM_ITERATIONS_1: 7
            type: "AdlaStep"
            name: "MyAdlaStep"
            script_name: "sample_script.usql"
            source_directory: "D:\\scripts\\Adla"
            inputs:
                employee_data:
                    source: employee_data
            outputs:
                OutputData:
                    destination: Output4
                    datastore: adftestadla
                    bind_mode: mount
```

### <a name="azure-batch-step"></a>Azure Batch lépés

| YAML kulcs | Leírás |
| ----- | ----- |
| `compute` | Az ehhez a lépéshez használt Azure Batch számítási cél. |
| `inputs` | A bemenetek lehetnek [InputPortBinding](/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.inputportbinding), [DataReference](#data-reference), [PortDataReference](/python/api/azureml-pipeline-core/azureml.pipeline.core.portdatareference), [PipelineData](/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata), [adatkészlet](/python/api/azureml-core/azureml.core.dataset%28class%29), [DatasetDefinition](/python/api/azureml-core/azureml.data.dataset_definition.datasetdefinition)vagy [PipelineDataset](/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedataset). |
| `outputs` | A kimenetek lehetnek [PipelineData](/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata) vagy [OutputPortBinding](/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.outputportbinding). |
| `source_directory` | A modul bináris fájljait, végrehajtható fájlját és szerelvényeit tartalmazó könyvtár. |
| `executable` | Annak a parancsnak/végrehajtható fájlnak a neve, amelyet a rendszer a feladatok részeként futtat. |
| `create_pool` | Logikai jelző, amely azt jelzi, hogy a rendszer létrehozza-e a készletet a feladatok futtatása előtt. |
| `delete_batch_job_after_finish` | Logikai jelző, amely azt jelzi, hogy a rendszer törli-e a feladatot a Batch-fiókból a befejezés után. |
| `delete_batch_pool_after_finish` | Logikai jelző, amely jelzi, hogy a művelet befejezése után törölni kell-e a készletet. |
| `is_positive_exit_code_failure` | Logikai jelző, amely azt jelzi, hogy a feladat meghiúsul-e, ha a feladat pozitív kóddal kilép. |
| `vm_image_urn` | Ha `create_pool` a `True` és a virtuális gép használja `VirtualMachineConfiguration` . |
| `pool_id` | Annak a készletnek az azonosítója, amelyben a feladatot futtatni fogja. |
| `allow_reuse` | Meghatározza, hogy a lépés újra fel kell-e használni az előző eredményeket, ha ugyanazokkal a beállításokkal futnak újra. |

Az alábbi példa egy Azure Batch lépés definícióját tartalmazza:

```yaml
pipeline:
    name: SamplePipelineFromYaml
    parameters:
        PipelineParam1:
            type: int
            default: 3
    data_references:
        input:
            datastore: workspaceblobstore
            path_on_datastore: "input.txt"
    default_compute: testbatch
    steps:
        Step1:
            runconfig: "D:\\Yaml\\default_runconfig.yml"
            parameters:
                NUM_ITERATIONS_2:
                    source: PipelineParam1
                NUM_ITERATIONS_1: 7
            type: "AzureBatchStep"
            name: "MyAzureBatchStep"
            pool_id: "MyPoolName"
            create_pool: true
            executable: "azurebatch.cmd"
            source_directory: "D:\\scripts\\AureBatch"
            allow_reuse: false
            inputs:
                input:
                    source: input
            outputs:
                output:
                    destination: output
                    datastore: workspaceblobstore
```

### <a name="databricks-step"></a>Databricks lépés

| YAML kulcs | Leírás |
| ----- | ----- |
| `compute` | Az ehhez a lépéshez használt Azure Databricks számítási cél. |
| `inputs` | A bemenetek lehetnek [InputPortBinding](/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.inputportbinding), [DataReference](#data-reference), [PortDataReference](/python/api/azureml-pipeline-core/azureml.pipeline.core.portdatareference), [PipelineData](/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata), [adatkészlet](/python/api/azureml-core/azureml.core.dataset%28class%29), [DatasetDefinition](/python/api/azureml-core/azureml.data.dataset_definition.datasetdefinition)vagy [PipelineDataset](/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedataset). |
| `outputs` | A kimenetek lehetnek [PipelineData](/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata) vagy [OutputPortBinding](/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.outputportbinding). |
| `run_name` | A Databricks tartozó név ehhez a futtatáshoz. |
| `source_directory` | A parancsfájlt és más fájlokat tartalmazó könyvtár. |
| `num_workers` | A Databricks-fürtön futó feldolgozók statikus száma. |
| `runconfig` | Egy fájl elérési útja `.runconfig` . Ez a fájl a [RunConfiguration](/python/api/azureml-core/azureml.core.runconfiguration) osztály YAML ábrázolása. További információ a fájl struktúrájáról: [runconfigschema.js](https://github.com/microsoft/MLOps/blob/b4bdcf8c369d188e83f40be8b748b49821f71cf2/infra-as-code/runconfigschema.json). |
| `allow_reuse` | Meghatározza, hogy a lépés újra fel kell-e használni az előző eredményeket, ha ugyanazokkal a beállításokkal futnak újra. |

A következő példa egy Databricks lépést tartalmaz:

```yaml
pipeline:
    name: SamplePipelineFromYaml
    parameters:
        PipelineParam1:
            type: int
            default: 3
    data_references:
        adls_test_data:
            datastore: adftestadla
            path_on_datastore: "testdata"
        blob_test_data:
            datastore: workspaceblobstore
            path_on_datastore: "dbtest"
    default_compute: mydatabricks
    steps:
        Step1:
            runconfig: "D:\\Yaml\\default_runconfig.yml"
            parameters:
                NUM_ITERATIONS_2:
                    source: PipelineParam1
                NUM_ITERATIONS_1: 7
            type: "DatabricksStep"
            name: "MyDatabrickStep"
            run_name: "DatabricksRun"
            python_script_name: "train-db-local.py"
            source_directory: "D:\\scripts\\Databricks"
            num_workers: 1
            allow_reuse: true
            inputs:
                blob_test_data:
                    source: blob_test_data
            outputs:
                OutputData:
                    destination: Output4
                    datastore: workspaceblobstore
                    bind_mode: mount
```

### <a name="data-transfer-step"></a>Adatátviteli lépés

| YAML kulcs | Leírás |
| ----- | ----- |
| `compute` | Az ehhez a lépéshez használt Azure Data Factory számítási cél. |
| `source_data_reference` | Bemeneti kapcsolatok, amely adatátviteli műveletek forrásaként szolgál. A támogatott értékek a következők: [InputPortBinding](/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.inputportbinding), [DataReference](#data-reference), [PortDataReference](/python/api/azureml-pipeline-core/azureml.pipeline.core.portdatareference), [PipelineData](/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata), [DataSet](/python/api/azureml-core/azureml.core.dataset%28class%29), [DatasetDefinition](/python/api/azureml-core/azureml.data.dataset_definition.datasetdefinition)vagy [PipelineDataset](/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedataset). |
| `destination_data_reference` | Az adatátviteli műveletek céljaként szolgáló bemeneti kapcsolatok. A támogatott értékek a következők: [PipelineData](/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata) és [OutputPortBinding](/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.outputportbinding). |
| `allow_reuse` | Meghatározza, hogy a lépés újra fel kell-e használni az előző eredményeket, ha ugyanazokkal a beállításokkal futnak újra. |

Az alábbi példa egy adatátviteli lépést tartalmaz:

```yaml
pipeline:
    name: SamplePipelineFromYaml
    parameters:
        PipelineParam1:
            type: int
            default: 3
    data_references:
        adls_test_data:
            datastore: adftestadla
            path_on_datastore: "testdata"
        blob_test_data:
            datastore: workspaceblobstore
            path_on_datastore: "testdata"
    default_compute: adftest
    steps:
        Step1:
            runconfig: "D:\\Yaml\\default_runconfig.yml"
            parameters:
                NUM_ITERATIONS_2:
                    source: PipelineParam1
                NUM_ITERATIONS_1: 7
            type: "DataTransferStep"
            name: "MyDataTransferStep"
            adla_compute_name: adftest
            source_data_reference:
                adls_test_data:
                    source: adls_test_data
            destination_data_reference:
                blob_test_data:
                    source: blob_test_data
```

### <a name="python-script-step"></a>Python-parancsfájl lépései

| YAML kulcs | Leírás |
| ----- | ----- |
| `inputs` | A bemenetek lehetnek [InputPortBinding](/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.inputportbinding), [DataReference](#data-reference), [PortDataReference](/python/api/azureml-pipeline-core/azureml.pipeline.core.portdatareference), [PipelineData](/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata), [adatkészlet](/python/api/azureml-core/azureml.core.dataset%28class%29), [DatasetDefinition](/python/api/azureml-core/azureml.data.dataset_definition.datasetdefinition)vagy [PipelineDataset](/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedataset). |
| `outputs` | A kimenetek lehetnek [PipelineData](/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata) vagy [OutputPortBinding](/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.outputportbinding). |
| `script_name` | A Python-szkript neve (a `source_directory` következőhöz képest). |
| `source_directory` | A parancsfájlt, a Conda-környezetet stb. tartalmazó könyvtár. |
| `runconfig` | Egy fájl elérési útja `.runconfig` . Ez a fájl a [RunConfiguration](/python/api/azureml-core/azureml.core.runconfiguration) osztály YAML ábrázolása. További információ a fájl struktúrájáról: [runconfig.js](https://github.com/microsoft/MLOps/blob/b4bdcf8c369d188e83f40be8b748b49821f71cf2/infra-as-code/runconfigschema.json). |
| `allow_reuse` | Meghatározza, hogy a lépés újra fel kell-e használni az előző eredményeket, ha ugyanazokkal a beállításokkal futnak újra. |

A következő példa egy Python-parancsfájl lépését tartalmazza:

```yaml
pipeline:
    name: SamplePipelineFromYaml
    parameters:
        PipelineParam1:
            type: int
            default: 3
    data_references:
        DataReference1:
            datastore: workspaceblobstore
            path_on_datastore: testfolder/sample.txt
    default_compute: cpu-cluster
    steps:
        Step1:
            runconfig: "D:\\Yaml\\default_runconfig.yml"
            parameters:
                NUM_ITERATIONS_2:
                    source: PipelineParam1
                NUM_ITERATIONS_1: 7
            type: "PythonScriptStep"
            name: "MyPythonScriptStep"
            script_name: "train.py"
            allow_reuse: True
            source_directory: "D:\\scripts\\PythonScript"
            inputs:
                InputData:
                    source: DataReference1
            outputs:
                OutputData:
                    destination: Output4
                    datastore: workspaceblobstore
                    bind_mode: mount
```

### <a name="parallel-run-step"></a>Párhuzamos futtatási lépés

| YAML kulcs | Leírás |
| ----- | ----- |
| `inputs` | A bemenetek [adatkészlet](/python/api/azureml-core/azureml.core.dataset%28class%29), [DatasetDefinition](/python/api/azureml-core/azureml.data.dataset_definition.datasetdefinition)vagy [PipelineDataset](/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedataset)lehet. |
| `outputs` | A kimenetek lehetnek [PipelineData](/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata) vagy [OutputPortBinding](/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.outputportbinding). |
| `script_name` | A Python-szkript neve (a `source_directory` következőhöz képest). |
| `source_directory` | A parancsfájlt, a Conda-környezetet stb. tartalmazó könyvtár. |
| `parallel_run_config` | Egy fájl elérési útja `parallel_run_config.yml` . Ez a fájl a [ParallelRunConfig](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.parallelrunconfig) osztály YAML ábrázolása. |
| `allow_reuse` | Meghatározza, hogy a lépés újra fel kell-e használni az előző eredményeket, ha ugyanazokkal a beállításokkal futnak újra. |

A következő példa egy párhuzamos futtatási lépést tartalmaz:

```yaml
pipeline:
    description: SamplePipelineFromYaml
    default_compute: cpu-cluster
    data_references:
        MyMinistInput:
            dataset_name: mnist_sample_data
    parameters:
        PipelineParamTimeout:
            type: int
            default: 600
    steps:        
        Step1:
            parallel_run_config: "yaml/parallel_run_config.yml"
            type: "ParallelRunStep"
            name: "parallel-run-step-1"
            allow_reuse: True
            arguments:
            - "--progress_update_timeout"
            - parameter:timeout_parameter
            - "--side_input"
            - side_input:SideInputData
            parameters:
                timeout_parameter:
                    source: PipelineParamTimeout
            inputs:
                InputData:
                    source: MyMinistInput
            side_inputs:
                SideInputData:
                    source: Output4
                    bind_mode: mount
            outputs:
                OutputDataStep2:
                    destination: Output5
                    datastore: workspaceblobstore
                    bind_mode: mount
```

### <a name="pipeline-with-multiple-steps"></a>Több lépésből álló folyamat 

| YAML kulcs | Leírás |
| ----- | ----- |
| `steps` | Egy vagy több PipelineStep-definíció sorszáma. Vegye figyelembe, hogy az `destination` egyik lépés kulcsai a `outputs` `source` `inputs` következő lépéshez tartozó kulcsok lesznek.| 

```yaml
pipeline:
    name: SamplePipelineFromYAML
    description: Sample multistep YAML pipeline
    data_references:
        TitanicDS:
            dataset_name: 'titanic_ds'
            bind_mode: download
    default_compute: cpu-cluster
    steps:
        Dataprep:
            type: "PythonScriptStep"
            name: "DataPrep Step"
            compute: cpu-cluster
            runconfig: ".\\default_runconfig.yml"
            script_name: "prep.py"
            arguments:
            - '--train_path'
            - output:train_path
            - '--test_path'
            - output:test_path
            allow_reuse: True
            inputs:
                titanic_ds:
                    source: TitanicDS
                    bind_mode: download
            outputs:
                train_path:
                    destination: train_csv
                    datastore: workspaceblobstore
                test_path:
                    destination: test_csv
        Training:
            type: "PythonScriptStep"
            name: "Training Step"
            compute: cpu-cluster
            runconfig: ".\\default_runconfig.yml"
            script_name: "train.py"
            arguments:
            - "--train_path"
            - input:train_path
            - "--test_path"
            - input:test_path
            inputs:
                train_path:
                    source: train_csv
                    bind_mode: download
                test_path:
                    source: test_csv
                    bind_mode: download

```

## <a name="schedules"></a>Ütemezések

Egy folyamat ütemtervének meghatározásakor az adattárolók által aktivált vagy ismétlődő érték lehet egy adott időintervallum alapján. Az alábbi kulcsok az ütemterv definiálásához használhatók:

| YAML kulcs | Leírás |
| ----- | ----- |
| `description` | Az ütemterv leírása. |
| `recurrence` | Ismétlődési beállításokat tartalmaz, ha az ütemezése ismétlődő. |
| `pipeline_parameters` | A folyamat által igényelt paraméterek. |
| `wait_for_provisioning` | Meg kell-e várni az ütemezett kiépítés befejezését. |
| `wait_timeout` | Azon másodpercek száma, ameddig az időtúllépés előtt várni kell. |
| `datastore_name` | A módosított/hozzáadott Blobok figyelésére szolgáló adattár. |
| `polling_interval` | A módosított/hozzáadott Blobok lekérdezési ideje (percben). Alapértelmezett érték: 5 perc. Csak az adattár-ütemtervek esetében támogatott. |
| `data_path_parameter_name` | Annak az adatelérési útvonal-feldolgozási paraméternek a neve, amely a módosított blob elérési úttal van megadva. Csak az adattár-ütemtervek esetében támogatott. |
| `continue_on_step_failure` | Azt határozza meg, hogy folytatja-e a beküldött PipelineRun más lépéseinek végrehajtását, ha egy lépés meghiúsul. Ha meg van adni, a felülbírálja a `continue_on_step_failure` folyamat beállítását.
| `path_on_datastore` | Választható. Az adattár elérési útja a módosított/hozzáadott Blobok figyeléséhez. Az elérési út az adattár tárolójában van, így az ütemezett figyelők tényleges elérési útja tároló/ `path_on_datastore` . Ha nincs, a rendszer figyeli az adattár tárolóját. A nem figyeli a hozzáadásokat/a-almappában végrehajtott módosításokat `path_on_datastore` . Csak az adattár-ütemtervek esetében támogatott. |

Az alábbi példa egy adattár által aktivált ütemezés definícióját tartalmazza:

```yaml
Schedule: 
      description: "Test create with datastore" 
      recurrence: ~ 
      pipeline_parameters: {} 
      wait_for_provisioning: True 
      wait_timeout: 3600 
      datastore_name: "workspaceblobstore" 
      polling_interval: 5 
      data_path_parameter_name: "input_data" 
      continue_on_step_failure: None 
      path_on_datastore: "file/path" 
```

**Ismétlődő ütemterv** definiálásakor használja a következő kulcsokat a alatt `recurrence` :

| YAML kulcs | Leírás |
| ----- | ----- |
| `frequency` | Az ütemezett ismétlődések gyakorisága. Az érvényes értékek:,,, `"Minute"` `"Hour"` `"Day"` `"Week"` vagy `"Month"` . |
| `interval` | Milyen gyakran következik be az ütemterv. Az egész érték azon időegységek száma, ameddig az ütemezés újra be nem következik. |
| `start_time` | Az ütemterv kezdési időpontja. Az érték karakterlánc-formátuma: `YYYY-MM-DDThh:mm:ss` . Ha nincs megadva kezdési idő, az első számítási feladat azonnal fut, és a jövőbeli munkaterhelések az ütemterv alapján futnak. Ha a kezdési időpont a múltban van, akkor az első munkaterhelés a következő számított futási időpontban fut. |
| `time_zone` | A kezdési idő időzónája. Ha nincs megadva időzóna, a rendszer UTC-t használ. |
| `hours` | Ha `frequency` a `"Day"` vagy a `"Week"` , egy vagy több, 0 és 23 közötti egész számot is megadhat, vesszővel elválasztva, a folyamat futási idejét. Csak `time_of_day` vagy `hours` és használható `minutes` . |
| `minutes` | Ha a vagy a, megadhat `frequency` `"Day"` `"Week"` egy vagy több egész számot 0 és 59 között, vesszővel elválasztva, az óra percében, amikor a folyamat futni fog. Csak `time_of_day` vagy `hours` és használható `minutes` . |
| `time_of_day` | Ha a vagy a, megadhatja `frequency` `"Day"` `"Week"` az ütemterv futtatásának időpontját. Az érték karakterlánc-formátuma: `hh:mm` . Csak `time_of_day` vagy `hours` és használható `minutes` . |
| `week_days` | Ha `frequency` van `"Week"` , egy vagy több napot is megadhat, vesszővel elválasztva, ha az ütemtervnek futnia kell. Az érvényes értékek:,,,,, `"Monday"` `"Tuesday"` `"Wednesday"` `"Thursday"` `"Friday"` `"Saturday"` és `"Sunday"` . |

Az alábbi példa egy ismétlődő ütemezés definícióját tartalmazza:

```yaml
Schedule: 
    description: "Test create with recurrence" 
    recurrence: 
        frequency: Week # Can be "Minute", "Hour", "Day", "Week", or "Month". 
        interval: 1 # how often fires 
        start_time: 2019-06-07T10:50:00 
        time_zone: UTC 
        hours: 
        - 1 
        minutes: 
        - 0 
        time_of_day: null 
        week_days: 
        - Friday 
    pipeline_parameters: 
        'a': 1 
    wait_for_provisioning: True 
    wait_timeout: 3600 
    datastore_name: ~ 
    polling_interval: ~ 
    data_path_parameter_name: ~ 
    continue_on_step_failure: None 
    path_on_datastore: ~ 
```

## <a name="next-steps"></a>Következő lépések

Ismerje meg, hogyan [használható a CLI bővítmény a Azure Machine Learninghoz](reference-azure-machine-learning-cli.md).