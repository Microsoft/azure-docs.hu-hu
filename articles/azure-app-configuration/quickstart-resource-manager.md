---
title: Tároló Azure App Configuration létrehozása egy Azure Resource Manager (ARM-sablon) használatával
titleSuffix: Azure App Configuration
description: Megtudhatja, hogyan hozhat létre Azure App Configuration tárolót egy Azure Resource Manager (ARM-sablon) használatával.
author: GrantMeStrength
ms.author: jken
ms.date: 10/16/2020
ms.service: azure-app-configuration
ms.topic: quickstart
ms.custom: subject-armqs, devx-track-azurepowershell
ms.openlocfilehash: 92ca80a6c807394c45be8f0187c7add736ba83ce
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2021
ms.locfileid: "107831779"
---
# <a name="quickstart-create-an-azure-app-configuration-store-by-using-an-arm-template"></a>Rövid útmutató: Azure App Configuration létrehozása ARM-sablonnal

Ez a rövid útmutató a következőt ismerteti:

- Üzembe helyezhet App Configuration tárolót egy Azure Resource Manager sablonnal (ARM-sablonnal).
- Kulcsértékek létrehozása egy App Configuration ARM-sablonnal.
- Kulcs-értékek olvasása egy App Configuration ARM-sablonból.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Ha a környezet megfelel az előfeltételeknek, és már ismeri az ARM-sablonokat, kattintson az **Üzembe helyezés az Azure-ban** gombra. A sablon az Azure Portalon fog megnyílni.

