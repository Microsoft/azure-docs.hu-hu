---
title: Biztonságos adathozzáférés a felhőben
titleSuffix: Azure Machine Learning
description: Megtudhatja, hogyan csatlakozhat biztonságosan az Azure-beli adattárolóhoz Azure Machine Learning adattárakkal és adatkészletekkel.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: nibaccam
author: nibaccam
ms.author: nibaccam
ms.date: 08/31/2020
ms.custom: devx-track-python, data4ml
ms.openlocfilehash: 601be8409db22162a410d481e6609d378718a7b4
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/09/2021
ms.locfileid: "102503589"
---
# <a name="secure-data-access-in-azure-machine-learning"></a>Adatelérés védelme Azure Machine Learning

A Azure Machine Learning megkönnyíti a felhőben tárolt adataihoz való kapcsolódást. Absztrakt réteget biztosít a mögöttes tárolási szolgáltatáshoz, így biztonságosan férhet hozzá és dolgozhat az adataival anélkül, hogy kódot kellene írnia a tárolási típusra. A Azure Machine Learning a következő adatkezelési képességeket is biztosítja:

*    Együttműködés a pandák és a Spark DataFrames
*    Az adatvonal verziószámozása és nyomon követése
*    Adatfelirat 
*    Adateltérések monitorozása
    
## <a name="data-workflow"></a>Adatmunkafolyamat

Ha készen áll a felhőalapú tárolási megoldásban tárolt adatfelhasználásra, javasoljuk, hogy a következő adattovábbítási munkafolyamatot ajánljuk. Ez a munkafolyamat feltételezi, hogy rendelkezik [Azure Storage-fiókkal](../storage/common/storage-account-create.md?tabs=azure-portal) és-adattal az Azure felhőalapú tárolási szolgáltatásában. 

