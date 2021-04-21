---
title: Oktatóanyag – Virtuális gépek terheléselosztása magas rendelkezésre álláshoz
description: Ebből az oktatóanyagból megtudhatja, hogyan hozhat létre három Linux rendszerű virtuális gépen terheléselosztót az Azure CLI-vel egy magas rendelkezésre állású és biztonságos alkalmazáshoz
author: cynthn
ms.subservice: networking
ms.service: virtual-machines
ms.collection: linux
ms.devlang: azurecli
ms.topic: tutorial
ms.workload: infrastructure
ms.date: 04/20/2021
ms.author: cynthn
ms.custom: mvc, devx-track-js, devx-track-azurecli
ms.openlocfilehash: 191eb1338533cf1a5f81f4d04c5dfc6fd5cc569c
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2021
ms.locfileid: "107818745"
---
# <a name="tutorial-load-balance-vms-for-high-availability"></a>Oktatóanyag: Virtuális gépek terheléselosztása magas rendelkezésre álláshoz

A terheléselosztás magasabb szintű rendelkezésre állást biztosít, mivel a bejövő kérelmeket több virtuális gép között osztja szét. Ebben az oktatóanyagban megismerkedhet az Azure Load Balancer különböző összetevőivel, amelyek elosztják a forgalmat, és gondoskodnak a magas rendelkezésre állásról. Az alábbiak végrehajtásának módját ismerheti meg:

> [!div class="checklist"]
> * Terheléselosztó létrehozása
> * Állapotminta létrehozása
> * Forgalmi szabályok létrehozása
> * Alapszintű alkalmazás telepítése a cloud-init Node.js használatával
> * Virtuális gépek létrehozása és csatolása a terheléselosztáshoz
> * A terheléselosztás megtekintése művelet közben
> * Virtuális gépek hozzáadása és eltávolítása a terheléselosztási eszközből

Ez az oktatóanyag a cli-t használja [a Azure Cloud Shell,](../../cloud-shell/overview.md)amely folyamatosan frissül a legújabb verzióra. A kód Cloud Shell válassza a Try **it (Próbálja** ki) gombra a kódblokkok tetején.

Ha a parancssori felület helyi telepítését és használatát választja, akkor ehhez az oktatóanyaghoz az Azure CLI 2.0.30-as vagy újabb verziójára lesz szükség. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése]( /cli/azure/install-azure-cli).

## <a name="azure-load-balancer-overview"></a>Az Azure-terheléselosztók áttekintése

Az Azure-terheléselosztók 4. rétegbeli (TCP, UDP) terheléselosztók, amelyek magas rendelkezésre állást biztosítanak azáltal, hogy a bejövő forgalmat elosztják az ép virtuális gépek között. A terheléselosztó állapotmintája ez egyes virtuális gépek adott portjait monitorozza, és csak a működő virtuális gépekre terjeszt forgalmat.

Meg kell adnia egy előtérbeli IP-konfigurációt, amely egy vagy több nyilvános IP-címet tartalmaz. Ez az előtérbeli IP-konfiguráció lehetővé teszi, hogy a terheléselosztó és az alkalmazások elérhetők legyenek az interneten keresztül. 

A virtuális gépek a virtuális hálózati adapterkártyájuk (NIC) segítségével csatlakoznak a terheléselosztóhoz. Annak érdekében, hogy a forgalmat el lehessen osztani a virtuális gépek között, egy háttércímkészlet tartalmazza a terheléselosztóhoz csatlakoztatott NIC-k IP-címét.

A forgalom szabályozásához terheléselosztási szabályokat kell megadnia a virtuális gépekhez rendelt adott portokhoz és protokollokhoz.

Ha követte az előző oktatóanyagot, amelyben [virtuálisgép-méretezési csoportot hozott létre](tutorial-create-vmss.md), a rendszer létrehozott egy terheléselosztót is. A rendszer konfigurálta is az összetevőket a méretezési csoport részeként.


## <a name="create-azure-load-balancer"></a>Azure-terheléselosztó létrehozása
Ez a szakasz részletesen ismerteti a terheléselosztó egyes összetevőinek létrehozását és konfigurálását. Mielőtt létrehozhatna egy terheléselosztót, létre kell hoznia egy erőforráscsoportot az [az group create](/cli/azure/group) paranccsal. A következő példa létrehoz egy *myResourceGroupLoadBalancer* nevű erőforráscsoportot az *eastus* helyen:

