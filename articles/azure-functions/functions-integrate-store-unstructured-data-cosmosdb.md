---
title: Strukturálatlan adatok tárolása Azure Cosmos DB és függvények használatával
description: Strukturálatlan adatok tárolása az Azure Functions és a Cosmos DB használatával
ms.topic: quickstart
ms.date: 10/01/2020
ms.custom: devx-track-csharp, mvc
ms.openlocfilehash: 5bc3895cb219338acde492b871dce806db70622b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "91661159"
---
# <a name="store-unstructured-data-using-azure-functions-and-azure-cosmos-db"></a>Strukturálatlan adatok tárolása az Azure Functions és az Azure Cosmos DB használatával

Az [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) kiválóan alkalmas strukturálatlan és JSON-adatok tárolására. A Cosmos DB, az Azure Functions szolgáltatással kombinálva felgyorsítja és megkönnyíti az adattárolást, hiszen lényegesen kevesebb kódot igényel, mint amennyi egy relációs adatbázisban történő adattároláshoz szükséges.

> [!NOTE]
> Az Azure Cosmos DB-eseményindító, illetve a bemeneti és a kimeneti kötések jelenleg csak az SQL API- és a Graph API-fiókokkal képesek együttműködni.

Az Azure Functions bemeneti és kimeneti kötései deklaratív módszert biztosítanak a külső szolgáltatások adataihoz a függvényből történő csatlakozásra. Ebben a cikkben megtudhatja, hogyan módosíthat egy meglévő függvényt egy kimeneti kötés hozzáadásához, amely strukturálatlan adatokat tárol egy Azure Cosmos DB-dokumentumban.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez:

[!INCLUDE [Previous quickstart note](../../includes/functions-quickstart-previous-topics.md)]

## <a name="create-an-azure-cosmos-db-account"></a>Azure Cosmos DB-fiók létrehozása

A kimeneti kötés létrehozásához rendelkeznie kell egy SQL API-t használó Azure Cosmos DB-fiókkal.

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

## <a name="add-an-output-binding"></a>Kimeneti kötés hozzáadása

1. A Azure Portal navigáljon, és válassza ki a korábban létrehozott Function alkalmazást.

1. Válassza a **függvények** lehetőséget, majd válassza a HttpTrigger függvényt.

    :::image type="content" source="./media/functions-integrate-store-unstructured-data-cosmosdb/functions-select-http-function.png" alt-text="Válassza ki a http-függvényt a Azure Portalban." border="true":::

1. Válassza az **integráció** és a **+ kimenet hozzáadása** elemet.

     :::image type="content" source="./media/functions-integrate-store-unstructured-data-cosmosdb/functions-add-output-binding.png" alt-text="Adjon hozzá egy Azure Cosmos DB kimeneti kötést." border="true":::

1. Használja a táblázatban megadott kimeneti beállításokat a **létrehozáshoz** :

     :::image type="content" source="./media/functions-integrate-store-unstructured-data-cosmosdb/functions-configure-cosmosdb-binding.png" alt-text="Azure Cosmos DB kimeneti kötés konfigurálása." border="true":::

    | Beállítás      | Ajánlott érték  | Leírás                                |
    | ------------ | ---------------- | ------------------------------------------ |
    | **Kötés típusa** | Azure Cosmos DB | Annak a kötési típusnak a neve, amelyet a Azure Cosmos DBhoz tartozó kimeneti kötés létrehozásához kíván kiválasztani. |
    | **Dokumentumparaméter neve** | taskDocument | A Cosmos DB-objektumra utaló név a kódban. |
    | **Adatbázis neve** | taskDatabase | Adatbázis neve a dokumentumok mentéséhez. |
    | **Gyűjtemény neve** | taskCollection | Az adatbázis-gyűjtemény neve. |
    | **Ha az értéke true, létrehozza a Cosmos DB-adatbázist és -gyűjteményt** | Yes | A gyűjtemény még nem létezik, hozza létre. |
    | **Cosmos DB-fiókkapcsolat** | Új beállítás | Válassza az **új** lehetőséget, majd válassza ki **Azure Cosmos db fiókot** és a korábban létrehozott **adatbázis-fiókot** , majd kattintson **az OK gombra**. Ezzel létrehoz egy alkalmazásbeállítást a fiókkapcsolathoz. Ezt a beállítást használja a kötés, hogy kapcsolódjon az adatbázishoz. |

