---
title: Az Azure Communication Services bemutatása
description: Ismerje meg, hogy az Azure kommunikációs szolgáltatásai hogyan segíthetnek a valós idejű kommunikációban gazdag felhasználói élmények fejlesztésében.
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 03/10/2021
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: 0efdf48e78d0cc48e288bea354f5de5f9635c760
ms.sourcegitcommit: 5fd1f72a96f4f343543072eadd7cdec52e86511e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/01/2021
ms.locfileid: "106106840"
---
# <a name="what-is-azure-communication-services"></a>Az Azure Communication Services bemutatása

> [!IMPORTANT]
> Az Azure kommunikációs szolgáltatásokkal felépített alkalmazások a Microsoft csapatával tudnak kommunikálni. További tudnivalókért tekintse meg a [csapat együttműködési](./quickstarts/voice-video-calling/get-started-teams-interop.md) dokumentációját.


Az Azure kommunikációs szolgáltatások lehetővé teszik a valós idejű multimédiás hang-, videó-és telefonos IP-alapú kommunikációs funkciók egyszerű hozzáadását az alkalmazásaihoz. A kommunikációs szolgáltatások SDK-kódtárai lehetővé teszik a csevegési és SMS-funkciók hozzáadását a kommunikációs megoldásokhoz.

<br>

