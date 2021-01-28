---
title: NAT-átjáró létrehozása és tesztelése – Azure CLI
titlesuffix: Azure Virtual Network NAT
description: Ez az oktatóanyag bemutatja, hogyan hozhat létre NAT-átjárót az Azure CLI használatával, és hogyan tesztelheti a NAT szolgáltatást
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumudD
Customer intent: I want to test a NAT gateway for outbound connectivity for my virtual network.
ms.service: virtual-network
ms.subservice: nat
ms.devlang: na
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 06/11/2020
ms.author: allensu
ms.custom: devx-track-azurecli
ms.openlocfilehash: 457749b353b9b7fabfb137ebe907463bb17158ba
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/28/2021
ms.locfileid: "98926931"
---
# <a name="tutorial-create-a-nat-gateway-using-azure-cli-and-test-the-nat-service"></a>Oktatóanyag: NAT-átjáró létrehozása az Azure CLI használatával és a NAT szolgáltatás tesztelése

Ebben az oktatóanyagban létrehoz egy NAT-átjárót az Azure-beli virtuális gépek kimenő kapcsolatának biztosításához. A NAT-átjáró teszteléséhez üzembe kell helyeznie egy forrás-és cél virtuális gépet. Tesztelje a NAT-átjárót úgy, hogy kimenő kapcsolatokat végez a nyilvános IP-címhez. Ezek a kapcsolatok a forrásról a cél virtuális gépre kerülnek. Ez az oktatóanyag a forrás és a cél két különböző virtuális hálózatban való üzembe helyezését végzi el, csak az egyszerűség kedvéért.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Ehhez a cikkhez az Azure CLI 2.0.71 vagy újabb verziójára van szükség. Azure Cloud Shell használata esetén a legújabb verzió már telepítve van.

## <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

Hozzon létre egy erőforráscsoportot az [az group create](/cli/azure/group) paranccsal. Az Azure-erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat.

A következő példában létrehozunk egy **myResourceGroupNAT** nevű erőforráscsoportot a **eastus2** helyen:

```azurecli-interactive
  az group create \
    --name myResourceGroupNAT \
    --location eastus2
    
```

## <a name="create-the-nat-gateway"></a>NAT-átjáró létrehozása

### <a name="create-a-public-ip-address"></a>Hozzon létre egy nyilvános IP-címet

A nyilvános internethez való hozzáféréshez szüksége lesz egy vagy több nyilvános IP-címre a NAT-átjáró számára. Az [az Network Public-IP Create](/cli/azure/network/public-ip) paranccsal hozzon létre egy **myPublicIPsource** nevű nyilvános IP-cím-erőforrást a **myResourceGroupNAT**-ben.

```azurecli-interactive
  az network public-ip create \
  --resource-group myResourceGroupNAT \
  --name myPublicIPsource \
  --sku standard
  
```

### <a name="create-a-public-ip-prefix"></a>Nyilvános IP-előtag létrehozása

