---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 01/04/2021
ms.author: trbye
ms.openlocfilehash: 407906727332f3db8d3d0a6840d0c865c6b33ff7
ms.sourcegitcommit: 73d80a95e28618f5dfd719647ff37a8ab157a668
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2021
ms.locfileid: "105609416"
---
Először töltse be a kulcsszavas modellt tartalmazó fájlt a `FromFile()` statikus függvénnyel, amely egy értéket ad vissza `KeywordRecognitionModel` . Használja a `.table` Speech studióból letöltött fájl elérési útját. Emellett `AudioConfig` az alapértelmezett mikrofon használatával is létrehoz egy új példányt a `KeywordRecognizer` hangkonfiguráció használatával.

```csharp
using Microsoft.CognitiveServices.Speech;
using Microsoft.CognitiveServices.Speech.Audio;

var keywordModel = KeywordRecognitionModel.FromFile("your/path/to/Activate_device.table");
using var audioConfig = AudioConfig.FromDefaultMicrophoneInput();
using var keywordRecognizer = new KeywordRecognizer(audioConfig);
```

Ezután a kulcsszó-felismerés futtatása egy hívással történik `RecognizeOnceAsync()` a Model objektum átadásával. Ez elindít egy kulcsszó-felismerési munkamenetet, amely addig tart, amíg el nem ismeri a kulcsszót. Ezért ezt a kialakítási mintát általában többszálas alkalmazásokban vagy használati esetekben érdemes használni, ahol előfordulhat, hogy a rendszer határozatlan ideig várakozik.

```csharp
KeywordRecognitionResult result = await keywordRecognizer.RecognizeOnceAsync(keywordModel);
```

> [!NOTE]
> Az itt látható példa a helyi kulcsszavas felismerést használja, mert nincs szükség `SpeechConfig` objektumra a hitelesítési környezethez, és nem lép kapcsolatba a háttérrel. A kulcsszavak felismerését és ellenőrzését azonban [közvetlenül háttérbeli kapcsolatok](../../../tutorial-voice-enable-your-bot-speech-sdk.md#view-the-source-code-that-enables-keyword)használatával is futtathatja.

### <a name="continuous-recognition"></a>Folyamatos felismerés

A Speech SDK többi osztálya támogatja a folyamatos felismerést (a beszéd-és a szándék-felismerés esetében is) a kulcsszó-felismeréssel. Ez lehetővé teszi, hogy ugyanazt a kódot használja, amelyet általában a folyamatos felismeréshez használhat, és a `.table` kulcsszavas modellre mutató fájlra hivatkozhat.

Beszéd – szöveg [esetén a folyamatos](../../../get-started-speech-to-text.md?pivots=programming-language-csharp&tabs=script%2cbrowser%2cwindowsinstall#continuous-recognition) felismerés beállításához kövesse a rövid útmutatóban látható tervezési mintát. Ezután cserélje le a-t `recognizer.StartContinuousRecognitionAsync()` a `recognizer.StartKeywordRecognitionAsync(KeywordRecognitionModel)` -re, és adja át az `KeywordRecognitionModel` objektumnak. A kulcsszó-felismeréssel való folyamatos felismerés leállításához használja `recognizer.StopKeywordRecognitionAsync()` a helyett a következőt: `recognizer.StopContinuousRecognitionAsync()` .

A szándék-felismerés azonos mintát használ a [`StartKeywordRecognitionAsync`](/dotnet/api/microsoft.cognitiveservices.speech.intent.intentrecognizer.startkeywordrecognitionasync#Microsoft_CognitiveServices_Speech_Intent_IntentRecognizer_StartKeywordRecognitionAsync_Microsoft_CognitiveServices_Speech_KeywordRecognitionModel_) és a [`StopKeywordRecognitionAsync`](/dotnet/api/microsoft.cognitiveservices.speech.intent.intentrecognizer.stopkeywordrecognitionasync#Microsoft_CognitiveServices_Speech_Intent_IntentRecognizer_StopKeywordRecognitionAsync) függvényekkel.
