---
title: Felhasználó által megadott útvonalak (UDR-EK) testreszabása az Azure Kubernetes szolgáltatásban (ak)
description: Ismerje meg, hogyan határozhat meg egyéni kimenő útvonalakat az Azure Kubernetes szolgáltatásban (ak)
services: container-service
ms.topic: article
ms.date: 06/29/2020
ms.openlocfilehash: 72ba90510afb00ee001c97612e88f452039f53a4
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2021
ms.locfileid: "102182128"
---
# <a name="customize-cluster-egress-with-a-user-defined-route"></a>A fürt kilépésének testreszabása User-Defined útvonalon

Az AK-fürtökből való kilépések testreszabhatók az adott forgatókönyvek kihasználása érdekében. Alapértelmezés szerint az AK kiépít egy szabványos SKU-Load Balancer, amelyet a kimenő forgalomhoz kell beállítani és használni. Előfordulhat azonban, hogy az alapértelmezett beállítás nem teljesíti az összes forgatókönyv követelményeit, ha a nyilvános IP-címek nem megengedettek, vagy további ugrásokra van szükség a kimenő forgalomhoz.

Ez a cikk bemutatja, hogyan szabhatja testre a fürt kimenő útvonalát az egyéni hálózati forgatókönyvek támogatásához, például a nyilvános IP-címek használatát, és a fürtnek a hálózati virtuális berendezés (NVA) mögött kell lennie.

## <a name="prerequisites"></a>Előfeltételek
* Az Azure CLI verziója 2.0.81 vagy újabb
* API-verziója `2020-01-01` vagy nagyobb


## <a name="limitations"></a>Korlátozások
* A OutboundType csak a fürt létrehozási idején definiálható, és ezt követően nem lehet frissíteni.
* A beállításhoz a `outboundType` és a rendszerhez tartozó AK-fürtök szükségesek `vm-set-type` `VirtualMachineScaleSets` `load-balancer-sku` `Standard` .
* A `outboundType` érték beállításához a `UDR` fürthöz érvényes kimenő kapcsolattal rendelkező felhasználó által megadott útvonal szükséges.
* Az `outboundType` értékre való beállítás azt jelenti, hogy a `UDR` bemenő forrás IP-címe, amely a terheléselosztó felé van átirányítva, előfordulhat, hogy **nem felel** meg a fürt kimenő kilépési céljának.

## <a name="overview-of-outbound-types-in-aks"></a>A kimenő típusok áttekintése az AK-ban

Az AK-fürtök testreszabhatók egyedi `outboundType` típussal `loadBalancer` vagy `userDefinedRouting` .

> [!IMPORTANT]
> A kimenő típus csak a fürt kimenő forgalmára van hatással. További információ: a [beáramló vezérlők beállítása](ingress-basic.md).

> [!NOTE]
> Saját [útválasztási táblázatot][byo-route-table] is használhat UDR és kubenet hálózatkezeléssel. Győződjön meg arról, hogy a fürt identitása (egyszerű szolgáltatásnév vagy felügyelt identitás) közreműködői engedélyekkel rendelkezik az egyéni útválasztási táblázathoz.

### <a name="outbound-type-of-loadbalancer"></a>A terheléselosztó kimenő típusa

Ha `loadBalancer` be van állítva, az AK a következő konfigurációt automatikusan végrehajtja. A terheléselosztó egy AK-beli hozzárendelt nyilvános IP-címen keresztüli kimenő forgalomhoz használatos. Egy kimenő típus `loadBalancer` támogatja a típusú Kubernetes-szolgáltatásokat `loadBalancer` , ami várhatóan kilép az AK erőforrás-szolgáltató által létrehozott terheléselosztó alól.

A következő konfigurációt az AK hajtja végre.
   * Nyilvános IP-cím van kiépítve a fürt kimenő forgalmához.
   * A rendszer a terheléselosztó erőforráshoz rendeli a nyilvános IP-címet.
   * A terheléselosztó backend-készletei a fürtben lévő ügynökök csomópontjain vannak beállítva.

Az alábbiakban egy olyan hálózati topológia található, amely alapértelmezésben egy AK-alapú fürtbe van telepítve, amely a-t használja `outboundType` `loadBalancer` .

