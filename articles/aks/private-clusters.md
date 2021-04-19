---
title: Privát fürt Azure Kubernetes Service létrehozása
description: Megtudhatja, hogyan hozhat létre Azure Kubernetes Service (AKS-) fürtöt
services: container-service
ms.topic: article
ms.date: 3/31/2021
ms.openlocfilehash: 339bb41aed5ead3d7ee7d1217bfbc771cf068832
ms.sourcegitcommit: 79c9c95e8a267abc677c8f3272cb9d7f9673a3d7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/19/2021
ms.locfileid: "107719114"
---
# <a name="create-a-private-azure-kubernetes-service-cluster"></a>Privát fürt Azure Kubernetes Service létrehozása

Privát fürtben a vezérlősík vagy az API-kiszolgáló belső IP-címekkel rendelkezik, amelyek az [RFC1918 – Címfoglalás](https://tools.ietf.org/html/rfc1918) magánhálózati internethez dokumentumban vannak meghatározva. Privát fürt használatával biztosíthatja, hogy az API-kiszolgáló és a csomópontkészletek közötti hálózati forgalom csak a magánhálózaton maradjon.

A vezérlősík vagy AZ API-kiszolgáló egy Azure Kubernetes Service (AKS) által felügyelt Azure-előfizetésben található. Az ügyfél fürtje vagy csomópontkészlete az ügyfél előfizetésében található. A kiszolgáló és a fürt vagy a csomópontkészlet az [API-kiszolgáló][private-link-service] virtuális hálózatának Azure Private Link-szolgáltatásán és az ügyfél AKS-fürt alhálózatán elérhető privát végponton keresztül kommunikálhat egymással.

## <a name="region-availability"></a>Régiónkénti elérhetőség

A privát fürt nyilvános régiókban, Azure Government és Azure China 21Vianet, ahol az [AKS támogatott.](https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service)

> [!NOTE]
> Azure Government helyek támogatottak, US Gov Texas jelenleg nem támogatottak a hiányzó Private Link miatt.

## <a name="prerequisites"></a>Előfeltételek

* Az Azure CLI 2.2.0-s vagy újabb verziója
* Az Private Link szolgáltatás csak a Standard Azure Load Balancer támogatott. Az Azure Load Balancer nem támogatott.  
* Egyéni DNS-kiszolgáló használata esetén adja hozzá a Azure DNS 168.63.129.16 IP-címet a felfelé irányuló DNS-kiszolgálóként az egyéni DNS-kiszolgálón.

## <a name="create-a-private-aks-cluster"></a>Privát AKS-fürt létrehozása

### <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

Hozzon létre egy erőforráscsoportot, vagy használjon egy meglévő erőforráscsoportot az AKS-fürthöz.

```azurecli-interactive
az group create -l westus -n MyResourceGroup
```

### <a name="default-basic-networking"></a>Alapértelmezett alapszintű hálózat 

```azurecli-interactive
az aks create -n <private-cluster-name> -g <private-cluster-resource-group> --load-balancer-sku standard --enable-private-cluster  
```
Ahol `--enable-private-cluster` a kötelező jelző egy privát fürthöz. 

### <a name="advanced-networking"></a>Speciális hálózat  

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
Ahol `--enable-private-cluster` a kötelező jelző egy privát fürthöz. 

> [!NOTE]
> Ha a Docker-híd CIDR-címe (172.17.0.1/16) ütközik az alhálózat CIDR-ével, módosítsa megfelelően a Docker-híd címét.

## <a name="configure-private-dns-zone"></a>A saját DNS konfigurálása 

Az alábbi paramétereket használhatja a zóna saját DNS konfigurálása érdekében.

- Az alapértelmezett érték a "Rendszer". Ha a --private-dns-zone argumentum nincs megadva, az AKS létrehoz egy saját DNS zónát a csomópont-erőforráscsoportban.
- A "Nincs" azt jelenti, hogy az AKS nem hoz létre saját DNS zónát.  Ehhez saját DNS-kiszolgálót kell hoznia, és konfigurálnia kell a DNS-feloldásokat a privát teljes tartománynévhez.  Ha nem konfigurálja a DNS-feloldást, a DNS csak az ügynökcsomópontokon belül feloldható, és az üzembe helyezés után fürt problémákat okoz. 
- A "CUSTOM_PRIVATE_DNS_ZONE_RESOURCE_ID" használatához létre kell hoznia egy saját DNS zónát a következő formátumban az Azure globális felhőhöz: `privatelink.<region>.azmk8s.io` . A következő lépésként szüksége lesz az adott saját DNS erőforrás-azonosítójára.  Emellett szüksége lesz egy felhasználó által hozzárendelt identitásra vagy szolgáltatásnévre, amely legalább a és a `private dns zone contributor`  szerepkört `vnet contributor` is be van osztva.
- Az "fqdn-subdomain" a "CUSTOM_PRIVATE_DNS_ZONE_RESOURCE_ID" csak az altartomány képességeinek biztosítanak `privatelink.<region>.azmk8s.io`

### <a name="prerequisites"></a>Előfeltételek

* Az AKS Előzetes verzió 0.5.7-es vagy újabb verziója
* Az API 2020-11-01-es vagy újabb verziója

### <a name="create-a-private-aks-cluster-with-private-dns-zone"></a>Privát AKS-fürt létrehozása saját DNS zónával

```azurecli-interactive
az aks create -n <private-cluster-name> -g <private-cluster-resource-group> --load-balancer-sku standard --enable-private-cluster --enable-managed-identity --assign-identity <ResourceId> --private-dns-zone [system|none]
```

### <a name="create-a-private-aks-cluster-with-a-custom-private-dns-zone"></a>Privát AKS-fürt létrehozása egyéni saját DNS zónával

```azurecli-interactive
az aks create -n <private-cluster-name> -g <private-cluster-resource-group> --load-balancer-sku standard --enable-private-cluster --enable-managed-identity --assign-identity <ResourceId> --private-dns-zone <custom private dns zone ResourceId> --fqdn-subdomain <subdomain-name>
```

## <a name="options-for-connecting-to-the-private-cluster"></a>A magánfürthöz való csatlakozás lehetőségei

Az API-kiszolgálóvégpontnak nincs nyilvános IP-címe. Az API-kiszolgáló kezeléséhez olyan virtuális gépet kell használnia, amely hozzáféréssel rendelkezik az AKS-fürt Azure Virtual Network (VNet) szolgáltatásához. A magánfürthöz való hálózati kapcsolat létesítéhez több lehetőség is rendelkezésre áll.

* Hozzon létre egy virtuális gépet ugyanabban az Azure Virtual Network (VNet) és az AKS-fürt között.
* Használjon egy virtuális gépet egy külön hálózatban, és állítsa be a [virtuális hálózatok közötti társviszonyt.][virtual-network-peering]  Erről a lehetőségről az alábbi szakaszban olvashat bővebben.
* Express [Route- vagy VPN-kapcsolatot][express-route-or-VPN] használjon.
* Használja az [AKS parancsfuttatás funkciót.](#aks-run-command-preview)

A legegyszerűbb megoldás egy virtuális gép létrehozása az AKS-fürttel azonos virtuális hálózatban.  Az Express Route és a VPN-ek további költségekkel járnak, és további hálózati összetettséget igényelnek.  A virtuális hálózatok közötti társviszony létesítéséhez meg kell tervezni a hálózati CIDR-tartományokat, hogy ne legyen átfedésben.

### <a name="aks-run-command-preview"></a>AKS-parancsfuttatás (előzetes verzió)

Jelenleg, ha egy magánfürtöt kell elérnie, azt a fürt virtuális hálózatán, egy társhálózaton vagy egy ügyfélszámítógépen belül kell megtennie. Ehhez általában VPN-en vagy Express Route-on keresztül kell csatlakoztatni a gépet a fürt virtuális hálózatához, vagy jumpboxot kell létrehozni a fürt virtuális hálózatában. Az AKS futtatás parancsa lehetővé teszi parancsok távoli meghívását egy AKS-fürtben az AKS API-n keresztül. Ez a funkció egy olyan API-t biztosít, amely lehetővé teszi például, hogy egy privát fürthöz egy távoli laptopról hajtsa végre az éppen szükséges parancsokat. Ez nagyban segíthet a privát fürtök gyors, időbeni elérésében, ha az ügyfélszámítógép nem a fürt magánhálózatán található, miközben megtartja és kikényszi ugyanezeket az RBAC-vezérlőket és a privát API-kiszolgálót.

### <a name="register-the-runcommandpreview-preview-feature"></a>Az előzetes `RunCommandPreview` verziójú funkció regisztrálása

Az új parancsfuttatás API használatához engedélyeznie kell a funkciójelölőt `RunCommandPreview` az előfizetésében.

Regisztrálja `RunCommandPreview` a funkciójelölőt az [az feature register][az-feature-register] paranccsal az alábbi példában látható módon:

```azurecli-interactive
az feature register --namespace "Microsoft.ContainerService" --name "RunCommandPreview"
```

Eltarthat néhány percig, hogy az állapot Regisztrált *állapotúra mutasson.* Ellenőrizze a regisztráció állapotát az [az feature list paranccsal:][az-feature-list]

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/RunCommandPreview')].{Name:name,State:properties.state}"
```

Ha készen áll, frissítse a *Microsoft.ContainerService* erőforrás-szolgáltató regisztrációját az [az provider register paranccsal:][az-provider-register]

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

### <a name="use-aks-run-command"></a>AKS-parancsfuttatás

Egyszerű parancs

```azurecli-interactive
az aks command invoke -g <resourceGroup> -n <clusterName> -c "kubectl get pods -n kube-system"
```

Jegyzék üzembe helyezése az adott fájl csatolása alapján

```azurecli-interactive
az aks command invoke -g <resourceGroup> -n <clusterName> -c "kubectl apply -f deployment.yaml -n default" -f deployment.yaml
```

Jegyzék üzembe helyezése egy egész mappa csatolása alapján

```azurecli-interactive
az aks command invoke -g <resourceGroup> -n <clusterName> -c "kubectl apply -f deployment.yaml -n default" -f .
```

Helm-telepítés végrehajtása és az adott értékek jegyzékfájlja

```azurecli-interactive
az aks command invoke -g <resourceGroup> -n <clusterName> -c "helm repo add bitnami https://charts.bitnami.com/bitnami && helm repo update && helm install my-release -f values.yaml bitnami/nginx" -f values.yaml
```

## <a name="virtual-network-peering"></a>Társviszony létesítése virtuális hálózatok között

Ahogy említettük, a virtuális hálózatok közötti társviszony-létesítés a privát fürt elérésének egyik módja. A virtuális hálózatok közötti társviszony-létesítéshez be kell állítania egy kapcsolatot a virtuális hálózat és a privát DNS-zóna között.
    
1. A csomópont erőforráscsoportját a Azure Portal.  
2. Válassza ki a privát DNS-zónát.   
3. A bal oldali panelen válassza a **Virtuális hálózat hivatkozást.**  
4. Hozzon létre egy új hivatkozást, amely hozzáadja a virtuális gép virtuális hálózatát a privát DNS-zónához. Eltarthat néhány percig, hogy a DNS-zóna hivatkozása elérhetővé váljon.  
5. A Azure Portal keresse meg a fürt virtuális hálózatát tartalmazó erőforráscsoportot.  
6. A jobb oldali panelen válassza ki a virtuális hálózatot. A virtuális hálózat neve *aks-vnet- \* formában van.*  
7. A bal oldali panelen válassza a **Társviszonyok lehetőséget.**  
8. Válassza **a Hozzáadás** lehetőséget, adja hozzá a virtuális gép virtuális hálózatát, majd hozza létre a társviszonyt.  
9. Válassza ki a virtuális hálózatot, ahol a virtuális gép található, válassza a Társviszonyok **lehetőséget,** válassza ki az AKS virtuális hálózatot, majd hozza létre a társviszonyt. Ha az AKS virtuális hálózaton lévő címtartományok és a virtuális gép virtuális hálózata ütközik, a társviszony-létesítés meghiúsul. További információ: Virtuális [hálózatok közötti társviszony létesítés.][virtual-network-peering]

## <a name="hub-and-spoke-with-custom-dns"></a>Küllős küllős konfiguráció egyéni DNS-sel

[A küllős architektúrákat](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke) gyakran használják hálózatok üzembe helyezésére az Azure-ban. Sok ilyen környezetben a küllő virtuális hálózatok DNS-beállításai úgy vannak konfigurálva, hogy egy központi DNS-továbbítóra hivatkozzon, hogy lehetővé tegye a helyszíni és az Azure-alapú DNS-feloldásokat. Amikor ilyen hálózati környezetben helyez üzembe egy AKS-fürtöt, figyelembe kell vennie néhány speciális szempontot.

![Privát fürtközpont és küllő](media/private-clusters/aks-private-hub-spoke.png)

1. Ha magánfürtöt létesít, alapértelmezés szerint egy privát végpontot (1) és egy privát DNS-zónát (2) hoz létre a fürt által felügyelt erőforráscsoportban. A fürt egy A rekordot használ a privát zónában a privát végpont IP-címének feloldásához az API-kiszolgálóval való kommunikációhoz.

2. A privát DNS-zóna csak ahhoz a virtuális hálózathoz kapcsolódik, amelyhez a fürtcsomópontok csatlakoznak (3). Ez azt jelenti, hogy a privát végpontot csak a csatolt virtuális hálózat gazdagépe oldhatja fel. Olyan forgatókönyvekben, ahol nincs egyéni DNS konfigurálva a virtuális hálózatban (alapértelmezés), ez probléma nélkül működik, mivel a 168.63.129.16-os állomáspont a DNS esetében a kapcsolat miatt fel tudja oldani a privát DNS-zónában lévő rekordokat.

3. Ha a fürtöt tartalmazó virtuális hálózat egyéni DNS-beállításokkal (4) rendelkezik, a fürt üzembe helyezése meghiúsul, kivéve, ha a privát DNS-zóna az egyéni DNS-feloldókat tartalmazó virtuális hálózathoz van kapcsolva (5). Ez a hivatkozás manuálisan is létrehozható, miután a privát zóna létrejött a fürt kiépítése során, vagy a zóna eseményalapú üzembehelyezéssel történő létrehozásának észlelése után automatizálható (például Azure Event Grid vagy Azure Functions).

> [!NOTE]
> Ha a Saját útválasztási táblázat használata a [Kubenethez](./configure-kubenet.md#bring-your-own-subnet-and-route-table-with-kubenet) és a Saját DNS használata privát fürthöz lehetőséget használja, a fürt létrehozása sikertelen lesz. A sikeres létrehozáshoz társítania kell a Csomópont erőforráscsoportban található [RouteTable-t](./configure-kubenet.md#bring-your-own-subnet-and-route-table-with-kubenet) az alhálózathoz a fürt létrehozása után.

## <a name="limitations"></a>Korlátozások 
* Az IP-cím engedélyezett tartományai nem alkalmazhatók a privát API-kiszolgáló végpontjára, csak a nyilvános API-kiszolgálóra vonatkoznak
* [Azure Private Link szolgáltatáskorlátozások vonatkoznak][private-link-service] a privát fürtökre.
* Nem támogatott az Azure DevOps Microsoft által üzemeltetett ügynökök privát fürtökön. Fontolja meg a saját [üzemeltetett ügynökök használatát.](/azure/devops/pipelines/agents/agents?tabs=browser) 
* Azok az ügyfelek, akik Azure Container Registry AKS-sel való munkát, az Container Registry virtuális hálózatot társviszonyban kell létesítenie az ügynökfürt virtuális hálózatával.
* Nem támogatott a meglévő AKS-fürtök privát fürtökké konvertálása
* Ha törli vagy módosítja a privát végpontot az ügyfél alhálózatán, a fürt működése leáll. 
* Miután az ügyfelek frissítették az A rekordot a saját DNS-kiszolgálóikon, ezek a podok a migrálás után is feloldják az apiserver teljes tartománynevét a régebbi IP-címekre, amíg újra nem indítják őket. Az ügyfeleknek újra kell indítania a gazdagéphálózati podokat és a default-DNSPolicy podokat a vezérlősík migrálását követően.
* A vezérlősík karbantartása esetén az [AKS IP-címe](./limit-egress-traffic.md) változhat. Ebben az esetben frissítenie kell az A rekordot, amely az API-kiszolgáló magánhálózati IP-címére mutat az egyéni DNS-kiszolgálón, és újra kell indítania az egyéni podokat és üzemelő példányokat a hostNetwork használatával.

<!-- LINKS - internal -->
[az-provider-register]: /cli/azure/provider#az-provider-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[private-link-service]: ../private-link/private-link-service-overview.md#limitations
[virtual-network-peering]: ../virtual-network/virtual-network-peering-overview.md
[azure-bastion]: ../bastion/tutorial-create-host-portal.md
[express-route-or-vpn]: ../expressroute/expressroute-about-virtual-network-gateways.md
[devops-agents]: /azure/devops/pipelines/agents/agents
[availability-zones]: availability-zones.md
