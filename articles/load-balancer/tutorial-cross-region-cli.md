---
title: 'Oktatóanyag: Régiók közötti terheléselosztás létrehozása az Azure CLI használatával'
titleSuffix: Azure Load Balancer
description: Az oktatóanyag első lépésekben régiók közötti virtuális gépeket helyez Azure Load Balancer Azure CLI használatával.
author: asudbring
ms.author: allensu
ms.service: load-balancer
ms.topic: tutorial
ms.date: 03/04/2021
ms.openlocfilehash: ca4134ff25dc9915f256b5a7bdd9404021b60a8e
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107791912"
---
# <a name="tutorial-create-a-cross-region-azure-load-balancer-using-azure-cli"></a>Oktatóanyag: Régiók közötti Azure Load Balancer létrehozása az Azure CLI használatával

A régiók közötti terheléselosztás biztosítja, hogy a szolgáltatás globálisan több Azure-régióban is elérhető. Ha az egyik régió meghibásodik, a forgalom a legközelebbi megfelelő állapotú regionális terheléselosztáshoz lesz irányítva.  

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Régiók közötti terheléselosztás létrehozása.
> * Hozzon létre egy terheléselosztó-szabályt.
> * Hozzon létre egy két regionális terheléselosztást tartalmazó háttérkészletet.
> * A terheléselosztás tesztelése.

Ha nem rendelkezik Azure-előfizetéssel, kezdés előtt hozzon létre egy [ingyenes](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) fiókot.

## <a name="prerequisites"></a>Előfeltételek

- Azure-előfizetés.
- Két **standard** termékváltozat az Azure Load Balancer két különböző Azure-régióban üzembe helyezett háttérkészletekkel.
    - További információ regionális standard terheléselosztás létrehozásáról és virtuális gépek háttérkészletek számára való létrehozásáról: Rövid útmutató: Nyilvános terheléselosztás létrehozása a virtuális gépek Terheléselosztáshoz az [Azure CLI használatával.](quickstart-load-balancer-standard-public-cli.md)
        - Minden régióban fűzheti hozzá a terheléselosztások és virtuális gépek nevét egy **-R1 és** **-R2 egymáshoz.** 
- Az Azure CLI helyileg vagy helyileg Azure Cloud Shell.

Ha a CLI helyi telepítését és használatát választja, akkor ehhez a rövid útmutatóhoz az Azure CLI 2.0.28-as vagy újabb verziójára lesz szükség. A verzió megkereséséhez futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne, olvassa el [az Azure CLI telepítését]( /cli/azure/install-azure-cli) ismertető cikket.

## <a name="sign-in-to-azure-cli"></a>Bejelentkezés az Azure CLI-be

Jelentkezzen be az Azure CLI-be:

```azurecli-interactive
az login
```

## <a name="create-cross-region-load-balancer"></a>Régiók közötti terheléselosztás létrehozása

Ebben a szakaszban egy régiók közötti terheléselosztási, nyilvános IP-címet és terheléselosztási szabályt fog létrehozni.

### <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

Az Azure-erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat.