```azurecli-interactive
az group create --name myResourceGroupLoadBalancer --location eastus
```

### <a name="create-a-public-ip-address"></a>Hozzon létre egy nyilvános IP-címet
Az alkalmazás internetes eléréséhez a terheléselosztónak nyilvános IP-címmel kell rendelkeznie. Hozzon létre egy nyilvános IP-címet az [az network public-ip create](/cli/azure/network/public-ip) paranccsal. Az alábbi példában létrejön egy *myPublicIP* nevű nyilvános IP-cím a *myResourceGroupLoadBalancer* erőforráscsoportban:

```azurecli-interactive
az network public-ip create \
    --resource-group myResourceGroupLoadBalancer \
    --name myPublicIP
```

### <a name="create-a-load-balancer"></a>Terheléselosztó létrehozása
Hozzon létre egy terheléselosztót az [az network lb create](/cli/azure/network/lb) paranccsal. Az alábbi példa létrehoz egy *myLoadBalancer* nevű terheléselosztót, és hozzárendeli a *myPublicIP* címet az előtérbeli IP-konfigurációhoz:

```azurecli-interactive
az network lb create \
    --resource-group myResourceGroupLoadBalancer \
    --name myLoadBalancer \
    --frontend-ip-name myFrontEndPool \
    --backend-pool-name myBackEndPool \
    --public-ip-address myPublicIP
```

### <a name="create-a-health-probe"></a>Állapotminta létrehozása
Ahhoz, hogy a terheléselosztó monitorozhassa az alkalmazás állapotát, állapotmintát kell használni. Az állapotminta dinamikusan hozzáadja a virtuális gépeket a terheléselosztó rotációjához vagy eltávolítja belőle azokat, az állapotellenőrzésekre adott válaszuk alapján. Alapértelmezés szerint a virtuális gépeket a rendszer akkor távolítja el a terheléselosztó elosztásából, ha egy 15 másodperces intervallumon belül két, egymást követő hiba jelentkezik. Az állapotmintákat az alkalmazás egy protokollja vagy adott állapotellenőrzési oldala alapján lehet létrehozni. 

Az alábbi példában egy TCP-mintavétel jön létre. Egyéni HTTP-mintavételt is létrehozhat részletesebb állapotellenőrzések elvégzéséhez. Egyéni HTTP-mintavétel használatakor létre kell hoznia az állapotellenőrzési oldalt, például: *healthcheck.js*. A mintavételnek a **HTTP 200 OK** választ kell visszaadnia ahhoz, hogy a terheléselosztó a gazdagépet a rotációban tartsa.

TCP-állapotminta létrehozásához használja az [az network lb probe create](/cli/azure/network/lb/probe) parancsot. Az alábbi példa egy *myHealthProbe* nevű állapotmintát hoz létre:

```azurecli-interactive
az network lb probe create \
    --resource-group myResourceGroupLoadBalancer \
    --lb-name myLoadBalancer \
    --name myHealthProbe \
    --protocol tcp \
    --port 80
```

### <a name="create-a-load-balancer-rule"></a>Terheléselosztási szabály létrehozása
A terheléselosztási szabállyal azt lehet megadni, hogy a rendszer hogyan ossza el a forgalmat a virtuális gépek között. Meg kell határoznia az előtérbeli IP-konfigurációt a bejövő forgalomhoz és a háttérbeli IP-készletet a forgalom fogadásához, valamint a szükséges forrás- és célportot. Ahhoz, hogy csak a megfelelő állapotú virtuális gépek fogadhassanak forgalmat, adja meg a használandó állapotmintát is.

Hozzon létre egy terheléselosztási szabályt az [az network lb rule create](/cli/azure/network/lb/rule) paranccsal. Az alábbi példa létrehoz egy *myLoadBalancerRule* nevű szabályt, a *myHealthProbe* állapotmintát használja, és elosztja a *80*-as port forgalmát:

```azurecli-interactive
az network lb rule create \
    --resource-group myResourceGroupLoadBalancer \
    --lb-name myLoadBalancer \
    --name myLoadBalancerRule \
    --protocol tcp \
    --frontend-port 80 \
    --backend-port 80 \
    --frontend-ip-name myFrontEndPool \
    --backend-pool-name myBackEndPool \
    --probe-name myHealthProbe
```


