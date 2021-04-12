---
title: Ajánlott eljárások hálózati erőforrásokhoz
titleSuffix: Azure Kubernetes Service
description: A virtuális hálózati erőforrásokhoz és az Azure Kubernetes szolgáltatásban (ak) való csatlakozáshoz használható fürtözési ajánlott eljárások ismertetése
services: container-service
ms.topic: conceptual
ms.date: 03/10/2021
ms.openlocfilehash: 1e0212766e7d5443664d57a97cfa9ea9d0035da3
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/08/2021
ms.locfileid: "107104949"
---
# <a name="best-practices-for-network-connectivity-and-security-in-azure-kubernetes-service-aks"></a>Hálózati kapcsolatra és biztonságra vonatkozó ajánlott eljárások az Azure Kubernetes Service-ben (AKS)

A fürtök Azure Kubernetes szolgáltatásban (ak) való létrehozásakor és kezelésekor hálózati kapcsolatot biztosít a csomópontjai és alkalmazásai számára. Ezek a hálózati erőforrások közé tartoznak az IP-címtartományok, a terheléselosztó és a bejövő vezérlők. Az alkalmazások magas színvonalú kiszolgálásának fenntartása érdekében strategize és konfigurálnia kell ezeket az erőforrásokat.

Ez az ajánlott eljárási cikk a fürtszolgáltatások hálózati kapcsolatára és biztonságára koncentrál. Ebben a cikkben az alábbiakkal ismerkedhet meg:

> [!div class="checklist"]
> * Hasonlítsa össze a kubenet és az Azure Container hálózatkezelési interfész (CNI) hálózati módjait az AK-ban.
> * Tervezze meg a szükséges IP-címzést és-kapcsolatot.
> * A forgalom elosztása terheléselosztó, bejövő vezérlők vagy webalkalmazási tűzfal (WAF) használatával.
> * Biztonságosan csatlakozhat a fürtcsomópontokhöz.

## <a name="choose-the-appropriate-network-model"></a>A megfelelő hálózati modell kiválasztása

> **Útmutatás az ajánlott eljárásokhoz** 
> 
> A meglévő virtuális hálózatokkal vagy helyszíni hálózatokkal való integrációhoz használja az AK-ban az Azure CNI hálózatkezelést. Ez a hálózati modell lehetővé teszi az erőforrások és a vezérlőelemek nagyobb elkülönítését egy vállalati környezetben.

A virtuális hálózatok alapszintű kapcsolatot biztosítanak az AK-csomópontok és az ügyfelek számára az alkalmazások eléréséhez. Az AK-fürtök két különböző módon telepíthetők virtuális hálózatokra:

* **Azure CNI hálózatkezelés**

    Üzembe helyez egy virtuális hálózatban, és az [Azure CNI][cni-networking] Kubernetes beépülő modult használja. A hüvelyek olyan egyedi IP-címeket fogadnak, amelyek más hálózati szolgáltatásokhoz vagy helyszíni erőforrásokhoz is továbbíthatók.
* **Kubenet hálózatkezelés**

    Az Azure kezeli a virtuális hálózati erőforrásokat, mivel a fürt üzembe lett helyezve, és a [kubenet][kubenet] Kubernetes beépülő modult használja.


Éles üzembe helyezés esetén a kubenet és az Azure CNI is érvényes beállítások.

### <a name="cni-networking"></a>CNI hálózatkezelés

Az Azure CNI egy olyan szállító-semleges protokoll, amely lehetővé teszi, hogy a tároló futásidejű kérelmeit egy hálózati szolgáltató számára tegye elérhetővé. Az IP-címeket a hüvelyekhez és a csomópontokhoz rendeli hozzá, és az IP-címek kezelésének (IPAM) funkcióit biztosítja a meglévő Azure-beli virtuális hálózatokhoz való kapcsolódáskor. Az egyes csomópontok és Pod-erőforrások IP-címet kapnak az Azure Virtual Networkben – nincs szükség további útválasztásra más erőforrásokkal vagy szolgáltatásokkal való kommunikációhoz.

