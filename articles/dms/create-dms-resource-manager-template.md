---
title: DMS-példány létrehozása (Azure Resource Manager sablon)
description: Megtudhatja, hogyan hozhat létre Database Migration Service a Azure Resource Manager Template (ARM-sablon) használatával.
author: MashaMSFT
ms.topic: quickstart
ms.custom: subject-armqs
ms.author: mathoma
ms.date: 06/29/2020
ms.service: dms
ms.openlocfilehash: a9c68bca4d50af734a0a2cd8a91c7e46d9b56ff1
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "94963215"
---
# <a name="quickstart-create-instance-of-azure-database-migration-service-using-arm-template"></a>Gyors útmutató: Azure Database Migration Service példányának létrehozása ARM-sablonnal

Ezzel a Azure Resource Manager sablonnal (ARM-sablon) telepítheti a Azure Database Migration Service egy példányát. 

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Ha a környezet megfelel az előfeltételeknek, és már ismeri az ARM-sablonokat, kattintson az **Üzembe helyezés az Azure-ban** gombra. A sablon az Azure Portalon fog megnyílni.

[![Üzembe helyezés az Azure-ban](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2f101-azure-database-migration-simple-deploy%2fazuredeploy.json)

## <a name="prerequisites"></a>Előfeltételek

Az Azure Database Migration Service ARM-sablonhoz a következők szükségesek: 

- Az [Azure CLI](/cli/azure/install-azure-cli) és/vagy a [PowerShell](/powershell/scripting/install/installing-powershell)legújabb verziója. 
- Azure-előfizetés. Ha még nem rendelkezik ilyennel, a Kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) .

## <a name="review-the-template"></a>A sablon áttekintése

Az ebben a gyorsútmutatóban használt sablon az [Azure-gyorssablonok](https://azure.microsoft.com/resources/templates/101-azure-database-migration-simple-deploy/) közül származik.

:::code language="json" source="~/quickstart-templates/101-azure-database-migration-simple-deploy/azuredeploy.json":::

Három Azure-erőforrás van definiálva a sablonban: 

- [Microsoft. Network/virtualNetworks](/azure/templates/microsoft.network/virtualnetworks): létrehozza a virtuális hálózatot. 
- [Microsoft. Network/virtualNetworks/Subnets](/azure/templates/microsoft.network/virtualnetworks/subnets): létrehozza az alhálózatot. 
- [Microsoft. DataMigration/Services](/azure/templates/microsoft.datamigration/services): központilag telepíti a Azure Database Migration Service egy példányát. 

További Azure Database Migration Services-sablonok a rövid útmutató [sablonjának](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Datamigration&pageNumber=1&sort=Popular)katalógusában találhatók.


## <a name="deploy-the-template"></a>A sablon üzembe helyezése

1. Kattintson az alábbi gombra az Azure-ba való bejelentkezéshez és egy sablon megnyitásához. A sablon létrehozza a Azure Database Migration Service egy példányát. 

   [![Üzembe helyezés az Azure-ban](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2f101-azure-database-migration-simple-deploy%2fazuredeploy.json)

2. Válassza ki vagy adja meg a következő értékeket.

    * **Előfizetés**: válasszon ki egy Azure-előfizetést.
    * **Erőforráscsoport**: válasszon ki egy meglévő erőforráscsoportot a legördülő listából, vagy válassza az **új létrehozása** lehetőséget az új erőforráscsoport létrehozásához. 
    * **Régió**: a hely, ahol az erőforrások telepítve lesznek.
    * **Szolgáltatás neve**: az új áttelepítési szolgáltatás neve.
    * **Hely**: az erőforráscsoport helye, az alapértelmezett érték marad `[resourceGroup().location]` .
    * **Vnet neve**: az új virtuális hálózat neve.
    * **Alhálózat neve**: a virtuális hálózathoz társított új alhálózat neve.



3. Válassza az **Áttekintés + létrehozás** lehetőséget. Miután a Azure Database Migration Service példánya sikeresen telepítve lett, értesítést kap. 


Az Azure Portalon helyezhető üzembe a sablon. A Azure Portalon kívül használhatja a Azure PowerShell, az Azure CLI és a REST API is. További információ az üzembe helyezési módszerekről: [sablonok üzembe helyezése](../azure-resource-manager/templates/deploy-powershell.md).

## <a name="review-deployed-resources"></a>Üzembe helyezett erőforrások áttekintése

Az Azure CLI használatával is ellenőrizhetők a telepített erőforrások. 


```azurecli-interactive
echo "Enter the resource group where your SQL Server VM exists:" &&
read resourcegroupName &&
az resource list --resource-group $resourcegroupName 
```


## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, törölje az erőforráscsoportot az Azure CLI vagy a Azure PowerShell használatával:

# <a name="cli"></a>[Parancssori felület](#tab/CLI)

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName &&
echo "Press [ENTER] to continue ..."
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
Remove-AzResourceGroup -Name $resourceGroupName
Write-Host "Press [ENTER] to continue..."
```

---

## <a name="next-steps"></a>További lépések

A sablonok létrehozásának folyamatát ismertető, lépésenkénti oktatóanyagért lásd:

> [!div class="nextstepaction"]
> [ Oktatóanyag: az első ARM-sablon létrehozása és üzembe helyezése](../azure-resource-manager/templates/template-tutorial-create-first-template.md)

A Azure Database Migration Service központi telepítésének egyéb módjaival kapcsolatban lásd: 
- [Azure Portal](quickstart-create-data-migration-service-portal.md)

További információ: [Azure Database Migration Service áttekintése](dms-overview.md)