## <a name="configure-virtual-network"></a>Virtuális hálózat konfigurálása
Mielőtt üzembe helyezne néhány virtuális gépet és tesztelné az elosztót, hozza létre a támogató virtuális hálózati erőforrásokat. További információt a virtuális hálózatokról az [Azure-beli virtuális hálózatok kezelésével](tutorial-virtual-network.md) foglalkozó oktatóanyagban talál.

### <a name="create-network-resources"></a>Hálózati erőforrások létrehozása
Hozzon létre egy virtuális hálózatot az [az network vnet create](/cli/azure/network/vnet) paranccsal. Az alábbi példa létrehoz egy *mySubnet* nevű alhálózattal rendelkező *myVNet* nevű virtuális hálózatot:

```azurecli-interactive
az network vnet create \
    --resource-group myResourceGroupLoadBalancer \
    --name myVnet \
    --subnet-name mySubnet
```

Hálózati biztonsági csoport hozzáadásához használja az [az network nsg create](/cli/azure/network/nsg) parancsot. A következő példa a *myNetworkSecurityGroup* nevű hálózati biztonsági csoportot hozza létre:

```azurecli-interactive
az network nsg create \
    --resource-group myResourceGroupLoadBalancer \
    --name myNetworkSecurityGroup
```

Hozzon létre egy hálózati biztonsági csoportra vonatkozó szabályt az [az network nsg rule create](/cli/azure/network/nsg/rule) paranccsal. Az alábbi példa létrehoz egy *myNetworkSecurityGroupRule* nevű, hálózati biztonsági csoportra vonatkozó szabályt:

```azurecli-interactive
az network nsg rule create \
    --resource-group myResourceGroupLoadBalancer \
    --nsg-name myNetworkSecurityGroup \
    --name myNetworkSecurityGroupRule \
    --priority 1001 \
    --protocol tcp \
    --destination-port-range 80
```

Virtuális hálózati adapterek (NIC-k) az [az network nic create](/cli/azure/network/nic) paranccsal hozhatók létre. Az alábbi példa három virtuális NIC-t hoz létre. (Egy virtuális NIC-t minden virtuális géphez, amelyet létre fog hozni az alkalmazáshoz a következő lépések során). Bármikor létrehozhat további virtuális NIC-ket és virtuális gépeket, és hozzáadhatja őket a terheléselosztóhoz:

```azurecli
for i in `seq 1 3`; do
    az network nic create \
        --resource-group myResourceGroupLoadBalancer \
        --name myNic$i \
        --vnet-name myVnet \
        --subnet mySubnet \
        --network-security-group myNetworkSecurityGroup \
        --lb-name myLoadBalancer \
        --lb-address-pools myBackEndPool
done
```

Amikor létrejött mind a három virtuális NIC, folytassa a következő lépéssel.


## <a name="create-virtual-machines"></a>Virtuális gépek létrehozása

### <a name="create-cloud-init-config"></a>Cloud-init konfiguráció létrehozása
A [Linux virtuális gép testre szabása az első rendszerbetöltéskor](tutorial-automate-vm-deployment.md) című korábbi oktatóanyagból megtudhatta, hogyan automatizálható egy virtuális gép testreszabása a cloud-init használatával. Ugyanezzel a cloud-init konfigurációs fájllal telepítheti az NGINX-et, és futtathat egy egyszerű „Hello World” Node.js-alkalmazást a következő lépésben. Ha működés közben szeretné látni a terheléselosztót, az oktatóanyag végén hozzáférhet ehhez az egyszerű alkalmazáshoz egy webböngészőben.

Az aktuális parancshéjban hozzon létre egy *cloud-init.txt* nevű fájlt, és illessze bele a következő konfigurációt. Például hozza létre a fájlt a Cloud Shellben, és ne a helyi gépén. Írja be a `sensible-editor cloud-init.txt` parancsot a fájl létrehozásához és az elérhető szerkesztők listájának megtekintéséhez. Ügyeljen arra, hogy megfelelően másolja ki a teljes cloud-init-fájlt, különösen az első sort:

