---
title: Windows rendszerű virtuális gépek létrehozása és kezelése több hálózati adaptert használó Azure-ban
description: Megtudhatja, hogyan hozhat létre és kezelhet olyan Windowsos virtuális gépeket, amelyek több hálózati adapterrel rendelkeznek, Azure PowerShell vagy Resource Manager-sablonok használatával.
author: cynthn
ms.service: virtual-machines
ms.collection: windows
ms.topic: how-to
ms.workload: infrastructure
ms.date: 09/26/2017
ms.author: cynthn
ms.openlocfilehash: 2664d175818a6e29dcd3f704c6938987bae050cd
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/10/2021
ms.locfileid: "102555176"
---
# <a name="create-and-manage-a-windows-virtual-machine-that-has-multiple-nics"></a>Több hálózati adapterrel rendelkező Windows rendszerű virtuális gép létrehozása és kezelése
Az Azure-ban a virtuális gépek (VM-EK) több virtuális hálózati adapterrel (NIC) is rendelkezhetnek hozzájuk. Gyakori forgatókönyv, hogy különböző alhálózatokat kell létrehozni az előtér-és háttér-kapcsolathoz. Több hálózati adaptert is hozzárendelhet egy virtuális GÉPHEZ több alhálózathoz, de ezek az alhálózatok mind ugyanabban a virtuális hálózatban (vNet) találhatók. Ez a cikk részletesen ismerteti, hogyan hozható létre több hálózati adapterrel rendelkező virtuális gép. Azt is megtudhatja, hogyan adhat hozzá vagy távolíthat el hálózati adaptereket egy meglévő virtuális gépről. A különböző virtuálisgép- [méretek](../sizes.md) eltérő számú hálózati adaptert támogatnak, ezért a virtuális gépet ennek megfelelően kell méretezni.

## <a name="prerequisites"></a>Előfeltételek

Az alábbi példákban cserélje le a példában szereplő paraméterek nevét a saját értékeire. A paraméterek nevei például a következők: *myResourceGroup*, *myVnet* és *myVM*.

 

## <a name="create-a-vm-with-multiple-nics"></a>Több hálózati adapterrel rendelkező virtuális gép létrehozása
Először hozzon létre egy erőforráscsoportot. A következő példában létrehozunk egy *myResourceGroup* nevű erőforráscsoportot a *EastUs* helyen:

```powershell
New-AzResourceGroup -Name "myResourceGroup" -Location "EastUS"
```

### <a name="create-virtual-network-and-subnets"></a>Virtuális hálózat és alhálózatok létrehozása
Gyakori forgatókönyv, hogy egy virtuális hálózat két vagy több alhálózattal rendelkezik. Egy alhálózat lehet az előtér-forgalomhoz, a másik a háttér-forgalomhoz. Ha mindkét alhálózathoz szeretne csatlakozni, több hálózati adaptert is használhat a virtuális gépen.

1. Hozzon létre két virtuális hálózati alhálózatot a [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig). A következő példa a *mySubnetFrontEnd* és a *mySubnetBackEnd* alhálózatait definiálja:

    ```powershell
    $mySubnetFrontEnd = New-AzVirtualNetworkSubnetConfig -Name "mySubnetFrontEnd" `
        -AddressPrefix "192.168.1.0/24"
    $mySubnetBackEnd = New-AzVirtualNetworkSubnetConfig -Name "mySubnetBackEnd" `
        -AddressPrefix "192.168.2.0/24"
    ```

2. Hozza létre a virtuális hálózatot és az alhálózatokat a [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork). A következő példában létrehozunk egy *myVnet* nevű virtuális hálózatot:

    ```powershell
    $myVnet = New-AzVirtualNetwork -ResourceGroupName "myResourceGroup" `
        -Location "EastUs" `
        -Name "myVnet" `
        -AddressPrefix "192.168.0.0/16" `
        -Subnet $mySubnetFrontEnd,$mySubnetBackEnd
    ```


