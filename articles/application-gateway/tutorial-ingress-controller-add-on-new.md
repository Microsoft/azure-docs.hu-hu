---
title: 'Oktatóanyag: a bejövő adatkezelő beépülő modul engedélyezése új, Azure-Application Gateway'
description: Ebből az oktatóanyagból megtudhatja, hogyan engedélyezheti a bejövő adatkezelő beépülő modult új, Application Gateway példánnyal rendelkező új AK-fürthöz.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: tutorial
ms.date: 03/02/2021
ms.author: caya
ms.openlocfilehash: c37168c5165f5402dd4f57c8557bc2b7b3603533
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "101720188"
---
# <a name="tutorial-enable-the-ingress-controller-add-on-for-a-new-aks-cluster-with-a-new-application-gateway-instance"></a>Oktatóanyag: a bejövő adatkezelő beépülő modul engedélyezése egy új, Application Gateway példánnyal rendelkező új AK-fürthöz

Az Azure CLI-vel engedélyezheti az új [Azure Kubernetes Services-(ak-)](https://azure.microsoft.com/services/kubernetes-service/) fürthöz tartozó [Application Gateway beáramló vezérlő (AGIC)](ingress-controller-overview.md) bővítményét.

Ebben az oktatóanyagban létre fog hozni egy AK-fürtöt, amelyen engedélyezve van a AGIC-bővítmény. A fürt létrehozása automatikusan létrehoz egy Azure Application Gateway-példányt, amelyet használni fog. Ezután telepítenie kell egy minta alkalmazást, amely a bővítmény használatával teszi elérhetővé az alkalmazást Application Gatewayon keresztül. 

A bővítmény sokkal gyorsabban üzembe helyezi a AGIC-t az AK-fürthöz, mint [korábban a helmon keresztül](ingress-controller-overview.md#difference-between-helm-deployment-and-aks-add-on). Emellett teljes körűen felügyelt élményt nyújt.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Hozzon létre egy erőforráscsoportot. 
> * Hozzon létre egy új AK-fürtöt a AGIC-bővítmény engedélyezésével.
> * Helyezzen üzembe egy minta alkalmazást a AGIC használatával az AK-fürtön történő bejövő forgalomhoz.
> * Győződjön meg arról, hogy az alkalmazás elérhető Application Gatewayon keresztül.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

## <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

Az Azure-ban kapcsolódó erőforrásokat oszt ki egy erőforráscsoporthoz. Hozzon létre egy erőforráscsoportot az [az Group Create](/cli/azure/group#az-group-create)paranccsal. A következő példában létrehozunk egy *myResourceGroup* nevű erőforráscsoportot a *canadacentral* helyen (régió): 

```azurecli-interactive
az group create --name myResourceGroup --location canadacentral
```

## <a name="deploy-an-aks-cluster-with-the-add-on-enabled"></a>AK-fürt üzembe helyezése a bővítmény engedélyezésével

Most üzembe kell helyeznie egy új AK-fürtöt a AGIC-bővítmény engedélyezésével. Ha nem ad meg meglévő Application Gateway-példányt ebben a folyamatban, automatikusan létrehozunk egy új Application Gateway-példányt, és beállítja az AK-fürt felé irányuló forgalom kiszolgálását.  

> [!NOTE]
> A Application Gateway beáramlási vezérlő bővítmény *csak* Application Gateway v2 SKU-t (standard és WAF) támogat, és *nem* az Application Gateway v1 SKU-t. Ha új Application Gateway példányt telepít a AGIC-bővítményen keresztül, akkor csak egy Application Gateway Standard_v2 SKU-t telepíthet. Ha engedélyezni szeretné a bővítményt egy Application Gateway WAF_v2 SKU-hoz, használja a következő módszerek egyikét:
>
> - Application Gateway WAF engedélyezése a portálon keresztül. 
> - Először hozza létre a WAF_v2 Application Gateway példányt, majd kövesse a [AGIC-bővítmény meglévő AK-fürttel és meglévő Application Gateway-példánnyal való engedélyezésének](tutorial-ingress-controller-add-on-existing.md)utasításait. 

A következő példában egy *myCluster* nevű új AK-fürtöt fog üzembe helyezni az [Azure CNI](../aks/concepts-network.md#azure-cni-advanced-networking) és a [felügyelt identitások](../aks/use-managed-identity.md)használatával. A AGIC-bővítmény engedélyezve lesz a létrehozott erőforráscsoport *myResourceGroup*. 

Ha egy meglévő Application Gateway-példány megadása nélkül telepít egy új AK-fürtöt a AGIC-bővítménysel, akkor a rendszer egy Standard_v2 SKU Application Gateway példány automatikus létrehozását fogja jelenteni. Így megadhatja a Application Gateway példány nevét és alhálózatának címterület-területét is. Az Application Gateway-példány neve *myApplicationGateway* lesz, és az alhálózati címtartomány a jelenleg használt 10.2.0.0/16.

```azurecli-interactive
az aks create -n myCluster -g myResourceGroup --network-plugin azure --enable-managed-identity -a ingress-appgw --appgw-name myApplicationGateway --appgw-subnet-cidr "10.2.0.0/16" --generate-ssh-keys
```

A parancs további paramétereinek konfigurálásához `az aks create` tekintse meg [ezeket a hivatkozásokat](/cli/azure/aks#az-aks-create). 

> [!NOTE]
> A létrehozott AK-fürt megjelenik a létrehozott erőforráscsoport *myResourceGroup*. Az automatikusan létrehozott Application Gateway példány azonban a csomópont-erőforráscsoporthoz kerül, ahol az ügynök-készletek vannak. Alapértelmezés szerint a csomópont-erőforráscsoport neve *MC_resource-Group-name_cluster-name_location* , de módosítható. 

## <a name="deploy-a-sample-application-by-using-agic"></a>Minta alkalmazás üzembe helyezése a AGIC használatával

Most üzembe kell helyeznie egy minta alkalmazást a létrehozott AK-fürtön. Az alkalmazás a AGIC-bővítményt fogja használni a bejövő forgalomhoz, és a Application Gateway példányt az AK-fürthöz kapcsolja. 

Először kérje le a hitelesítő adatokat az AK-fürthöz a következő parancs futtatásával `az aks get-credentials` : 

```azurecli-interactive
az aks get-credentials -n myCluster -g myResourceGroup
```

Most, hogy rendelkezik hitelesítő adatokkal, futtassa a következő parancsot egy olyan AGIC beállításához, amely a fürtbe való belépést használja. A AGIC frissíti a korábban beállított Application Gateway példányt a megfelelő útválasztási szabályokkal a telepített új alkalmazáshoz.  

```azurecli-interactive
kubectl apply -f https://raw.githubusercontent.com/Azure/application-gateway-kubernetes-ingress/master/docs/examples/aspnetapp.yaml 
```

## <a name="check-that-the-application-is-reachable"></a>Győződjön meg arról, hogy az alkalmazás elérhető

Most, hogy a Application Gateway-példány úgy van beállítva, hogy kiszolgálja a forgalmat az AK-fürt számára, ellenőrizze, hogy az alkalmazás elérhető-e. Először szerezze be a bejövő forgalom IP-címét: 

```azurecli-interactive
kubectl get ingress
```

Győződjön meg arról, hogy a létrehozott minta alkalmazás a következők valamelyikével fut:

- Keresse meg az előző parancs futtatásával kapott Application Gateway példány IP-címét.
- A használata `curl` . 

A frissítés beszerzéséhez Application Gateway egy percet is igénybe vehet. Ha a Application Gateway még mindig **frissítési** állapotban van a portálon, hagyja befejezni az IP-cím elérését. 

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szüksége rájuk, távolítsa el az erőforráscsoportot, a Application Gateway példányt és az összes kapcsolódó erőforrást:

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [További információ a AGIC-bővítmény letiltásáról](./ingress-controller-disable-addon.md)
