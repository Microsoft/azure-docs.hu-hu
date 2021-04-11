---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 04/13/2020
ms.author: trbye
ms.custom: devx-track-csharp
ms.openlocfilehash: 798db8e438a78fb818bd0af18f84c87c1f9ceb84
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105104513"
---
A Speech Service egyik fő funkciója az emberi beszéd felismerése és más nyelvekre való lefordítása. Ebből a rövid útmutatóból megtudhatja, hogyan használhatja a Speech SDK-t az alkalmazásaiban és termékeiben, hogy kiváló minőségű hangfordítást végezzen. Ez a rövid útmutató a következő témaköröket tartalmazza:

* Beszéd – szöveg fordítása
* Beszéd lefordítása több célként megadott nyelvre
* Közvetlen beszéd – beszéd fordítás végrehajtása

## <a name="skip-to-samples-on-github"></a>Ugrás a mintákra a GitHubon

Ha közvetlenül a mintakód kihagyását szeretné kihagyni, tekintse meg a [C# gyors példákat](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/csharp/dotnet/translate-speech-to-text) a githubon.

## <a name="prerequisites"></a>Előfeltételek

Ez a cikk azt feltételezi, hogy rendelkezik Azure-fiókkal és Speech Service-előfizetéssel. Ha nem rendelkezik fiókkal és előfizetéssel, [próbálja ki ingyenesen a Speech szolgáltatást](../../../overview.md#try-the-speech-service-for-free).

## <a name="install-the-speech-sdk"></a>A Speech SDK telepítése

Mielőtt bármit elvégezhet, telepítenie kell a Speech SDK-t. A platformtól függően kövesse a _SPEECH SDK névjegye_ című cikk a <a href="/azure/cognitive-services/speech-service/speech-sdk#get-the-speech-sdk" target="_blank">Speech SDK beszerzése</a> című szakaszában található utasításokat.

## <a name="import-dependencies"></a>Függőségek importálása

A cikkben szereplő példák futtatásához foglalja bele a következő `using` utasításokat a *program. cs* fájl elejére.

```csharp
using System;
using System.Collections.Generic;
using System.IO;
using System.Text;
using System.Threading.Tasks;
using Microsoft.CognitiveServices.Speech;
using Microsoft.CognitiveServices.Speech.Audio;
using Microsoft.CognitiveServices.Speech.Translation;
```

## <a name="sensitive-data-and-environment-variables"></a>Bizalmas adatok és környezeti változók

A cikkben szereplő mintakód a bizalmas adatok tárolására szolgáló környezeti változóktól, például a beszédfelismerési erőforrás-előfizetési kulcstól és régiótól függ. Az `Program` osztály két olyan `static readonly string` értéket tartalmaz, amelyek a gazdagép-gépek környezeti változói alapján vannak hozzárendelve, nevezetesen `SPEECH__SUBSCRIPTION__KEY` és `SPEECH__SERVICE__REGION` . Mindkét mező a Class (osztály) hatókörében szerepel, így azok elérhetővé válnak az osztály metódus szervein belül. További információ a környezeti változókról: [környezeti változók és alkalmazás konfigurációja](../../../../cognitive-services-security.md#environment-variables-and-application-configuration).

```csharp
public class Program
{
    static readonly string SPEECH__SUBSCRIPTION__KEY =
        Environment.GetEnvironmentVariable(nameof(SPEECH__SUBSCRIPTION__KEY));
    
    static readonly string SPEECH__SERVICE__REGION =
        Environment.GetEnvironmentVariable(nameof(SPEECH__SERVICE__REGION));

    static Task Main() => Task.CompletedTask;
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

```csharp
public class Program
{
    static readonly string SPEECH__SUBSCRIPTION__KEY =
        Environment.GetEnvironmentVariable(nameof(SPEECH__SUBSCRIPTION__KEY));
    
    static readonly string SPEECH__SERVICE__REGION =
        Environment.GetEnvironmentVariable(nameof(SPEECH__SERVICE__REGION));

    static Task Main() => TranslateSpeechAsync();

    static async Task TranslateSpeechAsync()
    {
        var translationConfig =
            SpeechTranslationConfig.FromSubscription(SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);
    }
}
```

## <a name="change-source-language"></a>Forrás nyelvének módosítása

A beszéd fordításának egyik gyakori feladata, hogy megadja a bemeneti (vagy a forrás) nyelvet. Vessünk egy pillantást arra, hogyan változtathatja meg a szövegbeviteli nyelvet az olasz nyelvre. A kódban lépjen kapcsolatba a [`SpeechTranslationConfig`][config] példánnyal, és rendelje hozzá a `SpeechRecognitionLanguage` tulajdonsághoz.

```csharp
static async Task TranslateSpeechAsync()
{
    var translationConfig =
        SpeechTranslationConfig.FromSubscription(SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);

    // Source (input) language
    translationConfig.SpeechRecognitionLanguage = "it-IT";
}
```

A [`SpeechRecognitionLanguage`][recognitionlang] tulajdonság nyelvi területi beállítású karakterláncot vár. A **területi beállítások** oszlopban bármilyen értéket megadhat a támogatott [területi beállítások/nyelvek](../../../language-support.md)listájában.

## <a name="add-translation-language"></a>Fordítási nyelv hozzáadása

A beszédfelismerés egy másik gyakori feladata, hogy megadják a cél fordítási nyelveket, de legalább egy szükséges, de a többszörösek támogatottak. A következő kódrészlet a franciát és a német nyelvet állítja be fordítási nyelvi célokként.

```csharp
static async Task TranslateSpeechAsync()
{
    var translationConfig =
        SpeechTranslationConfig.FromSubscription(SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);

    translationConfig.SpeechRecognitionLanguage = "it-IT";
    
    // Translate to languages. See, https://aka.ms/speech/sttt-languages
    translationConfig.AddTargetLanguage("fr");
    translationConfig.AddTargetLanguage("de");
}
```

Minden hívás [`AddTargetLanguage`][addlang] esetén új fordítási nyelv van megadva. Más szóval, ha a beszédfelismerést a forrás nyelvéről ismerik fel, minden cél fordítás az eredményül kapott fordítási művelet részeként érhető el.

## <a name="initialize-a-translation-recognizer"></a>Fordító-felismerő inicializálása

Miután létrehozta a [`SpeechTranslationConfig`][config] -t, a következő lépés a inicializálása [`TranslationRecognizer`][recognizer] . A inicializálásakor [`TranslationRecognizer`][recognizer] át kell adnia a következőt: `translationConfig` . A konfigurációs objektum biztosítja azokat a hitelesítő adatokat, amelyeket a beszédfelismerési szolgáltatás a kérelem érvényesítéséhez igényel.

Ha az eszköz alapértelmezett mikrofonjának használatával ismeri fel a beszédet, a következőképpen [`TranslationRecognizer`][recognizer] kell kinéznie:

```csharp
static async Task TranslateSpeechAsync()
{
    var translationConfig =
        SpeechTranslationConfig.FromSubscription(SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);

    var fromLanguage = "en-US";
    var toLanguages = new List<string> { "it", "fr", "de" };
    translationConfig.SpeechRecognitionLanguage = fromLanguage;
    toLanguages.ForEach(translationConfig.AddTargetLanguage);

    using var recognizer = new TranslationRecognizer(translationConfig);
}
```

Ha meg szeretné adni a hangbemeneti eszközt, létre kell hoznia egy-t, és meg kell [`AudioConfig`][audioconfig] adnia a `audioConfig` paramétert a inicializálásakor [`TranslationRecognizer`][recognizer] .

> [!TIP]
> [Ismerje meg, hogyan kérheti le az eszköz azonosítóját a hangbemeneti eszközhöz](../../../how-to-select-audio-input-devices.md).

Először az alábbi módon hivatkozhat az `AudioConfig` objektumra:

```csharp
static async Task TranslateSpeechAsync()
{
    var translationConfig =
        SpeechTranslationConfig.FromSubscription(SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);
    
    var fromLanguage = "en-US";
    var toLanguages = new List<string> { "it", "fr", "de" };
    translationConfig.SpeechRecognitionLanguage = fromLanguage;
    toLanguages.ForEach(translationConfig.AddTargetLanguage);

    using var audioConfig = AudioConfig.FromDefaultMicrophoneInput();
    using var recognizer = new TranslationRecognizer(translationConfig, audioConfig);
}
```

Ha mikrofon használata helyett hangfájlt szeretne biztosítani, akkor továbbra is meg kell adnia a következőt: `audioConfig` . Ha azonban a létrehozása helyett a (z) metódust hozza létre [`AudioConfig`][audioconfig] `FromDefaultMicrophoneInput` , hívja meg `FromWavFileInput` és adja át a `filename` paramétert.

```csharp
static async Task TranslateSpeechAsync()
{
    var translationConfig =
        SpeechTranslationConfig.FromSubscription(SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);
    
    var fromLanguage = "en-US";
    var toLanguages = new List<string> { "it", "fr", "de" };
    translationConfig.SpeechRecognitionLanguage = fromLanguage;
    toLanguages.ForEach(translationConfig.AddTargetLanguage);

    using var audioConfig = AudioConfig.FromWavFileInput("YourAudioFile.wav");
    using var recognizer = new TranslationRecognizer(translationConfig, audioConfig);
}
```

## <a name="translate-speech"></a>Beszéd fordítása

A beszédfelismerés lefordításához a Speech SDK egy mikrofonra vagy egy hangfájl-bemenetre támaszkodik. A beszédfelismerés a beszéd fordítása előtt következik be. Az összes objektum inicializálása után hívja meg a felismerési egyszeri függvényt, és szerezze be az eredményt.

```csharp
static async Task TranslateSpeechAsync()
{
    var translationConfig =
        SpeechTranslationConfig.FromSubscription(SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);
    
    var fromLanguage = "en-US";
    var toLanguages = new List<string> { "it", "fr", "de" };
    translationConfig.SpeechRecognitionLanguage = fromLanguage;
    toLanguages.ForEach(translationConfig.AddTargetLanguage);

    using var recognizer = new TranslationRecognizer(translationConfig);

    Console.Write($"Say something in '{fromLanguage}' and ");
    Console.WriteLine($"we'll translate into '{string.Join("', '", toLanguages)}'.\n");
    
    var result = await recognizer.RecognizeOnceAsync();
    if (result.Reason == ResultReason.TranslatedSpeech)
    {
        Console.WriteLine($"Recognized: \"{result.Text}\":");
        foreach (var (language, translation) in result.Translations)
        {
            Console.WriteLine($"Translated into '{language}': {translation}");
        }
    }
}
```

A beszédfelismerés [alapjairól a beszédfelismerés alapjait](../../../get-started-speech-to-text.md)ismertető témakörben olvashat bővebben.

## <a name="synthesize-translations"></a>Fordítások szintézise

A sikeres beszédfelismerés és a fordítás után az eredmény egy szótár összes fordítását tartalmazza. A [`Translations`][translations] szótár kulcsa a fordítási nyelv, és az érték a lefordított szöveg. A felismert beszéd lefordítható, majd más nyelven (beszéd – beszéd) lehet szintetizálni.

### <a name="event-based-synthesis"></a>Eseményvezérelt szintézis

Az `TranslationRecognizer` objektum egy eseményt tesz elérhetővé `Synthesizing` . Az esemény többször is bekövetkezik, és egy mechanizmust biztosít a szintetizált hang lekéréséhez a fordítási felismerés eredményéről. Ha több nyelvre végez fordítást, olvassa el a [manuális szintézis](#manual-synthesis)című témakört. Adja meg a szintézis hangját, és adjon meg egy eseménykezelőt [`VoiceName`][voicename] az `Synthesizing` eseményhez, majd szerezze be a hangot. Az alábbi példa *. wav* -fájlként menti a lefordított hangot.

> [!IMPORTANT]
> Az Event-alapú szintézis csak egyetlen fordítással működik, **ne** adjon hozzá több fordítási nyelvet. Emellett a [`VoiceName`][voicename] nyelvnek meg kell egyeznie a cél fordítási nyelvével, például a következőhöz: `"de"` `"de-DE-Hedda"` .

```csharp
static async Task TranslateSpeechAsync()
{
    var translationConfig =
        SpeechTranslationConfig.FromSubscription(SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);
    
    var fromLanguage = "en-US";
    var toLanguage = "de";
    translationConfig.SpeechRecognitionLanguage = fromLanguage;
    translationConfig.AddTargetLanguage(toLanguage);

    // See: https://aka.ms/speech/sdkregion#standard-and-neural-voices
    translationConfig.VoiceName = "de-DE-Hedda";

    using var recognizer = new TranslationRecognizer(translationConfig);

    recognizer.Synthesizing += (_, e) =>
    {
        var audio = e.Result.GetAudio();
        Console.WriteLine($"Audio synthesized: {audio.Length:#,0} byte(s) {(audio.Length == 0 ? "(Complete)" : "")}");

        if (audio.Length > 0)
        {
            File.WriteAllBytes("YourAudioFile.wav", audio);
        }
    };

    Console.Write($"Say something in '{fromLanguage}' and ");
    Console.WriteLine($"we'll translate into '{toLanguage}'.\n");

    var result = await recognizer.RecognizeOnceAsync();
    if (result.Reason == ResultReason.TranslatedSpeech)
    {
        Console.WriteLine($"Recognized: \"{result.Text}\"");
        Console.WriteLine($"Translated into '{toLanguage}': {result.Translations[toLanguage]}");
    }
}
```

### <a name="manual-synthesis"></a>Manuális szintézis

A [`Translations`][translations] szótár használható a hangfordítási szöveg hangszintéziséhez. Ismételje meg az egyes fordításokat, és szintetizálja a fordítást. Egy példány létrehozásakor `SpeechSynthesizer` az `SpeechConfig` objektumnak a [`SpeechSynthesisVoiceName`][speechsynthesisvoicename] kívánt hangra kell állítania a tulajdonságát. Az alábbi példa öt nyelvet fordít le, és az egyes fordítások a megfelelő neurális nyelven egy hangfájlba kerülnek.

```csharp
static async Task TranslateSpeechAsync()
{
    var translationConfig =
        SpeechTranslationConfig.FromSubscription(SPEECH__SERVICE__KEY, SPEECH__SERVICE__REGION);

    var fromLanguage = "en-US";
    var toLanguages = new List<string> { "de", "en", "it", "pt", "zh-Hans" };
    translationConfig.SpeechRecognitionLanguage = fromLanguage;
    toLanguages.ForEach(translationConfig.AddTargetLanguage);

    using var recognizer = new TranslationRecognizer(translationConfig);

    Console.Write($"Say something in '{fromLanguage}' and ");
    Console.WriteLine($"we'll translate into '{string.Join("', '", toLanguages)}'.\n");

    var result = await recognizer.RecognizeOnceAsync();
    if (result.Reason == ResultReason.TranslatedSpeech)
    {
        // See: https://aka.ms/speech/sdkregion#standard-and-neural-voices
        var languageToVoiceMap = new Dictionary<string, string>
        {
            ["de"] = "de-DE-KatjaNeural",
            ["en"] = "en-US-AriaNeural",
            ["it"] = "it-IT-ElsaNeural",
            ["pt"] = "pt-BR-FranciscaNeural",
            ["zh-Hans"] = "zh-CN-XiaoxiaoNeural"
        };

        Console.WriteLine($"Recognized: \"{result.Text}\"");

        foreach (var (language, translation) in result.Translations)
        {
            Console.WriteLine($"Translated into '{language}': {translation}");

            var speechConfig =
                SpeechConfig.FromSubscription(
                    SPEECH__SERVICE__KEY, SPEECH__SERVICE__REGION);
            speechConfig.SpeechSynthesisVoiceName = languageToVoiceMap[language];

            using var audioConfig = AudioConfig.FromWavFileOutput($"{language}-translation.wav");
            using var synthesizer = new SpeechSynthesizer(speechConfig, audioConfig);
            
            await synthesizer.SpeakTextAsync(translation);
        }
    }
}
```

A Speech szintézissel kapcsolatos további információkért lásd: [a beszédfelismerés alapjai](../../../get-started-text-to-speech.md).

[config]: /dotnet/api/microsoft.cognitiveservices.speech.speechtranslationconfig
[audioconfig]: /dotnet/api/microsoft.cognitiveservices.speech.audio.audioconfig
[recognizer]: /dotnet/api/microsoft.cognitiveservices.speech.translation.translationrecognizer
[recognitionlang]: /dotnet/api/microsoft.cognitiveservices.speech.speechconfig.speechrecognitionlanguage
[addlang]: /dotnet/api/microsoft.cognitiveservices.speech.speechtranslationconfig.addtargetlanguage
[translations]: /dotnet/api/microsoft.cognitiveservices.speech.translation.translationrecognitionresult.translations
[voicename]: /dotnet/api/microsoft.cognitiveservices.speech.speechtranslationconfig.voicename
[speechsynthesisvoicename]: /dotnet/api/microsoft.cognitiveservices.speech.speechconfig.speechsynthesisvoicename