---
title: 'Two-Class átlagos Perceptron: modul-hivatkozás'
titleSuffix: Azure Machine Learning
description: Megtudhatja, hogyan használhatja a tervezőben a Two-Class átlagos Perceptron modult bináris osztályozó létrehozásához.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 04/22/2020
ms.openlocfilehash: f1161dc99b84026bcc830c08b63b95d0bf3b0994
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "93421141"
---
# <a name="two-class-averaged-perceptron-module"></a>Two-Class átlagos Perceptron modul

Ez a cikk a Azure Machine Learning Designer egyik modulját ismerteti.

Ezzel a modullal gépi tanulási modellt hozhat létre az átlagos perceptron algoritmus alapján.  
  
Ez a besorolási algoritmus felügyelt tanulási módszer, és olyan *címkézett adatkészletet* igényel, amely tartalmaz egy címke oszlopot. A modellt úgy is betaníthatja, hogy a modell és a címkézett adatkészlet bemenetként adja meg a [betanítási modellt](./train-model.md). A betanított modell ezután felhasználható az új bemeneti példák értékeinek előrejelzésére.  

### <a name="about-averaged-perceptron-models"></a>Az átlagos perceptron modellek

Az *átlagos perceptron módszer* egy neurális hálózat korai és egyszerű verziója. Ebben a megközelítésben a bemenetek egy lineáris függvény alapján több lehetséges kimenetbe vannak besorolva, és a szolgáltatás vektora által származtatott súlyok készletével kombinálva, így a "perceptron" névvel.

Az egyszerűbb perceptron modellek a lineárisan elválasztható minták tanulására alkalmasak, míg a neurális hálózatok (különösen a mély neurális hálózatok) összetettebb osztálybeli határokat tudnak modellezni. Azonban a perceptrons gyorsabbak, és mivel az eseteket a sorosan dolgozzák fel, a perceptrons folyamatos képzéssel használható.

## <a name="how-to-configure-two-class-averaged-perceptron"></a>Two-Class átlagos Perceptron konfigurálása

1.  Adja hozzá a **kétosztályos átlagú Perceptron-** modult a folyamathoz.  

2.  Határozza meg, hogyan kívánja képezni a modellt az **oktatói mód létrehozása** lehetőség beállításával.  
  
    -   **Egyetlen paraméter**: ha tudja, hogyan szeretné konfigurálni a modellt, adjon meg egy adott értékeket argumentumként.

    -   **Paraméter tartománya**: akkor válassza ezt a lehetőséget, ha nem biztos benne, hogy a legjobb paramétereket szeretné használni, és szeretne futtatni egy paramétert. Válassza ki a megismételni kívánt értékek tartományát, és a [finomhangolási modell hiperparaméterek beállítása](tune-model-hyperparameters.md) az optimális eredményeket eredményező hiperparaméterek beállítása meghatározásához megadott beállítások összes lehetséges kombinációján.  
  
3.  A **tanulási arány** mezőben határozza meg a *képzési arány* értékét. A tanulási arány értéke a modell minden egyes tesztelésekor és javításakor a sztochasztikus gradiensben használt lépés méretét szabályozza.
  
     A sebesség csökkentésével gyakrabban teszteli a modellt, és azzal a kockázattal jár, hogy egy helyi fennsíkon fog elakadni. A lépés elvégzésével megközelítheti a valódi minimumokat, és így gyorsabban is megoszthatja őket.
  
4.  Az **Ismétlések maximális száma** beállításnál adja meg, hogy az algoritmus hány alkalommal vizsgálja meg a betanítási adatmennyiséget.  
  
     A korai leállítás gyakran jobb általánosítást biztosít. Az ismétlések számának növelése növeli a beilleszkedés kockázatát.
  
5.  A **véletlenszerű számú magok** esetében szükség esetén adjon meg egy egész számot, amelyet magként kíván használni. A magok használata ajánlott, ha biztosítani szeretné, hogy a folyamat megismételhetősége fusson.  
  
1.  Csatlakoztasson egy betanítási adatkészletet, és tanítsa be a modellt:

    + Ha az **oktatói módot** **egyetlen paraméterként** állítja be, csatlakoztasson egy címkézett adatkészletet és a [betanítási modell](train-model.md) modult.  
  
    + Ha az **oktatói mód** beállítása **paraméter-tartományra** van beállítva, csatlakoztasson egy címkézett adatkészletet, és a modell betanításához használja a [modell hiperparaméterek beállítása](tune-model-hyperparameters.md).  
  
    > [!NOTE]
    > 
    > Ha egy paraméter tartományát adja át a [betanítási modellnek](train-model.md), az csak az alapértelmezett értéket használja az egyetlen paraméteres listában.  
    > 
    > Ha egy paraméter értékének egyetlen készletét adja át a [Tune Model hiperparaméterek beállítása](tune-model-hyperparameters.md) modulnak, amikor az egyes paraméterekhez különböző beállításokat vár, figyelmen kívül hagyja az értékeket, és a tanuló alapértelmezett értékeit használja.  
    > 
    > Ha a **paraméter tartománya** lehetőséget választja, és egyetlen értéket ad meg bármelyik paraméterhez, akkor a rendszer az egyetlen megadott értéket használja a teljes takarítás során, még akkor is, ha más paraméterek egy adott tartományon változnak.




## <a name="next-steps"></a>Következő lépések

Tekintse [meg a Azure Machine learning elérhető modulok készletét](module-reference.md) . 