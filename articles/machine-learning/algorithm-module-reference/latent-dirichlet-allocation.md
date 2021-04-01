---
title: 'Látens Dirichlet-kiosztás: modul-hivatkozás'
titleSuffix: Azure Machine Learning
description: Megtudhatja, hogyan használhatja a látens Dirichlet kiosztási modult más, nem besorolt szöveg kategóriákba való csoportosításához.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 06/05/2020
ms.openlocfilehash: f9f239ea69aaf71e591a447feb300c13a45ba1a4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "90907848"
---
# <a name="latent-dirichlet-allocation-module"></a>Látens Dirichlet kiosztási modul

Ez a cikk azt ismerteti, hogyan használható a látens Dirichlet-kiosztási modul a Azure Machine Learning Designerben, hogy az egyébként nem besorolt szövegeket csoportosítsa kategóriákba. 

A látens Dirichlet-kiosztást (LDA) gyakran természetes nyelvi feldolgozásban használják a hasonló szövegek kereséséhez. Egy másik gyakori kifejezés a *témakör modellezése*.

Ez a modul a szöveg egy oszlopát veszi át, és létrehozza ezeket a kimeneteket:

+ A forrás szövege, valamint az egyes kategóriákhoz tartozó pontszám

+ A kinyert kifejezéseket és az egyes kategóriákhoz tartozó együtthatókat tartalmazó szolgáltatás-mátrix

+ Egy átalakítás, amelyet menthet és újra alkalmazhat a bemenetként használt új szövegre.

