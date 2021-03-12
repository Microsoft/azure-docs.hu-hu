---
title: Speech Service-tárolók – gyakori kérdések (GYIK)
titleSuffix: Azure Cognitive Services
description: Beszédfelismerési tárolók telepítése és futtatása. a beszéd-szöveg szöveggé átmásolja a hangadatfolyamokat valós időben a szöveggé, hogy alkalmazásai, eszközei vagy eszközei képesek legyenek a felhasználásra vagy a megjelenítésre. A szöveg és a beszéd szöveggé alakítja át a bemeneti szöveget az emberi, például a szintetizált beszédbe.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/11/2021
ms.author: trbye
ms.custom: devx-track-csharp
ms.openlocfilehash: 86c8943531171094600bc7d93b8694bdd1c6e051
ms.sourcegitcommit: 94c3c1be6bc17403adbb2bab6bbaf4a717a66009
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/12/2021
ms.locfileid: "103225805"
---
# <a name="speech-service-containers-frequently-asked-questions-faq"></a>Speech Service-tárolók – gyakori kérdések (GYIK)

Ha a beszédfelismerési szolgáltatást tárolókkal használja, a támogatás megkezdése előtt támaszkodjon a gyakori kérdések gyűjteményére. Ez a cikk az általánostól a műszaki feladatoktól eltérő mértékben rögzíti a kérdéseket. A válasz kibontásához kattintson a kérdésre.

## <a name="general-questions"></a>Általános kérdések

<details>
<summary>
<b>Hogyan működnek a beszédfelismerési tárolók, és hogyan állíthatom be őket?</b>
</summary>

**Válasz:** Az éles fürt beállításakor több szempontot is figyelembe kell venni. Először is, ha egyetlen nyelvet, több tárolót állítanak be ugyanazon a gépen, nem lehet nagy probléma. Ha problémákat tapasztal, lehet, hogy hardverrel kapcsolatos probléma van – ezért először tekintse meg az erőforrást, azaz a következőt:. CPU-és memória-specifikációk.

Vegyünk egy pillanatra, a `ja-JP` tárolót és a legújabb modellt. Az akusztikus modell a legigényesebb CPU-Wise, míg a nyelvi modell a legtöbb memóriát igényli. A használat mérése után a 0,6 CPU-magok használatával egyetlen beszéd-szöveges kérést dolgoz fel, amikor a hang valós időben áramlik be (például a mikrofonból). Ha a valós idejű (például egy fájlból származó) hanganyagot, a használat kétszeres (1,2 x mag) lehet. Eközben az alább felsorolt memória a dekódoláshoz használt memória. *Nem* veszi figyelembe a nyelvi modell tényleges teljes méretét, amely a fájl-gyorsítótárban fog megjelenni. Ez `ja-JP` egy további 2 GB; a esetében `en-US` több is lehet (6-7 GB).

Ha olyan géppel rendelkezik, ahol kevés a memória, és több nyelvet próbál telepíteni rajta, lehetséges, hogy a fájl-gyorsítótár megtelt, és az operációs rendszer a modellek be-és kikapcsolására kényszerül. Egy futó átíráshoz, amely katasztrofális lehet, és a teljesítménnyel kapcsolatos egyéb következményekkel járhat.

