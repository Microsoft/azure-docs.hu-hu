---
title: Felügyelt identitások használata egy virtuális gépen hozzáférési jogkivonat lekérte – Azure AD
description: Részletes utasítások és példák az OAuth hozzáférési jogkivonatok virtuális gépeken lévő Azure-erőforrások felügyelt identitásának használatára.
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/12/2021
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1ee7739d9dbfd34190dc1e856b98fdd21be15743
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2021
ms.locfileid: "107364940"
---
# <a name="how-to-use-managed-identities-for-azure-resources-on-an-azure-vm-to-acquire-an-access-token"></a>Hozzáférési jogkivonat beszerzése azure-beli virtuális gépen található Azure-erőforrások felügyelt identitásának használatával 

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]  

Az Azure-erőforrások felügyelt identitása automatikusan felügyelt identitást biztosít az Azure-szolgáltatásoknak a Azure Active Directory. Ezzel az identitással bármely Azure AD-hitelesítést támogató szolgáltatásban hitelesíthet anélkül, hogy hitelesítő adatokat ad meg a kódban. 

Ez a cikk számos kód- és szkriptpémakört tartalmaz a jogkivonatok beszerzéséhez, valamint útmutatást nyújt olyan fontos témakörökhöz, mint a jogkivonatok lejáratának kezelése és a HTTP-hibák kezelése. 

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [msi-qs-configure-prereqs](../../../includes/active-directory-msi-qs-configure-prereqs.md)]

Ha a cikkben említett példákat Azure PowerShell használni, akkor a legújabb verzióját telepítse a [Azure PowerShell.](/powershell/azure/install-az-ps)


> [!IMPORTANT]
> - A cikkben található összes mintakód/szkript feltételezi, hogy az ügyfél egy olyan virtuális gépen fut, amely felügyelt identitásokkal rendelkezik az Azure-erőforrásokhoz. A virtuális gép "Csatlakozás" funkcióját a Azure Portal virtuális géphez való távoli csatlakozáshoz használhatja. Az Azure-erőforrások felügyelt identitásának virtuális gépen való engedélyezésével kapcsolatos részletekért lásd: Azure-erőforrások felügyelt identitásának konfigurálása virtuális gépen [a Azure Portal](qs-configure-portal-windows-vm.md)használatával, vagy az egyik változatcikk (a PowerShell, a parancssori felület, egy sablon vagy egy Azure SDK használatával). 

> [!IMPORTANT]
> - Az Azure-erőforrások felügyelt identitásának biztonsági határa az az erőforrás, amelyen használatban van. A virtuális gépen futó összes kód/szkript lekérhet és lekérhet jogkivonatokat a rajta elérhető felügyelt identitásokhoz. 

## <a name="overview"></a>Áttekintés

