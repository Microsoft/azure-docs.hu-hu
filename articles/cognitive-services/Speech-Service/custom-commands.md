---
title: Egyéni parancsok – beszédfelismerési szolgáltatás
titleSuffix: Azure Cognitive Services
description: Az egyéni parancsok funkcióinak, képességeinek és korlátozásának áttekintése, hangalapú alkalmazások létrehozására szolgáló megoldás.
services: cognitive-services
author: trrwilson
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: travisw
ms.openlocfilehash: e4ae8a09a554afe232091b68888af17dfa096771
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "102426469"
---
# <a name="what-is-custom-commands"></a>Mik azok az egyéni parancsok?

Az olyan alkalmazások, mint például a [hangsegédek](voice-assistants.md) , megfigyelik a felhasználókat, és válaszként olyan műveleteket végeznek, amelyek gyakran beszélnek vissza. A felhasználók beszédének [szövegét](speech-to-text.md) használják a felhasználó beszédének átírására, majd a szöveg természetes nyelvének megismerésére. Ez a művelet gyakran tartalmazza a [szöveg-beszéd](text-to-speech.md)kapcsolattal generált segédből származó beszédes kimenetet. Az eszközök a Speech SDK objektumával csatlakoznak a segédekhez `DialogServiceConnector` .

Az **egyéni parancsok** megkönnyítik a hangvezérelt hangvezérelt alkalmazások készítését a hang-első interakciós élményhez. Egységes szerzői élményt, automatikus üzemeltetési modellt és viszonylag alacsony bonyolultságot biztosít, amely segít a legjobb megoldás kialakításában a hangvezérelt forgatókönyvekhez.

Az egyéni parancsok a feladatok befejezéséhez és a parancs-és vezérlési forgatókönyvekhez legmegfelelőbbek, különösen a eszközök internetes hálózata (IoT) eszközök, a környezeti és a fej nélküli eszközök esetében is. Ilyenek például a vendéglátás, a kiskereskedelmi és az autóipari megoldások, amelyek lehetővé teszik a vendégek számára a legjobb, hangvezérelt felhasználói élmény kialakítását a vendégeknek, kezelheti a leltárt az áruházban, és vezérelheti az autón belüli funkciókat az áthelyezés során.

> [!TIP]
> Tekintse meg a kezdőlapon található minta bemutatókat [https://speech.microsoft.com/customcommands](https://speech.microsoft.com/customcommands) .

Ha szeretne összetett társalgási alkalmazásokat kiépíteni, javasoljuk, hogy próbálja ki a bot Framework-et a [virtuális asszisztens megoldás](/azure/bot-service/bot-builder-enterprise-template-overview)használatával. Közvetlenül is hozzáadhat hangokat bármelyik bot Framework-robothoz közvetlen vonalas beszéd használatával.

Az egyéni parancsok jó jelöltje egy rögzített, jól definiált változóval rendelkező szókincs. A Home Automation-feladatok, például a Termosztátok szabályozása ideális megoldás.

   ![Példák feladat-végrehajtási forgatókönyvekre](media/voice-assistants/task-completion-examples.png "feladatok befejezésére vonatkozó példák")

## <a name="getting-started-with-custom-commands"></a>Egyéni parancsok – első lépések

Célunk, hogy egyéni parancsokkal csökkentse a kognitív terhelést, hogy megismerje a különböző technológiákat, és koncentráljon a hangvezérelt alkalmazás létrehozására. Az <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesSpeechServices" target="_blank">Azure Speech-erőforrások létrehozására </a>szolgáló egyéni parancsok használatának első lépése. Megadhatja az egyéni parancsok alkalmazást a Speech Studióban, és közzéteheti azt, amely után az eszközön futó alkalmazások kommunikálhatnak a Speech SDK használatával.

#### <a name="authoring-flow-for-custom-commands"></a>Egyéni parancsok létrehozási folyamata
   ![Egyéni parancsok létrehozási folyamata](media/voice-assistants/custom-commands-flow.png "Az egyéni parancsok létrehozási folyamata")

Kövesse a gyors üzembe helyezést, hogy az első egyéni parancsok alkalmazása kevesebb, mint 10 perc alatt futtassa a kódot.

* [Hangvezérelt asszisztens létrehozása egyéni parancsokkal](quickstart-custom-commands-application.md)

Ha elkészült a gyors üzembe helyezéssel, tekintse át a útmutatókat, amelyek részletesen ismertetik az egyéni parancsok alkalmazásának tervezéséhez, fejlesztéséhez, hibakereséséhez, üzembe helyezéséhez és integrálásához szükséges lépéseket.

## <a name="building-voice-assistants-with-custom-commands"></a>Hangsegédek kiépítése egyéni parancsokkal
> [!VIDEO https://www.youtube.com/embed/1zr0umHGFyc]

## <a name="next-steps"></a>Következő lépések

* [Beszédfelismerési szolgáltatás előfizetési kulcsának beszerzése ingyenesen](overview.md#try-the-speech-service-for-free)
* [Megtekintheti a hangsegédek tárházát a GitHubon a mintákhoz](https://aka.ms/speech/cc-samples)
* [Az egyéni parancsok kipróbálásához nyissa meg a Speech studiót](https://speech.microsoft.com/customcommands)
* [A Speech SDK beszerzése](speech-sdk.md)