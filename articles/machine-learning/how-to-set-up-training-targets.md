---
title: Betanítási Futtatás konfigurálása
titleSuffix: Azure Machine Learning
description: A gépi tanulási modell tanítása különböző képzési környezetekben (számítási célok). Könnyedén válthat a képzési környezetek között.
services: machine-learning
author: sdgilley
ms.author: sgilley
ms.reviewer: sgilley
ms.service: machine-learning
ms.subservice: core
ms.date: 09/28/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python, contperf-fy21q1
ms.openlocfilehash: f38fe7d847754247f8c1510527b3ffe026c20be5
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102518500"
---
# <a name="configure-and-submit-training-runs"></a>Betanítási futtatások konfigurálása és elküldése

Ebből a cikkből megtudhatja, hogyan konfigurálhat és küldhet be Azure Machine Learning-futtatásokat a modellek betanításához. A kódrészletek a betanítási szkriptek konfigurációjának és beküldésének főbb részeit ismertetik.  Ezután használja az egyik [példa notebookot](#notebooks) a teljes körű működésre vonatkozó példák megtalálásához.

Ha betanítást végez, gyakori, hogy elindítsa a helyi számítógépen, majd később kibővíti a felhőalapú fürtöt. A Azure Machine Learning használatával különböző számítási célokból futtathat parancsfájlokat anélkül, hogy módosítani kellene a betanítási szkriptet.

Mindössze annyit kell tennie, hogy a **parancsfájl futtatási konfigurációján** belül minden számítási cél esetében meghatározza a környezetet.  Ha ezt követően egy másik számítási célra szeretné futtatni a betanítási kísérletet, adja meg az adott számítás futtatási konfigurációját.

## <a name="prerequisites"></a>Előfeltételek

* Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy ingyenes fiókot a virtuális gép létrehozásának megkezdése előtt. Próbálja ki a [Azure Machine learning ingyenes vagy fizetős verzióját](https://aka.ms/AMLFree) még ma
* A [Pythonhoz készült Azure Machine learning SDK](/python/api/overview/azure/ml/install) (>= 1.13.0)
* Egy [Azure Machine learning munkaterület](how-to-manage-workspace.md), `ws`
* Egy számítási cél `my_compute_target` .  [Számítási cél létrehozása](how-to-create-attach-compute-studio.md) 

## <a name="whats-a-script-run-configuration"></a><a name="whats-a-run-configuration"></a>Mi az a parancsfájl-futtatási konfiguráció?
A [ScriptRunConfig](/python/api/azureml-core/azureml.core.scriptrunconfig) használatával konfigurálhatja a betanítási futtatáshoz szükséges információkat a kísérlet részeként.

A betanítási kísérletet egy ScriptRunConfig-objektummal küldi el.  Ez az objektum az alábbiakat tartalmazza:

* **source_directory**: a betanítási parancsfájlt tartalmazó forrás könyvtára
* **parancsfájl**: a futtatandó betanítási szkript
* **compute_target**: a futtatandó számítási cél
* **környezet**: a parancsfájl futtatásakor használandó környezet
* és néhány további konfigurálható lehetőség is (további információt a [dokumentációban](/python/api/azureml-core/azureml.core.scriptrunconfig) talál)

## <a name="train-your-model"></a><a id="submit"></a>A modell betanítása

A betanítási futtatást elküldő kód mintája megegyezik a számítási célok összes típusával:

1. Kísérlet létrehozása a futtatáshoz
1. Hozzon létre egy környezetet, amelyben a parancsfájl futni fog
1. ScriptRunConfig létrehozása, amely meghatározza a számítási célt és a környezetet
1. A Futtatás elküldése
1. Várjon, amíg a Futtatás befejeződik

Vagy a következőket teheti:

* HyperDrive-Futtatás küldése a [hiperparaméter finomhangolásához](how-to-tune-hyperparameters.md).
* Kísérlet küldése a [vs Code bővítmény](tutorial-train-deploy-image-classification-model-vscode.md#train-the-model)használatával.

## <a name="create-an-experiment"></a>Kísérlet létrehozása

Hozzon létre egy kísérletet a munkaterületen.

```python
from azureml.core import Experiment

experiment_name = 'my_experiment'
experiment = Experiment(workspace=ws, name=experiment_name)
```

## <a name="select-a-compute-target"></a>Számítási cél kiválasztása

Válassza ki azt a számítási célt, amelyen a betanítási parancsfájl futni fog. Ha nincs megadva számítási cél a ScriptRunConfig, vagy ha az `compute_target='local'` Azure ml a parancsfájlt helyileg fogja végrehajtani. 

A cikkben szereplő mintakód azt feltételezi, hogy már létrehozott egy számítási célt `my_compute_target` az "Előfeltételek" szakaszból.

>[!Note]
>A Azure Databricks nem támogatott számítási célként a modell betanításához. Az adatelőkészítési és-telepítési feladatokhoz Azure Databricks is használhatja. 

## <a name="create-an-environment"></a>Környezet létrehozása
Azure Machine Learning [környezetek](concept-environments.md) a gépi tanulási képzést végző környezet beágyazását jelentik. Megadják a Python-csomagokat, a Docker-rendszerképet, a környezeti változókat és a szoftver beállításait a képzés és a pontozási szkriptek köré. Emellett a futtatókörnyezeteket (Python, Spark vagy Docker) is megadják.

Meghatározhatja saját környezetét, vagy használhat egy Azure ML-beli kiszervezett környezetet is. A válogatott [környezetek](./how-to-use-environments.md#use-a-curated-environment) előre definiált környezetek, amelyek alapértelmezés szerint a munkaterületen elérhetők. Ezeket a környezeteket a gyorsítótárazott Docker-rendszerképek alkotják, ami csökkenti a Futtatás előkészítési költségeit. A rendelkezésre álló, kitalált környezetek teljes listájáért tekintse meg [Azure Machine learning kurátori környezeteket](./resource-curated-environments.md) .

Távoli számítási cél esetén a következő népszerű kurátori környezetek egyikét használhatja a kezdéshez:

```python
from azureml.core import Workspace, Environment

ws = Workspace.from_config()
myenv = Environment.get(workspace=ws, name="AzureML-Minimal")
```

A környezetekkel kapcsolatos további információkért és részletekért lásd: [Create & Azure Machine learning-környezetek használata](how-to-use-environments.md).
  
### <a name="local-compute-target"></a><a name="local"></a>Helyi számítási cél

Ha a számítási cél a **helyi gép**, Ön felelős annak biztosításáért, hogy az összes szükséges csomag elérhető legyen a Python-környezetben, ahol a szkript fut.  A használatával az `python.user_managed_dependencies` aktuális Python-környezetet (vagy a Pythont a megadott elérési úton) használhatja.

```python
from azureml.core import Environment

myenv = Environment("user-managed-env")
myenv.python.user_managed_dependencies = True

# You can choose a specific Python environment by pointing to a Python path 
# myenv.python.interpreter_path = '/home/johndoe/miniconda3/envs/myenv/bin/python'
```

## <a name="create-the-script-run-configuration"></a>A parancsfájl futtatási konfigurációjának létrehozása

Most, hogy van egy számítási cél ( `my_compute_target` ) és egy környezet ( `myenv` ), hozzon létre egy parancsfájl-futtatási konfigurációt, amely futtatja a saját könyvtárában található képzési parancsfájlt ( `train.py` ) `project_folder` :

```python
from azureml.core import ScriptRunConfig

src = ScriptRunConfig(source_directory=project_folder,
                      script='train.py',
                      compute_target=my_compute_target,
                      environment=myenv)

# Set compute target
# Skip this if you are running on your local computer
script_run_config.run_config.target = my_compute_target
```

Ha nem ad meg környezetet, a rendszer létrehoz egy alapértelmezett környezetet az Ön számára.

Ha olyan parancssori argumentumokkal rendelkezik, amelyeket át szeretne adni a betanítási szkriptnek, akkor megadhatja azokat a **`arguments`** ScriptRunConfig konstruktor paraméterén keresztül, például: `arguments=['--arg1', arg1_val, '--arg2', arg2_val]` .

Ha szeretné felülbírálni a futtatáshoz engedélyezett alapértelmezett maximális időt, ezt a paraméterrel teheti meg **`max_run_duration_seconds`** . A rendszer megkísérli automatikusan megszakítani a futtatást, ha ez az érték hosszabb időt vesz igénybe.

### <a name="specify-a-distributed-job-configuration"></a>Elosztott feladatok konfigurációjának meghatározása
Ha elosztott betanítási feladatot szeretne futtatni, adja meg az elosztott feladatokhoz tartozó konfigurációt a **`distributed_job_config`** paraméterhez. A támogatott konfigurációs típusok a következők: [MpiConfiguration](/python/api/azureml-core/azureml.core.runconfig.mpiconfiguration), [TensorflowConfiguration](/python/api/azureml-core/azureml.core.runconfig.tensorflowconfiguration)és [PyTorchConfiguration](/python/api/azureml-core/azureml.core.runconfig.pytorchconfiguration). 

További információ és példák az elosztott Horovod, a TensorFlow és a PyTorch feladatok futtatásáról:

* [TensorFlow-modellek betanítása](./how-to-train-tensorflow.md#distributed-training)
* [PyTorch-modellek betanítása](./how-to-train-pytorch.md#distributed-training)

## <a name="submit-the-experiment"></a>A kísérlet elküldése

```python
run = experiment.submit(config=src)
run.wait_for_completion(show_output=True)
```

> [!IMPORTANT]
> A betanítási Futtatás elküldésekor létrejön a betanítási parancsfájlokat tartalmazó könyvtár pillanatképe, amelyet a rendszer elküld a számítási célra. A munkaterületen található kísérlet részeként is tárolja. Ha módosítja a fájlokat, és újra elküldi a futtatást, csak a módosított fájlok lesznek feltöltve.
>
> [!INCLUDE [amlinclude-info](../../includes/machine-learning-amlignore-gitignore.md)]
> 
> További információ a pillanatképekről: [Pillanatképek](concept-azure-machine-learning-architecture.md#snapshots).

> [!IMPORTANT]
> **Speciális mappák** Két mappa, *kimenet* és *napló*, a Azure Machine learning speciális kezelést kap. Ha a betanítás során a rendszer a gyökérkönyvtárhoz viszonyított *kimeneteket* és *naplókat* tartalmazó mappákba ír fájlokat `./outputs` , `./logs` a fájlok automatikusan feltöltve lesznek a futtatási előzményekbe, hogy a Futtatás befejezése után hozzáférhessenek hozzájuk.
>
> Az összetevőknek a betanítás során történő létrehozásához (például a Model Files, az ellenőrzőpontok, az adatfájlok vagy a képek kirajzolása) írja ezeket a `./outputs` mappába.
>
> Hasonlóképpen bármilyen naplót írhat a betanításból a `./logs` mappába. Azure Machine Learning [TensorBoard-integrációjának](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/track-and-monitor-experiments/tensorboard/export-run-history-to-tensorboard/export-run-history-to-tensorboard.ipynb) kihasználása érdekében ügyeljen arra, hogy a TensorBoard-naplókat a mappába írja. Amíg a Futtatás folyamatban van, el tudja indítani a TensorBoard, és továbbíthatja ezeket a naplókat.  Később is visszaállíthatja a naplókat az előző futtatások bármelyikéről.
>
> Ha például le szeretné tölteni a *kimenet* mappába a helyi gépre írt fájlt a távoli képzés futtatása után: `run.download_file(name='outputs/my_output_file', output_file_path='my_destination_path')`

## <a name="git-tracking-and-integration"></a><a id="gitintegration"></a>Git-követés és-integráció

Ha olyan képzést indít el, ahol a forrás könyvtára helyi git-tárház, a rendszer a tárház adatait a futtatási előzményekben tárolja. További információ: git- [integráció Azure Machine Learninghoz](concept-train-model-git-integration.md).

## <a name="notebook-examples"></a><a name="notebooks"></a>Jegyzetfüzet-példák

Tekintse meg ezeket a jegyzetfüzeteket a futtatások konfigurálására példákat a különböző képzési forgatókönyvekhez:
* [Képzés különböző számítási célokból](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training)
* [Képzés ML-keretrendszerekkel](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/ml-frameworks)
* [oktatóanyagok/IMG-Classification-part1-Training. ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/tutorials/image-classification-mnist-data/img-classification-part1-training.ipynb)

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-clone-for-examples.md)]

## <a name="troubleshooting"></a>Hibaelhárítás

* **A Futtatás sikertelen `jwt.exceptions.DecodeError` a** következővel: pontos hibaüzenet: `jwt.exceptions.DecodeError: It is required that you pass in a value for the "algorithms" argument when calling decode()` . 
    
    Érdemes lehet a azureml-Core legújabb verziójára frissíteni: `pip install -U azureml-core` .
    
    Ha a problémát helyi futtatások esetén is futtatja, ellenőrizze a környezetében telepített PyJWT verzióját, ahol a futtatást elindítja. A PyJWT támogatott verziói < 2.0.0. Távolítsa el a PyJWT a környezetből, ha a verzió >= 2.0.0. A PyJWT verzióját a következőképpen tekintheti meg, távolíthatja el és telepítheti a megfelelő verziót:
    1. Indítsa el a parancssort, aktiválja a Conda környezetet, ahol a azureml-Core telepítve van.
    2. Adja meg `pip freeze` és keresse meg `PyJWT` , ha található, a felsorolt verziónak < 2.0.0 kell lennie
    3. Ha a felsorolt verzió nem támogatott verziójú, a `pip uninstall PyJWT` parancs-rendszerhéjban írja be az y értéket a megerősítéshez.
    4. Végezze el a telepítést a `pip install 'PyJWT<2.0.0'` paranccsal
    
    Ha a futtatásával felhasználó által létrehozott környezetet küld, érdemes lehet a azureml-Core legújabb verzióját használni ebben a környezetben. Verziók >= a azureml-Core 1.18.0 már PyJWT < 2.0.0. Ha a azureml-Core < 1.18.0 verzióját kell használnia a beküldött környezetben, ügyeljen arra, hogy a PyJWT < 2.0.0 a pip-függőségekben.


 * **ModuleErrors (nincs nevű modul)**: Ha a ModuleErrors-ben fut a kísérletek Azure ml-ben való elküldése közben, a betanítási szkript egy telepítendő csomagot vár, de nem adja hozzá. A csomag nevének megadása után az Azure ML telepíti a csomagot a betanítási futtatáshoz használt környezetben.

    Ha a becslések-t használja a kísérletek elküldéséhez, megadhatja a csomag nevét `pip_packages` `conda_packages` a kalkulátoron keresztül vagy paraméterrel, attól függően, hogy melyik forrásból szeretné telepíteni a csomagot. Egy YML-fájlt is megadhat az összes függőségének használatával, `conda_dependencies_file` vagy listázhatja az összes pip-követelményét egy txt-fájlban a `pip_requirements_file` paraméter használatával. Ha rendelkezik saját Azure ML-környezetbeli objektummal, amellyel felül szeretné bírálni a kalkulátor által használt alapértelmezett rendszerképet, megadhatja ezt a környezetet a `environment` kalkulátor konstruktorának paraméterén keresztül.
    
    Az Azure ML által karbantartott Docker-rendszerképek és azok tartalma [AzureML-tárolókban](https://github.com/Azure/AzureML-Containers)láthatók.
    A keretrendszer-specifikus függőségek a vonatkozó keretrendszer dokumentációjában találhatók:
    *  [Chainer](/python/api/azureml-train-core/azureml.train.dnn.chainer#remarks)
    * [PyTorch](/python/api/azureml-train-core/azureml.train.dnn.pytorch#remarks)
    * [TensorFlow](/python/api/azureml-train-core/azureml.train.dnn.tensorflow#remarks)
    *  [SKLearn](/python/api/azureml-train-core/azureml.train.sklearn.sklearn#remarks)
    
    > [!Note]
    > Ha úgy gondolja, hogy egy adott csomag elég gyakori ahhoz, hogy hozzá lehessen adni az Azure ML karbantartott lemezképekhez és környezetekhez, hozzon létre GitHub-problémát a [AzureML-tárolókban](https://github.com/Azure/AzureML-Containers). 
 
* **NameError (név nincs meghatározva), AttributeError (objektum nem rendelkezik attribútummal)**: Ez a kivétel a betanítási szkriptből származik. A naplófájlokat a Azure Portalból tekintheti meg, ha további információt szeretne kapni a nem definiált névvel vagy az attribútum hibával kapcsolatban. Az SDK segítségével `run.get_details()` megtekintheti a hibaüzenetet. Ekkor a rendszer a futtatáshoz létrehozott összes naplófájlt is felsorolja. Győződjön meg arról, hogy megtekinti a betanítási szkriptet, és javítsa ki a hibát, mielőtt elküldené a futtatást. 


* A **Run vagy a Experiment művelet törlése**: a kísérletek a [kísérlet. Archive](/python/api/azureml-core/azureml.core.experiment%28class%29#archive--) metódussal vagy a Azure Machine learning Studio-ügyfél kísérlet lapjának az "Archive Experiment" gomb használatával is archiválható. Ez a művelet elrejti a kísérletet a lekérdezések és nézetek listájában, de nem törli azt.

    Az egyes kísérletek vagy futtatások végleges törlése jelenleg nem támogatott. További információ a munkaterület-eszközök törléséről: [Machine learning szolgáltatás-munkaterület adatainak exportálása vagy törlése](how-to-export-delete-data.md).

* A **metrikai dokumentum túl nagy**: a Azure Machine learning belső korláttal rendelkezik azon metrikai objektumok méretén, amelyek bejelentkezhetnek a betanítási futtatás során. Ha „a metrikadokumentum túl nagy” hibát észlel egy értéklistát tartalmazó metrika naplózásakor, próbálja meg felosztani a listát kisebb tömbökre, például:

    ```python
    run.log_list("my metric name", my_metric[:N])
    run.log_list("my metric name", my_metric[N:])
    ```

    Az Azure ML belsőleg egy összefüggő listává fűzi össze az ugyanazzal a metrikanévvel rendelkező tömböket.

* A **számítási cél elkezdése hosszú időt vesz igénybe**: a számítási célokhoz tartozó Docker-rendszerképek betöltődik Azure Container Registryból (ACR). Alapértelmezés szerint a Azure Machine Learning létrehoz egy ACR-t, *amely az alapszintű* szolgáltatási szintet használja. A munkaterületre vonatkozó ACR a standard vagy a prémium szintre való módosítása csökkentheti a lemezképek létrehozásához és betöltéséhez szükséges időt. További információ: [Azure Container Registry szolgáltatási szintek](../container-registry/container-registry-skus.md).

## <a name="next-steps"></a>Következő lépések

* [Oktatóanyag: a betanítási modell](tutorial-train-models-with-aml.md) felügyelt számítási célt használ a modellek betanításához.
* Megtudhatja, hogyan taníthat modelleket konkrét ML-keretrendszerekkel, például a [Scikit-Learn](how-to-train-scikit-learn.md), a [TensorFlow](how-to-train-tensorflow.md)és a [PyTorch](how-to-train-pytorch.md).
* Ismerje meg, hogy miként lehet [hatékonyan hangolni a hiperparaméterek beállítása](how-to-tune-hyperparameters.md) a jobb modellek létrehozásához.
* A betanított modellel megtudhatja, [Hogyan és hol helyezheti üzembe a modelleket](how-to-deploy-and-where.md).
* Tekintse meg a [ScriptRunConfig Class](/python/api/azureml-core/azureml.core.scriptrunconfig) SDK-referenciát.
* [Azure Machine Learning használata az Azure Virtual Networks használatával](./how-to-network-security-overview.md)
