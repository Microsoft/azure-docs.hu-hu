---
title: Markdown formátuma – QnA Maker
description: A következő lista azokat a Markdown-formátumokat tartalmazza, amelyeket QnA Maker a válasz szövegében is használhat.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: reference
ms.date: 03/19/2020
ms.openlocfilehash: f49e05578f55a38845acbd4010f928fb17c51606
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "96352269"
---
# <a name="markdown-format-supported-in-qna-maker-answer-text"></a>QnA Maker válasz szövegében támogatott Markdown formátum

QnA Maker a Markdown-ként tárolja a válasz szövegét. A Markdown számos változata létezik. Ennek a hivatkozásnak a használatával biztosíthatja, hogy a rendszer visszaadja a válasz szövegét, és helyesen jelenjen meg.

A Markdown ellenőrzéséhez használja a **[CommonMark](https://commonmark.org/help/tutorial/index.html)** oktatóanyagot. Az oktatóanyag egy **kipróbálási** funkcióval rendelkezik a gyors másolási/beillesztési ellenőrzéshez.

## <a name="when-to-use-rich-text-editing-versus-markdown"></a>Mikor kell használni a Rich Text szerkesztési és a Markdown

A válaszok [gazdag szövegű szerkesztése](How-To/edit-knowledge-base.md#add-an-editorial-qna-set) lehetővé teszi, hogy a szerzőként egy formázási eszköztár használatával gyorsan kiválassza és formázza a szöveget.

A Markdown jobb eszköz, ha automatikusan létre kell hoznia egy tartalmat a CI/CD-folyamat vagy a [Batch-tesztelés](./index.yml)részeként importálandó tudásbázisok létrehozásához.

## <a name="supported-markdown-format"></a>Támogatott Markdown-formátum

A következő lista azokat a Markdown-formátumokat tartalmazza, amelyeket QnA Maker a válasz szövegében is használhat.

|Cél|Formátum|Markdown-példa|Renderelés<br>a csevegési robotban megjelenő módon|
|--|--|--|--|
Egy új sor két mondat között.|`\n\n`|`How can I create a bot with \n\n QnA Maker?`|![új sor formázása két mondat között](./media/qnamaker-concepts-datasources/format-newline.png)|
|A H1-től a H6-ig terjedő fejlécek száma, `#` amely a fejlécet jelöli. 1 `#` a H1.|`\n# text \n## text \n### text \n####text \n#####text` |`## Creating a bot \n ...text.... \n### Important news\n ...text... \n### Related Information\n ....text...`<br><br>`\n# my h1 \n## my h2\n### my h3 \n#### my h4 \n##### my h5`|![formátum Markdown-fejlécekkel](./media/qnamaker-concepts-datasources/format-headers.png)<br>![formátum Markdown fejlécekkel H1 – H5](./media/qnamaker-concepts-datasources/format-h1-h5.png)|
|Dőlt |`*text*`|`How do I create a bot with *QnA Maker*?`|![formázás dőlt betűvel](./media/qnamaker-concepts-datasources/format-italics.png)|
|Erős (félkövér)|`**text**`|`How do I create a bot with **QnA Maker**?`|![formázás erős jelöléssel félkövérre](./media/qnamaker-concepts-datasources/format-strong.png)|
|Hivatkozás URL-címe|`[text](https://www.my.com)`|`How do I create a bot with [QnA Maker](https://www.qnamaker.ai)?`|![URL-cím (hiperhivatkozás) formátuma](./media/qnamaker-concepts-datasources/format-url.png)|
|* Nyilvános rendszerkép URL-címe|`![text](https://www.my.com/image.png)`|`How can I create a bot with ![QnAMaker](https://review.docs.microsoft.com/azure/cognitive-services/qnamaker/media/qnamaker-how-to-key-management/qnamaker-resource-list.png)`|![nyilvános rendszerkép URL-címének formátuma ](./media/qnamaker-concepts-datasources/format-image-url.png)|
|Áthúzott|`~~text~~`|`some ~~questoins~~ questions need to be asked`|![áthúzás formátuma](./media/qnamaker-concepts-datasources/format-strikethrough.png)|
|Félkövér és dőlt|`***text**_`|`How can I create a _*_QnA Maker_** bot?`|![félkövér és dőlt formázás](./media/qnamaker-concepts-datasources/format-bold-italics.png)|
|Hivatkozás félkövér URL-címe|`[**text**](https://www.my.com)`|`How do I create a bot with [**QnA Maker**](https://www.qnamaker.ai)?`|![félkövér URL-cím formázása](./media/qnamaker-concepts-datasources/format-bold-url.png)|
|Dőlt betűs URL a hivatkozáshoz|`[*text*](https://www.my.com)`|`How do I create a bot with [*QnA Maker*](https://www.qnamaker.ai)?`|![dőlt betűs URL formátuma](./media/qnamaker-concepts-datasources/format-url-italics.png)|
|Escape Markdown szimbólumok|`\*text\*`|`How do I create a bot with \*QnA Maker\*?`|![Escape Markdown-szimbólumok formátuma](./media/qnamaker-concepts-datasources/format-escape-markdown-symbols.png)|
|Rendezett lista|`\n 1. item1 \n 1. item2`|`This is an ordered list: \n 1. List item 1 \n 1. List item 2`<br>Az előző példában a Markdown beépített automatikus számozást használ.<br>`This is an ordered list: \n 1. List item 1 \n 2. List item 2`<br>Az előző példa explicit számozást használ.|![rendezett lista formátuma](./media/qnamaker-concepts-datasources/format-ordered-list.png)|
|Rendezetlen listák|`\n * item1 \n * item2`<br>vagy<br>`\n - item1 \n - item2`|`This is an unordered list: \n * List item 1 \n * List item 2`|![Rendezetlen lista formátuma](./media/qnamaker-concepts-datasources/format-unordered-list.png)|
|Beágyazott listák|`\n * Parent1 \n\t * Child1 \n\t * Child2 \n * Parent2`<br><br>`\n * Parent1 \n\t 1. Child1 \n\t * Child2 \n 1. Parent2`<br><br>A rendezett és rendezetlen listák együtt is ágyazhatók egymásba. A lap a `\t` gyermek elem behúzási szintjét jelzi.|`This is an unordered list: \n * List item 1 \n\t * Child1 \n\t * Child2 \n * List item 2`<br><br>`This is an ordered nested list: \n 1. Parent1 \n\t 1. Child1 \n\t 1. Child2 \n 1. Parent2`|![beágyazott Rendezetlen lista formátuma](./media/qnamaker-concepts-datasources/format-nested-unordered-list.png)<br>![beágyazott rendezett lista formátuma](./media/qnamaker-concepts-datasources/format-nested-ordered-list.png)|

* QnA Maker semmilyen módon nem dolgozza fel a rendszerképet. Ez az ügyfélalkalmazás szerepe a rendszerkép megjelenítéséhez.

Ha a Tudásbázis API-jai segítségével szeretne tartalmat felvenni, és a tartalom/fájl HTML-címkéket tartalmaz, megtarthatja a fájlban lévő HTML-kódot, hogy a címkék megnyitását és zárását a kódolt formátumban konvertálja a rendszer.

| HTML megőrzése  | Ábrázolás az API-kérelemben  | Megjelenítés a KB-ban |
|-----------|---------|-------------------------|
| Yes | \&lt; br \& gt; | &lt;br&gt; |
| Yes | \&lt; H3 \& gt; fejléc \& lt;/H3 \& gt; | &lt;H3 &gt; fejléc &lt; /H3&gt; |

Emellett a CR LF (\r\n) a KB-ban a \n értékre lett konvertálva. Az LF (\n) a következőképpen van megtartva:. Ha a \t vagy a \n escape-sorozatot is el szeretné kerülni, használhat fordított perjelet, például: " \\ \\ r \\ \\ n" és " \\ \\ t"

## <a name="next-steps"></a>Következő lépések

A [Batch tesztelési fájlformátumainak](reference-tsv-format-batch-testing.md)áttekintése.