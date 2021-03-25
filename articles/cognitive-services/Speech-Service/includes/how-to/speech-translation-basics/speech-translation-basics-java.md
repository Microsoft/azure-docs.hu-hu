---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 04/13/2020
ms.custom: devx-track-java
ms.author: trbye
ms.openlocfilehash: e030e6466570d3fc2abf809ca09d46c9f596b765
ms.sourcegitcommit: ed7376d919a66edcba3566efdee4bc3351c57eda
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2021
ms.locfileid: "105104599"
---
A Speech Service egyik fő funkciója az emberi beszéd felismerése és más nyelvekre való lefordítása. Ebből a rövid útmutatóból megtudhatja, hogyan használhatja a Speech SDK-t az alkalmazásaiban és termékeiben, hogy kiváló minőségű hangfordítást végezzen. Ez a rövid útmutató a következő témaköröket tartalmazza:

* Beszéd – szöveg fordítása
* Beszéd lefordítása több célként megadott nyelvre
* Közvetlen beszéd – beszéd fordítás végrehajtása

## <a name="skip-to-samples-on-github"></a>Ugrás a mintákra a GitHubon

Ha egyenesen a mintakód kihagyása mellett szeretne kiugrani, tekintse meg a Java-gyors üzembe helyezési [mintákat](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/java/jre/translate-speech-to-text) a githubon.

## <a name="prerequisites"></a>Előfeltételek