> [!VIDEO https://www.youtube.com/embed/apBX7ASurgM]

<br>
<br>

A kommunikációs szolgáltatásokat hang-, videó-, szöveg-és adatkommunikációra használhatja számos esetben:

- Böngésző – böngésző, böngésző – alkalmazás és alkalmazás – alkalmazás kommunikáció
- Botokkal vagy más szolgáltatásokkal kommunikáló felhasználók
- A nyilvános kapcsolóval rendelkező telefonos hálózaton keresztül kommunikáló felhasználók és robotok

A vegyes forgatókönyvek támogatottak. A kommunikációs szolgáltatások alkalmazásai például a böngészőkből és a hagyományos telefonos eszközökből származó felhasználókkal is rendelkezhetnek. A kommunikációs szolgáltatások a Azure Bot Service használatával is kombinálhatók a robot által vezérelt interaktív hangválasztó (IVR) rendszerek létrehozásához.

## <a name="common-scenarios"></a>Gyakori forgatókönyvek

Az alábbi források nagyszerű helyet biztosítanak az Azure kommunikációs szolgáltatásainak megkezdéséhez.
<br>

| Erőforrás                               |Leírás                           |
|---                                    |---                                   |
|**[Communication Services-erőforrás létrehozása](./quickstarts/create-communication-resource.md)**|Az Azure kommunikációs szolgáltatások használatának megkezdéséhez használja a Azure Portal vagy a Communication Services SDK-t az első kommunikációs szolgáltatások erőforrásának kiépítéséhez. A kommunikációs szolgáltatások erőforrás-kapcsolati karakterlánca után kiépítheti az első felhasználói hozzáférési jogkivonatait.|
|**[Telefonszám beszerzése](./quickstarts/telephony-sms/get-phone-number.md)**|Az Azure kommunikációs szolgáltatásokkal telefonszámokat helyezhet üzembe és szabadíthat fel. Ezeket a telefonszámokat a kimenő hívások kezdeményezésére és SMS kommunikációs megoldások létrehozására lehet használni.|

A kommunikációs szolgáltatások erőforrásának létrehozása után megkezdheti az ügyfél-forgatókönyvek, például a hang-és videohívás-hívások vagy a szöveges csevegés létrehozását.

| Erőforrás                               |Leírás                           |
|---                                    |---                                   |
|**[Az első felhasználói hozzáférési jogkivonat létrehozása](./quickstarts/access-tokens.md)**|A felhasználói hozzáférési jogkivonatok segítségével hitelesítheti szolgáltatásait az Azure kommunikációs szolgáltatások erőforrásán. Ezek a tokenek a kommunikációs szolgáltatások SDK-val lettek kiépítve és kibocsátva.|
|**[Ismerkedés a hang-és videohívások meghívásával](./quickstarts/voice-video-calling/getting-started-with-calling.md)**| Az Azure kommunikációs szolgáltatások lehetővé teszik hang-és videohívás hozzáadását az alkalmazásokhoz a hívó SDK használatával. Ezt a kódtárat a WebRTC működteti, és lehetővé teszi, hogy az alkalmazásokon belül társközi, multimédia és valós idejű kommunikációt hozzon létre.|
|**[Hívási alkalmazás csatlakoztatása Teams-értekezlethez](./quickstarts/voice-video-calling/get-started-teams-interop.md)**|Az Azure kommunikációs szolgáltatásaival egyéni Meeting-élmények hozhatók létre, amelyek a Microsoft csapatait használják. A kommunikációs szolgáltatási megoldás (ok) felhasználóinak interakcióba léphetnek a csapat résztvevőinek hang-, videó-, csevegési és nyomtatómegosztást.|
|**[Ismerkedés a csevegéssel](./quickstarts/chat/get-started.md)**|Az Azure Communication Services csevegési SDK használatával valós idejű csevegést építhet be alkalmazásaiba.|
|**[SMS küldése az alkalmazásból](./quickstarts/telephony-sms/send.md)**|Az Azure Communication Services SMS SDK lehetővé teszi, hogy SMS-üzeneteket küldjön és fogadjon .NET-és JavaScript-alkalmazásaiból.|

## <a name="samples"></a>Példák

Az alábbi példák az Azure kommunikációs szolgáltatások SDK-kódtárainak végpontok közötti kihasználtságát szemléltetik. Nyugodtan használhatja ezeket a mintákat a saját kommunikációs szolgáltatások megoldásának elindításához.
<br>

| Minta neve                               | Leírás                           |
|---                                    |---                                   |
|**[A Hero-mintát hívó csoport](./samples/calling-hero-sample.md)**|Megtudhatja, hogyan használható a kommunikációs szolgáltatások SDK-kódtára egy csoportos hívási élmény létrehozásához.|
|**[A csoportos csevegési hős mintája](./samples/chat-hero-sample.md)**|Ismerje meg, hogyan használható a kommunikációs szolgáltatások SDK-kódtára csoportos csevegési élmény létrehozásához.|


## <a name="platforms-and-sdk-libraries"></a>Platformok és SDK-kódtárak

Az alábbi források segítséget nyújtanak az Azure Communication Services SDK-kódtárak megismeréséhez:

| Erőforrás                               | Leírás                           |
|---                                    |---                                   |
|**[SDK-kódtárak és REST API-k](./concepts/sdk-options.md)**|Az Azure kommunikációs szolgáltatások funkcióit koncepcionálisan hat területre rendezik, amelyeket az SDK képvisel. A valós idejű kommunikációs igények alapján eldöntheti, hogy melyik SDK-kódtárat szeretné használni.|
|**[Az SDK meghívása – áttekintés](./concepts/voice-video-calling/calling-sdk-features.md)**|Tekintse át a kommunikációs szolgáltatásokat hívó SDK áttekintése című témakört.|
|**[A csevegési SDK áttekintése](./concepts/chat/sdk-features.md)**|Tekintse át a kommunikációs szolgáltatások csevegési SDK áttekintése című témakört.|
|**[SMS SDK – áttekintés](./concepts/telephony-sms/sdk-features.md)**|Tekintse át a kommunikációs szolgáltatások SMS SDK áttekintése című témakört.|

## <a name="other-microsoft-communication-services"></a>Egyéb Microsoft kommunikációs szolgáltatások

Két másik Microsoft kommunikációs termék közül választhat, amelyek a kommunikációs szolgáltatásokkal közvetlenül nem működnek együtt:

 - [Microsoft Graph a Felhőbeli kommunikációs API](/graph/cloud-communications-concept-overview) -k lehetővé teszik a szervezetek számára, hogy Microsoft 365 licenccel rendelkező Azure Active Directory felhasználókhoz kötődő kommunikációs élményt hozzanak létre. Ez ideális a Azure Active Directoryhoz kötött alkalmazások számára, illetve a termelékenységi tapasztalatok kibővítésére a Microsoft Teams szolgáltatásban. Emellett API-k is létrehozhatnak alkalmazásokat és testreszabásokat a [csapatok felhasználói felületén belül.](/microsoftteams/platform/?preserve-view=true&view=msteams-client-js-latest)

 - Az [Azure PlayFab fél](/gaming/playfab/features/multiplayer/networking/) leegyszerűsíti az alacsony késésű csevegés és az adatkommunikáció hozzáadását a játékokhoz. A kommunikációs szolgáltatásokkal rendelkező Power Gaming chat-és hálózatkezelési rendszerek esetében a PlayFab egy testre szabott lehetőség, és az Xbox-on is ingyenes.


## <a name="next-steps"></a>Következő lépések

 - [Communication Services-erőforrás létrehozása](./quickstarts/create-communication-resource.md)
