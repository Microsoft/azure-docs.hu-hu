---
title: 'Rövid útmutató: Node.js Webalkalmazás létrehozása'
description: Percek alatt üzembe helyezheti az első Node.js Hello World Azure App Service.
ms.assetid: 582bb3c2-164b-42f5-b081-95bfcb7a502a
ms.topic: quickstart
ms.date: 08/01/2020
ms.custom: mvc, devcenter, seodec18
zone_pivot_groups: app-service-platform-windows-linux
adobe-target: true
adobe-target-activity: DocsExp–386541–A/B–Enhanced-Readability-Quickstarts–2.19.2021
adobe-target-experience: Experience B
adobe-target-content: ./quickstart-nodejs-uiex
ms.openlocfilehash: 3b1ff02d6a9b3e8c7b7abaf1b185a9becd2be12b
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/08/2021
ms.locfileid: "107105833"
---
# <a name="create-a-nodejs-web-app-in-azure"></a>Node.js-webalkalmazás létrehozása az Azure-ban

::: zone pivot="platform-windows"  

Ismerkedjen meg Azure App Service azáltal, hogy a Visual Studio Code használatával helyileg létrehoz egy Node.js/Express alkalmazást, majd üzembe helyezi az alkalmazást a felhőben. Mivel ingyenes App Service szintet használ, a rövid útmutató elvégzéséhez nem jár költséggel.

## <a name="prerequisites"></a>Előfeltételek

- Aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egy fiókot ingyenesen](https://azure.microsoft.com/free/?utm_source=campaign&utm_campaign=vscode-tutorial-app-service-extension&mktingSource=vscode-tutorial-app-service-extension).
- <a href="https://git-scm.com/" target="_blank">A Git telepítése</a>
- [Node.js és NPM](https://nodejs.org). A parancs futtatásával `node --version` ellenőrizze, hogy telepítve van-e a Node.js.
- [Visual Studio Code](https://code.visualstudio.com/).
- A Visual Studio Code [Azure app Service bővítménye](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azureappservice) .

## <a name="clone-and-run-a-local-nodejs-application"></a>Helyi Node.js alkalmazás klónozása és futtatása

1. A helyi számítógépen nyisson meg egy terminált, és klónozással nyissa meg a minta tárházat:

    ```bash
    git clone https://github.com/Azure-Samples/nodejs-docs-hello-world
    ```

1. Navigáljon az új alkalmazás mappájába:

    ```bash
    cd nodejs-docs-hello-world
    ```

1. Az alkalmazás elindítása helyi teszteléshez:

    ```bash
    npm start
    ```
    
1. Nyissa meg a böngészőt, és navigáljon a gombra `http://localhost:1337` . A böngészőben a következő üzenetnek kell megjelennie: "Hello World!".

1. A kiszolgáló leállításához nyomja le a **CTRL C billentyűt** +  a terminálon.

> [!div class="nextstepaction"]
> [Egy hibába ütközött](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azure-app-service&step=create-app)

## <a name="deploy-the-app-to-azure"></a>Az alkalmazás üzembe helyezése az Azure-ban

Ebben a szakaszban az Node.js alkalmazást a VS Code és a Azure App Service bővítmény használatával helyezi üzembe az Azure-ban.

1. Győződjön meg arról, hogy a terminálon a *NodeJS-docs-Hello-World* mappában van, majd indítsa el a Visual Studio Code-ot a következő paranccsal:

    ```bash
    code .
    ```

1. A VS Code tevékenység sávján válassza ki az Azure-emblémát az **Azure app Service** Explorer megjelenítéséhez. Válassza a bejelentkezés az Azure-ba **...** lehetőséget, és kövesse az utasításokat. (Lásd: [Az Azure-bejelentkezés hibaelhárítása](#troubleshooting-azure-sign-in) , ha hibákat futtat.) A bejelentkezést követően a tallózónak meg kell jelennie az Azure-előfizetés nevének.

    ![Bejelentkezés az Azure-ba](media/quickstart-nodejs/sign-in.png)

1. Az **Azure app Service** EXPLORERBEN a vs Code-ban kattintson a kék felfelé mutató nyíl ikonra az alkalmazás üzembe helyezéséhez az Azure-ban. (Ugyanezt a parancsot a **parancssorból** is meghívhatja (**CTRL** + **SHIFT** + **P**) a "üzembe helyezés a webalkalmazásba" parancs beírásával, és a **Azure app Service: üzembe helyezés a webalkalmazásban**) lehetőségre.

    :::image type="content" source="media/quickstart-nodejs/deploy.png" alt-text="Képernyőkép az Azure app Service-ről a VS Code-ban, amelyen a kék nyíl ikon van kiválasztva.":::
        
1. Válassza ki a *NodeJS-docs-Hello-World* mappát.

1. Válasszon egy létrehozási lehetőséget azon operációs rendszer alapján, amelyre telepíteni kívánja a következőt:

    - Linux: válassza az **új Webalkalmazás létrehozása** lehetőséget.
    - Windows: válassza az **új Webalkalmazás létrehozása... lehetőséget. Speciális**

1. Írjon be egy globálisan egyedi nevet a webalkalmazásnak, és nyomja le az **ENTER** billentyűt. A névnek egyedinek kell lennie az összes Azure-ban, és csak alfanumerikus karaktereket ("A-Z", "a-z" és "0-9") és kötőjeleket ("-") kell használnia.

1. A Linux megcélzása esetén válassza ki a Node.js verziót, amikor a rendszer kéri. Az **LTS** -verzió használata javasolt.

1. Ha a Windowst célozza meg, kövesse a további utasításokat:
    1. Válassza az **Új erőforráscsoport létrehozása** lehetőséget, majd adja meg az erőforráscsoport nevét, például: `AppServiceQS-rg` .
    1. Válassza a **Windows** lehetőséget az operációs rendszer számára.
    1. Válassza az **új App Service terv létrehozása** lehetőséget, majd adja meg a csomag nevét (például `AppServiceQS-plan` ), majd válassza az **ingyenes F1** lehetőséget a díjszabási szinten.
    1. Válassza **a Kihagyás lehetőséget,** amikor a rendszer rákérdez a Application Insightsra.
    1. Válasszon egy Önhöz közeli régiót vagy az elérni kívánt erőforrások közelében.

1. Az összes kérésre való válaszadás után a VS Code megjeleníti az alkalmazáshoz létrehozott Azure-erőforrásokat az értesítési felugró ablakban.

    Linux rendszeren való üzembe helyezéskor válassza az **Igen** lehetőséget, ha a rendszer felszólítja, hogy frissítse a konfigurációt `npm install` a cél Linux-kiszolgálón való futtatásra.

    ![Rákérdezés a konfiguráció frissítésére a cél Linux-kiszolgálón](media/quickstart-nodejs/server-build.png)

1. Ha a rendszer kéri, válassza az **Igen** lehetőséget, és **mindig telepítse a "NodeJS-docs-Hello-World" munkaterületet az (alkalmazás neve)** elemre. Ha az **Igen** lehetőséget választja, a vs Code automatikusan ugyanazt a app Service webalkalmazást célozza meg a későbbi központi telepítésekkel.

1. Ha Linux rendszerre telepíti a rendszert, válassza a **Tallózás webhely** lehetőséget a kérdésben, hogy a telepítés befejezése után megtekintse a frissen telepített webalkalmazást. A böngészőben meg kell jelennie a következőnek: "Hello World!"

1. Ha Windows rendszeren telepíti a rendszert, először be kell állítania a Node.js verziószámát a webalkalmazáshoz:

    1. A VS Code-ban bontsa ki a csomópontot az új App Service-hez, kattintson a jobb gombbal az **Alkalmazásbeállítások** elemre, majd válassza az **új beállítás hozzáadása lehetőséget...**:

        ![Alkalmazás-beállítási parancs hozzáadása](media/quickstart-nodejs/add-setting.png)

    1. Adja meg `WEBSITE_NODE_DEFAULT_VERSION` a beállítás kulcsát.
    1. Adja meg `10.15.2` a beállítás értékét.
    1. Kattintson a jobb gombbal az App Service-csomópontra, és válassza az **Újraindítás** lehetőséget.

        ![App Service-parancs újraindítása](media/quickstart-nodejs/restart.png)

    1. Kattintson a jobb gombbal az App Service-csomópontra, és válassza a **Tallózás webhely** lehetőséget.

> [!div class="nextstepaction"]
> [Egy hibába ütközött](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azure-app-service&step=deploy-app)

### <a name="troubleshooting-azure-sign-in"></a>Azure-bejelentkezés hibaelhárítása

Ha az Azure-ba való bejelentkezéskor a **"nem található az előfizetés neve [előfizetés-azonosító]"** hibaüzenet jelenik meg, annak oka az lehet, hogy a proxy mögött van, és nem érhető el az Azure API. Konfigurálja a `HTTP_PROXY` és a `HTTPS_PROXY` környezeti változókat a saját proxy-adataival a használatával `export` .

```bash
export HTTPS_PROXY=https://username:password@proxy:8080
export HTTP_PROXY=http://username:password@proxy:8080
```

Ha a környezeti változók beállítása nem javítsa ki a problémát, vegye fel velünk a kapcsolatot, ha a fenti **probléma** gombra kattintott.

### <a name="update-the-app"></a>Az alkalmazás frissítése

Az alkalmazás módosításait úgy helyezheti üzembe, hogy a VS Code-ban szerkeszti a módosításokat, majd menti a fájlokat, majd ugyanazzal a folyamattal használja, mint a meglévő alkalmazás kiválasztását, és nem hoz létre újat.

## <a name="viewing-logs"></a>Naplók megtekintése

Az `console.log` alkalmazásból közvetlenül a vs Code kimenet ablakban megtekintheti a napló kimenetét (meghívások).

1. Az **Azure app Service** Explorerben kattintson a jobb gombbal az alkalmazás csomópontjára, és válassza a **streaming naplók indítása** lehetőséget.

    ![Adatfolyam-naplók indítása](media/quickstart-nodejs/view-logs.png)

1. Ha a rendszer kéri, válassza a naplózás engedélyezését, és indítsa újra az alkalmazást. Az alkalmazás újraindítása után a VS Code kimenet ablak megnyílik a log streamtel létesített kapcsolatban. 

    :::image type="content" source="media/quickstart-nodejs/enable-restart.png" alt-text="Képernyőfelvétel a Visual Studio Code-ról a naplózás engedélyezéséhez és az alkalmazás újraindításához az igen gomb kiválasztásával.":::

1. Néhány másodperc elteltével a kimeneti ablak egy üzenetet jelenít meg, amely jelzi, hogy csatlakozik a log-streaming szolgáltatáshoz. A böngészőben az oldal frissítésével több kimeneti tevékenység is létrehozható.

    <pre>
    Connecting to log stream...
    2020-03-04T19:29:44  Welcome, you are now connected to log-streaming service. The default timeout is 2 hours.
    Change the timeout with the App Setting SCM_LOGSTREAM_TIMEOUT (in seconds).
    </pre>

> [!div class="nextstepaction"]
> [Egy hibába ütközött](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azure-app-service&step=tailing-logs)

## <a name="next-steps"></a>Következő lépések

Gratulálunk, sikeresen elvégezte ezt a rövid útmutatót!

> [!div class="nextstepaction"]
> [Oktatóanyag: Node.js alkalmazás a MongoDB](tutorial-nodejs-mongodb-app.md)

> [!div class="nextstepaction"]
> [Node.js alkalmazás konfigurálása](configure-language-nodejs.md)

Tekintse meg a többi Azure-bővítményt.

* [Cosmos DB](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-cosmosdb)
* [Azure Functions](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions)
* [Docker-eszközök](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker)
* [Azure CLI-eszközök](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azurecli)
* [Eszközök Azure Resource Manager](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)

Az Azure Extension Packhez készült [Node Pack](https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack) telepítésével vagy az összes beszerzésével.
::: zone-end

::: zone pivot="platform-linux"  
## <a name="prerequisites"></a>Előfeltételek

Ha még nem rendelkezik Azure-fiókkal, [Regisztráljon még ma](https://azure.microsoft.com/free/?utm_source=campaign&utm_campaign=vscode-tutorial-app-service-extension&mktingSource=vscode-tutorial-app-service-extension) az Azure-kreditek $200-es ingyenes fiókjával, hogy kipróbálja a szolgáltatások bármilyen kombinációját.

A [Visual Studio Code](https://code.visualstudio.com/) -ot a [Node.js és a npm](https://nodejs.org/en/download), a Node.js Package Managerrel együtt kell telepíteni.

Emellett telepítenie kell a [Azure app Service bővítményt](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azureappservice)is, amellyel Linux-web Appseket hozhat létre, kezelhet és helyezhet üzembe az Azure platform szolgáltatásként (Péter).

### <a name="sign-in"></a>Bejelentkezés

Miután telepítette a bővítményt, jelentkezzen be az Azure-fiókjába. A tevékenység sávján válassza ki az Azure-emblémát az **Azure app Service** Explorer megjelenítéséhez. Válassza a bejelentkezés az Azure-ba **...** lehetőséget, és kövesse az utasításokat.

![Bejelentkezés az Azure-ba](./media/quickstart-nodejs/sign-in.png)

### <a name="troubleshooting"></a>Hibaelhárítás

Ha a következő hibaüzenet jelenik meg: **"nem található az előfizetés a (z) [előfizetés-azonosítóval]" névvel**, lehetséges, hogy a proxy mögött van, és nem érhető el az Azure API. Konfigurálja a `HTTP_PROXY` és a `HTTPS_PROXY` környezeti változókat a saját proxy-adataival a használatával `export` .

```sh
export HTTPS_PROXY=https://username:password@proxy:8080
export HTTP_PROXY=http://username:password@proxy:8080
```

Ha a környezeti változók beállítása nem javítsa ki a problémát, vegye fel velünk a kapcsolatot az alábbi, a **probléma elhárítása** gombra kattintva.

### <a name="prerequisite-check"></a>Előfeltételek ellenőrzése

A folytatás előtt győződjön meg arról, hogy az összes előfeltétel telepítve és konfigurálva van.

A VS Code-ban az Azure **app Service** Explorerben tekintse meg az Azure-beli e-mail-címét az állapotjelző sávban és az előfizetésében.

> [!div class="nextstepaction"]
> [Egy hibába ütközött](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azure-app-service&step=getting-started)

## <a name="create-your-nodejs-application"></a>A Node.js-alkalmazás létrehozása

Ezután hozzon létre egy Node.js alkalmazást, amely üzembe helyezhető a felhőben. Ez a rövid útmutató egy alkalmazás-generátort használ az alkalmazás egy terminálból való gyors előkészítéséhez.

> [!TIP]
> Ha már elvégezte a [Node.js oktatóanyagot](https://code.visualstudio.com/docs/nodejs/nodejs-tutorial), ugorjon az Azure-ba való [üzembe helyezésre](#deploy-to-azure).

### <a name="scaffold-a-new-application-with-the-express-generator"></a>Új alkalmazás készítése az expressz generátorral

Az [Express](https://www.expressjs.com) egy népszerű keretrendszer Node.js alkalmazások létrehozásához és futtatásához. Az [Express Generator](https://expressjs.com/en/starter/generator.html) Tool használatával új expressz alkalmazást készíthet (létrehozhatja). Az expressz generátort NPM modulként szállítjuk, és a NPM parancssori eszköz használatával közvetlenül (telepítés nélkül) futtathatók `npx` .

```bash
npx express-generator myExpressApp --view pug --git
```

A `--view pug --git` Paraméterek közlik a létrehozóval, hogy a [mopsz](https://pugjs.org/api/getting-started.html) -sablon motorját (korábbi nevén: `jade` ) és egy fájl létrehozásához használja `.gitignore` .

Az alkalmazás összes függőségének telepítéséhez nyissa meg az új mappát, és futtassa a parancsot `npm install` .

```bash
cd myExpressApp
npm install
```

### <a name="run-the-application"></a>Az alkalmazás futtatása

Ezután győződjön meg arról, hogy az alkalmazás fut. A terminálból indítsa el az alkalmazást a parancs használatával a `npm start` kiszolgáló indításához.

```bash
npm start
```

Most nyissa meg a böngészőt, és lépjen a címre `http://localhost:3000` , ahol a következőhöz hasonlónak kell megjelennie:

![Expressz alkalmazás futtatása](./media/quickstart-nodejs/express.png)

> [!div class="nextstepaction"]
> [Egy hibába ütközött](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azure-app-service&step=create-app)

## <a name="deploy-to-azure"></a>Üzembe helyezés az Azure-ban

Ebben a szakaszban az Node.js alkalmazást a VS Code és a Azure App Service bővítmény használatával helyezi üzembe. Ez a rövid útmutató a legalapvetőbb üzembe helyezési modellt használja, amelyben az alkalmazás tömörített és üzembe helyezése egy Azure Web App on Linux.

### <a name="deploy-using-azure-app-service"></a>Üzembe helyezés Azure App Service használatával

Először nyissa meg az alkalmazás mappáját a VS Code-ban.

```bash
code .
```

Az **Azure app Service** Explorerben válassza a kék felfelé mutató nyíl ikont az alkalmazás üzembe helyezéséhez az Azure-ban.

:::image type="content" source="./media/quickstart-nodejs/deploy.png" alt-text="Képernyőfelvétel a Visual Studio Code Azure App Service menüjéről a kiválasztott kék üzembe helyezési nyíllal.":::

> [!TIP]
> A **parancssorból** (CTRL + SHIFT + P) is üzembe helyezheti az "üzembe helyezés a webalkalmazásba" parancsot, és futtathatja a **Azure app Service: Deploy to Web App** paranccsal.

1. Válassza ki a jelenleg megnyitott könyvtárat `myExpressApp` .

1. Válassza a **Create New Web App (új Webalkalmazás létrehozása**) lehetőséget, amely alapértelmezés szerint a Linux rendszeren app Service.

1. Írjon be egy globálisan egyedi nevet a webalkalmazásnak, és nyomja le az ENTER billentyűt. Az alkalmazás nevének érvényes karaktereinek neve: "a-z", "0-9" és "-".

1. Válassza ki a **Node.js verzióját**, az LTS használata javasolt.

    Az értesítési csatorna az alkalmazáshoz létrehozott Azure-erőforrásokat jeleníti meg.

1. Válassza az **Igen** lehetőséget, ha a rendszer felszólítja, hogy frissítse a konfigurációt a célkiszolgálón való futtatásra `npm install` . Ezután üzembe helyezi az alkalmazást.

    :::image type="content" source="./media/quickstart-nodejs/server-build.png" alt-text="Képernyőkép a konfiguráció frissítéséről a célkiszolgálón az igen gomb kiválasztásával.":::

1. Ha elindul az üzembe helyezés, a rendszer kéri, hogy frissítse a munkaterületet, hogy a későbbi központi telepítések automatikusan ugyanazt a App Service webalkalmazást célozzák meg. Válassza az **Igen** lehetőséget, hogy a módosítások a megfelelő alkalmazásra legyenek telepítve.

    :::image type="content" source="./media/quickstart-nodejs/save-configuration.png" alt-text="Képernyőkép a munkaterület frissítéséről a kiválasztott igen gombbal.":::

> [!TIP]
> Győződjön meg arról, hogy az alkalmazás figyeli a PORT környezeti változó által biztosított portot: `process.env.PORT` .

### <a name="browse-the-app-in-azure"></a>Az alkalmazás tallózása az Azure-ban

Miután az üzembe helyezés befejeződött, a kérdésben válassza a **Tallózás webhely** lehetőséget a frissen telepített webalkalmazás megtekintéséhez.

### <a name="troubleshooting"></a>Hibaelhárítás

Ha a következő hibaüzenet jelenik meg: **"nincs engedélye a könyvtár vagy lap megtekintésére."**, akkor az alkalmazás valószínűleg nem indult el megfelelően. Lépjen a következő szakaszra, és tekintse meg a napló kimenetét, és javítsa ki a hibát. Ha nem tudja kijavítani a problémát, lépjen kapcsolatba velünk az alábbi, a **probléma** elhárítása gombra kattintva. Örömmel segítünk!

> [!div class="nextstepaction"]
> [Egy hibába ütközött](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azure-app-service&step=deploy-app)

### <a name="update-the-app"></a>Az alkalmazás frissítése

Az alkalmazás módosításait ugyanazzal a folyamattal telepítheti, és a meglévő alkalmazást is kiválaszthatja, és nem hozhat létre újat.

## <a name="viewing-logs"></a>Naplók megtekintése

Ebből a szakaszból megtudhatja, hogyan tekintheti meg (vagy "farok") a naplókat a futó App Service alkalmazásból. Az `console.log` alkalmazásban megjelenő hívások a Visual Studio Code kimenet ablakában jelennek meg.

Keresse meg az alkalmazást az **Azure app Service** Explorerben, kattintson a jobb gombbal az alkalmazásra, majd válassza a **streaming naplók indítása** lehetőséget.

Megnyílik a VS Code kimenet ablak a log streamtel létesített kapcsolatban.

![Adatfolyam-naplók indítása](./media/quickstart-nodejs/view-logs.png)

:::image type="content" source="./media/quickstart-nodejs/enable-restart.png" alt-text="Képernyőfelvétel a VS Code parancssorról a fájlok naplózásának engedélyezéséhez és a webalkalmazás újraindításához az igen gomb kiválasztásával.":::

Néhány másodperc elteltével megjelenik egy üzenet, amely jelzi, hogy csatlakozik a log-streaming szolgáltatáshoz. Frissítse az oldalt néhányszor, hogy láthassa a tevékenységeket.

<pre>
2019-09-20 20:37:39.574 INFO  - Initiating warmup request to container msdocs-vscode-node_2_00ac292a for site msdocs-vscode-node
2019-09-20 20:37:55.011 INFO  - Waiting for response to warmup request for container msdocs-vscode-node_2_00ac292a. Elapsed time = 15.4373071 sec
2019-09-20 20:38:08.233 INFO  - Container msdocs-vscode-node_2_00ac292a for site msdocs-vscode-node initialized successfully and is ready to serve requests.
2019-09-20T20:38:21  Startup Request, url: /Default.cshtml, method: GET, type: request, pid: 61,1,7, SCM_SKIP_SSL_VALIDATION: 0, SCM_BIN_PATH: /opt/Kudu/bin, ScmType: None
</pre>

> [!div class="nextstepaction"]
> [Egy hibába ütközött](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azure-app-service&step=tailing-logs)

## <a name="next-steps"></a>Következő lépések

Gratulálunk, sikeresen elvégezte ezt a rövid útmutatót!

Ezután tekintse meg a többi Azure-bővítményt.

* [Cosmos DB](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-cosmosdb)
* [Azure Functions](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions)
* [Docker-eszközök](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker)
* [Azure CLI-eszközök](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azurecli)
* [Eszközök Azure Resource Manager](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)

Az Azure Extension Packhez készült [Node Pack](https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack) telepítésével vagy az összes beszerzésével.


::: zone-end
