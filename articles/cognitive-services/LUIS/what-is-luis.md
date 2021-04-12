---
title: Mi a Language Understanding (LUIS)?
description: Language Understanding (LUIS) – felhőalapú API-szolgáltatás gépi tanulással, természetes nyelvvel, a jelentés és az adatok kinyerése érdekében.
keywords: Azure, mesterséges intelligencia, AI, természetes nyelvi feldolgozás, NLP, természetes nyelvi megértés, NLU, LUIS, társalgási AI, AI Csevegőrobot, NLP AI, Azure Luis
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: overview
ms.date: 03/22/2021
ms.custom: cog-serv-seo-aug-2020
ms.openlocfilehash: f32f58bebc0a7d64443259981590e368b109b19b
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2021
ms.locfileid: "106278949"
---
# <a name="what-is-language-understanding-luis"></a>Mi a Language Understanding (LUIS)?

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

Language Understanding (LUIS) egy felhőalapú társalgási szolgáltatás, amely egyéni gépi tanulási intelligenciát alkalmaz a felhasználó társalgási, természetes nyelvű szövegére az általános jelentés kiszámításához, valamint a releváns és részletes információk lekéréséhez.

A LUIS ügyfélalkalmazása lehet bármely beszélgetési alkalmazás, amely a felhasználóval természetes nyelven kommunikál egy adott feladat végrehajtásához. Ilyenek például a közösségi multimédia alkalmazások, az AI csevegőrobotok és a beszédfelismerést támogató asztali alkalmazások.

![A Cognitive Services Language Understanding (LUIS)-vel dolgozó 3 ügyfélalkalmazás fogalmi képe](./media/luis-overview/luis-entry-point.png "A Cognitive Services Language Understanding (LUIS)-vel dolgozó 3 ügyfélalkalmazás fogalmi képe")

Ez a dokumentáció a következő cikk-típusokat tartalmazza:  

* [**A gyors**](luis-get-started-create-app.md) üzembe helyezési útmutató végigvezeti Önt a szolgáltatásra irányuló kérések lépésein.  
* A [**útmutatók**](luis-how-to-start-new-app.md) útmutatói a szolgáltatás részletesebb vagy testreszabott módokon történő használatára vonatkozó utasításokat tartalmaznak.  
* A [**fogalmak**](artificial-intelligence.md) részletesen ismertetik a szolgáltatás funkcióit és funkcióit.  
* Az [**oktatóanyagok**](tutorial-intents-only.md) már olyan útmutatók, amelyek bemutatják, hogyan használhatja a szolgáltatást összetevőként a szélesebb körű üzleti megoldásokban.  

## <a name="use-luis-in-a-chat-bot"></a>A LUIS használata csevegőrobotban

<a name="Accessing-LUIS"></a>

Miután közzétette az Azure LUIS alkalmazást, egy ügyfélalkalmazás hosszúságú kimondott szöveg (Text) küld a LUIS Natural Language Processing Endpoint [API][endpoint-apis] -nak, és JSON-válaszként fogadja az eredményeket. A csevegőrobotok a LUIS gyakori ügyfélalkalmazásai.


![A LUIS elméleti ábrázolása a csevegő használatával a természetes nyelvi megértést (NLP) használó felhasználói szöveg előrejelzéséhez](./media/luis-overview/LUIS-chat-bot-request-response.svg "A LUIS-vel kapcsolatos elméleti képek a csevegő használatával a természetes nyelvi megértéssel (NLP")

|Lépés|Művelet|
|:--|:--|
|1|Az ügyfélalkalmazás a következő felhasználói _kimondott szöveget_ (saját szavaikkal megfogalmazott szöveg) küldi el HTTP-kérésként a LUIS-végpontnak: „Fel szeretném hívni a HR képviselőmet.”|
|2|A LUIS lehetővé teszi, hogy egyéni nyelvi modelljeit az alkalmazáshoz való intelligenciával bővítse. A gépi megtanult nyelvi modellek a felhasználó strukturálatlan bemeneti szövegét, és egy JSON-formátumú választ adnak vissza, amely a legfelső szándékot használja `HRContact` . A JSON-végpont minimális válasza tartalmazza a lekérdezési kimondott szöveget, valamint a legmagasabb pontszámú szándékot. Emellett olyan adatok kinyerésére is képes, mint például a _Contact Type_ entitás.|
|3|Az ügyfélalkalmazás a JSON-választ használatával dönti el, hogy hogyan teljesítse a felhasználói kérést. Ezek a döntések magukban foglalhatják a bot Framework kódjának döntési fáját, és más szolgáltatásokhoz is meghívhatják azokat. |

A LUIS alkalmazás biztosítja az intelligenciát, így az ügyfélalkalmazás is intelligens döntéseket hozhat. A döntéseket nem a LUIS biztosítja.

<a name="Key-LUIS-concepts"></a>
<a name="what-is-a-luis-model"></a>

