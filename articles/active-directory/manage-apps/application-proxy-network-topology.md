---
title: Az Azure AD Application Proxy hálózati topológiájának szempontjai
description: Hálózati topológia szempontjai foglalkozik, amikor az Azure AD-alkalmazásproxy használatával.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/22/2019
ms.author: mimart
ms.reviewer: harshja
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 075b2c92168afe0c366608266c38b14394b73cff
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/21/2019
ms.locfileid: "74275489"
---
# <a name="network-topology-considerations-when-using-azure-active-directory-application-proxy"></a>Hálózati topológia szempontjai Azure Active Directory Application Proxy használatával

Ez a cikk ismerteti a hálózati topológia szempontjai közzétételéhez, és távolról fér hozzá az alkalmazások Azure Active Directory (Azure AD) alkalmazásproxy használata esetén.

## <a name="traffic-flow"></a>Forgalom

Amikor egy alkalmazás közzé van téve az Azure AD-alkalmazásproxyn keresztül, a felhasználóktól az alkalmazások számára a forgalom három kapcsolatokon keresztül:

1. A felhasználó csatlakozik az Azure AD-alkalmazásproxy szolgáltatás nyilvános végpontot az Azure-ban
1. Az alkalmazásproxy-szolgáltatás csatlakozik az alkalmazásproxy-összekötő
1. Az Application Proxy connector csatlakozik a célalkalmazás

![Ábra adatforgalmat a felhasználótól a célalkalmazás](./media/application-proxy-network-topology/application-proxy-three-hops.png)

## <a name="tenant-location-and-application-proxy-service"></a>Bérlő helye és az alkalmazásproxy-szolgáltatás

Az Azure AD-bérlőre való regisztráláskor a bérlő régióját a megadott ország/régió határozza meg. Ha engedélyezi az alkalmazásproxyt, az alkalmazásproxy szolgáltatás példányainak a bérlő számára választott vagy és az Azure AD-bérlője ugyanabban a régióban, vagy az ahhoz legközelebbi régióban létrehozott.

Például ha az Azure AD-bérlő ország vagy régió az Egyesült Királyság, az alkalmazásproxy-összekötők használata szolgáltatáspéldányok EU adatközpontokban. Amikor a felhasználók hozzáférjenek a közzétett alkalmazásokhoz, hogy a forgalmat végighalad az alkalmazásproxy-szolgáltatás példányainak ezen a helyen.

## <a name="considerations-for-reducing-latency"></a>Csökkenti a késéseket szempontjai

Az összes proxymegoldások késés töltsünk a hálózati kapcsolatot. Függetlenül attól, hogy mely proxy- vagy VPN-megoldás a távelérési megoldás választja a kapcsolat a vállalati hálózaton belüli engedélyezése a kiszolgálók egy csoportja mindig tartalmazza.

Szervezetek általában tartalmaznak kiszolgálói végpontot a szegélyhálózaton. Az Azure AD-alkalmazásproxy azonban a forgalom a felhőbeli proxy szolgáltatáson keresztül, míg az összekötők a vállalati hálózaton találhatók. Nincs szegélyhálózat-alapú hálózatra szükség.

A következő szakaszok tartalmazzák további javaslatok megosztásához írjon még tovább a késés csökkentése érdekében. 

### <a name="connector-placement"></a>Összekötő elhelyezése

Az alkalmazásproxy példányok helyét, a bérlő helye alapján választja ki. Azonban kap arról dönteni, hogy telepítse az összekötőt, amellyel a késési jellemzői a hálózati forgalom meghatározására.

Ha beállította az alkalmazásproxy-szolgáltatás, kérje meg a következő kérdéseket:

- Hol található a az alkalmazás?
- Hol találhatók a legtöbb olyan felhasználókkal, akik az alkalmazást?
- Hol található a az alkalmazásproxy-példány?
- Már rendelkezik, mint például az Azure ExpressRoute vagy egy hasonló VPN beállítása az Azure-adatközpontok dedikált hálózati kapcsolat?

