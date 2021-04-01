---
title: 'Neurális hálózat regressziója: modul leírása'
titleSuffix: Azure Machine Learning
description: Megtudhatja, hogyan használhatja a Azure Machine Learning neurális hálózat regressziós modulját a regressziós modell testreszabható neurális hálózati algoritmus használatával történő létrehozásához.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 04/22/2020
ms.openlocfilehash: 403576454615effeb53651b51679681422b08e9e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "90890363"
---
# <a name="neural-network-regression-module"></a>Neurális hálózat regressziós modulja

*Egy regressziós modellt hoz létre egy neurális hálózati algoritmus használatával*  
  
 Kategória: Machine Learning/modell inicializálása/regresszió
  
## <a name="module-overview"></a>Modul áttekintése  

Ez a cikk a Azure Machine Learning Designer egyik modulját ismerteti.

Ezzel a modullal létrehozhat egy regressziós modellt egy testreszabható neurális hálózati algoritmus használatával.
  
 Habár a neurális hálózatok széles körben ismertek a mélyreható tanulásban és az összetett problémák modellezésében, például a képfelismerésben, könnyen alkalmazkodnak a regressziós problémákhoz. A statisztikai modellek bármely osztálya lehet egy neurális hálózat, ha adaptív súlyozást használnak, és a bemenetük nem lineáris függvényeit is megközelítik. Így a neurális hálózat regressziója olyan problémákra alkalmas, amelyekben a hagyományos regressziós modell nem fér hozzá a megoldásokhoz.
  
 A neurális hálózat regressziója felügyelt tanulási módszer, ezért *címkézett adatkészletet* igényel, amely tartalmaz egy címke oszlopot. Mivel a regressziós modell egy numerikus értéket jelez, a Label oszlopnak numerikus adattípusnak kell lennie.  
  
 A modellt úgy is betaníthatja, hogy a modell és a címkézett adatkészlet bemenetként adja meg a [betanítási modellt](./train-model.md). A betanított modell ezután felhasználható az új bemeneti példák értékeinek előrejelzésére.  
  
## <a name="configure-neural-network-regression"></a>Az neurális hálózat regressziójának konfigurálása 

A neurális hálózatokat széles körben lehet testreszabni. Ez a szakasz azt ismerteti, hogyan hozhat létre modellt két módszer használatával:
  
