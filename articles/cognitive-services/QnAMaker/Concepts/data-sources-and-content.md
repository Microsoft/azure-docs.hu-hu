---
title: Adatforrások és tartalomtípusok – QnA Maker
description: Megtudhatja, hogyan importálhat kérdés-válasz párokat adatforrásokból és támogatott tartalomtípusokból, amelyek számos szabványos strukturált dokumentumot tartalmaznak, például PDF-, DOCX-és TXT-QnA Maker.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 10/13/2020
ms.openlocfilehash: 0d4d32aba34a97c6a060c999694f66d79933d011
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "99556041"
---
# <a name="importing-from-data-sources"></a>Importálás adatforrásokból

A Tudásbázis a nyilvános URL-címek és fájlok által benyújtott kérdések és válaszok párokból áll.

## <a name="data-source-locations"></a>Adatforrás helyei

A tartalom egy adatforrásból származó tudásbázisba kerül. Az adatforrás helyei **nyilvános URL-címek vagy fájlok**, amelyek nem igényelnek hitelesítést.

A hitelesítéssel védett [SharePoint-fájlok](../how-to/add-sharepoint-datasources.md)kivételt képeznek. A SharePoint-erőforrásoknak fájlokat, nem weblapokat kell tartalmazniuk. Ha az URL-cím egy webes kiterjesztéssel végződik, pl. ASPX, a rendszer nem importálja azt a SharePointból a QnA Makerbe.

## <a name="chit-chat-content"></a>Chit-csevegési tartalom

A Chit-Chat-tartalom készlete teljes tartalom-adatforrásként van felkínálva több nyelven és társalgási stílusban. Ez lehet a bot személyiségének kiindulási pontja, és megtakarítja az időt és a költségeket a semmiből való írás során. Megtudhatja, hogyan adhat hozzá a saját tudásbázishoz a [Chit-Chat tartalmakat](../how-to/chit-chat-knowledge-base.md) .

## <a name="structured-data-format-through-import"></a>Strukturált adatformátum importálással

A Tudásbázis importálása lecseréli a meglévő Tudásbázis tartalmát. Az importáláshoz olyan strukturált `.tsv` fájl szükséges, amely kérdéseket és válaszokat tartalmaz. Ez az információ segít QnA Maker csoportosítani a kérdés-válasz párokat, és azokat egy adott adatforráshoz rendelni.

| Kérdés  | Válasz  | Forrás| Metaadatok (1 kulcs: 1 érték) |
|-----------|---------|----|---------------------|
| Question1 | Answer1 | Url1 | <code>Key1:Value1 &#124; Key2:Value2</code> |
| Question2 | Answer2 | Szerkesztői|    `Key:Value`       |

## <a name="structured-multi-turn-format-through-import"></a>Strukturált többsoros formátum importálással

A többszörös kapcsolású beszélgetéseket fájlformátumban hozhatja létre `.tsv` . A formátum lehetővé teszi a többfordulatos beszélgetések létrehozását a korábbi csevegési naplók elemzésével (más folyamatokkal, nem a QnA Maker használatával), majd a fájl létrehozását az `.tsv` Automation segítségével. Importálja a fájlt a meglévő Tudásbázis cseréjéhez.

> [!div class="mx-imgBorder"]
> ![3 szintű többfunkciós kérdés fogalmi modellje](../media/qnamaker-concepts-knowledgebase/nested-multi-turn.png)

A többszörös bekapcsolás oszlopa a többszörös `.tsv` bekapcsolásra vonatkozik.  Az `.tsv` Excelben látható példa a több bekapcsolású gyermekeket definiáló információk megjelenítéséhez:

```JSON
[
    {"displayOrder":0,"qnaId":2,"displayText":"Level 2 Question A"},
    {"displayOrder":0,"qnaId":3,"displayText":"Level 2 - Question B"}
]
```

A **displayOrder** numerikus, és a **szöveg** olyan szöveg, amely nem tartalmaz Markdown.

> [!div class="mx-imgBorder"]
> ![Példa az Excelben látható, többszörösen elérhető kérdésekre](../media/qnamaker-concepts-knowledgebase/multi-turn-tsv-columns-excel-example.png)

## <a name="export-as-example"></a>Exportálás példaként

