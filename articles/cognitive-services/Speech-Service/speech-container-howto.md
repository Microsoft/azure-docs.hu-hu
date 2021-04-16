---
title: Docker-tárolók telepítése és futtatása a Speech service API-khoz
titleSuffix: Azure Cognitive Services
description: A Speech Service Docker-tárolói segítségével a helyszínen végezhet beszédfelismerést, átírást, generációt és egyéb műveleteket.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/02/2021
ms.author: aahi
ms.custom: cog-serv-seo-aug-2020
keywords: helyszíni, Docker, tároló
ms.openlocfilehash: cb99dc3c5e16ee117df46d7fda0caab9c57f0853
ms.sourcegitcommit: aa00fecfa3ad1c26ab6f5502163a3246cfb99ec3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2021
ms.locfileid: "107388091"
---
# <a name="install-and-run-docker-containers-for-the-speech-service-apis"></a>Docker-tárolók telepítése és futtatása a Speech service API-khoz 

A tárolók lehetővé teszik a Speech szolgáltatás API-jainak a saját környezetében való futtatását. A tárolók kiválóan alkalmasak adott biztonsági és adatszabályozási követelményekhez. Ez a cikk azt mutatja be, hogyan tölthet le, telepíthet és futtathat egy Speech-tárolót.

A Speech-tárolókkal az ügyfelek beszédalapú alkalmazások architektúráját hozhatják létre, amely a robusztus felhőbeli képességekhez és a helyi peremhálózathoz is optimalizálva van. Számos tároló érhető el, amelyek [](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/) díjszabása megegyezik a felhőalapú Azure Speech Services díjszabásával.


> [!IMPORTANT]
> A következő Speech-tárolók mostantól általánosan elérhetők:
> * Standard beszédfelismerés
> * Egyéni beszédfelismerés
> * Standard szöveg beszédfelismerés
> * Neurális szöveg beszédfelismerés
>
> A következő beszédtárolók vannak betekintőben.
> * Egyéni szövegfelismerés
> * Speech Nyelvfelismerés 
>
> A beszédtárolók használatának igényléséhez be kell nyújtania egy online kérést, és jóvá kell hagynia azt. További **információért tekintse** meg az alábbi, Jóváhagyás kérése a tároló futtatásához című szakaszt.

