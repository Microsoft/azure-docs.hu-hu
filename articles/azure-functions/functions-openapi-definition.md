---
title: A függvények elérhetővé tétele a OpenAPI az Azure API Management használatával
description: Hozzon létre egy OpenAPI definíciót, amely lehetővé teszi más alkalmazások és szolgáltatások számára, hogy meghívják a függvényt az Azure-ban.
ms.topic: tutorial
ms.date: 04/21/2020
ms.reviewer: sunayv
ms.custom: devx-track-csharp, mvc, cc996988-fb4f-47, references_regions
ms.openlocfilehash: 9083ff7d8f65c68ce8d173973a4eda650ac355aa
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "88212906"
---
# <a name="create-an-openapi-definition-for-a-serverless-api-using-azure-api-management"></a>OpenAPI-definíció létrehozása kiszolgáló nélküli API-hoz az Azure API Management használatával

A REST API-kat gyakran egy OpenAPI-definíció használatával írják le. Ez a definíció tartalmazza az API-ban elérhető műveletekkel kapcsolatos információkat, illetve az API kérés- és válaszadatainak felépítését.

Ebben az oktatóanyagban létrehoz egy függvényt, amely megállapítja, hogy egy szélturbina sürgősségi javítása költséghatékony-e. Ezután létrehoz egy OpenAPI-definíciót a Function alkalmazáshoz az [Azure API Management](../api-management/api-management-key-concepts.md) használatával, hogy a függvény más alkalmazásokból és szolgáltatásokból is meghívható legyen.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Függvény létrehozása az Azure-ban
> * OpenAPI-definíció létrehozása az Azure API Management használatával
> * A definíció tesztelése a függvény meghívásával
> * A OpenAPI-definíció letöltése

## <a name="create-a-function-app"></a>Függvényalkalmazás létrehozása

Rendelkeznie kell egy függvényalkalmazással a függvények végrehajtásának biztosításához. A Function app lehetővé teszi, hogy logikai egységként csoportosítsa a függvényeket az erőforrások egyszerűbb felügyelete, üzembe helyezése, skálázása és megosztása érdekében.

[!INCLUDE [Create function app Azure portal](../../includes/functions-create-function-app-portal.md)]

## <a name="create-the-function"></a>A függvény létrehozása

Ez az oktatóanyag egy HTTP által aktivált függvényt használ, amely két paramétert vesz igénybe:

* A turbina javításának várható ideje (óra).
* A turbina kapacitása kilowattban. 

