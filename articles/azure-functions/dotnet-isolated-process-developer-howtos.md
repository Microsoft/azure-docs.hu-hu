---
title: .NET 5-függvények fejlesztése és közzététele Azure Functions
description: Megtudhatja, hogyan hozhat létre és hibakeresést C#-függvények a .NET 5.0 használatával, majd hogyan helyezheti üzembe a helyi projektet kiszolgáló nélküli üzemeltetésre a Azure Functions.
ms.date: 03/03/2021
ms.topic: how-to
zone_pivot_groups: development-environment-functions
ms.openlocfilehash: c76fde9a61ca60171ac094ef541e8c5841846aab
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2021
ms.locfileid: "107866268"
---
# <a name="develop-and-publish-net-5-functions-using-azure-functions"></a>.NET 5-függvények fejlesztése és közzététele Azure Functions 

Ez a cikk bemutatja, hogyan használhatók a C#-függvények a .NET 5.0 használatával, amelyek a Azure Functions futnak. Megtudhatja, hogyan hozhatja létre, hogyan lehet helyileg hibakeresést végezni, és hogyan lehet közzétenni ezeket az elkülönített .NET-folyamatfunkciókat az Azure-ban. Az Azure-ban ezek a függvények a .NET 5.0-t támogató elkülönített folyamatban futnak. További tudnivalókért lásd: Útmutató függvények [futtatásához a .NET 5.0-ban az Azure-ban.](dotnet-isolated-process-guide.md)

Ha nincs szüksége a .NET 5.0 támogatására vagy a függvények folyamaton túli futtatására, érdemes létrehoznia egy C#-osztálytári [függvényt.](functions-create-your-first-function-visual-studio.md)

>[!NOTE]
>A .NET elkülönített folyamatfunkciók fejlesztése a Azure Portal jelenleg nem támogatott. Az Azure CLI-t vagy az Visual Studio Code-közzétételt kell használnia egy olyan függvényalkalmazás létrehozásához az Azure-ban, amely támogatja a .NET 5.0-alkalmazások folyamaton belüli futtatását.   

## <a name="prerequisites"></a>Előfeltételek

+ Aktív előfizetéssel rendelkezik egy Azure-fiók. [Hozzon létre egy ingyenes fiókot.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)

