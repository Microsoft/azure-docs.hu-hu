---
title: Az Media Services monitorozása – referencia
description: Fontos referenciaanyagok, amelyekre szükség van a Media Services
author: IngridAtMicrosoft
ms.author: inhenkel
manager: femila
ms.topic: reference
ms.service: media-services
ms.custom: subject-monitoring
ms.date: 04/21/2021
ms.openlocfilehash: 3fd7b8013ec67d718f308ccd1b72a6f90012e02e
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2021
ms.locfileid: "107873054"
---
# <a name="monitoring-media-services-data-reference"></a>Az Media Services monitorozása – referencia

Ez a cikk azokat az adatokat tartalmazza, amelyek hasznosak lehetnek a Media Services. További információ a Azure Monitor támogatott platformmetrikákról: [Támogatott metrikák Azure Monitor.](../../../azure-monitor/essentials/metrics-supported.md)

## <a name="metrics"></a>Mérőszámok

A rendszer rendszeres időközönként gyűjt metrikákat, függetlenül attól, hogy változik-e az érték. Hasznosak a riasztások esetében, mert gyakran mintavételezheti őket, a riasztások pedig viszonylag egyszerű logikával gyorsan elbocsáthatóak.


Media Services a következő erőforrásokhoz támogatja a metrikák monitorozását:

|Metrika típusa | Erőforrás-szolgáltató / Névtér típusa<br/> és az egyes metrikákra mutató hivatkozás |
|-------|-----|
| Media Services általános | [Általános](/azure/azure-monitor/essentials/metrics-supported#microsoftmediamediaservices) |
| Élő események | [Microsoft.Media/mediaservices/liveEvents](/azure/azure-monitor/essentials/metrics-supported#microsoftmediamediaservicesliveevents) 
| Streamvégpontok | [Microsoft.Media/mediaservices/streamingEndpoints,](/azure/azure-monitor/essentials/metrics-supported#microsoftmediamediaservicesstreamingendpoints)amelyek a streamvégpontokhoz [kapcsolódóan REST API.](/rest/api/media/streamingendpoints) 


Emellett tekintse át a [fiókkvótákat és a korlátokat is.](../limits-quotas-constraints-reference.md)


## <a name="metric-dimensions"></a>Metrikadimenziók

További információ a metrikadimenziókról: [Többdimenziós metrikák.](../../../azure-monitor/essentials/data-platform-metrics.md#multi-dimensional-metrics)

A Media Services a következő metrikadimenziókkal rendelkezik.  Ezek az ő támogatásukhoz támogatott metrikákon alapulnak.  További [információért tekintse meg a fenti](#metrics) metrikákra mutató hivatkozásokat.   
- OutputFormat (Kimenet formázása)
- HttpStatusCode 
- ErrorCode 
- TrackName (Nyomon követése) 

## <a name="resource-logs"></a>Erőforrásnaplók

Az erőforrásnaplók részletes és gyakori adatokat biztosítanak az Azure-erőforrások működéséről. További információ: Naplóadatok gyűjtése és felhasználása [az Azure-erőforrásokból.](../../../azure-monitor/essentials/platform-logs-overview.md)

Media Services a következő erőforrásnaplókat támogatja: [Microsoft.Media/mediaservices](/azure/azure-monitor/essentials/resource-logs-categories#microsoftmediamediaservices)

## <a name="schemas"></a>Sémák

A legfelső szintű diagnosztikai naplók sémáját a Támogatott szolgáltatások, sémák és kategóriák [az Azure Diagnostics-naplókhoz szakasz tartalmazza részletesen.](../../../azure-monitor/essentials/resource-logs-schema.md)

## <a name="key-delivery-log-schema-properties"></a>Kulcskésési napló sématulajdonságok

Ezek a tulajdonságok a kulcsk kézbesítési napló sémára vonatkoznak.

|Név|Leírás|
|---|---|
|keyId (kulcsazonosító)|A kért kulcs azonosítója.|
|keyType|A következő értékek egyike lehet: "Clear" (nincs titkosítás), "FairPlay", "PlayReady" vagy "Widevine".|
|policyName (szabályzat neve)|A Azure Resource Manager neve.|
|tokenType|A jogkivonat típusa.|
|statusMessage (állapotüzenet)|Az állapotüzenet.|

### <a name="example"></a>Példa

A kulcsk kézbesítési kérelmek sémája.

```json
{
    "time": "2019-01-11T17:59:10.4908614Z",
    "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-0000000000/RESOURCEGROUPS/SBKEY/PROVIDERS/MICROSOFT.MEDIA/MEDIASERVICES/SBDNSTEST",
    "operationName": "MICROSOFT.MEDIA/MEDIASERVICES/CONTENTKEYS/READ",
    "operationVersion": "1.0",
    "category": "KeyDeliveryRequests",
    "resultType": "Succeeded",
    "resultSignature": "OK",
    "durationMs": 315,
    "identity": {
        "authorization": {
            "issuer": "http://testacs",
            "audience": "urn:test"
        },
        "claims": {
            "urn:microsoft:azure:mediaservices:contentkeyidentifier": "3321e646-78d0-4896-84ec-c7b98eddfca5",
            "iss": "http://testacs",
            "aud": "urn:test",
            "exp": "1547233138"
        }
    },
    "level": "Informational",
    "location": "uswestcentral",
    "properties": {
        "requestId": "b0243468-d8e5-4edf-a48b-d408e1661050",
        "keyType": "Clear",
        "keyId": "3321e646-78d0-4896-84ec-c7b98eddfca5",
        "policyName": "56a70229-82d0-4174-82bc-e9d3b14e5dbf",
        "tokenType": "JWT",
        "statusMessage": "OK"
    }
} 
```

```json
 {
    "time": "2019-01-11T17:59:33.4676382Z",
    "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-0000000000/RESOURCEGROUPS/SBKEY/PROVIDERS/MICROSOFT.MEDIA/MEDIASERVICES/SBDNSTEST",
    "operationName": "MICROSOFT.MEDIA/MEDIASERVICES/CONTENTKEYS/READ",
    "operationVersion": "1.0",
    "category": "KeyDeliveryRequests",
    "resultType": "Failed",
    "resultSignature": "Unauthorized",
    "durationMs": 2,
    "level": "Error",
    "location": "uswestcentral",
    "properties": {
        "requestId": "875af030-b77c-416b-b7e1-58f23ebec182",
        "keyType": "Clear",
        "keyId": "3321e646-78d0-4896-84ec-c7b98eddfca5",
        "policyName": "56a70229-82d0-4174-82bc-e9d3b14e5dbf",
        "tokenType": "None",
        "statusMessage": "No token present in authorization header or URL."
    }
} 
```

>[!NOTE]
> A Widevine a Google Inc. által biztosított szolgáltatás, amely a Google, Inc. szolgáltatási feltételeire és adatvédelmi szabályzatára vonatkozik.

## <a name="next-steps"></a>Következő lépések

[!INCLUDE [monitoring-next-steps](../includes/monitoring-next-steps.md)]
