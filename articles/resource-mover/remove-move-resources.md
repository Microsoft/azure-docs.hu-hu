---
title: Erőforrások eltávolítása a Azure Resource Mover
description: Megtudhatja, hogyan távolíthat el erőforrásokat egy áthelyezési gyűjteményből a Azure Resource Mover.
manager: evansma
author: rayne-wiselman
ms.service: resource-move
ms.topic: how-to
ms.date: 02/22/2020
ms.author: raynew
ms.openlocfilehash: 067aa245223bce0e8fa009b88af760a26f7ce3dc
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107739025"
---
# <a name="manage-move-collections-and-resource-groups"></a>Áthelyezési gyűjtemények és erőforráscsoportok kezelése

Ez a cikk azt ismerteti, hogyan távolíthat el erőforrásokat egy áthelyezési gyűjteményből, illetve hogyan távolíthat el egy áthelyezési gyűjteményt/erőforráscsoportot a [Azure Resource Mover.](overview.md) Az áthelyezési gyűjtemények az Azure-erőforrások Azure-régiók közötti áthelyezésekor használatosak.

## <a name="remove-a-resource-portal"></a>Erőforrás eltávolítása (portál)

Az áthelyezési gyűjtemények erőforrásait a resource Mover portálon a következőképpen távolíthatja el:

1. A **Régiók között beállításban** jelölje ki a gyűjteményből eltávolítani kívánt összes erőforrást, majd válassza az Eltávolítás **lehetőséget.** 

    ![Az eltávolítani kívánt gomb](./media/remove-move-resources/portal-select-resources.png)

2. Az **Erőforrások eltávolítása menüben** kattintson az **Eltávolítás elemre.**

    ![Az erőforrások áthelyezési gyűjteményből való eltávolítását kiválasztó gomb](./media/remove-move-resources/remove-portal.png)

## <a name="remove-a-move-collectionresource-group-portal"></a>Áthelyezési gyűjtemény/erőforráscsoport eltávolítása (portál)

Az áthelyezési gyűjteményt/erőforráscsoportot a portálon távolíthatja el.

1. Az erőforrások gyűjteményből való eltávolításához kövesse a fenti eljárás utasításait. Ha egy erőforráscsoportot távolít el, győződjön meg arról, hogy az nem tartalmaz erőforrásokat.
2. Törölje az áthelyezési gyűjteményt vagy erőforráscsoportot.  

## <a name="remove-a-resource-powershell"></a>Erőforrás eltávolítása (PowerShell)

A PowerShell-parancsmagok használatával egyetlen erőforrást távolíthat el a MoveCollection-ból, vagy több erőforrást is.

### <a name="remove-a-single-resource"></a>Egyetlen erőforrás eltávolítása

Távolítsa el az erőforrást (a példánkban a *psdemorm-vnet* virtuális hálózatot) a következőképpen:

```azurepowershell-interactive
# Remove a resource using the resource ID
Remove-AzResourceMoverMoveResource -ResourceGroupName "RG-MoveCollection-demoRMS" -MoveCollectionName "PS-centralus-westcentralus-demoRMS" -Name "psdemorm-vnet"
```
**Kimenet a parancsmag futtatása után**

![Kimeneti szöveg egy erőforrás áthelyezése utáni gyűjteményből való eltávolítása után](./media/remove-move-resources/powershell-remove-single-resource.png)

### <a name="remove-multiple-resources"></a>Több erőforrás eltávolítása

A következőképpen távolíthat el több erőforrást:

1. Függőségek ellenőrzése:

    ````azurepowershell-interactive
    $resp = Invoke-AzResourceMoverBulkRemove -ResourceGroupName "RG-MoveCollection-demoRMS" -MoveCollectionName "PS-centralus-westcentralus-demoRMS"  -MoveResource $('psdemorm-vnet') -ValidateOnly
    ```

    **Output after running cmdlet**

    ![Output text after removing multiple resources from a move collection](./media/remove-move-resources/remove-multiple-validate-dependencies.png)

2. Retrieve the dependent resources that need to be removed (along with our example virtual network psdemorm-vnet):

    ````azurepowershell-interactive
    $resp.AdditionalInfo[0].InfoMoveResource
    ```

    **Output after running cmdlet**

    ![Output text after removing multiple resources from a move collection](./media/remove-move-resources/remove-multiple-get-dependencies.png)


