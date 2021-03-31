---
title: A szándék-felismerés áttekintése – beszédfelismerési szolgáltatás
titleSuffix: Azure Cognitive Services
description: A szándék-felismerés lehetővé teszi az előre definiált felhasználói célkitűzések felismerését. Ez a cikk áttekintést nyújt a szándék-felismerési szolgáltatás előnyeiről és képességeiről.
services: cognitive-services
author: v-demjoh
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 10/13/2020
ms.author: v-demjoh
keywords: szándék-felismerés
ms.openlocfilehash: 215b25e440b8cad76e0656e47d32b184edf4ac66
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "95018701"
---
# <a name="what-is-intent-recognition"></a>Mi a szándék-felismerés?

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

Ebben az áttekintésben megismerheti a szándék-felismerés előnyeit és képességeit. A Cognitive Services Speech SDK-ja integrálható a Language Understanding szolgáltatással (LUIS)szándékfelismerés céljából. A szándék az, amit a felhasználó tenni szeretne: például repülőutat foglalni, megnézni az időjárást vagy telefonhívást indítani.
A szándék-felismerés használatával az alkalmazások, eszközök és eszközök határozzák meg, hogy a felhasználó milyen beállításokat kíván kezdeményezni vagy végrehajtani a LUIS-ben meghatározott beállítások alapján.

## <a name="luis-key-required"></a>LUIS-kulcs szükséges

* A LUIS integrálható a Speech Service szolgáltatással a beszédből való szándékfelismerés céljából. Ehhez nincs szükség Speech Service-előfizetésre, csak a LUIS-ra.
* A beszédfelismerési szándék-felismerés integrálva van az SDK-val. A Speech Service-ben LUIS-kulcsot is használhat.
* A Speech SDK-n keresztüli szándék-felismerést a [Luis által támogatott régiók egy részhalmaza kínálja](./regions.md#intent-recognition).

## <a name="get-started"></a>Bevezetés

Tekintse [meg az első lépéseket a szándék](quickstarts/intent-recognition.md) -felismeréssel kapcsolatban.

## <a name="sample-code"></a>Mintakód

Mintakód a szándék-felismeréshez:

* [Rövid útmutató: Előre összeállított otthonautomatizálási alkalmazás használata](../luis/luis-get-started-create-app.md)
* [Beszédfelismerési szándék felismerése a C Speech SDK használatával #](./how-to-recognize-intents-from-speech-csharp.md)
* [Szándék-felismerés és egyéb Speech Services a C egységben #](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/unity/speechrecognizer)
* [Leképezések felismerése a Pythonhoz készült Speech SDK használatával](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/python/console)
* [Szándék-felismerés és egyéb Speech Services a C++ nyelvhez készült Speech SDK használatával Windows rendszeren](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/cpp/windows/console)
* [Szándék-felismerés és egyéb Speech Services a Javához készült Speech SDK-val Windows vagy Linux rendszeren](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/java/jre/console)
* [Szándék-felismerés és egyéb Speech Services a JavaScripthez készült Speech SDK-val egy böngészőben](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/js/browser)

## <a name="reference-docs"></a>Dokumentációs dokumentumok

* [Beszéd SDK](./speech-sdk.md)

## <a name="next-steps"></a>Következő lépések

* A szándék [-felismerési](quickstarts/intent-recognition.md) útmutató befejezése
* [Beszédfelismerési szolgáltatás előfizetési kulcsának beszerzése ingyenesen](overview.md#try-the-speech-service-for-free)
* [A Speech SDK beszerzése](speech-sdk.md)