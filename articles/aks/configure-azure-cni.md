---
title: Hálózati Azure CNI konfigurálása a Azure Kubernetes Service (AKS) szolgáltatásban
description: Megtudhatja, hogyan konfigurálhatja Azure CNI (speciális) hálózatépítést az Azure Kubernetes Service -ban (AKS), beleértve egy AKS-fürt üzembe helyezését egy meglévő virtuális hálózatban és alhálózaton.
services: container-service
ms.topic: article
ms.date: 06/03/2019
ms.custom: references_regions, devx-track-azurecli
ms.openlocfilehash: 62885a4695e7b061a5e7f0e70496cde4663c943d
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2021
ms.locfileid: "107478930"
---
# <a name="configure-azure-cni-networking-in-azure-kubernetes-service-aks"></a>Hálózati Azure CNI konfigurálása a Azure Kubernetes Service (AKS) szolgáltatásban

Alapértelmezés szerint az AKS-fürtök a [kubenetet][kubenet]használják, és létrejön egy virtuális hálózat és egy alhálózat. A *kubenettel a* csomópontok egy IP-címet szereznek be egy virtuális hálózat alhálózatán. Ezután konfigurálja a hálózati címfordítást (NAT) a csomópontokon, és a podok egy "rejtett" IP-címet kapnak a csomópont IP-címe mögött. Ez a megközelítés csökkenti a podok számára a hálózati térben lefoglalni szükséges IP-címek számát.

A [Azure Container Networking Interface (CNI) minden][cni-networking]pod kap egy IP-címet az alhálózatról, és közvetlenül elérhető. Ezeknek az IP-címeknek egyedinek kell lennie a hálózati térben, és előre meg kell tervezni őket. Minden csomópont rendelkezik egy konfigurációs paraméterrel a támogatott podok maximális számára. Ezután a csomópontonkénti azonos számú IP-címet a rendszer előre lefoglalja az adott csomópont számára. Ez a megközelítés több tervezést igényel, és gyakran az IP-címek kimerítéséhez vagy a fürtök egy nagyobb alhálózaton való újraépítéséhez vezet az alkalmazás igényeinek növekedésével.

Ez a cikk bemutatja, hogyan hozhat *Azure CNI* hálózati alhálózatokat egy AKS-fürthöz, és hogyan használhatja őket. A hálózati beállításokkal és szempontokkal kapcsolatos további információkért lásd: Hálózati fogalmak [a Kuberneteshez és az AKS-hez.][aks-network-concepts]

## <a name="prerequisites"></a>Előfeltételek

