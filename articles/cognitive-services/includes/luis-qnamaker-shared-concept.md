---
title: fájl belefoglalása
description: fájl belefoglalása
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.custom: include file
ms.date: 04/16/2020
ms.openlocfilehash: b2b405e7f6babf84ef8297310a5a7a34c3f2dc11
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "102623545"
---
Cognitive Services két természetes nyelvi feldolgozási szolgáltatást biztosít, [Language Understanding](../luis/what-is-luis.md) és [QnA Maker](../qnamaker/overview/overview.md), amelyek mindegyike más célra szolgál. Ismerje meg, hogy mikor kell használni az egyes szolgáltatásokat, és hogyan kell egymással kiegészíteni őket.

A természetes nyelvi feldolgozás (NLP) lehetővé teszi az ügyfélalkalmazás, például a csevegési robot használatát a felhasználókkal való munkavégzéshez természetes nyelven. A felhasználó mondatot vagy kifejezést ír be. A felhasználó szövege nem lehet rossz nyelvtan, helyesírás és írásjel. A kognitív szolgáltatás a felhasználói mondaton keresztül is képes működni, így információt ad vissza, amikor a csevegési robot segítségére van szüksége a felhasználónak.

## <a name="cognitive-services-with-nlp"></a>NLP-Cognitive Services

Language Understanding (LUIS) és QnA Maker biztosítanak NLP számára. Az ügyfélalkalmazás természetes nyelvi szöveget küld. A szolgáltatás végrehajtja a szöveget, feldolgozza és visszaadja az eredményt.

## <a name="when-to-use-each-service"></a>Mikor kell használni az egyes szolgáltatásokat

Language Understanding (LUIS) és QnA Maker különböző problémák megoldására. LUIS meghatározza, hogy a felhasználó szövegének (azaz a teljes kifejezésnek) szándéka, míg QnA Maker meghatározza a felhasználó szövegére (azaz lekérdezésre) adott választ.

A megfelelő szolgáltatás kiválasztásához meg kell ismernie az ügyfélalkalmazástól érkező felhasználói szöveget, valamint azt, hogy milyen információkra van szükség az ügyfélalkalmazás számára a kognitív szolgáltatástól.

Ha a csevegési robot megkapja a szöveget `How do I get to the Human Resources building on the Seattle North campus?` , az alábbi táblázat segítségével megismerheti, hogy az egyes szolgáltatások hogyan működnek a szöveggel.

|Szolgáltatás|Ügyfélalkalmazás határozza meg|
|--|--|
|LUIS|**Meghatározza a felhasználó szövegének szándékát** – a szolgáltatás nem adja vissza a kérdésre adott választ. Ez a szöveg például a szándéknak megfelelőként van osztályozva `FindLocation` .<br>|
|QnA Maker|Egy egyéni Tudásbázisból származó **kérdésre adott válasz visszaadása** . Ez a szöveg például a statikus szöveges válaszával kapcsolatos kérdésként van meghatározva  `Get on the #9 bus and get off at Franklin street` .|
|||

> [!div class="mx-imgBorder"]
> ![Infographic – annak meghatározása, hogy mikor kell használni a LUIS-t és mikor kell használni a QnA Maker](./luis-qna-maker-together-decision.png)

## <a name="when-do-you-use-luis"></a>Mikor használja a LUIS-t?

A LUIS használata akkor ajánlott, amikor tisztában kell lennie a beszédelem szándékával a csevegőrobot egyik folyamatának részeként. Ha továbbra is a példában szereplő szövegre `How do I get to the Human Resources building on the Seattle North campus?` kattint, a felhasználó szándéka, hogy megkeresse a helyet, megadhatja a teljes (entitásokkal kihúzott) részletek részleteit egy másik szolgáltatás, például egy szállítási kiszolgáló számára a válasz beszerzéséhez.

Nem kell összekapcsolnia a LUIS és a QnA Makert a szándék meghatározásához.

Összekapcsolhatja a két szolgáltatást ehhez a kilépéshez, ha a csevegési robotnak a szándékok és entitások alapján kell feldolgoznia a szöveget (a LUIS használatával), valamint meg kell keresnie az adott statikus szöveges választ (QnA Maker használatával).

## <a name="when-do-you-use-qna-maker"></a>Mikor használja a QnA Maker?

A QnA Makert akkor használja, ha rendelkezésére áll a válaszok statikus tudásbázisa. Ez a tudásbázis az Ön egyedi igényeinek felel meg, és dokumentumokkal, például PDF-ekkel és URL-címekkel hozta létre.

