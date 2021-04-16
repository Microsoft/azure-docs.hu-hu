---
title: Machine Learning YAML-folyamat
titleSuffix: Azure Machine Learning
description: Ismerje meg, hogyan definiálhat gépi tanulási folyamatot YAML-fájl használatával. A YAML-folyamatdefiníciókat az Azure CLI gépi tanulási bővítményével együtt használjuk.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
ms.reviewer: larryfr
ms.author: nilsp
author: NilsPohlmann
ms.date: 07/31/2020
ms.custom: devx-track-python, devx-track-azurecli
ms.openlocfilehash: 1c44f018d558b9426ba6271c0cbb1c2356a2a9b2
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2021
ms.locfileid: "107478284"
---
# <a name="define-machine-learning-pipelines-in-yaml"></a>Gépi tanulási folyamatok meghatározása a YAML-ben

Ismerje meg, hogyan definiálhatja a gépi tanulási folyamatokat a [YAML-ben.](https://yaml.org/) Ha az Azure CLI gépi tanulási bővítményét használja, a folyamathoz kapcsolódó számos parancs egy YAML-fájlt vár, amely meghatározza a folyamatot.

Az alábbi táblázat felsorolja, hogy jelenleg mi támogatott és mi nem támogatott a folyamatok YAML-ben való definiálása során:

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
| ModuleStep (Modul)Step (Modul) | Igen |
| MPIStep | Nem |
| EstimatorStep | Nem |

## <a name="pipeline-definition"></a>Folyamatdefiníció

A folyamatdefiníciók a következő kulcsokat használják, amelyek megfelelnek [a Pipelines osztálynak:](/python/api/azureml-pipeline-core/azureml.pipeline.core.pipeline.pipeline)

| YAML-kulcs | Leírás |
| ----- | ----- |
| `name` | A folyamat leírása. |
| `parameters` | Paraméter(eket) a folyamathoz. |
| `data_reference` | Meghatározza, hogyan és hol kell elérhetővé tenni az adatokat a futtatásban. |
| `default_compute` | Alapértelmezett számítási cél, ahol a folyamat összes lépése fut. |
| `steps` | A folyamatban használt lépések. |

## <a name="parameters"></a>Paraméterek

A `parameters` szakasz a következő kulcsokat használja, amelyek megfelelnek a [PipelineParameter osztálynak:](/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelineparameter)

| YAML-kulcs | Leírás |
| ---- | ---- |
| `type` | A paraméter értéktípusa. Érvényes típusok: `string` , , , vagy `int` `float` `bool` `datapath` . |
| `default` | Az alapértelmezett érték. |

Minden paraméter neve. Az alábbi YAML-kódrészlet például három, , és nevű `NumIterationsParameter` `DataPathParameter` paramétert `NodeCountParameter` definiál:

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

A `data_references` szakasz a következő kulcsokat használja, amelyek megfelelnek a [DataReference -nek:](/python/api/azureml-core/azureml.data.data_reference.datareference)

| YAML-kulcs | Leírás |
| ----- | ----- |
| `datastore` | A hivatkozni szükséges adattár. |
| `path_on_datastore` | Az adathivatkozáshoz a háttértár relatív elérési útja. |

Minden adathivatkozás egy kulcsban található. Az alábbi YAML-kódrészlet például meghatároz egy nevű kulcsban tárolt `employee_data` adathivatkozást:

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

A lépések egy számítási környezetet határoznak meg a környezeten futtatott fájlokkal együtt. Egy lépés típusának meghatározásához használja a `type` kulcsot:

| Lépés típusa | Leírás |
| ----- | ----- |
| `AdlaStep` | Egy U-SQL-szkriptet futtat Azure Data Lake Analytics. Az [AdlaStep osztálynak](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.adlastep) felel meg. |
| `AzureBatchStep` | Feladatok futtatása a Azure Batch. Az [AzureBatchStep osztálynak](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.azurebatchstep) felel meg. |
| `DatabricsStep` | Hozzáad egy Databricks-jegyzetfüzetet, Python-szkriptet vagy JAR-t. A [DatabricksStep osztálynak](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.databricksstep) felel meg. |
| `DataTransferStep` | Adatokat továbbít a tárolási lehetőségek között. A [DataTransferStep osztálynak](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.datatransferstep) felel meg. |
| `PythonScriptStep` | Egy Python-szkriptet futtat. A [PythonScriptStep osztálynak](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.python_script_step.pythonscriptstep) felel meg. |
| `ParallelRunStep` | Egy Python-szkriptet futtat nagy mennyiségű adat aszinkron és párhuzamos feldolgozásához. A [ParallelRunStep osztálynak](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.parallel_run_step.parallelrunstep) felel meg. |

### <a name="adla-step"></a>ADLA lépés

| YAML-kulcs | Leírás |
| ----- | ----- |
| `script_name` | A U-SQL-szkript neve (a értékhez `source_directory` viszonyítva). |
| `compute` | Az ehhez a lépéshez használt Azure Data Lake számítási cél. |
| `parameters` | [A](#parameters) folyamat paraméterei. |
| `inputs` | Bemenetek: [InputPortBinding,](/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.inputportbinding) [DataReference,](#data-reference) [PortDataReference,](/python/api/azureml-pipeline-core/azureml.pipeline.core.portdatareference) [PipelineData,](/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata) [Dataset, DatasetDefinition](/python/api/azureml-core/azureml.data.dataset_definition.datasetdefinition)vagy [PipelineDataset.](/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedataset) [](/python/api/azureml-core/azureml.core.dataset%28class%29) |
| `outputs` | A kimenet lehet [PipelineData vagy](/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata) [OutputPortBinding.](/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.outputportbinding) |
| `source_directory` | A szkriptet, szerelvényeket stb. tartalmazó könyvtár. |
| `priority` | Az aktuális feladathoz használnia kell a prioritásértéket. |
| `params` | Név-érték párok szótára. |
| `degree_of_parallelism` | A feladathoz használt párhuzamosság mértéke. |
| `runtime_version` | Az Data Lake Analytics motor Data Lake Analytics verziója. |
| `allow_reuse` | Meghatározza, hogy a lépés újra felhasználja-e a korábbi eredményeket, amikor újra fut ugyanezekkel a beállításokkal. |

Az alábbi példa egy ADLA-lépés definícióját tartalmazza:

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

| YAML-kulcs | Leírás |
| ----- | ----- |
| `compute` | Az Azure Batch ehhez a lépéshez használt számítási cél. |
| `inputs` | Bemenetek: [InputPortBinding,](/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.inputportbinding) [DataReference,](#data-reference) [PortDataReference,](/python/api/azureml-pipeline-core/azureml.pipeline.core.portdatareference) [PipelineData,](/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata) [Dataset, DatasetDefinition](/python/api/azureml-core/azureml.data.dataset_definition.datasetdefinition)vagy [PipelineDataset.](/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedataset) [](/python/api/azureml-core/azureml.core.dataset%28class%29) |
| `outputs` | A kimenet lehet [PipelineData vagy](/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata) [OutputPortBinding.](/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.outputportbinding) |
| `source_directory` | A modul bináris fájlokat, végrehajtható fájlokat, szerelvényeket stb. tartalmazó könyvtára. |
| `executable` | A feladat részeként futtatandó parancs/végrehajtható fájl neve. |
| `create_pool` | Logikai jelző, amely jelzi, hogy létre kell-e hozni a készletet a feladat futtatása előtt. |
| `delete_batch_job_after_finish` | Logikai jelző, amely jelzi, hogy a feladatot törölni kell-e a Batch-fiókból, miután befejeződött. |
| `delete_batch_pool_after_finish` | Logikai jelző, amely jelzi, hogy a feladat befejezése után törölje-e a készletet. |
| `is_positive_exit_code_failure` | Logikai jelző, amely azt jelzi, hogy a feladat meghiúsul-e, ha a tevékenység pozitív kóddal kilép. |
| `vm_image_urn` | Ha a , akkor a `create_pool` `True` virtuális gép a et `VirtualMachineConfiguration` használja. |
| `pool_id` | Annak a készletnek az azonosítója, ahol a feladat futni fog. |
| `allow_reuse` | Meghatározza, hogy a lépés újból felhasználja-e a korábbi eredményeket, amikor újra fut ugyanazokkal a beállításokkal. |

Az alábbi példa egy Azure Batch definícióját tartalmazza:

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

### <a name="databricks-step"></a>Databricks-lépés

| YAML-kulcs | Leírás |
| ----- | ----- |
| `compute` | A Azure Databricks számítási cél, amely ehhez a lépéshez használható. |
| `inputs` | Bemenetek: [InputPortBinding,](/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.inputportbinding) [DataReference,](#data-reference) [PortDataReference,](/python/api/azureml-pipeline-core/azureml.pipeline.core.portdatareference) [PipelineData,](/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata) [Dataset,](/python/api/azureml-core/azureml.core.dataset%28class%29) [DatasetDefinition](/python/api/azureml-core/azureml.data.dataset_definition.datasetdefinition), vagy [PipelineDataset.](/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedataset) |
| `outputs` | A kimenet lehet [PipelineData vagy](/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata) [OutputPortBinding.](/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.outputportbinding) |
| `run_name` | A futtatás neve a Databricksben. |
| `source_directory` | A parancsfájlt és más fájlokat tartalmazó könyvtár. |
| `num_workers` | A Databricks-fürtön futó dolgozók statikus száma. |
| `runconfig` | Egy fájl `.runconfig` elérési útja. Ez a fájl a [RunConfiguration osztály YAML-reprezentációja.](/python/api/azureml-core/azureml.core.runconfiguration) A fájl szerkezetével kapcsolatos további információkért lásd arunconfigschema.js[oldalon.](https://github.com/microsoft/MLOps/blob/b4bdcf8c369d188e83f40be8b748b49821f71cf2/infra-as-code/runconfigschema.json) |
| `allow_reuse` | Meghatározza, hogy a lépés újból felhasználja-e a korábbi eredményeket, amikor újra fut ugyanazokkal a beállításokkal. |

Az alábbi példa egy Databricks-lépést tartalmaz:

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

| YAML-kulcs | Leírás |
| ----- | ----- |
| `compute` | A Azure Data Factory számítási cél, amely ehhez a lépéshez használható. |
| `source_data_reference` | Bemeneti kapcsolat, amely az adatátviteli műveletek forrásaként szolgál. A támogatott értékek: [InputPortBinding,](/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.inputportbinding) [DataReference,](#data-reference) [PortDataReference,](/python/api/azureml-pipeline-core/azureml.pipeline.core.portdatareference) [PipelineData,](/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata) [Dataset,](/python/api/azureml-core/azureml.core.dataset%28class%29) [DatasetDefinition](/python/api/azureml-core/azureml.data.dataset_definition.datasetdefinition)vagy [PipelineDataset.](/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedataset) |
| `destination_data_reference` | Bemeneti kapcsolat, amely az adatátviteli műveletek céljaként szolgál. A támogatott értékek a [PipelineData és](/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata) [az OutputPortBinding.](/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.outputportbinding) |
| `allow_reuse` | Meghatározza, hogy a lépés újra felhasználja-e a korábbi eredményeket, amikor újra fut ugyanezekkel a beállításokkal. |

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

### <a name="python-script-step"></a>Python-szkript lépés

| YAML-kulcs | Leírás |
| ----- | ----- |
| `inputs` | Bemenetek: [InputPortBinding,](/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.inputportbinding) [DataReference,](#data-reference) [PortDataReference,](/python/api/azureml-pipeline-core/azureml.pipeline.core.portdatareference) [PipelineData,](/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata) [Dataset, DatasetDefinition](/python/api/azureml-core/azureml.data.dataset_definition.datasetdefinition)vagy [PipelineDataset.](/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedataset) [](/python/api/azureml-core/azureml.core.dataset%28class%29) |
| `outputs` | A kimenet lehet [PipelineData vagy](/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata) [OutputPortBinding.](/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.outputportbinding) |
| `script_name` | A Python-szkript neve (a következőhöz `source_directory` viszonyítva: ). |
| `source_directory` | A szkriptet, a Conda-környezetet stb. tartalmazó könyvtár. |
| `runconfig` | Egy fájl `.runconfig` elérési útja. Ez a fájl a [RunConfiguration osztály YAML-ábrázolása.](/python/api/azureml-core/azureml.core.runconfiguration) A fájl szerkezetével kapcsolatos további információkért lásd arunconfig.js[oldalon.](https://github.com/microsoft/MLOps/blob/b4bdcf8c369d188e83f40be8b748b49821f71cf2/infra-as-code/runconfigschema.json) |
| `allow_reuse` | Meghatározza, hogy a lépés újra felhasználja-e a korábbi eredményeket, amikor újra fut ugyanezekkel a beállításokkal. |

Az alábbi példa egy Python-szkriptlépést tartalmaz:

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

### <a name="parallel-run-step"></a>Párhuzamos futtatás lépése

| YAML-kulcs | Leírás |
| ----- | ----- |
| `inputs` | Bemenetek: [Adatkészlet,](/python/api/azureml-core/azureml.core.dataset%28class%29) [DatasetDefinition](/python/api/azureml-core/azureml.data.dataset_definition.datasetdefinition)vagy [PipelineDataset.](/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedataset) |
| `outputs` | A kimenet lehet [PipelineData vagy](/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata) [OutputPortBinding.](/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.outputportbinding) |
| `script_name` | A Python-szkript neve (a következőhöz `source_directory` viszonyítva: ). |
| `source_directory` | A szkriptet, a Conda-környezetet stb. tartalmazó könyvtár. |
| `parallel_run_config` | Egy fájl `parallel_run_config.yml` elérési útja. Ez a fájl a [ParallelRunConfig osztály YAML-reprezentációja.](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.parallelrunconfig) |
| `allow_reuse` | Meghatározza, hogy a lépés újból felhasználja-e a korábbi eredményeket, amikor újra fut ugyanazokkal a beállításokkal. |

Az alábbi példa egy Párhuzamos futtatás lépést tartalmaz:

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

### <a name="pipeline-with-multiple-steps"></a>Folyamat több lépéssel 

| YAML-kulcs | Leírás |
| ----- | ----- |
| `steps` | Egy vagy több PipelineStep-definíció sorozata. Vegye figyelembe, hogy az egyik lépés kulcsa lesz a következő `destination` `outputs` lépés `source` `inputs` kulcsa.| 

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

Egy folyamat ütemezésének meghatározásakor az adattár aktiválható vagy ismétlődő lehet egy időintervallum alapján. Az ütemezés meghatározásához használt kulcsok a következők:

| YAML-kulcs | Leírás |
| ----- | ----- |
| `description` | Az ütemezés leírása. |
| `recurrence` | Ismétlődési beállításokat tartalmaz, ha az ütemezés ismétlődő. |
| `pipeline_parameters` | A folyamat által megkövetelt paraméterek. |
| `wait_for_provisioning` | Meg kell-e várni az ütemezés kiépítésének befejezését. |
| `wait_timeout` | Az időkorreklig várakozási idő másodpercben. |
| `datastore_name` | A módosított/hozzáadott blobok figyelése érdekében használt adattár. |
| `polling_interval` | A módosított/hozzáadott blobok lekérdezése közötti idő percek alatt. Alapértelmezett érték: 5 perc. Csak adattár-ütemezések esetén támogatott. |
| `data_path_parameter_name` | A módosított blob elérési úthoz beállítható adatútvonal-folyamat paraméterének neve. Csak adattár-ütemezések esetén támogatott. |
| `continue_on_step_failure` | Azt határozza meg, hogy folytassa-e az elküldött PipelineRun (Folyamatfuttatás) további lépéseit, ha egy lépés meghiúsul. Ha meg van téve, a felülírja `continue_on_step_failure` a folyamat beállítását.
| `path_on_datastore` | Választható. Az adattár elérési útja a módosított/hozzáadott blobok figyelése érdekében. Az elérési út az adattár tárolója alatt található, így az ütemezés által figyelt tényleges útvonal a container/ `path_on_datastore` . Ha nincs ilyen, a rendszer figyeli az adattártárolót. A almappában végzett kiegészítéseket/módosításokat a rendszer `path_on_datastore` nem figyeli. Csak adattár-ütemezések esetén támogatott. |

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

Ismétlődő ütemezés **definiálásakor** használja a következő kulcsokat a `recurrence` alatt:

| YAML-kulcs | Leírás |
| ----- | ----- |
| `frequency` | Milyen gyakran ismétlődik az ütemezés. Érvényes értékek: `"Minute"` , , , vagy `"Hour"` `"Day"` `"Week"` `"Month"` . |
| `interval` | Milyen gyakran tűz ki az ütemezés. Az egész szám az az időegység, ameddig az ütemezés újra olt. |
| `start_time` | Az ütemezés kezdési ideje. Az érték sztringformátuma `YYYY-MM-DDThh:mm:ss` . Ha nincs megadott kezdési időpont, az első számítási feladat azonnal lefut, a jövőbeli számítási feladatok pedig az ütemezés alapján futnak. Ha a kezdési időpont múltbeli, az első számítási feladat a következő számított futási időpontban lesz futtatva. |
| `time_zone` | A kezdési időpont időzónája. Ha nincs megadva időzóna, a utc időzóna lesz használva. |
| `hours` | Ha a vagy a , akkor 0 és 23 között egy vagy több egész szám adható meg vesszővel elválasztva a folyamat futásának nap `frequency` `"Day"` `"Week"` órájában. Csak `time_of_day` vagy `hours` és `minutes` használható. |
| `minutes` | Ha a vagy a , akkor 0 és 59 között egy vagy több, vesszővel elválasztott egész szám adható meg a folyamat futásának óra `frequency` `"Day"` `"Week"` percében. Csak `time_of_day` vagy `hours` és `minutes` használható. |
| `time_of_day` | Ha a vagy a , megadhatja az ütemezés `frequency` `"Day"` `"Week"` futtatásának napnak megfelelő idejét. Az érték sztringformátuma `hh:mm` . Csak `time_of_day` vagy `hours` és `minutes` használható. |
| `week_days` | Ha a , akkor megadhat egy vagy több napot `frequency` vesszővel elválasztva, hogy mikor `"Week"` fusson az ütemezés. Érvényes értékek: `"Monday"` , , , , , és `"Tuesday"` `"Wednesday"` `"Thursday"` `"Friday"` `"Saturday"` `"Sunday"` . |

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

Megtudhatja, hogyan [használhatja a CLI-bővítményt a Azure Machine Learning.](reference-azure-machine-learning-cli.md)
