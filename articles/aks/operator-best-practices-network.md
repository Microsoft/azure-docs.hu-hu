---
title: Ajánlott eljárások hálózati erőforrásokhoz
titleSuffix: Azure Kubernetes Service
description: A virtuális hálózati erőforrásokhoz és az Azure Kubernetes szolgáltatásban (ak) való csatlakozáshoz használható fürtözési ajánlott eljárások ismertetése
services: container-service
ms.topic: conceptual
ms.date: 12/10/2018
ms.openlocfilehash: 2bd332dbf9412f5c42e77b14ada3aab67ec8b66a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "102508588"
---
# <a name="best-practices-for-network-connectivity-and-security-in-azure-kubernetes-service-aks"></a>Hálózati kapcsolatra és biztonságra vonatkozó ajánlott eljárások az Azure Kubernetes Service-ben (AKS)

A fürtök Azure Kubernetes szolgáltatásban (ak) való létrehozásakor és kezelésekor hálózati kapcsolatot biztosít a csomópontjai és alkalmazásai számára. Ezek a hálózati erőforrások közé tartoznak az IP-címtartományok, a terheléselosztó és a bejövő vezérlők. Az alkalmazások magas színvonalú kiszolgálásának fenntartása érdekében meg kell terveznie, majd konfigurálnia kell ezeket az erőforrásokat.

Ez az ajánlott eljárási cikk a fürtszolgáltatások hálózati kapcsolatára és biztonságára koncentrál. Ebben a cikkben az alábbiakkal ismerkedhet meg:

> [!div class="checklist"]
> * A kubenet és az Azure Container Networking Interface (CNI) hálózati módjai összehasonlítása az AK-ban
> * A szükséges IP-címzés és-kapcsolat tervezése
> * Forgalom elosztása terheléselosztó, bejövő vezérlők vagy webalkalmazási tűzfal (WAF) használatával
> * Biztonságos kapcsolódás fürtcsomópontok számára

## <a name="choose-the-appropriate-network-model"></a>A megfelelő hálózati modell kiválasztása

**Ajánlott eljárási útmutató** – a meglévő virtuális hálózatokkal vagy helyszíni hálózatokkal való integrációhoz használja az Azure CNI hálózatkezelést az AK-ban. Ez a hálózati modell az erőforrások és a vezérlők nagyobb elkülönítését is lehetővé teszi egy vállalati környezetben.

A virtuális hálózatok alapszintű kapcsolatot biztosítanak az AK-csomópontok és az ügyfelek számára az alkalmazások eléréséhez. Az AK-fürtök két különböző módon telepíthetők virtuális hálózatokra:

* **Kubenet hálózatkezelés** – az Azure felügyeli a virtuális hálózati erőforrásokat, mivel a fürt üzembe lett helyezve, és a [Kubenet][kubenet] Kubernetes beépülő modult használja.
* **Azure CNI hálózatkezelés** – üzembe helyezi egy virtuális hálózatban, és az [Azure Container Network Interface (CNI)][cni-networking] Kubernetes beépülő modult használja. A hüvelyek olyan egyedi IP-címeket fogadnak, amelyek más hálózati szolgáltatásokhoz vagy helyszíni erőforrásokhoz is továbbíthatók.

Éles üzembe helyezés esetén a kubenet és az Azure CNI is érvényes beállítások.

### <a name="cni-networking"></a>CNI hálózatkezelés

A Container Network Interface (CNI) egy olyan szállító-semleges protokoll, amely lehetővé teszi, hogy a tároló futásidejű kérelmeit egy hálózati szolgáltató számára tegye elérhetővé. Az Azure-CNI az IP-címeket a hüvelyekhez és a csomópontokhoz rendeli hozzá, és az IP-címek kezelésére szolgáló (IPAM) funkciókat biztosít a meglévő Azure-beli virtuális hálózatokhoz való kapcsolódáshoz. Minden egyes csomópont-és Pod-erőforrás IP-címet kap az Azure Virtual Networkben, és nincs szükség további útválasztásra más erőforrásokkal vagy szolgáltatásokkal való kommunikációhoz.

