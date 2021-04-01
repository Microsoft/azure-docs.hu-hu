---
title: Erőforrások kezelése – Azure CLI
description: Az Azure CLI és a Azure Resource Manager használatával kezelheti az erőforrásokat. Az erőforrások üzembe helyezésének és törlésének módját mutatja be.
author: mumian
ms.topic: conceptual
ms.date: 02/11/2019
ms.author: jgao
ms.custom: devx-track-azurecli
ms.openlocfilehash: 077ebdb4dd33249923064a4f5ed20c5641a82e26
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "97695898"
---
# <a name="manage-azure-resources-by-using-azure-cli"></a>Azure-erőforrások kezelése az Azure CLI használatával

Ismerje meg, hogyan kezelheti Azure-erőforrásait az Azure CLI és a [Azure Resource Manager](overview.md) használatával. Az erőforráscsoportok kezelésével kapcsolatban lásd: [Azure-erőforráscsoportok kezelése az Azure CLI használatával](manage-resource-groups-cli.md).

További cikkek az erőforrások kezelésével kapcsolatban:

- [Azure-erőforrások kezelése a Azure Portal használatával](manage-resources-portal.md)
- [Azure-erőforrások kezelése Azure PowerShell használatával](manage-resources-powershell.md)

## <a name="deploy-resources-to-an-existing-resource-group"></a>Erőforrások központi telepítése meglévő erőforráscsoporthoz

Az Azure-erőforrásokat közvetlenül az Azure CLI használatával helyezheti üzembe, vagy üzembe helyezhet egy Resource Manager-sablont Azure-erőforrások létrehozásához.

### <a name="deploy-a-resource"></a>Erőforrás üzembe helyezése

A következő szkript létrehoz egy Storage-fiókot.

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
echo "Enter the location (i.e. centralus):" &&
read location &&
echo "Enter the storage account name:" &&
read storageAccountName &&
az storage account create --resource-group $resourceGroupName --name $storageAccountName --location $location --sku Standard_LRS --kind StorageV2 &&
az storage account show --resource-group $resourceGroupName --name $storageAccountName 
```

### <a name="deploy-a-template"></a>Sablon üzembe helyezése

A következő szkript létrehoz egy gyors üzembe helyezési sablont egy Storage-fiók létrehozásához. További információ: gyors útmutató [: ARM-sablonok létrehozása a Visual Studio Code](../templates/quickstart-create-templates-use-visual-studio-code.md?tabs=PowerShell)-ban.

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
echo "Enter the location (i.e. centralus):" &&
read location &&
az deployment group create --resource-group $resourceGroupName --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json"
```

További információ: [erőforrások üzembe helyezése Resource Manager-sablonokkal és az Azure CLI-vel](../templates/deploy-cli.md).

## <a name="deploy-a-resource-group-and-resources"></a>Erőforráscsoport és erőforrások üzembe helyezése

Létrehozhat egy erőforráscsoportot, és erőforrásokat telepíthet a csoportba. További információ: [erőforráscsoport létrehozása és erőforrások telepítése](../templates/deploy-to-subscription.md#resource-groups).

## <a name="deploy-resources-to-multiple-subscriptions-or-resource-groups"></a>Erőforrások üzembe helyezése több előfizetéshez vagy erőforráscsoporthoz

A sablonban lévő összes erőforrást általában egyetlen erőforráscsoporthoz kell telepíteni. Vannak azonban olyan forgatókönyvek, amelyekben különböző erőforrás-készleteket kíván üzembe helyezni, de más erőforráscsoportokbe vagy előfizetésbe helyezi őket. További információ: [Azure-erőforrások üzembe helyezése több előfizetésre vagy erőforráscsoporthoz](../templates/deploy-to-resource-group.md).

## <a name="delete-resources"></a>Erőforrások törlése

A következő szkript bemutatja, hogyan törölhet egy Storage-fiókot.

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
echo "Enter the storage account name:" &&
read storageAccountName &&
az storage account delete --resource-group $resourceGroupName --name $storageAccountName 
```

További információ arról, hogy a Azure Resource Manager hogyan rendeli az erőforrások törlését: [Azure Resource Manager erőforráscsoport törlése](delete-resource-group.md).

## <a name="move-resources"></a>Erőforrások áthelyezése

Az alábbi szkript bemutatja, hogyan távolíthat el egy Storage-fiókot egyik erőforráscsoporthoz egy másik erőforráscsoporthoz.

```azurecli-interactive
echo "Enter the source Resource Group name:" &&
read srcResourceGroupName &&
echo "Enter the destination Resource Group name:" &&
read destResourceGroupName &&
echo "Enter the storage account name:" &&
read storageAccountName &&
storageAccount=$(az resource show --resource-group $srcResourceGroupName --name $storageAccountName --resource-type Microsoft.Storage/storageAccounts --query id --output tsv) &&
az resource move --destination-group $destResourceGroupName --ids $storageAccount
```

További információ: [Erőforrások áthelyezése új erőforráscsoportba vagy előfizetésbe](move-resource-group-and-subscription.md).

## <a name="lock-resources"></a>Erőforrások zárolása

A zárolás megakadályozza a szervezet más felhasználói számára a kritikus erőforrások, például az Azure-előfizetés, az erőforráscsoport vagy az erőforrás véletlen törlését vagy módosítását. 

A következő parancsfájl zárolja a Storage-fiókot, így a fiók nem törölhető.

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
echo "Enter the storage account name:" &&
read storageAccountName &&
az lock create --name LockSite --lock-type CanNotDelete --resource-group $resourceGroupName --resource-name $storageAccountName --resource-type Microsoft.Storage/storageAccounts 
```

A következő parancsfájl minden zárolást beolvas egy Storage-fiókhoz:

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
echo "Enter the storage account name:" &&
read storageAccountName &&
az lock list --resource-group $resourceGroupName --resource-name $storageAccountName --resource-type Microsoft.Storage/storageAccounts --parent ""
```

A következő szkript törli a Storage-fiók zárolását:

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
echo "Enter the storage account name:" &&
read storageAccountName &&
lockId=$(az lock show --name LockSite --resource-group $resourceGroupName --resource-type Microsoft.Storage/storageAccounts --resource-name $storageAccountName --output tsv --query id)&&
az lock delete --ids $lockId
```

További információ: [Erőforrások zárolása az Azure Resource Manager eszközzel](lock-resources.md).

## <a name="tag-resources"></a>Erőforrások címkézése

A címkézés segítségével logikailag rendszerezheti az erőforráscsoportot és az erőforrásokat. További információ: [címkék használata az Azure-erőforrások rendszerezéséhez](tag-resources.md#azure-cli).

## <a name="manage-access-to-resources"></a>Erőforrásokhoz való hozzáférés kezelése

Az Azure [szerepköralapú hozzáférés-vezérlés (Azure RBAC)](../../role-based-access-control/overview.md) segítségével kezelheti az Azure-beli erőforrásokhoz való hozzáférést. További információ: [Azure-beli szerepkör-hozzárendelések hozzáadása vagy eltávolítása az Azure CLI használatával](../../role-based-access-control/role-assignments-cli.md).

## <a name="next-steps"></a>Következő lépések

- Azure Resource Manager megismeréséhez tekintse meg a [Azure Resource Manager áttekintése](overview.md)című témakört.
- A Resource Manager-sablon szintaxisának megismeréséhez tekintse meg [Azure Resource Manager sablonok struktúrájának és szintaxisának megismerése](../templates/template-syntax.md)című témakört.
- A sablonok fejlesztésének megismeréséhez tekintse meg az útmutató [lépésről lépésre szóló oktatóanyagokat](../index.yml).
- A Azure Resource Manager sablon sémáinak megtekintéséhez lásd: [sablon-hivatkozás](/azure/templates/).