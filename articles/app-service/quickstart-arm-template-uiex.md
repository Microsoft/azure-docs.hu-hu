---
title: App Service alkalmazás létrehozása Azure Resource Manager sablon használatával
description: Hozza létre az első alkalmazást, hogy másodpercek alatt Azure App Service egy Azure Resource Manager sablon (ARM-sablon) használatával, amely a App Service üzembe helyezésének számos módszere.
author: msangapu-msft
ms.author: msangapu
ms.assetid: 582bb3c2-164b-42f5-b081-95bfcb7a502a
ms.topic: quickstart
ms.date: 10/16/2020
ms.custom: subject-armqs, devx-track-azurecli
zone_pivot_groups: app-service-platform-windows-linux
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 6e0368abcca5f0c38a7b143386d666e7908f30b2
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2021
ms.locfileid: "101748807"
---
# <a name="quickstart-create-app-service-app-using-an-arm-template"></a>Rövid útmutató: App Service alkalmazás létrehozása ARM-sablon használatával

A [Azure app Service](overview.md) használatának első lépései a Felhőbeli alkalmazások üzembe helyezésével <abbr title="Egy JSON-fájl, amely egy vagy több Azure-erőforrást és függőséget definiál a telepített erőforrások között. A sablon erőforrások konzisztens és ismétlődő telepítésére használandó.">ARM-sablon</abbr> és Cloud Shell [Azure CLI](/cli/azure/get-started-with-azure-cli) -vel. Mivel ingyenes App Service szintet használ, a rövid útmutató elvégzéséhez nem jár költséggel. <abbr title="A deklaratív szintaxissal úgy írhatja le a kívánt üzemelő példányt, hogy nem kell megírnia az üzemelő példány létrehozására szolgáló programozási parancsokat.">A sablon deklaratív szintaxist használ.</abbr>

 Ha a környezet megfelel az előfeltételeknek, és már ismeri az [ARM-sablonok](../azure-resource-manager/templates/overview.md)használatát, válassza az **üzembe helyezés az Azure** -ban gombot. A sablon az Azure Portalon fog megnyílni.

