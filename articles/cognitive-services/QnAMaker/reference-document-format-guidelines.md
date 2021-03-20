---
title: A dokumentum formátumára vonatkozó irányelvek importálása – QnA Maker
description: Ezeket az irányelveket a dokumentumok importálására használhatja, hogy a lehető legjobb eredményeket kapja a tartalomhoz.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: reference
ms.date: 04/06/2020
ms.openlocfilehash: 15ff2ec296cedc37b086a9ca2d0825fb20b4f05a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "99549541"
---
# <a name="format-guidelines-for-imported-documents-and-urls"></a>Az importált dokumentumokra és URL-címekre vonatkozó irányelvek formázása

Tekintse át ezeket a formázási irányelveket, hogy a lehető legjobb eredményeket kapja a tartalomhoz.

## <a name="formatting-considerations"></a>Formázási megfontolások

Fájl vagy URL importálása után QnA Maker átalakítja és tárolja a tartalmat a [Markdown formátumban](https://en.wikipedia.org/wiki/Markdown). Az átalakítási folyamat új sorokat szúr be a szövegbe, például: `\n\n` . A Markdown formátum ismerete segít megérteni a konvertált tartalmat, és felügyelni a Tudásbázis tartalmát.

Ha közvetlenül a Tudásbázisban adja hozzá vagy szerkeszti a tartalmat, a **Markdown formázás** használatával Rich Text tartalmat hozhat létre, vagy módosíthatja a Markdown formátum tartalmát, amely már szerepel a válaszban. QnA Maker támogatja a Markdown-formátum nagy részét, hogy Rich Text képességeket biztosítson a tartalomhoz. Előfordulhat azonban, hogy az ügyfélalkalmazás, például a csevegési robot nem támogatja ugyanazt a Markdown-formátumot. Fontos, hogy tesztelje az ügyfélalkalmazás válaszait.

Tekintse meg a [tartalomtípusok és példák](./concepts/data-sources-and-content.md#content-types-of-documents-you-can-add-to-a-knowledge-base)teljes listáját.

## <a name="basic-document-formatting"></a>Alapszintű dokumentum formázása

A QnA Maker a fájl szakaszait és alszakaszait és kapcsolatait azonosítja a vizualizációs jelek alapján, például:

* betűméret
* betűstílus
* számozás
* színek

> [!NOTE]
> Jelenleg nem támogatottak a feltöltött dokumentumokból származó képek kinyerése.

### <a name="product-manuals"></a>Termék-kézikönyvek

A manuális útmutató általában egy termékhez társuló segédanyag. Segítségével a felhasználó beállíthatja, használhatja, karbantarthatja és elháríthatja a terméket. Ha a QnA Maker manuálisan dolgozza fel a folyamatokat, a fejléceket és alfejléceket kérdésként és az azt követő tartalomként is kibontja a válaszként. [Itt](https://download.microsoft.com/download/2/9/B/29B20383-302C-4517-A006-B0186F04BE28/surface-pro-4-user-guide-EN.pdf)talál példát.

Az alábbi példa egy, az index oldalt tartalmazó manuális és a hierarchikus tartalom

 ![A tudásbázishoz tartozó termék kézi példája](./media/qnamaker-concepts-datasources/product-manual.png)

> [!NOTE]
> A kinyerés a legjobban olyan kézikönyvek esetében működik, amelyek tartalomjegyzéket és/vagy index oldalt, valamint egy hierarchikus fejlécekkel rendelkező tiszta struktúrát alkotnak.

### <a name="brochures-guidelines-papers-and-other-files"></a>Prospektusok, útmutatók, dokumentumok és egyéb fájlok

Számos más típusú dokumentum is feldolgozható a QA-párok létrehozásához, amennyiben azok tiszta struktúrával és elrendezéssel rendelkeznek. Ezek többek között a következők: prospektusok, irányelvek, jelentések, tanulmányok, tudományos dokumentumok, szabályzatok, könyvek stb. [Itt](https://qnamakerstore.blob.core.windows.net/qnamakerdata/docs/Manage%20Azure%20Blob%20Storage.docx)talál példát.

Az alábbi példa egy félig strukturált doc-ra mutat, index nélkül:

 ![Azure Blob Storage – félig strukturált doc](./media/qnamaker-concepts-datasources/semi-structured-doc.png)

### <a name="structured-qna-document"></a>Strukturált QnA-dokumentum

A DOC-fájlokban strukturált Question-Answers formátuma váltakozó kérdésekből és válaszokból áll, soronként egy kérdéssel, majd az alábbi sorban szereplő választal, az alábbi ábrán látható módon:

```text
Question1

Answer1

Question2

Answer2
```

Az alábbi példa egy strukturált QnA Word-dokumentumra mutat:

 ![Strukturált QnA dokumentum – példa tudásbázisra](./media/qnamaker-concepts-datasources/structured-qna-doc.png)

### <a name="structured-txt-tsv-and-xls-files"></a>Strukturált *txt*-, *TSV* -és *xls* -fájlok

A QnAs strukturált *. txt*, *. TSV* vagy *. xls* kiterjesztésű fájlok formájában is feltölthető QnA Maker a Tudásbázis létrehozásához vagy bővítéséhez.  Ezek lehetnek egyszerű szövegek, vagy RTF-vagy HTML-tartalommal is rendelkezhetnek.

| Kérdés  | Válasz  | Metaadatok (1 kulcs: 1 érték) |
|-----------|---------|-------------------------|
| Question1 | Answer1 | <code>Key1:Value1 &#124; Key2:Value2</code> |
| Question2 | Answer2 |      `Key:Value`           |

A forrásfájl további oszlopai figyelmen kívül lesznek hagyva.

#### <a name="example-of-structured-excel-file"></a>Példa strukturált Excel-fájlra

Az alábbi példa egy strukturált QnA *. xls* fájlt mutat be HTML-tartalommal:

 ![Strukturált QnA Excel-példa egy tudásbázisra](./media/qnamaker-concepts-datasources/structured-qna-xls.png)

#### <a name="example-of-alternate-questions-for-single-answer-in-excel-file"></a>Példa az Excel-fájl egyetlen válaszára vonatkozó alternatív kérdésekre

Az alábbi példa egy strukturált QnA *. xls* fájlt mutat be, amely több alternatív kérdést is biztosít egyetlen válaszhoz:

 ![Példa az Excel-fájl egyetlen válaszára vonatkozó alternatív kérdésekre](./media/qnamaker-concepts-datasources/xls-alternate-question-example.png)

A fájl importálása után a kérdés-válasz pár az alábbi Tudásbázisban látható:

 ![A tudásbázisba importált egyetlen válaszra vonatkozó alternatív kérdések képernyőképe](./media/qnamaker-concepts-datasources/xls-alternate-question-example-after-import.png)

### <a name="structured-data-format-through-import"></a>Strukturált adatformátum importálással

A Tudásbázis importálása lecseréli a meglévő Tudásbázis tartalmát. Az importáláshoz egy strukturált. TSV fájl szükséges, amely az adatforrás adatait tartalmazza. Ez az információ segít QnA Maker csoportosítani a kérdés-válasz párokat, és azokat egy adott adatforráshoz rendelni.

| Kérdés  | Válasz  | Forrás| Metaadatok (1 kulcs: 1 érték) |
|-----------|---------|----|---------------------|
| Question1 | Answer1 | Url1 | <code>Key1:Value1 &#124; Key2:Value2</code> |
| Question2 | Answer2 | Szerkesztői|    `Key:Value`       |

<a href="#formatting-considerations"></a>

### <a name="multi-turn-document-formatting"></a>Több-turn dokumentum formázása

* Fejlécek és alfejlécek használatával jelezheti a hierarchiát. Például a H1 paraméterrel jelezheti, hogy a szülő QnA és a H2 jelzi a QnA, amelyet a rendszer kérni fog. A kisebb fejlécek méretének használata a későbbi hierarchia jelölésére. Ne használjon stílust, színt vagy valamilyen más mechanizmust a dokumentum struktúrájának megjelenítéséhez, QnA Maker nem fogja kibontani a többszörös bekapcsolási utasításokat.
* A fejléc első karakterének tőkésített kell lennie.
* Ne fejezze be a fejlécet kérdőjeltel `?` .

**Minta dokumentumok**:<br>[Surface Pro (docx)](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/multi-turn.docx)<br>[Contoso-előnyök (docx)](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/Multiturn-ContosoBenefits.docx)<br>[Contoso-előnyök (PDF)](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/Multiturn-ContosoBenefits.pdf)

## <a name="faq-urls"></a>Gyakori kérdések URL-címei

A QnA Maker 3 különböző formában támogathatja a gyakori kérdések weblapjait:

* Egyszerű gyakori kérdések oldalai
* GYIK-lapok hivatkozásokkal
* GYIK-lapok a témakörök kezdőlapján

### <a name="plain-faq-pages"></a>Egyszerű gyakori kérdések oldalai

Ez a leggyakoribb GYIK-oldal, amelyben a válaszok azonnal követik egyazon oldal kérdéseit.

Az alábbi példa egy egyszerű GYIK-oldalra mutat:

![A Tudásbázis egyszerű gyakori kérdések oldalának példája](./media/qnamaker-concepts-datasources/plain-faq.png)


### <a name="faq-pages-with-links"></a>GYIK-lapok hivatkozásokkal

Ebben a gyakori kérdések oldalon a kérdések össze vannak összesítve, és az azonos oldal különböző szakaszaiban vagy különböző lapokon található válaszokhoz vannak társítva.

Az alábbi példa egy olyan GYIK-oldalt mutat be, amely az ugyanazon a lapon található hivatkozásokat tartalmaz:

 ![Szakasz hivatkozás – GYIK oldal, példa tudásbázisra](./media/qnamaker-concepts-datasources/sectionlink-faq.png)


### <a name="parent-topics-page-links-to-child-answers-pages"></a>Fölérendelt témakörök lap a gyermek válaszokat tartalmazó lapokra mutató hivatkozások

Az ilyen típusú GYIK olyan témaköröket tartalmaz, ahol az egyes témakörök egy adott kérdésekkel és válaszokkal vannak összekapcsolva egy másik oldalon. QnA Maker feltérképezi az összes csatolt oldalt, hogy kinyerje a megfelelő kérdéseket & választ.

Az alábbi példa egy olyan témakört mutat be, amely a különböző lapokon található gyakori kérdések fejezeteire mutató hivatkozásokat tartalmaz.

 ![Részletes hivatkozás – GYIK oldal, példa a tudásbázisra](./media/qnamaker-concepts-datasources/topics-faq.png)

### <a name="support-urls"></a>Támogatási URL-címek

A QnA Maker képes feldolgozni a részben strukturált támogatási weblapokat, például a webcikkeket, amelyek leírják, hogyan hajthat végre egy adott feladatot, hogyan diagnosztizálhatja és elháríthatja az adott problémát, és hogy mi az ajánlott eljárás az adott folyamathoz. A kinyerés a legjobb olyan tartalmaknál működik, amelyek hierarchikus fejlécekkel rendelkező tiszta struktúrával rendelkeznek.

> [!NOTE]
> A támogatási cikkek kinyerése egy új szolgáltatás, amely korai fázisban van. A legmegfelelőbb az egyszerű lapokhoz, amelyek jól strukturáltak, és nem tartalmaznak összetett fejléceket/élőlábakat.

![QnA Maker támogatja a félig strukturált weblapok kinyerését, ahol a világos struktúra hierarchikus fejlécekkel van megjelenítve](./media/qnamaker-concepts-datasources/support-web-pages-with-heirarchical-structure.png)

## <a name="import-and-export-knowledge-base"></a>Tudásbázis importálása és exportálása

Az exportált tudásbázisokból származó **TSV-és xls-fájlok** csak a QnA Maker portál **Settings (beállítások** ) lapján található fájlok importálásával használhatók. A Tudásbázis létrehozásakor nem használhatók adatforrásként, illetve a **Beállítások** lapon a **+ fájl hozzáadása** vagy a **+ URL** hozzáadása funkció. 

A Tudásbázis ezen **TSV-és XLS-fájlokon** keresztül történő importálásakor a rendszer hozzáadja a QnA-párokat a szerkesztői forráshoz, és nem a QnAs kinyert forrásait az exportált Tudásbázisban. 


## <a name="next-steps"></a>Következő lépések

Tekintse meg a [tartalomtípusok és példák](./concepts/data-sources-and-content.md#content-types-of-documents-you-can-add-to-a-knowledge-base) teljes listáját