A függvény ezután kiszámítja, hogy a javítás Mennyibe kerül, és mennyi bevételt lehet készíteni a turbina egy 24 órás időszakban. A HTTP által aktivált függvény létrehozása a [Azure Portalban](https://portal.azure.com):

1. A functions-alkalmazás bal oldali menüjében válassza a **függvények** lehetőséget, majd a felső menüben válassza a **Hozzáadás** lehetőséget.

1. Az **új függvény** ablakban válassza a **http-trigger** lehetőséget.

1. Az **új függvényhez** írja be a következőt: `TurbineRepair` . 

1. Válassza a **függvény** lehetőséget az **[engedélyezési szint](functions-bindings-http-webhook-trigger.md#http-auth)** legördülő listából, majd kattintson a **függvény létrehozása** elemre.

    :::image type="content" source="media/functions-openapi-definition/select-http-trigger-openapi.png" alt-text="HTTP-függvény létrehozása a OpenAPI":::

1. Válassza a **kód + teszt** lehetőséget, majd válassza a **Run. CSX** elemet a legördülő listából. Cserélje le a Run. CSX C# parancsfájl tartalmát a következő kódra, majd válassza a **Mentés** lehetőséget:

    ```csharp
    #r "Newtonsoft.Json"
    
    using System.Net;
    using Microsoft.AspNetCore.Mvc;
    using Microsoft.Extensions.Primitives;
    using Newtonsoft.Json;
    
    const double revenuePerkW = 0.12;
    const double technicianCost = 250;
    const double turbineCost = 100;
    
    public static async Task<IActionResult> Run(HttpRequest req, ILogger log)
    {
        // Get query strings if they exist
        int tempVal;
        int? hours = Int32.TryParse(req.Query["hours"], out tempVal) ? tempVal : (int?)null;
        int? capacity = Int32.TryParse(req.Query["capacity"], out tempVal) ? tempVal : (int?)null;
    
        // Get request body
        string requestBody = await new StreamReader(req.Body).ReadToEndAsync();
        dynamic data = JsonConvert.DeserializeObject(requestBody);
    
        // Use request body if a query was not sent
        capacity = capacity ?? data?.capacity;
        hours = hours ?? data?.hours;
    
        // Return bad request if capacity or hours are not passed in
        if (capacity == null || hours == null){
            return new BadRequestObjectResult("Please pass capacity and hours on the query string or in the request body");
        }
        // Formulas to calculate revenue and cost
        double? revenueOpportunity = capacity * revenuePerkW * 24;  
        double? costToFix = (hours * technicianCost) +  turbineCost;
        string repairTurbine;
    
        if (revenueOpportunity > costToFix){
            repairTurbine = "Yes";
        }
        else {
            repairTurbine = "No";
        };
    
        return (ActionResult)new OkObjectResult(new{
            message = repairTurbine,
            revenueOpportunity = "$"+ revenueOpportunity,
            costToFix = "$"+ costToFix
        });
    }
    ```

    Ez a függvény egy üzenetet ad vissza, `Yes` vagy `No` jelzi, hogy a vészhelyzeti javítás költséghatékony-e. Azt is adja vissza, hogy a turbina milyen bevételi lehetőséget képvisel, valamint a turbina javításának költségeit.

1. A függvény teszteléséhez válassza a **teszt** lehetőséget, válassza a **bevitel** fület, adja meg a **törzs** következő bemenetét, majd válassza a **Futtatás** lehetőséget:

    ```json
    {
    "hours": "6",
    "capacity": "2500"
    }
    ```

    :::image type="content" source="media/functions-openapi-definition/test-function.png" alt-text="A függvény tesztelése az Azure Portalon":::

    A **kimenet** lapon a következő kimenet lesz visszaadva:

    ```json
    {"message":"Yes","revenueOpportunity":"$7200","costToFix":"$1600"}
    ```

Most már van egy olyan függvénye, amely megállapítja a sürgősségi javítások költséghatékonyságát. Ezután létrehoz egy OpenAPI-definíciót a Function alkalmazáshoz.

## <a name="generate-the-openapi-definition"></a>Az OpenAPI-definíció létrehozása

A OpenAPI-definíció előállítása:

1. Válassza ki a Function alkalmazást, válassza a bal oldali menüben a **API Management** lehetőséget, majd válassza az **új létrehozása** lehetőséget **API Management** alatt.

    :::image type="content" source="media/functions-openapi-definition/select-all-settings-openapi.png" alt-text="API Management kiválasztása":::


1. Használja a következő táblázatban megadott API Management beállításokat:

    | Beállítás      | Ajánlott érték  | Leírás                                        |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Név** | Globálisan egyedi név | A rendszer a függvény alkalmazásának neve alapján hozza létre a nevet. |
    | **Előfizetés** | Az Ön előfizetése | Az az előfizetés, amely alatt az új erőforrást létrehozták. |  
    | **[Erőforráscsoport](../azure-resource-manager/management/overview.md)** |  myResourceGroup | A Function alkalmazással megegyező erőforrás, amelyet be kell állítani. |
    | **Hely** | USA nyugati régiója | Válassza ki az USA nyugati régióját. |
    | **Szervezet neve** | Contoso | A fejlesztői portálon használt szervezet neve és e-mail-értesítések. |
    | **Rendszergazda e-mail-címe** | e-mail-címe | A API Managementtól kapott rendszerértesítéseket tartalmazó e-mailek. |
    | **Tarifacsomag** | Használat | A felhasználási szintek nem érhetők el minden régióban. A díjszabással kapcsolatos részletekért tekintse meg a [API Management díjszabási oldalát](https://azure.microsoft.com/pricing/details/api-management/) . |

    ![Új API Management szolgáltatás létrehozása](media/functions-openapi-definition/new-apim-service-openapi.png)

1. A **Létrehozás** lehetőséget választva hozza létre az API Management-példányt. Ez több percet is igénybe vehet.

1. Miután az Azure létrehozta a példányt, **engedélyezi az Application Insights engedélyezése** lehetőséget az oldalon. Jelölje ki, hogy a rendszer ugyanarra a helyre küldje a naplókat, mint a Function alkalmazás, majd válassza az **API csatolása** lehetőséget.

1. Megnyílik az **importálási Azure functions** a **TurbineRepair** függvénnyel kiemelve. Válassza a **Kijelölés** elemet a folytatáshoz.

    ![Azure Functions importálása a API Managementba](media/functions-openapi-definition/import-function-openapi.png)

1. A **Létrehozás a függvényalkalmazás** lapon fogadja el az alapértelmezett értékeket, majd válassza a **Létrehozás** lehetőséget.

    :::image type="content" source="media/functions-openapi-definition/create-function-openapi.png" alt-text="Létrehozás függvényalkalmazás":::

    Az Azure létrehozza a függvény API-ját.

## <a name="test-the-api"></a>Az API tesztelése

A OpenAPI-definíció használata előtt ellenőrizze, hogy az API működik-e.

1. A Function app lapon válassza a **API Management** lehetőséget, válassza a **test (teszt** ) fület, majd válassza a **TurbineRepair közzététele** lehetőséget. 

1. Adja meg a következő kódot a **kérelem törzsében**:

    ```json
    {
    "hours": "6",
    "capacity": "2500"
    }
    ```

1. Válassza a **Küldés** lehetőséget, majd tekintse meg a **http-választ**.

    :::image type="content" source="media/functions-openapi-definition/test-function-api-openapi.png" alt-text="Function API tesztelése":::

## <a name="download-the-openapi-definition"></a>A OpenAPI-definíció letöltése

Ha az API a várt módon működik, letöltheti a OpenAPI-definíciót.

1. A lap tetején válassza a **OpenAPI-definíció letöltése** lehetőséget.
   
   ![OpenAPI-definíció letöltése](media/functions-openapi-definition/download-definition.png)

2. Mentse a letöltött JSON-fájlt, majd nyissa meg. Tekintse át a definíciót.

[!INCLUDE [clean-up-section-portal](../../includes/clean-up-section-portal.md)]

## <a name="next-steps"></a>Következő lépések

API Management integrációt használt a függvények OpenAPI-definíciójának létrehozásához. Most már szerkesztheti a definíciót API Management a portálon. [További tudnivalókat a API Managementról](../api-management/api-management-key-concepts.md)is kaphat.

> [!div class="nextstepaction"]
> [A OpenAPI-definíció szerkesztése a API Managementban](../api-management/edit-api.md)
