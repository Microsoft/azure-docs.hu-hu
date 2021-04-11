---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 04/13/2020
ms.author: trbye
ms.openlocfilehash: eaf8d8f741c120297e6ae57e8ddb8b62f7c7f534
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105104436"
---
A Speech Service egyik fő funkciója az emberi beszéd felismerése és más nyelvekre való lefordítása. Ebből a rövid útmutatóból megtudhatja, hogyan használhatja a Speech SDK-t az alkalmazásaiban és termékeiben, hogy kiváló minőségű hangfordítást végezzen. Ez a rövid útmutató a következő témaköröket tartalmazza:

* Beszéd – szöveg fordítása
* Beszéd lefordítása több célként megadott nyelvre
* Közvetlen beszéd – beszéd fordítás végrehajtása

## <a name="skip-to-samples-on-github"></a>Ugrás a mintákra a GitHubon

Ha közvetlenül a mintakód kihagyását szeretné kihagyni, tekintse meg a C++ gyors üzembe helyezési [mintákat](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/cpp/windows/translate-speech-to-text) a githubon.

## <a name="prerequisites"></a>Előfeltételek

Ez a cikk azt feltételezi, hogy rendelkezik Azure-fiókkal és Speech Service-előfizetéssel. Ha nem rendelkezik fiókkal és előfizetéssel, [próbálja ki ingyenesen a Speech szolgáltatást](../../../overview.md#try-the-speech-service-for-free).

## <a name="install-the-speech-sdk"></a>A Speech SDK telepítése

Mielőtt bármit elvégezhet, telepítenie kell a Speech SDK-t. A platformtól függően kövesse a _SPEECH SDK névjegye_ című cikk a <a href="/azure/cognitive-services/speech-service/speech-sdk#get-the-speech-sdk" target="_blank">Speech SDK beszerzése</a> című szakaszában található utasításokat.

## <a name="import-dependencies"></a>Függőségek importálása

A cikkben szereplő példák futtatásához foglalja bele a következő `#include` és `using` utasításokat a C++ kódlap elejére.

```cpp
#include <iostream> // cin, cout
#include <fstream>
#include <string>
#include <stdio.h>
#include <stdlib.h>
#include <speechapi_cxx.h>

using namespace std;
using namespace Microsoft::CognitiveServices::Speech;
using namespace Microsoft::CognitiveServices::Speech::Audio;
using namespace Microsoft::CognitiveServices::Speech::Translation;
```

## <a name="sensitive-data-and-environment-variables"></a>Bizalmas adatok és környezeti változók

A cikkben szereplő mintakód a bizalmas adatok tárolására szolgáló környezeti változóktól, például a beszédfelismerési erőforrás-előfizetési kulcstól és régiótól függ. A C++-kódrészlet két olyan karakterlánc-értéket tartalmaz, amelyek a gazdagép-gépek környezeti változói alapján vannak hozzárendelve, nevezetesen `SPEECH__SUBSCRIPTION__KEY` és `SPEECH__SERVICE__REGION` . Mindkét mező a Class (osztály) hatókörében szerepel, így azok elérhetővé válnak az osztály metódus szervein belül. További információ a környezeti változókról: [környezeti változók és alkalmazás konfigurációja](../../../../cognitive-services-security.md#environment-variables-and-application-configuration).

```cpp
auto SPEECH__SUBSCRIPTION__KEY = getenv("SPEECH__SUBSCRIPTION__KEY");
auto SPEECH__SERVICE__REGION = getenv("SPEECH__SERVICE__REGION");
```

## <a name="create-a-speech-translation-configuration"></a>Beszédfelismerési konfiguráció létrehozása

A beszédfelismerési szolgáltatás a Speech SDK használatával történő meghívásához létre kell hoznia egy [`SpeechTranslationConfig`][config] . Ez az osztály az előfizetésével kapcsolatos információkat tartalmaz, például a kulcsot és a társított régiót, végpontot, gazdagépet vagy engedélyezési jogkivonatot.

> [!TIP]
> Függetlenül attól, hogy elvégezte-e a beszédfelismerést, a beszédfelismerést, a fordítást vagy a szándék felismerését, mindig hozzon létre egy konfigurációt.

Az alábbiakat többféleképpen lehet inicializálni [`SpeechTranslationConfig`][config] :

* Előfizetéssel: adjon egy kulcsot és a hozzá tartozó régiót.
* Egy végponttal: pass a Speech Service-végponton. Kulcs-vagy engedélyezési jogkivonat nem kötelező.
* Gazdagép esetén: adjon meg egy gazdagép-címeket. Kulcs-vagy engedélyezési jogkivonat nem kötelező.
* Engedélyezési jogkivonattal: adjon meg egy engedélyezési jogkivonatot és a hozzá tartozó régiót.

Nézzük meg [`SpeechTranslationConfig`][config] , hogyan jön létre egy kulcs és régió használatával. Szerezze be ezeket a hitelesítő adatokat a [beszédfelismerési szolgáltatás ingyenes kipróbálásához](../../../overview.md#try-the-speech-service-for-free)szükséges lépések követésével.

```cpp
auto SPEECH__SUBSCRIPTION__KEY = getenv("SPEECH__SUBSCRIPTION__KEY");
auto SPEECH__SERVICE__REGION = getenv("SPEECH__SERVICE__REGION");

void translateSpeech() {
    auto config =
        SpeechTranslationConfig::FromSubscription(SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);
}

int main(int argc, char** argv) {
    setlocale(LC_ALL, "");
    translateSpeech();
    return 0;
}
```

## <a name="change-source-language"></a>Forrás nyelvének módosítása

A beszéd fordításának egyik gyakori feladata, hogy megadja a bemeneti (vagy a forrás) nyelvet. Vessünk egy pillantást arra, hogyan változtathatja meg a szövegbeviteli nyelvet az olasz nyelvre. A kódban lépjen kapcsolatba a [`SpeechTranslationConfig`][config] példánnyal, és hívja meg a `SetSpeechRecognitionLanguage` metódust.

```cpp
void translateSpeech() {
    auto translationConfig =
        SpeechTranslationConfig::FromSubscription(SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);

    // Source (input) language
    translationConfig->SetSpeechRecognitionLanguage("it-IT");
}
```

A [`SpeechRecognitionLanguage`][recognitionlang] tulajdonság nyelvi területi beállítású karakterláncot vár. A **területi beállítások** oszlopban bármilyen értéket megadhat a támogatott [területi beállítások/nyelvek](../../../language-support.md)listájában.

## <a name="add-translation-language"></a>Fordítási nyelv hozzáadása

A beszédfelismerés egy másik gyakori feladata, hogy megadják a cél fordítási nyelveket, de legalább egy szükséges, de a többszörösek támogatottak. A következő kódrészlet a franciát és a német nyelvet állítja be fordítási nyelvi célokként.

```cpp
void translateSpeech() {
    auto translationConfig =
        SpeechTranslationConfig::FromSubscription(SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);

    translationConfig->SetSpeechRecognitionLanguage("it-IT");

    // Translate to languages. See, https://aka.ms/speech/sttt-languages
    translationConfig->AddTargetLanguage("fr");
    translationConfig->AddTargetLanguage("de");
}
```

Minden hívás [`AddTargetLanguage`][addlang] esetén új fordítási nyelv van megadva. Más szóval, ha a beszédfelismerést a forrás nyelvéről ismerik fel, minden cél fordítás az eredményül kapott fordítási művelet részeként érhető el.

## <a name="initialize-a-translation-recognizer"></a>Fordító-felismerő inicializálása

Miután létrehozta a [`SpeechTranslationConfig`][config] -t, a következő lépés a inicializálása [`TranslationRecognizer`][recognizer] . A inicializálásakor [`TranslationRecognizer`][recognizer] át kell adnia a következőt: `translationConfig` . A konfigurációs objektum biztosítja azokat a hitelesítő adatokat, amelyeket a beszédfelismerési szolgáltatás a kérelem érvényesítéséhez igényel.

Ha az eszköz alapértelmezett mikrofonjának használatával ismeri fel a beszédet, a következőképpen [`TranslationRecognizer`][recognizer] kell kinéznie:

```cpp
void translateSpeech() {
    auto translationConfig =
        SpeechTranslationConfig::FromSubscription(SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);

    auto fromLanguage = "en-US";
    auto toLanguages = { "it", "fr", "de" };
    translationConfig->SetSpeechRecognitionLanguage(fromLanguage);
    for (auto language : toLanguages) {
        translationConfig->AddTargetLanguage(language);
    }

    auto recognizer = TranslationRecognizer::FromConfig(translationConfig);
}
```

Ha meg szeretné adni a hangbemeneti eszközt, létre kell hoznia egy-t, és meg kell [`AudioConfig`][audioconfig] adnia a `audioConfig` paramétert a inicializálásakor [`TranslationRecognizer`][recognizer] .

> [!TIP]
> [Ismerje meg, hogyan kérheti le az eszköz azonosítóját a hangbemeneti eszközhöz](../../../how-to-select-audio-input-devices.md).

Először az alábbi módon hivatkozhat az `AudioConfig` objektumra:

```cpp
void translateSpeech() {
    auto translationConfig =
        SpeechTranslationConfig::FromSubscription(SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);

    auto fromLanguage = "en-US";
    auto toLanguages = { "it", "fr", "de" };
    translationConfig->SetSpeechRecognitionLanguage(fromLanguage);
    for (auto language : toLanguages) {
        translationConfig->AddTargetLanguage(language);
    }

    auto audioConfig = AudioConfig::FromDefaultMicrophoneInput();
    auto recognizer = TranslationRecognizer::FromConfig(translationConfig, audioConfig);
}
```

Ha mikrofon használata helyett hangfájlt szeretne biztosítani, akkor továbbra is meg kell adnia a következőt: `audioConfig` . Ha azonban a létrehozása helyett a (z) metódust hozza létre [`AudioConfig`][audioconfig] `FromDefaultMicrophoneInput` , hívja meg `FromWavFileInput` és adja át a `filename` paramétert.

```cpp
void translateSpeech() {
    auto translationConfig =
        SpeechTranslationConfig::FromSubscription(SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);

    auto fromLanguage = "en-US";
    auto toLanguages = { "it", "fr", "de" };
    translationConfig->SetSpeechRecognitionLanguage(fromLanguage);
    for (auto language : toLanguages) {
        translationConfig->AddTargetLanguage(language);
    }

    auto audioConfig = AudioConfig::FromWavFileInput("YourAudioFile.wav");
    auto recognizer = TranslationRecognizer::FromConfig(translationConfig, audioConfig);
}
```

## <a name="translate-speech"></a>Beszéd fordítása

A beszédfelismerés lefordításához a Speech SDK egy mikrofonra vagy egy hangfájl-bemenetre támaszkodik. A beszédfelismerés a beszéd fordítása előtt következik be. Az összes objektum inicializálása után hívja meg a felismerési egyszeri függvényt, és szerezze be az eredményt.

```cpp
void translateSpeech() {
    auto translationConfig =
        SpeechTranslationConfig::FromSubscription(SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);

    string fromLanguage = "en-US";
    string toLanguages[3] = { "it", "fr", "de" };
    translationConfig->SetSpeechRecognitionLanguage(fromLanguage);
    for (auto language : toLanguages) {
        translationConfig->AddTargetLanguage(language);
    }

    auto recognizer = TranslationRecognizer::FromConfig(translationConfig);
    cout << "Say something in '" << fromLanguage << "' and we'll translate...\n";

    auto result = recognizer->RecognizeOnceAsync().get();
    if (result->Reason == ResultReason::TranslatedSpeech)
    {
        cout << "Recognized: \"" << result->Text << "\"" << std::endl;
        for (auto pair : result->Translations)
        {
            auto language = pair.first;
            auto translation = pair.second;
            cout << "Translated into '" << language << "': " << translation << std::endl;
        }
    }
}
```

A beszédfelismerés [alapjairól a beszédfelismerés alapjait](../../../get-started-speech-to-text.md)ismertető témakörben olvashat bővebben.

## <a name="synthesize-translations"></a>Fordítások szintézise

A sikeres beszédfelismerés és a fordítás után az eredmény egy szótár összes fordítását tartalmazza. A [`Translations`][translations] szótár kulcsa a fordítási nyelv, és az érték a lefordított szöveg. A felismert beszéd lefordítható, majd más nyelven (beszéd – beszéd) lehet szintetizálni.

### <a name="event-based-synthesis"></a>Eseményvezérelt szintézis

Az `TranslationRecognizer` objektum egy eseményt tesz elérhetővé `Synthesizing` . Az esemény többször is bekövetkezik, és egy mechanizmust biztosít a szintetizált hang lekéréséhez a fordítási felismerés eredményéről. Ha több nyelvre végez fordítást, olvassa el a [manuális szintézis](#manual-synthesis)című témakört. Adja meg a szintézis hangját, és adjon meg egy eseménykezelőt [`SetVoiceName`][voicename] az `Synthesizing` eseményhez, majd szerezze be a hangot. Az alábbi példa *. wav* -fájlként menti a lefordított hangot.

> [!IMPORTANT]
> Az Event-alapú szintézis csak egyetlen fordítással működik, **ne** adjon hozzá több fordítási nyelvet. Emellett a [`SetVoiceName`][voicename] nyelvnek meg kell egyeznie a cél fordítási nyelvével, például a következőhöz: `"de"` `"de-DE-Hedda"` .

```cpp
void translateSpeech() {
    auto translationConfig =
        SpeechTranslationConfig::FromSubscription(SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);

    auto fromLanguage = "en-US";
    auto toLanguage = "de";
    translationConfig->SetSpeechRecognitionLanguage(fromLanguage);
    translationConfig->AddTargetLanguage(toLanguage);

    // See: https://aka.ms/speech/sdkregion#standard-and-neural-voices
    translationConfig->SetVoiceName("de-DE-Hedda");

    auto recognizer = TranslationRecognizer::FromConfig(translationConfig);
    recognizer->Synthesizing.Connect([](const TranslationSynthesisEventArgs& e)
        {
            auto audio = e.Result->Audio;
            auto size = audio.size();
            cout << "Audio synthesized: " << size << " byte(s)" << (size == 0 ? "(COMPLETE)" : "") << std::endl;

            if (size > 0) {
                ofstream file("translation.wav", ios::out | ios::binary);
                auto audioData = audio.data();
                file.write((const char*)audioData, sizeof(audio[0]) * size);
                file.close();
            }
        });

    cout << "Say something in '" << fromLanguage << "' and we'll translate...\n";

    auto result = recognizer->RecognizeOnceAsync().get();
    if (result->Reason == ResultReason::TranslatedSpeech)
    {
        cout << "Recognized: \"" << result->Text << "\"" << std::endl;
        for (auto pair : result->Translations)
        {
            auto language = pair.first;
            auto translation = pair.second;
            cout << "Translated into '" << language << "': " << translation << std::endl;
        }
    }
}
```

### <a name="manual-synthesis"></a>Manuális szintézis

A [`Translations`][translations] szótár használható a hangfordítási szöveg hangszintéziséhez. Ismételje meg az egyes fordításokat, és szintetizálja a fordítást. Egy példány létrehozásakor `SpeechSynthesizer` az `SpeechConfig` objektumnak a [`SetSpeechSynthesisVoiceName`][speechsynthesisvoicename] kívánt hangra kell állítania a tulajdonságát. Az alábbi példa öt nyelvet fordít le, és az egyes fordítások a megfelelő neurális nyelven egy hangfájlba kerülnek.

```cpp
void translateSpeech() {
    auto translationConfig =
        SpeechTranslationConfig::FromSubscription(SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);

    auto fromLanguage = "en-US";
    auto toLanguages = { "de", "en", "it", "pt", "zh-Hans" };
    translationConfig->SetSpeechRecognitionLanguage(fromLanguage);
    for (auto language : toLanguages) {
        translationConfig->AddTargetLanguage(language);
    }

    auto recognizer = TranslationRecognizer::FromConfig(translationConfig);

    cout << "Say something in '" << fromLanguage << "' and we'll translate...\n";

    auto result = recognizer->RecognizeOnceAsync().get();
    if (result->Reason == ResultReason::TranslatedSpeech)
    {
        // See: https://aka.ms/speech/sdkregion#standard-and-neural-voices
        map<string, string> languageToVoiceMap;
        languageToVoiceMap["de"] = "de-DE-KatjaNeural";
        languageToVoiceMap["en"] = "en-US-AriaNeural";
        languageToVoiceMap["it"] = "it-IT-ElsaNeural";
        languageToVoiceMap["pt"] = "pt-BR-FranciscaNeural";
        languageToVoiceMap["zh-Hans"] = "zh-CN-XiaoxiaoNeural";

        cout << "Recognized: \"" << result->Text << "\"" << std::endl;
        for (auto pair : result->Translations)
        {
            auto language = pair.first;
            auto translation = pair.second;
            cout << "Translated into '" << language << "': " << translation << std::endl;

            auto speech_config =
                SpeechConfig::FromSubscription(SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);
            speech_config->SetSpeechSynthesisVoiceName(languageToVoiceMap[language]);

            auto audio_config = AudioConfig::FromWavFileOutput(language + "-translation.wav");
            auto synthesizer = SpeechSynthesizer::FromConfig(speech_config, audio_config);

            synthesizer->SpeakTextAsync(translation).get();
        }
    }
}
```

A Speech szintézissel kapcsolatos további információkért lásd: [a beszédfelismerés alapjai](../../../get-started-text-to-speech.md).

[config]: /cpp/cognitive-services/speech/translation-speechtranslationconfig
[audioconfig]: /cpp/cognitive-services/speech/audio-audioconfig
[recognizer]: /cpp/cognitive-services/speech/translation-translationrecognizer
[recognitionlang]: /cpp/cognitive-services/speech/speechconfig#setspeechrecognitionlanguage
[addlang]: /cpp/cognitive-services/speech/translation-speechtranslationconfig#addtargetlanguage
[translations]: /cpp/cognitive-services/speech/translation-translationrecognitionresult#translations
[voicename]: /cpp/cognitive-services/speech/translation-speechtranslationconfig#setvoicename
[speechsynthesisvoicename]: /cpp/cognitive-services/speech/speechconfig#setspeechsynthesisvoicename