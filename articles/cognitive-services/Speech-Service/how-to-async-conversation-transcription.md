---
title: Aszinkron beszélgetés átírása – beszédfelismerési szolgáltatás
titleSuffix: Azure Cognitive Services
description: Ismerje meg, hogyan használhatja az aszinkron beszélgetéseket a Speech Service használatával. Csak a Java és a C# esetében érhető el.
services: cognitive-services
author: markamos
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: amishu
ms.custom: devx-track-csharp
zone_pivot_groups: programming-languages-set-twenty-one
ms.openlocfilehash: 4b2dfa8d474f10d6b4ca1c46ac2b575e8d8407ff
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "88934641"
---
# <a name="asynchronous-conversation-transcription"></a>Aszinkron beszédátírás

Ebben a cikkben az aszinkron beszélgetések átírását mutatjuk be a **RemoteConversationTranscriptionClient** API használatával. Ha úgy konfigurálta a beszélgetési átírást, hogy aszinkron átírást végezzen `conversationId` , a `conversationId` **RemoteConversationTranscriptionClient** API-val társított átírást is beszerezheti.

## <a name="asynchronous-vs-real-time--asynchronous"></a>Aszinkron és valós idejű + aszinkron

Az aszinkron átírással továbbíthatja a beszélgetés hangját, de a valós időben nem kell visszaadnia az átírást. Ehelyett a hang elküldése után használja a parancsot az `conversationId` `Conversation` aszinkron átírás állapotának lekérdezéséhez. Ha az aszinkron átírás elkészült, megkapja a `RemoteConversationTranscriptionResult` .

A valós idejű és aszinkron megoldásokkal valós időben érheti el az átírást, az átírást pedig a `conversationId` (az aszinkron forgatókönyvhöz hasonlóan) lekérdezéssel is lekérdezheti.

Az aszinkron átírás végrehajtásához két lépés szükséges. Első lépésként töltse fel a hangot, válassza a csak aszinkron vagy a valós idejű és az aszinkron lehetőséget. A második lépés az átirat eredményeinek beolvasása.

::: zone pivot="programming-language-csharp"
[!INCLUDE [prerequisites](includes/how-to/remote-conversation/csharp/examples.md)]
::: zone-end

::: zone pivot="programming-language-java"
[!INCLUDE [prerequisites](includes/how-to/remote-conversation/java/examples.md)]
::: zone-end


## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [A GitHubon található minták megismerése](https://aka.ms/csspeech/samples)
