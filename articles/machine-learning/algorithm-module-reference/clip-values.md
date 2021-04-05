---
title: Értékek levágása
titleSuffix: Azure Machine Learning
description: Megtudhatja, hogyan használhatja a Azure Machine Learning a Clip Values modult a kiugró elemek és a klipek kiváltásához vagy az értékek cseréjéhez.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 09/09/2019
ms.openlocfilehash: 99fb41542dff28997438881abad71da11e927a78
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "96014890"
---
# <a name="clip-values"></a>Értékek levágása

Ez a cikk a Azure Machine Learning Designer modulját ismerteti.

A Clip Values modul segítségével azonosíthatja és tetszés szerint lecserélheti a megadott küszöbérték alatti vagy feletti adatértékeket egy középérték, egy konstans vagy más helyettesítő érték megadásával.  

A modult egy olyan adatkészlethez kapcsolja össze, amely a klipet tartalmazó számokat tartalmaz, válassza ki a használni kívánt oszlopokat, majd állítsa be a küszöbértéket vagy az értékek tartományát, valamint egy helyettesítő metódust. A modul kimenete vagy csak az eredmények, vagy az eredeti adatkészlethez fűzött módosított értékek is kiállíthatók.

## <a name="how-to-configure-clip-values"></a>A klip értékeinek konfigurálása

Mielőtt elkezdené, azonosítsa a leválasztani kívánt oszlopokat és a használandó módszert. Azt javasoljuk, hogy először tesztelje a kivágási módszert az adat kis részhalmazán.

A modul ugyanazokat a feltételeket és helyettesítő metódusokat alkalmazza a kijelölésbe foglalt **összes** oszlopra. Ezért ne felejtse el kizárni azokat az oszlopokat, amelyeket nem szeretne módosítani.

Ha egyes oszlopokra kivágási módszereket vagy különböző feltételeket kell alkalmaznia, akkor a hasonló oszlopok egyes készletei esetében a **klipek értékének** egy új példányát kell használnia.

1.  Adja hozzá a **klip értékei** modult a folyamathoz, és kapcsolódjon a módosítani kívánt adatkészlethez. Ez a modul az **adatátalakítás** alatt, a **skálázás és a csökkentés** kategóriában található. 
  
1.  Az **oszlopok listájában** válassza ki azokat az oszlopokat, amelyeken a rendszer a **klipek értékeit** alkalmazni kívánja.  
  
1.  A **küszöbértékek készletében** válassza az alábbi lehetőségek egyikét a legördülő listából. Ezek a beállítások határozzák meg, hogy az alsó és felső határok hogyan állíthatók be az elfogadható értékek és a kivágásra kerülő értékek között.  
  
    - **ClipPeaks**: Ha az értékeket csúcsok szerint ábrázolja, csak egy felső határt kell megadni. A határértéknél nagyobb érték lesz lecserélve.
  
    -  **ClipSubpeaks**: Ha az értékeket alcsúcsok alapján ábrázolja, csak egy alsó határt kell megadnia. A rendszer lecseréli azokat az értékeket, amelyek kisebbek, mint a határ értéke.  
  
    - **ClipPeaksAndSubpeaks**: Ha az értékeket csúcsok és alcsúcsok alapján ábrázolja, megadhatja a felső és az alsó határt is. A tartományon kívüli értékeket a rendszer lecseréli. A határ értékeit megegyező értékek nem változnak.
  
1.  Az előző lépésben kiválasztott választástól függően a következő küszöbértékeket állíthatja be: 

    + **Alsó küszöbérték**: csak akkor jelenik meg, ha **ClipSubPeaks** választ
    + **Felső küszöbérték**: csak akkor jelenik meg, ha **ClipPeaks** választ
    + **Küszöbérték**: csak akkor jelenik meg, ha **ClipPeaksAndSubPeaks** választ

    Az egyes küszöbértékek esetében válassza az **állandó** vagy a **percentilis** értéket.

1. Ha az **állandó** lehetőséget választja, adja meg a maximális vagy a minimális értéket a szövegmezőben. Tegyük fel például, hogy ismeri a 999 értéket helyőrző értékként. Kiválaszthatja az **állandó** értéket a felső küszöbértékhez, és a 999-as típust **állandó értékként a felső küszöbértékhez**.
  
