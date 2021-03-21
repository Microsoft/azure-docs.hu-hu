---
title: Fogalmak – hálózatkezelés az Azure Kubernetes Servicesben (ak)
description: Ismerje meg a hálózatkezelést az Azure Kubernetes szolgáltatásban (ak), beleértve a kubenet és az Azure CNI hálózatkezelését, a bejövő vezérlőket, a terheléselosztó és a statikus IP-címeket.
ms.topic: conceptual
ms.date: 06/11/2020
ms.custom: fasttrack-edit
ms.openlocfilehash: edb195fae2e05a1f746c10482576f7e0b1bff7c9
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "88243904"
---
# <a name="network-concepts-for-applications-in-azure-kubernetes-service-aks"></a>Az Azure Kubernetes Service-ben (ak) futó alkalmazások hálózati fogalmai

Az alkalmazások fejlesztésére szolgáló, tárolón alapuló mikroprocesszorok esetében az alkalmazás-összetevőknek együtt kell működniük a feladataik feldolgozásához. A Kubernetes különböző erőforrásokat biztosít az alkalmazás kommunikációjának engedélyezéséhez. Az alkalmazások belsőleg vagy külsőleg is csatlakoztathatók és elérhetők. A magasan elérhető alkalmazások kiépítéséhez terheléselosztást használhat az alkalmazásaiban. Az összetettebb alkalmazások az SSL/TLS-lezárás vagy több összetevő útválasztása esetén is megkövetelhetik a bejövő forgalom konfigurációját. Biztonsági okokból előfordulhat, hogy korlátozni kell a hálózati forgalom áramlását a hüvelyek és a csomópontok között.

Ez a cikk bemutatja azokat az alapvető fogalmakat, amelyek az AK-ban lévő alkalmazásai hálózatkezelését biztosítják:

