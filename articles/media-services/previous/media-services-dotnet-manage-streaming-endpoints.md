---
title: Adatfolyam-végpontok kezelése a .NET SDK-val. | Microsoft Docs
description: Ez a cikk bemutatja, hogyan kezelheti a streaming-végpontokat a .NET SDK-val.
services: media-services
documentationcenter: ''
author: Juliako
writer: juliako
manager: femila
editor: ''
ms.assetid: 0da34a97-f36c-48d0-8ea2-ec12584a2215
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/18/2019
ms.author: juliako
ms.custom: devx-track-csharp
ms.openlocfilehash: faf9acc6e91b43e2dd82589127f8697c666d86e3
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/25/2020
ms.locfileid: "91250529"
---
# <a name="manage-streaming-endpoints-with-net-sdk"></a>Streaming-végpontok kezelése a .NET SDK-val

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]  

>[!NOTE]
>Ügyeljen rá, hogy tekintse át az [áttekintő](media-services-streaming-endpoints-overview.md) cikket. Továbbá tekintse át a [streamvégpontok](/rest/api/media/operations/streamingendpoint).

A cikkben szereplő kód bemutatja, hogyan végezheti el a következő feladatokat a Azure Media Services .NET SDK használatával:

- Vizsgálja meg az alapértelmezett folyamatos átviteli végpontot.
- Új folyamatos átviteli végpont létrehozása/hozzáadása.

    Előfordulhat, hogy több folyamatos átviteli végponttal szeretne rendelkezni, ha különböző CDNs vagy CDN-t vagy közvetlen hozzáférést tervez.

    > [!NOTE]
    > Csak akkor számítunk fel díjat, ha a folyamatos átviteli végpont fut állapotban van.
    
- Frissítse a folyamatos átviteli végpontot.
    
    Győződjön meg arról, hogy az Update () függvényt hívja meg.

- Törölje a streaming végpontot.

    >[!NOTE]
    >Az alapértelmezett folyamatos átviteli végpont nem törölhető.

A folyamatos átviteli végpont skálázásával kapcsolatos információkért tekintse meg [ezt](media-services-portal-scale-streaming-endpoints.md) a cikket.

## <a name="create-and-configure-a-visual-studio-project"></a>Egy Visual Studio-projekt létrehozása és konfigurálása

Állítsa be a fejlesztési környezetet, és töltse fel a app.config fájlt a következő témakörben leírtak szerint: [Media Services fejlesztés a .net](media-services-dotnet-how-to-use.md)-tel. 

## <a name="add-code-that-manages-streaming-endpoints"></a>Adatfolyam-végpontokat kezelő kód hozzáadása
    
Cserélje le a kódot a Program.cs a következő kódra:

```csharp
using System;
using System.Configuration;
using System.Linq;
using Microsoft.WindowsAzure.MediaServices.Client;
using Microsoft.WindowsAzure.MediaServices.Client.Live;

namespace AMSStreamingEndpoint
{
    class Program
    {
        // Read values from the App.config file.

        private static readonly string _AADTenantDomain =
            ConfigurationManager.AppSettings["AMSAADTenantDomain"];
        private static readonly string _RESTAPIEndpoint =
            ConfigurationManager.AppSettings["AMSRESTAPIEndpoint"];
        private static readonly string _AMSClientId =
            ConfigurationManager.AppSettings["AMSClientId"];
        private static readonly string _AMSClientSecret =
            ConfigurationManager.AppSettings["AMSClientSecret"];

        private static CloudMediaContext _context = null;

        static void Main(string[] args)
        {
            AzureAdTokenCredentials tokenCredentials =
                new AzureAdTokenCredentials(_AADTenantDomain,
                    new AzureAdClientSymmetricKey(_AMSClientId, _AMSClientSecret),
                    AzureEnvironments.AzureCloudEnvironment);

            var tokenProvider = new AzureAdTokenProvider(tokenCredentials);

            _context = new CloudMediaContext(new Uri(_RESTAPIEndpoint), tokenProvider);

            var defaultStreamingEndpoint = _context.StreamingEndpoints.Where(s => s.Name.Contains("default")).FirstOrDefault();
            ExamineStreamingEndpoint(defaultStreamingEndpoint);

            IStreamingEndpoint newStreamingEndpoint = AddStreamingEndpoint();
            UpdateStreamingEndpoint(newStreamingEndpoint);
            DeleteStreamingEndpoint(newStreamingEndpoint);
        }

        static public void ExamineStreamingEndpoint(IStreamingEndpoint streamingEndpoint)
        {
            Console.WriteLine(streamingEndpoint.Name);
            Console.WriteLine(streamingEndpoint.StreamingEndpointVersion);
            Console.WriteLine(streamingEndpoint.FreeTrialEndTime);
            Console.WriteLine(streamingEndpoint.ScaleUnits);
            Console.WriteLine(streamingEndpoint.CdnProvider);
            Console.WriteLine(streamingEndpoint.CdnProfile);
            Console.WriteLine(streamingEndpoint.CdnEnabled);
        }

        static public IStreamingEndpoint AddStreamingEndpoint()
        {
            var name = "StreamingEndpoint" + DateTime.UtcNow.ToString("hhmmss");
            var option = new StreamingEndpointCreationOptions(name, 1)
            {
                StreamingEndpointVersion = new Version("2.0"),
                CdnEnabled = true,
                CdnProfile = "CdnProfile",
                CdnProvider = CdnProviderType.PremiumVerizon
            };

            var streamingEndpoint = _context.StreamingEndpoints.Create(option);

            return streamingEndpoint;
        }

        static public void UpdateStreamingEndpoint(IStreamingEndpoint streamingEndpoint)
        {
            if (streamingEndpoint.StreamingEndpointVersion == "1.0")
                streamingEndpoint.StreamingEndpointVersion = "2.0";

            streamingEndpoint.CdnEnabled = false;
            streamingEndpoint.Update();
        }

        static public void DeleteStreamingEndpoint(IStreamingEndpoint streamingEndpoint)
        {
            streamingEndpoint.Delete();
        }
    }
}
```

## <a name="next-steps"></a>Következő lépések
Tekintse át a Media Services képzési terveket.

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
