---
title: Korlátok és határok – QnA Maker
description: A QnA Maker meta-korlátozásokkal rendelkezik a Tudásbázis és a szolgáltatás részeihez. A teszteléshez és közzétételhez fontos, hogy megőrizze a tudásbázist a korlátokon belül.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: reference
ms.date: 11/09/2020
ms.openlocfilehash: 1e57ae537c271e61f0b2d37f5320cb177b04802b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "98164872"
---
# <a name="qna-maker-knowledge-base-limits-and-boundaries"></a>QnA Maker Tudásbázis korlátai és határai

Az alább megadott QnA Maker korlátok az [Azure Cognitive Search díjszabási szintjei](../../search/search-limits-quotas-capacity.md) és a [QnA Maker díjszabási szintjeinek](https://azure.microsoft.com/pricing/details/cognitive-services/qna-maker/)kombinációja. Ismernie kell mindkét korlátot, hogy tisztában legyen azzal, hogy hány tudásbázist hozhat létre erőforrás alapján, és hogy mekkora az egyes tudásbázisok mérete.

## <a name="knowledge-bases"></a>Tudásbázisok

A tudásbázisok maximális száma az [Azure Cognitive Search szintű korlátokon](../../search/search-limits-quotas-capacity.md)alapul.

|**Azure Cognitive Search-szintű** | **Ingyenes** | **Basic** |**S1** | **S2**| **S3** |**S3 HD**|
|---|---|---|---|---|---|----|
|A közzétett tudásbázisok maximális száma engedélyezett|2|14|49|199|199|2 999|

 Ha például a réteg 15 engedélyezett indextel rendelkezik, a közzétett Tudásbázisban 14 tudásbázist (1 indexet) tehet közzé. A tizenötödik index a `testkb` szerzői és tesztelési célú összes tudásbázishoz használatos.

## <a name="extraction-limits"></a>Kinyerési korlátok

### <a name="file-naming-constraints"></a>Fájl elnevezési korlátozásai

A fájlnevek nem tartalmazhatják a következő karaktereket:

|Ne használja a karaktert|
|--|
|Szimpla idézőjel `'`|
|Idézőjel `"`|

### <a name="maximum-file-size"></a>Maximális fájlméret

|Formátum|Maximális fájlméret (MB)|
|--|--|
|`.docx`|10|
|`.pdf`|25|
|`.tsv`|10|
|`.txt`|10|
|`.xlsx`|3|

### <a name="maximum-number-of-files"></a>Fájlok maximális száma

A kinyerhető fájlok maximális száma és a maximális fájlméret a **[QnA Maker díjszabási szintjétől](https://azure.microsoft.com/pricing/details/cognitive-services/qna-maker/)** függ.

> [!NOTE]
> QnA Maker felügyelt (előzetes verzió) egy ingyenes szolgáltatás, amely korlátozás nélkül használható a felvehető források számával. Az átviteli sebesség jelenleg 10 tranzakció/másodperc, a felügyeleti API-k és az előrejelzési API-k esetében is korlátozott.

### <a name="maximum-number-of-deep-links-from-url"></a>Az URL-címekről származó mély hivatkozások maximális száma

A QnAs egy URL-lapról való kinyeréséhez feltérképezhető mély hivatkozások maximális száma **20**.

## <a name="metadata-limits"></a>Metaadatok korlátai

A metaadatok szöveges kulcsként jelennek meg: érték pár, például `product:windows 10` . A rendszer kisbetűvel tárolja és hasonlítja össze.

### <a name="by-azure-cognitive-search-pricing-tier"></a>Az Azure Cognitive Search díjszabási szintje szerint

A metaadatok mezőinek maximális száma a Tudásbázisban az Azure- **[Cognitive Search szint korlátain](../../search/search-limits-quotas-capacity.md)** alapul.

|**Azure Cognitive Search-szintű** | **Ingyenes** | **Basic** |**S1** | **S2**| **S3** |**S3 HD**|
|---|---|---|---|---|---|----|
|Metaadat-mezők maximális száma QnA Maker szolgáltatásban (az összes Tudásbázis)|1,000|100 *|1,000|1,000|1,000|1,000|

### <a name="by-name-and-value"></a>Név és érték szerint

A metaadatok nevének és értékének hosszát és elfogadható karaktereit a következő táblázat tartalmazza.

|Elem|Engedélyezett karakterek|Regex-minta egyezése|Karakterek maximális száma|
|--|--|--|--|
|Név (kulcs)|Lehetővé teszi<br>alfanumerikus karakterek (betűk és számjegyek)<br>`_` aláhúzásjellel<br> Nem tartalmazhat szóközt.|`^[a-zA-Z0-9_]+$`|100|
|Érték|Mindent lehetővé tesz, kivéve<br>`:` kettőspont<br>`|` (függőleges cső)<br>Csak egy érték engedélyezett.|`^[^:|]+$`|500|
|||||

## <a name="knowledge-base-content-limits"></a>A Tudásbázis tartalmi korlátai
A Tudásbázis tartalmának általános korlátai:
* A válasz szövegének hossza: 25 000 karakter
* A szóban forgó szöveg hossza: 1 000 karakter
* A metaadatok kulcsának hossza szöveg: 100 karakter
* A metaadatok értékének hossza szöveg: 500 karakter
* A metaadatok nevének támogatott karakterei: ábécék, számjegyek és `_`
* A metaadatok értékének támogatott karaktereinek száma: az összes kivételével `:` , és `|`
* Fájlnév hossza: 200
* Támogatott fájlformátumok: ". TSV", ". pdf", ". txt", ". docx", ". xlsx".
* Alternatív kérdések maximális száma: 300
* A kérdés-válasz párok maximális száma: a kiválasztott **[Azure Cognitive Search szinttől](../../search/search-limits-quotas-capacity.md#document-limits)** függ. A kérdések és válaszok pár egy Azure Cognitive Search indexre vonatkozó dokumentumra mutat.
* URL-cím/HTML-lap: 1 000 000 karakter

## <a name="create-knowledge-base-call-limits"></a>Tudásbázisbeli hívási korlátok létrehozása:
Ezek az egyes Create Tudásbázis-műveletek korlátai. Ehhez kattintson a *Tudásbázis létrehozása* vagy a CreateKnowledgeBase API meghívása elemre.
* Az alternatív kérdések javasolt maximális száma egy adott válaszban: 300
* URL-címek maximális száma: 10
* Fájlok maximális száma: 10
* A QnAs engedélyezett maximális száma (hívás: 1000)

## <a name="update-knowledge-base-call-limits"></a>A Tudásbázis hívási korlátainak frissítése
Ezek a frissítési műveletek korlátainak felelnek meg; a UpdateKnowledgeBase API *mentése és betanítása* vagy meghívása elemre kattintva.
* Az egyes források nevének hossza: 300
* A hozzáadott vagy törölt alternatív kérdések ajánlott maximális száma: 300
* A hozzáadott vagy törölt metaadat-mezők maximális száma: 10
* A frissíthető URL-címek maximális száma: 5
* A QnAs engedélyezett maximális száma (hívás: 1000)

## <a name="next-steps"></a>Következő lépések

Megtudhatja, mikor és hogyan módosíthatja a [szolgáltatás díjszabási szintjeit](How-To/set-up-qnamaker-service-azure.md#upgrade-qna-maker-sku).
