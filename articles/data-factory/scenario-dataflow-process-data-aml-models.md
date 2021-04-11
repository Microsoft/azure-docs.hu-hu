---
title: Adatfolyamatok használata az automatizált Machine learning-(AutoML-) modellek adatainak feldolgozásához
description: Ismerje meg, hogyan dolgozhat fel Azure Data Factory adatfolyamatokat az automatizált Machine learning-(AutoML-) modellek adatainak feldolgozásához.
services: data-factory
author: amberz
co-author: ATLArcht
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 1/31/2021
ms.author: amberz
ms.co-author: Donnana
ms.openlocfilehash: 45cd44cc0678b7f3a006a88bf66be2bca091af76
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "104595379"
---
# <a name="process-data-from-automated-machine-learning-models-by-using-data-flows"></a>Adatok feldolgozása automatizált gépi tanulási modellből adatfolyamatok használatával

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Az automatizált gépi tanulást (AutoML) a gépi tanulási projektek fogadják el, hogy a besorolási, a regressziós és az idősorozat-előrejelzéshez megadott cél mérőszámok használatával automatikusan betanítsák, hangolják és megszerezzék a legjobb modelleket.

A AutoML egyik kihívása, hogy az adatraktárból vagy tranzakciós adatbázisból származó nyers adatok nagy méretű adathalmazt jelentenek, valószínűleg 10 GB-ot. A nagyméretű adatkészletek esetében a modellek betanítása több időt vesz igénybe, ezért javasoljuk, hogy Azure Machine Learning modellek betanítása előtt optimalizálja az adatfeldolgozást. Ez az oktatóanyag azt ismerteti, hogyan lehet a Azure Data Factory használatával particionálni egy adatkészletet egy Machine Learning adatkészlet AutoML-fájljaiba.

A AutoML projekt a következő három adatfeldolgozási forgatókönyvet tartalmazza:

