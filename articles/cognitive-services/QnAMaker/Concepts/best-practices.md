---
title: Ajánlott eljárások – QnA Maker
titlesuffix: Azure Cognitive Services
description: Ajánlott eljárások használatával növelheti a Tudásbázis, és jobb eredményeket nyújt az application/csevegőrobot a végfelhasználók számára.
services: cognitive-services
author: tulasim88
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 03/28/2019
ms.author: tulasim
ms.custom: seodec18
ms.openlocfilehash: 0f94a1fdc01825b5bf78644f84c72e6b031109c0
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "61379207"
---
# <a name="best-practices-of-a-qna-maker-knowledge-base"></a>Ajánlott eljárások a QnA Maker Tudásbázis
A [Tudásbázis fejlesztési életciklus](../Concepts/development-lifecycle-knowledge-base.md) végigvezeti Önt a a KB-os kezelése az elejétől a végéig. Ajánlott eljárások használatával növelheti a Tudásbázis, és jobb eredményeket nyújt az application/csevegőrobot a végfelhasználók számára.

## <a name="extraction"></a>Kinyerés
A QnA Maker szolgáltatást folyamatosan javul a algoritmusokat használnak a QnA-tudásbázisok kinyerése a tartalom és a támogatott fájl-és HTML formátumú bővítését. Kövesse a [irányelvek](../Concepts/data-sources-supported.md) az adatok kinyerése a dokumentum típusa alapján. 

Gyakori kérdéseket tartalmazó oldalak általában az önálló és egyéb információkat a nem összevont kell lennie. Kézikönyvek egyértelmű címsorok és lehetőleg index lapot kell rendelkeznie. 

## <a name="creating-good-questions-and-answers"></a>Jó kérdések és válaszok létrehozásával

### <a name="good-questions"></a>Jó kérdések

A legjobb kérdések egyszerűek. Fontolja meg a kulcs szót vagy kifejezést az összes kérdést, majd hozza létre az adott kulcsszót vagy kifejezést a kérdés. 

Adjon hozzá annyi másodlagos kérdések van szükség, de egyszerű megtartani a módosításokat. További szavakat vagy szinonimákkal, amelyek nem szerepelnek a fő cél a kérdés hozzáadása nem működik a QnA Maker egyezést talál. 

### <a name="good-answers"></a>Helyes válasz

A legjobb válaszokat már az egyszerű, de nem túl egyszerű, például az Igen és a válaszok. Ha a válasz kell hivatkozni a más forrásokból, vagy egy gazdag felhasználói élményt biztosítson adathordozó és a hivatkozások, [címkézés](../how-to/metadata-generateanswer-usage.md) különbséget tenni a várt válasz típusát, majd küldje el a lekérdezést a helyes válasz verzió beszerzéséhez a címkével.

## <a name="chit-chat"></a>Csevegési Chit
A robot, hogy a robot természetes nyelvi, és vonzó, chit csevegési hozzá-kis munkamennyiség. Egyszerűen adja hozzá a 3 előre definiált személyiséghez chit csevegési adatkészleteket a KB-os létrehozásakor, és bármikor módosíthatja őket. Ismerje meg, hogyan [chit csevegési ad hozzá a KB-os](../How-To/chit-chat-knowledge-base.md). 

### <a name="choosing-a-personality"></a>Egy személy kiválasztása
Csevegési Chit 3 előre meghatározott személyiséghez támogatott: 

|Személyiséghez|
|--|
|A Professional|
|A typu Friend|
|A Comic|

A válaszok köre a formális informális és irreverent. Akkor válassza a személy, amely legközelebb eső igazodik a képviselő hangvételét robotjait használni szeretne. Az adatkészlet megtekintése és válasszon, amely a robot alapként szolgál, és azután testre szabhatják a válaszokat. 

### <a name="edit-bot-specific-questions"></a>A robot-specifikus kérdések szerkesztése
Nincsenek robot-specifikus kérdésekre kaphat választ a csevegési chit adatkészlet részét képezik, és általános válaszokat kitöltötte. Ezek a válaszok robot adatait legjobban megfelelően módosítsa. 

Ennek végrehajtása a következő chit csevegési QnA-tudásbázisok pontosabb ajánlott:

* Ki Ön?
* Mire használható?
* Mennyi idős Ön?
* Ki hozta létre?
* Üdvözöljük!
   

## <a name="rankingscoring"></a>Ennek a területnek/pontozási
Győződjön meg arról, amelyek a lehető legjobban kihasználják támogatja a QnA Maker rangsorolási szolgáltatásokat. Ezzel javul a valószínűsége, hogy egy adott felhasználó lekérdezési válasz érkezik a megfelelő választ.

### <a name="choosing-a-threshold"></a>Egy küszöbértéket kiválasztása
Az alapértelmezett küszöbérték, amely megbízhatósági pontszám érték: 50, azonban a KB, szükség szerint módosíthatja. Mivel minden KB különböző, teszteléséhez, és válassza ki a küszöbérték, amely a legjobb megoldás az olyan adatbázisoknál a KB. Tudjon meg többet a [megbízhatósági pontszám](../Concepts/confidence-score.md). 