Használhat egy vagy több nyilvános IP-cím-erőforrást, nyilvános IP-előtagot vagy mindkettőt a NAT-átjáróval. Ehhez a forgatókönyvhöz egy nyilvános IP-előtag-erőforrást adunk hozzá, amely bemutatja a következőt:.   Az [az Network Public-IP előtag Create](/cli/azure/network/public-ip/prefix#az-network-public-ip-prefix-create) paranccsal hozzon létre egy **myPublicIPprefixsource** nevű nyilvános IP-előtag-erőforrást a **myResourceGroupNAT**.

```azurecli-interactive
  az network public-ip prefix create \
  --resource-group myResourceGroupNAT \
  --name myPublicIPprefixsource \
  --length 31
  
```

### <a name="create-a-nat-gateway-resource"></a>NAT-átjáró erőforrásának létrehozása

Ez a szakasz részletesen ismerteti, hogyan hozhatja létre és konfigurálhatja a NAT-szolgáltatás következő összetevőit a NAT-átjáró erőforrásának használatával:
  - Egy nyilvános IP-címkészlet és egy nyilvános IP-előtag, amelyet a NAT-átjáró erőforrása lefordított kimenő folyamatokhoz használ.
  - Módosítsa az üresjárati időkorlátot az alapértelmezett 4 perctől 10 percre.

Hozzon létre egy globális Azure NAT-átjárót az [az Network NAT Gateway Create](/cli/azure/network/nat) nevű **myNATgateway**. A parancs a nyilvános IP- **myPublicIP** és a nyilvános IP-előtag **myPublicIPprefix** is használja. A parancs az üresjárati időkorlátot 10 percre is módosítja.

```azurecli-interactive
  az network nat gateway create \
    --resource-group myResourceGroupNAT \
    --name myNATgateway \
    --public-ip-addresses myPublicIPsource \
    --public-ip-prefixes myPublicIPprefixsource \
    --idle-timeout 10       
    
  ```

Ezen a ponton a NAT-átjáró működik, és az összes hiányzó beállítással konfigurálható, hogy a virtuális hálózat mely alhálózatai használják azt.

## <a name="prepare-the-source-for-outbound-traffic"></a>A forrás előkészítése a kimenő forgalomhoz

Végigvezeti a teljes tesztkörnyezet beállításán. A NAT-átjáró ellenőrzéséhez nyílt forráskódú eszközök használatával állítson be egy tesztet. Kezdjük a forrással, amely a korábban létrehozott NAT-átjárót fogja használni.

### <a name="configure-virtual-network-for-source"></a>Virtuális hálózat konfigurálása a forráshoz

Mielőtt üzembe helyez egy virtuális GÉPET, és tesztelni tudja a NAT-átjárót, létre kell hoznia a virtuális hálózatot.

Hozzon létre egy **myVnetsource** nevű virtuális hálózatot egy **mySubnetsource** nevű alhálózattal a **myResourceGroupNAT** az [az Network Microsoft Azure Virtual Network Create](/cli/azure/network/vnet)paranccsal.  A virtuális hálózat IP-címe **192.168.0.0/16**. A virtuális hálózaton belüli alhálózat **192.168.0.0/24**.

```azurecli-interactive
  az network vnet create \
    --resource-group myResourceGroupNAT \
    --name myVnetsource \
    --address-prefix 192.168.0.0/16 \
    --subnet-name mySubnetsource \
    --subnet-prefix 192.168.0.0/24
    
```

### <a name="configure-nat-service-for-source-subnet"></a>NAT szolgáltatás konfigurálása a forrás-alhálózathoz

Konfigurálja a virtuális hálózati **myVnetsource** a forrás alhálózati **MYSUBNETSOURCE** egy adott NAT Gateway-erőforrás **myNATgateway** az [az Network Microsoft Azure Virtual Network subnet Update](/cli/azure/network/vnet/subnet)paranccsal. Ez a parancs aktiválja a NAT szolgáltatást a megadott alhálózaton.

```azurecli-interactive
    az network vnet subnet update \
    --resource-group myResourceGroupNAT \
    --vnet-name myVnetsource \
    --name mySubnetsource \
    --nat-gateway myNATgateway
    
```

A NAT szolgáltatás mostantól az összes internetes célhelyre irányuló kimenő forgalmat használja.  A UDR konfigurálása nem szükséges.

A NAT-átjáró teszteléséhez létre kell hoznia egy forrásoldali virtuális gépet.  Egy nyilvános IP-cím típusú erőforrást egy példány szintű nyilvános IP-címként rendelünk hozzá a virtuális géphez a külvilágtól való hozzáféréshez. Ez a címe csak a teszthez való hozzáféréshez használatos.  Bemutatjuk, hogy a NAT szolgáltatás milyen elsőbbséget élvez a többi kimenő lehetőséggel szemben.

Azt is megteheti, hogy nyilvános IP-cím nélkül hozza létre ezt a virtuális gépet, és egy olyan virtuális gépet hoz létre, amelyet Jumpbox használ nyilvános IP-cím nélkül.

### <a name="create-public-ip-for-source-vm"></a>Nyilvános IP-cím létrehozása a forrásként szolgáló virtuális gép számára

Létrehozunk egy nyilvános IP-címet, amely a forrás virtuális gép elérésére szolgál. Az [az Network Public-IP Create](/cli/azure/network/public-ip) paranccsal hozzon létre egy **myPublicIPsourceVM** nevű nyilvános IP-cím-erőforrást a **myResourceGroupNAT**-ben.

```azurecli-interactive
  az network public-ip create \
    --resource-group myResourceGroupNAT \
    --name myPublicIPsourceVM \
    --sku standard
    
```

### <a name="create-an-nsg-for-source-vm"></a>NSG létrehozása a forrásként szolgáló virtuális géphez

Mivel a standard nyilvános IP-címek alapértelmezetten "biztonságosak", létre kell hozni egy NSG, amely lehetővé teszi az SSH-hozzáférés bejövő hozzáférését.  Az Azure NAT szolgáltatás a flow irányának ismerete. Ezt a NSG nem fogja használni a rendszer a kimenő forgalomhoz, ha a NAT-átjáró ugyanazon az alhálózaton van konfigurálva. Az [az Network NSG Create](/cli/azure/network/nsg#az-network-nsg-create) paranccsal hozzon létre egy **myNSGsource** nevű NSG-erőforrást a **myResourceGroupNAT**-ben.

```azurecli-interactive
  az network nsg create \
    --resource-group myResourceGroupNAT \
    --name myNSGsource 
    
```

### <a name="expose-ssh-endpoint-on-source-vm"></a>SSH-végpont közzététele a forrásoldali virtuális gépen

Hozzunk létre egy szabályt a NSG a forrás virtuális géphez való SSH-hozzáféréshez. Az [az Network NSG Rule Create](/cli/azure/network/nsg/rule#az-network-nsg-rule-create) paranccsal hozzon létre egy **SSH** nevű NSG szabályt. Ez a szabály az erőforráscsoport **MyResourceGroupNAT** **myNSGsource** nevű NSG lesz létrehozva.

```azurecli-interactive
  az network nsg rule create \
    --resource-group myResourceGroupNAT \
    --nsg-name myNSGsource \
    --priority 100 \
    --name ssh \
    --description "SSH access" \
    --access allow \
    --protocol tcp \
    --direction inbound \
    --destination-port-ranges 22
    
```

### <a name="create-nic-for-source-vm"></a>Hálózati adapter létrehozása forrásként szolgáló virtuális géphez

Hozzon létre egy hálózati adaptert az az [Network NIC Create](/cli/azure/network/nic#az-network-nic-create) paranccsal, és társítsa a nyilvános IP-címet és a hálózati biztonsági csoportot. 

```azurecli-interactive
  az network nic create \
    --resource-group myResourceGroupNAT \
    --name myNicsource \
    --vnet-name myVnetsource \
    --subnet mySubnetsource \
    --public-ip-address myPublicIPSourceVM \
    --network-security-group myNSGsource
    
```

### <a name="create-a-source-vm"></a>Forrásként szolgáló virtuális gép létrehozása

Hozza létre a virtuális gépet az [az VM Create](/cli/azure/vm#az-vm-create)paranccsal.  A virtuális gép SSH-kulcsait generáljuk, és a titkos kulcsot később is használhatja.

```azurecli-interactive
  az vm create \
    --resource-group myResourceGroupNAT \
    --name myVMsource \
    --nics myNicsource \
    --image UbuntuLTS \
    --generate-ssh-keys \
    --no-wait
    
```

Amíg a parancs azonnal visszatér, eltarthat néhány percig, amíg a virtuális gép üzembe kerül.

## <a name="prepare-destination-for-outbound-traffic"></a>Cél előkészítése a kimenő forgalomhoz

Most létrehozunk egy célhelyet a NAT szolgáltatás által lefordított kimenő forgalomhoz, hogy tesztelni lehessen.

### <a name="configure-virtual-network-for-destination"></a>Virtuális hálózat konfigurálása célhelyként

 Létre kell hozni egy virtuális hálózatot, amelyben a cél virtuális gép lesz.  Ezek a parancsok ugyanazok a lépések, mint a forrásként szolgáló virtuális gép esetében, amely kis módosításokkal teszi elérhetővé a cél végpontot.

Hozzon létre egy **myVnetdestination** nevű virtuális hálózatot egy **mySubnetdestination** nevű alhálózattal a **myResourceGroupNAT** az [az Network Microsoft Azure Virtual Network Create](/cli/azure/network/vnet)paranccsal.  A virtuális hálózat IP-címe **192.168.0.0/16**. A virtuális hálózaton belüli alhálózat **192.168.0.0/24**.

```azurecli-interactive
  az network vnet create \
    --resource-group myResourceGroupNAT \
    --name myVnetdestination \
    --address-prefix 192.168.0.0/16 \
    --subnet-name mySubnetdestination \
    --subnet-prefix 192.168.0.0/24
    
```

### <a name="create-public-ip-for-destination-vm"></a>Nyilvános IP-cím létrehozása a cél virtuális géphez

Létrehozunk egy nyilvános IP-címet, amely a forrás virtuális gép elérésére szolgál. Az [az Network Public-IP Create](/cli/azure/network/public-ip) paranccsal hozzon létre egy **myPublicIPdestinationVM** nevű nyilvános IP-cím-erőforrást a **myResourceGroupNAT**-ben. 

```azurecli-interactive
  az network public-ip create \
  --resource-group myResourceGroupNAT \
  --name myPublicIPdestinationVM \
  --sku standard
  
```

### <a name="create-an-nsg-for-destination-vm"></a>NSG létrehozása a cél virtuális géphez

A standard nyilvános IP-címek alapértelmezés szerint "biztonságosak", létre kell hoznia egy NSG, amely engedélyezi a bejövő hozzáférést az SSH-hoz. Az Azure NAT szolgáltatás a flow irányának ismerete. Ezt a NSG nem fogja használni a rendszer a kimenő forgalomhoz, ha a NAT-átjáró ugyanazon az alhálózaton van konfigurálva. Az [az Network NSG Create](/cli/azure/network/nsg#az-network-nsg-create) paranccsal hozzon létre egy **myNSGdestination** nevű NSG-erőforrást a **myResourceGroupNAT**-ben.

```azurecli-interactive
    az network nsg create \
    --resource-group myResourceGroupNAT \
    --name myNSGdestination
    
```

### <a name="expose-ssh-endpoint-on-destination-vm"></a>SSH-végpont közzététele a cél virtuális gépen

Hozzunk létre egy szabályt a NSG a cél virtuális géphez való SSH-hozzáféréshez. Az [az Network NSG Rule Create](/cli/azure/network/nsg/rule#az-network-nsg-rule-create) paranccsal hozzon létre egy **SSH** nevű NSG szabályt. Ez a szabály az erőforráscsoport **MyResourceGroupNAT** **myNSGdestination** nevű NSG lesz létrehozva.

```azurecli-interactive
    az network nsg rule create \
    --resource-group myResourceGroupNAT \
    --nsg-name myNSGdestination \
    --priority 100 \
    --name ssh \
    --description "SSH access" \
    --access allow \
    --protocol tcp \
    --direction inbound \
    --destination-port-ranges 22
    
```

### <a name="expose-http-endpoint-on-destination-vm"></a>HTTP-végpont közzététele a cél virtuális gépen

Létre kell hozni egy szabályt a NSG a cél virtuális géphez való HTTP-hozzáféréshez. Az [az Network NSG Rule Create](/cli/azure/network/nsg/rule#az-network-nsg-rule-create) paranccsal hozzon létre egy **http** nevű NSG-SZABÁLYT a **myNSGdestination** nevű NSG a **myResourceGroupNAT**-ben.

```azurecli-interactive
    az network nsg rule create \
    --resource-group myResourceGroupNAT \
    --nsg-name myNSGdestination \
    --priority 101 \
    --name http \
    --description "HTTP access" \
    --access allow \
    --protocol tcp \
    --direction inbound \
    --destination-port-ranges 80
    
```

### <a name="create-nic-for-destination-vm"></a>Hálózati adapter létrehozása a cél virtuális géphez

Hozzon létre egy hálózati adaptert az az [Network NIC Create](/cli/azure/network/nic#az-network-nic-create) paranccsal, és társítsa a nyilvános IP- **myPublicIPdestinationVM** és a hálózati biztonsági csoport **myNSGdestination**. 

```azurecli-interactive
    az network nic create \
    --resource-group myResourceGroupNAT \
    --name myNicdestination \
    --vnet-name myVnetdestination \
    --subnet mySubnetdestination \
    --public-ip-address myPublicIPdestinationVM \
    --network-security-group myNSGdestination
    
```

### <a name="create-a-destination-vm"></a>Cél virtuális gép létrehozása

Hozza létre a virtuális gépet az [az VM Create](/cli/azure/vm#az-vm-create)paranccsal.  A virtuális gép SSH-kulcsait generáljuk, és a titkos kulcsot később is használhatja.

 ```azurecli-interactive
    az vm create \
    --resource-group myResourceGroupNAT \
    --name myVMdestination \
    --nics myNicdestination \
    --image UbuntuLTS \
    --generate-ssh-keys \
    --no-wait
    
```
Amíg a parancs azonnal visszatér, eltarthat néhány percig, amíg a virtuális gép üzembe kerül.

## <a name="prepare-a-web-server-and-test-payload-on-destination-vm"></a>Webkiszolgáló és tesztelési tartalom előkészítése a célként megadott virtuális gépen

Először fel kell deríteni a cél virtuális gép IP-címét.  A cél virtuális gép nyilvános IP-címének lekéréséhez használja az [az Network Public-IP show](/cli/azure/network/public-ip#az-network-public-ip-show)lehetőséget. 

```azurecli-interactive
  az network public-ip show \
    --resource-group myResourceGroupNAT \
    --name myPublicIPdestinationVM \
    --query [ipAddress] \
    --output tsv
    
``` 

>[!IMPORTANT]
>Másolja a nyilvános IP-címet, majd illessze be egy Jegyzettömbbe, hogy azt a következő lépésekben használhatja. Jelezze, hogy ez a cél virtuális gép.

### <a name="sign-in-to-destination-vm"></a>Bejelentkezés a cél virtuális gépre

Az SSH hitelesítő adatait az előző művelet során a Cloud Shell kell tárolnia.  Nyisson meg egy [Azure Cloud Shell](https://shell.azure.com) a böngészőben. Használja az előző lépésben lekért IP-címet az SSH-val a virtuális gépre. 

```bash
ssh <ip-address-destination>
```

Ha bejelentkezett, másolja és illessze be a következő parancsokat.  

```bash
sudo apt -y update && \
sudo apt -y upgrade && \
sudo apt -y install nginx && \
sudo ln -sf /dev/null /var/log/nginx/access.log && \
sudo touch /var/www/html/index.html && \
sudo rm /var/www/html/index.nginx-debian.html && \
sudo dd if=/dev/zero of=/var/www/html/100k bs=1024 count=100

```

Ezek a parancsok frissítik a virtuális gépet, telepítik az Nginx-et, és létrehozunk egy 100-es kilobájtos fájlt. Ezt a fájlt a rendszer a forrás virtuális gépről a NAT szolgáltatás használatával kéri le.

A célként megadott virtuális géppel zárjuk be az SSH-munkamenetet.

## <a name="prepare-test-on-source-vm"></a>Teszt előkészítése a forrás virtuális gépen

Először fel kell deríteni a forrás virtuális gép IP-címét.  A forrás virtuális gép nyilvános IP-címének lekéréséhez használja az [az Network Public-IP show](/cli/azure/network/public-ip#az-network-public-ip-show)lehetőséget. 

```azurecli-interactive
  az network public-ip show \
    --resource-group myResourceGroupNAT \
    --name myPublicIPsourceVM \
    --query [ipAddress] \
    --output tsv
    
``` 

>[!IMPORTANT]
>Másolja a nyilvános IP-címet, majd illessze be egy Jegyzettömbbe, hogy azt a következő lépésekben használhatja. Jelezze, hogy ez a forrás virtuális gép.

### <a name="sign-in-to-source-vm"></a>Bejelentkezés a forrás virtuális gépre

Az SSH hitelesítő adatait a rendszer a Cloud Shell tárolja. Nyisson meg egy új lapot a böngészőben [Azure Cloud Shellhoz](https://shell.azure.com) .  Használja az előző lépésben lekért IP-címet az SSH-val a virtuális gépre. 

```bash
ssh <ip-address-source>
```

Másolja és illessze be a következő parancsokat a NAT szolgáltatás tesztelésének előkészítéséhez.

```bash
sudo apt -y update && \
sudo apt -y upgrade && \
sudo apt install -y nload golang && \
echo 'export GOPATH=${HOME}/go' >> .bashrc && \
echo 'export PATH=${PATH}:${GOPATH}/bin' >> .bashrc && \
. ~/.bashrc &&
go get -u github.com/rakyll/hey

```

Ezzel a paranccsal frissítheti a virtuális gépet, telepítheti a go [-t, telepítheti a](https://github.com/rakyll/hey) githubról, és frissítheti a rendszerhéj-környezetet.

Most már készen áll a NAT szolgáltatás tesztelésére.

## <a name="validate-nat-service"></a>NAT szolgáltatás ellenőrzése

Ha bejelentkezett a forrás virtuális gépre, a **curl** és a **Hey** használatával is létrehozhat KÉRÉSEKET a cél IP-címhez.

Az 100-kilobájtos fájl beolvasásához használja a curlt.  Cserélje le az **\<ip-address-destination>** alábbi példában a korábban másolt cél IP-címet.  A **--output** paraméter azt jelzi, hogy a beolvasott fájl el lesz vetve.

```bash
curl http://<ip-address-destination>/100k --output /dev/null
```

A **Hey** használatával több kérelem is létrehozható. Ismét cserélje le **\<ip-address-destination>** a helyére a korábban másolt cél IP-címet.

```bash
hey -n 100 -c 10 -t 30 --disable-keepalive http://<ip-address-destination>/100k
```

Ezzel a paranccsal az 100-es kérések, 10 párhuzamosan, 30 másodperces időkorláttal fognak létrejönni. A TCP-kapcsolatok nem lesznek újra felhasználva.  Minden kérelem 100 KB-ot fog beolvasni.  A Futtatás végén a **Hey** jelentést készít arról, hogy milyen jól működik a NAT szolgáltatás.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, az az [Group delete](/cli/azure/group#az-group-delete) paranccsal eltávolíthatja az erőforráscsoportot és a benne található összes erőforrást.

```azurecli-interactive 
  az group delete --name myResourceGroupNAT
  
```

## <a name="next-steps"></a>Következő lépések
Ebben az oktatóanyagban létrehozott egy NAT-átjárót, létrehozta a forrás és a cél virtuális gépet, majd tesztelte a NAT-átjárót.

Tekintse át a Azure Monitor mérőszámait a NAT szolgáltatás működésének megtekintéséhez. Problémák diagnosztizálása, például az elérhető SNAT-portok erőforrás-kimerülése.  A SNAT-portok erőforrás-kimerülése könnyen kezelhető további nyilvános IP-címek vagy nyilvános IP-előtag-erőforrások hozzáadásával vagy mindkettővel.

- Tudnivalók a [Virtual Network NAT](./nat-overview.md) -ról
- További információ a [NAT-átjáró erőforrásáról](./nat-gateway-resource.md).
- Rövid útmutató a [NAT-átjáró erőforrásának Azure CLI](./quickstart-create-nat-gateway-cli.md)-vel történő üzembe helyezéséhez.
- Útmutató a NAT- [átjáró erőforrásának Azure PowerShell használatával](./quickstart-create-nat-gateway-powershell.md)történő üzembe helyezéséhez.
- Útmutató a NAT- [átjáró erőforrásának Azure Portal használatával](./quickstart-create-nat-gateway-portal.md)történő üzembe helyezéséhez.

> [!div class="nextstepaction"]
