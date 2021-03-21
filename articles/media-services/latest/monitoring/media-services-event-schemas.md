---
title: Media Services eseményekhez Azure Event Grid sémák
description: Ismerkedjen meg a Azure Event Gridokkal Media Services eseményekhez megadott tulajdonságokkal.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: reference
ms.date: 03/17/2021
ms.author: inhenkel
ms.openlocfilehash: 6b502690a62410f1e8d2950074e8b479f7dc7f88
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "104609318"
---
# <a name="azure-event-grid-schemas-for-media-services-events"></a>Media Services eseményekhez Azure Event Grid sémák

[!INCLUDE [media services api v3 logo](../includes/v3-hr.md)]

Ez a cikk a Media Services események sémáit és tulajdonságait ismerteti.

A mintául szolgáló parancsfájlok és oktatóanyagok listáját itt tekintheti meg: [Media Services eseményforrás](../../../event-grid/event-schema-subscriptions.md).

## <a name="job-related-event-types"></a>Feladathoz kapcsolódó események típusai

Media Services elbocsátja az alább ismertetett **feladathoz** kapcsolódó eseménytípus típusát. A **feladathoz** kapcsolódó események két kategóriába sorolhatók: "a feladatok állapotának változásának figyelése" és "a feladatok kimeneti állapotának módosítása". 

A JobStateChange eseményre való feliratkozással regisztrálhat az összes eseményre. Vagy előfizethet csak bizonyos eseményekre (például a végső állapotokra, például a JobErrored, a JobFinished és a JobCanceled).   

### <a name="monitoring-job-state-changes"></a>Figyelési feladatok állapotának változásai

| Eseménytípus | Leírás |
| ---------- | ----------- |
| Microsoft. Media. JobStateChange| Esemény beolvasása a feladatok állapotának változásaihoz. |
| Microsoft. Media. JobScheduled| Esemény beolvasása a feladatok ütemezett állapotba való átváltásakor. |
| Microsoft. Media. JobProcessing| Esemény, amikor a feladatok feldolgozási állapotba kerülnek. |
| Microsoft. Media. JobCanceling| Esemény beolvasása, amikor a feladat átvált az állapot megszakítására. |
| Microsoft. Media. JobFinished| Esemény beolvasása, amikor a feladatok befejezve állapotba kerülnek. Ez egy végső állapot, amely tartalmazza a feladatok kimeneteit.|
| Microsoft. Media. JobCanceled| Esemény beolvasása, ha a feladat megszakított állapotra vált. Ez egy végső állapot, amely tartalmazza a feladatok kimeneteit.|
| Microsoft. Media. JobErrored| Esemény beolvasása, amikor a feladatok hibás állapotra váltanak. Ez egy végső állapot, amely tartalmazza a feladatok kimeneteit.|

