---
title: Azure Functions összekötése a Visual Studio Code használatával Azure Cosmos DB
description: Megtudhatja, hogyan csatlakoztathat Azure Functionst egy Azure Cosmos DB-fiókhoz egy kimeneti kötés hozzáadásával a Visual Studio Code-projekthez.
author: ThomasWeiss
ms.date: 03/23/2021
ms.topic: quickstart
ms.author: thweiss
zone_pivot_groups: programming-languages-set-functions-temp
ms.openlocfilehash: 0a0c63ee54699185bcd02104b1a3f4d0070ea808
ms.sourcegitcommit: a8ff4f9f69332eef9c75093fd56a9aae2fe65122
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2021
ms.locfileid: "105023248"
---
# <a name="connect-azure-functions-to-azure-cosmos-db-using-visual-studio-code"></a>Azure Functions összekötése a Visual Studio Code használatával Azure Cosmos DB

[!INCLUDE [functions-add-storage-binding-intro](../../includes/functions-add-storage-binding-intro.md)]

Ez a cikk bemutatja, hogyan használható a Visual Studio Code a [Azure Cosmos db](../cosmos-db/introduction.md) az előző rövid útmutató cikkében létrehozott függvényhez való összekapcsolásához. Az ehhez a függvényhez hozzáadott kimeneti kötés adatokat ír a HTTP-kérelemből egy Azure Cosmos DB tárolóban tárolt JSON-dokumentumba. 

