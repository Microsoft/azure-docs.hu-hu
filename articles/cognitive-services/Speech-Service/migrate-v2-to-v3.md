---
title: Migrálás v2-ről v3 REST API-Speech szolgáltatásra
titleSuffix: Azure Cognitive Services
description: Ez a dokumentum segít a fejlesztőknek, hogy a beszédfelismerési szolgáltatások beszéd – szöveg REST APIjában áttelepítse a kódot a v2-től a v3-ig.
services: cognitive-services
author: bexxx
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 02/12/2020
ms.author: rbeckers
ms.custom: devx-track-csharp
ms.openlocfilehash: 9c8016b566db8be1b7f5c5ddb8d92123d6673db5
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98569844"
---
# <a name="migrate-code-from-v20-to-v30-of-the-rest-api"></a>Telepítse át a 2.0-s és a v 3.0-s verzióját a REST API

A v2-höz képest a beszédfelismerési REST API szolgáltatások v3-as verziója megbízhatóbb, könnyebben használható, és a hasonló szolgáltatásokhoz tartozó API-k jobban konzisztensek. A legtöbb csapat a v2-ről a v3-re migrálhat egy-két nap alatt.

## <a name="forward-compatibility"></a>Továbbítási kompatibilitás

A v2-ből származó összes entitás a V3 API-ban is megtalálható ugyanazon identitás alatt. Ha az eredmény sémája megváltozott (például átírások), az API v3-as verziójának lekérésének eredménye a v3 sémát használja. Az API v2-es verziójának beolvasásának eredménye ugyanazt a v2-sémát használja. A v3-es újonnan létrehozott entitások **nem**   érhetők el a v2 API-k válaszában. 

## <a name="migration-steps"></a>A migrálás lépései

Az áttelepítésre való felkészülés során figyelembe kell venni az elemek összegzését. Részletek az egyes hivatkozásokban találhatók. Az API aktuális használatából függően nem minden itt felsorolt lépés alkalmazható. Csak néhány módosítás nem triviális módosításokat igényel a hívó kódban. A legtöbb módosításhoz csak az elemek nevének módosítása szükséges. 

Általános módosítások: 