### <a name="add-alternate-questions"></a>Alternatív kérdések hozzáadása
[Kérdések az eredetitől eltérő](../How-To/edit-knowledge-base.md) felhasználói lekérdezés egyezést valószínűségének növelése. Alternatív kérdések akkor hasznos, ha több módon is ugyanezt a kérdést kérni. Ezek lehetnek a módosítások a mondatok és a word-stílusú.

|Eredeti lekérdezés|Alternatív lekérdezéseket|Módosítás| 
|--|--|--|
|Az ideiglenes elérhető?|Autó park van?|mondatok|
 |szia|Yo<br>Hey van!|a Word-stílus és szleng|

<a name="use-metadata-filters"></a>

### <a name="use-metadata-tags-to-filter-questions-and-answers"></a>Használja a metaadat-címkéket a szűrő kérdések és válaszok

[Metaadatok](../How-To/edit-knowledge-base.md) lehetővé teszi az eredményeket a felhasználói lekérdezés alapján metaadat-címkéket. A Tudásbázis választ a metaadat-kódcímke alapján is eltérnek, akkor is, ha a lekérdezés nem ugyanaz. Például *"hol is található ideiglenes"* egy másik választ is rendelkezhet, ha az éttermi ág helye eltérő –, hogy a metaadatok van *helye: Seattle* és *helye: Redmond*.

### <a name="use-synonyms"></a>A szinonimák használata
Míg néhány támogatás angol nyelven szinonimák, használja kis-és [word-átalakítások](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75fd) szinonimák hozzáadása, amelyek különböző kulcsszavakat. Szinonimák kell adni a QnA Maker szolgáltatás szintű és a szolgáltatás minden tudásbázisok által megosztott.

|Eredeti word|Szinonimák|
|--|--|
|Vásárlás|vásároljon<br>netbanking<br>nettó banki|

### <a name="use-distinct-words-to-differentiate-questions"></a>Önálló szavak használatával tesz különbséget a kérdések
A QnA Maker rangsorolási algoritmust, egy felhasználói lekérdezés egy kérdést a Tudásbázis megfelelő akkor működik a legjobban, ha minden kérdés-címek egy másik szükséges. Ismétlődő kérdések között ugyanazon szó csökkenti annak valószínűsége, hogy a helyes válasz egy adott felhasználó lekérdezés azokat a szavakat az van kiválasztva. 

Előfordulhat például, hogy két külön QnA-tudásbázisok az az alábbi kérdésekre:

|QnA-tudásbázisok|
|--|
|hol található az ideiglenes *helye*|
|hol található a ATM *helye*|

E két QnA-tudásbázisok vannak phrased nagyon hasonló szavakat tartalmaznak, mivel a hasonlóság okozhat számos felhasználói lekérdezések, amelyek például a rendszer phrased nagyon hasonló pontszámok *"hol van a `<x>` helye"*. Ehelyett a lekérdezésekkel például jól megkülönböztethető próbál *"hol található az ideiglenes sok"* és *"hol található a ATM"*, lehetőleg ne szavakat, például a "hely", amely csak a sok kérdések a KB-ban. 

## <a name="collaborate"></a>Együttműködés
A QnA Maker lehetővé teszi a felhasználóknak [együttműködés](../How-to/collaborate-knowledge-base.md) a Tudásbázis. Felhasználók az Azure QnA Maker erőforráscsoport hozzá kell férniük ahhoz, hogy hozzáférhessen a tudásbázisok. Egyes szervezetek érdemes kiszervezik a Tudásbázis szerkesztéséhez és a karbantartás, és továbbra is felhasználhatják az Azure-erőforrásokhoz való hozzáférés védelme. Ez a szerkesztő, jóváhagyó modell végzi el beállítása két azonos [QnA Maker szolgáltatás](../How-to/set-up-qnamaker-service-azure.md) különböző előfizetésekben találhatóak, és ha kiválaszt egy, a Szerkesztés tesztelés ciklusig az újbóli. Tesztelés befejezése után a Tudásbázis-tartalmat a átkerülnek egy [az import-export](../Tutorials/migrate-knowledge-base.md) feldolgozása a QnA Maker szolgáltatás a jóváhagyó, végül pedig a Tudásbázis közzététele, és a végpont frissítéséhez.

## <a name="active-learning"></a>Aktív tanulás

[Aktív tanulás](../How-to/improve-knowledge-base.md) does alternatív kérdések javasolása széles minőségét és a felhasználó-alapú lekérdezések mennyisége, ha a legjobb feladat. Fontos, hogy az ügyfél-alkalmazások felhasználói lekérdezések részt az aktív tanulás visszajelzési ciklus ellenőrzéstől nélkül. Kérdések a QnA Maker Portal használata javasolt, ha is **[javaslatok szűrés](../How-To/improve-knowledge-base.md#add-active-learning-suggestion-to-knowledge-base)** , majd tekintse át és fogadja el vagy elutasíthatja a szólhatnak. 

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Tudásbázis szerkesztése](../How-to/edit-knowledge-base.md)
