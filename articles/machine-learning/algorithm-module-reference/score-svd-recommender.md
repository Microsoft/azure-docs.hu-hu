---
title: 'Pontszám SVD ajánló: modul referenciája'
titleSuffix: Azure Machine Learning
description: Megtudhatja, hogyan használhatja a score SVD ajánló modulját Azure Machine Learning az adathalmazokra vonatkozó ajánlások előrejelzéséhez.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 08/10/2020
ms.openlocfilehash: bf62fa995724b8e1fff757e89945cc39db3d9842
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "90893713"
---
# <a name="score-svd-recommender"></a>SVD-ajánló pontozása

Ez a cikk azt ismerteti, hogyan használható a score SVD ajánló modul a Azure Machine Learning Designerben. Ezzel a modullal előrejelzéseket hozhat létre egy betanított javaslati modell használatával az egyértékű dekompozíciós (SVD) algoritmus alapján.

A SVD ajánló két különböző típusú előrejelzést tud előállítani:

- [Egy adott felhasználó és tétel minősítésének előrejelzése](#prediction-of-ratings)
- [Elemek ajánlása egy felhasználónak](#recommendations-for-users)

A második típusú előrejelzések létrehozásakor az alábbi módokon is működhet:

- Az **éles üzemmód** az összes felhasználót vagy elemet veszi figyelembe. Ez általában egy webszolgáltatásban használatos.

  Új felhasználók számára is létrehozhat pontszámokat, nem csak a betanítás során látott felhasználókat. További információ: [technikai megjegyzések](#technical-notes). 

- A **kiértékelési mód** a felhasználók vagy a kiértékelhető elemek csökkentett készletén működik. Általában a folyamat műveletei során használatos.

A SVD ajánló algoritmussal kapcsolatos további információkért tekintse meg a Research Paper [Matrix faktorizációs-technikákat az ajánló rendszerek esetében](https://datajobs.com/data-science-repo/Recommender-Systems-[Netflix].pdf).

## <a name="how-to-configure-score-svd-recommender"></a>A score SVD ajánló konfigurálása

Ez a modul két különböző típusú előrejelzést támogat, amelyek mindegyike eltérő követelményeket támaszt. 

###  <a name="prediction-of-ratings"></a>Minősítések előrejelzése

A minősítések előrejelzése során a modell kiszámítja, hogy a felhasználó hogyan reagál egy adott elemre a betanítási adatok alapján. A pontozás bemeneti adatának meg kell adnia egy felhasználót és a díjszabást is.

1. Adjon hozzá egy betanított javaslatot a folyamathoz, és kapcsolódjon a **betanított SVD-ajánlóhoz**. A modellt a [Train SVD ajánló](train-SVD-recommender.md) moduljának használatával kell létrehoznia.

2. Az **Ajánlói előrejelző típushoz** válassza a **minősítési előrejelzés** lehetőséget. Nincs szükség más paraméterekre.

3. Adja hozzá azokat az adatokat, amelyekhez előrejelzéseket kíván készíteni, majd a **pontszámhoz kapcsolódjon az adatkészlethez**.

   Ahhoz, hogy a modell megjósolja a minősítéseket, a bemeneti adatkészletnek tartalmaznia kell a felhasználó-elem párokat.

   Az adatkészlet az első és a második oszlopban szereplő felhasználói elem párokra vonatkozóan opcionális harmadik oszlopot is tartalmazhat. A harmadik oszlop azonban figyelmen kívül lesz hagyva az előrejelzés során.

4. A folyamat elküldése.

### <a name="results-for-rating-predictions"></a>A minősítési előrejelzések eredményei 

A kimeneti adatkészlet három oszlopot tartalmaz: felhasználók, elemek, valamint az egyes bemeneti felhasználók és elemek előre jelzett minősítése.

###  <a name="recommendations-for-users"></a>Javaslatok felhasználók számára 

A felhasználókra vonatkozó elemek ajánlásához adja meg a felhasználók és az elemek listáját bemenetként. Ezekből az adatokból a modell a meglévő elemek és felhasználók ismereteit használja az egyes felhasználókra vonatkozó, valószínűleg fellebbezéssel rendelkező elemek listájának létrehozásához. Testreszabhatja a visszaadott javaslatok számát. Továbbá beállíthat egy küszöbértéket a javaslatok létrehozásához szükséges korábbi ajánlások számának megadásához.

1. Adjon hozzá egy betanított javaslatot a folyamathoz, és kapcsolódjon a **betanított SVD-ajánlóhoz**.  A modellt a [Train SVD ajánló](train-svd-recommender.md) moduljának használatával kell létrehoznia.

2. A felhasználók listájának megjelenítéséhez állítsa az **Ajánlói előrejelzési típust** **elemre**.

3. Az **ajánlott elemek kiválasztásához** jelezze, hogy éles környezetben vagy modell kiértékeléséhez használja-e a pontozási modult. Válasszon egyet az alábbi értékek közül:

    - **Az összes elemből**: válassza ezt a lehetőséget, ha egy webszolgáltatásban vagy éles környezetben használni kívánt folyamatot állít be.  Ez a beállítás engedélyezi az *éles üzemmódot*. A modul javaslatokat tesz a betanítás során látható összes elemről.

    - **A névleges elemek közül (a modell kiértékeléséhez)**: válassza ezt a lehetőséget, ha modellt fejleszt vagy tesztel. Ez a beállítás lehetővé teszi a *kiértékelési módot*. A modul csak a megadott bemeneti adatkészletben lévő elemekről tesz javaslatokat.
    
    - Nem **értékelt elemektől (új elemek a felhasználók számára)**: válassza ezt a lehetőséget, ha azt szeretné, hogy a modul csak a nem értékelt betanítási adatkészletben lévő elemekről tegyen javaslatot. 

4. Adja hozzá azt az adatkészletet, amelyhez előrejelzéseket kíván készíteni, majd a pontszámhoz kapcsolódjon az **adatkészlethez**.

    - **Minden elemnél** a bemeneti adatkészletnek egy oszlopból kell állnia. Azon felhasználók azonosítóit tartalmazza, amelyekhez javaslatokat szeretne tenni.

      Az adatkészlet tartalmazhatja az elemek azonosítóinak és minősítésének két további oszlopát is, de ez a két oszlop figyelmen kívül lesz hagyva. 

    - A **névleges elemektől (a modell kiértékeléséhez)** a bemeneti adatkészletnek a felhasználó-elem párokból kell állnia. Az első oszlopnak tartalmaznia kell a felhasználói azonosítót. A második oszlopnak tartalmaznia kell a megfelelő elemek azonosítóit.

      Az adatkészlet tartalmazhat a felhasználói elemek minősítésének harmadik oszlopát is, de ez az oszlop figyelmen kívül lesz hagyva.

    - A nem **értékelt elemek esetében (az új elemek felhasználók számára történő javaslatához)** a bemeneti adatkészletnek felhasználói elemből álló párokból kell állnia. Az első oszlopnak tartalmaznia kell a felhasználói azonosítót. A második oszlopnak tartalmaznia kell a megfelelő elemek azonosítóit.

     Az adatkészlet tartalmazhat a felhasználói elemek minősítésének harmadik oszlopát is, de ez az oszlop figyelmen kívül lesz hagyva.

5. A **felhasználó számára ajánlott elemek maximális száma**: Itt adhatja meg az egyes felhasználók számára visszaadni kívánt elemek számát. Alapértelmezés szerint a modul öt elemet javasol.

6. **A javaslati készlet minimális mérete felhasználónként**: adjon meg egy értéket, amely azt jelzi, hogy hány előzetes javaslat szükséges. Alapértelmezés szerint ez a paraméter 2 értékre van állítva, ami azt jelenti, hogy legalább két másik felhasználó javasolta az elem megadását.

   Ezt a lehetőséget csak akkor használja, ha próbaverziós módban dolgozik. A beállítás nem érhető el, ha az **összes elemből** vagy **a nem értékelt elemek közül a lehetőséget választja (új elemeket javasol a felhasználóknak)**.

7.  A nem **értékelt elemek esetében (az új elemek felhasználók számára történő javaslatához)** használja a **betanítási adatok** nevű harmadik bemeneti portot, hogy eltávolítsa azokat az elemeket, amelyek már a jóslat eredményeiből lettek kiértékelve.

    A szűrő alkalmazásához az eredeti betanítási adatkészletet a bemeneti porthoz kell kapcsolni.

8. A folyamat elküldése.

### <a name="results-of-item-recommendation"></a>Az elemek javaslatának eredményei

A score SVD ajánló által visszaadott pontozásos adatkészlet felsorolja az egyes felhasználók ajánlott elemeit:

- Az első oszlop tartalmazza a felhasználói azonosítókat.
- Számos további oszlop jön létre, attól függően, hogy milyen értékre van beállítva a **felhasználó számára ajánlott elemek maximális száma**. Minden oszlop egy javasolt (azonosítóval rendelkező) tételt tartalmaz. A javaslatok a felhasználó-tétel affinitás szerint vannak rendezve. A legmagasabb affinitású tétel az **1**. oszlopba kerül.


##  <a name="technical-notes"></a>Technikai megjegyzések

Ha rendelkezik egy, a SVD ajánlóval rendelkező folyamattal, és a modellt éles környezetbe helyezi át, vegye figyelembe, hogy fontos különbségek vannak az ajánló kiértékelési módban való használata és éles módban való használata között.

A kiértékeléshez a definíció szerint olyan előrejelzések szükségesek, amelyeket a rendszer egy tesztelési készletben ellenőrizheti a *terepi igazságban* . Az ajánló kiértékelése során csak azokat az elemeket kell megjósolni, amelyek a tesztelési készletben lettek értékelve. Ez korlátozza az előre jelzett lehetséges értékeket.

A modell működővé tenni az előrejelzési módot általában úgy módosítja, hogy az összes lehetséges elem alapján javaslatokat tegyen, hogy a lehető legjobb előrejelzések legyenek. Ezen előrejelzések nagy része nem rendelkezik megfelelő indokokkal. Így az ajánlás pontossága nem ellenőrizhető ugyanúgy, mint a folyamat műveletei során.


## <a name="next-steps"></a>Következő lépések

Tekintse [meg a Azure Machine learning elérhető modulok készletét](module-reference.md) . 
