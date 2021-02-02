---
title: Az Azure AD Application Proxy hálózati topológiájának szempontjai
description: A hálózati topológia szempontjait ismerteti az Azure AD Application Proxy használatakor.
services: active-directory
documentationcenter: ''
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/22/2019
ms.author: kenwith
ms.reviewer: harshja
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: d67505e7112c41b21b2ae5e8acc834ff047a470d
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/02/2021
ms.locfileid: "99254805"
---
# <a name="network-topology-considerations-when-using-azure-active-directory-application-proxy"></a>Hálózati topológiai megfontolások Azure Active Directory Application Proxy használatakor

Ez a cikk ismerteti a hálózati topológiával kapcsolatos szempontokat, amikor a Azure Active Directory (Azure AD) alkalmazásproxy használatával távolról teszi közzé és érheti el az alkalmazásokat.

## <a name="traffic-flow"></a>Forgalmi folyamat

Ha egy alkalmazást az Azure AD Application Proxyon keresztül tesznek közzé, a felhasználók és az alkalmazások közötti forgalom három kapcsolaton keresztül áramlik:

1. A felhasználó az Azure AD Application Proxy szolgáltatás nyilvános végpontján csatlakozik az Azure-ban
1. Az Application proxy szolgáltatás csatlakozik az alkalmazásproxy-összekötőhöz
1. Az alkalmazásproxy-összekötő csatlakozik a célalkalmazás

![A felhasználótól a célalkalmazás felé irányuló forgalmat bemutató ábra](./media/application-proxy-network-topology/application-proxy-three-hops.png)

## <a name="tenant-location-and-application-proxy-service"></a>Bérlő helye és alkalmazásproxy szolgáltatása

Az Azure AD-bérlőre való regisztráláskor a bérlő régióját a megadott ország/régió határozza meg. Az alkalmazásproxy engedélyezésekor a bérlőhöz tartozó alkalmazásproxy-példányok az Azure AD-Bérlővel megegyező régióban vannak kiválasztva vagy létrehozva, vagy a legközelebbi régiót.

Ha például az Azure AD-bérlő országa vagy régiója az Egyesült Királyság, az összes alkalmazásproxy-összekötő használja az Európai adatközpontokban lévő szolgáltatási példányokat. Amikor a felhasználók közzétett alkalmazásokhoz férnek hozzá, a forgalom az alkalmazás proxy szolgáltatás példányain halad át ezen a helyen.

## <a name="considerations-for-reducing-latency"></a>A késés csökkentése szempontjai

Minden proxy-megoldás késést jelent a hálózati kapcsolatban. Függetlenül attól, hogy melyik proxy-vagy VPN-megoldást választja a távelérési megoldásként, mindig tartalmaz olyan kiszolgálókat, amelyek lehetővé teszik a kapcsolódást a vállalati hálózaton belül.

A szervezetek jellemzően a peremhálózaton lévő kiszolgálói végpontokat tartalmazzák. Az Azure AD Application Proxy azonban a forgalom a felhőben a proxy szolgáltatáson keresztül áramlik, miközben az összekötők a vállalati hálózaton találhatók. Nincs szükség peremhálózati hálózatra.

A következő részekben további javaslatokat talál, amelyek segítségével még tovább csökkentheti a késést. 

### <a name="connector-placement"></a>Összekötő elhelyezése

Az alkalmazásproxy kiválasztja a példányok helyét a bérlő helye alapján. Azonban eldöntheti, hogy hová szeretné telepíteni az összekötőt, így meghatározhatja a hálózati forgalom késési jellemzőit.

Az alkalmazásproxy szolgáltatás beállításakor tegye a következő kérdéseket:

- Hol található az alkalmazás?
- Hol találhatók a legtöbb felhasználó, aki hozzáfér az alkalmazáshoz?
- Hol találhatók az alkalmazásproxy-példányok?
- Van olyan dedikált hálózati kapcsolata az Azure-adatközpontokhoz, mint például az Azure ExpressRoute vagy egy hasonló VPN?

