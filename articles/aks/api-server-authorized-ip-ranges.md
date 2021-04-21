---
title: API-kiszolgáló engedélyezett IP-címtartományai a Azure Kubernetes Service (AKS)
description: Ismerje meg, hogyan biztosíthatja a fürt biztonságossá a fürtöt egy IP-címtartomány használatával az API-kiszolgáló eléréséhez a Azure Kubernetes Service (AKS) szolgáltatásban
services: container-service
ms.topic: article
ms.date: 09/21/2020
ms.openlocfilehash: 8fca3fe61e26a031e6ea09692c9ba0781bfca21f
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107769642"
---
# <a name="secure-access-to-the-api-server-using-authorized-ip-address-ranges-in-azure-kubernetes-service-aks"></a>Biztonságos hozzáférés az API-kiszolgálóhoz hitelesített IP-címtartományok használatával a Azure Kubernetes Service (AKS)

A Kubernetesben az API-kiszolgáló kéréseket kap a fürtön lévő műveletek végrehajtásához, például erőforrások létrehozásához vagy a csomópontok számának skálázához. Az API-kiszolgáló a fürtök kezelésének és kezelésének központi módja. A fürtbiztonság javítása és a támadások minimalizálása érdekében az API-kiszolgálónak csak korlátozott SZÁMÚ IP-címtartományból szabad elérhetőnek lennie.

Ez a cikk bemutatja, hogyan használhatja az API-kiszolgáló engedélyezett IP-címtartományait annak korlátozására, hogy mely IP-címek és CIDR-ek férhetnek hozzá a vezérlősíkhoz.

## <a name="before-you-begin"></a>Előkészületek

Ez a cikk bemutatja, hogyan hozhat létre AKS-fürtöt az Azure CLI használatával.

Az Azure CLI 2.0.76-os vagy újabb verzióját kell telepítenie és konfigurálnia. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése][install-azure-cli].

### <a name="limitations"></a>Korlátozások

