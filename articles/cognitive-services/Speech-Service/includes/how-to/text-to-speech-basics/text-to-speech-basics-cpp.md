---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/25/2020
ms.author: trbye
ms.openlocfilehash: e95d2adfd4a4a1c9cbecdf068fd9ee5baf8dcc49
ms.sourcegitcommit: 17b36b13857f573639d19d2afb6f2aca74ae56c1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/10/2020
ms.locfileid: "94424709"
---
Ebben a rövid útmutatóban megtudhatja, hogyan végezheti el a szöveg-beszéd szintézist a Speech SDK használatával. Első lépésként alapkonfigurációt és szintézist kell elvégeznie, és az egyéni alkalmazások fejlesztéséhez további speciális példákra kell lépnie, például:

* Válaszok a memóriában tárolt adatfolyamok beolvasására
* A kimeneti mintavételezési arány és a bitsűrűség testreszabása
* Összegző kérelmek elküldése a SSML használatával (Speech szintézis Markup Language)
* Neurális hangok használata

## <a name="skip-to-samples-on-github"></a>Ugrás a mintákra a GitHubon

Ha közvetlenül a mintakód kihagyását szeretné kihagyni, tekintse meg a C++ gyors üzembe helyezési [mintákat](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/cpp/windows/text-to-speech) a githubon.

## <a name="prerequisites"></a>Előfeltételek

