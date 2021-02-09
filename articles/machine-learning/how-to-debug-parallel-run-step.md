---
title: A ParallelRunStep hibaelhárítása
titleSuffix: Azure Machine Learning
description: Tippek a gépi tanulási folyamatokban a ParallelRunStep használatával kapcsolatos hibák elhárításához.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: troubleshooting
ms.custom: troubleshooting
ms.reviewer: larryfr, vaidyas, laobri, tracych
ms.author: trmccorm
author: tmccrmck
ms.date: 09/23/2020
ms.openlocfilehash: a0f813253520d76731a9b49a89b0bcace7c2ef34
ms.sourcegitcommit: 706e7d3eaa27f242312d3d8e3ff072d2ae685956
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/09/2021
ms.locfileid: "99979164"
---
# <a name="troubleshooting-the-parallelrunstep"></a>A ParallelRunStep hibaelhárítása

Ebből a cikkből megtudhatja, hogyan végezheti el a hibaelhárítást, ha a [ParallelRunStep](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.parallel_run_step.parallelrunstep?preserve-view=true&view=azure-ml-py) osztályt használja a [Azure Machine learning SDK](/python/api/overview/azure/ml/intro?preserve-view=true&view=azure-ml-py)-ból.

A folyamatok hibaelhárításával kapcsolatos általános tippekért lásd: [gépi tanulási folyamatok hibaelhárítása](how-to-debug-pipelines.md).

## <a name="testing-scripts-locally"></a>Parancsfájlok helyi tesztelése

 A ParallelRunStep a ML-folyamatok lépéseit futtatja. Érdemes lehet a [parancsfájlokat helyileg tesztelni](how-to-debug-visual-studio-code.md#debug-and-troubleshoot-machine-learning-pipelines) első lépésként.

##  <a name="script-requirements"></a>Parancsfájlokra vonatkozó követelmények

Az a parancsfájlnak `ParallelRunStep` két függvényt *kell tartalmaznia* :
- `init()`: Használja ezt a funkciót bármilyen költséges vagy közös felkészüléshez a későbbi következtetésekhez. Használhatja például a modell betöltését egy globális objektumba. Ezt a függvényt a rendszer csak egyszer hívja meg a folyamat elején.
-  `run(mini_batch)`: A függvény minden `mini_batch` példánynál futni fog.
    -  `mini_batch`: `ParallelRunStep` meghívja a Run metódust, és egy listát vagy `DataFrame` a pandák argumentumot ad át a metódusnak. A mini_batch minden bejegyzése egy fájl elérési útja lesz, ha a bemenet `FileDataset` vagy egy Panda, `DataFrame` Ha a bemenet a `TabularDataset` .
    -  `response`: a Run () metódusnak egy pandák `DataFrame` vagy egy tömböt kell visszaadnia. Append_row output_action esetében ezek a visszaadott elemek a közös kimeneti fájlba vannak hozzáfűzve. Summary_only esetén a rendszer figyelmen kívül hagyja az elemek tartalmát. Az összes kimeneti művelet esetében minden visszaadott kimeneti elem a bemeneti elem egy sikeres futtatását jelzi a bemenet mini-batchben. Győződjön meg arról, hogy a Futtatás eredményében elegendő adat szerepel a kimenet leképezése a kimeneti eredmény futtatásához. A futtatási kimenet a kimeneti fájlban lesz megírva, és nem garantált, hogy sorrendben legyenek, a kimenetben lévő egyes kulcsokat kell használnia a bemenethez való leképezéshez.

```python
%%writefile digit_identification.py
# Snippets from a sample script.
# Refer to the accompanying digit_identification.py
# (https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/machine-learning-pipelines/parallel-run)
# for the implementation script.

import os
import numpy as np
import tensorflow as tf
from PIL import Image
from azureml.core import Model


def init():
    global g_tf_sess

    # Pull down the model from the workspace
    model_path = Model.get_model_path("mnist")

    # Construct a graph to execute
    tf.reset_default_graph()
    saver = tf.train.import_meta_graph(os.path.join(model_path, 'mnist-tf.model.meta'))
    g_tf_sess = tf.Session()
    saver.restore(g_tf_sess, os.path.join(model_path, 'mnist-tf.model'))


def run(mini_batch):
    print(f'run method start: {__file__}, run({mini_batch})')
    resultList = []
    in_tensor = g_tf_sess.graph.get_tensor_by_name("network/X:0")
    output = g_tf_sess.graph.get_tensor_by_name("network/output/MatMul:0")

    for image in mini_batch:
        # Prepare each image
        data = Image.open(image)
        np_im = np.array(data).reshape((1, 784))
        # Perform inference
        inference_result = output.eval(feed_dict={in_tensor: np_im}, session=g_tf_sess)
        # Find the best probability, and add it to the result list
        best_result = np.argmax(inference_result)
        resultList.append("{}: {}".format(os.path.basename(image), best_result))

    return resultList
```

Ha egy másik fájl vagy mappa található a következtetési parancsfájllal megegyező könyvtárban, hivatkozhat rá az aktuális munkakönyvtár megkeresésével.

```python
script_dir = os.path.realpath(os.path.join(__file__, '..',))
file_path = os.path.join(script_dir, "<file_name>")
```

### <a name="parameters-for-parallelrunconfig"></a>A ParallelRunConfig paraméterei

`ParallelRunConfig` a `ParallelRunStep` Azure Machine learning folyamaton belüli példány fő konfigurációja. Ezzel a paranccsal becsomagolhatja a parancsfájlt, és konfigurálhatja a szükséges paramétereket, beleértve az alábbi bejegyzéseket:
- `entry_script`: Egy felhasználói parancsfájl helyi fájl elérési útjaként, amely több csomóponton párhuzamosan fog futni. Ha `source_directory` van ilyen, használjon relatív elérési utat. Ellenkező esetben használja a gépen elérhető bármely elérési utat.
- `mini_batch_size`: A mini-batch egyetlen hívásnak átadott mérete `run()` . (nem kötelező; az alapértelmezett érték a és a rendszerhez tartozó `10` fájlok `FileDataset` `1MB` `TabularDataset` .)
    - A esetében `FileDataset` a minimális értékkel rendelkező fájlok száma `1` . Több fájlt is egyesítheti egyetlen mini-kötegbe.
    - A esetében `TabularDataset` a mérete az adatmennyiség. Az értékek például a következők:,, `1024` `1024KB` `10MB` és `1GB` . A javasolt érték: `1MB` . A mini-batch-ból `TabularDataset` soha nem lesz keresztben a fájl határa. Ha például. csv fájlokkal rendelkezik, amelyek különböző méretűek, a legkisebb fájl 100 KB, a legnagyobb pedig 10 MB. Ha be van állítva `mini_batch_size = 1MB` , akkor az 1 MB-nál kisebb méretű fájlok egyetlen mini batch-ként lesznek kezelve. Az 1 MB-nál nagyobb méretű fájlok több mini-kötegre lesznek felosztva.
- `error_threshold`: A rendszer figyelmen kívül hagyja a hibák számát a (z `TabularDataset` ) és a (z) esetében a `FileDataset` feldolgozás során. Ha a teljes bemenethez tartozó hibák száma meghaladja ezt az értéket, a rendszer megszakítja a feladatot. A hiba küszöbértéke a teljes bemenetre vonatkozik, nem a metódusnak eljuttatott egyes mini-batch esetében `run()` . A tartomány `[-1, int.max]` . A `-1` rész azt jelzi, hogy a rendszer figyelmen kívül hagyja az összes hibát a feldolgozás során.
- `output_action`: Az alábbi értékek egyike azt jelzi, hogyan lesz rendszerezve a kimenet:
    - `summary_only`: A felhasználói parancsfájl a kimenetet fogja tárolni. `ParallelRunStep` a csak a hiba küszöbértékének kiszámításához használja a kimenetet.
    - `append_row`: Minden bemenet esetében csak egy fájl lesz létrehozva a kimeneti mappában, hogy az összes kimenetet sor szerint elválasztva fűzze hozzá.
- `append_row_file_name`: Append_row output_action kimeneti fájljának testreszabása (nem kötelező; az alapértelmezett érték `parallel_run_step.txt` ).
- `source_directory`: A számítási célra végrehajtandó összes fájlt tartalmazó mappák elérési útja (nem kötelező).
- `compute_target`: Csak `AmlCompute` a támogatott.
- `node_count`: A felhasználói parancsfájl futtatásához használandó számítási csomópontok száma.
- `process_count_per_node`: A folyamatok száma egy csomóponton. Az ajánlott eljárás az, ha a GPU vagy a CPU egyik csomópontjának száma (opcionális; alapértelmezett érték) van beállítva `1` .
- `environment`: A Python-környezet definíciója. Beállíthatja egy meglévő Python-környezet használatára vagy egy ideiglenes környezet beállítására. A definíció a szükséges alkalmazás-függőségek (opcionális) beállítására is felelős.
- `logging_level`: Naplózási részletesség. A részletességgel bővülő értékek a következők: `WARNING` , `INFO` és `DEBUG` . (nem kötelező; az alapértelmezett érték: `INFO` )
- `run_invocation_timeout`: A `run()` metódus meghívásának időtúllépése másodpercben. (nem kötelező; az alapértelmezett érték: `60` )
- `run_max_try`: Maximális számú próbálkozás a `run()` mini batch számára. A nem `run()` sikerült, ha kivétel keletkezik, vagy ha a rendszer nem ad vissza semmit, ha `run_invocation_timeout` a szolgáltatás elérte az értéket (opcionális; az alapértelmezett érték `3` ). 

A (z),,,, és as értéket megadhatja, `mini_batch_size` `node_count` hogy a `process_count_per_node` `logging_level` `run_invocation_timeout` `run_max_try` `PipelineParameter` folyamat futásának újraküldésekor a paraméterek értékei finomhangolása megtörténjen. Ebben a példában a `PipelineParameter` és a és a () `mini_batch_size` értéket fogja használni, `Process_count_per_node` Ha később újra elküld egy futtatást. 

### <a name="parameters-for-creating-the-parallelrunstep"></a>A ParallelRunStep létrehozásához szükséges paraméterek

Hozza létre a ParallelRunStep a parancsfájl, a környezeti konfiguráció és a paraméterek használatával. Adja meg azt a számítási célt, amely már a munkaterülethez van csatolva a következtetési parancsfájl végrehajtásának céljaként. `ParallelRunStep`A paranccsal hozhatja létre a Batch-következtetési folyamat lépését, amely a következő paramétereket veszi figyelembe:
- `name`: A lépés neve a következő elnevezési korlátozásokkal: Unique, 3-32 karakter, és regex ^ \[ a-z \] ([-a-Z0-9] * [a-Z0-9])? $.
- `parallel_run_config`: A `ParallelRunConfig` korábban definiált objektum.
- `inputs`: Egy vagy több, egybeírt Azure Machine Learning-adathalmaz párhuzamos feldolgozásra particionálva.
- `side_inputs`: Egy vagy több olyan hivatkozási adat vagy adatkészlet, amelyet nem kell particionálni.
- `output`: `PipelineData` A kimeneti könyvtárnak megfelelő objektum.
- `arguments`: A felhasználói parancsfájlnak átadott argumentumok listája. A unknown_args használatával kérheti le őket a belépési parancsfájlban (nem kötelező).
- `allow_reuse`: Azt határozza meg, hogy a lépés felhasználja-e az előző eredményeket, ha ugyanazokkal a beállításokkal/bemenetekkel futnak. Ha ez a paraméter `False` , akkor a folyamat végrehajtása során a rendszer mindig új futtatást hoz létre ehhez a lépéshez. (nem kötelező; az alapértelmezett érték: `True` .)

```python
from azureml.pipeline.steps import ParallelRunStep

parallelrun_step = ParallelRunStep(
    name="predict-digits-mnist",
    parallel_run_config=parallel_run_config,
    inputs=[input_mnist_ds_consumption],
    output=output_dir,
    allow_reuse=True
)
```

## <a name="debugging-scripts-from-remote-context"></a>Parancsfájlok hibakeresése távoli környezetből

Egy pontozási szkript helyi hibakeresése egy valós folyamaton belül egy pontozási parancsfájl hibakeresése egy nehéz ugrás lehet. A naplók a portálon való megtalálásával kapcsolatos információkért lásd:  [Machine learning-folyamatok című rész, amely a parancsfájlok távoli környezetből való hibakeresését](how-to-debug-pipelines.md)ismerteti. Az ebben a szakaszban található információk egy ParallelRunStep is érvényesek.

A naplófájl például a `70_driver_log.txt` vezérlőből származó adatokat tartalmaz, amely elindítja a ParallelRunStep kódot.

A ParallelRunStep-feladatok elosztott jellegéből adódóan számos különböző forrásból származó naplók találhatók. A rendszer azonban két konszolidált fájlt hoz létre, amelyek magas szintű információkat biztosítanak:

- `~/logs/job_progress_overview.txt`: Ez a fájl magas szintű információt nyújt a mini-batchs (más néven feladatoknak) számáról, amely az eddig feldolgozott mini-batchek számát tartalmazza. Ezen a helyen a feladatok eredményét jeleníti meg. Ha a feladatainak végrehajtása sikertelen volt, a hibaüzenet jelenik meg, és a hibaelhárítás elindításának helye.

- `~/logs/sys/master_role.txt`: Ez a fájl a futó feladathoz tartozó fő csomópontot (más néven Orchestrator) jeleníti meg. Magában foglalja a feladatok létrehozását, a folyamat figyelését, a Futtatás eredményét.

A EntryScript Helper és Print utasítások használatával generált naplók a következő fájlokban találhatók:

- `~/logs/user/entry_script_log/<ip_address>/<process_name>.log.txt`: Ezek a fájlok entry_script a EntryScript Helper használatával írt naplók.

- `~/logs/user/stdout/<ip_address>/<process_name>.stdout.txt`: Ezek a fájlok az stdout-ból (pl. Print utasítás) származó naplók a entry_script.

- `~/logs/user/stderr/<ip_address>/<process_name>.stderr.txt`: Ezek a fájlok a entry_script stderr származó naplók.

A parancsfájlban található hibák tömör megismeréséhez a következőt kell tennie:

- `~/logs/user/error.txt`: Ez a fájl megpróbálja összefoglalni a parancsfájlban szereplő hibákat.

A parancsfájl hibáit a következő témakörben találhatja meg:

- `~/logs/user/error/`: A betöltési parancsfájl betöltésekor és futtatásakor eldobott kivételek teljes verem-nyomkövetését tartalmazza.

Ha teljes mértékben meg kell ismernie, hogy az egyes csomópontok hogyan hajtották végre a pontszám-parancsfájlt, tekintse meg az egyes csomópontok egyes folyamatainak naplóit. A folyamat naplófájljai a `sys/node` mappában találhatók, munkavégző csomópontok szerint csoportosítva:

- `~/logs/sys/node/<ip_address>/<process_name>.txt`: Ez a fájl részletes információkat nyújt az egyes mini-kötegekről, mivel azokat a feldolgozók felvettek vagy elvégezték. Minden egyes mini-batch esetében ez a fájl a következőket tartalmazza:

    - A munkavégző folyamat IP-címe és PID-je. 
    - Az elemek teljes száma, a sikeresen feldolgozott elemek száma és a sikertelen elemek száma.
    - A kezdési idő, az időtartam, a feldolgozási idő és a futtatási módszer ideje.

Az egyes csomópontok erőforrás-használatának időszakos ellenőrzésének eredményeit is megtekintheti. A naplófájlok és a telepítőfájlok a következő mappában találhatók:

- `~/logs/perf`: Állítsa be `--resource_monitor_interval` az ellenőrzési időközt másodpercben. Az alapértelmezett intervallum `600` , amely körülbelül 10 percet vesz igénybe. A figyelés leállításához állítsa a értéket a következőre: `0` . Az egyes mappák a következőket `<ip_address>` tartalmazzák:

    - `os/`: A csomóponton futó összes folyamatra vonatkozó információ. Egy ellenőrzés futtatja az operációs rendszer parancsát, és menti az eredményt egy fájlba. Linux rendszeren a parancs a következő: `ps` . Windows rendszeren használja a következőt: `tasklist` .
        - `%Y%m%d%H`: Az almappa neve az idő óra.
            - `processes_%M`: A fájl az ellenőrzési idő percével végződik.
    - `node_disk_usage.csv`: A csomópont részletes lemezes használata.
    - `node_resource_usage.csv`: A csomópont erőforrás-használati áttekintése.
    - `processes_resource_usage.csv`: Az egyes folyamatok erőforrás-használati áttekintése.

### <a name="how-do-i-log-from-my-user-script-from-a-remote-context"></a>A felhasználói szkriptből Hogyan a naplót egy távoli környezetből?

A ParallelRunStep process_count_per_node alapján több folyamatot is futtathat egy csomóponton. Az egyes folyamatok naplófájljainak a csomóponton való rendszerezéséhez és a Print és a log utasítás összevonásához javasoljuk, hogy az alább látható módon használja a ParallelRunStep naplózó használatát. Egy adatgyűjtő-EntryScript kap, és a naplók megjelennek a **naplók/felhasználói** mappában a portálon.

**Egy minta-bejegyzési parancsfájl a Logger használatával:**
```python
from azureml_user.parallel_run import EntryScript

def init():
    """ Initialize the node."""
    entry_script = EntryScript()
    logger = entry_script.logger
    logger.debug("This will show up in files under logs/user on the Azure portal.")


def run(mini_batch):
    """ Accept and return the list back."""
    # This class is in singleton pattern and will return same instance as the one in init()
    entry_script = EntryScript()
    logger = entry_script.logger
    logger.debug(f"{__file__}: {mini_batch}.")
    ...

    return mini_batch
```

### <a name="how-could-i-pass-a-side-input-such-as-a-file-or-files-containing-a-lookup-table-to-all-my-workers"></a>Hogyan adhatok át egy olyan oldali bemenetet, mint például egy keresési táblázatot tartalmazó fájl vagy fájl (ok) az összes feldolgozónak?

A felhasználó a ParalleRunStep side_inputs paraméterével adhat át hivatkozási adattípust a parancsfájlnak. A side_inputsként megadott összes adatkészlet minden munkavégző csomóponthoz lesz csatlakoztatva. A felhasználó lekérheti a csatlakoztatás helyét a pass argumentum használatával.

Készítse el a hivatkozási adatokat tartalmazó [adatkészletet](/python/api/azureml-core/azureml.core.dataset.dataset?preserve-view=true&view=azure-ml-py) , és regisztrálja azt a munkaterületen. Adja át a `side_inputs` paraméterének `ParallelRunStep` . Emellett az elérési útját is hozzáadhatja a `arguments` szakaszhoz, hogy könnyen hozzáférhessen a csatlakoztatott elérési úthoz:

```python
label_config = label_ds.as_named_input("labels_input")
batch_score_step = ParallelRunStep(
    name=parallel_step_name,
    inputs=[input_images.as_named_input("input_images")],
    output=output_dir,
    arguments=["--labels_dir", label_config],
    side_inputs=[label_config],
    parallel_run_config=parallel_run_config,
)
```

Ezután elérheti azt a következtetési parancsfájlban (például az init () metódusban) a következőképpen:

```python
parser = argparse.ArgumentParser()
parser.add_argument('--labels_dir', dest="labels_dir", required=True)
args, _ = parser.parse_known_args()

labels_path = args.labels_dir
```

### <a name="how-to-use-input-datasets-with-service-principal-authentication"></a>A bemeneti adatkészletek használata egyszerű szolgáltatás hitelesítésével

A felhasználó átadhat bemeneti adatkészleteket a munkaterületen használt egyszerű szolgáltatás-hitelesítéssel. Az ilyen adatkészletek ParallelRunStep való használata megköveteli, hogy az adatkészlet regisztrálva legyen az ParallelRunStep-konfiguráció összeállításához.

```python
service_principal = ServicePrincipalAuthentication(
    tenant_id="***",
    service_principal_id="***",
    service_principal_password="***")
 
ws = Workspace(
    subscription_id="***",
    resource_group="***",
    workspace_name="***",
    auth=service_principal
    )
 
default_blob_store = ws.get_default_datastore() # or Datastore(ws, '***datastore-name***') 
ds = Dataset.File.from_files(default_blob_store, '**path***')
registered_ds = ds.register(ws, '***dataset-name***', create_new_version=True)
```

## <a name="next-steps"></a>Következő lépések

* Tekintse meg az alábbi [Jupyter-jegyzetfüzeteket Azure Machine learning folyamatok bemutatásával](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/machine-learning-pipelines)

* A [azureml-pipeline-Steps](/python/api/azureml-pipeline-steps/azureml.pipeline.steps?preserve-view=true&view=azure-ml-py) csomaggal kapcsolatos segítségért tekintse meg az SDK-referenciát. A ParallelRunStep osztály [dokumentációjának](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.parallelrunstep?preserve-view=true&view=azure-ml-py) megtekintése.

* Kövesse a [speciális oktatóanyagot](tutorial-pipeline-batch-scoring-classification.md) a folyamatok ParallelRunStep használatával történő használatáról. Az oktatóanyag bemutatja, hogyan adhat át egy másik fájlt oldalsó bemenetként.
