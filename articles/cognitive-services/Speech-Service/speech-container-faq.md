---
title: Speech-szolgáltatástárolók – gyakori kérdések (GYIK)
titleSuffix: Azure Cognitive Services
description: Speech-tárolók telepítése és futtatása. A beszédfelismerés valós időben ír át hangstreameket szövegre, amit az alkalmazások, eszközök vagy eszközök fel tudnak használni vagy megjeleníteni. A szöveg-beszéd átalakítja a bemeneti szöveget emberhez hasonló szintetizált beszédgé.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/11/2021
ms.author: trbye
ms.custom: devx-track-csharp
ms.openlocfilehash: 28a044f42d0774d940521964b68b38a0f35bcdbb
ms.sourcegitcommit: aa00fecfa3ad1c26ab6f5502163a3246cfb99ec3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2021
ms.locfileid: "107387955"
---
# <a name="speech-service-containers-frequently-asked-questions-faq"></a>Speech-szolgáltatástárolók – gyakori kérdések (GYIK)

Ha a Speech szolgáltatást tárolókhoz használja, a támogatás eszkalálása előtt támaszkodjon a gyakori kérdések gyűjteményére. Ez a cikk különböző mértékben rögzíti a kérdéseket, az általánostól a technikaiig. A válasz kibontásához kattintson a kérdésre.

## <a name="general-questions"></a>Általános kérdések

<details>
<summary>
<b>Hogyan működnek a Speech-tárolók, és hogyan állíthatom be őket?</b>
</summary>

**Válasz:** Az éles fürt beállításakor több dolgot is figyelembe kell venni. Először is, az egyetlen nyelv, több tároló ugyanazon a gépen történő beállítása nem jelent nagy problémát. Ha problémákat tapasztal, lehet, hogy hardverrel kapcsolatos probléma, tehát először az erőforrással, vagyis a PROCESSZOR- és memóriasokajátok.

Gondolja át egy pillanatra a tárolót `ja-JP` és a legújabb modellt. Az akusztikai modell a legnagyobb processzorigényű darab, míg a nyelvi modell igényli a legtöbb memóriát. Amikor teljesítménytesztettünk a használaton, körülbelül 0,6 processzormag szükséges egyetlen beszéd-szöveg kérés feldolgozásához, amikor a hang valós időben áramlik (például a mikrofonból). Ha a valós idejűnél gyorsabban ad hanganyagot (például egy fájlból), ez a használat megduplázhatja (1,2x mag). Eközben az alább felsorolt memória a beszéd dekódolására használt memória. Nem *veszi figyelembe* a nyelvi modell tényleges teljes méretét, amely a fájlgyorsítótárban található. Ez további 2 GB, a esetén több `ja-JP` `en-US` (6–7 GB) is lehet.

Ha egy olyan géppel van, ahol kevés a memória, és több nyelvet próbál üzembe helyezni rajta, lehetséges, hogy a fájlgyorsítótár megtelt, és az operációs rendszer a modellek be- és kilaposodása miatt megtelt. Futó átírások során ez katasztrofális következményekkel járhat, és lassuláshoz és egyéb teljesítménybeli következményekhez vezethet.

