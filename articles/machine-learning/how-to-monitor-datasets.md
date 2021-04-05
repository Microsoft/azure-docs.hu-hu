---
title: Az adatkészletek adateltolódásának észlelése (előzetes verzió)
titleSuffix: Azure Machine Learning
description: Ismerje meg, hogyan állíthatja be az adateltolódás észlelését az Azure learningben. Adatkészletek létrehozása (előzetes verzió), az adateltolódás figyelése és riasztások beállítása.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.reviewer: sgilley
ms.author: copeters
author: lostmygithubaccount
ms.date: 06/25/2020
ms.topic: conceptual
ms.custom: how-to, data4ml, contperf-fy21q2
ms.openlocfilehash: 5a3d16445c5a4276f07f4ed502b9830a10c4ff72
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "102518908"
---
# <a name="detect-data-drift-preview-on-datasets"></a>Adatcsere (előzetes verzió) észlelése az adatkészleteken

Megtudhatja, hogyan figyelheti az adateltolódást, és hogyan állíthatja be a riasztásokat, ha magas a drift  

A Azure Machine Learning adatkészlet figyelők (előzetes verzió) segítségével a következőket teheti:
* **Elemezze az adateltolódást az adataiban** , hogy megértse, hogyan változik az idő múlásával.
* A **modellre vonatkozó adatokat figyelheti** a képzés és az adatkészletek kiszolgálásához.  Először [gyűjtsön modell-adatokból származó üzembe helyezett modelleket](how-to-enable-data-collection.md).
* Az új adatokat az alapkonfiguráció és a célként megadott adatkészlet közötti különbségekkel **figyelheti** .
* **Az adatprofil-funkciók** segítségével követheti nyomon a statisztikai tulajdonságok időbeli változását.
* **Riasztásokat állíthat be az adateltolódással** kapcsolatban a lehetséges problémákkal kapcsolatos korai figyelmeztetésekhez. 
* **[Hozzon létre egy új adatkészlet-verziót] (útmutató – verzió – az adatkészletek** ), amikor megállapítja, hogy az adatokat túl sok sodródott.

Az [Azure Machine learning-adatkészlet](how-to-create-register-datasets.md) a figyelő létrehozásához használatos. Az adatkészletnek tartalmaznia kell egy timestamp oszlopot.

Megtekintheti az adateltolódási metrikákat a Python SDK-val vagy a Azure Machine Learning Studióban.  Az egyéb mérőszámok és adatok az Azure Machine Learning munkaterülethez társított [Azure Application Insights](../azure-monitor/app/app-insights-overview.md) -erőforráson keresztül érhetők el.

> [!IMPORTANT]
> Az adathalmazok adateltolódás-észlelése jelenleg nyilvános előzetes verzióban érhető el.
> Az előzetes verzió szolgáltatási szintű szerződés nélkül van megadva, és nem ajánlott éles számítási feladatokhoz. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Előfeltételek

