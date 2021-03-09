---
title: Kubenet hálózatkezelés konfigurálása az Azure Kubernetes szolgáltatásban (ak)
description: Megtudhatja, hogyan konfigurálhat kubenet (alapszintű) hálózatot az Azure Kubernetes szolgáltatásban (ak) egy AK-fürt meglévő virtuális hálózatba és alhálózatba való üzembe helyezéséhez.
services: container-service
ms.topic: article
ms.date: 06/02/2020
ms.reviewer: nieberts, jomore
ms.openlocfilehash: 639bed3dcd8f3f443b73f51efb60e7c8aeaa00a0
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/09/2021
ms.locfileid: "102504218"
---
# <a name="use-kubenet-networking-with-your-own-ip-address-ranges-in-azure-kubernetes-service-aks"></a>Kubenet hálózatkezelés használata saját IP-címtartományok az Azure Kubernetes szolgáltatásban (ak)

Alapértelmezés szerint az AK-fürtök a [kubenet][kubenet]-t használják, és létrehoznak egy Azure-beli virtuális hálózatot és egy alhálózatot. A *kubenet* a csomópontok IP-címet kapnak az Azure virtuális hálózat alhálózatáról. A podok IP-címe a csomópontok Azure-beli virtuális hálózati alhálózatától logikailag eltérő címtérből származik. A hálózati címfordítás (NAT) konfigurálása lehetővé teszi, hogy a podok hozzáférjenek az Azure-beli virtuális hálózat erőforrásaihoz. A forgalom forrás IP-címe a NAT-t a csomópont elsődleges IP-címére irányítja. Ez a megközelítés nagy mértékben csökkenti azon IP-címek számát, amelyeket a hüvelyek számára a hálózati térben le kell foglalni.

Az [Azure Container Network Interface (CNI)][cni-networking]használatával minden Pod IP-címet kap az alhálózatból, és közvetlenül elérhető. Ezeknek az IP-címeknek egyedinek kell lenniük a hálózati térben, és előre kell tervezni. Mindegyik csomóponthoz tartozik egy konfigurációs paraméter az általa támogatott hüvelyek maximális számához. A csomópontok azonos számú IP-címet, majd az adott csomópont előtt fenntartanak. Ennek a megközelítésnek több megtervezésre van szüksége, és gyakran az IP-címek kimerülését, illetve a fürtök egy nagyobb alhálózaton való újraépítését igényli, mivel az alkalmazás által igényelt növekedés. A fürtön üzembe helyezhető maximális hüvelyeket konfigurálhatja egy csomópont létrehozási ideje vagy új csomópont-készletek létrehozásakor. Ha nem ad meg maxPods az új csomópontok létrehozásakor, a rendszer a kubenet alapértelmezett 110-as értékét kapja meg.

Ebből a cikkből megtudhatja, hogyan használhatja a *kubenet* hálózatkezelést virtuális hálózati alhálózat létrehozására és használatára egy AK-fürthöz. A hálózati beállításokkal és a szempontokkal kapcsolatos további információkért lásd: [hálózati fogalmak a Kubernetes és az AK][aks-network-concepts]-hoz.

## <a name="prerequisites"></a>Előfeltételek

* Az AK-fürthöz tartozó virtuális hálózatnak engedélyeznie kell a kimenő internetkapcsolatot.
* Ne hozzon létre egynél több AK-fürtöt ugyanabban az alhálózatban.
* Az AK-fürtök nem használhatják a,, `169.254.0.0/16` `172.30.0.0/16` `172.31.0.0/16` , vagy `192.0.2.0/24` a Kubernetes szolgáltatási címtartomány, a pod címtartomány vagy a fürt virtuális hálózati címtartomány-tartományát.
* Az AK-fürt által használt fürt identitásának legalább [hálózati közreműködő](../role-based-access-control/built-in-roles.md#network-contributor) szerepkörrel kell rendelkeznie a virtuális hálózaton belüli alhálózaton. Emellett rendelkeznie kell a megfelelő engedélyekkel, például az előfizetés tulajdonosával a fürt identitásának létrehozásához és az engedélyek hozzárendeléséhez. Ha [Egyéni szerepkört](../role-based-access-control/custom-roles.md) szeretne definiálni a beépített hálózati közreműködő szerepkör használata helyett, a következő engedélyek szükségesek:
  * `Microsoft.Network/virtualNetworks/subnets/join/action`
  * `Microsoft.Network/virtualNetworks/subnets/read`

> [!WARNING]
> A Windows Server Node-készletek használatához az Azure CNI-t kell használnia. A kubenet használata hálózati modellként nem érhető el a Windows Server-tárolók esetében.

## <a name="before-you-begin"></a>Előkészületek

Szüksége lesz az Azure CLI-verzió 2.0.65 vagy újabb verziójára, és konfigurálva van. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése][install-azure-cli].

