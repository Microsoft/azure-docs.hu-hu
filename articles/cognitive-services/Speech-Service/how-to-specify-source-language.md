---
title: Beszéd nyelvének megadása szöveghez
titleSuffix: Azure Cognitive Services
description: A Speech SDK lehetővé teszi, hogy a beszédfelismerés szövegre konvertálásakor megadja a forrás nyelvét. Ez a cikk azt ismerteti, hogyan használható a FromConfig és a SourceLanguageConfig metódus, hogy a beszédfelismerési szolgáltatás Ismerje a forrás nyelvét, és adjon meg egy egyéni modell célját.
services: cognitive-services
author: susanhu
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/19/2020
ms.author: qiohu
zone_pivot_groups: programming-languages-set-two
ms.openlocfilehash: 32c08af129172fb1dbebf1679ea01694e8bd3d1a
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/19/2020
ms.locfileid: "83653281"
---
# <a name="specify-source-language-for-speech-to-text"></a>Forrás nyelvének megadása beszédhez szövegként

Ebből a cikkből megtudhatja, hogyan határozhatja meg a beszédfelismeréshez készült Speech SDK által átadott hangbemenet forrásának nyelvét. Emellett például a kód megadásával egyéni beszédfelismerési modellt is megadhat a jobb felismeréshez.

::: zone pivot="programming-language-csharp"

## <a name="how-to-specify-source-language-in-c"></a>A forrás nyelvének meghatározása a C-ban #

Ebben a példában a forrás nyelve explicit módon paraméterként van megadva a `SpeechRecognizer` konstrukció használatával.

```csharp
var recognizer = new SpeechRecognizer(speechConfig, "de-DE", audioConfig);
```

Ebben a példában a forrás nyelvét kell megadnia a következővel: `SourceLanguageConfig` . Ezt követően a `sourceLanguageConfig` rendszer paraméterként adja át a értékét a `SpeechRecognizer` létrehozáshoz.

```csharp
var sourceLanguageConfig = SourceLanguageConfig.FromLanguage("de-DE");
var recognizer = new SpeechRecognizer(speechConfig, sourceLanguageConfig, audioConfig);
```

Ebben a példában a forrás nyelvét és az egyéni végpontot a használatával biztosítjuk `SourceLanguageConfig` . Ezt követően a `sourceLanguageConfig` rendszer paraméterként adja át a értékét a `SpeechRecognizer` létrehozáshoz.

```csharp
var sourceLanguageConfig = SourceLanguageConfig.FromLanguage("de-DE", "The Endpoint ID for your custom model.");
var recognizer = new SpeechRecognizer(speechConfig, sourceLanguageConfig, audioConfig);
```

>[!Note]
> `SpeechRecognitionLanguage``EndpointId`a és a set metódusok elavultak a `SpeechConfig` C# osztályból. A módszerek használata nem ajánlott, és nem használható az a összeállításakor `SpeechRecognizer` .

::: zone-end

::: zone pivot="programming-language-cpp"


## <a name="how-to-specify-source-language-in-c"></a>Forrás nyelvének meghatározása C++ nyelven

Ebben a példában a forrás nyelvét explicit módon kell megadni paraméterként a metódus használatával `FromConfig` .

```C++
auto recognizer = SpeechRecognizer::FromConfig(speechConfig, "de-DE", audioConfig);
```

Ebben a példában a forrás nyelvét kell megadnia a következővel: `SourceLanguageConfig` . Ezt követően a `sourceLanguageConfig` rendszer paraméterként adja át a értékét a `FromConfig` létrehozásakor `recognizer` .

```C++
auto sourceLanguageConfig = SourceLanguageConfig::FromLanguage("de-DE");
auto recognizer = SpeechRecognizer::FromConfig(speechConfig, sourceLanguageConfig, audioConfig);
```

Ebben a példában a forrás nyelvét és az egyéni végpontot a használatával biztosítjuk `SourceLanguageConfig` . A a (z `sourceLanguageConfig` ) paraméterként lesz átadva `FromConfig` a következő létrehozásakor: `recognizer` .

```C++
auto sourceLanguageConfig = SourceLanguageConfig::FromLanguage("de-DE", "The Endpoint ID for your custom model.");
auto recognizer = SpeechRecognizer::FromConfig(speechConfig, sourceLanguageConfig, audioConfig);
```

>[!Note]
> `SetSpeechRecognitionLanguage`és `SetEndpointId` elavult metódusok a `SpeechConfig` C++ és a Java osztályból. A módszerek használata nem ajánlott, és nem használható az a összeállításakor `SpeechRecognizer` .

::: zone-end

::: zone pivot="programming-language-java"

## <a name="how-to-specify-source-language-in-java"></a>Forrás nyelvének meghatározása Java-ban

Ebben a példában a forrás nyelvét explicit módon kell megadnia új létrehozásakor `SpeechRecognizer` .

```Java
SpeechRecognizer recognizer = new SpeechRecognizer(speechConfig, "de-DE", audioConfig);
```

Ebben a példában a forrás nyelvét kell megadnia a következővel: `SourceLanguageConfig` . Ezt követően a `sourceLanguageConfig` rendszer paraméterként adja át a függvényt új létrehozásakor `SpeechRecognizer` .

```Java
SourceLanguageConfig sourceLanguageConfig = SourceLanguageConfig.fromLanguage("de-DE");
SpeechRecognizer recognizer = new SpeechRecognizer(speechConfig, sourceLanguageConfig, audioConfig);
```

Ebben a példában a forrás nyelvét és az egyéni végpontot a használatával biztosítjuk `SourceLanguageConfig` . Ezt követően a `sourceLanguageConfig` rendszer paraméterként adja át a függvényt új létrehozásakor `SpeechRecognizer` .

