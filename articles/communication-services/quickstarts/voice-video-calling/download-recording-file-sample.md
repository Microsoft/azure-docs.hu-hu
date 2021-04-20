---
title: Hívások rekordja és letöltése a Event Grid – Azure Communication Services rövid útmutató
titleSuffix: An Azure Communication Services quickstart
description: Ebből a rövid útmutatóból megtudhatja, hogyan rögzíthet és tölthet le hívásokat a Event Grid.
author: joseys
manager: anvalent
services: azure-communication-services
ms.author: joseys
ms.date: 04/14/2021
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: 493a35a627f458fe649931d9fabc175b0affc3a6
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/19/2021
ms.locfileid: "107730568"
---
# <a name="record-and-download-calls-with-event-grid"></a>Hívások rögzítésére és letöltésére Event Grid

[!INCLUDE [Private Preview Notice](../../includes/private-preview-include.md)]

A Azure Communication Services használatának első Communication Services a hívásokat a Azure Event Grid.

## <a name="prerequisites"></a>Előfeltételek
- Aktív előfizetéssel rendelkezik egy Azure-fiók. [Hozzon létre egy ingyenes fiókot.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- Egy aktív Communication Services erőforrás. [Hozzon létre egy Communication Services erőforrást.](../create-communication-resource.md?pivots=platform-azp&tabs=windows)
- A [`Microsoft.Azure.EventGrid`](https://www.nuget.org/packages/Microsoft.Azure.EventGrid/) NuGet-csomag.

## <a name="create-a-webhook-and-subscribe-to-the-recording-events"></a>Webhook létrehozása és feliratkozás a rögzítési eseményekre
*Webhookokat és eseményeket* fogunk *használni* a hívásrögzítés és a médiafájlok letöltésének elősegítésére. 

Először létrehozunk egy webhookot. A Communication Services erőforrás a Event Grid értesíti ezt a webhookot, amikor az esemény aktiválódik, majd amikor a rögzített adathordozó készen áll a `recording` letöltésre.

Saját egyéni webhookot is írhat az eseményértesítések fogadására. Fontos, hogy ez a webhook válaszoljon a bejövő üzenetekre az érvényesítési kóddal, hogy sikeresen előfizet a webhookot az eseményszolgáltatásra.

```
[HttpPost]
public async Task<ActionResult> PostAsync([FromBody] object request)
  {
   //Deserializing the request 
    var eventGridEvent = JsonConvert.DeserializeObject<EventGridEvent[]>(request.ToString())
        .FirstOrDefault();
    var data = eventGridEvent.Data as JObject;

    // Validate whether EventType is of "Microsoft.EventGrid.SubscriptionValidationEvent"
    if (string.Equals(eventGridEvent.EventType, EventTypes.EventGridSubscriptionValidationEvent, StringComparison.OrdinalIgnoreCase))
   {
        var eventData = data.ToObject<SubscriptionValidationEventData>();
        var responseData = new SubscriptionValidationResponseData
        {
            ValidationResponse = eventData.ValidationCode
        };
        if (responseData.ValidationResponse != null)
        {
            return Ok(responseData);
        }
    }

    // Implement your logic here.
    ...
    ...
  }
```


A fenti kód a `Microsoft.Azure.EventGrid` NuGet-csomagtól függ. A végpontérvényesítésről Event Grid a végpontérvényesítési [dokumentációban talál további információt.](https://docs.microsoft.com/azure/event-grid/receive-events#endpoint-validation)

Ezt követően előfizetjük ezt a webhookot az `recording` eseményre:

1. Válassza ki `Events` a panelt a Azure Communication Services erőforrásból.
2. Válassza `Event Subscription` a lehetőséget az alább látható módon.
![Képernyőkép az Event Grid felhasználói felületről](./media/call-recording/image1-event-grid.png)
3. Konfigurálja az esemény-előfizetést, és válassza `Call Recording File Status Update` a `Event Type` lehetőséget. Válassza `Webhook` a `Endpoint type` lehetőséget.
![Esemény-előfizetés létrehozása](./media/call-recording/image2-create-subscription.png)
4. Adja meg a webhook URL-címét a `Subscriber Endpoint` következőben: .
![Feliratkozás eseményre](./media/call-recording/image3-subscribe-to-event.png)

A webhook mostantól értesítést kap, ha Communication Services erőforrást egy hívás rögzítésére használja.

## <a name="notification-schema"></a>Értesítési séma
Ha a felvétel letölthető, a Communication Services erőforrása az alábbi eseménysémával küld értesítést. A felvétel dokumentum-adatokat az egyes mezőkből `documentId` lehet lekérni. `recordingChunk`

```
{
    "id": string, // Unique guid for event
    "topic": string, // Azure Communication Services resource id
    "subject": string, // /recording/call/{call-id}
    "data": {
        "recordingStorageInfo": {
            "recordingChunks": [
                {
                    "documentId": string, // Document id for retrieving from AMS storage
                    "index": int, // Index providing ordering for this chunk in the entire recording
                    "endReason": string, // Reason for chunk ending: "SessionEnded", "ChunkMaximumSizeExceeded”, etc.
                }
            ]
        },
        "recordingStartTime": string, // ISO 8601 date time for the start of the recording
        "recordingDurationMs": int, // Duration of recording in milliseconds
        "sessionEndReason": string // Reason for call ending: "CallEnded", "InitiatorLeft”, etc.
    },
    "eventType": string, // "Microsoft.Communication.RecordingFileStatusUpdated"
    "dataVersion": string, // "1.0"
    "metadataVersion": string, // "1"
    "eventTime": string // ISO 8601 date time for when the event was created
}

```

## <a name="download-the-recorded-media-files"></a>A rögzített médiafájlok letöltése

Miután lekérte a letölteni kívánt fájl dokumentumazonosítóját, az alábbi Azure Communication Services API-kat fogjuk hívni a rögzített média és metaadatok HMAC-hitelesítéssel való letöltéséhez.

A rögzítőfájl maximális mérete 1,5 GB. Ha túllépi ezt a fájlméretet, a rögzítő automatikusan több fájlra osztja fel a rögzített adathordozót.

Az ügyfélnek egyetlen kéréssel le kell tudnia tölteni az összes médiafájlt. Probléma esetén az ügyfél újrapróbálkhat egy tartományfejléccel, hogy elkerülje a már letöltött szegmensek újbóli letöltését.

Rögzített adathordozó letöltése: 
- Módszer: `GET` 
- Url: https://contoso.communication.azure.com/recording/download/{documentId}?api-version=2021-04-15-preview1

Rögzített médiametaadatok letöltése: 
- Módszer: `GET` 
- Url: https://contoso.communication.azure.com/recording/download/{documentId}/metadata?api-version=2021-04-15-preview1


### <a name="authentication"></a>Hitelesítés
A rögzített médiatartalmak és metaadatok letöltéséhez HMAC-hitelesítéssel hitelesítse a kérést Azure Communication Services API-kon.

Hozzon létre egy kódot, és adja hozzá a szükséges `HttpClient` fejléceket `HmacAuthenticationUtils` az alábbiak szerint:

```
  var client = new HttpClient();

  // Set Http Method
  var method = HttpMethod.Get;
  StringContent content = null;

  // Build request
  var request = new HttpRequestMessage
  {
      Method = method, // Http GET method
      RequestUri = new Uri(<Download_Recording_Url>), // Download recording Url
      Content = content // content if required for POST methods
  };

  // Question: Why do we need to pass String.Empty to CreateContentHash() method?
  // Answer: In HMAC authentication, the hash of the content is one of the parameters used to generate the HMAC token.
  // In our case our recording download APIs are GET methods and do not have any content/body to be passed in the request. 
  // However in this case we still need the SHA256 hash for the empty content and hence we pass an empty string. 


  string serializedPayload = string.Empty;

  // Hash the content of the request.
  var contentHashed = HmacAuthenticationUtils.CreateContentHash(serializedPayload);

  // Add HAMC headers.
  HmacAuthenticationUtils.AddHmacHeaders(request, contentHashed, accessKey, method);

  // Make a request to the Azure Communication Services APIs mentioned above
  var response = await client.SendAsync(request).ConfigureAwait(false);
```

#### <a name="hmacauthenticationutils"></a>HmacAuthenticationUtils 
Az alábbi segédprogramok segítségével felügyelheti a HMAC-munkafolyamatot.

**Tartalom kivonatának létrehozása**

```
public static string CreateContentHash(string content)
{
    var alg = SHA256.Create();

    using (var memoryStream = new MemoryStream())
    using (var contentHashStream = new CryptoStream(memoryStream, alg, CryptoStreamMode.Write))
    {
        using (var swEncrypt = new StreamWriter(contentHashStream))
        {
            if (content != null)
            {
                swEncrypt.Write(content);
            }
        }
    }

    return Convert.ToBase64String(alg.Hash);
}
```

**HMAC-fejlécek hozzáadása**

```
public static void AddHmacHeaders(HttpRequestMessage requestMessage, string contentHash, string accessKey)
{
    var utcNowString = DateTimeOffset.UtcNow.ToString("r", CultureInfo.InvariantCulture);
    var uri = requestMessage.RequestUri;
    var host = uri.Authority;
    var pathAndQuery = uri.PathAndQuery;

    var stringToSign = $"{requestMessage.Method}\n{pathAndQuery}\n{utcNowString};{host};{contentHash}";
    var hmac = new HMACSHA256(Convert.FromBase64String(accessKey));
    var hash = hmac.ComputeHash(Encoding.ASCII.GetBytes(stringToSign));
    var signature = Convert.ToBase64String(hash);
    var authorization = $"HMAC-SHA256 SignedHeaders=date;host;x-ms-content-sha256&Signature={signature}";

    requestMessage.Headers.Add("x-ms-content-sha256", contentHash);
    requestMessage.Headers.Add("Date", utcNowString);
    requestMessage.Headers.Add("Authorization", authorization);
}
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása
Ha törölni vagy eltávolítani szeretne egy Communication Services előfizetést, törölheti az erőforrást vagy az erőforráscsoportot. Az erőforráscsoport törlésével a hozzá társított összes többi erőforrást is törli. További információ az [erőforrások tisztításról.](../create-communication-resource.md?pivots=platform-azp&tabs=windows#clean-up-resources)


## <a name="next-steps"></a>Következő lépések
További információért tekintse át a következő cikkeket:

- Tekintse meg a [webes hívási mintát](https://docs.microsoft.com/azure/communication-services/samples/web-calling-sample)
- Tudnivalók az [SDK-képességek hívásához](https://docs.microsoft.com/azure/communication-services/quickstarts/voice-video-calling/calling-client-samples?pivots=platform-web)
- További információ a [hívásról](https://docs.microsoft.com/azure/communication-services/concepts/voice-video-calling/about-call-types)
