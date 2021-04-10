---
title: Azure Media Services tartalom közzététele a .NET használatával | Microsoft Docs
description: Megtudhatja, hogyan hozhat létre egy streaming URL-cím létrehozásához használt lokátort. A kód minták C# nyelven íródtak, és a .NET-hez készült Media Services SDK-t használják.
author: IngridAtMicrosoft
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.assetid: c53b1f83-4cb1-4b09-840f-9c145b7d6f8d
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.custom: devx-track-csharp
ms.openlocfilehash: a1a06c9a5fdac1082a57bee71cccad271a6aae81
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "103015794"
---
# <a name="publish-media-services-content-using-net"></a>Media Services tartalom közzététele a .NET használatával

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!div class="op_single_selector"]
> * [REST](media-services-rest-deliver-streaming-content.md)
> * [.NET](media-services-deliver-streaming-content.md)
> * [Portál](media-services-portal-publish.md)
> 
> 

## <a name="overview"></a>Áttekintés
Az adaptív sávszélességű MP4-készleteket egy OnDemand streaming-lokátor létrehozásával és egy streaming URL-cím megadásával is továbbíthatja. Az [eszköz kódolása](media-services-encode-asset.md) című témakör bemutatja, hogyan kódolhat egy adaptív sávszélességű MP4-készletbe. 

> [!NOTE]
> Ha a tartalom titkosítva van, a lokátor létrehozása előtt konfigurálja az eszköz kézbesítési szabályzatát (a [jelen](media-services-dotnet-configure-asset-delivery-policy.md) témakörben leírtak szerint). 
> 
> 

A OnDemand streaming-lokátor használatával olyan URL-címeket is létrehozhat, amelyek a fokozatosan letöltött MP4-fájlokra mutatnak.  

Ebből a témakörből megtudhatja, hogyan hozhat létre egy OnDemand streaming-lokátort az objektum közzétételéhez, valamint zökkenőmentes, MPEG DASH és HLS streaming URL-címek létrehozásához. Azt is bemutatja, hogyan készíthet a progresszív letöltési URL-címeket. 

## <a name="create-an-ondemand-streaming-locator"></a>OnDemand-kereső létrehozása
A OnDemand streaming-lokátor létrehozásához és az URL-címek lekéréséhez a következő műveleteket kell elvégeznie:

1. Ha a tartalom titkosítva van, adjon meg egy hozzáférési szabályzatot.
2. Hozzon létre egy OnDemand streaming lokátort.
3. Ha adatfolyamot szeretne készíteni, szerezze be a streaming manifest-fájlt (. ISM) az eszközben. 
   
   Ha azt tervezi, hogy fokozatosan letölti a fájlt, olvassa be az MP4-fájlok nevét az objektumban.  
4. URL-címeket hozhat létre a jegyzékfájlhoz vagy MP4-fájlokhoz. 


