---
title: Az Azure Storage-ba irányuló kiszűrése korlátozása – Azure PowerShell
description: Ebből a cikkből megtudhatja, hogyan korlátozhatja és korlátozhatja a virtuális hálózati kiszűrése az Azure Storage-erőforrásokhoz a virtuális hálózati szolgáltatás végpont-házirendjeivel Azure PowerShell használatával.
services: virtual-network
documentationcenter: virtual-network
author: RDhillon
manager: narayan
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: ''
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/03/2020
ms.author: rdhillon
ms.custom: ''
ms.openlocfilehash: 3ff0ee79413640c09132b8399a3b0c07dd1783cc
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/31/2021
ms.locfileid: "106063695"
---
# <a name="manage-data-exfiltration-to-azure-storage-accounts-with-virtual-network-service-endpoint-policies-using-azure-powershell"></a>Az Azure Storage-fiókok kiszűrése való kezelése virtuális hálózati szolgáltatás végponti házirendjeivel Azure PowerShell használatával

A Virtual Network szolgáltatás végponti házirendjei lehetővé teszik, hogy az Azure Storage-fiókok hozzáférés-vezérlését a szolgáltatás-végpontokon keresztül egy virtuális hálózaton belül alkalmazza. Ez a legfontosabb feladat a számítási feladatok biztonságossá tétele, a Storage-fiókok kezelése, valamint az adatok kiszűrése engedélyezése.
Ebben a cikkben az alábbiakkal ismerkedhet meg:

* Hozzon létre egy virtuális hálózatot.
* Adjon hozzá egy alhálózatot, és engedélyezze a szolgáltatási végpontot az Azure Storage-hoz.
* Hozzon létre két Azure Storage-fiókot, és engedélyezze a hálózati hozzáférést a fent létrehozott alhálózatból.
* Hozzon létre egy szolgáltatás-végponti házirendet, amely lehetővé teszi, hogy csak az egyik Storage-fiókhoz férhessen hozzá.
* Helyezzen üzembe egy virtuális gépet (VM) az alhálózaton.
* Erősítse meg az engedélyezett Storage-fiókhoz való hozzáférést az alhálózaton.
* Győződjön meg arról, hogy a hozzáférés meg van tagadva a nem engedélyezett Storage-fiókhoz az alhálózaton.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Ha a PowerShell helyi telepítését és használatát választja, akkor ehhez a cikkhez az Azure PowerShell-modul 1.0.0-es vagy újabb verziójára lesz szükség. A telepített verzió azonosításához futtassa a következőt: `Get-Module -ListAvailable Az`. Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-az-ps) ismertető cikket. Ha helyileg futtatja a PowerShellt, akkor emellett a `Connect-AzAccount` futtatásával kapcsolatot kell teremtenie az Azure-ral.

## <a name="create-a-virtual-network"></a>Virtuális hálózat létrehozása

A virtuális hálózat létrehozása előtt létre kell hoznia egy erőforráscsoportot a virtuális hálózathoz, és az ebben a cikkben létrehozott összes többi erőforrást. Hozzon létre egy erőforráscsoportot a [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). A következő példában létrehozunk egy *myResourceGroup* nevű erőforráscsoportot: 

```azurepowershell-interactive
New-AzResourceGroup `
  -ResourceGroupName myResourceGroup `
  -Location EastUS
```

Hozzon létre egy új virtuális hálózatot a [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork). A következő példában létrehozunk egy *myVirtualNetwork* nevű virtuális hálózatot a *10.0.0.0/16* előtaggal.

```azurepowershell-interactive
$virtualNetwork = New-AzVirtualNetwork `
  -ResourceGroupName myResourceGroup `
  -Location EastUS `
  -Name myVirtualNetwork `
  -AddressPrefix 10.0.0.0/16
```

## <a name="enable-a-service-endpoint"></a>Szolgáltatásvégpont engedélyezése

Hozzon létre egy alhálózatot a virtuális hálózaton. Ebben a példában egy *privát* nevű alhálózat jön létre a *Microsoft. Storage* szolgáltatás-végponttal: 

```azurepowershell-interactive
$subnetConfigPrivate = Add-AzVirtualNetworkSubnetConfig `
  -Name Private `
  -AddressPrefix 10.0.0.0/24 `
  -VirtualNetwork $virtualNetwork `
  -ServiceEndpoint Microsoft.Storage

$virtualNetwork | Set-AzVirtualNetwork
```

## <a name="restrict-network-access-for-the-subnet"></a>Az alhálózat hálózati hozzáférésének korlátozása

