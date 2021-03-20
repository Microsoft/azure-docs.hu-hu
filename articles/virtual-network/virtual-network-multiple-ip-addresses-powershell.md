---
title: Több IP-cím az Azure Virtual Machines szolgáltatáshoz – PowerShell | Microsoft Docs
description: Megtudhatja, hogyan rendelhet hozzá több IP-címet egy virtuális géphez a PowerShell használatával. | Erőforrás-kezelő
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumudD
ms.service: virtual-network
ms.subservice: ip-services
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/24/2017
ms.author: allensu
ms.openlocfilehash: d86d4248b449ad3961a7798fd36a320eb6a74009
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98217073"
---
# <a name="assign-multiple-ip-addresses-to-virtual-machines-using-powershell"></a>Több IP-cím társítása virtuális gépekhez a PowerShell használatával

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [virtual-network-multiple-ip-addresses-intro.md](../../includes/virtual-network-multiple-ip-addresses-intro.md)]

Ez a cikk bemutatja, hogyan hozhat létre virtuális gépet (VM) a Azure Resource Manager üzemi modellel a PowerShell használatával. Több IP-cím nem rendelhető hozzá a klasszikus üzemi modellel létrehozott erőforrásokhoz. Ha többet szeretne megtudni az Azure-beli üzembe helyezési modellekről, olvassa el a [telepítési modellek ismertetése](../azure-resource-manager/management/deployment-models.md) című cikket.

[!INCLUDE [virtual-network-multiple-ip-addresses-scenario.md](../../includes/virtual-network-multiple-ip-addresses-scenario.md)]

## <a name="create-a-vm-with-multiple-ip-addresses"></a><a name = "create"></a>Több IP-címmel rendelkező virtuális gép létrehozása

Az alábbi lépések elmagyarázzák, hogyan hozhat létre egy példaként több IP-címmel rendelkező virtuális gépet a forgatókönyvben leírtak szerint. Módosítsa a változó értékeit a megvalósításhoz szükséges módon.

1. Nyisson meg egy PowerShell-parancssort, és hajtsa végre a szakasz további lépéseit egyetlen PowerShell-munkameneten belül. Ha még nincs telepítve és konfigurálva a PowerShell, hajtsa végre a [Azure PowerShell telepítésének és konfigurálásának](/powershell/azure/) lépései című cikk lépéseit.
2. Jelentkezzen be a fiókjába a `Connect-AzAccount` paranccsal.
3. Cserélje le a *myResourceGroup* és a *westus* nevet a választott névre és helyre. Hozzon létre egy erőforráscsoportot. Az erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat.

   ```powershell
   $RgName   = "MyResourceGroup"
   $Location = "westus"

   New-AzResourceGroup `
   -Name $RgName `
   -Location $Location
   ```

