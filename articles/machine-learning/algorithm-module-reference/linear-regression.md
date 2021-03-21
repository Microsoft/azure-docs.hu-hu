---
title: 'Lineáris regresszió: modul-hivatkozás'
titleSuffix: Azure Machine Learning
description: Megtudhatja, hogyan hozhat létre egy lineáris regressziós modellt a Azure Machine Learning lineáris regressziós modullal a folyamatokban való használatra.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 04/22/2020
ms.openlocfilehash: 809b1be4f9f12e1963ff5caeaacd109c84db154f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "93323659"
---
# <a name="linear-regression-module"></a>Lineáris regressziós modul
Ez a cikk a Azure Machine Learning Designer egyik modulját ismerteti.

Ezzel a modullal lineáris regressziós modellt hozhat létre a folyamatokban való használatra.  Lineáris regressziós kísérlet egy lineáris kapcsolat létrehozására egy vagy több független változó és egy numerikus eredmény, vagy egy függő változó között. 

Ezzel a modullal lineáris regressziós módszert határozhat meg, majd elvégezheti a modell tanítását egy címkézett adatkészlet használatával. Ezt követően a betanított modell segítségével előrejelzéseket készíthet.

## <a name="about-linear-regression"></a>A lineáris regresszió ismertetése

A lineáris regresszió egy közös statisztikai módszer, amely a gépi tanulásban lett elfogadva, és számos új módszerrel bővült a vonal-és mérési hibákkal. Egyszerűen fogalmazva, a regresszió egy numerikus cél előrejelzésére utal. A lineáris regresszió még mindig jó választás, ha egy egyszerű prediktív feladathoz szeretne egyszerű modellt használni. A lineáris regresszió a nagy dimenziós, ritka adathalmazok összetettségének hiányában is jól működik.

A Azure Machine Learning a lineáris regresszió mellett számos regressziós modellt támogat. A "regresszió" kifejezés azonban lazán is értelmezhető, és a más eszközökben megadott regressziós típusok nem támogatottak.

+ A klasszikus regressziós probléma egyetlen független változót és egy függő változót is magában foglal. Ezt nevezzük *egyszerű regressziónak*.  Ez a modul támogatja az egyszerű regressziót.

+ *Több lineáris regresszió* két vagy több olyan független változót foglal magában, amelyek egyetlen függő változóhoz járulnak hozzá. Azok a problémák, amelyekben a rendszer több bemenetet használ egy numerikus eredmény előrejelzésére, más néven *többváltozós lineáris regressziónak* is nevezik.

    A **lineáris regressziós** modul megoldja ezeket a problémákat, csakúgy, mint a többi regressziós modul többségét.

+ A *többcímkés regresszió* a több függő változó egy adott modellen belüli előrejelzésének feladata. A többcímkés logisztikai regresszió esetében például a minta több különböző címkéhez is hozzárendelhető. (Ez különbözik az egy osztály változón belüli több szint előrejelzésének feladattól.)

    Az ilyen típusú regresszió nem támogatott a Azure Machine Learningban. Több változó előrejelzéséhez hozzon létre egy külön tanulót minden előre jelezni kívánt kimenethez.

Az évek statisztikusai egyre fejlettebb módszereket fejlesztenek a regresszióhoz. Ez még a lineáris regresszió esetén is igaz. Ez a modul két módszert támogat a hibák mérésére és a regressziós vonal igazítására: a legkisebb négyzetes metódust és a színátmenetes leereszkedést.

- A **színátmenet-leereszkedés** olyan metódus, amely a modell betanítási folyamatának egyes lépéseiben minimálisra csökkentheti a hibák mennyiségét. Széles körben vizsgálták a színátmenetek és a különböző tanulási problémák optimalizálásának számos változatát. Ha ezt a lehetőséget választja a **megoldási módszerhez**, számos paramétert állíthat be a lépés méretének, a tanulási aránynak és így tovább. Ez a beállítás támogatja az integrált paraméteres sweep használatát is.

- A **szokásos legkisebb négyzet** a lineáris regresszió egyik leggyakrabban használt módszere. Például a legalacsonyabb négyzet az a módszer, amelyet a Microsoft Excel Analysis ToolPak használ.

    A legkisebb négyzetek a Loss függvényre utalnak, amely a tényleges érték és a várt vonal közötti távolságot eredményezi, és a modellnek a négyzetes hiba minimalizálása mellett illeszkedik. Ez a módszer feltételezi, hogy a bemenetek és a függő változó között erős lineáris kapcsolat áll fenn.

