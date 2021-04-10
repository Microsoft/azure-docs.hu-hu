---
title: 'Oktatóanyag: NAT-átjáró létrehozása – PowerShell'
titlesuffix: Azure Virtual Network NAT
description: 'Első lépések: NAT-átjáró létrehozása Azure PowerShell használatával.'
author: asudbring
ms.author: allensu
ms.service: virtual-network
ms.subservice: nat
ms.topic: tutorial
ms.date: 03/09/2021
ms.custom: template-tutorial
ms.openlocfilehash: 884697cee84c05916fe19fb8f9435de86bda291e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "102620230"
---
# <a name="tutorial-create-a-nat-gateway-using-azure-powershell"></a>Oktatóanyag: NAT-átjáró létrehozása Azure PowerShell használatával

Ez az oktatóanyag bemutatja, hogyan használhatja az Azure Virtual Network NAT szolgáltatást. Létre kell hoznia egy NAT-átjárót, amely kimenő kapcsolatot biztosít az Azure-beli virtuális gépek számára. 

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Hozzon létre egy virtuális hálózatot.
> * Virtuális gépet hoz létre.
> * Hozzon létre egy NAT-átjárót, és társítsa a virtuális hálózathoz.
> * Kapcsolódjon a virtuális géphez, és ellenőrizze a NAT IP-címét.

## <a name="prerequisites"></a>Előfeltételek

- Aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egy fiókot ingyenesen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Helyileg telepített Azure PowerShell vagy Azure Cloud Shell

Ha a PowerShell helyi telepítése és használata mellett dönt, ehhez a cikkhez az Azure PowerShell-modul 5.4.1-es vagy újabb verziójára lesz szükség. A telepített verzió azonosításához futtassa a következőt: `Get-Module -ListAvailable Az`. Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-Az-ps) ismertető cikket. Ha helyileg futtatja a PowerShellt, akkor azt is futtatnia kell, `Connect-AzAccount` hogy létrehozza az Azure-hoz való kapcsolódást.

## <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

Hozzon létre egy erőforráscsoportot a [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). Az Azure-erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat.

A következő példában létrehozunk egy **myResourceGroupNAT** nevű erőforráscsoportot a **eastus2** helyen:

```azurepowershell-interactive
$rsg = @{
    Name = 'myResourceGroupNAT'
    Location = 'eastus2'
}
New-AzResourceGroup @rsg
```
## <a name="create-the-nat-gateway"></a>NAT-átjáró létrehozása

Ebben a szakaszban a NAT-átjárót és a támogató erőforrásokat fogjuk létrehozni.

* Az Internet eléréséhez szüksége lesz egy vagy több nyilvános IP-címre a NAT-átjáró számára. A [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) használatával hozzon létre egy **MYPUBLICIP** nevű nyilvános IP-cím-erőforrást a **myResourceGroupNAT**-ben. 

* Hozzon létre egy globális Azure NAT-átjárót a [New-AzNatGateway](/powershell/module/az.network/new-aznatgateway). A parancs eredménye egy **myNATgateway** nevű átjáró-erőforrást hoz létre, amely a nyilvános IP- **myPublicIP** használja. Az Üresjárati időkorlát 10 percre van beállítva.  

* Hozzon létre egy **myVnet** nevű virtuális hálózatot egy **mySubnet** nevű alhálózattal a New [-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig) használatával a **myResourceGroup** [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork)használatával. A virtuális hálózat IP-címe **10.1.0.0/16**. A virtuális hálózaton belüli alhálózat **10.1.0.0/24**.

* Hozzon létre egy **myBastionHost** nevű Azure Bastion-gazdagépet a virtuális gép eléréséhez. A megerősített gazdagép létrehozásához használja a [New-AzBastion](/powershell/module/az.network/new-azbastion) . Hozzon létre egy nyilvános IP-címet a megerősített gazdagép számára a [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress).

```azurepowershell-interactive
## Create public IP address for NAT gateway ##
$ip = @{
    Name = 'myPublicIP'
    ResourceGroupName = 'myResourceGroupNAT'
    Location = 'eastus2'
    Sku = 'Standard'
    AllocationMethod = 'Static'
}
$publicIP = New-AzPublicIpAddress @ip

## Create NAT gateway resource ##
$nat = @{
    ResourceGroupName = 'myResourceGroupNAT'
    Name = 'myNATgateway'
    IdleTimeoutInMinutes = '10'
    Sku = 'Standard'
    Location = 'eastus2'
    PublicIpAddress = $publicIP
}
$natGateway = New-AzNatGateway @nat

## Create subnet config and associate NAT gateway to subnet##
$subnet = @{
    Name = 'mySubnet'
    AddressPrefix = '10.1.0.0/24'
    NatGateway = $natGateway
}
$subnetConfig = New-AzVirtualNetworkSubnetConfig @subnet 

## Create Azure Bastion subnet. ##
$bastsubnet = @{
    Name = 'AzureBastionSubnet' 
    AddressPrefix = '10.1.1.0/24'
}
$bastsubnetConfig = New-AzVirtualNetworkSubnetConfig @bastsubnet

## Create the virtual network ##
$net = @{
    Name = 'myVNet'
    ResourceGroupName = 'myResourceGroupNAT'
    Location = 'eastus2'
    AddressPrefix = '10.1.0.0/16'
    Subnet = $subnetConfig,$bastsubnetConfig
}
$vnet = New-AzVirtualNetwork @net

## Create public IP address for bastion host. ##
$ip = @{
    Name = 'myBastionIP'
    ResourceGroupName = 'myResourceGroupNAT'
    Location = 'eastus2'
    Sku = 'Standard'
    AllocationMethod = 'Static'
}
$publicip = New-AzPublicIpAddress @ip

## Create bastion host ##
$bastion = @{
    ResourceGroupName = 'myResourceGroupNAT'
    Name = 'myBastion'
    PublicIpAddress = $publicip
    VirtualNetwork = $vnet
}
New-AzBastion @bastion -AsJob

```

