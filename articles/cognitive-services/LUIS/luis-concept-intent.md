---
title: Szándékok és entitások – LUIS
titleSuffix: Azure Cognitive Services
description: Egyetlen szándék a felhasználó által végrehajtani kívánt feladatot vagy műveletet jelöli. A felhasználó beszédeleme mögött rejlő célt vagy kívánságot jelölik. Adjon meg olyan leképezéseket, amelyek megfelelnek a felhasználók által az alkalmazásban használni kívánt műveleteknek.
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 10/10/2019
ms.openlocfilehash: bf5ab7361c84fb787366c7c361829e52362fe427
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "95025908"
---
# <a name="intents-in-your-luis-app"></a>Szándékok a LUIS-alkalmazásban

A szándék azt a feladatot vagy műveletet jelöli, amelyet a felhasználó végre szeretne hajtani. Ez a felhasználó [teljes](luis-concept-utterance.md)kifejezésében kifejezett cél vagy cél.

Adjon meg olyan leképezéseket, amelyek megfelelnek a felhasználók által az alkalmazásban használni kívánt műveleteknek. Egy utazási alkalmazás például több leképezést is definiál:

Utazási alkalmazások leképezése   |   Példák kimondott szövegekre   |
------|------|
 RepülőjegyFoglalás     |   "Könyv készítése a Rio-ra a jövő héten" <br/> "Fly to Rio on 24" <br/> "A következő vasárnapra van szükségem a Rio de Janeiroban"    |
 Üdvözlés     |   „Szia” <br/>„Helló” <br/>„Jó reggelt”  |
 Időjárás-ellenőrzés | "Mi az időjárás, mint a Boston?" <br/> "A hétvégi előrejelzés megjelenítése" |
 Nincsenek         | "Cookie-recept beszerzése"<br>"A Lakers nyerte?" |

Minden alkalmazás a "[nincs](#none-intent)" előre definiált szándékkal, azaz a tartalék szándékkal van ellátva.

## <a name="prebuilt-domains-provide-intents"></a>Az előre elkészített tartományok biztosítanak leképezéseket
A definiált szándékon kívül az előre [elkészített tartományok](./howto-add-prebuilt-models.md)egyikének előre összekészített leképezéseit is használhatja.

## <a name="return-all-intents-scores"></a>Az összes leképezési pontszám visszaküldése
A teljes hozzárendelést egyetlen szándékhoz rendeli. Ha a LUIS a végpontra vonatkozó kiírást kap, alapértelmezés szerint a rendszer visszaadja az adott kifejezéshez legfelső szándékot.

Ha azt szeretné, hogy a pontszámok a teljes értékre legyenek kiválasztva, megadhat egy jelzőt az előrejelzési API lekérdezési karakterláncában.

|Előrejelzési API verziója|Jelölő|
|--|--|
|2. verzió|`verbose=true`|
|V3|`show-all-intents=true`|

## <a name="intent-compared-to-entity"></a>Az entitáshoz viszonyított szándék
A szándék azt a műveletet jelöli, amelyet az alkalmazásnak el kell végeznie a felhasználó számára, és a teljes Kimondás alapján kell megjelennie. A Kimondás csak egyetlen Top pontozási szándékkal rendelkezhet, de számos entitást tartalmazhat.

<a name="how-do-intents-relate-to-entities"></a>

Hozzon létre egy szándékot, ha a felhasználó _szándéka_ egy műveletet indít el az ügyfélalkalmazás, például a checkweather () függvény hívása. Ezután hozzon létre entitásokat a művelet végrehajtásához szükséges paraméterek ábrázolásához.

|Szándék   | Entitás | Példa kimondott szöveg   |
|------------------|------------------------------|------------------------------|
| Időjárás-ellenőrzés | {"type": "location", "Entity": "Seattle"}<br>{"type": "beépített. datetimeV2. Date", "entitás": "Tomorrow", "megoldás": "2018-05-23"} | Milyen időjárási viszonyok vannak `Seattle` `tomorrow` ? |
| Időjárás-ellenőrzés | {"type": "date_range", "entitás": "Ez a hétvége"} | Előrejelzés megjelenítése a következőhöz: `this weekend` |
||||

## <a name="prebuilt-domain-intents"></a>Előre elkészített tartományi leképezések

Az [előre elkészített tartományok](./howto-add-prebuilt-models.md) a hosszúságú kimondott szöveg-vel való leképezéseket biztosítanak.

## <a name="none-intent"></a>A None szándék

A rendszer nem hozza létre a **nincs** szándékot, de a célra üresen hagyta. A **none** cél a kötelező szándék, ezért nem törölhető és nem nevezhető át. Ebbe töltheti fel a tartományon kívül eső beszédelemeket.

