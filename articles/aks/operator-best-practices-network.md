---
title: Operátor ajánlott eljárások – hálózati kapcsolatot az Azure Kubernetes-szolgáltatások (AKS)
description: Ismerje meg a fürt operátor gyakorlati tanácsok a virtuális hálózati erőforrások és a kapcsolatot az Azure Kubernetes Service (AKS)
services: container-service
author: rockboyfor
ms.service: container-service
ms.topic: conceptual
origin.date: 12/10/2018
ms.date: 04/08/2019
ms.author: v-yeche
ms.openlocfilehash: aaa16245fada7fbccdd0865d973de2fa19970989
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60464005"
---
# <a name="best-practices-for-network-connectivity-and-security-in-azure-kubernetes-service-aks"></a>Hálózati kapcsolat és az Azure Kubernetes Service (AKS) biztonsági védelmének bevált gyakorlata

Hozzon létre és kezelheti a fürtöket az Azure Kubernetes Service (AKS), adjon meg hálózati kapcsolat a csomópontok és az alkalmazások számára. A hálózati erőforrások közé tartoznak a IP-címtartományok, a terheléselosztók és a bejövő forgalom tartományvezérlők. Jó minőségű szolgáltatást az alkalmazások karbantartásához, meg kell tervezése, és adja meg ezeket az erőforrásokat.

Ez – gyakorlati tanácsok cikk összpontosít hálózati kapcsolati és biztonsági fürt operátorok számára. Ebben a cikkben az alábbiakkal ismerkedhet meg:

> [!div class="checklist"]
> * Hasonlítsa össze a kubenet és Azure CNI hálózati módok az aks-ben
> * Szükséges IP-címkezelés és a kapcsolatok tervezése
> * Osztja el a forgalmat, terheléselosztók, bejövő tartományvezérlők vagy a webalkalmazás-tűzfalak (WAF) használatával
> * Biztonságosan csatlakozhat a fürt csomópontjai

## <a name="choose-the-appropriate-network-model"></a>Válassza ki a megfelelő hálózati modell

**Ajánlott eljárásokkal kapcsolatos útmutatás** - integráció a meglévő virtuális hálózatokat vagy a helyszíni hálózatok, az Azure CNI hálózat használatának az aks-ben. A hálózati modell lehetővé teszi az erőforrások és a vezérlők nagyobb elkülönítése vállalati környezetben.

Virtuális hálózatok biztosítják a hálózati kapcsolat az AKS-csomópontok és az ügyfelek számára az alkalmazások. Helyezhet üzembe az AKS-fürtöket virtuális hálózatokra két különböző módja van:

* **Kubenet hálózatkezelés** – Azure kezeli a virtuális hálózati erőforrásokat, a fürt üzemel, és használja a [kubenet] [ kubenet] Kubernetes beépülő modult.
* **Az Azure-hálózatok CNI** – egy meglévő virtuális hálózatban helyez üzembe, és használja a [Azure Container hálózati adapter (CNI)] [ cni-networking] Kubernetes beépülő modult. Podok kapnak egyéni IP-címek, amelyek más hálózati szolgáltatások vagy a helyszíni erőforrásokhoz irányíthatja.

A tároló hálózati adapter (CNI) szállító-semleges protokoll, amely lehetővé teszi, hogy a tároló-futtatókörnyezet kéréseket küld egy hálózati szolgáltató. Az Azure CNI podok és csomópontok IP-címeket rendel, és IP-cím címfelügyeleti (IPAM) olyan szolgáltatásokat nyújt a meglévő Azure virtuális hálózatok összekapcsolása. Minden egyes csomópont és a pod erőforrás IP-címet kap az Azure virtuális hálózatban, és nincs további útválasztási kommunikálni más erőforrásokhoz vagy szolgáltatásokhoz szükség van.

![A hidak egyes egyetlen Azure virtuális hálózathoz csatlakozó két csomópont bemutató ábra.](media/operator-best-practices-network/advanced-networking-diagram.png)

A legtöbb éles környezetekben üzemelő példányok Azure CNI hálózatkezelés kell használnia. A hálózati modell lehetővé teszi, hogy a vezérlőelem elkülönül az erőforrások kezelését. Biztonsági szempontból a gyakran érdemes különböző csapat felügyeletére és védelmére ezeket az erőforrásokat. Az Azure CNI hálózatkezelés lehetővé teszi a meglévő Azure-erőforrások, a helyszíni erőforrásokhoz vagy más szolgáltatások közvetlenül rendelt egyes pod IP-címek keresztül csatlakozik.

