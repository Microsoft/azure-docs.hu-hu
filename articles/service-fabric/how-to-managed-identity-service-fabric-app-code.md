---
title: Felügyelt identitás használata alkalmazással
description: Felügyelt identitások használata az Azure Service Fabric alkalmazás kódjában az Azure-szolgáltatások eléréséhez.
ms.topic: article
ms.date: 10/09/2019
ms.openlocfilehash: e26a29020f26583f7e4aa16434c7e8647ba9a5a3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98871061"
---
# <a name="how-to-leverage-a-service-fabric-applications-managed-identity-to-access-azure-services"></a>Service Fabric alkalmazás felügyelt identitásának kihasználása az Azure-szolgáltatások eléréséhez

Service Fabric alkalmazások a felügyelt identitásokat használhatják a Azure Active Directory-alapú hitelesítést támogató egyéb Azure-erőforrások eléréséhez. Egy alkalmazás olyan [hozzáférési jogkivonatot](../active-directory/develop/developer-glossary.md#access-token) szerezhet be, amely az identitását jelképezi, amely lehet rendszer által hozzárendelt vagy felhasználó által hozzárendelt, és "tulajdonos" tokenként használva hitelesíti magát egy másik szolgáltatásnak – más néven [védett erőforrás-kiszolgálónak](../active-directory/develop/developer-glossary.md#resource-server)–. A jogkivonat a Service Fabric alkalmazáshoz rendelt identitást jelöli, és csak az adott identitást használó Azure-erőforrások (például SF-alkalmazások) számára lesz kibocsátva. Tekintse át a felügyelt [identitások áttekintő](../active-directory/managed-identities-azure-resources/overview.md) dokumentációját a felügyelt identitások részletes leírását, valamint a rendszer által hozzárendelt és a felhasználó által hozzárendelt identitások megkülönböztetését. Ebben [a cikkben](../active-directory/develop/developer-glossary.md#client-application) a felügyelt identitás-kompatibilis Service Fabric alkalmazásra fogunk hivatkozni.

Tekintse meg a társ-minta alkalmazást, amely a rendszerhez rendelt és felhasználó által hozzárendelt [Service Fabric alkalmazás felügyelt identitások](https://github.com/Azure-Samples/service-fabric-managed-identity) használatát mutatja be Reliable Services és tárolók használatával.

> [!IMPORTANT]
> A felügyelt identitás az erőforrást tartalmazó előfizetéshez társított Azure AD-bérlőben az Azure-erőforrás és egy egyszerű szolgáltatásnév közötti társítást jelöli. A Service Fabric kontextusában a felügyelt identitások csak az Azure-erőforrásként üzembe helyezett alkalmazások esetében támogatottak. 

> [!IMPORTANT]
> A Service Fabric alkalmazás felügyelt identitásának használata előtt az ügyfélalkalmazás számára hozzáférést kell biztosítani a védett erőforráshoz. Tekintse meg az Azure [ad-hitelesítést támogató Azure-szolgáltatások](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-managed-identities-for-azure-resources) listáját a támogatás megkereséséhez, majd a megfelelő szolgáltatás dokumentációjában, amely alapján az identitás hozzáférést biztosít a fontos erőforrásokhoz. 
 

## <a name="leverage-a-managed-identity-using-azureidentity"></a>Felügyelt identitás kihasználása az Azure. Identity használatával

Az Azure Identity SDK mostantól támogatja a Service Fabric. Az Azure. Identity használatával a kód írásával Service Fabric alkalmazás által felügyelt identitások könnyebben használhatók, mert kezeli a jogkivonatok beolvasását, a gyorsítótárazási tokeneket és a kiszolgáló hitelesítését. A legtöbb Azure-erőforráshoz való hozzáférés során a jogkivonat fogalma rejtve marad.

A Service Fabric támogatás a következő verziókban érhető el ezekhez a nyelvekhez: 
- [C# a verzió 1.3.0](https://www.nuget.org/packages/Azure.Identity). Lásd: [C#-minta](https://github.com/Azure-Samples/service-fabric-managed-identity).
- [Python a 1.5.0-es verzióban](https://pypi.org/project/azure-identity/). Tekintse meg a [Python-mintát](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/identity/azure-identity/tests/managed-identity-live/service-fabric/service_fabric.md).
- [Java a 1.2.0 verzióban](/java/api/overview/azure/identity-readme).

C# minta a hitelesítő adatok inicializálásához és a hitelesítő adatok használatával Azure Key Vault titkos kód beolvasásához:

```csharp
using Azure.Identity;
using Azure.Security.KeyVault.Secrets;

namespace MyMIService
{
    internal sealed class MyMIService : StatelessService
    {
        protected override async Task RunAsync(CancellationToken cancellationToken)
        {
            try
            {
                // Load the service fabric application managed identity assigned to the service
                ManagedIdentityCredential creds = new ManagedIdentityCredential();

                // Create a client to keyvault using that identity
                SecretClient client = new SecretClient(new Uri("https://mykv.vault.azure.net/"), creds);

                // Fetch a secret
                KeyVaultSecret secret = (await client.GetSecretAsync("mysecret", cancellationToken: cancellationToken)).Value;
            }
            catch (CredentialUnavailableException e)
            {
                // Handle errors with loading the Managed Identity
            }
            catch (RequestFailedException)
            {
                // Handle errors with fetching the secret
            }
            catch (Exception e)
            {
                // Handle generic errors
            }
        }
    }
}

```

## <a name="acquiring-an-access-token-using-rest-api"></a>Hozzáférési jogkivonat beszerzése REST API használatával
A felügyelt identitáshoz engedélyezett fürtökön a Service Fabric futtatókörnyezet elérhetővé teszi a localhost végpontot, melyet az alkalmazások használhatnak a hozzáférési tokenek beszerzéséhez. A végpont a fürt minden csomópontján elérhető, és elérhető az adott csomóponton lévő összes entitás számára. A jogosult hívók a végpont meghívásával és egy hitelesítési kód bemutatásával kaphatnak hozzáférési jogkivonatokat. a kódot a Service Fabric futtatókörnyezet hozza létre az egyes különböző szervizcsomag-csomagok aktiválásakor, és a szolgáltatási kód csomagjait futtató folyamat élettartamára van kötve.

A felügyelt identitás-kompatibilis Service Fabric szolgáltatás környezetét a következő változók szerint kell kiszámítani:
- "IDENTITY_ENDPOINT": a szolgáltatás felügyelt identitásának megfelelő localhost végpont
- "IDENTITY_HEADER": egy egyedi hitelesítési kód, amely az aktuális csomóponton lévő szolgáltatást jelképezi
- "IDENTITY_SERVER_THUMBPRINT": a Service Fabric felügyelt identitás-kiszolgáló ujjlenyomata

> [!IMPORTANT]
> Az alkalmazás kódjának figyelembe kell vennie a "IDENTITY_HEADER" környezeti változó értékét bizalmas adatokként – nem szabad naplózni vagy egyéb módon terjeszteni. A hitelesítési kód nem rendelkezik a helyi csomóponton kívüli értékkel, vagy a szolgáltatást futtató folyamat megszakadt, de a Service Fabric szolgáltatás identitását jelöli, ezért a hozzáférési jogkivonattal azonos óvintézkedésekkel kell kezelni.

A jogkivonat beszerzéséhez az ügyfél a következő lépéseket hajtja végre:
- egy URI-t képez a felügyelt identitás végpontjának (IDENTITY_ENDPOINT érték) összefűzésével a tokenhez szükséges API-verzióval és erőforrással (célközönséggel).
- http (s) kérelem létrehozása a megadott URI-hoz
- a megfelelő kiszolgálói tanúsítvány érvényesítési logikájának beadása
- hozzáadja a hitelesítési kódot (IDENTITY_HEADER értéket) a kérelem fejlécéhez.
- elküldi a kérést

A sikeres válasz tartalmazni fog egy JSON-adattartalmat, amely az eredményül kapott hozzáférési jogkivonatot, valamint az azt leíró metaadatokat tartalmazza. A sikertelen válasz a hiba magyarázatát is tartalmazni fogja. A hibák kezelésével kapcsolatos további részletekért lásd alább.

A hozzáférési jogkivonatokat a rendszer a különböző szinteken (csomópont, fürt, erőforrás-szolgáltatói szolgáltatás) Service Fabric gyorsítótárazza, így a sikeres válasz nem feltétlenül jelenti azt, hogy a tokent közvetlenül a felhasználói alkalmazás kérelmére adta ki. A rendszer gyorsítótárazza a tokeneket, hogy az élettartamuk kevesebb legyen, és hogy egy alkalmazás érvényes jogkivonatot kapjon. Azt javasoljuk, hogy az alkalmazás kódja gyorsítótárazza az általa beszerzett hozzáférési jogkivonatokat. a gyorsítótárazási kulcsnak tartalmaznia kell a célközönséget (a). 

Példa a kérelemre:
```http
GET 'https://localhost:2377/metadata/identity/oauth2/token?api-version=2019-07-01-preview&resource=https://vault.azure.net/' HTTP/1.1 Secret: 912e4af7-77ba-4fa5-a737-56c8e3ace132
```
ahol:

| Elem | Leírás |
| ------- | ----------- |
| `GET` | A HTTP-művelet, amely azt jelzi, hogy a végpontról kívánja beolvasni az adatait. Ebben az esetben egy OAuth hozzáférési jogkivonat. | 
| `https://localhost:2377/metadata/identity/oauth2/token` | A felügyelt identitás végpontja Service Fabric alkalmazásokhoz, amelyeket a IDENTITY_ENDPOINT környezeti változón keresztül adnak meg. |
| `api-version` | Egy lekérdezési karakterlánc paraméter, amely megadja a felügyelt Identity token szolgáltatás API-verzióját; jelenleg az egyetlen elfogadott érték a `2019-07-01-preview` , és a változás változhat. |
| `resource` | Egy lekérdezési karakterlánc paraméter, amely a cél erőforrás alkalmazás-azonosító URI azonosítóját jelzi. Ez a `aud` kiállított jogkivonat (célközönség) jogcímeként jelenik meg. Ez a példa jogkivonatot kér a Azure Key Vault eléréséhez, amelynek az alkalmazás-azonosító URI-ja https: \/ /Vault.Azure.net/. |
| `Secret` | Egy HTTP-kérelem fejlécének mezője, amelyet a Service Fabric felügyelt Identity jogkivonat szolgáltatás igényel Service Fabric-szolgáltatásokhoz a hívó hitelesítéséhez. Ezt az értéket az SF Runtime IDENTITY_HEADER környezeti változón keresztül kapja meg. |


Példa a válaszra:
```json
HTTP/1.1 200 OK
Content-Type: application/json
{
    "token_type":  "Bearer",
    "access_token":  "eyJ0eXAiO...",
    "expires_on":  1565244611,
    "resource":  "https://vault.azure.net/"
}
```
ahol:

| Elem | Leírás |
| ------- | ----------- |
| `token_type` | A jogkivonat típusa; Ebben az esetben egy "tulajdonos" hozzáférési token, ami azt jelenti, hogy a token tulajdonosa ("birtokosa") a jogkivonat tárgya. |
| `access_token` | A kért hozzáférési jogkivonat. Biztonságos REST API hívásakor a jogkivonat a `Authorization` kérelem fejléc mezőjébe ágyazva "tulajdonos" tokenként van beágyazva, ami lehetővé teszi, hogy az API hitelesítse a hívót. | 
| `expires_on` | A hozzáférési token lejárati időbélyegzője; a (z) "1970-01-01T0:0: 0Z UTC" számú másodpercet jelöli, és megfelel a jogkivonat `exp` jogcímenek. Ebben az esetben a jogkivonat a 2019-08-08T06:10:11 + 00:00 (az RFC 3339-ben) lejár|
| `resource` | Az erőforrás, amelyhez a hozzáférési jogkivonat ki lett állítva, a `resource` kérelem lekérdezési karakterlánc paraméterén keresztül lett megadva; a jogkivonat "AUD" jogcímének felel meg. |


## <a name="acquiring-an-access-token-using-c"></a>Hozzáférési jogkivonat beszerzése a C használatával #
A fentiek a C# nyelvben:

```C#
namespace Azure.ServiceFabric.ManagedIdentity.Samples
{
    using System;
    using System.Net.Http;
    using System.Text;
    using System.Threading;
    using System.Threading.Tasks;
    using System.Web;
    using Newtonsoft.Json;

    /// <summary>
    /// Type representing the response of the SF Managed Identity endpoint for token acquisition requests.
    /// </summary>
    [JsonObject]
    public sealed class ManagedIdentityTokenResponse
    {
        [JsonProperty(Required = Required.Always, PropertyName = "token_type")]
        public string TokenType { get; set; }

        [JsonProperty(Required = Required.Always, PropertyName = "access_token")]
        public string AccessToken { get; set; }

        [JsonProperty(PropertyName = "expires_on")]
        public string ExpiresOn { get; set; }

        [JsonProperty(PropertyName = "resource")]
        public string Resource { get; set; }
    }

    /// <summary>
    /// Sample class demonstrating access token acquisition using Managed Identity.
    /// </summary>
    public sealed class AccessTokenAcquirer
    {
        /// <summary>
        /// Acquire an access token.
        /// </summary>
        /// <returns>Access token</returns>
        public static async Task<string> AcquireAccessTokenAsync()
        {
            var managedIdentityEndpoint = Environment.GetEnvironmentVariable("IDENTITY_ENDPOINT");
            var managedIdentityAuthenticationCode = Environment.GetEnvironmentVariable("IDENTITY_HEADER");
            var managedIdentityServerThumbprint = Environment.GetEnvironmentVariable("IDENTITY_SERVER_THUMBPRINT");
            // Latest api version, 2019-07-01-preview is still supported.
            var managedIdentityApiVersion = Environment.GetEnvironmentVariable("IDENTITY_API_VERSION");
            var managedIdentityAuthenticationHeader = "secret";
            var resource = "https://management.azure.com/";

            var requestUri = $"{managedIdentityEndpoint}?api-version={managedIdentityApiVersion}&resource={HttpUtility.UrlEncode(resource)}";

            var requestMessage = new HttpRequestMessage(HttpMethod.Get, requestUri);
            requestMessage.Headers.Add(managedIdentityAuthenticationHeader, managedIdentityAuthenticationCode);
            
            var handler = new HttpClientHandler();
            handler.ServerCertificateCustomValidationCallback = (httpRequestMessage, cert, certChain, policyErrors) =>
            {
                // Do any additional validation here
                if (policyErrors == SslPolicyErrors.None)
                {
                    return true;
                }
                return 0 == string.Compare(cert.GetCertHashString(), managedIdentityServerThumbprint, StringComparison.OrdinalIgnoreCase);
            };

            try
            {
                var response = await new HttpClient(handler).SendAsync(requestMessage)
                    .ConfigureAwait(false);

                response.EnsureSuccessStatusCode();

                var tokenResponseString = await response.Content.ReadAsStringAsync()
                    .ConfigureAwait(false);

                var tokenResponseObject = JsonConvert.DeserializeObject<ManagedIdentityTokenResponse>(tokenResponseString);

                return tokenResponseObject.AccessToken;
            }
            catch (Exception ex)
            {
                string errorText = String.Format("{0} \n\n{1}", ex.Message, ex.InnerException != null ? ex.InnerException.Message : "Acquire token failed");

                Console.WriteLine(errorText);
            }

            return String.Empty;
        }
    } // class AccessTokenAcquirer
} // namespace Azure.ServiceFabric.ManagedIdentity.Samples
```
## <a name="accessing-key-vault-from-a-service-fabric-application-using-managed-identity"></a>Key Vault elérése Service Fabric alkalmazásban felügyelt identitás használatával
Ez a minta a fentiekben a felügyelt identitás használatával mutatja be egy Key Vault tárolt titok elérését.

```C#
        /// <summary>
        /// Probe the specified secret, displaying metadata on success.  
        /// </summary>
        /// <param name="vault">vault name</param>
        /// <param name="secret">secret name</param>
        /// <param name="version">secret version id</param>
        /// <returns></returns>
        public async Task<string> ProbeSecretAsync(string vault, string secret, string version)
        {
            // initialize a KeyVault client with a managed identity-based authentication callback
            var kvClient = new Microsoft.Azure.KeyVault.KeyVaultClient(new Microsoft.Azure.KeyVault.KeyVaultClient.AuthenticationCallback((a, r, s) => { return AuthenticationCallbackAsync(a, r, s); }));

            Log(LogLevel.Info, $"\nRunning with configuration: \n\tobserved vault: {config.VaultName}\n\tobserved secret: {config.SecretName}\n\tMI endpoint: {config.ManagedIdentityEndpoint}\n\tMI auth code: {config.ManagedIdentityAuthenticationCode}\n\tMI auth header: {config.ManagedIdentityAuthenticationHeader}");
            string response = String.Empty;

            Log(LogLevel.Info, "\n== {DateTime.UtcNow.ToString()}: Probing secret...");
            try
            {
                var secretResponse = await kvClient.GetSecretWithHttpMessagesAsync(vault, secret, version)
                    .ConfigureAwait(false);

                if (secretResponse.Response.IsSuccessStatusCode)
                {
                    // use the secret: secretValue.Body.Value;
                    response = String.Format($"Successfully probed secret '{secret}' in vault '{vault}': {PrintSecretBundleMetadata(secretResponse.Body)}");
                }
                else
                {
                    response = String.Format($"Non-critical error encountered retrieving secret '{secret}' in vault '{vault}': {secretResponse.Response.ReasonPhrase} ({secretResponse.Response.StatusCode})");
                }
            }
            catch (Microsoft.Rest.ValidationException ve)
            {
                response = String.Format($"encountered REST validation exception 0x{ve.HResult.ToString("X")} trying to access '{secret}' in vault '{vault}' from {ve.Source}: {ve.Message}");
            }
            catch (KeyVaultErrorException kvee)
            {
                response = String.Format($"encountered KeyVault exception 0x{kvee.HResult.ToString("X")} trying to access '{secret}' in vault '{vault}': {kvee.Response.ReasonPhrase} ({kvee.Response.StatusCode})");
            }
            catch (Exception ex)
            {
                // handle generic errors here
                response = String.Format($"encountered exception 0x{ex.HResult.ToString("X")} trying to access '{secret}' in vault '{vault}': {ex.Message}");
            }

            Log(LogLevel.Info, response);

            return response;
        }

        /// <summary>
        /// KV authentication callback, using the application's managed identity.
        /// </summary>
        /// <param name="authority">The expected issuer of the access token, from the KV authorization challenge.</param>
        /// <param name="resource">The expected audience of the access token, from the KV authorization challenge.</param>
        /// <param name="scope">The expected scope of the access token; not currently used.</param>
        /// <returns>Access token</returns>
        public async Task<string> AuthenticationCallbackAsync(string authority, string resource, string scope)
        {
            Log(LogLevel.Verbose, $"authentication callback invoked with: auth: {authority}, resource: {resource}, scope: {scope}");
            var encodedResource = HttpUtility.UrlEncode(resource);

            // This sample does not illustrate the caching of the access token, which the user application is expected to do.
            // For a given service, the caching key should be the (encoded) resource uri. The token should be cached for a period
            // of time at most equal to its remaining validity. The 'expires_on' field of the token response object represents
            // the number of seconds from Unix time when the token will expire. You may cache the token if it will be valid for at
            // least another short interval (1-10s). If its expiration will occur shortly, don't cache but still return it to the 
            // caller. The MI endpoint will not return an expired token.
            // Sample caching code:
            //
            // ManagedIdentityTokenResponse tokenResponse;
            // if (responseCache.TryGetCachedItem(encodedResource, out tokenResponse))
            // {
            //     Log(LogLevel.Verbose, $"cache hit for key '{encodedResource}'");
            //
            //     return tokenResponse.AccessToken;
            // }
            //
            // Log(LogLevel.Verbose, $"cache miss for key '{encodedResource}'");
            //
            // where the response cache is left as an exercise for the reader. MemoryCache is a good option, albeit not yet available on .net core.

            var requestUri = $"{config.ManagedIdentityEndpoint}?api-version={config.ManagedIdentityApiVersion}&resource={encodedResource}";
            Log(LogLevel.Verbose, $"request uri: {requestUri}");

            var requestMessage = new HttpRequestMessage(HttpMethod.Get, requestUri);
            requestMessage.Headers.Add(config.ManagedIdentityAuthenticationHeader, config.ManagedIdentityAuthenticationCode);
            Log(LogLevel.Verbose, $"added header '{config.ManagedIdentityAuthenticationHeader}': '{config.ManagedIdentityAuthenticationCode}'");

            var response = await httpClient.SendAsync(requestMessage)
                .ConfigureAwait(false);
            Log(LogLevel.Verbose, $"response status: success: {response.IsSuccessStatusCode}, status: {response.StatusCode}");

            response.EnsureSuccessStatusCode();

            var tokenResponseString = await response.Content.ReadAsStringAsync()
                .ConfigureAwait(false);

            var tokenResponse = JsonConvert.DeserializeObject<ManagedIdentityTokenResponse>(tokenResponseString);
            Log(LogLevel.Verbose, "deserialized token response; returning access code..");

            // Sample caching code (continuation):
            // var expiration = DateTimeOffset.FromUnixTimeSeconds(Int32.Parse(tokenResponse.ExpiresOn));
            // if (expiration > DateTimeOffset.UtcNow.AddSeconds(5.0))
            //    responseCache.AddOrUpdate(encodedResource, tokenResponse, expiration);

            return tokenResponse.AccessToken;
        }

        private string PrintSecretBundleMetadata(SecretBundle bundle)
        {
            StringBuilder strBuilder = new StringBuilder();

            strBuilder.AppendFormat($"\n\tid: {bundle.Id}\n");
            strBuilder.AppendFormat($"\tcontent type: {bundle.ContentType}\n");
            strBuilder.AppendFormat($"\tmanaged: {bundle.Managed}\n");
            strBuilder.AppendFormat($"\tattributes:\n");
            strBuilder.AppendFormat($"\t\tenabled: {bundle.Attributes.Enabled}\n");
            strBuilder.AppendFormat($"\t\tnbf: {bundle.Attributes.NotBefore}\n");
            strBuilder.AppendFormat($"\t\texp: {bundle.Attributes.Expires}\n");
            strBuilder.AppendFormat($"\t\tcreated: {bundle.Attributes.Created}\n");
            strBuilder.AppendFormat($"\t\tupdated: {bundle.Attributes.Updated}\n");
            strBuilder.AppendFormat($"\t\trecoveryLevel: {bundle.Attributes.RecoveryLevel}\n");

            return strBuilder.ToString();
        }

        private enum LogLevel
        {
            Info,
            Verbose
        };

        private void Log(LogLevel level, string message)
        {
            if (level != LogLevel.Verbose
                || config.DoVerboseLogging)
            {
                Console.WriteLine(message);
            }
        }

```

## <a name="error-handling"></a>Hibakezelés
A HTTP-válasz fejlécének Állapotkód mezőjében a kérelem sikerességi állapota látható. a "200 OK" állapot jelzi a sikerességet, és a válasz a fent leírtak szerint tartalmazza a hozzáférési jogkivonatot. Az alábbiakban a lehetséges hibák rövid enumerálása látható.

| Állapotkód | Hiba oka | Kezelés |
| ----------- | ------------ | ------------- |
| 404 nem található. | Ismeretlen hitelesítési kód, vagy az alkalmazás nem rendelt hozzá felügyelt identitást. | Helyesbítse az alkalmazás telepítőjét vagy a jogkivonat-beszerzési kódot. |
| 429 túl sok kérés. |  Elérte a szabályozási korlátot, amely HRE vagy SF alapján lett kiszabva. | Próbálja megismételni az exponenciális leállítási. Lásd az alábbi útmutatást. |
| 4xx hiba a kérelemben. | Egy vagy több kérelem paramétere helytelen volt. | Ne próbálkozzon újra.  További információért tekintse meg a hiba részleteit.  a 4xx hibák a tervezési idejű hibák.|
| 5xx hiba a szolgáltatástól. | A felügyelt identitás alrendszer vagy Azure Active Directory átmeneti hibát adott vissza. | Rövid idő múlva nyugodtan próbálkozhat. Az újrapróbálkozáskor a szabályozás feltételeit (429) is elérheti.|

Ha hiba történik, a megfelelő HTTP-válasz törzse tartalmaz egy JSON-objektumot, amely a hiba részleteit tartalmazza:

| Elem | Leírás |
| ------- | ----------- |
| code | Hibakód. |
| correlationId | A hibakereséshez használható korrelációs azonosító. |
| message | Hiba részletes leírása. **A hibákkal kapcsolatos leírások bármikor megváltoztathatók. A nem függ magától a hibaüzenettől.**|

Minta hiba:
```json
{"error":{"correlationId":"7f30f4d3-0f3a-41e0-a417-527f21b3848f","code":"SecretHeaderNotFound","message":"Secret is not found in the request headers."}}
```

A következő lista a felügyelt identitásokra jellemző jellemző Service Fabric hibák listáját tartalmazza:

| Code | Üzenet | Leírás | 
| ----------- | ----- | ----------------- |
| SecretHeaderNotFound | A titkos kód nem található a kérelem fejlécében. | A hitelesítési kódot nem adta meg a kérelem. | 
| ManagedIdentityNotFound | Nem található felügyelt identitás a megadott alkalmazás-gazdagéphez. | Az alkalmazás nem rendelkezik identitással, vagy a hitelesítési kód ismeretlen. |
| ArgumentNullOrEmpty | Az "erőforrás" paraméter nem lehet null értékű vagy üres karakterlánc. | A kérelemben nincs megadva az erőforrás (célközönség). |
| InvalidApiVersion | A (z) "" API-verzió nem támogatott. A támogatott verzió: "2019-07-01-Preview". | Hiányzó vagy nem támogatott API-verzió van megadva a kérelem URI azonosítójában. |
| InternalServerError | Hiba történt. | Hiba történt a felügyelt identitás alrendszerében, valószínűleg a Service Fabric veremön kívül. A legvalószínűbb ok az erőforráshoz megadott helytelen érték (a záró "/" jelölést kell megadnia?) | 

## <a name="retry-guidance"></a>Újrapróbálkozási útmutató 

Általában az egyetlen újrapróbálkozást lehetővé tevő hibakód 429 (túl sok kérés); a belső kiszolgálói hibák/5xx-hibakódok valószínűleg újrapróbálkoznak, de az ok lehet állandó. 

A szabályozás korlátozásai a felügyelt identitási alrendszerre irányuló hívások számára vonatkoznak – konkrétan a "felsőbb rétegbeli" függőségek (felügyelt identitás Azure szolgáltatás vagy a biztonságos jogkivonat szolgáltatás). Service Fabric gyorsítótárazza a tokeneket a folyamat különböző szintjein, de az érintett összetevők elosztott jellege miatt előfordulhat, hogy a hívó inkonzisztens szabályozási válaszokat észlel (például lekéri az alkalmazás egy csomópontján/példányán, de nem egy másik csomóponton, miközben az azonos identitáshoz tartozó jogkivonatot kér).) Ha a szabályozási feltétel be van állítva, az azonos alkalmazásból érkező további kérések meghiúsulnak az 429-as HTTP-állapotkód (túl sok kérés), amíg a feltétel nem törlődik.  

Azt javasoljuk, hogy a sávszélesség-szabályozás miatti kérelmeket az alábbi módon próbálja megismételni egy exponenciális leállítási: 

| Hívási index | Művelet a 429-es fogadáshoz | 
| --- | --- | 
| 1 | Várjon 1 másodpercet, és próbálkozzon újra |
| 2 | Várjon 2 másodpercet, és próbálkozzon újra |
| 3 | Várjon 4 másodpercet, és próbálkozzon újra |
| 4 | Várjon 8 másodpercet, és próbálkozzon újra |
| 4 | Várjon 8 másodpercet, és próbálkozzon újra |
| 5 | Várjon 16 másodpercet, és próbálkozzon újra |

## <a name="resource-ids-for-azure-services"></a>Az Azure-szolgáltatások erőforrás-azonosítói
Tekintse meg az Azure ad- [hitelesítést támogató Azure-szolgáltatásokat](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md) az Azure ad-t támogató erőforrások listáját, valamint a hozzájuk tartozó erőforrás-azonosítókat.

## <a name="next-steps"></a>Következő lépések
* [Azure Service Fabric-alkalmazás üzembe helyezése rendszerhez rendelt felügyelt identitással](./how-to-deploy-service-fabric-application-system-assigned-managed-identity.md)
* [Azure Service Fabric-alkalmazás üzembe helyezése felhasználó által hozzárendelt felügyelt identitással](./how-to-deploy-service-fabric-application-user-assigned-managed-identity.md)
* [Azure Service Fabric-alkalmazások hozzáférésének biztosítása más Azure-erőforrásokhoz](./how-to-grant-access-other-resources.md)
* [Minta alkalmazás megismerése Service Fabric felügyelt identitás használatával](https://github.com/Azure-Samples/service-fabric-managed-identity)