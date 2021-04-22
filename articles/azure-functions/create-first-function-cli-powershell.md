---
title: PowerShell-függvény létrehozása a parancssorból – Azure Functions
description: Megtudhatja, hogyan hozhat létre PowerShell-függvényt a parancssorból, majd hogyan tehet közzé egy helyi projektet kiszolgáló nélküli üzemeltetésre a Azure Functions.
ms.date: 11/03/2020
ms.topic: quickstart
ms.custom:
- devx-track-powershell
- devx-track-azurecli
- devx-track-azurepowershell
ms.openlocfilehash: 441cc17099379366755a6c1e41c7f544215dc19d
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2021
ms.locfileid: "107866178"
---
# <a name="quickstart-create-a-powershell-function-in-azure-from-the-command-line"></a>Rövid útmutató: PowerShell-függvény létrehozása az Azure-ban a parancssorból

[!INCLUDE [functions-language-selector-quickstart-cli](../../includes/functions-language-selector-quickstart-cli.md)]

Ebben a cikkben parancssori eszközökkel hoz létre egy PowerShell-függvényt, amely HTTP-kérésekre válaszol. A kód helyi tesztelése után üzembe kell helyeznie a Azure Functions.

A rövid útmutató elvégzése néhány dollár vagy annál kisebb költséggel jár az Azure-fiókban.

A cikk Visual Studio [kódalapú](create-first-function-vs-code-powershell.md) verziója is elérhető.

## <a name="configure-your-local-environment"></a>A helyi környezet konfigurálása

Mielőtt hozzákezd, a következőkre lesz majd kíváncsi:

+ Aktív előfizetéssel rendelkezik egy Azure-fiók. [Hozzon létre egy ingyenes fiókot.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)

