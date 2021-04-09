---
title: Hálózati forgalom szűrése – Azure CLI | Microsoft Docs
description: Ebből a cikkből megtudhatja, hogyan szűrheti az alhálózatra irányuló hálózati forgalmat egy hálózati biztonsági csoporttal az Azure CLI használatával.
services: virtual-network
documentationcenter: virtual-network
author: KumudD
manager: twooley
editor: ''
tags: azure-resource-manager
Customer intent: I want to filter network traffic to virtual machines that perform similar functions, such as web servers.
ms.assetid: ''
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: how-to
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 03/30/2018
ms.author: kumud
ms.custom: devx-track-azurecli
ms.openlocfilehash: d2cabecfb2a70e6d325129335c066bb35aeb02ba
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98221846"
---
# <a name="filter-network-traffic-with-a-network-security-group-using-the-azure-cli"></a>Hálózati forgalom szűrése hálózati biztonsági csoporttal az Azure CLI használatával

A virtuális hálózatok alhálózatainak bejövő vagy kimenő hálózati forgalmát hálózati biztonsági csoport segítségével szűrheti. A hálózati biztonsági csoportok biztonsági szabályokat tartalmaznak, amelyek IP-cím, port és protokoll szerint szűrik a hálózati forgalmat. A biztonsági szabályok az egyes alhálózatokban üzembe helyezett erőforrásokra vonatkoznak. Ebben a cikkben az alábbiakkal ismerkedhet meg:

* Hálózati biztonsági csoport és biztonsági szabályok létrehozása
* Virtuális hálózat létrehozása és hálózati biztonsági csoport hozzárendelése egy alhálózathoz
* Virtuális gépek üzembe helyezése egy alhálózaton
* Forgalomszűrők tesztelése

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Ehhez a cikkhez az Azure CLI 2.0.28 verziójára vagy újabb verziójára van szükség. Azure Cloud Shell használata esetén a legújabb verzió már telepítve van.

## <a name="create-a-network-security-group"></a>Hálózati biztonsági csoport létrehozása

A hálózati biztonsági csoportok biztonsági szabályokat tartalmaznak. A biztonsági szabályok egy forrást és egy célt határoznak meg. A források és célok lehetnek alkalmazásbiztonsági csoportok.

### <a name="create-application-security-groups"></a>Alkalmazásbiztonsági csoportok létrehozása

Először hozzon létre egy erőforráscsoportot az ebben a cikkben létrehozott összes erőforráshoz az [az Group Create](/cli/azure/group)paranccsal. A következő példa létrehoz egy erőforráscsoportot az *eastus* helyen: 

```azurecli-interactive
az group create \
  --name myResourceGroup \
  --location eastus
```

Hozzon létre egy alkalmazás biztonsági csoportot az [az Network ASG Create](/cli/azure/network/asg)paranccsal. Az alkalmazásbiztonsági csoportok lehetővé teszik, hogy csoportokba rendezze a hasonló portszűrési követelményekkel rendelkező kiszolgálókat. Az alábbi példa két alkalmazásbiztonsági csoportot hoz létre.

```azurecli-interactive
az network asg create \
  --resource-group myResourceGroup \
  --name myAsgWebServers \
  --location eastus

az network asg create \
  --resource-group myResourceGroup \
  --name myAsgMgmtServers \
  --location eastus
```

### <a name="create-a-network-security-group"></a>Hálózati biztonsági csoport létrehozása

Hozzon létre egy hálózati biztonsági csoportot az [az Network NSG Create](/cli/azure/network/nsg)paranccsal. Az alábbi példa egy *myNsg* nevű hálózati biztonsági csoportot hoz létre: 

```azurecli-interactive 
# Create a network security group
az network nsg create \
  --resource-group myResourceGroup \
  --name myNsg
```

### <a name="create-security-rules"></a>Biztonsági szabályok létrehozása

Hozzon létre egy biztonsági szabályt az [az Network NSG Rule Create](/cli/azure/network/nsg/rule)paranccsal. Az alábbi példa egy olyan szabályt hoz létre, amely engedélyezi az internetről a *myWebServers* alkalmazásbiztonsági csoportba bejövő forgalmat a 80-as és 443-as porton keresztül.

```azurecli-interactive
az network nsg rule create \
  --resource-group myResourceGroup \
  --nsg-name myNsg \
  --name Allow-Web-All \
  --access Allow \
  --protocol Tcp \
  --direction Inbound \
  --priority 100 \
  --source-address-prefix Internet \
  --source-port-range "*" \
  --destination-asgs "myAsgWebServers" \
  --destination-port-range 80 443
```

