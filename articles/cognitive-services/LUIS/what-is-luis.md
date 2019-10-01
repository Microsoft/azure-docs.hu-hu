---
title: Mi a Language Understanding (LUIS)?
titleSuffix: Azure Cognitive Services
description: A Language Understanding (LUIS) egy olyan felhőalapú API-szolgáltatás, amely egyéni gépi tanulási intelligenciát használ a felhasználó párbeszédszerű, természetes nyelvi szövege általános jelentésének megállapítására, valamint a releváns, részletes információk kinyerésére.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: overview
ms.date: 09/27/2019
ms.author: diberry
ms.openlocfilehash: 3d4251613ac2a00ddc56d5e573b49ced01adf61c
ms.sourcegitcommit: 6fe40d080bd1561286093b488609590ba355c261
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/01/2019
ms.locfileid: "71703100"
---
# <a name="what-is-language-understanding-luis"></a>Mi a Language Understanding (LUIS)?

A Language Understanding (LUIS) egy olyan felhőalapú API-szolgáltatás, amely egyéni gépi tanulási intelligenciát használ a felhasználó párbeszédszerű, természetes nyelvi szövege általános jelentésének megállapítására, valamint a releváns, részletes információk kinyerésére. 

A LUIS ügyfélalkalmazása lehet bármely beszélgetési alkalmazás, amely a felhasználóval természetes nyelven kommunikál egy adott feladat végrehajtásához. Az ügyfélalkalmazások közé sorolhatók például a közösségimédia-alkalmazások, a csevegőrobotok és a beszédalapú asztali alkalmazások.  

![A Cognitive Services Language Understanding (LUIS) szolgáltatással együttműködő 3 kliensalkalmazás koncepcióképe](./media/luis-overview/luis-entry-point.png "A Cognitive Services Language Understanding (LUIS) szolgáltatással együttműködő 3 kliensalkalmazás koncepcióképe")

## <a name="use-luis-in-a-chat-bot"></a>A LUIS használata csevegőrobotban

<a name="Accessing-LUIS"></a>

Miután közzétette a LUIS alkalmazást, egy ügyfélalkalmazás hosszúságú kimondott szöveg (Text) küld a LUIS Natural Language Processing Endpoint [API][endpoint-apis] -nak, és JSON-válaszként fogadja az eredményeket. A csevegőrobotok a LUIS gyakori ügyfélalkalmazásai.


![Koncepciókép: csevegőrobottal együttműködő LUIS a felhasználói szöveg természetesnyelv-feldolgozással (NLP) történő megjósolásához](./media/luis-overview/luis-overview-process-2.png "Koncepciókép: csevegőrobottal együttműködő LUIS a felhasználói szöveg természetesnyelv-feldolgozással (NLP) történő megjósolásához")

|Lépés|Műveletek|
|:--|:--|
|1|Az ügyfélalkalmazás a következő felhasználói _kimondott szöveget_ (saját szavaikkal megfogalmazott szöveg) küldi el HTTP-kérésként a LUIS-végpontnak: „Fel szeretném hívni a HR képviselőmet.”|
|2|A LUIS alkalmazza a tanult modellt a természetes nyelvű szövegre a felhasználói bemenet intelligens feldolgozása végett. A LUIS JSON-formátumú választ ad vissza a „HRContact” legfelső szándékkal. A JSON-végpont minimális válasza tartalmazza a lekérdezési kimondott szöveget, valamint a legmagasabb pontszámú szándékot. Az olyan adatokat is képes kinyerni, mint a Kapcsolat típusa entitás.|
|3|Az ügyfélalkalmazás a JSON-választ használatával dönti el, hogy hogyan teljesítse a felhasználói kérést. Ezek a döntések a robot keretrendszerében lévő döntési fákat és más szolgáltatások felé irányuló hívásokat tartalmazhatnak. |

A LUIS alkalmazás biztosítja az intelligenciát, így az ügyfélalkalmazás is intelligens döntéseket hozhat. A döntéseket nem a LUIS biztosítja. 