Emellett előre becsomagoljuk a speciális vektorkiterjesztésű [(AVX2)](speech-container-howto.md#advanced-vector-extension-support) utasításkészletű gépek végrehajtható fájlokat. Az AVX512 utasításkészletet tartalmazó gépeknek ehhez a célhoz kód generálása szükséges, és 10 nyelvhez 10 tároló indítása átmenetileg kimerítheti a CPU-t. Az ehhez hasonló üzenet megjelenik a Docker-naplókban:

```console
2020-01-16 16:46:54.981118943 
[W:onnxruntime:Default, tvm_utils.cc:276 LoadTVMPackedFuncFromCache]
Cannot find Scan4_llvm__mcpu_skylake_avx512 in cache, using JIT...
```

Egy tárolóban a kívánt dekóderek számát változó használatával *állíthatja* `DECODER MAX_COUNT` be. Tehát gyakorlatilag a termékváltozatával (CPU/memória) kell kezdenünk, és javasolni tudjuk, hogyan lehet a lehető legjobbat kihozni belőle. Egy jó kiindulási pont a gazdagép ajánlott erőforrás-specifikációira utal.

<br>
</details>

<details>
<summary>
<b>Segíthetne a kapacitástervezésben és a speech-to-text tárolók költségbecslésében?</b>
</summary>

**Válasz:** Kötegelt feldolgozási módban a tárolókapacitások esetén minden dekóder 2–3x valós időben, két processzormaggal képes egyetlen felismerésre. Nem javasoljuk, hogy tárolópéldányonként kettőnél több egyidejű felismerést tartsunk meg, de a megbízhatósági/rendelkezésre állási okokból javasoljuk, hogy több tárolópéldányt futtatasunk egy terheléselosztás mögött.

Bár az egyes tárolópéldányok több dekóderen futnak. Például tárolópéldányonként 7 dekóder beállítható egy nyolcmagos gépen (mindegyiknél több mint 2x), ami 15x átviteli sebességet eredményez. Van egy paraméter, `DECODER_MAX_COUNT` amelyről tudnia kell. Szélsőséges esetben megbízhatósági és késési problémák merülnek fel, amelyek átviteli sebességének jelentősen megnőtt. Mikrofonhoz 1x valós időben. Az általános használatnak körülbelül egy magnak kell lennie egyetlen felismeréshez.

Az 1 K óra/nap kötegelt feldolgozási módban történő feldolgozás esetén szélsőséges esetben 3 virtuális gép 24 órán belül képes kezelni azt, de ez nem garantált. A kiugróan magas napok, a feladatátvétel, a frissítés és a minimális biztonsági mentés/BCP biztosítása érdekében fürtönként 4–5 gépet javasolunk 3 helyett, és több fürtöt is.

Hardverek esetén a standard Azure-beli virtuális gépet használjuk referenciaként (minden magnak `DS13_v2` legalább 2,6 GHz-nek kell lennie, engedélyezett AVX2 utasításkészlet mellett).

| Példány  | vCPU(k) | RAM    | Ideiglenes tároló | Fizetés a AHB-hez | 1 éves tartalék AHB-val (%- megtakarítás) | 3 éves, AHB-hez fenntartva (%- os megtakarítás) |
|-----------|---------|--------|--------------|------------------------|-------------------------------------|--------------------------------------|
| `DS13 v2` | 8       | 56 GiB | 112 GiB      | 0,598 USD/óra            | 0,3528 USD/óra (~41%)                 | 0,2333 USD/óra (~61%)                  |

A kialakítási referencia alapján (két 5 virtuális gépből áll, amelyek 1 K óra/nap hangkötet-feldolgozást kezelnek) az 1 éves hardverköltség a következő lesz:

> 2 (fürtök) * 5 (virtuális gépek fürtönként) * 0,3528 USD/óra * 365 (nap) * 24 (óra) = $31K / év

Fizikai gépre való leképezéskor az általános becslés 1 vCPU = 1 fizikai processzormag. A valóságban az 1vCPU hatékonyabb, mint egy mag.

A szolgáltatásban a következő tényezők mind a következő tényezőket befolyásolják:

- Milyen típusú a fizikai CPU, és hány mag található rajta?
- Hány PROCESSZOR fut együtt ugyanazon a dobozon/gépen
- A virtuális gépek beállítása
- A hyper-threading/többszálas használata
- A memória-osztva
- Az operációs rendszer stb.

Általában nincs olyan jól hangolva, mint az Azure-környezet. Egyéb többletterhelést figyelembe véve azt hiszem, a biztonságos becslés 10 fizikai processzormag = 8 Azure vCPU. Bár a népszerű cpu-k csak nyolc maggal oszlnak meg. A virtuális gép üzembe helyezése magasabb költséggel jár, mint az Azure-beli virtuális gépek használata. Vegye figyelembe az értékcsökkenési rátát is.

A szolgáltatási költség megegyezik az online szolgáltatással: 1 USD/óra a beszédfelismeréshez. A Speech szolgáltatás költsége:

> 1 * 1000 * 365 = 365 E USD

A Microsoftnak kifizetett karbantartási költségek a szolgáltatás szintjétől és tartalmától függnek. Az alapszintű szint 29,99 USD/hó és akár több százezer is lehet, ha helyszíni szolgáltatásról van szó. A szolgáltatás/karbantartás körülbelül 300 USD/óra. A személyek költsége nem szerepel a csomagban. Az egyéb infrastrukturális költségeket (például a tárolást, a hálózatokat és a terheléselosztást) nem tartalmazza.

<br>
</details>

<details>
<summary>
<b>Miért hiányzik az írásjel az átírásból?</b>
</summary>

**Válasz:** A-t explicit módon kell konfigurálni a `speech_recognition_language=<YOUR_LANGUAGE>` kérésben, ha Carbon-ügyfelet használ.

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
<b>Használhatok egyéni akusztikai modellt és nyelvi modellt a Speech-tárolóval?</b>
</summary>

Jelenleg csak egy modellazonosítót tudunk átadni: egyéni nyelvi modellt vagy egyéni akusztikai modellt.

**Válasz:** Úgy *döntöttek, hogy* nem támogatják egyszerre az akusztikai és nyelvi modelleket is. Ez mindaddig érvényben marad, amíg létre nem hoz egy egységesített azonosítót az API-törések csökkentése érdekében. Ez sajnos jelenleg nem támogatott.

<br>
</details>

<details>
<summary>
<b>Elmagyarázná ezeket a hibákat az egyéni beszédfelismerési tárolóból?</b>
</summary>

**1-es hiba:**

```cmd
Failed to fetch manifest: Status: 400 Bad Request Body:
{
    "code": "InvalidModel",
    "message": "The specified model is not supported for endpoint manifests."
}
```

**1. válasz:** Ha a legújabb egyéni modellt használja a betanításhoz, azt jelenleg nem támogatjuk. Ha a betanítás régebbi verzióval történik, akkor a használata is lehetséges. Még dolgozunk a legújabb verziók támogatásán.

Az egyéni tárolók lényegében nem támogatják a Halide- vagy ONNX-alapú akusztikai modelleket (ez az alapértelmezett beállítás az egyéni képzési portálon). Ennek az az oka, hogy az egyéni modellek nincsenek titkosítva, és nem szeretnénk elérhetővé téve az ONNX-modelleket; A nyelvi modellek nem gondok. Az ügyfélnek explicit módon ki kell választania egy régebbi, nem ONNX modellt az egyéni betanításhoz. A pontosságot ez nem befolyásolja. A modell mérete nagyobb lehet (100 MB-nál).

> Támogatási modell > 20190220 (v4.5 Unified)

**2-es hiba:**

```cmd
HTTPAPI result code = HTTPAPI_OK.
HTTP status code = 400.
Reason:  Synthesis failed.
StatusCode: InvalidArgument,
Details: Voice does not match.
```

**2. válasz:** A kérésben meg kell adnia a megfelelő hangnevet, amely megkülönbözteti a kis- és nagybetűket. Tekintse meg a teljes szolgáltatásnév-leképezést.

**3-as hiba:**

```json
{
    "code": "InvalidProductId",
    "message": "The subscription SKU \"CognitiveServices.S0\" is not supported in this service instance."
}
```

**3. válasz:** Egy Speech-erőforrást hoz létre, nem egy Cognitive Services erőforrást.


<br>
</details>

<details>
<summary>
<b>Mely API-protokollok támogatottak: REST vagy WS?</b>
</summary>

**Válasz:** A beszédfelismerési és egyéni beszédfelismerési tárolókhoz jelenleg csak a websocket-alapú protokollt támogatjuk. Az SDK csak a WS-hez támogatja a hívásokat, a REST-et nem. Terv van REST-támogatás hozzáadására, de az ETA jelenleg nem. Mindig tekintse meg a hivatalos dokumentációt: [lekérdezés-előrejelzési végpontok.](speech-container-howto.md#query-the-containers-prediction-endpoint)

<br>
</details>

<details>
<summary>
<b>A CentOS támogatott a Speech-tárolókhoz?</b>
</summary>

**Válasz:** A Python SDK még nem támogatja a CentOS 7-et, az Ubuntu 19.04 pedig nem támogatott.

A Python Speech SDK csomag a következő operációs rendszerekhez érhető el:
- **Windows** – x64 és x86
- **Mac** – macOS X 10.12-es vagy újabb verzió
- **Linux** – Ubuntu 16.04, Ubuntu 18.04, Debian 9 x64-en

További információ a környezet beállításról: [Python-platform beállítása.](quickstarts/setup-platform.md?pivots=programming-language-python) Jelenleg az Ubuntu 18.04 az ajánlott verzió.

<br>
</details>

<details>
<summary>
<b>Miért kapok hibaüzeneteket a LUIS előrejelzési végpontjainak hívása során?</b>
</summary>

A LUIS-tárolót egy IoT Edge használom, és egy másik tárolóból szeretném meghívni a LUIS előrejelzési végpontját. A LUIS-tároló az 5001-es porton figyel, és az általom használt URL-cím a következő:

```csharp
var luisEndpoint =
    $"ws://192.168.1.91:5001/luis/prediction/v3.0/apps/{luisAppId}/slots/production/predict";
var config = SpeechConfig.FromEndpoint(new Uri(luisEndpoint));
```

A következő hibaüzenetet kapom:

```cmd
WebSocket Upgrade failed with HTTP status code: 404 SessionId: 3cfe2509ef4e49919e594abf639ccfeb
```

A kérelem megjelenik a LUIS-tároló naplóiban, és az üzenet a következő:

```cmd
The request path /luis//predict" does not match a supported file type.
```

Mit jelent ez? Mi hiányzik? A Speech SDK példáját követtem, [innen:](https://github.com/Azure-Samples/cognitive-services-speech-sdk). A forgatókönyv szerint a hangot közvetlenül a számítógép mikrofonja alapján észleljük, és a betanított LUIS-alkalmazás alapján próbáljuk meghatározni a szándékot. A hivatkozott példa pontosan ezt teszi. És jól működik a LUIS felhőalapú szolgáltatásával. Úgy tűnik, hogy a Speech SDK használatával nem kellett külön explicit hívást küldenünk a speech-to-text API-nak, majd egy második hívást a LUIS-nak.

Ezért csak át szeretnék váltani a felhőbeli LUIS-t használó forgatókönyvről a LUIS-tárolóra. Nem tudom elképzelni, hogy ha a Speech SDK működik az egyikben, akkor nem fog működni a másikban.

**Válasz:** A Speech SDK nem használható LUIS-tárolókhoz. A LUIS-tároló használata esetén a LUIS SDK-t REST API LUIS-t kell használni. A Speech SDK-t beszédtárolóhoz kell használni.

A felhő különbözik a tárolóktól. A felhő több összesített tárolóból (más néven mikroszolgáltatásból) is alkotható. Tehát van egy LUIS-tároló, majd egy Speech-tároló – Két különálló tároló. A Speech-tároló csak a beszédet teszi. A LUIS-tároló csak a LUIS-t teszi meg. A felhőben, mivel mindkét tároló üzembe helyezése ismert, és rossz teljesítményt eredményez, ha egy távoli ügyfél a felhőbe kerül, beszédet mond, visszatér a felhőbe, és újra a LUIS-t használja, olyan funkciót biztosítunk, amely lehetővé teszi az ügyfél számára, hogy a Speechbe menjen, a felhőben maradjon, majd a LUIS-hoz visszatérve visszatér az ügyfélhez. Így még ebben a forgatókönyvben is a Speech SDK a Speech felhőbeli tárolóba kerül hanggal, majd a Speech Cloud-tároló szöveggel beszélget a LUIS felhőalapú tárolóval. A LUIS-tárolóban nincs szükség hang elfogadására (a LUIS-tárolónak nincs értelme streamelési hangokat fogadni – a LUIS egy szövegalapú szolgáltatás). Helyszíni környezetben nem biztos, hogy az ügyfél mindkét tárolót üzembe helyezett, nem feltételezhető, hogy az ügyfél telephelyén található tárolók között történik a vezénylés, és ha mindkét tároló a helyszínen van üzembe adva, mivel ezek helyibbek az ügyfél számára, nem kell először az SR-t visszamenni az ügyfélhez, és az ügyfélnek át kell vinnie a SZÖVEGET a LUIS-hoz.

<br>
</details>

<details>
<summary>
<b>Miért kapunk hibákat a macOS, a Speech-tároló és a Python SDK használatakor?</b>
</summary>

Amikor egy *.wav fájlt* küldünk átírásra, az eredmény a következővel lesz visszahozva:

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

Tudjuk, hogy a websocket megfelelően van beállítva.

**Válasz:** Ha ez a helyzet, tekintse meg ezt [a GitHub-problémát.](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/310) Az itt javasolt megoldás a [következő:](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/310#issuecomment-527542722).

A szén-dioxid ezt kijavította az 1.8-as verzióban.


<br>
</details>

<details>
<summary>
<b>Mi a különbség a Speech-tároló végpontjaiban?</b>
</summary>

Segíthet az alábbi tesztmetrikák kitöltésében, beleértve a tesztelni szükséges függvényeket, valamint az SDK és a REST API-k tesztelését? Különösen az "interaktív" és a "beszélgetés" közötti különbségeket, amelyeket a meglévő dokumentumban/mintában nem láthattam.

| Végpont                                                | Funkcionális teszt                                                   | SDK | REST API |
|---------------------------------------------------------|-------------------------------------------------------------------|-----|----------|
| `/speech/synthesize/cognitiveservices/v1`               | Szövegszintetizálás (szöveg-beszéd)                                  |     | Igen      |
| `/speech/recognition/dictation/cognitiveservices/v1`    | Cognitive Services 1-es websocket-végponthoz        | Igen | Nem       |
| `/speech/recognition/interactive/cognitiveservices/v1`  | A Cognitive Services v1 websocket végpontja  |     |          |
| `/speech/recognition/conversation/cognitiveservices/v1` | A Cognitive Services on-prem conversation v1 websocket végpont |     |          |

**Válasz:** Ez a következők olvad össze:
- A tárolókhoz tartozó diktálás végpontját próbáló személyek (nem tudom, hogyan ták meg ezt az URL-címet)
- Az<sup>1. féltől</sup> származó végpont a tárolóban található végpont.
- Az<sup>1. féltől</sup> származó végpont, amely speech.fragment üzeneteket ad vissza a 3 rd részvégpont által a diktálás végpontja `speech.hypothesis` számára visszaadott üzenetek<sup></sup> helyett.
- A Carbon gyorsútmutatók mind használatban `RecognizeOnce` vannak (interaktív mód)
- A szén-dioxid azt állítja, hogy a igénylést igénylő üzenetek `speech.fragment` nem térnek vissza interaktív módban.
- A szén a kibocsátási buildek során kigyűjti a helyességi adatokat (leoltja a folyamatot).

Megkerülő megoldásként váltson a kódban a folyamatos felismerésre, vagy (gyorsabban) csatlakozzon a tárolóban található interaktív vagy folyamatos végponthoz.
A kódhoz állítsa a végpontot `host:port` a következőre: /speech/recognition/interactive/cognitiveservices/v1

A különböző módokról lásd: Beszédmódok – lásd alább:

## <a name="speech-modes---interactive-conversation-dictation"></a>Beszédmódok – Interaktív, beszélgetés, diktálás

[!INCLUDE [speech-modes](includes/speech-modes.md)]

A megfelelő javítás az SDK 1.8-as verziójával érkezik, amely támogatja a webhelyet (kiválasztja a megfelelő végpontot, így nem lesz rosszabb, mint az online szolgáltatás). Addig is van egy minta a folyamatos felismeréshez. Miért nem mutatunk rá?

https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/6805d96bf69d9e95c9137fe129bc5d81e35f6309/samples/python/console/speech_sample.py#L196

<br>
</details>

<details>
<summary>
<b>Melyik módot használjam a különböző hangfájlokhoz?</b>
</summary>

**Válasz:** Rövid útmutató a [Python használatához.](./get-started-speech-to-text.md?pivots=programming-language-python) A docs webhelyen hivatkozott többi nyelvet is megtalálja.

Csak hogy tisztázza az interaktív, társalgási és diktálási funkciókat; Ez egy speciális módszer annak megadására, hogy a szolgáltatás milyen módon fogja kezelni a beszédkérést. Sajnos a helyi tárolókhoz meg kell adnom a teljes URI-t (mivel a helyi gépet is tartalmazza), ezért ez az információ kiszivárogt az absztrakcióból. Dolgozunk az SDK csapatával, hogy a jövőben még használhatóbb legyen.

<br>
</details>

<details>
<summary>
<b>Hogyan mérheti a tranzakciók/másodperc/magok alapértékét?</b>
</summary>

**Válasz:** Íme néhány, a meglévő modelltől elvárt durva szám (a ga-ban kihozott modellben jobb lesz):

- Fájlok esetében a szabályozás a Speech SDK-ban lesz, 2x. Az első öt másodperc hanganyag nem lesz lehangolt. A dekóder körülbelül 3x valós idejű. Ehhez a teljes CPU-használat közel 2 mag lesz egyetlen felismeréshez.
- A mic esetén a valós idő 1x lesz. Az általános használatnak körülbelül 1 magnak kell lennie egyetlen felismeréshez.

Ezek mind ellenőrizhetők a Docker-naplókból. A sort valójában munkamenet- és kifejezés-/kimondott szövegstatisztikával dobjuk ki, amely tartalmazza az RTF-számokat is.

<br>
</details>

<details>
<summary>
<b>Hogyan, hogy több tároló fusson ugyanazon a gazdagépen?</b>
</summary>

A dokumentum szerint egy másik portot kell elérhetővé tenni, amit én is szeretnék, de a LUIS-tároló továbbra is figyel az 5000-es porton?

**Válasz:** Próbálja ki `-p <outside_unique_port>:5000` a et. Például: `-p 5001:5000`.


<br>
</details>

## <a name="technical-questions"></a>Technikai kérdések

<details>
<summary>
<b>Hogyan kaphatok nem Batch API-kat a &lt; hang 15 másodperces kezeléshez?</b>
</summary>

**Válasz:** `RecognizeOnce()` az interaktív módban legfeljebb 15 másodpercnyi hanganyagot tud feldolgozni, mivel a mód a Beszédparancsoláshoz használható, ahol a kimondott szöveg várhatóan rövid lesz. Ha diktáláshoz vagy beszélgetéshez használja, nincs `StartContinuousRecognition()` 15 másodperces korlát.


<br>
</details>

<details>
<summary>
<b>Melyek az ajánlott erőforrások, a CPU és a RAM; 50 egyidejű kéréshez?</b>
</summary>

Hány egyidejű kérést fog kezelni egy 4 magos, 4 GB RAM? Ha például 50 egyidejű kérést kell kiszolgálni, hány magot és RAM-ot ajánlunk?

**Válasz:** Valós időben, 8 a legújabb verzióval, ezért több Docker-tároló használatát javasoljuk 6 egyidejű `en-US` kérésen túl. 16 magnál nagyobb ítót kap, és nem egységes memóriaelérésű (NUMA) csomópontérzékenysé válik. Az alábbi táblázat ismerteti az egyes Speech-tárolók erőforrásainak minimális és ajánlott lefoglalását.

# <a name="speech-to-text"></a>[Diktálás](#tab/stt)

| Tároló      | Minimális             | Ajánlott         |
|----------------|---------------------|---------------------|
| Diktálás | 2 mag, 2 GB memória | 4 mag, 4 GB memória |

# <a name="custom-speech-to-text"></a>[Egyéni beszédfelismerés](#tab/cstt)

| Tároló             | Minimális             | Ajánlott         |
|-----------------------|---------------------|---------------------|
| Egyéni beszédfelismerés | 2 mag, 2 GB memória | 4 magos, 4 GB memória |

# <a name="text-to-speech"></a>[Szövegfelolvasás](#tab/tts)

| Tároló      | Minimális             | Ajánlott         |
|----------------|---------------------|---------------------|
| Szövegfelolvasás | 1 mag, 2 GB memória | 2 mag, 3 GB memória |

# <a name="custom-text-to-speech"></a>[Egyéni szövegfelismerés](#tab/ctts)

| Tároló             | Minimális             | Ajánlott         |
|-----------------------|---------------------|---------------------|
| Egyéni szövegfelismerés | 1 mag, 2 GB memória | 2 mag, 3 GB memória |

***

- Minden magnak legalább 2,6 GHz-es vagy gyorsabbnak kell lennie.
- Fájlok esetében a szabályozás a Speech SDK-ban, 2x lesz (az első 5 másodperc hanganyag nem lesz lehangolt).
- A dekóder körülbelül 2–3x valós idejű. Ehhez a teljes CPU-használat közel két mag lesz egyetlen felismeréshez. Ezért nem javasoljuk, hogy tárolópéldányonként kettőnél több aktív kapcsolatot tartsunk meg. A szélsőséges oldal körülbelül 10 dekóder, 2x valós időben, egy nyolcmagos gépben, például a következőben: `DS13_V2` . A tároló 1.3-as és újabb verzióihoz egy paramétert is kipróbálhat a `DECODER_MAX_COUNT=20` beállításával.
- A mikrofon 1x valós idejű. Az általános használatnak körülbelül egy magra kell lennie egyetlen felismeréshez.

Vegye figyelembe, hogy hány órányi hanganyaga van. Ha a szám nagy, a megbízhatóság/rendelkezésre állás javítása érdekében javasoljuk, hogy több tárolópéldányt futtason egy dobozon vagy több dobozon, egy terheléselosztási rendszer mögött. A vezénylés a Kubernetes (K8S) és a Helm, vagy a Docker Compose használatával is megtehet.

Például 1000 óra/24 óra kezeléshez 3–4 virtuális gépet próbáltunk meg begépelni, virtuális gépenként 10 példány/dekóder ával.

<br>
</details>

<details>
<summary>
<b>Támogatja a Speech-tároló az írásjeleket?</b>
</summary>

**Válasz:** A prem tárolóban elérhető a kis- és nagybetűsítés (ITN). Az írásjelek nyelvfüggőek, és egyes nyelvek, például a kínai és a japán nyelv esetében nem támogatottak.

A *meglévő* tárolókhoz implicit és alapszintű írásjelek is támogatottak, de ez `off` alapértelmezés szerint az. Ez azt jelenti, hogy a példában szereplő karaktert le lehet kapni, a karaktert `.` `。` nem. Az implicit logika engedélyezéséhez az alábbi példa bemutatja, hogyan teheti ezt meg a Pythonban a Speech SDK-val (ez más nyelveken is hasonló lenne):

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
<b>Miért kapok 404-es hibákat, amikor adatokat szeretnék elküldeni a speech-to-text tárolóba?</b>
</summary>

Példa a HTTP POST-on:

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

**Válasz:** Egyik speech-to-text REST API sem támogatjuk a beszédfelismerést, csak a WebSocketeket támogatjuk a Speech SDK-val. Mindig tekintse meg a hivatalos dokumentációt: [lekérdezés-előrejelzési végpontok.](speech-container-howto.md#query-the-containers-prediction-endpoint)

<br>
</details>


<details>
<summary>
<b> Miért fut a tároló nem gyökér szintű felhasználóként? Milyen problémák léphetnek fel emiatt?</b>
</summary>

**Válasz:** Vegye figyelembe, hogy a tároló alapértelmezett felhasználója nem gyökér szintű felhasználó. Ez védelmet nyújt a tárolót kikerülő folyamatok ellen, és eszkalált engedélyeket szerez be a gazdacsomóponton. Alapértelmezés szerint egyes platformok, például az OpenShift-tárolóplatform ezt már egy tetszőlegesen hozzárendelt felhasználói azonosítóval futtatják a tárolók futtatásával. Ezekben a platformokban a nem gyökér szintű felhasználónak engedéllyel kell rendelkeznie az írást igénylő, külsőleg leképezett kötetre történő íráshoz. Például egy naplózási mappa vagy egy egyéni modell letöltési mappája.
<br>
</details>

<details>
<summary>
<b>A speech-to-text szolgáltatás használata esetén miért jelenik meg ez a hiba?</b>
</summary>

```cmd
Error in STT call for file 9136835610040002161_413008000252496:
{
    "reason": "ResultReason.Canceled",
    "error_details": "Due to service inactivity the client buffer size exceeded. Resetting the buffer. SessionId: xxxxx..."
}
```

**Válasz:** Ez általában akkor fordul elő, ha gyorsabban használhatja a hangot, mint a Speech Recognition-tároló. Az ügyfélpufferek megtelnek, és a megszakítás aktiválódik. Szabályozni kell az egyidejűséget és az RTF-et, amelyen a hangot elküldi.

<br>
</details>

<details>
<summary>
<b>Elmagyarázná ezeket a szöveg-beszéd tárolóhibákat a C++ példákból?</b>
</summary>

**Válasz:** Ha a tároló verziója 1.3-asnál régebbi, akkor ezt a kódot kell használni:

```cpp
const auto endpoint = "http://localhost:5000/speech/synthesize/cognitiveservices/v1";
auto config = SpeechConfig::FromEndpoint(endpoint);
auto synthesizer = SpeechSynthesizer::FromConfig(config);
auto result = synthesizer->SpeakTextAsync("{{{text1}}}").get();
```

A régebbi tárolók nem működnek az API-val való munkához szükséges `FromHost` végponttal. Ha az 1.3-as verzióhoz használt tárolók, akkor ezt a kódot kell használni:

```cpp
const auto host = "http://localhost:5000";
auto config = SpeechConfig::FromHost(host);
config->SetSpeechSynthesisVoiceName(
    "Microsoft Server Speech Text to Speech Voice (en-US, AriaRUS)");
auto synthesizer = SpeechSynthesizer::FromConfig(config);
auto result = synthesizer->SpeakTextAsync("{{{text1}}}").get();
```

Az alábbi példa az API használatát `FromEndpoint` mutatja be:

```cpp
const auto endpoint = "http://localhost:5000/cognitiveservices/v1";
auto config = SpeechConfig::FromEndpoint(endpoint);
config->SetSpeechSynthesisVoiceName(
    "Microsoft Server Speech Text to Speech Voice (en-US, AriaRUS)");
auto synthesizer = SpeechSynthesizer::FromConfig(config);
auto result = synthesizer->SpeakTextAsync("{{{text2}}}").get();
```

 A függvényt azért hívjuk meg, mert a frissített szöveg-beszéd motorral működő tárolókhoz szükség van a `SetSpeechSynthesisVoiceName` hangnévre.

<br>
</details>

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Cognitive Services tárolók](speech-container-howto.md)
