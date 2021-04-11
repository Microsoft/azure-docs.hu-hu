---
title: Vowpal Wabbit modell betanítása
titleSuffix: Azure Machine Learning
description: Ismerje meg, hogyan hozhat létre gépi tanulási modellt a Vowpal Wabbit Model modullal a Vowpal Wabbit egy példányának használatával.
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 07/02/2020
ms.openlocfilehash: 70d0fc456b3697e3c74a5ec45cc936a02b77e591
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "104657654"
---
# <a name="train-vowpal-wabbit-model"></a>Vowpal Wabbit modell betanítása
Ez a cikk azt ismerteti, hogyan használható a **Vowpal Wabbit Model** modul a Azure Machine learning Designerben, és hogyan hozható létre gépi tanulási modell a Vowpal Wabbit használatával.  

A Vowpal Wabbit a gépi tanuláshoz való használatához formázza a bemenetet a Vowpal Wabbit követelményeinek megfelelően, és készítse elő az adatokat a szükséges formátumban. Ezzel a modullal adhatja meg a Vowpal Wabbit parancssori argumentumokat. 

A folyamat futtatásakor a Vowpal Wabbit egy példánya betöltődik a kísérlet futási idejébe, a megadott adattal együtt. A képzés befejezésekor a modell szerializálva lesz a munkaterületre. A modellt azonnal használhatja az adatpontszámhoz. 

Ahhoz, hogy fokozatosan betanítsa egy meglévő modellt az új adatokra, csatlakoztasson egy mentett modellt a **Vowpal Wabbit Model** **Vowpal Wabbit modell** bemeneti portjához, és adja hozzá az új adatokat a másik bemeneti porthoz.  

## <a name="what-is-vowpal-wabbit"></a>Mi az a Vowpal-Wabbit?  

A Vowpal Wabbit (VW) egy gyors, párhuzamos gépi tanulási keretrendszer, amelyet a Yahoo! elosztott számítástechnikai szolgáltatásának fejlesztett ki Kutatás. Később a Windows rendszerre portolták, és John Langford (Microsoft Research) által alakította ki a párhuzamos architektúrák tudományos számítástechnikai szolgáltatásait.  

A Vowpal-Wabbit a gépi tanulás szempontjából fontos funkciói közé tartozik a folyamatos tanulás (online tanulás), a dimenzióját csökkentése és az interaktív tanulás. A Vowpal Wabbit olyan megoldás, amely akkor is problémát jelent, ha nem fér bele a modellbe a memóriába.  

A Vowpal Wabbit elsődleges felhasználói olyan adatelemzők, akik korábban már használták a gépi tanulási feladatokhoz, például a besoroláshoz, a regresszióhoz, a témakör modellezéséhez vagy a mátrix faktorizációs tartozó keretrendszert. A Vowpal-Wabbit készült Azure wrapper nagyon hasonlít a helyszíni verzióra, így a Vowpal-Wabbit hatékony funkciói és natív teljesítménye, valamint a betanított modell üzembe helyezése könnyen elvégezhető.  

A [szolgáltatás-kivonatolási](feature-hashing.md) modul a Vowpal Wabbit által biztosított funkciókat is tartalmazza, amely lehetővé teszi a szöveges adatkészletek bináris szolgáltatásba való átalakítását egy kivonatoló algoritmus használatával.  

## <a name="how-to-configure-vowpal-wabbit-model"></a>A Vowpal Wabbit-modell konfigurálása  

Ez a szakasz azt ismerteti, hogyan lehet új modellt betanítani, és hogyan adhat hozzá új információkat egy meglévő modellhez.

A Designer más moduljaitól eltérően ez a modul a modul paramétereit is megadja, és a modellt is betanítja. Ha már rendelkezik egy meglévő modellel, a modell fokozatos betanításához hozzáadhatja opcionális bemenetként is.

