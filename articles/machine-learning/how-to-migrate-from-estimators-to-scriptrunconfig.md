---
title: Migrálás becslőkből a ScriptRunConfigba
titleSuffix: Azure Machine Learning
description: Áttelepítési útmutató a becslések-ről a ScriptRunConfig-re való áttelepítéshez a betanítási feladatok konfigurálásához.
services: machine-learning
author: mx-iao
ms.author: minxia
ms.reviewer: sgilley
ms.service: machine-learning
ms.subservice: core
ms.date: 12/14/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python, contperf-fy21q1
ms.openlocfilehash: ae0623a11b940a4d142f6bfae02d4b20727a6f55
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "102518872"
---
# <a name="migrating-from-estimators-to-scriptrunconfig"></a>Migrálás a becslések-ből a ScriptRunConfig-be

Eddig több módszer is rendelkezésre áll a betanítási feladatok Azure Machine Learning az SDK-n keresztül, beleértve a becslések, a ScriptRunConfig és az alsó szintű RunConfiguration.   A kétértelműség és az inkonzisztencia megoldásához az Azure ML-ben leegyszerűsítjük a feladatok konfigurálásának folyamatát.  Most az ScriptRunConfig-t kell használnia a betanítási feladatok konfigurálásához javasolt beállításként. 

A becslések elavultak a Python SDK 1.19.0-kiadásával. Általában kerülnie kell egy RunConfiguration objektum explicit módon történő létrehozását is, ehelyett a ScriptRunConfig osztály használatával kell konfigurálnia a feladatot.

Ez a cikk a becslések-ről ScriptRunConfig-re való áttelepítésének gyakori szempontjait ismerteti.

> [!IMPORTANT]
> A becslések-ból származó ScriptRunConfig való Migrálás esetén győződjön meg arról, hogy a Python SDK-t használja >= 1.15.0.

## <a name="scriptrunconfig-documentation-and-samples"></a>A ScriptRunConfig dokumentációja és mintái
Azure Machine Learning dokumentáció és minták frissítve lettek a [ScriptRunConfig](/python/api/azureml-core/azureml.core.script_run_config.scriptrunconfig) használatára a feladatok konfigurálásához és a beküldéshez.

A ScriptRunConfig használatával kapcsolatos információkért tekintse meg a következő dokumentációt:
* [Betanítási futtatások konfigurálása és elküldése](how-to-set-up-training-targets.md)
* [PyTorch-betanítási futtatások konfigurálása](how-to-train-pytorch.md)
* [TensorFlow-betanítási futtatások konfigurálása](how-to-train-tensorflow.md)
* [A scikit konfigurálása – képzések futtatásának megismerése](how-to-train-scikit-learn.md)

