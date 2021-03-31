---
title: 'Two-Class megnövelt döntési fa: modulok referenciája'
titleSuffix: Azure Machine Learning
description: Megtudhatja, hogyan hozhat létre bináris osztályozó használatával a tervező Two-Class növelt döntési fájának modulját.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 08/24/2020
ms.openlocfilehash: 810878956a89b6e7400be99692cf43925d1cd134
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "94375912"
---
# <a name="two-class-boosted-decision-tree-module"></a>Two-Class megnövelt döntési fa modul

Ez a cikk a Azure Machine Learning Designer egyik modulját ismerteti.

Ezzel a modullal létrehozhat egy gépi tanulási modellt, amely a megnövelt döntési fák algoritmusán alapul. 

A megnövelt döntési fa egy olyan együttes tanulási módszer, amelyben a második fa kijavítja az első fa hibáit, a harmadik fa kijavítja az első és a második fa hibáit, és így tovább. Az előrejelzések a fák teljes egészére épülnek, amely az előrejelzést végzi.
  
Általában, ha megfelelően van konfigurálva, a megnövelt döntési fák a legkönnyebben használható módszerek, amelyekkel a gépi tanulási feladatok széles skáláját érhetik el. Ugyanakkor a memória-igényes tanulók egyike is, és a jelenlegi implementáció mindent megtart a memóriában. Ezért előfordulhat, hogy a megnövelt döntési famodell nem tudja feldolgozni a lineáris tanulók által kezelhető nagyméretű adatkészleteket.

Ez a modul a LightGBM algoritmuson alapul.

## <a name="how-to-configure"></a>Konfigurálás

Ez a modul egy képzetlen besorolási modellt hoz létre. Mivel a besorolás egy felügyelt tanulási módszer, a modell betanításához olyan *címkézett adatkészletre* van szükség, amely tartalmazza az összes sor értékét tartalmazó címke oszlopot.

Ezt a modellt betaníthatja a [betanítási modell](././train-model.md)használatával. 

1.  A Azure Machine Learningban adja hozzá a **megnövelt döntési fa** modult a folyamathoz.
  
2.  Határozza meg, hogyan kívánja képezni a modellt az **oktatói mód létrehozása** lehetőség beállításával.
  
    + **Egyetlen paraméter**: ha tudja, hogyan szeretné konfigurálni a modellt, megadhatja az értékek adott készletét argumentumként.
  
    + **Paraméter tartománya**: Ha nem biztos benne, hogy a legjobb paramétereket használja, megkeresheti az optimális paramétereket az [Tune Model hiperparaméterek beállítása](tune-model-hyperparameters.md) modullal. Bizonyos értékeket adhat meg, és az oktató a beállítások több kombinációján keresztül megismétli a legjobb eredményt eredményező értékek kombinációját.
  
3.  A **falevelek maximális számának** megadásához adja meg a fában létrehozható terminál-csomópontok (levelek) maximális számát.
  
     Ennek az értéknek a növelésével lehetőség van a fa méretének növelésére és jobb pontosságra, ha a túlillesztés és a hosszú betanítási idő fennáll.
  
4.  A **minták minimális száma a levél csomópontjainál** adja meg, hogy hány esetben szükséges a terminál csomópont (levél) létrehozása a fában.  
  
     Az érték növelésével növelheti az új szabályok létrehozásának küszöbértékét. Ha például az alapértelmezett érték 1, akkor akár egyetlen esetben is létrehozhat egy új szabályt. Ha az értéket 5-re emeli, a betanítási adatmennyiségnek legalább öt olyan esetet kellene tartalmaznia, amelyek megfelelnek ugyanazoknak a feltételeknek.
  
5.  A **tanulási arány** mezőben adjon meg egy 0 és 1 közötti számot, amely meghatározza a lépés méretét a tanulás során.  
  
     A tanulási arány határozza meg, hogy a tanuló milyen gyorsan vagy lassabban konvergál az optimális megoldáson. Ha a lépés mérete túl nagy, lehet, hogy az optimális megoldást is lelövi. Ha a lépés mérete túl kicsi, a képzés továbbra is a legjobb megoldáshoz közeledik.
  
6.  A **kiépített fák száma mezőben** adja meg az Ensemble-ben létrehozandó döntési fák teljes számát. További döntési fák létrehozásával lehetőség van jobb lefedettségre, de a képzési idő növekedni fog.
  
     Ha az értéket 1-re állítja, a rendszer csak egy fát állít elő (a kezdeti paraméterekkel rendelkező fát), és nem végez további iterációt.
  
7.  A **véletlenszerű számú magok** esetében opcionálisan adjon meg egy nem negatív egész számot, amelyet véletlenszerű mag értékként kíván használni. A vetőmagok megadásával biztosítható az azonos adatokkal és paraméterekkel rendelkező futtatások közötti reprodukálhatóság.  
  
     A véletlenszerű magok alapértelmezett értéke 0, ami azt jelenti, hogy a kezdeti mag értékét a rendszer órája szerzi be.  A véletlenszerű magok használatával végzett egymást követő futtatások különböző eredményekkel rendelkezhetnek.
  

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
   
## <a name="results"></a>Results (Eredmények)

A betanítás befejezése után:

+ Ha menteni szeretné a betanított modell pillanatképét, válassza a **kimenetek** fület a **vonat modell** moduljának jobb oldali paneljén. Válassza az **adatkészlet regisztrálása** ikont a modell újrafelhasználható modulként való mentéséhez.

+ A modell pontozáshoz való használatához adja hozzá a **pontszám modell** modult egy folyamathoz.

## <a name="next-steps"></a>Következő lépések

Tekintse [meg a Azure Machine learning elérhető modulok készletét](module-reference.md) . 