| Tároló | Funkciók | Legutóbbi |
|--|--|--|
| Diktálás | Hangulatelemzést és folyamatos, valós idejű beszéd- vagy kötegelt hangrögzítéseket ír át köztes eredményekkel.  | 2.11.0 |
| Egyéni beszédfelismerés | A [Custom Speech](https://speech.microsoft.com/customspeech)portálról származó egyéni modell használatával folyamatos valós idejű beszédet vagy kötegelt hangfelvételeket ír át szövegre köztes eredményekkel. | 2.11.0 |
| Szövegfelolvasás | Természetes hangú beszédgé alakítja a szöveget egyszerű szövegbevitellel vagy beszédszintézis-jelölőnyelvvel (SSML). | 1.13.0 |
| Egyéni szövegfelismerés | Ha egyéni modellt használ a Custom Voice [portálról,](https://aka.ms/custom-voice-portal)a szöveget természetes hangú beszédgé alakítja egyszerű szöveges bemenettel vagy beszédszintézis-jelölőnyelvvel (SSML). | 1.13.0 |
| Speech Nyelvfelismerés | Hangfájlokban beszélt nyelv felismerése. | 1.0 |
| Neurális szöveg beszédfelismerés | A szöveget természetes hangú beszédgé alakítja mély neurális hálózati technológiával, így természetesebb szintetizált beszédet is lehetővé téve. | 1.5.0 |

Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/cognitive-services/).

## <a name="prerequisites"></a>Előfeltételek

Az alábbi előfeltételek a Speech-tárolók használata előtt:

| Kötelező | Cél |
|--|--|
| A Docker-motor | Telepítenie kell a Docker Engine-t egy [gazdagépre.](#the-host-computer) A Docker csomagokat biztosít a Docker-környezet konfigurálásához [macOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/) és [Linux](https://docs.docker.com/engine/installation/#supported-platforms) rendszereken. A Docker és a tárolók alapszintű ismertetéséért lásd a [Docker felhasználói útmutatóját](https://docs.docker.com/engine/docker-overview/).<br><br> A Docker-t úgy kell konfigurálni, hogy engedélyezze a tárolók számára a kapcsolódást az Azure-hoz, és elküldjék a számlázási adatokat az Azure-nak. <br><br> **Windows rendszeren** a Docker-t is úgy kell konfigurálni, hogy támogassa a Linux-tárolókat.<br><br> |
| A Docker ismerete | Alapszintű ismeretekkel kell rendelkezik a Docker olyan fogalmairól, mint a regisztrációs jegyzékek, adattárak, tárolók és tároló rendszerképek, valamint az alapszintű `docker` parancsok ismerete. |
| Speech-erőforrás | A tárolók csak akkor használhatók, ha:<br><br>Egy Azure _Speech-erőforrás_ a társított API-kulcs és a végpont URI-jának lekérte. Mindkét érték elérhető a Azure Portal **Speech** Overview (Beszéd áttekintése) és a Keys (Kulcsok) oldalon. Mindkettőre szükség van a tároló elindítani.<br><br>**{API_KEY}**: A Kulcsok lapon elérhető két erőforráskulcs **egyike**<br><br>**{ENDPOINT_URI}**: Az Áttekintés oldalon megadott **végpont** |

[!INCLUDE [Gathering required parameters](../containers/includes/container-gathering-required-parameters.md)]

## <a name="the-host-computer"></a>A gazdagép

[!INCLUDE [Host Computer requirements](../../../includes/cognitive-services-containers-host-computer.md)]

### <a name="advanced-vector-extension-support"></a>Speciális vektorbővítmények támogatása

A **gazdagép** az a számítógép, amely a Docker-tárolót futtatja. A *gazdagépnek támogatnia* kell [az Advanced Vector Extensions](https://en.wikipedia.org/wiki/Advanced_Vector_Extensions#CPUs_with_AVX2) (AVX2) bővítményeket. A Linux-gazdagépek AVX2-támogatását a következő paranccsal ellenőrizheti:

```console
grep -q avx2 /proc/cpuinfo && echo AVX2 supported || echo No AVX2 support detected
```
> [!WARNING]
> A gazdagépnek *támogatnia kell* az AVX2-t. A tároló *nem fog megfelelően* működni az AVX2 támogatása nélkül.

### <a name="container-requirements-and-recommendations"></a>Tárolókövetelmények és javaslatok

Az alábbi táblázat az egyes Speech-tárolókhoz minimális és ajánlott erőforrás-kiosztást ismerteti.

| Tároló | Minimális | Ajánlott |
|-----------|---------|-------------|
| Diktálás | 2 mag, 2 GB memória | 4 magos, 4 GB memória |
| Egyéni beszédfelismerés | 2 mag, 2 GB memória | 4 magos, 4 GB memória |
| Szövegfelolvasás | 1 mag, 2 GB memória | 2 mag, 3 GB memória |
| Egyéni szövegfelismerés | 1 mag, 2 GB memória | 2 mag, 3 GB memória |
| Speech Nyelvfelismerés | 1 mag, 1 GB memória | 1 mag, 1 GB memória |
| Neurális szöveg beszédfelismerés | 6 mag, 12 GB memória | 8 mag, 16 GB memória |

* Minden magnak legalább 2,6 GHz-es vagy gyorsabbnak kell lennie.

A mag és a memória a parancs részeként használt `--cpus` és `--memory` beállításoknak `docker run` felel meg.

> [!NOTE]
> A minimális és ajánlott érték a Docker korlátain alapul, *nem a* gazdagép erőforrásain. A beszédfelismerési tárolók például egy nagy nyelvi modell memóriatérkép-részei, ezért ajánlott a teljes fájl elférni a memóriában, ami további 4–6 GB.  Emellett mindkét tároló első futtatása hosszabb időt is igénybe vehet, mivel a modellek a memóriába vannak belaposodva.

## <a name="request-approval-to-the-run-the-container"></a>Jóváhagyás kérése a tároló futtatásához

Töltse ki és küldje el [a kéreleműrlapon,](https://aka.ms/csgate) hogy hozzáférést kérjen a tárolóhoz. 

[!INCLUDE [Request access to public preview](../../../includes/cognitive-services-containers-request-access.md)]


## <a name="get-the-container-image-with-docker-pull"></a>A tároló rendszerképének lekért fájlja a következővel: `docker pull`

A Speech tároló rendszerképe a következő Container Registry.

# <a name="speech-to-text"></a>[Diktálás](#tab/stt)

| Tároló | Adattár |
|-----------|------------|
| Diktálás | `mcr.microsoft.com/azure-cognitive-services/speechservices/speech-to-text:latest` |

# <a name="custom-speech-to-text"></a>[Egyéni beszédfelismerés](#tab/cstt)

| Tároló | Adattár |
|-----------|------------|
| Egyéni beszédfelismerés | `mcr.microsoft.com/azure-cognitive-services/speechservices/custom-speech-to-text:latest` |

# <a name="text-to-speech"></a>[Szövegfelolvasás](#tab/tts)

| Tároló | Adattár |
|-----------|------------|
| Szövegfelolvasás | `mcr.microsoft.com/azure-cognitive-services/speechservices/text-to-speech:latest` |

# <a name="neural-text-to-speech"></a>[Neurális szöveg beszédfelismerés](#tab/ntts)

| Tároló | Adattár |
|-----------|------------|
| Neurális szöveg beszédfelismerés | `mcr.microsoft.com/azure-cognitive-services/speechservices/neural-text-to-speech:latest` |

# <a name="custom-text-to-speech"></a>[Egyéni szövegfelismerés](#tab/ctts)

| Tároló | Adattár |
|-----------|------------|
| Egyéni szövegfelismerés | `mcr.microsoft.com/azure-cognitive-services/speechservices/custom-text-to-speech:latest` |

# <a name="speech-language-detection"></a>[Speech Nyelvfelismerés](#tab/lid)

> [!TIP]
> A leghasznosabb eredmények eléréséhez javasoljuk, hogy a Speech nyelvfelismerési tárolót használja a Speech-to-Text vagy a Custom Speech-to-Text tárolókhoz. 

| Tároló | Adattár |
|-----------|------------|
| Speech Nyelvfelismerés | `mcr.microsoft.com/azure-cognitive-services/speechservices/language-detection:latest` |

***

[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]

### <a name="docker-pull-for-the-speech-containers"></a>Docker-le pull a Speech-tárolókhoz

# <a name="speech-to-text"></a>[Diktálás](#tab/stt)

#### <a name="docker-pull-for-the-speech-to-text-container"></a>Docker-le pull a Speech-to-Text tárolóhoz

A [docker pull paranccsal](https://docs.docker.com/engine/reference/commandline/pull/) töltsön le egy tároló rendszerképét a Microsoft Container Registryből.

```Docker
docker pull mcr.microsoft.com/azure-cognitive-services/speechservices/speech-to-text:latest
```

> [!IMPORTANT]
> A `latest` címke lekérte a `en-US` területi jelölőt. További területi adatokért [lásd: A beszédfelismerés területi mezői.](#speech-to-text-locales)

#### <a name="speech-to-text-locales"></a>Beszédfelismerés területi mezői

A kivételével minden címke `latest` a következő formátumban van, és megkülönbözteti a kis- és nagybetűket:

```
<major>.<minor>.<patch>-<platform>-<locale>-<prerelease>
```

Az alábbi címke egy példa a formátumra:

```
2.6.0-amd64-en-us
```

A **speech-to-text** tároló támogatott területi stb.-éhez tekintse meg a [Speech-to-Text képcímkéket.](../containers/container-image-tags.md#speech-to-text)

# <a name="custom-speech-to-text"></a>[Egyéni beszédfelismerés](#tab/cstt)

#### <a name="docker-pull-for-the-custom-speech-to-text-container"></a>Docker-le pull a Custom Speech-to-Text tárolóhoz

A [docker pull paranccsal](https://docs.docker.com/engine/reference/commandline/pull/) töltsön le egy tároló rendszerképét a Microsoft Container Registryből.

```Docker
docker pull mcr.microsoft.com/azure-cognitive-services/speechservices/custom-speech-to-text:latest
```

> [!NOTE]
> Az `locale` egyéni Speech-tárolók és típusát a tároló által betöltött `voice` egyéni modell határozza meg.

# <a name="text-to-speech"></a>[Szövegfelolvasás](#tab/tts)

#### <a name="docker-pull-for-the-text-to-speech-container"></a>Docker-lekért szöveg beszédfelismerési tárolóhoz

A [docker pull paranccsal](https://docs.docker.com/engine/reference/commandline/pull/) töltsön le egy tároló rendszerképét a Microsoft Container Registryből.

```Docker
docker pull mcr.microsoft.com/azure-cognitive-services/speechservices/text-to-speech:latest
```

> [!IMPORTANT]
> A `latest` címke lekérte `en-US` a területi jelölőt és `ariarus` a hangot. További területi adatokért [lásd: Szöveg-beszéd területi adatok.](#text-to-speech-locales)

#### <a name="text-to-speech-locales"></a>Szöveg-beszéd beszéddel való beszéddel való beszéd területi

A kivételével minden címke `latest` a következő formátumban van, és megkülönbözteti a kis- és nagybetűket:

```
<major>.<minor>.<patch>-<platform>-<locale>-<voice>-<prerelease>
```

Az alábbi címke egy példa a formátumra:

```
1.8.0-amd64-en-us-ariarus
```

A támogatott területi helyeket és  a szöveg-beszéd tároló megfelelő hangját lásd: [Szöveg-beszéd képcímkék.](../containers/container-image-tags.md#text-to-speech)

> [!IMPORTANT]
> Szöveg beszédfelismerési HTTP *POST-üzenet* írása során a Beszédszintézis-jelölőnyelv [(SSML)](speech-synthesis-markup.md) üzenethez attribútummal kell `voice` `name` felépíteni egy elemet. Az érték a tároló megfelelő területi és hangalapú értéke, más néven [a "rövid név".](language-support.md#standard-voices) Például a `latest` címke hangneve `en-US-AriaRUS` .

# <a name="neural-text-to-speech"></a>[Neurális szöveg beszédfelismerés](#tab/ntts)

#### <a name="docker-pull-for-the-neural-text-to-speech-container"></a>Docker-lekért szöveg beszédfelismerési tárolóhoz

A [docker pull paranccsal](https://docs.docker.com/engine/reference/commandline/pull/) töltsön le egy tároló rendszerképét a Microsoft Container Registryből.

```Docker
docker pull mcr.microsoft.com/azure-cognitive-services/speechservices/neural-text-to-speech:latest
```

> [!IMPORTANT]
> A `latest` címke lekérte `en-US` a területi jelölőt és `arianeural` a hangot. További területi adatokért [lásd: Neurális szöveg beszédfelismerés területi mezői.](#neural-text-to-speech-locales)

#### <a name="neural-text-to-speech-locales"></a>Neurális szöveg-beszéd területi

A kivételével minden címke `latest` a következő formátumban van, és megkülönbözteti a kis- és nagybetűket:

```
<major>.<minor>.<patch>-<platform>-<locale>-<voice>
```

Az alábbi címke egy példa a formátumra:

```
1.3.0-amd64-en-us-arianeural
```

A támogatott területi helyeket és a neurális **szöveg-beszéd** tároló megfelelő hangját lásd: Neurális szöveg [beszédfelismerési képcímkék.](../containers/container-image-tags.md#neural-text-to-speech)

> [!IMPORTANT]
> Neurális szöveg beszédfelismerési HTTP *POST-ként* való felépítéséhez a Beszédszintézis-jelölőnyelv [(SSML)](speech-synthesis-markup.md) üzenethez attribútummal kell `voice` felépíteni egy `name` elemet. Az érték a tároló megfelelő területi és hangalapú értéke, más néven [a "rövid név".](language-support.md#neural-voices) Például a `latest` címke hangneve `en-US-AriaNeural` .

# <a name="custom-text-to-speech"></a>[Egyéni szövegfelismerés](#tab/ctts)

#### <a name="docker-pull-for-the-custom-text-to-speech-container"></a>Docker-lekért üzenet a Custom Text-to-Speech tárolóhoz

A [docker pull paranccsal](https://docs.docker.com/engine/reference/commandline/pull/) töltsön le egy tároló rendszerképét a Microsoft Container Registryből.

```Docker
docker pull mcr.microsoft.com/azure-cognitive-services/speechservices/custom-text-to-speech:latest
```

> [!NOTE]
> Az `locale` egyéni Speech-tárolók és típusát a tároló által betöltött `voice` egyéni modell határozza meg.

# <a name="speech-language-detection"></a>[Speech Nyelvfelismerés](#tab/lid)

#### <a name="docker-pull-for-the-speech-language-detection-container"></a>Docker-lekért speech Nyelvfelismerés tárolóhoz

A [docker pull paranccsal](https://docs.docker.com/engine/reference/commandline/pull/) töltsön le egy tároló rendszerképét a Microsoft Container Registryből.

```Docker
docker pull mcr.microsoft.com/azure-cognitive-services/speechservices/language-detection:latest
```

***

## <a name="how-to-use-the-container"></a>A tároló használata

Ha a tároló már a gazdagépen [van,](#the-host-computer)a következő folyamattal dolgozhat a tárolóval.

1. [Futtassa a tárolót](#run-the-container-with-docker-run)a szükséges számlázási beállításokkal. További [példák](speech-container-configuration.md#example-docker-run-commands) érhetők el `docker run` a parancsra.
1. [A tároló előrejelzési végpontjának lekérdezése.](#query-the-containers-prediction-endpoint)

## <a name="run-the-container-with-docker-run"></a>Futtassa a tárolót a következővel: `docker run`

Futtassa a tárolót a [docker run](https://docs.docker.com/engine/reference/commandline/run/) paranccsal. A és [az érték lekért](#gathering-required-parameters) részleteiért tekintse meg a szükséges paraméterek `{Endpoint_URI}` összegyűjtését. `{API_Key}` A [parancsra](speech-container-configuration.md#example-docker-run-commands) további `docker run` példák is elérhetők.

# <a name="speech-to-text"></a>[Diktálás](#tab/stt)

A Standard *Speech-to-Text tároló futtatásához* hajtsa végre a következő `docker run` parancsot.

```bash
docker run --rm -it -p 5000:5000 --memory 4g --cpus 4 \
mcr.microsoft.com/azure-cognitive-services/speechservices/speech-to-text \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

A parancs a következőket hajtja végre:

* Egy *Speech-to-Text tárolót futtat* a tároló rendszerképből.
* 4 processzormagot és 4 gigabájt (GB) memóriát foglal le.
* Elérhetővé teszi az 5000-es TCP-portot, és lefoglal egy pszeudo-TTY-t a tárolóhoz.
* A rendszer a kilépés után automatikusan eltávolítja a tárolót. A tároló rendszerképe továbbra is elérhető a gazdagépen.

> [!NOTE]
> A tárolók támogatják a Speech SDK tömörített hangbemenetét a GStreamer használatával.
> A GStreamer tárolóban való telepítéséhez kövesse a Linux GStreamerre vonatkozó utasításait a Codec tömörített hangbemenet használata a [Speech SDK-val.](how-to-use-codec-compressed-audio-input-streams.md)

#### <a name="diarization-on-the-speech-to-text-output"></a>A beszédfelismerés kimenetének diarizációja
A mellékjelek alapértelmezés szerint engedélyezve vannak. A válaszban a diarizáláshoz használja a `diarize_speech_config.set_service_property` következőt: .

1. A kifejezés kimeneti formátumát állítsa a `Detailed` következőre: .
2. Állítsa be a mellékjelek módját. A támogatott módok a `Identity` és `Anonymous` a .
```python
diarize_speech_config.set_service_property(
    name='speechcontext-PhraseOutput.Format',
    value='Detailed',
    channel=speechsdk.ServicePropertyChannel.UriQueryParameter
)

diarize_speech_config.set_service_property(
    name='speechcontext-phraseDetection.speakerDiarization.mode',
    value='Identity',
    channel=speechsdk.ServicePropertyChannel.UriQueryParameter
)
```
> [!NOTE]
> Az "Identitás" mód vagy értéket `"SpeakerId": "Customer"` ad `"SpeakerId": "Agent"` vissza.
> A "Névtelen" mód vagy értéket `"SpeakerId": "Speaker 1"` ad vissza `"SpeakerId": "Speaker 2"`


#### <a name="analyze-sentiment-on-the-speech-to-text-output"></a>Hangulatelemzés a beszédfelismerés kimenetében 
A speech-to-text tároló 2.6.0-s verziótól kezdődően az előzetes verzió helyett a TextAnalytics 3.0 API-végpontot kell használnia. Példa:
* `https://westus2.api.cognitive.microsoft.com/text/analytics/v3.0/sentiment`
* `https://localhost:5000/text/analytics/v3.0/sentiment`

> [!NOTE]
> A Text Analytics API visszamenőlegesen nem kompatibilis `v3.0` a `v3.0-preview.1` Text Analytics. A legújabb hangulati funkció támogatásához használja a speech-to-text tároló rendszerképét, és `v2.6.0` `v3.0` Text Analytics.

A speech-to-text tároló 2.2.0-s verziótól kezdődően a kimeneten hívhatja meg a [hangulatelemzési v3](../text-analytics/how-tos/text-analytics-how-to-sentiment-analysis.md) API-t. A hangulatelemzés hívásához szüksége lesz egy Text Analytics API-erőforrásvégpontra. Például: 
* `https://westus2.api.cognitive.microsoft.com/text/analytics/v3.0-preview.1/sentiment`
* `https://localhost:5000/text/analytics/v3.0-preview.1/sentiment`

Ha a felhőben fér hozzá egy Text Analytics-végponthoz, szüksége lesz egy kulcsra. Ha helyileg futtatja a Text Analytics, előfordulhat, hogy ezt nem kell adnia.

A kulcsot és a végpontot argumentumként adhatja át a Speech-tárolónak, ahogy az alábbi példában is.

```bash
docker run -it --rm -p 5000:5000 \
mcr.microsoft.com/azure-cognitive-services/speechservices/speech-to-text:latest \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY} \
CloudAI:SentimentAnalysisSettings:TextAnalyticsHost={TEXT_ANALYTICS_HOST} \
CloudAI:SentimentAnalysisSettings:SentimentAnalysisApiKey={SENTIMENT_APIKEY}
```

A parancs a következőket hajtja végre:

* A fenti paranccsal azonos lépéseket hajt végre.
* Egy Text Analytics API-végpontot és -kulcsot tárol, amely hangulatelemzési kéréseket küld. 

#### <a name="phraselist-v2-on-the-speech-to-text-output"></a>Phraselist v2 a speech-to-text kimeneten 

A speech-to-text tároló 2.6.0-s és 2.0-s vagy 2.0-s vagy 2.0-s vagy középső kifejezésekkel való kimenete a saját kifejezésekkel is lekért. Például a *magas férfi a* következő mondatban:

* "Ez egy mondat, amely a **magas férfi,** ez egy újabb mondat."

Kifejezéslista konfigurálásához saját kifejezéseket kell hozzáadnia a híváskor. Például:

```python
    phrase="the tall man"
    recognizer = speechsdk.SpeechRecognizer(
        speech_config=dict_speech_config,
        audio_config=audio_config)
    phrase_list_grammer = speechsdk.PhraseListGrammar.from_recognizer(recognizer)
    phrase_list_grammer.addPhrase(phrase)
    
    dict_speech_config.set_service_property(
        name='setflight',
        value='xonlineinterp',
        channel=speechsdk.ServicePropertyChannel.UriQueryParameter
    )
```

Ha több kifejezést is hozzá szeretne adni, hívja meg az egyes kifejezéseket, hogy hozzáadja őket a `.addPhrase()` kifejezéslistához. 


# <a name="custom-speech-to-text"></a>[Egyéni beszédfelismerés](#tab/cstt)

A *Custom Speech-to-Text tároló* egy egyéni beszédmodellre támaszkodik. Az egyéni modellt az [](how-to-custom-speech-train-model.md) egyéni beszédportállal [kell betanítanunk.](https://speech.microsoft.com/customspeech)

A tároló **futtatásához** szükség van az egyéni beszédmodell-azonosítóra. Ez az egyéni **beszédportál Betanítás** lapján található. Az egyéni beszédportálon lépjen a **Betanítás oldalra,** és válassza ki a modellt.
<br>

![Egyéni beszéd betanítás lapja](media/custom-speech/custom-speech-model-training.png)

Szerezze be **a** parancs paraméterének argumentumaként használni szükséges `ModelId` `docker run` modellazonosítót.
<br>

![Egyéni beszédmodell részletei](media/custom-speech/custom-speech-model-details.png)

Az alábbi táblázat a különböző `docker run` paramétereket és a hozzájuk tartozó leírásokat tartalmazza:

| Paraméter | Leírás |
|---------|---------|
| `{VOLUME_MOUNT}` | A gazdagép [kötetének csatlakoztatása,](https://docs.docker.com/storage/volumes/)amelyet a Docker az egyéni modell megőrzéséhez használ. Például: *C:\CustomSpeech,* ahol a *C meghajtó* a gazdagépen található. |
| `{MODEL_ID}` | A Custom Speech Model  **azonosítója** az egyéni beszédportál Betanítás lapján. |
| `{ENDPOINT_URI}` | A végpont a méréshez és a számlázáshoz szükséges. További információkért lásd a szükséges [paraméterek összegyűjtését.](#gathering-required-parameters) |
| `{API_KEY}` | Az API-kulcs szükséges. További információkért lásd a szükséges [paraméterek összegyűjtését.](#gathering-required-parameters) |

A *Custom Speech-to-Text tároló futtatásához* futtassa a következő `docker run` parancsot:

```bash
docker run --rm -it -p 5000:5000 --memory 4g --cpus 4 \
-v {VOLUME_MOUNT}:/usr/local/models \
mcr.microsoft.com/azure-cognitive-services/speechservices/custom-speech-to-text \
ModelId={MODEL_ID} \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

A parancs a következőket hajtja végre:

* Egy *Custom Speech-to-Text tárolót* futtat a tároló rendszerképből.
* 4 processzormagot és 4 gigabájt (GB) memóriát foglal le.
* Betölti *a Custom Speech-to-Text modellt* a kötetbemeneti csatlakoztatásból, *például: C:\CustomSpeech.*
* Elérhetővé teszi az 5000-es TCP-portot, és egy pszeudo-TTY-t foglal le a tárolóhoz.
* Letölti a modellt a `ModelId` (ha nem található a kötet-csatlakoztatáson).
* Ha az egyéni modellt korábban már letöltötte, a rendszer `ModelId` figyelmen kívül hagyja a típust.
* A rendszer automatikusan eltávolítja a tárolót, miután az kilép. A tároló rendszerképe továbbra is elérhető a gazdagépen.


#### <a name="base-model-download-on-the-custom-speech-to-text-container"></a>Alapmodell letöltése az egyéni beszédfelismerési tárolóra  
A custom-speech-to-text tároló 2.6.0-s v2.0-s típusával kezdődően az alapmodellhez elérhető információkat a kapcsoló használatával kaphatja `BaseModelLocale=<locale>` meg. Ezzel a lehetőséggel megjelenik az elérhető alapmodellek listája a számlázási fiók területi beállításában. Például:

```bash
docker run --rm -it \
mcr.microsoft.com/azure-cognitive-services/speechservices/custom-speech-to-text \
BaseModelLocale={LOCALE} \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

A parancs a következőket hajtja végre:

* Egy *Custom Speech-to-Text tárolót* futtat a tároló rendszerképből.
* Ellenőrizze és adja vissza a célterület elérhető alapmodelljéit.

A kimenet tartalmazza az alapmodellek listáját az információk területi értékekkel, a modell azonosítójával és a létrehozás dátumával. A modellazonosítóval letöltheti és használhatja az Önnek megfelelő alapmodellt. Például:
```
Checking available base model for en-us
2020/10/30 21:54:20 [Info] Searching available base models for en-us
2020/10/30 21:54:21 [Info] [Base model] Locale: en-us, CreatedDate: 2016-11-04T08:23:42Z, Id: a3d8aab9-6f36-44cd-9904-b37389ce2bfa
2020/10/30 21:54:21 [Info] [Base model] Locale: en-us, CreatedDate: 2016-11-04T12:01:02Z, Id: cc7826ac-5355-471d-9bc6-a54673d06e45
2020/10/30 21:54:21 [Info] [Base model] Locale: en-us, CreatedDate: 2017-08-17T12:00:00Z, Id: a1f8db59-40ff-4f0e-b011-37629c3a1a53
2020/10/30 21:54:21 [Info] [Base model] Locale: en-us, CreatedDate: 2018-04-16T11:55:00Z, Id: c7a69da3-27de-4a4b-ab75-b6716f6321e5
2020/10/30 21:54:21 [Info] [Base model] Locale: en-us, CreatedDate: 2018-09-21T15:18:43Z, Id: da494a53-0dad-4158-b15f-8f9daca7a412
2020/10/30 21:54:21 [Info] [Base model] Locale: en-us, CreatedDate: 2018-10-19T11:28:54Z, Id: 84ec130b-d047-44bf-a46d-58c1ac292ca7
2020/10/30 21:54:21 [Info] [Base model] Locale: en-us, CreatedDate: 2018-11-26T07:59:09Z, Id: ee5c100f-152f-4ae5-9e9d-014af3c01c56
2020/10/30 21:54:21 [Info] [Base model] Locale: en-us, CreatedDate: 2018-11-26T09:21:55Z, Id: d04959a6-71da-4913-9997-836793e3c115
2020/10/30 21:54:21 [Info] [Base model] Locale: en-us, CreatedDate: 2019-01-11T10:04:19Z, Id: 488e5f23-8bc5-46f8-9ad8-ea9a49a8efda
2020/10/30 21:54:21 [Info] [Base model] Locale: en-us, CreatedDate: 2019-02-18T14:37:57Z, Id: 0207b3e6-92a8-4363-8c0e-361114cdd719
2020/10/30 21:54:21 [Info] [Base model] Locale: en-us, CreatedDate: 2019-03-03T17:34:10Z, Id: 198d9b79-2950-4609-b6ec-f52254074a05
2020/10/30 21:54:21 [Fatal] Please run this tool again and assign --modelId '<one above base model id>'. If no model id listed above, it means currently there is no available base model for en-us
```

#### <a name="custom-pronunciation-on-the-custom-speech-to-text-container"></a>Egyéni kiejtés az egyéni beszédfelismerési tárolón 
A custom-speech-to-text tároló 2.5.0-s v2.0-ástól kezdődően egyéni kiejtési eredményt kaphat a kimenetben. Mindössze be kell állítania a saját egyéni kiejtési szabályait az egyéni modellben, és csatlakoztatnia kell a modellt a custom-speech-to-text tárolóhoz.


# <a name="text-to-speech"></a>[Szövegfelolvasás](#tab/tts)

A Standard *Text-to-Speech tároló futtatásához* hajtsa végre a következő `docker run` parancsot.

```bash
docker run --rm -it -p 5000:5000 --memory 2g --cpus 1 \
mcr.microsoft.com/azure-cognitive-services/speechservices/text-to-speech \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

A parancs a következőket hajtja végre:

* Egy Standard *text-to-speech tárolót futtat* a tároló rendszerképből.
* 1 processzormagot és 2 gigabájt (GB) memóriát foglal le.
* Elérhetővé teszi az 5000-es TCP-portot, és lefoglal egy pszeudo-TTY-t a tárolóhoz.
* A rendszer a kilépés után automatikusan eltávolítja a tárolót. A tároló rendszerképe továbbra is elérhető a gazdagépen.

# <a name="neural-text-to-speech"></a>[Neurális szöveg beszédfelismerés](#tab/ntts)

A neurális *szöveg beszédfelismerési tároló futtatásához* hajtsa végre a következő `docker run` parancsot.

```bash
docker run --rm -it -p 5000:5000 --memory 12g --cpus 6 \
mcr.microsoft.com/azure-cognitive-services/speechservices/neural-text-to-speech \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

A parancs a következőket hajtja végre:

* Egy *neurális szöveg-beszéd tárolót futtat* a tároló rendszerképből.
* 6 processzormagot és 12 gigabájt (GB) memóriát foglal le.
* Elérhetővé teszi az 5000-es TCP-portot, és lefoglal egy pszeudo-TTY-t a tárolóhoz.
* A rendszer a kilépés után automatikusan eltávolítja a tárolót. A tároló rendszerképe továbbra is elérhető a gazdagépen.

# <a name="custom-text-to-speech"></a>[Egyéni szövegfelismerés](#tab/ctts)

A *Custom Text-to-Speech tároló* egy egyéni hangmodellre támaszkodik. Az egyéni modellt az [](how-to-custom-voice-create-voice.md) egyéni hangportállal [kell betanítanunk.](https://aka.ms/custom-voice-portal) A tároló **futtatásához** szükség van az egyéni hangmodell-azonosítóra. Ez az egyéni hangportál **Betanítás** lapján található. Az egyéni hangportálon lépjen a **Betanítás oldalra,** és válassza ki a modellt.
<br>

![Egyéni hangbeképzési oldal](media/custom-voice/custom-voice-model-training.png)

Szerezze be **a** docker run parancs paraméterének argumentumaként használni szükséges `ModelId` modellazonosítót.
<br>

![Egyéni hangmodell részletei](media/custom-voice/custom-voice-model-details.png)

Az alábbi táblázat a különböző `docker run` paramétereket és a hozzájuk tartozó leírásokat tartalmazza:

| Paraméter | Leírás |
|---------|---------|
| `{VOLUME_MOUNT}` | A gazdagép [kötetének csatlakoztatása,](https://docs.docker.com/storage/volumes/)amelyet a Docker az egyéni modell megőrzéséhez használ. Például: *C:\CustomSpeech,* ahol a *C meghajtó* a gazdagépen található. |
| `{MODEL_ID}` | A Custom **Speech-modell azonosítója** **az** egyéni hangportál Betanítás lapján. |
| `{ENDPOINT_URI}` | A végpont a méréshez és a számlázáshoz szükséges. További információkért lásd a szükséges [paraméterek összegyűjtését.](#gathering-required-parameters) |
| `{API_KEY}` | Az API-kulcs szükséges. További információkért lásd a szükséges [paraméterek összegyűjtését.](#gathering-required-parameters) |

A Custom *Text-to-Speech tároló futtatásához* futtassa a következő `docker run` parancsot:

```bash
docker run --rm -it -p 5000:5000 --memory 2g --cpus 1 \
-v {VOLUME_MOUNT}:/usr/local/models \
mcr.microsoft.com/azure-cognitive-services/speechservices/custom-text-to-speech \
ModelId={MODEL_ID} \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

A parancs a következőket hajtja végre:

* Egyéni *szöveg-beszéd tárolót futtat* a tároló rendszerképből.
* 1 processzormagot és 2 gigabájt (GB) memóriát foglal le.
* Betölti a *Custom Text-to-Speech* modellt a kötetbemeneti csatlakoztatásból, *például: C:\CustomVoice.*
* Elérhetővé teszi az 5000-es TCP-portot, és lefoglal egy pszeudo-TTY-t a tárolóhoz.
* Letölti a modellt a megadott alapján `ModelId` (ha nem található a kötet-csatlakoztatáson).
* Ha az egyéni modellt korábban letöltötte, a rendszer figyelmen `ModelId` kívül hagyja a típust.
* A rendszer a kilépés után automatikusan eltávolítja a tárolót. A tároló rendszerképe továbbra is elérhető a gazdagépen.

# <a name="speech-language-detection"></a>[Speech Nyelvfelismerés](#tab/lid)

A *Speech* Nyelvfelismerés futtassa a következő `docker run` parancsot.

```bash
docker run --rm -it -p 5003:5003 --memory 1g --cpus 1 \
mcr.microsoft.com/azure-cognitive-services/speechservices/language-detection \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

A parancs a következőket hajtja végre: 

* Egy beszédnyelv-észlelési tárolót futtat a tároló rendszerképből. Jelenleg nem számítunk fel díjat a rendszerkép futtatásáért.
* 1 processzormagot és 1 gigabájt (GB) memóriát foglal le.
* Elérhetővé teszi az 5003-as TCP-portot, és lefoglal egy pszeudo-TTY-t a tároló számára.
* A rendszer a kilépés után automatikusan eltávolítja a tárolót. A tároló rendszerképe továbbra is elérhető a gazdagépen.

Ha csak Speech Nyelvfelismerés küld, a Speech-ügyfél értékét értékre kell `phraseDetection` `None` állítania.  

```python
speech_config.set_service_property(
      name='speechcontext-phraseDetection.Mode',
      value='None',
      channel=speechsdk.ServicePropertyChannel.UriQueryParameter
   )
```

Ha ezt a tárolót a speech-to-text tárolóval szeretné futtatni, használhatja ezt a [Docker-rendszerképet.](https://hub.docker.com/r/antsu/on-prem-client) Miután mindkét tároló elindult, használja ezt a Docker Run parancsot a `speech-to-text-with-languagedetection-client` futtatásához.

```Docker
docker run --rm -v ${HOME}:/root -ti antsu/on-prem-client:latest ./speech-to-text-with-languagedetection-client ./audio/LanguageDetection_en-us.wav --host localhost --lport 5003 --sport 5000
```

> [!NOTE]
> Az egyidejű hívások számának növelése befolyásolhatja a megbízhatóságot és a késést. A nyelvfelismeréshez legfeljebb 4 egyidejű hívást javasolunk 1 PROCESSZOR és 1 GB memória használatával. A 2 PROCESSZORt és 2 GB memóriát tartalmazó gazdagépek esetén legfeljebb 6 egyidejű hívás használatát javasoljuk.

***

> [!IMPORTANT]
> A , és beállításokat meg kell adni a tároló futtatásához; ellenkező esetben a `Eula` `Billing` tároló nem indul `ApiKey` el.  További információ: [Számlázás.](#billing)

## <a name="query-the-containers-prediction-endpoint"></a>A tároló előrejelzési végpontjának lekérdezése

> [!NOTE]
> Használjon egyedi portszámot, ha több tárolót futtat.

| Tárolók | SDK-gazdagép URL-címe | Protokoll |
|--|--|--|
| Standard beszédfelismerés és custom speech-to-text | `ws://localhost:5000` | WS |
| Szöveg-beszéd (beleértve a standard, egyéni és neurális nyelveket), Beszédfelismerés | `http://localhost:5000` | HTTP |

További információ a WSS és a HTTPS protokollok használatával kapcsolatban: [tárolóbiztonság.](../cognitive-services-container-support.md#azure-cognitive-services-container-security)

### <a name="speech-to-text-standard-and-custom"></a>Beszédfelismerés (Standard és Egyéni)

[!INCLUDE [Query Speech-to-text container endpoint](includes/speech-to-text-container-query-endpoint.md)]

#### <a name="analyze-sentiment"></a>Vélemények elemzése

Ha a Text Analytics API hitelesítő adatait a tárolónak [küldte,](#analyze-sentiment-on-the-speech-to-text-output)a Speech SDK-val beszédfelismerési kéréseket küldhet hangulatelemzéssel. Az API-válaszokat egyszerű vagy  részletes formátumra *is konfigurálhatja.*
> [!NOTE]
> A Speech Service Python SDK 1.13-as verziójának hangulatelemzési problémája van. Ha a Speech Service Python SDK-ban hangulatelemzést használ, használja az 1.12.x vagy korábbi verzió használatát.

# <a name="simple-format"></a>[Egyszerű formátum](#tab/simple-format)

Ha a Speech-ügyfelet egyszerű formátumra szeretne konfigurálni, adja hozzá a értéket `"Sentiment"` a `Simple.Extensions` értékéhez. Ha a modell egy adott verzióját Text Analytics választani, cserélje le a helyére a `'latest'` `speechcontext-phraseDetection.sentimentAnalysis.modelversion` tulajdonságkonfigurációt.

```python
speech_config.set_service_property(
    name='speechcontext-PhraseOutput.Simple.Extensions',
    value='["Sentiment"]',
    channel=speechsdk.ServicePropertyChannel.UriQueryParameter
)
speech_config.set_service_property(
    name='speechcontext-phraseDetection.sentimentAnalysis.modelversion',
    value='latest',
    channel=speechsdk.ServicePropertyChannel.UriQueryParameter
)
```

`Simple.Extensions` A visszaadja a hangulati eredményt a válasz gyökérrétegében.

```json
{
   "DisplayText":"What's the weather like?",
   "Duration":13000000,
   "Id":"6098574b79434bd4849fee7e0a50f22e",
   "Offset":4700000,
   "RecognitionStatus":"Success",
   "Sentiment":{
      "Negative":0.03,
      "Neutral":0.79,
      "Positive":0.18
   }
}
```

# <a name="detailed-format"></a>[Részletes formátum](#tab/detailed-format)

Ahhoz, hogy a Speech-ügyfelet részletes formátum használatára konfigurálja, adja hozzá a értéket a , vagy `"Sentiment"` `Detailed.Extensions` mindkettő `Detailed.Options` értékéhez. Ha egy adott modellverziót Text Analytics választani, cserélje le a helyére a `'latest'` `speechcontext-phraseDetection.sentimentAnalysis.modelversion` tulajdonságkonfigurációt.

```python
speech_config.set_service_property(
    name='speechcontext-PhraseOutput.Detailed.Options',
    value='["Sentiment"]',
    channel=speechsdk.ServicePropertyChannel.UriQueryParameter
)
speech_config.set_service_property(
    name='speechcontext-PhraseOutput.Detailed.Extensions',
    value='["Sentiment"]',
    channel=speechsdk.ServicePropertyChannel.UriQueryParameter
)
speech_config.set_service_property(
    name='speechcontext-phraseDetection.sentimentAnalysis.modelversion',
    value='latest',
    channel=speechsdk.ServicePropertyChannel.UriQueryParameter
)
```

`Detailed.Extensions` A hangulati eredményt ad a válasz gyökérrétegében. `Detailed.Options` A a válasz `NBest` rétegében biztosítja az eredményt. Külön vagy együtt is használhatók.

```json
{
   "DisplayText":"What's the weather like?",
   "Duration":13000000,
   "Id":"6a2aac009b9743d8a47794f3e81f7963",
   "NBest":[
      {
         "Confidence":0.973695,
         "Display":"What's the weather like?",
         "ITN":"what's the weather like",
         "Lexical":"what's the weather like",
         "MaskedITN":"What's the weather like",
         "Sentiment":{
            "Negative":0.03,
            "Neutral":0.79,
            "Positive":0.18
         }
      },
      {
         "Confidence":0.9164971,
         "Display":"What is the weather like?",
         "ITN":"what is the weather like",
         "Lexical":"what is the weather like",
         "MaskedITN":"What is the weather like",
         "Sentiment":{
            "Negative":0.02,
            "Neutral":0.88,
            "Positive":0.1
         }
      }
   ],
   "Offset":4700000,
   "RecognitionStatus":"Success",
   "Sentiment":{
      "Negative":0.03,
      "Neutral":0.79,
      "Positive":0.18
   }
}
```

---

Ha teljesen le szeretné tiltani a hangulatelemzést, adjon hozzá egy értéket a `false` `sentimentanalysis.enabled` fájlhoz.

```python
speech_config.set_service_property(
    name='speechcontext-phraseDetection.sentimentanalysis.enabled',
    value='false',
    channel=speechsdk.ServicePropertyChannel.UriQueryParameter
)
```

### <a name="text-to-speech-standard-neural-and-custom"></a>Szöveg-beszéd (Standard, Neurális és Egyéni)

[!INCLUDE [Query Text-to-speech container endpoint](includes/text-to-speech-container-query-endpoint.md)]

### <a name="run-multiple-containers-on-the-same-host"></a>Több tároló futtatása ugyanazon a gazdagépen

Ha több, elérhető portokkal rendelkező tárolót kíván futtatni, minden tárolót külön-külön elérhető porttal kell futtatnia. Futtassa például az első tárolót az 5000-es porton, a másodikat pedig az 5001-es porton.

Ez a tároló és egy másik Azure Cognitive Services futhat együtt a GAZDAGÉPen. Ugyanazon tárolóhoz több tároló is Cognitive Services futhat.

[!INCLUDE [Validate container is running - Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="stop-the-container"></a>A tároló leállítása

[!INCLUDE [How to stop the container](../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>Hibaelhárítás

A tároló megkezdésekor vagy futtatásakor problémákat tapasztalhat. Használjon kimeneti [csatlakoztatást,](speech-container-configuration.md#mount-settings) és engedélyezze a naplózást. Ez lehetővé teszi, hogy a tároló olyan naplófájlokat hozzon létre, amelyek hasznosak lehetnek a problémák elhárítása során.

[!INCLUDE [Cognitive Services FAQ note](../containers/includes/cognitive-services-faq-note.md)]

## <a name="billing"></a>Számlázás

A Speech-tárolók számlázási adatokat küldenek az Azure-nak egy *Speech-erőforrás használatával* az Azure-fiókban.

[!INCLUDE [Container's Billing Settings](../../../includes/cognitive-services-containers-how-to-billing-info.md)]

További információ ezekről a lehetőségekről: [Tárolók konfigurálása.](speech-container-configuration.md)

## <a name="summary"></a>Összefoglalás

Ebben a cikkben a Speech-tárolók letöltésével, telepítésével és futtatásával kapcsolatos fogalmakat és munkafolyamatokat sajátta el. Összegezve:

* A Speech négy Linux-tárolót biztosít a Dockerhez, amelyek különböző képességeket tartalmaznak:
  * *Diktálás*
  * *Egyéni beszédfelismerés*
  * *Szövegfelolvasás*
  * *Egyéni szövegfelismerés*
  * *Neurális szöveg beszédfelismerés*
  * *Speech Nyelvfelismerés*
* A tároló rendszerképeket az Azure-beli tároló-beállításjegyzékből töltheti le.
* A tároló rendszerképe a Dockerben fut.
* Akár a REST API (csak szöveg beszédfelismeréshez) vagy az SDK-t (Beszédfelismerés vagy Szövegfelismerés) használja, meg kell adnia a tároló gazdagépének URI-ját. 
* A tárolók példányosulásakor meg kell adnia a számlázási adatokat.

> [!IMPORTANT]
>  Cognitive Services tárolók nem futnak az Azure-hoz való csatlakozás nélkül mérésre. Az ügyfeleknek lehetővé kell tenniük, hogy a tárolók mindig kommunikálják a számlázási adatokat a mérési szolgáltatással. Cognitive Services tárolók nem küldenek ügyféladatokat (például az elemzett képet vagy szöveget) a Microsoftnak.

## <a name="next-steps"></a>Következő lépések

* Tekintse át [a tárolók konfigurációs](speech-container-configuration.md) beállításokhoz való konfigurálásának áttekintését
* Megtudhatja, [hogyan használhatja a Speech service-tárolókat a Kubernetes és a Helm használatával](speech-container-howto-on-premises.md)
* További Cognitive Services [használata](../cognitive-services-container-support.md)