[![Üzembe helyezés az Azure-ban](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-app-configuration-store-kv%2Fazuredeploy.json)

## <a name="prerequisites"></a>Előfeltételek

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="review-the-template"></a>A sablon áttekintése

Az ebben a gyorsútmutatóban használt sablon az [Azure-gyorssablonok](https://azure.microsoft.com/resources/templates/101-app-configuration-store-kv/) közül származik. Létrehoz egy új App Configuration két kulcs-értékkel a tárolóban. Ezután a függvényt `reference` használja a két kulcs-érték erőforrás értékeinek kimenetére. A kulcs értékének ily módon való beolvasása lehetővé teszi, hogy a sablon más helyei is használva legyen.

A rövid útmutató a `copy` elemmel hozza létre a kulcs-érték erőforrás több példányát. Az elemmel kapcsolatos további `copy` információkért lásd: [Erőforrás-iteráció AZ ARM-sablonokban.](../azure-resource-manager/templates/copy-resources.md)

> [!IMPORTANT]
> Ehhez a sablonhoz App Configuration vagy újabb `2020-07-01-preview` verzió szükséges. Ez a verzió a `reference` függvényt használja a kulcs-értékek beolvassa. Az előző verzió kulcs-értékeinek olvasásához használt függvény a verziótól kezdődően nem `listKeyValue` érhető `2020-07-01-preview` el.

:::code language="json" source="~/quickstart-templates/101-app-configuration-store-kv/azuredeploy.json":::

A sablonban két Azure-erőforrás van meghatározva:

- [Microsoft.AppConfiguration/configurationStores:](/azure/templates/microsoft.appconfiguration/2020-07-01-preview/configurationstores)hozzon létre App Configuration tárolót.
- [Microsoft.AppConfiguration/configurationStores/keyValues:](/azure/templates/microsoft.appconfiguration/2020-07-01-preview/configurationstores/keyvalues)hozzon létre egy kulcs-értéket a App Configuration tárolóban.

> [!TIP]
> Az `keyValues` erőforrás neve kulcs és címke kombinációja. A kulcsot és a címkét az `$` elválasztójel összekapcsolja. A címke nem kötelező. A fenti példában a névvel jelölt erőforrás egy címke nélküli `keyValues` `myKey` kulcs-értéket hoz létre.
>
> A százalékkódolás, más néven URL-kódolás lehetővé teszi, hogy a kulcsok vagy címkék olyan karaktereket tartalmazzanak, amelyek nem engedélyezettek az ARM-sablonok erőforrásnevében. `%` A sem megengedett karakter, ezért a helyén `~` használatos. A név megfelelő kódolásához kövesse az alábbi lépéseket:
>
> 1. URL-kódolás alkalmazása
> 2. Cserélje le a `~` helyére a következőt `~7E`
> 3. Cserélje le a `%` helyére a következőt `~`
>
> Ha például kulcsnévvel és címkenévvel hoz létre kulcs-érték párt, az erőforrás nevének `AppName:DbEndpoint` `Test` a következőnek kell lennie: `AppName~3ADbEndpoint$Test` .

> [!NOTE]
> App Configuration kulcs-érték adatelérést tesz lehetővé egy privát [kapcsolaton](concept-private-endpoint.md) keresztül a virtuális hálózatról. Alapértelmezés szerint ha a funkció engedélyezve van, a nyilvános hálózaton App Configuration adatokra vonatkozó összes kérés le lesz tiltva. Mivel az ARM-sablon a virtuális hálózaton kívül fut, az ARM-sablonból való adatelérés nem engedélyezett. Ha privát kapcsolat használata esetén szeretné engedélyezni az adatok arm-sablonból való hozzáférését, a következő Azure CLI-paranccsal engedélyezheti a nyilvános hálózati hozzáférést. Ebben a forgatókönyvben fontos figyelembe venni a nyilvános hálózati hozzáférés engedélyezésének biztonsági következményeit.
>
> ```azurecli-interactive
> az appconfig update -g MyResourceGroup -n MyAppConfiguration --enable-public-network true
> ```

## <a name="deploy-the-template"></a>A sablon üzembe helyezése

Kattintson az alábbi gombra az Azure-ba való bejelentkezéshez és egy sablon megnyitásához. A sablon létrehoz egy App Configuration tárolót két kulcs-értékkel.

[![Üzembe helyezés az Azure-ban](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-app-configuration-store-kv%2Fazuredeploy.json)

A sablont a következő PowerShell-parancsmag használatával is üzembe helyezheti. A kulcs-értékek a PowerShell-konzol kimenetében lesznek.

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter a project name that is used for generating resource names"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-app-configuration-store-kv/azuredeploy.json"

$resourceGroupName = "${projectName}rg"

New-AzResourceGroup -Name $resourceGroupName -Location "$location"
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri

Read-Host -Prompt "Press [ENTER] to continue ..."
```

## <a name="review-deployed-resources"></a>Az üzembe helyezett erőforrások áttekintése

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. A Azure Portal keresőmezőbe írja be a **következőt: App Configuration.** Válassza **App Configuration** lehetőséget a listából.
1. Válassza ki az újonnan létrehozott App Configuration erőforrást.
1. A **Műveletek alatt kattintson** a Configuration Explorer **elemre.**
1. Ellenőrizze, hogy létezik-e két kulcs-érték.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, törölje az erőforráscsoportot, a App Configuration tárolót és az összes kapcsolódó erőforrást. Ha a jövőben a App Configuration használni, kihagyhatja annak törlését. Ha nem folytatja az áruház használatát, a következő parancsmag futtatásával törölje a rövid útmutatóhoz létrehozott összes erőforrást:

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
Remove-AzResourceGroup -Name $resourceGroupName
Write-Host "Press [ENTER] to continue..."
```

## <a name="next-steps"></a>Következő lépések

Ha többet szeretne megtudni a szolgáltatásjelölő hozzáadásáról és Key Vault egy App Configuration tárolóra való hivatkozásról, tekintse meg az alábbi ARM-sablonpéékokat.

- [101-app-configuration-store-ff](https://github.com/Azure/azure-quickstart-templates/tree/master/101-app-configuration-store-ff)
- [101-app-configuration-store-keyvaultref](https://github.com/Azure/azure-quickstart-templates/tree/master/101-app-configuration-store-keyvaultref)
