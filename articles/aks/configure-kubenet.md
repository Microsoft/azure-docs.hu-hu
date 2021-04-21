---
title: Kubenet-hálózat konfigurálása Azure Kubernetes Service (AKS)
description: Ismerje meg, hogyan konfigurálhatja a kubenet (alapszintű) hálózatot a Azure Kubernetes Service -ben (AKS) egy AKS-fürt meglévő virtuális hálózatban és alhálózaton való üzembe helyezéséhez.
services: container-service
ms.topic: article
ms.date: 06/02/2020
ms.reviewer: nieberts, jomore
ms.openlocfilehash: c373e45c8607f10c36f40a23c776bd081bf13207
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107789518"
---
# <a name="use-kubenet-networking-with-your-own-ip-address-ranges-in-azure-kubernetes-service-aks"></a>Kubenet-hálózat használata saját IP-címtartományokkal a Azure Kubernetes Service (AKS)

Alapértelmezés szerint az AKS-fürtök a [kubenetet][kubenet]használják, és létrejön egy Azure-beli virtuális hálózat és alhálózat. A *kubenet használatával* a csomópontok ip-címet szereznek be az Azure-beli virtuális hálózat alhálózatán. A podok IP-címe a csomópontok Azure-beli virtuális hálózati alhálózatától logikailag eltérő címtérből származik. A hálózati címfordítás (NAT) konfigurálása lehetővé teszi, hogy a podok hozzáférjenek az Azure-beli virtuális hálózat erőforrásaihoz. A forgalom forrás IP-címe a nat'd a csomópont elsődleges IP-címére. Ez a megközelítés jelentősen csökkenti a podok számára a hálózati térben lefoglalni szükséges IP-címek számát.

A [Azure Container Networking Interface (CNI)][cni-networking]minden pod kap egy IP-címet az alhálózatról, és közvetlenül elérhető. Ezeknek az IP-címeknek egyedinek kell lennie a hálózati térben, és előre meg kell tervezni őket. Minden csomópont rendelkezik egy konfigurációs paraméterrel a támogatott podok maximális számára. A csomópontonkénti azonos számú IP-címet ezután előre lefoglalja a rendszer az adott csomópont számára. Ez a megközelítés több tervezést igényel, és gyakran az IP-címek kimerüléséhez vagy a fürtök újraépítéséhez vezet egy nagyobb alhálózatban az alkalmazás igényeinek növekedésével. A csomóponton üzembe helyezhető podok maximális száma a fürt létrehozásakor vagy új csomópontkészletek létrehozásakor konfigurálható. Ha nem adja meg a maxPods értéket az új csomópontkészletek létrehozásakor, a Kubenet alapértelmezett értéke 110 lesz.

Ez a cikk bemutatja, hogyan hozhat létre és használhat virtuális hálózati alhálózatot egy AKS-fürthöz a *Kubenet-hálózat* használatával. A hálózati beállításokkal és szempontokkal kapcsolatos további információkért lásd: Hálózati fogalmak [a Kuberneteshez és az AKS-hez.][aks-network-concepts]

## <a name="prerequisites"></a>Előfeltételek

