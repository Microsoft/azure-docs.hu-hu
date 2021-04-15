---
title: 'Rövid útmutató: Statikus HTML-webalkalmazás létrehozása'
description: Percek alatt üzembe helyezheti az Hello World HTML Azure App Service kódot. Az üzembe helyezést a Git használatával kell üzembe helyezni, amely az üzembe helyezés számos App Service.
author: msangapu-msft
ms.assetid: 60495cc5-6963-4bf0-8174-52786d226c26
ms.topic: quickstart
ms.date: 08/23/2019
ms.author: msangapu
ms.custom: mvc, cli-validate, seodec18
adobe-target: true
adobe-target-activity: DocsExp–386541–A/B–Enhanced-Readability-Quickstarts–2.19.2021
adobe-target-experience: Experience B
adobe-target-content: ./quickstart-html-uiex
ms.openlocfilehash: 0a33889277c9f6cc60f5e81aff520e27828a1d06
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2021
ms.locfileid: "107379621"
---
# <a name="create-a-static-html-web-app-in-azure"></a>Statikus HTML-webalkalmazás létrehozása az Azure-ban

Az [Azure App Service](overview.md) egy hatékonyan méretezhető, önjavító webes üzemeltetési szolgáltatás. Ez a rövid útmutató bemutatja, hogyan helyezhet üzembe alapszintű HTML+CSS-webhelyet a Azure App Service. Ezt a rövid útmutatót [](../cloud-shell/overview.md)a következő Cloud Shell fogja végrehajtani, de helyileg is futtathatja ezeket a parancsokat az [Azure CLI-val.](/cli/azure/install-azure-cli)

![Mintaalkalmazás kezdőlapja](media/quickstart-html/hello-world-in-browser-az.png)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="download-the-sample"></a>A minta letöltése

A Cloud Shellben hozzon létre egy quickstart könyvtárat, és lépjen a könyvtárba.

```bash
mkdir quickstart

cd $HOME/quickstart
```

Ezután futtassa a következő parancsot a mintaalkalmazás-adattár a quickstart könyvtárba való klónozásához.

```bash
git clone https://github.com/Azure-Samples/html-docs-hello-world.git
```

## <a name="create-a-web-app"></a>Webalkalmazás létrehozása

Lépjen abba a könyvtárba, amelyben a mintakód található, és futtassa az `az webapp up` parancsot. A következő példában az <app_name> helyett adjon meg egy egyedi alkalmazásnevet. A statikus tartalmat a jelző `--html` jelzi.

```bash
cd html-docs-hello-world

az webapp up --location westeurope --name <app_name> --html
```

Az `az webapp up` parancs a következő műveleteket hajtja végre:

- Egy alapértelmezett erőforráscsoport létrehozása.

- Egy alapértelmezett App Service-csomag létrehozása.

- Egy alkalmazás létrehozása a megadott néven.

- Fájlok [tömörített üzembe helyezése](./deploy-zip.md) az aktuális munkakönyvtárból a webalkalmazásba.

A parancs futtatása eltarthat néhány percig. Futtatás közben a parancs a következő példához hasonló információkat jelenít meg:

```output
{
  "app_url": "https://&lt;app_name&gt;.azurewebsites.net",
  "location": "westeurope",
  "name": "&lt;app_name&gt;",
  "os": "Windows",
  "resourcegroup": "appsvc_rg_Windows_westeurope",
  "serverfarm": "appsvc_asp_Windows_westeurope",
  "sku": "FREE",
  "src_path": "/home/&lt;username&gt;/quickstart/html-docs-hello-world ",
  &lt; JSON data removed for brevity. &gt;
}
```

Jegyezze fel a `resourceGroup` értékét. Szüksége lesz rá [az erőforrások eltávolításával](#clean-up-resources) kapcsolatos szakaszban.

## <a name="browse-to-the-app"></a>Az alkalmazás megkeresése tallózással

Egy böngészőben ugrás az alkalmazás URL-címére: `http://<app_name>.azurewebsites.net` .

Az oldal Azure App Service webalkalmazásként fut.

![Mintaalkalmazás kezdőlapja](media/quickstart-html/hello-world-in-browser-az.png)

**Gratulálunk!** Elvégezte az első HTML-webapp üzembe helyezését az App Service-ben.

## <a name="update-and-redeploy-the-app"></a>Az alkalmazás frissítése és ismételt üzembe helyezése

A Cloud Shellben írja be a `nano index.html` parancsot a nano szövegszerkesztő megnyitásához. A `<h1>` fejléccímkében az „Azure App Service – Sample Static HTML Site” szöveget módosítsa „Azure App Service”-re a lent látható módon.

![Nano index.html](media/quickstart-html/nano-index-html.png)

Mentse a módosításokat, és lépjen ki a nanóból. A mentéshez a `^O`, a kilépéshez a `^X` parancsot használja.

Most újra üzembe helyezzük az alkalmazást ugyanazzal az `az webapp up` paranccsal.

```bash
az webapp up --location westeurope --name <app_name> --html
```

Az üzembe helyezés befejezését követően váltson vissza **Az alkalmazás megkeresése tallózással** lépésben megnyitott böngészőablakra, és frissítse az oldalt.

![Frissített mintaalkalmazás kezdőlapja](media/quickstart-html/hello-azure-in-browser-az.png)

## <a name="manage-your-new-azure-app"></a>Az új Azure-alkalmazás kezelése

A létrehozott webalkalmazás kezeléséhez az Azure Portal [keresse](https://portal.azure.com)meg és válassza **a** App Services. 

![A App Services válassza a Azure Portal](./media/quickstart-html/portal0.png)

A **App Services** válassza ki az Azure-alkalmazás nevét.

![Navigálás a portálon egy Azure-alkalmazáshoz](./media/quickstart-html/portal1.png)

Megtekintheti a webalkalmazás Áttekintés oldalát. Itt elvégezhet olyan alapszintű felügyeleti feladatokat, mint a tallózás, leállítás, elindítás, újraindítás és törlés.

![Az App Service panel az Azure Portalon](./media/quickstart-html/portal2.png)

A bal oldali menü az alkalmazás konfigurálásához biztosít különböző oldalakat.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Az előző lépésekben Azure-erőforrásokat hozott létre egy erőforráscsoportban. Ha várhatóan nem lesz szüksége ezekre az erőforrásokra a jövőben, törölje az erőforráscsoportot a következő parancs Cloud Shellben történő futtatásával. Ne feledje, hogy az erőforráscsoport neve automatikusan jött létre a [webalkalmazást létrehozó](#create-a-web-app) lépés során.

```bash
az group delete --name appsvc_rg_Windows_westeurope
```

A parancs futtatása egy percig is eltarthat.

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Egyéni tartomány leképezése](app-service-web-tutorial-custom-domain.md)
