---
title: 'Betanítási modell: modul-hivatkozás'
titleSuffix: Azure Machine Learning
description: Megtudhatja, hogyan használhatja a Azure Machine Learning **Train Model** modult egy besorolási vagy regressziós modell betanításához.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 03/10/2021
ms.openlocfilehash: 41b327e793c6b35a2b2aeae825c493a484d84fb4
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2021
ms.locfileid: "105562766"
---
# <a name="train-model-module"></a>Betanítási modell modul

Ez a cikk a Azure Machine Learning Designer egyik modulját ismerteti.

Ezzel a modullal betaníthatja a besorolási vagy regressziós modellt. A képzés a modell meghatározása és a hozzá tartozó paraméterek beállítása után zajlik, és címkézett adatokra van szükség. A **betanítási modell** használatával egy meglévő modell újratanítását is elvégezheti új adattal. 

## <a name="how-the-training-process-works"></a>A betanítási folyamat működése

Azure Machine Learning a Machine learning-modellek létrehozása és használata általában egy három lépésből álló folyamat. 

1. A modell konfigurálásához válasszon egy adott típusú algoritmust, és határozza meg a paramétereit vagy hiperparaméterek beállítása. Válassza ki a következő típusok bármelyikét: 

    + A **besorolási** modellek neurális hálózatok, Döntési fák és döntési erdők, valamint más algoritmusok alapján.
    + **Regressziós** modellek, amelyek standard lineáris regressziót tartalmazhatnak, vagy amelyek más algoritmusokat használnak, beleértve a neurális hálózatokat és a Bayes-regressziót is.  

2. Adja meg a címkével ellátott adatkészletet, és az algoritmussal kompatibilis adatokat tartalmaz. Kapcsolja össze az adattípust és a modellt a **modell betanításához**.

    A létrehozott képzések egy adott bináris formátum, a iLearner, amely az adatokból beszerzett statisztikai mintákat foglalja magában. Ezt a formátumot nem lehet közvetlenül módosítani vagy olvasni; más modulok azonban használhatják ezt a betanított modellt. 
    
    Megtekintheti a modell tulajdonságait is. További információ: Results (eredmények) szakasz.

3. A betanítás befejezése után használja a betanított modellt az egyik [pontozási modullal](./score-model.md), hogy előrejelzéseket készítsen az új adatmennyiségekről.

## <a name="how-to-use-train-model"></a>A Train Model használata 
    
1. Adja hozzá a **Train Model** modult a folyamathoz.  Ez a modul a **Machine learning** kategóriában található. Bontsa ki a **vonat** elemet, majd húzza a **Train Model** modult a folyamatba.
  
1.  A bal oldali bemeneten csatolja a képzetlen üzemmódot. Csatolja a betanítási adatkészletet a **betanítási modell** jobb oldali bemenetéhez.

    A betanítási adatkészlet címkét tartalmazó oszlopot kell tartalmaznia. A címkék nélküli sorok figyelmen kívül lesznek hagyva.
  
1.  A **label (címke) oszlopban** kattintson az **oszlop szerkesztése** elemre a modul jobb oldali paneljén, és válasszon ki egy olyan oszlopot, amely tartalmazza a modell által a képzéshez használható eredményeket.
  
    - Besorolási problémák esetén a Label oszlopnak **kategorikus** értékeket vagy **különálló** értékeket kell tartalmaznia. Néhány példa lehet igen/nem minősítés, a betegség besorolási kódja vagy neve, vagy egy bevételi csoport.  Ha nem kategorikus oszlopot választ, a modul a betanítás során hibát ad vissza.
  
    -   A regressziós problémák esetén a Label oszlopnak a válasz változót jelölő **numerikus értékeket** kell tartalmaznia. Ideális esetben a numerikus adat folyamatos méretezést jelent. 
    
    Ilyen lehet például a kreditek kockázati pontszáma, a merevlemez meghibásodásának tervezett ideje, vagy egy adott napon vagy időpontban a Call Center felé irányuló hívások előre jelzett száma.  Ha nem választ egy numerikus oszlopot, hibaüzenetet kaphat.
  
    -   Ha nem határozza meg, hogy melyik címke oszlopot szeretné használni, Azure Machine Learning megpróbálja kikövetkeztetni, hogy melyik a megfelelő címke oszlop az adatkészlet metaadatainak használatával. Ha nem a megfelelő oszlopot választja, a kiválasztó oszlop segítségével javítsa ki.
  
    > [!TIP] 
    > Ha problémája van az oszlop Választójának használatával, tekintse meg a tippek az [Oszlopok kiválasztása az adatkészletben](./select-columns-in-dataset.md) című cikket. Ismertet néhány gyakori forgatókönyvet és tippet a **with Rules** és a **Name** beállítások használatával.
  
