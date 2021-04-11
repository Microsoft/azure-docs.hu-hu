---
title: A ML-folyamatok hibaelhárítása
titleSuffix: Azure Machine Learning
description: Hibaelhárítás a gépi tanulási folyamat futtatásakor felmerülő hibák elhárítása során. Gyakori buktatók és tippek, amelyek segítenek a parancsfájlok hibakeresésében a távoli végrehajtás előtt és közben.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
author: lobrien
ms.author: laobri
ms.date: 10/22/2020
ms.topic: troubleshooting
ms.custom: troubleshooting, devx-track-python, contperf-fy21q2
ms.openlocfilehash: 195942d1787cdef51ee480fa5c5595db99bc7c78
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "102522087"
---
# <a name="troubleshooting-machine-learning-pipelines"></a>Gépi tanulási folyamatok hibaelhárítása

Ebből a cikkből megtudhatja, hogyan végezheti el a hibaelhárítást, ha a [Azure Machine learning SDK](/python/api/overview/azure/ml/intro) -ban és [Azure Machine learning Designerben](./concept-designer.md)a [Machine learning-folyamat](concept-ml-pipelines.md) futtatásakor hibák jelentkeznek. 

## <a name="troubleshooting-tips"></a>Hibaelhárítási tippek

Az alábbi táblázat a folyamat fejlesztése során felmerülő gyakori problémákat tartalmazza, és lehetséges megoldásokat tartalmaz.