Hozzon létre egy hálózati biztonsági csoport biztonsági szabályait a [New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig). A következő szabály lehetővé teszi a kimenő hozzáférést az Azure Storage szolgáltatáshoz rendelt nyilvános IP-címekhez: 

```azurepowershell-interactive
$rule1 = New-AzNetworkSecurityRuleConfig `
  -Name Allow-Storage-All `
  -Access Allow `
  -DestinationAddressPrefix Storage `
  -DestinationPortRange * `
  -Direction Outbound `
  -Priority 100 -Protocol * `
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
  -Priority 110 -Protocol * `
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
  -AddressPrefix 10.0.0.0/24 `
  -ServiceEndpoint Microsoft.Storage `
  -NetworkSecurityGroup $nsg

$virtualNetwork | Set-AzVirtualNetwork
```

## <a name="restrict-network-access-to-azure-storage-accounts"></a>Az Azure Storage-fiókokhoz való hálózati hozzáférés korlátozása

A szolgáltatásvégpontok használatára képes Azure-szolgáltatásokkal létrehozott erőforrásokhoz való hálózati hozzáférés korlátozásának lépései szolgáltatásonként eltérőek. Az egyes szolgáltatásokhoz szükséges lépéseket az adott szolgáltatások dokumentációja tartalmazza. A cikk további része egy Azure Storage-fiók hálózati hozzáférésének korlátozására szolgáló lépéseket mutat be példaként.

### <a name="create-two-storage-accounts"></a>Két Storage-fiók létrehozása

Hozzon létre egy Azure Storage [-fiókot a New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount).

```azurepowershell-interactive
$storageAcctName1 = 'allowedaccount'

New-AzStorageAccount `
  -Location EastUS `
  -Name $storageAcctName1 `
  -ResourceGroupName myResourceGroup `
  -SkuName Standard_LRS `
  -Kind StorageV2
```

A Storage-fiók létrehozása után kérje le a Storage-fiók kulcsát a [Get-AzStorageAccountKey](/powershell/module/az.storage/get-azstorageaccountkey)változóval:

```azurepowershell-interactive
$storageAcctKey1 = (Get-AzStorageAccountKey -ResourceGroupName myResourceGroup -AccountName $storageAcctName1).Value[0]
```

A kulcs használatával a fájlmegosztás egy későbbi lépésben hozható létre. Adja meg `$storageAcctKey` és jegyezze fel az értéket, mivel azt is be kell írnia egy későbbi lépésben, ha a fájlmegosztást egy virtuális gépen lévő meghajtóra rendeli.

Most ismételje meg a fenti lépéseket egy második Storage-fiók létrehozásához.

```azurepowershell-interactive
$storageAcctName2 = 'notallowedaccount'

New-AzStorageAccount `
  -Location EastUS `
  -Name $storageAcctName2 `
  -ResourceGroupName myResourceGroup `
  -SkuName Standard_LRS `
  -Kind StorageV2
```

Kérje le a Storage-fiók kulcsát ebből a fiókból a fájlmegosztás létrehozásához a későbbiekben.

```azurepowershell-interactive
$storageAcctKey2 = (Get-AzStorageAccountKey -ResourceGroupName myResourceGroup -AccountName $storageAcctName2).Value[0]
```

### <a name="create-a-file-share-in-each-of-the-storage-account"></a>Fájlmegosztás létrehozása mindegyik Storage-fiókban

Hozzon létre egy környezetet a Storage-fiókjához és a kulcshoz a [New-AzStorageContext](/powershell/module/az.storage/new-AzStoragecontext). A környezet beágyazza a Storage-fiók nevét és a fiók kulcsát:

```azurepowershell-interactive
$storageContext1 = New-AzStorageContext $storageAcctName1 $storageAcctKey1

$storageContext2 = New-AzStorageContext $storageAcctName2 $storageAcctKey2
```

Fájlmegosztás létrehozása a [New-AzStorageShare](/powershell/module/az.storage/new-azstorageshare):

```azurepowershell-interactive
$share1 = New-AzStorageShare my-file-share -Context $storageContext1

$share2 = New-AzStorageShare my-file-share -Context $storageContext2
```

### <a name="deny-all-network-access-to-a-storage-accounts"></a>Minden hálózati hozzáférés megtagadása egy Storage-fiókhoz