+ [Neurális hálózati modell létrehozása az alapértelmezett architektúra használatával](#bkmk_DefaultArchitecture)  
  
    Ha elfogadja az alapértelmezett neurális hálózati architektúrát, a **Tulajdonságok** ablaktáblán állíthatja be a neurális hálózat viselkedését vezérlő paramétereket, például a rejtett rétegben található csomópontok számát, a tanulási arányt és a normalizálás funkciót.

    Kezdje itt, ha ismerkedik a neurális hálózatokkal. A modul számos testreszabást támogat, valamint a modell finomhangolását a neurális hálózatok mélyreható ismerete nélkül. 

+ Egyéni architektúra definiálása egy neurális hálózathoz 

    Akkor használja ezt a beállítást, ha további rejtett rétegeket szeretne hozzáadni, vagy teljes mértékben testre szeretné szabni a hálózati architektúrát, a kapcsolatait és az aktiválási funkciókat.
    
    Ez a lehetőség akkor a legjobb, ha már némileg ismeri a neurális hálózatokat. A hálózati architektúrát a net # nyelv használatával határozhatja meg.  

##  <a name="create-a-neural-network-model-using-the-default-architecture"></a><a name="bkmk_DefaultArchitecture"></a> Neurális hálózati modell létrehozása az alapértelmezett architektúra használatával

1.  Adja hozzá a **neurális hálózat regressziós** modulját a folyamathoz a tervezőben. Ezt a modult a **regressziós** kategóriában, a **Machine learning**, az **inicializálás** lehetőség alatt találja. 
  
2. Adja meg, hogyan szeretné képezni a modellt az **oktatói mód létrehozása** lehetőség beállításával.  
  
    -   **Egyetlen paraméter**: akkor válassza ezt a lehetőséget, ha már tudja, hogyan szeretné konfigurálni a modellt.

    -   **Paraméter tartománya**: akkor válassza ezt a lehetőséget, ha nem biztos benne, hogy a legjobb paramétereket szeretné használni, és szeretne futtatni egy paramétert. Válassza ki a megismételni kívánt értékek tartományát, és a [finomhangolási modell hiperparaméterek beállítása](tune-model-hyperparameters.md) az optimális eredményeket eredményező hiperparaméterek beállítása meghatározásához megadott beállítások összes lehetséges kombinációján.   

3.  A **rejtett réteg specifikációjában** válassza a **teljesen csatlakoztatott eset** elemet. Ez a lehetőség létrehoz egy modellt az alapértelmezett neurális hálózati architektúrával, amely egy neurális hálózat regressziós modelljéhez a következő attribútumokat használja:  
  
    + A hálózat pontosan egy rejtett réteggel rendelkezik.
    + A kimeneti réteg teljes mértékben csatlakozik a rejtett réteghez, és a rejtett réteg teljesen csatlakoztatva van a bemeneti réteghez.
    + A rejtett rétegben található csomópontok számát a felhasználó állíthatja be (az alapértelmezett érték 100).  
  
    Mivel a bemeneti rétegben lévő csomópontok számát a betanítási adatokban található szolgáltatások száma határozza meg, a regressziós modellben csak egy csomópont lehet a kimeneti rétegben.  
  
4. A **rejtett csomópontok számának** megírásával adja meg a rejtett csomópontok számát. Az alapértelmezett érték egy rejtett réteg, amely 100 csomóponttal rendelkezik. (Ez a beállítás nem érhető el, ha a NET # használatával egyéni architektúrát határoz meg.)
  
5.  A **tanulási arány** mezőben adjon meg egy értéket, amely meghatározza az egyes iterációk lépéseit a javítás előtt. A tanulási ráta nagyobb értéke miatt a modell gyorsabban konvergálhat, de a helyi minimumok túllépése is lehet.

6.  A **tanulási ismétlések számának** megadásával meghatározhatja, hogy az algoritmus hány alkalommal dolgozza fel a betanítási eseteket.


8.  **A lendülethez** adja meg a tanulás során alkalmazandó értéket az előző iterációk csomópontjainak súlyozása alapján.

10. Az ismétlések közötti váltáshoz válassza ki a kívánt lehetőséget, majd a **shuffle példákat**. Ha kijelöli ezt a beállítást, a rendszer a folyamat minden egyes futtatásakor pontosan ugyanabban a sorrendben dolgozza fel az eseteket.
  
11. A **véletlenszerű számú magok** esetében igény szerint megadhatja a vetőmagként használandó értéket. A magok értékének megadásával akkor lehet hasznos, ha biztosítani szeretné az egyazon folyamaton belüli futtatások ismétlődését.
  
13. Egy képzési adatkészlet csatlakoztatása és a modell betanítása:

    + Ha az **oktatói módot** **egyetlen paraméterként** állítja be, csatlakoztasson egy címkézett adatkészletet és a [betanítási modell](train-model.md) modult.  
  
    + Ha az **oktatói mód** beállítása **paraméter-tartományra** van beállítva, csatlakoztasson egy címkézett adatkészletet, és a modell betanításához használja a [modell hiperparaméterek beállítása](tune-model-hyperparameters.md).  
  
    > [!NOTE]
    > 
    > Ha egy paraméter tartományát adja át a [betanítási modellnek](train-model.md), az csak az alapértelmezett értéket használja az egyetlen paraméteres listában.  
    > 
    > Ha egy paraméter értékének egyetlen készletét adja át a [Tune Model hiperparaméterek beállítása](tune-model-hyperparameters.md) modulnak, amikor az egyes paraméterekhez különböző beállításokat vár, figyelmen kívül hagyja az értékeket, és a tanuló alapértelmezett értékeit használja.  
    > 
    > Ha a **paraméter tartománya** lehetőséget választja, és egyetlen értéket ad meg bármelyik paraméterhez, akkor a rendszer az egyetlen megadott értéket használja a teljes takarítás során, még akkor is, ha más paraméterek egy adott tartományon változnak.  
  
   
14. A folyamat elküldése.  

## <a name="results"></a>Results (Eredmények)

A betanítás befejezése után:

- Ha menteni szeretné a betanított modell pillanatképét, válassza a **kimenetek** fület a **vonat modell** moduljának jobb oldali paneljén. Válassza az **adatkészlet regisztrálása** ikont a modell újrafelhasználható modulként való mentéséhez.

## <a name="next-steps"></a>Következő lépések

Tekintse [meg a Azure Machine learning elérhető modulok készletét](module-reference.md) . 