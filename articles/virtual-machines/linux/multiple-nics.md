---
title: Linuxos virtuális gép létrehozása az Azure-ban több hálózati adapterrel
description: Megtudhatja, hogyan hozhat létre egy több hálózati adapterrel rendelkező linuxos virtuális gépet az Azure CLI vagy Resource Manager-sablonok használatával.
author: cynthn
ms.service: virtual-machines
ms.subservice: networking
ms.topic: how-to
ms.workload: infrastructure
ms.date: 06/07/2018
ms.author: cynthn
ms.openlocfilehash: c0eea74890665297a0d450c8afd0a5d60dd1ae00
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102551810"
---
# <a name="how-to-create-a-linux-virtual-machine-in-azure-with-multiple-network-interface-cards"></a>Linux rendszerű virtuális gép létrehozása az Azure-ban több hálózati kártya használatával


Ez a cikk részletesen ismerteti, hogyan hozhat létre több hálózati adapterrel rendelkező virtuális gépet az Azure CLI-vel.

## <a name="create-supporting-resources"></a>Támogató erőforrások létrehozása
Telepítse a legújabb [Azure CLI](/cli/azure/install-az-cli2) -t, és jelentkezzen be egy Azure-fiókba az [az login](/cli/azure/reference-index)használatával.

Az alábbi példákban cserélje le a példában szereplő paraméterek nevét a saját értékeire. Példa a paraméterek neveire: *myResourceGroup*, *mystorageaccount* és *myVM*.

Először hozzon létre egy erőforráscsoportot az [az group create](/cli/azure/group) paranccsal. A következő példában létrehozunk egy *myResourceGroup* nevű erőforráscsoportot a *eastus* helyen:

```azurecli
az group create --name myResourceGroup --location eastus
```

Hozza létre a virtuális hálózatot az [az Network vnet Create](/cli/azure/network/vnet)paranccsal. A következő példában létrehozunk egy *myVnet* nevű virtuális hálózatot és egy *mySubnetFrontEnd* nevű alhálózatot:

```azurecli
az network vnet create \
    --resource-group myResourceGroup \
    --name myVnet \
    --address-prefix 10.0.0.0/16 \
    --subnet-name mySubnetFrontEnd \
    --subnet-prefix 10.0.1.0/24
```

Hozzon létre egy alhálózatot a háttér-forgalomhoz az [az Network vnet subnet Create](/cli/azure/network/vnet/subnet)paranccsal. A következő példa egy *mySubnetBackEnd* nevű alhálózatot hoz létre:

```azurecli
az network vnet subnet create \
    --resource-group myResourceGroup \
    --vnet-name myVnet \
    --name mySubnetBackEnd \
    --address-prefix 10.0.2.0/24
```

Hozzon létre egy hálózati biztonsági csoportot az [az Network NSG Create](/cli/azure/network/nsg)paranccsal. A következő példa a *myNetworkSecurityGroup* nevű hálózati biztonsági csoportot hozza létre:

```azurecli
az network nsg create \
    --resource-group myResourceGroup \
    --name myNetworkSecurityGroup
```

## <a name="create-and-configure-multiple-nics"></a>Több hálózati adapter létrehozása és konfigurálása
Hozzon létre két hálózati adaptert az [az Network NIC Create](/cli/azure/network/nic)paranccsal. Az alábbi példa két hálózati adaptert hoz létre, amelyek neve *myNic1* és *myNic2*, a hálózati biztonsági csoporthoz csatlakoztatva, és egy hálózati adapter csatlakozik az egyes alhálózatokhoz:

```azurecli
az network nic create \
    --resource-group myResourceGroup \
    --name myNic1 \
    --vnet-name myVnet \
    --subnet mySubnetFrontEnd \
    --network-security-group myNetworkSecurityGroup
az network nic create \
    --resource-group myResourceGroup \
    --name myNic2 \
    --vnet-name myVnet \
    --subnet mySubnetBackEnd \
    --network-security-group myNetworkSecurityGroup
```

## <a name="create-a-vm-and-attach-the-nics"></a>Virtuális gép létrehozása és a hálózati adapterek csatlakoztatása
A virtuális gép létrehozásakor határozza meg a által létrehozott hálózati adaptereket `--nics` . A virtuális gép méretének kiválasztásakor is ügyelnie kell rá. A virtuális gépekhez adható hálózati adapterek teljes száma korlátozott. További információ a [Linux rendszerű virtuális gépek méreteiről](../sizes.md).

Hozzon létre egy virtuális gépet az [az vm create](/cli/azure/vm) paranccsal. A következő példa egy *myVM* nevű virtuális gépet hoz létre:

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image UbuntuLTS \
    --size Standard_DS3_v2 \
    --admin-username azureuser \
    --generate-ssh-keys \
    --nics myNic1 myNic2