* Az AKS-fürt virtuális hálózatának engedélyeznie kell a kimenő internetkapcsolatot.
* Az AKS-fürtök nem használhatnak , , vagy értékeket a Kubernetes szolgáltatás címtartományához, pod címtartományához vagy fürt virtuális hálózati `169.254.0.0/16` `172.30.0.0/16` `172.31.0.0/16` `192.0.2.0/24` címtartományához.
* Az AKS-fürt által használt fürtidentitásnak legalább Hálózati közreműködő engedéllyel kell rendelkeznie a virtuális hálózat alhálózatán. [](../role-based-access-control/built-in-roles.md#network-contributor) Ha a beépített [](../role-based-access-control/custom-roles.md) Hálózati közreműködő szerepkör használata helyett egyéni szerepkört szeretne definiálni, a következő engedélyek szükségesek:
  * `Microsoft.Network/virtualNetworks/subnets/join/action`
  * `Microsoft.Network/virtualNetworks/subnets/read`
* Az AKS-csomópontkészlethez rendelt alhálózat nem lehet [delegált alhálózat.](../virtual-network/subnet-delegation-overview.md)

## <a name="plan-ip-addressing-for-your-cluster"></a>A fürt IP-címzésének megtervezése

A hálózati Azure CNI konfigurált fürtök további tervezést igényelnek. A virtuális hálózat és az alhálózat méretének igazodva kell lennie a futtatni kívánt podok és a fürt csomópontjainak számához.

A podok és a fürt csomópontjainak IP-címei a virtuális hálózat megadott alhálózatán vannak hozzárendelve. Minden csomópont egy elsődleges IP-címmel van konfigurálva. Alapértelmezés szerint 30 további IP-címet konfigurál a Azure CNI, amelyek a csomóponton ütemezett podokhoz vannak rendelve. A fürt horizontális felskálán való horizontális felskálán az egyes csomópontok hasonlóan vannak konfigurálva az alhálózat IP-címekkel. A podok [csomópontonkénti maximális száma is megtekinthető.](#maximum-pods-per-node)

> [!IMPORTANT]
> A szükséges IP-címek számának figyelembe kell vennie a frissítési és skálázási műveleteket. Ha az IP-címtartományt úgy adja meg, hogy csak meghatározott számú csomópontot támogatzon, akkor nem frissítheti vagy skálázhatja a fürtöt.
>
> * Amikor frissíti **az** AKS-fürtöt, a rendszer egy új csomópontot helyez üzembe a fürtön. A szolgáltatások és a számítási feladatok elkezdenek futni az új csomóponton, és a rendszer eltávolít egy régebbi csomópontot a fürtből. Ehhez a működés közbeni frissítési folyamathoz legalább egy további IP-címblokknak elérhetőnek kell lennie. A csomópontok száma ekkor `n + 1` .
>   * Ez a szempont különösen fontos a Windows Server-csomópontkészletek használata esetén. Az AKS-beli Windows Server-csomópontok nem alkalmazzák automatikusan a Windows-frissítéseket, hanem a csomópontkészlet frissítését hajtják végre. Ez a frissítés új csomópontokat helyez üzembe a Windows Server 2019 legújabb alapcsomópont-rendszerképével és biztonsági javításokkal. A Windows Server-csomópontkészletek frissítésével kapcsolatos további információkért lásd: Csomópontkészlet frissítése az [AKS-ban.][nodepool-upgrade]
>
> * Az  AKS-fürtök skálázásakor a rendszer egy új csomópontot helyez üzembe a fürtön. A szolgáltatások és a számítási feladatok az új csomóponton kezdenek futni. Az IP-címtartománynak figyelembe kell vennie, hogy a fürt által támogatott csomópontok és podok száma hogyan skálázható fel horizontálisan. A frissítési műveletekhez egy további csomópontot is bele kell foglalni. A csomópontok száma ekkor `n + number-of-additional-scaled-nodes-you-anticipate + 1` .

Ha azt várja, hogy a csomópontok a podok maximális számát futtatják, és rendszeresen megsemmisítik és üzembe helyezik a podokat, akkor csomópontonként néhány további IP-címet is figyelembe kell venünk. Ezek a további IP-címek figyelembe vehetnek néhány másodpercet, amíg egy szolgáltatás törlődik, és megkezdődik az új szolgáltatás üzembe helyezése és a cím lekért IP-címe.

Az AKS-fürt IP-címzési terve egy virtuális hálózatból, legalább egy alhálózatból áll a csomópontok és podok számára, valamint egy Kubernetes-szolgáltatás címtartománya.

| Címtartomány /Azure-erőforrás | Korlátok és méretezés |
| --------- | ------------- |
| Virtuális hálózat | Az Azure-beli virtuális hálózat akár /8 méretű is lehet, de legfeljebb 65 536 konfigurált IP-címre van korlátozva. A címtér konfigurálása előtt vegye figyelembe a hálózattal kapcsolatos összes igényét, beleértve a más virtuális hálózatokban lévő szolgáltatásokkal való kommunikációt is. Ha például túl nagy címteret konfigurál, problémákba kerülhet a hálózaton belüli más címterek átfedése miatt.|
| Alhálózat | Elég nagynak kell lennie ahhoz, hogy elférje a fürtben kiépített csomópontokat, podokat, valamint az összes Kubernetes- és Azure-erőforrást. Ha például belső ip-Azure Load Balancer üzembe, annak előtér-IP-eket a fürt alhálózata, nem pedig a nyilvános IP-k osztják ki. Az alhálózat méretének figyelembe kell vennie a frissítési műveleteket vagy a jövőbeli méretezési igényeket is.<p />Az alhálózat *minimális* méretének kiszámítása, beleértve egy további csomópontot a frissítési műveletekhez: `(number of nodes + 1) + ((number of nodes + 1) * maximum pods per node that you configure)`<p/>Példa 50 csomópontos fürtre: `(51) + (51  * 30 (default)) = 1,581` (/21 vagy nagyobb)<p/>Példa egy 50 csomópontos fürtre, amely egy további 10 csomópont horizontális felskáláshoz való üzembe építést is tartalmaz: `(61) + (61 * 30 (default)) = 1,891` (/21 vagy nagyobb)<p>Ha a fürt létrehozásakor nem adja meg a podok csomópontonkénti maximális számát, a podok csomópontonkénti maximális száma *30* lesz. A szükséges IP-címek minimális száma ezen az értéken alapul. Ha egy másik maximális értékre számítja ki [](#configure-maximum---new-clusters) a minimális IP-címre vonatkozó követelményeket, tekintse meg, hogyan konfigurálhatja a podok csomópontonkénti maximális számát ennek az értéknek a beállítását a fürt üzembe helyezésekor. |
| A Kubernetes Service címtartománya | Ezt a tartományt ne használja a virtuális hálózat egyik hálózati eleme sem, és nem kapcsolód hozzá. A szolgáltatáscím CIDR-nek /12-esnél kisebbnek kell lennie. Ezt a tartományt újra felhasználhatja a különböző AKS-fürtökben. |
| A Kubernetes DNS-szolgáltatás IP-címe | A Fürtszolgáltatás felderítése által használt, a Kubernetes szolgáltatás címtartományán belüli IP-cím. Ne használja a címtartomány első IP-címét, például .1. A rendszer az alhálózati tartomány első címét használja a *kubernetes.default.svc.cluster.local címhez.* |
| Docker-híd címe | A Docker-híd hálózati címe az összes Docker-telepítésben található alapértelmezett *docker0* híd hálózati címe. Bár az AKS-fürtök és maguk a podok nem használják a *Docker0-hidat,* ezt a címet úgy kell beállítania, hogy továbbra is támogassa az olyan forgatókönyveket, mint az AKS-fürtön belüli *docker build.* A Docker-híd hálózati címéhez CIDR-t kell választani, mert ellenkező esetben a Docker automatikusan választ alhálózatot, ami ütközhet más CIDR-ekkel. Olyan címteret kell választania, amely nem ütközik a hálózat többi CIDR-ével, beleértve a fürt szolgáltatásának CIDR-ját és a pod CIDR-ját. Az alapértelmezett érték: 172.17.0.1/16. Ezt a tartományt újra felhasználhatja a különböző AKS-fürtökben. |

## <a name="maximum-pods-per-node"></a>Podok maximális száma csomópontonként

Egy AKS-fürtben csomópontonként legfeljebb 250 pod lehet. A *podok* csomópontonkénti alapértelmezett maximális száma a *kubenet* és Azure CNI *hálózat* és a fürttelepítés módja között változik.

| Üzembe helyezési módszer | Kubenet alapértelmezett | Azure CNI alapértelmezett | Üzembe helyezéskor konfigurálható |
| -- | :--: | :--: | -- |
| Azure CLI | 110 | 30 | Igen (legfeljebb 250) |
| Resource Manager-sablon | 110 | 30 | Igen (legfeljebb 250) |
| Portál | 110 | 110 (a Csomópontkészletek lapon konfigurálva) | Nem |

### <a name="configure-maximum---new-clusters"></a>Maximális érték konfigurálása – új fürtök

A podok csomópontonkénti maximális számát a fürt üzembe helyezésekor vagy új csomópontkészletek hozzáadásakor konfigurálhatja. Ha az Üzembe helyezést az Azure CLI-val vagy egy Resource Manager sablonnal telepíti, a podok csomópontonkénti maximális értékét beállíthatja 250-re.

Ha nem adja meg a maxPods értéket az új csomópontkészletek létrehozásakor, az alapértelmezett érték 30 lesz a Azure CNI.

A fürt állapota szempontjából kritikus fontosságú rendszerpodok számára a podok csomópontonkénti maximális értékének minimális értéke van kényszerítve. A csomópontonkénti podok maximális számára beállítható minimális érték 10, ha és csak akkor, ha az egyes csomópontkészletek konfigurációjában van legalább 30 pod. Ha például a csomópontonkénti podok maximális számát 10-re adhatja meg, akkor minden egyes csomópontkészletnek legalább 3 csomóponttal kell rendelkezik. Ez a követelmény minden létrehozott új csomópontkészletre vonatkozik, így ha a csomópontonkénti 10 pod maximális podként van meghatározva, minden további hozzáadott csomópontkészletnek legalább 3 csomóponttal kell lennie.

| Hálózatkezelés | Minimum | Maximum |
| -- | :--: | :--: |
| Azure CNI | 10 | 250 |
| Kubenet | 10 | 110 |

> [!NOTE]
> A fenti táblázatban megadott minimális értéket szigorúan az AKS-szolgáltatás érvényesíti. Nem állíthat be a maxPods értékét a minimálisan látható értéknél alacsonyabbra, mert ez megakadályozhatja a fürt elindulását.

* **Azure CLI:** Adja meg a argumentumot, amikor az az aks create paranccsal helyez üzembe egy `--max-pods` [fürtöt.][az-aks-create] A maximális érték 250.
* **Resource Manager sablon:** Adja meg `maxPods` a [ManagedClusterAgentPoolProfile] objektum tulajdonságát, amikor fürtöt helyez üzembe egy Resource Manager sablonnal. A maximális érték 250.
* **Azure Portal:** Csomópontonként nem módosíthatja a podok maximális számát, amikor fürtöt helyez üzembe a Azure Portal. Azure CNI fürtök csomópontonként legfeljebb 30 podot Azure Portal.

### <a name="configure-maximum---existing-clusters"></a>Maximális érték konfigurálása – meglévő fürtök

A maxPod csomópontonként beállítás új csomópontkészlet létrehozásakor határozható meg. Ha növelni szeretné a maxPod csomópontonkénti beállítást egy meglévő fürtön, adjon hozzá egy új csomópontkészletet az új kívánt maxPod számmal. Miután mirating your pods to the new pool (Podok áttelepítése az új készletbe) törölje a régebbi készletet. A fürtben található régebbi készletek törléséhez győződjön meg arról, hogy a rendszer-csomópontkészletek dokumentumban meghatározott csomópontkészlet-módokat [ad meg.][system-node-pools]

## <a name="deployment-parameters"></a>Üzembe helyezési paraméterek

AKS-fürt létrehozásakor a következő paraméterek konfigurálhatók a Azure CNI számára:

**Virtuális hálózat:** Az a virtuális hálózat, amelyben üzembe szeretné helyezni a Kubernetes-fürtöt. Ha új virtuális hálózatot szeretne létrehozni a  fürthöz, válassza az Új létrehozása lehetőséget, és kövesse a Virtuális hálózat *létrehozása szakaszban található lépéseket.* További információ az Azure-beli virtuális hálózatok korlátairól és kvótáiról: Azure-előfizetések és -szolgáltatások [korlátai, kvótái és megkötései.](../azure-resource-manager/management/azure-subscription-service-limits.md#azure-resource-manager-virtual-networking-limits)

**Alhálózat:** Annak a virtuális hálózatnak az alhálózata, ahol a fürtöt üzembe szeretné helyezni. Ha új alhálózatot szeretne létrehozni a fürt virtuális  hálózatában, válassza az Új létrehozása lehetőséget, és kövesse az Alhálózat *létrehozása szakaszban található lépéseket.* Hibrid kapcsolatok esetén a címtartomány nem lehet átfedésben a környezet többi virtuális hálózatával.

**Azure hálózati** beépülő modul: Az Azure hálózati beépülő modul használata esetén az "externalTrafficPolicy=Local" belső LoadBalancer szolgáltatás nem érhető el olyan virtuális gépekről, amelyek IP-címe nem AKS-fürthöz tartozik a clusterCIDR-ben.

**Kubernetes-szolgáltatás címtartománya:** Ez a paraméter a kubernetes által [][services] a fürt belső szolgáltatásaihoz hozzárendelt virtuális IP-címek halmaza. Bármilyen olyan privát címtartományt használhat, amely megfelel az alábbi követelményeknek:

* Nem lehet a fürt virtuális hálózatának IP-címtartományában
* Nem lehet átfedésben más olyan virtuális hálózatokkal, amelyek között a fürt virtuális hálózatai társviszonyban vannak
* Nem lehet átfedésben a helyszíni IP-ekkel
* Nem lehet a `169.254.0.0/16` , `172.30.0.0/16` , , vagy `172.31.0.0/16` tartományon belül `192.0.2.0/24`

Bár technikailag lehetséges szolgáltatási címtartományt megadni ugyanazon a virtuális hálózaton belül, mint a fürtöt, ez nem ajánlott. Kiszámíthatatlan viselkedés eredményezhet átfedésben lévő IP-címtartományok használata esetén. További információért tekintse meg a cikk [GYIK](#frequently-asked-questions) szakaszát. A Kubernetes-szolgáltatásokkal kapcsolatos további információkért lásd a Szolgáltatások című [dokumentumot][services] a Kubernetes dokumentációjában.

**Kubernetes DNS-szolgáltatás IP-címe:** A fürt DNS-szolgáltatásának IP-címe. A címnek a *Kubernetes szolgáltatási címtartományába* kell tartoznia. Ne használja a címtartomány első IP-címét, például a .1-et. Az alhálózati tartomány első címét a *kubernetes.default.svc.cluster.local címhez használja* a rendszer.

**Docker-híd címe:** A Docker-híd hálózati címe az összes Docker-telepítésben található alapértelmezett *Docker0-híd* hálózati címét jelöli. Bár az AKS-fürtök és maguk a podok nem használják a *Docker0-hidat,* ezt a címet úgy kell beállítania, hogy továbbra is támogassa az olyan forgatókönyveket, mint az AKS-fürtön belüli *docker build.* CiDR-t kell választania a Docker-híd hálózati címéhez, mert ellenkező esetben a Docker automatikusan kiválaszt egy alhálózatot, amely ütközhet más CIDR-ekkel. Olyan címteret kell választania, amely nem ütközik a hálózat többi CIDR-ével, beleértve a fürt szolgáltatás cidr-ját és a pod CIDR-ját.

## <a name="configure-networking---cli"></a>Hálózat konfigurálása – CLI

Amikor létrehoz egy AKS-fürtöt az Azure CLI-val, konfigurálhatja a Azure CNI is. Az alábbi parancsokkal hozzon létre egy új AKS-fürtöt, Azure CNI engedélyezni.

Először szerezze be annak a meglévő alhálózatnak az alhálózati erőforrás-azonosítóját, amelyhez az AKS-fürt csatlakozik:

```azurecli-interactive
$ az network vnet subnet list \
    --resource-group myVnet \
    --vnet-name myVnet \
    --query "[0].id" --output tsv

/subscriptions/<guid>/resourceGroups/myVnet/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/default
```

Az [az aks create paranccsal][az-aks-create] és a argumentummal hozzon létre `--network-plugin azure` egy fürtöt speciális hálózattal. Frissítse az értéket az előző lépésben gyűjtött `--vnet-subnet-id` alhálózat-azonosítóval:

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

## <a name="configure-networking---portal"></a>Hálózat konfigurálása – portál

Az alábbi képernyőképen Azure Portal AKS-fürt létrehozása során látható példa ezeknek a beállításoknak a konfigurálásához:

! [Speciális hálózatkonfiguráció a Azure Portal] [portal-01-networking-advanced]

## <a name="dynamic-allocation-of-ips-and-enhanced-subnet-support-preview"></a>IP-k dinamikus kiosztása és továbbfejlesztett alhálózati támogatás (előzetes verzió)

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

> [!NOTE] 
> Ez az előzetes verziójú funkció jelenleg a következő régiókban érhető el:
>
> * USA nyugati középső régiója

A hagyományos CNI hátránya, hogy az AKS-fürt növekedésével a pod IP-címeinek kimerítése miatt a teljes fürtöt egy nagyobb alhálózatban kell újraépíteni. A fürt új dinamikus IP-Azure CNI úgy oldja meg ezt a problémát, hogy pod IP-címeket biztosít az AKS-fürtöt üzemeltető alhálózattól különálló alhálózatról.  A következő előnyöket kínálja:

* **Jobb IP-kihasználtság:** Az IP-címek dinamikusan vannak lefoglalva a podok számára a pod alhálózatról. Ez a fürt IP-címének jobb kihasználtságát eredményezi a hagyományos CNI-megoldáshoz képest, amely minden csomópont számára statikus IP-kiosztást tesz lehetővé.  

* **Skálázható és rugalmas:** A csomópont- és pod-alhálózatok egymástól függetlenül skálázhatók. Egyetlen pod-alhálózat megosztható egy fürt több csomópontkészlete között, vagy egy VNeten belül üzembe helyezett több AKS-fürtön. Egy csomópontkészlethez külön pod-alhálózatot is konfigurálhat.  

* **Nagy teljesítmény:** Mivel a podok virtuális hálózati IP-címekkel vannak kiosztva, közvetlen kapcsolatban állnak a virtuális hálózat más fürtpodjaival és erőforrásaival. A megoldás a nagyon nagy fürtök teljesítményét nem romlása nélkül támogatja.

* **Különálló VNet-szabályzatok podok számára:** Mivel a podok külön alhálózattal vannak, külön VNet-szabályzatokat konfigurálhat számukra, amelyek eltérnek a csomópont-házirendektől. Ez számos hasznos forgatókönyvet tesz lehetővé, például csak podok számára engedélyezi az internetkapcsolatot, csomópontok esetében nem, kijavítja a csomópontkészlet podjának forrás IP-címét egy virtuális hálózati NAT használatával, és NSG-kkel szűri a csomópontkészletek közötti forgalmat.  

* **Kubernetes hálózati szabályzatok:** Az Azure hálózati szabályzatai és a Calico is működnek ezzel az új megoldással.  

### <a name="install-the-aks-preview-azure-cli"></a>Az `aks-preview` Azure CLI telepítése

Szüksége lesz az *aks-preview* Azure CLI-bővítményre. Telepítse *az aks-preview* Azure CLI-bővítményt az [az extension add paranccsal.][az-extension-add] Vagy telepítse az elérhető frissítéseket az [az extension update paranccsal.][az-extension-update]

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

### <a name="register-the-podsubnetpreview-preview-feature"></a>Az előzetes `PodSubnetPreview` verziójú funkció regisztrálása

A funkció használatához engedélyeznie kell a funkciójelölőt `PodSubnetPreview` is az előfizetésen.

Regisztrálja `PodSubnetPreview` a funkciójelölőt az [az feature register paranccsal,][az-feature-register] az alábbi példában látható módon:

```azurecli-interactive
az feature register --namespace "Microsoft.ContainerService" --name "PodSubnetPreview"
```

Eltarthat néhány percig, hogy az állapot Regisztrált *állapotúra mutasson.* Ellenőrizze a regisztráció állapotát az [az feature list paranccsal:][az-feature-list]

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/PodSubnetPreview')].{Name:name,State:properties.state}"
```

Ha készen áll, frissítse a *Microsoft.ContainerService* erőforrás-szolgáltató regisztrációját az [az provider register paranccsal:][az-provider-register]

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

### <a name="additional-prerequisites"></a>További előfeltételek

A szolgáltatáshoz már felsorolt Azure CNI továbbra is érvényesek, de van néhány további korlátozás:

* Csak a linuxos csomópontfürtök és csomópontkészletek támogatottak.
* Az AKS-motor és a DIY-fürtök nem támogatottak.

### <a name="planning-ip-addressing"></a>IP-címzés megtervezése

A funkció használata esetén a tervezés sokkal egyszerűbb. Mivel a csomópontok és a podok egymástól függetlenül skálázhatók, a címtereik külön tervezhetők. Mivel a pod-alhálózatok a csomópontkészlet részletességével konfigurálhatóak, az ügyfelek mindig hozzáadhatnak új alhálózatokat, amikor csomópontkészletet adnak hozzá. A fürtben/csomópontkészletben található rendszerpodok a pod alhálózatának IP-jét is megkapják, ezért ezt a viselkedést figyelembe kell venni.

A K8S-szolgáltatások és a Docker-híd IP-ének megtervezése változatlan marad.

### <a name="maximum-pods-per-node-in-a-cluster-with-dynamic-allocation-of-ips-and-enhanced-subnet-support"></a>Podok maximális száma csomópontonként egy fürtben IP-k dinamikus kiosztásával és továbbfejlesztett alhálózati támogatással

A podok csomópontonkénti értékei a Azure CNI IP-kiosztással való használata kissé eltér a hagyományos CNI-viselkedéstől:

|CNI|Üzembe helyezési módszer|Alapértelmezett|Üzembe helyezéskor konfigurálható|
|--|--| :--: |--|
|Hagyományos Azure CNI|Azure CLI|30|Igen (legfeljebb 250)|
|Azure CNI IP-k dinamikus kiosztásával|Azure CLI|250|Igen (legfeljebb 250)|

A podonkénti maximális csomópontok konfigurálásával kapcsolatos összes többi útmutató változatlan marad.

### <a name="additional-deployment-parameters"></a>További üzembe helyezési paraméterek

A fent leírt üzembe helyezési paraméterek mind érvényesek, egyetlen kivétellel:

* Az **alhálózati** paraméter most a fürt csomópontjaihoz kapcsolódó alhálózatra hivatkozik.
* Egy további **pod-paraméter alhálózat** használatával adható meg az alhálózat, amelynek IP-címeit a rendszer dinamikusan lefoglalja a podok számára.

### <a name="configure-networking---cli-with-dynamic-allocation-of-ips-and-enhanced-subnet-support"></a>Hálózat konfigurálása – CLI IP-k dinamikus kiosztásával és továbbfejlesztett alhálózati támogatással

Az IP-k dinamikus kiosztása és a továbbfejlesztett alhálózati támogatás fürtön való használata hasonló az alapértelmezett módszerhez a fürthálózatok Azure CNI. Az alábbi példa végigvezeti egy új virtuális hálózat létrehozásának folyamatán, amely egy alhálózatot használ a csomópontokhoz és egy alhálózatot a podokhoz, valamint egy olyan fürt létrehozását, amely dinamikus IP-kiosztással és továbbfejlesztett alhálózati támogatással használja az Azure CNI-t. Mindenképpen cserélje le az olyan változókat, mint a `$subscription` saját értékei:

Először hozza létre a virtuális hálózatot két alhálózattal:

```azurecli-interactive
$resourceGroup="myResourceGroup"
$vnet="myVirtualNetwork"

# Create our two subnet network 
az network vnet create -g $rg --name $vnet --address-prefixes 10.0.0.0/8 -o none 
az network vnet subnet create -g $rg --vnet-name $vnet --name nodesubnet --address-prefixes 10.240.0.0/16 -o none 
az network vnet subnet create -g $rg --vnet-name $vnet --name podsubnet --address-prefixes 10.241.0.0/16 -o none 
```

Ezután hozza létre a fürtöt, és hivatkozzon a csomópont-alhálózatra a használatával, a `--vnet-subnet-id` pod alhálózatát pedig a `--pod-subnet-id` használatával:

```azurecli-interactive
$clusterName="myAKSCluster"
$location="eastus"
$subscription="aaaaaaa-aaaaa-aaaaaa-aaaa"

az aks create -n $clusterName -g $resourceGroup -l $location --max-pods 250 --node-count 2 --network-plugin azure --vnet-subnet-id /subscriptions/$subscription/resourceGroups/$resourceGroup/providers/Microsoft.Network/virtualNetworks/$vnet/subnets/nodesubnet --pod-subnet-id /subscriptions/$subscription/resourceGroups/$resourceGroup/providers/Microsoft.Network/virtualNetworks/$vnet/subnets/podsubnet  
```

#### <a name="adding-node-pool"></a>Csomópontkészlet hozzáadása

Csomópontkészlet hozzáadásakor hivatkozzon a használatával használt csomópont-alhálózatra, a `--vnet-subnet-id` pod-alhálózatra pedig a `--pod-subnet-id` használatával. Az alábbi példa két új alhálózatot hoz létre, amelyekre egy új csomópontkészlet létrehozásakor hivatkoznak:

```azurecli-interactive
az network vnet subnet create -g $resourceGroup --vnet-name $vnet --name node2subnet --address-prefixes 10.242.0.0/16 -o none 
az network vnet subnet create -g $resourceGroup --vnet-name $vnet --name pod2subnet --address-prefixes 10.243.0.0/16 -o none 

az aks nodepool add --cluster-name $clusterName -g $resourceGroup  -n newNodepool --max-pods 250 --node-count 2 --vnet-subnet-id /subscriptions/$subscription/resourceGroups/$resourceGroup/providers/Microsoft.Network/virtualNetworks/$vnet/subnets/node2subnet  --pod-subnet-id /subscriptions/$subscription/resourceGroups/$resourceGroup/providers/Microsoft.Network/virtualNetworks/$vnet/subnets/pod2subnet --no-wait 
```

## <a name="frequently-asked-questions"></a>Gyakori kérdések

Az alábbi kérdések és válaszok a hálózati **Azure CNI** vonatkoznak.

* *Üzembe helyezhetek virtuális gépeket a fürt alhálózatán?*

  Igen.

* *Milyen forrás IP-címet látnak a külső rendszerek a Azure CNI podból származó forgalomhoz?*

  Az AKS-fürthöz azonos virtuális hálózatban lévő rendszerek a pod IP-címét látják forráscímként a podról származó összes forgalom esetében. Az AKS-fürt virtuális hálózatán kívüli rendszerek a csomópont IP-címét látják forráscímként a podról származó összes forgalomhoz.

* *Konfigurálható podonkénti hálózati szabályzat?*

  Igen, a Kubernetes hálózati szabályzat elérhető az AKS-ban. Első lépésekért lásd: Secure traffic between pods by using network policies in AKS (A podok közötti adatforgalom [biztonságossá szolgáltatása hálózati szabályzatokkal az AKS-ban).][network-policy]

* *Konfigurálható a csomóponton üzembe helyezhető podok maximális száma?*

  Igen, amikor fürtöt helyez üzembe az Azure CLI-val vagy egy Resource Manager sablonnal. Lásd: [Podok maximális száma csomópontonként.](#maximum-pods-per-node)

  Meglévő fürtön nem módosíthatja a podok csomópontonkénti maximális számát.

* *Hogyan konfigurálni további tulajdonságokat az AKS-fürt létrehozásakor létrehozott alhálózathoz? Például szolgáltatásvégpontokat.*

  Az AKS-fürt létrehozása során létrehozott virtuális hálózat és alhálózatok tulajdonságainak teljes listája a fürt szabványos virtuális hálózati konfigurációs lapján konfigurálható Azure Portal.

* *Használhatok másik alhálózatot a* fürt virtuális hálózatán belül a **Kubernetes-szolgáltatás címtartományához?**

  Ez nem ajánlott, de ez a konfiguráció lehetséges. A szolgáltatás címtartománya a Kubernetes által a fürt belső szolgáltatásaihoz hozzárendelt virtuális IP-címek (VIP-k) halmaza. Azure-hálózatkezelés nem látható a Kubernetes-fürt szolgáltatás IP-címtartománya. Mivel nem látható a fürt szolgáltatáscímtartománya, később létrehozható egy új alhálózat a fürt virtuális hálózatában, amely átfedésben van a szolgáltatás címtartományával. Ilyen átfedés esetén a Kubernetes olyan IP-címet rendelhet a szolgáltatáshoz, amelyet az alhálózat egy másik erőforrása már használ, ami kiszámíthatatlan viselkedést vagy hibákat okoz. Ha a fürt virtuális hálózatán kívüli címtartományt használ, elkerülheti ezt az átfedési kockázatot.

### <a name="dynamic-allocation-of-ip-addresses-and-enhanced-subnet-support-faqs"></a>Az IP-címek dinamikus kiosztása és a továbbfejlesztett alhálózat támogatása – gyakori kérdések

Az alábbi kérdések és válaszok a hálózati Azure CNI konfigurációra vonatkoznak az IP-címek dinamikus kiosztása és a továbbfejlesztett **alhálózati támogatás használata esetén.**

* *Hozzárendelhetek több pod-alhálózatot egy fürthöz/csomópontkészlethez?*

  Egy fürthöz vagy csomópontkészlethez csak egy alhálózat rendelhető hozzá. Azonban több fürt vagy csomópontkészlet is osztozhat egyetlen alhálózaton.

* *Hozzárendelhetek pod-alhálózatokat egy másik virtuális hálózatról?*

  A pod alhálózatának ugyanannak a virtuális hálózatnak kell lennie, mint a fürtnek.  

* *Egy fürt egyes csomópontkészlete használhatja a hagyományos CNI-t, míg mások az új CNI-t?*

  A teljes fürtnek csak egy CNI-típust kell használnia.

## <a name="aks-engine"></a>AKS-motor

[Azure Kubernetes Service Motor (AKS Engine)][aks-engine] egy nyílt forráskódú projekt, amely Azure Resource Manager-sablonokat hoz létre, amelyek kubernetes-fürtök Azure-ban való üzembe helyezéséhez használhatók.

Az AKS-motorral létrehozott Kubernetes-fürtök a [Kubenet][kubenet] és a Azure CNI beépülő modulokat [is][cni-networking] támogatják. Ezért az AKS-motor mindkét hálózati forgatókönyvet támogatja.

## <a name="next-steps"></a>Következő lépések

Az AKS-hez való hálózatépítésről a következő cikkekben talál további információt:

* [Statikus IP-cím használata a Azure Kubernetes Service (AKS) terheléselosztási rendszerével](static-ip.md)
* [Belső terheléselosztás használata Azure Container Service (AKS)](internal-lb.md)

* [Alapszintű bejövő forgalomvezérlő létrehozása külső hálózati kapcsolattal][aks-ingress-basic]
* [A HTTP-alkalmazás-útválasztási bővítmény engedélyezése][aks-http-app-routing]
* [Belső, magánhálózatot és IP-címet használó bejövő vezérlő létrehozása][aks-ingress-internal]
* [Hozzon létre egy bejövő forgalomvezérlőt dinamikus nyilvános IP-címmel, és konfigurálja a Let's Encryptet TLS-tanúsítványok automatikus létrehozására][aks-ingress-tls]
* [Hozzon létre egy bejövő vezérlőt statikus nyilvános IP-címmel, és konfigurálja a Let's Encryptet TLS-tanúsítványok automatikus létrehozására][aks-ingress-static-tls]
<!-- IMAGES -->
[advanced-networking-diagram-01]: ./media/networking-overview/advanced-networking-diagram-01.png [portal-01-networking-advanced]: ./media/networking-overview/portal-01-networking-advanced.png

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
