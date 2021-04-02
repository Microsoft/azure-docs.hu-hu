---
title: Vowpal Wabbit modell pontozása
titleSuffix: Azure Machine Learning
description: Ebből a cikkből megtudhatja, hogyan hozhat létre pontszámokat egy meglévő betanított Vowpal Wabbit-modell használatával a score Vowpal Wabbit Model modul használatával.
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 07/02/2020
ms.openlocfilehash: 23dfee7b78f2606c54525391e1260af69a9b0779
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "90898409"
---
# <a name="score-vowpal-wabbit-model"></a>Vowpal Wabbit modell pontozása
Ez a cikk azt ismerteti, hogyan használható a **score Vowpal Wabbit Model** modul a Azure Machine learning Designerben, és hogyan hozhatók létre a bemeneti adatok egy készlete egy meglévő betanított Vowpal Wabbit-modell használatával.  

Ez a modul a Vowpal Wabbit Framework legújabb verzióját, a 8.8.1 verzióját tartalmazza. Ezzel a modullal egy, a VW 8-as verzióban mentett, betanított modell használatával szerzi be az adatpontszámokat.  

## <a name="how-to-configure-score-vowpal-wabbit-model"></a>A score Vowpal Wabbit-modell konfigurálása

1.  Adja hozzá a **pontszám Vowpal Wabbit Model** modult a kísérlethez.  
  
2.  Vegyen fel egy betanított Vowpal Wabbit modellt, és kapcsolódjon a bal oldali bemeneti porthoz. Egy kísérletben létrehozott betanított modellt is használhat, vagy megkereshet egy mentett modellt a tervező bal oldali navigációs paneljének **adatkészletek** kategóriájában. Azonban a modellnek elérhetőnek kell lennie Azure Machine Learning Designerben.  
  
    > [!NOTE]
    > Csak a Vowpal Wabbit 8.8.1-modellek támogatottak; más algoritmusok használatával nem csatlakoztathatók a mentett modellek.
  
3.  Adja hozzá a tesztelési adatkészletet, és kapcsolódjon a jobb oldali bemeneti porthoz. Ha a teszt adatkészlet egy olyan könyvtár, amely tartalmazza a teszt adatfájlját, adja meg a test adatfájljának **nevét a** teszt adatfájljának nevével. Ha a teszt adatkészlet egyetlen fájl, hagyja üresen **a teszt adatfájl nevét** .

4. A **VW-argumentumok** szövegmezőbe írja be az érvényes parancssori argumentumok készletét a Vowpal Wabbit végrehajtható fájlba.  

    További információ arról, hogy mely Vowpal Wabbit argumentumok támogatottak és nem támogatottak a Azure Machine Learningban: [technikai megjegyzések](#technical-notes) szakasz.  

5.  **A test adatfájljának neve**: írja be a bemeneti adatokat tartalmazó fájl nevét. Ezt az argumentumot csak akkor használja a rendszer, ha a teszt adatkészlet egy könyvtár.

6. **Adja meg a fájltípust**: adja meg a betanítási adatai által használt formátumot. A Vowpal Wabbit a következő két bemeneti fájlformátumot támogatja:  

   - A **VW** a Vowpal Wabbit által használt belső formátumot jelöli. A részletekért tekintse meg a [Vowpal Wabbit wiki oldalát](https://github.com/JohnLangford/vowpal_wabbit/wiki/Input-format) . 
   - A **SVMLight** egy másik gépi tanulási eszköz által használt formátum. 

7. Jelölje be a jelölőnégyzetet, és adja meg a **címkéket tartalmazó további oszlopot**, ha a pontszámokkal együtt szeretné kinyomtatni a címkéket.  

   A szöveges adatmennyiségek kezelésekor a Vowpal Wabbit nem igényel címkéket, és csak az egyes adatsorok pontszámait fogja visszaadni.  

8. Válassza a lehetőséget, és adjon meg **egy további, nyers pontszámokat tartalmazó oszlopot**, ha a nyers pontszámokat az eredményekkel együtt szeretné kiállítani.  

9. A folyamat elküldése.

## <a name="results"></a>Results (Eredmények)

A betanítás befejezése után:

+ Az eredmények megjelenítéséhez kattintson a jobb gombbal a [pontszám Vowpal Wabbit Model](score-vowpal-wabbit-model.md) modul kimenetére. A kimenet egy 0 és 1 közötti normalizált előrejelzési pontszámot jelez. 

+ Az eredmények kiértékeléséhez a kimeneti adatkészletnek tartalmaznia kell egy adott pontszám-oszlop nevét, amely megfelel a modell modul követelményeinek.

  + A regressziós feladathoz a kiértékelni kívánt adatkészletnek egy nevű oszlopból kell állnia, amely a pontozott `Regression Scored Labels` címkéket jelképezi.
  + A bináris besorolási feladathoz a kiértékelni kívánt adatkészlet két oszlopból áll, amelyek neve a következő: `Binary Class Scored Labels` `Binary Class Scored Probabilities` .
  + A többtényezős besorolás feladathoz a kiértékelni kívánt adatkészletnek egy nevű oszlopból kell állnia, amely a pontozott `Multi Class Scored Labels` címkéket jelképezi.

  Vegye figyelembe, hogy a pontszám Vowpal Wabbit modell moduljának eredményeit nem lehet közvetlenül kiértékelni. Az értékelés előtt az adatkészletet a fenti követelmények szerint módosítani kell.

##  <a name="technical-notes"></a>Technikai megjegyzések

Ez a szakasz megvalósítási részleteket, tippeket és válaszokat tartalmaz a gyakori kérdésekre.

### <a name="parameters"></a>Paraméterek

A Vowpal Wabbit számos parancssori kapcsolóval rendelkezik az algoritmusok kiválasztásához és hangolásához. Ezen beállítások teljes körű megvitatását itt nem lehet megtenni; Javasoljuk, hogy tekintse meg a [Vowpal Wabbit wiki oldalát](https://github.com/JohnLangford/vowpal_wabbit/wiki/Command-line-arguments).  

A következő paraméterek nem támogatottak a Azure Machine Learning Studioban (klasszikus).  

-   A ben megadott bemeneti/kimeneti beállítások [https://github.com/JohnLangford/vowpal_wabbit/wiki/Command-line-arguments](https://github.com/JohnLangford/vowpal_wabbit/wiki/Command-line-arguments)  
  
     A modul már automatikusan konfigurálja ezeket a tulajdonságokat.  
  
-   Továbbá minden olyan lehetőség, amely több kimenetet állít elő, vagy több bemenetet is igénybe vesz, nem engedélyezett. Ezek közé tartozik a, a *`--cbt`* *`--lda`* és a *`--wap`* .  
  
-   Csak a felügyelt tanulási algoritmusok támogatottak. Ez a következő beállításokat nem engedélyezi *`–active`* : `--rank` , *`--search`* stb.  

A fentiekben ismertetett összes argumentum engedélyezett.

## <a name="next-steps"></a>Következő lépések

Tekintse [meg a Azure Machine learning elérhető modulok készletét](module-reference.md) . 