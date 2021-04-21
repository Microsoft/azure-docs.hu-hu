---
title: Adatelsodródás észlelése az adatkészletek között (előzetes verzió)
titleSuffix: Azure Machine Learning
description: Ismerje meg, hogyan állíthatja be az adatsodródás-észlelést az Azure Learningben. Adathalmaz-figyelők létrehozása (előzetes verzió), adateltávolság figyelése és riasztások beállítása.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.reviewer: sgilley
ms.author: copeters
author: lostmygithubaccount
ms.date: 06/25/2020
ms.topic: conceptual
ms.custom: how-to, data4ml, contperf-fy21q2
ms.openlocfilehash: 95fb2dfeea98b988eaeaea43efc4ea44fd6e33fd
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107770308"
---
# <a name="detect-data-drift-preview-on-datasets"></a>Adateltávolás észlelése (előzetes verzió) az adatkészletek között

Ismerje meg, hogyan figyelheti az sodródásokat, és állíthat be riasztásokat, ha az eltérés magas.  

Az Azure Machine Learning (előzetes verzió) figyelőivel a következőre van képes:
* **Elemezheti az adatok eltérését,** hogy megértse, hogyan változik az idő alatt.
* **A modelladatok figyelése** az adatkészletek betanítása és kiszolgálása közötti különbségeket keresve.  Először [gyűjtse össze a modelladatokat az üzembe helyezett modellekből.](how-to-enable-data-collection.md)
* **Figyelje az új adatokat** az alapkonfigurációk és a céladatkészletek közötti különbségek tekintetében.
* **Profil-funkciók az adatokban a** statisztikai tulajdonságok változásának nyomon követéséhez.
* **Állítson be riasztásokat az adatsodródásról** a korai figyelmeztetések és a lehetséges problémák stb. tekintetében. 
* **[Hozzon létre egy új adatkészlet-verziót,](how-to-version-track-datasets.md)** ha megállapítja, hogy az adatok túl sokat távolodtak.

A [figyelő létrehozásához egy Azure Machine Learning-adatkészletet](how-to-create-register-datasets.md) használ a rendszer. Az adatkészletnek tartalmaznia kell egy időbélyeg-oszlopot.

Az eltérési metrikákat a Python SDK-val vagy az Azure Machine Learning stúdió.  Más metrikák és elemzések a munkaterülethez [társított Azure Application Insights-erőforráson](../azure-monitor/app/app-insights-overview.md) keresztül Azure Machine Learning érhetők el.

> [!IMPORTANT]
> Az adatsodródás észlelése jelenleg nyilvános előzetes verzióban érhető el.
> Az előzetes verzió szolgáltatói szerződés nélkül érhető el, éles számítási feladatokhoz pedig nem ajánlott. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Előfeltételek

