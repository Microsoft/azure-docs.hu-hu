---
title: 'Rövid útmutató: Virtuális gép hálózati forgalmi szűrővel való problémájának diagnosztizálása – Azure CLI'
titleSuffix: Azure Network Watcher
description: Megtudhatja, hogyan diagnosztizálhatja a virtuális gépek hálózati forgalomszűrési problémáját az Azure CLI használatával az Azure Network Watcher.
services: network-watcher
documentationcenter: network-watcher
author: KumudD
manager: twooley
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: network-watcher
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: network-watcher
ms.workload: infrastructure
ms.date: 01/07/2021
ms.author: kumud
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 701df4353e8d2e36baf0496bd6944c4a95395414
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107763261"
---
# <a name="quickstart-diagnose-a-virtual-machine-network-traffic-filter-problem---azure-cli"></a>Rövid útmutató: Virtuális gép hálózati forgalmi szűrőhibájának diagnosztizálása – Azure CLI

Ennek a rövid útmutatónak a követésével egy virtuális gépet fog üzembe helyezni, majd ellenőriz egy IP-címre és URL-címre irányuló és egy IP-címről érkező kommunikációt. Meghatározza a kommunikációs hiba okát és feloldásának módját.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Ehhez a rövid útmutatóhoz az Azure CLI 2.0-s vagy újabb verziójára van szükség. Ha a Azure Cloud Shell, a legújabb verzió már telepítve van. 

- Az ebben a rövid útmutatóban az Azure CLI-parancsok Bash-rendszerhéjban való futtatásra vannak formázva.

## <a name="create-a-vm"></a>Virtuális gép létrehozása

Mielőtt virtuális gépet hozhatna létre, létre kell hoznia egy erőforráscsoportot, amely majd tartalmazza a virtuális gépet. Hozzon létre egy erőforráscsoportot az [az group create](/cli/azure/group) paranccsal. A következő példában létrehozunk egy *myResourceGroup* nevű erőforráscsoportot az *eastus* helyen:

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Hozzon létre egy virtuális gépet az [az vm create](/cli/azure/vm) paranccsal. Ha az SSH-kulcsok még nem léteznek a kulcsok alapértelmezett helyén, a parancs létrehozza őket. Ha konkrét kulcsokat szeretné használni, használja az `--ssh-key-value` beállítást. Az alábbi példa egy *myVm* nevű virtuális gépet hoz létre:

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVm \
  --image UbuntuLTS \
  --generate-ssh-keys
```

A virtuális gép üzembe helyezése néhány percet vesz igénybe. Ne folytassa a további lépésekkel, amíg létre nem jön a virtuális gép, és az Azure CLI vissza nem adja a kimenetet.

## <a name="test-network-communication"></a>Hálózati kommunikáció tesztelése

A hálózati kommunikáció Network Watcherrel való teszteléséhez először engedélyeznie kell azt abban a régióban, ahol a tesztelni kívánt virtuális gép van, majd használja a kommunikáció tesztelésére a Network Watcher IP-folyamat ellenőrzése funkcióját.

### <a name="enable-network-watcher"></a>A Network Watcher engedélyezése

Ha már van egy hálózati figyelő engedélyezve az USA keleti régiójában, folytassa az [IP-folyamat ellenőrzésének használata](#use-ip-flow-verify) lépéssel. Használja az [az network watcher configure](/cli/azure/network/watcher#az_network_watcher_configure) parancsot hálózati figyelő létrehozásához az EastUS régióban:

```azurecli-interactive
az network watcher configure \
  --resource-group NetworkWatcherRG \
  --locations eastus \
  --enabled
```

### <a name="use-ip-flow-verify"></a>IP-folyamat ellenőrzésének használata

Amikor létrehoz egy virtuális gépet, az Azure az alapértelmezésnek megfelelően engedélyezi és tiltja le a virtuális gépre irányuló és onnan érkező forgalmat. Később felülbírálhatja az Azure alapértelmezett beállításait, és további forgalomtípusokat engedélyezhet vagy tilthat le. Annak megállapítására, hogy a különböző célokra irányuló vagy egy forrás IP-címről érkező forgalom engedélyezett vagy letiltott, használja az [az network watcher test-ip-flow](/cli/azure/network/watcher#az_network_watcher_test_ip_flow) parancsot.

Tesztelje a virtuális gép kimenő kommunikációját a www.bing.com IP-címeinek egyikén:

```azurecli-interactive
az network watcher test-ip-flow \
  --direction outbound \
  --local 10.0.0.4:60000 \
  --protocol TCP \
  --remote 13.107.21.200:80 \
  --vm myVm \
  --nic myVmVMNic \
  --resource-group myResourceGroup \
  --out table
