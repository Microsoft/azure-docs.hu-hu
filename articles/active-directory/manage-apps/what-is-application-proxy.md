---
title: Helyszíni alkalmazások közzététele az Azure AD Application Proxy
description: Ismerje meg, hogy miért érdemes az alkalmazásproxy használatával a helyszíni webalkalmazásokat külsőleg közzétenni a távoli felhasználók számára. Ismerje meg az alkalmazásproxy architektúráját, az összekötőket, a hitelesítési módszereket és a biztonsági előnyöket.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.date: 12/31/2020
ms.author: kenwith
ms.reviewer: japere
ms.openlocfilehash: 1569e577ec535a4680cfaec6f7975c056275ecbe
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/02/2021
ms.locfileid: "99258778"
---
# <a name="using-azure-ad-application-proxy-to-publish-on-premises-apps-for-remote-users"></a>Helyszíni alkalmazások közzététele az Azure AD Application Proxy használatával távoli felhasználók számára

Azure Active Directory (Azure AD) számos funkciót kínál a felhasználók, alkalmazások és adatok felhőben és a helyszínen történő védelméhez. Az Azure AD Application Proxy funkcióját olyan informatikai szakemberek is tudják megvalósítani, akik külsőleg szeretnék közzétenni a helyszíni webalkalmazásokat. Azok a távoli felhasználók, akiknek szükségük van a belső alkalmazásokhoz való hozzáférésre, biztonságos módon férhetnek hozzájuk.

A belső alkalmazások a hálózaton kívülről való biztonságos elérésének lehetősége még fontosabbá válik a modern munkahelyen. Az olyan forgatókönyvekkel, mint a BYOD (saját eszközök használata) és a mobileszközök, az informatikai szakemberek kihívást jelentenek két cél teljesítéséhez:

* A végfelhasználók bármikor és bárhonnan dolgozhatnak
* A céges eszközök védelmének biztosítása mindig

Számos szervezet azt feltételezi, hogy felügyelet alatt állnak, és védettek, ha az erőforrások a vállalati hálózatok határain belül vannak. A mai digitális munkahelyen azonban ez a határ a felügyelt mobileszközök és a Felhőbeli erőforrások és szolgáltatások terén bővült. Most már kezelni kell a felhasználói identitások és az eszközökön és alkalmazásokban tárolt adatok védelmének összetettségét.

Lehet, hogy már használja az Azure AD-t a felhőben lévő azon felhasználók felügyeletéhez, akiknek Microsoft 365 és más SaaS-alkalmazásokhoz, valamint a helyszínen üzemeltetett webalkalmazásokhoz is hozzá kell férniük. Ha már rendelkezik az Azure AD-vel, egyetlen ellenőrzési síkon használhatja azt, hogy zökkenőmentes és biztonságos hozzáférést biztosítson a helyszíni alkalmazásokhoz. Vagy lehet, hogy továbbra is tervezi a felhőbe való áttérést. Ha igen, megkezdheti a felhőbe való utazást az alkalmazásproxy bevezetésével és az első lépéssel egy erős identitás-alaprendszer kiépítése felé.

Az alábbi lista néhány olyan dolgot mutat be, amely lehetővé teszi, hogy az App proxyt egy hibrid, párhuzamosan használható forgatókönyvben implementálja:

* Helyszíni webalkalmazások közzététele egy egyszerűsített módon, DMZ nélkül
* Egyszeri bejelentkezés (SSO) támogatása a felhőben és a helyszínen lévő összes eszközön, erőforráson és alkalmazáson keresztül
* A többtényezős hitelesítés támogatása a felhőben és a helyszínen lévő alkalmazásokhoz
* A felhő szolgáltatásainak gyors kihasználása a Microsoft Cloud biztonságával
* A felhasználói fiókok felügyeletének központosítása
* Az identitás és a biztonság központosított kezelése
* Alkalmazásokhoz való felhasználói hozzáférés automatikus hozzáadása vagy eltávolítása a csoporttagság alapján

Ez a cikk azt ismerteti, hogy az Azure AD és az Application proxy hogyan nyújt távoli felhasználók számára egyszeri bejelentkezéses (SSO) élményt. A felhasználók VPN-vagy kétkiszolgálós kiszolgálók és tűzfalszabályok nélkül biztonságosan csatlakozhatnak a helyszíni alkalmazásokhoz. Ebből a cikkből megtudhatja, hogy az alkalmazásproxy hogyan teszi lehetővé a felhő képességeit és biztonsági előnyeit a helyszíni webalkalmazásokhoz. Emellett leírja a lehetséges architektúrát és topológiákat is.

