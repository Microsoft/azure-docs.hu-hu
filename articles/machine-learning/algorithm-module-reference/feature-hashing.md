---
title: Szolgáltatás-kivonatolási modul referenciája
titleSuffix: Azure Machine Learning
description: Megtudhatja, hogyan használhatja a Azure Machine Learning Designer funkció-kivonatolási modulját szöveges adatokat szabadkézi.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/22/2020
ms.openlocfilehash: cd48b32afee320aa3d252540d566317c374c73a8
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "93420784"
---
# <a name="feature-hashing-module-reference"></a>Szolgáltatás-kivonatolási modul referenciája

Ez a cikk a Azure Machine Learning Designer részét képező modult ismerteti.

A szolgáltatás-kivonatolási modul használatával az angol nyelvű adatfolyamokat egész számokból álló funkciókba alakíthatja át. Ezt követően átadhatja ezt a kivonatoló funkciót egy gépi tanulási algoritmusba egy szöveges elemzési modell betanításához.

Az ebben a modulban megadott funkció-kivonatolási funkció a nimbusml-keretrendszeren alapul. További információ: [NgramHash osztály](/python/api/nimbusml/nimbusml.feature_extraction.text.extractor.ngramhash?view=nimbusml-py-latest&preserve-view=true).

## <a name="what-is-feature-hashing"></a>Mi a funkció-kivonatolás?

A szolgáltatások kivonatolása úgy működik, hogy az egyedi tokeneket egész számokra konvertálja. A művelet a bemenetként megadott pontos karakterláncokon működik, és nem végez nyelvi elemzést vagy előfeldolgozást. 

Tegyük fel például, hogy a fentiekhez hasonló egyszerű mondatokat, majd egy hangulati pontszámot követ. Tegyük fel, hogy ezt a szöveget szeretné használni a modell létrehozásához.

|Felhasználói szöveg|Hangulat|
|--------------|---------------|
|Imádtam ezt a könyvet|3|
|Utáltam ezt a könyvet|1|
|Ez a könyv nagyszerű volt|3|
|Szeretem a könyveket|2|

Belsőleg a funkció-kivonatolási modul egy n-grammos szótárt hoz létre. Az adatkészlet bigrams listája például a következőhöz hasonló lesz:

|Kifejezés (bigrams)|Gyakoriság|
|------------|---------------|
|Ez a könyv|3|
|Szerettem|1|
|Utáltam|1|
|Szeretem|1|

Az n-gramm méretének szabályozására az **n-gramm** tulajdonság használható. Ha a bigrams lehetőséget választja, a rendszer a unigrams is kiszámítja. A szótár a következőhöz hasonló egyetlen kifejezést is tartalmaz:

|Kifejezés (unigrams)|Gyakoriság|
|------------|---------------|
|könyv|3|
|I|3|
|könyvek|1|
|lett|1|

A szótár felépítése után a funkció-kivonatolási modul a szótári kifejezést kivonatoló értékre konvertálja. Ezután kiszámítja, hogy az egyes esetekben használták-e a szolgáltatást. A modul minden egyes sornyi szöveges adatokat tartalmaz, és az egyes kivonatoló funkciók egy oszlopát adja vissza.

Például a kivonatolás után a funkció oszlopai a következőhöz hasonló módon jelenhetnek meg:

|Minősítés|Kivonatolási funkció 1|Kivonatolási funkció 2|3. kivonatolási funkció|
|-----|-----|-----|-----|
|4|1|1|0|
|5|0|0|0|

* Ha az oszlopban szereplő érték 0, akkor a sor nem tartalmazta a kivonatoló funkciót.
* Ha az érték 1, akkor a sor tartalmazta a funkciót.

A szolgáltatás-kivonatolás lehetővé teszi, hogy a változó hosszúságú szöveges dokumentumokat a dimenzióját csökkentése érdekében numerikus funkciós vektorként jelenítse meg. Ha a Text (szöveg) oszlopot a betanításhoz próbálta használni, a rendszer egy kategorikus funkció oszlopként kezeli, amely számos különböző értéket tartalmaz.

A numerikus kimenetek a gyakori gépi tanulási módszerek használatát is teszik elérhetővé, beleértve a besorolást, a fürtözést és az információk lekérését. Mivel a keresési műveletek a karakterlánc-összehasonlítás helyett egész kivonatokat használhatnak, a funkciók súlyozásának beszerzése is sokkal gyorsabb.

