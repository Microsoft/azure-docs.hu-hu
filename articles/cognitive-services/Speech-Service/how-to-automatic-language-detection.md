---
title: A beszédfelismerés automatikus nyelvfelismerés használata szövegre
titleSuffix: Azure Cognitive Services
description: A Speech SDK támogatja a beszédfelismerés automatikus nyelvi észlelését a szöveghez. A szolgáltatás használatakor a rendszer összehasonlítja a megadott hangot a nyelvek egy adott listájával, és a legvalószínűbb egyezést határozza meg. A visszaadott érték Ezután kiválaszthatja a szövegre való beszédfelismeréshez használt nyelvi modellt.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/19/2020
ms.author: trbye
zone_pivot_groups: programming-languages-speech-services-nomore-variant
ms.custom: devx-track-js, devx-track-csharp
ms.openlocfilehash: b558d4b3be64f82775eb9caf2f3ea8c5a8f95c6d
ms.sourcegitcommit: a8ff4f9f69332eef9c75093fd56a9aae2fe65122
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2021
ms.locfileid: "105025220"
---
# <a name="automatic-language-detection-for-speech-to-text"></a>Beszédfelismerés automatikus nyelvfelismerés a szöveghez

Az automatikus nyelvfelismerés segítségével megállapítható, hogy a beszédfelismerési SDK milyen nagy valószínűséggel egyezik meg a megadott nyelvek listájának összehasonlításával. Ekkor a rendszer az automatikus nyelvfelismerés által visszaadott értéket használja a beszédfelismeréshez használt nyelvi modell kiválasztásához, amely pontosabb átírást biztosít. Ha szeretné megtekinteni, hogy mely nyelvek érhetők el, tekintse meg a [nyelvi támogatást](language-support.md).

Ebből a cikkből megtudhatja, hogyan hozhat `AutoDetectSourceLanguageConfig` létre egy `SpeechRecognizer` objektumot, és hogyan kérheti le az észlelt nyelvet.

> [!IMPORTANT]
> Ez a funkció csak a Speech SDK-hoz, C#, C++, Java, Python, JavaScript és Objective-C nyelven érhető el.

## <a name="automatic-language-detection-with-the-speech-sdk"></a>Automatikus nyelvfelismerés a Speech SDK-val

Az automatikus nyelvfelismerés jelenleg legfeljebb négy nyelvet tartalmaz. Tartsa szem előtt ezt a korlátozást az `AutoDetectSourceLanguageConfig` objektum építésekor. Az alábbi mintákban létre fog hozni egy `AutoDetectSourceLanguageConfig` -t, majd felhasználhatja azt a létrehozásához `SpeechRecognizer` .