Ha folytatja a példát, `How do I get to the Human Resources building on the Seattle North campus?` küldje el a szöveget lekérdezésként a közzétett QnA Maker szolgáltatásba, és fogadja el a legjobb választ.

Nincs szükség a LUIS és a QnA Maker összekapcsolására a kérdés megválaszolásának meghatározásához.

Összekapcsolhatja a két szolgáltatást ehhez a kilépéshez, ha a csevegési robotnak a szándékok és entitások alapján kell feldolgoznia a szöveget (a LUIS használatával), valamint a válasz megtalálását (QnA Maker használatával).

## <a name="use-both-services-when-your-knowledge-base-is-incomplete"></a>Mindkét szolgáltatás használata, ha a Tudásbázis hiányos

Ha felépíti a QnA Maker tudásbázist, de tudja, hogy a tárgy tartománya módosul (például az időben megjelenő információk), akkor a LUIS és a QnA Maker szolgáltatásokat kombinálhatja. Ez lehetővé teszi a tudásbázisban található információk használatát, de a LUIS használatával is meghatározhatja a felhasználó szándékát. Ha az ügyfélalkalmazás szándéka van, kérheti a kapcsolódó adatokat más forrásokból is.

Az ügyfélalkalmazás a következő pontszámok esetében is figyelnie kell a LUIS és a QnA Maker válaszokat. Ha a QnA Maker pontszáma valamilyen tetszőleges küszöbérték alá esik, használja a LUIS által visszaadott szándékot és entitási adatokat, hogy átadja az adatokat egy külső szolgáltatásnak.

A példában szereplő szöveg folytatásakor `How do I get to the Human Resources building on the Seattle North campus?` tegyük fel, hogy QnA Maker alacsony megbízhatósági pontszámot ad vissza. A LUIS `FindLocation` és a kinyert entitások, például a és a által visszaadott szándék használatával `Human Resources building` `Seattle North campus` küldhetik ezeket az információkat egy leképezési vagy keresési szolgáltatásba egy másik válaszra.

Ezt a külső féltől származó választ is bemutathatja a felhasználónak az érvényesítéshez. A felhasználó jóváhagyása után visszatérhet a QnA Makerra, és hozzáadhatja az adatokat az ismeretek növeléséhez.

## <a name="use-both-services-when-your-chat-bot-needs-more-information"></a>Mindkét szolgáltatást használhatja, ha a csevegő robotjának további információra van szüksége

Ha a csevegési robotnak több információra van szüksége, mint amennyit a szolgáltatás nyújt, a döntési fa folytatásához használja mindkét szolgáltatást, és dolgozza fel mindkét választ az ügyfélalkalmazás számára.

A robot Framework **[elküldési parancssori](https://github.com/Microsoft/botbuilder-tools/tree/master/packages/Dispatch)** felületének használatával létrehozhat egy folyamatot, amely mindkét szolgáltatással használható. Ez az eszköz egy olyan legjobb LUIS-alkalmazást hoz létre, amely a LUIS és a QnA Maker között alárendelt alkalmazásként való kiszállításra szolgál. [További](/azure/bot-service/bot-builder-tutorial-dispatch?tabs=cs) információ a Luis, a QnA Maker és a bot keretrendszer integrálásáról.

A bot Builder minta, az **NLP a küldéssel**, [C#](https://github.com/microsoft/BotBuilder-Samples/tree/master/samples/csharp_dotnetcore/14.nlp-with-dispatch) vagy [Node.js](https://github.com/microsoft/BotBuilder-Samples/tree/master/samples/javascript_nodejs/14.nlp-with-dispatch)segítségével megvalósíthatja ezt a típusú csevegési robotot.

## <a name="best-practices"></a>Ajánlott eljárások

Ajánlott eljárások megvalósítása az egyes szolgáltatásokhoz:

* [Luis](../luis/luis-concept-best-practices.md) – ajánlott eljárások
* [QnA Maker](../qnamaker/concepts/best-practices.md) ajánlott eljárások

## <a name="see-also"></a>Lásd még

* [Language Understanding (LUIS)](../luis/what-is-luis.md)
* [QnA Maker](../qnamaker/overview/overview.md)
* [Küldési CLI](https://github.com/Microsoft/botbuilder-tools/tree/master/packages/Dispatch)
* [Robot Framework-minták](https://github.com/Microsoft/BotBuilder-Samples)
* [Azure bot Service](/azure/bot-service/bot-service-overview-introduction)
* [Azure bot Emulator](https://github.com/Microsoft/BotFramework-Emulator)
* [Bot Framework webes csevegés](https://github.com/microsoft/BotFramework-WebChat)