### <a name="create-multiple-nics"></a>Több hálózati adapter létrehozása
Hozzon létre két hálózati adaptert a [New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface). Csatoljon egy hálózati adaptert az előtér-alhálózathoz és egy hálózati adaptert a háttérbeli alhálózathoz. A következő példa a *myNic1* és a *MyNic2* nevű hálózati adaptereket hozza létre:

```powershell
$frontEnd = $myVnet.Subnets|?{$_.Name -eq 'mySubnetFrontEnd'}
$myNic1 = New-AzNetworkInterface -ResourceGroupName "myResourceGroup" `
    -Name "myNic1" `
    -Location "EastUs" `
    -SubnetId $frontEnd.Id

$backEnd = $myVnet.Subnets|?{$_.Name -eq 'mySubnetBackEnd'}
$myNic2 = New-AzNetworkInterface -ResourceGroupName "myResourceGroup" `
    -Name "myNic2" `
    -Location "EastUs" `
    -SubnetId $backEnd.Id
```

Általában egy [hálózati biztonsági csoportot](../../virtual-network/network-security-groups-overview.md) is létrehoz a virtuális gépre irányuló hálózati forgalom szűrésére, valamint a [terheléselosztást](../../load-balancer/load-balancer-overview.md) a több virtuális gép közötti forgalom elosztására.

### <a name="create-the-virtual-machine"></a>A virtuális gép létrehozása
Most kezdje el létrehozni a virtuális gép konfigurációját. Minden virtuálisgép-mérethez tartozik egy korlát a virtuális géphez adható hálózati adapterek teljes számára. További információ: [Windowsos virtuális gépek méretei](../sizes.md).

1. Állítsa a virtuális gép hitelesítő adatait a `$cred` változóra a következőképpen:

    ```powershell
    $cred = Get-Credential
    ```

2. Adja meg a virtuális gépet a [New-AzVMConfig](/powershell/module/az.compute/new-azvmconfig). Az alábbi példa egy *myVM* nevű virtuális gépet definiál, és olyan virtuálisgép-méretet használ, amely több mint két hálózati adaptert támogat (*Standard_DS3_v2*):

    ```powershell
    $vmConfig = New-AzVMConfig -VMName "myVM" -VMSize "Standard_DS3_v2"
    ```

