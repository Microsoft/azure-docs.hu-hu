---
title: Statikus IP-cím használata a bejövő forgalomhoz
titleSuffix: Azure Kubernetes Service
description: Megtudhatja, hogyan hozhat létre és használhat statikus nyilvános IP-címet a forgalomhoz egy Azure Kubernetes Service -fürtben
services: container-service
ms.topic: article
ms.date: 03/16/2021
ms.openlocfilehash: 7cff3f5d66bb9872a0c949c6237150f8b69c9fa7
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107773008"
---
# <a name="use-a-static-public-ip-address-for-egress-traffic-with-a-basic-sku-load-balancer-in-azure-kubernetes-service-aks"></a>Statikus nyilvános IP-cím használata a  forgalomhoz alapszintű termékváltozatú terheléselosztással a Azure Kubernetes Service (AKS)

Alapértelmezés szerint a rendszer véletlenszerűen rendeli hozzá a Azure Kubernetes Service (AKS) fürtből származó bejövő IP-címet. Ez a konfiguráció nem ideális, ha például egy IP-címet kell azonosítania a külső szolgáltatásokhoz való hozzáféréshez. Ehelyett előfordulhat, hogy statikus IP-címet kell hozzárendelni a szolgáltatás-hozzáférés engedélyezési listához.

Ez a cikk bemutatja, hogyan hozhat létre és használhat statikus nyilvános IP-címet egy AKS-fürtből bejövő forgalommal való használatra.

## <a name="before-you-begin"></a>Előkészületek

Ez a cikk feltételezi, hogy az Azure Basic Load Balancer.  Javasoljuk az [Azure standard Load Balancer](../load-balancer/load-balancer-overview.md)használatát, és speciálisabb funkciókat is használhat az AKS-beli bejövő [forgalom szabályozására.](./limit-egress-traffic.md)

Ez a cikk feltételezi, hogy már van AKS-fürte. Ha AKS-fürtre van szüksége, tekintse meg az AKS rövid útmutatóját az [Azure CLI][aks-quickstart-cli] vagy a [Azure Portal.][aks-quickstart-portal]

Az Azure CLI 2.0.59-es vagy újabb verziójára is telepítve és konfigurálva kell. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése][install-azure-cli].

> [!IMPORTANT]
> Ez a cikk az *alapszintű termékváltozatú* terheléseltöltőt használja egyetlen csomópontkészletben. Ez a konfiguráció több csomópontkészlet esetén nem érhető el, mivel az alapszintű *termékváltozat* terheléselosztása több csomópontkészlet esetén nem támogatott. A Standard termékváltozatú terheléselosztás használatával kapcsolatos további részletekért lásd: Nyilvános  standard Load Balancer használata a Azure Kubernetes Service [(AKS)][slb] használatával.

## <a name="egress-traffic-overview"></a>A bejövő forgalom áttekintése

Az AKS-fürtről kimenő forgalom a [következő Azure Load Balancer követi:][outbound-connections]. Az első típusú Kubernetes-szolgáltatás létrehozása előtt az AKS-fürt ügynökcsomópontjai nem részei egyetlen Azure Load Balancer `LoadBalancer` sem. Ebben a konfigurációban a csomópontoknak nincs példányszintű nyilvános IP-címe. Az Azure olyan nyilvános forrás IP-címre fordítja le a kimenő folyamatot, amely nem konfigurálható vagy determinisztikus.

Egy típusú Kubernetes-szolgáltatás létrehozása után az ügynökcsomópontok hozzáadva egy Azure Load Balancer `LoadBalancer` készlethez. Load Balancer Basic egyetlen előteret választ a kimenő folyamatokhoz, ha több (nyilvános) IP-előtere van kiválasztva kimenő forgalomhoz. Ez a választás nem konfigurálható, és a kiválasztási algoritmust véletlenszerűnek kell tekintenünk. Ez a nyilvános IP-cím csak az erőforrás élettartama alatt érvényes. Ha törli a Kubernetes LoadBalancer szolgáltatást, a társított terheléselosztási és IP-cím is törlődik. Ha egy adott IP-címet szeretne hozzárendelni, vagy meg szeretne tartani egy IP-címet az újra üzembe adott Kubernetes-szolgáltatásokhoz, létrehozhat és használhat statikus nyilvános IP-címet.

## <a name="create-a-static-public-ip"></a>Statikus nyilvános IP-cím létrehozása

Az az [aks show][az-aks-show] paranccsal szerezze be az erőforráscsoport nevét, és adja hozzá a `--query nodeResourceGroup` lekérdezési paramétert. A következő példa a myResourceGroup nevű erőforráscsoportban lekért *myAKSCluster AKS-fürt* nevének *csomópont-erőforráscsoportját:*

```azurecli-interactive
$ az aks show --resource-group myResourceGroup --name myAKSCluster --query nodeResourceGroup -o tsv

MC_myResourceGroup_myAKSCluster_eastus
```