Ez a cikk azt feltételezi, hogy rendelkezik Azure-fiókkal és Speech Service-előfizetéssel. Ha nem rendelkezik fiókkal és előfizetéssel, [próbálja ki ingyenesen a Speech szolgáltatást](../../../overview.md#try-the-speech-service-for-free).

## <a name="install-the-speech-sdk"></a>A Speech SDK telepítése

Mielőtt bármit elvégezhet, telepítenie kell a Speech SDK-t. A platformtól függően kövesse a _SPEECH SDK névjegye_ című cikk a <a href="/azure/cognitive-services/speech-service/speech-sdk#get-the-speech-sdk" target="_blank">Speech SDK beszerzése</a> című szakaszában található utasításokat.

## <a name="import-dependencies"></a>Függőségek importálása

A cikkben szereplő példák futtatásához foglalja bele a következő `import` utasításokat a **. Java* -kód fájl.

```java
package speech;

import java.io.*;
import java.util.*;
import java.util.concurrent.*;
import com.microsoft.cognitiveservices.speech.*;
import com.microsoft.cognitiveservices.speech.audio.*;
import com.microsoft.cognitiveservices.speech.translation.*;
```

## <a name="sensitive-data-and-environment-variables"></a>Bizalmas adatok és környezeti változók

A cikkben szereplő mintakód a bizalmas adatok tárolására szolgáló környezeti változóktól, például a beszédfelismerési erőforrás-előfizetési kulcstól és régiótól függ. A Java-kódrészlet két olyan `static final String` értéket tartalmaz, amelyek a gazdagép-gépek környezeti változói alapján vannak hozzárendelve, nevezetesen `SPEECH__SUBSCRIPTION__KEY` és `SPEECH__SERVICE__REGION` . Mindkét mező a Class (osztály) hatókörében szerepel, így azok elérhetővé válnak az osztály metódus szervein belül. További információ a környezeti változókról: [környezeti változók és alkalmazás konfigurációja](../../../../cognitive-services-security.md#environment-variables-and-application-configuration).

```java
public class App {

    static final String SPEECH__SUBSCRIPTION__KEY = System.getenv("SPEECH__SUBSCRIPTION__KEY");
    static final String SPEECH__SERVICE__REGION = System.getenv("SPEECH__SERVICE__REGION");

    public static void main(String[] args) { }
}
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

```java
public class App {

    static final String SPEECH__SUBSCRIPTION__KEY = System.getenv("SPEECH__SERVICE__KEY");
    static final String SPEECH__SERVICE__REGION = System.getenv("SPEECH__SERVICE__REGION");

    public static void main(String[] args) {
        try {
            translateSpeech();
            System.exit(0);
        } catch (Exception ex) {
            System.out.println(ex);
            System.exit(1);
        }
    }

    static void translateSpeech() {
        SpeechTranslationConfig config = SpeechTranslationConfig.fromSubscription(
            SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);
    }
}
```

## <a name="change-source-language"></a>Forrás nyelvének módosítása

A beszéd fordításának egyik gyakori feladata, hogy megadja a bemeneti (vagy a forrás) nyelvet. Vessünk egy pillantást arra, hogyan változtathatja meg a szövegbeviteli nyelvet az olasz nyelvre. A kódban lépjen kapcsolatba a [`SpeechTranslationConfig`][config] példánnyal, és hívja meg a `setSpeechRecognitionLanguage` metódust.

```java
static void translateSpeech() {
    SpeechTranslationConfig translationConfig = SpeechTranslationConfig.fromSubscription(
        SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);
    
    // Source (input) language
    translationConfig.setSpeechRecognitionLanguage("it-IT");
}
```

A [`setSpeechRecognitionLanguage`][recognitionlang] függvény nyelvi területi beállítású karakterláncot vár. A **területi beállítások** oszlopban bármilyen értéket megadhat a támogatott [területi beállítások/nyelvek](../../../language-support.md)listájában.

## <a name="add-translation-language"></a>Fordítási nyelv hozzáadása

A beszédfelismerés egy másik gyakori feladata, hogy megadják a cél fordítási nyelveket, de legalább egy szükséges, de a többszörösek támogatottak. A következő kódrészlet a franciát és a német nyelvet állítja be fordítási nyelvi célokként.

```java
static void translateSpeech() {
    SpeechTranslationConfig translationConfig = SpeechTranslationConfig.fromSubscription(
        SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);
    
    translationConfig.setSpeechRecognitionLanguage("it-IT");

    // Translate to languages. See, https://aka.ms/speech/sttt-languages
    translationConfig.addTargetLanguage("fr");
    translationConfig.addTargetLanguage("de");
}
```

Minden hívás [`addTargetLanguage`][addlang] esetén új fordítási nyelv van megadva. Más szóval, ha a beszédfelismerést a forrás nyelvéről ismerik fel, minden cél fordítás az eredményül kapott fordítási művelet részeként érhető el.

## <a name="initialize-a-translation-recognizer"></a>Fordító-felismerő inicializálása

Miután létrehozta a [`SpeechTranslationConfig`][config] -t, a következő lépés a inicializálása [`TranslationRecognizer`][recognizer] . A inicializálásakor [`TranslationRecognizer`][recognizer] át kell adnia a következőt: `translationConfig` . A konfigurációs objektum biztosítja azokat a hitelesítő adatokat, amelyeket a beszédfelismerési szolgáltatás a kérelem érvényesítéséhez igényel.

Ha az eszköz alapértelmezett mikrofonjának használatával ismeri fel a beszédet, a következőképpen [`TranslationRecognizer`][recognizer] kell kinéznie:

```java
static void translateSpeech() {
    SpeechTranslationConfig translationConfig = SpeechTranslationConfig.fromSubscription(
        SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);
    
    String fromLanguage = "en-US";
    String[] toLanguages = { "it", "fr", "de" };
    translationConfig.setSpeechRecognitionLanguage(fromLanguage);
    for (String language : toLanguages) {
        translationConfig.addTargetLanguage(language);
    }

    try (TranslationRecognizer recognizer = new TranslationRecognizer(translationConfig)) {
    }
}
```

Ha meg szeretné adni a hangbemeneti eszközt, létre kell hoznia egy-t, és meg kell [`AudioConfig`][audioconfig] adnia a `audioConfig` paramétert a inicializálásakor [`TranslationRecognizer`][recognizer] .

> [!TIP]
> [Ismerje meg, hogyan kérheti le az eszköz azonosítóját a hangbemeneti eszközhöz](../../../how-to-select-audio-input-devices.md).

Először az alábbi módon hivatkozhat az `AudioConfig` objektumra:

```java
static void translateSpeech() {
    SpeechTranslationConfig translationConfig = SpeechTranslationConfig.fromSubscription(
        SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);
    
    String fromLanguage = "en-US";
    String[] toLanguages = { "it", "fr", "de" };
    translationConfig.setSpeechRecognitionLanguage(fromLanguage);
    for (String language : toLanguages) {
        translationConfig.addTargetLanguage(language);
    }

    AudioConfig audioConfig = AudioConfig.fromDefaultMicrophoneInput();
    try (TranslationRecognizer recognizer = new TranslationRecognizer(translationConfig, audioConfig)) {
        
    }
}
```

Ha mikrofon használata helyett hangfájlt szeretne biztosítani, akkor továbbra is meg kell adnia a következőt: `audioConfig` . Ha azonban a létrehozása helyett a (z) metódust hozza létre [`AudioConfig`][audioconfig] `fromDefaultMicrophoneInput` , hívja meg `fromWavFileInput` és adja át a `filename` paramétert.

```java
static void translateSpeech() {
    SpeechTranslationConfig translationConfig = SpeechTranslationConfig.fromSubscription(
        SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);
    
    String fromLanguage = "en-US";
    String[] toLanguages = { "it", "fr", "de" };
    translationConfig.setSpeechRecognitionLanguage(fromLanguage);
    for (String language : toLanguages) {
        translationConfig.addTargetLanguage(language);
    }

    AudioConfig audioConfig = AudioConfig.fromWavFileInput("YourAudioFile.wav");
    try (TranslationRecognizer recognizer = new TranslationRecognizer(translationConfig, audioConfig)) {
        
    }
}
```

## <a name="translate-speech"></a>Beszéd fordítása

A beszédfelismerés lefordításához a Speech SDK egy mikrofonra vagy egy hangfájl-bemenetre támaszkodik. A beszédfelismerés a beszéd fordítása előtt következik be. Az összes objektum inicializálása után hívja meg a felismerési egyszeri függvényt, és szerezze be az eredményt.

```java
static void translateSpeech() throws ExecutionException, InterruptedException {
    SpeechTranslationConfig translationConfig = SpeechTranslationConfig.fromSubscription(
        SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);
    
    String fromLanguage = "en-US";
    String[] toLanguages = { "it", "fr", "de" };
    translationConfig.setSpeechRecognitionLanguage(fromLanguage);
    for (String language : toLanguages) {
        translationConfig.addTargetLanguage(language);
    }

    try (TranslationRecognizer recognizer = new TranslationRecognizer(translationConfig)) {
        System.out.printf("Say something in '%s' and we'll translate...", fromLanguage);

        TranslationRecognitionResult result = recognizer.recognizeOnceAsync().get();
        if (result.getReason() == ResultReason.TranslatedSpeech) {
            System.out.printf("Recognized: \"%s\"\n", result.getText());
            for (Map.Entry<String, String> pair : result.getTranslations().entrySet()) {
                System.out.printf("Translated into '%s': %s\n", pair.getKey(), pair.getValue());
            }
        }
    }
}
```

A beszédfelismerés [alapjairól a beszédfelismerés alapjait](../../../get-started-speech-to-text.md)ismertető témakörben olvashat bővebben.

## <a name="synthesize-translations"></a>Fordítások szintézise

A sikeres beszédfelismerés és a fordítás után az eredmény egy szótár összes fordítását tartalmazza. A [`getTranslations`][translations] függvény egy olyan szótárt ad vissza, amelynek a kulcsa a célként megadott fordítási nyelv, és az érték a lefordított szöveg. A felismert beszéd lefordítható, majd más nyelven (beszéd – beszéd) lehet szintetizálni.

### <a name="event-based-synthesis"></a>Eseményvezérelt szintézis

Az `TranslationRecognizer` objektum egy eseményt tesz elérhetővé `synthesizing` . Az esemény többször is bekövetkezik, és egy mechanizmust biztosít a szintetizált hang lekéréséhez a fordítási felismerés eredményéről. Ha több nyelvre végez fordítást, olvassa el a [manuális szintézis](#manual-synthesis)című témakört. Adja meg a szintézis hangját, és adjon meg egy eseménykezelőt [`setVoiceName`][voicename] az `synthesizing` eseményhez, majd szerezze be a hangot. Az alábbi példa *. wav* -fájlként menti a lefordított hangot.

> [!IMPORTANT]
> Az Event-alapú szintézis csak egyetlen fordítással működik, **ne** adjon hozzá több fordítási nyelvet. Emellett a [`setVoiceName`][voicename] nyelvnek meg kell egyeznie a cél fordítási nyelvével, például a következőhöz: `"de"` `"de-DE-Hedda"` .

```java
static void translateSpeech() throws ExecutionException, FileNotFoundException, InterruptedException, IOException {
    SpeechTranslationConfig translationConfig = SpeechTranslationConfig.fromSubscription(
        SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);

    String fromLanguage = "en-US";
    String toLanguage = "de";
    translationConfig.setSpeechRecognitionLanguage(fromLanguage);
    translationConfig.addTargetLanguage(toLanguage);

    // See: https://aka.ms/speech/sdkregion#standard-and-neural-voices
    translationConfig.setVoiceName("de-DE-Hedda");

    try (TranslationRecognizer recognizer = new TranslationRecognizer(translationConfig)) {
        recognizer.synthesizing.addEventListener((s, e) -> {
            byte[] audio = e.getResult().getAudio();
            int size = audio.length;
            System.out.println("Audio synthesized: " + size + " byte(s)" + (size == 0 ? "(COMPLETE)" : ""));

            if (size > 0) {
                try (FileOutputStream file = new FileOutputStream("translation.wav")) {
                    file.write(audio);
                } catch (IOException ex) {
                    ex.printStackTrace();
                }
            }
        });

        System.out.printf("Say something in '%s' and we'll translate...", fromLanguage);

        TranslationRecognitionResult result = recognizer.recognizeOnceAsync().get();
        if (result.getReason() == ResultReason.TranslatedSpeech) {
            System.out.printf("Recognized: \"%s\"\n", result.getText());
            for (Map.Entry<String, String> pair : result.getTranslations().entrySet()) {
                String language = pair.getKey();
                String translation = pair.getValue();
                System.out.printf("Translated into '%s': %s\n", language, translation);
            }
        }
    }
}
```

### <a name="manual-synthesis"></a>Manuális szintézis

A [`getTranslations`][translations] függvény egy olyan szótárt ad vissza, amely a hanganyagnak a fordítási szövegből való szintetizálása céljából használható. Ismételje meg az egyes fordításokat, és szintetizálja a fordítást. Egy példány létrehozásakor `SpeechSynthesizer` az `SpeechConfig` objektumnak a [`setSpeechSynthesisVoiceName`][speechsynthesisvoicename] kívánt hangra kell állítania a tulajdonságát. Az alábbi példa öt nyelvet fordít le, és az egyes fordítások a megfelelő neurális nyelven egy hangfájlba kerülnek.

```java
static void translateSpeech() throws ExecutionException, InterruptedException {
    SpeechTranslationConfig translationConfig = SpeechTranslationConfig.fromSubscription(
        SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);
    
    String fromLanguage = "en-US";
    String[] toLanguages = { "de", "en", "it", "pt", "zh-Hans" };
    translationConfig.setSpeechRecognitionLanguage(fromLanguage);
    for (String language : toLanguages) {
        translationConfig.addTargetLanguage(language);
    }

    try (TranslationRecognizer recognizer = new TranslationRecognizer(translationConfig)) {
        System.out.printf("Say something in '%s' and we'll translate...", fromLanguage);

        TranslationRecognitionResult result = recognizer.recognizeOnceAsync().get();
        if (result.getReason() == ResultReason.TranslatedSpeech) {
            // See: https://aka.ms/speech/sdkregion#standard-and-neural-voices
            Map<String, String> languageToVoiceMap = new HashMap<String, String>();
            languageToVoiceMap.put("de", "de-DE-KatjaNeural");
            languageToVoiceMap.put("en", "en-US-AriaNeural");
            languageToVoiceMap.put("it", "it-IT-ElsaNeural");
            languageToVoiceMap.put("pt", "pt-BR-FranciscaNeural");
            languageToVoiceMap.put("zh-Hans", "zh-CN-XiaoxiaoNeural");

            System.out.printf("Recognized: \"%s\"\n", result.getText());
            for (Map.Entry<String, String> pair : result.getTranslations().entrySet()) {
                String language = pair.getKey();
                String translation = pair.getValue();
                System.out.printf("Translated into '%s': %s\n", language, translation);

                SpeechConfig speechConfig =
                    SpeechConfig.fromSubscription(SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);
                speechConfig.setSpeechSynthesisVoiceName(languageToVoiceMap.get(language));

                AudioConfig audioConfig = AudioConfig.fromWavFileOutput(language + "-translation.wav");
                try (SpeechSynthesizer synthesizer = new SpeechSynthesizer(speechConfig, audioConfig)) {
                    synthesizer.SpeakTextAsync(translation).get();
                }
            }
        }
    }
}
```

A Speech szintézissel kapcsolatos további információkért lásd: [a beszédfelismerés alapjai](../../../get-started-text-to-speech.md).

[config]: /java/api/com.microsoft.cognitiveservices.speech.translation.SpeechTranslationConfig
[audioconfig]: /java/api/com.microsoft.cognitiveservices.speech.audio.AudioConfig
[recognizer]: /java/api/com.microsoft.cognitiveservices.speech.translation.TranslationRecognizer
[recognitionlang]: /java/api/com.microsoft.cognitiveservices.speech.speechconfig.setspeechrecognitionlanguage
[addlang]: /java/api/com.microsoft.cognitiveservices.speech.translation.speechtranslationconfig.addtargetlanguage
[translations]: /java/api/com.microsoft.cognitiveservices.speech.translation.translationrecognitionresult.gettranslations
[voicename]: /java/api/com.microsoft.cognitiveservices.speech.translation.speechtranslationconfig.setvoicename
[speechsynthesisvoicename]: /java/api/com.microsoft.cognitiveservices.speech.speechconfig.setspeechsynthesisvoicename