Alapértelmezés szerint a tárfiókok bármely hálózatban lévő ügyféltől érkező hálózati kapcsolatokat elfogadnak. Ha korlátozni szeretné a hozzáférést a kiválasztott hálózatokra, módosítsa az alapértelmezett műveletet az [Update-AzStorageAccountNetworkRuleSet](/powershell/module/az.storage/update-azstorageaccountnetworkruleset) *utasítás megtagadásához* . Ha a hálózati hozzáférés le van tiltva, a tárfiók egyetlen hálózatról sem érhető el.

```azurepowershell-interactive
Update-AzStorageAccountNetworkRuleSet `
  -ResourceGroupName myresourcegroup `
  -Name $storageAcctName1 `
  -DefaultAction Deny

Update-AzStorageAccountNetworkRuleSet  `
  -ResourceGroupName myresourcegroup `
  -Name $storageAcctName2 `
  -DefaultAction Deny
```

### <a name="enable-network-access-only-from-the-vnet-subnet"></a>Hálózati hozzáférés engedélyezése csak a VNet alhálózatból

A [Get-AzVirtualNetwork](/powershell/module/az.network/get-azvirtualnetwork) paranccsal kérje le a létrehozott virtuális hálózatot, majd kérje le a Private subnet objektumot egy olyan változóba, amelyben a [Get-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/get-azvirtualnetworksubnetconfig):

```azurepowershell-interactive
$privateSubnet = Get-AzVirtualNetwork `
  -ResourceGroupName myResourceGroup `
  -Name myVirtualNetwork `
  | Get-AzVirtualNetworkSubnetConfig -Name Private
```

Engedélyezze a hálózati *hozzáférést a Storage* -fiókhoz az [Add-AzStorageAccountNetworkRule](/powershell/module/az.network/add-aznetworksecurityruleconfig)használatával.

```azurepowershell-interactive
Add-AzStorageAccountNetworkRule `
  -ResourceGroupName myresourcegroup `
  -Name $storageAcctName1 `
  -VirtualNetworkResourceId $privateSubnet.Id

Add-AzStorageAccountNetworkRule `
  -ResourceGroupName myresourcegroup `
  -Name $storageAcctName2 `
  -VirtualNetworkResourceId $privateSubnet.Id
```

## <a name="apply-policy-to-allow-access-to-valid-storage-account"></a>Házirend alkalmazása az érvényes Storage-fiókhoz való hozzáférés engedélyezéséhez

Annak biztosítása érdekében, hogy a virtuális hálózatban lévő felhasználók csak a biztonságos és engedélyezett Azure Storage-fiókokhoz férhessenek hozzá, létrehozhat egy szolgáltatás-végponti házirendet a definícióban engedélyezett Storage-fiókok listájával. A rendszer ezután alkalmazza ezt a házirendet arra a virtuális hálózati alhálózatra, amely a tárolási végpontokon keresztül csatlakozik a tárolóhoz.

### <a name="create-a-service-endpoint-policy"></a>Szolgáltatás-végponti szabályzat létrehozása

Ez a szakasz a házirend-definíciót a szolgáltatás végpontján elérhető engedélyezett erőforrások listájával hozza létre.

Az első (engedélyezett) Storage-fiók erőforrás-AZONOSÍTÓjának beolvasása 

```azurepowershell-interactive
$resourceId = (Get-AzStorageAccount -ResourceGroupName myresourcegroup -Name $storageAcctName1).id
```

A házirend-definíció létrehozása a fenti erőforrás engedélyezéséhez

```azurepowershell-interactive
$policyDefinition = New-AzServiceEndpointPolicyDefinition -Name mypolicydefinition `
  -Description "Service Endpoint Policy Definition" `
  -Service "Microsoft.Storage" `
  -ServiceResource $resourceId
```

A szolgáltatás-végponti házirend létrehozása a fent létrehozott szabályzat-definíció használatával

```azurepowershell-interactive
$sepolicy = New-AzServiceEndpointPolicy -ResourceGroupName myresourcegroup `
  -Name mysepolicy -Location EastUS
  -ServiceEndpointPolicyDefinition $policyDefinition
```

### <a name="associate-the-service-endpoint-policy-to-the-virtual-network-subnet"></a>A szolgáltatás-végponti házirend hozzárendelése a virtuális hálózati alhálózathoz

A szolgáltatás-végponti házirend létrehozása után társítsa azt a célként megadott alhálózathoz az Azure Storage szolgáltatáshoz tartozó végpont-konfigurációval.

```azurepowershell-interactive
Set-AzVirtualNetworkSubnetConfig -VirtualNetwork $VirtualNetwork `
  -Name Private `
  -AddressPrefix 10.0.0.0/24 `
  -NetworkSecurityGroup $nsg `
  -ServiceEndpoint Microsoft.Storage `
  -ServiceEndpointPolicy $sepolicy

$virtualNetwork | Set-AzVirtualNetwork
```
## <a name="validate-access-restriction-to-azure-storage-accounts"></a>Az Azure Storage-fiókokhoz való hozzáférés korlátozásának ellenőrzése

