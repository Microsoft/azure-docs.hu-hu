---
title: 'Oktatóanyag: e-mail küldése Logic Apps'
description: Megtudhatja, hogyan hívhat meg üzleti folyamatokat a App Service alkalmazásból. E-mailek, tweetek és Facebook-bejegyzések, Hozzáadás a levelezési listához és sok más egyéb üzenet küldése.
ms.topic: tutorial
ms.date: 04/08/2020
ms.custom: devx-track-csharp, mvc
ms.openlocfilehash: 8072a941cd89290af3e25cc63c4fccccce705df9
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "95014662"
---
# <a name="tutorial-send-email-and-invoke-other-business-processes-from-app-service"></a>Oktatóanyag: e-mail küldése és más üzleti folyamatok meghívása App Service

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a App Service alkalmazást az üzleti folyamataival. Ez a webalkalmazás-forgatókönyvek esetében gyakori, például a következő esetekben:

- Tranzakció megerősítő e-mail-címének küldése
- Felhasználó felvétele a Facebook-csoportba
- Kapcsolódjon külső rendszerekhez, például SAP, Salesforce stb.
- Exchange standard B2B-üzenetek

Ebben az oktatóanyagban e-maileket küld a Gmailben a App Service alkalmazásból [Azure Logic apps](../logic-apps/logic-apps-overview.md)használatával. Más módokon is küldhet e-maileket egy webalkalmazásból, például a nyelvi keretrendszer által biztosított SMTP-konfigurációból. Logic Apps azonban sokkal nagyobb hatékonyságot biztosít a App Service alkalmazásnak anélkül, hogy komplexitást kellene hozzáadnia a kódhoz. A Logic Apps egy egyszerű konfigurációs felületet biztosít a legnépszerűbb üzleti integrációk számára, és az alkalmazás egy HTTP-kéréssel bármikor meghívhatja őket.

## <a name="prerequisite"></a>Előfeltétel

Helyezzen üzembe egy alkalmazást az Ön által választott nyelvi keretrendszerrel App Service. A minta alkalmazás üzembe helyezésére vonatkozó oktatóanyag követéséhez tekintse meg az alábbi lépéseket:

### <a name="aspnet"></a>[ASP.NET](#tab/dotnet)

[Oktatóanyag: ASP.NET-alkalmazás létrehozása az Azure-ban SQL Database használatával](app-service-web-tutorial-dotnet-sqldatabase.md)

### <a name="aspnet-core"></a>[ASP.NET Core](#tab/dotnetcore)

[Oktatóanyag: ASP.NET Core és SQL Database alkalmazás létrehozása Azure App Service](tutorial-dotnetcore-sqldb-app.md)

### <a name="nodejs"></a>[Node.js](#tab/node)

[Oktatóanyag: Node.js-és MongoDB-alkalmazás létrehozása az Azure-ban](tutorial-nodejs-mongodb-app.md)

### <a name="php"></a>[PHP](#tab/php)

[Oktatóanyag: PHP-és MySQL-alkalmazás létrehozása az Azure-ban](tutorial-php-mysql-app.md)

### <a name="python"></a>[Python](#tab/python)

[Oktatóanyag: Python-(Django-) webalkalmazás futtatása a PostgreSQL-sel Azure App Service](tutorial-python-postgresql-app.md)

### <a name="ruby"></a>[Ruby](#tab/ruby)

[Ruby-és postgres-alkalmazás létrehozása Linuxon Azure App Service](tutorial-ruby-postgres-app.md)

---

## <a name="create-the-logic-app"></a>A logikai alkalmazás létrehozása

