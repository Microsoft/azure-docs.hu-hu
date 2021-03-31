---
title: Szöveg – beszéd tároló végpontjának lekérdezése
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 08/31/2020
ms.author: aahi
ms.openlocfilehash: 9a29244745b154aa81997813fcf4e1457f599270
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "103621946"
---
A tároló [REST-alapú Endpoint API-kat](../rest-text-to-speech.md)biztosít. A platformhoz, a keretrendszerhez és a nyelvi változatokhoz számos [minta forráskód-projekt](https://github.com/Azure-Samples/Cognitive-Speech-TTS) áll rendelkezésre.

A standard vagy a neurális szöveg-beszéd típusú tárolókkal a letöltött Képcímke területi beállítását és hangját kell használnia. Ha például letöltötte a `latest` címkét, az alapértelmezett területi beállítás a `en-US` és a `AriaNeural` hang. Az `{VOICE_NAME}` argumentum ekkor a következő: [`en-US-AriaNeural`](../language-support.md#neural-voices) . Tekintse meg az alábbi példában SSML:

```xml
<speak version="1.0" xml:lang="en-US">
    <voice name="en-US-AriaNeural">
        This text will get converted into synthesized speech.
    </voice>
</speak>
```

Az *egyéni szöveg-beszéd* kapcsolathoz azonban az [Egyéni hangportálon](https://aka.ms/custom-voice-portal)kell megszereznie a **hangfelvételt/modellt** . Az egyéni modell neve szinonimája a hang nevét. Navigáljon a **képzés** lapra, és másolja a használni kívánt **hang/modell** `{VOICE_NAME}` argumentumként.
<br><br>
:::image type="content" source="../media/custom-voice/custom-voice-model-voice-name.png" alt-text="Egyéni hangmodell – hang neve":::

Tekintse meg az alábbi példában SSML:

```xml
<speak version="1.0" xml:lang="en-US">
    <voice name="custom-voice-model">
        This text will get converted into synthesized speech.
    </voice>
</speak>
```

Hozzon létre egy HTTP POST-kérelmet, amely néhány fejlécet és adattartalomot biztosít. Cserélje le a `{VOICE_NAME}` helyőrzőt a saját értékére.

```curl
curl -s -v -X POST http://localhost:5000/speech/synthesize/cognitiveservices/v1 \
 -H 'Accept: audio/*' \
 -H 'Content-Type: application/ssml+xml' \
 -H 'X-Microsoft-OutputFormat: riff-24khz-16bit-mono-pcm' \
 -d '<speak version="1.0" xml:lang="en-US"><voice name="{VOICE_NAME}">This is a test, only a test.</voice></speak>' > output.wav
```


A parancs a következőket hajtja végre:

* Egy HTTP POST-kérelmet hoz létre a `speech/synthesize/cognitiveservices/v1` végponthoz.
* Egy `Accept` fejlécet ad meg `audio/*`
* A `Content-Type` fejlécének megadása `application/ssml+xml` , további információ: [kérelem törzse](../rest-text-to-speech.md#request-body).
* A `X-Microsoft-OutputFormat` fejlécét adja meg `riff-16khz-16bit-mono-pcm` , további beállításokért lásd: [hangkimenet](../rest-text-to-speech.md#audio-outputs).
* A [Speech szintézis Markup Language (SSML)](../speech-synthesis-markup.md) kérelem küldése a `{VOICE_NAME}` végpontnak.
