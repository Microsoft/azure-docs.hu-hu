---
title: Virtuálisgép-címkék kezelése Azure Stack Edge Pro GPU-eszközön Azure PowerShell használatával
description: Ismerteti, hogyan hozhat létre és kezelhet virtuálisgép-címkéket Azure Stack Edge-ben futó virtuális gépekhez Azure PowerShell használatával.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 04/06/2021
ms.author: alkohli
ms.openlocfilehash: be4348359e6b53c3e7454e9ab7c1af8ce8a7020a
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2021
ms.locfileid: "107305555"
---
# <a name="manage-vm-tags-on-azure-stack-edge-via-azure-powershell"></a>Virtuálisgép-címkék kezelése Azure Stack Edge-n keresztül Azure PowerShell

Ez a cikk azt ismerteti, hogyan címkézheti az Azure Stack Edge Pro GPU-eszközökön futó virtuális gépeket Azure PowerShell használatával.

## <a name="about-tags"></a>A címkék ismertetése

A címkék olyan, felhasználó által definiált kulcs-érték párok, amelyek egy erőforráshoz vagy egy erőforráscsoporthoz társíthatók. Az eszközön futó virtuális gépekre címkéket alkalmazhat, így logikailag rendszerezheti őket egy besorolásban. A címkéket a létrehozáskor vagy egy meglévő erőforráshoz való hozzáadáskor helyezheti el egy erőforráson. Alkalmazhatja például a nevet `Organization` és az értéket a `Engineering` szervezet mérnöki részlege által használt összes virtuális gépre.

A címkékkel kapcsolatos további információkért lásd: [címkék kezelése a AzureRM PowerShell](/powershell/module/azurerm.tags/?view=azurermps-6.13.0&preserve-view=true)használatával.

## <a name="prerequisites"></a>Előfeltételek

Mielőtt üzembe helyezhet egy virtuális gépet az eszközön a PowerShell használatával, győződjön meg a következőket:

- Hozzáférése van egy olyan ügyfélhez, amelyet az eszközhöz való kapcsolódáshoz fog használni.
    - Az ügyfél egy [támogatott operációs rendszert](azure-stack-edge-gpu-system-requirements.md#supported-os-for-clients-connected-to-device)futtat.
    - Az ügyfél az eszköz helyi Azure Resource Manager való kapcsolódásra van konfigurálva, mint a [Kapcsolódás a Azure Resource Managerhez eszközhöz](azure-stack-edge-gpu-connect-resource-manager.md)című részben leírtak szerint.


## <a name="verify-connection-to-local-azure-resource-manager"></a>Helyi Azure Resource Managerhoz való kapcsolódás ellenőrzése

[!INCLUDE [azure-stack-edge-gateway-verify-azure-resource-manager-connection](../../includes/azure-stack-edge-gateway-verify-azure-resource-manager-connection.md)]


## <a name="add-a-tag-to-a-vm"></a>Címke hozzáadása egy virtuális géphez

1. Állítsa be a paramétereket.

    ```powershell
    $VMName = <VM Name>
    $VMRG = <VM Resource Group>
    $TagName = <Tag Name>
    $TagValue = <Tag Value>   
    ```

    Íme egy példa a kimenetre:

    ```powershell
    PS C:\WINDOWS\system32> $VMName = "myasetestvm1"
    PS C:\WINDOWS\system32> $VMRG = "myaserg2"
    PS C:\WINDOWS\system32> $TagName = "Organization"
    PS C:\WINDOWS\system32> $TagValue = "Sales"
    ```

2. Szerezze be a virtuálisgép-objektumot és annak címkéit.

   ```powershell
   $VirtualMachine = Get-AzureRmVM -ResourceGroupName $VMRG -Name $VMName
   $tags = $VirtualMachine.Tags
   ```

3. Adja hozzá a címkét, és frissítse a virtuális gépet. A virtuális gép frissítése néhány percet is igénybe vehet.

    A nem kötelező **-kényszerített** jelző használatával felhasználói megerősítés nélkül futtathatja a parancsot.

    ```powershell
    $tags.Add($TagName, $TagValue)
    Set-AzureRmResource -ResourceId $VirtualMachine.Id -Tag $tags [-Force]
    ```

    Íme egy példa a kimenetre:

    ```powershell
    PS C:\WINDOWS\system32> $VirtualMachine = Get-AzureRMVM -ResourceGroupName $VMRG -Name $VMName
    PS C:\WINDOWS\system32> $tags = $VirtualMachine.Tags
    PS C:\WINDOWS\system32> $tags.Add($TagName, $TagValue)
    PS C:\WINDOWS\system32> Set-AzureRmResource -ResourceID $VirtualMachine.ID -Tag $tags -Force
    
    Name              : myasetestvm1
    ResourceId        : /subscriptions/992601bc-b03d-4d72-598e-d24eac232122/resourceGroups/myaserg2/providers/Microsoft.Compute/virtua
                        lMachines/myasetestvm1
    ResourceName      : myasetestvm1
    ResourceType      : Microsoft.Compute/virtualMachines
    ResourceGroupName : myaserg2
    Location          : dbelocal
    SubscriptionId    : 992601bc-b03d-4d72-598e-d24eac232122
    Tags              : {Organization}
    Properties        : @{vmId=958c0baa-e143-4d8a-82bd-9c6b1ba45e86; hardwareProfile=; storageProfile=; osProfile=; networkProfile=;
                        provisioningState=Succeeded}
    
    PS C:\WINDOWS\system32>
    ```

További információ: [Add-AzureRMTag](/powershell/module/azurerm.tags/remove-azurermtag?view=azurermps-6.13.0&preserve-view=true).

## <a name="view-tags-of-a-vm"></a>Virtuális gép címkéinak megtekintése

Megtekintheti az eszközön futó adott virtuális gépre alkalmazott címkéket. 

1. Adja meg a megtekinteni kívánt címkével rendelkező virtuális géphez társított paramétereket.

   ```powershell
   $VMName = <VM Name>
   $VMRG = <VM Resource Group>
   ```
    Íme egy példa a kimenetre:

    ```powershell
    PS C:\WINDOWS\system32> $VMName = "myasetestvm1"
    PS C:\WINDOWS\system32> $VMRG = "myaserg2"
    PS C:\WINDOWS\system32> $TagName = "Organization"
    PS C:\WINDOWS\system32> $TagValue = "Sales"
    ```
1. Szerezze be a virtuálisgép-objektumot, és tekintse meg a címkéit.

   ```powershell
   $VirtualMachine = Get-AzureRmVM -ResourceGroupName $VMRG -Name $VMName
   $VirtualMachine.Tags
   ```
    Íme egy példa a kimenetre:

    ```powershell
    PS C:\WINDOWS\system32> $VirtualMachine = Get-AzureRMVM -ResourceGroupName $VMRG -Name $VMName
    PS C:\WINDOWS\system32> $VirtualMachine

    ResourceGroupName : myaserg2
    Id                : /subscriptions/992601bc-b03d-4d72-598e-d24eac232122/resourceGroups/myaserg2/providers/Microsoft.Compute/virtua
    lMachines/myasetestvm1
    VmId              : 958c0baa-e143-4d8a-82bd-9c6b1ba45e86
    Name              : myasetestvm1
    Type              : Microsoft.Compute/virtualMachines
    Location          : dbelocal
    Tags              : {"Organization":"Sales"}
    HardwareProfile   : {VmSize}
    NetworkProfile    : {NetworkInterfaces}
    OSProfile         : {ComputerName, AdminUsername, LinuxConfiguration, Secrets}
    ProvisioningState : Succeeded
    StorageProfile    : {ImageReference, OsDisk, DataDisks}
    
    PS C:\WINDOWS\system32>
    ```
## <a name="view-tags-for-all-resources"></a>Címkék megtekintése az összes erőforráshoz

Ha meg szeretné tekinteni a címkék aktuális listáját az eszköz helyi Azure Resource Manager előfizetésében (amely eltér az Azure-előfizetéstől), használja az `Get-AzureRMTag` parancsot.


Íme egy példa kimenet, ha több virtuális gép fut az eszközön, és az összes címkét meg szeretné tekinteni az összes virtuális gépen.

```powershell
PS C:\WINDOWS\system32> Get-AzureRMTag

Name         Count
----         -----
Organization 3

PS C:\WINDOWS\system32>
```

Az előző kimenet azt jelzi, hogy három `Organization` címke található az eszközön futó virtuális gépeken.

További részletek megtekintéséhez használja a (z `-Detailed` ) paramétert.

```powershell
PS C:\WINDOWS\system32> Get-AzureRMTag -Detailed |fl

Name        : Organization
ValuesTable :
              Name         Count
              ===========  =====
              Engineering  2
              Sales        1

Count       : 3
Values      : {Engineering, Sales}

PS C:\WINDOWS\system32>
```

Az előző kimenet azt jelzi, hogy a három címkén kívül 2 virtuális gép címkézve van, `Engineering` és az egyik a következőhöz tartozóként van megjelölve: `Sales` .

## <a name="remove-a-tag-from-a-vm"></a>Címke eltávolítása egy virtuális gépről

1. Állítsa be a paramétereket. 

    ```powershell
    $VMName = <VM Name>
    $VMRG = <VM Resource Group>
    $TagName = <Tag Name>
   ``` 
    Íme egy példa a kimenetre:

    ```powershell
    PS C:\WINDOWS\system32> $VMName = "myaselinuxvm1"
    PS C:\WINDOWS\system32> $VMRG = "myaserg1"
    PS C:\WINDOWS\system32> $TagName = "Organization" 
    ```
2. Szerezze be a virtuálisgép-objektumot.

    ```powershell
    $VirtualMachine = Get-AzureRmVM -ResourceGroupName $VMRG -Name $VMName
    $VirtualMachine   
    ```   

    Íme egy példa a kimenetre:

    ```powershell
    PS C:\WINDOWS\system32> $VirtualMachine = Get-AzureRMVM -ResourceGroupName $VMRG -Name $VMName
    ResourceGroupName : myaserg1
    Id                : /subscriptions/992601bc-b03d-4d72-598e-d24eac232122/resourceGroups/myaserg1/providers/Microsoft.Compute/virtualMachines/myaselinuxvm1
    VmId              : 290b3fdd-0c99-4905-9ea1-cf93cd6f25ee
    Name              : myaselinuxvm1
    Type              : Microsoft.Compute/virtualMachines
    Location          : dbelocal
    Tags              : {"Organization":"Engineering"}
    HardwareProfile   : {VmSize}
    NetworkProfile    : {NetworkInterfaces}
    OSProfile         : {ComputerName, AdminUsername, LinuxConfiguration, Secrets}
    ProvisioningState : Succeeded
    StorageProfile    : {ImageReference, OsDisk, DataDisks}
                                                                              PS C:\WINDOWS\system32> 
    ```
3. Távolítsa el a címkét, és frissítse a virtuális gépet. A választható `-Force` jelző használatával a parancsot felhasználói megerősítés nélkül futtathatja.

    ```powershell
    $tags = $VirtualMachine.Tags
    $tags.Remove($TagName)
    Set-AzureRmResource -ResourceId $VirtualMachine.Id -Tag $tags [-Force]
    ```
    Íme egy példa a kimenetre:

    ```powershell
    PS C:\WINDOWS\system32> $tags = $Virtualmachine.Tags
    PS C:\WINDOWS\system32> $tags
    Key          Value
    ---          -----
    Organization Engineering
    PS C:\WINDOWS\system32> $tags.Remove($TagName)
    True
    PS C:\WINDOWS\system32> Set-AzureRMResource -ResourceID $VirtualMachine.ID -Tag $tags -Force
    Name              : myaselinuxvm1
    ResourceId        : /subscriptions/992601bc-b03d-4d72-598e-d24eac232122/resourceGrou
                        ps/myaserg1/providers/Microsoft.Compute/virtualMachines/myaselin
                        uxvm1
    ResourceName      : myaselinuxvm1
    ResourceType      : Microsoft.Compute/virtualMachines
    ResourceGroupName : myaserg1
    Location          : dbelocal
    SubscriptionId    : 992601bc-b03d-4d72-598e-d24eac232122
    Tags              : {}
    Properties        : @{vmId=290b3fdd-0c99-4905-9ea1-cf93cd6f25ee; hardwareProfile=;
                        storageProfile=; osProfile=; networkProfile=;
                        provisioningState=Succeeded}
    PS C:\WINDOWS\system32>
    ```


## <a name="next-steps"></a>Következő lépések

Megtudhatja, hogyan [kezelheti a címkéket a AzureRM PowerShell](/powershell/module/azurerm.tags/?view=azurermps-6.13.0&preserve-view=true)használatával.
