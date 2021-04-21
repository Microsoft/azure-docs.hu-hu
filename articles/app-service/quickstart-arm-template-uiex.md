---
title: Alkalmazás App Service létrehozása Azure Resource Manager sablonnal
description: Hozza létre az első alkalmazását Azure App Service másodpercek alatt egy Azure Resource Manager-sablonnal (ARM-sablonnal), amely az üzembe helyezés számos App Service.
author: msangapu-msft
ms.author: msangapu
ms.assetid: 582bb3c2-164b-42f5-b081-95bfcb7a502a
ms.topic: quickstart
ms.date: 10/16/2020
ms.custom: subject-armqs, devx-track-azurecli
zone_pivot_groups: app-service-platform-windows-linux
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: bce6bfb61eb59d1fa66c550a133ac8b6f8d7f2c5
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107769002"
---
# <a name="quickstart-create-app-service-app-using-an-arm-template"></a>Rövid útmutató: App Service létrehozása ARM-sablonnal

A Azure App Service [üzembe](overview.md) helyezéséhez telepítsen egy alkalmazást a felhőben a következő használatával: <abbr title="Egy JSON-fájl, amely deklaratív módon definiál egy vagy több Azure-erőforrást és függőséget az üzembe helyezett erőforrások között. A sablon erőforrások konzisztens és ismétlődő telepítésére használandó.">ARM-sablon</abbr> és [az Azure CLI](/cli/azure/get-started-with-azure-cli) Cloud Shell. Mivel ingyenes App Service használ, a rövid útmutató költségekkel jár. <abbr title="A deklaratív szintaxissal úgy írhatja le a kívánt üzemelő példányt, hogy nem kell megírnia az üzemelő példány létrehozására szolgáló programozási parancsokat.">A sablon deklaratív szintaxist használ.</abbr>

 Ha a környezet megfelel az előfeltételeknek, és már ismeri az [ARM-sablonok](../azure-resource-manager/templates/overview.md)használatának módszerét, válassza az Üzembe helyezés az **Azure-ban** gombot. A sablon az Azure Portalon fog megnyílni.

