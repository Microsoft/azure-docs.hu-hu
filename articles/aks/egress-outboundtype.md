---
title: Felhasználó által megadott útvonalak (UDR) testreszabása a Azure Kubernetes Service (AKS)
description: Megtudhatja, hogyan definiálhat egyéni bejövő forgalomútvonalat a Azure Kubernetes Service (AKS)
services: container-service
ms.topic: article
ms.date: 06/29/2020
ms.openlocfilehash: e9433978c8ee855ec66901c7692e4d2b59261fd3
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107773044"
---
# <a name="customize-cluster-egress-with-a-user-defined-route"></a>Fürt ki- és becslérek testreszabása User-Defined útvonalon

Az AKS-fürtből való ki- és beúsulás adott forgatókönyvekhez szabható testre. Alapértelmezés szerint az AKS kiépít egy standard termékváltozatot, Load Balancer be kell állítani és használni a forgalomhoz. Előfordulhat azonban, hogy az alapértelmezett beállítás nem felel meg az összes forgatókönyv követelményeinek, ha a nyilvános IP-k nem engedélyezettek, vagy további ugrások szükségesek a forgalomhoz.

Ez a cikk bemutatja, hogyan szabhatja testre egy fürt kivezeti útvonalát az egyéni hálózati forgatókönyvek támogatása érdekében, például amelyek nem támogatják a nyilvános IP-címeket, és amelyek megkövetelik, hogy a fürt egy hálózati virtuális berendezés (NVA) mögött legyen.

## <a name="prerequisites"></a>Előfeltételek
* Az Azure CLI 2.0.81-es vagy újabb verziója
* A vagy újabb `2020-01-01` API-verziója


## <a name="limitations"></a>Korlátozások
* Az OutboundType csak a fürt létrehozásakor határozható meg, és később nem frissíthető.
* A `outboundType` beállításhoz olyan AKS-fürtökre van szükség, amelyek és `vm-set-type` `VirtualMachineScaleSets` `load-balancer-sku` típusúak. `Standard`
* A értékre való beállításához egy felhasználó által megadott útvonalra van szükség, amely `outboundType` `UDR` érvényes kimenő kapcsolattal rendelkezik a fürthöz.
* Ha értéket ad meg, az azt jelenti, hogy a terheléselosztási eszközre irányítva a bejövő forrás IP-címe nem egyezik meg a fürt kimenő `outboundType` `UDR` célcímével. 

## <a name="overview-of-outbound-types-in-aks"></a>Az AKS kimenő típusainak áttekintése

Az AKS-fürtök egyedi típusú vagy `outboundType` típussal `loadBalancer` szabhatók `userDefinedRouting` testre.

> [!IMPORTANT]
> A kimenő típus csak a fürt kimenő forgalmára van hatással. További információ: [Bejövő vezérlők beállítása.](ingress-basic.md)

> [!NOTE]
> Saját útvonaltáblát [UDR-][byo-route-table] és Kubenet-hálózattal is használhat. Győződjön meg arról, hogy a fürtidentitás (szolgáltatásnév vagy felügyelt identitás) Közreműködői engedélyekkel rendelkezik az egyéni útvonaltáblához.

### <a name="outbound-type-of-loadbalancer"></a>A loadBalancer kimenő típusa

Ha `loadBalancer` be van állítva, az AKS automatikusan befejezi az alábbi konfigurációt. A terheléselosztás az AKS-hez hozzárendelt nyilvános IP-címeken keresztüli bejövő forgalomhoz használatos. A kimenő típusa támogatja a `loadBalancer` típusú Kubernetes-szolgáltatásokat, amelyek az AKS erőforrás-szolgáltató által létrehozott terheléseltöltőből kimenő forgalmat `loadBalancer` várnak.

A következő konfigurációt az AKS-nek kell konfigurálni.
   * A rendszer kiépít egy nyilvános IP-címet a fürt ki- és visszacsatolt forgalom számára.
   * A nyilvános IP-cím a terheléselosztási erőforráshoz van rendelve.
   * A terheléselosztás háttérkészlete a fürt ügynökcsomópontjaihoz van beállítva.

Az alábbiakban az AKS-fürtökben alapértelmezés szerint üzembe helyezett hálózati topológia látható, amely a értéket `outboundType` `loadBalancer` használja.

