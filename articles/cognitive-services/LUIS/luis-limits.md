---
title: Korlátok – LUIS
description: Ez a cikk a luis (LUIS Azure Cognitive Services Language Understanding ismert korlátait tartalmazza. A LUIS több korlátozási területből áll. A modellkorlát vezérli a szándékokat, entitásokat és funkciókat a LUIS-ban. Kvótakorlátok kulcstípus alapján. A LUIS webhelyét a billentyűzetkombináció vezérli.
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 06/04/2020
ms.openlocfilehash: 1f917087eb15d8c77356995299e27dfc1657cb5d
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2021
ms.locfileid: "107497200"
---
# <a name="limits-for-your-luis-model-and-keys"></a>A LUIS-modell és -kulcsok korlátai
A LUIS több korlátozási területből áll. Az első a [modell korlátja,](#model-limits)amely a LUIS szándékait, entitásait és funkcióit szabályozza. A második terület a [kulcstípuson](#key-limits) alapuló kvótakorlátok. A korlátok harmadik területe a [LUIS-webhely](#keyboard-controls) vezérlésére használható billentyűzetkombináció. A negyedik terület [](luis-reference-regions.md) a luis szerzői webhely és a [LUIS](luis-glossary.md#endpoint) végponti API-k közötti világterület-leképezés.

<a name="model-boundaries"></a>

## <a name="model-limits"></a>Modellkorlátok

Ha az alkalmazás meghaladja a LUIS-modell korlátait, fontolja meg egy [LUIS-diszpécseralkalmazás](luis-concept-enterprise.md#dispatch-tool-and-model) vagy egy [LUIS-tároló használatának alkalmazását.](luis-container-howto.md)

|Terület|Korlát|
|--|:--|
| [Alkalmazás neve][luis-get-started-create-app] | *Alapértelmezett karakter maximuma |
| Alkalmazások| 500 alkalmazás Azure szerzői erőforrásonként |
| [Kötegelt tesztelés][batch-testing]| 10 adatkészlet, 1000 kimondott szöveg adatkészletenként|
| Explicit lista | Alkalmazásonként 50|
| Külső entitások | nincsenek korlátok |
| [Leképezések][intents]|Alkalmazásonként 500: 499 egyéni szándék és a szükséges _None_ szándék.<br>[A dispatch-alapú](https://aka.ms/dispatch-tool) alkalmazás megfelelő 500 diszpécserforrással rendelkezik.|
| [Entitások listázása](./luis-concept-entity-types.md) | Szülő: 50, gyermek: 20 000 elem. A canonical name a *default character max. A szinonimaértékek nem vonatkoznak hosszkorlátozásra. |
| [gépi tanulási entitások + szerepkörök:](./luis-concept-entity-types.md)<br> Kompozit<br>Egyszerű<br>entitásszerepk|Legfeljebb 100 szülőentitás vagy 330 entitás lehet, amelyik előbb a felhasználóra van korlátozva. A szerepkör ennek a korlátnak az entitásának számít. Ilyen például egy egyszerű entitással rendelkező összetett megoldás, amelynek 2 szerepköre van: 1 összetett + 1 egyszerű + 2 szerepkör = 4 a 330 entitásból.<br>Az alentitások legfeljebb 5 szintbe ágyazhatóak be, és szintenként legfeljebb 20 gyermek lehet.|
|Modell mint funkció| Egy adott modell jellemzőként használható modellek maximális száma 10 modellre. Egy adott modellhez jellemzőként használt kifejezéslisták maximális száma 10 kifejezéslista.|
| [Előzetes verzió – Dinamikus listaentitások](./luis-migration-api-v3.md)|Lekérdezés-előrejelzési végpontonként 2 lista az ~1k-ről|
| [Minták](luis-concept-patterns.md)|Alkalmazásonként 500 minta.<br>A minta maximális hossza 400 karakter.<br>3 Pattern.anyntities per pattern<br>Legfeljebb 2 beágyazott választható szöveg a mintában|
| [Pattern.any](./luis-concept-entity-types.md)|100 alkalmazásonként, 3 pattern.any entitás mintánként |
| [Kifejezéslista][phrase-list]|500 kifejezéslista. 10 globális kifejezéslista a modell funkciókorlátja miatt. A nem felcserélhető kifejezések listája legfeljebb 5000 kifejezést tartalmazza. A felcserélhető kifejezések listája legfeljebb 50 000 kifejezést tartalmazza. A kifejezések maximális száma alkalmazásonként 500 000 kifejezés.|
| [Előre összeállított entitások](./howto-add-prebuilt-models.md) | nincs korlát|
| [Reguláriskifejezés-entitások](./luis-concept-entity-types.md)|20 entitás<br>Legfeljebb 500 karakter. reguláriskifejezés-entitásmintánként|
| [Szerepkörök](./luis-concept-entity-types.md)|300 szerepkör alkalmazásonként. 10 szerepkör entitásonként|
| [Beszédelem][utterances] | 500 karakter<br><br>Ha a szöveg ennél a karakterkorlátnál hosszabb, a LUIS-nak való bemenet előtt szegmentálni kell a kimondott szöveget, és szegmensenként egyedi szándékválaszokat fog kapni. Kézenfekvő törések, például írásjelek és hosszú szünetek a beszédben.|
| [Kimondott szöveg – példák][utterances] | 15 000 alkalmazásonként – a kimondott szöveg szándékonkénti száma nincs korlátozva<br><br>Ha további példákkal kell betanítenie [](https://github.com/Microsoft/botbuilder-tools/tree/master/packages/Dispatch) az alkalmazást, használjon diszpécsermodell-megközelítést. Az egyes LUIS-alkalmazásokat (más néven gyermekalkalmazásokat a szülő-diszpécseralkalmazásba) egy vagy több szándékkal betaníthatja, majd betanít egy diszpécseralkalmazást, amely az egyes gyermek LUIS-alkalmazások kimondott szövegeit minták alapján irányítja át az előrejelzési kérést a megfelelő gyermekalkalmazáshoz. |
| [Versions](./luis-concept-app-iteration.md) (Verziók)| 100 verzió alkalmazásonként |
| [Verzió neve][luis-how-to-manage-versions] | 128 karakter |

*Az alapértelmezett karakter max. 50 karakter.

<a name="intent-and-entity-naming"></a>

## <a name="name-uniqueness"></a>Név egyedisége

Az objektumok nevének egyedinek kell lennie az azonos szintű többi objektumhoz képest.

|Objektumokat|Korlátozások|
|--|--|
|Szándék, entitás|Az összes szándék- és entitásnévnek egyedinek kell lennie egy alkalmazásverzióban.|
|Ml-entitás összetevői|Minden gépi tanulási entitás összetevőnek (gyermekentitásnak) egyedinek kell lennie az adott entitáson belül az azonos szintű összetevők esetében.|
|Funkciók | Minden elnevezett funkciónak, például a kifejezéslistáknak egyedinek kell lennie az alkalmazás verzióján belül.|
|Entitásszerepkörök|Egy entitás vagy entitásösszetevő minden szerepkörének egyedinek kell lennie, ha ugyanazon az entitásszinten vannak (szülő, gyermek, unoka stb.).|

## <a name="object-naming"></a>Objektumok elnevezése

Ne használja a következő karaktereket a következő nevekben.

|Objektum|Karakterek kizárása|
|--|--|
|Szándék-, entitás- és szerepkörnevek|`:`<br>`$` <br> `&`|
|Verzió neve|`\`<br> `/`<br> `:`<br> `?`<br> `&`<br> `=`<br> `*`<br> `+`<br> `(`<br> `)`<br> `%`<br> `@`<br> `$`<br> `~`<br> `!`<br> `#`|

## <a name="resource-usage-and-limits"></a>Erőforrás-használat és korlátok

A Language Understand külön erőforrásokkal rendelkezik, egy típussal a szerzői és egy típussal az előrejelzési végpont lekérdezéséhez. A kulcstípusok közötti különbségekkel kapcsolatos további információkért lásd: Előrejelzési végpontkulcsok létrehozása és lekérdezése a [LUIS-ban.](luis-how-to-azure-subscription.md)

<a name="key-limits"></a>

### <a name="authoring-resource-limits"></a>Erőforráskorlátok írása

Az _erőforrás-szűréshez_ használja a következő típusú `LUIS.Authoring` erőforrásokat: , Azure Portal. A LUIS azure szerzői erőforrásonként 500 alkalmazást korlátozza.

|Szerzői erőforrás|Szerzői TPS|
|--|--|
|Kezdő|1 millió/hónap, 5/másodperc|
|F0 – Ingyenes szint |1 millió/hónap, 5/másodperc|

* TPS = Tranzakciók másodpercenként

[További információ a díjszabásról.][pricing]

### <a name="query-prediction-resource-limits"></a>Lekérdezés-előrejelzés erőforráskorlátai

Az _erőforrás-szűréshez_ használja a következő típusú `LUIS` erőforrásokat: , Azure Portal. A LUIS lekérdezés-előrejelzési végpont erőforrása, amely a futásidőben használatos, csak végpontlekérdezésekhez érvényes.

|Lekérdezés-előrejelzési erőforrás|TPS lekérdezése|
|--|--|
|F0 – Ingyenes szint |10 ezer/hónap, 5/másodperc|
|S0 – Standard szint|50/másodperc|

### <a name="sentiment-analysis"></a>Hangulatelemzés

[A hangulatelemzés integrációja,](luis-how-to-publish-app.md#enable-sentiment-analysis)amely hangulatadatokat biztosít, anélkül biztosítjuk, hogy egy másik Azure-erőforrásra volna szükség.

### <a name="speech-integration"></a>Beszédintegráció

[A Beszédintegráció](../speech-service/how-to-recognize-intents-from-speech-csharp.md) egységköltségenként 1000 végpontkérést biztosít.

[További információ a díjszabásról.][pricing]

## <a name="keyboard-controls"></a>Billentyűzetvezérlők

|Billentyűzetbemenet | Leírás |
|--|--|
|Control+E|váltás a jogkivonatok és az entitások között a kimondott szöveglistában|

## <a name="website-sign-in-time-period"></a>Webhely bejelentkezési időszaka

A bejelentkezési hozzáférés **60 percig tart.** Ezt az időszakot követően ez a hiba jelenik meg. Újra be kell jelentkeznie.

[luis-get-started-create-app]: ./luis-get-started-create-app.md
[batch-testing]: ./luis-concept-test.md#batch-testing
[intents]: ./luis-concept-intent.md
[phrase-list]: ./luis-concept-feature.md
[utterances]: ./luis-concept-utterance.md
[luis-how-to-manage-versions]: ./luis-how-to-manage-versions.md
[pricing]: https://azure.microsoft.com/pricing/details/cognitive-services/language-understanding-intelligent-services/
<!-- TBD: fix this link -->
[speech-to-intent-pricing]: https://azure.microsoft.com/pricing/details/cognitive-services/language-understanding-intelligent-services/
