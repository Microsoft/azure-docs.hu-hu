---
title: 'Rövid útmutató: Node.js Webalkalmazás létrehozása'
description: Percek alatt üzembe helyezheti az első Node.js Hello World Azure App Service.
ms.assetid: 582bb3c2-164b-42f5-b081-95bfcb7a502a
ms.topic: quickstart
ms.date: 08/01/2020
ms.custom: mvc, devcenter, seodec18
zone_pivot_groups: app-service-platform-windows-linux
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 7cd9add699d9bd4a3eff9cdcf1e65af0d754b70a
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2021
ms.locfileid: "101748608"
---
# <a name="create-a-nodejs-web-app-in-azure"></a>Node.js-webalkalmazás létrehozása az Azure-ban

Első lépések <abbr title="HTTP-alapú szolgáltatás webes alkalmazások, REST API-k és mobil háttérbeli alkalmazások üzemeltetéséhez.">Azure App Service</abbr> Ha a Visual Studio Code használatával helyileg létrehoz egy Node.js/Express alkalmazást, majd üzembe helyezi az alkalmazást az Azure-felhőben. Mivel a <abbr title="Azure App Service egy alapszintű, amelyben az alkalmazás ugyanazon a virtuális gépeken fut, mint a többi alkalmazás, beleértve a más ügyfelek alkalmazásait is. Ez a platform fejlesztési és tesztelési célokra szolgál.">ingyenes szintű</abbr>a rövid útmutató elvégzéséhez nem jár költséggel.

## <a name="1-prepare-your-environment"></a>1. a környezet előkészítése

- Aktív Azure-fiók <abbr title="Az Azure-előfizetés az erőforrások kiosztására használt logikai tároló a Azure-ban. Az összes erőforrás (például virtuális gépek, adatbázisok stb.) adatait tárolja.">előfizetést</abbr>. [Hozzon létre egy fiókot ingyenesen](https://azure.microsoft.com/free/?utm_source=campaign&utm_campaign=vscode-tutorial-app-service-extension&mktingSource=vscode-tutorial-app-service-extension).
- <a href="https://git-scm.com/" target="_blank">A Git telepítése</a>
- [Node.js és NPM](https://nodejs.org). A parancs futtatásával `node --version` ellenőrizze, hogy telepítve van-e a Node.js.
- [Visual Studio Code](https://code.visualstudio.com/).
- A Visual Studio Code [Azure app Service bővítménye](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azureappservice) .

[Probléma bejelentése](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azure-app-service&prepare-your-environment)




<br>
<hr/>

## <a name="2-clone-and-run-a-local-nodejs-application"></a>2. helyi Node.js alkalmazás klónozása és futtatása

1. A helyi számítógépen nyisson meg egy terminált, és klónozással nyissa meg a minta tárházat:

    ```bash
    git clone https://github.com/Azure-Samples/nodejs-docs-hello-world
    ```

1. Navigáljon az új alkalmazás mappájába:

    ```bash
    cd nodejs-docs-hello-world
    ```

1. A függőségek telepítése:

    ```bash
    npm install
    ```

1. Az alkalmazás elindítása helyi teszteléshez:

    ```bash
    npm start
    ```
    
1. Nyissa meg a böngészőt, és navigáljon a gombra `http://localhost:1337` . A böngészőben a következő üzenetnek kell megjelennie: "Hello World!".

1. A kiszolgáló leállításához nyomja le a <kbd>CTRL C billentyűt</kbd>  +  <kbd></kbd> a terminálon.

[Probléma bejelentése](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azure-app-service&prepare-your-environment)


<br>
<hr/>




<!-- VS Code extension works differently for Windows/Linus - Step 3 -->

::: zone pivot="platform-windows"  

[!INCLUDE [Windows](./includes/quickstart-nodejs-uiex-windows.md)]


::: zone-end

::: zone pivot="platform-linux"  

[!INCLUDE [Windows](./includes/quickstart-nodejs-uiex-linux.md)]

::: zone-end


## <a name="4-viewing-logs-from-visual-studio-code"></a>4. a naplók megtekintése a Visual Studio Code-ból

Tekintse meg a <abbr title="Az `console.log` alkalmazásban megjelenő hívások a Visual Studio Code kimenet ablakában jelennek meg.">napló</abbr> a futó App Service alkalmazás.

1. Keresse meg az alkalmazást az **Azure app Service** Explorerben, kattintson a jobb gombbal az alkalmazás nevére, és válassza a **streaming naplók indítása** lehetőséget.

1. Megnyílik a Visual Studio Code kimenet ablaka.

    ![Folyamatos átviteli naplók megtekintése](./media/quickstart-nodejs/view-logs.png)

    :::image type="content" source="./media/quickstart-nodejs/enable-restart.png" alt-text="Képernyőfelvétel a VS Code parancssorról a fájlok naplózásának engedélyezéséhez és a webalkalmazás újraindításához az igen gomb kiválasztásával.":::

1. Néhány másodperc elteltével megjelenik egy üzenet, amely jelzi, hogy csatlakozik a log-streaming szolgáltatáshoz. 
1. Frissítse az oldalt néhányszor, hogy láthassa a tevékenységeket.

    <pre class="is-monospace is-size-small has-padding-medium has-background-tertiary has-text-tertiary-invert">
    2020-09-20 20:37:39.574 INFO  - Initiating warmup request to container msdocs-vscode-node_2_00ac292a for site msdocs-vscode-node
    2020-09-20 20:37:55.011 INFO  - Waiting for response to warmup request for container msdocs-vscode-node_2_00ac292a. Elapsed time = 15.4373071 sec
    2020-09-20 20:38:08.233 INFO  - Container msdocs-vscode-node_2_00ac292a for site msdocs-vscode-node initialized successfully and is ready to serve requests.
    2020-09-20T20:38:21  Startup Request, url: /Default.cshtml, method: GET, type: request, pid: 61,1,7, SCM_SKIP_SSL_VALIDATION: 0, SCM_BIN_PATH: /opt/Kudu/bin, ScmType: None
    </pre>

<br>

[Probléma bejelentése](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azure-app-service&prepare-your-environment)

<br>
<hr/>

## <a name="5-clean-up-resources"></a>5. erőforrások tisztítása

Keresse meg az alkalmazást az **Azure app Service** Explorerben, kattintson a jobb gombbal az alkalmazás nevére, és válassza a **Törlés** lehetőséget. 

:::image type="content" source="./media/quickstart-nodejs/delete-resource-ieux.png" alt-text="Keresse meg az alkalmazást a &quot;* *&quot; AZURE APP SERVICE &quot;* *&quot; Explorerben, kattintson a jobb gombbal az alkalmazás nevére, és válassza a &quot;Törlés&quot; lehetőséget.":::

## <a name="next-steps"></a>Következő lépések

Gratulálunk, sikeresen elvégezte ezt a rövid útmutatót! Az alkalmazás módosításait ugyanazzal a folyamattal telepítheti, és a meglévő alkalmazást is kiválaszthatja, és nem hozhat létre újat.

Ezután tekintse meg a többi Azure-bővítményt.

* [Cosmos DB](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-cosmosdb)
* [Azure Functions](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions)
* [Docker-eszközök](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker)
* [Azure CLI-eszközök](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azurecli)
* [Eszközök Azure Resource Manager](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)

Az Azure Extension Packhez készült [Node Pack](https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack) telepítésével vagy az összes beszerzésével.