![Az ábra azt mutatja, hogy az I P bejövő forgalom és a bejövő forgalom P p. egy terheléselosztási kiszolgálóra irányítja a forgalmat, amely egy belső fürtre és más forgalomra irányít át, az I P pedig az internetre, az M C R-re, az Azure szükséges szolgáltatásaira és az A K S vezérlősíkra.](media/egress-outboundtype/outboundtype-lb.png)

### <a name="outbound-type-of-userdefinedrouting"></a>A userDefinedRouting kimenő típusa

> [!NOTE]
> A kimenő típus használata speciális hálózati forgatókönyv, és megfelelő hálózati konfigurációt igényel.

Ha be van állítva, az AKS nem konfigurálja automatikusan a `userDefinedRouting` bejövő útvonalakat. A forgalom beállítását Önnek kell beállítania.

Az AKS-fürtöt egy meglévő virtuális hálózatban kell üzembe helyezni egy korábban konfigurált alhálózattal, mert ha nem standard terheléselosztási (SLB) architektúrát használ, explicit ki- és be kell állítania a ki- és betöltést. Ennek az architektúrának a használatához explicit módon kell küldeni a bejövő forgalmat egy berendezésnek, például tűzfalnak, átjárónak, proxynak, vagy engedélyezni, hogy a hálózati címfordítást (NAT) a standard terheléselosztáshoz vagy berendezéshez rendelt nyilvános IP-cím tegye lehetővé.

#### <a name="load-balancer-creation-with-userdefinedrouting"></a>Terheléselosztás létrehozása a userDefinedRouting függvővel

Az UDR kimenő típusú AKS-fürtök csak az első loadBalancer típusú Kubernetes-szolgáltatás üzembe helyezésekor kapnak standard terheléseltöltőt (SLB). A terheléselosztás nyilvános IP-címmel van  konfigurálva a bejövő kérések számára, és egy háttérkészlet a bejövő *kérések* számára. A bejövő szabályokat az Azure-felhőszolgáltató konfigurálja, de nem konfigurálja a kimenő nyilvános **IP-címet** vagy a kimenő szabályokat az UDR kimenő típusa miatt. Továbbra is az UDR lesz a bejövő forgalom egyetlen forrása.

Az Azure Load Balancer nem számít fel díjat, [amíg egy szabályt nem helyez el.](https://azure.microsoft.com/pricing/details/load-balancer/)

## <a name="deploy-a-cluster-with-outbound-type-of-udr-and-azure-firewall"></a>Fürt üzembe helyezése kimenő UDR és Azure Firewall

Egy felhasználó által megadott útvonalon kimenő típusú fürt alkalmazásának szemléltetésére egy fürt konfigurálható egy virtuális hálózaton, amely saját alhálózatán Azure Firewall virtuális gépekkel. Tekintse meg ezt a példát a bejövő forgalom Azure [Firewalllal való korlátozására példát.](limit-egress-traffic.md#restrict-egress-traffic-using-azure-firewall)

> [!IMPORTANT]
> Az UDR kimenő típusa megköveteli, hogy legyen egy útvonal a 0.0.0.0/0 útvonalhoz és az NVA következő ugrási céljához (hálózati virtuális berendezés) az útvonaltáblában.
> Az útvonaltábla már rendelkezik egy alapértelmezett 0.0.0.0/0-s internettel, és a SNAT-nek nem kell nyilvános IP-címet hozzáadnia ehhez az útvonalhoz, az nem fogja biztosítani a ki- és beadást. Az AKS ellenőrzi, hogy nem az internetre, hanem NVA-ra vagy átjáróra stb. mutató 0.0.0.0/0-s útvonalat hoz-e létre. Ha kimenő típusú UDR-t használ, a terheléselelosztási nyilvános IP-cím csak akkor  jön létre a bejövő kérelmekhez, ha be van állítva terheléselegyenlő típusú szolgáltatás.  Az AKS  soha nem hoz létre nyilvános IP-címet a kimenő kérelmekhez, ha be van állítva kimenő UDR-típus.

## <a name="next-steps"></a>Következő lépések

Lásd: [Az Azure hálózati UDR áttekintése.](../virtual-network/virtual-networks-udr-overview.md)

Tekintse [meg, hogyan hozhat létre, módosíthat vagy törölhet útválasztási táblázatot.](../virtual-network/manage-route-table.md)

<!-- LINKS - internal -->
[az-aks-get-credentials]: /cli/azure/aks#az_aks_get_credentials
[byo-route-table]: configure-kubenet.md#bring-your-own-subnet-and-route-table-with-kubenet