## <a name="overview-of-kubenet-networking-with-your-own-subnet"></a>A saját alhálózat kubenet hálózatkezelésének áttekintése

Számos környezetben meghatározta a lefoglalt IP-címtartományok virtuális hálózatait és alhálózatait. Ezek a virtuális hálózati erőforrások több szolgáltatás és alkalmazás támogatására szolgálnak. A hálózati kapcsolat biztosításához az AK-fürtök a *kubenet* (alapszintű Hálózatkezelés) vagy az Azure CNI (*speciális hálózatkezelés*) használatát teszik lehetővé.

A *kubenet* csak a csomópontok kapnak IP-címet a virtuális hálózat alhálózatában. A hüvelyek nem tudnak közvetlenül kommunikálni egymással. Ehelyett a rendszer a felhasználó által megadott útválasztást (UDR) és az IP-továbbítást használja a hüvelyek közötti kapcsolathoz a csomópontok között. Alapértelmezés szerint a UDR és az IP-továbbítási konfigurációt az AK szolgáltatás hozza létre és tartja karban, de a [saját útválasztási táblázatot is használhatja az egyéni útvonalak kezeléséhez][byo-subnet-route-table]. A hüvelyt olyan szolgáltatás mögött is üzembe helyezheti, amely egy hozzárendelt IP-címet kap, és az alkalmazás számára az adatforgalom terheléselosztását végzi. Az alábbi ábra azt mutatja, hogy az AK-csomópontok Hogyan kapják meg az IP-címet a virtuális hálózati alhálózatban, a hüvelyek kivételével:

![Kubenet hálózati modell egy AK-fürttel](media/use-kubenet/kubenet-overview.png)

Az Azure legfeljebb 400 útvonalat támogat egy UDR, így nem rendelkezhet 400 csomópontnál nagyobb AK-fürttel. Az AK-beli [virtuális csomópontok][virtual-nodes] és az Azure-hálózati házirendek nem támogatottak a *kubenet*.  A kubenet által támogatott, a [tarka hálózati házirendeket][calico-network-policies]is használhatja.

Az *Azure CNI* minden Pod IP-címet kap az IP-alhálózatban, és közvetlenül tud kommunikálni más hüvelyekkel és szolgáltatásokkal. A fürtök mérete lehet a megadott IP-címtartomány. Az IP-címtartományt azonban előre meg kell tervezni, és az összes IP-címet az AK-csomópontok használják az általa támogatott hüvelyek maximális száma alapján. Az *Azure CNI* támogatja a speciális hálózati szolgáltatásokat és forgatókönyveket, például a [virtuális csomópontokat][virtual-nodes] vagy a hálózati házirendeket (akár az Azure-t, akár a tarkat is).

### <a name="limitations--considerations-for-kubenet"></a>Korlátozások & megfontolások a kubenet

