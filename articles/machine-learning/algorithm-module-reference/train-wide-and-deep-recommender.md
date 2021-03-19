---
title: A Train Wide & Deep ajánló modul használata
titleSuffix: Azure Machine Learning
description: Megtudhatja, hogyan használhatja a Azure Machine Learning Designerben a teljes körű & Deep ajánló modult egy ajánlási modell betanításához.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 06/12/2020
ms.openlocfilehash: d7dd7105ddb0d6503faefb996b84c0e53a62ce49
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "104655376"
---
# <a name="train-wide--deep-recommender"></a>Széles körű & a mély ajánló
Ez a cikk azt ismerteti, hogyan használható a Azure Machine Learning Designerben a **& Deep ajánló** modul a javaslati modell betanításához. Ez a modul a Google által javasolt széles körű & mély tanuláson alapul.

A **betanítási szintű & Deep ajánló** modul beolvassa a felhasználói értékű tripla adatkészletét, és szükség esetén bizonyos felhasználók és elemek funkcióit. Egy betanított, széles körű, & részletes ajánlót ad vissza.  Ezután a betanított modell segítségével minősítési előrejelzéseket vagy javaslatokat készíthet a [pontszám széles és mély ajánló](score-wide-and-deep-recommender.md) modul használatával.  

## <a name="more-about-recommendation-models-and-the-wide--deep-recommender"></a>További tudnivalók a javaslatok modelljeiről és a széles körű & részletes ajánlóról  

Egy ajánlási rendszer fő célja, hogy a rendszer egy vagy több *elemét* javasolja a *felhasználóknak* . Egy elem például film, étterem, könyv vagy zeneszám lehet. A felhasználó lehet személy, csoport vagy más entitás, amely elem-beállításokkal rendelkezik.  

Az ajánlott rendszerek két fő megközelítéssel rendelkeznek. 

+ Az első a **Content-based** megközelítés, amely lehetővé teszi a felhasználók és elemek funkcióinak használatát. A felhasználókat olyan tulajdonságok írják le, mint például az életkor és a nemek, és az elemeket a szerző és a gyártó tulajdonságai is meghatározhatják. A közösségi partnerkereső webhelyeken tipikusan példákat talál a Content-based ajánlási rendszerekre. 
+ A második módszer az **együttműködésen** alapuló szűrés, amely kizárólag a felhasználók és az elemek azonosítóit használja, és a felhasználók által az elemekhez megadott minősítési (ritka) mátrixból beolvassa az ezen entitásokra vonatkozó implicit információkat. Megtudhatja, hogy egy felhasználó a minősítéssel rendelkező elemekről, illetve azokról a felhasználókról, akik azonos elemeket értékeltek.  

A Wide & Deep ajánló ezeket a megközelítéseket kombinálja, és az együttműködésen alapuló szűrést alkalmaz egy Content-based megközelítéssel. Ezért **hibrid ajánlónak** minősül. 

Hogyan működik ez a funkció: Ha egy felhasználó viszonylag új a rendszer számára, az előrejelzések a felhasználó szolgáltatásával kapcsolatos információk felhasználásával javulnak, így a jól ismert "hidegindító" problémával foglalkoznak. Ha azonban elegendő számú minősítést gyűjtött egy adott felhasználótól, akkor a saját szolgáltatásai helyett teljes mértékben személyre szabott előrejelzések készíthetők a rájuk vonatkozó értékelésük alapján. Ezért az együttműködésen alapuló szűrés alapján a tartalomon alapuló javaslatok zökkenőmentesen áttérnek a javaslatokra. Ha a felhasználó vagy az elem funkciói nem érhetők el, a Wide & Deep ajánló továbbra is működni fog az együttműködési szűrési módban.  

