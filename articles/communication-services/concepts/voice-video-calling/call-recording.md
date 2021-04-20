---
title: Azure Communication Services Call Recording áttekintése
titleSuffix: An Azure Communication Services concept document
description: Áttekintést nyújt a Call Recording funkcióról és az API-król.
author: joseys
manager: anvalent
services: azure-communication-services
ms.author: joseys
ms.date: 04/13/2021
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: df9638588b4d677a7ceb80bafbe7157bb5c2df42
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/19/2021
ms.locfileid: "107730579"
---
# <a name="calling-recording-overview"></a>A Calling Recording áttekintése

[!INCLUDE [Private Preview Notice](../../includes/private-preview-include.md)]

> [!NOTE]
> Számos ország és állam rendelkezik olyan jogszabályokkal és szabályozásokkal, amelyek a PSTN-, hang- és videohívások rögzítésére vonatkoznak, amelyekhez gyakran szükséges, hogy a felhasználók hozzájárulnak a kommunikációjuk rögzítéséhez. Az Ön felelőssége, hogy a hívásrögzítési képességeket a jogszabályoknak megfelelően használja. A rögzített kommunikációra vonatkozó beleegyezést kell szereznie a felektől olyan módon, amely megfelel az egyes résztvevőkre vonatkozó törvényeknek.

> [!NOTE]
> A személyes adatok karbantartásával kapcsolatos szabályozások megkövetelik a felhasználói adatok exportálásának képességét. A követelmények támogatása érdekében a metaadatfájlok rögzítéséhez a tömb minden egyes hívási résztvevőjének résztvevőjének résztvevőazonosítója `participants` szerepel. A tömbben szereplő MRI-eket kereszthivatkozással azonosíthatja a hívás résztvevőinek `participants` azonosításához a belső felhasználói identitásokkal. Referenciaként alább egy példát mutatunk be a rögzítési metaadatfájlra.

A Call Recording API-kat biztosít a felvétel elindítani, leállítani, szüneteltetni és folytatni. Ezek az API-k a kiszolgálóoldali üzleti logikából vagy a felhasználói műveletek által kiváltott eseményeken keresztül érhetők el. A rögzített médiakimenet formátuma megegyezik a Teams által az adathordozó `MP4 Audio+Video` rögzítéséhez használt formátummal. Az adathordozóval és metaadatokkal kapcsolatos értesítéseket a rendszer a Event Grid. A felvételeket 48 órán át tárolja a rendszer a beépített ideiglenes tárolón a lekérés és a választott hosszú távú tárolási megoldásba való mozgatás érdekében. 

## <a name="run-time-control-apis"></a>Futásidő-vezérlési API-k
A futásidő-vezérlési API-k a rögzítés belső üzleti logikai eseményindítókon keresztüli kezelésére használhatók. Ilyen lehet például egy csoporthívást és a beszélgetést rögzítő alkalmazás, vagy egy felhasználó által aktivált művelet, amely arra utasítja a kiszolgálóalkalmazást, hogy kezdje meg a rögzítést. Mindkét esetben `<conversation-id>` a kötelező egy adott értekezlet vagy hívás rögzítéséhez. 

#### <a name="getting-conversation-id-from-a-server-initiated-call"></a>Beszélgetésazonosító lehívása egy kiszolgáló által kezdeményezett hívásból

A `ConversationId` az eseményen keresztül lesz `Microsoft.Communication.CallLegStateChanged` visszaadva. Ez az eseményértesítés a hívás létrejötte után lesz kiadva. Ez a mezőben `data.ConversationId` található. Ez az érték közvetlenül használható paraméterként `{conversationId}` a futásidő-vezérlési API-kban:
```
      {
        "id": null,
        "topic": null,
        "subject": "callLeg/<callLegId>/callState",
        "data": {
---->       "ConversationId": "<conversation-id>",    <----
            "CallLegId": "<callLegId>",
            "CallState": "Established"
        },
        "eventType": "Microsoft.Communication.CallLegStateChanged",
        "eventTime": "2021-04-14T16:32:34.1115003Z",
        "metadataVersion": null,
        "dataVersion": null
    }
```
                                                            
#### <a name="getting-the-conversation-id-from-a-user-triggered-event-on-the-client"></a>A beszélgetés azonosítójának lekérte egy felhasználó által aktivált esemény az ügyfélen