* További ugrásra van szükség a kubenet kialakításában, amely kisebb késést biztosít a pod-kommunikációhoz.
* A kubenet használatához útválasztási táblák és felhasználó által definiált útvonalak szükségesek, ami bonyolultságot biztosít a műveletekhez.
* A közvetlen Pod-címzés nem támogatott a kubenet-kialakítás miatti kubenet.
* Az Azure CNI-fürtökkel ellentétben több kubenet-fürt nem tud alhálózatot megosztani.
* **A kubenet-ben nem támogatott szolgáltatások a** következők:
   * Az [Azure hálózati házirendjei](use-network-policies.md#create-an-aks-cluster-and-enable-network-policy), de a kubenet támogatottak a tarka hálózati házirendek
   * [Windows-csomópontos készletek](./windows-faq.md)
   * [Virtuális csomópontok bővítmény](virtual-nodes.md#network-requirements)

### <a name="ip-address-availability-and-exhaustion"></a>IP-cím rendelkezésre állása és kimerülése

Az *Azure CNI* gyakori probléma, hogy a HOZZÁRENDELT IP-címtartomány túl kicsi ahhoz, hogy a fürt méretezése vagy frissítése során további csomópontokat vegyen fel. Előfordulhat, hogy a hálózati csapat nem tud kiadni elég nagy méretű IP-címtartományt a várt alkalmazási igények kielégítéséhez.

Kompromisszumként létrehozhat egy *kubenet* -t használó AK-fürtöt, és csatlakozhat egy meglévő virtuális hálózat alhálózatához. Ez a megközelítés lehetővé teszi, hogy a csomópontok meghatározott IP-címeket kapjanak, anélkül, hogy a fürtben futtatott összes lehetséges hüvelyhez le kellene foglalni a nagy számú IP-címet.

A *kubenet* segítségével sokkal kisebb IP-címtartományt használhat, és képes támogatni a nagyméretű fürtöket és az alkalmazások igényeit. Ha például egy */27* IP-címtartomány van az alhálózaton, akkor futtathat egy 20-25 csomópont-fürtöt, amely elegendő mozgásteret biztosít a méretezéshez vagy a frissítéshez. Ez a fürt mérete akár *2200-2750* hüvelyt is támogat (az alapértelmezett maximális 110 hüvely/csomópont). A *kubenet* -ben konfigurálható hüvelyek maximális száma az AK-ban 110.

A következő alapvető számítások összehasonlítják a hálózati modellek különbségét:

- **kubenet** – egy egyszerű */24* IP-címtartomány legfeljebb *251* csomópontot tud támogatni a fürtben (mindegyik Azure-beli virtuális hálózati alhálózat fenntartja az első három IP-címet a felügyeleti műveletekhez)
  - A csomópontok száma akár *27 610* hüvelyt is támogathat (a *kubenet* alapértelmezett maximális 110 hüvelye)
- **Azure CNI** – ugyanaz az alapszintű */24* alhálózati tartomány legfeljebb *8* csomópontot támogat a fürtben
  - Ez a csomópontok száma legfeljebb *240* hüvely használatát támogatja (az *Azure CNI* alapértelmezés szerint legfeljebb 30 hüvelyt tartalmazhat)

> [!NOTE]
> A maximális érték nem veszi figyelembe a frissítési és a skálázási műveleteket. A gyakorlatban nem futtathatja az alhálózat IP-címtartomány által támogatott csomópontok maximális számát. A frissítési műveletek skálázása során bizonyos IP-címeket el kell hagyni a használatra.

### <a name="virtual-network-peering-and-expressroute-connections"></a>Virtuális hálózati és ExpressRoute kapcsolatok

A helyszíni kapcsolat biztosításához mind a *kubenet* , mind az *Azure-CNI* hálózati megközelítések használhatják az [Azure Virtual Network-peering][vnet-peering] vagy a [ExpressRoute-kapcsolatokat][express-route]. Körültekintően tervezze meg az IP-címtartományt, hogy elkerülje az átfedést és a helytelen forgalmi útválasztást. Például számos helyszíni hálózat olyan *10.0.0.0/8* címtartományt használ, amelyet a ExpressRoute-kapcsolaton keresztül hirdettek meg. Azt javasoljuk, hogy hozzon létre egy AK-fürtöt az Azure-beli virtuális hálózati alhálózatokban ezen a címtartományból kívül, például *172.16.0.0/16*.

### <a name="choose-a-network-model-to-use"></a>Válassza ki a használni kívánt hálózati modellt

Az AK-fürthöz használandó hálózati beépülő modul kiválasztása általában a rugalmasság és a speciális konfigurációs igények közötti egyensúly. Az alábbi szempontok segítenek felvázolni, hogy az egyes hálózati modellek a legmegfelelőbbek legyenek.

*Kubenet* használata:

- Korlátozott IP-címtartomány áll rendelkezésre.
- A pod-kommunikáció nagy része a fürtön belül van.
- Nincs szüksége olyan speciális AK-funkciókra, mint a virtuális csomópontok vagy az Azure hálózati házirendje.  A [tarka hálózati házirendek][calico-network-policies]használata.

Az *Azure CNI* használata:

- Rendelkezésre áll az IP-címtartomány.
- A pod-kommunikáció nagy része a fürtön kívüli erőforrások.
- Nem szeretné kezelni a felhasználó által megadott útvonalakat a pod-kapcsolathoz.
- Szükség van egy olyan speciális szolgáltatásra, mint a virtuális csomópontok vagy az Azure hálózati házirendje.  A [tarka hálózati házirendek][calico-network-policies]használata.

További információ a használni kívánt hálózati modell eldöntéséhez: [hálózati modellek összehasonlítása és a támogatási hatókörük][network-comparisons].

## <a name="create-a-virtual-network-and-subnet"></a>Virtuális hálózat és alhálózat létrehozása

A *kubenet* és a saját virtuális hálózati alhálózat használatának megkezdéséhez először hozzon létre egy erőforráscsoportot az az [Group Create][az-group-create] paranccsal. A következő példában létrehozunk egy *myResourceGroup* nevű erőforráscsoportot a *eastus* helyen:

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Ha nem rendelkezik meglévő virtuális hálózattal és alhálózattal, hozza létre ezeket a hálózati erőforrásokat az az [Network vnet Create][az-network-vnet-create] parancs használatával. A következő példában a virtuális hálózat neve *myVnet* , a *192.168.0.0/16* előtaggal. A rendszer létrehoz egy *myAKSSubnet* nevű alhálózatot a *192.168.1.0/24* előtaggal.

```azurecli-interactive
az network vnet create \
    --resource-group myResourceGroup \
    --name myAKSVnet \
    --address-prefixes 192.168.0.0/16 \
    --subnet-name myAKSSubnet \
    --subnet-prefix 192.168.1.0/24
```

## <a name="create-a-service-principal-and-assign-permissions"></a>Egyszerű szolgáltatásnév létrehozása és engedélyek kiosztása

Ahhoz, hogy egy AKS-fürt kommunikálhasson más Azure-erőforrásokkal, Azure Active Directory-szolgáltatásnevet kell használnia. Az egyszerű szolgáltatásnak rendelkeznie kell engedéllyel az AK-csomópontok által használt virtuális hálózat és alhálózat kezeléséhez. Egyszerű szolgáltatásnév létrehozásához használja az az [ad SP Create-for-RBAC][az-ad-sp-create-for-rbac] parancsot:

```azurecli-interactive
az ad sp create-for-rbac --skip-assignment
```

A következő példa kimenetében a szolgáltatáshoz tartozó alkalmazás azonosítója és jelszava látható. Ezek az értékek a szerepkör a szolgáltatáshoz való hozzárendelésének további lépésein, majd az AK-fürt létrehozásán alapulnak:

```azurecli
az ad sp create-for-rbac --skip-assignment
```

```output
{
  "appId": "476b3636-5eda-4c0e-9751-849e70b5cfad",
  "displayName": "azure-cli-2019-01-09-22-29-24",
  "name": "http://azure-cli-2019-01-09-22-29-24",
  "password": "a1024cd7-af7b-469f-8fd7-b293ecbb174e",
  "tenant": "72f998bf-85f1-41cf-92ab-2e7cd014db46"
}
```

A többi lépésben a megfelelő delegálások hozzárendeléséhez használja az az [Network vnet show][az-network-vnet-show] és [az az Network vnet subnet show][az-network-vnet-subnet-show] parancsot a szükséges erőforrás-azonosítók lekéréséhez. Ezek az erőforrás-azonosítók változóként vannak tárolva, és a fennmaradó lépések során hivatkoznak rá:

```azurecli-interactive
VNET_ID=$(az network vnet show --resource-group myResourceGroup --name myAKSVnet --query id -o tsv)
SUBNET_ID=$(az network vnet subnet show --resource-group myResourceGroup --vnet-name myAKSVnet --name myAKSSubnet --query id -o tsv)
```

Most rendeljen hozzá egy egyszerű szolgáltatásnevet az AK-fürt *hálózati közreműködői* számára a virtuális hálózaton az az [role hozzárendelés Create][az-role-assignment-create] paranccsal. Adja meg a sajátját az *\<appId>* előző parancs kimenetében látható módon az egyszerű szolgáltatás létrehozásához:

```azurecli-interactive
az role assignment create --assignee <appId> --scope $VNET_ID --role "Network Contributor"
```

## <a name="create-an-aks-cluster-in-the-virtual-network"></a>AK-fürt létrehozása a virtuális hálózaton

Ezzel létrehozta a virtuális hálózatot és az alhálózatot, és létrehozta és hozzárendelte az egyszerű szolgáltatásnév számára a hálózati erőforrások használatára vonatkozó engedélyeket. Most hozzon létre egy AK-fürtöt a virtuális hálózaton és az alhálózatban az az [AK Create][az-aks-create] paranccsal. Adja meg a saját szolgáltatásnevet, *\<appId>* és az *\<password>* előző parancs kimenetében látható módon hozza létre az egyszerű szolgáltatást.

A következő IP-címtartományok a fürt létrehozási folyamatának részeként is definiálva vannak:

* A *--Service-CIDR* egy IP-cím használatával rendeli hozzá a belső szolgáltatásokat az AK-fürthöz. Ez az IP-címtartomány olyan címterület, amely nem használatos a hálózati környezetében, beleértve a helyszíni hálózati tartományokat is, ha a csatlakozáshoz vagy a csatlakozás megtervezéséhez az Azure Virtual Networks Express Route vagy helyek közötti VPN-kapcsolatot használ.

* A *--DNS-Service-IP* címnek a szolgáltatás IP- *.10* tartozó címnek kell lennie.

* A *--Pod-CIDR* nagyméretű címtartománynek kell lennie, amely nincs használatban a hálózati környezetében máshol. Ez a tartomány bármely helyszíni hálózati tartományt magában foglal, ha csatlakoztatja vagy megtervezi, hogy az Azure-beli virtuális hálózatok Express Route vagy helyek közötti VPN-kapcsolaton keresztül csatlakoznak-e.
    * Ennek a címtartomány-tartománynak elég nagynak kell lennie ahhoz, hogy megfeleljen a felskálázásra várt csomópontok számának. Ez a címtartomány nem módosítható a fürt üzembe helyezésekor, ha további csomópontokra van szüksége.
    * A pod IP-címtartomány egy */24* címterület hozzárendelésére szolgál a fürt mindegyik csomópontján. A következő példában a *--Pod-CIDR* of *10.244.0.0/16* az első csomópontot ( *10.244.0.0/24*), a második csomópontot ( *10.244.1.0/24*) és a harmadik, *10.244.2.0/24* csomópontot rendeli hozzá.
    * A fürt skálázása vagy frissítése során az Azure platform továbbra is egy Pod IP-címtartományt rendel minden új csomóponthoz.
    
* A *--Docker-Bridge-címe* lehetővé teszi, hogy az AK-csomópontok kommunikálnak az alapul szolgáló felügyeleti platformmal. Ez az IP-cím nem lehet a fürt virtuális hálózati IP-címének tartományán belül, és nem fedi át a hálózaton használt többi címtartományt.

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
> Ha engedélyezni szeretné, hogy egy AK-fürt tartalmazzon egy [kartonos hálózati házirendet][calico-network-policies] , a következő parancsot használhatja.

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

Ha AK-fürtöt hoz létre, a rendszer automatikusan létrehoz egy hálózati biztonsági csoportot és egy útválasztási táblázatot. Ezeket a hálózati erőforrásokat az AK vezérlő síkja kezeli. A hálózati biztonsági csoport automatikusan a csomópontokon lévő virtuális hálózati adapterekhez van társítva. Az útválasztási táblázat automatikusan a virtuális hálózat alhálózatához van társítva. A hálózati biztonsági csoport szabályai és útválasztási táblái automatikusan frissülnek a szolgáltatások létrehozásakor és közzétételekor.

## <a name="bring-your-own-subnet-and-route-table-with-kubenet"></a>Saját alhálózat és útválasztási táblázat használata a kubenet

A kubenet esetében az útválasztási táblázatnak léteznie kell a fürt alhálózatán (k). Az AK támogatja a saját meglévő alhálózat és útválasztási táblázat bevezetését.

Ha az egyéni alhálózat nem tartalmaz útválasztási táblázatot, az AK létrehozza az egyiket az Ön számára, és a fürt életciklusa során a szabályok hozzáadására is lehetőséget nyújt. Ha az egyéni alhálózat útválasztási táblázatot tartalmaz a fürt létrehozásakor, az AK felismeri a meglévő útválasztási táblázatot a fürt műveletei során, és ennek megfelelően hozzáadja/frissíti a szabályokat a felhőalapú szolgáltató műveleteihez.

> [!WARNING]
> Az egyéni szabályok hozzáadhatók az egyéni útválasztási táblázathoz és frissíthetők. A szabályokat azonban a Kubernetes Cloud Provider adja hozzá, amely nem frissíthető és nem távolítható el. A 0.0.0.0/0 szabályoknak mindig léteznie kell egy adott útválasztási táblában, és le kell képeznie az internetes átjáró céljára, például egy NVA vagy más kimenő átjáróra. Körültekintően járjon el, amikor csak az egyéni szabályok módosítására kerülő szabályokat frissíti.

További információ az [Egyéni útválasztási táblázat][custom-route-table]beállításáról.

A Kubenet hálózatkezeléséhez a kérések sikeres továbbításához meg kell adni a szervezett útválasztási táblázat szabályait. Ennek a kialakításnak köszönhetően az útválasztási táblákat gondosan karban kell tartani minden olyan fürt esetében, amely arra támaszkodik. Több fürt nem oszthat meg útválasztási táblázatot, mert a különböző fürtökből származó Pod CIDRs átfedésbe kerülhet, ami váratlan és hibás útválasztást okoz. Ha több fürtöt állít be ugyanazon a virtuális hálózaton, vagy egy virtuális hálózatot az egyes fürtökhöz kíván hozzárendelni, ügyeljen rá, hogy a következő korlátozások legyenek figyelembe véve.

Korlátozások:

* Az engedélyeket hozzá kell rendelni a fürt létrehozása előtt, ügyeljen arra, hogy az egyéni alhálózathoz és az egyéni útválasztási táblázathoz írási engedéllyel rendelkező egyszerű szolgáltatást használjon.
* Az AK-fürt létrehozása előtt egyéni útválasztási táblázatot kell társítani az alhálózathoz.
* A társított útválasztási tábla erőforrása nem frissíthető a fürt létrehozása után. Amíg az útválasztási tábla erőforrása nem frissíthető, egyéni szabályok módosíthatók az útválasztási táblázatban.
* Mindegyik AK-fürtnek egyetlen, egyedi útválasztási táblázatot kell használnia a fürthöz társított összes alhálózathoz. Nem lehet felhasználni több fürtből álló útválasztási táblázatot, mert lehetséges az átfedésben lévő Pod CIDRs és az ütköző útválasztási szabályok.

Miután létrehozta az egyéni útválasztási táblázatot, és hozzárendeli azt a virtuális hálózatban lévő alhálózathoz, létrehozhat egy új, az útválasztási táblázatot használó munkaállomás-fürtöt.
Az alhálózati azonosítót kell használnia az AK-fürt üzembe helyezéséhez. Ezt az alhálózatot is társítani kell az egyéni útválasztási táblázathoz.

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

A meglévő virtuális hálózati alhálózatba üzembe helyezett AK-fürttel mostantól a fürtöt normál módon is használhatja. Ismerkedjen meg az [új alkalmazások a Helm használatával történő létrehozásával][develop-helm] vagy [meglévő alkalmazások a Helm használatával történő üzembe helyezésével][use-helm].

<!-- LINKS - External -->
[cni-networking]: https://github.com/Azure/azure-container-networking/blob/master/docs/cni.md
[kubenet]: https://kubernetes.io/docs/concepts/cluster-administration/network-plugins/#kubenet
[Calico-network-policies]: https://docs.projectcalico.org/v3.9/security/calico-network-policy

<!-- LINKS - Internal -->
[install-azure-cli]: /cli/azure/install-azure-cli
[aks-network-concepts]: concepts-network.md
[az-group-create]: /cli/azure/group#az-group-create
[az-network-vnet-create]: /cli/azure/network/vnet#az-network-vnet-create
[az-ad-sp-create-for-rbac]: /cli/azure/ad/sp#az-ad-sp-create-for-rbac
[az-network-vnet-show]: /cli/azure/network/vnet#az-network-vnet-show
[az-network-vnet-subnet-show]: /cli/azure/network/vnet/subnet#az-network-vnet-subnet-show
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[az-aks-create]: /cli/azure/aks#az-aks-create
[byo-subnet-route-table]: #bring-your-own-subnet-and-route-table-with-kubenet
[develop-helm]: quickstart-helm.md
[use-helm]: kubernetes-helm.md
[virtual-nodes]: virtual-nodes-cli.md
[vnet-peering]: ../virtual-network/virtual-network-peering-overview.md
[express-route]: ../expressroute/expressroute-introduction.md
[network-comparisons]: concepts-network.md#compare-network-models
[custom-route-table]: ../virtual-network/manage-route-table.md
