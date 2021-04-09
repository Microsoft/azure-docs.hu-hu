---
title: 'Two-Class neurális hálózat: modul-hivatkozás'
titleSuffix: Azure Machine Learning
description: Megtudhatja, hogyan használhatja a Azure Machine Learning Two-Class neurális hálózati modulját bináris osztályozó létrehozásához.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 04/22/2020
ms.openlocfilehash: 9131a2439facef00cae818bffef38e536a40a2fd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "93421158"
---
# <a name="two-class-neural-network-module"></a>Two-Class neurális hálózati modul

Ez a cikk a Azure Machine Learning Designer egyik modulját ismerteti.

Ezzel a modullal létrehozhat egy olyan neurális hálózati modellt, amely egy olyan cél előrejelzésére használható, amely csak két értékkel rendelkezik.

A neurális hálózatokat használó besorolás felügyelt tanulási módszer, ezért *címkézett adatkészletet* igényel, amely tartalmaz egy címke oszlopot. Használhatja például ezt a neurális hálózati modellt olyan bináris eredmények előrejelzéséhez, mint például az, hogy a betegnek van-e bizonyos betegsége, illetve hogy a gép valószínűleg meghiúsul-e egy adott időszakon belül.  

A modell meghatározása után betaníthatja azt úgy, hogy címkézett adatkészletet és a modellt bemenetként adja meg a [betanítási modellnek](./train-model.md). A betanított modell ezután felhasználható az új bemenetek értékeinek előrejelzésére.

### <a name="more-about-neural-networks"></a>További információ a neurális hálózatokról

A neurális hálózatok egymással összekapcsolt rétegekből állnak. A bemenetek az első réteg, és egy kimeneti réteghez csatlakoznak egy súlyozott élekből és csomópontokból álló aciklikus gráf használatával.

A bemeneti és a kimeneti rétegek között több rejtett réteget is beszúrhat. A legtöbb prediktív feladat egyszerűen elvégezhető egy vagy több rejtett réteg használatával. A legújabb kutatások azonban azt mutatták, hogy a sok réteggel rendelkező Deep neurális hálózatok (DNN) hatékonyak lehetnek olyan összetett feladatokban, mint például a képek vagy a beszédfelismerés. Az egymást követő rétegek a szemantikai mélység növekvő szintjének modellezésére szolgálnak.

A bemenetek és a kimenetek közötti kapcsolatot a rendszer betanítja a neurális hálózat betanítása a bemeneti adatokon. A gráf iránya a bemeneti rétegből és a kimeneti rétegből folytatódik. A rétegben lévő összes csomópontot a súlyozott élek kötik a következő réteg csomópontjaira.

Egy adott bemenethez tartozó hálózat kimenetének kiszámításához egy értéket kell kiszámítani a rejtett rétegek és a kimeneti réteg minden csomópontján. Az érték úgy van beállítva, hogy kiszámítja az előző réteg csomópontjainak értékeinek súlyozott összegét. Ezután az adott súlyozott összegre alkalmazza az aktiválási függvényt.
  
## <a name="how-to-configure"></a>Konfigurálás

1.  Adja hozzá a **kétosztályos neurális hálózati** modult a folyamathoz. Ezt a modult a **besorolás** kategóriában, a **Machine learning**, az **inicializálás** lehetőség alatt találja.  
  
2.  Határozza meg, hogyan kívánja képezni a modellt az **oktatói mód létrehozása** lehetőség beállításával.  
  
    -   **Egyetlen paraméter**: akkor válassza ezt a lehetőséget, ha már tudja, hogyan szeretné konfigurálni a modellt.

    -   **Paraméter tartománya**: Ha nem biztos benne, hogy a legjobb paramétereket használja, megkeresheti az optimális paramétereket az [Tune Model hiperparaméterek beállítása](tune-model-hyperparameters.md) modullal. Bizonyos értékeket adhat meg, és az oktató a beállítások több kombinációján keresztül megismétli a legjobb eredményt eredményező értékek kombinációját.  

3.  A **rejtett réteg specifikációja** mezőben válassza ki a létrehozandó hálózati architektúra típusát.  
  
    -   **Teljes mértékben csatlakoztatott eset**: az alapértelmezett neurális hálózati architektúrát használja, amelyet a kétosztályos neurális hálózatok határoznak meg a következőképpen:
  
        -   Egy rejtett réteggel rendelkezik.
  
        -   A kimeneti réteg teljes mértékben csatlakozik a rejtett réteghez, és a rejtett réteg teljes mértékben csatlakozik a bemeneti réteghez.
  
        -   A bemeneti rétegben lévő csomópontok száma megegyezik a betanítási adatokban található szolgáltatások számával.
  
        -   A rejtett rétegben található csomópontok számát a felhasználó állítja be. Az alapértelmezett érték 100.
  
        -   A csomópontok száma egyenlő az osztályok számával. Kétosztályos neurális hálózat esetén ez azt jelenti, hogy minden bemenetnek a kimeneti réteg két csomópontjának egyikéhez kell tartoznia.

5.  A **tanulás arányának** meghatározásához adja meg az egyes iterációk során végrehajtott lépés méretét a javítás előtt. A tanulási ráta nagyobb értéke miatt a modell gyorsabban konvergálhat, de a helyi minimumok túllépése is lehet.

6.  A **tanulási ismétlések számának** megadásával adhatja meg, hogy az algoritmus hány alkalommal dolgozza fel a betanítási eseteket.

7.  **A kezdeti tanulási súlyok átmérőjének** megadásához a tanulási folyamat elején meg kell adni a csomópontok súlyozását.

8.  **A lendülethez** meg kell határoznia az előző iterációk csomópontjain való tanulás során alkalmazandó súlyozást.  

10. Válassza a **shuffle példák** lehetőséget az ismétlések közötti esetek rendezéséhez. Ha kijelöli ezt a beállítást, a rendszer a folyamat minden egyes futtatásakor pontosan ugyanabban a sorrendben dolgozza fel az eseteket.
  
11. A **véletlenszerű számú magok** esetében adja meg a vetőmagként használandó értéket.
  
     A magok értékének megadásával akkor lehet hasznos, ha biztosítani szeretné az egyazon folyamaton belüli futtatások ismétlődését.  Ellenkező esetben a rendszer egy rendszeróra-értéket használ a magokként, ami némileg eltérő eredményeket eredményezhet a folyamat minden egyes futtatásakor.
  
13. Vegyen fel egy címkével ellátott adatkészletet a folyamatba, és tanítsa be a modellt:

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

+ Ha menteni szeretné a betanított modell pillanatképét, válassza a **kimenetek** fület a **vonat modell** moduljának jobb oldali paneljén. Válassza az **adatkészlet regisztrálása** ikont a modell újrafelhasználható modulként való mentéséhez.

+ A modell pontozáshoz való használatához adja hozzá a **pontszám modell** modult egy folyamathoz.


## <a name="next-steps"></a>Következő lépések

Tekintse [meg a Azure Machine learning elérhető modulok készletét](module-reference.md) . 
