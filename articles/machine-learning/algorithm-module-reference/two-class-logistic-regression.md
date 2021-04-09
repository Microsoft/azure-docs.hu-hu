---
title: 'Two-Class logisztikai regresszió: modul leírása'
titleSuffix: Azure Machine Learning
description: Megtudhatja, hogyan használhatja a Azure Machine Learning Two-Class logisztikai regressziós modulját bináris osztályozó létrehozásához.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 04/22/2020
ms.openlocfilehash: 2e29a666f4d478e11986f834cff94d9743223f22
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "96012646"
---
# <a name="two-class-logistic-regression-module"></a>Two-Class logisztikai regressziós modul

Ez a cikk a Azure Machine Learning Designer egyik modulját ismerteti.

Ezzel a modullal létrehozhat egy logisztikai regressziós modellt, amely két (és csak két) eredmény előrejelzésére használható. 

A logisztikai regresszió egy jól ismert statisztikai módszer, amely számos különféle probléma modellezésére használható. Ez az algoritmus *felügyelt tanulási* módszer;  Ezért olyan adatkészletet kell megadnia, amely már tartalmazza a modell betanításához szükséges eredményeket.  

### <a name="about-logistic-regression"></a>A logisztikai regresszió ismertetése  

A logisztikai regresszió egy jól ismert módszer a statisztikában, amely az eredmény valószínűségének előrejelzésére szolgál, és különösen népszerű a besorolási feladatokhoz. Az algoritmus azt jelzi, hogy egy esemény előfordulásának valószínűsége egy logisztikai függvénynek megfelelő adat betöltésével várható.
  
Ebben a modulban a besorolási algoritmus dichotóm vagy bináris változóra van optimalizálva. Ha több eredmény besorolására van szükség, használja a [többosztályos logisztikai regressziós](./multiclass-logistic-regression.md) modult.

##  <a name="how-to-configure"></a>Konfigurálás  

A modell betanításához meg kell adnia egy olyan adatkészletet, amely címkét vagy osztály oszlopot tartalmaz. Mivel ez a modul kétosztályos problémákhoz készült, a címke vagy az osztály oszlopnak pontosan két értéket kell tartalmaznia. 

Előfordulhat például, hogy a Label (címke) oszlop [szavazott], és az "igen" vagy "No" lehetséges értékekkel rendelkezik. Vagy lehet, hogy [hitelkockázat], a "magas" vagy "alacsony" lehetséges értékekkel. 
  
1.  Adja hozzá a **kétosztályos logisztikai regressziós** modult a folyamathoz.  
  
2.  Határozza meg, hogyan kívánja képezni a modellt az **oktatói mód létrehozása** lehetőség beállításával.  
  
    -   **Egyetlen paraméter**: ha tudja, hogyan szeretné konfigurálni a modellt, megadhatja az értékek adott készletét argumentumként.  

    -   **Paraméter tartománya**: Ha nem biztos benne, hogy a legjobb paramétereket használja, megkeresheti az optimális paramétereket az [Tune Model hiperparaméterek beállítása](tune-model-hyperparameters.md) modullal. Bizonyos értékeket adhat meg, és az oktató a beállítások több kombinációján keresztül megismétli a legjobb eredményt eredményező értékek kombinációját.
  
3.  Az **optimalizálási tolerancia** beállításnál a modell optimalizálásakor használandó küszöbértéket határozza meg. Ha az ismétlések közötti javulás a megadott küszöbérték alá esik, az algoritmus úgy tekintendő, hogy a megoldás konvergál, és a képzés leáll.  
  
