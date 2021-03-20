---
title: Statikus IP-cím használata a kimenő forgalomhoz
titleSuffix: Azure Kubernetes Service
description: Megtudhatja, hogyan hozhat létre és használhat statikus nyilvános IP-címet a kimenő forgalomhoz egy Azure Kubernetes Service-(ak-) fürtben
services: container-service
ms.topic: article
ms.date: 03/16/2021
ms.openlocfilehash: e1f81bf4c4d35108557449a8bebd126bdf744191
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "104592370"
---
# <a name="use-a-static-public-ip-address-for-egress-traffic-with-a-basic-sku-load-balancer-in-azure-kubernetes-service-aks"></a>Statikus nyilvános IP-cím használata a kimenő forgalomhoz egy *alapszintű* SKU Load Balancer használatával az Azure Kubernetes szolgáltatásban (ak)

Alapértelmezés szerint a rendszer véletlenszerűen rendeli hozzá a kimenő IP-címet egy Azure Kubernetes-szolgáltatás (ak) fürtjéből. Ez a konfiguráció nem ideális, ha meg kell határoznia egy IP-címet a külső szolgáltatásokhoz való hozzáféréshez, például:. Ehelyett előfordulhat, hogy hozzá kell rendelnie egy statikus IP-címet, amelyet hozzá kell adni egy engedélyezési a szolgáltatás eléréséhez.

Ebből a cikkből megtudhatja, hogyan hozhat létre és használhat egy statikus nyilvános IP-címet egy AK-fürt kimenő forgalmának használatával.

## <a name="before-you-begin"></a>Előkészületek

Ez a cikk azt feltételezi, hogy az Azure alapszintű Load Balancer használja.  Javasoljuk, hogy az [Azure standard Load Balancer](../load-balancer/load-balancer-overview.md)használatát használja, és az [AK kimenő forgalmának szabályozására](./limit-egress-traffic.md)szolgáló fejlettebb funkciókkal is rendelkezik.

Ez a cikk feltételezi, hogy rendelkezik egy meglévő AK-fürttel. Ha AK-fürtre van szüksége, tekintse meg az AK gyors üzembe helyezését [Az Azure CLI használatával][aks-quickstart-cli] vagy [a Azure Portal használatával][aks-quickstart-portal].

Szüksége lesz az Azure CLI 2.0.59 vagy újabb verziójára is, valamint a telepítésre és konfigurálásra. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése][install-azure-cli].

> [!IMPORTANT]
> Ez a cikk az *alapszintű* SKU Load balancert használja egyetlen csomópontos készlettel. Ez a konfiguráció több csomópontos készlet esetében nem érhető el, mivel az *alapszintű* SKU Load Balancer több csomópontos készlet esetében nem támogatott. További információ a *standard* SKU Load Balancer használatáról: [nyilvános standard Load Balancer használata az Azure Kubernetes szolgáltatásban (ak)][slb] .

## <a name="egress-traffic-overview"></a>Kimenő forgalom áttekintése

Az AK-fürtök kimenő forgalma [Azure Load Balancer konvenciókat][outbound-connections]követi. Az első Kubernetes-szolgáltatás létrehozása előtt `LoadBalancer` egy AK-fürt ügynök-csomópontjai nem tartoznak egyetlen Azure Load Balancer-készletbe sem. Ebben a konfigurációban a csomópontok nem rendelkeznek példány-szintű nyilvános IP-címmel. Az Azure a kimenő forgalmat olyan nyilvános forrású IP-címekre fordítja le, amely nem konfigurálható vagy determinisztikus.

Ha létrejön egy Kubernetes-szolgáltatás, az `LoadBalancer` ügynök csomópontjai hozzáadódnak egy Azure Load Balancer készlethez. Load Balancer Basic (alapszintű) beállítás egyetlen előtér-felületet választ ki a kimenő folyamatokhoz, ha a kimenő folyamatok esetében több (nyilvános) IP-frontend szerepel. Ez a kijelölés nem konfigurálható, és a kiválasztási algoritmust véletlenszerűen kell figyelembe venni. Ez a nyilvános IP-cím csak az adott erőforrás élettartama esetén érvényes. Ha törli a Kubernetes terheléselosztó szolgáltatást, a rendszer a társított terheléselosztó és az IP-cím is törlődik. Ha egy adott IP-címet szeretne hozzárendelni, vagy IP-címet kíván megőrizni az újratelepített Kubernetes-szolgáltatásokhoz, létrehozhat és használhat statikus nyilvános IP-címet.

## <a name="create-a-static-public-ip"></a>Statikus nyilvános IP-cím létrehozása