## <a name="virtual-machine"></a>Virtuális gép

Ebben a szakaszban létre fog hozni egy virtuális gépet a NAT-átjáró teszteléséhez és a kimenő kapcsolatok nyilvános IP-címének ellenőrzéséhez.

* Hozzon létre egy hálózati adaptert a [New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface).

* Állítsa be a virtuális gép rendszergazdai felhasználónevét és jelszavát a [Get-hitelesítő adatokkal](/powershell/module/microsoft.powershell.security/get-credential).

* A virtuális gép létrehozása a rel:
    * [New-AzVM](/powershell/module/az.compute/new-azvm)
    * [Új – AzVMConfig](/powershell/module/az.compute/new-azvmconfig)
    * [Set-AzVMOperatingSystem](/powershell/module/az.compute/set-azvmoperatingsystem)
    * [Set-AzVMSourceImage](/powershell/module/az.compute/set-azvmsourceimage)
    * [Add-AzVMNetworkInterface](/powershell/module/az.compute/add-azvmnetworkinterface)
    
```azurepowershell-interactive
# Set the administrator and password for the VMs. ##
$cred = Get-Credential

## Place the virtual network into a variable. ##
$vnet = Get-AzVirtualNetwork -Name 'myVNet' -ResourceGroupName 'myResourceGroupNAT'

## Create network interface for virtual machine. ##
$nic = @{
    Name = "myNicVM"
    ResourceGroupName = 'myResourceGroupNAT'
    Location = 'eastus2'
    Subnet = $vnet.Subnets[0]
}
$nicVM = New-AzNetworkInterface @nic

## Create a virtual machine configuration for VMs ##
$vmsz = @{
    VMName = "myVM"
    VMSize = 'Standard_DS1_v2'  
}
$vmos = @{
    ComputerName = "myVM"
    Credential = $cred
}
$vmimage = @{
    PublisherName = 'MicrosoftWindowsServer'
    Offer = 'WindowsServer'
    Skus = '2019-Datacenter'
    Version = 'latest'    
}
$vmConfig = New-AzVMConfig @vmsz `
    | Set-AzVMOperatingSystem @vmos -Windows `
    | Set-AzVMSourceImage @vmimage `
    | Add-AzVMNetworkInterface -Id $nicVM.Id

## Create the virtual machine for VMs ##
$vm = @{
    ResourceGroupName = 'myResourceGroupNAT'
    Location = 'eastus2'
    VM = $vmConfig
}
New-AzVM @vm

```

Várjon, amíg befejeződik a virtuális gép létrehozása, mielőtt továbblép a következő szakaszra.

## <a name="test-nat-gateway"></a>NAT-átjáró tesztelése

Ebben a szakaszban teszteljük a NAT-átjárót. Először a NAT-átjáró nyilvános IP-címét fogjuk felderíteni. Ezután csatlakozik a teszt virtuális géphez, és ellenőrizze a kimenő kapcsolatot a NAT-átjárón keresztül.
    
1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com)

1. Keresse meg a NAT-átjáró nyilvános IP-címét az **Áttekintés** képernyőn. Válassza a **minden szolgáltatás** lehetőséget a bal oldali menüben, válassza a **minden erőforrás** lehetőséget, majd válassza a **myPublicIP** lehetőséget.

2. Jegyezze fel a nyilvános IP-címet:

    :::image type="content" source="./media/tutorial-create-nat-gateway-portal/find-public-ip.png" alt-text="A NAT-átjáró nyilvános IP-címének felderítése" border="true":::

3. Válassza a **minden szolgáltatás** lehetőséget a bal oldali menüben, válassza a **minden erőforrás** lehetőséget, majd az erőforrások listából válassza ki a **myVM** , amely a **myResourceGroupNAT** erőforráscsoporthoz található.

4. Az **Áttekintés** lapon válassza a **kapcsolat**, majd a **Bastion** lehetőséget.

5. Válassza a kék **használat Bastion** gombot.

6. Adja meg a virtuális gép létrehozásakor megadott felhasználónevet és jelszót.

7. Nyissa meg az **Internet Explorert** a **myTestVM**.

8. Írja be **https://whatsmyip.com** a címsorba.

9. Ellenőrizze, hogy a megjelenő IP-cím megegyezik-e az előző lépésben feljegyzett NAT-átjáró címével:

    :::image type="content" source="./media/tutorial-create-nat-gateway-portal/my-ip.png" alt-text="Külső kimenő IP-címet mutató Internet Explorer" border="true":::

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha nem folytatja az alkalmazás használatát, törölje a virtuális hálózatot, a virtuális gépet és a NAT-átjárót a következő lépésekkel:

```azurepowershell-interactive
Remove-AzResourceGroup -Name 'myResourceGroupNAT' -Force
```

## <a name="next-steps"></a>Következő lépések

Az Azure Virtual Network NAT-ról további információt a következő témakörben talál:
> [!div class="nextstepaction"]
> [Virtual Network NAT áttekintése](nat-overview.md)
