---
title: Ügyfél- és kiszolgálói architektúra
titleSuffix: An Azure Communication Services concept document
description: Ismerje meg a kommunikációs szolgáltatások architektúráját.
author: mikben
manager: mikben
services: azure-communication-services
ms.author: mikben
ms.date: 09/30/2020
ms.topic: conceptual
ms.service: azure-communication-services
ms.openlocfilehash: 27f444c6889767e0333384a779883805d1e8f348
ms.sourcegitcommit: 8d1b97c3777684bd98f2cfbc9d440b1299a02e8f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/09/2021
ms.locfileid: "102485910"
---
# <a name="client-and-server-architecture"></a>Ügyfél és kiszolgáló architektúrája

[!INCLUDE [Public Preview Notice](../includes/public-preview-include.md)]


<!--
> [!WARNING]
> This document is under construction and needs the following items to be addressed:
> - Need to add security best practices for token management here
> - Reference docs:
> - https://docs.microsoft.com/windows/security/threat-protection/security-policy-settings/create-a-token-object
> - https://docs.microsoft.com/azure/aks/operator-best-practices-identity
> - https://docs.microsoft.com/cloud-app-security/api-tokens?view=gestures-1.0-->

Minden Azure Communication Services-alkalmazáshoz olyan **ügyfélalkalmazások** tartoznak, amelyek **szolgáltatásokat** használnak a személyek közötti kapcsolat megkönnyítésére. Ez az oldal számos különböző forgatókönyvben mutatja be a közös építészeti elemeket.

## <a name="user-access-management"></a>Felhasználói hozzáférés kezelése

Az Azure kommunikációs szolgáltatások ügyféloldali kódtárainak `user access tokens` biztonságosan kell elérniük a kommunikációs szolgáltatások erőforrásait. `User access tokens` egy megbízható szolgáltatásnak kell létrehoznia és felügyelni a jogkivonat bizalmas jellege és a létrehozásához szükséges kapcsolati karakterlánc miatt. A hozzáférési tokenek megfelelő kezelésének elmulasztása további díjakat eredményezhet az erőforrásokkal való visszaélés miatt. Javasoljuk, hogy használjon megbízható szolgáltatást a felhasználók felügyeletéhez. A megbízható szolgáltatás létrehozza a jogkivonatokat, és a megfelelő titkosítás használatával továbbítja azokat az ügyfélnek. A minta architektúra folyamata az alábbiakban található:

:::image type="content" source="../media/scenarios/archdiagram-access.png" alt-text="A felhasználói hozzáférési jogkivonat architektúráját bemutató ábra.":::

További információkért tekintse át a [legjobb Identitáskezelés-kezelési gyakorlatot](../../security/fundamentals/identity-management-best-practices.md)

## <a name="browser-communication"></a>Böngészőalapú kommunikáció

Az Azure Communications JavaScript-ügyféloldali kódtárai lehetővé teszik a webes alkalmazások gazdag szöveg-, hang-és videó-interakcióval való kezelését. Az alkalmazás közvetlenül kommunikál az Azure kommunikációs szolgáltatásokkal az ügyfél-függvénytáron keresztül az adatsíkon való hozzáféréshez és valós idejű szöveg-, hang-és videó-kommunikációhoz. A minta architektúra folyamata az alábbiakban található:

:::image type="content" source="../media/scenarios/archdiagram-browser.png" alt-text="A kommunikációs szolgáltatások böngésző-architektúráját bemutató ábra.":::

## <a name="native-app-communication"></a>Natív alkalmazás kommunikációja

Számos forgatókönyv a legalkalmasabb a natív alkalmazásokhoz. Az Azure kommunikációs szolgáltatások a böngészők közötti és az alkalmazások közötti kommunikációt is támogatják.  Natív alkalmazások létrehozásakor a leküldéses értesítések lehetővé teszik, hogy a felhasználók akkor is fogadhasson hívásokat, ha az alkalmazás nem fut. Az Azure kommunikációs szolgáltatások megkönnyítik az integrált leküldéses értesítéseket a Google Firebase, a Apple Push Notification Service és a Windows leküldéses értesítésekhez. A minta architektúra folyamata az alábbiakban található:

:::image type="content" source="../media/scenarios/archdiagram-app.png" alt-text="A kommunikációs szolgáltatások architektúráját bemutató ábra a natív alkalmazással való kommunikációhoz.":::

## <a name="voice-and-sms-over-the-public-switched-telephony-network-pstn"></a>Hang és SMS a nyilvános kapcsolóval rendelkező telefonos hálózaton (PSTN) keresztül

A telefonos rendszeren keresztüli kommunikáció jelentősen növelheti az alkalmazás elérhetőségét. A PSTN hang-és SMS-forgatókönyvek támogatásához az Azure kommunikációs szolgáltatásokkal közvetlenül a Azure Portal, illetve a REST API-k és az ügyféloldali kódtárak használatával [szerezhetők be telefonszámok](../quickstarts/telephony-sms/get-phone-number.md) . A telefonszámok beszerzése után a rendszer felhasználhatja az ügyfeleket a PSTN-hívással és az SMS-sel együtt a bejövő és kimenő helyzetekben is. A minta architektúra folyamata az alábbiakban található:

> [!Note]
> A nyilvános előzetes verzióban az Egyesült államokbeli telefonszámok kiosztása az Egyesült Államokban és Kanadában található számlázási címmel rendelkező ügyfelek számára érhető el.

:::image type="content" source="../media/scenarios/archdiagram-pstn.png" alt-text="A kommunikációs szolgáltatások PSTN-architektúráját bemutató ábra.":::

A PSTN-telefonszámokkal kapcsolatos további információkért lásd: [telefonszám-típusok](../concepts/telephony-sms/plan-solution.md)

## <a name="humans-communicating-with-bots-and-other-services"></a>A botokkal és más szolgáltatásokkal kommunikáló emberek

Az Azure kommunikációs szolgáltatások az Azure kommunikációs szolgáltatások adatsíkjával közvetlenül hozzáférő szolgáltatásokkal támogatják az emberi és a rendszer közötti kommunikációt, de szöveges és hangcsatornákat is. Lehet például, hogy a bot megválaszolja a bejövő telefonhívásokat, vagy részt vesz egy webes csevegésben. Az Azure kommunikációs szolgáltatások olyan ügyféloldali kódtárakat biztosít, amelyek lehetővé teszik ezen forgatókönyvek meghívását és csevegését. A minta architektúra folyamata az alábbiakban található:

:::image type="content" source="../media/scenarios/archdiagram-bot.png" alt-text="A kommunikációs szolgáltatások bot architektúráját bemutató ábra.":::

## <a name="networking"></a>Hálózat

Előfordulhat, hogy tetszőleges adatokat kell cserélnie a felhasználók között, például szinkronizálnia kell a közös, vegyes valóságot vagy játékélményt. A szöveg-, hang-és videó-kommunikációhoz használt valós idejű adatsíkok kétféleképpen érhetők el:

- **Ügyféloldali függvénytár meghívása** – az eszközök hívási csatornán keresztüli küldéséhez és fogadásához az API-k hozzáférhetnek. Ez a legegyszerűbb módszer arra, hogy adatkommunikációt vegyen fel egy meglévő interakcióba.
- A **kábító/turn** -Azure kommunikációs szolgáltatások szabványoknak megfelelő kábítást tesznek lehetővé, és a szolgáltatások elérhetővé válnak. Ez lehetővé teszi egy nagy mértékben testreszabott átviteli réteg létrehozását ezen szabványosított primitívek felett. Létrehozhat saját szabványoknak megfelelő ügyfelet, vagy használhat nyílt forráskódú kódtárakat, például a [WinRTC](https://github.com/microsoft/winrtc)-t.

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Felhasználói hozzáférési tokenek létrehozása](../quickstarts/access-tokens.md)

További információért tekintse át a következő cikkeket:

- A [hitelesítés](../concepts/authentication.md) ismertetése
- Tudnivalók a [telefonszámok típusairól](../concepts/telephony-sms/plan-solution.md)

- [Csevegés hozzáadása az alkalmazáshoz](../quickstarts/chat/get-started.md)
- [Hanghívás hozzáadása az alkalmazáshoz](../quickstarts/voice-video-calling/getting-started-with-calling.md)