Az ügyfélalkalmazások felügyelt identitásokat kérhetnek az [Azure-erőforrások](../develop/developer-glossary.md#access-token) csak alkalmazásra vonatkozó hozzáférési jogkivonatához egy adott erőforrás eléréséhez. A jogkivonat az Azure-erőforrások szolgáltatásnév [felügyelt identitásán alapul.](overview.md#managed-identity-types) Így nincs szükség arra, hogy az ügyfél regisztrálja magát ahhoz, hogy hozzáférési jogkivonatot szerezzen be a saját szolgáltatásnév alatt. A jogkivonat alkalmas az ügyfél-hitelesítő adatokat igénylő szolgáltatások között a [jogkivonatként való használatra.](../develop/v2-oauth2-client-creds-grant-flow.md)

| Hivatkozás | Leírás |
| -------------- | -------------------- |
| [Jogkivonat le kérése HTTP használatával](#get-a-token-using-http) | Az Azure-erőforrások felügyelt identitásának protokolljának részletei jogkivonatvégpont |
| [Jogkivonat lekérte a Microsoft.Azure.Services.AppAuthentication kódtárat a .NET-hez](#get-a-token-using-the-microsoftazureservicesappauthentication-library-for-net) | Példa a Microsoft.Azure.Services.AppAuthentication kódtár .NET-ügyfélből való használatára
| [Jogkivonat lekérte a C használatával #](#get-a-token-using-c) | Példa azure-erőforrások felügyelt identitásának C#-ügyfélből származó REST-végpontjának felhasználásával |
| [Jogkivonat lekérte a Java használatával](#get-a-token-using-java) | Azure-erőforrások felügyelt identitásának REST-végpont java-ügyfélből való használatának példája |
| [Jogkivonat lekérte a Go használatával](#get-a-token-using-go) | Példa Azure-erőforrások felügyelt identitásának GO-ügyfélből származó REST-végponthoz való használatra |
| [Jogkivonat lekérte az Azure PowerShell](#get-a-token-using-azure-powershell) | Példa Azure-erőforrások felügyelt identitásának REST-végpontjának PowerShell-ügyfélből való használatra |
| [Jogkivonat lekérte a CURL használatával](#get-a-token-using-curl) | Példa Azure-erőforrások felügyelt identitásának REST-végpontra Bash-/CURL-ügyfélből való használatával |
| Jogkivonatok gyorsítótárazása | Útmutatás lejárt hozzáférési jogkivonatok kezelésével |
| [Hibakezelés](#error-handling) | Útmutató az Azure-erőforrások felügyelt identitási jogkivonatának végpontja által visszaadott HTTP-hibák kezelésével kapcsolatban |
| [Azure-szolgáltatások erőforrás-nevei](#resource-ids-for-azure-services) | A támogatott Azure-szolgáltatások erőforrás-számainak lekért száma |

## <a name="get-a-token-using-http"></a>Jogkivonat beszerzése HTTP használatával 

A hozzáférési jogkivonatok megszerzésének alapvető felülete a REST-en alapul, így elérhetővé teszi a virtuális gépen futó bármely olyan ügyfélalkalmazás számára, amely KÉPES HTTP REST-hívásokat kezdeményezni. Ez hasonló az Azure AD programozási modelljéhez, azzal a kivétellel, hogy az ügyfél végpontot használ a virtuális gépen (és nem egy Azure AD-végponton).

Mintakérés az Azure Instance Metadata Service (IMDS) végpont használatával *(ajánlott):*

```
GET 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https://management.azure.com/' HTTP/1.1 Metadata: true
```

| Elem | Leírás |
| ------- | ----------- |
| `GET` | A HTTP-művelet, amely azt jelzi, hogy adatokat szeretne lekérni a végpontról. Ebben az esetben egy OAuth hozzáférési jogkivonatot. | 
| `http://169.254.169.254/metadata/identity/oauth2/token` | Az Azure-erőforrások felügyelt identitásának végpontja a Instance Metadata Service. |
| `api-version`  | Egy lekérdezési sztring paraméter, amely az IMDS-végpont API-verzióját jelzi. Használja az API-verziót `2018-02-01` vagy újabb verziót. |
| `resource` | Egy lekérdezési sztring paraméter, amely a célerőforrás alkalmazásazonosító URI-ját jelzi. A kiállított jogkivonat (célközönség) jogcímében `aud` is megjelenik. Ez a példa egy jogkivonatot kér a Azure Resource Manager eléréséhez, amelynek az alkalmazásazonosító URI-azonosítója `https://management.azure.com/` . |
| `Metadata` | Egy HTTP-kérés fejlécmezője, amely az Azure-erőforrások felügyelt identitásai számára szükséges a kiszolgálóoldali kérelmek hamisítási (SSRF) támadásának csökkentése érdekében. Ezt az értéket kisbetűsen "true" (igaz) értékre kell állítani. |
| `object_id` | (Nem kötelező) Egy lekérdezési sztringparaméter, amely object_id identitás identitását jelzi, amely a jogkivonatot szeretné. Kötelező, ha a virtuális gép több felhasználó által hozzárendelt felügyelt identitást is használ.|
| `client_id` | (Nem kötelező) Egy lekérdezési sztringparaméter, amely client_id identitás identitását jelzi, amely a jogkivonatot szeretné. Kötelező, ha a virtuális gép több felhasználó által hozzárendelt felügyelt identitást is használ.|
| `mi_res_id` | (Nem kötelező) Egy lekérdezési sztringparaméter, amely mi_res_id jogkivonatot tartalmazó felügyelt identitás mi_res_id (Azure-erőforrás-azonosító). Kötelező, ha a virtuális gép több felhasználó által hozzárendelt felügyelt identitást is használ. |

Mintaválasz:

```json
HTTP/1.1 200 OK
Content-Type: application/json
{
  "access_token": "eyJ0eXAi...",
  "refresh_token": "",
  "expires_in": "3599",
  "expires_on": "1506484173",
  "not_before": "1506480273",
  "resource": "https://management.azure.com/",
  "token_type": "Bearer"
}
```

| Elem | Leírás |
| ------- | ----------- |
| `access_token` | A kért hozzáférési jogkivonat. Biztonságos kulcs REST API jogkivonat "bearer" jogkivonatként lesz beágyazva a kérelem fejlécmezőbe, így az API hitelesítheti `Authorization` a hívót. | 
| `refresh_token` | Az Azure-erőforrások felügyelt identitása nem használja. |
| `expires_in` | Az a másodpercek száma, amely után a hozzáférési jogkivonat érvényes marad a kiadástól a lejáratig. A kiállítás ideje a jogkivonat jogcímében `iat` található. |
| `expires_on` | A hozzáférési jogkivonat lejárati ideje. A dátumot az "1970-01-01T0:0:0Z UTC" időponttól (UTC) származó másodpercek száma jelöli (a jogkivonat jogcímének `exp` felel meg). |
| `not_before` | A hozzáférési jogkivonat effektusának időkorrekta, amely elfogadható. A dátumot az "1970-01-01T0:0:0Z UTC" időponttól (UTC) származó másodpercek száma jelöli (a jogkivonat jogcímének `nbf` felel meg). |
| `resource` | Az az erőforrás, amelyre a hozzáférési jogkivonatot kérelmezték, és amely megegyezik a kérés `resource` lekérdezési sztring paraméterének értékére. |
| `token_type` | A jogkivonat típusa, amely egy "Bearer" hozzáférési jogkivonat, ami azt jelenti, hogy az erőforrás hozzáférést adhat a jogkivonat kezelője számára. |

## <a name="get-a-token-using-the-microsoftazureservicesappauthentication-library-for-net"></a>Jogkivonat beszerzése a .NET-hez használt Microsoft.Azure.Services.AppAuthentication kódtár használatával

A .NET-alkalmazások és -függvények esetében az Azure-erőforrások felügyelt identitásait a Legegyszerűbben a Microsoft.Azure.Services.AppAuthentication csomaggal lehet kezelni. Ez a kódtár azt is lehetővé teszi, hogy helyileg tesztelje a kódot a fejlesztői gépen az Visual Studio, az [Azure CLI](/cli/azure)vagy az integrált hitelesítés Active Directory használatával. A kódtár helyi fejlesztési lehetőségeiről további információt a [Microsoft.Azure.Services.AppAuthentication referencia tartalmaz.](/dotnet/api/overview/azure/service-to-service-authentication) Ez a szakasz bemutatja, hogyan ássa el a kódtárat a kódban.

1. Adjon hozzá a [Microsoft.Azure.Services.AppAuthentication és](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication) [a Microsoft.Azure.KeyVault](https://www.nuget.org/packages/Microsoft.Azure.KeyVault) NuGet-csomagokra mutató hivatkozásokat az alkalmazáshoz.

2.  Adja hozzá a következő kódot az alkalmazáshoz:

    ```csharp
    using Microsoft.Azure.Services.AppAuthentication;
    using Microsoft.Azure.KeyVault;
    // ...
    var azureServiceTokenProvider = new AzureServiceTokenProvider();
    string accessToken = await azureServiceTokenProvider.GetAccessTokenAsync("https://management.azure.com/");
    // OR
    var kv = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(azureServiceTokenProvider.KeyVaultTokenCallback));
    ```
    
A Microsoft.Azure.Services.AppAuthentication szolgáltatásról és az elérhetővé tévő műveletekről további információt a [Microsoft.Azure.Services.AppAuthentication](/dotnet/api/overview/azure/service-to-service-authentication) referenciában, valamint az Azure-erőforrások felügyelt identitásokkal való App Service és [KeyVault](https://github.com/Azure-Samples/app-service-msi-keyvault-dotnet)használatát tartalmazó .NET-minta tartalmaz.

## <a name="get-a-token-using-c"></a>Jogkivonat lekérte a C használatával #

```csharp
using System;
using System.Collections.Generic;
using System.IO;
using System.Net;
using System.Web.Script.Serialization; 

// Build request to acquire managed identities for Azure resources token
HttpWebRequest request = (HttpWebRequest)WebRequest.Create("http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https://management.azure.com/");
request.Headers["Metadata"] = "true";
request.Method = "GET";

try
{
    // Call /token endpoint
    HttpWebResponse response = (HttpWebResponse)request.GetResponse();

    // Pipe response Stream to a StreamReader, and extract access token
    StreamReader streamResponse = new StreamReader(response.GetResponseStream()); 
    string stringResponse = streamResponse.ReadToEnd();
    JavaScriptSerializer j = new JavaScriptSerializer();
    Dictionary<string, string> list = (Dictionary<string, string>) j.Deserialize(stringResponse, typeof(Dictionary<string, string>));
    string accessToken = list["access_token"];
}
catch (Exception e)
{
    string errorText = String.Format("{0} \n\n{1}", e.Message, e.InnerException != null ? e.InnerException.Message : "Acquire token failed");
}

```

## <a name="get-a-token-using-java"></a>Jogkivonat lekérte a Java használatával

Ezzel a [JSON-kódtáraval](https://mvnrepository.com/artifact/com.fasterxml.jackson.core/jackson-core/2.9.4) lekér egy jogkivonatot a Java használatával.

```Java
import java.io.*;
import java.net.*;
import com.fasterxml.jackson.core.*;
 
class GetMSIToken {
    public static void main(String[] args) throws Exception {
 
        URL msiEndpoint = new URL("http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https://management.azure.com/");
        HttpURLConnection con = (HttpURLConnection) msiEndpoint.openConnection();
        con.setRequestMethod("GET");
        con.setRequestProperty("Metadata", "true");
 
        if (con.getResponseCode()!=200) {
            throw new Exception("Error calling managed identity token endpoint.");
        }
 
        InputStream responseStream = con.getInputStream();
 
        JsonFactory factory = new JsonFactory();
        JsonParser parser = factory.createParser(responseStream);
 
        while(!parser.isClosed()){
            JsonToken jsonToken = parser.nextToken();
 
            if(JsonToken.FIELD_NAME.equals(jsonToken)){
                String fieldName = parser.getCurrentName();
                jsonToken = parser.nextToken();
 
                if("access_token".equals(fieldName)){
                    String accesstoken = parser.getValueAsString();
                    System.out.println("Access Token: " + accesstoken.substring(0,5)+ "..." + accesstoken.substring(accesstoken.length()-5));
                    return;
                }
            }
        }
    }
}
```

## <a name="get-a-token-using-go"></a>Jogkivonat lekérte a Go használatával

```
package main

import (
  "fmt"
  "io/ioutil"
  "net/http"
  "net/url"
  "encoding/json"
)

type responseJson struct {
  AccessToken string `json:"access_token"`
  RefreshToken string `json:"refresh_token"`
  ExpiresIn string `json:"expires_in"`
  ExpiresOn string `json:"expires_on"`
  NotBefore string `json:"not_before"`
  Resource string `json:"resource"`
  TokenType string `json:"token_type"`
}

func main() {
    
    // Create HTTP request for a managed services for Azure resources token to access Azure Resource Manager
    var msi_endpoint *url.URL
    msi_endpoint, err := url.Parse("http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01")
    if err != nil {
      fmt.Println("Error creating URL: ", err)
      return 
    }
    msi_parameters := url.Values{}
    msi_parameters.Add("resource", "https://management.azure.com/")
    msi_endpoint.RawQuery = msi_parameters.Encode()
    req, err := http.NewRequest("GET", msi_endpoint.String(), nil)
    if err != nil {
      fmt.Println("Error creating HTTP request: ", err)
      return 
    }
    req.Header.Add("Metadata", "true")

    // Call managed services for Azure resources token endpoint
    client := &http.Client{}
    resp, err := client.Do(req) 
    if err != nil{
      fmt.Println("Error calling token endpoint: ", err)
      return
    }

    // Pull out response body
    responseBytes,err := ioutil.ReadAll(resp.Body)
    defer resp.Body.Close()
    if err != nil {
      fmt.Println("Error reading response body : ", err)
      return
    }

    // Unmarshall response body into struct
    var r responseJson
    err = json.Unmarshal(responseBytes, &r)
    if err != nil {
      fmt.Println("Error unmarshalling the response:", err)
      return
    }

    // Print HTTP response and marshalled response body elements to console
    fmt.Println("Response status:", resp.Status)
    fmt.Println("access_token: ", r.AccessToken)
    fmt.Println("refresh_token: ", r.RefreshToken)
    fmt.Println("expires_in: ", r.ExpiresIn)
    fmt.Println("expires_on: ", r.ExpiresOn)
    fmt.Println("not_before: ", r.NotBefore)
    fmt.Println("resource: ", r.Resource)
    fmt.Println("token_type: ", r.TokenType)
}
```

## <a name="get-a-token-using-azure-powershell"></a>Jogkivonat lekérte az Azure PowerShell

Az alábbi példa bemutatja, hogyan használhatja az Azure-erőforrások felügyelt identitásai REST-végpontot egy PowerShell-ügyfélből a következőkre:

1. Hozzáférési jogkivonat beszerzése.
2. A hozzáférési jogkivonattal hívhat meg egy Azure Resource Manager REST API, és lekérte a virtuális gép adatait. Mindenképpen helyettesítse be az előfizetés-azonosítót, az erőforráscsoport nevét és a virtuális gép nevét a , a és a `<SUBSCRIPTION-ID>` `<RESOURCE-GROUP>` `<VM-NAME>` névvel.

```azurepowershell
Invoke-WebRequest -Uri 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fmanagement.azure.com%2F' -Headers @{Metadata="true"}
```

Példa a hozzáférési jogkivonat a válaszból való elemzési mikéntjére:
```azurepowershell
# Get an access token for managed identities for Azure resources
$response = Invoke-WebRequest -Uri 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fmanagement.azure.com%2F' `
                              -Headers @{Metadata="true"}
$content =$response.Content | ConvertFrom-Json
$access_token = $content.access_token
echo "The managed identities for Azure resources access token is $access_token"

# Use the access token to get resource information for the VM
$vmInfoRest = (Invoke-WebRequest -Uri 'https://management.azure.com/subscriptions/<SUBSCRIPTION-ID>/resourceGroups/<RESOURCE-GROUP>/providers/Microsoft.Compute/virtualMachines/<VM-NAME>?api-version=2017-12-01' -Method GET -ContentType "application/json" -Headers @{ Authorization ="Bearer $access_token"}).content
echo "JSON returned from call to get VM info:"
echo $vmInfoRest

```

## <a name="get-a-token-using-curl"></a>Jogkivonat lekérte a CURL használatával

```bash
curl 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fmanagement.azure.com%2F' -H Metadata:true -s
```


Példa a hozzáférési jogkivonat a válaszból való elemzési mikéntjére:

```bash
response=$(curl 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fmanagement.azure.com%2F' -H Metadata:true -s)
access_token=$(echo $response | python -c 'import sys, json; print (json.load(sys.stdin)["access_token"])')
echo The managed identities for Azure resources access token is $access_token
```

## <a name="token-caching"></a>Jogkivonatok gyorsítótárazása

Bár az Azure-erőforrások alrendszerének felügyelt identitásai gyorsítótárazási jogkivonatokat is tartalmaznak, javasoljuk, hogy a kódban implementálja a jogkivonatok gyorsítótárazása kódot. Ennek eredményeképpen fel kell készülnie az olyan forgatókönyvekre, ahol az erőforrás azt jelzi, hogy a jogkivonat lejárt. 

Az Azure AD-hez való vezetéken keresztüli hívások csak a következőkor:

- A gyorsítótár-tévesztés azért történik, mert nincs jogkivonat az Azure-erőforrások alrendszer-gyorsítótárának felügyelt identitásában.
- A gyorsítótárazott jogkivonat lejárt.

## <a name="error-handling"></a>Hibakezelés

Az Azure-erőforrások felügyelt identitása végpontja 4xx-es vagy 5xx-es hibákként jelzi a hibákat a HTTP-válasz üzenetfejlécének állapotkód mezőjében:

| Állapotkód | Hiba oka | A kezelés |
| ----------- | ------------ | ------------- |
| 404 Nem található. | Az IMDS-végpont frissül. | Újrapróbálkozás exponenciális leépítéssel. Tekintse meg az alábbi útmutatót. |
| 429 Túl sok kérelem. |  Elérte az IMDS-korlátozást. | Újrapróbálkozás exponenciális leépítéssel. Tekintse meg az alábbi útmutatót. |
| 4xx Hiba a kérésben. | Egy vagy több kérelemparaméter helytelen volt. | Ne próbálja újra.  További információért vizsgálja meg a hiba részleteit.  A 4xx-es hibák tervezéskor előforduló hibák.|
| 5xx A szolgáltatás átmeneti hibája. | Az Azure-erőforrások alrendszerének vagy virtuális gépének felügyelt identitása Azure Active Directory hibát adott vissza. | Legalább 1 másodperc várakozás után biztonságosan újrapróbálkozás.  Ha túl gyorsan vagy túl gyakran próbálkozik újra, előfordulhat, hogy az IMDS és/vagy az Azure AD sebességkorlát-hibát ad vissza (429).|
| timeout | Az IMDS-végpont frissül. | Újrapróbálkozás exponenciális leépítéssel. Tekintse meg az alábbi útmutatót. |

Hiba esetén a megfelelő HTTP-válasz törzse tartalmazza a hiba részleteit tartalmazó JSON-t:

| Elem | Leírás |
| ------- | ----------- |
| error   | Hibaazonosító. |
| error_description | A hiba részletes leírása. **A hibaleírások bármikor változhatnak. Ne írjon olyan kódot, amely elágaszik a hiba leírásában megadott értékek alapján.**|

### <a name="http-response-reference"></a>HTTP-válaszreakció referenciája

Ez a szakasz a lehetséges hibaválaszokat sorolja fel. A "200 OK" állapot sikeres válasz, és a hozzáférési jogkivonat a válasz törzsének JSON-ját tartalmazza a access_token elemben.

| Állapotkód | Hiba | Hiba leírása | Megoldás |
| ----------- | ----- | ----------------- | -------- |
| 400 Hibás kérés | invalid_resource | AADSTS50001: A nevű alkalmazás *\<URI\>* nem található a nevű bérlőben. *\<TENANT-ID\>* Ez akkor fordulhat elő, ha az alkalmazást nem a bérlő rendszergazdája telepítette, vagy nem fogadta el egy felhasználó sem a bérlőben. Előfordulhat, hogy nem a megfelelő bérlőre küldte a hitelesítési kérést.\ | (csak Linux esetén) |
| 400 Hibás kérés | bad_request_102 | Nincs megadva kötelező metaadat-fejléc | A `Metadata` kérelem fejlécmezője hiányzik a kérelemből, vagy helytelenül van formázva. Az értéket kisbetűsen `true` a következőként kell megadni: . Erre példát az előző REST-szakaszban található "Mintakérés" című szakaszban láthat.|
| 401 Nem engedélyezett | unknown_source | Ismeretlen forrás *\<URI\>* | Ellenőrizze, hogy a HTTP GET kérelem URI-ját helyesen formázta-e. A `scheme:host/resource-path` résznek a következőként kell lennie: `http://localhost:50342/oauth2/token` . Erre példát az előző REST-szakaszban található "Mintakérés" című szakaszban láthat.|
|           | invalid_request | A kérelemhez hiányzik egy kötelező paraméter, érvénytelen paraméterértéket tartalmaz, egy paramétert tartalmaz egynél több alkalommal, vagy más módon helytelenül formázott. |  |
|           | unauthorized_client | Az ügyfél nem jogosult hozzáférési jogkivonatot kérni ezzel a módszerrel. | Egy olyan virtuális gépre vonatkozó kérés okozza, amely nem rendelkezik megfelelően konfigurált Azure-erőforrások felügyelt identitásokkal. Ha [segítségre van szüksége a](qs-configure-portal-windows-vm.md) virtuális gépek konfigurálásával kapcsolatban, tekintse meg az Azure-erőforrások felügyelt identitásának konfigurálása virtuális gépen a Azure Portal használatával. |
|           | access_denied | Az erőforrás tulajdonosa vagy az engedélyezési kiszolgáló elutasította a kérést. |  |
|           | unsupported_response_type | Az engedélyezési kiszolgáló nem támogatja hozzáférési jogkivonat beszerzését ezzel a módszerrel. |  |
|           | invalid_scope | A kért hatókör érvénytelen, ismeretlen vagy helytelenül formázott. |  |
| 500 Belső kiszolgálóhiba | Ismeretlen | Nem sikerült lekérni a jogkivonatot az Active Directoryból. Részletekért tekintse meg a következőt: *\<file path\>* | Ellenőrizze, hogy az Azure-erőforrások felügyelt identitása engedélyezve van-e a virtuális gépen. Ha [segítségre van szüksége a](qs-configure-portal-windows-vm.md) virtuális gépek konfigurálásával kapcsolatban, tekintse meg az Azure-erőforrások felügyelt identitásának konfigurálása virtuális gépen a Azure Portal használatával.<br><br>Azt is ellenőrizze, hogy a HTTP GET kérelem URI-ját helyesen formázta-e, különösen a lekérdezési sztringben megadott erőforrás URI-t. Az előző REST-szakaszban található "Mintakérés" szakaszban talál egy példát, vagy az [Azure AD-hitelesítést](./services-support-managed-identities.md) támogató Azure-szolgáltatásokat a szolgáltatások és a megfelelő erőforrás-azonosításuk listájáért.

## <a name="retry-guidance"></a>Újrapróbálkozási útmutató 

404-es, 429-es vagy 5xx-es hibakód esetén ajánlott újrapróbálkozás (lásd fent a [Hibakezelést).](#error-handling)

A szabályozási korlátok az IMDS-végpontra vonatkozó hívások számára vonatkoznak. A szabályozási küszöbérték túllépése esetén az IMDS-végpont korlátozza a további kéréseket, amíg a szabályozás érvényben van. Ebben az időszakban az IMDS-végpont a 429-es HTTP-állapotkódot ("Túl sok kérés") adja vissza, és a kérések meghiúsulnak. 

Újrapróbálkozáshoz a következő stratégiát javasoljuk: 

| **Újrapróbálkozási stratégia** | **Beállítások** | **Értékek** | **Működés** |
| --- | --- | --- | --- |
|ExponentialBackoff |Ismétlések száma<br />Visszatartás (min.)<br />Visszatartás (max.)<br />Visszatartás (változás)<br />Első gyors újrapróbálkozás |5<br />0 másodperc<br />60 másodperc<br />2 másodperc<br />hamis |1. kísérlet – 0 mp. késleltetés<br />2. kísérlet – kb. 2 mp. késleltetés<br />3. kísérlet – kb. 6 mp. késleltetés<br />4. kísérlet – kb. 14 mp. késleltetés<br />5. kísérlet – kb. 30 mp. késleltetés |

## <a name="resource-ids-for-azure-services"></a>Azure-szolgáltatások erőforrás-nevei

Az [Azure AD-t támogató, az Azure-erőforrások](./services-support-managed-identities.md) felügyelt identitásával és azok erőforrás-azonosítóival tesztelt erőforrások listáját az Azure AD-hitelesítést támogató Azure-szolgáltatásokban talál.


## <a name="next-steps"></a>Következő lépések

- Az Azure-erőforrások felügyelt identitásának Azure-beli virtuális gépen való engedélyezéséhez lásd: Azure-erőforrások felügyelt identitásának konfigurálása egy virtuális gépen [a Azure Portal.](qs-configure-portal-windows-vm.md)