## <a name="configure-the-feature-hashing-module"></a>A szolgáltatás kivonatolási moduljának konfigurálása

1.  Adja hozzá a szolgáltatás-kivonatolási modult a folyamathoz a tervezőben.

1. Az elemezni kívánt szöveget tartalmazó adatkészlet csatlakoztatása.

    > [!TIP]
    > Mivel a funkció-kivonatolás nem hajt végre olyan lexikális műveleteket, mint például a kivonás vagy a csonkítás, a szolgáltatások kivonatolása előtt időnként a szöveg előfeldolgozásával jobb eredményeket érhet el. 

1. Állítsa be a **cél oszlopokat** a kivonatoló funkciókra átalakítani kívánt szöveges oszlopokra. Ne feledje, hogy:

    * Az oszlopoknak karakterlánc adattípusúnak kell lenniük.
    
    * Több szöveges oszlop kiválasztásával jelentős hatással lehet a funkciók dimenzióját. Egy 10 bites kivonat oszlopainak száma például 1 024, és egyetlen oszlopból 2 048 két oszlopra.

1. **Kivonatoló bitsize** használata a kivonatoló tábla létrehozásakor használandó bitek számának megadásához.
    
    Az alapértelmezett bit mérete 10. Számos probléma esetén ez az érték megfelelő. Előfordulhat, hogy a betanítási szövegben az n-gramm szókincs méretétől függően több helyre van szükség az ütközések elkerüléséhez.
    
1. **N-gramm** esetén adjon meg egy számot, amely meghatározza a képzési szótárhoz hozzáadandó N-g maximális hosszát. Az n-Gram az *n* betűs sorozat, amely egyedi egységként van kezelve.

    Ha például a 3, a unigrams, a bigrams és a Trigrams értéket adja meg, a rendszer létrehozza a következőt:.

1. A folyamat elküldése.

## <a name="results"></a>Results (Eredmények)

A feldolgozás befejezése után a modul egy átalakított adatkészletet ad eredményül, amelyben az eredeti szöveges oszlop több oszlopra lett konvertálva. Minden oszlop a szöveg egyik funkcióját jelöli. Attól függően, hogy milyen jelentős a szótár, az eredményül kapott adatkészlet nagyméretű lehet:

|Oszlop neve 1|2-es típusú oszlop|
|-------------------|-------------------|
|USERTEXT|Eredeti adatoszlop|
|HANGULATELEMZÉS|Eredeti adatoszlop|
|USERTEXT-kivonatolási funkció 1|Kivonatoló funkció oszlopa|
|USERTEXT – kivonatoló funkció 2|Kivonatoló funkció oszlopa|
|USERTEXT-kivonatolási funkció n|Kivonatoló funkció oszlopa|
|USERTEXT-kivonatolási funkció 1024|Kivonatoló funkció oszlopa|

Az átalakított adatkészlet létrehozása után használhatja a betanítási modell modul bemenetként.
 
## <a name="best-practices"></a>Ajánlott eljárások

Az alábbi ajánlott eljárások segítséget nyújtanak a funkció-kivonatolási modul leghatékonyabban történő kihasználása érdekében:

* A bemeneti szöveg előfeldolgozásához vegyen fel egy előfeldolgozó szöveges modult, mielőtt a szolgáltatás-kivonatolást használja. 

* Adja hozzá a Select Columns modult, miután a funkció-kivonatolási modul eltávolítja a szöveges oszlopokat a kimeneti adatkészletből. A kivonatolási funkciók létrehozása után nincs szükség a szöveges oszlopokra.
    
* Ezeket a szöveg-előfeldolgozási beállításokat érdemes használni az eredmények egyszerűsítése és a pontosság javítása érdekében:

    * Sortörés
    * A Word eltávolításának leállítása
    * Kis-és nagybetűk normalizálása
    * Írásjelek és speciális karakterek eltávolítása
    * Eredő  

Az egyes megoldásokban alkalmazandó előfeldolgozási módszerek optimális készlete a tartománytól, a szókincstől és az üzleti igényektől függ. a folyamat adataival megtekintheti, hogy mely szöveg-feldolgozási módszerek a leghatékonyabbak.

## <a name="next-steps"></a>Következő lépések
            
Tekintse [meg a Azure Machine learning elérhető modulok készletét](module-reference.md)