---
title: JavaScript-függvény létrehozása a parancssorból – Azure Functions
description: Megtudhatja, hogyan hozhat létre JavaScript-függvényt a parancssorból, majd hogyan tehet közzé Node.js helyi Node.js kiszolgáló nélküli üzemeltetésre a Azure Functions.
ms.date: 11/03/2020
ms.topic: quickstart
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: eb162bcff647e9e1dbced19ddea33a8f3c54b58e
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2021
ms.locfileid: "107831941"
---
# <a name="quickstart-create-a-javascript-function-in-azure-from-the-command-line"></a>Rövid útmutató: JavaScript-függvény létrehozása az Azure-ban a parancssorból


[!INCLUDE [functions-language-selector-quickstart-cli](../../includes/functions-language-selector-quickstart-cli.md)]

Ebben a cikkben parancssori eszközökkel hoz létre egy JavaScript-függvényt, amely HTTP-kérésekre válaszol. A kód helyi tesztelése után üzembe helyezheti a virtuális gép kiszolgáló nélküli Azure Functions. 

Ennek a rövid útmutatónak az elvégzése néhány dollár vagy annál kisebb költséggel jár az Azure-fiókjában.

A cikk [kódalapú Visual Studio is](create-first-function-vs-code-node.md) elérhető.

## <a name="configure-your-local-environment"></a>A helyi környezet konfigurálása

Mielőtt hozzákezd, a következőkre lesz majd elég:

+ Aktív előfizetéssel rendelkezik egy Azure-fiók. [Hozzon létre egy ingyenes fiókot.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)

