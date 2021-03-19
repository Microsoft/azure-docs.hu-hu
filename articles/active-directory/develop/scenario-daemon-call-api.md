---
title: Webes API meghívása Daemon-alkalmazásból | Azure
titleSuffix: Microsoft identity platform
description: Megtudhatja, hogyan hozhat létre egy webes API-t meghívó Daemon-alkalmazást.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 10/30/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: f4b223c9195168b445b7eb81c2709a61807fddac
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "104578396"
---
# <a name="daemon-app-that-calls-web-apis---call-a-web-api-from-the-app"></a>Webes API-kat meghívó Daemon-alkalmazás – webes API meghívása az alkalmazásból

A .NET Daemon-alkalmazások hívhatnak webes API-t. A .NET Daemon-alkalmazások számos előre jóváhagyott webes API-t is meghívhatnak.

## <a name="calling-a-web-api-from-a-daemon-application"></a>Webes API meghívása egy Daemon-alkalmazásból

A token használatával a következő módon hívhat meg API-t:

# <a name="net"></a>[.NET](#tab/dotnet)

[!INCLUDE [Call web API in .NET](../../../includes/active-directory-develop-scenarios-call-apis-dotnet.md)]

# <a name="java"></a>[Java](#tab/java)

```Java
HttpURLConnection conn = (HttpURLConnection) url.openConnection();

// Set the appropriate header fields in the request header.
conn.setRequestProperty("Authorization", "Bearer " + accessToken);
conn.setRequestProperty("Accept", "application/json");

String response = HttpClientHelper.getResponseStringFromConn(conn);

int responseCode = conn.getResponseCode();
if(responseCode != HttpURLConnection.HTTP_OK) {
    throw new IOException(response);
}

JSONObject responseObject = HttpClientHelper.processResponse(responseCode, response);
```

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

HTTP-ügyfél (például [Axios](https://www.npmjs.com/package/axios)) használatával hívja meg az API-végponti URI-t egy hozzáférési jogkivonattal az *engedélyezési tulajdonosként*.

```JavaScript
const axios = require('axios');

async function callApi(endpoint, accessToken) {

    const options = {
        headers: {
            Authorization: `Bearer ${accessToken}`
        }
    };

    console.log('request made to web API at: ' + new Date().toString());

    try {
        const response = await axios.default.get(endpoint, options);
        return response.data;
    } catch (error) {
        console.log(error)
        return error;
    }
};
```

# <a name="python"></a>[Python](#tab/python)

```Python
endpoint = "url to the API"
http_headers = {'Authorization': 'Bearer ' + result['access_token'],
                'Accept': 'application/json',
                'Content-Type': 'application/json'}
data = requests.get(endpoint, headers=http_headers, stream=False).json()
```

---

## <a name="calling-several-apis"></a>Több API meghívása

A Daemon-alkalmazások esetében a hívott webes API-kat előzetesen jóvá kell hagyni. A Daemon-alkalmazásokhoz nem szükséges növekményes beleegyezni. (Nincs felhasználói beavatkozás.) A bérlői rendszergazdának előre meg kell adnia a belefoglalást az alkalmazáshoz és az összes API-engedélyhez. Ha több API-t szeretne meghívni, minden alkalommal meghívja az egyes erőforrásokhoz tartozó jogkivonatot `AcquireTokenForClient` . A MSAL az alkalmazás-jogkivonat gyorsítótárát fogja használni a szükségtelen szolgáltatási hívások elkerülése érdekében.

## <a name="next-steps"></a>Következő lépések

# <a name="net"></a>[.NET](#tab/dotnet)

Az ebben a forgatókönyvben lévő következő cikkre való áttéréssel váltson [éles környezetbe](./scenario-daemon-production.md?tabs=dotnet).

# <a name="java"></a>[Java](#tab/java)

Az ebben a forgatókönyvben lévő következő cikkre való áttéréssel váltson [éles környezetbe](./scenario-daemon-production.md?tabs=java).

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

Az ebben a forgatókönyvben lévő következő cikkre való áttéréssel váltson [éles környezetbe](./scenario-daemon-production.md?tabs=nodejs).

# <a name="python"></a>[Python](#tab/python)

Az ebben a forgatókönyvben lévő következő cikkre való áttéréssel váltson [éles környezetbe](./scenario-daemon-production.md?tabs=python).

---