```Java
SourceLanguageConfig sourceLanguageConfig = SourceLanguageConfig.fromLanguage("de-DE", "The Endpoint ID for your custom model.");
SpeechRecognizer recognizer = new SpeechRecognizer(speechConfig, sourceLanguageConfig, audioConfig);
```

>[!Note]
> `setSpeechRecognitionLanguage`és `setEndpointId` elavult metódusok a `SpeechConfig` C++ és a Java osztályból. A módszerek használata nem ajánlott, és nem használható az a összeállításakor `SpeechRecognizer` .

::: zone-end

::: zone pivot="programming-language-python"

## <a name="how-to-specify-source-language-in-python"></a>A forrás nyelvének meghatározása a Pythonban

Ebben a példában a forrás nyelve explicit módon paraméterként van megadva a `SpeechRecognizer` konstrukció használatával.

```Python
speech_recognizer = speechsdk.SpeechRecognizer(
        speech_config=speech_config, language="de-DE", audio_config=audio_config)
```

Ebben a példában a forrás nyelvét kell megadnia a következővel: `SourceLanguageConfig` . Ezt követően a `SourceLanguageConfig` rendszer paraméterként adja át a értékét a `SpeechRecognizer` létrehozáshoz.

```Python
source_language_config = speechsdk.languageconfig.SourceLanguageConfig("de-DE")
speech_recognizer = speechsdk.SpeechRecognizer(
        speech_config=speech_config, source_language_config=source_language_config, audio_config=audio_config)
```

Ebben a példában a forrás nyelvét és az egyéni végpontot a használatával biztosítjuk `SourceLanguageConfig` . Ezt követően a `SourceLanguageConfig` rendszer paraméterként adja át a értékét a `SpeechRecognizer` létrehozáshoz.

```Python
source_language_config = speechsdk.languageconfig.SourceLanguageConfig("de-DE", "The Endpoint ID for your custom model.")
speech_recognizer = speechsdk.SpeechRecognizer(
        speech_config=speech_config, source_language_config=source_language_config, audio_config=audio_config)
```

>[!Note]
> `speech_recognition_language``endpoint_id`a és a tulajdonságok elavultak a `SpeechConfig` Python osztályból. Ezeknek a tulajdonságoknak a használata nem ajánlott, és nem használható az a összeállításakor `SpeechRecognizer` .

::: zone-end

::: zone pivot="programming-language-more"

## <a name="how-to-specify-source-language-in-javascript"></a>Forrás nyelvének meghatározása a JavaScriptben

Első lépésként hozzon létre egy `SpeechConfig` :

```Javascript
var speechConfig = sdk.SpeechConfig.fromSubscription("YourSubscriptionkey", "YourRegion");
```

Ezután adja meg a hang forrásának nyelvét a következővel `speechRecognitionLanguage` :

```Javascript
speechConfig.speechRecognitionLanguage = "de-DE";
```

Ha egyéni modellt használ az elismeréshez, megadhatja a végpontot a következővel `endpointId` :

```Javascript
speechConfig.endpointId = "The Endpoint ID for your custom model.";
```

## <a name="how-to-specify-source-language-in-objective-c"></a>A forrás nyelvének meghatározása a Objective-C-ben

Ebben a példában a forrás nyelve explicit módon paraméterként van megadva a `SPXSpeechRecognizer` konstrukció használatával.

```Objective-C
SPXSpeechRecognizer* speechRecognizer = \
    [[SPXSpeechRecognizer alloc] initWithSpeechConfiguration:speechConfig language:@"de-DE" audioConfiguration:audioConfig];
```

Ebben a példában a forrás nyelvét kell megadnia a következővel: `SPXSourceLanguageConfiguration` . Ezt követően a `SPXSourceLanguageConfiguration` rendszer paraméterként adja át a értékét a `SPXSpeechRecognizer` létrehozáshoz.

```Objective-C
SPXSourceLanguageConfiguration* sourceLanguageConfig = [[SPXSourceLanguageConfiguration alloc]init:@"de-DE"];
SPXSpeechRecognizer* speechRecognizer = [[SPXSpeechRecognizer alloc] initWithSpeechConfiguration:speechConfig
                                                                     sourceLanguageConfiguration:sourceLanguageConfig
                                                                              audioConfiguration:audioConfig];
```

Ebben a példában a forrás nyelvét és az egyéni végpontot a használatával biztosítjuk `SPXSourceLanguageConfiguration` . Ezt követően a `SPXSourceLanguageConfiguration` rendszer paraméterként adja át a értékét a `SPXSpeechRecognizer` létrehozáshoz.

```Objective-C
SPXSourceLanguageConfiguration* sourceLanguageConfig = \
        [[SPXSourceLanguageConfiguration alloc]initWithLanguage:@"de-DE"
                                                     endpointId:@"The Endpoint ID for your custom model."];
SPXSpeechRecognizer* speechRecognizer = [[SPXSpeechRecognizer alloc] initWithSpeechConfiguration:speechConfig
                                                                     sourceLanguageConfiguration:sourceLanguageConfig
                                                                              audioConfiguration:audioConfig];
```

>[!Note]
> `speechRecognitionLanguage`és `endpointId` a tulajdonságok elavultak az `SPXSpeechConfiguration` Objective-C osztályban. Ezeknek a tulajdonságoknak a használata nem ajánlott, és nem használható az a összeállításakor `SPXSpeechRecognizer` .

::: zone-end

## <a name="see-also"></a>Lásd még

* A támogatott nyelvek és területi beállítások listájáért lásd: [nyelvi támogatás](language-support.md).

## <a name="next-steps"></a>További lépések

* [A Speech SDK dokumentációja](speech-sdk.md)