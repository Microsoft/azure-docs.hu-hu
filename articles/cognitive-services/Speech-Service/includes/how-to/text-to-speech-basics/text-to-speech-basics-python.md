---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/25/2020
ms.author: trbye
ms.openlocfilehash: 9d6d6ec36d5ab7d29b3050dafda5fd711b01f1f4
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/08/2021
ms.locfileid: "107108859"
---
Ebben a rövid útmutatóban megtudhatja, hogyan végezheti el a szöveg-beszéd szintézist a Speech SDK használatával. Első lépésként alapkonfigurációt és szintézist kell elvégeznie, és az egyéni alkalmazások fejlesztéséhez további speciális példákra kell lépnie, például:

* Válaszok a memóriában tárolt adatfolyamok beolvasására
* A kimeneti mintavételezési arány és a bitsűrűség testreszabása
* Összegző kérelmek elküldése a SSML használatával (Speech szintézis Markup Language)
* Neurális hangok használata

## <a name="skip-to-samples-on-github"></a>Ugrás a mintákra a GitHubon

Ha közvetlenül a mintakód kihagyása mellett szeretne kiugrani, tekintse meg a [Python gyors példákat](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/python/text-to-speech) a githubon.

## <a name="prerequisites"></a>Előfeltételek

Ez a cikk azt feltételezi, hogy rendelkezik Azure-fiókkal és Speech Service-előfizetéssel. Ha nem rendelkezik fiókkal és előfizetéssel, [próbálja ki ingyenesen a Speech szolgáltatást](../../../overview.md#try-the-speech-service-for-free).

## <a name="install-the-speech-sdk"></a>A Speech SDK telepítése

Mielőtt bármit elvégezhet, telepítenie kell a Speech SDK-t.

```Python
pip install azure-cognitiveservices-speech
```

Ha macOS rendszeren fut, és telepítési problémákba ütközik, előfordulhat, hogy először futtatnia kell ezt a parancsot.

```Python
python3 -m pip install --upgrade pip
```

A Speech SDK telepítését követően az alábbi importálási utasítások szerepelnek a parancsfájl elején.

```Python
from azure.cognitiveservices.speech import AudioDataStream, SpeechConfig, SpeechSynthesizer, SpeechSynthesisOutputFormat
from azure.cognitiveservices.speech.audio import AudioOutputConfig
```

## <a name="create-a-speech-configuration"></a>Beszédfelismerési konfiguráció létrehozása

A beszédfelismerési szolgáltatás a Speech SDK használatával történő meghívásához létre kell hoznia egy [`SpeechConfig`](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig) . Ez az osztály az előfizetésével kapcsolatos információkat tartalmaz, például a kulcsot és a társított régiót, végpontot, gazdagépet vagy engedélyezési jogkivonatot.

> [!NOTE]
> Függetlenül attól, hogy elvégezte-e a beszédfelismerést, a beszédfelismerést, a fordítást vagy a szándék felismerését, mindig hozzon létre egy konfigurációt.

Az alábbiakat többféleképpen lehet inicializálni [`SpeechConfig`](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig) :

* Előfizetéssel: adjon egy kulcsot és a hozzá tartozó régiót.
* Egy végponttal: pass a Speech Service-végponton. Kulcs-vagy engedélyezési jogkivonat nem kötelező.
* Gazdagép esetén: adjon meg egy gazdagép-címeket. Kulcs-vagy engedélyezési jogkivonat nem kötelező.
* Engedélyezési jogkivonattal: adjon meg egy engedélyezési jogkivonatot és a hozzá tartozó régiót.

Ebben a példában egy [`SpeechConfig`](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig) előfizetési kulcsot és egy régiót hoz létre. Szerezze be ezeket a hitelesítő adatokat a [beszédfelismerési szolgáltatás ingyenes kipróbálásához](../../../overview.md#try-the-speech-service-for-free)szükséges lépések követésével.

```python
speech_config = SpeechConfig(subscription="YourSubscriptionKey", region="YourServiceRegion")
```

## <a name="synthesize-speech-to-a-file"></a>Beszéd készítése fájlba

Ezután létrehoz egy [`SpeechSynthesizer`](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechsynthesizer) objektumot, amely szöveg-beszéd átalakításokat és kimeneteket hajt végre a hangszórók, fájlok vagy más kimeneti adatfolyamok számára. Az [`SpeechSynthesizer`](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechsynthesizer) elfogadva paraméterként az [`SpeechConfig`](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig) előző lépésben létrehozott objektumot, valamint egy objektumot, [`AudioOutputConfig`](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.audio.audiooutputconfig) amely meghatározza a kimeneti eredmények kezelését.

Az indításhoz hozzon létre egy `AudioOutputConfig` -t, hogy automatikusan a kimenetet egy fájlba írja a `.wav` `filename` konstruktor param használatával.

```python
audio_config = AudioOutputConfig(filename="path/to/write/file.wav")
```

Ezután hozza létre a példányt a `SpeechSynthesizer` `speech_config` objektum és az `audio_config` objektum paraméterként való átadásával. Ezután a beszédfelismerés végrehajtása és a fájlba való írás olyan egyszerű, mintha `speak_text_async()` egy szöveges karakterláncot futtasson.

```python
synthesizer = SpeechSynthesizer(speech_config=speech_config, audio_config=audio_config)
synthesizer.speak_text_async("A simple test to write to a file.")
```

Futtassa a programot, és a rendszer a `.wav` megadott helyre írja a szintetizált fájlt. Ez jó példa a legalapvetőbb használatra, de a következő lépés a kimenet testreszabása és a kimeneti válasz kezelése memóriában tárolt adatfolyamként az egyéni forgatókönyvek használata esetén.

## <a name="synthesize-to-speaker-output"></a>Szintetizálás a hangszórók kimenetére

Bizonyos esetekben érdemes közvetlenül a szintetizált beszédet közvetlenül a beszélőhöz adni. Ehhez használja az előző szakaszban szereplő példát, de módosítsa a `AudioOutputConfig` `filename` paramétert a param eltávolításával, majd állítsa be a következőt: `use_default_speaker=True` . Ez a kimenet az aktuális aktív kimeneti eszközre mutat.

```python
audio_config = AudioOutputConfig(use_default_speaker=True)
```

## <a name="get-result-as-an-in-memory-stream"></a>Eredmény beolvasása memóriabeli adatfolyamként

A beszédfelismerés számos forgatókönyve esetében valószínű, hogy az eredményül kapott hangadatokat memórián belüli adatfolyamként kell megadnia, nem pedig közvetlenül egy fájlba írni. Ez lehetővé teszi, hogy egyéni viselkedést hozzon létre, beleértve a következőket:

* Az eredményül kapott bájtos tömböt az egyéni alsóbb rétegbeli szolgáltatásokhoz tartozó, keresési lehetőségként szolgáló adatfolyamként elvonta
* Az eredmény integrálása más API-k vagy szolgáltatások használatával.
* A hangadatok módosítása, egyéni `.wav` fejlécek írása stb.

Ezt a változást egyszerűen elvégezheti az előző példából. Először távolítsa el a `AudioConfig` -t, mert ezzel a lépéssel manuálisan fogja kezelni a kimeneti viselkedést a jobb szabályozás érdekében. Ezután adja át a-t a `None` `AudioConfig` `SpeechSynthesizer` konstruktorban.

> [!NOTE]
> `None`A ( `AudioConfig` z) helyett, ahelyett, hogy kihagyja, mint a fenti hangsugárzó-kimeneti példában, a nem játssza le alapértelmezés szerint a hangot a jelenlegi aktív kimeneti eszközön.

Ezúttal egy változóba menti az eredményt [`SpeechSynthesisResult`](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechsynthesisresult) . A `audio_data` tulajdonság a `bytes` kimeneti adatokat tartalmazó objektumot tartalmaz. Ezt az objektumot manuálisan is használhatja, vagy a [`AudioDataStream`](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.audiodatastream) osztály használatával kezelheti a memóriában lévő adatfolyamot. Ebben a példában a konstruktor használatával `AudioDataStream` kap egy streamet az eredményből.

```python
synthesizer = SpeechSynthesizer(speech_config=speech_config, audio_config=None)
result = synthesizer.speak_text_async("Getting the response as an in-memory stream.").get()
stream = AudioDataStream(result)
```

Innen bármilyen egyéni viselkedést alkalmazhat az eredményül kapott `stream` objektum használatával.

## <a name="customize-audio-format"></a>Hangformátum testreszabása

A következő szakasz bemutatja, hogyan szabhatja testre a hangkimeneti attribútumokat, beleértve a következőket:

* Hangfájl típusa
* Mintavételezési arány
* Kis mélység

A hangformátum módosításához használja a `set_speech_synthesis_output_format()` függvényt az `SpeechConfig` objektumon. Ez a függvény egy `enum` típust vár [`SpeechSynthesisOutputFormat`](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechsynthesisoutputformat) , amelyet a kimeneti formátum kiválasztásához használ. A rendelkezésre álló [hangformátumok listáját](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechsynthesisoutputformat) az útmutató dokumentációjában tekintheti meg.

A követelményektől függően különböző fájltípusok választhatók. Vegye figyelembe, hogy definíció szerint a nyers formátumok, például a nem `Raw24Khz16BitMonoPcm` tartalmaznak hangfejléceket. A nyers formátumok csak akkor használhatók, ha tudja, hogy az alsóbb rétegbeli implementáció dekódolást végez a nyers Bitstream, vagy ha manuálisan kívánja felépíteni a fejléceket a kis mélység, a mintavételezési arány, a csatornák száma stb. alapján.

Ebben a példában egy magas hűségű RIFF formátumot kell megadnia az `Riff24Khz16BitMonoPcm` objektumra vonatkozó beállítással `SpeechSynthesisOutputFormat` `SpeechConfig` . Az előző szakaszban szereplő példához hasonlóan a [`AudioDataStream`](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.audiodatastream) segítségével beolvashatja az eredmény memóriában lévő adatfolyamát, majd megírhatja azt egy fájlba.


```python
speech_config.set_speech_synthesis_output_format(SpeechSynthesisOutputFormat["Riff24Khz16BitMonoPcm"])
synthesizer = SpeechSynthesizer(speech_config=speech_config, audio_config=None)

result = synthesizer.speak_text_async("Customizing audio output format.").get()
stream = AudioDataStream(result)
stream.save_to_wav_file("path/to/write/file.wav")
```

A program ismételt futtatásakor a rendszer egy testreszabott `.wav` fájlt ír a megadott elérési útra.

## <a name="use-ssml-to-customize-speech-characteristics"></a>A SSML használata a beszédfelismerési jellemzők testreszabásához

A Speech szintézis Markup Language (SSML) lehetővé teszi, hogy a kérések XML-sémából való elküldésével részletesen beállítsa a szöveg-beszéd kimenetet, a kiejtést, a beszéd sebességét, a kötetet és a többit. Ez a szakasz egy példát mutat be a hang módosítására, de részletesebb útmutatásért tekintse meg a [SSML ismertető cikket](../../../speech-synthesis-markup.md).

A SSML testreszabáshoz való használatának megkezdéséhez egy egyszerű módosítást hajt végre, amely átváltja a hangot.
Először hozzon létre egy új XML-fájlt a SSML config-hoz a legfelső szintű Project-címtárban, ebben a példában `ssml.xml` . A gyökérelem mindig `<speak>` , és egy elem szövegének körbefuttatása `<voice>` lehetővé teszi a hang módosítását a `name` param használatával. Tekintse meg a támogatott **neurális** hangok [teljes listáját](../../../language-support.md#neural-voices) .

```xml
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
  <voice name="en-US-AriaNeural">
    When you're on the freeway, it's a good idea to use a GPS.
  </voice>
</speak>
```

Ezután módosítania kell a beszédfelismerési kérést az XML-fájlra való hivatkozáshoz. A kérelem többnyire azonos, de a függvény használata helyett használja a parancsot `speak_text_async()` `speak_ssml_async()` . Ez a függvény egy XML-karakterláncot vár, ezért először olvassa be a SSML config karakterláncként. Innen az eredmény objektum pontosan megegyezik az előző példákkal.

> [!NOTE]
> Ha a `ssml_string` `ï»¿` karakterlánc elején található, ki kell választania az anyagjegyzék formátumát, vagy a szolgáltatás hibát ad vissza. Ezt úgy teheti meg, hogy az alábbiak szerint állítja be a `encoding` paramétert: `open("ssml.xml", "r", encoding="utf-8-sig")` .

```python
synthesizer = SpeechSynthesizer(speech_config=speech_config, audio_config=None)

ssml_string = open("ssml.xml", "r").read()
result = synthesizer.speak_ssml_async(ssml_string).get()

stream = AudioDataStream(result)
stream.save_to_wav_file("path/to/write/file.wav")
```

> [!NOTE]
> Ha a SSML használata nélkül szeretné módosítani a hangot, beállíthatja a tulajdonságot a következő `SpeechConfig` használatával: `SpeechConfig.speech_synthesis_voice_name = "en-US-AriaNeural"`

## <a name="get-facial-pose-events"></a>Arc póz-események beolvasása

A beszédfelismerés jó módszer lehet az arc-kifejezések animálására.
A [visemes](../../../how-to-speech-synthesis-viseme.md) gyakran a megfigyelt beszédben jelentenek, például az ajkak, az állkapocs és a nyelv pozíciója egy adott fonéma készítésekor.
Feliratkozhat a viseme eseményre a Speech SDK-ban.
Ezt követően alkalmazhatja a viseme eseményeket, így animálhatja a karakterek színeit beszéd hanglejátszásként.
Ismerje meg [, hogyan szerezhet be viseme-eseményeket](../../../how-to-speech-synthesis-viseme.md#get-viseme-events-with-the-speech-sdk).