Az összekötő rendelkezik az Azure és kommunikálni az alkalmazások (2. és a forgalmának folyamatábrája a 3. lépés), így az összekötő hatással van a elhelyezését ezen két kapcsolat késését. Ha kipróbálja az összekötő elhelyezését, vegye figyelembe a következő szempontokat:

- Ha azt szeretné, a Kerberos által korlátozott delegálás (KCD) használata egyszeri bejelentkezéshez, majd az összekötőjét egy adatközpontba üzemel. Emellett az összekötő kiszolgáló kell csatlakoznia kell.  
- Ha kétségei vannak, telepítse az közelebb az alkalmazáshoz.

### <a name="general-approach-to-minimize-latency"></a>A késés minimalizálása érdekében az általános megközelítés

Minimalizálhatja a késést, a végpontok közötti forgalom optimalizálásával minden hálózati kapcsolat. Minden kapcsolat optimalizálhatók:

- Csökkenti a Ugrás a két fél közötti távolság.
- A megfelelő hálózati gyermekelemeinek kiválasztása. Például a nyilvános interneten, hanem egy magánhálózaton áthaladó gyorsabb lehet, dedikált hivatkozások miatt.

Ha rendelkezik egy dedikált VPN vagy ExpressRoute hivatkozást az Azure és a vállalati hálózat között, érdemes azt használja.

## <a name="focus-your-optimization-strategy"></a>Összpontosítson az optimalizálási stratégia

Nincs kicsit vezérelheti a felhasználók és az alkalmazásproxy szolgáltatás közötti kapcsolat is van. A felhasználók otthoni hálózatból, kávézóból vagy más országból/régióból férhetnek hozzá az alkalmazásaihoz. Ehelyett optimalizálhatja az alkalmazásproxy-szolgáltatás kapcsolatot az alkalmazásproxy-összekötők az alkalmazásokra. Vegye fontolóra a következő minták a környezetben.

### <a name="pattern-1-put-the-connector-close-to-the-application"></a>1\. minta: Az közel az alkalmazás-összekötő elhelyezése

Az összekötő a célalkalmazás közelében helyezze az ügyfél hálózatán. Ez a konfiguráció minimálisra csökkenti a topográfia ábrán 3. lépés, mivel az összekötő és az alkalmazás bezárása.

Ha az összekötő kell a tartományvezérlőre egy üzemel, ez a minta azért előnyösebb. Legtöbb ügyfelünk akkor használja ezt a mintát, mivel a legtöbb forgatókönyvhöz jól működik. Ez a minta mintával 2 a szolgáltatás és az összekötő közötti forgalom optimalizálása érdekében is kombinálhatók.

### <a name="pattern-2-take-advantage-of-expressroute-with-microsoft-peering"></a>2\. minta: Kihasználhatja az ExpressRoute a Microsoft társviszony-létesítés

Ha az ExpressRoute állítsa be a Microsoft társviszony-létesítés, használhatja a gyorsabb ExpressRoute-kapcsolat Application Proxy és az összekötő közötti forgalom. Az összekötő továbbra is a hálózat, az alkalmazás közelében van.

### <a name="pattern-3-take-advantage-of-expressroute-with-private-peering"></a>3\. minta: Kihasználhatja az ExpressRoute privát társviszony-létesítést az

Ha rendelkezik egy dedikált VPN vagy az ExpressRoute állítsa be a magánhálózati társviszony-létesítés Azure és a vállalati hálózat között, hogy egy másik lehetőséget. Ebben a konfigurációban a virtuális hálózat az Azure-ban általában a vállalati hálózat kiterjesztése minősül. Így az összekötő telepítése az Azure-adatközpontban, és továbbra is megfelelnek a kis késés az összekötő-alkalmazás kapcsolat.

Késés nem sérült, mert adatforgalommal egy dedikált kapcsolaton keresztül. Továbbfejlesztett alkalmazásproxy-szolgáltatás-összekötő késés is, mert az összekötő telepítve van az Azure-adatközpontban az Azure AD-bérlő földrajzi közeli beolvasása.

