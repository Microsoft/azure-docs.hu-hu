---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/25/2020
ms.custom: devx-track-java
ms.author: trbye
ms.openlocfilehash: cb1c6588dc6c770c809b786982ece1c9f0b1b1b5
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/08/2021
ms.locfileid: "107108927"
---
Ebben a rövid útmutatóban megtudhatja, hogyan végezheti el a szöveg-beszéd szintézist a Speech SDK használatával. Első lépésként alapkonfigurációt és szintézist kell elvégeznie, és az egyéni alkalmazások fejlesztéséhez további speciális példákra kell lépnie, például:

* Válaszok a memóriában tárolt adatfolyamok beolvasására
* A kimeneti mintavételezési arány és a bitsűrűség testreszabása
* Összegző kérelmek elküldése a SSML használatával (Speech szintézis Markup Language)
* Neurális hangok használata

## <a name="skip-to-samples-on-github"></a>Ugrás a mintákra a GitHubon

Ha egyenesen a mintakód kihagyása mellett szeretne kiugrani, tekintse meg a Java-gyors üzembe helyezési [mintákat](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/java/jre/text-to-speech) a githubon.

## <a name="prerequisites"></a>Előfeltételek

Ez a cikk azt feltételezi, hogy rendelkezik Azure-fiókkal és Speech Service-előfizetéssel. Ha nem rendelkezik fiókkal és előfizetéssel, [próbálja ki ingyenesen a Speech szolgáltatást](../../../overview.md#try-the-speech-service-for-free).

## <a name="install-the-speech-sdk"></a>A Speech SDK telepítése

Mielőtt bármit elvégezhet, telepítenie kell a Speech SDK-t. A platformtól függően kövesse az alábbi utasításokat:

* <a href="/azure/cognitive-services/speech-service/quickstarts/setup-platform?pivots=programming-language-java&tabs=jre" target="_blank">Java-futtatókörnyezet </a>
* <a href="/azure/cognitive-services/speech-service/quickstarts/setup-platform?pivots=programming-language-java&tabs=android" target="_blank">Android </a>

## <a name="import-dependencies"></a>Függőségek importálása

A cikkben szereplő példák futtatásához adja meg a következő importálási utasításokat a parancsfájl elején.

```java
import com.microsoft.cognitiveservices.speech.AudioDataStream;
import com.microsoft.cognitiveservices.speech.SpeechConfig;
import com.microsoft.cognitiveservices.speech.SpeechSynthesizer;
import com.microsoft.cognitiveservices.speech.SpeechSynthesisOutputFormat;
import com.microsoft.cognitiveservices.speech.SpeechSynthesisResult;
import com.microsoft.cognitiveservices.speech.audio.AudioConfig;

import java.io.*;
import java.util.Scanner;
```

## <a name="create-a-speech-configuration"></a>Beszédfelismerési konfiguráció létrehozása

A beszédfelismerési szolgáltatás a Speech SDK használatával történő meghívásához létre kell hoznia egy [`SpeechConfig`](/java/api/com.microsoft.cognitiveservices.speech.speechconfig) . Ez az osztály az előfizetésével kapcsolatos információkat tartalmaz, például a kulcsot és a társított régiót, végpontot, gazdagépet vagy engedélyezési jogkivonatot.

> [!NOTE]
> Függetlenül attól, hogy elvégezte-e a beszédfelismerést, a beszédfelismerést, a fordítást vagy a szándék felismerését, mindig hozzon létre egy konfigurációt.

Az alábbiakat többféleképpen lehet inicializálni [`SpeechConfig`](/java/api/com.microsoft.cognitiveservices.speech.speechconfig) :

* Előfizetéssel: adjon egy kulcsot és a hozzá tartozó régiót.
* Egy végponttal: pass a Speech Service-végponton. Kulcs-vagy engedélyezési jogkivonat nem kötelező.
* Gazdagép esetén: adjon meg egy gazdagép-címeket. Kulcs-vagy engedélyezési jogkivonat nem kötelező.
* Engedélyezési jogkivonattal: adjon meg egy engedélyezési jogkivonatot és a hozzá tartozó régiót.

Ebben a példában egy [`SpeechConfig`](/java/api/com.microsoft.cognitiveservices.speech.speechconfig) előfizetési kulcsot és egy régiót hoz létre. Szerezze be ezeket a hitelesítő adatokat a [beszédfelismerési szolgáltatás ingyenes kipróbálásához](../../../overview.md#try-the-speech-service-for-free)szükséges lépések követésével. Emellett a cikk további részében is létrehozhat egy alapszintű, a különböző testreszabási beállításokkal módosítható egyszerű kiírási kódot.

```java
public class Program
{
    public static void main(String[] args) {
        SpeechConfig speechConfig = SpeechConfig.fromSubscription("YourSubscriptionKey", "YourServiceRegion");
    }
}
```

## <a name="synthesize-speech-to-a-file"></a>Beszéd készítése fájlba

Ezután létrehoz egy [`SpeechSynthesizer`](/java/api/com.microsoft.cognitiveservices.speech.speechsynthesizer) objektumot, amely szöveg-beszéd átalakításokat és kimeneteket hajt végre a hangszórók, fájlok vagy más kimeneti adatfolyamok számára. Az [`SpeechSynthesizer`](/java/api/com.microsoft.cognitiveservices.speech.speechsynthesizer) elfogadva paraméterként az [`SpeechConfig`](/java/api/com.microsoft.cognitiveservices.speech.speechconfig) előző lépésben létrehozott objektumot, valamint egy objektumot, [`AudioConfig`](/java/api/com.microsoft.cognitiveservices.speech.audio.audioconfig) amely meghatározza a kimeneti eredmények kezelését.

Az indításhoz hozzon létre egy `AudioConfig` fájlt, amely automatikusan a kimenetet egy fájlba írja a `.wav` `fromWavFileOutput()` statikus függvény használatával.

```java
public static void main(String[] args) {
    SpeechConfig speechConfig = SpeechConfig.fromSubscription("YourSubscriptionKey", "YourServiceRegion");
    AudioConfig audioConfig = AudioConfig.fromWavFileOutput("path/to/write/file.wav");
}
```

Ezután hozza létre az `SpeechSynthesizer` `speechConfig` objektumot és az `audioConfig` objektumot paraméterként. Ezután a beszédfelismerés végrehajtása és a fájlba való írás olyan egyszerű, mintha `SpeakText()` egy szöveges karakterláncot futtasson.

```java
public static void main(String[] args) {
    SpeechConfig speechConfig = SpeechConfig.fromSubscription("YourSubscriptionKey", "YourServiceRegion");
    AudioConfig audioConfig = AudioConfig.fromWavFileOutput("path/to/write/file.wav");

    SpeechSynthesizer synthesizer = new SpeechSynthesizer(speechConfig, audioConfig);
    synthesizer.SpeakText("A simple test to write to a file.");
}
```

Futtassa a programot, és a rendszer a `.wav` megadott helyre írja a szintetizált fájlt. Ez jó példa a legalapvetőbb használatra, de a következő lépés a kimenet testreszabása és a kimeneti válasz kezelése memóriában tárolt adatfolyamként az egyéni forgatókönyvek használata esetén.

## <a name="synthesize-to-speaker-output"></a>Szintetizálás a hangszórók kimenetére

Bizonyos esetekben érdemes közvetlenül a szintetizált beszédet közvetlenül a beszélőhöz adni. Ehhez hozza létre a `AudioConfig` `fromDefaultSpeakerOutput()` statikus függvény használatával. Ez a kimenet az aktuális aktív kimeneti eszközre mutat.

```java
public static void main(String[] args) {
    SpeechConfig speechConfig = SpeechConfig.fromSubscription("YourSubscriptionKey", "YourServiceRegion");
    AudioConfig audioConfig = AudioConfig.fromDefaultSpeakerOutput();

    SpeechSynthesizer synthesizer = new SpeechSynthesizer(speechConfig, audioConfig);
    synthesizer.SpeakText("Synthesizing directly to speaker output.");
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

Ezúttal egy változóba menti az eredményt [`SpeechSynthesisResult`](/java/api/com.microsoft.cognitiveservices.speech.speechsynthesisresult) . A `SpeechSynthesisResult.getAudioData()` függvény a `byte []` kimeneti adatokat adja vissza. Ezt manuálisan is elvégezheti `byte []` , vagy a [`AudioDataStream`](/java/api/com.microsoft.cognitiveservices.speech.audiodatastream) osztály használatával kezelheti a memóriában lévő adatfolyamot. Ebben a példában a `AudioDataStream.fromResult()` statikus függvény használatával kap egy streamet az eredményből.

```java
public static void main(String[] args) {
    SpeechConfig speechConfig = SpeechConfig.fromSubscription("YourSubscriptionKey", "YourServiceRegion");
    SpeechSynthesizer synthesizer = new SpeechSynthesizer(speechConfig, null);

    SpeechSynthesisResult result = synthesizer.SpeakText("Getting the response as an in-memory stream.");
    AudioDataStream stream = AudioDataStream.fromResult(result);
    System.out.print(stream.getStatus());
}
```

Innen bármilyen egyéni viselkedést alkalmazhat az eredményül kapott `stream` objektum használatával.

## <a name="customize-audio-format"></a>Hangformátum testreszabása

A következő szakasz bemutatja, hogyan szabhatja testre a hangkimeneti attribútumokat, beleértve a következőket:

* Hangfájl típusa
* Mintavételezési arány
* Kis mélység

A hangformátum módosításához használja a `setSpeechSynthesisOutputFormat()` függvényt az `SpeechConfig` objektumon. Ez a függvény egy `enum` típust vár [`SpeechSynthesisOutputFormat`](/java/api/com.microsoft.cognitiveservices.speech.speechsynthesisoutputformat) , amelyet a kimeneti formátum kiválasztásához használ. A rendelkezésre álló [hangformátumok listáját](/dotnet/api/microsoft.cognitiveservices.speech.speechsynthesisoutputformat) az útmutató dokumentációjában tekintheti meg.

A követelményektől függően különböző fájltípusok választhatók. Vegye figyelembe, hogy definíció szerint a nyers formátumok, például a nem `Raw24Khz16BitMonoPcm` tartalmaznak hangfejléceket. A nyers formátumok csak akkor használhatók, ha tudja, hogy az alsóbb rétegbeli implementáció dekódolást végez a nyers Bitstream, vagy ha manuálisan kívánja felépíteni a fejléceket a kis mélység, a mintavételezési arány, a csatornák száma stb. alapján.

Ebben a példában egy magas hűségű RIFF formátumot kell megadnia az `Riff24Khz16BitMonoPcm` objektumra vonatkozó beállítással `SpeechSynthesisOutputFormat` `SpeechConfig` . Az előző szakaszban szereplő példához hasonlóan a [`AudioDataStream`](/java/api/com.microsoft.cognitiveservices.speech.audiodatastream) segítségével beolvashatja az eredmény memóriában lévő adatfolyamát, majd megírhatja azt egy fájlba.

```java
public static void main(String[] args) {
    SpeechConfig speechConfig = SpeechConfig.fromSubscription("YourSubscriptionKey", "YourServiceRegion");

    // set the output format
    speechConfig.setSpeechSynthesisOutputFormat(SpeechSynthesisOutputFormat.Riff24Khz16BitMonoPcm);

    SpeechSynthesizer synthesizer = new SpeechSynthesizer(speechConfig, null);
    SpeechSynthesisResult result = synthesizer.SpeakText("Customizing audio output format.");
    AudioDataStream stream = AudioDataStream.fromResult(result);
    stream.saveToWavFile("path/to/write/file.wav");
}
```

A program ismételt futtatásakor a rendszer egy `.wav` fájlt ír a megadott elérési útra.

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

Ezután módosítania kell a beszédfelismerési kérést az XML-fájlra való hivatkozáshoz. A kérelem többnyire azonos, de a függvény használata helyett használja a parancsot `SpeakText()` `SpeakSsml()` . Ez a függvény egy XML-karakterláncot vár, ezért először létre kell hoznia egy függvényt egy XML-fájl betöltéséhez, és karakterláncként kell visszaadnia.

```java
private static String xmlToString(String filePath) {
    File file = new File(filePath);
    StringBuilder fileContents = new StringBuilder((int)file.length());

    try (Scanner scanner = new Scanner(file)) {
        while(scanner.hasNextLine()) {
            fileContents.append(scanner.nextLine() + System.lineSeparator());
        }
        return fileContents.toString().trim();
    } catch (FileNotFoundException ex) {
        return "File not found.";
    }
}
```

Innen az eredmény objektum pontosan megegyezik az előző példákkal.

```java
public static void main(String[] args) {
    SpeechConfig speechConfig = SpeechConfig.fromSubscription("YourSubscriptionKey", "YourServiceRegion");
    SpeechSynthesizer synthesizer = new SpeechSynthesizer(speechConfig, null);

    String ssml = xmlToString("ssml.xml");
    SpeechSynthesisResult result = synthesizer.SpeakSsml(ssml);
    AudioDataStream stream = AudioDataStream.fromResult(result);
    stream.saveToWavFile("path/to/write/file.wav");
}
```

> [!NOTE]
> Ha a SSML használata nélkül szeretné módosítani a hangot, beállíthatja a tulajdonságot a következő `SpeechConfig` használatával: `SpeechConfig.setSpeechSynthesisVoiceName("en-US-AriaNeural");`

## <a name="get-facial-pose-events"></a>Arc póz-események beolvasása

A beszédfelismerés jó módszer lehet az arc-kifejezések animálására.
A [visemes](../../../how-to-speech-synthesis-viseme.md) gyakran a megfigyelt beszédben jelentenek, például az ajkak, az állkapocs és a nyelv pozíciója egy adott fonéma készítésekor.
Előfizethet a Speech SDK viseme eseményeire, és az arc-animálási adatként beolvashatja az adott karaktert az arc animálása során.
Ismerje meg [, hogyan szerezhet be viseme-eseményeket](../../../how-to-speech-synthesis-viseme.md#get-viseme-events-with-the-speech-sdk).