A JavaScript-kódtárból, a meghívásának létrehozása után a lekért meghívása után a Base64Url kódolja a kódolót, hogy az a `@azure/communication-calling` `let result = call.info.getConversationUrl()` `conversationUrl` **futásidő-vezérlési `conversationUrl` `{conversationId}` API-kban való használatra lekérte.** A kódolást az ügyfélen is meg lehet tenni, mielőtt elküldi az eseményt a kiszolgálónak vagy a kiszolgálóoldalnak.

Vegye figyelembe, hogy a `conversationUrl` *típusnak* Base64Url kódolásúnak kell lennie, hogy ne legyen összekeverve a Base64 kódolással (azaz btoa).                                                            

### <a name="start-recording"></a>Rögzítés indítása

#### <a name="request"></a>Kérés

**HTTP**
<!-- {
  "blockType": "request",
  "name": "start-recording"
}-->
```
POST /conversations/{conversationId}/Recordings
Content-Type: application/json

{
  "operationContext": "string", // developer provided string for correlation context on each operation
  "recordingStateCallbackUri": "string"
}
```
**C# SDK**
<!-- {
  "blockType": "request",
  "name": "start-recording"
}-->
```C#
string connectionString = "YOUR_CONNECTION_STRING";
ConversationClient conversationClient = new ConversationClient(connectionString);

/// start call recording
StartRecordingResponse startRecordingResponse = await conversationClient.StartRecordingAsync(
    conversationId: "<conversation-id>"
    operationContext: "<operation-context>", /// developer provided string for correlation context on each operation
    recordingStateCallbackUri: "<recording-state-callback-uri>").ConfigureAwait(false);

string recordingId = startRecordingResponse.RecordingId;
```

#### <a name="response"></a>Reagálás

**HTTP**
<!-- {
  "blockType": "response",
  "truncated": true,
} -->

```http
HTTP/1.1 200 Success
Content-Type: application/json

{
  "recordingId": "string"
}
```
```
HTTP/1.1 400 Bad request
Content-Type: application/json

{
  "code": "string",
  "message": "string",
  "target": "string",
  "details": [
    null
  ]
}
```
```
HTTP/1.1 404 Not found
Content-Type: application/json

{
  "code": "string",
  "message": "string",
  "target": "string",
  "details": [
    null
  ]
}
```
```
HTTP/1.1 500    Internal server error
Content-Type: application/json

{
  "code": "string",
  "message": "string",
  "target": "string",
  "details": [
    null
  ]
}
```

### <a name="get-call-recording-state"></a>Hívásrögzítési állapot lehívása

#### <a name="request"></a>Kérés

**HTTP**
<!-- {
  "blockType": "request",
  "name": "get-recording-state"
}-->
```http
GET /conversations/{conversationId}/recordings/{recordingId}
Content-Type: application/json

{
}
```
**C# SDK**
<!-- {
  "blockType": "request",
  "name": "start-recording"
}-->
```C#
string connectionString = "YOUR_CONNECTION_STRING";
ConversationClient conversationClient = new ConversationClient(connectionString);

/// get recording state
GetCallRecordingStateResponse recordingState = await conversationClient.GetRecordingStateAsync(
    conversationId: "<conversation-id>",
    recordingId: <recordingId>).ConfigureAwait(false);
```
#### <a name="response"></a>Reagálás

**HTTP**
<!-- {
  "blockType": "response",
  "truncated": true,
} -->

```http
HTTP/1.1 200 Success
Content-Type: application/json

{
  "recordingState": "active"
}
```
```
HTTP/1.1 400 Bad request
Content-Type: application/json

{
  "code": "string",
  "message": "string",
  "target": "string",
  "details": [
    null
  ]
}
```
```
HTTP/1.1 500    Internal server error
Content-Type: application/json

{
  "code": "string",
  "message": "string",
  "target": "string",
  "details": [
    null
  ]
}
```

### <a name="stop-recording"></a>Rögzítés leállítása
#### <a name="request"></a>Kérés
**HTTP**
<!-- {
  "blockType": "request",
  "name": "stop-recording"
}-->
```
DELETE /conversations/{conversationId}/recordings/{recordingId}
Content-Type: application/json

{
  "operationContext": "string" // developer provided string for correlation context on each operation
}
```
**C# SDK**
<!-- {
  "blockType": "request",
  "name": "start-recording"
}-->
```C#
string connectionString = "YOUR_CONNECTION_STRING";
ConversationClient conversationClient = new ConversationClient(connectionString);

/// stop recording
StopRecordingResponse response = conversationClient.StopRecordingAsync(
    conversationId: "<conversation-id>",
    recordingId: <recordingId>,
    operationContext: "<operation-context>").ConfigureAwait(false);
```
#### <a name="response"></a>Reagálás
**HTTP**
<!-- {
  "blockType": "response",
  "truncated": true,
} -->

