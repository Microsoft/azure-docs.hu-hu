---
title: Az API-kiszolgáló által jóváhagyott IP-címtartományok az Azure Kubernetes szolgáltatásban (ak)
description: Ismerje meg, hogyan biztonságossá teheti a fürtöt IP-címtartomány használatával az Azure Kubernetes szolgáltatásban (ak) lévő API-kiszolgálóhoz való hozzáféréshez
services: container-service
ms.topic: article
ms.date: 09/21/2020
ms.openlocfilehash: ca6e1c06b3ad90ef12c9bf375bae50d46c5f7c37
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98890636"
---
# <a name="secure-access-to-the-api-server-using-authorized-ip-address-ranges-in-azure-kubernetes-service-aks"></a>Biztonságos hozzáférés az API-kiszolgálóhoz a jogosult IP-címtartományok használatával az Azure Kubernetes szolgáltatásban (ak)

A Kubernetes-ben az API-kiszolgáló kérelmeket fogad a fürt műveleteinek végrehajtásához, például erőforrások létrehozásához vagy a csomópontok számának skálázásához. Az API-kiszolgáló a fürttel való kommunikáció és felügyelet központi módja. A fürt biztonságának növelése és a támadások csökkentése érdekében az API-kiszolgálónak csak korlátozott IP-címtartományok közül kell elérhetőnek lennie.

Ebből a cikkből megtudhatja, hogyan használható az API-kiszolgáló által engedélyezett IP-címtartományok annak a korlátozására, hogy mely IP-címek és CIDRs férhetnek hozzá a vezérlő síkja.

## <a name="before-you-begin"></a>Előkészületek

Ez a cikk bemutatja, hogyan hozhat létre egy AK-fürtöt az Azure CLI használatával.

Szüksége lesz az Azure CLI-verzió 2.0.76 vagy újabb verziójára, és konfigurálva van. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése][install-azure-cli].

### <a name="limitations"></a>Korlátozások

