---
title: Az Azure Communication Services bemutatása
description: Megtudhatja, Azure Communication Services segítségével hogyan fejleszthet gazdag felhasználói élményt valós idejű kommunikációval.
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 03/10/2021
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: 8c2559315e3bfffc41c138be6826adae95dd7b07
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/17/2021
ms.locfileid: "107588105"
---
# <a name="what-is-azure-communication-services"></a>Az Azure Communication Services bemutatása

Azure Communication Services lehetővé teszi, hogy egyszerűen adjon hozzá valós idejű hang-, videó- és telefon-kommunikációt az alkalmazásokhoz. Communication Services az SDK-k lehetővé teszik SMS-funkciók hozzáadását a kommunikációs megoldásokhoz. Azure Communication Services identitástól független, és teljes körűen szabályozhatja a végfelhasználók azonosításának és hitelesítésének a mikéntét. Az embereket csatlakoztathatja a kommunikációs adatsíkhoz vagy szolgáltatásokhoz (robotokhoz).

Az alkalmazások a következők:

- **Felhasználó és felhasználó vállalkozás (B2C).** A vállalat alkalmazottai és szolgáltatásai hang-, videó- és rich text csevegéssel kommunikálhatnak a felhasználókkal egy egyéni böngészőben vagy mobilalkalmazásban. A szervezetek SMS-üzeneteket küldhetnek és fogadhatnak, vagy az Azure-ral beszerzett telefonszámmal használhatnak interaktív hangalapú válaszrendszert (IVR-t). [A Microsoft Teams-integráció lehetővé teszi](./quickstarts/voice-video-calling/get-started-teams-interop.md) a felhasználók számára, hogy csatlakozzanak az alkalmazottak által tartott Teams-értekezletekhez; ideális távoli egészségügyi, banki és terméktámogatási forgatókönyvekhez, ahol az alkalmazottak már ismerik a Teamst.
- **Fogyasztóról fogyasztóra.** Építsen vonzó közösségi téreket a felhasználók közötti kommunikációhoz hang-, videó- és rich text csevegéssel. Bármilyen típusú felhasználói felület építhető Azure Communication Services-kre, de teljes alkalmazásminták és felhasználói felületi eszközök állnak rendelkezésre a gyors első lépésekhez.

