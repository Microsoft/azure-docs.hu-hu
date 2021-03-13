---
title: 'Oktatóanyag: régiók közötti terheléselosztó létrehozása az Azure CLI-vel'
titleSuffix: Azure Load Balancer
description: Ismerkedjen meg ezzel az Oktatóanyaggal, és telepítsen egy régiók közötti Azure Load Balancer az Azure CLI használatával.
author: asudbring
ms.author: allensu
ms.service: load-balancer
ms.topic: tutorial
ms.date: 03/04/2021
ms.openlocfilehash: 83efb428a94d49b77ecd923d4868afe034374b5f
ms.sourcegitcommit: 94c3c1be6bc17403adbb2bab6bbaf4a717a66009
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/12/2021
ms.locfileid: "103225183"
---
# <a name="tutorial-create-a-cross-region-azure-load-balancer-using-azure-cli"></a>Oktatóanyag: régiók közötti Azure Load Balancer létrehozása az Azure CLI-vel

A régiók közötti terheléselosztó biztosítja, hogy a szolgáltatások globálisan elérhetők legyenek több Azure-régió között. Ha az egyik régió meghibásodik, a rendszer átirányítja a forgalmat a legközelebbi, legközelebb egészséges regionális Load balancerbe.  

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Hozza létre a régiók közötti Load balancert.
> * Hozzon létre egy terheléselosztó-szabályt.
> * Hozzon létre két regionális terheléselosztó-t tartalmazó háttér-készletet.
> * A terheléselosztó tesztelése.

Ha nem rendelkezik Azure-előfizetéssel, a Kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) .

## <a name="prerequisites"></a>Előfeltételek

- Azure-előfizetés.
- Két különböző Azure-régióban üzembe helyezett backend-készletekkel rendelkező **standard** SKU Azure Load Balancer.
    - A regionális standard Load Balancer és a backend-készletek virtuális gépei létrehozásával kapcsolatos információkért lásd [: rövid útmutató: nyilvános terheléselosztó létrehozása a virtuális gépek terheléselosztásához az Azure CLI használatával](quickstart-load-balancer-standard-public-cli.md).
        - Fűzze hozzá az egyes régiókban található terheléselosztó és virtuális gépek nevét az **-R1** és **-R2** értékkel. 
- Az Azure CLI helyileg vagy Azure Cloud Shell van telepítve.

Ha a parancssori felület helyi telepítését és használatát választja, akkor ehhez a rövid útmutatóhoz az Azure CLI 2.0.28 verziójára vagy újabb verziójára van szükség. A verzió megkereséséhez futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne, olvassa el [az Azure CLI telepítését]( /cli/azure/install-azure-cli) ismertető cikket.

## <a name="sign-in-to-azure-cli"></a>Bejelentkezés az Azure CLI-be

Jelentkezzen be az Azure CLI-be:

```azurecli-interactive
az login
```

## <a name="create-cross-region-load-balancer"></a>Régiók közötti Load Balancer létrehozása

Ebben a szakaszban egy régiók közötti terheléselosztó, nyilvános IP-cím és terheléselosztási szabály jön létre.

### <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

Az Azure-erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat.