Továbbá tekintse át az alábbi példákat & oktatóanyagokat:
* [Azure/MachineLearningNotebooks](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/ml-frameworks)
* [Azure/azureml – példák](https://github.com/Azure/azureml-examples)

## <a name="defining-the-training-environment"></a>A képzési környezet meghatározása
Habár a különböző keretrendszer-becslések olyan előre konfigurált környezetekkel rendelkeznek, amelyeket Docker-rendszerképek támogatnak, a lemezképek Dockerfiles privát.  Ezért nem rendelkezik a környezetek által tartalmazott nagy átláthatósággal. Emellett a becslések a környezettel kapcsolatos konfigurációkat is külön paraméterekként (például:) veszi igénybe a `pip_packages` `custom_docker_image` megfelelő konstruktorokban.

A ScriptRunConfig használatakor a rendszer az összes környezettel kapcsolatos konfigurációt beágyazza az `Environment` objektumba, amelyet a rendszer a `environment` ScriptRunConfig konstruktorának paraméterében adott át. A betanítási feladatok konfigurálásához adjon meg egy olyan környezetet, amely rendelkezik a betanítási parancsfájlhoz szükséges összes függőséggel. Ha nincs megadva környezet, az Azure ML az alapértelmezett környezetként az Azure ML alaprendszerképének egyikét fogja használni, amely a által meghatározott `azureml.core.environment.DEFAULT_CPU_IMAGE` . Több módon is biztosítható környezet:

* A beválogatott [környezetek használata](how-to-use-environments.md#use-a-curated-environment) – a munkaterületen alapértelmezés szerint előre definiált környezetek érhetők el. Az egyes előre konfigurált keretrendszer-vagy verziójú Docker-rendszerképekhez megfelelő, az egyes keretrendszer-kalkulátorok által támogatott rendszerbiztonsági környezet tartozik.
* [Saját egyéni környezet definiálása](how-to-use-environments.md)

Íme egy példa arra, hogyan használható a PyTorch 1,6-környezet a betanításhoz:

```python
from azureml.core import Workspace, ScriptRunConfig, Environment

curated_env_name = 'AzureML-PyTorch-1.6-GPU'
pytorch_env = Environment.get(workspace=ws, name=curated_env_name)

compute_target = ws.compute_targets['my-cluster']
src = ScriptRunConfig(source_directory='.',
                      script='train.py',
                      compute_target=compute_target,
                      environment=pytorch_env)
```

Ha olyan **környezeti változót** szeretne megadni, amely a betanítási parancsfájl végrehajtásának folyamatán lesz beállítva, használja a környezeti objektumot:
```
myenv.environment_variables = {"MESSAGE":"Hello from Azure Machine Learning"}
```

Az Azure ML-környezetek konfigurálásával és kezelésével kapcsolatos információkért lásd:
* [A környezetek használata](how-to-use-environments.md)
* [Válogatott környezetek](resource-curated-environments.md)
* [Betanítás egyéni Docker-lemezképpel](how-to-train-with-custom-image.md)

## <a name="using-data-for-training"></a>Az adatképzések használata
### <a name="datasets"></a>Adathalmazok
Ha Azure ML-adatkészletet használ a betanításhoz, adja át az adatkészletet a szkript argumentumként a `arguments` paraméter használatával. Ezzel az adatelérési utat (csatlakoztatási pont vagy letöltési útvonal) a betanítási parancsfájlban az argumentumok segítségével érheti el.

A következő példa egy olyan betanítási feladatot konfigurál, amelyben a FileDataset a távoli számítási feladatokhoz csatlakozik `mnist_ds` .
```python
src = ScriptRunConfig(source_directory='.',
                      script='train.py',
                      arguments=['--data-folder', mnist_ds.as_mount()], # or mnist_ds.as_download() to download
                      compute_target=compute_target,
                      environment=pytorch_env)
```

### <a name="datareference-old"></a>DataReference (régi)
Noha azt javasoljuk, hogy az Azure ML-adatkészleteket a régi DataReference módon használja, ha bármilyen okból továbbra is használja a DataReferences-t, a feladatot a következőképpen kell konfigurálnia:
```python
# if you want to pass a DataReference object, such as the below:
datastore = ws.get_default_datastore()
data_ref = datastore.path('./foo').as_mount()

src = ScriptRunConfig(source_directory='.',
                      script='train.py',
                      arguments=['--data-folder', str(data_ref)], # cast the DataReference object to str
                      compute_target=compute_target,
                      environment=pytorch_env)
src.run_config.data_references = {data_ref.data_reference_name: data_ref.to_config()} # set a dict of the DataReference(s) you want to the `data_references` attribute of the ScriptRunConfig's underlying RunConfiguration object.
```

További információ a betanítási adatok használatáról:
* [Képzés az Azure ML adatkészletekkel](./how-to-train-with-datasets.md)

## <a name="distributed-training"></a>Elosztott betanítás
Ha a betanításhoz elosztott feladatot kell konfigurálnia, akkor a `distributed_job_config` paramétert a ScriptRunConfig konstruktorban kell megadnia. Továbbítson egy [MpiConfiguration](/python/api/azureml-core/azureml.core.runconfig.mpiconfiguration), [PyTorchConfiguration](/python/api/azureml-core/azureml.core.runconfig.pytorchconfiguration)vagy [TensorflowConfiguration](/python/api/azureml-core/azureml.core.runconfig.tensorflowconfiguration) a megfelelő típusú elosztott feladatokhoz.

Az alábbi példa egy PyTorch-betanítási feladatot konfigurál az elosztott képzések MPI/Horovod használatával történő használatához:
```python
from azureml.core.runconfig import MpiConfiguration

src = ScriptRunConfig(source_directory='.',
                      script='train.py',
                      compute_target=compute_target,
                      environment=pytorch_env,
                      distributed_job_config=MpiConfiguration(node_count=2, process_count_per_node=2))
```

További információkért lásd:
* [Elosztott betanítás a PyTorch használatával](how-to-train-pytorch.md#distributed-training)
* [Elosztott képzés a TensorFlow](how-to-train-tensorflow.md#distributed-training)

## <a name="miscellaneous"></a>Különböző veszélyes anyagok és tárgyak
Ha bármilyen okból el kell érnie egy ScriptRunConfig mögöttes RunConfiguration objektumot, a következőképpen teheti meg:
```
src.run_config
```

## <a name="next-steps"></a>Következő lépések

* [Betanítási futtatások konfigurálása és elküldése](how-to-set-up-training-targets.md)