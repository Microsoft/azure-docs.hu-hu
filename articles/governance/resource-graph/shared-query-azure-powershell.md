---
title: 'Gyors útmutató: megosztott lekérdezés létrehozása Azure PowerShell'
description: Ebben a rövid útmutatóban egy erőforrás-gráf megosztott lekérdezés létrehozásához szükséges lépéseket követve Azure PowerShell használatával.
ms.date: 01/11/2021
ms.topic: quickstart
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 2b771253b1dea4bd1d2913bf7c48062112019a19
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "99981544"
---
# <a name="quickstart-create-a-resource-graph-shared-query-using-azure-powershell"></a>Gyors útmutató: erőforrás-gráf megosztott lekérdezés létrehozása Azure PowerShell használatával

Ez a cikk azt ismerteti, hogyan hozhat létre Azure Resource Graph-beli megosztott lekérdezést az az [. ResourceGraph](/powershell/module/az.resourcegraph) PowerShell-modul használatával.

## <a name="prerequisites"></a>Előfeltételek

- Ha nem rendelkezik Azure-előfizetéssel, első lépésként mindössze néhány perc alatt létrehozhat egy [ingyenes](https://azure.microsoft.com/free/) fiókot.

[!INCLUDE [azure-powershell-requirements-no-header.md](../../../includes/azure-powershell-requirements-no-header.md)]

  > [!IMPORTANT]
  > Míg az az **. ResourceGraph** PowerShell-modul előzetes verzióban érhető el, a parancsmaggal külön kell telepítenie `Install-Module` .

  ```azurepowershell-interactive
  Install-Module -Name Az.ResourceGraph
  ```

- Ha több Azure-előfizetéssel rendelkezik, válassza ki a megfelelő előfizetést, amelyben az erőforrásokat számlázni kell. Válasszon ki egy adott előfizetést a [set-AzContext](/powershell/module/az.accounts/set-azcontext) parancsmag használatával.

  ```azurepowershell-interactive
  Set-AzContext -SubscriptionId 00000000-0000-0000-0000-000000000000
  ```

## <a name="create-a-resource-graph-shared-query"></a>Erőforrás-gráf megosztott lekérdezésének létrehozása

Ha az az **. ResourceGraph** PowerShell-modullal bővült a környezete, akkor itt az ideje, hogy létrehozzon egy Resource Graph-beli megosztott lekérdezést. A megosztott lekérdezés egy Azure Resource Manager objektum, amely engedélyt adhat az Azure Resource Graph Explorerben, vagy futtathatja azt. A lekérdezés a _hely_ szerint csoportosított összes erőforrás számát összegzi.

1. Hozzon létre egy erőforráscsoportot a [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) az Azure Resource Graph megosztott lekérdezés tárolásához. Ez az erőforráscsoport neve `resource-graph-queries` , a helye pedig `westus2` .

   ```azurepowershell-interactive
   # Login first with `Connect-AzAccount` if not using Cloud Shell

   # Create the resource group
   New-AzResourceGroup -Name resource-graph-queries -Location westus2
   ```

1. Hozza létre az Azure Resource Graph megosztott lekérdezését az az **. ResourceGraph** PowerShell-modul és a [New-AzResourceGraphQuery](/powershell/module/az.resourcegraph/new-azresourcegraphquery) parancsmag használatával:

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

1. Az új erőforráscsoport megosztott lekérdezéseit sorolja fel. A [Get-AzResourceGraphQuery](/powershell/module/az.resourcegraph/get-azresourcegraphquery) parancsmag értékek tömbjét adja vissza.

   ```azurepowershell-interactive
   # List all the Azure Resource Graph shared queries in a resource group
   Get-AzResourceGraphQuery -ResourceGroupName resource-graph-queries
   ```

1. Ha csak egyetlen megosztott lekérdezési eredményt szeretne kapni, használja a `Get-AzResourceGraphQuery` `Name` paramétert.

   ```azurepowershell-interactive
   # Show a specific Azure Resource Graph shared query
   Get-AzResourceGraphQuery -ResourceGroupName resource-graph-queries -Name 'Summarize resources by location'
   ```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha el szeretné távolítani az erőforrás-gráf megosztott lekérdezését és az erőforráscsoportot az Azure-környezetből, ezt az alábbi parancsokkal végezheti el:

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

Ebben a rövid útmutatóban létrehozott egy Resource Graph megosztott lekérdezést Azure PowerShell használatával. Ha többet szeretne megtudni az erőforrás-gráf nyelvéről, folytassa a lekérdezés nyelvének részletei lapon.

> [!div class="nextstepaction"]
> [További információ a lekérdezési nyelvről](./concepts/query-language.md)