![Összekötő telepítve van az Azure-adatközpont bemutató ábra.](./media/application-proxy-network-topology/application-proxy-expressroute-private.png)

### <a name="other-approaches"></a>Más megoldások

Bár ez a cikk a fókuszt összekötő elhelyezése, módosíthatja az alkalmazások jobb késési jellemzői elhelyezését is.

Szervezetek egyre, költöznek hálózataikat üzemeltetett környezetekben. Ez lehetővé teszi számukra, hogy alkalmazásaikat elhelyezni, amely része is vállalati hálózatában üzemeltetett környezetben, és továbbra is a tartományon belül kell. Ebben az esetben a minták a fenti szakaszokban ismertetett is alkalmazható az új alkalmazás helyét. Ha az Ön által választandó ezt a beállítást, [Azure AD tartományi szolgáltatások](../../active-directory-domain-services/overview.md).

Ezenkívül szervezhet az összekötőkkel [összekötőcsoportok](application-proxy-connector-groups.md) , a különböző helyeken és a hálózatok megcélzása az alkalmazásokkal.

## <a name="common-use-cases"></a>Gyakori használati helyzetek

Ez a szakasz ismerteti azokat a lépéseket néhány olyan gyakori helyzetet keresztül. Tegyük fel, amely az Azure AD-bérlő (és ezért az proxy szolgáltatás végpontja) található, az Egyesült Államok (US). A szempontok tárgyalt, ezek használt esetek is vonatkoznak más régiókban világszerte.

Ebben az esetben azt hívása minden kapcsolat egy "ugrást", és number azokat könnyebb leírásáért lásd:

- **1 Ugrás**: az alkalmazásproxy-szolgáltatás felhasználó
- **Ugrás a 2**: alkalmazásproxy-szolgáltatás és az alkalmazásproxy-összekötő
- **Ugrás a 3**: az alkalmazásproxy-összekötő a célalkalmazás 

### <a name="use-case-1"></a>Használja az 1. eset

**Forgatókönyv:** az alkalmazás nem rendelkező felhasználók ugyanabban a régióban az Egyesült Államokban, a szervezet hálózatban található. Nem az ExpressRoute VPN létezik vagy az Azure-adatközpontban és a vállalati hálózat között.

**Javaslat:** kövesse minta 1, az előző szakaszban ismertetett. Nagyobb késésű fontolja meg ExpressRoute segítségével, ha szükséges.

Ez az egy egyszerű minta. Ugrás 3 optimalizálása úgy, hogy az összekötő közel az alkalmazást. Ez azért is kézenfekvő lehet, mert az összekötő általában telepítve van a üzemel az alkalmazás és az Adatközpont KCD műveletek végrehajtásához.

![A felhasználókat, proxyt, összekötőt és alkalmazást bemutató diagram mind az Egyesült Államokban](./media/application-proxy-network-topology/application-proxy-pattern1.png)

### <a name="use-case-2"></a>Használja a 2. eset

**Forgatókönyv:** az alkalmazás nem az Egyesült Államokban, egy vállalati hálózaton lévő felhasználókkal globálisan terjednek. Nem az ExpressRoute VPN létezik vagy az Azure-adatközpontban és a vállalati hálózat között.

**Javaslat:** kövesse minta 1, az előző szakaszban ismertetett.

Újra az egyik optimalizálása Ugrás 3, ahol elhelyezi az összekötő közel az alkalmazást. Ugrás 3 nem általában költséges, ha az összes ugyanazon a régión belül. Azonban 1 Ugrás lehet drágább, attól függően, ahol a felhasználó tagja, mivel a világ különböző kell férnie az alkalmazásproxy-példány az Egyesült Államokban. Fontos megjegyezni, hogy a proxy megoldással kapcsolatos folyamatban terjednek globálisan felhasználók hasonló jellemzőkkel rendelkezik.

