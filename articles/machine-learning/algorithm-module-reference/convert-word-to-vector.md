---
title: 'A Word konvertálása vektorba: modul-hivatkozás'
titleSuffix: Azure Machine Learning
description: Ebből a témakörből megtudhatja, hogyan hozhatja ki a három megadott Word2Vec-modellt a szókincs és a hozzá tartozó Word-beágyazások szövegből való kinyeréséhez.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 05/19/2020
ms.openlocfilehash: 5fad3e4862b0c40c9edd00a5b9d47b245e529396
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "91536732"
---
# <a name="convert-word-to-vector-module"></a>A Word átalakítása vektoros modulba

Ez a cikk azt ismerteti, hogyan használható a Word konvertálása vektoros modul Azure Machine Learning Designerben a következő feladatok elvégzéséhez:

- Alkalmazzon különböző Word2Vec-modelleket (Word2Vec, FastText, kesztyűs előképzéses modellt) a beírt szövegre, amelyet bemenetként adott meg.
- Word-beágyazásokkal rendelkező szókincs létrehozása.

Ez a modul a Gensim könyvtárat használja. A Gensim kapcsolatos további információkért tekintse meg a [hivatalos webhelyét](https://radimrehurek.com/gensim/apiref.html), amely oktatóanyagokat és az algoritmusok magyarázatát tartalmazza.

### <a name="more-about-converting-words-to-vectors"></a>További információ a szavak vektorokra konvertálásáról

A szavak vektorokra vagy Word vektorizációt való konvertálása természetes nyelvi feldolgozási (NLP) folyamat. A folyamat nyelvi modellekkel képezi le a szavakat a vektoros területre. A vektoros terület minden szót a valós számok vektora jelöl. Azt is lehetővé teszi, hogy hasonló jelentésekkel rendelkező szavak hasonló ábrázolásokkal rendelkezzenek.

A Word-beágyazások kezdeti bemenetként használhatók az NLP alsóbb rétegbeli feladataihoz, például a szöveges besoroláshoz és a hangulat elemzéséhez.

Ebben a modulban három széles körben használt módszert hoztunk létre a különböző Word-beágyazási technológiák között. A két, a Word2Vec és a FastText online képzési modell. A másik egy előre betanított modell, kesztyű-wiki-gigaword-100. 

Online – a betanítási modellek a bemeneti adatokra vannak betanítva. Az előképzett modelleket a rendszer offline állapotba helyezi egy nagyobb szöveges Corpus (például wikipedia, Google Hírek) esetében, amely általában körülbelül 100 000 000 000 szót tartalmaz. A Word beágyazása után a Word vektorizációt állandó marad. Az előképzésen átadott Word-modellek olyan előnyöket biztosítanak, mint például a kevesebb tanítási idő, a jobb Word-vektorok kódolása és a jobb általános teljesítmény.

Íme néhány információ a módszerekről:

+ A Word2Vec az egyik legnépszerűbb módszer a Word-beágyazások egy sekély neurális hálózat használatával történő megismerésére. Az elméletet a PDF-letöltésként elérhető dokumentumban tárgyaljuk: a [Word reprezentációk hatékony becslése a vektoros térben](https://arxiv.org/pdf/1301.3781.pdf). A modul implementációja a [Word2Vec Gensim könyvtárán](https://radimrehurek.com/gensim/models/word2vec.html)alapul.

+ A FastText elméletet a PDF-letöltésként elérhető dokumentum ismerteti: a [Word-vektorok gazdagítása alword-információkkal](https://arxiv.org/pdf/1607.04606.pdf). A modul implementációja a [FastText Gensim könyvtárán](https://radimrehurek.com/gensim/models/fasttext.html)alapul.

+ A kesztyű előre betanított modellje a kesztyű-wiki-gigaword-100. Egy olyan, előképzésen alapuló vektorok gyűjteménye, amelyek egy, a 5 600 000 000-es tokeneket és 400 000 nem megfelelő szókincset tartalmaznak PDF-Letöltés érhető el: [kesztyű: globális vektorok a Word ábrázolásához](https://nlp.stanford.edu/pubs/glove.pdf).

## <a name="how-to-configure-convert-word-to-vector"></a>A Word átalakítása vektorra történő konfigurálásának módja

Ehhez a modulhoz olyan adatkészlet szükséges, amely egy szöveges oszlopot tartalmaz. Az előfeldolgozott szöveg jobb.

1. Adja hozzá a **Word konvertálása vektoros** modult a folyamathoz.

2. A modul bemenete adjon meg egy adatkészletet, amely egy vagy több szöveges oszlopot tartalmaz.

3. A **cél oszlop** esetében csak egy olyan oszlopot válasszon, amely feldolgozni kívánt szöveget tartalmaz.

    Mivel ez a modul szövegből hoz létre egy szókincset, az oszlopok tartalma eltér, ami különböző szókincs-tartalmakhoz vezet. Ezért a modul csak egy cél oszlopot fogad el.

4. A  **Word2Vec stratégia** esetében válasszon a következő, az **előképzésen átadott angol modellből**, a **Gensim Word2Vec** és a **Gensim FastText**.

5. Ha a **Word2Vec stratégia** a **Gensim Word2Vec** vagy a **Gensim FastText**:

    + **Word2Vec betanítási algoritmus** esetén válasszon a **Skip_gram** és a **CBOW** közül. A különbség az [eredeti dokumentumban (PDF)](https://arxiv.org/pdf/1301.3781.pdf)van bevezetve.

        Az alapértelmezett módszer a **Skip_gram**.

    + A **Word beágyazásának hosszához** a Word-vektorok dimenzióját kell megadni. Ez a beállítás megfelel a `size` Gensim paraméterének.

        Az alapértelmezett beágyazási méret 100.

    + A **környezeti ablak méretéhez** határozza meg az előre jelzett szó és az aktuális szó közötti maximális távolságot. Ez a beállítás megfelel a `window` Gensim paraméterének.

        Az alapértelmezett ablakméret 5.

    + Az **időpontok számának** megadásához adja meg az időpontok számát (iteráció) a corpuson. Megfelel a `iter` paraméternek a Gensim.

        Az alapértelmezett alapérték 5.

6. A **szókincs maximális méretéhez** a generált szókincsben szereplő szavak maximális számát határozza meg.

    Ha a maximális méretnél több egyedi szó van, a nem gyakoriak.

    Az alapértelmezett szókincs mérete 10 000.

7. A **szavak minimális száma** mezőben adja meg a minimális számú szót. A modul figyelmen kívül hagyja az összes olyan szót, amelynek a gyakorisága ennél az értéknél kisebb.

    Az alapértelmezett érték 5.

8. A folyamat elküldése.

## <a name="examples"></a>Példák

A modul egy kimenettel rendelkezik:

+ **Beágyazást tartalmazó szókincs**: tartalmazza a generált szókincset, valamint az egyes szavak beágyazását. Az egyik dimenzió egy oszlopot foglal le.

Az alábbi példa bemutatja, hogyan működik a Word konvertálása vektoros modulra. A Word konvertálása vektorra az alapértelmezett beállításokkal az előfeldolgozott wikipedia SP 500-adatkészletre használja.

### <a name="source-dataset"></a>Forrásadatkészlet

Az adatkészlet tartalmaz egy category oszlopot, valamint a wikipedia-ból beolvasott teljes szöveget. Az alábbi táblázat néhány jellemző példát mutat be.

|Szöveg|
|----------|
|NASDAQ 100 Component s p 500 Component Foundation alapító Location City Apple Campus 1 végtelen Loop Street infinite loop Cupertino California Cupertino Kalifornia helyszín ország Egyesült Államok...|
|br NASDAQ 100 NASDAQ 100 összetevő br s p 500 s p 500 Component Industry számítógép Software Foundation br alapítója, Charles geschke br John Warnock hely Adobe Systems...|
|s p 500 s p 500 összetevő-iparág autóipari elődje, General Motors Corporation 1908 2009 utód...|
|s p 500 s p 500 összetevő iparági konglomerátum társasági konglomerátum Foundation alapító Location City Fairfield Connecticut Fairfield Connecticut Location Country USA területén...|
|br s p 500 s p 500 Component Foundation 1903 alapító William s Harley br Arthur Davidson Harley Davidson alapító Arthur Davidson br Walter Davidson br William a Davidson hely...|

### <a name="output-vocabulary-with-embeddings"></a>Kimeneti Szószedet beágyazásokkal

A következő táblázat tartalmazza a modul kimenetét, amely a wikipedia SP 500 adatkészletét bemenetként veszi figyelembe. A bal szélső oszlop a szókincset jelöli. A beágyazási vektort az ugyanabban a sorban található fennmaradó oszlopok értékei jelölik.

|Szókincs|A Dim 0 beágyazása|A Dim 1 beágyazása|A Dim 2 beágyazása|A Dim 3 beágyazása|A 4. Dim beágyazás|A Dim 5 beágyazása|...|A Dim 99 beágyazása|
|-------------|-------------|-------------|-------------|-------------|-------------|-------------|-------------|-------------|
|NASDAQ|– 0,375865|0,609234|0,812797|– 0,002236|0,319071|– 0,591986|...|0,364276
|-összetevő|0,081302|0,40001|0,121803|0,108181|0,043651|– 0,091452|...|0,636587
|s|– 0,34355|– 0,037092|– 0,012167|0,151542|0,601019|0,084501|...|0,149419
|P|– 0,133407|0,073244|0,170396|0,326706|0,213463|– 0,700355|...|0,530901
Alapítvány|– 0,166819|0,10883|– 0,07933|– 0,073753|0,262137|0,045725|...|0,27487
alapítója|– 0,297408|0,493067|0,316709|– 0,031651|0,455416|– 0,284208|...|0,22798
location|– 0,375213|0,461229|0,310698|0,213465|0,200092|0,314288|...|0,14228
city|– 0,460828|0,505516|– 0,074294|– 0,00639|0,116545|0,494368|...|– 0,2403
Apple|0,05779|0,672657|0,597267|– 0,898889|0,099901|0,11833|...|0,4636
Campus|– 0,281835|0,29312|0,106966|– 0,031385|0,100777|– 0,061452|...|0,05978
végtelen|– 0,263074|0,245753|0,07058|– 0,164666|0,162857|– 0,027345|...|– 0,0525
hurok|– 0,391421|0,52366|0,141503|– 0,105423|0,084503|– 0,018424|...|– 0,0521

Ebben a példában a **Word2Vec-stratégia** alapértelmezett **Gensim-Word2Vec** használtuk, és a **betanítási algoritmus** **kihagyása – gramm**. **A Word beágyazás hossza** 100, ezért 100 beágyazási oszlopokat tartalmaz.

## <a name="technical-notes"></a>Technikai megjegyzések

Ez a szakasz tippeket és válaszokat tartalmaz a gyakori kérdésekre.

+ Az online képzés és az előre betanított modell közötti különbség:

    A Word konvertálása vektoros modulba három különböző stratégiát nyújtottunk be: két online képzési modell és egy előre betanított modell. Az online képzési modellek a bemeneti adatkészletet betanítási adatként használják, és a tanítás során szókincset és Word-vektorokat hozhatnak. Az előre betanított modellt már egy sokkal nagyobb szöveg-Corpus, például a wikipedia vagy a Twitter szövege képezi. Az előképzésen alapuló modell tulajdonképpen a Word-vagy beágyazási párok gyűjteménye.  

    A kesztyűt előre betanított modell összefoglalja a bemeneti adatkészletből származó szókincset, és létrehoz egy beágyazási vektort az előképzett modell minden szavához. Online képzés nélkül az előképző modell használata időt takaríthat meg. Jobb teljesítményt nyújt, különösen akkor, ha a bemeneti adatkészlet mérete viszonylag kicsi.

+ Beágyazási méret:

    Általánosságban elmondható, hogy a Word beágyazásának hossza több százra van állítva. Például: 100, 200, 300. A kis beágyazási méret kis vektoros területet jelent, ami a Word beágyazási ütközéseket okozhat.  

    Az előre betanított modellek esetében a Word-beágyazások hossza rögzített. Ebben a példában a kesztyű beágyazásának mérete: wiki-gigaword-100, 100.


## <a name="next-steps"></a>Következő lépések

Tekintse [meg a Azure Machine learning elérhető modulok készletét](module-reference.md) . 

A Designer modulokra jellemző hibák listáját itt tekintheti meg: [Machine learning hibakódok](designer-error-codes.md).
