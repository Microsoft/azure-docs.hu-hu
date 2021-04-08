---
title: 'Többosztályos logisztikai regresszió: modul leírása'
titleSuffix: Azure Machine Learning
description: Megtudhatja, hogyan használhatja a többosztályos logisztikai regressziós modult Azure Machine Learning Designerben több érték előrejelzéséhez.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 04/22/2020
ms.openlocfilehash: e2bbc28735bcbfd952c4941453956acd0568ea67
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "93420733"
---
# <a name="multiclass-logistic-regression-module"></a>Többosztályos logisztikai regressziós modul

Ez a cikk a Azure Machine Learning Designer egyik modulját ismerteti.

Ezzel a modullal létrehozhat egy logisztikai regressziós modellt, amely több érték előrejelzésére is használható.

A logisztikai regressziót használó besorolás felügyelt tanulási módszer, ezért címkével ellátott adatkészletet igényel. A modellt úgy kell betanítani, hogy a modellt és a címkézett adatkészletet bemenetként adja meg egy modulhoz, például a [betanítási modellhez](./train-model.md). A betanított modell ezután felhasználható az új bemeneti példák értékeinek előrejelzésére.

A Azure Machine Learning egy [kétosztályos logisztikai regressziós](./two-class-logistic-regression.md) modult is biztosít, amely a bináris vagy dichotóm változók besorolására alkalmas.

## <a name="about-multiclass-logistic-regression"></a>A többosztályos logisztikai regresszió ismertetése

A logisztikai regresszió egy jól ismert módszer a statisztikában, amely az eredmény valószínűségének előrejelzésére szolgál, és népszerű a besorolási feladatokhoz. Az algoritmus azt jelzi, hogy egy esemény előfordulásának valószínűsége egy logisztikai függvénynek megfelelő adat betöltésével várható. 

A többosztályos logisztikai regresszió esetében az osztályozó használatával több eredmény is kiszámítható.

## <a name="configure-a-multiclass-logistic-regression"></a>Többosztályos logisztikai regresszió konfigurálása

1. Adja hozzá a **többosztályos logisztikai regressziós** modult a folyamathoz.

2. Határozza meg, hogyan kívánja képezni a modellt az **oktatói mód létrehozása** lehetőség beállításával.

    + **Egyetlen paraméter**: akkor használja ezt a beállítást, ha tudja, hogyan szeretné konfigurálni a modellt, és az értékek egy adott készletét argumentumként adja meg.

    + **Paraméter tartománya**: akkor válassza ezt a lehetőséget, ha nem biztos benne, hogy a legjobb paramétereket szeretné használni, és szeretne futtatni egy paramétert. Válassza ki a megismételni kívánt értékek tartományát, és a [finomhangolási modell hiperparaméterek beállítása](tune-model-hyperparameters.md) az optimális eredményeket eredményező hiperparaméterek beállítása meghatározásához megadott beállítások összes lehetséges kombinációján.  

3. **Optimalizálási tolerancia**: az optimalizáló konvergenciájának küszöbértékét határozza meg. Ha az ismétlések közötti javulás kisebb a küszöbértéknél, az algoritmus leáll, és az aktuális modellt adja vissza.

4. **L1 regularizációs súlyozás**, **L2 regularizációs súlya**: adjon meg egy értéket, amelyet az L1 és az L2 regularizációs paraméterekhez kell használni. A nem nulla értékű érték mindkét esetében ajánlott.

    A regularizációs olyan módszer, amellyel megelőzhető a rendkívül hatékony értékekkel rendelkező modellek szankcionálása. A regularizációs úgy működik, hogy a hipotézis hibája alapján együttható értékekkel társított büntetést ad hozzá. A rendkívül hatékony értékekkel rendelkező pontos modell többek között büntethető, de a kevésbé pontos, konzervatív értékekkel rendelkező modellek kevesebbet büntetik.

     Az L1 és az L2 regularizációs különböző effektusokkal és felhasználással rendelkeznek. Az L1 a ritka modellekre is alkalmazható, amelyek hasznosak lehetnek a nagy dimenziós adattípusok használatakor. Ezzel szemben az L2 regularizációs a nem ritka adatértékekhez ajánlott.  Ez az algoritmus az L1 és az L2 regularizációs értékek lineáris kombinációját támogatja: Ez a (z), ha `x = L1` és `y = L2` , `ax + by = c` meghatározza a regularizációs-kifejezések lineáris kiterjedését.

     Az L1 és a L2 kifejezések különböző lineáris kombinációit tervezték logisztikai regressziós modellekhez, például a [rugalmas háló regularizációs](https://wikipedia.org/wiki/Elastic_net_regularization).

6. **Véletlenszerű számú mag**: adjon meg egy egész számot, amelyet az algoritmus magjaként kíván használni, ha azt szeretné, hogy az eredmények a futtatások során ismételhetők legyenek. Ellenkező esetben a rendszer egy rendszeróra-értéket használ a magokként, amely némileg eltérő eredményeket eredményezhet ugyanazon folyamat futtatásában.

8. Egy címkézett adatkészlet csatlakoztatása és a modell betanítása:

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



## <a name="next-steps"></a>Következő lépések

Tekintse [meg a Azure Machine learning elérhető modulok készletét](module-reference.md) . 