| Probléma | Lehetséges megoldás |
|--|--|
| Nem sikerült átadni az `PipelineData` adatkönyvtárat | Győződjön meg arról, hogy létrehozott egy olyan könyvtárat a szkriptben, amely megfelel annak a könyvtárnak, ahová a folyamat várja a lépés kimeneti adatait. A legtöbb esetben egy bemeneti argumentum határozza meg a kimeneti könyvtárat, amelyet utána explicit módon kell létrehoznia. A kimeneti könyvtárat az `os.makedirs(args.output_dir, exist_ok=True)` metódus használatával hozhatja létre. Tekintse meg az [oktatóanyagot](tutorial-pipeline-batch-scoring-classification.md#write-a-scoring-script) egy pontozási parancsfájl példája, amely ezt a kialakítási mintát mutatja. |
| Függőséggel kapcsolatos hibák | Ha olyan függőségi hibákat lát a távoli folyamatban, amelyek a helyi teszteléskor nem fordultak elő, ellenőrizze, hogy a távoli környezet függőségei és verziói megfelelnek-e a tesztkörnyezet függőségeinek és verzióinak. (Lásd: [környezetek kiépítése, gyorsítótárazása és újrafelhasználása](./concept-environments.md#environment-building-caching-and-reuse)|
| Nem egyértelmű hibák a számítási célokkal | Próbálkozzon a számítási célok törlésével és újbóli létrehozásával. A számítási célok újbóli létrehozása gyorsan elvégezhető, és bizonyos átmeneti problémák is megoldhatók. |
| A folyamat nem használja újra a lépéseket | Az ismételt használat alapértelmezés szerint engedélyezve van, de gondoskodjon arról, hogy ne tiltsa le egy folyamat lépéseiben. Ha az újbóli használat le van tiltva, a `allow_reuse` lépésben megadott paraméter a következő lesz: `False` . |
| A folyamat feleslegesen fut újra | Annak biztosítása érdekében, hogy a lépések csak akkor fussanak újra, amikor a mögöttes adatokat vagy parancsfájlokat módosítják, az egyes lépésekhez tartozó forráskód-címtárakat le kell választva. Ha ugyanazt a könyvtárat használja több lépéshez, előfordulhat, hogy szükségtelen ismétléseket tapasztal. Használja a `source_directory` paramétert egy folyamat lépés objektumon, hogy az elkülönített könyvtárba mutasson erre a lépésre, és győződjön meg arról, hogy nem ugyanazt az `source_directory` útvonalat használja több lépéshez. |
| A képzési időszakokra vagy más hurok-viselkedésre lelassított lépés | Próbáljon meg bármilyen írást váltani, beleértve a naplózást is `as_mount()` `as_upload()` . A **csatlakoztatási** mód távoli virtualizált fájlrendszert használ, és minden alkalommal feltölti a teljes fájlt. |
| A számítási cél elkezdése hosszú időt vesz igénybe | A számítási célokhoz tartozó Docker-rendszerképek betöltődik Azure Container Registryból (ACR). Alapértelmezés szerint a Azure Machine Learning létrehoz egy ACR-t, *amely az alapszintű* szolgáltatási szintet használja. A munkaterületre vonatkozó ACR a standard vagy a prémium szintre való módosítása csökkentheti a lemezképek létrehozásához és betöltéséhez szükséges időt. További információ: [Azure Container Registry szolgáltatási szintek](../container-registry/container-registry-skus.md). |

### <a name="authentication-errors"></a>Hitelesítési hibák

Ha egy távoli feladatból származó számítási célra hajt végre felügyeleti műveletet, a következő hibák valamelyikét fogja kapni: 

```json
{"code":"Unauthorized","statusCode":401,"message":"Unauthorized","details":[{"code":"InvalidOrExpiredToken","message":"The request token was either invalid or expired. Please try again with a valid token."}]}
```

```json
{"error":{"code":"AuthenticationFailed","message":"Authentication failed."}}
```

Ha például megpróbál létrehozni vagy csatolni egy számítási célt egy olyan ML-folyamatból, amely távoli végrehajtásra van elküldve, a rendszer hibaüzenetet küld.
## <a name="troubleshooting-parallelrunstep"></a>Hibaelhárítás `ParallelRunStep` 

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
- `output`: `OutputFileDatasetConfig` A kimeneti könyvtárnak megfelelő objektum.
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

## <a name="debugging-techniques"></a>Hibakeresési technikák

A folyamatok hibakereséséhez három fő módszer áll rendelkezésre: 

* Az egyes folyamatokkal kapcsolatos lépések hibakeresése a helyi számítógépen
* A naplózás és a Application Insights használatával elkülönítheti és diagnosztizálhatja a probléma forrását
* Távoli hibakereső csatolása az Azure-ban futó folyamatokhoz

### <a name="debug-scripts-locally"></a>Parancsfájlok helyi hibakeresése

A folyamat egyik leggyakoribb meghibásodása, hogy a tartomány parancsfájlja nem a kívánt módon fut, vagy futásidejű hibákat tartalmaz a távoli számítási környezetben, amely nehezen hibakeresést végez.

Maguk a folyamatok nem futtathatók helyileg, de a parancsfájlok a helyi gépen való elkülönítése lehetővé teszi a gyorsabb hibakeresést, mivel nem kell megvárnia a számítási és a környezeti felépítési folyamatra. Ehhez szükség van egy fejlesztési munkára:

* Ha az adatai egy felhőalapú adattárban találhatók, le kell töltenie az adatait, és elérhetővé kell tennie azt a parancsfájl számára. Az adat kis mintájának használatával lerövidítheti a futtatókörnyezetet, és gyors visszajelzést kaphat a parancsfájlok működéséről
* Ha egy közbenső folyamat lépését kísérli meg szimulálni, előfordulhat, hogy manuálisan kell létrehoznia az adott parancsfájlnak az előző lépésből várt típusú objektumokat.
* Emellett meg kell határoznia a saját környezetét, és replikálnia kell a távoli számítási környezetben meghatározott függőségeket.

Ha a parancsfájl beállítása a helyi környezetben való futtatásra van beállítva, sokkal egyszerűbb a hibakeresési feladatok végrehajtása, például:

* Egyéni hibakeresési konfiguráció csatolása
* A végrehajtás felfüggesztése és az objektum állapotának vizsgálata
* Olyan típusú vagy logikai hibák, amelyek nem lesznek elérhetők a futtatókörnyezetig

> [!TIP] 
> Ha ellenőrizni szeretné, hogy a parancsfájl a várt módon fut-e, egy jó következő lépés futtatja a szkriptet egy egylépéses folyamaton, mielőtt egy több lépésből álló folyamaton próbálja futtatni azt.

## <a name="configure-write-to-and-review-pipeline-logs"></a>A folyamat naplófájljainak konfigurálása, írása és áttekintése

A parancsfájlok helyi tesztelése nagyszerű módja annak, hogy a folyamat megkezdése előtt hibakeresést végezzen a fő kódrészletek és az összetett logika között, de előfordulhat, hogy a tényleges folyamat futtatásakor valószínűleg a parancsfájlokat kell hibakeresést végeznie, különösen a folyamat lépései közötti interakció során felmerülő viselkedés diagnosztizálásakor. Javasoljuk, hogy az utasítások liberális használatát `print()` a lépés parancsfájljaiban is megtekintheti, így a távoli végrehajtás során az objektum állapota és a várt értékek láthatók, hasonlóan a JavaScript-kód hibakereséséhez.

### <a name="logging-options-and-behavior"></a>Naplózási beállítások és viselkedés

Az alábbi táblázat a folyamatok különböző hibakeresési lehetőségeiről nyújt információt. Nem kimerítő lista, mert az itt látható Azure Machine Learning, Python és OpenCensus mellett más lehetőségek is vannak.

| Kódtár                    | Típus   | Példa                                                          | Cél                                  | Források                                                                                                                                                                                                                                                                                                                    |
|----------------------------|--------|------------------------------------------------------------------|----------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Azure Machine Learning SDK | Metric | `run.log(name, val)`                                             | Azure Machine Learning portál felhasználói felülete             | [Kísérletek nyomon követése](how-to-track-experiments.md)<br>[azureml. Core. Run osztály](/python/api/azureml-core/azureml.core.run%28class%29)                                                                                                                                                 |
| Python-nyomtatás/-naplózás    | Napló    | `print(val)`<br>`logging.info(message)`                          | Illesztőprogram-naplók, Azure Machine Learning Designer | [Kísérletek nyomon követése](how-to-track-experiments.md)<br><br>[Python-naplózás](https://docs.python.org/2/library/logging.html)                                                                                                                                                                       |
| OpenCensus Python          | Napló    | `logger.addHandler(AzureLogHandler())`<br>`logging.log(message)` | Application Insights – nyomkövetés                | [Folyamatok hibakeresése az Application Insightsban](./how-to-log-pipelines-application-insights.md)<br><br>[OpenCensus Azure Monitor-exportálók](https://github.com/census-instrumentation/opencensus-python/tree/master/contrib/opencensus-ext-azure)<br>[Python-naplózási szakácskönyv](https://docs.python.org/3/howto/logging-cookbook.html) |

#### <a name="logging-options-example"></a>Naplózási beállítások – példa

```python
import logging

from azureml.core.run import Run
from opencensus.ext.azure.log_exporter import AzureLogHandler

run = Run.get_context()

# Azure ML Scalar value logging
run.log("scalar_value", 0.95)

# Python print statement
print("I am a python print statement, I will be sent to the driver logs.")

# Initialize python logger
logger = logging.getLogger(__name__)
logger.setLevel(args.log_level)

# Plain python logging statements
logger.debug("I am a plain debug statement, I will be sent to the driver logs.")
logger.info("I am a plain info statement, I will be sent to the driver logs.")

handler = AzureLogHandler(connection_string='<connection string>')
logger.addHandler(handler)

# Python logging with OpenCensus AzureLogHandler
logger.warning("I am an OpenCensus warning statement, find me in Application Insights!")
logger.error("I am an OpenCensus error statement with custom dimensions", {'step_id': run.id})
``` 

## <a name="azure-machine-learning-designer"></a>Azure Machine Learning Designer

A tervezőben létrehozott folyamatok esetében az **70_driver_log** fájlt a szerzői műveletek lapon vagy a folyamat futtatása Részletek lapon találja.

### <a name="enable-logging-for-real-time-endpoints"></a>Valós idejű végpontok naplózásának engedélyezése

A tervezőben a valós idejű végpontok hibaelhárításához és hibakereséséhez engedélyeznie kell az alkalmazás-betekintési naplózást az SDK használatával. A naplózás lehetővé teszi a modell üzembe helyezési és használati problémáinak hibaelhárítását és hibakeresését. További információ: [a telepített modellek naplózása](./how-to-enable-app-insights.md). 

### <a name="get-logs-from-the-authoring-page"></a>Naplók beolvasása a szerzői műveletek lapról

Amikor elküld egy folyamat futását, és az authoring (szerzői műveletek) oldalon marad, megkeresheti az egyes modulokhoz generált naplófájlokat, mivel az egyes modulok futtatása befejeződött.

1. Válassza ki azt a modult, amely a szerzői műveletek vásznon fut.
1. A modul jobb oldali ablaktáblájában lépjen a  **kimenetek és naplók** lapra.
1. Bontsa ki a jobb oldali ablaktáblát, és válassza ki a **70_driver_log.txt** a fájl böngészőben való megtekintéséhez. A naplókat helyileg is letöltheti.

    ![Kibontott kimeneti ablaktábla a tervezőben](./media/how-to-debug-pipelines/designer-logs.png)

### <a name="get-logs-from-pipeline-runs"></a>Naplók beolvasása a folyamat-futtatásokból

A naplófájlokat meghatározott futtatásokhoz is megtalálhatja a folyamat futtatása Részletek lapon, amely a Studióban található **folyamatok** vagy **kísérletek** szakaszban található.

1. Válassza ki a tervezőben létrehozott folyamat-futtatást.

    ![Folyamat futtatása lap](./media/how-to-debug-pipelines/designer-pipelines.png)

1. Válasszon ki egy modult a betekintő ablaktáblán.
1. A modul jobb oldali ablaktáblájában lépjen a  **kimenetek és naplók** lapra.
1. A jobb oldali ablaktábla kibontásával megtekintheti a **70_driver_log.txt** fájlt a böngészőben, vagy kiválaszthatja a fájlt a naplók helyi letöltéséhez.

> [!IMPORTANT]
> Ha frissíteni szeretne egy folyamatot a folyamat futásának részletei lapon, a  folyamatot egy új folyamat-piszkozatra kell futtatnia. A folyamat futtatása a folyamat pillanatképe. A naplófájlhoz hasonló, és nem módosítható. 

## <a name="application-insights"></a>Application Insights
A OpenCensus Python-függvénytár ily módon történő használatával kapcsolatos további információkért tekintse meg a következő útmutatót: a [gépi tanulási folyamatok hibakeresése és hibaelhárítása Application Insights](./how-to-log-pipelines-application-insights.md)

## <a name="interactive-debugging-with-visual-studio-code"></a>Interaktív hibakeresés a Visual Studio Code-ban

Bizonyos esetekben előfordulhat, hogy interaktívan kell hibakeresést végeznie a ML-folyamaton használt Python-kóddal. A Visual Studio Code (VS Code) és a debugpy használatával a kódot a betanítási környezetben futtatva is csatlakoztathatja. További információkért tekintse meg az [interaktív hibakeresést a vs Code útmutatóban](how-to-debug-visual-studio-code.md#debug-and-troubleshoot-machine-learning-pipelines).

## <a name="next-steps"></a>Következő lépések

* A használatával kapcsolatos teljes oktatóanyagért `ParallelRunStep` lásd [: oktatóanyag: Azure Machine learning folyamat létrehozása a Batch-pontozáshoz](tutorial-pipeline-batch-scoring-classification.md).

* Az automatizált gépi tanulást ML-folyamatokban bemutató teljes példa: az [automatikus ml használata egy Azure Machine learning-folyamaton a Pythonban](how-to-use-automlstep-in-pipelines.md).

* Tekintse meg az SDK-referenciát a [azureml-pipeline-Core](/python/api/azureml-pipeline-core/) csomag és a [azureml-Steps](/python/api/azureml-pipeline-steps/) csomag súgójában.

* Tekintse meg a [Designer-kivételek és-hibakódok](algorithm-module-reference/designer-error-codes.md)listáját.