---
title: Media Services adathivatkozás figyelése
description: A Media Services figyeléséhez szükséges fontos referenciaanyagok
author: IngridAtMicrosoft
ms.author: inhenkel
manager: femila
ms.topic: reference
ms.service: media-services
ms.date: 03/17/2021
ms.openlocfilehash: 866b2faf473f06fc3f85cdb434d6555504a7f6a8
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "104598184"
---
# <a name="monitoring-media-services-data-reference"></a>Media Services adathivatkozás figyelése

Ez a cikk a Media Services figyeléséhez hasznos információkat ismerteti. A Azure Monitor által támogatott összes platform metrikával kapcsolatos további információkért tekintse át [a támogatott mérőszámokat Azure monitor](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported).

## <a name="media-services-metrics"></a>Media Services metrikák

A metrikák gyűjtése rendszeres időközönként történik, függetlenül attól, hogy az érték megváltozik-e. Ezek a riasztások azért hasznosak, mert gyakran mintavételezésre is képesek, és a riasztások gyorsan és viszonylag egyszerű logikával is elindíthatók.

Media Services támogatja a következő erőforrások figyelési metrikáit:

* Fiók
* Streamvégpont

### <a name="account"></a>Fiók

A következő fiók metrikáit figyelheti.

|Metrika neve|Megjelenített név|Description|
|---|---|---|
|AssetCount|Eszközök száma|A fiókban lévő eszközök.|
|AssetQuota|Eszköz kvótája|A fiókban lévő eszköz kvótája.|
|AssetQuotaUsedPercentage|Eszköz kvótájának kihasználtsága (%)|Az eszköz kvótájának százalékos aránya már használatban van.|
|ContentKeyPolicyCount|Tartalmi kulcsokra vonatkozó szabályzatok száma|A fiókban található tartalmi kulcsokra vonatkozó szabályzatok.|
|ContentKeyPolicyQuota|Tartalmi kulcs házirend-kvótája|A fiókban található tartalmi kulcs házirendjeinek kvótája.|
|ContentKeyPolicyQuotaUsedPercentage|Tartalom kulcsára vonatkozó házirend kvótájának százalékos aránya|A tartalmi kulcs házirend-kvótájának százalékos aránya már használatban van.|
|StreamingPolicyCount|Folyamatos átviteli szabályzatok száma|Streaming-szabályzatok a fiókban.|
|StreamingPolicyQuota|Streaming Policy-kvóta|Folyamatos átviteli szabályzatok kvótája a fiókban.|
|StreamingPolicyQuotaUsedPercentage|Adatfolyam-házirend kvótájának kihasználtsága (%)|Az adatfolyam-házirend kvótájának százalékos aránya már használatban van.|

Tekintse át [a fiók kvótáit és korlátozásait](../limits-quotas-constraints.md)is.

### <a name="streaming-endpoint"></a>Streamvégpont

A következő Media Services [folyamatos átviteli végpontok](/rest/api/media/streamingendpoints) metrikáit támogatja:

|Metrika neve|Megjelenített név|Description|
|---|---|---|
|Kérelmek|Kérelmek|A folyamatos átviteli végpont által kiszolgált HTTP-kérelmek teljes számát adja meg.|
|Kimenő forgalom|Kimenő forgalom|Kimenő átviteli végpontok percenkénti bájtjainak száma.|
|SuccessE2ELatency|A Befejezés végének késése|Az az időtartam, amikor a folyamatos átviteli végpont a válasz utolsó bájtjának küldésére vonatkozó kérést kapott.|
|Processzorhasználat| | A prémium szintű streaming-végpontok CPU-használata. Ezek az adatátviteli végpontok nem érhetők el. |
|Kimenő sávszélesség | | Kimenő forgalom (bit/mp).|

## <a name="metric-dimensions"></a>Metrikus méretek

A metrikus dimenziókkal kapcsolatos további információkért lásd: [többdimenziós mérőszámok](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform-metrics#multi-dimensional-metrics).

<!--**PLACEHOLDER** for dimensions table.-->

## <a name="resource-logs"></a>Erőforrásnaplók

## <a name="media-services-diagnostic-logs"></a>Diagnosztikai naplók Media Services

A diagnosztikai naplók részletes és gyakori információkat biztosítanak az Azure-erőforrások működéséről. További információkért lásd: az [Azure-erőforrások naplózási adatainak gyűjtése és felhasználása](https://docs.microsoft.com/azure/azure-monitor/essentials/platform-logs-overview.md).

A Media Services a következő diagnosztikai naplókat támogatja:

* Kulcs kézbesítése

### <a name="key-delivery"></a>Kulcs kézbesítése

|Név|Leírás|
|---|---|
|Kulcs kézbesítési szolgáltatásának kérése|A kulcs kézbesítési szolgáltatására vonatkozó adatokat megjelenítő naplók. További információ: [sémák](monitor-media-services-data-reference.md).|

## <a name="schemas"></a>Sémák

A legfelső szintű diagnosztikai naplók sémájának részletes ismertetését lásd: [támogatott szolgáltatások, sémák és kategóriák az Azure diagnosztikai naplóihoz](https://docs.microsoft.com/azure/azure-monitor/essentials/resource-logs-schema.md).

## <a name="key-delivery-log-schema-properties"></a>Kulcs kézbesítési naplójának sémájának tulajdonságai

Ezek a tulajdonságok a Key Delivery log sémára vonatkoznak.

|Név|Leírás|
|---|---|
|keyId|A kért kulcs azonosítója.|
|keyType|A következő értékek egyike lehet: "Clear" (nincs titkosítás), "FairPlay", "PlayReady" vagy "Widevine".|
|policyName|A házirend Azure Resource Manager neve.|
|tokenType|A jogkivonat típusa.|
|statusMessage|Az állapotjelző üzenet.|

### <a name="example"></a>Példa

A kulcs kézbesítési kérések sémájának tulajdonságai.

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
> A Widevine a Google Inc által biztosított szolgáltatás, és a Google, Inc. szolgáltatási és adatvédelmi szabályzatának feltételei vonatkoznak rá.

## <a name="next-steps"></a>Következő lépések

[!INCLUDE [monitoring-next-steps](../includes/monitoring-next-steps.md)]