Az adatkészlet-figyelők létrehozásához és működéséhez a következőkre lesz szüksége:
* Azure-előfizetés. Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy ingyenes fiókot. Próbálja ki a [Azure Machine learning ingyenes vagy fizetős verzióját](https://aka.ms/AMLFree) még ma.
* Egy [Azure Machine learning munkaterület](how-to-manage-workspace.md).
* A [Azure Machine learning SDK for Python telepítve](/python/api/overview/azure/ml/install), amely tartalmazza a azureml-adatkészletek csomagot.
* Strukturált (táblázatos) adatokat az adatokat tartalmazó fájl elérési útja, fájlneve vagy oszlopa által megadott időbélyeggel.

## <a name="what-is-data-drift"></a>Mi az az adateltolódás?

Az adateltolódás az egyik legfontosabb oka annak, hogy a modell pontossága az idő múlásával csökken.  A gépi tanulási modellek esetében az adateltolódás a modellben a teljesítmény romlását eredményező bemeneti adatok változása.  Az adateltolódás figyelése segít észlelni a modell teljesítményével kapcsolatos problémákat.

Az adateltolódás okai a következők:

- A felsőbb rétegbeli folyamatok változásai, például egy lecserélt érzékelő, amely a mérési egységeket hüvelykről centiméterre módosítja. 
- Az adatminőségi problémák, például a hibás szenzorok mindig a 0 értéket olvassák.
- Természetes eltolódás az adatpontokban, például az átlagos hőmérséklet az évszakok szerint változik.
- A szolgáltatások vagy az egyváltozós váltás közötti kapcsolat módosítása. 

Azure Machine Learning leegyszerűsíti a drift észlelését azáltal, hogy egyetlen mérőszámot számít fel az összehasonlított adatkészletek összetettségének kiszámításával.  Ezek az adatkészletek több száz funkcióval és több tízezer sorral rendelkezhetnek. A rendszer a drift észlelése után részletezi, hogy mely funkciók okozzák a driftet.  Ezután megvizsgálhatja a szolgáltatási szint mérőszámait, hogy hibakeresést végezzen, és elkülönítse a drift kiváltó okát.

Ez a legfelső szintű módszer megkönnyíti a hagyományos szabályokon alapuló technikák helyett az adatfigyelést. A szabályokon alapuló technikák (például az engedélyezett adattartományok vagy az engedélyezett egyedi értékek) időigényesek lehetnek.

Azure Machine Learning az adateltolódás észleléséhez és riasztásához használja az adatkészlet-figyelőket.
  
### <a name="dataset-monitors"></a>Adatkészlet-figyelők 

Adatkészlet-figyelővel a következőket teheti:

* Adateltolódás észlelése és riasztása az adatkészletben lévő új adatvesztéssel kapcsolatban.
* A korábbi adateltolódások elemzése.
* Az új adatkezelési idő.

Az adatdrift algoritmus az adatok változásának általános mértékét nyújtja, és jelzi, hogy mely szolgáltatások felelősek a további vizsgálatért. Az adatkészlet-figyelők számos más metrikát hoznak létre az adatkészletben lévő új adatok profilkészítésével `timeseries` . 

Egyéni riasztások állíthatók be a figyelő által az [Azure Application Insights](../azure-monitor/app/app-insights-overview.md)használatával létrehozott összes mérőszámon. Az adatkészlet-figyelők használatával gyorsan elvégezhető az adatvesztés, és csökkentheti a probléma hibakeresésének idejét a valószínűleg előforduló okok azonosításával.  

Elméletileg három fő forgatókönyv áll rendelkezésre az adatkészlet-figyelők beállításához Azure Machine Learningban.

Forgatókönyv | Leírás
---|---
Egy modellnek a betanítási adatokból való eltolódására szolgáló adatok figyelése | Az ebből a forgatókönyvből származó eredmények a modell pontosságára szolgáló proxy figyelésére szolgálnak, mivel a modell pontossága csökkenti a kiszolgált adatoknak a betanítási adatokból való eltolódását.
Idősorozat-adatkészlet figyelése egy korábbi időszakból való eltolódáshoz. | Ez a forgatókönyv általánosabb, és a modell kiépítése során a felsőbb rétegbeli vagy az ahhoz kapcsolódó adatkészletek figyelésére használható.  A célként megadott adatkészlet időbélyeg-oszlopának kell lennie. Az alapadatkészlet bármely táblázatos adatkészlet lehet, amely közösen tartalmazza a célként megadott adatkészletet.
Elemzés végrehajtása a múltbeli adatokon. | Ez a forgatókönyv felhasználható a korábbi adathalmazok megismerésére és a döntések meghozatalára az adatkészlet-figyelők beállításaiban.

Az adatkészlet-figyelők a következő Azure-szolgáltatásoktól függenek.

|Azure-szolgáltatás  |Leírás  |
|---------|---------|
| *Adathalmaz* | A drift Machine Learning adatkészletek használatával kéri le a betanítási adatokat, és összehasonlítja az adatokat a modell betanításához  Az adatok profiljának generálása a jelentett mérőszámok némelyikét, például a min, a maximumot, a különböző értékeket, a különböző értékek számát hozza létre. |
| *Azureml-folyamat és-számítás* | A drift számítási feladatot a azureml-folyamat tárolja.  A feladatot igény szerint, vagy ütemezés szerint kell elindítani a drift monitor létrehozási idején konfigurált számítási feladatok futtatásához.
| *Application bepillantást*| A drift a Machine learning-munkaterülethez tartozó mérőszámokat bocsát ki Application Insights.
| *Azure Blob Storage*| A drift JSON formátumú metrikákat bocsát ki az Azure Blob Storage-ba.

### <a name="baseline-and-target-datasets"></a>Alapkonfiguráció és célként megadott adatkészletek 

Az adateltolódáshoz az [Azure Machine learning-adatkészleteket](how-to-create-register-datasets.md) figyelheti. Adatkészlet-figyelő létrehozásakor az alábbiakra kell hivatkoznia:
* Alapterv-adatkészlet – általában a modell betanítási adatkészlete.
* Cél adatkészlet – általában a bemeneti adatok modellezése – az alapkonfigurációhoz képest az idő múlásával összehasonlítható. Ez az összehasonlítás azt jelenti, hogy a célként megadott adatkészlet esetében meg kell adni egy időbélyeg-oszlopot.

A figyelő összehasonlítja az alaptervet és a célként megadott adatkészleteket.

## <a name="create-target-dataset"></a>Cél adatkészlet létrehozása

A célként megadott adatkészlethez meg kell adni a tulajdonságot úgy, hogy az `timeseries` időbélyegző oszlopot az adatok egyik oszlopával vagy egy, a fájlok elérési útjából származtatott virtuális oszloppal adja meg. Hozza létre az adatkészletet egy időbélyeg használatával a [PYTHON SDK](#sdk-dataset) vagy a [Azure Machine learning Studio](#studio-dataset)segítségével. Az adatkészlethez tartozó tulajdonságok hozzáadásához meg kell adni egy "Timestamp" értéket képviselő oszlopot `timeseries` . Ha az adatok particionálva vannak az időadatokkal (például "{éééé/hh/nn}"), hozzon létre egy virtuális oszlopot az elérésiút-minta beállítással, és állítsa be "partíció időbélyege" értékre a Time Series funkció fontosságának növelése érdekében.

# <a name="python"></a>[Python](#tab/python)
<a name="sdk-dataset"></a>

Az [`Dataset`](/python/api/azureml-core/azureml.data.tabulardataset#with-timestamp-columns-timestamp-none--partition-timestamp-none--validate-false----kwargs-) osztály [`with_timestamp_columns()`](/python/api/azureml-core/azureml.data.tabulardataset#with-timestamp-columns-timestamp-none--partition-timestamp-none--validate-false----kwargs-)  metódus az adatkészlet időbélyegző oszlopát határozza meg.

```python 
from azureml.core import Workspace, Dataset, Datastore

# get workspace object
ws = Workspace.from_config()

# get datastore object 
dstore = Datastore.get(ws, 'your datastore name')

# specify datastore paths
dstore_paths = [(dstore, 'weather/*/*/*/*/data.parquet')]

# specify partition format
partition_format = 'weather/{state}/{date:yyyy/MM/dd}/data.parquet'

# create the Tabular dataset with 'state' and 'date' as virtual columns 
dset = Dataset.Tabular.from_parquet_files(path=dstore_paths, partition_format=partition_format)

# assign the timestamp attribute to a real or virtual column in the dataset
dset = dset.with_timestamp_columns('date')

# register the dataset as the target dataset
dset = dset.register(ws, 'target')
```

> [!TIP]
> Az `timeseries` adatkészletek tulajdonságaival kapcsolatos teljes példaként tekintse meg a [Jegyzetfüzet](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/work-with-data/datasets-tutorial/timeseries-datasets/tabular-timeseries-dataset-filtering.ipynb) vagy az [adatkészletek SDK dokumentációját](/python/api/azureml-core/azureml.data.tabulardataset#with-timestamp-columns-timestamp-none--partition-timestamp-none--validate-false----kwargs-).

# <a name="studio"></a>[Studio](#tab/azure-studio)

<a name="studio-dataset"></a>

Ha Azure Machine Learning Studióval hozza létre az adatkészletet, ügyeljen arra, hogy az adatok elérési útja tartalmazza az adatokhoz tartozó összes almappát, és állítsa be a partíció formátumát.

A következő példában a *NoaaIsdFlorida/2019* almappában található összes adat létrejön, a partíció formátuma pedig a timestamp év, hónap és nap oszlopát adja meg.

[![Partíció formátuma](./media/how-to-monitor-datasets/partition-format.png)](media/how-to-monitor-datasets/partition-format-expand.png)

A **séma** beállításainál adja meg a megadott adatkészlet virtuális vagy valódi oszlopának timestamp oszlopát:

:::image type="content" source="media/how-to-monitor-datasets/timestamp.png" alt-text="Időbélyeg beállítása":::

Ha az adatok a dátum szerint particionálva vannak, ahogy az itt látható, akkor megadhatja a partition_timestamp is.  Ez lehetővé teszi a dátumok hatékonyabb feldolgozását.

:::image type="content" source="media/how-to-monitor-datasets/timeseries-partitiontimestamp.png" alt-text="Partíció időbélyege":::

---

## <a name="create-dataset-monitor"></a>Adatkészlet-figyelő létrehozása

Hozzon létre egy adatkészlet-figyelőt egy új adatkészlet adateltolódásának észleléséhez és riasztásához.  Használja a [PYTHON SDK](#sdk-monitor) -t vagy a [Azure Machine learning studiót](#studio-monitor).

# <a name="python"></a>[Python](#tab/python)
<a name="sdk-monitor"></a> A teljes részletekért tekintse [meg a PYTHON SDK dokumentációját az adateltolódásról](/python/api/azureml-datadrift/azureml.datadrift) . 

Az alábbi példa bemutatja, hogyan hozhat létre egy adatkészlet-figyelőt a Python SDK használatával

```python
from azureml.core import Workspace, Dataset
from azureml.datadrift import DataDriftDetector
from datetime import datetime

# get the workspace object
ws = Workspace.from_config()

# get the target dataset
dset = Dataset.get_by_name(ws, 'target')

# set the baseline dataset
baseline = target.time_before(datetime(2019, 2, 1))

# set up feature list
features = ['latitude', 'longitude', 'elevation', 'windAngle', 'windSpeed', 'temperature', 'snowDepth', 'stationName', 'countryOrRegion']

# set up data drift detector
monitor = DataDriftDetector.create_from_datasets(ws, 'drift-monitor', baseline, target, 
                                                      compute_target='cpu-cluster', 
                                                      frequency='Week', 
                                                      feature_list=None, 
                                                      drift_threshold=.6, 
                                                      latency=24)

# get data drift detector by name
monitor = DataDriftDetector.get_by_name(ws, 'drift-monitor')

# update data drift detector
monitor = monitor.update(feature_list=features)

# run a backfill for January through May
backfill1 = monitor.backfill(datetime(2019, 1, 1), datetime(2019, 5, 1))

# run a backfill for May through today
backfill1 = monitor.backfill(datetime(2019, 5, 1), datetime.today())

# disable the pipeline schedule for the data drift detector
monitor = monitor.disable_schedule()

# enable the pipeline schedule for the data drift detector
monitor = monitor.enable_schedule()
```

> [!TIP]
> Az `timeseries` adatkészletek és adatdrift-detektorok beállításának teljes példáját a [példaként szolgáló jegyzetfüzetben](https://aka.ms/datadrift-notebook)tekintheti meg.


# <a name="studio"></a>[Studio](#tab/azure-studio)
<a name="studio-monitor"></a>

1. Navigáljon a [Studio kezdőlapjára](https://ml.azure.com).
1. Válassza az **adatkészletek** fület a bal oldalon. 
1. Válassza az **adatkészlet-figyelők** elemet.
   ![Figyelési lista](./media/how-to-monitor-datasets/monitor-list.png)

1. Kattintson a **+ figyelő létrehozása** gombra, és folytassa a varázslót **a Tovább gombra kattintva.**  

:::image type="content" source="media/how-to-monitor-datasets/wizard.png" alt-text="Figyelő varázsló létrehozása":::

* **Válassza ki a cél adatkészletet**.  A célként megadott adatkészlet egy olyan táblázatos adatkészlet, amelyben az időbélyegző oszlop van megadva, amelyet az adateltolódáshoz elemez a rendszer. A célként megadott adatkészletnek közös szolgáltatásokkal kell rendelkeznie az alapkonfigurációhoz, és olyan `timeseries` adatkészletnek kell lennie, amelybe a rendszer hozzáfűzi az új adatokat. A célként megadott adatkészletben lévő korábbi adatokat elemezni lehet, vagy az új adatokat lehet figyelni.

* **Válassza az alapadatkészlet lehetőséget.**  Válassza ki azt a táblázatos adatkészletet, amelyet alaptervként kíván használni a célként megadott adatkészlet időbeli összehasonlításához.  Az alapadatkészletnek a célként megadott adatkészlettel közös szolgáltatásokkal kell rendelkeznie.  Válassza ki azt az időtartományt, amely a cél adatkészlet szeletét használja, vagy adjon meg egy külön adatkészletet, amelyet alaptervként kíván használni.

* **Beállítások figyelése**.  Ezek a beállítások az ütemezett adatkészlet-figyelő folyamathoz tartoznak, amelyet a rendszer létrehoz. 

    | Beállítás | Leírás | Tippek | Változtatható | 
    | ------- | ----------- | ---- | ------- |
    | Name | Az adatkészlet figyelő neve. | | No |
    | Funkciók | Azoknak a szolgáltatásoknak a listája, amelyeket az adateltolódás az idő múlásával elemezni fog. | A modell kimeneti funkciójának beállítása a koncepciók eltolódásának mérésére. Ne tartalmazzon olyan funkciókat, amelyek természetesen az idő függvényében sodródnak (hónap, év, index stb.). A szolgáltatások listájának módosítása után a backfill és a meglévő adatdrift-figyelő is megadható. | Yes | 
    | Számítási cél | Azure Machine Learning számítási célt az adatkészlet-figyelő feladatok futtatásához. | | Yes | 
    | Engedélyezés | Az adatkészlet-figyelő folyamatának ütemezett engedélyezése vagy letiltása | Tiltsa le az ütemtervet, hogy elemezze a korábbi adatértékeket a backfill beállítással. Az adatkészlet-figyelő létrehozása után is engedélyezhető. | Yes | 
    | Gyakoriság | A folyamat feladatainak beütemezett és a korábbi adatok elemzéséhez használt gyakoriság, ha backfill fut. A beállítások a következők: naponta, hetente vagy havonta. | Minden Futtatás összehasonlítja a cél adatkészletben lévő adatokat a gyakoriság szerint: <li>Napi: a legutóbbi teljes nap összehasonlítása a célként megadott adatkészlet alapkonfigurációval <li>Hetente: a legutóbbi teljes hét (hétfő – vasárnap) összehasonlítása a célként megadott adatkészlet alapkonfigurációval <li>Havi: a legutóbbi teljes hónap összehasonlítása a célként megadott adatkészlet alapkonfigurációval | No | 
    | Késés | Az adatkészletbe való beérkezéshez szükséges idő (óra). Ha például három napot vesz igénybe az adatkészletet tároló SQL-ADATBÁZISba való beérkezési adat, a késést állítsa 72-re. | Az adatkészlet-figyelő létrehozása után nem módosítható | No | 
    | E-mail-címek | A riasztások e-mail-címei az adateltolódás százalékos küszöbértékének megszegése alapján. | Az e-maileket Azure Monitor küldi el a rendszer. | Yes | 
    | Küszöbérték | Az e-mail riasztások százalékos küszöbértéke az e-mailek küldéséhez. | További riasztások és események a munkaterület társított Application Insights erőforrásának számos más metrikáján is megadhatók. | Yes |

A varázsló befejezése után az eredményül kapott adatkészlet figyelő megjelenik a listában. Válassza ki, hogy megnyissa a figyelő részleteket tartalmazó lapját.

---

## <a name="understand-data-drift-results"></a>Az adateltolódás eredményeinek megismerése

Ez a szakasz az Azure Studio **adatkészletek**  /  **adatkészlet-figyelők** lapján található adatkészlet figyelésének eredményeit jeleníti meg.  A lapon frissítheti a beállításokat, valamint elemezheti a meglévőket az adott időszakra vonatkozóan.  

Kezdje a legfelső szintű betekintéssel az adateltolódás nagyságát, és a további megvizsgálandó funkciók kiemelését.

:::image type="content" source="media/how-to-monitor-datasets/drift-overview.png" alt-text="A drift áttekintése":::


| Metric | Leírás | 
| ------ | ----------- | 
| Adateltolódás nagysága | Az alapterv és a célként megadott adatkészlet időbeli eltolódásának százalékos aránya. A 0 és 100 közötti arányban a 0 érték azt jelzi, hogy az adathalmazok és a 100 azt jelzi, hogy a Azure Machine Learning adateltolódási modellje teljesen megadhatja a két adathalmazt. A rendszer a megfelelő mérési arányban mért zaj miatt várhatóan a gépi tanulási technikákat használja a magnitúdó létrehozásához. | 
| Leggyakoribb drift funkciók | Megjeleníti az adatkészlet azon funkcióit, amelyek a legtöbbet sodródtak, és így a leginkább a drift magnitúdó metrikához járulnak hozzá. Az egyváltozós váltás miatt a szolgáltatások mögöttes eloszlása nem feltétlenül szükséges ahhoz, hogy a szolgáltatás viszonylag magas szintű legyen. |
| Küszöbérték | A beállított küszöbértéken túli adateltolódási mennyiség riasztásokat indít el. Ez a figyelési beállítások között konfigurálható. | 

### <a name="drift-magnitude-trend"></a>Drift nagyságának trendje

Megtudhatja, hogyan tér el az adatkészlet a megadott időszakban a cél adatkészlettől.  Minél közelebb a 100%-hoz, annál több a két adathalmaz eltérő.

:::image type="content" source="media/how-to-monitor-datasets/drift-magnitude.png" alt-text="Drift nagyságának trendje":::

### <a name="drift-magnitude-by-features"></a>Drift magnitúdó a funkciók alapján

Ez a szakasz a kiválasztott szolgáltatás eloszlásának változását, valamint az egyéb statisztikákat az idő múlásával mutatja be.

A célként megadott adatkészlet az idő múlásával is bekerül. Az egyes szolgáltatások alapkonfiguráció-eloszlása közötti statisztikai távolság összehasonlítható a célként megadott adatkészlettel.  Elméletileg ez hasonló az adateltolódás nagyságához.  Ez a statisztikai távolság azonban az összes funkció helyett egy egyéni szolgáltatáshoz tartozik. A min, a Max és a Mean is elérhető.

A Azure Machine Learning Studióban kattintson a gráf egyik sávjára, és tekintse meg az adott dátumra vonatkozó szolgáltatási szint részleteit. Alapértelmezés szerint az alapadatkészlet eloszlása és a legutóbbi futtatási eloszlása is megjelenik ugyanazon funkció alapján.

:::image type="content" source="media/how-to-monitor-datasets/drift-by-feature.gif" alt-text="Drift magnitúdó a funkciók alapján":::

Ezek a metrikák a Python SDK-ban is beolvashatók `get_metrics()` egy objektum metódusával `DataDriftDetector` .

### <a name="feature-details"></a>Szolgáltatás részletei

Végül görgessen le az egyes funkciók részleteinek megtekintéséhez.  A diagram fölötti legördülő listából válassza ki a funkciót, és válassza ki a megtekinteni kívánt metrikát.

:::image type="content" source="media/how-to-monitor-datasets/numeric-feature.gif" alt-text="Numerikus funkciók gráfja és összehasonlítása":::

A diagram metrikái a szolgáltatás típusától függenek.

* Numerikus funkciók

    | Metric | Leírás |  
    | ------ | ----------- |  
    | Wasserstein távolság | Az alapkonfiguráció terjesztésének a célként megadott eloszlásba való átalakításához szükséges minimális mennyiség. |
    | Középérték | A szolgáltatás átlagos értéke. |
    | Minimális érték | A szolgáltatás minimális értéke. |
    | Maximális érték | A szolgáltatás maximális értéke. |

* Kategorikus funkciók
    
    | Metric | Leírás |  
    | ------ | ----------- |  
    | Euklidészi távolság     |  Kategorikus oszlopok esetében kiszámítva. A euklideszi távolság két vektorra van kiszámítva, amely a két adatkészletből származó azonos kategorikus oszlop empirikus eloszlása alapján jön létre. 0 azt jelzi, hogy nincs különbség az empirikus eloszlásokban.  Minél többet tér el a 0 értéktől, annál több ez az oszlop sodródik. A tendenciák megfigyelhetők a metrika idősorozat-ábráján, és hasznosak lehetnek egy sodródó funkció felfedéséhez.  |
    | Egyedi értékek | A szolgáltatás egyedi értékeinek (kardinális) száma. |

Ezen a diagramon válassza ki azt a dátumot, amellyel össze szeretné hasonlítani a szolgáltatások eloszlását a cél és a megjelenített szolgáltatás ezen dátuma között. A numerikus funkciók esetében ez két valószínűségi eloszlást mutat.  Ha a funkció numerikus, akkor megjelenik egy oszlopdiagram.

:::image type="content" source="media/how-to-monitor-datasets/select-date-to-compare.gif" alt-text="Válassza ki a célként összehasonlítani kívánt dátumot":::

## <a name="metrics-alerts-and-events"></a>Metrikák, riasztások és események

A metrikák a Machine learning-munkaterülethez társított [Azure Application Insights](../azure-monitor/app/app-insights-overview.md) -erőforrásban kérhetők le. A Application Insights összes funkcióját használhatja, beleértve az egyéni riasztási szabályok és a műveleti csoportok beállítását is, így például egy e-mail-, SMS-/leküldéses/hang-vagy Azure-függvényt. A részletekért tekintse meg a teljes Application Insights dokumentációját. 

Első lépésként navigáljon a [Azure Portal](https://portal.azure.com) , és válassza ki a munkaterület **Áttekintés** lapját.  A kapcsolódó Application Insights erőforrás a jobb szélen van:

[![Az Azure Portal áttekintése](./media/how-to-monitor-datasets/ap-overview.png)](media/how-to-monitor-datasets/ap-overview-expanded.png)

Válassza a naplók (elemzés) lehetőséget a bal oldali ablaktábla figyelés területén:

![Az Application ininsight áttekintése](./media/how-to-monitor-datasets/ai-overview.png)

Az adatkészlet-figyelő metrikái a következőképpen tárolódnak: `customMetrics` . Az adatkészlet-figyelő beállítása után írhat és futtathat egy lekérdezést a megtekintéséhez:

[![Log Analytics-lekérdezés](./media/how-to-monitor-datasets/simple-query.png)](media/how-to-monitor-datasets/simple-query-expanded.png)

Miután azonosított mérőszámokat a riasztási szabályok beállításához, hozzon létre egy új riasztási szabályt:

![Új riasztási szabály](./media/how-to-monitor-datasets/alert-rule.png)

Meglévő műveleti csoportot is használhat, vagy létrehozhat egy újat, hogy meghatározza a beállított feltételek teljesülése esetén végrehajtandó műveletet:

![Új műveleti csoport](./media/how-to-monitor-datasets/action-group.png)


## <a name="troubleshooting"></a>Hibaelhárítás

Az adatdrift figyelőkkel kapcsolatos korlátozások és ismert problémák:

* A korábbi adatok elemzésének időtartománya a figyelő gyakorisági beállításának 31 intervallumára korlátozódik. 
* A 200 funkcióinak korlátozása, kivéve, ha nincs megadva szolgáltatási lista (az összes funkció használatban van).
* A számítás méretének elég nagynak kell lennie az adatok kezeléséhez.
* Győződjön meg arról, hogy az adatkészlet egy adott figyelő futtatásának kezdési és befejezési dátumán belüli adatokat tartalmaz.
* Az adatkészlet-figyelők csak a 50 vagy több sort tartalmazó adatkészleteken működnek.
* Az adatkészletben lévő oszlopok vagy szolgáltatások a következő táblázatban szereplő feltételek alapján kategorikusnak vagy numerikusnak minősülnek. Ha a szolgáltatás nem felel meg az alábbi feltételeknek – például egy karakterlánc típusú, >100 egyedi értékekkel rendelkező oszlop – a szolgáltatás el lesz dobva az adateltolódási algoritmusból, de a rendszer még mindig felhasználható. 

    | Szolgáltatás típusa | Adattípus | Feltétel | Korlátozások | 
    | ------------ | --------- | --------- | ----------- |
    | Kategorikus | karakterlánc, bool, int, float | A szolgáltatásban található egyedi értékek száma kevesebb, mint 100, és a sorok száma kevesebb, mint 5%. | A Null érték a saját kategóriája. | 
    | Numerikus | int, float | A szolgáltatás értékei numerikus adattípussal rendelkeznek, és nem felelnek meg a kategorikus funkció feltételének. | A szolgáltatás el lett dobva, ha az értékek 15%-a null értékű >. | 

* Ha létrehozott egy adatdrift-figyelőt, de nem látja az adatokat a Azure Machine Learning Studio **adatkészlet-figyelők** lapján, próbálkozzon a következőkkel.

    1. Ellenőrizze, hogy a megfelelő dátumtartomány van-e kiválasztva az oldal tetején.  
    1. Az **adatkészlet-figyelők** lapon válassza a kísérlet hivatkozást a Futtatás állapotának vizsgálatához.  Ez a hivatkozás a tábla jobb szélén található.
    1. Ha a Futtatás sikeresen befejeződött, ellenőrizze az illesztőprogram-naplókat, hogy hány mérőszám lett létrehozva, vagy hogy van-e figyelmeztető üzenet.  A kísérletre való kattintás után keresse meg az illesztőprogram-naplókat a **kimenet + naplók** lapon.

* Ha az SDK `backfill()` -függvény nem hozza ki a várt kimenetet, előfordulhat, hogy egy hitelesítési probléma okozza.  Amikor létrehozza a számítást, hogy átadja ezt a függvényt, ne használja `Run.get_context().experiment.workspace.compute_targets` .  Ehelyett használja a [ServicePrincipalAuthentication](/python/api/azureml-core/azureml.core.authentication.serviceprincipalauthentication) , például az alábbiakat a függvénybe beadott számítási számítás létrehozásához `backfill()` : 

  ```python
   auth = ServicePrincipalAuthentication(
          tenant_id=tenant_id,
          service_principal_id=app_id,
          service_principal_password=client_secret
          )
   ws = Workspace.get("xxx", auth=auth, subscription_id="xxx", resource_group="xxx")
   compute = ws.compute_targets.get("xxx")
   ```

* A modell adatgyűjtője akár 10 percet is igénybe vehet, amíg az adatok megérkeznek a blob Storage-fiókba. Egy parancsfájlban vagy jegyzetfüzetben várjon 10 percet, hogy az alábbi cellák biztosan fussanak.

    ```python
    import time
    time.sleep(600)
    ```

## <a name="next-steps"></a>Következő lépések

* Hozzon létre egy adatkészlet-figyelőt a [Azure Machine learning Studióban](https://ml.azure.com) vagy a [Python-jegyzetfüzetben](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/work-with-data/datadrift-tutorial/datadrift-tutorial.ipynb) .
* Ismerje meg, hogyan állíthatja be az adateltolódást az [Azure Kubernetes szolgáltatásban üzembe helyezett modelleken](./how-to-enable-data-collection.md).
* Adatkészlet-drift figyelők beállítása az [Event Grid](how-to-use-event-grid.md)használatával.