1. A [Azure Portal](https://portal.azure.com)hozzon létre egy üres logikai alkalmazást a [logikai alkalmazás létrehozása](../logic-apps/quickstart-create-first-logic-app-workflow.md#create-your-logic-app)című témakör útmutatásai alapján. Amikor megjelenik a **Logic apps Designer**, térjen vissza ehhez az oktatóanyaghoz.
1. A Logic Apps Designer Splash (kikapcsolt) oldalán válassza ki, hogy **mikor érkezik HTTP-kérelem** a **Start és a Common trigger együttes** használata esetén.

    ![Képernyőkép, amely megjeleníti a Logic Apps Designer Splash lapját, ha egy H T t P-kérelem kiemelése megtörtént.](./media/tutorial-send-email/receive-http-request.png)
1. A **http-kérés fogadásakor** a párbeszédpanelen válassza a **minta hasznos adatok használata a séma létrehozásához** lehetőséget.

    ![Képernyőkép, amely megjeleníti a H T T P-kérések párbeszédpanelét, valamint a minta hasznos adatok használatával létrehozott séma-opion. ](./media/tutorial-send-email/generate-schema-with-payload.png)

1. Másolja a következő minta JSON-t a szövegmezőbe, majd válassza a **kész** lehetőséget.

    ```json
    {
        "task": "<description>",
        "due": "<date>",
        "email": "<email-address>"
    }
    ```

    A rendszer most létrehozta a sémát a kívánt kérelemhez. A gyakorlatban egyszerűen rögzítheti az alkalmazás kódjának tényleges kérelmi adatait, és lehetővé teszi, hogy az Azure létrehozza a JSON-sémát. 
1. A Logic Apps Designer tetején válassza a **Mentés** lehetőséget. 

    Ekkor megjelenik a HTTP-kérelem triggerének URL-címe. A másolás ikonra kattintva másolja azt későbbi használatra.

    ![Képernyőkép, amely kiemeli a másolási ikont, és másolja a H R L-t a H T t P-kérelmi triggerbe.](./media/tutorial-send-email/http-request-url.png)

    Ez a HTTP-kérelem definíciója minden olyan trigger, amelyet a logikai alkalmazásban szeretne elvégezni, legyen az Gmail vagy bármi más. Később meg fogja hívni ezt az URL-címet a App Service alkalmazásban. További információ a kérelem-triggerről: http- [kérés/-válasz hivatkozása](../connectors/connectors-native-reqres.md).

1. A tervező alján kattintson az **új lépés** gombra, írja be a **Gmail** kifejezést a műveletek keresőmezőbe, és keresse meg és válassza az **e-mail küldése (v2)** lehetőséget.
    
    > [!TIP]
    > Más típusú integrációkat is kereshet, például a SendGrid, a MailChimp, a Microsoft 365 és a SalesForce. További információ: [Logic apps dokumentáció](../logic-apps/index.yml).

1. A **Gmail** párbeszédpanelen válassza a **Bejelentkezés** lehetőséget, és jelentkezzen be arra a Gmail-fiókba, ahová el szeretné küldeni az e-mailt.

    ![Képernyőkép, amely a Gmail-fiókba való bejelentkezéshez használt Gmail-párbeszédpanelt jeleníti meg, amelyről e-mailt szeretne küldeni.](./media/tutorial-send-email/gmail-sign-in.png)

1. Miután bejelentkezett, kattintson a **to** szövegmezőbe, és a dinamikus tartalom párbeszédablak automatikusan megnyílik.

1. A HTTP- **kérelem fogadása** művelet mellett válassza a **továbbiak** lehetőséget.

    ![Képernyőkép: a továbbiak gomb melletti, ha egy H T t P-kérelem érkezik művelet.](./media/tutorial-send-email/expand-dynamic-content.png)

    Ekkor megjelenik a korábban használt JSON-adatok három tulajdonsága. Ebben a lépésben ezeket a tulajdonságokat a HTTP-kérelemből használhatja e-mailek létrehozásához.
1. Mivel kiválasztja a **to** mező értékét, válassza az **e-mail** lehetőséget. Ha szeretné, a dinamikus tartalom **hozzáadása** gombra kattintva kapcsolja ki a dinamikus tartalom párbeszédpanelt.

    ![Képernyőkép, amely megjeleníti az e-mail-beállítást, és kiemelte a dinamikus tartalom hozzáadása lehetőséget.](./media/tutorial-send-email/hide-dynamic-content.png)

1. Az **új paraméter hozzáadása** legördülő menüben válassza a **Tárgy** és a **törzs** lehetőséget.

1. Kattintson a **Tárgy** szövegmezőre, és válassza a **feladat** lehetőséget. A kurzor továbbra is a **Tárgy** mezőbe írja be a következőt *: létrehozva*. 

1. Kattintson a **törzsre**, és ugyanígy válassza a **megfelelő** lehetőséget. Vigye a kurzort a **megfelelő** értékre, és írja be *ezt a munkaelemet*.

    > [!TIP]
    > Ha közvetlenül az e-mail szövegtörzsében szeretné szerkeszteni a HTML-tartalmat, válassza a Logic Apps Designer ablak tetején található **kód nézet** lehetőséget. Ügyeljen rá, hogy megőrizze a dinamikus tartalom kódját (például: `@{triggerBody()?['due']}` ).
    >
    > ![Képernyőkép, amely bemutatja, hogy a rendszer hogyan jeleníti meg a H T M L tartalom megtekintését közvetlenül az e-mail törzsében.](./media/tutorial-send-email/edit-rich-html-email.png) 

1. Ezután adjon hozzá egy aszinkron HTTP-választ a HTTP-triggerhez. A HTTP-trigger és a Gmail művelet között kattintson a **+** jelre, majd válassza a **párhuzamos ág hozzáadása** elemet.

    ![Képernyőkép, amely megjeleníti a + jelet, és hozzáad egy párhuzamos ág lehetőséget.](./media/tutorial-send-email/add-http-response.png)

1. A keresőmezőbe írja be a **Válasz** kifejezést, majd válassza ki a **Válasz** műveletet.

    ![Képernyőkép, amely a keresősáv és a válasz művelet kiemelését jeleníti meg.](./media/tutorial-send-email/choose-response-action.png)

    Alapértelmezés szerint a Response művelet HTTP 200-et küld. Ez elég jó ehhez az oktatóanyaghoz. További információ: [http-kérés/válasz referenciája](../connectors/connectors-native-reqres.md).

1. A Logic Apps Designer tetején válassza a **Mentés** újra lehetőséget. 

## <a name="add-http-request-code-to-app"></a>HTTP-kérelem kódjának hozzáadása az alkalmazáshoz

Győződjön meg arról, hogy a korábban a HTTP-kérelem triggerének URL-címét másolta. Mivel bizalmas adatokat tartalmaz, az ajánlott eljárás, hogy közvetlenül a kódban ne helyezze el. A App Service segítségével Ehelyett környezeti változóként hivatkozhat az Alkalmazásbeállítások használatával. 

A [Cloud Shell](https://shell.azure.com)hozza létre az alkalmazás beállítását a következő paranccsal (cserélje le *\<app-name>* , *\<resource-group-name>* és *\<logic-app-url>* ):

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings LOGIC_APP_URL="<your-logic-app-url>"
```

A kódban szabványos HTTP-bejegyzést hajtson végre az URL-címre a nyelvi keretrendszerben elérhető bármely HTTP-ügyfél nyelvén, a következő konfigurációval:

- A kérelem törzse ugyanazt a JSON-formátumot tartalmazza, amelyet a logikai alkalmazáshoz adott meg:

    ```json
    {
        "task": "<description>",
        "due": "<date>",
        "email": "<email-address>"
    }
    ```

- A kérelem tartalmazza a fejlécet `Content-Type: application/json` . 
- A teljesítmény optimalizálása érdekében a kérést aszinkron módon küldje el, ha lehetséges.

Egy példa megjelenítéséhez kattintson az alábbi előnyben részesített nyelv/keretrendszer lapra.

### <a name="aspnet"></a>[ASP.NET](#tab/dotnet)

A ASP.NET-ben a HTTP-bejegyzést a [System .net. http. HttpClient](/dotnet/api/system.net.http.httpclient) osztály használatával küldheti el. Például:

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

Ha teszteli ezt a kódot a következő oktatóanyag-alkalmazásban [: ASP.NET-alkalmazás létrehozása az Azure-ban a SQL Database](app-service-web-tutorial-dotnet-sqldatabase.md)használatával, a [létrehozási művelettel](https://github.com/Azure-Samples/dotnet-sqldb-tutorial/blob/master/DotNetAppSqlDb/Controllers/TodosController.cs#L52-L63)az elem hozzáadása után elküldheti e-mailben a megerősítést `Todo` . A fenti aszinkron kód használatához alakítsa át a Create műveletet aszinkron értékre.

### <a name="aspnet-core"></a>[ASP.NET Core](#tab/dotnetcore)

ASP.NET Core a HTTP-bejegyzést a [System .net. http. HttpClient](/dotnet/api/system.net.http.httpclient) osztály használatával küldheti el. Például:

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
> Ez a kód a bemutató egyszerűségére íródott. A gyakorlatban ne hozza létre az `HttpClient` összes kérelemhez tartozó objektumot. A [rugalmas HTTP-kérések megvalósításához kövesse a IHttpClientFactory használata](/dotnet/architecture/microservices/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests)című témakör útmutatását.

Ha teszteli ezt a kódot a minta alkalmazásban [oktatóanyag: ASP.net Core létrehozása és SQL Database alkalmazás](tutorial-dotnetcore-sqldb-app.md)létrehozása a Azure app Service-ben, akkor a használatával e-mailt küldhet a [létrehozási műveletben](https://github.com/Azure-Samples/dotnetcore-sqldb-tutorial/blob/master/Controllers/TodosController.cs#L56-L65)az `Todo` elem hozzáadása után.

### <a name="nodejs"></a>[Node.js](#tab/node)

Node.js a HTTP-bejegyzést egyszerűen elküldheti egy olyan NPM-csomaggal, mint például a [axios](https://www.npmjs.com/package/axios). Például:

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

Ha teszteli ezt a kódot a minta alkalmazásban [oktatóanyag: Node.js-és MongoDB-alkalmazás](tutorial-nodejs-mongodb-app.md)létrehozása az Azure-ban, akkor azt is elküldheti, hogy e-mail-megerősítést küldjön a [create függvényben](https://github.com/Azure-Samples/meanjs/blob/master/modules/articles/server/controllers/articles.server.controller.js#L14-L27) [a cikk sikeres mentése](https://github.com/Azure-Samples/meanjs/blob/master/modules/articles/server/controllers/articles.server.controller.js#L24)után.

### <a name="php"></a>[PHP](#tab/php)

A PHP-ben egyszerűen elküldheti a HTTP-Postot a [zabál](http://docs.guzzlephp.org/en/stable/index.html)használatával. Például:

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

Ha teszteli ezt a kódot a minta alkalmazásban az [oktatóanyaghoz: php-és MySQL-alkalmazás létrehozása az Azure-ban](tutorial-php-mysql-app.md), akkor azt is megteheti, hogy e-mail-visszaigazolást küld a [Route::p OST függvényben](https://github.com/Azure-Samples/laravel-tasks/blob/master/routes/web.php#L30-L48), közvetlenül a Return utasítás előtt.

### <a name="python"></a>[Python](#tab/python)

A Pythonban egyszerűen elküldheti a HTTP-bejegyzést a [kérelmekkel](https://pypi.org/project/requests/). Például:

```python
# Requires pip install requests && pip freeze > requirements.txt
import requests
...
payload = {
    "email": "a-valid@emailaddress.com",
    "due": "4/1/2020",
    "task": "My new task!"
}
response = requests.post("https://prod-112.westeurope.logic.azure.com:443/workfl$
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

Ha ezt a kódot a minta alkalmazásban a következő [oktatóanyagban teszteli: Python-(Django-) webalkalmazás futtatása a PostgreSQL-](tutorial-python-postgresql-app.md)sel a Azure app Service-ben, akkor azt is megteheti, hogy e-mail-visszaigazolást küld a [Route::p OST függvénynek](https://github.com/Azure-Samples/laravel-tasks/blob/master/routes/web.php#L30-L48), közvetlenül a Return utasítás előtt.

### <a name="ruby"></a>[Ruby](#tab/ruby)

A Ruby szolgáltatásban egyszerűen elküldheti a HTTP-Postot a JSONClient. Például:

```ruby
clnt = JSONClient.new
body = { 
    'email' => 'a-valid@emailaddress.com',
    'due' => '4/1/2020',
    'task' => "My new task!"
}

connection = clnt.post_async(ENV['LOGIC_APP_URL'], body)
```

Ha ezt a kódot a alkalmazásban a [Ruby és a postgres alkalmazás](tutorial-ruby-postgres-app.md)létrehozásához használja a linuxon Azure app Service, akkor a használatával e-mailben visszaigazolhatja a [létrehozási](https://github.com/Azure-Samples/rubyrails-tasks/blob/master/app/controllers/tasks_controller.rb#L26-L38) műveletet, [Ha a @task.save sikeres](https://github.com/Azure-Samples/rubyrails-tasks/blob/master/app/controllers/tasks_controller.rb#L30).

---

## <a name="more-resources"></a>További erőforrások

[Oktatóanyag: CORS-támogatással rendelkező RESTful API üzemeltetése az Azure App Service-ben](app-service-web-tutorial-rest-api.md)  
[HTTP-kérelem/-válasz referenciája Logic Apps](../connectors/connectors-native-reqres.md)  
[Rövid útmutató: az első munkafolyamat létrehozása Azure Logic Apps-Azure Portal használatával](../logic-apps/quickstart-create-first-logic-app-workflow.md)