```yaml
#cloud-config
package_upgrade: true
packages:
  - nginx
  - nodejs
  - npm
write_files:
  - owner: www-data:www-data
  - path: /etc/nginx/sites-available/default
    content: |
      server {
        listen 80;
        location / {
          proxy_pass http://localhost:3000;
          proxy_http_version 1.1;
          proxy_set_header Upgrade $http_upgrade;
          proxy_set_header Connection keep-alive;
          proxy_set_header Host $host;
          proxy_cache_bypass $http_upgrade;
        }
      }
  - owner: azureuser:azureuser
  - path: /home/azureuser/myapp/index.js
    content: |
      var express = require('express')
      var app = express()
      var os = require('os');
      app.get('/', function (req, res) {
        res.send('Hello World from host ' + os.hostname() + '!')
      })
      app.listen(3000, function () {
        console.log('Hello world app listening on port 3000!')
      })
runcmd:
  - service nginx restart
  - cd "/home/azureuser/myapp"
  - npm init
  - npm install express -y
  - nodejs index.js
```

### <a name="create-virtual-machines"></a>Virtuális gépek létrehozása
Az alkalmazás magas rendelkezésre állásának növeléséhez helyezze a virtuális gépeket egy rendelkezésre állási csoportba. A rendelkezésre állási csoportokról további információt az előző, a [magas rendelkezésre állású virtuális gépek létrehozásával](tutorial-availability-sets.md) foglalkozó oktatóanyagban talál.

Hozzon létre egy rendelkezésre állási készletet [az az vm availability-set create gombra.](/cli/azure/vm/availability-set) Az alábbi példa egy *myAvailabilitySet* nevű rendelkezésre állási csoportot hoz létre:

```azurecli-interactive 
az vm availability-set create \
    --resource-group myResourceGroupLoadBalancer \
    --name myAvailabilitySet
```

Most már létrehozhatja a virtuális gépeket az [az vm create](/cli/azure/vm) paranccsal. Az alábbi példa három virtuális gépet hoz létre, illetve SSH-kulcsokat, ha azok még nem léteznek:

```azurecli
for i in `seq 1 3`; do
    az vm create \
        --resource-group myResourceGroupLoadBalancer \
        --name myVM$i \
        --availability-set myAvailabilitySet \
        --nics myNic$i \
        --image UbuntuLTS \
        --admin-username azureuser \
        --generate-ssh-keys \
        --custom-data cloud-init.txt \
        --no-wait
done
```

Néhány háttérfeladat azután is tovább fut, hogy az Azure CLI visszairányítja Önt a parancssorhoz. A `--no-wait` paraméter nem várja meg az összes feladat befejeződését. Eltarthat még néhány percig, amíg hozzáférhet az alkalmazáshoz. A terheléselosztó állapotmintája automatikusan észleli, amikor az alkalmazás fut az egyes virtuális gépeken. Mihelyt az alkalmazás fut, a terheléselosztási szabály megkezdi a forgalom elosztását.


## <a name="test-load-balancer"></a>Terheléselosztó tesztelése
Kérje le a terheléselosztó nyilvános IP-címét az [az network public-ip show](/cli/azure/network/public-ip) paranccsal. A következő példa a korábban létrehozott *myPublicIP* IP-címét kéri le:

```azurecli-interactive
az network public-ip show \
    --resource-group myResourceGroupLoadBalancer \
    --name myPublicIP \
    --query [ipAddress] \
    --output tsv
```

A nyilvános IP-címet beírhatja egy böngészőbe. Ne feledje: néhány percet igénybe vesz, hogy a virtuális gépek készen álljanak arra, hogy a terheléselosztó elkezdje elosztani köztük a forgalmat. Amikor készek, megjelenik az alkalmazás, amely tartalmazza annak a virtuális gépnek az eszköznevét, amelyhez a terheléselosztó forgalmat irányított, ahogy az alábbi példában is látható:

![Node.js-alkalmazás futtatása](./media/tutorial-load-balancer/running-nodejs-app.png)

Annak megtekintéséhez, hogyan osztja el a terheléselosztó az alkalmazást futtató három virtuális gép között a forgalmat, kényszerítheti a webböngésző frissítését.


## <a name="add-and-remove-vms"></a>Virtuális gépek hozzáadása és eltávolítása
Előfordulhat, hogy karbantartás kell végeznie az alkalmazást futtató virtuális gépeken (például operációsrendszer-frissítést kell telepítenie). Az alkalmazás megnövekedett forgalmának kezeléséhez szükség lehet további virtuális gépek hozzáadására. Ez a szakasz bemutatja, hogyan távolíthat el egy virtuális gépet a terheléselosztóból, vagy hogyan adhat hozzá virtuális gépeket.