4. Hozzon létre egy virtuális hálózatot (VNet) és alhálózatot ugyanazon a helyen, mint az erőforráscsoportot:

   ```powershell

   # Create a subnet configuration
   $SubnetConfig = New-AzVirtualNetworkSubnetConfig `
   -Name MySubnet `
   -AddressPrefix 10.0.0.0/24

   # Create a virtual network
   $VNet = New-AzVirtualNetwork `
   -ResourceGroupName $RgName `
   -Location $Location `
   -Name MyVNet `
   -AddressPrefix 10.0.0.0/16 `
   -Subnet $subnetConfig

   # Get the subnet object
   $Subnet = Get-AzVirtualNetworkSubnetConfig -Name $SubnetConfig.Name -VirtualNetwork $VNet
   ```

5. Hozzon létre egy hálózati biztonsági csoportot (NSG) és egy szabályt. A NSG a bejövő és kimenő szabályok használatával védi a virtuális gépet. Ebben az esetben létrejön egy bejövő szabály a 3389-es porthoz, amely lehetővé teszi a bejövő távoli asztali kapcsolatokat.

    ```powershell
    
    # Create an inbound network security group rule for port 3389

    $NSGRule = New-AzNetworkSecurityRuleConfig `
    -Name MyNsgRuleRDP `
    -Protocol Tcp `
    -Direction Inbound `
    -Priority 1000 `
    -SourceAddressPrefix * `
    -SourcePortRange * `
    -DestinationAddressPrefix * `
    -DestinationPortRange 3389 -Access Allow
    
    # Create a network security group
    $NSG = New-AzNetworkSecurityGroup `
    -ResourceGroupName $RgName `
    -Location $Location `
    -Name MyNetworkSecurityGroup `
    -SecurityRules $NSGRule
    ```

6. Adja meg a hálózati adapter elsődleges IP-konfigurációját. Módosítsa a 10.0.0.4 a létrehozott alhálózat érvényes címére, ha nem a korábban definiált értéket használja. Statikus IP-cím hozzárendelésének megkezdése előtt ajánlott először megerősíteni, hogy még nincs használatban. Adja meg a parancsot `Test-AzPrivateIPAddressAvailability -IPAddress 10.0.0.4 -VirtualNetwork $VNet` . Ha a címe elérhető, a kimenet *igaz* értéket ad vissza. Ha nem érhető el, a kimenet *hamis* értéket ad vissza, és felsorolja a rendelkezésre álló címeket. 

    A következő parancsokban **cserélje le a \<replace-with-your-unique-name> nevet a használni kívánt egyedi DNS-névre.** A névnek egyedinek kell lennie az Azure-régión belüli összes nyilvános IP-cím között. Ez egy opcionális paraméter. Akkor távolítható el, ha csak a nyilvános IP-cím használatával szeretne csatlakozni a virtuális géphez.

    ```powershell
    
    # Create a public IP address
    $PublicIP1 = New-AzPublicIpAddress `
    -Name "MyPublicIP1" `
    -ResourceGroupName $RgName `
    -Location $Location `
    -DomainNameLabel <replace-with-your-unique-name> `
    -AllocationMethod Static
        
    #Create an IP configuration with a static private IP address and assign the public IP address to it
    $IpConfigName1 = "IPConfig-1"
    $IpConfig1     = New-AzNetworkInterfaceIpConfig `
    -Name $IpConfigName1 `
    -Subnet $Subnet `
    -PrivateIpAddress 10.0.0.4 `
    -PublicIpAddress $PublicIP1 `
    -Primary
    ```

    Ha több IP-konfigurációt rendel egy hálózati adapterhez, az egyik konfigurációt *elsődlegesként* kell hozzárendelni.

    > [!NOTE]
    > A nyilvános IP-címekhez névleges díj vonatkozik. Ha többet szeretne megtudni az IP-címek díjszabásáról, olvassa el az [IP-címek díjszabását](https://azure.microsoft.com/pricing/details/ip-addresses) ismertető oldalt. Az előfizetésben használható nyilvános IP-címek száma korlátozva van. A korlátozásokkal kapcsolatos további információkért olvassa el az [Azure korlátairól](../azure-resource-manager/management/azure-subscription-service-limits.md#networking-limits) szóló cikket.

7. Adja meg a hálózati adapter másodlagos IP-konfigurációit. Szükség szerint hozzáadhat vagy eltávolíthat konfigurációkat. Minden IP-konfigurációhoz hozzá kell rendelni egy magánhálózati IP-címet. Minden konfigurációhoz szükség lehet egy nyilvános IP-címhez.

    ```powershell
    
    # Create a public IP address
    $PublicIP2 = New-AzPublicIpAddress `
    -Name "MyPublicIP2" `
    -ResourceGroupName $RgName `
    -Location $Location `
    -AllocationMethod Static
        
    #Create an IP configuration with a static private IP address and assign the public IP address to it
    $IpConfigName2 = "IPConfig-2"
    $IpConfig2     = New-AzNetworkInterfaceIpConfig `
    -Name $IpConfigName2 `
    -Subnet $Subnet `
    -PrivateIpAddress 10.0.0.5 `
    -PublicIpAddress $PublicIP2
        
    $IpConfigName3 = "IpConfig-3"
    $IpConfig3 = New-AzNetworkInterfaceIpConfig `
    -Name $IPConfigName3 `
    -Subnet $Subnet `
    -PrivateIpAddress 10.0.0.6
    ```

