---
title: A pontszám szintű & Deep ajánló modul használata
titleSuffix: Azure Machine Learning
description: Megtudhatja, hogyan használhatja a score Wide & Deep ajánló modulját Azure Machine Learning az adathalmazokra vonatkozó javaslati előrejelzések pontozásához.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 08/12/2020
ms.openlocfilehash: 9a1a3892e6a47aabd9b5129ca551900494616bc8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "90905175"
---
# <a name="score-wide-and-deep-recommender"></a>Wide and Deep ajánló pontozása

Ez a cikk azt ismerteti, hogyan használhatja a Azure Machine Learning Designerben a **pontszám széles és mély ajánló** modulját, hogy a betanított javaslat modell alapján hozzon létre előrejelzéseket a Google széles körű & mély tanulása alapján.

A széles és mély ajánló két különböző előrejelzést készíthet:

- [Egy adott felhasználó és tétel minősítésének előrejelzése](#predict-ratings)

- [Elemek ajánlása egy adott felhasználónak](#recommend-items)


Az utóbbi típusú előrejelzések létrehozásakor akár *üzemi módban* , akár *kiértékelési módban* is működhet.

- Az **éles üzemmód** minden felhasználót vagy elemet figyelembe vesz, és általában egy webszolgáltatásban használatos. Új felhasználók számára is létrehozhat pontszámokat, nem csak a betanítás során látott felhasználókat. 

- A **kiértékelési mód** a felhasználók vagy a kiértékelhető elemek csökkentett készletén működik, és általában a kísérletezés során használatos.

A széles körű és részletes ajánló és a mögöttes elmélet további részletei a megfelelő kutatási dokumentumban olvashatók:  [széles körű & az ajánló rendszerek mélyreható megismerése](https://arxiv.org/pdf/1606.07792.pdf).  

## <a name="how-to-configure-score-wide-and-deep-recommender"></a>A pontszám széles és mély ajánlójának konfigurálása

Ez a modul különböző típusú ajánlásokat támogat, amelyek mindegyike eltérő követelményeket támaszt. Kattintson a kívánt adattípusra mutató hivatkozásra, valamint a létrehozni kívánt javaslat típusára.

+ [Értékelések előrejelzése](#predict-ratings)
+ [Elemek ajánlása](#recommend-items)

### <a name="predict-ratings"></a>Értékelések előrejelzése

A minősítések előrejelzése során a modell kiszámítja, hogy egy adott felhasználó hogyan reagál egy adott elemre a betanítási adatok miatt. Ezért a pontozás bemeneti adatának meg kell adnia egy felhasználót és a díjszabást is.

1. Vegyen fel egy betanított, széles körű & mélyreható javaslatot a kísérletbe, és kapcsolja össze azt a **széles körű és mélyreható javaslati modellel**.  A modellt a [Wide és a Deep ajánló](train-wide-and-deep-recommender.md)használatával kell létrehoznia.

2. **Ajánlói előrejelző típus**: válassza a **minősítési előrejelzés** lehetőséget. Nincs szükség további paraméterekre.

3. Adja hozzá azokat az adatokat, amelyekhez előrejelzéseket kíván készíteni, majd a **pontszámhoz kapcsolódjon az adatkészlethez**.

    A minősítések előrejelzéséhez a bemeneti adatkészletnek tartalmaznia kell a felhasználó-elem párokat.

    Az adatkészlet az első és a második oszlopban lévő felhasználói elem párokra vonatkozóan opcionális harmadik oszlopot is tartalmazhat, de a harmadik oszlop figyelmen kívül lesz hagyva az előrejelzés során.

4.  (Nem kötelező). Ha a felhasználói szolgáltatások adatkészletével rendelkezik, kapcsolja össze a **felhasználói szolgáltatásokkal**.

    A felhasználói szolgáltatások adatkészletében szerepelnie kell a felhasználói azonosítónak az első oszlopban. A fennmaradó oszlopoknak tartalmazniuk kell a felhasználókat jellemző értékeket, például a nemet, a beállításokat, a helyet stb.
  
    A betanítási adatkészletben a minősítéssel rendelkező felhasználók funkcióit a rendszer figyelmen kívül hagyja a **pontszám széles és mély ajánlása** alapján, mivel azokat a képzés során már megtanulták. Ezért az adathalmazt előre szűrheti, hogy csak a *hidegindító felhasználókat*, vagy azokat a felhasználókat tartalmazza, akik nem értékelték az elemeket.

    > [!WARNING]
    > Ha a modell a felhasználói funkciók használata nélkül lett betanítva, nem lehet felhasználói funkciókat bevezetni a pontozás során.

5. Ha rendelkezik az elem funkcióinak adatkészletével, azt összekapcsolhatjuk az **elem szolgáltatásaival**.

    Az elem funkcióinak adatkészletében szerepelnie kell egy elem azonosítójának az első oszlopban. A fennmaradó oszlopoknak tartalmazniuk kell az elemeket jellemző értékeket.

    A betanítási adatkészletben szereplő értékelt elemek funkcióit a rendszer figyelmen kívül hagyja, és a részletes **ajánlót** a képzés során már megtanulta. Ezért korlátozza a pontozási adatkészletet a *hidegindító elemekre*, vagy olyan elemeket, amelyeket semmilyen felhasználó nem minősített.

    > [!WARNING]
    > Ha a modellt az elem funkcióinak használata nélkül tanítják meg, akkor a pontozás során nem lehet bevezetni az elemek funkcióit.

7. Futtassa a kísérletet.

### <a name="results-for-rating-predictions"></a>A minősítési előrejelzések eredményei 

A kimeneti adatkészlet három oszlopot tartalmaz, amelyek tartalmazzák a felhasználót, az elemeket, valamint az egyes bemeneti felhasználók és elemek előre jelzett minősítését.

Emellett az alábbi módosítások is érvénybe lépnek a pontozás során:

-  Numerikus felhasználó vagy elem funkció oszlop esetén a rendszer automatikusan lecseréli a hiányzó értékeket a nem hiányzó betanítási értékek **középértékére** . A kategorikus funkció esetében a hiányzó értékeket a szolgáltatás bármely lehetséges értéke helyett ugyanazzal a kategorikus értékkel cseréli a rendszer.
-  Nem alkalmaz fordítást a szolgáltatás értékeire, hogy megőrizze a adatelégtelenséget.

### <a name="recommend-items"></a>Elemek ajánlása

A felhasználókra vonatkozó elemek ajánlásához adja meg a felhasználók és az elemek listáját bemenetként. Ezekből az adatokból a modell a meglévő elemek és felhasználók ismereteit használja az egyes felhasználókra vonatkozó, valószínűleg fellebbezéssel rendelkező elemek listájának létrehozásához. Testreszabhatja a visszaadott javaslatok számát, és beállíthat egy küszöbértéket a javaslat létrehozásához szükséges korábbi javaslatok számára.

1. Vegyen fel egy betanított, széles körű és részletes javaslatot a kísérlethez, és kapcsolja össze azt a **széles körű és mélyreható javaslati modellel**.  A modellt a [Wide és a Deep ajánló](train-wide-and-deep-recommender.md)használatával kell létrehoznia.

2. Ha a felhasználók egy adott listájához szeretne elemeket ajánlani, állítsa az **Ajánlói előrejelzési típust** **elemre**.

3. **Javasolt elem kiválasztása**: jelezze, hogy éles környezetben vagy modell kiértékeléséhez használja-e a pontozási modult:

    - **A névleges elemek közül (a modell kiértékeléséhez)**: válassza ezt a lehetőséget, ha modellt fejleszt vagy tesztel. Ez a beállítás lehetővé teszi a **kiértékelési módot**, és a modul csak a megadott bemeneti adatkészletben lévő elemekről tesz javaslatokat.
    - **Az összes elemből**: akkor válassza ezt a lehetőséget, ha egy webszolgáltatásban vagy éles környezetben használni kívánt kísérletet állít be.  Ez a beállítás engedélyezi az **éles üzemmódot**, és a modul javaslatokat tesz a betanítás során látható összes elemről.
    - Nem **értékelt elemektől (új elemek a felhasználók számára)**: válassza ezt a lehetőséget, ha azt szeretné, hogy a modul csak a nem értékelt betanítási adatkészletben lévő elemekről tegyen javaslatot. 
4. Adja hozzá azt az adatkészletet, amelyhez előrejelzéseket kíván készíteni, majd a pontszámhoz kapcsolódjon az **adatkészlethez**.

    - Ha a lehetőséget választja, az **összes elemnél** a bemeneti adatkészletnek egy és csak egy oszlopból kell állnia, amely tartalmazza azon felhasználók azonosítóit, akik számára javaslatokat szeretne tenni.

        Az adatkészlet tartalmazhatja az elemek azonosítóinak és minősítésének két további oszlopát is, de ez a két oszlop figyelmen kívül lesz hagyva. 

    - Ha a lehetőséget választja, a **névleges elemek közül (a modell kiértékeléséhez)** a bemeneti adatkészletnek tartalmaznia kell a **felhasználó-elem párokat**. Az első oszlopnak tartalmaznia kell a **felhasználói** azonosítót. A második oszlopnak tartalmaznia kell a megfelelő **elemek** azonosítóit.

        Az adatkészlet tartalmazhat a felhasználói elemek minősítésének harmadik oszlopát is, de ez az oszlop figyelmen kívül lesz hagyva.
        
    - A nem **értékelt elemek esetében (az új elemek felhasználók számára történő javaslatához)** a bemeneti adatkészletnek felhasználói elemből álló párokból kell állnia. Az első oszlopnak tartalmaznia kell a felhasználói azonosítót. A második oszlopnak tartalmaznia kell a megfelelő elemek azonosítóit.

        Az adatkészlet tartalmazhat a felhasználói elemek minősítésének harmadik oszlopát is, de ez az oszlop figyelmen kívül lesz hagyva.

5. (Nem kötelező). Ha a **felhasználói szolgáltatások** adatkészletével rendelkezik, kapcsolja össze a **felhasználói szolgáltatásokkal**.

    A felhasználói szolgáltatások adatkészletének első oszlopában szerepelnie kell a felhasználói azonosítónak. A fennmaradó oszlopoknak tartalmazniuk kell a felhasználót jellemző értékeket, például a nemet, a beállításokat, a helyet stb.

    A minősítéssel rendelkező felhasználók funkcióit figyelmen kívül hagyják a **pontszám széles és mély ajánlója**, mivel ezek a funkciók már megtanulták a képzés során. Ezért előre szűrheti az adatkészletet, hogy csak a *megfázást használó felhasználókat*, illetve azokat a felhasználókat tartalmazza, akik nem értékelték az elemeket.

    > [!WARNING]
    >  Ha a modell a felhasználói funkciók használata nélkül lett betanítva, nem használhatja a funkciók alkalmazása funkciót a pontozás során.

6. Választható Ha rendelkezik az **elem funkcióinak** adatkészletével, azt összekapcsolhatjuk az **elem szolgáltatásaival**.

    Az elem funkciói adatkészlet első oszlopának tartalmaznia kell az elem azonosítóját. A fennmaradó oszlopoknak tartalmazniuk kell az elemeket jellemző értékeket.

    A minősítéssel rendelkező elemek jellemzőit a **pontszám széles és mély ajánlója** figyelmen kívül hagyja, mivel ezek a funkciók már megtanulták a képzés során. Ezért a pontozási adatkészletet letilthatja a *hidegindító elemekre* vagy olyan elemekre, amelyeket egyetlen felhasználó sem értékelt ki.

    > [!WARNING]
    >  Ha a modellt cikk-funkciók használata nélkül tanítják meg, ne használja az elem funkcióit pontozáskor.  

7. A **felhasználó számára ajánlott elemek maximális száma**: írja be az egyes felhasználók számára visszaadni kívánt elemek számát. Alapértelmezés szerint 5 elem ajánlott.

8. **Az ajánlási készlet minimális mérete felhasználónként**: adjon meg egy értéket, amely azt jelzi, hogy hány előzetes javaslat szükséges.  Alapértelmezés szerint ez a paraméter 2 értékre van állítva, ami azt jelenti, hogy az elemnek legalább két másik felhasználó által ajánlottnak kell lennie.

    Ezt a beállítást csak akkor kell használni, ha próbaverziós módban van. A beállítás nem érhető el, ha az **összes elemből** vagy **a nem értékelt elemek közül a lehetőséget választja (új elemeket javasol a felhasználóknak)**.

9. A nem **értékelt elemek esetében (az új elemek felhasználók számára történő javaslatához)** használja a **betanítási adatok** nevű harmadik bemeneti portot, hogy eltávolítsa azokat az elemeket, amelyek már a jóslat eredményeiből lettek kiértékelve.

    A szűrő alkalmazásához az eredeti betanítási adatkészletet a bemeneti porthoz kell kapcsolni.

10. Futtassa a kísérletet.
### <a name="results-of-item-recommendation"></a>Az elemek javaslatának eredményei

A **pontszám széles és részletes ajánlója** által visszaadott pontozásos adatkészlet felsorolja az egyes felhasználók ajánlott elemeit.

- Az első oszlop tartalmazza a felhasználói azonosítókat.
- Számos további oszlop jön létre, attól függően, hogy milyen értékre van beállítva a **felhasználó számára ajánlott elemek maximális száma**. Minden oszlop egy javasolt (azonosítóval rendelkező) tételt tartalmaz. Az ajánlásokat a felhasználó-elem affinitása alapján rendezi a rendszer, a legmagasabb affinitású elemmel pedig az **1. tételt**.

##  <a name="technical-notes"></a>Technikai megjegyzések

Ez & a szakasz az előrejelzések létrehozásával kapcsolatos gyakori kérdésekre adott válaszokat tartalmazza.  

###  <a name="cold-start-users-and-recommendations"></a>Megfázás-indítási felhasználók és javaslatok

A javaslatok létrehozásához általában a **pontszám széles és mély ajánló** moduljának ugyanazokat a bemeneteket kell használnia, amelyeket a modell betanításakor használt, beleértve a felhasználói azonosítót is. Ennek az az oka, hogy az algoritmusnak tudnia kell, hogy megismerte-e a felhasználóra vonatkozó valamit a betanítás során. 

Az új felhasználók számára azonban előfordulhat, hogy nem rendelkezik felhasználói AZONOSÍTÓval, csak bizonyos felhasználói funkciók, például a kor, a nem, és így tovább.

Továbbra is létrehozhat olyan felhasználókat, akik újak a rendszer számára, mivel ezeket a felhasználókat felhasználva használják a *felhasználók.* Ilyen felhasználók esetében az ajánlási algoritmus nem használ korábbi előzményeket vagy korábbi minősítéseket, csak a felhasználói funkciókat.

Az előrejelzési célból a hidegindító felhasználó olyan AZONOSÍTÓval rendelkező felhasználóként van meghatározva, amely még nem lett felhasználva a betanításhoz. Annak biztosítása érdekében, hogy az azonosítók ne egyezzenek meg a betanításban használt azonosítókkal, új azonosítókat hozhat létre. Előfordulhat például, hogy egy meghatározott tartományon belül véletlenszerű azonosítókat hoz, vagy a megfázás-indítási felhasználók számára előre több azonosítót oszt ki.

Ha azonban nem rendelkezik együttműködési szűrési adattal (például a felhasználói funkciók vektorával), akkor jobb, ha besorolást vagy regressziós tanulót használ.

###  <a name="production-use-of-the-wide-and-deep-recommender"></a>A széles és mély ajánló üzemi használata

Ha a széles és mély ajánlóval kísérletezett, majd a modellt éles környezetbe helyezi, vegye figyelembe a legfontosabb különbségeket, amikor az ajánlót kiértékelési módban és éles módban használja:

- A kiértékeléshez a definíció szerint olyan előrejelzések szükségesek, amelyeket a rendszer egy tesztelési készletben ellenőrizheti a *terepi igazságban* . Ezért az ajánló kiértékelése során csak azokat az elemeket kell megjósolni, amelyek a tesztelési készletben lettek értékelve. Ez szükségszerűen korlátozza az előre jelzett lehetséges értékeket.

    A modell működővé tenni azonban általában úgy módosítja az előrejelzési módot, hogy az összes lehetséges elem alapján javaslatokat tegyen, hogy a lehető legjobb előrejelzések legyenek. Az előrejelzések többsége nem rendelkezik a megfelelő indokokkal, így az ajánlás pontossága nem ellenőrizhető ugyanúgy, mint a kísérletezés során.

- Ha nem ad meg egy felhasználói azonosítót az éles környezetben, és csak a szolgáltatás-vektort adja meg, akkor az összes lehetséges felhasználóra vonatkozó javaslatok listáját választhatja. Ügyeljen arra, hogy adjon meg egy felhasználói azonosítót.

    A visszaadott javaslatok számának korlátozásához megadhatja a felhasználónként visszaadott elemek maximális számát is. 



## <a name="next-steps"></a>Következő lépések

Tekintse [meg Azure Machine learning elérhető modulok készletét](module-reference.md) . 