Ez a modul a scikit-Learn könyvtárat használja. További információ a scikit-ről: [GitHub-adattár](https://github.com/scikit-learn/scikit-learn), amely oktatóanyagokat és az algoritmus magyarázatát tartalmazza.

## <a name="more-about-latent-dirichlet-allocation"></a>További információ a látens Dirichlet kiosztásáról

A LDA általában nem a besorolási módszer. Azonban egy generatív megközelítést használ, így nem kell megadnia ismert címkéket, majd következtetni a mintázatokra.  Ehelyett az algoritmus létrehoz egy valószínűségi modellt, amely a témakörök csoportjai azonosítására szolgál. A valószínűségi modell segítségével a meglévő betanítási eseteket, illetve a modellbe bemenetként megadott új eseteket is osztályozhatja.

Érdemes lehet egy generatív modellt előnyben részesíteni, mivel elkerüli a szöveg és a kategóriák közötti kapcsolat erős feltételezéseit. Csak a szavak eloszlását használja matematikai modellekkel kapcsolatos témakörökben.

Ebben a dokumentumban a PDF-Letöltés: [látens Dirichlet-kiosztás: Bleiweiss, ng és Jordan](https://ai.stanford.edu/~ang/papers/nips01-lda.pdf)nyelven elérhető elméletet tárgyaljuk.

A modul implementációja a LDA [scikit-Learn könyvtárán](https://github.com/scikit-learn/scikit-learn/blob/master/sklearn/decomposition/_lda.py) alapul.

További információ: [technikai megjegyzések](#technical-notes) szakasz.

## <a name="how-to-configure-latent-dirichlet-allocation"></a>Látens Dirichlet-foglalás konfigurálása

Ehhez a modulhoz olyan adatkészlet szükséges, amely tartalmazza a nyers vagy az előfeldolgozott szöveg egy oszlopát.

1. Adja hozzá a **látens Dirichlet kiosztási** modult a folyamathoz.

2. A modul bemenete adjon meg egy adatkészletet, amely egy vagy több szöveges oszlopot tartalmaz.

3. A **célobjektum** mezőben válasszon ki egy vagy több olyan oszlopot, amely az elemezni kívánt szöveget tartalmazza.

    Több oszlopot is kiválaszthat, de a **karakterlánc** adattípusnak kell lennie.

    Mivel a LDA egy nagyméretű szolgáltatás-mátrixot hoz létre a szövegből, általában egyetlen szöveges oszlopot fog elemezni.

4. A  **modellhez tartozó témakörök számának** megadásához adjon meg egy 1 és 1000 közötti egész számot, amely azt jelzi, hogy hány kategóriát vagy témakört szeretne a bemeneti szövegből származtatni.

    Alapértelmezés szerint 5 témakör jön létre.

5. **N-gramm** esetén a kivonatolás során generált n-g maximális hosszát határozza meg.

    Az alapértelmezett érték a 2, ami azt jelenti, hogy mind a bigrams, mind a unigrams létrejön.

6. Válassza a **normalizálás** lehetőséget a kimeneti értékek valószínűségekre való átalakításához. 

    Ahelyett, hogy az átalakított értékeket egész számként ábrázolja, a kimenet és a szolgáltatás adatkészletében lévő értékek a következőképpen lesznek átalakítva:

    + Az adatkészletben lévő értékek valószínűséggel lesznek megjelenítve `P(topic|document)` .

    + A szolgáltatás témakörének mátrixában szereplő értékek a következő valószínűséggel lesznek megjelenítve: `P(word|topic)` .

    > [!NOTE] 
    > Azure Machine Learning Designerben a scikit-Learn könyvtár már nem támogatja a 0,19-es verzió nem normalizált *doc_topic_distr* kimenetét. Ebben a modulban a **normalizálás** paraméter csak a *szolgáltatás témakörének mátrix* kimenetére alkalmazható. Az *átalakított adatkészlet* kimenete mindig normalizált.

7. Válassza a **minden beállítás megjelenítése** lehetőséget, majd állítsa **igaz** értékre, ha a következő speciális paramétereket szeretné beállítani.

    Ezek a paraméterek a LDA scikit-Learn megvalósítására vonatkoznak. Van néhány jó oktatóanyag a scikit-Learn LDA, valamint a hivatalos [scikit – Learn dokumentumban](https://scikit-learn.org/stable/modules/generated/sklearn.decomposition.LatentDirichletAllocation.html).

    + **Rho paraméter**. Adjon meg egy korábbi valószínűséget a adatelégtelenségetához. Ez a paraméter a sklearn `topic_word_prior` paraméterének felel meg. Ha azt szeretné, hogy a szavak eloszlása lapos legyen, használja az **1** értéket. Ez azt jelenti, hogy a rendszer az összes szót equiprobable feltételezi. Ha úgy gondolja, hogy a legtöbb szó ritkán jelenik meg, lehet, hogy alacsonyabb értékre állítja be.

    + **Alpha paraméter**. Meg kell adni egy korábbi valószínűséget a adatelégtelenséget témák súlyozásához. Ez a paraméter a sklearn `doc_topic_prior` paraméterének felel meg.

    + **A dokumentumok becsült száma**. Adjon meg egy számot, amely a feldolgozásra kerülő dokumentumok (sorok) számának legjobb becslését jelöli. Ez a paraméter lehetővé teszi, hogy a modul elegendő méretű kivonatoló táblázatot foglaljon le. Az `total_samples` scikit-Learn paraméternek felel meg.

    + **A köteg mérete**. Adja meg azt a számot, amely azt jelzi, hogy hány sort kell belefoglalni a LDA-modellbe küldendő egyes kötegekben. Ez a paraméter megfelel a `batch_size` scikit-Learn paraméternek.

    + A **tanulás frissítési ütemtervében használt iteráció kezdeti értéke**. Itt adhatja meg a kezdő értéket, amely downweights az online tanulásban a korai iterációk tanulási arányát. Ez a paraméter megfelel a `learning_offset` scikit-Learn paraméternek.

    + **A frissítések során a rendszer az iterációra alkalmazza a teljesítményt**. Adja meg az iterációk számának mértékét, hogy az online frissítések során a tanulási arányt vezérelni lehessen. Ez a paraméter megfelel a `learning_decay` scikit-Learn paraméternek.

    + **Az adattovábbítások száma**. Adja meg, hogy az algoritmus hány alkalommal fogja átvenni az adatmennyiséget. Ez a paraméter megfelel a `max_iter` scikit-Learn paraméternek.

8. Válassza a **ngrams** létrehozása vagy a ngrams létrehozása a **Lda előtt** lehetőséget, ha a szöveg osztályozása előtt létre szeretné hozni az n-Gram-listát egy kezdeti menetben.

    Ha korábban hozza létre a kezdeti szótárt, később a modell áttekintéséhez használhatja a szótárt. Az eredmények szövegre való leképezése a numerikus indexek helyett általában könnyebben értelmezhető. A szótár mentése azonban hosszabb időt vesz igénybe, és további tárhelyet fog használni.

9. A **ngram-szótár maximális méretének** megadásához adja meg az n-Gram-szótárban létrehozható sorok teljes számát.

    Ez a beállítás a szótár méretének szabályozásához hasznos. Ha azonban a bemeneti ngrams száma meghaladja ezt a méretet, ütközések merülhetnek fel.

10. A folyamat elküldése. A LDA modul a Bayes-tétel használatával határozza meg, hogy az egyes szavakat milyen témakörökhöz lehet társítani. A szavak nem kizárólag egyetlen témakörhöz vagy csoporthoz vannak társítva. Ehelyett minden n-Gram megtanulta, hogy a felderített osztályok bármelyikéhez társítva van-e.

## <a name="results"></a>Results (Eredmények)

A modul két kimenettel rendelkezik:

+ **Átalakított adatkészlet**: Ez a kimenet tartalmazza a bemeneti szöveget, a megadott számú felderített kategóriát, valamint az egyes kategóriákhoz tartozó egyes szöveges példák pontszámait.

+ **Szolgáltatás témakörének mátrixa**: a bal szélső oszlop tartalmazza a kinyert szöveg funkciót. Az egyes kategóriák egyik oszlopa tartalmazza az adott szolgáltatáshoz tartozó pontszámot.


### <a name="lda-transformation"></a>LDA-transzformáció

Ez a modul a *Lda átalakítást* is megjeleníti, amely a Lda alkalmazza az adatkészletre.

Ezt a transzformációt mentheti, és más adatkészletekhez is felhasználhatja. Ez a technika akkor lehet hasznos, ha egy nagy corpuson van betanítva, és szeretné újra felhasználni az együtthatókat vagy kategóriákat.

Az átalakítás újbóli felhasználásához válassza a látens Dirichlet-kiosztási modul jobb oldali paneljén található **adatkészlet regisztrálása** ikont, hogy a modul a modul listájának **adatkészletek** kategóriája alatt maradjon. Ezt a modult a transzformációs modul [alkalmazása](apply-transformation.md) lehetőséggel is összekapcsolhatjuk az átalakítás újrafelhasználásához.

### <a name="refining-an-lda-model-or-results"></a>LDA-modell vagy-eredmények finomítása

Általában nem hozhat létre egyetlen LDA modellt, amely minden igényt kielégít. Még az egy feladathoz tervezett modellnek is sok iterációra lehet szüksége a pontosság javítása érdekében. Javasoljuk, hogy az összes módszert kipróbálva javítsa a modelljét:

+ A modell paramétereinek módosítása
+ Az eredmények megismerése vizualizáció használatával
+ A témával foglalkozó szakértők visszajelzésének beszerzése annak megállapításához, hogy a létrehozott témakörök hasznosak-e

A minőségi mértékek az eredmények kiértékeléséhez is hasznosak lehetnek. A témakör modellezési eredményeinek kiértékeléséhez fontolja meg a következőket:

+ Pontosságát. Hasonló elemek nagyon hasonlóak?
+ Sokszínűséget. A modell különbséget tesz a hasonló elemek között, ha az üzleti probléma szükséges?
+ Méretezhetőség. Működik a szöveges kategóriák széles körére, vagy csak egy keskeny céltartományban?

A LDA alapuló modellek pontossága gyakran javítható a természetes nyelvi feldolgozás használatával a szöveg tisztításához, összefoglalásához és egyszerűsítéséhez, illetve kategorizálásához. Például az alábbi, Azure Machine Learning által támogatott módszerek javítják a besorolás pontosságát:

+ A Word eltávolításának leállítása

+ Kis-és nagybetűk normalizálása

+ Morfológiai elemzéshez vagy eredő

+ Nevesített entitások felismerése

További információ: [szöveg előfeldolgozása](preprocess-text.md).

A tervezőben R-vagy Python-kódtárakat is használhat a szöveg feldolgozásához: [r-szkript](execute-r-script.md)végrehajtása, Python-  [szkript végrehajtása](execute-python-script.md).



## <a name="technical-notes"></a>Technikai megjegyzések

Ez a szakasz megvalósítási részleteket, tippeket és válaszokat tartalmaz a gyakori kérdésekre.

### <a name="implementation-details"></a>Megvalósítás részletei

Alapértelmezés szerint a rendszer az átalakított adatkészlet és a szolgáltatás-témakör mátrix kimeneteit a következő valószínűséggel normalizálja:

+ Az átalakított adatkészlet normalizált, mivel a témakörök feltételes valószínűsége egy dokumentumnak adott. Ebben az esetben az egyes sorok összegének értéke 1.

+ A Feature-topic mátrix normalizált, mivel a szavak egy témakörnek a feltételes valószínűsége. Ebben az esetben az egyes oszlopok összegének értéke 1.

> [!TIP]
> Esetenként előfordulhat, hogy a modul üres témakört ad vissza. Leggyakrabban az algoritmus pszeudo-véletlenszerű inicializálását okozza. Ha ez történik, akkor megpróbálhatja módosítani a kapcsolódó paramétereket. Módosítsa például az N-Gram szótár maximális méretét, illetve a szolgáltatások kivonatolásához használandó bitek számát.

### <a name="lda-and-topic-modeling"></a>LDA és témakör modellezése

A látens Dirichlet-kiosztás gyakran használatos a *tartalom alapú témakör modellezéséhez*, ami alapvetően a nem besorolt szövegből származó tanulási kategóriákat jelenti. A tartalomon alapuló témakör modellezésében a témakör a szavak eloszlása.

Tegyük fel például, hogy egy olyan, az ügyfelek által készített felülvizsgálattal rendelkezik, amely sok terméket tartalmaz. Az ügyfelek által az idő múlásával benyújtott felülvizsgálatok szövege számos kifejezést tartalmaz, amelyek némelyike több témakörben is használatos.

A LDA folyamat által azonosított *témakör* egy adott termékre vonatkozó felülvizsgálatokat is tartalmazhat, vagy a termékek egy csoportját jelöli. Ahhoz, hogy LDA, a témakör önmagában csak egy valószínűségi eloszlást tartalmaz a szavak egy adott halmaza esetében.

A feltételek ritkán kizárólag egyetlen termékre vonatkoznak. Más termékekre is hivatkozhatnak, vagy általános feltételek vonatkoznak rá, amelyek mindenre érvényesek ("nagy", "borzasztó"). Más feltételek lehetnek zaj-szavak. A LDA metódus azonban nem próbálkozik az univerzumban lévő összes szó rögzítésével, illetve a szavak egymáshoz való kapcsolódásának megismerésével, eltekintve a közös előfordulási valószínűségtől. Csak a céltartományban használt szavak csoportosítására használható.

A kiszámított indexek kiszámítása után a távolság-alapú hasonlósági mérték összehasonlítja az egyes sorok szövegét, és meghatározza, hogy két szöveg hasonló-e. Előfordulhat például, hogy úgy találja, hogy a termék több, szorosan korrelált névvel rendelkezik. Vagy előfordulhat, hogy az erősen negatív kifejezések általában egy adott termékhez vannak társítva. A hasonlósági mértéket egyaránt használhatja a kapcsolódó kifejezések azonosításához és javaslatok létrehozásához.

###  <a name="module-parameters"></a>Modul paramétereinek

|Név|Típus|Tartomány|Választható|Alapértelmezett|Leírás|  
|----------|----------|-----------|--------------|-------------|-----------------|  
|Cél oszlop (ok)|Oszlop kijelölése||Kötelező|StringFeature|Cél oszlop neve vagy indexe|  
|A modellhez tartozó témakörök száma|Egész szám|[1; 1000]|Kötelező|5|A dokumentumok terjesztésének modellezése N témákkal.|  
|N – gramm|Egész szám|[1; 10]|Kötelező|2|A kivonatolás során generált N-gramm sorrend.|  
|Normalizálni|Logikai|Igaz vagy hamis|Kötelező|true|A kimenet normalizálása valószínűségek szerint.  Az átalakított adatkészlet a következő lesz: P (témakör&#124;dokumentum), és a szolgáltatás témakörének mátrixa a P (Word&#124;témakör) lesz.|  
|Az összes beállítás megjelenítése|Logikai|Igaz vagy hamis|Kötelező|Hamis|A scikit-Learn online LDA kapcsolatos további paramétereket jelenít meg.|  
|Rho paraméter|Float|[0.00001; 1.0]|Akkor érvényes, ha a **minden beállítás megjelenítése** jelölőnégyzet be van jelölve|0,01|Témakör korábbi eloszlása.|  
|Alfa-paraméter|Float|[0.00001; 1.0]|Akkor érvényes, ha a **minden beállítás megjelenítése** jelölőnégyzet be van jelölve|0,01|A dokumentum korábbi eloszlása című témakör.|  
|A dokumentumok becsült száma|Egész szám|[1; int. MaxValue|Akkor érvényes, ha a **minden beállítás megjelenítése** jelölőnégyzet be van jelölve|1000|A dokumentumok becsült száma. A `total_samples` paraméternek felel meg.|  
|A köteg mérete|Egész szám|[1; 1024]|Akkor érvényes, ha a **minden beállítás megjelenítése** jelölőnégyzet be van jelölve|32|A köteg mérete.|  
|A tanulási sebesség frissítési ütemtervében használt iteráció kezdeti értéke|Egész szám|[0; int. MaxValue|Akkor érvényes, ha a **minden beállítás megjelenítése** jelölőnégyzet be van jelölve|0|Kezdeti érték, amely a korai iterációk downweights képzési arányát. A `learning_offset` paraméternek felel meg.|  
|A frissítések során az iterációra alkalmazott teljesítmény|Float|[0.0; 1.0]|Akkor érvényes, ha a **minden beállítás megjelenítése** jelölőnégyzet be van jelölve|0,5|A tanulási arány szabályozása érdekében a rendszer az iterációk számára alkalmazza a szükséges teljesítményt. A `learning_decay` paraméternek felel meg. |  
|Betanítási Ismétlések száma|Egész szám|[1; 1024]|Akkor érvényes, ha a **minden beállítás megjelenítése** jelölőnégyzet be van jelölve|25|A betanítási Ismétlések száma.|  
|Ngrams-szótár összeállítása|Logikai|Igaz vagy hamis|Akkor érvényes, ha a **minden beállítás megjelenítése** jelölőnégyzet *nincs* bejelölve|Igaz|Létrehozza a ngrams szótárát a számítástechnikai LDA előtt. Modellek ellenőrzése és értelmezése hasznos.|  
|Ngram-szótár maximális mérete|Egész szám|[1; int. MaxValue|Akkor érvényes, ha a **ngrams-összeállítási szótár** értéke **true (igaz** )|20000|A ngrams szótár maximális mérete Ha a bemeneti tokenek száma meghaladja ezt a méretet, ütközések merülhetnek fel.|  
|A szolgáltatás-kivonatoláshoz használandó bitek száma.|Egész szám|[1; 31]|Akkor érvényes, ha az **összes beállítás megjelenítése** jelölőnégyzet *nincs bejelölve,* és a **ngrams szótárának létrehozása** **hamis** .|12|A szolgáltatás-kivonatoláshoz használandó bitek száma.| 
|Ngrams-szótár létrehozása a LDA előtt|Logikai|Igaz vagy hamis|Akkor érvényes, ha a **minden beállítás megjelenítése** jelölőnégyzet be van jelölve|Igaz|A LDA előtti ngrams-szótárt hoz létre. Modellek ellenőrzése és értelmezése hasznos.|  
|Ngrams maximális száma a szótárban|Egész szám|[1; int. MaxValue|Akkor érvényes, ha a **minden beállítás megjelenítése** jelölőnégyzet be van jelölve, és a **ngrams fordítási szótár** értéke **true (igaz** ).|20000|A szótár maximális mérete Ha a bemeneti tokenek száma meghaladja ezt a méretet, ütközések merülhetnek fel.|  
|Kivonatoló bitek száma|Egész szám|[1; 31]|Akkor érvényes, ha a **minden beállítás megjelenítése** jelölőnégyzet be van jelölve, és a **ngrams-összeállítás szótárának** beállítása **hamis** .|12|A funkciók kivonatolásakor használandó bitek száma.|   


## <a name="next-steps"></a>Következő lépések

Tekintse [meg a Azure Machine learning elérhető modulok készletét](module-reference.md) . 

A modulokra jellemző hibák listáját a [tervező kivételei és hibakódai](designer-error-codes.md)című témakörben tekintheti meg.