+ A [Azure Functions Core Tools](./functions-run-local.md#v2) 3.x verzió.

+ Az alábbi eszközök egyike az Azure-erőforrások létrehozásához:

    + [Az Azure CLI](/cli/azure/install-azure-cli) 2.4-es vagy újabb verziója.

    + [Azure PowerShell](/powershell/azure/install-az-ps) 5.0-s vagy újabb verzió.

+ [Node.js](https://nodejs.org/) 12-es verzió. Node.js 10-es verzió is támogatott.

### <a name="prerequisite-check"></a>Előfeltétel-ellenőrzés

Ellenőrizze az előfeltételeket, amelyek attól függnek, hogy az Azure CLI-t Azure PowerShell azure-erőforrások létrehozásához használja:

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

A Azure Functions a függvényprojekt egy tároló egy vagy több egyéni függvény számára, amelyek egy adott eseményindítóra válaszolnak. Egy projekt összes függvénye azonos helyi és üzemeltetési konfigurációval rendelkezik. Ebben a szakaszban egy függvényprojektet hoz létre, amely egyetlen függvényt tartalmaz.

1. Futtassa a parancsot az alábbiak szerint, hogy létrehozzon egy függvényprojektet `func init` egy *LocalFunctionProj* nevű mappában a megadott futásidővel:  

    ```console
    func init LocalFunctionProj --javascript
    ```

1. Lépjen a projektmappába:

    ```console
    cd LocalFunctionProj
    ```

    Ez a mappa a projekt különböző fájljait [](functions-run-local.md#local-settings-file) tartalmazza, beleértve alocal.settings.jsnevű konfigurációs fájlokat, [valaminthost.jsfájlját.](functions-host-json.md) Mivel *local.settings.js* fájl tartalmazhatja az Azure-ból letöltött titkos adatokat, a fájl alapértelmezés szerint ki van zárva a verziókezelőből a *.gitignore fájlban.*

1. Adjon hozzá egy függvényt a projekthez a következő paranccsal, ahol az argumentum a függvény (HttpExample) egyedi neve, az argumentum pedig a függvény eseményindítóját `--name` `--template` (HTTP) határozza meg.

    ```console
    func new --name HttpExample --template "HTTP trigger" --authlevel "anonymous"
    ```  
    
    `func new` létrehoz egy almappát, amely megegyezik a függvény nevével, amely tartalmazza a projekt választott nyelvének megfelelő kódfájlt, valamint egy nevű konfigurációs *function.jsa következőn:*.
    
### <a name="optional-examine-the-file-contents"></a>(Nem kötelező) A fájl tartalmának vizsgálata

Ha szeretné, ugorjon a Függvény helyi [futtatása](#run-the-function-locally) részhez, és később vizsgálja meg a fájl tartalmát.

#### <a name="indexjs"></a>index.js

*index.js* exportál egy függvényt, amely a fájlban a(function.js *konfigurációja alapján aktiválódik.*

:::code language="javascript" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-JavaScript/index.js":::

HTTP-eseményindító esetén a függvény a változóban kapja meg a kérelemadatokat a(function.js) `req` *változóban.* A válasz a következőként van `res` definiálva: *function.jsés* a használatával érhető `context.res` el. További információ: [HTTP Azure Functions triggerek és kötések .](./functions-bindings-http-webhook.md?tabs=javascript)

#### <a name="functionjson"></a>function.json

*function.jsegy konfigurációs* fájl, amely meghatározza a függvény bemenetét és kimenetét, beleértve `bindings` az eseményindító típusát is. 

:::code language="json" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-JavaScript/function.json":::

Minden kötéshez szükség van egy irányra, egy típusra és egy egyedi névre. A HTTP-eseményindító típusú bemeneti és kimeneti [`httpTrigger`](functions-bindings-http-webhook-trigger.md) kötéssel [`http`](functions-bindings-http-webhook-output.md) rendelkezik.

[!INCLUDE [functions-run-function-test-local-cli](../../includes/functions-run-function-test-local-cli.md)]

[!INCLUDE [functions-create-azure-resources-cli](../../includes/functions-create-azure-resources-cli.md)]

4. Hozza létre a függvényalkalmazást az Azure-ban:

    # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
        
    ```azurecli
    az functionapp create --resource-group AzureFunctionsQuickstart-rg --consumption-plan-location westeurope --runtime node --runtime-version 12 --functions-version 3 --name <APP_NAME> --storage-account <STORAGE_NAME>
    ```
    
    Az [az functionapp create parancs](/cli/azure/functionapp#az_functionapp_create) létrehozza a függvényalkalmazást az Azure-ban. Ha a 10-es Node.js használja, módosítsa a et is `--runtime-version` a-re. `10`
    
    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)
    
    ```azurepowershell
    New-AzFunctionApp -Name <APP_NAME> -ResourceGroupName AzureFunctionsQuickstart-rg -StorageAccount <STORAGE_NAME> -Runtime node -RuntimeVersion 12 -FunctionsVersion 3 -Location 'West Europe'
    ```
    
    A [New-AzFunctionApp](/powershell/module/az.functions/new-azfunctionapp) parancsmag létrehozza a függvényalkalmazást az Azure-ban. Ha a 10-es Node.js használja, módosítsa a(Node.js) `-RuntimeVersion` `10` parancsra.
    
    ---
    
    Az előző példában cserélje le a helyére az előző lépésben használt fiók nevét, a helyére pedig az Önnek megfelelő globálisan egyedi `<STORAGE_NAME>` `<APP_NAME>` nevet. Az `<APP_NAME>` egyben a függvényalkalmazás alapértelmezett DNS-tartományaként is szolgál, 
    
    Ez a parancs létrehoz egy függvényalkalmazást, amely a megadott nyelvi futtatókörnyezetben fut a [Azure Functions Használat](consumption-plan.md)alapján csomag alatt, amely ingyenes az itt használt mennyiséghez. A parancs egy társított Azure Application Insights-példányt is kiad ugyanabban az erőforráscsoportban, amellyel figyelheti a függvényalkalmazást és megtekintheti a naplókat. További információ: [Monitor Azure Functions.](functions-monitoring.md) A példány nem jár költségekkel, amíg ön nem aktiválja.

[!INCLUDE [functions-publish-project-cli](../../includes/functions-publish-project-cli.md)]

[!INCLUDE [functions-run-remote-azure-cli](../../includes/functions-run-remote-azure-cli.md)]

[!INCLUDE [functions-streaming-logs-cli-qs](../../includes/functions-streaming-logs-cli-qs.md)]

[!INCLUDE [functions-cleanup-resources-cli](../../includes/functions-cleanup-resources-cli.md)]

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Csatlakozás Azure Storage-üzenetsorhoz](functions-add-output-binding-storage-queue-cli.md?pivots=programming-language-javascript)
