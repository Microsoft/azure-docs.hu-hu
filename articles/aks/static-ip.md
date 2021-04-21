---
title: Statikus IP-cím használata terheléselosztással
titleSuffix: Azure Kubernetes Service
description: Megtudhatja, hogyan hozhat létre és használhat statikus IP-címet a Azure Kubernetes Service (AKS) terheléselosztási rendszerével.
services: container-service
ms.topic: article
ms.date: 11/14/2020
ms.openlocfilehash: bb1e5691027a4bd86b57390e12259ac165ca9ed8
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107769516"
---
# <a name="use-a-static-public-ip-address-and-dns-label-with-the-azure-kubernetes-service-aks-load-balancer"></a>Statikus nyilvános IP-cím és DNS-címke használata a Azure Kubernetes Service (AKS) terheléselosztási rendszerével

Alapértelmezés szerint az AKS-fürt által létrehozott terheléselosztási erőforráshoz rendelt nyilvános IP-cím csak az adott erőforrás élettartama alatt érvényes. Ha törli a Kubernetes-szolgáltatást, a társított terheléselosztási és IP-cím is törlődik. Ha egy adott IP-címet szeretne hozzárendelni, vagy meg szeretne tartani egy IP-címet az újra üzembe adott Kubernetes-szolgáltatásokhoz, létrehozhat és használhat statikus nyilvános IP-címet.

Ez a cikk bemutatja, hogyan hozhat létre statikus nyilvános IP-címet, és hogyan rendelheti hozzá a Kubernetes-szolgáltatáshoz.

## <a name="before-you-begin"></a>Előkészületek

Ez a cikk feltételezi, hogy már van AKS-fürte. Ha AKS-fürtre van szüksége, tekintse meg az AKS rövid útmutatóját az [Azure CLI][aks-quickstart-cli] vagy a [Azure Portal.][aks-quickstart-portal]

Az Azure CLI 2.0.59-es vagy újabb verziójára is telepítve és konfigurálva kell. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése][install-azure-cli].

Ez a cikk a *Standard* termékváltozatú IP-cím standard termékváltozatú *terheléseltöltővel* való használatával foglalkozik. További információ: [IP-címtípusok és lefoglalási módszerek az Azure-ban.][ip-sku]

## <a name="create-a-static-ip-address"></a>Statikus IP-cím létrehozása

Hozzon létre egy statikus nyilvános IP-címet [az az network public ip create paranccsal.][az-network-public-ip-create] Az alábbiakban létrehozunk egy *myAKSPublicIP* nevű statikus IP-erőforrást a *myResourceGroup* erőforráscsoportban:

```azurecli-interactive
az network public-ip create \
    --resource-group myResourceGroup \
    --name myAKSPublicIP \
    --sku Standard \
    --allocation-method static
```

> [!NOTE]
> Ha alapszintű *termékváltozat-terheléselosztást* használ az AKS-fürtben, nyilvános IP-cím definiálásakor használja az Alapszintűt a *termékváltozat* paraméteréhez.  Csak *az alapszintű* termékváltozat-IP-k működnek az alapszintű *termékváltozatú* terheléselosztással, és csak a *Standard* termékváltozatú IP-k működnek a *Standard* termékváltozatú terheléselosztásokkal. 

Megjelenik az IP-cím, ahogyan az a következő rövid példakimenetben látható:

```json
{
  "publicIp": {
    ...
    "ipAddress": "40.121.183.52",
    ...
  }
}
```

A nyilvános IP-címet később az [az network public-ip list paranccsal kaphatja][az-network-public-ip-list] meg. Adja meg a csomóponti erőforráscsoport nevét és a létrehozott nyilvános IP-címet, és az alábbi példában látható módon lekérdezi az *IP-címet:*

```azurecli-interactive
$ az network public-ip show --resource-group myResourceGroup --name myAKSPublicIP --query ipAddress --output tsv

40.121.183.52
```

## <a name="create-a-service-using-the-static-ip-address"></a>Szolgáltatás létrehozása a statikus IP-cím használatával

Szolgáltatás létrehozása előtt győződjön meg arról, hogy az AKS-fürt által használt fürtidentitás delegált engedélyekkel rendelkezik a másik erőforráscsoporthoz. Például:

```azurecli-interactive
az role assignment create \
    --assignee <Client ID> \
    --role "Network Contributor" \
    --scope /subscriptions/<subscription id>/resourceGroups/<resource group name>
```

