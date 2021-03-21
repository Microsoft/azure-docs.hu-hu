---
title: Jó példa hosszúságú kimondott szöveg – LUIS
description: A beszédelem az a felhasználói bemenet, amelyet az alkalmazásnak értelmeznie kell. Gyűjtse be azokat a kifejezéseket, amelyeket a felhasználók meg fognak adni. Olyan hosszúságú kimondott szöveg adjon meg, amelyek ugyanazt a dolgot jelentik, de a szó hosszában és a Word elhelyezésekor máshogy vannak kiépítve.
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 05/19/2020
ms.openlocfilehash: 46004d81756809958e359c2a0b72c143599c2853
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "101706758"
---
# <a name="understand-what-good-utterances-are-for-your-luis-app"></a>Ismerje meg, hogy a LUIS-alkalmazás milyen jó hosszúságú kimondott szöveg

A **hosszúságú kimondott szöveg** olyan adatokat kapnak a felhasználótól, akiket az alkalmazásnak kell értelmezni. A LUIS betanításához, hogy kinyerje a szándékait és az entitásokat, fontos, hogy az egyes szándékok különböző példáit hosszúságú kimondott szöveg. Az aktív tanulás, illetve az új hosszúságú kimondott szöveg való folyamatos betanítás folyamata elengedhetetlen a gépi tanulási intelligenciához, amelyet a LUIS biztosít.

Gyűjtse össze azokat a hosszúságú kimondott szöveg, amelyeket a felhasználók meg fognak adni. Vegyen fel hosszúságú kimondott szöveg, ami ugyanazt jelenti, de különböző módokon vannak kiépítve:

* Teljes hossz – rövid, közepes és hosszú az ügyfél-alkalmazáshoz
* Szó és kifejezés hossza
* Szó elhelyezése – entitás elején, közepén és végén
* Nyelvtan
* Pluralization
* Eredő
* Főnév és ige választása
* [Írásjelek](luis-reference-application-settings.md#punctuation-normalization) – a helyes, helytelen és nem nyelvtant használó számos fajta

## <a name="how-to-choose-varied-utterances"></a>Változatos hosszúságú kimondott szöveg kiválasztása

Első lépésként vegyen fel [például hosszúságú kimondott szöveg](./luis-how-to-add-entities.md) a Luis-modellbe, és tekintse meg az alábbi alapelveket.

### <a name="utterances-arent-always-well-formed"></a>A hosszúságú kimondott szöveg nem mindig megfelelően formázott

Ez lehet egy mondat, például: "jegy befoglalása Párizsba", vagy egy mondat töredéke, például "foglalás" vagy "Párizs Flight".  A felhasználók gyakran végeznek helyesírási hibákat. Az alkalmazás megtervezése során érdemes megfontolni, hogy az [Bing Spell Check](luis-tutorial-bing-spellcheck.md) használatával javítsa ki a felhasználói adatokat, mielőtt elkezdené a Luis-nek.

Ha nem jelöli be a felhasználó hosszúságú kimondott szöveg, akkor a LUIS-t a hosszúságú kimondott szöveg kell betanítania, amely tartalmazza az elírásokat és a hibákat.

### <a name="use-the-representative-language-of-the-user"></a>A felhasználó reprezentatív nyelvének használata

A hosszúságú kimondott szöveg kiválasztásakor vegye figyelembe, hogy a leggyakoribb kifejezés vagy kifejezés nem megfelelő az ügyfélalkalmazás jellemző felhasználója számára. Előfordulhat, hogy nem rendelkeznek tartományi tapasztalattal. Legyen körültekintő, ha olyan kifejezéseket vagy kifejezéseket használ, amelyeket a felhasználó csak szakértőnek mond.

### <a name="choose-varied-terminology-as-well-as-phrasing"></a>Válassza a különböző terminológiát és a beszédmód

Azt tapasztalhatja, hogy még akkor is, ha a különböző mondatok létrehozásához tett erőfeszítéseket, továbbra is meg kell ismételnie néhány szókincset.

Végezze el az alábbi példát a hosszúságú kimondott szöveg:

|Példák kimondott szövegekre|
|--|
|Hogyan szerezhetek be számítógépet?|
|Hol szerezhetek be számítógépet?|
|Szeretnék beolvasni egy számítógépet, hogyan tudok?|
|Mikor lehet a számítógépem?|

A fő kifejezés itt, a *számítógép*, nem változtatható meg. Használjon olyan alternatívákat, mint az asztali számítógép, a laptop, a munkaállomás vagy akár a gép. A LUIS intelligens módon kikövetkeztetheti a szinonimákat a kontextusból, de ha hosszúságú kimondott szöveg hoz létre a betanításhoz, mindig érdemes megváltoznia.

## <a name="example-utterances-in-each-intent"></a>Példa hosszúságú kimondott szöveg az egyes leképezésekben

Minden leképezésnek legalább 15 hosszúságú kimondott szöveg kell lennie. Ha olyan szándékkal rendelkezik, amely nem rendelkezik például hosszúságú kimondott szöveg, nem fogja tudni betanítani a LUIS-t. Ha a hosszúságú kimondott szöveg egy vagy nagyon kevés példával rendelkezik, akkor a LUIS nem tudja pontosan megjósolni a szándékot.

## <a name="add-small-groups-of-15-utterances-for-each-authoring-iteration"></a>15 hosszúságú kimondott szöveg kisebb csoportjainak hozzáadása minden szerzői iterációhoz

A modell minden egyes iterációjában ne adjon hozzá nagy mennyiségű hosszúságú kimondott szöveg. Adja hozzá a hosszúságú kimondott szöveg a 15. számú mennyiséghez. A [betanítás](luis-how-to-train.md), [Közzététel](luis-how-to-publish-app.md)és [tesztelés](luis-interactive-test.md) .

LUIS olyan hatékony modelleket épít be a hosszúságú kimondott szöveg, amelyeket a LUIS Model szerzője körültekintően választ ki. Túl sok hosszúságú kimondott szöveg hozzáadása nem értékes, mert zavart okoz.

Érdemes kezdeni néhány hosszúságú kimondott szöveg, majd [tekintse át a végpontok hosszúságú kimondott szöveg](luis-how-to-review-endpoint-utterances.md) a megfelelő szándék-előrejelzés és az entitások kinyerése érdekében.

## <a name="utterance-normalization"></a>Kimondás normalizálása

A kizáró normalizálás az a folyamat, amelynek során figyelmen kívül hagyja a különböző típusú szövegeket, például a központozás és a Mellékjelek hatását a képzés és az előrejelzés során.

Alapértelmezés szerint a teljes normalizálás beállításai ki vannak kapcsolva. Ezek a beállítások többek között:

* Word-űrlapok
* Mellékjelek
* Központozási

Ha bekapcsolja a normalizálás beállítást, a **teszt** ablaktáblán a pontszámok, a Batch-tesztek és a végpont-lekérdezések a normalizálás beállítás összes hosszúságú kimondott szöveg változnak.

Ha a LUIS-portálon egy verziót klónozott, a verzió beállításai továbbra is az új klónozott verzióra kerülnek.

A verzió beállításait a LUIS portálon, a **kezelés** szakaszban, az **Alkalmazásbeállítások** lapon, vagy a [frissítési verzió beállításai API](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/versions-update-application-version-settings)-n keresztül állíthatja be. További információ ezekről a normalizálás változásairól a [hivatkozásban](luis-reference-application-settings.md).

### <a name="word-forms"></a>Word-űrlapok

A **Word-űrlapok** normalizálása figyelmen kívül hagyja a gyökéren túlmutató szavak különbségeit.

<a name="utterance-normalization-for-diacritics-and-punctuation"></a>

### <a name="diacritics"></a>Mellékjelek

A Mellékjelek a szövegben lévő jelek vagy jelek, például:

```
İ ı Ş Ğ ş ğ ö ü
```

### <a name="punctuation-marks"></a>Központozás jelei
A **központozás** normalizálása azt jelenti, hogy a modellek betanítása előtt és a végponti lekérdezések megkezdése előtt az írásjelek el lesznek távolítva a hosszúságú kimondott szöveg.

A központozás egy különálló jogkivonat a LUIS-ben. Egy teljes időszakot, amely a végén található pontot és egy teljes időszakot tartalmaz, és nem tartalmaz két különálló hosszúságú kimondott szöveg, és két különböző előrejelzést kaphat.

Ha a központozás nem normalizált, akkor a LUIS alapértelmezés szerint nem hagyja figyelmen kívül a központozás jelét, mert egyes ügyfélalkalmazások fontosnak helyezhetik el ezeket a jeleket. Győződjön meg arról, hogy a példa hosszúságú kimondott szöveg mindkét írásjelet használja, és nincs írásjel, hogy mindkét stílus ugyanazt a relatív pontszámot adja vissza.

Győződjön meg arról, hogy a modell kezeli a központozást a példában szereplő hosszúságú kimondott szöveg (és nem rendelkezik írásjelekkel), vagy azokban a [mintázatokban](luis-concept-patterns.md) , amelyekben a speciális szintaxissal egyszerűbb a központozás figyelmen kívül hagyása: `I am applying for the {Job} position[.]`

Ha a központozás nem rendelkezik konkrét jelentéssel az ügyfélalkalmazás számára, akkor az írásjelek normalizálása révén érdemes [figyelmen kívül hagyni](#utterance-normalization) a központozást.

### <a name="ignoring-words-and-punctuation"></a>Szavak és írásjelek figyelmen kívül hagyása

Ha nem szeretné figyelmen kívül hagyni a konkrét szavakat vagy írásjeleket a mintázatokban, használjon [mintázatot](luis-concept-patterns.md#pattern-syntax) a szögletes zárójelek szintaxisának _figyelmen kívül_ hagyásával `[]` .

<a name="training-utterances"></a>

## <a name="training-with-all-utterances"></a>Képzés minden hosszúságú kimondott szöveg

A képzés általában nem determinisztikus: a Kimondás előrejelzése némileg változhat a verziók és az alkalmazások között.
A nem determinisztikus-képzések eltávolításához frissítse a [Version Settings](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/versions-update-application-version-settings) API-t a `UseAllTrainingData` név/érték párokkal, és használja az összes betanítási adatforrást.

## <a name="testing-utterances"></a>Hosszúságú kimondott szöveg tesztelése

A fejlesztőknek meg kell kezdeniük a LUIS-alkalmazás valós forgalmú tesztelését, ha hosszúságú kimondott szöveg küldenek az [előrejelzési végpont](luis-how-to-azure-subscription.md) URL-címére. Ezek a hosszúságú kimondott szöveg a leképezések és entitások teljesítményének növelésére szolgálnak a [felülvizsgálati hosszúságú kimondott szöveg](luis-how-to-review-endpoint-utterances.md). A LUIS webhely tesztelési paneljén elküldött tesztek nem jutnak el a végponton keresztül, így nem járulnak hozzá az aktív tanuláshoz.

## <a name="review-utterances"></a>Hosszúságú kimondott szöveg áttekintése

A modell kiképzése, közzététele és a [végponti](luis-glossary.md#endpoint) lekérdezések fogadása után [tekintse át a](luis-how-to-review-endpoint-utterances.md) Luis által javasolt hosszúságú kimondott szöveg. LUIS kijelöli a végpontok hosszúságú kimondott szöveg, amelyekben a szándék vagy az entitás számára alacsony pontszám van.

## <a name="best-practices"></a>Ajánlott eljárások

Tekintse át az [ajánlott eljárásokat](luis-concept-best-practices.md) , és alkalmazza őket a szokásos szerzői ciklus részeként.

## <a name="label-for-word-meaning"></a>Felirat a szó jelentéséhez

Ha a Word Choice vagy a Word megállapodás megegyezik, de nem ugyanazt a dolgot jelenti, ne címkézze fel az entitással.

A következő hosszúságú kimondott szöveg a szó `fair` egy homográfia. A helyesírása ugyanaz, de a jelentése eltérő:

|Beszédelem|
|--|
|Milyen megyei vásárok történnek a Seattle területén ezen a nyáron?|
|A Seattle felülvizsgálati vásár jelenlegi minősítése?|

Ha azt szeretné, hogy az esemény entitása az összes eseményt megkeresse, címkézze meg a szót az első szövegben `fair` , de ne a másodikban.


## <a name="next-steps"></a>Következő lépések
A felhasználói hosszúságú kimondott szöveg megismeréséhez lásd: [példa hosszúságú kimondott szöveg hozzáadása](./luis-how-to-add-entities.md) a Luis-alkalmazások betanításához.