```http
HTTP/1.1 200 Success
Content-Type: application/json

{
}
```
```
HTTP/1.1 400 Bad request
Content-Type: application/json

{
  "code": "string",
  "message": "string",
  "target": "string",
  "details": [
    null
  ]
}
```
```
HTTP/1.1 500    Internal server error
Content-Type: application/json

{
  "code": "string",
  "message": "string",
  "target": "string",
  "details": [
    null
  ]
}
```

### <a name="pause-recording"></a>Rögzítés szüneteltetése
A hívásrögzítés szüneteltetését és folytatását lehetővé teszi, hogy kihagyja egy hívás vagy értekezlet egy részének rögzítését, és folytatja a rögzítést egyetlen fájlba. 
#### <a name="request"></a>Kérés
**HTTP**
<!-- {
  "blockType": "request",
  "name": "pause-recording"
}-->
```
POST /conversations/{conversationId}/recordings/{recordingId}/Pause
Content-Type: application/json

{
  "operationContext": "string" // developer provided string for correlation context on each operation
}
```
**C# SDK**
<!-- {
  "blockType": "request",
  "name": "start-recording"
}-->
```C#
string connectionString = "YOUR_CONNECTION_STRING";
ConversationClient conversationClient = new ConversationClient(connectionString);

/// pause recording
PauseRecordingResponse response = conversationClient.PauseRecordingAsync(
    conversationId: "<conversation-id>",
    recordingId: <recordingId>,
    operationContext: "<operation-context>").ConfigureAwait(false);
```
#### <a name="response"></a>Reagálás
**HTTP**
<!-- {
  "blockType": "response",
  "truncated": true,
} -->

```http
HTTP/1.1 200 Success
Content-Type: application/json

{
}
```
```
HTTP/1.1 400 Bad request
Content-Type: application/json

{
  "code": "string",
  "message": "string",
  "target": "string",
  "details": [
    null
  ]
}
```
```
HTTP/1.1 500    Internal server error
Content-Type: application/json

{
  "code": "string",
  "message": "string",
  "target": "string",
  "details": [
    null
  ]
}
```

### <a name="resume-recording"></a>Rögzítés folytatása
#### <a name="request"></a>Kérés
**HTTP**
<!-- {
  "blockType": "request",
  "name": "resume-recording"
}-->
```
POST /conversations/{conversationId}/recordings/{recordingId}/Resume
Content-Type: application/json

{
  "operationContext": "string" // developer provided string for correlation context on each operation
}
```
**C# SDK**
<!-- {
  "blockType": "request",
  "name": "start-recording"
}-->
```C#
string connectionString = "YOUR_CONNECTION_STRING";
ConversationClient conversationClient = new ConversationClient(connectionString);

/// resume recording
ResumeRecordingResponse response = conversationClient.ResumeRecordingAsync(
    conversationId: "<conversation-id>",
    recordingId: <recordingId>,
    operationContext: "<operation-context>").ConfigureAwait(false);
```
#### <a name="response"></a>Reagálás
**HTTP**
<!-- {
  "blockType": "response",
  "truncated": true,
} -->

```http
HTTP/1.1 200 Success
Content-Type: application/json

{
}
```
```
HTTP/1.1 400 Bad request
Content-Type: application/json

{
  "code": "string",
  "message": "string",
  "target": "string",
  "details": [
    null
  ]
}
```
```
HTTP/1.1 500    Internal server error
Content-Type: application/json

{
  "code": "string",
  "message": "string",
  "target": "string",
  "details": [
    null
  ]
}
```

## <a name="media-output-types"></a>Médiakimenet-típusok
A hívásrögzítés jelenleg a vegyes hang- és videó MP4 kimeneti formátumot támogatja. A kimeneti média megegyezik a Microsoft Teams-felvételen keresztül készült értekezletfelvételekkel.

| Csatorna típusa | Tartalomformátum | Videó | Audió |
| :----------- | :------------- | :---- | :--------------------------- |
| audioVideo | mp4 | 1920x1080 8 A csempe alapértelmezett elrendezésének minden résztvevőjét bemutató videó | 16 kHz-es mp4a vegyes hang az összes résztvevőtől |

