---
title: Hangvezérelt asszisztensek Windows rendszeren – első lépések
titleSuffix: Azure Cognitive Services
description: A Windows hangügynök fejlesztésének megkezdéséhez szükséges lépések, beleértve a mintakód rövid útmutatóját is.
services: cognitive-services
author: cfogg6
manager: trrwilson
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: travisw
ms.openlocfilehash: b50b98095cdfe0e6ec19c89b57887ebc4a0f6317
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2021
ms.locfileid: "101713048"
---
# <a name="getting-started-with-voice-assistants-on-windows"></a>Első lépések a hangsegédek Windowson való használatáról

Ez az útmutató végigvezeti a hangsegéd Windowson való fejlesztésének lépésein.

## <a name="set-up-your-development-environment"></a>A fejlesztési környezet beállítása

A Windowshoz készült hangsegédek fejlesztésének megkezdéséhez meg kell győződnie arról, hogy megfelelő fejlesztési környezettel rendelkezik.

- **Visual Studio:** Telepítenie kell a [Microsoft Visual Studio 2017](https://visualstudio.microsoft.com/), Community Edition vagy újabb verzióját
- **Windows-verzió**: egy számítógép Windows Insider gyors gyűrűs összeállításával és a Windows SDK Windows Insider-verziójával. Ez a mintakód a Windows Insider kiadás 19025.vb_release_analog .191112-1600 Windows SDK 19018-es verziójával való használatakor ellenőrizhető. A megadott verziók feletti buildek vagy SDK-nak kompatibilisnek kell lennie.
- **UWP fejlesztői eszközök**: a univerzális Windows-platform fejlesztési számítási feladat a Visual Studióban. A UWP [beszerzése](/windows/uwp/get-started/get-set-up) lapon megtekintheti, hogy a gép készen áll-e a UWP-alkalmazások fejlesztésére.
- **Egy működő mikrofon és hangkimenet**

## <a name="obtain-resources-from-microsoft"></a>Erőforrások beszerzése a Microsofttól

A Windows rendszerhez készült teljesen testre szabott hangügynökhöz szükséges egyes erőforrásokhoz a Microsoft erőforrásai szükségesek. A [UWP Hangsegéd mintája](windows-voice-assistants-faq.md#the-uwp-voice-assistant-sample) ezeknek az erőforrásoknak a mintáit tartalmazza a kezdeti fejlesztéshez és teszteléshez, ezért ez a szakasz nem szükséges a kezdeti fejlesztéshez.

- **Kulcsszavas modell:** A hangaktiváláshoz egy. bin fájl formájában a Microsoft kulcsszavas modellje szükséges. A UWP hangsegéd mintájában megadott. bin-fájl a *contoso* kulcsszóra van betanítva.
- **Korlátozott hozzáférésű szolgáltatás tokenje:** Mivel a ConversationalAgent API-k hozzáférést biztosítanak a mikrofon hanghoz, a korlátozott hozzáférésű szolgáltatásokra vonatkozó korlátozások védik a védelmet. Korlátozott hozzáférésű szolgáltatás használatához be kell szereznie egy korlátozott hozzáférés-szolgáltatási tokent, amely az alkalmazás csomag-identitásához csatlakozik a Microsofttól.

## <a name="establish-a-dialog-service"></a>Párbeszédpanel-szolgáltatás létrehozása

A hangsegédek teljes körű élménye érdekében az alkalmazásnak olyan párbeszédpanel-szolgáltatásra lesz szüksége, amely

- Kulcsszó észlelése egy adott hangfájlban
- Felhasználói bevitel figyelése és szöveggé konvertálása
- Adja meg a szöveget egy robotnak
- A robot szöveges válaszának lefordítása hangkimenetre

Az alapszintű párbeszédpanel-szolgáltatás közvetlen vonalas Beszédtel történő létrehozásához szükséges követelmények.

- **Speech Services-előfizetés:** Az előfizetést a kognitív beszédfelismerési szolgáltatásokhoz a beszédfelismerés és a szöveg közötti konverzióhoz. Próbálja [ki ingyenesen a](./overview.md#try-the-speech-service-for-free)Speech Services szolgáltatást.
- **Bot Framework bot:**  A bot Framework 4,2-es vagy újabb verziójának használatával létrehozott robot, amely a hangbemenet és a kimenet engedélyezéséhez előfizetett a [közvetlen vonalas beszédre](./direct-line-speech.md) . [Ez az útmutató](./tutorial-voice-enable-your-bot-speech-sdk.md) részletes útmutatást tartalmaz az "Echo bot" létrehozásához és a közvetlen vonalas beszédre való előfizetéshez. [Itt](https://blog.botframework.com/2018/05/07/build-a-microsoft-bot-framework-bot-with-the-bot-builder-sdk-v4/) is megtudhatja, hogyan hozhat létre testreszabott robotot, majd ugyanezen [lépéseket](./tutorial-voice-enable-your-bot-speech-sdk.md) követve előfizethet a közvetlen vonalas beszédre, de az új robot helyett az "Echo bot" kifejezésre.

## <a name="try-out-the-sample-app"></a>Próbálja ki a minta alkalmazást

A Speech Services előfizetési kulcsával és az ECHO robotjának robotjának azonosítójával készen áll arra, hogy kipróbálja a [UWP Voice Assistant-mintát](windows-voice-assistants-faq.md#the-uwp-voice-assistant-sample). Az alkalmazás futtatásához és a hitelesítő adatok megadásához kövesse a readme utasításait.

## <a name="create-your-own-voice-assistant-for-windows"></a>Saját hangsegéd létrehozása Windowshoz

Miután megkapta a korlátozott hozzáférésű szolgáltatás tokenjét és a Microsofttól származó bin-fájlt, elkezdheti a saját hangsegédjét a Windowson.

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [A hangsegéd megvalósítási útmutatójának beolvasása](windows-voice-assistants-implementation-guide.md)