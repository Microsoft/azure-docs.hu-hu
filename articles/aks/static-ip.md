---
title: Statikus IP-cím használata a Load balancerrel
titleSuffix: Azure Kubernetes Service
description: Ismerje meg, hogyan hozhat létre és használhat statikus IP-címet az Azure Kubernetes Service (ak) terheléselosztó használatával.
services: container-service
ms.topic: article
ms.date: 11/14/2020
ms.openlocfilehash: 102df48ca22fb996e0f4d9c402b8ce8f0fa80f2c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "102509472"
---
# <a name="use-a-static-public-ip-address-and-dns-label-with-the-azure-kubernetes-service-aks-load-balancer"></a>Statikus nyilvános IP-cím és DNS-címke használata az Azure Kubernetes Service (ak) terheléselosztó használatával

Alapértelmezés szerint egy AK-fürt által létrehozott terheléselosztó-erőforráshoz hozzárendelt nyilvános IP-cím csak az adott erőforrás élettartama esetén érvényes. Ha törli a Kubernetes szolgáltatást, a rendszer a társított terheléselosztó és az IP-cím is törlődik. Ha egy adott IP-címet szeretne hozzárendelni, vagy IP-címet kíván megőrizni az újratelepített Kubernetes-szolgáltatásokhoz, létrehozhat és használhat statikus nyilvános IP-címet.

Ez a cikk bemutatja, hogyan hozhat létre statikus nyilvános IP-címet, és hogyan rendelheti hozzá a Kubernetes szolgáltatáshoz.

## <a name="before-you-begin"></a>Előkészületek

Ez a cikk feltételezi, hogy rendelkezik egy meglévő AK-fürttel. Ha AK-fürtre van szüksége, tekintse meg az AK gyors üzembe helyezését [Az Azure CLI használatával][aks-quickstart-cli] vagy [a Azure Portal használatával][aks-quickstart-portal].

Szüksége lesz az Azure CLI 2.0.59 vagy újabb verziójára is, valamint a telepítésre és konfigurálásra. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése][install-azure-cli].

Ez a cikk a standard *SKU IP* *standard* SKU Load Balancer használatával történő használatát ismerteti. További információt [az IP-címek típusai és a kiosztási módszerek az Azure-ban][ip-sku]című témakörben talál.

## <a name="create-a-static-ip-address"></a>Statikus IP-cím létrehozása

Hozzon létre egy statikus nyilvános IP-címet az az [Network Public IP Create][az-network-public-ip-create] paranccsal. A következő létrehoz egy *myAKSPublicIP* nevű statikus IP-erőforrást a *myResourceGroup* erőforráscsoporthoz:

```azurecli-interactive
az network public-ip create \
    --resource-group myResourceGroup \
    --name myAKSPublicIP \
    --sku Standard \
    --allocation-method static
```

> [!NOTE]
> Ha *alapszintű* SKU Load balancert használ az AK-fürtben, az *alapszintű* érték használata a nyilvános IP-cím definiálásakor a *SKU* paraméter esetében. Csak az *Alapszintű* SKU IP-címei működnek az *alapszintű* SKU Load balancerben, és csak a *standard* SKU IP-címei működnek a *standard* SKU Load balancerekkel. 

Az IP-cím jelenik meg, ahogy az a következő tömörített példában látható:

```json
{
  "publicIp": {
    ...
    "ipAddress": "40.121.183.52",
    ...
  }
}
```

Később lekérheti a nyilvános IP-címet az az [Network Public-IP List][az-network-public-ip-list] paranccsal. Adja meg a létrehozott csomópont-erőforráscsoport és nyilvános IP-cím nevét, és az *IP* -cím lekérdezését az alábbi példában látható módon:

```azurecli-interactive
$ az network public-ip show --resource-group myResourceGroup --name myAKSPublicIP --query ipAddress --output tsv

40.121.183.52
```

## <a name="create-a-service-using-the-static-ip-address"></a>Szolgáltatás létrehozása a statikus IP-cím használatával

A szolgáltatás létrehozása előtt győződjön meg arról, hogy az AK-fürt által használt fürt identitása delegált engedéllyel rendelkezik a másik erőforráscsoporthoz. Például:

```azurecli-interactive
az role assignment create \
    --assignee <Client ID> \
    --role "Network Contributor" \
    --scope /subscriptions/<subscription id>/resourceGroups/<resource group name>
```

> [!IMPORTANT]
> Ha a kimenő IP-címet testreszabta, győződjön meg arról, hogy a fürt identitása rendelkezik a kimenő nyilvános IP-címhez és a bejövő nyilvános IP-címhez tartozó engedélyekkel is.

Ha a *terheléselosztó* szolgáltatást statikus nyilvános IP-címmel szeretné létrehozni, adja hozzá a `loadBalancerIP` tulajdonságot és a statikus nyilvános IP-cím értékét a YAML-jegyzékhez. Hozzon létre egy nevű fájlt `load-balancer-service.yaml` , és másolja a következő YAML. Adja meg az előző lépésben létrehozott saját nyilvános IP-címet. A következő példa a jegyzetet is beállítja az *myResourceGroup* nevű erőforráscsoporthoz. Adja meg a saját erőforráscsoport-nevét.

