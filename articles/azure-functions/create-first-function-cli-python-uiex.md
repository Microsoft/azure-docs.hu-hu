---
title: Python-függvény létrehozása a parancssorból Azure Functions
description: Megtudhatja, hogyan hozhat létre Python-függvényt a parancssorból, és hogyan teheti közzé a helyi projektet a Azure Functions kiszolgáló nélküli üzemeltetéséhez.
ms.date: 11/03/2020
ms.topic: quickstart
ms.custom:
- devx-track-python
- devx-track-azurecli
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: da7f6fdaedd8105363cc62bf55bae2cb5f72f234
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102031650"
---
# <a name="quickstart-create-a-python-function-in-azure-from-the-command-line"></a>Gyors útmutató: Python-függvény létrehozása az Azure-ban a parancssorból

> [!div class="op_single_selector" title1="Válassza ki a függvény nyelvét: "]
> - [Python](create-first-function-cli-python.md)
> - [C#](create-first-function-cli-csharp.md)
> - [Java](create-first-function-cli-java.md)
> - [JavaScript](create-first-function-cli-node.md)
> - [PowerShell](create-first-function-cli-powershell.md)
> - [TypeScript](create-first-function-cli-typescript.md)

Ebben a cikkben parancssori eszközöket használ egy olyan Python-függvény létrehozásához, amely válaszol a HTTP-kérelmekre. A kód helyi tesztelését követően telepítse azt a <abbr title="Egy futásidejű számítástechnikai környezet, amelyben a kiszolgáló összes adata átlátható az alkalmazások fejlesztői számára, ami leegyszerűsíti a kód üzembe helyezésének és kezelésének folyamatát.">kiszolgáló nélküli</abbr> környezet <abbr title="Egy Azure-szolgáltatás, amely alacsony költséghatékonyságú, kiszolgáló nélküli számítási környezetet biztosít az alkalmazásokhoz.">Azure Functions</abbr>.

A rövid útmutató elvégzésével az Azure-fiókjában néhány USD értékű vagy annál kisebb költséggel jár.

A cikk [Visual Studio Code-alapú verziója](create-first-function-vs-code-python.md) is létezik.

## <a name="1-configure-your-environment"></a>1. a környezet konfigurálása

Mielőtt elkezdené, a következőkkel kell rendelkeznie:

+ Egy Azure <abbr title="Az Azure-használati számlázási adatokat fenntartó profil.">account</abbr> aktív <abbr title="Az alapszintű szervezeti struktúra, amelyben az Azure-ban kezelheti az erőforrásokat, jellemzően egy adott szervezeten belüli személyhez vagy részleghez társítva.">előfizetést</abbr>. [Hozzon létre egy fiókot ingyenesen](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

+ A [Azure functions Core Tools](functions-run-local.md#v2) 3. x verzió. 
  
+ Vagy a <abbr title="Többplatformos parancssori eszközök az Azure-erőforrások helyi fejlesztési számítógépről való használatához, a Azure Portal használatának alternatívájaként.">Azure CLI</abbr> vagy <abbr title="Egy PowerShell-modul, amely parancsokat biztosít az Azure-erőforrások helyi fejlesztési számítógépről való használatához, a Azure Portal használatának alternatívájaként.">Azure PowerShell</abbr> Azure-erőforrások létrehozásához:

    + Az [Azure CLI](/cli/azure/install-azure-cli) 2,4-es vagy újabb verziója.

    + [Azure PowerShell](/powershell/azure/install-az-ps) 5,0-es vagy újabb verzió.

+ [Python 3,8 (64 bites)](https://www.python.org/downloads/release/python-382/), [python 3,7 (64-bit)](https://www.python.org/downloads/release/python-375/), [Python 3,6 (64-bit)](https://www.python.org/downloads/release/python-368/), amelyek mindegyike támogatott a 3. x verzióban Azure functions.

### <a name="11-prerequisite-check"></a>1,1 Előfeltételek ellenőrzése

Ellenőrizze az előfeltételeket, amelyek attól függnek, hogy az Azure CLI-t vagy Azure PowerShell Azure-erőforrások létrehozásához használja-e:

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

+ A terminál vagy a parancssorablakban futtassa a parancsot, `func --version` és győződjön meg arról, hogy a <abbr title="Parancssori eszközök készlete, amelyekkel a helyi számítógép Azure Functions dolgozhat.">Azure Functions Core Tools</abbr> 3. x verziójú.

+ Futtassa az parancsot az `az --version` Azure CLI 2,4-es vagy újabb verziójának megadásához.

+ A futtatásával `az login` Jelentkezzen be az Azure-ba, és ellenőrizze az aktív előfizetést.

+ Futtassa `python --version` a (Linux/MacOS) vagy a `py --version` (Windows) rendszert a Python-verziók 3,8. x, 3.7. x vagy 3.6. x verziójának vizsgálatához.

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

+ A terminál vagy a parancssorablakban futtassa a parancsot, `func --version` és győződjön meg arról, hogy a <abbr title="Parancssori eszközök készlete, amelyekkel a helyi számítógép Azure Functions dolgozhat.">Azure Functions Core Tools</abbr> 3. x verziójú.

+ Futtassa `(Get-Module -ListAvailable Az).Version` és ellenőrizze a 5,0-es vagy újabb verziót. 

+ A futtatásával `Connect-AzAccount` Jelentkezzen be az Azure-ba, és ellenőrizze az aktív előfizetést.

+ Futtassa `python --version` a (Linux/MacOS) vagy a `py --version` (Windows) rendszert a Python-verziók 3,8. x, 3.7. x vagy 3.6. x verziójának vizsgálatához.

---

<br/>

---

## <a name="2-create-and-activate-a-virtual-environment"></a>2. <a name="create-venv"></a> virtuális környezet létrehozása és aktiválása

Egy megfelelő mappában futtassa a következő parancsokat a nevű virtuális környezet létrehozásához és aktiválásához `.venv` . Ügyeljen arra, hogy a Azure Functions által támogatott Python 3,8, 3,7 vagy 3,6-ot használja.

# <a name="bash"></a>[bash](#tab/bash)

```bash
python -m venv .venv
```

```bash
source .venv/bin/activate
```

Ha a Python nem telepítette a venv csomagot a Linux-disztribúcióban, futtassa a következő parancsot:

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

Az összes további parancsot futtatja ebben az aktivált virtuális környezetben. 

<br/>

---

## <a name="3-create-a-local-function-project"></a>3. helyi függvény létrehozása projekt

Ebben a szakaszban létrehoz egy helyi <abbr title="Egy vagy több, együtt telepíthető és felügyelhető különálló függvény logikai tárolója.">Azure Functions projekt</abbr> Pythonban. A projekt minden funkciója válaszol egy adott <abbr title="A függvény kódját meghívó esemény típusa, például egy HTTP-kérelem, egy üzenetsor-üzenet vagy egy adott időpont.">eseményindító</abbr>.

1. Futtassa a `func init` parancsot egy functions-projekt létrehozásához egy *LocalFunctionProj* nevű mappában a megadott futtatókörnyezettel:  

    ```console
    func init LocalFunctionProj --python
    ```

1. Navigáljon a projekt mappájába:

    ```console
    cd LocalFunctionProj
    ```
    
    <br/>
    <details>
    <summary><strong>Mi jön létre a LocalFunctionProj mappában?</strong></summary>
    
    Ez a mappa a projekthez különböző fájlokat tartalmaz, beleértve a [local.settings.json](functions-run-local.md#local-settings-file) és a [host.js](functions-host-json.md)nevű konfigurációs fájlokat is. Mivel a *local.settings.json* az Azure-ból letöltött titkos kódok is lehetnek, a fájl a *. gitignore* fájlban alapértelmezés szerint ki van zárva a forrás-vezérlőelemből.
    </details>

1. Adjon hozzá egy függvényt a projekthez a következő parancs használatával:

    ```console
    func new --name HttpExample --template "HTTP trigger" --authlevel "anonymous"
    ```   
    Az `--name` argumentum a függvény egyedi neve (HttpExample).

    Az `--template` argumentum megadja a függvény triggerét (http).
    
    `func new`létrehoz egy almappát, amely megfelel a függvénynek, amely tartalmazza a függvény kódját és egy *function.js* nevű konfigurációs *\_ \_ \_ \_ fájlt.*

    <br/>    
    <details>
    <summary><strong>Az __init__. a-kód kódja</strong></summary>
    
    az *\_ \_ init \_ \_ .* a (z `main()` ) egy Python-függvényt tartalmaz, amely a *function.js* konfigurációjának megfelelően aktiválódik.
    
    :::code language="python" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-Python/__init__.py":::
    
    HTTP-trigger esetén a függvény a `req` *function.json* megadottak szerint fogadja a kérelmeket a változóban. `req` az [Azure. functions. HttpRequest osztály](/python/api/azure-functions/azure.functions.httprequest)egy példánya. Afunction.json értékben definiált visszatérési objektum az `$return` [Azure. functions. HttpResponse osztály](/python/api/azure-functions/azure.functions.httpresponse)egy példánya.  További információ: [Azure FUNCTIONS http-eseményindítók és-kötések](./functions-bindings-http-webhook.md?tabs=python).
    </details>

    <br/>
    <details>
    <summary><strong>function.jskódja</strong></summary>

    A *function.json* egy konfigurációs fájl, amely meghatározza a <abbr title="A függvények és más erőforrások közötti deklaratív kapcsolatok. A bemeneti kötés adatokat biztosít a függvénynek; a kimeneti kötések adatokat biztosítanak a függvénytől más erőforrásoknak.">bemeneti és kimeneti kötések</abbr> a függvényhez, beleértve az trigger típusát is.
    
    Ha kívánja `scriptFile` , másik Python-fájlt is meghívhat.
    
    :::code language="json" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-Python/function.json":::
    
    Minden kötéshez meg kell adni egy irányt, egy típust és egy egyedi nevet. A HTTP-trigger típusa [`httpTrigger`](functions-bindings-http-webhook-trigger.md) és kimeneti kötése típusú bemeneti kötést tartalmaz [`http`](functions-bindings-http-webhook-output.md) .    
    </details>

<br/>

---

## <a name="4-run-the-function-locally"></a>4. a függvény helyi futtatása

1. A függvény futtatásához indítsa el a helyi Azure Functions Runtime-gazdagépet a *LocalFunctionProj* mappából:

    ```
    func start
    ```

    A kimenet vége felé a következő soroknak kell megjelenniük: 
    
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
    <summary><strong>Nem látom a HttpExample a kimenetben</strong></summary>

    Ha a HttpExample nem jelenik meg, valószínűleg elindította a gazdagépet a projekt gyökérkönyvtárán kívülről. Ebben az esetben a <kbd>CTRL + C</kbd> billentyűkombinációval állítsa le a gazdagépet, navigáljon a projekt gyökérmappa mappájához, és futtassa újra az előző parancsot.
    </details>

1. Másolja a **HttpExample** függvény URL-címét ebből a kimenetből egy böngészőbe, és fűzze hozzá a lekérdezési karakterláncot **? név =<YOUR_NAME>**, így a teljes URL-cím, például: **http://localhost:7071/api/HttpExample?name=Functions** . A böngészőben a következő üzenet jelenik meg, mint például a **Hello functions**:

    ![A függvény helyi futtatásának eredménye a böngészőben](../../includes/media/functions-run-function-test-local-cli/function-test-local-browser.png)

1. A terminál, amelyben elindította a projektet, a naplók kimenetét is megjeleníti a kérések elkészítésekor.

1. Ha elkészült, használja a <kbd>CTRL + C billentyűkombinációt</kbd> , és a functions gazdagép leállításához válassza az <kbd>y</kbd> lehetőséget.

<br/>

---

## <a name="5-create-supporting-azure-resources-for-your-function"></a>5. a függvényhez kapcsolódó Azure-erőforrások létrehozása

Mielőtt üzembe helyezi a függvény kódját az Azure-ban, létre kell hoznia egy <abbr title="Logikai tároló a kapcsolódó Azure-erőforrásokhoz, amelyeket egységként lehet kezelni.">erőforráscsoport</abbr>, a <abbr title="Egy olyan fiók, amely az összes Azure Storage-adatobjektumot tartalmazza. A Storage-fiók egyedi névteret biztosít a tárolási adataihoz.">tárfiók</abbr>és egy <abbr title="Az Azure-ban kiszolgáló nélküli funkciókat futtató felhőalapú erőforrás, amely a függvények futtatását szolgáló számítási környezetet biztosítja.">function alkalmazás</abbr> a következő parancsok használatával:

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
 
    Az az [Group Create](/cli/azure/group#az-group-create) parancs létrehoz egy erőforráscsoportot. Általában az erőforráscsoport és az erőforrások létrehozása <abbr title="Földrajzi hivatkozás egy adott Azure-adatközpontra, amelyben az erőforrások le vannak foglalva.">régió</abbr> a közeljövőben a parancsból visszaadott elérhető régiót használhat `az account list-locations` .

    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

    ```azurepowershell
    New-AzResourceGroup -Name AzureFunctionsQuickstart-rg -Location westeurope
    ```

    A [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) parancs létrehoz egy erőforráscsoportot. A [Get-AzLocation](/powershell/module/az.resources/get-azlocation) parancsmag által visszaadott elérhető régió használatával általában létrehozhatja az erőforráscsoportot és az erőforrásokat egy közeli régióban.

    ---

    Nem futtathat Linux-és Windows-alkalmazásokat ugyanabban az erőforráscsoporthoz. Ha már van egy nevű erőforráscsoport `AzureFunctionsQuickstart-rg` egy Windows-függvény alkalmazással vagy webalkalmazással, egy másik erőforráscsoportot kell használnia.

1. Hozzon létre egy általános célú Azure Storage-fiókot az erőforráscsoport és a régió területén:

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

    A helyére írja be `<STORAGE_NAME>` a megfelelő nevet, és <abbr title="A névnek egyedinek kell lennie az Azure-ügyfelek által világszerte használt összes Storage-fiókban. Használhatja például a személyes vagy a vállalat neve, az alkalmazás neve és a numerikus azonosító kombinációját is a contosobizappstorage20.">egyedi az Azure Storage-ban</abbr>. A névnek három – 24 karakterből kell állnia, és csak kisbetűket tartalmazhat. `Standard_LRS` a [függvények által támogatott](storage-considerations.md#storage-account-requirements)általános célú fiókot határozza meg.
    
    A Storage-fiók ebben a rövid útmutatóban csak néhány cent (USD) értékkel rendelkezik.

1. A Function alkalmazás létrehozása az Azure-ban:

    # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
        
    ```azurecli
    az functionapp create --resource-group AzureFunctionsQuickstart-rg --consumption-plan-location westeurope --runtime python --runtime-version 3.8 --functions-version 3 --name <APP_NAME> --storage-account <STORAGE_NAME> --os-type linux
    ```
    
    Az az [functionapp Create](/cli/azure/functionapp#az_functionapp_create) parancs létrehozza a Function alkalmazást az Azure-ban. Ha Python 3,7 vagy 3,6-et használ, váltson a következőre: `--runtime-version` `3.7` vagy `3.6` , illetve.
    
    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)
    
    ```azurepowershell
    New-AzFunctionApp -Name <APP_NAME> -ResourceGroupName AzureFunctionsQuickstart-rg -StorageAccount <STORAGE_NAME> -FunctionsVersion 3 -RuntimeVersion 3.8 -Runtime python -Location 'West Europe'
    ```
    
    A [New-AzFunctionApp](/powershell/module/az.functions/new-azfunctionapp) parancsmag létrehozza a Function alkalmazást az Azure-ban. Ha Python 3,7 vagy 3,6-et használ, váltson a következőre: `-RuntimeVersion` `3.7` vagy `3.6` , illetve.

    ---
    
    A helyére írja `<STORAGE_NAME>` be az előző lépésben használt fiók nevét.

    Cserélje `<APP_NAME>` le a <abbr title="Az összes Azure-ügyfél globálisan egyedi névnek kell lennie. Használhatja például a személyes vagy szervezet neve, az alkalmazás neve és a numerikus azonosító kombinációját, mint a contoso-bizapp-függvény-20.">globálisan egyedi név, amely megfelel az Ön számára</abbr>. Az `<APP_NAME>` egyben a függvényalkalmazás alapértelmezett DNS-tartományaként is szolgál, 
    
    <br/>
    <details>
    <summary><strong>Milyen költségeket kell kiépíteni az Azure-beli erőforrásokért?</strong></summary>

    Ez a parancs létrehoz egy, a megadott nyelvi futtatókörnyezetben futó Function alkalmazást a [Azure functions használati terv](functions-scale.md#overview-of-plans)alatt, amely ingyenesen használható az itt felmerülő felhasználási mennyiséghez. A parancs egy kapcsolódó Azure Application Insights-példányt is kiépít ugyanabban az erőforráscsoporthoz, amellyel nyomon követheti a Function alkalmazást, és megtekintheti a naplókat. További információ: [Azure functions figyelése](functions-monitoring.md). A példány nem jár költséggel, amíg be nem aktiválja.
    </details>

<br/>

---

## <a name="6-deploy-the-function-project-to-azure"></a>6. a Function projekt üzembe helyezése az Azure-ban

Miután sikeresen létrehozta a Function alkalmazást az Azure-ban, most már készen áll a **helyi functions-projekt üzembe helyezésére** az functions [Azure functionapp publish](functions-run-local.md#project-file-deployment) parancs használatával.  

A következő példában cserélje le az `<APP_NAME>` alkalmazást az alkalmazás nevére.

```console
func azure functionapp publish <APP_NAME>
```

A `publish` parancs az alábbi kimenethez hasonló eredményeket jelenít meg (az egyszerűség kedvéért csonkítva):

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

## <a name="7-invoke-the-function-on-azure"></a>7. a függvény meghívása az Azure-ban

Mivel a függvény HTTP-eseményindítót használ, meghívja azt úgy, hogy HTTP-kérést küld az URL-címére a böngészőben, vagy egy hasonló eszközzel <abbr title="Egy parancssori eszköz, amely HTTP-kéréseket hoz létre egy URL-címre; Lásd https://curl.se/">curl</abbr>. 

# <a name="browser"></a>[Böngésző](#tab/browser)

Másolja a parancs kimenetében látható teljes **meghívó URL-** címet `publish` egy böngésző címsorába, és illessze be a lekérdezési paramétert **&Name = functions**. A böngészőnek hasonló kimenetet kell megjelenítenie, mint amikor a funkciót helyileg futtatta.

![A függvény kimenete az Azure-ban egy böngészőben fut](../../includes/media/functions-run-remote-azure-cli/function-test-cloud-browser.png)

# <a name="curl"></a>[curl](#tab/curl)

Futtassa a parancsot [`curl`](https://curl.haxx.se/) a **meghívás URL-címével**, és illessze be a (z) **&Name = functions** paramétert. A parancs kimenetének a "Hello functions" szövegnek kell lennie.

![A függvény kimenete az Azure-on a curl használatával fut](../../includes/media/functions-run-remote-azure-cli/function-test-cloud-curl.png)

---

### <a name="71-view-real-time-streaming-logs"></a>7,1 valós idejű adatfolyam-naplók megtekintése

A következő parancs futtatásával tekintheti meg a közel valós idejű [adatfolyam-naplókat](functions-run-local.md#enable-streaming-logs) a Azure Portal Application Insightsban:

```console
func azure functionapp logstream <APP_NAME> --browser
```

Cserélje le a helyére `<APP_NAME>` a Function alkalmazás nevét.

Egy külön terminál-ablakban vagy a böngészőben hívja meg újra a távoli függvényt. Az Azure-ban a funkció végrehajtásának részletes naplója látható a terminálon. 

<br/>

---

## <a name="8-clean-up-resources"></a>8. erőforrások törlése

Ha folytatja a [következő lépéssel](#next-steps) , és hozzáad egy <abbr title="Azt jelenti, hogy a függvényt egy tárolási várólistához kell rendelni, hogy üzeneteket lehessen létrehozni a várólistán. ">Azure Storage-üzenetsor kimeneti kötése</abbr>megtarthatja az összes erőforrását, ahogy a már elvégzett szolgáltatásra épít.

Ellenkező esetben a következő paranccsal törölheti az erőforráscsoportot és az összes benne lévő erőforrást, hogy elkerülje a további költségek felmerülését.

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
> [Kapcsolódás Azure Storage-várólistához](functions-add-output-binding-storage-queue-cli.md?pivots=programming-language-python)

[Problémák léptek fel? Tudassa velünk.](https://aka.ms/python-functions-qs-survey)
