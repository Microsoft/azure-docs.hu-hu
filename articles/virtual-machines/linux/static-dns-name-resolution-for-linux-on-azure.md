---
title: Belső DNS használata a virtuális gépek névfeloldásához az Azure CLI-vel
description: Virtuális hálózati adapterek létrehozása és belső DNS használata az Azure-beli virtuális gépek névfeloldásához az Azure CLI-vel.
author: cynthn
ms.service: virtual-machines
ms.subservice: networking
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 02/16/2017
ms.author: cynthn
ms.openlocfilehash: 3d68ac7aa9927e62011c58b17139d7232ce4a10c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98200753"
---
# <a name="create-virtual-network-interface-cards-and-use-internal-dns-for-vm-name-resolution-on-azure"></a>Virtuális hálózati kártya létrehozása és belső DNS használata a virtuális gépek névfeloldásához az Azure-ban

Ebből a cikkből megtudhatja, hogyan állíthatja be a linuxos virtuális gépek statikus belső DNS-neveit a Virtual Network Cards (Vnic) és a DNS-címkék neveivel az Azure CLI használatával. A statikus DNS-neveket olyan állandó infrastruktúra-szolgáltatásokhoz használják, mint például egy Jenkins-Build kiszolgáló, amelyet a dokumentum vagy egy git-kiszolgáló használ.

Követelmények:

