---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/11/2020
ms.author: trbye
ms.custom: devx-track-csharp
ms.openlocfilehash: 7c4b368c6b02304a6d73e7929ff2d7a93f0921ee
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/07/2021
ms.locfileid: "102445583"
---
A beszédfelismerési szolgáltatás egyik fő funkciója az emberi beszéd (más néven beszéd – szöveg) felismerése és átírása. Ebből a rövid útmutatóból megtudhatja, hogyan használhatja a Speech SDK-t az alkalmazásaiban és termékeiben a kiváló minőségű beszéd-szöveg átalakítás elvégzéséhez.

## <a name="skip-to-samples-on-github"></a>Ugrás a mintákra a GitHubon

Ha közvetlenül a mintakód kihagyását szeretné kihagyni, tekintse meg a [C# gyors példákat](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/csharp/dotnet) a githubon.

## <a name="prerequisites"></a>Előfeltételek

Ez a cikk azt feltételezi, hogy rendelkezik Azure-fiókkal és Speech Service-előfizetéssel. Ha nem rendelkezik fiókkal és előfizetéssel, [próbálja ki ingyenesen a Speech szolgáltatást](../../../overview.md#try-the-speech-service-for-free).

## <a name="install-the-speech-sdk"></a>A Speech SDK telepítése

Ha csak a csomag nevét szeretné megkezdeni, futtassa a parancsot `Install-Package Microsoft.CognitiveServices.Speech` a NuGet-konzolon.

A platformra vonatkozó telepítési utasításokért tekintse meg az alábbi hivatkozásokat:

* <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/setup-platform?tabs=dotnet&pivots=programming-language-csharp" target="_blank">.NET-keretrendszer </a>
* <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/setup-platform?tabs=dotnetcore&pivots=programming-language-csharp" target="_blank">.NET Core </a>
* <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/setup-platform?tabs=unity&pivots=programming-language-csharp" target="_blank">Unity </a>
* <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/setup-platform?tabs=uwps&pivots=programming-language-csharp" target="_blank">UWP </a>
* <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/setup-platform?tabs=xaml&pivots=programming-language-csharp" target="_blank">Xamarin </a>

## <a name="create-a-speech-configuration"></a>Beszédfelismerési konfiguráció létrehozása

A beszédfelismerési szolgáltatás a Speech SDK használatával történő meghívásához létre kell hoznia egy [`SpeechConfig`](/dotnet/api/microsoft.cognitiveservices.speech.speechconfig) . Ez az osztály az előfizetésével kapcsolatos információkat tartalmaz, például a kulcsot és a társított régiót, végpontot, gazdagépet vagy engedélyezési jogkivonatot. Hozzon létre egy [`SpeechConfig`](/dotnet/api/microsoft.cognitiveservices.speech.speechconfig) -t a kulcs és a régió használatával. A [kulcsok és régiók keresése](../../../overview.md#find-keys-and-region) lapon találja meg a kulcs-régió párokat.

```csharp
using System;
using System.IO;
using System.Threading.Tasks;
using Microsoft.CognitiveServices.Speech;
using Microsoft.CognitiveServices.Speech.Audio;

class Program 
{
    async static Task Main(string[] args)
    {
        var speechConfig = SpeechConfig.FromSubscription("<paste-your-subscription-key>", "<paste-your-region>");
    }
}
```

Néhány más módon is elvégezhető a következők inicializálása [`SpeechConfig`](/dotnet/api/microsoft.cognitiveservices.speech.speechconfig) :

* Egy végponttal: pass a Speech Service-végponton. Kulcs-vagy engedélyezési jogkivonat nem kötelező.
* Gazdagép esetén: adjon meg egy gazdagép-címeket. Kulcs-vagy engedélyezési jogkivonat nem kötelező.
* Engedélyezési jogkivonattal: adjon meg egy engedélyezési jogkivonatot és a hozzá tartozó régiót.

> [!NOTE]
> Függetlenül attól, hogy elvégezte-e a beszédfelismerést, a beszédfelismerést, a fordítást vagy a szándék felismerését, mindig hozzon létre egy konfigurációt.

## <a name="recognize-from-microphone"></a>Felismerés mikrofonból

Ha az eszköz mikrofonját használva szeretné felismerni a beszédfelismerést, hozzon létre egy `AudioConfig` használatával `FromDefaultMicrophoneInput()` . Ezután inicializálja a t [`SpeechRecognizer`](/dotnet/api/microsoft.cognitiveservices.speech.speechrecognizer) , és adja át a `audioConfig` és a `speechConfig` .

```csharp
using System;
using System.IO;
using System.Threading.Tasks;
using Microsoft.CognitiveServices.Speech;
using Microsoft.CognitiveServices.Speech.Audio;

class Program 
{
    async static Task FromMic(SpeechConfig speechConfig)
    {
        using var audioConfig = AudioConfig.FromDefaultMicrophoneInput();
        using var recognizer = new SpeechRecognizer(speechConfig, audioConfig);

        Console.WriteLine("Speak into your microphone.");
        var result = await recognizer.RecognizeOnceAsync();
        Console.WriteLine($"RECOGNIZED: Text={result.Text}");
    }

    async static Task Main(string[] args)
    {
        var speechConfig = SpeechConfig.FromSubscription("<paste-your-subscription-key>", "<paste-your-region>");
        await FromMic(speechConfig);
    }
}
```

Ha egy *adott* hangbeviteli eszközt szeretne használni, meg kell adnia az eszköz azonosítóját a következőben: `AudioConfig` . Ismerje meg [, hogyan kérheti le az eszköz azonosítóját](../../../how-to-select-audio-input-devices.md) a hangbemeneti eszközhöz.

## <a name="recognize-from-file"></a>Felismerés fájlból

Ha mikrofon helyett hangfájlból szeretné felismerni a beszédet, továbbra is létre kell hoznia egy elemet `AudioConfig` . A [`AudioConfig`](/dotnet/api/microsoft.cognitiveservices.speech.audio.audioconfig) hívása helyett azonban a `FromDefaultMicrophoneInput()` `FromWavFileInput()` fájl elérési útját kell meghívnia és átadnia.

```csharp
using System;
using System.IO;
using System.Threading.Tasks;
using Microsoft.CognitiveServices.Speech;
using Microsoft.CognitiveServices.Speech.Audio;

class Program 
{
    async static Task FromFile(SpeechConfig speechConfig)
    {
        using var audioConfig = AudioConfig.FromWavFileInput("PathToFile.wav");
        using var recognizer = new SpeechRecognizer(speechConfig, audioConfig);

        var result = await recognizer.RecognizeOnceAsync();
        Console.WriteLine($"RECOGNIZED: Text={result.Text}");
    }

    async static Task Main(string[] args)
    {
        var speechConfig = SpeechConfig.FromSubscription("<paste-your-subscription-key>", "<paste-your-region>");
        await FromFile(speechConfig);
    }
}
```

## <a name="recognize-from-in-memory-stream"></a>Felismerés a memóriában lévő adatfolyamból

Sok felhasználási eset esetében valószínű, hogy a hangadatok a blob Storage-ból fognak származni, vagy más módon már a memóriában lévő `byte[]` vagy hasonló nyers adatstruktúraként vannak tárolva. Az alábbi példa a a [`PushAudioInputStream`](/dotnet/api/microsoft.cognitiveservices.speech.audio.pushaudioinputstream) használatával ismeri fel a beszédet, amely lényegében egy absztrakt memória. A mintakód a következő műveleteket végzi el:

* Nyers hangadatokat (PCM) ír a `PushAudioInputStream` függvény használatával `Write()` , amely fogadja a következőt: `byte[]` .
* Egy fájl beolvasása `.wav` `FileReader` bemutató céljára, de ha már rendelkezik hangadatokkal a-ben `byte[]` , kihagyhatja a tartalmat a bemeneti adatfolyamba.
* Az alapértelmezett formátum: 16 bites, 16khz monó PCM. A formátum testreszabásához átadhat egy [`AudioStreamFormat`](/dotnet/api/microsoft.cognitiveservices.speech.audio.audiostreamformat) objektumot `CreatePushStream()` a statikus függvény használatára `AudioStreamFormat.GetWaveFormatPCM(sampleRate, (byte)bitRate, (byte)channels)` .

```csharp
using System;
using System.IO;
using System.Threading.Tasks;
using Microsoft.CognitiveServices.Speech;
using Microsoft.CognitiveServices.Speech.Audio;

class Program 
{
    async static Task FromStream(SpeechConfig speechConfig)
    {
        var reader = new BinaryReader(File.OpenRead("PathToFile.wav"));
        using var audioInputStream = AudioInputStream.CreatePushStream();
        using var audioConfig = AudioConfig.FromStreamInput(audioInputStream);
        using var recognizer = new SpeechRecognizer(speechConfig, audioConfig);

        byte[] readBytes;
        do
        {
            readBytes = reader.ReadBytes(1024);
            audioInputStream.Write(readBytes, readBytes.Length);
        } while (readBytes.Length > 0);

        var result = await recognizer.RecognizeOnceAsync();
        Console.WriteLine($"RECOGNIZED: Text={result.Text}");
    }

    async static Task Main(string[] args)
    {
        var speechConfig = SpeechConfig.FromSubscription("<paste-your-subscription-key>", "<paste-your-region>");
        await FromStream(speechConfig);
    }
}
```

A leküldéses adatfolyamok bemenetként való használata azt feltételezi, hogy a hangadatok egy nyers PCM, például a fejlécek kihagyása.
Az API bizonyos esetekben továbbra is működni fog, ha a fejléc nem lett kihagyva, de a legjobb eredmény érdekében érdemes lehet a fejlécek olvasására kiolvasni a logikát, hogy az a `byte[]` *hangadatok kezdetekor* induljon el.

## <a name="error-handling"></a>Hibakezelés

Az előző példákban egyszerűen lekérheti a felismert szöveget `result.text` , de a hibák és más válaszok kezeléséhez meg kell írnia egy kódot az eredmény kezeléséhez. A következő kód kiértékeli a [`result.Reason`](/dotnet/api/microsoft.cognitiveservices.speech.recognitionresult.reason) tulajdonságot, és:

* Kinyomtatja az eredmények felismerésének eredményét: `ResultReason.RecognizedSpeech`
* Ha nincs felismerési egyezés, tájékoztassa a felhasználót: `ResultReason.NoMatch`
* Ha hiba történt, nyomtassa ki a hibaüzenetet: `ResultReason.Canceled`

```csharp
switch (result.Reason)
{
    case ResultReason.RecognizedSpeech:
        Console.WriteLine($"RECOGNIZED: Text={result.Text}");
        break;
    case ResultReason.NoMatch:
        Console.WriteLine($"NOMATCH: Speech could not be recognized.");
        break;
    case ResultReason.Canceled:
        var cancellation = CancellationDetails.FromResult(result);
        Console.WriteLine($"CANCELED: Reason={cancellation.Reason}");

        if (cancellation.Reason == CancellationReason.Error)
        {
            Console.WriteLine($"CANCELED: ErrorCode={cancellation.ErrorCode}");
            Console.WriteLine($"CANCELED: ErrorDetails={cancellation.ErrorDetails}");
            Console.WriteLine($"CANCELED: Did you update the subscription info?");
        }
        break;
}
```

## <a name="continuous-recognition"></a>Folyamatos felismerés

Az előző példákban a single-shot felismerést használjuk, amely egyetlen Kimondás. Az egyetlen kiírás végének meghatározása úgy történik, hogy a csendet figyeli a végén, vagy legfeljebb 15 másodperces hangot dolgoz fel.

Ezzel szemben a folyamatos felismerést akkor kell használni, ha meg szeretné **határozni** , hogy mikor kell leállítani a felismerést. `Recognizing` `Recognized` A felismerési eredmények beszerzéséhez elő kell fizetnie a, a és az `Canceled` eseményekre. Az felismerés leállításához hívja meg a következőt: [`StopContinuousRecognitionAsync`](/dotnet/api/microsoft.cognitiveservices.speech.speechrecognizer.stopcontinuousrecognitionasync) . Íme egy példa arra, hogyan történik a folyamatos felismerés egy hangbemeneti fájlon.

Először határozza meg a bemenetet, és inicializálja a [`SpeechRecognizer`](/dotnet/api/microsoft.cognitiveservices.speech.speechrecognizer) következőket:

```csharp
using var audioConfig = AudioConfig.FromWavFileInput("YourAudioFile.wav");
using var recognizer = new SpeechRecognizer(speechConfig, audioConfig);
```

Ezután hozzon létre egy `TaskCompletionSource<int>` -t a beszédfelismerés állapotának kezeléséhez.

```csharp
var stopRecognition = new TaskCompletionSource<int>();
```

Ezután fizessen elő a által elküldett eseményekre [`SpeechRecognizer`](/dotnet/api/microsoft.cognitiveservices.speech.speechrecognizer) .

* [`Recognizing`](/dotnet/api/microsoft.cognitiveservices.speech.speechrecognizer.recognizing): A köztes felismerési eredményeket tartalmazó események jelzése.
* [`Recognized`](/dotnet/api/microsoft.cognitiveservices.speech.speechrecognizer.recognized): A végső felismerési eredményeket tartalmazó események jelzése (sikeres felismerési kísérletet jelezve).
* [`SessionStopped`](/dotnet/api/microsoft.cognitiveservices.speech.recognizer.sessionstopped): Az elismerési munkamenet végét jelző események jelzése (művelet).
* [`Canceled`](/dotnet/api/microsoft.cognitiveservices.speech.speechrecognizer.canceled): A megszakított felismerési eredményeket tartalmazó események jelzése (olyan felismerési kísérletet jelez, amelyet a rendszer az eredmény vagy a közvetlen törlési kérelem miatt megszakított, vagy ha egy átviteli vagy protokollhiba történt).

```csharp
recognizer.Recognizing += (s, e) =>
{
    Console.WriteLine($"RECOGNIZING: Text={e.Result.Text}");
};

recognizer.Recognized += (s, e) =>
{
    if (e.Result.Reason == ResultReason.RecognizedSpeech)
    {
        Console.WriteLine($"RECOGNIZED: Text={e.Result.Text}");
    }
    else if (e.Result.Reason == ResultReason.NoMatch)
    {
        Console.WriteLine($"NOMATCH: Speech could not be recognized.");
    }
};

recognizer.Canceled += (s, e) =>
{
    Console.WriteLine($"CANCELED: Reason={e.Reason}");

    if (e.Reason == CancellationReason.Error)
    {
        Console.WriteLine($"CANCELED: ErrorCode={e.ErrorCode}");
        Console.WriteLine($"CANCELED: ErrorDetails={e.ErrorDetails}");
        Console.WriteLine($"CANCELED: Did you update the subscription info?");
    }

    stopRecognition.TrySetResult(0);
};

recognizer.SessionStopped += (s, e) =>
{
    Console.WriteLine("\n    Session stopped event.");
    stopRecognition.TrySetResult(0);
};
```

Az összes beállítása `StartContinuousRecognitionAsync` után hívja meg az felismerést.

```csharp
await recognizer.StartContinuousRecognitionAsync();

// Waits for completion. Use Task.WaitAny to keep the task rooted.
Task.WaitAny(new[] { stopRecognition.Task });

// make the following call at some point to stop recognition.
// await recognizer.StopContinuousRecognitionAsync();
```

### <a name="dictation-mode"></a>Diktálási mód

Folyamatos felismerés használatakor engedélyezheti a diktálás feldolgozását a megfelelő "diktálás engedélyezése" funkció használatával. Ez a mód azt eredményezi, hogy a beszédfelismerési konfigurációs példány értelmezi a mondatok (például a központozás) szövegének leírását. A "Do You Live in Town kérdőjel" kifejezés például "a városban él?" szöveget fogja értelmezni.

A diktálási mód engedélyezéséhez használja a [`EnableDictation`](/dotnet/api/microsoft.cognitiveservices.speech.speechconfig.enabledictation) metódust a alkalmazásban [`SpeechConfig`](/dotnet/api/microsoft.cognitiveservices.speech.speechconfig) .

```csharp
speechConfig.EnableDictation();
```

## <a name="change-source-language"></a>Forrás nyelvének módosítása

A beszédfelismerés általános feladata, hogy megadja a bemeneti (vagy a forrás) nyelvet. Vessünk egy pillantást arra, hogyan változtathatja meg a szövegbeviteli nyelvet az olasz nyelvre. A kódban keresse meg [`SpeechConfig`](/dotnet/api/microsoft.cognitiveservices.speech.speechconfig) , majd adja hozzá ezt a sort közvetlenül alatta.

```csharp
speechConfig.SpeechRecognitionLanguage = "it-IT";
```

A [`SpeechRecognitionLanguage`](/dotnet/api/microsoft.cognitiveservices.speech.speechconfig.speechrecognitionlanguage) tulajdonság nyelvi területi beállítású karakterláncot vár. A **területi beállítások** oszlopban bármilyen értéket megadhat a támogatott [területi beállítások/nyelvek](../../../language-support.md)listájában.

## <a name="improve-recognition-accuracy"></a>Az elismerés pontosságának javítása

A kifejezések listája a hangadatokban található ismert kifejezések azonosítására szolgál, például egy személy nevéhez vagy egy adott helyhez. A kifejezések listájának megadásával javíthatja a beszédfelismerés pontosságát.

Ha például a "Move to" parancs és a "Ward" lehetséges célpontja látható, akkor hozzáadhat egy "Áthelyezés a kórterembe" bejegyzést. Egy kifejezés hozzáadásával nő a valószínűsége annak, hogy ha a hang felismerése felismerte, hogy az "Áthelyezés a kórterembe" lehetőség fel lesz ismerve a "költözés felé" helyett.

Az egyes szavak vagy teljes kifejezések hozzáadhatók egy kifejezési listához. Az elismerés során a rendszer a mondatok listájában szereplő szavak és kifejezések felismerését fokozza, még akkor is, ha a bejegyzések a teljes szöveg közepén jelennek meg. 

> [!IMPORTANT]
> A kifejezések listázása funkció a következő nyelveken érhető el: en-US, de-DE, en-AU, en-CA, en-GB, es-ES, es-MX, fr-CA, fr-FR, IT-IT, ja-JP, ko-KR, PT-BR, zh-CN

A kifejezések listájának használatához először hozzon létre egy [`PhraseListGrammar`](/dotnet/api/microsoft.cognitiveservices.speech.phraselistgrammar) objektumot, majd adjon hozzá konkrét szavakat és kifejezéseket a következővel: [`AddPhrase`](/dotnet/api/microsoft.cognitiveservices.speech.phraselistgrammar.addphrase) .

A [`PhraseListGrammar`](/dotnet/api/microsoft.cognitiveservices.speech.phraselistgrammar) következő felismerés vagy a beszédfelismerési szolgáltatás újrakapcsolódása után a módosítások érvénybe lépnek.

```csharp
var phraseList = PhraseListGrammar.FromRecognizer(recognizer);
phraseList.AddPhrase("Supercalifragilisticexpialidocious");
```

Ha törölnie kell a kifejezések listáját: 

```csharp
phraseList.Clear();
```

### <a name="other-options-to-improve-recognition-accuracy"></a>Egyéb lehetőségek az elismerés pontosságának növeléséhez

A kifejezések listája csak egyetlen lehetőség az elismerés pontosságának javítására. További lehetőségek: 

* [Pontosság javítása Custom Speech segítségével](../../../custom-speech-overview.md)
* [Pontosság javítása bérlőmodellekkel](../../../tutorial-tenant-model.md)