::: zone pivot="platform-windows"
[![Üzembe helyezés az Azure-ban](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-app-service-docs-windows%2Fazuredeploy.json)
::: zone-end

::: zone pivot="platform-linux"
[![Üzembe helyezés az Azure-ban](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-app-service-docs-linux%2Fazuredeploy.json)
::: zone-end

<hr/>

## <a name="1-prepare-your-environment"></a>1. a környezet előkészítése

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

<hr/>

## <a name="2-review-the-template"></a>2. a sablon áttekintése

::: zone pivot="platform-windows"
Az ebben a gyorsútmutatóban használt sablon az [Azure-gyorssablonok](https://azure.microsoft.com/resources/templates/101-app-service-docs-windows) közül származik. Üzembe helyez egy App Service csomagot és egy App Service alkalmazást a Windows rendszeren.

:::code language="json" source="~/quickstart-templates/101-app-service-docs-windows/azuredeploy.json":::

<details>
<summary>Milyen erőforrások és paraméterek vannak meghatározva a sablonban?</summary>

Két Azure-erőforrás van definiálva a sablonban:

* [**Microsoft. Web/kiszolgálófarmok**](/azure/templates/microsoft.web/serverfarms): hozzon létre egy app Service csomagot.
* [**Microsoft. Web/Sites**](/azure/templates/microsoft.web/sites): hozzon létre egy app Service alkalmazást.

A következő táblázat az alapértelmezett paramétereket és azok leírásait tartalmazza:

| Paraméterek | Típus    | Alapértelmezett érték                | Leírás |
|------------|---------|------------------------------|-------------|
| webAppName | sztring  | "webApp- **[`<uniqueString>`](../azure-resource-manager/templates/template-functions-string.md#uniquestring)** " | Alkalmazás neve |
| location   | sztring  | "[[resourceGroup (). location](../azure-resource-manager/templates/template-functions-resource.md#resourcegroup)]" | Alkalmazás-régió |
| SKU        | sztring  | F1                         | Példány mérete (F1 = ingyenes csomag) |
| language   | sztring  | .net                       | Programozási nyelv stack (.net, php, node, HTML) |
| helloWorld | boolean | Hamis                        | True = "Hello World" alkalmazás üzembe helyezése |
| reszakadás    | sztring  | " "                          | Külső git-tárház (opcionális) |

---

</details>
::: zone-end
::: zone pivot="platform-linux"
Az ebben a gyorsútmutatóban használt sablon az [Azure-gyorssablonok](https://azure.microsoft.com/resources/templates/101-app-service-docs-linux) közül származik. Üzembe helyez egy App Service csomagot és egy App Service alkalmazást a Windows rendszeren.

:::code language="json" source="~/quickstart-templates/101-app-service-docs-linux/azuredeploy.json":::

Ez a sablon tartalmazza az Azure-erőforrásokat és az Ön kényelméhez megadott paramétereket.

<details>
<summary>Milyen erőforrások és paraméterek vannak meghatározva a sablonban?</summary>

Két Azure-erőforrás van definiálva a sablonban:

* [**Microsoft. Web/kiszolgálófarmok**](/azure/templates/microsoft.web/serverfarms): hozzon létre egy app Service csomagot.
* [**Microsoft. Web/Sites**](/azure/templates/microsoft.web/sites): hozzon létre egy app Service alkalmazást.

A következő táblázat az alapértelmezett paramétereket és azok leírásait tartalmazza:

| Paraméterek | Típus    | Alapértelmezett érték                | Leírás |
|------------|---------|------------------------------|-------------|
| webAppName | sztring  | "webApp- **[`<uniqueString>`](../azure-resource-manager/templates/template-functions-string.md#uniquestring)** " | Alkalmazás neve |
| location   | sztring  | "[[resourceGroup (). location](../azure-resource-manager/templates/template-functions-resource.md#resourcegroup)]" | Alkalmazás-régió |
| SKU        | sztring  | F1                         | Példány mérete (F1 = ingyenes csomag) |
| linuxFxVersion   | sztring  | "DOTNETCORE&#124;3,0        | "Programozási nyelvi verem &#124; verziója" |
| reszakadás    | sztring  | " "                          | Külső git-tárház (opcionális) |

---

</details>

::: zone-end

<hr/>

## <a name="3-deploy-the-template"></a>3. a sablon üzembe helyezése

::: zone pivot="platform-windows"
Futtassa az alábbi kódot a .NET-keretrendszerbeli alkalmazás Azure CLI-vel történő üzembe helyezéséhez. 

Csere <abbr title="Az érvényes karakterek: `a-z` , `0-9` és `-` .">`<app-name>`</abbr> globálisan egyedi alkalmazás-névvel. További információ <abbr title="Használhatja a Azure Portal, Azure PowerShell és REST API is.">üzembe helyezési módszerek</abbr>: [sablonok üzembe helyezése](../azure-resource-manager/templates/deploy-powershell.md). Itt találhat további [Azure app Service sablon mintákat](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Sites).

```azurecli-interactive
az group create --name myResourceGroup --location "southcentralus" &&
az deployment group create --resource-group myResourceGroup \
--parameters language=".net" helloWorld="true" webAppName="<app-name>" \
--template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-app-service-docs-windows/azuredeploy.json"
```
::: zone-end
::: zone pivot="platform-linux"
Futtassa az alábbi kódot egy Python-alkalmazás Linuxon való létrehozásához. 

Csere <abbr title="Az érvényes karakterek: `a-z` , `0-9` és `-` .">`<app-name>`</abbr> globálisan egyedi alkalmazás-névvel.

```azurecli-interactive
az group create --name myResourceGroup --location "southcentralus" &&
az deployment group create --resource-group myResourceGroup --parameters webAppName="<app-name>" linuxFxVersion="PYTHON|3.7" \
--template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-app-service-docs-linux/azuredeploy.json"
```
::: zone-end

<details>
<summary>Mi a kód?</summary>
<p>A parancsok a következő műveleteket hajtják végre:</p>
<ul>
<li>Alapértelmezett létrehozása <abbr title="Logikai tároló a kapcsolódó Azure-erőforrásokhoz, amelyeket egységként lehet kezelni.">erőforráscsoport</abbr>.</li>
<li>Alapértelmezett létrehozása <abbr title="Az alkalmazást futtató webkiszolgáló-Farm helyét, méretét és szolgáltatásait meghatározó csomag.">App Service-csomag</abbr>.</li>
<li><a href="/cli/azure/webapp?view=azure-cli-latest#az-webapp-create">Hozzon létre egy <abbr title="A webalkalmazás ábrázolása, amely az alkalmazás kódját, DNS-állomásnevét, tanúsítványait és kapcsolódó erőforrásait tartalmazza. "> App Service alkalmazás</abbr></a> a megadott névvel.</li>
</ul>
</details>

::: zone pivot="platform-windows"
<details>
<summary>Hogyan egy másik nyelvi verem üzembe helyezését?</summary>
Másik nyelvi verem üzembe helyezéséhez frissítsen <abbr title="Ez a sablon kompatibilis a .NET Core, a .NET Framework, a PHP, a Node.js és a statikus HTML-alkalmazásokkal. "> nyelvi paraméter</abbr> megfelelő értékekkel. A Java esetében lásd: <a href="/azure/app-service/quickstart-java-uiex">Java-alkalmazás létrehozása</a>.

| Paraméterek | Típus    | Alapértelmezett érték                | Leírás |
|------------|---------|------------------------------|-------------|
| language   | sztring  | .net                       | Programozási nyelv stack (.net, php, node, HTML) |

---

</details>
::: zone-end
::: zone pivot="platform-linux"
<details>
<summary>Hogyan egy másik nyelvi verem üzembe helyezését?</summary>
Egy másik nyelvi verem telepítéséhez a `linuxFxVersion` megfelelő értékekkel kell frissíteni. A minták alább láthatók. Az aktuális verziók megjelenítéséhez futtassa a következő parancsot a Cloud Shellban: `az webapp config show --resource-group myResourceGroup --name <app-name> --query linuxFxVersion`

| Nyelv    | Példa                                              |
|-------------|------------------------------------------------------|
| **.NET**    | linuxFxVersion = "DOTNETCORE&#124;3,0"                 |
| **PHP**     | linuxFxVersion = "PHP&#124;7,4"                        |
| **Node.js** | linuxFxVersion = "NODE&#124;10,15"                     |
| **Java**    | linuxFxVersion = "JAVA&#124;1,8 &#124;TOMCAT&#124;9,0" |
| **Python**  | linuxFxVersion = "PYTHON&#124;3,7"                     |
| **Ruby**    | linuxFxVersion = "RUBY&#124;2,6"                       |

---

</details>
::: zone-end

<hr/>

## <a name="4-validate-the-deployment"></a>4. az üzemelő példány ellenőrzése

Tallózással keresse `http://<app_name>.azurewebsites.net/` meg és ellenőrizze, hogy létrejött-e.

<hr/>

## <a name="5-clean-up-resources"></a>5. erőforrások tisztítása

Ha már nincs rá szükség, [törölje az erőforráscsoportot](../azure-resource-manager/management/delete-resource-group.md?tabs=azure-portal#delete-resource-group).

<hr/>

## <a name="next-steps"></a>Következő lépések

- [Üzembe helyezés a helyi Gitből](deploy-local-git.md)
- [ASP.NET Core és SQL Database](tutorial-dotnetcore-sqldb-app.md)
- [Python és Postgres](tutorial-python-postgresql-app.md)
- [PHP és MySQL](tutorial-php-mysql-app.md)
- [Kapcsolódás az Azure SQL Database-hez a Javával](../azure-sql/database/connect-query-java.md?toc=%2fazure%2fjava%2ftoc.json)
- [Egyéni tartomány leképezése](app-service-web-tutorial-custom-domain-uiex.md)

