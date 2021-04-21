---
title: Python-függvény létrehozása a parancssorból – Azure Functions
description: Megtudhatja, hogyan hozhat létre Python-függvényt a parancssorból, majd hogyan tehet közzé egy helyi projektet a kiszolgáló nélküli üzemeltetéshez a Azure Functions.
ms.date: 11/03/2020
ms.topic: quickstart
ms.custom:
- devx-track-python
- devx-track-azurecli
adobe-target: true
adobe-target-activity: DocsExp–386541–A/B–Enhanced-Readability-Quickstarts–2.19.2021
adobe-target-experience: Experience B
adobe-target-content: ./create-first-function-cli-python-uiex
ms.openlocfilehash: f5c51630d111bd68e311a93100abb8266e2a8e27
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107787430"
---
# <a name="quickstart-create-a-python-function-in-azure-from-the-command-line"></a>Rövid útmutató: Python-függvény létrehozása az Azure-ban a parancssorból

[!INCLUDE [functions-language-selector-quickstart-cli](../../includes/functions-language-selector-quickstart-cli.md)]

Ebben a cikkben parancssori eszközökkel hoz létre egy Python-függvényt, amely HTTP-kérésekre válaszol. A kód helyi tesztelése után üzembe kell helyeznie a Azure Functions.

A rövid útmutató elvégzése néhány dollár vagy annál kisebb költséggel jár az Azure-fiókban.

A cikk Visual Studio [kódalapú](create-first-function-vs-code-python.md) verziója is elérhető.

## <a name="configure-your-local-environment"></a>A helyi környezet konfigurálása

Mielőtt hozzákezd, a következőkre lesz majd kíváncsi:

+ Aktív előfizetéssel rendelkezik egy Azure-fiók. [Hozzon létre egy ingyenes fiókot.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)