```

Adja hozzá az útválasztási táblázatokat a vendég operációs rendszerhez a [vendég operációs rendszer konfigurálása több hálózati adapterhez](#configure-guest-os-for-multiple-nics)című témakörben leírt lépések végrehajtásával.

## <a name="add-a-nic-to-a-vm"></a>Hálózati adapter hozzáadása egy virtuális géphez
Az előző lépések több hálózati adapterrel rendelkező virtuális gépet hoztak létre. A meglévő virtuális gépekhez az Azure CLI-vel is hozzáadhat hálózati adaptereket. A különböző virtuálisgép- [méretek](../sizes.md) eltérő számú hálózati adaptert támogatnak, ezért a virtuális gépet ennek megfelelően kell méretezni. Ha szükséges, [átméretezheti a virtuális gépet](change-vm-size.md).

Hozzon létre egy másik hálózati adaptert az [az Network NIC Create](/cli/azure/network/nic)paranccsal. A következő példában létrehozunk egy *myNic3* nevű hálózati adaptert az előző lépésekben létrehozott háttérbeli alhálózathoz és hálózati biztonsági csoporthoz:

```azurecli
az network nic create \
    --resource-group myResourceGroup \
    --name myNic3 \
    --vnet-name myVnet \
    --subnet mySubnetBackEnd \
    --network-security-group myNetworkSecurityGroup
```

Egy hálózati adapter meglévő virtuális géphez való hozzáadásához először szabadítsa fel a virtuális gépet az [az VM felszabadításával](/cli/azure/vm). A következő példa felszabadítja a *myVM* nevű virtuális gépet:


```azurecli
az vm deallocate --resource-group myResourceGroup --name myVM
```

Adja hozzá a hálózati adaptert az [az VM NIC Add](/cli/azure/vm/nic)paranccsal. A következő példa a *myNic3* -t adja hozzá a *myVM*-hoz:

```azurecli
az vm nic add \
    --resource-group myResourceGroup \
    --vm-name myVM \
    --nics myNic3
```

Indítsa el a virtuális gépet az [az VM Start](/cli/azure/vm)paranccsal:

```azurecli
az vm start --resource-group myResourceGroup --name myVM
```

Adja hozzá az útválasztási táblázatokat a vendég operációs rendszerhez a [vendég operációs rendszer konfigurálása több hálózati adapterhez](#configure-guest-os-for-multiple-nics)című témakörben leírt lépések végrehajtásával.

## <a name="remove-a-nic-from-a-vm"></a>Hálózati adapter eltávolítása egy virtuális gépről
Egy hálózati adapter meglévő virtuális gépről való eltávolításához először szabadítsa fel a virtuális gépet az [az VM](/cli/azure/vm)felszabadításával. A következő példa felszabadítja a *myVM* nevű virtuális gépet:

```azurecli
az vm deallocate --resource-group myResourceGroup --name myVM
```

Távolítsa el a hálózati adaptert az [az VM NIC Remove](/cli/azure/vm/nic)paranccsal. Az alábbi példa eltávolítja a *myNic3* -t a *myVM*-ből:

```azurecli
az vm nic remove \
    --resource-group myResourceGroup \
    --vm-name myVM \
    --nics myNic3
```

Indítsa el a virtuális gépet az [az VM Start](/cli/azure/vm)paranccsal:

```azurecli
az vm start --resource-group myResourceGroup --name myVM
```


## <a name="create-multiple-nics-using-resource-manager-templates"></a>Több hálózati adapter létrehozása Resource Manager-sablonok használatával
Azure Resource Manager-sablonok deklaratív JSON-fájlokat használnak a környezet definiálásához. [A Azure Resource Manager áttekintése](../../azure-resource-manager/management/overview.md)olvasható. A Resource Manager-sablonok lehetővé teszik az erőforrások több példányának létrehozását az üzembe helyezés során, például több hálózati adapter létrehozását. A *Másolás* használatával megadhatja a létrehozandó példányok számát:

```json
"copy": {
    "name": "multiplenics"
    "count": "[parameters('count')]"
}
```

További információ a [ *Másolás* használatával létrehozott több példány létrehozásáról](../../azure-resource-manager/templates/copy-resources.md). 

Az a használatával is `copyIndex()` hozzáfűzheti a számot egy erőforrás nevéhez, amely lehetővé teszi, hogy az, `myNic1` `myNic2` stb. Az alábbi példa az index értékének hozzáfűzését mutatja be:

```json
"name": "[concat('myNic', copyIndex())]", 
```

A [Resource Manager-sablonok használatával több hálózati adaptert](../../virtual-network/template-samples.md)is létrehozhat.

Adja hozzá az útválasztási táblázatokat a vendég operációs rendszerhez a [vendég operációs rendszer konfigurálása több hálózati adapterhez](#configure-guest-os-for-multiple-nics)című témakörben leírt lépések végrehajtásával.

## <a name="configure-guest-os-for-multiple-nics"></a>A vendég operációs rendszer konfigurálása több hálózati adapterhez

Az előző lépések létrehozott egy virtuális hálózatot és alhálózatot, csatolt hálózati adaptereket, majd létrehozott egy virtuális GÉPET. Nem jött létre olyan nyilvános IP-cím és hálózati biztonsági csoportra vonatkozó szabály, amely engedélyezi az SSH-forgalmat. Ha több hálózati adapterhez szeretné konfigurálni a vendég operációs rendszert, engedélyeznie kell a távoli kapcsolatokat, és helyileg kell futtatnia a parancsokat a virtuális gépen.

Az SSH-forgalom engedélyezéséhez hozzon létre egy hálózati biztonsági csoportra vonatkozó szabályt az [az Network NSG Rule Create](/cli/azure/network/nsg/rule#az-network-nsg-rule-create) paranccsal a következőképpen:

```azurecli
az network nsg rule create \
    --resource-group myResourceGroup \
    --nsg-name myNetworkSecurityGroup \
    --name allow_ssh \
    --priority 101 \
    --destination-port-ranges 22
