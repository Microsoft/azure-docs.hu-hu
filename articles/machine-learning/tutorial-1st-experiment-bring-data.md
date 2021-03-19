---
title: 'Oktatóanyag: saját adatai használata'
titleSuffix: Azure Machine Learning
description: A Azure Machine Learning Get-Started sorozat 4. része azt mutatja be, hogyan használhatók a saját adatai egy távoli tanítási futtatásban.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: aminsaied
ms.author: amsaied
ms.reviewer: sgilley
ms.date: 02/11/2021
ms.custom: tracking-python
ms.openlocfilehash: ecabfde624ba6d3393bbf6d5480b83dbb5303c5e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "104604558"
---
# <a name="tutorial-use-your-own-data-part-4-of-4"></a>Oktatóanyag: saját adatai használata (4. rész)

Ebből az oktatóanyagból megtudhatja, hogyan tölthet fel és használhat saját adatait a gépi tanulási modellek Azure Machine Learningban való betanításához.

Ez az oktatóanyag *egy négy részből álló oktatóanyag-Sorozat 4. része* , amelyben megismerheti a Azure Machine learning és az Azure-ban végzett feladatok-alapú gépi tanulási feladatok alapjait. Ez az oktatóanyag az [1. rész: beállítás](tutorial-1st-experiment-sdk-setup-local.md), [2. rész: futtassa a "Hello World!"](tutorial-1st-experiment-hello-world.md)és a [3. rész: a modell betanítása](tutorial-1st-experiment-sdk-train.md)című részben leírtakat.

A [3. részben: a modell betanítása](tutorial-1st-experiment-sdk-train.md)az `torchvision.datasets.CIFAR10` PyTorch API beépített metódusával lett letöltve. Sok esetben azonban a saját adatait is használni szeretné egy távoli betanítási futtatás során. Ez a cikk azt a munkafolyamatot mutatja be, amelynek használatával a saját adataival dolgozhat Azure Machine Learningban.

Az oktatóanyag során az alábbi lépéseket fogja végrehajtani:

> [!div class="checklist"]
> * Betanítási parancsfájlt konfigurálhat a helyi címtárban lévő adatértékek használatára.
> * Tesztelje a betanítási szkriptet helyileg.
> * Adatok feltöltése az Azure-ba.
> * Hozzon létre egy vezérlő parancsfájlt.
> * Ismerje meg az új Azure Machine Learning fogalmakat (paraméterek, adatkészletek, adattárolók átadása).
> * A betanítási szkript elküldése és futtatása.
> * A kód kimenetének megtekintése a felhőben.

## <a name="prerequisites"></a>Előfeltételek

Szüksége lesz az előző oktatóanyagban létrehozott pytorch-környezetre vonatkozó adatmennyiségre és frissített verzióra.  Győződjön meg arról, hogy végrehajtotta a következő lépéseket:

1. [A betanítási szkript létrehozása](tutorial-1st-experiment-sdk-train.md#create-training-scripts)
1. [Új Python-környezet létrehozása](tutorial-1st-experiment-sdk-train.md#environment)
1. [Helyi tesztelés](tutorial-1st-experiment-sdk-train.md#test-local)
1. [A Conda-környezet fájljának frissítése](tutorial-1st-experiment-sdk-train.md#update-the-conda-environment-file)

## <a name="adjust-the-training-script"></a>A betanítási parancsfájl módosítása

Mostantól a Azure Machine Learning futó betanítási szkriptet (oktatóanyag/src/Train. Másolás) futtatja, és nyomon követheti a modell teljesítményét. Parametrizálja a betanítási szkriptet argumentumok bevezetésével. Az argumentumok használata lehetővé teszi a különböző hiperparaméterek beállítása egyszerű összehasonlítását.

A betanítási szkript most úgy van beállítva, hogy a CIFAR10-adatkészletet minden futtatáskor letöltse. A következő Python-kód lett kialakítva, hogy beolvassa a címtárból származó adatok olvasását.

>[!NOTE] 
> A `argparse` szkript felparaméterezi használata.

:::code language="python" source="~/MachineLearningNotebooks/tutorials/get-started-day1/code/pytorch-cifar10-your-data/train.py":::

### <a name="understanding-the-code-changes"></a>A kód módosításainak megismerése

A kód a `train.py` függvénytárat használta a, a `argparse` és a beállításához `data_path` `learning_rate` `momentum` .

```python
# .... other code
parser = argparse.ArgumentParser()
parser.add_argument('--data_path', type=str, help='Path to the training data')
parser.add_argument('--learning_rate', type=float, default=0.001, help='Learning rate for SGD')
parser.add_argument('--momentum', type=float, default=0.9, help='Momentum for SGD')
args = parser.parse_args()
# ... other code
```

A parancsfájl a `train.py` felhasználó által definiált paraméterek használatára is alkalmazkodott az Optimizer frissítéséhez:

```python
optimizer = optim.SGD(
    net.parameters(),
    lr=args.learning_rate,     # get learning rate from command-line argument
    momentum=args.momentum,    # get momentum from command-line argument
)
```

> [!div class="nextstepaction"]
> [Kiigazítottam a betanítási szkriptet](?success=adjust-training-script#test-locally) [egy hibába ütközött](https://www.research.net/r/7C6W7BQ?issue=adjust-training-script)

## <a name="test-the-script-locally"></a><a name="test-locally"></a> A parancsfájl helyi tesztelése

A szkript mostantól argumentumként fogadja az _adatelérési utat_ . A-től kezdődően tesztelje helyileg. Adja hozzá az oktatóanyag-címtár struktúrához egy nevű mappát `data` . A címtár struktúrájának a következőhöz hasonlóan kell kinéznie:

:::image type="content" source="media/tutorial-1st-experiment-bring-data/directory-structure.png" alt-text="A címtár szerkezete a. azureml, az adat és az src alkönyvtárakat mutatja.":::

1. Lépjen ki a jelenlegi környezetből.

    ```bash
    conda deactivate

1. Now create and activate the new environment.  This will rebuild the pytorch-aml-env with the [updated environment file](tutorial-1st-experiment-sdk-train.md#update-the-conda-environment-file)


    ```bash
    conda env create -f .azureml/pytorch-env.yml    # create the new conda environment with updated dependencies
    ```

    ```bash
    conda activate pytorch-aml-env          # activate new conda environment
    ```

1. Végül futtassa helyileg a módosított betanítási parancsfájlt.

    ```bash
    python src/train.py --data_path ./data --learning_rate 0.003 --momentum 0.92
    ```

Nem kell letöltenie a CIFAR10-adatkészletet az adat helyi elérési útjának átadásával. A _tanulási sebesség_ és a _lendület_ hiperparaméterek beállítása különböző értékeivel is kísérletezhet, anélkül, hogy a betanítási szkriptben kellene őket feldolgoznia.

> [!div class="nextstepaction"]
> [A szkriptet helyileg teszteltem](?success=test-locally#upload) [egy probléma](https://www.research.net/r/7C6W7BQ?issue=test-locally)

## <a name="upload-the-data-to-azure"></a><a name="upload"></a> Adatok feltöltése az Azure-ba

A szkript Azure Machine Learning-ben való futtatásához a betanítási adatait elérhetővé kell tenni az Azure-ban. Az Azure Machine Learning-munkaterület _alapértelmezett_ adattárral van ellátva. Ez egy Azure Blob Storage-fiók, ahol elvégezheti a betanítási adatai tárolását.

>[!NOTE] 
> A Azure Machine Learning lehetővé teszi az adatai tárolására szolgáló más felhőalapú adattárolók összekapcsolását. További részletekért tekintse meg az [adattár dokumentációját](./concept-data.md).  

Hozzon létre egy új `05-upload-data.py` , a címtárban nevű Python-vezérlő parancsfájlt `tutorial` :

:::code language="python" source="~/MachineLearningNotebooks/tutorials/get-started-day1/IDE-users/05-upload-data.py":::

Az `target_path` érték határozza meg az adattároló azon elérési útját, ahol a CIFAR10-adatfeltöltés történik.

>[!TIP] 
> Az adatok feltöltéséhez Azure Machine Learning használatakor a [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) használatával ad hoc fájlokat tölthet fel. Ha ETL-eszközre van szüksége, a [Azure Data Factory](../data-factory/introduction.md) használatával betöltheti adatait az Azure-ba.

Az aktivált *tutorial1* Conda környezettel rendelkező ablakban futtassa a Python-fájlt az adatok feltöltéséhez. (A feltöltésnek gyorsnak, 60 másodpercnél rövidebbnek kell lennie.)

```bash
python 05-upload-data.py
```

A következő standard kimenetnek kell megjelennie:

```txt
Uploading ./data\cifar-10-batches-py\data_batch_2
Uploaded ./data\cifar-10-batches-py\data_batch_2, 4 files out of an estimated total of 9
.
.
Uploading ./data\cifar-10-batches-py\data_batch_5
Uploaded ./data\cifar-10-batches-py\data_batch_5, 9 files out of an estimated total of 9
Uploaded 9 files
```

> [!div class="nextstepaction"]
> [Feltöltöttem a](?success=upload-data#control-script) [problémákba ütközött](https://www.research.net/r/7C6W7BQ?issue=upload-data)

## <a name="create-a-control-script"></a><a name="control-script"></a> Vezérlő parancsfájl létrehozása

Ahogy korábban végzett, hozzon létre egy új, nevű Python-vezérlő parancsfájlt `06-run-pytorch-data.py` :

```python
# 06-run-pytorch-data.py
from azureml.core import Workspace
from azureml.core import Experiment
from azureml.core import Environment
from azureml.core import ScriptRunConfig
from azureml.core import Dataset

if __name__ == "__main__":
    ws = Workspace.from_config()
    datastore = ws.get_default_datastore()
    dataset = Dataset.File.from_files(path=(datastore, 'datasets/cifar10'))

    experiment = Experiment(workspace=ws, name='day1-experiment-data')

    config = ScriptRunConfig(
        source_directory='./src',
        script='train.py',
        compute_target='cpu-cluster',
        arguments=[
            '--data_path', dataset.as_named_input('input').as_mount(),
            '--learning_rate', 0.003,
            '--momentum', 0.92],
    )
    # set up pytorch environment
    env = Environment.from_conda_specification(
        name='pytorch-env',
        file_path='./.azureml/pytorch-env.yml'
    )
    config.run_config.environment = env

    run = experiment.submit(config)
    aml_url = run.get_portal_url()
    print("Submitted to compute cluster. Click link below")
    print("")
    print(aml_url)
```

### <a name="understand-the-code-changes"></a>A kód módosításainak megismerése

A vezérlő parancsfájl hasonló a [sorozat 3. részéből](tutorial-1st-experiment-sdk-train.md), a következő új sorokkal:

:::row:::
   :::column span="":::
      `dataset = Dataset.File.from_files( ... )`
   :::column-end:::
   :::column span="2":::
      Az [adatkészletek](/python/api/azureml-core/azureml.core.dataset.dataset) az Azure Blob Storageba feltöltött adatokat használják. Az adatkészletek olyan absztrakt rétegek, amelyek a megbízhatóság és a megbízhatóság javítása érdekében lettek kialakítva.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      `config = ScriptRunConfig(...)`
   :::column-end:::
   :::column span="2":::
      A [ScriptRunConfig](/python/api/azureml-core/azureml.core.scriptrunconfig) úgy módosul, hogy tartalmazza a számára átadott argumentumok listáját `train.py` . Az `dataset.as_named_input('input').as_mount()` argumentum azt jelenti, hogy a megadott könyvtár a számítási célhoz lesz _csatlakoztatva_ .
   :::column-end:::
:::row-end:::

> [!div class="nextstepaction"]
> [Létrehoztam a vezérlési parancsfájlt](?success=control-script#submit-to-cloud) [, amely egy hibába ütközött](https://www.research.net/r/7C6W7BQ?issue=control-script)

## <a name="submit-the-run-to-azure-machine-learning"></a><a name="submit-to-cloud"></a> A Futtatás beküldése Azure Machine Learningre

Most küldje el újra a futtatást az új konfiguráció használatára:

```bash
python 06-run-pytorch-data.py
```

Ez a kód egy URL-címet fog kinyomtatni a kísérlethez a Azure Machine Learning Studióban. Ha erre a hivatkozásra kattint, megtekintheti a kód futását.

> [!div class="nextstepaction"]
> [Újraküldöttem a futtatást](?success=submit-to-cloud#inspect-log) [egy hibába](https://www.research.net/r/7C6W7BQ?issue=submit-to-cloud)

### <a name="inspect-the-log-file"></a><a name="inspect-log"></a> A naplófájl vizsgálata

A Studióban nyissa meg a kísérlet futtatását (az előző URL-kimenet kiválasztásával), majd a **kimenetek + naplók** elemet. Válassza ki a `70_driver_log.txt` fájlt. A következő kimenetnek kell megjelennie:

```txt
Processing 'input'.
Processing dataset FileDataset
{
  "source": [
    "('workspaceblobstore', 'datasets/cifar10')"
  ],
  "definition": [
    "GetDatastoreFiles"
  ],
  "registration": {
    "id": "XXXXX",
    "name": null,
    "version": null,
    "workspace": "Workspace.create(name='XXXX', subscription_id='XXXX', resource_group='X')"
  }
}
Mounting input to /tmp/tmp9kituvp3.
Mounted input to /tmp/tmp9kituvp3 as folder.
Exit __enter__ of DatasetContextManager
Entering Run History Context Manager.
Current directory:  /mnt/batch/tasks/shared/LS_root/jobs/dsvm-aml/azureml/tutorial-session-3_1600171983_763c5381/mounts/workspaceblobstore/azureml/tutorial-session-3_1600171983_763c5381
Preparing to call script [ train.py ] with arguments: ['--data_path', '$input', '--learning_rate', '0.003', '--momentum', '0.92']
After variable expansion, calling script [ train.py ] with arguments: ['--data_path', '/tmp/tmp9kituvp3', '--learning_rate', '0.003', '--momentum', '0.92']

Script type = None
===== DATA =====
DATA PATH: /tmp/tmp9kituvp3
LIST FILES IN DATA PATH...
['cifar-10-batches-py', 'cifar-10-python.tar.gz']
```

Megjegyzčs

- A Azure Machine Learning automatikusan csatlakoztatta Blob Storage a számítási fürthöz.
- A ``dataset.as_named_input('input').as_mount()`` vezérlő parancsfájlban használt érték a csatlakoztatási pontra lesz feloldva.

> [!div class="nextstepaction"]
> [Megvizsgáltam a naplófájlt](?success=inspect-log#clean-up-resources) [, amely egy hibába ütközött](https://www.research.net/r/7C6W7BQ?issue=inspect-log)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [aml-delete-resource-group](../../includes/aml-delete-resource-group.md)]

Megtarthatja az erőforráscsoportot is, de törölhet egyetlen munkaterületet is. Jelenítse meg a munkaterület tulajdonságait, és válassza a **Törlés** lehetőséget.

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban láttuk, hogyan tölthetők fel adatok az Azure-ba a használatával `Datastore` . Az adattár Felhőbeli tárolóként szolgál a munkaterülethez, így megőrizheti az adatok megőrzésének állandó és rugalmas helyét.

Megismerte, hogyan módosíthatja a betanítási szkriptet az adatelérési út parancssoron keresztüli elfogadásához. A használatával `Dataset` egy könyvtárat csatlakoztathat a távoli futtatáshoz. 

Most, hogy már rendelkezik egy modellel, ismerkedjen meg a következőket:

* [Modellek üzembe helyezése Azure Machine learning](how-to-deploy-and-where.md)használatával.