## <a name="remote-access-in-the-past"></a>Távoli hozzáférés a múltban

Korábban a vezérlési sík védi a támadók belső erőforrásait, miközben a távoli felhasználók hozzáférésének megkönnyítése mind a DMZ, mind a peremhálózaton volt. A DMZ-ben üzembe helyezett VPN-és fordított proxy-megoldások azonban a külső ügyfelek által a vállalati erőforrásokhoz való hozzáféréshez nem alkalmasak a felhő világába. Általában a következő hátrányokból származnak:

* Hardverköltségek
* Biztonság fenntartása (javítások, figyelési portok stb.)
* Felhasználók hitelesítése az Edge-ben
* Felhasználók hitelesítése a peremhálózaton lévő webkiszolgálókon
* A VPN-hozzáférés megőrzése a távoli felhasználók számára a VPN-ügyfélszoftver elosztásával és konfigurálásával. Emellett a DMZ-ben a tartományhoz csatlakoztatott kiszolgálók is megmaradnak, amelyek sebezhetőek lehetnek a külső támadásokkal szemben.

Napjaink Felhőbeli első világában az Azure AD a legmegfelelőbben szabályozza, hogy ki és mi jut a hálózatra. Az Azure AD Application Proxy integrálható a modern hitelesítéssel és a felhőalapú technológiákkal, például az SaaS-alkalmazásokkal és az identitás-szolgáltatókkal. Ez az integráció lehetővé teszi a felhasználók számára, hogy bárhonnan hozzáférjenek az alkalmazásokhoz. A mai digitális munkahely esetében nem csupán az App proxy, hanem biztonságosabb, mint a VPN-és fordított proxy-megoldások, és egyszerűbben valósítható meg. A távoli felhasználók ugyanúgy férhetnek hozzá a helyszíni alkalmazásokhoz, mint a Microsoft és az Azure AD-vel integrált SaaS-alkalmazások. Nem szükséges módosítani vagy frissíteni az alkalmazásokat ahhoz, hogy használhatók legyenek az alkalmazásproxyval. Az App proxy továbbá nem igényli a bejövő kapcsolatok megnyitását a tűzfalon keresztül. Az App proxyval egyszerűen beállíthatja és elfelejtheti.

## <a name="the-future-of-remote-access"></a>A távelérés jövője

A mai digitális munkahelyen a felhasználók bárhol dolgozhatnak több eszközzel és alkalmazással. Az egyetlen állandó a felhasználói identitás. Éppen ezért az első lépés egy biztonságos hálózatra, hogy az [Azure ad Identity Management](../../security/fundamentals/identity-management-overview.md) képességeit használja a biztonsági vezérlő síkjaként. Az identitást használó modell általában a következő összetevőkből áll:

* Egy identitás-szolgáltató, amellyel nyomon követheti a felhasználókat és a felhasználóval kapcsolatos információkat.
* Az eszköz könyvtára a vállalati erőforrásokhoz hozzáférő eszközök listájának fenntartásához. Ez a könyvtár tartalmazza a megfelelő eszköz adatait (például az eszköz típusát, az integritást stb.).
* Házirend-értékelési szolgáltatás annak megállapításához, hogy a felhasználó és az eszköz megfelel-e a biztonsági rendszergazdák által beállított szabályzatnak.
* A szervezeti erőforrásokhoz való hozzáférés megadásának vagy megtagadásának lehetősége.

Az Application proxy segítségével az Azure AD nyomon követi azokat a felhasználókat, akiknek a helyszíni és a felhőben közzétett webalkalmazásokhoz kell hozzáférnie. Központi felügyeleti pontot biztosít az alkalmazások számára. Habár nem kötelező, javasoljuk, hogy engedélyezze az Azure AD feltételes hozzáférését is. A felhasználók hitelesítésére és elérésére vonatkozó feltételek meghatározásával biztosíthatja, hogy a megfelelő személyek hozzáférjenek az alkalmazásokhoz.

**Megjegyzés:** Fontos tisztában lenni azzal, hogy az Azure AD Application Proxy a belső erőforrásokhoz való hozzáférést igénylő barangolási (vagy távoli) felhasználók számára VPN-vagy fordított proxy-cserére szolgál. Nem a vállalati hálózat belső felhasználói számára készült. Azok a belső felhasználók, akik szükségtelenül használják az alkalmazásproxy-t, váratlan és nemkívánatos teljesítménnyel kapcsolatos problémákat okozhatnak.