További tudnivalókért tekintse meg [a Microsoft Mechanics-videót](https://www.youtube.com/watch?v=apBX7ASurgM) vagy az alább hivatkozott erőforrásokat.

## <a name="common-scenarios"></a>Gyakori forgatókönyvek

<br>

| Erőforrás                               |Leírás                           |
|---                                    |---                                   |
|**[Communication Services-erőforrás létrehozása](./quickstarts/create-communication-resource.md)**|Az első Azure Communication Services az Azure Portal vagy Communication Services SDK használatával megkezdheti a Communication Services üzembe. Ha megvan a Communication Services erőforrás kapcsolati sztringje, kiépítheti az első felhasználói hozzáférési jogkivonatát.|
|**[Telefonszám lekért száma](./quickstarts/telephony-sms/get-phone-number.md)**|A telefonszámokat Azure Communication Services és kiadhatja. Ezekkel a telefonszámokkal telefonhívásokat kezdeményezhet vagy fogadhat, és SMS-megoldásokat építhet ki.|
|**[SMS küldése az alkalmazásból](./quickstarts/telephony-sms/send.md)**|A Azure Communication Services SMS SDK szolgáltatásalkalmazások SMS-üzenetek küldését és fogadását használja.|

Miután létrehozta a Communication Services, elkezdhet ügyfélforgatókönyveket létrehozni, például hang- és videóhívásokat vagy szöveges csevegést:

| Erőforrás                               |Leírás                           |
|---                                    |---                                   |
|**[Az első felhasználói hozzáférési jogkivonat létrehozása](./quickstarts/access-tokens.md)**|A felhasználói hozzáférési jogkivonatokkal hitelesíthetők az ügyfelek a Azure Communication Services erőforráson. Ezek a jogkivonatok a Communication Services SDK-val vannak kiépítve és újra kiépítve.|
|**[Hang- és videóhívás – első lépések](./quickstarts/voice-video-calling/getting-started-with-calling.md)**| Azure Communication Services lehetővé teszi hang- és videóhívások hozzáadását a böngészőhöz vagy a natív alkalmazásokhoz a hívó SDK használatával. |
|**[Hívási alkalmazás csatlakoztatása Teams-értekezlethez](./quickstarts/voice-video-calling/get-started-teams-interop.md)**|Azure Communication Services a Microsoft Teamsben egyéni értekezleteket hozhat létre. A felhasználói Communication Services a Teams-résztvevőkkel hang-, videó-, csevegés- és képernyőmegosztáson keresztül kommunikálhatnak.|
|**[Csevegés – első lépések](./quickstarts/chat/get-started.md)**|A Azure Communication Services Chat SDK használatával gazdag, valós idejű szöveges csevegést adhat az alkalmazásokhoz.|

## <a name="samples"></a>Példák

Az alábbi minták a végpontok között mutatják be a Azure Communication Services. Ezekkel a mintákkal rendszerindítást is Communication Services megoldásait.
<br>

| Mintanév                               | Description                           |
|---                                    |---                                   |
|**[A főképmintát hívó csoport](./samples/calling-hero-sample.md)**| Töltse le a böngészők, iOS- és Android-eszközök csoportos hívására tervezett alkalmazásmintát. |
|**[A csoportos csevegés főképmintája](./samples/chat-hero-sample.md)**| Töltse le a böngészők csoportos szöveges csevegéséhez tervezett alkalmazásmintát. |


## <a name="platforms-and-sdk-libraries"></a>Platformok és SDK-kódtárak

Az alábbi forrásokkal Azure Communication Services további információt az Azure Communication Services DK-król. A REST API-k a legtöbb funkcióhoz elérhetők, ha saját ügyfeleket szeretne felépíteni, vagy más módon szeretné elérni a szolgáltatást az interneten keresztül.

| Erőforrás                               | Leírás                           |
|---                                    |---                                   |
|**[SDK-kódtárak és REST API-k](./concepts/sdk-options.md)**|Azure Communication Services képességek fogalmilag hat területre vannak rendezve, amelyek mindegyikét egy SDK képviseli. A valós idejű kommunikációs igények alapján eldöntheti, hogy mely SDK-kódtárakat használja.|
|**[Az SDK hívásának áttekintése](./concepts/voice-video-calling/calling-sdk-features.md)**|Tekintse át a Communication Services SDK áttekintését.|
|**[A csevegési SDK áttekintése](./concepts/chat/sdk-features.md)**|Tekintse át a Communication Services Chat SDK áttekintését.|
|**[Az SMS SDK áttekintése](./concepts/telephony-sms/sdk-features.md)**|Tekintse át a Communication Services SMS SDK áttekintését.|

## <a name="other-microsoft-communication-services"></a>Egyéb Microsoft-Communication Services

Két másik Microsoft kommunikációs terméket is érdemes lehet használni, amelyek jelenleg nem Communication Services egymással:

 - [Microsoft Graph felhőalapú kommunikációs API-k](/graph/cloud-communications-concept-overview) lehetővé teszik, hogy a szervezetek kommunikációs élményt építsenek Azure Active Directory licenccel rendelkező Microsoft 365 kapcsolatban. Ez ideális olyan alkalmazásokhoz, amelyek Azure Active Directory, vagy ahol a Microsoft Teamsben szeretné kiterjeszteni a hatékonyságot. Emellett API-k is rendelkezésre állnak, amelyek alkalmazásokat és testreszabásokat hoznak létre a [Teams-felhasználói élményben.](/microsoftteams/platform/?preserve-view=true&view=msteams-client-js-latest)

 - [Az Azure PlayFab Party](/gaming/playfab/features/multiplayer/networking/) leegyszerűsíti a kis késleltetésű csevegés és adatkommunikáció hozzáadását a játékokhoz. Bár a játékokkal csevegési és hálózati rendszereket is Communication Services, a PlayFab egy személyre szabott, ingyenes Xbox-megoldás.


## <a name="next-steps"></a>Következő lépések

 - [Communication Services-erőforrás létrehozása](./quickstarts/create-communication-resource.md)
