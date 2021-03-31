---
title: 'Döntési erdő regressziója: modul leírása'
titleSuffix: Azure Machine Learning
description: Megtudhatja, hogyan használható a döntési erdő regressziós modulja Azure Machine Learning egy regressziós modell létrehozásához a döntési fák együttese alapján.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 04/22/2020
ms.openlocfilehash: 76fd09b5e2c2540cbc1608558800e7897a6cf839
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "94375363"
---
# <a name="decision-forest-regression-module"></a>Döntési erdő regressziós modulja

Ez a cikk a Azure Machine Learning Designer egyik modulját ismerteti.

Ezzel a modullal egy regressziós modellt hozhat létre a döntési fák együttese alapján.

A modell konfigurálása után a modellt a címkével ellátott adatkészlet és a [vonat modell](./train-model.md) modul használatával kell betanítania. Ezt követően a betanított modell segítségével előrejelzéseket készíthet. 

## <a name="how-it-works"></a>Működés

A döntési fák olyan nem parametrikus modellek, amelyek az egyes példányok esetében egyszerű teszteket hajtanak végre, a bináris faszerkezetek adatstruktúráját, amíg el nem éri a levél csomópontot (döntés).

A döntési fák a következő előnyöket biztosítják:

- Hatékonyak mind a számítások, mind a memóriahasználat esetében a képzés és az előrejelzés során.

- Nem lineáris döntési határokat is jelenthetnek.

- Integrált funkciók kiválasztását és besorolását végzik, és rugalmasan működnek a zajos funkciók jelenlétében.

Ez a regressziós modell a döntési fák együttesét tartalmazza. A regressziós döntési erdőben minden fa a Gauss-eloszlást előrejelzésként jeleníti meg. A fák összevonása során a rendszer összesítést végez a modellben található összes fa összevont eloszlásához legközelebb eső Gauss-eloszlás megtalálásához.

