---
title: Nyelvi támogatás – LUIS
titleSuffix: Azure Cognitive Services
description: A LUIS számos funkciót biztosít a szolgáltatáson belül. Egyes funkciók azonban nem érhetők el bizonyos nyelveken. Ellenőrizze, hogy az Önt érdeklő funkciók támogatottak-e a használni kívánt nyelvkultúrában. A LUIS-alkalmazás Culture-specifikus, és a beállítása után nem módosítható.
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 12/09/2019
ms.openlocfilehash: 9363a2dacd91d3868e69e47381eea528e358935c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "97845466"
---
# <a name="language-and-region-support-for-luis"></a>A LUIS nyelv és régió támogatása

A LUIS számos funkciót biztosít a szolgáltatáson belül. Egyes funkciók azonban nem érhetők el bizonyos nyelveken. Ellenőrizze, hogy az Önt érdeklő funkciók támogatottak-e a használni kívánt nyelvkultúrában. A LUIS-alkalmazás Culture-specifikus, és a beállítása után nem módosítható.

## <a name="multi-language-luis-apps"></a>Többnyelvű LUIS-alkalmazások

Ha több nyelvű LUIS-ügyfélalkalmazás, például Csevegőrobot van szüksége, néhány lehetőség közül választhat. Ha a LUIS támogatja az összes nyelvet, minden nyelvhez fejleszt egy LUIS-alkalmazást. Minden LUIS-alkalmazás egyedi alkalmazás-AZONOSÍTÓval és végponti naplóval rendelkezik. Ha nem támogatja a nyelvi megértést a LUIS nyelvhez, a [Translator szolgáltatással](../Translator/translator-info-overview.md) lefordíthatja a teljes változatot egy támogatott nyelvre, elküldheti a kitekintést a Luis-végpontba, és megkaphatja az eredményül kapott pontszámokat.

## <a name="languages-supported"></a>Támogatott nyelvek

LUIS a következő nyelveken ismeri fel a hosszúságú kimondott szöveg:

| Nyelv |Területi beállítás  |  Előre elkészített tartomány | Előre elkészített entitás | Kifejezések listája – javaslatok | **[Szöveges elemzés](../text-analytics/language-support.md)<br>(Hangulat és<br>Kulcsszavak|
|--|--|:--:|:--:|:--:|:--:|
| Arab (előzetes verzió – modern standard arab) |`ar-AR`|-|-|-|-|
| *[kínai](#chinese-support-notes) |`zh-CN` | ✔ | ✔ |✔|-|
| Holland |`nl-NL` |✔|-|-|✔|
| angol (Egyesült Államok) |`en-US` | ✔ | ✔  |✔|✔|
| Francia (Kanada) |`fr-CA` |-|-|-|✔|
| Francia (Franciaország) |`fr-FR` |✔| ✔ |✔ |✔|
| Német |`de-DE` |✔| ✔ |✔ |✔|
| gudzsaráti | `gu-IN`|-|-|-|-|
| Hindi | `hi-IN`|-|✔|-|-|
| Olasz |`it-IT` |✔| ✔ |✔|✔|
| *[japán](#japanese-support-notes) |`ja-JP` |✔| ✔ |✔|Csak a kulcs kifejezése|
| Koreai |`ko-KR` |✔|-|-|Csak a kulcs kifejezése|
| marathi | `mr-IN`|-|-|-|-|
| Portugál (Brazília) |`pt-BR` |✔| ✔ |✔ |nem minden alkultúra|
| Spanyol (Mexikó)|`es-MX` |-|-|✔|✔|
| Spanyol (Spanyolország) |`es-ES` |✔| ✔ |✔|✔|
| tamil | `ta-IN`|-|-|-|-|
| telugu | `te-IN`|-|-|-|-|
| Török | `tr-TR` |✔|✔|-|Csak hangulat|




A nyelvi támogatás az [előre elkészített entitások](luis-reference-prebuilt-entities.md) és az [előre összeépített tartományok](luis-reference-prebuilt-domains.md)esetében változhat.

[!INCLUDE [Chinese language support notes](includes/chinese-language-support-notes.md)]

### <a name="japanese-support-notes"></a>* Japán támogatási megjegyzések

 - Mivel a LUIS nem ad meg szintaktikai elemzést, és nem fogja megérteni a Keigo és az informális Japán közötti különbséget, be kell építenie a különböző formaságokat, mint például az alkalmazásaihoz való betanítási szintet.
     - a でございます nem ugyanaz, mint a です.
     - a です nem ugyanaz, mint a だ.

[!INCLUDE [Text Analytics support notes](includes/text-analytics-support-notes.md)]

### <a name="speech-api-supported-languages"></a>Beszédfelismerési API által támogatott nyelvek
Tekintse meg a beszédfelismerés [által támogatott nyelvek](../speech-service/speech-to-text.md) beszédfelismerési mód nyelveit.

### <a name="bing-spell-check-supported-languages"></a>Bing Spell Check támogatott nyelvek
A támogatott nyelvek és állapotok listáját a Bing Spell Check [támogatott nyelvek](../bing-spell-check/language-support.md) című részben tekintheti meg.

## <a name="rare-or-foreign-words-in-an-application"></a>Ritka vagy idegen szavak egy alkalmazásban
A `en-us` kulturális kultúrában Luis megtanulja a legtöbb angol szót megkülönböztetni, beleértve a szlenget is. A `zh-cn` kulturális kultúrában Luis megtanulja a legtöbb kínai karakter megkülönböztetését. Ha ritka szót vagy karaktert használ a alkalmazásban `en-us` `zh-cn` , és láthatja, hogy Luis úgy tűnik, hogy nem tudja megkülönböztetni ezt a szót vagy karaktert, felveheti ezt a szót vagy karaktert egy [kifejezés-lista szolgáltatásba](luis-how-to-add-features.md). Például az alkalmazás kultúráján kívüli szavak – azaz idegen szavak – egy kifejezés-lista szolgáltatásba kerülnek.

<!--This phrase list should be marked non-interchangeable, to indicate that the set of rare words forms a class that LUIS should learn to recognize, but they are not synonyms or interchangeable with each other.-->

### <a name="hybrid-languages"></a>Hibrid nyelvek
A hibrid nyelvek két kulturális környezetből (például angol és kínai) származó szavakat egyesítenek. A LUIS nem támogatja ezeket a nyelveket, mivel az alkalmazások egyetlen kultúrán alapulnak.

## <a name="tokenization"></a>Jogkivonatok létrehozása
A gépi tanulás elvégzéséhez LUIS a kulturális környezet alapján megszakítja a [jogkivonatokat](luis-glossary.md#token) .

|Nyelv|  minden szóköz vagy speciális karakter | karakter szintje|összetett szavak
|--|:--:|:--:|:--:|
|Arab|✔|||
|Kínai||✔||
|Holland|✔||✔|
|Angol (en-us)|✔ |||
|Francia (fr-FR)|✔|||
|Francia (FR-CA)|✔|||
|Német|✔||✔|
|gudzsaráti|✔|||
|Hindi|✔|||
|Olasz|✔|||
|Japán|||✔
|Koreai||✔||
|marathi|✔|||
|Portugál (Brazília)|✔|||
|Spanyol (es-ES)|✔|||
|Spanyol (es-MX)|✔|||
|tamil|✔|||
|telugu|✔|||
|Török|✔|||


### <a name="custom-tokenizer-versions"></a>Egyéni tokenizer-verziók

A következő kultúrákban egyéni tokenizer verziók szerepelnek:

|Kultúra|Verzió|Cél|
|--|--|--|
|Német<br>`de-de`|1.0.0|A szavakat Tokenizes egy gépi tanuláson alapuló tokenizer, amely az összetett szavakat egyetlen összetevőjére próbálja bontani.<br>Ha a felhasználó Kimondás lép fel, a rendszer a következőre `Ich fahre einen krankenwagen` vált: `Ich fahre einen kranken wagen` . A `kranken` és a `wagen` különböző entitások egymástól függetlenül történő megjelölésének engedélyezése.|
|Német<br>`de-de`|1.0.2|Tokenizes a szavakat a szóközök felosztásával.<br> Ha egy felhasználó `Ich fahre einen krankenwagen` Kimondás lép fel, akkor egyetlen token marad. Így `krankenwagen` egyetlen entitásként van megjelölve. |
|Holland<br>`nl-nl`|1.0.0|A szavakat Tokenizes egy gépi tanuláson alapuló tokenizer, amely az összetett szavakat egyetlen összetevőjére próbálja bontani.<br>Ha a felhasználó Kimondás lép fel, a rendszer a következőre `Ik ga naar de kleuterschool` vált: `Ik ga naar de kleuter school` . A `kleuter` és a `school` különböző entitások egymástól függetlenül történő megjelölésének engedélyezése.|
|Holland<br>`nl-nl`|1.0.1|Tokenizes a szavakat a szóközök felosztásával.<br> Ha egy felhasználó `Ik ga naar de kleuterschool` Kimondás lép fel, akkor egyetlen token marad. Így `kleuterschool` egyetlen entitásként van megjelölve. |


### <a name="migrating-between-tokenizer-versions"></a>Áttelepítés tokenizer-verziók között
<!--
Your first choice is to change the tokenizer version in the app file, then import the version. This action changes how the utterances are tokenized but allows you to keep the same app ID.

Tokenizer JSON for 1.0.0. Notice the property value for  `tokenizerVersion`.

```JSON
{
    "luis_schema_version": "3.2.0",
    "versionId": "0.1",
    "name": "german_app_1.0.0",
    "desc": "",
    "culture": "de-de",
    "tokenizerVersion": "1.0.0",
    "intents": [
        {
            "name": "i1"
        },
        {
            "name": "None"
        }
    ],
    "entities": [
        {
            "name": "Fahrzeug",
            "roles": []
        }
    ],
    "composites": [],
    "closedLists": [],
    "patternAnyEntities": [],
    "regex_entities": [],
    "prebuiltEntities": [],
    "model_features": [],
    "regex_features": [],
    "patterns": [],
    "utterances": [
        {
            "text": "ich fahre einen krankenwagen",
            "intent": "i1",
            "entities": [
                {
                    "entity": "Fahrzeug",
                    "startPos": 23,
                    "endPos": 27
                }
            ]
        }
    ],
    "settings": []
}
```

Tokenizer JSON for version 1.0.1. Notice the property value for  `tokenizerVersion`.

```JSON
{
    "luis_schema_version": "3.2.0",
    "versionId": "0.1",
    "name": "german_app_1.0.1",
    "desc": "",
    "culture": "de-de",
    "tokenizerVersion": "1.0.1",
    "intents": [
        {
            "name": "i1"
        },
        {
            "name": "None"
        }
    ],
    "entities": [
        {
            "name": "Fahrzeug",
            "roles": []
        }
    ],
    "composites": [],
    "closedLists": [],
    "patternAnyEntities": [],
    "regex_entities": [],
    "prebuiltEntities": [],
    "model_features": [],
    "regex_features": [],
    "patterns": [],
    "utterances": [
        {
            "text": "ich fahre einen krankenwagen",
            "intent": "i1",
            "entities": [
                {
                    "entity": "Fahrzeug",
                    "startPos": 16,
                    "endPos": 27
                }
            ]
        }
    ],
    "settings": []
}
```
-->

A jogkivonatok létrehozása az alkalmazás szintjén történik. A verzió szintű jogkivonatok létrehozása nem támogatott.

[Importálja a fájlt új alkalmazásként](luis-how-to-start-new-app.md)a verzió helyett. Ez a művelet azt jelenti, hogy az új alkalmazáshoz egy másik alkalmazás-azonosító tartozik, de a fájlban megadott tokenizer-verziót használja.