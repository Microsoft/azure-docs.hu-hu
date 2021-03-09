---
title: Az Azure CNI hálózatkezelés konfigurálása az Azure Kubernetes szolgáltatásban (ak)
description: Ismerje meg, hogyan konfigurálhatja az Azure CNI (Advanced) hálózatkezelést az Azure Kubernetes szolgáltatásban (ak), beleértve az AK-fürtök meglévő virtuális hálózatba és alhálózatba való telepítését is.
services: container-service
ms.topic: article
ms.date: 06/03/2019
ms.custom: references_regions
ms.openlocfilehash: ef9e3689f5846ddfc66c47a15967a18fc6550d35
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/09/2021
ms.locfileid: "102504252"
---
# <a name="configure-azure-cni-networking-in-azure-kubernetes-service-aks"></a>Az Azure CNI hálózatkezelés konfigurálása az Azure Kubernetes szolgáltatásban (ak)

Alapértelmezés szerint az AK-fürtök a [kubenet][kubenet]-t használják, és létrehoznak egy virtuális hálózatot és egy alhálózatot. A *kubenet* a csomópontok egy virtuális hálózati alhálózatból kapnak IP-címet. A hálózati címfordítás (NAT) ezután konfigurálva van a csomópontokon, és a hüvelyek a csomópont IP-címe mögött "rejtett" IP-címet kapnak. Ezzel a módszerrel csökkenthető az IP-címek száma, amelyeket a hüvelyek számára a hálózati térben le kell foglalni.

Az [Azure Container Network Interface (CNI)][cni-networking]használatával minden Pod IP-címet kap az alhálózatból, és közvetlenül elérhető. Ezeknek az IP-címeknek egyedinek kell lenniük a hálózati térben, és előre kell tervezni. Mindegyik csomóponthoz tartozik egy konfigurációs paraméter az általa támogatott hüvelyek maximális számához. A csomópontok azonos számú IP-címet, majd az adott csomópont előtt fenntartanak. Ennek a megközelítésnek több megtervezésre van szüksége, és gyakran az IP-címek kimerülését, illetve a fürtök egy nagyobb alhálózaton való újraépítését igényli, mivel az alkalmazás által igényelt növekedés.

Ebből a cikkből megtudhatja, hogyan hozhat létre és használhat az *Azure CNI* hálózatkezelési szolgáltatásával virtuális hálózati alhálózatot egy AK-fürthöz. A hálózati beállításokkal és a szempontokkal kapcsolatos további információkért lásd: [hálózati fogalmak a Kubernetes és az AK][aks-network-concepts]-hoz.

## <a name="prerequisites"></a>Előfeltételek

