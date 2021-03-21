---
title: Nyilvános Load Balancer használata
titleSuffix: Azure Kubernetes Service
description: Megtudhatja, hogyan használhatja a nyilvános Load balancert egy standard SKU-val, hogy szolgáltatásai elérhetők legyenek az Azure Kubernetes szolgáltatással (ak).
services: container-service
ms.topic: article
ms.date: 11/14/2020
ms.author: jpalma
author: palma21
ms.openlocfilehash: e37c5a748a8e99f49e3535946268427139bbbf44
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102184423"
---
# <a name="use-a-public-standard-load-balancer-in-azure-kubernetes-service-aks"></a>Nyilvános standard Load Balancer használata az Azure Kubernetes szolgáltatásban (ak)

A Azure Load Balancer a nyílt rendszerek összekapcsolási (OSI) modellje, amely mind a bejövő, mind a kimenő forgatókönyveket támogatja. Elosztja a terheléselosztó előtér-példányaira érkező bejövő folyamatokat.

Az AK-val integrált **nyilvános** Load Balancer két célt szolgálnak:

1. A fürt csomópontjain belüli kimenő kapcsolatok biztosításához az AK virtuális hálózaton belül. Ezt a célt úgy éri el, hogy a csomópontok magánhálózati IP-címét egy olyan nyilvános IP-címhez fordítja le, amely a *kimenő készlet* részét képezi.
2. Az alkalmazásokhoz való hozzáférés biztosítása Kubernetes-szolgáltatásokon keresztül `LoadBalancer` . Ezzel könnyedén méretezheti alkalmazásait, és létrehozhat egy magasan elérhető szolgáltatásokat.

Egy **belső (vagy privát)** terheléselosztó akkor használatos, ha csak a magánhálózati IP-címek engedélyezettek a rendszerfelületként. A belső terheléselosztó a virtuális hálózaton belüli forgalom elosztására szolgál. A terheléselosztó felülete egy helyszíni hálózatról is elérhető hibrid forgatókönyv esetén.

Ez a dokumentum a nyilvános Load balancerrel való integrációt ismerteti. A belső Load Balancer integrálásával kapcsolatban tekintse meg az [AK belső terheléselosztó dokumentációját](internal-lb.md).

## <a name="before-you-begin"></a>Előkészületek

Azure Load Balancer két SKU-ban érhető el – *Alapszintű* és *standard*. Alapértelmezés szerint a *standard* SKU-t használja a rendszer, amikor egy AK-fürtöt hoz létre. A *standard* SKU használatával férhet hozzá a hozzáadott funkciókhoz, például egy nagyobb háttérrendszer-készlethez, [**több csomópontos**](use-multiple-node-pools.md)készlethez és [**Availability Zoneshoz**](availability-zones.md). Ez az ajánlott Load Balancer SKU az AK-hoz.

Az *alapszintű* és a *standard* SKU-ról további információt az [Azure Load Balancer SKU-összehasonlítását][azure-lb-comparison]ismertető témakörben talál.

Ez a cikk feltételezi, hogy van egy AK-fürtje a *szabványos* SKU Azure Load Balancer, és végigvezeti a terheléselosztó egyes képességeinek és funkcióinak használatáról és konfigurálásáról. Ha AK-fürtre van szüksége, tekintse meg az AK gyors üzembe helyezését [Az Azure CLI használatával][aks-quickstart-cli] vagy [a Azure Portal használatával][aks-quickstart-portal].

> [!IMPORTANT]
> Ha inkább nem szeretné kihasználni a Azure Load Balancer a kimenő kapcsolatok biztosításához, és helyette saját átjárója van, akkor a tűzfal vagy a proxy erre a célra kihagyhatja a terheléselosztó kimenő készletének létrehozását és a megfelelő előtér-IP-címet a [**kimenő típus UserDefinedRouting (UDR)**](egress-outboundtype.md)való használatával. A kimenő típus határozza meg a kilépési módszert egy fürthöz, és alapértelmezés szerint a következőt írja be: Load Balancer.

## <a name="use-the-public-standard-load-balancer"></a>A nyilvános standard Load Balancer használata

Miután létrehozta az AK-fürtöt a kimenő típussal: Load Balancer (alapértelmezett), a fürt készen áll arra, hogy a terheléselosztó használatával elérhetővé tegye a szolgáltatásokat is.

Ahhoz, hogy létre lehessen hozni egy olyan típusú nyilvános szolgáltatást `LoadBalancer` , amely az alábbi példában látható. Első lépésként hozzon létre egy nevű szolgáltatási jegyzékfájlt `public-svc.yaml` :

```yaml
apiVersion: v1
kind: Service
metadata:
  name: public-svc
spec:
  type: LoadBalancer
  ports:
  - port: 80
  selector:
    app: public-app
```

Telepítse a nyilvános szolgáltatás jegyzékfájlját a [kubectl alkalmazásával][kubectl-apply] , és adja meg a YAML-jegyzék nevét:

```azurecli-interactive
kubectl apply -f public-svc.yaml
```

