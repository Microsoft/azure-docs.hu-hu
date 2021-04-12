---
title: A webes API-kat meghívó webes API-k jogkivonatának beolvasása | Azure
titleSuffix: Microsoft identity platform
description: Ismerje meg, hogyan hozhat létre olyan webes API-t, amely meghívja az alkalmazás jogkivonatának beszerzését igénylő webes API-kat.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 07/15/2020
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 9f9758ec765ad34e5ef5d8b4d4e0a420a6701b6e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98756399"
---
# <a name="a-web-api-that-calls-web-apis-acquire-a-token-for-the-app"></a>Webes API-kat meghívó webes API: az alkalmazás jogkivonatának beszerzése

Miután létrehozott egy ügyfélalkalmazás-objektumot, használja azt egy olyan jogkivonat beszerzéséhez, amely a webes API-k meghívására használható.

## <a name="code-in-the-controller"></a>Kód a vezérlőben

### <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

A *Microsoft. Identity. Web* olyan bővítményi metódusokat ad meg, amelyek kényelmi szolgáltatásokat biztosítanak Microsoft Graph vagy alsóbb rétegbeli webes API meghívásához. Ezeket a módszereket részletesen ismerteti a [webes API-kat meghívó webes API-k: az API meghívása](scenario-web-api-call-api-call-api.md). Ezekkel a segítő módszerekkel nem kell manuálisan megvásárolnia a tokent.

Ha azonban a tokent manuálisan szeretné beszerezni, a következő kód egy példát mutat be a *Microsoft. Identity. Web* használatára egy API-vezérlőben. Egy *ToDoList* nevű alsóbb RÉTEGbeli API-t hív meg.
Az alárendelt API meghívására szolgáló jogkivonat lekéréséhez a `ITokenAcquisition` szolgáltatás a vezérlő konstruktorában (vagy az oldal konstruktorában, ha a Blazer használatakor) befecskendezi a szolgáltatást, és a vezérlő műveleteiben használja, a felhasználó ( `GetAccessTokenForUserAsync` ) vagy az alkalmazás () jogkivonatának beszerzése `GetAccessTokenForAppAsync` egy démoni forgatókönyv esetén.

```csharp
[Authorize]
public class MyApiController : Controller
{
    /// <summary>
    /// The web API will accept only tokens 1) for users, 2) that have the `access_as_user` scope for
    /// this API.
    /// </summary>
    static readonly string[] scopeRequiredByApi = new string[] { "access_as_user" };

     static readonly string[] scopesToAccessDownstreamApi = new string[] { "api://MyTodolistService/access_as_user" };

    private readonly ITokenAcquisition _tokenAcquisition;

    public MyApiController(ITokenAcquisition tokenAcquisition)
    {
        _tokenAcquisition = tokenAcquisition;
    }

    public IActionResult Index()
    {
        HttpContext.VerifyUserHasAnyAcceptedScope(scopeRequiredByApi);

        string accessToken = _tokenAcquisition.GetAccessTokenForUserAsync(scopesToAccessDownstreamApi);
        return await callTodoListService(accessToken);
    }
}
```

A metódussal kapcsolatos részletekért `callTodoListService` tekintse meg  [a webes API-kat meghívó webes API-t: az API meghívása](scenario-web-api-call-api-call-api.md)című témakört.

### <a name="java"></a>[Java](#tab/java)

Íme egy példa az API-vezérlők műveleteiben meghívott kódra. Meghívja az alsóbb rétegbeli API-Microsoft Graph.

```java
@RestController
public class ApiController {

    @Autowired
    MsalAuthHelper msalAuthHelper;

    @RequestMapping("/graphMeApi")
    public String graphMeApi() throws MalformedURLException {

        String oboAccessToken = msalAuthHelper.getOboToken("https://graph.microsoft.com/.default");

        return callMicrosoftGraphMeEndpoint(oboAccessToken);
    }

}
```

### <a name="python"></a>[Python](#tab/python)
 
A Python webes API-k a middleware használatával ellenőrzik az ügyféltől kapott tulajdonosi jogkivonatot. A webes API ezután a metódus meghívásával beszerezhet egy alsóbb rétegbeli API hozzáférési tokenjét a MSAL Python-könyvtár használatával [`acquire_token_on_behalf_of`](https://msal-python.readthedocs.io/en/latest/?badge=latest#msal.ConfidentialClientApplication.acquire_token_on_behalf_of) .
 
Íme egy példa arra a kódra, amely a `acquire_token_on_behalf_of` metódus és a lombik keretrendszer használatával szerzi be a hozzáférési tokent. Meghívja az alárendelt API-t – az Azure felügyeleti előfizetések végpontját.
 
```python
def get(self):
 
        _scopes = ["https://management.azure.com/user_impersonation"]
        _azure_management_subscriptions_uri = "https://management.azure.com/subscriptions?api-version=2020-01-01"
 
        current_access_token = request.headers.get("Authorization", None)
        
        #This example only uses the default memory token cache and should not be used for production
        msal_client = msal.ConfidentialClientApplication(
                client_id=os.environ.get("CLIENT_ID"),
                authority=os.environ.get("AUTHORITY"),
                client_credential=os.environ.get("CLIENT_SECRET"))
 
        #acquire token on behalf of the user that called this API
        arm_resource_access_token = msal_client.acquire_token_on_behalf_of(
            user_assertion=current_access_token.split(' ')[1],
            scopes=_scopes
        )
 
        headers = {'Authorization': arm_resource_access_token['token_type'] + ' ' + arm_resource_access_token['access_token']}
 
        subscriptions_list = req.get(_azure_management_subscriptions_uri), headers=headers).json()
 
        return jsonify(subscriptions_list)
```

## <a name="advanced-accessing-the-signed-in-users-token-cache-from-background-apps-apis-and-services"></a>Speciális A bejelentkezett felhasználó jogkivonat-gyorsítótárának elérése a háttérben futó alkalmazások, API-k és szolgáltatások között

[!INCLUDE [advanced-token-caching](../../../includes/advanced-token-cache.md)]

---

## <a name="next-steps"></a>Következő lépések

Lépjen be a következő cikkbe ebben a forgatókönyvben, [hívjon fel egy API](scenario-web-api-call-api-call-api.md)-t.