+ [A bemeneti adatok előkészítése a szükséges formátumok egyikében](#prepare-the-input-data)
+ [Új modell betanítása](#create-and-train-a-vowpal-wabbit-model)
+ [Meglévő modell fokozatos betanítása](#retrain-an-existing-vowpal-wabbit-model)

### <a name="prepare-the-input-data"></a>A bemeneti adatok előkészítése

Ha ezt a modult használja a modell betanításához, a bemeneti adatkészlet csak egyetlen szöveges oszlopból állhat a két támogatott formátum valamelyikében: **SVMLight** vagy **VW**. Ez nem jelenti azt, hogy a Vowpal Wabbit csak szöveges adatok elemzését végzi el, csak azt, hogy a szolgáltatások és az értékek a szükséges szövegfájl-formátumban legyenek előkészítve.  

Az adatok két típusú adatkészletből, fájl-adatkészletből vagy táblázatos adatkészletből olvashatók. Mindkét adatkészlet SVMLight vagy VW formátumúnak kell lennie. A Vowpal Wabbit adatformátumának előnye, hogy nem igényel oszlopos formátumot, amely lemezterületet takarít meg a ritka adatmennyiségek kezelésekor. További információ erről a formátumról: [Vowpal Wabbit wiki oldal](https://github.com/JohnLangford/vowpal_wabbit/wiki/Input-format).  

### <a name="create-and-train-a-vowpal-wabbit-model"></a>Vowpal Wabbit-modell létrehozása és betanítása

1. Adja hozzá a **Vowpal Wabbit Model** modult a kísérlethez. 
  
2. Adja hozzá a betanítási adatkészletet, és kapcsolja össze a **betanítási adatokat**. Ha a betanítási adatkészlet egy olyan könyvtár, amely tartalmazza a betanítási adatfájlt, adja meg a betanítási adatfájl **nevét** a betanítási adatfájl nevével. Ha a betanítási adatkészlet egyetlen fájl, hagyja üresen **a betanítási adat fájljának nevét** .

3. A **VW-argumentumok** szövegmezőbe írja be a Vowpal Wabbit végrehajtható fájl parancssori argumentumait.

     Például a Hozzáadás lehetőséggel *`–l`* megadhatja a képzési sebességet, vagy *`-b`* jelezheti a kivonatoló bitek számát.  

     További információ: [Vowpal Wabbit Parameters](#supported-and-unsupported-parameters) szakasz.  

4. **A betanítási adatok fájljának neve**: írja be a bemeneti adatokat tartalmazó fájl nevét. Ezt az argumentumot csak akkor használja a rendszer, ha a betanítási adatkészlet egy könyvtár.

5. **Adja meg a fájltípust**: adja meg a betanítási adatai által használt formátumot. A Vowpal Wabbit a következő két bemeneti fájlformátumot támogatja:  

    - A **VW** a Vowpal Wabbit által használt belső formátumot jelöli. A részletekért tekintse meg a [Vowpal Wabbit wiki oldalát](https://github.com/JohnLangford/vowpal_wabbit/wiki/Input-format) . 
    - A **SVMLight** egy másik gépi tanulási eszköz által használt formátum. 

6. **Kimeneti olvasható forrásfájl**: válassza a lehetőséget, ha azt szeretné, hogy a modul mentse az olvasható modellt a futtatási rekordokra. Ez az argumentum a `--readable_model` VW parancssorban található paraméternek felel meg.  

7. **Kimeneti invertált kivonatoló fájl**: válassza a lehetőséget, ha azt szeretné, hogy a modul a futtatási rekordok egyik fájljába mentse a fordított kivonatolási függvényt. Ez az argumentum a `--invert_hash` VW parancssorban található paraméternek felel meg.  

8. A folyamat elküldése.

### <a name="retrain-an-existing-vowpal-wabbit-model"></a>Meglévő Vowpal-Wabbit modell újratanítása

A Vowpal Wabbit támogatja a növekményes képzést azáltal, hogy új adattípust ad hozzá egy meglévő modellhez. A meglévő modelleket kétféleképpen lehet betanítani:

+ Egy másik **Vowpal Wabbit modell** -modul kimenetének használata ugyanabban a folyamatban.  
  
+ A tervező bal oldali navigációs paneljének **adatkészletek** kategóriájában keresse meg a mentett modellt, majd húzza azt a folyamatba.  

1. Adja hozzá a **Train Vowpal Wabbit Model** modult a folyamathoz.  
2. A korábban betanított modell összekötése a modul **előre betanított Vowpal Wabbit modellbe** való bemenő portjával.
3. Kapcsolódjon az új betanítási adatokhoz a modul **betanítási adatok** bemeneti portjához.
4. A **Vowpal Wabbit modell** paraméterek paneljén adja meg az új betanítási adatok formátumát, valamint a betanítási adatfájl nevét is, ha a bemeneti adatkészlet egy könyvtár.
5. Válassza ki a **kimeneti olvasható modell fájlját** és a **kifordított kivonatoló fájl** beállításait, ha a megfelelő fájlokat menteni kell a futtatási rekordokban.

6. A folyamat elküldése.  
7. Válassza ki a modult, és válassza az **adatkészlet regisztrálása** a jobb oldali ablaktábla **kimenetek + naplók** lapján, hogy megőrizze a frissített modellt a Azure Machine learning munkaterületen.  Ha nem ad meg új nevet, a frissített modell felülírja a meglévő mentett modellt.

## <a name="results"></a>Results (Eredmények)

+ A modellből származó pontszámok létrehozásához használja a [score Vowpal Wabbit modellt](score-vowpal-wabbit-model.md).

> [!NOTE]
> Ha telepítenie kell a betanított modellt a tervezőben, győződjön meg arról, hogy a pontszám **modell** helyett a [score Vowpal Wabbit modellje](score-vowpal-wabbit-model.md) csatlakozik a [webszolgáltatás kimeneti moduljának](web-service-input-output.md) bemenetéhez a következtetési folyamatban.

## <a name="technical-notes"></a>Technikai megjegyzések

Ez a szakasz megvalósítási részleteket, tippeket és válaszokat tartalmaz a gyakori kérdésekre.

### <a name="advantages-of-vowpal-wabbit"></a>A Vowpal Wabbit előnyei

A Vowpal Wabbit rendkívül gyors tanulást tesz lehetővé olyan nem lineáris funkciókkal, mint az n-gramm.  

A Vowpal Wabbit *online tanulási* technikákat használ, például a sztochasztikus gradienst (SGD), hogy egyszerre egy adott rekordhoz illeszkedjen. Így nagyon gyorsan megismétli a nyers adatfeldolgozást, és a többi modellnél gyorsabban fejlesztheti a jó előrejelzőt. Ez a megközelítés azt is megakadályozza, hogy az összes betanítási adatmennyiséget beolvassa a memóriába.  

A Vowpal Wabbit az összes adatokat kivonatokra konvertálja, nem csak szöveges adatokat, hanem más kategorikus változókat. A kivonatok használata hatékonyabbá teszi a regressziós súlyok használatát, ami kritikus fontosságú a hatékony sztochasztikus átmenethez.  

###  <a name="supported-and-unsupported-parameters"></a>Támogatott és nem támogatott paraméterek 

Ez a szakasz a Azure Machine Learning Designer Vowpal-Wabbit parancssori paramétereinek támogatását ismerteti. 

Általában csak az argumentumok korlátozott halmaza támogatott. Az argumentumok teljes listáját a [Vowpal Wabbit wiki oldalán](https://github.com/JohnLangford/vowpal_wabbit/wiki/Command-line-arguments)végezheti el.    

A következő paraméterek nem támogatottak:

-   A ben megadott bemeneti/kimeneti beállítások [https://github.com/JohnLangford/vowpal_wabbit/wiki/Command-line-arguments](https://github.com/JohnLangford/vowpal_wabbit/wiki/Command-line-arguments)  
  
     A modul már automatikusan konfigurálja ezeket a tulajdonságokat.  
  
-   Továbbá minden olyan lehetőség, amely több kimenetet állít elő, vagy több bemenetet is igénybe vesz, nem engedélyezett. Ezek közé tartozik a, a *`--cbt`* *`--lda`* és a *`--wap`* .  
  
-   Csak a felügyelt tanulási algoritmusok támogatottak. Ezért ezek a beállítások nem támogatottak: *`–active`* , `--rank` *`--search`* stb. 

### <a name="restrictions"></a>Korlátozások

Mivel a szolgáltatás célja, hogy támogassa a Vowpal-Wabbit tapasztalt felhasználóit, a bemeneti adatokat előre elő kell készíteni a Vowpal Wabbit natív szöveges formátumának használatával, nem pedig a más modulok által használt adatkészlet formátumát.

## <a name="next-steps"></a>Következő lépések

Tekintse [meg a Azure Machine learning elérhető modulok készletét](module-reference.md) . 