Hozzon létre egy erőforráscsoportot [az az group create gombra:](/cli/azure/group#az_group_create)

* A **neve myResourceGroupLB-CR.**
* A **westus helyen.**

```azurecli-interactive
  az group create \
    --name myResourceGroupLB-CR \
    --location westus
```

### <a name="create-the-load-balancer-resource"></a>A terheléselosztási erőforrás létrehozása

Hozzon létre egy régiók közötti terheléselosztást [az az network cross-region-lb create segítségével:](/cli/azure/network/cross-region-lb#az_network_cross_region_lb_create)

* A **neve myLoadBalancer-CR.**
* Egy **myFrontEnd-CR nevű előtérben található készlet.**
* Egy **myBackEndPool-CR** nevű háttérkészlet.

```azurecli-interactive
  az network cross-region-lb create \
    --name myLoadBalancer-CR \
    --resource-group myResourceGroupLB-CR \
    --frontend-ip-name myFrontEnd-CR \
    --backend-pool-name myBackEndPool-CR     
```

### <a name="create-the-load-balancer-rule"></a>A terheléselosztási szabály létrehozása

A terheléselosztási szabályok a következőt határozzák meg:

* A bejövő forgalom előtere IP-konfigurációja.
* A forgalom fogadására a háttér-IP-készlet.
* A szükséges forrás- és célport. 

Hozzon létre egy terheléselosztási szabályt [az az network cross-region-lb rule create segítségével:](/cli/azure/network/cross-region-lb/rule#az_network_cross_region_lb_rule_create)

* **MyHTTPRule-CR nevű**
* A **myFrontEnd-CR** előterekészlet **80-as** portján figyel.
* Elosztott terhelésű hálózati forgalom küldése a **myBackEndPool-CR** háttércímkészletbe a **80-as port használatával.** 
* TCP **protokoll**.

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

Ebben a szakaszban két regionális standard terheléselosztást fog hozzáadni a régiók közötti terheléselosztás háttérkészlethez.

> [!IMPORTANT]
> A lépések befejezéséhez győződjön meg arról, hogy két, háttérkészletekkel bíró regionális terheléselosztást helyezett üzembe az előfizetésében.  További információkért lásd: Rövid útmutató: Nyilvános terheléselosztás létrehozása a virtuális gépek terhelésének az Azure CLI használatával **[való terheléselosztása érdekében.](quickstart-load-balancer-standard-public-cli.md)**

### <a name="add-the-regional-frontends-to-load-balancer"></a>A regionális előterek hozzáadása a terheléselosztáshoz

Ebben a szakaszban két regionális terheléselosztási frontend erőforrás-értékét fogja változókba venni.  Ezután a változók használatával hozzáadja az előtereket a régiók közötti terheléselosztás háttércímkészletének.

Az erőforrás-idok lekérése az [az network lb frontend-ip show val.](/cli/azure/network/lb/frontend-ip#az_network_lb_frontend_ip_show)

Az [az network cross-region-lb address-pool add](/cli/azure/network/cross-region-lb/address-pool/address#az_network_cross_region_lb_address_pool_address_add) használatával adja hozzá a régiók közötti terheléselosztás háttérkészletének változóiban elhelyezett előtereket:

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

Ebben a szakaszban a régiók közötti terheléselosztást fogja tesztelni. Egy webböngészőben fog csatlakozni a nyilvános IP-címhez.  Leállítja az egyik regionális terheléselosztási háttérkészlet virtuális gépeit, és megfigyeli a feladatátvételt.

1. A terheléselosztás nyilvános IP-címének leához használja [az az network public-ip show címet:](/cli/azure/network/public-ip#az_network_public_ip_show)

    ```azurecli-interactive
      az network public-ip show \
        --resource-group myResourceGroupLB-CR \
        --name PublicIPmyLoadBalancer-CR \
        --query ipAddress \
        --output tsv
    ```
2. Másolja a nyilvános IP-címet, majd illessze be a böngésző címsorába. Az IIS-webkiszolgáló alapértelmezett oldala jelenik meg a böngészőben.

3. Állítsa le az egyik regionális terheléselosztás háttérkészletében található virtuális gépeket.

4. Frissítse a webböngészőt, és figyelje meg a kapcsolat feladatátvételét a másik regionális terheléseltöltővel.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, az [az group delete paranccsal](/cli/azure/group#az_group_delete) távolítsa el az erőforráscsoportot, a terheléselosztást és az összes kapcsolódó erőforrást.

```azurecli-interactive
  az group delete \
    --name myResourceGroupLB-CR
```

## <a name="next-steps"></a>További lépések

Az oktatóanyag során az alábbi lépéseket fogja végrehajtani:

* Létrehozott egy régiók közötti terheléselosztást.
* Létrehozott egy terheléselosztási szabályt.
* Regionális terheléselosztások hozzáadva a régiók közötti terheléselosztás háttérkészletében.
* Tesztelte a terheléseltöltőt.

A következő cikkből megtudhatja, hogyan...
> [!div class="nextstepaction"]
> [Virtuális gépek terheléselosztása rendelkezésre állási zónák között](tutorial-load-balancer-standard-public-zone-redundant-portal.md)