![Diagram, amely két csomópontot mutat be egyetlen Azure-VNet csatlakozó hidakkal](media/operator-best-practices-network/advanced-networking-diagram.png)

Az Azure CNI-hálózatkezelés az éles környezetben lehetővé teszi az erőforrások felügyeletének és kezelésének elkülönítését. Biztonsági szempontból gyakran más csapatoknak is szüksége lehet az erőforrások felügyeletére és védelmére. Az Azure CNI hálózatkezeléssel a meglévő Azure-erőforrásokhoz, helyszíni erőforrásokhoz és egyéb szolgáltatásokhoz közvetlenül kapcsolódhat az egyes Pod-eszközökhöz rendelt IP-címeken keresztül.

Ha Azure CNI hálózatkezelést használ, a virtuális hálózati erőforrás egy különálló erőforráscsoport az AK-fürthöz. Engedélyek delegálása az AK-beli fürt identitásához az erőforrások eléréséhez és kezeléséhez. Az AK-fürt által használt fürt identitásának legalább [hálózati közreműködői](../role-based-access-control/built-in-roles.md#network-contributor) engedélyekkel kell rendelkeznie a virtuális hálózaton belüli alhálózaton. 

Ha [Egyéni szerepkört](../role-based-access-control/custom-roles.md) szeretne definiálni a beépített hálózati közreműködő szerepkör használata helyett, a következő engedélyek szükségesek:
  * `Microsoft.Network/virtualNetworks/subnets/join/action`
  * `Microsoft.Network/virtualNetworks/subnets/read`

Alapértelmezés szerint az AK felügyelt identitást használ a fürt identitásához. Ehelyett azonban használhat egy egyszerű szolgáltatást. További tudnivalók a következőkről:
* Az AK szolgáltatás egyszerű delegálása: [hozzáférés delegálása más Azure-erőforrásokhoz][sp-delegation]. 
* Felügyelt identitások: [felügyelt identitások használata](use-managed-identity.md).

Mivel az egyes csomópontok és a pod a saját IP-címét kapja, tervezze meg az AK-alhálózatok címtartományt. Ne feledje:
* Az alhálózatnak elég nagynak kell lennie ahhoz, hogy az Ön által telepített összes csomópont, hüvely és hálózati erőforrás IP-címét meg lehessen adni. 
    * Mind a kubenet, mind az Azure CNI hálózatkezelés esetében a futtatott csomópontok mindegyike alapértelmezett korlátozásokkal rendelkezik a hüvelyek számához.
* Az egyes AK-fürtöket a saját alhálózatán kell elhelyezni. 
* Kerülje a meglévő hálózati erőforrásokkal átfedésben lévő IP-címtartományok használatát. 
    * Az Azure-ban helyszíni vagy egymással létesített hálózatokhoz való kapcsolódás engedélyezése szükséges.
* A kibővíthető események és a fürtök frissítéseinek kezeléséhez további IP-címekre van szükség a hozzárendelt alhálózatban. 
    * Ez az extra címtartomány különösen fontos, ha Windows Server-tárolókat használ, mivel a csomópont-készletek frissítést igényelnek a legújabb biztonsági javítások alkalmazásához. A Windows Server-csomópontokkal kapcsolatos további információkért lásd: [csomópont-készlet frissítése az AK-ban][nodepool-upgrade].

A szükséges IP-cím kiszámításához lásd: az [Azure CNI Networking konfigurálása az AK-ban][advanced-networking].

Fürt Azure CNI hálózatkezeléssel való létrehozásakor meg kell adnia a fürt más címtartományt, például a Docker-híd címét, a DNS-szolgáltatási IP-címet és a szolgáltatási címtartományt. Általánosságban ellenőrizze, hogy ezek a címtartományok:
* Ne legyen átfedés egymással.
* Ne legyen átfedésben a fürthöz társított hálózatokkal, beleértve a virtuális hálózatokat, az alhálózatokat, a helyszíni és a társas hálózatokat. 

A címtartományok korlátainak és méretezésének részletes ismertetését lásd: az [Azure CNI Networking konfigurálása az AK-ban][advanced-networking].

### <a name="kubenet-networking"></a>Kubenet hálózatkezelés

Bár a kubenet nem igényli, hogy a fürt üzembe helyezése előtt állítsa be a virtuális hálózatokat, a várakozás hátrányai vannak:

* Mivel a csomópontok és a hüvelyek különböző IP-alhálózatokra vannak helyezve, a felhasználó által megadott útválasztás (UDR) és az IP-továbbítási útvonalak a hüvelyek és a csomópontok közötti forgalmat Ez a további útválasztás csökkentheti a hálózati teljesítményt.
* A meglévő helyszíni hálózatokhoz vagy más Azure-beli virtuális hálózatokhoz való csatlakozás bonyolult lehet.

Mivel nem hozza létre a virtuális hálózatot és az alhálózatokat az AK-fürttől függetlenül, a Kubenet ideális a következőkhöz:
* Kis fejlesztési vagy tesztelési feladatok. 
* Alacsony forgalmú egyszerű webhelyek.
* A számítási feladatok tárolóba való emelése és áthelyezése.

A legtöbb éles környezetben az Azure CNI hálózatkezelését kell terveznie és használni.

[A kubenet használatával saját IP-címtartományt és virtuális hálózatokat is konfigurálhat][aks-configure-kubenet-networking]. Az Azure CNI hálózatkezeléshez hasonlóan ezek a címtartományok nem fedik át egymást, és nem fedik át a fürthöz társított hálózatokat (a virtuális hálózatokat, az alhálózatokat, a helyszíni és a társas hálózatokat). 

A címtartományok korlátainak és méretezésének részletes ismertetését lásd: [kubenet hálózatkezelés használata saját IP-címtartományok használatával az AK-ban][aks-configure-kubenet-networking].

## <a name="distribute-ingress-traffic"></a>Bejövő forgalom elosztása

> **Útmutatás az ajánlott eljárásokhoz** 
> 
> Ha HTTP-vagy HTTPS-forgalmat szeretne terjeszteni az alkalmazásaiba, használja a bejövő erőforrásokat és a vezérlőket. Az Azure Load Balancerhez képest a bejövő vezérlők további funkciókat biztosítanak, és natív Kubernetes-erőforrásként kezelhetők.

Míg az Azure Load Balancer az AK-fürtben lévő alkalmazásokba is terjesztheti az ügyfelek forgalmát, a forgalom megértése korlátozott. A terheléselosztó erőforrás a 4. rétegben működik, és protokoll vagy portok alapján osztja el a forgalmat. 

A HTTP-t vagy HTTPS-t használó webalkalmazások többsége a 7. rétegbeli Kubernetes-bejövő erőforrásokat és vezérlőket használja. A bejövő forgalom elosztása az alkalmazás URL-címe és a TLS/SSL-lezárás kezelése alapján végezhető el. A bejövő forgalom emellett csökkenti a kimutatott és leképezett IP-címek számát is. 

A terheléselosztó esetében minden alkalmazásnak jellemzően egy nyilvános IP-címet kell kiosztania, amelyet a szolgáltatáshoz kell hozzárendelni az AK-fürtben. A bejövő erőforrások esetében egyetlen IP-cím több alkalmazás számára is terjesztheti a forgalmat.

![Egy AK-fürt bejövő forgalmát bemutató diagram](media/operator-best-practices-network/aks-ingress.png)

 Két összetevő áll rendelkezésre a bejövő forgalomhoz:

 * Bejövő *erőforrások*
 * Bejövő *adatkezelő*

### <a name="ingress-resource"></a>Bejövő erőforrások

A *bejövő erőforrás* a YAML jegyzéke `kind: Ingress` . Meghatározza a gazdagépet, a tanúsítványokat és a szabályokat, amelyekkel átirányíthatja a forgalmat az AK-fürtön futó szolgáltatásokra. 

Az alábbi példa YAML-jegyzék a *MyApp.com* forgalmát a két szolgáltatás, a *blogservice* vagy a *storeservice* egyikére terjeszti. Az ügyfél egy szolgáltatásra vagy a másikra irányítja az általuk elért URL-cím alapján.

```yaml
kind: Ingress
metadata:
 name: myapp-ingress
   annotations: kubernetes.io/ingress.class: "PublicIngress"
spec:
 tls:
 - hosts:
   - myapp.com
   secretName: myapp-secret
 rules:
   - host: myapp.com
     http:
      paths:
      - path: /blog
        backend:
         serviceName: blogservice
         servicePort: 80
      - path: /store
        backend:
         serviceName: storeservice
         servicePort: 80
```

### <a name="ingress-controller"></a>Bejövő adatkezelő

A bejövő *vezérlő* egy AK-csomóponton futó démon, amely a beérkező kéréseket figyeli. Ezt követően a forgalom elosztása a bejövő erőforrások erőforrásában meghatározott szabályok alapján történik. Míg a leggyakoribb bejövő forgalom-vezérlő az [NGINX]-re épül, az AK nem korlátozza Önt egy adott vezérlőre. Használhatja a [Contour][contour], a [HAProxy][haproxy], a [Traefik][traefik]stb.

A bejövő vezérlőket Linux-csomóponton kell ütemezni. Azt jelzi, hogy az erőforrásnak Linux-alapú csomóponton kell futnia a YAML manifest vagy a Helm diagram üzembe helyezése csomópont-választóval. További információkért lásd: [csomópont-választók használata annak vezérléséhez, hogy a hüvelyek hol vannak ütemezve az AK-ban][concepts-node-selectors].

> [!NOTE]
> Windows Server-csomópontok nem futtathatják a bejövő forgalmi vezérlőt.

Számos forgatókönyv áll rendelkezésre a bejövő forgalomhoz, beleértve a következő útmutatókat:

* [Alapszintű bejövő adatvezérlő létrehozása külső hálózati kapcsolattal][aks-ingress-basic]
* [Belső, privát hálózatot és IP-címet használó bejövő adatforgalom-vezérlő létrehozása][aks-ingress-internal]
* [Saját TLS-tanúsítványokat használó bejövő adatkezelő létrehozása][aks-ingress-own-tls]
* Hozzon létre egy olyan bejövő vezérlőt, amely lehetővé teszi a titkosítást [egy dinamikus nyilvános IP-címmel][aks-ingress-tls] vagy [statikus nyilvános IP-címmel rendelkező][aks-ingress-static-tls] TLS-tanúsítványok automatikus létrehozásához

## <a name="secure-traffic-with-a-web-application-firewall-waf"></a>Biztonságos forgalom webalkalmazási tűzfallal (WAF)

> **Útmutatás az ajánlott eljárásokhoz**
> 
> A lehetséges támadások bejövő forgalmának vizsgálatához használjon egy webalkalmazási tűzfalat (WAF), például [BARRACUDA WAF for Azure][barracuda-waf] vagy Azure Application Gateway. Ezek a fejlettebb hálózati erőforrások csak a HTTP-és HTTPS-kapcsolatokon, vagy az alapszintű TLS-megszakításon túl is irányítják a forgalmat.

A bejövő vezérlő általában egy Kubernetes-erőforrás az AK-fürtben, amely elosztja a forgalmat a szolgáltatásokhoz és alkalmazásokhoz. A vezérlő egy AK-csomóponton démonként fut, és a csomópont erőforrásait, például a PROCESSZORt, a memóriát és a hálózati sávszélességet használja. Nagyobb környezetekben a következőkre lesz szüksége:
* A forgalmi útválasztás vagy a TLS-megszakítás egy részét kiszervezheti egy hálózati erőforrásra az AK-fürtön kívül.
* A potenciális támadások bejövő forgalmának vizsgálata.

![A webalkalmazási tűzfal (WAF), például az Azure app Gateway képes a forgalom biztonságára és terjesztésére az AK-alapú fürt számára](media/operator-best-practices-network/web-application-firewall-app-gateway.png)

Ennél az extra biztonsági rétegnél a webalkalmazási tűzfal (WAF) szűri a bejövő forgalmat. Az Open Web Application Security Project (OWASP) a szabályok halmazával figyeli a támadásokat, például a helyek közötti parancsfájlkezelést vagy a cookie-mérgezést. Az [Azure Application Gateway][app-gateway] (jelenleg előzetes verzióban érhető el) egy WAF, amely az AK-fürtökkel integrálható, és a biztonsági funkciók zárolása előtt a forgalom eléri az AK-fürtöt és-alkalmazásokat. 

Mivel más, harmadik féltől származó megoldások is végrehajtják ezeket a funkciókat, továbbra is használhatja a meglévő beruházásokat vagy szakértelmet a kívánt termékben.

A Load Balancer vagy a bejövő erőforrások folyamatosan futnak az AK-fürtön, és pontosítják a forgalom eloszlását. Az alkalmazás-átjáró központilag felügyelhető egy erőforrás-definícióval rendelkező bejövő vezérlőként. Első lépésként [hozzon létre egy Application Gateway bejövő adatkezelőt][app-gateway-ingress].

## <a name="control-traffic-flow-with-network-policies"></a>A forgalmi forgalom szabályozása hálózati házirendekkel

> **Útmutatás az ajánlott eljárásokhoz** 
>
> Hálózati házirendek használata a hüvelyek forgalmának engedélyezéséhez vagy megtagadásához. Alapértelmezés szerint az összes forgalom engedélyezett a fürtön belüli hüvelyek között. A fokozott biztonság érdekében határozza meg a pod-kommunikációt korlátozó szabályokat.

A hálózati házirend egy AK-ban elérhető Kubernetes funkció, amely lehetővé teszi a hüvelyek közötti adatforgalom szabályozását. A pod-ra irányuló forgalmat olyan beállítások alapján engedélyezheti vagy tilthatja le, mint a hozzárendelt címkék, a névtér vagy a forgalmi port. A hálózati házirendek Felhőbeli natív módon szabályozzák a hüvelyek forgalmának áramlását. Mivel a hüvelyek dinamikusan jönnek létre egy AK-fürtben, a szükséges hálózati házirendeket automatikusan alkalmazni lehet.

Ha hálózati házirendet szeretne használni, engedélyezze a szolgáltatást új AK-fürt létrehozásakor. A hálózati házirend nem engedélyezhető egy meglévő AK-fürtön. Előre tervezze meg a hálózati házirend engedélyezését a szükséges fürtökön. 

>[!NOTE]
>A hálózati házirendet csak a Linux-alapú csomópontok és a hüvelyek esetében kell használni az AK-ban.

A hálózati házirendet Kubernetes-erőforrásként kell létrehozni egy YAML-jegyzékfájl használatával. A szabályzatok meghatározott hüvelyekre érvényesek, a bejövő és a kimenő forgalomra vonatkozó szabályokkal. 

Az alábbi példa egy hálózati házirendet alkalmaz a hüvelyekre az *alkalmazással: háttérbeli* címke alkalmazva. A bejövő forgalom szabálya csak a hüvelyek forgalmát engedélyezi az *alkalmazás:* előtér-címke használatával:

```yaml
kind: NetworkPolicy
apiVersion: networking.k8s.io/v1
metadata:
  name: backend-policy
spec:
  podSelector:
    matchLabels:
      app: backend
  ingress:
  - from:
    - podSelector:
        matchLabels:
          app: frontend
```

A szabályzatok használatának megkezdéséhez lásd: a [hüvelyek közötti biztonságos forgalom a hálózati házirendek használatával az Azure Kubernetes szolgáltatásban (ak)][use-network-policies].

## <a name="securely-connect-to-nodes-through-a-bastion-host"></a>Biztonságos kapcsolódás a csomópontokhoz egy megerősített gazdagépen keresztül

> **Útmutatás az ajánlott eljárásokhoz** 
>
> Ne tegye elérhetővé az AK-csomópontok távoli kapcsolatát. Hozzon létre egy megerősített gazdagépet vagy egy Jump Box-t egy felügyeleti virtuális hálózaton. A megerősített gazdagép használatával biztonságosan irányíthatja át a forgalmat az AK-fürtbe a távoli felügyeleti feladatokhoz.

A legtöbb műveletet az AK-ban az Azure felügyeleti eszközeivel vagy a Kubernetes API-kiszolgálón keresztül végezheti el. Az AK-csomópontok csak privát hálózaton érhetők el, és nem kapcsolódnak a nyilvános internethez. A csomópontokhoz való csatlakozáshoz és a karbantartás és támogatás biztosításához irányítsa a kapcsolatokat egy megerősített gazdagépen vagy egy Jump Box-on keresztül. Ellenőrizze, hogy ez az állomás egy különálló, biztonságos módon működtetett felügyeleti virtuális hálózatban lakik-e az AK-alapú fürt virtuális hálózatában.

![Kapcsolódás az AK-csomópontokhoz egy megerősített gazdagép vagy egy Jump Box használatával](media/operator-best-practices-network/connect-using-bastion-host-simplified.png)

A megerősített gazdagép felügyeleti hálózatát is védeni kell. Egy [Azure ExpressRoute][expressroute] vagy [VPN Gateway][vpn-gateway] használatával csatlakozhat egy helyszíni hálózathoz, és szabályozhatja a hozzáférést hálózati biztonsági csoportokkal.

## <a name="next-steps"></a>Következő lépések

Ez a cikk a hálózati kapcsolatra és a biztonságra koncentrál. További információ a Kubernetes hálózati alapjairól: az [Azure Kubernetes szolgáltatásban található alkalmazások hálózati fogalmai (ak)][aks-concepts-network]

<!-- LINKS - External -->
[cni-networking]: https://github.com/Azure/azure-container-networking/blob/master/docs/cni.md
[kubenet]: https://kubernetes.io/docs/concepts/cluster-administration/network-plugins/#kubenet
[app-gateway-ingress]: https://github.com/Azure/application-gateway-kubernetes-ingress
[Nginx]: https://www.nginx.com/products/nginx/kubernetes-ingress-controller
[contour]: https://github.com/heptio/contour
[haproxy]: https://www.haproxy.org
[traefik]: https://github.com/containous/traefik
[barracuda-waf]: https://www.barracuda.com/products/webapplicationfirewall/models/5

<!-- INTERNAL LINKS -->
[aks-concepts-network]: concepts-network.md
[sp-delegation]: kubernetes-service-principal.md#delegate-access-to-other-azure-resources
[expressroute]: ../expressroute/expressroute-introduction.md
[vpn-gateway]: ../vpn-gateway/vpn-gateway-about-vpngateways.md
[aks-ingress-internal]: ingress-internal-ip.md
[aks-ingress-static-tls]: ingress-static-ip.md
[aks-ingress-basic]: ingress-basic.md
[aks-ingress-tls]: ingress-tls.md
[aks-ingress-own-tls]: ingress-own-tls.md
[app-gateway]: ../application-gateway/overview.md
[use-network-policies]: use-network-policies.md
[advanced-networking]: configure-azure-cni.md
[aks-configure-kubenet-networking]: configure-kubenet.md
[concepts-node-selectors]: concepts-clusters-workloads.md#node-selectors
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool