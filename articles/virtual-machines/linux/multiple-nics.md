---
title: Linux rendszerű virtuális gép létrehozása több hálózati etitokkal az Azure-ban
description: Megtudhatja, hogyan hozhat létre Egy Linux rendszerű virtuális gépet több, az Azure CLI vagy a hozzá csatolt hálózati Resource Manager használatával.
author: cynthn
ms.service: virtual-machines
ms.subservice: networking
ms.topic: how-to
ms.workload: infrastructure
ms.date: 06/07/2018
ms.author: cynthn
ms.openlocfilehash: b08e8ebbba3ba91c1c1aa0f135c4cba37ba038b1
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107769912"
---
# <a name="how-to-create-a-linux-virtual-machine-in-azure-with-multiple-network-interface-cards"></a>Linux rendszerű virtuális gép létrehozása az Azure-ban több hálózati adapter használatával


Ez a cikk részletesen bemutatja, hogyan hozhat létre több hálózati ic-et tartalmazó virtuális gépet az Azure CLI használatával.

## <a name="create-supporting-resources"></a>Támogató erőforrások létrehozása
Telepítse a legújabb [Azure CLI-t,](/cli/azure/install-az-cli2) és jelentkezzen be egy Azure-fiókba [az az login használatával.](/cli/azure/reference-index)

A következő példákban cserélje le a példaparaméter-neveket a saját értékeire. A paraméternevek közé tartozik például a *myResourceGroup,* *a mystorageaccount* és a *myVM.*

Először hozzon létre egy erőforráscsoportot az [az group create](/cli/azure/group) paranccsal. A következő példában létrehozunk egy *myResourceGroup* nevű erőforráscsoportot az *eastus* helyen:

```azurecli
az group create --name myResourceGroup --location eastus
```

Hozza létre a virtuális hálózatot [az az network vnet create gombra.](/cli/azure/network/vnet) Az alábbi példa egy mySubnetFrontEnd nevű virtuális hálózatot és alhálózatot hoz *létre mySubnetFrontEnd névvel:* 

```azurecli
az network vnet create \
    --resource-group myResourceGroup \
    --name myVnet \
    --address-prefix 10.0.0.0/16 \
    --subnet-name mySubnetFrontEnd \
    --subnet-prefix 10.0.1.0/24
```

Hozzon létre egy alhálózatot a háttérforgalom számára [az az network vnet subnet create segítségével.](/cli/azure/network/vnet/subnet) Az alábbi példa létrehoz egy *mySubnetBackEnd nevű alhálózatot:*

```azurecli
az network vnet subnet create \
    --resource-group myResourceGroup \
    --vnet-name myVnet \
    --name mySubnetBackEnd \
    --address-prefix 10.0.2.0/24
```

Hozzon létre egy hálózati biztonsági csoportot [az az network nsg create gombra.](/cli/azure/network/nsg) A következő példa a *myNetworkSecurityGroup* nevű hálózati biztonsági csoportot hozza létre:

```azurecli
az network nsg create \
    --resource-group myResourceGroup \
    --name myNetworkSecurityGroup
```

## <a name="create-and-configure-multiple-nics"></a>Több hálózati adatbecset létrehozása és konfigurálása
Hozzon létre két hálózati adaptert [az az network nic create segítségével.](/cli/azure/network/nic) Az alábbi példa két hálózati adaptert hoz létre *myNic1* és *myNic2* névvel, csatlakoztatva a hálózati biztonsági csoportot, és mindegyik alhálózathoz egy hálózati adapter csatlakozik:

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

## <a name="create-a-vm-and-attach-the-nics"></a>Virtuális gép létrehozása és a NIC-k csatolása
A virtuális gép létrehozásakor adja meg a következővel létrehozott NIC-ket: `--nics` . A virtuális gép méretének kiválasztásakor is ügyelni kell rá. A virtuális géphez hozzáadhatja a NIC-k teljes számát korlátozásokkal. További információ a [Linux rendszerű virtuális gépek méretről:](../sizes.md).

Hozzon létre egy virtuális gépet az [az vm create](/cli/azure/vm) paranccsal. Az alábbi példa egy *myVM* nevű virtuális gépet hoz létre:

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

