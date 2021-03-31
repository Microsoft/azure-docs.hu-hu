---
title: A Machine learning szolgáltatásban a csoportos adatelemzési folyamat
description: Ismerkedjen meg a funkciók mérnöki és szerepkörével a gépi tanulás adatfejlesztési folyamatában.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: conceptual
ms.date: 05/14/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath, contperf-fy20q4
ms.openlocfilehash: b20a6744644678879fedf44e960854f558eb0f03
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "98610437"
---
# <a name="feature-engineering-in-machine-learning"></a>A gépi tanulás funkcióinak mérnöki funkciója

Ebből a cikkből megtudhatja, hogyan fejlesztheti a funkciók mérnöki funkcióit, és hogyan javíthatja az adatait a gépi tanulásban. Ismerkedjen meg [Azure Machine learning Studio (klasszikus)](../overview-what-is-machine-learning-studio.md#ml-studio-classic-vs-azure-machine-learning-studio) kísérletekből származó szemléltető példákkal. 

* **Szolgáltatás-mérnöki** folyamat: új funkciók létrehozása a nyers adatokból a tanulási algoritmus prediktív teljesítményének növeléséhez. A mérnöki funkcióknak olyan további információkat kell rögzíteniük, amelyek nem könnyen láthatók az eredeti szolgáltatáskészlet esetében.
* **Szolgáltatás kiválasztása**: a funkciók dimenzióját kiválasztásának folyamata, amely csökkenti a betanítási probléma mértékét.

A rendszer általában a szolgáltatások **fejlesztését** alkalmazza a további funkciók létrehozásához, majd a **funkció kiválasztásával** megszünteti a lényegtelen, redundáns vagy szorosan korrelált funkciókat.

A funkciók mérnöki és kiválasztási funkciója a csoportos adatelemzési folyamat (TDSP) [modellezési szakaszának](lifecycle-modeling.md) részét képezi. További információ a TDSP és az adatelemzési életciklusról: [Mi a TDSP?](overview.md)

## <a name="what-is-feature-engineering"></a>Mi az a Feature Engineering?

A betanítási információ sorokból és oszlopokból álló mátrixból áll. A mátrix minden sora egy megfigyelési vagy rekord. Az egyes sorok oszlopai az egyes rekordokat leíró funkciók. A kísérleti tervben megadott funkcióknak az adatmintákat kell megadniuk.

Bár a nyers adatmezők közül sok használható közvetlenül a modell betanítására, gyakran szükség van további (tervezett) funkciók létrehozására egy bővített betanítási adatkészlethez.

A képzést javító, megerősített funkciók olyan információkat biztosítanak, amelyek jobban megkülönböztetik az adatokban lévő mintákat. Ez a folyamat azonban a Művészetek egyike. A hang-és produktív döntések gyakran igényelnek tartományi szakértelmet.

## <a name="example-1-add-temporal-features-for-a-regression-model"></a>1. példa: időbeli funkciók hozzáadása regressziós modellhez

A kísérletekhez a Azure Machine Learning Studio (klasszikus) [kerékpár-kölcsönzési igények előrejelzését](https://gallery.azure.ai/Experiment/Regression-Demand-estimation-4) használjuk a regressziós feladat funkcióinak megtervezéséhez. A kísérlet célja, hogy előre megjósolja a kerékpár-kölcsönzés igényét egy adott hónapban/napon/órában.

### <a name="bike-rental-dataset"></a>Kerékpár-kölcsönzési adatkészlet

A [Bike RENTAL UCI-adatkészlet](http://archive.ics.uci.edu/ml/datasets/Bike+Sharing+Dataset/) a Egyesült Államokon alapuló, a bike Share cégtől származó valós adatokon alapul. Az 2011-es és a 2012-os év egy adott óráján belül a kerékpár-kölcsönzések számát jelöli. 17 379 sort és 17 oszlopot tartalmaz.

A nyers szolgáltatáskészlet időjárási körülményeket (hőmérséklet/páratartalom/Szélsebesség) és a nap típusát (ünnepnap/hét) tartalmazza. Az előre jelzett mező a darabszám, amely egy adott órán belül a kerékpár-kölcsönzést jelöli. Az 1-től 977-ig terjedő tartományok száma.

### <a name="create-a-feature-engineering-experiment"></a>Szolgáltatás-mérnöki kísérlet létrehozása

A betanítási adatokat tartalmazó hatékony funkciók kiépítésének céljaként négy regressziós modell készül ugyanazzal az algoritmussal, de négy különböző betanítási adatkészlettel. A négy adatkészlet ugyanazokat a nyers bemeneti adatokat jelöli, de egyre több szolgáltatás van beállítva. Ezek a funkciók négy kategóriába vannak csoportosítva:

1. A = időjárás + nyaralás + hétköznap + hétvégi funkciók az előre jelzett napra
2. B = az előző 12 órában bérelt kerékpárok száma
3. C = az előző 12 nap során bérelt kerékpárok száma ugyanazon az órában
4. D = az előző 12 hétben az adott órában és ugyanazon a napon bérelt kerékpárok száma

Az A szolgáltatáskészlet mellett, amely már létezik az eredeti nyers adatban, a szolgáltatások másik három készletét a szolgáltatás mérnöki folyamata hozza létre. A B szolgáltatáskészlet a legújabb igényeket rögzíti a kerékpárok számára. A C szolgáltatáskészlet egy adott órában rögzíti a kerékpárok igényét. A D szolgáltatáskészlet adott órában és a hét adott napján rögzíti a kerékpárok igényét. A négyféle betanítási adatkészlet az A, A + B, A + B + C és A + B + C + D készleteket tartalmazza.

### <a name="feature-engineering-using-studio-classic"></a>Feature Engineering a Studio használatával (klasszikus)

A Studio (klasszikus) kísérlet során a rendszer ezt a négy betanítási adatkészletet négy ág alapján alkotja az előre feldolgozott bemeneti adatkészletből. A bal szélső ág kivételével mindegyik ág tartalmaz egy [végrehajtási R parancsfájl](/azure/machine-learning/studio-module-reference/execute-r-script) -modult, amelyben a származtatott funkciókat (B, C és D) a rendszer kiépíti és hozzáfűzi az importált adatkészlethez.

Az alábbi ábra bemutatja a B. szolgáltatáskészlet létrehozásához használt R-szkriptet a második bal oldali ágban.

![szolgáltatások létrehozása](./media/create-features/addFeature-Rscripts.png)

### <a name="results"></a>Results (Eredmények)

A négy modell teljesítmény-eredményeinek összehasonlítását az alábbi táblázat foglalja össze: 

![eredmény-összehasonlítás](./media/create-features/result1.png)

A legjobb eredményeket a + B + C funkció mutatja be. A hiba mértéke csökken, ha további szolgáltatáskészlet szerepel a betanítási adatkészletben. Ellenőrzi azt a feltételezést, hogy a B, C beállított funkció további releváns információkat biztosít a regressziós feladathoz. A D funkció hozzáadása azonban úgy tűnik, hogy nem biztosít további csökkentést a hibák arányában.

## <a name="example-2-create-features-for-text-mining"></a><a name="example2"></a> 2. példa: szolgáltatások létrehozása a szöveges adatbányászathoz

A szolgáltatások fejlesztését széles körben alkalmazzák a szöveges adatbányászatgal kapcsolatos feladatokban, például a dokumentumok besorolásával és a hangulat elemzésével. Mivel az egyes nyers szövegek általában bemeneti adatokként szolgálnak, a funkció-tervezési folyamat szükséges a szó/kifejezés gyakoriságát érintő funkciók létrehozásához.

### <a name="feature-hashing"></a>Szolgáltatások kivonatolása

Ennek a feladatnak a megvalósításához a rendszer a [szolgáltatás-kivonatolás](/azure/machine-learning/studio-module-reference/feature-hashing) nevű technikát alkalmazza, hogy hatékonyan kapcsolja be a tetszőleges szöveges funkciókat az indexekben. Ahelyett, hogy az egyes szöveges funkciókat (szavakat/kifejezéseket) egy adott indexhez társítsa, ez a módszer egy kivonatoló függvényt alkalmaz a funkciókra, és a kivonatok értékeit közvetlenül is használja.

A Studio (klasszikus) szolgáltatásban van egy [funkció-kivonatolási](/azure/machine-learning/studio-module-reference/feature-hashing) modul, amely a szó/kifejezés funkciókat kényelmesen hozza létre. Az alábbi ábrán egy példa látható a modul használatára. A bemeneti adatkészlet két oszlopot tartalmaz: a könyv minősítése 1-től 5-ig terjed, és a tényleges felülvizsgálati tartalom. Ennek a modulnak a célja egy olyan új funkció beolvasása, amely a megfelelő szó (ok)/phrase előfordulási gyakoriságát mutatja az adott könyv-ellenőrzésen belül. A modul használatához hajtsa végre a következő lépéseket:

* Először válassza ki a bemeneti szöveget tartalmazó oszlopot (ebben a példában a "Col2").
* Másodszor állítsa a "kivonatolási bitsize" a 8-as értékre, ami azt jelenti, hogy 2 ^ 8 = 256 funkció lesz létrehozva. Az összes szövegben szereplő szó/fázis kivonata 256-es indexbe kerül. A "kivonatolási bitsize" paraméter 1 és 31 közötti tartományba esik. A/phrase (ok) kevésbé valószínű, hogy ugyanabba az indexbe kerül, ha nagyobb számra van beállítva.
* Harmadszor, állítsa az "N-gramm" paramétert 2 értékre. Ez az érték beolvassa a unigrams előfordulási gyakoriságát (minden egyes szó funkcióját) és a bigrams (a szomszédos szavak minden pár funkcióját) a bemeneti szövegből. Az "N-gramm" paraméter 0 és 10 közötti tartományba esik, ami azt jelzi, hogy legfeljebb hány szekvenciális szót kell szerepeltetni egy adott szolgáltatásban.  

!["Szolgáltatások kivonatolása" modul](./media/create-features/feature-Hashing1.png)

Az alábbi ábra az új funkció megjelenését mutatja be.

!["Szolgáltatások kivonatolása" példa](./media/create-features/feature-Hashing2.png)

## <a name="conclusion"></a>Összegzés
A tervezett és a kiválasztott funkciók növelhetik a betanítási folyamat hatékonyságát, amely az adatokban található kulcsfontosságú információk kinyerését kísérli meg. Emellett javítják ezeknek a modelleknek a hatékonyságát a bemeneti adatok pontos osztályozása érdekében, valamint a kamatok kiszámításának hatékonyabbá válását.

A funkciók mérnöki és kijelölési funkciói is kombinálhatók, így a tanulás több számítási feltételt is igénybe vehet. Ezt a modell kalibrálásához vagy betanításához szükséges szolgáltatások számának növelésével és csökkentésével végzi. Matematikailag a kiválasztott szolgáltatások olyan független változók minimális készletei, amelyek az adatmintázatokat és a várható eredmények sikerességét mutatják be.

Nem mindig feltétlenül kell végrehajtani a funkciók mérnöki vagy szolgáltatásbeli kijelölését. Ez az adatoktól, a kiválasztott algoritmustól és a kísérlet céljától függ.

## <a name="next-steps"></a>Következő lépések

Bizonyos környezetekben lévő adatszolgáltatások létrehozásához tekintse meg a következő cikkeket:

* [Szolgáltatások létrehozása a SQL Serverban található adatszolgáltatásokhoz](create-features-sql-server.md)
* [Hadoop-fürtben lévő adatszolgáltatások létrehozása struktúra-lekérdezések használatával](create-features-hive.md)