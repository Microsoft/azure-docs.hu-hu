---
title: Az Azure CNI hálózatkezelés konfigurálása az Azure Kubernetes szolgáltatásban (ak)
description: Ismerje meg, hogyan konfigurálhatja az Azure CNI (Advanced) hálózatkezelést az Azure Kubernetes szolgáltatásban (ak), beleértve az AK-fürtök meglévő virtuális hálózatba és alhálózatba való telepítését is.
services: container-service
ms.topic: article
ms.date: 06/03/2019
ms.openlocfilehash: 17778c367eb731a7e41f5017c3ae630dc152454e
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "82207496"
---
# <a name="configure-azure-cni-networking-in-azure-kubernetes-service-aks"></a>Az Azure CNI hálózatkezelés konfigurálása az Azure Kubernetes szolgáltatásban (ak)

Alapértelmezés szerint az AK-fürtök a [kubenet][kubenet]-t használják, és létrehoznak egy virtuális hálózatot és egy alhálózatot. A *kubenet*a csomópontok egy virtuális hálózati alhálózatból kapnak IP-címet. A hálózati címfordítás (NAT) ezután konfigurálva van a csomópontokon, és a hüvelyek a csomópont IP-címe mögött "rejtett" IP-címet kapnak. Ezzel a módszerrel csökkenthető az IP-címek száma, amelyeket a hüvelyek számára a hálózati térben le kell foglalni.

Az [Azure Container Network Interface (CNI)][cni-networking]használatával minden Pod IP-címet kap az alhálózatból, és közvetlenül elérhető. Ezeknek az IP-címeknek egyedinek kell lenniük a hálózati térben, és előre kell tervezni. Mindegyik csomóponthoz tartozik egy konfigurációs paraméter az általa támogatott hüvelyek maximális számához. A csomópontok azonos számú IP-címet, majd az adott csomópont előtt fenntartanak. Ennek a megközelítésnek több megtervezésre van szüksége, és gyakran az IP-címek kimerülését, illetve a fürtök egy nagyobb alhálózaton való újraépítését igényli, mivel az alkalmazás által igényelt növekedés.

Ebből a cikkből megtudhatja, hogyan hozhat létre és használhat az *Azure CNI* hálózatkezelési szolgáltatásával virtuális hálózati alhálózatot egy AK-fürthöz. A hálózati beállításokkal és a szempontokkal kapcsolatos további információkért lásd: [hálózati fogalmak a Kubernetes és az AK][aks-network-concepts]-hoz.

## <a name="prerequisites"></a>Előfeltételek