Az API-kiszolgáló Engedélyezett IP-címtartományok szolgáltatása a következő korlátozásokkal rendelkezik:
- Az API-kiszolgáló engedélyezett IP-címtartományai előzetes verzióból 2019 októberében való eltávozása után létrehozott fürtökön az API-kiszolgáló által engedélyezett IP-címtartományok csak a *Standard* termékváltozat terheléseltöltőn támogatottak. Az alapszintű *termékváltozatú* terheléselosztással és az API-kiszolgáló engedélyezett IP-címtartományokkal konfigurált meglévő fürtök továbbra is a jelenlegi módon működnek, de nem migrálhatóak *standard* termékváltozatú terheléselosztásra. Ezek a meglévő fürtök akkor is működni fognak, ha a Kubernetes-verzió vagy a vezérlősík frissítve van. Az API-kiszolgáló engedélyezett IP-címtartományai privát fürtök esetén nem támogatottak.
- Ez a szolgáltatás nem kompatibilis a csomópontonként nyilvános [IP-címet () használ fürtökön.](use-multiple-node-pools.md#assign-a-public-ip-per-node-for-your-node-pools)

## <a name="overview-of-api-server-authorized-ip-ranges"></a>Az API-kiszolgáló engedélyezett IP-címtartományának áttekintése

A Kubernetes API-kiszolgáló az alapul szolgáló Kubernetes API-k elérhetővé téve. Ez az összetevő biztosítja az interakciót a felügyeleti eszközök, például a vagy a `kubectl` Kubernetes-irányítópult számára. Az AKS egybérlős fürtvezérlő síkot biztosít dedikált API-kiszolgálóval. Alapértelmezés szerint az API-kiszolgálóhoz nyilvános IP-cím van rendelve, és a hozzáférést a Kubernetes szerepköralapú hozzáférés-vezérlésével (Kubernetes RBAC) vagy az Azure RBAC-val kell szabályozni.

A egyébként nyilvánosan elérhető AKS-vezérlősíkhoz/API-kiszolgálóhoz való hozzáférés biztonságossá tétele érdekében engedélyezheti és használhatja az engedélyezett IP-címtartományokat. Ezek az engedélyezett IP-tartományok csak meghatározott IP-címtartományok számára teszik lehetővé az API-kiszolgálóval való kommunikációt. Az API-kiszolgálónak az engedélyezett IP-címtartományok részét nem használó IP-címről le van tiltva egy kérés. Használja továbbra is a Kubernetes RBAC-t vagy az Azure RBAC-t a felhasználók és a kért műveletek jogosultságának igénylésére.

További információ az API-kiszolgálóról és más fürtösszetevőkről: [Kubernetes alapfogalmak az AKS-hez.][concepts-clusters-workloads]

## <a name="create-an-aks-cluster-with-api-server-authorized-ip-ranges-enabled"></a>AKS-fürt létrehozása engedélyezett API-kiszolgálói IP-címtartományokkal

Hozzon létre egy fürtöt [az az aks create][az-aks-create] használatával, és adja meg a paramétert az engedélyezett *`--api-server-authorized-ip-ranges`* IP-címtartományok listájának megadásához. Ezek az IP-címtartományok általában a helyszíni hálózatok vagy a nyilvános IP-címek által használt címtartományok. CIDR-tartomány megadásakor kezdje a tartomány első IP-címével. Például a *137.117.106.90/29* érvényes tartomány, de ügyeljen arra, hogy a tartomány első IP-címét adja meg, *például: 137.117.106.88/29.*

> [!IMPORTANT]
> Alapértelmezés szerint a fürt a [Standard termékváltozatú terheléselosztást][standard-sku-lb] használja, amellyel konfigurálhatja a kimenő átjárót. Ha engedélyezi az API-kiszolgáló engedélyezett IP-címtartományait a fürt létrehozása során, a fürt nyilvános IP-címe is alapértelmezés szerint engedélyezett a megadott tartományok mellett. Ha a *""* értéket adja meg, vagy nem ad meg értéket, az *`--api-server-authorized-ip-ranges`* API-kiszolgáló engedélyezett IP-címtartományai le lesznek tiltva. Vegye figyelembe, hogy ha a PowerShellt használja, az (egyenlőségjeles) használatával elkerülheti az *`--api-server-authorized-ip-ranges=""`* elemzési problémákat.

Az alábbi példa egy *myAKSCluster* nevű egycsomópontos fürtöt hoz létre a *myResourceGroup nevű erőforráscsoportban,* engedélyezett ENGEDÉLYEZETT API-kiszolgálói IP-címtartományokkal. Az engedélyezett IP-címtartományok a *következőek: 73.140.245.0/24:*

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-count 1 \
    --vm-set-type VirtualMachineScaleSets \
    --load-balancer-sku standard \
    --api-server-authorized-ip-ranges 73.140.245.0/24 \
    --generate-ssh-keys
```

> [!NOTE]
> Ezeket a tartományokat hozzá kell adni egy engedélyező listához:
> - A tűzfal nyilvános IP-címe
> - Bármely tartomány, amely azokat a hálózatokat jelöli, amelyekről a fürtöt felügyelni fogja
> - Ha az Azure Dev Spacest használja az AKS-fürtön, további tartományokat kell engedélyeznie a [régiója alapján.][dev-spaces-ranges]
>
> A megadható IP-tartományok felső korlátja 200.
>
> A szabályok propagálása akár 2 perc is lehet. A kapcsolat tesztelésekor adjon meg legfeljebb ennyi időt.

### <a name="specify-the-outbound-ips-for-the-standard-sku-load-balancer"></a>Adja meg a standard termékváltozatú terheléselosztás kimenő IP-eit

AKS-fürt létrehozásakor, ha megadja a fürt kimenő IP-címeit vagy előtagját, akkor ezek a címek vagy előtagok is engedélyezettek. Például:

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-count 1 \
    --vm-set-type VirtualMachineScaleSets \
    --load-balancer-sku standard \
    --api-server-authorized-ip-ranges 73.140.245.0/24 \
    --load-balancer-outbound-ips <publicIpId1>,<publicIpId2> \
    --generate-ssh-keys
```

A fenti példában a paraméterben megadott összes IP-pont engedélyezett a paraméterben található *`--load-balancer-outbound-ip-prefixes`* IP-ekkel *`--api-server-authorized-ip-ranges`* együtt.

Ehelyett megadhatja a paramétert a kimenő *`--load-balancer-outbound-ip-prefixes`* terheléselosztási IP-előtagok engedélyezése érdekében.

### <a name="allow-only-the-outbound-public-ip-of-the-standard-sku-load-balancer"></a>Csak a Standard termékváltozat terheléselosztásának kimenő nyilvános IP-címének engedélyezése

Ha engedélyezi az API-kiszolgáló engedélyezett IP-címtartományait a fürt létrehozása során, a fürt standard termékváltozatú terheléselosztásának kimenő nyilvános IP-címe alapértelmezés szerint az Ön által megadott tartományokon kívül is engedélyezve lesz. Ha csak a Standard termékváltozat terheléselosztásának kimenő nyilvános IP-címét engedélyezi, használja a *0.0.0.0/32* értéket a paraméter *`--api-server-authorized-ip-ranges`* megadásakor.

A következő példában csak a Standard termékváltozat terheléselosztásának kimenő nyilvános IP-címe engedélyezett, és az API-kiszolgáló csak a fürt csomópontjairól érhető el.

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-count 1 \
    --vm-set-type VirtualMachineScaleSets \
    --load-balancer-sku standard \
    --api-server-authorized-ip-ranges 0.0.0.0/32 \
    --generate-ssh-keys
```

## <a name="update-a-clusters-api-server-authorized-ip-ranges"></a>Fürt API-kiszolgálójának engedélyezett IP-címtartományok frissítése

Az API-kiszolgáló engedélyezett IP-címtartományának meglévő fürtön való frissítéséhez használja az [az aks update][az-aks-update] parancsot, és használja a *`--api-server-authorized-ip-ranges`* ,--load-balancer-outbound-ip-prefixes *, *`--load-balancer-outbound-ips`* vagy--load-balancer-outbound-ip-prefixes* paramétereket.

A következő példa frissíti a myResourceGroup nevű erőforráscsoportban található *myAKSCluster* nevű fürt *API-kiszolgálójának engedélyezett IP-címtartományát.* Az engedélyeznie kell az IP-címtartományt: *73.140.245.0/24:*

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --api-server-authorized-ip-ranges  73.140.245.0/24
```

A *0.0.0.0/32* paramétert is használhatja a paraméter megadásakor, hogy csak a Standard termékváltozatú terheléselosztás nyilvános *`--api-server-authorized-ip-ranges`* IP-címét engedélyezze.

## <a name="disable-authorized-ip-ranges"></a>Engedélyezett IP-tartományok letiltása

Az engedélyezett IP-tartományok letiltásához használja [az az aks update][az-aks-update] et, és adjon meg egy üres tartományt az API-kiszolgáló által engedélyezett IP-tartományok letiltásához. Például:

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --api-server-authorized-ip-ranges ""
```

## <a name="find-existing-authorized-ip-ranges"></a>Meglévő engedélyezett IP-címtartományok megkerese

A hitelesített IP-tartományok kereséséhez használja [az az aks show][az-aks-show] használhatja, és adja meg a fürt nevét és erőforráscsoportját. Például:

```azurecli-interactive
az aks show \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --query apiServerAccessProfile.authorizedIpRanges'
```

## <a name="update-disable-and-find-authorized-ip-ranges-using-azure-portal"></a>Az engedélyezett IP-címtartományok frissítése, letiltása és megkeres Azure Portal

Az engedélyezett IP-tartományok hozzáadásának, frissítésének, keresésének és letiltásának fenti műveletei a következő műveleteket is Azure Portal. A hozzáféréshez lépjen a Hálózat **elemre** **a** fürterőforrás menü panelének Beállítások menüjében.

:::image type="content" source="media/api-server-authorized-ip-ranges/ip-ranges-specified.PNG" alt-text="A böngészőben megjelenik a fürterőforrás hálózati beállításainak Azure Portal lapon. A &quot;megadott IP-tartomány beállítása&quot; és a &quot;Megadott IP-címtartományok&quot; beállítás ki van emelve.":::

## <a name="how-to-find-my-ip-to-include-in---api-server-authorized-ip-ranges"></a>Hogyan találhatom meg az IP-címemet, hogy szerepeltetsem a `--api-server-authorized-ip-ranges` következőben: ?

Az API-kiszolgáló innen való eléréséhez hozzá kell adni a fejlesztési gépeket, az eszközök vagy az automation IP-címeket a jóváhagyott IP-tartományok AKS-fürtök listájához. 

Egy másik lehetőség egy jumpbox konfigurálása a szükséges eszközkészlettel a tűzfal virtuális hálózatának egy külön alhálózatán belül. Ez azt feltételezi, hogy a környezet rendelkezik tűzfallal a megfelelő hálózattal, és ön hozzáadta a tűzfal IP-eket az engedélyezett tartományokhoz. Hasonlóképpen, ha kényszerített bújtatást kényszerített az AKS alhálózata és a tűzfal alhálózata között, akkor a fürt alhálózatában található jumpbox is megfelelő.

Adjon hozzá egy másik IP-címet a jóváhagyott tartományokhoz az alábbi paranccsal.

```bash
# Retrieve your IP address
CURRENT_IP=$(dig @resolver1.opendns.com ANY myip.opendns.com +short)
# Add to AKS approved list
az aks update -g $RG -n $AKSNAME --api-server-authorized-ip-ranges $CURRENT_IP/32
```

>> [!NOTE]
> A fenti példa hozzáfűzi az API-kiszolgáló engedélyezett IP-címtartományát a fürthöz. Az engedélyezett IP-tartományok letiltásához használja az az aks update használhatja, és adjon meg egy üres tartományt "". 

Másik lehetőségként használja az alábbi parancsot Windows rendszereken a nyilvános IPv4-cím lekért címéhez, vagy kövesse az [IP-cím megkeresése lépéseit.](https://support.microsoft.com/en-gb/help/4026518/windows-10-find-your-ip-address)

```azurepowershell-interactive
Invoke-RestMethod http://ipinfo.io/json | Select -exp ip
```

Ezt a címet úgy is megkeresheti, ha egy webböngészőben rákeres a "mi az IP-címem" szóra.

## <a name="next-steps"></a>Következő lépések

Ebben a cikkben engedélyezte az API-kiszolgáló engedélyezett IP-címtartományokat. Ez a megközelítés a biztonságos AKS-fürtök futtatásának egyik része.

További információkért lásd az [AKS-alkalmazások][concepts-security] és fürtök biztonsági fogalmait és az AKS-hez használható fürtbiztonsági és frissítési ajánlott [eljárásokat.][operator-best-practices-cluster-security]

<!-- LINKS - external -->
[cni-networking]: https://github.com/Azure/azure-container-networking/blob/master/docs/cni.md
[dev-spaces-ranges]: ../dev-spaces/configure-networking.md#aks-cluster-network-requirements
[kubenet]: https://kubernetes.io/docs/concepts/extend-kubernetes/compute-storage-net/network-plugins/#kubenet

<!-- LINKS - internal -->
[az-aks-update]: /cli/azure/ext/aks-preview/aks#ext-aks-preview-az-aks-update
[az-aks-create]: /cli/azure/aks#az_aks_create
[az-aks-show]: /cli/azure/aks#az_aks_show
[az-network-public-ip-list]: /cli/azure/network/public-ip#az_network_public_ip_list
[concepts-clusters-workloads]: concepts-clusters-workloads.md
[concepts-security]: concepts-security.md
[install-azure-cli]: /cli/azure/install-azure-cli
[operator-best-practices-cluster-security]: operator-best-practices-cluster-security.md
[route-tables]: ../virtual-network/manage-route-table.md
[standard-sku-lb]: load-balancer-standard.md