## <a name="natural-language-understanding-nlu"></a>Természetes nyelvi megértés (NLU)

A [Luis a mesterséges intelligenciát (AI)](artificial-intelligence.md "LUIS mesterséges intelligenciát (AI) biztosít") NLU, a természetes nyelvi feldolgozó mesterséges intelligencia egy részét képezi.

A LUIS-alkalmazás egy tartományszintű természetes nyelvi modellt tartalmaz. A LUIS alkalmazás elindításához használhat előre összeállított tartománymodellt, elkészítheti a saját modelljét vagy kombinálhatja egy előre elkészített tartomány elemeit saját egyéni adataival.

* **Előre összeállított modell** A LUIS számos előre összeállított, szándékokat, kimondott szövegeket és előre létrehozott entitásokat tartalmazó tartománymodellel rendelkezik. Az előre létrehozott entitásokat a szándékok és kimondott szövegek használata nélkül is alkalmazhatja. Az [Előre összeállított tartománymodellek](./howto-add-prebuilt-models.md "Előre elkészített tartományi modellek") tartalmazzák a teljes dizájnt, így nagyszerű módot kínálnak arra, hogy a LUIS használatát gyorsan elkezdhesse.

* **Egyéni modell** LUIS számos lehetőséget kínál a saját egyéni modelljeinek, például a szándékok és az entitások azonosítására. Az entitások például a gépi tanulási entitások, a konkrét vagy literális entitások, valamint a gépi tanulás és a literál kombinációja.

További információ az [NLP AI](artificial-intelligence.md "NLP")-ról és a NLU Luis-specifikus területéről.

## <a name="step-1-design-and-build-your-model"></a>1. lépés: a modell megtervezése és összeállítása

