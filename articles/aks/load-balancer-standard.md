---
title: Nyilvános nyilvános Load Balancer
titleSuffix: Azure Kubernetes Service
description: Ismerje meg, hogyan használható nyilvános terheléselosztás standard termékváltozatokkal a szolgáltatások Azure Kubernetes Service (AKS) használatával.
services: container-service
ms.topic: article
ms.date: 11/14/2020
ms.author: jpalma
author: palma21
ms.openlocfilehash: 3f2219f5052aee0c0a9cd43aa87df8789adbcae2
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107783088"
---
# <a name="use-a-public-standard-load-balancer-in-azure-kubernetes-service-aks"></a>Nyilvános nyilvános standard Load Balancer használata Azure Kubernetes Service (AKS)

A Azure Load Balancer A nyílt rendszerek összekapcsolása (OSI) modell L4-es verziója, amely a bejövő és kimenő forgatókönyveket is támogatja. Elosztja a terheléselosztó előtere felé érkező bejövő folyamatokat a háttérkészlet példányai között.

A **nyilvános Load Balancer** AKS-sel integrálva két célt szolgál:

1. Kimenő kapcsolatok az AKS virtuális hálózaton belüli fürtcsomópontokhoz. Ezt a célt úgy éri el, hogy a csomópontok magánhálózati IP-címét lefordítja egy nyilvános IP-címre, amely a kimenő készlet *része.*
2. Az alkalmazásokhoz való hozzáférés a típusú Kubernetes-szolgáltatásokon `LoadBalancer` keresztül. A segítségével könnyedén skálázhatja az alkalmazásait, és magas rendelkezésre álló szolgáltatásokat hozhat létre.

Belső **(vagy privát)** terheléselosztást használ, ahol csak privát IP-k engedélyezettek előtereként. A belső terheléselosztási rendszer a virtuális hálózaton belüli forgalom terheléselosztásához használható. A terheléselosztási előtere egy hibrid forgatókönyvben helyszíni hálózatról is elérhető.

Ez a dokumentum a nyilvános Load Balancer integrációját fedi le. Belső terheléselosztási Load Balancer az [AKS belső terheléselosztási dokumentációjában talál.](internal-lb.md)

## <a name="before-you-begin"></a>Előkészületek

Azure Load Balancer szolgáltatás két SKUS-ban érhető el: *Alapszintű* és *Standard*. Alapértelmezés szerint *a Standard* termékváltozatot használja a rendszer az AKS-fürtök létrehozásakor. A *Standard* termékváltozattal további funkciókhoz férhet hozzá, például egy [](use-multiple-node-pools.md)nagyobb háttérkészlethez, több csomópontkészlethez és [**Availability Zones.**](availability-zones.md) Ez az AKS Load Balancer ajánlott termékváltozata.

További információ az  alapszintű és *a standard* termékváltozatról: Azure [Load Balancer termékváltozatok összehasonlítása.][azure-lb-comparison]

Ez a cikk feltételezi, hogy rendelkezik  egy Standard termékváltozatú AKS-fürtgal Azure Load Balancer és végigvezeti a terheléseltöltő egyes képességeinek és funkcióinak használatán és konfigurálásán. Ha AKS-fürtre van szüksége, tekintse meg az AKS rövid útmutatóját az [Azure CLI][aks-quickstart-cli] vagy a [Azure Portal.][aks-quickstart-portal]

> [!IMPORTANT]
> Ha nem szeretné a Azure Load Balancer-t kimenő kapcsolat létrehozására használni, és ehelyett saját átjáróval, tűzfallal vagy proxyval rendelkezik erre a célra, kihagyhatja a terheléseltöltő kimenő készletének és a megfelelő előtere IP-címének létrehozását, ha a kimenő típust [**UserDefinedRouting (UDR)**](egress-outboundtype.md)típusúként használja. A Kimenő típus határozza meg a fürt kimenő forgalomra vonatkozó metódusát, és alapértelmezés szerint a következőt írja be: load balancer.

## <a name="use-the-public-standard-load-balancer"></a>A nyilvános standard terheléselosztás használata

Miután létrehozta az AKS-fürtöt kimenő típussal: Load Balancer (alapértelmezés), a fürt készen áll arra, hogy a terheléseltöltővel is elérhetővé tegye a szolgáltatásokat.

A számára létrehozhat egy típusú nyilvános szolgáltatást `LoadBalancer` az alábbi példában látható módon. Először hozzon létre egy nevű `public-svc.yaml` szolgáltatásjegyzéket:

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

Telepítse a nyilvános szolgáltatás jegyzékfájlt a [kubectl apply][kubectl-apply] használatával, és adja meg a YAML-jegyzék nevét:

```azurecli-interactive
kubectl apply -f public-svc.yaml
```

A Azure Load Balancer egy új nyilvános IP-címmel lesz konfigurálva, amely az új szolgáltatás előtt áll. Mivel a Azure Load Balancer több előtere IP-címmel is rendelkezik, minden üzembe helyezett új szolgáltatás egy új dedikált előtere IP-címet kap, amely egyedi módon elérhető.

