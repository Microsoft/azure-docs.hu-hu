---
title: Portok megnyitása egy virtuális géphez az Azure CLI-vel
description: Megtudhatja, hogyan nyithat meg egy portot/hozzon létre egy végpontot a virtuális géphez az Azure CLI használatával.
author: cynthn
manager: gwallace
ms.service: virtual-machines
ms.subservice: networking
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 12/13/2017
ms.author: cynthn
ms.custom: devx-track-azurecli
ms.openlocfilehash: a96c0f7c6fb767b96be273a615149143043e8bc1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "91975127"
---
# <a name="open-ports-and-endpoints-to-a-vm-with-the-azure-cli"></a>Portok és végpontok megnyitása egy virtuális gépre az Azure CLI-vel

Megnyit egy portot, vagy létrehozhat egy végpontot egy virtuális géphez (VM) az Azure-ban egy alhálózat vagy virtuálisgép-hálózati adapter hálózati szűrőjének létrehozásával. Ezeket a szűrőket, amelyek a bejövő és a kimenő forgalmat is szabályozzák, a forgalmat fogadó erőforráshoz csatolt hálózati biztonsági csoporton. Az 80-as porton a webes forgalom általános példáját használjuk. Ez a cikk bemutatja, hogyan nyithat meg egy portot egy virtuális géphez az Azure CLI-vel. 


Hálózati biztonsági csoport és szabályok létrehozásához a legújabb [Azure CLI](/cli/azure/install-az-cli2) -t telepíteni kell, és be kell jelentkeznie egy Azure-fiókba az [az login](/cli/azure/reference-index)használatával.

Az alábbi példákban cserélje le a példában szereplő paraméterek nevét a saját értékeire. A paraméterek nevei például a következők: *myResourceGroup*, *myNetworkSecurityGroup* és *myVnet*.


## <a name="quickly-open-a-port-for-a-vm"></a>Virtuális gép portjának gyors megnyitása
Ha egy fejlesztési/tesztelési forgatókönyvben gyorsan meg kell nyitnia egy virtuális gép portját, az az [VM Open-port](/cli/azure/vm) parancsot használhatja. Ez a parancs létrehoz egy hálózati biztonsági csoportot, hozzáadja a szabályt, és alkalmazza azt egy virtuális gépre vagy alhálózatra. Az alábbi példa megnyitja a *80* -es portot a *myVM* nevű virtuális gépen a *myResourceGroup* nevű erőforráscsoport-csoportban.

```azurecli
az vm open-port --resource-group myResourceGroup --name myVM --port 80
```

A szabályok részletesebb szabályozásához, például a forrás IP-címtartomány definiálásához folytassa a cikk további lépéseit.


## <a name="create-a-network-security-group-and-rules"></a>Hálózati biztonsági csoport és szabályok létrehozása
Hozza létre a hálózati biztonsági csoportot az [az Network NSG Create](/cli/azure/network/nsg)paranccsal. A következő példában létrehozunk egy *myNetworkSecurityGroup* nevű hálózati biztonsági csoportot a *eastus* helyen:

```azurecli
az network nsg create \
    --resource-group myResourceGroup \
    --location eastus \
    --name myNetworkSecurityGroup
```

Adjon hozzá egy szabályt az [az Network NSG Rule Create](/cli/azure/network/nsg/rule) paranccsal, hogy engedélyezze a HTTP-forgalmat a webkiszolgálón (vagy módosítsa a saját forgatókönyvét, például SSH-hozzáférést vagy adatbázis-kapcsolatot). A következő példa egy *myNetworkSecurityGroupRule* nevű szabályt hoz létre a 80-es porton a TCP-forgalom engedélyezéséhez:

```azurecli
az network nsg rule create \
    --resource-group myResourceGroup \
    --nsg-name myNetworkSecurityGroup \
    --name myNetworkSecurityGroupRule \
    --protocol tcp \
    --priority 1000 \
    --destination-port-range 80
```


## <a name="apply-network-security-group-to-vm"></a>Hálózati biztonsági csoport alkalmazása virtuális gépre
Társítsa a hálózati biztonsági csoportot a virtuális gép hálózati adapteréhez (NIC) az [az Network NIC Update paranccsal](/cli/azure/network/nic). Az alábbi példa egy *myNic* nevű meglévő NIC-t társít a *MyNetworkSecurityGroup* nevű hálózati biztonsági csoporttal:

```azurecli
az network nic update \
    --resource-group myResourceGroup \
    --name myNic \
    --network-security-group myNetworkSecurityGroup
```

Azt is megteheti, hogy a hálózati biztonsági csoportot egy virtuális hálózati alhálózattal társítja az [az Network vnet subnet Update](/cli/azure/network/vnet/subnet) helyett, nem csupán egyetlen virtuális gép hálózati adapteréhez. Az alábbi példa egy *mySubnet* nevű meglévő alhálózatot társít a *myVnet* virtuális hálózatban a *myNetworkSecurityGroup* nevű hálózati biztonsági csoporttal:

```azurecli
az network vnet subnet update \
    --resource-group myResourceGroup \
    --vnet-name myVnet \
    --name mySubnet \
    --network-security-group myNetworkSecurityGroup
```

## <a name="more-information-on-network-security-groups"></a>További információ a hálózati biztonsági csoportokról
Az itt található gyors parancsok lehetővé teszik a virtuális gép felé irányuló forgalom működésének megkezdését. A hálózati biztonsági csoportok számos nagyszerű szolgáltatást és részletességet biztosítanak az erőforrásokhoz való hozzáférés szabályozásához. [A hálózati biztonsági csoport és az ACL-szabályok létrehozásával kapcsolatban itt](tutorial-virtual-network.md#secure-network-traffic)talál további információt.

A magasan elérhető webalkalmazások esetében a virtuális gépeket egy Azure Load Balancer mögé kell helyeznie. A terheléselosztó elosztja a forgalmat a virtuális gépekre egy olyan hálózati biztonsági csoporttal, amely forgalmi szűrést biztosít. További információkért lásd: a [Linux rendszerű virtuális gépek terheléselosztása az Azure-ban egy olyan, magasan elérhető alkalmazás létrehozásához](tutorial-load-balancer.md).

## <a name="next-steps"></a>Következő lépések
Ebben a példában egy egyszerű szabályt hozott létre a HTTP-forgalom engedélyezéséhez. A részletesebb környezetek létrehozásáról a következő cikkekben talál információt:

* [Az Azure Resource Manager áttekintése](../../azure-resource-manager/management/overview.md)
* [Mi az a hálózati biztonsági csoport (NSG)?](../../virtual-network/network-security-groups-overview.md)