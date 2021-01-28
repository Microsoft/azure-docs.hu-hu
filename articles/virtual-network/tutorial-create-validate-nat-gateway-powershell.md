---
title: NAT-átjáró létrehozása és tesztelése – Azure PowerShell
titlesuffix: Azure Virtual Network NAT
description: Ez az oktatóanyag bemutatja, hogyan hozhat létre NAT-átjárót a Azure PowerShell segítségével, és tesztelheti a NAT szolgáltatást
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumudD
Customer intent: I want to test a NAT gateway for outbound connectivity for my virtual network.
ms.service: virtual-network
ms.subservice: nat
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/11/2020
ms.author: allensu
ms.openlocfilehash: f319e77bd4801a2844a54ba8eba955c2b062fe7e
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/28/2021
ms.locfileid: "98934282"
---
# <a name="tutorial-create-a-nat-gateway-using-azure-powershell-and-test-the-nat-service"></a>Oktatóanyag: NAT-átjáró létrehozása a Azure PowerShell használatával és a NAT szolgáltatás tesztelése

Ebben az oktatóanyagban létrehoz egy NAT-átjárót az Azure-beli virtuális gépek kimenő kapcsolatának biztosításához. A NAT-átjáró teszteléséhez üzembe kell helyeznie egy forrás-és cél virtuális gépet. Tesztelje a NAT-átjárót úgy, hogy kimenő kapcsolatokat végez a nyilvános IP-címhez. Ezek a kapcsolatok a forrásról a cél virtuális gépre kerülnek. Ez az oktatóanyag az egyszerűség kedvéért két különböző virtuális hálózatban helyezi üzembe a forrás-és a célhelyet.


