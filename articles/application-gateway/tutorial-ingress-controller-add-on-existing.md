---
title: 'Oktatóanyag: Bejövő forgalomvezérlő-Add-On engedélyezése meglévő AKS-fürtön meglévő Azure Application Gateway'
description: Ebből az oktatóanyagból megtudhatja, hogyan engedélyezheti a Add-On-vezérlést meglévő AKS-fürtje számára egy meglévő Application Gateway
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: tutorial
ms.date: 03/02/2021
ms.author: caya
ms.openlocfilehash: 2fe615da256099c3135f607a7b6f8095bb93b442
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107772846"
---
# <a name="tutorial-enable-application-gateway-ingress-controller-add-on-for-an-existing-aks-cluster-with-an-existing-application-gateway"></a>Oktatóanyag: Bejövő Application Gateway vezérlő bővítmény engedélyezése meglévő AKS-fürtön meglévő Application Gateway

Az Azure CLI vagy a Portál használatával engedélyezheti az Application Gateway Bejövő forgalomvezérlő [(AGIC)](ingress-controller-overview.md) bővítményt egy meglévő [Azure Kubernetes Services- (AKS-) fürtön.](https://azure.microsoft.com/services/kubernetes-service/) Ebből az oktatóanyagból megtudhatja, hogyan használható az AGIC-bővítmény a Kubernetes-alkalmazás meglévő AKS-fürtön való elérhetővé helyezéséhez egy meglévő virtuális Application Gateway üzembe helyezett meglévő virtuális hálózaton keresztül. Először egy AKS-fürtöt hoz létre egy virtuális hálózatban, és egy Application Gateway egy külön virtuális hálózatban a meglévő erőforrások szimulálása érdekében. Ezután engedélyezi az AGIC-bővítményt, társviszonyt létesít a két virtuális hálózat között, és üzembe helyez egy mintaalkalmazást, amely az AGIC-bővítmény használatával a Application Gateway keresztül lesz elérhetővé téve. Ha az AGIC-bővítményt egy ugyanazon a virtuális hálózaton található meglévő Application Gateway- és meglévő AKS-fürtön engedélyezi, akkor kihagyhatja az alábbi társviszony-létesítés lépését. A bővítmény sokkal gyorsabban helyezheti üzembe az AGIC-t az AKS-fürtön, mint korábban a [Helmen](ingress-controller-overview.md#difference-between-helm-deployment-and-aks-add-on) keresztül, és teljes körűen felügyelt felhasználói élményt nyújt.  

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Erőforráscsoport létrehozása 
> * Új AKS-fürt létrehozása 
> * Új fiók Application Gateway 
> * Az AGIC-bővítmény engedélyezése a meglévő AKS-fürtön az Azure CLI-ben 
> * Az AGIC-bővítmény engedélyezése a meglévő AKS-fürtön a portálon keresztül 
> * Társviszony Application Gateway virtuális hálózat és az AKS-fürt virtuális hálózata között
> * Mintaalkalmazás üzembe helyezése AGIC használatával bejövő forgalomhoz az AKS-fürtön
> * Ellenőrizze, hogy az alkalmazás elérhető-e a Application Gateway

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

## <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

Az Azure-ban a kapcsolódó erőforrásokat egy erőforráscsoporthoz rendeli hozzá. Hozzon létre egy erőforráscsoportot [az az group create használatával.](/cli/azure/group#az_group_create) Az alábbi példa létrehoz egy *myResourceGroup* nevű erőforráscsoportot *a canadacentral* helyen (régióban). 

```azurecli-interactive
az group create --name myResourceGroup --location canadacentral
```

## <a name="deploy-a-new-aks-cluster"></a>Új AKS-fürt üzembe helyezése

Most egy új AKS-fürtöt fog üzembe helyezni, hogy szimulálja egy meglévő AKS-fürtöt, amelynek engedélyezni szeretné az AGIC-bővítményét.  

A következő példában egy *új, myCluster* nevű AKS-fürtöt fog üzembe helyezni a [létrehozott erőforráscsoportban](../aks/concepts-network.md#azure-cni-advanced-networking) Azure CNI és Managed [Identities](../aks/use-managed-identity.md) *használatával.*

```azurecli-interactive
az aks create -n myCluster -g myResourceGroup --network-plugin azure --enable-managed-identity 
```

A parancs további paramétereinek `az aks create` konfigurálásért látogasson el a [hivatkozásokra itt.](/cli/azure/aks#az_aks_create) 

## <a name="deploy-a-new-application-gateway"></a>Új virtuális gép Application Gateway 

Most üzembe fog helyezni egy új Application Gateway, hogy szimulálja, hogy egy meglévő Application Gateway szeretne használni az AKS-fürt, a *myCluster* forgalmának terheléselosztása érdekében. A név Application Gateway *myApplicationGateway* lesz, de először létre kell hoznia egy nyilvános IP-erőforrást. A neve *myPublicIp,* és egy *myVnet* nevű új virtuális hálózat 11.0.0.0/8 címtérbe, valamint egy mySubnet nevű, 11.1.0.0/16 címtérű alhálózatba, és telepítse az Application Gateway-t a *mySubnetben* a *myPublicIp használatával.*  

Ha AKS-fürtöt használ, Application Gateway külön virtuális hálózatokban található virtuális hálózatokhoz, a két virtuális hálózat címterei nem lehetnek átfedésben. Az AKS-fürt által üzembe helyezett alapértelmezett címtér a 10.0.0.0/8, ezért a Application Gateway virtuális hálózat címelőtagját a 11.0.0.0/8 címre állítva állítva. 

```azurecli-interactive
az network public-ip create -n myPublicIp -g MyResourceGroup --allocation-method Static --sku Standard
az network vnet create -n myVnet -g myResourceGroup --address-prefix 11.0.0.0/8 --subnet-name mySubnet --subnet-prefix 11.1.0.0/16 
az network application-gateway create -n myApplicationGateway -l canadacentral -g myResourceGroup --sku Standard_v2 --public-ip-address myPublicIp --vnet-name myVnet --subnet mySubnet
```

> [!NOTE]
> Application Gateway (AGIC) bővítmény csak a Application Gateway  v2 SKUs (Standard és WAF)  és a Application Gateway v1 SKUs-t támogatja. 

## <a name="enable-the-agic-add-on-in-existing-aks-cluster-through-azure-cli"></a>Az AGIC bővítmény engedélyezése meglévő AKS-fürtön az Azure CLI-ben 

Ha továbbra is használni szeretné az Azure CLI-t, továbbra is engedélyezheti az AGIC bővítményt a létrehozott AKS-fürtben *(myCluster)* és megadhatja az AGIC bővítményt a létrehozott Application Gateway *myApplicationGateway használatára.*

```azurecli-interactive
appgwId=$(az network application-gateway show -n myApplicationGateway -g myResourceGroup -o tsv --query "id") 
az aks enable-addons -n myCluster -g myResourceGroup -a ingress-appgw --appgw-id $appgwId
```

## <a name="enable-the-agic-add-on-in-existing-aks-cluster-through-portal"></a>Az AGIC-bővítmény engedélyezése meglévő AKS-fürtön a portálon keresztül 

Ha az AGIC-bővítmény engedélyezéséhez Azure Portal használni, lépjen a [ https://aka.ms/azure/portal/aks/agic) ](https://aka.ms/azure/portal/aks/agic) () webhelyre, és keresse meg az AKS-fürtöt a Portal hivatkozásán keresztül. Innen válassza az AKS-fürt Hálózat lapját. Egy bejövő Application Gateway vezérlő szakaszt fog látni, amely lehetővé teszi a bejövő vezérlő bővítményének engedélyezését vagy letiltását a portál felhasználói felületén. Jelölje be a "Bejövő forgalomvezérlő engedélyezése" jelölőnégyzetet, és Application Gateway a legördülő menüben a *myApplicationGateway* elemet. 

![Application Gateway bejövő forgalomvezérlő portálja](./media/tutorial-ingress-controller-add-on-existing/portal-ingress-controller-add-on.png)

## <a name="peer-the-two-virtual-networks-together&quot;></a>Társviszony létesítása a két virtuális hálózat között

Mivel az AKS-fürtöt a saját virtuális hálózatában, az Application Gateway-t pedig egy másik virtuális hálózatban helyeztünk üzembe, társviszonyt kell létesítenünk a két virtuális hálózat között ahhoz, hogy a Application Gateway felől a fürt podjaiba áramló forgalom el tudja végezni. A két virtuális hálózat közötti társviszony létesítéséhez az Azure CLI-parancs két külön futtatására van szükség, hogy a kapcsolat kétirányú legyen. Az első parancs társviszony-kapcsolatot hoz létre az Application Gateway és az AKS virtuális hálózat között; A második parancs létrehoz egy társviszony-kapcsolatot a másik irányban.

```azurecli-interactive
nodeResourceGroup=$(az aks show -n myCluster -g myResourceGroup -o tsv --query &quot;nodeResourceGroup")
aksVnetName=$(az network vnet list -g $nodeResourceGroup -o tsv --query "[0].name")

aksVnetId=$(az network vnet show -n $aksVnetName -g $nodeResourceGroup -o tsv --query "id")
az network vnet peering create -n AppGWtoAKSVnetPeering -g myResourceGroup --vnet-name myVnet --remote-vnet $aksVnetId --allow-vnet-access

appGWVnetId=$(az network vnet show -n myVnet -g myResourceGroup -o tsv --query "id")
az network vnet peering create -n AKStoAppGWVnetPeering -g $nodeResourceGroup --vnet-name $aksVnetName --remote-vnet $appGWVnetId --allow-vnet-access
```

## <a name="deploy-a-sample-application-using-agic"></a>Mintaalkalmazás üzembe helyezése az AGIC használatával 

Most üzembe fog helyezni egy mintaalkalmazást a létrehozott AKS-fürtön, amely az AGIC bővítményt fogja használni a bejövő forgalomhoz, és csatlakoztatja a Application Gateway az AKS-fürthöz. Először a parancs futtatásával kap hitelesítő adatokat az üzembe helyezett AKS-fürthöz. `az aks get-credentials` 

```azurecli-interactive
az aks get-credentials -n myCluster -g myResourceGroup
```

Miután létrehozta a létrehozott fürt hitelesítő adatait, futtassa a következő parancsot egy olyan mintaalkalmazás beállításához, amely AGIC-t használ a fürt bejövő forgalomához. Az AGIC frissíti a Application Gateway beállított halmazt a megfelelő útválasztási szabályokkal az üzembe helyezett új mintaalkalmazáshoz.  

```azurecli-interactive
kubectl apply -f https://raw.githubusercontent.com/Azure/application-gateway-kubernetes-ingress/master/docs/examples/aspnetapp.yaml 
```

## <a name="check-that-the-application-is-reachable"></a>Ellenőrizze, hogy az alkalmazás elérhető-e

Most, hogy Application Gateway be van állítva az AKS-fürtre való forgalom kiszolgálására, ellenőrizzük, hogy az alkalmazás elérhető-e. Először le kell kapnia a bejövő forgalom IP-címét. 

```azurecli-interactive
kubectl get ingress
```

Ellenőrizze, hogy a létrehozott mintaalkalmazás működik-e. Ezt a fenti parancs futtatásából kapott ip-címének Application Gateway, vagy ellenőrizze a következővel: `curl` . A frissítés le Application Gateway egy percet is igénybe vehet, így ha a Application Gateway még frissítés állapotban van a portálon, akkor hagyja, hogy befejeződik, mielőtt megpróbálja elérni az IP-címet. 

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, távolítsa el az erőforráscsoportot, az alkalmazásátjárót és az összes kapcsolódó erőforrást.

```azurecli-interactive
az group delete --name myResourceGroup 
```

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [További információ az AGIC bővítmény letiltásával kapcsolatban](./ingress-controller-disable-addon.md)
