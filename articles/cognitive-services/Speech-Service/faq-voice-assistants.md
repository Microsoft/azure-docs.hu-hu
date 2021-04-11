---
title: Hangvezérelt asszisztensek – gyakori kérdések
titleSuffix: Azure Cognitive Services
description: Választ kaphat a hangasszisztensekkel kapcsolatos legnépszerűbb kérdésekre egyéni parancsok vagy a közvetlen vonalas beszéd csatorna használatával.
services: cognitive-services
author: trrwilson
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/05/2019
ms.author: travisw
ms.openlocfilehash: 511eb12df511fd037fc0b5bec701c0cc5c29bad2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "102617777"
---
# <a name="voice-assistants-frequently-asked-questions"></a>Hangvezérelt asszisztensek – gyakori kérdések

Ha a jelen dokumentumban nem talál választ a kérdéseire, tekintse meg az [egyéb támogatási lehetőségeket](../cognitive-services-support-options.md?context=%2fazure%2fcognitive-services%2fspeech-service%2fcontext%2fcontext%253fcontext%253d%2fazure%2fcognitive-services%2fspeech-service%2fcontext%2fcontext).

## <a name="general"></a>Általános kérdések

**K: Mi az a hangsegéd?**

**A:** A Cortana hasonlóan a hangsegéd olyan megoldás, amely figyeli a felhasználó által kimondott hosszúságú kimondott szöveg, elemzi a jelentés tartalmának tartalmát, a teljes szándékra válaszul egy vagy több műveletet hajt végre, majd választ ad arra a felhasználóra, aki gyakran tartalmaz egy szóbeli összetevőt. Ez egy "hangvételt, hangvételt" kínál a rendszerekkel való interakcióhoz. a hangsegéd szerzők létrehozhatnak egy on-Device alkalmazást a `DialogServiceConnector` SPEECH SDK használatával, amely az [Egyéni parancsokkal](custom-commands.md) létrehozott asszisztenssel vagy a bot-keretrendszer [közvetlen beszédfelismerési](direct-line-speech.md) csatornájának használatával kommunikál. Ezek az asszisztensek egyéni kulcsszavakat, egyéni beszédeket és egyéni hangvételt használhatnak, hogy a márka vagy termék számára szabott élményt nyújtsanak.

**K: használhatok egyéni parancsokat vagy közvetlen vonalas beszédet? Mi a különbség?**

