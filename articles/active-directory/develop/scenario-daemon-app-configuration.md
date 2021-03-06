---
title: Webes API-kat meghívó Daemon-alkalmazások konfigurálása – Microsoft Identity platform | Azure
description: Megtudhatja, hogyan konfigurálhatja a webes API-kat meghívó Daemon-alkalmazás kódját (alkalmazás konfigurációja)
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/19/2020
ms.author: jmprieur
ms.custom: aaddev, devx-track-python
ms.openlocfilehash: 3c52d4d80fd3c77cff5e335967fc9d109212ce29
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "104578430"
---
# <a name="daemon-app-that-calls-web-apis---code-configuration"></a>Webes API-kat meghívó Daemon-alkalmazás – kód konfigurálása

Megtudhatja, hogyan konfigurálhatja a webes API-kat meghívó Daemon-alkalmazás kódját.

## <a name="microsoft-libraries-supporting-daemon-apps"></a>Daemon-alkalmazásokat támogató Microsoft-kódtárak

A következő Microsoft-kódtárak támogatják a Daemon-alkalmazásokat:

[!INCLUDE [active-directory-develop-libraries-daemon](../../../includes/active-directory-develop-libraries-daemon.md)]

## <a name="configure-the-authority"></a>A szolgáltató konfigurálása

A Daemon-alkalmazások a delegált engedélyek helyett alkalmazás-engedélyeket használnak. Ezért a támogatott fióktípus nem lehet fiók bármilyen szervezeti címtárban vagy személyes Microsoft-fiók (például Skype, Xbox, Outlook.com). Nincs olyan bérlői rendszergazda, aki beleegyezik a Microsoft személyes fiókjához tartozó Daemon-alkalmazásba. A szervezetem vagy a fiókjaim *fiókjait* *minden szervezetnél* ki kell választania.

Az alkalmazás konfigurációjában megadott szolgáltatónak Bérlőnek kell lennie (a bérlő AZONOSÍTÓját vagy a szervezethez társított tartománynevet kell megadnia).

Még ha több-bérlős eszközt is szeretne biztosítani, használja a bérlői azonosítót vagy a tartománynevet, **ne** `common` vagy ezzel a `organizations` folyamattal, mert a szolgáltatás nem tudja megbízhatóan kikövetkeztetni, hogy melyik bérlőt kell használni.

## <a name="configure-and-instantiate-the-application"></a>Az alkalmazás konfigurálása és példányának beállítása

A MSAL-könyvtárakban az ügyfél hitelesítő adatai (titkos vagy tanúsítvány) a bizalmas ügyfélalkalmazás-építés paramétereként lesznek átadva.

> [!IMPORTANT]
> Még akkor is, ha az alkalmazás egy olyan konzolos alkalmazás, amely szolgáltatásként fut, és amely egy démon alkalmazás, bizalmas ügyfélalkalmazás szükséges.

### <a name="configuration-file"></a>Konfigurációs fájl

A konfigurációs fájl határozza meg a következőket:

- A Felhőbeli példány és a bérlő azonosítója, amely együttesen alkotja a *szolgáltatót*.
- Az alkalmazás regisztrálásakor kapott ügyfél-azonosító.
- Vagy egy ügyfél titka vagy egy tanúsítvány.

# <a name="net"></a>[.NET](#tab/dotnet)

Íme egy példa arra, hogyan határozható meg a konfiguráció egy [*appsettings.js*](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/blob/master/1-Call-MSGraph/daemon-console/appsettings.json) fájlban. Ez a példa a " [.net Core Console Daemon](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2) code" mintából származik a githubon.

```json
{
  "Instance": "https://login.microsoftonline.com/{0}",
  "Tenant": "[Enter here the tenantID or domain name for your Azure AD tenant]",
  "ClientId": "[Enter here the ClientId for your application]",
  "ClientSecret": "[Enter here a client secret for your application]",
  "CertificateName": "[Or instead of client secret: Enter here the name of a certificate (from the user cert store) as registered with your application]"
}
```

Adja meg a `ClientSecret` vagy a vagy a-t `CertificateName` . Ezek a beállítások kizárólagosak.

# <a name="java"></a>[Java](#tab/java)

```Java
 private final static String CLIENT_ID = "";
 private final static String AUTHORITY = "https://login.microsoftonline.com/<tenant>/";
 private final static String CLIENT_SECRET = "";
 private final static Set<String> SCOPE = Collections.singleton("https://graph.microsoft.com/.default");
```

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

