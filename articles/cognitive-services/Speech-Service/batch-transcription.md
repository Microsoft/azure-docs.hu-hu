---
title: A Batch transzkripció használata – beszédfelismerési szolgáltatás
titleSuffix: Azure Cognitive Services
description: A Batch-átírás ideális megoldás, ha nagy mennyiségű hanganyagot szeretne átírni a Storage-ban, például az Azure-blobokat. A dedikált REST API használatával a hangfájlok közös hozzáférésű aláírási (SAS) URI-val és aszinkron módon fogadhatók.
services: cognitive-services
author: wolfma61
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/23/2020
ms.author: wolfma
ms.custom: devx-track-csharp
ms.openlocfilehash: e48fead4d4364fd84f178388dbfb9158296e687b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98659971"
---
# <a name="how-to-use-batch-transcription"></a>A Batch-átírás használata

A Batch átírása REST API művelet, amely lehetővé teszi nagy mennyiségű hang tárolását. A hangfájlokra egy tipikus URI vagy egy [közös hozzáférésű aláírás (SAS)](../../storage/common/storage-sas-overview.md) URI használatával, valamint az átírási eredmények aszinkron fogadásával is rámutathat. A v 3.0 API-val egy vagy több hangfájlt is átadhat, vagy feldolgozhatja a teljes tárolót.

A Batch átírása REST API-kkal a következő módszereket hívhatja:

|    Kötegelt átírási művelet                                             |    Metódus    |    REST API hívás                                   |
|------------------------------------------------------------------------------|--------------|----------------------------------------------------|
|    Új átírást hoz létre.                                              |    POST      |    speechtotext/v 3.0/átiratok            |
|    Lekéri a hitelesített előfizetéshez tartozó átírások listáját.    |    GET       |    speechtotext/v 3.0/átiratok            |
|    Lekéri a támogatott területi beállítások listáját az offline átírásokhoz.              |    GET       |    speechtotext/v 3.0/átiratok/területi beállítások    |
|    Frissíti az azonosító alapján azonosított átirat megváltoztathatatlan részleteit.    |    JAVÍTÁS     |    speechtotext/v 3.0/átiratok/{azonosító}       |
|    Törli a megadott átírási feladatot.                                 |    DELETE    |    speechtotext/v 3.0/átiratok/{azonosító}       |
|    Lekéri az adott azonosító által azonosított átírást.                        |    GET       |    speechtotext/v 3.0/átiratok/{azonosító}       |
|    Lekéri a megadott azonosító által azonosított átirat eredményének fájljait.    |    GET       |    speechtotext/v 3.0/átiratok/{azonosító}/fájlok |

Áttekintheti és tesztelheti a részletes API-t, amely [hencegő dokumentumként](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0)érhető el.

A Batch-átírási feladatok ütemezése a lehető legjobb megoldás szerint történik.
Nem lehet megbecsülni, ha a feladat futási állapotba változik, de a normál rendszerterhelésnek megfelelően perceken belül meg kell történnie. Ha a futó állapotban van, az átirat gyorsabb, mint a lejátszási idő lejátszási sebessége.

## <a name="prerequisites"></a>Előfeltételek