Az API-kiszolgáló által jóváhagyott IP-címtartományok funkció a következő korlátozásokkal rendelkezik:
- Az API-kiszolgáló által engedélyezett IP-címtartományok után létrehozott fürtökön a 2019 októberi időszakban az API-kiszolgáló által engedélyezett IP-címtartományok csak a *standard* SKU Load Balancer esetében támogatottak. Az *alapszintű* SKU Load Balancer és a konfigurált API-kiszolgáló által jóváhagyott IP-címtartományok meglévő fürtök továbbra is ugyanúgy működnek, mint a *standard* SKU Load Balancer. Ezek a meglévő fürtök továbbra is működni fognak, ha a Kubernetes verziója vagy a vezérlő síkja frissül. Az API-kiszolgáló által engedélyezett IP-címtartományok a privát fürtök esetében nem támogatottak.
- Ez a funkció nem kompatibilis azokkal a fürtökkel, amelyek [nyilvános IP-t használnak a csomóponti csomópontok készletének előzetes](use-multiple-node-pools.md#assign-a-public-ip-per-node-for-your-node-pools-preview)verziójában.

## <a name="overview-of-api-server-authorized-ip-ranges"></a>Az API-kiszolgáló által jóváhagyott IP-címtartományok áttekintése

A Kubernetes API-kiszolgáló az alapul szolgáló Kubernetes API-k számára elérhető. Ez az összetevő a felügyeleti eszközök, például `kubectl` a vagy a Kubernetes irányítópultjának interakcióját biztosítja. Az AK egy egybérlős fürtbeli vezérlési síkot biztosít dedikált API-kiszolgálóval. Alapértelmezés szerint az API-kiszolgáló egy nyilvános IP-címet kap, és a hozzáférést a Kubernetes szerepköralapú hozzáférés-vezérlés (Kubernetes RBAC) vagy az Azure RBAC használatával szabályozhatja.

Az egyébként nyilvánosan elérhető AK-vezérlési sík/API-kiszolgáló hozzáférésének biztonságossá tételéhez engedélyezheti és használhatja az engedélyezett IP-tartományokat. Ezek a megengedett IP-címtartományok csak a meghatározott IP-címtartományok használatát teszik lehetővé az API-kiszolgálóval való kommunikációhoz. Az API-kiszolgálónak egy olyan IP-címről érkező kérés le van tiltva, amely nem része ezeknek az engedélyezett IP-tartományoknak. Folytassa a Kubernetes RBAC vagy az Azure RBAC használatával a felhasználók és az általuk kért műveletek engedélyezéséhez.

További információ az API-kiszolgálóról és az egyéb fürt-összetevőkről: [Kubernetes Core Concepts for AK][concepts-clusters-workloads].

## <a name="create-an-aks-cluster-with-api-server-authorized-ip-ranges-enabled"></a>AK-fürt létrehozása az API-kiszolgáló által engedélyezett IP-címtartományok engedélyezve

Hozzon létre egy fürtöt az [az AK Create][az-aks-create] paranccsal, és adja meg a *`--api-server-authorized-ip-ranges`* paramétert a jóváhagyott IP-címtartományok listájának megadásához. Ezek az IP-címtartományok általában a helyszíni hálózatok vagy a nyilvános IP-címek által használt címtartományok. CIDR-tartomány megadásakor a tartomány első IP-címével kezdjen el. Például a *137.117.106.90/29* egy érvényes tartomány, de győződjön meg arról, hogy az első IP-címet adta meg a tartományban, például *137.117.106.88/29*.

> [!IMPORTANT]
> Alapértelmezés szerint a fürt a [standard SKU Load balancert][standard-sku-lb] használja, amelyet a kimenő átjáró konfigurálására használhat. Ha engedélyezi az API-kiszolgálók engedélyezett IP-tartományait a fürt létrehozása során, a fürt nyilvános IP-címe alapértelmezés szerint is engedélyezett a megadott tartományokon kívül. Ha a *""* vagy a nem értéket ad meg *`--api-server-authorized-ip-ranges`* , az API-kiszolgáló által engedélyezett IP-címtartományok le lesznek tiltva. Vegye figyelembe, hogy ha a PowerShellt használja, használja az *`--api-server-authorized-ip-ranges=""`* (egyenlőségjel) lehetőséget az elemzési problémák elkerüléséhez.

A következő példa egy *myAKSCluster* nevű egycsomópontos fürtöt hoz létre az *myResourceGroup* nevű erőforráscsoport és az API-kiszolgáló által engedélyezett IP-címtartományok használatával. Az IP-címtartományok megengedett *73.140.245.0/24*:

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
> Adja hozzá ezeket a tartományokat egy engedélyezési listához:
> - A tűzfal nyilvános IP-címe
> - Bármely tartomány, amely azokat a hálózatokat képviseli, amelyekről a fürtöt felügyelni szeretné
> - Ha az AK-fürtön az Azure dev Spaces szolgáltatást használja, akkor a [régión alapuló további tartományokat][dev-spaces-ranges]is engedélyeznie kell.
>
> A megadható IP-címtartományok felső határa 200.
>
> A szabályok akár a 2perc is eltarthat. A kapcsolódás tesztelésekor adja meg a megadott időt.

### <a name="specify-the-outbound-ips-for-the-standard-sku-load-balancer"></a>A standard SKU Load Balancer kimenő IP-címeinek megadása

AK-fürt létrehozásakor, ha megadja a fürt kimenő IP-címeit vagy előtagjait, akkor ezek a címek vagy előtagok is engedélyezettek. Például:

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

A fenti példában a paraméterben megadott összes IP-cím *`--load-balancer-outbound-ip-prefixes`* engedélyezett a paraméterben található IP-címekkel együtt *`--api-server-authorized-ip-ranges`* .

Ehelyett megadhatja a *`--load-balancer-outbound-ip-prefixes`* paramétert a kimenő terheléselosztó IP-előtagjainak engedélyezéséhez.

### <a name="allow-only-the-outbound-public-ip-of-the-standard-sku-load-balancer"></a>Csak a standard SKU Load Balancer kimenő nyilvános IP-címének engedélyezése

Ha engedélyezi az API-kiszolgáló engedélyezett IP-tartományait a fürt létrehozása során, akkor a fürt szabványos SKU-Load Balancer kimenő nyilvános IP-címe is alapértelmezés szerint engedélyezett a megadott tartományokon kívül. Ha csak a standard SKU Load Balancer kimenő nyilvános IP-címét szeretné engedélyezni, a paraméter megadásakor a *0.0.0.0/32* protokollt használja *`--api-server-authorized-ip-ranges`* .

A következő példában csak a standard SKU Load Balancer kimenő nyilvános IP-címe engedélyezett, és csak a fürt csomópontjain található API-kiszolgáló férhet hozzá.

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

## <a name="update-a-clusters-api-server-authorized-ip-ranges"></a>Fürt API-kiszolgálójának IP-tartományának frissítése

Ha egy meglévő fürtön szeretné frissíteni az API-kiszolgáló által jóváhagyott IP-tartományokat, használja az [az AK Update][az-aks-update] parancsot, és használja a *`--api-server-authorized-ip-ranges`* ,--Load-Balancer-kimenő-IP-előtag *, *`--load-balancer-outbound-ips`* , vagy--Load-Balancer-kimenő-IP-előtagok* paramétereit.

Az alábbi példa frissíti az API-kiszolgáló által jóváhagyott IP-tartományokat a *myAKSCluster* nevű fürtön a *myResourceGroup* nevű erőforráscsoport alatt. Az engedélyezni kívánt IP-címtartomány a *73.140.245.0/24*:

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --api-server-authorized-ip-ranges  73.140.245.0/24
```

A *0.0.0.0/32* -et is használhatja a paraméter megadásával *`--api-server-authorized-ip-ranges`* , hogy csak a standard SKU Load Balancer nyilvános IP-címét engedélyezze.

## <a name="disable-authorized-ip-ranges"></a>A hitelesítő IP-címtartományok letiltása

Az engedélyezett IP-címtartományok letiltásához használja az [az AK Update][az-aks-update] parancsot, és az API-kiszolgáló által engedélyezett IP-címtartományok letiltásához használjon üres tartományt. Például:

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --api-server-authorized-ip-ranges ""
```

## <a name="find-existing-authorized-ip-ranges"></a>Meglévő, jóváhagyott IP-címtartományok keresése

Az engedélyezni kívánt IP-címtartományok megkereséséhez használja az [az AK show][az-aks-show] nevet, és adja meg a fürt nevét és az erőforráscsoportot. Például:

```azurecli-interactive
az aks show \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --query apiServerAccessProfile.authorizedIpRanges'
```

## <a name="update-disable-and-find-authorized-ip-ranges-using-azure-portal"></a>Hitelesítő IP-címtartományok frissítése, letiltása és keresése Azure Portal használatával

Az adott IP-címtartományok hozzáadásával, frissítésével, megkeresésével és letiltásával kapcsolatos műveletek a Azure Portalon is elvégezhetők. A hozzáféréshez navigáljon a **hálózat** menüpontra a fürterőforrás menüjének **Beállítások** területén.

:::image type="content" source="media/api-server-authorized-ip-ranges/ip-ranges-specified.PNG" alt-text="A böngészőben a fürterőforrás hálózati beállítások Azure Portal lapja látható. A &quot;megadott IP-címtartomány beállítása&quot; és a &quot;megadott IP-címtartományok&quot; beállítás ki van emelve.":::

## <a name="how-to-find-my-ip-to-include-in---api-server-authorized-ip-ranges"></a>Hogyan találhatom meg az IP-címet, hogy szerepeljen a alkalmazásban `--api-server-authorized-ip-ranges` ?

Az API-kiszolgáló innen való eléréséhez hozzá kell adnia a fejlesztői gépeket, az eszközök vagy az Automation IP-címeit a jóváhagyott IP-tartományok AK-beli fürtjének listájához. 

Egy másik lehetőség, hogy a tűzfal virtuális hálózatán belül egy külön alhálózaton belüli Jumpbox konfigurálja a szükséges eszközökkel. Ez azt feltételezi, hogy a környezete tűzfallal rendelkezik a megfelelő hálózattal, és a tűzfal IP-címei a jogosult tartományokhoz lettek adva. Hasonlóképpen, ha úgy döntött, hogy az AK-alhálózatról a tűzfal alhálózatára kényszerített bújtatást, akkor a fürt alhálózatán lévő Jumpbox is rendben van.

Adjon hozzá egy másik IP-címet a jóváhagyott tartományokhoz a következő paranccsal.

```bash
# Retrieve your IP address
CURRENT_IP=$(dig @resolver1.opendns.com ANY myip.opendns.com +short)
# Add to AKS approved list
az aks update -g $RG -n $AKSNAME --api-server-authorized-ip-ranges $CURRENT_IP/32
```

>> [!NOTE]
> A fenti példa hozzáfűzi az API-kiszolgáló által jóváhagyott IP-tartományokat a fürthöz. Az engedélyezett IP-címtartományok letiltásához használja az az AK Update parancsot, és határozzon meg egy üres tartományt. 

Egy másik lehetőség az alábbi parancs használata Windows rendszereken a nyilvános IPv4-cím lekéréséhez, vagy az [IP-cím megkeresése](https://support.microsoft.com/en-gb/help/4026518/windows-10-find-your-ip-address)című cikkben ismertetett lépéseket követve.

```azurepowershell-interactive
Invoke-RestMethod http://ipinfo.io/json | Select -exp ip
```

Ezt a címet a "mi az IP-cím" kifejezéssel keresheti meg egy böngészőben.

## <a name="next-steps"></a>Következő lépések

Ebben a cikkben engedélyezte az API-kiszolgáló engedélyezett IP-tartományait. Ez a megközelítés a biztonságos AK-fürtök futtatásának egyik része.

További információ: az [AK-ban található alkalmazásokhoz és fürtökhöz tartozó biztonsági fogalmak][concepts-security] , valamint a [fürt biztonságára és az AK-ban való frissítésére vonatkozó ajánlott eljárások][operator-best-practices-cluster-security].

<!-- LINKS - external -->
[cni-networking]: https://github.com/Azure/azure-container-networking/blob/master/docs/cni.md
[dev-spaces-ranges]: ../dev-spaces/configure-networking.md#aks-cluster-network-requirements
[kubenet]: https://kubernetes.io/docs/concepts/extend-kubernetes/compute-storage-net/network-plugins/#kubenet

<!-- LINKS - internal -->
[az-aks-update]: /cli/azure/ext/aks-preview/aks#ext-aks-preview-az-aks-update
[az-aks-create]: /cli/azure/aks#az-aks-create
[az-aks-show]: /cli/azure/aks#az_aks_show
[az-network-public-ip-list]: /cli/azure/network/public-ip#az-network-public-ip-list
[concepts-clusters-workloads]: concepts-clusters-workloads.md
[concepts-security]: concepts-security.md
[install-azure-cli]: /cli/azure/install-azure-cli
[operator-best-practices-cluster-security]: operator-best-practices-cluster-security.md
[route-tables]: ../virtual-network/manage-route-table.md
[standard-sku-lb]: load-balancer-standard.md
