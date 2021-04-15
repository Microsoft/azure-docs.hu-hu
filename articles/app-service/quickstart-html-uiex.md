---
title: 'Rövid útmutató: Statikus HTML-webalkalmazás létrehozása'
description: Percek alatt üzembe helyezheti az Hello World HTML Azure App Service kódot. Az üzembe helyezés a Git használatával lehetséges, amely az üzembe helyezés számos App Service.
author: msangapu-msft
ms.assetid: 60495cc5-6963-4bf0-8174-52786d226c26
ms.topic: quickstart
ms.date: 08/23/2019
ms.author: msangapu
ms.custom: mvc, cli-validate, seodec18
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: be555039375e2d71d00bcf8ee58898f54cc3645a
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2021
ms.locfileid: "107379570"
---
# <a name="create-a-static-html-web-app-in-azure"></a>Statikus HTML-webalkalmazás létrehozása az Azure-ban

Ez a rövid útmutató bemutatja, hogyan helyezhet üzembe alapszintű HTML+CSS-webhelyet <abbr title="HTTP-alapú szolgáltatás webalkalmazások, REST API-k és mobil háttéralkalmazások üzemeltetéséhez.">Azure App Service</abbr>. Ezt a rövid útmutatót [](../cloud-shell/overview.md)a következő Cloud Shell fogja végrehajtani, de helyileg is futtathatja ezeket a parancsokat az [Azure CLI-val.](/cli/azure/install-azure-cli)

## <a name="1-prepare-your-environment"></a>1. A környezet előkészítése

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

A [Cloud Shell](../cloud-shell/overview.md)hozzon létre egy gyorsindítási könyvtárat, majd váltsa át a könyvtárra.

```bash
mkdir quickstart

cd $HOME/quickstart
```

Ezután futtassa a következő parancsot a mintaalkalmazás-adattár a quickstart könyvtárba való klónozásához.

```bash
git clone https://github.com/Azure-Samples/html-docs-hello-world.git
```
<hr/>

## <a name="2-create-a-web-app"></a>2. Webalkalmazás létrehozása

Lépjen abba a könyvtárba, amelyben a mintakód található, és futtassa az `az webapp up` parancsot. **Csere** `<app-name>` egy globálisan egyedi névvel.

```bash
cd html-docs-hello-world

az webapp up --location westeurope --name <app_name> --html
```

<details>
<summary>Hibaelhárítás</summary>
<ul>
<li>Ha a parancs nem ismerhető fel, győződjön meg arról, hogy az Azure CLI telepítve van a <code>az</code> <a href="#1-prepare-your-environment">környezet előkészítésével () leírtak szerint.</a></li>
<li>Cserélje le a helyére a teljes Azure-ban egyedi nevet ( érvényes <code>&lt;app-name&gt;</code> <em> karakterek: <code>a-z</code> , és <code>0-9</code> <code>-</code> </em> ). Jó minta lehet a vállalat nevének és egy alkalmazásazonosítónak a kombinációja.</li>
<li>A <code>--sku F1</code> argumentum létrehozza a webalkalmazást az Ingyenes tarifacsomagban. Ezt az argumentumot kihagyva gyorsabb prémium szintet használjon, amely óránkénti költségeket használ.</li>
<li>A argumentum szerint a mappa tartalmát statikus tartalomként kell kezelni, és le kell tiltani a <code>--html</code> build automatizálását.</li>
<li>Igény szerint meg is használhatja a <code>--location &lt;location-name&gt;</code> argumentumot, ahol az egy elérhető <code>&lt;location-name&gt;</code> Azure-régió. Az Azure-fiókhoz elérhető régiók listáját az parancs futtatásával lehet <a href="/cli/azure/appservice#az-appservice-list-locations"> <code>az account list-locations</code> </a> lekérni.</li>
</ul>
</details>

A parancs befejezése eltarthat néhány percig. 

