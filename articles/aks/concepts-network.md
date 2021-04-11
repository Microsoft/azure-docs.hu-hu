---
title: Fogalmak – hálózatkezelés az Azure Kubernetes Servicesben (ak)
description: Ismerje meg a hálózatkezelést az Azure Kubernetes szolgáltatásban (ak), beleértve a kubenet és az Azure CNI hálózatkezelését, a bejövő vezérlőket, a terheléselosztó és a statikus IP-címeket.
ms.topic: conceptual
ms.date: 03/11/2021
ms.custom: fasttrack-edit
ms.openlocfilehash: 56c98163434fbe2d29cf49bf750d6f7d1cfe0d2b
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/08/2021
ms.locfileid: "107105340"
---
# <a name="network-concepts-for-applications-in-azure-kubernetes-service-aks"></a>Az Azure Kubernetes Service-ben (ak) futó alkalmazások hálózati fogalmai

Az alkalmazások fejlesztésére szolgáló, tároló-alapú, mikroprocesszorok által az alkalmazás-összetevők együttműködve dolgozzák fel a feladataikat. A Kubernetes számos olyan erőforrást biztosít, amelyek lehetővé teszik ezt az együttműködést:
* Az alkalmazások belsőleg vagy külsőleg is csatlakoztathatók és elérhetők. 
* Az alkalmazások terheléselosztásával kiválóan elérhető alkalmazásokat hozhat létre. 
* Összetettebb alkalmazásai esetében az SSL/TLS-lezárás vagy több összetevő útválasztása esetében is konfigurálhatja a bejövő forgalmat. 
* Biztonsági okokból korlátozhatja a hálózati forgalom áramlását a hüvelyek és a csomópontok között.

Ez a cikk bemutatja azokat az alapvető fogalmakat, amelyek az AK-ban lévő alkalmazásai hálózatkezelését biztosítják:

- [Szolgáltatások](#services)
- [Azure-beli virtuális hálózatok](#azure-virtual-networks)
- [Bemeneti vezérlők](#ingress-controllers)
- [Hálózati házirendek](#network-policies)

## <a name="kubernetes-basics"></a>A Kubernetes alapjai

Az alkalmazásokhoz való hozzáférés engedélyezéséhez vagy az alkalmazás-összetevők között a Kubernetes egy absztrakt réteget biztosít a virtuális hálózat számára. A Kubernetes-csomópontok virtuális hálózathoz csatlakoznak, és a hüvelyekhez bejövő és kimenő kapcsolatot biztosítanak. A *Kube-proxy* összetevő minden csomóponton fut, hogy megadja ezeket a hálózati szolgáltatásokat.

A Kubernetes-ben:
* A *szolgáltatások* logikailag csoportosítják a hüvelyeket, amelyek lehetővé teszik a közvetlen hozzáférést egy adott porton egy IP-cím vagy a DNS-név használatával. 
* A forgalom *a terheléselosztó használatával terjeszthető.* 
* Az alkalmazások forgalmának összetettebb útválasztási lehetőségeit a *bejövő vezérlőkkel* is megvalósíthatja. 
* A hüvelyek hálózati forgalmának biztonsága és szűrése Kubernetes *hálózati házirendekkel* lehetséges.

Az Azure platform emellett leegyszerűsíti az AK-fürtök virtuális hálózatkezelését. Kubernetes terheléselosztó létrehozásakor az alapul szolgáló Azure Load Balancer erőforrás is létrehozható és konfigurálható. Amikor hálózati portokat nyit meg a hüvelyekhez, a megfelelő Azure hálózati biztonsági csoportra vonatkozó szabályokat konfigurálja a rendszer. A HTTP-alkalmazások útválasztásához az Azure a *külső DNS-* t is konfigurálhatja, mivel az új bejövő útvonalak vannak konfigurálva.

## <a name="services"></a>Szolgáltatások

Az alkalmazások számítási feladatainak hálózati konfigurációjának leegyszerűsítése érdekében a Kubernetes a *szolgáltatásokat* használva logikailag csoportosítja a hüvelyek készletét, és hálózati kapcsolatot biztosít. A következő típusú szolgáltatások érhetők el:

- **Fürt IP-címe** 
  
  Létrehoz egy belső IP-címet az AK-fürtön belüli használatra. Jó csak belső alkalmazásokhoz, amelyek támogatják a fürtön belüli egyéb munkaterheléseket.

    ![A fürt IP-forgalmának folyamatábrája egy AK-fürtben][aks-clusterip]

- **NodePort** 

  Létrehoz egy port-hozzárendelést az alapul szolgáló csomóponton, amely lehetővé teszi, hogy az alkalmazás közvetlenül hozzáférhessen a csomópont IP-címével és portjával.

    ![A NodePort adatforgalmát ábrázoló diagram egy AK-fürtben][aks-nodeport]

- **LoadBalancer** 

  Létrehoz egy Azure Load Balancer-erőforrást, konfigurál egy külső IP-címet, és csatlakoztatja a kért hüvelyt a terheléselosztó háttér-készletéhez. Ha engedélyezni szeretné, hogy az ügyfelek forgalmát elérjék az alkalmazás, a terheléselosztási szabályok a kívánt portokon jönnek létre. 

    ![Egy AK-fürtön Load Balancer forgalom áramlását bemutató diagram][aks-loadbalancer]

    A bejövő forgalom további vezérléséhez és útválasztásához Ehelyett használjon egy bejövő [vezérlőt](#ingress-controllers).

- **ExternalName** 

  Egy adott DNS-bejegyzést hoz létre az alkalmazások könnyebb eléréséhez.

A terheléselosztó és a szolgáltatások IP-címe dinamikusan kiosztható, vagy megadhat egy meglévő statikus IP-címet is. A belső és a külső statikus IP-címeket is hozzárendelheti. A meglévő statikus IP-címek gyakran egy DNS-bejegyzéshez vannak kötve.

*Belső* és *külső* terheléselosztó is létrehozható. A belső terheléselosztó csak privát IP-címet kap, ezért nem érhetők el az internetről.

## <a name="azure-virtual-networks"></a>Azure-beli virtuális hálózatok

Az AKS-ben üzembe helyezhető fürt a következő két hálózati modell egyikét használja:

- *Kubenet* hálózatkezelés

  A hálózati erőforrásokat általában a rendszer az AK-fürt üzembe helyezésével hozza létre és konfigurálja.

- *Azure Container Network Interface (CNI)* hálózatkezelés
 
  Az AKS-fürt csatlakozik a meglévő virtuálishálózat-erőforrásokhoz és -konfigurációkhoz.

### <a name="kubenet-basic-networking"></a>Kubenet (alapszintű) hálózatkezelés

Az *kubenet* hálózatkezelési beállítás az az alapértelmezett konfiguráció az AK-fürtök létrehozásához. A *kubenet*:
1. A csomópontok IP-címet kapnak az Azure virtuális hálózat alhálózatáról. 
1. A hüvelyek olyan IP-címet kapnak, amely logikailag eltér a csomópontok Azure-beli virtuális hálózati alhálózatán. 
1. A hálózati címfordítás (NAT) konfigurálása lehetővé teszi, hogy a podok hozzáférjenek az Azure-beli virtuális hálózat erőforrásaihoz. 
1. A forgalom forrás IP-címe a csomópont elsődleges IP-címére van lefordítva.

A csomópontok a [kubenet][kubenet] Kubernetes beépülő modult használják. A következőket teheti:
* Lehetővé teszi, hogy az Azure platform létrehozza és konfigurálja a virtuális hálózatokat, vagy 
* Válassza az AK-fürt üzembe helyezését egy meglévő virtuális hálózati alhálózatra. 

Ne feledje, hogy csak a csomópontok kapnak egy irányítható IP-címet. A hüvelyek NAT használatával kommunikálnak az AK-fürtön kívüli egyéb erőforrásokkal. Ezzel a módszerrel csökkenthető az IP-címek száma, amelyeket a hüvelyek számára a hálózati térben kell lefoglalni.

További információ: [kubenet hálózatkezelés konfigurálása AK-fürthöz][aks-configure-kubenet-networking].

### <a name="azure-cni-advanced-networking"></a>Azure CNI (speciális) hálózatkezelés

Az Azure CNI használatával minden pod kap egy IP-címet az alhálózatból, és közvetlenül elérhetővé válik. Ezeket az IP-címeket előre kell tervezni, és a hálózati térben egyedinek kell lenniük. Mindegyik csomóponthoz tartozik egy konfigurációs paraméter az általa támogatott hüvelyek maximális számához. Ezután a csomópontok azonos számú IP-címet kell fenntartani. A tervezés nélkül ez a megközelítés az IP-címek kimerítését vagy a fürtök nagyobb alhálózaton belüli újraépítését eredményezheti, mivel az alkalmazás igényei növekednek.

A kubenet-től eltérően az azonos virtuális hálózatban lévő végpontok felé irányuló adatforgalom nem a csomópont elsődleges IP-címére vonatkozik. A virtuális hálózaton belüli forgalom forrásának címe a pod IP. A virtuális hálózaton kívüli forgalom továbbra is NAT a csomópont elsődleges IP-címével.

A csomópontok az [Azure CNI][cni-networking] Kubernetes beépülő modult használják.

![Diagram, amely két csomópontot mutat be egyetlen Azure-VNet csatlakozó hidakkal][advanced-networking-diagram]

További információ: az [Azure CNI konfigurálása az AK-fürtökhöz][aks-configure-advanced-networking].

### <a name="compare-network-models"></a>Hálózati modellek összehasonlítása

A kubenet és az Azure CNI egyaránt biztosítanak hálózati kapcsolatot az AK-fürtökhöz. Vannak azonban előnyei és hátrányai is. Magas szinten a következő szempontokat kell figyelembe venni:

* **kubenet**
    * Megőrzi az IP-címtartomány méretét.
    * A Kubernetes belső vagy külső terheléselosztó használatával éri el a hüvelyeket a fürtön kívülről.
    * A felhasználó által megadott útvonalakat (UDR) manuálisan kezelheti és karbantarthatja.
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

A DNS-t illetően a kubenet és az Azure CNI beépülő modul DNS-t is kínáljuk a CoreDNS, az AK-ban futó üzemelő példányok a saját autoskálázásával. A Kubernetes CoreDNS kapcsolatos további információkért lásd: a [DNS-szolgáltatás testreszabása](https://kubernetes.io/docs/tasks/administer-cluster/dns-custom-nameservers/). A CoreDNS alapértelmezés szerint úgy van konfigurálva, hogy ismeretlen tartományokat továbbítson azon Azure-Virtual Network DNS-funkcióihoz, amelybe az AK-fürt telepítve van. Ezért a Azure DNS és a privát zónák az AK-ban futó hüvelyek esetében működnek.

### <a name="support-scope-between-network-models"></a>A hálózati modellek közötti hatókör támogatása

Függetlenül attól, hogy milyen hálózati modellt használ, a kubenet és az Azure CNI is üzembe helyezhetők a következő módszerek egyikével:

* Az Azure platform képes automatikusan létrehozni és konfigurálni a virtuális hálózati erőforrásokat, amikor egy AK-fürtöt hoz létre.
* A virtuális hálózati erőforrásokat manuálisan is létrehozhatja és konfigurálhatja, és csatolhatja ezeket az erőforrásokat az AK-fürt létrehozásakor.

Bár a szolgáltatás-végpontok vagy UDR is támogatottak mind a kubenet, mind az Azure CNI esetében, az [AK támogatási szabályzatai][support-policies] határozzák meg, hogy milyen módosításokat végezhet el. Például:

* Ha egy AK-fürthöz manuálisan hozza létre a virtuális hálózati erőforrásokat, akkor a saját UDR vagy szolgáltatási végpontok konfigurálásakor is támogatott.
* Ha az Azure-platform automatikusan létrehozza az AK-fürthöz tartozó virtuális hálózati erőforrásokat, akkor a saját UDR vagy szolgáltatás-végpontok konfigurálásához nem módosíthatja manuálisan az AK által felügyelt erőforrásokat.

## <a name="ingress-controllers"></a>Bemeneti vezérlők

Terheléselosztó szolgáltatás létrehozásakor létrehoz egy mögöttes Azure Load Balancer-erőforrást is. A terheléselosztó úgy van konfigurálva, hogy egy adott porton keresztül továbbítsa a forgalmat a szolgáltatásban lévő hüvelyekre. 

A terheléselosztó csak a 4. rétegben működik. A 4. rétegben a szolgáltatás nem ismeri a tényleges alkalmazásokat, és nem tud további útválasztási megfontolásokat végezni.

A belépési *vezérlők* a 7. rétegben működnek, és az alkalmazások forgalmának elosztásához intelligensebb szabályokat is használhatnak. A bemeneti vezérlők általában a bejövő URL-cím alapján irányítják át a HTTP-forgalmat a különböző alkalmazásokba.

![Egy AK-fürt bejövő forgalmát bemutató diagram][aks-ingress]

### <a name="create-an-ingress-resource"></a>Bejövő erőforrások létrehozása
Az AK-ban létrehozhat egy bejövő erőforrásokat az NGINX, egy hasonló eszköz vagy az AK HTTP-alkalmazás útválasztási funkciója használatával. Ha egy AK-fürthöz engedélyezi a HTTP-alkalmazás útválasztását, az Azure platform létrehozza a bejövő vezérlőt és egy *külső DNS-* vezérlőt. Mivel a Kubernetes-ben új bejövő erőforrások jönnek létre, a szükséges DNS-rekordok egy fürtre vonatkozó DNS-zónában jönnek létre. 

További információ: a [http-alkalmazás útválasztásának telepítése][aks-http-routing].

### <a name="application-gateway-ingress-controller-agic"></a>Bejövő Application Gateway vezérlő (AGIC)

Az Application Gateway inporting Controller (AGIC) bővítménnyel a (z) rendszerű ügyfelek az Azure natív Application Gateway 7. szintű terheléselosztó használatával teszik elérhetővé a felhőalapú szoftvereket az interneten. A AGIC figyeli a gazdagép Kubernetes-fürtöt, és folyamatosan frissíti az Application Gateway, és kiteszi a kiválasztott szolgáltatásokat az internethez. 

Ha többet szeretne megtudni az AK-beli AGIC-bővítményről, tekintse meg a [Mi az Application Gateway beáramló vezérlő?][agic-overview]című témakört.

### <a name="ssltls-termination"></a>SSL/TLS-lezárás

Az SSL/TLS-lezárás egy másik gyakori szolgáltatás a bejövő forgalomban. A HTTPS-kapcsolaton keresztül elért nagyméretű webalkalmazások esetében a bejövő erőforrások nem az alkalmazáson belül, hanem a TLS-megszakítást kezelik. A TLS-hitelesítés automatikus létrehozásához és konfigurálásához beállíthatja, hogy a bejövő erőforrások olyan szolgáltatók használatára legyenek használhatók, mint például a "titkosítás". 

Az NGINX beáramlási vezérlőnek a titkosítással való konfigurálásával kapcsolatos további információkért lásd: [bejövő és TLS][aks-ingress-tls].

### <a name="client-source-ip-preservation"></a>Ügyfél forrásának IP-megőrzése

Konfigurálja a bejövő adatkezelőt úgy, hogy megőrizze az ügyfél forrásának IP-címét az AK-fürtön lévő tárolók kéréseire. Amikor a bejövő adatkezelője egy ügyfél kérelmét az AK-fürt egyik tárolójába irányítja, a kérelem eredeti forrás IP-címe nem érhető el a cél tároló számára. Ha engedélyezi az *ügyfél forrás IP-címének megőrzését*, az ügyfél forrás IP-címe a kérelem fejlécében érhető el az *X által továbbított – esetében*. 

Ha ügyfél-forrás IP-megőrzést használ a bejövő adatkezelőn, akkor nem használhatja a TLS átmenő átvitelt. Az ügyfél-forrás IP-megőrzés és a TLS-továbbítás más szolgáltatásokkal, például a *terheléselosztó* típussal is használható.

## <a name="network-security-groups"></a>Network security groups (Hálózati biztonsági csoportok)

A hálózati biztonsági csoport olyan virtuális gépek forgalmát szűri, mint az AK-csomópontok. A szolgáltatások, például a terheléselosztó létrehozása során az Azure platform automatikusan konfigurálja a szükséges hálózati biztonsági csoportokra vonatkozó szabályokat. 

Nem kell manuálisan konfigurálnia a hálózati biztonsági csoport szabályait a hüvelyek forgalmának szűréséhez egy AK-fürtben. Egyszerűen adja meg a szükséges portokat és továbbítást a Kubernetes-szolgáltatás jegyzékfájljának részeként. Lehetővé teszi, hogy az Azure platform létrehozza vagy frissítse a megfelelő szabályokat. 

Hálózati házirendeket is használhat a forgalmi szűrő szabályainak a hüvelyekre való automatikus alkalmazásához.

## <a name="network-policies"></a>Hálózati házirendek

Alapértelmezés szerint az összes AK-fürtben lévő hüvelyek korlátozás nélkül küldhetnek és fogadhatnak forgalmat. A fokozott biztonság érdekében olyan szabályokat határozhat meg, amelyek a forgalom áramlását vezérlik, például:
* A háttérbeli alkalmazások csak a szükséges előtér-szolgáltatásoknak vannak kitéve. 
* Az adatbázis-összetevők csak a hozzájuk csatlakozó alkalmazási rétegek számára érhetők el.

A hálózati házirend egy AK-ban elérhető Kubernetes funkció, amely lehetővé teszi a hüvelyek közötti adatforgalom szabályozását. A pod-ra irányuló forgalmat olyan beállítások alapján engedélyezheti vagy tilthatja le, mint a hozzárendelt címkék, a névtér vagy a forgalmi port. Habár a hálózati biztonsági csoportok jobbak az AK-csomópontok számára, a hálózati házirendek a hüvelyek forgalmának szabályozására alkalmas, felhőben natív módon szabályozzák a forgalom áramlását. Mivel a hüvelyek dinamikusan jönnek létre egy AK-fürtben, a szükséges hálózati házirendeket automatikusan alkalmazni lehet.

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