4.  Az **L1 regularizációs súlyozáshoz** és az **L2 regularizációs súlyozáshoz** adja meg az L1 és a regularizációs paraméterekhez használandó értéket. A nem nulla értékű érték mindkét esetében ajánlott.  
     A *regularizációs* olyan módszer, amellyel megelőzhető a rendkívül hatékony értékekkel rendelkező modellek szankcionálása. A regularizációs úgy működik, hogy a hipotézis hibája alapján együttható értékekkel társított büntetést ad hozzá. Így a rendkívül hatékony értékekkel rendelkező pontos modell többre is felhasználható, de a kevésbé pontos, konzervatív értékekkel rendelkező modellek kevesebbet büntetik.  
  
     Az L1 és az L2 regularizációs különböző effektusokkal és felhasználással rendelkeznek.  
  
    -   Az L1 a ritka modellekre is alkalmazható, amelyek hasznosak lehetnek a nagy dimenziós adattípusok használatakor.  
  
    -   Ezzel szemben az L2 regularizációs a nem ritka adatértékekhez ajánlott.  
  
     Ez az algoritmus az L1 és az L2 regularizációs értékek lineáris kombinációját támogatja: Ez a (z), ha <code>x = L1</code> és <code>y = L2</code> , majd <code>ax + by = c</code> meghatározza a regularizációs kifejezések lineáris hosszát.  
  
    > [!NOTE]
    >  Többet szeretne megtudni az L1 és az L2 regularizációs? A következő cikk azt ismerteti, hogy az L1 és az L2 regularizációs miben különböznek, és hogy azok hogyan befolyásolják a modell-kialakítást, a logisztikai regressziós és neurális hálózati modellek esetében pedig:  [L1 és L2 regularizációs Machine learning](/archive/msdn-magazine/2015/february/test-run-l1-and-l2-regularization-for-machine-learning)  
    >
    > Az L1 és a L2 kifejezések különböző lineáris kombinációit a logisztikai regressziós modellekhez alakítottuk ki: például a [rugalmas háló regularizációs](https://wikipedia.org/wiki/Elastic_net_regularization). Javasoljuk, hogy ezeket a kombinációkat a modellben érvényes lineáris kombináció meghatározására hivatkozzon.
      
5.  Az **l-BFGS memória méretéhez** határozza meg az *l-BFGS* optimalizáláshoz használandó memória mennyiségét.  
  
     Az L-BFGS a "korlátozott memória Broyden-Fletcher-Goldfarb-Shanno" értékre áll. Ez egy optimalizálási algoritmus, amely népszerű a paraméterek becsléséhez. Ez a paraméter a következő lépés kiszámítására szolgáló korábbi pozíciók és színátmenetek számát jelzi.  
  
     Ez az optimalizálási paraméter a következő lépés és irány kiszámításához használt memória mennyiségét korlátozza. Ha kevesebb memóriát ad meg, a képzés gyorsabb, de kevésbé pontos.  
  
6.  A **véletlenszerű számú magok** mezőben adjon meg egy egész számot. A mag értékének meghatározása akkor fontos, ha azt szeretné, hogy az eredmények reprodukálva legyenek ugyanazon folyamat több futtatására.  
  
  
8. Vegyen fel egy címkével ellátott adatkészletet a folyamatba, és tanítsa be a modellt:

    + Ha az **oktatói módot** **egyetlen paraméterként** állítja be, csatlakoztasson egy címkézett adatkészletet és a [betanítási modell](train-model.md) modult.  
  
    + Ha az **oktatói mód** beállítása **paraméter-tartományra** van beállítva, csatlakoztasson egy címkézett adatkészletet, és a modell betanításához használja a [modell hiperparaméterek beállítása](tune-model-hyperparameters.md).  
  
    > [!NOTE]
    > 
    > Ha egy paraméter tartományát adja át a [betanítási modellnek](train-model.md), az csak az alapértelmezett értéket használja az egyetlen paraméteres listában.  
    > 
    > Ha egy paraméter értékének egyetlen készletét adja át a [Tune Model hiperparaméterek beállítása](tune-model-hyperparameters.md) modulnak, amikor az egyes paraméterekhez különböző beállításokat vár, figyelmen kívül hagyja az értékeket, és a tanuló alapértelmezett értékeit használja.  
    > 
    > Ha a **paraméter tartománya** lehetőséget választja, és egyetlen értéket ad meg bármelyik paraméterhez, akkor a rendszer az egyetlen megadott értéket használja a teljes takarítás során, még akkor is, ha más paraméterek egy adott tartományon változnak.  
  
9. A folyamat elküldése.  
  
## <a name="results"></a>Results (Eredmények)

A betanítás befejezése után:
 
  
+ Az új adatokra vonatkozó előrejelzések készítéséhez használja a betanított modellt és az új adatokat bemenetként a [pontszám modell](./score-model.md) modulba. 


## <a name="next-steps"></a>Következő lépések

Tekintse [meg a Azure Machine learning elérhető modulok készletét](module-reference.md) .