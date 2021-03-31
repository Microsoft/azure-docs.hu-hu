---
title: 'Az adatnormalizálás: modul-hivatkozás'
titleSuffix: Azure Machine Learning
description: Megtudhatja, hogyan alakíthatja át az adathalmazt *normalizálás* használatával a Azure Machine learning a normalizált adatmodul segítségével.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/22/2020
ms.openlocfilehash: de0a23ca9dea210d91fe259b06622226549ba3b2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "90890447"
---
# <a name="normalize-data-module"></a>Adatmodul normalizálása

Ez a cikk a Azure Machine Learning Designer egyik modulját ismerteti.

Ezzel a modullal lehet átalakítani egy adatkészletet a *normalizálás* segítségével.

A normalizálás olyan technika, amelyet gyakran alkalmaznak a gépi tanuláshoz szükséges adatelőkészítés részeként. A normalizálás célja, hogy az adatkészletben lévő numerikus oszlopok értékeit közös skála használatára módosítsa, és ne torzítsa az értékek tartományán belüli különbségeket, vagy veszítse el az adatokat. Bizonyos algoritmusok esetében a normalizálás is szükséges ahhoz, hogy az adatmodell megfelelően működjön.

Tegyük fel például, hogy a bemeneti adatkészlet egy, 0 és 1 közötti értékeket tartalmazó oszlopot tartalmaz, és egy másik oszlop, amelynek értéke 10 000 és 100 000 között van. A számok *skálájának* nagy különbsége problémákat okozhat, ha az értékeket a modellezés során funkcióként próbálja meg kombinálni.

A *normalizálás* elkerüli ezeket a problémákat olyan új értékek létrehozásával, amelyek fenntartják az általános eloszlást és a forrásadatok arányát, miközben a modellben használt összes numerikus oszlopon belül megtartják az értékeket.

Ez a modul több lehetőséget kínál a numerikus adat átalakítására:

- Az összes érték módosítható egy 0-1-os méretre, vagy átalakíthatja az értékeket úgy, hogy az abszolút értékek helyett percentilis-rangsorként jelöli meg őket.
- A normalizálás egyetlen oszlopra vagy ugyanazon adatkészlet több oszlopára is alkalmazható.
- Ha meg kell ismételnie a folyamatot, vagy ugyanazokat a normalizálás lépéseket kell alkalmaznia más adatokat is, a lépéseket elmentheti normalizálás átalakításként, és alkalmazhatja azokat más adatkészletekre, amelyek ugyanazzal a sémával rendelkeznek.

> [!WARNING]
> Egyes algoritmusok esetében a modell betanítása előtt normalizálni kell az adattípusokat. Más algoritmusok saját adatméretezést vagy normalizálás hajtanak végre. Ezért ha egy prediktív modell létrehozásához használni kívánt gépi tanulási algoritmust választ, mindenképpen tekintse át az algoritmus adatkövetelményeit, mielőtt alkalmazza a betanítási adat normalizálása előtt.

##  <a name="configure-normalize-data"></a>Az adatnormalizálás konfigurálása

A modul használatával egyszerre csak egy normalizáló módszert alkalmazhat. Ezért ugyanazt a normalizáló módszert alkalmazza a rendszer az összes kiválasztott oszlopra. A különböző normalizáló módszerek használatához használja az **Adatnormalizálás** második példányát.

1. Adja hozzá az **adategység normalizálása** modult a folyamathoz. A modult Azure Machine Learningban, az **adatátalakítás** alatt, a **skála és a csökkentés** kategóriában találja.

2. Csatlakoztasson egy olyan adatkészletet, amely legalább egy, az összes számot tartalmazó oszlopot tartalmaz.

3. A normalizálni kívánt numerikus oszlopok kiválasztásához használja az oszlop választóját. Ha nem választja ki az egyes oszlopokat, alapértelmezés szerint az **összes** numerikus típusú oszlop szerepel a bemenetben, és ugyanazt a normalizáló folyamatot alkalmazza az összes kijelölt oszlopra. 

    Ez furcsa eredményekhez vezethet, ha olyan numerikus oszlopokat is tartalmaz, amelyek nem lesznek normalizálva! Mindig gondosan tekintse meg az oszlopokat.

    Ha nem észlelhető numerikus oszlop, ellenőrizze az oszlop metaadatait annak ellenőrzéséhez, hogy az oszlop adattípusa támogatott numerikus típusú-e.

    > [!TIP]
    > Annak érdekében, hogy a megadott típusú oszlopok bemenetként legyenek megadva, próbálja meg az **adatok normalizálása** előtt használni az [Oszlopok kiválasztása az adatkészlet](./select-columns-in-dataset.md) modulban.

