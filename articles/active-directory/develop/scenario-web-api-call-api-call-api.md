---
title: Webes API-kat meghívó webes API | Azure
titleSuffix: Microsoft identity platform
description: Megtudhatja, hogyan hozhat létre webes API-kat meghívó webes API-t.
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
ms.openlocfilehash: bbb42a4955ff0b4fbbac58830ec5c8aecf04915d
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2021
ms.locfileid: "101686733"
---
# <a name="a-web-api-that-calls-web-apis-call-an-api"></a>Webes API-kat meghívó webes API: az API meghívása

A jogkivonat meghívása után meghívhat egy védett webes API-t. Általában az alsóbb rétegbeli API-kat hívja a webes API vezérlő vagy lapjai alapján.

## <a name="controller-code"></a>Vezérlő kódja

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

A *Microsoft. Identity. Web* használatakor három felhasználási forgatókönyv áll rendelkezésre:

- [Webes API-kat meghívó webes API: az API meghívása](#a-web-api-that-calls-web-apis-call-an-api)
  - [Vezérlő kódja](#controller-code)
- [ASP.NET Core](#aspnet-core)
      - [1. lehetőség: a Microsoft Graph meghívása az SDK-val](#option-1-call-microsoft-graph-with-the-sdk)
      - [2. lehetőség: alárendelt webes API meghívása a segítő osztállyal](#option-2-call-a-downstream-web-api-with-the-helper-class)
      - [3. lehetőség: alárendelt webes API meghívása a segítő osztály nélkül](#option-3-call-a-downstream-web-api-without-the-helper-class)
- [Java](#java)
- [Python](#python)
  - [Következő lépések](#next-steps)

#### <a name="option-1-call-microsoft-graph-with-the-sdk"></a>1. lehetőség: a Microsoft Graph meghívása az SDK-val

Ebben a forgatókönyvben a Startup.cs- `.AddMicrosoftGraph()` ben  megadott módon adta hozzá a [kódot](scenario-web-api-call-api-app-configuration.md#option-1-call-microsoft-graph), és közvetlenül a vezérlőben vagy az oldal konstruktorában is befecskendezheti a `GraphServiceClient` műveleteket a műveletekben való használatra. A következő példában a borotva oldal a bejelentkezett felhasználó fényképét jeleníti meg.

```CSharp
 [Authorize]
 [AuthorizeForScopes(Scopes = new[] { "user.read" })]
 public class IndexModel : PageModel
 {
     private readonly GraphServiceClient _graphServiceClient;

     public IndexModel(GraphServiceClient graphServiceClient)
     {
         _graphServiceClient = graphServiceClient;
     }

     public async Task OnGet()
     {
         var user = await _graphServiceClient.Me.Request().GetAsync();
         try
         {
             using (var photoStream = await _graphServiceClient.Me.Photo.Content.Request().GetAsync())
             {
                 byte[] photoByte = ((MemoryStream)photoStream).ToArray();
                 ViewData["photo"] = Convert.ToBase64String(photoByte);
             }
             ViewData["name"] = user.DisplayName;
         }
         catch (Exception)
         {
             ViewData["photo"] = null;
         }
     }
 }
```

#### <a name="option-2-call-a-downstream-web-api-with-the-helper-class"></a>2. lehetőség: alárendelt webes API meghívása a segítő osztállyal

Ebben a forgatókönyvben a Startup.cs- `.AddDownstreamWebApi()` ben  megadott módon adta hozzá a [kódot](scenario-web-api-call-api-app-configuration.md#option-2-call-a-downstream-web-api-other-than-microsoft-graph), és közvetlenül is befecskendezheti a `IDownstreamWebApi` szolgáltatást a vezérlőbe vagy az oldal konstruktorba, és használhatja azokat a műveletekben:

```CSharp
 [Authorize]
 [AuthorizeForScopes(ScopeKeySection = "TodoList:Scopes")]
 public class TodoListController : Controller
 {
     private IDownstreamWebApi _downstreamWebApi;
     private const string ServiceName = "TodoList";

     public TodoListController(IDownstreamWebApi downstreamWebApi)
     {
         _downstreamWebApi = downstreamWebApi;
     }

     public async Task<ActionResult> Details(int id)
     {
         var value = await _downstreamWebApi.CallWebApiForUserAsync(
             ServiceName,
             options =>
             {
                 options.RelativePath = $"me";
             });
         return View(value);
     }
```

A `CallWebApiForUserAsync` metódus olyan általános felülbírálásokat is tartalmaz, amelyek lehetővé teszik egy objektum közvetlen fogadását. A következő metódus például egy `Todo` példányt kapott, amely a webes API által visszaadott JSON szigorúan beírt ábrázolása.

```CSharp
 // GET: TodoList/Details/5
 public async Task<ActionResult> Details(int id)
 {
     var value = await _downstreamWebApi.CallWebApiForUserAsync<object, Todo>(
         ServiceName,
         null,
         options =>
         {
             options.HttpMethod = HttpMethod.Get;
             options.RelativePath = $"api/todolist/{id}";
         });
     return View(value);
 }
```

#### <a name="option-3-call-a-downstream-web-api-without-the-helper-class"></a>3. lehetőség: alárendelt webes API meghívása a segítő osztály nélkül

Ha úgy döntött, hogy a tokent manuálisan szerzi be a `ITokenAcquisition` szolgáltatás használatával, most a tokent kell használnia. Ebben az esetben a következő kód továbbra is a webes API-kat meghívó webes API-kon megjelenő példa kódját mutatja be [: az alkalmazáshoz tartozó jogkivonat beszerzése](scenario-web-api-call-api-acquire-token.md). A kódot az API-vezérlők műveleteiben hívják meg. Egy *ToDoList* nevű alsóbb RÉTEGbeli API-t hív meg.

 Miután megszerezte a tokent, használja tulajdonosi jogkivonatként az alsóbb rétegbeli API meghívásához.

```csharp
 private async Task CallTodoListService(string accessToken)
 {
  // After the token has been returned by Microsoft.Identity.Web, add it to the HTTP authorization header before making the call to access the todolist service.
 _httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", result.AccessToken);

 // Call the todolist service.
 HttpResponseMessage response = await _httpClient.GetAsync(TodoListBaseAddress + "/api/todolist");
 // ...
 }
 ```

# <a name="java"></a>[Java](#tab/java)

A következő kód folytatja a webes API-kat meghívó webes API-kon megjelenő példa kódját [: az alkalmazás jogkivonatának beszerzése](scenario-web-api-call-api-acquire-token.md). A kódot az API-vezérlők műveleteiben hívják meg. Meghívja az alsóbb rétegbeli API MS Graphot.

Miután megszerezte a tokent, használja tulajdonosi jogkivonatként az alsóbb rétegbeli API meghívásához.

```Java
private String callMicrosoftGraphMeEndpoint(String accessToken){
    RestTemplate restTemplate = new RestTemplate();

    HttpHeaders headers = new HttpHeaders();
    headers.setContentType(MediaType.APPLICATION_JSON);

    headers.set("Authorization", "Bearer " + accessToken);

    HttpEntity<String> entity = new HttpEntity<>(null, headers);

    String result = restTemplate.exchange("https://graph.microsoft.com/v1.0/me", HttpMethod.GET,
            entity, String.class).getBody();

    return result;
}
```

# <a name="python"></a>[Python](#tab/python)
A folyamatot bemutató minta a MSAL Python-val az [MS-Identity-Python-on-a nevében](https://github.com/Azure-Samples/ms-identity-python-on-behalf-of)érhető el.

---

## <a name="next-steps"></a>Következő lépések

Az ebben a forgatókönyvben lévő következő cikkre való áttéréssel váltson [éles környezetbe](scenario-web-api-call-api-production.md).