A **none** cél a tartalék leképezés, amely minden alkalmazásban fontos, és az összes hosszúságú kimondott szöveg 10%-át kell tartalmaznia. Az alkalmazás-tartományba (a tárgy területére) nem fontos LUIS hosszúságú kimondott szöveg tanítani. Ha nem ad hozzá semmilyen hosszúságú kimondott szöveg a **none** szándékhoz, a Luis a tartományon kívüli teljes tartománynevet kényszeríti az egyik tartományon belüli leképezésre. Ez elferdíti az előrejelzési pontszámokat, ha a LUIS nem megfelelő szándékot tanít a kiíráshoz.

Ha a kiírást a nem szándék alapján jósolják meg, az ügyfélalkalmazás további kérdéseket tehet fel, vagy megadhat egy menüt, amely a felhasználót az érvényes választásokra irányítja.

## <a name="negative-intentions"></a>Negatív szándékok
Ha meg szeretné határozni a negatív és pozitív célokat, például a **"szeretnék** autót" és a "nem **szeretnék** autót", létrehozhat két leképezést (egy pozitív és egy negatív), és hozzáadhatja a megfelelő hosszúságú kimondott szöveg. Létrehozhat egyetlen leképezést is, és megadhatja a két különböző pozitív és negatív kifejezést entitásként.

## <a name="intents-and-patterns"></a>Szándékok és minták

Ha van példa hosszúságú kimondott szöveg, amely a részben vagy egészben is meghatározható reguláris kifejezésként, érdemes lehet a [reguláris kifejezéssel](luis-concept-entity-types.md#regular-expression-entity) párosítani egy [mintázattal](luis-concept-patterns.md).

A reguláris kifejezéssel rendelkező entitások garantálják az kivonást, így a minta egyeztetése megtörténik. A minta egyeztetése garantálja a pontos szándékot.

## <a name="intent-balance"></a>Leképezési egyenleg
Az alkalmazás-tartományhoz tartozó leképezéseknek egyensúlyt kell hosszúságú kimondott szöveg az egyes szándékok között. Ne legyen egy szándéka 10 hosszúságú kimondott szöveg és egy másik szándékkal a 500 hosszúságú kimondott szöveg. Ez nem kiegyensúlyozott. Ha ezt a helyzetet látja el, tekintse át a 500 hosszúságú kimondott szöveg szándékát, és ellenőrizze, hogy a szándékok közül sok átrendezhető-e egy [mintázatba](luis-concept-patterns.md).

A **nincs** szándék nem tartalmazza az egyenleget. Ennek a szándéknak az alkalmazás teljes hosszúságú kimondott szöveg 10%-át kell tartalmaznia.

## <a name="intent-limits"></a>Leképezési korlátok
Tekintse át a [korlátokat](luis-limits.md#model-boundaries) , hogy megértse, hány leképezést lehet hozzáadni egy modellhez.

### <a name="if-you-need-more-than-the-maximum-number-of-intents"></a>Ha többre van szüksége a maximálisan megengedett számnál
Először is gondolja át, hogy a rendszer túl sok szándékot használ-e.

### <a name="can-multiple-intents-be-combined-into-single-intent-with-entities"></a>Több leképezés egyesíthető egyetlen szándékkal az entitások használatával.
A túl hasonló szándékok megnehezítik a LUIS számára a közöttük való különbségtételt. A szándéknak elég változatosaknak kell lennie ahhoz, hogy rögzítse a felhasználó által kért fő feladatokat, de nem kell rögzítenie a kód minden elérési útját. Például a BookFlight és a FlightCustomerService különálló leképezések lehetnek egy utazási alkalmazásban, de a BookInternationalFlight és a BookDomesticFlight túl hasonlóak. Ha a rendszeren meg kell különböztetni őket, használjon entitásokat vagy más logikát a szándék helyett.

### <a name="dispatcher-model"></a>Diszpécser modell
További információ a LUIS és a QnA Maker alkalmazások a [küldő modellel](luis-concept-enterprise.md#when-you-need-to-combine-several-luis-and-qna-maker-apps)való kombinálásával.

### <a name="request-help-for-apps-with-significant-number-of-intents"></a>A jelentős számú szándékkal rendelkező alkalmazások súgójának kérése
Ha csökkenti a leképezések számának csökkentését vagy a szándékok több alkalmazásba való felosztását, akkor forduljon az ügyfélszolgálathoz. Ha az Azure-előfizetése támogatási szolgáltatásokat tartalmaz, forduljon az [Azure technikai támogatási](https://azure.microsoft.com/support/options/)szolgálatához.

## <a name="next-steps"></a>Következő lépések

* További információ az [entitásokról](luis-concept-entity-types.md), amelyek a szándékok szempontjából fontos szavakat mutatnak
* Megtudhatja, hogyan [veheti fel és kezelheti a szándékait](luis-how-to-add-intents.md) a Luis-alkalmazásban.
* [Ajánlott eljárások](luis-concept-best-practices.md) áttekintése