3. Hozza létre a virtuális gép többi konfigurációját a [set-AzVMOperatingSystem](/powershell/module/az.compute/set-azvmoperatingsystem) és a [set-AzVMSourceImage](/powershell/module/az.compute/set-azvmsourceimage). A következő példa egy Windows Server 2016 rendszerű virtuális gépet hoz létre:

    ```powershell
    $vmConfig = Set-AzVMOperatingSystem -VM $vmConfig `
        -Windows `
        -ComputerName "myVM" `
        -Credential $cred `
        -ProvisionVMAgent `
        -EnableAutoUpdate
    $vmConfig = Set-AzVMSourceImage -VM $vmConfig `
        -PublisherName "MicrosoftWindowsServer" `
        -Offer "WindowsServer" `
        -Skus "2016-Datacenter" `
        -Version "latest"
   ```

4. Csatolja a korábban a [Add-AzVMNetworkInterface](/powershell/module/az.compute/add-azvmnetworkinterface)által létrehozott két hálózati adaptert:

    ```powershell
    $vmConfig = Add-AzVMNetworkInterface -VM $vmConfig -Id $myNic1.Id -Primary
    $vmConfig = Add-AzVMNetworkInterface -VM $vmConfig -Id $myNic2.Id
    ```

5. Hozzon létre egy új virtuális gépet a [New-AzVM](/powershell/module/az.compute/new-azvm):

    ```powershell
    New-AzVM -VM $vmConfig -ResourceGroupName "myResourceGroup" -Location "EastUs"
    ```

6. Adja hozzá az útvonalakat a másodlagos hálózati adapterekhez az operációs [rendszer konfigurálása több hálózati adapterhez](#configure-guest-os-for-multiple-nics)című rész lépéseit követve.

## <a name="add-a-nic-to-an-existing-vm"></a>Hálózati adapter hozzáadása meglévő virtuális géphez
Ha virtuális NIC-t szeretne hozzáadni egy meglévő virtuális GÉPHEZ, szabadítsa fel a virtuális GÉPET, adja hozzá a virtuális hálózati adaptert, majd indítsa el a virtuális GÉPET. A különböző virtuálisgép- [méretek](../sizes.md) eltérő számú hálózati adaptert támogatnak, ezért a virtuális gépet ennek megfelelően kell méretezni. Ha szükséges, [átméretezheti a virtuális gépet](resize-vm.md).

1. Szabadítsa fel a virtuális gépet a [stop-AzVM](/powershell/module/az.compute/stop-azvm). Az alábbi példa felszabadítja a *myVM* nevű virtuális gépet a *myResourceGroup*-ben:

    ```powershell
    Stop-AzVM -Name "myVM" -ResourceGroupName "myResourceGroup"
    ```

2. Szerezze be a virtuális gép meglévő konfigurációját a [Get-AzVm](/powershell/module/az.compute/get-azvm). A következő példa a *myVM* nevű virtuális gép adatait olvassa be a *myResourceGroup*-ben:

    ```powershell
    $vm = Get-AzVm -Name "myVM" -ResourceGroupName "myResourceGroup"
    ```

3. A következő példa egy olyan virtuális hálózati adaptert hoz létre, amely a *mySubnetBackEnd*-hez csatolt, [új AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface) nevű *myNic3* rendelkezik. Ezután csatolja a virtuális hálózati adaptert a *myVM* nevű virtuális géphez a *myResourceGroup* -ben az [Add-AzVMNetworkInterface](/powershell/module/az.compute/add-azvmnetworkinterface)használatával:

    ```powershell
    # Get info for the back end subnet
    $myVnet = Get-AzVirtualNetwork -Name "myVnet" -ResourceGroupName "myResourceGroup"
    $backEnd = $myVnet.Subnets|?{$_.Name -eq 'mySubnetBackEnd'}

    # Create a virtual NIC
    $myNic3 = New-AzNetworkInterface -ResourceGroupName "myResourceGroup" `
        -Name "myNic3" `
        -Location "EastUs" `
        -SubnetId $backEnd.Id

    # Get the ID of the new virtual NIC and add to VM
    $nicId = (Get-AzNetworkInterface -ResourceGroupName "myResourceGroup" -Name "MyNic3").Id
    Add-AzVMNetworkInterface -VM $vm -Id $nicId | Update-AzVm -ResourceGroupName "myResourceGroup"
    ```

    ### <a name="primary-virtual-nics"></a>Elsődleges virtuális hálózati adapterek
    Egy több hálózati adapterrel rendelkező virtuális gép egyik hálózati adapterének elsődlegesnek kell lennie. Ha a virtuális gép egyik meglévő virtuális hálózati adaptere már elsődlegesként van beállítva, akkor kihagyhatja ezt a lépést. Az alábbi példa azt feltételezi, hogy két virtuális hálózati adapter van jelen a virtuális gépen, és az első hálózati adaptert ( `[0]` ) elsődlegesként szeretné hozzáadni:
        
    ```powershell
    # List existing NICs on the VM and find which one is primary
    $vm.NetworkProfile.NetworkInterfaces
    
    # Set NIC 0 to be primary
    $vm.NetworkProfile.NetworkInterfaces[0].Primary = $true
    $vm.NetworkProfile.NetworkInterfaces[1].Primary = $false
    
    # Update the VM state in Azure
    Update-AzVM -VM $vm -ResourceGroupName "myResourceGroup"
    ```

4. Indítsa el a virtuális gépet a [Start-AzVm](/powershell/module/az.compute/start-azvm):

    ```powershell
    Start-AzVM -ResourceGroupName "myResourceGroup" -Name "myVM"
    ```

