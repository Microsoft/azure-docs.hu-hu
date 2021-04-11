---
title: A Speech SDK használata a kiejtési felméréshez
titleSuffix: Azure Cognitive Services
description: A Speech SDK támogatja a kiejtési felmérést, amely kiértékeli a beszéd bemenetének kiejtési minőségét, pontossággal, gördülékenyen, teljességgel stb.
services: cognitive-services
author: yulin-li
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 01/12/2021
ms.author: yulili
ms.custom: references_regions
zone_pivot_groups: programming-languages-speech-services-nomore-variant
ms.openlocfilehash: 2d1b5e490b7c8212e6103e3d169c1b5491d01dde
ms.sourcegitcommit: d23602c57d797fb89a470288fcf94c63546b1314
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/01/2021
ms.locfileid: "106167430"
---
# <a name="pronunciation-assessment"></a>Kiejtés értékelése

A kiejtés kiértékelése kiértékeli a Speech kiejtést, és a beszélők pontosságáról és a hangvételről szóló visszajelzésekről ad visszajelzést.
A kiejtés értékelésével a nyelvi tanulók felhasználhatják az azonnali visszajelzéseket, és javíthatják a kiejtését, hogy biztosan beszéljenek és a helyükön legyenek.
A pedagógusok képesek a több beszélő kiejtésének valós idejű kiértékelésére.

Ebből a cikkből megtudhatja, hogyan állíthatja be `PronunciationAssessmentConfig` és kérheti le a `PronunciationAssessmentResult` beszédfelismerési SDK használatát.