3. Remove all resources, along with the virtual network:

    
    ````azurepowershell-interactive
    Invoke-AzResourceMoverBulkRemove -ResourceGroupName "RG-MoveCollection-demoRMS" -MoveCollectionName "PS-centralus-westcentralus-demoRMS"  -MoveResource $('PSDemoVM','psdemovm111', 'PSDemoRM-vnet','PSDemoVM-nsg')
    ```

    **Output after running cmdlet**

    ![Output text after removing all resources from a move collection](./media/remove-move-resources/remove-multiple-all.png)


## Remove a collection (PowerShell)

Remove an entire move collection from the subscription, as follows:

1. Follow the instructions above to remove resources in the collection using PowerShell.
2. Run:

    ```azurepowershell-interactive
    Remove-AzResourceMoverMoveCollection -ResourceGroupName "RG-MoveCollection-demoRMS" -MoveCollectionName "PS-centralus-westcentralus-demoRMS"
    ```

    **Output after running cmdlet**
    
    ![Output text after removing a move collection](./media/remove-move-resources/remove-collection.png)

## VM resource state after removing

What happens when you remove a VM resource from a move collection depends on the resource state, as summarized in the table.

###  Remove VM state
**Resource state** | **VM** | **Networking**
--- | --- | --- 
**Added to move collection** | Delete from move collection. | Delete from move collection. 
**Dependencies resolved/prepare pending** | Delete from move collection  | Delete from move collection. 
**Prepare in progress**<br/> (or any other state in progress) | Delete operation fails with error.  | Delete operation fails with error.
**Prepare failed** | Delete from the move collection.<br/>Delete anything created in the target region, including replica disks. <br/><br/> Infrastructure resources created during the move need to be deleted manually. | Delete from the move collection.  
**Initiate move pending** | Delete from move collection.<br/><br/> Delete anything created in the target region, including VM, replica disks etc.  <br/><br/> Infrastructure resources created during the move need to be deleted manually. | Delete from move collection.
**Initiate move failed** | Delete from move collection.<br/><br/> Delete anything created in the target region, including VM, replica disks etc.  <br/><br/> Infrastructure resources created during the move need to be deleted manually. | Delete from move collection.
**Commit pending** | We recommend that you discard the move so that the target resources are deleted first.<br/><br/> The resource goes back to the **Initiate move pending** state, and you can continue from there. | We recommend that you discard the move so that the target resources are deleted first.<br/><br/> The resource goes back to the **Initiate move pending** state, and you can continue from there. 
**Commit failed** | We recommend that you discard the  so that the target resources are deleted first.<br/><br/> The resource goes back to the **Initiate move pending** state, and you can continue from there. | We recommend that you discard the move so that the target resources are deleted first.<br/><br/> The resource goes back to the **Initiate move pending** state, and you can continue from there.
**Discard completed** | The resource goes back to the **Initiate move pending** state.<br/><br/> It's deleted from the move collection, along with anything created at target - VM, replica disks, vault etc.  <br/><br/> Infrastructure resources created during the move need to be deleted manually. <br/><br/> Infrastructure resources created during the move need to be deleted manually. |  The resource goes back to the **Initiate move pending** state.<br/><br/> It's deleted from the move collection.
**Discard failed** | We recommend that you discard the moves so that the target resources are deleted first.<br/><br/> After that, the resource goes back to the **Initiate move pending** state, and you can continue from there. | We recommend that you discard the moves so that the target resources are deleted first.<br/><br/> After that, the resource goes back to the **Initiate move pending** state, and you can continue from there.
**Delete source pending** | Deleted from the move collection.<br/><br/> It doesn't delete anything created in the target region.  | Deleted from the move collection.<br/><br/> It doesn't delete anything created in the target region.
**Delete source failed** | Deleted from the move collection.<br/><br/> It doesn't delete anything created in the target region. | Deleted from the move collection.<br/><br/> It doesn't delete anything created in the target region.
**Move completed** | Deleted from the move collection.<br/><br/> It doesn't delete anything created in the target or source region. |  Deleted from the move collection.<br/><br/> It doesn't delete anything created in the target or source region.

## SQL resource state after removing

What happens when you remove an Azure SQL resource from a move collection depends on the resource state, as summarized in the table.

**Resource state** | **SQL** 
--- | --- 
**Added to move collection** | Delete from move collection. 
**Dependencies resolved/prepare pending** | Delete from move collection 
**Prepare in progress**<br/> (or any other state in progress)  | Delete operation fails with error. 
**Prepare failed** | Delete from move collection<br/><br/>Delete anything created in the target region. 
**Initiate move pending** |  Delete from move collection<br/><br/>Delete anything created in the target region. The SQL database exists at this point and will be deleted. 
**Initiate move failed** | Delete from move collection<br/><br/>Delete anything created in the target region. The SQL database exists at this point and must be deleted. 
**Commit pending** | We recommend that you discard the move so that the target resources are deleted first.<br/><br/> The resource goes back to the **Initiate move pending** state, and you can continue from there.
**Commit failed** | We recommend that you discard the move so that the target resources are deleted first.<br/><br/> The resource goes back to the **Initiate move pending** state, and you can continue from there. 
**Discard completed** |  The resource goes back to the **Initiate move pending** state.<br/><br/> It's deleted from the move collection, along with anything created at target, including SQL databases. 
**Discard failed** | We recommend that you discard the moves so that the target resources are deleted first.<br/><br/> After that, the resource goes back to the **Initiate move pending** state, and you can continue from there. 
**Delete source pending** | Deleted from the move collection.<br/><br/> It doesn't delete anything created in the target region. 
**Delete source failed** | Deleted from the move collection.<br/><br/> It doesn't delete anything created in the target region. 
**Move completed** | Deleted from the move collection.<br/><br/> It doesn't delete anything created in the target or source region.

## Next steps

Try [moving a VM](tutorial-move-region-virtual-machines.md) to another region with Resource Mover.
