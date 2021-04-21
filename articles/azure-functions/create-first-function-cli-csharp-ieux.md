---
title: C#-függvény létrehozása a parancssorból – Azure Functions
description: Megtudhatja, hogyan hozhat létre C#-függvényt a parancssorból, majd hogyan tehet közzé helyi projektet kiszolgáló nélküli üzemeltetésre a Azure Functions.
ms.date: 10/03/2020
ms.topic: quickstart
ms.custom:
- devx-track-csharp
- devx-track-azurecli
- devx-track-azurepowershell
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: fb287333ba8b0b4fc0bb35ce91d2e3113ff50e2b
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2021
ms.locfileid: "107831113"
---
# <a name="quickstart-create-a-c-function-in-azure-from-the-command-line"></a>Rövid útmutató: C#-függvény létrehozása az Azure-ban a parancssorból

> [!div class="op_single_selector" title1="Válassza ki a függvény nyelvét: "]
> - [C#](create-first-function-cli-csharp-ieux.md)
> - [Java](create-first-function-cli-java.md)
> - [JavaScript](create-first-function-cli-node.md)
> - [PowerShell](create-first-function-cli-powershell.md)
> - [Python](create-first-function-cli-python.md)
> - [TypeScript](create-first-function-cli-typescript.md)

Ebben a cikkben parancssori eszközökkel hoz létre egy C#-osztálytáralapú függvényt, amely HTTP-kérésekre válaszol. A kód helyi tesztelése után üzembe helyezheti a <abbr title="Egy futásidejű számítási környezet, amelyben a kiszolgáló összes adata átlátható az alkalmazásfejlesztők számára, ami leegyszerűsíti a kód üzembe helyezésének és kezelésének folyamatát.">kiszolgáló nélküli</abbr> környezete <abbr title="Az Azure szolgáltatása, amely alacsony költségű, kiszolgáló nélküli számítási környezetet biztosít az alkalmazások számára.">Azure Functions</abbr>.

Ennek a rövid útmutatónak az elvégzése néhány dollár vagy annál kisebb költséggel jár az Azure-fiókjában.

A cikk [kódalapú Visual Studio is](create-first-function-vs-code-csharp.md) elérhető.

## <a name="1-prepare-your-environment"></a>1. A környezet előkészítése

+ Azure-fiók lekért <abbr title="Az Azure-használat számlázási adatait fenntartó profil.">account</abbr> aktív <abbr title="Az azure-beli erőforrások kezeléséhez használt alapvető szervezeti struktúra, amely általában egy szervezeten belüli egyéni vagy részleghez van társítva.">előfizetést</abbr>. [Hozzon létre egy ingyenes fiókot.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)

+ A [.NET Core SDK 3.1 telepítése](https://www.microsoft.com/net/download)

+ Telepítse [Azure Functions Core Tools](functions-run-local.md#v2) 3.x verziót.

+ Vagy a <abbr title="Platformfüggetlen parancssori eszközök a helyi fejlesztői számítógépről származó Azure-erőforrások kezeléséhez, a virtuális gép használatának Azure Portal.">Azure CLI</abbr> vagy <abbr title="Egy PowerShell-modul, amely parancsokat biztosít az Azure-erőforrások helyi fejlesztői számítógépről való felhasználásával kapcsolatban, a virtuális gép használatának Azure Portal.">Azure PowerShell</abbr> Azure-erőforrások létrehozásához:

    + [Az Azure CLI](/cli/azure/install-azure-cli) 2.4-es vagy újabb verziója.

    + [Azure PowerShell](/powershell/azure/install-az-ps) 5.0-s vagy újabb verziót.

---

### <a name="2-verify-prerequisites"></a>2. Előfeltételek ellenőrzése

Ellenőrizze az előfeltételeket, amelyek attól függnek, hogy az Azure CLI-t vagy Azure PowerShell azure-erőforrások létrehozására használja:

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

+ Egy terminálban vagy parancsablakban futtassa a `func --version` parancsot annak ellenőrzéshez, hogy a <abbr title="Parancssori eszközök a helyi Azure Functions való eszközökhöz.">Azure Functions Core Tools</abbr> A a 3.x verziójú.

+ **Futtatás** `az --version` az Azure CLI 2.4-es vagy újabb verziójának ellenőrzéshez.

+ **Futtatás** `az login` az Azure-ba való bejelentkezéshez és egy aktív előfizetés ellenőrzéséhez.

+ **Futtatás** `dotnet --list-sdks` annak ellenőrzése, hogy .NET Core SDK 3.1.x verzió van-e telepítve

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

+**Futtatás** `func --version` annak ellenőrzéshez, hogy Azure Functions Core Tools 3.x verziójú-e.

+ **Futtatás** `(Get-Module -ListAvailable Az).Version` és ellenőrizze az 5.0-s vagy újabb verziót. 

+ **Futtatás** `Connect-AzAccount` az Azure-ba való bejelentkezéshez és egy aktív előfizetés ellenőrzéséhez.

+ **Futtatás** `dotnet --list-sdks` annak ellenőrzése, hogy .NET Core SDK 3.1.x verzió van-e telepítve

---

## <a name="3-create-a-local-function-project"></a>3. Helyi függvényprojekt létrehozása

Ebben a szakaszban egy helyi <abbr title="Logikai tároló egy vagy több különálló függvényhez, amelyek együtt helyezhetők üzembe és kezelhetők.">Azure Functions projekt</abbr> A C#-ban. A projekt minden függvénye egy adott függvényre válaszol <abbr title="Egy esemény, amely meghívja a függvény kódját, például egy HTTP-kérést, egy üzenetsor-üzenetet vagy egy adott időt.">Ravaszt</abbr>.

1. A parancs `func init` futtatásával hozzon létre egy függvényprojektet egy *LocalFunctionProj* nevű mappában a megadott futásidejűvel:  

    ```csharp
    func init LocalFunctionProj --dotnet
    ```

1. **Futtatás** "cd LocalFunctionProj" a <abbr title="Ez a mappa a projekt különböző fájljait tartalmazza, beleértve a local.settings.jsnevű konfigurációs fájlokat és host.jsbe. Mivel local.settings.jsa fájl tartalmazhat az Azure-ból letöltött titkos adatokat, a fájl alapértelmezés szerint ki van zárva a verziókezelőből a .gitignore fájlban.">Projektmappa</abbr>.

    ```console
    cd LocalFunctionProj
    ```
    <br/>

1. Adjon hozzá egy függvényt a projekthez a következő paranccsal:
    
    ```console
    func new --name HttpExample --template "HTTP trigger" --authlevel "anonymous"
    ``` 
    A `--name` argumentum a függvény (HttpExample) egyedi neve.

    A `--template` argumentum határozza meg a függvény eseményindítóját (HTTP).

    
    <br/>   
    <details>  
    <summary><strong>Nem kötelező: A HttpExample.cs kódja</strong></summary>  
    
    *A HttpExample.cs* olyan metódust tartalmaz, amely a változóban fogadja a kérelemadatokat egy `Run` `req` [HttpRequest,](/dotnet/api/microsoft.aspnetcore.http.httprequest) amelyet a **HttpTriggerAttribute** attribútummal egészített ki, és amely meghatározza az eseményindító viselkedését.

    :::code language="csharp" source="~/functions-docs-csharp/http-trigger-template/HttpExample.cs":::
        
    A visszaadott objektum egy [ActionResult,](/dotnet/api/microsoft.aspnetcore.mvc.actionresult) amely egy válaszüzenetet ad vissza [OkObjectResult](/dotnet/api/microsoft.aspnetcore.mvc.okobjectresult) (200) vagy [BadRequestObjectResult](/dotnet/api/microsoft.aspnetcore.mvc.badrequestobjectresult) (400) értékként. További információ: [HTTP Azure Functions triggerek és kötések .](./functions-bindings-http-webhook.md?tabs=csharp)  
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


1. A  <kbd>Ctrl+C billentyűkombinációval</kbd> állítsa le a függvénygazdát <kbd>az y</kbd> billentyűkombinációval.

<br/>

---
    
## <a name="5-create-supporting-azure-resources-for-your-function"></a>5. Támogató Azure-erőforrások létrehozása a függvényhez

Mielőtt üzembe helyezheti a függvénykódot az Azure-ban, létre kell hoznia egy <abbr title="Logikai tároló a kapcsolódó Azure-erőforrásokhoz, amelyek egy egységként kezelhetők.">erőforráscsoport</abbr>A <abbr title="Egy fiók, amely az összes Azure Storage-adatobjektumot tartalmazza. A tárfiók egyedi névteret biztosít a tárolási adatok számára.">tárfiók</abbr>, és egy <abbr title="A kiszolgáló nélküli függvényeket az Azure-ban kezelő felhőerőforrás, amely biztosítja a mögöttes számítási környezetet, amelyben a függvények futnak.">függvényalkalmazás</abbr> a következő parancsokkal:

1. Ha még nem tette meg, jelentkezzen be az Azure-ba:

    # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
    ```azurecli
    az login
    ```


    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell) 
    ```azurepowershell
    Connect-AzAccount
    ```


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


    ---

    Linux- és Windows-alkalmazások nem használhatók ugyanabban az erőforráscsoportban. Ha rendelkezik egy nevű meglévő erőforráscsoporttal egy Windows-függvényalkalmazással vagy `AzureFunctionsQuickstart-rg` -webalkalmazással, másik erőforráscsoportot kell használnia.
    
1. Hozzon létre egy általános célú Azure Storage-fiókot az erőforráscsoportban és a régióban:

    # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

    ```azurecli
    az storage account create --name <STORAGE_NAME> --location westeurope --resource-group AzureFunctionsQuickstart-rg --sku Standard_LRS
    ```


    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

    ```azurepowershell
    New-AzStorageAccount -ResourceGroupName AzureFunctionsQuickstart-rg -Name <STORAGE_NAME> -SkuName Standard_LRS -Location westeurope
    ```


    ---

    Cserélje `<STORAGE_NAME>` le a helyére az Önnek megfelelő nevet, és <abbr title="A névnek egyedinek kell lennie az összes Azure-ügyfél által globálisan használt összes tárfiókban. Használhatja például a személyes vagy a vállalata nevét, az alkalmazás nevét és egy numerikus azonosítót a contosobizappstorage20 fájlban megadottak szerint.">egyedi az Azure Storage-ban</abbr>. A neveknek három–24 karakter hosszúságú számokat és csak kisbetűket tartalmazhatnak. `Standard_LRS`A egy általános célú fiókot ad meg, amelyet [a Functions támogat.](storage-considerations.md#storage-account-requirements)


1. Hozza létre a függvényalkalmazást az Azure-ban.
**Csere** "<STORAGE_NAME>** az előző lépésben.
**Csere** "<APP_NAME>" globálisan egyedi névvel.

    # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
        
    ```azurecli
    az functionapp create --resource-group AzureFunctionsQuickstart-rg --consumption-plan-location westeurope --runtime dotnet --functions-version 3 --name <APP_NAME> --storage-account <STORAGE_NAME>
    ```
    
    
    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)
    
    ```azurepowershell
    New-AzFunctionApp -Name <APP_NAME> -ResourceGroupName AzureFunctionsQuickstart-rg -StorageAccount <STORAGE_NAME> -Runtime dotnet -FunctionsVersion 3 -Location 'West Europe'
    ```
    
    
    ---
    
    Cserélje le a helyére az előző `<STORAGE_NAME>` lépésben használt fiók nevét.

    Cserélje `<APP_NAME>` le a helyére a következőt: <abbr title="Olyan név, amely globálisan minden Azure-ügyfél esetében egyedinek kell lennie. Használhatja például a személyes vagy a szervezet nevének, az alkalmazás nevének és egy numerikus azonosítónak a kombinációját, ahogy a contoso-bizapp-func-20 is.">egyedi név</abbr>. Az `<APP_NAME>` egyben a függvényalkalmazás alapértelmezett DNS-tartományaként is szolgál, 

    <br/>
    <details>
    <summary><strong>Milyen költséggel jár az Azure-ban kiépített erőforrások költsége?</strong></summary>

    Ez a parancs létrehoz egy függvényalkalmazást, amely a megadott nyelvi futtatókörnyezetben fut a [Azure Functions Használat](consumption-plan.md)alapján csomag keretében, amely ingyenes az itt használt mennyiségért. A parancs egy társított Azure Application Insights-példányt is kiad ugyanabban az erőforráscsoportban, amellyel figyelheti a függvényalkalmazást és megtekintheti a naplókat. További információ: [Monitor Azure Functions.](functions-monitoring.md) A példány nem jár költségekkel, amíg ön nem aktiválja.
    </details>

<br/>

---

## <a name="6-deploy-the-function-project-to-azure"></a>6. A függvényprojekt üzembe helyezése az Azure-ban


**Másolás** ' func azure funtionapp publish <APP_NAME> a terminálban **Replace** (Csere) helyére `<APP_NAME>` az alkalmazás nevére.
**Futtatás**

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

Másolja a parancs kimenetében megjelenő teljes **Meghívási URL-címet** `publish` egy böngésző címsorába. **Fűzheti** hozzá a **lekérdezési paramétert&name=Functions paraméterhez.** 

![A függvény kimenete egy böngészőben fut az Azure-ban](../../includes/media/functions-run-remote-azure-cli/function-test-cloud-browser.png)

<br/>

---

## <a name="8-clean-up-resources"></a>8. Erőforrások megtisztítása

Ha folytatja a következő [lépéssel,](#next-steps) és hozzáad egy Azure Storage-üzenetsor kimenetét <abbr title="Deklaratív kapcsolat egy függvény és más erőforrások között. A bemeneti kötés adatokat biztosít a függvénynek; A kimeneti kötések adatokat szolgáltatnak a függvényből más erőforrásoknak.">Kötelező</abbr>, tartsa a helyén az összes erőforrást, mivel a már végzett munka alapján fog felépíteni.

Ellenkező esetben a következő paranccsal törölheti az erőforráscsoportot és annak összes tartalmazott erőforrását, hogy elkerülje a további költségeket.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az group delete --name AzureFunctionsQuickstart-rg
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell
Remove-AzResourceGroup -Name AzureFunctionsQuickstart-rg
```

---

<br/>

---

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Csatlakozás Azure Storage-üzenetsorhoz](functions-add-output-binding-storage-queue-cli.md?pivots=programming-language-csharp)