Ahogy a Speech Service összes funkciója esetében, létrehozhat egy előfizetési kulcsot a [Azure Portal](https://portal.azure.com) az első [lépéseket ismertető útmutatóban](overview.md#try-the-speech-service-for-free).

>[!NOTE]
> A Batch-átírás használatához standard előfizetés (S0) szükséges a Speech Service-hez. Az ingyenes előfizetés kulcsa (F0) nem fog működni. További információ: [díjszabás és korlátok](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/).

Ha azt tervezi, hogy testreszabja a modelleket, kövesse az [akusztikai Testreszabás](./how-to-custom-speech-train-model.md) és a [nyelvi Testreszabás](./how-to-custom-speech-train-model.md)lépéseit. Ha a létrehozott modelleket a Batch-átírásban szeretné használni, szüksége lesz a modell helyére. A modell helyét a modell (tulajdonság) részleteinek vizsgálatával kérheti le `self` . A Batch átíró szolgáltatáshoz *nem szükséges* egy telepített egyéni végpont.

>[!NOTE]
> A REST API részeként a Batch-átírás [kvótákat és korlátokat](speech-services-quotas-and-limits.md#batch-transcription)tartalmaz, amelyeket javasoljuk, hogy tekintse át. Ha szeretné kihasználni a Batch átírási képességét, hogy hatékonyan átmásolja a nagy mennyiségű hangfájlt, javasoljuk, hogy mindig több fájlt küldjön, vagy mutasson egy Blob Storage tárolóra a hangfájlok segítségével. A szolgáltatás az átfutási idő csökkentése érdekében egyszerre fogja átírni a fájlokat. Egyetlen kérelemben több fájl használata nagyon egyszerű és egyértelmű – lásd a [konfigurációs](#configuration) szakaszt. 

## <a name="batch-transcription-api"></a>Batch-átírási API

A Batch transzkripciós API a következő formátumokat támogatja:

| Formátum | Codec | Bit/minta | Mintavételezési arány             |
|--------|-------|---------|---------------------------------|
| WAV    | PCM   | 16 bites  | 8 kHz vagy 16 kHz, monó vagy sztereó |
| MP3    | PCM   | 16 bites  | 8 kHz vagy 16 kHz, monó vagy sztereó |
| VORBIS    | OPUS  | 16 bites  | 8 kHz vagy 16 kHz, monó vagy sztereó |

A sztereó hangadatfolyamok esetében a bal és a jobb oldali csatorna az átírás során oszlik meg. Minden csatornához létre kell hozni egy JSON-eredményhalmaz fájlját.
Rendezett végleges átirat létrehozásához használja a kiíráskor generált időbélyeget.

### <a name="configuration"></a>Konfiguráció

A konfigurációs paraméterek JSON-ként vannak megadva. 

**Egy vagy több különálló fájl átirata.** Ha egynél több fájlt szeretne átírni, javasoljuk, hogy több fájlt küldjön egy kérelembe. Az alábbi példa három fájlt használ:

```json
{
  "contentUrls": [
    "<URL to an audio file 1 to transcribe>",
    "<URL to an audio file 2 to transcribe>",
    "<URL to an audio file 3 to transcribe>"
  ],
  "properties": {
    "wordLevelTimestampsEnabled": true
  },
  "locale": "en-US",
  "displayName": "Transcription of file using default model for en-US"
}
```

**Teljes tároló feldolgozása.** A tároló [sas](../../storage/common/storage-sas-overview.md) -nak tartalmaznia kell `r` (olvasási) és `l` (lista) engedélyeket:

```json
{
  "contentContainerUrl": "<SAS URL to the Azure blob container to transcribe>",
  "properties": {
    "wordLevelTimestampsEnabled": true
  },
  "locale": "en-US",
  "displayName": "Transcription of container using default model for en-US"
}
```

**Egyéni betanított modellt használhat a Batch-átíráshoz.** A példa három fájlt használ:

```json
{
  "contentUrls": [
    "<URL to an audio file 1 to transcribe>",
    "<URL to an audio file 2 to transcribe>",
    "<URL to an audio file 3 to transcribe>"
  ],
  "properties": {
    "wordLevelTimestampsEnabled": true
  },
  "locale": "en-US",
  "model": {
    "self": "https://westus.api.cognitive.microsoft.com/speechtotext/v3.0/models/{id}"
  },
  "displayName": "Transcription of file using default model for en-US"
}
```


### <a name="configuration-properties"></a>Konfigurációs tulajdonságok

Ezeket a választható tulajdonságokat az átírás konfigurálásához használhatja:

:::row:::
   :::column span="1":::
      **Paraméter**
   :::column-end:::
   :::column span="2":::
      **Leírás**
:::row-end:::
:::row:::
   :::column span="1":::
      `profanityFilterMode`
   :::column-end:::
   :::column span="2":::
      Nem kötelező, alapértelmezett érték: `Masked` . Meghatározza, hogyan kezelhető a káromkodás az eredmények felismerésében. Az elfogadott értékek a `None` káromkodás szűrésének letiltására, a `Masked` káromkodás és a csillagokkal való kiváltására, `Removed` az eredményből való káromkodás eltávolítására, vagy a `Tags` "káromkodás" címkék hozzáadására szolgálnak.
:::row-end:::
:::row:::
   :::column span="1":::
      `punctuationMode`
   :::column-end:::
   :::column span="2":::
      Nem kötelező, alapértelmezett érték: `DictatedAndAutomatic` . Meghatározza, hogyan kezelhető a központozás a felismerési eredményekben. Az elfogadott értékek az `None` írásjelek letiltására szolgálnak, a `Dictated` explicit (szóbeli) írásjelek megjelenítéséhez, `Automatic` hogy a dekóder a központozással legyen kezelve, vagy `DictatedAndAutomatic` pedig diktált és automatikus írásjeleket használjon.
:::row-end:::
:::row:::
   :::column span="1":::
      `wordLevelTimestampsEnabled`
   :::column-end:::
   :::column span="2":::
      Alapértelmezés szerint nem kötelező `false` . Megadja, hogy a rendszer hozzáadja-e a Word szintű időbélyegeket a kimenethez.
:::row-end:::
:::row:::
   :::column span="1":::
      `diarizationEnabled`
   :::column-end:::
   :::column span="2":::
      Alapértelmezés szerint nem kötelező `false` . Meghatározza, hogy a diarization-elemzést a bemeneten kell végrehajtani, amely a várt két hangokat tartalmazó monó-csatorna. Megjegyzés: a használatához a szükséges értékre `wordLevelTimestampsEnabled` kell állítani `true` .
:::row-end:::
:::row:::
   :::column span="1":::
      `channels`
   :::column-end:::
   :::column span="2":::
      Nem kötelező, `0` és `1` alapértelmezés szerint az átirata. A feldolgozandó csatorna-számok tömbje. Itt megadhatja a hangfájlban található elérhető csatornák egy részhalmazát (például `0` csak).
:::row-end:::
:::row:::
   :::column span="1":::
      `timeToLive`
   :::column-end:::
   :::column span="2":::
      Nem kötelező, alapértelmezés szerint nincs törlés. Az átiratok befejezését követő automatikus törlési időtartam. A a `timeToLive` tömeges feldolgozás során hasznos, hogy a rendszer végül törölni lehessen őket (például `PT12H` 12 órára).
:::row-end:::
:::row:::
   :::column span="1":::
      `destinationContainerUrl`
   :::column-end:::
   :::column span="2":::
      Opcionális URL-cím [ad hoc sas](../../storage/common/storage-sas-overview.md) -vel egy írható tárolóhoz az Azure-ban. Az eredmény ebben a tárolóban tárolódik. A tárolt hozzáférési házirenddel rendelkező SAS **nem** támogatott. Ha nincs megadva, a Microsoft az eredményeket a Microsoft által kezelt tároló tárolóban tárolja. Ha az átírást törli az [átírás](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0/operations/DeleteTranscription)meghívásával, a rendszer törli az eredményül kapott adatértéket is.
:::row-end:::

### <a name="storage"></a>Tárolás

A Batch-átírás képes a nyilvánosan látható internetes URI-n keresztül olvasni a hanganyagot, és az [Azure Blob Storage](../../storage/blobs/storage-blobs-overview.md)-t használó sas URI-val képes hang-és írási átírásokat olvasni.

## <a name="batch-transcription-result"></a>Köteg átírásának eredménye

Minden hangbemenethez létrejön egy átírási eredmény fájl.
Az [átírási fájlok beolvasása](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0/operations/GetTranscriptionFiles) művelet az átíráshoz tartozó találatok listáját adja vissza. Egy adott bemeneti fájl átírási fájljának megkereséséhez szűrje az összes visszaadott fájlt a és a értékkel `kind`  ==  `Transcription` `name`  ==  `{originalInputName.suffix}.json` .

Az átírási eredmények fájljának formátuma a következő:

```json
{
  "source": "...",                      // sas url of a given contentUrl or the path relative to the root of a given container
  "timestamp": "2020-06-16T09:30:21Z",  // creation time of the transcription, ISO 8601 encoded timestamp, combined date and time
  "durationInTicks": 41200000,          // total audio duration in ticks (1 tick is 100 nanoseconds)
  "duration": "PT4.12S",                // total audio duration, ISO 8601 encoded duration
  "combinedRecognizedPhrases": [        // concatenated results for simple access in single string for each channel
    {
      "channel": 0,                     // channel number of the concatenated results
      "lexical": "hello world",
      "itn": "hello world",
      "maskedITN": "hello world",
      "display": "Hello world."
    }
  ],
  "recognizedPhrases": [                // results for each phrase and each channel individually
    {
      "recognitionStatus": "Success",   // recognition state, e.g. "Success", "Failure"          
      "speaker": 1,                     // if `diarizationEnabled` is `true`, this is the identified speaker (1 or 2), otherwise this property is not present
      "channel": 0,                     // channel number of the result
      "offset": "PT0.07S",              // offset in audio of this phrase, ISO 8601 encoded duration 
      "duration": "PT1.59S",            // audio duration of this phrase, ISO 8601 encoded duration
      "offsetInTicks": 700000.0,        // offset in audio of this phrase in ticks (1 tick is 100 nanoseconds)
      "durationInTicks": 15900000.0,    // audio duration of this phrase in ticks (1 tick is 100 nanoseconds)

      // possible transcriptions of the current phrase with confidences
      "nBest": [
        {
          "confidence": 0.898652852,    // confidence value for the recognition of the whole phrase
          "lexical": "hello world",
          "itn": "hello world",
          "maskedITN": "hello world",
          "display": "Hello world.",

          // if wordLevelTimestampsEnabled is `true`, there will be a result for each word of the phrase, otherwise this property is not present
          "words": [
            {
              "word": "hello",
              "offset": "PT0.09S",
              "duration": "PT0.48S",
              "offsetInTicks": 900000.0,
              "durationInTicks": 4800000.0,
              "confidence": 0.987572
            },
            {
              "word": "world",
              "offset": "PT0.59S",
              "duration": "PT0.16S",
              "offsetInTicks": 5900000.0,
              "durationInTicks": 1600000.0,
              "confidence": 0.906032
            }
          ]
        }
      ]
    }
  ]
}
```

Az eredmény a következő mezőket tartalmazza:

:::row:::
   :::column span="1":::
      **Mező**
   :::column-end:::
   :::column span="2":::
      **Tartalom**
:::row-end:::
:::row:::
   :::column span="1":::
      `lexical`
   :::column-end:::
   :::column span="2":::
      A tényleges szavak felismerhetők.
:::row-end:::
:::row:::
   :::column span="1":::
      `itn`
   :::column-end:::
   :::column span="2":::
      Inverz szöveg – a felismert szöveg normalizált formája. A rövidítések ("doktor Smith" a "Dr Smith"), a telefonszámok és más átalakítások alkalmazása.
:::row-end:::
:::row:::
   :::column span="1":::
      `maskedITN`
   :::column-end:::
   :::column span="2":::
      A ITN űrlap a káromkodás maszkolásával lett alkalmazva.
:::row-end:::
:::row:::
   :::column span="1":::
      `display`
   :::column-end:::
   :::column span="2":::
      A felismert szöveg megjelenítési formája A rendszer a hozzáadott írásjeleket és a nagybetűket is tartalmazza.
:::row-end:::

## <a name="speaker-separation-diarization"></a>Beszélő elkülönítése (diarization)

A Diarization a hangszórók elválasztásának folyamata egy hanganyagban. A Batch-folyamat támogatja a diarization, és képes a Mono Channel-felvételek két hangszórójának felismerésére. A szolgáltatás nem érhető el a sztereó felvételeken.

Az diarization-mel rendelkező átiratok kimenete `Speaker` minden egyes átmásolt kifejezéshez tartalmaz egy bejegyzést. Ha a diarization nincs használatban, a `Speaker` tulajdonság nem szerepel a JSON-kimenetben. A diarization két hangokat támogatunk, így a hangszórók a vagy a néven azonosíthatók `1` `2` .

A diarization igényléséhez adja hozzá a `diarizationEnabled` tulajdonságot úgy, hogy `true` az alábbihoz hasonló HTTP-kérést adjon meg.

 ```json
{
  "contentUrls": [
    "<URL to an audio file to transcribe>",
  ],
  "properties": {
    "diarizationEnabled": true,
    "wordLevelTimestampsEnabled": true,
    "punctuationMode": "DictatedAndAutomatic",
    "profanityFilterMode": "Masked"
  },
  "locale": "en-US",
  "displayName": "Transcription of file using default model for en-US"
}
```

A Word-szintű időbélyegeket engedélyezni kell, ha a fenti kérelemben szereplő paraméterek jelzik.

## <a name="best-practices"></a>Ajánlott eljárások

A Batch transzkripciós szolgáltatás nagy számú beküldött átírást képes kezelni. Lekérdezheti az átiratok állapotát a [Get transzkripciós](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0/operations/GetTranscriptions)használatával.
Az eredmények lekérése után rendszeresen hívja meg a [delete átiratot](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0/operations/DeleteTranscription) a szolgáltatásból. Másik lehetőségként állítsa be `timeToLive` a tulajdonságot az eredmények végleges törlésének biztosítására.

## <a name="sample-code"></a>Mintakód

A teljes minták a [GitHub minta adattárában](https://aka.ms/csspeech/samples) érhetők el az `samples/batch` alkönyvtáron belül.

Frissítse a mintát az előfizetési adatokkal, a szolgáltatási régióval, a hangfájlra mutató URI-val, és a modell helyére, ha egyéni modellt használ.

[!code-csharp[Configuration variables for batch transcription](~/samples-cognitive-services-speech-sdk/samples/batch/csharp/program.cs#transcriptiondefinition)]

A mintakód beállítja az ügyfelet, és elküldi az átírási kérelmet. Ezután lekérdezi az állapotadatok adatait, és kinyomtatja az átírási folyamat részleteit.

```csharp
// get the status of our transcriptions periodically and log results
int completed = 0, running = 0, notStarted = 0;
while (completed < 1)
{
    completed = 0; running = 0; notStarted = 0;

    // get all transcriptions for the user
    paginatedTranscriptions = null;
    do
    {
        // <transcriptionstatus>
        if (paginatedTranscriptions == null)
        {
            paginatedTranscriptions = await client.GetTranscriptionsAsync().ConfigureAwait(false);
        }
        else
        {
            paginatedTranscriptions = await client.GetTranscriptionsAsync(paginatedTranscriptions.NextLink).ConfigureAwait(false);
        }

        // delete all pre-existing completed transcriptions. If transcriptions are still running or not started, they will not be deleted
        foreach (var transcription in paginatedTranscriptions.Values)
        {
            switch (transcription.Status)
            {
                case "Failed":
                case "Succeeded":
                    // we check to see if it was one of the transcriptions we created from this client.
                    if (!createdTranscriptions.Contains(transcription.Self))
                    {
                        // not created form here, continue
                        continue;
                    }

                    completed++;

                    // if the transcription was successful, check the results
                    if (transcription.Status == "Succeeded")
                    {
                        var paginatedfiles = await client.GetTranscriptionFilesAsync(transcription.Links.Files).ConfigureAwait(false);

                        var resultFile = paginatedfiles.Values.FirstOrDefault(f => f.Kind == ArtifactKind.Transcription);
                        var result = await client.GetTranscriptionResultAsync(new Uri(resultFile.Links.ContentUrl)).ConfigureAwait(false);
                        Console.WriteLine("Transcription succeeded. Results: ");
                        Console.WriteLine(JsonConvert.SerializeObject(result, SpeechJsonContractResolver.WriterSettings));
                    }
                    else
                    {
                        Console.WriteLine("Transcription failed. Status: {0}", transcription.Properties.Error.Message);
                    }

                    break;

                case "Running":
                    running++;
                    break;

                case "NotStarted":
                    notStarted++;
                    break;
            }
        }

        // for each transcription in the list we check the status
        Console.WriteLine(string.Format("Transcriptions status: {0} completed, {1} running, {2} not started yet", completed, running, notStarted));
    }
    while (paginatedTranscriptions.NextLink != null);

    // </transcriptionstatus>
    // check again after 1 minute
    await Task.Delay(TimeSpan.FromMinutes(1)).ConfigureAwait(false);
}
```

Az előző hívásokkal kapcsolatos részletes információkért tekintse meg a [hencegő dokumentumot](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0). Az itt látható teljes minta esetében lépjen a [githubra](https://aka.ms/csspeech/samples) az `samples/batch` alkönyvtárban.

Ez a példa egy aszinkron telepítőt használ a hang és a fogadás átírási állapotának elküldéséhez.
A `PostTranscriptions` metódus elküldi a hangfájl részleteit, és a `GetTranscriptions` metódus fogadja az állapotokat.
`PostTranscriptions` egy leírót ad vissza, és a `GetTranscriptions` használatával létrehoz egy leírót az átirat állapotának lekéréséhez.

Ez a mintakód nem ad meg egyéni modellt. A szolgáltatás az alapmodellt használja a fájl vagy fájlok átírásához. A modell megadásához ugyanezt a módszert kell átadni az egyéni modellhez tartozó modell-referenciára.

> [!NOTE]
> Az alapértékek átírásakor nem kell deklarálnia az alapmodell AZONOSÍTÓját.

## <a name="next-steps"></a>Következő lépések

- [Beszéd a Text V3 API-hoz – dokumentáció](https://centralus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0/operations/CopyModelToSubscription)
