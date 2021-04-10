---
title: 'Oktatóanyag: az első Machine learning-modell betanítása – Python'
titleSuffix: Azure Machine Learning
description: A Azure Machine Learning Get-Started sorozat 3. része azt mutatja be, hogyan lehet betanítani a Machine learning-modellt.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: aminsaied
ms.author: amsaied
ms.reviewer: sgilley
ms.date: 02/11/2021
ms.custom: devx-track-python, contperf-fy21q3
ms.openlocfilehash: 7e035406ce220728c316aaa2005e0f4d6089b5e2
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105935515"
---
# <a name="tutorial-train-your-first-machine-learning-model-part-3-of-4"></a>Oktatóanyag: az első gépi tanulási modell betanítása (4. rész)

Ez az oktatóanyag bemutatja, hogyan végezheti el a gépi tanulási modellek betanítását Azure Machine Learning.

Ez az oktatóanyag *egy négy részből álló oktatóanyag-Sorozat 3. része* , amelyben megismerheti a Azure Machine learning és az Azure-ban végzett feladatok-alapú gépi tanulási feladatok alapjait. Ez az oktatóanyag az [1. rész: beállítás](tutorial-1st-experiment-sdk-setup-local.md) és a 2. rész: a ["Helló világ!" futtatása](tutorial-1st-experiment-hello-world.md) során végzett munkára épül. az adatsorozatból.

Ebben az oktatóanyagban a következő lépést kell elvégeznie egy gépi tanulási modellt bemutató parancsfájl elküldésével. Ez a példa segít megérteni, hogyan könnyíti meg a Azure Machine Learning a helyi hibakeresés és a távoli futtatások közötti konzisztens viselkedést.

Az oktatóanyag során az alábbi lépéseket fogja végrehajtani:

> [!div class="checklist"]
> * Hozzon létre egy képzési parancsfájlt.
> * Azure Machine Learning-környezet definiálásához használja a Conda.
> * Hozzon létre egy vezérlő parancsfájlt.
> * Azure Machine Learning osztályok ( `Environment` ,,) megismerése `Run` `Metrics` .
> * A betanítási szkript elküldése és futtatása.
> * A kód kimenetének megtekintése a felhőben.
> * Azure Machine Learningra vonatkozó naplózási mérőszámok.
> * Megtekintheti a mérőszámokat a felhőben.

## <a name="prerequisites"></a>Előfeltételek

