---
title: TypeScript-függvény létrehozása a parancssorból – Azure Functions
description: Megtudhatja, hogyan hozhat létre TypeScript-függvényt a parancssorból, majd hogyan tehet közzé egy helyi projektet kiszolgáló nélküli üzemeltetésre a Azure Functions.
ms.date: 11/03/2020
ms.topic: quickstart
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: c7b37700d93ea129ba46de81989f06dd2afe52ba
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2021
ms.locfileid: "107831815"
---
# <a name="quickstart-create-a-typescript-function-in-azure-from-the-command-line"></a>Rövid útmutató: TypeScript-függvény létrehozása az Azure-ban a parancssorból

[!INCLUDE [functions-language-selector-quickstart-cli](../../includes/functions-language-selector-quickstart-cli.md)]

Ebben a cikkben parancssori eszközökkel hoz létre egy TypeScript-függvényt, amely HTTP-kérésekre válaszol. A kód helyi tesztelése után üzembe kell helyeznie a Azure Functions.

A rövid útmutató elvégzése néhány dollár vagy annál kisebb költséggel jár az Azure-fiókban.

A cikk Visual Studio [kódalapú](create-first-function-vs-code-typescript.md) verziója is elérhető.

## <a name="configure-your-local-environment"></a>A helyi környezet konfigurálása

Mielőtt hozzákezd, a következőkre lesz majd kíváncsi:

+ Aktív előfizetéssel rendelkezik egy Azure-fiók. [Hozzon létre egy ingyenes fiókot.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)

