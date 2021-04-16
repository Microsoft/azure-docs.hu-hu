---
title: Language Understanding (LUIS) áttekintése
description: Language Understanding (LUIS) – egy felhőalapú API-szolgáltatás, amely gépi tanulást használ a beszélgetésre, természetes nyelvre a jelentés előrejelzése és az információk kinyere érdekében.
keywords: Azure, mesterséges intelligencia, mesterséges intelligencia, természetes nyelvi feldolgozás, nlp, természetes nyelvi megértés, nlu, LUIS, beszélgetési AI, ai csevegőrobot, nlp ai, azure luis
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: overview
ms.date: 04/13/2021
ms.custom: cog-serv-seo-aug-2020
ms.openlocfilehash: f46586b3f120cf191d88b7de9cf8686ca9b16cca
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2021
ms.locfileid: "107503769"
---
# <a name="what-is-language-understanding-luis"></a>Mi a Language Understanding (LUIS)?

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

Language Understanding (LUIS) egy felhőalapú beszélgetési AI-szolgáltatás, amely egyéni gépi tanulási intelligenciát alkalmaz a felhasználó beszélgetési, természetes nyelvű szövegére az általános jelentés előrejelzése és a releváns, részletes információk kihozása érdekében. A LUIS az egyéni [portálon,][endpoint-apis] [API-kon](https://www.luis.ai)és [SDK-ügyfélkódtárakon keresztül biztosít hozzáférést.](client-libraries-rest-api.md)

Első alkalommal a felhasználók az [](sign-in-luis-portal.md "bejelentkezés a LUIS portálra") alábbi lépésekkel jelentkeznek be a LUIS-portálra. Első lépésként kipróbálhatja az előre összeállított [LUIS-tartományi](luis-get-started-create-app.md) alkalmazásokat, vagy felépítheti az [alkalmazást.](get-started-portal-build-app.md)

Ez a dokumentáció a következő cikktípusokat tartalmazza:  

* [**A rövid útmutatók**](luis-get-started-create-app.md) első lépésekre vonatkozó utasítások, amelyek végigvezetik a szolgáltatásnak indított kérések folyamatán.  
* [**Az útmutatók a**](luis-how-to-start-new-app.md) szolgáltatás pontosabb vagy testre szabottabb módon való használatával kapcsolatos utasításokat tartalmaznak.  
* [**A fogalmak**](artificial-intelligence.md) részletes magyarázatot nyújtanak a szolgáltatás funkcióiról és funkcióiról.  
* [**Az oktatóanyagok**](tutorial-intents-only.md) hosszabb útmutatók, amelyekből megtudhatja, hogyan használhatja a szolgáltatást összetevőként a szélesebb körű üzleti megoldásokban.  

## <a name="what-does-luis-offer"></a>Mit kínál a LUIS? 

* **Egyszerűség:** A LUIS kiszervezést ad ki a házon található AI-szakértelem vagy bármilyen korábbi gépi tanulási tudás miatt. Mindössze néhány kattintással saját beszélgetési AI-alkalmazást is felépíthet. Egyéni alkalmazását az egyik rövid útmutatónk alapján hozhatja [létre,](get-started-portal-build-app.md)vagy használhatja az egyik előre összeállított [tartományalkalmazásunkat.](luis-get-started-create-app.md)
* **Biztonság, adatvédelem és megfelelőség:** Az Azure-infrastruktúrán keresztül a LUIS nagyvállalati szintű biztonságot, adatvédelmet és megfelelőséget kínál. Adatai továbbra is az Ön adatai maradnak; az adatokat bármikor törölheti. Az adatok titkosítva vannak, amíg a tárolóban vannak. Erről [itt](https://azure.microsoft.com/support/legal/cognitive-services-compliance-and-privacy) olvashat részletesebb tájékoztatást.
* **Integráció:** a LUIS-alkalmazás egyszerűen integrálható más szolgáltatásokkal Microsoft-szolgáltatások például a [Microsoft Bot Framework,](https://docs.microsoft.com/composer/tutorial/tutorial-luis) [a QnA Maker](../QnAMaker/choose-natural-language-processing-service.md)és a Speech [service szolgáltatással.](../Speech-Service/quickstarts/intent-recognition.md)


## <a name="luis-scenarios"></a>LUIS-forgatókönyvek
* [Nagyvállalati szintű](https://docs.microsoft.com/azure/architecture/reference-architectures/ai/conversational-bot)beszélgetőrobot összeállítása: Ez a referenciaarchitektúra azt ismerteti, hogyan lehet nagyvállalati szintű beszélgetőrobotot (csevegőrobotot) építeni az Azure Bot Framework.
* [Kereskedelmi csevegőrobot:](https://docs.microsoft.com/azure/architecture/solution-ideas/articles/commerce-chatbot)Együttesen a Azure Bot Service és Language Understanding szolgáltatás lehetővé teszi a fejlesztők számára, hogy beszélgetési felületeket hozzanak létre különböző forgatókönyvekhez, például banki, utazási és szórakoztató szolgáltatásokhoz.
* [IoT-eszközök](https://docs.microsoft.com/azure/architecture/solution-ideas/articles/iot-controlling-devices-with-voice-assistant)vezérlése hangsegéddel: Zökkenőmentes beszélgetési felületeket hozhat létre az összes internetről elérhető eszközzel , a csatlakoztatott tévékészülékről vagy a csatlakoztatott gyárban lévő eszközökre.


## <a name="application-development-life-cycle"></a>Alkalmazásfejlesztés életciklusa

![LUIS-alkalmazásfejlesztési életciklus](./media/luis-overview/luis-dev-lifecycle.png "LUIS-alkalmazás-develooment életciklus")

-   **Tervezés:** Azonosítsa azokat a forgatókönyveket, amelyekhez a felhasználók az alkalmazást használják. Határozza meg a felismerni szükséges műveleteket és releváns információkat.
-   **Build**: Használja a szerzői erőforrást az alkalmazás fejlesztéséhez. Először határozza meg a [szándékokat és](luis-concept-intent.md) [entitásokat.](luis-concept-entity-types.md) Ezután adjon hozzá betanító [kimondott szövegeket](luis-concept-utterance.md) minden szándékhoz. 
-   **Tesztelés és** fejlesztés: Tesztelje a modellt más kimondott szövegekkel, hogy jobban át tudja látni az alkalmazás viselkedését, és eldöntheti, hogy van-e szükség fejlesztésre. Az alkalmazást a következő ajánlott eljárások szerint [fejlesztheti:](luis-concept-best-practices.md). 
-   **Közzététel:** Üzembe helyezheti az alkalmazást előrejelzésre, és lekérdezheti a végpontot az előrejelzési erőforrás használatával. A szerzői és előrejelzési erőforrásokról itt olvashat [bővebben.](luis-how-to-azure-subscription.md#luis-resources) 
-   **Csatlakozás:** Kapcsolódhat más szolgáltatásokhoz, például a [Microsoft Bot Frameworkhez,](https://docs.microsoft.com/composer/tutorial/tutorial-luis) [a QnA Maker](../QnAMaker/choose-natural-language-processing-service.md)és a [Speech service-hez.](../Speech-Service/quickstarts/intent-recognition.md) 
-   **Pontosítás:** [A végponti kimondott szövegeket áttekintve](luis-concept-review-endpoint-utterances.md) valós példákkal fejleszti az alkalmazást

Az alkalmazás megtervezéséről és építésről itt olvashat [bővebben.](luis-how-plan-your-app.md)

## <a name="next-steps"></a>Következő lépések

* [A szolgáltatás és](whats-new.md "Újdonságok") a dokumentáció újdonsága
* [LUIS-alkalmazások felépítése](tutorial-intents-only.md)
* [API-leírások][endpoint-apis]
* [Ajánlott eljárások](luis-concept-best-practices.md)
* [Fejlesztői erőforrások a](developer-reference-resource.md "Fejlesztői erőforrások") LUIS-hoz.
* [Tervezze meg az alkalmazást](luis-how-plan-your-app.md "Az alkalmazás megtervezése") [szándékokkal és](luis-concept-intent.md "Cél") [entitásokkal.](luis-concept-entity-types.md "Entitások")

[bot-framework]: /bot-framework/
[flow]: /connectors/luis/
[authoring-apis]: https://go.microsoft.com/fwlink/?linkid=2092087
[endpoint-apis]: https://go.microsoft.com/fwlink/?linkid=2092356
[qnamaker]: https://qnamaker.ai/
