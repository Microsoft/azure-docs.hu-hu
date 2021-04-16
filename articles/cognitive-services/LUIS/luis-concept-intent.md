---
title: Szándékok és entitások – LUIS
titleSuffix: Azure Cognitive Services
description: Egyetlen szándék egy olyan feladatot vagy műveletet jelent, amit a felhasználó végre szeretne hajtatni. A felhasználó beszédeleme mögött rejlő célt vagy kívánságot jelölik. Definiálja a szándékok egy halmazát, amely a felhasználók által az alkalmazásban végre kívánt műveleteknek felel meg.
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 04/13/2021
ms.openlocfilehash: 8e76e3e7683d43a7a39bc0c168a29016a988c705
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2021
ms.locfileid: "107499410"
---
# <a name="intents-in-your-luis-app"></a>Szándékok a LUIS-alkalmazásban

A szándék azt a feladatot vagy műveletet jelöli, amit a felhasználó végre szeretne hajtatni. Ez a felhasználó kimondott szövegében kifejezett cél vagy [cél.](luis-concept-utterance.md)

Definiálja a szándékok egy halmazát, amely a felhasználók által az alkalmazásban végre kívánt műveleteknek felel meg. Egy utazási alkalmazás például több szándékot is meghatároz:

Utazási alkalmazás szándékai   |   Példák kimondott szövegekre   |
------|------|
 RepülőjegyFoglalás     |   "Foglalj egy repülőjegyet Rioba jövő héten" <br/> "Fly me to Rio on the 24th" <br/> "Szükségem van egy repülőjegyre jövő vasárnap Rio de Janeiróba"    |
 Üdvözlés     |   „Szia” <br/>„Helló” <br/>„Jó reggelt”  |
 Időjárás-ellenőrzés | "Milyen az időjárás Bostonban?" <br/> "Show me the forecast for this weekend" (A hétvégére vonatkozó előrejelzés megjelenítése) |
 Nincsenek         | "Get me a cookie recipe" (Szerezze be a cookie-receptet)<br>"Nyerték a Lakerst?" |

