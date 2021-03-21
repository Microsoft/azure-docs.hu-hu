---
title: A hálózati hozzáférés korlátozása a Pásti-erőforrásokhoz – Azure PowerShell
description: Ebből a cikkből megtudhatja, hogyan korlátozhatja és korlátozhatja az Azure-erőforrásokhoz, például az Azure Storage-hoz és a Azure SQL Databasehoz való hálózati hozzáférést Azure PowerShell használatával.
services: virtual-network
documentationcenter: virtual-network
author: KumudD
manager: mtillman
editor: ''
tags: azure-resource-manager
Customer intent: I want only resources in a virtual network subnet to access an Azure PaaS resource, such as an Azure Storage account.
ms.assetid: ''
ms.service: virtual-network
ms.devlang: ''
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/14/2018
ms.author: kumud
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 6770486158b9c5f2e896951d91ff41643b6c8813
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98790140"
---
# <a name="restrict-network-access-to-paas-resources-with-virtual-network-service-endpoints-using-powershell"></a>A virtuális hálózati szolgáltatásbeli végpontokkal való hálózati hozzáférés korlátozása a PowerShell használatával

Virtuális hálózati szolgáltatásvégpontokkal egy adott virtuális hálózati alhálózatra korlátozható az egyes Azure-szolgáltatási erőforrásokhoz való hálózati hozzáférés. Emellett teljesen le is tiltható az internetes hozzáférés az erőforrásokhoz. A szolgáltatásvégpontok közvetlen csatlakozást biztosítanak a virtuális hálózat és a támogatott Azure-szolgáltatások között, így lehetővé teszik a virtuális hálózat magáncímterének használatát az Azure-szolgáltatások eléréséhez. A szolgáltatásvégpontokon keresztül az Azure-erőforrások felé irányuló forgalom mindig a Microsoft Azure gerinchálózatán marad. Ebben a cikkben az alábbiakkal ismerkedhet meg:

* Virtuális hálózat létrehozása egyetlen alhálózattal
* Alhálózat hozzáadása és szolgáltatásvégpont engedélyezése
* Azure-erőforrás létrehozása és hálózati hozzáférés engedélyezése az erőforráshoz egyetlen alhálózatról
* Virtuális gép (VM) üzembe helyezése az egyes alhálózatokon
* Erőforráshoz való alhálózati hozzáférés ellenőrzése
* Erőforráshoz való alhálózati és internetes hozzáférés letiltásának ellenőrzése

Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Ha a PowerShell helyi telepítését és használatát választja, akkor ehhez a cikkhez az Azure PowerShell-modul 1.0.0-es vagy újabb verziójára lesz szükség. A telepített verzió azonosításához futtassa a következőt: `Get-Module -ListAvailable Az`. Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-az-ps) ismertető cikket. Ha helyileg futtatja a PowerShellt, akkor emellett a `Connect-AzAccount` futtatásával kapcsolatot kell teremtenie az Azure-ral.

## <a name="create-a-virtual-network"></a>Virtuális hálózat létrehozása

A virtuális hálózat létrehozása előtt létre kell hoznia egy erőforráscsoportot a virtuális hálózathoz, és az ebben a cikkben létrehozott összes többi erőforrást. Hozzon létre egy erőforráscsoportot a [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). A következő példában létrehozunk egy *myResourceGroup* nevű erőforráscsoportot: 

```azurepowershell-interactive
New-AzResourceGroup -ResourceGroupName myResourceGroup -Location EastUS
```

Hozzon létre egy új virtuális hálózatot a [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork). A következő példában létrehozunk egy *myVirtualNetwork* nevű virtuális hálózatot a *10.0.0.0/16* előtaggal.

```azurepowershell-interactive
$virtualNetwork = New-AzVirtualNetwork `
  -ResourceGroupName myResourceGroup `
  -Location EastUS `
  -Name myVirtualNetwork `
  -AddressPrefix 10.0.0.0/16
```

Hozzon létre egy alhálózati konfigurációt a [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig). Az alábbi példa létrehoz egy alhálózati konfigurációt egy *nyilvános* alhálózathoz:

```azurepowershell-interactive
$subnetConfigPublic = Add-AzVirtualNetworkSubnetConfig `
  -Name Public `
  -AddressPrefix 10.0.0.0/24 `
  -VirtualNetwork $virtualNetwork
