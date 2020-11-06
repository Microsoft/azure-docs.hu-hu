---
title: Python-függvény létrehozása a parancssorból – Azure Functions
description: Megtudhatja, hogyan hozhat létre Python-függvényt a parancssorból, majd hogyan teheti közzé a helyi projektet a Azure Functions kiszolgáló nélküli üzemeltetéséhez.
ms.date: 11/03/2020
ms.topic: quickstart
ms.custom:
- devx-track-python
- devx-track-azurecli
ms.openlocfilehash: 71745243c29fac95fca452f3c907bcf50982ea97
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/06/2020
ms.locfileid: "93425048"
---
# <a name="quickstart-create-a-python-function-in-azure-from-the-command-line"></a>Gyors útmutató: Python-függvény létrehozása az Azure-ban a parancssorból

[!INCLUDE [functions-language-selector-quickstart-cli](../../includes/functions-language-selector-quickstart-cli.md)]

Ebben a cikkben parancssori eszközöket használ egy olyan Python-függvény létrehozásához, amely válaszol a HTTP-kérelmekre. A kód helyi tesztelését követően a Azure Functions kiszolgáló nélküli környezetében helyezheti üzembe.

A rövid útmutató elvégzésével az Azure-fiókjában néhány USD értékű vagy annál kisebb költséggel jár.

A cikk [Visual Studio Code-alapú verziója](create-first-function-vs-code-python.md) is létezik.

## <a name="configure-your-local-environment"></a>A helyi környezet konfigurálása

Mielőtt elkezdené, a következőkkel kell rendelkeznie:

+ Aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egy fiókot ingyenesen](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

+ A [Azure functions Core Tools](functions-run-local.md#v2) 3. x verzió.
  
+ Az alábbi eszközök egyike az Azure-erőforrások létrehozásához:

    + Az [Azure CLI](/cli/azure/install-azure-cli) 2,4-es vagy újabb verziója.

    + [Azure PowerShell](/powershell/azure/install-az-ps) 5,0-es vagy újabb verzió.

+ [Python 3,8 (64 bites)](https://www.python.org/downloads/release/python-382/), [python 3,7 (64-bit)](https://www.python.org/downloads/release/python-375/), [Python 3,6 (64-bit)](https://www.python.org/downloads/release/python-368/), amelyek mindegyike támogatott a 3. x verzióban Azure functions.

### <a name="prerequisite-check"></a>Előfeltételek ellenőrzése

Ellenőrizze az előfeltételeket, amelyek attól függnek, hogy az Azure CLI-t vagy Azure PowerShell Azure-erőforrások létrehozásához használja-e:

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

+ A terminál vagy a parancssorablakban futtassa a parancsot az `func --version` Azure functions Core Tools 3. x verziójának megadásához.

+ Futtassa az parancsot az `az --version` Azure CLI 2,4-es vagy újabb verziójának megadásához.

+ A futtatásával `az login` Jelentkezzen be az Azure-ba, és ellenőrizze az aktív előfizetést.

+ Futtassa `python --version` a (Linux/MacOS) vagy a `py --version` (Windows) rendszert a Python-verziók 3,8. x, 3.7. x vagy 3.6. x verziójának vizsgálatához.

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

+ A terminál vagy a parancssorablakban futtassa a parancsot az `func --version` Azure functions Core Tools 3. x verziójának megadásához.

+ Futtassa `(Get-Module -ListAvailable Az).Version` és ellenőrizze a 5,0-es vagy újabb verziót. 

+ A futtatásával `Connect-AzAccount` Jelentkezzen be az Azure-ba, és ellenőrizze az aktív előfizetést.

+ Futtassa `python --version` a (Linux/MacOS) vagy a `py --version` (Windows) rendszert a Python-verziók 3,8. x, 3.7. x vagy 3.6. x verziójának vizsgálatához.

---

## <a name="create-a-local-function-project"></a>Helyi függvény projekt létrehozása

Azure Functions egy függvény-projekt egy vagy több olyan egyedi függvény tárolója, amely mindegyik reagál egy adott triggerre. Egy projekt összes funkciója ugyanazokat a helyi és üzemeltetési konfigurációkat használja. Ebben a szakaszban egy függvény-projektet hoz létre, amely egyetlen függvényt tartalmaz.

1. Futtassa a `func init` parancsot az alábbiak szerint, hogy hozzon létre egy functions-projektet egy *LocalFunctionProj* nevű mappában a megadott futtatókörnyezettel:  

    ```console
    func init LocalFunctionProj --python
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

#### <a name="__init__py"></a>\_\_init \_ \_ . a

az *\_ \_ init \_ \_ .* a (z `main()` ) egy Python-függvényt tartalmaz, amely a *function.js* konfigurációjának megfelelően aktiválódik.

:::code language="python" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-Python/__init__.py":::

HTTP-trigger esetén a függvény a `req` *function.json* megadottak szerint fogadja a kérelmeket a változóban. `req` az [Azure. functions. HttpRequest osztály](/python/api/azure-functions/azure.functions.httprequest)egy példánya. Afunction.json értékben definiált visszatérési objektum az `$return` [Azure. functions. HttpResponse osztály](/python/api/azure-functions/azure.functions.httpresponse)egy példánya. *function.json* További információ: [Azure FUNCTIONS http-eseményindítók és-kötések](./functions-bindings-http-webhook.md?tabs=python).

#### <a name="functionjson"></a>function.json

A *function.json* egy konfigurációs fájl, amely meghatározza a függvény bemenetét és kimenetét `bindings` , beleértve az trigger típusát is.

Ha kívánja `scriptFile` , másik Python-fájlt is meghívhat.

:::code language="json" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-Python/function.json":::

Minden kötéshez meg kell adni egy irányt, egy típust és egy egyedi nevet. A HTTP-trigger típusa [`httpTrigger`](functions-bindings-http-webhook-trigger.md) és kimeneti kötése típusú bemeneti kötést tartalmaz [`http`](functions-bindings-http-webhook-output.md) .

[!INCLUDE [functions-run-function-test-local-cli](../../includes/functions-run-function-test-local-cli.md)]

## <a name="create-supporting-azure-resources-for-your-function"></a>A függvényhez kapcsolódó Azure-erőforrások létrehozása

Mielőtt üzembe helyezi a függvény kódját az Azure-ban, három erőforrást kell létrehoznia:

- Egy erőforráscsoport, amely a kapcsolódó erőforrások logikai tárolója.
- Egy Storage-fiók, amely fenntartja az állapotot és a projektekkel kapcsolatos egyéb információkat.
- Egy Function alkalmazás, amely biztosítja a környezetet a függvény kódjának végrehajtásához. A Function alkalmazás a helyi function projekthez kapcsolódik, és lehetővé teszi, hogy logikai egységként csoportosítsa a függvényeket az erőforrások egyszerűbb felügyelete, üzembe helyezése és megosztása érdekében.

Ezen elemek létrehozásához használja a következő parancsokat. Az Azure CLI és a PowerShell is támogatott.

1. Ha még nem tette meg, jelentkezzen be az Azure-ba:

    # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
    ```azurecli
    az login
    ```

    Az az [login](/cli/azure/reference-index#az-login) parancs aláírja az Azure-fiókját.

    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell) 
    ```azurepowershell
    Connect-AzAccount
    ```

    A [AzAccount](/powershell/module/az.accounts/connect-azaccount) parancsmag bejelentkezik az Azure-fiókjába.

    ---

1. Hozzon létre egy nevű erőforráscsoportot `AzureFunctionsQuickstart-rg` a `westeurope` régióban. 

    # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
    
    ```azurecli
    az group create --name AzureFunctionsQuickstart-rg --location westeurope
    ```
 
    Az az [Group Create](/cli/azure/group#az-group-create) parancs létrehoz egy erőforráscsoportot. Az erőforráscsoport és az erőforrások általában az Ön közelében lévő régiókban hozhatók létre, a parancs által visszaadott elérhető régió használatával `az account list-locations` .

    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

    ```azurepowershell
    New-AzResourceGroup -Name AzureFunctionsQuickstart-rg -Location westeurope
    ```

    A [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) parancs létrehoz egy erőforráscsoportot. A [Get-AzLocation](https://docs.microsoft.com/powershell/module/az.resources/get-azlocation) parancsmag által visszaadott elérhető régió használatával általában létrehozhatja az erőforráscsoportot és az erőforrásokat egy közeli régióban.

    ---

    > [!NOTE]
    > Nem futtathat Linux-és Windows-alkalmazásokat ugyanabban az erőforráscsoporthoz. Ha már van egy nevű erőforráscsoport `AzureFunctionsQuickstart-rg` egy Windows-függvény alkalmazással vagy webalkalmazással, egy másik erőforráscsoportot kell használnia.

1. Hozzon létre egy általános célú Storage-fiókot az erőforráscsoport és a régió területén:

    # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

    ```azurecli
    az storage account create --name <STORAGE_NAME> --location westeurope --resource-group AzureFunctionsQuickstart-rg --sku Standard_LRS
    ```

    Az az [Storage Account Create](/cli/azure/storage/account#az-storage-account-create) parancs létrehozza a Storage-fiókot. 

    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

    ```azurepowershell
    New-AzStorageAccount -ResourceGroupName AzureFunctionsQuickstart-rg -Name <STORAGE_NAME> -SkuName Standard_LRS -Location westeurope
    ```

    A [New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount) parancsmag létrehozza a Storage-fiókot.

    ---

    Az előző példában cserélje le a `<STORAGE_NAME>` nevet, amely megfelel az Ön számára, és egyedi az Azure Storage-ban. A névnek három – 24 karakterből kell állnia, és csak kisbetűket tartalmazhat. `Standard_LRS` a [függvények által támogatott](storage-considerations.md#storage-account-requirements)általános célú fiókot határozza meg.
    
    A Storage-fiók ebben a rövid útmutatóban csak néhány cent (USD) értékkel rendelkezik.

1. A Function alkalmazás létrehozása az Azure-ban:

    # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
        
    ```azurecli
    az functionapp create --resource-group AzureFunctionsQuickstart-rg --consumption-plan-location westeurope --runtime python --runtime-version 3.8 --functions-version 3 --name <APP_NAME> --storage-account <STORAGE_NAME>
    ```
    
    Az az [functionapp Create](/cli/azure/functionapp#az_functionapp_create) parancs létrehozza a Function alkalmazást az Azure-ban. Ha Python 3,7 vagy 3,6-et használ, váltson a következőre: `--runtime-version` `3.7` vagy `3.6` , illetve.
    
    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)
    
    ```azurepowershell
    New-AzFunctionApp -Name <APP_NAME> -ResourceGroupName AzureFunctionsQuickstart-rg -StorageAccount <STORAGE_NAME> -FunctionsVersion 3 -RuntimeVersion 3.8 -Runtime python -Location 'West Europe'
    ```
    
    <a name="the-new-azfunctionapp-cmdlet-creates-the-function-app-in-azure-if-youre-using-python-37-or-36-change--runtimeversion-to-37-or-36-respectively"></a>A [New-AzFunctionApp](/powershell/module/az.functions/new-azfunctionapp) parancsmag létrehozza a Function alkalmazást az Azure-ban. Ha Python 3,7 vagy 3,6-et használ, váltson a következőre: `-RuntimeVersion` `3.7` vagy `3.6` , illetve.
    ---
    
    Az előző példában a helyére írja be az `<STORAGE_NAME>` előző lépésben használt fiók nevét, és cserélje le az értékét a `<APP_NAME>` megfelelő globálisan egyedi névre.  Az `<APP_NAME>` egyben a függvényalkalmazás alapértelmezett DNS-tartományaként is szolgál, 
    
    Ez a parancs létrehoz egy, a megadott nyelvi futtatókörnyezetben futó Function alkalmazást a [Azure functions használati terv](functions-scale.md#consumption-plan)alatt, amely ingyenesen használható az itt felmerülő felhasználási mennyiséghez. A parancs egy kapcsolódó Azure Application Insights-példányt is kiépít ugyanabban az erőforráscsoporthoz, amellyel nyomon követheti a Function alkalmazást, és megtekintheti a naplókat. További információ: [Azure functions figyelése](functions-monitoring.md). A példány nem jár költséggel, amíg be nem aktiválja.

[!INCLUDE [functions-publish-project-cli](../../includes/functions-publish-project-cli.md)]

[!INCLUDE [functions-run-remote-azure-cli](../../includes/functions-run-remote-azure-cli.md)]

A következő parancs futtatásával tekintheti meg a közel valós idejű [adatfolyam-naplókat](functions-run-local.md#enable-streaming-logs) a Azure Portal Application Insightsban:

```console
func azure functionapp logstream <APP_NAME> --browser
```

Egy külön terminál-ablakban vagy a böngészőben hívja meg újra a távoli függvényt. Az Azure-ban a funkció végrehajtásának részletes naplója látható a terminálon. 

[!INCLUDE [functions-cleanup-resources-cli](../../includes/functions-cleanup-resources-cli.md)]

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Kapcsolódás Azure Storage-várólistához](functions-add-output-binding-storage-queue-cli.md?pivots=programming-language-python)