Ha nem biztos abban, hogy az QnA-párt képviselje a `.tsv` fájlban:
* Ezt a [letölthető példát használhatja a githubról](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/Structured-multi-turn-format.xlsx?raw=true)
* Vagy hozza létre a párt a QnA Maker portálon, mentse, majd exportálja a tudásbázist egy példaként, hogy miként reprezentálja a párt.

## <a name="content-types-of-documents-you-can-add-to-a-knowledge-base"></a>A tudásbázisba felvehető dokumentumok tartalomtípusai
A tartalomtípusok számos szabványos strukturált dokumentumot tartalmaznak, mint például a PDF, a DOC és a TXT.

### <a name="file-and-url-data-types"></a>Fájl-és URL-adattípusok

Az alábbi táblázat összefoglalja a QnA Maker által támogatott tartalom-és fájlformátumok típusait.

|Source Type (Forrás típusa)|Tartalom típusa| Példák|
|--|--|--|
|URL-cím|Gyakori kérdések<br> (Lapos, szakaszok vagy témakörök kezdőlapja)<br>Támogatási lapok <br> (Egyoldalas útmutató cikkek, hibaelhárítási cikkek stb.)|[Egyszerű gyakori kérdések](../troubleshooting.md) <br>[Gyakori kérdések a hivatkozásokkal kapcsolatban](https://www.microsoft.com/en-us/software-download/faq)<br> [GYIK a témakörök kezdőlapján](https://www.microsoft.com/Licensing/servicecenter/Help/Faq.aspx)<br>[Támogatási cikk](./best-practices.md)|
|PDF/DOC|GYIK<br> Termék kézi,<br> Brosúrák<br> Tanulmány<br> Szórólap-szabályzat,<br> Támogatási útmutató,<br> Strukturált QnA,<br> stb.|**Többszörös kapcsolás nélkül**<br>[Strukturált QnA.docx](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/structured.docx),<br> [Minta termék Manual.pdf](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/product-manual.pdf),<br> [Minta semi-structured.docx](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/semi-structured.docx),<br> [Minta fehér paper.pdf](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/white-paper.pdf),<br><br>**Többszörös kapcsolás**:<br>[Surface Pro (docx)](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/multi-turn.docx)<br>[Contoso-előnyök (docx)](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/Multiturn-ContosoBenefits.docx)<br>[Contoso-előnyök (PDF)](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/Multiturn-ContosoBenefits.pdf)|
|* Excel|Strukturált QnA-fájl<br> (többek között az RTF, a HTML-támogatás)|**Többszörös kapcsolás nélkül**:<br>[Minta QnA FAQ.xls](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/QnA%20Maker%20Sample%20FAQ.xlsx)<br><br>**Többszörös kapcsolás**:<br>[Strukturált egyszerű FAQ.xls](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/Structured-multi-turn-format.xlsx)<br>[Surface laptop FAQ.xls](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/Multiturn-Surface-Pro.xlsx)|
|* TXT/TSV|Strukturált QnA-fájl|[Példa: Chit-Chat. TSV](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/Scenario_Responses_Friendly.tsv)|

Ha hitelesítésre van szüksége az adatforráshoz, vegye figyelembe a következő módszereket a tartalom QnA Makerba való beszerzéséhez:

* Töltse le manuálisan a fájlt, és importálja QnA Maker
* A fájl hozzáadása a hitelesített [SharePoint-helyről](../how-to/add-sharepoint-datasources.md)

### <a name="url-content"></a>URL-tartalom

Két típusú dokumentum importálható QnA Maker URL- **cím** használatával:

* Gyakori kérdések URL-címei
* Támogatási URL-címek

Mindegyik típus egy várt formátumot jelez.

### <a name="file-based-content"></a>Fájl alapú tartalom

A [QnA Maker-portálon](https://www.qnamaker.ai)hozzáadhat fájlokat egy nyilvános forrásból vagy a helyi fájlrendszerből is.

### <a name="content-format-guidelines"></a>Tartalomformátumra vonatkozó útmutatók

További információ a különböző fájlokra vonatkozó [formátumokról](../reference-document-format-guidelines.md) .

## <a name="next-steps"></a>Következő lépések

Megtudhatja, hogyan [szerkesztheti a QnAs](../how-to/edit-knowledge-base.md).