A Wide & Deep ajánló és a mögöttes valószínűségi algoritmus további részletei a megfelelő kutatási dokumentumban találhatók: [széles körű & az ajánló rendszerek mélyreható megismerése](https://arxiv.org/pdf/1606.07792.pdf).  

## <a name="how-to-configure-train-wide--deep-recommender"></a>A tanítási szintű & részletes ajánlójának konfigurálása  

+ [A betanítási adatfeldolgozás előkészítése](#prepare-data)
+ [A modell betanítása](#train-the-model)

### <a name="prepare-data"></a>Adatok előkészítése

A modul használatának megkísérlése előtt elengedhetetlen, hogy az adatai a javaslati modell által várt formátumban legyenek. Szükség van egy felhasználói értékű **tripla-** alapú betanítási adatkészletre, de a felhasználói szolgáltatásokat és az elemek funkcióit (ha elérhetők) külön adatkészletekben is megadhatja.

#### <a name="required-dataset-of-user-item-ratings"></a>A felhasználói elemek minősítéséhez szükséges adatkészlet

A betanításhoz használt bemeneti adatoknak a megfelelő formátumban kell tartalmazniuk a megfelelő típusú adatokat: 

+ Az első oszlopnak felhasználói azonosítókat kell tartalmaznia.
+ A második oszlopnak tartalmaznia kell az elemek azonosítóit.
+ A harmadik oszlop tartalmazza a felhasználó-tétel párok minősítését. A minősítési értékeknek numerikus típusúnak kell lenniük. 

Például a felhasználó-elem minősítések tipikus készlete a következőhöz hasonló lehet:

|UserId (Felhasználóazonosító)|MovieId|Minősítés|
|------------|-------------|------------|
|1|68646|10|
|223|31381|10|

#### <a name="user-features-dataset-optional"></a>Felhasználói szolgáltatások adatkészlete (nem kötelező)

A **felhasználói szolgáltatások** adatkészletének tartalmaznia kell a felhasználók azonosítóit, és ugyanazokat az azonosítókat kell használnia, amelyeket a felhasználók – elemek – minősítési adatkészlet első oszlopában adtak meg. A fennmaradó oszlopok tetszőleges számú funkciót tartalmazhatnak, amelyek leírják a felhasználókat.  

Például a felhasználói funkciók egy tipikus készlete a következőhöz hasonló lehet: 

|UserId (Felhasználóazonosító)|Életkor|Nem|Érdeklődését|Hely|
|------------|--------------|-----------------------|---------------|------------|
|1|25|male| Dráma    |Európa|
|223|40|female|Francia|Ázsia|

#### <a name="item-features-dataset-optional"></a>Elem funkcióinak adatkészlete (nem kötelező)

Az elem funkcióinak adatkészletének tartalmaznia kell az első oszlopban szereplő elem-azonosítókat. A fennmaradó oszlopok tetszőleges számú leíró funkciót tartalmazhatnak az elemekhez.  

Például az elemek egy tipikus készlete a következőhöz hasonló lehet:  

|MovieId|Cím|Eredeti nyelv|Műfajokat|Year|
|-------------|-------------|-------------------|-----------|---------------|
|68646|A Keresztapa|Angol|Dráma|1972|
|31381|Elment a szélhez|Angol|Előzmények|1939|

### <a name="train-the-model"></a>A modell betanítása

1.  Adja hozzá a teljes **körű és mély ajánló** modult a kísérlethez a tervezőben, és kapcsolja össze a betanítási adatkészlettel.  
  
2. Ha a felhasználói funkciók és/vagy az elem funkcióinak külön adatkészlete van, akkor kapcsolja össze őket a **vonat széles és mély ajánló** moduljában.  
  
    - **Felhasználói szolgáltatások adatkészlete**: kapcsolja össze az adatkészletet, amely leírja a felhasználókat a második bemenetre.
    - **Elem funkciói adatkészlet**: kapcsolja össze az adatkészletet, amely leírja az elemeket a harmadik bemenetre.  
    
3.  **Alapkorszakok**: azt jelzi, hogy az algoritmus hányszor dolgozza fel a teljes betanítási adatmennyiséget. 

    Minél nagyobb ez a szám, annál megfelelőbb a képzés; a képzés azonban több időt is igénybe vehet, és a túlilleszkedés okozhatja a költségeket.

4. **Batch-méret**: írja be az egy betanítási lépésben felhasznált betanítási példák számát. 

     Ez a hiperparaméter hatással lehet a betanítási sebességre. A magasabb batch-méret kevesebb időt vehet igénybe, de növelheti a konvergencia időpontját. Ha a köteg túl nagy a GPU/CPU-hoz való illeszkedéshez, akkor memóriahiba merülhet fel.

5.  **Széles körű optimalizáló**: válassza ki az egyik optimalizáló, hogy a modell széles részén alkalmazza a színátmeneteket.

6.  **Széles körű optimalizáló tanulási arány**: adjon meg egy 0,0 és 2,0 közötti számot, amely meghatározza a Wide rész-optimalizáló képzési arányát.

    Ez a hiperparaméter határozza meg az egyes betanítási lépések lépésének méretét, miközben a minimális veszteség függvény felé mozdul. A túl nagy tanulási sebesség miatt előfordulhat, hogy a tanulás átugorja a minimumokat, míg egy túl kicsi tanulási arány a konvergencia-problémát okozhatja.

7.  Átadott **szolgáltatás dimenzió**: írja be a dimenziót a kívánt felhasználói azonosítók és az elem-azonosító funkciók beírásával. 

    A Wide & részletes ajánlója alapértelmezés szerint a felhasználói azonosító és az elem-azonosító funkcióinak több termékre kiterjedő átalakítását végzi. Ennek a számnak a alapján a rendszer a dimenziót biztosítja.

8.  **Mély rész-optimalizáló**: válassza ki az egyik optimalizáló, hogy a modell mély részén alkalmazza a színátmeneteket.

9.  Részletes **optimalizáló tanulási arány**: adjon meg egy 0,0 és 2,0 közötti számot, amely meghatározza a Deep rész-optimalizáló képzési arányát.

10.  **Felhasználói beágyazási dimenzió**: adjon meg egy egész számot a felhasználói azonosító beágyazási dimenziójának megadásához.

     A Wide & Deep ajánló létrehozza a megosztott felhasználói azonosító beágyazásait és az elemek azonosító beágyazását mind a széles, mind a mély részekhez.

11.  **Elemek beágyazási dimenziója**: adjon meg egy egész számot, hogy meghatározza az elemek azonosító beágyazásának dimenzióját.

12.  A **kategorikus funkciók beágyazási dimenziója**: adjon meg egy egész számot, hogy meghatározza a kategorikus funkciók beágyazásának dimenzióit.

     A Wide & Deep ajánló mély összetevőjében minden kategorikus szolgáltatáshoz egy beágyazási vektort is megtanultunk. És ezek a beágyazási vektorok ugyanazt a dimenziót használják.

13.  **Rejtett egységek**: írja be a mély összetevő rejtett csomópontjainak számát. Az egyes rétegekben található csomópontok száma vesszővel elválasztva. Ha például a "1 000 500 100" típust adja meg, akkor a Deep összetevőnek három réteget kell megadnia, az első réteghez pedig 1000 csomópontot, 500 csomópontot és 100 csomópontot.

14.  **Aktiválási függvény**: válasszon ki egy, az egyes rétegekre alkalmazott aktiválási függvényt, az alapértelmezett érték a ReLU.

15.  **Lemorzsolódás**: adjon meg 0,0 és 1,0 közötti számot annak megállapításához, hogy az egyes rétegekben a kimenetek el lesznek-e dobva a betanítás során.

     A lemorzsolódás egy regularizációs módszer, amely megakadályozza a neurális hálózatok túlillesztését. Ennek az értéknek az egyik gyakori döntése a 0,5-as számú, amely úgy tűnik, hogy a hálózatokhoz és feladatokhoz képest optimálisan közelíthető.

16.  **Batch normalizálása**: válassza ezt a lehetőséget, ha a mély összetevő minden rejtett rétege után szeretné használni a Batch-normalizálás funkciót.

     A Batch normalizálása egy olyan módszer, amellyel a hálózatok betanításakor a belső, egyváltozós váltási problémák is megtalálhatók. Általánosságban segítheti a hálózatok sebességének, teljesítményének és stabilitásának növelését. 

17.  A folyamat futtatása.

## <a name="results"></a>Results (Eredmények)

A folyamat futása után a modell a pontozáshoz való használatához a széles és [mély ajánlót a teljes](train-wide-and-deep-recommender.md) körű [és részletes ajánlóhoz](score-wide-and-deep-recommender.md)kell kötni az új bemeneti példák értékének előrejelzéséhez.

##  <a name="technical-notes"></a>Technikai megjegyzések

A Wide & a nagy lineáris modelleket és a mély neurális hálózatokat kombinálva ötvözi a memorizálás és az általánosítás erősségét. A széles összetevő a funkciók interakcióinak megadásához a nyers szolgáltatások és a szolgáltatás-átalakítások készletét fogadja el. A kevésbé fontos funkciókkal rendelkező funkciók esetében pedig a mély összetevő általánosítja a láthatatlan funkciók kombinációját az alacsony dimenziós sűrű funkció-beágyazások segítségével. 

A Wide & Deep ajánló implementációjában a modul egy alapértelmezett modell-struktúrát használ. A széles összetevő a felhasználói beágyazásokat, az elemek beágyazását, valamint a felhasználói azonosítók és az elem-azonosítók a terméken belüli átalakítását adja meg bemenetként. A modell mély részét képező beágyazási vektort minden egyes kategorikus funkció esetében megtanultuk. Más numerikus funkciós vektorokkal együtt ezek a vektorok a Deep feed-Forward neurális hálózatba kerülnek. A széles rész és a mélyebb rész összevonásával összefoglalja a végső kimeneti naplóbeli szorzókat, amelyek a közös képzések esetében egy közös adatvesztési funkciót mutatnak.


## <a name="next-steps"></a>Következő lépések

Tekintse [meg Azure Machine learning elérhető modulok készletét](module-reference.md) . 
