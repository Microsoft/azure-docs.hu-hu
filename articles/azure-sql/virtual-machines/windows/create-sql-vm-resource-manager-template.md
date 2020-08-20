---
title: SQL Server VM létrehozása ARM-sablonnal
description: Megtudhatja, hogyan hozhat létre SQL Server az Azure-beli virtuális gépen (VM) egy Azure Resource Manager-sablon (ARM-sablon) használatával.
author: MashaMSFT
ms.topic: quickstart
ms.custom: subject-armqs
ms.author: mathoma
ms.date: 06/29/2020
ms.service: virtual-machines-sql
ms.openlocfilehash: 21909a9c7a1edda5059b9e83cf8dda243862b966
ms.sourcegitcommit: d18a59b2efff67934650f6ad3a2e1fe9f8269f21
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/20/2020
ms.locfileid: "88660207"
---
# <a name="quickstart-create-sql-server-vm-using-an-arm-template"></a>Rövid útmutató: SQL Server VM létrehozása ARM-sablonnal

Ezzel a Azure Resource Manager sablonnal (ARM-sablon) SQL Server helyezhet üzembe egy Azure-beli virtuális gépen (VM). 

[!INCLUDE [About Azure Resource Manager](../../../../includes/resource-manager-quickstart-introduction.md)]

Ha a környezet megfelel az előfeltételeknek, és már ismeri az ARM-sablonokat, kattintson az **Üzembe helyezés az Azure-ban** gombra. A sablon az Azure Portalon fog megnyílni.

[![Üzembe helyezés az Azure-ban](../../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2f101-sql-vm-new-storage%2fazuredeploy.json)

## <a name="prerequisites"></a>Előfeltételek

Az SQL Server VM ARM-sablonhoz a következők szükségesek:

- Az [Azure CLI](/cli/azure/install-azure-cli) és/vagy a [PowerShell](/powershell/scripting/install/installing-powershell)legújabb verziója. 
- Előre konfigurált [erőforráscsoport](../../../azure-resource-manager/management/manage-resource-groups-portal.md#create-resource-groups) egy előkészített [virtuális hálózattal](../../../virtual-network/quick-create-portal.md) és [alhálózattal](../../../virtual-network/virtual-network-manage-subnet.md#add-a-subnet).
- Azure-előfizetés. Ha még nem rendelkezik ilyennel, a Kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) .


## <a name="review-the-template"></a>A sablon áttekintése

Az ebben a gyorsútmutatóban használt sablon az [Azure-gyorssablonok](https://azure.microsoft.com/resources/templates/101-sql-vm-new-storage/) közül származik.

:::code language="json" source="~/quickstart-templates/101-sql-vm-new-storage/azuredeploy.json":::

Öt Azure-erőforrás van definiálva a sablonban: 

- [Microsoft. Network/nyilvános IP](/azure/templates/microsoft.network/publicipaddresses): létrehoz egy nyilvános IP-címet. 
- [Microsoft. Network/networkSecurityGroups](/azure/templates/microsoft.network/networksecuritygroups): létrehoz egy hálózati biztonsági csoportot. 
- [Microsoft. Network/networkInterfaces](/azure/templates/microsoft.network/networkinterfaces): konfigurálja a hálózati adaptert. 
- [Microsoft. számítás/virtualMachines](/azure/templates/microsoft.compute/virtualmachines): virtuális gép létrehozása az Azure-ban. 
- [Microsoft. SqlVirtualMachine/SqlVirtualMachines](/azure/templates/microsoft.sqlvirtualmachine/sqlvirtualmachines): regisztrálja a virtuális GÉPET az SQL VM erőforrás-szolgáltatóval. 

Az Azure-beli virtuálisgép-sablonokkal kapcsolatos további SQL Server a gyors üzembe helyezési [sablon galériájában](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Sqlvirtualmachine&pageNumber=1&sort=Popular)találhatók.


## <a name="deploy-the-template"></a>A sablon üzembe helyezése

1. Kattintson az alábbi gombra az Azure-ba való bejelentkezéshez és egy sablon megnyitásához. A sablon egy virtuális gépet hoz létre, amelynek a tervezett SQL Server verziója telepítve van, és regisztrálva van az SQL VM erőforrás-szolgáltatóban. 

   [![Üzembe helyezés az Azure-ban](../../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2f101-sql-vm-new-storage%2fazuredeploy.json)

2. Válassza ki vagy adja meg a következő értékeket.

    * **Előfizetés**: válasszon ki egy Azure-előfizetést.
    * **Erőforráscsoport**: a SQL Server VM előkészített erőforráscsoport. 
    * **Régió**: válasszon régiót.  Például: **USA középső régiója**.
    * **Virtuális gép neve**: adja meg SQL Server virtuális gép nevét. 
    * **Virtuális gép mérete**: válassza ki a virtuális gép megfelelő méretét a legördülő menüből.
    * **Meglévő Virtual Network neve**: adja meg a SQL Server VM felkészített virtuális hálózatának nevét. 
    * **Meglévő vnet erőforráscsoport**: adja meg azt az erőforráscsoportot, amelyben a virtuális hálózat elkészült. 
    * **Meglévő alhálózat neve**: az előkészített alhálózat neve. 
    * **Rendszerkép-ajánlat**: válassza ki az üzleti igényeinek leginkább megfelelő SQL Server és Windows Server-rendszerképet. 
    * **SQL SKU**: válassza ki az üzleti igényeinek leginkább megfelelő SQL Server SKU kiadását. 
    * **Rendszergazdai Felhasználónév**: a virtuális gép rendszergazdájához tartozó Felhasználónév. 
    * **Rendszergazdai jelszó**: a virtuális gép rendszergazdai fiókja által használt jelszó. 
    * **Tárolási munkaterhelés típusa**: az Ön vállalatának leginkább megfelelő számítási feladathoz tartozó tároló típusa. 
    * **SQL-adatlemezek száma**: az adatfájlok SQL Server használt lemezek száma.  
    * **Adatelérési út**: a SQL Server adatfájljainak elérési útja. 
    * **SQL-naplózási lemezek száma**: a naplófájlok SQL Server által használt lemezek száma. 
    * **Napló elérési útja**: a SQL Server naplófájlok elérési útja. 
    * **Hely**: az összes erőforrás helye, ennek az értéknek az alapértelmezettnek kell maradnia `[resourceGroup().location]` . 

3. Válassza a **Felülvizsgálat + létrehozás** lehetőséget. A SQL Server VM sikeres üzembe helyezését követően értesítést kap.

Az Azure Portalon helyezhető üzembe a sablon. A Azure Portalon kívül használhatja a Azure PowerShell, az Azure CLI és a REST API is. További információ az üzembe helyezési módszerekről: [sablonok üzembe helyezése](../../../azure-resource-manager/templates/deploy-powershell.md).

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
> [ Oktatóanyag: az első ARM-sablon létrehozása és üzembe helyezése](/azure/azure-resource-manager/templates/template-tutorial-create-first-template)

A SQL Server VM telepítésének egyéb módjairól a következő témakörben talál további információt: 
- [Azure Portal](create-sql-vm-portal.md)
- [PowerShell](create-sql-vm-powershell.md)

További tudnivalókat az Azure-beli [virtuális gépek SQL Server áttekintése](sql-server-on-azure-vm-iaas-what-is-overview.md)című témakörben talál.