>[!NOTE]
>A különböző AMS-szabályzatok (például a Locator vagy a ContentKeyAuthorizationPolicy) esetében a korlát 1 000 000 szabályzat. Ha mindig ugyanazokat a napokat/hozzáférési engedélyeket használja, használja ugyanazt a házirend-azonosítót. Például olyan lokátorokra vonatkozó szabályzatok, amelyek hosszú ideig tartanak maradni (nem feltöltési szabályzatok). További információ [ebben](media-services-dotnet-manage-entities.md#limit-access-policies) a témakörben érhető el.

### <a name="use-media-services-net-sdk"></a>A Media Services .NET SDK használata
Streaming URL-címek összeállítása 

```csharp
    private static void BuildStreamingURLs(IAsset asset)
    {

        // Create a 30-day readonly access policy. 
          // You cannot create a streaming locator using an AccessPolicy that includes write or delete permissions.
        IAccessPolicy policy = _context.AccessPolicies.Create("Streaming policy",
            TimeSpan.FromDays(30),
            AccessPermissions.Read);

        // Create a locator to the streaming content on an origin. 
        ILocator originLocator = _context.Locators.CreateLocator(LocatorType.OnDemandOrigin, asset,
            policy,
            DateTime.UtcNow.AddMinutes(-5));

        // Display some useful values based on the locator.
        Console.WriteLine("Streaming asset base path on origin: ");
        Console.WriteLine(originLocator.Path);
        Console.WriteLine();

        // Get a reference to the streaming manifest file from the  
        // collection of files in the asset. 
        var manifestFile = asset.AssetFiles.ToList().Where(f => f.Name.ToLower().
                                    EndsWith(".ism")).
                                    FirstOrDefault();

        // Create a full URL to the manifest file. Use this for playback
        // in streaming media clients. 
        string urlForClientStreaming = originLocator.Path + manifestFile.Name + "/manifest";
        Console.WriteLine("URL to manifest for client streaming using Smooth Streaming protocol: ");
        Console.WriteLine(urlForClientStreaming);
        Console.WriteLine("URL to manifest for client streaming using HLS protocol: ");
        Console.WriteLine(urlForClientStreaming + "(format=m3u8-aapl)");
        Console.WriteLine("URL to manifest for client streaming using MPEG DASH protocol: ");
        Console.WriteLine(urlForClientStreaming + "(format=mpd-time-csf)"); 
        Console.WriteLine();
    }
```

A kimenetek:

- Az ügyfél-adatfolyam Smooth Streaming protokollon keresztüli továbbításának jegyzékfájlja: \
  `http://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny.ism/manifest`
- Az ügyfél-adatfolyam HLS protokollal való továbbításának jegyzékfájlja: \
  `http://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny.ism/manifest(format=m3u8-aapl)`
- Az ügyfél adatfolyam-továbbítási URL-címe az MPEG DASH protokoll használatával: \
  `http://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny.ism/manifest(format=mpd-time-csf)`

> [!NOTE]
> A tartalmat TLS-kapcsolaton keresztül is továbbíthatja. Ehhez a megközelítéshez győződjön meg arról, hogy a streaming URL-címek a HTTPS protokollal kezdődnek. Az AMS jelenleg nem támogatja a TLS-t egyéni tartományokkal.
> 
> 

Progresszív letöltési URL-címek készítése 

```csharp
    private static void BuildProgressiveDownloadURLs(IAsset asset)
    {
        // Create a 30-day readonly access policy. 
        IAccessPolicy policy = _context.AccessPolicies.Create("Streaming policy",
            TimeSpan.FromDays(30),
            AccessPermissions.Read);

        // Create an OnDemandOrigin locator to the asset. 
        ILocator originLocator = _context.Locators.CreateLocator(LocatorType.OnDemandOrigin, asset,
            policy,
            DateTime.UtcNow.AddMinutes(-5));

        // Display some useful values based on the locator.
        Console.WriteLine("Streaming asset base path on origin: ");
        Console.WriteLine(originLocator.Path);
        Console.WriteLine();

        // Get MP4 files.
        IEnumerable<IAssetFile> mp4AssetFiles = asset
            .AssetFiles
            .ToList()
            .Where(af => af.Name.EndsWith(".mp4", StringComparison.OrdinalIgnoreCase));

        // Create a full URL to the MP4 files. Use this to progressively download files.
        foreach (var pd in mp4AssetFiles)
            Console.WriteLine(originLocator.Path + pd.Name);
    }
```
A kimenetek:

- `http://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny_H264_650kbps_AAC_und_ch2_96kbps.mp4`
- `http://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny_H264_400kbps_AAC_und_ch2_96kbps.mp4`
- `http://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny_H264_3400kbps_AAC_und_ch2_96kbps.mp4`
- `http://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny_H264_2250kbps_AAC_und_ch2_96kbps.mp4`

    . . . 

### <a name="use-media-services-net-sdk-extensions"></a>Media Services .NET SDK-bővítmények használata
A következő kód meghívja a .NET SDK-bővítmények metódusait, amelyek egy lokátort hoznak létre, és létrehozzák az adaptív adatfolyamhoz tartozó Smooth Streaming, HLS és MPEG-DASH URL-címeket.
```csharp
    // Create a loctor.
    _context.Locators.Create(
        LocatorType.OnDemandOrigin,
        inputAsset,
        AccessPermissions.Read,
        TimeSpan.FromDays(30));

    // Get the streaming URLs.
    Uri smoothStreamingUri = inputAsset.GetSmoothStreamingUri();
    Uri hlsUri = inputAsset.GetHlsUri();
    Uri mpegDashUri = inputAsset.GetMpegDashUri();

    Console.WriteLine(smoothStreamingUri);
    Console.WriteLine(hlsUri);
    Console.WriteLine(mpegDashUri);
```

## <a name="media-services-learning-paths"></a>A Media Services tanulási útvonalai
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="next-steps"></a>Következő lépések
* [Eszközök letöltése](media-services-deliver-asset-download.md)
* [Objektumtovábbítási szabályzat konfigurálása](media-services-dotnet-configure-asset-delivery-policy.md)

