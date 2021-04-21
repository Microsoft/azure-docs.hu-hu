---
title: Gépi tanulási folyamatok hibaelhárítása
titleSuffix: Azure Machine Learning
description: Hibaelhárítás gépi tanulási folyamatok futtatásakor. Gyakori buktatók és tippek a szkriptek távoli végrehajtás előtti és közbeni hibakeresésében.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
author: lobrien
ms.author: laobri
ms.date: 10/22/2020
ms.topic: troubleshooting
ms.custom: troubleshooting, devx-track-python, contperf-fy21q2
ms.openlocfilehash: 3a85566f395e97bbe88d52a8b306c7e0aa15669d
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2021
ms.locfileid: "107817340"
---
# <a name="troubleshooting-machine-learning-pipelines"></a>Gépi tanulási folyamatok hibaelhárítása

Ebből a cikkből megtudhatja, hogyan háríthatja el [a](concept-ml-pipelines.md) gépi tanulási folyamatok futtatásával kapcsolatos hibákat az [Azure Machine Learning SDK-ban](/python/api/overview/azure/ml/intro) és a [Azure Machine Learning tervezőben.](./concept-designer.md) 

## <a name="troubleshooting-tips"></a>Hibaelhárítási tippek

Az alábbi táblázat gyakori problémákat tartalmaz a folyamatfejlesztés során, és lehetséges megoldásokat is tartalmaz.