Azure CNI hálózat használata esetén a virtuális hálózati erőforrás szerepel egy külön erőforráscsoportot, amelybe az AKS-fürtöt. Az AKS szolgáltatásnévhez elérését és kezelését, ezekhez az erőforrásokhoz tartozó engedélyeket delegálhatnak. Az AKS-fürt által használt egyszerű szolgáltatás rendelkeznie kell legalább [hálózati közreműködő](../role-based-access-control/built-in-roles.md#network-contributor) az alhálózaton belül a virtuális hálózat engedélyeit. Ha meg szeretné egy [egyéni szerepkör](../role-based-access-control/custom-roles.md) helyett használja a hálózati közreműködő szerepkört, a következő engedélyek szükségesek:
  * `Microsoft.Network/virtualNetworks/subnets/join/action`
  * `Microsoft.Network/virtualNetworks/subnets/read`

Az AKS szolgáltatás egyszerű delegálással kapcsolatos további információkért lásd: [más Azure-erőforrásokhoz való hozzáférés delegálásához][sp-delegation].

Mivel minden egyes csomópont és a pod a saját IP-címet kapnak, tervezze meg az AKS alhálózat címtartományát. Az alhálózat elég nagynak kell lennie minden csomópont, a podok és a hálózati erőforrások, központilag telepített IP-címek számára. Minden egyes AKS-fürtöt a saját alhálózatában kell elhelyezni. Ahhoz, hogy a kapcsolat a helyszíni vagy a társviszonyban lévő hálózatok az Azure-ban, ne használjon egymást átfedő IP-címtartományok a meglévő hálózati erőforrásokhoz. Minden egyes csomópont kubenet és Azure CNI-hálózatok egyaránt futtató podok számát az alapértelmezett korlátozva van. Vertikális felskálázási események vagy a fürtfrissítések kezelése érdekében is szüksége lesz elérhető további IP-címek használatát a hozzárendelt alhálózat.

Az IP-cím kiszámításához szükséges, lásd: [konfigurálása Azure CNI hálózatkezelés az aks-ben][advanced-networking].

### <a name="kubenet-networking"></a>Kubenet hálózatkezelés

Kubenet nincs szükség a virtuális hálózatok beállítását, mielőtt a fürt üzembe lesz helyezve, bár vannak hátrányait:

* Csomópontok és a podok különböző IP-alhálózatok kerülnek. Felhasználó által megadott útválasztás (UDR) és IP-továbbítás segítségével podok és a csomópontok közötti forgalom irányítására. A további útválasztási csökkentheti a hálózati teljesítmény.
* Csatlakozhat a helyszíni hálózatok meglévő, vagy más Azure virtuális hálózatok társviszony összetett folyamat lehet.

Kubenet nem alkalmas a kisebb fejlesztési és tesztelési számítási feladatok esetén, mert nem kell létrehozni a virtuális hálózat és alhálózatok külön-külön az AKS-fürtöt a. Egyszerű webhelyek alacsony forgalmú vagy lift- and -shift munkaterhelések vannak tárolókba, előnyeit is kihasználhatják az egyszerűség AKS-fürtök üzembe helyezett kubenet hálózattal. A legtöbb éles környezetekben üzemelő példányok tervezze meg és Azure CNI hálózattal. Emellett [konfigurálása a saját IP-címtartományok és a virtuális hálózatok használatával kubenet][aks-configure-kubenet-networking].

## <a name="distribute-ingress-traffic"></a>Bejövő forgalom elosztása

**Ajánlott eljárásokkal kapcsolatos útmutatás** – a HTTP vagy HTTPS-forgalom az alkalmazások terjesztése, használja a bejövő forgalom erőforrások és a tartományvezérlők. Bejövő forgalom tartományvezérlőkön keresztül rendszeres Azure load balancer további alkalmazásszolgáltatások biztosítása érdekében, és natív Kubernetes-erőforrásként kezelheti.

Az Azure load balancer terjesztheti az alkalmazásokat az AKS-fürt ügyfelek adatforgalmát, de mi, tisztában van azzal, hogy a forgalom korlátozódik. Egy terheléselosztó-erőforrásra 4 rétegben működik, és elosztja a forgalmat a protokoll és portok alapján. A legtöbb webes alkalmazások, amelyek a HTTP vagy HTTPS PROTOKOLLT kell használnia, Kuberenetes bejövő erőforrások és a tartományvezérlők, amelyek a 7. rétegben működik. Bejövő forgalom URL-címét a alkalmazást, és kezelni a TLS/SSL-lezárást alapján lehet osztani. Ez a képesség teszi közzé, és a térkép IP-címek száma is csökkenti. Az a load balancerrel mindegyik alkalmazás általában kell hozzárendelve, és az AKS-fürtöt a szolgáltatáshoz rendelt nyilvános IP-címet. Egy belépő erőforrással egyetlen IP-cím több alkalmazás-forgalom juttathatja el.

![Az AKS-fürt bejövő adatforgalmat bemutató ábra.](media/operator-best-practices-network/aks-ingress.png)

 A bejövő forgalom két összetevőből áll:

 * Bejövő forgalmi *erőforrás*, és
 * Bejövő forgalmi *vezérlő*

A bejövő forgalom erőforrás egy YAML-jegyzékfájlt, `kind: Ingress` , amely meghatározza a gazdagép, a tanúsítványokat és a szabályok a forgalom irányítása az AKS-fürt futó szolgáltatások. A következő példa YAML jegyzékfájl lenne osztja el a forgalmat a *myapp.com* két szolgáltatás egyik *blogservice* vagy *storeservice*. Van irányítva az ügyfél egy szolgáltatás, vagy a másik férnek hozzá az URL-címe alapján.

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

Bejövőforgalom-vezérlőjéhez egy démon, amely egy AKS-csomóponton fut, és a bejövő kéréseket figyeli. Ezután ossza forgalmat a bejövő forgalom erőforrás megadott szabályok alapján. A leggyakoribb bejövőforgalom-vezérlőjéhez alapján [NGINX]. Az AKS nem korlátozza az Ön konkrét vezérlőhöz, így például használhatja más vezérlők [kontúrt][contour], [HAProxy][haproxy], vagy [ Traefik][traefik].

Nincsenek számos forgatókönyvek a bejövő forgalom, többek között a következő útmutatókat:

* [Hozzon létre egy alapszintű bejövőforgalom-vezérlőjéhez külső hálózatok közötti kapcsolatokkal][aks-ingress-basic]
* [Hozzon létre egy bejövőforgalom-vezérlőt, amely egy belső, saját hálózat és IP-cím][aks-ingress-internal]
* [Hozzon létre egy saját TLS-tanúsítványokat használ a bejövőforgalom-vezérlőt][aks-ingress-own-tls]
* Hozzon létre egy bejövőforgalom-vezérlőt használó hozzunk titkosítása automatikusan létrehozni a TLS-tanúsítványok [dinamikus nyilvános IP-címmel rendelkező] [ aks-ingress-tls] vagy [egy statikus nyilvános IP-címmel][aks-ingress-static-tls]

## <a name="secure-traffic-with-a-web-application-firewall-waf"></a>A webalkalmazási tűzfal (WAF) forgalmának biztonságossá tétele

**Ajánlott eljárásokkal kapcsolatos útmutatás** – a bejövő forgalmat a potenciális támadások vizsgálata, például használja a webalkalmazási tűzfal (WAF) [Barracuda WAF for Azure] [ barracuda-waf] vagy az Azure Application Gateway. Ezek összetettebb hálózati erőforrások is irányíthatja a forgalmat csak a HTTP és HTTPS-kapcsolatok vagy az alapvető SSL-lezárást túl.

Egy bejövőforgalom-vezérlőt, amely elosztja a forgalmat a szolgáltatások és alkalmazások általában egy Kubernetes-erőforrást az AKS-fürt az. A vezérlő démonként fut, egy AKS-csomóponton, és a csomópont forrásokhoz, például a Processzor, memória és a hálózati sávszélességet használ fel. Nagyobb környezetekben milyen gyakran szeretne kiszervezni néhány a forgalom-útválasztást vagy a TLS-lezárást az AKS-fürtön kívüli hálózati erőforráshoz. Emellett vizsgálni kívánt potenciális támadások bejövő forgalmat.

![Webalkalmazási tűzfal (WAF) például az Azure App Gateway védelmét, és az AKS-fürt a forgalom elosztását](media/operator-best-practices-network/web-application-firewall-app-gateway.png)

Webalkalmazási tűzfal (WAF) egy további biztonsági réteget nyújt a bejövő forgalom szűrésével. Az Open Web Application Security Project (OWASP) a támadásoktól, például az idegen hely parancsfájl-kezelési és cookie-k identitáshamisítás kapcsolatos szabályok készletét nyújtja. [Az Azure Application Gateway] [ app-gateway] (az aks-ben jelenleg előzetes verzió) egy WAF, amely előtt a forgalom eléri az AKS-fürt és az alkalmazások, adja meg ezeket a biztonsági funkciókat az AKS-fürtökkel integrálva. Egyéb külső megoldások is így továbbra is használja a meglévő befektetések vagy szakértelmet az adott termék hajtsa végre ezeket a funkciókat.

Load balancer vagy a belépő erőforrások továbbra is fut tovább finomíthatja az adatforgalom eloszlása az AKS-fürt. Alkalmazásátjáró-bejövőforgalom-vezérlőt egy erőforrás-definícióval, központilag kezelhetők. Első lépésként [hozzon létre egy Application Gateway Bejövőforgalom-vezérlőjéhez][app-gateway-ingress].

## <a name="control-traffic-flow-with-network-policies"></a>Átvitelvezérlés forgalom hálózati házirendekkel

**Ajánlott eljárásokkal kapcsolatos útmutatás** – hálózati házirendek segítségével a podok adatforgalom engedélyezéséhez vagy letiltásához. Alapértelmezés szerint az összes forgalom fürtözött pods között engedélyezett. A fokozott biztonság céljából definiálása a szabályokat, amelyek a pod kommunikáció korlátozása.

Hálózati házirend (jelenleg előzetes verzióban érhető el az aks-ben) egy Kubernetes-szolgáltatás, amely lehetővé teszi a podok közötti adatforgalom szabályozásához. Ha szeretné, beállítások, például a hozzárendelt címkék, névtérre vagy forgalmat port alapján adatforgalom engedélyezéséhez vagy letiltásához. A hálózati házirendek használatát szabályozza a forgalom egy natív felhőalapú megoldást kínál. Podok dinamikusan az AKS-fürt létrehozásakor, a szükséges hálózati házirendeket a rendszer automatikusan elvégez. Ne használja az Azure-beli hálózati biztonsági csoportok a pod-pod-forgalom szabályozása hálózati házirendek használja.

Hálózati házirend használatára, a funkciót engedélyezni kell egy AKS-fürt létrehozásakor. Nem engedélyezhető a hálózati házirend egy meglévő AKS-fürtre. Tervez, győződjön meg arról, hogy engedélyezze a hálózati házirend-fürtökön, és használhatja őket igény szerint.

A hálózati házirend használatával egy YAML-jegyzékfájlt Kubernetes erőforrásként jön létre. A szabályzatokkal a megadott podok, majd a bejövő és kimenő szabályok határozzák meg, hogy az adatforgalom is. Az alábbi példa egy hálózati házirend vonatkozik a podok a *app: háttérrendszer* címkét alkalmazza őket. A bejövő szabály csak lehetővé teszi a podok érkező forgalmat a *app: előtérbeli* címkét:

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

Első lépések a házirendek, lásd: [podok hálózati házirendek segítségével az Azure Kubernetes Service (AKS) közötti adatforgalom biztonságossá][use-network-policies].

## <a name="securely-connect-to-nodes-through-a-bastion-host"></a>Biztonságosan csatlakozhat a csomópontok bástyagazdagép keresztül

**Ajánlott eljárásokkal kapcsolatos útmutatás** – távoli kapcsolatot az AKS-csomópontok nem teszik elérhetővé. Hozzon létre egy bástyagazdagép, de a mezőben, és a felügyeleti virtuális hálózati ugorhat. A bástyagazdagép használatával biztonságosan irányítja a forgalmat az AKS-fürt távoli felügyeleti feladatok be.

Az aks-ben a legtöbb művelet elvégezhető az Azure felügyeleti eszközeivel vagy a Kubernetes API-kiszolgálón keresztül. AKS-csomópontok nem csatlakoznak az internethez csatlakozik, és csak egy magánhálózaton érhetők el. Csatlakozhat csomópontokhoz és karbantartásával, vagy hibáinak elhárítása, a kapcsolatok bástyagazdagép keresztül irányítani, illetve jump-box. Egy különálló felügyeleti virtuális hálózatra, amelyhez az AKS-fürt virtuális hálózat társviszonyban biztonságosan van ezen a gazdagépen kell lennie.

![AKS-csomópontok bástyagazdagép használatával csatlakozni, vagy a jump-box](media/operator-best-practices-network/connect-using-bastion-host-simplified.png)

A felügyeleti hálózat a megerősített gazdagép védelméhez, túl. Használata egy [Azure ExpressRoute] [ expressroute] vagy [VPN-átjáró] [ vpn-gateway] a helyszíni hálózat csatlakoztatása, és hálózati biztonság hozzáférés csoportok.

## <a name="next-steps"></a>További lépések

Ez a cikk a hálózati kapcsolatot és a biztonsági összpontosít. Hálózati alapjai a Kubernetes kapcsolatos további információkért lásd: [hálózati alkalmazások az Azure Kubernetes Service (AKS) fogalmai][aks-concepts-network]

<!-- LINKS - External -->
[cni-networking]: https://github.com/Azure/azure-container-networking/blob/master/docs/cni.md
[kubenet]: https://kubernetes.io/docs/concepts/cluster-administration/network-plugins/#kubenet
[app-gateway-ingress]: https://github.com/Azure/application-gateway-kubernetes-ingress
[nginx]: https://www.nginx.com/products/nginx/kubernetes-ingress-controller
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