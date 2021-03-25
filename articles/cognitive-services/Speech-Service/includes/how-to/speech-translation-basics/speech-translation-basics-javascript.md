---
author: v-demjoh
ms.service: cognitive-services
ms.topic: include
ms.date: 07/14/2020
ms.author: v-demjoh
ms.custom: devx-track-js
ms.openlocfilehash: b43c0da3303b4cdfd4941f9d76b663f8089a1417
ms.sourcegitcommit: ed7376d919a66edcba3566efdee4bc3351c57eda
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2021
ms.locfileid: "105104152"
---
A Speech Service egyik fő funkciója az emberi beszéd felismerése és más nyelvekre való lefordítása. Ebből a rövid útmutatóból megtudhatja, hogyan használhatja a Speech SDK-t az alkalmazásaiban és termékeiben, hogy kiváló minőségű hangfordítást végezzen. Ez a rövid útmutató a következő témaköröket tartalmazza:

* Beszéd – szöveg fordítása
* Beszéd lefordítása több célként megadott nyelvre
* Közvetlen beszéd – beszéd fordítás végrehajtása

## <a name="skip-to-samples-on-github"></a>Ugrás a mintákra a GitHubon

Ha közvetlenül a mintakód kihagyását szeretné kihagyni, tekintse meg a JavaScript gyors üzembe helyezési [mintákat](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/javascript/browser/translate-speech-to-text) a githubon.

## <a name="prerequisites"></a>Előfeltételek