Emellett a [speciális vektoros kiterjesztésű (AVX2)](speech-container-howto.md#advanced-vector-extension-support) utasításkészlet esetén a gépek előzetes csomagjait is megadhatja. Ahhoz, hogy a AVX512-utasításhoz beállított gép megkövetelje a kód generálását, a 10 nyelvhez tartozó 10 tároló indítása pedig átmenetileg kimerítheti a CPU-t. Az ehhez hasonló üzenet a Docker naplóiban jelenik meg:

```console
2020-01-16 16:46:54.981118943 
[W:onnxruntime:Default, tvm_utils.cc:276 LoadTVMPackedFuncFromCache]
Cannot find Scan4_llvm__mcpu_skylake_avx512 in cache, using JIT...
```

Megadhatja, hogy hány dekódert szeretne egy *adott* tárolóban a `DECODER MAX_COUNT` változó használatával. Tehát alapvetően az SKU-t kell kezdenie (CPU/memória), és javaslatot teszünk arra, hogyan érheti el a legjobbat. A jó kiindulási pont a gazdagépek ajánlott erőforrás-specifikációit jelöli.

<br>
</details>

<details>
<summary>
<b>Segíthet a kapacitás megtervezésében és a helyszíni beszéd-szöveg típusú tárolók költséghatékonyságán?</b>
</summary>

**Válasz:** Ha a tároló kapacitása kötegelt feldolgozási módban van, az egyes dekóderek valós időben 2 – 3x-ot tudnak kezelni, két CPU-maggal egyetlen felismeréshez. A Container-példányok esetében a kettőnél több párhuzamos felismerést nem javasoljuk, de a tárolók több példányának futtatását javasoljuk a megbízhatósági és rendelkezésre állási okokból, a terheléselosztó mögött.

Bár az egyes tárolók példányai több dekóderrel is futhatnak. Előfordulhat például, hogy egy 8 magos gépen (a kettőnél több, mint 2x) a 15x átviteli sebességet adja meg. A következő paramétert `DECODER_MAX_COUNT` kell figyelembe venni:. Szélsőséges esetben a megbízhatósági és a késési problémák merülnek fel, az átviteli sebesség pedig jelentősen megnő. Mikrofon esetén a szolgáltatás 1x valós időben fog futni. Az általános használatnak körülbelül egy mag kell lennie egyetlen felismeréshez.

Az 1 K/nap kötegelt feldolgozási módban való feldolgozásának forgatókönyve extrém esetben 3 virtuális gép, amely 24 órán belül, de nem garantált módon kezelhető. A nyárs napok, a feladatátvétel, a frissítés és a minimális biztonsági mentés/BCP biztosításához a 4-5-es gépeket ajánlott 3/fürt helyett 2 + fürttel.

A hardver esetében a standard szintű Azure-beli virtuális gépeket használjuk `DS13_v2` referenciáként (minden mag 2,6 GHz-es vagy jobb, a AVX2 utasításkészlet engedélyezve van).

| Példány  | vCPU (ok) | RAM    | Ideiglenes tároló | Utólagos elszámolás a AHB | 1 éves tartalék AHB (%-os megtakarítás) | 3 éves foglalás AHB (%-os megtakarítás) |
|-----------|---------|--------|--------------|------------------------|-------------------------------------|--------------------------------------|
| `DS13 v2` | 8       | 56 GiB | 112 GiB      | $0.598/óra            | $0.3528/óra (~ 41%)                 | $0.2333/óra (~ 61%)                  |

A kialakítási hivatkozás alapján (két, 5 virtuális gép 1 – 7 óra/nap hangkötegelt feldolgozás kezelésére) az 1 éves hardveres díj a következő lesz:

> 2 (fürtök) * 5 (virtuális gépek/fürt) * $0.3528/óra * 365 (nap) * 24 (óra) = $31K/év

Fizikai géphez való leképezéskor az általános becslés 1 vCPU = 1 fizikai CPU-mag. A valóságban a 1vCPU erősebb, mint egyetlen mag.

A helyszíni használatra ezen további tényezők az alábbiak:

- Milyen típusú fizikai CPU-t és hány magot
- Hány CPU fut egyszerre ugyanazon a dobozon/gépen
- A virtuális gépek beállítása
- A Hyper-Threading/többszálú használat használata
- A memória megosztása
- Az operációs rendszer stb.

Általában nem az Azure-környezetnek megfelelően van beállítva. Más terhek esetén azt mondanám, hogy a biztonságos becslés 10 fizikai CPU-mag = 8 Azure-vCPU. Bár a népszerű processzorok csak nyolc maggal rendelkeznek. A helyszíni üzemelő példányok esetében a Cost magasabb lesz, mint az Azure-beli virtuális gépek használata. Vegye figyelembe az értékcsökkenési arányt is.

A szolgáltatás díja megegyezik az online szolgáltatással: $1/óra a beszéd és a szöveg között. A beszédfelismerési szolgáltatás díja:

> $1 * 1000 * 365 = $365K

A Microsoft részére fizetett karbantartási díj a szolgáltatás szintjétől és tartalmától függ. Ez több, mint $29.99/hó alapszintű, akár több százezer, ha helyszíni szolgáltatás. A durva szám $300/óra a szolgáltatáshoz/karbantartáshoz. A felhasználók nem tartalmazzák a költségeket. Az infrastruktúra egyéb költségei (például a tárolás, a hálózatok és a terheléselosztó) nem tartoznak ide.

<br>
</details>

<details>
<summary>
<b>Miért hiányzik a központozás az átiratból?</b>
</summary>

**Válasz:** Az `speech_recognition_language=<YOUR_LANGUAGE>` explicit módon be kell állítani a kérést, ha a szén-ügyfelet használják.

Például:

```python
if not recognize_once(
    speechsdk.SpeechRecognizer(
        speech_config=speechsdk.SpeechConfig(
            endpoint=template.format("interactive"),
            speech_recognition_language="ja-JP"),
            audio_config=audio_config)):

    print("Failed interactive endpoint")
    exit(1)
```
A kimenet itt látható:

```cmd
RECOGNIZED: SpeechRecognitionResult(
    result_id=2111117c8700404a84f521b7b805c4e7, 
    text="まだ早いまだ早いは猫である名前はまだないどこで生まれたかとんと見当を検討をなつかぬ。
    何でも薄暗いじめじめした所でながら泣いていた事だけは記憶している。
    まだは今ここで初めて人間と言うものを見た。
    しかも後で聞くと、それは書生という人間中で一番同額同額。",
    reason=ResultReason.RecognizedSpeech)
```

<br>
</details>

<details>
<summary>
<b>Használhatok egyéni akusztikus modellt és nyelvi modellt a Speech Container használatával?</b>
</summary>

Jelenleg csak egy modell-azonosítót lehet átadni, vagy egyéni nyelvi modellt vagy egyéni akusztikus modellt.

**Válasz:** Az akusztikai és nyelvi modelleket *nem* támogató döntés párhuzamosan történt. Ez továbbra is érvényben marad, amíg létre nem jön egy egységes azonosító az API-szünetek csökkentése érdekében. Így sajnos ez jelenleg nem támogatott.

<br>
</details>

<details>
<summary>
<b>Meg tudná magyarázni ezeket a hibákat az egyéni beszéd – szöveg típusú tárolóból?</b>
</summary>

**1. hiba:**

```cmd
Failed to fetch manifest: Status: 400 Bad Request Body:
{
    "code": "InvalidModel",
    "message": "The specified model is not supported for endpoint manifests."
}
```

**1. Válasz:** Ha a legújabb egyéni modellel dolgozik, jelenleg nem támogatott. Ha egy régebbi verzióval rendelkezik, a használata is lehetséges. Továbbra is dolgozunk a legújabb verziók támogatásán.

Az egyéni tárolók alapvetően nem támogatják a halogén vagy ONNX-alapú akusztikus modelleket (ez az alapértelmezett az egyéni képzési portálon). Ennek oka az, hogy az egyéni modellek nincsenek titkosítva, és nem szeretnénk kitenni a ONNX-modelleket, azonban a nyelvi modellek rendben vannak. Az ügyfélnek explicit módon ki kell választania egy régebbi, nem ONNX modellt az egyéni képzéshez. A pontosságot nem érinti a rendszer. A modell mérete nagyobb lehet (100 MB).

> Támogatási modell > 20190220 (v 4.5 Unified)

**2. hiba:**

```cmd
HTTPAPI result code = HTTPAPI_OK.
HTTP status code = 400.
Reason:  Synthesis failed.
StatusCode: InvalidArgument,
Details: Voice does not match.
```

**2. Válasz:** Meg kell adnia a megfelelő hangnevet a kérelemben, amely megkülönbözteti a kis-és nagybetűket. Tekintse át a teljes szolgáltatásnév leképezését.

**3. hiba:**

```json
{
    "code": "InvalidProductId",
    "message": "The subscription SKU \"CognitiveServices.S0\" is not supported in this service instance."
}
```

**3. Válasz:** A rendszer nem Cognitive Services erőforrást hoz létre a beszédfelismerési erőforrás létrehozásához.


<br>
</details>

<details>
<summary>
<b>Milyen API-protokollok támogatottak, a REST vagy a WS?</b>
</summary>

**Válasz:** A beszéd-szöveg és az egyéni beszéd-szöveg tárolók esetében jelenleg csak a WebSocket-alapú protokollt támogatjuk. Az SDK csak a WS-ben, de nem REST-ben történő hívást támogatja. A REST-támogatás hozzáadására van lehetőség, de jelenleg nem az ETA. Mindig tekintse meg a hivatalos dokumentációt, lásd: [lekérdezés-előrejelzési végpontok](speech-container-howto.md#query-the-containers-prediction-endpoint).

<br>
</details>

<details>
<summary>
<b>A CentOS támogatja a Speech containers szolgáltatást?</b>
</summary>

**Válasz:** A Python SDK még nem támogatja a CentOS 7 használatát, de az Ubuntu 19,04 is nem támogatott.

A Python Speech SDK csomag elérhető a következő operációs rendszerekhez:
- **Windows** -x64 és x86
- **Mac** – macOS X 10,12 vagy újabb verzió
- **Linux** -Ubuntu 16,04, Ubuntu 18,04, Debian 9 x64 rendszeren

A környezet beállításával kapcsolatos további információkért lásd a [Python-platform beállítása](quickstarts/setup-platform.md?pivots=programming-language-python)című témakört. Jelenleg az Ubuntu 18,04 az ajánlott verzió.

<br>
</details>

<details>
<summary>
<b>Miért kapok hibaüzeneteket a LUIS előrejelzési végpontok meghívására tett kísérlet során?</b>
</summary>

A LUIS-tárolót egy IoT Edge üzemelő példányban használom, és megpróbálom meghívni a LUIS előrejelzési végpontot egy másik tárolóból. A LUIS-tároló figyeli a 5001-es portot, és a használt URL-cím a következő:

```csharp
var luisEndpoint =
    $"ws://192.168.1.91:5001/luis/prediction/v3.0/apps/{luisAppId}/slots/production/predict";
var config = SpeechConfig.FromEndpoint(new Uri(luisEndpoint));
```

A következő hibaüzenetet kapok:

```cmd
WebSocket Upgrade failed with HTTP status code: 404 SessionId: 3cfe2509ef4e49919e594abf639ccfeb
```

A kérelem a LUIS Container logs-ben jelenik meg, és az üzenet a következőt mondja:

```cmd
The request path /luis//predict" does not match a supported file type.
```

Mit jelent ez? Mi hiányzik? A Speech SDK példáját követem [itt](https://github.com/Azure-Samples/cognitive-services-speech-sdk). A forgatókönyv az, hogy a hang közvetlenül a PC-mikrofonból van érzékelve, és a szándék alapján határozható meg a betanított LUIS-alkalmazás alapján. A hivatkozott példa pontosan ezt teszi. És jól működik a LUIS felhőalapú szolgáltatásával. Úgy tűnt, hogy a Speech SDK használatával külön explicit módon meghívja a beszéd-szöveg API-t, majd másodszor meghívja a LUIS-t.

Tehát minden, amit megpróbálok végrehajtani, váltson a LUIS Felhőbeli használatával a LUIS-tároló használatára. Nem tudom elképzelni, hogy a Speech SDK működik-e az egyikre, de nem fog működni.

**Válasz:** A Speech SDK nem használható LUIS-tárolón. A LUIS-tároló használatához a LUIS SDK-t vagy LUIS REST APIt kell használni. A Speech SDK-t egy beszédfelismerési tárolón kell használni.

A felhő különbözik a tárolótól. A felhő több összesített tárolóból is állhat (más néven Micro Services). Tehát van egy LUIS-tároló, és van egy beszédfelismerési tároló – két különálló tároló. A beszédfelismerési tároló csak a beszédet végzi. A LUIS-tároló csak a LUIS-t támogatja. A felhőben, mivel mindkét tárolót központilag üzembe kell helyezni, és ez rossz teljesítmény egy távoli ügyfél számára a felhőbe való ugráshoz, a beszéd, a visszatérés, majd a felhőbe való újbóli elvégzése, valamint a LUIS, biztosítunk egy funkciót, amely lehetővé teszi az ügyfél számára, hogy a beszéd, a felhőben maradjon, és térjen vissza az ügyfélhez Így még ebben az esetben is a Speech SDK a hangfelvételt folytató Felhőbeli tárolóba kerül, majd a beszédfelismerési Felhőbeli tároló beszélget a szöveggel ellátott LUIS Cloud-tárolóval. A LUIS-tároló nem rendelkezik hangvételi koncepcióval (nem lenne értelme a LUIS-tárolónak, hogy fogadja a streaming audio-LUIS egy szöveges alapú szolgáltatást). Helyszíni, nem vagyunk biztosak abban, hogy az ügyfelenk mindkét tárolót üzembe helyezte, nem feltételezjük, hogy az ügyfél telephelyén található tárolók között nem vagyunk felkészítve, és ha mindkét tároló üzembe helyezése a helyszíni környezetben történik, mivel azok jobban megtalálhatók az ügyfél számára, akkor nem jelent terhet az SR első, az ügyfél felé irányuló, és az ügyfél elvégzésére, és

<br>
</details>

<details>
<summary>
<b>Miért kapok hibákat a macOS, a Speech Container és a Python SDK használatával?</b>
</summary>

Ha egy *. wav* fájlt küldünk, az eredmény a következővel jön vissza:

```cmd
recognition is running....
Speech Recognition canceled: CancellationReason.Error
Error details: Timeout: no recognition result received.
When creating a websocket connection from the browser a test, we get:
wb = new WebSocket("ws://localhost:5000/speech/recognition/dictation/cognitiveservices/v1")
WebSocket
{
    url: "ws://localhost:5000/speech/recognition/dictation/cognitiveservices/v1",
    readyState: 0,
    bufferedAmount: 0,
    onopen: null,
    onerror: null,
    ...
}
```

Tudjuk, hogy a WebSocket helyesen van beállítva.

**Válasz:** Ha ez a helyzet, akkor tekintse meg [ezt a GitHub-problémát](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/310). [Itt](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/310#issuecomment-527542722)is találunk munkát.

A szén a 1,8-es verzióban rögzítette.


<br>
</details>

<details>
<summary>
<b>Mi a különbség a beszédfelismerési tároló végpontjai között?</b>
</summary>

Segíthet az alábbi tesztelési mérőszámok kitöltésében, beleértve a tesztelni kívánt funkciókat, valamint az SDK és a REST API-k tesztelését? Különösen az "interaktív" és a "beszélgetés" közötti különbségek, amelyeket nem látok a meglévő doc/Sample-ből.

| Végpont                                                | Funkcionális teszt                                                   | SDK | REST API |
|---------------------------------------------------------|-------------------------------------------------------------------|-----|----------|
| `/speech/synthesize/cognitiveservices/v1`               | Szintetizáló szöveg (szöveg – beszéd)                                  |     | Yes      |
| `/speech/recognition/dictation/cognitiveservices/v1`    | Cognitive Services on-Prem diktálás v1 WebSocket-végpont        | Igen | Nem       |
| `/speech/recognition/interactive/cognitiveservices/v1`  | A Cognitive Services on-Prem Interactive v1 WebSocket-végpont  |     |          |
| `/speech/recognition/conversation/cognitiveservices/v1` | A kognitív szolgáltatások on-Prem beszélgetés v1 WebSocket-végpont |     |          |

**Válasz:** Ez a következő fúzió:
- A tárolók diktálási végpontját kipróbáló személyek (nem tudom, hogyan kapták meg ezt az URL-címet)
- Az 1<sup>St</sup> -fél végpont egy tárolóban található.
- Az 1<sup>St</sup> féltől származó végpont a Speech. fragment üzenetet adja vissza az üzenetek helyett, `speech.hypothesis` a diktálási végponthoz tartozó 3<sup>Távoli asztali</sup> rész végpontja.
- A teljes széndioxid-használat `RecognizeOnce` (interaktív mód)
- A szén-dioxid azt állítja be, hogy az olyan üzenetek esetében, amelyekhez `speech.fragment` nem ad vissza interaktív módban.
- A kibocsátás a kiállítók által kiváltott kiadásokat (a folyamat leölését) állítja be.

A megkerülő megoldás vagy váltás a kód folyamatos felismerésének használatára, vagy (gyorsabb) kapcsolódás a tárolóban lévő interaktív vagy folyamatos végpontokhoz.
A kód esetében állítsa a végpontot/Speech/Recognition/Interactive/cognitiveservices/v1 értékre. `host:port`

A különböző módokon lásd a beszédfelismerési módokat – lásd alább:

## <a name="speech-modes---interactive-conversation-dictation"></a>Beszédfelismerési módok – interaktív, beszélgetés, diktálás

[!INCLUDE [speech-modes](includes/speech-modes.md)]

A megfelelő javítás az SDK 1,8-as verziójában érhető el, amely helyszíni támogatással rendelkezik (a megfelelő végpontot fogja kiválasztani, így nem lesz rosszabb, mint az online szolgáltatás). Addig is van egy példa a folyamatos felismerésre, miért nem erre mutatunk?

https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/6805d96bf69d9e95c9137fe129bc5d81e35f6309/samples/python/console/speech_sample.py#L196

<br>
</details>

<details>
<summary>
<b>Milyen módot érdemes használni a különböző hangfájlokhoz?</b>
</summary>

**Válasz:** Íme egy rövid útmutató a [Python használatával](./get-started-speech-to-text.md?pivots=programming-language-python). A docs webhelyhez csatolt más nyelveket is megtalálhatja.

Csak hogy tisztázza az interaktív, a beszélgetést és a diktálást; Ez egy speciális módszer, amely meghatározza, hogy a szolgáltatás milyen módon fogja kezelni a beszédfelismerési kérést. Sajnos a helyszíni tárolók esetében meg kell adni a teljes URI-t (mivel ez tartalmazza a helyi gépet is), így a rendszer ezt az információt kiszivárgott az absztrakcióból. Az SDK-csapattal dolgozunk, hogy ezt a jövőben is használhatóvá tegye.

<br>
</details>

<details>
<summary>
<b>Hogyan lehet a tranzakció/másodperc/mag durva mértékét felmérni?</b>
</summary>

**Válasz:** Íme néhány a meglévő modellből várható durva számok közül (az a legjobb, ha az egyiket a GA-ban fogjuk szállítani):

- A fájlok esetében a szabályozás a Speech SDK-ban lesz, 2x-ben. Az audió első öt másodperce nincs szabályozva. A dekóder képes a 3x valós idejű végrehajtására. Ebben az esetben a teljes CPU-használat két magot mutat be egyetlen felismeréshez.
- A MIC esetében a szolgáltatás 1x valós időben fog megjelenni. Az általános használatnak körülbelül 1 mag kell lennie egyetlen felismeréshez.

Ez a Docker-naplókból is ellenőrizhető. Tulajdonképpen a munkamenetet és a kifejezési/kiírási statisztikát tartalmazó sort, valamint az RTF-számokat is tartalmazza.


<br>
</details>

<details>
<summary>
<b>Gyakori a hangfájlok felosztása a hangtárolók általi használatra?</b>
</summary>

A jelenlegi tervem egy meglévő hangfájl elkészítése, és 10 másodperces darabokra osztása és a tárolón keresztüli elküldése. Elfogadható a forgatókönyv?  Van jobb módszer a tárolóban lévő nagyobb hangfájlok feldolgozására?

**Válasz:** Csak használja a Speech SDK-t, és adja meg a fájlt, és tegye meg a megfelelő dolgot. Miért van szükség a fájl kidarabolására?


<br>
</details>

<details>
<summary>
<b>Hogyan hogy több tároló fusson ugyanazon a gazdagépen?</b>
</summary>

A doki egy másik portot tesz elérhetővé, amelyet mégis, de a LUIS-tároló továbbra is figyeli a 5000-es portot?

**Válasz:** Próbálja ki `-p <outside_unique_port>:5000` . Például: `-p 5001:5000`.


<br>
</details>

## <a name="technical-questions"></a>Technikai kérdések

<details>
<summary>
<b>Hogyan szerezhetek be nem batch API-kat a &lt; 15 másodperces hang kezelésére?</b>
</summary>

**Válasz:** `RecognizeOnce()` az interaktív módban csak a legfeljebb 15 másodperces hangot dolgozza fel, mivel a mód olyan beszédfelismerési parancsokhoz használható, ahol a hosszúságú kimondott szöveg várhatóan rövidek lesznek. Ha `StartContinuousRecognition()` diktálást vagy beszélgetést használ, nincs 15 másodperces korlát.


<br>
</details>

<details>
<summary>
<b>Mik az ajánlott erőforrások, a processzor és a RAM; 50 egyidejű kérelem esetén?</b>
</summary>

Hány egyidejű kérelem fog egy 4 magos, 4 GB RAM-ot kezelni? Ha például az 50 egyidejű kéréseket, hány mag és RAM ajánlott?

**Válasz:** Valós időben, 8 a legújabb `en-US` , ezért javasoljuk, hogy több Docker-tárolót használjon 6 egyidejű kérésen túl. A 16 magot meghaladó, a nem egységes memória-hozzáférési (NUMA-) csomópontra vált. Az alábbi táblázat az egyes beszédfelismerési tárolók minimális és ajánlott erőforrás-elosztását ismerteti.

# <a name="speech-to-text"></a>[Diktálás](#tab/stt)

| Tároló      | Minimális             | Ajánlott         |
|----------------|---------------------|---------------------|
| Diktálás | 2 mag, 2 GB memória | 4 mag, 4 GB memória |

# <a name="custom-speech-to-text"></a>[Custom Speech – szöveg](#tab/cstt)

| Tároló             | Minimális             | Ajánlott         |
|-----------------------|---------------------|---------------------|
| Custom Speech – szöveg | 2 mag, 2 GB memória | 4 mag, 4 GB memória |

# <a name="text-to-speech"></a>[Szövegfelolvasás](#tab/tts)

| Tároló      | Minimális             | Ajánlott         |
|----------------|---------------------|---------------------|
| Szövegfelolvasás | 1 mag, 2 GB memória | 2 mag, 3 GB memória |

# <a name="custom-text-to-speech"></a>[Egyéni szöveg – beszéd](#tab/ctts)

| Tároló             | Minimális             | Ajánlott         |
|-----------------------|---------------------|---------------------|
| Egyéni szöveg – beszéd | 1 mag, 2 GB memória | 2 mag, 3 GB memória |

***

- Minden mag legalább 2,6 GHz-es vagy gyorsabb lehet.
- A fájlok esetében a szabályozás a Speech SDK-ban lesz, 2x-ben (az első 5 másodperc nem szabályozható).
- A dekóder 2 – 3x valós idejű működésre képes. Ebben az esetben a teljes CPU-használat két magot mutat be egyetlen felismeréshez. Ezért nem ajánlott több mint két aktív kapcsolatot tartani a tároló-példányok esetében. A szélsőséges oldal az, hogy egy nyolc Magos gépen (például:) körülbelül 10 dekódert helyezzen el körülbelül 2x valós időben `DS13_V2` . A Container 1,3-es és újabb verzióiban van egy paraméter, amelyből megpróbálkozhat a beállítással `DECODER_MAX_COUNT=20` .
- A mikrofon esetében a szolgáltatás 1x valós időben fog futni. Az általános használatnak körülbelül egy mag kell lennie egyetlen felismeréshez.

Vegye figyelembe, hogy hány órányi hangra van szüksége. Ha a szám nagy, a megbízhatóság és a rendelkezésre állás javítása érdekében javasoljuk, hogy a tárolók több példányát futtassuk, akár egyetlen, akár több mezőben, a terheléselosztó mögött. A koordinálás a Kubernetes (K8S) és a Helm, illetve a Docker-összeállítás használatával végezhető el.

A 1000 óra/24 óra kezeléséhez például megpróbálta beállítani a 3-4 virtuális gépeket, és virtuális gépenként 10 példányt/dekódert.

<br>
</details>

<details>
<summary>
<b>Támogatja a beszédfelismerési tároló a központozást?</b>
</summary>

**Válasz:** A helyszíni tárolóban elérhető a nagybetűs (ITN). A központozás nyelvtől függ, és egyes nyelvek esetében nem támogatott, beleértve a kínai és a japán nyelvet is.

A meglévő tárolók esetében implicit és alapszintű központozási *támogatást biztosítunk* , de alapértelmezés szerint ez a `off` beállítás. Ez azt jelenti, hogy a karaktert lekérheti a `.` példában, nem pedig a `。` karaktert. Ennek az implicit logikának az engedélyezéséhez példát mutatunk arra, hogyan teheti ezt meg a Pythonban a Speech SDK használatával (más nyelveken is hasonló lenne):

```python
speech_config.set_service_property(
    name='punctuation',
    value='implicit',
    channel=speechsdk.ServicePropertyChannel.UriQueryParameter
)
```

<br>
</details>

<details>
<summary>
<b>Miért kapok 404-es hibaüzenetet, amikor a rendszer megkísérelt adatpostát küldeni a szöveges tárolóba?</b>
</summary>

Példa HTTP POST:

```http
POST /speech/recognition/conversation/cognitiveservices/v1?language=en-US&format=detailed HTTP/1.1
Accept: application/json;text/xml
Content-Type: audio/wav; codecs=audio/pcm; samplerate=16000
Transfer-Encoding: chunked
User-Agent: PostmanRuntime/7.18.0
Cache-Control: no-cache
Postman-Token: xxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
Host: 10.0.75.2:5000
Accept-Encoding: gzip, deflate
Content-Length: 360044
Connection: keep-alive
HTTP/1.1 404 Not Found
Date: Tue, 22 Oct 2019 15:42:56 GMT
Server: Kestrel
Content-Length: 0
```

**Válasz:** Nem támogatjuk a REST APIt a beszéd-szöveg tárolóban, csak a Speech SDK-n keresztül támogatjuk a websocketeket. Mindig tekintse meg a hivatalos dokumentációt, lásd: [lekérdezés-előrejelzési végpontok](speech-container-howto.md#query-the-containers-prediction-endpoint).

<br>
</details>


<details>
<summary>
<b> Miért nem root felhasználóként fut a tároló? Milyen problémák merülhetnek fel?</b>
</summary>

**Válasz:** Vegye figyelembe, hogy a tárolóban lévő alapértelmezett felhasználó nem root felhasználó. Ez védelmet biztosít a tárolón elkerülő folyamatokkal szemben, és a gazdagép csomópontján megszerezheti a megnövekvő engedélyeket. Alapértelmezés szerint egyes platformokon, például a OpenShift-tároló platformon már ezt a tárolókat egy tetszőlegesen hozzárendelt felhasználói azonosító használatával futtathatja. Ezekhez a platformokhoz a nem gyökérszintű felhasználónak engedélyre van szüksége az írást igénylő, külsőleg leképezett kötetre való íráshoz. Például egy naplózási mappa vagy egy egyéni modell letöltési mappája.
<br>
</details>

<details>
<summary>
<b>A beszédfelismerési szolgáltatás használatakor Miért kapok hibaüzenetet?</b>
</summary>

```cmd
Error in STT call for file 9136835610040002161_413008000252496:
{
    "reason": "ResultReason.Canceled",
    "error_details": "Due to service inactivity the client buffer size exceeded. Resetting the buffer. SessionId: xxxxx..."
}
```

**Válasz:** Ez általában akkor fordul elő, ha a hangfelismerő tárolónál gyorsabban táplálja a hangot. Megtörténik az ügyfél-pufferek kitöltése, a megszakítás pedig aktiválódik. Meg kell határoznia a párhuzamosságot és az RTF-t, amelyen elküldi a hangot.

<br>
</details>

<details>
<summary>
<b>Meg tudná magyarázni ezeket a szöveg-beszéd tárolói hibákat a C++ példákból?</b>
</summary>

**Válasz:** Ha a tároló verziója 1,3-nál régebbi, akkor ezt a kódot kell használni:

```cpp
const auto endpoint = "http://localhost:5000/speech/synthesize/cognitiveservices/v1";
auto config = SpeechConfig::FromEndpoint(endpoint);
auto synthesizer = SpeechSynthesizer::FromConfig(config);
auto result = synthesizer->SpeakTextAsync("{{{text1}}}").get();
```

A régebbi tárolók nem rendelkeznek az API-val való együttműködéshez szükséges végponttal `FromHost` . Ha az 1,3-es verzióhoz használt tárolók, akkor ezt a kódot kell használni:

```cpp
const auto host = "http://localhost:5000";
auto config = SpeechConfig::FromHost(host);
config->SetSpeechSynthesisVoiceName(
    "Microsoft Server Speech Text to Speech Voice (en-US, AriaRUS)");
auto synthesizer = SpeechSynthesizer::FromConfig(config);
auto result = synthesizer->SpeakTextAsync("{{{text1}}}").get();
```

Az alábbi példa az API használatát mutatja be `FromEndpoint` :

```cpp
const auto endpoint = "http://localhost:5000/cognitiveservices/v1";
auto config = SpeechConfig::FromEndpoint(endpoint);
config->SetSpeechSynthesisVoiceName(
    "Microsoft Server Speech Text to Speech Voice (en-US, AriaRUS)");
auto synthesizer = SpeechSynthesizer::FromConfig(config);
auto result = synthesizer->SpeakTextAsync("{{{text2}}}").get();
```

 A `SetSpeechSynthesisVoiceName` függvény hívása, mert a frissített szöveg-beszéd motorral rendelkező tárolók megkövetelik a hang nevét.

<br>
</details>

<details>
<summary>
<b>Hogyan használhatom a Speech SDK v 1.7-es verzióját egy beszédfelismerési tárolóval?</b>
</summary>

**Válasz:** A beszédfelismerési tároló három végpontot használ a különböző használatokhoz, ezek a beszédfelismerési módokként vannak meghatározva – lásd alább:

## <a name="speech-modes"></a>Beszédfelismerési módok

[!INCLUDE [speech-modes](includes/speech-modes.md)]

Különböző célokra szolgálnak, és eltérő módon használatosak.

Python- [minták](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/python/console/speech_sample.py):
- Az egyszeri felismeréshez (interaktív mód) egyéni végponttal (azaz `SpeechConfig` végponti paraméterrel) lásd: `speech_recognize_once_from_file_with_custom_endpoint_parameters()` .
- A folyamatos felismeréshez (beszélgetési mód), és csak módosítsa úgy, hogy egyéni végpontot használjon a fentiek szerint: `speech_recognize_continuous_from_file()` .
- Ha szeretné engedélyezni a diktálást a fenti mintákban (csak ha valóban szüksége van rá), közvetlenül a létrehozás után `speech_config` adja hozzá a kódot `speech_config.enable_dictation()` .

A C#-ban a diktálás engedélyezéséhez hívja meg a `SpeechConfig.EnableDictation()` függvényt.

### <a name="fromendpoint-apis"></a>`FromEndpoint` API
| Nyelv | API-részletek |
|----------|:------------|
| C++ | <a href="https://docs.microsoft.com/cpp/cognitive-services/speech/speechconfig#fromendpoint" target="_blank">`SpeechConfig::FromEndpoint` <span class="docon docon-navigate-external x-hidden-focus"></span></a> |
| C# | <a href="https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig.fromendpoint" target="_blank">`SpeechConfig.FromEndpoint` <span class="docon docon-navigate-external x-hidden-focus"></span></a> |
| Java | <a href="https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechconfig.fromendpoint" target="_blank">`SpeechConfig.fromendpoint` <span class="docon docon-navigate-external x-hidden-focus"></span></a> |
| Objective-C | <a href="https://docs.microsoft.com/objectivec/cognitive-services/speech/spxspeechconfiguration#initwithendpoint" target="_blank">`SPXSpeechConfiguration:initWithEndpoint;` <span class="docon docon-navigate-external x-hidden-focus"></span></a> |
| Python | <a href="https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig" target="_blank">`SpeechConfig;` <span class="docon docon-navigate-external x-hidden-focus"></span></a> |
| JavaScript | Jelenleg nem támogatott, és nem is tervezett. |

<br>
</details>

<details>
<summary>
<b>Hogyan használhatom a Speech SDK v 1.8-as verzióját egy Speech Container használatával?</b>
</summary>

**Válasz:** Új `FromHost` API van. Ez nem helyettesíti vagy nem módosítja a meglévő API-kat. Ez csak egy alternatív módszert tesz lehetővé egy beszédfelismerési konfiguráció egyéni gazdagép használatával történő létrehozásához.

### <a name="fromhost-apis"></a>`FromHost` API

| Nyelv | API-részletek |
|--|:-|
| C# | <a href="https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig.fromhost" target="_blank">`SpeechConfig.FromHost` <span class="docon docon-navigate-external x-hidden-focus"></span></a> |
| C++ | <a href="https://docs.microsoft.com/cpp/cognitive-services/speech/speechconfig#fromhost" target="_blank">`SpeechConfig::FromHost` <span class="docon docon-navigate-external x-hidden-focus"></span></a> |
| Java | <a href="https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechconfig.fromhost" target="_blank">`SpeechConfig.fromHost` <span class="docon docon-navigate-external x-hidden-focus"></span></a> |
| Objective-C | <a href="https://docs.microsoft.com/objectivec/cognitive-services/speech/spxspeechconfiguration#initwithhost" target="_blank">`SPXSpeechConfiguration:initWithHost;` <span class="docon docon-navigate-external x-hidden-focus"></span></a> |
| Python | <a href="https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig" target="_blank">`SpeechConfig;` <span class="docon docon-navigate-external x-hidden-focus"></span></a> |
| JavaScript | Egyelőre nem támogatott |

> Paraméterek: állomás (kötelező), előfizetési kulcs (nem kötelező, ha a szolgáltatást nélkül is használhatja).

A gazdagép formátuma `protocol://hostname:port` nem `:port` kötelező (lásd alább):
- Ha a tároló helyileg fut, az állomásnév `localhost` .
- Ha a tároló távoli kiszolgálón fut, használja az adott kiszolgáló állomásnevét vagy IPv4-címeit.

Host paraméter példák a beszédfelismerési szövegre:
- `ws://localhost:5000` -nem biztonságos csatlakozás egy helyi tárolóhoz a 5000-es port használatával
- `ws://some.host.com:5000` -nem biztonságos kapcsolódás távoli kiszolgálón futó tárolóhoz

Python-minták a fentiek közül, de `host` a paraméter helyett használja a következőt `endpoint` :

```python
speech_config = speechsdk.SpeechConfig(host="ws://localhost:5000")
```

<br>
</details>

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Cognitive Services tárolók](speech-container-howto.md)