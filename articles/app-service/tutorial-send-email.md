---
title: 'Oktatóanyag: e-mail küldése Logic Apps'
description: Megtudhatja, hogyan hívhat meg üzleti folyamatokat a App Service alkalmazásból. E-maileket, tweeteket és Facebook-bejegyzéseket küldhet, levelezési listákhoz adhat hozzá stb.
ms.topic: tutorial
ms.date: 04/08/2020
ms.custom: devx-track-csharp, mvc
ms.openlocfilehash: 6b15c09162146de7aef8abfcdba40627eb77621c
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2021
ms.locfileid: "107830933"
---
# <a name="tutorial-send-email-and-invoke-other-business-processes-from-app-service"></a>Oktatóanyag: E-mail küldése és más üzleti folyamatok meghívása a App Service

Ez az oktatóanyag bemutatja, hogyan integrálhatja a App Service az üzleti folyamatokkal. Ez gyakori a webalkalmazások esetében, például:

- Megerősítő e-mail küldése egy tranzakcióhoz
- Felhasználó hozzáadása Facebook-csoporthoz
- Csatlakozás olyan külső rendszerekhez, mint az SAP, a Salesforce stb.
- Szabványos B2B-üzenetek cseréje

Ebben az oktatóanyagban e-maileket fog küldeni a Gmail használatával a App Service alkalmazásból a [következő használatával: Azure Logic Apps.](../logic-apps/logic-apps-overview.md) Más módokon is küldhet e-maileket egy webalkalmazásból, például a nyelvi keretrendszer által biztosított SMTP-konfigurációt. A Logic Apps azonban sokkal nagyobb teljesítményt App Service alkalmazásában anélkül, hogy bonyolultabbá tené a kódot. Logic Apps egy egyszerű konfigurációs felületet biztosít a legnépszerűbb üzleti integrációkhoz, és az alkalmazás bármikor hívhatja őket EGY HTTP-kéréssel.

## <a name="prerequisite"></a>Előfeltétel

Üzembe helyezhet egy alkalmazást a választott nyelvi keretrendszer használatával, App Service. Mintaalkalmazás üzembe helyezését ismertető oktatóanyagot az alábbi módon követhet:

### <a name="aspnet"></a>[ASP.NET](#tab/dotnet)

[Oktatóanyag: ASP.NET-alkalmazás létrehozása az Azure-ban SQL Database használatával](app-service-web-tutorial-dotnet-sqldatabase.md)

### <a name="aspnet-core"></a>[ASP.NET Core](#tab/dotnetcore)

[Oktatóanyag: ASP.NET Core- és SQL Database-alkalmazás Azure App Service](tutorial-dotnetcore-sqldb-app.md)

### <a name="nodejs"></a>[Node.js](#tab/node)

[Oktatóanyag: Node.js MongoDB-alkalmazás összeállítása az Azure-ban](tutorial-nodejs-mongodb-app.md)

### <a name="php"></a>[PHP](#tab/php)

[Oktatóanyag: PHP- és MySQL-alkalmazás összeállítása az Azure-ban](tutorial-php-mysql-app.md)

### <a name="python"></a>[Python](#tab/python)

[Oktatóanyag: Python- (Django-) webalkalmazás futtatása a PostgreSQL-lel a Azure App Service](tutorial-python-postgresql-app.md)

### <a name="ruby"></a>[Ruby](#tab/ruby)

[Ruby- és Postgres-alkalmazás Azure App Service on Linux](tutorial-ruby-postgres-app.md)

---

## <a name="create-the-logic-app"></a>A logikai alkalmazás létrehozása

