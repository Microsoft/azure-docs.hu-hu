---
title: Vállalati fogalmak – LUIS
titleSuffix: Azure Cognitive Services
description: Megismerheti a nagyméretű LUIS-alkalmazások vagy több alkalmazás tervezési fogalmait, beleértve a LUIS-t és a QnA Maker együtt.
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 07/29/2019
ms.openlocfilehash: b8110323afda2ad445ffe279030ee7f3035e2b71
ms.sourcegitcommit: 6386854467e74d0745c281cc53621af3bb201920
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/08/2021
ms.locfileid: "102455399"
---
# <a name="enterprise-strategies-for-a-luis-app"></a>A LUIS-alkalmazás vállalati stratégiái
Tekintse át ezeket a tervezési stratégiákat a vállalati alkalmazáshoz.

## <a name="when-you-expect-luis-requests-beyond-the-quota"></a>Ha a kvótán túli LUIS-kérelmek várhatók

A LUIS havi kvótával és másodpercenkénti kvótával rendelkezik az Azure-erőforrás díjszabási szintjétől függően. 

Ha a LUIS-alkalmazásra vonatkozó kérelmek aránya meghaladja az engedélyezett [kvóta mértékét](https://azure.microsoft.com/pricing/details/cognitive-services/language-understanding-intelligent-services/), a következőket teheti:

* Terjessze a terhelést több LUIS-alkalmazásba [ugyanazzal az alkalmazás-definícióval](#use-multiple-apps-with-same-app-definition). Ez magában foglalja a LUIS egy [tárolóból](luis-container-howto.md)való futtatását is. 
* Hozzon létre és [rendeljen több kulcsot](#assign-multiple-luis-keys-to-same-app) az alkalmazáshoz. 

### <a name="use-multiple-apps-with-same-app-definition"></a>Több alkalmazás használata ugyanazzal az alkalmazás-definícióval
Exportálja az eredeti LUIS alkalmazást, majd importálja újra az alkalmazást külön alkalmazásokba. Minden alkalmazás saját alkalmazás-AZONOSÍTÓval rendelkezik. Ha közzéteszi, ahelyett, hogy ugyanazt a kulcsot használja az összes alkalmazásban, hozzon létre külön kulcsot minden alkalmazáshoz. A terhelés elosztása minden alkalmazásban, hogy egyetlen alkalmazás se legyen túlterhelve. [Application Insights](./luis-csharp-tutorial-bf-v4.md) hozzáadása a használat figyeléséhez. 

Ahhoz, hogy ugyanazt a legfelső szintű szándékot kapja az összes alkalmazás között, győződjön meg arról, hogy az első és a második cél közötti leképezési előrejelzés elég nagy ahhoz, hogy a LUIS ne legyen zavaros, így különböző eredményekkel szolgálhat az alkalmazások között a hosszúságú kimondott szöveg kisebb változataihoz. 

A testvéri alkalmazások betanításakor ügyeljen arra, hogy az [összes adattal betanítsa](luis-how-to-train.md#train-with-all-data).

Egyetlen alkalmazást jelöljön ki főkiszolgálóként. A felülvizsgálatra javasolt összes hosszúságú kimondott szöveg hozzá kell adni a Master alkalmazáshoz, majd vissza kell térnie az összes többi alkalmazáshoz. Ez vagy az alkalmazás teljes exportálása, vagy a hosszúságú kimondott szöveg a főkiszolgálóról a gyermekeknek való betöltése. A betöltés történhet a [Luis](luis-reference-regions.md) webhelyről vagy a SZERZŐi API-ból [egyetlen](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c08) kiíráshoz vagy egy [köteghez](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c09). 

Ütemezzen rendszeres áttekintést, például kéthetente, az aktív tanulás [végpont-hosszúságú kimondott szöveg](luis-how-to-review-endpoint-utterances.md) , majd végezze el a betanítást és az újbóli közzétételt. 

### <a name="assign-multiple-luis-keys-to-same-app"></a>Több LUIS-kulcs társítása ugyanahhoz az alkalmazáshoz
Ha a LUIS-alkalmazás több végponti találatot kap, mint amennyit az egyetlen kulcs kvótája engedélyez, hozzon létre és rendeljen hozzá további kulcsokat a LUIS-alkalmazáshoz. Hozzon létre egy Traffic Managert vagy terheléselosztó-t, hogy a végponton keresztüli lekérdezéseket kezelhesse a végponti kulcsok között. 

## <a name="when-your-monolithic-app-returns-wrong-intent"></a>Ha a monolit alkalmazás nem megfelelő szándékot ad vissza
Ha az alkalmazás célja, hogy előre megjósolja a felhasználói hosszúságú kimondott szöveg széles körét, érdemes lehet megvalósítani a [küldő modellt](#dispatch-tool-and-model). A monolit alkalmazások feldarabolása lehetővé teszi a LUIS számára, hogy a fölérendelt alkalmazásban és a gyermek alkalmazásokban való összetévesztés helyett a leképezések közötti adatfelismerést is sikeresen kihasználja. 

Ütemezze a [végpontok hosszúságú kimondott szöveg rendszeres áttekintését](luis-how-to-review-endpoint-utterances.md) az aktív tanuláshoz, például kéthetente, majd az újratanításhoz és az újbóli közzétételhez. 

## <a name="when-you-need-to-have-more-than-500-intents"></a>Ha több mint 500 szándékkal kell rendelkeznie
Tegyük fel, hogy olyan Office-Segédet fejleszt, amelynek több mint 500 szándéka van. Ha az 200 szándéka az ütemezési értekezletekhez kapcsolódik, a 200 emlékeztetőket tartalmaz, 200 a munkatársakkal kapcsolatos információk beszerzéséről, az 200 pedig az e-mailek küldéséhez, a csoportos leképezésekhez, hogy az egyes csoportok egyetlen alkalmazásban legyenek, majd hozzon létre egy legfelső szintű alkalmazást, amely tartalmazza az egyes leképezéseket. A legfelső szintű alkalmazás létrehozásához használja a [küldő modellt](#dispatch-tool-and-model) . Ezután módosítsa a robotot a lépcsőzetes hívás használatára a [küldő modell oktatóanyagában](/azure/bot-service/bot-builder-tutorial-dispatch?tabs=cs&view=azure-bot-service-4.0)látható módon. 

## <a name="when-you-need-to-combine-several-luis-and-qna-maker-apps"></a>Ha több LUIS-és QnA-készítő alkalmazást is össze kell kapcsolni
Ha több LUIS-és QnA-készítő alkalmazásra van szüksége, amelyeknek egy robotra kell válaszolnia, használja a [küldő modellt](#dispatch-tool-and-model) a legfelső szintű alkalmazás létrehozásához.  Ezután módosítsa a robotot a lépcsőzetes hívás használatára a [küldő modell oktatóanyagában](/azure/bot-service/bot-builder-tutorial-dispatch?tabs=cs&view=azure-bot-service-4.0)látható módon. 

## <a name="dispatch-tool-and-model"></a>Küldő eszköz és modell
A [botbuilder témakörben-eszközökben](https://github.com/Microsoft/botbuilder-tools) található [küldő][dispatch-tool] parancssori eszköz használatával több Luis-és/vagy QnA Maker-alkalmazást egyesítheti egy szülő Luis-alkalmazásba. Ez a módszer lehetővé teszi, hogy egy szülő tartománnyal rendelkezzen, beleértve az összes tárgyat és a különböző alárendelt tartományokat különálló alkalmazásokban. 

![A küldő architektúra fogalmi képe](./media/luis-concept-enterprise/dispatch-architecture.png)

A szülőtartomány megjegyezte a LUIS-ben egy nevű verziót `Dispatch` az alkalmazások listájában. 

A csevegési robot megkapja a megnyilatkozás, majd az elküldés a szülő LUIS alkalmazást az előrejelzéshez. A fölérendelt alkalmazás első előre jelzett szándéka határozza meg, hogy melyik LUIS Child-alkalmazást hívják tovább. A csevegési robot részletesebb előrejelzést küld a gyermek alkalmazásnak.

Ismerje meg, hogy a rendszer hogyan hívja meg ezt a hierarchiát a Bot Builder v4 [diszpécser-Application-oktatóanyagból](/azure/bot-service/bot-builder-tutorial-dispatch?tabs=cs&view=azure-bot-service-4.0).  

### <a name="intent-limits-in-dispatch-model"></a>Leképezési korlátok a küldő modellben
A küldő alkalmazásnak 500 elküldési forrása van, amely 500 szándékkal egyenértékű, maximális értékként. 

## <a name="more-information"></a>További információ

* [Bot Framework SDK](https://github.com/Microsoft/botframework)
* [Útmutató a küldő modellhez](/azure/bot-service/bot-builder-tutorial-dispatch?tabs=cs&view=azure-bot-service-4.0)
* [Küldési CLI](https://github.com/Microsoft/botbuilder-tools)
* Küldési modell bot Sample- [.net](https://github.com/microsoft/BotBuilder-Samples/tree/master/samples/csharp_dotnetcore/14.nlp-with-dispatch), [Node.js](https://github.com/microsoft/BotBuilder-Samples/tree/master/samples/javascript_nodejs/14.nlp-with-dispatch)

## <a name="next-steps"></a>Következő lépések

* Útmutató [a Batch teszteléséhez](luis-how-to-batch-test.md)

[dispatcher-application-tutorial]: /azure/bot-service/bot-builder-tutorial-dispatch
[dispatch-tool]: https://aka.ms/dispatch-tool