Tervezze meg a modellt a **[felhasználói szándékok nevű](luis-concept-intent.md "leképezések")** kategóriával. Minden egyes szándéknak szüksége van felhasználói **[kimondott szövegek](luis-concept-utterance.md "hosszúságú kimondott szöveg")** példáira. Minden Kimondás lehetővé teszi a [gépi tanulási entitásokkal](luis-concept-entity-types.md#effective-machine-learned-entities "gépi tanulással rendelkező entitások")kinyerni kívánt adatgyűjtést.

|Példa felhasználói kimondott szövegre|Szándék|Kinyert adathalmazok|
|-----------|-----------|-----------|
|`Book a flight to Seattle?`|RepülőjegyFoglalás|Seattle|
|`When does your store open?`|ÜzletNyitvatartásÉsHelyszín|nyitás|
|`Schedule a meeting at 1pm with Bob in Distribution`|ÉrtekezletÜtemezés|13 óra, Bob|

Hozza létre a modellt a [szerzői](https://go.microsoft.com/fwlink/?linkid=2092087 "létrehozás") API-kkal vagy a **[Luis-portálral](https://www.luis.ai "LUIS portál")**, vagy mindkettővel. További információ a [portál](get-started-portal-build-app.md "portál") és az [SDK-ügyfél kódtárainak](./client-libraries-rest-api.md?pivots=rest-api "SDK-ügyféloldali kódtárak")létrehozásáról.

## <a name="step-2-get-the-query-prediction"></a>2. lépés: a lekérdezés előrejelzésének beolvasása

Miután az alkalmazás modelljét betanítják és közzétették a végponton, egy ügyfélalkalmazás (például egy chat-bot) elküldi a hosszúságú kimondott szöveg az előrejelzési [végpont](https://go.microsoft.com/fwlink/?linkid=2092356 "endpoint") API-nak. Az API a modellt a teljes elemzésre alkalmazza, és az előrejelzési eredményeket JSON formátumban adja meg.

A JSON-végpont minimális válasza tartalmazza a lekérdezési kimondott szöveget, valamint a legmagasabb pontszámú szándékot. Emellett olyan adatok kinyerése is kinyerhető, mint például a következő **kapcsolatfelvételi típus** entitás és az általános hangulat.

```JSON
{
    "query": "I want to call my HR rep",
    "prediction": {
        "topIntent": "HRContact",
        "intents": {
            "HRContact": {
                "score": 0.8582669
            }
        },
        "entities": {
            "Contact Type": [
                "call"
            ]
        },
        "sentiment": {
            "label": "neutral",
            "score": 0.5
        }
    }
}
```

## <a name="step-3-improve-model-prediction"></a>3. lépés: a modell előrejelzésének javítása

Miután közzétette a LUIS-alkalmazást, és valós felhasználói hosszúságú kimondott szöveg kapott, a LUIS a végpontok hosszúságú kimondott szöveg [aktív tanulását](luis-concept-review-endpoint-utterances.md "aktív tanulás") teszi lehetővé az előrejelzés pontosságának javítása érdekében. Tekintse át ezeket a javaslatokat a fejlesztési életciklusának rendszeres karbantartási munkájának részeként.

<a name="using-luis"></a>

## <a name="development-lifecycle-and-tools"></a>Fejlesztési életciklus és eszközök
LUIS eszközöket, verziószámozást és együttműködést biztosít más LUIS szerzőkkel, hogy integrálni lehessen a teljes [fejlesztési](luis-concept-app-iteration.md "fejlesztési életciklus")életciklusba.

Language Understanding (LUIS) REST APIként bármilyen termékkel, szolgáltatással vagy keretrendszerrel használható HTTP-kéréssel. A LUIS az ügyféloldali kódtárakat (SDK-kat) is biztosítja számos legnépszerűbb programozási nyelvhez. További információ a megadott [fejlesztői erőforrásokról](developer-reference-resource.md "fejlesztői erőforrások") .

Eszközök a LUIS robotokkal való gyors és egyszerű használatához:
* [Luis CLI](https://github.com/Microsoft/botbuilder-tools/tree/master/packages/LUIS "LUIS CLI") A NPM csomag szerzői és előrejelzési lehetőségeket biztosít önálló parancssori eszközként vagy importálásként.
* A [LUISGen](https://github.com/Microsoft/botbuilder-tools/tree/master/packages/LUISGen "LUISGen") eszközzel szigorú típusmegadású C#- és TypeScript-forráskód hozható létre exportált LUIS-modellekből.
* A [Dispatch](https://aka.ms/dispatch-tool "Küldése") többféle LUIS- és QnA Maker-alkalmazás számára is lehetővé teszi, hogy elérhetők legyenek egy Dispatcher-modellt használó szülőalkalmazásból.
* [LUDown](https://github.com/Microsoft/botbuilder-tools/tree/master/packages/Ludown "LUDown") A LUDown egy parancssori eszköz, amely segít a robot nyelvi modelljeinek kezelésében.

## <a name="integrate-with-a-bot"></a>Integrálás robottal

Használja az [Azure bot Service](/azure/bot-service/ "Azure bot Service") -t a [Microsoft bot Framework](https://dev.botframework.com/ "Microsoft Bot-keretrendszer") használatával egy csevegési robot létrehozásához és üzembe helyezéséhez. Megtervezheti és fejlesztheti a grafikus kezelőfelülettel, a [zeneszerzővel](/composer/ "Composer")vagy a leggyakoribb bot-forgatókönyvekhez tervezett [munkabot-mintákkal](https://github.com/microsoft/BotBuilder-Samples "működő bot-minták") .

## <a name="integrate-with-other-cognitive-services"></a>Integrálás más Cognitive Servicesokkal

További, a LUIS szolgáltatással használható Cognitive Services szolgáltatások:
* A [QnA Maker](../QnAMaker/overview/overview.md "QnA Maker") különböző típusú szövegek kombinálásával teszi lehetővé egy kérdéseken és válaszokon alapuló tudásbázis kialakítását.
* A [Speech service](../Speech-Service/overview.md "Speech szolgáltatás") szöveggé alakítja a szóbeli kéréseket.

A LUIS a meglévő LUIS-erőforrások részeként Text Analytics funkciókat biztosít. Ez a funkció magában foglalja az [érzelmek elemzését](luis-how-to-publish-app.md#configuring-publish-settings "hangulat elemzése") és a [kulcsfontosságú kifejezés kinyerését](luis-reference-prebuilt-keyphrase.md "fő kifejezés kibontása") az előre elkészített keyPhrase-entitással.

## <a name="learn-with-the-quickstarts"></a>Ismerkedjen meg a gyors útmutatókkal

Ismerje meg a LUIS-t gyakorlati útmutatókkal a [portál](get-started-portal-build-app.md "portál") és az [SDK ügyféloldali kódtárak](./client-libraries-rest-api.md?pivots=rest-api "SDK-ügyféloldali kódtárak")használatával.


## <a name="deploy-on-premises-using-docker-containers"></a>Helyszíni üzembe helyezés Docker-tárolók használatával

A [Luis-tárolók használatával](luis-container-howto.md) üzembe helyezhet API-szolgáltatásokat a helyszínen. Ezek a Docker-tárolók lehetővé teszik, hogy a szolgáltatás a megfelelőségi, biztonsági vagy egyéb működési okokból közelebb kerüljön az adataihoz.

## <a name="next-steps"></a>Következő lépések

* A szolgáltatással és a dokumentációval kapcsolatos [Újdonságok](whats-new.md "Újdonságok")
* [Tervezze meg alkalmazását](luis-how-plan-your-app.md "Az alkalmazás megtervezése") [szándékokkal](luis-concept-intent.md "leképezések") és [entitásokkal](luis-concept-entity-types.md "szervezetek").

[bot-framework]: /bot-framework/
[flow]: /connectors/luis/
[authoring-apis]: https://go.microsoft.com/fwlink/?linkid=2092087
[endpoint-apis]: https://go.microsoft.com/fwlink/?linkid=2092356
[qnamaker]: https://qnamaker.ai/