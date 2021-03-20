---
title: Felügyelt identitások használata virtuális gépen hozzáférési jogkivonat beszerzéséhez – Azure AD
description: Részletes utasítások és példák egy virtuális gépen található Azure-erőforrások felügyelt identitásának használatára egy OAuth hozzáférési jogkivonat beszerzéséhez.
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
ms.date: 11/03/2020
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.openlocfilehash: 541f76ad825f492679530902c571096ca4b01902
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98726231"
---
# <a name="how-to-use-managed-identities-for-azure-resources-on-an-azure-vm-to-acquire-an-access-token"></a>Egy Azure-beli virtuális gépen található Azure-erőforrások felügyelt identitásának használata hozzáférési jogkivonat beszerzéséhez 

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]  

Az Azure-erőforrások felügyelt identitásai az Azure-szolgáltatásokat a Azure Active Directory automatikusan felügyelt identitással biztosítják. Ezt az identitást használhatja bármely olyan szolgáltatás hitelesítéséhez, amely támogatja az Azure AD-hitelesítést, és nem rendelkezik hitelesítő adatokkal a kódban. 

Ez a cikk különböző kódokat és parancsfájl-példákat tartalmaz a tokenek beszerzéséhez, valamint útmutatást nyújt a fontos témakörökhöz, például a jogkivonat lejáratának és a HTTP-hibáknak a kezelésére 

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [msi-qs-configure-prereqs](../../../includes/active-directory-msi-qs-configure-prereqs.md)]

Ha azt tervezi, hogy a jelen cikkben szereplő Azure PowerShell példákat is használja, telepítse a [Azure PowerShell](/powershell/azure/install-az-ps)legújabb verzióját.


> [!IMPORTANT]
> - A cikkben szereplő összes mintakód/szkript azt feltételezi, hogy az ügyfél az Azure-erőforrások felügyelt identitásával rendelkező virtuális gépen fut. Használja a virtuális gép "kapcsolódás" funkcióját a Azure Portal, hogy távolról csatlakozhasson a virtuális GÉPHEZ. A virtuális gépeken található Azure-erőforrások felügyelt identitásának engedélyezésével kapcsolatos részletekért lásd: [felügyelt identitások konfigurálása egy virtuális gépen az Azure-erőforrások számára a Azure Portal](qs-configure-portal-windows-vm.md)vagy az egyik varianting article (POWERSHELL, CLI, sablon vagy Azure SDK használatával). 

> [!IMPORTANT]
> - Az Azure-erőforrások felügyelt identitásának biztonsági határa az az erőforrás, amelyet a használ. A virtuális gépen futó összes kód/szkript kérheti és beolvashatja az összes elérhető felügyelt identitás jogkivonatát. 

## <a name="overview"></a>Áttekintés

