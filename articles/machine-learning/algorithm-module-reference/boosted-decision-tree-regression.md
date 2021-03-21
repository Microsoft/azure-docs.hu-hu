---
title: 'Gyorsított döntési fa regresszió: modul referenciája'
titleSuffix: Azure Machine Learning
description: Megtudhatja, hogyan hozhat létre a regressziós fák kombinációját a Azure Machine Learning a kihasználható döntési fa regressziós modullal a növeléssel.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 08/24/2020
ms.openlocfilehash: 664943fc5535883b3df77b2795383e5c0586a71c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "94375329"
---
# <a name="boosted-decision-tree-regression-module"></a>A döntési fa regressziós moduljának növelése

Ez a cikk a Azure Machine Learning Designer egyik modulját ismerteti.

Ezzel a modullal a regressziós fák együttesét hozhatja létre a boosting használatával. A *kiemelés* azt jelenti, hogy az egyes faszerkezetek az előző fáktől függenek. Az algoritmus az előtte lévő fák maradékának beépítésével tanul. Így a döntési fazenekarban egyre nagyobb a pontosság, és kisebb a lefedettségük.  

Ez a modul a LightGBM algoritmuson alapul.
  
Ez a regressziós módszer egy felügyelt tanulási módszer, ezért *címkével ellátott adatkészletet* igényel. A Label oszlopnak numerikus értékeket kell tartalmaznia.  

> [!NOTE]
> Ezt a modult csak numerikus változókat használó adatkészletekkel használhatja.  

A modell meghatározása után betaníthatja azt a betanítási [modell](./train-model.md)használatával.

  
## <a name="more-about-boosted-regression-trees"></a>További információ a regressziós fák növeléséről  

A boosting számos klasszikus módszer az Ensemble-modellek létrehozásához, valamint a csomagok, a véletlenszerű erdők és így tovább.  Azure Machine Learning a megnövelt döntési fák a MART gradiens-növelő algoritmus hatékony megvalósítását használják. A színátmenet-növelés a gépi tanulási módszer a regressziós problémákhoz. Az egyes regressziós faszerkezeteket egy lépésenkénti, előre definiált Loss függvénnyel méri, hogy az egyes lépésekben a hibát megmérjük, és a következőben javítsa. Így az előrejelzési modell tulajdonképpen a gyengébb előrejelző modellek együttese.  
  
A regressziós problémák esetében a kibővíti a fák egy sorozatát egy részletesen megjelenő módon, majd kiválasztja az optimális fát egy tetszőleges differentiable Loss függvény használatával.  
  
További információkért tekintse meg a következő cikkeket:  
  
+ [https://wikipedia.org/wiki/Gradient_boosting#Gradient_tree_boosting](https://wikipedia.org/wiki/Gradient_boosting)

    Ez a wikipedia-cikk a színátmenet-növeléssel kapcsolatban nyújt némi hátteret a felerősített fákat illetően. 
  
-  [https://research.microsoft.com/apps/pubs/default.aspx?id=132652](https://research.microsoft.com/apps/pubs/default.aspx?id=132652)  

    Microsoft Research: a RankNet-ről a LambdaRank-re a LambdaMART-be: áttekintés. J.C. szerint Burges.

A színátmenet-növelési módszer a besorolási problémákhoz is használható, ha csökkenti a regressziót egy megfelelő veszteség-függvénnyel. A besorolási feladatokhoz kapcsolódó felerősített fák megvalósításával kapcsolatos további információkért lásd: [kétosztályos megnövelt döntési fa](./two-class-boosted-decision-tree.md).  

## <a name="how-to-configure-boosted-decision-tree-regression"></a>A megnövelt döntési fa regressziójának konfigurálása

1.  Adja hozzá a **megnövelt döntési fa** modult a folyamathoz. Ezt a modult **Machine learning**, **inicializálás**, a **regressziós** kategória alatt találja. 
  
2.  Határozza meg, hogyan kívánja képezni a modellt az **oktatói mód létrehozása** lehetőség beállításával.  
  
    -   **Egyetlen paraméter**: válassza ezt a lehetőséget, ha tudja, hogyan szeretné konfigurálni a modellt, és adjon meg egy adott értékeket argumentumként. 
     
    -   **Paraméter tartománya**: akkor válassza ezt a lehetőséget, ha nem biztos benne, hogy a legjobb paramétereket szeretné használni, és szeretne futtatni egy paramétert. Válassza ki a megismételni kívánt értékek tartományát, és a [finomhangolási modell hiperparaméterek beállítása](tune-model-hyperparameters.md) az optimális eredményeket eredményező hiperparaméterek beállítása meghatározásához megadott beállítások összes lehetséges kombinációján.    
   
  
3. A **kihagyható levelek maximális száma faszerkezetben**: a bármely fában létrehozható terminál-csomópontok (levelek) maximális számát jelzi.  

    Ennek az értéknek a növelésével lehetőség van a fa méretének növelésére és jobb pontosságra, ha a túlillesztés és a hosszú betanítási idő fennáll.  

4. **Minták minimális száma a levél csomópontjain**: azt jelzi, hogy az esetek minimális száma szükséges ahhoz, hogy minden terminál-csomópont (levél) a fában legyen létrehozva.

    Az érték növelésével növelheti az új szabályok létrehozásának küszöbértékét. Ha például az alapértelmezett érték 1, akkor akár egyetlen esetben is létrehozhat egy új szabályt. Ha az értéket 5-re emeli, a betanítási adatmennyiségnek legalább 5 olyan esetet kell tartalmaznia, amely ugyanazokat a feltételeket teljesíti.

5. **Tanulási arány**: adjon meg egy 0 és 1 közötti számot, amely meghatározza a lépés méretét a tanulás során. A tanulási arány határozza meg, hogy a tanuló milyen gyorsan vagy lassabban konvergál az optimális megoldáson. Ha a lépés mérete túl nagy, lehet, hogy az optimális megoldást is lelövi. Ha a lépés mérete túl kicsi, a képzés továbbra is a legjobb megoldáshoz közeledik.

6. **Felépített fák száma**: az Ensemble-ben létrehozandó döntési fák teljes számát jelzi. További döntési fák létrehozásával jobb lefedettséget érhet el, de a képzési idő növekszik.

    Ha az értéket 1-re állítja, a rendszer azonban csak egy fát állít elő (a kezdeti paraméterekkel rendelkező fát), és nem végez további iterációt.

7. **Véletlenszerű számú mag**: adjon meg egy nem negatív, nem negatív egész számot, amelyet véletlenszerű mag értékként kíván használni. A vetőmagok megadásával biztosítható az azonos adatokkal és paraméterekkel rendelkező futtatások közötti reprodukálhatóság.

    Alapértelmezés szerint a véletlenszerű mag értéke 0, ami azt jelenti, hogy a kezdeti mag értékét a rendszer órája szerzi be.
  

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
  
## <a name="results"></a>Results (Eredmények)

A betanítás befejezése után:

+ Ha a modellt a pontozáshoz szeretné használni, kapcsolja össze a [betanítási modellt](train-model.md) [az új](./score-model.md)bemeneti példák értékeinek előrejelzéséhez.

+ A betanított modell pillanatképének mentéséhez a **betanított modell** jobb oldali paneljén válassza a **kimenetek** fület, majd kattintson az **adatkészlet regisztrálása** ikonra. A betanított modell másolata modulként lesz mentve a modul fájában, és a folyamat egymást követő futtatása során nem lesz frissítve.

## <a name="next-steps"></a>Következő lépések

Tekintse [meg a Azure Machine learning elérhető modulok készletét](module-reference.md) . 
