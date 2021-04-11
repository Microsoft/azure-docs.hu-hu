---
title: Virtuálisgép-rendszerképek létrehozása a Windows VHD speciális rendszerképéről az Azure Stack Edge Pro GPU-eszközhöz
description: Ismerteti, hogyan lehet virtuálisgép-lemezképeket létrehozni a speciális rendszerképekből a Windows VHD-ről vagy egy VHDX kezdve. Ezzel a speciális képpel olyan virtuálisgép-lemezképeket hozhat létre, amelyeket az Azure Stack Edge Pro GPU-eszközön üzembe helyezett virtuális gépekhez használhat.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 03/30/2021
ms.author: alkohli
ms.openlocfilehash: d03aeb9759fb321b580fa65e06dc09ccde4a44a0
ms.sourcegitcommit: b0557848d0ad9b74bf293217862525d08fe0fc1d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/07/2021
ms.locfileid: "106556130"
---
# <a name="deploy-a-vm-from-a-specialized-image-on-your-azure-stack-edge-pro-device-via-azure-powershell"></a>Virtuális gép üzembe helyezése egy speciális rendszerképből az Azure Stack Edge Pro-eszközön a Azure PowerShell használatával 

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Ez a cikk azokat a lépéseket ismerteti, amelyek szükségesek egy virtuális gép (VM) üzembe helyezéséhez a Azure Stack Edge Pro-eszközön egy speciális rendszerképből. 

## <a name="about-specialized-images"></a>A speciális rendszerképek ismertetése

A Windows rendszerű virtuális merevlemezek vagy VHDX egy *speciális* rendszerkép vagy *általánosított* rendszerkép létrehozására használhatók. A következő táblázat összefoglalja a *speciális* és az *általánosított* lemezképek közötti főbb különbségeket.


|Lemezkép típusa  |Általánosított  |Specializált  |
|---------|---------|---------|
|Cél     |Bármely rendszeren üzembe helyezhető         | Adott rendszerre irányul        |
|Telepítés a rendszerindítás után     | A telepítő szükséges a virtuális gép első indításakor.          | A telepítés nem szükséges. <br> A platform bekapcsolja a virtuális gépet.        |
|Konfiguráció     |Állomásnév, rendszergazda-felhasználó és más virtuálisgép-specifikus beállítások szükségesek.         |Előre konfigurálva.         |
|A következőhöz használatos     |Hozzon létre több új virtuális gépet ugyanabból a rendszerképből.         |Egy adott gép migrálása vagy egy virtuális gép visszaállítása a korábbi biztonsági mentésből.         |


Ez a cikk a speciális lemezképből történő üzembe helyezéshez szükséges lépéseket ismerteti. Általánosított lemezképből történő üzembe helyezéssel kapcsolatban lásd: az [általánosított Windows VHD használata](azure-stack-edge-gpu-prepare-windows-vhd-generalized-image.md) az eszközhöz.


## <a name="vm-image-workflow"></a>VM-rendszerkép munkafolyamata

A virtuális gépek speciális lemezképből történő üzembe helyezésének magas szintű munkafolyamata a következő:

1. Másolja a VHD-t egy helyi Storage-fiókba az Azure Stack Edge Pro GPU-eszközön.
1. Hozzon létre egy új felügyelt lemezt a VHD-ből.
1. Hozzon létre egy új virtuális gépet a felügyelt lemezről, és csatolja a felügyelt lemezt.


## <a name="prerequisites"></a>Előfeltételek

Mielőtt üzembe helyezhet egy virtuális gépet az eszközön a PowerShell használatával, győződjön meg a következőket:

- Hozzáférése van egy olyan ügyfélhez, amelyet az eszközhöz való kapcsolódáshoz fog használni.
    - Az ügyfél egy [támogatott operációs rendszert](azure-stack-edge-gpu-system-requirements.md#supported-os-for-clients-connected-to-device)futtat.
    - Az ügyfél az eszköz helyi Azure Resource Manager való kapcsolódásra van konfigurálva, mint a [Kapcsolódás a Azure Resource Managerhez eszközhöz](azure-stack-edge-gpu-connect-resource-manager.md)című részben leírtak szerint.

## <a name="verify-the-local-azure-resource-manager-connection"></a>A helyi Azure Resource Manager-kapcsolatok ellenőrzése

Ellenőrizze, hogy az ügyfél tud-e csatlakozni a helyi Azure Resource Managerhoz. 

1. Helyi eszköz API-k hívása a hitelesítéshez:

    ```powershell
    Login-AzureRMAccount -EnvironmentName <Environment Name>
    ```

2. Adja meg a felhasználónevet `EdgeArmUser` és a jelszót Azure Resource Manager használatával történő kapcsolódáshoz. Ha nem emlékszik a jelszóra, [állítsa Alaphelyzetbe Azure Resource Manager jelszavát](azure-stack-edge-gpu-set-azure-resource-manager-password.md) , és ezt a jelszót használja a bejelentkezéshez.
 

## <a name="deploy-vm-from-specialized-image"></a>Virtuális gép üzembe helyezése speciális lemezképből

A következő szakaszokban részletes útmutatást talál a virtuális gép speciális lemezképből történő üzembe helyezéséhez.

## <a name="copy-vhd-to-local-storage-account-on-device"></a>Virtuális merevlemez másolása helyi Storage-fiókba az eszközön

A virtuális merevlemez helyi Storage-fiókba való másolásához kövesse az alábbi lépéseket:

1. Másolja a forrás VHD-t egy helyi blob Storage-fiókba az Azure Stack Edge-ben. 

1. Jegyezze fel az eredményül kapott URI-t. Ezt az URI-t egy későbbi lépésben fogja használni.
    
    Helyi Storage-fiók létrehozásához és eléréséhez tekintse meg a [Storage-fiók létrehozása](azure-stack-edge-gpu-deploy-virtual-machine-powershell.md#create-a-storage-account) a [VHD feltöltésével](azure-stack-edge-gpu-deploy-virtual-machine-powershell.md#upload-a-vhd) című cikket: virtuális [gépek telepítése a Azure stack peremhálózati eszközön Azure PowerShell használatával](azure-stack-edge-gpu-deploy-virtual-machine-powershell.md). 

## <a name="create-a-managed-disk-from-vhd"></a>Felügyelt lemez létrehozása VHD-ből

Az alábbi lépéseket követve hozzon létre egy felügyelt lemezt egy virtuális merevlemezről, amelyet korábban feltöltött a Storage-fiókba:

1. Állítsa be a paramétereket.

    ```powershell
    $VhdURI = <URI of VHD in local storage account>
    $DiskRG = <managed disk resource group>
    $DiskName = <managed disk name>    
    ```
    Íme egy példa a kimenetre.

    ```powershell
    PS C:\WINDOWS\system32> $VHDURI = "https://myasevmsa.blob.myasegpudev.wdshcsso.com/vhds/WindowsServer2016Datacenter.vhd"
    PS C:\WINDOWS\system32> $DiskRG = "myasevm1rg"
    PS C:\WINDOWS\system32> $DiskName = "myasemd1"
    ```
1. Hozzon létre egy új felügyelt lemezt.

    ```powershell
    $DiskConfig = New-AzureRmDiskConfig -Location DBELocal -CreateOption Import -SourceUri $VhdURI
    $disk = New-AzureRMDisk -ResourceGroupName $DiskRG -DiskName $DiskName -Disk $DiskConfig
    ```

    Íme egy példa a kimenetre. Az itt megadott hely a helyi Storage-fiók helye, és mindig `DBELocal` az Azure stack Edge Pro GPU-eszközön található összes helyi Storage-fiókra vonatkozik. 

    ```powershell
    PS C:\WINDOWS\system32> $DiskConfig = New-AzureRmDiskConfig -Location DBELocal -CreateOption Import -SourceUri $VHDURI
    PS C:\WINDOWS\system32> $disk = New-AzureRMDisk -ResourceGroupName $DiskRG -DiskName $DiskName -Disk $DiskConfig
    PS C:\WINDOWS\system32>    
    ```
## <a name="create-a-vm-from-managed-disk"></a>Virtuális gép létrehozása felügyelt lemezről

A virtuális gép felügyelt lemezről történő létrehozásához kövesse az alábbi lépéseket:

1. Állítsa be a paramétereket.

    ```powershell
    $NicRG = <NIC resource group>
    $NicName = <NIC name>
    $IPConfigName = <IP config name>
    $PrivateIP = <IP address> #Optional
    
    $VMRG = <VM resource group>
    $VMName = <VM name>
    $VMSize = <VM size> 
    ```

    >[!NOTE]
    > A `PrivateIP` paraméter megadása nem kötelező. Ezzel a paraméterrel statikus IP-címet rendelhet hozzá, az alapértelmezett érték a DHCP protokollt használó dinamikus IP-cím.

    Íme egy példa a kimenetre. Ebben a példában ugyanazt az erőforráscsoportot adja meg az összes virtuálisgép-erőforráshoz, de szükség esetén külön erőforráscsoportokat hozhat létre az erőforrásokhoz.

    ```powershell
    PS C:\WINDOWS\system32> $NicRG = "myasevm1rg"
    PS C:\WINDOWS\system32> $NicName = "myasevmnic1"
    PS C:\WINDOWS\system32> $IPConfigName = "myaseipconfig1" 

    PS C:\WINDOWS\system32> $VMRG = "myasevm1rg"
    PS C:\WINDOWS\system32> $VMName = "myasetestvm1"
    PS C:\WINDOWS\system32> $VMSize = "Standard_D1_v2"   
    ```

1. Szerezze be a virtuális hálózat adatait, és hozzon létre egy új hálózati adaptert.

    Ez a példa azt feltételezi, hogy egyetlen hálózati adaptert hoz létre az alapértelmezett `ASEVNET` erőforráscsoporthoz társított alapértelmezett virtuális hálózaton `ASERG` . Ha szükséges, alternatív virtuális hálózatot is megadhat, vagy létrehozhat több hálózati adaptert is. További információ: [hálózati adapter hozzáadása virtuális géphez a Azure Portal használatával](azure-stack-edge-gpu-manage-virtual-machine-network-interfaces-portal.md).

    ```powershell
    $armVN = Get-AzureRMVirtualNetwork -Name ASEVNET -ResourceGroupName ASERG
    $ipConfig = New-AzureRmNetworkInterfaceIpConfig -Name $IPConfigName -SubnetId $armVN.Subnets[0].Id [-PrivateIpAddress $PrivateIP]
    $nic = New-AzureRmNetworkInterface -Name $NicName -ResourceGroupName $NicRG -Location DBELocal -IpConfiguration $ipConfig
    ```

    Íme egy példa a kimenetre.

    ```powershell
    PS C:\WINDOWS\system32> $armVN = Get-AzureRMVirtualNetwork -Name ASEVNET -ResourceGroupName ASERG
    PS C:\WINDOWS\system32> $ipConfig = New-AzureRmNetworkInterfaceIpConfig -Name $IPConfigName -SubnetId $armVN.Subnets[0].Id
    PS C:\WINDOWS\system32> $nic = New-AzureRmNetworkInterface -Name $NicName -ResourceGroupName $NicRG -Location DBELocal -IpConfiguration $ipConfig
    WARNING: The output object type of this cmdlet will be modified in a future release.
    PS C:\WINDOWS\system32>    
    ```
1. Hozzon létre egy új virtuálisgép-konfigurációs objektumot.

    ```powershell
    $vmConfig = New-AzureRmVMConfig -VMName $VMName -VMSize $VMSize
    ```

    
1. Adja hozzá a hálózati adaptert a virtuális géphez.

    ```powershell
    $vm = Add-AzureRmVMNetworkInterface -VM $vmConfig -Id $nic.Id
    ```

1. Állítsa be az operációsrendszer-lemez tulajdonságait a virtuális gépen.

    ```powershell
    $vm = Set-AzureRmVMOSDisk -VM $vm -ManagedDiskId $disk.Id -StorageAccountType StandardLRS -CreateOption Attach –[Windows/Linux]
    ```
    A parancs utolsó jelzője `-Windows` vagy attól függően változik, hogy `-Linux` melyik operációs rendszert használja a virtuális géphez.

1. Hozza létre a virtuális gépet.

    ```powershell
    New-AzureRmVM -ResourceGroupName $VMRG -Location DBELocal -VM $vm 
    ```

    Íme egy példa a kimenetre. 

    ```powershell
    PS C:\WINDOWS\system32> $vmConfig = New-AzureRmVMConfig -VMName $VMName -VMSize $VMSize
    PS C:\WINDOWS\system32> $vm = Add-AzureRmVMNetworkInterface -VM $vmConfig -Id $nic.Id
    PS C:\WINDOWS\system32> $vm = Set-AzureRmVMOSDisk -VM $vm -ManagedDiskId $disk.Id -StorageAccountType StandardLRS -CreateOption Attach -Windows
    PS C:\WINDOWS\system32> New-AzureRmVM -ResourceGroupName $VMRG -Location DBELocal -VM $vm
    WARNING: Since the VM is created using premium storage or managed disk, existing standard storage account, myasevmsa, is used for
    boot diagnostics.    
    RequestId IsSuccessStatusCode StatusCode ReasonPhrase
    --------- ------------------- ---------- ------------
                             True         OK OK        
    PS C:\WINDOWS\system32>
    ```

## <a name="delete-vm-and-resources"></a>Virtuális gép és erőforrások törlése

Ez a cikk csak egy erőforráscsoportot használt az összes virtuálisgép-erőforrás létrehozásához. Az erőforráscsoport törlésével törlődik a virtuális gép és az összes kapcsolódó erőforrás. 

1. Először tekintse meg az erőforráscsoport alatt létrehozott összes erőforrást.

    ```powershell
    Get-AzureRmResource -ResourceGroupName <Resource group name>
    ```
    Íme egy példa a kimenetre.
    
    ```powershell
    PS C:\WINDOWS\system32> Get-AzureRmResource -ResourceGroupName myasevm1rg
    
    
    Name              : myasemd1
    ResourceGroupName : myasevm1rg
    ResourceType      : Microsoft.Compute/disks
    Location          : dbelocal
    ResourceId        : /subscriptions/992601bc-b03d-4d72-598e-d24eac232122/resourceGroups/myasevm1rg/providers/Microsoft.Compute/disk
                        s/myasemd1
    
    Name              : myasetestvm1
    ResourceGroupName : myasevm1rg
    ResourceType      : Microsoft.Compute/virtualMachines
    Location          : dbelocal
    ResourceId        : /subscriptions/992601bc-b03d-4d72-598e-d24eac232122/resourceGroups/myasevm1rg/providers/Microsoft.Compute/virt
                        ualMachines/myasetestvm1
    
    Name              : myasevmnic1
    ResourceGroupName : myasevm1rg
    ResourceType      : Microsoft.Network/networkInterfaces
    Location          : dbelocal
    ResourceId        : /subscriptions/992601bc-b03d-4d72-598e-d24eac232122/resourceGroups/myasevm1rg/providers/Microsoft.Network/netw
                        orkInterfaces/myasevmnic1
    
    Name              : myasevmsa
    ResourceGroupName : myasevm1rg
    ResourceType      : Microsoft.Storage/storageaccounts
    Location          : dbelocal
    ResourceId        : /subscriptions/992601bc-b03d-4d72-598e-d24eac232122/resourceGroups/myasevm1rg/providers/Microsoft.Storage/stor
                        ageaccounts/myasevmsa
    
    PS C:\WINDOWS\system32>
    ```

1. Törölje az erőforráscsoportot és az összes kapcsolódó erőforrást.

    ```powershell
    Remove-AzureRmResourceGroup -ResourceGroupName <Resource group name>
    ```
    Íme egy példa a kimenetre.
    
    ```powershell
    PS C:\WINDOWS\system32> Remove-AzureRmResourceGroup -ResourceGroupName myasevm1rg
    
    Confirm
    Are you sure you want to remove resource group 'myasevm1rg'
    [Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): Y
    True
    PS C:\WINDOWS\system32>
    ```

1. Ellenőrizze, hogy az erőforráscsoport törölve lett-e. Az eszközön található összes erőforráscsoport beolvasása. 

    ```powershell
    Get-AzureRmResourceGroup
    ```
    Íme egy példa a kimenetre.

    ```powershell
    PS C:\WINDOWS\system32> Get-AzureRmResourceGroup

    ResourceGroupName : ase-image-resourcegroup
    Location          : dbelocal
    ProvisioningState : Succeeded
    Tags              :
    ResourceId        : /subscriptions/992601bc-b03d-4d72-598e-d24eac232122/resourceGroups/ase-image-resourcegroup
    
    ResourceGroupName : ASERG
    Location          : dbelocal
    ProvisioningState : Succeeded
    Tags              :
    ResourceId        : /subscriptions/992601bc-b03d-4d72-598e-d24eac232122/resourceGroups/ASERG
    
    ResourceGroupName : myaserg
    Location          : dbelocal
    ProvisioningState : Succeeded
    Tags              :
    ResourceId        : /subscriptions/992601bc-b03d-4d72-598e-d24eac232122/resourceGroups/myaserg
        
    PS C:\WINDOWS\system32>
    ```

## <a name="next-steps"></a>Következő lépések

Az üzembe helyezés jellegétől függően az alábbi eljárások közül választhat.

- [Virtuális gép üzembe helyezése általánosított lemezképből Azure PowerShell használatával](azure-stack-edge-gpu-deploy-virtual-machine-powershell.md)  
- [Virtuális gép üzembe helyezése Azure Portal használatával](azure-stack-edge-gpu-deploy-virtual-machine-portal.md)