```yaml
apiVersion: v1
kind: Service
metadata:
  annotations:
    service.beta.kubernetes.io/azure-load-balancer-resource-group: myResourceGroup
  name: azure-load-balancer
spec:
  loadBalancerIP: 40.121.183.52
  type: LoadBalancer
  ports:
  - port: 80
  selector:
    app: azure-load-balancer
```

Hozza létre a szolgáltatást és az üzembe helyezést a `kubectl apply` paranccsal.

```console
kubectl apply -f load-balancer-service.yaml
```

## <a name="apply-a-dns-label-to-the-service"></a>DNS-címke alkalmazása a szolgáltatásra

Ha a szolgáltatás dinamikus vagy statikus nyilvános IP-címet használ, használhatja a szolgáltatás megjegyzését `service.beta.kubernetes.io/azure-dns-label-name` egy nyilvános DNS-címke megadásához. Ez egy teljes tartománynevet tesz közzé a szolgáltatáshoz az Azure nyilvános DNS-kiszolgálói és legfelső szintű tartománya segítségével. A jegyzet értékének egyedinek kell lennie az Azure-ban, ezért azt javasoljuk, hogy használjon megfelelően minősített címkét.   

Az Azure ezt követően automatikusan hozzáfűz egy alapértelmezett alhálózatot, például `<location>.cloudapp.azure.com` (ahol a hely a kiválasztott régió), hogy az Ön által megadott nevet adja meg a teljes DNS-név létrehozásához. Például:

```yaml
apiVersion: v1
kind: Service
metadata:
  annotations:
    service.beta.kubernetes.io/azure-dns-label-name: myserviceuniquelabel
  name: azure-load-balancer
spec:
  type: LoadBalancer
  ports:
  - port: 80
  selector:
    app: azure-load-balancer
```

> [!NOTE] 
> A szolgáltatás saját tartományon való közzétételéhez tekintse meg a [Azure DNS][azure-dns-zone] és a [külső DNS-][external-dns] projektet.

## <a name="troubleshoot"></a>Hibaelhárítás

Ha a Kubernetes szolgáltatás jegyzékfájljának *loadBalancerIP* tulajdonságában definiált statikus IP-cím nem létezik, vagy nem lett létrehozva a csomópont-erőforráscsoporthoz, és nincs további delegálás konfigurálva, a terheléselosztó szolgáltatás létrehozása sikertelen lesz. A hibák megoldásához tekintse át a szolgáltatás-létrehozási eseményeket a [kubectl leíró][kubectl-describe] paranccsal. Adja meg a szolgáltatás nevét a YAML jegyzékfájlban megadott módon, az alábbi példában látható módon:

```console
kubectl describe service azure-load-balancer
```

Megjelenik a Kubernetes szolgáltatás erőforrásával kapcsolatos információ. Az alábbi példa kimenetének végén lévő *események* azt jelzik, hogy a *felhasználó által megadott IP-cím nem található*. Ezekben a forgatókönyvekben ellenőrizze, hogy a csomópont erőforráscsoporthoz létrehozta-e a statikus nyilvános IP-címet, és hogy a Kubernetes szolgáltatás jegyzékfájljában megadott IP-cím helyes-e.

```
Name:                     azure-load-balancer
Namespace:                default
Labels:                   <none>
Annotations:              <none>
Selector:                 app=azure-load-balancer
Type:                     LoadBalancer
IP:                       10.0.18.125
IP:                       40.121.183.52
Port:                     <unset>  80/TCP
TargetPort:               80/TCP
NodePort:                 <unset>  32582/TCP
Endpoints:                <none>
Session Affinity:         None
External Traffic Policy:  Cluster
Events:
  Type     Reason                      Age               From                Message
  ----     ------                      ----              ----                -------
  Normal   CreatingLoadBalancer        7s (x2 over 22s)  service-controller  Creating load balancer
  Warning  CreatingLoadBalancerFailed  6s (x2 over 12s)  service-controller  Error creating load balancer (will retry): Failed to create load balancer for service default/azure-load-balancer: user supplied IP Address 40.121.183.52 was not found
```

## <a name="next-steps"></a>Következő lépések

Az alkalmazásokra irányuló hálózati forgalom további szabályozása érdekében érdemes lehet [egy bejövő vezérlőt létrehozni][aks-ingress-basic]. Egy [statikus nyilvános IP-címmel rendelkező bejövő vezérlőt is létrehozhat][aks-static-ingress].

<!-- LINKS - External -->
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[azure-dns-zone]: https://azure.microsoft.com/services/dns/
[external-dns]: https://github.com/kubernetes-sigs/external-dns

<!-- LINKS - Internal -->
[aks-faq-resource-group]: faq.md#why-are-two-resource-groups-created-with-aks
[az-network-public-ip-create]: /cli/azure/network/public-ip#az-network-public-ip-create
[az-network-public-ip-list]: /cli/azure/network/public-ip#az-network-public-ip-list
[az-aks-show]: /cli/azure/aks#az-aks-show
[aks-ingress-basic]: ingress-basic.md
[aks-static-ingress]: ingress-static-ip.md
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[ip-sku]: ../virtual-network/public-ip-addresses.md#sku