```

Hozza létre az alhálózatot a virtuális hálózatban úgy, hogy az alhálózat konfigurációját a [set-AzVirtualNetwork](/powershell/module/az.network/Set-azVirtualNetwork)használatával a virtuális hálózatra írja:

```azurepowershell-interactive
$virtualNetwork | Set-AzVirtualNetwork
```

## <a name="enable-a-service-endpoint"></a>Szolgáltatásvégpont engedélyezése

A szolgáltatási végpontokat csak olyan szolgáltatások esetében engedélyezheti, amelyek támogatják a szolgáltatási végpontokat. A [Get-AzVirtualNetworkAvailableEndpointService](/powershell/module/az.network/get-azvirtualnetworkavailableendpointservice)Azure-helyen elérhető szolgáltatás-végpontok számára engedélyezett szolgáltatások megtekintése. A következő példa a *eastus* régióban elérhető szolgáltatás-végpontok számára engedélyezett szolgáltatások listáját adja vissza. A visszaadott szolgáltatások listája idővel növekszik, ahogy az Azure-szolgáltatások egyre nagyobb számú szolgáltatási végpontot kapnak.

```azurepowershell-interactive
Get-AzVirtualNetworkAvailableEndpointService -Location eastus | Select Name
```

Hozzon létre egy további alhálózatot a virtuális hálózaton. Ebben a példában egy *privát* nevű alhálózat jön létre a *Microsoft. Storage* szolgáltatás-végponttal: 

```azurepowershell-interactive
$subnetConfigPrivate = Add-AzVirtualNetworkSubnetConfig `
  -Name Private `
  -AddressPrefix 10.0.1.0/24 `
  -VirtualNetwork $virtualNetwork `
  -ServiceEndpoint Microsoft.Storage

$virtualNetwork | Set-AzVirtualNetwork
```

## <a name="restrict-network-access-for-a-subnet"></a>Alhálózat hálózati hozzáférésének korlátozása

Hozzon létre egy hálózati biztonsági csoport biztonsági szabályait a [New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig). A következő szabály lehetővé teszi a kimenő hozzáférést az Azure Storage szolgáltatáshoz rendelt nyilvános IP-címekhez: 

```azurepowershell-interactive
$rule1 = New-AzNetworkSecurityRuleConfig `
  -Name Allow-Storage-All `
  -Access Allow `
  -DestinationAddressPrefix Storage `
  -DestinationPortRange * `
  -Direction Outbound `
  -Priority 100 `
  -Protocol * `
  -SourceAddressPrefix VirtualNetwork `
  -SourcePortRange *
```

A következő szabály tiltja az összes nyilvános IP-cím elérését. Az előző szabály felülbírálja ezt a szabályt a magasabb prioritás miatt, ami lehetővé teszi az Azure Storage nyilvános IP-címeinek elérését.

```azurepowershell-interactive
$rule2 = New-AzNetworkSecurityRuleConfig `
  -Name Deny-Internet-All `
  -Access Deny `
  -DestinationAddressPrefix Internet `
  -DestinationPortRange * `
  -Direction Outbound `
  -Priority 110 `
  -Protocol * `
  -SourceAddressPrefix VirtualNetwork `
  -SourcePortRange *
```

A következő szabály lehetővé teszi, hogy bárhonnan beérkező RDP protokoll (RDP) forgalmat az alhálózatba. A távoli asztali kapcsolatok engedélyezve vannak az alhálózatban, így egy későbbi lépésben ellenőrizhetik a hálózati hozzáférést egy adott erőforráshoz.

```azurepowershell-interactive
$rule3 = New-AzNetworkSecurityRuleConfig `
  -Name Allow-RDP-All `
  -Access Allow `
  -DestinationAddressPrefix VirtualNetwork `
  -DestinationPortRange 3389 `
  -Direction Inbound `
  -Priority 120 `
  -Protocol * `
  -SourceAddressPrefix * `
  -SourcePortRange *
```

Hozzon létre egy hálózati biztonsági csoportot a [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup). A következő példa egy *myNsgPrivate* nevű hálózati biztonsági csoportot hoz létre.

```azurepowershell-interactive
$nsg = New-AzNetworkSecurityGroup `
  -ResourceGroupName myResourceGroup `
  -Location EastUS `
  -Name myNsgPrivate `
  -SecurityRules $rule1,$rule2,$rule3
```

Társítsa a hálózati biztonsági csoportot a *privát* alhálózathoz a [set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/set-azvirtualnetworksubnetconfig) , majd írja be az alhálózat konfigurációját a virtuális hálózatra. A következő példa a *myNsgPrivate* hálózati biztonsági csoportot társítja a *privát* alhálózathoz:

```azurepowershell-interactive
Set-AzVirtualNetworkSubnetConfig `
  -VirtualNetwork $VirtualNetwork `
  -Name Private `
  -AddressPrefix 10.0.1.0/24 `
  -ServiceEndpoint Microsoft.Storage `
  -NetworkSecurityGroup $nsg

