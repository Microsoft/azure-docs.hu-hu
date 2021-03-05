---
title: Privát Azure Kubernetes Service-fürt létrehozása
description: Ismerje meg, hogyan hozhat létre egy privát Azure Kubernetes Service-(ak-) fürtöt
services: container-service
ms.topic: article
ms.date: 7/17/2020
ms.openlocfilehash: f0c74c1b3715fd3f5c83c3a9231009e622b87927
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2021
ms.locfileid: "102181227"
---
# <a name="create-a-private-azure-kubernetes-service-cluster"></a>Privát Azure Kubernetes Service-fürt létrehozása

Egy privát fürtben a vezérlő síkja vagy az API-kiszolgáló belső IP-címekkel rendelkezik, amelyek a [RFC1918-címek kiosztásában](https://tools.ietf.org/html/rfc1918) vannak meghatározva a magánhálózati internetes dokumentumokhoz. Privát fürt használatával biztosíthatja, hogy az API-kiszolgáló és a csomópont-készletek közötti hálózati forgalom csak a magánhálózaton maradjon.

A vezérlő síkja vagy az API-kiszolgáló egy Azure Kubernetes szolgáltatásban (ak) felügyelt Azure-előfizetésben található. Az ügyfél fürtje vagy csomópont-készlete az ügyfél előfizetésében található. A kiszolgáló és a fürt vagy a csomópont-készlet képes kommunikálni egymással az API-kiszolgáló virtuális hálózatának [Azure Private link szolgáltatásával][private-link-service] , valamint egy olyan privát végponttal, amely az ügyfél AK-fürt alhálózatán van kitéve.

## <a name="region-availability"></a>Régiónkénti elérhetőség

A privát fürt nyilvános régiókban, Azure Government és Azure China 21Vianet-régiókban érhető el, ahol az [AK támogatott](https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service).

> [!NOTE]
> Azure Government helyek támogatottak, azonban US Gov Texas jelenleg nem támogatott, mert hiányzik a privát kapcsolat támogatása.

## <a name="prerequisites"></a>Előfeltételek

* Az Azure CLI 2.2.0 vagy újabb verziója
* A Private link Service csak a standard Azure Load Balancer esetén támogatott. Az alapszintű Azure Load Balancer nem támogatott.  
* Ha egyéni DNS-kiszolgálót szeretne használni, adja hozzá a Azure DNS IP-168.63.129.16 a felsőbb rétegbeli DNS-kiszolgálóként az egyéni DNS-kiszolgálón.

## <a name="create-a-private-aks-cluster"></a>Privát AK-fürt létrehozása

### <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

Hozzon létre egy erőforráscsoportot, vagy használjon egy meglévő erőforráscsoportot az AK-fürthöz.

```azurecli-interactive
az group create -l westus -n MyResourceGroup
```

### <a name="default-basic-networking"></a>Alapértelmezett alapszintű hálózatkezelés 

```azurecli-interactive
az aks create -n <private-cluster-name> -g <private-cluster-resource-group> --load-balancer-sku standard --enable-private-cluster  
```
Ahol a egy `--enable-private-cluster` privát fürt kötelező jelzője. 

### <a name="advanced-networking"></a>Speciális hálózatkezelés  

```azurecli-interactive
az aks create \
    --resource-group <private-cluster-resource-group> \
    --name <private-cluster-name> \
    --load-balancer-sku standard \
    --enable-private-cluster \
    --network-plugin azure \
    --vnet-subnet-id <subnet-id> \
    --docker-bridge-address 172.17.0.1/16 \
    --dns-service-ip 10.2.0.10 \
    --service-cidr 10.2.0.0/24 
```
Ahol a egy `--enable-private-cluster` privát fürt kötelező jelzője. 

> [!NOTE]
> Ha a Docker-híd CIDR (172.17.0.1/16) ütközne az alhálózati CIDR, módosítsa a Docker-híd megfelelőjét.

## <a name="configure-private-dns-zone"></a>saját DNS zóna konfigurálása 

A következő paraméterek használhatók a saját DNS zónák konfigurálásához.

1. A "System" az alapértelmezett érték. Ha a--Private-DNS-Zone argumentum ki van hagyva, az AK létrehoz egy saját DNS zónát a csomópont-erőforráscsoporthoz.
2. A "None" érték azt jelenti, hogy az AK nem hoz létre saját DNS zónát.  Ehhez a saját DNS-kiszolgálót kell használnia, és konfigurálnia kell a DNS-feloldást a privát FQDN számára.  Ha nem konfigurálja a DNS-feloldást, a DNS csak az ügynök-csomópontokon belül oldható fel, és a telepítés után problémát okoz a fürtben.
3. Az "egyéni DNS-zóna neve" formátumnak ebben a formátumban kell lennie az Azure globális felhőhöz: `privatelink.<region>.azmk8s.io` . Szüksége lesz az adott saját DNS zónához tartozó erőforrás-azonosítóra.  Emellett szüksége lesz egy felhasználóhoz rendelt identitásra vagy egyszerű szolgáltatásra, amely legalább a `private dns zone contributor` szerepkört az egyéni magánhálózati DNS-zónához rendeli.

### <a name="prerequisites"></a>Előfeltételek

* Az AK előzetes verziójának 0.4.71 vagy újabb verziója
* Az API 2020-11-01-es vagy újabb verziója

### <a name="create-a-private-aks-cluster-with-private-dns-zone-preview"></a>Privát AK-fürt létrehozása saját DNS zónával (előzetes verzió)

```azurecli-interactive
az aks create -n <private-cluster-name> -g <private-cluster-resource-group> --load-balancer-sku standard --enable-private-cluster --enable-managed-identity --assign-identity <ResourceId> --private-dns-zone [none|system|custom private dns zone ResourceId]
```
## <a name="options-for-connecting-to-the-private-cluster"></a>A privát fürthöz való csatlakozás lehetőségei

Az API-kiszolgáló végpontjának nincs nyilvános IP-címe. Az API-kiszolgáló kezeléséhez olyan virtuális gépet kell használnia, amely hozzáféréssel rendelkezik az AK-fürt Azure-Virtual Networkához (VNet). Több lehetőség is van a magánhálózati kapcsolat létrehozására a privát fürthöz.

* Hozzon létre egy virtuális gépet ugyanabba az Azure-Virtual Networkba (VNet), mint az AK-fürtöt.
* Használjon különálló hálózatban található virtuális GÉPET, és állítsa be a [virtuális hálózatok][virtual-network-peering]közötti társítást.  Erről a lehetőségről az alábbi szakaszban talál további információt.
* [Express Route-vagy VPN-][express-route-or-VPN] kapcsolat használata.

A legegyszerűbb lehetőség a virtuális gép létrehozása ugyanabban a VNET, mint az AK-fürt.  Az expressz útvonal és a VPN-EK növelik a költségeket és további hálózati bonyolultságot igényelnek.  A virtuális hálózat társításához meg kell terveznie a hálózati CIDR-tartományokat, hogy ne legyenek átfedésben lévő tartományok.

## <a name="virtual-network-peering"></a>Társviszony létesítése virtuális hálózatok között

Ahogy azt említettük, a virtuális hálózatok egymáshoz való hozzáférésének egyik módja a privát fürt elérésének. A virtuális hálózati kapcsolatok használatához létre kell hoznia egy kapcsolatot a virtuális hálózat és a magánhálózati DNS-zóna között.
    
1. Nyissa meg a Azure Portal csomópont-erőforráscsoportot.  
2. Válassza ki a magánhálózati DNS-zónát.   
3. A bal oldali ablaktáblán válassza ki a **virtuális hálózati** kapcsolatot.  
4. Hozzon létre egy új hivatkozást, amely hozzáadja a virtuális gép virtuális hálózatát a magánhálózati DNS-zónához. Néhány percet vesz igénybe, amíg a DNS-zóna hivatkozása elérhetővé válik.  
5. A Azure Portal navigáljon a fürt virtuális hálózatát tartalmazó erőforráscsoporthoz.  
6. A jobb oldali ablaktáblában válassza ki a virtuális hálózatot. A virtuális hálózat neve: *AK-vnet- \**.  
7. A bal oldali ablaktáblán válassza **a** társítások lehetőséget.  
8. Válassza a **Hozzáadás** lehetőséget, adja hozzá a virtuális gép virtuális hálózatát, majd hozza létre a társítást.  
9. Nyissa meg a virtuális hálózatot, ahol a virtuális gép rendelkezik **, válassza a társítások lehetőséget,** válassza ki az AK-beli virtuális hálózatot, majd hozza létre a társítást. Ha a címtartomány az AK-beli virtuális hálózaton és a virtuális gép virtuális hálózatának összevonásán alapul, a társítás sikertelen lesz. További információ:  [Virtual Network peering][virtual-network-peering].

## <a name="hub-and-spoke-with-custom-dns"></a>A hub és a küllő egyéni DNS-sel

A központilag [és küllős architektúrákat](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke) gyakran használják az Azure-beli hálózatok üzembe helyezésére. Ezen telepítések többségében a küllős virtuális hálózatok DNS-beállításai úgy vannak konfigurálva, hogy egy központi DNS-továbbítóra hivatkozzon, amely lehetővé teszi a helyszíni és az Azure-alapú DNS-feloldást. Ha egy AK-fürtöt egy ilyen hálózati környezetben helyez üzembe, néhány speciális szempontot figyelembe kell venni.

![Privát fürt hub és küllő](media/private-clusters/aks-private-hub-spoke.png)

1. Alapértelmezés szerint a rendszer a fürt által felügyelt erőforráscsoporthoz (1) és egy privát DNS-zónát (2) hoz létre egy privát fürt üzembe helyezésekor. A fürt egy rekordot használ a privát zónában az API-kiszolgálóval való kommunikációhoz használt privát végpont IP-címének feloldásához.

2. A magánhálózati DNS-zóna csak azon VNet van csatolva, amelyhez a fürtcsomópontok csatlakoznak (3). Ez azt jelenti, hogy a magánhálózati végpontot csak az adott csatolt VNet lévő gazdagépek oldják fel. Olyan esetekben, amikor nincs egyéni DNS konfigurálva a VNet (alapértelmezett), ez nem jelent problémát, mert a 168.63.129.16-hoz tartozó DNS-hez tartozó gazdagépek a hivatkozás miatt feloldhatók a magánhálózati DNS-zónában lévő rekordokkal.

3. Olyan esetekben, amikor a fürtöt tartalmazó VNet egyéni DNS-beállításokkal rendelkezik (4), a fürt üzembe helyezése meghiúsul, ha a magánhálózati DNS-zóna az egyéni DNS-feloldókat (5) tartalmazó VNet van társítva. Ez a hivatkozás manuálisan hozható létre, miután a privát zóna létrejött a fürt kiépítése során vagy az automatizáláson keresztül, amikor az eseményvezérelt központi telepítési mechanizmusok használatával észleli a zóna létrehozását (például Azure Event Grid és Azure Functions).

> [!NOTE]
> Ha a saját [útválasztási táblázatot használja a kubenet](./configure-kubenet.md#bring-your-own-subnet-and-route-table-with-kubenet) , és saját DNS-t használ privát fürttel, a fürt létrehozása sikertelen lesz. Ahhoz, hogy a létrehozás sikeres legyen, a csomópont-erőforráscsoport [útvonaltábla](./configure-kubenet.md#bring-your-own-subnet-and-route-table-with-kubenet) hozzá kell rendelnie az alhálózathoz.

## <a name="limitations"></a>Korlátozások 
* A jogosult IP-címtartományok nem alkalmazhatók a privát API-kiszolgálói végpontra, csak a nyilvános API-kiszolgálóra érvényesek.
* Az [Azure Private link Service korlátozásai][private-link-service] a privát fürtökre vonatkoznak.
* Az Azure DevOps nem támogatja a Microsoft által üzemeltetett ügynököket privát fürtökkel. Érdemes lehet saját üzemeltetésű [ügynököket](/azure/devops/pipelines/agents/agents?tabs=browser)használni. 
* Azon ügyfelek számára, akik számára engedélyezni kell a Azure Container Registryt a privát AK-val való munkavégzéshez, az Container Registry virtuális hálózatot az ügynök-fürt virtuális hálózatának kell megadnia.
* Meglévő AK-fürtök privát fürtökre való konvertálása nem támogatott
* Ha törli vagy módosítja a magánhálózati végpontot az ügyfél alhálózatán, a fürt működése leáll. 
* Miután az ügyfelek frissítették a rekordot a saját DNS-kiszolgálóin, ezek a hüvelyek továbbra is feloldják a apiserver teljes tartománynevét a régebbi IP-címekre a Migrálás után, amíg újra nem indulnak. Az ügyfeleknek újra kell indítaniuk a hostNetwork-hüvelyeket és az alapértelmezett-DNSPolicy hüvelyeket a vezérlési sík áttelepítése után.
* Ha karbantartást végez a vezérlési síkon, az [AK IP-címe](./limit-egress-traffic.md) változhat. Ebben az esetben frissítenie kell az a rekordot, amely az API-kiszolgáló magánhálózati IP-címére mutat az egyéni DNS-kiszolgálón, és minden egyéni hüvelyt vagy üzemelő példányt újraindít a hostNetwork használatával.

<!-- LINKS - internal -->
[az-provider-register]: /cli/azure/provider#az-provider-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[private-link-service]: ../private-link/private-link-service-overview.md#limitations
[virtual-network-peering]: ../virtual-network/virtual-network-peering-overview.md
[azure-bastion]: ../bastion/tutorial-create-host-portal.md
[express-route-or-vpn]: ../expressroute/expressroute-about-virtual-network-gateways.md
[devops-agents]: /azure/devops/pipelines/agents/agents?view=azure-devops
[availability-zones]: availability-zones.md