+ A [Azure Functions Core Tools](functions-run-local.md#v2) 3.x verzió.
  
+ Az azure-erőforrások létrehozására vonatkozó alábbi eszközök egyike:

    + [Az Azure CLI](/cli/azure/install-azure-cli) 2.4-es vagy újabb verziója.

    + [Azure PowerShell](/powershell/azure/install-az-ps) 5.0-s vagy újabb verziót.

+ [A Azure Functions](supported-languages.md#languages-by-runtime-version)

### <a name="prerequisite-check"></a>Előfeltétel-ellenőrzés

Ellenőrizze az előfeltételeket, amelyek attól függnek, hogy az Azure CLI-t vagy Azure PowerShell azure-erőforrások létrehozására használja:

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

+ Egy terminálban vagy parancsablakban futtassa a parancsot annak ellenőrzéshez, hogy a Azure Functions Core Tools `func --version` 3.x verziójú-e.

+ Az `az --version` futtatásával ellenőrizze, hogy az Azure CLI 2.4-es vagy újabb verziója van-e.

+ Az `az login` futtatásával jelentkezzen be az Azure-ba, és ellenőrizze az aktív előfizetést.

+ Futtassa `python --version` a (Linux/macOS) vagy a (Windows) rendszert a `py --version` Python-verziójelentések 3.8.x, 3.7.x vagy 3.6.x verziójának ellenőrzéshez.

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

+ Egy terminálban vagy parancsablakban futtassa a parancsot annak ellenőrzéshez, hogy a Azure Functions Core Tools `func --version` 3.x verziójú-e.

+ Futtassa `(Get-Module -ListAvailable Az).Version` és ellenőrizze az 5.0-s vagy újabb verziót. 

+ Az `Connect-AzAccount` futtatásával jelentkezzen be az Azure-ba, és ellenőrizze az aktív előfizetést.

+ Futtassa `python --version` a (Linux/macOS) vagy a (Windows) rendszert a `py --version` Python-verziójelentések 3.8.x, 3.7.x vagy 3.6.x verziójának ellenőrzéshez.

---

## <a name="create-and-activate-a-virtual-environment"></a><a name="create-venv"></a>Virtuális környezet létrehozása és aktiválása

Futtassa a következő parancsokat egy nevű virtuális környezet létrehozásához és aktiválásához egy megfelelő `.venv` mappában. Mindenképpen a Python 3.8-as, 3.7-es vagy 3.6-os verzióját használja, amelyet a Azure Functions.

# <a name="bash"></a>[Bash](#tab/bash)

```bash
python -m venv .venv
```

```bash
source .venv/bin/activate
```

Ha a Python nem telepíti a Venv-csomagot a Linux-disztribúción, futtassa a következő parancsot:

```bash
sudo apt-get install python3-venv
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
py -m venv .venv
```

```powershell
.venv\scripts\activate
```

# <a name="cmd"></a>[Cmd](#tab/cmd)

```cmd
py -m venv .venv
```

```cmd
.venv\scripts\activate
```

---

Az összes további parancsot ebben az aktivált virtuális környezetben futtatja. 

## <a name="create-a-local-function-project"></a>Helyi függvényprojekt létrehozása

A Azure Functions a függvényprojekt egy tároló egy vagy több különálló függvény számára, amelyek egy adott eseményindítóra válaszolnak. Egy projekt összes függvénye azonos helyi és üzemeltetési konfigurációval rendelkezik. Ebben a szakaszban egy függvényprojektet hoz létre, amely egyetlen függvényt tartalmaz.

1. Futtassa a parancsot az alábbiak szerint, hogy létrehozzon egy függvényprojektet `func init` egy *LocalFunctionProj* nevű mappában a megadott futásidejűvel:  

    ```console
    func init LocalFunctionProj --python
    ```

1. Lépjen a projektmappába:

    ```console
    cd LocalFunctionProj
    ```
    
    Ez a mappa a projekt különböző fájljait tartalmazza, beleértve a fájlnevű konfigurációslocal.settings.js[és](functions-run-local.md#local-settings-file)host.js[a fájlban.](functions-host-json.md) Mivel *local.settings.jsa fájl* tartalmazhat az Azure-ból letöltött titkos adatokat, a fájl alapértelmezés szerint ki van zárva a verziókezelőből a *.gitignore fájlban.*

1. Adjon hozzá egy függvényt a projekthez a következő paranccsal, ahol az argumentum a függvény (HttpExample) egyedi neve, az argumentum pedig a függvény eseményindítóját `--name` `--template` (HTTP) határozza meg.

    ```console
    func new --name HttpExample --template "HTTP trigger" --authlevel "anonymous"
    ```   
    
    `func new` létrehoz egy almappát, amely megegyezik a függvény nevével, amely tartalmazza a projekt választott nyelvének megfelelő kódfájlt, valamint egy nevű konfigurációs *function.jsa következőn:*.

### <a name="optional-examine-the-file-contents"></a>(Nem kötelező) A fájl tartalmának vizsgálata

Ha szeretné, ugorjon a Függvény helyi [futtatása](#run-the-function-locally) részhez, és később vizsgálja meg a fájl tartalmát.

#### <a name="__init__py"></a>\_\_init \_ \_ .py

*\_ \_ Az init \_ \_ .py* egy Python-függvényt tartalmaz, amely a fájlban található `main()` *function.jsalapján aktiválódik.*

:::code language="python" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-Python/__init__.py":::

HTTP-eseményindító esetén a függvény a változóban kapja meg a kérelemadatokat a(function.js) `req` *változóban.* `req`A az [azure.functions.HttpRequest osztály egy példánya.](/python/api/azure-functions/azure.functions.httprequest) A visszaadott objektum a (függvények)function.js`$return` *az* [azure.functions.HttpResponse osztály egy példánya.](/python/api/azure-functions/azure.functions.httpresponse) További információ: HTTP Azure Functions [triggerek és kötések.](./functions-bindings-http-webhook.md?tabs=python)

#### <a name="functionjson"></a>function.json

*function.jsegy* konfigurációs fájl, amely meghatározza a függvény bemenetét és kimenetét, `bindings` beleértve az eseményindító típusát is.

Ha szeretné, másik `scriptFile` Python-fájlt is meghívhat.

:::code language="json" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-Python/function.json":::

Minden kötéshez szükség van egy irányra, egy típusra és egy egyedi névre. A HTTP-eseményindító típusú bemeneti és kimeneti [`httpTrigger`](functions-bindings-http-webhook-trigger.md) kötéssel [`http`](functions-bindings-http-webhook-output.md) rendelkezik.

[!INCLUDE [functions-run-function-test-local-cli](../../includes/functions-run-function-test-local-cli.md)]

## <a name="create-supporting-azure-resources-for-your-function"></a>Támogató Azure-erőforrások létrehozása a függvényhez

Mielőtt üzembe helyezheti a függvénykódot az Azure-ban, három erőforrást kell létrehoznia:

- Egy erőforráscsoport, amely a kapcsolódó erőforrások logikai tárolója.
- Egy Storage-fiók, amely fenntartja a projektek állapotát és egyéb adatait.
- Egy függvényalkalmazást, amely biztosítja a függvénykód futtatásának környezetét. A függvényalkalmazás a helyi függvényprojekthez van leképezve, és lehetővé teszi a függvények logikai egységként való csoportosítását az erőforrások egyszerűbb kezelése, üzembe helyezése és megosztása érdekében.

Ezeket az elemeket az alábbi parancsokkal hozhatja létre. Az Azure CLI és a PowerShell is támogatott.

1. Ha még nem tette meg, jelentkezzen be az Azure-ba:

    # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
    ```azurecli
    az login
    ```

    Az [az login](/cli/azure/reference-index#az_login) parancs bejelentkeztet az Azure-fiókjába.

    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell) 
    ```azurepowershell
    Connect-AzAccount
    ```

    A [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) parancsmag bejelentkeztet az Azure-fiókjába.

    ---

1. Hozzon létre egy nevű `AzureFunctionsQuickstart-rg` erőforráscsoportot a `westeurope` régióban. 

    # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
    
    ```azurecli
    az group create --name AzureFunctionsQuickstart-rg --location westeurope
    ```
 
    Az [az group create parancs](/cli/azure/group#az_group_create) létrehoz egy erőforráscsoportot. Az erőforráscsoportot és az erőforrásokat általában egy Közeli régióban hozza létre a parancs által visszaadott elérhető régió `az account list-locations` használatával.

    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

    ```azurepowershell
    New-AzResourceGroup -Name AzureFunctionsQuickstart-rg -Location westeurope
    ```

    A [New-AzResourceGroup parancs](/powershell/module/az.resources/new-azresourcegroup) létrehoz egy erőforráscsoportot. Az erőforráscsoportot és az erőforrásokat általában egy közeli régióban hozza létre a [Get-AzLocation](/powershell/module/az.resources/get-azlocation) parancsmag által visszaadott elérhető régió használatával.

    ---

    > [!NOTE]
    > Linux- és Windows-alkalmazások nem használhatók ugyanabban az erőforráscsoportban. Ha rendelkezik egy nevű meglévő erőforráscsoporttal egy Windows-függvényalkalmazással vagy `AzureFunctionsQuickstart-rg` -webalkalmazással, másik erőforráscsoportot kell használnia.

1. Hozzon létre egy általános célú tárfiókot az erőforráscsoportban és régióban:

    # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

    ```azurecli
    az storage account create --name <STORAGE_NAME> --location westeurope --resource-group AzureFunctionsQuickstart-rg --sku Standard_LRS
    ```

    Az [az storage account create parancs](/cli/azure/storage/account#az_storage_account_create) létrehozza a tárfiókot. 

    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

    ```azurepowershell
    New-AzStorageAccount -ResourceGroupName AzureFunctionsQuickstart-rg -Name <STORAGE_NAME> -SkuName Standard_LRS -Location westeurope
    ```

    A [New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount) parancsmag létrehozza a tárfiókot.

    ---

    Az előző példában cserélje le a helyére az Ön számára megfelelő, az `<STORAGE_NAME>` Azure Storage-ban egyedi nevet. A neveknek három–24 karakter hosszúságú számokat és csak kisbetűket tartalmazhatnak. `Standard_LRS`A egy általános célú fiókot ad meg, amelyet [a Functions támogat.](storage-considerations.md#storage-account-requirements)
    
    Ebben a rövid útmutatóban a tárfiók csak néhány centet (USD) jelent.

1. A függvényalkalmazás létrehozása az Azure-ban:

    # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
        
    ```azurecli
    az functionapp create --resource-group AzureFunctionsQuickstart-rg --consumption-plan-location westeurope --runtime python --runtime-version 3.8 --functions-version 3 --name <APP_NAME> --storage-account <STORAGE_NAME> --os-type linux
    ```
    
    Az [az functionapp create parancs](/cli/azure/functionapp#az_functionapp_create) létrehozza a függvényalkalmazást az Azure-ban. Ha Python 3.7-et vagy 3.6-ot használ, módosítsa a vagy `--runtime-version` `3.7` a `3.6` értékre.
    
    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)
    
    ```azurepowershell
    New-AzFunctionApp -Name <APP_NAME> -ResourceGroupName AzureFunctionsQuickstart-rg -StorageAccount <STORAGE_NAME> -FunctionsVersion 3 -RuntimeVersion 3.8 -Runtime python -Location 'West Europe'
    ```
    
    A [New-AzFunctionApp](/powershell/module/az.functions/new-azfunctionapp) parancsmag létrehozza a függvényalkalmazást az Azure-ban. Ha Python 3.7-et vagy 3.6-ot használ, módosítsa a vagy a `-RuntimeVersion` `3.7` `3.6` értékre.

    ---
    
    Az előző példában cserélje le a helyére az előző lépésben használt fiók nevét, a helyére pedig az Önnek megfelelő globálisan `<STORAGE_NAME>` `<APP_NAME>` egyedi nevet.  Az `<APP_NAME>` egyben a függvényalkalmazás alapértelmezett DNS-tartományaként is szolgál, 
    
    Ez a parancs létrehoz egy függvényalkalmazást, amely a megadott nyelvi futtatókörnyezetben fut a [Azure Functions használat](consumption-plan.md)alapján, amely ingyenes az itt használt mennyiségért. A parancs egy társított Azure Application Insights-példányt is kiad ugyanabban az erőforráscsoportban, amellyel figyelheti a függvényalkalmazást és megtekintheti a naplókat. További információ: [Monitor Azure Functions.](functions-monitoring.md) A példány nem jár költségekkel, amíg ön nem aktiválja.

[!INCLUDE [functions-publish-project-cli](../../includes/functions-publish-project-cli.md)]

[!INCLUDE [functions-run-remote-azure-cli](../../includes/functions-run-remote-azure-cli.md)]

Futtassa a következő parancsot a [](functions-run-local.md#enable-streaming-logs) streamnaplók közel valós idejű megtekintéséhez Application Insights a Azure Portal:

```console
func azure functionapp logstream <APP_NAME> --browser
```

Egy külön terminálablakban vagy a böngészőben hívja meg újra a távoli függvényt. A terminálon megjelenik a függvény Azure-beli végrehajtásának részletes naplója. 

[!INCLUDE [functions-cleanup-resources-cli](../../includes/functions-cleanup-resources-cli.md)]

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Csatlakozás Azure Storage-üzenetsorhoz](functions-add-output-binding-storage-queue-cli.md?pivots=programming-language-python)

[Problémákat? Tudajuk meg.](https://aka.ms/python-functions-qs-survey)