![Azure Active Directory és az összes alkalmazás](media/what-is-application-proxy/azure-ad-and-all-your-apps.png)

### <a name="an-overview-of-how-app-proxy-works"></a>Az App proxy működésének áttekintése

Az alkalmazásproxy egy Azure AD-szolgáltatás, amelyet a Azure Portal konfigurál. Lehetővé teszi egy külső nyilvános HTTP/HTTPS URL-végpont közzétételét az Azure-felhőben, amely egy belső alkalmazáskiszolgáló URL-címéhez csatlakozik a szervezetében. Ezek a helyszíni webalkalmazások az Azure AD-vel integrálhatók az egyszeri bejelentkezés támogatásához. A végfelhasználók a helyi webalkalmazásokhoz ugyanúgy férhetnek hozzá, mint a Microsoft 365 és más SaaS-alkalmazásokhoz.

A szolgáltatás összetevői közé tartozik a felhőben futtatott alkalmazásproxy-összekötő, amely egy egyszerűsített ügynök, amely egy helyszíni kiszolgálón, az Azure AD-ben pedig az identitás-szolgáltatón fut. Mindhárom összetevő együttműködik, hogy a felhasználó számára egyszeri bejelentkezést biztosítson a helyszíni webalkalmazásokhoz való hozzáféréshez.

A bejelentkezést követően a külső felhasználók ismerős URL-cím vagy [saját alkalmazások](../user-help/my-apps-portal-end-user-access.md) segítségével érhetik el a helyszíni webalkalmazásokat az asztali vagy iOS-vagy Mac-eszközökről. Az alkalmazásproxy például távoli hozzáférést és egyszeri bejelentkezést biztosíthat a Távoli asztal, a SharePoint-webhelyek, a tabló, a Qlik, az Outlook és az üzletági (LOB) alkalmazások számára.

![Azure AD Application Proxy-architektúra](media/what-is-application-proxy/azure-ad-application-proxy-architecture.png)

### <a name="authentication"></a>Hitelesítés

Több módon is konfigurálhat egy alkalmazást az egyszeri bejelentkezéshez, és a kiválasztott módszer az alkalmazás által használt hitelesítéstől függ. Az Application proxy a következő típusú alkalmazásokat támogatja:

* Webalkalmazások
* Webes API-k, amelyeket a különböző eszközökön lévő gazdag alkalmazások számára kíván tenni
* Egy Távoli asztali átjáró mögött futó alkalmazások
* A [Microsoft Authentication Library (MSAL)](../develop/v2-overview.md) szolgáltatással integrált, gazdag alkalmazások

Az App proxy a következő natív hitelesítési protokollt használó alkalmazásokkal működik:

* **[Integrált Windows-hitelesítés (IWA)](application-proxy-configure-single-sign-on-with-kcd.md).** A IWA esetében az alkalmazásproxy-összekötők a Kerberos által korlátozott delegálás (KCD) használatával hitelesítik a felhasználókat a Kerberos-alkalmazásban.

Az App proxy a következő hitelesítési protokollokat is támogatja harmadik féltől származó integrációval vagy adott konfigurációs helyzetekben:

* [**Fejléc-alapú hitelesítés**](./application-proxy-configure-single-sign-on-with-headers.md). Ez a bejelentkezési módszer egy PingAccess nevű harmadik féltől származó hitelesítési szolgáltatást használ, amely akkor használatos, amikor az alkalmazás fejléceket használ a hitelesítéshez. Ebben az esetben a hitelesítést a PingAccess kezeli.
* [**Űrlap-vagy jelszó-alapú hitelesítés**](application-proxy-configure-single-sign-on-password-vaulting.md). Ezzel a hitelesítési módszerrel a felhasználók először jelentkeznek be az alkalmazásba egy felhasználónévvel és jelszóval az első alkalommal, amikor hozzáférnek hozzá. Az első bejelentkezés után az Azure AD megadja a felhasználónevet és a jelszót az alkalmazáshoz. Ebben az esetben a hitelesítést az Azure AD kezeli.
* [**SAML-hitelesítés**](application-proxy-configure-single-sign-on-on-premises-apps.md). Az SAML-alapú egyszeri bejelentkezés az SAML 2,0 vagy WS-Federation protokollt használó alkalmazások esetében támogatott. Az SAML egyszeri bejelentkezéssel az Azure AD hitelesíti az alkalmazást a felhasználó Azure AD-fiókjának használatával.