| Probléma | Lehetséges megoldás |
|--|--|
| Nem sikerült adatokat átadni a `PipelineData` címtárnak | Győződjön meg arról, hogy létrehozott egy olyan könyvtárat a szkriptben, amely megfelel annak a könyvtárnak, ahová a folyamat várja a lépés kimeneti adatait. A legtöbb esetben egy bemeneti argumentum határozza meg a kimeneti könyvtárat, amelyet utána explicit módon kell létrehoznia. A kimeneti könyvtárat az `os.makedirs(args.output_dir, exist_ok=True)` metódus használatával hozhatja létre. Tekintse meg [az oktatóanyagot,](tutorial-pipeline-batch-scoring-classification.md#write-a-scoring-script) amely egy pontozószk szkriptet mutat be, amely ezt a tervezési mintát mutatja be. |
| Függőséggel kapcsolatos hibák | Ha olyan függőségi hibákat lát a távoli folyamatban, amelyek a helyi teszteléskor nem fordultak elő, ellenőrizze, hogy a távoli környezet függőségei és verziói megfelelnek-e a tesztkörnyezet függőségeinek és verzióinak. [(Lásd: Környezetek kiépítése, gyorsítótárazása és újrafelhasználása](./concept-environments.md#environment-building-caching-and-reuse)|
| Számítási célokkal kapcsolatos nem egyértelmű hibák | Próbálja meg törölni és újra létrehozni a számítási célokat. A számítási célok újra létrehozása gyors, és bizonyos átmeneti problémákat megoldhat. |
| A folyamat nem használja újra a lépéseket | A lépés újrafelhasználása alapértelmezés szerint engedélyezve van, de győződjön meg arról, hogy nem tiltotta le azt egy folyamatlépésben. Ha az újbóli felhasználás le van tiltva, a lépés `allow_reuse` paramétere a következőre lesz beállítva: `False` . |
| A folyamat feleslegesen fut újra | Annak biztosítása érdekében, hogy a lépések csak akkor futnak újra, ha az alapul szolgáló adatok vagy szkriptek megváltoznak, minden egyes lépésnél külön kell külön-különülni a forráskód könyvtáraitól. Ha több lépéshez is ugyanazt a forráskönyvtárat használja, szükségtelen újrafuttatásokat tapasztalhat. Használja a paramétert egy folyamatlépési objektumon, hogy az adott lépés elkülönített könyvtárába mutasson, és győződjön meg arról, hogy nem ugyanazt az elérési utat használja `source_directory` `source_directory` több lépéshez. |
| A betanítás alapkvóta- vagy egyéb hurkolási viselkedésének lassulása | Próbálja meg átkapcsolni a fájlírásokat, beleértve a naplózást is, a `as_mount()` következőre: `as_upload()` . A **csatlakoztatási** mód távoli virtualizált fájlrendszert használ, és minden hozzáfűzésekor feltölti a teljes fájlt. |
| A számítási cél hosszú ideig tart | A számítási célokhoz használható Docker-rendszerképeket a rendszer Azure Container Registry (ACR) betölti. Alapértelmezés szerint a Azure Machine Learning alapszintű szolgáltatási szintet használó *ACR-t* hoz létre. Ha a munkaterület ACR-ét standard vagy prémium szintre módosítja, csökkentheti a rendszerképek létrehozásához és betöltéséhez szükséges időt. További információ: Azure Container Registry [szolgáltatási szintek.](../container-registry/container-registry-skus.md) |

### <a name="authentication-errors"></a>Hitelesítési hibák

Ha távoli feladatból hajt végre felügyeleti műveletet egy számítási célon, a következő hibák egyike fog fellépni: 

```json
{"code":"Unauthorized","statusCode":401,"message":"Unauthorized","details":[{"code":"InvalidOrExpiredToken","message":"The request token was either invalid or expired. Please try again with a valid token."}]}
```

```json
{"error":{"code":"AuthenticationFailed","message":"Authentication failed."}}
```

Például hibaüzenetet kap, ha távoli végrehajtásra elküldött gépi tanulási folyamatból próbál létrehozni vagy csatolni egy számítási célt.
## <a name="troubleshooting-parallelrunstep"></a>Hibaelhárítás `ParallelRunStep` 

Az parancsprogramnak `ParallelRunStep` *két függvényt* kell tartalmaznia:
- `init()`: Ezt a függvényt bármilyen költséges vagy gyakori előkészülethez használhatja későbbi következtetéshez. A használatával például betöltheti a modellt egy globális objektumba. Ezt a függvényt a rendszer csak egyszer fogja meghívni a folyamat elején.
-  `run(mini_batch)`: A függvény minden példányon `mini_batch` fut.
    -  `mini_batch`: meghívja a run metódust, és egy `ParallelRunStep` listát vagy pandast ad át `DataFrame` argumentumként a metódusnak. A mini_batch minden bejegyzése egy fájl elérési útja lesz, ha a bemenet egy vagy `FileDataset` egy pandas, ha a bemenet `DataFrame` `TabularDataset` .
    -  `response`: a run() metódusnak egy pandast vagy egy `DataFrame` tömböt kell visszaadni. A append_row output_action a visszaadott elemeket a rendszer hozzáfűzi a közös kimeneti fájlhoz. A summary_only a rendszer figyelmen kívül hagyja az elemek tartalmát. Minden kimeneti művelethez minden visszaadott kimeneti elem a bemeneti elem egy sikeres futtatását jelzi a bemeneti mini kötegben. Győződjön meg arról, hogy a futtatás eredménye elegendő adatot tartalmaz a bemenet leképezéséhez a kimeneti eredmény futtatásához. A futtatás kimenete kimeneti fájlban lesz megírva, és nem garantált, hogy a megfelelő sorrendben van. A kimenetben valamilyen kulcsot kell használnia a bemenethez való leképezéshez.

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

Ha egy másik fájl vagy mappa ugyanabban a könyvtárban van, mint a következtetési parancsfájl, hivatkozhat rá az aktuális munkakönyvtár megkeresése segítségével.

```python
script_dir = os.path.realpath(os.path.join(__file__, '..',))
file_path = os.path.join(script_dir, "<file_name>")
```

### <a name="parameters-for-parallelrunconfig"></a>A ParallelRunConfig paraméterei

`ParallelRunConfig` A a folyamat fő konfigurációja a `ParallelRunStep` Azure Machine Learning belül. Ezzel becsomagolhatja a szkriptet, és konfigurálhatja a szükséges paramétereket, beleértve az alábbi bejegyzéseket is:
- `entry_script`: Felhasználói parancsfájl helyi fájlútvonalként, amely párhuzamosan fog futni több csomóponton. Ha `source_directory` van ilyen, használjon relatív elérési utat. Ellenkező esetben használja a gépen elérhető elérési utat.
- `mini_batch_size`: Az egyetlen hívásnak átadott mini köteg `run()` mérete. (nem kötelező; az alapértelmezett érték a és a `10` `FileDataset` `1MB` `TabularDataset` fájlja.)
    - A értéke a minimális értékű fájlok `FileDataset` `1` száma. Több fájlt is egyesíthet egyetlen minikötetben.
    - A `TabularDataset` fájlban ez az adatok mérete. Példaértékek: `1024` `1024KB` , , és `10MB` `1GB` . Az ajánlott érték `1MB` a következő: . A mini kötege `TabularDataset` soha nem lépi át a fájlok határait. Ha például különböző méretű .csv-fájlokat tartalmaz, a legkisebb fájl 100 KB, a legnagyobb pedig 10 MB. Ha be van állítva, akkor az 1 MB-nál kisebb méretű fájlok egy minikötetként `mini_batch_size = 1MB` lesznek kezelve. Az 1 MB-nál nagyobb méretű fájlok több mini kötegre oszlanak.
- `error_threshold`: A rekordhibák számát és a fájlhibáit a feldolgozás során figyelmen kívül `TabularDataset` `FileDataset` kell hagyni. Ha a teljes bemenet hibaszáma meghaladja ezt az értéket, a rendszer megszakítja a feladatot. A hibaküszöb a teljes bemenetre vonatkozik, nem pedig a metódusnak küldött egyes mini `run()` kötegre. A tartomány `[-1, int.max]` a következő: . A `-1` rész az összes hiba figyelmen kívül hagyása a feldolgozás során.
- `output_action`: Az alábbi értékek egyike jelzi a kimenet rendszerezését:
    - `summary_only`: A felhasználói szkript tárolja a kimenetet. `ParallelRunStep` A csak a hibaküszöbérték kiszámításához használja a kimenetet.
    - `append_row`: Minden bemenethez csak egy fájl jön létre a kimeneti mappában az összes kimenet sorokkal elválasztott hozzáfűzése érdekében.
- `append_row_file_name`: A kimeneti fájl nevének testreszabása a append_row output_action (nem kötelező; az alapértelmezett érték `parallel_run_step.txt` ).
- `source_directory`: A számítási célon végrehajtható összes fájlt tartalmazó mappák elérési útjai (nem kötelező).
- `compute_target`: Csak `AmlCompute` az támogatott.
- `node_count`: A felhasználói szkript futtatásához használt számítási csomópontok száma.
- `process_count_per_node`: A folyamatok száma csomópontonként. Ajánlott úgy beállítani, hogy az egy csomóponton található GPU vagy CPU hány processzorral rendelkezik (nem kötelező; az alapértelmezett érték `1` a ).
- `environment`: A Python-környezet definíciója. Konfigurálhatja meglévő Python-környezet használatára vagy ideiglenes környezet beállításra. A definíció a szükséges alkalmazásfüggőségek beállításáért is felelős (nem kötelező).
- `logging_level`: Napló részletessége. A részletesség növelése a következő értékekből állnak: `WARNING` , `INFO` és `DEBUG` . (nem kötelező; az alapértelmezett érték `INFO` )
- `run_invocation_timeout`: A `run()` metódus meghívásának időkorlátja másodpercben. (nem kötelező; az alapértelmezett érték `60` )
- `run_max_try`: Egy mini köteg maximálisan `run()` kipróbált darabszáma. Az sikertelen, ha kivételt ad vissza, vagy a rendszer nem ad vissza semmit a `run()` `run_invocation_timeout` elérésekor (nem kötelező; az alapértelmezett érték `3` ). 

A , , , , , és értékeket megadhatja , így a folyamatfuttatás újratüldetéskor finomhangolhatja `mini_batch_size` `node_count` a `process_count_per_node` `logging_level` `run_invocation_timeout` `run_max_try` `PipelineParameter` paraméterértékeket. Ebben a példában a és a értéket használja, és ezeket az értékeket fogja módosítani a futtatás `PipelineParameter` `mini_batch_size` későbbi `Process_count_per_node` újrafuttatásakor. 

### <a name="parameters-for-creating-the-parallelrunstep"></a>Paraméterek a ParallelRunStep létrehozásához

Hozza létre a ParallelRunStep létrehozására a szkriptet, a környezeti konfigurációt és a paramétereket használva. Adja meg a munkaterülethez már csatolt számítási célt a következtetési szkript végrehajtási céljaként. A `ParallelRunStep` használatával hozza létre a kötegelt következtetési folyamat lépését, amely az alábbi paramétereket veszi fel:
- `name`: A lépés neve a következő elnevezési korlátozásokkal: egyedi, 3–32 karakter és regex ^ \[ a-z \] ([-a-z0-9]*[a-z0-9])?$.
- `parallel_run_config`: Egy `ParallelRunConfig` objektum, a korábban meghatározottak szerint.
- `inputs`: Egy vagy több egytípusú Azure Machine Learning, amelyek párhuzamos feldolgozás céljából particionálva lesznek.
- `side_inputs`: Egy vagy több referenciaadat vagy -adatkészlet, amelyek particionálás nélkül használhatók oldalbemenetként.
- `output`: A `OutputFileDatasetConfig` kimeneti könyvtárnak megfelelő objektum.
- `arguments`: A felhasználói szkriptnek átadott argumentumok listája. A unknown_args beolvassa őket a bejegyzési szkriptben (nem kötelező).
- `allow_reuse`: Azt határozza meg, hogy a lépés újra felhasználja-e a korábbi eredményeket, ha ugyanazokkal a beállításokkal/bemenetekkel fut. Ha ez a paraméter , a folyamat végrehajtása során mindig létrejön egy új futtatás ehhez a `False` lépéshez. (nem kötelező; az alapértelmezett érték `True` .)

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

A folyamatok hibakeresésének három fő technikája van: 

* Folyamat egyes lépései hibakeresése a helyi számítógépen
* Naplózás és Application Insights használata a probléma forrásának elkülönítése és diagnosztizálása érdekében
* Távoli hibakereső csatolása egy Azure-ban futó folyamathoz

### <a name="debug-scripts-locally"></a>Szkriptek helyi hibakeresése

A folyamatok egyik leggyakoribb hibája, hogy a tartományi szkript nem a kívántnak megfelelően fut, vagy olyan futásidejű hibákat tartalmaz a távoli számítási környezetben, amelyeken nehéz a hibakeresés.

Maguk a folyamatok nem futtathatók helyileg, de ha a szkripteket elkülönítve futtatja a helyi gépen, gyorsabban lehet hibakeresést végezni, mivel nem kell megvárni a számítási és környezeti buildelési folyamatot. Ehhez fejlesztési munkára van szükség:

* Ha az adatok egy felhőalapú adattárban találhatóak, le kell töltenie az adatokat, és elérhetővé kell tennie a szkript számára. Az adatok kis mintáját használva egyszerűen le lehet csökkenteni a futásidőt, és gyorsan visszajelzést kaphat a szkript viselkedéséről
* Ha köztes folyamatlépést próbál szimulálni, előfordulhat, hogy manuálisan kell létrehoznia az adott szkript által az előző lépésben várt objektumtípusokat
* Saját környezetet is meg kell határoznia, és replikálni kell a távoli számítási környezetben definiált függőségeket

Ha már van egy szkriptbeállítása a helyi környezetben való futtatáshoz, sokkal egyszerűbb a következőhöz hasonló hibakeresési feladatok elvégzése:

* Egyéni hibakeresési konfiguráció csatolása
* A végrehajtás szüneteltetés és az objektumállapot vizsgálata
* Típus- vagy logikai hibákat fog fogni, amelyek a futásidőig nem lesznek elérhetőek

> [!TIP] 
> Miután meggyőződött arról, hogy a szkript a várt módon fut, egy jó következő lépés a szkript futtatása egy egylépéses folyamatban, mielőtt több lépésből áll egy folyamatban próbál meg futtatni.

## <a name="configure-write-to-and-review-pipeline-logs"></a>Folyamatnaplók konfigurálása, írása és áttekintése

A szkriptek helyi tesztelése nagyszerű módja a fő kódtöredékek és az összetett logika hibakeresésének a folyamat létrehozása előtt, de egy bizonyos ponton valószínűleg hibakeresést kell végeznie a szkriptekben a folyamat tényleges futtatása során, különösen akkor, ha a folyamat lépései közötti interakció során diagnosztizálja a viselkedést. Javasoljuk az utasítások használatát a lépés parancsfájlokban, hogy a távoli végrehajtás során az objektumok állapota és a várt értékek is láthatóak, hasonlóan ahhoz, mint a `print()` JavaScript-kód hibakeresése.

### <a name="logging-options-and-behavior"></a>Naplózási lehetőségek és viselkedés

Az alábbi táblázat a folyamatok különböző hibakeresési lehetőségeiről nyújt információt. A lista nem teljes, mivel az itt látható Azure Machine Learning, Python és OpenCensus mellett más lehetőségek is léteznek.

| Kódtár                    | Típus   | Példa                                                          | Cél                                  | Források                                                                                                                                                                                                                                                                                                                    |
|----------------------------|--------|------------------------------------------------------------------|----------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Azure Machine Learning SDK | Metric | `run.log(name, val)`                                             | Azure Machine Learning portál felhasználói felülete             | [Kísérletek nyomon követése](how-to-log-view-metrics.md)<br>[azureml.core.Run osztály](/python/api/azureml-core/azureml.core.run%28class%29)                                                                                                                                                 |
| Python-nyomtatás/-naplózás    | Napló    | `print(val)`<br>`logging.info(message)`                          | Illesztőprogram-naplók, Azure Machine Learning tervező | [Kísérletek nyomon követése](how-to-log-view-metrics.md)<br><br>[Python-naplózás](https://docs.python.org/2/library/logging.html)                                                                                                                                                                       |
| OpenCensus Python          | Napló    | `logger.addHandler(AzureLogHandler())`<br>`logging.log(message)` | Application Insights – nyomkövetések                | [Folyamatok hibakeresése az Application Insightsban](./how-to-log-pipelines-application-insights.md)<br><br>[OpenCensus Azure Monitor-exportálók](https://github.com/census-instrumentation/opencensus-python/tree/master/contrib/opencensus-ext-azure)<br>[Python-naplózási kézikönyv](https://docs.python.org/3/howto/logging-cookbook.html) |

#### <a name="logging-options-example"></a>Naplózási lehetőségek – példa

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

A tervezőben létrehozott folyamatok esetében a  70_driver_log fájlt a szerzői oldalon vagy a folyamat futtatás részletező oldalán találja.

### <a name="enable-logging-for-real-time-endpoints"></a>Valós idejű végpontok naplózásának engedélyezése

A tervezőben a valós idejű végpontok hibaelhárításához és hibakereséshez engedélyeznie kell az Application Insight-naplózást az SDK használatával. A naplózás lehetővé teszi a modell üzembe helyezésével és használatával kapcsolatos problémák elhárítását és hibakeresését. További információ: [Logging for deployed models (Üzembe helyezett modellek naplózása).](./how-to-enable-app-insights.md) 

### <a name="get-logs-from-the-authoring-page"></a>Naplók lekérte a szerzői oldalról

Amikor beküld egy folyamat futását, és a szerzői oldalon marad, az egyes modulok futásának befejezésekor az egyes modulokhoz létrehozott naplófájlokat is megtalálja.

1. Válasszon ki egy modult, amely már nem fut a szerzői vásznon.
1. A modul jobb oldali panelen válassza a  **Kimenetek és naplók** lapot.
1. Bontsa ki a jobb oldali panelt, és70_driver_log.txt **gombra** a fájl böngészőben való megtekintéséhez. A naplókat helyileg is letöltheti.

    ![Kibontott kimeneti panel a tervezőben](./media/how-to-debug-pipelines/designer-logs.png)

### <a name="get-logs-from-pipeline-runs"></a>Naplók lekérte a folyamatfuttatókból

Az egyes futtatásokkal kapcsolatos naplófájlokat a folyamatfutat részletező oldalán is megtalálja, amely a studio **Folyamatok** vagy Kísérletek szakaszában található. 

1. Válassza ki a tervezőben létrehozott folyamatfuttassat.

    ![Folyamatfuttassanak lapja](./media/how-to-debug-pipelines/designer-pipelines.png)

1. Válasszon ki egy modult az előnézeti panelen.
1. A modul jobb oldali panelen válassza a  **Kimenetek és naplók** lapot.
1. Bontsa ki a jobb oldali panelt a70_driver_log.txt **megtekintéséhez** a böngészőben, vagy válassza ki a fájlt a naplók helyi letöltéséhez.

> [!IMPORTANT]
> Ha frissítenie kell egy folyamatot a  folyamatfuttassa részletek oldalról, klónoznia kell a folyamat futását egy új folyamat vázlatára. A folyamatfutat a folyamat pillanatképe. Hasonlít a naplófájlhoz, és nem módosítható. 

## <a name="application-insights"></a>Application Insights
Az OpenCensus Python-kódtár ily módon való használatával kapcsolatos további információkért tekintse meg ezt az útmutatót: [Gépi](./how-to-log-pipelines-application-insights.md) tanulási folyamatok hibakeresése és hibaelhárítása a Application Insights

## <a name="interactive-debugging-with-visual-studio-code"></a>Interaktív hibakeresés az Visual Studio Code-ban

Bizonyos esetekben előfordulhat, hogy interaktív módon kell hibakeresést végeznie a gépi tanulási folyamatban használt Python-kódon. A Visual Studio Code (VS Code) és a debugpy használatával csatolhatja a kódot, miközben az a betanító környezetben fut. További információért látogasson el a VS Code interaktív [hibakeresési útmutatóját.](how-to-debug-visual-studio-code.md#debug-and-troubleshoot-machine-learning-pipelines)

## <a name="next-steps"></a>Következő lépések

* A használatával kapcsolatos teljes oktatóanyagért `ParallelRunStep` [lásd: Oktatóanyag: Azure Machine Learning-folyamat létrehozása kötegelt pontozáshoz.](tutorial-pipeline-batch-scoring-classification.md)

* Az automatizált gépi tanulás gépi tanulási folyamatokban való használatát bemutató teljes példa: Automatizált gépi tanulás használata egy Azure Machine Learning [a Pythonban.](how-to-use-automlstep-in-pipelines.md)

* Az [azureml-pipelines-core](/python/api/azureml-pipeline-core/) csomaggal és az [azureml-pipelines-steps csomaggal](/python/api/azureml-pipeline-steps/) kapcsolatos segítségért tekintse meg az SDK-referenciát.

* Tekintse meg a tervezői [kivételek és hibakódok listáját.](algorithm-module-reference/designer-error-codes.md)