> [!NOTE]
> A kiejtés-értékelési funkció jelenleg támogatja a `en-US` nyelvet, amely az összes [beszéd és szöveg közötti régióban](regions.md#speech-to-text-text-to-speech-and-translation)elérhető. A és a `en-GB` `zh-CN` languages támogatása az előzetes verzióban érhető el, amely a `westus` és a `eastasia` `centralindia` régiókban is elérhető.

## <a name="pronunciation-assessment-with-the-speech-sdk"></a>Kiejtési értékelés a Speech SDK-val

Az alábbi mintákban létre kell hoznia egy `PronunciationAssessmentConfig` t, majd alkalmaznia kell rá a következőre: `SpeechRecognizer` .

Az alábbi kódrészletek azt szemléltetik, hogyan használható az automatikus nyelvfelismerés az alkalmazásokban:

::: zone pivot="programming-language-csharp"

```csharp
var pronunciationAssessmentConfig = new PronunciationAssessmentConfig(
    "reference text", GradingSystem.HundredMark, Granularity.Phoneme);

using (var recognizer = new SpeechRecognizer(
    speechConfig,
    audioConfig))
{
    // apply the pronunciation assessment configuration to the speech recognizer
    pronunciationAssessmentConfig.ApplyTo(recognizer);
    var speechRecognitionResult = await recognizer.RecognizeOnceAsync();
    var pronunciationAssessmentResult =
        PronunciationAssessmentResult.FromResult(speechRecognitionResult);
    var pronunciationScore = pronunciationAssessmentResult.PronunciationScore;
}
```

::: zone-end

::: zone pivot="programming-language-cpp"

```cpp
auto pronunciationAssessmentConfig =
    PronunciationAssessmentConfig::Create("reference text",
        PronunciationAssessmentGradingSystem::HundredMark,
        PronunciationAssessmentGranularity::Phoneme);

auto recognizer = SpeechRecognizer::FromConfig(
    speechConfig,
    audioConfig);

// apply the pronunciation assessment configuration to the speech recognizer
pronunciationAssessmentConfig->ApplyTo(recognizer);
speechRecognitionResult = recognizer->RecognizeOnceAsync().get();
auto pronunciationAssessmentResult =
    PronunciationAssessmentResult::FromResult(speechRecognitionResult);
auto pronunciationScore = pronunciationAssessmentResult->PronunciationScore;
```

::: zone-end

::: zone pivot="programming-language-java"

```java
PronunciationAssessmentConfig pronunciationAssessmentConfig =
    new PronunciationAssessmentConfig("reference text", 
        PronunciationAssessmentGradingSystem.HundredMark,
        PronunciationAssessmentGranularity.Phoneme);

SpeechRecognizer recognizer = new SpeechRecognizer(
    speechConfig,
    audioConfig);

// apply the pronunciation assessment configuration to the speech recognizer
pronunciationAssessmentConfig.applyTo(recognizer);
Future<SpeechRecognitionResult> future = recognizer.recognizeOnceAsync();
SpeechRecognitionResult result = future.get(30, TimeUnit.SECONDS);
PronunciationAssessmentResult pronunciationAssessmentResult =
    PronunciationAssessmentResult.fromResult(result);
Double pronunciationScore = pronunciationAssessmentResult.getPronunciationScore();

recognizer.close();
speechConfig.close();
audioConfig.close();
pronunciationAssessmentConfig.close();
result.close();
```

::: zone-end

::: zone pivot="programming-language-python"

```Python
pronunciation_assessment_config = \
        speechsdk.PronunciationAssessmentConfig(reference_text='reference text',
                grading_system=msspeech.PronunciationAssessmentGradingSystem.HundredMark,
                granularity=msspeech.PronunciationAssessmentGranularity.Phoneme)
speech_recognizer = speechsdk.SpeechRecognizer(
        speech_config=speech_config, \
        audio_config=audio_config)

# apply the pronunciation assessment configuration to the speech recognizer
pronunciation_assessment_config.apply_to(speech_recognizer)
result = speech_recognizer.recognize_once()
pronunciation_assessment_result = speechsdk.PronunciationAssessmentResult(result)
pronunciation_score = pronunciation_assessment_result.pronunciation_score
```

::: zone-end

::: zone pivot="programming-language-javascript"

```Javascript
var pronunciationAssessmentConfig = new SpeechSDK.PronunciationAssessmentConfig("reference text",
    PronunciationAssessmentGradingSystem.HundredMark,
    PronunciationAssessmentGranularity.Word, true);
var speechRecognizer = SpeechSDK.SpeechRecognizer.FromConfig(speechConfig, audioConfig);
// apply the pronunciation assessment configuration to the speech recognizer
pronunciationAssessmentConfig.applyTo(speechRecognizer);

speechRecognizer.recognizeOnceAsync((result: SpeechSDK.SpeechRecognitionResult) => {
        var pronunciationAssessmentResult = SpeechSDK.PronunciationAssessmentResult.fromResult(result);
        var pronunciationScore = pronunciationAssessmentResult.pronunciationScore;
        var wordLevelResult = pronunciationAssessmentResult.detailResult.Words;
},
{});
```

::: zone-end

::: zone pivot="programming-language-objectivec"

```Objective-C
SPXPronunciationAssessmentConfiguration* pronunciationAssessmentConfig =
    [[SPXPronunciationAssessmentConfiguration alloc]init:@"reference text"
                                           gradingSystem:SPXPronunciationAssessmentGradingSystem_HundredMark
                                             granularity:SPXPronunciationAssessmentGranularity_Phoneme];

SPXSpeechRecognizer* speechRecognizer = \
        [[SPXSpeechRecognizer alloc] initWithSpeechConfiguration:speechConfig
                                              audioConfiguration:audioConfig];

// apply the pronunciation assessment configuration to the speech recognizer
[pronunciationAssessmentConfig applyToRecognizer:speechRecognizer];

SPXSpeechRecognitionResult *result = [speechRecognizer recognizeOnce];
SPXPronunciationAssessmentResult* pronunciationAssessmentResult = [[SPXPronunciationAssessmentResult alloc] init:result];
double pronunciationScore = pronunciationAssessmentResult.pronunciationScore;
```

::: zone-end

### <a name="pronunciation-assessment-configuration-parameters"></a>Kiejtés-értékelési konfigurációs paraméterek

Ez a táblázat a kiejtési értékelés konfigurációs paramétereit sorolja fel.

| Paraméter | Leírás | Kötelező? |
|-----------|-------------|---------------------|
| ReferenceText | A kiejtés kiértékeléséhez használandó szöveg | Kötelező |
| GradingSystem | A pontszám kalibrációs pontjának rendszere. A `FivePoint` rendszer 0-5 lebegőpontos pontszámot ad, és `HundredMark` egy 0-100 lebegőpontos pontszámot ad. Alapértelmezett: `FivePoint`. | Választható |
| Részletesség | A kiértékelés részletessége. Az elfogadott értékek a teljes szöveges `Phoneme` , a Word-és a fonéma-szinten látható pontszám, amely a teljes szöveg `Word` és a szó szintjén mutatja a pontszámot, amely a `FullText` teljes szöveges szint pontszámát mutatja. Alapértelmezett: `Phoneme`. | Választható |
| EnableMiscue | Engedélyezi a gikszer számítását. Ha ez a beállítás engedélyezve van, a rendszer összehasonlítja a kiejtett szavakat a hivatkozási szöveggel, és az összehasonlítás alapján kihagyási/beszúrási művelettel jelöli meg őket. Az elfogadott értékek: `False` és `True` . Alapértelmezett: `False`. | Választható |
| ScenarioId | Egy testreszabott pont rendszerét jelző GUID. | Választható |

### <a name="pronunciation-assessment-result-parameters"></a>Kiejtés értékelésének eredményei – paraméterek

Ez a táblázat a kiejtési értékelés eredmény-paramétereit sorolja fel.

| Paraméter | Leírás |
|-----------|-------------|
| `AccuracyScore` | A beszéd kiejtésének pontossága. A pontosság azt jelzi, hogy a fonémák milyen szorosan illeszkedik a natív beszélő kiejtéséhez. A Word és a teljes szöveges szint pontossági pontszáma a fonéma szint pontossági pontszámával van összesítve. |
| `FluencyScore` | Az adott beszédet. A gördülékenység azt jelzi, hogy a beszéd milyen szorosan illeszkedik a szavak közötti csendes szünetekhez. |
| `CompletenessScore` | A beszéd teljessége, amelyet a Kiejtett szavak arányának kiszámításával határozhat meg a szöveges bevitelre való hivatkozáshoz. |
| `PronunciationScore` | Az adott beszéd kiejtési minőségét jelző összesített pontszám. Ez az Összesítés a `AccuracyScore` `FluencyScore` és `CompletenessScore` a súlyozással történik. |
| `ErrorType` | Ez az érték azt jelzi, hogy egy szó ki van-e hagyva, be van-e jelölve vagy rosszul van-e kiválasztva `ReferenceText` . A lehetséges értékek: `None` (azaz nincs hiba ezen a szónak), `Omission` `Insertion` és `Mispronunciation` . |

### <a name="sample-responses"></a>Mintául szolgáló válaszok

Egy tipikus kiejtési értékelési eredmény a JSON-ban:

```json
{
  "RecognitionStatus": "Success",
  "Offset": "400000",
  "Duration": "11000000",
  "NBest": [
      {
        "Confidence" : "0.87",
        "Lexical" : "good morning",
        "ITN" : "good morning",
        "MaskedITN" : "good morning",
        "Display" : "Good morning.",
        "PronunciationAssessment":
        {
            "PronScore" : 84.4,
            "AccuracyScore" : 100.0,
            "FluencyScore" : 74.0,
            "CompletenessScore" : 100.0,
        },
        "Words": [
            {
              "Word" : "Good",
              "Offset" : 500000,
              "Duration" : 2700000,
              "PronunciationAssessment":
              {
                "AccuracyScore" : 100.0,
                "ErrorType" : "None"
              }
            },
            {
              "Word" : "morning",
              "Offset" : 5300000,
              "Duration" : 900000,
              "PronunciationAssessment":
              {
                "AccuracyScore" : 100.0,
                "ErrorType" : "None"
              }
            }
        ]
      }
  ]
}
```

## <a name="next-steps"></a>Következő lépések

<!-- TODO: update JavaScript sample links after release -->

* Tekintse meg a kiejtés értékelését bemutató videó [bevezetését](https://www.youtube.com/watch?v=cBE8CUHOFHQ) és [videós oktatóanyagát](https://www.youtube.com/watch?v=zFlwm7N4Awc)

* Próbálja ki a [kiejtés Assessment bemutatóját](https://github.com/Azure-Samples/Cognitive-Speech-TTS/tree/master/PronunciationAssessment/BrowserJS)

::: zone pivot="programming-language-csharp"
* A kiejtés értékeléséhez tekintse meg a GitHubon található [mintakód](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/csharp/sharedcontent/console/speech_recognition_samples.cs#L949) című részt.
::: zone-end

::: zone pivot="programming-language-cpp"
* A kiejtés értékeléséhez tekintse meg a GitHubon található [mintakód](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/cpp/windows/console/samples/speech_recognition_samples.cpp#L633) című részt.
::: zone-end

::: zone pivot="programming-language-java"
* A kiejtés értékeléséhez tekintse meg a GitHubon található [mintakód](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/java/jre/console/src/com/microsoft/cognitiveservices/speech/samples/console/SpeechRecognitionSamples.java#L697) című részt.
::: zone-end

::: zone pivot="programming-language-python"
* A kiejtés értékeléséhez tekintse meg a GitHubon található [mintakód](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/python/console/speech_sample.py#L576) című részt.
::: zone-end

::: zone pivot="programming-language-objectivec"
* A kiejtés értékeléséhez tekintse meg a GitHubon található [mintakód](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/objective-c/ios/speech-samples/speech-samples/ViewController.m#L642) című részt.
::: zone-end

* [A Speech SDK dokumentációja](speech-sdk.md)

* [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/free/cognitive-services/)
