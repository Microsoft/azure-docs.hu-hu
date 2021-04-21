---
title: Korlátok és határok – QnA Maker
description: QnA Maker a tudásbázis és a szolgáltatás egyes részeire vonatkozó metakorlátokkal rendelkezik. A teszteléshez és a közzétételhez fontos, hogy tudásbázisát a korlátokon belül tartsa.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: reference
ms.date: 11/09/2020
ms.openlocfilehash: ad498575b029f918538909a9b5b2d52c71c1389c
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2021
ms.locfileid: "107816367"
---
# <a name="qna-maker-knowledge-base-limits-and-boundaries"></a>QnA Maker tudásbázis korlátainak és határainak beállítása

QnA Maker alább megadott korlátozások a Azure Cognitive Search [](../../search/search-limits-quotas-capacity.md) tarifacsomagra és a QnA Maker tarifacsomagra vonatkozó korlátok [QnA Maker kombinációja.](https://azure.microsoft.com/pricing/details/cognitive-services/qna-maker/) Mindkét korlátcsoportot meg kell tudni, hogy hány tudásbázist hozhat létre erőforrásonként, és hogy az egyes tudásbázisok mekkora mennyiségben növekedhetnek.

## <a name="knowledge-bases"></a>Tudásbázisok

A tudásbázisok maximális száma az adott Azure Cognitive Search [alapul.](../../search/search-limits-quotas-capacity.md)

|**Azure Cognitive Search szint** | **Ingyenes** | **Basic** |**S1** | **S2**| **S3** |**S3 HD**|
|---|---|---|---|---|---|----|
|A közzétett tudásbázisok engedélyezett maximális száma|2|14|49|199|199|2,999|

 Ha például a szint 15 engedélyezett indexszel rendelkezik, közzétehet 14 tudásbázist (közzétett tudásbázisonként 1 indexet). A 15. indexet használjuk az összes szerzői és tesztelési `testkb` tudásbázishoz.

## <a name="extraction-limits"></a>Kinyerési korlátok

### <a name="file-naming-constraints"></a>Fájlelnevezési korlátozások

A fájlnevek nem tartalmazhatják a következő karaktereket:

|Ne használjon karaktert|
|--|
|Egyszeres idézőjel `'`|
|Dupla idézőjel `"`|

### <a name="maximum-file-size"></a>Maximális fájlméret

|Formátum|Maximális fájlméret (MB)|
|--|--|
|`.docx`|10|
|`.pdf`|25|
|`.tsv`|10|
|`.txt`|10|
|`.xlsx`|3|

### <a name="maximum-number-of-files"></a>Fájlok maximális száma

A kibontható fájlok maximális száma és maximális fájlmérete a tarifacsomag **[QnA Maker függ.](https://azure.microsoft.com/pricing/details/cognitive-services/qna-maker/)**

> [!NOTE]
> QnA Maker (előzetes verzió) egy ingyenes szolgáltatás, amely nem korlátozza a hozzáadható források számát. Az átviteli sebesség jelenleg másodpercenként 10 tranzakcióra van korlátozva mind a felügyeleti API-k, mind az előrejelzési API-k esetében.

### <a name="maximum-number-of-deep-links-from-url"></a>AZ URL-címből származó mélyhivatkozások maximális száma

A QnA-k URL-oldalról való kinyerése érdekében bejárható mélyhivatkozások maximális száma **20.**

## <a name="metadata-limits"></a>Metaadatkorlátok

A metaadatok szövegalapú kulcs:érték párként jelennek meg, `product:windows 10` például: . Kisbetűkben van tárolva és összehasonlítva.

### <a name="by-azure-cognitive-search-pricing-tier"></a>Tarifacsomag Azure Cognitive Search szerint

A metaadatmezők tudásbázisonkénti maximális száma a Azure Cognitive Search **[alapján.](../../search/search-limits-quotas-capacity.md)**

|**Azure Cognitive Search szint** | **Ingyenes** | **Basic** |**S1** | **S2**| **S3** |**S3 HD**|
|---|---|---|---|---|---|----|
|Metaadatmezők maximális száma QnA Maker szolgáltatásonként (az összes kB-nként)|1,000|100*|1,000|1,000|1,000|1,000|

### <a name="by-name-and-value"></a>Név és érték szerint

A metaadatok nevének és értékének hossza és elfogadható karakterei az alábbi táblázatban vannak felsorolva.

|Elem|Engedélyezett karakter|Regex minta egyezése|Karakter maximuma|
|--|--|--|--|
|Név (kulcs)|Lehetővé teszi<br>alfanumerikus karakterek (betűk és számjegyek)<br>`_` (aláhúzás)<br> Nem tartalmazhat szóközöket.|`^[a-zA-Z0-9_]+$`|100|
|Érték|Mindent megenged, kivéve<br>`:` (kettőspont)<br>`|` (függőleges függőleges vonal)<br>Csak egy érték engedélyezett.|`^[^:|]+$`|500|
|||||

## <a name="knowledge-base-content-limits"></a>Tudásbázis tartalomkorlátok
A tudásbázis tartalmának általános korlátai:
* Válaszszöveg hossza: 25 000 karakter
* Kérdés szövegének hossza: 1000 karakter
* A metaadatkulcs szövegének hossza: 100 karakter
* A metaadatérték szövegének hossza: 500 karakter
* A metaadatok nevének támogatott karakterei: ábécé, számjegy és `_`
* Metaadatértékek támogatott karakterei: Az és a `:` kivételével `|`
* Fájlnév hossza: 200
* Támogatott fájlformátumok: ".tsv", ".pdf", ".txt", ".docx", ".xlsx".
* Alternatív kérdések maximális száma: 300
* Kérdés-válasz párok maximális száma: A választott Azure Cognitive Search **[függ.](../../search/search-limits-quotas-capacity.md#document-limits)** A kérdés-válasz párok az indexen Azure Cognitive Search dokumentumra.
* URL/HTML-oldal: 1 millió karakter

## <a name="create-knowledge-base-call-limits"></a>Tudásbázis hívási korlátainak létrehozása:
Ezek az egyes tudásbázis-létrehozási művelet korlátait jelentik; vagyis kattintson a *Tudásbázis létrehozása* elemre, vagy hívja meg a CreateKnowledgeBase API-t.
* Válaszonként javasolt alternatív kérdések maximális száma: 300
* URL-címek maximális száma: 10
* Fájlok maximális száma: 10
* A hívásonként engedélyezett QnA-k maximális száma: 1000

## <a name="update-knowledge-base-call-limits"></a>Tudásbázis hívási korlátainak frissítése
Ezek az egyes frissítési művelet korlátait jelentik; vagyis kattintson a *Save and train (Mentés és betanítás) gombra,* vagy hívja meg az UpdateKnowledgeBase API-t.
* Az egyes forrásnév hossza: 300
* Alternatív kérdések javasolt maximális száma hozzáadva vagy törölve: 300
* A hozzáadott vagy törölt metaadatmezők maximális száma: 10
* Frissíthetők URL-címek maximális száma: 5
* A hívásonként engedélyezett QnA-k maximális száma: 1000

## <a name="add-unstructured-file-limits"></a>Strukturálatlan fájlkorlátok hozzáadása

> [!NOTE]
> * Ha a korlát által lehetővé tesznél nagyobb fájlokat kell használnia, a fájlt kisebb fájlokra bonthatja, mielőtt elküldi őket az API-nak. 

Ezek a tudásbázis létrehozásához vagy a  CreateKnowledgeBase API hívásához használt strukturálatlan fájlok korlátait jelentik:
* Fájl hossza: Az első 32000 karaktert fogjuk kinyerni
* Fájlonként legfeljebb 3 válasz.

## <a name="prebuilt-question-answering-limits"></a>Előre összeállított kérdések megválaszolása – korlátok

> [!NOTE]
> * Ha nagyobb dokumentumokat kell használnia, mint amennyit a korlát lehetővé tesz, a szöveget kisebb szövegtömbökre bonthatja, mielőtt elküldi őket az API-nak. 
> * A dokumentum egyetlen szöveges karakterből áll.  

Ezek a korlátokat jelentik, amikor az előre összeállított API-t használja *a válasz létrehozásához* vagy a GenerateAnswer API hívásához:
* Dokumentumok száma: 5
* Egyetlen dokumentum maximális mérete: 5120 karakter
* Legfeljebb 3 válasz dokumentumonként.

## <a name="next-steps"></a>Következő lépések

Megtudhatja, mikor és hogyan módosíthatja a [szolgáltatási tarifacsomagokat.](How-To/set-up-qnamaker-service-azure.md#upgrade-qna-maker-sku)