Hozzon létre egy erőforráscsoportot az [az Group Create](/cli/azure/group#az-group-create)paranccsal:

* **Myresourcegrouplb erőforráscsoportban-CR** névvel ellátott.
* A **westus** helyen.

```azurecli-interactive
  az group create \
    --name myResourceGroupLB-CR \
    --location westus
```

### <a name="create-the-load-balancer-resource"></a>A terheléselosztó erőforrásának létrehozása

Régiók közötti terheléselosztó létrehozása az [az Network Cross-region-LB Create](/cli/azure/network/cross-region-lb#az_network_cross_region_lb_create):

* **MyLoadBalancer-CR** névvel ellátott.
* Egy **myFrontEnd-CR** nevű frontend-készlet.
* Egy **myBackEndPool-CR** nevű háttér-készlet.

```azurecli-interactive
  az network cross-region-lb create \
    --name myLoadBalancer-CR \
    --resource-group myResourceGroupLB-CR \
    --frontend-ip-name myFrontEnd-CR \
    --backend-pool-name myBackEndPool-CR     
```

### <a name="create-the-load-balancer-rule"></a>A terheléselosztási szabály létrehozása

A terheléselosztó szabálya az alábbiakat határozza meg:

* A bejövő forgalom előtérbeli IP-konfigurációja.
* A háttérbeli IP-készlet a forgalom fogadásához.
* A szükséges forrás-és célport. 

Terheléselosztó-szabály létrehozása az [az Network Cross-region-LB Rule Create](/cli/azure/network/cross-region-lb/rule#az_network_cross_region_lb_rule_create):

* Elnevezett **: myhttprule-CR**
* Hallgassa meg a 80-es **portot** a **myFrontEnd-CR** felületi készletben.
* Elosztott terhelésű hálózati forgalom küldése a háttérbeli címkészlet **myBackEndPool-CR** használatával a **80-es porton** keresztül. 
* **TCP** protokoll.

```azurecli-interactive
  az network cross-region-lb rule create \
    --backend-port 80 \
    --frontend-port 80 \
    --lb-name myLoadBalancer-CR \
    --name myHTTPRule-CR \
    --protocol tcp \
    --resource-group myResourceGroupLB-CR \
    --backend-pool-name myBackEndPool-CR \
    --frontend-ip-name myFrontEnd-CR
```

## <a name="create-backend-pool"></a>Háttérkészlet létrehozása

Ebben a szakaszban két regionális standard Load balancert fog hozzáadni a régiók közötti terheléselosztó háttér-készletéhez.

> [!IMPORTANT]
> A lépések elvégzéséhez győződjön meg arról, hogy az előfizetésében két regionális terheléselosztó van telepítve a háttér-készletek használatával.  További információ: gyors üzembe helyezés – **[nyilvános terheléselosztó létrehozása a virtuális gépek terheléselosztásához az Azure CLI használatával](quickstart-load-balancer-standard-public-cli.md)**.

### <a name="add-the-regional-frontends-to-load-balancer"></a>A regionális frontendek hozzáadása a Load Balancerhez

Ebben a szakaszban a két regionális terheléselosztó felületének erőforrás-azonosítóit kell változókba helyezni.  Ezután a változók használatával adja hozzá a frontendeket a régiók közötti terheléselosztó háttérbeli címkészlet.

Az erőforrás-azonosítók lekérése az [az Network LB frontend-IP show](/cli/azure/network/lb/frontend-ip#az_network_lb_frontend_ip_show)paranccsal.

Használja az [az Network Cross-region-LB-címkészlet címe Hozzáadás](/cli/azure/network/cross-region-lb/address-pool/address#az_network_cross_region_lb_address_pool_address_add) lehetőséget, hogy hozzáadja a változókban a régiók közötti Load Balancer háttér-készletében elhelyezett előtérbeli felületeket:

```azurecli-interactive
  region1id=$(az network lb frontend-ip show \
    --lb-name myLoadBalancer-R1 \
    --name myFrontEnd-R1 \
    --resource-group CreatePubLBQS-rg-r1 \
    --query id \
    --output tsv)

  az network cross-region-lb address-pool address add \
    --frontend-ip-address $region1id \
    --lb-name myLoadBalancer-CR \
    --name myFrontEnd-R1 \
    --pool-name myBackEndPool-CR \
    --resource-group myResourceGroupLB-CR

  region2id=$(az network lb frontend-ip show \
    --lb-name myLoadBalancer-R2 \
    --name myFrontEnd-R2 \
    --resource-group CreatePubLBQS-rg-r2 \
    --query id \
    --output tsv)
  
  az network cross-region-lb address-pool address add \
    --frontend-ip-address $region2id \
    --lb-name myLoadBalancer-CR \
    --name myFrontEnd-R2 \
    --pool-name myBackEndPool-CR \
    --resource-group myResourceGroupLB-CR
```

## <a name="test-the-load-balancer"></a>A terheléselosztó tesztelése

Ebben a szakaszban tesztelni fogja a régiók közötti Load balancert. A nyilvános IP-címhez egy böngészőben fog csatlakozni.  A virtuális gépeket a regionális terheléselosztó backend-készletek egyikében állíthatja le, és megfigyelheti a feladatátvételt.

1. A terheléselosztó nyilvános IP-címének lekéréséhez használja az [az Network Public-IP show](/cli/azure/network/public-ip#az-network-public-ip-show):

    ```azurecli-interactive
      az network public-ip show \
        --resource-group myResourceGroupLB-CR \
        --name PublicIPmyLoadBalancer-CR \
        --query ipAddress \
        --output tsv
    ```
2. Másolja a nyilvános IP-címet, majd illessze be a böngésző címsorába. Az IIS-webkiszolgáló alapértelmezett oldala jelenik meg a böngészőben.

3. Állítsa le a virtuális gépeket az egyik regionális terheléselosztó háttér-készletében.

4. Frissítse a webböngészőt, és figyelje meg a másik regionális terheléselosztó kapcsolatának feladatátvételét.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, az az [Group delete](/cli/azure/group#az-group-delete) paranccsal távolítsa el az erőforráscsoportot, a Load balancert és az összes kapcsolódó erőforrást.

```azurecli-interactive
  az group delete \
    --name myResourceGroupLB-CR
```

## <a name="next-steps"></a>További lépések

Az oktatóanyag során az alábbi lépéseket fogja végrehajtani:

* Létrehozta a régiók közötti Load balancert.
* Létrehozott egy terheléselosztási szabályt.
* Regionális terheléselosztó hozzáadása a régiók közötti terheléselosztó háttér-készletéhez.
* Tesztelte a terheléselosztó.

A következő cikkből megtudhatja, hogyan...
> [!div class="nextstepaction"]
> [Virtuális gépek terheléselosztása rendelkezésre állási zónák között](tutorial-load-balancer-standard-public-zone-redundant-portal.md)