1. Hozzon létre egy [Azure Machine learning adattárt](#datastores) a kapcsolódási adatok Azure Storage-ba való tárolásához.

2. Ebből az adattárból hozzon létre egy [Azure Machine learning adatkészletet](#datasets) , amely egy adott fájl (ok) ra mutat a mögöttes tárolóban. 

3. Ha ezt az adatkészletet a Machine learning-kísérletben szeretné használni, a következő lehetőségek közül választhat:
    1. Csatlakoztassa a kísérlet számítási céljához a modell betanításához.

        **OR** 

    1. Használja közvetlenül a Azure Machine Learning-megoldásokban, például az automatikus gépi tanulás (automatizált ML) kísérleteket, a gépi tanulási folyamatokat vagy a [Azure Machine learning designert](concept-designer.md).

4. Hozzon létre [adatkészlet-figyelőket](#drift) a modell kimeneti adatkészletéhez az adateltolódás észleléséhez. 

5. Ha az adateltolódás észlelhető, frissítse a bemeneti adatkészletet, és ennek megfelelően módosítsa a modellt.

Az alábbi ábra a javasolt munkafolyamat vizuális bemutatását mutatja be.

![A diagram megjeleníti az Azure Storage szolgáltatást, amely egy adatkészletbe áramló adattárba kerül. Az adatkészlet a modell betanításba kerül, amely adateltolódásba kerül, és az adatkészletbe kerül vissza.](./media/concept-data/data-concept-diagram.svg)

<a name="datastores"></a>
## <a name="connect-to-storage-with-datastores"></a>Kapcsolódás a tárolóhoz adattárral

Azure Machine Learning adattárolók biztonságosan megőrzik a kapcsolódási adatokat az Azure-beli adattároláshoz, így nem kell azt a parancsfájlokba írnia. [Regisztráljon, és hozzon létre egy adattárt](how-to-access-data.md) a Storage-fiókhoz való egyszerű kapcsolódáshoz, és a mögöttes tárolási szolgáltatásban lévő adatokat. 

Az Azure-ban támogatott felhőalapú tárolási szolgáltatások, amelyek adattárként regisztrálhatók:

+ Azure Blob-tároló
+ Azure-fájlmegosztás
+ Azure Data Lake
+ Azure Data Lake Gen2
+ Azure SQL Database
+ Azure Database for PostgreSQL
+ Databricks fájlrendszer
+ Azure Database for MySQL

>[!TIP]
> Az adattárolók létrehozásának általánosan elérhető funkciói hitelesítő adatokat igényelnek a tárolási szolgáltatások, például az egyszerű szolgáltatásnév vagy a közös hozzáférésű aláírás (SAS) tokenek eléréséhez. Ezeket a hitelesítő adatokat azok a felhasználók érhetik el, akik *olvasói* hozzáféréssel rendelkeznek a munkaterülethez. <br><br>Ha ez problémát jelent,  [hozzon létre egy adattárolót, amely identitás-alapú adatelérést használ a Storage Services (előzetes verzió) szolgáltatáshoz](how-to-identity-based-data-access.md). Ez a funkció egy [kísérleti](/python/api/overview/azure/ml/#stable-vs-experimental) előzetes verzió, és bármikor megváltozhat.

<a name="datasets"></a>
## <a name="reference-data-in-storage-with-datasets"></a>A tárolóban lévő adatkészletekkel való adatforrások

Azure Machine Learning adathalmazok nem másolják az adatokat. Adatkészlet létrehozásával a Storage szolgáltatásban lévő adatokra mutató hivatkozást, valamint a hozzá tartozó metaadatok másolatát is létrehozhatja. 

Mivel az adathalmazok kiértékelése a lustán történik, és az adatokat a meglévő helyükön maradják,

* További tárolási költségek nem merülnek fel.
* Ne kockáztatja véletlenül az eredeti adatforrások módosítását.
* Javítsa a ML-munkafolyamatok teljesítményének sebességét.

Ha a tárolóban lévő adatokkal szeretne kommunikálni, [hozzon létre egy adatkészletet](how-to-create-register-datasets.md) , amely az adatokat a gépi tanulási feladatokhoz tartozó, fogyóeszközök szerinti objektummá csomagolja. Regisztrálja az adatkészletet a munkaterületen, hogy az adatfeldolgozási bonyolultság nélkül ossza meg és használja fel a különböző kísérletek között.

Az adatkészletek helyi fájlokból, nyilvános URL-címekből, [Azure Open-adatkészletből](https://azure.microsoft.com/services/open-datasets/)vagy Azure Storage-szolgáltatásokból hozhatók létre adattárolók használatával. 

Két típusú adatkészlet létezik: 

+ A [FileDataset](/python/api/azureml-core/azureml.data.file_dataset.filedataset) egy vagy több fájlt hivatkozik az adattárolókban vagy a nyilvános URL-címeken. Ha az adatok már ki vannak takarítva, és használatra készen állnak a betanítási kísérletekben, [letöltheti vagy csatlakoztathatja](how-to-train-with-datasets.md#mount-files-to-remote-compute-targets) a FileDatasets által hivatkozott fájlokat a számítási célra.

+ A [TabularDataset](/python/api/azureml-core/azureml.data.tabulardataset) táblázatos formátumban jeleníti meg az adatokat a megadott fájl vagy fájlok listájának elemzésével. A TabularDataset egy Panda vagy Spark DataFrame is betöltheti további manipuláció és tisztítás céljából. A TabularDatasets létrehozásához használható adatformátumok teljes listáját a [TabularDatasetFactory osztályban](/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory)tekintheti meg.

Az adatkészletek további funkciói a következő dokumentációban találhatók:

+ [Az adatkészlet verziószáma és nyomon követése](how-to-version-track-datasets.md) .
+ [Figyelje az adathalmazt](how-to-monitor-datasets.md) , hogy segítsen az adateltolódás észlelésében.    

## <a name="work-with-your-data"></a>Az adataival dolgozhat

Az adatkészletekkel számos gépi tanulási feladatot hajthat végre Azure Machine Learning funkciókkal való zökkenőmentes integráció révén. 

+ [Adatcímkéző projekt](#label)létrehozása.
+ A gépi tanulási modellek betanítása:
     + [automatizált ML-kísérletek](how-to-use-automated-ml-for-ml-models.md)
     + a [tervező](tutorial-designer-automobile-price-train-score.md#import-data)
     + [notebookok](how-to-train-with-datasets.md)
     + [Azure Machine Learning folyamatok](./how-to-create-machine-learning-pipelines.md)
+ Az adatkészletek a [gépi tanulási folyamatokban](./how-to-create-machine-learning-pipelines.md)a [Batch-következtetéssel](./tutorial-pipeline-batch-scoring-classification.md) való pontozáshoz érhetők el.
+ Adatkészlet-figyelő beállítása az [adateltolódás](#drift) észleléséhez.

<a name="label"></a>

## <a name="label-data-with-data-labeling-projects"></a>Adatcímkéző projektekkel rendelkező adatfeliratok

A nagyméretű adatmennyiségek címkézése gyakran fejfájást eredményezett a gépi tanulási projektekben. A számítógépes látási összetevőkkel, például a képbesorolással vagy az objektum-észleléssel rendelkezők általában több ezer képet és a hozzájuk tartozó címkéket igényelnek.

Azure Machine Learning központi helyet biztosít a címkézési projektek létrehozásához, kezeléséhez és figyeléséhez. A projektek címkézése segít az adatok, címkék és csoporttagok koordinálásában, így hatékonyabban kezelheti a címkézési feladatokat. A jelenleg támogatott feladatok a képbesorolás, a többcímkés vagy a többosztályos, valamint a kötött mezőkkel rendelkező objektumok azonosítása.

Hozzon létre egy [adatcímkéző projektet](how-to-create-labeling-projects.md), és exportálja az adatkészletet a gépi tanulási kísérletekben való használatra.

<a name="drift"></a>

## <a name="monitor-model-performance-with-data-drift"></a>Modell teljesítményének figyelése az adateltolódással

A gépi tanulás kontextusában az adateltolódás a modellben a teljesítmény romlását eredményező bemeneti adatok változása. Ez az egyik legfontosabb ok, hogy a modell pontossága az idő múlásával csökken, így az adateltolódás monitorozása segít a modell teljesítményével kapcsolatos problémák észlelésében.

Az adatkészlet létrehozása című cikkből megtudhatja, hogyan azonosíthatja és [figyelheti](how-to-monitor-datasets.md) az adateltolódást az adatkészletekben lévő új adatokat.

## <a name="next-steps"></a>Következő lépések 

+ Hozzon létre egy adatkészletet a Azure Machine Learning Studióban vagy a Python SDK-val az [alábbi lépések segítségével.](how-to-create-register-datasets.md)
+ A [minta-jegyzetfüzetekkel](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/work-with-data/)kipróbálhatja az adatkészlet tanítási példáit.