Az összekötőnek kommunikálnia kell az Azure-val és az alkalmazásokkal (a forgalmi folyamat diagramjának 2. és 3. lépése), így az összekötő elhelyezése befolyásolja a két kapcsolat késését. Az összekötő elhelyezésének kiértékelése során vegye figyelembe a következő szempontokat:

- Ha a Kerberos által korlátozott delegálást (KCD) szeretné használni az egyszeri bejelentkezéshez, az összekötőnek egy adatközpontra van szüksége. Emellett az összekötő-kiszolgálónak tartományhoz kell csatlakoznia.  
- Ha kétségei vannak, az összekötőt közelebb kell telepíteni az alkalmazáshoz.

### <a name="general-approach-to-minimize-latency"></a>Általános megközelítés a késés minimalizálásához

Az egyes hálózati kapcsolatok optimalizálásával csökkentheti a végpontok közötti forgalom késését. Minden egyes kapcsolatban a következőket lehet optimalizálni:

- Csökkentse a távolságot a hop két vége között.
- A megfelelő hálózat kiválasztása a bejáráshoz. Előfordulhat például, hogy a privát hálózat nem a nyilvános Internet, hanem a dedikált hivatkozások miatt gyorsabban halad.

Ha az Azure és a vállalati hálózat között dedikált VPN-vagy ExpressRoute-kapcsolattal rendelkezik, érdemes ezt használni.

## <a name="focus-your-optimization-strategy"></a>Az optimalizálási stratégia fókuszba helyezése

A felhasználók és az alkalmazásproxy szolgáltatás közötti kapcsolat szabályozása kevéssé szükséges. A felhasználók otthoni hálózatból, kávézóból vagy más országból/régióból férhetnek hozzá az alkalmazásaihoz. Ehelyett optimalizálhatja az alkalmazásproxy szolgáltatás kapcsolatait az alkalmazásproxy-összekötők és az alkalmazások között. Vegye fontolóra a következő minták beépítését a környezetbe.

### <a name="pattern-1-put-the-connector-close-to-the-application"></a>1. minta: az összekötő elhelyezése az alkalmazáshoz

Helyezze az összekötőt a célalkalmazás közelébe a Customer Networkben. Ez a konfiguráció a domborzati diagram 3. lépését is lekicsinyíti, mivel az összekötő és az alkalmazás be van zárva.

Ha az összekötőnek szüksége van a tartományvezérlőre, akkor ez a minta előnyös. Ügyfeleink többsége ezt a mintát használja, mivel a legtöbb esetben jól működik. Ez a minta a 2. mintával együtt is használható a szolgáltatás és az összekötő közötti forgalom optimalizálása érdekében.

### <a name="pattern-2-take-advantage-of-expressroute-with-microsoft-peering"></a>2. minta: a ExpressRoute előnyeinek kihasználása a Microsoft-partnerekkel

Ha a Microsoft ExpressRoute van beállítva, az alkalmazásproxy és az összekötő közötti adatforgalomhoz a gyorsabb ExpressRoute-kapcsolatot használhatja. Az összekötő továbbra is a hálózaton található, az alkalmazáshoz közeledve.

### <a name="pattern-3-take-advantage-of-expressroute-with-private-peering"></a>3. minta: a ExpressRoute előnyeinek kihasználása privát partnerekkel

Ha dedikált VPN-vagy ExpressRoute van beállítva az Azure és a vállalati hálózat között, akkor egy másik lehetőség áll rendelkezésére. Ebben a konfigurációban az Azure-beli virtuális hálózat általában a vállalati hálózat kiterjesztésének tekintendő. Így telepítheti az összekötőt az Azure-adatközpontban, és továbbra is kielégíti az összekötő és az alkalmazás közötti kapcsolatok alacsony késési követelményeit.

A késés nem sérül, mert a forgalom egy dedikált kapcsolaton keresztül áramlik. Emellett továbbfejlesztett alkalmazásproxy-szolgáltatás-összekötő késést érhet el, mivel az összekötő egy Azure-adatközpontban van telepítve az Azure AD-bérlői helyhez közel.

