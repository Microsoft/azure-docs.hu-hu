---
title: Streamvégpontok kezelése
description: Ez a cikk bemutatja, hogyan kezelheti a streaming-végpontokat Azure Media Services v3-val.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
writer: juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 08/31/2020
ms.author: inhenkel
ms.custom: devx-track-azurecli, devx-track-csharp
ms.openlocfilehash: 5ad7ef447e1b8e5ac141e4feb65f1b8361b2a646
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2021
ms.locfileid: "106282171"
---
# <a name="manage-streaming-endpoints-with--media-services-v3"></a>Adatfolyam-végpontok kezelése Media Services v3-val

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

A Media Services fiók létrehozásakor a rendszer **leállított** állapotban adja hozzá a fiókhoz az **alapértelmezett** [folyamatos átviteli végpontot](stream-streaming-endpoint-concept.md) . A tartalom folyamatos átvitelének megkezdéséhez, valamint a [dinamikus csomagolás](encode-dynamic-packaging-concept.md) és a [dinamikus titkosítás](drm-content-protection-concept.md)kihasználásához a adatfolyam-továbbítási végpontnak **futó** állapotban kell lennie.

Ez a cikk bemutatja, hogyan hajthatja végre a [Start](/rest/api/media/streamingendpoints/start) parancsot a folyamatos átviteli végponton különböző technológiák használatával. 
 
> [!NOTE]
> Csak akkor számítunk fel díjat, ha a folyamatos átviteli végpont fut állapotban van.
    
## <a name="prerequisites"></a>Előfeltételek

Tekintse át 

* [Media Services fogalmak](concepts-overview.md)
* [Streaming Endpoint koncepciója](stream-streaming-endpoint-concept.md)
* [Dinamikus csomagolás](encode-dynamic-packaging-concept.md)

## <a name="use-rest"></a>A REST használata

```rest
POST https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/mediaresources/providers/Microsoft.Media/mediaservices/slitestmedia10/streamingEndpoints/myStreamingEndpoint1/start?api-version=2018-07-01
```

További információkért lásd: 

* A [Start a streamvégpontok](/rest/api/media/streamingendpoints/start) Reference dokumentációja.
* Az adatfolyam-végpont indítása aszinkron művelet. 

    További információ a hosszan futó műveletek figyeléséről: [hosszan futó műveletek](media-services-apis-overview.md).
* Ez a [Poster-gyűjtemény](https://github.com/Azure-Samples/media-services-v3-rest-postman/blob/master/Postman/Media%20Services%20v3.postman_collection.json) több Rest-műveletre mutató példákat tartalmaz, beleértve a folyamatos átviteli végpontok indítását is.

## <a name="use-the-azure-portal"></a>Az Azure Portal használata 
 
1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
1. Nyissa meg Azure Media Services-fiókját.
1. A bal oldali ablaktáblán válassza a  **folyamatos átviteli végpontok** lehetőséget.
1. Válassza ki az elindítani kívánt folyamatos átviteli végpontot, majd kattintson a **Start** gombra.

## <a name="use-the-azure-cli"></a>Az Azure parancssori felületének használata

```cli
az ams streaming-endpoint start [--account-name]
                                [--ids]
                                [--name]
                                [--no-wait]
                                [--resource-group]
                                [--subscription]
```

További információ: [az AMS streaming-Endpoint Start](/cli/azure/ams/streaming-endpoint#az-ams-streaming-endpoint-start).

## <a name="use-sdks"></a>SDK-k használata

### <a name="java"></a>Java
    
```java
if (streamingEndpoint != null) {
// Start The Streaming Endpoint if it is not running.
if (streamingEndpoint.resourceState() != StreamingEndpointResourceState.RUNNING) {
    manager.streamingEndpoints().startAsync(config.getResourceGroup(), config.getAccountName(), STREAMING_ENDPOINT_NAME).await();
}
```

Tekintse meg a teljes [Java-kód mintát](https://github.com/Azure-Samples/media-services-v3-java/blob/master/DynamicPackagingVODContent/StreamHLSAndDASH/src/main/java/sample/StreamHLSAndDASH.java#L128).

### <a name="net"></a>.NET

```csharp
StreamingEndpoint streamingEndpoint = await client.StreamingEndpoints.GetAsync(config.ResourceGroup, config.AccountName, DefaultStreamingEndpointName);

if (streamingEndpoint != null)
{
    if (streamingEndpoint.ResourceState != StreamingEndpointResourceState.Running)
    {
        await client.StreamingEndpoints.StartAsync(config.ResourceGroup, config.AccountName, DefaultStreamingEndpointName);
    }
```

Tekintse meg a [.net-kód teljes mintáját](https://github.com/Azure-Samples/media-services-v3-dotnet/blob/main/Streaming/StreamHLSAndDASH/Program.cs#L112).

---

## <a name="next-steps"></a>Következő lépések

* [Media Services v3 OpenAPI-specifikáció (hencegés)](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2018-07-01)
* [Folyamatos átviteli végpont műveletei](/rest/api/media/streamingendpoints)
