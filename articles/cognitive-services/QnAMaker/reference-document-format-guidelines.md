---
title: A dokumentum formátumára vonatkozó irányelvek importálása – QnA Maker
description: Ezeket az irányelveket a dokumentumok importálására használhatja, hogy a lehető legjobb eredményeket kapja a tartalomhoz.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: reference
ms.date: 04/06/2020
ms.openlocfilehash: 76b33d0c09df87196b8fc1f3cbbb0fd06bd30ce1
ms.sourcegitcommit: eb546f78c31dfa65937b3a1be134fb5f153447d6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/02/2021
ms.locfileid: "99430645"
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

## <a name="product-manuals"></a>Termék-kézikönyvek

A manuális útmutató általában egy termékhez társuló segédanyag. Segítségével a felhasználó beállíthatja, használhatja, karbantarthatja és elháríthatja a terméket. Ha a QnA Maker manuálisan dolgozza fel a folyamatokat, a fejléceket és alfejléceket kérdésként és az azt követő tartalomként is kibontja a válaszként. [Itt](https://download.microsoft.com/download/2/9/B/29B20383-302C-4517-A006-B0186F04BE28/surface-pro-4-user-guide-EN.pdf)talál példát.

Az alábbi példa egy, az index oldalt tartalmazó manuális és a hierarchikus tartalom

 ![A tudásbázishoz tartozó termék kézi példája](./media/qnamaker-concepts-datasources/product-manual.png)

> [!NOTE]
> A kinyerés a legjobban olyan kézikönyvek esetében működik, amelyek tartalomjegyzéket és/vagy index oldalt, valamint egy hierarchikus fejlécekkel rendelkező tiszta struktúrát alkotnak.

## <a name="brochures-guidelines-papers-and-other-files"></a>Prospektusok, útmutatók, dokumentumok és egyéb fájlok

Számos más típusú dokumentum is feldolgozható a QA-párok létrehozásához, amennyiben azok tiszta struktúrával és elrendezéssel rendelkeznek. Ezek többek között a következők: prospektusok, irányelvek, jelentések, tanulmányok, tudományos dokumentumok, szabályzatok, könyvek stb. [Itt](https://qnamakerstore.blob.core.windows.net/qnamakerdata/docs/Manage%20Azure%20Blob%20Storage.docx)talál példát.

Az alábbi példa egy félig strukturált doc-ra mutat, index nélkül:

 ![Azure Blob Storage – félig strukturált doc](./media/qnamaker-concepts-datasources/semi-structured-doc.png)

## <a name="structured-qna-document"></a>Strukturált QnA-dokumentum

A DOC-fájlokban strukturált Question-Answers formátuma váltakozó kérdésekből és válaszokból áll, soronként egy kérdéssel, majd az alábbi sorban szereplő választal, az alábbi ábrán látható módon:

```text
Question1

Answer1

Question2

Answer2
```

Az alábbi példa egy strukturált QnA Word-dokumentumra mutat:

 ![Strukturált QnA dokumentum – példa tudásbázisra](./media/qnamaker-concepts-datasources/structured-qna-doc.png)

## <a name="structured-txt-tsv-and-xls-files"></a>Strukturált *txt*-, *TSV* -és *xls* -fájlok

A QnAs strukturált *. txt*, *. TSV* vagy *. xls* kiterjesztésű fájlok formájában is feltölthető QnA Maker a Tudásbázis létrehozásához vagy bővítéséhez.  Ezek lehetnek egyszerű szövegek, vagy RTF-vagy HTML-tartalommal is rendelkezhetnek.

| Kérdés  | Válasz  | Metaadatok (1 kulcs: 1 érték) |
|-----------|---------|-------------------------|
| Question1 | Answer1 | <code>Key1:Value1 &#124; Key2:Value2</code> |
| Question2 | Answer2 |      `Key:Value`           |

A forrásfájl további oszlopai figyelmen kívül lesznek hagyva.

### <a name="example-of-structured-excel-file"></a>Példa strukturált Excel-fájlra

Az alábbi példa egy strukturált QnA *. xls* fájlt mutat be HTML-tartalommal:

 ![Strukturált QnA Excel-példa egy tudásbázisra](./media/qnamaker-concepts-datasources/structured-qna-xls.png)

### <a name="example-of-alternate-questions-for-single-answer-in-excel-file"></a>Példa az Excel-fájl egyetlen válaszára vonatkozó alternatív kérdésekre

Az alábbi példa egy strukturált QnA *. xls* fájlt mutat be, amely több alternatív kérdést is biztosít egyetlen válaszhoz:

 ![Példa az Excel-fájl egyetlen válaszára vonatkozó alternatív kérdésekre](./media/qnamaker-concepts-datasources/xls-alternate-question-example.png)

A fájl importálása után a kérdés-válasz pár az alábbi Tudásbázisban látható:

 ![A tudásbázisba importált egyetlen válaszra vonatkozó alternatív kérdések képernyőképe](./media/qnamaker-concepts-datasources/xls-alternate-question-example-after-import.png)

## <a name="structured-data-format-through-import"></a>Strukturált adatformátum importálással

A Tudásbázis importálása lecseréli a meglévő Tudásbázis tartalmát. Az importáláshoz egy strukturált. TSV fájl szükséges, amely az adatforrás adatait tartalmazza. Ez az információ segít QnA Maker csoportosítani a kérdés-válasz párokat, és azokat egy adott adatforráshoz rendelni.

| Kérdés  | Válasz  | Forrás| Metaadatok (1 kulcs: 1 érték) |
|-----------|---------|----|---------------------|
| Question1 | Answer1 | Url1 | <code>Key1:Value1 &#124; Key2:Value2</code> |
| Question2 | Answer2 | Szerkesztői|    `Key:Value`       |

<a href="#formatting-considerations"></a>

## <a name="multi-turn-document-formatting"></a>Több-turn dokumentum formázása

* Fejlécek és alfejlécek használatával jelezheti a hierarchiát. Például a H1 paraméterrel jelezheti, hogy a szülő QnA és a H2 jelzi a QnA, amelyet a rendszer kérni fog. A kisebb fejlécek méretének használata a későbbi hierarchia jelölésére. Ne használjon stílust, színt vagy valamilyen más mechanizmust a dokumentum struktúrájának megjelenítéséhez, QnA Maker nem fogja kibontani a többszörös bekapcsolási utasításokat.
* A fejléc első karakterének tőkésített kell lennie.
* Ne fejezze be a fejlécet kérdőjeltel `?` .

**Minta dokumentumok**:<br>[Surface Pro (docx)](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/multi-turn.docx)<br>[Contoso-előnyök (docx)](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/Multiturn-ContosoBenefits.docx)<br>[Contoso-előnyök (PDF)](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/Multiturn-ContosoBenefits.pdf)

## <a name="next-steps"></a>Következő lépések

Tekintse meg a [tartalomtípusok és példák](./concepts/data-sources-and-content.md#content-types-of-documents-you-can-add-to-a-knowledge-base) teljes listáját
