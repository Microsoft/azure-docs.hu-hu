---
title: 'Azure Resource Manager: Azure SQL felügyelt példány létrehozása'
description: Megtudhatja, hogyan hozhat létre egy felügyelt Azure SQL-példányt egy Azure Resource Manager sablon használatával.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: subject-armqs
ms.devlang: ''
ms.topic: quickstart
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 06/22/2020
ms.openlocfilehash: badeb850e8d0346347a994f053fb0f64fd01240a
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "91283343"
---
# <a name="quickstart-create-an-azure-sql-managed-instance-using-an-arm-template"></a>Gyors útmutató: felügyelt Azure SQL-példány létrehozása ARM-sablonnal

Ez a rövid útmutató egy Azure Resource Manager-sablon (ARM-sablon) üzembe helyezésének folyamatát tárgyalja az Azure SQL felügyelt példányának és vNet létrehozásához. Az [Azure SQL felügyelt példánya](sql-managed-instance-paas-overview.md) egy intelligens, teljes körűen felügyelt, méretezhető Felhőbeli adatbázis, amely csaknem 100%-os szolgáltatási paritást biztosít a SQL Server adatbázis-motorral.

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

Ha a környezet megfelel az előfeltételeknek, és már ismeri az ARM-sablonokat, kattintson az **Üzembe helyezés az Azure-ban** gombra. A sablon az Azure Portalon fog megnyílni.

[![Üzembe helyezés az Azure-ban](../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-sqlmi-new-vnet%2Fazuredeploy.json)

## <a name="prerequisites"></a>Előfeltételek

Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/).

## <a name="review-the-template"></a>A sablon áttekintése

Az ebben a gyorsútmutatóban használt sablon az [Azure-gyorssablonok](https://azure.microsoft.com/resources/templates/101-sqlmi-new-vnet/) közül származik.

:::code language="json" source="~/quickstart-templates/101-sqlmi-new-vnet/azuredeploy.json":::

Ezek az erőforrások a sablonban vannak definiálva:

- [**Microsoft. Network/networkSecurityGroups**](/azure/templates/microsoft.Network/networkSecurityGroups)
- [**Microsoft. Network/routeTables**](/azure/templates/microsoft.Network/routeTables)
- [**Microsoft. Network/virtualNetworks**](/azure/templates/microsoft.Network/virtualNetworks)
- [**Microsoft. SQL/managedinstances**](/azure/templates/microsoft.sql/managedinstances)

További sablon-minták találhatók az [Azure Gyorsindítás sablonjaiban](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Sql&pageNumber=1&sort=Popular).

## <a name="deploy-the-template"></a>A sablon üzembe helyezése

A Azure Cloud Shell megnyitásához válassza a **kipróbálás** a következő PowerShell-kódból elemet.

> [!IMPORTANT]
> A felügyelt példányok üzembe helyezése hosszan futó művelet. Az alhálózat első példányának üzembe helyezése általában sokkal hosszabb időt vesz igénybe, mint egy meglévő felügyelt példányokkal rendelkező alhálózatba való üzembe helyezés. Az átlagos kiépítési idő: [SQL felügyelt példányok kezelési műveletei](sql-managed-instance-paas-overview.md#management-operations).

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter a project name that is used for generating resource names"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-sqlmi-new-vnet/azuredeploy.json"

$resourceGroupName = "${projectName}rg"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri

Read-Host -Prompt "Press [ENTER] to continue ..."
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
read -p "Enter a project name that is used for generating resource names:" projectName &&
read -p "Enter the location (i.e. centralus):" location &&
templateUri="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-sqlmi-new-vnet/azuredeploy.json" &&
resourceGroupName="${projectName}rg" &&
az group create --name $resourceGroupName --location "$location" &&
az deployment group create --resource-group $resourceGroupName --template-uri  $templateUri &&
echo "Press [ENTER] to continue ..." &&
read
```

---

## <a name="review-deployed-resources"></a>Üzembe helyezett erőforrások áttekintése

Látogasson el a [Azure Portalra](https://portal.azure.com/#blade/HubsExtension/BrowseResourceGroups) , és ellenőrizze, hogy a felügyelt példány a kiválasztott erőforráscsoporthoz van-e. Mivel a felügyelt példányok létrehozása hosszabb időt is igénybe vehet, előfordulhat, hogy ellenőriznie kell a **központi telepítések** hivatkozást az erőforráscsoport **Áttekintés** lapján.

- Az Azure-beli virtuális gépekről az SQL felügyelt példányhoz való kapcsolódást bemutató rövid útmutató: Azure-beli [Virtuálisgép-kapcsolat konfigurálása](connect-vm-instance-configure.md).
- A pont – hely kapcsolat [konfigurálásával](point-to-site-p2s-configure.md)megtudhatja, hogyan CSATLAKOZHAT az SQL felügyelt példányához egy helyszíni ügyfélszámítógépről pont – hely kapcsolat használatával.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Tartsa meg a felügyelt példányt, ha a [következő lépésekre](#next-steps)szeretne lépni, de további oktatóanyagok befejezése után törölje a felügyelt példányt és a kapcsolódó erőforrásokat. Felügyelt példány törlése után tekintse meg [az alhálózat törlése a felügyelt példány törlése után](virtual-cluster-delete.md)című témakört.


Az erőforráscsoport törlése:

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
Remove-AzResourceGroup -Name $resourceGroupName
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName
```

---

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Azure-beli virtuális gép konfigurálása a felügyelt Azure SQL-példányhoz való kapcsolódáshoz](connect-vm-instance-configure.md)
