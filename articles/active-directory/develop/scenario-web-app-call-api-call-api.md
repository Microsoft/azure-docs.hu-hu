---
title: Webes API meghívása egy webalkalmazásból | Azure
titleSuffix: Microsoft identity platform
description: Ismerje meg, hogyan hozhat létre olyan webalkalmazást, amely webes API-kat hív meg (védett webes API-t hív meg)
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/25/2020
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: b294a56a523adaa2629a5d1e72a7ccef532956e0
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98753280"
---
# <a name="a-web-app-that-calls-web-apis-call-a-web-api"></a>Webes API-kat meghívó webalkalmazás: webes API meghívása

Most, hogy rendelkezik egy jogkivonattal, meghívhat egy védett webes API-t. Általában egy alsóbb rétegbeli API-t hív meg a webalkalmazás vezérlője vagy lapja.

## <a name="call-a-protected-web-api"></a>Védett webes API meghívása

A védett webes API-k meghívása a választott nyelvtől és keretrendszertől függ:

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

A *Microsoft. Identity. Web* használatakor három felhasználási lehetőség áll rendelkezésre az API meghívásához:

- [1. lehetőség: a Microsoft Graph meghívása az Microsoft Graph SDK-val](#option-1-call-microsoft-graph-with-the-sdk)
- [2. lehetőség: alárendelt webes API meghívása a segítő osztállyal](#option-2-call-a-downstream-web-api-with-the-helper-class)
- [3. lehetőség: alárendelt webes API meghívása a segítő osztály nélkül](#option-3-call-a-downstream-web-api-without-the-helper-class)

#### <a name="option-1-call-microsoft-graph-with-the-sdk"></a>1. lehetőség: a Microsoft Graph meghívása az SDK-val

Meg szeretné hívni Microsoft Graph. Ebben a forgatókönyvben az `AddMicrosoftGraph` *Indítás. cs* a [kód konfigurálása beállításban](scenario-web-app-call-api-app-configuration.md#option-1-call-microsoft-graph)megadott módon lett hozzáadva, és közvetlenül a `GraphServiceClient` vezérlőben vagy az oldal konstruktorában is befecskendezheti a műveleteket a műveletekben való használatra. A következő példában a borotva oldal a bejelentkezett felhasználó fényképét jeleníti meg.

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

Nem Microsoft Graph webes API-t szeretne meghívni. Ebben az esetben az `AddDownstreamWebApi` *Indítás. cs* a [kód konfigurálása beállításban](scenario-web-app-call-api-app-configuration.md#option-2-call-a-downstream-web-api-other-than-microsoft-graph)megadott módon lett hozzáadva, és közvetlenül is beadhat egy `IDownstreamWebApi` szolgáltatást a vezérlőben vagy az oldal konstruktorában, és használhatja azokat a műveletekben:

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
}
```

A `CallWebApiForUserAsync` szintén erősen begépelt általános felülbírálásokat tartalmaz, amelyek lehetővé teszik egy objektum közvetlen fogadását. A következő metódus például egy `Todo` példányt kap, amely a webes API által visszaadott JSON szigorúan beírt ábrázolása.

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

Úgy döntött, hogy a tokent manuálisan szerzi be a `ITokenAcquisition` szolgáltatás használatával, és most a tokent kell használnia. Ebben az esetben a következő kód folytatja a webes [API-kat meghívó webalkalmazásban megjelenő példa kódját: az alkalmazás jogkivonatának beszerzése](scenario-web-app-call-api-acquire-token.md). A kód a webalkalmazás-vezérlők műveleteiben hívható meg.

A token beszerzése után az alárendelt API meghívásához használja tulajdonosi jogkivonatként, ebben az esetben Microsoft Graph.

 ```csharp
public async Task<IActionResult> Profile()
{
 // Acquire the access token.
 string[] scopes = new string[]{"user.read"};
 string accessToken = await tokenAcquisition.GetAccessTokenForUserAsync(scopes);

 // Use the access token to call a protected web API.
 HttpClient client = new HttpClient();
 client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", accessToken);

  var response = await httpClient.GetAsync($"{webOptions.GraphApiUrl}/beta/me");

  if (response.StatusCode == HttpStatusCode.OK)
  {
   var content = await response.Content.ReadAsStringAsync();

   dynamic me = JsonConvert.DeserializeObject(content);
   ViewData["Me"] = me;
  }

  return View();
}
```
> [!NOTE]
> Ugyanezt az elvet használhatja bármely webes API meghívásához.
>
> A legtöbb Azure-beli webes API-k olyan SDK-t biztosítanak, amely leegyszerűsíti az API meghívását, mint a Microsoft Graph esetén. Lásd például: [hozzon létre egy webalkalmazást, amely engedélyezi a blob Storage-hoz való hozzáférést az Azure ad-vel](../../storage/common/storage-auth-aad-app.md?tabs=dotnet&toc=%2fazure%2fstorage%2fblobs%2ftoc.json) egy, a Microsoft. Identity. web és az Azure Storage SDK-t használó webalkalmazás számára.

# <a name="java"></a>[Java](#tab/java)

```Java
private String getUserInfoFromGraph(String accessToken) throws Exception {
    // Microsoft Graph user endpoint
    URL url = new URL("https://graph.microsoft.com/v1.0/me");

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
    return responseObject.toString();
}

```

# <a name="python"></a>[Python](#tab/python)

```Python
@app.route("/graphcall")
def graphcall():
    token = _get_token_from_cache(app_config.SCOPE)
    if not token:
        return redirect(url_for("login"))
    graph_data = requests.get(  # Use token to call downstream service.
        app_config.ENDPOINT,
        headers={'Authorization': 'Bearer ' + token['access_token']},
        ).json()
    return render_template('display.html', result=graph_data)
```

---

## <a name="next-steps"></a>Következő lépések

Az ebben a forgatókönyvben lévő következő cikkre való áttéréssel váltson [éles környezetbe](scenario-web-app-call-api-production.md).