+ [.NET 5.0 SDK](https://dotnet.microsoft.com/download)

+ [Azure Functions Core Tools](functions-run-local.md#v2) 3.0.3381-es vagy újabb verziót.

+ [Az Azure CLI](/cli/azure/install-azure-cli) 2.20-as vagy újabb verziója.  
::: zone pivot="development-environment-vscode"
+ [Visual Studio Code](https://code.visualstudio.com/) az egyik [támogatott platformon](https://code.visualstudio.com/docs/supporting/requirements#_platforms).  

+ A Visual Studio Code [C#-bővítménye](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp).  

+ A [Azure Functions](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) Code Visual Studio 1.3.0-s vagy újabb verziója.
::: zone-end
::: zone pivot="development-environment-vs"
+ [Visual Studio 2019-es,](https://azure.microsoft.com/downloads/)az **Azure-fejlesztési számítási feladatot** is beleértve.  
A .NET izolált függvény projektsablonjai és közzététele jelenleg nem érhető el a Visual Studio.
::: zone-end

## <a name="create-a-local-function-project"></a>Helyi függvényprojekt létrehozása

A Azure Functions a függvényprojekt egy vagy több különálló függvény tárolója, amelyek egy adott eseményindítóra válaszolnak. Egy projekt összes függvénye azonos helyi és üzemeltetési konfigurációval rendelkezik. Ebben a szakaszban egy függvényprojektet hoz létre, amely egyetlen függvényt tartalmaz.

::: zone pivot="development-environment-vs"

>[!NOTE]  
> Jelenleg nincsenek olyan projektsablonok, Visual Studio a .NET izolált függvényprojektek létrehozását. Ez a cikk bemutatja, hogyan hozhatja létre a C#-projektet a Core Tools használatával, amelyet helyileg futtathat, és hibakeresést futtathat a Visual Studio.  

::: zone-end

::: zone pivot="development-environment-vscode"  
1. A műveleti sávban válassza ki az Azure ikont, majd az **Azure: Functions** területen válassza az **Új projekt létrehozása...** ikont.

    ![Új projekt létrehozása lehetőség kiválasztása](./media/functions-create-first-function-vs-code/create-new-project.png)

1. Válasszon egy címtárbeli helyet a projekt munkaterületéhez, és kattintson a **Kiválasztás** lehetőségre.

    > [!NOTE]
    > Ezeket a lépéseket munkaterületen kívülre tervezték. Ebben az esetben ne válasszon olyan projektmappát, amely valamely munkaterület része.

1. Amikor a rendszer kéri, adja meg az alábbi információkat:

    + **Válasszon nyelvet a függvényprojekthez**: Válassza a `C#` elemet.

    + **Válasszon ki egy .NET-runtime-t:** Válassza a `.NET 5 isolated` lehetőséget.

    + **Válasszon sablont a projekt első függvényéhez**: Válassza a `HTTP trigger` elemet.

    + **Adjon meg egy függvénynevet**: Gépelje be: `HttpExample`.

    + **Adjon meg egy névteret**: Gépelje be: `My.Functions`.

    + **Engedélyezési szint:** Válassza a lehetőséget, amely bárki számára lehetővé `Anonymous` teszi a függvényvégpont hívását. Az engedélyezési szinttel kapcsolatos további információkért lásd: [Engedélyezési kulcsok.](functions-bindings-http-webhook-trigger.md#authorization-keys)

    + **Válassza ki, hogyan szeretné megnyitni a projektet**: Válassza a `Add to workspace` elemet.

1. Ezen információk alapján a Visual Studio Code létrehoz egy Azure Functions projektet egy HTTP-eseményindítóval. A helyi projektfájlokat az Explorerben tudja megtekinteni. A létrehozott fájlokkal kapcsolatos további információkért lásd a létrehozott [projektfájlokat.](functions-develop-vs-code.md#generated-project-files)
::: zone-end  
::: zone pivot="development-environment-cli,development-environment-vs"  

1. Futtassa a parancsot az alábbiak szerint, hogy létrehozzon egy `func init` függvényprojektet egy *LocalFunctionProj nevű mappában:*  

    ```console
    func init LocalFunctionProj --worker-runtime dotnetisolated
    ```
    
    A megadása `dotnetisolated` létrehoz egy projektet, amely a .NET 5.0-ban fut.


1. Lépjen a projektmappába:

    ```console
    cd LocalFunctionProj
    ```

    Ez a mappa a projekt különböző [](functions-run-local.md#local-settings-file) fájljait tartalmazza, beleértve alocal.settings.jséshost.js[konfigurációs](functions-host-json.md) fájlokra vonatkozó adatokat. Mivel *local.settings.js* fájl tartalmazhatja az Azure-ból letöltött titkos adatokat, a fájl alapértelmezés szerint ki van zárva a verziókezelőből a *.gitignore fájlban.*

1. Adjon hozzá egy függvényt a projekthez a következő paranccsal, ahol az argumentum a függvény (HttpExample) egyedi neve, az argumentum pedig a függvény eseményindítóját `--name` `--template` (HTTP) határozza meg.

    ```console
    func new --name HttpExample --template "HTTP trigger" --authlevel "anonymous"
    ``` 

    `func new` A létrehoz egy HttpExample.cs kódfájlt.
::: zone-end  

::: zone pivot="development-environment-vscode"  

[!INCLUDE [functions-run-function-test-local-vs-code](../../includes/functions-run-function-test-local-vs-code.md)]

::: zone-end  
::: zone pivot="development-environment-cli" 

[!INCLUDE [functions-run-function-test-local-cli](../../includes/functions-run-function-test-local-cli.md)]

::: zone-end

::: zone pivot="development-environment-vs"

## <a name="run-the-function-locally"></a>A függvény helyi futtatása

Ezen a ponton futtathatja a parancsot a projektmappa gyökerében a C# elkülönített `func start` függvényprojekt fordításához és futtatásához. Ha jelenleg a folyamaton túli függvénykódon szeretne hibakeresést végezni az Visual Studio-ban, manuálisan kell csatolnia egy hibakeresőt a futó Functions-futtatókörnyezeti folyamathoz az alábbi lépések használatával:  

1. Nyissa meg a projektfájlt (.csproj) a Visual Studio. Áttekintheti és módosíthatja a projektkódot, és beállíthatja a kívánt töréspontokat a kódban. 

1. A gyökérprojekt mappájában használja a terminálon a következő parancsot vagy egy parancssort a futásidejű gazdagép futtatásához:

    ```console
    func start --dotnet-isolated-debug
    ```

    A kapcsoló arra utasítja a folyamatot, hogy a folytatás előtt várja meg, amíg `--dotnet-isolated-debug` a hibakereső csatolva lesz. A kimenet vége felé az alábbi sorokhoz hasonlónak kell lennie: 
    
    <pre>
    ...
    
    Functions:

        HttpExample: [GET,POST] http://localhost:7071/api/HttpExample

    For detailed output, run func with --verbose flag.
    [2021-03-09T08:41:41.904Z] Azure Functions .NET Worker (PID: 81720) initialized in debug mode. Waiting for debugger to attach...
    ...
    
    </pre> 

    A a futó Functions-gazdagépként dotnet.exe folyamat folyamatazonosítóját `PID: XXXXXX` (PID).
 
1. A Azure Functions-kimenetben jegyezze fel a gazdafolyamat folyamatazonosítóját, amelyhez csatolni fog egy hibakeresőt. Jegyezze fel a helyi függvény URL-címét is.

1. A **hibakeresési menüben** válassza Visual Studio **Csatolás folyamathoz...** lehetőséget, keresse meg a folyamatazonosítónak megfelelő folyamatot, majd válassza a Csatolás **lehetőséget.** 
    
    :::image type="content" source="media/dotnet-isolated-process-developer-howtos/attach-to-process.png" alt-text="A hibakereső csatolása a Functions-gazdafolyamathoz":::    

    A csatolt hibakeresővel a szokásos módon hibakeresést is futtathat a függvénykódon.

1. A böngésző címsorába írja be a helyi függvény URL-címét, amely az alábbihoz hasonló, majd futtassa a kérést. 

    `http://localhost:7071/api/HttpExample`

    Látnia kell a futó terminálra írt kérés nyomkövetési kimenetét. A kódvégrehajtás leáll a függvény kódban beállított töréspontoknál.

1. Ha végzett, a terminálban állítsa le a gazdagépfolyamatot a Ctrl + C billentyűkombinációval.
 
Miután ellenőrizte, hogy a függvény megfelelően fut a helyi számítógépen, tegye közzé a projektet az Azure-ban.

> [!NOTE]  
> Visual Studio közzététel jelenleg nem érhető el a .NET elkülönített folyamatalkalmazások esetében. Miután befejezte a projekt kidolgozását a Visual Studio, az Azure CLI-t kell használnia a távoli Azure-erőforrások létrehozásához. Ezután ismét használhatja a parancssorból Azure Functions Core Tools, hogy közzéteje a projektet az Azure-ban.
::: zone-end

::: zone pivot="development-environment-cli,development-environment-vs" 
## <a name="create-supporting-azure-resources-for-your-function"></a>Támogató Azure-erőforrások létrehozása a függvényhez

Mielőtt üzembe helyezheti a függvénykódot az Azure-ban, három erőforrást kell létrehoznia:

- Egy [erőforráscsoport,](../azure-resource-manager/management/overview.md)amely a kapcsolódó erőforrások logikai tárolója.
- Egy [Tárfiók,](../storage/common/storage-account-create.md)amely a függvények állapotának és egyéb információinak fenntartására használható.
- Egy függvényalkalmazást, amely biztosítja a függvénykód futtatásának környezetét. A függvényalkalmazás a helyi függvényprojekthez van leképezve, és lehetővé teszi a függvények logikai egységként való csoportosítását az erőforrások egyszerűbb kezelése, üzembe helyezése és megosztása érdekében.

Ezeket az elemeket az alábbi parancsokkal hozhatja létre.

1. Ha még nem tette meg, jelentkezzen be az Azure-ba:

    ```azurecli
    az login
    ```

    Az [az login](/cli/azure/reference-index#az_login) parancs bejelentkeztet az Azure-fiókjába.

1. Hozzon létre egy nevű `AzureFunctionsQuickstart-rg` erőforráscsoportot a `westeurope` régióban:

    ```azurecli
    az group create --name AzureFunctionsQuickstart-rg --location westeurope
    ```
 
    Az [az group create parancs](/cli/azure/group#az_group_create) létrehoz egy erőforráscsoportot. Az erőforráscsoportot és az erőforrásokat általában egy Közeli régióban hozza létre a parancs által visszaadott elérhető régió `az account list-locations` használatával.

1. Hozzon létre egy általános célú tárfiókot az erőforráscsoportban és régióban:

    ```azurecli
    az storage account create --name <STORAGE_NAME> --location westeurope --resource-group AzureFunctionsQuickstart-rg --sku Standard_LRS
    ```

    Az [az storage account create parancs](/cli/azure/storage/account#az_storage_account_create) létrehozza a tárfiókot. 

    Az előző példában cserélje le a helyére az Ön számára megfelelő, az `<STORAGE_NAME>` Azure Storage-ban egyedi nevet. A neveknek három–24 karakter hosszúságú számokat és csak kisbetűket tartalmazhatnak. `Standard_LRS`A egy általános célú fiókot ad meg, amelyet [a Functions támogat.](storage-considerations.md#storage-account-requirements)

1. A függvényalkalmazás létrehozása az Azure-ban:
   
    ```azurecli
    az functionapp create --resource-group AzureFunctionsQuickstart-rg --consumption-plan-location westeurope --runtime dotnet-isolated --runtime-version 5.0 --functions-version 3 --name <APP_NAME> --storage-account <STORAGE_NAME>
    ```

    Az [az functionapp create parancs](/cli/azure/functionapp#az_functionapp_create) létrehozza a függvényalkalmazást az Azure-ban. 
    
    Az előző példában cserélje le a helyére az előző lépésben használt fiók nevét, a helyére pedig az Önnek megfelelő globálisan `<STORAGE_NAME>` `<APP_NAME>` egyedi nevet. Az `<APP_NAME>` egyben a függvényalkalmazás alapértelmezett DNS-tartományaként is szolgál, 
    
    Ez a parancs egy .NET 5.0-t futtató függvényalkalmazást hoz létre [a Azure Functions csomag alatt.](consumption-plan.md) Ennek a csomagnak ingyenesnek kell lennie az ebben a cikkben használatban. A parancs ki is hoz egy társított Azure Application Insights példányt ugyanabban az erőforráscsoportban. Ezzel a példánysal figyelheti a függvényalkalmazást, és megtekintheti a naplókat. További információ: [Monitor Azure Functions.](functions-monitoring.md) A példány nem jár költségekkel, amíg ön nem aktiválja.

[!INCLUDE [functions-publish-project-cli](../../includes/functions-publish-project-cli.md)]

::: zone-end

::: zone pivot="development-environment-vscode"

[!INCLUDE [functions-sign-in-vs-code](../../includes/functions-sign-in-vs-code.md)]

## <a name="publish-the-project-to-azure"></a>A projekt közzététele az Azure-ban

Ebben a szakaszban létrehoz egy függvényalkalmazást és a kapcsolódó erőforrásokat az Azure-előfizetésében, majd üzembe helyezheti a kódot.

> [!IMPORTANT]
> Meglévő függvényalkalmazásba való közzététel felülírja az adott alkalmazás tartalmát az Azure-ban.


1. Válassza az Azure ikont a Tevékenységsávon, majd az **Azure: Functions** területen válassza az Üzembe **helyezés a függvényalkalmazásban...** gombot.

    ![A projekt közzététele az Azure-ban](../../includes/media/functions-publish-project-vscode/function-app-publish-project.png)

1. Amikor a rendszer kéri, adja meg az alábbi információkat:

    - **Mappa kiválasztása:** Válasszon egy mappát a munkaterületről, vagy tallózással keresse meg azt, amely tartalmazza a függvényalkalmazást. Ez a kérdés nem látható, ha már megnyitott egy érvényes függvényalkalmazást.

    - **Előfizetés kiválasztása:** Válassza ki a használni kívánt előfizetést. Ha csak egy előfizetéssel rendelkezik, akkor ez a kérdés nem fog látni.

    - **Azure-beli Függvényalkalmazás kiválasztása**: Válassza a `- Create new Function App` elemet. (Ne válassza azt a lehetőséget, amelyet ez a cikk `Advanced` nem fed le.)
      
    - Adjon meg egy globálisan egyedi nevet a **függvényalkalmazásnak:** Írjon be egy url-címben érvényes nevet. A rendszer ellenőrzi a beírt nevet, hogy az egyedi legyen a Azure Functions.
    
    - **Válasszon ki egy futásidejű verem:** Válassza a `.NET 5 (non-LTS)` lehetőséget. 
    
    - **Új erőforrások helyének kiválasztása:** A jobb teljesítmény érdekében válasszon egy [közeli](https://azure.microsoft.com/regions/) régiót. 
    
    Az értesítési területen láthatja az egyes erőforrások Azure-beli létrehozásuk állapotát.

    :::image type="content" source="../../includes/media/functions-publish-project-vscode/resource-notification.png" alt-text="Értesítés az Azure-erőforrások létrehozásáról":::
    
1.  Ha elkészült, az alábbi Azure-erőforrásokat a rendszer a függvényalkalmazás neve alapján hozta létre az előfizetésben:
    
    [!INCLUDE [functions-vs-code-created-resources](../../includes/functions-vs-code-created-resources.md)]

    A függvényalkalmazás létrehozása és a telepítőcsomag alkalmazása után megjelenik egy értesítés. 

    [!INCLUDE [functions-vs-code-create-tip](../../includes/functions-vs-code-create-tip.md)]

4. Az **értesítés kimenetének** megtekintése lehetőséget választva megtekintheti a létrehozási és üzembe helyezési eredményeket, beleértve a létrehozott Azure-erőforrásokat is. Ha nem látja az értesítést, a jobb alsó sarokban lévő harang ikont választva újra láthatja.

    ![Teljes értesítés létrehozása](../../includes/media/functions-publish-project-vscode/function-create-notifications.png)

::: zone-end

::: zone pivot="development-environment-cli"  
[!INCLUDE [functions-run-remote-azure-cli](../../includes/functions-run-remote-azure-cli.md)]  
::: zone-end  

::: zone pivot="development-environment-vscode"  
[!INCLUDE [functions-vs-code-run-remote](../../includes/functions-vs-code-run-remote.md)]  
::: zone-end  

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

A cikk befejezéséhez erőforrásokat hozott létre. [Fiókjának állapotától](https://azure.microsoft.com/account/) és a [szolgáltatási díjszabástól](https://azure.microsoft.com/pricing/) függően lehetséges, hogy az erőforrások használata díjköteles. 

::: zone pivot="development-environment-cli"  
A következő paranccsal törölheti az erőforráscsoportot és annak összes tartalmazott erőforrását, hogy elkerülje a további költségeket.

```azurecli
az group delete --name AzureFunctionsQuickstart-rg
```
::: zone-end  

::: zone pivot="development-environment-vscode"  
A következő lépésekkel törölheti a függvényalkalmazást és a kapcsolódó erőforrásokat, hogy elkerülje a további költségeket.

[!INCLUDE [functions-cleanup-resources-vs-code-inner.md](../../includes/functions-cleanup-resources-vs-code-inner.md)]  
::: zone-end  
::: zone pivot="development-environment-vs"   
A következő lépésekkel törölheti a függvényalkalmazást és a kapcsolódó erőforrásokat, hogy elkerülje a további költségeket.

1. A Cloud Explorerben bontsa ki az előfizetését a > **App Services,** kattintson a jobb gombbal a függvényalkalmazásra, majd válassza a Megnyitás a **portálon lehetőséget.** 

1. A függvényalkalmazás oldalán válassza az **Áttekintés lapot,** majd az **Erőforráscsoport alatt található hivatkozást.**

   :::image type="content" source="media/functions-create-your-first-function-visual-studio/functions-app-delete-resource-group.png" alt-text="Válassza ki a törölni kívánt erőforráscsoportot a függvényalkalmazás oldalán":::

2. Az **Erőforráscsoport lapon** tekintse át a benne foglalt erőforrások listáját, és ellenőrizze, hogy azokat szeretné-e törölni.
 
3. Válassza az **Erőforráscsoport törlése** elemet, majd kövesse az utasításokat.

   A törlés eltarthat néhány percig. Amint a művelet befejeződött, néhány másodpercre egy értesítés jelenik meg. Az értesítést úgy is megtekintheti, ha kiválasztja a harang ikont az oldal tetején.
::: zone-end

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [További információ a .NET izolált függvényekkel kapcsolatban](dotnet-isolated-process-guide.md)