- [Anaconda](https://www.anaconda.com/download/) vagy [Miniconda](https://www.anaconda.com/download/) a Python virtuális környezetek kezeléséhez és csomagok telepítéséhez.
- A [part1](tutorial-1st-experiment-sdk-setup-local.md) és a sorozat [2. részének](tutorial-1st-experiment-hello-world.md) befejezése.

## <a name="create-training-scripts"></a>Képzési parancsfájlok létrehozása

Először meg kell határoznia a neurális hálózati architektúrát egy `model.py` fájlban. Az összes betanítási kód az `src` alkönyvtárba kerül, beleértve a következőket is: `model.py` .

[Ebből a bevezető példából](https://pytorch.org/tutorials/beginner/blitz/cifar10_tutorial.html) a PyTorch a következő kódot veszi figyelembe. Vegye figyelembe, hogy a Azure Machine Learning fogalmak minden gépi tanulási kódra érvényesek, nem csak a PyTorch.

:::code language="python" source="~/MachineLearningNotebooks/tutorials/get-started-day1/IDE-users/src/model.py":::

Ezután adja meg a betanítási parancsfájlt. Ez a parancsfájl a PyTorch API-k használatával tölti le a CIFAR10-adatkészletet `torchvision.dataset` , beállítja a hálózatban definiált hálózatot, és két időpontra, a `model.py` standard SGD és a több entrópia közötti adatvesztés használatával hajtja majd be.

Hozzon létre egy `train.py` parancsfájlt az `src` alkönyvtárban:

:::code language="python" source="~/MachineLearningNotebooks/tutorials/get-started-day1/IDE-users/src/train.py":::

Most már rendelkezik a következő címtár-struktúrával:

:::image type="content" source="media/tutorial-1st-experiment-sdk-train/directory-structure.png" alt-text="A címtár szerkezete a train.py mutatja az src alkönyvtárában":::


> [!div class="nextstepaction"]
> [Létrehoztam a betanítási szkripteket](?success=create-scripts#environment) [, amelyekkel egy probléma lépett](https://www.research.net/r/7CTJQQN?issue=create-scripts) fel

## <a name="create-a-new-python-environment"></a><a name="environment"></a> Új Python-környezet létrehozása

Hozzon létre egy nevű fájlt `pytorch-env.yml` a `.azureml` rejtett könyvtárban:

:::code language="yml" source="~/MachineLearningNotebooks/tutorials/get-started-day1/IDE-users/environments/pytorch-env.yml":::

Ez a környezet minden, a modellhez és a képzési parancsfájlhoz szükséges függőséget tartalmaz. Figyelje meg, hogy nincs függőség a Pythonhoz készült Azure Machine Learning SDK-val.

> [!div class="nextstepaction"]
> [Létrehoztam a környezeti fájlt](?success=create-env-file#test-local) [, amelyet egy hibába ütközött](https://www.research.net/r/7CTJQQN?issue=create-env-file)

## <a name="test-locally"></a><a name="test-local"></a> Helyi tesztelés

Egy terminál-vagy anaconda-parancssorban használja a következő kódot a szkript helyi teszteléséhez az új környezetben.  

```bash
conda deactivate                                # If you are still using the tutorial environment, exit it
conda env create -f .azureml/pytorch-env.yml    # create the new Conda environment
conda activate pytorch-env                      # activate new Conda environment
python src/train.py                             # train model
```

A parancsfájl futtatása után a rendszer egy nevű könyvtárba letölti az adatletöltést `tutorial/data` .

> [!div class="nextstepaction"]
> [A kód helyi futtatása a](?success=test-local#create-local) [probléma megoldásával történt](https://www.research.net/r/7CTJQQN?issue=test-local)

## <a name="create-the-control-script"></a><a name="create-local"></a> A vezérlő parancsfájl létrehozása

A következő vezérlő szkript és a "Hello World!" küldéséhez használt különbség azt is megteheti, hogy hozzáad egy pár extra sort a környezet beállításához.

Hozzon létre egy új Python-fájlt a (z) `tutorial` nevű könyvtárban `04-run-pytorch.py` :

```python
# 04-run-pytorch.py
from azureml.core import Workspace
from azureml.core import Experiment
from azureml.core import Environment
from azureml.core import ScriptRunConfig

if __name__ == "__main__":
    ws = Workspace.from_config()
    experiment = Experiment(workspace=ws, name='day1-experiment-train')
    config = ScriptRunConfig(source_directory='./src',
                             script='train.py',
                             compute_target='cpu-cluster')

    # set up pytorch environment
    env = Environment.from_conda_specification(
        name='pytorch-env',
        file_path='./.azureml/pytorch-env.yml'
    )
    config.run_config.environment = env

    run = experiment.submit(config)

    aml_url = run.get_portal_url()
    print(aml_url)
```    
    
### <a name="understand-the-code-changes"></a>A kód módosításainak megismerése

:::row:::
   :::column span="":::
      `env = ...`
   :::column-end:::
   :::column span="2":::
      Azure Machine Learning egy olyan [környezet](/python/api/azureml-core/azureml.core.environment.environment) koncepcióját mutatja be, amely egy reprodukálható, verziószámmal rendelkező Python-környezetet jelöl a kísérletek futtatásához. Könnyen létrehozhat környezetet helyi Conda vagy pip-környezetből.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      `config.run_config.environment = env`
   :::column-end:::
   :::column span="2":::
      Hozzáadja a környezetet a [ScriptRunConfig](/python/api/azureml-core/azureml.core.scriptrunconfig)-hez.
   :::column-end:::
:::row-end:::

> [!div class="nextstepaction"]
> [Létrehoztam a vezérlési parancsfájlt](?success=control-script#submit) [, amely egy hibába ütközött](https://www.research.net/r/7CTJQQN?issue=control-script)


## <a name="submit-the-run-to-azure-machine-learning"></a><a name="submit"></a> A Futtatás beküldése Azure Machine Learningre

Váltson vissza arra az *oktatóanyag* -környezetre, amelyen telepítve van a Azure Machine learning SDK for Python. Mivel a betanítási kód nem fut a számítógépen, nincs szükség a PyTorch telepítésére.  Erre azonban szüksége van `azureml-sdk` , amely az *oktatóanyag* környezetében található.

```bash
conda deactivate
conda activate tutorial
python 04-run-pytorch.py
```

>[!NOTE] 
> Amikor először futtatja ezt a parancsfájlt, Azure Machine Learning egy új Docker-rendszerképet hoz létre a PyTorch-környezetből. A teljes Futtatás akár 5 – 10 percet is igénybe vehet. 
>
> A Docker-Build naplóit a Azure Machine Learning Studióban tekintheti meg. Kövesse a studióra mutató hivatkozást, válassza a **kimenetek + naplók** lapot, majd válassza a lehetőséget `20_image_build_log.txt` .
>
> A rendszer ezt a rendszerképet újra felhasználja a későbbi futtatásokban, hogy azok sokkal gyorsabban fussanak.

A rendszerkép felépítése után válassza ki a `70_driver_log.txt` betanítási szkript kimenetét.

```txt
Downloading https://www.cs.toronto.edu/~kriz/cifar-10-python.tar.gz to ./data/cifar-10-python.tar.gz
...
Files already downloaded and verified
epoch=1, batch= 2000: loss 2.19
epoch=1, batch= 4000: loss 1.82
epoch=1, batch= 6000: loss 1.66
epoch=1, batch= 8000: loss 1.58
epoch=1, batch=10000: loss 1.52
epoch=1, batch=12000: loss 1.47
epoch=2, batch= 2000: loss 1.39
epoch=2, batch= 4000: loss 1.38
epoch=2, batch= 6000: loss 1.37
epoch=2, batch= 8000: loss 1.33
epoch=2, batch=10000: loss 1.31
epoch=2, batch=12000: loss 1.27
Finished Training
```

> [!WARNING]
> Ha hibaüzenet jelenik meg `Your total snapshot size exceeds the limit` , a `data` könyvtár a `source_directory` ben használt értékben található `ScriptRunConfig` .
>
> Áthelyezés `data` kívülre `src` .

A környezetek regisztrálhatók egy munkaterületre a használatával `env.register(ws)` . Ezt követően könnyen megoszthatók, újra felhasználhatók és verziószámozást végeznek. A környezetek megkönnyítik a korábbi eredmények újbóli előállítását és a csapattal való együttműködést.

Azure Machine Learning is karbantartja a kurátori környezetek gyűjteményét. Ezek a környezetek a gyakori gépi tanulási forgatókönyvekre vonatkoznak, és a gyorsítótárazott Docker-rendszerképekből állnak. A gyorsítótárazott Docker-rendszerképek teszik lehetővé az első távoli futtatást.

Röviden, a regisztrált környezetek használatával időt takaríthat meg! További információért olvassa el [a környezetek használatát](./how-to-use-environments.md) ismertető témakört.

> [!div class="nextstepaction"]
> [Elküldtem a futtatást](?success=test-w-environment#log) [egy hibával](https://www.research.net/r/7CTJQQN?issue=test-w-environment)

## <a name="log-training-metrics"></a><a name="log"></a> A betanítási mérőszámok naplózása

Most, hogy már rendelkezik a modellekkel kapcsolatos képzéssel Azure Machine Learningban, kezdje nyomon követni a teljesítmény mérőszámait.

Az aktuális betanítási szkript mérőszámokat nyomtat a terminálra. Azure Machine Learning a metrikák naplózására szolgáló mechanizmust biztosít a további funkciókkal. Néhány sornyi kód hozzáadásával lehetősége nyílik a mérőszámok megjelenítésére a Studióban, illetve a metrikák összehasonlítására több Futtatás között.

### <a name="modify-trainpy-to-include-logging"></a>Módosítás `train.py` a naplózás belefoglalásához

Módosítsa a `train.py` parancsfájlt úgy, hogy a kód két további sorát tartalmazza:

:::code language="python" source="~/MachineLearningNotebooks/tutorials/get-started-day1/code/pytorch-cifar10-train-with-logging/train.py":::


#### <a name="understand-the-additional-two-lines-of-code"></a>A kód további két sorának megismerése

A alkalmazásban a `train.py` metódus használatával érheti el  a Run objektumot a betanítási parancsfájlból, `Run.get_context()` és használhatja azt a metrikák naplózásához:

```python
# in train.py
run = Run.get_context()

...

run.log('loss', loss)
```

A Azure Machine Learning metrikái a következők:

- A kísérlet és a Futtatás alapján rendezi, így könnyen nyomon követheti és összehasonlíthatja a metrikákat.
- Egy felhasználói felülettel rendelkezik, így megjelenítheti a képzések teljesítményét a Studióban.
- Úgy tervezték, hogy méretezhető legyen, így Ön is megőrizheti ezeket az előnyöket, még akkor is, ha több száz kísérletet futtat.

> [!div class="nextstepaction"]
> [Módosítottam Train.py ](?success=modify-train#log) [egy probléma](https://www.research.net/r/7CTJQQN?issue=modify-train)

### <a name="update-the-conda-environment-file"></a>A Conda-környezet fájljának frissítése

A `train.py` szkript csak új függőséget vett igénybe `azureml.core` . A `pytorch-env.yml` módosítást tükröző frissítés:

:::code language="python" source="~/MachineLearningNotebooks/tutorials/get-started-day1/configuration/pytorch-aml-env.yml":::

> [!div class="nextstepaction"]
> [Frissítettem a](?success=update-environment#submit-again) [probléma állapotba helyezett](https://www.research.net/r/7CTJQQN?issue=update-environment) környezeti fájlt

### <a name="submit-the-run-to-azure-machine-learning"></a><a name="submit-again"></a> A Futtatás beküldése Azure Machine Learningre
A szkript elküldése még egyszer:

```bash
python 04-run-pytorch.py
```

Ez alkalommal, amikor felkeresi a stúdiót, ugorjon a **metrikák** lapra, ahol mostantól a modell betanítása lehetőségre kattintva megtekintheti az élő frissítéseket.

:::image type="content" source="media/tutorial-1st-experiment-sdk-train/logging-metrics.png" alt-text="A metrikák lapon megjelenő adatvesztési gráf":::

> [!div class="nextstepaction"]
> [Újraküldöttem a futtatást](?success=resubmit-with-logging#next-steps) [egy hibába](https://www.research.net/r/7CTJQQN?issue=resubmit-with-logging)

## <a name="next-steps"></a>Következő lépések

Ebben a munkamenetben egy alapszintű "Helló világból" frissítünk. parancsfájlt egy reálisabb betanítási parancsfájlhoz, amely egy adott Python-környezet futtatásához szükséges. Megismerte, hogyan végezheti el a helyi Conda-környezetet a felhőben Azure Machine Learning környezettel. Végül azt is láttuk, hogy a kódok néhány sorában hogyan naplózhatja a mérőszámokat Azure Machine Learningba.

Azure Machine Learning környezetek is létrehozhatók, például [egy pip requirements.txt](/python/api/azureml-core/azureml.core.environment.environment#from-pip-requirements-name--file-path-) fájlból vagy [egy meglévő helyi Conda-környezetből](/python/api/azureml-core/azureml.core.environment.environment#from-existing-conda-environment-name--conda-environment-name-).

A következő munkamenetben láthatja, hogyan dolgozhat a Azure Machine Learningban lévő adatokat, ha feltölti a CIFAR10-adatkészletet az Azure-ba.

> [!div class="nextstepaction"]
> [Oktatóanyag: saját adatbevitel](tutorial-1st-experiment-bring-data.md)

>[!NOTE] 
> Ha itt szeretné befejezni az oktatóanyag-sorozatot, és nem halad a következő lépéssel, ne felejtse el [törölni az erőforrásokat](tutorial-1st-experiment-bring-data.md#clean-up-resources).
