---
title: 'Rövid útmutató: Virtuális gép hálózati forgalmi szűrővel való problémájának diagnosztizálása – Azure PowerShell'
titleSuffix: Azure Network Watcher
description: Megtudhatja, hogyan diagnosztizálhatja a virtuális gépek Azure PowerShell forgalomszűrési problémáját az Azure-beli virtuális gépek IP-forgalom-ellenőrző funkcióját Network Watcher.
services: network-watcher
documentationcenter: network-watcher
author: damendo
ms.author: damendo
editor: ''
ms.date: 01/07/2021
ms.assetid: ''
ms.topic: quickstart
ms.service: network-watcher
ms.workload: infrastructure
ms.tgt_pltfrm: network-watcher
ms.devlang: na
tags:
- azure-resource-manager
ms.custom:
- mvc
- devx-track-azurepowershell
- mode-api
ms.openlocfilehash: fafe090d72d53b33ddb3e9863b7e62deba1055c5
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107535764"
---
# <a name="quickstart-diagnose-a-virtual-machine-network-traffic-filter-problem---azure-powershell"></a>Rövid útmutató: Virtuális gép hálózati forgalmi szűrőhibájának diagnosztizálása – Azure PowerShell

Ennek a rövid útmutatónak a követésével egy virtuális gépet fog üzembe helyezni, majd ellenőriz egy IP-címre és URL-címre irányuló és egy IP-címről érkező kommunikációt. Meghatározza a kommunikációs hiba okát és feloldásának módját.

Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Ha a PowerShell helyi telepítése és használata között dönt, ehhez a rövid útmutatóhoz szükség lesz a Azure PowerShell `Az` modulra. A telepített verzió azonosításához futtassa a következőt: `Get-Module -ListAvailable Az`. Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-Az-ps) ismertető cikket. Ha helyileg futtatja a PowerShellt, akkor emellett a `Connect-AzAccount` futtatásával kapcsolatot kell teremtenie az Azure-ral.



## <a name="create-a-vm"></a>Virtuális gép létrehozása

Mielőtt virtuális gépet hozhatna létre, létre kell hoznia egy erőforráscsoportot, amely majd tartalmazza a virtuális gépet. Hozzon létre egy erőforráscsoportot a [New-AzResourceGroup segítségével.](/powershell/module/az.Resources/New-azResourceGroup) A következő példában létrehozunk egy *myResourceGroup* nevű erőforráscsoportot az *eastus* helyen.

```azurepowershell-interactive
New-AzResourceGroup -Name myResourceGroup -Location EastUS
```

Hozza létre a virtuális gépet a [New-AzVM segítségével.](/powershell/module/az.compute/new-azvm) Ennek a lépésnek a futtatásakor a rendszer a hitelesítő adatok megadását kéri. Az itt megadott értékek határozzák meg a virtuális géphez tartozó felhasználónevet és jelszót.

```azurepowershell-interactive
$vM = New-AzVm `
    -ResourceGroupName "myResourceGroup" `
    -Name "myVm" `
    -Location "East US"
```

A virtuális gép üzembe helyezése néhány percet vesz igénybe. Ne folytassa a további lépések megtételét, amíg létre nem jön a virtuális gép, és a PowerShell vissza nem adja a kimenetet.

## <a name="test-network-communication"></a>Hálózati kommunikáció tesztelése

A hálózati kommunikáció Network Watcherrel való teszteléséhez először engedélyeznie kell azt abban a régióban, ahol a tesztelni kívánt virtuális gép van, majd használja a kommunikáció tesztelésére a Network Watcher IP-folyamat ellenőrzése funkcióját.

### <a name="enable-network-watcher"></a>A Network Watcher engedélyezése

Ha már engedélyezve van egy Network Watcher az USA keleti régiójában, a [Get-AzNetworkWatcher](/powershell/module/az.network/get-aznetworkwatcher) használatával lekéri a network watchert. A következő példa egy meglévő, *NetworkWatcher_eastus* nevű hálózati figyelőt kér le, amely a *NetworkWatcherRG* erőforráscsoportban található:

```azurepowershell-interactive
$networkWatcher = Get-AzNetworkWatcher `
  -Name NetworkWatcher_eastus `
  -ResourceGroupName NetworkWatcherRG