- [Szolgáltatások](#services)
- [Azure-beli virtuális hálózatok](#azure-virtual-networks)
- [Bemeneti vezérlők](#ingress-controllers)
- [Hálózati házirendek](#network-policies)

## <a name="kubernetes-basics"></a>A Kubernetes alapjai

Az alkalmazásokhoz való hozzáférés engedélyezéséhez, illetve az alkalmazás-összetevők közötti kommunikációhoz a Kubernetes egy absztrakt réteget biztosít a virtuális hálózat számára. A Kubernetes-csomópontok egy virtuális hálózathoz csatlakoznak, és a hüvelyek számára biztosítanak bejövő és kimenő kapcsolatokat. A *Kube-proxy* összetevő minden csomóponton fut, hogy megadja ezeket a hálózati szolgáltatásokat.

A Kubernetes-ben a *szolgáltatások* logikailag csoportosítják a hüvelyeket, amelyek lehetővé teszik a közvetlen hozzáférést egy IP-cím vagy DNS-név és egy adott porton keresztül. A forgalom *a terheléselosztó használatával is terjeszthető.* Az alkalmazások forgalmának összetettebb útválasztási lehetőségeit a *bejövő vezérlőkkel* is megvalósíthatja. A hüvelyek hálózati forgalmának biztonsága és szűrése Kubernetes *hálózati házirendekkel* lehetséges.

Az Azure platform emellett segít leegyszerűsíteni az AK-fürtök virtuális hálózatkezelését. Kubernetes terheléselosztó létrehozásakor létrejön és konfigurálva lesz az alapul szolgáló Azure Load Balancer erőforrás. Amikor hálózati portokat nyit meg a hüvelyekhez, a megfelelő Azure hálózati biztonsági csoportra vonatkozó szabályokat konfigurálja a rendszer. A HTTP-alkalmazások útválasztásához az Azure a *külső DNS-* t is konfigurálhatja, mivel az új bejövő útvonalak vannak konfigurálva.

## <a name="services"></a>Szolgáltatások

Az alkalmazások számítási feladatainak hálózati konfigurációjának leegyszerűsítése érdekében a Kubernetes a *szolgáltatásokat* használva logikailag csoportosítja a hüvelyek készletét, és hálózati kapcsolatot biztosít. A következő típusú szolgáltatások érhetők el:

- **Fürt IP** -címe – belső IP-címet hoz létre az AK-fürtön belüli használatra. Jó csak belső alkalmazásokhoz, amelyek támogatják a fürtön belüli egyéb munkaterheléseket.

    ![A fürt IP-forgalmának folyamatábrája egy AK-fürtben][aks-clusterip]

- **NodePort** – létrehoz egy port-hozzárendelést az alapul szolgáló csomóponton, amely lehetővé teszi, hogy az alkalmazás közvetlenül hozzáférhessen a csomópont IP-címével és portjával.

    ![A NodePort adatforgalmát ábrázoló diagram egy AK-fürtben][aks-nodeport]

- **Terheléselosztó** – létrehoz egy Azure Load Balancer-erőforrást, konfigurálja a külső IP-címet, és csatlakoztatja a kért hüvelyt a terheléselosztó háttér-készletéhez. Ha engedélyezni szeretné, hogy az ügyfelek forgalmát elérjék az alkalmazás, a terheléselosztási szabályok a kívánt portokon jönnek létre. 

    ![Egy AK-fürtön Load Balancer forgalom áramlását bemutató diagram][aks-loadbalancer]

    A bejövő forgalom további vezérléséhez és útválasztásához Ehelyett használjon bejövő [vezérlőt](#ingress-controllers).

- **ExternalName** – egy adott DNS-bejegyzést hoz létre az alkalmazások egyszerűbb eléréséhez.

A terheléselosztó és szolgáltatások IP-címe dinamikusan rendelhető hozzá, vagy megadhat egy meglévő statikus IP-címet is. A belső és a külső statikus IP-címek is hozzárendelhetők. Ez a létező statikus IP-cím gyakran egy DNS-bejegyzéshez van kötve.

A *belső* és *külső* terheléselosztó is létrehozható. A belső terheléselosztó csak privát IP-címet kap, ezért nem érhetők el az internetről.

## <a name="azure-virtual-networks"></a>Azure-beli virtuális hálózatok

Az AKS-ben üzembe helyezhető fürt a következő két hálózati modell egyikét használja:

- *Kubenet* hálózatkezelés – a hálózati erőforrásokat általában a rendszer az AK-fürt üzembe helyezésével hozza létre és konfigurálja.
- *Azure Container Network Interface (CNI)* hálózatkezelés – az AK-fürt csatlakozik a meglévő virtuális hálózati erőforrásokhoz és konfigurációkhoz.

### <a name="kubenet-basic-networking"></a>Kubenet (alapszintű) hálózatkezelés

Az *kubenet* hálózatkezelési beállítás az az alapértelmezett konfiguráció az AK-fürtök létrehozásához. A *kubenet* a csomópontok IP-címet kapnak az Azure virtuális hálózat alhálózatáról. A podok IP-címe a csomópontok Azure-beli virtuális hálózati alhálózatától logikailag eltérő címtérből származik. A hálózati címfordítás (NAT) konfigurálása lehetővé teszi, hogy a podok hozzáférjenek az Azure-beli virtuális hálózat erőforrásaihoz. A forgalom forrás IP-címe a NAT-t a csomópont elsődleges IP-címére irányítja.

A csomópontok a [kubenet][kubenet] Kubernetes beépülő modult használják. Lehetővé teheti, hogy az Azure platform létrehozza és konfigurálja a virtuális hálózatokat, vagy dönthet úgy, hogy egy meglévő virtuális hálózati alhálózatra telepíti az AK-fürtöt. Megint csak a csomópontok kapnak egy irányítható IP-címet, és a hüvelyek a NAT használatával kommunikálnak az AK-fürtön kívüli más erőforrásokkal. Ez a megközelítés nagy mértékben csökkenti azon IP-címek számát, amelyeket a hüvelyek számára a hálózati térben le kell foglalni.

További információ: [kubenet hálózatkezelés konfigurálása AK-fürthöz][aks-configure-kubenet-networking].

### <a name="azure-cni-advanced-networking"></a>Azure CNI (speciális) hálózatkezelés

Az Azure CNI használatával minden pod kap egy IP-címet az alhálózatból, és közvetlenül elérhetővé válik. Ezeknek az IP-címeknek egyedinek kell lenniük a hálózati térben, és előre kell tervezni. Mindegyik csomóponthoz tartozik egy konfigurációs paraméter az általa támogatott hüvelyek maximális számához. A csomópontok azonos számú IP-címet, majd az adott csomópont előtt fenntartanak. Ennek a megközelítésnek több megtervezésre van szüksége, mivel ez az IP-címek kimerüléséhez vezethet, vagy a fürtök újraépítésére van szükség egy nagyobb alhálózaton, mivel az alkalmazás által igényelt növekedés szükséges.

A kubenet-től eltérően az azonos virtuális hálózatban lévő végpontok felé irányuló adatforgalom nem a csomópont elsődleges IP-címére vonatkozik. A virtuális hálózaton belüli forgalom forrásának címe a pod IP. A virtuális hálózaton kívüli forgalom továbbra is NAT a csomópont elsődleges IP-címével.

A csomópontok az [Azure Container Network Interface (CNI)][cni-networking] Kubernetes beépülő modult használják.

![Diagram, amely két csomópontot mutat be egyetlen Azure-VNet csatlakozó hidakkal][advanced-networking-diagram]

További információ: az [Azure CNI konfigurálása az AK-fürtökhöz][aks-configure-advanced-networking].

### <a name="compare-network-models"></a>Hálózati modellek összehasonlítása

A kubenet és az Azure CNI egyaránt biztosítanak hálózati kapcsolatot az AK-fürtökhöz. Vannak azonban előnyei és hátrányai is. Magas szinten a következő szempontokat kell figyelembe venni:

* **kubenet**
    * Megőrzi az IP-címtartomány méretét.
    * A Kubernetes belső vagy külső terheléselosztó használatával éri el a hüvelyeket a fürtön kívülről.
    * A felhasználó által megadott útvonalakat (UDR) manuálisan kell kezelni és karbantartani.
    * Fürtben legfeljebb 400 csomópont adható meg.
* **Azure-CNI**
    * A hüvelyek teljes virtuális hálózati kapcsolattal rendelkeznek, és közvetlenül a saját magánhálózati IP-címükkel érhetik el a csatlakoztatott hálózatokból.
    * További IP-címtartomány szükséges.

A kubenet és az Azure CNI között a következő viselkedési különbségek léteznek:

| Képesség                                                                                   | Kubenet   | Azure-CNI |
|----------------------------------------------------------------------------------------------|-----------|-----------|
| Fürt üzembe helyezése meglévő vagy új virtuális hálózaton                                            | Támogatott – UDR manuálisan alkalmazva | Támogatott |
| Pod-Pod kapcsolat                                                                         | Támogatott | Támogatott |
| Pod-VM-kapcsolat; Virtuális gép ugyanabban a virtuális hálózatban                                          | Működik, ha a pod kezdeményezi | Mindkét módon működik |
| Pod-VM-kapcsolat; Virtuális gép a virtuális hálózatban                                            | Működik, ha a pod kezdeményezi | Mindkét módon működik |
| Helyszíni hozzáférés VPN vagy Express Route használatával                                                | Működik, ha a pod kezdeményezi | Mindkét módon működik |
| Hozzáférés a szolgáltatási végpontok által védett erőforrásokhoz                                             | Támogatott | Támogatott |
| Kubernetes-szolgáltatások közzététele terheléselosztó szolgáltatás, app Gateway vagy bejövő adatkezelő használatával | Támogatott | Támogatott |
| Alapértelmezett Azure DNS és privát zónák                                                          | Támogatott | Támogatott |

A DNS-t illetően a kubenet és az Azure CNI beépülő modul DNS-t is kínáljuk a CoreDNS, az AK-ban futó üzemelő példányok a saját autoskálázásával. A Kubernetes CoreDNS kapcsolatos további információkért lásd: a [DNS-szolgáltatás testreszabása](https://kubernetes.io/docs/tasks/administer-cluster/dns-custom-nameservers/). A CoreDNS alapértelmezés szerint úgy van konfigurálva, hogy ismeretlen tartományokat továbbítson a csomópont DNS-kiszolgálóira, vagyis az Azure Virtual Network DNS-funkcióit, amelyeken az AK-fürt telepítve van. Ezért a Azure DNS és a privát zónák az AK-ban futó hüvelyek esetében működnek.

### <a name="support-scope-between-network-models"></a>A hálózati modellek közötti hatókör támogatása

A használt hálózati modelltől függetlenül a kubenet és az Azure CNI is üzembe helyezhetők az alábbi módszerek egyikével:

* Az Azure platform képes automatikusan létrehozni és konfigurálni a virtuális hálózati erőforrásokat, amikor egy AK-fürtöt hoz létre.
* A virtuális hálózati erőforrásokat manuálisan is létrehozhatja és konfigurálhatja, és csatolhatja ezeket az erőforrásokat az AK-fürt létrehozásakor.

Bár a szolgáltatás-végpontok vagy UDR is támogatottak mind a kubenet, mind az Azure CNI esetében, az [AK támogatási szabályzatai][support-policies] határozzák meg, hogy milyen módosításokat végezhet el. Például:

* Ha egy AK-fürthöz manuálisan hozza létre a virtuális hálózati erőforrásokat, akkor a saját UDR vagy szolgáltatási végpontok konfigurálásakor is támogatott.
* Ha az Azure-platform automatikusan létrehozza az AK-fürthöz tartozó virtuális hálózati erőforrásokat, a saját UDR vagy szolgáltatási végpontok konfigurálásához nem támogatott manuálisan módosítani az AK által felügyelt erőforrásokat.

## <a name="ingress-controllers"></a>Bemeneti vezérlők

Terheléselosztó-típus szolgáltatás létrehozásakor létrejön egy mögöttes Azure Load Balancer-erőforrás. A terheléselosztó úgy van konfigurálva, hogy egy adott porton keresztül továbbítsa a forgalmat a szolgáltatásban lévő hüvelyekre. A terheléselosztó csak a 4. rétegben működik – a szolgáltatás nem ismeri a tényleges alkalmazásokat, és nem végezhet további útválasztási szempontokat.

A belépési *vezérlők* a 7. rétegben működnek, és az alkalmazások forgalmának elosztásához intelligensebb szabályokat is használhatnak. A bejövő adatkezelők közös használata a HTTP-forgalom átirányítása különböző alkalmazásokba a bejövő URL-cím alapján.

![Egy AK-fürt bejövő forgalmát bemutató diagram][aks-ingress]

Az AK-ban létrehozhat egy bejövő erőforrásokat, például NGINX-et, vagy használhatja az AK HTTP-alkalmazás útválasztási szolgáltatását. Ha egy AK-fürthöz engedélyezi a HTTP-alkalmazás útválasztását, az Azure platform létrehozza a bejövő vezérlőt és egy *külső DNS-* vezérlőt. Mivel a Kubernetes-ben új bejövő erőforrások jönnek létre, a szükséges DNS-rekordok egy fürtre vonatkozó DNS-zónában jönnek létre. További információ: a [http-alkalmazás útválasztásának telepítése][aks-http-routing].

A Application Gateway inporting Controller (AGIC) bővítmény lehetővé teszi, hogy az AK-ügyfelek kihasználják az Azure natív Application Gateway 7. szintű Load-balancert, hogy elérhetővé tegye a felhőalapú szoftvereket az interneten. A AGIC figyeli az üzemeltetett Kubernetes-fürtöt, és folyamatosan frissíti egy Application Gateway, hogy a kiválasztott szolgáltatások elérhetők legyenek az internethez. Ha többet szeretne megtudni az AK-beli AGIC-bővítményről, tekintse meg a [Mi az Application Gateway beáramló vezérlőt?][agic-overview]

A bejövő forgalom egy másik gyakori funkciója az SSL/TLS-lezárás. A HTTPS-kapcsolaton keresztül elért nagyméretű webalkalmazások esetében a TLS-megszakítást a bejövő erőforrások nem az alkalmazáson belül, hanem a bejövő erőforrással is tudják kezelni. A TLS-hitelesítés automatikus létrehozásához és konfigurálásához beállíthatja, hogy a bejövő erőforrások olyan szolgáltatók használatára legyenek használhatók, mint például a titkosítás. Az NGINX beáramlási vezérlőnek a titkosítással való konfigurálásával kapcsolatos további információkért lásd: [bejövő és TLS][aks-ingress-tls].

A bejövő adatkezelőt úgy is konfigurálhatja, hogy megőrizze az ügyfél forrásának IP-címét az AK-fürtön lévő tárolók kéréseire. Ha az ügyfél kérelmét az AK-fürtön lévő egyik tárolóhoz irányítja át a bejövő vezérlőn keresztül, a kérelem eredeti forrás IP-címe nem lesz elérhető a cél tároló számára. Ha engedélyezi az *ügyfél forrás IP-címének megőrzését*, az ügyfél forrás IP-címe a kérelem fejlécében érhető el az *X által továbbított – esetében*. Ha ügyfél-forrás IP-megőrzést használ a bejövő adatkezelőn, akkor nem használhatja a TLS átmenő átvitelt. Az ügyfél-forrás IP-megőrzés és a TLS-továbbítás más szolgáltatásokkal, például a *terheléselosztó* típussal is használható.

## <a name="network-security-groups"></a>Network security groups (Hálózati biztonsági csoportok)

A hálózati biztonsági csoport a virtuális gépek, például az AK-csomópontok forgalmát szűri. A szolgáltatások, például a terheléselosztó létrehozása során az Azure platform automatikusan konfigurálja a hálózati biztonsági csoportok szükséges szabályait. Ne konfigurálja manuálisan a hálózati biztonsági csoport szabályait a hüvelyek forgalmának szűrésére egy AK-fürtben. Adja meg a szükséges portokat és továbbítást a Kubernetes szolgáltatás jegyzékfájljának részeként, és hagyja, hogy az Azure platform létrehozza vagy frissítse a megfelelő szabályokat. Hálózati házirendeket is használhat a következő szakaszban leírtak szerint, hogy automatikusan alkalmazza a forgalmi szűrő szabályait a hüvelyekre.

## <a name="network-policies"></a>Hálózati házirendek

Alapértelmezés szerint az összes AK-fürtben lévő hüvelyek korlátozás nélkül küldhetnek és fogadhatnak forgalmat. A fokozott biztonság érdekében érdemes lehet olyan szabályokat definiálni, amelyek vezérlik a forgalom áramlását. A háttérbeli alkalmazások gyakran csak a szükséges előtér-szolgáltatásoknak vannak kitéve, vagy az adatbázis-összetevők csak a hozzájuk csatlakozó alkalmazási rétegek számára érhetők el.

A hálózati házirend egy AK-ban elérhető Kubernetes funkció, amely lehetővé teszi a hüvelyek közötti adatforgalom szabályozását. Dönthet úgy, hogy engedélyezi vagy megtagadja a forgalmat olyan beállítások alapján, mint a hozzárendelt címkék, a névtér vagy a forgalmi port. A hálózati biztonsági csoportok többek között az AK-csomópontok, nem a hüvelyek. A hálózati házirendek használata megfelelőbb, Felhőbeli natív módszer a forgalom szabályozására. Mivel a hüvelyek dinamikusan jönnek létre egy AK-fürtben, a szükséges hálózati házirendeket automatikusan alkalmazni lehet.

További információ: a [hüvelyek közötti biztonságos forgalom a hálózati házirendek használatával az Azure Kubernetes szolgáltatásban (ak)][use-network-policies].

## <a name="next-steps"></a>Következő lépések

Az AK hálózatkezelésének megkezdéséhez hozzon létre és konfiguráljon egy AK-fürtöt a saját IP-címtartományok használatával a [kubenet][aks-configure-kubenet-networking] vagy az [Azure CNI][aks-configure-advanced-networking]segítségével.

A kapcsolódó ajánlott eljárásokért lásd: [ajánlott eljárások a hálózati kapcsolathoz és a biztonsághoz az AK-ban][operator-best-practices-network].

Az alapvető Kubernetes és az AK-fogalmakkal kapcsolatos további információkért tekintse meg a következő cikkeket:

- [Kubernetes/AK-fürtök és-munkaterhelések][aks-concepts-clusters-workloads]
- [Kubernetes/AK-hozzáférés és-identitás][aks-concepts-identity]
- [Kubernetes/AK biztonság][aks-concepts-security]
- [Kubernetes/AK-tároló][aks-concepts-storage]
- [Kubernetes/AK-skála][aks-concepts-scale]

<!-- IMAGES -->
[aks-clusterip]: ./media/concepts-network/aks-clusterip.png
[aks-nodeport]: ./media/concepts-network/aks-nodeport.png
[aks-loadbalancer]: ./media/concepts-network/aks-loadbalancer.png
[advanced-networking-diagram]: ./media/concepts-network/advanced-networking-diagram.png
[aks-ingress]: ./media/concepts-network/aks-ingress.png

<!-- LINKS - External -->
[cni-networking]: https://github.com/Azure/azure-container-networking/blob/master/docs/cni.md
[kubenet]: https://kubernetes.io/docs/concepts/cluster-administration/network-plugins/#kubenet

<!-- LINKS - Internal -->
[aks-http-routing]: http-application-routing.md
[aks-ingress-tls]: ./ingress-tls.md
[aks-configure-kubenet-networking]: configure-kubenet.md
[aks-configure-advanced-networking]: configure-azure-cni.md
[aks-concepts-clusters-workloads]: concepts-clusters-workloads.md
[aks-concepts-security]: concepts-security.md
[aks-concepts-scale]: concepts-scale.md
[aks-concepts-storage]: concepts-storage.md
[aks-concepts-identity]: concepts-identity.md
[agic-overview]: ../application-gateway/ingress-controller-overview.md
[use-network-policies]: use-network-policies.md
[operator-best-practices-network]: operator-best-practices-network.md
[support-policies]: support-policies.md
