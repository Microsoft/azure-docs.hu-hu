---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/09/2020
ms.author: trbye
ms.openlocfilehash: ad32204739d728006362ef55657a2f433be7aefc
ms.sourcegitcommit: 9514d24118135b6f753d8fc312f4b702a2957780
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/07/2021
ms.locfileid: "97978873"
---
A Speech SDK konfigurálása a tömörített hangbemenetek fogadására, létrehozás `PullAudioInputStream` vagy `PushAudioInputStream` . Ezután hozzon létre egy `AudioConfig` példányt a stream osztály egy példányáról, és adja meg az adatfolyam tömörítési formátumát.

Tegyük fel, hogy rendelkezik egy nevű bemeneti stream-osztállyal `pushStream` , és az Opus/OGG-t használja. A kód így néz ki:

```cpp
using namespace Microsoft::CognitiveServices::Speech;
using namespace Microsoft::CognitiveServices::Speech::Audio;

// ... omitted for brevity

 auto config =
    SpeechConfig::FromSubscription(
        "YourSubscriptionKey",
        "YourServiceRegion"
    );

auto audioFormat =
    AudioStreamFormat::GetCompressedFormat(
        AudioStreamContainerFormat::OGG_OPUS
    );
auto audioConfig =
    AudioConfig::FromStreamInput(
        pushStream,
        audioFormat
    );

auto recognizer = SpeechRecognizer::FromConfig(config, audioConfig);
auto result = recognizer->RecognizeOnceAsync().get();

auto text = result->Text;
```
