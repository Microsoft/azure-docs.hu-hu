---
title: Python-függvény létrehozása a parancssorból a Azure Functions
description: Megtudhatja, hogyan hozhat létre Python-függvényt a parancssorból, és hogyan tehet közzé egy helyi projektet a kiszolgáló nélküli üzemeltetéshez a Azure Functions.
ms.date: 11/03/2020
ms.topic: quickstart
ms.custom:
- devx-track-powershell
- devx-track-azurecli
- devx-track-azurepowershell
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: e144304ae1b36ca02d4b8796e7994e87b09505d9
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2021
ms.locfileid: "107831095"
---
# <a name="quickstart-create-a-python-function-in-azure-from-the-command-line"></a>Rövid útmutató: Python-függvény létrehozása az Azure-ban a parancssorból

> [!div class="op_single_selector" title1="Válassza ki a függvény nyelvét: "]
> - [Python](create-first-function-cli-python.md)
> - [C#](create-first-function-cli-csharp.md)
> - [Java](create-first-function-cli-java.md)
> - [JavaScript](create-first-function-cli-node.md)
> - [PowerShell](create-first-function-cli-powershell.md)
> - [TypeScript](create-first-function-cli-typescript.md)

Ebben a cikkben parancssori eszközökkel hoz létre egy Python-függvényt, amely HTTP-kérésekre válaszol. A kód helyi tesztelése után üzembe helyezheti a <abbr title="Egy futásidejű számítási környezet, amelyben a kiszolgáló összes adata átlátható az alkalmazásfejlesztők számára, ami leegyszerűsíti a kód üzembe helyezésének és kezelésének folyamatát.">kiszolgáló nélküli</abbr> környezete <abbr title="Egy Azure-szolgáltatás, amely alacsony költségű, kiszolgáló nélküli számítási környezetet biztosít az alkalmazások számára.">Azure Functions</abbr>.

A rövid útmutató elvégzése néhány dollár vagy annál kisebb költséggel jár az Azure-fiókban.

A cikk Visual Studio [kódalapú](create-first-function-vs-code-python.md) verziója is elérhető.

## <a name="1-configure-your-environment"></a>1. A környezet konfigurálása

Mielőtt hozzákezd, a következőkre lesz majd kíváncsi:

+ Egy Azure <abbr title="Az Azure-használat számlázási adatait fenntartó profil.">account</abbr> aktív <abbr title="Az alapvető szervezeti struktúra, amelyben az Azure-ban kezelheti az erőforrásokat, általában egy szervezeten belüli egyéni vagy részleghez társítva.">előfizetést</abbr>. [Hozzon létre egy ingyenes fiókot.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)

+ A [Azure Functions Core Tools](functions-run-local.md#v2) 3.x verzió. 
  
+ Vagy a <abbr title="Platformfüggetlen parancssori eszközök a helyi fejlesztői számítógépről származó Azure-erőforrásokkal való munkavégzéshez, a virtuális gép használatának Azure Portal.">Azure CLI</abbr> vagy <abbr title="Egy PowerShell-modul, amely parancsokat biztosít az Azure-erőforrásokkal való munkavégzéshez a helyi fejlesztői számítógépről a virtuális gép Azure Portal.">Azure PowerShell</abbr> Azure-erőforrások létrehozásához:

    + [Az Azure CLI](/cli/azure/install-azure-cli) 2.4-es vagy újabb verziója.

    + [Azure PowerShell](/powershell/azure/install-az-ps) 5.0-s vagy újabb verzió.

+ [Python 3.8 (64 bites)](https://www.python.org/downloads/release/python-382/), [Python 3.7 (64 bites)](https://www.python.org/downloads/release/python-375/), [Python 3.6 (64 bites)](https://www.python.org/downloads/release/python-368/), amelyek mindegyikét a Azure Functions.

### <a name="11-prerequisite-check"></a>1.1 Előfeltétel-ellenőrzés

Ellenőrizze az előfeltételeket, amelyek attól függnek, hogy az Azure CLI-t vagy a Azure PowerShell azure-erőforrások létrehozásához használja:

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

+ Egy terminálban vagy parancsablakban futtassa a parancsot `func --version` annak ellenőrzéshez, hogy a <abbr title="Parancssori eszközök a helyi számítógépen Azure Functions való munkavégzéshez.">Azure Functions Core Tools</abbr> A a 3.x verziójú.

+ A `az --version` futtatásával ellenőrizze, hogy az Azure CLI 2.4-es vagy újabb verziója van-e.

+ Az `az login` futtatásával jelentkezzen be az Azure-ba, és ellenőrizze az aktív előfizetést.

+ Futtassa `python --version` a (Linux/macOS) vagy a (Windows) rendszert a `py --version` Python-verziójelentések 3.8.x, 3.7.x vagy 3.6.x verziójának ellenőrzéshez.

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

+ Egy terminálban vagy parancsablakban futtassa a parancsot `func --version` annak ellenőrzéshez, hogy a <abbr title="Parancssori eszközök a helyi számítógépen Azure Functions való munkavégzéshez.">Azure Functions Core Tools</abbr> A a 3.x verziójú.

+ Futtassa `(Get-Module -ListAvailable Az).Version` és ellenőrizze az 5.0-s vagy újabb verziót. 

+ Az `Connect-AzAccount` futtatásával jelentkezzen be az Azure-ba, és ellenőrizze az aktív előfizetést.

+ Futtassa `python --version` a (Linux/macOS) vagy a (Windows) rendszert a `py --version` Python-verziójelentések 3.8.x, 3.7.x vagy 3.6.x verziójának ellenőrzéshez.

---

<br/>

---

## <a name="2-create-and-activate-a-virtual-environment"></a>2. <a name="create-venv"></a> Virtuális környezet létrehozása és aktiválása

Egy megfelelő mappában futtassa a következő parancsokat egy nevű virtuális környezet létrehozásához és `.venv` aktiválásához. Mindenképpen a Python 3.8-as, 3.7-es vagy 3.6-os verzióját használja, amelyet a Azure Functions.

# <a name="bash"></a>[Bash](#tab/bash)

```bash
python -m venv .venv
```

```bash
source .venv/bin/activate
```

Ha a Python nem telepíti a venv csomagot a Linux-disztribúción, futtassa a következő parancsot:

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

<br/>

---

## <a name="3-create-a-local-function-project"></a>3. Helyi függvényprojekt létrehozása

Ebben a szakaszban egy helyi <abbr title="Logikai tároló egy vagy több különálló függvényhez, amelyek együtt helyezhetők üzembe és kezelhetők.">Azure Functions projekt</abbr> a Pythonban. A projekt minden függvénye egy adott válaszra válaszol <abbr title="A függvény kódját meghívó esemény típusa, például EGY HTTP-kérés, egy üzenetsor-üzenet vagy egy adott időpont.">Ravaszt</abbr>.

1. A parancs `func init` futtatásával hozzon létre egy függvényprojektet egy *LocalFunctionProj* nevű mappában a megadott futásidejűvel:  

    ```console
    func init LocalFunctionProj --python
    ```

1. Lépjen a projektmappába:

    ```console
    cd LocalFunctionProj
    ```
    
    <br/>
    <details>
    <summary><strong>Mi jött létre a LocalFunctionProj mappában?</strong></summary>
    
    Ez a mappa a projekt különböző fájljait [](functions-run-local.md#local-settings-file) tartalmazza, beleértve alocal.settings.jsnevű konfigurációs fájlokat, [valaminthost.jsfájlját.](functions-host-json.md) Mivel *local.settings.js* fájl tartalmazhatja az Azure-ból letöltött titkos adatokat, a fájl alapértelmezés szerint ki van zárva a verziókezelőből a *.gitignore fájlban.*
    </details>

1. Adjon hozzá egy függvényt a projekthez a következő paranccsal:

    ```console
    func new --name HttpExample --template "HTTP trigger" --authlevel "anonymous"
    ```   
    A `--name` argumentum a függvény (HttpExample) egyedi neve.

    A `--template` argumentum határozza meg a függvény eseményindítóját (HTTP).
    
    `func new`létrehoz egy almappát, amely megfelel a függvény nevének, amely egy *\_ \_ init \_ \_ .py* fájlt tartalmaz a függvény kódjának és egyfunction.js *nevű konfigurációs fájlnak.*

    <br/>    
    <details>
    <summary><strong>Az __init__.py kódja</strong></summary>
    
    *\_ \_ Az init \_ \_ .py* egy Python-függvényt tartalmaz, amely a fájlban található `main()` *function.jsalapján aktiválódik.*
    
    :::code language="python" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-Python/__init__.py":::
    
    HTTP-eseményindítók esetében a függvény a változóban fogadja a kérelemadatokat a(function.js) `req` *változóban.* `req`A az [azure.functions.HttpRequest osztály egy példánya.](/python/api/azure-functions/azure.functions.httprequest) A visszatérési objektum a onfunction.js`$return` *definiálva* az [azure.functions.HttpResponse osztály egy példánya.](/python/api/azure-functions/azure.functions.httpresponse) További információ: [HTTP Azure Functions triggerek és kötések .](./functions-bindings-http-webhook.md?tabs=python)
    </details>

    <br/>
    <details>
    <summary><strong>Kód a function.jsbe</strong></summary>

    *function.jsegy* konfigurációs fájl, amely meghatározza a <abbr title="Deklaratív kapcsolatok egy függvény és más erőforrások között. A bemeneti kötések adatokat szolgáltatnak a függvénynek; A kimeneti kötések adatokat szolgáltatnak a függvényből más erőforrásoknak.">bemeneti és kimeneti kötések</abbr> a függvényhez, beleértve az eseményindító típusát is.
    
    Ha szeretné, másik `scriptFile` Python-fájlt is meghívhat.
    
    :::code language="json" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-Python/function.json":::
    
    Minden kötéshez szükség van egy irányra, egy típusra és egy egyedi névre. A HTTP-eseményindító típusú bemeneti és kimeneti [`httpTrigger`](functions-bindings-http-webhook-trigger.md) kötéssel [`http`](functions-bindings-http-webhook-output.md) rendelkezik.    
    </details>

<br/>

---

## <a name="4-run-the-function-locally"></a>4. A függvény helyi futtatása

1. Futtassa a függvényt a helyi Azure Functions a *LocalFunctionProj* mappából:

    ```
    func start
    ```

    A kimenet vége felé a következő soroknak kell megjelennie: 
    
    <pre class="is-monospace is-size-small has-padding-medium has-background-tertiary has-text-tertiary-invert">
    ...
    
    Now listening on: http://0.0.0.0:7071
    Application started. Press Ctrl+C to shut down.
    
    Http Functions:
    
            HttpExample: [GET,POST] http://localhost:7071/api/HttpExample
    ...
    
    </pre>
    
    <br/>
    <details>
    <summary><strong>Nem látom a HttpExample-t a kimenetben</strong></summary>

    Ha a HttpExample nem jelenik meg, valószínűleg a projekt gyökérmappán kívülről indította el a gazdagépet. Ebben az esetben a <kbd>Ctrl+C billentyűkombinációval</kbd> állítsa le a gazdagépet, navigáljon a projekt gyökérmappához, és futtassa újra az előző parancsot.
    </details>

1. Másolja a **HttpExample** függvény URL-címét ebből a kimenetből egy böngészőbe, és fűzza hozzá a **?name=<YOUR_NAME>,** így a teljes URL-cím a következőhöz hasonló **http://localhost:7071/api/HttpExample?name=Functions** lesz: . A böngészőnek a Hello Functionshöz hasonló üzenetet **kell megjelenítenie:**

    ![A függvény eredménye helyileg fut a böngészőben](../../includes/media/functions-run-function-test-local-cli/function-test-local-browser.png)

1. A terminál, amelyen elindította a projektet, a naplókimenetet is megjeleníti a kérések igénylése során.

1. Ha végzett, a <kbd>Ctrl+C billentyűkombinációval</kbd> állítsa le a függvénygazdát <kbd>az y</kbd> billentyűkombinációval.

<br/>

---

## <a name="5-create-supporting-azure-resources-for-your-function"></a>5. Támogató Azure-erőforrások létrehozása a függvényhez

Mielőtt üzembe helyezheti a függvénykódot az Azure-ban, létre kell hoznia egy <abbr title="Logikai tároló a kapcsolódó Azure-erőforrásokhoz, amelyek egy egységként kezelhetők.">erőforráscsoport</abbr>A <abbr title="Egy fiók, amely az összes Azure Storage-adatobjektumot tartalmazza. A tárfiók egyedi névteret biztosít a tárolási adatok számára.">tárfiók</abbr>, és egy <abbr title="A kiszolgáló nélküli függvényeket az Azure-ban kezelő felhőerőforrás, amely biztosítja a mögöttes számítási környezetet, amelyben a függvények futnak.">függvényalkalmazás</abbr> a következő parancsokkal:

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

    A [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) parancsmag bejelentkezik az Azure-fiókjába.

    ---

1. Hozzon létre egy nevű `AzureFunctionsQuickstart-rg` erőforráscsoportot a `westeurope` régióban. 

    # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
    
    ```azurecli
    az group create --name AzureFunctionsQuickstart-rg --location westeurope
    ```
 
    Az [az group create parancs](/cli/azure/group#az_group_create) létrehoz egy erőforráscsoportot. Az erőforráscsoportot és az erőforrásokat általában egy <abbr title="Egy adott Azure-adatközpont földrajzi hivatkozása, amelyben az erőforrások le vannak osztva.">régió</abbr> a következő parancs által visszaadott elérhető régió `az account list-locations` használatával: .

    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

    ```azurepowershell
    New-AzResourceGroup -Name AzureFunctionsQuickstart-rg -Location westeurope
    ```

    A [New-AzResourceGroup parancs](/powershell/module/az.resources/new-azresourcegroup) létrehoz egy erőforráscsoportot. Erőforráscsoportját és erőforrásait általában egy közeli régióban hozza létre a [Get-AzLocation](/powershell/module/az.resources/get-azlocation) parancsmag által visszaadott elérhető régió használatával.

    ---

    Linux- és Windows-alkalmazások nem használhatók ugyanabban az erőforráscsoportban. Ha rendelkezik egy nevű meglévő erőforráscsoporttal egy Windows-függvényalkalmazással vagy `AzureFunctionsQuickstart-rg` -webalkalmazással, másik erőforráscsoportot kell használnia.

1. Hozzon létre egy általános célú Azure Storage-fiókot az erőforráscsoportban és a régióban:

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

    Cserélje `<STORAGE_NAME>` le a helyére az Önnek megfelelő nevet, és <abbr title="A névnek egyedinek kell lennie az összes Azure-ügyfél által globálisan használt összes tárfiókban. Használhatja például a személyes vagy a vállalata nevét, az alkalmazás nevét és egy numerikus azonosítót, mint a contosobizappstorage20.">egyedi az Azure Storage-ban</abbr>. A neveknek három–24 karakter hosszúságú számokat és csak kisbetűket tartalmazhatnak. `Standard_LRS`A egy általános célú fiókot ad meg, amelyet [a Functions támogat.](storage-considerations.md#storage-account-requirements)
    
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
    
    Cserélje le a helyére az előző lépésben `<STORAGE_NAME>` használt fiók nevét.

    Cserélje `<APP_NAME>` le a helyére a következőt: <abbr title="Olyan név, amely globálisan minden Azure-ügyfél esetében egyedinek kell lennie. Használhatja például a személyes vagy a szervezet nevének, az alkalmazás nevének és egy numerikus azonosítónak a kombinációját, ahogy az a contoso-bizapp-func-20 fájlban is szerepel.">az Ön számára megfelelő globálisan egyedi név</abbr>. Az `<APP_NAME>` egyben a függvényalkalmazás alapértelmezett DNS-tartományaként is szolgál, 
    
    <br/>
    <details>
    <summary><strong>Milyen költséggel jár az Azure-ban kiépített erőforrások költsége?</strong></summary>

    Ez a parancs létrehoz egy függvényalkalmazást, amely a megadott nyelvi futtatókörnyezetben fut a [Azure Functions használat](functions-scale.md#overview-of-plans)alapján, amely ingyenes az itt használt mennyiségért. A parancs egy társított Azure Application Insights-példányt is kiad ugyanabban az erőforráscsoportban, amellyel figyelheti a függvényalkalmazást és megtekintheti a naplókat. További információ: [Monitor Azure Functions.](functions-monitoring.md) A példány nem jár költségekkel, amíg ön nem aktiválja.
    </details>

<br/>

---

## <a name="6-deploy-the-function-project-to-azure"></a>6. A függvényprojekt üzembe helyezése az Azure-ban

Miután sikeresen létrehozta a függvényalkalmazást az Azure-ban,  készen áll a helyi függvényprojekt üzembe helyezésére a [func azure functionapp publish paranccsal.](functions-run-local.md#project-file-deployment)  

A következő példában cserélje le `<APP_NAME>` a helyére az alkalmazás nevét.

```console
func azure functionapp publish <APP_NAME>
```

A parancs az alábbi kimenethez hasonló eredményeket mutat (az egyszerűség kedvéért `publish` csonkoltuk):

<pre class="is-monospace is-size-small has-padding-medium has-background-tertiary has-text-tertiary-invert">
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
        Invoke url: https://msdocs-azurefunctions-qs.azurewebsites.net/api/httpexample
</pre>

<br/>

---

## <a name="7-invoke-the-function-on-azure"></a>7. A függvény meghívása az Azure-ban

Mivel a függvény HTTP-eseményindítót használ, egy HTTP-kérés böngészőben vagy egy hasonló eszközzel való meghívásával hívhatja meg az URL-címére <abbr title="Parancssori eszköz URL-címre vonatkozó HTTP-kérések létrehozásához; lásd: https://curl.se/">curl</abbr>. 

# <a name="browser"></a>[Böngésző](#tab/browser)

Másolja a parancs kimenetében látható teljes **meghívási URL-címet** egy böngésző címsorába, és fűzi hozzá `publish` a **name=Functions&paramétert.** A böngészőnek hasonló kimenetet kell megjelenítenie, mint amikor helyileg futtatta a függvényt.

![A függvény kimenete egy böngészőben fut az Azure-ban](../../includes/media/functions-run-remote-azure-cli/function-test-cloud-browser.png)

# <a name="curl"></a>[curl](#tab/curl)

Futtassa [`curl`](https://curl.haxx.se/) a **következőt: Invoke URL (URL-cím** meghívása) , és fűzi hozzá&**name=Functions paramétert.** A parancs kimenetének a "Hello Functions" szövegnek kell lennie.

![A függvény kimenete a curl használatával fut az Azure-ban](../../includes/media/functions-run-remote-azure-cli/function-test-cloud-curl.png)

---

### <a name="71-view-real-time-streaming-logs"></a>7.1 Valós idejű streamnaplók megtekintése

Futtassa a következő parancsot, [](functions-run-local.md#enable-streaming-logs) hogy megtekintsen közel valós idejű streamnaplókat Application Insights a Azure Portal:

```console
func azure functionapp logstream <APP_NAME> --browser
```

Cserélje `<APP_NAME>` le a helyére a függvényalkalmazás nevét.

Egy külön terminálablakban vagy a böngészőben hívja meg újra a távoli függvényt. A terminálon megjelenik a függvény Azure-ban történő végrehajtásának részletes naplója. 

<br/>

---

## <a name="8-clean-up-resources"></a>8. Erőforrások tisztítása

Ha folytatja a következő [lépéssel,](#next-steps) és hozzáad egy <abbr title="A azt jelenti, hogy egy függvényt társít egy tárolási üzenetsorhoz, hogy üzeneteket hoz létre az üzenetsorban. ">Azure Storage-üzenetsor kimeneti kötése</abbr>, tartsa a helyén az összes erőforrást, mivel a már végzett munka alapján fog felépíteni.

Ellenkező esetben a következő paranccsal törölheti az erőforráscsoportot és annak összes tartalmazott erőforrását, hogy elkerülje a további költségeket.

 # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az group delete --name AzureFunctionsQuickstart-rg
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell
Remove-AzResourceGroup -Name AzureFunctionsQuickstart-rg
```

<br/>

---

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Csatlakozás Azure Storage-üzenetsorhoz](functions-add-output-binding-storage-queue-cli.md?pivots=programming-language-python)

[Problémákat? Tudajuk meg.](https://aka.ms/python-functions-qs-survey)