Ez a cikk azt feltételezi, hogy rendelkezik Azure-fiókkal és Speech Service-előfizetéssel. Ha nem rendelkezik fiókkal és előfizetéssel, [próbálja ki ingyenesen a Speech szolgáltatást](../../../overview.md#try-the-speech-service-for-free).

## <a name="install-the-speech-sdk"></a>A Speech SDK telepítése

Mielőtt bármit elvégezhet, telepítenie kell a <a href="https://www.npmjs.com/package/microsoft-cognitiveservices-speech-sdk" target="_blank">javascripthez készült SPEECH SDK </a>-t. A platformtól függően kövesse az alábbi utasításokat:
- <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk?tabs=nodejs#get-the-speech-sdk" target="_blank">Node.js <span 
class="docon docon-navigate-external x-hidden-focus"></span></a>
- <a href="/azure/cognitive-services/speech-service/speech-sdk?tabs=browser#get-the-speech-sdk" target="_blank">Webböngésző </a>

Emellett a cél környezettől függően a következők egyikét használja:

# <a name="script"></a>[parancsfájl](#tab/script)

Töltse le és csomagolja ki a JavaScript *microsoft.cognitiveservices.speech.sdk.bundle.js* -fájlhoz készült <a href="https://aka.ms/csspeech/jsbrowserpackage" target="_blank">Speech SDK</a> -t, és helyezze el a HTML-fájl számára elérhető mappába.

```html
<script src="microsoft.cognitiveservices.speech.sdk.bundle.js"></script>;
```

> [!TIP]
> Ha webböngészőt céloz meg, és a `<script>` címkét használja, az `sdk` előtag nem szükséges. Az `sdk` előtag a modul elnevezésére szolgáló alias `require` .

# <a name="import"></a>[importálása](#tab/import)

```javascript
import * from "microsoft-cognitiveservices-speech-sdk";
```

További információ `import` : <a href="https://javascript.info/import-export" target="_blank">Exportálás és importálás </a>.

# <a name="require"></a>[igényel](#tab/require)

```javascript
const sdk = require("microsoft-cognitiveservices-speech-sdk");
```

További információ `require` : <a href="https://nodejs.org/en/knowledge/getting-started/what-is-require/" target="_blank">Mi a szükséges? </a>.

---

## <a name="create-a-translation-configuration"></a>Fordítási konfiguráció létrehozása

A fordítási szolgáltatás a Speech SDK használatával történő meghívásához létre kell hoznia egy [`SpeechTranslationConfig`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechtranslationconfig) . Ez az osztály az előfizetésével kapcsolatos információkat tartalmaz, például a kulcsot és a társított régiót, végpontot, gazdagépet vagy engedélyezési jogkivonatot.

> [!NOTE]
> Függetlenül attól, hogy elvégezte-e a beszédfelismerést, a beszédfelismerést, a fordítást vagy a szándék felismerését, mindig hozzon létre egy konfigurációt.
Az alábbiakat többféleképpen lehet inicializálni [`SpeechTranslationConfig`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechtranslationconfig) :

* Előfizetéssel: adjon egy kulcsot és a hozzá tartozó régiót.
* Egy végponttal: pass a Speech Service-végponton. Kulcs-vagy engedélyezési jogkivonat nem kötelező.
* Gazdagép esetén: adjon meg egy gazdagép-címeket. Kulcs-vagy engedélyezési jogkivonat nem kötelező.
* Engedélyezési jogkivonattal: adjon meg egy engedélyezési jogkivonatot és a hozzá tartozó régiót.

Nézzük meg [`SpeechTranslationConfig`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechtranslationconfig) , hogyan jön létre egy kulcs és régió használatával. Szerezze be ezeket a hitelesítő adatokat a [beszédfelismerési szolgáltatás ingyenes kipróbálásához](../../../overview.md#try-the-speech-service-for-free)szükséges lépések követésével.

```javascript
const speechTranslationConfig = SpeechTranslationConfig.fromSubscription("YourSubscriptionKey", "YourServiceRegion");
```

## <a name="initialize-a-translator"></a>Fordító inicializálása

Miután létrehozta a [`SpeechTranslationConfig`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechtranslationconfig) -t, a következő lépés a inicializálása [`TranslationRecognizer`](/javascript/api/microsoft-cognitiveservices-speech-sdk/translationrecognizer) . A inicializálásakor [`TranslationRecognizer`](/javascript/api/microsoft-cognitiveservices-speech-sdk/translationrecognizer) át kell adnia a következőt: `speechTranslationConfig` . Ez biztosítja azokat a hitelesítő adatokat, amelyeket a fordítási szolgáltatás igényel a kérelem érvényesítéséhez.

Ha az eszköz alapértelmezett mikrofonján keresztül fordítja le a beszédet, a következőképpen [`TranslationRecognizer`](/javascript/api/microsoft-cognitiveservices-speech-sdk/translationrecognizer) kell kinéznie:

```javascript
const translator = new TranslationRecognizer(speechTranslationConfig);
```

Ha meg szeretné adni a hangbemeneti eszközt, létre kell hoznia egy-t, és meg kell [`AudioConfig`](/javascript/api/microsoft-cognitiveservices-speech-sdk/audioconfig) adnia a `audioConfig` paramétert a inicializálásakor [`TranslationRecognizer`](/javascript/api/microsoft-cognitiveservices-speech-sdk/translationrecognizer) .

> [!TIP]
> [Ismerje meg, hogyan kérheti le az eszköz azonosítóját a hangbemeneti eszközhöz](../../../how-to-select-audio-input-devices.md).
Az `AudioConfig` objektumra a következőképpen hivatkozhat:

```javascript
const audioConfig = AudioConfig.fromDefaultMicrophoneInput();
const recognizer = new TranslationRecognizer(speechTranslationConfig, audioConfig);
```

Ha mikrofon használata helyett hangfájlt szeretne biztosítani, akkor továbbra is meg kell adnia a következőt: `audioConfig` . Ez azonban csak akkor végezhető el, ha a **Node.js** célozza meg, és a [`AudioConfig`](/javascript/api/microsoft-cognitiveservices-speech-sdk/audioconfig) hívása helyett `fromDefaultMicrophoneInput` `fromWavFileOutput` a paramétert hívja meg és adja át `filename` .

```javascript
const audioConfig = AudioConfig.fromWavFileInput("YourAudioFile.wav");
const recognizer = new TranslationRecognizer(speechTranslationConfig, audioConfig);
```

## <a name="translate-speech"></a>Beszéd fordítása

A JavaScripthez készült Speech SDK [TranslationRecognizer osztálya](/javascript/api/microsoft-cognitiveservices-speech-sdk/translationrecognizer) néhány módszert tesz elérhetővé, amelyeket a beszédfelismeréshez használhat.

* Egylépéses fordítás (aszinkron) – a fordítást nem blokkoló (aszinkron) módban hajtja végre. Ez egyetlen Kimondás lefordítását eredményezi. Az egyetlen kiírás végének meghatározása úgy történik, hogy a csendet figyeli a végén, vagy legfeljebb 15 másodperces hangot dolgoz fel.
* Folyamatos fordítás (aszinkron) – aszinkron módon kezdeményezi a folyamatos fordítási műveletet. A felhasználó regisztrálja az eseményeket, és kezeli a különböző alkalmazási állapotokat. A folyamatos aszinkron fordítás leállításához hívja meg a következőt: [`stopContinuousRecognitionAsync`](/javascript/api/microsoft-cognitiveservices-speech-sdk/translationrecognizer#stopcontinuousrecognitionasync) .

> [!NOTE]
> További információ a [beszédfelismerési mód kiválasztásáról](../../../get-started-speech-to-text.md).
### <a name="specify-a-target-language"></a>Cél nyelvének meghatározása

A fordításhoz a forrás nyelvét és legalább egy célnyelv is meg kell adni.
A [hangfordítási táblázatban](../../../language-support.md#speech-translation)felsorolt területi beállításokkal kiválaszthatja a forrás nyelvét. Megkeresheti a lefordított nyelv beállításait ugyanazon a hivatkozáson. A célnyelv lehetőségei eltérőek lehetnek, ha szöveget szeretne megtekinteni, vagy a szintetizált fordítási beszédet szeretné hallani. Az Angolról németre való fordításhoz módosítsa a fordítási konfigurációs objektumot:

```javascript
speechTranslationConfig.speechRecognitionLanguage = "en-US";
speechTranslationConfig.addTargetLanguage("de");
```

### <a name="single-shot-recognition"></a>Egyszeri felvétel felismerése

Íme egy példa az aszinkron egyszeri felvételre a használatával [`recognizeOnceAsync`](/javascript/api/microsoft-cognitiveservices-speech-sdk/translationrecognizer#recognizeonceasync) :

```javascript
recognizer.recognizeOnceAsync(result => {
    // Interact with result
});
```

Az eredmény kezeléséhez meg kell írnia egy kódot. Ez a példa a [`result.reason`](/javascript/api/microsoft-cognitiveservices-speech-sdk/translationrecognitionresult) német nyelvű fordítást értékeli ki:

```javascript
recognizer.recognizeOnceAsync(
  function (result) {
    let translation = result.translations.get("de");
    window.console.log(translation);
    recognizer.close();
  },
  function (err) {
    window.console.log(err);
    recognizer.close();
});
```

A kód a fordítás feldolgozásakor megadott frissítéseket is képes kezelni.
Ezekkel a frissítésekkel vizuális visszajelzést adhat a fordítási folyamatról.
Tekintse meg [ezt a JavaScript Node.js példát](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/js/node/translation.js) a fordítási folyamat során megjelenő frissítéseket bemutató mintakód számára. A következő kód a fordítási folyamat során létrehozott részleteket is megjeleníti.

```javascript
recognizer.recognizing = function (s, e) {
    var str = ("(recognizing) Reason: " + SpeechSDK.ResultReason[e.result.reason] +
            " Text: " +  e.result.text +
            " Translation:");
    str += e.result.translations.get("de");
    console.log(str);
};
recognizer.recognized = function (s, e) {
    var str = "\r\n(recognized)  Reason: " + SpeechSDK.ResultReason[e.result.reason] +
            " Text: " + e.result.text +
            " Translation:";
    str += e.result.translations.get("de");
    str += "\r\n";
    console.log(str);
};
```

### <a name="continuous-translation"></a>Folyamatos fordítás

A folyamatos fordítás valamivel többet vesz igénybe, mint az egyszeres felvétel felismerése. `recognizing` `recognized` A felismerési eredmények beszerzéséhez elő kell fizetnie a, a és az `canceled` eseményekre. A fordítás leállításához hívnia kell a következőt: [`stopContinuousRecognitionAsync`](/javascript/api/microsoft-cognitiveservices-speech-sdk/translationrecognizer#stopcontinuousrecognitionasync) . Íme egy példa arra, hogyan történik a folyamatos fordítás egy hangbemeneti fájlon.

Első lépésként definiáljuk a bemenetet, és inicializáljuk a [`TranslationRecognizer`](/javascript/api/microsoft-cognitiveservices-speech-sdk/translationrecognizer) következőket:

```javascript
const translator = new TranslationRecognizer(speechTranslationConfig);
```

A szolgáltatásból érkező eseményekre fogunk előfizetni [`TranslationRecognizer`](/javascript/api/microsoft-cognitiveservices-speech-sdk/translationrecognizer) .

* [`recognizing`](/javascript/api/microsoft-cognitiveservices-speech-sdk/translationrecognizer#recognizing): A közbenső fordítás eredményeit tartalmazó események jelzése.
* [`recognized`](/javascript/api/microsoft-cognitiveservices-speech-sdk/translationrecognizer#recognized): A végső fordítási eredményeket tartalmazó események jelzése (sikeres fordítási kísérletet jelezve).
* [`sessionStopped`](/javascript/api/microsoft-cognitiveservices-speech-sdk/translationrecognizer#sessionstopped): A fordítási munkamenet (művelet) végét jelző események jelzése.
* [`canceled`](/javascript/api/microsoft-cognitiveservices-speech-sdk/translationrecognizer#canceled): A megszakított fordítási eredményeket tartalmazó események jelzése (olyan fordítási kísérletet jelez, amelyet a rendszer az eredmény vagy a közvetlen törlési kérelem miatt megszakított, vagy egy átviteli vagy protokollhiba miatt megszakadt).

```javascript
recognizer.recognizing = (s, e) => {
    console.log(`TRANSLATING: Text=${e.result.text}`);
};
recognizer.recognized = (s, e) => {
    if (e.result.reason == ResultReason.RecognizedSpeech) {
        console.log(`TRANSLATED: Text=${e.result.text}`);
    }
    else if (e.result.reason == ResultReason.NoMatch) {
        console.log("NOMATCH: Speech could not be translated.");
    }
};
recognizer.canceled = (s, e) => {
    console.log(`CANCELED: Reason=${e.reason}`);
    if (e.reason == CancellationReason.Error) {
        console.log(`"CANCELED: ErrorCode=${e.errorCode}`);
        console.log(`"CANCELED: ErrorDetails=${e.errorDetails}`);
        console.log("CANCELED: Did you update the subscription info?");
    }
    recognizer.stopContinuousRecognitionAsync();
};
recognizer.sessionStopped = (s, e) => {
    console.log("\n    Session stopped event.");
    recognizer.stopContinuousRecognitionAsync();
};
```

Minden beállítással meghívhatjuk [`startContinuousRecognitionAsync`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer#startcontinuousrecognitionasync) .

```javascript
// Starts continuous recognition. Uses stopContinuousRecognitionAsync() to stop recognition.
recognizer.startContinuousRecognitionAsync();
// Something later can call, stops recognition.
// recognizer.StopContinuousRecognitionAsync();
```

## <a name="choose-a-source-language"></a>Forrás nyelvének kiválasztása

A beszéd fordításának gyakori feladata a bemeneti (vagy a forrás) nyelv megadása. Vessünk egy pillantást arra, hogyan változtathatja meg a szövegbeviteli nyelvet az olasz nyelvre. A kódban keresse meg [`SpeechTranslationConfig`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechtranslationconfig) , majd adja hozzá a következő sort közvetlenül alatta.

```javascript
speechTranslationConfig.speechRecognitionLanguage = "it-IT";
```

A [`speechRecognitionLanguage`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechtranslationconfig#speechrecognitionlanguage) tulajdonság nyelvi területi beállítású karakterláncot vár. A **területi beállítások** oszlopban bármilyen értéket megadhat a támogatott [területi beállítások/nyelvek](../../../language-support.md)listájában.

## <a name="choose-one-or-more-target-languages"></a>Válasszon ki egy vagy több megcélzott nyelvet

A beszédfelismerési SDK párhuzamosan több cél nyelvre is fordítható. A rendelkezésre álló célnyelv némileg eltérnek a forrás nyelvének listájától, és nem területi beállítás, hanem nyelvi kód használatával határozzák meg a célként megadott nyelveket.
Tekintse meg a szöveges célokhoz tartozó nyelvi kódok listáját a [nyelvi támogatás oldalon található Speech Translation táblában](../../../language-support.md#speech-translation). Itt megtalálhatja a szintetizált nyelvekre való fordítás részleteit is.

A következő kód a német nyelvet adja meg célként:

```javascript
translationConfig.addTargetLanguage("de");
```

Mivel a több cél nyelvi fordítás is lehetséges, a kódnak meg kell adnia a célként megadott nyelvet az eredmény vizsgálatakor. A következő kód a német fordítási eredményeket jeleníti meg.

```javascript
recognizer.recognized = function (s, e) {
    var str = "\r\n(recognized)  Reason: " +
            sdk.ResultReason[e.result.reason] +
            " Text: " + e.result.text + " Translations:";
    var language = "de";
    str += " [" + language + "] " + e.result.translations.get(language);
    str += "\r\n";
    // show str somewhere
};
```