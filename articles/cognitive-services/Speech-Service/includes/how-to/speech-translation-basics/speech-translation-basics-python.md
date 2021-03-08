---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 04/13/2020
ms.author: trbye
ms.openlocfilehash: 679104a1941e677dd78190fb80964a5d39813e61
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/07/2021
ms.locfileid: "102444638"
---
A Speech Service egyik fő funkciója az emberi beszéd felismerése és más nyelvekre való lefordítása. Ebből a rövid útmutatóból megtudhatja, hogyan használhatja a Speech SDK-t az alkalmazásaiban és termékeiben, hogy kiváló minőségű hangfordítást végezzen. Ez a rövid útmutató a következő témaköröket tartalmazza:

* Beszéd – szöveg fordítása
* Beszéd lefordítása több célként megadott nyelvre
* Közvetlen beszéd – beszéd fordítás végrehajtása

## <a name="skip-to-samples-on-github"></a>Ugrás a mintákra a GitHubon

Ha közvetlenül a mintakód kihagyása mellett szeretne kiugrani, tekintse meg a [Python gyors példákat](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/python) a githubon.

## <a name="prerequisites"></a>Előfeltételek

Ez a cikk azt feltételezi, hogy rendelkezik Azure-fiókkal és Speech Service-előfizetéssel. Ha nem rendelkezik fiókkal és előfizetéssel, [próbálja ki ingyenesen a Speech szolgáltatást](../../../overview.md#try-the-speech-service-for-free).

## <a name="install-the-speech-sdk"></a>A Speech SDK telepítése

Mielőtt bármit elvégezhet, telepítenie kell a Speech SDK-t. A platformtól függően kövesse a _SPEECH SDK névjegye_ című cikk a <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk#get-the-speech-sdk" target="_blank">Speech SDK beszerzése</a> című szakaszában található utasításokat.

## <a name="import-dependencies"></a>Függőségek importálása

A cikkben szereplő példák futtatásához foglalja bele a következő `import` utasításokat a Python-kódlap elejére.

```python
import os
import azure.cognitiveservices.speech as speechsdk
```

## <a name="sensitive-data-and-environment-variables"></a>Bizalmas adatok és környezeti változók

A cikkben szereplő mintakód a bizalmas adatok tárolására szolgáló környezeti változóktól, például a beszédfelismerési erőforrás-előfizetési kulcstól és régiótól függ. A Python-kódrészlet két olyan értéket tartalmaz, amelyek a gazdagép-gépek környezeti változói alapján vannak hozzárendelve, nevezetesen `SPEECH__SUBSCRIPTION__KEY` és `SPEECH__SERVICE__REGION` . Mindkét változó globális hatókörű, így azok elérhetővé válnak a kódlap Function definition-definíciójában. További információ a környezeti változókról: [környezeti változók és alkalmazás konfigurációja](../../../../cognitive-services-security.md#environment-variables-and-application-configuration).

```python
speech_key, service_region = os.environ['SPEECH__SUBSCRIPTION__KEY'], os.environ['SPEECH__SERVICE__REGION']
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

```python
from_language, to_language = 'en-US', 'de'

def translate_speech_to_text():
    translation_config = speechsdk.translation.SpeechTranslationConfig(
            subscription=speech_key, region=service_region)
```

## <a name="change-source-language"></a>Forrás nyelvének módosítása

A beszéd fordításának egyik gyakori feladata, hogy megadja a bemeneti (vagy a forrás) nyelvet. Vessünk egy pillantást arra, hogyan változtathatja meg a szövegbeviteli nyelvet az olasz nyelvre. A kódban lépjen kapcsolatba a [`SpeechTranslationConfig`][config] példánnyal, és rendelje hozzá a `speech_recognition_language` tulajdonsághoz.

```python
def translate_speech_to_text():
    translation_config = speechsdk.translation.SpeechTranslationConfig(
            subscription=speech_key, region=service_region)

    # Source (input) language
    translation_config.speech_recognition_language = from_language
```

A [`speech_recognition_language`][recognitionlang] tulajdonság nyelvi területi beállítású karakterláncot vár. A **területi beállítások** oszlopban bármilyen értéket megadhat a támogatott [területi beállítások/nyelvek](../../../language-support.md)listájában.

## <a name="add-translation-language"></a>Fordítási nyelv hozzáadása

A beszédfelismerés egy másik gyakori feladata, hogy megadják a cél fordítási nyelveket, de legalább egy szükséges, de a többszörösek támogatottak. A következő kódrészlet a franciát és a német nyelvet állítja be fordítási nyelvi célokként.

```python
def translate_speech_to_text():
    translation_config = speechsdk.translation.SpeechTranslationConfig(
            subscription=speech_key, region=service_region)

    translation_config.speech_recognition_language = "it-IT"

    # Translate to languages. See, https://aka.ms/speech/sttt-languages
    translation_config.add_target_language("fr")
    translation_config.add_target_language("de")
```

Minden hívás [`add_target_language`][addlang] esetén új fordítási nyelv van megadva. Más szóval, ha a beszédfelismerést a forrás nyelvéről ismerik fel, minden cél fordítás az eredményül kapott fordítási művelet részeként érhető el.

## <a name="initialize-a-translation-recognizer"></a>Fordító-felismerő inicializálása

Miután létrehozta a [`SpeechTranslationConfig`][config] -t, a következő lépés a inicializálása [`TranslationRecognizer`][recognizer] . A inicializálásakor [`TranslationRecognizer`][recognizer] át kell adnia a következőt: `translation_config` . A konfigurációs objektum biztosítja azokat a hitelesítő adatokat, amelyeket a beszédfelismerési szolgáltatás a kérelem érvényesítéséhez igényel.

Ha az eszköz alapértelmezett mikrofonjának használatával ismeri fel a beszédet, a következőképpen [`TranslationRecognizer`][recognizer] kell kinéznie:

```python
def translate_speech_to_text():
    translation_config = speechsdk.translation.SpeechTranslationConfig(
            subscription=speech_key, region=service_region)

    translation_config.speech_recognition_language = from_language
    translation_config.add_target_language(to_language)

    recognizer = speechsdk.translation.TranslationRecognizer(
            translation_config=translation_config)
```

Ha meg szeretné adni a hangbemeneti eszközt, létre kell hoznia egy-t, és meg kell [`AudioConfig`][audioconfig] adnia a `audio_config` paramétert a inicializálásakor [`TranslationRecognizer`][recognizer] .

> [!TIP]
> [Ismerje meg, hogyan kérheti le az eszköz azonosítóját a hangbemeneti eszközhöz](../../../how-to-select-audio-input-devices.md).

Először az alábbi módon hivatkozhat az `AudioConfig` objektumra:

```python
def translate_speech_to_text():
    translation_config = speechsdk.translation.SpeechTranslationConfig(
            subscription=speech_key, region=service_region)

    translation_config.speech_recognition_language = from_language
    for lang in to_languages:
        translation_config.add_target_language(lang)

    audio_config = speechsdk.audio.AudioConfig(use_default_microphone=True)
    recognizer = speechsdk.translation.TranslationRecognizer(
            translation_config=translation_config, audio_config=audio_config)
```

Ha mikrofon használata helyett hangfájlt szeretne biztosítani, akkor továbbra is meg kell adnia a következőt: `audioConfig` . Ha azonban a [`AudioConfig`][audioconfig] -vel való meghívása helyett a-t hozza létre, `use_default_microphone=True` meghívja a-t, `filename="path-to-file.wav"` és adja meg a `filename` paramétert.

```python
def translate_speech_to_text():
    translation_config = speechsdk.translation.SpeechTranslationConfig(
            subscription=speech_key, region=service_region)

    translation_config.speech_recognition_language = from_language
    for lang in to_languages:
        translation_config.add_target_language(lang)

    audio_config = speechsdk.audio.AudioConfig(filename="path-to-file.wav")
    recognizer = speechsdk.translation.TranslationRecognizer(
            translation_config=translation_config, audio_config=audio_config)
```

## <a name="translate-speech"></a>Beszéd fordítása

A beszédfelismerés lefordításához a Speech SDK egy mikrofonra vagy egy hangfájl-bemenetre támaszkodik. A beszédfelismerés a beszéd fordítása előtt következik be. Az összes objektum inicializálása után hívja meg a felismerési egyszeri függvényt, és szerezze be az eredményt.

```python
import os
import azure.cognitiveservices.speech as speechsdk

speech_key, service_region = os.environ['SPEECH__SERVICE__KEY'], os.environ['SPEECH__SERVICE__REGION']
from_language, to_languages = 'en-US', 'de'

def translate_speech_to_text():
    translation_config = speechsdk.translation.SpeechTranslationConfig(
            subscription=speech_key, region=service_region)

    translation_config.speech_recognition_language = from_language
    translation_config.add_target_language(to_language)

    recognizer = speechsdk.translation.TranslationRecognizer(
            translation_config=translation_config)
    
    print('Say something...')
    result = recognizer.recognize_once()
    print(get_result_text(reason=result.reason, result=result))

def get_result_text(reason, result):
    reason_format = {
        speechsdk.ResultReason.TranslatedSpeech:
            f'RECOGNIZED "{from_language}": {result.text}\n' +
            f'TRANSLATED into "{to_language}"": {result.translations[to_language]}',
        speechsdk.ResultReason.RecognizedSpeech: f'Recognized: "{result.text}"',
        speechsdk.ResultReason.NoMatch: f'No speech could be recognized: {result.no_match_details}',
        speechsdk.ResultReason.Canceled: f'Speech Recognition canceled: {result.cancellation_details}'
    }
    return reason_format.get(reason, 'Unable to recognize speech')

translate_speech_to_text()
```

A beszédfelismerés [alapjairól a beszédfelismerés alapjait](../../../get-started-speech-to-text.md)ismertető témakörben olvashat bővebben.

## <a name="synthesize-translations"></a>Fordítások szintézise

A sikeres beszédfelismerés és a fordítás után az eredmény egy szótár összes fordítását tartalmazza. A [`translations`][translations] szótár kulcsa a fordítási nyelv, és az érték a lefordított szöveg. A felismert beszéd lefordítható, majd más nyelven (beszéd – beszéd) lehet szintetizálni.

### <a name="event-based-synthesis"></a>Eseményvezérelt szintézis

Az `TranslationRecognizer` objektum egy eseményt tesz elérhetővé `Synthesizing` . Az esemény többször is bekövetkezik, és egy mechanizmust biztosít a szintetizált hang lekéréséhez a fordítási felismerés eredményéről. Ha több nyelvre végez fordítást, olvassa el a [manuális szintézis](#manual-synthesis)című témakört. Adja meg a szintézis hangját, és adjon meg egy eseménykezelőt [`voice_name`][voicename] az `Synthesizing` eseményhez, majd szerezze be a hangot. Az alábbi példa *. wav* -fájlként menti a lefordított hangot.

> [!IMPORTANT]
> Az Event-alapú szintézis csak egyetlen fordítással működik, **ne** adjon hozzá több fordítási nyelvet. Emellett a [`voice_name`][voicename] nyelvnek meg kell egyeznie a cél fordítási nyelvével, például a következőhöz: `"de"` `"de-DE-Hedda"` .

```python
import os
import azure.cognitiveservices.speech as speechsdk

speech_key, service_region = os.environ['SPEECH__SERVICE__KEY'], os.environ['SPEECH__SERVICE__REGION']
from_language, to_language = 'en-US', 'de'

def translate_speech_to_text():
    translation_config = speechsdk.translation.SpeechTranslationConfig(
            subscription=speech_key, region=service_region)

    translation_config.speech_recognition_language = from_language
    translation_config.add_target_language(to_language)

    # See: https://aka.ms/speech/sdkregion#standard-and-neural-voices
    translation_config.voice_name = "de-DE-Hedda"

    recognizer = speechsdk.translation.TranslationRecognizer(
            translation_config=translation_config)

    def synthesis_callback(evt):
        size = len(evt.result.audio)
        print(f'Audio synthesized: {size} byte(s) {"(COMPLETED)" if size == 0 else ""}')

        if size > 0:
            file = open('translation.wav', 'wb+')
            file.write(evt.result.audio)
            file.close()

    recognizer.synthesizing.connect(synthesis_callback)

    print(f'Say something in "{from_language}" and we\'ll translate into "{to_language}".')

    result = recognizer.recognize_once()
    print(get_result_text(reason=result.reason, result=result))

def get_result_text(reason, result):
    reason_format = {
        speechsdk.ResultReason.TranslatedSpeech:
            f'Recognized "{from_language}": {result.text}\n' +
            f'Translated into "{to_language}"": {result.translations[to_language]}',
        speechsdk.ResultReason.RecognizedSpeech: f'Recognized: "{result.text}"',
        speechsdk.ResultReason.NoMatch: f'No speech could be recognized: {result.no_match_details}',
        speechsdk.ResultReason.Canceled: f'Speech Recognition canceled: {result.cancellation_details}'
    }
    return reason_format.get(reason, 'Unable to recognize speech')

translate_speech_to_text()
```

### <a name="manual-synthesis"></a>Manuális szintézis

A [`translations`][translations] szótár használható a hangfordítási szöveg hangszintéziséhez. Ismételje meg az egyes fordításokat, és szintetizálja a fordítást. Egy példány létrehozásakor `SpeechSynthesizer` az `SpeechConfig` objektumnak a [`speech_synthesis_voice_name`][speechsynthesisvoicename] kívánt hangra kell állítania a tulajdonságát. Az alábbi példa öt nyelvet fordít le, és az egyes fordítások a megfelelő neurális nyelven egy hangfájlba kerülnek.

```python
import os
import azure.cognitiveservices.speech as speechsdk

speech_key, service_region = os.environ['SPEECH__SERVICE__KEY'], os.environ['SPEECH__SERVICE__REGION']
from_language, to_languages = 'en-US', [ 'de', 'en', 'it', 'pt', 'zh-Hans' ]

def translate_speech_to_text():
    translation_config = speechsdk.translation.SpeechTranslationConfig(
            subscription=speech_key, region=service_region)

    translation_config.speech_recognition_language = from_language
    for lang in to_languages:
        translation_config.add_target_language(lang)

    recognizer = speechsdk.translation.TranslationRecognizer(
            translation_config=translation_config)
    
    print('Say something...')
    result = recognizer.recognize_once()
    synthesize_translations(result=result)

def synthesize_translations(result):
    language_to_voice_map = {
        "de": "de-DE-KatjaNeural",
        "en": "en-US-AriaNeural",
        "it": "it-IT-ElsaNeural",
        "pt": "pt-BR-FranciscaNeural",
        "zh-Hans": "zh-CN-XiaoxiaoNeural"
    }
    print(f'Recognized: "{result.text}"')

    for language in result.translations:
        translation = result.translations[language]
        print(f'Translated into "{language}": {translation}')

        speech_config = speechsdk.SpeechConfig(subscription=speech_key, region=service_region)
        speech_config.speech_synthesis_voice_name = language_to_voice_map.get(language)
        
        audio_config = speechsdk.audio.AudioOutputConfig(filename=f'{language}-translation.wav')
        speech_synthesizer = speechsdk.SpeechSynthesizer(speech_config=speech_config, audio_config=audio_config)
        speech_synthesizer.speak_text_async(translation).get()

translate_speech_to_text()
```

A Speech szintézissel kapcsolatos további információkért lásd: [a beszédfelismerés alapjai](../../../get-started-text-to-speech.md).

[config]: /python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.translation.speechtranslationconfig
[audioconfig]: /python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.audio.audioconfig
[recognizer]: /python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.translation.translationrecognizer
[recognitionlang]: /python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig
[addlang]: /python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.translation.speechtranslationconfig#add-target-language-language--str-
[translations]: /python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.translation.translationrecognitionresult#translations
[voicename]: /python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.translation.speechtranslationconfig#voice-name
[speechsynthesisvoicename]: /python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig#speech-synthesis-voice-name