**A:** az [egyéni parancsok](custom-commands.md) egy kevésbé összetett eszköz, amellyel könnyedén létrehozhatók és beállíthatók a feladat-végrehajtási forgatókönyvekhez jól illeszkedő asszisztensek. A [közvetlen vonalas beszéd](direct-line-speech.md) gazdagabb és kifinomultabb képességeket biztosít, amelyek lehetővé teszik robusztus társalgási forgatókönyvek használatát. További információért lásd a [segéd-megoldások összehasonlítását](voice-assistants.md#choosing-an-assistant-solution) ismertető témakört.

**K: Hogyan első lépések?**

**A:** A legjobb módszer az egyéni parancsok (előzetes verzió) alkalmazás vagy az alapszintű bot Framework bot létrehozásához.

- [Egyéni parancsok (előzetes verzió) alkalmazás létrehozása](./quickstart-custom-commands-application.md)
- [Alapszintű robot-keretrendszer robot létrehozása](/azure/bot-service/bot-builder-tutorial-basic-deploy)
- [Robot összekötése a közvetlen vonalas beszéd csatornával](/azure/bot-service/bot-service-channel-connect-directlinespeech)

## <a name="debugging"></a>Hibakeresés

**K: hol van a Channel Secret?**

**A:** Ha a Direct line Speech előzetes verzióját használta, vagy a kapcsolódó dokumentációt olvasta, előfordulhat, hogy a Direct line Speech Channel regisztrációs lapján talál egy titkos kulcsot. A `DialogServiceConfig` SPEECH SDK v 1.7 `FromBotSecret` -es gyári metódusa is ezt az értéket várja.

A Direct line Speech legújabb verziója leegyszerűsíti a robot egy eszközről való kapcsolatfelvételi folyamatát. A Channel Registration (csatorna regisztrálása) lapon a felül lévő legördülő lista a Direct line Speech Channel-regisztrációt egy beszédfelismerési erőforrással társítja. Ha társítva van, a v 1.8 Speech SDK olyan `BotFrameworkConfig::FromSubscription` gyári metódust tartalmaz, amely a `DialogServiceConnector` -hoz való kapcsolatfelvételhez az előfizetéshez társított robotot fogja beállítani.

Ha továbbra is áttelepíti az ügyfélalkalmazás alkalmazást a v 1.7-ről a v 1.8-ra, `DialogServiceConfig::FromBotSecret` akkor továbbra is használhat egy nem üres, nem null értékű értéket a Channel Secret paraméterhez, például a korábban használt titkot. Ezt a rendszer egyszerűen figyelmen kívül hagyja, ha egy újabb csatornás regisztrációhoz társított beszédfelismerési előfizetést használ. Vegye figyelembe, hogy az érték _nem lehet null_ és nem üres, mivel ezeket a rendszer az eszközön a szolgáltatási oldal társítása előtt ellenőrzi.

Részletesebb útmutatásért tekintse meg az [oktatóanyag című szakaszt](tutorial-voice-enable-your-bot-speech-sdk.md#register-the-direct-line-speech-channel) , amely végigvezeti a csatorna regisztrálásán.

**K: 401-es hibaüzenetet kapok a csatlakozáskor, és semmi nem működik. Tudom, hogy a Speech előfizetés kulcsa érvényes. mi történik?**

**A:** Az előfizetésnek a Azure Portal való kezelésekor győződjön meg arról, hogy a **beszédfelismerési** erőforrást (Microsoft. CognitiveServicesSpeechServices, "Speech") és _nem_ a **Cognitive Services** erőforrást (microsoft. CognitiveServicesAllInOne, "All Cognitive Services") használja. Kérjük, tekintse [meg a beszédfelismerési szolgáltatási régió támogatását a hangasszisztensek számára](regions.md#voice-assistants).

![megfelelő előfizetés a közvetlen vonalas beszédhez](media/voice-assistants/faq-supported-subscription.png "kompatibilis beszédfelismerési előfizetés – példa")

**K: az elismerés szövege visszanyerhető az én `DialogServiceConnector` , de "1011" hibaüzenet jelenik meg, és nem a robotom. miért?**

**A:** Ez a hiba kommunikációs problémát jelez a segéd és a hangsegéd szolgáltatás között.

- Egyéni parancsok esetén győződjön meg arról, hogy az egyéni parancsok alkalmazás közzé van téve.
- A közvetlen vonalas beszédhez győződjön meg arról, hogy [csatlakoztatta a robotot a közvetlen vonalas hangcsatornához](/azure/bot-service/bot-service-channel-connect-directlinespeech), [hozzáadta a folyamatos átviteli protokoll támogatását](/azure/bot-service/directline-speech-bot) a robothoz (a kapcsolódó webes szoftvercsatorna-támogatással), majd ellenőrizze, hogy a robot válaszol-e a csatorna bejövő kéréseire.

**K: Ez a kód továbbra sem működik, és/vagy más hibaüzenetet kapok a használatakor `DialogServiceConnector` . Mit tegyek?**

**A:** A fájl alapú naplózás lényegesen több részletet biztosít, és a támogatási kérelmek felgyorsítását is segíti. A funkció engedélyezéséhez tekintse meg a [fájlok naplózásának használata](how-to-use-logging.md)című témakört.

## <a name="next-steps"></a>Következő lépések

- [Hibaelhárítás](troubleshooting.md)
- [Kibocsátási megjegyzések](releasenotes.md)