* Az AKS-fürt virtuális hálózatának engedélyeznie kell a kimenő internetkapcsolatot.
* Ne hozzon létre egynél több AKS-fürtöt ugyanabban az alhálózatban.
* Az AKS-fürtök nem használhatnak , , vagy értékeket a `169.254.0.0/16` `172.30.0.0/16` Kubernetes szolgáltatás címtartományához, podcímtartományához vagy fürt virtuális hálózati `172.31.0.0/16` `192.0.2.0/24` címtartományához.
* Az AKS-fürt által használt fürtidentitásnak legalább Hálózati közreműködő szerepkörben kell lennie a virtuális hálózat alhálózatán. [](../role-based-access-control/built-in-roles.md#network-contributor) A fürtidentitás létrehozásához és az engedélyek hozzárendeléséhez megfelelő engedélyekkel is rendelkeznie kell, például az előfizetés tulajdonosának. Ha a beépített [](../role-based-access-control/custom-roles.md) Hálózati közreműködő szerepkör használata helyett egyéni szerepkört szeretne definiálni, a következő engedélyekre van szükség:
  * `Microsoft.Network/virtualNetworks/subnets/join/action`
  * `Microsoft.Network/virtualNetworks/subnets/read`

> [!WARNING]
> A Windows Server-csomópontkészletek használatának első Azure CNI. A kubenet hálózati modellként való használata Nem érhető el a Windows Server-tárolókhoz.

## <a name="before-you-begin"></a>Előkészületek

Az Azure CLI 2.0.65-ös vagy újabb verzióját kell telepítenie és konfigurálnia. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése][install-azure-cli].

## <a name="overview-of-kubenet-networking-with-your-own-subnet"></a>A saját alhálózattal való Kubenet-hálózatépítés áttekintése

Számos környezetben definiált virtuális hálózatokat és alhálózatokat lefoglalt IP-címtartományokkal. Ezek a virtuális hálózati erőforrások több szolgáltatás és alkalmazás támogatására használhatók. A hálózati kapcsolat érdekében az AKS-fürtök *kubenet* (alapszintű hálózat) vagy Azure CNI (speciális *hálózat) használatával.*

A *kubenet esetén* csak a csomópontok kapnak IP-címet a virtuális hálózat alhálózatán. A podok nem kommunikálhatnak közvetlenül egymással. Ehelyett a felhasználó által megadott útválasztás (UDR) és az IP-továbbítás a podok csomópontok közötti kapcsolatához használatos. Alapértelmezés szerint az AKS-szolgáltatás hozza létre és tartja fenn az UDR-eket és az IP-továbbítási konfigurációt, de saját útválasztási táblázatot kell használnia az egyéni [útvonalkezeléshez.][byo-subnet-route-table] Podokat is üzembe helyezhet egy olyan szolgáltatás mögött, amely egy hozzárendelt IP-címet kap, és terheléselosztást biztosít az alkalmazás forgalmának. Az alábbi ábra bemutatja, hogy az AKS-csomópontok hogyan kapnak IP-címet a virtuális hálózat alhálózatán, de a podokat nem:

![Kubenet hálózati modell AKS-fürtgal](media/use-kubenet/kubenet-overview.png)

Az Azure legfeljebb 400 útvonalat támogat egy UDR-ben, így nem lehet 400 csomópontnál nagyobb AKS-fürt. A Kubenet [nem][virtual-nodes] támogatja az AKS virtuális csomópontokat és az Azure *hálózati szabályzatokat.*  A [Calico Hálózati szabályzatok is használhatók,][calico-network-policies]mivel a Kubenet támogatja őket.

A *Azure CNI* a minden pod kap egy IP-címet az IP-alhálózaton, és közvetlenül kommunikálhat más podokkal és szolgáltatásokkal. A fürtök mérete a megadott IP-címtartománynál is nagyobb lehet. Az IP-címtartományt azonban előre meg kell tervezni, és az összes IP-címet az AKS-csomópontok az azok által támogatott podok maximális száma alapján fogyasztják. A speciális hálózati funkciókat és forgatókönyveket, például a virtuális [csomópontokat][virtual-nodes] vagy a hálózati szabályzatokat (Azure- vagy Calico-alapú) támogatja a *Azure CNI.*

### <a name="limitations--considerations-for-kubenet"></a>A kubenet & megfontolandó korlátozások