```

Ha még nincs hálózati figyelő engedélyezve az USA keleti régiójában, a [New-AzNetworkWatcher](/powershell/module/az.network/new-aznetworkwatcher) használatával hozzon létre egy hálózati figyelőt az USA keleti régiójában:

```azurepowershell-interactive
$networkWatcher = New-AzNetworkWatcher `
  -Name "NetworkWatcher_eastus" `
  -ResourceGroupName "NetworkWatcherRG" `
  -Location "East US"
```

### <a name="use-ip-flow-verify"></a>IP-folyamat ellenőrzésének használata

Amikor létrehoz egy virtuális gépet, az Azure az alapértelmezésnek megfelelően engedélyezi és tiltja le a virtuális gépre irányuló és onnan érkező forgalmat. Később felülbírálhatja az Azure alapértelmezett beállításait, és további forgalomtípusokat engedélyezhet vagy tilthat le. A [Test-AzNetworkWatcherIPFlow](/powershell/module/az.network/test-aznetworkwatcheripflow) paranccsal tesztelje, hogy engedélyezve van-e vagy megtagadva a forgalom a különböző célhelyre és egy forrás IP-címről.

Tesztelje a virtuális gép kimenő kommunikációját a www.bing.com IP-címeinek egyikén:

```azurepowershell-interactive
Test-AzNetworkWatcherIPFlow `
  -NetworkWatcher $networkWatcher `
  -TargetVirtualMachineId $vM.Id `
  -Direction Outbound `
  -Protocol TCP `
  -LocalIPAddress 192.168.1.4 `
  -LocalPort 60000 `
  -RemoteIPAddress 13.107.21.200 `
  -RemotePort 80
```

Néhány másodperc múlva a visszaadott eredmény tájékoztatja, hogy a hozzáférést egy **AllowInternetOutbound** nevű biztonsági szabály engedélyezte.

Tesztelje a virtuális gép kimenő kommunikációját a 172.31.0.100 címen:

```azurepowershell-interactive
Test-AzNetworkWatcherIPFlow `
  -NetworkWatcher $networkWatcher `
  -TargetVirtualMachineId $vM.Id `
  -Direction Outbound `
  -Protocol TCP `
  -LocalIPAddress 192.168.1.4 `
  -LocalPort 60000 `
  -RemoteIPAddress 172.31.0.100 `
  -RemotePort 80
```

A visszaadott eredmény tájékoztatja, hogy a hozzáférést egy **DefaultOutboundDenyAll** nevű biztonsági szabály megtagadta.

Tesztelje a 172.31.0.100 címről a virtuális gépre bejövő kommunikációt:

```azurepowershell-interactive
Test-AzNetworkWatcherIPFlow `
  -NetworkWatcher $networkWatcher `
  -TargetVirtualMachineId $vM.Id `
  -Direction Inbound `
  -Protocol TCP `
  -LocalIPAddress 192.168.1.4 `
  -LocalPort 80 `
  -RemoteIPAddress 172.31.0.100 `
  -RemotePort 60000
```

A visszaadott eredmény tájékoztatja, hogy a hozzáférés egy **DefaultInboundDenyAll** nevű biztonsági szabály miatt meg lett tagadva. Most, hogy tudja, hogy mely biztonsági szabályok engedélyezik vagy tagadják meg a virtuális gépre érkező vagy onnan induló forgalmat, meghatározhatja a problémák megoldásának módját.

## <a name="view-details-of-a-security-rule"></a>Biztonsági szabály részleteinek megtekintése

