---
title: Streamvégpontok kezelése
description: Ez a cikk bemutatja, hogyan kezelheti a streamvégpontokat Azure Media Services 3-as Azure Media Services használatával.
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
ms.openlocfilehash: 2b442edc537ec64b12df215a18ab017ee47becff
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107791732"
---
# <a name="manage-streaming-endpoints-with--media-services-v3"></a>Streamvégpont kezelése a 3-as Media Services használatával

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

A Media Services létrehozásakor **a** [](stream-streaming-endpoint-concept.md) rendszer hozzáad egy alapértelmezett streamvégpontot a fiókhoz Leállítva **állapotban.** A tartalom streamelésének és [](encode-dynamic-packaging-concept.md) a [](drm-content-protection-concept.md)dinamikus csomagolás és a dinamikus titkosítás előnyeinek kihasználás érdekében a tartalomstreameléshez használt streamvégpontnak Fut **állapotban** kell lennie.

Ez a cikk bemutatja, hogyan hajtható végre a [start](/rest/api/media/streamingendpoints/start) parancs a streamvégponton különböző technológiák használatával. 
 
> [!NOTE]
> Csak akkor kell fizetni, ha a streamvégpont fut.
    
## <a name="prerequisites"></a>Előfeltételek

Felülvizsgálat: 

* [Media Services alapfogalmak](concepts-overview.md)
* [A streamvégpont fogalma](stream-streaming-endpoint-concept.md)
* [Dinamikus csomagolás](encode-dynamic-packaging-concept.md)

## <a name="use-rest"></a>A REST használata

```rest
POST https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/mediaresources/providers/Microsoft.Media/mediaservices/slitestmedia10/streamingEndpoints/myStreamingEndpoint1/start?api-version=2018-07-01
```

További információkért lásd: 

* A [StreamingEndpoint referenciadokumentációjának](/rest/api/media/streamingendpoints/start) kezdete.
* A streamvégpont indítása aszinkron művelet. 

    A hosszú ideig futó műveletek figyelésének további információiért lásd: [Hosszú ideig futó műveletek.](media-services-apis-overview.md)
* Ez a [Postman-gyűjtemény](https://github.com/Azure-Samples/media-services-v3-rest-postman/blob/master/Postman/Media%20Services%20v3.postman_collection.json) több REST-műveletre is tartalmaz példákat, többek között a streamvégpont indítási mikéntjére.

## <a name="use-the-azure-portal"></a>Az Azure Portal használata 
 
1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
1. Ugrás az Azure Media Services fiókra.
1. A bal oldali panelen válassza a **Streamvégpont lehetőséget.**
1. Válassza ki az elindítani kívánt streamvégpontot, majd válassza az Indítás **lehetőséget.**

## <a name="use-the-azure-cli"></a>Az Azure parancssori felületének használata

```cli
az ams streaming-endpoint start [--account-name]
                                [--ids]
                                [--name]
                                [--no-wait]
                                [--resource-group]
                                [--subscription]
```

További információ: [az ams streaming-endpoint start.](/cli/azure/ams/streaming-endpoint#az_ams_streaming_endpointstart)

## <a name="use-sdks"></a>SDK-k használata

### <a name="java"></a>Java
    
```java
if (streamingEndpoint != null) {
// Start The Streaming Endpoint if it is not running.
if (streamingEndpoint.resourceState() != StreamingEndpointResourceState.RUNNING) {
    manager.streamingEndpoints().startAsync(config.getResourceGroup(), config.getAccountName(), STREAMING_ENDPOINT_NAME).await();
}
```

Lásd a teljes [Java-kódmintát.](https://github.com/Azure-Samples/media-services-v3-java/blob/master/DynamicPackagingVODContent/StreamHLSAndDASH/src/main/java/sample/StreamHLSAndDASH.java#L128)

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

Lásd a teljes [.NET-kódmintát.](https://github.com/Azure-Samples/media-services-v3-dotnet/blob/main/Streaming/StreamHLSAndDASH/Program.cs#L112)

---

## <a name="next-steps"></a>Következő lépések

* [Media Services v3 OpenAPI-specifikáció (Swagger)](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2018-07-01)
* [Streamvégpont-műveletek](/rest/api/media/streamingendpoints)
