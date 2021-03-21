---
title: Rövid útmutató – Linux rendszerű virtuális gép létrehozása Azure PowerShell
description: Ebből a rövid útmutatóból elsajátíthatja, hogyan használható az Azure PowerShell Linux rendszerű virtuális gépek létrehozására
author: cynthn
ms.service: virtual-machines
ms.collection: linux
ms.topic: quickstart
ms.workload: infrastructure
ms.date: 07/31/2020
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 2cef611fe79ca04303840076b09b4cf6344b7e7d
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102616230"
---
# <a name="quickstart-create-a-linux-virtual-machine-in-azure-with-powershell"></a>Rövid útmutató: Linux virtuális gép létrehozása az Azure-ban PowerShell-lel

Az Azure PowerShell-modul az Azure-erőforrások PowerShell-parancssorból vagy szkriptekkel történő létrehozására és kezelésére használható. Ez a rövid útmutató bemutatja, hogyan helyezhet üzembe az Azure PowerShell modullal Linux rendszerű virtuális gépeket (VM-eket) az Azure-ban. Ez a rövid útmutató az Ubuntu 18,04 LTS Marketplace rendszerképét használja a Canonicalből. A virtuális gép működésének megtekintéséhez létrehoz majd egy SSH-kapcsolatot a virtuális géppel, és telepíti az NGINX-webkiszolgálót.

Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="launch-azure-cloud-shell"></a>Az Azure Cloud Shell elindítása

Az Azure Cloud Shell egy olyan ingyenes interaktív kezelőfelület, amelyet a jelen cikkben található lépések futtatására használhat. A fiókjával való használat érdekében a gyakran használt Azure-eszközök már előre telepítve és konfigurálva vannak rajta. 

A Cloud Shell megnyitásához válassza a **Kipróbálás** lehetőséget egy kódblokk jobb felső sarkában. A **Copy** (másolás) gombra kattintva másolja és illessze be a kódot a Cloud Shellbe, majd nyomja le az Enter billentyűt a futtatáshoz.

## <a name="create-ssh-key-pair"></a>SSH-kulcspár létrehozása