Most hozzon létre egy statikus nyilvános IP-címet [az az network public ip create paranccsal.][az-network-public-ip-create] Adja meg az előző parancsban lekért csomópont-erőforráscsoport nevét, majd az IP-címerőforrás nevét, például *myAKSPublicIP:*

```azurecli-interactive
az network public-ip create \
    --resource-group MC_myResourceGroup_myAKSCluster_eastus \
    --name myAKSPublicIP \
    --allocation-method static
```

Az IP-cím az alábbi rövid példakimenetnek megfelelő módon jelenik meg:

```json
{
  "publicIp": {
    "dnsSettings": null,
    "etag": "W/\"6b6fb15c-5281-4f64-b332-8f68f46e1358\"",
    "id": "/subscriptions/<SubscriptionID>/resourceGroups/MC_myResourceGroup_myAKSCluster_eastus/providers/Microsoft.Network/publicIPAddresses/myAKSPublicIP",
    "idleTimeoutInMinutes": 4,
    "ipAddress": "40.121.183.52",
    [..]
  }
```

A nyilvános IP-címet később az [az network public-ip list][az-network-public-ip-list] paranccsal kaphatja meg. Adja meg a csomóponti erőforráscsoport nevét, majd az alábbi példában látható módon lekérdezi az *IP-címet:*

```azurecli-interactive
$ az network public-ip list --resource-group MC_myResourceGroup_myAKSCluster_eastus --query [0].ipAddress --output tsv

40.121.183.52
```

## <a name="create-a-service-with-the-static-ip"></a>Szolgáltatás létrehozása statikus IP-címmel

Ha statikus nyilvános IP-címmel szeretne szolgáltatást létrehozni, adja hozzá a statikus nyilvános IP-cím tulajdonságát és értékét a `loadBalancerIP` YAML-jegyzékfájlhoz. Hozzon létre egy nevű `egress-service.yaml` fájlt, és másolja be a következő YAML-fájlt. Adja meg az előző lépésben létrehozott saját nyilvános IP-címét.

```yaml
apiVersion: v1
kind: Service
metadata:
  name: azure-egress
spec:
  loadBalancerIP: 40.121.183.52
  type: LoadBalancer
  ports:
  - port: 80
```

Hozza létre a szolgáltatást és az üzembe helyezést az `kubectl apply` paranccsal.

```console
kubectl apply -f egress-service.yaml
```

Ez a szolgáltatás új előtere IP-címet konfigurál a Azure Load Balancer. Ha nem konfigurált más IP-címeket, akkor az összes bejövő forgalomnak ezt a címet kell használnia.  Ha több cím van konfigurálva a Azure Load Balancer, a nyilvános IP-címek bármelyike kiválasztható a kimenő forgalomhoz, és véletlenszerűen lesz kiválasztva.

## <a name="verify-egress-address"></a>A bejövő forgalom címének ellenőrzése

A statikus nyilvános IP-cím használatának ellenőrzéséhez dns-névfeloldási szolgáltatást használhat, például a következőt: `checkip.dyndns.org` .

Indíts el és csatoljon egy *alapszintű Debian podot:*

```console
kubectl run -it --rm aks-ip --image=mcr.microsoft.com/aks/fundamental/base-ubuntu:v0.0.11
```

Egy webhely tárolón belüli eléréséhez a használatával telepítse `apt-get` a-t `curl` a tárolóba.

```console
apt-get update && apt-get install curl -y
```

Most a curl használatával férhet hozzá a *checkip.dyndns.org* helyhez. A kimenő IP-cím az alábbi kimenetben látható módon jelenik meg. Ez az IP-cím megegyezik a loadBalancer szolgáltatáshoz létrehozott és definiált statikus nyilvános IP-címmel:

```console
$ curl -s checkip.dyndns.org

<html><head><title>Current IP Check</title></head><body>Current IP Address: 40.121.183.52</body></html>
```

## <a name="next-steps"></a>Következő lépések

Ha nem kell több nyilvános IP-címet fenntartania a Azure Load Balancer, ehelyett használhat egy bejövő vezérlőt. A bejövő vezérlők további előnyöket biztosítanak, például az SSL/TLS-megszakítást, az URI-átírások támogatását és a upstream SSL/TLS-titkosítást. További információ: Alapszintű bejövő forgalomvezérlő létrehozása az [AKS-ban.][ingress-aks-cluster]

<!-- LINKS - internal -->
[az-network-public-ip-create]: /cli/azure/network/public-ip#az_network_public_ip_create
[az-network-public-ip-list]: /cli/azure/network/public-ip#az_network_public_ip_list
[az-aks-show]: /cli/azure/aks#az_aks_show
[azure-cli-install]: /cli/azure/install-azure-cli
[ingress-aks-cluster]: ./ingress-basic.md
[outbound-connections]: ../load-balancer/load-balancer-outbound-connections.md#scenarios
[public-ip-create]: /cli/azure/network/public-ip#az_network_public_ip_create
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[slb]: load-balancer-standard.md