$virtualNetwork | Set-AzVirtualNetwork
```

## <a name="restrict-network-access-to-a-resource"></a>Erőforráshoz való hálózati hozzáférés korlátozása

A szolgáltatásvégpontok használatára képes Azure-szolgáltatásokkal létrehozott erőforrásokhoz való hálózati hozzáférés korlátozásának lépései szolgáltatásonként eltérőek. Az egyes szolgáltatásokhoz szükséges lépéseket az adott szolgáltatások dokumentációja tartalmazza. A cikk további része egy Azure Storage-fiók hálózati hozzáférésének korlátozására szolgáló lépéseket mutat be példaként.

### <a name="create-a-storage-account"></a>Tárfiók létrehozása

Hozzon létre egy Azure Storage [-fiókot a New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount). Cserélje le `<replace-with-your-unique-storage-account-name>` az karaktert az összes Azure-helyen található egyedi névre, amely 3-24 karakter hosszúságú, és csak számokat és kisbetűket használ.

```azurepowershell-interactive
$storageAcctName = '<replace-with-your-unique-storage-account-name>'

New-AzStorageAccount `
  -Location EastUS `
  -Name $storageAcctName `
  -ResourceGroupName myResourceGroup `
  -SkuName Standard_LRS `
  -Kind StorageV2
```

A Storage-fiók létrehozása után kérje le a Storage-fiók kulcsát a [Get-AzStorageAccountKey](/powershell/module/az.storage/get-azstorageaccountkey)változóval:

```azurepowershell-interactive
$storageAcctKey = (Get-AzStorageAccountKey `
  -ResourceGroupName myResourceGroup `
  -AccountName $storageAcctName).Value[0]
```

A kulcs használatával a fájlmegosztás egy későbbi lépésben hozható létre. Adja meg `$storageAcctKey` és jegyezze fel az értéket, mivel azt is be kell írnia egy későbbi lépésben, ha a fájlmegosztást egy virtuális gépen lévő meghajtóra rendeli.

### <a name="create-a-file-share-in-the-storage-account"></a>Fájlmegosztás létrehozása a tárfiókban

Hozzon létre egy környezetet a Storage-fiókjához és a kulcshoz a [New-AzStorageContext](/powershell/module/az.storage/new-AzStoragecontext). A környezet beágyazza a Storage-fiók nevét és a fiók kulcsát:

```azurepowershell-interactive
$storageContext = New-AzStorageContext $storageAcctName $storageAcctKey
```

Fájlmegosztás létrehozása a [New-AzStorageShare](/powershell/module/az.storage/new-azstorageshare):

$share = New-AzStorageShare My-file-share-Context $storageContext

### <a name="deny-all-network-access-to-a-storage-account"></a>Minden hálózati hozzáférés megtagadása egy Storage-fiókhoz

Alapértelmezés szerint a tárfiókok bármely hálózatban lévő ügyféltől érkező hálózati kapcsolatokat elfogadnak. Ha korlátozni szeretné a hozzáférést a kiválasztott hálózatokra, módosítsa az alapértelmezett műveletet az [Update-AzStorageAccountNetworkRuleSet](/powershell/module/az.storage/update-azstorageaccountnetworkruleset) *utasítás megtagadásához* . Ha a hálózati hozzáférés le van tiltva, a tárfiók egyetlen hálózatról sem érhető el.

```azurepowershell-interactive
Update-AzStorageAccountNetworkRuleSet  `
  -ResourceGroupName "myresourcegroup" `
  -Name $storageAcctName `
  -DefaultAction Deny
```

### <a name="enable-network-access-from-a-subnet"></a>Hálózati hozzáférés engedélyezése alhálózatról

A [Get-AzVirtualNetwork](/powershell/module/az.network/get-azvirtualnetwork) paranccsal kérje le a létrehozott virtuális hálózatot, majd kérje le a Private subnet objektumot egy olyan változóba, amelyben a [Get-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/get-azvirtualnetworksubnetconfig):

```azurepowershell-interactive
$privateSubnet = Get-AzVirtualNetwork `
  -ResourceGroupName "myResourceGroup" `
  -Name "myVirtualNetwork" `
  | Get-AzVirtualNetworkSubnetConfig `
  -Name "Private"
```

Engedélyezze a hálózati *hozzáférést a Storage* -fiókhoz az [Add-AzStorageAccountNetworkRule](/powershell/module/az.network/add-aznetworksecurityruleconfig)használatával.

```azurepowershell-interactive
Add-AzStorageAccountNetworkRule `
  -ResourceGroupName "myresourcegroup" `
  -Name $storageAcctName `
  -VirtualNetworkResourceId $privateSubnet.Id
```