A [Node.js Daemon-minta](https://github.com/Azure-Samples/ms-identity-javascript-nodejs-console/) konfigurációs paraméterei egy *. env* fájlban találhatók:

```Text 
# Credentials
TENANT_ID=Enter_the_Tenant_Info_Here
CLIENT_ID=Enter_the_Application_Id_Here
CLIENT_SECRET=Enter_the_Client_Secret_Here

# Endpoints
AAD_ENDPOINT=Enter_the_Cloud_Instance_Id_Here
GRAPH_ENDPOINT=Enter_the_Graph_Endpoint_Here
```

# <a name="python"></a>[Python](#tab/python)

Amikor bizalmas ügyfelet hoz létre az ügyfél titkos kulcsaival, a [Python Daemon](https://github.com/Azure-Samples/ms-identity-python-daemon) -minta konfigurációs fájljának [parameters.js](https://github.com/Azure-Samples/ms-identity-python-daemon/blob/master/1-Call-MsGraph-WithSecret/parameters.json) a következő:

```Json
{
  "authority": "https://login.microsoftonline.com/<your_tenant_id>",
  "client_id": "your_client_id",
  "scope": [ "https://graph.microsoft.com/.default" ],
  "secret": "The secret generated by AAD during your confidential app registration",
  "endpoint": "https://graph.microsoft.com/v1.0/users"
}
```

Ha tanúsítványokkal rendelkező bizalmas ügyfelet hoz létre, a [Python Daemon](https://github.com/Azure-Samples/ms-identity-python-daemon) -minta konfigurációs fájljának [parameters.js](https://github.com/Azure-Samples/ms-identity-python-daemon/blob/master/2-Call-MsGraph-WithCertificate/parameters.json) a következő:

```Json
{
  "authority": "https://login.microsoftonline.com/<your_tenant_id>",
  "client_id": "your_client_id",
  "scope": [ "https://graph.microsoft.com/.default" ],
  "thumbprint": "790E... The thumbprint generated by AAD when you upload your public cert",
  "private_key_file": "server.pem",
  "endpoint": "https://graph.microsoft.com/v1.0/users"
}
```

---

### <a name="instantiate-the-msal-application"></a>A MSAL alkalmazás példányának példánya

A MSAL alkalmazás létrehozásához adja hozzá, hivatkozzon vagy importálja a MSAL-csomagot (a nyelvtől függően).

Az építkezés eltérő, attól függően, hogy az ügyfél titkos kulcsait vagy tanúsítványait használja (vagy speciális forgatókönyvként, aláírt kijelentésként).

#### <a name="reference-the-package"></a>A csomagra mutató hivatkozás

Hivatkozzon a MSAL csomagra az alkalmazás kódjában.

# <a name="net"></a>[.NET](#tab/dotnet)

Adja hozzá a [Microsoft. Identity. Client](https://www.nuget.org/packages/Microsoft.Identity.Client) NuGet-csomagot az alkalmazáshoz, majd adjon hozzá egy `using` direktívát a kódban a hivatkozáshoz.

A MSAL.NET-ben a bizalmas ügyfélalkalmazás az `IConfidentialClientApplication` illesztőfelületet jelképezi.

```csharp
using Microsoft.Identity.Client;
IConfidentialClientApplication app;
```

# <a name="java"></a>[Java](#tab/java)

```java
import com.microsoft.aad.msal4j.ClientCredentialFactory;
import com.microsoft.aad.msal4j.ClientCredentialParameters;
import com.microsoft.aad.msal4j.ConfidentialClientApplication;
import com.microsoft.aad.msal4j.IAuthenticationResult;
import com.microsoft.aad.msal4j.IClientCredential;
import com.microsoft.aad.msal4j.MsalException;
import com.microsoft.aad.msal4j.SilentParameters;
```

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

Egyszerűen telepítse a csomagokat `npm install` abban a mappában, ahol a *package.js* fájl található. Ezután importálja az **msal-Node** csomagot:

```JavaScript 
const msal = require('@azure/msal-node');
```

# <a name="python"></a>[Python](#tab/python)

```python
import msal
import json
import sys
import logging
```

---

#### <a name="instantiate-the-confidential-client-application-with-a-client-secret"></a>A bizalmas ügyfélalkalmazás példányának létrehozása az ügyfél titkos kódjával

Az alábbi kód segítségével hozhatja létre a bizalmas ügyfélalkalmazás egy ügyfél titkos kulcsával:

# <a name="net"></a>[.NET](#tab/dotnet)

```csharp
app = ConfidentialClientApplicationBuilder.Create(config.ClientId)
           .WithClientSecret(config.ClientSecret)
           .WithAuthority(new Uri(config.Authority))
           .Build();
```

A a `Authority` Felhőbeli példány és a bérlő azonosítójának összefűzése, például `https://login.microsoftonline.com/contoso.onmicrosoft.com` vagy `https://login.microsoftonline.com/eb1ed152-0000-0000-0000-32401f3f9abd` . A [konfigurációs fájl](#configuration-file) szakaszban láthatóappsettings.jsa (z) és a (z) értékben a (z) és értékek szerint jelennek *meg* `Instance` `Tenant` .

A kódban az előző kódrészletet a a `Authority`  [AuthenticationConfig](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/blob/ffc4a9f5d9bdba5303e98a1af34232b434075ac7/1-Call-MSGraph/daemon-console/AuthenticationConfig.cs#L61-L70) osztály egyik tulajdonsága határozza meg, amely a következőképpen van definiálva:

```csharp
/// <summary>
/// URL of the authority
/// </summary>
public string Authority
{
    get
    {
        return String.Format(CultureInfo.InvariantCulture, Instance, Tenant);
    }
}
```

# <a name="java"></a>[Java](#tab/java)

```Java
IClientCredential credential = ClientCredentialFactory.createFromSecret(CLIENT_SECRET);

ConfidentialClientApplication cca =
        ConfidentialClientApplication
                .builder(CLIENT_ID, credential)
                .authority(AUTHORITY)
                .build();
```

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

```JavaScript

const msalConfig = {
    auth: {
        clientId: process.env.CLIENT_ID,
        authority: process.env.AAD_ENDPOINT + process.env.TENANT_ID,
        clientSecret: process.env.CLIENT_SECRET,
    }
};

const apiConfig = {
    uri: process.env.GRAPH_ENDPOINT + 'v1.0/users',
};

const tokenRequest = {
    scopes: [process.env.GRAPH_ENDPOINT + '.default'],
};

const cca = new msal.ConfidentialClientApplication(msalConfig);
```

# <a name="python"></a>[Python](#tab/python)

```Python
# Pass the parameters.json file as an argument to this Python script. E.g.: python your_py_file.py parameters.json
config = json.load(open(sys.argv[1]))

# Create a preferably long-lived app instance that maintains a token cache.
app = msal.ConfidentialClientApplication(
    config["client_id"], authority=config["authority"],
    client_credential=config["secret"],
    # token_cache=...  # Default cache is in memory only.
                       # You can learn how to use SerializableTokenCache from
                       # https://msal-python.rtfd.io/en/latest/#msal.SerializableTokenCache
    )
```

---

#### <a name="instantiate-the-confidential-client-application-with-a-client-certificate"></a>A bizalmas ügyfélalkalmazás példányának létrehozása ügyféltanúsítványt

A következő kód segítségével hozhat létre egy alkalmazást egy tanúsítvánnyal:

# <a name="net"></a>[.NET](#tab/dotnet)

```csharp
X509Certificate2 certificate = ReadCertificate(config.CertificateName);
app = ConfidentialClientApplicationBuilder.Create(config.ClientId)
    .WithCertificate(certificate)
    .WithAuthority(new Uri(config.Authority))
    .Build();
```
# <a name="java"></a>[Java](#tab/java)

A MSAL Java-ban két építő található a bizalmas ügyfélalkalmazás tanúsítványokkal történő létrehozásához:

```Java

InputStream pkcs12Certificate = ... ; /* Containing PCKS12-formatted certificate*/
string certificatePassword = ... ;    /* Contains the password to access the certificate */

IClientCredential credential = ClientCredentialFactory.createFromCertificate(pkcs12Certificate, certificatePassword);

ConfidentialClientApplication cca =
        ConfidentialClientApplication
                .builder(CLIENT_ID, credential)
                .authority(AUTHORITY)
                .build();
```

vagy

```Java
PrivateKey key = getPrivateKey(); /* RSA private key to sign the assertion */
X509Certificate publicCertificate = getPublicCertificate(); /* x509 public certificate used as a thumbprint */

IClientCredential credential = ClientCredentialFactory.createFromCertificate(key, publicCertificate);

ConfidentialClientApplication cca =
        ConfidentialClientApplication
                .builder(CLIENT_ID, credential)
                .authority(AUTHORITY)
                .build();
```

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

A minta alkalmazás jelenleg nem valósítja meg az inicializálást a tanúsítványokkal.

# <a name="python"></a>[Python](#tab/python)

```Python
# Pass the parameters.json file as an argument to this Python script. E.g.: python your_py_file.py parameters.json
config = json.load(open(sys.argv[1]))

# Create a preferably long-lived app instance that maintains a token cache.
app = msal.ConfidentialClientApplication(
    config["client_id"], authority=config["authority"],
    client_credential={"thumbprint": config["thumbprint"], "private_key": open(config['private_key_file']).read()},
    # token_cache=...  # Default cache is in memory only.
                       # You can learn how to use SerializableTokenCache from
                       # https://msal-python.rtfd.io/en/latest/#msal.SerializableTokenCache
    )
```

---

#### <a name="advanced-scenario-instantiate-the-confidential-client-application-with-client-assertions"></a>Speciális forgatókönyv: a bizalmas ügyfélalkalmazás példánya az ügyfél-kijelentésekkel

# <a name="net"></a>[.NET](#tab/dotnet)

Az ügyfél titkos kulcsa vagy tanúsítványa helyett a bizalmas ügyfélalkalmazás is igazolhatja az identitását az ügyfél-kijelentések használatával.

A MSAL.NET kétféle módszerrel biztosítanak aláírt állításokat a bizalmas ügyfélalkalmazás számára:

- `.WithClientAssertion()`
- `.WithClientClaims()`

A használatakor `WithClientAssertion` adjon meg egy aláírt JWT. Ez a speciális forgatókönyv részletesen szerepel az [ügyfél-kijelentésekben](msal-net-client-assertions.md).

```csharp
string signedClientAssertion = ComputeAssertion();
app = ConfidentialClientApplicationBuilder.Create(config.ClientId)
                                          .WithClientAssertion(signedClientAssertion)
                                          .Build();
```

A használatakor a `WithClientClaims` MSAL.net egy aláírt jogcímet hoz létre, amely tartalmazza az Azure ad által várt jogcímeket, valamint az elküldeni kívánt további ügyfelek jogcímeit.
Ez a kód a következőket mutatja be:

```csharp
string ipAddress = "192.168.1.2";
var claims = new Dictionary<string, string> { { "client_ip", ipAddress } };
X509Certificate2 certificate = ReadCertificate(config.CertificateName);
app = ConfidentialClientApplicationBuilder.Create(config.ClientId)
                                          .WithAuthority(new Uri(config.Authority))
                                          .WithClientClaims(certificate, claims)
                                          .Build();
```

További részletekért lásd: [ügyfél-kijelentések](msal-net-client-assertions.md).

# <a name="java"></a>[Java](#tab/java)

```Java
IClientCredential credential = ClientCredentialFactory.createFromClientAssertion(assertion);

ConfidentialClientApplication cca =
        ConfidentialClientApplication
                .builder(CLIENT_ID, credential)
                .authority(AUTHORITY)
                .build();
```

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

A minta alkalmazás jelenleg nem valósítja meg az inicializálást az állításokkal.

# <a name="python"></a>[Python](#tab/python)

A MSAL Pythonban megadhatja az ügyfél jogcímeit a titkos kulcs által aláírt jogcímek használatával `ConfidentialClientApplication` .

```Python
# Pass the parameters.json file as an argument to this Python script. E.g.: python your_py_file.py parameters.json
config = json.load(open(sys.argv[1]))

# Create a preferably long-lived app instance that maintains a token cache.
app = msal.ConfidentialClientApplication(
    config["client_id"], authority=config["authority"],
    client_credential={"thumbprint": config["thumbprint"], "private_key": open(config['private_key_file']).read()},
    client_claims = {"client_ip": "x.x.x.x"}
    # token_cache=...  # Default cache is in memory only.
                       # You can learn how to use SerializableTokenCache from
                       # https://msal-python.rtfd.io/en/latest/#msal.SerializableTokenCache
    )
```

Részletekért tekintse meg a MSAL Python dokumentációját a [ConfidentialClientApplication](https://msal-python.readthedocs.io/en/latest/#msal.ClientApplication.__init__).

---

## <a name="next-steps"></a>Következő lépések

# <a name="net"></a>[.NET](#tab/dotnet)

Ebben a forgatókönyvben a következő cikkre léphet, amely [az alkalmazáshoz tartozó jogkivonatot szerzi](./scenario-daemon-acquire-token.md?tabs=dotnet)be.

# <a name="java"></a>[Java](#tab/java)

Ebben a forgatókönyvben a következő cikkre léphet, amely [az alkalmazáshoz tartozó jogkivonatot szerzi](./scenario-daemon-acquire-token.md?tabs=java)be.

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

Ebben a forgatókönyvben a következő cikkre léphet, amely [az alkalmazáshoz tartozó jogkivonatot szerzi](./scenario-daemon-acquire-token.md?tabs=nodejs)be.

# <a name="python"></a>[Python](#tab/python)

Ebben a forgatókönyvben a következő cikkre léphet, amely [az alkalmazáshoz tartozó jogkivonatot szerzi](./scenario-daemon-acquire-token.md?tabs=python)be.

---