```

Hozzon létre egy nyilvános IP-címet az [az Network Public-IP Create](/cli/azure/network/public-ip#az-network-public-ip-create) paranccsal, és rendelje hozzá az első hálózati adapterhez az [az Network NIC IP-config Update](/cli/azure/network/nic/ip-config#az-network-nic-ip-config-update):

```azurecli
az network public-ip create --resource-group myResourceGroup --name myPublicIP

az network nic ip-config update \
    --resource-group myResourceGroup \
    --nic-name myNic1 \
    --name ipconfig1 \
    --public-ip myPublicIP
```

A virtuális gép nyilvános IP-címének megtekintéséhez használja az [az VM show](/cli/azure/vm#az-vm-show) a következőt:

```azurecli
az vm show --resource-group myResourceGroup --name myVM -d --query publicIps -o tsv
```

Most SSH-t a virtuális gép nyilvános IP-címére. Az előző lépésben megadott alapértelmezett Felhasználónév az *azureuser* volt. Adja meg saját felhasználónevét és nyilvános IP-címét:

```bash
ssh azureuser@137.117.58.232
```

Másodlagos hálózati adapterről történő küldéshez manuálisan kell állandó útvonalakat hozzáadni az operációs rendszerhez az egyes másodlagos hálózati adapterekhez. Ebben a cikkben a *eth1* a másodlagos felület. Az állandó útvonalak az operációs rendszerhez való hozzáadására vonatkozó utasítások a disztribúción keresztül változnak. Útmutatásért tekintse meg a disztribúció dokumentációját.

Amikor az útvonalat hozzáadja az operációs rendszerhez, az átjáró címe *1* , hogy a hálózati adapter melyik alhálózathoz tartozik. Ha például a hálózati adapter hozzá van rendelve a *10.0.2.4*, az útvonalhoz megadott átjáró *10.0.2.1*. Megadhat egy adott hálózatot az útvonal céljához, vagy megadhatja a *0.0.0.0* célhelyét, ha azt szeretné, hogy a csatoló összes forgalma áthaladjon a megadott átjárón. Az egyes alhálózatok átjáróját a virtuális hálózat kezeli.

Miután hozzáadta az útvonalat egy másodlagos csatolóhoz, ellenőrizze, hogy az útvonal az útválasztási táblában van-e a következővel: `route -n` . A következő példa kimenete az útválasztási tábla, amelyben a virtuális gép két hálózati adaptere lett hozzáadva ebben a cikkben:

```bash
Kernel IP routing table
Destination     Gateway         Genmask         Flags Metric Ref    Use Iface
0.0.0.0         10.0.1.1        0.0.0.0         UG    0      0        0 eth0
0.0.0.0         10.0.2.1        0.0.0.0         UG    0      0        0 eth1
10.0.1.0        0.0.0.0         255.255.255.0   U     0      0        0 eth0
10.0.2.0        0.0.0.0         255.255.255.0   U     0      0        0 eth1
168.63.129.16   10.0.1.1        255.255.255.255 UGH   0      0        0 eth0
169.254.169.254 10.0.1.1        255.255.255.255 UGH   0      0        0 eth0
```

Ellenőrizze, hogy a hozzáadott útvonal megmarad-e az újraindítások között az útválasztási táblázat újraindítást követően történő ellenőrzésével. A kapcsolat teszteléséhez megadhatja a következő parancsot, például a *eth1* a másodlagos hálózati adapter neve:

```bash
ping bing.com -c 4 -I eth1
```

## <a name="next-steps"></a>Következő lépések
Tekintse át a Linux rendszerű [virtuális gépek méretét](../sizes.md) , amikor több hálózati adapterrel rendelkező virtuális gépet próbál létrehozni. Ügyeljen arra, hogy az egyes VM-méretek hány hálózati adaptert támogatnak.

A virtuális gépek további biztonságossá tételéhez használja az igény szerinti virtuálisgép-hozzáférést. Ez a szolgáltatás a hálózati biztonsági csoportra vonatkozó szabályokat nyitja meg az SSH-forgalomhoz, ha szükséges, és egy meghatározott ideig. További információk: [Manage virtual machine access using just in time](../../security-center/security-center-just-in-time.md) (A virtuális gépekhez való hozzáférés kezelése igény szerinti hozzáférés használata esetén).