## <a name="create-virtual-machines"></a>Virtuális gépek létrehozása

Tárfiókhoz való hálózati hozzáférés teszteléséhez helyezzen üzembe egy virtuális gépet minden alhálózaton.

### <a name="create-the-first-virtual-machine"></a>Az első virtuális gép létrehozása

Hozzon létre egy virtuális gépet a *nyilvános* alhálózatban a [New-AzVM](/powershell/module/az.compute/new-azvm). A következő parancs futtatásakor a rendszer a hitelesítő adatok megadását kéri. Az itt megadott értékek határozzák meg a virtuális géphez tartozó felhasználónevet és jelszót. Az `-AsJob` kapcsoló a háttérben hozza létre a virtuális gépet, így Ön eközben folytathatja a következő lépéssel.

```azurepowershell-interactive
New-AzVm `
    -ResourceGroupName "myResourceGroup" `
    -Location "East US" `
    -VirtualNetworkName "myVirtualNetwork" `
    -SubnetName "Public" `
    -Name "myVmPublic" `
    -AsJob
```

Az alábbi példához hasonló kimenetet ad vissza:

```powershell
Id     Name            PSJobTypeName   State         HasMoreData     Location             Command                  
--     ----            -------------   -----         -----------     --------             -------                  
1      Long Running... AzureLongRun... Running       True            localhost            New-AzVM     
```

### <a name="create-the-second-virtual-machine"></a>A második virtuális gép létrehozása

Hozzon létre egy virtuális gépet a *privát* alhálózatban:

```azurepowershell-interactive
New-AzVm `
    -ResourceGroupName "myResourceGroup" `
    -Location "East US" `
    -VirtualNetworkName "myVirtualNetwork" `
    -SubnetName "Private" `
    -Name "myVmPrivate"
```

A virtuális gép létrehozása néhány percet vesz igénybe az Azure számára. Ne folytassa a következő lépéssel, amíg az Azure befejezi a virtuális gép létrehozását, és a kimenetet visszaadja a PowerShellnek.

## <a name="confirm-access-to-storage-account"></a>Tárfiókhoz való hozzáférés ellenőrzése

A [Get-AzPublicIpAddress](/powershell/module/az.network/get-azpublicipaddress) használatával visszaállíthatja egy virtuális gép nyilvános IP-címét. A következő példa a *myVmPrivate* virtuális gép nyilvános IP-címét adja vissza:

```azurepowershell-interactive
Get-AzPublicIpAddress `
  -Name myVmPrivate `
  -ResourceGroupName myResourceGroup `
  | Select IpAddress
```

A következő parancsban cserélje le a `<publicIpAddress>` értékét az előző parancs által visszaadott nyilvános IP-címre, majd írja be a következő parancsot:

```powershell
mstsc /v:<publicIpAddress>
```

A rendszer létrehoz és letölt a számítógépre egy Remote Desktop Protocol (.rdp) fájlt. Nyissa meg a letöltött RDP-fájlt. Ha a rendszer kéri, válassza a **Csatlakozás** lehetőséget. Írja be a virtuális gép létrehozásakor megadott felhasználónevet és jelszót. Előfordulhat, hogy a virtuális gép létrehozásakor megadott hitelesítő adatok megadásához a **További lehetőségek**, majd a **Másik fiók használata** lehetőségre kell kattintania. Válassza az **OK** lehetőséget. A bejelentkezés során egy figyelmeztetés jelenhet meg a tanúsítvánnyal kapcsolatban. Ha figyelmeztetést kap, kattintson az **Igen** vagy a **Folytatás** gombra a csatlakozás folytatásához.