<details>
<summary>Mi a <code>az webapp up</code> baj?</summary>
<p>Az <code>az webapp up</code> parancs a következő műveleteket hajtja végre:</p>
<ul>
<li>Egy alapértelmezett erőforráscsoport létrehozása.</li>
<li>Hozzon létre egy App Service tervet.</li>
<li><a href="/cli/azure/webapp#az-webapp-create">Hozzon létre App Service alkalmazást</a> a megadott névvel.</li>
<li><a href="/azure/app-service/deploy-zip">Tömörítsen fájlokat</a> az aktuális munkakönyvtárból az alkalmazásba.</li>
<li>A futtatás közben üzeneteket biztosít az erőforrások létrehozásáról, a naplózásról és a ZIP üzembe helyezéséről.</li>
</ul>

Amikor befejeződik, az alábbi példához hasonló információkat jelenít meg:

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

</details>

Később szüksége lesz `resourceGroup` az értékre az [erőforrások megtisztítása](#6-clean-up-resources) érdekében.

<hr/>

## <a name="3-browse-to-the-app"></a>3. Tallózással keresse meg az alkalmazást

Egy böngészőben ugrás az alkalmazás URL-címére: `http://<app_name>.azurewebsites.net` .

Az oldal Azure App Service webalkalmazásként fut.

![Mintaalkalmazás kezdőlapja](media/quickstart-html/hello-world-in-browser-az.png)

<hr/>

## <a name="4-update-and-redeploy-the-app"></a>4. Az alkalmazás frissítése és ismételt üzembe állása

A gép Cloud Shell  `nano index.html` gépelje be a nano szövegszerkesztőt. 

A `<h1>` fejléccímkében módosítsa a "Azure App Service - Sample Static HTML Site" (Statikus HTML-mintawebhely) kódot "Azure App Service" névre.

![Nano index.html](media/quickstart-html/nano-index-html.png)

**Mentse** a módosításokat a `^O` paranccsal.

**Lépjen ki a** nano alkalmazásból a paranccsal. `^X`

Az alkalmazás újratelepítése a `az webapp up` paranccsal.

```bash
az webapp up --html
```

Váltson vissza az alkalmazás tallózása lépésben megnyitott **böngészőablakra.**

**Frissítse** az oldalt.

![Frissített mintaalkalmazás kezdőlapja](media/quickstart-html/hello-azure-in-browser-az.png)

<hr/>

## <a name="5-manage-your-new-azure-app"></a>5. Az új Azure-alkalmazás kezelése

**Lépjen** a [Azure Portal](https://portal.azure.com)., 

**Keresse meg** és válassza **a App Services.** 

![A App Services válassza a Azure Portal](./media/quickstart-html/portal0.png)

**Válassza** ki az Azure-alkalmazás nevét.

![Navigálás a portálon egy Azure-alkalmazáshoz](./media/quickstart-html/portal1.png)

Megtekintheti a webalkalmazás Áttekintés oldalát. Itt elvégezhet olyan alapszintű felügyeleti feladatokat, mint a tallózás, leállítás, elindítás, újraindítás és törlés.

![Az App Service panel az Azure Portalon](./media/quickstart-html/portal2.png)

A bal oldali menü az alkalmazás konfigurálásához biztosít különböző oldalakat.

<hr/>

## <a name="6-clean-up-resources"></a>6. Erőforrások tisztítása

Az előző lépésekben Azure-erőforrásokat hozott létre egy erőforráscsoportban. Ha várhatóan nem lesz szüksége ezekre az erőforrásokra a jövőben, törölje az erőforráscsoportot a következő parancs Cloud Shellben történő futtatásával. Ne feledje, hogy az erőforráscsoport neve automatikusan jött létre a [webalkalmazást létrehozó](#2-create-a-web-app) lépés során.

```bash
az group delete --name appsvc_rg_Windows_westeurope
```

A parancs futtatása egy percig is eltarthat.

<hr/>

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Egyéni tartomány leképezése](app-service-web-tutorial-custom-domain-uiex.md)