Az alábbi példa egy olyan szabályt hoz létre, amely engedélyezi az internetről a *myMgmtServers* alkalmazás biztonsági csoportba érkező, a 22-es porton keresztül érkező forgalmat:

```azurecli-interactive
az network nsg rule create \
  --resource-group myResourceGroup \
  --nsg-name myNsg \
  --name Allow-SSH-All \
  --access Allow \
  --protocol Tcp \
  --direction Inbound \
  --priority 110 \
  --source-address-prefix Internet \
  --source-port-range "*" \
  --destination-asgs "myAsgMgmtServers" \
  --destination-port-range 22
```

Ebben a cikkben az SSH-t (a 22-es portot) a *myAsgMgmtServers* virtuális gép internete teszi elérhetővé. Éles környezetek esetén a 22-es port internetre való kihelyezése helyett ajánlott VPN-vagy [magánhálózati](../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json) kapcsolaton keresztül felügyelni kívánt Azure [-](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json) erőforrásokhoz csatlakozni.

## <a name="create-a-virtual-network"></a>Virtuális hálózat létrehozása

Hozzon létre egy virtuális hálózatot az [az network vnet create](/cli/azure/network/vnet) paranccsal. Az alábbi példa egy *myVirtualNetwork* virtuális hálózatot hoz létre:

```azurecli-interactive 
az network vnet create \
  --name myVirtualNetwork \
  --resource-group myResourceGroup \
  --address-prefixes 10.0.0.0/16
```

Adjon hozzá egy alhálózatot egy virtuális hálózathoz az [az Network vnet subnet Create](/cli/azure/network/vnet/subnet)paranccsal. Az alábbi példa egy *mySubnet* nevű alhálózatot ad hozzá a virtuális hálózathoz, és hozzárendeli a *myNsg* hálózati biztonsági csoportot.

```azurecli-interactive
az network vnet subnet create \
  --vnet-name myVirtualNetwork \
  --resource-group myResourceGroup \
  --name mySubnet \
  --address-prefix 10.0.0.0/24 \
  --network-security-group myNsg
```

## <a name="create-virtual-machines"></a>Virtuális gépek létrehozása

Hozzon létre két virtuális gépet a virtuális hálózatban, hogy érvényesíthesse majd a fogalom szűrését egy később lépésben. 

Hozzon létre egy virtuális gépet az [az vm create](/cli/azure/vm) paranccsal. Az alábbi példa egy virtuális gépet hoz létre, amely webkiszolgálóként fog szolgálni. A `--asgs myAsgWebServers` beállítás hatására az Azure hálózati adaptert hoz létre a virtuális géphez a *myAsgWebServers* alkalmazás biztonsági csoportjának tagjaként.

A `--nsg ""` beállítás megadásával megakadályozható, hogy az Azure hozzon létre egy alapértelmezett hálózati biztonsági csoportot az Azure-hoz létrehozott hálózati adapterhez, amikor létrehozza a virtuális gépet. A cikk egyszerűsítése érdekében a rendszer jelszót használ. A kulcsokat jellemzően éles környezetben használják. Ha kulcsokat használ, az SSH-ügynök továbbítását is konfigurálnia kell a hátralévő lépésekhez. További információkért tekintse meg az SSH-ügyfél dokumentációját. Cserélje le a `<replace-with-your-password>` parancsot a következő parancsra a választott jelszóval.

```azurecli-interactive
adminPassword="<replace-with-your-password>"

az vm create \
  --resource-group myResourceGroup \
  --name myVmWeb \
  --image UbuntuLTS \
  --vnet-name myVirtualNetwork \
  --subnet mySubnet \
  --nsg "" \
  --asgs myAsgWebServers \
  --admin-username azureuser \
  --admin-password $adminPassword
```

A virtuális gép üzembe helyezése néhány percet vesz igénybe. A virtuális gép létrehozása után a következő példához hasonló kimenetet ad vissza: 

```output
{
  "fqdns": "",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVmWeb",
  "location": "eastus",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "13.90.242.231",
  "resourceGroup": "myResourceGroup"
}
```

