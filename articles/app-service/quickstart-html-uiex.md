---
title: 'Gyors útmutató: statikus HTML-Webalkalmazás létrehozása'
description: Percek alatt üzembe helyezheti az első HTML-Hello World Azure App Service. Az üzembe helyezés a git használatával történik, amely a App Service üzembe helyezésének számos módszerének egyike.
author: msangapu-msft
ms.assetid: 60495cc5-6963-4bf0-8174-52786d226c26
ms.topic: quickstart
ms.date: 08/23/2019
ms.author: msangapu
ms.custom: mvc, cli-validate, seodec18, devx-track-azurecli
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 538d414ce606b944fcea7adbb1c817386e13090e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102178575"
---
# <a name="create-a-static-html-web-app-in-azure"></a>Statikus HTML-webalkalmazás létrehozása az Azure-ban

Ez a rövid útmutató bemutatja, hogyan helyezhet üzembe egy alapszintű HTML + CSS-helyet a <abbr title="HTTP-alapú szolgáltatás webes alkalmazások, REST API-k és mobil háttérbeli alkalmazások üzemeltetéséhez.">Azure App Service</abbr>. Ez a rövid útmutató [Cloud Shellban](../cloud-shell/overview.md)is elvégezhető, de ezeket a parancsokat helyileg is futtathatja az [Azure CLI](/cli/azure/install-azure-cli)-vel.

## <a name="1-prepare-your-environment"></a>1. a környezet előkészítése

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

A [Cloud Shellban](../cloud-shell/overview.md)hozzon létre egy gyors üzembe helyezési könyvtárat, majd váltson rá.

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

Lépjen abba a könyvtárba, amelyben a mintakód található, és futtassa az `az webapp up` parancsot. **Cserélje le** `<app-name>` globálisan egyedi névvel.

```bash
cd html-docs-hello-world

az webapp up --location westeurope --name <app_name> --html
```

<details>
<summary>Hibaelhárítás</summary>
<ul>
<li>Ha a <code>az</code> parancs nem ismerhető fel, győződjön meg arról, hogy az Azure CLI telepítve van a <a href="#1-prepare-your-environment">környezet előkészítése</a>című témakörben leírtak szerint.</li>
<li>Cserélje le a <code>&lt;app-name&gt;</code> karaktert az összes Azure-beli egyedi névre ( <em> érvényes karakterek: <code>a-z</code> , <code>0-9</code> és <code>-</code> </em> ). Jó példa a vállalat nevének és az alkalmazás-azonosító kombinációjának használatára.</li>
<li>Az <code>--sku F1</code> argumentum a webalkalmazást az ingyenes díjszabási szinten hozza létre. Hagyja ki ezt az argumentumot a gyorsabb prémium szint használatához, amely óradíjat eredményez.</li>
<li>Az <code>--html</code> argumentum azt mondja, hogy az összes mappa tartalmát statikus tartalomként kezeli, és letiltja a Build automationt.</li>
<li>Igény szerint megadhatja az argumentumot <code>--location &lt;location-name&gt;</code> , ahol az <code>&lt;location-name&gt;</code> egy elérhető Azure-régió. Az Azure-fiók számára engedélyezett régiók listáját a parancs futtatásával kérheti le <a href="/cli/azure/appservice#az-appservice-list-locations"> <code>az account list-locations</code> </a> .</li>
</ul>
</details>

A parancs végrehajtása több percet is igénybe vehet. 

<details>
<summary>Mi történik <code>az webapp up</code> ?</summary>
<p>Az <code>az webapp up</code> parancs a következő műveleteket hajtja végre:</p>
<ul>
<li>Egy alapértelmezett erőforráscsoport létrehozása.</li>
<li>Hozzon létre egy alapértelmezett App Service csomagot.</li>
<li><a href="/cli/azure/webapp#az-webapp-create">Hozzon létre egy app Service alkalmazást</a> a megadott névvel.</li>
<li>A <a href="/azure/app-service/deploy-zip">zip telepíti</a> a fájlokat az aktuális munkakönyvtárból az alkalmazásba.</li>
<li>A futtatása közben üzeneteket biztosít az erőforrás-létrehozással, a naplózással és a ZIP-telepítéssel kapcsolatban.</li>
</ul>

Amikor befejeződik, a következő példához hasonló információkat jelenít meg:

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

Az erőforrások későbbi törléséhez szüksége lesz az `resourceGroup` értékre. [](#6-clean-up-resources)

<hr/>

## <a name="3-browse-to-the-app"></a>3. tallózással keresse meg az alkalmazást

A böngészőben nyissa meg az alkalmazás URL-címét: `http://<app_name>.azurewebsites.net` .

Az oldal Azure App Service webalkalmazásként fut.

![Mintaalkalmazás kezdőlapja](media/quickstart-html/hello-world-in-browser-az.png)

<hr/>

## <a name="4-update-and-redeploy-the-app"></a>4. az alkalmazás frissítése és újbóli üzembe helyezése

A Cloud Shell **írja be** `nano index.html` a nano Text Editor megnyitásához. 

A `<h1>` fejléc címkéjén módosítsa a "Azure app Service-Sample statikus HTML-hely" kifejezést a "Azure app Service" értékre.

![Nano index.html](media/quickstart-html/nano-index-html.png)

**Mentse** a módosításokat a parancs használatával `^O` .

**Lépjen ki** a nano parancs használatával `^X` .

Telepítse újra az alkalmazást a `az webapp up` paranccsal.

```bash
az webapp up --html
```

Váltson vissza arra a böngészőablakra, amely megnyílik a **Tallózás az alkalmazáshoz** lépéshez.

**Frissítse** az oldalt.

![Frissített mintaalkalmazás kezdőlapja](media/quickstart-html/hello-azure-in-browser-az.png)

<hr/>

## <a name="5-manage-your-new-azure-app"></a>5. az új Azure-alkalmazás kezelése

**Navigáljon** a [Azure Portal](https://portal.azure.com). 

**Keresse** meg és **válassza ki** a **app Services**.

![Válassza ki a App Services a Azure Portal](./media/quickstart-html/portal0.png)

**Válassza ki** az Azure-alkalmazás nevét.

![Navigálás a portálon egy Azure-alkalmazáshoz](./media/quickstart-html/portal1.png)

Megtekintheti a webalkalmazás Áttekintés oldalát. Itt elvégezhet olyan alapszintű felügyeleti feladatokat, mint a tallózás, leállítás, elindítás, újraindítás és törlés.

![Az App Service panel az Azure Portalon](./media/quickstart-html/portal2.png)

A bal oldali menü az alkalmazás konfigurálásához biztosít különböző oldalakat.

<hr/>

## <a name="6-clean-up-resources"></a>6. erőforrások törlése

Az előző lépésekben Azure-erőforrásokat hozott létre egy erőforráscsoportban. Ha várhatóan nem lesz szüksége ezekre az erőforrásokra a jövőben, törölje az erőforráscsoportot a következő parancs Cloud Shellben történő futtatásával. Ne feledje, hogy az erőforráscsoport neve automatikusan jött létre a [webalkalmazást létrehozó](#2-create-a-web-app) lépés során.

```bash
az group delete --name appsvc_rg_Windows_westeurope
```

A parancs futtatása egy percig is eltarthat.

<hr/>

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Egyéni tartomány leképezése](app-service-web-tutorial-custom-domain-uiex.md)