* Az AK-fürthöz tartozó virtuális hálózatnak engedélyeznie kell a kimenő internetkapcsolatot.
* Az AK-fürtök nem használhatják a,, `169.254.0.0/16` `172.30.0.0/16` `172.31.0.0/16` , vagy `192.0.2.0/24` a Kubernetes szolgáltatási címtartomány, a pod címtartomány vagy a fürt virtuális hálózati címtartomány-tartományát.
* Az AK-fürt által használt fürt identitásának legalább [hálózati közreműködői](../role-based-access-control/built-in-roles.md#network-contributor) engedélyekkel kell rendelkeznie a virtuális hálózaton belüli alhálózaton. Ha [Egyéni szerepkört](../role-based-access-control/custom-roles.md) szeretne definiálni a beépített hálózati közreműködő szerepkör használata helyett, a következő engedélyek szükségesek:
  * `Microsoft.Network/virtualNetworks/subnets/join/action`
  * `Microsoft.Network/virtualNetworks/subnets/read`
* Az AK-csomóponti készlethez rendelt alhálózat nem lehet [delegált alhálózat](../virtual-network/subnet-delegation-overview.md).

## <a name="plan-ip-addressing-for-your-cluster"></a>A fürt IP-címzésének megtervezése

Az Azure CNI hálózatkezeléssel konfigurált fürtök további tervezést igényelnek. A virtuális hálózat és az alhálózata méretének meg kell felelnie a futtatni kívánt hüvelyek számának és a fürt csomópontjainak.

A hüvelyek és a fürt csomópontjainak IP-címei a virtuális hálózaton belüli megadott alhálózathoz vannak rendelve. Mindegyik csomópont elsődleges IP-címmel van konfigurálva. Alapértelmezés szerint 30 további IP-cím előre konfigurálva van az Azure CNI, amelyek a csomóponton ütemezett hüvelyekhez vannak rendelve. Ha kibővíti a fürtöt, az egyes csomópontok Hasonlóképpen az alhálózat IP-címeivel vannak konfigurálva. Megtekintheti a [maximális hüvelyek/csomópontok számát](#maximum-pods-per-node)is.

> [!IMPORTANT]
> A szükséges IP-címek számának tartalmaznia kell a frissítési és skálázási műveletek szempontjait. Ha úgy állítja be az IP-címtartományt, hogy csak a rögzített számú csomópontot támogassa, a fürt nem frissíthető és nem méretezhető.
>
> * Az AK-fürt **frissítésekor** a rendszer egy új csomópontot telepít a fürtbe. A szolgáltatások és a munkaterhelések az új csomóponton futnak, és a rendszer eltávolítja egy régebbi csomópontot a fürtből. Ennek a működés közbeni frissítési folyamatnak legalább egy további IP-blokkot kell használnia. Ezután a csomópontok száma megtörténik `n + 1` .
>   * Ez a megfontolás különösen fontos a Windows Server-csomópontok készletének használatakor. Az AK-beli Windows Server-csomópontok nem alkalmazzák automatikusan a Windows-frissítéseket, hanem a csomópont-készleten végeznek frissítést. Ez a frissítés új csomópontokat helyez üzembe a legújabb Window Server 2019 Alapcsomópont-lemezkép és biztonsági javítások alapján. A Windows Server-csomópontok készletének frissítésével kapcsolatos további információkért lásd: [csomópont-készlet frissítése az AK-ban][nodepool-upgrade].
>
> * AK-fürtök **skálázásakor** a rendszer egy új csomópontot telepít a fürtbe. A szolgáltatások és a munkaterhelések az új csomóponton futnak. Az IP-címtartományt figyelembe kell vennie, hogyan érdemes felmérni a fürt által támogatott csomópontok és hüvelyek számát. A frissítési műveletekhez egy további csomópontot is bele kell foglalni. Ezután a csomópontok száma megtörténik `n + number-of-additional-scaled-nodes-you-anticipate + 1` .

Ha azt szeretné, hogy a csomópontok a maximális számú hüvelyt futtassák, és a hüvelyek rendszeres megsemmisítését és üzembe helyezését is lehetővé teszi, akkor a csomópontok további IP-címeit is érdemes figyelembe venni Ezek a további IP-címek figyelembe veszik, hogy a szolgáltatás törölhető, és az új szolgáltatás üzembe helyezésének és a cím megvásárlásának IP-címe is eltarthat.

Az AK-fürtök IP-címének csomagja egy virtuális hálózatból, a csomópontok és hüvelyek legalább egy alhálózatból, valamint egy Kubernetes-szolgáltatási címtartományből áll.

| Címtartomány/Azure-erőforrás | Korlátok és méretezés |
| --------- | ------------- |
| Virtuális hálózat | Az Azure-beli virtuális hálózat lehet olyan nagy, mint/8, de 65 536 konfigurált IP-címekre van korlátozva. Vegye figyelembe az összes hálózati igényt, beleértve a más virtuális hálózatokban lévő szolgáltatásokkal való kommunikációt a címterület konfigurálása előtt. Ha például egy címtartomány túl nagy részét állítja be, akkor előfordulhat, hogy a hálózaton belül átfedésben van más címtartomány.|
| Alhálózat | Elég nagynak kell lennie ahhoz, hogy megfeleljen a fürtben esetlegesen kiépített csomópontoknak, hüvelyeknek és az összes Kubernetes és Azure-erőforrásnak. Ha például belső Azure Load Balancer telepít, az előtér-IP-címek a fürt alhálózatán vannak lefoglalva, nem nyilvános IP-címek. Az alhálózat méretének figyelembe kell vennie a frissítési műveleteket vagy a jövőbeli méretezési igényeket is.<p />Az alhálózat *minimális* méretének kiszámításához, beleértve egy további csomópontot a frissítési műveletekhez: `(number of nodes + 1) + ((number of nodes + 1) * maximum pods per node that you configure)`<p/>Példa 50 csomópontos fürtre: `(51) + (51  * 30 (default)) = 1,581` (/21 vagy nagyobb)<p/>Példa egy 50 csomópontos fürtre, amely a további 10 csomópontok vertikális felskálázását is magában foglalja: `(61) + (61 * 30 (default)) = 1,891` (/21 vagy nagyobb)<p>Ha a fürt létrehozásakor nem ad meg maximális számú hüvelyt egy csomóponton, a csomópontok maximális száma *30* értékre van állítva. Az IP-címek minimálisan szükséges száma az adott értéken alapul. Ha az IP-címek minimális követelményeit eltérő maximális értékre számítja ki, tekintse meg a következő témakört: [a hüvelyek maximális számának beállítása](#configure-maximum---new-clusters) a fürt telepítésekor ezt az értéket. |
| A Kubernetes Service címtartománya | Ezt a tartományt nem szabad az ehhez a virtuális hálózathoz csatlakoztatott bármely hálózati elemhez használni. A szolgáltatási címek CIDR kisebbnek kell lennie, mint/12. Ezt a tartományt a különböző AK-fürtökön is felhasználhatja. |
| A Kubernetes DNS-szolgáltatás IP-címe | Az Kubernetes szolgáltatási címtartomány azon IP-címe, amelyet a fürtszolgáltatás felderítése fog használni. Ne használja az első IP-címet a címtartományból, például. 1. Az alhálózat tartományának első címe a *kubernetes. default. SVC. cluster. local* címen található. |
| Docker-híd címe | A Docker-híd hálózati címe az összes Docker-telepítésben található alapértelmezett *docker0* híd hálózati címe. Habár a *docker0* híd nem használatos az AK-fürtök vagy maguk a hüvelyek számára, a címnek úgy kell beállítania, hogy továbbra is támogassa az olyan forgatókönyveket, mint például a *Docker Build* az AK-fürtön belül. A Docker-híd hálózati címéhez ki kell választania egy CIDR, mert különben a Docker automatikusan kiválaszt egy alhálózatot, ami ütközik más CIDRs. Olyan címtartományt kell választania, amely nem ütközik a hálózatok többi CIDRs, beleértve a fürt Service CIDR és a pod CIDR. A 172.17.0.1/16 alapértelmezett értéke. Ezt a tartományt a különböző AK-fürtökön is felhasználhatja. |

## <a name="maximum-pods-per-node"></a>Hüvelyek maximális száma/csomópont

A hüvelyek maximális száma egy AK-fürtben 250. A *kubenet* és az *Azure CNI* Hálózatkezelés és a fürt üzembe helyezése közötti alapértelmezett maximális szám a hüvelyek száma *szerint* változik.

| Üzembe helyezési módszer | Kubenet alapértelmezett értéke | Alapértelmezett Azure-CNI | Konfigurálható az üzembe helyezéskor |
| -- | :--: | :--: | -- |
| Azure CLI | 110 | 30 | Igen (legfeljebb 250) |
| Resource Manager-sablon | 110 | 30 | Igen (legfeljebb 250) |
| Portál | 110 | 110 (a csomópont-készletek lapon van konfigurálva) | No |

### <a name="configure-maximum---new-clusters"></a>Maximális – új fürtök konfigurálása

A csomópontok maximális számát a fürt központi telepítésének idején vagy új csomópont-készletek hozzáadásakor konfigurálhatja. Ha az Azure CLI-vel vagy egy Resource Manager-sablonnal végzi a telepítést, a csomópontok maximális számát a 250 értékre állíthatja.

Ha nem ad meg maxPods az új csomópontok létrehozásakor, az Azure CNI esetében a 30 alapértelmezett érték jelenik meg.

A csomópontok maximális számaként megadott minimális érték kényszerítve van, hogy a rendszer a fürt állapotának kritikus fontosságú területét biztosítsa. A maximális hüvelyek esetében beállítható minimális érték 10, ha és csak akkor, ha az egyes csomópontok készletének konfigurációja legalább 30 hüvelyből áll. Például a maximális hüvelyek/csomópontok minimum 10 értékre való beállítása megköveteli, hogy minden egyes csomópont-készlet legalább 3 csomóponttal rendelkezzen. Ez a követelmény minden létrehozott új csomópont-készletre vonatkozik, így ha a 10 a csomópontok maximális hüvelye van definiálva, minden további hozzáadott csomópontnak legalább 3 csomóponttal kell rendelkeznie.

| Hálózat | Minimum | Maximum |
| -- | :--: | :--: |
| Azure-CNI | 10 | 250 |
| Kubenet | 10 | 110 |

> [!NOTE]
> A fenti táblázatban szereplő minimális értéket szigorúan kényszeríti az AK szolgáltatás. Nem állíthat be olyan maxPods értéket, amely kisebb, mint a minimálisan megjelenő érték, így megakadályozhatja, hogy a fürt el tudja kezdeni.

* **Azure CLI**: határozza meg az `--max-pods` argumentumot, amikor üzembe helyez egy fürtöt az az [AK Create][az-aks-create] paranccsal. A maximális érték 250.
* **Resource Manager-sablon**: a `maxPods` [ManagedClusterAgentPoolProfile] objektumban megadhatja a tulajdonságot, amikor Resource Manager-sablonnal telepít egy fürtöt. A maximális érték 250.
* **Azure Portal**: a többcsomópontos hüvelyek maximális száma nem módosítható, ha fürtöt telepít a Azure Portal. Az Azure CNI hálózatkezelési fürtök a Azure Portal használatával történő üzembe helyezéskor a csomópontokon 30 hüvelyre korlátozódnak.

### <a name="configure-maximum---existing-clusters"></a>Maximálisan meglévő fürtök konfigurálása

Az új maxPod létrehozásakor a Node (csomópontok száma) beállítás is meghatározható. Ha a maxPod egy meglévő fürtön kell megnövelni, adjon hozzá egy új csomópont-készletet az új kívánt maxPod-számhoz. Miután elvégezte a hüvelyek új készletbe való áttelepítését, törölje a régi készletet. A fürtben lévő bármelyik régebbi készlet törléséhez győződjön meg arról, hogy a [rendszercsomópont-készletek dokumentumban][system-node-pools]meghatározott módon állítja be a csomópont-készlet módjait.

## <a name="deployment-parameters"></a>Központi telepítési paraméterek

AK-fürt létrehozásakor a következő paraméterek konfigurálhatók az Azure CNI hálózatkezeléshez:

**Virtuális hálózat**: az a virtuális hálózat, amelybe telepíteni kívánja a Kubernetes-fürtöt. Ha új virtuális hálózatot szeretne létrehozni a fürthöz, válassza az *új létrehozása* elemet, és kövesse a *virtuális hálózat létrehozása* szakasz lépéseit. Az Azure-beli virtuális hálózatok korlátaival és kvótákkal kapcsolatos információkért lásd: [Azure-előfizetések és-szolgáltatások korlátai, kvótái és megkötései](../azure-resource-manager/management/azure-subscription-service-limits.md#azure-resource-manager-virtual-networking-limits).

**Alhálózat**: azon a virtuális hálózaton belüli alhálózat, ahová a fürtöt telepíteni kívánja. Ha létre szeretne hozni egy új alhálózatot a fürt virtuális hálózatában, válassza az *új létrehozása* lehetőséget, és kövesse az *alhálózat létrehozása* szakaszban leírt lépéseket. A hibrid kapcsolatok esetében a címtartomány nem fedi át a környezetében lévő többi virtuális hálózatot.

**Azure hálózati beépülő modul**: Ha az Azure hálózati beépülő modult használja, a belső terheléselosztó szolgáltatás "ExternalTrafficPolicy = local" értékkel nem érhető el olyan virtuális gépekről, amelyek nem tartoznak az AK-fürthöz tartozó clusterCIDR.

**Kubernetes szolgáltatási címtartomány**: Ez a paraméter azon virtuális IP-címek készlete, amelyeket a Kubernetes a fürt belső [szolgáltatásaihoz][services] rendel hozzá. A következő követelményeknek megfelelő magánhálózati címtartományt is használhat:

* Nem lehet a fürt virtuális hálózati IP-címének tartományán belül
* Nem lehet átfedésben más olyan virtuális hálózatokkal, amelyekkel a fürt virtuális hálózati társai
* Nem lehet átfedésben a helyszíni IP-címekkel
* Nem lehet a (,) vagy a () tartományon belül `169.254.0.0/16` `172.30.0.0/16` `172.31.0.0/16``192.0.2.0/24`

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

A speciális hálózatkezelést használó fürt létrehozásához használja az az [AK Create][az-aks-create] parancsot az `--network-plugin azure` argumentummal. Frissítse az `--vnet-subnet-id` értéket az előző lépésben összegyűjtött alhálózati azonosítóval:

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

! [Speciális hálózatkezelési konfiguráció a Azure Portal] [portál-01-hálózatkezelés-speciális]

## <a name="dynamic-allocation-of-ips-and-enhanced-subnet-support-preview"></a>Az IP-címek dinamikus kiosztása és a bővített alhálózat támogatása (előzetes verzió)

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

> [!NOTE] 
> Ez az előzetes verziójú funkció jelenleg a következő régiókban érhető el:
>
> * USA nyugati középső régiója

A hagyományos CNI való hátránya a pod IP-címek kimerülése, mivel az AK-fürt növekszik, így a teljes fürt újraépítése szükséges egy nagyobb alhálózatban. Az Azure CNI új dinamikus IP-kiosztási funkciója megoldja ezt a problémát úgy, hogy a allotting Pod IP-címeket egy, az AK-fürtöt üzemeltető alhálózattól eltérő alhálózatból oldja meg.  A következő előnyöket nyújtja:

* **Jobb IP-használat**: az IP-címek dinamikusan vannak kiosztva a hüvelyi alhálózatból származó fürtökhöz. Ez azt eredményezi, hogy az IP-címek jobb kihasználtsága a fürtben a hagyományos CNI-megoldáshoz képest történik, amely az IP-címek statikus kiosztását teszi ki minden csomóponthoz.  

* **Méretezhető és rugalmas**: a Node és a pod alhálózatok egymástól függetlenül méretezhetők. Egyetlen Pod alhálózat egy fürt több csomópont-készletében, vagy ugyanabban a VNet üzembe helyezett több AK-fürtben is megosztható. Egy különálló Pod alhálózatot is beállíthat egy csomópont-készlethez.  

* **Nagy teljesítmény**: mivel a pod a VNet IP-címekhez van rendelve, közvetlen kapcsolattal rendelkezik a VNet lévő többi fürthöz és erőforrásokhoz. A megoldás a nagy méretű fürtöket a teljesítmény romlása nélkül támogatja.

* **Különálló VNet szabályzatok a hüvelyekhez**: mivel a hüvelyek külön alhálózattal rendelkeznek, külön VNet szabályzatokat konfigurálhat, amelyek eltérnek a csomópont-házirendektől. Ez számos olyan hasznos forgatókönyvet tesz lehetővé, mint például az internetkapcsolat csak a hüvelyek esetében, és nem a csomópontok számára, a VNet hálózati NAT használatával rögzíti a pod forrás IP-címét egy csomópont-készletben, valamint a NSG használatával szűrheti a csomópontok közötti forgalmat.  

* **Kubernetes hálózati házirendek**: az Azure hálózati házirendjei és a tarka is együttműködik ezzel az új megoldással.  

### <a name="install-the-aks-preview-azure-cli"></a>Az `aks-preview` Azure CLI telepítése

Szüksége lesz az *AK-előnézeti* Azure CLI-bővítményre. Telepítse az *AK – előzetes* verzió Azure CLI-bővítményét az az [Extension Add][az-extension-add] paranccsal. Vagy telepítse az elérhető frissítéseket az az [Extension Update][az-extension-update] paranccsal.

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

### <a name="register-the-podsubnetpreview-preview-feature"></a>Az `PodSubnetPreview` előzetes verzió funkciójának regisztrálása

A funkció használatához engedélyeznie kell a `PodSubnetPreview` szolgáltatás jelölőjét is az előfizetésében.

Regisztrálja a `PodSubnetPreview` szolgáltatás jelölőjét az az [Feature Register][az-feature-register] paranccsal, az alábbi példában látható módon:

```azurecli-interactive
az feature register --namespace "Microsoft.ContainerService" --name "PodSubnetPreview"
```

Néhány percet vesz igénybe, amíg az állapot *regisztrálva* jelenik meg. Ellenőrizze a regisztrációs állapotot az az [Feature List][az-feature-list] parancs használatával:

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/PodSubnetPreview')].{Name:name,State:properties.state}"
```

Ha elkészült, frissítse a *Microsoft. tárolószolgáltatás* erőforrás-szolgáltató regisztrációját az az [Provider Register][az-provider-register] parancs használatával:

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

### <a name="additional-prerequisites"></a>További előfeltételek

Az Azure CNI már felsorolt előfeltételek továbbra is érvényben vannak, de van néhány további korlátozás:

* Csak a Linux csomópont-fürtök és a csomópont-készletek támogatottak.
* Az AK-motor és a DIY-fürtök nem támogatottak.

### <a name="planning-ip-addressing"></a>IP-címzés tervezése

A szolgáltatás használatakor a tervezés sokkal egyszerűbb. Mivel a csomópontok és a hüvelyek egymástól függetlenül méretezhetők, a Címterület is külön tervezhető. Mivel a pod-alhálózatok konfigurálhatók a csomópontok részletességére, az ügyfelek mindig hozzáadhatnak egy új alhálózatot, amikor egy csomópont-készletet vesznek fel. A fürt/csomópont-készletben található rendszerhüvelyek IP-címeket is kapnak a pod-alhálózatból, ezért ezt a viselkedést kell figyelembe venni.

A K8S-szolgáltatások és a Docker-híd IP-címeinek tervezése változatlan marad.

### <a name="maximum-pods-per-node-in-a-cluster-with-dynamic-allocation-of-ips-and-enhanced-subnet-support"></a>Az IP-címek és a bővített alhálózat támogatásának dinamikus kiosztásával rendelkező fürtben lévő maximális hüvelyek száma

Ha az IP-címek dinamikus kiosztásával az Azure CNI-t használja, akkor a csomópontok száma a hagyományos CNI viselkedéstől némileg megváltozott:

|CNI|Üzembe helyezési módszer|Alapértelmezett|Konfigurálható az üzembe helyezéskor|
|--|--| :--: |--|
|Hagyományos Azure-CNI|Azure CLI|30|Igen (legfeljebb 250)|
|Azure CNI az IP-címek dinamikus kiosztásával|Azure CLI|250|Igen (legfeljebb 250)|

Minden egyéb, a csomópontok maximális csomópontjainak konfigurálásához kapcsolódó útmutatás változatlan marad.

### <a name="additional-deployment-parameters"></a>További telepítési paraméterek

A fent ismertetett központi telepítési paraméterek továbbra is érvényesek, egyetlen kivétellel:

* Az **alhálózati** paraméter most a fürt csomópontjaihoz kapcsolódó alhálózatra hivatkozik.
* Egy további paraméter **Pod alhálózattal** megadható az alhálózat, amelynek IP-címei dinamikusan lesznek kiosztva a hüvelyekhez.

### <a name="configure-networking---cli-with-dynamic-allocation-of-ips-and-enhanced-subnet-support"></a>Hálózatkezelés konfigurálása – parancssori felület dinamikus IP-címekkel és bővített alhálózat-támogatással

Az IP-címek dinamikus kiosztása és az alhálózati támogatás használata a fürtben hasonló a fürt Azure-CNI konfigurálásának alapértelmezett módszeréhez. Az alábbi példa bemutatja, hogyan hozhat létre egy új virtuális hálózatot a csomópontok és a hüvelyek alhálózata számára, és hogyan hozhat létre egy olyan fürtöt, amely az IP-címek és a bővített alhálózat támogatásának dinamikus kiosztását használó Azure CNI használ. Ügyeljen arra, hogy a változókat, például a `$subscription` saját értékeit cserélje le:

Először hozza létre a virtuális hálózatot két alhálózattal:

```azurecli-interactive
$resourceGroup="myResourceGroup"
$vnet="myVirtualNetwork"

# Create our two subnet network 
az network vnet create -g $rg --name $vnet --address-prefixes 10.0.0.0/8 -o none 
az network vnet subnet create -g $rg --vnet-name $vnet --name nodesubnet --address-prefixes 10.240.0.0/16 -o none 
az network vnet subnet create -g $rg --vnet-name $vnet --name podsubnet --address-prefixes 10.241.0.0/16 -o none 
```

Ezután hozza létre a fürtöt, amely a csomópont alhálózatára hivatkozik a `--vnet-subnet-id` és a pod alhálózat használatával `--pod-subnet-id` :

```azurecli-interactive
$clusterName="myAKSCluster"
$location="eastus"
$subscription="aaaaaaa-aaaaa-aaaaaa-aaaa"

az aks create -n $clusterName -g $resourceGroup -l $location --max-pods 250 --node-count 2 --network-plugin azure --vnet-subnet-id /subscriptions/$subscription/resourceGroups/$resourceGroup/providers/Microsoft.Network/virtualNetworks/$vnet/subnets/nodesubnet --pod-subnet-id /subscriptions/$subscription/resourceGroups/$resourceGroup/providers/Microsoft.Network/virtualNetworks/$vnet/subnets/podsubnet  
```

#### <a name="adding-node-pool"></a>Csomópont-készlet hozzáadása

Csomópont-készlet hozzáadásakor a `--vnet-subnet-id` és a-t használó Pod alhálózattal hivatkozhat a csomópont-alhálózatra `--pod-subnet-id` . Az alábbi példa két új alhálózatot hoz létre, amelyek ezután egy új csomópont-készlet létrehozásakor hivatkoznak:

```azurecli-interactive
az network vnet subnet create -g $resourceGroup --vnet-name $vnet --name node2subnet --address-prefixes 10.242.0.0/16 -o none 
az network vnet subnet create -g $resourceGroup --vnet-name $vnet --name pod2subnet --address-prefixes 10.243.0.0/16 -o none 

az aks nodepool add --cluster-name $clusterName -g $resourceGroup  -n newNodepool --max-pods 250 --node-count 2 --vnet-subnet-id /subscriptions/$subscription/resourceGroups/$resourceGroup/providers/Microsoft.Network/virtualNetworks/$vnet/subnets/node2subnet  --pod-subnet-id /subscriptions/$subscription/resourceGroups/$resourceGroup/providers/Microsoft.Network/virtualNetworks/$vnet/subnets/pod2subnet --no-wait 
```

## <a name="frequently-asked-questions"></a>Gyakori kérdések

Az alábbi kérdések és válaszok az **Azure CNI** hálózati konfigurációra vonatkoznak.

* *Telepíthetek virtuális gépeket a fürt alhálózatában?*

  Igen.

* *Milyen forrás IP-címet látnak a külső rendszerek az Azure CNI-t támogató Pod-alapú forgalomhoz?*

  Az egyazon virtuális hálózatban található rendszerek, mint az AK-fürt, tekintse meg a pod IP-címet a pod-ból érkező adatforgalom forrásaként. Az AK-alapú fürt virtuális hálózatán kívüli rendszerek a pod-ból érkező adatforgalom forrásaként a csomópont IP-címét látják.

* *Beállíthat egy Pod hálózati házirendeket?*

  Igen, a Kubernetes hálózati házirendje az AK-ban érhető el. Első lépésként lásd: a [hüvelyek közötti biztonságos forgalom a hálózati szabályzatok használata az AK-ban][network-policy].

* *A kihelyezhető hüvelyek maximális száma a csomóponton konfigurálható?*

  Igen, amikor üzembe helyez egy fürtöt az Azure CLI-vel vagy egy Resource Manager-sablonnal. Lásd: a [hüvelyek maximális száma csomópontban](#maximum-pods-per-node).

  Egy meglévő fürtön nem módosítható a hüvelyek maximális száma egy csomóponton.

* *Hogyan konfigurálja az AK-fürt létrehozása során létrehozott alhálózat további tulajdonságait? Például a szolgáltatási végpontok.*

  A virtuális hálózat és az AK-fürt létrehozása során létrehozott alhálózatok tulajdonságainak teljes listája a Azure Portal normál virtuális hálózati konfiguráció lapján konfigurálható.

* *Használhatok egy másik alhálózatot a fürt virtuális hálózatán a* **Kubernetes szolgáltatási címtartomány** esetében?

  Nem ajánlott, de ez a konfiguráció lehetséges. A szolgáltatási címtartomány a virtuális IP-címek (VIP) készlete, amelyet a Kubernetes a fürt belső szolgáltatásaihoz rendel hozzá. Az Azure hálózatkezelésének nincs láthatósága a Kubernetes-fürt szolgáltatási IP-címének tartományán. A fürt szolgáltatási címtartomány láthatóságának hiánya miatt később létrehozhat egy új alhálózatot a fürt virtuális hálózatában, amely átfedésben van a szolgáltatási címtartomány használatával. Ha átfedés történik, a Kubernetes hozzárendelhet egy olyan IP-címet, amelyet már egy másik erőforrás használ az alhálózatban, ami kiszámíthatatlan működést vagy hibát okoz. Annak biztosításával, hogy a fürt virtuális hálózatán kívüli címtartományt használjon, elkerülheti ezt az átfedési kockázatot.

### <a name="dynamic-allocation-of-ip-addresses-and-enhanced-subnet-support-faqs"></a>Az IP-címek dinamikus kiosztása és a bővített alhálózat támogatása – gyakori kérdések

Az alábbi kérdések és válaszok az **Azure CNI hálózati konfigurációra vonatkoznak az IP-címek dinamikus kiosztásának és az alhálózatok támogatásának használatakor**.

* *Több Pod alhálózatot is hozzárendelhet egy fürthöz vagy csomópont-készlethez?*

  Egy fürthöz vagy csomópont-készlethez csak egy alhálózat rendelhető hozzá. Azonban több fürt vagy csomópont-készlet is megoszthat egyetlen alhálózatot.

* *A pod alhálózatok egy másik VNet is hozzárendelhetők?*

  A pod alhálózatnak a fürttel megegyező VNet kell származnia.  

* *A fürt egyes csomópontjai a hagyományos CNI használják, míg mások használják az új CNI?*

  A teljes fürtnek csak egy CNI kell használnia.

## <a name="aks-engine"></a>AKS-motor

Az [Azure Kubernetes Service Engine (Kabai motor)][aks-engine] egy nyílt forráskódú projekt, amely Azure Resource Manager sablonokat hoz létre, amelyek segítségével Kubernetes-fürtöket helyezhet üzembe az Azure-ban.

Az AK-motorral létrehozott Kubernetes-fürtök támogatják a [kubenet][kubenet] és az [Azure CNI][cni-networking] beépülő modult is. Ennek megfelelően mindkét hálózati forgatókönyvet támogatja az AK-motor.

## <a name="next-steps"></a>Következő lépések

További információ a hálózatkezelésről az AK-ban a következő cikkekben található:

* [Statikus IP-cím használata az Azure Kubernetes szolgáltatás (ak) terheléselosztó használatával](static-ip.md)
* [Belső terheléselosztó használata Azure Container Service (ak) használatával](internal-lb.md)

* [Alapszintű bejövő adatvezérlő létrehozása külső hálózati kapcsolattal][aks-ingress-basic]
* [A HTTP-alkalmazás útválasztási bővítményének engedélyezése][aks-http-app-routing]
* [Belső, privát hálózatot és IP-címet használó bejövő adatforgalom-vezérlő létrehozása][aks-ingress-internal]
* [Dinamikus nyilvános IP-címmel rendelkező bejövő vezérlő létrehozása, amely lehetővé teszi a titkosítást a TLS-tanúsítványok automatikus létrehozásához][aks-ingress-tls]
* [Egy statikus nyilvános IP-címmel rendelkező bejövő vezérlő létrehozása, amely lehetővé teszi a titkosítást a TLS-tanúsítványok automatikus létrehozásához][aks-ingress-static-tls]
<!-- IMAGES -->
[Advanced-Networking-diagram-01]:./Media/Networking-Overview/advanced-networking-diagram-01.png [portál-01-Networking-Advanced]:./Media/Networking-Overview/portal-01-networking-advanced.png

<!-- LINKS - External -->
[aks-engine]: https://github.com/Azure/aks-engine
[services]: https://kubernetes.io/docs/concepts/services-networking/service/
[portal]: https://portal.azure.com
[cni-networking]: https://github.com/Azure/azure-container-networking/blob/master/docs/cni.md
[kubenet]: https://kubernetes.io/docs/concepts/cluster-administration/network-plugins/#kubenet

<!-- LINKS - Internal -->
[az-aks-create]: /cli/azure/aks#az-aks-create
[aks-ssh]: ssh.md
[ManagedClusterAgentPoolProfile]: /azure/templates/microsoft.containerservice/managedclusters#managedclusteragentpoolprofile-object
[aks-network-concepts]: concepts-network.md
[aks-ingress-basic]: ingress-basic.md
[aks-ingress-tls]: ingress-tls.md
[aks-ingress-static-tls]: ingress-static-ip.md
[aks-http-app-routing]: http-application-routing.md
[aks-ingress-internal]: ingress-internal-ip.md
[az-extension-add]: /cli/azure/extension#az_extension_add
[az-extension-update]: /cli/azure/extension#az_extension_update
[az-feature-register]: /cli/azure/feature#az_feature_register
[az-feature-list]: /cli/azure/feature#az_feature_list
[az-provider-register]: /cli/azure/provider#az_provider_register
[network-policy]: use-network-policies.md
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
[network-comparisons]: concepts-network.md#compare-network-models
[system-node-pools]: use-system-pools.md