Meggyőződhet arról, hogy a szolgáltatás létrejött, és a terheléselosztás konfigurálva van, ha például a következőt futtatja:

```azurecli-interactive
kubectl get service public-svc
```

```console
NAMESPACE     NAME          TYPE           CLUSTER-IP     EXTERNAL-IP     PORT(S)         AGE
default       public-svc    LoadBalancer   10.0.39.110    52.156.88.187   80:32068/TCP    52s
```

A szolgáltatás részleteinek megtekintésekor a terheléselosztáson a szolgáltatáshoz létrehozott nyilvános *IP-cím az EXTERNAL-IP* oszlopban jelenik meg. A fenti példában látható módon egy-két percet is igénybe vehet, amíg az IP-címről egy tényleges nyilvános *\<pending\>* IP-címre vált.

## <a name="configure-the-public-standard-load-balancer"></a>A nyilvános standard terheléselosztás konfigurálása

A Standard termékváltozatú nyilvános terheléselosztási eszköz használata esetén a beállítások a létrehozáskor vagy a fürt frissítésével szabhatók testre. Ezekkel a beállításokkal testreszabhatja a Load Balancer, hogy megfeleljen a számítási feladatok igényeinek, és ennek megfelelően kell azokat felülvizsgálni. A Standard terheléselosztással a következőt használhatja:

* A felügyelt kimenő IP-k számának beállítása vagy méretezése
* Saját egyéni kimenő [IP-címek vagy kimenő IP-előtagok](#provide-your-own-outbound-public-ips-or-prefixes)
* A fürt egyes csomópontjaihoz lefoglalt kimenő portok számának testreszabása
* Az üresjárati kapcsolatok időtúllépési beállításának konfigurálása

> [!IMPORTANT]
> Egy adott időpontban csak egy kimenő IP-cím használható (felügyelt IP-címek, saját IP-cím használata vagy IP-előtag).

### <a name="scale-the-number-of-managed-outbound-public-ips"></a>Felügyelt kimenő nyilvános IP-k számának méretezése

Azure Load Balancer a bejövő mellett a virtuális hálózat kimenő kapcsolatát is biztosítja. A kimenő szabályok egyszerűvé teszik a nyilvános standard Load Balancer a kimenő hálózati címfordítást.

Mint minden Load Balancer, a kimenő szabályok is ugyanazt a szintaxist követik, mint a terheléselosztás és a bejövő NAT-szabályok:

***előtere IP-k + paraméterek + háttérkészlet***

A kimenő szabály a háttérkészlet által azonosított összes virtuális gép kimenő NAT-ját konfigurálja az előtere fordítására. A paraméterek további részletes vezérlést biztosítanak a kimenő NAT-algoritmus felett.

Bár egy kimenő szabály csak egyetlen nyilvános IP-címmel használható, a kimenő szabályok megkönnyítik a kimenő NAT skálázásának konfigurációs terhét. Nagy léptékű forgatókönyvek tervezéséhez több IP-címet is használhat, kimenő szabályokkal pedig mérsékelheti a SNAT-fogyásban bővelkedő mintákat. Az előtere által biztosított minden további IP-cím 64 000 gyorsítóportot biztosít, Load Balancer SNAT-portként használhatók. 

Ha standard *termékváltozatú* terheléselosztást használ felügyelt kimenő nyilvános IP-kkel, amelyek alapértelmezés szerint létrejönnek, a paraméterrel skálázhatja a felügyelt kimenő nyilvános IP-k **`load-balancer-managed-ip-count`** számát.

Egy meglévő fürt frissítéséhez futtassa a következő parancsot. Ez a paraméter a fürt létrehozásakor is beállítható több felügyelt kimenő nyilvános IP-hez.

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --load-balancer-managed-outbound-ip-count 2
```

A fenti példa *2-re* állítja a felügyelt kimenő nyilvános IP-k számát a *myResourceGroup* *myAKSCluster* fürtjéhez. 

A paraméterrel beállíthatja a felügyelt kimenő nyilvános IP-k kezdeti számát a fürt létrehozásakor, ha hozzáfűzi a paramétert, és a kívánt értékhez **`load-balancer-managed-ip-count`** **`--load-balancer-managed-outbound-ip-count`** adja meg. A felügyelt kimenő nyilvános IP-k alapértelmezett száma 1.

### <a name="provide-your-own-outbound-public-ips-or-prefixes"></a>Adjon meg saját kimenő nyilvános IP-eket vagy előtagokat

Ha standard  termékváltozatú terheléselosztást használ, az AKS-fürt alapértelmezés szerint automatikusan létrehoz egy nyilvános IP-címet az AKS által felügyelt infrastruktúra erőforráscsoportban, és hozzárendeli azt a terheléselosztás kimenő készletéhez.

Az AKS által létrehozott nyilvános IP-cím AKS által felügyelt erőforrásnak minősül. Ez azt jelenti, hogy a nyilvános IP-cím életciklusát az AKS kezeli, és nem igényel felhasználói műveletet közvetlenül a nyilvános IP-erőforráson. Másik lehetőségként saját egyéni nyilvános IP-címet vagy nyilvános IP-előtagot is hozzárendelhet a fürt létrehozásakor. Az egyéni IP-k egy meglévő fürt terheléselosztási tulajdonságain is frissíthetők.

A saját nyilvános IP-cím vagy előtag használatának követelményei:

- Az egyéni nyilvános IP-címeket a felhasználónak kell létrehoznia és birtokolni. Az AKS által létrehozott felügyelt nyilvános IP-címek nem használhatók újra saját egyéni IP-címként, mivel ez felügyeleti ütközéseket okozhat.
- Győződjön meg arról, hogy az AKS-fürtidentitás (szolgáltatásnév vagy felügyelt identitás) rendelkezik a kimenő IP-cím elérésére vonatkozó engedélyekkel. A szükséges nyilvános [IP-engedélyek listájának megfelelő listában.](kubernetes-service-principal.md#networking)
- Győződjön meg arról, hogy megfelel a kimenő [IP-címek](../virtual-network/public-ip-address-prefix.md#constraints) vagy a kimenő IP-előtagok konfiguráláshoz szükséges előfeltételeknek és korlátozásoknak.

#### <a name="update-the-cluster-with-your-own-outbound-public-ip"></a>A fürt frissítése saját kimenő nyilvános IP-címmel

Használja az [az network public-ip show parancsot][az-network-public-ip-show] a nyilvános IP-címek ip-címeinek listához.

```azurecli-interactive
az network public-ip show --resource-group myResourceGroup --name myPublicIP --query id -o tsv
```

A fenti parancs a *myResourceGroup* erőforráscsoport *myPublicIP* nyilvános IP-címének azonosítóját jeleníti meg.

Használja a `az aks update` parancsot a **`load-balancer-outbound-ips`** paraméterrel a fürt nyilvános IP-ekkel való frissítéséhez.

Az alábbi példa a paramétert használja az előző parancsból `load-balancer-outbound-ips` származó adatokat használva.

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --load-balancer-outbound-ips <publicIpId1>,<publicIpId2>
```

#### <a name="update-the-cluster-with-your-own-outbound-public-ip-prefix"></a>A fürt frissítése saját kimenő nyilvános IP-előtaggal

A standard termékváltozatú terheléselosztással nyilvános  IP-előtagokat is használhat a forgalomhoz. Az alábbi példa az [az network public-ip prefix show parancsot][az-network-public-ip-prefix-show] használja a nyilvános IP-előtagok címeinek listához:

```azurecli-interactive
az network public-ip prefix show --resource-group myResourceGroup --name myPublicIPPrefix --query id -o tsv
```

A fenti parancs a *myResourceGroup* erőforráscsoport *myPublicIPPrefix* nyilvános IP-előtagjának azonosítóját jeleníti meg.

Az alábbi példa a *load-balancer-outbound-ip-prefixes* paramétert használja az előző parancsban megadott értékekkel.

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --load-balancer-outbound-ip-prefixes <publicIpPrefixId1>,<publicIpPrefixId2>
```

#### <a name="create-the-cluster-with-your-own-public-ip-or-prefixes"></a>A fürt létrehozása saját nyilvános IP-címmel vagy előtagokkal

Előfordulhat, hogy a fürt létrehozásakor saját IP-címeket vagy IP-előtagokat szeretne használni a forgalomhoz, hogy támogassa az olyan forgatókönyveket, mint például a bejövő forgalom végpontjainak hozzáadása egy engedélyező listához. Fűzheti hozzá a fent látható paramétereket a fürt létrehozási lépéséhez a saját nyilvános IP-címek és IP-előtagok meghatározásához a fürt életciklusának elején.

Használja *az az aks create* parancsot a *load-balancer-outbound-ips* paraméterrel egy új fürt létrehozásához a kezdéskor a nyilvános IP-címekkel.

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --load-balancer-outbound-ips <publicIpId1>,<publicIpId2>
```

Az *az aks create parancsot* a *load-balancer-outbound-ip-prefixes* paraméterrel használva hozzon létre egy új fürtöt a kezdéskor a nyilvános IP-előtagokkal.

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --load-balancer-outbound-ip-prefixes <publicIpPrefixId1>,<publicIpPrefixId2>
```

### <a name="configure-the-allocated-outbound-ports"></a>A lefoglalt kimenő portok konfigurálása

> [!IMPORTANT]
> Ha olyan alkalmazásokkal rendelkezik a fürtön, amelyek várhatóan nagy számú kapcsolatot hoznak létre kis célcsoportokkal, például sok előterepéldány csatlakozik egy SQL DB-hez, van egy forgatókönyv, amely nagyon ki van téve az SNAT-portfogyásnak (elfogynak a portok, amelyekből csatlakozni lehet). Ezekben a forgatókönyvekben erősen ajánlott növelni a lefoglalt kimenő portokat és a kimenő előtere IP-eket a terheléselosztáson. A növekedésnek azt kell figyelembe vennie, hogy egy (1) további IP-cím 64 000 további portot ad hozzá az összes fürtcsomópont közötti elosztáshoz.


Ha másként nincs megadva, az AKS a Lefoglalt kimenő portok alapértelmezett értékét használja, standard Load Balancer a konfiguráláskor. Ez az érték **null az** AKS API-n vagy **0** az SLB API-n az alábbi parancsban látható módon:

```azurecli-interactive
NODE_RG=$(az aks show --resource-group myResourceGroup --name myAKSCluster --query nodeResourceGroup -o tsv)
az network lb outbound-rule list --resource-group $NODE_RG --lb-name kubernetes -o table
```

Az előző parancsok felsorolják a terheléselosztás kimenő szabályát, például:

```console
AllocatedOutboundPorts    EnableTcpReset    IdleTimeoutInMinutes    Name             Protocol    ProvisioningState    ResourceGroup
------------------------  ----------------  ----------------------  ---------------  ----------  -------------------  -------------
0                         True              30                      aksOutboundRule  All         Succeeded            MC_myResourceGroup_myAKSCluster_eastus  
```

Ez a kimenet nem azt jelenti, hogy 0 porttal [][azure-lb-outbound-preallocatedports]rendelkezik, hanem azt, hogy a háttérkészlet mérete alapján használja az automatikus kimenő port-hozzárendelést, így például ha egy fürt 50 vagy kevesebb csomóponttal rendelkezik, minden csomóponthoz 1024 port van lefoglalva, mivel a csomópontok számának növelésével fokozatosan kevesebb portot kap csomópontonként.


A lefoglalt kimenő portok számának meghatározásához vagy növeléséhez kövesse az alábbi példát:


```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --load-balancer-managed-outbound-ip-count 7 \
    --load-balancer-outbound-ports 4000
```

Ez a példa 4000 lefoglalt kimenő portot adna meg a fürt minden csomópontja számára, és 7 IP-címmel csomópontonként *4000 port lenne * 100 csomópont = 400 000 port < = 448 000 port = 7 IP * 64 000 port IP-címenként.* Ez lehetővé teszi, hogy biztonságosan skálázható legyen 100 csomópontra, és legyen egy alapértelmezett frissítési művelete. Rendkívül fontos elegendő portot lefoglalni a frissítéshez és egyéb műveletekhez szükséges további csomópontokhoz. Az AKS alapértelmezés szerint egy puffercsomópontot használ a frissítéshez, ebben a példában ehhez 4000 szabad portra van szükség egy adott időpontban. A [maxSurge](upgrade-cluster.md#customize-node-surge-upgrade)értékek használata esetén szorozza meg a csomópontonkénti kimenő portokat a maxSurge értékkel.

Ha biztonságosan 100 csomópont fölé szeretne lépni, további IP-eket kell hozzáadnia.


> [!IMPORTANT]
> A [kapcsolati vagy skálázhatósági][requirements] problémák elkerülése érdekében ki kell számítania a szükséges kvótát, és ellenőriznie kell a követelményeket a *allocatedOutboundPorts* testreszabása előtt.

A fürtök létrehozásakor a paramétereket is használhatja, de a , vagy a **`load-balancer-outbound-ports`** **`load-balancer-managed-outbound-ip-count`** **`load-balancer-outbound-ips`** **`load-balancer-outbound-ip-prefixes`** paramétert is meg kell adnia.  Például:

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --load-balancer-sku standard \
    --load-balancer-managed-outbound-ip-count 2 \
    --load-balancer-outbound-ports 1024 
```

### <a name="configure-the-load-balancer-idle-timeout"></a>A terheléselosztás üresjárati időtúllépésének konfigurálása

Ha az SNAT-port erőforrásai kimerülnek, a kimenő folyamatok meghiúsulnak, amíg a meglévő folyamatok ki nem oldják az SNAT-portokat. Load Balancer visszaigényli az SNAT-portokat, amikor a folyamat bezárul, és az AKS által konfigurált terheléseltöltő 30 perces üresjárati időkorlátot használ a SNAT-portok üresjárati folyamatokból való visszaigényléséhez.
Használhatja az átviteli folyamatot (például ) vagy egy üresjárati folyamat frissítését, és szükség esetén alaphelyzetbe állíthatja ezt az üresjárati **`TCP keepalives`** **`application-layer keepalives`** időtúllépést. Ezt az időtúllépést az alábbi példa szerint konfigurálhatja: 


```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --load-balancer-idle-timeout 4
```

Ha azt várja, hogy számos rövid élettartamú kapcsolattal rendelkezik, és nincsenek hosszú élettartamú, esetleg hosszú tétlen időszakaik, például átlagolása, vagy érdemes lehet alacsony időtúllépési értéket, például 4 percet `kubectl proxy` `kubectl port-forward` használni. TCP-tartás használata esetén is elegendő engedélyezni őket a kapcsolat egyik oldalán. Elég például engedélyezni őket a kiszolgálói oldalon, hogy a folyamat tétlen időzítője alaphelyzetbe állítva legyen, és nem szükséges, hogy mindkét oldal elindítsa a TCP-tartásokat. Hasonló fogalmak léteznek az alkalmazásrétegre, beleértve az adatbázis ügyfél-kiszolgáló konfigurációit is. Ellenőrizze a kiszolgálóoldalon, hogy milyen lehetőségek állnak rendelkezésre az alkalmazásspecifikus tartáshoz.

> [!IMPORTANT]
> Az AKS alapértelmezés szerint engedélyezi a TCP-visszaállítást tétlen állapotban, és javasolja, hogy tartsa bekapcsolva ezt a konfigurációt, és használja fel az alkalmazás kiszámíthatóbb viselkedéséhez a forgatókönyvekben.
> A TCP RST csak a TCP-kapcsolaton keresztül, ESTABLISHED állapotban van elküldve. [Itt](../load-balancer/load-balancer-tcp-reset.md) talál további információt.

### <a name="requirements-for-customizing-allocated-outbound-ports-and-idle-timeout"></a>A lefoglalt kimenő portok és az üresjárati időtúllépés testreszabásának követelményei

- A *allocatedOutboundPorts* értékének szintén a 8 többszörösének kell lennie.
- Elegendő kimenő IP-kapacitással kell rendelkezik a csomópont virtuális gépeinek száma és a szükséges lefoglalt kimenő portok alapján. A következő képlettel ellenőrizheti, hogy elegendő kimenő IP-kapacitással rendelkezik-e: 
 
*kimenő IP-k* \* 64 000 \> *nodeVM* \* *desiredAllocatedOutboundPorts*.
 
Ha például 3 *nodeVM-et* és 50 000 *desiredAllocatedOutboundPorts-ot* tartalmaz, legalább 3 kimenő *IP-vel kell,* hogy legyen. Javasoljuk, hogy a szükségesen felül további kimenő IP-kapacitást építsen be. Emellett figyelembe kell vennie az automatikus fürtméretozót, valamint a csomópontkészlet frissítésének lehetőségét a kimenő IP-kapacitás kiszámításakor. Az automatikus fürtméretozónál tekintse át az aktuális csomópontok számát és a csomópontok maximális számát, és használja a magasabb értéket. A frissítéshez minden csomópontkészlethez egy további csomóponti virtuális gépet kell figyelembe venni, amely lehetővé teszi a frissítést.

- Ha *az IdleTimeoutInMinutes* értékét az alapértelmezett 30 perctől eltérőre állítva, vegye figyelembe, hogy mennyi ideig lesz szüksége a számítási feladatoknak kimenő kapcsolatra. Vegye figyelembe az AKS-en kívül használt *standard* termékváltozatú terheléselosztás alapértelmezett időtúllépési értékét is 4 perc. Egy *IdleTimeoutInMinutes* érték, amely pontosabban tükrözi az adott AKS-számítási feladatot, segíthet csökkenteni az SNAT-kimerítést, amit az okoz, hogy a kapcsolatok már nem vannak használva.

> [!WARNING]
> A *AllocatedOutboundPorts* és *az IdleTimeoutInMinutes* értékeinek módosítása jelentősen megváltoztathatja a terheléselosztó kimenő szabályának viselkedését, és ezeket az értékeket nem szabad könnyedén tenni a problémák és az alkalmazás kapcsolati [][azure-lb-outbound-connections] mintái ismerete nélkül, tekintse át az alábbi [SNAT-hibaelhárítási][troubleshoot-snat] szakaszt, és tekintse át az [Azure Load Balancer][azure-lb-outbound-rules-overview] kimenő szabályait és kimenő kapcsolatait, mielőtt frissítené ezeket az értékeket a módosítások hatásának teljes megértéséhez.

## <a name="restrict-inbound-traffic-to-specific-ip-ranges"></a>Bejövő forgalom korlátozása adott IP-tartományokra

A következő jegyzékfájl *a loadBalancerSourceRanges erőforrást* használja egy új IP-címtartomány megadásához a bejövő külső forgalomhoz:

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
> A bejövő forgalom a terheléselosztásból az AKS-fürt virtuális hálózatára áramlik. A virtuális hálózat hálózati biztonsági csoporttal (NSG) rendelkezik, amely engedélyezi a terheléseltöltőből bejövő összes forgalmat. Ez az NSG [egy][service-tags] *LoadBalancer* típusú szolgáltatáscímkét használ a terheléseltöltőtől származó forgalom engedélyezése érdekében.

## <a name="maintain-the-clients-ip-on-inbound-connections"></a>Az ügyfél IP-címének fenntartása bejövő kapcsolatokon

Alapértelmezés szerint a `LoadBalancer` [Kubernetesben](https://kubernetes.io/docs/tutorials/services/source-ip/#source-ip-for-services-with-type-loadbalancer) és az AKS-ban található típusú szolgáltatások nem fogják megőrzni az ügyfél IP-címét a podhoz való csatlakozáson. A podnak küldött csomag forrás IP-címe a csomópont magánhálózati IP-címe lesz. Az ügyfél IP-címének fenntartásához a következőt kell beállítania a szolgáltatás `service.spec.externalTrafficPolicy` `local` definíciójában: . Az alábbi jegyzékfájl erre mutat egy példát:

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

## <a name="additional-customizations-via-kubernetes-annotations"></a>További testreszabások Kubernetes-jegyzetek segítségével

Az alábbi lista a típusú Kubernetes-szolgáltatások által támogatott jegyzeteket sorolja fel, amelyek csak az INBOUND folyamatokra `LoadBalancer` **vonatkoznak:**

| Jegyzet | Érték | Leírás
| ----------------------------------------------------------------- | ------------------------------------- | ------------------------------------------------------------ 
| `service.beta.kubernetes.io/azure-load-balancer-internal`         | `true` vagy `false`                     | Adja meg, hogy a terheléselosztás belső legyen-e. Ha nincs beállítva, alapértelmezés szerint a public (nyilvános) értéket használja.
| `service.beta.kubernetes.io/azure-load-balancer-internal-subnet`  | Az alhálózat neve                    | Adja meg, hogy a belső terheléselosztás melyik alhálózathoz legyen kötve. Ha nincs beállítva, alapértelmezés szerint a felhőalapú konfigurációs fájlban konfigurált alhálózatot használja.
| `service.beta.kubernetes.io/azure-dns-label-name`                 | A DNS-címke neve a nyilvános IP-címben   | Adja meg a NYILVÁNOS  szolgáltatás DNS-címkenevét. Ha üres sztringre van beállítva, a nyilvános IP-cím DNS-bejegyzése nem lesz használva.
| `service.beta.kubernetes.io/azure-shared-securityrule`            | `true` vagy `false`                     | Adja meg, hogy a szolgáltatást egy olyan Azure-beli biztonsági szabály használatával tegye elérhetővé, amely megosztható egy másik szolgáltatással, és a szabályok adottságával való kereskedelem érdekében növelje az elérhetővé teresedő szolgáltatások számát. Ez a jegyzet a hálózati biztonsági csoportok Azure [kibővített biztonsági](../virtual-network/network-security-groups-overview.md#augmented-security-rules) szabályok funkcióját használja. 
| `service.beta.kubernetes.io/azure-load-balancer-resource-group`   | Az erőforráscsoport neve            | Adja meg a terheléselosztási nyilvános IP-k erőforráscsoportját, amelyek nem ugyanabban az erőforráscsoportban vannak, mint a fürt infrastruktúrája (csomópont erőforráscsoportja).
| `service.beta.kubernetes.io/azure-allowed-service-tags`           | Az engedélyezett szolgáltatáscímkék listája          | Adja meg az engedélyezett [szolgáltatáscímkék listáját][service-tags] vesszővel elválasztva.
| `service.beta.kubernetes.io/azure-load-balancer-tcp-idle-timeout` | A TCP üresjárati időkorlátja percek alatt          | Adja meg a TCP-kapcsolat üresjárati időtúllépésének idejét (percben). Az alapértelmezett és a minimális érték 4. A maximális érték 30. Egész számnak kell lennie.
|`service.beta.kubernetes.io/azure-load-balancer-disable-tcp-reset` | `true`                                | Letiltás `enableTcpReset` SLB-hez


## <a name="troubleshooting-snat"></a>Az SNAT hibaelhárítása

Ha tudja, hogy sok kimenő TCP- vagy UDP-kapcsolatot kezd el ugyanerre a cél IP-címre és portra, és azt figyeli, hogy a kimenő kapcsolatok sikertelenek, vagy a támogatás arra figyelmezteti, hogy kimeríti az SNAT-portokat (a PAT által használt, előre lefoglalt, idő előtti portokat), számos általános kockázatcsökkentési lehetőség áll rendelkezésre. Tekintse át ezeket a lehetőségeket, és döntse el, mi érhető el és melyik a legmegfelelőbb az Ön forgatókönyvéhez. Lehetséges, hogy egy vagy több is segíthet ennek a forgatókönyvnek a kezelésében. Részletes információkért tekintse át a [kimenő kapcsolatok hibaelhárítási útmutatóját.](../load-balancer/troubleshoot-outbound-connection.md)

Az SNAT-kimerítés kiváltó oka gyakran a kimenő kapcsolat létrejöttének, a felügyeltnek vagy a konfigurálható időzítőknek az alapértelmezett értékekhez való módosulását gátló minta. Alaposan olvassa át ezt a szakaszt.

### <a name="steps"></a>Lépések
1. Ellenőrizze, hogy a kapcsolatok hosszú ideig tétlenek maradnak-e, és használja az alapértelmezett üresjárati időkorlátot a port felszabadításakor. Ha igen, előfordulhat, hogy az alapértelmezett 30 perces időkorlátot csökkenteni kell a forgatókönyvhöz.
2. Vizsgálja meg, hogyan hoz létre az alkalmazás kimenő kapcsolatot (például kód áttekintését vagy csomagrögzítést).
3. Állapítsa meg, hogy ez a tevékenység várt viselkedés-e, vagy az alkalmazás nem megfelelően működik. A [metrikák](../load-balancer/load-balancer-standard-diagnostics.md) és [naplók](../load-balancer/load-balancer-monitor-log.md) használatával Azure Monitor az eredményeket. Használja például a "Sikertelen" kategóriát az SNAT-kapcsolatok metrikákhoz.
4. Értékelje ki, hogy [követi-e a](#design-patterns) megfelelő mintákat.
5. Értékelje ki, hogy az SNAT-portok kimerítése további kimenő IP-címekkel és további lefoglalt kimenő portokkal [mérsékelhető-e.](#configure-the-allocated-outbound-ports)

### <a name="design-patterns"></a>Tervezési minták
Mindig használja ki a kapcsolat újrafelhasználásának és készletezésének előnyeit, amikor csak lehetséges. Ezek a minták elkerülik az erőforrás-kimerítési problémákat, és kiszámítható viselkedést eredményeznek. Ezeknek a mintáknak a primitívei számos fejlesztési kódtárban és keretrendszerben megtalálhatóak.

- Az atomi kérések (kapcsolatonként egy kérés) általában nem jó tervezési döntés. Az ilyen mintakorlátok skálázva vannak, csökkentik a teljesítményt és csökkentik a megbízhatóságot. Ehelyett használja újra a HTTP/S-kapcsolatokat a kapcsolatok és a kapcsolódó SNAT-portok számának csökkentése érdekében. A TLS használata esetén az alkalmazás mérete nő és javul a teljesítmény, mivel csökken a kézfogások, a többletterhelés és a titkosítási műveletek költsége.
- Ha a fürtön/egyéni DNS-t használja, vagy a coreDNS-hez egyéni, upstream kiszolgálókat használ, akkor a DNS számos különálló folyamat bevezetésére képes köteten, amikor az ügyfél nem gyorsítótárazást használ a DNS-feloldók eredményéhez. Először a coreDNS-t szabja testre egyéni DNS-kiszolgálók használata helyett, és határozzon meg egy jó gyorsítótárazás értéket.
- Az UDP-folyamatok (például DNS-keresések) lefoglalnak SNAT-portokat az üresjárati időkorlát időtartamára. Minél hosszabb az üresjárati időkorlát, annál nagyobb a SNAT-portokra nehezedő nyomás. Használjon rövid üresjárati időkorlátot (például 4 perc).
Használjon kapcsolatkészleteket a kapcsolatkötet formálásaként.
- Soha ne hagyjon fel egy TCP-folyamatot csendesen, és ne támaszkodjon TCP-időzítőkre a folyamat tisztításakor. Ha nem hagyja, hogy a TCP explicit módon lezárja a kapcsolatot, az állapot lefoglalva marad a köztes rendszereken és végpontokon, és elérhetetlenné teszi az SNAT-portokat más kapcsolatok számára. Ez a minta alkalmazáshibákat és SNAT-kimerítést okozhat.
- Ne módosítsa az operációs rendszerszintű TCP-lezáráshoz kapcsolódó időzítőértékeket a hatás szakértői ismerete nélkül. Bár a TCP-verem helyreáll, az alkalmazás teljesítményét hátrányosan befolyásolhatja, ha a kapcsolat végpontjainak elvárásai nem egyezőek. Az időzítők módosítása általában egy mögöttes tervezési probléma jele. Tekintse át a következő javaslatokat.


A fenti példa úgy frissíti a szabályt, hogy csak a bejövő külső forgalmat engedélyezze a *MY_EXTERNAL_IP_RANGE* tartományból. Ha lecseréli *a MY_EXTERNAL_IP_RANGE* a belső alhálózat IP-címére, a forgalom csak a fürt belső IP-címére korlátozódik. Ez nem engedélyezi a Kubernetes-fürtön kívüli ügyfelek számára a terheléselosztáshoz való hozzáférést.

## <a name="moving-from-a-basic-sku-load-balancer-to-standard-sku"></a>Áthelyezés alapszintű termékváltozatú terheléselosztásról standard termékváltozatra

Ha már van alapszintű termékváltozatú fürt Load Balancer, fontos viselkedésbeli különbségeket kell figyelembe Load Balancer.

A fürtök áttelepítésére való kék/zöld üzembe helyezés például gyakori gyakorlat, mivel a fürt típusa csak a fürt létrehozásakor `load-balancer-sku` határozható meg. Az *alapszintű termékváltozatú* terheléselosztások azonban alapszintű *termékváltozatú* IP-címeket használnak, amelyek nem kompatibilisek a *standard termékváltozatú* terheléselosztásokkal, mivel standard *termékváltozatú* IP-címeket igényelnek. A fürtök termékváltozatok Load Balancer való áttelepítésekor új IP-címre lesz szükség egy kompatibilis IP-cím-termékváltozatmal.

A fürtök áttelepítésével kapcsolatos további szempontokért tekintse meg a migrálás szempontjaival kapcsolatos dokumentációt a migrálás során megfontolandó fontos témakörök listájának megtekintéséhez. [](aks-migration.md) Az alábbi korlátozások emellett fontos viselkedésbeli különbségek, amelyek a standard termékváltozatú terheléselosztások AKS-beli használata esetén is fontosak.

## <a name="limitations"></a>Korlátozások

A standard termékváltozatú terheléselosztást támogató AKS-fürtök létrehozásakor és kezelésekor a következő korlátozások *érvényesek:*

* Az AKS-fürtből bejövő forgalom engedélyezéséhez legalább egy nyilvános IP-cím vagy IP-előtag szükséges. A nyilvános IP-cím vagy IP-előtag a vezérlősík és az ügynökcsomópontok közötti kapcsolat fenntartásához, valamint az AKS korábbi verzióival való kompatibilitás fenntartásához is szükséges. A nyilvános IP-címek vagy IP-előtagok *standard* termékváltozatú terheléselosztással való megadására a következő lehetőségek állnak rendelkezésre:
    * Adjon meg saját nyilvános IP-eket.
    * Adja meg a saját nyilvános IP-előtagját.
    * Adjon meg egy legfeljebb 100-as számot, hogy az AKS-fürt ennyi *standard* termékváltozatú nyilvános IP-t hoz létre az AKS-fürtként létrehozott erőforráscsoportban, amelynek neve általában MC_ elején.  Az AKS hozzárendeli a nyilvános IP-címet *a Standard* termékváltozatú terheléselosztáshoz. Alapértelmezés szerint egy nyilvános IP-cím automatikusan létrejön ugyanabban az erőforráscsoportban, mint az AKS-fürt, ha nincs nyilvános IP-cím, nyilvános IP-előtag vagy IP-címek száma megadva. Emellett engedélyeznie kell a nyilvános címeket, és nem kell létrehoznia Azure Policy ip-cím létrehozását tiltó házirendeket.
* Az AKS által létrehozott nyilvános IP-címek nem használhatók újra egyéni nyilvános IP-címként. Minden egyéni IP-címet a felhasználónak kell létrehoznia és kezelnie.
* A terheléselosztási termékváltozat csak AKS-fürt létrehozásakor definiálható. A terheléselosztási termékváltozat az AKS-fürt létrehozása után már nem változott.
* Egy fürtben csak egy típusú terheléselelosztási termékváltozatot használhat (Alapszintű vagy Standard).
* *Standard* A termékváltozatú terheléselosztások csak a *standard termékváltozatú* IP-címeket támogatják.


## <a name="next-steps"></a>Következő lépések

A Kubernetes-szolgáltatásokról a [Kubernetes-szolgáltatások][kubernetes-services]dokumentációjában talál további információt.

A belső forgalom bejövő forgalomhoz való Load Balancer az [AKS belső biztonsági és Load Balancer dokumentációjában talál további információt.](internal-lb.md)

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
[az-aks-show]: /cli/azure/aks#az_aks_show
[az-aks-create]: /cli/azure/aks#az_aks_create
[az-aks-get-credentials]: /cli/azure/aks#az_aks_get_credentials
[az-aks-install-cli]: /cli/azure/aks#az_aks_install_cli
[az-extension-add]: /cli/azure/extension#az_extension_add
[az-feature-list]: /cli/azure/feature#az_feature_list
[az-feature-register]: /cli/azure/feature#az_feature_register
[az-group-create]: /cli/azure/group#az_group_create
[az-provider-register]: /cli/azure/provider#az_provider_register
[az-network-lb-outbound-rule-list]: /cli/azure/network/lb/outbound-rule#az_network_lb_outbound_rule_list
[az-network-public-ip-show]: /cli/azure/network/public-ip#az_network_public_ip_show
[az-network-public-ip-prefix-show]: /cli/azure/network/public-ip/prefix#az_network_public_ip_prefix_show
[az-role-assignment-create]: /cli/azure/role/assignment#az_role_assignment_create
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
[az-extension-add]: /cli/azure/extension#az_extension_add
[az-extension-update]: /cli/azure/extension#az_extension_update
[requirements]: #requirements-for-customizing-allocated-outbound-ports-and-idle-timeout
[use-multiple-node-pools]: use-multiple-node-pools.md
[troubleshoot-snat]: #troubleshooting-snat
[service-tags]: ../virtual-network/network-security-groups-overview.md#service-tags