A *myVmPrivate* virtuális gépen a PowerShell-lel képezze le az Azure-fájlmegosztást a Z meghajtóra. Az alábbi parancsok futtatása előtt cserélje le a `<storage-account-key>` és `<storage-account-name>` a értéket a megadott értékekre, vagy olvassa be a következőt: [Storage-fiók létrehozása](#create-a-storage-account).

```powershell
$acctKey = ConvertTo-SecureString -String "<storage-account-key>" -AsPlainText -Force
$credential = New-Object System.Management.Automation.PSCredential -ArgumentList "Azure\<storage-account-name>", $acctKey
New-PSDrive -Name Z -PSProvider FileSystem -Root "\\<storage-account-name>.file.core.windows.net\my-file-share" -Credential $credential
```

A PowerShell a következő példához hasonló kimenetet ad vissza:

```powershell
Name           Used (GB)     Free (GB) Provider      Root
----           ---------     --------- --------      ----
Z                                      FileSystem    \\vnt.file.core.windows.net\my-f...
```

Az Azure-fájlmegosztás sikeresen le lett képezve a Z meghajtóra.

Győződjön meg arról, hogy a virtuális gép nem rendelkezik kimenő kapcsolattal más nyilvános IP-címekhez:

```powershell
ping bing.com
```

Nem kap választ, mert a *Magánjellegű* alhálózathoz rendelt hálózati biztonsági csoport nem engedélyezi a kimenő hozzáférést olyan nyilvános IP-címekhez, amelyek nem az Azure Storage szolgáltatáshoz rendelt címek.

Zárja be a *myVmPrivate* virtuális gépre irányuló távoli asztali munkamenetet.

## <a name="confirm-access-is-denied-to-storage-account"></a>Tárfiókhoz való hozzáférés letiltásának ellenőrzése

A *myVmPublic* virtuális gép nyilvános IP-címének beszerzése:

```azurepowershell-interactive
Get-AzPublicIpAddress `
  -Name myVmPublic `
  -ResourceGroupName myResourceGroup `
  | Select IpAddress
```

A következő parancsban cserélje le a `<publicIpAddress>` értékét az előző parancs által visszaadott nyilvános IP-címre, majd írja be a következő parancsot: 

```powershell
mstsc /v:<publicIpAddress>
```

A *myVmPublic* virtuális gépen próbálja meg leképezni az Azure-fájlmegosztást a Z meghajtóra. Az alábbi parancsok futtatása előtt cserélje le a `<storage-account-key>` és `<storage-account-name>` a értéket a megadott értékekre, vagy olvassa be a következőt: [Storage-fiók létrehozása](#create-a-storage-account).

```powershell
$acctKey = ConvertTo-SecureString -String "<storage-account-key>" -AsPlainText -Force
$credential = New-Object System.Management.Automation.PSCredential -ArgumentList "Azure\<storage-account-name>", $acctKey
New-PSDrive -Name Z -PSProvider FileSystem -Root "\\<storage-account-name>.file.core.windows.net\my-file-share" -Credential $credential
```

A megosztáshoz való hozzáférés megtagadva, és `New-PSDrive : Access is denied` hibaüzenetet kap. A hozzáférést a rendszer megtagadja, mert a *myVmPublic* virtuális gép a *Nyilvános* alhálózaton van üzembe helyezve. A *Nyilvános* alhálózat nem rendelkezik az Azure Storage-hoz engedélyezett szolgáltatásvégponttal, és a tárfiók kizárólag a *Magánjellegű* alhálózatról engedélyezi a hozzáférést, a *Nyilvános* alhálózatról nem.

Zárja be a távoli asztali munkamenetet a *myVmPublic* virtuális géppel.

A számítógépről próbálja meg megtekinteni a fájlmegosztást a Storage-fiókban a következő paranccsal:

```powershell-interactive
Get-AzStorageFile `
  -ShareName my-file-share `
  -Context $storageContext
```

A hozzáférés megtagadva, és *kap egy Get-AzStorageFile: a távoli kiszolgáló a következő hibát adta vissza: (403) tiltott. HTTP-állapotkód: 403-HTTP-hibaüzenet: Ez a kérelem nem jogosult a művelet hibájának elvégzésére* , mert a számítógép nem a *MyVirtualNetwork* virtuális hálózat *privát* alhálózatán található.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, a [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) használatával eltávolíthatja az erőforráscsoportot és a benne található összes erőforrást:

```azurepowershell-interactive 
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Következő lépések

Ebben a cikkben egy virtuális hálózati alhálózat szolgáltatási végpontját engedélyezte. Megismerte, hogy az Azure-szolgáltatásokkal üzembe helyezett erőforrásokhoz szolgáltatásvégpontok engedélyezhetők. Létrehozott egy Azure Storage-fiókot, és egy adott virtuális hálózati alhálózat erőforrásaira korlátozta a fiók felé irányuló hálózati hozzáférést. További információkat a szolgáltatásvégpontokról a [szolgáltatásvégpontok áttekintését](virtual-network-service-endpoints-overview.md) és az [alhálózatok kezelését](virtual-network-manage-subnet.md) ismertető cikkekben olvashat.

Ha több virtuális hálózat található a fiókjában, érdemes lehet összekapcsolni két virtuális hálózatot, hogy az egyes virtuális hálózatokban található erőforrások kommunikálhassanak egymással. További információt a [virtuális hálózatok összekapcsolásával](tutorial-connect-virtual-networks-powershell.md)foglalkozó témakörben talál.