5. Adja hozzá az útvonalakat a másodlagos hálózati adapterekhez az operációs [rendszer konfigurálása több hálózati adapterhez](#configure-guest-os-for-multiple-nics)című rész lépéseit követve.

## <a name="remove-a-nic-from-an-existing-vm"></a>NIC eltávolítása meglévő virtuális gépről
Ha egy virtuális hálózati adaptert szeretne eltávolítani egy meglévő virtuális gépről, szüntesse meg a virtuális gép felszabadítását, távolítsa el a virtuális hálózati adaptert, majd indítsa el a virtuális GÉPET.

1. Szabadítsa fel a virtuális gépet a [stop-AzVM](/powershell/module/az.compute/stop-azvm). Az alábbi példa felszabadítja a *myVM* nevű virtuális gépet a *myResourceGroup*-ben:

    ```powershell
    Stop-AzVM -Name "myVM" -ResourceGroupName "myResourceGroup"
    ```

2. Szerezze be a virtuális gép meglévő konfigurációját a [Get-AzVm](/powershell/module/az.compute/get-azvm). A következő példa a *myVM* nevű virtuális gép adatait olvassa be a *myResourceGroup*-ben:

    ```powershell
    $vm = Get-AzVm -Name "myVM" -ResourceGroupName "myResourceGroup"
    ```

3. A hálózati adapter eltávolításával kapcsolatos információk lekérése a [Get-AzNetworkInterface](/powershell/module/az.network/get-aznetworkinterface). Az alábbi példa a *myNic3* kapcsolatos információkat ismerteti:

    ```powershell
    # List existing NICs on the VM if you need to determine NIC name
    $vm.NetworkProfile.NetworkInterfaces

    $nicId = (Get-AzNetworkInterface -ResourceGroupName "myResourceGroup" -Name "myNic3").Id   
    ```

4. Távolítsa el a hálózati adaptert a [Remove-AzVMNetworkInterface](/powershell/module/az.compute/remove-azvmnetworkinterface) , majd frissítse a virtuális gépet a [Update-AzVm](/powershell/module/az.compute/update-azvm). Az alábbi példa eltávolítja a *myNic3* `$nicId` az előző lépésben kapott módon:

    ```powershell
    Remove-AzVMNetworkInterface -VM $vm -NetworkInterfaceIDs $nicId | `
        Update-AzVm -ResourceGroupName "myResourceGroup"
    ```   

5. Indítsa el a virtuális gépet a [Start-AzVm](/powershell/module/az.compute/start-azvm):

    ```powershell
    Start-AzVM -Name "myVM" -ResourceGroupName "myResourceGroup"
    ```   

## <a name="create-multiple-nics-with-templates"></a>Több hálózati adapter létrehozása sablonokkal
Azure Resource Manager-sablonok segítségével az erőforrások több példánya is létrehozható az üzembe helyezés során, például több hálózati adapter létrehozása. A Resource Manager-sablonok deklaratív JSON-fájlokat használnak a környezet definiálásához. További információ: [Azure Resource Manager áttekintése](../../azure-resource-manager/management/overview.md). A *Másolás* lehetőséggel megadhatja a létrehozandó példányok számát:

```json
"copy": {
    "name": "multiplenics",
    "count": "[parameters('count')]"
}
```

További információ: [több példány létrehozása a *copy* paranccsal](../../azure-resource-manager/templates/copy-resources.md). 

A `copyIndex()` segítségével egy számot is csatolhat egy erőforrás nevéhez. Ezután létrehozhat *myNic1*, *MyNic2* és így tovább. Az alábbi kód egy példát mutat be az index értékének hozzáfűzésére:

```json
"name": "[concat('myNic', copyIndex())]", 
```

A [Resource Manager-sablonok segítségével több hálózati adapter létrehozásával](../../virtual-network/template-samples.md)kapcsolatos teljes példát is megtekintheti.

Adja hozzá az útvonalakat a másodlagos hálózati adapterekhez az operációs [rendszer konfigurálása több hálózati adapterhez](#configure-guest-os-for-multiple-nics)című rész lépéseit követve.

## <a name="configure-guest-os-for-multiple-nics"></a>A vendég operációs rendszer konfigurálása több hálózati adapterhez

Az Azure egy alapértelmezett átjárót rendel hozzá a virtuális géphez csatolt első (elsődleges) hálózati adapterhez. Az Azure nem rendel hozzá alapértelmezett átjárót a virtuális géphez csatolt további (másodlagos) hálózati adapterekhez. Alapértelmezés szerint ezért nem lehetséges a kommunikáció olyan erőforrásokkal, amelyek a másodlagos hálózati adaptert tartalmazó alhálózaton kívül vannak. A másodlagos hálózati adapterek azonban az alhálózaton kívüli erőforrásokkal is kommunikálhatnak, bár a kommunikáció engedélyezésének lépései eltérőek a különböző operációs rendszereken.

1. Egy Windows-parancssorból futtassa a `route print` parancsot, amely a következő kimenethez hasonló kimenetet ad vissza egy két csatlakoztatott hálózati adapterrel rendelkező virtuális gép esetében:

    ```
    ===========================================================================
    Interface List
    3...00 0d 3a 10 92 ce ......Microsoft Hyper-V Network Adapter #3
    7...00 0d 3a 10 9b 2a ......Microsoft Hyper-V Network Adapter #4
    ===========================================================================
    ```
 
    Ebben a példában a **Microsoft Hyper-V hálózati Adapter #4** (Interface 7) a másodlagos hálózati adapter, amelyhez nincs hozzárendelve alapértelmezett átjáró.

2. A parancssorból futtassa a parancsot, `ipconfig` és ellenőrizze, hogy melyik IP-cím van hozzárendelve a másodlagos hálózati adapterhez. Ebben a példában a 192.168.2.4 a 7. felülethez van rendelve. A másodlagos hálózati adapterhez nem érkezik alapértelmezett átjáró-címet.

3. Ha a másodlagos hálózati adapter alhálózatán kívüli címekre irányuló összes forgalmat át szeretné irányítani az alhálózathoz tartozó átjáróra, futtassa a következő parancsot:

    ```
    route add -p 0.0.0.0 MASK 0.0.0.0 192.168.2.1 METRIC 5015 IF 7
    ```

    Az alhálózat átjárójának címe az első IP-cím (1) az alhálózathoz definiált címtartományból. Ha nem szeretné, hogy az alhálózaton kívüli összes forgalom átirányítható legyen, akkor helyette egyedi útvonalakat adhat hozzá az adott célhelyekhez. Ha például csak a másodlagos hálózati adapterről a 192.168.3.0-hálózatra irányítja át a forgalmat, adja meg a következő parancsot:

      ```
      route add -p 192.168.3.0 MASK 255.255.255.0 192.168.2.1 METRIC 5015 IF 7
      ```
  
4. Ha szeretné megerősíteni a 192.168.3.0-hálózaton lévő erőforrásokkal való sikeres kommunikációt, írja be például a következő parancsot a 192.168.3.4 pingeléséhez a 7. felület (192.168.2.4) használatával:

    ```
    ping 192.168.3.4 -S 192.168.2.4
    ```

    Előfordulhat, hogy meg kell nyitnia az ICMP-t az alábbi paranccsal pingelni kívánt eszköz Windows tűzfalán:
  
      ```
      netsh advfirewall firewall add rule name=Allow-ping protocol=icmpv4 dir=in action=allow
      ```
  
5. Ha ellenőrizni szeretné, hogy a hozzáadott útvonal szerepel-e az útválasztási táblában, írja be a `route print` parancsot, amely az alábbi szöveghez hasonló kimenetet ad vissza:

    ```
    ===========================================================================
    Active Routes:
    Network Destination        Netmask          Gateway       Interface  Metric
              0.0.0.0          0.0.0.0      192.168.1.1      192.168.1.4     15
              0.0.0.0          0.0.0.0      192.168.2.1      192.168.2.4   5015
    ```

    Az **átjáró** alatt a *192.168.1.1* listázott útvonal az elsődleges hálózati adapter alapértelmezett útvonala. Az **átjáró** alatti *192.168.2.1* útvonal a hozzáadott útvonal.

## <a name="next-steps"></a>Következő lépések
Ha több hálózati adapterrel rendelkező virtuális gépet próbál létrehozni, tekintse át a [Windows rendszerű virtuális gépek méretét](../sizes.md) . Ügyeljen arra, hogy az egyes VM-méretek hány hálózati adaptert támogatnak.