Adja hozzá az útválasztási táblákat a vendég operációs rendszerhez a Vendég operációs rendszer konfigurálása több hálózati [kapcsolathoz lépésekkel.](#configure-guest-os-for-multiple-nics)

## <a name="add-a-nic-to-a-vm"></a>Hálózati adapter hozzáadása virtuális géphez
Az előző lépések egy virtuális gépet hoztak létre több NIC-val. Hálózaticsomópontokat egy meglévő virtuális géphez is hozzáadhat az Azure CLI használatával. A [különböző virtuálisgép-méretek](../sizes.md) eltérő számú NIC-t támogatnak, ezért ennek megfelelően méretezve a virtuális gépet. Szükség esetén átméretezhet [egy virtuális gépet.](change-vm-size.md)

Hozzon létre egy másik hálózati adaptert [az az network nic create gombra.](/cli/azure/network/nic) A következő példában létrehozunk egy *myNic3* nevű hálózati adaptert, amely az előző lépésekben létrehozott háttér-alhálózathoz és hálózati biztonsági csoporthoz csatlakozik:

```azurecli
az network nic create \
    --resource-group myResourceGroup \
    --name myNic3 \
    --vnet-name myVnet \
    --subnet mySubnetBackEnd \
    --network-security-group myNetworkSecurityGroup
```

Ha hálózati adaptert szeretne hozzáadni egy meglévő virtuális géphez, először szabadította fel a virtuális gépet [az az vm deallocate gombra.](/cli/azure/vm) Az alábbi példa felszabadítja a *myVM* nevű virtuális gépet:


```azurecli
az vm deallocate --resource-group myResourceGroup --name myVM
```

Adja hozzá a hálózati adaptert [az az vm nic add gombra.](/cli/azure/vm/nic) Az alábbi példa hozzáadja a *myNic3 adatokat* a *myVM-hez:*

```azurecli
az vm nic add \
    --resource-group myResourceGroup \
    --vm-name myVM \
    --nics myNic3
```

Indítsa el a virtuális gépet [az az vm start sal:](/cli/azure/vm)

```azurecli
az vm start --resource-group myResourceGroup --name myVM
```

Adja hozzá az útválasztási táblákat a vendég operációs rendszerhez a Vendég operációs rendszer konfigurálása több hálózati [kapcsolathoz lépésekkel.](#configure-guest-os-for-multiple-nics)

## <a name="remove-a-nic-from-a-vm"></a>Hálózati adapter eltávolítása virtuális gépről
Egy hálózati adapter meglévő virtuális gépről való eltávolításához először szabadítsuk fel a virtuális gépet [az az vm deallocate gombra.](/cli/azure/vm) Az alábbi példa felszabadítja a *myVM* nevű virtuális gépet:

```azurecli
az vm deallocate --resource-group myResourceGroup --name myVM
```

Távolítsa el a hálózati adaptert [az az vm nic remove gombra.](/cli/azure/vm/nic) Az alábbi példa eltávolítja a *myNic3 adatokat* a *myVM-ről:*

```azurecli
az vm nic remove \
    --resource-group myResourceGroup \
    --vm-name myVM \
    --nics myNic3
```

Indítsa el a virtuális gépet [az az vm start menüvel:](/cli/azure/vm)

```azurecli
az vm start --resource-group myResourceGroup --name myVM
```


## <a name="create-multiple-nics-using-resource-manager-templates"></a>Több NICs létrehozása Resource Manager sablonokkal
Azure Resource Manager sablonok deklaratív JSON-fájlokat használnak a környezet meghatározásához. A cikk [áttekintését a Azure Resource Manager.](../../azure-resource-manager/management/overview.md) Resource Manager sablonokkal több erőforráspéldányt is létrehozhat az üzembe helyezés során, például több NIC-t. A másolat *használatával adhatja* meg a létrehozni kívánt példányok számát:

```json
"copy": {
    "name": "multiplenics"
    "count": "[parameters('count')]"
}
```

További információ több [példány másolás használatával történő *létrehozásáról.*](../../azure-resource-manager/templates/copy-resources.md) 

A használatával egy számot is hozzáfűzhet egy erőforrásnévhez, amely lehetővé teszi a `copyIndex()` `myNic1` , `myNic2` stb. létrehozására. Az alábbiakban egy példát mutatunk be az indexérték hozzáfűzésre:

```json
"name": "[concat('myNic', copyIndex())]", 
```

A sablonokkal teljes körű példát olvashat több Resource Manager [létrehozásáról.](../../virtual-network/template-samples.md)

Adja hozzá az útválasztási táblákat a vendég operációs rendszerhez a Vendég operációs rendszer konfigurálása [több hálózati gombra lépéseit követve.](#configure-guest-os-for-multiple-nics)

## <a name="configure-guest-os-for-multiple-nics"></a>Vendég operációs rendszer konfigurálása több hálózati ic-hez

Az előző lépések létrehozták a virtuális hálózatot és az alhálózatot, hálózati adaptereket csatoltak, majd létrehoztak egy virtuális gépet. Az SSH-forgalmat engedélyező nyilvános IP-cím és hálózati biztonságicsoport-szabályok nem voltak létrehozva. Ha a vendég operációs rendszert több hálózati vezérlőre is konfigurálni kell, engedélyeznie kell a távoli kapcsolatokat, és helyileg kell futtatnia a parancsokat a virtuális gépen.

Az SSH-forgalom engedélyezése érdekében hozzon létre egy hálózati biztonságicsoport-szabályt [az az network nsg rule create](/cli/azure/network/nsg/rule#az_network_nsg_rule_create) gombra a következőképpen:

```azurecli
az network nsg rule create \
    --resource-group myResourceGroup \
    --nsg-name myNetworkSecurityGroup \
    --name allow_ssh \
    --priority 101 \
    --destination-port-ranges 22
```

Hozzon létre egy nyilvános [IP-címet az az network public-ip create](/cli/azure/network/public-ip#az_network_public_ip_create) segítségével, és rendelje hozzá az első hálózati adapterhez [az az network nic ip-config update segítségével:](/cli/azure/network/nic/ip-config#az_network_nic_ip_config_update)

```azurecli
az network public-ip create --resource-group myResourceGroup --name myPublicIP

az network nic ip-config update \
    --resource-group myResourceGroup \
    --nic-name myNic1 \
    --name ipconfig1 \
    --public-ip myPublicIP
```

A virtuális gép nyilvános IP-címének megtekintéséhez használja [az az vm show](/cli/azure/vm#az_vm_show) használhatja a következőt:

```azurecli
az vm show --resource-group myResourceGroup --name myVM -d --query publicIps -o tsv
```

Most pedig az SSH-t a virtuális gép nyilvános IP-címére. Az előző lépésben megadott alapértelmezett felhasználónév az *azureuser volt.* Adja meg a saját felhasználónevét és nyilvános IP-címét:

```bash
ssh azureuser@137.117.58.232
```

Ha másodlagos hálózati adapterre vagy adapterről szeretne küldeni, manuálisan kell állandó útvonalakat hozzáadnia az operációs rendszerhez minden másodlagos hálózati adapterhez. Ebben a cikkben az *eth1 a* másodlagos felület. Az állandó útvonalak operációs rendszerhez való hozzáadására vonatkozó utasítások disztribúciónként eltérőek lehetnek. Útmutatásért tekintse meg a disztribúció dokumentációját.

Amikor hozzáadja az útvonalat az operációs rendszerhez, az átjáró címe *.1* lesz bármelyik alhálózathoz, amelyiken a hálózati adapter található. Ha például a hálózati adapterhez a *10.0.2.4 cím* van hozzárendelve, az útvonalhoz megadott átjáró *a 10.0.2.1.* Megadhat egy adott hálózatot az útvonal célhelyének, vagy megadhat egy *0.0.0.0* célhelyet is, ha azt szeretné, hogy az adapter összes forgalma áthaladzon a megadott átjárón. Az egyes alhálózatok átjáróját a virtuális hálózat kezeli.

Miután hozzáadta az útvonalat egy másodlagos felülethez, ellenőrizze, hogy az útvonal az útvonaltáblában van-e a `route -n` használatával. A következő példakimenet arra az útvonaltáblára mutat, amely a cikkben a virtuális géphez hozzáadott két hálózati adaptert tartalmazza:

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

Az újraindítás után ellenőrizze, hogy a hozzáadott útvonal megmarad-e az újraindítások között. Ehhez ellenőrizze újra az útvonaltáblát. A kapcsolat teszteléséhez írja be a következő parancsot, például, ahol *az eth1* egy másodlagos hálózati adapter neve:

```bash
ping bing.com -c 4 -I eth1
```

## <a name="next-steps"></a>Következő lépések
Tekintse át a Linux rendszerű virtuális gépek [méretét,](../sizes.md) amikor több NPC-val rendelkező virtuális gépet próbál létrehozni. Figyelje meg az egyes virtuálisgép-méretek által támogatott maximális számú NIC-t.

A virtuális gépek biztonságának további biztosítása érdekében használjon időben elérhető virtuálisgép-hozzáférést. Ez a funkció szükség esetén és meghatározott időtartamra megnyitja az SSH-forgalom hálózati biztonságicsoport-szabályait. További információk: [Manage virtual machine access using just in time](../../security-center/security-center-just-in-time.md) (A virtuális gépekhez való hozzáférés kezelése igény szerinti hozzáférés használata esetén).