> [!TIP]
> Megadhat egy egyéni modellt is, amelyet a beszéd szövegre való elvégzéséhez használhat. További információ: [Egyéni modell használata automatikus nyelvfelismerés esetén](#use-a-custom-model-for-automatic-language-detection).

Az alábbi kódrészletek azt szemléltetik, hogyan használható az automatikus nyelvfelismerés az alkalmazásokban:

::: zone pivot="programming-language-csharp"

```csharp
var autoDetectSourceLanguageConfig =
    AutoDetectSourceLanguageConfig.FromLanguages(
        new string[] { "en-US", "de-DE" });

using (var recognizer = new SpeechRecognizer(
    speechConfig,
    autoDetectSourceLanguageConfig,
    audioConfig))
{
    var speechRecognitionResult = await recognizer.RecognizeOnceAsync();
    var autoDetectSourceLanguageResult =
        AutoDetectSourceLanguageResult.FromResult(speechRecognitionResult);
    var detectedLanguage = autoDetectSourceLanguageResult.Language;
}
```

::: zone-end

::: zone pivot="programming-language-cpp"

```cpp
auto autoDetectSourceLanguageConfig =
    AutoDetectSourceLanguageConfig::FromLanguages({ "en-US", "de-DE" });

auto recognizer = SpeechRecognizer::FromConfig(
    speechConfig,
    autoDetectSourceLanguageConfig,
    audioConfig
    );

speechRecognitionResult = recognizer->RecognizeOnceAsync().get();
auto autoDetectSourceLanguageResult =
    AutoDetectSourceLanguageResult::FromResult(speechRecognitionResult);
auto detectedLanguage = autoDetectSourceLanguageResult->Language;
```

::: zone-end

::: zone pivot="programming-language-java"

```java
AutoDetectSourceLanguageConfig autoDetectSourceLanguageConfig =
    AutoDetectSourceLanguageConfig.fromLanguages(Arrays.asList("en-US", "de-DE"));

SpeechRecognizer recognizer = new SpeechRecognizer(
    speechConfig,
    autoDetectSourceLanguageConfig,
    audioConfig);

Future<SpeechRecognitionResult> future = recognizer.recognizeOnceAsync();
SpeechRecognitionResult result = future.get(30, TimeUnit.SECONDS);
AutoDetectSourceLanguageResult autoDetectSourceLanguageResult =
    AutoDetectSourceLanguageResult.fromResult(result);
String detectedLanguage = autoDetectSourceLanguageResult.getLanguage();

recognizer.close();
speechConfig.close();
autoDetectSourceLanguageConfig.close();
audioConfig.close();
result.close();
```

::: zone-end

::: zone pivot="programming-language-python"

```Python
auto_detect_source_language_config = \
        speechsdk.languageconfig.AutoDetectSourceLanguageConfig(languages=["en-US", "de-DE"])
speech_recognizer = speechsdk.SpeechRecognizer(
        speech_config=speech_config, 
        auto_detect_source_language_config=auto_detect_source_language_config, 
        audio_config=audio_config)
result = speech_recognizer.recognize_once()
auto_detect_source_language_result = speechsdk.AutoDetectSourceLanguageResult(result)
detected_language = auto_detect_source_language_result.language
```

::: zone-end

::: zone pivot="programming-language-objectivec"

```Objective-C
NSArray *languages = @[@"zh-CN", @"de-DE"];
SPXAutoDetectSourceLanguageConfiguration* autoDetectSourceLanguageConfig = \
        [[SPXAutoDetectSourceLanguageConfiguration alloc]init:languages];
SPXSpeechRecognizer* speechRecognizer = \
        [[SPXSpeechRecognizer alloc] initWithSpeechConfiguration:speechConfig
                           autoDetectSourceLanguageConfiguration:autoDetectSourceLanguageConfig
                                              audioConfiguration:audioConfig];
SPXSpeechRecognitionResult *result = [speechRecognizer recognizeOnce];
SPXAutoDetectSourceLanguageResult *languageDetectionResult = [[SPXAutoDetectSourceLanguageResult alloc] init:result];
NSString *detectedLanguage = [languageDetectionResult language];
```

::: zone-end

::: zone pivot="programming-language-javascript"

```Javascript
var autoDetectConfig = SpeechSDK.AutoDetectSourceLanguageConfig.fromLanguages(["en-US", "de-DE"]);
var speechRecognizer = SpeechSDK.SpeechRecognizer.FromConfig(speechConfig, autoDetectConfig, audioConfig);
speechRecognizer.recognizeOnceAsync((result: SpeechSDK.SpeechRecognitionResult) => {
        var languageDetectionResult = SpeechSDK.AutoDetectSourceLanguageResult.fromResult(result);
        var detectedLanguage = languageDetectionResult.language;
},
{});
```

::: zone-end

## <a name="use-a-custom-model-for-automatic-language-detection"></a>Egyéni modell használata az automatikus nyelvfelismerés

A Speech Service-modelleket használó nyelvfelismerés mellett egyéni modellt is megadhat a továbbfejlesztett felismeréshez. Ha nincs megadva egyéni modell, a szolgáltatás az alapértelmezett nyelvi modellt fogja használni.

Az alábbi kódrészletek azt szemléltetik, hogyan lehet egyéni modellt megadni a beszédfelismerési szolgáltatás hívását. Ha az észlelt nyelv `en-US` , akkor a rendszer az alapértelmezett modellt használja. Ha az észlelt nyelv az, akkor a rendszer az `fr-FR` Egyéni modell végpontját használja:

::: zone pivot="programming-language-csharp"

```csharp
var sourceLanguageConfigs = new SourceLanguageConfig[]
{
    SourceLanguageConfig.FromLanguage("en-US"),
    SourceLanguageConfig.FromLanguage("fr-FR", "The Endpoint Id for custom model of fr-FR")
};
var autoDetectSourceLanguageConfig =
    AutoDetectSourceLanguageConfig.FromSourceLanguageConfigs(
        sourceLanguageConfigs);
```

::: zone-end

::: zone pivot="programming-language-cpp"

```cpp
std::vector<std::shared_ptr<SourceLanguageConfig>> sourceLanguageConfigs;
sourceLanguageConfigs.push_back(
    SourceLanguageConfig::FromLanguage("en-US"));
sourceLanguageConfigs.push_back(
    SourceLanguageConfig::FromLanguage("fr-FR", "The Endpoint Id for custom model of fr-FR"));

auto autoDetectSourceLanguageConfig =
    AutoDetectSourceLanguageConfig::FromSourceLanguageConfigs(
        sourceLanguageConfigs);
```

::: zone-end

::: zone pivot="programming-language-java"

```java
List sourceLanguageConfigs = new ArrayList<SourceLanguageConfig>();
sourceLanguageConfigs.add(
    SourceLanguageConfig.fromLanguage("en-US"));
sourceLanguageConfigs.add(
    SourceLanguageConfig.fromLanguage("fr-FR", "The Endpoint Id for custom model of fr-FR"));

AutoDetectSourceLanguageConfig autoDetectSourceLanguageConfig =
    AutoDetectSourceLanguageConfig.fromSourceLanguageConfigs(
        sourceLanguageConfigs);
```

::: zone-end

::: zone pivot="programming-language-python"

```Python
 en_language_config = speechsdk.languageconfig.SourceLanguageConfig("en-US")
 fr_language_config = speechsdk.languageconfig.SourceLanguageConfig("fr-FR", "The Endpoint Id for custom model of fr-FR")
 auto_detect_source_language_config = speechsdk.languageconfig.AutoDetectSourceLanguageConfig(
        sourceLanguageConfigs=[en_language_config, fr_language_config])
```

::: zone-end

::: zone pivot="programming-language-objectivec"

```Objective-C
SPXSourceLanguageConfiguration* enLanguageConfig = [[SPXSourceLanguageConfiguration alloc]init:@"en-US"];
SPXSourceLanguageConfiguration* frLanguageConfig = \
        [[SPXSourceLanguageConfiguration alloc]initWithLanguage:@"fr-FR"
                                                     endpointId:@"The Endpoint Id for custom model of fr-FR"];
NSArray *languageConfigs = @[enLanguageConfig, frLanguageConfig];
SPXAutoDetectSourceLanguageConfiguration* autoDetectSourceLanguageConfig = \
        [[SPXAutoDetectSourceLanguageConfiguration alloc]initWithSourceLanguageConfigurations:languageConfigs];
```

::: zone-end

::: zone pivot="programming-language-javascript"

```Javascript
var enLanguageConfig = SpeechSDK.SourceLanguageConfig.fromLanguage("en-US");
var frLanguageConfig = SpeechSDK.SourceLanguageConfig.fromLanguage("fr-FR", "The Endpoint Id for custom model of fr-FR");
var autoDetectConfig = SpeechSDK.AutoDetectSourceLanguageConfig.fromSourceLanguageConfigs([enLanguageConfig, frLanguageConfig]);
```

::: zone-end

## <a name="next-steps"></a>Következő lépések

::: zone pivot="programming-language-csharp"
* A GitHubon megtekintheti a [mintakód](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/csharp/sharedcontent/console/speech_recognition_samples.cs#L741) automatikus nyelvfelismerés
::: zone-end

::: zone pivot="programming-language-cpp"
* A GitHubon megtekintheti a [mintakód](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/cpp/windows/console/samples/speech_recognition_samples.cpp#L507) automatikus nyelvfelismerés
::: zone-end

::: zone pivot="programming-language-java"
* A GitHubon megtekintheti a [mintakód](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/java/jre/console/src/com/microsoft/cognitiveservices/speech/samples/console/SpeechRecognitionSamples.java#L521) automatikus nyelvfelismerés
::: zone-end

::: zone pivot="programming-language-python"
* A GitHubon megtekintheti a [mintakód](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/python/console/speech_sample.py#L458) automatikus nyelvfelismerés
::: zone-end

::: zone pivot="programming-language-objectivec"
* A GitHubon megtekintheti a [mintakód](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/objective-c/ios/speech-samples/speech-samples/ViewController.m#L525) automatikus nyelvfelismerés
::: zone-end

* [A Speech SDK dokumentációja](speech-sdk.md)
