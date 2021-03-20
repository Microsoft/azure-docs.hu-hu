---
title: Modell-hiperparaméterek hangolása
titleSuffix: Azure Machine Learning
description: Használja a tervező modell Hiperparaméterek beállítása modulját a Designerben a Hyper-Parameters hangolásához szükséges paraméterek elvégzéséhez.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/10/2020
ms.openlocfilehash: 2bbf75ba5de4ad20e11261bdcfd1204b1a0b0766
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "93420631"
---
# <a name="tune-model-hyperparameters"></a>Modell-hiperparaméterek hangolása

Ez a cikk azt ismerteti, hogyan használható a Hiperparaméterek beállítása-modul Tune Model Azure Machine Learning Designerben. A cél a gépi tanulási modell optimális hiperparaméterek beállítása meghatározása. A modul több modellt is felépít és tesztel a különböző beállítások kombinációjának használatával. Összehasonlítja az összes modell mérőszámait a beállítások kombinációjának lekéréséhez. 

A feltételek *paraméter* és a *hiperparaméter* zavaró lehet. A modell *paramétereit* a modul jobb oldali ablaktábláján állíthatja be. Alapvetően ez a modul egy *paramétert* hajt végre a megadott paraméterek beállításain. Megtanulja a _hiperparaméterek beállítása_ optimális készletét, amely minden egyes konkrét döntési fa, adatkészlet vagy regressziós módszer esetében eltérő lehet. Az optimális konfiguráció megtalálásának folyamatát más néven *hangolásnak* nevezzük. 

A modul a következő módszert támogatja a modell optimális beállításainak megkereséséhez: *integrált vonat és Finomhangolás.* Ebben a metódusban a használandó paraméterek halmazát konfigurálja. Ezután a modul több kombináción is megismételhető. A modul megméri a pontosságot, amíg meg nem találja a "legjobb" modellt. A legtöbb tanulói modul esetében kiválaszthatja, hogy mely paramétereket kell módosítani a betanítási folyamat során, és melyeket továbbra is rögzíteni kell.

Attól függően, hogy mennyi ideig szeretné futtatni a hangolási folyamatot, dönthet úgy, hogy kimerítően teszteli az összes kombinációt. Vagy lerövidítheti a folyamatot egy paraméter-kombinációk rácsának létrehozásával és a paraméter rácsának véletlenszerű részhalmazának tesztelésével.

Ez a metódus egy betanított modellt hoz létre, amelyet újból felhasználhat.  

> [!TIP] 
> Egy kapcsolódó feladatot is végrehajthat. A hangolás megkezdése előtt alkalmazza a szolgáltatás kijelölését a legmagasabb adatértékkel rendelkező oszlopok vagy változók meghatározásához.

## <a name="how-to-configure-tune-model-hyperparameters"></a>A Tune Model Hiperparaméterek beállítása konfigurálása  

A gépi tanulási modellek optimális hiperparaméterek beállítása megtanulása a folyamatok jelentős mértékű használatát igényli.

### <a name="train-a-model-by-using-a-parameter-sweep"></a>Modell betanítása egy paraméteres sweep használatával  

Ez a szakasz azt ismerteti, hogyan hajtható végre egy alapszintű paraméteres sweep, amely a modell Hiperparaméterek beállítása-moduljának finomhangolását végzi.

1.  Adja hozzá a Tune Model Hiperparaméterek beállítása modult a folyamathoz a tervezőben.

2.  Csatlakoztasson egy nem betanított modellt a bal szélső bemenethez. 

    > [!NOTE] 
    > A **modell Hiperparaméterek beállítása hangolása** csak a beépített gépi tanulási algoritmus-modulokhoz csatlakozhat, és nem támogatja a **Python-modell létrehozásakor** létrehozott testreszabott modellt.


3.  Adja hozzá a betanításhoz használni kívánt adatkészletet, és kapcsolja össze a Hiperparaméterek beállítása hangolási modell középső bemenetével.  

    Ha címkézett adatkészlettel rendelkezik, a jobb szélső bemeneti porthoz (**opcionális ellenőrzési adatkészlet**) is csatlakoztatható. Ez lehetővé teszi a pontosság mérését a képzés és a hangolás során.

4.  A modell Hiperparaméterek beállítása finomhangolása jobb oldali paneljén válasszon egy értéket a **paraméter-elsöprő üzemmódhoz**. Ezzel a beállítással szabályozhatja, hogy a paraméterek hogyan legyenek kiválasztva.

    - **Teljes rács**: Ha ezt a beállítást választja, a modul hurkot mutat a rendszer által előre meghatározott rács fölé, hogy kipróbálja a különböző kombinációkat, és azonosítsa a legjobb tanulót. Ez a beállítás akkor lehet hasznos, ha nem tudja, mi a legjobb paraméter-beállítás, és szeretné kipróbálni az értékek összes lehetséges kombinációját.

    - **Véletlenszerű sweep**: Ha ezt a beállítást választja, a modul véletlenszerűen kiválasztja a paraméterek értékét a rendszer által meghatározott tartományon. Meg kell adnia, hogy legfeljebb hány futtatást kíván végrehajtani a modul végrehajtásához. Ez a beállítás akkor hasznos, ha a modell teljesítményét a választott mérőszámok használatával szeretné bővíteni, de továbbra is megőrzi a számítástechnikai erőforrásokat.    

5.  A **label (címke) oszlopban** nyissa meg az oszlop-választót egyetlen feliratú oszlop kiválasztásához.

6.  Válassza ki a futtatások számát:

    - **Futtatások maximális száma véletlenszerű húzáskor**: ha véletlenszerű sweepet választ, megadhatja, hogy a modell hány alkalommal legyen betanítva, a paraméterek véletlenszerű kombinációjának használatával.

7.  A **rangsoroláshoz** válassza ki a modellek rangsorolásához használandó egyetlen mérőszámot.

    Ha lefuttat egy paramétert, a modul kiszámítja a modell típusához tartozó összes vonatkozó mérőszámot, és visszaadja azokat a **sweep Results** jelentésben. A modul külön mérőszámokat használ a regressziós és besorolási modellekhez.

    A kiválasztott metrika azonban meghatározza a modellek rangsorolásának módját. Csak a legfelső szintű modell, amelyet a kiválasztott metrika rangsorol, a kiképzésen alapuló modellként jeleníti meg az eredményt.

8.  A **véletlenszerű mag** mezőben adja meg a paraméter elindításához használandó számot. 

9. A folyamat elküldése.

## <a name="results-of-hyperparameter-tuning"></a>A hiperparaméter hangolásának eredményei

A betanítás befejezése után:

+ A sweep eredményeinek megtekintéséhez kattintson a jobb gombbal a modulra, majd válassza a **Megjelenítés** lehetőséget, vagy kattintson a jobb gombbal a modul bal oldali kimeneti portjára a megjelenítéshez.

    A **sweep eredmény** tartalmazza az összes paraméter-áthúzást és pontosságot, amely a modell típusára vonatkozik, és a rangsoroláshoz kiválasztott metrika határozza meg, hogy melyik modell a "legjobb".

+ A betanított modell pillanatképének mentéséhez válassza a **kimenetek + naplók** fület a **Train Model** modul jobb oldali paneljén. Válassza az **adatkészlet regisztrálása** ikont a modell újrafelhasználható modulként való mentéséhez.


## <a name="technical-notes"></a>Technikai megjegyzések

Ez a szakasz a megvalósítás részleteit és a tippeket tartalmazza.

### <a name="how-a-parameter-sweep-works"></a>A paraméter-elsöpörés működése

Amikor beállít egy paramétert, megadhatja a keresés hatókörét. Előfordulhat, hogy a keresés a véletlenszerűen kiválasztott, véges számú paramétert használja. Vagy lehet, hogy az Ön által definiált paraméter-terület teljes körű keresést végez.

+ **Véletlenszerű sweep**: Ez a beállítás egy modellt állít be egy megadott számú iteráció használatával. 

  Megadhatja az értékek tartományát, és a modul az értékek véletlenszerűen kiválasztott részhalmazát használja. Az értékek helyettesítéssel vannak kiválasztva, ami azt jelenti, hogy az előzőleg véletlenszerűen kiválasztott számok nem törlődnek a rendelkezésre álló számok készletéből. Így az is lehetséges, hogy bármely kiválasztott érték ugyanaz marad, mint minden halad.  

+ **Teljes rács**: a teljes rács használatának lehetősége azt jelenti, hogy minden kombináció tesztelve van. Ez a lehetőség a legalaposabb, de a legtöbb időt igényli. 

### <a name="controlling-the-length-and-complexity-of-training"></a>A képzés hosszának és összetettségének szabályozása

A beállítások több kombinációjának megismétlése időigényes lehet, így a modul számos módot biztosít a folyamat korlátozására:

+ Korlátozza a modellek teszteléséhez használt iterációk számát.
+ Korlátozza a paraméter területét.
+ Korlátozza az ismétlések számát és a paraméter területét.

Azt javasoljuk, hogy az adott adatkészletre és modellre vonatkozóan a leghatékonyabb képzési módszer meghatározásához a beállításokkal folyamatokat.

### <a name="choosing-an-evaluation-metric"></a>Értékelési metrika kiválasztása

A tesztelés végén a modell egy jelentést jelenít meg, amely tartalmazza az egyes modellek pontosságát, hogy áttekintse a mérőszám eredményeit:

- A rendszer az összes bináris besorolási modellhez egységes mérőszámot használ.
- Az összes többosztályos besorolási modell pontosságát használja a rendszer.
- A regressziós modellekhez a metrikák különböző készletei használatosak. 

A képzés során azonban *egyetlen* mérőszámot kell kiválasztania, amelyet a hangolási folyamat során generált modellek rangsorolásához kíván használni. Előfordulhat, hogy a legjobb metrika az üzleti problémától és a téves pozitív és a hamis negatív értéktől függően változhat.

#### <a name="metrics-used-for-binary-classification"></a>A bináris besoroláshoz használt metrikák

-   A **pontosság** a valódi eredmények aránya az összes esetben.  

-   A **pontosság** a pozitív eredmények valódi eredményeinek aránya.  

-   A **visszahívás** az összes eredmény összes helyes eredményének töredéke.  

-   Az **F-score** egy mérték, amely egyensúlyt teremt a pontosság és a visszahívás között.  

-   A **AUC** olyan érték, amely a görbe alatti területnek felel meg, ha a hamis pozitív értékek az x tengelyen jelennek meg, és az igaz pozitív értékek az y tengelyen vannak ábrázolva.  

-   A **naplózási műveletek átlagos elvesztése** a két valószínűségi eloszlás közötti különbség: az igaz, a modellben pedig egy.  

#### <a name="metrics-used-for-regression"></a>Regresszióhoz használt metrikák

-   **Abszolút érték:** a modellben lévő összes hiba átlaga, ahol a *hiba* azt jelenti, hogy az előre jelzett érték távolsága igaz. Gyakran *Mae*-ként van rövidítve.  

-   Az **átlagosan négyzetes hiba gyökere** a hibák négyzetének átlagát méri, majd az érték gyökerét veszi át. Ez gyakran rövidítve *gyökátlagos*.  

-   A **relatív abszolút hiba** a True érték százalékaként jeleníti meg a hibát.  

-   A **relatív négyzetes hiba** a teljes négyzetes hibát normalizálja úgy, hogy az előre jelzett értékek teljes négyzetes hibáját választja.  

-   A **meghatározási együttható** egyetlen szám, amely azt jelzi, hogy mennyire jól illeszkednek az adatmodellek. Az egyik érték azt jelenti, hogy a modell pontosan megfelel az adatmennyiségnek. A nulla érték azt jelenti, hogy az adatmennyiség véletlenszerű, vagy más módon nem fér hozzá a modellhez. Gyakran nevezik *r <sup>2</sup>*, *r <sup>2</sup>* vagy *r-négyzet alakúnak*.  

### <a name="modules-that-dont-support-a-parameter-sweep"></a>A paramétert nem támogató modulok

A Azure Machine Learning szinte minden tanuló támogatja a többtényezős hitelesítést egy integrált paraméteres sweeptel, amely lehetővé teszi, hogy kiválassza a folyamathoz használandó paramétereket. Ha a tanuló nem támogatja az értékek tartományának beállítását, akkor továbbra is használhatja azt több érvényesítéssel. Ebben az esetben a rendszer az összes engedélyezett értéket kijelöli a sweep értékre. 


## <a name="next-steps"></a>Következő lépések

Tekintse [meg a Azure Machine learning elérhető modulok készletét](module-reference.md) . 

