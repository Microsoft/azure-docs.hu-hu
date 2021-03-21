---
title: Oktatóanyag – a tárolók csoportjának elindítása az Azure Function alapján
description: HTTP-alapú, kiszolgáló nélküli PowerShell-függvény létrehozása az Azure Container instances létrehozásának automatizálásához
ms.topic: tutorial
ms.date: 06/10/2020
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: e7315796f2f7f89800b58f5fa607e69cd7ae3447
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98935409"
---
# <a name="tutorial-use-an-http-triggered-azure-function-to-create-a-container-group"></a>Oktatóanyag: egy HTTP-triggert használó Azure-függvény használata tároló csoport létrehozásához

[Azure functions](../azure-functions/functions-overview.md) egy kiszolgáló nélküli számítási szolgáltatás, amely parancsfájlokat vagy kódokat futtathat különböző eseményekre, például egy http-kérelemre, egy időzítőre vagy egy Azure Storage-várólistában lévő üzenetre válaszul.

Ebben az oktatóanyagban egy Azure-függvényt hoz létre, amely HTTP-kérést végez, és elindítja egy [tároló csoport](container-instances-container-groups.md)üzembe helyezését. Ez a példa a Azure Functions használatának alapjait mutatja be Azure Container Instances erőforrásainak automatikus létrehozásához. Módosítsa vagy bővítse a példát összetettebb forgatókönyvek vagy más eseményindítók esetén. 

Az alábbiak végrehajtásának módját ismerheti meg:

> [!div class="checklist"]
> * A Visual Studio Code és a [Azure functions bővítmény](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) használatával hozzon létre egy alapszintű http-triggeres PowerShell-függvényt.
> * Engedélyezzen egy identitást a Function alkalmazásban, és adja meg az IT-engedélyeket Azure-erőforrások létrehozásához.
> * Módosítsa és tegye közzé újra a PowerShell-függvényt egy egytárolós tároló-csoport üzembe helyezésének automatizálásához.
> * Ellenőrizze a tároló HTTP-triggeres telepítését.

## <a name="prerequisites"></a>Előfeltételek