Annak megállapításához, [](#test-network-communication) hogy a Hálózati kommunikáció tesztelése szabálya miért engedélyezi vagy akadályozza meg a kommunikációt, tekintse át a hálózati adapterre vonatkozó hatályos biztonsági szabályokat a [Get-AzEffectiveNetworkSecurityGroup használatával:](/powershell/module/az.network/get-azeffectivenetworksecuritygroup)

```azurepowershell-interactive
Get-AzEffectiveNetworkSecurityGroup `
  -NetworkInterfaceName myVm `
  -ResourceGroupName myResourceGroup
```

A visszaadott kimenet tartalmazza a következő szöveget az **AllowInternetOutbound** szabályhoz, amely engedélyezte a www.bing.com címre kimenő forgalmat az [IP-folyamat ellenőrzésének használata](#use-ip-flow-verify) lépésben:

```powershell
{
  "Name":
"defaultSecurityRules/AllowInternetOutBound",
  "Protocol": "All",
  "SourcePortRange": [
    "0-65535"
  ],
  "DestinationPortRange": [
    "0-65535"
  ],
  "SourceAddressPrefix": [
    "0.0.0.0/0"
  ],
  "DestinationAddressPrefix": [
    "Internet"
  ],
  "ExpandedSourceAddressPrefix": [],
  "ExpandedDestinationAddressPrefix": [
    "1.0.0.0/8",
    "2.0.0.0/7",
    "4.0.0.0/6",
    "8.0.0.0/7",
    "11.0.0.0/8",
    "12.0.0.0/6",
    ...
    ],
    "Access": "Allow",
    "Priority": 65001,
    "Direction": "Outbound"
  },
```

A kimenetben látható, hogy a **DestinationAddressPrefix** értéke **Internet**. Nem egyértelmű azonban, hogy az [IP-folyamat ellenőrzésének használata](#use-ip-flow-verify), lépésen tesztelt 13.107.21.200 cím hogyan viszonyul az **Internet** eredményhez. Több címelőtagot is láthat az **ExpandedDestinationAddressPrefix alatt.** A listában lévő előtagok egyike a **12.0.0.0/6**, amely magában foglalja a 12.0.0.1–15.255.255.254 tartományba tartozó IP-címeket. Mivel a 13.107.21.200 ebbe a címtartományba esik, az **AllowInternetOutBound** szabály engedélyezi a kimenő forgalmat. Emellett nincsenek magasabb **prioritású** (alacsonyabb számú) szabályok listázva a `Get-AzEffectiveNetworkSecurityGroup` által visszaadott kimenetben, amelyek felülbírálnák ezt a szabályt. Ha szeretné letiltani a 13.107.21.200 címre kimenő kommunikációt, akkor felvehet egy magasabb prioritású biztonsági szabályt, amely letiltja az IP-címre kimenő forgalmat a 80-as porton.

Amikor futtatta az `Test-AzNetworkWatcherIPFlow` parancsot, hogy tesztelje a 172.131.0.100 címre kimenő kommunikációt az [IP-folyamat ellenőrzésének használata](#use-ip-flow-verify) lépésben, a kimenetből megtudta, hogy a **DefaultOutboundDenyAll** szabály megtagadta a kommunikációt. A **DefaultOutboundDenyAll** szabály megfelel a **DenyAllOutBound** szabálynak, amely az `Get-AzEffectiveNetworkSecurityGroup` parancs következő kimenetében szerepel:

```powershell
{
"Name": "defaultSecurityRules/DenyAllOutBound",
"Protocol": "All",
"SourcePortRange": [
  "0-65535"
],
"DestinationPortRange": [
  "0-65535"
],
"SourceAddressPrefix": [
  "0.0.0.0/0"
],
"DestinationAddressPrefix": [
  "0.0.0.0/0"
],
"ExpandedSourceAddressPrefix": [],
"ExpandedDestinationAddressPrefix": [],
"Access": "Deny",
"Priority": 65500,
"Direction": "Outbound"
}
```

A szabály a **0.0.0.0/0** előtagot listázza **DestinationAddressPrefix értékként.** A szabály megtagadja a 172.131.0.100 címre kimenő kommunikációt, mert a cím nem található a parancs kimenetében található többi kimenő szabály **DestinationAddressPrefix** előtagja `Get-AzEffectiveNetworkSecurityGroup` között. Ha szeretné engedélyezni a kimenő kommunikációt, akkor felvehet egy magasabb prioritású biztonsági szabályt, amely engedélyezi az 172.131.0.100 IP-címre kimenő forgalmat a 80-as porton.

Amikor futtatta a `Test-AzNetworkWatcherIPFlow` parancsot, hogy tesztelje a 172.131.0.100 címről bejövő kommunikációt az [IP-folyamat ellenőrzésének használata](#use-ip-flow-verify) lépésben, a kimenetből megtudta, hogy a **DefaultInboundDenyAll** szabály megtagadta a kommunikációt. A **DefaultOutboundDenyAll** szabály megfelel a **DenyAllInBound** szabálynak, amely az `Get-AzEffectiveNetworkSecurityGroup` parancs következő kimenetében szerepel:

```powershell
{
"Name": "defaultSecurityRules/DenyAllInBound",
"Protocol": "All",
"SourcePortRange": [
  "0-65535"
],
"DestinationPortRange": [
  "0-65535"
],
"SourceAddressPrefix": [
  "0.0.0.0/0"
],
"DestinationAddressPrefix": [
  "0.0.0.0/0"
],
"ExpandedSourceAddressPrefix": [],
"ExpandedDestinationAddressPrefix": [],
"Access": "Deny",
"Priority": 65500,
"Direction": "Inbound"
},
```

A rendszer a **DenyAllInBound** szabályt alkalmazta, mert, amint az a kimenetben látható, nincs másik magasabb prioritású szabály az `Get-AzEffectiveNetworkSecurityGroup` parancs kimenetében, amely engedélyezné a virtuális gépre a 172.131.0.100 címről a 80-as porton át bejövő forgalmat. Ha szeretné engedélyezni a bejövő kommunikációt, akkor felvehet egy magasabb prioritású biztonsági szabályt, amely engedélyezi az 172.131.0.100 címről bejövő forgalmat a 80-as porton.

Az ebben a rövid útmutatóban található ellenőrzések az Azure-konfigurációt tesztelték. Ha az ellenőrzések a várt eredményt adták vissza, és továbbra is hálózati problémákat tapasztal, ellenőrizze, hogy nincs-e tűzfal a virtuális gép és a között a végpont között, amelyikkel kommunikál, és hogy a virtuális gép operációs rendszerének tűzfala nem engedélyezi-e vagy tagadja meg a kommunikációt.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, a [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) használatával eltávolíthatja az erőforráscsoportot és az összes benne található erőforrást:

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban létrehozott egy virtuális gépet, és diagnosztizálta a bejövő és kimenő hálózati forgalom szűrőit. Megtudta, hogy hálózati biztonsági csoportszabályok engedélyezik vagy tiltják le a virtuális gépekről érkező vagy oda irányuló adatforgalmat. További információ a [biztonsági szabályokról](../virtual-network/network-security-groups-overview.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json) és a [biztonsági szabályok létrehozásának](../virtual-network/manage-network-security-group.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json#create-a-security-rule) módjáról.

Még ha megfelelő hálózati adatforgalmi szűrők vannak is a helyükön, a virtuális géppel folytatott kommunikáció meghiúsulhat az útválasztás konfigurációja miatt. Ha szeretné elsajátítani a virtuális gépek hálózati útválasztási problémáinak diagnosztizálási módját, olvassa el [A virtuális gép útválasztási problémáinak diagnosztizálása](diagnose-vm-network-routing-problem-powershell.md) fejezetet, vagy ha a kimenő útválasztási, késési és forgalomszűrés problémák diagnosztizálását egyetlen eszközzel szeretné elvégezni, olvassa el a [Kapcsolatok hibaelhárítása](network-watcher-connectivity-powershell.md) fejezetet.