1. A kötés létrehozásához kattintson **az OK gombra** .

## <a name="update-the-function-code"></a>A függvénykód módosítása

Cserélje le a meglévő függvénykódot a következő kódra a kiválasztott nyelven:

# <a name="c"></a>[C#](#tab/csharp)

Cserélje le a meglévő C#-függvényt a következő kódra:

```csharp
#r "Newtonsoft.Json"

using Microsoft.AspNetCore.Mvc;
using Microsoft.AspNetCore.Http;
using Microsoft.Extensions.Logging;

public static IActionResult Run(HttpRequest req, out object taskDocument, ILogger log)
{
    string name = req.Query["name"];
    string task = req.Query["task"];
    string duedate = req.Query["duedate"];

    // We need both name and task parameters.
    if (!string.IsNullOrEmpty(name) && !string.IsNullOrEmpty(task))
    {
        taskDocument = new
        {
            name,
            duedate,
            task
        };

        return (ActionResult)new OkResult();
    }
    else
    {
        taskDocument = null;
        return (ActionResult)new BadRequestResult();
    }
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Cserélje le a meglévő JavaScript-függvényt a következő kódra:

```js
module.exports = async function (context, req) {

    // We need both name and task parameters.
    if (req.query.name && req.query.task) {

        // Set the output binding data from the query object.
        context.bindings.taskDocument = req.query;

        // Success.
        context.res = {
            status: 200
        };
    }
    else {
        context.res = {
            status: 400,
            body: "The query options 'name' and 'task' are required."
        };
    }
};
```
---

A mintakód beolvassa a HTTP-kérelem sztringjeit, és egy `taskDocument` objektum mezőihez rendeli azokat. A `taskDocument` kötés elküldi az ezen kötési paramétertől származó objektumadatokat a kötött dokumentum-adatbázisba tárolásra. Az adatbázis a függvény első futtatásakor jön létre.

## <a name="test-the-function-and-database"></a>A függvény és az adatbázis tesztelése

1. Válassza a **Tesztelés/Futtatás** lehetőséget. A **lekérdezés** területen válassza a **+ paraméter hozzáadása** lehetőséget, és adja hozzá a következő paramétereket a lekérdezési karakterlánchoz:

    + `name`
    + `task`
    + `duedate`

    :::image type="content" source="./media/functions-integrate-store-unstructured-data-cosmosdb/functions-test-function.png" alt-text="Tesztelje a függvényt." border="true":::


1. Válassza a **Futtatás** lehetőséget, és ellenőrizze, hogy a rendszer visszaadja-e a 200 állapotot.

    :::image type="content" source="./media/functions-integrate-store-unstructured-data-cosmosdb/functions-test-function-output.png" alt-text="A képernyőképen a Futtatás után a 200-as HTTP-Response kód állapot jelenik meg." border="true":::


1. A Azure Portal keresse meg és válassza a **Azure Cosmos db** lehetőséget.

    :::image type="content" source="./media/functions-integrate-store-unstructured-data-cosmosdb/functions-search-cosmos-db.png" alt-text="Keressen rá a Cosmos DB szolgáltatásra." border="true":::

1. Válassza ki Azure Cosmos DB-fiókját, majd válassza a  **adatkezelő** lehetőséget.

1. Bontsa ki a **TaskCollection** csomópontokat, válassza ki az új dokumentumot, és ellenőrizze, hogy a dokumentum tartalmazza-e a lekérdezési karakterlánc értékeit, valamint néhány további metaadatot.

    :::image type="content" source="./media/functions-integrate-store-unstructured-data-cosmosdb/functions-data-explorer-check-document.png" alt-text="Ellenőrizze a karakterlánc értékeit a dokumentumban." border="true":::

Sikeresen hozzáadott egy kötést a HTTP-triggerhez, hogy strukturálatlan adatokat tárolhasson az Azure Cosmos DB-ben.

[!INCLUDE [Clean-up section](../../includes/clean-up-section-portal.md)]

## <a name="next-steps"></a>Következő lépések

A Cosmos DB-adatbázisokhoz végzett kötésről további információt az [Azure Functions Cosmos DB-kötéseket](functions-bindings-cosmosdb.md) ismertető cikk tartalmaz.

[!INCLUDE [functions-quickstart-next-steps](../../includes/functions-quickstart-next-steps-2.md)]