[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Ezt az oktatóanyagot a Azure Cloud Shell használatával vagy a megfelelő parancsok helyi futtatásával végezheti el.  Ha még soha nem használta Azure Cloud Shellt, [Jelentkezzen be most](https://shell.azure.com).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]


## <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

Hozzon létre egy erőforráscsoportot az [az group create](/cli/azure/group) paranccsal. Az Azure-erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat.

A következő példában létrehozunk egy **myResourceGroupNAT** nevű erőforráscsoportot a **eastus2** helyen:


```azurepowershell-interactive
$rgname = 'myResourceGroupNAT'
$loc = 'eastus2'

$rg = New-AzResourceGroup -Name $rgname -Location $loc

```

## <a name="create-the-nat-gateway"></a>NAT-átjáró létrehozása

### <a name="create-a-public-ip-address"></a>Hozzon létre egy nyilvános IP-címet

Az Internet eléréséhez szüksége lesz egy vagy több nyilvános IP-címre a NAT-átjáró számára. A [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) használatával hozzon létre egy **MYPUBLICIPSOURCE** nevű nyilvános IP-cím-erőforrást a **myResourceGroupNAT**-ben. A parancs eredménye egy **$publicIPsource** nevű változóban lesz tárolva későbbi használatra.

```azurepowershell-interactive
$pipname = 'myPublicIPsource'
$alm = 'Static'
$sku = 'Standard'

$publicIPsource = 
New-AzPublicIpAddress -Name $pipname -ResourceGroupName $rg.ResourceGroupName -AllocationMethod $alm -Sku $sku -Location $rg.Location

```

### <a name="create-a-public-ip-prefix"></a>Nyilvános IP-előtag létrehozása

 A [New-AzPublicIpPrefix](/powershell/module/az.network/new-azpublicipprefix) használatával hozzon létre egy **MYPUBLICIPPREFIXSOURCE** nevű nyilvános IP-előtag-erőforrást a **myResourceGroupNAT**-ben.  A parancs eredménye egy **$publicIPPrefixsource** nevű változóban lesz tárolva későbbi használatra.

```azurepowershell-interactive
$prefixname = 'mypublicIPprefixsource'

$publicIPPrefixsource = 
New-AzPublicIpPrefix -Name $prefixname -ResourceGroupName $rg.ResourceGroupName -PrefixLength 31 -Location $rg.Location

```

### <a name="create-a-nat-gateway-resource"></a>NAT-átjáró erőforrásának létrehozása

Ez a szakasz részletesen ismerteti, hogyan hozhatja létre és konfigurálhatja a NAT-szolgáltatás következő összetevőit a NAT-átjáró erőforrásának használatával:
  - Egy nyilvános IP-címkészlet és egy nyilvános IP-előtag, amelyet a NAT-átjáró erőforrása lefordított kimenő folyamatokhoz használ.
  - Módosítsa az üresjárati időkorlátot az alapértelmezett 4 perctől 10 percre.

Hozzon létre egy globális Azure NAT-átjárót a [New-AzNatGateway](/powershell/module/az.network/new-aznatgateway). A parancs eredménye létrehoz egy **myNATgateway** nevű átjáró-erőforrást, amely a nyilvános IP- **myPublicIPsource** és a nyilvános IP-előtag **myPublicIPprefixsource** használja. Az Üresjárati időkorlát 10 percre van beállítva.  A parancs eredménye egy **$natGateway** nevű változóban lesz tárolva későbbi használatra.

```azurepowershell-interactive
$sku = 'Standard'
$natname = 'myNATgateway'

$natGateway = 
New-AzNatGateway -Name $natname -ResourceGroupName $rg.ResourceGroupName -PublicIpAddress $publicIPsource -PublicIpPrefix $publicIPPrefixsource -Sku $sku -IdleTimeoutInMinutes 10 -Location $rg.Location

  ```

Ezen a ponton a NAT-átjáró működik, és az összes hiányzó beállítással konfigurálható, hogy a virtuális hálózat mely alhálózatai használják azt.

## <a name="prepare-the-source-for-outbound-traffic"></a>A forrás előkészítése a kimenő forgalomhoz

Végigvezeti a teljes tesztkörnyezet beállításán. A NAT-átjáró ellenőrzéséhez nyílt forráskódú eszközök használatával állítson be egy tesztet. Kezdjük a forrással, amely a korábban létrehozott NAT-átjárót fogja használni.

### <a name="configure-virtual-network-for-source"></a>Virtuális hálózat konfigurálása a forráshoz

Hozza létre a virtuális hálózatot, és rendelje hozzá az alhálózatot az átjáróhoz.

Hozzon létre egy **myVnetsource** nevű virtuális hálózatot egy **mySubnetsource** nevű alhálózattal a New [-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig) használatával a **myResourceGroupNAT** [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork)használatával. A virtuális hálózat IP-címe **192.168.0.0/16**. A virtuális hálózaton belüli alhálózat **192.168.0.0/24**.  A parancsok eredményét a rendszer **$subnetsource** és az **$vnetsource** későbbi használatra fogja tárolni.

```azurepowershell-interactive
$subnetname = 'mySubnetsource'
$subnetprefix = '192.168.0.0/24'
$vnetname = 'myVnetsource'
$vnetprefix = '192.168.0.0/16'

$subnetsource = 
New-AzVirtualNetworkSubnetConfig -Name $subnetname -AddressPrefix $subnetprefix -NatGateway $natGateway

$vnetsource = 
New-AzVirtualNetwork -Name $vnetname -ResourceGroupName $rg.ResourceGroupName -AddressPrefix $vnetprefix -Subnet $subnetsource -Location $rg.Location

```

A NAT szolgáltatás mostantól az összes internetes célhelyre irányuló kimenő forgalmat használja.  Nem szükséges konfigurálni a UDR.

A NAT-átjáró teszteléséhez létre kell hoznia egy forrásoldali virtuális gépet.  Egy nyilvános IP-cím típusú erőforrást egy példány szintű nyilvános IP-címként rendelünk hozzá a virtuális géphez a külvilágtól való hozzáféréshez. Ez a címe csak a teszthez való hozzáféréshez használatos.  Bemutatjuk, hogy a NAT szolgáltatás milyen elsőbbséget élvez a többi kimenő lehetőséggel szemben.

Azt is megteheti, hogy nyilvános IP-cím nélkül hozza létre ezt a virtuális gépet, és egy olyan virtuális gépet hoz létre, amelyet Jumpbox használ nyilvános IP-cím nélkül.

### <a name="create-public-ip-for-source-vm"></a>Nyilvános IP-cím létrehozása a forrásként szolgáló virtuális gép számára

Létrehozunk egy nyilvános IP-címet, amelyet a virtuális gép eléréséhez kíván használni.  A [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) használatával hozzon létre egy **MYPUBLICIPVM** nevű nyilvános IP-cím-erőforrást a **myResourceGroupNAT**-ben.  A parancs eredménye egy **$publicIpsourceVM** nevű változóban lesz tárolva későbbi használatra.

```azurepowershell-interactive
$sku = 'Standard'
$pipvmname = 'myPublicIpsourceVM'
$alm = 'Static'

$publicIpsourceVM = 
New-AzPublicIpAddress -Name $pipvmname -ResourceGroupName $rg.ResourceGroupName -AllocationMethod $alm -sku $sku -Location $rg.Location

```

### <a name="create-an-nsg-and-expose-ssh-endpoint-for-vm"></a>Hozzon létre egy NSG, és tegye elérhetővé SSH-végpontot a virtuális géphez

Mivel a standard nyilvános IP-címek alapértelmezetten "biztonságosak", hozzunk létre egy NSG, amely engedélyezi a bejövő hozzáférést az SSH-hoz. A NAT szolgáltatás a flow irányát ismeri. Ez a NSG nem használható a kimenő forgalomhoz, ha a NAT-átjáró ugyanazon az alhálózaton van konfigurálva. A [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup) használatával hozzon létre egy **myNSGsource** nevű NSG-erőforrást. A [New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig) használatával hozzon létre egy NSG-szabályt az **SSH** -hozzáféréshez a **myResourceGroupNAT**-ben. A parancs eredményét az **$nsgsource** nevű változó fogja tárolni későbbi használatra.

```azurepowershell-interactive
$rnm = 'ssh'
$rdsc = 'SSH access'
$acc = 'Allow'
$prt = 'Tcp'
$dir = 'Inbound'
$nsnm = 'myNSGsource'

$sshrule = 
New-AzNetworkSecurityRuleConfig -Name $rnm -Description $rdsc -Access $acc -Protocol $prt -Direction $dir -Priority 100 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * -DestinationPortRange 22

$nsgsource = 
New-AzNetworkSecurityGroup -ResourceGroupName $rg.ResourceGroupName -Name $nsnm -SecurityRules $sshrule -Location $rg.Location

```

### <a name="create-nic-for-source-vm"></a>Hálózati adapter létrehozása forrásként szolgáló virtuális géphez

Hozzon létre egy hálózati adaptert a [New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface) nevű **myNicsource**. Ez a parancs a nyilvános IP-címet és a hálózati biztonsági csoportot rendeli hozzá. A parancs eredménye egy **$nicsource** nevű változóban lesz tárolva későbbi használatra.

```azurepowershell-interactive
$nin = 'myNicsource'

$nicsource = 
New-AzNetworkInterface -ResourceGroupName $rg.ResourceGroupName -Name $nin -NetworkSecurityGroupID $nsgsource.Id -PublicIPAddressID $publicIPVMsource.Id -SubnetID $vnetsource.Subnets[0].Id -Location $rg.Location

```

### <a name="create-a-source-vm"></a>Forrásként szolgáló virtuális gép létrehozása

#### <a name="create-ssh-key-pair"></a>SSH-kulcspár létrehozása

A rövid útmutató elvégzéséhez egy SSH-kulcspárra lesz szüksége. Ha már rendelkezésére áll egy SSH-kulcspár, kihagyhatja ezt a lépést.

Ssh-keygen használatával hozzon létre egy SSH-kulcspárt.

```azurepowershell-interactive
ssh-keygen -t rsa -b 2048

```
Az SSH-kulcspárok létrehozásáról, többek között a PuTTy használatáról az [SSH-kulcsok Windowsban való használatát](../virtual-machines/linux/ssh-from-windows.md) ismertető cikkben talál részletesebb információt.

Ha a Cloud Shell használatával hozza létre az SSH-kulcspárt, a rendszer a kulcspárt egy tároló rendszerképében tárolja. A [rendszer automatikusan létrehozza](../cloud-shell/persisting-shell-storage.md)ezt a Storage-fiókot. Ne törölje a Storage-fiókot vagy a fájlmegosztást a-ben, amíg a kulcsok beolvasása nem sikerült.

#### <a name="create-vm-configuration"></a>Virtuális gép konfigurációjának létrehozása

A PowerShellben a virtuális gépek létrehozáshoz egy konfigurációt kell létrehozni, amely a használni kívánt rendszerképpel, mérettel és hitelesítési beállításokkal egyező beállításokkal rendelkezik. A rendszer ezután ezzel a konfigurációval hozza létre a virtuális gépet.

Határozza meg az SSH hitelesítő adatokat, az operációs rendszer adatait és a virtuális gép méretét. Ebben a példában az SSH-kulcsot a ~/.ssh/id_rsa. pub tárolja.

```azurepowershell-interactive
# Define a credential object

$securePassword = 
ConvertTo-SecureString ' ' -AsPlainText -Force
$cred = 
New-Object System.Management.Automation.PSCredential ("azureuser", $securePassword)

# Create a virtual machine configuration
$vmn = 'myVMsource'
$vms = 'Standard_DS1_v2'
$pub = 'Canonical'
$off = 'UbuntuServer'
$skus = '18.04-LTS'
$ver = 'latest'

$vmConfigsource = 
New-AzVMConfig -VMName $vmn -VMSize $vms

Set-AzVMOperatingSystem -VM $vmConfigsource -Linux -ComputerName $vmn -Credential $cred -DisablePasswordAuthentication

Set-AzVMSourceImage -VM $vmConfigsource -PublisherName $pub -Offer $off -Skus $skus -Version $ver

Add-AzVMNetworkInterface -VM $vmConfigsource -Id $nicsource.Id

# Configure the SSH key

$sshPublicKey = cat ~/.ssh/id_rsa.pub

Add-AzVMSshPublicKey -VM $vmConfigsource -KeyData $sshPublicKey -Path "/home/azureuser/.ssh/authorized_keys"

```
A konfigurációs definíciók kombinálásával hozzon létre egy **myVMsource** nevű virtuális gépet a [New-AzVM](/powershell/module/az.compute/new-azvm) a **myResourceGroupNAT**-ben.

```azurepowershell-interactive
New-AzVM -ResourceGroupName $rg.ResourceGroupName -VM $vmConfigsource -Location $rg.Location

```

Amíg a parancs azonnal visszatér, eltarthat néhány percig, amíg a virtuális gép üzembe kerül.

## <a name="prepare-destination-for-outbound-traffic"></a>Cél előkészítése a kimenő forgalomhoz

Most létrehozunk egy célhelyet a NAT szolgáltatás által lefordított kimenő forgalomhoz, hogy tesztelni lehessen.

### <a name="configure-virtual-network-for-destination"></a>Virtuális hálózat konfigurálása célhelyként

Létre kell hozni egy virtuális hálózatot, amelyben a cél virtuális gép lesz.  Ezek a parancsok ugyanazok a lépések, mint a forrásoldali virtuális gép esetén. Kis változtatások lettek hozzáadva a cél végpont számára.

Hozzon létre egy **myVnetdestination** nevű virtuális hálózatot egy **mySubnetdestination** nevű alhálózattal a New [-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig) használatával a **myResourceGroupNAT** [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork)használatával. A virtuális hálózat IP-címe **192.168.0.0/16**. A virtuális hálózaton belüli alhálózat **192.168.0.0/24**.  A parancsok eredményét a rendszer **$subnetdestination** és az **$vnetdestination** későbbi használatra fogja tárolni.

```azurepowershell-interactive
$sbdn = 'mySubnetdestination'
$spfx = '192.168.0.0/24'
$vdn = 'myVnetdestination'
$vpfx = '192.168.0.0/16'

$subnetdestination = 
New-AzVirtualNetworkSubnetConfig -Name $sbdn -AddressPrefix $spfx

$vnetdestination = 
New-AzVirtualNetwork -Name $vdn -ResourceGroupName $rg.ResourceGroupName -AddressPrefix $vpfx -Subnet $subnetdestination -Location $rg.Location

```

### <a name="create-public-ip-for-destination-vm"></a>Nyilvános IP-cím létrehozása a cél virtuális géphez

Létrehozunk egy nyilvános IP-címet, amely a cél virtuális gép elérésére szolgál.  A [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) használatával hozzon létre egy **MYPUBLICIPDESTINATIONVM** nevű nyilvános IP-cím-erőforrást a **myResourceGroupNAT**-ben.  A parancs eredménye egy **$publicIpdestinationVM** nevű változóban lesz tárolva későbbi használatra.

```azurepowershell-interactive
$sku = 'Standard'
$all = 'Static'
$pipd = 'myPublicIPdestinationVM'

$publicIpdestinationVM = 
New-AzPublicIpAddress -Name $pipd -ResourceGroupName $rg.ResourceGroupName -AllocationMethod $all -Sku $sku -Location $rg.Location

```

### <a name="create-an-nsg-and-expose-ssh-and-http-endpoint-for-vm"></a>Hozzon létre egy NSG, és tegye elérhetővé SSH-és HTTP-végpontot a virtuális gépen

A standard nyilvános IP-címek alapértelmezés szerint "biztonságosak", egy NSG hozunk létre, amely lehetővé teszi az SSH bejövő hozzáférését. A [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup) használatával hozzon létre egy **myNSGdestination** nevű NSG-erőforrást. A [New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig) használatával hozzon létre egy NSG-szabályt az **SSH-hozzáféréshez.**  A [New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig) használatával hozzon létre egy NSG-szabályt a **http-hozzáféréshez.** Mindkét szabály a **myResourceGroupNAT**-ben lesz létrehozva. A parancs eredménye egy **$nsgdestination** nevű változóban lesz tárolva későbbi használatra.

```azurepowershell-interactive
$snm = 'ssh'
$sdsc = 'SSH access'
$acc = 'Allow'
$prt = 'Tcp'
$dir = 'Inbound'
$hnm = 'http'
$hdsc = 'HTTP access'
$nsnm = 'myNSGdestination'

$sshrule = 
New-AzNetworkSecurityRuleConfig -Name $snm -Description $sdsc -Access $acc -Protocol $prt -Direction $dir -Priority 100 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * -DestinationPortRange 22

$httprule = 
New-AzNetworkSecurityRuleConfig -Name $hnm -Description $hdsc -Access $acc -Protocol $prt -Direction $dir -Priority 101 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * -DestinationPortRange 80

$nsgdestination = 
New-AzNetworkSecurityGroup -ResourceGroupName $rg.ResourceGroupName -Name $nsnm -SecurityRules $sshrule,$httprule -Location $rg.Location

```

### <a name="create-nic-for-destination-vm"></a>Hálózati adapter létrehozása a cél virtuális géphez

Hozzon létre egy hálózati adaptert a [New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface) nevű **myNicdestination**. Ez a parancs társítva lesz a nyilvános IP-címhez és a hálózati biztonsági csoporthoz. A parancs eredménye egy **$nicdestination** nevű változóban lesz tárolva későbbi használatra.

```azurepowershell-interactive
$nnm = 'myNicdestination'

$nicdestination = 
New-AzNetworkInterface -ResourceGroupName $rg.ResourceGroupName -Name $nnm -NetworkSecurityGroupID $nsgdestination.Id -PublicIPAddressID $publicIPdestinationVM.Id -SubnetID $vnetdestination.Subnets[0].Id -Location $rg.Location

```

### <a name="create-a-destination-vm"></a>Cél virtuális gép létrehozása

#### <a name="create-vm-configuration"></a>Virtuális gép konfigurációjának létrehozása

A PowerShellben a virtuális gépek létrehozáshoz egy konfigurációt kell létrehozni, amely a használni kívánt rendszerképpel, mérettel és hitelesítési beállításokkal egyező beállításokkal rendelkezik. A rendszer ezután ezzel a konfigurációval hozza létre a virtuális gépet.

Határozza meg az SSH hitelesítő adatokat, az operációs rendszer adatait és a virtuális gép méretét. Ebben a példában az SSH-kulcsot a ~/.ssh/id_rsa. pub tárolja.

```azurepowershell-interactive
# Define a credential object

$securePassword = 
ConvertTo-SecureString ' ' -AsPlainText -Force
$cred = 
New-Object System.Management.Automation.PSCredential ("azureuser", $securePassword)

# Create a virtual machine configuration

$vmd = 'myVMdestination'
$vms = 'Standard_DS1_v2'
$pub = 'Canonical'
$off = 'UbuntuServer'
$skus = '18.04-LTS'
$ver = 'latest'

$vmConfigdestination = New-AzVMConfig -VMName $vmd -VMSize $vms

Set-AzVMOperatingSystem -VM $vmConfigdestination -Linux -ComputerName $vmd -Credential $cred -DisablePasswordAuthentication

Set-AzVMSourceImage -VM $vmConfigdestination -PublisherName $pub -Offer $off -Skus $skus -Version $ver

Add-AzVMNetworkInterface -VM $vmConfigdestination -Id $nicdestination.Id

# Configure the SSH key

$sshPublicKey = cat ~/.ssh/id_rsa.pub

Add-AzVMSshPublicKey -VM $vmConfigdestination -KeyData $sshPublicKey -Path "/home/azureuser/.ssh/authorized_keys"

```
A konfigurációs definíciók kombinálásával hozzon létre egy **myVMdestination** nevű virtuális gépet a [New-AzVM](/powershell/module/az.compute/new-azvm) a **myResourceGroupNAT**-ben.

```azurepowershell-interactive

New-AzVM -ResourceGroupName $rg.ResourceGroupName -VM $vmConfigdestination -Location $rg.Location

```

Amíg a parancs azonnal visszatér, eltarthat néhány percig, amíg a virtuális gép üzembe kerül.

## <a name="prepare-a-web-server-and-test-payload-on-destination-vm"></a>Webkiszolgáló és tesztelési tartalom előkészítése a célként megadott virtuális gépen

Először fel kell deríteni a cél virtuális gép IP-címét.  A virtuális gép nyilvános IP-címének lekéréséhez használja a [Get-AzPublicIpAddress](/powershell/module/az.network/get-azpublicipaddress). 

```azurepowershell-interactive
$pipname = 'myPublicIPdestinationVM'
  
$destip = Get-AzPublicIpAddress -ResourceGroupName $rg.ResourceGroupName -Name $pipname | select IpAddress

$destip

``` 

>[!IMPORTANT]
>Másolja a nyilvános IP-címet, majd illessze be egy Jegyzettömbbe, hogy azt a következő lépésekben használhatja. Jelezze, hogy ez a cél virtuális gép.

### <a name="sign-in-to-destination-vm"></a>Bejelentkezés a cél virtuális gépre

Az SSH hitelesítő adatait az előző művelet során a Cloud Shell kell tárolnia.  Nyisson meg egy [Azure Cloud Shell](https://shell.azure.com) a böngészőben. Használja az előző lépésben lekért IP-címet az SSH-val a virtuális gépre. 

```azurepowershell-interactive
ssh azureuser@$destip

```

Másolja ki és illessze be az alábbi parancsokat a bejelentkezés után.  

```bash
sudo apt -y update && \
sudo apt -y upgrade && \
sudo apt -y install nginx && \
sudo ln -sf /dev/null /var/log/nginx/access.log && \
sudo touch /var/www/html/index.html && \
sudo rm /var/www/html/index.nginx-debian.html && \
sudo dd if=/dev/zero of=/var/www/html/100k bs=1024 count=100

```

Ezek a parancsok frissítik a virtuális gépet, telepítik az Nginx-et, és létrehozunk egy 100-es kilobájtos fájlt. Ezt a fájlt a rendszer a forrás virtuális gépről a NAT szolgáltatás használatával kéri le.

A célként megadott virtuális géppel zárjuk be az SSH-munkamenetet.

## <a name="prepare-test-on-source-vm"></a>Teszt előkészítése a forrás virtuális gépen

Először fel kell deríteni a forrás virtuális gép IP-címét.  A virtuális gép nyilvános IP-címének lekéréséhez használja a [Get-AzPublicIpAddress](/powershell/module/az.network/get-azpublicipaddress). 

```azurepowershell-interactive
$pipname = 'myPublicIPsourceVM'

$srcip = Get-AzPublicIpAddress -ResourceGroupName $rg.ResourceGroupName -Name $pipname | select IpAddress

$srcip

``` 

>[!IMPORTANT]
>Másolja a nyilvános IP-címet, majd illessze be egy Jegyzettömbbe, hogy azt a következő lépésekben használhatja. Jelezze, hogy ez a forrás virtuális gép.

### <a name="log-into-source-vm"></a>Bejelentkezés a forrás virtuális gépre

Az SSH hitelesítő adatait a rendszer a Cloud Shell tárolja. Nyisson meg egy új lapot a böngészőben [Azure Cloud Shellhoz](https://shell.azure.com) .  Használja az előző lépésben lekért IP-címet az SSH-val a virtuális gépre. 

```azurepowershell-interactive
ssh azureuser@$srcip

```

Másolja és illessze be a következő parancsokat a NAT szolgáltatás tesztelésének előkészítéséhez.

```bash
sudo apt -y update && \
sudo apt -y upgrade && \
sudo apt install -y nload golang && \
echo 'export GOPATH=${HOME}/go' >> .bashrc && \
echo 'export PATH=${PATH}:${GOPATH}/bin' >> .bashrc && \
. ~/.bashrc &&
go get -u github.com/rakyll/hey

```

Ezek a parancsok frissítik a virtuális gépet, telepítheti a Go-t, telepítheti a [Hey](https://github.com/rakyll/hey) alkalmazást a githubról, és frissítheti a rendszerhéj-környezetet.

Most már készen áll a NAT szolgáltatás tesztelésére.

## <a name="validate-nat-service"></a>NAT szolgáltatás ellenőrzése

Ha bejelentkezett a forrás virtuális gépre, a **curl** és a **Hey** használatával is létrehozhat KÉRÉSEKET a cél IP-címhez.

Az 100-kilobájtos fájl beolvasásához használja a curlt.  Cserélje le az **\<ip-address-destination>** alábbi példában a korábban másolt cél IP-címet.  A **--output** paraméter azt jelzi, hogy a beolvasott fájl el lesz vetve.

```bash
curl http://<ip-address-destination>/100k --output /dev/null

```

A **Hey** használatával több kérelem is létrehozható. Ismét cserélje le **\<ip-address-destination>** a helyére a korábban másolt cél IP-címet.

```bash
hey -n 100 -c 10 -t 30 --disable-keepalive http://<ip-address-destination>/100k

```

Ezzel a paranccsal az 100-es kérések, 10 párhuzamosan, 30 másodperces időkorláttal fognak létrejönni. A TCP-kapcsolatok nem lesznek újra felhasználva.  Minden kérelem 100 KB-ot fog beolvasni.  A Futtatás végén a **Hey** jelentést készít arról, hogy milyen jól működik a NAT szolgáltatás.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, a [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) paranccsal távolíthatja el az erőforráscsoportot és a benne található összes erőforrást.

```azurepowershell-interactive 
Remove-AzResourceGroup -Name $rg.ResourceGroupName

```

## <a name="next-steps"></a>Következő lépések
Ebben az oktatóanyagban létrehozott egy NAT-átjárót, létrehozta a forrás és a cél virtuális gépet, majd tesztelte a NAT-átjárót.

Tekintse át a Azure Monitor mérőszámait a NAT szolgáltatás működésének megtekintéséhez. Problémák diagnosztizálása, például az elérhető SNAT-portok erőforrás-kimerülése.  A SNAT-portok erőforrás-kimerülése könnyen kezelhető további nyilvános IP-címek vagy nyilvános IP-előtag-erőforrások hozzáadásával vagy mindkettővel.

- Tudnivalók a [Virtual Network NAT](./nat-overview.md) -ról
- További információ a [NAT-átjáró erőforrásáról](./nat-gateway-resource.md).
- Rövid útmutató a [NAT-átjáró erőforrásának Azure CLI](./quickstart-create-nat-gateway-cli.md)-vel történő üzembe helyezéséhez.
- Útmutató a NAT- [átjáró erőforrásának Azure PowerShell használatával](./quickstart-create-nat-gateway-powershell.md)történő üzembe helyezéséhez.
- Útmutató a NAT- [átjáró erőforrásának Azure Portal használatával](./quickstart-create-nat-gateway-portal.md)történő üzembe helyezéséhez.

> [!div class="nextstepaction"]
