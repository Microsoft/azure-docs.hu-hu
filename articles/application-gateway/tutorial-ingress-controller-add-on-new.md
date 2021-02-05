---
title: 'Oktatóanyag: a bejövő adatkezelő beépülő modul engedélyezése egy új, Azure Application Gateway-példánnyal rendelkező új AK-fürthöz'
description: Ebből az oktatóanyagból megtudhatja, hogyan használhatja az Azure CLI-t az új, Application Gateway példánnyal rendelkező új AK-fürthöz tartozó bejövő vezérlő-bővítmény engedélyezéséhez.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: tutorial
ms.date: 09/24/2020
ms.author: caya
ms.openlocfilehash: 775dc2133473354a1e534275fb0d813f299217d1
ms.sourcegitcommit: f377ba5ebd431e8c3579445ff588da664b00b36b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/05/2021
ms.locfileid: "99593817"
---
# <a name="tutorial-enable-the-ingress-controller-add-on-preview-for-a-new-aks-cluster-with-a-new-application-gateway-instance"></a>Oktatóanyag: az új, Application Gateway példánnyal rendelkező új AK-fürthöz tartozó bejövő vezérlő bővítmény (előzetes verzió) engedélyezése

Az Azure CLI-vel engedélyezheti az [Azure Kubernetes Services-(ak-)](https://azure.microsoft.com/services/kubernetes-service/) fürthöz tartozó [Application Gateway beáramló vezérlő (AGIC)](ingress-controller-overview.md) bővítményt. A bővítmény jelenleg előzetes verzióban érhető el.

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

 - Az oktatóanyaghoz az Azure CLI 2.0.4 vagy újabb verziójára van szükség. Azure Cloud Shell használata esetén a legújabb verzió már telepítve van. Ha az Azure CLI-t használja, telepítenie kell az előzetes verziójú bővítményt a CLI-be a következő parancs használatával, ha még nem tette meg:
    ```azurecli-interactive
    az extension add --name aks-preview
    ```

 - Regisztrálja az *AK-IngressApplicationGatewayAddon* funkció jelzőjét az az [Feature Register](/cli/azure/feature#az-feature-register) paranccsal az alábbi példában látható módon. Ezt csak egyszer kell elvégeznie előfizetés esetén, amíg a bővítmény még előzetes verzióban van.
    ```azurecli-interactive
    az feature register --name AKS-IngressApplicationGatewayAddon --namespace Microsoft.ContainerService
    ```

   Az állapot megjelenítéséhez néhány percet is igénybe vehet `Registered` . A regisztrációs állapotot az az [Feature List](/cli/azure/feature#az-feature-register) parancs használatával tekintheti meg:
    ```azurecli-interactive
    az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/AKS-IngressApplicationGatewayAddon')].{Name:name,State:properties.state}"
    ```

 - Ha elkészült, frissítse a Microsoft. Tárolószolgáltatás erőforrás-szolgáltató regisztrációját az az [Provider Register](/cli/azure/provider#az-provider-register) paranccsal:
    ```azurecli-interactive
    az provider register --namespace Microsoft.ContainerService
    ```

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

Ha egy meglévő Application Gateway-példány megadása nélkül telepít egy új AK-fürtöt a AGIC-bővítménysel, akkor a rendszer egy Standard_v2 SKU Application Gateway példány automatikus létrehozását fogja jelenteni. Így megadhatja a Application Gateway példány nevét és alhálózatának címterület-területét is. Az Application Gateway-példány neve *myApplicationGateway* lesz, és az alhálózati címtartomány a jelenleg használt 10.2.0.0/16. Győződjön meg arról, hogy az ebben az oktatóanyagban az AK-előzetes bővítményt adta hozzá vagy frissítette. 

```azurecli-interactive
az aks create -n myCluster -g myResourceGroup --network-plugin azure --enable-managed-identity -a ingress-appgw --appgw-name myApplicationGateway --appgw-subnet-prefix "10.2.0.0/16" --generate-ssh-keys
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
