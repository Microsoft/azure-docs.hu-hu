---
title: .NET 5 függvények fejlesztése és közzététele Azure Functions használatával
description: Ismerje meg, hogyan hozhat létre és kereshet C#-függvényeket a .NET 5,0 használatával, majd hogyan telepítheti a helyi projektet a Azure Functions kiszolgáló nélküli üzemeltetésére.
ms.date: 03/03/2021
ms.topic: how-to
zone_pivot_groups: development-environment-functions
ms.openlocfilehash: 9751d5d354d738a64d6b02e8153464491d78ec14
ms.sourcegitcommit: 5f32f03eeb892bf0d023b23bd709e642d1812696
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/12/2021
ms.locfileid: "103201364"
---
# <a name="develop-and-publish-net-5-functions-using-azure-functions"></a>.NET 5 függvények fejlesztése és közzététele Azure Functions használatával 

Ebből a cikkből megtudhatja, hogyan dolgozhat a C#-függvényekkel a .NET 5,0-es verzióval, amely a Azure Functions futtatókörnyezetből folyamaton kívül fut. Megtudhatja, hogyan hozhat létre, végezhet hibakeresést helyileg, és hogyan teheti közzé ezeket a .NET elkülönített folyamatokat az Azure-ban. Az Azure-ban ezek a függvények egy olyan elkülönített folyamaton futnak, amely támogatja a .NET 5,0-et. További információ: [útmutató a függvények futtatásához a .net 5,0-ben az Azure-ban](dotnet-isolated-process-guide.md).

