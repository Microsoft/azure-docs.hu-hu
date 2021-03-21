---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/25/2020
ms.author: trbye
ms.custom: devx-track-csharp
ms.openlocfilehash: b1161fdcbed7933c7a8dd0dccadd2e896966b728
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "104719629"
---
Ebben a rövid útmutatóban megtudhatja, hogyan végezheti el a szöveg-beszéd szintézist a Speech SDK használatával. Első lépésként alapkonfigurációt és szintézist kell elvégeznie, és az egyéni alkalmazások fejlesztéséhez további speciális példákra kell lépnie, például:

* Válaszok a memóriában tárolt adatfolyamok beolvasására
* A kimeneti mintavételezési arány és a bitsűrűség testreszabása
* Összegző kérelmek elküldése a SSML használatával (Speech szintézis Markup Language)
* Neurális hangok használata

## <a name="skip-to-samples-on-github"></a>Ugrás a mintákra a GitHubon

Ha közvetlenül a mintakód kihagyását szeretné kihagyni, tekintse meg a [C# gyors példákat](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/csharp/dotnet/text-to-speech) a githubon.

## <a name="prerequisites"></a>Előfeltételek

Ez a cikk azt feltételezi, hogy rendelkezik Azure-fiókkal és Speech Service-előfizetéssel. Ha nem rendelkezik fiókkal és előfizetéssel, [próbálja ki ingyenesen a Speech szolgáltatást](../../../overview.md#try-the-speech-service-for-free).

## <a name="install-the-speech-sdk"></a>A Speech SDK telepítése

Mielőtt bármit elvégezhet, telepítenie kell a Speech SDK-t. A platformtól függően kövesse az alábbi utasításokat:

* <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/setup-platform?tabs=dotnet&pivots=programming-language-csharp" target="_blank">.NET-keretrendszer </a>
* <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/setup-platform?tabs=dotnetcore&pivots=programming-language-csharp" target="_blank">.NET Core </a>
* <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/setup-platform?tabs=unity&pivots=programming-language-csharp" target="_blank">Unity </a>
* <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/setup-platform?tabs=uwps&pivots=programming-language-csharp" target="_blank">UWP </a>
* <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/setup-platform?tabs=xaml&pivots=programming-language-csharp" target="_blank">Xamarin </a>

## <a name="import-dependencies"></a>Függőségek importálása

A cikkben szereplő példák futtatásához foglalja bele a következő `using` utasításokat a szkript elejére.

```csharp
using System;
using System.IO;
using System.Text;
using System.Threading.Tasks;
using Microsoft.CognitiveServices.Speech;
using Microsoft.CognitiveServices.Speech.Audio;
```

## <a name="create-a-speech-configuration"></a>Beszédfelismerési konfiguráció létrehozása

A beszédfelismerési szolgáltatás a Speech SDK használatával történő meghívásához létre kell hoznia egy [`SpeechConfig`](/dotnet/api/microsoft.cognitiveservices.speech.speechconfig) . Ez az osztály az előfizetésével kapcsolatos információkat tartalmaz, például a kulcsot és a társított régiót, végpontot, gazdagépet vagy engedélyezési jogkivonatot.

> [!NOTE]
> Függetlenül attól, hogy elvégezte-e a beszédfelismerést, a beszédfelismerést, a fordítást vagy a szándék felismerését, mindig hozzon létre egy konfigurációt.

Az alábbiakat többféleképpen lehet inicializálni [`SpeechConfig`](/dotnet/api/microsoft.cognitiveservices.speech.speechconfig) :

* Előfizetéssel: adjon egy kulcsot és a hozzá tartozó régiót.
* Egy végponttal: pass a Speech Service-végponton. Kulcs-vagy engedélyezési jogkivonat nem kötelező.
* Gazdagép esetén: adjon meg egy gazdagép-címeket. Kulcs-vagy engedélyezési jogkivonat nem kötelező.
* Engedélyezési jogkivonattal: adjon meg egy engedélyezési jogkivonatot és a hozzá tartozó régiót.

Ebben a példában egy [`SpeechConfig`](/dotnet/api/microsoft.cognitiveservices.speech.speechconfig) előfizetési kulcsot és egy régiót hoz létre. Szerezze be ezeket a hitelesítő adatokat a [beszédfelismerési szolgáltatás ingyenes kipróbálásához](../../../overview.md#try-the-speech-service-for-free)szükséges lépések követésével. Emellett a cikk további részében is létrehozhat egy alapszintű, a különböző testreszabási beállításokkal módosítható egyszerű kiírási kódot.

```csharp
public class Program
{
    static async Task Main()
    {
        await SynthesizeAudioAsync();
    }

    static async Task SynthesizeAudioAsync()
    {
        var config = SpeechConfig.FromSubscription("YourSubscriptionKey", "YourServiceRegion");
    }
}
```

## <a name="synthesize-speech-to-a-file"></a>Beszéd készítése fájlba

Ezután létrehoz egy [`SpeechSynthesizer`](/dotnet/api/microsoft.cognitiveservices.speech.speechsynthesizer) objektumot, amely szöveg-beszéd átalakításokat és kimeneteket hajt végre a hangszórók, fájlok vagy más kimeneti adatfolyamok számára. Az [`SpeechSynthesizer`](/dotnet/api/microsoft.cognitiveservices.speech.speechsynthesizer) elfogadva paraméterként az [`SpeechConfig`](/dotnet/api/microsoft.cognitiveservices.speech.speechconfig) előző lépésben létrehozott objektumot, valamint egy objektumot, [`AudioConfig`](/dotnet/api/microsoft.cognitiveservices.speech.audio.audioconfig) amely meghatározza a kimeneti eredmények kezelését.

Az indításhoz hozzon létre egy `AudioConfig` fájlt, és a függvény használatával automatikusan írja be a kimenetet egy `.wav` fájlba, majd készítsen egy `FromWavFileOutput()` `using` utasítást egy utasítással. Az `using` ebben a kontextusban található utasítás automatikusan elveti a nem felügyelt erőforrásokat, és az objektum kivezetését követően kikerül a hatókörből.

```csharp
static async Task SynthesizeAudioAsync()
{
    var config = SpeechConfig.FromSubscription("YourSubscriptionKey", "YourServiceRegion");
    using var audioConfig = AudioConfig.FromWavFileOutput("path/to/write/file.wav");
}
```

Ezután hozza létre a példányt egy `SpeechSynthesizer` másik `using` utasítással. Adja át az `config` objektumot és az `audioConfig` objektumot paraméterként. Ezután a beszédfelismerés végrehajtása és a fájlba való írás olyan egyszerű, mintha `SpeakTextAsync()` egy szöveges karakterláncot futtasson.

```csharp
static async Task SynthesizeAudioAsync()
{
    var config = SpeechConfig.FromSubscription("YourSubscriptionKey", "YourServiceRegion");
    using var audioConfig = AudioConfig.FromWavFileOutput("path/to/write/file.wav");
    using var synthesizer = new SpeechSynthesizer(config, audioConfig);
    await synthesizer.SpeakTextAsync("A simple test to write to a file.");
}
```

Futtassa a programot, és a rendszer a `.wav` megadott helyre írja a szintetizált fájlt. Ez jó példa a legalapvetőbb használatra, de a következő lépés a kimenet testreszabása és a kimeneti válasz kezelése memóriában tárolt adatfolyamként az egyéni forgatókönyvek használata esetén.

## <a name="synthesize-to-speaker-output"></a>Szintetizálás a hangszórók kimenetére

Bizonyos esetekben érdemes közvetlenül a szintetizált beszédet közvetlenül a beszélőhöz adni. Ehhez hagyja ki a `AudioConfig` paramétert a fenti példában szereplő érték létrehozásakor `SpeechSynthesizer` . Ez a kimenet a jelenlegi aktív kimeneti eszközre mutat.

```csharp
static async Task SynthesizeAudioAsync()
{
    var config = SpeechConfig.FromSubscription("YourSubscriptionKey", "YourServiceRegion");
    using var synthesizer = new SpeechSynthesizer(config);
    await synthesizer.SpeakTextAsync("Synthesizing directly to speaker output.");
}
```

## <a name="get-result-as-an-in-memory-stream"></a>Eredmény beolvasása memóriabeli adatfolyamként

A beszédfelismerés számos forgatókönyve esetében valószínű, hogy az eredményül kapott hangadatokat memórián belüli adatfolyamként kell megadnia, nem pedig közvetlenül egy fájlba írni. Ez lehetővé teszi, hogy egyéni viselkedést hozzon létre, beleértve a következőket:

* Az eredményül kapott bájtos tömböt az egyéni alsóbb rétegbeli szolgáltatásokhoz tartozó, keresési lehetőségként szolgáló adatfolyamként elvonta
* Az eredmény integrálása más API-k vagy szolgáltatások használatával.
* A hangadatok módosítása, egyéni `.wav` fejlécek írása stb.

Ezt a változást egyszerűen elvégezheti az előző példából. Először távolítsa el a `AudioConfig` blokkot, mivel a kimeneti viselkedést ettől kezdve manuálisan fogja kezelni a jobb szabályozás érdekében. Ezután adja át a-t a `null` `AudioConfig` `SpeechSynthesizer` konstruktorban.

> [!NOTE]
> `null`A ( `AudioConfig` z) helyett, ahelyett, hogy kihagyja, mint a fenti hangsugárzó-kimeneti példában, a nem játssza le alapértelmezés szerint a hangot a jelenlegi aktív kimeneti eszközön.

Ezúttal egy változóba menti az eredményt [`SpeechSynthesisResult`](/dotnet/api/microsoft.cognitiveservices.speech.speechsynthesisresult) . A `AudioData` tulajdonság tartalmazza a `byte []` kimeneti adatokat. Ezt manuálisan is elvégezheti `byte []` , vagy a [`AudioDataStream`](/dotnet/api/microsoft.cognitiveservices.speech.audiodatastream) osztály használatával kezelheti a memóriában lévő adatfolyamot. Ebben a példában a `AudioDataStream.FromResult()` statikus függvény használatával kap egy streamet az eredményből.

```csharp
static async Task SynthesizeAudioAsync()
{
    var config = SpeechConfig.FromSubscription("YourSubscriptionKey", "YourServiceRegion");
    using var synthesizer = new SpeechSynthesizer(config, null);

    var result = await synthesizer.SpeakTextAsync("Getting the response as an in-memory stream.");
    using var stream = AudioDataStream.FromResult(result);
}
```

Innen bármilyen egyéni viselkedést alkalmazhat az eredményül kapott `stream` objektum használatával.

## <a name="customize-audio-format"></a>Hangformátum testreszabása

A következő szakasz bemutatja, hogyan szabhatja testre a hangkimeneti attribútumokat, beleértve a következőket:

* Hangfájl típusa
* Mintavételezési arány
* Kis mélység

A hangformátum módosításához használja a `SetSpeechSynthesisOutputFormat()` függvényt az `SpeechConfig` objektumon. Ez a függvény egy `enum` típust vár [`SpeechSynthesisOutputFormat`](/dotnet/api/microsoft.cognitiveservices.speech.speechsynthesisoutputformat) , amelyet a kimeneti formátum kiválasztásához használ. A rendelkezésre álló [hangformátumok listáját](/dotnet/api/microsoft.cognitiveservices.speech.speechsynthesisoutputformat) az útmutató dokumentációjában tekintheti meg.

A követelményektől függően különböző fájltípusok választhatók. Vegye figyelembe, hogy definíció szerint a nyers formátumok, például a nem `Raw24Khz16BitMonoPcm` tartalmaznak hangfejléceket. A nyers formátumok csak akkor használhatók, ha tudja, hogy az alsóbb rétegbeli implementáció dekódolást végez a nyers Bitstream, vagy ha manuálisan kívánja felépíteni a fejléceket a kis mélység, a mintavételezési arány, a csatornák száma stb. alapján.

> [!NOTE]
> Az **en-us-AriaRUS** és az **en-us-GuyRUS** hangok a `Riff24Khz16BitMonoPcm` mintavételezési arányban kódolt mintákból jönnek létre.

Ebben a példában egy magas hűségű RIFF formátumot kell megadnia az `Riff24Khz16BitMonoPcm` objektumra vonatkozó beállítással `SpeechSynthesisOutputFormat` `SpeechConfig` . Az előző szakaszban szereplő példához hasonlóan a [`AudioDataStream`](/dotnet/api/microsoft.cognitiveservices.speech.audiodatastream) segítségével beolvashatja az eredmény memóriában lévő adatfolyamát, majd megírhatja azt egy fájlba.

```csharp
static async Task SynthesizeAudioAsync()
{
    var config = SpeechConfig.FromSubscription("YourSubscriptionKey", "YourServiceRegion");
    config.SetSpeechSynthesisOutputFormat(SpeechSynthesisOutputFormat.Riff24Khz16BitMonoPcm);

    using var synthesizer = new SpeechSynthesizer(config, null);
    var result = await synthesizer.SpeakTextAsync("Customizing audio output format.");

    using var stream = AudioDataStream.FromResult(result);
    await stream.SaveToWaveFileAsync("path/to/write/file.wav");
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

Ezután módosítania kell a beszédfelismerési kérést az XML-fájlra való hivatkozáshoz. A kérelem többnyire azonos, de a függvény használata helyett használja a parancsot `SpeakTextAsync()` `SpeakSsmlAsync()` . Ez a függvény egy XML-karakterláncot vár, így először be kell töltenie a SSML-konfigurációt karakterláncként a használatával `File.ReadAllText()` . Innen az eredmény objektum pontosan megegyezik az előző példákkal.

> [!NOTE]
> Ha a Visual studiót használja, a Build-konfiguráció valószínűleg nem fogja megkeresni az XML-fájlt alapértelmezetten. A probléma megoldásához kattintson a jobb gombbal az XML-fájlra, és válassza a **Tulajdonságok** lehetőséget. Változtassa meg a **Build műveletet** a *tartalomra*, és a másolás **a kimeneti könyvtárba** a *mindig* elemre.

```csharp
public static async Task SynthesizeAudioAsync()
{
    var config = SpeechConfig.FromSubscription("YourSubscriptionKey", "YourServiceRegion");
    using var synthesizer = new SpeechSynthesizer(config, null);

    var ssml = File.ReadAllText("./ssml.xml");
    var result = await synthesizer.SpeakSsmlAsync(ssml);

    using var stream = AudioDataStream.FromResult(result);
    await stream.SaveToWaveFileAsync("path/to/write/file.wav");
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
## <a name="get-facial-pose-events"></a>Arc póz-események beolvasása

A beszédfelismerés jó módszer lehet az arc-kifejezések animálására.
A [visemes](../../../how-to-speech-synthesis-viseme.md) gyakran a megfigyelt beszédben jelentenek, például az ajkak, az állkapocs és a nyelv pozíciója egy adott fonéma készítésekor.
Feliratkozhat a viseme eseményre a Speech SDK-ban.
Ezt követően alkalmazhatja a viseme eseményeket, így animálhatja a karakterek színeit beszéd hanglejátszásként.
Ismerje meg [, hogyan szerezhet be viseme-eseményeket](../../../how-to-speech-synthesis-viseme.md#get-viseme-events-with-the-speech-sdk).