```

Néhány másodperc múlva a visszaadott eredmény tájékoztatja, hogy a hozzáférést egy **AllowInternetOutbound** nevű biztonsági szabály engedélyezte.

Tesztelje a virtuális gép kimenő kommunikációját a 172.31.0.100 címen:

```azurecli-interactive
az network watcher test-ip-flow \
  --direction outbound \
  --local 10.0.0.4:60000 \
  --protocol TCP \
  --remote 172.31.0.100:80 \
  --vm myVm \
  --nic myVmVMNic \
  --resource-group myResourceGroup \
  --out table
```

A visszaadott eredmény tájékoztatja, hogy a hozzáférést egy **DefaultOutboundDenyAll** nevű biztonsági szabály megtagadta.

Tesztelje a 172.31.0.100 címről a virtuális gépre bejövő kommunikációt:

```azurecli-interactive
az network watcher test-ip-flow \
  --direction inbound \
  --local 10.0.0.4:80 \
  --protocol TCP \
  --remote 172.31.0.100:60000 \
  --vm myVm \
  --nic myVmVMNic \
  --resource-group myResourceGroup \
  --out table
```

A visszaadott eredmény tájékoztatja, hogy a hozzáférés egy **DefaultInboundDenyAll** nevű biztonsági szabály miatt meg lett tagadva. Most, hogy tudja, hogy mely biztonsági szabályok engedélyezik vagy tagadják meg a virtuális gépre érkező vagy onnan induló forgalmat, meghatározhatja a problémák megoldásának módját.

## <a name="view-details-of-a-security-rule"></a>Biztonsági szabály részleteinek megtekintése

Annak meghatározásához, hogy az [IP-folyamat ellenőrzésének használata](#use-ip-flow-verify) szabályai miért engedélyezik vagy akadályozzák meg a kommunikációt, tekintse át a hálózati adapter hatályban lévő biztonsági szabályait az [az network nic list-effective-nsg](/cli/azure/network/nic#az_network_nic_list_effective_nsg) paranccsal:

```azurecli-interactive
az network nic list-effective-nsg \
  --resource-group myResourceGroup \
  --name myVmVMNic
