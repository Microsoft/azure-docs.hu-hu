---
title: 'Poisson-regresszió: modul-hivatkozás'
titleSuffix: Azure Machine Learning
description: Megtudhatja, hogyan hozhat létre egy Poisson regressziós modellt a Azure Machine Learning Designer Poisson regressziós moduljának használatával.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 07/13/2020
ms.openlocfilehash: 2dfd8b3d919f9eeb3e183135ef543f417c878977
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "93420699"
---
# <a name="poisson-regression"></a>Poisson-regresszió

Ez a cikk a Azure Machine Learning Designer egyik modulját ismerteti.

Ezzel a modullal létrehozhat egy Poisson-regressziós modellt egy folyamaton belül. A Poisson-regresszió a numerikus értékek előrejelzésére szolgál, jellemzően a számítások számát. Ezért ezt a modult csak akkor kell használni a regressziós modell létrehozásához, ha az előre jelezni kívánt értékek megfelelnek a következő feltételeknek:

- A Response változó Poisson- [eloszlással](https://en.wikipedia.org/wiki/Poisson_distribution)rendelkezik.  

- A számok nem lehetnek negatívak. Ha negatív címkékkel próbálja használni, a metódus nem fog működni.

- A Poisson-eloszlás diszkrét eloszlás; ezért nem értelmezhető, hogy ezt a metódust nem egész számokkal használja.

> [!TIP]
> Ha a cél nem szám, a Poisson-regresszió valószínűleg nem megfelelő módszer. Próbálkozzon [más regressziós modulokkal a tervezőben](./module-reference.md#machine-learning-algorithms). 

Miután beállította a regressziós módszert, be kell tanítania a modellt egy olyan adatkészlet használatával, amely a megjósolni kívánt értékre mutató példákat tartalmaz. Ezt követően a betanított modell segítségével előrejelzéseket készíthet.

## <a name="more-about-poisson-regression"></a>További információ a Poisson regresszióról

A Poisson-regresszió egy speciális regressziós elemzés, amely általában a darabszámok modellezésére használatos. A Poisson-regresszió például hasznos lehet a következő helyzetekben:

- A repülőgép-járatokhoz társított megfázások számának modellezése

- A vészhelyzeti szolgáltatási hívások számának becslése egy esemény során

- Az előléptetést követő ügyfél-lekérdezések számának kivetítése

- Készenléti táblák létrehozása

Mivel a válasz változó Poisson-eloszlással rendelkezik, a modell eltérő feltételezéseket tesz az adatforgalomra és a valószínűségi eloszlásra, mint például a legkisebb négyzetes regresszió. Ezért a Poisson-modelleket más regressziós modellektől eltérően kell értelmezni.

## <a name="how-to-configure-poisson-regression"></a>A Poisson-regresszió konfigurálása

1. Adja hozzá a **Poisson regressziós** modult a folyamathoz a Designerben. Ez a modul a **regressziós** kategóriában **Machine learning algoritmusok** alatt található.

2. Adjon hozzá egy adatkészletet, amely a megfelelő típusú betanítási adatokat tartalmazza. 

    Javasoljuk, hogy a regressor betanításához használja az [adatok normalizálása](normalize-data.md) a bemeneti adatkészlet normalizálása előtt.

3. A **Poisson regressziós** moduljának jobb oldali ablaktábláján határozza meg, hogyan kívánja betanítani a modellt az **oktatói mód létrehozása** lehetőség beállításával.  
  
    - **Egyetlen paraméter**: ha tudja, hogyan szeretné konfigurálni a modellt, adjon meg egy adott értékeket argumentumként.
  
    - **Paraméter tartománya**: Ha nem biztos benne, hogy a legjobb paramétereket használja, hajtson végre egy paramétert a [Tune Model hiperparaméterek beállítása](tune-model-hyperparameters.md) modullal. Az oktató megismétli több megadott értéket az optimális konfiguráció megtalálásához.
  
4. **Optimalizálási tolerancia**: adjon meg egy értéket, amely meghatározza a tűréshatár intervallumát az optimalizálás során. Minél kisebb az érték, annál lassabb és pontosabb a szerelvény.

5. **L1 regularizációs súlyozás** és **L2 regularizációs súlyozás**: az L1 és a L2 regularizációs használandó értékek megadása. A *regularizációs* megkötéseket hoz létre a modell azon szempontjaira vonatkozóan, amelyek függetlenek a betanítási információtól. A regularizációs általában a túlilleszkedés elkerülésére használják. 

    - Az L1 regularizációs akkor hasznos, ha a cél egy olyan modell, amely a lehető legritkább.

        Az L1 regularizációs úgy végzi el, hogy kivonja a súlyozási vektor L1 súlyozását abból a veszteség-kifejezésből, amelyet a tanuló a lehető legkisebbre próbál. Az L1 szabvány jó közelítést mutat a l0-normához, amely a nem nulla koordináták száma.

    - Az L2 regularizációs meggátolja, hogy a súlyozási vektor egyetlen koordinálása ne legyen túl nagy mértékű. Az L2 regularizációs akkor hasznos, ha a cél egy olyan modell, amely kisebb összesített súlyozással rendelkezik.

    Ebben a modulban az L1 és az L2 regularizations kombinációját is alkalmazhatja. Az L1 és az L2 regularizációs kombinálásával kényszerítheti a paraméterek értékeinek mértékét. A tanuló megpróbálja minimalizálni a büntetést a veszteség minimalizálása érdekében.

    Az L1 és az L2 regularizációs jó megvitatására lásd: [L1 és L2 regularizációs Machine learning](/archive/msdn-magazine/2015/february/test-run-l1-and-l2-regularization-for-machine-learning).

6. Az **L-BFGS memória mérete**: Itt adhatja meg, hogy mennyi memóriát kell lefoglalni a modell-szerelvényhez és az optimalizáláshoz.

     Az L-BFGS egy adott optimalizálási módszer a Broyden – Fletcher – Goldfarb – Shanno (BFGS) algoritmus alapján. A metódus korlátozott mennyiségű memóriát (L) használ a következő lépés irányának kiszámításához.

     A paraméter módosításával hatással lehet a következő lépés kiszámításához tárolt korábbi pozíciók és átmenetek számára.

7. Kapcsolja össze a betanítási adatkészletet és a nem betanított modellt az egyik betanítási modulhoz: 

    - Ha az **oktatói módot** **egyetlen paraméterként** állítja be, használja a [Train Model](train-model.md) modult.

    - Ha az **oktatói mód** beállítása **paraméter-tartományra** van beállítva, használja a [Model hiperparaméterek beállítása (modell hangolása](tune-model-hyperparameters.md) ) modult.

    > [!WARNING]
    > 
    > - Ha egy paraméter tartományát adja át a [betanítási modellnek](train-model.md), az csak az első értéket használja a paraméter tartománya listában.
    > 
    > - Ha egy paraméter értékének egyetlen készletét adja át a [Tune Model hiperparaméterek beállítása](tune-model-hyperparameters.md) modulnak, amikor az egyes paraméterekhez különböző beállításokat vár, figyelmen kívül hagyja az értékeket, és a tanuló alapértelmezett értékeit használja.
    > 
    > - Ha a **paraméter tartománya** lehetőséget választja, és egyetlen értéket ad meg bármelyik paraméterhez, akkor a rendszer az egyetlen megadott értéket használja a teljes takarítás során, még akkor is, ha más paraméterek egy adott tartományon változnak.

8.  A folyamat elküldése.

## <a name="results"></a>Results (Eredmények)

A betanítás befejezése után:

+ Ha menteni szeretné a betanított modell pillanatképét, válassza ki a betanítási modult, majd váltson a **kimenet + naplók** lapra a jobb oldali panelen. Kattintson az ikonra az **adatkészlet regisztrálása** elemre.  A mentett modellt megtalálhatja modulként a modul fájában. 

## <a name="next-steps"></a>Következő lépések

Tekintse [meg a Azure Machine learning elérhető modulok készletét](module-reference.md) .