8. Hozza létre a hálózati adaptert, és rendelje hozzá a három IP-konfigurációt:

   ```powershell
   $NIC = New-AzNetworkInterface `
   -Name MyNIC `
   -ResourceGroupName $RgName `
   -Location $Location `
   -NetworkSecurityGroupId $NSG.Id `
   -IpConfiguration $IpConfig1,$IpConfig2,$IpConfig3
   ```

   >[!NOTE]
   >Bár ebben a cikkben az összes konfiguráció hozzá van rendelve egy hálózati adapterhez, több IP-konfigurációt is hozzárendelhet a virtuális géphez csatlakoztatott összes hálózati adapterhez. Ha többet szeretne megtudni arról, hogyan hozhat létre több hálózati adapterrel rendelkező virtuális gépet, olvassa el a [virtuális gép létrehozása több hálózati adapterrel](../virtual-machines/windows/multiple-nics.md) című cikket.

9. Hozza létre a virtuális gépet a következő parancsok beírásával:

    ```powershell
    
    # Define a credential object. When you run these commands, you're prompted to enter a username and password for the VM you're creating.
    $cred = Get-Credential
    
    # Create a virtual machine configuration
    $VmConfig = New-AzVMConfig `
    -VMName MyVM `
    -VMSize Standard_DS1_v2 | `
    Set-AzVMOperatingSystem -Windows `
    -ComputerName MyVM `
    -Credential $cred | `
    Set-AzVMSourceImage `
    -PublisherName MicrosoftWindowsServer `
    -Offer WindowsServer `
    -Skus 2016-Datacenter `
    -Version latest | `
    Add-AzVMNetworkInterface `
    -Id $NIC.Id
    
    # Create the VM
    New-AzVM `
    -ResourceGroupName $RgName `
    -Location $Location `
    -VM $VmConfig
    ```