<a name="Key-LUIS-concepts"></a>
<a name="what-is-a-luis-model"></a>

## <a name="natural-language-processing"></a>Természetes nyelvek feldolgozása

A LUIS alkalmazás tartományspecifikus természetes nyelvi modellt tartalmaz. A LUIS alkalmazás elindításához használhat előre összeállított tartománymodellt, elkészítheti a saját modelljét vagy kombinálhatja egy előre elkészített tartomány elemeit saját egyéni adataival.

* **Előre összeállított modell** A LUIS számos előre összeállított, szándékokat, kimondott szövegeket és előre létrehozott entitásokat tartalmazó tartománymodellel rendelkezik. Az előre létrehozott entitásokat a szándékok és kimondott szövegek használata nélkül is alkalmazhatja. Az [Előre összeállított tartománymodellek](luis-how-to-use-prebuilt-domains.md) tartalmazzák a teljes dizájnt, így nagyszerű módot kínálnak arra, hogy a LUIS használatát gyorsan elkezdhesse.

* **Egyéni entitások** A LUIS több módot kínál Önnek arra, hogy azonosítsa saját egyéni szándékait és entitásait, beleértve a gép által tanult entitásokat, a konkrét vagy literálentitásokat valamint a gép által tanult és literálentitások kombinációit is.

