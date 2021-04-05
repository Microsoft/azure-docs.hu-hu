---
title: Valós idejű beszélgetés átirata – beszédfelismerési szolgáltatás
titleSuffix: Azure Cognitive Services
description: Ismerje meg, hogyan használható a valós idejű beszélgetés a Speech SDK-val. A társalgási átirat lehetővé teszi, hogy az értekezleteket és más beszélgetéseket több résztvevő hozzáadására, eltávolítására és azonosítására képes legyen hangvételsel a beszédfelismerési szolgáltatásba.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 10/20/2020
ms.author: trbye
zone_pivot_groups: acs-js-csharp
ms.openlocfilehash: 48cd4c7996eabad7293aa2429c76b8943e0ab3da
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "100368472"
---
# <a name="get-started-with-real-time-conversation-transcription"></a>Ismerkedés a valós idejű Társalgások átírásával

A Speech SDK **ConversationTranscriber** API-jával több résztvevő hozzáadását, eltávolítását és azonosítását lehetővé tévő beszélgetéseket hozhat létre a hangvételi szolgáltatásban a vagy a használatával `PullStream` `PushStream` . Először hozzon létre hangaláírásokat minden résztvevő számára a REST API használatával, majd használja a hangaláírásokat az SDK-val a beszélgetések átírásához. További információért tekintse meg a beszélgetés átiratának [áttekintése](conversation-transcription.md) című témakört.

## <a name="limitations"></a>Korlátozások

* Csak a következő előfizetési régiókban érhető el:,, `centralus` `eastasia` `eastus` , `westeurope`
* 7 mikrofonos körkörös multi-mikrofonos tömböt igényel. A mikrofon tömbnek meg kell felelnie a [specifikációnak](./speech-devices-sdk-microphone.md).
* A [Speech Devices SDK](speech-devices-sdk.md) megfelelő eszközöket és egy minta alkalmazást biztosít, amely a beszélgetés átírását szemlélteti.

## <a name="prerequisites"></a>Előfeltételek

Ez a cikk azt feltételezi, hogy rendelkezik Azure-fiókkal és Speech Service-előfizetéssel. Ha nem rendelkezik fiókkal és előfizetéssel, [próbálja ki ingyenesen a Speech szolgáltatást](overview.md#try-the-speech-service-for-free).

::: zone pivot="programming-language-javascript"
[!INCLUDE [JavaScript Basics include](includes/how-to/conversation-transcription/real-time-javascript.md)]
::: zone-end

::: zone pivot="programming-language-csharp"
[!INCLUDE [C# Basics include](includes/how-to/conversation-transcription/real-time-csharp.md)]
::: zone-end

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Aszinkron beszélgetés átírása](how-to-async-conversation-transcription.md) 
>  [ROOBO-eszköz mintakód](https://github.com/Azure-Samples/Cognitive-Services-Speech-Devices-SDK/blob/master/Samples/Java/Android/Speech%20Devices%20SDK%20Starter%20App/example/app/src/main/java/com/microsoft/cognitiveservices/speech/samples/sdsdkstarterapp/ConversationTranscription.java) 
>  [Azure Kinect dev Kit mintakód](https://github.com/Azure-Samples/Cognitive-Services-Speech-Devices-SDK/blob/master/Samples/Java/Windows_Linux/SampleDemo/src/com/microsoft/cognitiveservices/speech/samples/Cts.java)