10. Adja hozzá a magánhálózati IP-címeket a virtuális gép operációs rendszeréhez úgy, hogy végrehajtja a jelen cikk [IP-címek hozzáadása a virtuális gép operációs rendszeréhez](#os-config) című szakaszának lépéseit. Ne adja hozzá a nyilvános IP-címeket az operációs rendszerhez.

## <a name="add-ip-addresses-to-a-vm"></a><a name="add"></a>IP-címek hozzáadása egy virtuális géphez

Az alábbi lépések végrehajtásával adhat hozzá privát és nyilvános IP-címeket az Azure hálózati adapterhez. Az alábbi szakaszokban szereplő példák azt feltételezik, hogy már rendelkezik egy virtuális géppel, amely a jelen cikkben szereplő [forgatókönyvben](#scenario) ismertetett három IP-konfigurációval rendelkezik, de erre nincs szükség.

1. Nyisson meg egy PowerShell-parancssort, és hajtsa végre a szakasz további lépéseit egyetlen PowerShell-munkameneten belül. Ha még nincs telepítve és konfigurálva a PowerShell, hajtsa végre a [Azure PowerShell telepítésének és konfigurálásának](/powershell/azure/) lépései című cikk lépéseit.
2. Módosítsa a következő $Variables értékeit annak a hálózati adapternek a nevére, amelyhez IP-címet kíván adni, valamint a hálózati adapterhez tartozó erőforráscsoportot és helyet:

   ```powershell
   $NicName  = "MyNIC"
   $RgName   = "MyResourceGroup"
   $Location = "westus"
   ```

   Ha nem ismeri a módosítani kívánt hálózati adapter nevét, írja be a következő parancsokat, majd módosítsa a korábbi változók értékeit:

   ```powershell
   Get-AzNetworkInterface | Format-Table Name, ResourceGroupName, Location
   ```

3. Hozzon létre egy változót, és állítsa be a meglévő hálózati adapterre a következő parancs beírásával:

   ```powershell
   $MyNIC = Get-AzNetworkInterface -Name $NicName -ResourceGroupName $RgName
   ```

4. A következő parancsokban módosítsa a *MyVNet* és a *MySubnet* nevet annak a VNet és alhálózatnak a nevére, amelyhez a hálózati adapter csatlakozik. Adja meg a VNet és az alhálózati objektumok lekéréséhez szükséges parancsokat, amelyhez a hálózati adapter csatlakozik:

   ```powershell
   $MyVNet = Get-AzVirtualnetwork -Name MyVNet -ResourceGroupName $RgName
   $Subnet = $MyVnet.Subnets | Where-Object { $_.Name -eq "MySubnet" }
   ```

   Ha nem ismeri a hálózati adapterhez csatlakoztatott VNet vagy alhálózat nevét, írja be a következő parancsot:

   ```powershell
   $MyNIC.IpConfigurations
   ```

   A kimenetben keresse meg az alábbi példához hasonló szöveget:

   ```
   "Id": "/subscriptions/[Id]/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/MyVNet/subnets/MySubnet"
   ```

    Ebben a kimenetben a *MyVnet* a VNet, a *MySubnet* pedig az a hálózat, amelyhez a hálózati adapter csatlakozik.

5. A követelmények alapján végezze el a következő részekben ismertetett lépéseket:

   **Magánhálózati IP-cím hozzáadása**

   Magánhálózati IP-cím hálózati adapterhez való hozzáadásához létre kell hoznia egy IP-konfigurációt. A következő parancs egy, a 10.0.0.7 statikus IP-címmel rendelkező konfigurációt hoz létre. Statikus IP-cím megadásakor az alhálózathoz nem használt címnek kell lennie. Javasoljuk, hogy először tesztelje a címeket, hogy az elérhető legyen a parancs beírásával `Test-AzPrivateIPAddressAvailability -IPAddress 10.0.0.7 -VirtualNetwork $myVnet` . Ha az IP-cím elérhető, a kimenet *igaz* értéket ad vissza. Ha nem érhető el, a kimenet *hamis* értéket ad vissza, és felsorolja a rendelkezésre álló címeket.

   ```powershell
   Add-AzNetworkInterfaceIpConfig -Name IPConfig-4 -NetworkInterface `
   $MyNIC -Subnet $Subnet -PrivateIpAddress 10.0.0.7
   ```

   Tetszőleges számú konfigurációt hozhat létre, egyedi konfigurációs neveket és magánhálózati IP-címeket (statikus IP-címekkel rendelkező konfigurációk esetén).

   Adja hozzá a magánhálózati IP-címet a virtuális gép operációs rendszeréhez úgy, hogy végrehajtja a jelen cikk [IP-címek hozzáadása a virtuális gép operációs rendszeréhez](#os-config) című szakaszának lépéseit.

   **Nyilvános IP-cím hozzáadása**

   A nyilvános IP-címet egy nyilvános IP-cím típusú erőforrás társításával vagy egy új IP-konfigurációhoz, vagy egy meglévő IP-konfigurációhoz társítja. Hajtsa végre az alábbi szakasz lépéseit, ahogy szükséges.

   > [!NOTE]
   > A nyilvános IP-címekhez névleges díj vonatkozik. Ha többet szeretne megtudni az IP-címek díjszabásáról, olvassa el az [IP-címek díjszabását](https://azure.microsoft.com/pricing/details/ip-addresses) ismertető oldalt. Az előfizetésben használható nyilvános IP-címek száma korlátozva van. A korlátozásokkal kapcsolatos további információkért olvassa el az [Azure korlátairól](../azure-resource-manager/management/azure-subscription-service-limits.md#networking-limits) szóló cikket.
   >

   **A nyilvános IP-cím erőforrásának hozzárendelése új IP-konfigurációhoz**

   Amikor nyilvános IP-címet ad hozzá egy új IP-konfigurációhoz, hozzá kell adnia egy magánhálózati IP-címet is, mivel minden IP-konfigurációnak magánhálózati IP-címmel kell rendelkeznie. Hozzáadhat egy meglévő nyilvános IP-cím-erőforrást, vagy létrehozhat egy újat is. Új létrehozásához írja be a következő parancsot:

   ```powershell
   $myPublicIp3 = New-AzPublicIpAddress `
   -Name "myPublicIp3" `
   -ResourceGroupName $RgName `
   -Location $Location `
   -AllocationMethod Static
   ```

   Ha új IP-konfigurációt szeretne létrehozni statikus magánhálózati IP-címmel és a társított *myPublicIp3* nyilvános IP-cím erőforrással, írja be a következő parancsot:

   ```powershell
   Add-AzNetworkInterfaceIpConfig `
   -Name IPConfig-4 `
   -NetworkInterface $myNIC `
   -Subnet $Subnet `
   -PrivateIpAddress 10.0.0.7 `
   -PublicIpAddress $myPublicIp3
   ```

   **A nyilvános IP-cím erőforrásának hozzárendelése meglévő IP-konfigurációhoz**

   Egy nyilvános IP-cím erőforrás csak olyan IP-konfigurációhoz társítható, amelyhez még nincs társítva. A következő parancs megadásával határozhatja meg, hogy egy IP-konfigurációhoz tartozik-e társított nyilvános IP-cím:

   ```powershell
   $MyNIC.IpConfigurations | Format-Table Name, PrivateIPAddress, PublicIPAddress, Primary
   ```

   Az alábbihoz hasonló kimenetet fog látni:

   ```
   Name       PrivateIpAddress PublicIpAddress                                           Primary

   IPConfig-1 10.0.0.4         Microsoft.Azure.Commands.Network.Models.PSPublicIpAddress    True
   IPConfig-2 10.0.0.5         Microsoft.Azure.Commands.Network.Models.PSPublicIpAddress   False
   IpConfig-3 10.0.0.6                                                                     False
   ```

   Mivel az *IpConfig-3* **PublicIpAddress** oszlopa üres, nincs társítva nyilvános IP-cím erőforrás. Hozzáadhat egy meglévő nyilvános IP-cím-erőforrást az IpConfig-3 számára, vagy megadhatja a következő parancsot egy létrehozásához:

   ```powershell
   $MyPublicIp3 = New-AzPublicIpAddress `
   -Name "MyPublicIp3" `
   -ResourceGroupName $RgName `
   -Location $Location -AllocationMethod Static
   ```

   A következő parancs megadásával rendelje hozzá a nyilvános IP-cím erőforrást az *IpConfig-3* nevű meglévő IP-konfigurációhoz:

   ```powershell
   Set-AzNetworkInterfaceIpConfig `
   -Name IpConfig-3 `
   -NetworkInterface $mynic `
   -Subnet $Subnet `
   -PublicIpAddress $myPublicIp3
   ```

6. Állítsa be a hálózati adaptert az új IP-konfigurációval a következő parancs beírásával:

   ```powershell
   Set-AzNetworkInterface -NetworkInterface $MyNIC
   ```

7. Tekintse meg a hálózati adapterhez rendelt magánhálózati IP-címeket és nyilvános IP-címek erőforrásait a következő parancs beírásával:

   ```powershell
   $MyNIC.IpConfigurations | Format-Table Name, PrivateIPAddress, PublicIPAddress, Primary
   ```

8. Adja hozzá a magánhálózati IP-címet a virtuális gép operációs rendszeréhez úgy, hogy végrehajtja a jelen cikk [IP-címek hozzáadása a virtuális gép operációs rendszeréhez](#os-config) című szakaszának lépéseit. Ne adja hozzá a nyilvános IP-címet az operációs rendszerhez.

[!INCLUDE [virtual-network-multiple-ip-addresses-os-config.md](../../includes/virtual-network-multiple-ip-addresses-os-config.md)]