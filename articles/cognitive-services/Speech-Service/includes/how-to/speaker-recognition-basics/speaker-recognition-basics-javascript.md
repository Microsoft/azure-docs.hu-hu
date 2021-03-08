---
author: v-jaswel
ms.service: cognitive-services
ms.topic: include
ms.date: 10/07/2020
ms.author: v-jawe
ms.custom: references_regions
ms.openlocfilehash: 81186e6cf49e5f7e76a938881441cafa99d178eb
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/07/2021
ms.locfileid: "102445715"
---
Ebben a rövid útmutatóban megtudhatja, milyen alapszintű tervezési mintákat Speaker Recognition a Speech SDK használatával, beleértve a következőket:

* Szövegtől függő és szöveg-független ellenőrzés
* Beszélő azonosítása a hangminták azonosításához a hangminta
* Hangprofilok törlése

A beszédfelismeréssel kapcsolatos fogalmak [áttekintését az áttekintő](../../../speaker-recognition-overview.md) cikkben tekintheti meg.

## <a name="skip-to-samples-on-github"></a>Ugrás a mintákra a GitHubon

Ha közvetlenül a mintakód kihagyását szeretné kihagyni, tekintse meg a JavaScript gyors üzembe helyezési [mintákat](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/fa6428a0837779cbeae172688e0286625e340942/quickstart/javascript/node/speaker-recognition) a githubon.

## <a name="prerequisites"></a>Előfeltételek