![A felhasználók globálisan oszlanak el, de minden más az Egyesült Államokban](./media/application-proxy-network-topology/application-proxy-pattern2.png)

### <a name="use-case-3"></a>Használja a 3. eset

**Forgatókönyv:** az alkalmazás szerepel a szervezeti hálózatához az adatközpontjába. A Microsoft társviszony-létesítési ExpressRoute létezik az Azure és a vállalati hálózat között.

**Javaslat:** hajtsa végre az 1. és 2, az előző szakaszban ismertetett mintákat.

Először helyezze el az alkalmazást a lehető legközelebb az összekötőt. Ezt követően a rendszer automatikusan használja az ExpressRoute az ugrások 2.

Ha az ExpressRoute-kapcsolat a Microsoft társviszony-létesítés használ, a proxy- és az összekötő közötti forgalom tovább az hivatkozás. Ugrás 2 késés van optimalizálva.

![A proxy- és összekötő között ExpressRoute bemutató ábra.](./media/application-proxy-network-topology/application-proxy-pattern3.png)

### <a name="use-case-4"></a>Használati eset 4

**Forgatókönyv:** az alkalmazás szerepel a szervezeti hálózatához az adatközpontjába. ExpressRoute privát társviszony-létesítést az Azure és a vállalati hálózat között van.

**Javaslat:** kövesse minta 3, az előző szakaszban ismertetett.

Az összekötő jelölje be az Azure-adatközpontban, amely a vállalati hálózaton, ExpressRoute privát társviszony-létesítésen keresztül csatlakozik.

Az összekötő helyezhető az Azure-adatközpontba. Mivel az összekötő továbbra is rendelkezik egy, az alkalmazás és az adatközpontban a magánhálózaton keresztül üzemel, Ugrás 3 optimalizált marad. Emellett Ugrás 2 további van optimalizálva.

![Összekötő az Azure-adatközpontban, ExpressRoute az összekötő és az alkalmazás között](./media/application-proxy-network-topology/application-proxy-pattern4.png)

### <a name="use-case-5"></a>Használati eset 5

**Forgatókönyv:** az alkalmazás szerepel a szervezeti hálózatához az Európai Unió, az alkalmazásproxy-példány és a legtöbb felhasználó az Egyesült Államokban.

**Javaslat:** az összekötő az alkalmazás közelében helyezze el. USA-beli felhasználók elérő történik, ugyanabban a régióban kell Application Proxy példányát, mert 1 Ugrás nem túl drága. Ugrás 3 optimalizáltuk. Fontolja meg az ExpressRoute használatát Ugrás 2 optimalizálása érdekében.

![Ábrán láthatók a felhasználók és a proxyk az USA-ban, az összekötőben és az alkalmazásban az Unióban](./media/application-proxy-network-topology/application-proxy-pattern5b.png)

Ebben az esetben egy másik változatot használatával is használni. Ha legtöbb a szervezeten belüli felhasználók az Egyesült Államokban, akkor esélyét, hogy a hálózat, valamint az Egyesült Államok terjed. Az összekötő helyezze az Egyesült Államokban, és használja a dedikált belső vállalati hálózathoz sort az alkalmazáshoz, az Európai Unión. E módon ugrások 2. és 3 vannak optimalizálva.

![Ábrán láthatók a felhasználók, a proxyk és az összekötők az Egyesült Államokban, az EU-ban](./media/application-proxy-network-topology/application-proxy-pattern5c.png)

## <a name="next-steps"></a>Következő lépések

- [Alkalmazásproxy engedélyezése](application-proxy-add-on-premises-application.md)
- [Egyszeri bejelentkezés engedélyezése](application-proxy-configure-single-sign-on-with-kcd.md)
- [Feltételes hozzáférés engedélyezése](application-proxy-integrate-with-sharepoint-server.md)
- [Problémák merültek fel az alkalmazásproxy használatával](application-proxy-troubleshoot.md)
