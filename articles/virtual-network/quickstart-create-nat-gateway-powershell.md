---
title: NAT-átjáró létrehozása – Azure PowerShell
titlesuffix: Azure Virtual Network NAT
description: Ez a rövid útmutató bemutatja, hogyan hozhat létre NAT-átjárót Azure PowerShell használatával
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumudD
Customer intent: I want to create a NAT gateway for outbound connectivity for my virtual network.
ms.service: virtual-network
ms.subservice: nat
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/18/2020
ms.author: allensu
ms.openlocfilehash: 54ec676da3311473f86a47aae600c2ec29192bb2
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/28/2021
ms.locfileid: "98935133"
---
# <a name="tutorial-create-a-nat-gateway-using-azure-powershell"></a>Oktatóanyag: NAT-átjáró létrehozása Azure PowerShell használatával

Ez az oktatóanyag bemutatja, hogyan használhatja az Azure Virtual Network NAT szolgáltatást. Létre kell hoznia egy NAT-átjárót, amely kimenő kapcsolatot biztosít az Azure-beli virtuális gépek számára. 


[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Ezt az oktatóanyagot a Azure Cloud Shell használatával vagy a parancsok helyi futtatásával végezheti el.  Ha még nem használta Azure Cloud Shellt, [Jelentkezzen be most](https://shell.azure.com).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

Hozzon létre egy erőforráscsoportot a [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). Az Azure-erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat.

A következő példában létrehozunk egy **myResourceGroupNAT** nevű erőforráscsoportot a **eastus2** helyen:

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$loc = 'eastus2'
  
New-AzResourceGroup -Name $rsg -Location $loc
```

## <a name="create-the-nat-gateway"></a>NAT-átjáró létrehozása

A NAT-átjáró nyilvános IP-beállításai a következők:

* **Nyilvános IP-címek**
* **Nyilvános IP-előtag**

Mindkettő használható a NAT-átjáróval.

Ezt a forgatókönyvet egy nyilvános IP-cím és egy nyilvános IP-előtag hozzáadásával mutatjuk be.

### <a name="create-a-public-ip-address"></a>Hozzon létre egy nyilvános IP-címet

Az Internet eléréséhez szüksége lesz egy vagy több nyilvános IP-címre a NAT-átjáró számára. A [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) használatával hozzon létre egy **MYPUBLICIP** nevű nyilvános IP-cím-erőforrást a **myResourceGroupNAT**-ben. A parancs eredményét egy változóban fogja tárolni **$publicIP** későbbi használatra.

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$loc = 'eastus2'
$sku = 'Standard'
$pbnm = 'myPublicIP'
  
$publicIP = 
New-AzPublicIpAddress -Name $pbnm -ResourceGroupName $rsg -AllocationMethod Static -Location $loc -Sku $sku
```

### <a name="create-a-public-ip-prefix"></a>Nyilvános IP-előtag létrehozása

A [New-AzPublicIpPrefix](/powershell/module/az.network/new-azpublicipprefix) használatával hozzon létre egy **MYPUBLICIPPREFIX** nevű nyilvános IP-előtag-erőforrást a **myResourceGroupNAT**-ben.  A parancs eredménye egy **$publicIPPrefix** nevű változóban lesz tárolva későbbi használatra.

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$loc = 'eastus2'
$pxnm = 'myPublicIPprefix'

$publicIPPrefix = 
New-AzPublicIpPrefix -Name $pxnm -ResourceGroupName $rsg -Location $loc -PrefixLength 31
```

### <a name="create-a-nat-gateway-resource"></a>NAT-átjáró erőforrásának létrehozása

Ez a szakasz részletesen ismerteti, hogyan hozhatja létre és konfigurálhatja a NAT-szolgáltatás következő összetevőit a NAT-átjáró erőforrásának használatával:
  - Egy nyilvános IP-címkészlet és egy nyilvános IP-előtag, amelyet a NAT-átjáró erőforrása lefordított kimenő folyamatokhoz használ.
  - Módosítsa az üresjárati időkorlátot az alapértelmezett 4 perctől 10 percre.

Hozzon létre egy globális Azure NAT-átjárót a [New-AzNatGateway](/powershell/module/az.network/new-aznatgateway). A parancs eredménye létrehoz egy **myNATgateway** nevű átjáró-erőforrást, amely a nyilvános IP- **myPublicIP** és a nyilvános IP-előtag **myPublicIPprefix** használja. Az Üresjárati időkorlát 10 percre van beállítva.  A parancs eredménye egy **$natGateway** nevű változóban lesz tárolva későbbi használatra.

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$loc = 'eastus2'
$sku = 'Standard'
$gnm = 'myNATgateway'

$natGateway = 
New-AzNatGateway -Name $gnm -ResourceGroupName $rsg -PublicIpAddress $publicIP -PublicIpPrefix $publicIPPrefix -Location $loc -Sku $sku -IdleTimeoutInMinutes 10
  ```

Ezen a ponton a NAT-átjáró működik, és az összes hiányzó beállítással konfigurálható, hogy a virtuális hálózat mely alhálózatai használják azt.

## <a name="configure-virtual-network"></a>Virtuális hálózat konfigurálása

Hozza létre a virtuális hálózatot, és rendelje hozzá az alhálózatot az átjáróhoz.

Hozzon létre egy **myVnet** nevű virtuális hálózatot egy **mySubnet** nevű alhálózattal a New [-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig) használatával a **myResourceGroup** [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork)használatával. A virtuális hálózat IP-címe **192.168.0.0/16**. A virtuális hálózaton belüli alhálózat **192.168.0.0/24**.  A parancsok eredményét a rendszer **$subnet** és az **$vnet** későbbi használatra fogja tárolni.

```azurepowershell-interactive
$sbnm = 'mySubnet'
$vnnm = 'myVnet'
$rsg = 'myResourceGroupNAT'
$loc = 'eastus2'
$pfxsub = '192.168.0.0/24'
$pfxvn = '192.168.0.0/16'

$subnet = 
New-AzVirtualNetworkSubnetConfig -Name $sbnm -AddressPrefix $pfxsub -NatGateway $natGateway

$vnet = 
New-AzVirtualNetwork -Name $vnnm -ResourceGroupName $rsg -Location $loc -AddressPrefix $pfxvn -Subnet $subnet
```

A NAT szolgáltatás mostantól az összes internetes célhelyre irányuló kimenő forgalmat használja.  Nem szükséges konfigurálni a UDR.

## <a name="create-a-vm-to-use-the-nat-service"></a>Virtuális gép létrehozása a NAT szolgáltatás használatához

Most létrehozunk egy virtuális gépet a NAT szolgáltatás használatához.  Ez a virtuális gép egy nyilvános IP-címmel rendelkezik, amely egy példány szintű nyilvános IP-címet használ, hogy lehetővé tegye a virtuális gép elérését.  A NAT szolgáltatás a flow irányának ismerete, és az alapértelmezett internetes célhelyet váltja fel az alhálózaton. A virtuális gép nyilvános IP-címe nem használható kimenő kapcsolatokhoz.

### <a name="create-public-ip-for-source-vm"></a>Nyilvános IP-cím létrehozása a forrásként szolgáló virtuális gép számára

Létrehozunk egy nyilvános IP-címet, amelyet a virtuális gép eléréséhez kíván használni.  A [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) használatával hozzon létre egy **MYPUBLICIPVM** nevű nyilvános IP-cím-erőforrást a **myResourceGroupNAT**-ben.  A parancs eredménye egy **$publicIpVM** nevű változóban lesz tárolva későbbi használatra.

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$loc = 'eastus2'
$ipnm = 'myPublicIPVM'
$sku = 'Standard'

$publicIpVM = 
New-AzPublicIpAddress -Name $ipnm -ResourceGroupName $rsg -AllocationMethod Static -Location $loc -Sku $sku
```

### <a name="create-an-nsg-and-expose-ssh-endpoint-for-vm"></a>Hozzon létre egy NSG, és tegye elérhetővé SSH-végpontot a virtuális géphez

A standard nyilvános IP-címek a "biztonság alapértelmezés szerint", létre kell hozni egy NSG, amely engedélyezi a bejövő hozzáférést az SSH-hoz. A [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup) használatával hozzon létre egy **myNSG** nevű NSG-erőforrást. A [New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig) használatával hozzon létre egy NSG-szabályt az **SSH** -hozzáféréshez a **myResourceGroupNAT**-ben.  A parancs eredménye egy **$NSG** nevű változóban lesz tárolva későbbi használatra.

```azurepowershell-interactive
$rnm = 'ssh'
$rdesc = 'SSH access'
$acc = 'Allow'
$pro = 'Tcp'
$dir = 'Inbound'
$pri = '100'
$prt = '22'
$rsg = 'myResourceGroupNAT'
$rnm = 'myNSG'
$loc = 'eastus2'

$sshrule = 
New-AzNetworkSecurityRuleConfig -Name $rnm -Description $rdesc -Access $acc -Protocol $pro -Direction $dir -Priority $pri -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * -DestinationPortRange $prt

$nsg = 
New-AzNetworkSecurityGroup -ResourceGroupName $rsg -Name $rnm -Location $loc -SecurityRules $sshrule 
```

### <a name="create-nic-for-vm"></a>Hálózati adapter létrehozása virtuális géphez

Hozzon létre egy hálózati adaptert a [New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface) nevű **myNic**. Ez a parancs a nyilvános IP-címet és a hálózati biztonsági csoportot társítja. A parancs eredménye egy **$NIC** nevű változóban lesz tárolva későbbi használatra.

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$nmn = 'myNic'
$loc = 'eastus2'

$nic = 
New-AzNetworkInterface -ResourceGroupName $rsg -Name $nmn -NetworkSecurityGroupID $nsg.Id -PublicIPAddressID $publicIPVM.Id -SubnetID $vnet.Subnets[0].Id -Location $loc
```

### <a name="create-vm"></a>Virtuális gép létrehozása

#### <a name="create-ssh-key-pair"></a>SSH-kulcspár létrehozása

A rövid útmutató elvégzéséhez egy SSH-kulcspárra lesz szüksége. Ha már rendelkezésére áll egy SSH-kulcspár, kihagyhatja ezt a lépést.

Ssh-keygen használatával hozzon létre egy SSH-kulcspárt.

```azurepowershell-interactive
ssh-keygen -t rsa -b 2048
```
Az SSH-kulcspárok létrehozásáról, többek között a PuTTy használatáról az [SSH-kulcsok Windowsban való használatát](../virtual-machines/linux/ssh-from-windows.md) ismertető cikkben talál részletesebb információt.

Ha a Cloud Shell használatával hozza létre az SSH-kulcspárt, a rendszer a kulcspárt egy tároló rendszerképében tárolja. A [rendszer automatikusan létrehozza](../cloud-shell/persisting-shell-storage.md)ezt a Storage-fiókot. Ne törölje a Storage-fiókot vagy a fájlmegosztást a-ben, amíg a kulcsok beolvasása nem sikerült.

#### <a name="create-vm-configuration"></a>Virtuális gép konfigurációjának létrehozása

Egy virtuális gép PowerShellben való létrehozásához létre kell hoznia egy olyan konfigurációt, amely a rendszerkép használatára, a méretre és a hitelesítési beállításokra vonatkozó beállításokat tartalmaz. A rendszer a virtuális gép létrehozásához használja a konfigurációt.

Határozza meg az SSH hitelesítő adatokat, az operációs rendszer adatait és a virtuális gép méretét. Ebben a példában az SSH-kulcsot a ~/.ssh/id_rsa. pub tárolja.

```azurepowershell-interactive
#Define a credential object

$securePassword = 
ConvertTo-SecureString ' ' -AsPlainText -Force

$cred = 
New-Object System.Management.Automation.PSCredential ("azureuser", $securePassword)

# Create a virtual machine configuration

$vnm = 'myVM'
$vsz = 'Standard_D1'
$pub = 'Canonical'
$off = 'UbuntuServer'
$sku = '18.04-LTS'
$ver = 'latest'

$vmConfig = 
New-AzVMConfig -VMName $vnm -VMSize $vsz

Set-AzVMOperatingSystem -VM $vmConfig -Linux -ComputerName $vnm -Credential $cred -DisablePasswordAuthentication

Set-AzVMSourceImage -VM $vmConfig -PublisherName $pub -Offer $off -Skus $sku -Version $ver

Add-AzVMNetworkInterface -VM $vmConfig -Id $nic.Id

# Configure the SSH key

$sshPublicKey = cat ~/.ssh/id_rsa.pub

Add-AzVMSshPublicKey -VM $vmconfig -KeyData $sshPublicKey -Path "/home/azureuser/.ssh/authorized_keys"

```
A konfigurációs definíciók kombinálásával hozzon létre egy **myVM** nevű virtuális gépet a [New-AzVM](/powershell/module/az.compute/new-azvm) a **myResourceGroupNAT**-ben.

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$loc = 'eastus2'

New-AzVM -ResourceGroupName $rsg -Location $loc -VM $vmconfig
```

Várjon, amíg a virtuális gép felkészüljön a telepítésre, majd folytassa a további lépések végrehajtásával.

## <a name="discover-the-ip-address-of-the-vm"></a>A virtuális gép IP-címének felderítése

Először fel kell deríteni a létrehozott virtuális gép IP-címét. A virtuális gép nyilvános IP-címének lekéréséhez használja a [Get-AzPublicIpAddress](/powershell/module/az.network/get-azpublicipaddress). 

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$nmn = 'myPublicIPVM'

Get-AzPublicIpAddress -ResourceGroupName $rsg -Name $nmn | select IpAddress
``` 

>[!IMPORTANT]
>Másolja a nyilvános IP-címet, majd illessze be egy Jegyzettömbbe, hogy hozzáférjen a virtuális géphez.

### <a name="sign-in-to-vm"></a>Bejelentkezés a virtuális gépre

Az SSH hitelesítő adatait az előző művelet során a Cloud Shell kell tárolnia.  Nyisson meg egy [Azure Cloud Shell](https://shell.azure.com) a böngészőben. Használja az előző lépésben lekért IP-címet az SSH-val a virtuális gépre.

```bash
ssh azureuser@<ip-address-destination>
```

Most már készen áll a NAT szolgáltatás használatára.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, a [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) paranccsal távolíthatja el az erőforráscsoportot és a benne található összes erőforrást.

```azurepowershell-interactive 
Remove-AzResourceGroup -Name myResourceGroupNAT
```

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban létrehozott egy NAT-átjárót és egy virtuális gépet a használatára. 

Tekintse át a Azure Monitor mérőszámait a NAT szolgáltatás működésének megtekintéséhez. Problémák diagnosztizálása, például az elérhető SNAT-portok erőforrás-kimerülése.  A SNAT-portok erőforrásainak kimerítése további nyilvános IP-cím-erőforrások vagy nyilvános IP-előtag-erőforrások hozzáadásával vagy mindkettővel foglalkozik.


- Tudnivalók az [Azure Virtual Network NAT](./nat-overview.md) -ról
- További információ a [NAT-átjáró erőforrásáról](./nat-gateway-resource.md).
- Rövid útmutató a [NAT-átjáró erőforrásának Azure CLI](./quickstart-create-nat-gateway-cli.md)-vel történő üzembe helyezéséhez.
- Útmutató a NAT- [átjáró erőforrásának Azure PowerShell használatával](./quickstart-create-nat-gateway-powershell.md)történő üzembe helyezéséhez.
- Útmutató a NAT- [átjáró erőforrásának Azure Portal használatával](./quickstart-create-nat-gateway-portal.md)történő üzembe helyezéséhez.
> [!div class="nextstepaction"]