Ez a cikk azt feltételezi, hogy rendelkezik Azure-fiókkal és Speech Service-előfizetéssel. Ha nem rendelkezik fiókkal és előfizetéssel, [próbálja ki ingyenesen a Speech szolgáltatást](../../../overview.md#try-the-speech-service-for-free).

> [!IMPORTANT]
> A Speaker Recognition jelenleg *csak* a régióban létrehozott Azure Speech-erőforrások esetében támogatott `westus` .

## <a name="install-the-speech-sdk"></a>A Speech SDK telepítése

Mielőtt bármit elvégezhet, telepítenie kell a <a href="https://www.npmjs.com/package/microsoft-cognitiveservices-speech-sdk" target="_blank">javascripthez készült SPEECH SDK </a>-t. A platformtól függően kövesse az alábbi utasításokat:

- <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk?tabs=nodejs#get-the-speech-sdk" target="_blank">Node.js <span 
class="docon docon-navigate-external x-hidden-focus"></span></a>
- <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk?tabs=browser#get-the-speech-sdk" target="_blank">Webböngésző </a>

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

## <a name="import-dependencies"></a>Függőségek importálása

A cikkben szereplő példák futtatásához adja hozzá a következő utasításokat a. js fájl elejéhez.

:::code language="javascript" source="~/cognitive-services-quickstart-code/javascript/speech/speaker-recognition.js" id="dependencies":::

Ezek az utasítások a szükséges kódtárak importálásával és a beszédfelismerési szolgáltatás előfizetési kulcsának és régiójának beszerzésére szolgálnak a környezeti változók alapján. A következő feladatokban használt hangfájlok elérési útját is megadják.

## <a name="create-helper-function"></a>Segítő függvény létrehozása

Adja hozzá a következő Helper függvényt a hangfájlok adatfolyamként való olvasásához a Speech Service számára.

:::code language="javascript" source="~/cognitive-services-quickstart-code/javascript/speech/speaker-recognition.js" id="helpers":::

Ebben a függvényben a [AudioInputStream. createPushStream](/javascript/api/microsoft-cognitiveservices-speech-sdk/audioinputstream#createpushstream-audiostreamformat-) és a [AudioConfig. fromStreamInput](/javascript/api/microsoft-cognitiveservices-speech-sdk/audioconfig#fromstreaminput-audioinputstream---pullaudioinputstreamcallback-) metódus használatával hozhat létre [AudioConfig](/javascript/api/microsoft-cognitiveservices-speech-sdk/audioconfig) objektumot. Ez az `AudioConfig` objektum egy hangadatfolyamot képvisel. `AudioConfig`A következő feladatok során több objektumot fog használni.

## <a name="text-dependent-verification"></a>Szövegtől függő ellenőrzés

Speaker Verification az a szabály, amely megerősíti, hogy egy beszélő ismert vagy **regisztrált** hangra illeszkedik. Az első **lépés a** hangprofil beléptetése, hogy a szolgáltatásnak van-e összehasonlítani a jövőbeli hangmintákkal. Ebben a példában egy **szövegtől függő** stratégia használatával regisztrálja a profilt, amelyhez a regisztrációhoz és ellenőrzéshez egy adott jelszó szükséges. A támogatott hozzáférési kódok listáját a [dokumentációs](/rest/api/speakerrecognition/) dokumentációban tekintheti meg.

### <a name="textdependentverification-function"></a>TextDependentVerification függvény

Először hozza létre a `TextDependentVerification` függvényt.

:::code language="javascript" source="~/cognitive-services-quickstart-code/javascript/speech/speaker-recognition.js" id="text_dependent_verification":::

Ez a függvény létrehoz egy [VoiceProfile](/javascript/api/microsoft-cognitiveservices-speech-sdk/voiceprofile) objektumot a [VoiceProfileClient. createProfileAsync](/javascript/api/microsoft-cognitiveservices-speech-sdk/voiceprofileclient#createprofileasync-voiceprofiletype--string---e--voiceprofile-----void---e--string-----void-) metódussal. Vegye figyelembe, hogy háromféle [típus](/javascript/api/microsoft-cognitiveservices-speech-sdk/voiceprofiletype) létezik `VoiceProfile` :

- TextIndependentIdentification
- TextDependentVerification
- TextIndependentVerification

Ebben az esetben a következőnek kell megfelelnie: `VoiceProfileType.TextDependentVerification` `VoiceProfileClient.createProfileAsync` .

Ezután hívja meg a következő két segítő függvényt, amelyeket meg fog határozni `AddEnrollmentsToTextDependentProfile` `SpeakerVerify` . Végül hívja meg a [VoiceProfileClient. deleteProfileAsync](/javascript/api/microsoft-cognitiveservices-speech-sdk/voiceprofileclient#deleteprofileasync-voiceprofile---response--voiceprofileresult-----void---e--string-----void-) parancsot a profil eltávolításához.

### <a name="addenrollmentstotextdependentprofile-function"></a>AddEnrollmentsToTextDependentProfile függvény

Adja meg a következő függvényt a hangprofil regisztrálásához.

:::code language="javascript" source="~/cognitive-services-quickstart-code/javascript/speech/speaker-recognition.js" id="add_enrollments_dependent":::

Ebben a függvényben meghívja a `GetAudioConfigFromFile` korábban definiált függvényt, hogy objektumokat hozzon létre `AudioConfig` a hangmintákból. Ezek a hangminták olyan hozzáférési kódot tartalmaznak, mint például a "saját hangom My Passport, ellenőrzés". Ezután regisztrálja ezeket a hangmintákat a [VoiceProfileClient. enrollProfileAsync](/javascript/api/microsoft-cognitiveservices-speech-sdk/voiceprofileclient#enrollprofileasync-voiceprofile--audioconfig---e--voiceprofileenrollmentresult-----void---e--string-----void-) metódus használatával.

### <a name="speakerverify-function"></a>SpeakerVerify függvény

Adja meg `SpeakerVerify` a következőt:

:::code language="javascript" source="~/cognitive-services-quickstart-code/javascript/speech/speaker-recognition.js" id="speaker_verify":::

Ebben a függvényben létrehoz egy [SpeakerVerificationModel](/javascript/api/microsoft-cognitiveservices-speech-sdk/speakerverificationmodel) objektumot a [SpeakerVerificationModel. FromProfile](/javascript/api/microsoft-cognitiveservices-speech-sdk/speakerverificationmodel#fromprofile-voiceprofile-) metódussal, amely a korábban létrehozott [VoiceProfile](/javascript/api/microsoft-cognitiveservices-speech-sdk/voiceprofile) objektumot adja meg.

Ezután hívja meg a [SpeechRecognizer. recognizeOnceAsync](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer#recognizeonceasync--e--speechrecognitionresult-----void---e--string-----void-) metódust egy olyan hangminta érvényesítéséhez, amely ugyanazt a hozzáférési kódot tartalmazza, mint a korábban regisztrált hangminták. `SpeechRecognizer.recognizeOnceAsync` egy [SpeakerRecognitionResult](/javascript/api/microsoft-cognitiveservices-speech-sdk/speakerrecognitionresult) objektumot ad vissza, amelynek `score` a tulajdonsága egy, a 0,0-1.0-tól származó hasonlósági pontszámot tartalmaz. Az `SpeakerRecognitionResult` objektum egy `reason` [ResultReason](/javascript/api/microsoft-cognitiveservices-speech-sdk/resultreason)típusú tulajdonságot is tartalmaz. Ha az ellenőrzés sikeres volt, a `reason` tulajdonságnak értékkel kell rendelkeznie `RecognizedSpeaker` .

## <a name="text-independent-verification"></a>Szövegtől független ellenőrzés

A **szövegtől függő** hitelesítéssel szemben a **szövegtől független** ellenőrzés:

* Nincs szükség egy bizonyos jelszó megírására, amit mondanak
* A nem igényel három hangmintát, *de a* teljes hang 20 másodpercet igényel

### <a name="textindependentverification-function"></a>TextIndependentVerification függvény

Először hozza létre a `TextIndependentVerification` függvényt.

:::code language="javascript" source="~/cognitive-services-quickstart-code/javascript/speech/speaker-recognition.js" id="text_independent_verification":::

A `TextDependentVerification` függvényhez hasonlóan ez a függvény létrehoz egy [VoiceProfile](/javascript/api/microsoft-cognitiveservices-speech-sdk/voiceprofile) objektumot a [VoiceProfileClient. createProfileAsync](/javascript/api/microsoft-cognitiveservices-speech-sdk/voiceprofileclient#createprofileasync-voiceprofiletype--string---e--voiceprofile-----void---e--string-----void-) metódussal.

Ebben az esetben a következőnek kell megfelelnie: `VoiceProfileType.TextIndependentVerification` `createProfileAsync` .

Ezután hívja meg a következő két segítő függvényt: `AddEnrollmentsToTextIndependentProfile` , amelyet a Next (tovább) és a (z `SpeakerVerify` ) már definiált. Végül hívja meg a [VoiceProfileClient. deleteProfileAsync](/javascript/api/microsoft-cognitiveservices-speech-sdk/voiceprofileclient#deleteprofileasync-voiceprofile---response--voiceprofileresult-----void---e--string-----void-) parancsot a profil eltávolításához.

### <a name="addenrollmentstotextindependentprofile"></a>AddEnrollmentsToTextIndependentProfile

Adja meg a következő függvényt a hangprofil regisztrálásához.

:::code language="javascript" source="~/cognitive-services-quickstart-code/javascript/speech/speaker-recognition.js" id="add_enrollments_independent":::

Ebben a függvényben meghívja a `GetAudioConfigFromFile` korábban definiált függvényt, hogy objektumokat hozzon létre `AudioConfig` a hangmintákból. Ezután regisztrálja ezeket a hangmintákat a [VoiceProfileClient. enrollProfileAsync](/javascript/api/microsoft-cognitiveservices-speech-sdk/voiceprofileclient#enrollprofileasync-voiceprofile--audioconfig---e--voiceprofileenrollmentresult-----void---e--string-----void-) metódus használatával.

## <a name="speaker-identification"></a>Beszélőazonosítás

A Speaker Identification segítségével megállapítható, hogy **ki** beszél a regisztrált hangok adott csoportjából. A folyamat hasonló a **szövegtől független ellenőrzéshez**, és a fő különbség, hogy egyszerre több hangprofillal is képes ellenőrizni, nem pedig egyetlen profilon.

### <a name="textindependentidentification-function"></a>TextIndependentIdentification függvény

Először hozza létre a `TextIndependentIdentification` függvényt.

:::code language="javascript" source="~/cognitive-services-quickstart-code/javascript/speech/speaker-recognition.js" id="text_independent_indentification":::

A `TextDependentVerification` és a `TextIndependentVerification` függvényekhez hasonlóan ez a függvény létrehoz egy [VoiceProfile](/javascript/api/microsoft-cognitiveservices-speech-sdk/voiceprofile) objektumot a [VoiceProfileClient. createProfileAsync](/javascript/api/microsoft-cognitiveservices-speech-sdk/voiceprofileclient#createprofileasync-voiceprofiletype--string---e--voiceprofile-----void---e--string-----void-) metódussal.

Ebben az esetben a következőnek kell megfelelnie: `VoiceProfileType.TextIndependentIdentification` `VoiceProfileClient.createProfileAsync` .

Ezután hívja meg a következő két segítő függvényt: `AddEnrollmentsToTextIndependentProfile` , amelyet már definiált, és `SpeakerIdentify` amelynél a Next (tovább) értéket fogja meghatározni. Végül hívja meg a [VoiceProfileClient. deleteProfileAsync](/javascript/api/microsoft-cognitiveservices-speech-sdk/voiceprofileclient#deleteprofileasync-voiceprofile---response--voiceprofileresult-----void---e--string-----void-) parancsot a profil eltávolításához.

### <a name="speakeridentify-function"></a>SpeakerIdentify függvény

Adja meg a `SpeakerIdentify` függvényt az alábbiak szerint.

:::code language="javascript" source="~/cognitive-services-quickstart-code/javascript/speech/speaker-recognition.js" id="speaker_identify":::

Ebben a függvényben létrehoz egy [SpeakerIdentificationModel](/javascript/api/microsoft-cognitiveservices-speech-sdk/speakeridentificationmodel) objektumot a [SpeakerIdentificationModel. fromProfiles](/javascript/api/microsoft-cognitiveservices-speech-sdk/speakeridentificationmodel#fromprofiles-voiceprofile---) metódussal, amely a korábban létrehozott [VoiceProfile](/javascript/api/microsoft-cognitiveservices-speech-sdk/voiceprofile) objektumot adja meg.

Ezután hívja meg a [SpeechRecognizer. recognizeOnceAsync](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer#recognizeonceasync--e--speechrecognitionresult-----void---e--string-----void-) metódust, és adjon meg egy hangmintát.
`SpeechRecognizer.recognizeOnceAsync` megpróbálja azonosítani a hangminta hangját a `VoiceProfile` létrehozásához használt objektumok alapján `SpeakerIdentificationModel` . Egy [SpeakerRecognitionResult](/javascript/api/microsoft-cognitiveservices-speech-sdk/speakerrecognitionresult) objektumot ad vissza, amelynek a `profileId` tulajdonsága azonosítja a megfeleltetést `VoiceProfile` , ha van ilyen, míg a `score` tulajdonság egy hasonlósági pontszámot tartalmaz a 0,0-1.0-tól kezdve.

## <a name="main-function"></a>Fő függvény

Végül adja meg a `main` függvényt az alábbiak szerint.

:::code language="javascript" source="~/cognitive-services-quickstart-code/javascript/speech/speaker-recognition.js" id="main":::

Ez a függvény létrehoz egy [VoiceProfileClient](/javascript/api/microsoft-cognitiveservices-speech-sdk/voiceprofileclient) objektumot, amely Hangprofilok létrehozására, regisztrálására és törlésére szolgál. Ezután meghívja a korábban definiált függvényeket.