+ A [Azure Functions Core Tools](functions-run-local.md#v2) 3.x verzió.

+ Az azure-erőforrások létrehozására vonatkozó alábbi eszközök egyike:

    + [Az Azure CLI](/cli/azure/install-azure-cli) 2.4-es vagy újabb verziója.

    + [Azure PowerShell](/powershell/azure/install-az-ps) 5.0-s vagy újabb verziót.

+ [Node.js](https://nodejs.org/), Active LTS és Maintenance LTS verziók (8.11.1 és 10.14.1 ajánlott).

### <a name="prerequisite-check"></a>Előfeltétel-ellenőrzés

Ellenőrizze az előfeltételeket, amelyek attól függnek, hogy az Azure CLI-t vagy Azure PowerShell azure-erőforrások létrehozására használja:

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

+ Egy terminálban vagy parancsablakban futtassa a parancsot annak ellenőrzéshez, hogy a Azure Functions Core Tools `func --version` 3.x verziójú-e.

+ Az `az --version` futtatásával ellenőrizze, hogy az Azure CLI 2.4-es vagy újabb verziója van-e.

+ Az `az login` futtatásával jelentkezzen be az Azure-ba, és ellenőrizze az aktív előfizetést.

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

+ Egy terminálban vagy parancsablakban futtassa a parancsot annak ellenőrzéshez, hogy a Azure Functions Core Tools `func --version` 3.x verziójú-e.

+ Futtassa `(Get-Module -ListAvailable Az).Version` és ellenőrizze az 5.0-s vagy újabb verziót. 

+ Az `Connect-AzAccount` futtatásával jelentkezzen be az Azure-ba, és ellenőrizze az aktív előfizetést.

---

## <a name="create-a-local-function-project"></a>Helyi függvényprojekt létrehozása

A Azure Functions a függvényprojekt egy tároló egy vagy több különálló függvény számára, amelyek egy adott eseményindítóra válaszolnak. Egy projekt összes függvénye azonos helyi és üzemeltetési konfigurációval rendelkezik. Ebben a szakaszban egy függvényprojektet hoz létre, amely egyetlen függvényt tartalmaz.

1. Futtassa a parancsot az alábbiak szerint, hogy létrehozzon egy függvényprojektet `func init` egy *LocalFunctionProj* nevű mappában a megadott futásidejűvel:  

    ```console
    func init LocalFunctionProj --typescript
    ```

1. Lépjen a projektmappába:

    ```console
    cd LocalFunctionProj
    ```
    
    Ez a mappa a projekt különböző fájljait tartalmazza, beleértve a fájlnevű konfigurációslocal.settings.js[és](functions-run-local.md#local-settings-file)host.js[a fájlban.](functions-host-json.md) Mivel *local.settings.jsa fájl* tartalmazhat az Azure-ból letöltött titkos adatokat, a fájl alapértelmezés szerint ki van zárva a verziókezelőből a *.gitignore fájlban.*

1. Adjon hozzá egy függvényt a projekthez a következő paranccsal, ahol az argumentum a függvény egyedi neve (HttpExample), az argumentum pedig a függvény eseményindítóját `--name` `--template` (HTTP) határozza meg.

    ```console
    func new --name HttpExample --template "HTTP trigger" --authlevel "anonymous"
    ``` 
    
    `func new`létrehoz egy almappát, amely megegyezik a függvény nevével, amely tartalmazza a projekt választott nyelvének megfelelő kódfájlt, valamint egy nevű konfigurációs fájlt *function.jsa fájlban.*

### <a name="optional-examine-the-file-contents"></a>(Nem kötelező) A fájl tartalmának vizsgálata

Ha szeretné, ugorjon a Függvény helyi [futtatása részhez,](#run-the-function-locally) és később vizsgálja meg a fájl tartalmát.

#### <a name="indexts"></a>index.ts

*Az index.ts* exportál egy függvényt, amely a fájlban a(function.js *megfelelően aktiválódik.*

:::code language="typescript" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-TypeScript/index.ts":::

HTTP-eseményindítók esetén a függvény a httpRequest típusú változóban fogadja a kérelemadatokat a `req` *fájlbanfunction.jsváltozóban.*  A visszaadott objektum, amely a on `$return` *function.js,* a válasz. 

#### <a name="functionjson"></a>function.json

*function.jsegy* konfigurációs fájl, amely meghatározza a függvény bemenetét és kimenetét, `bindings` beleértve az eseményindító típusát is. 

:::code language="json" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-JavaScript/function.json":::

Minden kötéshez szükség van egy irányra, egy típusra és egy egyedi névre. A HTTP-eseményindító típusú bemeneti és kimeneti [`httpTrigger`](functions-bindings-http-webhook-trigger.md) kötéssel [`http`](functions-bindings-http-webhook-output.md) rendelkezik.

## <a name="run-the-function-locally"></a>A függvény helyi futtatása

1. Futtassa a függvényt a local Azure Functions a *LocalFunctionProj* mappából:

    ```console
    npm install
    npm start
    ```
    
    A kimenet vége felé a következő soroknak kell megjelennie:
    
    <pre>
    ...
    
    Now listening on: http://0.0.0.0:7071
    Application started. Press Ctrl+C to shut down.
    
    Http Functions:
    
            HttpExample: [GET,POST] http://localhost:7071/api/HttpExample
    ...
    
    </pre>
    
    >[!NOTE]  
    > Ha a HttpExample nem az alábbi módon jelenik meg, akkor valószínűleg a projekt gyökérmappán kívülről indította el a gazdagépet. Ebben az esetben a **Ctrl** C billentyűkombinációval állítsa le a gazdagépet, navigáljon a projekt gyökérmappához, és futtassa újra az +  előző parancsot.

1. Másolja a függvény URL-címét ebből a kimenetből egy böngészőbe, és fűzza hozzá a lekérdezési sztringet, így a `HttpExample` `?name=<your-name>` teljes URL-cím a következőhöz hasonló `http://localhost:7071/api/HttpExample?name=Functions` lesz: . A böngészőnek a következőhöz hasonló üzenetet kell `Hello Functions` megjelenítenie:

    ![A függvény eredménye helyileg fut a böngészőben](./media/functions-create-first-azure-function-azure-cli/function-test-local-browser.png)
    
    A terminál, amelyen elindította a projektet, a naplókimenetet is megjeleníti a kérések igénylése során.

1. Ha készen áll, nyomja le a **Ctrl** C billentyűkombinációt, és állítsa +  le `y` a függvénygazdát.

[!INCLUDE [functions-create-azure-resources-cli](../../includes/functions-create-azure-resources-cli.md)]

4. A függvényalkalmazás létrehozása az Azure-ban:

    # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
        
    ```azurecli
    az functionapp create --resource-group AzureFunctionsQuickstart-rg --consumption-plan-location westeurope --runtime node --runtime-version 12 --functions-version 3 --name <APP_NAME> --storage-account <STORAGE_NAME>
    ```
    
    Az [az functionapp create parancs](/cli/azure/functionapp#az_functionapp_create) létrehozza a függvényalkalmazást az Azure-ban. Ha a 10-es Node.js használja, módosítsa a et is `--runtime-version` `10` a-re.
    
    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)
    
    ```azurepowershell
    New-AzFunctionApp -Name <APP_NAME> -ResourceGroupName AzureFunctionsQuickstart-rg -StorageAccount <STORAGE_NAME> -Runtime node -RuntimeVersion 12 -FunctionsVersion 3 -Location 'West Europe'
    ```
    
    A [New-AzFunctionApp](/powershell/module/az.functions/new-azfunctionapp) parancsmag létrehozza a függvényalkalmazást az Azure-ban. Ha a 10-es Node.js használja, módosítsa a(Node.js) `-RuntimeVersion` `10` útjára.

    ---
        
    Az előző példában cserélje le a helyére az előző lépésben használt fiók nevét, a helyére pedig az Önnek megfelelő globálisan `<STORAGE_NAME>` `<APP_NAME>` egyedi nevet. Az `<APP_NAME>` egyben a függvényalkalmazás alapértelmezett DNS-tartományaként is szolgál, 
    
    Ez a parancs létrehoz egy függvényalkalmazást, amely a megadott nyelvi futtatókörnyezetben fut a [Azure Functions használat](consumption-plan.md)alapján, amely ingyenes az itt használt mennyiségért. A parancs egy társított Azure Application Insights-példányt is kiad ugyanabban az erőforráscsoportban, amellyel figyelheti a függvényalkalmazást és megtekintheti a naplókat. További információ: [Monitor Azure Functions.](functions-monitoring.md) A példány nem jár költségekkel, amíg ön nem aktiválja.

## <a name="deploy-the-function-project-to-azure"></a>A függvényprojekt üzembe helyezése az Azure-ban

Mielőtt a Core Tools használatával üzembe helyezi a projektet az Azure-ban, létre kell hoznia egy JavaScript-fájlokból álló, éles használatra kész buildet a TypeScript forrásfájlokból.

1. Az alábbi paranccsal készítse elő a TypeScript-projektet az üzembe helyezéshez:

    ```console
    npm run build:production 
    ```

1. Most, hogy rendelkezésre állnak a szükséges erőforrások, készen áll a helyi függvényprojekt azure-beli függvényalkalmazásban való üzembe helyezésére a [func azure functionapp publish paranccsal.](functions-run-local.md#project-file-deployment) A következő példában cserélje le `<APP_NAME>` a helyére az alkalmazás nevét.

    ```console
    func azure functionapp publish <APP_NAME>
    ```
    
    Ha a "Nem található alkalmazás névvel ..." hibaüzenet jelenik meg, várjon néhány másodpercet, és próbálkozzon újra, mert előfordulhat, hogy az Azure nem inicializálta teljesen az alkalmazást az előző parancs `az functionapp create` után.
    
    A publish parancs az alábbi kimenethez hasonló eredményeket mutat (az egyszerűség kedvéért csonkolt):
    
    <pre>
    ...
    
    Getting site publishing info...
    Creating archive for current directory...
    Performing remote build for functions project.
    
    ...
    
    Deployment successful.
    Remote build succeeded!
    Syncing triggers...
    Functions in msdocs-azurefunctions-qs:
        HttpExample - [httpTrigger]
            Invoke url: https://msdocs-azurefunctions-qs.azurewebsites.net/api/httpexample?code=KYHrydo4GFe9y0000000qRgRJ8NdLFKpkakGJQfC3izYVidzzDN4gQ==
    </pre>

[!INCLUDE [functions-run-remote-azure-cli](../../includes/functions-run-remote-azure-cli.md)]

[!INCLUDE [functions-streaming-logs-cli-qs](../../includes/functions-streaming-logs-cli-qs.md)]

[!INCLUDE [functions-cleanup-resources-cli](../../includes/functions-cleanup-resources-cli.md)]

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Csatlakozás Azure Storage-üzenetsorhoz](functions-add-output-binding-storage-queue-cli.md?pivots=programming-language-typescript)