> [!IMPORTANT]
> Ha testre szabta a kimenő IP-címet, győződjön meg arról, hogy a fürtidentitás rendelkezik engedélyekkel mind a kimenő nyilvános IP-címhez, mind pedig a bejövő nyilvános IP-címhez.

Ha statikus nyilvános IP-címmel szeretne *loadBalancer* szolgáltatást létrehozni, adja hozzá a YAML-jegyzékfájlhoz a tulajdonságot és a statikus nyilvános `loadBalancerIP` IP-cím értékét. Hozzon létre egy nevű `load-balancer-service.yaml` fájlt, és másolja be a következő YAML-fájlt. Adja meg az előző lépésben létrehozott saját nyilvános IP-címét. A következő példa a jegyzetet is beállítja a *myResourceGroup nevű erőforráscsoportra.* Adja meg saját erőforráscsoport-nevét.

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

Hozza létre a szolgáltatást és az üzembe helyezést az `kubectl apply` paranccsal.

```console
kubectl apply -f load-balancer-service.yaml
```

## <a name="apply-a-dns-label-to-the-service"></a>DNS-címke alkalmazása a szolgáltatásra

Ha a szolgáltatás dinamikus vagy statikus nyilvános IP-címet használ, a szolgáltatásjegyzet használatával nyilvános `service.beta.kubernetes.io/azure-dns-label-name` DNS-címkét állíthat be. Ez közzétesz egy teljes tartománynevet a szolgáltatáshoz az Azure nyilvános DNS-kiszolgálói és a legfelső szintű tartomány használatával. A jegyzet értékének egyedinek kell lennie az Azure-helyen belül, ezért ajánlott megfelelően minősített címkét használni.   

Az Azure ezután automatikusan hozzáfűz egy alapértelmezett alhálózatot ,például (ahol a hely a kiválasztott régió), a megadott névhez a teljes `<location>.cloudapp.azure.com` DNS-név létrehozásához. Például:

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
> A szolgáltatás saját tartományon való közzétételéhez [lásd:][azure-dns-zone] Azure DNS [external-dns projekt.][external-dns]

## <a name="troubleshoot"></a>Hibaelhárítás

Ha a Kubernetes-szolgáltatásjegyzék *loadBalancerIP* tulajdonságában meghatározott statikus IP-cím nem létezik, vagy nem lett létrehozva a csomópont erőforráscsoportban, és nincs további delegálás konfigurálva, a terheléselelosztási szolgáltatás létrehozása sikertelen lesz. A hibaelhárításhoz tekintse át a szolgáltatás-létrehozási eseményeket a [kubectl describe paranccsal.][kubectl-describe] Adja meg a szolgáltatás YAML-jegyzékfájlban megadott nevét az alábbi példában látható módon:

```console
kubectl describe service azure-load-balancer
```

Megjelennek a Kubernetes Service-erőforrásra vonatkozó információk. A *következő* példakimenet végén található események azt jelzik, hogy a felhasználó által megadott *IP-cím nem található.* Ezekben a forgatókönyvekben ellenőrizze, hogy létrehozta-e a statikus nyilvános IP-címet a csomópont erőforráscsoportban, és hogy helyes-e a Kubernetes-szolgáltatásjegyzékben megadott IP-cím.

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

Az alkalmazások hálózati forgalmának további szabályozásához ehelyett létrehozhat egy [bejövőforgalom-vezérlőt.][aks-ingress-basic] Statikus nyilvános [IP-címmel is létrehozhat][aks-static-ingress]egy bejövő vezérlőt.

<!-- LINKS - External -->
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[azure-dns-zone]: https://azure.microsoft.com/services/dns/
[external-dns]: https://github.com/kubernetes-sigs/external-dns

<!-- LINKS - Internal -->
[aks-faq-resource-group]: faq.md#why-are-two-resource-groups-created-with-aks
[az-network-public-ip-create]: /cli/azure/network/public-ip#az_network_public_ip_create
[az-network-public-ip-list]: /cli/azure/network/public-ip#az_network_public_ip_list
[az-aks-show]: /cli/azure/aks#az_aks_show
[aks-ingress-basic]: ingress-basic.md
[aks-static-ingress]: ingress-static-ip.md
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[ip-sku]: ../virtual-network/public-ip-addresses.md#sku