1. [Állomásnév módosítása](#host-name-changes)

1. [A tulajdonság azonosítójának átnevezése az ügyfél kódjában](#identity-of-an-entity) 

1. [Kód módosítása az entitások gyűjteményének megismétléséhez](#working-with-collections-of-entities)

1. [Nevezze át a tulajdonság nevét a displayName értékre az ügyfél kódjában](#name-of-an-entity)

1. [A hivatkozott entitások metaadatainak lekérésének módosítása](#accessing-referenced-entities)

1. Ha batch-átírást használ: 

    * [Kód módosítása a Batch-átírások létrehozásához](#creating-transcriptions) 

    * [Kód átalakítása az új átírási eredmények sémájába](#format-of-v3-transcription-results)

    * [Kód módosítása az eredmények lekéréséhez](#getting-the-content-of-entities-and-the-results)

1. Ha egyéni Modelles képzést/tesztelési API-kat használ: 

    * [Módosítások alkalmazása az egyéni modell betanításához](#customizing-models)

    * [Az alap-és az egyéni modellek beolvasásának módosítása](#retrieving-base-and-custom-models)

    * [Az elérésiút-szegmens accuracytests átnevezése az ügyfél kódjában szereplő értékelésekhez](#accuracy-tests)

1. Ha endpoints API-kat használ:

    * [A végponti naplók lekérésének módosítása](#retrieving-endpoint-logs)

1. Egyéb kisebb változások: 

    * [Adja át az összes egyéni tulajdonságot customProperties-ként a POST-kérések tulajdonságai helyett](#using-custom-properties)

    * [A válasz fejlécének helye a művelet helye helyett](#response-headers)

## <a name="breaking-changes"></a>Kompatibilitástörő változások

### <a name="host-name-changes"></a>Állomásnév módosítása

A végpontok állomásneve módosultak a verzióról a verzióra `{region}.cris.ai` `{region}.api.cognitive.microsoft.com` . Az új végpontokhoz tartozó elérési utak már nem tartalmaznak, `api/` mert az állomásnév részét képezi. A [hencegő dokumentum](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0) felsorolja az érvényes régiókat és elérési utakat.
>[!IMPORTANT]
>Módosítsa a gazdagépet a helyről a `{region}.cris.ai` -re, `{region}.api.cognitive.microsoft.com` ahol a régió a beszédfelismerési előfizetés régiója. Távolítsa el az `api/` ügyfél kódjának bármely útvonalát is.

### <a name="identity-of-an-entity"></a>Entitás identitása

A tulajdonság `id` most `self` . A v2-ben az API-felhasználóknak tudnia kellett, hogyan jönnek létre az API-k elérési útjai. Ez nem bővíthető és szükségtelen munkát igényelt a felhasználótól. A tulajdonságot ( `id` UUID) a `self` (karakterlánc) helyettesíti, amely az entitás (URL) helye. Az érték továbbra is egyedi az összes entitás között. Ha a a `id` kódban karakterláncként van tárolva, az Átnevezés elegendő az új séma támogatásához. Mostantól használhatja a `self` tartalmat URL-címként az `GET` `PATCH` `DELETE` entitáshoz, és Rest-hívásokat is.

Ha az entitás további, más elérési utakon elérhető funkciókkal is rendelkezik, a lista alatt találhatók `links` . Az átíráshoz az alábbi példa egy külön módszert mutat be az `GET` átirat tartalmára:
>[!IMPORTANT]
>Nevezze át a tulajdonságot az `id` `self` ügyfél kódjába. Szükség esetén módosítsa a típust `uuid` `string` . 

**v2 átirat:**

```json
{
    "id": "9891c965-bb32-4880-b14b-6d44efb158f3",
    "createdDateTime": "2019-01-07T11:34:12Z",
    "lastActionDateTime": "2019-01-07T11:36:07Z",
    "status": "Succeeded",
    "locale": "en-US",
    "name": "Transcription using locale en-US"
}
```

**v3 átirat:**

```json
{
    "self": "https://westus.api.cognitive.microsoft.com/speechtotext/v3.0/transcriptions/9891c965-bb32-4880-b14b-6d44efb158f3",
    "createdDateTime": "2019-01-07T11:34:12Z",
    "lastActionDateTime": "2019-01-07T11:36:07Z",
    "status": "Succeeded",
    "locale": "en-US", 
    "displayName": "Transcription using locale en-US",
    "links": {
      "files": "https://westus.api.cognitive.microsoft.com/speechtotext/v3.0/transcriptions/9891c965-bb32-4880-b14b-6d44efb158f3/files"
    }
}
```

A kód implementációtól függően előfordulhat, hogy nem elég a tulajdonság átnevezése. Azt javasoljuk, hogy a visszaadott `self` és az `links` értékeket használja a REST-hívások célként megadott URL-címére ahelyett, hogy elérési utat kellene létrehoznia az ügyfélen. A visszaadott URL-címek használatával biztos lehet abban, hogy az elérési utakban a jövőbeli változások nem bontják le az ügyfélszoftvert.

### <a name="working-with-collections-of-entities"></a>Entitások gyűjteményének használata

Korábban a v2 API az összes elérhető entitást visszaadott egy eredményben. Annak érdekében, hogy részletesebben szabályozható legyen a v3-as várt válasz mérete, az összes gyűjtési eredmény tördeléssel van elválasztva. Ön szabályozhatja a visszaadott entitások számát és a lap kezdő eltolását. Ez a viselkedés megkönnyíti a válaszfájl futtatókörnyezetének előrejelzését.

A válasz alapszintű formája megegyezik az összes gyűjtemény esetében:

```json
{
    "values": [
        {     
        }
    ],
    "@nextLink": "https://{region}.api.cognitive.microsoft.com/speechtotext/v3.0/{collection}?skip=100&top=100"
}
```

A `values` tulajdonság a rendelkezésre álló gyűjtemény entitások egy részhalmazát tartalmazza. A darabszám és az eltolás a `skip` és a `top` lekérdezési paraméterek használatával vezérelhető. Ha `@nextLink` nem `null` , több információ is rendelkezésre áll, és a beolvasás után a következő adatköteget kérheti le `$.@nextLink` .

Ehhez a változáshoz az `GET` összes elem visszaadását követően egy hurokban kell meghívnia a gyűjteményt.

>[!IMPORTANT]
>Ha a GET `speechtotext/v3.0/{collection}` értéke tartalmazza a értéket `$.@nextLink` , folytassa a kiadást, `GETs` amíg nincs `$.@nextLink` beállítva a `$.@nextLink` gyűjtemény összes elemének beolvasása.

### <a name="creating-transcriptions"></a>Átiratok létrehozása

Az átírások kötegek létrehozásával kapcsolatos részletes leírást a [Batch-átírási útmutatóban](./batch-transcription.md)találhat.

A v3 átírási API lehetővé teszi, hogy explicit módon beállítsa a megadott átírási beállításokat. Az összes (opcionális) konfigurációs tulajdonság mostantól beállítható a `properties` tulajdonságban.
A v3-as verzió több bemeneti fájlt is támogat, ezért az URL-címek listáját kell megadnia, nem pedig egyetlen URL-címet a v2-nek. A v2-tulajdonságnév `recordingsUrl` mostantól `contentUrls` v3. Az átírások elemzésének funkcionalitása el lett távolítva a v3-as verzióban. Lásd: a Microsoft kognitív szolgáltatás [szöveges elemzése](https://azure.microsoft.com/en-us/services/cognitive-services/text-analytics/) az érzelmek elemzési lehetőségeiről.

Az új tulajdonsága `timeToLive` `properties` segít a meglévő befejezett entitások kimetszésében. A `timeToLive` meghatározza azt az időtartamot, amely után a befejezett entitást automatikusan törli a rendszer. Állítsa be magas értékre (például), `PT12H` Ha az entitásokat folyamatosan nyomon követik, felhasználják és törölik, és ezért általában hosszú ideig, 12 óra elteltével dolgozzák fel.

**v2 átirat utáni kérelem törzse:**

```json
{
  "locale": "en-US",
  "name": "Transcription using locale en-US",
  "recordingsUrl": "https://contoso.com/mystoragelocation",
  "properties": {
    "AddDiarization": "False",
    "AddWordLevelTimestamps": "False",
    "PunctuationMode": "DictatedAndAutomatic",
    "ProfanityFilterMode": "Masked"
  }
}
```

**v3 átirat POST kérelem törzse:**

```json
{
  "locale": "en-US",
  "displayName": "Transcription using locale en-US",
  "contentUrls": [
    "https://contoso.com/mystoragelocation",
    "https://contoso.com/myotherstoragelocation"
  ],
  "properties": {
    "diarizationEnabled": false,
    "wordLevelTimestampsEnabled": false,
    "punctuationMode": "DictatedAndAutomatic",
    "profanityFilterMode": "Masked"
  }
}
```
>[!IMPORTANT]
>Nevezze át a tulajdonságot egy `recordingsUrl` `contentUrls` URL-cím helyett, és adjon át egy URL-tömböt. A vagy a helyett adja meg a beállításokat `diarizationEnabled` `wordLevelTimestampsEnabled` `bool` `string` .

### <a name="format-of-v3-transcription-results"></a>V3 átirat eredményeinek formátuma

Az átírási eredmények sémája némileg megváltozott a valós idejű végpontok által létrehozott átírásokhoz való igazításhoz. Az új formátum részletes leírását a [Batch átírási útmutatójában](./batch-transcription.md)találja. Az eredmény sémáját a rendszer a GitHub- [minta adattárában](https://aka.ms/csspeech/samples) teszi közzé `samples/batch/transcriptionresult_v3.schema.json` .

A tulajdonságok nevei mostantól a Camel-tokozású, a `channel` és a és a most az `speaker` egész típusú értékek használata. Az időtartamok formátuma mostantól az ISO 8601-ben leírt struktúrát használja, amely a többi Azure API-ban használt időtartam-formázásnak felel meg.

Egy v3 átírási eredmény mintája. A különbségeket a megjegyzések írják le.

```json
{
  "source": "...",                      // (new in v3) was AudioFileName / AudioFileUrl
  "timestamp": "2020-06-16T09:30:21Z",  // (new in v3)
  "durationInTicks": 41200000,          // (new in v3) was AudioLengthInSeconds
  "duration": "PT4.12S",                // (new in v3)
  "combinedRecognizedPhrases": [        // (new in v3) was CombinedResults
    {
      "channel": 0,                     // (new in v3) was ChannelNumber
      "lexical": "hello world",
      "itn": "hello world",
      "maskedITN": "hello world",
      "display": "Hello world."
    }
  ],
  "recognizedPhrases": [                // (new in v3) was SegmentResults
    {
      "recognitionStatus": "Success",   // 
      "channel": 0,                     // (new in v3) was ChannelNumber
      "offset": "PT0.07S",              // (new in v3) new format, was OffsetInSeconds
      "duration": "PT1.59S",            // (new in v3) new format, was DurationInSeconds
      "offsetInTicks": 700000.0,        // (new in v3) was Offset
      "durationInTicks": 15900000.0,    // (new in v3) was Duration

      // possible transcriptions of the current phrase with confidences
      "nBest": [
        {
          "confidence": 0.898652852,phrase
          "speaker": 1,
          "lexical": "hello world",
          "itn": "hello world",
          "maskedITN": "hello world",
          "display": "Hello world.",

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
>[!IMPORTANT]
>Deszerializálja az átírás eredményét az új típusba, ahogy az a fentiekben látható. Egy hangcsatornán belüli egyetlen fájl helyett a csatornák megkülönböztetése az egyes elemekhez tartozó tulajdonságok értékének ellenőrzésével `channel` `recognizedPhrases` . Minden bemeneti fájlhoz már létezik egyetlen eredményhalmaz.


### <a name="getting-the-content-of-entities-and-the-results"></a>Az entitások és az eredmények tartalmának beolvasása

A v2-ben a bemeneti vagy az eredmény-fájlokra mutató hivatkozások a többi entitás metaadataival vannak befoglalva. A v3 fejlesztése során egyértelmű elkülönítés van az entitás metaadatainak között (amelyet a beolvasás ad vissza `$.self` ), valamint a részleteket és a hitelesítő adatokat az eredmény fájljainak eléréséhez. Ez az elkülönítés segít az ügyféladatok védelme terén, és lehetővé teszi a hitelesítő adatok érvényességi időtartamának részletes szabályozását.

A v3-as verzióban `links` vegyen fel egy nevű altulajdonságot, `files` Ha az entitás adatokat (adatkészleteket, átírásokat, végpontokat vagy értékeléseket) tesz elérhetővé. A beolvasás a `$.links.files` fájlok listáját és egy sas URL-címet ad vissza az egyes fájlok tartalmának eléréséhez. A SAS URL-címek érvényességi időtartamának szabályozásához a lekérdezési paraméter használható `sasValidityInSeconds` az élettartam megadásához.

**v2 átirat:**

```json
{
  "id": "9891c965-bb32-4880-b14b-6d44efb158f3",
  "status": "Succeeded",
  "reportFileUrl": "https://contoso.com/report.txt?st=2018-02-09T18%3A07%3A00Z&se=2018-02-10T18%3A07%3A00Z&sp=rl&sv=2017-04-17&sr=b&sig=6c044930-3926-4be4-be76-f728327c53b5",
  "resultsUrls": {
    "channel_0": "https://contoso.com/audiofile1.wav?st=2018-02-09T18%3A07%3A00Z&se=2018-02-10T18%3A07%3A00Z&sp=rl&sv=2017-04-17&sr=b&sig=6c044930-3926-4be4-be76-f72832e6600c",
    "channel_1": "https://contoso.com/audiofile2.wav?st=2018-02-09T18%3A07%3A00Z&se=2018-02-10T18%3A07%3A00Z&sp=rl&sv=2017-04-17&sr=b&sig=3e0163f1-0029-4d4a-988d-3fba7d7c53b5"
  }
}
```

**v3 átirat:**

```json
{
    "self": "https://westus.api.cognitive.microsoft.com/speechtotext/v3.0/transcriptions/9891c965-bb32-4880-b14b-6d44efb158f3",
    "links": {
      "files": "https://westus.api.cognitive.microsoft.com/speechtotext/v3.0/transcriptions/9891c965-bb32-4880-b14b-6d44efb158f3/files"
    } 
}
```

**A beszerzés a `$.links.files` következő eredményt eredményezi:**

```json
{
  "values": [
    {
      "self": "https://westus.api.cognitive.microsoft.com/speechtotext/v3.0/transcriptions/9891c965-bb32-4880-b14b-6d44efb158f3/files/f23e54f5-ed74-4c31-9730-2f1a3ef83ce8",
      "name": "Name",
      "kind": "Transcription",
      "properties": {
        "size": 200
      },
      "createdDateTime": "2020-01-13T08:00:00Z",
      "links": {
        "contentUrl": "https://customspeech-usw.blob.core.windows.net/artifacts/mywavefile1.wav.json?st=2018-02-09T18%3A07%3A00Z&se=2018-02-10T18%3A07%3A00Z&sp=rl&sv=2017-04-17&sr=b&sig=e05d8d56-9675-448b-820c-4318ae64c8d5"
      }
    },
    {
      "self": "https://westus.api.cognitive.microsoft.com/speechtotext/v3.0/transcriptions/9891c965-bb32-4880-b14b-6d44efb158f3/files/28bc946b-c251-4a86-84f6-ea0f0a2373ef",
      "name": "Name",
      "kind": "TranscriptionReport",
      "properties": {
        "size": 200
      },
      "createdDateTime": "2020-01-13T08:00:00Z",
      "links": {
        "contentUrl": "https://customspeech-usw.blob.core.windows.net/artifacts/report.json?st=2018-02-09T18%3A07%3A00Z&se=2018-02-10T18%3A07%3A00Z&sp=rl&sv=2017-04-17&sr=b&sig=e05d8d56-9675-448b-820c-4318ae64c8d5"
      }
    }
  ],
  "@nextLink": "https://westus.api.cognitive.microsoft.com/speechtotext/v3.0/transcriptions/9891c965-bb32-4880-b14b-6d44efb158f3/files?skip=2&top=2"
}
```

A `kind` tulajdonság a fájl tartalmának formátumát jelzi. Az átírások esetében a fajta fájl `TranscriptionReport` összefoglalása a feladatoknak és a fajta fájloknak az `Transcription` eredménye.

>[!IMPORTANT]
>A műveletek eredményeinek lekéréséhez használja a-t a on `GET` `/speechtotext/v3.0/{collection}/{id}/files` vagy a válaszában `GET` `/speechtotext/v3.0/{collection}/{id}` `/speechtotext/v3.0/{collection}` .

### <a name="customizing-models"></a>Modellek testreszabása

A v3 előtt az _akusztikus modell_ és a _nyelvi modell_ megkülönböztetése történt a modell betanítása előtt. Ez a különbség azt eredményezte, hogy a végpontok vagy átírások létrehozásakor több modellt kell megadnia. A hívó számára a folyamat leegyszerűsítése érdekében eltávolítjuk a különbségeket, és minden a modell betanításához használt adatkészletek tartalmától függ. Ezzel a módosítással a modell létrehozása mostantól támogatja a vegyes adatkészleteket (a nyelvi adatokat és az akusztikai adatokat). A végpontoknak és az átírásoknak már csak egy modellre van szükségük.

Ezzel a módosítással a `kind` `POST` művelethez szükséges egy műveletet eltávolították, és a `datasets[]` tömb már több azonos vagy vegyes típusú adatkészletet is tartalmazhat.

A betanított modell eredményeinek javításához a nyelvi képzés során a rendszer automatikusan használja az akusztikai adatmennyiséget. Általánosságban elmondható, hogy a V3 API-val létrehozott modellek pontosabb eredményeket biztosítanak, mint a v2 API-val létrehozott modellek.

>[!IMPORTANT]
>Az akusztikus és a nyelvi modell részének testreszabásához továbbítsa a bejegyzés összes szükséges nyelvét és akusztikai adatkészletét a következőre: `datasets[]` `/speechtotext/v3.0/models` . Ez egyetlen modellt hoz létre, amelyben mindkét rész testre van szabva.

### <a name="retrieving-base-and-custom-models"></a>Alapszintű és egyéni modellek beolvasása

A rendelkezésre álló modellek leegyszerűsítése érdekében a v3 elválasztta a "alapmodellek" gyűjteményeit az ügyfél "testreszabott modelljei" alapján. A két útvonal most `GET /speechtotext/v3.0/models/base` és `GET /speechtotext/v3.0/models/` .

A v2-ben az összes modell egyetlen válaszban lett visszaadva.

>[!IMPORTANT]
>A testreszabáshoz megadott alapmodellek listájának lekéréséhez használja a következőt: `GET` `/speechtotext/v3.0/models/base` . Megtalálhatja a saját testreszabott modelljeit is `GET` `/speechtotext/v3.0/models` .

### <a name="name-of-an-entity"></a>Entitás neve

A `name` tulajdonság most `displayName` . Ez konzisztens más Azure API-kkal, hogy ne jelezze az identitás tulajdonságait. Ennek a tulajdonságnak az értéke nem lehet egyedi, és az entitásnak a művelettel való létrehozása után módosítható `PATCH` .

**v2 átirat:**

```json
{
    "name": "Transcription using locale en-US"
}
```

**v3 átirat:**

```json
{
    "displayName": "Transcription using locale en-US"
}
```

>[!IMPORTANT]
>Nevezze át a tulajdonságot az `name` `displayName` ügyfél kódjába.

### <a name="accessing-referenced-entities"></a>A hivatkozott entitások elérése

A v2-ben a hivatkozott entitások mindig beágyazottak voltak, például egy végpont használt modelljei. Az entitások beágyazásával nagy válaszokat okoztak, és a felhasználók ritkán használják fel a beágyazott tartalmat. A válasz méretének csökkentése és a teljesítmény javítása érdekében a hivatkozott entitások már nem jelennek meg a válaszban. Ehelyett a másik entitásra mutató hivatkozás jelenik meg, és közvetlenül használható egy későbbi `GET` (URL-cím is), amely a hivatkozással megegyező mintát követve `self` .

**v2 átirat:**

```json
{
  "id": "9891c965-bb32-4880-b14b-6d44efb158f3",
  "models": [
    {
      "id": "827712a5-f942-4997-91c3-7c6cde35600b",
      "modelKind": "Language",
      "lastActionDateTime": "2019-01-07T11:36:07Z",
      "status": "Running",
      "createdDateTime": "2019-01-07T11:34:12Z",
      "locale": "en-US",
      "name": "Acoustic model",
      "description": "Example for an acoustic model",
      "datasets": [
        {
          "id": "702d913a-8ba6-4f66-ad5c-897400b081fb",
          "dataImportKind": "Language",
          "lastActionDateTime": "2019-01-07T11:36:07Z",
          "status": "Succeeded",
          "createdDateTime": "2019-01-07T11:34:12Z",
          "locale": "en-US",
          "name": "Language dataset",
        }
      ]
    },
  ]
}
```

**v3 átirat:**

```json
{
  "self": "https://westus.api.cognitive.microsoft.com/speechtotext/v3.0/transcriptions/9891c965-bb32-4880-b14b-6d44efb158f3",
  "model": {
    "self": "https://westus.api.cognitive.microsoft.com/speechtotext/v3.0/models/021a72d0-54c4-43d3-8254-27336ead9037"
  }
}
```

Ha a fenti példában látható módon kell felhasználnia egy hivatkozott modell részleteit, egyszerűen adja meg a beolvasást `$.model.self` .

>[!IMPORTANT]
>A hivatkozott entitások metaadatainak lekéréséhez adja meg a beolvasást `$.{referencedEntity}.self` , például az átíráshoz tartozó modell lekéréséhez `GET` `$.model.self` .


### <a name="retrieving-endpoint-logs"></a>Végponti naplók beolvasása

A szolgáltatás által támogatott naplózási végpont eredményeinek v2-es verziója. A végpontok eredményeinek a v2-vel való lekéréséhez hozzon létre egy "adatexportálást", amely az adott időtartomány által meghatározott eredmények pillanatképét képviseli. A kötegek exportálásának folyamata nem volt rugalmas. A V3 API hozzáférést biztosít az egyes fájlokhoz, és lehetővé teszi a rajtuk való iterációt.

**A v3-végpont sikeres futtatása:**

```json
{
  "self": "https://westus.api.cognitive.microsoft.com/speechtotext/v3.0/endpoints/afa0669c-a01e-4693-ae3a-93baf40f26d6",
  "links": {
    "logs": "https://westus.api.cognitive.microsoft.com/speechtotext/v3.0/endpoints/afa0669c-a01e-4693-ae3a-93baf40f26d6/files/logs" 
  }
}
```

**Válasz a GET `$.links.logs` :**

```json
{
  "values": [
    {
      "self": "https://westus.api.cognitive.microsoft.com/speechtotext/v3.0/endpoints/6d72ad7e-f286-4a6f-b81b-a0532ca6bcaa/files/logs/2019-09-20_080000_3b5f4628-e225-439d-bd27-8804f9eed13f.wav",
      "name": "2019-09-20_080000_3b5f4628-e225-439d-bd27-8804f9eed13f.wav",
      "kind": "Audio",
      "properties": {
        "size": 12345
      },
      "createdDateTime": "2020-01-13T08:00:00Z",
      "links": {
        "contentUrl": "https://customspeech-usw.blob.core.windows.net/artifacts/2019-09-20_080000_3b5f4628-e225-439d-bd27-8804f9eed13f.wav?st=2018-02-09T18%3A07%3A00Z&se=2018-02-10T18%3A07%3A00Z&sp=rl&sv=2017-04-17&sr=b&sig=e05d8d56-9675-448b-820c-4318ae64c8d5"
      }
    }    
  ],
  "@nextLink": "https://westus.api.cognitive.microsoft.com/speechtotext/v3.0/endpoints/afa0669c-a01e-4693-ae3a-93baf40f26d6/files/logs?top=2&SkipToken=2!188!MDAwMDk1ITZhMjhiMDllLTg0MDYtNDViMi1hMGRkLWFlNzRlOGRhZWJkNi8yMDIwLTA0LTAxLzEyNDY0M182MzI5NGRkMi1mZGYzLTRhZmEtOTA0NC1mODU5ZTcxOWJiYzYud2F2ITAwMDAyOCE5OTk5LTEyLTMxVDIzOjU5OjU5Ljk5OTk5OTlaIQ--"
}
```

A végponti naplók tördelése az összes többi gyűjteményhez hasonlóan működik, azzal a különbséggel, hogy nem lehet eltolást megadni. A nagy mennyiségű rendelkezésre álló érték miatt a tördelést a kiszolgáló határozza meg.

A v3-as verzióban minden végponti napló egyenként törölhető, `DELETE` Ha egy műveletet ad ki egy `self` fájlon vagy `DELETE` a on használatával `$.links.logs` . A befejezési dátum megadásához a lekérdezési paramétert `endDate` hozzá lehet adni a kéréshez.

>[!IMPORTANT]
>A naplófájlok `/api/speechtotext/v2.0/endpoints/{id}/data` `/v3.0/endpoints/{id}/files/logs/` külön való eléréséhez a használaton alapuló napló-exportálások létrehozása helyett. 

### <a name="using-custom-properties"></a>Egyéni tulajdonságok használata

Ha az egyéni tulajdonságokat el szeretné különíteni a választható konfigurációs tulajdonságoktól, az összes explicit módon elnevezett tulajdonság mostantól a `properties` tulajdonságban található, és a hívók által meghatározott összes tulajdonság már a `customProperties` tulajdonságban található.

**v2 átirat entitás:**

```json
{
  "properties": {
    "customerDefinedKey": "value",
    "diarizationEnabled": "False",
    "wordLevelTimestampsEnabled": "False"
  }
}
```

**v3 átirat entitás:**

```json
{
  "properties": {
    "diarizationEnabled": false,
    "wordLevelTimestampsEnabled": false 
  },
  "customProperties": {
    "customerDefinedKey": "value"
  }
}
```

Ez a módosítás azt is lehetővé teszi, hogy a megfelelő típusokat használja az összes explicit módon megnevezett tulajdonsághoz `properties` (például string helyett Boolean).

>[!IMPORTANT]
>Adja át az összes egyéni tulajdonságot a `customProperties` `properties` `POST` kérések helyett.

### <a name="response-headers"></a>Válaszfejlécek

a (z) v3 már nem adja vissza a `Operation-Location` fejlécet a `Location` kérelmek fejlécén kívül `POST` . A v2-ben mindkét fejléc értéke azonos volt. Most már csak `Location` a visszaadott értéket adja vissza.

Mivel az új API-verziót mostantól az Azure API Management (APIM) felügyeli, a szabályozáshoz kapcsolódó fejlécek, `X-RateLimit-Limit` `X-RateLimit-Remaining` és `X-RateLimit-Reset` nem szerepelnek a válasz fejlécében.

>[!IMPORTANT]
>A helyett olvassa el a helyet a válasz fejlécből `Location` `Operation-Location` . 429-hibakód esetén olvassa el a `Retry-After` fejléc értékét a, vagy a helyett `X-RateLimit-Limit` `X-RateLimit-Remaining` `X-RateLimit-Reset` .


### <a name="accuracy-tests"></a>Pontossági tesztek

A pontossági tesztek átnevezve lettek az értékelésre, mert az új név jobban leírja, hogy mit jelentenek. Az új elérési utak a következők: `https://{region}.api.cognitive.microsoft.com/speechtotext/v3.0/evaluations` .

>[!IMPORTANT]
>Nevezze át az elérésiút-szegmenst az `accuracytests` `evaluations` ügyfél kódjába.


## <a name="next-steps"></a>Következő lépések

Vizsgálja meg a Speech Services által biztosított gyakran használt REST API-k összes funkcióját:

* [Diktálás REST API](rest-speech-to-text.md)
* A REST API v3-as felvágási [dokumentuma](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0)
* A Batch-átírások végrehajtásához a mintakód esetében tekintse meg a [GitHub-minta tárházat](https://aka.ms/csspeech/samples) az `samples/batch` alkönyvtárban.