Ha nincs szüksége a .NET 5,0 támogatására vagy a függvények folyamaton kívüli futtatására, érdemes lehet [C#-függvénytári függvényt létrehoznia](functions-create-your-first-function-visual-studio.md).

>[!NOTE]
>A .NET elkülönített folyamat-függvények fejlesztése a Azure Portal jelenleg nem támogatott. Az Azure CLI vagy a Visual Studio Code Publishing használatával olyan Function-alkalmazást hozhat létre az Azure-ban, amely támogatja a .NET 5,0-alkalmazások folyamaton kívüli futtatását.   

## <a name="prerequisites"></a>Előfeltételek

+ Aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egy fiókot ingyenesen](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

+ [.NET SDK 5.0](https://www.microsoft.com/net/download)

+ [Azure functions Core Tools](functions-run-local.md#v2) verzió 3.0.3381 vagy újabb verzió.

+ Az [Azure CLI](/cli/azure/install-azure-cli) 2,20-es vagy újabb verziója.  
::: zone pivot="development-environment-vscode"
+ [Visual Studio Code](https://code.visualstudio.com/) az egyik [támogatott platformon](https://code.visualstudio.com/docs/supporting/requirements#_platforms).  

+ A Visual Studio Code [C#-bővítménye](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp).  

+ A Visual Studio Code-hoz készült [Azure functions-bővítmény](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) , 1.3.0 vagy újabb verzió.
::: zone-end
::: zone pivot="development-environment-vs"
+ [Visual Studio 2019](https://azure.microsoft.com/downloads/), beleértve az **Azure-fejlesztési** számítási feladatot.  
A .NET elkülönített függvény projekt sablonjai és a közzététel jelenleg nem érhető el a Visual Studióban.
::: zone-end

## <a name="create-a-local-function-project"></a>Helyi függvény projekt létrehozása

Azure Functions egy függvény-projekt egy vagy több olyan egyedi függvény tárolója, amely mindegyik reagál egy adott triggerre. Egy projekt összes funkciója ugyanazokat a helyi és üzemeltetési konfigurációkat használja. Ebben a szakaszban egy függvény-projektet hoz létre, amely egyetlen függvényt tartalmaz.

::: zone pivot="development-environment-vs"

>[!NOTE]  
> Jelenleg nincsenek olyan Visual Studio-sablonok, amelyek támogatják a .NET elkülönített függvények projektjeinek létrehozását. Ez a cikk bemutatja, hogyan hozhatja létre a C#-projektet a Core Tools használatával, amelyet aztán helyileg futtathat, és hibakeresést végezhet a Visual Studióban.  

::: zone-end

::: zone pivot="development-environment-vscode"  
1. A műveleti sávban válassza ki az Azure ikont, majd az **Azure: Functions** területen válassza az **Új projekt létrehozása...** ikont.

    ![Új projekt létrehozása lehetőség kiválasztása](./media/functions-create-first-function-vs-code/create-new-project.png)

1. Válasszon egy címtárbeli helyet a projekt munkaterületéhez, és kattintson a **Kiválasztás** lehetőségre.

    > [!NOTE]
    > Ezeket a lépéseket úgy tervezték, hogy a munkaterületen kívül is el lehessen végezni. Ebben az esetben ne válasszon olyan projektmappát, amely valamely munkaterület része.

1. Amikor a rendszer kéri, adja meg az alábbi információkat:

    + **Válasszon nyelvet a függvényprojekthez**: Válassza a `C#` elemet.

    + **Válassza ki a .net-futtatókörnyezetet**: válassza a lehetőséget `.NET 5 isolated` .

    + **Válasszon sablont a projekt első függvényéhez**: Válassza a `HTTP trigger` elemet.

    + **Adjon meg egy függvénynevet**: Gépelje be: `HttpExample`.

    + **Adjon meg egy névteret**: Gépelje be: `My.Functions`.

    + **Engedélyezési szint**: válassza `Anonymous` a lehetőséget, amely lehetővé teszi, hogy bárki meghívja a függvény végpontját. Az engedélyezési szint megismeréséhez tekintse meg az [engedélyezési kulcsok](functions-bindings-http-webhook-trigger.md#authorization-keys)című témakört.

    + **Válassza ki, hogyan szeretné megnyitni a projektet**: Válassza a `Add to workspace` elemet.

1. Ezen információk használatával a Visual Studio Code egy Azure Functions projektet hoz létre egy HTTP-triggerrel. A helyi projektfájlok a Explorerben tekinthetők meg. További információ a létrehozott fájlokról: [generált projektfájlok](functions-develop-vs-code.md#generated-project-files).
::: zone-end  
::: zone pivot="development-environment-cli,development-environment-vs"  

1. Futtassa az `func init` alábbi parancsot a functions-projekt létrehozásához a *LocalFunctionProj* nevű mappában:  

    ```console
    func init LocalFunctionProj --worker-runtime dotnetisolated
    ```
    
    A (z) megadásával `dotnetisolated` a .net 5,0-on futó projekt jön létre.


1. Navigáljon a projekt mappájába:

    ```console
    cd LocalFunctionProj
    ```

    Ez a mappa a projekt különböző fájljait tartalmazza, beleértve a konfigurációs fájlok [local.settings.js](functions-run-local.md#local-settings-file) és [host.js](functions-host-json.md) . Mivel a *local.settings.json* az Azure-ból letöltött titkos kódok is lehetnek, a fájl a *. gitignore* fájlban alapértelmezés szerint ki van zárva a forrás-vezérlőelemből.

1. Adjon hozzá egy függvényt a projekthez a következő parancs használatával, ahol az `--name` argumentum a függvény egyedi neve (HttpExample), az argumentum pedig a `--template` függvény triggerét (http) adja meg.

    ```console
    func new --name HttpExample --template "HTTP trigger" --authlevel "anonymous"
    ``` 

    `func new` létrehoz egy HttpExample.cs.
::: zone-end  

::: zone pivot="development-environment-vscode"  

[!INCLUDE [functions-run-function-test-local-vs-code](../../includes/functions-run-function-test-local-vs-code.md)]

::: zone-end  
::: zone pivot="development-environment-cli" 

[!INCLUDE [functions-run-function-test-local-cli](../../includes/functions-run-function-test-local-cli.md)]

::: zone-end

::: zone pivot="development-environment-vs"

## <a name="run-the-function-locally"></a>A függvény helyi futtatása

Ezen a ponton futtathatja a parancsot a `func start` projekt mappájának gyökeréből a C# elkülönített függvények fordításához és futtatásához. Ha a Visual Studióban szeretné feldolgozni a folyamaton kívüli függvény kódját, a következő lépések végrehajtásával manuálisan csatlakoztatnia kell a hibakeresőt a futó függvények futásidejű folyamatához:  

1. Nyissa meg a projektfájlt (. csproj) a Visual Studióban. Áttekintheti és módosíthatja a projekt kódját, és beállíthatja a kódban szereplő összes kívánt töréspontot. 

1. A legfelső szintű projekt mappából használja a következő parancsot a terminálról vagy egy parancssorból a futásidejű gazdagép elindításához:

    ```console
    func start --dotnet-isolated-debug
    ```

    A `--dotnet-isolated-debug` beállítás azt jelzi, hogy a folytatás előtt meg kell várni a hibakereső csatolását. A kimenet vége felé a következő sorokhoz hasonlóan kell megjelennie: 
    
    <pre>
    ...
    
    Functions:

        HttpExample: [GET,POST] http://localhost:7071/api/HttpExample

    For detailed output, run func with --verbose flag.
    [2021-03-09T08:41:41.904Z] Azure Functions .NET Worker (PID: 81720) initialized in debug mode. Waiting for debugger to attach...
    ...
    
    </pre> 

    A a `PID: XXXXXX` futó functions gazdagép dotnet.exe folyamatának azonosítóját (PID) jelzi.
 
1. A Azure Functions Runtime kimenetében jegyezze fel a gazdagép folyamatának AZONOSÍTÓját, amelyhez a hibakeresőt csatolni kell. Jegyezze fel a helyi függvény URL-címét is.

1. A Visual Studio **hibakeresés** menüjében válassza a **Csatolás a feldolgozáshoz...** lehetőséget, keresse meg a folyamat azonosítójának megfelelő folyamatot, majd válassza a **csatolás** elemet. 
    
    :::image type="content" source="media/dotnet-isolated-process-developer-howtos/attach-to-process.png" alt-text="A hibakereső csatlakoztatása a functions Host folyamathoz":::    

    Ha a hibakeresőt csatolta, a függvény kódját a megszokott módon tudja feldolgozni.

1. A böngésző címsorába írja be a helyi függvény URL-címét, amely a következőhöz hasonlóan néz ki, majd futtassa a kérelmet. 

    <http://localhost:7071/api/HttpExample>

    Meg kell jelennie a futó terminálra írt kérelem nyomkövetési kimenetének. A kód végrehajtása a függvény kódjában beállított összes töréspontnál leáll.

1. Ha elkészült, nyissa meg a terminált, és nyomja le a CTRL + C billentyűkombinációt a gazdagép folyamatának leállításához.
 
Miután ellenőrizte, hogy a függvény megfelelően fut a helyi számítógépen, tegye közzé a projektet az Azure-ban.

> [!NOTE]  
> A Visual Studio Publishing szolgáltatás jelenleg nem érhető el a .NET elkülönített feldolgozási alkalmazások számára. Miután befejezte a projekt fejlesztését a Visual Studióban, a távoli Azure-erőforrások létrehozásához az Azure CLI-t kell használnia. Ezután ismét használhatja a parancssorból a Azure Functions Core Toolst a projekt közzétételéhez az Azure-ban.
::: zone-end

::: zone pivot="development-environment-cli,development-environment-vs" 
## <a name="create-supporting-azure-resources-for-your-function"></a>A függvényhez kapcsolódó Azure-erőforrások létrehozása

Mielőtt üzembe helyezi a függvény kódját az Azure-ban, három erőforrást kell létrehoznia:

- Egy [erőforráscsoport](../azure-resource-manager/management/overview.md), amely a kapcsolódó erőforrások logikai tárolója.
- Egy [Storage-fiók](../storage/common/storage-account-create.md), amely az állapot és a függvények egyéb adatainak fenntartására szolgál.
- Egy Function alkalmazás, amely biztosítja a környezetet a függvény kódjának végrehajtásához. A Function alkalmazás a helyi function projekthez kapcsolódik, és lehetővé teszi, hogy logikai egységként csoportosítsa a függvényeket az erőforrások egyszerűbb felügyelete, üzembe helyezése és megosztása érdekében.

Ezen elemek létrehozásához használja a következő parancsokat.

1. Ha még nem tette meg, jelentkezzen be az Azure-ba:

    ```azurecli
    az login
    ```

    Az az [login](/cli/azure/reference-index#az_login) parancs aláírja az Azure-fiókját.

1. Hozzon létre egy nevű erőforráscsoportot `AzureFunctionsQuickstart-rg` a `westeurope` régióban:

    ```azurecli
    az group create --name AzureFunctionsQuickstart-rg --location westeurope
    ```
 
    Az az [Group Create](/cli/azure/group#az_group_create) parancs létrehoz egy erőforráscsoportot. Az erőforráscsoport és az erőforrások általában az Ön közelében lévő régiókban hozhatók létre, a parancs által visszaadott elérhető régió használatával `az account list-locations` .

1. Hozzon létre egy általános célú Storage-fiókot az erőforráscsoport és a régió területén:

    ```azurecli
    az storage account create --name <STORAGE_NAME> --location westeurope --resource-group AzureFunctionsQuickstart-rg --sku Standard_LRS
    ```

    Az az [Storage Account Create](/cli/azure/storage/account#az_storage_account_create) parancs létrehozza a Storage-fiókot. 

    Az előző példában cserélje le a `<STORAGE_NAME>` nevet, amely megfelel az Ön számára, és egyedi az Azure Storage-ban. A névnek három – 24 karakterből kell állnia, és csak kisbetűket tartalmazhat. `Standard_LRS` a [függvények által támogatott](storage-considerations.md#storage-account-requirements)általános célú fiókot határozza meg.

1. A Function alkalmazás létrehozása az Azure-ban:
   
    ```azurecli
    az functionapp create --resource-group AzureFunctionsQuickstart-rg --consumption-plan-location westeurope --runtime dotnet-isolated --runtime-version 5.0 --functions-version 3 --name <APP_NAME> --storage-account <STORAGE_NAME>
    ```

    Az az [functionapp Create](/cli/azure/functionapp#az_functionapp_create) parancs létrehozza a Function alkalmazást az Azure-ban. 
    
    Az előző példában a helyére írja be az `<STORAGE_NAME>` előző lépésben használt fiók nevét, és cserélje le az értékét a `<APP_NAME>` megfelelő globálisan egyedi névre. Az `<APP_NAME>` egyben a függvényalkalmazás alapértelmezett DNS-tartományaként is szolgál, 
    
    Ez a parancs létrehoz egy .NET 5,0-et futtató Function alkalmazást a [Azure functions használati tervben](consumption-plan.md). Ennek a csomagnak díjmentesnek kell lennie az ebben a cikkben felmerülő használati mennyiségért. A parancs egy társított Azure Application Insights-példányt is kiépít ugyanabban az erőforráscsoporthoz. Ezzel a példánnyal figyelheti a Function alkalmazást, és megtekintheti a naplókat. További információ: [Azure functions figyelése](functions-monitoring.md). A példány nem jár költséggel, amíg be nem aktiválja.

[!INCLUDE [functions-publish-project-cli](../../includes/functions-publish-project-cli.md)]

::: zone-end

::: zone pivot="development-environment-vscode"

[!INCLUDE [functions-sign-in-vs-code](../../includes/functions-sign-in-vs-code.md)]

## <a name="publish-the-project-to-azure"></a>A projekt közzététele az Azure-ban

Ebben a szakaszban egy Function-alkalmazást és egy kapcsolódó erőforrást hoz létre az Azure-előfizetésében, majd üzembe helyezi a kódot.

> [!IMPORTANT]
> Meglévő függvényalkalmazásba való közzététel felülírja az adott alkalmazás tartalmát az Azure-ban.


1. Válassza ki az Azure ikont a tevékenység sávjában, majd az **Azure: functions** területen válassza az **üzembe helyezés az alkalmazásban** ... gombot.

    ![A projekt közzététele az Azure-ban](../../includes/media/functions-publish-project-vscode/function-app-publish-project.png)

1. Amikor a rendszer kéri, adja meg az alábbi információkat:

    - **Mappa kiválasztása**: válasszon egy mappát a munkaterületről, vagy tallózással keresse meg a Function alkalmazást tartalmazó mappát. Ezt a kérdést nem fogja látni, ha már nyitva van egy érvényes Function alkalmazás.

    - **Előfizetés kiválasztása**: válassza ki a használni kívánt előfizetést. Ezt a kérdést nem fogja látni, ha csak egy előfizetéssel rendelkezik.

    - **Azure-beli Függvényalkalmazás kiválasztása**: Válassza a `- Create new Function App` elemet. (Ne válassza a `Advanced` lehetőséget, amely nem szerepel ebben a cikkben.)
      
    - **Adja meg a Function alkalmazás globálisan egyedi nevét**: írjon be egy URL-útvonalon érvényes nevet. A rendszer érvényesíti a beírt nevet, hogy a Azure Functions egyedi legyen.
    
    - **Válassza ki a futtatókörnyezetet**: válassza a lehetőséget `.NET 5 (non-LTS)` . 
    
    - **Válasszon egy helyet az új erőforrásokhoz**: a jobb teljesítmény érdekében válasszon egy Önhöz közeli [régiót](https://azure.microsoft.com/regions/) . 
    
    Az értesítési területen megtekintheti az egyes erőforrások állapotát, amelyeket az Azure-ban hoznak létre.

    :::image type="content" source="../../includes/media/functions-publish-project-vscode/resource-notification.png" alt-text="Értesítés az Azure-erőforrások létrehozásáról":::
    
1.  Ha elkészült, a következő Azure-erőforrások jönnek létre az előfizetésben, és neveket kell használni a Function app neve alapján:
    
    [!INCLUDE [functions-vs-code-created-resources](../../includes/functions-vs-code-created-resources.md)]

    A függvényalkalmazás létrehozása és a telepítőcsomag alkalmazása után megjelenik egy értesítés. 

    [!INCLUDE [functions-vs-code-create-tip](../../includes/functions-vs-code-create-tip.md)]

4. Válassza ki az értesítés **kimenetének megtekintése** lehetőséget a létrehozási és a telepítési eredmények megtekintéséhez, beleértve a létrehozott Azure-erőforrásokat is. Ha kihagyja az értesítést, válassza a jobb alsó sarokban található harang ikont az újbóli megjelenítéshez.

    ![Teljes értesítés létrehozása](../../includes/media/functions-publish-project-vscode/function-create-notifications.png)

::: zone-end

::: zone pivot="development-environment-cli"  
[!INCLUDE [functions-run-remote-azure-cli](../../includes/functions-run-remote-azure-cli.md)]  
::: zone-end  

::: zone pivot="development-environment-vscode"  
[!INCLUDE [functions-vs-code-run-remote](../../includes/functions-vs-code-run-remote.md)]  
::: zone-end  

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

A cikk végrehajtásához erőforrásokat hozott létre. [Fiókjának állapotától](https://azure.microsoft.com/account/) és a [szolgáltatási díjszabástól](https://azure.microsoft.com/pricing/) függően lehetséges, hogy az erőforrások használata díjköteles. 

::: zone pivot="development-environment-cli"  
A következő paranccsal törölheti az erőforráscsoportot és az összes benne lévő erőforrást, hogy elkerülje a további költségek felmerülését.

```azurecli
az group delete --name AzureFunctionsQuickstart-rg
```
::: zone-end  

::: zone pivot="development-environment-vscode"  
A következő lépésekkel törölheti a Function alkalmazást és annak kapcsolódó erőforrásait, hogy elkerülje a további költségek felmerülését.

[!INCLUDE [functions-cleanup-resources-vs-code-inner.md](../../includes/functions-cleanup-resources-vs-code-inner.md)]  
::: zone-end  
::: zone pivot="development-environment-vs"   
A következő lépésekkel törölheti a Function alkalmazást és annak kapcsolódó erőforrásait, hogy elkerülje a további költségek felmerülését.

1. A Cloud Explorerben bontsa ki az előfizetését > **app Services**, kattintson a jobb gombbal a Function alkalmazásra, majd válassza a **Megnyitás a portálon** lehetőséget. 

1. A Function alkalmazás lapon válassza az **Áttekintés** lapot, majd válassza ki az **erőforráscsoport** területen található hivatkozást.

   :::image type="content" source="media/functions-create-your-first-function-visual-studio/functions-app-delete-resource-group.png" alt-text="Válassza ki a függvény alkalmazás oldaláról törölni kívánt erőforráscsoportot.":::

2. Az **erőforráscsoport** lapon tekintse át a befoglalt erőforrások listáját, és ellenőrizze, hogy a törölni kívántak-e.
 
3. Válassza az **Erőforráscsoport törlése** elemet, majd kövesse az utasításokat.

   A törlés eltarthat néhány percig. Amint a művelet befejeződött, néhány másodpercre egy értesítés jelenik meg. Az értesítést úgy is megtekintheti, ha kiválasztja a harang ikont az oldal tetején.
::: zone-end

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [További információ a .NET elkülönített függvényekről](dotnet-isolated-process-guide.md)