* Az AK-fürthöz tartozó virtuális hálózatnak engedélyeznie kell a kimenő internetkapcsolatot.
* Az AK-fürtök nem `169.254.0.0/16` `172.30.0.0/16`használhatnak `172.31.0.0/16`,, `192.0.2.0/24` vagy a Kubernetes szolgáltatási címtartomány esetében.
* Az AK-fürt által használt egyszerű szolgáltatásnak legalább [hálózati közreműködői](../role-based-access-control/built-in-roles.md#network-contributor) engedélyekkel kell rendelkeznie a virtuális hálózaton belüli alhálózaton. Ha [Egyéni szerepkört](../role-based-access-control/custom-roles.md) szeretne definiálni a beépített hálózati közreműködő szerepkör használata helyett, a következő engedélyek szükségesek:
  * `Microsoft.Network/virtualNetworks/subnets/join/action`
  * `Microsoft.Network/virtualNetworks/subnets/read`
* Egyszerű szolgáltatásnév helyett használhatja a rendszerhez rendelt felügyelt identitást az engedélyekhez. További információ: [felügyelt identitások használata](use-managed-identity.md).
* Az AK-csomóponti készlethez rendelt alhálózat nem lehet [delegált alhálózat](../virtual-network/subnet-delegation-overview.md).

## <a name="plan-ip-addressing-for-your-cluster"></a>A fürt IP-címzésének megtervezése

Az Azure CNI hálózatkezeléssel konfigurált fürtök további tervezést igényelnek. A virtuális hálózat és az alhálózata méretének meg kell felelnie a futtatni kívánt hüvelyek számának és a fürt csomópontjainak.

A hüvelyek és a fürt csomópontjainak IP-címei a virtuális hálózaton belüli megadott alhálózathoz vannak rendelve. Mindegyik csomópont elsődleges IP-címmel van konfigurálva. Alapértelmezés szerint 30 további IP-cím előre konfigurálva van az Azure CNI, amelyek a csomóponton ütemezett hüvelyekhez vannak rendelve. Ha kibővíti a fürtöt, az egyes csomópontok Hasonlóképpen az alhálózat IP-címeivel vannak konfigurálva. Megtekintheti a [maximális hüvelyek/csomópontok számát](#maximum-pods-per-node)is.

> [!IMPORTANT]
> A szükséges IP-címek számának tartalmaznia kell a frissítési és skálázási műveletek szempontjait. Ha úgy állítja be az IP-címtartományt, hogy csak a rögzített számú csomópontot támogassa, a fürt nem frissíthető és nem méretezhető.
>
> - Az AK-fürt **frissítésekor** a rendszer egy új csomópontot telepít a fürtbe. A szolgáltatások és a munkaterhelések az új csomóponton futnak, és a rendszer eltávolítja egy régebbi csomópontot a fürtből. Ennek a működés közbeni frissítési folyamatnak legalább egy további IP-blokkot kell használnia. Ezután `n + 1`a csomópontok száma megtörténik.
>   - Ez a megfontolás különösen fontos a Windows Server-csomópontok készletének használatakor. Az AK-beli Windows Server-csomópontok nem alkalmazzák automatikusan a Windows-frissítéseket, hanem a csomópont-készleten végeznek frissítést. Ez a frissítés új csomópontokat helyez üzembe a legújabb Window Server 2019 Alapcsomópont-lemezkép és biztonsági javítások alapján. A Windows Server-csomópontok készletének frissítésével kapcsolatos további információkért lásd: [csomópont-készlet frissítése az AK-ban][nodepool-upgrade].
>
> - AK-fürtök **skálázásakor** a rendszer egy új csomópontot telepít a fürtbe. A szolgáltatások és a munkaterhelések az új csomóponton futnak. Az IP-címtartományt figyelembe kell vennie, hogyan érdemes felmérni a fürt által támogatott csomópontok és hüvelyek számát. A frissítési műveletekhez egy további csomópontot is bele kell foglalni. Ezután `n + number-of-additional-scaled-nodes-you-anticipate + 1`a csomópontok száma megtörténik.

Ha azt szeretné, hogy a csomópontok a maximális számú hüvelyt futtassák, és a hüvelyek rendszeres megsemmisítését és üzembe helyezését is lehetővé teszi, akkor a csomópontok további IP-címeit is érdemes figyelembe venni Ezek a további IP-címek figyelembe veszik, hogy a szolgáltatás törölhető, és az új szolgáltatás üzembe helyezésének és a cím megvásárlásának IP-címe is eltarthat.

Az AK-fürtök IP-címének csomagja egy virtuális hálózatból, a csomópontok és hüvelyek legalább egy alhálózatból, valamint egy Kubernetes-szolgáltatási címtartományből áll.

| Címtartomány/Azure-erőforrás | Korlátok és méretezés |
| --------- | ------------- |
| Virtuális hálózat | Az Azure-beli virtuális hálózat lehet olyan nagy, mint/8, de 65 536 konfigurált IP-címekre van korlátozva. |
| Alhálózat | Elég nagynak kell lennie ahhoz, hogy megfeleljen a fürtben esetlegesen kiépített csomópontoknak, hüvelyeknek és az összes Kubernetes és Azure-erőforrásnak. Ha például belső Azure Load Balancer telepít, az előtér-IP-címek a fürt alhálózatán vannak lefoglalva, nem nyilvános IP-címek. Az alhálózat méretének figyelembe kell vennie a frissítési műveleteket vagy a jövőbeli méretezési igényeket is.<p />Az alhálózat *minimális* méretének kiszámításához, beleértve egy további csomópontot a frissítési műveletekhez:`(number of nodes + 1) + ((number of nodes + 1) * maximum pods per node that you configure)`<p/>Példa 50 csomópontos fürtre: `(51) + (51  * 30 (default)) = 1,581` (/21 vagy nagyobb)<p/>Példa egy 50 csomópontos fürtre, amely a további 10 csomópontok vertikális felskálázását `(61) + (61 * 30 (default)) = 1,891` is magában foglalja: (/21 vagy nagyobb)<p>Ha a fürt létrehozásakor nem ad meg maximális számú hüvelyt egy csomóponton, a csomópontok maximális száma *30*értékre van állítva. Az IP-címek minimálisan szükséges száma az adott értéken alapul. Ha az IP-címek minimális követelményeit eltérő maximális értékre számítja ki, tekintse meg a következő témakört: [a hüvelyek maximális számának beállítása](#configure-maximum---new-clusters) a fürt telepítésekor ezt az értéket. |
| A Kubernetes Service címtartománya | Ezt a tartományt nem szabad az ehhez a virtuális hálózathoz csatlakoztatott bármely hálózati elemhez használni. A szolgáltatási címek CIDR kisebbnek kell lennie, mint/12. Ezt a tartományt a különböző AK-fürtökön is felhasználhatja. |
| A Kubernetes DNS-szolgáltatás IP-címe | Az Kubernetes szolgáltatási címtartomány azon IP-címe, amelyet a fürtszolgáltatás-felderítés (Kube-DNS) használ majd. Ne használja az első IP-címet a címtartományból, például. 1. Az alhálózat tartományának első címe a *kubernetes. default. SVC. cluster. local* címen található. |
| Docker-híd címe | A Docker-híd hálózati címe az összes Docker-telepítésben található alapértelmezett *docker0* híd hálózati címe. Habár a *docker0* híd nem használatos az AK-fürtök vagy maguk a hüvelyek számára, a címnek úgy kell beállítania, hogy továbbra is támogassa az olyan forgatókönyveket, mint például a *Docker Build* az AK-fürtön belül. A Docker-híd hálózati címéhez ki kell választania egy CIDR, mert máskülönben a Docker automatikusan kiválaszt egy alhálózatot, ami ütközik más CIDRs. Olyan címtartományt kell választania, amely nem ütközik a hálózatok többi CIDRs, beleértve a fürt Service CIDR és a pod CIDR. A 172.17.0.1/16 alapértelmezett értéke. Ezt a tartományt a különböző AK-fürtökön is felhasználhatja. |

## <a name="maximum-pods-per-node"></a>Hüvelyek maximális száma/csomópont

A hüvelyek maximális száma egy AK-fürtben 250. A *kubenet* és az *Azure CNI* Hálózatkezelés és a fürt üzembe helyezése közötti alapértelmezett maximális szám a hüvelyek száma *szerint* változik.

| Üzembe helyezési módszer | Kubenet alapértelmezett értéke | Alapértelmezett Azure-CNI | Konfigurálható az üzembe helyezéskor |
| -- | :--: | :--: | -- |
| Azure CLI | 110 | 30 | Igen (legfeljebb 250) |
| Resource Manager-sablon | 110 | 30 | Igen (legfeljebb 250) |
| Portál | 110 | 30 | Nem |

### <a name="configure-maximum---new-clusters"></a>Maximális – új fürtök konfigurálása

A hüvelyek maximális számát *csak a fürt központi telepítésének idejére*lehet konfigurálni. Ha az Azure CLI-vel vagy egy Resource Manager-sablonnal végzi a telepítést, a csomópontok maximális számát a 250 értékre állíthatja.

A csomópontok maximális számaként megadott minimális érték kényszerítve van, hogy a rendszer a fürt állapotának kritikus fontosságú területét biztosítsa. A maximális hüvelyek esetében beállítható minimális érték 10, ha és csak akkor, ha az egyes csomópontok készletének konfigurációja legalább 30 hüvelyből áll. Például a maximális hüvelyek/csomópontok minimum 10 értékre való beállítása megköveteli, hogy minden egyes csomópont-készlet legalább 3 csomóponttal rendelkezzen. Ez a követelmény minden létrehozott új csomópont-készletre vonatkozik, így ha a 10 a csomópontok maximális hüvelye van definiálva, minden további hozzáadott csomópontnak legalább 3 csomóponttal kell rendelkeznie.

| Hálózat | Minimális | Maximum |
| -- | :--: | :--: |
| Azure-CNI | 10 | 250 |
| Kubenet | 10 | 110 |

> [!NOTE]
> A fenti táblázatban szereplő minimális értéket szigorúan kényszeríti az AK szolgáltatás. Nem állíthat be olyan maxPods értéket, amely kisebb, mint a minimálisan megjelenő érték, így megakadályozhatja, hogy a fürt el tudja kezdeni.

* **Azure CLI**: határozza meg `--max-pods` az argumentumot, amikor üzembe helyez egy fürtöt az az [AK Create][az-aks-create] paranccsal. A maximális érték 250.
* **Resource Manager-sablon**: a `maxPods` [ManagedClusterAgentPoolProfile] objektumban megadhatja a tulajdonságot, amikor Resource Manager-sablonnal telepít egy fürtöt. A maximális érték 250.
* **Azure Portal**: a többcsomópontos hüvelyek maximális száma nem módosítható, ha fürtöt telepít a Azure Portal. Az Azure CNI hálózatkezelési fürtök a Azure Portal használatával történő üzembe helyezéskor a csomópontokon 30 hüvelyre korlátozódnak.

### <a name="configure-maximum---existing-clusters"></a>Maximálisan meglévő fürtök konfigurálása

Egy meglévő AK-fürtön nem módosítható a csomópontok maximális száma. A számot csak akkor állíthatja be, ha először telepíti a fürtöt.

## <a name="deployment-parameters"></a>Központi telepítési paraméterek

AK-fürt létrehozásakor a következő paraméterek konfigurálhatók az Azure CNI hálózatkezeléshez:

**Virtuális hálózat**: az a virtuális hálózat, amelybe telepíteni kívánja a Kubernetes-fürtöt. Ha új virtuális hálózatot szeretne létrehozni a fürthöz, válassza az *új létrehozása* elemet, és kövesse a *virtuális hálózat létrehozása* szakasz lépéseit. Az Azure-beli virtuális hálózatok korlátaival és kvótákkal kapcsolatos információkért lásd: [Azure-előfizetések és-szolgáltatások korlátai, kvótái és megkötései](../azure-resource-manager/management/azure-subscription-service-limits.md#azure-resource-manager-virtual-networking-limits).

**Alhálózat**: azon a virtuális hálózaton belüli alhálózat, ahová a fürtöt telepíteni kívánja. Ha létre szeretne hozni egy új alhálózatot a fürt virtuális hálózatában, válassza az *új létrehozása* lehetőséget, és kövesse az *alhálózat létrehozása* szakaszban leírt lépéseket. A hibrid kapcsolatok esetében a címtartomány nem fedi át a környezetében lévő többi virtuális hálózatot.

**Kubernetes szolgáltatási címtartomány**: Ez a virtuális IP-címek készlete, amelyeket a Kubernetes a fürt belső [szolgáltatásaihoz][services] rendel. A következő követelményeknek megfelelő magánhálózati címtartományt is használhat:

* Nem lehet a fürt virtuális hálózati IP-címének tartományán belül
* Nem lehet átfedésben más olyan virtuális hálózatokkal, amelyekkel a fürt virtuális hálózati társai
* Nem lehet átfedésben a helyszíni IP-címekkel
* Nem lehet a (,) `169.254.0.0/16`vagy `172.30.0.0/16`a `172.31.0.0/16`() tartományon belül`192.0.2.0/24`

Habár technikailag lehetséges egy szolgáltatási címtartomány megadására ugyanazon a virtuális hálózaton belül, mint a fürt, ez nem ajánlott. Az előre nem látható viselkedés az átfedésben lévő IP-tartományok használata esetén eredményezhet. További információkért tekintse meg a jelen cikk [Gyakori kérdések](#frequently-asked-questions) című szakaszát. A Kubernetes-szolgáltatásokkal kapcsolatos további információkért lásd: [szolgáltatások][services] a Kubernetes dokumentációjában.

**KUBERNETES DNS-szolgáltatás IP-címe**: a fürt DNS-szolgáltatásának IP-címe. A címnek a *Kubernetes szolgáltatási címtartományába* kell tartoznia. Ne használja az első IP-címet a címtartományból, például. 1. Az alhálózat tartományának első címe a *kubernetes. default. SVC. cluster. local* címen található.

**Docker-híd címe**: a Docker-híd hálózati címe az összes Docker-telepítésben megtalálható alapértelmezett *docker0* -híd hálózati címnek felel meg. Habár a *docker0* híd nem használatos az AK-fürtök vagy maguk a hüvelyek számára, a címnek úgy kell beállítania, hogy továbbra is támogassa az olyan forgatókönyveket, mint például a *Docker Build* az AK-fürtön belül. A Docker-híd hálózati címéhez ki kell választania egy CIDR, mert máskülönben a Docker automatikusan kiválaszt egy alhálózatot, ami ütközik más CIDRs. Olyan címtartományt kell választania, amely nem ütközik a hálózatok többi CIDRs, beleértve a fürt Service CIDR és a pod CIDR.

## <a name="configure-networking---cli"></a>Hálózatkezelés konfigurálása – parancssori felület

Ha AK-fürtöt hoz létre az Azure CLI-vel, akkor az Azure CNI hálózatkezelést is konfigurálhatja. Az alábbi parancsokkal létrehozhat egy új AK-fürtöt az Azure CNI Networking szolgáltatással.

Először szerezze be annak az alhálózatnak az alhálózati erőforrás-AZONOSÍTÓját, amelybe az AK-fürtöt csatlakoztatni kívánja:

```azurecli-interactive
$ az network vnet subnet list \
    --resource-group myVnet \
    --vnet-name myVnet \
    --query "[0].id" --output tsv

/subscriptions/<guid>/resourceGroups/myVnet/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/default
```

A speciális hálózatkezelést használó fürt létrehozásához `--network-plugin azure` használja az az [AK Create][az-aks-create] parancsot az argumentummal. Frissítse az `--vnet-subnet-id` értéket az előző lépésben összegyűjtött ALhálózati azonosítóval:

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --network-plugin azure \
    --vnet-subnet-id <subnet-id> \
    --docker-bridge-address 172.17.0.1/16 \
    --dns-service-ip 10.2.0.10 \
    --service-cidr 10.2.0.0/24 \
    --generate-ssh-keys
```

## <a name="configure-networking---portal"></a>Hálózatkezelés konfigurálása – portál

A Azure Portal következő képernyőképe egy példát mutat be ezeknek a beállításoknak az AK-fürt létrehozásakor történő konfigurálására:

![A Azure Portal speciális hálózatkezelési beállításai][portal-01-networking-advanced]

## <a name="frequently-asked-questions"></a>Gyakori kérdések

Az alábbi kérdések és válaszok az **Azure CNI** hálózati konfigurációra vonatkoznak.

* *Telepíthetek virtuális gépeket a fürt alhálózatában?*

  Nem. A virtuális gépeknek a Kubernetes-fürt által használt alhálózatban való telepítése nem támogatott. A virtuális gépek ugyanabban a virtuális hálózatban, de egy másik alhálózatban is üzembe helyezhetők.

* *Beállíthat egy Pod hálózati házirendeket?*

  Igen, a Kubernetes hálózati házirendje az AK-ban érhető el. Első lépésként lásd: a [hüvelyek közötti biztonságos forgalom a hálózati szabályzatok használata az AK-ban][network-policy].

* *A kihelyezhető hüvelyek maximális száma a csomóponton konfigurálható?*

  Igen, amikor üzembe helyez egy fürtöt az Azure CLI-vel vagy egy Resource Manager-sablonnal. Lásd: a [hüvelyek maximális száma csomópontban](#maximum-pods-per-node).

  Egy meglévő fürtön nem módosítható a hüvelyek maximális száma egy csomóponton.

* *Hogyan konfigurálja az AK-fürt létrehozása során létrehozott alhálózat további tulajdonságait? Például a szolgáltatási végpontok.*

  A virtuális hálózat és az AK-fürt létrehozása során létrehozott alhálózatok tulajdonságainak teljes listája a Azure Portal normál virtuális hálózati konfiguráció lapján konfigurálható.

* *Használhatok egy másik alhálózatot a fürt virtuális hálózatán a* **Kubernetes szolgáltatási címtartomány**esetében?

  Nem ajánlott, de ez a konfiguráció lehetséges. A szolgáltatási címtartomány a virtuális IP-címek (VIP) készlete, amelyet a Kubernetes a fürt belső szolgáltatásaihoz rendel hozzá. Az Azure hálózatkezelésének nincs láthatósága a Kubernetes-fürt szolgáltatási IP-címének tartományán. A fürt szolgáltatási címtartomány láthatóságának hiánya miatt később létrehozhat egy új alhálózatot a fürt virtuális hálózatában, amely átfedésben van a szolgáltatási címtartomány használatával. Ha átfedés történik, a Kubernetes hozzárendelhet egy olyan IP-címet, amelyet már egy másik erőforrás használ az alhálózatban, ami kiszámíthatatlan működést vagy hibát okoz. Annak biztosításával, hogy a fürt virtuális hálózatán kívüli címtartományt használjon, elkerülheti ezt az átfedési kockázatot.

## <a name="next-steps"></a>További lépések

További információ a hálózatkezelésről az AK-ban a következő cikkekben található:

- [Statikus IP-cím használata az Azure Kubernetes szolgáltatás (ak) terheléselosztó használatával](static-ip.md)
- [Belső terheléselosztó használata Azure Container Service (ak) használatával](internal-lb.md)

- [Alapszintű bejövő adatvezérlő létrehozása külső hálózati kapcsolattal][aks-ingress-basic]
- [A HTTP-alkalmazás útválasztási bővítményének engedélyezése][aks-http-app-routing]
- [Belső, privát hálózatot és IP-címet használó bejövő adatforgalom-vezérlő létrehozása][aks-ingress-internal]
- [Dinamikus nyilvános IP-címmel rendelkező bejövő vezérlő létrehozása, amely lehetővé teszi a titkosítást a TLS-tanúsítványok automatikus létrehozásához][aks-ingress-tls]
- [Egy statikus nyilvános IP-címmel rendelkező bejövő vezérlő létrehozása, amely lehetővé teszi a titkosítást a TLS-tanúsítványok automatikus létrehozásához][aks-ingress-static-tls]

### <a name="aks-engine"></a>AK-motor

Az [Azure Kubernetes Service Engine (Kabai motor)][aks-engine] egy nyílt forráskódú projekt, amely Azure Resource Manager sablonokat hoz létre, amelyek segítségével Kubernetes-fürtöket helyezhet üzembe az Azure-ban.

Az AK-motorral létrehozott Kubernetes-fürtök támogatják a [kubenet][kubenet] és az [Azure CNI][cni-networking] beépülő modult is. Ennek megfelelően mindkét hálózati forgatókönyvet támogatja az AK-motor.

<!-- IMAGES -->
[advanced-networking-diagram-01]: ./media/networking-overview/advanced-networking-diagram-01.png
[portal-01-networking-advanced]: ./media/networking-overview/portal-01-networking-advanced.png

<!-- LINKS - External -->
[aks-engine]: https://github.com/Azure/aks-engine
[services]: https://kubernetes.io/docs/concepts/services-networking/service/
[portal]: https://portal.azure.com
[cni-networking]: https://github.com/Azure/azure-container-networking/blob/master/docs/cni.md
[kubenet]: https://kubernetes.io/docs/concepts/cluster-administration/network-plugins/#kubenet

<!-- LINKS - Internal -->
[az-aks-create]: /cli/azure/aks?view=azure-cli-latest#az-aks-create
[aks-ssh]: ssh.md
[ManagedClusterAgentPoolProfile]: /azure/templates/microsoft.containerservice/managedclusters#managedclusteragentpoolprofile-object
[aks-network-concepts]: concepts-network.md
[aks-ingress-basic]: ingress-basic.md
[aks-ingress-tls]: ingress-tls.md
[aks-ingress-static-tls]: ingress-static-ip.md
[aks-http-app-routing]: http-application-routing.md
[aks-ingress-internal]: ingress-internal-ip.md
[network-policy]: use-network-policies.md
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
[network-comparisons]: concepts-network.md#compare-network-models