* [egy Azure-fiók](https://azure.microsoft.com/pricing/free-trial/)
* [SSH nyilvános- és titkoskulcs-fájlok](mac-create-ssh-keys.md)

## <a name="quick-commands"></a>Gyors parancsok
Ha gyorsan kell végrehajtania a feladatot, a következő szakasz részletezi a szükséges parancsokat. Az egyes lépések részletes információi és kontextusa a dokumentum többi részében található, [itt kezdődik](#detailed-walkthrough). Ezen lépések elvégzéséhez szüksége lesz a legújabb [Azure CLI](/cli/azure/install-az-cli2) -re, és be kell jelentkeznie egy Azure-fiókba az [az login](/cli/azure/reference-index)használatával.

Előfeltételek: erőforráscsoport, virtuális hálózat és alhálózat, SSH-bejövő hálózati biztonsági csoport.

### <a name="create-a-virtual-network-interface-card-with-a-static-internal-dns-name"></a>Virtuális hálózati kártya létrehozása statikus belső DNS-névvel
Hozza létre a vNic az [az Network NIC Create](/cli/azure/network/nic)paranccsal. A `--internal-dns-name` CLI jelölő a DNS-címke beállítására szolgál, amely a virtuális hálózati kártya (vNic) statikus DNS-nevét adja meg. Az alábbi példa létrehoz egy nevű vNic `myNic` , csatlakoztatja a `myVnet` virtuális hálózathoz, és létrehoz egy belső DNS-nevet a következő néven `jenkins` :

```azurecli
az network nic create \
    --resource-group myResourceGroup \
    --name myNic \
    --vnet-name myVnet \
    --subnet mySubnet \
    --internal-dns-name jenkins
```

### <a name="deploy-a-vm-and-connect-the-vnic"></a>Virtuális gép üzembe helyezése és a vNic összekötése
Hozzon létre egy virtuális gépet az [az vm create](/cli/azure/vm) paranccsal. A `--nics` jelző csatlakoztatja a vNic a virtuális géphez a üzembe helyezés során az Azure-ban. Az alábbi példa egy Azure Managed Disks nevű virtuális gépet hoz létre `myVM` , és csatolja az `myNic` előző lépésben megnevezett vNic:

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --nics myNic \
    --image UbuntuLTS \
    --admin-username azureuser \
    --ssh-key-value ~/.ssh/id_rsa.pub
```

## <a name="detailed-walkthrough"></a>Részletes bemutató

Az Azure-ban egy teljes körű folyamatos integrációs és folyamatos üzembe helyezési (vel-) infrastruktúra szükséges ahhoz, hogy bizonyos kiszolgálók statikus vagy hosszú élettartamú kiszolgálók legyenek. Javasoljuk, hogy az Azure-eszközök, például a virtuális hálózatok és a hálózati biztonsági csoportok statikus és hosszú élettartamú erőforrások legyenek, amelyeket ritkán telepítenek. A virtuális hálózat üzembe helyezését követően az új központi telepítések újra felhasználhatják az infrastruktúrát hátrányosan befolyásoló esetleges károsodás nélkül. Később hozzáadhat egy git-tárház-kiszolgálót vagy egy Jenkins Automation-kiszolgálót, amely a fejlesztési és tesztelési környezetekhez vel biztosít erre a virtuális hálózatra.  

A belső DNS-nevek csak egy Azure-beli virtuális hálózaton belül oldhatók fel. Mivel a DNS-nevek belsőek, nem oldhatók fel a külső interneten, és további biztonságot biztosítanak az infrastruktúrának.

Az alábbi példákban cserélje le a példában szereplő paraméterek nevét a saját értékeire. Például a paraméterek nevei a következők:, `myResourceGroup` `myNic` és `myVM` .

## <a name="create-the-resource-group"></a>Az erőforráscsoport létrehozása
Először hozza létre az erőforráscsoportot az [az Group Create](/cli/azure/group)paranccsal. A következő példában létrehozunk egy `westus` nevű erőforráscsoportot a `myResourceGroup` helyen:

```azurecli
az group create --name myResourceGroup --location westus
```

## <a name="create-the-virtual-network"></a>A virtuális hálózat létrehozása

A következő lépés egy virtuális hálózat létrehozása a virtuális gépek a rendszerbe való elindításához. A virtuális hálózat ebben az útmutatóban egy alhálózatot tartalmaz. Az Azure Virtual Networks szolgáltatással kapcsolatos további információkért lásd: [virtuális hálózat létrehozása](../../virtual-network/manage-virtual-network.md#create-a-virtual-network). 

Hozza létre a virtuális hálózatot az [az Network vnet Create](/cli/azure/network/vnet)paranccsal. A következő példában létrehozunk egy nevű virtuális hálózatot `myVnet` és egy alhálózatot `mySubnet` :

```azurecli
az network vnet create \
    --resource-group myResourceGroup \
    --name myVnet \
    --address-prefix 192.168.0.0/16 \
    --subnet-name mySubnet \
    --subnet-prefix 192.168.1.0/24
```

## <a name="create-the-network-security-group"></a>A hálózati biztonsági csoport létrehozása
Az Azure-beli hálózati biztonsági csoportok egyenértékűek a hálózati rétegben található tűzfallal. A hálózati biztonsági csoportokkal kapcsolatos további információkért lásd: [NSG létrehozása az Azure CLI-ben](../../virtual-network/tutorial-filter-network-traffic-cli.md). 

Hozza létre a hálózati biztonsági csoportot az [az Network NSG Create](/cli/azure/network/nsg)paranccsal. A következő példa egy nevű hálózati biztonsági csoportot hoz létre `myNetworkSecurityGroup` :

```azurecli
az network nsg create \
    --resource-group myResourceGroup \
    --name myNetworkSecurityGroup
```

## <a name="add-an-inbound-rule-to-allow-ssh"></a>Bejövő szabály hozzáadása az SSH engedélyezéséhez
Adjon hozzá egy bejövő szabályt a hálózati biztonsági csoporthoz az [az Network NSG Rule Create](/cli/azure/network/nsg/rule)paranccsal. A következő példa egy nevű szabályt hoz létre `myRuleAllowSSH` :

```azurecli
az network nsg rule create \
    --resource-group myResourceGroup \
    --nsg-name myNetworkSecurityGroup \
    --name myRuleAllowSSH \
    --protocol tcp \
    --direction inbound \
    --priority 1000 \
    --source-address-prefix '*' \
    --source-port-range '*' \
    --destination-address-prefix '*' \
    --destination-port-range 22 \
    --access allow
```

## <a name="associate-the-subnet-with-the-network-security-group"></a>Alhálózat hozzárendelése a hálózati biztonsági csoporthoz
Ha az alhálózatot a hálózati biztonsági csoporttal szeretné hozzárendelni, használja az [az Network vnet subnet Update](/cli/azure/network/vnet/subnet)lehetőséget. Az alábbi példa az alhálózat nevét társítja `mySubnet` a nevű hálózati biztonsági csoporttal `myNetworkSecurityGroup` :

```azurecli
az network vnet subnet update \
    --resource-group myResourceGroup \
    --vnet-name myVnet \
    --name mySubnet \
    --network-security-group myNetworkSecurityGroup
```


## <a name="create-the-virtual-network-interface-card-and-static-dns-names"></a>A virtuális hálózati kártya és a statikus DNS-nevek létrehozása
Az Azure nagyon rugalmas, de a virtuális gépek névfeloldásához DNS-neveket kell létrehoznia, és létre kell hoznia egy DNS-címkét tartalmazó virtuális hálózati adaptereket (Vnic). a Vnic fontosak, mivel ezeket újra felhasználhatja, ha a különböző virtuális gépekhez csatlakoztatja őket az infrastruktúra életciklusa alatt. Ez a megközelítés megtartja a vNic statikus erőforrásként, amíg a virtuális gépek átmenetiek lehetnek. A vNic DNS-címkézés használatával engedélyezhető az egyszerű névfeloldás a VNet lévő más virtuális gépekről. A feloldható nevek használatával más virtuális gépek is hozzáférhetnek az Automation-kiszolgálóhoz a DNS-név `Jenkins` vagy a git-kiszolgáló alapján `gitrepo` .  

Hozza létre a vNic az [az Network NIC Create](/cli/azure/network/nic)paranccsal. Az alábbi példa létrehoz egy nevű vNic `myNic` , összekapcsolja a `myVnet` nevű virtuális hálózattal, `myVnet` és létrehoz egy belső DNS-nevet a következő néven `jenkins` :

```azurecli
az network nic create \
    --resource-group myResourceGroup \
    --name myNic \
    --vnet-name myVnet \
    --subnet mySubnet \
    --internal-dns-name jenkins
```

## <a name="deploy-the-vm-into-the-virtual-network-infrastructure"></a>A virtuális gép üzembe helyezése a virtuális hálózati infrastruktúrában
Most már van egy virtuális hálózat és alhálózat, egy hálózati biztonsági csoport, amely tűzfalként működik az alhálózat védelme érdekében, az összes bejövő forgalom blokkolásával, kivéve a 22-es portot az SSH-hoz, és egy vNic. Most már üzembe helyezhet egy virtuális gépet a meglévő hálózati infrastruktúrán belül.

Hozzon létre egy virtuális gépet az [az vm create](/cli/azure/vm) paranccsal. Az alábbi példa egy Azure Managed Disks nevű virtuális gépet hoz létre `myVM` , és csatolja az `myNic` előző lépésben megnevezett vNic:

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --nics myNic \
    --image UbuntuLTS \
    --admin-username azureuser \
    --ssh-key-value ~/.ssh/id_rsa.pub
```

Ha a CLI-jelzőket használja a meglévő erőforrások meghívására, arra utasítja az Azure-t, hogy telepítse a virtuális gépet a meglévő hálózaton belül. Ha szeretné megismételni a VNet és az alhálózat üzembe helyezését, akkor az Azure-régióban statikus vagy állandó erőforrások maradhatnak.  

## <a name="next-steps"></a>Következő lépések
* [Saját egyéni környezet létrehozása Linux virtuális gép számára közvetlenül Azure parancssori felület parancsait használva](create-cli-complete.md)
* [Linux rendszerű virtuális gép létrehozása az Azure-ban sablonok használatával](create-ssh-secured-vm-from-template.md)