4. **Ha be van jelölve, válassza a 0 értéket az állandó oszlopoknál**: ezt a beállítást akkor válassza, ha bármely numerikus oszlop egyetlen változatlan értéket tartalmaz. Ez biztosítja, hogy az ilyen oszlopok ne legyenek használatban a normalizálás műveletekben.

5. Az **átalakítási módszer** legördülő listából válassza ki az összes kijelölt oszlopra alkalmazni kívánt egyetlen matematikai függvényt. 
  
    - **Zscore**: az összes értéket egy z-pontszámra konvertálja.
    
      Az oszlopban szereplő értékek a következő képlettel alakíthatók át:  
  
      ![normalizálás z&#45;pontszámok használatával](media/module/aml-normalization-z-score.png)
  
      A középérték és a szórás kiszámítása minden egyes oszlop esetében külön történik. A statisztikai sokaság szórása használatban van.
  
    - **MinMax**: a min-max normalizáló lineárisan átméretezi az összes szolgáltatást a [0, 1] intervallumra.
    
      A [0, 1] intervallumra való átméretezés az egyes szolgáltatások értékeinek eltolásával történik, így a minimális érték 0, majd az új maximális érték (amely az eredeti maximális és minimális értékek közötti különbség).
      
      Az oszlopban szereplő értékek a következő képlettel alakíthatók át:  
  
      ![normalizálás a min&#45;Max függvény használatával](media/module/aml-normalization-minmax.png "AML_normalization – MinMax")  
  
    - **Logisztika**: az oszlopban szereplő értékek a következő képlettel alakíthatók át:

      ![a logisztikai függvény által normalizált képlet](media/module/aml-normalization-logistic.png "AML_normalization – logisztika")  
  
    - **Lognormális**: Ez a beállítás az összes értéket lognormális-skálára konvertálja.
  
      Az oszlopban szereplő értékek a következő képlettel alakíthatók át:
  
      ![a képlet naplója&#45;normál eloszlás](media/module/aml-normalization-lognormal.png "AML_normalization – lognormális")
    
      Itt a μ és a σ a eloszlás paraméterei, amelyeket a rendszer az adatok alapján, a maximális valószínűségi becslések szerint számít, külön-külön.  
  
    - **TanH**: minden érték egy hiperbolikus tangensre lesz konvertálva.
    
      Az oszlopban szereplő értékek a következő képlettel alakíthatók át:
    
      ![normalizálás a TANH függvény használatával](media/module/aml-normalization-tanh.png "AML_normalization – TANH")

6. Küldje el a folyamatot, vagy kattintson duplán az **Adatnormalizálás** modulra, és válassza a **kijelölt futtatása** lehetőséget. 

## <a name="results"></a>Results (Eredmények)

A **normalizálás** adatmodul két kimenetet hoz létre:

- Az átalakított értékek megtekintéséhez kattintson a jobb gombbal a modulra, majd válassza a **Megjelenítés** lehetőséget.

    Alapértelmezés szerint az értékek a helyük szerint lesznek átalakítva. Ha össze szeretné hasonlítani az átalakított értékeket az eredeti értékekkel, az [Oszlopok hozzáadása](./add-columns.md) modullal egyesítheti az adatkészleteket, és megtekintheti az oszlopokat egymás mellett.

- Az átalakítás mentéséhez, hogy ugyanazt a normalizáló módszert alkalmazza egy másik adatkészletre, válassza ki a modult, és válassza az **adatkészlet regisztrálása** lehetőséget a jobb oldali panel **outputs (kimenet** ) lapján.

    Ezután betöltheti a mentett transzformációkat a bal oldali navigációs ablaktábla **átalakítások** csoportjából, és alkalmazhatja azt egy olyan adatkészletre, amely ugyanazzal a sémával rendelkezik a [transzformáció alkalmazása](apply-transformation.md)lehetőség használatával.  


## <a name="next-steps"></a>Következő lépések

Tekintse [meg a Azure Machine learning elérhető modulok készletét](module-reference.md) . 