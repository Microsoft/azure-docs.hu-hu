---
title: Webes API-kat meghívó webes API konfigurálása | Azure
titleSuffix: Microsoft identity platform
description: Megtudhatja, hogyan hozhat létre webes API-t meghívó webes API-kat (az alkalmazás kódjának konfigurációja)
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/26/2020
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 5072ae58d3a9412237e70a9bc98970296ce1e1fa
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "101686580"
---
# <a name="a-web-api-that-calls-web-apis-code-configuration"></a>Webes API-kat meghívó webes API: kód konfigurálása

Miután regisztrálta a webes API-t, beállíthatja az alkalmazás kódját.

A webes API konfigurálásához használt kód, hogy az alsóbb rétegbeli webes API-kat a webes API-k elleni védelemhez használt kód felett hozza létre. További információ [: Protected web API: app Configuration](scenario-protected-web-api-app-configuration.md).

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

## <a name="microsoftidentityweb"></a>Microsoft. Identity. Web

A Microsoft azt javasolja, hogy a [Microsoft. Identity. Web](https://www.nuget.org/packages/Microsoft.Identity.Web) NuGet csomagot használja egy ASP.net Core védett API-t használó, alsóbb rétegbeli webes API-k kifejlesztéséhez. Lásd [: védett webes API: kód konfigurálása | Microsoft. Identity. Web](scenario-protected-web-api-app-configuration.md#microsoftidentityweb) az adott könyvtár gyors megjelenítéséhez egy webes API kontextusában.

## <a name="client-secrets-or-client-certificates"></a>Ügyfél-titkok vagy Ügyféltanúsítványok

Mivel a webes API most egy alsóbb rétegbeli webes API-t hív meg, adjon meg egy ügyfél titkot vagy ügyféltanúsítványt a fájl *appsettings.js* . Hozzáadhat egy szakaszt is, amely meghatározza a következőket:

- Az alárendelt webes API URL-címe
- Az API meghívásához szükséges hatókörök

A következő példában a `GraphBeta` szakasz ezeket a beállításokat adja meg.

```JSON
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "ClientId": "[Client_id-of-web-api-eg-2ec40e65-ba09-4853-bcde-bcb60029e596]",
    "TenantId": "common"

   // To call an API
   "ClientSecret": "[Copy the client secret added to the app from the Azure portal]",
   "ClientCertificates": [
  ]
 },
 "GraphBeta": {
    "BaseUrl": "https://graph.microsoft.com/beta",
    "Scopes": "user.read"
    }
}
```

Az ügyfél titkos kulcsa helyett megadhat egy ügyféltanúsítványt. A következő kódrészlet a Azure Key Vaultban tárolt tanúsítvány használatát mutatja be.

```JSON
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "ClientId": "[Client_id-of-web-api-eg-2ec40e65-ba09-4853-bcde-bcb60029e596]",
    "TenantId": "common"

   // To call an API
   "ClientCertificates": [
      {
        "SourceType": "KeyVault",
        "KeyVaultUrl": "https://msidentitywebsamples.vault.azure.net",
        "KeyVaultCertificateName": "MicrosoftIdentitySamplesCert"
      }
   ]
  },
  "GraphBeta": {
    "BaseUrl": "https://graph.microsoft.com/beta",
    "Scopes": "user.read"
  }
}
```

A Microsoft. Identity. Web számos módszert kínál a tanúsítványok leírására, a konfiguráció vagy a kód alapján. Részletekért lásd: [Microsoft. Identity. Web wiki – tanúsítványok használata](https://github.com/AzureAD/microsoft-identity-web/wiki/Using-certificates) a githubon.

## <a name="startupcs"></a>Startup.cs

A webes API-nak meg kell adnia egy jogkivonatot az alsóbb rétegbeli API-hoz. Ezt úgy adhatja meg, hogy a sort a következő után adja hozzá `.EnableTokenAcquisitionToCallDownstreamApi()` `.AddMicrosoftIdentityWebApi(Configuration)` . Ez a sor teszi elérhetővé a `ITokenAcquisition` szolgáltatást, amelyet a vezérlő/lapok műveleteihez használhat. Azonban ahogy a következő két felsorolásjelen is látható, még egyszerűbben is megteheti. Ki kell választania egy jogkivonat-gyorsítótár implementációját is, például `.AddInMemoryTokenCaches()` az *indításkor. cs*:

```csharp
using Microsoft.Identity.Web;

public class Startup
{
  // ...
  public void ConfigureServices(IServiceCollection services)
  {
  // ...
  services.AddAuthentication(JwtBearerDefaults.AuthenticationScheme)
          .AddMicrosoftIdentityWebApi(Configuration, Configuration.GetSection("AzureAd"))
            .EnableTokenAcquisitionToCallDownstreamApi()
            .AddInMemoryTokenCaches();
   // ...
  }
  // ...
}
```

Ha nem szeretné magát a tokent beszerezni, a *Microsoft. Identity. Web* két mechanizmust biztosít az ALÁRENDELT webes API-k más API-ból való meghívásához. A választott lehetőség attól függ, hogy Microsoft Graph vagy egy másik API-t szeretne meghívni.

### <a name="option-1-call-microsoft-graph"></a>1. lehetőség: hívás Microsoft Graph

Ha meg szeretné hívni Microsoft Graph, a Microsoft. Identity. Web lehetővé teszi, hogy közvetlenül használja a `GraphServiceClient` (az Microsoft Graph SDK által közzétett) API-műveleteket. Microsoft Graph közzététele:

1. Adja hozzá a [Microsoft. Identity. Web. MicrosoftGraph](https://www.nuget.org/packages/Microsoft.Identity.Web.MicrosoftGraph) NuGet-csomagot a projekthez.
1. Adja `.AddMicrosoftGraph()` hozzá `.EnableTokenAcquisitionToCallDownstreamApi()` a következőt a *Startup. cs* fájlban. `.AddMicrosoftGraph()` több felülbírálással rendelkezik. A konfigurációs szakaszt paraméterként tartalmazó felülbírálás használatával a kód a következőképpen fog megjelenni:

```csharp
using Microsoft.Identity.Web;

public class Startup
{
  // ...
  public void ConfigureServices(IServiceCollection services)
  {
  // ...
  services.AddAuthentication(JwtBearerDefaults.AuthenticationScheme)
          .AddMicrosoftIdentityWebApi(Configuration, Configuration.GetSection("AzureAd"))
            .EnableTokenAcquisitionToCallDownstreamApi()
               .AddMicrosoftGraph(Configuration.GetSection("GraphBeta"))
            .AddInMemoryTokenCaches();
   // ...
  }
  // ...
}
```

### <a name="option-2-call-a-downstream-web-api-other-than-microsoft-graph"></a>2. lehetőség: a Microsoft Graphtól eltérő alárendelt webes API meghívása

A Microsoft Graph, a *Microsoft. Identity. Web* által biztosított alsóbb RÉTEGbeli API meghívásához `.AddDownstreamWebApi()` , amely tokeneket kér le, és meghívja az alárendelt webes API-t.

```csharp
using Microsoft.Identity.Web;

public class Startup
{
  // ...
  public void ConfigureServices(IServiceCollection services)
  {
  // ...
  services.AddAuthentication(JwtBearerDefaults.AuthenticationScheme)
          .AddMicrosoftIdentityWebApi(Configuration, "AzureAd")
            .EnableTokenAcquisitionToCallDownstreamApi()
               .AddDownstreamWebApi("MyApi", Configuration.GetSection("GraphBeta"))
            .AddInMemoryTokenCaches();
   // ...
  }
  // ...
}
```

Akárcsak a Web Apps esetében, különböző jogkivonat-gyorsítótár-implementációkat is választhat. Részletekért lásd: a [Microsoft Identity web-token cache szerializálás](https://aka.ms/ms-id-web/token-cache-serialization) a githubon.

Az alábbi ábrán a *Microsoft. Identity. Web* különböző lehetőségei láthatók, valamint a *Startup. cs* fájlra gyakorolt hatásuk:

:::image type="content" source="media/scenarios/microsoft-identity-web-startup-cs.svg" alt-text="A webes API-k meghívásához és a jogkivonat-gyorsítótár implementációjának megadásához szükséges, a szolgáltatás konfigurációs beállításait bemutató diagram letiltása":::

> [!NOTE]
> A példákban szereplő kódrészletek teljes megértéséhez Ismerkedjen meg [ASP.net Core alapjaival](/aspnet/core/fundamentals), és különösen a [függőségek befecskendezésével](/aspnet/core/fundamentals/dependency-injection) és [lehetőségeivel](/aspnet/core/fundamentals/configuration/options).

# <a name="java"></a>[Java](#tab/java)

A beérkező (OBO) folyamat az alárendelt webes API meghívására szolgáló token beszerzésére szolgál. Ebben a folyamatban a webes API egy tulajdonosi jogkivonatot kap a felhasználó által delegált engedélyekkel az ügyfélalkalmazás számára, majd ezt a tokent egy másik hozzáférési tokenre cseréli le az alárendelt webes API meghívásához.

Az alábbi kód a rugós biztonsági keretrendszert használja `SecurityContextHolder` a webes API-ban az ellenőrzött tulajdonosi jogkivonat beszerzéséhez. Ezután a MSAL Java Library használatával szerzi be az alsóbb rétegbeli API-tokent a `acquireToken` hívásával `OnBehalfOfParameters` . A MSAL gyorsítótárazza a jogkivonatot, így az API-hoz `acquireTokenSilently` való későbbi hívások a gyorsítótárazott token beszerzésére használhatják.

```Java
@Component
class MsalAuthHelper {

    @Value("${security.oauth2.client.authority}")
    private String authority;

    @Value("${security.oauth2.client.client-id}")
    private String clientId;

    @Value("${security.oauth2.client.client-secret}")
    private String secret;

    @Autowired
    CacheManager cacheManager;

    private String getAuthToken(){
        String res = null;
        Authentication authentication = SecurityContextHolder.getContext().getAuthentication();

        if(authentication != null){
            res = ((OAuth2AuthenticationDetails) authentication.getDetails()).getTokenValue();
        }
        return res;
    }

    String getOboToken(String scope) throws MalformedURLException {
        String authToken = getAuthToken();

        ConfidentialClientApplication application =
                ConfidentialClientApplication.builder(clientId, ClientCredentialFactory.create(secret))
                        .authority(authority).build();

        String cacheKey = Hashing.sha256()
                .hashString(authToken, StandardCharsets.UTF_8).toString();

        String cachedTokens = cacheManager.getCache("tokens").get(cacheKey, String.class);
        if(cachedTokens != null){
            application.tokenCache().deserialize(cachedTokens);
        }

        IAuthenticationResult auth;
        SilentParameters silentParameters =
                SilentParameters.builder(Collections.singleton(scope))
                        .build();
        auth = application.acquireTokenSilently(silentParameters).join();

        if (auth == null){
            OnBehalfOfParameters parameters =
                    OnBehalfOfParameters.builder(Collections.singleton(scope),
                            new UserAssertion(authToken))
                            .build();

            auth = application.acquireToken(parameters).join();
        }

        cacheManager.getCache("tokens").put(cacheKey, application.tokenCache().serialize());

        return auth.accessToken();
    }
}
```

# <a name="python"></a>[Python](#tab/python)

A beérkező (OBO) folyamat az alárendelt webes API meghívására szolgáló token beszerzésére szolgál. Ebben a folyamatban a webes API egy tulajdonosi jogkivonatot kap a felhasználó által delegált engedélyekkel az ügyfélalkalmazás számára, majd ezt a tokent egy másik hozzáférési tokenre cseréli le az alárendelt webes API meghívásához.

Egy Python webes API-nak bizonyos middleware-t kell használnia az ügyféltől kapott tulajdonosi jogkivonat ellenőrzéséhez. A webes API ezt követően a metódus meghívásával lekérheti az alsóbb rétegbeli API hozzáférési tokenjét a MSAL Python Library használatával [`acquire_token_on_behalf_of`](https://msal-python.readthedocs.io/en/latest/?badge=latest#msal.ConfidentialClientApplication.acquire_token_on_behalf_of) . Az API használatára példaként tekintse [meg a Microsoft-Authentication-Library-for-Python a githubon történő tesztelési kódját](https://github.com/AzureAD/microsoft-authentication-library-for-python/blob/1.2.0/tests/test_e2e.py#L429-L472). Tekintse meg a 53-es [probléma](https://github.com/AzureAD/microsoft-authentication-library-for-python/issues/53) ugyanazon tárházban való megvitatását is, amely megkerüli a középső rétegbeli alkalmazások szükségességét.

Megtekintheti az OBO flow megvalósításának példáját is az [MS-Identity-Python-on-nevében](https://github.com/Azure-Samples/ms-identity-python-on-behalf-of) .

---

Megtekintheti az OBO flow megvalósításának példáját is [Node.js és Azure functions](https://github.com/Azure-Samples/ms-identity-nodejs-webapi-onbehalfof-azurefunctions/blob/master/Function/MyHttpTrigger/index.js#L61).

## <a name="protocol"></a>Protokoll

További információ az OBO protokollról: [Microsoft Identity platform és OAuth 2,0 on-Half-of flow](./v2-oauth2-on-behalf-of-flow.md).

## <a name="next-steps"></a>Következő lépések

Ebben a forgatókönyvben a következő cikkre léphet, amely [az alkalmazáshoz tartozó jogkivonatot szerzi](scenario-web-api-call-api-acquire-token.md)be.
