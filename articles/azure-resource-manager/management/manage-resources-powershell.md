---
title: Erőforrások kezelése – Azure PowerShell
description: Az erőforrások kezeléséhez használja a Azure PowerShell és a Azure Resource Manager. Az erőforrások üzembe helyezésének és törlésének módját mutatja be.
author: mumian
ms.topic: conceptual
ms.date: 02/11/2019
ms.author: jgao
ms.openlocfilehash: ce3b1d9f8d01e722e4ec5078d92ef4148072da25
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96185742"
---
# <a name="manage-azure-resources-by-using-azure-powershell"></a>Azure-erőforrások kezelése Azure PowerShell használatával

Ismerje meg, hogyan kezelheti az Azure-erőforrásokat a Azure PowerShell és a [Azure Resource Manager](overview.md) használatával. Az erőforráscsoportok kezelésével kapcsolatban lásd: [Azure-erőforráscsoportok kezelése Azure PowerShell használatával](manage-resource-groups-powershell.md).

További cikkek az erőforrások kezelésével kapcsolatban:

- [Azure-erőforrások kezelése a Azure Portal használatával](manage-resources-portal.md)
- [Azure-erőforrások kezelése az Azure CLI használatával](manage-resources-cli.md)

## <a name="deploy-resources-to-an-existing-resource-group"></a>Erőforrások központi telepítése meglévő erőforráscsoporthoz

Az Azure-erőforrásokat közvetlenül a Azure PowerShell használatával telepítheti, vagy üzembe helyezhet egy Resource Manager-sablont az Azure-erőforrások létrehozásához.

### <a name="deploy-a-resource"></a>Erőforrás üzembe helyezése

A következő szkript létrehoz egy Storage-fiókot.

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$storageAccountName = Read-Host -Prompt "Enter the storage account name"

# Create the storage account.
$storageAccount = New-AzStorageAccount -ResourceGroupName $resourceGroupName `
  -Name $storageAccountName `
  -Location $location `
  -SkuName "Standard_LRS"

# Retrieve the context.
$ctx = $storageAccount.Context
```

### <a name="deploy-a-template"></a>Sablon üzembe helyezése

A következő szkript létrehoz egy gyors üzembe helyezési sablont egy Storage-fiók létrehozásához. További információ: gyors útmutató [: Azure Resource Manager-sablonok létrehozása a Visual Studio Code használatával](../templates/quickstart-create-templates-use-visual-studio-code.md?tabs=PowerShell).

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json"
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri -Location $location
```

További információ: [erőforrások üzembe helyezése Resource Manager-sablonokkal és-Azure PowerShellokkal](../templates/deploy-powershell.md).

## <a name="deploy-a-resource-group-and-resources"></a>Erőforráscsoport és erőforrások üzembe helyezése

Létrehozhat egy erőforráscsoportot, és erőforrásokat telepíthet a csoportba. További információ: [erőforráscsoport létrehozása és erőforrások telepítése](../templates/deploy-to-subscription.md#resource-groups).

## <a name="deploy-resources-to-multiple-subscriptions-or-resource-groups"></a>Erőforrások üzembe helyezése több előfizetéshez vagy erőforráscsoporthoz

A sablonban lévő összes erőforrást általában egyetlen erőforráscsoporthoz kell telepíteni. Vannak azonban olyan forgatókönyvek, amelyekben különböző erőforrás-készleteket kíván üzembe helyezni, de más erőforráscsoportokbe vagy előfizetésbe helyezi őket. További információ: [Azure-erőforrások üzembe helyezése több előfizetésre vagy erőforráscsoporthoz](../templates/deploy-to-resource-group.md).

## <a name="delete-resources"></a>Erőforrások törlése

A következő szkript bemutatja, hogyan törölhet egy Storage-fiókot.

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$storageAccountName = Read-Host -Prompt "Enter the storage account name"

Remove-AzStorageAccount -ResourceGroupName $resourceGroupName -AccountName $storageAccountName
```