::: zone pivot="platform-windows"
[![Üzembe helyezés az Azure-ban](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-app-service-docs-windows%2Fazuredeploy.json)
::: zone-end

::: zone pivot="platform-linux"
[![Üzembe helyezés az Azure-ban](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-app-service-docs-linux%2Fazuredeploy.json)
::: zone-end

<hr/>

## <a name="1-prepare-your-environment"></a>1. A környezet előkészítése

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

<hr/>

## <a name="2-review-the-template"></a>2. A sablon áttekintése

::: zone pivot="platform-windows"
Az ebben a gyorsútmutatóban használt sablon az [Azure-gyorssablonok](https://azure.microsoft.com/resources/templates/101-app-service-docs-windows) közül származik. Üzembe helyez egy App Service- és egy App Service Windows rendszeren.

:::code language="json" source="~/quickstart-templates/101-app-service-docs-windows/azuredeploy.json":::

<details>
<summary>Milyen erőforrásokat és paramétereket definiál a sablon?</summary>

A sablonban két Azure-erőforrás van meghatározva:

* [**Microsoft.Web/serverfarms:**](/azure/templates/microsoft.web/serverfarms)hozzon létre App Service tervet.
* [**Microsoft.Web/sites:**](/azure/templates/microsoft.web/sites)hozzon létre App Service alkalmazást.

Az alábbi táblázat az alapértelmezett paramétereket és azok leírását részletezi:

| Paraméterek | Típus    | Alapértelmezett érték                | Description |
|------------|---------|------------------------------|-------------|
| webAppName | sztring  | "webApp- **[`<uniqueString>`](../azure-resource-manager/templates/template-functions-string.md#uniquestring)** " | Alkalmazás neve |
| location   | sztring  | "[[resourceGroup().location](../azure-resource-manager/templates/template-functions-resource.md#resourcegroup)]" | Alkalmazás régiója |
| Sku        | sztring  | "F1"                         | Példányméret (F1 = ingyenes szint) |
| language   | sztring  | ".net"                       | Programozási nyelvi verem (.net, php, node, html) |
| helloWorld | boolean | Hamis                        | True = "Hello World" alkalmazás üzembe helyezése |
| repoUrl    | sztring  | " "                          | Külső Git-adattár (nem kötelező) |

---

</details>
::: zone-end
::: zone pivot="platform-linux"
Az ebben a gyorsútmutatóban használt sablon az [Azure-gyorssablonok](https://azure.microsoft.com/resources/templates/101-app-service-docs-linux) közül származik. Üzembe helyez egy App Service- és egy App Service Windows rendszeren.

:::code language="json" source="~/quickstart-templates/101-app-service-docs-linux/azuredeploy.json":::

Ez a sablon az Ön kényelme érdekében meghatározott Azure-erőforrásokat és paramétereket tartalmaz.

<details>
<summary>Milyen erőforrásokat és paramétereket definiál a sablon?</summary>

A sablonban két Azure-erőforrás van definiálva:

* [**Microsoft.Web/serverfarms:**](/azure/templates/microsoft.web/serverfarms)hozzon létre egy App Service tervet.
* [**Microsoft.Web/sites:**](/azure/templates/microsoft.web/sites)hozzon létre App Service alkalmazást.

Az alábbi táblázat az alapértelmezett paramétereket és azok leírását részletezi:

| Paraméterek | Típus    | Alapértelmezett érték                | Description |
|------------|---------|------------------------------|-------------|
| webAppName | sztring  | "webApp- **[`<uniqueString>`](../azure-resource-manager/templates/template-functions-string.md#uniquestring)** " | Alkalmazás neve |
| location   | sztring  | "[[resourceGroup().location](../azure-resource-manager/templates/template-functions-resource.md#resourcegroup)]" | Alkalmazás régiója |
| Sku        | sztring  | "F1"                         | Példányméret (F1 = ingyenes szint) |
| linuxFxVersion   | sztring  | "DOTNETCORE&#124;3.0        | "Programming language stack &#124; Version" |
| repoUrl    | sztring  | " "                          | Külső Git-adattár (nem kötelező) |

---

</details>

::: zone-end

<hr/>

## <a name="3-deploy-the-template"></a>3. A sablon üzembe helyezése

::: zone pivot="platform-windows"
Futtassa az alábbi kódot egy .NET-keretrendszerbeli alkalmazás windowsos üzembe helyezéséhez az Azure CLI használatával. 

Csere <abbr title="Érvényes karakterek: `a-z` `0-9` , és `-` .">`<app-name>`</abbr> egy globálisan egyedi alkalmazásnévvel. További információ <abbr title="Használhatja a Azure Portal, Azure PowerShell és REST API.">üzembe helyezési módszerek</abbr>, [lásd: Sablonok üzembe helyezése.](../azure-resource-manager/templates/deploy-powershell.md) További [sablonmintákat Azure App Service itt talál.](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Sites)

```azurecli-interactive
az group create --name myResourceGroup --location "southcentralus" &&
az deployment group create --resource-group myResourceGroup \
--parameters language=".net" helloWorld="true" webAppName="<app-name>" \
--template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-app-service-docs-windows/azuredeploy.json"
```
::: zone-end
::: zone pivot="platform-linux"
Futtassa az alábbi kódot egy Python-alkalmazás linuxos létrehozásához. 

Csere <abbr title="Érvényes karakterek: `a-z` `0-9` , és `-` .">`<app-name>`</abbr> egy globálisan egyedi alkalmazásnévvel.

```azurecli-interactive
az group create --name myResourceGroup --location "southcentralus" &&
az deployment group create --resource-group myResourceGroup --parameters webAppName="<app-name>" linuxFxVersion="PYTHON|3.7" \
--template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-app-service-docs-linux/azuredeploy.json"
```
::: zone-end

<details>
<summary>Mit csinál a kód?</summary>
<p>A parancsok a következő műveleteket ják végre:</p>
<ul>
<li>Alapértelmezett létrehozása <abbr title="Logikai tároló a kapcsolódó Azure-erőforrásokhoz, amelyek egy egységként kezelhetők.">erőforráscsoport</abbr>.</li>
<li>Alapértelmezett létrehozása <abbr title="A csomag, amely meghatározza az alkalmazást tartalmazó webkiszolgálófarm helyét, méretét és funkcióit.">App Service-csomag</abbr>.</li>
<li><a href="/cli/azure/webapp#az_webapp_create">Hozzon létre egy <abbr title="A webalkalmazás reprezentációja, amely tartalmazza az alkalmazás kódját, a DNS-állomásneveket, a tanúsítványokat és a kapcsolódó erőforrásokat. "> App Service alkalmazás</abbr></a> a megadott névvel.</li>
</ul>
</details>

::: zone pivot="platform-windows"
<details>
<summary>Hogyan nyelvi verem üzembe helyezéséhez?</summary>
Másik nyelvi verem üzembe helyezéséhez frissítse a <abbr title="Ez a sablon kompatibilis a .NET Core-, .NET-keretrendszer-, PHP-, Node.js- és statikus HTML-alkalmazásokkal. "> nyelvi paraméter</abbr> a megfelelő értékekkel. Javához lásd: <a href="/azure/app-service/quickstart-java-uiex">Java-alkalmazás létrehozása.</a>

| Paraméterek | Típus    | Alapértelmezett érték                | Description |
|------------|---------|------------------------------|-------------|
| language   | sztring  | ".net"                       | Programozási nyelvi verem (.net, php, node, html) |

---

</details>
::: zone-end
::: zone pivot="platform-linux"
<details>
<summary>Hogyan nyelvi verem üzembe helyezéséhez?</summary>
Másik nyelvi verem üzembe helyezéséhez frissítsen `linuxFxVersion` a megfelelő értékekkel. A minták alább láthatók. Az aktuális verziók a következő parancs futtatásával futtathatók a Cloud Shell: `az webapp config show --resource-group myResourceGroup --name <app-name> --query linuxFxVersion`

| Nyelv    | Példa                                              |
|-------------|------------------------------------------------------|
| **.NET**    | linuxFxVersion="DOTNETCORE&#124;3.0"                 |
| **PHP**     | linuxFxVersion="PHP&#124;7.4"                        |
| **Node.js** | linuxFxVersion="NODE&#124;10.15"                     |
| **Java**    | linuxFxVersion="JAVA&#124;1.8 &#124;TOMCAT&#124;9.0" |
| **Python**  | linuxFxVersion="PYTHON&#124;3.7"                     |
| **Ruby**    | linuxFxVersion="RUBY&#124;2.6"                       |

---

</details>
::: zone-end

<hr/>

## <a name="4-validate-the-deployment"></a>4. Az üzemelő példány ellenőrzése

Keresse meg `http://<app_name>.azurewebsites.net/` a webhelyet, és ellenőrizze, hogy létrejött-e.

<hr/>

## <a name="5-clean-up-resources"></a>5. Erőforrások tisztítása

Ha már nincs rá szükség, [törölje a erőforráscsoportot.](../azure-resource-manager/management/delete-resource-group.md?tabs=azure-portal#delete-resource-group)

<hr/>

## <a name="next-steps"></a>Következő lépések

- [Üzembe helyezés a helyi Gitből](deploy-local-git.md)
- [ASP.NET Core és SQL Database](tutorial-dotnetcore-sqldb-app.md)
- [Python és Postgres](tutorial-python-postgresql-app.md)
- [PHP és MySQL](tutorial-php-mysql-app.md)
- [Csatlakozás Azure SQL-adatbázishoz Java használatával](../azure-sql/database/connect-query-java.md?toc=%2fazure%2fjava%2ftoc.json)
- [Egyéni tartomány leképezése](app-service-web-tutorial-custom-domain-uiex.md)
