---
title: Mik azok a gépi tanulási folyamatok?
titleSuffix: Azure Machine Learning
description: Ismerje meg, hogyan hozhat létre, optimalizálhat és kezelhet gépi tanulási munkafolyamatokat a gépi tanulási folyamatok segítségével.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: laobri
author: lobrien
ms.date: 02/26/2021
ms.custom: devx-track-python
ms.openlocfilehash: 584e421b6beac0e4ecfab5b3e3cb735b8465e1b4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "102503521"
---
# <a name="what-are-azure-machine-learning-pipelines"></a>Mik azok a Azure Machine Learning folyamatok?

Ebből a cikkből megtudhatja, hogyan hozhat létre, optimalizálhat és kezelhet gépi tanulási munkafolyamatokat a gépi tanulási folyamatokban. 

<a name="compare"></a>
## <a name="which-azure-pipeline-technology-should-i-use"></a>Milyen Azure-adatcsatorna-technológiát használok?

Az Azure Cloud különböző típusú folyamatokat kínál, amelyek mindegyike más célra szolgál. A következő táblázat felsorolja a különböző folyamatokat, és azt, hogy mire szolgálnak:

| Eset | Elsődleges persona | Azure-ajánlat | OSS-ajánlat | Kanonikus cső | Erősségeit | 
| -------- | --------------- | -------------- | ------------ | -------------- | --------- | 
| Modell-előkészítés (gépi tanulás) | Adatelemző | Azure Machine Learning folyamatok | Kubeflow-folyamatok | Adat> modell | Eloszlás, gyorsítótárazás, kód – első, újrafelhasználás | 
| Adat-előkészítés (adat-előkészítés) | Adatszakértő | [Azure Data Factory-folyamatok](../data-factory/concepts-pipelines-activities.md) | Apache légáram | Adat>i adatszolgáltatások | Szigorúan beírt mozgás, az adatközpontú tevékenységek |
| Kód & alkalmazás-előkészítés (CI/CD) | Alkalmazás fejlesztője/Ops | [Azure Pipelines](https://azure.microsoft.com/services/devops/pipelines/) | Jenkins | Code + Model – > alkalmazás/szolgáltatás | A legtöbb nyitott és rugalmas tevékenység támogatása, jóváhagyási várólisták, fázisok és kapuzás | 

## <a name="what-can-machine-learning-pipelines-do"></a>Mire használhatók a gépi tanulási folyamatok?

Az Azure Machine Learning folyamat egy teljes gépi tanulási feladat egymástól független végrehajtható munkafolyamata. Az alfeladatok lépések sorozataként vannak beágyazva a folyamatba. Egy Azure Machine Learning folyamat olyan egyszerű lehet, amely egy Python-szkriptet hív meg, _így bármit elvégezhet._ A folyamatoknak a gépi tanulási feladatokra _kell_ összpontosítaniuk, például:

+ Adat-előkészítés, beleértve az importálást, az érvényesítést és a tisztítást, a kezelést és az átalakítást, a normalizálást és az előkészítést
+ Betanítás konfigurálása, beleértve az argumentumok, a fájlok elérési útvonalai és a naplózási/jelentéskészítési konfigurációk paraméterezését
+ Hatékony és ismételt betanítás és ellenőrzés. A hatékonyság az adott adathalmazok, a különböző hardveres számítási erőforrások, az elosztott feldolgozás és a folyamat figyelésének megadásával lehetséges.
+ Üzembe helyezés, beleértve a verziószámozást, a méretezést, a kiépítést és a hozzáférés-vezérlést

A független lépések lehetővé teszik, hogy több adatszakértő egyszerre működjön ugyanazon a folyamaton, és ne legyenek túlterhelő számítási erőforrások. A külön lépések azt is megkönnyítik, hogy az egyes lépésekhez különböző számítási típusokat/méreteket lehessen használni.

A folyamat megtervezése után a folyamat betanítási ciklusa gyakran nagyobb finomhangolást mutat. Amikor újrafuttat egy folyamatot, a Futtatás az újra futtatandó lépésekre ugrik, például egy frissített tanítási parancsfájlt. Az újrafuttatást nem igénylő lépéseket a rendszer kihagyja. 

A folyamatok esetében választhat, hogy különböző hardvereket használ a különböző feladatokhoz. Az Azure koordinálja az Ön által használt különböző [számítási célokat](concept-azure-machine-learning-architecture.md) , így a köztes adatok zökkenőmentesen áramlanak az alárendelt számítási célokba.

A [folyamat-kísérletek metrikáit](./how-to-track-experiments.md) közvetlenül Azure Portal vagy a munkaterület kezdőlapján [(előzetes verzió)](https://ml.azure.com)követheti nyomon. A folyamat közzététele után beállíthat egy REST-végpontot, amely lehetővé teszi a folyamat újrafuttatását bármely platformról vagy veremből.

Röviden, a gépi tanulási életciklus összes összetett feladata segíthet a folyamatokban. Más Azure-adatcsatorna-technológiák saját erősségei vannak. Az adatkezelést és az [Azure-folyamatokat](https://azure.microsoft.com/services/devops/pipelines/) tartalmazó [Azure Data Factory folyamatok](../data-factory/concepts-pipelines-activities.md) a megfelelő eszköz a folyamatos integrációhoz és üzembe helyezéshez. Ha azonban a hangsúly a gépi tanulás, a munkafolyamatok igényei szerint Azure Machine Learning folyamatok valószínűleg a legjobb választás. 

### <a name="analyzing-dependencies"></a>Függőségek elemzése

Számos programozási ökoszisztémához olyan eszközök tartoznak, amelyek az erőforrás-, a könyvtár-és a fordítási függőségeket hangolják össze. Ezek az eszközök általában a fájlok időbélyegét használják a függőségek kiszámításához. Egy fájl módosításakor a rendszer csak az IT-t és a hozzá tartozó függőket frissíti (letöltött, újrafordított vagy csomagolt). Azure Machine Learning folyamatok kiterjesztik ezt a koncepciót. A hagyományos Build-eszközökhöz hasonlóan a folyamatok is kiszámítják a lépések közötti függőségeket, és csak a szükséges újraszámításokat hajtják végre. 

A függőségek elemzése Azure Machine Learning folyamatokban bonyolultabb, mint az egyszerű időbélyegek esetében. Előfordulhat, hogy minden lépés más hardver-és szoftver-környezetben fut. Az adatelőkészítés lehet időigényes folyamat, de nem kell hatékony GPU-val futtatni a hardveren, bizonyos lépésekhez szükség lehet az operációs rendszer-specifikus szoftverekre, érdemes lehet elosztott képzést használni, és így tovább. 

Azure Machine Learning automatikusan összehangolja a folyamat lépései közötti összes függőséget. Ez a beállítás magában foglalhatja a Docker-rendszerképek rögzítését, a számítási erőforrások csatlakoztatását és leválasztását, valamint az adatok konzisztens és automatikus módon történő áthelyezését.

### <a name="coordinating-the-steps-involved"></a>Az érintett lépések koordinálása

Egy objektum létrehozásakor és futtatásakor `Pipeline` a következő magas szintű lépések történnek:

+ Az egyes lépésekhez a szolgáltatás a következő követelményeket számítja ki:
    + Hardveres számítási erőforrások
    + Operációs rendszer erőforrásai (Docker-rendszerkép (ek))
    + Szoftverek erőforrásai (Conda/virtualenv-függőségek)
    + Adatbemenetek 
+ A szolgáltatás meghatározza a lépések közötti függőségeket, ami dinamikus végrehajtási gráfot eredményez.
+ A végrehajtási gráf egyes csomópontjai a következőket futtatják:
    + A szolgáltatás konfigurálja a szükséges hardver-és szoftver-környezetet (például meglévő erőforrások újrafelhasználása)
    + A lépés futtatása, naplózási és figyelési információk biztosítása a tartalmazó `Experiment` objektumhoz
    + Ha a lépés befejeződik, a kimenetei a következő lépéshez és/vagy a Storage-ba írt bemenetként készülnek.
    + A már nem szükséges erőforrások véglegesítése és leválasztása

![Folyamat lépései](./media/concept-ml-pipelines/run_an_experiment_as_a_pipeline.png)

## <a name="building-pipelines-with-the-python-sdk"></a>Folyamatok fejlesztése a Python SDK-val

A [Azure Machine learning PYTHON SDK](/python/api/overview/azure/ml/install)-ban a folyamat egy Python-objektum, amely a `azureml.pipeline.core` modulban van definiálva. A [folyamat](/python/api/azureml-pipeline-core/azureml.pipeline.core.pipeline%28class%29) objektum egy vagy több [PipelineStep](/python/api/azureml-pipeline-core/azureml.pipeline.core.builder.pipelinestep) -objektum rendezett sorrendjét tartalmazza. Az `PipelineStep` osztály absztrakt, és a tényleges lépések alosztályok lesznek, például [EstimatorStep](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.estimatorstep), [PythonScriptStep](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.pythonscriptstep)vagy [DataTransferStep](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.datatransferstep). A [ModuleStep](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.modulestep) osztály a folyamatok között megosztható lépések újrafelhasználható szakaszát tartalmazza. Egy `Pipeline` fut az a részeként `Experiment` .

Az Azure Machine learning-folyamat egy Azure Machine Learning-munkaterülethez van társítva, és egy folyamat lépés társítva van egy, az adott munkaterületen belül elérhető számítási célhoz. További információ: Azure Machine Learning- [munkaterületek létrehozása és kezelése a Azure Portal](./how-to-manage-workspace.md) , illetve [Mik a Azure Machine learning számítási céljai?](./concept-compute-target.md)

### <a name="a-simple-python-pipeline"></a>Egy egyszerű Python-folyamat

Ez a kódrészlet a létrehozásához és futtatásához szükséges objektumokat és hívásokat mutatja `Pipeline` :

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

A kódrészlet a Common Azure Machine Learning Objects, a, a `Workspace` `Datastore` , a [ComputeTarget](/python/api/azureml-core/azureml.core.computetarget)és a () karakterrel kezdődik `Experiment` . Ezután a kód létrehozza a tárolni kívánt objektumokat `input_data` `prepped_data_path` . A a `input_data` [FileDataset](/python/api/azureml-core/azureml.data.filedataset) egy példánya, a `prepped_data_path` pedig a  [OutputFileDatasetConfig](/python/api/azureml-core/azureml.data.output_dataset_config.outputfiledatasetconfig)példánya. Az `OutputFileDatasetConfig` alapértelmezett viselkedés a kimenet másolása az `workspaceblobstore` adattárba az elérési út alatt `/dataset/{run-id}/{output-name}` , ahol a a `run-id` Futtatás azonosítója, és `output-name` egy automatikusan generált érték, ha a fejlesztő nem adja meg.

Az adatelőkészítési kód (nem látható), a tagolt fájlokat írja a következőre: `prepped_data_path` . Az adatok előkészítési lépésének kimeneteit `prepped_data` a betanítási lépésnek megfelelően adja át a rendszer. 

A tömb `steps` a következő két `PythonScriptStep` s-t tartalmazza: `dataprep_step` és `train_step` . A Azure Machine Learning elemzi az adatfüggőséget, `prepped_data` és korábban futtatja azt `dataprep_step` `train_step` . 

Ezután a kód maga hozza létre az `Pipeline` objektumot, és átadja a munkaterületet és a Steps tömböt. Az `experiment.submit(pipeline)` Azure ml-folyamat futtatásának megkezdésére irányuló hívás. A `wait_for_completion()` folyamat befejeződéséig a blokkoló hívás. 

Ha többet szeretne megtudni a folyamat adataihoz való csatlakoztatásáról, tekintse meg az [adathozzáférés a Azure Machine Learningban](concept-data.md) és az adatáthelyezés a következőbe: [ml-folyamat lépései (Python)](how-to-move-data-in-out-of-pipelines.md)című cikk adatait. 

## <a name="building-pipelines-with-the-designer"></a>Folyamatok fejlesztése a tervezővel

A vizuális tervezési felületet előnyben részesített fejlesztők a Azure Machine Learning Designer használatával hozhatnak létre folyamatokat. Ehhez az eszközhöz a munkaterület kezdőlapján található **tervező** kijelölésen keresztül férhet hozzá.  A Designer lehetővé teszi, hogy a lépéseket a tervezési felületre húzza. 

A folyamatok vizuális megtervezése során láthatók a lépések bemenetei és kimenetei. Az adatkapcsolatok húzásával gyorsan megismerheti és módosíthatja a folyamat adatfolyam.

![Azure Machine Learning Designer – példa](./media/concept-designer/designer-drag-and-drop.gif)

## <a name="key-advantages"></a>Főbb előnyök

A gépi tanulási munkafolyamatok folyamatainak használatának fő előnyei a következők:

|Kulcs előnye|Leírás|
|:-------:|-----------|
|**Felügyelet nélküli &nbsp; futtatások**|A lépéseket párhuzamosan vagy egymás után, megbízható és felügyelet nélküli módon történő futtatásra ütemezze. Az adatok előkészítése és modellezése az elmúlt napokban vagy hetekben is elvégezhető, és a folyamatok lehetővé teszik, hogy a folyamat futása közben más feladatokra összpontosítson. |
|**Különböző számítás**|Több folyamat is használható, amelyek megbízhatóan vannak összehangolva a heterogén és skálázható számítási erőforrások és a tárolási hely között. A rendelkezésre álló számítási erőforrások hatékony felhasználása a különböző számítási célokon, például a HDInsight, a GPU-adatelemzési virtuális gépeken és a Databricks futó egyes folyamatok lépéseinek futtatásával.|
|**Újrafelhasználhatóság**|Hozzon létre folyamat-sablonokat bizonyos forgatókönyvekhez, például az újraképzéshez és a Batch-pontozáshoz. A külső rendszerekből származó közzétett folyamatokat egyszerű REST-hívásokkal aktiválhatja.|
|**Nyomon követés és verziószámozás**|Az adatok és az eredmények elérési útjának manuális nyomon követése helyett használja a folyamatok SDK-t az adatforrások, bemenetek és kimenetek explicit elnevezéséhez és verziójának megkereséséhez. A szkriptek és az információk külön is kezelhetők a hatékonyság növelése érdekében.|
| **Modularitás** | Az érintett területek elkülönítése és a változások elkülönítése lehetővé teszi, hogy a szoftverek gyorsabb ütemben, magasabb színvonalú minőségben fejlődjenek. | 
|**Együttműködés**|A folyamatok lehetővé teszik az adatszakértők számára, hogy működjenek együtt a gépi tanulási tervezési folyamat minden területén, miközben egyidejűleg dolgozhatnak a folyamat lépésein.|

## <a name="next-steps"></a>Következő lépések

A Azure Machine Learning folyamatok egy hatékony létesítmény, amely megkezdi az értékek megvalósítását a korai fejlesztési fázisokban. Az érték növekszik, ahogy a csapat és a projekt növekszik. Ez a cikk azt ismerteti, hogyan vannak megadva a folyamatok a Azure Machine Learning Python SDK-val és az Azure-ban. Láthatta néhány egyszerű forráskódot, és bevezette néhányat a `PipelineStep` rendelkezésre álló osztályokba. Érdemes a Azure Machine Learning folyamatok használatát, valamint azt, hogy az Azure hogyan futtatja őket. 

+ Ismerje meg, hogyan [hozhatja létre első folyamatát](./how-to-create-machine-learning-pipelines.md).

+ Megtudhatja, hogyan [futtathat batch-előrejelzéseket nagy mennyiségű adattal](tutorial-pipeline-batch-scoring-classification.md ).

+ Tekintse meg az SDK-dokumentációt a [pipeline Core](/python/api/azureml-pipeline-core/) és a [folyamat lépéseihez](/python/api/azureml-pipeline-steps/).

+ Próbálja ki például a [Azure Machine learning-folyamatokat](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines)bemutató Jupyter notebookokat. Útmutató [jegyzetfüzetek futtatásához a szolgáltatás megismeréséhez](samples-notebooks.md).