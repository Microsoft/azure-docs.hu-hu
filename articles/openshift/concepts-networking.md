---
title: Fogalmak – hálózati diagram az Azure Red Hat-hoz a OpenShift 4
description: Hálózatkezelési diagram és az Azure Red Hat OpenShift hálózatkezelésének áttekintése
author: sakthi-vetrivel
ms.author: suvetriv
ms.topic: tutorial
ms.service: azure-redhat-openshift
ms.date: 11/23/2020
ms.openlocfilehash: 5d69aacb6e3f25e3414aa446c4c5ae7852cabdfc
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2021
ms.locfileid: "101720902"
---
# <a name="network-concepts-for-azure-red-hat-openshift-aro"></a>Az Azure Red Hat OpenShift (ARO) hálózati fogalmak

Ez az útmutató áttekintést nyújt a hálózatkezelésről az Azure Red Hat-OpenShift a OpenShift 4 fürtökön, valamint egy diagramot és a fontos végpontok listáját. Az alapvető OpenShift hálózati fogalmakkal kapcsolatos további információkért tekintse meg az [Azure Red Hat OpenShift 4 hálózatkezelési dokumentációját](https://docs.openshift.com/container-platform/4.6/networking/understanding-networking.html).

![Azure Red Hat OpenShift 4 hálózati diagram](./media/concepts-networking/aro4-networking-diagram.png)

Ha a OpenShift 4 rendszeren telepíti az Azure Red Hat-ot, a teljes fürt egy virtuális hálózaton belül található. Ezen a virtuális hálózaton belül a főcsomópontok és a munkavégzők csomópontjai a saját alhálózatában élnek. Mindegyik alhálózat egy belső terheléselosztó és egy nyilvános terheléselosztó használatával működik.

## <a name="networking-components"></a>Hálózati összetevők

Az alábbi lista az Azure Red Hat OpenShift-fürt fontos hálózati összetevőit ismerteti.

* **ARO-pls**
    * Ez egy Azure Private link-végpont, amelyet a Microsoft és a Red Hat site megbízhatósági mérnökök használnak a fürt kezeléséhez.
* **ARO – belső-LB**
    * Ez a végpont kiegyensúlyozza az API-kiszolgáló felé irányuló forgalmat. A terheléselosztó esetében a fő csomópontok a háttér-készletben találhatók.
* **ARO – nyilvános – LB**
    * Ha az API nyilvános, a végpont átirányítja és kiegyensúlyozza az API-kiszolgáló felé irányuló forgalmat. Ez a végpont egy nyilvános kimenő IP-címet rendel hozzá, így a Masters hozzáférhet Azure Resource Managerhoz, és visszajelentheti a fürt állapotát.
* **ARO – belső**
    * Ez a végpont kiegyensúlyozza a belső szolgáltatási forgalmat. A terheléselosztó esetében a feldolgozó csomópontok a háttér-készletben találhatók.
    * A terheléselosztó alapértelmezés szerint nem jön létre. Ez a terheléselosztó akkor jön létre, ha a terheléselosztó típusú szolgáltatást a megfelelő megjegyzésekkel hozza létre. Például: service.beta.kubernetes.io/azure-load-balancer-internal: "true".
* **ARO – belső-LB**
    * Ez a végpont bármilyen nyilvános forgalomhoz használatos. A létrehozás és az alkalmazás és az útvonal esetében ez a bejövő forgalom elérési útja.
    * Ez a terheléselosztó a kifelé irányuló internetkapcsolatot is magában foglalja a munkavégző csomópontokon futó összes Pod Azure Load Balancer kimenő szabályokon keresztül.
        * A kimenő szabályok jelenleg nem konfigurálhatók. 1 024 TCP-portot foglalnak le az egyes csomópontokhoz.
        * A új választható disableoutboundsnat nincs beállítva az LB-szabályokban, így a hüvelyek az ebben az ALB-ben konfigurált nyilvános IP-címekként is beszerezhetők.
        * A két előző pont következményeként az ideiglenes SNAT-portok hozzáadásának egyetlen módja, ha nyilvános terheléselosztó-szolgáltatásokat ad hozzá az ARO-hoz.
* **ARO-kimenő – pip**
    * Ez a végpont a munkavégző csomópontok nyilvános IP-címeként (PIP) szolgál.
    * Ez a végpont lehetővé teszi, hogy a szolgáltatások hozzáadhatnak egy Azure Red Hat OpenShift-fürtből származó adott IP-címet egy engedélyezési listához.
* **ARO – NSG**
    * Ha egy szolgáltatást tesz elérhetővé, az API létrehoz egy szabályt ebben a hálózati biztonsági csoportban, így a forgalom áthalad a vezérlési síkon és a csomópontokon.
    * Alapértelmezés szerint ez a hálózati biztonsági csoport engedélyezi az összes kimenő forgalmat. A kimenő forgalom jelenleg csak az Azure Red Hat OpenShift-vezérlési síkra korlátozódik.
* **ARO-controlplane-NSG**
  * Ez a végpont csak a főcsomópontok 6443-as portján keresztül engedélyezi a forgalmat.
* **Azure Container Registry**
    * Ez egy tároló-beállításjegyzék, amelyet a Microsoft belsőleg biztosít és használ. Ez a beállításjegyzék írásvédett, és nem az Azure Red Hat OpenShift-felhasználók általi használatra készült.
        * Ez a beállításjegyzék a gazdagép-platform lemezképeit és a fürt összetevőit tartalmazza. Például a tárolók figyelése vagy naplózása.
        * A beállításjegyzék kapcsolatai a szolgáltatási végponton keresztül történnek (az Azure-szolgáltatások közötti belső kapcsolat).
        * Ez a belső beállításjegyzék alapértelmezés szerint nem érhető el a fürtön kívül.
* **Privát kapcsolat**
    * Engedélyezi a hálózati kapcsolatot a felügyeleti síkon a Microsoft és a Red Hat hely megbízhatósági szakemberei számára a fürt kezeléséhez.

## <a name="networking-policies"></a>Hálózati házirendek

* **Bejövő** forgalom: a bejövő hálózati házirend a [OpenShift Sdn](https://docs.openshift.com/container-platform/4.5/networking/openshift_sdn/about-openshift-sdn.html)részeként támogatott. Ez a hálózati házirend alapértelmezés szerint engedélyezve van, és a kényszerítést a felhasználók hajtják végre. Míg a bejövő hálózati házirend a v1 NetworkPolicy-kompatibilis, a kimenő és a IPBlock típusok nem támogatottak.

* **Kimenő** forgalom: a kimenő hálózati házirendeket a OpenShift-ben elérhető [kimenő tűzfal](https://docs.openshift.com/container-platform/4.5/networking/openshift_sdn/configuring-egress-firewall.html) szolgáltatással lehet támogatni. Egy névtér/projekt esetében csak egy kimenő forgalomra vonatkozó szabályzat érhető el. A kimenő forgalomra vonatkozó szabályzatok nem támogatottak az "alapértelmezett" névtérben, és a kiértékelésük sorrendben történik (az elsőtől az utolsóig).

## <a name="networking-basics-in-openshift"></a>Hálózatkezelés alapjai a OpenShift-ben

A OpenShift szoftver által definiált hálózatkezelés [(Sdn)](https://docs.openshift.com/container-platform/4.6/networking/openshift_sdn/about-openshift-sdn.html) az VSwitch [(OVS)](https://www.openvswitch.org/), a Container Network Interface (CNI) specifikáción alapuló OpenFlow implementáció használatával konfigurálja az átfedési hálózatot. Az SDN különböző beépülő modulok használatát támogatja – a hálózati házirend az Azure Red Hat-on az OpenShift 4-ben használt beépülő modul. Az SDN felügyeli az összes hálózati kommunikációt, így a virtuális hálózatokon nincs szükség további útvonalakra a pod-alapú kommunikáció eléréséhez.

## <a name="networking--for-azure-red-hat-openshift"></a>Hálózatkezelés az Azure Red Hat OpenShift

A következő hálózatkezelési funkciók az Azure Red Hat OpenShift vonatkoznak:  
* A felhasználók létrehozhatják az ARO-fürtöt egy meglévő virtuális hálózaton, vagy létrehozhatnak egy virtuális hálózatot az ARO-fürt létrehozásakor.
* A pod és a Service Network CIDRs konfigurálható.
* A csomópontok és a főkiszolgálók eltérő alhálózatokban találhatók.
* A csomópontoknak és a főkiszolgálók virtuális hálózati alhálózatának minimális/27-nek kell lennie.
* Az alapértelmezett Pod CIDR a 10.128.0.0/14.
* Az alapértelmezett szolgáltatás CIDR a 172.30.0.0/16.
* A pod és a Service Network CIDRs nem fedik át a hálózaton használt többi címtartományt, és nem lehetnek a fürt virtuális hálózati IP-címének tartományán belül.
* A pod CIDR minimális/18 méretűnek kell lennie. (A pod-hálózat nem irányítható IP-címek, és csak a OpenShift SDN-ben használható.)
* Az egyes csomópontok kiosztása/23 alhálózat (512 IP-cím) a hüvelye számára. Ez az érték nem módosítható.
* Nem csatlakoztathat Pod-t több hálózathoz.
* Nem konfigurálhatja a kimenő forgalomra vonatkozó statikus IP-címet. (Ez egy OpenShift funkció. További információ: a [kimenő IP](https://docs.openshift.com/container-platform/4.6/networking/openshift_sdn/assigning-egress-ips.html)-címek konfigurálása.

## <a name="network-settings"></a>Hálózati beállítások

Az Azure Red Hat OpenShift 4 fürtökhöz a következő hálózati beállítások érhetők el:

* **API láthatósága** – az az [ARO Create parancs](tutorial-create-cluster.md#create-the-cluster)futtatásakor az API láthatóságát állíthatja be.
    * "Nyilvános" – az API-kiszolgáló külső hálózatok számára érhető el.
    * "Private" – az API-kiszolgáló egy magánhálózati IP-címet rendelt a főhálózatból, csak csatlakoztatott hálózatokon (a fürt más alhálózatai között) érhető el. A rendszer létrehoz egy privát DNS-zónát az ügyfél nevében.
* **Behatolás láthatósága** – az az [ARO Create parancs](tutorial-create-cluster.md#create-the-cluster)futtatásakor az API láthatóságát állíthatja be.
    * A "nyilvános" útvonalak alapértelmezett értéke a nyilvános standard Load Balancer (ez módosítható).
    * A "privát" útvonalak alapértelmezett értéke a belső terheléselosztó (ez módosítható).

## <a name="network-security-groups"></a>Network security groups (Hálózati biztonsági csoportok)
A hálózati biztonsági csoportok a csomópont erőforráscsoport-csoportjában jönnek létre, amely a felhasználók számára van zárolva. A hálózati biztonsági csoportok közvetlenül az alhálózatokhoz vannak rendelve, nem a csomópont hálózati adapterén. A hálózati biztonsági csoportok nem változtathatók meg, és a felhasználók nem rendelkeznek a rájuk vonatkozó engedélyekkel.

Nyilvánosan látható API-kiszolgáló esetén nem hozhat létre hálózati biztonsági csoportokat, és hozzárendelheti azokat a hálózati adapterekhez.

## <a name="domain-forwarding"></a>Tartomány továbbítása
Az Azure Red Hat OpenShift az CoreDNS-t használja. A tartomány továbbítása konfigurálható. Saját DNS-t nem lehet a virtuális hálózatokhoz. További információ: a [DNS-továbbítás használatának](https://docs.openshift.com/container-platform/4.6/networking/dns-operator.html#nw-dns-forward_dns-operator)dokumentációja.

## <a name="whats-new-in-openshift-45"></a>Az OpenShift 4,5 újdonságai

Az OpenShift 4,5 támogatásával az Azure Red Hat OpenShift néhány jelentős építészeti változást vezetett be. Ezek a módosítások csak az OpenShift 4,5-ot futtató újonnan létrehozott fürtökre érvényesek. A OpenShift 4,5-re frissített meglévő fürtök esetén a frissítési folyamat nem módosítja a hálózati architektúrát. A felhasználóknak újra létre kell hozniuk a fürtöket az új architektúra használatához.

![Azure Red Hat OpenShift 4,5 hálózatkezelési diagram](./media/concepts-networking/aro-4-5-networking-diagram.png)

A fenti ábrán látható módon néhány módosítást láthat:
* Korábban az ARO két nyilvános LoadBalancers használt: egyet az API-kiszolgálóhoz, egyet pedig a Worker Node-készlethez. Ennek az architektúrának a frissítésével ezt egyetlen terheléselosztó keretében konszolidálták. 
* A bonyolultság csökkentése érdekében a dedikált külső IP-címek erőforrásai el lettek távolítva.
* Az ARO-vezérlő síkja mostantól ugyanazzal a hálózati biztonsági csoporttal rendelkezik, mint az ARO Worker csomópontjai.

A OpenShift 4,5-es verziójával kapcsolatos további információkért tekintse meg a [OpenShift 4,5 kibocsátási megjegyzéseit](https://docs.openshift.com/container-platform/4.5/release_notes/ocp-4-5-release-notes.html).

## <a name="next-steps"></a>Következő lépések
A kimenő forgalomról és arról, hogy az Azure Red Hat OpenShift milyen támogatást nyújt a kilépéshez, tekintse meg a [támogatási szabályzatok](support-policies-v4.md) dokumentációját.
