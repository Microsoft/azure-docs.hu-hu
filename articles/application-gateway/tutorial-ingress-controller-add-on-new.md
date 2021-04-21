---
title: 'Oktatóanyag: A bejövő forgalomvezérlő bővítmény engedélyezése egy új AKS-fürtön egy új Azure Application Gateway'
description: Ebből az oktatóanyagból megtudhatja, hogyan engedélyezheti a bejövő forgalomvezérlő bővítményt az új AKS-fürthöz egy új Application Gateway példányon.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: tutorial
ms.date: 03/02/2021
ms.author: caya
ms.openlocfilehash: aad57c75481230db16a63aec7fb04fc5987ae8f0
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107772804"
---
# <a name="tutorial-enable-the-ingress-controller-add-on-for-a-new-aks-cluster-with-a-new-application-gateway-instance"></a>Oktatóanyag: A bejövő forgalomvezérlő bővítmény engedélyezése egy új AKS-fürthöz egy új Application Gateway példányon

Az Azure CLI használatával engedélyezheti az Application Gateway Bejövő forgalomvezérlő [(AGIC)](ingress-controller-overview.md) bővítményt egy új [Azure Kubernetes Services- (AKS-) fürthöz.](https://azure.microsoft.com/services/kubernetes-service/)

Ebben az oktatóanyagban egy AKS-fürtöt fog létrehozni engedélyezett AGIC-bővítménysel. A fürt létrehozása automatikusan létrehoz egy Azure Application Gateway használni. Ezután üzembe fog helyezni egy mintaalkalmazást, amely a bővítmény használatával teszi elérhetővé az alkalmazást a Application Gateway. 

A bővítmény sokkal gyorsabban helyezheti üzembe az AGIC-t az AKS-fürtön, mint [korábban a Helmen keresztül.](ingress-controller-overview.md#difference-between-helm-deployment-and-aks-add-on) Teljes körűen felügyelt felhasználói élményt is nyújt.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Hozzon létre egy erőforráscsoportot. 
> * Hozzon létre egy új AKS-fürtöt engedélyezett AGIC-bővítménysel.
> * Mintaalkalmazás üzembe helyezése AGIC használatával az AKS-fürt bejövő forgalomhoz való használatával.
> * Ellenőrizze, hogy az alkalmazás elérhető-e Application Gateway.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

## <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

Az Azure-ban a kapcsolódó erőforrásokat egy erőforráscsoporthoz rendeli. Hozzon létre egy erőforráscsoportot [az az group create használatával.](/cli/azure/group#az_group_create) Az alábbi példa létrehoz egy *myResourceGroup* nevű erőforráscsoportot *a canadacentral* helyen (régióban): 

```azurecli-interactive
az group create --name myResourceGroup --location canadacentral
```

## <a name="deploy-an-aks-cluster-with-the-add-on-enabled"></a>AKS-fürt üzembe helyezése engedélyezett bővítményekkel

Most üzembe fog helyezni egy új AKS-fürtöt, engedélyezett AGIC-bővítménysel. Ha nem ad meg meglévő Application Gateway-példányt a folyamathoz, automatikusan létrehozunk és beállítunk egy új Application Gateway-példányt az AKS-fürt forgalmának kiszolgálására.  

> [!NOTE]
> A Application Gateway bejövő forgalomvezérlő bővítmény csak a *Application Gateway* v2 SKUs (Standard és  WAF) és a Application Gateway v1 SKUs-t támogatja. Amikor új virtuálispéldányt helyez üzembe Application Gateway AGIC-bővítményen keresztül, csak egy új Application Gateway Standard_v2 helyezhet üzembe. Ha engedélyezni szeretné a bővítményt egy Application Gateway WAF_v2 termékváltozathoz, használja az alábbi módszerek egyikét:
>
> - Engedélyezze a WAF-Application Gateway a portálon keresztül. 
> - Először hozza WAF_v2 Application Gateway, majd kövesse az [AGIC bővítmény meglévő AKS-fürtön](tutorial-ingress-controller-add-on-existing.md)és meglévő Application Gateway engedélyezésére vonatkozó utasításokat. 

A következő példában egy új, *myCluster* nevű AKS-fürtöt fog üzembe helyezni Azure CNI és felügyelt [](../aks/concepts-network.md#azure-cni-advanced-networking) [identitások használatával.](../aks/use-managed-identity.md) Az AGIC-bővítmény engedélyezve lesz a létrehozott erőforráscsoportban, a *myResourceGroup erőforráscsoportban.* 

Ha egy új AKS-fürtöt úgy helyez üzembe, hogy az AGIC bővítmény engedélyezve van egy meglévő Application Gateway-példány megadása nélkül, az azt jelenti, hogy automatikusan létre kell Standard_v2 egy új termékváltozatot Application Gateway példányban. Így a virtuális gép példányának nevét és alhálózati címterületét is Application Gateway meg. A Application Gateway példány neve *myApplicationGateway* lesz, a használt alhálózati címtér pedig 10.2.0.0/16.

```azurecli-interactive
az aks create -n myCluster -g myResourceGroup --network-plugin azure --enable-managed-identity -a ingress-appgw --appgw-name myApplicationGateway --appgw-subnet-cidr "10.2.0.0/16" --generate-ssh-keys
```

A parancs további paramétereinek `az aks create` konfigurálásért tekintse meg ezeket [a hivatkozásokat.](/cli/azure/aks#az_aks_create) 

> [!NOTE]
> A létrehozott AKS-fürt a létrehozott *myResourceGroup erőforráscsoportban fog megjelenni.* Az automatikusan létrehozott Application Gateway azonban abban a csomóponti erőforráscsoportban lesz, ahol az ügynökkészletek vannak. A csomópont erőforráscsoportja alapértelmezés szerint *MC_resource-group-name_cluster-name_location,* de módosítható. 

## <a name="deploy-a-sample-application-by-using-agic"></a>Mintaalkalmazás üzembe helyezése az AGIC használatával

Most egy mintaalkalmazást fog üzembe helyezni a létrehozott AKS-fürtön. Az alkalmazás az AGIC bővítményt fogja használni a bejövő forgalomhoz, és csatlakoztatja az Application Gateway-példányt az AKS-fürthöz. 

Először szerezze be a hitelesítő adatokat az AKS-fürthöz az parancs `az aks get-credentials` futtatásával: 

```azurecli-interactive
az aks get-credentials -n myCluster -g myResourceGroup
```

Most, hogy már van hitelesítő adatai, futtassa a következő parancsot egy olyan mintaalkalmazás beállításához, amely AGIC-t használ a fürtbe történő bejövő forgalomhoz. Az AGIC frissíti Application Gateway korábban beállított új mintaalkalmazáshoz tartozó útválasztási szabályokkal.  

```azurecli-interactive
kubectl apply -f https://raw.githubusercontent.com/Azure/application-gateway-kubernetes-ingress/master/docs/examples/aspnetapp.yaml 
```

## <a name="check-that-the-application-is-reachable"></a>Ellenőrizze, hogy az alkalmazás elérhető-e

Most, hogy az Application Gateway-példány be van állítva az AKS-fürt forgalmának kiszolgálására, ellenőrizzük, hogy az alkalmazás elérhető-e. Először szerezze be a bejövő forgalom IP-címét: 

```azurecli-interactive
kubectl get ingress
```

Ellenőrizze, hogy a létrehozott mintaalkalmazás a következő egyikével fut-e:

- Az előző parancs futtatásából kapott Application Gateway IP-címének felkeresve.
- A `curl` használatával. 

Application Gateway frissítés letöltése egy percet is igénybe vehet. Ha Application Gateway frissítés még frissítési  állapotban van a portálon, hagyja, hogy befejeződik, mielőtt megpróbálja elérni az IP-címet. 

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rájuk szüksége, távolítsa el az erőforráscsoportot, a Application Gateway-példányt és az összes kapcsolódó erőforrást:

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Tudnivalók az AGIC bővítmény letiltásával kapcsolatban](./ingress-controller-disable-addon.md)
