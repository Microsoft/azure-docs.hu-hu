---
title: C#-függvény létrehozása a parancssorból – Azure Functions
description: Megtudhatja, hogyan hozhat létre C#-függvényt a parancssorból, majd hogyan teheti közzé a helyi projektet a Azure Functions kiszolgáló nélküli üzemeltetéséhez.
ms.date: 10/03/2020
ms.topic: quickstart
ms.custom:
- devx-track-csharp
- devx-track-azurecli
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: a78abea5bcc5925cb2e137d918c7217ae92b118e
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/04/2021
ms.locfileid: "102044323"
---
# <a name="quickstart-create-a-c-function-in-azure-from-the-command-line"></a>Rövid útmutató: C#-függvény létrehozása az Azure-ban a parancssorból

> [!div class="op_single_selector" title1="Válassza ki a függvény nyelvét: "]
> - [C#](create-first-function-cli-csharp-ieux.md)
> - [Java](create-first-function-cli-java.md)
> - [JavaScript](create-first-function-cli-node.md)
> - [PowerShell](create-first-function-cli-powershell.md)
> - [Python](create-first-function-cli-python.md)
> - [TypeScript](create-first-function-cli-typescript.md)

Ebben a cikkben parancssori eszközökkel hozhat létre egy C# szintű függvénytár-alapú függvényt, amely válaszol a HTTP-kérelmekre. A kód helyi tesztelését követően telepítse azt a <abbr title="Egy futásidejű számítástechnikai környezet, amelyben a kiszolgáló összes adata átlátható az alkalmazások fejlesztői számára, ami leegyszerűsíti a kód üzembe helyezésének és kezelésének folyamatát.">kiszolgáló nélküli</abbr> környezet <abbr title="Az Azure szolgáltatása, amely alacsony költséghatékonyságú, kiszolgáló nélküli számítási környezetet biztosít az alkalmazásokhoz.">Azure Functions</abbr>.

A rövid útmutató elvégzésével az Azure-fiókjában néhány USD értékű vagy annál kisebb költséggel jár.

A cikk [Visual Studio Code-alapú verziója](create-first-function-vs-code-csharp.md) is létezik.

## <a name="1-prepare-your-environment"></a>1. a környezet előkészítése

+ Azure beszerzése <abbr title="Az Azure-használati számlázási adatokat fenntartó profil.">account</abbr> aktív <abbr title="Az alapszintű szervezeti struktúra, amelyben az Azure-ban kezelheti az erőforrásokat, jellemzően a szervezeten belüli személyekhez vagy részlegekhez van társítva.">előfizetést</abbr>. [Hozzon létre egy fiókot ingyenesen](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

+ Telepítés [.NET Core SDK 3,1](https://www.microsoft.com/net/download)

+ Telepítse a [Azure functions Core Tools](functions-run-local.md#v2) 3. x verziót.

+ Vagy a <abbr title="Többplatformos parancssori eszközök az Azure-erőforrások helyi fejlesztési számítógépről való használatához, a Azure Portal használatának alternatívájaként.">Azure CLI</abbr> vagy <abbr title="Egy PowerShell-modul, amely parancsokat biztosít az Azure-erőforrások helyi fejlesztési számítógépről való használatához, a Azure Portal használatának alternatívájaként.">Azure PowerShell</abbr> Azure-erőforrások létrehozásához:

    + Az [Azure CLI](/cli/azure/install-azure-cli) 2,4-es vagy újabb verziója.

    + [Azure PowerShell](/powershell/azure/install-az-ps) 5,0-es vagy újabb verzió.

---

### <a name="2-verify-prerequisites"></a>2. Előfeltételek ellenőrzése

Ellenőrizze az előfeltételeket, amelyek attól függnek, hogy az Azure CLI-t vagy Azure PowerShell Azure-erőforrások létrehozásához használja-e:

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

+ A terminál vagy a parancssorablakban futtassa a parancsot, `func --version` és győződjön meg arról, hogy a <abbr title="Parancssori eszközök készlete, amelyekkel a helyi számítógép Azure Functions dolgozhat.">Azure Functions Core Tools</abbr> 3. x verziójú.

+ **Futtatás** `az --version` Az Azure CLI 2,4-es vagy újabb verziójának megadásához.

+ **Futtatás** `az login` Az Azure-ba való bejelentkezéshez és az aktív előfizetés ellenőrzéséhez.

+ **Futtatás** `dotnet --list-sdks` annak ellenőrzését, hogy a .NET Core SDK 3.1. x verziója telepítve van-e

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

+**Futtatás** `func --version` Győződjön meg arról, hogy a Azure Functions Core Tools 3. x verziójú.

+ **Futtatás** `(Get-Module -ListAvailable Az).Version` és ellenőrizze a 5,0-es vagy újabb verziót. 

+ **Futtatás** `Connect-AzAccount` Az Azure-ba való bejelentkezéshez és az aktív előfizetés ellenőrzéséhez.

+ **Futtatás** `dotnet --list-sdks` annak ellenőrzését, hogy a .NET Core SDK 3.1. x verziója telepítve van-e

---

## <a name="3-create-a-local-function-project"></a>3. helyi függvény létrehozása projekt

Ebben a szakaszban létrehoz egy helyi <abbr title="Egy vagy több, együtt telepíthető és felügyelhető különálló függvény logikai tárolója.">Azure Functions projekt</abbr> a C# nyelvben. A projekt minden funkciója válaszol egy adott <abbr title="Egy esemény, amely meghívja a függvény kódját, például egy HTTP-kérelmet, egy üzenetsor-üzenetet vagy egy adott időpontot.">eseményindító</abbr>.

1. Futtassa a `func init` parancsot egy functions-projekt létrehozásához egy *LocalFunctionProj* nevű mappában a megadott futtatókörnyezettel:  

    ```csharp
    func init LocalFunctionProj --dotnet
    ```

1. **Futtatás** a "CD LocalFunctionProj" elemre kattintva navigáljon a <abbr title="Ez a mappa a projekthez különböző fájlokat tartalmaz, beleértve a local.settings.json és a host.jsnevű konfigurációs fájlokat is. Mivel a local.settings.json az Azure-ból letöltött titkos kódok is lehetnek, a fájl a. gitignore fájlban alapértelmezés szerint ki van zárva a forrás-vezérlőelemből.">Projektmappa</abbr>.

    ```console
    cd LocalFunctionProj
    ```
    <br/>

1. Adjon hozzá egy függvényt a projekthez a következő parancs használatával:
    
    ```console
    func new --name HttpExample --template "HTTP trigger" --authlevel "anonymous"
    ``` 
    Az `--name` argumentum a függvény egyedi neve (HttpExample).

    Az `--template` argumentum megadja a függvény triggerét (http).

    
    <br/>   
    <details>  
    <summary><strong>Nem kötelező: kód a HttpExample.cs</strong></summary>  
    
    A *HttpExample.cs* olyan `Run` metódust tartalmaz, amely a változóban fogadja a `req` [HttpRequest](/dotnet/api/microsoft.aspnetcore.http.httprequest) , amely a **HttpTriggerAttribute** díszített, amely meghatározza az trigger viselkedését.

    :::code language="csharp" source="~/functions-docs-csharp/http-trigger-template/HttpExample.cs":::
        
    A Return objektum egy [ActionResult](/dotnet/api/microsoft.aspnetcore.mvc.actionresult) , amely egy válaszüzenetet ad vissza [OkObjectResult](/dotnet/api/microsoft.aspnetcore.mvc.okobjectresult) (200) vagy [BadRequestObjectResult](/dotnet/api/microsoft.aspnetcore.mvc.badrequestobjectresult) (400). További információ: [Azure FUNCTIONS http-eseményindítók és-kötések](./functions-bindings-http-webhook.md?tabs=csharp).  
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


1. Válassza a  <kbd>CTRL + C billentyűkombinációt</kbd> , majd a functions gazdagép leállításához válassza az <kbd>y</kbd> lehetőséget.

<br/>

---
    
## <a name="5-create-supporting-azure-resources-for-your-function"></a>5. a függvényhez kapcsolódó Azure-erőforrások létrehozása

Mielőtt üzembe helyezi a függvény kódját az Azure-ban, létre kell hoznia egy <abbr title="Logikai tároló a kapcsolódó Azure-erőforrásokhoz, amelyeket egységként lehet kezelni.">erőforráscsoport</abbr>, a <abbr title="Egy olyan fiók, amely az összes Azure Storage-adatobjektumot tartalmazza. A Storage-fiók egyedi névteret biztosít a tárolási adataihoz.">tárfiók</abbr>és egy <abbr title="Az Azure-ban kiszolgáló nélküli funkciókat futtató felhőalapú erőforrás, amely a függvények futtatását szolgáló számítási környezetet biztosítja.">function alkalmazás</abbr> a következő parancsok használatával:

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


    ---

    Nem futtathat Linux-és Windows-alkalmazásokat ugyanabban az erőforráscsoporthoz. Ha már van egy nevű erőforráscsoport `AzureFunctionsQuickstart-rg` egy Windows-függvény alkalmazással vagy webalkalmazással, egy másik erőforráscsoportot kell használnia.
    
1. Hozzon létre egy általános célú Azure Storage-fiókot az erőforráscsoport és a régió területén:

    # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

    ```azurecli
    az storage account create --name <STORAGE_NAME> --location westeurope --resource-group AzureFunctionsQuickstart-rg --sku Standard_LRS
    ```


    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

    ```azurepowershell
    New-AzStorageAccount -ResourceGroupName AzureFunctionsQuickstart-rg -Name <STORAGE_NAME> -SkuName Standard_LRS -Location westeurope
    ```


    ---

    A helyére írja be `<STORAGE_NAME>` a megfelelő nevet, és <abbr title="A névnek egyedinek kell lennie az Azure-ügyfelek által világszerte használt összes Storage-fiókban. Használhatja például a személyes vagy a vállalat neve, az alkalmazás neve és a numerikus azonosító kombinációját a contosobizappstorage20">egyedi az Azure Storage-ban</abbr>. A névnek három – 24 karakterből kell állnia, és csak kisbetűket tartalmazhat. `Standard_LRS` a [függvények által támogatott](storage-considerations.md#storage-account-requirements)általános célú fiókot határozza meg.


1. Hozza létre a Function alkalmazást az Azure-ban.
**Cserélje le** "<STORAGE_NAME> * * az előző lépésben szereplő névvel.
**Cserélje le** Globálisan egyedi névvel rendelkező "<APP_NAME>".

    # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
        
    ```azurecli
    az functionapp create --resource-group AzureFunctionsQuickstart-rg --consumption-plan-location westeurope --runtime dotnet --functions-version 3 --name <APP_NAME> --storage-account <STORAGE_NAME>
    ```
    
    
    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)
    
    ```azurepowershell
    New-AzFunctionApp -Name <APP_NAME> -ResourceGroupName AzureFunctionsQuickstart-rg -StorageAccount <STORAGE_NAME> -Runtime dotnet -FunctionsVersion 3 -Location 'West Europe'
    ```
    
    
    ---
    
    A helyére írja `<STORAGE_NAME>` be az előző lépésben használt fiók nevét.

    Cserélje `<APP_NAME>` le a <abbr title="Az összes Azure-ügyfél globálisan egyedi névnek kell lennie. Használhatja például a személyes vagy szervezet neve, az alkalmazás neve és a numerikus azonosító kombinációját, mint a contoso-bizapp-függvény-20.">egyedi név</abbr>. Az `<APP_NAME>` egyben a függvényalkalmazás alapértelmezett DNS-tartományaként is szolgál, 

    <br/>
    <details>
    <summary><strong>Milyen költségeket kell kiépíteni az Azure-beli erőforrásokért?</strong></summary>

    Ez a parancs létrehoz egy, a megadott nyelvi futtatókörnyezetben futó Function alkalmazást a [Azure functions használati terv](consumption-plan.md)alatt, amely ingyenesen használható az itt felmerülő felhasználási mennyiséghez. A parancs egy kapcsolódó Azure Application Insights-példányt is kiépít ugyanabban az erőforráscsoporthoz, amellyel nyomon követheti a Function alkalmazást, és megtekintheti a naplókat. További információ: [Azure functions figyelése](functions-monitoring.md). A példány nem jár költséggel, amíg be nem aktiválja.
    </details>

<br/>

---

## <a name="6-deploy-the-function-project-to-azure"></a>6. a Function projekt üzembe helyezése az Azure-ban


**Másolás** "az Azure funtionapp az <APP_NAME> a terminálba  `<APP_NAME>` az alkalmazás nevével helyettesíti.
**Futtatás**

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

Másolja a parancs kimenetében látható teljes **Meghívási URL-** címet `publish` egy böngésző címsorába. **Fűzze hozzá** a lekérdezési paramétert **&Name = functions**-hez. 

![A függvény kimenete az Azure-ban egy böngészőben fut](../../includes/media/functions-run-remote-azure-cli/function-test-cloud-browser.png)

<br/>

---

## <a name="8-clean-up-resources"></a>8. erőforrások törlése

Ha folytatja a [következő lépéssel](#next-steps) , és hozzáadja az Azure Storage-várólista kimenetét <abbr title="Egy függvény és más erőforrások deklaratív kapcsolata. A bemeneti kötés adatokat biztosít a függvénynek; a kimeneti kötések adatokat biztosítanak a függvénytől más erőforrásoknak.">kötés</abbr>megtarthatja az összes erőforrását, ahogy a már elvégzett szolgáltatásra épít.

Ellenkező esetben a következő paranccsal törölheti az erőforráscsoportot és az összes benne lévő erőforrást, hogy elkerülje a további költségek felmerülését.

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
> [Kapcsolódás Azure Storage-várólistához](functions-add-output-binding-storage-queue-cli.md?pivots=programming-language-csharp)