Az algoritmus elméleti keretrendszerével és annak megvalósításával kapcsolatos további információkért tekintse meg a következő cikket: [döntési erdők: az osztályozás, a regresszió, a sűrűség becslése, a sokrétű tanulás és a Semi-Supervised tanulás egységes keretrendszere](https://www.microsoft.com/en-us/research/publication/decision-forests-a-unified-framework-for-classification-regression-density-estimation-manifold-learning-and-semi-supervised-learning/?from=http%3A%2F%2Fresearch.microsoft.com%2Fapps%2Fpubs%2Fdefault.aspx%3Fid%3D158806#)

## <a name="how-to-configure-decision-forest-regression-model"></a>Döntési erdő regressziós modelljének konfigurálása

1. Adja hozzá a **döntési erdő regressziós** modulját a folyamathoz. A modult a tervezőben a **Machine learning**, a **modell inicializálása** és a **regresszió** lehetőség alatt találja.

2. Nyissa meg a modul tulajdonságait, és az **újramintavételezési módszernél** válassza ki az egyes fák létrehozásához használt módszert.  A **csomagok** és a **replikálás** lehetőség közül választhat.

    - **Poggyász**: a poggyászt rendszerindítási *összesítésnek* is nevezik. A regressziós döntési erdőben található fák mindegyike egy Gauss-eloszlást ad vissza előrejelzés útján. Az Összesítés egy olyan Gauss megkeresése, amelynek első két pillanata megegyezik az egyes fák által visszaadott összes eloszlás kombinálásával a Gauss-eloszlások keverékének pillanataival.

         További információ: a rendszerindítási [összesítések](https://wikipedia.org/wiki/Bootstrap_aggregating)Wikipedia-bejegyzése.

    - **Replikálás**: a replikáció során minden fát pontosan ugyanazok a bemeneti adatok képeznek. Az egyes facsomópontok esetében a kiosztott predikátumok véletlenszerűek maradnak, és a fák sokrétűek lesznek.

         További információ a betanítási folyamatról a **replikálási** lehetőséggel kapcsolatban: [Computer Vision és az orvosi képek elemzését szolgáló döntési erdők. Criminisi és J. Shotton. Springer 2013.](https://research.microsoft.com/projects/decisionforests/).

3. Határozza meg, hogyan kívánja képezni a modellt az **oktatói mód létrehozása** lehetőség beállításával.

    - **Egyetlen paraméter**

      Ha tudja, hogyan szeretné konfigurálni a modellt, megadhatja az értékek egy adott halmazát argumentumként. Lehetséges, hogy kísérletezéssel megismerte ezeket az értékeket, vagy útmutatásként kapta őket.

    - **Paraméter tartománya**: akkor válassza ezt a lehetőséget, ha nem biztos benne, hogy a legjobb paramétereket szeretné használni, és szeretne futtatni egy paramétert. Válassza ki a megismételni kívánt értékek tartományát, és a [finomhangolási modell hiperparaméterek beállítása](tune-model-hyperparameters.md) az optimális eredményeket eredményező hiperparaméterek beállítása meghatározásához megadott beállítások összes lehetséges kombinációján. 



4. A **döntési fák száma mezőben** adja meg az Ensemble-ben létrehozandó döntési fák teljes számát. További döntési fák létrehozásával lehetőség van jobb lefedettségre, de a képzési idő növekedni fog.

    > [!TIP]
    > Ha az értéket 1-re állítja, azonban ez azt jelenti, hogy csak egy fa lesz létrehozva (a kezdeti paraméterekkel rendelkező fa), és további iterációk nem lesznek végrehajtva.

5. **A döntési fák maximális mélységéhez** adjon meg egy számot, amely korlátozza a döntési fa maximális mélységét. A fa mélységének növelésével növelheti a pontosságot, és megnövelheti a beilleszkedő és a megnövekedett betanítási időt is.

6. A **véletlenszerű felosztások száma a csomóponton** mezőben adja meg a fa egyes csomópontjainak kiépítésekor használandó felosztások számát. A *felosztás* azt jelenti, hogy a fa (csomópont) egyes szintjeinek funkciói véletlenszerűen vannak osztva.

7. A **minták minimális száma a levél csomópontjainál** adja meg az esetek minimális számát, amely szükséges ahhoz, hogy bármely terminál csomópont (levél) a fában legyen létrehozva.

     Az érték növelésével növelheti az új szabályok létrehozásának küszöbértékét. Ha például az alapértelmezett érték 1, akkor akár egyetlen esetben is létrehozhat egy új szabályt. Ha az értéket 5-re emeli, a betanítási adatmennyiségnek legalább öt olyan esetet kellene tartalmaznia, amelyek megfelelnek ugyanazoknak a feltételeknek.


9. A modell betanítása:

    + Ha az **oktatói módot** **egyetlen paraméterként** állítja be, csatlakoztasson egy címkézett adatkészletet és a [betanítási modell](train-model.md) modult.  
  
    + Ha az **oktatói mód** beállítása **paraméter-tartományra** van beállítva, csatlakoztasson egy címkézett adatkészletet, és a modell betanításához használja a [modell hiperparaméterek beállítása](tune-model-hyperparameters.md).  
  
    > [!NOTE]
    > 
    > Ha egy paraméter tartományát adja át a [betanítási modellnek](train-model.md), az csak az alapértelmezett értéket használja az egyetlen paraméteres listában.  
    > 
    > Ha egy paraméter értékének egyetlen készletét adja át a [Tune Model hiperparaméterek beállítása](tune-model-hyperparameters.md) modulnak, amikor az egyes paraméterekhez különböző beállításokat vár, figyelmen kívül hagyja az értékeket, és a tanuló alapértelmezett értékeit használja.  
    > 
    > Ha a **paraméter tartománya** lehetőséget választja, és egyetlen értéket ad meg bármelyik paraméterhez, akkor a rendszer az egyetlen megadott értéket használja a teljes takarítás során, még akkor is, ha más paraméterek egy adott tartományon változnak.

   

10. A folyamat elküldése.

### <a name="results"></a>Results (Eredmények)

A betanítás befejezése után:

+ A betanított modell pillanatképének mentéséhez válassza ki a betanítási modult, majd váltson a **kimenetek** lapra a jobb oldali panelen. Kattintson az ikonra a **modell regisztrálása** elemre.  A mentett modellt megtalálhatja modulként a modul fájában. 

## <a name="next-steps"></a>Következő lépések

Tekintse [meg a Azure Machine learning elérhető modulok készletét](module-reference.md) . 