## <a name="event-grid-notifications"></a>Event Grid értesítések
A Event Grid akkor jelenik meg, amikor egy felvétel lekérésre kész, általában 1–2 perccel a rögzítési folyamat befejezése után (pl. az értekezlet befejezése, a felvétel `Microsoft.Communication.RecordingFileStatusUpdated` leállt). Az eseményértesítések rögzítése tartalmaz egy dokumentumazonosítót, amely a rögzített média és a rögzítési metaadatfájl lekérésére is használható:

- <Azure_Communication_Service_Endpoint>/recording/download/{documentId}
- <Azure_Communication_Service_Endpoint>/recording/download/{documentId}/metadata

Az Event Grid-értesítések kezeléséhez, valamint a rögzítési és metaadatfájlok letöltéséhez szükséges mintakód itt [található.](../../quickstarts/voice-video-calling/download-recording-file-sample.md) 

### <a name="notification-schema"></a>Értesítési séma
```
{
    "id": string, // Unique guid for event
    "topic": string, // Azure Communication Services resource id
    "subject": string, // /recording/call/{call-id}
    "data": {
        "recordingStorageInfo": {
            "recordingChunks": [
                {
                    "documentId": string, // Document id for retrieving from storage
                    "index": int, // Index providing ordering for this chunk in the entire recording
                    "endReason": string, // Reason for chunk ending: "SessionEnded", "ChunkMaximumSizeExceeded”, etc.
                }
            ]
        },
        "recordingStartTime": string, // ISO 8601 date time for the start of the recording
        "recordingDurationMs": int, // Duration of recording in milliseconds
        "sessionEndReason": string // Reason for call ending: "CallEnded", "InitiatorLeft", etc.
    },
    "eventType": string, // "Microsoft.Communication.RecordingFileStatusUpdated"
    "dataVersion": string, // "1.0"
    "metadataVersion": string, // "1"
    "eventTime": string // ISO 8601 date time for when the event was created
}
```
## <a name="file-download"></a>Fájl letöltése

> Azure Communication Services rövid távú médiatárolást biztosít a felvételekhez. **Exportálja a 48 órán belül megőrizni kívánt rögzített tartalmat.** 48 óra után a felvételek már nem lesznek elérhetők.

### <a name="download-recording"></a>Felvétel letöltése
#### <a name="request"></a>Kérés
**HTTP**
<!-- {
  "blockType": "request",
  "name": "download-recording"
}-->
```http
GET /recording/download/{documentId}
Content-Type: application/json

{
}
```
#### <a name="response"></a>Reagálás
**HTTP**
<!-- {
  "blockType": "response",
  "truncated": true,
} -->

```
HTTP/1.1 200 Success
Content-Type: video/mp4

{
string // Recording file bytes
}
```
```
HTTP/1.1 400 Bad request
Content-Type: application/json

{
  "code": "string",
  "message": "string",
  "target": "string",
  "details": [
    null
  ]
}
```
```
HTTP/1.1 500    Internal server error
Content-Type: application/json

{
  "code": "string",
  "message": "string",
  "target": "string",
  "details": [
    null
  ]
}
```
### <a name="download-recording-metadata"></a>Rögzítési metaadatok letöltése
#### <a name="request"></a>Kérés
**HTTP**
<!-- {
  "blockType": "request",
  "name": "download-recording-metadata"
}-->
```http
GET /recording/download/{documentId}/metadata
Content-Type: application/json

{
}
```
#### <a name="response"></a>Reagálás
**HTTP**
<!-- {
  "blockType": "response",
  "truncated": true,
} -->

```http
HTTP/1.1 200 Success
Content-Type: application/json

{
  "resourceId": "string",
  "callId": "string",
  "chunkDocumentId": "string",
  "chunkIndex": 0,
  "chunkStartTime": "string",
  "chunkDuration": 0,
  "pauseResumeIntervals": [
    {
      "startTime": "string",
      "duration": 0
    }
  ],
  "recordingInfo": {
    "contentType": "string",
    "channelType": "string",
    "format": "string",
    "audioConfiguration": {
      "sampleRate": "string",
      "bitRate": 0,
      "channels": 0
    },
    "videoConfiguration": {
      "longerSideLength": 0,
      "shorterSideLength": 0,
      "framerate": 0,
      "bitRate": 0
    }
  },
  "participants": [
    {
      "participantId": "string"
    }
  ]
}
```
```
HTTP/1.1 400 Bad request
Content-Type: application/json

{
  "code": "string",
  "message": "string",
  "target": "string",
  "details": [
    null
  ]
}
```
```
HTTP/1.1 500    Internal server error
Content-Type: application/json

{
  "code": "string",
  "message": "string",
  "target": "string",
  "details": [
    null
  ]
}
```
