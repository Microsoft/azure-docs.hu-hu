---
title: Régiók – beszédfelismerési szolgáltatás
titleSuffix: Azure Cognitive Services
description: A beszédfelismerési szolgáltatáshoz elérhető régiók és végpontok listája, beleértve a beszéd szövegét, a szöveg és a beszéd fordítását, valamint a beszédfelismerést.
services: cognitive-services
author: mahilleb-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 08/20/2020
ms.author: panosper
ms.custom: seodec18,references_regions
ms.openlocfilehash: 646d29e72b91cd6afcde8e70ad8fd8715442b88e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98786791"
---
# <a name="speech-service-supported-regions"></a>Beszédfelismerési szolgáltatás által támogatott régiók

A beszédfelismerési szolgáltatás lehetővé teszi, hogy az alkalmazás hangot Konvertáljon szöveggé, beszéd fordítást végezzen, és szövegeket készítsen a beszédhez. A szolgáltatás több régióban is elérhető a Speech SDK és a REST API-k egyedi végpontokkal.

A beszédfelismerési portál egyéni konfigurációkat hajt végre a beszédfelismerési élményben az összes régióban: https://speech.microsoft.com

Vegye figyelembe a következő szempontokat, amikor a régiókat fontolgatja:

* Ha az alkalmazás egy [SPEECH SDK](speech-sdk.md)-t használ, akkor adja meg a régió azonosítóját, például a `westus` beszédfelismerési konfiguráció létrehozásakor.
* Ha az alkalmazás a beszédfelismerési szolgáltatás [REST API](./overview.md#reference-docs)-jának egyikét használja, akkor a régió a kérések végrehajtásakor használt végponti URI része.
* A régióhoz létrehozott kulcsok csak az adott régióban érvényesek. A más régiókkal való használatának megkísérlése hitelesítési hibákat eredményez.

## <a name="speech-sdk"></a>Beszéd SDK

A [SPEECH SDK](speech-sdk.md)-ban a régiók karakterláncként vannak megadva (például paraméterként `SpeechConfig.FromSubscription` a C# Speech SDK-ban).

### <a name="speech-to-text-text-to-speech-and-translation"></a>Beszéd – szöveg, szöveg – beszéd és fordítás

A beszédfelismerési testreszabási portál itt érhető el: https://speech.microsoft.com

A beszédfelismerési szolgáltatás a következő régiókban érhető el a beszédfelismerés, a **szöveg és a beszéd** **megjelenítéséhez**, valamint a **fordításhoz**:

[!INCLUDE [](../../../includes/cognitive-services-speech-service-region-identifier.md)]

Ha a [SPEECH SDK](speech-sdk.md)-t használja, a régiókat a **régió azonosítója** adja meg (például paraméterként `SpeechConfig.FromSubscription` ). Győződjön meg arról, hogy a régió megfelel az előfizetés régiójának.

Ha egy egyéni modellt szeretne hangadatokkal betanítani, a gyorsabb képzés érdekében használja az egyik [régiót dedikált hardverrel](custom-speech-overview.md#set-up-your-azure-account) . A [REST API](https://centralus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0/operations/CopyModelToSubscription) segítségével később is másolhatja a teljes mértékben betanított modellt egy másik régióba.

### <a name="intent-recognition"></a>Szándékfelismerés

Az elérhető régiók a beszédfelismerési SDK-n keresztül történő **szándék-felismeréshez** a következők:

| Globális régió | Region           | Régió azonosítója |
| ------------- | ---------------- | -------------------- |
| Ázsia          | Kelet-Ázsia        | `eastasia`           |
| Ázsia          | Délkelet-Ázsia   | `southeastasia`      |
| Ausztrália     | Kelet-Ausztrália   | `australiaeast`      |
| Európa        | Észak-Európa     | `northeurope`        |
| Európa        | Nyugat-Európa      | `westeurope`         |
| Észak-Amerika | USA keleti régiója          | `eastus`             |
| Észak-Amerika | USA 2. keleti régiója        | `eastus2`            |
| Észak-Amerika | USA déli középső régiója | `southcentralus`     |
| Észak-Amerika | USA nyugati középső régiója  | `westcentralus`      |
| Észak-Amerika | USA nyugati régiója          | `westus`             |
| Észak-Amerika | USA 2. nyugati régiója        | `westus2`            |
| Dél-Amerika | Dél-Brazília     | `brazilsouth`        |

Ez a [Language Understanding szolgáltatás (Luis)](../luis/luis-reference-regions.md)által támogatott közzétételi régiók részhalmaza.

### <a name="voice-assistants"></a>Hangvezérelt asszisztensek

A [SPEECH SDK](speech-sdk.md) a **hangsegéd** képességeit a következő régiókban lévő [közvetlen beszédfelismeréssel](./direct-line-speech.md) támogatja:

| Globális régió | Region           | Régió azonosítója    |
| ------------- | ---------------- | -------------------- |
| Észak-Amerika | USA nyugati régiója          | `westus`             |
| Észak-Amerika | USA 2. nyugati régiója        | `westus2`            |
| Észak-Amerika | USA keleti régiója          | `eastus`             |
| Észak-Amerika | USA 2. keleti régiója        | `eastus2`            |
| Észak-Amerika | USA nyugati középső régiója  | `westcentralus`      |
| Észak-Amerika | USA déli középső régiója | `southcentralus`     |
| Európa        | Nyugat-Európa      | `westeurope`         |
| Európa        | Észak-Európa     | `northeurope`        |
| Ázsia          | Kelet-Ázsia        | `eastasia`           |
| Ázsia          | Délkelet-Ázsia   | `southeastasia`      |
| India         | Közép-India    | `centralindia`       |

### <a name="speaker-recognition"></a>Speaker Recognition

Speaker Recognition jelenleg csak a régióban érhető el `westus` .

## <a name="rest-apis"></a>REST API-k

A beszédfelismerési szolgáltatás a REST-végpontokat is elérhetővé teszi a beszéd – szöveg és a szöveg – beszéd kérésekhez.

### <a name="speech-to-text"></a>Diktálás

A beszéd – szöveg hivatkozási dokumentációja a [beszéd – szöveg REST API](rest-speech-to-text.md)című részben olvasható.

A REST API végpontjának formátuma a következő:

```
https://<REGION_IDENTIFIER>.stt.speech.microsoft.com/speech/recognition/conversation/cognitiveservices/v1
```

Cserélje le az `<REGION_IDENTIFIER>` elemet az előfizetés régiójának megfelelő azonosítóra ebből a táblából:

[!INCLUDE [](../../../includes/cognitive-services-speech-service-region-identifier.md)]

> [!NOTE]
> A nyelvi paramétert az URL-címhez kell hozzáfűzni, hogy ne kapjon 4xx HTTP-hibát. Az USA nyugati végpontját használó angol nyelv például a következő: `https://westus.stt.speech.microsoft.com/speech/recognition/conversation/cognitiveservices/v1?language=en-US` .

### <a name="text-to-speech"></a>Szövegfelolvasás

Szöveg – beszéd hivatkozási dokumentáció: [szöveg – beszéd REST API](rest-text-to-speech.md).

[!INCLUDE [](../../../includes/cognitive-services-speech-service-endpoints-text-to-speech.md)]