Az ügyfélalkalmazások felügyelt identitásokat igényelhetnek az Azure-erőforrások csak az adott erőforráshoz való [hozzáféréshez használható hozzáférési jogkivonattal](../develop/developer-glossary.md#access-token) . A jogkivonat az [Azure-erőforrások egyszerű szolgáltatásának felügyelt identitásán alapul](overview.md#managed-identity-types). Ezért nincs szükség arra, hogy az ügyfél regisztrálja magát egy hozzáférési jogkivonat beszerzéséhez a saját egyszerű szolgáltatásnév keretében. A jogkivonat alkalmas tulajdonosi jogkivonatként használni az [ügyfél hitelesítő adatait igénylő szolgáltatások közötti hívásokban](../develop/v2-oauth2-client-creds-grant-flow.md).

| Hivatkozás | Description |
| -------------- | -------------------- |
| [Token beszerzése HTTP használatával](#get-a-token-using-http) | Az Azure Resources jogkivonat-végponthoz tartozó felügyelt identitások protokolljának részletei |
| [Jogkivonat beszerzése a .NET-hez készült Microsoft. Azure. Services. AppAuthentication kódtár használatával](#get-a-token-using-the-microsoftazureservicesappauthentication-library-for-net) | Példa a Microsoft. Azure. Services. AppAuthentication függvénytár .NET-ügyfélről való használatára
| [Token beszerzése a C használatával #](#get-a-token-using-c) | Példa felügyelt identitások használatára az Azure-erőforrások REST-végpontjának C#-ügyfélből való használatával |
| [Token beszerzése a Javával](#get-a-token-using-java) | Példa felügyelt identitások használatára az Azure-erőforrások REST-végpontja számára egy Java-ügyfélből |
| [Token beszerzése a go használatával](#get-a-token-using-go) | Példa felügyelt identitások használatára az Azure-erőforrások REST-végpontján egy go-ügyfélből |
| [Token beszerzése Azure PowerShell használatával](#get-a-token-using-azure-powershell) | Példa felügyelt identitások használatára az Azure-erőforrások REST-végpontján egy PowerShell-ügyfélről |
| [Token beszerzése a CURL használatával](#get-a-token-using-curl) | Példa felügyelt identitások használatára az Azure-erőforrások REST-végpontján egy bash/CURL-ügyfélből |
| A jogkivonat gyorsítótárazásának feldolgozása | Útmutató a lejárt hozzáférési tokenek kezelésére |
| [Hibakezelés](#error-handling) | Útmutató az Azure Resources jogkivonat-végponthoz tartozó felügyelt identitások által visszaadott HTTP-hibák kezeléséhez |
| [Az Azure-szolgáltatások erőforrás-azonosítói](#resource-ids-for-azure-services) | Honnan kaphat erőforrás-azonosítókat a támogatott Azure-szolgáltatásokhoz |

## <a name="get-a-token-using-http"></a>Token beszerzése HTTP használatával 

A hozzáférési token beszerzésének alapvető felülete a REST-alapú, így elérhetővé válik bármely, a virtuális gépen futó ügyfélalkalmazás számára, amely HTTP REST-hívásokat tesz lehetővé. Ez hasonló az Azure AD programozási modellhez, kivéve, ha az ügyfél egy végpontot használ a virtuális gépen (vs egy Azure AD-végpont).

Példa az Azure Instance Metadata Service (IMDS) végpontjának használatával történő kérelemre *(ajánlott)*:

```
GET 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https://management.azure.com/' HTTP/1.1 Metadata: true
```

| Elem | Leírás |
| ------- | ----------- |
| `GET` | A HTTP-művelet, amely azt jelzi, hogy a végpontról kívánja beolvasni az adatait. Ebben az esetben egy OAuth hozzáférési jogkivonat. | 
| `http://169.254.169.254/metadata/identity/oauth2/token` | Az Azure-erőforrások végpontjának felügyelt identitásai a Instance Metadata Service számára. |
| `api-version`  | Egy lekérdezési karakterlánc paraméter, amely a IMDS-végpont API-verzióját jelzi. Használja az API `2018-02-01` -verziót vagy a nagyobbat. |
| `resource` | Egy lekérdezési karakterlánc paraméter, amely a cél erőforrás alkalmazás-azonosító URI azonosítóját jelzi. Ez a `aud` kiállított jogkivonat (célközönség) jogcímen is megjelenik. Ez a példa jogkivonatot kér a Azure Resource Manager eléréséhez, amelyhez az alkalmazás AZONOSÍTÓjának URI-ja tartozik `https://management.azure.com/` . |
| `Metadata` | Egy HTTP-kérelem fejlécének mezője, amelyet az Azure-erőforrások felügyelt identitásai igényelnek a kiszolgálóoldali kérelmek hamisításának (SSRF) támadása ellen. Ezt az értéket a "true" értékre kell beállítani, az összes kisbetű esetében. |
| `object_id` | Választható Egy lekérdezési karakterlánc paraméter, amely annak a felügyelt identitásnak a object_idét jelzi, amelyhez a tokent szeretné. Kötelező, ha a virtuális gépnek több felhasználó által hozzárendelt felügyelt identitása van.|
| `client_id` | Választható Egy lekérdezési karakterlánc paraméter, amely annak a felügyelt identitásnak a client_idét jelzi, amelyhez a tokent szeretné. Kötelező, ha a virtuális gépnek több felhasználó által hozzárendelt felügyelt identitása van.|
| `mi_res_id` | Választható Egy lekérdezési karakterlánc paraméter, amely annak a felügyelt identitásnak a mi_res_idét (Azure Resource ID) jelzi, amelyhez a tokent szeretné. Kötelező, ha a virtuális gépnek több felhasználó által hozzárendelt felügyelt identitása van. |

Mintául szolgáló kérelem az Azure-erőforrások virtuálisgép-bővítmény végpontjának felügyelt identitások használatával (a 2019 januári verzióban való *használatra tervezett)*:

```http
GET http://localhost:50342/oauth2/token?resource=https%3A%2F%2Fmanagement.azure.com%2F HTTP/1.1
Metadata: true
```

| Elem | Leírás |
| ------- | ----------- |
| `GET` | A HTTP-művelet, amely azt jelzi, hogy a végpontról kívánja beolvasni az adatait. Ebben az esetben egy OAuth hozzáférési jogkivonat. | 
| `http://localhost:50342/oauth2/token` | Az Azure-erőforrások végpontjának felügyelt identitásai, ahol a 50342 az alapértelmezett port, és konfigurálható. |
| `resource` | Egy lekérdezési karakterlánc paraméter, amely a cél erőforrás alkalmazás-azonosító URI azonosítóját jelzi. Ez a `aud` kiállított jogkivonat (célközönség) jogcímen is megjelenik. Ez a példa jogkivonatot kér a Azure Resource Manager eléréséhez, amelyhez az alkalmazás AZONOSÍTÓjának URI-ja tartozik `https://management.azure.com/` . |
| `Metadata` | Egy HTTP-kérelem fejlécének mezője, amelyet az Azure-erőforrások felügyelt identitásai igényelnek a kiszolgálóoldali kérelmek hamisításának (SSRF) támadása ellen. Ezt az értéket a "true" értékre kell beállítani, az összes kisbetű esetében.|
| `object_id` | Választható Egy lekérdezési karakterlánc paraméter, amely annak a felügyelt identitásnak a object_idét jelzi, amelyhez a tokent szeretné. Kötelező, ha a virtuális gépnek több felhasználó által hozzárendelt felügyelt identitása van.|
| `client_id` | Választható Egy lekérdezési karakterlánc paraméter, amely annak a felügyelt identitásnak a client_idét jelzi, amelyhez a tokent szeretné. Kötelező, ha a virtuális gépnek több felhasználó által hozzárendelt felügyelt identitása van.|

Példa a válaszra:

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
| `access_token` | A kért hozzáférési jogkivonat. Biztonságos REST API hívásakor a jogkivonat a `Authorization` kérelem fejléc mezőjébe ágyazva "tulajdonos" tokenként van beágyazva, ami lehetővé teszi, hogy az API hitelesítse a hívót. | 
| `refresh_token` | Az Azure-erőforrások felügyelt identitásai nem használják. |
| `expires_in` | Azon másodpercek száma, ameddig a hozzáférési jogkivonat továbbra is érvényben marad, a lejárat időpontja előtt. A kiadás időpontja megtalálható a jogkivonat `iat` jogcímen. |
| `expires_on` | A TimeSpan, amikor lejár a hozzáférési jogkivonat. A dátum az "1970-01-01T0:0: 0Z UTC" (a jogkivonat jogcímenek felel meg) másodpercben megadott számú másodperc `exp` . |
| `not_before` | A TimeSpan, ha a hozzáférési jogkivonat érvénybe lép, és el lehet fogadni. A dátum az "1970-01-01T0:0: 0Z UTC" (a jogkivonat jogcímenek felel meg) másodpercben megadott számú másodperc `nbf` . |
| `resource` | Az erőforráshoz a hozzáférési tokent kérték, amely megfelel a `resource` kérelem lekérdezési karakterlánc paraméterének. |
| `token_type` | A token típusa, amely egy "tulajdonos" hozzáférési jogkivonat, ami azt jelenti, hogy az erőforrás hozzáférést biztosíthat a jogkivonat tulajdonosához. |

## <a name="get-a-token-using-the-microsoftazureservicesappauthentication-library-for-net"></a>Jogkivonat beszerzése a .NET-hez készült Microsoft. Azure. Services. AppAuthentication kódtár használatával

A .NET-alkalmazások és-függvények esetében az Azure-erőforrások felügyelt identitásával való együttműködés legegyszerűbb módja a Microsoft. Azure. Services. AppAuthentication csomag. Ez a kódtár lehetővé teszi a kód helyi tesztelését a fejlesztői gépen, a Visual studióból, az [Azure CLI](/cli/azure)-ből vagy Active Directory integrált hitelesítésből származó felhasználói fiók használatával. A könyvtár helyi fejlesztési lehetőségeiről további információt a [Microsoft. Azure. Services. AppAuthentication dokumentációjában](/dotnet/api/overview/azure/service-to-service-authentication)talál. Ez a szakasz bemutatja, hogyan kezdheti meg a kódtárat a kódban.

1. Adjon hozzá hivatkozásokat a [Microsoft. Azure. Services. AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication) és a [Microsoft. Azure.](https://www.nuget.org/packages/Microsoft.Azure.KeyVault) kulcstartó NuGet-csomagjaihoz az alkalmazáshoz.

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
    
Ha többet szeretne megtudni a Microsoft. Azure. Services. AppAuthentication és az általa közzétett műveletekről, tekintse meg a [Microsoft. Azure. Services. AppAuthentication referenciáját](/dotnet/api/overview/azure/service-to-service-authentication) , valamint a [felügyelt identitásokkal rendelkező app Service és kulcstartót az Azure-erőforrások .net-mintában](https://github.com/Azure-Samples/app-service-msi-keyvault-dotnet).

## <a name="get-a-token-using-c"></a>Token beszerzése a C használatával #

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

## <a name="get-a-token-using-java"></a>Token beszerzése a Javával

Használja ezt a [JSON-függvénytárat](https://mvnrepository.com/artifact/com.fasterxml.jackson.core/jackson-core/2.9.4) a jogkivonatok Java használatával való lekéréséhez.

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

## <a name="get-a-token-using-go"></a>Token beszerzése a go használatával

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

## <a name="get-a-token-using-azure-powershell"></a>Token beszerzése Azure PowerShell használatával

Az alábbi példa bemutatja, hogyan használhatók a felügyelt identitások az Azure-erőforrások REST-végponthoz egy PowerShell-ügyfélről a következőre:

1. Hozzáférési jogkivonat beszerzése.
2. A hozzáférési token használatával meghívhat egy Azure Resource Manager REST API, és információkat kaphat a virtuális gépről. Ügyeljen rá, hogy az előfizetés-AZONOSÍTÓját, az erőforráscsoport nevét és a virtuális gép nevét adja meg a, a, a, illetve a esetében `<SUBSCRIPTION-ID>` `<RESOURCE-GROUP>` `<VM-NAME>` .

```azurepowershell
Invoke-WebRequest -Uri 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fmanagement.azure.com%2F' -Headers @{Metadata="true"}
```

Példa a hozzáférési jogkivonat elemzésére a válaszból:
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

## <a name="get-a-token-using-curl"></a>Token beszerzése a CURL használatával

```bash
curl 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fmanagement.azure.com%2F' -H Metadata:true -s
```


Példa a hozzáférési jogkivonat elemzésére a válaszból:

```bash
response=$(curl 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fmanagement.azure.com%2F' -H Metadata:true -s)
access_token=$(echo $response | python -c 'import sys, json; print (json.load(sys.stdin)["access_token"])')
echo The managed identities for Azure resources access token is $access_token
```

## <a name="token-caching"></a>Jogkivonat-gyorsítótárazás

Míg az Azure-erőforrásokhoz használt felügyelt identitások (az Azure-erőforrások virtuálisgép-bővítményének IMDS/felügyelt identitása) a gyorsítótár-jogkivonatokat használják, javasoljuk, hogy a token-gyorsítótárazást a kódban is implementálja. Ennek eredményeképpen elő kell készítenie azokat a forgatókönyveket, amelyekben az erőforrás azt jelzi, hogy a jogkivonat lejárt. 

Az Azure AD-ra irányuló hívásokat csak a következő esetekben lehet bevezetni:
- a gyorsítótár-kihagyás oka az Azure-erőforrások alrendszerének gyorsítótárában található felügyelt identitások nem rendelkeznek jogkivonat
- a gyorsítótárazott jogkivonat lejárt

## <a name="error-handling"></a>Hibakezelés

Az Azure-erőforrásokhoz tartozó felügyelt identitások a HTTP-válaszüzenet fejlécének állapotkód mezőjén keresztül jelentkeznek a 4xx vagy 5xx hibák esetén:

| Állapotkód | Hiba oka | Kezelés |
| ----------- | ------------ | ------------- |
| 404 nem található. | A IMDS-végpont frissítése folyamatban van. | Próbálja megismételni az exponenciális leállítási. Lásd az alábbi útmutatást. |
| 429 túl sok kérés. |  Elérte a IMDS szabályozási korlátot. | Próbálja megismételni az exponenciális leállítási. Lásd az alábbi útmutatást. |
| 4xx hiba a kérelemben. | Egy vagy több kérelem paramétere helytelen volt. | Ne próbálkozzon újra.  További információért tekintse meg a hiba részleteit.  a 4xx hibák a tervezési idejű hibák.|
| 5xx átmeneti hiba történt a szolgáltatástól. | Az Azure-erőforrások alrendszerhez vagy Azure Active Directory felügyelt identitása átmeneti hibát adott vissza. | Az újrapróbálkozást legalább 1 másodperc várakozás után érdemes elvégezni.  Ha túl gyorsan vagy túl gyakran próbálkozik újra, a IMDS és/vagy az Azure AD-re vonatkozó korlátozási hiba léphet fel (429).|
| timeout | A IMDS-végpont frissítése folyamatban van. | Próbálja megismételni az exponenciális leállítási. Lásd az alábbi útmutatást. |

Ha hiba történik, a megfelelő HTTP-válasz törzse JSON-t tartalmaz a hiba részleteivel:

| Elem | Leírás |
| ------- | ----------- |
| error   | Hiba azonosítója. |
| error_description | Hiba részletes leírása. **A hibákkal kapcsolatos leírások bármikor megváltoztathatók. Ne írjon olyan kódot, amely az ágakat a hiba leírásában szereplő értékek alapján írja.**|

### <a name="http-response-reference"></a>HTTP-válasz referenciája

Ez a szakasz a lehetséges hibákra adott válaszokat dokumentálja. A "200 OK" állapot sikeres válasz, és a hozzáférési jogkivonat a Válasz törzs JSON-ban található, a access_token elemben.

| Állapotkód | Hiba | Hiba leírása | Megoldás |
| ----------- | ----- | ----------------- | -------- |
| 400 Hibás kérés | invalid_resource | AADSTS50001: a nevű alkalmazás *\<URI\>* nem található a (z) nevű bérlőben *\<TENANT-ID\>* . Ez akkor fordulhat elő, ha az alkalmazást nem a bérlő rendszergazdája telepítette, vagy nem fogadta el egy felhasználó sem a bérlőben. Lehetséges, hogy nem megfelelő bérlőnek küldték a hitelesítési kérelmet. \ | (Csak Linux) |
| 400 Hibás kérés | bad_request_102 | Nincs megadva a szükséges metaadat-fejléc | A kérelem `Metadata` fejléce mező hiányzik a kérelemből, vagy helytelenül van formázva. Az értéket a következőképpen kell megadni `true` :, minden kisbetű esetében. Példaként tekintse meg a "minta kérés" szakaszt az előző REST szakaszban.|
| 401 Nem engedélyezett | unknown_source | Ismeretlen forrás *\<URI\>* | Ellenőrizze, hogy a HTTP GET kérelem URI-ja helyesen van-e formázva. A `scheme:host/resource-path` részt a következőképpen kell megadni: `http://localhost:50342/oauth2/token` . Példaként tekintse meg a "minta kérés" szakaszt az előző REST szakaszban.|
|           | invalid_request | A kérelemből hiányzik egy kötelező paraméter, a paraméter értéke érvénytelen, a paraméter többször is szerepel, vagy más módon helytelen formátumú. |  |
|           | unauthorized_client | Az ügyfél nem jogosult hozzáférési jogkivonat igénylésére a metódus használatával. | Olyan kérelem okozta, amely nem használ helyi visszacsatolást a bővítmény meghívásához, vagy olyan virtuális gépen, amely nem rendelkezik felügyelt identitásokkal az Azure-erőforrásokhoz megfelelően konfigurálva. Ha segítségre van szüksége a virtuálisgép-konfigurációval kapcsolatban, tekintse [meg a felügyelt identitások konfigurálása az Azure-erőforrásokhoz a Azure Portal segítségével](qs-configure-portal-windows-vm.md) című témakört. |
|           | access_denied | Az erőforrás-tulajdonos vagy az engedélyezési kiszolgáló megtagadta a kérelmet. |  |
|           | unsupported_response_type | Az engedélyezési kiszolgáló nem támogatja hozzáférési jogkivonat beszerzését ezzel a módszerrel. |  |
|           | invalid_scope | A kért hatókör érvénytelen, ismeretlen vagy helytelen formátumú. |  |
| 500 Belső kiszolgálóhiba | ismeretlen | Nem sikerült beolvasni a tokent az Active Directoryból. Részletekért lásd: naplók *\<file path\>* | Ellenőrizze, hogy az Azure-erőforrásokhoz tartozó felügyelt identitások engedélyezve lettek-e a virtuális gépen. Ha segítségre van szüksége a virtuálisgép-konfigurációval kapcsolatban, tekintse [meg a felügyelt identitások konfigurálása az Azure-erőforrásokhoz a Azure Portal segítségével](qs-configure-portal-windows-vm.md) című témakört.<br><br>Ellenőrizze azt is, hogy a HTTP GET kérelem URI-ja helyesen van-e formázva, különösen a lekérdezési karakterláncban megadott erőforrás-URI-t. Tekintse meg a "minta kérést" az előző REST szakaszban, vagy az Azure [ad-hitelesítést támogató Azure-szolgáltatásokat](./services-support-managed-identities.md) a szolgáltatások és a hozzájuk tartozó erőforrás-azonosítók listájához.

## <a name="retry-guidance"></a>Újrapróbálkozási útmutató 

Javasoljuk, hogy próbálkozzon újra, ha 404, 429 vagy 5xx hibakódot kap (lásd a fenti [hibakódot](#error-handling) ).

A szabályozás korlátozásai az IMDS-végponton végrehajtott hívások számára vonatkoznak. Ha túllépi a sávszélesség-szabályozási küszöbértéket, a IMDS végpontja korlátozza a további kérelmeket, amíg a szabályozás érvényben van. Ebben az időszakban a IMDS-végpont a 429-as HTTP-állapotkódot ("túl sok kérelem") adja vissza, és a kérések sikertelenek lesznek. 

Az újrapróbálkozáshoz a következő stratégiát javasoljuk: 

| **Újrapróbálkozási stratégia** | **Beállítások** | **Értékek** | **Működés** |
| --- | --- | --- | --- |
|ExponentialBackoff |Ismétlések száma<br />Visszatartás (min.)<br />Visszatartás (max.)<br />Visszatartás (változás)<br />Első gyors újrapróbálkozás |5<br />0 másodperc<br />60 másodperc<br />2 másodperc<br />hamis |1. kísérlet – 0 mp. késleltetés<br />2. kísérlet – kb. 2 mp. késleltetés<br />3. kísérlet – kb. 6 mp. késleltetés<br />4. kísérlet – kb. 14 mp. késleltetés<br />5. kísérlet – kb. 30 mp. késleltetés |

## <a name="resource-ids-for-azure-services"></a>Az Azure-szolgáltatások erőforrás-azonosítói

Tekintse meg az Azure ad- [hitelesítést támogató Azure-szolgáltatásokat](./services-support-managed-identities.md) az Azure ad-t támogató erőforrások listájáért, valamint az Azure-erőforrások felügyelt identitásokkal való tesztelését, valamint a hozzájuk tartozó erőforrás-azonosítókat.


## <a name="next-steps"></a>Következő lépések

- Az Azure-beli virtuális gépeken található Azure-erőforrások felügyelt identitásának engedélyezéséhez lásd: [felügyelt identitások konfigurálása egy virtuális gépen az Azure-erőforrásokhoz a Azure Portal használatával](qs-configure-portal-windows-vm.md).