```

A visszaadott kimenet tartalmazza a következő szöveget az **AllowInternetOutbound** szabályhoz, amely engedélyezte a www.bing.com címre kimenő forgalmat az előző, [IP-folyamat ellenőrzésének használata](#use-ip-flow-verify) lépésben:

```console
{
 "access": "Allow",
 "additionalProperties": {},
 "destinationAddressPrefix": "Internet",
 "destinationAddressPrefixes": [
  "Internet"
 ],
 "destinationPortRange": "0-65535",
 "destinationPortRanges": [
  "0-65535"
 ],
 "direction": "Outbound",
 "expandedDestinationAddressPrefix": [
  "1.0.0.0/8",
  "2.0.0.0/7",
  "4.0.0.0/6",
  "8.0.0.0/7",
  "11.0.0.0/8",
  "12.0.0.0/6",
  ...
 ],
 "expandedSourceAddressPrefix": null,
 "name": "defaultSecurityRules/AllowInternetOutBound",
 "priority": 65001,
 "protocol": "All",
 "sourceAddressPrefix": "0.0.0.0/0",
 "sourceAddressPrefixes": [
  "0.0.0.0/0"
 ],
 "sourcePortRange": "0-65535",
 "sourcePortRanges": [
  "0-65535"
 ]
},
```

Az előző kimenetben látható, hogy a **destinationAddressPrefix** értéke **Internet**. Nem egyértelmű azonban, hogyan vonatkozik a 13.107.21.200 az **Internet** értékre. Több címelőtagot is láthat az **expandedDestinationAddressPrefix** alatti listában. A listában lévő előtagok egyike a **12.0.0.0/6**, amely magában foglalja a 12.0.0.1–15.255.255.254 tartományba tartozó IP-címeket. Mivel a 13.107.21.200 ebbe a címtartományba esik, az **AllowInternetOutBound** szabály engedélyezi a kimenő forgalmat. Emellett nincsenek magasabb prioritású (alacsonyabb számú) szabályok listázva az előző kimenetben, amelyek felülbírálnák ezt a szabályt. Ha szeretne letiltani egy IP-címre kimenő kommunikációt, akkor felvehet egy magasabb prioritású biztonsági szabályt, amely letiltja az IP-címre kimenő forgalmat a 80-as porton.

Amikor futtatta az `az network watcher test-ip-flow` parancsot, hogy tesztelje a 172.131.0.100 címre kimenő kommunikációt az [IP-folyamat ellenőrzésének használata](#use-ip-flow-verify) lépésben, a kimenetből megtudta, hogy a **DefaultOutboundDenyAll** szabály megtagadta a kommunikációt. A **DefaultOutboundDenyAll** szabály megfelel a **DenyAllOutBound** szabálynak, amely az `az network nic list-effective-nsg` parancs következő kimenetében szerepel:

```console
{
 "access": "Deny",
 "additionalProperties": {},
 "destinationAddressPrefix": "0.0.0.0/0",
 "destinationAddressPrefixes": [
  "0.0.0.0/0"
 ],
 "destinationPortRange": "0-65535",
 "destinationPortRanges": [
  "0-65535"
 ],
 "direction": "Outbound",
 "expandedDestinationAddressPrefix": null,
 "expandedSourceAddressPrefix": null,
 "name": "defaultSecurityRules/DenyAllOutBound",
 "priority": 65500,
 "protocol": "All",
 "sourceAddressPrefix": "0.0.0.0/0",
 "sourceAddressPrefixes": [
  "0.0.0.0/0"
 ],
 "sourcePortRange": "0-65535",
 "sourcePortRanges": [
  "0-65535"
 ]
}
```

A szabály a **0.0.0.0/0** címet listázza, mint **destinationAddressPrefix**. A szabály megtagadja a 172.131.0.100 címre kimenő kommunikációt, mert a cím nincs az `az network nic list-effective-nsg` parancs egyetlen más kimeneti szabályának **destinationAddressPrefix** előtagján belül sem. Ha szeretné engedélyezni a kimenő kommunikációt, akkor felvehet egy magasabb prioritású biztonsági szabályt, amely engedélyezi az 172.131.0.100 IP-címre kimenő forgalmat a 80-as porton.

Amikor futtatta az `az network watcher test-ip-flow` parancsot, hogy tesztelje a 172.131.0.100 címről bejövő kommunikációt az [IP-folyamat ellenőrzésének használata](#use-ip-flow-verify) lépésben, a kimenetből megtudta, hogy a **DefaultInboundDenyAll** szabály megtagadta a kommunikációt. A **DefaultOutboundDenyAll** szabály megfelel a **DenyAllInBound** szabálynak, amely az `az network nic list-effective-nsg` parancs következő kimenetében szerepel:

```console
{
 "access": "Deny",
 "additionalProperties": {},
 "destinationAddressPrefix": "0.0.0.0/0",
 "destinationAddressPrefixes": [
  "0.0.0.0/0"
 ],
 "destinationPortRange": "0-65535",
 "destinationPortRanges": [
  "0-65535"
 ],
 "direction": "Inbound",
 "expandedDestinationAddressPrefix": null,
 "expandedSourceAddressPrefix": null,
 "name": "defaultSecurityRules/DenyAllInBound",
 "priority": 65500,
 "protocol": "All",
 "sourceAddressPrefix": "0.0.0.0/0",
 "sourceAddressPrefixes": [
  "0.0.0.0/0"
 ],
 "sourcePortRange": "0-65535",
 "sourcePortRanges": [
  "0-65535"
 ]
},
```

A rendszer a **DenyAllInBound** szabályt alkalmazta, mert, amint az a kimenetben látható, nincs másik magasabb prioritású szabály az `az network nic list-effective-nsg` parancs kimenetében, amely engedélyezné a virtuális gépre a 172.131.0.100 címről a 80-as porton át bejövő forgalmat. Ha szeretné engedélyezni a bejövő kommunikációt, akkor felvehet egy magasabb prioritású biztonsági szabályt, amely engedélyezi az 172.131.0.100 címről bejövő forgalmat a 80-as porton.

Az ebben a rövid útmutatóban található ellenőrzések az Azure-konfigurációt tesztelték. Ha az ellenőrzések a várt eredményt adták vissza, és továbbra is hálózati problémákat tapasztal, ellenőrizze, hogy nincs-e tűzfal a virtuális gép és a között a végpont között, amelyikkel kommunikál, és hogy a virtuális gép operációs rendszerének tűzfala nem engedélyezi-e vagy tagadja meg a kommunikációt.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, az [az group delete](/cli/azure/group) paranccsal törölheti az erőforráscsoportot és az összes benne található erőforrást:

```azurecli-interactive
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban létrehozott egy virtuális gépet, és diagnosztizálta a bejövő és kimenő hálózati forgalom szűrőit. Megtudta, hogy hálózati biztonsági csoportszabályok engedélyezik vagy tiltják le a virtuális gépekről érkező vagy oda irányuló adatforgalmat. További információ a [biztonsági szabályokról](../virtual-network/network-security-groups-overview.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json) és a [biztonsági szabályok létrehozásának](../virtual-network/manage-network-security-group.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json#create-a-security-rule) módjáról.

Még ha megfelelő hálózati adatforgalmi szűrők vannak is a helyükön, a virtuális géppel folytatott kommunikáció meghiúsulhat az útválasztás konfigurációja miatt. Ha szeretné elsajátítani a virtuális gépek hálózati útválasztási problémáinak diagnosztizálási módját, olvassa el [A virtuális gép útválasztási problémáinak diagnosztizálása](diagnose-vm-network-routing-problem-cli.md) fejezetet, vagy ha a kimenő útválasztási, késési és forgalomszűrés problémák diagnosztizálását egyetlen eszközzel szeretné elvégezni, olvassa el a [Kapcsolatok hibaelhárítása](network-watcher-connectivity-cli.md) fejezetet.