![Az Azure-adatközponton belül telepített összekötőt bemutató ábra](./media/application-proxy-network-topology/application-proxy-expressroute-private.png)

### <a name="other-approaches"></a>Egyéb megközelítések

Bár ennek a cikknek a fókusza az összekötő elhelyezése, az alkalmazás elhelyezését is módosíthatja, hogy jobb késési tulajdonságokat kapjon.

Egyre több szervezet helyezi át hálózatait az üzemeltetett környezetbe. Ez lehetővé teszi, hogy az alkalmazásaikat a vállalati hálózatának részét képező üzemeltetett környezetbe helyezik, és továbbra is a tartományon belül legyenek. Ebben az esetben az előző szakaszokban tárgyalt mintákat lehet alkalmazni az új alkalmazás helyére. Ha ezt a lehetőséget fontolgatja, tekintse meg a [Azure ad Domain Services](../../active-directory-domain-services/overview.md).

Emellett a különböző helyszíneken és hálózatokon lévő alkalmazások célzásához használjon [összekötő-csoportokat](application-proxy-connector-groups.md) az összekötők megszervezéséhez.

## <a name="common-use-cases"></a>Gyakori használati helyzetek

Ebben a szakaszban néhány gyakori forgatókönyvet ismertetünk. Tegyük fel, hogy az Azure AD-bérlő (és így a proxy szolgáltatás végpontja) a Egyesült Államok (US) helyen található. Az ezekben a használati esetekben tárgyalt megfontolások a világ más régióira is érvényesek.

Ezekben a forgatókönyvekben az egyes kapcsolatokat "hop"-ként hívjuk fel, és így könnyebben megoszthatjuk a vitát:

- **1. Ugrás**: felhasználó az alkalmazásproxy szolgáltatáshoz
- **2. Ugrás**: alkalmazásproxy-szolgáltatás alkalmazása az alkalmazásproxy-összekötőbe
- **3. Ugrás**: alkalmazásproxy-összekötő a célalkalmazás számára 

### <a name="use-case-1"></a>1. eset használata

**Forgatókönyv:** Az alkalmazás az Egyesült államokbeli szervezet hálózatában található, és a felhasználók ugyanabban a régióban vannak. Nem létezik ExpressRoute vagy VPN az Azure-adatközpont és a vállalati hálózat között.

**Javaslat:** Kövesse az 1. mintát, amelyet az előző szakaszban ismertetett. A jobb késés érdekében vegye fontolóra a ExpressRoute használatát, ha szükséges.

Ez egy egyszerű minta. A 3. Ugrás optimalizálásához helyezze el az összekötőt az alkalmazás közelében. Ez természetes választás is, mivel az összekötő jellemzően az alkalmazáshoz és az adatközponthoz van telepítve, hogy KCD műveleteket hajtson végre.

![A felhasználókat, proxyt, összekötőt és alkalmazást bemutató diagram mind az Egyesült Államokban](./media/application-proxy-network-topology/application-proxy-pattern1.png)

### <a name="use-case-2"></a>2. eset használata

**Forgatókönyv:** Az alkalmazás az Egyesült államokbeli szervezet hálózatában van, és a felhasználók globálisan oszlanak meg. Nem létezik ExpressRoute vagy VPN az Azure-adatközpont és a vállalati hálózat között.

**Javaslat:** Kövesse az 1. mintát, amelyet az előző szakaszban ismertetett.

A közös minta a hop 3 optimalizálása, ahol az összekötőt az alkalmazás közelében helyezheti el. A hop 3 általában nem drága, ha az azonos régión belül van. Az 1. Ugrás azonban drágább lehet attól függően, hogy hol található a felhasználó, mivel a világ minden pontján elérhető felhasználóknak el kell érniük az Application proxy-példányt az USA-ban. Érdemes megjegyezni, hogy bármely proxy megoldás hasonló tulajdonságokkal rendelkezik, mint a felhasználók globális terjesztése.

