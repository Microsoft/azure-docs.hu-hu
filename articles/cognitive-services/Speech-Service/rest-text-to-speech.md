---
title: Szöveg-beszéd API-referencia (REST) – Speech szolgáltatás
titleSuffix: Azure Cognitive Services
description: Megtudhatja, hogyan használhatja a szöveg-beszéd REST API. Ebben a cikkben megismerheti az engedélyezési lehetőségeket, a lekérdezési lehetőségeket, a kérések struktúraszerkezetét és a válaszok fogadását.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 01/08/2021
ms.author: trbye
ms.custom: references_regions
ms.openlocfilehash: 0065b6f4a7039e2883bca6acd5cf659be7b71069
ms.sourcegitcommit: 79c9c95e8a267abc677c8f3272cb9d7f9673a3d7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/19/2021
ms.locfileid: "107717674"
---
# <a name="text-to-speech-rest-api"></a>Szövegfelolvasás REST API

A Speech service [](#convert-text-to-speech) lehetővé teszi a szöveg [](#get-a-list-of-voices) szintetizált beszédgé konvertálását, és egy régió támogatott hanglistának lekért listáját REST API-k használatával. Minden elérhető végpont egy régióhoz van társítva. Szükség van egy előfizetői kulcsra a használni kívánt végponthoz/régióhoz.

A szöveg-beszéd REST API támogatja a neurális és a szabványos szöveg-beszéd hangokat, amelyek a területi beállítások alapján meghatározott nyelveket és dialektusokat támogatnak.

* A hangok teljes listájáért tekintse meg a [nyelvi támogatást.](language-support.md#text-to-speech)
* További információ a regionális rendelkezésre állásról: [régiók.](regions.md#text-to-speech)

> [!IMPORTANT]
> A költségek a standard, az egyéni és a neurális hangoktól függően változnak. További információ: [Díjszabás.](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/)

Az API használata előtt értse meg a következőt:

* A szöveg beszédfelismerési REST API engedélyfejlécet igényel. Ez azt jelenti, hogy jogkivonatcserét kell végrehajtania a szolgáltatás eléréséhez. További információért lásd: [Hitelesítés](#authentication).

> [!TIP]
> Ebben [a cikkben további](sovereign-clouds.md) Azure Government Azure China-végpontokat talál.

[!INCLUDE [](../../../includes/cognitive-services-speech-service-rest-auth.md)]

## <a name="get-a-list-of-voices"></a>Hangok listájának lekérte

A végpont lehetővé teszi egy adott régió/végpont hangjainak teljes `voices/list` listáját.

### <a name="regions-and-endpoints"></a>Régiók és végpontok

| Region | Végpont |
|--------|----------|
| Kelet-Ausztrália | `https://australiaeast.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Dél-Brazília | `https://brazilsouth.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Közép-Kanada | `https://canadacentral.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Az USA középső régiója | `https://centralus.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Kelet-Ázsia | `https://eastasia.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| USA keleti régiója | `https://eastus.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| USA 2. keleti régiója | `https://eastus2.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Közép-Franciaország | `https://francecentral.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Közép-India | `https://centralindia.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Kelet-Japán | `https://japaneast.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Dél-Korea középső régiója | `https://koreacentral.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| USA északi középső régiója | `https://northcentralus.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Észak-Európa | `https://northeurope.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Dél-Afrika északi régiója | `https://southafricanorth.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| USA déli középső régiója | `https://southcentralus.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Délkelet-Ázsia | `https://southeastasia.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Az Egyesült Királyság déli régiója | `https://uksouth.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| USA nyugati középső régiója | `https://westcentralus.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Nyugat-Európa | `https://westeurope.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| USA nyugati régiója | `https://westus.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| USA 2. nyugati régiója | `https://westus2.tts.speech.microsoft.com/cognitiveservices/voices/list` |

> [!TIP]
> [Az előzetes verziójú hangok](language-support.md#neural-voices-in-preview) csak a következő 3 régióban érhetők el: az USA keleti régiója, Nyugat-Európa és Délkelet-Ázsia.

### <a name="request-headers"></a>Kérésfejlécek

Ez a táblázat a szöveg-beszéd kérések kötelező és nem kötelező fejlécét sorolja fel.

| Fejléc | Description | Kötelező/nem kötelező |
|--------|-------------|---------------------|
| `Ocp-Apim-Subscription-Key` | A Speech szolgáltatás előfizetői kulcsa. | Ez a fejléc vagy `Authorization` kötelező. |
| `Authorization` | Egy engedélyezési jogkivonat, amelyet a következő szó előz `Bearer` meg: . További információért lásd: [Hitelesítés](#authentication). | Ez a fejléc vagy `Ocp-Apim-Subscription-Key` kötelező. |



### <a name="request-body"></a>A kérés törzse

Az erre a végpontra `GET` vonatkozó kérelmekhez nincs szükség törzsre.

### <a name="sample-request"></a>Mintakérés

Ehhez a kérelemhez csak engedélyezési fejléc szükséges.

```http
GET /cognitiveservices/voices/list HTTP/1.1

Host: westus.tts.speech.microsoft.com
Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY
```

### <a name="sample-response"></a>Mintaválasz

Ezt a választ csonkoltuk, hogy bemutassa a válasz szerkezetét.

> [!NOTE]
> A hang rendelkezésre állása régiónként/végpontonként változik.

```json
[

    {
    "Name": "Microsoft Server Speech Text to Speech Voice (en-US, AriaNeural)",
    "DisplayName": "Aria",
    "LocalName": "Aria",
    "ShortName": "en-US-AriaNeural",
    "Gender": "Female",
    "Locale": "en-US",
    "StyleList": [
      "chat",
      "customerservice",
      "newscast-casual",
      "newscast-formal",
      "cheerful",
      "empathetic"
    ],
    "SampleRateHertz": "24000",
    "VoiceType": "Neural",
    "Status": "GA"
  },

  ...

     {
    "Name": "Microsoft Server Speech Text to Speech Voice (ga-IE, OrlaNeural)",
    "DisplayName": "Orla",
    "LocalName": "Orla",
    "ShortName": "ga-IE-OrlaNeural",
    "Gender": "Female",
    "Locale": "ga-IE",
    "SampleRateHertz": "24000",
    "VoiceType": "Neural",
    "Status": "Preview"
  },

  ...

   {
    "Name": "Microsoft Server Speech Text to Speech Voice (zh-CN, YunxiNeural)",
    "DisplayName": "Yunxi",
    "LocalName": "云希",
    "ShortName": "zh-CN-YunxiNeural",
    "Gender": "Male",
    "Locale": "zh-CN",
    "StyleList": [
      "Calm",
      "Fearful",
      "Cheerful",
      "Disgruntled",
      "Serious",
      "Angry",
      "Sad",
      "Depressed",
      "Embarrassed"
    ],
    "SampleRateHertz": "24000",
    "VoiceType": "Neural",
    "Status": "Preview"
  },

    ...

   {
    "Name": "Microsoft Server Speech Text to Speech Voice (ar-EG, Hoda)",
    "DisplayName": "Hoda",
    "LocalName": "هدى",
    "ShortName": "ar-EG-Hoda",
    "Gender": "Female",
    "Locale": "ar-EG",
    "SampleRateHertz": "16000",
    "VoiceType": "Standard",
    "Status": "GA"
  },

...

]
```

### <a name="http-status-codes"></a>HTTP-állapotkódok

Az egyes válaszok HTTP-állapotkódja sikeres vagy gyakori hibákat jelez.

| HTTP-állapotkód | Leírás | Lehetséges ok |
|------------------|-------------|-----------------|
| 200 | OK | A kérés sikeres volt. |
| 400 | Hibás kérés | Hiányzik egy kötelező paraméter, üres vagy null értékű. Vagy a kötelező vagy nem kötelező paraméternek átadott érték érvénytelen. Gyakori probléma a túl hosszú fejléc. |
| 401 | Nem engedélyezett | A kérelem nincs engedélyezve. Ellenőrizze, hogy az előfizetési kulcs vagy jogkivonat érvényes-e, és a megfelelő régióban van-e. |
| 429 | Túl sok kérelem | Túllépte az előfizetéshez engedélyezett kérelmek kvótáját vagy sebességét. |
| 502 | Hibás átjáró    | Hálózati vagy kiszolgálóoldali probléma. Érvénytelen fejléceket is jelezhet. |


## <a name="convert-text-to-speech"></a>Szöveg átalakítása beszéddé

A végpont lehetővé teszi a szöveg beszédgé konvertálását `v1` a [Speech Synthesis Markup Language (SSML) használatával.](speech-synthesis-markup.md)

### <a name="regions-and-endpoints"></a>Régiók és végpontok

Ezek a régiók támogatottak a szöveg-beszéd beszédfelismeréshez a REST API. Győződjön meg arról, hogy az előfizetés régiójának megfelelő végpontot választja ki.

[!INCLUDE [](../../../includes/cognitive-services-speech-service-endpoints-text-to-speech.md)]

### <a name="request-headers"></a>Kérésfejlécek

Ez a táblázat a szöveg-beszéd kérések kötelező és nem kötelező fejlécét sorolja fel.

| Fejléc | Description | Kötelező / Nem kötelező |
|--------|-------------|---------------------|
| `Authorization` | Egy engedélyezési jogkivonat, amely előtt a szó előzi meg `Bearer` a következőt: . További információért lásd: [Hitelesítés](#authentication). | Kötelező |
| `Content-Type` | Megadja a megadott szöveg tartalomtípusát. Elfogadott érték: `application/ssml+xml` . | Kötelező |
| `X-Microsoft-OutputFormat` | Megadja a hangkimenet formátumát. Az elfogadott értékek teljes listájáért tekintse meg a [hangkimeneteket.](#audio-outputs) | Kötelező |
| `User-Agent` | Az alkalmazás neve. A megadott értéknek 255 karakternél rövidebbnek kell lennie. | Kötelező |

### <a name="audio-outputs"></a>Hangkimenetek

Ez az egyes kérések fejléceként küldött támogatott hangformátumok `X-Microsoft-OutputFormat` listája. Mindegyik tartalmaz egy bitátjárót és kódolási típust. A Speech Service 24 kHz-es, 16 kHz-es és 8 kHz-es hangkimeneteket támogat.

```output
raw-16khz-16bit-mono-pcm            riff-16khz-16bit-mono-pcm
raw-24khz-16bit-mono-pcm            riff-24khz-16bit-mono-pcm
raw-48khz-16bit-mono-pcm            riff-48khz-16bit-mono-pcm
raw-8khz-8bit-mono-mulaw            riff-8khz-8bit-mono-mulaw
raw-8khz-8bit-mono-alaw             riff-8khz-8bit-mono-alaw
audio-16khz-32kbitrate-mono-mp3     audio-16khz-64kbitrate-mono-mp3
audio-16khz-128kbitrate-mono-mp3    audio-24khz-48kbitrate-mono-mp3
audio-24khz-96kbitrate-mono-mp3     audio-24khz-160kbitrate-mono-mp3
audio-48khz-96kbitrate-mono-mp3     audio-48khz-192kbitrate-mono-mp3
raw-16khz-16bit-mono-truesilk       raw-24khz-16bit-mono-truesilk
webm-16khz-16bit-mono-opus          webm-24khz-16bit-mono-opus
ogg-16khz-16bit-mono-opus           ogg-24khz-16bit-mono-opus
ogg-48khz-16bit-mono-opus
```

> [!NOTE]
> Ha a kiválasztott hang- és kimeneti formátum bitsávja eltérő, a rendszer szükség szerint újra mintaként használja a hangot.
> Az ogg-24khz-16bit-mono-opus az [opus codec](https://opus-codec.org/downloads/) segítségével dekódolható

### <a name="request-body"></a>A kérés törzse

Az egyes kérések törzse Speech `POST` [Synthesis Markup Language (SSML) formátumban lesz elküldve.](speech-synthesis-markup.md) Az SSML segítségével kiválaszthatja a szintetizált beszéd hangját és nyelvét, amelyet a text-to-speech szolgáltatás ad vissza. A támogatott hangok teljes listáját lásd: [nyelvi támogatás.](language-support.md#text-to-speech)

> [!NOTE]
> Ha egyéni hangot használ, a kérés törzse egyszerű szövegként (ASCII vagy UTF-8) küldhető el.

### <a name="sample-request"></a>Mintakérés

Ez a HTTP-kérés SSML-t használ a hang és a nyelv megadásához. Ha a törzs hossza hosszú, és az eredményül kapott hanganyag mérete meghaladja a 10 percet – a rendszer 10 percre csonkolja. Más szóval a hang hossza nem haladhatja meg a 10 percet.

```http
POST /cognitiveservices/v1 HTTP/1.1

X-Microsoft-OutputFormat: raw-24khz-16bit-mono-pcm
Content-Type: application/ssml+xml
Host: westus.tts.speech.microsoft.com
Content-Length: 225
Authorization: Bearer [Base64 access_token]

<speak version='1.0' xml:lang='en-US'><voice xml:lang='en-US' xml:gender='Female'
    name='en-US-AriaNeural'>
        Microsoft Speech Service Text-to-Speech API
</voice></speak>
```

### <a name="http-status-codes"></a>HTTP-állapotkódok

Az egyes válaszok HTTP-állapotkódja sikeres vagy gyakori hibákat jelez.

| HTTP-állapotkód | Leírás | Lehetséges ok |
|------------------|-------------|-----------------|
| 200 | OK | A kérés sikeres volt; A válasz törzse egy hangfájl. |
| 400 | Hibás kérés | Hiányzik egy kötelező paraméter, üres vagy null értékű. Vagy a kötelező vagy nem kötelező paraméternek átadott érték érvénytelen. Gyakori probléma a túl hosszú fejléc. |
| 401 | Nem engedélyezett | A kérelem nincs engedélyezve. Ellenőrizze, hogy az előfizetési kulcs vagy jogkivonat érvényes-e, és a megfelelő régióban van-e. |
| 415 | Nem támogatott adathordozó-típus | Lehetséges, hogy helytelenül `Content-Type` lett megszabadva. `Content-Type` A beállításnak a (beállítás) `application/ssml+xml` legyen. |
| 429 | Túl sok kérelem | Túllépte az előfizetéshez engedélyezett kérelmek kvótáját vagy sebességét. |
| 502 | Hibás átjáró    | Hálózati vagy kiszolgálóoldali probléma. Érvénytelen fejléceket is jelezhet. |

Ha a HTTP-állapot , a válasz törzse egy hangfájlt tartalmaz `200 OK` a kért formátumban. Ez a fájl lejátszható az átvitel, pufferbe mentés vagy fájlba mentéskor.

## <a name="next-steps"></a>Következő lépések

- [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/free/cognitive-services/)
- [Aszinkron szintézis a hosszú formában álló hanganyaghoz](./long-audio-api.md)
- [Bevezetés a Custom Voice szolgáltatásba](how-to-custom-voice.md)