További információ arról, hogy a Azure Resource Manager hogyan rendeli az erőforrások törlését: [Azure Resource Manager erőforráscsoport törlése](delete-resource-group.md).

## <a name="move-resources"></a>Erőforrások áthelyezése

Az alábbi szkript bemutatja, hogyan távolíthat el egy Storage-fiókot egyik erőforráscsoporthoz egy másik erőforráscsoporthoz.

```azurepowershell-interactive
$srcResourceGroupName = Read-Host -Prompt "Enter the source Resource Group name"
$destResourceGroupName = Read-Host -Prompt "Enter the destination Resource Group name"
$storageAccountName = Read-Host -Prompt "Enter the storage account name"

$storageAccount = Get-AzResource -ResourceGroupName $srcResourceGroupName -ResourceName $storageAccountName
Move-AzResource -DestinationResourceGroupName $destResourceGroupName -ResourceId $storageAccount.ResourceId
```

További információ: [Erőforrások áthelyezése új erőforráscsoportba vagy előfizetésbe](move-resource-group-and-subscription.md).

## <a name="lock-resources"></a>Erőforrások zárolása

A zárolás megakadályozza a szervezet más felhasználói számára a kritikus erőforrások, például az Azure-előfizetés, az erőforráscsoport vagy az erőforrás véletlen törlését vagy módosítását. 

A következő parancsfájl zárolja a Storage-fiókot, így a fiók nem törölhető.

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$storageAccountName = Read-Host -Prompt "Enter the storage account name"

New-AzResourceLock -LockName LockStorage -LockLevel CanNotDelete -ResourceGroupName $resourceGroupName -ResourceName $storageAccountName -ResourceType Microsoft.Storage/storageAccounts 
```

A következő parancsfájl minden zárolást beolvas egy Storage-fiókhoz:

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$storageAccountName = Read-Host -Prompt "Enter the storage account name"

Get-AzResourceLock -ResourceGroupName $resourceGroupName -ResourceName $storageAccountName -ResourceType Microsoft.Storage/storageAccounts
```

A következő szkript törli a Storage-fiók zárolását:

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$storageAccountName = Read-Host -Prompt "Enter the storage account name"

$lockId = (Get-AzResourceLock -ResourceGroupName $resourceGroupName -ResourceName $storageAccountName -ResourceType Microsoft.Storage/storageAccounts).LockId
Remove-AzResourceLock -LockId $lockId
```

További információ: [Erőforrások zárolása az Azure Resource Manager eszközzel](lock-resources.md).

## <a name="tag-resources"></a>Erőforrások címkézése

A címkézés segítségével logikailag rendszerezheti az erőforráscsoportot és az erőforrásokat. További információ: [címkék használata az Azure-erőforrások rendszerezéséhez](tag-resources.md#powershell).

## <a name="manage-access-to-resources"></a>Erőforrásokhoz való hozzáférés kezelése

Az Azure [szerepköralapú hozzáférés-vezérlés (Azure RBAC)](../../role-based-access-control/overview.md) segítségével kezelheti az Azure-beli erőforrásokhoz való hozzáférést. További információ: [Azure szerepkör-hozzárendelések hozzáadása vagy eltávolítása Azure PowerShell használatával](../../role-based-access-control/role-assignments-powershell.md).

## <a name="next-steps"></a>Következő lépések

- Azure Resource Manager megismeréséhez tekintse meg a [Azure Resource Manager áttekintése](overview.md)című témakört.
- A Resource Manager-sablon szintaxisának megismeréséhez tekintse meg [Azure Resource Manager sablonok struktúrájának és szintaxisának megismerése](../templates/template-syntax.md)című témakört.
- A sablonok fejlesztésének megismeréséhez tekintse meg az útmutató [lépésről lépésre szóló oktatóanyagokat](../index.yml).
- A Azure Resource Manager sablon sémáinak megtekintéséhez lásd: [sablon-hivatkozás](/azure/templates/).