::: zone pivot="programming-language-csharp"
Mielőtt elkezdené, el kell végeznie a rövid útmutató [: C#-függvény létrehozása az Azure-ban a Visual Studio Code használatával](create-first-function-vs-code-csharp.md). Ha a cikk végén már megtisztította az erőforrásokat, folytassa újra a Function app és a kapcsolódó erőforrások Azure-ban való újbóli létrehozásához szükséges lépéseket.
::: zone-end
::: zone pivot="programming-language-javascript"  
Mielőtt elkezdené, el kell végeznie a rövid útmutató [: JavaScript-függvény létrehozása az Azure-ban a Visual Studio Code használatával](create-first-function-vs-code-node.md). Ha a cikk végén már megtisztította az erőforrásokat, folytassa újra a Function app és a kapcsolódó erőforrások Azure-ban való újbóli létrehozásához szükséges lépéseket.  
::: zone-end

## <a name="configure-your-environment"></a>A környezet konfigurálása

Az első lépések előtt telepítse az [Azure Databases bővítményt](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-cosmosdb) a Visual Studio Code-hoz.

## <a name="create-your-azure-cosmos-db-account"></a>Azure Cosmos DB fiók létrehozása

> [!IMPORTANT]
> A [kiszolgáló nélküli Azure Cosmos db](../cosmos-db/serverless.md) mostantól előzetes verzióban érhető el. Ez a fogyasztáson alapuló mód lehetővé teszi, hogy a kiszolgáló nélküli munkaterhelések esetében Azure Cosmos DB egy erős beállítást. Ha kiszolgáló nélküli módban szeretné használni a Azure Cosmos DBt, válassza a **kiszolgáló** nélküli lehetőséget a fiók létrehozásakor **kapacitás módban** .

1. Egy új böngészőablakban jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

2. Kattintson az **Erőforrás létrehozása** > **Adatbázisok** > **Azure Cosmos DB** lehetőségre.
   
    :::image type="content" source="../../includes/media/cosmos-db-create-dbaccount/create-nosql-db-databases-json-tutorial-1.png" alt-text="Az Azure Portal Adatbázisok panelje" border="true":::

3. A **Azure Cosmos db fiók létrehozása** lapon adja meg az új Azure Cosmos db-fiók beállításait. 
 
    Beállítás|Érték|Leírás
    ---|---|---
    Előfizetés|*Az előfizetése*|Válassza ki azt az Azure-előfizetést, amelyben a függvényalkalmazást az [előző cikkben](./create-first-function-vs-code-csharp.md)hozta létre.
    Erőforráscsoport|*Az erőforráscsoport*|Válassza ki azt az erőforráscsoportot, amelyben az [előző cikkben](./create-first-function-vs-code-csharp.md)létrehozta a függvényalkalmazás.
    Fiók neve|*Adjon meg egy egyedi nevet*|Adjon meg egy egyedi nevet az Azure Cosmos DB-fiók azonosításához.<br><br>A fiók neve csak kisbetűket, számokat és kötőjeleket (-) tartalmazhat, és 3 – 31 karakter hosszúságú lehet.
    API|Core (SQL)|Válassza a **Core (SQL)** lehetőséget egy olyan dokumentum-adatbázis létrehozásához, amelyet SQL-szintaxis használatával tud lekérdezni. [További információ a Azure Cosmos db SQL API-ról](../cosmos-db/introduction.md).|
    Hely|*Válassza ki a tartózkodási helyéhez legközelebb eső régiót*|Válassza ki az Azure Cosmos DB-fiókot üzemeltetéséhez használni kívánt földrajzi helyet. Használja az Önhöz legközelebb eső helyet, vagy a felhasználók számára a leggyorsabb hozzáférést az adataihoz.
    Kapacitásmód|Kiszolgáló nélküli vagy kiépített átviteli sebesség|[Kiszolgáló nélküli módban lévő](../cosmos-db/serverless.md) fiók létrehozásához válassza a **kiszolgáló** nélküli lehetőséget. Válassza a **kiépített átviteli sebesség** lehetőséget egy fiók [kiépített átviteli](../cosmos-db/set-throughput.md) módban való létrehozásához.<br><br>Válassza a **kiszolgáló** nélküli lehetőséget, ha első lépések Azure Cosmos db.

4. Kattintson a **Felülvizsgálat + létrehozás** elemre. Kihagyhatja a **hálózat** és **címkék** szakaszt. 

5. Tekintse át az összegzési információkat, és kattintson a **Létrehozás** gombra. 

6. Várjon, amíg létrejön az új Azure Cosmos DB, majd válassza az **erőforrás keresése** lehetőséget.

    :::image type="content" source="../cosmos-db/media/create-cosmosdb-resources-portal/azure-cosmos-db-account-deployment-successful.png" alt-text="A Azure Cosmos DB fiók létrehozása befejeződött" border="true":::

## <a name="create-an-azure-cosmos-db-database-and-container"></a>Azure Cosmos DB-adatbázis és-tároló létrehozása

A Azure Cosmos DB fiókjában válassza a **adatkezelő**, majd az **új tároló** elemet. Hozzon létre egy *saját-adatbázis* nevű új adatbázist, egy új tárolót, amely a *saját tároló* nevet kapta, és válassza ki `/id` a [partíció kulcsát](../cosmos-db/partitioning-overview.md).

:::image type="content" source="./media/functions-add-output-binding-cosmos-db-vs-code/create-container.png" alt-text="Új Azure Cosmos DB tároló létrehozása a Azure Portal" border="true":::

## <a name="update-your-function-app-settings"></a>A függvény alkalmazás beállításainak frissítése

Az [előző](./create-first-function-vs-code-csharp.md)rövid útmutatóban létrehozott egy Function alkalmazást az Azure-ban. Ebből a cikkből megtudhatja, hogyan írhat függvényalkalmazás JSON-dokumentumok írásához a fent létrehozott Azure Cosmos DB-tárolóban. A Azure Cosmos DB-fiókhoz való kapcsolódáshoz hozzá kell adnia a kapcsolati karakterláncát az alkalmazás beállításaihoz. Ezután letölti az új beállítást a local.settings.jsfájlba, így helyileg futtatva csatlakozhat a Azure Cosmos DB-fiókjához.

1. Keresse meg az imént létrehozott Azure Cosmos DB fiókot a Visual Studio Code-ban. Kattintson a jobb gombbal a nevére, majd válassza a **kapcsolati karakterlánc másolása** lehetőséget.

    :::image type="content" source="./media/functions-add-output-binding-cosmos-db-vs-code/copy-connection-string.png" alt-text="A Azure Cosmos DB-kapcsolatok karakterláncának másolása" border="true":::

1. Nyomja meg az <kbd>F1</kbd> billentyűt a parancs paletta megnyitásához, majd keresse meg és futtassa a parancsot `Azure Functions: Add New Setting...` .

1. Válassza ki az előző cikkben létrehozott Function alkalmazást. Amikor a rendszer kéri, adja meg az alábbi információkat:

    + **Adja meg az új Alkalmazásbeállítás nevét**: Type `CosmosDbConnectionString` .

    + **Adja meg a "CosmosDbConnectionString" értéket**: illessze be a Azure Cosmos db-fiókjának a korábban átmásolt hálózati karakterláncát.

1. Az <kbd>F1</kbd> billentyű lenyomásával nyissa meg a parancssort, majd keresse meg és futtassa a parancsot `Azure Functions: Download Remote Settings...` . 

1. Válassza ki az előző cikkben létrehozott Function alkalmazást. A meglévő helyi beállítások felülírásához válassza az **Igen** lehetőséget. 

## <a name="register-binding-extensions"></a>Kötési bővítmények regisztrálása

Mivel Azure Cosmos DB kimeneti kötést használ, a projekt futtatása előtt telepítenie kell a megfelelő kötési bővítményt. 

::: zone pivot="programming-language-csharp"

A HTTP-és időzítő-eseményindítók kivételével a kötések kiterjesztési csomagként vannak implementálva. Futtassa a következő [DotNet-csomag hozzáadása](/dotnet/core/tools/dotnet-add-package) parancsot a terminál ablakban a tárolási bővítmény csomagjának a projekthez való hozzáadásához.

```bash
dotnet add package Microsoft.Azure.WebJobs.Extensions.CosmosDB
```

::: zone-end

::: zone pivot="programming-language-javascript"

A projekt a [bővítmények](functions-bindings-register.md#extension-bundles)használatára lett konfigurálva, amely automatikusan telepíti a bővítmények előre meghatározott készletét. 

A bővítmény-csomagok használata engedélyezve van a projekt gyökérkönyvtárában lévő host.jsfájlban, amely a következőképpen jelenik meg:

:::code language="json" source="~/functions-quickstart-java/functions-add-output-binding-storage-queue/host.json":::

::: zone-end

Most hozzáadhatja a projekthez a Azure Cosmos DB kimeneti kötést.

## <a name="add-an-output-binding"></a>Kimeneti kötés hozzáadása

A functions szolgáltatásban minden típusú kötéshez a, `direction` `type` , és egyedi `name` érték szükséges a fájl function.js. Az attribútumok definiálásának módja a Function alkalmazás nyelvétől függ.

::: zone pivot="programming-language-csharp"

A C# Class Library-projektekben a kötések kötési attribútumokként vannak definiálva a Function metódusban. A függvények által igényelt fájl *function.js* automatikusan létrejön ezen attribútumok alapján.

Nyissa meg a *HttpExample. cs* projektfájlt, és adja hozzá a következő paramétert a `Run` metódus-definícióhoz:

```csharp
[CosmosDB(
    databaseName: "my-database",
    collectionName: "my-container",
    ConnectionStringSetting = "CosmosDbConnectionString")]IAsyncCollector<dynamic> documentsOut,
```

A `documentsOut` paraméter egy IAsyncCollector <T> -típus, amely egy JSON-dokumentumokból álló gyűjteményt jelöl, amelyet a rendszer az Azure Cosmos db tárolóba ír, amikor a függvény befejeződik. A megadott attribútumok a tároló nevét és a szülő adatbázisának nevét határozzák meg. A Azure Cosmos DB-fiókhoz tartozó kapcsolatok karakterláncát a ( `ConnectionStringSettingAttribute` ) adja meg.

A futtatási metódus definíciójának ekkor a következőhöz hasonlóan kell kinéznie:  

```csharp
[FunctionName("HttpExample")]
public static async Task<IActionResult> Run(
    [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)] HttpRequest req,
    [CosmosDB(
        databaseName: "my-database",
        collectionName: "my-container",
        ConnectionStringSetting = "CosmosDbConnectionString")]IAsyncCollector<dynamic> documentsOut,
    ILogger log)
```

::: zone-end

::: zone pivot="programming-language-javascript"

A kötési attribútumok közvetlenül a fájl function.jsvannak definiálva. A kötési típustól függően szükség lehet további tulajdonságokra. A [Azure Cosmos db kimeneti konfiguráció](./functions-bindings-cosmosdb-v2-output.md#configuration) leírja a Azure Cosmos db kimeneti kötéshez szükséges mezőket. A bővítmény megkönnyíti a kötések hozzáadását a fájl function.js. 

Kötés létrehozásához kattintson a jobb gombbal (CTRL + kattintás macOS-re) a `function.json` HttpTrigger mappában található fájlra, majd válassza a **kötés hozzáadása...** lehetőséget. Kövesse az utasításokat a következő kötési tulajdonságok definiálásához az új kötéshez:

| Adatkérés | Érték | Leírás |
| -------- | ----- | ----------- |
| **Kötési irány kiválasztása** | `out` | A kötés kimeneti kötés. |
| **Válassza ki a kötést a "kimenő" irányba** | `Azure Cosmos DB` | A kötés egy Azure Cosmos DB kötés. |
| **A kódban a kötés azonosítására használt név** | `outputDocument` | A kódban hivatkozott kötési paramétert azonosító név. |
| **Az a Cosmos DB-adatbázis, amelybe az adatírás történik** | `my-database` | A célként megadott tárolót tartalmazó Azure Cosmos DB adatbázis neve. |
| **Az adatbázis-gyűjtemény, amelybe az adatírás történik** | `my-container` | Annak a Azure Cosmos DB-tárolónak a neve, ahol a JSON-dokumentumok meg lesznek írva. |
| **Ha az értéke true, létrehozza a Cosmos DB-adatbázist és -gyűjteményt** | `false` | A célként megadott adatbázis és tároló már létezik. |
| **Válassza a beállítás elemet a "local.setting.json"** | `CosmosDbConnectionString` | Egy olyan Alkalmazásbeállítás neve, amely a Azure Cosmos DB fiókhoz tartozó kapcsolatok karakterláncát tartalmazza. |
| **Partíciókulcs (választható)** | *hagyja üresen* | Csak akkor szükséges, ha a kimeneti kötés létrehozza a tárolót. |
| **Gyűjtemény átviteli sebessége (nem kötelező)** | *hagyja üresen* | Csak akkor szükséges, ha a kimeneti kötés létrehozza a tárolót. |

A rendszer egy kötést ad hozzá a `bindings` function.jsa tömbhöz, amelynek a következőhöz hasonlóan kell kinéznie:

```json
{
    "type": "cosmosDB",
    "direction": "out",
    "name": "outputDocument",
    "databaseName": "my-database",
    "collectionName": "my-container",
    "createIfNotExists": "false",
    "connectionStringSetting": "CosmosDbConnectionString"
}
```

::: zone-end

## <a name="add-code-that-uses-the-output-binding"></a>Kimeneti kötést használó kód hozzáadása

::: zone pivot="programming-language-csharp"  

`documentsOut`Hozzon létre egy olyan kódot, amely a kimeneti kötési objektumot használja egy JSON-dokumentum létrehozásához. Adja hozzá ezt a kódot a metódus visszaadása előtt.

```csharp
if (!string.IsNullOrEmpty(name))
{
    // Add a JSON document to the output container.
    await documentsOut.AddAsync(new
    {
        // create a random ID
        id = System.Guid.NewGuid().ToString(),
        name = name
    });
}
```

Ezen a ponton a függvénynek a következőképpen kell kinéznie:

```csharp
[FunctionName("HttpExample")]
public static async Task<IActionResult> Run(
    [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)] HttpRequest req,
    [CosmosDB(
        databaseName: "my-database",
        collectionName: "my-container",
        ConnectionStringSetting = "CosmosDbConnectionString")]IAsyncCollector<dynamic> documentsOut,
    ILogger log)
{
    log.LogInformation("C# HTTP trigger function processed a request.");

    string name = req.Query["name"];

    string requestBody = await new StreamReader(req.Body).ReadToEndAsync();
    dynamic data = JsonConvert.DeserializeObject(requestBody);
    name = name ?? data?.name;

    if (!string.IsNullOrEmpty(name))
    {
        // Add a JSON document to the output container.
        await documentsOut.AddAsync(new
        {
            // create a random ID
            id = System.Guid.NewGuid().ToString(),
            name = name
        });
    }

    string responseMessage = string.IsNullOrEmpty(name)
        ? "This HTTP triggered function executed successfully. Pass a name in the query string or in the request body for a personalized response."
        : $"Hello, {name}. This HTTP triggered function executed successfully.";

    return new OkObjectResult(responseMessage);
}
```

::: zone-end

::: zone pivot="programming-language-javascript"  

`outputDocument` `context.bindings` JSON-dokumentum létrehozásához adja hozzá a kimeneti kötési objektumot használó kódot. Adja hozzá ezt a kódot az `context.res` utasítás előtt.

```javascript
if (name) {
    context.bindings.outputDocument = JSON.stringify({
        // create a random ID
        id: new Date().toISOString() + Math.random().toString().substr(2,8),
        name: name
    });
}
```

Ezen a ponton a függvénynek a következőképpen kell kinéznie:

```javascript
module.exports = async function (context, req) {
    context.log('JavaScript HTTP trigger function processed a request.');

    const name = (req.query.name || (req.body && req.body.name));
    const responseMessage = name
        ? "Hello, " + name + ". This HTTP triggered function executed successfully."
        : "This HTTP triggered function executed successfully. Pass a name in the query string or in the request body for a personalized response.";

    if (name) {
        context.bindings.outputDocument = JSON.stringify({
            // create a random ID
            id: new Date().toISOString() + Math.random().toString().substr(2,8),
            name: name
        });
    }

    context.res = {
        // status: 200, /* Defaults to 200 */
        body: responseMessage
    };
}
```

::: zone-end  

## <a name="run-the-function-locally"></a>A függvény helyi futtatása

1. Ahogy az előző cikkben is, az <kbd>F5</kbd> billentyű lenyomásával elindíthatja a Function app Project és a Core eszközöket. 

1. Az alapszintű eszközök futtatásával nyissa meg az **Azure: functions** területen. A **függvények** területen bontsa ki a **helyi Project**  >  **functions** elemet. Kattintson a jobb gombbal (CTRL + kattintás Mac gépen) a `HttpExample` függvényre, és válassza a **művelet végrehajtása most.**.. lehetőséget.

    :::image type="content" source="../../includes/media/functions-run-function-test-local-vs-code/execute-function-now.png" alt-text="A függvény végrehajtása most a Visual Studio Code-ból":::

1. Az **írja be a kérelem törzsében** megjelenik a kérelem üzenet törzsének értéke `{ "name": "Azure" }` . Nyomja le az ENTER billentyűt a kérelem üzenetének a függvénynek való elküldéséhez.  
 
1. A válasz visszaadása után nyomja le a <kbd>CTRL + C</kbd> billentyűkombinációt az alapvető eszközök leállításához.

### <a name="verify-that-a-json-document-has-been-created"></a>A JSON-dokumentum létrehozásának ellenőrzése

1. A Azure Portal lépjen vissza a Azure Cosmos DB-fiókjához, és válassza a **adatkezelő** lehetőséget.

1. Bontsa ki az adatbázist és a tárolót, és válassza az **elemek elemet** a tárolóban létrehozott dokumentumok listázásához.

1. Ellenőrizze, hogy a kimeneti kötés létrehozta-e az új JSON-dokumentumot.

    :::image type="content" source="./media/functions-add-output-binding-cosmos-db-vs-code/verify-output.png" alt-text="Annak ellenőrzése, hogy új dokumentum lett létrehozva a Azure Cosmos DB tárolóban" border="true":::

## <a name="redeploy-and-verify-the-updated-app"></a>A frissített alkalmazás újbóli üzembe helyezése és ellenőrzése

1. A Visual Studio Code-ban nyomja le az F1 billentyűt a parancs paletta megnyitásához. A parancs palettáján keresse meg és válassza ki a következőt: `Azure Functions: Deploy to function app...` .

1. Válassza ki az első cikkben létrehozott Function alkalmazást. Mivel a projekt ugyanarra az alkalmazásba való újbóli üzembe helyezését végzi, válassza a **telepítés** lehetőséget a fájlok felülírásával kapcsolatos figyelmeztetés elvetéséhez.

1. Az üzembe helyezés befejezése után ismét használhatja a **Execute függvényt** az Azure-ban a függvény elindításához... funkciót.

1. Ismét [ellenőrizze a Azure Cosmos db tárolóban létrehozott dokumentumokat](#verify-that-a-json-document-has-been-created) annak ellenőrzéséhez, hogy a kimeneti kötés ismét létrehoz egy új JSON-dokumentumot.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Az Azure-ban az *erőforrások* a Function apps, a functions, a Storage-fiókok és így tovább. *Erőforráscsoportokba* vannak csoportosítva, és a csoport törlésével törölheti a csoport összes elemét.

E rövid útmutatók elvégzéséhez erőforrásokat hozott létre. [Fiókjának állapotától](https://azure.microsoft.com/account/) és a [szolgáltatási díjszabástól](https://azure.microsoft.com/pricing/) függően lehetséges, hogy az erőforrások használata díjköteles. Ha már nincs szüksége ezekre az erőforrásokra, a következőképpen törölheti őket:

[!INCLUDE [functions-cleanup-resources-vs-code-inner.md](../../includes/functions-cleanup-resources-vs-code-inner.md)]

## <a name="next-steps"></a>Következő lépések

Frissítette a HTTP által aktivált függvényt, hogy JSON-dokumentumokat írjon egy Azure Cosmos DB tárolóba. Most már többet is megtudhat a függvények a Visual Studio Code használatával történő fejlesztéséről:

+ [Azure Functions fejlesztése a Visual Studio Code használatával](functions-develop-vs-code.md)

+ [Azure functions eseményindítók és kötések](functions-triggers-bindings.md).
::: zone pivot="programming-language-csharp"  
+ [Példák a teljes körű függvények projektjeire a C#-ban](/samples/browse/?products=azure-functions&languages=csharp).

+ [Azure Functions C# – fejlesztői dokumentáció](functions-dotnet-class-library.md)  
::: zone-end 
::: zone pivot="programming-language-javascript"  
+ [Példák a teljes körű függvények projektjeire a JavaScriptben](/samples/browse/?products=azure-functions&languages=javascript).

+ [Azure Functions JavaScript fejlesztői útmutató](functions-reference-node.md)  
::: zone-end  