![Diagram, amely két csomópontot mutat be egyetlen Azure-VNet csatlakozó hidakkal](media/operator-best-practices-network/advanced-networking-diagram.png)

Az Azure CNI hálózatkezelésének jelentős előnye, hogy a hálózati modell lehetővé teszi az erőforrások felügyeletének és kezelésének elkülönítését. Biztonsági szempontból gyakran más csapatoknak is szüksége lehet az erőforrások felügyeletére és védelmére. Az Azure CNI hálózatkezelés lehetővé teszi a meglévő Azure-erőforrásokhoz, helyszíni erőforrásokhoz és egyéb szolgáltatásokhoz való kapcsolódást közvetlenül az egyes Pod-eszközökhöz rendelt IP-címeken keresztül.

Ha Azure CNI hálózatkezelést használ, a virtuális hálózati erőforrás egy különálló erőforráscsoport az AK-fürthöz. Engedélyek delegálása az AK-beli fürt identitásához az erőforrások eléréséhez és kezeléséhez. Az AK-fürt által használt fürt identitásának legalább [hálózati közreműködői](../role-based-access-control/built-in-roles.md#network-contributor) engedélyekkel kell rendelkeznie a virtuális hálózaton belüli alhálózaton. Ha [Egyéni szerepkört](../role-based-access-control/custom-roles.md) szeretne definiálni a beépített hálózati közreműködő szerepkör használata helyett, a következő engedélyek szükségesek:
  * `Microsoft.Network/virtualNetworks/subnets/join/action`
  * `Microsoft.Network/virtualNetworks/subnets/read`

Alapértelmezés szerint az AK felügyelt identitást használ a fürt identitásához, de lehetősége van egy egyszerű szolgáltatásnév használatára. Az AK szolgáltatás egyszerű delegálásával kapcsolatos további információkért lásd: [hozzáférés delegálása más Azure-erőforrásokhoz][sp-delegation]. A felügyelt identitásokkal kapcsolatos további információkért lásd: [felügyelt identitások használata](use-managed-identity.md).

Mivel a csomópontok és a pod a saját IP-címüket kapják, tervezze meg az AK-alhálózatok címtartományt. Az alhálózatnak elég nagynak kell lennie ahhoz, hogy az Ön által telepített összes csomópont, hüvely és hálózati erőforrás IP-címét meg lehessen adni. Az egyes AK-fürtöket a saját alhálózatán kell elhelyezni. A helyszíni vagy az Azure-beli helyi hálózatokhoz való kapcsolódás engedélyezéséhez ne használjon olyan IP-címtartományt, amely átfedésben van a meglévő hálózati erőforrásokkal. Az egyes csomópontok által futtatott kubenet és az Azure CNI hálózatkezelésének alapértelmezett korlátai vannak. A kibővíthető események és a fürtök frissítésének kezeléséhez további IP-címekre is szükség van a hozzárendelt alhálózatban való használathoz. Ez az extra címtartomány különösen fontos, ha Windows Server-tárolókat használ, mivel a csomópont-készletek frissítést igényelnek a legújabb biztonsági javítások alkalmazásához. A Windows Server-csomópontokkal kapcsolatos további információkért lásd: [csomópont-készlet frissítése az AK-ban][nodepool-upgrade].

A szükséges IP-cím kiszámításához lásd: az [Azure CNI Networking konfigurálása az AK-ban][advanced-networking].

Amikor Azure CNI hálózatkezeléssel hoz létre fürtöt, a fürt által használt egyéb címtartományt is megadhat, például a Docker-híd címét, a DNS-szolgáltatás IP-címét és a szolgáltatási címtartományt. Általánosságban elmondható, hogy ezek a címtartományok nem fedik át egymást, és nem fedik át a fürthöz társított hálózatokat, beleértve a virtuális hálózatokat, az alhálózatokat, a helyszíni és a társas hálózatokat. A címtartományok korlátainak és méretezésének részletes ismertetését lásd: az [Azure CNI Networking konfigurálása az AK-ban][advanced-networking].

### <a name="kubenet-networking"></a>Kubenet hálózatkezelés

Bár a kubenet nem igényli a virtuális hálózatok beállítását a fürt üzembe helyezése előtt, hátrányok vannak:

* A csomópontok és a hüvelyek eltérő IP-alhálózatokra vannak helyezve. A felhasználó által megadott útválasztás (UDR) és az IP-továbbítás a hüvelyek és a csomópontok közötti forgalom irányítására szolgál. Ez a további útválasztás csökkentheti a hálózati teljesítményt.
* A meglévő helyszíni hálózatokhoz vagy más Azure-beli virtuális hálózatokhoz való csatlakozás bonyolult lehet.

A Kubenet alkalmas a kisebb fejlesztési vagy tesztelési feladatokhoz, mivel nem kell külön létrehoznia a virtuális hálózatot és az alhálózatokat az AK-fürtből. Az alacsony forgalmú egyszerű webhelyek, illetve a számítási feladatok tárolóba való átemelése a kubenet hálózatkezeléssel üzembe helyezett AK-fürtök egyszerűségét is kihasználhatja. A legtöbb éles környezetben az Azure CNI hálózatkezelését kell terveznie és használni.

[A kubenet használatával saját IP-címtartományt és virtuális hálózatokat is konfigurálhat][aks-configure-kubenet-networking]. Az Azure CNI hálózatkezeléshez hasonlóan ezek a címtartományok nem fedik át egymást, és nem fedik át a fürthöz társított hálózatokat, beleértve a virtuális hálózatokat, az alhálózatokat, a helyszíni és a társas hálózatokat. A címtartományok korlátainak és méretezésének részletes ismertetését lásd: [kubenet hálózatkezelés használata saját IP-címtartományok használatával az AK-ban][aks-configure-kubenet-networking].

## <a name="distribute-ingress-traffic"></a>Bejövő forgalom elosztása

**Ajánlott eljárási útmutató** – http-vagy https-forgalom terjesztése az alkalmazásokba, a bejövő erőforrások és a vezérlők használata. A bejövő vezérlők további funkciókat biztosítanak egy normál Azure Load balancerben, és natív Kubernetes-erőforrásként kezelhetők.

Az Azure Load Balancer az ügyfelek adatforgalmát az AK-fürtben lévő alkalmazásokba terjesztheti, de ez csak abban van, hogy mit ért el a forgalom. A terheléselosztó erőforrás a 4. rétegben működik, és protokoll vagy portok alapján osztja el a forgalmat. A HTTP-t vagy HTTPS-t használó legtöbb webalkalmazásnak a 7. rétegbeli Kubernetes-bejövő erőforrásokat és vezérlőket kell használnia. A bejövő forgalom elosztása az alkalmazás URL-címe és a TLS/SSL-lezárás kezelése alapján végezhető el. Ez a funkció csökkenti a kimutatott és leképezett IP-címek számát is. A terheléselosztó esetében minden alkalmazásnak jellemzően egy nyilvános IP-címet kell kiosztania, amelyet a szolgáltatáshoz kell hozzárendelni az AK-fürtben. A bejövő erőforrások esetében egyetlen IP-cím több alkalmazás számára is terjesztheti a forgalmat.

![Egy AK-fürt bejövő forgalmát bemutató diagram](media/operator-best-practices-network/aks-ingress.png)

 Két összetevő áll rendelkezésre a bejövő forgalomhoz:

 * Egy bejövő *erőforrás*, és
 * Bejövő *adatkezelő*

A bejövő erőforrás egy YAML-jegyzék, `kind: Ingress` amely meghatározza a gazdagépet, a tanúsítványokat és a szabályokat, amelyekkel átirányíthatja a forgalmat az AK-fürtön futó szolgáltatásokra. Az alábbi példa YAML-jegyzék a *MyApp.com* forgalmát a két szolgáltatás, a *blogservice* vagy a *storeservice* egyikére terjeszti. Az ügyfél egy szolgáltatásra vagy a másikra irányítja az általuk elért URL-cím alapján.

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

A bejövő vezérlő egy AK-csomóponton futó démon, amely a beérkező kéréseket figyeli. Ezt követően a forgalom elosztása a bejövő erőforrások erőforrásában meghatározott szabályok alapján történik. A legelterjedtebb bejövő vezérlő az [NGINX]-alapú. Az AK nem korlátozza Önt egy adott vezérlőre, így más vezérlőket is használhat, például a [Contour][contour], a [HAProxy][haproxy]vagy a [Traefik][traefik].

A bejövő vezérlőket Linux-csomóponton kell ütemezni. Windows Server-csomópontok nem futtathatják a bejövő forgalmi vezérlőt. A YAML-jegyzékfájl vagy a Helm-diagram üzembe helyezése csomópont-választóval jelezheti, hogy az erőforrásnak Linux-alapú csomóponton kell futnia. További információkért lásd: [csomópont-választók használata annak vezérléséhez, hogy a hüvelyek hol vannak ütemezve az AK-ban][concepts-node-selectors].

Számos forgatókönyv áll rendelkezésre a bejövő forgalomhoz, beleértve a következő útmutatókat:

* [Alapszintű bejövő adatvezérlő létrehozása külső hálózati kapcsolattal][aks-ingress-basic]
* [Belső, privát hálózatot és IP-címet használó bejövő adatforgalom-vezérlő létrehozása][aks-ingress-internal]
* [Saját TLS-tanúsítványokat használó bejövő adatkezelő létrehozása][aks-ingress-own-tls]
* Hozzon létre egy olyan bejövő vezérlőt, amely lehetővé teszi a titkosítást [egy dinamikus nyilvános IP-címmel][aks-ingress-tls] vagy [statikus nyilvános IP-címmel rendelkező][aks-ingress-static-tls] TLS-tanúsítványok automatikus létrehozásához

## <a name="secure-traffic-with-a-web-application-firewall-waf"></a>Biztonságos forgalom webalkalmazási tűzfallal (WAF)

**Ajánlott eljárási útmutató** – a lehetséges támadások beérkező forgalmának vizsgálatához használja a webalkalmazási tűzfalat (WAF), például a [Barracuda WAF for Azure vagy az][barracuda-waf] Azure Application Gateway. Ezek a fejlettebb hálózati erőforrások csak a HTTP-és HTTPS-kapcsolatokon, vagy az alapszintű TLS-megszakításon túl is irányítják a forgalmat.

A szolgáltatások és alkalmazások forgalmát terjesztő bejövő vezérlő általában Kubernetes-erőforrás az AK-fürtben. A vezérlő egy AK-csomóponton démonként fut, és a csomópont erőforrásait, például a PROCESSZORt, a memóriát és a hálózati sávszélességet használja. Nagyobb környezetekben gyakran szeretné kiszervezni a forgalmi útválasztás vagy a TLS-leállítást egy hálózati erőforrásra az AK-fürtön kívül. A potenciális támadások esetében is ellenőrizni szeretné a bejövő forgalmat.

![A webalkalmazási tűzfal (WAF), például az Azure app Gateway képes a forgalom biztonságára és terjesztésére az AK-alapú fürt számára](media/operator-best-practices-network/web-application-firewall-app-gateway.png)

A webalkalmazási tűzfal (WAF) további biztonsági réteget biztosít a bejövő forgalom szűrésével. Az Open Web Application Security Project (OWASP) olyan szabályokat tartalmaz, amelyekkel megfigyelheti a több helyre történő parancsfájlok vagy a cookie-mérgezés elleni támadásokat. Az [Azure Application Gateway][app-gateway] (jelenleg előzetes verzióban érhető el) egy olyan WAF, amely az AK-fürtökkel integrálva biztosítja ezeket a biztonsági funkciókat, mielőtt a forgalom eléri az AK-fürtöt és-alkalmazásokat. Más, harmadik féltől származó megoldások is végrehajtják ezeket a funkciókat, így továbbra is használhatnak egy adott termék meglévő befektetéseit vagy szakértelmét.

A terheléselosztó vagy a bejövő erőforrások továbbra is futnak az AK-fürtön, hogy tovább pontosítsák a forgalom eloszlását. Az alkalmazás-átjáró központilag felügyelhető egy erőforrás-definícióval rendelkező bejövő vezérlőként. Első lépésként [hozzon létre egy Application Gateway bejövő adatkezelőt][app-gateway-ingress].

## <a name="control-traffic-flow-with-network-policies"></a>A forgalmi forgalom szabályozása hálózati házirendekkel

**Ajánlott eljárások – útmutatás** – hálózati házirendek használata a hüvelyek forgalmának engedélyezéséhez vagy megtagadásához. Alapértelmezés szerint az összes forgalom engedélyezett a fürtön belüli hüvelyek között. A fokozott biztonság érdekében határozza meg a pod-kommunikációt korlátozó szabályokat.

A hálózati házirend egy Kubernetes funkció, amely lehetővé teszi a hüvelyek közötti adatforgalom szabályozását. Dönthet úgy, hogy engedélyezi vagy megtagadja a forgalmat olyan beállítások alapján, mint a hozzárendelt címkék, a névtér vagy a forgalmi port. A hálózati házirendek használatával Felhőbeli natív módon vezérelheti a forgalom áramlását. Mivel a hüvelyek dinamikusan jönnek létre egy AK-fürtben, a szükséges hálózati házirendeket automatikusan alkalmazni lehet. Ne használjon Azure hálózati biztonsági csoportokat a pod-to-Pod típusú forgalom vezérléséhez, használja a hálózati házirendeket.

A hálózati házirend használatához engedélyezni kell a funkciót, ha AK-fürtöt hoz létre. A hálózati házirend nem engedélyezhető egy meglévő AK-fürtön. Előre tervezze meg, hogy engedélyezi-e a hálózati házirendet a fürtökön, és igény szerint használhatja őket. A hálózati házirendet csak a Linux-alapú csomópontok és a hüvelyek esetében kell használni az AK-ban.

A hálózati szabályzatok Kubernetes-erőforrásként jönnek létre YAML-jegyzékfájl használatával. A szabályzatok meghatározott hüvelyekre érvényesek, majd a bejövő és a kimenő forgalom szabályai határozzák meg, hogy a forgalom hogyan áramlik. Az alábbi példa egy hálózati házirendet alkalmaz a hüvelyekre az *alkalmazással: háttérbeli* címke alkalmazva. A befelé irányuló szabály ekkor csak a hüvelyek forgalmát engedélyezi az *alkalmazás:* előtér-címkével:

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

**Ajánlott eljárási útmutató** – ne tegye elérhetővé az AK-csomópontok távoli kapcsolatát. Hozzon létre egy megerősített gazdagépet vagy egy Jump Box-t egy felügyeleti virtuális hálózaton. A megerősített gazdagép használatával biztonságosan irányíthatja át a forgalmat az AK-fürtbe a távoli felügyeleti feladatokhoz.

Az AK-ban a legtöbb művelet az Azure felügyeleti eszközeivel vagy a Kubernetes API-kiszolgálóval végezhető el. Az AK-csomópontok nem kapcsolódnak a nyilvános internethez, és csak privát hálózaton érhetők el. A csomópontokhoz való csatlakozáshoz és a karbantartáshoz, illetve a hibák elhárításához, a kapcsolatok továbbítása egy megerősített gazdagépen vagy a Jump Box használatával. Ennek a gazdagépnek egy különálló felügyeleti virtuális hálózatban kell lennie, amely biztonságosan csatlakozik az AK-fürt virtuális hálózatához.

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