Kérje le az erőforráscsoport nevét az az [az AK show][az-aks-show] paranccsal, és adja hozzá a `--query nodeResourceGroup` lekérdezési paramétert. A következő példa lekéri a csomópont-erőforráscsoportot az AK-fürt neve *myAKSCluster* az erőforráscsoport neve *myResourceGroup*:

```azurecli-interactive
$ az aks show --resource-group myResourceGroup --name myAKSCluster --query nodeResourceGroup -o tsv

MC_myResourceGroup_myAKSCluster_eastus
```

Most hozzon létre egy statikus nyilvános IP-címet az az [Network Public IP Create][az-network-public-ip-create] paranccsal. Adja meg az előző parancsban beszerzett csomópont-erőforráscsoport nevét, majd az IP-cím erőforrás nevét, például *myAKSPublicIP*:

```azurecli-interactive
az network public-ip create \
    --resource-group MC_myResourceGroup_myAKSCluster_eastus \
    --name myAKSPublicIP \
    --allocation-method static
```

Az IP-cím jelenik meg, ahogy az a következő tömörített példában látható:

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

Később lekérheti a nyilvános IP-címet az az [Network Public-IP List][az-network-public-ip-list] paranccsal. Adja meg a csomópont-erőforráscsoport nevét, majd az *IP* -cím lekérdezését az alábbi példában látható módon:

```azurecli-interactive
$ az network public-ip list --resource-group MC_myResourceGroup_myAKSCluster_eastus --query [0].ipAddress --output tsv

40.121.183.52
```

## <a name="create-a-service-with-the-static-ip"></a>Szolgáltatás létrehozása a statikus IP-címmel

Egy statikus nyilvános IP-címmel rendelkező szolgáltatás létrehozásához adja hozzá a `loadBalancerIP` tulajdonságot és a statikus nyilvános IP-cím értékét a YAML jegyzékfájlhoz. Hozzon létre egy nevű fájlt `egress-service.yaml` , és másolja a következő YAML. Adja meg az előző lépésben létrehozott saját nyilvános IP-címet.

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

Hozza létre a szolgáltatást és az üzembe helyezést a `kubectl apply` paranccsal.

```console
kubectl apply -f egress-service.yaml
```

Ez a szolgáltatás egy új előtér-IP-címet konfigurál a Azure Load Balancer. Ha más IP-címek nincsenek konfigurálva, akkor az **összes** kimenő forgalomnak most ezt a címet kell használnia. Ha több cím van konfigurálva a Azure Load Balanceron, akkor ezek közül bármelyik nyilvános IP-cím a kimenő forgalomra jelölt, az egyik pedig véletlenszerűen van kiválasztva.

## <a name="verify-egress-address"></a>Kimenő forgalom ellenőrzésének ellenőrzése

Annak ellenőrzéséhez, hogy a statikus nyilvános IP-cím használatban van-e, használhatja a DNS-alapú keresés szolgáltatást, például: `checkip.dyndns.org` .

Alapszintű *Debian* Pod elindítása és csatlakoztatása:

```console
kubectl run -it --rm aks-ip --image=mcr.microsoft.com/aks/fundamental/base-ubuntu:v0.0.11
```

Webhelynek a tárolóból való eléréséhez használja a `apt-get` t `curl` a tárolóba való telepítéshez.

```console
apt-get update && apt-get install curl -y
```

Most a curl használatával férhet hozzá a *checkip.dyndns.org* webhelyhez. Megjelenik a kimenő forgalom IP-címe, ahogy az az alábbi példában is látható. Ez az IP-cím megegyezik a terheléselosztó szolgáltatáshoz létrehozott és definiált statikus nyilvános IP-címmel:

```console
$ curl -s checkip.dyndns.org

<html><head><title>Current IP Check</title></head><body>Current IP Address: 40.121.183.52</body></html>
```

## <a name="next-steps"></a>Következő lépések

Ha el szeretné kerülni a Azure Load Balancer több nyilvános IP-cím fenntartását, használhat egy bejövő vezérlőt is. A beáramlási vezérlők további előnyöket biztosítanak, például az SSL/TLS-megszakítást, az URI-újraírások támogatását és az SSL/TLS-titkosítást. További információkért lásd: [alapszintű bejövő vezérlő létrehozása az AK-ban][ingress-aks-cluster].

<!-- LINKS - internal -->
[az-network-public-ip-create]: /cli/azure/network/public-ip#az-network-public-ip-create
[az-network-public-ip-list]: /cli/azure/network/public-ip#az-network-public-ip-list
[az-aks-show]: /cli/azure/aks#az-aks-show
[azure-cli-install]: /cli/azure/install-azure-cli
[ingress-aks-cluster]: ./ingress-basic.md
[outbound-connections]: ../load-balancer/load-balancer-outbound-connections.md#scenarios
[public-ip-create]: /cli/azure/network/public-ip#az-network-public-ip-create
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[slb]: load-balancer-standard.md