### <a name="remove-a-vm-from-the-load-balancer"></a>Virtuális gép eltávolítása a terheléselosztóból
A virtuális gépet az [az network nic ip-config address-pool remove](/cli/azure/network/nic/ip-config/address-pool) paranccsal távolíthatja el a háttércímkészletből. A következő példa eltávolítja a **myVM2** virtuális NIC-jét a *myLoadBalancer* elemből:

```azurecli-interactive
az network nic ip-config address-pool remove \
    --resource-group myResourceGroupLoadBalancer \
    --nic-name myNic2 \
    --ip-config-name ipConfig1 \
    --lb-name myLoadBalancer \
    --address-pool myBackEndPool 
```

Annak megtekintéséhez, hogyan osztja el a terheléselosztó az alkalmazást futtató megmaradt kettő virtuális gép között a forgalmat, kényszerítheti a webböngésző frissítését. Most már elvégezheti a virtuális gépen a szükséges karbantartást, például telepítheti az operációs rendszer frissítéseit, vagy újraindíthatja a virtuális gépet.

A terheléselosztóhoz csatlakozó, virtuális NIC-kkel rendelkező virtuális gépek listájának megtekintéséhez használja az [az network lb address-pool show](/cli/azure/network/lb/address-pool) parancsot. A következőképpen kérheti le és szűrhet rá a virtuális NIC-k azonosítójára:

```azurecli-interactive
az network lb address-pool show \
    --resource-group myResourceGroupLoadBalancer \
    --lb-name myLoadBalancer \
    --name myBackEndPool \
    --query backendIpConfigurations \
    --output tsv | cut -f4
```

A kimenet a következő példához hasonló, amelyben látható, hogy a 2. virtuális gép virtuális NIC-je már nem része a háttércímkészletnek:

```output
/subscriptions/<guid>/resourceGroups/myResourceGroupLoadBalancer/providers/Microsoft.Network/networkInterfaces/myNic1/ipConfigurations/ipconfig1
/subscriptions/<guid>/resourceGroups/myResourceGroupLoadBalancer/providers/Microsoft.Network/networkInterfaces/myNic3/ipConfigurations/ipconfig1
```

### <a name="add-a-vm-to-the-load-balancer"></a>Virtuális gép hozzáadása a terheléselosztóhoz
A virtuális gép karbantartásának elvégzése után, vagy ha bővítenie kell a kapacitást, az [az network nic ip-config address-pool add](/cli/azure/network/nic/ip-config/address-pool) paranccsal adhat virtuális gépet a háttércímkészlethez. A következő példa hozzáadja a **myVM2** virtuális NIC-jét a *myLoadBalancer* elemhez:

```azurecli-interactive
az network nic ip-config address-pool add \
    --resource-group myResourceGroupLoadBalancer \
    --nic-name myNic2 \
    --ip-config-name ipConfig1 \
    --lb-name myLoadBalancer \
    --address-pool myBackEndPool
```

Az előző lépésben szereplő [az network lb address-pool show](/cli/azure/network/lb/address-pool) paranccsal ellenőrizheti, hogy a virtuális NIC csatlakozik-e a háttércímkészlethez.


## <a name="next-steps"></a>Következő lépések
Ebben az oktatóanyagban létrehozott egy terheléselosztót, majd virtuális gépeket csatolt hozzá. Megtanulta végrehajtani az alábbi műveleteket:

> [!div class="checklist"]
> * Azure-terheléselosztó létrehozása
> * Terheléselosztó állapotmintájának létrehozása
> * Terheléselosztó forgalmára vonatkozó szabályok létrehozása
> * Alapszintű Node.js-alkalmazás létrehozása a cloud-init használatával
> * Virtuális gépek létrehozása és terheléselosztóhoz csatolása
> * Terheléselosztó megtekintése működés közben
> * Virtuális gépek hozzáadása a terheléselosztóhoz és eltávolításuk a terheléselosztóból

Folytassa a következő oktatóanyaggal, ha többet szeretne megtudni az Azure-beli virtuális hálózati összetevőkről.

> [!div class="nextstepaction"]
> [Virtuális gépek és virtuális hálózatok kezelése](tutorial-virtual-network.md)