A Azure Load Balancer egy új nyilvános IP-címmel lesz konfigurálva, amely megkezdi ezt az új szolgáltatást. Mivel a Azure Load Balancer több előtér-IP-címmel is rendelkezhet, minden új szolgáltatás üzembe helyezése egy új dedikált előtér-IP-címet kap, amely egyedi módon elérhetővé válik.

Ellenőrizheti, hogy a szolgáltatás létrejött-e, és hogy a terheléselosztó konfigurálva van-e, például:

```azurecli-interactive
kubectl get service public-svc
```

```console
NAMESPACE     NAME          TYPE           CLUSTER-IP     EXTERNAL-IP     PORT(S)         AGE
default       public-svc    LoadBalancer   10.0.39.110    52.156.88.187   80:32068/TCP    52s
```

A szolgáltatás részleteinek megtekintésekor a terheléselosztó szolgáltatáshoz létrehozott nyilvános IP-cím megjelenik a *külső IP-* oszlopban. Előfordulhat, hogy az IP-cím egy percet vagy kettőt is igénybe vehet, és a *\<pending\>* fenti példában látható módon megváltoznak.

## <a name="configure-the-public-standard-load-balancer"></a>A nyilvános standard Load Balancer konfigurálása

A standard SKU nyilvános terheléselosztó használatakor lehetőség van a létrehozási időben vagy a fürt frissítésével testreszabható beállításokra. Ezekkel a beállításokkal testreszabhatja a Load Balancer, hogy megfeleljen a számítási feladatoknak, és ennek megfelelően felül kell vizsgálni. A standard Load balancerrel a következőket teheti:

* A felügyelt kimenő IP-címek számának beállítása vagy méretezése
* Saját egyéni kimenő IP [-címek vagy kimenő IP-előtag](#provide-your-own-outbound-public-ips-or-prefixes) használata
* A lefoglalt kimenő portok számának testreszabása a fürt mindegyik csomópontján
* Az üresjárati kapcsolatok időtúllépési beállításának konfigurálása

> [!IMPORTANT]
> Egy adott időpontban csak egy kimenő IP-beállítás (felügyelt IP-címek, saját IP-cím vagy IP-előtag) használható.

### <a name="scale-the-number-of-managed-outbound-public-ips"></a>A felügyelt kimenő nyilvános IP-címek számának skálázása

Azure Load Balancer kimenő kapcsolatot biztosít egy virtuális hálózattól a bejövő adatok mellett. A kimenő szabályok egyszerűvé teszik a nyilvános standard Load Balancer kimenő hálózati címfordításának konfigurálását.

Az összes Load Balancer-szabályhoz hasonlóan a kimenő szabályok ugyanazt a szintaxist követik, mint a terheléselosztás és a bejövő NAT-szabályok:

***előtér-IP-címek + paraméterek + háttér-készlet***

Egy kimenő szabály konfigurálja a kimenő NAT-t a háttér-készlet által azonosított összes virtuális gép számára, hogy a rendszer lefordítsa a felületet. A és a paraméterek további részletes szabályozást biztosítanak a kimenő NAT-algoritmushoz képest.

Míg egy kimenő szabály csak egyetlen nyilvános IP-címmel használható, a kimenő szabályok megkönnyítik a kimenő NAT skálázásának konfigurációs terhelését. Több IP-címet is használhat a nagy léptékű forgatókönyvek tervezéséhez, és a kimenő szabályok használatával csökkentheti a SNAT kimerülésének hajlamos mintázatát. A frontend által biztosított minden további IP-cím a SNAT-portokként való használatra Load Balancer 64 KB-os ideiglenes portot biztosít. 

Ha egy *szabványos* SKU-Load balancert használ a felügyelt kimenő nyilvános IP-címekkel, amelyek alapértelmezés szerint jönnek létre, akkor a paraméter használatával méretezheti a felügyelt kimenő nyilvános IP-címek számát **`load-balancer-managed-ip-count`** .

Meglévő fürt frissítéséhez futtassa a következő parancsot. Ez a paraméter a fürt létrehozási ideje beállításnál is beállítható, hogy több felügyelt kimenő nyilvános IP-cím legyen.

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --load-balancer-managed-outbound-ip-count 2
```

A fenti példa a felügyelt kimenő nyilvános IP-címek számát állítja be a *myResourceGroup*-ben található *myAKSCluster* - *fürt esetében.* 

A **`load-balancer-managed-ip-count`** paraméter használatával beállíthatja a felügyelt kimenő nyilvános IP-címek kezdeti számát a fürt létrehozásakor a paraméter hozzáfűzésével **`--load-balancer-managed-outbound-ip-count`** és a kívánt értékre való beállításával. A felügyelt kimenő nyilvános IP-címek alapértelmezett száma 1.

### <a name="provide-your-own-outbound-public-ips-or-prefixes"></a>Saját kimenő nyilvános IP-címek vagy előtagok megadása

Ha *standard* SKU Load balancert használ, alapértelmezés szerint az Kabai fürt automatikusan létrehoz egy nyilvános IP-címet az AK által felügyelt infrastruktúra-erőforráscsoport számára, és hozzárendeli azt a terheléselosztó kimenő készletéhez.

Az AK által létrehozott nyilvános IP-címek egy AK által felügyelt erőforrásnak tekintendők. Ez azt jelenti, hogy a nyilvános IP-cím életciklusát az AK felügyeli, és nem igényel felhasználói beavatkozást közvetlenül a nyilvános IP-erőforráson. Azt is megteheti, hogy a fürt létrehozásakor saját egyéni nyilvános IP-címet vagy nyilvános IP-előtagot rendel hozzá. Az egyéni IP-címek egy meglévő fürt terheléselosztó-tulajdonságain is frissíthetők.

A saját nyilvános IP-cím vagy előtag használatára vonatkozó követelmények:

- Az egyéni nyilvános IP-címeket a felhasználónak kell létrehoznia és birtokolnia. Az AK által létrehozott felügyelt nyilvános IP-címek nem használhatók fel a saját egyéni IP-címének használatára, mivel a felügyeleti ütközéseket okozhatnak.
- Győződjön meg arról, hogy az AK-fürt identitása (egyszerű szolgáltatásnév vagy felügyelt identitás) rendelkezik a kimenő IP-címek eléréséhez szükséges engedélyekkel. A [szükséges nyilvános IP-engedélyek listájának](kubernetes-service-principal.md#networking)megfelelően.
- Győződjön meg arról, hogy megfelel a kimenő IP-címek vagy a kimenő IP-előtagok konfigurálásához szükséges előfeltételeknek [és megkötéseknek](../virtual-network/public-ip-address-prefix.md#constraints) .

#### <a name="update-the-cluster-with-your-own-outbound-public-ip"></a>A fürt frissítése saját kimenő nyilvános IP-címmel

A nyilvános IP-címek azonosítóinak listázásához használja az az [Network Public-IP show][az-network-public-ip-show] parancsot.

```azurecli-interactive
az network public-ip show --resource-group myResourceGroup --name myPublicIP --query id -o tsv
```

A fenti parancs a *myPublicIP* nyilvános IP-CÍMÉnek azonosítóját jeleníti meg a *myResourceGroup* -erőforráscsoporthoz.

A `az aks update` parancs és a **`load-balancer-outbound-ips`** paraméter használatával frissítse a fürtöt a nyilvános IP-címekkel.

A következő példa a `load-balancer-outbound-ips` paramétert használja az előző parancs azonosítói alapján.

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --load-balancer-outbound-ips <publicIpId1>,<publicIpId2>
```

#### <a name="update-the-cluster-with-your-own-outbound-public-ip-prefix"></a>A fürt frissítése saját kimenő nyilvános IP-előtaggal

Nyilvános IP-előtagokat is használhat a *standard* SKU Load balancerrel való kimenő forgalomhoz. Az alábbi példa az az [Network Public-IP előtag show][az-network-public-ip-prefix-show] parancsot használja a nyilvános IP-előtagok azonosítóinak listázásához:

```azurecli-interactive
az network public-ip prefix show --resource-group myResourceGroup --name myPublicIPPrefix --query id -o tsv
```

A fenti parancs a *myPublicIPPrefix* nyilvános IP-előtag azonosítóját jeleníti meg a *myResourceGroup* erőforráscsoporthoz.

Az alábbi példa a *Load-Balancer-kimenő-IP-előtag* paramétert használja az előző parancs azonosítói alapján.

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --load-balancer-outbound-ip-prefixes <publicIpPrefixId1>,<publicIpPrefixId2>
```

#### <a name="create-the-cluster-with-your-own-public-ip-or-prefixes"></a>A fürt létrehozása saját nyilvános IP-címmel vagy előtagokkal

Előfordulhat, hogy saját IP-címeket vagy IP-előtagokat kíván létrehozni a kimenő forgalomhoz a fürt létrehozási ideje alatt, hogy támogassa a kimenő végpontok engedélyezési listához való hozzáadását. Fűzze hozzá ugyanezeket a paramétereket a fürt létrehozási lépéseként, hogy meghatározza a saját nyilvános IP-címeit és az IP-előtagokat a fürt életciklusának elején.

*A* *Load-Balancer-kimenő-IPS* paraméterrel hozzon létre egy új fürtöt a nyilvános IP-címekkel az elején.

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --load-balancer-outbound-ips <publicIpId1>,<publicIpId2>
```

Használja az az *AK Create* parancsot a *Load-Balancer-kimenő-IP-előtag* paraméterrel egy új fürt létrehozásához a nyilvános IP-előtagokkal az elején.

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --load-balancer-outbound-ip-prefixes <publicIpPrefixId1>,<publicIpPrefixId2>
```

### <a name="configure-the-allocated-outbound-ports"></a>A lefoglalt kimenő portok konfigurálása

> [!IMPORTANT]
> Ha olyan alkalmazásokkal rendelkezik a fürtön, amelyek nagy számú kapcsolatot kívánnak létrehozni kis mennyiségű célállomással, például:. az SQL DB-hez csatlakozó előtér-példányok sok esetben nagyon hajlamosak arra, hogy SNAT a portok kimerülését (kifogyott a portokról a csatlakozáshoz). Ezekben a forgatókönyvekben erősen ajánlott a lefoglalt kimenő portok és a kimeneti előtérbeli IP-címek bővítése a terheléselosztó esetében. A növekedésnek figyelembe kell vennie, hogy egy (1) további IP-cím további 64 millió további portot használ az összes fürtcsomóponton való terjesztéshez.


Ha másként nincs megadva, az AK a standard Load Balancer által definiált lefoglalt kimenő portok alapértelmezett értékét fogja használni a konfigurálásakor. Ez az érték **null értékű** az AK API-ban, vagy **0** a SLB API-ban, ahogy az alábbi parancs is mutatja:

```azurecli-interactive
NODE_RG=$(az aks show --resource-group myResourceGroup --name myAKSCluster --query nodeResourceGroup -o tsv)
az network lb outbound-rule list --resource-group $NODE_RG --lb-name kubernetes -o table
```

Az előző parancs a terheléselosztó kimenő szabályát fogja kilistázni, például:

```console
AllocatedOutboundPorts    EnableTcpReset    IdleTimeoutInMinutes    Name             Protocol    ProvisioningState    ResourceGroup
------------------------  ----------------  ----------------------  ---------------  ----------  -------------------  -------------
0                         True              30                      aksOutboundRule  All         Succeeded            MC_myResourceGroup_myAKSCluster_eastus  
```

Ez a kimenet nem jelenti azt, hogy 0 porttal rendelkezik, hanem a [háttérrendszer-készlet mérete alapján kihasználja az automatikus kimenő port hozzárendelését][azure-lb-outbound-preallocatedports], így például ha egy fürt 50-as vagy kevesebb csomóponttal rendelkezik, az egyes csomópontok esetében 1024-portok vannak lefoglalva, mivel a csomópontok számának növelésével a csomópontok száma fokozatosan kevesebb portot kap.


A lefoglalt kimenő portok számának meghatározásához vagy növeléséhez kövesse az alábbi példát:


```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --load-balancer-managed-outbound-ip-count 7 \
    --load-balancer-outbound-ports 4000
```

Ebben a példában a fürt minden csomópontja számára 4000 lefoglalt kimenő portot, a 7 4000 IP-címmel pedig a *csomópontok száma * 100 csomópont = 400k összes portja < = 448k Total ports = 7 IP-címek * 64 GB-os port/IP*. Ez lehetővé teszi, hogy biztonságosan méretezhető 100-csomópontra, és alapértelmezett frissítési művelettel rendelkezzen. Fontos, hogy elegendő portot foglaljon le a frissítéshez és egyéb műveletekhez szükséges további csomópontokhoz. Az AK alapértelmezett értéke az egyik puffer csomópontja a frissítéshez, ebben a példában ehhez az adott időpontban 4000 szabad port szükséges. MaxSurge- [értékek](upgrade-cluster.md#customize-node-surge-upgrade)használata esetén a maxSurge értékével szorozza meg a kimenő portokat.

A 100-csomópontok feletti biztonság érdekében további IP-címeket kell hozzáadnia.


> [!IMPORTANT]
> Ki kell [számítania a szükséges kvótát, és ellenőriznie kell a követelményeket][requirements] , mielőtt testreszabja a *allocatedOutboundPorts* a kapcsolatok és a skálázási problémák elkerüléséhez.

A **`load-balancer-outbound-ports`** paramétereket a fürt létrehozásakor is használhatja, de meg kell adnia a vagy a, **`load-balancer-managed-outbound-ip-count`** **`load-balancer-outbound-ips`** vagy is **`load-balancer-outbound-ip-prefixes`** .  Például:

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --load-balancer-sku standard \
    --load-balancer-managed-outbound-ip-count 2 \
    --load-balancer-outbound-ports 1024 
```

### <a name="configure-the-load-balancer-idle-timeout"></a>A terheléselosztó üresjárati időkorlátjának konfigurálása

A SNAT-portok erőforrásainak kimerítése esetén a kimenő folyamatok meghiúsulnak, amíg a meglévő folyamatok SNAT-portokat nem szabadítanak fel. Load Balancer visszaállítja a SNAT-portokat a folyamat bezárásakor, és az AK-konfigurált terheléselosztó 30 perces üresjárati időkorlátot használ a SNAT-portok üresjárati forgalomból való visszaigényléséhez.
Az átvitelt (például) is használhatja, **`TCP keepalives`** vagy **`application-layer keepalives`** egy üresjárati folyamat frissítésére, és szükség esetén alaphelyzetbe állíthatja az üresjárati időkorlátot. Ezt az időtúllépést az alábbi példát követve állíthatja be: 


```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --load-balancer-idle-timeout 4
```

Ha várhatóan több rövid életű kapcsolatra van szüksége, és nem áll rendelkezésre hosszú ideig tartó kapcsolat, és előfordulhat, hogy az `kubectl proxy` `kubectl port-forward` alacsony időtúllépési érték (például 4 perc) használata hosszabb ideig tart. A TCP-Keepalives használata esetén is elegendő, ha engedélyezi őket a csatlakozás egyik oldalán. Például elegendő, ha csak a kiszolgáló oldalon engedélyezi őket, hogy alaphelyzetbe állítsa a folyamat üresjárati időzítőjét, és mindkét fél számára nem szükséges a TCP-Keepalives elindításához. Hasonló fogalmak léteznek az alkalmazási réteghez, beleértve az adatbázis-ügyfél-kiszolgáló konfigurációkat is. Tekintse meg a kiszolgáló oldalát, hogy milyen lehetőségek léteznek az alkalmazásspecifikus Keepalives.

> [!IMPORTANT]
> Az AK alapértelmezés szerint engedélyezi a TCP alaphelyzetbe állítását, és azt javasolja, hogy ezt a konfigurációt továbbra is megtartsa, és kihasználja azt a forgatókönyvek további kiszámítható alkalmazása érdekében.
> Az első TCP-t a rendszer csak a TCP-kapcsolatok során, a létesített állapotban továbbítja. [Itt](../load-balancer/load-balancer-tcp-reset.md) talál további információt.

### <a name="requirements-for-customizing-allocated-outbound-ports-and-idle-timeout"></a>A lefoglalt kimenő portok és az üresjárat időkorlátjának testreszabására vonatkozó követelmények

- A *allocatedOutboundPorts* megadott értéknek a 8-as többszörösének is kell lennie.
- A csomópont virtuális gépei és a szükséges lefoglalt kimenő portok száma alapján elegendő kimenő IP-kapacitással kell rendelkeznie. A következő képlettel ellenőrizheti, hogy van-e elegendő kimenő IP-kapacitása: 
 
*outboundIPs* \* 64 000 \> *nodeVMs* \* *desiredAllocatedOutboundPorts*.
 
Ha például 3 *nodeVMs* van, és 50 000 *desiredAllocatedOutboundPorts*, legalább 3 *outboundIPs* kell lennie. Javasoljuk, hogy a szükségesnél újabb kimenő IP-kapacitást építsen ki. Emellett a fürt automéretezőjét és a csomópont-készlet frissítésének lehetőségét is figyelembe kell vennie a kimenő IP-kapacitás kiszámításakor. A fürt autoskálázása esetében tekintse át az aktuális csomópontok darabszámát és a csomópontok maximális darabszámát, és használja a magasabb értéket. A frissítéshez az összes olyan csomópont-készlethez, amely lehetővé teszi a frissítését, egy további csomópontos virtuális gép számára.

- Ha a *IdleTimeoutInMinutes* eltérő értékre állítja be, mint az alapértelmezett 30 perc, akkor vegye figyelembe, hogy a számítási feladatoknak mennyi ideig kell kiadniuk a kimenő kapcsolatokat. Azt is vegye figyelembe, hogy egy *standard* SKU-Load Balancer alapértelmezett időtúllépési értéke 4 perc. Egy olyan *IdleTimeoutInMinutes* -érték, amely pontosabban tükrözi az adott AK-beli munkaterhelést, csökkentheti a SNAT okozta kimerültséget, mivel a kapcsolatok már nincsenek használatban.

> [!WARNING]
> Ha módosítja a *AllocatedOutboundPorts* és a *IdleTimeoutInMinutes* értékeit, jelentősen megváltoztathatja a terheléselosztó kimenő szabályának viselkedését, és a kompromisszumok és az alkalmazás kapcsolati mintáinak megismerése nélkül nem kell megtörténnie, a [SNAT hibaelhárítási szakaszában][troubleshoot-snat] ellenőrizze az alábbi lépéseket, és tekintse át a [Load Balancer kimenő][azure-lb-outbound-rules-overview] és [kimenő kapcsolatokat az Azure-ban][azure-lb-outbound-connections] , mielőtt frissíti ezeket az értékeket a módosítások hatásának teljes megértéséhez

## <a name="restrict-inbound-traffic-to-specific-ip-ranges"></a>A bejövő forgalom korlátozása adott IP-tartományokra

A következő jegyzékfájl a *loadBalancerSourceRanges* -t használja a bejövő külső forgalomhoz tartozó új IP-címtartomány megadásához:

```yaml
apiVersion: v1
kind: Service
metadata:
  name: azure-vote-front
spec:
  type: LoadBalancer
  ports:
  - port: 80
  selector:
    app: azure-vote-front
  loadBalancerSourceRanges:
  - MY_EXTERNAL_IP_RANGE
```

> [!NOTE]
> Bejövő, külső forgalom a terheléselosztó és az AK-fürt virtuális hálózata között áramlik. A virtuális hálózat egy hálózati biztonsági csoporttal (NSG) rendelkezik, amely lehetővé teszi a terheléselosztó összes bejövő forgalmát. Ez a NSG a *terheléselosztó* típusú [szolgáltatási címkét][service-tags] használja, hogy engedélyezze a forgalmat a terheléselosztó számára.

## <a name="maintain-the-clients-ip-on-inbound-connections"></a>Az ügyfél IP-címének karbantartása bejövő kapcsolatokon

Alapértelmezés szerint a `LoadBalancer` [Kubernetes](https://kubernetes.io/docs/tutorials/services/source-ip/#source-ip-for-services-with-type-loadbalancer) és az AK-ban található típusú szolgáltatások nem őrzik meg az ügyfél IP-címét a pod-nal létesített kapcsolatban. A pod számára továbbított csomag forrás IP-címe a csomópont magánhálózati IP-címe lesz. Az ügyfél IP-címének fenntartásához be kell állítania `service.spec.externalTrafficPolicy` a `local` szolgáltatást a szolgáltatás definíciójában. A következő jegyzékfájl egy példát mutat be:

```yaml
apiVersion: v1
kind: Service
metadata:
  name: azure-vote-front
spec:
  type: LoadBalancer
  externalTrafficPolicy: Local
  ports:
  - port: 80
  selector:
    app: azure-vote-front
```

## <a name="additional-customizations-via-kubernetes-annotations"></a>További testreszabások Kubernetes-jegyzetek használatával

Az alábbi lista a Kubernetes-szolgáltatások típussal támogatott megjegyzéseit sorolja fel `LoadBalancer` , ezek a jegyzetek csak a **bejövő** folyamatokra érvényesek:

| Jegyzet | Érték | Leírás
| ----------------------------------------------------------------- | ------------------------------------- | ------------------------------------------------------------ 
| `service.beta.kubernetes.io/azure-load-balancer-internal`         | `true` vagy `false`                     | Annak megadása, hogy a terheléselosztó belső legyen-e. Alapértelmezés szerint a nyilvános, ha nincs beállítva.
| `service.beta.kubernetes.io/azure-load-balancer-internal-subnet`  | Az alhálózat neve                    | Határozza meg, hogy melyik alhálózathoz kell kötni a belső terheléselosztó. Ha nincs beállítva, a rendszer alapértelmezés szerint a Cloud config fájlban konfigurált alhálózatot állítja be.
| `service.beta.kubernetes.io/azure-dns-label-name`                 | A nyilvános IP-címeken lévő DNS-címke neve   | Adja meg a **nyilvános** szolgáltatás DNS-címkéjének nevét. Ha üres karakterláncra van beállítva, a rendszer nem használja a nyilvános IP-címen található DNS-bejegyzést.
| `service.beta.kubernetes.io/azure-shared-securityrule`            | `true` vagy `false`                     | Annak megadása, hogy a szolgáltatás elérhető legyen egy másik szolgáltatással megosztható Azure biztonsági szabály használatával, a szabályok kereskedelmi sajátossága, amely az elérhető szolgáltatások számának növekedését eredményezi. Ez a jegyzet a hálózati biztonsági csoportok Azure [kibővített biztonsági szabályok](../virtual-network/network-security-groups-overview.md#augmented-security-rules) funkcióját veszi alapul. 
| `service.beta.kubernetes.io/azure-load-balancer-resource-group`   | Az erőforráscsoport neve            | Itt adhatja meg a terheléselosztó nyilvános IP-címeinek azon erőforrásait, amelyek nem ugyanabban az erőforráscsoporthoz vannak, mint a fürt-infrastruktúra (csomópont-erőforráscsoport).
| `service.beta.kubernetes.io/azure-allowed-service-tags`           | Engedélyezett szolgáltatási címkék listája          | Itt adhatja meg az engedélyezett [szolgáltatási címkék][service-tags] vesszővel elválasztott listáját.
| `service.beta.kubernetes.io/azure-load-balancer-tcp-idle-timeout` | TCP Üresjárati időkorlát (perc)          | Itt adhatja meg, hogy hány perc múlva történjen a TCP-kapcsolat üresjárati időtúllépése a terheléselosztó esetében. Az alapértelmezett és a minimális érték 4. A maximális érték 30. Egész számnak kell lennie.
|`service.beta.kubernetes.io/azure-load-balancer-disable-tcp-reset` | `true`                                | `enableTcpReset`SLB letiltása


## <a name="troubleshooting-snat"></a>SNAT hibaelhárítása

Ha tudja, hogy több kimenő TCP-vagy UDP-kapcsolatra van szüksége ugyanahhoz a cél IP-címhez és porthoz, és megfigyelheti a sikertelen kimenő kapcsolatokat, vagy ha támogatja a SNAT-portok kimerítésének támogatását (a PAT által használt, előzetesen lefoglalt ideiglenes portok), számos általános kockázatcsökkentő lehetőség közül választhat. Tekintse át ezeket a beállításokat, és döntse el, hogy mi az elérhető és melyik a legmegfelelőbb a forgatókönyvhöz. Lehetséges, hogy egy vagy több segíthet a forgatókönyv kezelésében. Részletes információkért tekintse át a [Kimenő kapcsolatok hibaelhárítási útmutatóját](../load-balancer/troubleshoot-outbound-connection.md).

A SNAT-kimerülés kiváltó oka gyakran a kimenő kapcsolat létesítésének, kezelésének vagy konfigurálható időzítőknek az alapértelmezett értékekről való változásának egy anti-mintázata. Alaposan olvassa át ezt a szakaszt.

### <a name="steps"></a>Lépések
1. Ellenőrizze, hogy a kapcsolatok hosszú ideig tétlenek maradnak-e, és hogy az adott port kiadásának alapértelmezett üresjárati időkorlátját használja-e. Ha így van, előfordulhat, hogy az alapértelmezett 30 perces időkorlátot csökkenteni kell a forgatókönyv esetében.
2. Vizsgálja meg, hogy az alkalmazás hogyan hozza létre a kimenő kapcsolatokat (például a kód felülvizsgálatát vagy a csomagok rögzítését).
3. Állapítsa meg, hogy a tevékenység várható viselkedés-e, vagy hogy az alkalmazás nem működik-e. A Azure Monitor [metrikák](../load-balancer/load-balancer-standard-diagnostics.md) és [naplók](../load-balancer/load-balancer-monitor-log.md) használata az eredmények alátámasztására. Használja például a "sikertelen" kategóriát a SNAT-kapcsolatok mérőszámához.
4. Értékelje ki, hogy a megfelelő [mintákat](#design-patterns) követi-e.
5. Értékelje ki, hogy a SNAT-portok kimerülését ki kell-e enyhíteni [további kimenő IP-címekkel + további lefoglalt kimenő portokkal](#configure-the-allocated-outbound-ports) .

### <a name="design-patterns"></a>Tervezési minták
Mindig használja ki a kapcsolatok újrafelhasználását és a kapcsolatok készletezését, amikor csak lehetséges. Ezek a minták elkerülik az erőforrás-kimerülési problémákat, és kiszámítható viselkedést eredményeznek. A mintákhoz tartozó primitívek számos fejlesztői könyvtárban és keretrendszerben találhatók.

- Az atomi kérelmek (egy kérelem kapcsolatonként) általában nem jó kialakítás. Az ilyen jellegű Anti-pattern korlátozza a méretezést, csökkenti a teljesítményt, és csökkenti a megbízhatóságot. Ehelyett használja újra a HTTP/S-kapcsolatokat a kapcsolatok és a társított SNAT-portok számának csökkentése érdekében. Az alkalmazások méretezése a TLS használata esetén a kisebb kézfogások, a terhelési és a titkosítási műveletek költségeinek növekedésével és teljesítményével nő.
- Ha fürt/egyéni DNS-t használ, vagy a coreDNS lévő egyéni felsőbb rétegbeli kiszolgálókat is figyelembe kell vennie, a DNS-ben számos különálló folyamat is bevezethető, ha az ügyfél nem gyorsítótárazza a DNS-feloldók eredményét. Ügyeljen rá, hogy az egyéni DNS-kiszolgálók használata helyett először a coreDNS szabja meg, és adjon meg egy jó gyorsítótárazási értéket.
- Az UDP-folyamatok (például a DNS-lekérdezések) lefoglalják a SNAT-portokat az Üresjárati időkorlát időtartamára. Minél hosszabb az Üresjárati időkorlát, annál nagyobb a terhelés a SNAT-portokon. Használjon rövid üresjárati időkorlátot (például 4 perc).
A kapcsolatok kötetét a kapcsolódási készletek használatával formázhatja.
- Soha ne hagyjon le csendes TCP-forgalmat, és a TCP-időzítők használatával törölje a folyamatot. Ha nem engedi, hogy a TCP explicit módon lezárta a kapcsolatot, az állapot a közbenső rendszerek és végpontok számára is lefoglalva marad, és a SNAT portok nem érhetők el más kapcsolatok számára. Ez a minta aktiválhatja az alkalmazások hibáit és SNAT a kimerültséget.
- Ne változtassa meg az operációs rendszer szintű TCP-hez kapcsolódó időzítő-értékeket a hatás szakértői ismerete nélkül. A TCP-verem helyreállítása közben az alkalmazás teljesítménye negatív hatással lehet, ha a kapcsolatok végpontjai eltérő elvárásokkal rendelkeznek. Az időzítők módosítása általában egy mögöttes tervezési probléma jele. Tekintse át a következő javaslatokat.


A fenti példa frissíti a szabályt úgy, hogy csak a *MY_EXTERNAL_IP_RANGE* tartomány bejövő külső forgalmát engedélyezze. Ha a *MY_EXTERNAL_IP_RANGEt* a belső alhálózat IP-címére cseréli le, a forgalom csak a fürt belső IP-címeire korlátozódik. Ez nem teszi lehetővé a Kubernetes-fürtön kívüli ügyfelek számára a terheléselosztó elérését.

## <a name="moving-from-a-basic-sku-load-balancer-to-standard-sku"></a>Áttérés alapszintű SKU Load balancerről standard SKU-ra

Ha rendelkezik egy meglévő, alapszintű SKU-Load Balancer rendelkező fürttel, akkor fontos, hogy a rendszer az áttelepítés során vegye figyelembe, hogy mikor kell a standard SKU-Load Balancer használatával fürtöt használni.

Tegyük fel például, hogy a fürtök áttelepítésére szolgáló kék/zöld központi telepítések egy gyakori eljárás, mivel a `load-balancer-sku` fürt típusa csak a fürt létrehozási ideje alatt definiálható. Az *alapszintű SKU* -Load Balancer azonban *alapszintű SKU* IP-címeket használ, amelyek nem kompatibilisek a *standard* SKU-beli Load balancerekkel, mivel *szabványos SKU* IP-címeket igényelnek. Amikor a fürtöket áttelepíti Load Balancer SKU-ra, egy kompatibilis IP-címmel rendelkező új IP-címet kell megadni.

A fürtök áttelepítésével kapcsolatos további szempontokért tekintse meg a [dokumentációt az áttelepítési megfontolásokból](aks-migration.md) , és tekintse meg az áttelepítés során megfontolandó fontos témakörök listáját. Az alábbi korlátozások szintén fontos viselkedési különbségeket is figyelembe vesznek, ha standard SKU Load Balancert használ az AK-ban.

## <a name="limitations"></a>Korlátozások

A következő korlátozások érvényesek a terheléselosztó és a *szabványos* SKU-t támogató AK-fürtök létrehozásakor és kezelésekor:

* Legalább egy nyilvános IP-cím vagy IP-előtag szükséges ahhoz, hogy a kimenő forgalom az AK-fürtből legyen engedélyezve. A nyilvános IP-cím vagy IP-előtag szükséges a vezérlési sík és az ügynök csomópontjai közötti kapcsolat fenntartásához, valamint az AK korábbi verzióival való kompatibilitás fenntartásához. A következő lehetőségek közül választhat a *szabványos* SKU Load balancerrel rendelkező nyilvános IP-címek vagy IP-előtagok megadásához:
    * Adja meg saját nyilvános IP-címeit.
    * Adja meg saját nyilvános IP-előtagjait.
    * 100-ig terjedő számot kell megadnia, amely lehetővé teszi, hogy az AK-fürt számos *szabványos* SKU-beli nyilvános IP-címet hozzon létre ugyanabban az erőforráscsoporthoz, amely az AK-fürthöz lett létrehozva, amelyet általában a *MC_* elején neveznek el. Az AK a nyilvános IP-címet a *standard* SKU Load Balancerhez rendeli. Alapértelmezés szerint a rendszer automatikusan létrehoz egy nyilvános IP-címet ugyanabban az erőforráscsoportban, mint az AK-fürtöt, ha nincs megadva nyilvános IP-cím, nyilvános IP-előtag vagy IP-címek száma. Emellett engedélyeznie kell a nyilvános címeket, és el kell kerülnie az IP-létrehozást megtiltó Azure Policy létrehozását.
* Az AK által létrehozott nyilvános IP-címek nem használhatók fel újra egyéni saját nyilvános IP-címként. A felhasználónak minden egyéni IP-címet létre kell hoznia és kezelnie kell.
* A terheléselosztó SKU definiálása csak akkor hajtható végre, ha AK-fürtöt hoz létre. A terheléselosztó SKU-t egy AK-fürt létrehozása után nem módosíthatja.
* Egyetlen fürtben csak egyetlen típusú terheléselosztó SKU-t (alapszintű vagy standard) használhat.
* *Standard szintű* Az SKU-terheléselosztó csak a *szabványos* SKU IP-címeket támogatja.


## <a name="next-steps"></a>Következő lépések

További információ a Kubernetes Services szolgáltatásról a [Kubernetes Services dokumentációjában][kubernetes-services].

További információk a belső Load Balancer a bejövő forgalomhoz való használatáról az [AK belső Load Balancer dokumentációjában](internal-lb.md).

<!-- LINKS - External -->
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubectl-delete]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#delete
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubernetes-services]: https://kubernetes.io/docs/concepts/services-networking/service/
[aks-engine]: https://github.com/Azure/aks-engine

<!-- LINKS - Internal -->
[advanced-networking]: configure-azure-cni.md
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[aks-sp]: kubernetes-service-principal.md#delegate-access-to-other-azure-resources
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-aks-create]: /cli/azure/aks#az-aks-create
[az-aks-get-credentials]: /cli/azure/aks#az-aks-get-credentials
[az-aks-install-cli]: /cli/azure/aks#az-aks-install-cli
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-group-create]: /cli/azure/group#az-group-create
[az-provider-register]: /cli/azure/provider#az-provider-register
[az-network-lb-outbound-rule-list]: /cli/azure/network/lb/outbound-rule#az-network-lb-outbound-rule-list
[az-network-public-ip-show]: /cli/azure/network/public-ip#az-network-public-ip-show
[az-network-public-ip-prefix-show]: /cli/azure/network/public-ip/prefix#az-network-public-ip-prefix-show
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[azure-lb]: ../load-balancer/load-balancer-overview.md
[azure-lb-comparison]: ../load-balancer/skus.md
[azure-lb-outbound-rules]: ../load-balancer/load-balancer-outbound-connections.md#outboundrules
[azure-lb-outbound-connections]: ../load-balancer/load-balancer-outbound-connections.md
[azure-lb-outbound-preallocatedports]: ../load-balancer/load-balancer-outbound-connections.md#preallocatedports
[azure-lb-outbound-rules-overview]: ../load-balancer/load-balancer-outbound-connections.md#outboundrules
[install-azure-cli]: /cli/azure/install-azure-cli
[internal-lb-yaml]: internal-lb.md#create-an-internal-load-balancer
[kubernetes-concepts]: concepts-clusters-workloads.md
[use-kubenet]: configure-kubenet.md
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[requirements]: #requirements-for-customizing-allocated-outbound-ports-and-idle-timeout
[use-multiple-node-pools]: use-multiple-node-pools.md
[troubleshoot-snat]: #troubleshooting-snat
[service-tags]: ../virtual-network/network-security-groups-overview.md#service-tags