![A felhasználók globálisan oszlanak el, de minden más az Egyesült Államokban](./media/application-proxy-network-topology/application-proxy-pattern2.png)

### <a name="use-case-3"></a>3. eset használata

**Forgatókönyv:** Az alkalmazás az Egyesült államokbeli szervezet hálózatában található. A Microsoft ExpressRoute az Azure és a vállalati hálózat között van.

**Javaslat:** Kövesse az 1. és a 2. mintát az előző szakaszban leírtak szerint.

Először helyezze el az összekötőt a lehető leghamarabb az alkalmazáshoz. Ezt követően a rendszer automatikusan a ExpressRoute használja a 2. ugráshoz.

Ha a ExpressRoute hivatkozás a Microsoft-társat használja, a proxy és az összekötő közötti forgalom a kapcsolaton keresztül áramlik. A 2. Ugrás optimalizált késéssel rendelkezik.

![A proxy és az összekötő közötti ExpressRoute mutató diagram](./media/application-proxy-network-topology/application-proxy-pattern3.png)

### <a name="use-case-4"></a>4. eset használata

**Forgatókönyv:** Az alkalmazás az Egyesült államokbeli szervezet hálózatában található. Az Azure és a vállalati hálózat között ExpressRoute van.

**Javaslat:** Kövesse az előző szakaszban ismertetett 3. mintát.

Helyezze az összekötőt az Azure-adatközpontba, amely a vállalati hálózathoz csatlakozik a ExpressRoute-alapú privát kapcsolaton keresztül.

Az összekötő helyezhető az Azure-adatközpontba. Mivel az összekötő továbbra is az alkalmazáshoz és az adatközponthoz tartozik a magánhálózaton keresztül, a hop 3 továbbra is optimalizálva marad. Emellett a 2. Ugrás is tovább van optimalizálva.

![Összekötő az Azure-adatközpontban, ExpressRoute az összekötő és az alkalmazás között](./media/application-proxy-network-topology/application-proxy-pattern4.png)

### <a name="use-case-5"></a>5. eset használata

**Forgatókönyv:** Az alkalmazás az Európai szervezet hálózatában található, az alkalmazásproxy-példánnyal és az Egyesült Államokban a legtöbb felhasználóval.

**Javaslat:** Helyezze el az összekötőt az alkalmazás közelében. Mivel az Egyesült államokbeli felhasználók egy olyan alkalmazásproxy-példányhoz férnek hozzá, amely ugyanabban a régióban található, az 1. Ugrás nem túl drága. A hop 3 optimalizált. A 2. Ugrás optimalizálásához érdemes a ExpressRoute-t használni.

![Ábrán láthatók a felhasználók és a proxyk az Egyesült Államokban, az összekötőben és az alkalmazásban Európában](./media/application-proxy-network-topology/application-proxy-pattern5b.png)

Ebben a helyzetben érdemes lehet egy másik változatot is használni. Ha a szervezet legtöbb felhasználója az Egyesült Államokban van, akkor valószínű, hogy a hálózat az USA-ra is kiterjed. Helyezze el az összekötőt az USA-ban, és használja a dedikált belső vállalati hálózati vonalat az alkalmazáshoz Európában. Így a 2. és a 3. Ugrás is optimalizálva van.

![Ábrán láthatók a felhasználók, a proxyk és az összekötők az USA-ban, az Európai alkalmazásokban](./media/application-proxy-network-topology/application-proxy-pattern5c.png)

## <a name="next-steps"></a>Következő lépések

- [Alkalmazásproxy engedélyezése](application-proxy-add-on-premises-application.md)
- [Egyszeri bejelentkezés engedélyezése](application-proxy-configure-single-sign-on-with-kcd.md)
- [Feltételes hozzáférés engedélyezése](application-proxy-integrate-with-sharepoint-server.md)
- [Az alkalmazásproxyval kapcsolatos problémák hibaelhárítása](application-proxy-troubleshoot.md)