1.  A folyamat elküldése. Ha sok adattal rendelkezik, eltarthat egy darabig.

    > [!IMPORTANT] 
    > Ha van olyan azonosító oszlopa, amely az egyes sorok azonosítója, vagy egy olyan szöveges oszlop, amely túl sok egyedi értéket tartalmaz, a **betanítási modell** hibát eredményezhet, például: "a (z)" {column_name} "oszlop egyedi értékeinek száma nagyobb, mint az engedélyezett.
    >
    > Ennek az az oka, hogy az oszlop megnyomja az egyedi értékek küszöbértékét, és a memóriából is okozhat. A [metaadatok szerkesztése](edit-metadata.md) lehetőséggel megadhatja, hogy az oszlop **egyértelmű szolgáltatásként** legyen felhasználva, és nem használható a betanításban, vagy az [N-Gram funkciók kinyerése a szöveges modulból](extract-n-gram-features-from-text.md) a Text (szöveg) oszlop előfeldolgozásához További részletekért tekintse meg a [Designer hibakódot](././designer-error-codes.md) .

## <a name="model-interpretability"></a>Modell értelmezése

A modell értelmezése lehetővé teszi a ML modell megértését, valamint a döntéshozatal alapjául szolgáló döntéseket olyan módon, amely az emberek számára érthető.

A **betanítási** modul jelenleg támogatja [az adatelemzési csomagok használatát az ml-modellek elmagyarázása érdekében](../how-to-machine-learning-interpretability-aml.md#generate-feature-importance-values-via-remote-runs). A következő beépített algoritmusok támogatottak:

- Lineáris regresszió
- Neurális hálózat típusú regresszió
- Kétosztályos logisztikai regresszió
- Kétosztályos támogató vektorgép
- Többosztályos döntési erdő

A modell magyarázatának létrehozásához válassza az **igaz** lehetőséget a **modell magyarázatának** legördülő listában a betanítási modell modulban. Alapértelmezés szerint a **Train Model** modulban hamis értékre van állítva. Vegye figyelembe, hogy a létrehozás magyarázata további számítási költségeket igényel.

![Képernyőkép a modell magyarázatát jelző jelölőnégyzetről](./media/module/train-model-explanation-checkbox.png)

A folyamat futásának befejeződése után a **betanítási modell** moduljának jobb oldali ablaktábláján meglátogathatja a **magyarázatok** lapot, és megismerheti a modell teljesítményét, az adatkészletet és a funkció fontosságát.

![Képernyőkép a modell magyarázatait bemutató diagramokról](./media/module/train-model-explanations-tab.gif)

Ha többet szeretne megtudni a modell magyarázatának használatáról Azure Machine Learningban, tekintse meg a következő témakört: a [ml modellek értelmezése](../how-to-machine-learning-interpretability-aml.md#generate-feature-importance-values-via-remote-runs).

## <a name="results"></a>Results (Eredmények)

A modell képzése után:


+ Ha más folyamatokban szeretné használni a modellt, válassza ki a modult, és válassza az **adatkészlet regisztrálása** ikont az **outputs (kimenetek** ) lapon a jobb oldali panelen. A mentett modelleket a modul palettáján, az **adatkészletek** területen érheti el.

+ Ha a modellt az új értékek előrejelzéséhez szeretné használni, akkor a [pontszám modell](./score-model.md) modulhoz kell csatlakoznia, az új bemeneti adatokkal együtt.


## <a name="next-steps"></a>Következő lépések

Tekintse [meg a Azure Machine learning elérhető modulok készletét](module-reference.md) .