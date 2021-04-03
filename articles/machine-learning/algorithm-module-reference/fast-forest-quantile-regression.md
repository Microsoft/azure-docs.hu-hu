---
title: 'Gyors erdő Quantile regresszió: modul-hivatkozás'
titleSuffix: Azure Machine Learning
description: Megtudhatja, hogyan hozhat létre olyan regressziós modellt a gyors erdő Quantile regressziós modullal, amely egy adott számú quantiles értékének előrejelzésére képes.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 07/13/2020
ms.openlocfilehash: 6d59d2fabb0b74ef12e33f55467b0ba68e0b7386
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "90907951"
---
# <a name="fast-forest-quantile-regression"></a>Gyors erdő típusú kvantilis regresszió

Ez a cikk a Azure Machine Learning Designer egyik modulját ismerteti.

Ezzel a modullal hozhat létre egy gyors erdő quantile regressziós modellt egy folyamaton belül. Az erdő gyors quantile regressziója akkor hasznos, ha többet szeretne megtudni az előre jelzett érték eloszlásáról, és nem csak egyetlen átlagos előrejelzési értéket kap. Ez a metódus számos alkalmazást tartalmaz, többek között:  
  
- Árak előrejelzése  
  
- A diákok teljesítményének becslése vagy növekedési diagramok alkalmazása a gyermek-fejlesztés értékeléséhez  
  
- A prediktív kapcsolatok feltárása olyan esetekben, amikor csak gyenge a változók közötti kapcsolat  
  
Ez a regressziós algoritmus egy **felügyelt** tanulási módszer, ami azt jelenti, hogy a címkét tartalmazó oszlopot tartalmazó címkézett adatkészletet igényel. Mivel ez egy regressziós algoritmus, a Label oszlop csak numerikus értékeket tartalmazhat.

## <a name="more-about-quantile-regression"></a>További információ a quantile regresszióról

Számos különböző típusú regresszió létezik. Egyszerűen fogalmazva, a regresszió azt jelenti, hogy a modellt egy numerikus vektorként kifejezett célra illeszti be. A statisztikusok azonban egyre fejlettebb módszereket fejlesztenek a regresszióhoz.

A *quantile* legegyszerűbb meghatározása olyan érték, amely az adathalmazokat egyenlő méretű csoportokra osztja fel. a quantile értékek tehát a csoportok közötti határokat jelzik. Statisztikailag beszélve a quantiles a véletlenszerűen változó kumulatív terjesztési funkció (CDF) inverze alapján rendszeres időközönként végrehajtott értékek.

Míg a lineáris regressziós modellek egyetlen becsléssel kísérlik meg egy numerikus változó értékének előrejelzését, ami *azt jelenti*, hogy előfordulhat, hogy előre meg kell becsülnie a cél változó tartományát vagy teljes eloszlását. Erre a célra olyan technikákat fejlesztettek ki, mint a Bayes regresszió és a quantile regresszió.

A Quantile regresszió segítségével megismerheti az előre jelzett érték eloszlását. A faalapú quantile regressziós modelljei, például az ebben a modulban használt, további előnye, hogy a nem számszerű eloszlások előrejelzésére használhatók.

  
## <a name="how-to-configure-fast-forest-quantile-regression"></a>A gyors erdő Quantile-regressziójának konfigurálása

1. Adja hozzá a **gyors erdő Quantile regressziós** modulját a folyamathoz a tervezőben. Ez a modul a **regressziós** kategóriában **Machine learning algoritmusok** alatt található.

2. A **gyors erdő Quantile regressziós** moduljának jobb oldali ablaktábláján határozza meg, hogyan kívánja betanítani a modellt az **oktatói mód létrehozása** lehetőség beállításával.  
  
    - **Egyetlen paraméter**: ha tudja, hogyan szeretné konfigurálni a modellt, adjon meg egy adott értékeket argumentumként. A modell betanításakor használja a [betanítási modellt](train-model.md).
  
    - **Paraméter tartománya**: Ha nem biztos benne, hogy a legjobb paramétereket használja, hajtson végre egy paramétert a [Tune Model hiperparaméterek beállítása](tune-model-hyperparameters.md) modullal. Az oktató megismétli több megadott értéket az optimális konfiguráció megtalálásához.