## <a name="build-the-luis-model"></a>A LUIS-modell létrehozása
A modell létrehozása a [szerkesztési](https://go.microsoft.com/fwlink/?linkid=2092087) API-kkal vagy a LUIS-portállal lehetséges.

A LUIS-modell a **[szándékok](luis-concept-intent.md)** elnevezésű felhasználói szándékok kategóriáival kezdődik. Minden egyes szándéknak szüksége van felhasználói **[kimondott szövegek](luis-concept-utterance.md)** példáira. Minden egyes kimondott szöveg tartalmazhat számos különféle adatot, amelyeket **[entitásokkal](luis-concept-entity-types.md)** kell kinyerni. 

|Példa felhasználói kimondott szövegre|Szándék|Entitások|
|-----------|-----------|-----------|
|„Foglalj repülőjegyet __Seattle-be__.”|RepülőjegyFoglalás|Seattle|
|„Mikor __nyit ki__ az üzlet?”|ÜzletNyitvatartásÉsHelyszín|nyitás|
|„Ütemezz egy értekezletet __13 órára__ __Bobbal__ a terjesztési osztályról.”|ÉrtekezletÜtemezés|13 óra, Bob|

## <a name="query-prediction-endpoint"></a>Előrejelzési végpont lekérdezése

A modell létrehozása és végponton való közzététele után az ügyfélalkalmazás kimondott szövegeket küld a közzétett előrejelzési [végpont](https://go.microsoft.com/fwlink/?linkid=2092356) API-jának. Az API alkalmazza a modellt a szövegre elemzés céljából. Az API JSON-formátumú válaszban adja vissza az előrejelzés eredményeit.  

A JSON-végpont minimális válasza tartalmazza a lekérdezési kimondott szöveget, valamint a legmagasabb pontszámú szándékot. Az olyan adatokat is képes kinyerni, mint a következő **Kapcsolat típusa** entitás. 

```JSON
{
  "query": "I want to call my HR rep.",
  "topScoringIntent": {
    "intent": "HRContact",
    "score": 0.921233
  },
  "entities": [
    {
      "entity": "call",
      "type": "Contact Type",
      "startIndex": 10,
      "endIndex": 13,
      "score": 0.7615982
    }
  ]
}
```

## <a name="improve-model-prediction"></a>Modell-előrejelzés javítása

Miután egy LUIS-modell a közzététele után valós felhasználói kimondott szövegeket fogad, a LUIS több módszert is biztosít az előrejelzés pontosításához: végponti kimondott szövegek [aktív tanulása](luis-concept-review-endpoint-utterances.md), [kifejezéslisták](luis-concept-feature.md) a tartomány szavainak belefoglalása érdekében, valamint [minták](luis-concept-patterns.md) a szükséges kimondott szövegek számának csökkentésére.

<a name="using-luis"></a>

## <a name="development-lifecycle"></a>Fejlesztési életciklus
A LUIS eszközöket és verziókezelést, valamint a többi LUIS-szerzővel való együttműködés lehetőségét biztosítva teszi lehetővé az integrációt a teljes fejlesztési ügyfélciklusban az ügyfélalkalmazás és a nyelvi modell szintjén. 

## <a name="implementing-luis"></a>A LUIS implementálása
Mivel a LUIS egy REST API, bármely olyan termékkel, szolgáltatással vagy keretrendszerrel használható, amely HTTP-kéréseket hoz létre. Az alábbi lista tartalmazza a főbb, LUIS szolgáltatással használható Microsoft-termékeket és -szolgáltatásokat.

A LUIS leggyakoribb ügyfélalkalmazása:
* A [Web app bot](https://docs.microsoft.com/azure/bot-service/?view=azure-bot-service-4.0) gyorsan létrehoz egy LUIS-kompatibilis csevegőrobotot, amely szövegbevitel útján kommunikál a felhasználóval. A [bot Framework][bot-framework] [4. x](https://github.com/Microsoft/botbuilder-dotnet) verzióját használja egy teljes körű robot-élményhez.

Eszközök a LUIS robotokkal való gyors és egyszerű használatához:
* [Luis CLI](https://github.com/Microsoft/botbuilder-tools/tree/master/packages/LUIS) A NPM csomag szerzői és előrejelzési lehetőségeket biztosít önálló parancssori eszközként vagy importálásként. 
* A [LUISGen](https://github.com/Microsoft/botbuilder-tools/tree/master/packages/LUISGen) eszközzel szigorú típusmegadású C#- és TypeScript-forráskód hozható létre exportált LUIS-modellekből.
* A [Dispatch](https://aka.ms/dispatch-tool) többféle LUIS- és QnA Maker-alkalmazás számára is lehetővé teszi, hogy elérhetők legyenek egy Dispatcher-modellt használó szülőalkalmazásból.
* A [LUDown](https://github.com/Microsoft/botbuilder-tools/tree/master/packages/Ludown) a robotok nyelvi modelljeinek kezelését egyszerűsítő parancssori eszköz.

További, a LUIS szolgáltatással használható Cognitive Services szolgáltatások:
* [QnA Maker][qnamaker] lehetővé teszi, hogy több típusú szöveg egyesíthető legyen egy kérdés-és válasz-Tudásbázisban.
* A [Bing Spell Check API](../bing-spell-check/proof-text.md) az előrejelzés előtt lehetővé teszi a szöveg javítását. 
* A [Speech service](../Speech-Service/overview.md) szöveggé alakítja a szóbeli kéréseket. 
* A [Conversation learner](https://docs.microsoft.com/azure/cognitive-services/labs/conversation-learner/overview) robotbeszélgetések gyorsabb létrehozását teszi lehetővé a LUIS szolgáltatással.
* A [Project personality chat](https://docs.microsoft.com/azure/cognitive-services/project-personality-chat/overview) a robotokkal való társalgást kezeli.

Minták LUIS használatával:
* [Társalgási AI](https://github.com/Microsoft/AI) GitHub-tárház.
* [Language Understanding](https://github.com/Azure-Samples/cognitive-services-language-understanding) Azure-minták

## <a name="next-steps"></a>További lépések

Hozzon létre új LUIS alkalmazást [előre összeállított](luis-get-started-create-app.md) vagy [egyéni](luis-quickstart-intents-only.md) tartománnyal. [Kérdezze le egy nyilvános IoT-alkalmazás előrejelzési végpontját](luis-get-started-get-intent-from-browser.md).

[bot-framework]: https://docs.microsoft.com/bot-framework/
[flow]: https://docs.microsoft.com/connectors/luis/
[authoring-apis]: https://go.microsoft.com/fwlink/?linkid=2092087
[endpoint-apis]: https://go.microsoft.com/fwlink/?linkid=2092356
[qnamaker]: https://qnamaker.ai/
