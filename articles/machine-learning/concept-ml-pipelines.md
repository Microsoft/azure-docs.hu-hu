---
title: Mik azok a gépi tanulási folyamatok?
titleSuffix: Azure Machine Learning
description: Megtudhatja, hogyan segítik a gépi tanulási folyamatok a gépi tanulási munkafolyamatok felépítését, optimalizálását és kezelését.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: laobri
author: lobrien
ms.date: 02/26/2021
ms.custom: devx-track-python
ms.openlocfilehash: 57f5da06909436e0cbce92559c29c309ca9e20e3
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2021
ms.locfileid: "107819231"
---
# <a name="what-are-azure-machine-learning-pipelines"></a>Mik azok Azure Machine Learning folyamatok?

Ebből a cikkből megtudhatja, hogyan segít egy gépi tanulási folyamat a gépi tanulási munkafolyamatok felépítésében, optimalizálásában és kezelésében. 

<a name="compare"></a>
## <a name="which-azure-pipeline-technology-should-i-use"></a>Melyik Azure-folyamattechnológiát használjam?

Az Azure-felhő számos különböző típusú folyamatot biztosít, amelyek mindegyikének más a célja. Az alábbi táblázatban a különböző folyamatok és azok mire valók:

| Eset | Elsődleges személy | Azure-ajánlat | OSS-ajánlat | Canonical pipe | Erősségeit | 
| -------- | --------------- | -------------- | ------------ | -------------- | --------- | 
| Modellvezénylés (gépi tanulás) | Adatelemző | Azure Machine Learning folyamatok | Kubeflow-folyamatok | Adatmodell > | Terjesztés, gyorsítótárazás, kód– első, újbóli felhasználás | 
| Adatvezénylés (adat-előkészítés) | Adatszakértő | [Azure Data Factory-folyamatok](../data-factory/concepts-pipelines-activities.md) | Apache Airflow | Data -> Data | Erősen típusos mozgás, adatközpontú tevékenységek |
| Kód & alkalmazások vezénylése (CI/CD) | Alkalmazásfejlesztő / Ops | [Azure Pipelines](https://azure.microsoft.com/services/devops/pipelines/) | Jenkins | Kód + Modell -> App/Service | A legtöbb nyitott és rugalmas tevékenységtámogatás, jóváhagyási várólisták, fázisok a beszabadolással | 

## <a name="what-can-machine-learning-pipelines-do"></a>Mire valók a gépi tanulási folyamatok?

A Azure Machine Learning folyamat egy teljes gépi tanulási feladat egymástól függetlenül végrehajtható munkafolyamata. Az alfeladatok lépések sorozataként vannak beágyazva a folyamatba. A Azure Machine Learning folyamat lehet olyan egyszerű is, mint egy Python-szkriptet hív meg, így _bármit_ képes megtenni. A folyamatoknak _a_ következő gépi tanulási feladatokra kell összpontosítaniuk:

+ Adat-előkészítés, beleértve az importálást, az érvényesítést és a tisztítást, a kezelést és az átalakítást, a normalizálást és az előkészítést
+ Betanítás konfigurálása, beleértve az argumentumok, a fájlok elérési útvonalai és a naplózási/jelentéskészítési konfigurációk paraméterezését
+ Hatékony és ismétlődő betanítás és érvényesség. A hatékonyságot adott adat részkészletek, különböző hardveres számítási erőforrások, elosztott feldolgozás és folyamatfigyelés meghatározása segítheti
+ Üzembe helyezés, beleértve a verziószámozást, a méretezést, a kiépítést és a hozzáférés-vezérlést

A független lépések lehetővé teszik, hogy egyszerre több adattudós dolgozzon ugyanazon a folyamaton a számítási erőforrások túladózása nélkül. A különálló lépések megkönnyítik a különböző számítási típusok/méretek használatát az egyes lépésekhez.

A folyamat megtervése után gyakran finomhangoljuk a folyamat betanítás ciklusát. Amikor újrafuttat egy folyamatot, a futtatás az újrafuttatható lépésekre ugrik, például egy frissített betanító szkriptre. A rendszer kihagyja az újrafuttatni nem szükséges lépéseket. 

A folyamatok esetében dönthet úgy, hogy különböző hardvereket használ a különböző feladatokhoz. Az Azure koordinálja a különböző [használt](concept-azure-machine-learning-architecture.md) számítási célokat, így a köztes adatok zökkenőmentesen áramolnak lefelé irányuló számítási célokhoz.

A [folyamatkísérletek](./how-to-log-view-metrics.md) metrikai közvetlenül a Azure Portal a munkaterület kezdőlapján [(előzetes verzió) követhetők](https://ml.azure.com)nyomon. A folyamat közzététele után konfigurálhat egy REST-végpontot, amellyel bármely platformról vagy veremből újrafuttathatja a folyamatot.

Röviden, a gépi tanulási életciklus összetett feladatai a folyamatokkal is segíthetnek. Más Azure-folyamattechnológiáknak is megvan a saját erősségeik. [Azure Data Factory folyamatok](../data-factory/concepts-pipelines-activities.md) kitűnően működnek együtt az adatokkal, és az [Azure Pipelines](https://azure.microsoft.com/services/devops/pipelines/) a megfelelő eszköz a folyamatos integrációhoz és üzembe helyezéshez. Ha azonban a gépi tanulásra koncentrál, Azure Machine Learning folyamat valószínűleg a legjobb választás a munkafolyamat igényeinek. 

### <a name="analyzing-dependencies"></a>Függőségek elemzése

Számos programozási ökoszisztéma tartalmaz olyan eszközöket, amelyek összehangolják az erőforrás-, kódtár- vagy fordítási függőségeket. Ezek az eszközök általában fájl-időbélyegeket használnak a függőségek kiszámításához. Ha egy fájl módosul, csak a fájl és annak függői frissülnek (letöltve, újra fel lehet őket tölteni vagy csomagolni). Azure Machine Learning folyamatok kiterjesztik ezt a fogalmat. A hagyományos buildeszközökhöz hasonló a folyamatok a lépések közötti függőségeket számítják ki, és csak a szükséges újraszámításokat hajtsák végre. 

A függőségelemzés a Azure Machine Learning azonban kifinomultabb, mint az egyszerű időbélyegek. Minden lépés más hardver- és szoftverkörnyezetben futhat. Az adatok előkészítése időigényes folyamat lehet, de nem kell nagy teljesítményű GPU-kkal hardveren futnia, bizonyos lépésekhez szükség lehet az operációs rendszer specifikus szoftverére, érdemes lehet elosztott betanítást használni stb. 

Azure Machine Learning automatikusan összehangolja a folyamat lépései közötti összes függőséget. Ez a vezénylés magában foglalhatja a Docker-rendszerképek fel- és lesorakorálását, a számítási erőforrások csatolását és leválasztást, valamint az adatok konzisztens és automatikus áthelyezését a lépések között.

### <a name="coordinating-the-steps-involved"></a>Az érintett lépések koordinálása

Objektum létrehozásakor és futtatásakor a következő magas szintű `Pipeline` lépésekre lesz szükség:

+ A szolgáltatás minden lépéshez kiszámítja a következő követelményeket:
    + Hardveres számítási erőforrások
    + Operációsrendszer-erőforrások (Docker-rendszerkép(ék))
    + Szoftvererőforrások (Conda/virtualenv-függőségek)
    + Adatbemenetek 
+ A szolgáltatás meghatározza a lépések közötti függőségeket, és dinamikus végrehajtási gráfot eredményez
+ A végrehajtási gráf minden csomópontjának futtatásakor:
    + A szolgáltatás konfigurálja a szükséges hardver- és szoftverkörnyezetet (például a meglévő erőforrások felhasználását)
    + A lépés lefut, és naplózási és monitorozási információkat biztosít az azt tartalmazó `Experiment` objektumhoz
    + Amikor a lépés befejeződik, a kimenetek elő vannak készítve bemenetként a következő lépéshez, és/vagy tárolóba vannak írva
    + A már nem szükséges erőforrások véglegesül és le vannak különítve

![Folyamat lépései](./media/concept-ml-pipelines/run_an_experiment_as_a_pipeline.png)

## <a name="building-pipelines-with-the-python-sdk"></a>Folyamatok létrehozása a Python SDK-val

A Azure Machine Learning [Python SDK-ban](/python/api/overview/azure/ml/install)a folyamat egy Python-objektum, amely a modulban van `azureml.pipeline.core` meghatározva. A [Pipeline objektum](/python/api/azureml-pipeline-core/azureml.pipeline.core.pipeline%28class%29) egy vagy több [PipelineStep](/python/api/azureml-pipeline-core/azureml.pipeline.core.builder.pipelinestep) objektum rendezett sorozatát tartalmazza. A osztály absztrakt, és a tényleges lépések olyan alosztályok lesznek, mint a `PipelineStep` [EstimatorStep,](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.estimatorstep) [a PythonScriptStep](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.pythonscriptstep)vagy a [DataTransferStep.](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.datatransferstep) A [ModuleStep](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.modulestep) osztály a folyamatok között megosztható lépések újrahasználható sorozatát tartalmazza. A `Pipeline` egy részeként `Experiment` fut.

Az Azure Machine Learning-folyamat egy Azure Machine Learning munkaterülethez, a folyamatlépés pedig a munkaterületen elérhető számítási célhoz van társítva. További információ: [Munkaterületek](./how-to-manage-workspace.md) létrehozása és Azure Machine Learning a Azure Portal vagy Mik azok a számítási célok a [Azure Machine Learning?](./concept-compute-target.md).

### <a name="a-simple-python-pipeline"></a>Egy egyszerű Python-folyamat

Ez a kódrészlet a létrehozásához és futtatásához szükséges objektumokat és hívásokat jeleníti `Pipeline` meg:

```python
ws = Workspace.from_config() 
blob_store = Datastore(ws, "workspaceblobstore")
compute_target = ws.compute_targets["STANDARD_NC6"]
experiment = Experiment(ws, 'MyExperiment') 

input_data = Dataset.File.from_files(
    DataPath(datastore, '20newsgroups/20news.pkl'))
prepped_data_path = OutputFileDatasetConfig(name="output_path")

dataprep_step = PythonScriptStep(
    name="prep_data",
    script_name="dataprep.py",
    source_directory="prep_src",
    compute_target=compute_target,
    arguments=["--prepped_data_path", prepped_data_path],
    inputs=[input_dataset.as_named_input('raw_data').as_mount() ]
    )

prepped_data = prepped_data_path.read_delimited_files()

train_step = PythonScriptStep(
    name="train",
    script_name="train.py",
    compute_target=compute_target,
    arguments=["--prepped_data", prepped_data],
    source_directory="train_src"
)
steps = [ dataprep_step, train_step ]

pipeline = Pipeline(workspace=ws, steps=steps)

pipeline_run = experiment.submit(pipeline)
pipeline_run.wait_for_completion()
```

A kódrészlet a gyakori Azure Machine Learning, egy `Workspace` , egy , egy `Datastore` [ComputeTarget és](/python/api/azureml-core/azureml.core.computetarget)egy objektummal `Experiment` kezdődik. Ezután a kód létrehozza a és a `input_data` `prepped_data_path` objektumokat. A `input_data` a [FileDataset egy példánya,](/python/api/azureml-core/azureml.data.filedataset) a `prepped_data_path` pedig az [OutputFileDatasetConfig példánya.](/python/api/azureml-core/azureml.data.output_dataset_config.outputfiledatasetconfig) Az alapértelmezett viselkedés az, hogy a kimenetet az adattárba másolja a elérési úton, ahol a a futtatás azonosítója, és egy automatikusan létrehozott érték, ha a fejlesztő nem adja `OutputFileDatasetConfig` `workspaceblobstore` `/dataset/{run-id}/{output-name}` `run-id` `output-name` meg.

Az adatelőkészítési kód (nem látható) tagolt fájlokat ír a `prepped_data_path` következőbe: . Az adatelőkészítési lépés kimenetei a `prepped_data` betanítás lépéseként vannak átkedve. 

A tömb `steps` a és `PythonScriptStep` a tömböt `dataprep_step` `train_step` tartalmazza. Azure Machine Learning a és a adatfüggőségét a előtt `prepped_data` `dataprep_step` fogja `train_step` futtatni. 

Ezután a kód példányosított maga az objektum, a munkaterület és a `Pipeline` lépések tömbben. A `experiment.submit(pipeline)` hívása megkezdi az Azure ML-folyamat futtatását. A blokkok `wait_for_completion()` hívása, amíg a folyamat be nem fejeződik. 

Ha többet szeretne megtudni a folyamat adatokhoz [](concept-data.md) való csatlakoztatásával kapcsolatban, olvassa el az Adatelérés az Azure Machine Learning és az Adatok mozgatása ml-folyamat lépéseibe és között [(Python) cikket.](how-to-move-data-in-out-of-pipelines.md) 

## <a name="building-pipelines-with-the-designer"></a>Folyamatok létrehozása a tervezővel

A vizuális tervezőfelületet előnyben részesítő fejlesztők a Azure Machine Learning tervezővel hozhatnak létre folyamatokat. Ezt az eszközt a munkaterület **kezdőlapján** található Designer kiválasztásával lehet elérni.  A tervezővel húzással húzhatja a lépéseket a tervezőfelületre. 

A folyamatok vizuális tervezésekor a lépés bemenetei és kimenetei látható módon jelennek meg. Áthúzhatja az adatkapcsolatokat, így gyorsan megértheti és módosíthatja a folyamat adatfolyamát.

![Azure Machine Learning példa tervezőre](./media/concept-designer/designer-drag-and-drop.gif)

## <a name="key-advantages"></a>Fő előnyök

A folyamatok gépi tanulási munkafolyamatokhoz való használatának fő előnyei a következőek:

|Fő előny|Description|
|:-------:|-----------|
|**Felügyelet nélküli &nbsp; futtatás**|Ütemezési lépések párhuzamos vagy egymás után, megbízható és felügyelet nélküli futtatáshoz. Az adatok előkészítése és modellezése akár napokig vagy hetekig is tart, a folyamatok pedig lehetővé teszik, hogy a folyamat futása közben más feladatokra koncentráljon. |
|**Heterogén számítás**|Több folyamatot is használhat, amelyek megbízhatóan koordinálva vannak a heterogén és skálázható számítási erőforrások és tárolási helyek között. A rendelkezésre álló számítási erőforrások hatékony felhasználása az egyes folyamat lépéseinek különböző számítási célokon való futtatásával, például a HDInsighton, a GPU Data Science virtuális gépeken és a Databricksen.|
|**Újrahasználhatóság**|Folyamatsablonok létrehozása adott forgatókönyvekhez, például újraképzéshez és kötegelt pontozáshoz. Közzétett folyamatok aktiválása külső rendszerekből egyszerű REST-hívásokkal.|
|**Nyomon követés és verziószámozás**|Az adatok és az eredmények elérési útjának manuális nyomon követése helyett az iterálás során használja a pipelines SDK-t az adatforrások, bemenetek és kimenetek explicit elnevezéséhez és verziószámozásához. A jobb hatékonyság érdekében a szkripteket és az adatokat külön is kezelheti.|
| **Modularitás** | A problémák területeinek elkülönítése és a változások elkülönítése lehetővé teszi a szoftverek gyorsabb, jobb minőségű fejlődését. | 
|**Együttműködés**|A folyamatok lehetővé teszik az adattudósok számára, hogy együttműködjön a gépi tanulási tervezési folyamat minden területén, miközben egyidejűleg dolgozhatnak a folyamat lépésein.|

## <a name="next-steps"></a>Következő lépések

Azure Machine Learning a folyamatok hatékony eszközök, amelyek a fejlesztés korai szakaszaiban elkezdnek értéket kihozni. Az érték a csapat és a projekt növekedésével együtt növekszik. Ez a cikk azt ismertette, hogyan vannak megadva a folyamatok a Azure Machine Learning Python SDK-val, és hogyan vannak vezényelve az Azure-ban. Már látott néhány egyszerű forráskódot, és bemutattunk néhány rendelkezésre álló `PipelineStep` osztályt. Tudnia kell, hogy mikor érdemes a Azure Machine Learning, és hogyan futtatja őket az Azure. 

+ Ismerje meg, hogyan [hozhatja létre első folyamatát.](./how-to-create-machine-learning-pipelines.md)

+ Ismerje meg, hogyan [futtathat kötegelt előrejelzéseket nagy méretű adatokon.](tutorial-pipeline-batch-scoring-classification.md )

+ A folyamatmag és [](/python/api/azureml-pipeline-core/) a folyamat lépéseit az SDK referencia-dokumentumokban [láthatja.](/python/api/azureml-pipeline-steps/)

+ Próbálja ki a példa Jupyter-notebookokat, amelyek a [Azure Machine Learning mutatják be.](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines) Ismerje meg, hogyan [futtathat jegyzetfüzeteket a szolgáltatás felfedezéséhez.](samples-notebooks.md)