Lásd: az alábbi [séma-példák](#event-schema-examples) .

### <a name="monitoring-job-output-state-changes"></a>A feladatok kimeneti állapotának változásai

A feladatok több feladatot is tartalmazhatnak (ha úgy állította be az átalakítást, hogy több feladatot is kiírjanak.) Ha nyomon szeretné követni az egyes feladatok kimenetének részleteit, figyelje a feladatok kimeneti változási eseményeit.

Az egyes **feladatok** magasabb szinten lesznek, mint a **JobOutput**, így a feladatok kimeneti eseményei egy adott feladatokon belül kerülnek beindításra. 

A-ben lévő hibaüzenetek az `JobFinished` `JobCanceled` `JobError` egyes feladatok kimenetének összesített eredményeit jelenítik meg – ha mindegyik elkészült. Míg a feladat kimeneti eseményei az egyes feladatok befejeződése után következnek be. Ha például kódolási kimenettel rendelkezik, amelyet egy videó-elemzési kimenet követ, akkor a végső JobFinished esemény előtt két eseményt kell beolvasnia, mielőtt az összesített adatokat beolvassa.

| Eseménytípus | Leírás |
| ---------- | ----------- |
| Microsoft. Media. JobOutputStateChange| Esemény beolvasása a feladatok kimeneti állapotának változásaihoz. |
| Microsoft. Media. JobOutputScheduled| Esemény beolvasása, amikor a feladatok kimenete ütemezett állapotba kerül. |
| Microsoft. Media. JobOutputProcessing| Olyan esemény beszerzése, amikor a feladatok kimenete feldolgozási állapotba kerül. |
| Microsoft. Media. JobOutputCanceling| Olyan esemény beszerzése, amikor a feladat kimenete megszakítja az állapotot.|
| Microsoft. Media. JobOutputFinished| Esemény beolvasása, amikor a feladatok kimenete befejezett állapotba vált.|
| Microsoft. Media. JobOutputCanceled| Esemény beolvasása, ha a feladat kimenete megszakított állapotra változik.|
| Microsoft. Media. JobOutputErrored| Esemény beolvasása, amikor a feladatok kimenete hibás állapotba vált.|

Lásd: az alábbi [séma-példák](#event-schema-examples) .

### <a name="monitoring-job-output-progress"></a>A feladatok kimeneti folyamatának figyelése

| Eseménytípus | Leírás |
| ---------- | ----------- |
| Microsoft. Media. JobOutputProgress| Ez az esemény a feladatok feldolgozásának folyamatát tükrözi, 0%-ról 100%-ra. A szolgáltatás egy eseményt próbál elküldeni, ha a folyamatjelző értéke 5% vagy nagyobb, vagy a legutóbbi esemény (szívverés) óta több mint 30 másodperc volt. A Progress érték nem garantált 0%-os indításra, vagy a 100% elérésére, és nem garantált, hogy az idő múlásával folyamatosan növekszik. Ez az esemény nem használható annak megállapításához, hogy a feldolgozás befejeződött-e, Ehelyett használja az állapotváltozás eseményeit.|

Lásd: az alábbi [séma-példák](#event-schema-examples) .

## <a name="live-event-types"></a>Élő események típusai

A Media Services az alább leírt **élő** események típusait is kibocsátja. Az **élő** eseményekhez két kategória van: stream szintű események és nyomon követési szintű események. 

### <a name="stream-level-events"></a>Stream szintű események

Adatfolyam-szintű események jönnek létre adatfolyamként vagy kapcsolatonként. Minden eseményhez tartozik egy `StreamId` paraméter, amely a kapcsolat vagy a stream azonosítására szolgál. Minden adatfolyam vagy-kapcsolatok egy vagy több különböző típusú sávot tartalmaz. Előfordulhat például, hogy egy kódoló egyik csatlakoztatása egy hangsávval és négy videóval rendelkezik. A stream-események típusai a következők:

| Eseménytípus | Leírás |
| ---------- | ----------- |
| Microsoft. Media. LiveEventConnectionRejected | A kódoló csatlakozási kísérlete el lett utasítva. |
| Microsoft. Media. LiveEventEncoderConnected | A kódoló élő eseménnyel létesít kapcsolatot. |
| Microsoft. Media. LiveEventEncoderDisconnected | A kódoló bontja a kapcsolatot. |

Lásd: az alábbi [séma-példák](#event-schema-examples) .

### <a name="track-level-events"></a>Követési szintű események

A nyomkövetési szintű események száma nyomon követhető. 

> [!NOTE]
> Az összes követési szintű esemény egy élő kódoló csatlakoztatása után következik be.

A nyomkövetési szintű események típusai a következők:

| Eseménytípus | Leírás |
| ---------- | ----------- |
| Microsoft. Media. LiveEventIncomingDataChunkDropped | A Media Server elveszi az adatrészletet, mert túl késő, vagy átfedésben van az időbélyegzővel (az új adattömb időbélyege kevesebb, mint az előző adatrészlet befejezési időpontja). |
| Microsoft. Media. LiveEventIncomingStreamReceived | A Media Server a streamben vagy a kapcsolaton belül minden egyes nyomkövetési adatrészletet fogad. |
| Microsoft. Media. LiveEventIncomingStreamsOutOfSync | A Media Server észleli a hang-és video-adatfolyamok szinkronizálását. Figyelmeztetésként használható, mert lehetséges, hogy a felhasználói élmény nem érinti. |
| Microsoft. Media. LiveEventIncomingVideoStreamsOutOfSync | A Media Server észleli, hogy a külső kódolótól érkező két videó stream nincs szinkronban. Figyelmeztetésként használható, mert lehetséges, hogy a felhasználói élmény nem érinti. |
| Microsoft. Media. LiveEventIngestHeartbeat | Az élő esemény futása során minden egyes pályán 20 másodpercenként közzétettek. Betöltési állapot összegzését biztosítja.<br/><br/>A kódoló kezdeti csatlakoztatása után a szívverési esemény továbbra is 20 másodpercenként bocsátja ki, hogy a kódoló még csatlakoztatva van-e. |
| Microsoft. Media. LiveEventTrackDiscontinuityDetected | A Media Server a bejövő nyomon követésben észleli a megszakításokat. |

Lásd: az alábbi [séma-példák](#event-schema-examples) .

## <a name="event-schema-examples"></a>Példák az esemény-sémára

### <a name="jobstatechange"></a>JobStateChange

Az alábbi példa a **JobStateChange** esemény sémáját mutatja be: 

```json
[
  {
    "topic": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaservices/<account-name>",
    "subject": "transforms/VideoAnalyzerTransform/jobs/<job-id>",
    "eventType": "Microsoft.Media.JobStateChange",
    "eventTime": "2018-04-20T21:26:13.8978772",
    "id": "b9d38923-9210-4c2b-958f-0054467d4dd7",
    "data": {
      "previousState": "Processing",
      "state": "Finished"
    },
    "dataVersion": "1.0",
    "metadataVersion": "1"
  }
]
```

Az adatobjektum a következő tulajdonságokkal rendelkezik:

| Tulajdonság | Típus | Description |
| -------- | ---- | ----------- |
| previousState | sztring | A feladattípus állapota az esemény előtt. |
| állapot | sztring | Az eseményben szereplő feladatok új állapota. Például "ütemezett: a művelet készen áll a kezdésre" vagy "kész: a művelet befejeződött".|

Ahol a feladat állapota lehet a következő értékek egyike: *várólista*, *ütemezett*, *feldolgozás*, *befejezett*, *hiba*, *megszakított*, *megszakítás*

> [!NOTE]
> Az *üzenetsor* -kezelési szolgáltatás csak a **previousState** tulajdonságban fog megjelenni, az **állapot** tulajdonságban nem.

### <a name="jobscheduled-jobprocessing-jobcanceling"></a>JobScheduled, JobProcessing, JobCanceling

Az egyes nem végleges feladatok állapotának változásakor (például JobScheduled, JobProcessing, JobCanceling) a példa séma a következőhöz hasonlóan néz ki:

```json
[{
  "topic": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaservices/<account-name>",
  "subject": "transforms/VideoAnalyzerTransform/jobs/<job-id>",
  "eventType": "Microsoft.Media.JobProcessing",
  "eventTime": "2018-10-12T16:12:18.0839935",
  "id": "a0a6efc8-f647-4fc2-be73-861fa25ba2db",
  "data": {
    "previousState": "Scheduled",
    "state": "Processing",
    "correlationData": {
      "testKey1": "testValue1",
      "testKey2": "testValue2"
    }
  },
  "dataVersion": "1.0",
  "metadataVersion": "1"
}]
```

### <a name="jobfinished-jobcanceled-joberrored"></a>JobFinished, JobCanceled, JobErrored

Az egyes végső feladatok állapotának változásaihoz (például JobFinished, JobCanceled, JobErrored) a példa sémája a következőhöz hasonlóan néz ki:

```json
[{
  "topic": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaservices/<account-name>",
  "subject": "transforms/VideoAnalyzerTransform/jobs/<job-id>",
  "eventType": "Microsoft.Media.JobFinished",
  "eventTime": "2018-10-12T16:25:56.4115495",
  "id": "9e07e83a-dd6e-466b-a62f-27521b216f2a",
  "data": {
    "outputs": [
      {
        "@odata.type": "#Microsoft.Media.JobOutputAsset",
        "assetName": "output-7640689F",
        "error": null,
        "label": "VideoAnalyzerPreset_0",
        "progress": 100,
        "state": "Finished"
      }
    ],
    "previousState": "Processing",
    "state": "Finished",
    "correlationData": {
      "testKey1": "testValue1",
      "testKey2": "testValue2"
    }
  },
  "dataVersion": "1.0",
  "metadataVersion": "1"
}]
```

Az adatobjektum a következő tulajdonságokkal rendelkezik:

| Tulajdonság | Típus | Description |
| -------- | ---- | ----------- |
| kimenetek | Tömb | A feladatok kimenetének beolvasása.|

### <a name="joboutputstatechange"></a>JobOutputStateChange

Az alábbi példa a **JobOutputStateChange** esemény sémáját mutatja be:

```json
[{
  "topic": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaservices/<account-name>",
  "subject": "transforms/VideoAnalyzerTransform/jobs/<job-id>",
  "eventType": "Microsoft.Media.JobOutputStateChange",
  "eventTime": "2018-10-12T16:25:56.0242854",
  "id": "dde85f46-b459-4775-b5c7-befe8e32cf90",
  "data": {
    "previousState": "Processing",
    "output": {
      "@odata.type": "#Microsoft.Media.JobOutputAsset",
      "assetName": "output-7640689F",
      "error": null,
      "label": "VideoAnalyzerPreset_0",
      "progress": 100,
      "state": "Finished"
    },
    "jobCorrelationData": {
      "testKey1": "testValue1",
      "testKey2": "testValue2"
    }
  },
  "dataVersion": "1.0",
  "metadataVersion": "1"
}]
```

### <a name="joboutputscheduled-joboutputprocessing-joboutputfinished-joboutputcanceling-joboutputcanceled-joboutputerrored"></a>JobOutputScheduled, JobOutputProcessing, JobOutputFinished, JobOutputCanceling, JobOutputCanceled, JobOutputErrored

Az egyes JobOutput-állapotok változásaihoz a példában szereplő séma a következőhöz hasonlóan néz ki:

```json
[{
  "topic": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaservices/<account-name>",
  "subject": "transforms/VideoAnalyzerTransform/jobs/<job-id>",
  "eventType": "Microsoft.Media.JobOutputProcessing",
  "eventTime": "2018-10-12T16:12:18.0061141",
  "id": "f1fd5338-1b6c-4e31-83c9-cd7c88d2aedb",
  "data": {
    "previousState": "Scheduled",
    "output": {
      "@odata.type": "#Microsoft.Media.JobOutputAsset",
      "assetName": "output-7640689F",
      "error": null,
      "label": "VideoAnalyzerPreset_0",
      "progress": 0,
      "state": "Processing"
    },
    "jobCorrelationData": {
      "testKey1": "testValue1",
      "testKey2": "testValue2"
    }
  },
  "dataVersion": "1.0",
  "metadataVersion": "1"
}]
```
### <a name="joboutputprogress"></a>JobOutputProgress

A példában szereplő séma a következőhöz hasonlóan néz ki:

 ```json
[{
  "topic": "/subscriptions/<subscription-id>/resourceGroups/belohGroup/providers/Microsoft.Media/mediaservices/<account-name>",
  "subject": "transforms/VideoAnalyzerTransform/jobs/job-5AB6DE32",
  "eventType": "Microsoft.Media.JobOutputProgress",
  "eventTime": "2018-12-10T18:20:12.1514867",
  "id": "00000000-0000-0000-0000-000000000000",
  "data": {
    "jobCorrelationData": {
      "TestKey1": "TestValue1",
      "testKey2": "testValue2"
    },
    "label": "VideoAnalyzerPreset_0",
    "progress": 86
  },
  "dataVersion": "1.0",
  "metadataVersion": "1"
}]
```

### <a name="liveeventconnectionrejected"></a>LiveEventConnectionRejected

Az alábbi példa a **LiveEventConnectionRejected** esemény sémáját mutatja be: 

```json
[
  {
    "topic": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaServices/<account-name>",
    "subject": "/LiveEvents/MyLiveEvent1",
    "eventType": "Microsoft.Media.LiveEventConnectionRejected",
    "eventTime": "2018-01-16T01:57:26.005121Z",
    "id": "b303db59-d5c1-47eb-927a-3650875fded1",
    "data": { 
      "streamId":"Mystream1",
      "ingestUrl": "http://abc.ingest.isml",
      "encoderIp": "118.238.251.xxx",
      "encoderPort": 52859,
      "resultCode": "MPE_INGEST_CODEC_NOT_SUPPORTED"
    },
    "dataVersion": "1.0",
    "metadataVersion": "1"
  }
]
```

Az adatobjektum a következő tulajdonságokkal rendelkezik:

| Tulajdonság | Típus | Description |
| -------- | ---- | ----------- |
| Streamazonosítója | sztring | Az adatfolyam vagy a kapcsolatok azonosítója. A kódoló vagy az ügyfél feladata, hogy hozzáadja ezt az azonosítót a betöltési URL-címben. |  
| ingestUrl | sztring | Betöltési URL-cím, amelyet az élő esemény biztosít. |  
| encoderIp | sztring | A kódoló IP-címe. |
| encoderPort | sztring | Annak a kódolónak a portja, ahonnan a stream érkezik. |
| resultCode | sztring | A rendszer elutasította a csatlakozás okát. Az eredmény-kódokat a következő táblázat tartalmazza. |

A hibák eredményének kódjait az [élő események](../live-event-error-codes.md)hibakódjában találja.

### <a name="liveeventencoderconnected"></a>LiveEventEncoderConnected

Az alábbi példa a **LiveEventEncoderConnected** esemény sémáját mutatja be: 

```json
[
  { 
    "topic": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaservices/<account-name>",
    "subject": "liveEvent/mle1",
    "eventType": "Microsoft.Media.LiveEventEncoderConnected",
    "eventTime": "2018-08-07T23:08:09.1710643",
    "id": "<id>",
    "data": {
      "ingestUrl": "http://mle1-amsts03mediaacctgndos-ts031.channel.media.azure-test.net:80/ingest.isml",
      "streamId": "15864-stream0",
      "encoderIp": "131.107.147.xxx",
      "encoderPort": "27485"
    },
    "dataVersion": "1.0",
    "metadataVersion": "1"
  }
]
```

Az adatobjektum a következő tulajdonságokkal rendelkezik:

| Tulajdonság | Típus | Description |
| -------- | ---- | ----------- |
| Streamazonosítója | sztring | Az adatfolyam vagy a kapcsolatok azonosítója. A kódoló vagy az ügyfél felelős azért, hogy ezt az azonosítót a betöltési URL-címben adja meg. |
| ingestUrl | sztring | Betöltési URL-cím, amelyet az élő esemény biztosít. |
| encoderIp | sztring | A kódoló IP-címe. |
| encoderPort | sztring | Annak a kódolónak a portja, ahonnan a stream érkezik. |

### <a name="liveeventencoderdisconnected"></a>LiveEventEncoderDisconnected

Az alábbi példa a **LiveEventEncoderDisconnected** esemény sémáját mutatja be: 

```json
[
  { 
    "topic": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaservices/<account-name>",
    "subject": "liveEvent/mle1",
    "eventType": "Microsoft.Media.LiveEventEncoderDisconnected",
    "eventTime": "2018-08-07T23:08:09.1710872",
    "id": "<id>",
    "data": {
      "ingestUrl": "http://mle1-amsts03mediaacctgndos-ts031.channel.media.azure-test.net:80/ingest.isml",
      "streamId": "15864-stream0",
      "encoderIp": "131.107.147.xxx",
      "encoderPort": "27485",
      "resultCode": "S_OK"
    },
    "dataVersion": "1.0",
    "metadataVersion": "1"
  }
]
```

Az adatobjektum a következő tulajdonságokkal rendelkezik:

| Tulajdonság | Típus | Description |
| -------- | ---- | ----------- |
| Streamazonosítója | sztring | Az adatfolyam vagy a kapcsolatok azonosítója. A kódoló vagy az ügyfél feladata, hogy hozzáadja ezt az azonosítót a betöltési URL-címben. |  
| ingestUrl | sztring | Betöltési URL-cím, amelyet az élő esemény biztosít. |  
| encoderIp | sztring | A kódoló IP-címe. |
| encoderPort | sztring | Annak a kódolónak a portja, ahonnan a stream érkezik. |
| resultCode | sztring | A kódoló kibontásának oka. Előfordulhat, hogy a rendszer kecsesen leválasztja vagy hibát észlelt. Az eredmény-kódokat a következő táblázat tartalmazza. |

A hibák eredményének kódjait az [élő események](../live-event-error-codes.md)hibakódjában találja.

A kecses leválasztási eredmények kódjai a következők:

| Eredménykód | Description |
| ----------- | ----------- |
| S_OK | A kódoló leválasztása sikeresen megtörtént. |
| MPE_CLIENT_TERMINATED_SESSION | A kódoló leválasztva (RTMP). |
| MPE_CLIENT_DISCONNECTED | A kódoló leválasztva (FMP4). |
| MPI_REST_API_CHANNEL_RESET | A csatorna alaphelyzetbe állítása parancs érkezett. |
| MPI_REST_API_CHANNEL_STOP | A Channel stop parancs érkezett. |
| MPI_REST_API_CHANNEL_STOP | A csatorna karbantartás alatt áll. |
| MPI_STREAM_HIT_EOF | Az EOF adatfolyamot a kódoló küldi el. |

### <a name="liveeventincomingdatachunkdropped"></a>LiveEventIncomingDataChunkDropped

Az alábbi példa a **LiveEventIncomingDataChunkDropped** esemény sémáját mutatja be: 

```json
[
  {
    "topic": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaServices/<account-name>",
    "subject": "/LiveEvents/MyLiveEvent1",
    "eventType": "Microsoft.Media.LiveEventIncomingDataChunkDropped",
    "eventTime": "2018-01-16T01:57:26.005121Z",
    "id": "03da9c10-fde7-48e1-80d8-49936f2c3e7d",
    "data": { 
      "trackType": "Video",
      "trackName": "Video",
      "bitrate": 300000,
      "timestamp": 36656620000,
      "timescale": 10000000,
      "resultCode": "FragmentDrop_OverlapTimestamp"
    },
    "dataVersion": "1.0",
    "metadataVersion": "1"
  }
]
```

Az adatobjektum a következő tulajdonságokkal rendelkezik:

| Tulajdonság | Típus | Description |
| -------- | ---- | ----------- |
| trackType | sztring | A pálya típusa (hang/videó). |
| trackName | sztring | A pálya neve. |
| sávszélességű | egész szám | A pálya bitrátája. |
| időbélyeg | sztring | Az adattömb eldobott időbélyegzője. |
| időskála | sztring | Az időbélyeg időkerete. |
| resultCode | sztring | Az adattömb eldobásának oka. **FragmentDrop_OverlapTimestamp** vagy **FragmentDrop_NonIncreasingTimestamp**. |

### <a name="liveeventincomingstreamreceived"></a>LiveEventIncomingStreamReceived

Az alábbi példa a **LiveEventIncomingStreamReceived** esemény sémáját mutatja be: 

```json
[
  {
    "topic": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaservices/<account-name>",
    "subject": "liveEvent/mle1",
    "eventType": "Microsoft.Media.LiveEventIncomingStreamReceived",
    "eventTime": "2018-08-07T23:08:10.5069288Z",
    "id": "7f939a08-320c-47e7-8250-43dcfc04ab4d",
    "data": {
      "ingestUrl": "http://mle1-amsts03mediaacctgndos-ts031.channel.media.azure-test.net:80/ingest.isml/Streams(15864-stream0)15864-stream0",
      "trackType": "video",
      "trackName": "video",
      "bitrate": 2962000,
      "encoderIp": "131.107.147.xxx",
      "encoderPort": "27485",
      "timestamp": "15336831655032322",
      "duration": "20000000",
      "timescale": "10000000"
    },
    "dataVersion": "1.0",
    "metadataVersion": "1"
  }
]
```

Az adatobjektum a következő tulajdonságokkal rendelkezik:

| Tulajdonság | Típus | Description |
| -------- | ---- | ----------- |
| trackType | sztring | A pálya típusa (hang/videó). |
| trackName | sztring | A nyomkövetés neve (amelyet a kódoló biztosít, vagy ha RTMP esetén a kiszolgáló *TrackType_Bitrate* formátumban hozza létre). |
| sávszélességű | egész szám | A pálya bitrátája. |
| ingestUrl | sztring | Betöltési URL-cím, amelyet az élő esemény biztosít. |
| encoderIp | sztring  | A kódoló IP-címe. |
| encoderPort | sztring | Annak a kódolónak a portja, ahonnan a stream érkezik. |
| időbélyeg | sztring | A fogadott adathalmaz első időbélyege. |
| időskála | sztring | Az időbélyeg megjelenítésének időkerete. |

### <a name="liveeventincomingstreamsoutofsync"></a>LiveEventIncomingStreamsOutOfSync

Az alábbi példa a **LiveEventIncomingStreamsOutOfSync** esemény sémáját mutatja be: 

```json
[
  {
    "topic": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaservices/<account-name>",
    "subject": "liveEvent/mle1",
    "eventType": "Microsoft.Media.LiveEventIncomingStreamsOutOfSync",
    "eventTime": "2018-08-10T02:26:20.6269183Z",
    "id": "b9d38923-9210-4c2b-958f-0054467d4dd7",
    "data": {
      "minLastTimestamp": "319996",
      "typeOfStreamWithMinLastTimestamp": "Audio",
      "maxLastTimestamp": "366000",
      "typeOfStreamWithMaxLastTimestamp": "Video",
      "timescaleOfMinLastTimestamp": "10000000", 
      "timescaleOfMaxLastTimestamp": "10000000"       
    },
    "dataVersion": "1.0",
    "metadataVersion": "1"
  }
]
```

Az adatobjektum a következő tulajdonságokkal rendelkezik:

| Tulajdonság | Típus | Description |
| -------- | ---- | ----------- |
| minLastTimestamp | sztring | Az összes műsorszám (hang vagy videó) utolsó időbélyegének minimális száma. |
| typeOfTrackWithMinLastTimestamp | sztring | A pálya (hang vagy videó) minimális utolsó időbélyeg-típusa. |
| maxLastTimestamp | sztring | Az összes műsorszám (hang vagy videó) közötti időbélyegek maximális száma. |
| typeOfTrackWithMaxLastTimestamp | sztring | A nyomvonal (hang vagy videó) típusa, amely az utolsó időbélyeg maximális számát adja meg. |
| timescaleOfMinLastTimestamp| sztring | Lekérdezi azt az időskálát, amelyben a "MinLastTimestamp" kifejezés jelenik meg.|
| timescaleOfMaxLastTimestamp| sztring | Lekérdezi azt az időskálát, amelyben a "MaxLastTimestamp" kifejezés jelenik meg.|

### <a name="liveeventincomingvideostreamsoutofsync"></a>LiveEventIncomingVideoStreamsOutOfSync

Az alábbi példa a **LiveEventIncomingVideoStreamsOutOfSync** esemény sémáját mutatja be: 

```json
[
  {
    "topic": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaServices/<account-name>",
    "subject": "/LiveEvents/LiveEvent1",
    "eventType": "Microsoft.Media.LiveEventIncomingVideoStreamsOutOfSync",
    "eventTime": "2018-01-16T01:57:26.005121Z",
    "id": "6dd4d862-d442-40a0-b9f3-fc14bcf6d750",
    "data": {
      "firstTimestamp": "2162058216",
      "firstDuration": "2000",
      "secondTimestamp": "2162057216",
      "secondDuration": "2000",
      "timescale": "10000000"      
    },
    "dataVersion": "1.0",
    "metadataVersion": "1"
  }
]
```

Az adatobjektum a következő tulajdonságokkal rendelkezik:

| Tulajdonság | Típus | Description |
| -------- | ---- | ----------- |
| firstTimestamp | sztring | A videó típusú számok/minőségi szintek egyikéhez tartozó időbélyeg érkezett. |
| firstDuration | sztring | Az adathalmaz időtartama az első időbélyeggel. |
| secondTimestamp | sztring  | A videó típusának más követési/minőségi szintjéhez tartozó időbélyeg érkezett. |
| secondDuration | sztring | Az adattömb időtartama második időbélyegzővel. |
| időskála | sztring | Az időbélyegek és az időtartam időkerete.|

### <a name="liveeventingestheartbeat"></a>LiveEventIngestHeartbeat

Az alábbi példa a **LiveEventIngestHeartbeat** esemény sémáját mutatja be: 

```json
[
  {
    "topic": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaservices/<account-name>",
    "subject": "liveEvent/mle1",
    "eventType": "Microsoft.Media.LiveEventIngestHeartbeat",
    "eventTime": "2018-08-07T23:17:57.4610506",
    "id": "7f450938-491f-41e1-b06f-c6cd3965d786",
    "data": {
      "trackType": "audio",
      "trackName": "audio",
      "bitrate": 160000,
      "incomingBitrate": 155903,
      "lastTimestamp": "15336837535253637",
      "timescale": "10000000",
      "overlapCount": 0,
      "discontinuityCount": 0,
      "nonincreasingCount": 0,
      "unexpectedBitrate": false,
      "state": "Running",
      "healthy": true
    },
    "dataVersion": "1.0",
    "metadataVersion": "1"
  }
]
```

Az adatobjektum a következő tulajdonságokkal rendelkezik:

| Tulajdonság | Típus | Description |
| -------- | ---- | ----------- |
| trackType | sztring | A pálya típusa (hang/videó). |
| trackName | sztring | A nyomkövetés neve (amelyet a kódoló biztosít, vagy ha RTMP esetén a kiszolgáló *TrackType_Bitrate* formátumban hozza létre). |
| sávszélességű | egész szám | A pálya bitrátája. |
| incomingBitrate | egész szám | A kódolóból érkező adattömbök alapján számított bitráta. |
| lastTimestamp | sztring | Utolsó 20 másodpercben egy nyomkövetéshez tartozó legutóbbi időbélyeg érkezett. |
| időskála | sztring | Az időbélyegek kiértékelési időkerete. |
| overlapCount | egész szám | Az átfedő időbélyegek száma az elmúlt 20 másodpercben. |
| discontinuityCount | egész szám | Az elmúlt 20 másodpercben megfigyelt megszakítások száma. |
| nonIncreasingCount | egész szám | A múltban az időbélyegzővel rendelkező adattömbök száma az elmúlt 20 másodpercben érkezett. |
| unexpectedBitrate | logikai | Ha a várt érték és a tényleges bitsebességek az elmúlt 20 másodpercben több mint megengedett korláttal térnek el egymástól. Igaz, ha és csak akkor, ha incomingBitrate >= 2 * bitráta vagy incomingBitrate <= bitráta/2 vagy IncomingBitrate = 0. |
| állapot | sztring | Az élő esemény állapota. |
| kifogástalan | logikai | Azt jelzi, hogy a betöltés kifogástalan állapotú-e a számok és a jelzők alapján. Az kifogástalan állapot akkor igaz, ha overlapCount = 0 && discontinuityCount = 0 && nonIncreasingCount = 0 && unexpectedBitrate = false. |

### <a name="liveeventtrackdiscontinuitydetected"></a>LiveEventTrackDiscontinuityDetected

Az alábbi példa a **LiveEventTrackDiscontinuityDetected** esemény sémáját mutatja be: 

```json
[
  {
    "topic": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaservices/<account-name>",
    "subject": "liveEvent/mle1",
    "eventType": "Microsoft.Media.LiveEventTrackDiscontinuityDetected",
    "eventTime": "2018-08-07T23:18:06.1270405Z",
    "id": "5f4c510d-5be7-4bef-baf0-64b828be9c9b",
    "data": {
      "trackName": "video",
      "previousTimestamp": "15336837615032322",
      "trackType": "video",
      "bitrate": 2962000,
      "newTimestamp": "15336837619774273",
      "discontinuityGap": "575284",
      "timescale": "10000000"
    },
    "dataVersion": "1.0",
    "metadataVersion": "1"
  }
]
```

Az adatobjektum a következő tulajdonságokkal rendelkezik:

| Tulajdonság | Típus | Description |
| -------- | ---- | ----------- |
| trackType | sztring | A pálya típusa (hang/videó). |
| trackName | sztring | A nyomkövetés neve (amelyet a kódoló biztosít, vagy ha RTMP esetén a kiszolgáló *TrackType_Bitrate* formátumban hozza létre). |
| sávszélességű | egész szám | A pálya bitrátája. |
| previousTimestamp | sztring | Az előző töredék időbélyegzője. |
| newTimestamp | sztring | Az aktuális töredék időbélyegzője. |
| discontinuityGap | sztring | A két időbélyeg közötti különbség. |
| időskála | sztring | Az az időkeret, amelyben az időbélyegző és a folytonossági hézag is szerepel. |

### <a name="common-event-properties"></a>Gyakori esemény tulajdonságai

Egy esemény a következő legfelső szintű adattal rendelkezik:

| Tulajdonság | Típus | Description |
| -------- | ---- | ----------- |
| témakör | sztring | A EventGrid témakör. Ez a tulajdonság a Media Services fiók erőforrás-azonosítójával rendelkezik. |
| tárgy | sztring | A Media Services fiók Media Servicesi csatornájának erőforrás-elérési útja. A témakör és a tárgy összefűzésével megadhatja a feladatokhoz tartozó erőforrás-azonosítót. |
| eventType | sztring | Az eseményforráshoz felvett eseménytípusok egyike. Például: "Microsoft. Media. JobStateChange". |
| eventTime | sztring | Az esemény a szolgáltató UTC-ideje alapján történő létrehozásakor. |
| id | sztring | Az esemény egyedi azonosítója. |
| adatok | object | Media Services az eseményekre vonatkozó adatgyűjtést. |
| dataVersion | sztring | Az adatobjektum sémaverziója. A sémaverziót a közzétevő határozza meg. |
| metadataVersion | sztring | Az esemény metaadatok sémaverziója. A legfelső szintű tulajdonságokra az Event Grid határozza meg a sémát. Az értéket az Event Grid adja meg. |

## <a name="next-steps"></a>Következő lépések

[Regisztrálja a feladatok állapotának változási eseményeit](../job-state-events-cli-how-to.md)

## <a name="see-also"></a>Lásd még

- [A Media Service-eseményeket tartalmazó EventGrid .NET SDK](https://www.nuget.org/packages/Microsoft.Azure.EventGrid/)
- [Media Services események definíciói](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/eventgrid/data-plane/Microsoft.Media/stable/2018-01-01/MediaServices.json)
- [Élő események hibakódja](../live-event-error-codes.md)
