---
title: Streamelési lokátorok a Azure Media Services
description: Ez a cikk bemutatja, mik azok a streamelési lokátorok, és hogyan használják őket a Azure Media Services.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: conceptual
ms.date: 03/04/2020
ms.author: inhenkel
ms.custom: devx-track-csharp
ms.openlocfilehash: bcee8d0554b9c3349c7efc88c10e9eee8b185acb
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107773278"
---
# <a name="streaming-locators"></a>Streamelési lokátor

Ahhoz, hogy a kimeneti adategységben található videók elérhetők legyenek az ügyfelek számára lejátszásra, létre kell hozni egy [streamelési lokátort](/rest/api/media/streaminglocators), majd streamelési URL-címeket. URL-cím felépítéséhez össze kell egészítenie a streamvégpont gazdagépnevét és a streamelési lokátor elérési útját. Egy .NET-példáért tekintse meg a [streamelési lokátor beszerzését](stream-files-tutorial-with-api.md#get-a-streaming-locator) bemutató cikket.

A streamelési lokátor **létrehozásának** folyamatát közzétételnek nevezzük. Alapértelmezés szerint a **streamelési** lokátor az API-hívások után azonnal érvényes, és a törlésig érvényes, hacsak nem konfigurálja a választható kezdési és záró időpontokat. 

Streamelési **lokátor létrehozásakor** meg  kell adnia egy eszköznevet és egy **streamelési** szabályzat nevét. További információkért tekintse át a következők témaköröket:

* [Adategységek](assets-concept.md)
* [Streamelési szabályzatok](stream-streaming-policy-concept.md)
* [Tartalomkulcs-szabályzatok](drm-content-key-policy-concept.md)

Megadhatja a kezdő és záró időt is a streamelési lokátoron, így a felhasználó csak ezen időpontok között (például 2019. 05. és 2019. 05. 05. között) lejátszhatja a tartalmat.  

## <a name="considerations"></a>Megfontolandó szempontok

* **A streamelési lokátorok** nem frissíthetők. 
* A Datetime **típusú streamelési lokátorok** tulajdonságai mindig UTC formátumúak.
* Korlátozott számú szabályzatot kell tervezni a Media Service-fiókhoz, és újra fel kell használni őket a streamelési lokátorokhoz, ha ugyanazokra a beállításokra van szükség. További információ: [Kvóták és korlátok.](limits-quotas-constraints-reference.md)

## <a name="create-streaming-locators"></a>Streamelési lokátorok létrehozása  

### <a name="not-encrypted"></a>Nincs titkosítva

Ha a fájlt titkosítatlan (nem titkosított) streamelni szeretné, állítsa az előre definiált tiszta streamelési szabályzatot "Predefined_ClearStreamingOnly" (a .NET-ben használhatja a PredefinedStreamingPolicy.ClearStreamingOnly enumot).

```csharp
StreamingLocator locator = await client.StreamingLocators.CreateAsync(
    resourceGroup,
    accountName,
    locatorName,
    new StreamingLocator
    {
        AssetName = assetName,
        StreamingPolicyName = PredefinedStreamingPolicy.ClearStreamingOnly
    });
```

### <a name="encrypted"></a>Titkosított 

Ha CENC-titkosítással kell titkosítania a tartalmat, állítsa a szabályzatot "Predefined_MultiDrmCencStreaming". A Widevine-titkosítás egy DASH-streamre, a PlayReady pedig Smoothra lesz alkalmazva. A kulcs a konfigurált DRM-licencek alapján lesz kézbesítve a lejátszást lehetővé tő ügyfélnek.

```csharp
StreamingLocator locator = await client.StreamingLocators.CreateAsync(
    resourceGroup,
    accountName,
    locatorName,
    new StreamingLocator
    {
        AssetName = assetName,
        StreamingPolicyName = "Predefined_MultiDrmCencStreaming",
        DefaultContentKeyPolicyName = contentPolicyName
    });
```

Ha a HLS-streamet CBCS (FairPlay) használatával is szeretné titkosítani, használja a Predefined_MultiDrmStreaming.

> [!NOTE]
> A Widevine a Google Inc. által biztosított szolgáltatás, amely a Google, Inc. szolgáltatási feltételeire és adatvédelmi szabályzatára vonatkozik.

## <a name="associate-filters-with-streaming-locators"></a>Szűrők társítása streamelési lokátorokkal

Lásd: [Szűrők: társítás streamelési lokátorokkal.](filters-concept.md#associating-filters-with-streaming-locator)

## <a name="filter-order-page-streaming-locator-entities"></a>Szűrés, sorrend, oldalstreamelési lokátor entitások

Lásd: [Entitások szűrése, sorrendbe Media Services lapozása.](filter-order-page-entitites-how-to.md)

## <a name="list-streaming-locators-by-asset-name"></a>Streamelési lokátorok listája eszköznév szerint

Ha a streamelési lokátorokat a társított eszköznév alapján le kell kapnia, használja a következő műveleteket:

|Nyelv|API|
|---|---|
|REST|[liststreaminglocators (liststreaminglocators)](/rest/api/media/assets/liststreaminglocators)|
|parancssori felület|[az ams asset list-streaming-locators](/cli/azure/ams/asset#az_ams_asset_list_streaming_locators)|
|.NET|[ListStreamingLocators (ListStreamingLocators)](/dotnet/api/microsoft.azure.management.media.assetsoperationsextensions.liststreaminglocators#Microsoft_Azure_Management_Media_AssetsOperationsExtensions_ListStreamingLocators_Microsoft_Azure_Management_Media_IAssetsOperations_System_String_System_String_System_String_)|
|Java|[AssetStreamingLocator](/rest/api/media/assets/liststreaminglocators#assetstreaminglocator)|
|Node.js|[listStreamingLocators (ListStreamingLocators)](/javascript/api/@azure/arm-mediaservices/assets#liststreaminglocators-string--string--string--msrest-requestoptionsbase-)|

## <a name="see-also"></a>Lásd még

* [Adategységek](assets-concept.md)
* [Streamelési szabályzatok](stream-streaming-policy-concept.md)
* [Tartalomkulcs-szabályzatok](drm-content-key-policy-concept.md)
* [Oktatóanyag: Videók feltöltése, kódolása és streamelése a .NET használatával](stream-files-tutorial-with-api.md)

## <a name="next-steps"></a>Következő lépések

[Streamelési lokátor és URL-címek létrehozása](create-streaming-locator-build-url.md)
