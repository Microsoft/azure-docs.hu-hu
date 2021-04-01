---
title: A GA környezetekben található hivatkozási adatkezelés a C#-Azure Time Series Insights használatával | Microsoft Docs
description: Megtudhatja, hogyan kezelheti a GA-környezet hivatkozási adatait egy C# nyelven írt egyéni alkalmazás létrehozásával.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 09/29/2020
ms.custom: seodec18, devx-track-csharp
ms.openlocfilehash: d6f0e5230fb3c59cab690620e837f476f3392a48
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "95020078"
---
# <a name="manage-reference-data-for-an-azure-time-series-insights-gen-1-environment-using-c-sharp"></a>Azure Time Series Insights 1. generációs környezethez tartozó referenciák kezelése C Sharp használatával

> [!CAUTION]
> Ez egy Gen1-cikk.

Ez a cikk bemutatja, hogyan egyesítheti a C#, a [MSAL.net](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet)és a Azure Active Directoryt, hogy programozott API-kéréseket lehessen készíteni a Azure Time Series Insights Gen 1 [Reference adatkezelés API](/rest/api/time-series-insights/gen1-reference-data-api)-hoz.

> [!TIP]
> A GA C# kód mintáinak megtekintése a következő helyen: [https://github.com/Azure-Samples/Azure-Time-Series-Insights](https://github.com/Azure-Samples/Azure-Time-Series-Insights/tree/master/gen1-sample/csharp-tsi-gen1-sample) .

## <a name="summary"></a>Összefoglalás

Az alábbi mintakód a következő funkciókat mutatja be:

* Hozzáférési jogkivonat beszerzése a [MSAL.net](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet) **PublicClientApplication** használatával.
* Szekvenciális létrehozási, OLVASÁSI, frissítési és TÖRLÉSi műveletek az 1. generációs [hivatkozási adatkezelés API](/rest/api/time-series-insights/gen1-reference-data-api)-hoz.
* Gyakori válasz-kódok, beleértve a [gyakori hibakódokat](/rest/api/time-series-insights/gen1-reference-data-api#validation-and-error-handling).

    A Reference adatkezelés API egyenként dolgozza fel az egyes elemeket, és egy adott elemmel kapcsolatos hiba miatt nem akadályozza meg, hogy a többi sikeres befejezést hajtson végre. Ha például a kérelem 100 elemet tartalmaz, és egy elem hibát tartalmaz, akkor a 99-es elemek írása megtörténik, és a rendszer elutasítja az egyiket.

## <a name="prerequisites-and-setup"></a>Előfeltételek és beállítás

A mintakód fordítása és futtatása előtt végezze el a következő lépéseket:

1. Hozzon létre [egy 1. generációs Azure Time Series Insights](./time-series-insights-get-started.md
) környezetet.

1. [Hozzon létre egy hivatkozási adatkészletet](time-series-insights-add-reference-data-set.md) a környezetében. Használja a következő hivatkozási adatsémát:

   | Kulcs neve | Típus |
   | --- | --- |
   | uuid | Sztring |

1. Konfigurálja Azure Time Series Insights-környezetét Azure Active Directory a [hitelesítés és engedélyezés](time-series-insights-authentication-and-authorization.md)című témakörben leírtak szerint. Használja `http://localhost:8080/` **ÁTirányítási URI**-ként.

1. Telepítse a szükséges projekt-függőségeket.

1. Szerkessze az alábbi mintakód összes **#PLACEHOLDER #** helyére a megfelelő környezeti azonosítóval.

1. A `dotnet run` projekt gyökérkönyvtárában fut. Ha a rendszer kéri, használja a felhasználói profilját az Azure-ba való bejelentkezéshez.

## <a name="project-dependencies"></a>Projekt függőségei

Javasoljuk, hogy a Visual Studio és a **NETCore.app** legújabb verzióját használja:

* [Visual Studio 2019](https://visualstudio.microsoft.com/vs/) – 16.4.2 + verzió
* [NETCore.app](https://www.nuget.org/packages/Microsoft.NETCore.App/2.2.8) – 2.2.8 verziója

A mintakód két kötelező függőséggel rendelkezik:

* MSAL.NET [Microsoft. Identity. Client](https://www.nuget.org/packages/Microsoft.Identity.Client/) -4.7.1 csomag.
* [Newtonsoft.Json](https://www.nuget.org/packages/Newtonsoft.Json) -12.0.3 csomag.

Adja hozzá a csomagokat a [NuGet 2.12 +](https://www.nuget.org/)használatával:

* `dotnet add package Newtonsoft.Json --version 12.0.3`
* `dotnet add package Microsoft.Identity.Client --version 4.7.1`

Vagy

1. Fájl deklarálása `csharp-tsi-msal-ga-sample.csproj` :

    ```XML
    <Project Sdk="Microsoft.NET.Sdk">
      <PropertyGroup>
        <OutputType>Exe</OutputType>
        <TargetFramework>netcoreapp2.1</TargetFramework>
        <LangVersion>latest</LangVersion>
        <RootNamespace>csharp-tsi-msal-ga-sample</RootNamespace>
        <RunWorkingDirectory>$(MSBuildThisFileDirectory)</RunWorkingDirectory>
      </PropertyGroup>
      <ItemGroup>
        <PackageReference Include="Microsoft.Identity.Client" Version="4.7.1.0" Culture="neutral" PublicKeyToken="0a613f4dd989e8ae" />
        <PackageReference Include="Newtonsoft.Json" Version="12.0.3" />
      </ItemGroup>
    </Project>
    ```

1. Ez után futtassa a `dotnet restore` parancsot.

## <a name="c-sample-code"></a>C# mintakód

```csharp
// Copyright (c) Microsoft Corporation.  All rights reserved.

namespace CsharpTsiMsalGaSample
{
    using Microsoft.Identity.Client;
    using Newtonsoft.Json;
    using Newtonsoft.Json.Linq;
    using System;
    using System.IO;
    using System.Net.Http;
    using System.Text;
    using System.Threading.Tasks;

    public static class Program
    {
        /**
         * Review the product documentation for detailed configuration steps or skip ahead and configure your environment settings.
         *
         * https://docs.microsoft.com/azure/time-series-insights/time-series-insights-authentication-and-authorization
         */

        // Azure Time Series Insights environment configuration
        internal static string EnvironmentFqdn = "#PLACEHOLDER#.env.timeseries.azure.com";
        internal static string EnvironmentReferenceDataSetName = "#PLACEHOLDER#";

        // Azure Active Directory application configuration
        internal static string AadClientApplicationId = "#PLACEHOLDER#";
        internal static string[] AadScopes = new string[] { "https://api.timeseries.azure.com//user_impersonation" };
        internal static string AadRedirectUri = "http://localhost:8080/";
        internal static string AadTenantName = "#PLACEHOLDER#";
        internal static string AadAuthenticationAuthority = "https://login.microsoftonline.com/" + AadTenantName + ".onmicrosoft.com/oauth2/authorize?resource=https://api.timeseries.azure.com/";

        private static async Task<string> AcquireAccessTokenAsync()
        {
            if (AadClientApplicationId == "#PLACEHOLDER#" || AadScopes.Length == 0 || AadRedirectUri == "#PLACEHOLDER#" || AadTenantName.StartsWith("#PLACEHOLDER#"))
            {
                throw new Exception($"Use the link {"https://docs.microsoft.com/azure/time-series-insights/time-series-insights-get-started"} to update the values of 'AadClientApplicationId', 'AadScopes', 'AadRedirectUri', and 'AadAuthenticationAuthority'.");
            }

            /**
             * MSAL.NET configuration. Review the product documentation for more information about MSAL.NET authentication options.
             *
             * https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/
             */

            IPublicClientApplication app = PublicClientApplicationBuilder
                .Create(AadClientApplicationId)
                .WithRedirectUri(AadRedirectUri)
                .WithAuthority(AadAuthenticationAuthority)
                .Build();

            AuthenticationResult result = await app
                .AcquireTokenInteractive(AadScopes)
                .ExecuteAsync();

            Console.WriteLine("MSAL Authentication Token Acquired: {0}", result.AccessToken);
            Console.WriteLine("");
            return result.AccessToken;
        }

        // System.Net.HttpClient helper to wrap HTTP POST made to the GA Reference Data API
        private static async Task<HttpResponseMessage> AsyncHttpPostRequestHelper(HttpClient httpClient, string input)
        {
             if (EnvironmentFqdn.StartsWith("#PLACEHOLDER#") || EnvironmentReferenceDataSetName == "#PLACEHOLDER#")
             {
                throw new Exception($"Use the link {"https://docs.microsoft.com/azure/time-series-insights/time-series-insights-authentication-and-authorization"} to update the values of 'EnvironmentFqdn' and 'EnvironmentReferenceDataSetName'.");
             }

             Console.WriteLine("HTTP JSON Request Body: {0}", input);
             Console.WriteLine("");
             HttpContent requestBody = new StringContent(input, Encoding.UTF8, "application/json");

             Uri uri = new UriBuilder("https", EnvironmentFqdn)
             {
                Path = $"referencedatasets/{EnvironmentReferenceDataSetName}/$batch",
                Query = "api-version=2016-12-12"
             }.Uri;

             Console.WriteLine("Making HTTP POST to URI: {0}", uri);
             Console.WriteLine("");

             HttpResponseMessage response = await httpClient.PostAsync(uri, requestBody);
             if (response.IsSuccessStatusCode)
             {
                var jsonString = await response.Content.ReadAsStringAsync();
                var jsonStringTransferObject = JsonConvert.DeserializeObject<object>(jsonString);
                Console.WriteLine("HTTP JSON Response Body: {0}", jsonStringTransferObject);
                Console.WriteLine("");
                return response;
             }

             return null;
        }

        private static async Task TsiMsalGaSample()
        {
            Console.WriteLine("Beginning demo...");
            Console.WriteLine("");
            Console.WriteLine("The following samples assume a single Key Name (uuid) with String type...");
            Console.WriteLine("");

            string accessToken = await AcquireAccessTokenAsync();
            var httpClient = new HttpClient();
            httpClient.DefaultRequestHeaders.Add("Authorization", "Bearer " + accessToken);

            {
                // CREATE reference data
                Console.WriteLine("CREATE reference data example...");
                Console.WriteLine("");
                string createInput = @"
                    {
                        ""put"": [
                            {
                                ""uuid"": ""Fan1""
                            },
                            {
                                ""uuid"": ""Fan2"",
                                ""color"": ""White"",
                                ""floor"": 2
                            },
                            {
                                ""uuid"": ""Fan3"",
                                ""color"": ""Red"",
                                ""maxSpeed"": 5
                            }
                        ]
                    }";


                var createResponse = await AsyncHttpPostRequestHelper(httpClient, createInput);

                // READ reference data
                Console.WriteLine("READ reference data example...");
                Console.WriteLine("");
                string readInput = @"
                    {
                        ""get"": [
                            {
                                ""uuid"": ""Fan1""
                            },
                            {
                                ""uuid"": ""Fan2"",
                                ""color"": ""White"",
                                ""desc"": ""example""
                            },
                            {
                                ""uuid"": ""Fan3"",
                                ""color"": ""Red"",
                                ""maxSpeed"": 5
                            }
                        ]
                    }";

                var readResponse = await AsyncHttpPostRequestHelper(httpClient, readInput);

                // UPDATE reference data
                Console.WriteLine("UPDATE reference data example...");
                Console.WriteLine("");
                string updateInput = @"
                    {
                        ""patch"": [
                            {
                                ""uuid"": ""Fan1"",
                                ""color"": ""Red""
                            },
                            {
                                ""uuid"": ""Fan2"",
                                ""color"": ""Orange""
                            },
                            {
                                ""uuid"": ""Fan3"",
                                ""desc"": ""Example""
                            }
                        ]
                    }";

                var inputResponse = await AsyncHttpPostRequestHelper(httpClient, updateInput);

                // DELETE reference data
                Console.WriteLine("DELETE reference data example...");
                Console.WriteLine("");
                string deleteInput = @"
                    {
                        ""delete"": [
                            {
                                ""uuid"": ""Fan1""
                            },
                            {
                                ""uuid"": ""Fan2"",
                                ""color"": ""Orange""
                            },
                            {
                                ""uuid"": ""Fan2"",
                                ""desc"": ""Blue""
                            }
                        ]
                    }";

                var deleteResponse = await AsyncHttpPostRequestHelper(httpClient, deleteInput);
            }
        }

        internal static void Main(string[] args)
        {
            Task.Run(async () => await TsiMsalGaSample()).Wait();
        }
    }
}
```

## <a name="next-steps"></a>Következő lépések

* Olvassa el az 1. generációs [referenciát adatkezelés API](/rest/api/time-series-insights/gen1-reference-data-api) -dokumentációt.