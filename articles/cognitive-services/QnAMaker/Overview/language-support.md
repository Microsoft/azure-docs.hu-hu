---
title: Nyelvi támogatás – QnA Maker
titleSuffix: Azure Cognitive Services
description: A tudásalapú QnA Maker által támogatott kulturális környezetek, természetes nyelvek listája. Ne keverje a nyelveket ugyanabban a Tudásbázisban.
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: reference
ms.date: 11/09/2019
ms.openlocfilehash: 1edd5ffc2578a27a53c7e9a46a4a5f1cf61331ff
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "97605059"
---
# <a name="language-support-for-a-qna-maker-resource-and-knowledge-bases"></a>QnA Maker erőforrások és tudásbázisok nyelvi támogatása

Ez a cikk a QnA Maker erőforrások és tudásbázisok nyelvi támogatási lehetőségeit ismerteti. 

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabil kiadás)](#tab/v1)

A szolgáltatás nyelve akkor van kiválasztva, amikor létrehozza az első tudásbázist az erőforrásban. Az erőforrás összes további tudásbázisának azonos nyelven kell lennie. 

A nyelv meghatározza, hogy az eredmények mennyire fontosak QnA Maker a felhasználói lekérdezésekre adott válaszként. A QnA Maker erőforrás és az erőforráson belüli összes Tudásbázis egyetlen nyelvet támogat. Az egyetlen nyelv szükséges ahhoz, hogy a lekérdezéshez a legjobb eredményt adja.

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker felügyelt (előzetes verzió)](#tab/v2)

A QnA Maker felügyelt lehetőséggel megadhatja a nyelvi beállításokat az egyéni Tudásbázis szintjén. Ez a beállítás csak a szolgáltatás tudásbázisának használatával engedélyezhető. A beállítás után a nyelvi beállítások nem módosíthatók a szolgáltatás esetében. 

Ha a nyelvi beállítások lehetőséget választja, hogy a tudásalapot adja meg, akkor lehetősége van arra, hogy a szolgáltatásban különböző nyelvek ismereteit hozza létre. 

---

## <a name="single-language-per-resource"></a>Egy nyelv/erőforrás

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabil kiadás)](#tab/v1)

A következőket ajánljuk figyelmébe:

* A QnA Maker szolgáltatás és annak összes tudásbázisa csak egy nyelvet támogat.
* A nyelv explicit módon be van állítva a szolgáltatás első tudásbázisának létrehozásakor.
* A nyelv meghatározása a Tudásbázis létrehozásakor hozzáadott fájlokból és URL-címekből történik.
* A nyelv nem módosítható a szolgáltatás bármely más tudásbázisa esetében.
* A nyelvet a Cognitive Search szolgáltatás (ranker #1) és a QnA Maker szolgáltatás (ranker #2) használja a legjobb válasz lekérdezésre való létrehozásához.

# <a name="qnamaker-managed-preview"></a>[QnAMaker által felügyelt (előzetes verzió)](#tab/v2)
![QnA Maker felügyelt nyelvi beállítás](../media/language-support/language-setting-managed.png)

Ha **nem jelöli be a nyelvi beállítás engedélyezése a Tudásbázisban jelölőnégyzetet**, vegye figyelembe a következőket: 
* A QnA Maker szolgáltatás és az összes tudásbázisa csak egy nyelvet támogat.
* A nyelv explicit módon be van állítva a szolgáltatás első tudásbázisának létrehozásakor
* A nyelv meghatározása a Tudásbázis létrehozásakor hozzáadott fájlokból és URL-címekből történik
* A nyelv nem módosítható a szolgáltatás bármely más tudásbázisa esetében.
* A nyelvet a Cognitive Search szolgáltatás (ranker #1) és a QnA Maker szolgáltatás (ranker #2) használja a legjobb válasz lekérdezésre való létrehozásához.

---

## <a name="supporting-multiple-languages-in-one-qna-maker-resource"></a>Több nyelv támogatása egy QnA Maker erőforrásban

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabil kiadás)](#tab/v1)
Ez a funkció jelenleg nem támogatott a jelenleg általánosan elérhető (GA) stabil kiadásban. A funkció teszteléséhez tekintse meg QnA Maker felügyelve. 

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker felügyelt (előzetes verzió)](#tab/v2)
* Amikor létrehozta az első tudásbázist a szolgáltatásban, lehetősége van arra, hogy a nyelvi beállítást egy Tudásbázisban engedélyezze. Jelölje be a jelölőnégyzetet, hogy a különböző nyelvekhez tartozó tudásbázisokat hozzon létre egy szolgáltatáson belül.
* A nyelvi beállítási lehetőség nem módosítható a szolgáltatás esetében az első Tudásbázis létrehozása után.
* Ha az összes tudásbázisra vonatkozóan engedélyezi a nyelvi beállításokat, akkor a szolgáltatáshoz tartozó egyik tesztelési index helyett a Tudásbázis egy tesztelési indexét fogja tartalmazni. 

![QnA Maker felügyelt nyelvi beállítás](../media/language-support/language-setting-managed.png)

---

## <a name="supporting-multiple-languages-in-one-knowledge-base"></a>Több nyelv támogatása egy Tudásbázisban

Ha egy Tudásbázis-rendszer támogatására van szüksége, amely több nyelvet is tartalmaz, a következőket teheti:

* A [Translator szolgáltatással](../../translator/translator-info-overview.md) egyetlen nyelven fordíthatja le a kérdést, mielőtt elküldené a kérdést a Tudásbázisnak. Ez lehetővé teszi, hogy a minőségre koncentráljon, valamint a alternatív kérdések és válaszok minőségére.
* Minden nyelvhez hozzon létre egy QnA Maker erőforrást és egy tudásbázist az adott erőforráson belül. Ez lehetővé teszi, hogy külön alternatív kérdéseket kezeljen, és az egyes nyelvekhez árnyaltabb szövegeket adjon. Ez sokkal nagyobb rugalmasságot biztosít, de sokkal nagyobb karbantartási költségeket igényel, ha a kérdések és válaszok minden nyelven megváltoznak.


## <a name="languages-supported"></a>Támogatott nyelvek

A következő lista a QnA Maker erőforrás által támogatott nyelveket tartalmazza. 

| Nyelv |
|--|
| Arab |
| örmény |
| Bangla |
| Baszk |
| Bolgár |
| Katalán |
| Chinese_Simplified |
| Chinese_Traditional |
| Horvát |
| Cseh |
| Dán |
| Holland |
| Angol |
| Észt |
| Finn |
| Francia |
| Gallego |
| Német |
| Görög |
| gudzsaráti |
| héber |
| Hindi |
| Magyar |
| Izlandi |
| Indonéz |
| Ír |
| Olasz |
| Japán |
| kannada |
| Koreai |
| Lett |
| Litván |
| malajálam |
| Maláj |
| Norvég |
| Lengyel |
| Portugál |
| pandzsábi |
| Román |
| Orosz |
| Serbian_Cyrillic |
| Serbian_Latin |
| Szlovák |
| Szlovén |
| Spanyol |
| Svéd |
| tamil |
| telugu |
| Thai |
| Török |
| Ukrán |
| urdu |
| Vietnámi |

## <a name="query-matching-and-relevance"></a>Lekérdezés megfeleltetése és relevanciája
A QnA Maker az [Azure Cognitive Search Language Analyzers](/rest/api/searchservice/language-support) szolgáltatástól függ az eredmények biztosításához.

Habár az Azure Cognitive Search képességei támogatott nyelveken is elérhetők, QnA Maker rendelkezik egy további, az Azure keresési eredményein felül található rangsorsal. Ebben a Ranger-modellben néhány speciális szemantikai és Word-alapú funkciót használunk a következő nyelveken.

|Nyelvek további rangsorsal|
|--|
|Kínai|
|Cseh|
|Holland|
|Angol|
|Francia|
|Német|
|Magyar|
|Olasz|
|Japán|
|Koreai|
|Lengyel|
|Portugál|
|Spanyol|
|Svéd|

Ez a további rangsorolás a QnA Maker rangsorának belső működése.

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Nyelv kiválasztása](../index.yml)