Ez a cikk azt feltételezi, hogy rendelkezik Azure-fiókkal és Speech Service-előfizetéssel. Ha nem rendelkezik fiókkal és előfizetéssel, [próbálja ki ingyenesen a Speech szolgáltatást](../../../overview.md#try-the-speech-service-for-free).

## <a name="install-the-speech-sdk"></a>A Speech SDK telepítése

Mielőtt bármit elvégezhet, telepítenie kell a Speech SDK-t. A platformtól függően kövesse az alábbi utasításokat:

* <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/setup-platform?tabs=linux&pivots=programming-language-cpp" target="_blank">Linux <span class="docon docon-navigate-external x-hidden-focus"></span></a>
* <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/setup-platform?tabs=macos&pivots=programming-language-cpp" target="_blank">macOS <span class="docon docon-navigate-external x-hidden-focus"></span></a>
* <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/setup-platform?tabs=windows&pivots=programming-language-cpp" target="_blank">Windows <span class="docon docon-navigate-external x-hidden-focus"></span></a>

## <a name="import-dependencies"></a>Függőségek importálása

A cikkben szereplő példák futtatásához a következő importálási és utasítások szerepelnek a `using` parancsfájl elején.

```cpp
#include <iostream>
#include <fstream>
#include <string>
#include <speechapi_cxx.h>

using namespace std;
using namespace Microsoft::CognitiveServices::Speech;
using namespace Microsoft::CognitiveServices::Speech::Audio;
```

## <a name="create-a-speech-configuration"></a>Beszédfelismerési konfiguráció létrehozása

A beszédfelismerési szolgáltatás a Speech SDK használatával történő meghívásához létre kell hoznia egy [`SpeechConfig`](/cpp/cognitive-services/speech/speechconfig) . Ez az osztály az előfizetésével kapcsolatos információkat tartalmaz, például a kulcsot és a társított régiót, végpontot, gazdagépet vagy engedélyezési jogkivonatot.

> [!NOTE]
> Függetlenül attól, hogy elvégezte-e a beszédfelismerést, a beszédfelismerést, a fordítást vagy a szándék felismerését, mindig hozzon létre egy konfigurációt.

Az alábbiakat többféleképpen lehet inicializálni [`SpeechConfig`](/cpp/cognitive-services/speech/speechconfig) :

* Előfizetéssel: adjon egy kulcsot és a hozzá tartozó régiót.
* Egy végponttal: pass a Speech Service-végponton. Kulcs-vagy engedélyezési jogkivonat nem kötelező.
* Gazdagép esetén: adjon meg egy gazdagép-címeket. Kulcs-vagy engedélyezési jogkivonat nem kötelező.
* Engedélyezési jogkivonattal: adjon meg egy engedélyezési jogkivonatot és a hozzá tartozó régiót.

Ebben a példában egy [`SpeechConfig`](/cpp/cognitive-services/speech/speechconfig) előfizetési kulcsot és egy régiót hoz létre. Szerezze be ezeket a hitelesítő adatokat a [beszédfelismerési szolgáltatás ingyenes kipróbálásához](../../../overview.md#try-the-speech-service-for-free)szükséges lépések követésével. Emellett a cikk további részében is létrehozhat egy alapszintű, a különböző testreszabási beállításokkal módosítható egyszerű kiírási kódot.

```cpp
int wmain()
{
    try
    {
        synthesizeSpeech();
    }
    catch (exception e)
    {
        cout << e.what();
    }
    return 0;
}
    
void synthesizeSpeech() 
{
    auto config = SpeechConfig::FromSubscription("YourSubscriptionKey", "YourServiceRegion");
}
```

## <a name="synthesize-speech-to-a-file"></a>Beszéd készítése fájlba

Ezután létrehoz egy [`SpeechSynthesizer`](/cpp/cognitive-services/speech/speechsynthesizer) objektumot, amely szöveg-beszéd átalakításokat és kimeneteket hajt végre a hangszórók, fájlok vagy más kimeneti adatfolyamok számára. Az [`SpeechSynthesizer`](/cpp/cognitive-services/speech/speechsynthesizer) elfogadva paraméterként az [`SpeechConfig`](/cpp/cognitive-services/speech/speechconfig) előző lépésben létrehozott objektumot, valamint egy objektumot, [`AudioConfig`](/cpp/cognitive-services/speech/audio-audioconfig) amely meghatározza a kimeneti eredmények kezelését.

Az indításhoz hozzon létre egy `AudioConfig` -t, hogy a függvény használatával automatikusan megírja a kimenetet egy `.wav` fájlba `FromWavFileOutput()` .

```cpp
void synthesizeSpeech() 
{
    auto config = SpeechConfig::FromSubscription("YourSubscriptionKey", "YourServiceRegion");
    auto audioConfig = AudioConfig::FromWavFileOutput("path/to/write/file.wav");
}
```

Ezután hozza létre az a példányt `SpeechSynthesizer` , és adja át az `config` objektum és az `audioConfig` objektum paraméterként való átadását. Ezután a beszédfelismerés végrehajtása és a fájlba való írás olyan egyszerű, mintha `SpeakTextAsync()` egy szöveges karakterláncot futtasson.

```cpp
void synthesizeSpeech() 
{
    auto config = SpeechConfig::FromSubscription("YourSubscriptionKey", "YourServiceRegion");
    auto audioConfig = AudioConfig::FromWavFileOutput("path/to/write/file.wav");
    auto synthesizer = SpeechSynthesizer::FromConfig(config, audioConfig);
    auto result = synthesizer->SpeakTextAsync("A simple test to write to a file.").get();
}
```

Futtassa a programot, és a rendszer a `.wav` megadott helyre írja a szintetizált fájlt. Ez jó példa a legalapvetőbb használatra, de a következő lépés a kimenet testreszabása és a kimeneti válasz kezelése memóriában tárolt adatfolyamként az egyéni forgatókönyvek használata esetén.

## <a name="synthesize-to-speaker-output"></a>Szintetizálás a hangszórók kimenetére

Bizonyos esetekben érdemes közvetlenül a szintetizált beszédet közvetlenül a beszélőhöz adni. Ehhez egyszerűen hagyja `AudioConfig` ki a paramétert a `SpeechSynthesizer` fenti példában szereplő példa létrehozásakor. Ez a kimenet az aktuális aktív kimeneti eszközre mutat.

```cpp
void synthesizeSpeech() 
{
    auto config = SpeechConfig::FromSubscription("YourSubscriptionKey", "YourServiceRegion");
    auto synthesizer = SpeechSynthesizer::FromConfig(config);
    auto result = synthesizer->SpeakTextAsync("Synthesizing directly to speaker output.").get();
}
```

## <a name="get-result-as-an-in-memory-stream"></a>Eredmény beolvasása memóriabeli adatfolyamként

A beszédfelismerés számos forgatókönyve esetében valószínű, hogy az eredményül kapott hangadatokat memórián belüli adatfolyamként kell megadnia, nem pedig közvetlenül egy fájlba írni. Ez lehetővé teszi, hogy egyéni viselkedést hozzon létre, beleértve a következőket:

* Az eredményül kapott bájtos tömböt az egyéni alsóbb rétegbeli szolgáltatásokhoz tartozó, keresési lehetőségként szolgáló adatfolyamként elvonta
* Az eredmény integrálása más API-k vagy szolgáltatások használatával.
* A hangadatok módosítása, egyéni `.wav` fejlécek írása stb.

Ezt a változást egyszerűen elvégezheti az előző példából. Először távolítsa el a `AudioConfig` -t, mert ezzel a lépéssel manuálisan fogja kezelni a kimeneti viselkedést a jobb szabályozás érdekében. Ezután adja át a-t a `NULL` `AudioConfig` `SpeechSynthesizer` konstruktorban. 

> [!NOTE]
> `NULL`A ( `AudioConfig` z) helyett, ahelyett, hogy kihagyja, mint a fenti hangsugárzó-kimeneti példában, a nem játssza le alapértelmezés szerint a hangot a jelenlegi aktív kimeneti eszközön.

Ezúttal egy változóba menti az eredményt [`SpeechSynthesisResult`](/cpp/cognitive-services/speech/speechsynthesisresult) . A `GetAudioData` beolvasó a `byte []` kimeneti adatokat adja vissza. Ezt manuálisan is elvégezheti `byte []` , vagy a [`AudioDataStream`](/cpp/cognitive-services/speech/audiodatastream) osztály használatával kezelheti a memóriában lévő adatfolyamot. Ebben a példában a `AudioDataStream.FromResult()` statikus függvény használatával kap egy streamet az eredményből.

```cpp
void synthesizeSpeech() 
{
    auto config = SpeechConfig::FromSubscription("YourSubscriptionKey", "YourServiceRegion");
    auto synthesizer = SpeechSynthesizer::FromConfig(config, NULL);
    
    auto result = synthesizer->SpeakTextAsync("Getting the response as an in-memory stream.").get();
    auto stream = AudioDataStream::FromResult(result);
}
```

Innen bármilyen egyéni viselkedést alkalmazhat az eredményül kapott `stream` objektum használatával.

## <a name="customize-audio-format"></a>Hangformátum testreszabása

A következő szakasz bemutatja, hogyan szabhatja testre a hangkimeneti attribútumokat, beleértve a következőket:

* Hangfájl típusa
* Mintavételezési arány
* Kis mélység

A hangformátum módosításához használja a `SetSpeechSynthesisOutputFormat()` függvényt az `SpeechConfig` objektumon. Ez a függvény egy `enum` típust vár [`SpeechSynthesisOutputFormat`](/cpp/cognitive-services/speech/microsoft-cognitiveservices-speech-namespace#speechsynthesisoutputformat) , amelyet a kimeneti formátum kiválasztásához használ. A rendelkezésre álló [hangformátumok listáját](/cpp/cognitive-services/speech/microsoft-cognitiveservices-speech-namespace#speechsynthesisoutputformat) az útmutató dokumentációjában tekintheti meg.

A követelményektől függően különböző fájltípusok választhatók. Vegye figyelembe, hogy definíció szerint a nyers formátumok, például a nem `Raw24Khz16BitMonoPcm` tartalmaznak hangfejléceket. A nyers formátumok csak akkor használhatók, ha tudja, hogy az alsóbb rétegbeli implementáció dekódolást végez a nyers Bitstream, vagy ha manuálisan kívánja felépíteni a fejléceket a kis mélység, a mintavételezési arány, a csatornák száma stb. alapján.

Ebben a példában egy magas hűségű RIFF formátumot kell megadnia az `Riff24Khz16BitMonoPcm` objektumra vonatkozó beállítással `SpeechSynthesisOutputFormat` `SpeechConfig` . Az előző szakaszban szereplő példához hasonlóan a [`AudioDataStream`](/cpp/cognitive-services/speech/audiodatastream) segítségével beolvashatja az eredmény memóriában lévő adatfolyamát, majd megírhatja azt egy fájlba.

```cpp
void synthesizeSpeech() 
{
    auto config = SpeechConfig::FromSubscription("YourSubscriptionKey", "YourServiceRegion");
    config->SetSpeechSynthesisOutputFormat(SpeechSynthesisOutputFormat::Riff24Khz16BitMonoPcm);

    auto synthesizer = SpeechSynthesizer::FromConfig(config, NULL);
    auto result = synthesizer->SpeakTextAsync("A simple test to write to a file.").get();
    
    auto stream = AudioDataStream::FromResult(result);
    stream->SaveToWavFileAsync("path/to/write/file.wav").get();
}
```

A program ismételt futtatásakor a rendszer egy `.wav` fájlt ír a megadott elérési útra.

## <a name="use-ssml-to-customize-speech-characteristics"></a>A SSML használata a beszédfelismerési jellemzők testreszabásához

A Speech szintézis Markup Language (SSML) lehetővé teszi, hogy a kérések XML-sémából való elküldésével részletesen beállítsa a szöveg-beszéd kimenetet, a kiejtést, a beszéd sebességét, a kötetet és a többit. Ez a szakasz néhány hasznos példát mutat be, de részletesebb útmutatásért tekintse meg a [SSML ismertető cikket](../../../speech-synthesis-markup.md).

A SSML testreszabáshoz való használatának megkezdéséhez egy egyszerű módosítást hajt végre, amely átváltja a hangot.
Először hozzon létre egy új XML-fájlt a SSML config-hoz a legfelső szintű Project-címtárban, ebben a példában `ssml.xml` . A gyökérelem mindig `<speak>` , és egy elem szövegének körbefuttatása `<voice>` lehetővé teszi a hang módosítását a `name` param használatával. Ez a példa egy férfi angol (Egyesült Királysági) hangra vált. Vegye figyelembe, hogy ez a hang egy **szabványos** hang, amely különböző díjszabással és rendelkezésre állással rendelkezik, mint a **neurális** hangok. Tekintse meg a támogatott **standard** hangok [teljes listáját](../../../language-support.md#standard-voices) .

```xml
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
  <voice name="en-GB-George-Apollo">
    When you're on the motorway, it's a good idea to use a sat-nav.
  </voice>
</speak>
```

Ezután módosítania kell a beszédfelismerési kérést az XML-fájlra való hivatkozáshoz. A kérelem többnyire azonos, de a függvény használata helyett használja a parancsot `SpeakTextAsync()` `SpeakSsmlAsync()` . Ez a függvény egy XML-karakterláncot vár, így először be kell töltenie a SSML config karakterláncként. Innen az eredmény objektum pontosan megegyezik az előző példákkal.

```cpp
void synthesizeSpeech() 
{
    auto config = SpeechConfig::FromSubscription("YourSubscriptionKey", "YourServiceRegion");
    auto synthesizer = SpeechSynthesizer::FromConfig(config, NULL);
    
    std::ifstream file("./ssml.xml");
    std::string ssml, line;
    while (std::getline(file, line))
    {
        ssml += line;
        ssml.push_back('\n');
    }
    auto result = synthesizer->SpeakSsmlAsync(ssml).get();
    
    auto stream = AudioDataStream::FromResult(result);
    stream->SaveToWavFileAsync("path/to/write/file.wav").get();
}
```

A kimenet működik, de van néhány egyszerű további módosítás is, amely segít a természetesebb hangzásban. A teljes beszélő sebesség egy kicsit túl gyors, ezért felvesszük a `<prosody>` címkét, és az alapértelmezett sebesség **90%-ában** csökkentik a sebességet. Emellett a mondatban található vessző utáni szünet egy kicsit túl rövid és természetellenes hang. A probléma megoldásához vegyen fel egy `<break>` címkét a beszéd késleltetéséhez, és állítsa be az időparamétert a **200ms** értékre. Futtassa újra a szintézist, hogy megtekintse, hogy a testreszabások hogyan érintik a kimenetet.

```xml
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
  <voice name="en-GB-George-Apollo">
    <prosody rate="0.9">
      When you're on the motorway,<break time="200ms"/> it's a good idea to use a sat-nav.
    </prosody>
  </voice>
</speak>
```

## <a name="neural-voices"></a>Neurális hangok

A neurális hangok a mély neurális hálózatok által működtetett hangszintézisi algoritmusok. Neurális hang használatakor a szintetizált beszéd szinte nem különbözteti meg az emberi felvételeket. Az emberi jellegű természetes prosody és a szavak egyértelmű megfogalmazásával a neurális hangok jelentősen csökkentik a figyelési fáradtságot, amikor a felhasználók interakcióba lépnek az AI-rendszerekkel.

Ha egy neurális hangra szeretne váltani, módosítsa a `name` beállítást az egyik [neurális hanglehetőségre](../../../language-support.md#neural-voices). Ezután adjon hozzá egy XML-névteret a `mstts` elemhez, és szúrja be a szöveget a `<mstts:express-as>` címkébe. A `style` paraméter használatával testreszabhatja a beszéd stílusát. Ez a példa a következőt használja `cheerful` , de megpróbáljuk beállítani a vagy a értékre, `customerservice` `chat` hogy megtekintse a beszéd stílusának különbségét.

> [!IMPORTANT]
> A neurális hangokat **csak** az *USA keleti* régiójában, *Dél-Kelet-Ázsia* és *Nyugat-Európában* létrehozott beszédfelismerési erőforrások támogatják.

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xmlns:mstts="https://www.w3.org/2001/mstts" xml:lang="en-US">
  <voice name="en-US-AriaNeural">
    <mstts:express-as style="cheerful">
      This is awesome!
    </mstts:express-as>
  </voice>
</speak>
```