További információ a támogatott módszerekről: [egyszeri bejelentkezés módszerének kiválasztása](sso-options.md#choosing-a-single-sign-on-method).

### <a name="security-benefits"></a>Biztonsági előnyök

Az Application proxy és az Azure AD által kínált távelérési megoldás számos biztonsági előnyt biztosít ügyfeleinknek, többek között a következőket:

* **Hitelesített hozzáférés**. Az alkalmazásproxy a legmegfelelőbb az alkalmazások [előzetes hitelesítéssel](application-proxy-security.md#authenticated-access) történő közzétételéhez, így biztosítva, hogy csak a hitelesített kapcsolatok elérje a hálózatot. Az előhitelesítéssel közzétett alkalmazások esetében nem engedélyezett a forgalom továbbítása az App proxy szolgáltatáson keresztül a helyszíni környezetbe, érvényes jogkivonat nélkül. Az előhitelesítés jellegéből adódóan blokkolja a megtámadott támadások jelentős számát, mivel csak a hitelesített identitások férhetnek hozzá a háttérbeli alkalmazáshoz.
* **Feltételes hozzáférés**. A gazdagabb házirend-vezérlők a hálózathoz való csatlakozás előtt alkalmazhatók. A feltételes hozzáféréssel korlátozásokat határozhat meg a háttérbeli alkalmazáshoz engedélyezett forgalomra vonatkozóan. A bejelentkezéseket a hely, a hitelesítés erőssége és a felhasználói kockázati profil alapján korlátozó házirendek hozhatók létre. A feltételes hozzáférés fejlődése során további vezérlők kerülnek hozzáadásra, amelyek további biztonságot, például Microsoft Cloud App Security (MCAS) való integrációt tesznek lehetővé. A MCAS-integráció lehetővé teszi a helyszíni alkalmazások [valós idejű figyelését](application-proxy-integrate-with-microsoft-cloud-application-security.md) a feltételes hozzáférés monitorozásához és vezérléséhez a feltételes hozzáférési házirendek alapján valós időben.
* **Forgalom megszakítása**. A háttér-alkalmazásba irányuló összes forgalom a felhőben lévő alkalmazásproxy-szolgáltatásban megszűnik, miközben a munkamenet újra létrejön a háttér-kiszolgálóval. Ez a kapcsolódási stratégia azt jelenti, hogy a háttér-kiszolgálók nem teszik közzé a HTTP-forgalom közvetlen elérését. Hatékonyabb védelmet biztosítanak a megcélozt DoS (szolgáltatásmegtagadási) támadásokkal szemben, mert a tűzfal nem támadás alatt áll.
* **Minden hozzáférés kimenő**. Az alkalmazásproxy-összekötők csak a felhőben lévő alkalmazásproxy szolgáltatáshoz, a 80-as és a 443-es porton keresztül csatlakoznak a kimenő kapcsolatokhoz. Bejövő kapcsolatok nélkül nincs szükség a tűzfal portjainak megnyitására a DMZ bejövő kapcsolataihoz vagy összetevőihez. Minden kapcsolat kimenő és biztonságos csatornán keresztül történik.
* **Biztonsági elemzési és Machine learning (ml) alapú intelligencia**. Mivel Azure Active Directory része, az alkalmazásproxy kihasználhatja [Azure ad Identity Protection](../identity-protection/overview-identity-protection.md) ( [prémium P2-licenc](https://azure.microsoft.com/pricing/details/active-directory/)szükséges). Azure AD Identity Protection egyesíti a gépi tanulási biztonsági intelligenciát a Microsoft [Digital Crimes Unit](https://news.microsoft.com/stories/cybercrime/index.html) és a [Microsoft Security Response Center](https://www.microsoft.com/msrc) adatcsatornái között, hogy proaktív módon azonosítsa a feltört fiókokat. Az Identity Protection valós idejű védelmet biztosít a magas kockázatú bejelentkezések esetében. Figyelembe veszi azokat a tényezőket, mint például a fertőzött eszközökről a anonimizálásával-hálózatokon keresztül való hozzáférés, illetve az atipikus és a nem valószínű helyekről a munkamenet kockázati profiljának növelésére. Ez a kockázati profil a valós idejű védelemhez használatos. Ezen jelentések és események közül sok már elérhető egy API-n keresztül az SIEM-rendszerekkel való integrációhoz.

* **Távoli elérés szolgáltatásként**. Nem kell aggódnia a helyszíni kiszolgálók karbantartásával és javításával a távoli hozzáférés engedélyezéséhez. Az alkalmazásproxy egy Internet-méretezési szolgáltatás, amelyet a Microsoft birtokol, így mindig a legújabb biztonsági javításokat és frissítéseket kapja meg. A nem javított szoftverek nagy számú támadás esetén is fiókok. A Belbiztonsági Minisztérium szerint a [megcélzó támadások](https://www.us-cert.gov/ncas/alerts/TA15-119A)közül több mint 85%-a megelőzhető. Ezzel a szolgáltatási modellel nem kell nagy terhet vállalni a peremhálózat-kiszolgálók kezeléséhez, és szükség szerint fel kell oldania a szükséges javításokat.

* **Intune-integráció**. Az Intune-nal a vállalati forgalom a személyes forgalomtól függetlenül irányítható. Az alkalmazásproxy biztosítja a vállalati forgalom hitelesítését. [Az alkalmazásproxy és a Intune Managed Browser](/intune/app-configuration-managed-browser#how-to-configure-application-proxy-settings-for-protected-browsers) funkció együttes használatával lehetővé teheti, hogy a távoli felhasználók biztonságosan hozzáférjenek a belső webhelyekhez az iOS-és Android-eszközökről.

### <a name="roadmap-to-the-cloud"></a>Ütemterv a felhőhöz

Az alkalmazásproxy megvalósításának egy másik jelentős előnye, hogy kiterjeszti az Azure AD-t a helyszíni környezetbe. Valójában az alkalmazásproxy megvalósítása a szervezet és az alkalmazások felhőbe való áthelyezésének egyik kulcsfontosságú lépése. Ha a felhőbe helyezi át, és a helyszíni hitelesítéstől távolabb van, csökkenti a helyszíni lábnyomot, és az Azure AD Identitáskezelés-felügyeleti képességeit használja a vezérlő síkjaként. A meglévő alkalmazások minimális vagy nem frissített frissítéseivel hozzáférhet a Felhőbeli képességekhez, például az egyszeri bejelentkezéshez, a többtényezős hitelesítéshez és a központi felügyelethez. A szükséges összetevők az App proxyba való telepítése egyszerű folyamat a távelérési keretrendszer létrehozásához. A felhőbe való áttéréssel hozzáférhet a legújabb Azure AD-funkciókhoz,-frissítésekhez és-funkciókhoz, például a magas rendelkezésre álláshoz és a vész-helyreállításhoz.

Ha többet szeretne megtudni az alkalmazások Azure AD-re való áttelepítéséről, olvassa el az [alkalmazások áttelepítése Azure Active Directory](https://aka.ms/migrateapps/whitepaper) tanulmányba című dokumentumot.

## <a name="architecture"></a>Architektúra

Az alábbi ábra azt szemlélteti, hogy az Azure AD Authentication Services és az alkalmazásproxy hogyan működik együtt, hogy egyszeri bejelentkezést biztosítson a helyi alkalmazásoknak a végfelhasználók számára.

![Azure AD Application Proxy hitelesítési folyamat](media/what-is-application-proxy/azure-ad-application-proxy-authentication-flow.png)

1. Ha a felhasználó egy végponton keresztül fér hozzá az alkalmazáshoz, a rendszer átirányítja a felhasználót az Azure AD bejelentkezési oldalára. Ha konfigurálta a feltételes hozzáférési szabályzatokat, a rendszer jelenleg ellenőrzi, hogy a szervezet biztonsági követelményeinek megfelelőek-e.
2. Sikeres bejelentkezés után az Azure AD jogkivonatot küld a felhasználó ügyfelének.
3. Az ügyfél elküldi a jogkivonatot az alkalmazásproxy szolgáltatásnak, amely lekéri az egyszerű felhasználónevet (UPN) és a rendszerbiztonsági tag nevét (SPN) a jogkivonatból.
4. Az alkalmazásproxy továbbítja a kérést, amelyet az alkalmazásproxy- [összekötő](application-proxy-connectors.md)is felvesz.
5. Az összekötő végrehajtja a felhasználó nevében szükséges további hitelesítést (a *hitelesítési módszertől függően választható*), kéri az alkalmazáskiszolgáló belső végpontját, és elküldi a kérést a helyszíni alkalmazásnak.
6. Az alkalmazáskiszolgáló válaszát az összekötőn keresztül küldik az alkalmazásproxy szolgáltatásnak.
7. A rendszer elküldi a választ az alkalmazásproxy szolgáltatástól a felhasználónak.

|**Összetevő**|**Leírás**|
|:-|:-|
|Végpont|A végpont egy URL-cím vagy egy [végfelhasználói portál](end-user-experiences.md). A felhasználók a hálózaton kívül érhetik el az alkalmazásokat egy külső URL-cím elérésével. A hálózaton belüli felhasználók egy URL-címen vagy egy végfelhasználói portálon keresztül érhetik el az alkalmazást. Ha a felhasználók ezen végpontok egyikén jelentkeznek, a hitelesítés az Azure AD-ben történik, majd az összekötőn keresztül a helyszíni alkalmazáshoz irányítja őket.|
|Azure AD|Az Azure AD a felhőben tárolt bérlői könyvtár használatával végzi a hitelesítést.|
|Alkalmazásproxy szolgáltatás|Ez az alkalmazásproxy-szolgáltatás a felhőben fut az Azure AD részeként. Továbbítja a bejelentkezési jogkivonatot a felhasználótól az alkalmazásproxy-Összekötőbe. Az alkalmazásproxy továbbítja az elérhető fejléceket a kérelemben, és a protokoll szerint állítja be a fejléceket az ügyfél IP-címére. Ha a proxy felé irányuló bejövő kérelem már tartalmazza ezt a fejlécet, a rendszer hozzáadja az ügyfél IP-címét a vesszővel tagolt lista végéhez, amely a fejléc értéke.|
|Alkalmazásproxy-összekötő|Az összekötő egy egyszerűsített ügynök, amely egy Windows Serveren fut a hálózaton belül. Az összekötő kezeli a felhőben és a helyszíni alkalmazásban futó alkalmazásproxy szolgáltatás közötti kommunikációt. Az összekötő csak kimenő kapcsolatokat használ, így nem kell megnyitnia a bejövő portokat, vagy semmit sem kell tennie a DMZ-ben. Az összekötők állapot nélküliek, és szükség szerint lekérik az adatokat a felhőből. Az összekötők, például a terheléselosztás és a hitelesítés módjával kapcsolatos további információkért lásd: az [Azure ad Application proxy-összekötők ismertetése](application-proxy-connectors.md).|
|Active Directory (AD)|Active Directory a tartományi fiókok hitelesítésének végrehajtásához a helyszínen fut. Ha az egyszeri bejelentkezés konfigurálva van, az összekötő az AD-vel kommunikálva további hitelesítést hajt végre.|
|Helyszíni alkalmazás|Végül a felhasználó hozzáférhet egy helyszíni alkalmazáshoz.|

Az Azure AD Application Proxy a felhőalapú alkalmazásproxy-szolgáltatásból és egy helyszíni összekötőből áll. Az összekötő figyeli az alkalmazásproxy szolgáltatástól érkező kéréseket, és kezeli a belső alkalmazásokhoz való kapcsolódást. Fontos megjegyezni, hogy minden kommunikáció a TLS-kapcsolaton keresztül történik, és mindig az alkalmazásproxy szolgáltatáshoz tartozó összekötőből származik. Ez a kommunikáció csak kimenő. Az összekötő egy ügyféltanúsítványt használ az alkalmazásproxy szolgáltatásba való hitelesítéshez az összes híváshoz. A kapcsolat biztonságának egyetlen kivétele az a kezdeti telepítési lépés, ahol az ügyféltanúsítvány létrejött. További részletekért tekintse meg a [motorháztető alatt](application-proxy-security.md#under-the-hood) található alkalmazásproxy című témakört.

### <a name="application-proxy-connectors"></a>Alkalmazásproxy-összekötők

Az [alkalmazásproxy-összekötők](application-proxy-connectors.md) a helyszínen üzembe helyezett, a felhőben található alkalmazásproxy-szolgáltatás felé irányuló kimenő kapcsolatok megkönnyítését lehetővé tevő egyszerűsített ügynökök. Az összekötőket olyan Windows-kiszolgálóra kell telepíteni, amely hozzáfér a háttérbeli alkalmazáshoz. A felhasználók az App proxy Cloud Service-szolgáltatáshoz csatlakoznak, amely az alábbi ábrán látható összekötőn keresztül irányítja át a forgalmat az alkalmazásokba.

![Azure AD Application Proxy hálózati kapcsolatok](media/what-is-application-proxy/azure-ad-application-proxy-network-connections.png)

Az összekötő és az App proxy szolgáltatás közötti beállítás és regisztráció a következőképpen történik:

1. A rendszergazda megnyitja a 80-es és a 443-as portot a kimenő forgalom számára, és lehetővé teszi az összekötő, az App proxy szolgáltatás és az Azure AD által igényelt számos URL-cím elérését.
2. A rendszergazda bejelentkezik a Azure Portalba, és egy végrehajtható fájl futtatásával telepíti az összekötőt egy helyszíni Windows Serverre.
3. Az összekötő megkezdi az App proxy szolgáltatás "figyelését".
4. A rendszergazda hozzáadja a helyszíni alkalmazást az Azure AD-hez, és konfigurálja azokat a beállításokat, mint például az URL-felhasználóknak csatlakozniuk kell az alkalmazásaihoz.

További információ: [Azure ad Application proxy üzemelő példány megtervezése](application-proxy-deployment-plan.md).

Javasoljuk, hogy mindig helyezzen üzembe több összekötőt a redundancia és a skálázás érdekében. Az összekötők, a szolgáltatással együtt, gondoskodnak az összes magas rendelkezésre állású feladatról, és dinamikusan hozzáadhatók vagy eltávolíthatók. Minden alkalommal, amikor új kérés érkezik, a rendszer átirányítja az egyik elérhető összekötőhöz. Amikor egy összekötő fut, aktív marad, ahogy csatlakozik a szolgáltatáshoz. Ha egy összekötő átmenetileg nem érhető el, nem válaszol erre a forgalomra. A fel nem használt összekötők inaktívként vannak megjelölve, és 10 napos inaktivitás után törlődnek.

Az összekötők is lekérdezik a kiszolgálót, hogy megtudja, van-e újabb verziója az összekötőnek. Bár manuális frissítést is végezhet, az összekötők automatikusan frissülnek, amíg az alkalmazásproxy Connector Updater szolgáltatás fut. Több összekötővel rendelkező bérlők esetében az automatikus frissítés az egyes csoportokban egyszerre egy összekötőt céloz meg, hogy megakadályozza az állásidőt a környezetben.

**Megjegyzés**: az alkalmazás-proxy [verzió előzményei lapot](application-proxy-release-version-history.md) az RSS-hírcsatornára való feliratkozással is követheti.

Minden alkalmazásproxy-összekötő hozzá van rendelve egy [összekötő-csoporthoz](application-proxy-connector-groups.md). Az azonos összekötő csoportba tartozó összekötők a magas rendelkezésre állás és a terheléselosztás érdekében egyetlen egységként működnek. Létrehozhat új csoportokat, összekötőket rendelhet hozzájuk a Azure Portal, majd meghatározott összekötőket rendelhet hozzájuk az adott alkalmazások kiszolgálásához. A magas rendelkezésre állás érdekében ajánlott legalább két összekötőt használni az egyes összekötők csoportjához.

Az összekötő-csoportok akkor hasznosak, ha a következő helyzetekben kell támogatást biztosítani:

* Földrajzi alkalmazás közzététele
* Alkalmazás szegmentálása/elkülönítése
* A felhőben vagy a helyszínen futó webalkalmazások közzététele

További információ az összekötők telepítésének és a hálózat optimalizálásának kiválasztásáról: [hálózati topológia szempontjai Azure Active Directory Application proxy használatakor](application-proxy-network-topology.md).

## <a name="other-use-cases"></a>Egyéb használati esetek

Ezen a ponton az Application proxy használatával a helyszíni alkalmazások külsőleg is közzétehető, miközben az egyszeri bejelentkezés az összes Felhőbeli és helyszíni alkalmazásra engedélyezve van. Azonban léteznek más használati esetek is az App proxy számára, amely érdemes megemlíteni. Ezek közé tartoznak például az alábbiak:

* **REST API-k biztonságos közzététele**. Ha a helyszínen vagy a felhőben futó virtuális gépeken üzemeltetett üzleti logikával vagy API-kkal rendelkezik, az Application proxy nyilvános végpontot biztosít API-hozzáféréshez. Az API Endpoint Access lehetővé teszi a hitelesítés és az engedélyezés vezérlését a bejövő portok megkövetelése nélkül. További biztonságot nyújt olyan prémium szintű Azure AD-funkciókon keresztül, mint például a többtényezős hitelesítés és az eszköz-alapú feltételes hozzáférés asztali számítógépekhez, iOS-, MAC-és Android-eszközökhöz az Intune használatával. További információ: [a natív ügyfélalkalmazások engedélyezése a](application-proxy-configure-native-client-application.md) OAuth és az API-k védelemmel való ellátásához a [2,0-as Azure Active Directory és API Management használatával](../../api-management/api-management-howto-protect-backend-with-aad.md).
* **Távoli asztali szolgáltatások** **(RDS)**. A standard szintű RDS központi telepítések nyitott bejövő kapcsolatokat igényelnek. A [Távoli asztali környezet és az alkalmazásproxy üzembe helyezése](application-proxy-integrate-with-remote-desktop-services.md) azonban állandó kimenő kapcsolatban áll az összekötő szolgáltatást futtató kiszolgálóval. Így több alkalmazást is biztosíthat a végfelhasználók számára a helyszíni alkalmazások Távoli asztali szolgáltatások használatával történő közzétételével. Emellett csökkentheti az üzemelő példány támadási felületét is, és a kétlépéses ellenőrzés és a feltételes hozzáférés-vezérlés korlátozott készletét használhatja az RDS-hez.
* **WebSockets használatával csatlakozó alkalmazások közzététele**. A [Qlik Sense](application-proxy-qlik.md) támogatás nyilvános előzetes verzióban érhető el, és a jövőben más alkalmazásokra is kiterjed.
* **Natív ügyfélalkalmazások engedélyezése a proxy alkalmazásokkal való kommunikációhoz**. Az Azure AD Application Proxy webalkalmazások közzétételére is használható, de az Azure AD Authentication Library (ADAL) szolgáltatással konfigurált [natív ügyfélalkalmazások](application-proxy-configure-native-client-application.md) közzétételére is használható. A natív ügyfélalkalmazások eltérnek a webalkalmazások, mert azok egy eszközre vannak telepítve, míg a Web Apps böngészőn keresztül érhető el.

## <a name="conclusion"></a>Összegzés

A munkánk és az általunk használt eszközök gyorsan változnak. Ha több alkalmazott dolgozik a saját eszközein, és a szolgáltatott szoftveres (SaaS) alkalmazások átható használatát, a szervezeteknek az adatkezelést és az adatvédelmet is ki kell alakítani. A vállalatok már nem működnek kizárólag saját falain belül, és egy olyan árok védi őket, amely körülveszi a szegélyét. Az adattovábbítás több helyre, mint valaha – a helyszíni és a felhőalapú környezetek között. Ez az evolúció hozzájárult a felhasználók termelékenységének növeléséhez és a közös együttműködéshez, ugyanakkor nagyobb kihívást jelent a bizalmas adatok védelme is.

Függetlenül attól, hogy az Azure AD-t használja-e egy hibrid párhuzamos forgatókönyvben lévő felhasználók kezelésére, vagy érdekli a felhőbe való utazás megkezdése, az Azure-AD Application Proxy megvalósítása a távoli hozzáférés szolgáltatásként történő biztosításával csökkentheti a helyszíni lábnyom méretét.

A szervezeteknek a következő előnyök kihasználásával kell megkezdeniük az App proxy előnyeit:

* Helyszíni alkalmazások közzététele külsőleg, a hagyományos VPN-vagy más helyszíni webes közzétételi megoldások és a DMZ megközelítésének fenntartása nélkül
* Egyszeri bejelentkezés az összes alkalmazásba, legyen az Microsoft 365 vagy más SaaS-alkalmazás, beleértve a helyszíni alkalmazásokat is
* A felhőalapú méretezés biztonsága, ahol az Azure AD kihasználja Microsoft 365 telemetria a jogosulatlan hozzáférés megakadályozása érdekében
* Intune-integráció a vállalati forgalom hitelesítésének biztosításához
* A felhasználói fiókok kezelésének központosítása
* Automatikus frissítések a legújabb biztonsági javítások biztosításához
* Új funkciók, mint azok kiadása; a legutóbbi támogatás az SAML egyszeri bejelentkezéshez és az alkalmazás-cookie-k részletesebb kezeléséhez

## <a name="next-steps"></a>Következő lépések

* Az Azure AD Application Proxy tervezésével, működésével és kezelésével kapcsolatos információkért lásd: Azure-beli [ad Application proxy üzembe helyezésének tervezése](application-proxy-deployment-plan.md).
* Egy élő bemutató beléptetéséhez vagy az ingyenes 90 napos próbaverzió kiértékeléséhez tekintse meg a [Enterprise Mobility + Security első lépéseivel foglalkozó](https://www.microsoft.com/cloud-platform/enterprise-mobility-security-trial)témakört.