3. A **fák száma** mezőbe írja be az Ensemble-ban létrehozható fák maximális számát. Ha több fát hoz létre, az általában nagyobb pontosságot eredményez, de a hosszabb betanítási idő díja.  

4. A **levelek száma**, beírhatja a bármely fában létrehozható levelek vagy terminál-csomópontok maximális számát.  

5. **Egy levél kiosztásához szükséges betanítási példányok minimális száma**, itt adhatja meg, hogy milyen minimális számú példa szükséges ahhoz, hogy minden terminál-csomópont (levél) egy fában legyen létrehozva.  
  
     Az érték növelésével növelheti az új szabályok létrehozásának küszöbértékét. Ha például az alapértelmezett érték 1, akkor akár egyetlen esetben is létrehozhat egy új szabályt. Ha az értéket 5-re emeli, a betanítási adatmennyiségnek legalább 5 olyan esetet kell tartalmaznia, amely ugyanazokat a feltételeket teljesíti.

6. A **poggyász frakciója** 0 és 1 közötti számot ad meg, amely az egyes quantiles-csoportok kiépítésekor használandó minták töredékét jelöli. A mintákat véletlenszerűen választjuk ki, cserével.

7. **Bontsa ki a frakciót**, írjon be egy 0 és 1 közötti számot, amely a fa egyes felosztásában használandó szolgáltatások töredékét jelöli. A használt funkciók mindig véletlenszerűen vannak kiválasztva.

8. A **becsült Quantiles** írja be annak a Quantiles a pontosvesszővel tagolt listáját, amelynek a modelljét be szeretné képezni és előrejelzéseket szeretne készíteni.
  
     Ha például a quartiles becslését szeretné létrehozni, írja be a következőt: `0.25; 0.5; 0.75` .  

9. Ha szeretné, beírhatja a **véletlenszám-magok** értékét a modell által használt véletlenszám-generátor kivetéséhez.  Az alapértelmezett érték 0, ami azt jelenti, hogy egy véletlenszerű mag van kiválasztva.
  
     Meg kell adnia egy értéket, ha az egymást követő futtatások során újra létre kell hozni az eredményeket ugyanazon az adategységen.  

10. Kapcsolja össze a betanítási adatkészletet és a nem betanított modellt az egyik betanítási modulhoz: 

    - Ha az **oktatói módot** **egyetlen paraméterként** állítja be, használja a [Train Model](train-model.md) modult.

    - Ha az **oktatói mód** beállítása **paraméter-tartományra** van beállítva, használja a [Model hiperparaméterek beállítása (modell hangolása](tune-model-hyperparameters.md) ) modult.

    > [!WARNING]
    > 
    > - Ha egy paraméter tartományát adja át a [betanítási modellnek](train-model.md), az csak az első értéket használja a paraméter tartománya listában.
    > 
    > - Ha egy paraméter értékének egyetlen készletét adja át a [Tune Model hiperparaméterek beállítása](tune-model-hyperparameters.md) modulnak, amikor az egyes paraméterekhez különböző beállításokat vár, figyelmen kívül hagyja az értékeket, és a tanuló alapértelmezett értékeit használja.
    > 
    > - Ha a **paraméter tartománya** lehetőséget választja, és egyetlen értéket ad meg bármelyik paraméterhez, akkor a rendszer az egyetlen megadott értéket használja a teljes takarítás során, még akkor is, ha más paraméterek egy adott tartományon változnak.

11. A folyamat elküldése.

## <a name="results"></a>Results (Eredmények)

A betanítás befejezése után:

+ Ha menteni szeretné a betanított modell pillanatképét, válassza ki a betanítási modult, majd váltson a **kimenet + naplók** lapra a jobb oldali panelen. Kattintson az ikonra az **adatkészlet regisztrálása** elemre.  A mentett modellt megtalálhatja modulként a modul fájában.

## <a name="next-steps"></a>Következő lépések

Tekintse [meg a Azure Machine learning elérhető modulok készletét](module-reference.md) .
