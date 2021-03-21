---
title: Windows rendszerű virtuális gép létrehozása speciális virtuális merevlemezről az Azure-ban
description: Hozzon létre egy új Windowsos virtuális gépet úgy, hogy a Resource Manager-alapú üzemi modell használatával egy speciális felügyelt lemezt csatlakoztat az operációsrendszer-lemezként.
author: cynthn
ms.service: virtual-machines
ms.subservice: imaging
ms.collection: windows
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 10/10/2019
ms.author: cynthn
ms.openlocfilehash: 642a28d5eea6a89de31247ed715c0b352b9ff7d1
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102552218"
---
# <a name="create-a-windows-vm-from-a-specialized-disk-by-using-powershell"></a>Windows rendszerű virtuális gép létrehozása specializált lemezből a PowerShell-lel

Hozzon létre egy új virtuális gépet egy speciális felügyelt lemez csatlakoztatásával operációsrendszer-lemezként. A speciális lemezek a virtuális merevlemezek (VHD-k) másolata egy meglévő virtuális gépről, amely tartalmazza az eredeti virtuális gépről származó felhasználói fiókokat, alkalmazásokat és egyéb állapotinformációkat. 

Erre számos lehetősége van:
* [Meglévő felügyelt lemez használata](#option-1-use-an-existing-disk). Ez a beállítás akkor hasznos, ha olyan virtuális géppel rendelkezik, amely nem megfelelően működik. Törölheti a virtuális gépet, majd újra felhasználhatja a felügyelt lemezt egy új virtuális gép létrehozásához. 
* [VHD feltöltése](#option-2-upload-a-specialized-vhd) 
* [Meglévő Azure-beli virtuális gép másolása Pillanatképek használatával](#option-3-copy-an-existing-azure-vm)

A Azure Portal használatával [egy új virtuális gépet is létrehozhat egy speciális VHD-ből](create-vm-specialized-portal.md).

Ez a cikk bemutatja, hogyan használhatja a felügyelt lemezeket. Ha olyan örökölt központi telepítéssel rendelkezik, amely Storage-fiók használatát igényli, tekintse meg a [virtuális gép létrehozása egy speciális VHD-ből a Storage-fiókban](/previous-versions/azure/virtual-machines/windows/sa-create-vm-specialized)című témakört.

> [!IMPORTANT]
> 
> Ha speciális lemezt használ egy új virtuális gép létrehozásához, az új virtuális gép megőrzi az eredeti virtuális gép számítógépnevét. A számítógépekre vonatkozó egyéb információk (például a CMID) is megmaradnak, és bizonyos esetekben az ismétlődő információk problémákat okozhatnak. A virtuális gépek másolásakor vegye figyelembe, hogy az alkalmazásai milyen típusú számítógép-specifikus információkat használnak.  
> Ezért ne használjon speciális lemezt, ha több virtuális gépet szeretne létrehozni. Ehelyett nagyobb telepítések esetén [hozzon létre egy rendszerképet](capture-image-resource.md) , majd [ezt a rendszerképet használva hozzon létre több virtuális](create-vm-generalized-managed.md)gépet.

Azt javasoljuk, hogy az egyidejű központi telepítések számát legfeljebb 20 virtuális gépre korlátozza egyetlen VHD-fájlból vagy pillanatképből. 

## <a name="option-1-use-an-existing-disk"></a>1. lehetőség: meglévő lemez használata

Ha már törölt egy virtuális gépet, és újra szeretné használni az operációsrendszer-lemezt egy új virtuális gép létrehozásához, használja a [Get-AzDisk](/powershell/module/az.compute/get-azdisk).

```powershell
$resourceGroupName = 'myResourceGroup'
$osDiskName = 'myOsDisk'
$osDisk = Get-AzDisk `
-ResourceGroupName $resourceGroupName `
-DiskName $osDiskName
```
Most már csatlakoztathatja ezt a lemezt az operációsrendszer-lemezként egy [új virtuális géphez](#create-the-new-vm).

## <a name="option-2-upload-a-specialized-vhd"></a>2. lehetőség: speciális VHD feltöltése

Feltöltheti a virtuális merevlemezt egy helyszíni virtualizációs eszközzel, például a Hyper-V-vel létrehozott speciális virtuális gépről, vagy egy másik felhőből exportált virtuális gépre.

### <a name="prepare-the-vm"></a>A virtuális gép előkészítése
Egy új virtuális gép létrehozásához használja a virtuális merevlemezt. 
  
  * [Készítse elő a Windows VHD-t az Azure-ba való feltöltéshez](prepare-for-upload-vhd-image.md). **Ne általánosítsa** a virtuális gépet a Sysprep használatával.
  * Távolítsa el a virtuális gépre telepített összes vendég virtualizációs eszközt és ügynököt (például a VMware-eszközökre).
  * Győződjön meg arról, hogy a virtuális gép konfigurálva van a DHCP IP-címének és DNS-beállításainak lekérésére. Ez biztosítja, hogy a kiszolgáló az indításkor megszerezze a virtuális hálózaton belüli IP-címet. 


### <a name="upload-the-vhd"></a>A VHD feltöltése

Most már közvetlenül is feltölthet egy virtuális merevlemezt egy felügyelt lemezre. Útmutatásért lásd: [virtuális merevlemez feltöltése az Azure-ba Azure PowerShell használatával](disks-upload-vhd-to-managed-disk-powershell.md).

## <a name="option-3-copy-an-existing-azure-vm"></a>3. lehetőség: meglévő Azure-beli virtuális gép másolása

Létrehozhat egy, a felügyelt lemezeket használó virtuális gép másolatát a virtuális gép pillanatképének elkészítésével, majd a pillanatkép használatával új felügyelt lemez és új virtuális gép létrehozásához.

Ha egy meglévő virtuális gépet egy másik régióba szeretne másolni, érdemes lehet a azcopy használatával [másolatot készíteni egy lemezről egy másik régióban](disks-upload-vhd-to-managed-disk-powershell.md#copy-a-managed-disk). 

### <a name="take-a-snapshot-of-the-os-disk"></a>Pillanatkép készítése az operációsrendszer-lemezről

Pillanatképet készíthet egy teljes virtuális gépről (beleértve az összes lemezt is), vagy csak egyetlen lemezről. A következő lépések bemutatják, hogyan készíthet pillanatképet a virtuális gép operációsrendszer-lemezéről a [New-AzSnapshot](/powershell/module/az.compute/new-azsnapshot) parancsmaggal. 

Először állítson be néhány paramétert. 

 ```powershell
$resourceGroupName = 'myResourceGroup' 
$vmName = 'myVM'
$location = 'westus' 
$snapshotName = 'mySnapshot'  
```

Szerezze be a virtuálisgép-objektumot.

```powershell
$vm = Get-AzVM -Name $vmName `
   -ResourceGroupName $resourceGroupName
```
Szerezze be az operációsrendszer-lemez nevét.

 ```powershell
$disk = Get-AzDisk -ResourceGroupName $resourceGroupName `
   -DiskName $vm.StorageProfile.OsDisk.Name
```

Hozza létre a pillanatkép-konfigurációt. 

 ```powershell
$snapshotConfig =  New-AzSnapshotConfig `
   -SourceUri $disk.Id `
   -OsType Windows `
   -CreateOption Copy `
   -Location $location 
```

Készítsen pillanatképet.

```powershell
$snapShot = New-AzSnapshot `
   -Snapshot $snapshotConfig `
   -SnapshotName $snapshotName `
   -ResourceGroupName $resourceGroupName
```


Ha ezt a pillanatképet szeretné használni a nagy teljesítményt igénylő virtuális gépek létrehozásához, adja hozzá a paramétert `-AccountType Premium_LRS` a New-AzSnapshotConfig parancshoz. Ez a paraméter hozza létre a pillanatképet, hogy az a prémium szintű felügyelt lemezként legyen tárolva. A prémium szintű Managed Disks drágábbak, mint a standard, ezért a paraméter használata előtt győződjön meg róla, hogy prémiumra van szüksége.

### <a name="create-a-new-disk-from-the-snapshot"></a>Új lemez létrehozása a pillanatképből

Hozzon létre egy felügyelt lemezt a pillanatképből a [New-AzDisk](/powershell/module/az.compute/new-azdisk)használatával. Ez a példa a *myOSDisk* használja a lemez neveként.

Hozzon létre egy új erőforráscsoportot az új virtuális géphez.

```powershell
$destinationResourceGroup = 'myDestinationResourceGroup'
New-AzResourceGroup -Location $location `
   -Name $destinationResourceGroup
```

Állítsa be az operációsrendszer-lemez nevét. 

```powershell
$osDiskName = 'myOsDisk'
```

Hozza létre a felügyelt lemezt.

```powershell
$osDisk = New-AzDisk -DiskName $osDiskName -Disk `
    (New-AzDiskConfig  -Location $location -CreateOption Copy `
    -SourceResourceId $snapshot.Id) `
    -ResourceGroupName $destinationResourceGroup
```


## <a name="create-the-new-vm"></a>Az új virtuális gép létrehozása 

Hozzon létre hálózatkezelést és más virtuálisgép-erőforrásokat, amelyeket az új virtuális gép használni fog.

### <a name="create-the-subnet-and-virtual-network"></a>Az alhálózat és a virtuális hálózat létrehozása

Hozza létre a [virtuális hálózatot](../../virtual-network/virtual-networks-overview.md) és az alhálózatot a virtuális géphez.

1. Hozza létre az alhálózatot. Ez a példa létrehoz egy *mySubNet* nevű alhálózatot az erőforráscsoport *myDestinationResourceGroup*, és az alhálózati címnek az *10.0.0.0/24* értékre állítja az előtagot.
   
    ```powershell
    $subnetName = 'mySubNet'
    $singleSubnet = New-AzVirtualNetworkSubnetConfig `
       -Name $subnetName `
       -AddressPrefix 10.0.0.0/24
    ```
    
2. Hozza létre a virtuális hálózatot. Ebben a példában a virtuális hálózat nevét *myVnetName*, az *USA nyugati* régióját és a virtuális hálózat *10.0.0.0/16*-ra való helyét állítja be. 
   
    ```powershell
    $vnetName = "myVnetName"
    $vnet = New-AzVirtualNetwork `
       -Name $vnetName -ResourceGroupName $destinationResourceGroup `
       -Location $location `
       -AddressPrefix 10.0.0.0/16 `
       -Subnet $singleSubnet
    ```    
    

### <a name="create-the-network-security-group-and-an-rdp-rule"></a>A hálózati biztonsági csoport és egy RDP-szabály létrehozása
Ahhoz, hogy be tudja jelentkezni a virtuális gépre a Remote Desktop Protocol (RDP) használatával, rendelkeznie kell egy olyan biztonsági szabállyal, amely engedélyezi az RDP-hozzáférést a 3389-es porton. A példánkban az új virtuális gép virtuális merevlemeze egy meglévő speciális virtuális gépről lett létrehozva, így olyan fiókot használhat, amely a forrás virtuális gépen létezett RDP-hez.

Ez a példa beállítja a hálózati biztonsági csoport (NSG) nevét a *myNsg* és az RDP-szabály nevét a *myRdpRule* értékre.

```powershell
$nsgName = "myNsg"

$rdpRule = New-AzNetworkSecurityRuleConfig -Name myRdpRule -Description "Allow RDP" `
    -Access Allow -Protocol Tcp -Direction Inbound -Priority 110 `
    -SourceAddressPrefix Internet -SourcePortRange * `
    -DestinationAddressPrefix * -DestinationPortRange 3389
$nsg = New-AzNetworkSecurityGroup `
   -ResourceGroupName $destinationResourceGroup `
   -Location $location `
   -Name $nsgName -SecurityRules $rdpRule
    
```

További információ a végpontokról és a NSG-szabályokról: [portok megnyitása az Azure-beli virtuális](nsg-quickstart-powershell.md)gépekhez a PowerShell használatával.

### <a name="create-a-public-ip-address-and-nic"></a>Nyilvános IP-cím és hálózati adapter létrehozása
Ha engedélyezni szeretné a virtuális hálózatban lévő virtuális géppel való kommunikációt, szüksége lesz egy [nyilvános IP-címére](../../virtual-network/public-ip-addresses.md) és egy hálózati adapterre.

1. Hozza létre a nyilvános IP-címet. Ebben a példában a nyilvános IP-cím neve *myIP* értékre van állítva.
   
    ```powershell
    $ipName = "myIP"
    $pip = New-AzPublicIpAddress `
       -Name $ipName -ResourceGroupName $destinationResourceGroup `
       -Location $location `
       -AllocationMethod Dynamic
    ```       
    
2. Hozza létre a hálózati adaptert. Ebben a példában a hálózati adapter neve *myNicName* értékre van állítva.
   
    ```powershell
    $nicName = "myNicName"
    $nic = New-AzNetworkInterface -Name $nicName `
       -ResourceGroupName $destinationResourceGroup `
       -Location $location -SubnetId $vnet.Subnets[0].Id `
       -PublicIpAddressId $pip.Id `
       -NetworkSecurityGroupId $nsg.Id
    ```
    


### <a name="set-the-vm-name-and-size"></a>A virtuális gép nevének és méretének beállítása

Ebben a példában a virtuális gép nevét *myVM* értékre állítja, a virtuális gép mérete pedig *Standard_A2*.

```powershell
$vmName = "myVM"
$vmConfig = New-AzVMConfig -VMName $vmName -VMSize "Standard_A2"
```

### <a name="add-the-nic"></a>A hálózati adapter hozzáadása
    
```powershell
$vm = Add-AzVMNetworkInterface -VM $vmConfig -Id $nic.Id
```
    

### <a name="add-the-os-disk"></a>Az operációsrendszer-lemez hozzáadása 

Adja hozzá az operációsrendszer-lemezt a konfigurációhoz a [set-AzVMOSDisk](/powershell/module/az.compute/set-azvmosdisk)használatával. Ez a példa a lemez méretét *128 GB* -ra állítja, és a felügyelt lemezt *Windows* operációsrendszer-lemezként csatolja.
 
```powershell
$vm = Set-AzVMOSDisk -VM $vm -ManagedDiskId $osDisk.Id -StorageAccountType Standard_LRS `
    -DiskSizeInGB 128 -CreateOption Attach -Windows
```

### <a name="complete-the-vm"></a>A virtuális gép befejezése 

Hozza létre a virtuális gépet a [New-AzVM](/powershell/module/az.compute/new-azvm) használatával az imént létrehozott konfigurációk alapján.

```powershell
New-AzVM -ResourceGroupName $destinationResourceGroup -Location $location -VM $vm
```

Ha a parancs sikeres, a következőhöz hasonló kimenetet fog látni:

```powershell
RequestId IsSuccessStatusCode StatusCode ReasonPhrase
--------- ------------------- ---------- ------------
                         True         OK OK   

```

### <a name="verify-that-the-vm-was-created"></a>Annak ellenőrzése, hogy a virtuális gép létrejött-e
Az újonnan létrehozott virtuális gépet a virtuális gépek **böngészése** vagy a következő PowerShell-parancsok használatával tekintheti meg a [Azure Portal](https://portal.azure.com)  >  .

```powershell
$vmList = Get-AzVM -ResourceGroupName $destinationResourceGroup
$vmList.Name
```

## <a name="next-steps"></a>Következő lépések
Jelentkezzen be az új virtuális gépre. További információ: [Kapcsolódás és bejelentkezés egy Windows rendszerű Azure-beli virtuális gépre](connect-logon.md).