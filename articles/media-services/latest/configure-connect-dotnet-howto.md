---
title: Kapcsolódás Azure Media Services V3 API-hoz – .NET
description: Ez a cikk bemutatja, hogyan csatlakozhat Media Services V3 API-hoz a .NET-tel.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 11/17/2020
ms.author: inhenkel
ms.custom: has-adal-ref, devx-track-csharp
ms.openlocfilehash: 8946f6e94dd26db45622bc7609fb2375d59bb57e
ms.sourcegitcommit: 6386854467e74d0745c281cc53621af3bb201920
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/08/2021
ms.locfileid: "102455382"
---
# <a name="connect-to-media-services-v3-api---net"></a>Kapcsolódás Media Services V3 API-hoz – .NET

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Ez a cikk bemutatja, hogyan csatlakozhat a Azure Media Services v3 .NET SDK-hoz az egyszerű szolgáltatás bejelentkezési módszerének használatával.

## <a name="prerequisites"></a>Előfeltételek

- [Hozzon létre egy Media Services fiókot](./create-account-howto.md). Ügyeljen rá, hogy jegyezze fel az erőforráscsoport nevét és a Media Services fiók nevét
- Telepítsen egy olyan eszközt, amelyet a .NET-fejlesztéshez szeretne használni. A cikkben ismertetett lépések bemutatják, hogyan használható a [Visual Studio 2019 Community Edition](https://www.visualstudio.com/downloads/). A Visual Studio Code-ot használhatja [a C#](https://code.visualstudio.com/docs/languages/csharp)használata című témakörben. Másik lehetőségként más Kódszerkesztő is használható.

> [!IMPORTANT]
> Tekintse át az [elnevezési konvenciókat](media-services-apis-overview.md#naming-conventions).

## <a name="create-a-console-application"></a>Konzolalkalmazás létrehozása

1. Indítsa el a Visual Studiót. 
1. A **fájl** menüben kattintson az **új**  >  **projekt** elemre. 
1. Hozzon létre egy **.net Core** Console-alkalmazást.

Az ebben a témakörben található minta alkalmazás a célokat célozza meg `netcoreapp2.0` . A kód az "aszinkron Main" protokollt használja, amely a C# 7,1-től kezdődően érhető el. További részletekért tekintse meg ezt a [blogot](/archive/blogs/benwilli/async-main-is-available-but-hidden) .

## <a name="add-required-nuget-packagesassemblies"></a>Szükséges NuGet-csomagok/-szerelvények hozzáadása

1. A Visual Studióban válassza az **eszközök**  >  **NuGet csomagkezelő**  >  **NuGet-kezelő konzolt**.
2. A **Package Manager Console (csomagkezelő konzol** ) ablakban a `Install-Package` következő NuGet-csomagok hozzáadásához használja a parancsot. Például: `Install-Package Microsoft.Azure.Management.Media`.

|Csomag|Description|
|---|---|
|`Microsoft.Azure.Management.Media`|Azure Media Services SDK. <br/>Győződjön meg arról, hogy a legújabb Azure Media Services csomagot használja, és ellenőrizze a [Microsoft. Azure. Management. Media eszközt](https://www.nuget.org/packages/Microsoft.Azure.Management.Media).|

### <a name="other-required-assemblies"></a>Egyéb szükséges szerelvények

- Azure. Storage. Blobok
- Microsoft.Extensions.Configszülő
- Microsoft.Extensions.Configszülő. EnvironmentVariables
- Microsoft.Extensions.Configuration.Jsbekapcsolva
- Microsoft. Rest. ClientRuntime. Azure. Authentication

## <a name="create-and-configure-the-app-settings-file"></a>Az Alkalmazásbeállítások fájl létrehozása és konfigurálása

### <a name="create-appsettingsjson"></a>appsettings.jslétrehozása a következőn

1. Ugrás az **általános**  >  **szövegfájlba**.
1. Nevezze el "appsettings.jsbekapcsolva" értékre.
1. Állítsa a. JSON fájl "másolás a kimeneti könyvtárba" tulajdonságát a "Copy if újabb" értékre (hogy az alkalmazás elérhető legyen a közzétételkor).

### <a name="set-values-in-appsettingsjson"></a>Értékek beállítása appsettings.json

Futtassa a `az ams account sp create` parancsot a következő témakörben leírtak szerint: [Access API](./access-api-howto.md)-k. A parancs visszaadja a JSON-t, amelyet a "appsettings.jsbe" gombra kell másolni.
 
## <a name="add-configuration-file"></a>Konfigurációs fájl hozzáadása

A kényelem érdekében adjon hozzá egy olyan konfigurációs fájlt, amely felelős az értékek "appsettings.json" értékről való olvasásához.

1. Adjon hozzá egy új. cs osztályt a projekthez. Nevezze el a következőképpen: `ConfigWrapper`. 
1. Illessze be a következő kódot a fájlba (ez a példa feltételezi, hogy rendelkezik a névtérrel `ConsoleApp1` ).

```csharp
using System;

using Microsoft.Extensions.Configuration;

namespace ConsoleApp1
{
    public class ConfigWrapper
    {
        private readonly IConfiguration _config;

        public ConfigWrapper(IConfiguration config)
        {
            _config = config;
        }

        public string SubscriptionId
        {
            get { return _config["SubscriptionId"]; }
        }

        public string ResourceGroup
        {
            get { return _config["ResourceGroup"]; }
        }

        public string AccountName
        {
            get { return _config["AccountName"]; }
        }

        public string AadTenantId
        {
            get { return _config["AadTenantId"]; }
        }

        public string AadClientId
        {
            get { return _config["AadClientId"]; }
        }

        public string AadSecret
        {
            get { return _config["AadSecret"]; }
        }

        public Uri ArmAadAudience
        {
            get { return new Uri(_config["ArmAadAudience"]); }
        }

        public Uri AadEndpoint
        {
            get { return new Uri(_config["AadEndpoint"]); }
        }

        public Uri ArmEndpoint
        {
            get { return new Uri(_config["ArmEndpoint"]); }
        }

        public string Location
        {
            get { return _config["Location"]; }
        }
    }
}
```

## <a name="connect-to-the-net-client"></a>Kapcsolódás a .NET-ügyfélhez

Ha szeretné megkezdeni a Media Services API-k használatát a .NET-tel, létre kell hoznia egy **AzureMediaServicesClient** objektumot. Az objektum létrehozásához meg kell adnia a hitelesítő adatokat, amelyekkel az ügyfél csatlakozhat az Azure-hoz az Azure AD használatával. Az alábbi kódban a GetCredentialsAsync függvény a helyi konfigurációs fájlban megadott hitelesítő adatok alapján hozza létre a ServiceClientCredentials objektumot.

1. Nyissa meg a következő fájlt: `Program.cs`.
1. Illessze be a következő kódot:

```csharp
using System;
using System.Collections.Generic;
using System.IO;
using System.Linq;
using System.Threading.Tasks;

using Microsoft.Azure.Management.Media;
using Microsoft.Azure.Management.Media.Models;
using Microsoft.Extensions.Configuration;
using Microsoft.IdentityModel.Clients.ActiveDirectory;
using Microsoft.Rest;
using Microsoft.Rest.Azure.Authentication;

namespace ConsoleApp1
{
    class Program
    {
        public static async Task Main(string[] args)
        {
            
            ConfigWrapper config = new ConfigWrapper(new ConfigurationBuilder()
                .SetBasePath(Directory.GetCurrentDirectory())
                .AddJsonFile("appsettings.json", optional: true, reloadOnChange: true)
                .AddEnvironmentVariables()
                .Build());

            try
            {
                IAzureMediaServicesClient client = await CreateMediaServicesClientAsync(config);
                Console.WriteLine("connected");
            }
            catch (Exception exception)
            {
                if (exception.Source.Contains("ActiveDirectory"))
                {
                    Console.Error.WriteLine("TIP: Make sure that you have filled out the appsettings.json file before running this sample.");
                }

                Console.Error.WriteLine($"{exception.Message}");

                ApiErrorException apiException = exception.GetBaseException() as ApiErrorException;
                if (apiException != null)
                {
                    Console.Error.WriteLine(
                        $"ERROR: API call failed with error code '{apiException.Body.Error.Code}' and message '{apiException.Body.Error.Message}'.");
                }
            }

            Console.WriteLine("Press Enter to continue.");
            Console.ReadLine();
        }
 
        private static async Task<ServiceClientCredentials> GetCredentialsAsync(ConfigWrapper config)
        {
            // Use ApplicationTokenProvider.LoginSilentWithCertificateAsync or UserTokenProvider.LoginSilentAsync to get a token using service principal with certificate
            //// ClientAssertionCertificate
            //// ApplicationTokenProvider.LoginSilentWithCertificateAsync

            // Use ApplicationTokenProvider.LoginSilentAsync to get a token using a service principal with symmetric key
            ClientCredential clientCredential = new ClientCredential(config.AadClientId, config.AadSecret);
            return await ApplicationTokenProvider.LoginSilentAsync(config.AadTenantId, clientCredential, ActiveDirectoryServiceSettings.Azure);
        }

        private static async Task<IAzureMediaServicesClient> CreateMediaServicesClientAsync(ConfigWrapper config)
        {
            var credentials = await GetCredentialsAsync(config);

            return new AzureMediaServicesClient(config.ArmEndpoint, credentials)
            {
                SubscriptionId = config.SubscriptionId,
            };
        }

    }
}
```

## <a name="next-steps"></a>Következő lépések

- [Oktatóanyag: videók feltöltése, kódolása és továbbítása – .NET](stream-files-tutorial-with-api.md) 
- [Oktatóanyag: élő stream a Media Services v3-.NET-tel](stream-live-tutorial-with-api.md)
- [Oktatóanyag: videók elemzése Media Services v3-.NET használatával](analyze-videos-tutorial-with-api.md)
- [Feladathoz tartozó bemenet létrehozása helyi fájlból – .NET](job-input-from-local-file-how-to.md)
- [Feladathoz tartozó bemenet létrehozása egy HTTPS URL-címről – .NET](job-input-from-http-how-to.md)
- [Kódolás egyéni átalakítással – .NET](customize-encoder-presets-how-to.md)
- [AES-128 dinamikus titkosítás és a Key Delivery Service használata – .NET](protect-with-aes128.md)
- [A DRM dinamikus titkosítása és a licenc kézbesítése szolgáltatás használata – .NET](protect-with-drm.md)
- [Aláíró kulcs beszerzése a meglévő házirendből – .NET](get-content-key-policy-dotnet-howto.md)
- [Szűrők létrehozása Media Services-.NET-tel](filters-dynamic-manifest-dotnet-howto.md)
- [Speciális video igény szerinti példák a Azure Functions v2-re Media Services v3](https://aka.ms/ams3functions)

## <a name="see-also"></a>Lásd még

* [.NET-referencia](/dotnet/api/overview/azure/mediaservices/management)
* További példákat a [.net SDK-minták](https://github.com/Azure-Samples/media-services-v3-dotnet) tárházában talál.