+ A [Azure Functions Core Tools](functions-run-local.md#v2) 3.x verzió.

+ Az azure-erőforrások létrehozására vonatkozó alábbi eszközök egyike:

    + [Azure PowerShell](/powershell/azure/install-az-ps) 5.0-s vagy újabb verziót.

    + [Az Azure CLI](/cli/azure/install-azure-cli) 2.4-es vagy újabb verziója.

+ A [.NET Core 3.1 SDK](https://dotnet.microsoft.com/download)

### <a name="prerequisite-check"></a>Előfeltétel-ellenőrzés

Ellenőrizze az előfeltételeket, amelyek attól függnek, hogy az Azure CLI-t vagy Azure PowerShell azure-erőforrások létrehozására használja:

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

+ Egy terminálban vagy parancsablakban futtassa a parancsot annak ellenőrzéshez, hogy a Azure Functions Core Tools `func --version` 3.x verziójú-e.

+ Az `az --version` futtatásával ellenőrizze, hogy az Azure CLI 2.4-es vagy újabb verziója van-e.

+ Az `az login` futtatásával jelentkezzen be az Azure-ba, és ellenőrizze az aktív előfizetést.

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

+ Egy terminálban vagy parancsablakban futtassa a parancsot annak ellenőrzéshez, hogy a Azure Functions Core Tools `func --version` 3.x verziójúak-e.

+ Futtassa `(Get-Module -ListAvailable Az).Version` és ellenőrizze az 5.0-s vagy újabb verziót. 

+ Az `Connect-AzAccount` futtatásával jelentkezzen be az Azure-ba, és ellenőrizze az aktív előfizetést.

---

## <a name="create-a-local-function-project"></a>Helyi függvényprojekt létrehozása

A Azure Functions a függvényprojekt egy tároló egy vagy több különálló függvény számára, amelyek egy adott eseményindítóra válaszolnak. Egy projekt összes függvénye azonos helyi és üzemeltetési konfigurációval rendelkezik. Ebben a szakaszban egy függvényprojektet hoz létre, amely egyetlen függvényt tartalmaz.

1. Futtassa a parancsot az alábbiak szerint, hogy létrehozzon egy függvényprojektet `func init` egy *LocalFunctionProj* nevű mappában a megadott futásidejűvel:  

    ```console
    func init LocalFunctionProj --powershell
    ```

1. Lépjen a projektmappába:

    ```console
    cd LocalFunctionProj
    ```
    
    Ez a mappa a projekt különböző fájljait tartalmazza, beleértve a fájlnevű konfigurációslocal.settings.js[és](functions-run-local.md#local-settings-file)host.js[a fájlban.](functions-host-json.md) Mivel *local.settings.jsfájl tartalmazhat* az Azure-ból letöltött titkos adatokat, a fájl alapértelmezés szerint ki van zárva a verziókezelőből a *.gitignore fájlban.*
    
1. Adjon hozzá egy függvényt a projekthez a következő paranccsal, ahol az argumentum a függvény (HttpExample) egyedi neve, az argumentum pedig a függvény eseményindítóját `--name` `--template` (HTTP) határozza meg. 

    ```console
    func new --name HttpExample --template "HTTP trigger" --authlevel "anonymous"
    ```   
    
    `func new`létrehoz egy almappát, amely megegyezik a függvény nevével, amely tartalmazza a projekt választott nyelvének megfelelő kódfájlt, valamint egy nevű konfigurációs fájlt *function.jsa fájlban.*

### <a name="optional-examine-the-file-contents"></a>(Nem kötelező) A fájl tartalmának vizsgálata

Ha szeretné, ugorjon a Függvény helyi [futtatása részhez,](#run-the-function-locally) és később vizsgálja meg a fájl tartalmát.

#### <a name="runps1"></a>run.ps1

*run.ps1* definiál egy függvényszkprogramot, amely a onfunction.js *konfigurációja alapján lesz aktiválva.*

:::code language="powershell" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-PowerShell/run.ps1":::

HTTP-eseményindítók esetén a függvény a paraméterben meghatározott paraméternek átadott `$Request` *kérelemadatokatfunction.jsa függvényhez.* A parancsmag a parancsmagnak válaszkéntfunction.jsa parancsmagnak a parancsmagban megadott `Response`  `Push-OutputBinding` visszaadott objektumot. 

#### <a name="functionjson"></a>function.json

*function.jsegy* konfigurációs fájl, amely meghatározza a függvény bemenetét és kimenetét, `bindings` beleértve az eseményindító típusát is. 

:::code language="json" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-PowerShell/function.json":::

Minden kötéshez szükség van egy irányra, egy típusra és egy egyedi névre. A HTTP-eseményindító típusú bemeneti és kimeneti [`httpTrigger`](functions-bindings-http-webhook-trigger.md) kötéssel [`http`](functions-bindings-http-webhook-output.md) rendelkezik.

[!INCLUDE [functions-run-function-test-local-cli](../../includes/functions-run-function-test-local-cli.md)]

[!INCLUDE [functions-create-azure-resources-cli](../../includes/functions-create-azure-resources-cli.md)]

4. A függvényalkalmazás létrehozása az Azure-ban:

    # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
        
    ```azurecli
    az functionapp create --resource-group AzureFunctionsQuickstart-rg --consumption-plan-location westeurope --runtime powershell --functions-version 3 --name <APP_NAME> --storage-account <STORAGE_NAME>
    ```
    
    Az [az functionapp create parancs](/cli/azure/functionapp#az_functionapp_create) létrehozza a függvényalkalmazást az Azure-ban. 
    
    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)
    
    ```azurepowershell
    New-AzFunctionApp -Name <APP_NAME> -ResourceGroupName AzureFunctionsQuickstart-rg -StorageAccount <STORAGE_NAME> -Runtime PowerShell -FunctionsVersion 3 -Location 'West Europe'
    ```
    
    A [New-AzFunctionApp](/powershell/module/az.functions/new-azfunctionapp) parancsmag létrehozza a függvényalkalmazást az Azure-ban. 
    
    ---
    
    Az előző példában cserélje le a helyére az előző lépésben használt fiók nevét, a helyére pedig az Önnek megfelelő globálisan `<STORAGE_NAME>` `<APP_NAME>` egyedi nevet. Az `<APP_NAME>` egyben a függvényalkalmazás alapértelmezett DNS-tartományaként is szolgál, 
    
    Ez a parancs létrehoz egy függvényalkalmazást, amely a megadott nyelvi futtatókörnyezetben fut a [Azure Functions használat](consumption-plan.md)alapján, amely ingyenes az itt használt mennyiségért. A parancs egy társított Azure Application Insights-példányt is kiad ugyanabban az erőforráscsoportban, amellyel figyelheti a függvényalkalmazást és megtekintheti a naplókat. További információ: [Monitor Azure Functions.](functions-monitoring.md) A példány nem jár költségekkel, amíg ön nem aktiválja.

[!INCLUDE [functions-publish-project-cli](../../includes/functions-publish-project-cli.md)]

[!INCLUDE [functions-run-remote-azure-cli](../../includes/functions-run-remote-azure-cli.md)]

[!INCLUDE [functions-streaming-logs-cli-qs](../../includes/functions-streaming-logs-cli-qs.md)]

[!INCLUDE [functions-cleanup-resources-cli](../../includes/functions-cleanup-resources-cli.md)]

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Csatlakozás Azure Storage-üzenetsorhoz]

[Csatlakozás Azure Storage-üzenetsorhoz]: functions-add-output-binding-storage-queue-cli.md?pivots=programming-language-powershell