Az adatkészlet-figyelők létrehozásához és a velük való munkához a következőre van szükség:
* Azure-előfizetés. Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy ingyenes fiókot. Próbálja ki a Azure Machine Learning [ingyenes vagy Azure Machine Learning](https://aka.ms/AMLFree) verzióját.
* Egy [Azure Machine Learning munkaterület.](how-to-manage-workspace.md)
* Az [Azure Machine Learning SDK for Python telepítve van,](/python/api/overview/azure/ml/install)amely tartalmazza az azureml-datasets csomagot.
* Strukturált (táblázatos) adatok az adatok fájlútvonalában, fájlnevében vagy oszlopában megadott időbélyegzővel.

## <a name="what-is-data-drift"></a>Mi az az eltérés?

A sodródás az egyik fő oka annak, hogy a modell pontossága idővel csökken.  A gépi tanulási modellek esetében az sodródás a modellbemeneti adatok változását jelenti, amely a modell teljesítményének romlásához vezet.  A sodródás monitorozása segít a modell teljesítményével kapcsolatos problémák észlelésében.

A sodródás okai többek között a következők:

- A folyamat felfelé irányuló változásai, például egy lecserélt érzékelő, amely hüvelykről centiméterre módosítja a mértékegységeket. 
- Adatminőségi problémák, például egy hibás érzékelő, amely mindig a 0-t olvassa.
- Természetes sodródás az adatokban, például az átlagos hőmérséklet-változás az évszakban.
- A jellemzők közötti változás vagy az eltolás közös használata. 

Azure Machine Learning leegyszerűsíti a sodródásészlelést egyetlen metrika kiszámításával, amely absztrakttá teszi az összehasonlítás alatt álló adatkészletek összetettségét.  Ezek az adathalmazok több száz jellemzővel és több tízezer sorral is lehetnek. A sodródás észlelése után le kell fúrni, hogy mely jellemzők okozzák a sodródást.  Ezután megvizsgálhatja a szolgáltatásszintű metrikákat a hibakeresés és a sodródás kiváltó okának elkülönítése érdekében.

Ez a felülről lefelé megközelítés megkönnyíti az adatok figyelése a hagyományos szabályokon alapuló technikák helyett. A szabályalapú technikák, például az engedélyezett adattartományok vagy az engedélyezett egyedi értékek időigényesek, és sok hiba lehet.

Ebben Azure Machine Learning adatkészlet-figyelők használatával észleli az adatlódásokat, és riasztást küld az adatlódásról.
  
### <a name="dataset-monitors"></a>Adatkészlet-figyelők 

Az adatkészlet-figyelővel a következőre van képes:

* Adatsodródás észlelése és riasztás az adatkészletek új adataiban.
* Előzményadatok elemzése eltéréshez.
* Profil létrehozása az új adatokról az idő alapján.

Az adatsodródási algoritmus átfogóan méri az adatok változását, és jelzi, hogy mely funkciók felelősek a további vizsgálatért. Az adatkészlet-figyelők számos más metrikát is előhoznak az adatkészletben új adatok `timeseries` profilkészítésével. 

Az egyéni riasztások a figyelő által létrehozott összes metrikán beállíthatók az [Azure Application Insights.](../azure-monitor/app/app-insights-overview.md) Az adatkészlet-figyelők segítségével gyorsan észreveheti az adatokkal kapcsolatos problémákat, és csökkentheti a hibakereséshez szükséges időt a valószínű okok azonosításával.  

Fogalmilag három elsődleges forgatókönyv íme az adatkészlet-figyelők beállítására a Azure Machine Learning.

Forgatókönyv | Leírás
---|---
Modell kiszolgálási adatainak figyelése a betanítás adataitól való eltérés érdekében | Az ebből a forgatókönyvből származó eredmények a modell pontosságának proxyfigyeléseként értelmezhetők, mivel a modell pontossága csökken, amikor a kiszolgálói adatok eltávolodnak a betanítás adataitól.
Idősorozat-adatkészlet figyelése az előző időszaktól való eltéréshez. | Ez a forgatókönyv általánosabb, és a modell kiépítése előtt vagy után érintett adatkészletek figyelése is használható.  A céladatkészletnek időbélyegoszloppal kell lennie. Az alapérték bármely táblázatos adatkészlet lehet, amely a céladatkészletben közös jellemzőkkel rendelkezik.
Végezzen elemzést a korábbi adatokon. | Ezzel a forgatókönyvvel megértheti az előzményadatokat, és döntéseket hozhat az adatkészlet-figyelők beállításaiban.

Az adatkészlet-figyelők az alábbi Azure-szolgáltatásoktól függenek.

|Azure-szolgáltatás  |Description  |
|---------|---------|
| *Adathalmaz* | A sodródás Machine Learning adatkészletek segítségével lekéri a betanítás adatait, és összehasonlítja az adatokat a modell betanítása során.  Az adatprofil generálása a jelentett metrikák egy része, például a min, a max, az eltérő értékek és a különböző értékek darabszámának generálása. |
| *Azureml-folyamat és -számítás* | A sodródás számítási feladata az azureml-folyamatban van üzemeltetve.  A feladat igény szerint vagy ütemezés szerint aktiválódik egy, a sodródásfigyelő létrehozásakor konfigurált számításon.
| *Application Insights*| A sodródás metrikákat bocsát Application Insights gépi tanulási munkaterülethez tartozó virtuális gépekre.
| *Azure Blob Storage*| A Drift JSON formátumban bocsát ki metrikákat az Azure Blob Storage-ba.

### <a name="baseline-and-target-datasets"></a>Alapkonfiguráció és céladatkészletek 

Az [Azure Machine Learning-adatkészletek](how-to-create-register-datasets.md) adatsodródásának figyelése. Adatkészlet-figyelő létrehozásakor a következőre fog hivatkozni:
* Alapérték-adatkészlet – általában a modell betanítás adatkészlete.
* A céladatkészletet – általában a modell bemeneti adatait – a rendszer idővel összehasonlítja az alapadatkészlettel. Ez az összehasonlítás azt jelenti, hogy a céladatkészletben meg kell adni egy időbélyegző oszlopot.

A figyelő összehasonlítja az alapkonfigurációt és a céladatkészleteket.

## <a name="create-target-dataset"></a>Céladatkészlet létrehozása

A céladatkészletnek szüksége van a jellemzőre úgy, hogy megadja az időbélyegző oszlopot az adatok egyik oszlopában, vagy egy virtuális oszlopban, amely a fájlok `timeseries` elérésiút-mintázata alapján van kivezetve. Hozza létre az adatkészletet egy időbélyegzővel a [Python SDK-val](#sdk-dataset) vagy a [Azure Machine Learning stúdió.](#studio-dataset) Az "időbélyeget" képviselő oszlopot meg kell adni, hogy jellemzőt adjon az `timeseries` adatkészlethez. Ha az adatok időinformációkat tartalmazó mappastruktúrába (például "{yyyy/MM/dd}") van particionálva, hozzon létre egy virtuális oszlopot az elérésiút-minta beállításával, és állítsa be őket "partíció-időbélyegként" az idősorozat-funkciók fontosságának növelése érdekében.

# <a name="python"></a>[Python](#tab/python)
<a name="sdk-dataset"></a>

A class metódus határozza meg az adatkészlet [`Dataset`](/python/api/azureml-core/azureml.data.tabulardataset#with-timestamp-columns-timestamp-none--partition-timestamp-none--validate-false----kwargs-) [`with_timestamp_columns()`](/python/api/azureml-core/azureml.data.tabulardataset#with-timestamp-columns-timestamp-none--partition-timestamp-none--validate-false----kwargs-)  időbélyegző-oszlopát.

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
> Az adatkészletek jellemző használatának teljes példájéhez tekintse meg a példajegyzetfüzetet vagy az `timeseries` [adatkészletek SDK-dokumentációját.](/python/api/azureml-core/azureml.data.tabulardataset#with-timestamp-columns-timestamp-none--partition-timestamp-none--validate-false----kwargs-) [](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/work-with-data/datasets-tutorial/timeseries-datasets/tabular-timeseries-dataset-filtering.ipynb)

# <a name="studio"></a>[Studio](#tab/azure-studio)

<a name="studio-dataset"></a>

Ha az adatkészletet a Azure Machine Learning stúdió használatával hozza létre, ellenőrizze, hogy az adatok elérési útja tartalmaz-e időbélyegző-információkat, adja meg az összes almappát az adatokkal, és állítsa be a partíció formátumát.

A következő példában a *NoaaIsdHálózat/2019* almappában található összes adat le van taken, és a partícióformátum határozza meg az időbélyegző évét, hónapját és napját.

[![Partícióformátum](./media/how-to-monitor-datasets/partition-format.png)](media/how-to-monitor-datasets/partition-format-expand.png)

A **Sémabeállítások** között adja meg a megadott adatkészlet egy virtuális vagy valós oszlopának időbélyegző-oszlopát:

:::image type="content" source="media/how-to-monitor-datasets/timestamp.png" alt-text="Az időbélyeg beállítása":::

Ha az adatok dátum szerint vannak particionálva, ahogyan itt is, megadhatja a partition_timestamp.  Ez lehetővé teszi a dátumok hatékonyabb feldolgozását.

:::image type="content" source="media/how-to-monitor-datasets/timeseries-partitiontimestamp.png" alt-text="Partíció időbélyege":::

---

## <a name="create-dataset-monitor"></a>Adatkészlet-figyelő létrehozása

Hozzon létre egy adatkészlet-figyelőt, amely észleli az adatsodródást egy új adatkészleten, és riasztást küld rá.  Használja a [Python SDK-t](#sdk-monitor) vagy a [Azure Machine Learning stúdió.](#studio-monitor)

# <a name="python"></a>[Python](#tab/python)
<a name="sdk-monitor"></a> A részletekért tekintse meg [a Python SDK sodródásról készült](/python/api/azureml-datadrift/azureml.datadrift) referenciadokumentációját. 

Az alábbi példa bemutatja, hogyan hozhat létre adatkészlet-figyelőt a Python SDK használatával

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
> Az adatkészlet és az sodródásérzékelő beállításának teljes példájéhez tekintse meg a `timeseries` [példajegyzetfüzetet.](https://aka.ms/datadrift-notebook)


# <a name="studio"></a>[Studio](#tab/azure-studio)
<a name="studio-monitor"></a>

1. Lépjen a [Studio kezdőlapjára.](https://ml.azure.com)
1. Válassza a **bal oldalon az** Adatkészletek lapot. 
1. Válassza **az Adatkészlet-figyelők lehetőséget.**
   ![Figyelőlista](./media/how-to-monitor-datasets/monitor-list.png)

1. Kattintson a **+Figyelő létrehozása gombra,** és folytassa a varázsló lépéseit a Tovább **gombra kattintva.**  

:::image type="content" source="media/how-to-monitor-datasets/wizard.png" alt-text="Figyelő varázsló létrehozása":::

* **Válassza ki a céladatkészletet.**  A céladatkészlet egy táblázatos adatkészlet, amelyben meg van adva az időbélyegző oszlop, és a rendszer elemezni fogja az eltérést. A céladatkészletnek az alapadatkészletben közös funkciókkal kell rendelkezik, és adatkészletnek kell lennie, amelyhez a rendszer új adatokat `timeseries` fűz hozzá. A céladatkészletben található előzményadatok elemezhetők, vagy új adatok figyelhetők.

* **Válassza ki az alapkonfiguráció-adatkészletet.**  Válassza ki azt a táblázatos adatkészletet, amely alapértékként használható a céladatkészlet összehasonlításához az idő alatt.  Az alapadatkészletnek a céladatkészletben közös funkciókkal kell rendelkezik.  Válasszon ki egy időtartományt a céladatkészlet szeletének használatára, vagy adjon meg egy különálló adatkészletet alapértékként.

* **Beállítások figyelése.**  Ezek a beállítások az ütemezett adatkészlet-monitorozási folyamatra vonatkozóak, amely létre lesz hozva. 

    | Beállítás | Leírás | Tippek | Változtatható | 
    | ------- | ----------- | ---- | ------- |
    | Name | Az adatkészlet-figyelő neve. | | No |
    | Funkciók | Azon funkciók listája, amelyek az adatok eltérésének tükrében lesznek elemezve. | A modell kimeneti funkció(i) beállításával mérheti a fogalmak eltérését. Ne foglaljon bele olyan funkciókat, amelyek természetesen eltelődnek az idő (hónap, év, index stb.) között. A funkciók listájának módosítását követően visszatöltést és meglévő sodródásfigyelőt is lehet beállítani. | Yes | 
    | Számítási cél | Azure Machine Learning számítási cél az adatkészlet-figyelő feladatok futtatásához. | | Yes | 
    | Engedélyezés | Az ütemezés engedélyezése vagy letiltása az adatkészlet-monitorozási folyamatban | Tiltsa le az ütemezést az előzményadatok a backfill beállítással való elemzéséhez. Az adatkészlet-figyelő létrehozása után engedélyezhető. | Yes | 
    | Gyakoriság | A folyamat feladatának ütemezéséhez és az előzményadatok elemzéséhez használt gyakoriság visszatöltés futtatásakor. A lehetőségek lehetnek napi, heti vagy havi. | Minden egyes futtatás a céladatkészletben a gyakoriságnak megfelelően hasonlítja össze az adatokat: <li>Napi: A céladatkészlet legutóbbi teljes napját hasonlítja össze az alapkonfigurációval <li>Hetente: A legutóbbi teljes hét összehasonlítása (hétfőtől vasárnapig) a céladatkészletben az alapkonfigurációval <li>Havi: A legutóbbi teljes hónap összehasonlítása a céladatkészletben az alapkonfigurációval | No | 
    | Késés | Órákban megtelik, hogy az adatok megérkeznek az adatkészletbe. Ha például három napba telik, hogy az adatok megérkeznek az SQL-adatbázisba, az adatkészlet beágyazása után állítsa a késést 72-re. | Az adatkészlet-figyelő létrehozása után nem módosítható | No | 
    | E-mail-címek | Az adatsodródás százalékos küszöbértékének túllépése alapján riasztást küldő e-mail-címek. | Az e-maileket a Azure Monitor. | Yes | 
    | Küszöbérték | Az adatsodródás százalékos küszöbértéke az e-mailes riasztások esetében. | További riasztások és események is beállíthatók a munkaterület erőforráshoz társított Application Insights metrikákhoz. | Yes |

A varázsló befejezése után az eredményül kapott adatkészlet-figyelő megjelenik a listában. Válassza ki a figyelő részleteket tartalmazó oldalának kiválasztásához.

---

## <a name="understand-data-drift-results"></a>Az eltérési eredmények

Ez a szakasz egy adatkészlet monitorozásának eredményeit mutatja be, amely az Azure Studio Adatkészletek adatkészlet-figyelési oldalán  /   található.  Ezen az oldalon frissítheti a beállításokat, és elemezheti a meglévő adatokat egy adott időszakra vonatkozóan.  

Kezdje az adatsodródás mértékének felső szintű elemzésekkel, valamint a további vizsgálatra kijelölt jellemzők kiemelését.

:::image type="content" source="media/how-to-monitor-datasets/drift-overview.png" alt-text="Eltérés áttekintése":::


| Metric | Leírás | 
| ------ | ----------- | 
| Eltérés nagyságrendje | Az alapkonfiguráció és a céladatkészlet közötti eltérés százalékos aránya az idő alapján. 0 és 100 között a 0 azonos adatkészleteket jelez, a 100 pedig azt jelzi, hogy az Azure Machine Learning-adateltávolás modell teljesen el tudja különedni a két adatkészletet. A mért pontos százalékértékben a zaj várható, mivel a gépi tanulási technikákat ennek a nagyságrendnek a előállítására használják. | 
| Legnépszerűbb sodródó jellemzők | Az adatkészlet azon funkcióit jeleníti meg, amelyek a legnagyobb eltérést mutatják, ezért a legnagyobb mértékű hozzájárulás a sodródás mértékének metrika alapján. A közös eltolás miatt egy jellemző mögöttes eloszlását nem feltétlenül kell módosítani ahhoz, hogy viszonylag nagy funkciót jelentsen. |
| Küszöbérték | Az adatsodródásnak a beállított küszöbértéken túli mértéke riasztásokat vált ki. Ez a figyelő beállításaiban konfigurálható. | 

### <a name="drift-magnitude-trend"></a>Eltérés nagyságrendi trendje

Tekintse meg, hogy miben különbözik az adatkészlet a céladatkészlettől a megadott időszakban.  Minél közelebb van a 100%-hoz, annál nagyobb a különbség a két adatkészlet között.

:::image type="content" source="media/how-to-monitor-datasets/drift-magnitude.png" alt-text="Eltérés nagyságrendi trendje":::

### <a name="drift-magnitude-by-features"></a>Eltérés mértéke jellemzők szerint

Ez a szakasz szolgáltatásszintű elemzéseket tartalmaz a kiválasztott funkció eloszlásában, valamint az egyéb statisztikák időben történő változásában.

A rendszer a céladatkészletről is profilt ad az idő alatt. Az egyes jellemzők alapkonfigurációja és a céladatkészlet közötti statisztikai távolság az idő alapján van összehasonlítva.  Elméleti szinten ez hasonló az adatsodródás mértékéhez.  Ez a statisztikai távolság azonban nem az összes jellemzőre, hanem egy adott jellemzőre jellemző. A min, a max és a mean is elérhető.

A Azure Machine Learning stúdió kattintson a diagram egyik sávján az erre a dátumra vonatkozó jellemzőszint-részletekért. Alapértelmezés szerint az alapkonfiguráció adatkészletének eloszlása és ugyanazon funkció legutóbbi futtatásos eloszlása látható.

:::image type="content" source="media/how-to-monitor-datasets/drift-by-feature.gif" alt-text="Eltérés mértéke jellemzők szerint":::

Ezek a metrikák a Python SDK-ban is lekértek a `get_metrics()` metódussal egy `DataDriftDetector` objektumon.

### <a name="feature-details"></a>Funkció részletei

Végül görgessen le az egyes funkciók részleteinek megtekintéséhez.  A diagram fölötti legördülő menük használatával válassza ki a funkciót, valamint a megtekinteni kívánt metrikát.

:::image type="content" source="media/how-to-monitor-datasets/numeric-feature.gif" alt-text="Numerikus jellemzőgrafikon és összehasonlítás":::

A diagram metrikai a funkció típusától függnek.

* Numerikus jellemzők

    | Metric | Leírás |  
    | ------ | ----------- |  
    | Wasserstein-távolság | Az alapkonfiguráció céleloszlásra való átalakításához szükséges munka minimális mennyisége. |
    | Átlagos érték | A funkció átlagos értéke. |
    | Minimális érték | A funkció minimális értéke. |
    | Maximális érték | A funkció maximális értéke. |

* Kategorikus jellemzők
    
    | Metric | Leírás |  
    | ------ | ----------- |  
    | Euklúcia távolsága     |  Kategorikus oszlopokhoz kiszámítva. Az euklideális távolságot két vektor alapján számítjuk ki, amelyek ugyanazon kategorikus oszlop két adatkészletből való empirikus eloszlása alapján jönnek létre. A 0 azt jelzi, hogy nincs különbség az empirikus eloszlásban.  Minél jobban eltér a 0-tól, annál jobban sodródott ez az oszlop. Trendek figyelhetők meg a metrika idősorozat-ábrázolásán, és hasznosak lehetnek a sodródó jellemző feltárásában.  |
    | Egyedi értékek | A funkció egyedi értékeinek száma (számossága). |

Ezen a diagramon válasszon ki egy dátumot, hogy összehasonlítsa a funkcióeloszlást a cél és a megjelenített funkció ezen dátuma között. A numerikus jellemzők két valószínűségi eloszlást mutatnak.  Ha a jellemző numerikus, egy sávdiagram jelenik meg.

:::image type="content" source="media/how-to-monitor-datasets/select-date-to-compare.gif" alt-text="A célhoz összehasonlítani kívánt dátum kiválasztása":::

## <a name="metrics-alerts-and-events"></a>Metrikák, riasztások és események

A metrikák lekérdezhetőek az [Azure Application Insights](../azure-monitor/app/app-insights-overview.md) gépi tanulási munkaterülethez társított erőforrásban. A szolgáltatás összes funkcióját elérheti Application Insights beleértve az egyéni riasztási szabályok és műveletcsoportok beállítását egy művelet, például egy e-mail/SMS/leküldéses/hang vagy azure-függvény aktiválására. A részletekért tekintse Application Insights dokumentációját. 

Első lépésként lépjen a [Azure Portal,](https://portal.azure.com) és válassza ki a munkaterület Áttekintés **lapját.**  A Application Insights erőforrás a jobb oldalon van:

[![Az Azure Portal áttekintése](./media/how-to-monitor-datasets/ap-overview.png)](media/how-to-monitor-datasets/ap-overview-expanded.png)

A bal oldali panel Figyelés panelen válassza a Naplók (Elemzés) lehetőséget:

![Az Application Insights áttekintése](./media/how-to-monitor-datasets/ai-overview.png)

Az adatkészlet-figyelő metrika a következőként van `customMetrics` tárolva: . Miután beállított egy adatkészlet-figyelőt a megtekintéséhez, lekérdezést írhat és futtathat:

[![Log Analytics-lekérdezés](./media/how-to-monitor-datasets/simple-query.png)](media/how-to-monitor-datasets/simple-query-expanded.png)

Miután azonosította a metrikákat a riasztási szabályok beállításhoz, hozzon létre egy új riasztási szabályt:

![Új riasztási szabály](./media/how-to-monitor-datasets/alert-rule.png)

Használhat egy meglévő műveletcsoportot, vagy létrehozhat egy újat, amely meghatározza a meghatározott feltételek teljesültekor eltevő műveletet:

![Új műveletcsoport](./media/how-to-monitor-datasets/action-group.png)


## <a name="troubleshooting"></a>Hibaelhárítás

Az sodródásfigyelők korlátozásai és ismert problémái:

* Az előzményadatok elemzésekor az időtartomány a figyelő gyakorisági beállításának 31 intervallumára van korlátozva. 
* 200 funkció korlátozása, kivéve, ha nincs megadva funkciólista (minden használt funkció).
* A számítási méretnek elég nagynak kell lennie az adatok kezeléséhez.
* Győződjön meg arról, hogy az adatkészlet rendelkezik adatokkal az adott figyelő futtatásának kezdő és záró dátumán belül.
* Az adathalmaz-figyelők csak olyan adathalmazokon működnek, amelyek legalább 50 sort tartalmaznak.
* Az adatkészlet oszlopai vagy jellemzői kategorikusként vagy numerikusként vannak besorolva az alábbi táblázatban lévő feltételek alapján. Ha a funkció nem felel meg ezeknek a feltételeknek – például egy sztring típusú, 100 egyedi értékkel >típusú oszlop – a funkció el lesz dobva az sodródás algoritmusból, de továbbra is profilt használ. 

    | Szolgáltatástípus | Adattípus | Feltétel | Korlátozások | 
    | ------------ | --------- | --------- | ----------- |
    | Kategorikus | sztring, bool, int, float | A funkció egyedi értékeinek száma kevesebb, mint 100 és a sorok számának 5%-a. | A null értéket a saját kategóriájaként kezeli. | 
    | Numerikus | int, float | A jellemző értékei numerikus adattípusúak, és nem felelnie meg egy kategorikus jellemző feltételének. | A funkció el van >ha az értékek 15%-a null értékű. | 

* Ha létrehozott egy adatsodródás-figyelőt, de nem látja az adatokat a Azure Machine Learning stúdió adatkészlet-figyelők oldalán, próbálkozzon a következővel. 

    1. Ellenőrizze, hogy a megfelelő dátumtartományt választotta-e ki az oldal tetején.  
    1. Az **Adatkészlet-figyelők lapon** kattintson a kísérlet hivatkozására a futtatás állapotának ellenőrzéséhez.  Ez a hivatkozás a tábla jobb távoli részen található.
    1. Ha a futtatás sikeresen befejeződött, ellenőrizze az illesztőprogram-naplókat, hogy hány metrika lett létrehozva, vagy hogy vannak-e figyelmeztető üzenetek.  Miután rákattintott egy kísérletre, keresse meg az illesztőprogram-naplókat az Output **+ logs** (Kimenet + naplók) lapon.

* Ha az SDK-függvény nem a várt kimenetet hozza létre, annak oka hitelesítési `backfill()` probléma lehet.  Amikor létrehozza a függvénynek átadható számítást, ne használja a `Run.get_context().experiment.workspace.compute_targets` függvényt.  Ehelyett használja a [ServicePrincipalAuthentication](/python/api/azureml-core/azureml.core.authentication.serviceprincipalauthentication) függvényt, például a következőt a függvénynek átadható számítás `backfill()` létrehozásához: 

  ```python
   auth = ServicePrincipalAuthentication(
          tenant_id=tenant_id,
          service_principal_id=app_id,
          service_principal_password=client_secret
          )
   ws = Workspace.get("xxx", auth=auth, subscription_id="xxx", resource_group="xxx")
   compute = ws.compute_targets.get("xxx")
   ```

* A Modelladatgyűjtőből akár 10 percig is eltarthat, hogy az adatok megérkeznek a Blob Storage-fiókba. Egy szkriptben vagy jegyzetfüzetben várjon 10 percet, hogy az alábbi cellák fusson.

    ```python
    import time
    time.sleep(600)
    ```

## <a name="next-steps"></a>Következő lépések

* Az [adathalmaz-Azure Machine Learning stúdió](https://ml.azure.com) a [Python-jegyzetfüzetben](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/work-with-data/datadrift-tutorial/datadrift-tutorial.ipynb) állíthat be adathalmaz-figyelőt.
* Tekintse meg, hogyan állíthatja be az adateltávol [Azure Kubernetes Service.](./how-to-enable-data-collection.md)
* Adathalmaz-eltérésfigyelők beállítása az [Event Grid segítségével.](how-to-use-event-grid.md)