1. Ha a **percentilis** lehetőséget választja, akkor az oszlop értékeit egy percentilis tartományra kell korlátozni. 

    Tegyük fel például, hogy csak az 10-80 percentilis tartomány értékeit kívánja megőrizni, és az összes többiet lecseréli. Válassza a **percentilis** lehetőséget, majd írja be a 10-es **értéket az alsó küszöbértéknél**, és írja be a 80 **értéket a felső küszöbértéknél a percentilis értékhez**. 

    A percentilis-tartományok használatának néhány példája a [percentilis](#examples-for-clipping-using-percentiles) című szakaszban található.  
  
1.  Adjon meg egy helyettesítő értéket.

    Azok a számok, amelyek pontosan megfelelnek a megadott határoknak, a megengedett tartományon belül vannak, ezért nem lesznek lecserélve. A megadott tartományon kívül eső számok helyébe a helyettesítő érték kerül. 
  
    + A **csúcsok helyettesítő értéke**: meghatározza az összes olyan oszlop értékének helyettesítésére szolgáló értéket, amely nagyobb a megadott küszöbértéknél.  
    + **Alcsúcsok helyettesítő értéke**: meghatározza az összes olyan oszlop értékének helyettesítésére használandó értéket, amely kisebb a megadott küszöbértéknél.  
    + Ha a **ClipPeaksAndSubpeaks** kapcsolót használja, külön helyettesítő értékeket adhat meg a felső és az alsó levágott értékekhez.  

    A következő helyettesítő értékek támogatottak:  
  
    -   **Küszöbérték**: a kivágott értékeket a megadott küszöbértékkel helyettesíti.  
  
    -   **Középérték**: a kivágott értékeket az oszlop értékeinek középértékével helyettesíti. A középérték kiszámítása az értékek kivágása előtt történik.  
  
    -   **Medián**: a kivágott értékeket az oszlop értékeinek középértékével helyettesíti. A medián kiszámítása az értékek kivágása előtt történik.   
  
    -   **Hiányzik**. Lecseréli a kivágott értékeket a hiányzó (üres) értékre.  
  
1.  **Jelző oszlopok hozzáadása**: akkor válassza ezt a lehetőséget, ha egy új oszlopot szeretne készíteni, amely megadja, hogy a megadott levágási művelet az adott sorban lévő értékekre vonatkozik-e. Ez a beállítás akkor hasznos, ha új nyírási és helyettesítési értékeket próbál meg tesztelni.  
  
1. **Felülírási jelző**: adja meg, hogyan kívánja létrehozni az új értékeket. Alapértelmezés szerint a **klip értékei** egy új oszlopot állítanak össze, amely a kívánt küszöbértékre kivágott csúcsérték-értékeket hoz létre. Az új értékek felülírják az eredeti oszlopot.  
  
    Ha meg szeretné őrizni az eredeti oszlopot, és egy új oszlopot szeretne felvenni a kivágott értékekkel, törölje a jelölőnégyzet jelölését.  
  
1.  A folyamat elküldése.  
  
    Kattintson a jobb gombbal a **klip értékei** modulra, válassza a **Megjelenítés** lehetőséget, vagy válassza ki a modult, és váltson a jobb oldali panel **outputs (kimenetek** ) lapjára, kattintson a **port kimenetében** található hisztogram ikonra, és ellenőrizze az értékeket, és győződjön meg arról, hogy a kivágási művelet megfelelt az elvárásainak.  
 
### <a name="examples-for-clipping-using-percentiles"></a>Példák a kivágásra a percentilis használatával

Annak megismeréséhez, hogy a százalékos értékek hogyan működnek, vegyünk fel egy 10 sorból álló adatkészletet, amelyek mindegyike rendelkezik egy példánnyal a 1-10 értékkel.  
  
- Ha a százalékos arányt a felső küszöbértékként használja, akkor az adatkészletben lévő összes érték 90 százalékának az értéknél kisebbnek kell lennie.  
  
- Ha a százalékos arányt alacsonyabb küszöbértékként használja, a 10. százalékos értéknél az adatkészlet összes értékének 10 százalékának kisebbnek kell lennie ennél az értéknél.  
  
1.  A **küszöbértékek készletében** válassza a **ClipPeaksAndSubPeaks** lehetőséget.  
  
1.  A **felső küszöbértéknél** válassza a **percentilis** lehetőséget, a **százalékos értéknél** pedig a 90 értéket.  
  
1.  A **felső helyettesítő értéknél** válassza a **hiányzó érték** elemet.  
  
1.  Az **alsó küszöbértéknél** válassza a **percentilis** lehetőséget, a **százalékos értéknél** pedig a 10-es értéket.  
  
1.  **Alacsonyabb helyettesítő érték** esetén válassza a **hiányzó érték** elemet.  
  
1.  Törölje a jelölőnégyzet jelölését, és jelölje be a **jelző oszlop hozzáadása** **elemet.**  
  
Most próbálja meg ugyanazt a folyamatot a 60-as értékkel, mint a felső percentilis küszöbértékét, és 30-nál alacsonyabb százalékos küszöbértéket, és használja a küszöbértéket helyettesítő értékként. A következő táblázat összehasonlítja ezt a két eredményt:  
  
1.  Lecserélés hiányzik; Felső küszöbérték = 90; Alsó küszöbérték = 20  
  
1.  Helyettesítse a küszöbértéket; Felső percentilis = 60; Alsó percentilis = 40  
  
|Eredeti adatértékek|Lecserélés hiányzóra|Csere küszöbértékkel|  
|-------------------|--------------------------|----------------------------|  
|1<br /><br /> 2<br /><br /> 3<br /><br /> 4<br /><br /> 5<br /><br /> 6<br /><br /> 7<br /><br /> 8<br /><br /> 9<br /><br /> 10|IGAZ<br /><br /> IGAZ<br /><br /> 3, HAMIS<br /><br /> 4, HAMIS<br /><br /> 5, HAMIS<br /><br /> 6, HAMIS<br /><br /> 7, HAMIS<br /><br /> 8, HAMIS<br /><br /> 9, HAMIS<br /><br /> IGAZ|4, IGAZ<br /><br /> 4, IGAZ<br /><br /> 4, IGAZ<br /><br /> 4, IGAZ<br /><br /> 5, HAMIS<br /><br /> 6, HAMIS<br /><br /> 7, IGAZ<br /><br /> 7, IGAZ<br /><br /> 7, IGAZ<br /><br /> 7, IGAZ| 
 
## <a name="next-steps"></a>Következő lépések

Tekintse [meg a Azure Machine learning elérhető modulok készletét](module-reference.md) . 