### <a name="deploy-the-virtual-machine"></a>A virtuális gép üzembe helyezése

Egy Storage-fiók hálózati hozzáférésének teszteléséhez helyezzen üzembe egy virtuális gépet az alhálózaton.

Hozzon létre egy virtuális gépet a *privát* alhálózatban a [New-AzVM](/powershell/module/az.compute/new-azvm). A következő parancs futtatásakor a rendszer a hitelesítő adatok megadását kéri. Az itt megadott értékek határozzák meg a virtuális géphez tartozó felhasználónevet és jelszót. Az `-AsJob` kapcsoló a háttérben hozza létre a virtuális gépet, így Ön eközben folytathatja a következő lépéssel.

```azurepowershell-interactive
New-AzVm -ResourceGroupName myresourcegroup `
  -Location "East US" `
  -VirtualNetworkName myVirtualNetwork `
  -SubnetName Private `
  -Name "myVMPrivate" -AsJob
```

Az alábbi példához hasonló kimenetet ad vissza:

```powershell
Id     Name            PSJobTypeName   State         HasMoreData     Location             Command
--     ----            -------------   -----         -----------     --------             -------
1      Long Running... AzureLongRun... Running       True            localhost            New-AzVM
```

### <a name="confirm-access-to-the-allowed-storage-account"></a>Az *engedélyezett* Storage-fiókhoz való hozzáférés megerősítése

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

A *myVmPrivate* virtuális gépen képezze le az Azure-fájlmegosztást az engedélyezett Storage-fiókból a Z meghajtóra a PowerShell használatával. 

```powershell
$acctKey = ConvertTo-SecureString -String $storageAcctKey1 -AsPlainText -Force
$credential = New-Object System.Management.Automation.PSCredential -ArgumentList ("Azure\allowedaccount"), $acctKey
New-PSDrive -Name Z -PSProvider FileSystem -Root "\\allowedaccount.file.core.windows.net\my-file-share" -Credential $credential
```

A PowerShell a következő példához hasonló kimenetet ad vissza:

```powershell
Name           Used (GB)     Free (GB) Provider      Root
----           ---------     --------- --------      ----
Z                                      FileSystem    \\allowedaccount.file.core.windows.net\my-f...
```

Az Azure-fájlmegosztás sikeresen le lett képezve a Z meghajtóra.

Zárja be a *myVmPrivate* virtuális gépre irányuló távoli asztali munkamenetet.

### <a name="confirm-access-is-denied-to-non-allowed-storage-account"></a>A hozzáférés megerősítése megtagadva a *nem engedélyezett Storage-* fiókhoz

Ugyanazon a *myVmPrivate* virtuális gépen próbálja meg leképezni az Azure-fájlmegosztást az X meghajtóra. 

```powershell
$acctKey = ConvertTo-SecureString -String $storageAcctKey1 -AsPlainText -Force
$credential = New-Object System.Management.Automation.PSCredential -ArgumentList "Azure\notallowedaccount", $acctKey
New-PSDrive -Name X -PSProvider FileSystem -Root "\\notallowedaccount.file.core.windows.net\my-file-share" -Credential $credential
```

A megosztáshoz való hozzáférés megtagadva, és `New-PSDrive : Access is denied` hibaüzenetet kap. A hozzáférés megtagadva, mert a *notallowedaccount* nem szerepel a szolgáltatási végpont házirend engedélyezési erőforrások listájában. 

Zárja be a távoli asztali munkamenetet a *myVmPublic* virtuális géppel.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, a [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) használatával eltávolíthatja az erőforráscsoportot és a benne található összes erőforrást:

```azurepowershell-interactive 
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Következő lépések

Ebben a cikkben egy szolgáltatás-végponti házirendet alkalmazott egy Azure Virtual Network szolgáltatási végponton az Azure Storage-ba. Létrehozta az Azure Storage-fiókokat és a korlátozott hálózati hozzáférést csak bizonyos Storage-fiókokhoz (és így másokat is megtagadott) egy virtuális hálózat alhálózatáról. A szolgáltatás-végponti házirendekkel kapcsolatos további tudnivalókért lásd: [szolgáltatás-végponti házirendek áttekintése](virtual-network-service-endpoint-policies-overview.md).
