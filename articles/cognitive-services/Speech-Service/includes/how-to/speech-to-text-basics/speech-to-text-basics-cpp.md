---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/06/2020
ms.author: trbye
ms.openlocfilehash: 6240cdb184e0e226e5d407c8d24fed7395a285c2
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/30/2020
ms.locfileid: "93135629"
---
A beszédfelismerési szolgáltatás egyik fő funkciója az emberi beszéd (más néven beszéd – szöveg) felismerése és átírása. Ebből a rövid útmutatóból megtudhatja, hogyan használhatja a Speech SDK-t az alkalmazásaiban és termékeiben a kiváló minőségű beszéd-szöveg átalakítás elvégzéséhez.

## <a name="skip-to-samples-on-github"></a>Ugrás a mintákra a GitHubon

Ha közvetlenül a mintakód kihagyását szeretné kihagyni, tekintse meg a C++ gyors üzembe helyezési [mintákat](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/cpp/windows) a githubon.

## <a name="prerequisites"></a>Előfeltételek

Ez a cikk azt feltételezi, hogy rendelkezik Azure-fiókkal és Speech Service-előfizetéssel. Ha nem rendelkezik fiókkal és előfizetéssel, [próbálja ki ingyenesen a Speech szolgáltatást](../../../overview.md#try-the-speech-service-for-free).

## <a name="install-the-speech-sdk"></a>A Speech SDK telepítése

Mielőtt bármit elvégezhet, telepítenie kell a Speech SDK-t. A platformtól függően kövesse az alábbi utasításokat:

* <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/setup-platform?tabs=linux&pivots=programming-language-cpp" target="_blank">Linux <span class="docon docon-navigate-external x-hidden-focus"></span></a>
* <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/setup-platform?tabs=macos&pivots=programming-language-cpp" target="_blank">macOS <span class="docon docon-navigate-external x-hidden-focus"></span></a>
* <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/setup-platform?tabs=windows&pivots=programming-language-cpp" target="_blank">Windows <span class="docon docon-navigate-external x-hidden-focus"></span></a>

## <a name="create-a-speech-configuration"></a>Beszédfelismerési konfiguráció létrehozása

A beszédfelismerési szolgáltatás a Speech SDK használatával történő meghívásához létre kell hoznia egy [`SpeechConfig`](https://docs.microsoft.com/cpp/cognitive-services/speech/speechconfig) . Ez az osztály az előfizetésével kapcsolatos információkat tartalmaz, például a kulcsot és a társított régiót, végpontot, gazdagépet vagy engedélyezési jogkivonatot. Hozzon létre egy [`SpeechConfig`](https://docs.microsoft.com/cpp/cognitive-services/speech/speechconfig) -t a kulcs és a régió használatával. A régió azonosítójának megkereséséhez tekintse meg a [régiók támogatása](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#speech-sdk) lapot.

```cpp
using namespace std;
using namespace Microsoft::CognitiveServices::Speech;

auto config = SpeechConfig::FromSubscription("<paste-your-subscription-key>", "<paste-your-region>");
```

Néhány más módon is elvégezhető a következők inicializálása [`SpeechConfig`](https://docs.microsoft.com/cpp/cognitive-services/speech/speechconfig) :

* Egy végponttal: pass a Speech Service-végponton. Kulcs-vagy engedélyezési jogkivonat nem kötelező.
* Gazdagép esetén: adjon meg egy gazdagép-címeket. Kulcs-vagy engedélyezési jogkivonat nem kötelező.
* Engedélyezési jogkivonattal: adjon meg egy engedélyezési jogkivonatot és a hozzá tartozó régiót.

> [!NOTE]
> Függetlenül attól, hogy elvégezte-e a beszédfelismerést, a beszédfelismerést, a fordítást vagy a szándék felismerését, mindig hozzon létre egy konfigurációt.

## <a name="recognize-from-microphone"></a>Felismerés mikrofonból

Ha az eszköz mikrofonját használva szeretné felismerni a beszédfelismerést, hozzon létre egy `AudioConfig` használatával `FromDefaultMicrophoneInput()` . Ezután inicializálja a t [`SpeechRecognizer`](https://docs.microsoft.com/cpp/cognitive-services/speech/speechrecognizer) , és adja át a `audioConfig` és a `config` .

```cpp
using namespace Microsoft::CognitiveServices::Speech::Audio;

auto audioConfig = AudioConfig::FromDefaultMicrophoneInput();
auto recognizer = SpeechRecognizer::FromConfig(config, audioConfig);

cout << "Speak into your microphone." << std::endl;
auto result = recognizer->RecognizeOnceAsync().get();
cout << "RECOGNIZED: Text=" << result->Text << std::endl;
```

Ha egy *adott* hangbeviteli eszközt szeretne használni, meg kell adnia az eszköz azonosítóját a következőben: `AudioConfig` . Ismerje meg [, hogyan kérheti le az eszköz azonosítóját](../../../how-to-select-audio-input-devices.md) a hangbemeneti eszközhöz.

## <a name="recognize-from-file"></a>Felismerés fájlból

Ha mikrofon használata helyett hangfájlból szeretné felismerni a beszédet, akkor továbbra is létre kell hoznia egy elemet `AudioConfig` . A [`AudioConfig`](https://docs.microsoft.com/cpp/cognitive-services/speech/audio-audioconfig) hívása helyett azonban a `FromDefaultMicrophoneInput()` `FromWavFileInput()` fájl elérési útját kell meghívnia és átadnia.

```cpp
using namespace Microsoft::CognitiveServices::Speech::Audio;

auto audioInput = AudioConfig::FromWavFileInput("YourAudioFile.wav");
auto recognizer = SpeechRecognizer::FromConfig(config, audioInput);

auto result = recognizer->RecognizeOnceAsync().get();
cout << "RECOGNIZED: Text=" << result->Text << std::endl;
```

## <a name="recognize-speech"></a>Beszéd felismerése

A C++ Speech SDK [felismerési osztálya](https://docs.microsoft.com/cpp/cognitive-services/speech/speechrecognizer) a beszédfelismeréshez használható néhány módszert.

### <a name="single-shot-recognition"></a>Egyszeri felvétel felismerése

Az egyszeres felvételes felismerés aszinkron módon felismeri a teljes kilépést. Az egyetlen kiírás végének meghatározása úgy történik, hogy a csendet figyeli a végén, vagy legfeljebb 15 másodperces hangot dolgoz fel. Íme egy példa az aszinkron egyszeri felvételre a használatával [`RecognizeOnceAsync`](https://docs.microsoft.com/cpp/cognitive-services/speech/speechrecognizer#recognizeonceasync) :

```cpp
auto result = recognizer->RecognizeOnceAsync().get();
```

Az eredmény kezeléséhez meg kell írnia egy kódot. Ez a példa a következőket értékeli ki [`result->Reason`](https://docs.microsoft.com/cpp/cognitive-services/speech/recognitionresult#reason) :

* Kinyomtatja az eredmények felismerésének eredményét: `ResultReason::RecognizedSpeech`
* Ha nincs felismerési egyezés, tájékoztassa a felhasználót: `ResultReason::NoMatch`
* Ha hiba történt, nyomtassa ki a hibaüzenetet: `ResultReason::Canceled`

```cpp
switch (result->Reason)
{
    case ResultReason::RecognizedSpeech:
        cout << "We recognized: " << result->Text << std::endl;
        break;
    case ResultReason::NoMatch:
        cout << "NOMATCH: Speech could not be recognized." << std::endl;
        break;
    case ResultReason::Canceled:
        {
            auto cancellation = CancellationDetails::FromResult(result);
            cout << "CANCELED: Reason=" << (int)cancellation->Reason << std::endl;
    
            if (cancellation->Reason == CancellationReason::Error) {
                cout << "CANCELED: ErrorCode= " << (int)cancellation->ErrorCode << std::endl;
                cout << "CANCELED: ErrorDetails=" << cancellation->ErrorDetails << std::endl;
                cout << "CANCELED: Did you update the subscription info?" << std::endl;
            }
        }
        break;
    default:
        break;
}
```

### <a name="continuous-recognition"></a>Folyamatos felismerés

A folyamatos felismerés valamivel többet vesz igénybe, mint a single-shot felismerés. `Recognizing` `Recognized` A felismerési eredmények beszerzéséhez elő kell fizetnie a, a és az `Canceled` eseményekre. Az felismerés leállításához hívnia kell a [StopContinuousRecognitionAsync](https://docs.microsoft.com/cpp/cognitive-services/speech/speechrecognizer#stopcontinuousrecognitionasync). Íme egy példa arra, hogyan történik a folyamatos felismerés egy hangbemeneti fájlon.

Első lépésként definiáljuk a bemenetet, és inicializáljuk a [`SpeechRecognizer`](https://docs.microsoft.com/cpp/cognitive-services/speech/speechrecognizer) következőket:

```cpp
auto audioInput = AudioConfig::FromWavFileInput("YourAudioFile.wav");
auto recognizer = SpeechRecognizer::FromConfig(config, audioInput);
```

Ezután hozzon létre egy változót a beszédfelismerés állapotának kezeléséhez. A kezdéshez bejelentjük a `promise<void>` -t, mivel az elismerés megkezdése után nyugodtan feltételezhetjük, hogy nem fejeződött be.

```cpp
promise<void> recognitionEnd;
```

A szolgáltatásból érkező eseményekre fogunk előfizetni [`SpeechRecognizer`](https://docs.microsoft.com/cpp/cognitive-services/speech/speechrecognizer) .

* [`Recognizing`](https://docs.microsoft.com/cpp/cognitive-services/speech/asyncrecognizer#recognizing): A köztes felismerési eredményeket tartalmazó események jelzése.
* [`Recognized`](https://docs.microsoft.com/cpp/cognitive-services/speech/asyncrecognizer#recognized): A végső felismerési eredményeket tartalmazó események jelzése (sikeres felismerési kísérletet jelezve).
* [`SessionStopped`](https://docs.microsoft.com/cpp/cognitive-services/speech/asyncrecognizer#sessionstopped): Az elismerési munkamenet végét jelző események jelzése (művelet).
* [`Canceled`](https://docs.microsoft.com/cpp/cognitive-services/speech/asyncrecognizer#canceled): A megszakított felismerési eredményeket tartalmazó események jelzése (olyan felismerési kísérletet jelez, amelyet a rendszer az eredmény vagy a közvetlen törlési kérelem miatt megszakított, vagy ha egy átviteli vagy protokollhiba történt).

```cpp
recognizer->Recognizing.Connect([](const SpeechRecognitionEventArgs& e)
    {
        cout << "Recognizing:" << e.Result->Text << std::endl;
    });

recognizer->Recognized.Connect([](const SpeechRecognitionEventArgs& e)
    {
        if (e.Result->Reason == ResultReason::RecognizedSpeech)
        {
            cout << "RECOGNIZED: Text=" << e.Result->Text 
                 << " (text could not be translated)" << std::endl;
        }
        else if (e.Result->Reason == ResultReason::NoMatch)
        {
            cout << "NOMATCH: Speech could not be recognized." << std::endl;
        }
    });

recognizer->Canceled.Connect([&recognitionEnd](const SpeechRecognitionCanceledEventArgs& e)
    {
        cout << "CANCELED: Reason=" << (int)e.Reason << std::endl;
        if (e.Reason == CancellationReason::Error)
        {
            cout << "CANCELED: ErrorCode=" << (int)e.ErrorCode << "\n"
                 << "CANCELED: ErrorDetails=" << e.ErrorDetails << "\n"
                 << "CANCELED: Did you update the subscription info?" << std::endl;

            recognitionEnd.set_value(); // Notify to stop recognition.
        }
    });

recognizer->SessionStopped.Connect([&recognitionEnd](const SessionEventArgs& e)
    {
        cout << "Session stopped.";
        recognitionEnd.set_value(); // Notify to stop recognition.
    });
```

Minden beállítással meghívhatjuk [`StopContinuousRecognitionAsync`](https://docs.microsoft.com/cpp/cognitive-services/speech/speechrecognizer#startcontinuousrecognitionasync) .

```cpp
// Starts continuous recognition. Uses StopContinuousRecognitionAsync() to stop recognition.
recognizer->StartContinuousRecognitionAsync().get();

// Waits for recognition end.
recognitionEnd.get_future().get();

// Stops recognition.
recognizer->StopContinuousRecognitionAsync().get();
```

### <a name="dictation-mode"></a>Diktálási mód

Folyamatos felismerés használatakor engedélyezheti a diktálás feldolgozását a megfelelő "diktálás engedélyezése" funkció használatával. Ez a mód azt eredményezi, hogy a beszédfelismerési konfigurációs példány értelmezi a mondatok (például a központozás) szövegének leírását. A "Do You Live in Town kérdőjel" kifejezés például "a városban él?" szöveget fogja értelmezni.

A diktálási mód engedélyezéséhez használja a [`EnableDictation`](https://docs.microsoft.com/cpp/cognitive-services/speech/speechconfig#enabledictation) metódust a alkalmazásban [`SpeechConfig`](https://docs.microsoft.com/cpp/cognitive-services/speech/speechconfig) .

```cpp
config->EnableDictation();
```

## <a name="change-source-language"></a>Forrás nyelvének módosítása

A beszédfelismerés általános feladata, hogy megadja a bemeneti (vagy a forrás) nyelvet. Vessünk egy pillantást arra, hogyan változtathatja meg a szövegbeviteli nyelvet németre. A kódban keresse meg [`SpeechConfig`](https://docs.microsoft.com/cpp/cognitive-services/speech/speechconfig) , majd adja hozzá ezt a sort közvetlenül alatta.

```cpp
config->SetSpeechRecognitionLanguage("de-DE");
```

[`SetSpeechRecognitionLanguage`](https://docs.microsoft.com/cpp/cognitive-services/speech/speechconfig#setspeechrecognitionlanguage) egy paraméter, amely argumentumként karakterláncot vesz fel. A támogatott [területi beállítások/nyelvek](../../../language-support.md)listájában bármilyen értéket megadhat.

## <a name="improve-recognition-accuracy"></a>Az elismerés pontosságának javítása

A Speech SDK-val többféleképpen javítható az elismerés pontossága. Vessünk egy pillantást a kifejezések listájára. A kifejezések listája a hangadatokban található ismert kifejezések azonosítására szolgál, például egy személy nevéhez vagy egy adott helyhez. Az egyes szavak vagy teljes kifejezések hozzáadhatók egy kifejezési listához. Az elismerés során a rendszer a kifejezések listájában szereplő bejegyzést használja, ha a teljes kifejezés pontos egyezése szerepel a hangban. Ha a kifejezés pontos egyezése nem található, az elismerés nem támogatott.

> [!IMPORTANT]
> A kifejezés lista szolgáltatás csak angol nyelven érhető el.

A kifejezések listájának használatához először hozzon létre egy [`PhraseListGrammar`](https://docs.microsoft.com/cpp/cognitive-services/speech/phraselistgrammar) objektumot, majd adjon hozzá konkrét szavakat és kifejezéseket a következővel: [`AddPhrase`](https://docs.microsoft.com/cpp/cognitive-services/speech/phraselistgrammar#addphrase) .

A [`PhraseListGrammar`](https://docs.microsoft.com/cpp/cognitive-services/speech/phraselistgrammar) következő felismerés vagy a beszédfelismerési szolgáltatás újrakapcsolódása után a módosítások érvénybe lépnek.

```cpp
auto phraseListGrammar = PhraseListGrammar::FromRecognizer(recognizer);
phraseListGrammar->AddPhrase("Supercalifragilisticexpialidocious");
```

Ha törölnie kell a kifejezések listáját: 

```cpp
phraseListGrammar->Clear();
```

### <a name="other-options-to-improve-recognition-accuracy"></a>Egyéb lehetőségek az elismerés pontosságának növeléséhez

A kifejezések listája csak egyetlen lehetőség az elismerés pontosságának javítására. További lehetőségek: 

* [Pontosság javítása Custom Speech segítségével](../../../how-to-custom-speech.md)
* [Pontosság javítása bérlőmodellekkel](../../../tutorial-tenant-model.md)