Lásd: [az első függvény létrehozása az Azure-ban a Visual Studio Code használatával](../azure-functions/create-first-function-vs-code-csharp.md?pivots=programming-language-powershell#configure-your-environment) a Visual Studio Code telepítéséhez és használatához az operációs rendszer Azure functions bővítménnyel.

A cikkben szereplő további lépések a Azure PowerShell használatát ismertetik. Ha telepíteni vagy frissíteni szeretne, olvassa el a [Azure PowerShell telepítése][azure-powershell-install] és [Az Azure-ba való bejelentkezés](/powershell/azure/get-started-azureps#sign-in-to-azure)című témakört.

## <a name="create-a-basic-powershell-function"></a>Alapszintű PowerShell-függvény létrehozása

Kövesse az [első PowerShell-függvény létrehozása az Azure-ban](../azure-functions/create-first-function-vs-code-csharp.md?pivots=programming-language-powershell) című témakör lépéseit, és hozzon létre egy PowerShell-függvényt a http-trigger sablon használatával. Használja az alapértelmezett Azure-függvény nevét **HttpTrigger**. Ahogy a gyors útmutatóban is látható, a függvényt helyileg tesztelheti, és közzéteheti a projektet egy Azure-beli Function alkalmazásban. Ez a példa egy alapszintű HTTP-triggert használó függvény, amely szöveges karakterláncot ad vissza. A cikk későbbi lépéseiben módosítja a függvényt egy tároló csoport létrehozásához.

Ez a cikk azt feltételezi, hogy közzéteszi a projektet a *myfunctionapp* név használatával, amely az Azure-erőforráscsoportban automatikusan elnevezett, a Function app neve ( *myfunctionapp*) alapján. Helyettesítse be az egyedi Function-alkalmazás nevét és az erőforráscsoport nevét a későbbi lépésekben.

## <a name="enable-an-azure-managed-identity-in-the-function-app"></a>Azure által felügyelt identitás engedélyezése a Function alkalmazásban

A következő parancsok lehetővé teszik a rendszerhez rendelt [felügyelt identitás](../app-service/overview-managed-identity.md?toc=/azure/azure-functions/toc.json#add-a-system-assigned-identity) használatát a Function alkalmazásban. Az alkalmazást futtató PowerShell-állomás automatikusan képes hitelesíteni az Azure-t ezen identitás használatával, így a függvények olyan Azure-szolgáltatásokban végezhetnek műveleteket, amelyekhez az identitás hozzáférést kap. Ebben az oktatóanyagban megadhatja a felügyelt identitás engedélyeit ahhoz, hogy erőforrásokat hozzon létre a Function alkalmazás erőforráscsoporthoz. 

[Identitás hozzáadása](../app-service/overview-managed-identity.md?tabs=dotnet#using-azure-powershell-1) a Function alkalmazáshoz:

```powershell
Update-AzFunctionApp -Name myfunctionapp `
    -ResourceGroupName myfunctionapp `
    -IdentityType SystemAssigned
```

Rendelje hozzá a közreműködői szerepkört az erőforráscsoport hatóköréhez:

```powershell
$SP=(Get-AzADServicePrincipal -DisplayName myfunctionapp).Id
$RG=(Get-AzResourceGroup -Name myfunctionapp).ResourceId
New-AzRoleAssignment -ObjectId $SP -RoleDefinitionName "Contributor" -Scope $RG
```

## <a name="modify-httptrigger-function"></a>HttpTrigger-függvény módosítása

Módosítsa a **HttpTrigger** függvény PowerShell-kódját egy tároló csoport létrehozásához. A függvény fájljában `run.ps1` Keresse meg a következő kódrészletet. Ez a kód egy Name (név) értéket jelenít meg, ha a függvény URL-címében egy lekérdezési karakterláncként adja át a következőt:

```powershell
[...]
if ($name) {
    $body = "Hello, $name. This HTTP triggered function executed successfully."
}
[...]
```

Cserélje le ezt a kódot a következő példa blokkra. Itt, ha a lekérdezési karakterláncban egy név érték van megadva, akkor a rendszer a [New-AzContainerGroup][new-azcontainergroup] parancsmaggal nevezi át és hozza létre a tároló csoportot. Győződjön meg arról, hogy az erőforráscsoport neve *myfunctionapp* az erőforráscsoport nevére cseréli a Function alkalmazáshoz:

```powershell
[...]
if ($name) {
    New-AzContainerGroup -ResourceGroupName myfunctionapp -Name $name `
        -Image alpine -OsType Linux `
        -Command "echo 'Hello from an Azure container instance triggered by an Azure function'" `
        -RestartPolicy Never
    if ($?) {
        $body = "This HTTP triggered function executed successfully. Started container group $name"
    }
    else  {
        $body = "There was a problem starting the container group."
    }
[...]
```

Ez a példa egy tároló csoportot hoz létre, amely a rendszerképet futtató egyetlen tároló példányból áll `alpine` . A tároló egyetlen parancsot futtat `echo` , majd leáll. Valós példaként egy batch-feladatok futtatásához egy vagy több tároló-csoport létrehozását is elindíthatja.
 
## <a name="test-function-app-locally"></a>A Function alkalmazás helyi tesztelése

Győződjön meg arról, hogy a függvény helyileg fut, mielőtt újból közzéteszi a Function app-projektet az Azure-ban. Ha helyileg fut, a függvény nem hoz létre Azure-erőforrásokat. Azonban tesztelheti a függvényt a és a között, és anélkül, hogy egy lekérdezési karakterláncban adja át a name értéket. A függvény hibakereséséhez tekintse meg a [PowerShell Azure functions helyi hibakeresését](../azure-functions/functions-debug-powershell-local.md)ismertető témakört.

## <a name="republish-azure-function-app"></a>Azure Function-alkalmazás újbóli közzététele

Miután meggyőződött róla, hogy a függvény helyileg fut, tegye közzé újra a projektet az Azure-beli meglévő Function alkalmazásban.

1. Nyissa meg a Parancskatalógust a Visual Studio Code-ban. Keresse meg és válassza ki a következőt: `Azure Functions: Deploy to Function App...` .
1. Válassza ki az aktuális munkamappát a zip-ben és az üzembe helyezéshez.
1. Válassza ki az előfizetést, majd a meglévő Function app (*myfunctionapp*) nevét. Erősítse meg, hogy felül szeretné írni az előző telepítést.

A függvényalkalmazás létrehozása és a telepítőcsomag alkalmazása után megjelenik egy értesítés. Válassza ki az értesítés **kimenetének megtekintése** lehetőséget a létrehozási és a telepítési eredmények megtekintéséhez, beleértve a frissített Azure-erőforrásokat is.

## <a name="run-the-function-in-azure"></a>A függvény futtatása az Azure-ban

Miután az üzembe helyezés sikeresen befejeződött, szerezze be a függvény URL-címét. Például az **Azure: functions** területen a Visual Studio Code-ban másolja a **HttpTrigger** függvény URL-címét, vagy kérje le a függvény url-címét a [Azure Portal](../azure-functions/functions-get-started.md).

A függvény URL-címe a következőket képezi:

```
https://myfunctionapp.azurewebsites.net/api/HttpTrigger
```

### <a name="run-function-without-passing-a-name"></a>Függvény futtatása a név átadása nélkül

Első tesztként futtassa a parancsot, `curl` és adja át a függvény URL-címét a `name` lekérdezési karakterlánc hozzáfűzése nélkül. 

```bash
curl --verbose "https://myfunctionapp.azurewebsites.net/api/HttpTrigger"
```

A függvény a 200 állapotkódot és a szöveget adja vissza `This HTTP triggered function executed successfully. Pass a name in the query string or in the request body for a personalized response` :

```
[...]
> GET /api/HttpTrigger? HTTP/1.1
> Host: myfunctionapp.azurewebsites.net
> User-Agent: curl/7.64.1
> Accept: */*
> 
* Connection state changed (MAX_CONCURRENT_STREAMS updated)!
< HTTP/1.1 200 OK
< Content-Length: 135
< Content-Type: text/plain; charset=utf-8
< Request-Context: appId=cid-v1:d0bd0123-f713-4579-8990-bb368a229c38
< Date: Wed, 10 Jun 2020 17:50:27 GMT
< 
* Connection #0 to host myfunctionapp.azurewebsites.net left intact
This HTTP triggered function executed successfully. Pass a name in the query string or in the request body for a personalized response.* Closing connection 0
```

### <a name="run-function-and-pass-the-name-of-a-container-group"></a>Futtassa a függvényt, és adja át egy tároló csoport nevét

Most futtassa a `curl` parancsot, és fűzze hozzá a Container Group (*mycontainergroup*) nevét lekérdezési karakterláncként `?name=mycontainergroup` :

```bash
curl --verbose "https://myfunctionapp.azurewebsites.net/api/HttpTrigger?name=mycontainergroup"
```

A függvény a 200 állapotkódot adja vissza, és elindítja a tároló csoport létrehozását:

```
[...]
> GET /api/HttpTrigger1?name=mycontainergroup HTTP/1.1
> Host: myfunctionapp.azurewebsites.net
> User-Agent: curl/7.64.1
> Accept: */*
> 
< HTTP/1.1 200 OK
< Content-Length: 92
< Content-Type: text/plain; charset=utf-8
< Request-Context: appId=cid-v1:d0bd0123-f713-4579-8990-bb368a229c38
< Date: Wed, 10 Jun 2020 17:54:31 GMT
< 
* Connection #0 to host myfunctionapp.azurewebsites.net left intact
This HTTP triggered function executed successfully. Started container group mycontainergroup* Closing connection 0
```

Győződjön meg arról, hogy a tároló a [Get-AzContainerInstanceLog][get-azcontainerinstancelog] paranccsal futott:

```azurecli
Get-AzContainerInstanceLog -ResourceGroupName myfunctionapp `
  -ContainerGroupName mycontainergroup 
```

Példa a kimenetre:

```console
Hello from an Azure container instance triggered by an Azure function
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szüksége az oktatóanyagban létrehozott összes erőforrásra, a [az Group delete] [az-Group-delete] parancs futtatásával távolítsa el az erőforráscsoportot és a benne található összes erőforrást. Ez a parancs törli a létrehozott Function alkalmazást, valamint a futó tárolót és az összes kapcsolódó erőforrást.

```azurecli-interactive
az group delete --name myfunctionapp
```

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban létrehozott egy Azure-függvényt, amely HTTP-kérést végez, és elindítja egy tároló csoport üzembe helyezését. Megtanulta végrehajtani az alábbi műveleteket:

> [!div class="checklist"]
> * A Visual Studio Code és a Azure Functions bővítmény használatával hozzon létre egy alapszintű HTTP-triggeres PowerShell-függvényt.
> * Engedélyezzen egy identitást a Function alkalmazásban, és adja meg az IT-engedélyeket Azure-erőforrások létrehozásához.
> * Módosítsa a PowerShell-függvény kódját az egytárolós tároló-csoportok üzembe helyezésének automatizálásához.
> * Ellenőrizze a tároló HTTP-triggeres telepítését.

A tárolók elindítására és figyelésére vonatkozó részletes példákért tekintse meg az [eseményvezérelt kiszolgáló nélküli tárolók PowerShell-Azure functions és Azure Container instances és a hozzá](https://dev.to/azure/event-driven-serverless-containers-with-powershell-azure-functions-and-azure-container-instances-e9b) [tartozó kódrészletet](https://github.com/anthonychu/functions-powershell-run-aci)ismertető blogbejegyzést.

Az Azure functions létrehozásával és a functions-projektek közzétételével kapcsolatos részletes útmutatásért tekintse meg az [Azure functions dokumentációját](../azure-functions/index.yml) . 

<!-- IMAGES -->


<!-- LINKS - external -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - internal -->
[azure-powershell-install]: /powershell/azure/install-az-ps
[new-azcontainergroup]: /powershell/module/az.containerinstance/new-azcontainergroup
[get-azcontainerinstancelog]: /powershell/module/az.containerinstance/get-azcontainerinstancelog
