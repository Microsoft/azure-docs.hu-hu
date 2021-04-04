---
title: Forgatókönyvek azonosítása és az elemzési folyamat megtervezése – csoportos adatelemzési folyamat | Azure Machine Learning
description: Azonosíthatja a forgatókönyveket, és megtervezheti a fejlett elemzési adatok feldolgozását a legfontosabb kérdések sorozatának megfontolásával.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: ffe1a4b9b32223cc57c6a6399fdc074a9d51d714
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "93322393"
---
# <a name="how-to-identify-scenarios-and-plan-for-advanced-analytics-data-processing"></a>Forgatókönyvek azonosítása és a bővített analitika adatfeldolgozása

Milyen erőforrásokra van szükség ahhoz, hogy olyan környezetet hozzon létre, amely speciális analitikai feldolgozást végez egy adatkészleten? Ez a cikk számos kérdést javasol, amely segítséget nyújt a forgatókönyvhöz kapcsolódó feladatok és erőforrások azonosításában.

A prediktív elemzések magas szintű lépéseinek sorrendjéről a [Mi a csoportos adatelemzési folyamat (TDSP)](overview.md)című témakörben tájékozódhat. Az egyes lépésekhez konkrét erőforrásokra van szükség az adott forgatókönyvhöz kapcsolódó feladatokhoz.

A forgatókönyvek azonosításához a következő területeken kell válaszolnia a legfontosabb kérdésekre:

* adatlogisztika
* adatok jellemzői
* adatkészlet minősége
* előnyben részesített eszközök és nyelvek

## <a name="logistic-questions-data-locations-and-movement"></a>Logisztikai kérdések: az adathelyszínek és a mozgás

A logisztikai kérdések a következő elemeket fedik le:

* adatforrás helye
* cél cél az Azure-ban
* az adatáthelyezésre vonatkozó követelmények, beleértve az ütemtervet, az összeget és az érintett erőforrásokat

Előfordulhat, hogy az elemzési folyamat során többször kell áthelyeznie az adatátvitelt. Gyakori forgatókönyv, hogy a helyi adattárolást egy Azure-beli tárolóba helyezi át, majd Machine Learning Studioba.

### <a name="what-is-your-data-source"></a>Mi az adatforrás?

Helyi vagy Felhőbeli adatai vannak? A lehetséges helyszínek a következők:

* nyilvánosan elérhető HTTP-címek
* helyi vagy hálózati fájl helye
* SQL Server-adatbázis
* Azure Storage-tároló

### <a name="what-is-the-azure-destination"></a>Mi az Azure-cél?

Hol kell az adatai feldolgozásához vagy modellezéséhez? 

* Azure Blob Storage
* SQL Azure-adatbázisok
* Azure-beli virtuális gépeken futtatott SQL Server
* HDInsight (Hadoop az Azure-ban) vagy kaptár táblák
* Azure Machine Learning
* Azure-beli virtuális merevlemezek csatlakoztathatók

### <a name="how-are-you-going-to-move-the-data"></a>Hogyan fogja áthelyezni az adatátvitelt?

Az olyan eljárások és erőforrások esetében, amelyek különböző tárolási és feldolgozási környezetekben töltik be vagy töltenek be adatot, tekintse meg a következőt:

* [Adatok betöltése a tárolási környezetekbe elemzés céljából](ingest-data.md)
* [Betanítási adatok importálása Azure Machine Learning Studioba (klasszikus) különböző adatforrásokból](../classic/import-data.md)

### <a name="does-the-data-need-to-be-moved-on-a-regular-schedule-or-modified-during-migration"></a>Át kell-e helyezni az adatátvitelt rendszeres időközönként, vagy módosítani kell az áttelepítés során?

Vegye fontolóra Azure Data Factory (ADF) használatát, ha folyamatosan át kell telepíteni az adatátvitelt. Az ADF hasznos lehet a következőhöz:

* hibrid forgatókönyv, amely a helyszíni és a Felhőbeli erőforrásokat egyaránt magában foglalja
* olyan forgatókönyv, amelyben az üzleti logika az áttelepítés során az adatfeldolgozást, módosítást vagy módosítást végez

További információ: [adatok áthelyezése egy SQL Server-adatbázisból SQL Azurera a Azure Data Factory használatával](move-sql-azure-adf.md).

### <a name="how-much-of-the-data-is-to-be-moved-to-azure"></a>Mennyibe kell helyezni az adatmennyiséget az Azure-ba?

A nagyméretű adathalmazok túllépik bizonyos környezetek tárolási kapacitását. Példaként tekintse meg a következő szakaszban a Machine Learning Studio (klasszikus) méretének korlátozásait. Ilyen esetekben az elemzés során használhat egy mintát az adataihoz. Az adathalmazok különböző Azure-környezetekben történő lebontásával kapcsolatos részletekért lásd: [mintaadatok a csoportos adatelemzési folyamat során](sample-data.md).

## <a name="data-characteristics-questions-type-format-and-size"></a>Az adatok jellemzőit érintő kérdések: típus, formátum és méret

