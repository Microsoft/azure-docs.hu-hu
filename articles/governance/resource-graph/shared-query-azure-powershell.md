---
title: 'Rövid útmutató: Megosztott lekérdezés létrehozása Azure PowerShell'
description: Ebben a rövid útmutatóban a lépéseket követve létrehozhat egy megosztott Resource Graph lekérdezést a Azure PowerShell.
ms.date: 01/11/2021
ms.topic: quickstart
ms.custom:
- devx-track-azurepowershell
- mode-api
ms.openlocfilehash: 9015b6df99bdd6f153194e8f4cbbe7658cf1d6dc
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107535802"
---
# <a name="quickstart-create-a-resource-graph-shared-query-using-azure-powershell"></a>Rövid útmutató: Megosztott Resource Graph létrehozása a Azure PowerShell

Ez a cikk azt ismerteti, hogyan hozhat létre megosztott Azure Resource Graph az [Az.ResourceGraph](/powershell/module/az.resourcegraph) PowerShell-modullal.

## <a name="prerequisites"></a>Előfeltételek

- Ha nem rendelkezik Azure-előfizetéssel, első lépésként mindössze néhány perc alatt létrehozhat egy [ingyenes](https://azure.microsoft.com/free/) fiókot.

[!INCLUDE [azure-powershell-requirements-no-header.md](../../../includes/azure-powershell-requirements-no-header.md)]

  > [!IMPORTANT]
  > Bár az **Az.ResourceGraph** PowerShell-modul előzetes verzióban érhető el, külön kell telepítenie a `Install-Module` parancsmag használatával.

  ```azurepowershell-interactive
  Install-Module -Name Az.ResourceGraph
  ```

- Ha több Azure-előfizetéssel rendelkezik, válassza ki a megfelelő előfizetést, amelyben az erőforrásokat ki kell számlázni. Válasszon ki egy adott előfizetést a [Set-AzContext](/powershell/module/az.accounts/set-azcontext) parancsmag használatával.

  ```azurepowershell-interactive
  Set-AzContext -SubscriptionId 00000000-0000-0000-0000-000000000000
  ```

## <a name="create-a-resource-graph-shared-query"></a>Megosztott Resource Graph létrehozása

Most, hogy az **Az.ResourceGraph** PowerShell-modul hozzá van adva a választott környezethez, ideje létrehozni egy Resource Graph lekérdezést. A megosztott lekérdezés egy Azure Resource Manager objektum, amely számára engedélyeket adhat vagy futtathat Azure Resource Graph Explorerben. A lekérdezés összefoglalja az összes erőforrás számát hely _szerint csoportosítva._

1. Hozzon létre egy erőforráscsoportot a [New-AzResourceGroup használatával](/powershell/module/az.resources/new-azresourcegroup) a megosztott Azure Resource Graph tárolására. Ennek az erőforráscsoportnak a neve , `resource-graph-queries` a hely pedig `westus2` .

   ```azurepowershell-interactive
   # Login first with `Connect-AzAccount` if not using Cloud Shell

   # Create the resource group
   New-AzResourceGroup -Name resource-graph-queries -Location westus2
   ```

1. Hozza létre Azure Resource Graph lekérdezést az **Az.ResourceGraph** PowerShell-modul és a [New-AzResourceGraphQuery](/powershell/module/az.resourcegraph/new-azresourcegraphquery) parancsmag használatával:

   ```azurepowershell-interactive
   # Create the Azure Resource Graph shared query
   $Params = @{
     Name = 'Summarize resources by location'
     ResourceGroupName = 'resource-graph-queries'
     Location = 'westus2'
     Description = 'This shared query summarizes resources by location for a pinnable map graphic.'
     Query = 'Resources | summarize count() by location'
   }
   New-AzResourceGraphQuery @Params
   ```

1. Sorolja fel az új erőforráscsoportban található megosztott lekérdezéseket. A [Get-AzResourceGraphQuery parancsmag](/powershell/module/az.resourcegraph/get-azresourcegraphquery) értéktömböt ad vissza.

   ```azurepowershell-interactive
   # List all the Azure Resource Graph shared queries in a resource group
   Get-AzResourceGraphQuery -ResourceGroupName resource-graph-queries
   ```

1. Ha csak egyetlen megosztott lekérdezési eredményt kap, használja a `Get-AzResourceGraphQuery` `Name` paramétert a paraméterrel.

   ```azurepowershell-interactive
   # Show a specific Azure Resource Graph shared query
   Get-AzResourceGraphQuery -ResourceGroupName resource-graph-queries -Name 'Summarize resources by location'
   ```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha el szeretné távolítani a megosztott Resource Graph lekérdezést és erőforráscsoportot az Azure-környezetből, ezt a következő parancsokkal használhatja:

- [Remove-AzResourceGraphQuery](/powershell/module/az.resourcegraph/remove-azresourcegraphquery)
- [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup)

```azurepowershell-interactive
# Delete the Azure Resource Graph shared query
Remove-AzResourceGraphQuery -ResourceGroupName resource-graph-queries -Name 'Summarize resources by location'

# Remove the resource group
# WARNING: This command deletes ALL resources you've added to this resource group
Remove-AzResourceGroup -Name resource-graph-queries
```

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban megosztott lekérdezést hozott létre a Resource Graph Azure PowerShell. Ha többet szeretne megtudni a Resource Graph nyelvről, folytassa a lekérdezési nyelv részleteit tartalmazó oldalon.

> [!div class="nextstepaction"]
> [További információ a lekérdezési nyelvről](./concepts/query-language.md)
