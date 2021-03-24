---
title: Arc-események beszerzése a LIP-Sync szolgáltatáshoz
titleSuffix: Azure Cognitive Services
description: A Speech SDK támogatja a viseme-eseményt a beszédfelismerésben, amelyek a kulcsot jelölik a megfigyelt beszédben, például az ajkak, az állkapocs és a nyelv pozícióját, amikor egy adott fonéma állítanak elő.
services: cognitive-services
author: yulin-li
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/03/2021
ms.author: yulili
ms.custom: references_regions
zone_pivot_groups: programming-languages-speech-services-nomore-variant
ms.openlocfilehash: 15fa1dd230b7f07846653278533805fa66ed2195
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/23/2021
ms.locfileid: "104870389"
---
# <a name="get-facial-pose-events"></a>Arc póz-események beolvasása

> [!NOTE]
> A Viseme csak az `en-US-AriaNeural` USA nyugati régiójában () található hangra működik `westus` , és `en-US` az összes hangra az 2021 április végéig lesz elérhető.

A viseme a beszélt nyelv fonéma vizuális leírása.
Meghatározza az arc és a száj pozícióját, amikor beszél egy szót.
Mindegyik viseme a fonémák egy adott halmazához tartozó fő Arcfelismerés ábrázolja.
A visemes és a fonémák között nincs egy-az-egyhez levelezés.
Gyakran több fonémák felel meg egyetlen viseme, mivel több fonémák is ugyanúgy néz ki az arcon, mint például a és a `s` `z` .
Tekintse meg a [leképezési táblázatot a visemes és a fonémák között](#map-phonemes-to-visemes).

A visemes használatával több természetes és intelligens hírszolgáltatási asszisztenst, interaktív játékokat és rajzfilmfigurát hozhat létre, és intuitív nyelvi tanítási videókat is használhat. A hallás miatt nem párosított hangok vizuálisan és "LIP-Read" beszéd tartalmakat is választhatnak, amelyek egy animált arc visemes jelenítenek meg.

## <a name="get-viseme-events-with-the-speech-sdk"></a>Viseme-események beolvasása a Speech SDK-val

A viseme-események elvégzéséhez a bemeneti szöveget fonéma-sorozatokra és a hozzájuk tartozó viseme-sorozatokra konvertáljuk. Megbecsüljük az egyes viseme kezdési idejét a beszédfelismerési hangban. A Viseme események Viseme-azonosítók sorát tartalmazzák, amelyek mindegyike egy olyan hangeltolással rendelkezik, ahol a Viseme megjelenik. Ezek az események olyan száj-animációkhoz vezethetnek, amelyek a bemeneti szöveget beszélő személyt szimulálnak.

| Paraméter | Leírás |
|-----------|-------------|
| Viseme-azonosító | Egész szám, amely meghatározza a viseme. Az angol (Egyesült Államok) nyelvben 22 különböző visemes kínálunk, amelyek a fonémák egy adott halmazához tartozó száj alakzatokat ábrázolják. Tekintse meg a [leképezési táblázatot a Viseme-azonosító és a fonémák között](#map-phonemes-to-visemes).  |
| Hang eltolása | Az egyes viseme kezdési időpontja (100 nanoszekundumban). |

A viseme események beszerzéséhez fizessen elő a `VisemeReceived` SPEECH SDK eseményére.
Az alábbi kódrészletek bemutatják, hogyan fizethet elő a viseme eseményre.

::: zone pivot="programming-language-csharp"

```csharp
using (var synthesizer = new SpeechSynthesizer(speechConfig, audioConfig))
{
    // Subscribes to viseme received event
    synthesizer.VisemeReceived += (s, e) =>
    {
        Console.WriteLine($"Viseme event received. Audio offset: " +
            $"{e.AudioOffset / 10000}ms, viseme id: {e.VisemeId}.");
    };

    var result = await synthesizer.SpeakSsmlAsync(ssml));
}

```

::: zone-end

::: zone pivot="programming-language-cpp"

```cpp
auto synthesizer = SpeechSynthesizer::FromConfig(speechConfig, audioConfig);

// Subscribes to viseme received event
synthesizer->VisemeReceived += [](const SpeechSynthesisVisemeEventArgs& e)
{
    cout << "viseme event received. "
        // The unit of e.AudioOffset is tick (1 tick = 100 nanoseconds), divide by 10,000 to convert to milliseconds.
        << "Audio offset: " << e.AudioOffset / 10000 << "ms, "
        << "viseme id: " << e.VisemeId << "." << endl;
};

auto result = synthesizer->SpeakSsmlAsync(ssml).get();
```

::: zone-end

::: zone pivot="programming-language-java"

```java
SpeechSynthesizer synthesizer = new SpeechSynthesizer(speechConfig, audioConfig);

// Subscribes to viseme received event
synthesizer.VisemeReceived.addEventListener((o, e) -> {
    // The unit of e.AudioOffset is tick (1 tick = 100 nanoseconds), divide by 10,000 to convert to milliseconds.
    System.out.print("Viseme event received. Audio offset: " + e.getAudioOffset() / 10000 + "ms, ");
    System.out.println("viseme id: " + e.getVisemeId() + ".");
});

SpeechSynthesisResult result = synthesizer.SpeakSsmlAsync(ssml).get();
```

::: zone-end

::: zone pivot="programming-language-python"

```Python
speech_synthesizer = speechsdk.SpeechSynthesizer(speech_config=speech_config, audio_config=audio_config)

# Subscribes to viseme received event
speech_synthesizer.viseme_received.connect(lambda evt: print(
    "Viseme event received: audio offset: {}ms, viseme id: {}.".format(evt.audio_offset / 10000, evt.viseme_id)))

result = speech_synthesizer.speak_ssml_async(ssml).get()
```

::: zone-end

::: zone pivot="programming-language-javascript"

```Javascript
var synthesizer = new SpeechSDK.SpeechSynthesizer(speechConfig, audioConfig);

// Subscribes to viseme received event
synthesizer.visemeReceived = function (s, e) {
    window.console.log("(Viseme), Audio offset: " + e.audioOffset / 10000 + "ms. Viseme ID: " + e.visemeId);
}

synthesizer.speakSsmlAsync(ssml);
```

::: zone-end

::: zone pivot="programming-language-objectivec"

```Objective-C
SPXSpeechSynthesizer *synthesizer =
    [[SPXSpeechSynthesizer alloc] initWithSpeechConfiguration:speechConfig
                                           audioConfiguration:audioConfig];

// Subscribes to viseme received event
[synthesizer addVisemeReceivedEventHandler: ^ (SPXSpeechSynthesizer *synthesizer, SPXSpeechSynthesisVisemeEventArgs *eventArgs) {
    NSLog(@"Viseme event received. Audio offset: %fms, viseme id: %lu.", eventArgs.audioOffset/10000., eventArgs.visemeId);
}];

[synthesizer speakSsml:ssml];
```

::: zone-end

## <a name="map-phonemes-to-visemes"></a>Fonémák leképezése visemes

A Visemes nyelv szerint változhat. Minden nyelvhez tartozik egy visemes, amely megfelel az adott fonémák. Az alábbi táblázat a nemzetközi fonetikus ábécé (IPA) fonémák és a viseme-azonosítók (angol) közötti levelezést mutatja be (Egyesült Államok).

| IPA | Példa | Viseme-azonosító |
|-----|---------|-----------|
| i   | **EA** t   | 6 |
| ɪ   | **i** f | 6 |
| eɪ  | **a** te | 4 |
| ɛ | **e** nagyon | 4 |
|jelszava  |   **egy** ctive        |1|
|ɑ  |   **o** bstinate     |2|
|ɔ  |   c **au** se         |3|
|ʊ  |   b **OO** k          |4|
|oʊ |   **o** ld           |8|
|u  |   **U** BER          |7|
|ʌ  |   **u** ncle         |1|
|aɪ |   **i** CE           |11|
|aʊ |   t **szervezeti egység**           |9|
|ɔɪ |   **oi** l           |10|
|Ju |   **Yu** ma          |[6, 7]|
|elterjedésében  |   **egy** ugrás           |1|
|ɪɹ |   **fül** s          |[6, 13]|
|ɛɹ |   **légi** sík      |[4, 13]|
|ʊɹ |   c **az** e          |[4, 13]|
|aɪ (elterjedésében) ɹ |   **Ire**-föld   |[11, 13]|
|aʊ (elterjedésében) ɹ |   **óra** s     |[9, 13]|
|ɔɹ |   **vagy** Ange        |[3, 13]|
|ɑɹ |   **AR** TIST        |[2, 13]|
|ɝ  |   **fül** th         |[5, 13]|
|ɚ  |   összes **er** gy       |[1, 13]|
|w  |   **w** edik, s **UE** de   |7|
|j  |   **y** ARD, f **e** w     |6|
|P  |   **p** ut           |21|
|b  |   **b** ig           |21|
|t  |   **t** alk          |19|
|n  |   **d** ig           |19|
|k  |   **c** ut           |20|
|g  |   **g** o            |20|
|m  |   **m**-re, s **m** hamu    |21|
|p  |   **n** o, s **n** ow      |19|
|ŋ  |   Li **n** k          |20|
|f  |   **f** ork          |18|
|v  |   **v** alue         |18|
|θ  |   **th**          |17|
|ð  |   **th** en          |17|
|s  |   **s**           |15|
|z  |   **z** AP           |15|
|ʃ  |   e. **sh**           |16|
|ʒ  |   **J** acques       |16|
|ó  |   **h** ELP          |12|
|tʃ |   **CH** a          |16|
|dʒ |   **j** Oy           |16|
|l  |   **l** azonosító, g **l** ad     |14|
|ɹ  |   **r** ED, b **r**    |13|


## <a name="next-steps"></a>Következő lépések

* [A Speech SDK dokumentációja](speech-sdk.md)