1. A [Azure Portal](https://portal.azure.com)hozzon létre egy üres logikai alkalmazást a Logikai alkalmazás [létrehozása útmutatását követve.](../logic-apps/quickstart-create-first-logic-app-workflow.md#create-your-logic-app) Amikor látja a **Logic Apps Designert,** térjen vissza ehhez az oktatóanyaghoz.
1. A Logic Apps Designer kezdőlapján válassza a Start with a common trigger (Kezdés közös eseményindítóval) lehetőség alatt a When an HTTP request is received an HTTP request is received **(HTTP-kérés** **érkezik) lehetőséget.**

    ![A képernyőképen a Logic Apps Designer kezdőlapja látható, a When a H T T P request is received (Ha T T P kérés érkezik) kiemeléssel.](./media/tutorial-send-email/receive-http-request.png)
1. A HTTP-kérések esetén párbeszédpanelen válassza **a** **Séma létrehozása hasznosadat-minta használatával lehetőséget.**

    ![Képernyőkép a When a H T T P request (Ha T T P kérelem) párbeszédpanelről és a Use sample payload to generate schema opion (Séma-művelet létrehozása mintaadatokkal) lehetőség be van jelölve. ](./media/tutorial-send-email/generate-schema-with-payload.png)

1. Másolja az alábbi JSON-mintát a szövegmezőbe, és válassza a **Kész lehetőséget.**

    ```json
    {
        "task": "<description>",
        "due": "<date>",
        "email": "<email-address>"
    }
    ```

    A rendszer létrehoz egy sémát a kívánt kérelemadatokhoz. A gyakorlatban egyszerűen rögzítheti az alkalmazás kódja által generált tényleges kérelemadatokat, és hagyhatja, hogy az Azure létrehozza a JSON-sémát. 
1. A tervező felső Logic Apps kattintson a **Mentés gombra.** 

    Most már láthatja a HTTP-kérés eseményindítója URL-címét. Kattintson a másolás ikonra a másoláshoz későbbi használatra.

    ![Képernyőkép a másolás ikonról, amely a H T T P kérési eseményindító U R L-ének másolását mutatja be.](./media/tutorial-send-email/http-request-url.png)

    Ez a HTTP-kérés-definíció egy eseményindító, amely minden olyan eseményindító, amit ebben a logikai alkalmazásban szeretne tenni, legyen szó Gmailről vagy bármi másról. Később ezt az URL-címet fogja meghívni a App Service alkalmazásban. A kérés eseményindítóval kapcsolatos további információkért tekintse meg a [HTTP-kérések/-válaszok referenciáját.](../connectors/connectors-native-reqres.md)

1. A tervező alján kattintson az Új lépés **elemre,** írja be a **Gmailt** a műveletek keresőmezőbe, majd keresse meg és válassza az E-mail küldése **(V2) lehetőséget.**
    
    > [!TIP]
    > Más típusú integrációkat is kereshet, mint például a SendGrid, a MailChimp, a Microsoft 365 és a SalesForce. További információt a [dokumentációban Logic Apps talál.](../logic-apps/index.yml)

1. A **Gmail párbeszédpanelen** válassza a **Bejelentkezés** lehetőséget, és jelentkezzen be arra a Gmail-fiókra, amelyről el szeretné küldeni az e-mailt.

    ![Képernyőkép a Gmail párbeszédpanelről, amelyről bejelentkezik arra a Gmail-fiókra, amelyről e-mailt szeretne küldeni.](./media/tutorial-send-email/gmail-sign-in.png)

1. Miután bejelentkezett, kattintson a **To (To)** szövegmezőre, és a dinamikus tartalom párbeszédpanel automatikusan megnyílik.

1. A **HTTP-kérések** esetén művelet mellett válassza a **További információk lehetőséget.**

    ![Képernyőkép az When a H T T P request is received action (H T T P kérés fogadott művelet esetén) mellett található See More (További információk) gombról.](./media/tutorial-send-email/expand-dynamic-content.png)

    Most a korábban használt JSON-mintaadatokból származó három tulajdonságnak kell látsza. Ebben a lépésben ezeket a tulajdonságokat használja a HTTP-kérésből egy e-mail felépítéséhez.
1. Mivel a Címzett mező értékét választja, **válassza** az e-mail **lehetőséget.** Ha szeretné, a Dinamikus tartalom hozzáadása gombra kattintva válthat a dinamikus tartalom **párbeszédpaneljére.**

    ![Képernyőkép az e-mail lehetőségről és a Dinamikus tartalom hozzáadása lehetőség kiemelésről.](./media/tutorial-send-email/hide-dynamic-content.png)

1. Az Add **new parameter (Új paraméter hozzáadása) legördülő** menüben válassza a Subject (Tárgy) és **a** **Body (Törzs) lehetőséget.**

1. Kattintson a **Tárgy szövegmezőre,** és ugyanígy válassza a feladat **lehetőséget.** Ha a kurzor továbbra is a **Tárgy mezőben** van, írja be a *created parancsot.* 

1. Kattintson a **törzsre,** és ugyanígy válassza a **due (esedékes) lehetőséget.** Vigye a kurzort a due (esedékesség) elem bal **oldali útjára,** és írja be *az This work item is due on (Ez a munkaelem a következőre esedékes) parancsot.*

    > [!TIP]
    > Ha közvetlenül az e-mail törzsében szeretné  szerkeszteni a HTML-tartalmat, válassza a Kódnézet lehetőséget a Logic Apps ablak tetején. Csak győződjön meg arról, hogy megtartja a dinamikus tartalomkódot `@{triggerBody()?['due']}` (például: )
    >
    > ![Képernyőkép a H T M L-tartalmak közvetlenül az e-mail törzsében való megtekintésére vonatkozó kódnézetről.](./media/tutorial-send-email/edit-rich-html-email.png) 

1. Ezután adjon hozzá egy aszinkron HTTP-választ a HTTP-eseményindítóhoz. A HTTP-eseményindító és a Gmail művelet között kattintson az jelre, **+** és válassza a Párhuzamos ág hozzáadása **lehetőséget.**

    ![Képernyőkép a + aláírás és a Párhuzamos ág hozzáadása lehetőség kiemelésével.](./media/tutorial-send-email/add-http-response.png)

1. A keresőmezőben keressen rá a válasz kifejezésre, majd válassza a **Válasz** műveletet.

    ![Képernyőkép a keresősávról és a kiemelt Response műveletről.](./media/tutorial-send-email/choose-response-action.png)

    Alapértelmezés szerint a válasz művelet http 200-as értéket küld. Ez elég ehhez az oktatóanyaghoz. További információkért tekintse meg a [HTTP-kérések/-válaszok referenciáját.](../connectors/connectors-native-reqres.md)

1. A tervező felső Logic Apps válassza ismét a **Mentés** lehetőséget. 

## <a name="add-http-request-code-to-app"></a>HTTP-kérelemkód hozzáadása az alkalmazáshoz

Győződjön meg arról, hogy kimásotta a HTTP-kérés eseményindítója URL-címét a korábbiból. Mivel bizalmas adatokat tartalmaz, ajánlott eljárás azokat nem közvetlenül a kódba tenni. A App Service környezeti változóként hivatkozhat rá az alkalmazásbeállítások használatával. 

A [Cloud Shell](https://shell.azure.com)hozza létre az alkalmazásbeállítást a következő paranccsal (cserélje le a *\<app-name>* , és *\<resource-group-name>* *\<logic-app-url>* adatokat):

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings LOGIC_APP_URL="<your-logic-app-url>"
```

A kódban egy szabványos HTTP-bejegyzést kell elküldenie az URL-címre a nyelvi keretrendszer számára elérhető bármely HTTP-ügyfélnyelv használatával, a következő konfigurációval:

- A kérelem törzse ugyanazt a JSON-formátumot tartalmazza, mint amit a logikai alkalmazásnak megadott:

    ```json
    {
        "task": "<description>",
        "due": "<date>",
        "email": "<email-address>"
    }
    ```

- A kérelem a fejlécet `Content-Type: application/json` tartalmazza. 
- A teljesítmény optimalizálása érdekében ha lehetséges, aszinkron módon küldje el a kérést.

Kattintson az alábbi előnyben részesített nyelv/keretrendszer lapra egy példa megtekintése érdekében.

### <a name="aspnet"></a>[ASP.NET](#tab/dotnet)

A ASP.NET elküldheti a HTTP-bejegyzést a [System.Net.Http.HttpClient osztály](/dotnet/api/system.net.http.httpclient) használatával. Például:

```csharp
// requires using System.Net.Http;
var client = new HttpClient();
// requires using System.Text.Json;
var jsonData = JsonSerializer.Serialize(new
{
    email = "a-valid@emailaddress.com",
    due = "4/1/2020",
    task = "My new task!"
});

HttpResponseMessage result = await client.PostAsync(
    // requires using System.Configuration;
    ConfigurationManager.AppSettings["LOGIC_APP_URL"],
    new StringContent(jsonData, Encoding.UTF8, "application/json"));
    
var statusCode = result.StatusCode.ToString();
```

Ha ezt a kódot az Oktatóanyag: [ASP.NET-alkalmazás](app-service-web-tutorial-dotnet-sqldatabase.md)létrehozása az Azure-ban a SQL Database használatával mintaalkalmazásban teszteli, az elem hozzáadása után a Létrehozás műveletben elküldhet egy megerősítést tartalmazó [](https://github.com/Azure-Samples/dotnet-sqldb-tutorial/blob/master/DotNetAppSqlDb/Controllers/TodosController.cs#L52-L63)e-mailt. `Todo` A fenti aszinkron kód használatának érdekében konvertálja a Létrehozás műveletet aszinkronra.

### <a name="aspnet-core"></a>[ASP.NET Core](#tab/dotnetcore)

A ASP.NET a HTTP-bejegyzést a [System.Net.Http.HttpClient osztály használatával küldheti](/dotnet/api/system.net.http.httpclient) el. Például:

```csharp
// requires using System.Net.Http;
var client = new HttpClient();
// requires using System.Text.Json;
var jsonData = JsonSerializer.Serialize(new
{
    email = "a-valid@emailaddress.com",
    due = "4/1/2020",
    task = "My new task!"
});

HttpResponseMessage result = await client.PostAsync(
    // Requires DI configuration to access app settings. See https://docs.microsoft.com/azure/app-service/configure-language-dotnetcore#access-environment-variables
    _configuration["LOGIC_APP_URL"],
    new StringContent(jsonData, Encoding.UTF8, "application/json"));
    
var statusCode = result.StatusCode.ToString();
```

> [!NOTE]
> Ez a kód a bemutató egyszerűsége miatt íródott. A gyakorlatban ne példányosodjon az egyes `HttpClient` kérések objektuma. Kövesse az [IHttpClientFactory használata rugalmas HTTP-kérések megvalósításához útmutatót.](/dotnet/architecture/microservices/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests)

Ha ezt a kódot az [Oktatóanyag: ASP.NET Core-](tutorial-dotnetcore-sqldb-app.md)és SQL Database-alkalmazás létrehozása a Azure App Service-ben mintaalkalmazásban teszteli, az [](https://github.com/Azure-Samples/dotnetcore-sqldb-tutorial/blob/master/Controllers/TodosController.cs#L56-L65)elem hozzáadása után a Létrehozás műveletben elküldhet egy megerősítést tartalmazó e-mailt. `Todo`

### <a name="nodejs"></a>[Node.js](#tab/node)

A Node.js egyszerűen elküldheti a HTTP-bejegyzést egy npm-csomaggal, például [az axios-sel.](https://www.npmjs.com/package/axios) Például:

```javascript
// Requires npm install --save axios
const axios = require('axios');

var jsonData = {
        email: "a-valid@emailaddress.com",
        due: "4/1/2020",
        task: "My new task!"
};

(async function(data) {
    try {
        const response = await axios.post(process.env.LOGIC_APP_URL, jsonData);
        console.log(response.status);
    } catch (error) {
        console.log(error);
    }
})(jsonData);

```

Ha ezt a kódot az Oktatóanyag: Node.js- és [MongoDB-alkalmazás](tutorial-nodejs-mongodb-app.md)létrehozása az Azure-ban mintaalkalmazásban teszteli, a használatával a cikk sikeres mentése után elküldhet egy megerősítést kérő e-mailt a [create](https://github.com/Azure-Samples/meanjs/blob/master/modules/articles/server/controllers/articles.server.controller.js#L14-L27) [függvényben.](https://github.com/Azure-Samples/meanjs/blob/master/modules/articles/server/controllers/articles.server.controller.js#L24)

### <a name="php"></a>[PHP](#tab/php)

A PHP-ben egyszerűen elküldheti a HTTP-bejegyzést a [Guzzle használatával.](http://docs.guzzlephp.org/en/stable/index.html) Például:

```php
// Requires composer require guzzlehttp/guzzle:~6.0
use GuzzleHttp\Client;
...
$client = new Client();
$options = [
    'json' => [ 
        'email' => 'a-valid@emailaddress.com',
        'due' => '4/1/2020',
        'task' => "My new task!"
    ]
];

$promise = $client-> postAsync(getenv($LOGIC_APP_URL), $options)->then( 
    function ($response) {
        return $response->getStatusCode();
    }, function ($exception) {
        return $exception->getResponse();
    }
);

$response = $promise->wait();
// Requires Laravel to run Log::info(). Check the documentation of your preferred framework for logging instructions.
Log::info(print_r($response, TRUE));
```

Ha ezt a kódot [az Oktatóanyag: PHP-](tutorial-php-mysql-app.md)és MySQL-alkalmazás Azure-ban való létrehozásához készült mintaalkalmazásban teszteli, a használatával e-mailben megerősíthet a [Route::p ost](https://github.com/Azure-Samples/laravel-tasks/blob/master/routes/web.php#L30-L48)függvényben, a visszatérési utasítás előtt.

### <a name="python"></a>[Python](#tab/python)

A Pythonban egyszerűen küldheti el a HTTP-bejegyzést [a kérésekkel.](https://pypi.org/project/requests/) Például:

```python
# Requires pip install requests && pip freeze > requirements.txt
import requests
import os
...
payload = {
    "email": "a-valid@emailaddress.com",
    "due": "4/1/2020",
    "task": "My new task!"
}
response = requests.post(os.environ['LOGIC_APP_URL'], data = payload)
print(response.status_code)
```
<!-- ```python
# Requires pip install aiohttp && pip freeze > requirements.txt
import aiohttp
...
payload = {
        'email': 'a-valid@emailaddress.com',
        'due': '4/1/2020',
        'task': 'My new task!'
}
async with aiohttp.post('http://httpbin.org/post', data=json.dump(payload)) as resp:
    print(await resp.status())
``` -->

Ha ezt a kódot az [Oktatóanyag: Python- (Django-) webalkalmazás futtatása a PostgreSQL-lel](tutorial-python-postgresql-app.md)a Azure App Service-ben mintaalkalmazásban teszteli, a használatával e-mailt küldhet a [Route::p ost függvényben,](https://github.com/Azure-Samples/laravel-tasks/blob/master/routes/web.php#L30-L48)a visszatérési utasítás előtt.

### <a name="ruby"></a>[Ruby](#tab/ruby)

A Rubyban egyszerűen elküldheti a HTTP-bejegyzést a JSONClient használatával. Például:

```ruby
clnt = JSONClient.new
body = { 
    'email' => 'a-valid@emailaddress.com',
    'due' => '4/1/2020',
    'task' => "My new task!"
}

connection = clnt.post_async(ENV['LOGIC_APP_URL'], body)
```

Ha ezt a kódot a Ruby- és [Postgres-alkalmazás](tutorial-ruby-postgres-app.md)Azure App Service on Linux-ben való létrehozásához használt mintaalkalmazásban [](https://github.com/Azure-Samples/rubyrails-tasks/blob/master/app/controllers/tasks_controller.rb#L26-L38) teszteli, akkor használhatja a létrehozási műveletben megerősítő e-mail elküldését, ha a [ @task.save művelet sikeres.](https://github.com/Azure-Samples/rubyrails-tasks/blob/master/app/controllers/tasks_controller.rb#L30)

---

## <a name="more-resources"></a>További erőforrások

[Oktatóanyag: CORS-támogatással rendelkező RESTful API üzemeltetése az Azure App Service-ben](app-service-web-tutorial-rest-api.md)  
[HTTP-kérések/-válaszok referenciája Logic Apps](../connectors/connectors-native-reqres.md)  
[Rövid útmutató: Az első munkafolyamat létrehozása a Azure Logic Apps – Azure Portal](../logic-apps/quickstart-create-first-logic-app-workflow.md)
