---
author: v-jaswel
ms.service: cognitive-services
ms.topic: include
ms.date: 09/28/2020
ms.author: v-jawe
ms.custom: references_regions
ms.openlocfilehash: f8b29da09f5da4a30378cc0228fecad63d196162
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/07/2021
ms.locfileid: "102445364"
---
Ebben a rövid útmutatóban megtudhatja, milyen alapszintű tervezési mintákat Speaker Recognition a Speech SDK használatával, beleértve a következőket:

* Szövegtől függő és szöveg-független ellenőrzés
* Beszélő azonosítása a hangminták azonosításához a hangminta
* Hangprofilok törlése

A beszédfelismeréssel kapcsolatos fogalmak [áttekintését az áttekintő](../../../speaker-recognition-overview.md) cikkben tekintheti meg.

## <a name="skip-to-samples-on-github"></a>Ugrás a mintákra a GitHubon

Ha közvetlenül a mintakód kihagyását szeretné kihagyni, tekintse meg a C++ gyors üzembe helyezési [mintákat](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/cpp/windows) a githubon.

## <a name="prerequisites"></a>Előfeltételek

Ez a cikk azt feltételezi, hogy rendelkezik Azure-fiókkal és Speech Service-előfizetéssel. Ha nem rendelkezik fiókkal és előfizetéssel, [próbálja ki ingyenesen a Speech szolgáltatást](../../../overview.md#try-the-speech-service-for-free).

> [!IMPORTANT]
> A Speaker Recognition jelenleg *csak* a régióban létrehozott Azure Speech-erőforrások esetében támogatott `westus` .

## <a name="install-the-speech-sdk"></a>A Speech SDK telepítése

Mielőtt bármit elvégezhet, telepítenie kell a Speech SDK-t. A platformtól függően kövesse az alábbi utasításokat:

* <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/setup-platform?tabs=linux&pivots=programming-language-cpp" target="_blank">Linux </a>
* <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/setup-platform?tabs=macos&pivots=programming-language-cpp" target="_blank">macOS </a>
* <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/setup-platform?tabs=windows&pivots=programming-language-cpp" target="_blank">Windows </a>

## <a name="import-dependencies"></a>Függőségek importálása

A cikkben szereplő példák futtatásához adja hozzá a következő utasításokat a. cpp fájl elejéhez.

:::code language="cpp" source="~/cognitive-services-quickstart-code/cpp/speech/speaker-recognition.cpp" id="dependencies":::

## <a name="create-a-speech-configuration"></a>Beszédfelismerési konfiguráció létrehozása

A beszédfelismerési szolgáltatás a Speech SDK használatával történő meghívásához létre kell hoznia egy [`SpeechConfig`](/cpp/cognitive-services/speech/speechconfig) . Ez az osztály az előfizetésével kapcsolatos információkat tartalmaz, például a kulcsot és a társított régiót, végpontot, gazdagépet vagy engedélyezési jogkivonatot.

:::code language="cpp" source="~/cognitive-services-quickstart-code/cpp/speech/speaker-recognition.cpp" id="get_speech_config":::

## <a name="text-dependent-verification"></a>Szövegtől függő ellenőrzés

Speaker Verification az a szabály, amely megerősíti, hogy egy beszélő ismert vagy **regisztrált** hangra illeszkedik. Az első **lépés a** hangprofil beléptetése, hogy a szolgáltatásnak van-e összehasonlítani a jövőbeli hangmintákkal. Ebben a példában egy **szövegtől függő** stratégia használatával regisztrálja a profilt, amelyhez a regisztrációhoz és ellenőrzéshez egy adott jelszó szükséges. A támogatott hozzáférési kódok listáját a [dokumentációs](/rest/api/speakerrecognition/) dokumentációban tekintheti meg.

### <a name="textdependentverification-function"></a>TextDependentVerification függvény

Először hozza létre a `TextDependentVerification` függvényt.

:::code language="cpp" source="~/cognitive-services-quickstart-code/cpp/speech/speaker-recognition.cpp" id="text_dependent_verification":::

Ez a függvény létrehoz egy [VoiceProfile](/cpp/cognitive-services/speech/voiceprofile) objektumot a [CreateProfileAsync](/cpp/cognitive-services/speech/voiceprofileclient#createprofileasync) metódussal. Vegye figyelembe, hogy háromféle [típus](/cpp/cognitive-services/speech/microsoft-cognitiveservices-speech-namespace#enum-voiceprofiletype) létezik `VoiceProfile` :

- TextIndependentIdentification
- TextDependentVerification
- TextIndependentVerification

Ebben az esetben a következőnek kell megfelelnie: `VoiceProfileType::TextDependentVerification` `CreateProfileAsync` .

Ezután hívja meg a következő két segítő függvényt, amelyeket meg fog határozni `AddEnrollmentsToTextDependentProfile` `SpeakerVerify` . Végezetül a [DeleteProfileAsync](/cpp/cognitive-services/speech/voiceprofileclient#deleteprofileasync) meghívásával törölje a profilt.

### <a name="addenrollmentstotextdependentprofile-function"></a>AddEnrollmentsToTextDependentProfile függvény

Adja meg a következő függvényt a hangprofil regisztrálásához.

:::code language="cpp" source="~/cognitive-services-quickstart-code/cpp/speech/speaker-recognition.cpp" id="add_enrollments_dependent":::

Ebben a függvényben olyan hangmintákat regisztrál egy `while` hurokban, amelyek nyomon követik a beléptetéshez szükséges és a szükséges mintákat. Az egyes iterációkban a [EnrollProfileAsync](/cpp/cognitive-services/speech/voiceprofileclient#enrollprofileasync) felszólítja, hogy beszéljen a jelszót a mikrofonba, és hozzáadja a mintát a hangprofilhoz.

### <a name="speakerverify-function"></a>SpeakerVerify függvény

Adja meg `SpeakerVerify` a következőt:

:::code language="cpp" source="~/cognitive-services-quickstart-code/cpp/speech/speaker-recognition.cpp" id="speaker_verify":::

Ebben a függvényben létrehoz egy [SpeakerVerificationModel](/cpp/cognitive-services/speech/speakerverificationmodel) objektumot a [SpeakerVerificationModel:: FromProfile](/cpp/cognitive-services/speech/speakerverificationmodel#fromprofile) metódussal, amely a korábban létrehozott [VoiceProfile](/cpp/cognitive-services/speech/voiceprofile) objektumba kerül.

Next, [SpeechRecognizer:: RecognizeOnceAsync](/cpp/cognitive-services/speech/speechrecognizer#recognizeonceasync) megkéri, hogy ismét beszéljen a jelszóval, de ezúttal érvényesíti a hangprofilját, és egy hasonlósági pontszámot ad vissza a 0,0-1.0-tól kezdve. A [SpeakerRecognitionResult](/cpp/cognitive-services/speech/speakerrecognitionresult) objektum a vagy a függvényt is megadja `Accept` `Reject` , attól függően, hogy a jelszó egyezik-e.

## <a name="text-independent-verification"></a>Szövegtől független ellenőrzés

A **szövegtől függő** hitelesítéssel szemben a **szövegtől független** ellenőrzés:

* Nincs szükség egy bizonyos jelszó megírására, amit mondanak
* A nem igényel három hangmintát, *de a* teljes hang 20 másodpercet igényel

### <a name="textindependentverification-function"></a>TextIndependentVerification függvény

Először hozza létre a `TextIndependentVerification` függvényt.

:::code language="cpp" source="~/cognitive-services-quickstart-code/cpp/speech/speaker-recognition.cpp" id="text_independent_verification":::

A `TextDependentVerification` függvényhez hasonlóan ez a függvény létrehoz egy [VoiceProfile](/cpp/cognitive-services/speech/voiceprofile) objektumot a [CreateProfileAsync](/cpp/cognitive-services/speech/voiceprofileclient#createprofileasync) metódussal.

Ebben az esetben a következőnek kell megfelelnie: `VoiceProfileType::TextIndependentVerification` `CreateProfileAsync` .

Ezután hívja meg a következő két segítő függvényt: `AddEnrollmentsToTextIndependentProfile` , amelyet a Next (tovább) és a (z `SpeakerVerify` ) már definiált. Végezetül a [DeleteProfileAsync](/cpp/cognitive-services/speech/voiceprofileclient#deleteprofileasync) meghívásával törölje a profilt.

### <a name="addenrollmentstotextindependentprofile"></a>AddEnrollmentsToTextIndependentProfile

Adja meg a következő függvényt a hangprofil regisztrálásához.

:::code language="cpp" source="~/cognitive-services-quickstart-code/cpp/speech/speaker-recognition.cpp" id="add_enrollments_independent":::

Ebben a függvényben olyan hangmintákat regisztrál a `while` hurokban, amelyek nyomon követik a beléptetéshez szükséges hangmennyiséget és a szükséges időt. Az egyes iterációkban a [EnrollProfileAsync](/cpp/cognitive-services/speech/voiceprofileclient#enrollprofileasync) felszólítja, hogy beszéljen a mikrofonba, és hozzáadja a mintát a hangprofilhoz.

## <a name="speaker-identification"></a>Beszélőazonosítás

A Speaker Identification segítségével megállapítható, hogy **ki** beszél a regisztrált hangok adott csoportjából. A folyamat nagyon hasonlít a **szövegtől független ellenőrzéshez**, és a fő különbség, hogy egyszerre több hangprofillal is képes ellenőrizni, nem pedig egyetlen profilon.

### <a name="textindependentidentification-function"></a>TextIndependentIdentification függvény

Először hozza létre a `TextIndependentIdentification` függvényt.

:::code language="cpp" source="~/cognitive-services-quickstart-code/cpp/speech/speaker-recognition.cpp" id="text_independent_indentification":::

A `TextDependentVerification` és a `TextIndependentVerification` függvényekhez hasonlóan ez a függvény létrehoz egy [VoiceProfile](/cpp/cognitive-services/speech/voiceprofile) objektumot a [CreateProfileAsync](/cpp/cognitive-services/speech/voiceprofileclient#createprofileasync) metódussal.

Ebben az esetben a következőnek kell megfelelnie: `VoiceProfileType::TextIndependentIdentification` `CreateProfileAsync` .

Ezután hívja meg a következő két segítő függvényt: `AddEnrollmentsToTextIndependentProfile` , amelyet már definiált, és `SpeakerIdentify` amelynél a Next (tovább) értéket fogja meghatározni. Végezetül a [DeleteProfileAsync](/cpp/cognitive-services/speech/voiceprofileclient#deleteprofileasync) meghívásával törölje a profilt.

### <a name="speakeridentify-function"></a>SpeakerIdentify függvény

Adja meg a `SpeakerIdentify` függvényt az alábbiak szerint.

:::code language="cpp" source="~/cognitive-services-quickstart-code/cpp/speech/speaker-recognition.cpp" id="speaker_identify":::

Ebben a függvényben létrehoz egy [SpeakerIdentificationModel](/cpp/cognitive-services/speech/speakeridentificationmodel) objektumot a [SpeakerIdentificationModel:: FromProfiles](/cpp/cognitive-services/speech/speakeridentificationmodel#fromprofiles) metódussal. `SpeakerIdentificationModel::FromProfiles` a [VoiceProfile](/cpp/cognitive-services/speech/voiceprofile) objektumok listáját fogadja el. Ebben az esetben csak a `VoiceProfile` korábban létrehozott objektumot adja meg. Ha azonban azt szeretné, több objektumot is átadhat `VoiceProfile` , amelyek mindegyike más hangmintákkal van regisztrálva.

Következő, [SpeechRecognizer:: RecognizeOnceAsync](/cpp/cognitive-services/speech/speechrecognizer#recognizeonceasync) megkéri, hogy beszéljen újra. Ezúttal összehasonlítja a hangját a regisztrált hangprofilokkal, és visszaadja a leginkább hasonló hangprofilt.

## <a name="main-function"></a>Fő függvény

Végül adja meg a `main` függvényt az alábbiak szerint.

:::code language="cpp" source="~/cognitive-services-quickstart-code/cpp/speech/speaker-recognition.cpp" id="main":::

Ez a függvény egyszerűen meghívja a korábban definiált függvényeket. Először is létrehoz egy [VoiceProfileClient](/cpp/cognitive-services/speech/voiceprofileclient) objektumot és egy [SpeakerRecognizer](/cpp/cognitive-services/speech/speakerrecognizer) objektumot.

```
auto speech_config = GetSpeechConfig();
auto client = VoiceProfileClient::FromConfig(speech_config);
auto recognizer = SpeakerRecognizer::FromConfig(speech_config, audio_config);
```

A a `VoiceProfileClient` Hangprofilok létrehozásához, regisztrálásához és törléséhez használatos. A a `SpeakerRecognizer` beszédfelismerési minták egy vagy több regisztrált hangprofilra való érvényesítésére szolgál.

## <a name="changing-audio-input-type"></a>Hangbemenet típusának módosítása

A cikkben szereplő példák az alapértelmezett eszköz mikrofonját használják a hangmintákhoz bemenetként. Azonban olyan helyzetekben, amikor hangfájlokat kell használnia a mikrofon bemenete helyett, egyszerűen módosítsa a következő sort:

```
auto audio_config = Audio::AudioConfig::FromDefaultMicrophoneInput();
```

erre:

```
auto audio_config = Audio::AudioConfig::FromWavFileInput(path/to/your/file.wav);
```

Vagy cserélje le a `audio_config` [hanganyag használatát:: AudioConfig:: FromWavFileInput](/cpp/cognitive-services/speech/audio-audioconfig#fromwavfileinput). Vegyes bemeneteket is használhat, ha például egy mikrofont használ a regisztráláshoz és a fájlok ellenőrzéséhez.