* A Kubenet kialakításához további ugrásra van szükség, amely kisebb késést ad a podok kommunikációja számára.
* A Kubenet használata esetén útvonaltáblákra és felhasználó által megadott útvonalakra van szükség, ami összetettebbé teszi a műveleteket.
* A kubenet kialakítása miatt a közvetlen podcímzés nem támogatott a Kubenethez.
* A Azure CNI fürtöktől eltérően több Kubenet-fürt nem osztozhat egy alhálózaton.
* A **Kubenet által nem támogatott funkciók** a következők:
   * [Azure-beli hálózati szabályzatok](use-network-policies.md#create-an-aks-cluster-and-enable-network-policy), de a Calico hálózati szabályzatok támogatottak a Kubenetben
   * [Windows-csomópontkészletek](./windows-faq.md)
   * [Virtuális csomópontok bővítmény](virtual-nodes.md#network-requirements)

### <a name="ip-address-availability-and-exhaustion"></a>IP-címek rendelkezésre állása és kimerítése

A *Azure CNI* gyakori probléma, hogy a hozzárendelt IP-címtartomány túl kicsi ahhoz, hogy további csomópontokat adjon hozzá a fürtök méretezése vagy frissítése során. Előfordulhat, hogy a hálózati csapat nem tud elég nagy IP-címtartományt kiadni a várt alkalmazásigénynek megfelelően.

Biztonsági megoldásként létrehozhat egy *kubenetet* használó AKS-fürtöt, amely egy meglévő virtuális hálózati alhálózathoz csatlakozik. Ez a megközelítés lehetővé teszi, hogy a csomópontok meghatározott IP-címeket fogadjanak anélkül, hogy nagy mennyiségű IP-címet kellene előre lefoglalni a fürtben futtatható összes lehetséges pod számára.

A *kubenet használatával* sokkal kisebb IP-címtartományt használhat, és nagy fürtök és alkalmazásigényeket is képes támogatni. Például akár */27* IP-címtartomány esetén is futtathat egy 20–25 csomópontos fürtöt, amely elegendő teremben van a méretezéshez vagy a frissítéshez. Ez a fürtméret legfeljebb *2 200–2750* podot támogat (alapértelmezés szerint csomópontonként legfeljebb 110 podot). Az AKS-ben a *Kubenethez* konfigurálható podok maximális száma csomópontonként 110.

A hálózati modellek különbségét az alábbi alapszámítások hasonlítják össze:

- **kubenet** – egy egyszerű */24* IP-címtartomány akár *251* csomópontot is támogathat a fürtben (minden Azure-beli virtuális hálózat alhálózata lefoglalja az első három IP-címet a felügyeleti műveletekhez)
  - Ez a csomópontszám akár *27 610* podot is támogathat (alapértelmezés szerint csomópontonként legfeljebb 110 podot támogat a *Kubenet)*
- **Azure CNI –** ugyanaz az *alapszintű /24* alhálózati tartomány csak legfeljebb *8* csomópontot támogathat a fürtben
  - Ez a csomópontszám legfeljebb *240* podot támogat (alapértelmezetten legfeljebb 30 podot támogat csomópontonként Azure CNI *)*

> [!NOTE]
> Ezek a maximumok nem veszik figyelembe a frissítési vagy méretezési műveleteket. A gyakorlatban nem futtathatja az alhálózat IP-címtartománya által támogatott csomópontok maximális számát. A frissítési műveletek skálázható méretezése során néhány IP-címet elérhetővé kell hagynia.

### <a name="virtual-network-peering-and-expressroute-connections"></a>Virtuális hálózatok közötti társviszony-létesítés és ExpressRoute-kapcsolatok

A helyszíni kapcsolatokhoz a *Kubenet* és az *Azure-CNI* hálózati megközelítések [is használhatnak][vnet-peering] Azure-beli virtuális hálózatok közötti társviszonyt vagy [ExpressRoute-kapcsolatokat.][express-route] Gondosan tervezze meg az IP-címtartományokat, hogy megelőzze az átfedéseket és a helytelen forgalom-útválasztást. Sok helyszíni hálózat például *10.0.0.0/8* címtartományt használ, amely az ExpressRoute-kapcsolaton van meghirdetve. Javasoljuk, hogy az AKS-fürtöt a címtartományon kívüli Azure-beli virtuális hálózat alhálózataiba hozza létre, *például: 172.16.0.0/16.*

### <a name="choose-a-network-model-to-use"></a>A használni szükséges hálózati modell kiválasztása

Az AKS-fürthöz használni szükséges hálózati beépülő modul általában a rugalmasság és a speciális konfigurációs igények egyensúlya. Az alábbi szempontok segítenek felvázolni, hogy mikor lehet a legmegfelelőbb az egyes hálózati modellek.

Akkor *használja a kubenetet,* ha:

- Korlátozott IP-címtér áll rendelkezésre.
- A podok kommunikációja nagy része a fürtön belül van.
- Nincs szükség speciális AKS-funkciókra, például virtuális csomópontokra vagy Azure-hálózati szabályzatra.  Calico [hálózati szabályzatok használata.][calico-network-policies]

A *Azure CNI* használja a következőkor:

- Rendelkezésre álló IP-címtér áll rendelkezésre.
- A podok kommunikációja nagy része a fürtön kívüli erőforrásokhoz való.
- A podkapcsolathoz nem szeretné kezelni a felhasználó által megadott útvonalakat.
- Az AKS olyan speciális funkcióira van szüksége, mint a virtuális csomópontok vagy az Azure Network Policy.  Calico [hálózati szabályzatok használata.][calico-network-policies]

A használni szükséges hálózati modellel kapcsolatos további információkért lásd: Hálózati modellek és azok támogatási [hatókörének összehasonlítása.][network-comparisons]

## <a name="create-a-virtual-network-and-subnet"></a>Virtuális hálózat és alhálózat létrehozása

A *kubenet* és a saját virtuális hálózati alhálózat használatának első lépéseként hozzon létre egy erőforráscsoportot [az az group create paranccsal.][az-group-create] A következő példában létrehozunk egy *myResourceGroup* nevű erőforráscsoportot az *eastus* helyen:

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Ha nem rendelkezik meglévő virtuális hálózattal és alhálózattal, hozza létre ezeket a hálózati erőforrásokat [az az network vnet create paranccsal.][az-network-vnet-create] A következő példában a virtuális hálózat neve *myVnet,* a címelőtagja *pedig 192.168.0.0/16.* Létrejön egy *myAKSSubnet* nevű alhálózat a *192.168.1.0/24 címelőtaggal.*

```azurecli-interactive
az network vnet create \
    --resource-group myResourceGroup \
    --name myAKSVnet \
    --address-prefixes 192.168.0.0/16 \
    --subnet-name myAKSSubnet \
    --subnet-prefix 192.168.1.0/24
```

## <a name="create-a-service-principal-and-assign-permissions"></a>Szolgáltatásnév létrehozása és engedélyek hozzárendelése

Ahhoz, hogy egy AKS-fürt kommunikálhasson más Azure-erőforrásokkal, Azure Active Directory-szolgáltatásnevet kell használnia. A szolgáltatásnévnek engedéllyel kell rendelkeznie az AKS-csomópontok által használt virtuális hálózat és alhálózat kezeléséhez. Szolgáltatásnév létrehozásához használja az [az ad sp create-for-rbac parancsot:][az-ad-sp-create-for-rbac]

```azurecli-interactive
az ad sp create-for-rbac --skip-assignment
```

Az alábbi példakimenet a szolgáltatásnév alkalmazásazonosítóját és jelszavát mutatja be. Ezek az értékek további lépésekben használhatók egy szerepkör szolgáltatásnévhez való hozzárendeléséhez, majd az AKS-fürt létrehozásához:

```output
{
  "appId": "476b3636-5eda-4c0e-9751-849e70b5cfad",
  "displayName": "azure-cli-2019-01-09-22-29-24",
  "name": "http://azure-cli-2019-01-09-22-29-24",
  "password": "a1024cd7-af7b-469f-8fd7-b293ecbb174e",
  "tenant": "72f998bf-85f1-41cf-92ab-2e7cd014db46"
}
```

A fennmaradó lépésekben a megfelelő delegálások hozzárendeléséhez használja az [az network vnet show][az-network-vnet-show] és az az network [vnet subnet show][az-network-vnet-subnet-show] parancsot a szükséges erőforrás-idok lekért kiosztásához. Ezek az erőforrás-azonosítók változókként vannak tárolva, és a további lépésekben hivatkoznak rá:

```azurecli-interactive
VNET_ID=$(az network vnet show --resource-group myResourceGroup --name myAKSVnet --query id -o tsv)
SUBNET_ID=$(az network vnet subnet show --resource-group myResourceGroup --vnet-name myAKSVnet --name myAKSSubnet --query id -o tsv)
```

Most rendelje hozzá az egyszerű szolgáltatást  az AKS-fürt hálózati közreműködői engedélyéhez a virtuális hálózaton [az az role assignment create paranccsal.][az-role-assignment-create] Adja meg a sajátját az előző parancs kimenetében látható *\<appId>* módon a szolgáltatásnév létrehozásához:

```azurecli-interactive
az role assignment create --assignee <appId> --scope $VNET_ID --role "Network Contributor"
```

## <a name="create-an-aks-cluster-in-the-virtual-network"></a>AKS-fürt létrehozása a virtuális hálózaton

Ezzel létrehozott egy virtuális hálózatot és alhálózatot, valamint létrehozott és hozzárendelt engedélyeket egy szolgáltatásnév számára ezen hálózati erőforrások használatára. Most hozzon létre egy AKS-fürtöt a virtuális hálózatban és az alhálózatban [az az aks create paranccsal.][az-aks-create] Definiálja a saját és szolgáltatását az előző parancs kimenetében látható módon a *\<appId>* *\<password>* szolgáltatásnév létrehozásához.

A fürt létrehozási folyamatának részeként a következő IP-címtartományok is definiálva vannak:

* A *--service-cidr az* AKS-fürtön belüli szolgáltatások IP-címének hozzárendelésére használható. Ennek az IP-címtartománynak olyan címtartománynak kell lennie, amely nincs használatban a hálózati környezetben máshol, beleértve a helyszíni hálózati tartományokat is, ha Express Route-kapcsolatot vagy egy hely–hely VPN-kapcsolatot használó Azure-beli virtuális hálózatokat csatlakoztat vagy tervez csatlakoztatni.

* A *--dns-service-ip* címnek a szolgáltatás IP-címtartományának *.10-es* címének kell lennie.

* A *--pod-cidr-nek* olyan nagy címtérnek kell lennie, amely a hálózati környezetben máshol nincs használatban. Ebbe a tartományba beletartozik minden helyszíni hálózati tartomány, ha Express Route vagy egy hely–hely VPN-kapcsolat használatával csatlakozik az Azure-beli virtuális hálózatokhoz, vagy ha csatlakozni szeretne.
    * Ennek a címtartománynak elég nagynak kell lennie ahhoz, hogy el tudja fogadni a várhatóan felskálanó csomópontok számát. Ezt a címtartományt nem módosíthatja a fürt üzembe helyezése után, ha további címekre van szüksége további csomópontokhoz.
    * A pod IP-címtartományának segítségével */24 címtartomány rendelhető* hozzá a fürt egyes csomópontjaihoz. A következő példában a *10.244.0.0/16* *--pod-cidr* a *10.244.0.0/24* csomópontot, a második *csomópontot (10.244.1.0/24)* és a harmadik csomópontot *(10.244.2.0/24) rendeli hozzá.*
    * A fürt méretezésével vagy frissítésével az Azure platform továbbra is pod IP-címtartományt rendel minden új csomóponthoz.
    
* A *--docker-bridge-address lehetővé* teszi, hogy az AKS-csomópontok kommunikáljanak a mögöttes felügyeleti platformmal. Ez az IP-cím nem lehet a fürt virtuális hálózatának IP-címtartományában, és nem lehet átfedésben a hálózaton használt más címtartományokkal.

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-count 3 \
    --network-plugin kubenet \
    --service-cidr 10.0.0.0/16 \
    --dns-service-ip 10.0.0.10 \
    --pod-cidr 10.244.0.0/16 \
    --docker-bridge-address 172.17.0.1/16 \
    --vnet-subnet-id $SUBNET_ID \
    --service-principal <appId> \
    --client-secret <password>
```

> [!Note]
> Ha engedélyezni szeretne egy AKS-fürtöt, hogy tartalmazza a [Calico][calico-network-policies] hálózati szabályzatot, használja a következő parancsot.

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-count 3 \
    --network-plugin kubenet --network-policy calico \
    --service-cidr 10.0.0.0/16 \
    --dns-service-ip 10.0.0.10 \
    --pod-cidr 10.244.0.0/16 \
    --docker-bridge-address 172.17.0.1/16 \
    --vnet-subnet-id $SUBNET_ID \
    --service-principal <appId> \
    --client-secret <password>
```

AKS-fürt létrehozásakor a rendszer automatikusan létrehoz egy hálózati biztonsági csoportot és egy útválasztási táblázatot. Ezeket a hálózati erőforrásokat az AKS-vezérlősík kezeli. A hálózati biztonsági csoport automatikusan társítva van a csomópontokon lévő virtuális hálózati adapterekkel. Az útvonaltábla automatikusan társítva van a virtuális hálózat alhálózatával. A hálózati biztonsági csoportok szabályai és útvonaltáblái automatikusan frissülnek a szolgáltatások létrehozásakor és elérhetővétekor.

## <a name="bring-your-own-subnet-and-route-table-with-kubenet"></a>Saját alhálózat és útvonaltábla a Kubenettel

A kubenet esetén egy útvonaltáblának kell léteznie a fürt alhálózat(ában). Az AKS támogatja a saját meglévő alhálózatának és útvonaltáblájának a behozását.

Ha az egyéni alhálózat nem tartalmaz útvonaltáblát, az AKS létrehoz egyet, és szabályokat ad hozzá a fürt életciklusa során. Ha az egyéni alhálózat tartalmaz egy útvonaltáblát a fürt létrehozásakor, az AKS nyugtázza a meglévő útvonaltáblát a fürtműveletek során, és ennek megfelelően hozzáadja/frissíti a szabályokat a felhőszolgáltatói műveletekhez.

> [!WARNING]
> Egyéni szabályok hozzáadhatóak az egyéni útvonaltáblához, és frissíthetők. A Kubernetes felhőszolgáltató azonban olyan szabályokat ad hozzá, amelyeket nem szabad frissíteni vagy eltávolítani. Az olyan szabályoknak, mint a 0.0.0.0/0, mindig léteznie kell egy adott útvonaltáblában, és leképezni kell az internetes átjáró céljára, például egy NVA-ra vagy egy egyéb bejövő átjáróra. A szabályok frissítésekkor körültekintően járjon el, ha csak az egyéni szabályok módosulnak.

További információ az egyéni [útvonaltáblák beállításával kapcsolatban.][custom-route-table]

A Kubenet hálózatának használatához rendezett útválasztási táblázatszabályokra van szükség a kérések sikeres útválasztásához. Ennek a kialakításnak köszönhetően az útvonaltáblákat gondosan kell karbantartani minden olyan fürthöz, amely erre támaszkodik. Több fürt nem osztozhat az útvonaltáblán, mert a különböző fürtök pod-CIDR-je átfedésben lehet, ami váratlan és hibás útválasztást okozhat. Ha több fürtöt konfigurál ugyanazon a virtuális hálózaton, vagy virtuális hálózatot dedikál mindegyik fürtnek, ügyeljen a következő korlátozásokra.

Korlátozások:

* Az engedélyeket a fürt létrehozása előtt hozzá kell rendelni, és győződjön meg arról, hogy olyan szolgáltatásnévvel rendelkezik, amely írási engedélyekkel rendelkezik az egyéni alhálózathoz és az egyéni útvonaltáblához.
* Az AKS-fürt létrehozása előtt egy egyéni útválasztási táblázatot kell hozzárendelni az alhálózathoz.
* A társított útvonaltábla-erőforrás nem frissíthető a fürt létrehozása után. Bár az útvonaltábla erőforrása nem frissíthető, az egyéni szabályok módosíthatók az útvonaltáblán.
* Minden AKS-fürtnek egyetlen egyedi útvonaltáblát kell használnia a fürthöz társított összes alhálózathoz. Az útvonaltáblák nem használhatók újra több fürthöz, mivel lehetséges, hogy átfedésben vannak a podOK CIDR-ekkel és ütköző útválasztási szabályokkal.

Miután létrehozott egy egyéni útvonaltáblát, és hozzárendeli azt a virtuális hálózat alhálózatához, létrehozhat egy új AKS-fürtöt, amely az útvonaltáblát használja.
Az alhálózat azonosítóját kell használnia ahhoz a helyhez, ahol az AKS-fürt üzembe helyezését tervezi. Ezt az alhálózatot az egyéni útválasztási táblázathoz is társítva kell lennie.

```azurecli-interactive
# Find your subnet ID
az network vnet subnet list --resource-group
                            --vnet-name
                            [--subscription]
```

```azurecli-interactive
# Create a kubernetes cluster with with a custom subnet preconfigured with a route table
az aks create -g MyResourceGroup -n MyManagedCluster --vnet-subnet-id MySubnetID
```

## <a name="next-steps"></a>Következő lépések

Most, hogy üzembe helyezett egy AKS-fürtöt a meglévő virtuális hálózat alhálózatán, a fürtöt a szokásos módon használhatja. Az új alkalmazások Helm használatával való létrehozásának [első lépések,][develop-helm] vagy meglévő [alkalmazások üzembe helyezése a Helm használatával.][use-helm]

<!-- LINKS - External -->
[cni-networking]: https://github.com/Azure/azure-container-networking/blob/master/docs/cni.md
[kubenet]: https://kubernetes.io/docs/concepts/cluster-administration/network-plugins/#kubenet
[Calico-network-policies]: https://docs.projectcalico.org/v3.9/security/calico-network-policy

<!-- LINKS - Internal -->
[install-azure-cli]: /cli/azure/install-azure-cli
[aks-network-concepts]: concepts-network.md
[az-group-create]: /cli/azure/group#az_group_create
[az-network-vnet-create]: /cli/azure/network/vnet#az_network_vnet_create
[az-ad-sp-create-for-rbac]: /cli/azure/ad/sp#az_ad_sp_create_for_rbac
[az-network-vnet-show]: /cli/azure/network/vnet#az_network_vnet_show
[az-network-vnet-subnet-show]: /cli/azure/network/vnet/subnet#az_network_vnet_subnet_show
[az-role-assignment-create]: /cli/azure/role/assignment#az_role_assignment_create
[az-aks-create]: /cli/azure/aks#az_aks_create
[byo-subnet-route-table]: #bring-your-own-subnet-and-route-table-with-kubenet
[develop-helm]: quickstart-helm.md
[use-helm]: kubernetes-helm.md
[virtual-nodes]: virtual-nodes-cli.md
[vnet-peering]: ../virtual-network/virtual-network-peering-overview.md
[express-route]: ../expressroute/expressroute-introduction.md
[network-comparisons]: concepts-network.md#compare-network-models
[custom-route-table]: ../virtual-network/manage-route-table.md
