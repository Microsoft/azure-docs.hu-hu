---
title: Virtuálisgép-rendszerképek létrehozása Windows VHD specializált rendszerképből az Azure Stack Edge Pro GPU-eszközhöz
description: Leírja, hogyan hozhat létre virtuálisgép-rendszerképeket specializált rendszerképekből Windows VHD-ről vagy VHDX-ről kiindulva. Ezzel a specializált rendszerképpel olyan virtuálisgép-rendszerképeket hozhat létre, amelyek a GPU-eszközön üzembe helyezett Azure Stack Edge Pro használhatók.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 04/15/2021
ms.author: alkohli
ms.openlocfilehash: 6bfa42e99f295b429eba40a27eb59becb8aa80a1
ms.sourcegitcommit: d3bcd46f71f578ca2fd8ed94c3cdabe1c1e0302d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107575946"
---
# <a name="deploy-a-vm-from-a-specialized-image-on-your-azure-stack-edge-pro-gpu-device-via-azure-powershell"></a>Virtuális gép üzembe helyezése specializált rendszerképből az Azure Stack Edge Pro GPU-eszközön a Azure PowerShell 

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Ez a cikk azokat a lépéseket ismerteti, amelyek egy virtuális gép (VM) egy speciális rendszerképből Azure Stack Edge Pro GPU-eszközön való üzembe helyezéséhez szükségesek. 

Ha egy általános rendszerképet kell előkészítenie a virtuális gépek Azure Stack Edge Pro GPU-ban való üzembe helyezéséhez, tekintse meg az Általános rendszerkép előkészítése [Windows VHD-ről](azure-stack-edge-gpu-prepare-windows-vhd-generalized-image.md) vagy Általános rendszerkép előkészítése [ISO-lemezképből fejezetet.](azure-stack-edge-gpu-prepare-windows-generalized-image-iso.md)

## <a name="about-vm-images"></a>A virtuálisgép-rendszerképekről

A Windows VHD vagy VHDX segítségével  specializált rendszerképet vagy általános *rendszerképet hozhat* létre. Az alábbi táblázat összefoglalja  a speciális és az általánosított rendszerképek *közötti fő különbségeket.*

[!INCLUDE [about-vm-images-for-azure-stack-edge](../../includes/azure-stack-edge-about-vm-images.md)]

## <a name="workflow"></a>Munkafolyamat

A virtuális gépek specializált rendszerképből való üzembe helyezésének magas szintű munkafolyamata a következő:

1. Másolja a VHD-t egy helyi tárfiókba a Azure Stack Edge Pro GPU-eszközén.
1. Hozzon létre egy új felügyelt lemezt a virtuális merevlemezből.
1. Hozzon létre egy új virtuális gépet a felügyelt lemezről, és csatolja a felügyelt lemezt.

## <a name="prerequisites"></a>Előfeltételek

Mielőtt üzembe helyez egy virtuális gépet az eszközén a PowerShell használatával, győződjön meg a következő szolgáltatásokról:

- Rendelkezik hozzáféréssel az eszközhöz való csatlakozáshoz használt ügyfélhez.
    - Az ügyfél egy támogatott [operációs rendszert futtat.](azure-stack-edge-gpu-system-requirements.md#supported-os-for-clients-connected-to-device)
    - Az ügyfél úgy van konfigurálva, hogy csatlakozzon az eszköz Azure Resource Manager helyi számítógépéhez a Csatlakozás Azure Resource Manager [eszközhöz útmutatása alapján.](azure-stack-edge-gpu-connect-resource-manager.md)

## <a name="verify-the-local-azure-resource-manager-connection"></a>A helyi hálózati kapcsolat Azure Resource Manager ellenőrzése

Ellenőrizze, hogy az ügyfél tud-e csatlakozni a helyi Azure Resource Manager. 

1. Helyi eszköz API-k meghívása a hitelesítéshez:

    ```powershell
    Login-AzureRMAccount -EnvironmentName <Environment Name>
    ```

2. Adja meg a `EdgeArmUser` felhasználónevet és a jelszót a Azure Resource Manager. Ha nem emlékszik a jelszóra, állítsa [alaphelyzetbe](azure-stack-edge-gpu-set-azure-resource-manager-password.md) a jelszót a Azure Resource Manager, és használja ezt a jelszót a bejelentkezéshez.

## <a name="deploy-vm-from-specialized-image"></a>Virtuális gép üzembe helyezése specializált rendszerképből

A következő szakaszok lépésenként ismertetik a virtuális gépek specializált rendszerképből való üzembe helyezését.

## <a name="copy-vhd-to-local-storage-account-on-device"></a>VHD másolása helyi tárfiókba az eszközön

A VHD helyi tárfiókba való másoláshoz kövesse az alábbi lépéseket:

1. Másolja a forrás VHD-t egy helyi Blob Storage-fiókba a Azure Stack Edge.

1. Jegyezze fel az eredményül kapott URI-t. Ezt az URI-t egy későbbi lépésben fogja használni.

    Helyi tárfiók létrehozásához és eléréséhez tekintse meg [a](azure-stack-edge-gpu-deploy-virtual-machine-powershell.md#create-a-storage-account) virtuális merevlemez feltöltésével történő tárfiók létrehozása szakaszt a következő cikkben: Virtuális gépek üzembe helyezése Azure Stack Edge eszközön a [Azure PowerShell.](azure-stack-edge-gpu-deploy-virtual-machine-powershell.md) [](azure-stack-edge-gpu-deploy-virtual-machine-powershell.md#upload-a-vhd) 

## <a name="create-a-managed-disk-from-vhd"></a>Felügyelt lemez létrehozása VHD-ről

Az alábbi lépésekkel hozhat létre felügyelt lemezt egy vhD-ről, amit korábban töltött fel a tárfiókba:

1. Állítson be néhány paramétert.

    ```powershell
    $VhdURI = <URI of VHD in local storage account>
    $DiskRG = <managed disk resource group>
    $DiskName = <managed disk name>    
    ```
    Példa a kimenetre.

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

    Példa a kimenetre. Az itt található hely a helyi tárfiók helyére van beállítva, és mindig a GPU-eszközön található `DBELocal` összes Azure Stack Edge Pro tárfiókhoz. 

    ```powershell
    PS C:\WINDOWS\system32> $DiskConfig = New-AzureRmDiskConfig -Location DBELocal -CreateOption Import -SourceUri $VHDURI
    PS C:\WINDOWS\system32> $disk = New-AzureRMDisk -ResourceGroupName $DiskRG -DiskName $DiskName -Disk $DiskConfig
    PS C:\WINDOWS\system32>    
    ```
## <a name="create-a-vm-from-managed-disk"></a>Virtuális gép létrehozása felügyelt lemezről

Virtuális gép felügyelt lemezről való létrehozásához kövesse az alábbi lépéseket:

1. Állítson be néhány paramétert.

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
    > A `PrivateIP` paraméter használata nem kötelező. Ezzel a paraméterrel statikus IP-címet rendelhet hozzá, de az alapértelmezett érték a DHCP protokollt használó dinamikus IP-cím.

    Példa a kimenetre. Ebben a példában ugyanazt az erőforráscsoportot adhatja meg az összes virtuálisgép-erőforráshoz, de szükség esetén külön erőforráscsoportokat is létrehozhat és megadhat az erőforrásokhoz.

    ```powershell
    PS C:\WINDOWS\system32> $NicRG = "myasevm1rg"
    PS C:\WINDOWS\system32> $NicName = "myasevmnic1"
    PS C:\WINDOWS\system32> $IPConfigName = "myaseipconfig1" 

    PS C:\WINDOWS\system32> $VMRG = "myasevm1rg"
    PS C:\WINDOWS\system32> $VMName = "myasetestvm1"
    PS C:\WINDOWS\system32> $VMSize = "Standard_D1_v2"   
    ```

1. Szerezze be a virtuális hálózat adatait, és hozzon létre egy új hálózati adaptert.

    Ez a példa azt feltételezi, hogy egyetlen hálózati adaptert hoz létre az alapértelmezett erőforráscsoporthoz társított alapértelmezett `ASEVNET` virtuális `ASERG` hálózaton. Szükség esetén megadhat egy másik virtuális hálózatot, vagy létrehozhat több hálózati adaptert is. További információ: [Hálózati adapter hozzáadása virtuális](azure-stack-edge-gpu-manage-virtual-machine-network-interfaces-portal.md)géphez a Azure Portal.

    ```powershell
    $armVN = Get-AzureRMVirtualNetwork -Name ASEVNET -ResourceGroupName ASERG
    $ipConfig = New-AzureRmNetworkInterfaceIpConfig -Name $IPConfigName -SubnetId $armVN.Subnets[0].Id [-PrivateIpAddress $PrivateIP]
    $nic = New-AzureRmNetworkInterface -Name $NicName -ResourceGroupName $NicRG -Location DBELocal -IpConfiguration $ipConfig
    ```

    Példa a kimenetre.

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
    A parancs utolsó jelzője vagy lehet attól függően, hogy melyik operációs rendszert használja `-Windows` `-Linux` a virtuális géphez.

1. Hozza létre a virtuális gépet.

    ```powershell
    New-AzureRmVM -ResourceGroupName $VMRG -Location DBELocal -VM $vm 
    ```

    Példa a kimenetre. 

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

Ez a cikk csak egy erőforráscsoportot használt az összes virtuálisgép-erőforrás létrehozásához. Az erőforráscsoport törlésével törli a virtuális gépet és az összes társított erőforrást. 

1. Először tekintse meg az erőforráscsoportban létrehozott összes erőforrást.

    ```powershell
    Get-AzureRmResource -ResourceGroupName <Resource group name>
    ```
    Példa a kimenetre.
    
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

1. Törölje az erőforráscsoportot és az összes társított erőforrást.

    ```powershell
    Remove-AzureRmResourceGroup -ResourceGroupName <Resource group name>
    ```
    Példa a kimenetre.
    
    ```powershell
    PS C:\WINDOWS\system32> Remove-AzureRmResourceGroup -ResourceGroupName myasevm1rg
    
    Confirm
    Are you sure you want to remove resource group 'myasevm1rg'
    [Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): Y
    True
    PS C:\WINDOWS\system32>
    ```

1. Ellenőrizze, hogy az erőforráscsoport törölve lett-e. Szerezze be az eszközön meglévő összes erőforráscsoportot. 

    ```powershell
    Get-AzureRmResourceGroup
    ```
    Példa a kimenetre.

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

- [Általános rendszerkép előkészítése Windows vhD-ről virtuális gépek üzembe helyezéséhez Azure Stack Edge Pro GPU-val](azure-stack-edge-gpu-prepare-windows-vhd-generalized-image.md)
- [Általános rendszerkép előkészítése ISO-lemezképből](azure-stack-edge-gpu-prepare-windows-generalized-image-iso.md) a virtuális gépek GPU Azure Stack Edge Pro való üzembe helyezéséhez