* A nagyméretű adatmennyiséget AutoML-fájlokba particionálhatja a modellek betanítása előtt.

     A [pandák adatkeretet](https://pandas.pydata.org/pandas-docs/stable/getting_started/overview.html) általában a modellek betanítása előtt dolgozzák fel. A Panda adatkerete jól működik az 1 GB-nál kisebb adatméretek esetében, de ha az adat 1 GB-nál nagyobb méretű, a pandák adatkeret lelassítja az adatfeldolgozást. Időnként előfordulhat, hogy a memóriában is megjelenik egy hibaüzenet. Azt javasoljuk, hogy használjon egy [Parquet fájlformátumot](https://parquet.apache.org/) a gépi tanuláshoz, mert bináris oszlopos formátumú.
    
     Data Factory a leképezési adatfolyamatok vizuálisan tervezett adatátalakítások, amelyek lehetővé tennék az adatmérnökök számára a kód írását. Az adatforgalom leképezése hatékony módja a nagyméretű adatfeldolgozásnak, mivel a folyamat kibővített Spark-fürtöket használ.

* Ossza szét a betanítási adatkészletet és a tesztelési adatkészletet.
    
    A betanítási adatkészletet egy képzési modellhez fogjuk használni. A rendszer a tesztelési adatkészlet használatával értékeli ki a modelleket egy Machine learning-projektben. Az adatfolyamatok hozzárendelésének feltételes felosztása a betanítási adatforgalom felosztására és az adattesztelésre is használható.

* Távolítsa el a nem minősített adatfájlokat.

    Előfordulhat, hogy el szeretné távolítani a nem minősített adatfájlokat, például egy nulla sorból álló parketta-fájlt. Ebben az oktatóanyagban az összesített tevékenységet fogjuk használni a sorok számának megszerzéséhez. A sorok száma a nem minősített adathalmazok eltávolításának feltétele lesz.

## <a name="preparation"></a>Előkészítés

Használja az alábbi Azure SQL Database táblázatot.

```
CREATE TABLE [dbo].[MyProducts](
    [ID] [int] NULL,
    [Col1] [char](124) NULL,
    [Col2] [char](124) NULL,
    [Col3] datetime NULL,
    [Col4] int NULL

) 

```

## <a name="convert-data-format-to-parquet"></a>Adatformátum konvertálása a parketta szolgáltatásba

A következő adatfolyam egy SQL Database-táblázatot egy parketta fájlformátumba konvertál:

- **Forrás adatkészlet**: SQL Database tranzakciós táblázata.
- Fogadó **adatkészlet**: blob Storage parketta formátummal.

## <a name="remove-unqualified-data-based-on-row-count"></a>Nem minősített adategységek eltávolítása a sorok száma alapján

Tegyük fel, hogy el kell távolítania a sorok darabszámát, amely kisebb, mint kettő.

1. A sorok számának beolvasásához használja az összesítő tevékenységet. A **Csoportosítás** alapja a Col2 és az **összesítések** használata a `count(1)` sorok száma alapján.

    ![Képernyőfelvétel: az összesített tevékenység konfigurálásának beállítása a sorok számának beolvasásához.](./media/scenario-dataflow-process-data-aml-models/aggregate-activity-addrowcount.png)

1. A fogadó tevékenység használatával válassza ki a fogadó **típusát** **gyorsítótárként** **a fogadó lapon.** Ezután válassza ki a kívánt oszlopot a **Beállítások** lapon a **kulcs oszlopai** legördülő listából.

    ![A CacheSink tevékenység konfigurálását bemutató képernyőkép a gyorsítótárazott fogadóban lévő sorok számának beolvasásához.](./media/scenario-dataflow-process-data-aml-models/cachesink-activity-addrowcount.png)

1. A származtatott oszlop tevékenységgel hozzáadhat egy sorok száma oszlopot a forrás adatfolyamban. A **származtatott oszlop beállítások** lapján a `CacheSink#lookup` kifejezéssel beolvashatja a sorok darabszámát a CacheSink.

    ![A származtatott oszlop tevékenységének konfigurálását bemutató képernyőkép, amely a source1 sorainak számát adja meg.](./media/scenario-dataflow-process-data-aml-models/derived-column-activity-rowcount-source-1.png)

1. A feltételes felosztási tevékenység használatával távolítsa el a nem minősített fájlokat. Ebben a példában a sorok száma a Col2 oszlop alapján történik. Az a feltétel, hogy a sorok darabszáma ne legyen kettőnél kevesebb, ezért a rendszer eltávolítja a két sort (azonosító = 2 és azonosító = 7). A nem minősített és a blob Storage adatkezelési adatvédelmet is elmentheti.

    ![Képernyőkép, amely a feltételes felosztási tevékenység konfigurálását mutatja a kettőnél nagyobb vagy egyenlő adatlekérdezéshez.](./media/scenario-dataflow-process-data-aml-models/conditionalsplit-greater-or-equal-than-2.png)

> [!NOTE]
>    * Hozzon létre egy új forrást a későbbi lépések során az eredeti forrásban használt sorok számának beolvasásához.
>    * A CacheSink használata teljesítmény szempontjából.

## <a name="split-training-data-and-test-data"></a>Az adatgyűjtési és-tesztelési célú felosztás

A betanítási adatgyűjtést és az egyes partíciók tesztelési értékeit szeretnénk megosztani. Ebben a példában a Col2 megegyező érték esetén a rendszer az első két sort teszteli adatokként és a többi sor betanítási adatként is beolvassa.

1. Az ablak tevékenységgel minden partícióhoz hozzáadhat egy oszlop sorszámát. A **több** oldalon válassza ki a partíció oszlopát. Ebben az oktatóanyagban particionáljuk a Col2. Adjon meg egy rendelést a **Rendezés** lapon, amely ebben az oktatóanyagban az azonosító alapján fog megjelenni. Adjon meg egy megrendelést az **ablak oszlopai** lapon, és adja hozzá az egyes partíciók sorát egy oszlophoz.

    ![Képernyőfelvétel: az ablak tevékenységének konfigurálása egy új oszlop sorszámának hozzáadásához.](./media/scenario-dataflow-process-data-aml-models/window-activity-add-row-number.png)

1. A feltételes felosztási tevékenységgel feloszthatja az egyes partíciók első két sorát a tesztelési adatkészletbe és a többi sort a betanítási adatkészletbe. A **feltételes felosztási beállítások** lapon használja `lesserOrEqual(RowNum,2)` feltételként a kifejezést.

    ![Képernyőkép, amely bemutatja a feltételes felosztási tevékenység konfigurálását az aktuális adatkészlet a betanítási adatkészletbe és a tesztelési adatkészletbe való felosztásához.](./media/scenario-dataflow-process-data-aml-models/split-training-dataset-test-dataset.png)

## <a name="partition-the-training-and-test-datasets-with-parquet-format"></a>A betanítási és tesztelési adatkészletek felosztása a parketta formátumával

A fogadó tevékenység használatakor az **optimalizálás** lapon a **partíciók egyedi értéke** alapján állítsa be az oszlopot a partíció oszlop kulcsaként.

![Képernyőfelvétel: a fogadó tevékenység konfigurálásának beállítása a betanítási adatkészlet partíciójának beállításához.](./media/scenario-dataflow-process-data-aml-models/partition-training-dataset-sink.png)

Nézzük vissza a teljes folyamat logikáját.

![Képernyőkép, amely a teljes folyamat logikáját mutatja.](./media/scenario-dataflow-process-data-aml-models/entire-pipeline.png)

## <a name="next-steps"></a>Következő lépések

Hozzon létre egy adatáramlási logikát az adatáramlás- [átalakítások](concepts-data-flow-overview.md)használatával.
