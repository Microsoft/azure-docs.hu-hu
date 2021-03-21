---
title: PowerShell-függvény létrehozása a parancssorból – Azure Functions
description: Megtudhatja, hogyan hozhat létre PowerShell-függvényt a parancssorból, majd hogyan teheti közzé a helyi projektet a Azure Functions kiszolgáló nélküli üzemeltetéséhez.
ms.date: 11/03/2020
ms.topic: quickstart
ms.custom:
- devx-track-powershell
- devx-track-azurecli
ms.openlocfilehash: abbe3b9ed4d9a8c9bf30c6be3e6980228d319090
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "97937229"
---
# <a name="quickstart-create-a-powershell-function-in-azure-from-the-command-line"></a>Gyors útmutató: PowerShell-függvény létrehozása az Azure-ban a parancssorból

[!INCLUDE [functions-language-selector-quickstart-cli](../../includes/functions-language-selector-quickstart-cli.md)]

Ebben a cikkben parancssori eszközöket használ egy olyan PowerShell-függvény létrehozásához, amely válaszol a HTTP-kérelmekre. A kód helyi tesztelését követően a Azure Functions kiszolgáló nélküli környezetében helyezheti üzembe.

A rövid útmutató elvégzésével az Azure-fiókjában néhány USD értékű vagy annál kisebb költséggel jár.

A cikk [Visual Studio Code-alapú verziója](create-first-function-vs-code-powershell.md) is létezik.

## <a name="configure-your-local-environment"></a>A helyi környezet konfigurálása

Mielőtt elkezdené, a következőkkel kell rendelkeznie:

+ Aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egy fiókot ingyenesen](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

+ A [Azure functions Core Tools](functions-run-local.md#v2) 3. x verzió.

+ Az alábbi eszközök egyike az Azure-erőforrások létrehozásához:

    + [Azure PowerShell](/powershell/azure/install-az-ps) 5,0-es vagy újabb verzió.

    + Az [Azure CLI](/cli/azure/install-azure-cli) 2,4-es vagy újabb verziója.

+ A [.NET Core SDK 3,1](https://www.microsoft.com/net/download)

### <a name="prerequisite-check"></a>Előfeltételek ellenőrzése

Ellenőrizze az előfeltételeket, amelyek attól függnek, hogy az Azure CLI-t vagy Azure PowerShell Azure-erőforrások létrehozásához használja-e:

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

+ A terminál vagy a parancssorablakban futtassa a parancsot az `func --version` Azure functions Core Tools 3. x verziójának megadásához.

+ Futtassa az parancsot az `az --version` Azure CLI 2,4-es vagy újabb verziójának megadásához.

+ A futtatásával `az login` Jelentkezzen be az Azure-ba, és ellenőrizze az aktív előfizetést.

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

+ A terminál vagy a parancssorablakban futtassa a parancsot az `func --version` Azure functions Core Tools 3. x verziójának megadásához.

+ Futtassa `(Get-Module -ListAvailable Az).Version` és ellenőrizze a 5,0-es vagy újabb verziót. 

+ A futtatásával `Connect-AzAccount` Jelentkezzen be az Azure-ba, és ellenőrizze az aktív előfizetést.

---

## <a name="create-a-local-function-project"></a>Helyi függvény projekt létrehozása

Azure Functions egy függvény-projekt egy vagy több olyan egyedi függvény tárolója, amely mindegyik reagál egy adott triggerre. Egy projekt összes funkciója ugyanazokat a helyi és üzemeltetési konfigurációkat használja. Ebben a szakaszban egy függvény-projektet hoz létre, amely egyetlen függvényt tartalmaz.

1. Futtassa a `func init` parancsot az alábbiak szerint, hogy hozzon létre egy functions-projektet egy *LocalFunctionProj* nevű mappában a megadott futtatókörnyezettel:  

    ```console
    func init LocalFunctionProj --powershell
    ```

1. Navigáljon a projekt mappájába:

    ```console
    cd LocalFunctionProj
    ```
    
    Ez a mappa a projekthez különböző fájlokat tartalmaz, beleértve a [local.settings.json](functions-run-local.md#local-settings-file) és a [host.js](functions-host-json.md)nevű konfigurációs fájlokat is. Mivel a *local.settings.json* az Azure-ból letöltött titkos kódok is lehetnek, a fájl a *. gitignore* fájlban alapértelmezés szerint ki van zárva a forrás-vezérlőelemből.
    
1. Adjon hozzá egy függvényt a projekthez a következő parancs használatával, ahol az `--name` argumentum a függvény egyedi neve (HttpExample), az argumentum pedig a `--template` függvény triggerét (http) adja meg. 

    ```console
    func new --name HttpExample --template "HTTP trigger" --authlevel "anonymous"
    ```   
    
    `func new` egy olyan almappát hoz létre, amely megfelel a projekt választott nyelvének és a *function.json* nevű konfigurációs fájlnak.

### <a name="optional-examine-the-file-contents"></a>Választható A fájl tartalmának vizsgálata

Ha szeretné, kihagyhatja [a függvény helyi futtatását](#run-the-function-locally) , és később is megvizsgálhatja a fájl tartalmát.

#### <a name="runps1"></a>run.ps1

*run.ps1* definiál egy olyan függvény-parancsfájlt, amely a *function.js* beállításának megfelelően aktiválódik.

:::code language="powershell" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-PowerShell/run.ps1":::

A HTTP-triggerek esetében a függvény fogadja a `$Request` *function.js* által megadott paraméternek átadott kérelmeket. A rendszer a (z)function.jsban definiált visszaadott objektumot adja `Response` át  `Push-OutputBinding` válaszként a parancsmagnak. 

#### <a name="functionjson"></a>function.json

A *function.json* egy konfigurációs fájl, amely meghatározza a függvény bemenetét és kimenetét `bindings` , beleértve az trigger típusát is. 

:::code language="json" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-PowerShell/function.json":::

Minden kötéshez meg kell adni egy irányt, egy típust és egy egyedi nevet. A HTTP-trigger típusa [`httpTrigger`](functions-bindings-http-webhook-trigger.md) és kimeneti kötése típusú bemeneti kötést tartalmaz [`http`](functions-bindings-http-webhook-output.md) .

[!INCLUDE [functions-run-function-test-local-cli](../../includes/functions-run-function-test-local-cli.md)]

[!INCLUDE [functions-create-azure-resources-cli](../../includes/functions-create-azure-resources-cli.md)]

4. A Function alkalmazás létrehozása az Azure-ban:

    # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
        
    ```azurecli
    az functionapp create --resource-group AzureFunctionsQuickstart-rg --consumption-plan-location westeurope --runtime powershell --functions-version 3 --name <APP_NAME> --storage-account <STORAGE_NAME>
    ```
    
    Az az [functionapp Create](/cli/azure/functionapp#az_functionapp_create) parancs létrehozza a Function alkalmazást az Azure-ban. 
    
    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)
    
    ```azurepowershell
    New-AzFunctionApp -Name <APP_NAME> -ResourceGroupName AzureFunctionsQuickstart-rg -StorageAccount <STORAGE_NAME> -Runtime PowerShell -FunctionsVersion 3 -Location 'West Europe'
    ```
    
    A [New-AzFunctionApp](/powershell/module/az.functions/new-azfunctionapp) parancsmag létrehozza a Function alkalmazást az Azure-ban. 
    
    ---
    
    Az előző példában a helyére írja be az `<STORAGE_NAME>` előző lépésben használt fiók nevét, és cserélje le az értékét a `<APP_NAME>` megfelelő globálisan egyedi névre. Az `<APP_NAME>` egyben a függvényalkalmazás alapértelmezett DNS-tartományaként is szolgál, 
    
    Ez a parancs létrehoz egy, a megadott nyelvi futtatókörnyezetben futó Function alkalmazást a [Azure functions használati terv](consumption-plan.md)alatt, amely ingyenesen használható az itt felmerülő felhasználási mennyiséghez. A parancs egy kapcsolódó Azure Application Insights-példányt is kiépít ugyanabban az erőforráscsoporthoz, amellyel nyomon követheti a Function alkalmazást, és megtekintheti a naplókat. További információ: [Azure functions figyelése](functions-monitoring.md). A példány nem jár költséggel, amíg be nem aktiválja.

[!INCLUDE [functions-publish-project-cli](../../includes/functions-publish-project-cli.md)]

[!INCLUDE [functions-run-remote-azure-cli](../../includes/functions-run-remote-azure-cli.md)]

[!INCLUDE [functions-streaming-logs-cli-qs](../../includes/functions-streaming-logs-cli-qs.md)]

[!INCLUDE [functions-cleanup-resources-cli](../../includes/functions-cleanup-resources-cli.md)]

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Kapcsolódás Azure Storage-várólistához]

[Kapcsolódás Azure Storage-várólistához]: functions-add-output-binding-storage-queue-cli.md?pivots=programming-language-powershell