## <a name="configure-linear-regression"></a>Lineáris regresszió konfigurálása

Ez a modul két módszert támogat a regressziós modellek beépítéséhez, különböző lehetőségekkel:

+ [Regressziós modell igazítása a szokásos legkisebb négyzetek használatával](#create-a-regression-model-using-ordinary-least-squares)

    Kis adatkészletek esetében a legjobb, ha a legkevesebb négyzetet választja. Ehhez hasonló eredményeket kell megadni az Excelben.
    
+ [Regressziós modell létrehozása online átmenetes leereszkedés használatával](#create-a-regression-model-using-online-gradient-descent)

    A színátmenetes desüllyedás jobb veszteségi funkció a összetettebb, vagy túl kevés betanítási adattal rendelkező modellek esetében a változók száma miatt.

### <a name="create-a-regression-model-using-ordinary-least-squares"></a>Regressziós modell létrehozása a legkisebb négyzetek használatával

1. Adja hozzá a **lineáris regressziós modell** modult a folyamathoz a tervezőben.

    Ez a modul a **Machine learning** kategóriában található. Bontsa ki a **modell inicializálása**, majd a **regresszió** csomópontot, majd húzza a **lineáris regressziós modell** modult a folyamatba.

2. A **Tulajdonságok** ablaktábla **megoldási módszer** legördülő listájában válassza a **szokásos legalacsonyabb négyzeteket**. Ez a beállítás határozza meg a regressziós vonal megkereséséhez használt számítási módszert.

3. Az **L2 regularizációs súlyozásnál** adja meg az L2 regularizációs súlyozásához használandó értéket. Azt javasoljuk, hogy ne használjon nullától eltérő értéket a túlilleszkedés elkerüléséhez.

     Ha többet szeretne megtudni arról, hogy a regularizációs hogyan befolyásolja a modellek beépítését, tekintse meg ezt a cikket: [L1 és L2 regularizációs for Machine learning](/archive/msdn-magazine/2015/february/test-run-l1-and-l2-regularization-for-machine-learning)

4. Ha meg szeretné tekinteni az elfogáshoz tartozó kifejezést, válassza az **elfogási kifejezés belefoglalása** lehetőséget.

    Törölje ezt a beállítást, ha nem kell áttekintenie a regressziós képletet.

5. A **véletlenszerű számú magok** esetében igény szerint beírhat egy értéket a modell által használt véletlenszám-generátor kivetéséhez.

    A magok értékének használata akkor lehet hasznos, ha ugyanazokat az eredményeket szeretné fenntartani ugyanazon folyamat különböző futtatásai között. Ellenkező esetben az alapértelmezett érték a rendszerórát használó érték használata.


7. Adja hozzá a [Train Model](./train-model.md) modult a folyamathoz, és csatlakoztasson egy címkézett adatkészletet.

8. A folyamat elküldése.

### <a name="results-for-ordinary-least-squares-model"></a>A legkevesebb négyzetes modell eredményei

A betanítás befejezése után:


+ Az előrejelzések elvégzéséhez csatlakoztassuk a betanított modellt a [pontszám modell](./score-model.md) modulhoz, valamint egy új értékből álló adatkészletet. 


### <a name="create-a-regression-model-using-online-gradient-descent"></a>Regressziós modell létrehozása online átmenetes leereszkedés használatával

1. Adja hozzá a **lineáris regressziós modell** modult a folyamathoz a tervezőben.

    Ez a modul a **Machine learning** kategóriában található. Bontsa ki a **modell inicializálása**, majd a **regresszió** csomópontot, és húzza a **lineáris regressziós modell** modult a folyamatba.

2. A **Tulajdonságok** ablaktábla **megoldási módszer** legördülő listájában válassza ki az **online átmenetes ereszkedést** a regressziós vonal megtalálásához használt számítási módszerként.

3. Az **oktatói mód létrehozásához** jelezze, hogy a modellt előre definiált paraméterekkel kívánja-e betanítani, vagy ha a modellt a sweep paraméter használatával szeretné optimalizálni.

    + **Egyetlen paraméter**: ha tudja, hogyan szeretné konfigurálni a lineáris regressziós hálózatot, megadhatja az értékek adott készletét argumentumként.
    
    + **Paraméter tartománya**: akkor válassza ezt a lehetőséget, ha nem biztos benne, hogy a legjobb paramétereket szeretné használni, és szeretne futtatni egy paramétert. Válassza ki a megismételni kívánt értékek tartományát, és a [finomhangolási modell hiperparaméterek beállítása](tune-model-hyperparameters.md) az optimális eredményeket eredményező hiperparaméterek beállítása meghatározásához megadott beállítások összes lehetséges kombinációján.  

   
4. A **tanulási arány** beállításnál határozza meg a sztochasztikus színátmenet-optimalizáló kezdeti tanulási arányát.

5. A **képzési korszakok száma** mezőben adja meg azt az értéket, amely azt jelzi, hogy az algoritmusnak hányszor kell megismételni a példákat. A kis mennyiségű példát tartalmazó adatkészletek esetében ennek a számnak nagynak kell lennie a konvergencia eléréséhez.

6. **Funkciók normalizálása**: Ha már normalizálta a modell betanításához használt numerikus adattípust, akkor törölje ezt a beállítást. Alapértelmezés szerint a modul az összes numerikus bemenetet 0 és 1 közötti tartományba normalizálja.

    > [!NOTE]
    > 
    > Ne feledje, hogy ugyanazt a normalizáló módszert alkalmazza a pontozáshoz használt új adatértékekre.

7. Az **L2 regularizációs súlyozásnál** adja meg az L2 regularizációs súlyozásához használandó értéket. Azt javasoljuk, hogy ne használjon nullától eltérő értéket a túlilleszkedés elkerüléséhez.

    Ha többet szeretne megtudni arról, hogy a regularizációs hogyan befolyásolja a modellek beépítését, tekintse meg ezt a cikket: [L1 és L2 regularizációs for Machine learning](/archive/msdn-magazine/2015/february/test-run-l1-and-l2-regularization-for-machine-learning)


9. Válassza a tanulási arány **csökkentése** lehetőséget, ha azt szeretné, hogy a tanulási ráta az ismétlések előrehaladásával csökkenjen.  

10. A **véletlenszerű számú magok** esetében igény szerint beírhat egy értéket a modell által használt véletlenszám-generátor kivetéséhez. A magok értékének használata akkor lehet hasznos, ha ugyanazokat az eredményeket szeretné fenntartani ugyanazon folyamat különböző futtatásai között.


12. A modell betanítása:

    + Ha az **oktatói módot** **egyetlen paraméterként** állítja be, csatlakoztasson egy címkézett adatkészletet és a [betanítási modell](train-model.md) modult.  
  
    + Ha az **oktatói mód** beállítása **paraméter-tartományra** van beállítva, csatlakoztasson egy címkézett adatkészletet, és a modell betanításához használja a [modell hiperparaméterek beállítása](tune-model-hyperparameters.md).  
  
    > [!NOTE]
    > 
    > Ha egy paraméter tartományát adja át a [betanítási modellnek](train-model.md), az csak az alapértelmezett értéket használja az egyetlen paraméteres listában.  
    > 
    > Ha egy paraméter értékének egyetlen készletét adja át a [Tune Model hiperparaméterek beállítása](tune-model-hyperparameters.md) modulnak, amikor az egyes paraméterekhez különböző beállításokat vár, figyelmen kívül hagyja az értékeket, és a tanuló alapértelmezett értékeit használja.  
    > 
    > Ha a **paraméter tartománya** lehetőséget választja, és egyetlen értéket ad meg bármelyik paraméterhez, akkor a rendszer az egyetlen megadott értéket használja a teljes takarítás során, még akkor is, ha más paraméterek egy adott tartományon változnak.

13. A folyamat elküldése.

### <a name="results-for-online-gradient-descent"></a>Az online átmenetes ereszkedés eredményei

A betanítás befejezése után:

+ Az előrejelzések létrehozásához csatlakoztassuk a betanított modellt a [pontszám modell](./score-model.md) modulhoz, az új bemeneti adatokkal együtt.


## <a name="next-steps"></a>Következő lépések

Tekintse [meg a Azure Machine learning elérhető modulok készletét](module-reference.md) .