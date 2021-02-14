---
title: A adatfolyam használata az adatok gépi tanulási (AutoML) modellekből való feldolgozásához
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
ms.openlocfilehash: 494fc2c227b6fe855e96a780d43cc6702722fa94
ms.sourcegitcommit: e972837797dbad9dbaa01df93abd745cb357cde1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/14/2021
ms.locfileid: "100521015"
---
# <a name="process-data-from-automated-machine-learningautoml-models-using-data-flow"></a>Az automatizált Machine learning-(AutoML-) modellek adatainak feldolgozása az adatfolyam használatával

Az automatizált gépi tanulást (AutoML) a gépi tanulási projektek fogadják el, hogy a besoroláshoz, a regresszióhoz és az idősorozat-előrejelzéshez megadott cél metrika használatával automatikusan betanítsák és beszerezzék a legjobb modellt. 

Az egyik kihívás az adattárházból vagy a tranzakciós adatbázisból származó nyers adatok nagy adathalmaz lenne, például: 10 GB, a nagyméretű adathalmaznak több időre van szüksége a modellek betanításához, ezért ajánlott az adatfeldolgozás optimalizálása a Azure Machine Learning modellek betanítása előtt. Ebből az oktatóanyagból megtudhatja, hogyan használhatja az ADF-t az adatkészlet Azure Machine Learning adatkészlethez tartozó parketta-fájlokba való particionálásához. 

Az automatizált Machine learning (AutoML) projektekben a következő három adatfeldolgozási forgatókönyvet alkalmazza:

* A modellek betanítása előtt nagy mennyiségű fájlt particionálhat a parketta-fájlokba. 

     A [pandák adatkeretet](https://pandas.pydata.org/pandas-docs/stable/getting_started/overview.html) általában az oktatási modellek betanítása előtt dolgozzák fel. A pandák adatkerete jól működik az 1 GB-nál kisebb adatméretek esetében, de ha az adatmennyiség 1 GB-nál nagyobb, akkor a Panda adatkeret lelassítja az adatfeldolgozást, és egy kicsit elkerül a memória hibaüzenete. A [parketta](https://parquet.apache.org/) -fájlformátumokat a gépi tanuláshoz ajánlott használni, mivel ez bináris oszlopos formátum.
    
    Az Azure-beli adatfeldolgozók leképezése az adatfolyamatokat vizuálisan tervezett adatátalakítások alapján, az adatmérnökök számára is ingyenes. Nagy mennyiségű adatfeldolgozást végeznek, mivel a folyamat skálázható kibővített Spark-fürtöket használ.

* Osztott betanítási adatkészlet és tesztelési adatkészlet.
    
    A betanítási adatkészletet a betanítási modellhez fogjuk használni, és tesztelni fogjuk a modelleket a Machine learning-projektben. Az adatfolyamatok feltételes felosztásának leképezése tevékenység feloszthatja a betanítási adatait, és tesztelheti az adatait. 

* Távolítsa el a nem minősített adatfájlokat.

    Előfordulhat, hogy el kívánja távolítani a nem minősített adatfájlokat, például a következőt: Parquet, nulla sorral. Ebben az oktatóanyagban az összesített tevékenységet fogjuk használni a sorok számának beolvasásához, a sorok száma pedig a nem minősített adatok eltávolításának feltétele. 


## <a name="preparation"></a>Előkészítés
Használja az alábbi táblázatot Azure SQL Database. 
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

Az adatfolyam a Azure SQL Database egy táblázatát a parketta fájlformátumba konvertálja. 

**Forrás adatkészlet**: Azure SQL Database tranzakciós táblázata

Fogadó **adatkészlet**: blob Storage parketta formátummal


## <a name="remove-unqualified-data-based-on-row-count"></a>Nem minősített adategységek eltávolítása a sorok száma alapján

Tegyük fel, hogy a sorok száma nem lehet kisebb, mint 2. 

1. Összesítő tevékenység használata a sorok számának beolvasásához: **Csoportosítás** a Col2 és az **összesítések** alapján a sorok száma értékkel (1). 

    ![Összesítő tevékenység konfigurálása a sorok számának beolvasásához](./media/scenario-dataflow-process-data-aml-models/aggregate-activity-addrowcount.png)

1. Használja a fogadó tevékenységet, **válassza a fogadó** **típusa** gyorsítótárként lehetőséget a fogadó lapon, majd válassza a kívánt oszlop elemet a **kulcsok oszlopok** legördülő listából a **Beállítások** lapon. 

    ![CacheSink-tevékenység konfigurálása a gyorsítótárazott fogadóban lévő sorok számának lekéréséhez](./media/scenario-dataflow-process-data-aml-models/cachesink-activity-addrowcount.png)

1. A származtatott oszlop tevékenység használatával adja hozzá a sorok száma oszlopot a forrás-adatfolyamban. A **származtatott oszlop beállítások** lapján használja a CacheSink # lookup kifejezést, és adja meg a SinkCache a sorok darabszámát.
    ![Származtatott oszlop tevékenységének beállítása az 1. forrásban lévő sorok számának hozzáadásához](./media/scenario-dataflow-process-data-aml-models/derived-column-activity-rowcount-source-1.png)

1. Feltételes felosztású tevékenység használata a nem minősített adatműveletek eltávolításához. Ebben a példában a sorok száma a Col2 oszlop alapján történik, és a feltétel a sorok számának kisebbnek kell lennie, mint 2, ezért a rendszer két sort (azonosító = 2 és ID = 7) távolít el. Az adatkezeléshez a nem minősített adattárakat kell mentenie egy blob Storage-tárolóba. 

    ![Feltételes felosztású tevékenység konfigurálása, amely nagyobb vagy egyenlő, mint 2](./media/scenario-dataflow-process-data-aml-models/conditionalsplit-greater-or-equal-than-2.png)

> [!NOTE]
>    *    Hozzon létre egy új forrást a sorok számának lekéréséhez, amelyek az eredeti forrásban lesznek felhasználva a későbbi lépésekben. 
>    *    CacheSink használata a teljesítmény szempontjából. 

## <a name="split-training-data-and-test-data"></a>Az adatgyűjtési és-tesztelési célú felosztás 

1. A betanítási adatgyűjtést és az egyes partíciók tesztelési értékeit szeretnénk megosztani. Ebben a példában a Col2 azonos értékeként az első 2 sort beolvassa a vizsgálati adatok és a REST-sorok betanítási adatokként. 

    Az ablak tevékenységgel minden partícióhoz hozzáadhat egy oszlopos sorszámot. A **több mint** lapon válassza a partíció oszlopát (ebben az oktatóanyagban a Col2 partícióját), a **rendezési** sorrend fület (ebben az oktatóanyagban a sorrend alapján), az **ablak oszlopai** lapon pedig adja hozzá az egyes partíciók sorszámát. 
    ![Ablak beállítása tevékenység egy új oszlop sorszámának hozzáadásához](./media/scenario-dataflow-process-data-aml-models/window-activity-add-row-number.png)

1. A feltételes felosztási tevékenységgel feloszthatja az egyes partíciók első 2 sorát az adatkészlet teszteléséhez, valamint a REST-sorokhoz az adatkészlet betanításához. A **feltételes felosztási beállítások** lapon használja feltételként a kifejezés LesserOrEqual (RowNum, 2). 

    ![feltételes felosztási tevékenység konfigurálása az aktuális adatkészlet felosztásához az adatkészlet és a tesztelési adatkészlet betanításához](./media/scenario-dataflow-process-data-aml-models/split-training-dataset-test-dataset.png)

## <a name="partition-training-dataset-and-test-dataset-with-parquet-format"></a>Particionálási adatkészletek és tesztelési adatkészlet parketta formátummal

1. Használja a fogadó tevékenységet, az **optimalizálás** lapon, a **partíciók egyedi értékének** használatával, hogy az oszlopot egy oszlopként adja meg a partícióhoz. 
    ![a fogadó tevékenység konfigurálása a betanítási adatkészlet particionálásának beállításához](./media/scenario-dataflow-process-data-aml-models/partition-training-dataset-sink.png)

    Nézzük vissza a teljes folyamat logikáját.
    ![A teljes folyamat logikája](./media/scenario-dataflow-process-data-aml-models/entire-pipeline.png)


## <a name="next-steps"></a>Következő lépések

* Hozza létre a többi adatáramlási logikát az adatforgalom- [átalakítások](concepts-data-flow-overview.md)leképezése használatával.