Minden alkalmazáshoz tartozik egy előre meghatározott "[Nincs"](#none-intent)szándék, amely a tartalék szándék.

## <a name="prebuilt-domains-provide-intents"></a>Az előre összeállított tartományok szándékokat biztosítanak
Az Ön által definiált szándékok mellett az egyik előre összeállított tartományból is használhat előre összeállított [szándékokat.](./howto-add-prebuilt-models.md)

## <a name="return-all-intents-scores"></a>Az összes szándék pontszámának visszaadva
Egy kimondott szöveg egyetlen szándékhoz rendelhető hozzá. Amikor a LUIS kimondott szövegeket kap a végponton, alapértelmezés szerint az adott kimondott szöveg legfelső szándékát adja vissza.

Ha a kimondott szöveg összes szándékának pontszámait szeretné lekérdezni, meg kell adnia egy jelzőt az előrejelzési API lekérdezési sztringjéhez.

|Előrejelzési API verziója|Jelölő|
|--|--|
|2. verzió|`verbose=true`|
|V3|`show-all-intents=true`|

## <a name="intent-compared-to-entity"></a>Szándék az entitáshoz képest
A szándék azt a műveletet jelöli, amit az alkalmazásnak el kell vinnie a felhasználó számára, és a teljes kimondott szövegen alapul. Egy kimondott szövegnek csak egy legmagasabb pontszámú szándéka lehet, de több entitása is lehet.

<a name="how-do-intents-relate-to-entities"></a>

Hozzon létre egy szándékot, amikor a felhasználó szándéka elindít egy műveletet az ügyfélalkalmazásban, például a checkweather() függvény hívását.  Ezután hozzon létre entitásokat, amelyek a művelet végrehajtásához szükséges paramétereket képviselik.

|Szándék   | Entitás | Példa kimondott szöveg   |
|------------------|------------------------------|------------------------------|
| Időjárás-ellenőrzés | { "type": "location", "entity": "Seattle" }<br>{ "type": "builtin.datetimeV2.date","entity": "tomorrow","resolution":"2018-05-23" } | Milyen az időjárás a `Seattle` `tomorrow` -ban? |
| Időjárás-ellenőrzés | { "type": "date_range", "entity": "this weekend" } | Show me the forecast for (Az előrejelzés megjelenítése a következőre: `this weekend` |
||||

## <a name="prebuilt-domain-intents"></a>Előre összeállított tartományi szándékok

[Az előre összeállított tartományok](./howto-add-prebuilt-models.md) kimondott szövegekkel biztosítanak szándékokat.

## <a name="none-intent"></a>A None szándék

A **None** szándék létrejön, de szándékosan üresen marad. A **None** szándék kötelező szándék, és nem törölhető vagy nevezhető át. Ebbe töltheti fel a tartományon kívül eső beszédelemeket.

A **None** szándék a tartalék szándék, amely minden alkalmazásban fontos, és az összes kimondott szöveg 10%-ával kell lennie. Arra használható, hogy megtanítsa a LUIS-nak az alkalmazástartományban (a tárgyterületen) nem fontos kimondott szövegeket. Ha nem ad hozzá kimondott szövegeket a **None** szándékhoz, a LUIS a tartományon kívüli kimondott szövegeket kényszeríti ki az egyik tartományi szándékba. Ez eltened az előrejelzési pontszámokat azáltal, hogy helytelen szándékot tanít be a LUIS-nak a kimondott szöveghez.

Ha egy kimondott szöveg None szándékként van előrejelezni, az ügyfélalkalmazás további kérdéseket is feltehet, vagy egy menüben érvényes választási lehetőségekhez irányíthatja a felhasználót.

## <a name="negative-intentions"></a>Negatív szándékok
Ha meg szeretné határozni a negatív és pozitív szándékokat, például "I **want** a car" (Autóra van szükségem) és "I **don't** want a car", létrehozhat két szándékot (egy pozitív és egy negatív), és mindegyikhez hozzáadhat megfelelő kimondott szövegeket. Vagy létrehozhat egy szándékot, és entitásként jelölheti meg a két különböző pozitív és negatív fogalmat.

## <a name="intents-and-patterns"></a>Szándékok és minták

Ha vannak példa kimondott szövegei, amelyek részben vagy teljesen is definiálhatóak reguláris kifejezésként, érdemes lehet a reguláriskifejezés-entitást használni egy [mintával párosítva.](luis-concept-patterns.md) [](luis-concept-entity-types.md#regex-entity)

A reguláriskifejezés-entitások használata garantálja az adatok kinyerését, így a minta megfeleltethető. A mintaegyezés garantálja, hogy a rendszer pontos szándékot ad vissza.

## <a name="intent-balance"></a>Szándékegyenleg
Az alkalmazástartomány szándékai között egyensúlynak kell lennie az egyes szándékok kimondott szövegei között. Ne legyen egy szándék 10 kimondott szövegből, egy másik pedig 500 kimondott szövegből. Ez nem kiegyensúlyozott. Ha ilyen helyzet áll elő, tekintse át az 500 kimondott szövegből áll szándékot, és ellenőrizze, hogy a szándékok közül sokat át lehet-e szervezni egy [mintába.](luis-concept-patterns.md)

A **None** szándék nem szerepel az egyenlegben. Ennek a szándéknak az alkalmazásban található összes kimondott szöveg 10%-át kell tartalmaznia.

## <a name="intent-limits"></a>Szándékkorlátok
Tekintse [át a korlátokat,](luis-limits.md#model-boundaries) hogy megértse, hány szándékot adhat hozzá egy modellhez.

### <a name="if-you-need-more-than-the-maximum-number-of-intents"></a>Ha a szándékok maximális számnál többre van szüksége
Először is gondolja át, hogy a rendszer túl sok szándékot használ-e.

### <a name="can-multiple-intents-be-combined-into-single-intent-with-entities"></a>Több szándék is kombinálható egyetlen szándékba entitásokkal
A túl hasonló szándékok megnehezítik a LUIS számára a közöttük való megkülönböztetést. A szándékokat elég változatosnak kell lennie ahhoz, hogy rögzítse a felhasználó által kért fő feladatokat, de nem kell minden útvonalat rögzítenie a kódban. Például a BookFlight és a FlightCustomerService egy utazási alkalmazás különálló szándékai lehetnek, de a BookInternationalFlight és a BookDomesticFlight is hasonló. Ha a rendszernek meg kell különböztetni őket, használjon entitásokat vagy más logikát a szándékok helyett.

### <a name="dispatcher-model"></a>Dispatcher-modell
További információ a LUIS- és A QnA Maker-alkalmazások és a [diszpécsermodell kombinálásával kapcsolatban.](luis-concept-enterprise.md#when-you-need-to-combine-several-luis-and-qna-maker-apps)

### <a name="request-help-for-apps-with-significant-number-of-intents"></a>Segítség kérése olyan alkalmazásokhoz, amelyekhez jelentős mennyiségű szándékot kell kihozni
Ha a szándékok számának csökkentése vagy a szándékok több alkalmazásra való osztása nem működik, forduljon az ügyfélszolgálathoz. Ha az Azure-előfizetése támogatási szolgáltatásokat is tartalmaz, forduljon az [Azure műszaki támogatási szolgálatához.](https://azure.microsoft.com/support/options/)

## <a name="next-steps"></a>Következő lépések

* További információ az [entitásokkal kapcsolatban,](luis-concept-entity-types.md)amelyek a szándékokhoz kapcsolódó fontos szavak
* Megtudhatja, hogyan [adhat hozzá és kezelhet szándékokat](luis-how-to-add-intents.md) a LUIS-alkalmazásban.
* A szándékkal [kapcsolatos ajánlott eljárások áttekintése](luis-concept-best-practices.md)