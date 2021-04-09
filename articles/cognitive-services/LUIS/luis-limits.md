---
title: Korlátok – LUIS
description: Ez a cikk az Azure Cognitive Services Language Understanding (LUIS) ismert korlátait tartalmazza. LUIS több korlátozási területtel rendelkezik. A modell korlátozza a LUIS-ben lévő leképezéseket, entitásokat és szolgáltatásokat. Kvóta korlátai a kulcs típusa alapján. A billentyűzet kombinációja a LUIS webhelyet vezérli.
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 06/04/2020
ms.openlocfilehash: 41423ce34a62dfdbd5b9a60f683a2366a94d1bfd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "97976792"
---
# <a name="limits-for-your-luis-model-and-keys"></a>A LUIS-modell és-kulcsok korlátai
LUIS több korlátozási területtel rendelkezik. Az első a [modell korlátja](#model-limits), amely a Luis szándékait, entitásait és szolgáltatásait vezérli. A második régió a kulcs típusa alapján [korlátozza a kvótákat](#key-limits) . A határértékek harmadik területe a LUIS webhely vezérlésére szolgáló [billentyűkombináció](#keyboard-controls) . A negyedik terület az a [régió](luis-reference-regions.md) , amely a Luis authoring webhelye és a Luis [Endpoint](luis-glossary.md#endpoint) API-k között van.

<a name="model-boundaries"></a>

## <a name="model-limits"></a>Modell korlátai

Ha az alkalmazása meghaladja a LUIS-modell korlátait, érdemes lehet [Luis küldő](luis-concept-enterprise.md#dispatch-tool-and-model) alkalmazást vagy [Luis-tárolót](luis-container-howto.md)használni.

|Terület|Korlát|
|--|:--|
| [Alkalmazás neve][luis-get-started-create-app] | * Alapértelmezett karakter Max |
| Alkalmazások| 500 alkalmazás/Azure authoring Resource |
| [Kötegelt tesztelés][batch-testing]| 10 adatkészlet, 1000 hosszúságú kimondott szöveg/adatkészlet|
| Explicit lista | 50/alkalmazás|
| Külső entitások | Nincs korlát |
| [Leképezések][intents]|500/alkalmazás: 499 egyéni szándék, és a szükséges _none_ szándék.<br>A [küldő-alapú](https://aka.ms/dispatch-tool) alkalmazás megfelelő 500 elküldési forrással rendelkezik.|
| [Entitások listázása](./luis-concept-entity-types.md) | Szülő: 50, gyermek: 20 000 elem. A Canonical neve * alapértelmezett karakter max. A szinonimák értékeinek hossza nem korlátozza a korlátot. |
| [gépi tanulási entitások + szerepkörök](./luis-concept-entity-types.md):<br> összetett<br>egyszerű<br>entitás szerepköre|Legfeljebb 100 szülő entitás vagy 330 entitás, amely a felhasználó által elsőként megjelenő korlátot korlátozza. A szerepkör entitásként számít a korlát szempontjából. Ilyen például egy egyszerű entitást tartalmazó kompozit, amely 2 szerepkörrel rendelkezik: 1 kompozit + 1 egyszerű + 2 szerepkör = 4 az 330 entitások közül.<br>Az alentitások legfeljebb 5 szinten ágyazhatók egymásba, és szintenként legfeljebb 10 gyermek lehet.|
|Modell szolgáltatásként| Egy adott modell funkcióként használható modelljeinek maximális száma 10 modell. Egy adott modellhez funkcióként használt kifejezések listájának maximális száma 10 kifejezéses felsorolás.|
| [Előnézet – dinamikus lista entitásai](./luis-migration-api-v3.md)|2 a ~ 1k/Query előrejelzési végpontra vonatkozó kérelem listája|
| [Minták](luis-concept-patterns.md)|500 minta alkalmazásként.<br>A minta maximális hossza 400 karakter.<br>3 minta. minden entitás/minta<br>Legfeljebb 2 beágyazott opcionális szöveg a mintában|
| [Minta. any](./luis-concept-entity-types.md)|100/alkalmazás, 3 minta. bármely entitás/minta |
| [Kifejezések listája][phrase-list]|500-kifejezések listája. 10 globális kifejezések listája a modellnek köszönhetően a szolgáltatás korlátja. A nem felcserélhető kifejezések listája legfeljebb 5 000 kifejezést tartalmaz. A felcserélhető kifejezések listája legfeljebb 50 000 kifejezést tartalmaz. Az 500 000-mondatok alkalmazásával kapcsolatos teljes mondatok maximális száma.|
| [Előre összeállított entitások](./howto-add-prebuilt-models.md) | korlátlan|
| [Reguláriskifejezés-entitások](./luis-concept-entity-types.md)|20 entitás<br>500 karakter max. /reguláris kifejezési entitás mintája|
| [Szerepkörök](./luis-concept-entity-types.md)|300 szerepkör egy alkalmazásban. 10 szerepkör/entitás|
| [Beszédelem][utterances] | 500 karakter<br><br>Ha ezen a karakternél hosszabb szöveg van, a kiírást a LUIS-be való bevitel előtt kell megadnia, és minden szegmensre külön szándékot kell kapnia. Nyilvánvaló megszakítások is használhatók, például írásjelek és a beszéd hosszú szüneteltetése.|
| [Példák a teljes kifejezésekre][utterances] | 15 000/alkalmazás – a hosszúságú kimondott szöveg száma nem korlátozható a szándék alapján<br><br>Ha több példával kell betanítania az alkalmazást, használja a [küldő](https://github.com/Microsoft/botbuilder-tools/tree/master/packages/Dispatch) modell megközelítését. Az egyes LUIS-alkalmazásokat (más néven alárendelt alkalmazásokat a fölérendelt küldő alkalmazásba) egy vagy több szándékkal kell elképeznie, majd betanítani egy olyan küldő alkalmazást, amely az egyes gyermek LUIS-alkalmazások hosszúságú kimondott szöveg mintákat küld, hogy az előrejelzési kérést a megfelelő alárendelt alkalmazásra irányítsa. |
| [Versions](./luis-concept-app-iteration.md) (Verziók)| 100 verzió/alkalmazás |
| [Verzió neve][luis-how-to-manage-versions] | 128 karakter |

* Az alapértelmezett karakter Max 50 karakter.

<a name="intent-and-entity-naming"></a>

## <a name="name-uniqueness"></a>Név egyedisége

Az objektumok nevének egyedinek kell lennie, ha az azonos szint más objektumaihoz hasonlít.

|Objektumokat|Korlátozások|
|--|--|
|Szándék, entitás|Az összes leképezésnek és az entitás nevének egyedinek kell lennie az alkalmazás egy verziójában.|
|ML entitás-összetevők|Az adott entitáson belül minden gépi tanulási entitás-összetevőnek (gyermek entitásnak) egyedinek kell lennie az azonos szinten lévő összetevőkhöz.|
|Funkciók | Az összes megnevezett funkciónak, például a kifejezések listájának egyedinek kell lennie az alkalmazás egy verzióján belül.|
|Entitásszerepkörök|Az entitás vagy entitás összetevő összes szerepkörének egyedinek kell lennie, ha ugyanazon az entitás szintjén (szülő, gyermek, unoka stb.) vannak.|

## <a name="object-naming"></a>Objektum elnevezése

Ne használja az alábbi karaktereket a következő nevekben.

|Objektum|Karakterek kizárása|
|--|--|
|Cél, entitás és szerepkör neve|`:`<br>`$` <br> `&`|
|Verzió neve|`\`<br> `/`<br> `:`<br> `?`<br> `&`<br> `=`<br> `*`<br> `+`<br> `(`<br> `)`<br> `%`<br> `@`<br> `$`<br> `~`<br> `!`<br> `#`|

## <a name="resource-usage-and-limits"></a>Erőforrás-használat és-korlátok

A nyelv értelmezése külön erőforrásokkal, egy típussal és egy típussal kérdezi le az előrejelzési végpontot. Ha többet szeretne megtudni a főbb típusok közötti különbségekről, olvassa el az [előrejelzési végpont kulcsainak készítése a Luis-ben](luis-how-to-azure-subscription.md)című témakört.

<a name="key-limits"></a>

### <a name="authoring-resource-limits"></a>Erőforrás-korlátok létrehozása

Használja a _típust_, `LUIS.Authoring` Ha a Azure Portal erőforrásait szűri. LUIS korlátozza az Azure authoring Resource 500 alkalmazásait.

|Erőforrás létrehozása|Szerzői TPS|
|--|--|
|Kezdő|1 millió/hónap, 5/másodperc|
|F0 – ingyenes réteg |1 millió/hónap, 5/másodperc|

* TPS = tranzakció/másodperc

[További információ a díjszabásról.][pricing]

### <a name="query-prediction-resource-limits"></a>Lekérdezés-előrejelzési erőforrások korlátai

Használja a _típust_, `LUIS` Ha a Azure Portal erőforrásait szűri. A-futtatókörnyezetben használt LUIS lekérdezés-előrejelzési végpont erőforrás csak végponti lekérdezések esetén érvényes.

|Lekérdezés-előrejelzési erőforrás|TPS lekérdezése|
|--|--|
|F0 – ingyenes réteg |10 ezer/hónap, 5/másodperc|
|S0 – standard szint|50/másodperc|

### <a name="sentiment-analysis"></a>Hangulatelemzés

A [hangulati elemzések integrációja](luis-how-to-publish-app.md#enable-sentiment-analysis), amely a fontos információk megadását teszi lehetővé anélkül, hogy más Azure-erőforrásra lenne szükség.

### <a name="speech-integration"></a>Beszédfelismerési integráció

A [beszédfelismerési funkció](../speech-service/how-to-recognize-intents-from-speech-csharp.md) egységenkénti díj 1000 végponti kérést biztosít.

[További információ a díjszabásról.][pricing]

## <a name="keyboard-controls"></a>Billentyűzet vezérlőelem

|Billentyűzet bemenete | Leírás |
|--|--|
|Vezérlő + E|a jogkivonatok és entitások közötti váltás a hosszúságú kimondott szöveg listán|

## <a name="website-sign-in-time-period"></a>Webhely bejelentkezési időtartama

A bejelentkezési hozzáférés **60 perc**. Ezt a hibát a következő időszak után kapja meg. Újra be kell jelentkeznie.

[luis-get-started-create-app]: ./luis-get-started-create-app.md
[batch-testing]: ./luis-concept-test.md#batch-testing
[intents]: ./luis-concept-intent.md
[phrase-list]: ./luis-concept-feature.md
[utterances]: ./luis-concept-utterance.md
[luis-how-to-manage-versions]: ./luis-how-to-manage-versions.md
[pricing]: https://azure.microsoft.com/pricing/details/cognitive-services/language-understanding-intelligent-services/
<!-- TBD: fix this link -->
[speech-to-intent-pricing]: https://azure.microsoft.com/pricing/details/cognitive-services/language-understanding-intelligent-services/