Ezek a kérdések kulcsfontosságúak a tárolási és feldolgozási környezetek megtervezéséhez. Segítséget nyújtanak az adattípushoz tartozó megfelelő forgatókönyv kiválasztásában és az összes korlátozás megismerésében.

### <a name="what-are-the-data-types"></a>Mik az adattípusok?

* Numerikus
* Kategorikus
* Sztringek
* Bináris

### <a name="how-is-your-data-formatted"></a>Hogyan történik az adatai formázása?

* Vesszővel tagolt (CSV) vagy tabulátorral tagolt (TSV) egyszerű fájlok
* Tömörítve vagy tömörítetlen
* Azure-Blobok
* Hadoop-struktúra táblái
* Táblák SQL Server

### <a name="how-large-is-your-data"></a>Milyen nagy az adatai?

* Kicsi: kevesebb, mint 2 GB
* Közepes: 2 GB-nál nagyobb és 10 GB-nál kevesebb
* Nagyméretű: 10 GB-nál nagyobb

Használja például a Azure Machine Learning Studio (klasszikus) környezetet:

* A Azure Machine Learning Studio által támogatott adatformátumok és típusok listáját az [adatformátumok és az adattípusok támogatottak](../classic/import-data.md#supported-data-formats-and-data-types) című szakaszban találja.
* Az elemzési folyamatban használt egyéb Azure-szolgáltatások korlátaival kapcsolatos információkért lásd: Azure- [előfizetések és-szolgáltatások korlátai, kvótái és megkötései](../../azure-resource-manager/management/azure-subscription-service-limits.md).

## <a name="data-quality-questions-exploration-and-pre-processing"></a>Adatminőségi kérdések: feltárás és előzetes feldolgozás

### <a name="what-do-you-know-about-your-data"></a>Mit tud az adatairól?

Ismerje meg az adatok alapvető jellemzőit:

* Milyen mintákat vagy trendeket mutat
* A kiugró elemek
* Hány érték hiányzik

Ez a lépés azért fontos, hogy segítsen:

* Annak meghatározása, hogy mennyi előzetes feldolgozásra van szükség
* A legmegfelelőbb funkciókat vagy elemzési típusokat sugalló hipotézisek meghatározása
* Tervek készítése további adatgyűjtés céljából

Az adatok vizsgálatának hasznos módszerei közé tartozik a leíró statisztikai számítás és a vizualizációs ábrázolás. Az adatkészletek különböző Azure-környezetekben történő feltárásával kapcsolatos további információkért lásd: [adatok feltárása a csoportos adatelemzési folyamatban](explore-data.md).

### <a name="does-the-data-require-preprocessing-or-cleaning"></a>Szükséges az adatfeldolgozás vagy a tisztítás?

Előfordulhat, hogy az adatkészletnek a gépi tanuláshoz való hatékony használata előtt elő kell állítania és el kell végeznie az adatfeldolgozást. A nyers adatfeldolgozás gyakran zajos és megbízhatatlan. Lehet, hogy hiányzik az érték. Ha ilyen adatokat használ a modellezéshez, félrevezető eredményeket hozhat létre. Leírásért tekintse meg a [speciális gépi tanulásra vonatkozó adatok előkészítésének feladatait](prepare-data.md).

## <a name="tools-and-languages-questions"></a>Eszközök és nyelvek kérdései

A nyelvekhez, a fejlesztési környezetekhez és az eszközökhöz számos lehetőség áll rendelkezésre. Vegye figyelembe az igényeket és a preferenciákat.

### <a name="what-languages-do-you-prefer-to-use-for-analysis"></a>Milyen nyelveket szeretne használni az elemzéshez?

* R
* Python
* SQL

### <a name="what-tools-should-you-use-for-data-analysis"></a>Milyen eszközöket érdemes használni az adatelemzéshez?

* [Microsoft Azure PowerShell](/powershell/azure/) – az Azure-erőforrások parancsfájl-nyelven való felügyeletéhez használt parancsfájl nyelve
* [Azure Machine Learning Studio](../overview-what-is-machine-learning-studio.md#ml-studio-classic-vs-azure-machine-learning-studio)
* [Revolution Analytics](https://www.microsoft.com/sql-server/machinelearningserver)
* [RStudio](https://www.rstudio.com)
* [Python Tools for Visual Studio](/visualstudio/python/)
* [Anaconda](https://www.anaconda.com/)
* [Jupyter notebookok](https://jupyter.org/)
* [Microsoft Power BI](https://powerbi.microsoft.com)

## <a name="identify-your-advanced-analytics-scenario"></a>A fejlett elemzési forgatókönyv azonosítása

Miután megválaszolta az előző szakaszban leírtakat, készen áll annak meghatározására, hogy melyik forgatókönyv felel meg legjobban az esetnek. A példákat a [Azure Machine learning speciális elemzési forgatókönyvei](plan-sample-scenarios.md)ismertetik.

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Mi az a csoportos adatelemzési folyamat (TDSP)?](overview.md)