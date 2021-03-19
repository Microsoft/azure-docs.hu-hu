---
title: Durable Functions létrehozása a Azure Portal használatával
description: Megtudhatja, hogyan telepítheti a Azure Functions Durable Functions bővítményét a portál fejlesztéséhez.
ms.topic: conceptual
ms.date: 04/10/2020
ms.reviewer: azfuncdf
ms.openlocfilehash: b029fa246977dfe4210f6e8df242415f7e4103f1
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "87081916"
---
# <a name="create-durable-functions-using-the-azure-portal"></a>Durable Functions létrehozása a Azure Portal használatával

A Azure Functions [Durable functions](durable-functions-overview.md) -bővítménye a [Microsoft. Azure. Webjobs. Extensions. DurableTask](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.DurableTask)NuGet csomagban található. Ezt a bővítményt telepíteni kell a Function alkalmazásban. Ez a cikk bemutatja, hogyan telepítheti a csomagot úgy, hogy tartós funkciókat fejlesszen ki a Azure Portalban.

> [!NOTE]
> 
> * Ha a C#-ban tartós funkciókat fejleszt, érdemes megfontolnia a [Visual Studio 2019 fejlesztését](durable-functions-create-first-csharp.md).
> * Ha a JavaScriptben tartós funkciókat fejleszt, érdemes megfontolnia a [Visual Studio Code-fejlesztést](./quickstart-js-vscode.md).

## <a name="create-a-function-app"></a>Függvényalkalmazás létrehozása

A függvények végrehajtásának üzemeltetéséhez Function-alkalmazás szükséges. A Function app lehetővé teszi, hogy logikai egységként csoportosítsa a függvényeket az erőforrások egyszerűbb felügyelete, üzembe helyezése, skálázása és megosztása érdekében. Létrehozhat .NET-vagy JavaScript-alkalmazást is.

[!INCLUDE [Create function app Azure portal](../../../includes/functions-create-function-app-portal.md)]

Alapértelmezés szerint a létrehozott Function alkalmazás a Azure Functions futtatókörnyezet 2. x verzióját használja. A Durable Functions bővítmény a C# verzióban az Azure Functions Runtime 1. x és 2. x verziójával, valamint a JavaScript 2. x verziójának használatával működik. A sablonok azonban csak akkor érhetők el, ha a kiválasztott nyelvtől függetlenül megcélozzák a futtatókörnyezet 2. x verzióját.

## <a name="install-the-durable-functions-npm-package-javascript-only"></a>A tartós függvények NPM-csomagjának telepítése (csak JavaScript)

Ha JavaScript-Durable Functions hoz létre, telepítenie kell a [ `durable-functions` NPM csomagot](https://www.npmjs.com/package/durable-functions):

1. A Function alkalmazás oldalán válassza a **speciális eszközök** lehetőséget a bal oldali ablaktábla **fejlesztői eszközök** területén.

   :::image type="content" source="./media/durable-functions-create-portal/function-app-platform-features-choose-kudu.png" alt-text="Functions platform-funkciók kudu kiválasztása":::

2. A **speciális eszközök** lapon válassza az **Indítás** lehetőséget.

3. A kudu-konzolon válassza a **hibakeresési konzol**, majd a **cmd** elemet.

   :::image type="content" source="./media/durable-functions-create-portal/kudu-choose-debug-console.png" alt-text="Kudu hibakeresési konzol":::

3. A Function alkalmazás fájljának könyvtár-struktúrájának meg kell jelennie. Lépjen a `site/wwwroot` mappába. Innen feltöltheti a fájlt úgy, hogy `package.json` áthúzza a fájl könyvtár ablakába. A minta a `package.json` következő:

    ```json
    {
      "dependencies": {
        "durable-functions": "^1.3.1"
      }
    }
    ```

   :::image type="content" source="./media/durable-functions-create-portal/kudu-choose-debug-console.png" alt-text="Kudu feltöltési package.js":::

4. A `package.json` feltöltése után futtassa a parancsot a `npm install` kudu távoli végrehajtási konzolon.

   ![Kudu NPM-telepítés futtatása](./media/durable-functions-create-portal/kudu-npm-install.png)
   
## <a name="create-an-orchestrator-function"></a>Orchestrator függvény létrehozása

1. A Function alkalmazásban válassza a bal oldali ablaktábla **függvények** elemét, majd a felső menüben válassza a **Hozzáadás** lehetőséget. 

1. Az **új függvény** lap keresés mezőjébe írja be a kifejezést `durable` , majd válassza ki a **Durable functions http-indító** sablont.

   :::image type="content" source="./media/durable-functions-create-portal/durable-functions-http-starter-template.png" alt-text="Válassza ki Durable Functions HTTP Starter":::

1. Az **új függvény** neve mezőbe írja be a következőt: `HttpStart` , majd kattintson a **függvény létrehozása** elemre.

   A létrehozott függvény az előkészítés elindítására szolgál.

1. Hozzon létre egy másik függvényt a Function alkalmazásban, ezúttal a **Durable functions Orchestrator** sablon használatával. Nevezze el az új előkészítési függvényt `HelloSequence` .

1. Hozzon létre egy nevű harmadik függvényt `Hello` a **Durable functions Activity** sablon használatával.

## <a name="test-the-durable-function-orchestration"></a>A tartós függvények előkészítésének tesztelése

1. Lépjen vissza a **HttpStart** függvényhez, válassza a **függvény URL-címének beolvasása** lehetőséget, majd a **Másolás a vágólapra** ikonra kattintva másolja az URL-címet. Ezt az URL-címet használhatja a **HelloSequence** függvény elindításához.

1. Használjon olyan HTTP-eszközt, mint a Poster vagy a cURL, hogy POST-kérést küldjön a másolt URL-címre. A következő példa egy olyan cURL-parancs, amely POST-kérést küld a tartós függvénynek:

    ```bash
    curl -X POST https://{your-function-app-name}.azurewebsites.net/api/orchestrators/HelloSequence --header "Content-Length: 0"
    ```

    Ebben a példában az a `{your-function-app-name}` tartomány, amely a Function alkalmazás neve. A válaszüzenet URI-végpontok készletét fogja tartalmazni, amelyek a végrehajtás monitorozásához és szabályozásához használhatók fel hasonlóan a következő példához:

    ```json
    {  
       "id":"10585834a930427195479de25e0b952d",
       "statusQueryGetUri":"https://...",
       "sendEventPostUri":"https://...",
       "terminatePostUri":"https://...",
       "rewindPostUri":"https://..."
    }
    ```

1. Hívja meg a `statusQueryGetUri` végpont URI-ját, és láthatja a tartós függvény aktuális állapotát, ami az alábbi példához hasonló lehet:

    ```json
        {
            "runtimeStatus": "Running",
            "input": null,
            "output": null,
            "createdTime": "2017-12-01T05:37:33Z",
            "lastUpdatedTime": "2017-12-01T05:37:36Z"
        }
    ```

1. Folytassa a végpont meghívását `statusQueryGetUri` mindaddig, amíg az állapot be nem **fejeződik**, és az alábbi példához hasonló választ láthat:

    ```json
    {
            "runtimeStatus": "Completed",
            "input": null,
            "output": [
                "Hello Tokyo!",
                "Hello Seattle!",
                "Hello London!"
            ],
            "createdTime": "2017-12-01T05:38:22Z",
            "lastUpdatedTime": "2017-12-01T05:38:28Z"
        }
    ```

Az első tartós funkció most már működik az Azure-ban.

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [További tudnivalók a tartós függvények gyakori mintái](durable-functions-overview.md#application-patterns)
