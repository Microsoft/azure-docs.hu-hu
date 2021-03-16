---
title: Beszéd és szöveg közötti API-referenciák (REST) – beszédfelismerési szolgáltatás
titleSuffix: Azure Cognitive Services
description: Megtudhatja, hogyan használhatja a beszéd – szöveg REST API. Ebben a cikkben megismerheti az engedélyezési lehetőségeket, a lekérdezési lehetőségeket, a kérések strukturálása és a válasz fogadását.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 01/08/2021
ms.author: trbye
ms.custom: devx-track-csharp
ms.openlocfilehash: 90a4ad068f332b198696d96bddbb794c820f42f5
ms.sourcegitcommit: 18a91f7fe1432ee09efafd5bd29a181e038cee05
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/16/2021
ms.locfileid: "103564388"
---
# <a name="speech-to-text-rest-api"></a>Diktálás REST API

A beszédfelismerési szöveg két különböző REST API-val rendelkezik. Minden API speciális célt szolgál, és különböző végpontokat használ.

A beszéd – szöveg REST API-k a következők:
- A [beszédfelismerés és a szöveg közötti REST API v 3.0](#speech-to-text-rest-api-v30) használatos a [kötegelt átíráshoz](batch-transcription.md) és a [Custom Speechhoz](custom-speech-overview.md). a v 3.0 a [v 2.0 utódja](./migrate-v2-to-v3.md).
- A beszéd [-szöveg REST API a rövid hanghoz](#speech-to-text-rest-api-for-short-audio) a [Speech SDK](speech-sdk.md)alternatívájaként használható online átírás. Az API-t használó kérelmek legfeljebb 60 másodpercig továbbítanak kérelmeket. 

## <a name="speech-to-text-rest-api-v30"></a>Beszéd – szöveg REST API v 3.0

A beszédfelismerés és a szöveg közötti REST API v 3.0 használatos a [kötegelt átíráshoz](batch-transcription.md) és a [Custom Speechhoz](custom-speech-overview.md). Ha a REST-on keresztül kell kommunikálnia az online átírással, a [rövid hangfelvételhez használjon beszéd-szöveges REST API](#speech-to-text-rest-api-for-short-audio).

REST API v 3.0 használata a következőhöz:
- Modellek másolása más előfizetésekre arra az esetre, ha azt szeretné, hogy a munkatársak hozzáférjenek a létrehozott modellhez, vagy olyan esetekben, amikor egy modellt több régióra kíván telepíteni
- Adatok átírása egy tárolóból (tömeges átírás), valamint több hangfájl URL-címének megadása
- Adatok feltöltése az Azure Storage-fiókokba SAS URI használatával
- Naplók beolvasása egy végponton, ha a rendszer naplózza a naplókat a végponthoz
- A létrehozott modellek jegyzékfájljának kérése a helyszíni tárolók beállítása céljából

A REST API v 3.0 a következőkhöz hasonló funkciókat tartalmaz:
- **Értesítések – webhookok**– a szolgáltatás összes futó folyamata mostantól támogatja a webhook-értesítéseket. A REST API v 3.0 biztosítja azokat a hívásokat, amelyek lehetővé teszik, hogy regisztrálja az értesítéseket küldő webhookokat
- **A végpontok mögötti modellek frissítése** 
- **Modellek átalakítása több adatkészlettel**– modell átalakítása az akusztikus, a nyelv és a kiejtési adat több adatkészlet-kombinációjának használatával
- **Saját tárterület** használata – saját Storage-fiókok használata naplókhoz, átírási fájlokhoz és egyéb adatfájlokhoz

Tekintse meg a következő [cikket](batch-transcription.md): példák a Batch átírásával REST API v 3.0 használatával.

Ha beszédfelismerést REST API v 2.0-s verziót használ, tekintse meg az [útmutató](./migrate-v2-to-v3.md)a v 3.0-ba való áttelepítését ismertető témakört.

Tekintse meg a teljes beszéd – szöveg REST API v [3.0 referenciát](https://centralus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0).

## <a name="speech-to-text-rest-api-for-short-audio"></a>Beszéd – szöveg REST API rövid hanghoz

A Speech [SDK](speech-sdk.md)alternatívájaként a beszédfelismerési szolgáltatás lehetővé teszi, hogy REST API használatával alakítsa át a beszédfelismerést a szöveggé. Minden elérhető végpont egy régióhoz van társítva. Az alkalmazáshoz szükség van egy előfizetési kulcsra a használni kívánt végponthoz. A rövid hang REST API nagyon korlátozott, és csak abban az esetben használható, ha a [SPEECH SDK](speech-sdk.md) nem.

Mielőtt a rövid hanghoz tartozó beszéd-szöveg REST API használja, vegye figyelembe a következőket:

* A rövid hangra és a hang továbbítására szolgáló REST APIt használó kérések közvetlenül legfeljebb 60 másodperces hangot tartalmazhatnak.
* A rövid hanganyaghoz tartozó beszéd-szöveg REST API csak a végső eredményeket adja vissza. A részleges eredmények nincsenek megadva.

Ha a hosszú hang küldése az alkalmazásra vonatkozó követelmény, érdemes lehet használni a [SPEECH SDK](speech-sdk.md) [-t vagy a beszéd – szöveg REST API v 3.0](#speech-to-text-rest-api-v30)-s verzióját.

> [!TIP]
> Tekintse meg [ezt a cikket](sovereign-clouds.md) Azure Government és az Azure China-végpontokról.

[!INCLUDE [](../../../includes/cognitive-services-speech-service-rest-auth.md)]

### <a name="regions-and-endpoints"></a>Régiók és végpontok

A rövid hang REST API végpontjának formátuma a következő:

```
https://<REGION_IDENTIFIER>.stt.speech.microsoft.com/speech/recognition/conversation/cognitiveservices/v1
```

Cserélje le az `<REGION_IDENTIFIER>` elemet az előfizetés régiójának megfelelő azonosítóra ebből a táblából:

[!INCLUDE [](../../../includes/cognitive-services-speech-service-region-identifier.md)]

> [!NOTE]
> A nyelvi paramétert az URL-címhez kell hozzáfűzni, hogy ne kapjon 4xx HTTP-hibát. Az USA nyugati végpontját használó angol nyelv például a következő: `https://westus.stt.speech.microsoft.com/speech/recognition/conversation/cognitiveservices/v1?language=en-US` .

### <a name="query-parameters"></a>Lekérdezési paraméterek

Ezek a paraméterek szerepelhetnek a REST-kérelem lekérdezési karakterláncában.

| Paraméter | Leírás | Kötelező/nem kötelező |
|-----------|-------------|---------------------|
| `language` | Azonosítja a felismert nyelvet. Lásd: [támogatott nyelvek](language-support.md#speech-to-text). | Kötelező |
| `format` | Megadja az eredmény formátumát. Az elfogadott értékek: `simple` és `detailed` . Az egyszerű eredmények közé tartoznak a következők:,, `RecognitionStatus` `DisplayText` `Offset` és `Duration` . A részletes válaszok a megjelenítendő szöveg négy különböző ábrázolását tartalmazzák. Az alapértelmezett beállítás: `simple`. | Választható |
| `profanity` | Meghatározza, hogyan kezelhető a káromkodás az eredmények felismerésében. Az elfogadott értékek olyanok, `masked` amelyek a káromkodást helyettesítik a csillagokkal, `removed` ami eltávolítja az eredményből az összes trágár elemet, vagy az `raw` eredménybe beletartozik a káromkodás is. Az alapértelmezett beállítás: `masked`. | Választható |
| `cid` | Ha egyéni modelleket hoz létre a [Custom Speech-portálon](./custom-speech-overview.md) , egyéni modelleket használhat a **telepítési** oldalon található **végpont-azonosítón** keresztül. Használja a **VÉGPONT azonosítóját** a `cid` lekérdezési karakterlánc paraméter argumentumaként. | Választható |

### <a name="request-headers"></a>Kérésfejlécek

Ez a táblázat a beszédfelismerési kérelmekhez szükséges és nem kötelező fejléceket sorolja fel.

|Fejléc| Leírás | Kötelező/nem kötelező |
|------|-------------|---------------------|
| `Ocp-Apim-Subscription-Key` | A beszédfelismerési szolgáltatás előfizetési kulcsa. | Ez a fejléc vagy `Authorization` kötelező. |
| `Authorization` | A szó előtt egy engedélyezési jogkivonat `Bearer` . További információért lásd: [Hitelesítés](#authentication). | Ez a fejléc vagy `Ocp-Apim-Subscription-Key` kötelező. |
| `Pronunciation-Assessment` | Azokat a paramétereket adja meg, amelyekkel megjelenítheti a kiejtési pontszámokat az elismerés eredményei között, amelyek felmérik a beszédfelismerés kiejtési minőségét, pontossággal, gördülékenyen, teljességgel stb. Ez a paraméter egy Base64 kódolású JSON, amely több részletes paramétert tartalmaz. A fejléc létrehozásához tekintse meg a [kiejtés értékelési paramétereit](#pronunciation-assessment-parameters) . | Választható |
| `Content-type` | A megadott hangadatok formátumát és kodekjét ismerteti. Az elfogadott értékek: `audio/wav; codecs=audio/pcm; samplerate=16000` és `audio/ogg; codecs=opus` . | Kötelező |
| `Transfer-Encoding` | Meghatározza, hogy a rendszer a darabolásos hangadatokat egyetlen fájl helyett elküldje. Csak a hangadatok darabolásakor használja ezt a fejlécet. | Választható |
| `Expect` | Ha darabolásos átvitelt használ, küldje el `Expect: 100-continue` . A beszédfelismerési szolgáltatás tudomásul veszi a kezdeti kérést, és további adatra vár.| A darabolásos hangadatok küldéséhez szükséges. |
| `Accept` | Ha meg van adni, azt kötelező megadni `application/json` . A beszédfelismerési szolgáltatás az eredményeket JSON-formátumban jeleníti meg. Néhány kérelem-keretrendszer nem kompatibilis alapértelmezett értéket biztosít. Célszerű mindig belefoglalni `Accept` . | Nem kötelező, de ajánlott. |

### <a name="audio-formats"></a>Hangformátumok

A hang a HTTP-kérelem törzsében lesz elküldve `POST` . Ennek a táblázatnak az egyik formátumában kell szerepelnie:

| Formátum | Codec | Átviteli sebesség | Mintavételezési arány  |
|--------|-------|----------|--------------|
| WAV    | PCM   | 256 kbps | 16 kHz, monó |
| VORBIS    | OPUS  | 256 kpbs | 16 kHz, monó |

>[!NOTE]
>A fenti formátumok a beszédfelismerési szolgáltatásban a rövid hang és a WebSocket REST APIon keresztül támogatottak. A [SPEECH SDK](speech-sdk.md) jelenleg a WAV formátumot támogatja a PCM-kodekkel és [más formátumokkal](how-to-use-codec-compressed-audio-input-streams.md).

### <a name="pronunciation-assessment-parameters"></a>Kiejtés-értékelési paraméterek

Ez a táblázat a kiejtés értékeléséhez szükséges és választható paramétereket sorolja fel.

| Paraméter | Leírás | Kötelező? |
|-----------|-------------|---------------------|
| ReferenceText | A kiejtés kiértékeléséhez használandó szöveg | Kötelező |
| GradingSystem | A pontszám kalibrációs pontjának rendszere. A `FivePoint` rendszer 0-5 lebegőpontos pontszámot ad, és `HundredMark` egy 0-100 lebegőpontos pontszámot ad. Alapértelmezett: `FivePoint`. | Választható |
| Részletesség | A kiértékelés részletessége. Az elfogadott értékek a teljes szöveges `Phoneme` , a Word-és a fonéma-szinten látható pontszám, amely a teljes szöveg `Word` és a szó szintjén mutatja a pontszámot, amely a `FullText` teljes szöveges szint pontszámát mutatja. Az alapértelmezett beállítás: `Phoneme`. | Választható |
| Dimenzió | Meghatározza a kimeneti feltételeket. Az elfogadott értékek `Basic` , amelyek csak a pontossági pontszámot mutatják, `Comprehensive` több dimenzióban jelenítik meg a pontszámokat (pl. a teljes szöveges szinten a teljességi pontszám és a teljesség pontszáma, a hiba típusa a szó szintjén). Ellenőrizze a [Válasz paramétereit](#response-parameters) , és tekintse meg a különböző pontszám-dimenziók és a Word-hibák definícióit. Az alapértelmezett beállítás: `Basic`. | Választható |
| EnableMiscue | Engedélyezi a gikszer számítását. Ha ez a beállítás engedélyezve van, a rendszer összehasonlítja a kiejtett szavakat a hivatkozási szöveggel, és az összehasonlítás alapján kihagyási/beszúrási művelettel jelöli meg őket. Az elfogadott értékek: `False` és `True` . Az alapértelmezett beállítás: `False`. | Választható |
| ScenarioId | Egy testreszabott pont rendszerét jelző GUID. | Választható |

Alább látható egy példa a kiejtés-értékelési paramétereket tartalmazó JSON-ra:

```json
{
  "ReferenceText": "Good morning.",
  "GradingSystem": "HundredMark",
  "Granularity": "FullText",
  "Dimension": "Comprehensive"
}
```

Az alábbi mintakód bemutatja, hogyan építheti ki a kiejtés-értékelési paramétereket a `Pronunciation-Assessment` fejlécbe:

```csharp
var pronAssessmentParamsJson = $"{{\"ReferenceText\":\"Good morning.\",\"GradingSystem\":\"HundredMark\",\"Granularity\":\"FullText\",\"Dimension\":\"Comprehensive\"}}";
var pronAssessmentParamsBytes = Encoding.UTF8.GetBytes(pronAssessmentParamsJson);
var pronAssessmentHeader = Convert.ToBase64String(pronAssessmentParamsBytes);
```

Javasoljuk, hogy a hangadatok közzététele során a streaming (darabolásos) feltöltést is javasolja, ami jelentősen csökkentheti a késést. A streaming engedélyezéséhez lásd: [mintakód különböző programozási nyelveken](https://github.com/Azure-Samples/Cognitive-Speech-TTS/tree/master/PronunciationAssessment) .

>[!NOTE]
>A kiejtés-értékelési funkció jelenleg csak nyelven érhető el `en-US` .

### <a name="sample-request"></a>Példa a kérelemre

Az alábbi minta tartalmazza az állomásnév és a szükséges fejléceket. Fontos megjegyezni, hogy a szolgáltatás a hangadatokat is várja, amelyek nem szerepelnek ebben a mintában. Ahogy korábban említettük, a darabolás javasolt, de nem kötelező.

```HTTP
POST speech/recognition/conversation/cognitiveservices/v1?language=en-US&format=detailed HTTP/1.1
Accept: application/json;text/xml
Content-Type: audio/wav; codecs=audio/pcm; samplerate=16000
Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY
Host: westus.stt.speech.microsoft.com
Transfer-Encoding: chunked
Expect: 100-continue
```

A kiejtés értékelésének engedélyezéséhez az alábbi fejlécet adhat hozzá. A fejléc létrehozásához tekintse meg a [kiejtés értékelési paramétereit](#pronunciation-assessment-parameters) .

```HTTP
Pronunciation-Assessment: eyJSZWZlcm...
```

### <a name="http-status-codes"></a>HTTP-állapotkódok

Az egyes válaszok HTTP-állapotkód sikeres vagy gyakori hibákat jelez.

| HTTP-állapotkód | Leírás | Lehetséges ok |
|------------------|-------------|-----------------|
| `100` | Folytatás | A kezdeti kérelem el lett fogadva. Folytassa a további adatok küldésével. (Darabolásos átvitelsel használatos) |
| `200` | OK | A kérelem sikeres volt; a válasz törzse egy JSON-objektum. |
| `400` | Hibás kérelem | A nyelvi kód nincs megadva, nem támogatott nyelv, érvénytelen hangfájl stb. |
| `401` | Nem engedélyezett | Az előfizetési kulcs vagy az engedélyezési jogkivonat érvénytelen a megadott régióban, vagy érvénytelen végpont. |
| `403` | Forbidden | Hiányzó előfizetési kulcs vagy engedélyezési jogkivonat. |

### <a name="chunked-transfer"></a>Darabolásos átvitel

A darabolásos átvitel ( `Transfer-Encoding: chunked` ) segítségével csökkentheti az elismerés késését. Lehetővé teszi, hogy a beszédfelismerési szolgáltatás megkezdje a hangfájl feldolgozását a továbbítás során. A rövid hang REST API nem nyújt részleges vagy időközi eredményeket.

Ez a mintakód azt mutatja be, hogyan lehet hangot küldeni a darabokban. Csak az első részletnek kell tartalmaznia a hangfájl fejlécét. `request` egy olyan `HttpWebRequest` objektum, amely a megfelelő Rest-végponthoz van csatlakoztatva. `audioFile` a lemezen lévő hangfájl elérési útja.

```csharp
var request = (HttpWebRequest)HttpWebRequest.Create(requestUri);
request.SendChunked = true;
request.Accept = @"application/json;text/xml";
request.Method = "POST";
request.ProtocolVersion = HttpVersion.Version11;
request.Host = host;
request.ContentType = @"audio/wav; codecs=audio/pcm; samplerate=16000";
request.Headers["Ocp-Apim-Subscription-Key"] = "YOUR_SUBSCRIPTION_KEY";
request.AllowWriteStreamBuffering = false;

using (var fs = new FileStream(audioFile, FileMode.Open, FileAccess.Read))
{
    // Open a request stream and write 1024 byte chunks in the stream one at a time.
    byte[] buffer = null;
    int bytesRead = 0;
    using (var requestStream = request.GetRequestStream())
    {
        // Read 1024 raw bytes from the input audio file.
        buffer = new Byte[checked((uint)Math.Min(1024, (int)fs.Length))];
        while ((bytesRead = fs.Read(buffer, 0, buffer.Length)) != 0)
        {
            requestStream.Write(buffer, 0, bytesRead);
        }

        requestStream.Flush();
    }
}
```

### <a name="response-parameters"></a>Válasz paraméterei

Az eredmények JSON-ként vannak megadva. A `simple` formátum tartalmazza ezeket a legfelső szintű mezőket.

| Paraméter | Leírás  |
|-----------|--------------|
|`RecognitionStatus`|Állapot, például `Success` sikeres felismeréshez. Lásd a következő táblázatot.|
|`DisplayText`|A felismert szöveg a tőkésítés, a központozás, az inverz szöveg normalizálása (a szóbeli szöveg konvertálása rövidebb formátumokra, például 200 a "200" vagy "Dr. Smith" kifejezésre a "Doctor Smith" esetében) és a káromkodás maszkolása után. Csak sikeres.|
|`Offset`|Az az idő (100-ns egységben), amikor a felismert beszéd az audio streamben kezdődik.|
|`Duration`|Az audio streamben felismert beszéd időtartama (100-ns egységben).|

A `RecognitionStatus` mező a következő értékeket tartalmazhatja:

| Állapot | Leírás |
|--------|-------------|
| `Success` | Az felismerés sikeres volt, és a `DisplayText` mező jelen van. |
| `NoMatch` | A rendszer beszédet észlelt az audio streamben, de a célnyelv nem felelt meg a szavaknak. Általában azt jelenti, hogy az elismerés nyelve egy másik nyelv, amelyet a felhasználó beszél. |
| `InitialSilenceTimeout` | A hangadatfolyam kezdete csak csendet foglalt, és a szolgáltatás a beszédfelismerésre való várakozás során időtúllépést várt. |
| `BabbleTimeout` | A hangadatfolyam elején csak a zaj szerepel, és a szolgáltatás a beszédfelismerésre való várakozás során időtúllépést okozott. |
| `Error` | A felismerési szolgáltatás belső hibát észlelt, és nem folytatható. Próbálkozzon újra, ha lehetséges. |

> [!NOTE]
> Ha a hang csak a káromkodásból áll, és a `profanity` lekérdezési paraméter értéke `remove` , akkor a szolgáltatás nem ad vissza beszédfelismerési eredményt.

A `detailed` formátum a felismert eredmények további formáit is tartalmazza.
A formátum használatakor a a `detailed` `DisplayText` `Display` lista minden eredményének megfelelően van megadva `NBest` .

A listában szereplő objektum a következőket `NBest` tartalmazhatja:

| Paraméter | Leírás |
|-----------|-------------|
| `Confidence` | A 0,0-es bejegyzés megbízhatósági pontszáma (nincs megbízhatóság) 1,0-re (teljes biztonság) |
| `Lexical` | A felismert szöveg lexikális formája: a tényleges szavak felismerése. |
| `ITN` | A felismert szöveg, a telefonszámok, a számok, a rövidítések ("doktor Smith" – "Dr Smith") és az alkalmazott egyéb átalakítások inverz szövege. |
| `MaskedITN` | A ITN űrlap a káromkodás maszkolásával lett alkalmazva, ha szükséges. |
| `Display` | A felismert szöveg megjelenítési formája, írásjelekkel és nagybetűkkel kiegészítve. Ez a paraméter megegyezik a `DisplayText` Formátum beállításakor megadott értékkel `simple` . |
| `AccuracyScore` | A beszéd kiejtésének pontossága. A pontosság azt jelzi, hogy a fonémák milyen szorosan illeszkedik a natív beszélő kiejtéséhez. A Word és a teljes szöveges szint pontossági pontszáma a fonéma szint pontossági pontszámával van összesítve. |
| `FluencyScore` | Az adott beszédet. A gördülékenység azt jelzi, hogy a beszéd milyen szorosan illeszkedik a szavak közötti csendes szünetekhez. |
| `CompletenessScore` | A beszéd teljessége, amelyet a Kiejtett szavak arányának kiszámításával határozhat meg a szöveges bevitelre való hivatkozáshoz. |
| `PronScore` | Az adott beszéd kiejtési minőségét jelző összesített pontszám. Ez az Összesítés a `AccuracyScore` `FluencyScore` és `CompletenessScore` a súlyozással történik. |
| `ErrorType` | Ez az érték azt jelzi, hogy egy szó ki van-e hagyva, be van-e jelölve vagy rosszul van-e kiválasztva `ReferenceText` . A lehetséges értékek: `None` (azaz nincs hiba ezen a szónak), `Omission` `Insertion` és `Mispronunciation` . |

### <a name="sample-responses"></a>Mintául szolgáló válaszok

Egy tipikus válasz az `simple` elismerésre:

```json
{
  "RecognitionStatus": "Success",
  "DisplayText": "Remind me to buy 5 pencils.",
  "Offset": "1236645672289",
  "Duration": "1236645672289"
}
```

Egy tipikus válasz az `detailed` elismerésre:

```json
{
  "RecognitionStatus": "Success",
  "Offset": "1236645672289",
  "Duration": "1236645672289",
  "NBest": [
      {
        "Confidence" : "0.87",
        "Lexical" : "remind me to buy five pencils",
        "ITN" : "remind me to buy 5 pencils",
        "MaskedITN" : "remind me to buy 5 pencils",
        "Display" : "Remind me to buy 5 pencils.",
      }
  ]
}
```

Egy tipikus válasz az elismeréshez a kiejtés értékelésével:

```json
{
  "RecognitionStatus": "Success",
  "Offset": "400000",
  "Duration": "11000000",
  "NBest": [
      {
        "Confidence" : "0.87",
        "Lexical" : "good morning",
        "ITN" : "good morning",
        "MaskedITN" : "good morning",
        "Display" : "Good morning.",
        "PronScore" : 84.4,
        "AccuracyScore" : 100.0,
        "FluencyScore" : 74.0,
        "CompletenessScore" : 100.0,
        "Words": [
            {
              "Word" : "Good",
              "AccuracyScore" : 100.0,
              "ErrorType" : "None",
              "Offset" : 500000,
              "Duration" : 2700000
            },
            {
              "Word" : "morning",
              "AccuracyScore" : 100.0,
              "ErrorType" : "None",
              "Offset" : 5300000,
              "Duration" : 900000
            }
        ]
      }
  ]
}
```

## <a name="next-steps"></a>Következő lépések

- [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/free/cognitive-services/)
- [Akusztikai modellek testreszabása](./how-to-custom-speech-train-model.md)
- [Nyelvi modellek testreszabása](./how-to-custom-speech-train-model.md)
- [Ismerkedjen meg a Batch-átírással](batch-transcription.md)