SSH [-keygen](https://www.ssh.com/ssh/keygen/) használatával hozzon létre egy SSH-kulcspárt. Ha már rendelkezésére áll egy SSH-kulcspár, kihagyhatja ezt a lépést.


```azurepowershell-interactive
ssh-keygen -t rsa -b 4096
```

A rendszer megkéri, hogy adjon meg egy fájlnevet a kulcspár számára, vagy az **ENTER billentyűt** megnyomva használja az alapértelmezett helyet `/home/<username>/.ssh/id_rsa` . Ha szeretné, létrehozhat egy jelszót is a kulcsokhoz.

Az SSH-kulcspár létrehozásával kapcsolatos részletes információkért lásd: [az ssh-kulcsok használata a Windowsban](ssh-from-windows.md).

Ha a Cloud Shell használatával hozza létre az SSH-kulcspárt, akkor azt a rendszer a [Cloud Shell által automatikusan létrehozott Storage-fiókban](../../cloud-shell/persisting-shell-storage.md)tárolja. Ne törölje a Storage-fiókot, vagy a fájlok megosztását, amíg a kulcsok beolvasása vagy a virtuális gép elérésének elvesztése megszűnik. 

## <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

Hozzon létre egy Azure-erőforráscsoportot a [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). Az erőforráscsoportok olyan logikai tárolók, amelyekbe a rendszer üzembe helyezi az Azure-erőforrásokat és kezeli azokat:

```azurepowershell-interactive
New-AzResourceGroup -Name "myResourceGroup" -Location "EastUS"
```

## <a name="create-virtual-network-resources"></a>Virtuális hálózati erőforrások létrehozása

Hozzon létre egy virtuális hálózatot, egy alhálózatot és egy nyilvános IP-címet. Ezek az erőforrások a virtuális gép hálózati csatlakoztatásának biztosítására, illetve az internethez csatlakoztatására használatosak:

```azurepowershell-interactive
# Create a subnet configuration
$subnetConfig = New-AzVirtualNetworkSubnetConfig `
  -Name "mySubnet" `
  -AddressPrefix 192.168.1.0/24

# Create a virtual network
$vnet = New-AzVirtualNetwork `
  -ResourceGroupName "myResourceGroup" `
  -Location "EastUS" `
  -Name "myVNET" `
  -AddressPrefix 192.168.0.0/16 `
  -Subnet $subnetConfig

# Create a public IP address and specify a DNS name
$pip = New-AzPublicIpAddress `
  -ResourceGroupName "myResourceGroup" `
  -Location "EastUS" `
  -AllocationMethod Static `
  -IdleTimeoutInMinutes 4 `
  -Name "mypublicdns$(Get-Random)"
```

Hozzon létre egy Azure hálózati biztonsági csoportot és egy adatforgalmi szabályt. A hálózati biztonsági csoport bejövő és kimenő szabályok használatával teszi biztonságossá a virtuális gépet. A következő példában létrehozunk egy bejövő forgalomra vonatkozó szabályt a 22-es TCP-porthoz, amely engedélyezi az SSH-kapcsolatokat. A bejövő webforgalom engedélyezéséhez létrehozunk egy bejövő forgalomra vonatkozó szabályt is a 80-as TCP-porthoz.

```azurepowershell-interactive
# Create an inbound network security group rule for port 22
$nsgRuleSSH = New-AzNetworkSecurityRuleConfig `
  -Name "myNetworkSecurityGroupRuleSSH"  `
  -Protocol "Tcp" `
  -Direction "Inbound" `
  -Priority 1000 `
  -SourceAddressPrefix * `
  -SourcePortRange * `
  -DestinationAddressPrefix * `
  -DestinationPortRange 22 `
  -Access "Allow"

# Create an inbound network security group rule for port 80
$nsgRuleWeb = New-AzNetworkSecurityRuleConfig `
  -Name "myNetworkSecurityGroupRuleWWW"  `
  -Protocol "Tcp" `
  -Direction "Inbound" `
  -Priority 1001 `
  -SourceAddressPrefix * `
  -SourcePortRange * `
  -DestinationAddressPrefix * `
  -DestinationPortRange 80 `
  -Access "Allow"

# Create a network security group
$nsg = New-AzNetworkSecurityGroup `
  -ResourceGroupName "myResourceGroup" `
  -Location "EastUS" `
  -Name "myNetworkSecurityGroup" `
  -SecurityRules $nsgRuleSSH,$nsgRuleWeb
```

Hozzon létre egy virtuális hálózati adaptert (NIC) a [New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface). A virtuális hálózati kártya csatlakoztatja a virtuális gépet egy alhálózathoz, egy hálózati biztonsági csoporthoz és egy nyilvános IP-címhez.

```azurepowershell-interactive
# Create a virtual network card and associate with public IP address and NSG
$nic = New-AzNetworkInterface `
  -Name "myNic" `
  -ResourceGroupName "myResourceGroup" `
  -Location "EastUS" `
  -SubnetId $vnet.Subnets[0].Id `
  -PublicIpAddressId $pip.Id `
  -NetworkSecurityGroupId $nsg.Id
```

## <a name="create-a-virtual-machine"></a>Virtuális gép létrehozása

A PowerShellben a virtuális gépek létrehozáshoz egy konfigurációt kell létrehozni, amely a használni kívánt rendszerképpel, mérettel és hitelesítési beállításokkal egyező beállításokkal rendelkezik. A rendszer ezután ezzel a konfigurációval hozza létre a virtuális gépet.

Határozza meg az SSH hitelesítő adatokat, az operációs rendszer adatait és a virtuális gép méretét. Ebben a példában az SSH-kulcsot a `~/.ssh/id_rsa.pub` fájl tárolja. 

```azurepowershell-interactive
# Define a credential object
$securePassword = ConvertTo-SecureString ' ' -AsPlainText -Force
$cred = New-Object System.Management.Automation.PSCredential ("azureuser", $securePassword)

# Create a virtual machine configuration
$vmConfig = New-AzVMConfig `
  -VMName "myVM" `
  -VMSize "Standard_D1" | `
Set-AzVMOperatingSystem `
  -Linux `
  -ComputerName "myVM" `
  -Credential $cred `
  -DisablePasswordAuthentication | `
Set-AzVMSourceImage `
  -PublisherName "Canonical" `
  -Offer "UbuntuServer" `
  -Skus "18.04-LTS" `
  -Version "latest" | `
Add-AzVMNetworkInterface `
  -Id $nic.Id

# Configure the SSH key
$sshPublicKey = cat ~/.ssh/id_rsa.pub
Add-AzVMSshPublicKey `
  -VM $vmconfig `
  -KeyData $sshPublicKey `
  -Path "/home/azureuser/.ssh/authorized_keys"
```

Most egyesítse az előző konfigurációs definíciókat, és hozzon létre [új-AzVM](/powershell/module/az.compute/new-azvm):

```azurepowershell-interactive
New-AzVM `
  -ResourceGroupName "myResourceGroup" `
  -Location eastus -VM $vmConfig
```

A virtuális gép üzembe helyezése eltarthat néhány percig. Az üzembe helyezés végeztével lépjen tovább a következő szakaszra.

## <a name="connect-to-the-vm"></a>Kapcsolódás a virtuális géphez

Hozzon létre egy SSH-kapcsolatot a virtuális géppel a nyilvános IP-cím alapján. A virtuális gép nyilvános IP-címének megtekintéséhez használja a [Get-AzPublicIpAddress](/powershell/module/az.network/get-azpublicipaddress) parancsmagot:

```azurepowershell-interactive
Get-AzPublicIpAddress -ResourceGroupName "myResourceGroup" | Select "IpAddress"
```

Az SSH-kulcspár létrehozásához használt rendszerhéj használatával illessze be a következő parancsot a rendszerhéjba egy SSH-munkamenet létrehozásához. Cserélje le a *10.111.12.123* -t a virtuális gép IP-címére.

```bash
ssh azureuser@10.111.12.123
```

Ha a rendszer kéri, adja meg a következő bejelentkezési felhasználónevet: *azureuser*. Ha jelszót használ az SSH-kulcsokkal, akkor azt a rendszer kérésére meg kell adnia.


## <a name="install-nginx"></a>Az NGINX telepítése

A virtuális gép működésének ellenőrzéséhez telepítse az NGINX-webkiszolgálót. Frissítse a csomag forrásait, majd telepítse a legújabb NGINX-csomagot az SSH-munkamenetből.

```bash
sudo apt-get -y update
sudo apt-get -y install nginx
```

Ha elkészült, az SSH-munkamenetből való kilépéshez írja be: `exit`.


## <a name="view-the-web-server-in-action"></a>A webkiszolgáló működésének ellenőrzése

Egy tetszőleges webböngészővel tekintse meg az alapértelmezett NGINX-kezdőlapot. Webcímként adja meg a virtuális gép nyilvános IP-címét. A nyilvános IP-cím a virtuális gép áttekintő oldalán található, vagy a korábban használt SSH-kapcsolati sztring részeként.

![NGINX alapértelmezett kezdőlapja](./media/quick-create-cli/nginix-welcome-page.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, használhatja a [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) parancsmagot az erőforráscsoport, a virtuális gép és az összes kapcsolódó erőforrás eltávolításához:

```azurepowershell-interactive
Remove-AzResourceGroup -Name "myResourceGroup"
```

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban üzembe helyezett egy egyszerű virtuális gépet, létrehozott egy hálózati biztonsági csoportot és szabályt, valamint telepített egy alapszintű webkiszolgálót. Ha bővebb információra van szüksége az Azure-beli virtuális gépekkel kapcsolatban, lépjen tovább a Linux rendszerű virtuális gépekről szóló oktatóanyagra.

> [!div class="nextstepaction"]
> [Azure-beli Linux rendszerű virtuális gépek – oktatóanyag](./tutorial-manage-vm.md)