![Ábrán látható a bejövő i P és a kimenő I p, ahol a bejövő I P egy terheléselosztó felé irányítja át a forgalmat, amely egy belső fürtre irányuló, illetve a kimenő I P-re irányuló egyéb forgalomra irányítja a forgalmat, amely az internetre, az M C R-re, az Azure-ra vonatkozó szükséges szolgáltatásokra és az a K-vezérlési síkra](media/egress-outboundtype/outboundtype-lb.png)

### <a name="outbound-type-of-userdefinedrouting"></a>A userDefinedRouting kimenő típusa

> [!NOTE]
> A kimenő típus használata fejlett hálózati forgatókönyv, és megfelelő hálózati konfigurációt igényel.

Ha `userDefinedRouting` be van állítva, az AK nem konfigurálja automatikusan a kimenő útvonalakat. A kimenő telepítést Önnek kell elvégeznie.

Az AK-fürtöt egy korábban konfigurált alhálózattal rendelkező meglévő virtuális hálózatba kell telepíteni, mert ha nem használ standard Load Balancer (SLB) architektúrát, explicit kimenő forgalmat kell létrehoznia. Ebben az architektúrában explicit módon kell elküldeni a kimenő forgalmat egy olyan készülékre, mint például a tűzfal, az átjáró, a proxy vagy a hálózati címfordítás (NAT) a standard Load Balancerhez vagy berendezéshez rendelt nyilvános IP-cím használatával.

#### <a name="load-balancer-creation-with-userdefinedrouting"></a>Terheléselosztó létrehozása a userDefinedRouting

A kimenő UDR rendelkező AK-fürtök csak akkor kapnak standard Load balancert (SLB), ha az első, "terheléselosztó" típusú Kubernetes szolgáltatás telepítve van. A terheléselosztó nyilvános IP-címmel van konfigurálva a *bejövő* kérelmekhez, valamint egy háttérbeli készlet a *bejövő* kérésekhez. A bejövő szabályokat az Azure Cloud Provider konfigurálja, de a kimenő **nyilvános IP-címek és a kimenő szabályok nem** konfigurálhatók a kimenő UDR. A UDR továbbra is az egyetlen forrása a kimenő forgalomnak.

Az Azure Load Balancer [nem számít fel díjat, amíg meg nem kerül egy szabály](https://azure.microsoft.com/pricing/details/load-balancer/).

## <a name="deploy-a-cluster-with-outbound-type-of-udr-and-azure-firewall"></a>Fürt üzembe helyezése kimenő UDR-típussal és Azure Firewall

Egy felhasználó által megadott útvonal használatával a kimenő típusú fürt alkalmazásának szemléltetéséhez egy fürt konfigurálható egy, a saját alhálózatán lévő Azure Firewall rendelkező virtuális hálózaton. Tekintse meg ezt a példát a [kimenő forgalom korlátozása az Azure Firewall-mel – példa](limit-egress-traffic.md#restrict-egress-traffic-using-azure-firewall).

> [!IMPORTANT]
> A UDR kimenő típusa megköveteli, hogy az útválasztási táblázatban a 0.0.0.0/0 és a következő ugrási cél NVA (hálózati virtuális berendezés) legyen.
> Az útválasztási táblázathoz már tartozik egy alapértelmezett 0.0.0.0/0 az internetre, anélkül, hogy a nyilvános IP-cím SNAT csak ezt az útvonalat adja hozzá, a kimenő forgalom nem fog megjelenni. Az AK ellenőrzi, hogy nem hoz létre 0.0.0.0/0 útvonalat az interneten, hanem NVA vagy átjáróként, stb. A UDR kimenő típusának használatakor a rendszer nem hoz létre terheléselosztó nyilvános IP-címet a **bejövő kérelmekhez** , kivéve, ha a *terheléselosztó* típusú szolgáltatások konfigurálva vannak. A **kimenő kérésekhez** tartozó nyilvános IP-címet soha nem hozza létre a rendszer, ha a UDR kimenő típusa van beállítva.

## <a name="next-steps"></a>Következő lépések

Lásd: [Azure Networking UDR – áttekintés](../virtual-network/virtual-networks-udr-overview.md).

Lásd: [útválasztási táblázat létrehozása, módosítása vagy törlése](../virtual-network/manage-route-table.md).

<!-- LINKS - internal -->
[az-aks-get-credentials]: /cli/azure/aks#az-aks-get-credentials
[byo-route-table]: configure-kubenet.md#bring-your-own-subnet-and-route-table-with-kubenet