Jegyezze fel a **publicIpAddress** értékét. Ez a címe egy későbbi lépésben a virtuális gép internetről való elérésére szolgál.  Hozzon létre egy felügyeleti kiszolgálóként szolgáló virtuális gépet:

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVmMgmt \
  --image UbuntuLTS \
  --vnet-name myVirtualNetwork \
  --subnet mySubnet \
  --nsg "" \
  --asgs myAsgMgmtServers \
  --admin-username azureuser \
  --admin-password $adminPassword
```

A virtuális gép üzembe helyezése néhány percet vesz igénybe. A virtuális gép létrehozása után jegyezze fel a visszaadott kimenet **publicIpAddress** . Ez a címe a virtuális gép következő lépésben való elérésére szolgál. Ne ugorjon a következő lépésre, amíg az Azure be nem fejezte a virtuális gép létrehozását.

## <a name="test-traffic-filters"></a>Forgalomszűrők tesztelése

Az alábbi parancs használatával hozzon létre egy SSH-munkamenetet a *myVmMgmt* virtuális géppel. Cserélje le a *\<publicIpAddress>* t a virtuális gép nyilvános IP-címére. A fenti példában az IP-cím *13.90.242.231*.

```bash 
ssh azureuser@<publicIpAddress>
```

Ha a rendszer jelszót kér, adja meg a [virtuális gépek létrehozásakor](#create-virtual-machines)megadott jelszót.

A kapcsolódás sikeres volt, mert a 22-es port az internetről a *myAsgMgmtServers* alkalmazás biztonsági csoportba való bejövő hozzáférés engedélyezett, és a *myVmMgmt* virtuális géphez csatolt hálózati adapter be van kapcsolva.

Használja az alábbi parancsot az SSH-val a *myVmWeb* virtuális gépre a *myVmMgmt* virtuális gépről:

```bash 
ssh azureuser@myVmWeb
```

A kapcsolat sikeresen létrejön, mert az egyes hálózati biztonsági csoportokon belüli alapértelmezett biztonsági szabály minden porton keresztül engedélyezi a forgalmat a virtuális hálózaton belüli összes IP-cím között. Az internetről nem lehet SSH-kapcsolatot *myVmWeb* virtuális géphez, mert a *myAsgWebServers* biztonsági szabálya nem engedélyezi az internetről érkező 22-es port használatát.

Az alábbi parancsokkal telepítheti az Nginx-webkiszolgálót a *myVmWeb* virtuális gépre:

```bash 
# Update package source
sudo apt-get -y update

# Install NGINX
sudo apt-get -y install nginx
```

Az *myVmWeb* virtuális gép engedélyezve van az internetre, hogy beolvassa az Nginx-t, mert egy alapértelmezett biztonsági szabály engedélyezi az összes kimenő forgalmat az internetre. Lépjen ki a *myVmWeb* SSH-munkamenetből, amely elhagyja a `username@myVmMgmt:~$` *myVmMgmt* virtuális gép kérdését. Az Nginx üdvözlőképernyő *myVmWeb* virtuális gépről való beolvasásához írja be a következő parancsot:

```bash
curl myVmWeb
```

A *myVmMgmt* virtuális gép kijelentkezése. Annak ellenőrzéséhez, hogy a *myVmWeb* webkiszolgálót az Azure-on kívülről is elérheti, adja meg a `curl <publicIpAddress>` saját számítógépét. A kapcsolódás sikeres, mert az 80-es port engedélyezve van az internetről a *myAsgWebServers* alkalmazás biztonsági csoportjába, amelyhez az *myVmWeb* virtuális géphez csatolt hálózati adapter tartozik.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, az [az Group delete](/cli/azure/group) paranccsal távolítsa el az erőforráscsoportot és a benne található összes erőforrást.

```azurecli-interactive
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>Következő lépések

Ebben a cikkben létrehozott egy hálózati biztonsági csoportot, és hozzárendelte azt egy virtuális hálózati alhálózathoz. A hálózati biztonsági csoportokkal kapcsolatos további információ: [Hálózati biztonsági csoportok áttekintése](./network-security-groups-overview.md) és [Hálózati biztonsági csoportok kezelése](manage-network-security-group.md).

Az Azure alapértelmezés szerint irányítja a forgalmat az alhálózatok között. Ehelyett lehetősége van arra, hogy egy virtuális gépen keresztül irányítsa a forgalmat az alhálózatok között, amely így például tűzfalként is szolgálhat. További információt az [útválasztási táblázat létrehozása](tutorial-create-route-table-cli.md)című témakörben talál.