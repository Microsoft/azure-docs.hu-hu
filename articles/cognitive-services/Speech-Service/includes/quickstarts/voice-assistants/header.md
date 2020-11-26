---
title: 'Gyors útmutató: egyéni hangsegéd létrehozása – beszédfelismerési szolgáltatás'
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: trrwilson
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 03/20/2020
ms.author: travisw
ms.openlocfilehash: bb88ef7f048b2454f05ef33286e59277070c3ea0
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/26/2020
ms.locfileid: "96187570"
---
Ebben a rövid útmutatóban a [SPEECH SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) használatával hozzon létre egy egyéni hangsegéd-alkalmazást, amely egy már létrehozott és konfigurált robothoz csatlakozik. Ha robotot kell létrehoznia, tekintse meg [a kapcsolódó oktatóanyagot](~/articles/cognitive-services/speech-service/tutorial-voice-enable-your-bot-speech-sdk.md) az átfogóbb útmutatóhoz.

Néhány előfeltétel kielégítése után az egyéni hangsegéd csatlakoztatása csak néhány lépést vesz igénybe:
> [!div class="checklist"]
> * Hozzon létre egy `BotFrameworkConfig` objektumot az előfizetési kulcs és a régió alapján.
> * Hozzon létre egy `DialogServiceConnector` objektumot a `BotFrameworkConfig` fenti objektum használatával.
> * Az `DialogServiceConnector` objektum használatával megkezdheti a figyelési folyamatot egyetlen Kimondás esetén.
> * Vizsgálja meg a `ActivityReceivedEventArgs` visszaadott értéket.
