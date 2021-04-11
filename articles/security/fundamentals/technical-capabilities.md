---
title: Biztonsági technikai képességek az Azure-ban – Microsoft Azure
description: Az Azure-beli Security Services bemutatása, amely segít a felhőben tárolt adatvédelemben, erőforrásokban és alkalmazásokban.
services: security
author: TerryLanfear
manager: rkarlin
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.date: 02/04/2021
ms.author: terrylan
ms.openlocfilehash: 54be25317717bcf98b9e296d5c9ed59643cd334e
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105044753"
---
# <a name="azure-security-technical-capabilities"></a>Az Azure biztonságtechnikai képességei
Ez a cikk az Azure-beli biztonsági szolgáltatások bevezetését ismerteti, amelyek segítségével megvédheti adatait, erőforrásait és alkalmazásait a felhőben, és teljesítheti a vállalata biztonsági igényeit.

## <a name="azure-platform"></a>Azure-platform

A [Microsoft Azure](https://azure.microsoft.com/overview/what-is-azure/) egy infrastruktúra-és alkalmazás-szolgáltatásokból álló felhőalapú platform, amely az integrált adatszolgáltatások és a fejlett analitikai szolgáltatások, valamint a Microsoft nyilvános Felhőbeli adatközpontjai által üzemeltetett fejlesztői eszközök és szolgáltatások számára elérhető. Az Azure számos különböző kapacitáshoz és forgatókönyvhöz, az alapszintű számítási, hálózatkezelési és tárolási, mobil-és webalkalmazás-szolgáltatásokhoz, a teljes Felhőbeli forgatókönyvekhez, például a eszközök internetes hálózatahoz, valamint a nyílt forráskódú technológiákkal, valamint a hibrid felhőbe vagy az ügyfél adatközpontjában üzemeltetett környezetekhez használható. Az Azure olyan építőelemeket biztosít a felhőalapú technológiák számára, amelyek segítségével a vállalatok proaktív módon tudják megtakarítani a költségeket, az innovációt és a rendszereket. Ha egy felhőalapú szolgáltatóra épít, vagy áttelepíti az IT-eszközöket, az adott szervezet képességei az alkalmazások és az adatok védelmére szolgálnak a szolgáltatásokkal és a felhőalapú eszközök biztonságának kezeléséhez szükséges vezérlőkkel.

A Microsoft Azure az egyetlen olyan felhőalapú számítástechnikai szolgáltató, amely biztonságos, konzisztens alkalmazás-platformot és infrastruktúra-szolgáltatást biztosít a csapatok számára a különböző felhőalapú szakértelmével és a projekt bonyolultságának szintjein belüli munkához. az olyan integrált adatszolgáltatásokkal és elemzésekkel, amelyek a Microsofttól és a nem Microsofttól származó platformokon, nyílt keretrendszereken és eszközökön egyaránt felfedik az adatok felderítését, és lehetővé teszik a felhő helyszíni integrálását a helyszíni adatközpontokban, valamint az Azure Cloud Services üzembe helyezését. A Microsoft megbízható felhő részeként az ügyfelek az Azure-ra támaszkodnak az iparág piacvezető biztonsága, megbízhatósága, megfelelősége, védelme, valamint a Felhőbeli szervezetek támogatásához szükséges, a felhasználók, partnerek és folyamatok nagy hálózata számára.

A Microsoft Azure a következőket teheti:

- Gyorsítsa fel az innovációt a felhővel.

- Üzleti döntések & alkalmazások elemzésekkel.

- Hozzon létre szabadon és helyezzen üzembe bárhol.

- Üzleti védelemmel.

## <a name="security-technical-capabilities-to-fulfill-your-responsibility"></a>Biztonsági technikai képességek a felelősség teljesítése érdekében

Microsoft Azure olyan szolgáltatásokat nyújt, amelyek segítenek megfelelni a biztonsági, adatvédelmi és megfelelőségi igényeinek. A következő kép segít megmagyarázni a különböző Azure-szolgáltatásokat, amelyekkel az iparági szabványok alapján biztonságos és megfelelő alkalmazás-infrastruktúrát hozhat létre.

![Elérhető biztonsági technikai képességek – nagyméretű kép](./media/technical-capabilities/azure-security-technical-capabilities-fig1.png)

## <a name="manage-and-control-identity-and-user-access"></a>Identitás-és felhasználói hozzáférés kezelése és vezérlése

Az Azure a felhasználói identitások és a hitelesítő adatok kezeléséhez és a hozzáférés vezérléséhez nyújt segítséget az üzleti és a személyes adatok biztonságának biztosításához.

### <a name="azure-active-directory"></a>Azure Active Directory

A Microsoft identitás-és hozzáférés-kezelési megoldásai segítenek a vállalati adatközpontban és a felhőben lévő alkalmazásokhoz és erőforrásokhoz való hozzáférés védelmében, így további ellenőrzési szintek, például a többtényezős hitelesítés és a feltételes hozzáférési szabályzatok segítségével. A gyanús tevékenységek monitorozása speciális biztonsági jelentések kiadásán, naplózáson és riasztásokon keresztül valósul meg, így csökkenthető a potenciális biztonsági problémák kockázata. [Prémium szintű Azure Active Directory](../../active-directory/fundamentals/active-directory-whatis.md) egyszeri bejelentkezést tesz lehetővé több ezer felhőalapú alkalmazás számára, és hozzáférést biztosít a helyszínen futtatott webalkalmazásokhoz.

Az Azure Active Directory (Azure AD) biztonsági előnyei közé tartozik a következők lehetősége:

- A hibrid vállalat minden felhasználójához létrehozhat és kezelhet egy-egy identitást, így a felhasználók, csoportok és eszközök folyamatosan szinkronizálva lesznek.

- Egyszeri bejelentkezéses hozzáférést biztosíthat az alkalmazásokhoz, több ezer előre integrált SaaS-alkalmazáshoz.

- Szabályalapú többtényezős hitelesítés kikényszerítésével fokozhatja az alkalmazás-hozzáférés biztonságát a helyszíni és a felhőalkalmazásokban.

- Biztonságos távoli hozzáférés biztosítása helyszíni webalkalmazásokhoz az Azure AD Application Proxy használatával.

A [Azure Active Directory portál](https://aad.portal.azure.com/) a Azure Portal részeként érhető el. Ebből az irányítópultból áttekintheti a szervezet állapotát, és egyszerűen kezelheti a címtárat, a felhasználókat és az alkalmazás-hozzáférést.

![Azure Active Directory](./media/technical-capabilities/azure-security-technical-capabilities-fig2.png)

A következő alapvető Azure Identity Management-képességek:

- Egyszeri bejelentkezés

- Többtényezős hitelesítés

- Biztonsági monitorozás, riasztások és gépi tanuláson alapuló jelentések

- Felhasználói identitás és hozzáférés-kezelés

- Eszközregisztráció

- Privileged Identity Management

- Identity protection

#### <a name="single-sign-on"></a>Egyszeri bejelentkezés

Az [egyszeri bejelentkezés (SSO)](https://azure.microsoft.com/documentation/videos/overview-of-single-sign-on/) azt jelenti, hogy csak egyszer kell bejelentkeznie az összes olyan alkalmazáshoz és erőforráshoz, amelyre üzleti tevékenységet kell végeznie, csak egyszer, egyetlen felhasználói fiók használatával. A bejelentkezést követően az összes szükséges alkalmazáshoz hozzáférhet a hitelesítéshez (például egy jelszó megadásához).

Számos szervezet olyan szoftveres (SaaS) alkalmazásokra támaszkodik, mint például a Microsoft 365, a Box és a Salesforce a végfelhasználói hatékonyság érdekében. Az informatikai részlegnek az egyes SaaS-alkalmazásokban egyenként kell létrehoznia és frissítenie a felhasználói fiókokat, és a felhasználóknak minden SaaS-alkalmazáshoz meg kellett emlékezniük a jelszót.

[Az Azure ad kiterjeszti a helyszíni Active Directory a felhőbe](../../active-directory/manage-apps/what-is-single-sign-on.md), lehetővé téve a felhasználók számára, hogy az elsődleges szervezeti fiókját ne csak a tartományhoz csatlakoztatott eszközökre és a vállalati erőforrásokra jelentkezzen be, hanem a feladatokhoz szükséges összes webes és SaaS-alkalmazást is.

Nem csak a felhasználóknak nem kell több felhasználónevet és jelszót kezelnie, az alkalmazások hozzáférését a szervezeti csoportok és az alkalmazotti állapotuk alapján automatikusan kiépítheti vagy kiépítheti. Az [Azure ad olyan biztonsági és hozzáférési irányítási vezérlőket vezet be](../../active-directory/manage-apps/view-applications-portal.md) , amelyek segítségével központilag kezelheti a felhasználók hozzáférését az SaaS-alkalmazásokon keresztül.

#### <a name="multi-factor-authentication"></a>Többtényezős hitelesítés

Az [Azure AD multi-Factor Authentication (MFA)](../../active-directory/authentication/concept-mfa-howitworks.md) olyan hitelesítési módszer, amely több ellenőrzési módszer használatát igényli, és kritikus második biztonsági réteget ad hozzá a felhasználói bejelentkezésekhez és tranzakciókra. Az [MFA segít megvédeni](../../active-directory/authentication/concept-mfa-howitworks.md) az adathozzáférést az adatkezeléshez és az alkalmazásokhoz, miközben egy egyszerű bejelentkezési folyamatra vonatkozó felhasználói igényeket is kielégít. Erős hitelesítést tesz lehetővé számos ellenőrzési lehetőség – telefonhívás, szöveges üzenet vagy Mobile apps-értesítés, valamint ellenőrző kód és külső OAuth-tokenek – használatával.

#### <a name="security-monitoring-alerts-and-machine-learning-based-reports"></a>Biztonsági monitorozás, riasztások és gépi tanuláson alapuló jelentések

A biztonság monitorozása és a riasztások, valamint a nem konzisztens hozzáférési mintákat azonosító gépi tanuláson alapuló jelentések segíthetnek a vállalata védelmében. A Azure Active Directory hozzáférési és használati jelentéseivel betekintést nyerhet a szervezete címtárának integritására és biztonságára. Ezen információk alapján a címtár-rendszergazdák jobban meghatározhatják, hogy hol lehetnek a biztonsági kockázatok, hogy megfelelően megtervezzék a kockázatok enyhítését.

A Azure Portal vagy a [Azure Active Directory portálon](https://aad.portal.azure.com/)a [jelentések](../../active-directory/reports-monitoring/overview-reports.md) a következő módokon vannak kategorizálva:

- Anomália-jelentések – azokat a bejelentkezési eseményeket tartalmazza, amelyeket a rendszer rendellenesnek talált. Célunk, hogy tisztában legyenek az ilyen tevékenységekkel, és lehetővé tesszük, hogy el tudja dönteni, hogy az esemény gyanús-e.

- Integrált alkalmazások jelentései – betekintést nyújt a Felhőbeli alkalmazások használatára a szervezetében. A Azure Active Directory több ezer Felhőbeli alkalmazással is rendelkezik integrációval.

- Hibajelentések – azokat a hibákat jelzi, amelyek akkor fordulhatnak elő, amikor a fiókok külső alkalmazásokba való kihelyezése történik.

- Felhasználó-specifikus jelentések – egy adott felhasználó eszközének és bejelentkezési tevékenységének megjelenítése.

- Tevékenységnaplók – az elmúlt 24 órában, az elmúlt 7 napban vagy az elmúlt 30 napban, valamint a csoportos tevékenységek változásaira, valamint a jelszó-visszaállításra és a regisztrációs tevékenységre vonatkozó összes naplózott esemény rekordját tartalmazza.

#### <a name="consumer-identity-and-access-management"></a>Felhasználói identitás és hozzáférés-kezelés

A [Azure Active Directory B2C](https://azure.microsoft.com/services/active-directory-b2c/) egy nagyszámú, globális, identitás-kezelési szolgáltatás, amely több száz millió identitásra méretezhető. Mobil- és webes platformokba is integrálható. A felhasználók testreszabható felületeken, meglévő közösségi hálózati fiókjaikkal vagy új hitelesítő adatok létrehozásával jelentkezhetnek be minden alkalmazásába.

A múltban az alkalmazások fejlesztői számára, akik regisztrálni akartak, [és bejelentkeznek a felhasználók](../../active-directory-b2c/overview.md) alkalmazásaiba, saját programkódot kellett volna beírniuk. Ennek keretében általában helyszíni adatbázisokat vagy rendszereket használtak a felhasználónevek és jelszavak tárolására. A Azure Active Directory B2C a biztonságos, szabványokon alapuló platform és a bővíthető házirendek széles választékának segítségével lehetővé teszi, hogy a szervezet jobban integrálja a felhasználói identitások kezelését az alkalmazásokba.

Azure Active Directory B2C használatakor a felhasználók a meglévő közösségi fiókjaik (Facebook, Google, Amazon, LinkedIn) használatával regisztrálhatják alkalmazásaikat, vagy új hitelesítő adatokat (e-mail-címet, jelszót vagy felhasználónevet és jelszót) hoznak létre.

#### <a name="device-registration"></a>Eszközregisztráció

Az [Azure ad-eszközök regisztrációja](../../active-directory/devices/overview.md) az eszköz alapú [feltételes hozzáférési](../../active-directory/devices/overview.md) forgatókönyvek alapja. Ha egy eszköz regisztrálva van, az Azure AD-eszköz regisztrálása olyan identitást biztosít az eszköz számára, amely az eszköz hitelesítésére szolgál, amikor a felhasználó bejelentkezik. A hitelesített eszköz és az eszköz attribútumai ezután a felhőben és a helyszínen üzemeltetett alkalmazások feltételes hozzáférési házirendjeinek betartatására használhatók.

A [mobileszköz-kezelési (Mdm)](https://www.microsoft.com/itshowcase/Article/Content/588/Mobile-device-management-at-Microsoft) megoldással, például az Intune-nal együtt a Azure Active Directoryban található eszköz attribútumai frissülnek az eszközre vonatkozó további információkkal. Ez lehetővé teszi, hogy olyan feltételes hozzáférési szabályokat hozzon létre, amelyek az eszközök hozzáférését kényszerítik, hogy megfeleljenek a biztonsági és megfelelőségi szabványoknak.

#### <a name="privileged-identity-management"></a>Privileged Identity Management

[Azure Active Directory (ad) Privileged Identity Management](../../active-directory/privileged-identity-management/pim-configure.md) lehetővé teszi a Kiemelt identitások kezelését, vezérlését és figyelését, valamint az Azure ad-ben és más Microsoft-online szolgáltatások, például Microsoft 365 vagy Microsoft Intune található erőforrásokhoz való hozzáférést.

Időnként a felhasználóknak az Azure-ban, illetve Microsoft 365 erőforrásokban vagy más SaaS-alkalmazásokban kell megadniuk az emelt szintű műveleteket. Ez gyakran azt jelenti, hogy a szervezeteknek állandó jogosultsági szintű hozzáférést kell biztosítaniuk az Azure AD-ben. Ez egyre nagyobb biztonsági kockázatot jelent a felhőben üzemeltetett erőforrások esetében, mivel a szervezetek nem tudják eléggé figyelni, hogy a felhasználók hogyan használják a rendszergazdai jogosultságokat. Emellett, ha egy emelt szintű hozzáféréssel rendelkező felhasználói fiók biztonsága sérül, az egyik szabálysértés hatással lehet az általános Felhőbeli biztonságra. Azure AD Privileged Identity Management segít feloldani ezt a kockázatot.

A Azure AD Privileged Identity Management a következőket teszi lehetővé:

- Az Azure AD-rendszergazdák által megtekinthető felhasználók

- Igény szerinti, "igény szerinti" rendszergazdai hozzáférés engedélyezése a Microsoft Online szolgáltatásaihoz (például Microsoft 365 és Intune)

- A rendszergazdai hozzáférési előzményekkel és a rendszergazdai hozzárendelések változásaival kapcsolatos jelentések beolvasása

- A Kiemelt szerepkörhöz való hozzáférésre vonatkozó riasztások beszerzése

#### <a name="identity-protection"></a>Identity protection

A [Azure ad Identity Protection](../../active-directory/identity-protection/overview-identity-protection.md) egy olyan biztonsági szolgáltatás, amely összevont nézetet biztosít a szervezet identitásait érintő kockázati észlelések és potenciális sebezhetőségek számára. Az Identity Protection meglévő Azure Active Directory anomáliák észlelési képességeit használja (az Azure AD rendellenes tevékenységi jelentésein keresztül érhető el), és új kockázati észlelési típusokat vezet be, amelyek valós időben észlelik a rendellenességeket.

## <a name="secure-resource-access"></a>Erőforrás-hozzáférés biztonságossá tétele

Az Azure-beli hozzáférés-vezérlés számlázási szempontból megkezdődik. Egy Azure-fiók tulajdonosa, amely a [Azure Fiókközpont](https://account.windowsazure.com/subscriptions)meglátogatásával érhető el, a fiók RENDSZERGAZDÁJA (AA). Az előfizetések a számlázáshoz használható tárolók, de biztonsági határként működnek: minden előfizetés rendelkezik egy szolgáltatás-rendszergazdával (SA), aki az előfizetésben lévő Azure-erőforrásokat az Azure Portal használatával adhatja hozzá, távolíthatja el és módosíthatja. Az új előfizetés alapértelmezett SA az AA, de az AA megváltoztathatja a SA-t a Azure Fiókközpontban.

![Biztonságos erőforrás-hozzáférés az Azure-ban](./media/technical-capabilities/azure-security-technical-capabilities-fig3.png)

Az előfizetések egy címtárral is társítva vannak. A címtár a felhasználók készletét határozza meg. Ezek lehetnek a címtárat létrehozó munkahelyi vagy iskolai felhasználók, vagy külső felhasználók is lehetnek (azaz Microsoft-fiókok). Az előfizetések azon felhasználók egy részhalmaza számára érhetők el, akik szolgáltatás-rendszergazdaként (SA) vagy Co-Administrator (CA) vannak hozzárendelve. az egyetlen kivétel az, hogy az örökölt okokból a Microsoft-fiókok (korábbi nevén Windows Live ID) a címtárban való jelenlét nélkül is hozzárendelhetők SA vagy CA-ként.

A biztonsági irányultságú vállalatoknak arra kell összpontosítaniuk, hogy az alkalmazottaknak a rájuk vonatkozó pontos engedélyeket adják. Túl sok engedély teheti ki a fiókot a támadók számára. A túl kevés engedély azt jelenti, hogy az alkalmazottak nem tudják hatékonyan elvégezni a munkájukat. Az Azure [szerepköralapú hozzáférés-vezérlés (Azure RBAC)](../../role-based-access-control/overview.md) részletes hozzáférés-kezelést nyújt az Azure-hoz a probléma megoldásához.

![Biztonságos erőforrás-hozzáférés](./media/technical-capabilities/azure-security-technical-capabilities-fig4.png)

Az Azure RBAC használata lehetővé teszi, hogy elkülönítse a kötelességeket a csapaton belül, valamint csak olyan mértékű hozzáférést biztosítson, amelyre a felhasználóknak a feladataik elvégzéséhez szükségük van. Ahelyett, hogy az Azure-előfizetésben vagy-erőforrásokban mindenki számára nem korlátozott engedélyeket adna, csak bizonyos műveleteket engedélyezhet. Az Azure RBAC használatával például az egyik alkalmazott kezelheti a virtuális gépeket egy előfizetésben, míg egy másik SQL-adatbázist is kezelhet ugyanazon az előfizetésen belül.

![Biztonságos erőforrás-hozzáférés az Azure RBAC](./media/technical-capabilities/azure-security-technical-capabilities-fig5.png)

## <a name="data-security-and-encryption"></a>Adatbiztonság és titkosítás

A felhőben az adatvédelem egyik kulcsa az, hogy milyen állapotokban lehet az adatai, és hogy milyen vezérlők érhetők el az adott állapotban. Az Azure adatbiztonsággal és a titkosítással kapcsolatos ajánlott eljárások az alábbi adatállapotok körüli javaslatok.

- A REST-on: magában foglalja az összes olyan adattárolási objektumot, tárolót és típust, amely statikusan, fizikai adathordozón található, mágneses vagy optikai lemez.
- Tranzitban: Ha az adatok átvitele az összetevők, a helyszínek és a programok között történik, például a hálózaton keresztül, a helyszíniről a felhőbe és fordítva, beleértve a hibrid kapcsolatokat, például a ExpressRoute-t, vagy egy bemeneti/kimeneti folyamat során, úgy gondolja, hogy mozgásban van.

### <a name="encryption-at-rest"></a>Titkosítás inaktív állapotban

A inaktív adatok titkosítását részletesen ismertetjük az Azure-beli [adattitkosításban](encryption-atrest.md).

### <a name="encryption-in-transit"></a>Átvitel közbeni titkosítás

Az adattovábbítási adatok védelmének fontos részét kell képeznie az adatvédelmi stratégiának. Mivel az adatok több helyről térnek vissza és oda, az általános javaslat az, hogy mindig SSL/TLS protokollokat használ az adatok különböző helyszíneken való cseréjéhez. Bizonyos esetekben érdemes lehet elkülöníteni a teljes kommunikációs csatornát a helyszíni és a felhőalapú infrastruktúra között egy virtuális magánhálózat (VPN) használatával.

A helyszíni infrastruktúra és az Azure közötti adatátvitelhez érdemes megfontolnia a megfelelő védelmet, például a HTTPS-t vagy a VPN-t.

Azon szervezetek esetében, amelyeknek a helyszíni és az Azure-ban található több munkaállomásról kell védeniük a hozzáférést, az [Azure webhelyek közötti VPN](../../vpn-gateway/vpn-gateway-howto-site-to-site-classic-portal.md)-t használhatja.

Olyan szervezeteknek, amelyeknek a helyszínen lévő egyik munkaállomásról az Azure-ba kell védeniük a hozzáférést, a [pont – hely típusú VPN](../../vpn-gateway/vpn-gateway-howto-point-to-site-classic-azure-portal.md)használatával.

Nagyobb adatkészletek helyezhetők át egy dedikált nagy sebességű WAN-kapcsolaton, például a [ExpressRoute](https://azure.microsoft.com/services/expressroute/). Ha úgy dönt, hogy a ExpressRoute-t használja, az [SSL/TLS](https://web.archive.org/web/20150221085231/http://support.microsoft.com:80/kb/257591) vagy más protokollok használatával is titkosíthatja az alkalmazásokat az alkalmazás szintjén.

Ha az Azure Storage-t a Azure Portalon keresztül használja, az összes tranzakció HTTPS-kapcsolaton keresztül történik. A [storage REST API](/rest/api/storageservices/) HTTPS protokollon keresztül is használható az [Azure storage](https://azure.microsoft.com/services/storage/) és a [Azure SQL Database](https://azure.microsoft.com/services/sql-database/)használatára.

Azok a szervezetek, amelyek nem tudnak védelmet biztosítani az átvitel során, érzékenyebbek a [támadók](/previous-versions/office/skype-server-2010/gg195821(v=ocs.14)), a [lehallgatás](/previous-versions/office/skype-server-2010/gg195641(v=ocs.14))és a munkamenet-eltérítések számára. Ezek a támadások a bizalmas adatokhoz való hozzáférés első lépései lehetnek.

Ha többet szeretne megtudni az Azure VPN-ről, tekintse át a [VPN Gateway tervezésének és kialakításának](../../vpn-gateway/vpn-gateway-about-vpngateways.md)cikkét.

### <a name="enforce-file-level-data-encryption"></a>Fájl szintű adattitkosítás érvényesítése

A [Azure RMS](/azure/information-protection/what-is-azure-rms) titkosítási, identitási és engedélyezési házirendeket használ a fájlok és az e-mailek védelméhez. Azure RMS több eszközön is működik – telefonok, tabletták és PC-k, a szervezeten belüli és a szervezeten kívüli védelemmel. Ez a képesség azért lehetséges, mert a Azure RMS az adataiban maradó védelmi szintet is biztosít, még akkor is, ha elhagyja a szervezet határait.

Ha Azure RMSt használ a fájlok védelemmel való ellátásához, az iparági szabványnak megfelelő titkosítást használ az [FIPS 140-2](https://nvlpubs.nist.gov/nistpubs/FIPS/NIST.FIPS.140-2.pdf)teljes körű támogatásával. Ha adatvédelmet Azure RMS használ, akkor a védelem abban az esetben is fennáll, ha a fájl nem az informatikai, hanem egy felhőalapú tárolási szolgáltatás számára van átmásolva. Ugyanez vonatkozik az e-mailben megosztott fájlokra, a fájl egy e-mail-üzenet mellékletként van védve, és útmutatást nyújt a védett melléklet megnyitásához.
Azure RMS bevezetésének tervezésekor a következőket javasoljuk:

- Telepítse a [RMS-megosztó alkalmazást](/azure/information-protection/rms-client/sharing-app-windows). Ez az alkalmazás az Office-alkalmazásokkal integrálható egy Office-bővítmény telepítésével, amellyel a felhasználók könnyedén védetté tehetik a fájlokat.

- Alkalmazások és szolgáltatások konfigurálása Azure RMS támogatásához

- [Egyéni sablonokat](/azure/information-protection/configure-policy-templates) hozhat létre, amelyek az üzleti igényeknek megfelelően jelennek meg. Például: sablon a szigorúan titkos adathoz, amelyet az összes szigorúan kapcsolódó e-mailben alkalmazni kell.

Az [adatbesorolásra](https://download.microsoft.com/download/0/A/3/0A3BE969-85C5-4DD2-83B6-366AA71D1FE3/Data-Classification-for-Cloud-Readiness.pdf) és a fájlok védelmére gyengének számító szervezetek az adatszivárgásra érzékenyebbek lehetnek. A megfelelő adatvédelem nélkül a szervezetek nem tudják beolvasni az üzleti ismereteket, figyelve a visszaéléseket, és megakadályozhatják a fájlok rosszindulatú elérését.

> [!Note]
> Azure RMS az [Azure Rights Management első lépések](/azure/information-protection/requirements)című cikkben olvashat bővebben.

## <a name="secure-your-application"></a>Az alkalmazás biztonságossá tétele
Míg az Azure felelős az alkalmazás által futtatott infrastruktúra és platform védelméért, az Ön felelőssége, hogy saját maga is biztonságossá tegye az alkalmazást. Más szóval az alkalmazás kódjának és tartalmának biztonságos módon történő fejlesztését, üzembe helyezését és kezelését kell végeznie. Ennek hiányában az alkalmazás kódja vagy tartalma továbbra is sebezhető lehet a fenyegetésekkel szemben.

### <a name="web-application-firewall"></a>Webalkalmazási tűzfal
A [webalkalmazási tűzfal (WAF)](../../web-application-firewall/ag/ag-overview.md) a [Application Gateway](../../application-gateway/overview.md) szolgáltatása, amely központosított védelmet biztosít a webalkalmazások számára a gyakori biztonsági rések és sebezhetőségek ellen.

A webalkalmazási tűzfal az [alapvető OWASP-szabálykészletek](https://www.owasp.org/index.php/Category:OWASP_ModSecurity_Core_Rule_Set_Project) 3.0-s vagy 2.2.9-es verzióinak szabályai alapján működik. A webalkalmazások egyre inkább ki vannak téve rosszindulatú támadásoknak, amelyek az ismert biztonsági réseket használják ki. Az ilyen jellegű támadások között például gyakoriak az SQL-injektálásos és a webhelyek közötti, parancsprogramot alkalmazó támadások. Az ilyen támadások megakadályozása az alkalmazás kódjában kihívást jelenthet, és szigorú felügyeletet, javítást és megfigyelést igényelhet az alkalmazás topológiájának több rétegén. A központosított webalkalmazási tűzfal egyszerűbbé teszi a biztonságfelügyeletet, és segít az alkalmazás-rendszergazdáknak a fenyegetések vagy a behatolások elleni védekezésben. Emellett a WAF-megoldás gyorsabban képes kezelni a biztonsági fenyegetéseket azáltal, hogy kijavítja az ismert biztonsági réseket egy központi helyen, ahelyett hogy az egyes webalkalmazások védelmét biztosítaná. A meglévő alkalmazásátjárókat egyszerűen át lehet alakítani webalkalmazási tűzfallal rendelkező alkalmazásátjárókká.

A webalkalmazási tűzfal többek között a következő gyakori internetes biztonsági rések ellen nyújt védelmet:

- SQL-injektálás elleni védelem

- Webhelyek közötti, parancsprogramot alkalmazó támadások elleni védelem

- Gyakori webes támadások (például parancsinjektálás, HTTP-kéréscsempészet, HTTP-válaszfelosztás és távolifájl-beszúrásos támadás) elleni védelem

- HTTP protokoll megsértése elleni védelem

- HTTP protokollanomáliák (például hiányzó gazdagép-felhasználói ügynök és Accept (Elfogadás) fejlécek) elleni védelem

- Robotprogramok, webbejárók és képolvasók elleni védelem

- Az alkalmazások gyakori konfigurációs beállításainak észlelése (vagyis Apache, IIS stb.)

> [!Note]
> A szabályok és azok védelmének részletesebb listáját lásd a következő [alapvető szabályokban](../../web-application-firewall/ag/ag-overview.md):

Az Azure számos könnyen használható funkciót is biztosít, amelyek segítenek az alkalmazás bejövő és kimenő forgalmának biztonságossá tételében. Az Azure lehetővé teszi az ügyfelek számára az alkalmazás kódjának védelmét azáltal, hogy külsőleg biztosított funkciókat biztosít a webalkalmazás biztonsági rések vizsgálatához.

- [Azure Active Directory hitelesítés beállítása az alkalmazáshoz](https://azure.microsoft.com/blog/azure-websites-authentication-authorization/)

- [Biztonságos adatforgalom az alkalmazáshoz Transport Layer Security (TLS/SSL) engedélyezése – HTTPS](../../app-service/configure-ssl-bindings.md)

  - [Minden bejövő forgalom kényszerítése HTTPS-kapcsolaton keresztül](http://microsoftazurewebsitescheatsheet.info/)

  - [Szigorú átviteli biztonság engedélyezése (HSTS)](http://microsoftazurewebsitescheatsheet.info/#enable-http-strict-transport-security-hsts)

- [Az alkalmazáshoz való hozzáférés korlátozása az ügyfél IP-címe alapján](http://microsoftazurewebsitescheatsheet.info/#filtering-traffic-by-ip)

- [Az alkalmazáshoz való hozzáférés korlátozása az ügyfél viselkedése – kérelmek gyakorisága és egyidejűsége](http://microsoftazurewebsitescheatsheet.info/#dynamic-ip-restrictions)

- [A sztaniol biztonsági vizsgálatát használó biztonsági rések ellenőrzése a webalkalmazás-kódban](https://azure.microsoft.com/blog/web-vulnerability-scanning-for-azure-app-service-powered-by-tinfoil-security/)

- [A TLS kölcsönös hitelesítésének konfigurálása az Ügyféltanúsítványok megköveteléséhez a webalkalmazáshoz való kapcsolódáshoz](../../app-service/app-service-web-configure-tls-mutual-auth.md)

- [Ügyféltanúsítvány konfigurálása az alkalmazásból a külső erőforrásokhoz való biztonságos kapcsolódáshoz](https://azure.microsoft.com/blog/using-certificates-in-azure-websites-applications/)

- [A szabványos kiszolgálók fejlécének eltávolítása az alkalmazások ujjlenyomatának elkerüléséhez](https://azure.microsoft.com/blog/removing-standard-server-headers-on-windows-azure-web-sites/)

- [Az alkalmazás biztonságos csatlakoztatása egy magánhálózat erőforrásaival pont – hely típusú VPN használatával](../../app-service/web-sites-integrate-with-vnet.md)

- [Az alkalmazás biztonságos összekapcsolása egy magánhálózat erőforrásaival Hibrid kapcsolatok használatával](../../app-service/app-service-hybrid-connections.md)

A Azure App Service az Azure Cloud Services és a Virtual Machines által használt kártevő-elhárító megoldást használja. Ha többet szeretne megtudni erről, tekintse meg a [kártevők elleni dokumentációt](antimalware.md).

## <a name="secure-your-network"></a>A hálózat védelme
Microsoft Azure tartalmaz egy robusztus hálózati infrastruktúrát, amely támogatja az alkalmazások és szolgáltatások csatlakozási követelményeit. A hálózati kapcsolat az Azure-ban, a helyszíni és az Azure által üzemeltetett erőforrások, valamint az internetről és az Azure-ból származó erőforrások között lehetséges.

Az [Azure hálózati infrastruktúra](/previous-versions/azure/virtual-machines/windows/infrastructure-example) lehetővé teszi az Azure-erőforrások biztonságos összekapcsolását a [Virtual Networks (virtuális hálózatok)](../../virtual-network/virtual-networks-overview.md)szolgáltatással. A VNet a saját hálózatának ábrázolása a felhőben. A VNet az előfizetéséhez dedikált Azure Cloud Network logikai elkülönítése. A virtuális hálózatok a helyszíni hálózatokhoz is csatlakoztathatók.

![A hálózat biztonságossá tétele (védelem)](./media/technical-capabilities/azure-security-technical-capabilities-fig6.png)

Ha alapszintű hálózati szintű hozzáférés-vezérlésre van szüksége (az IP-cím és a TCP-vagy UDP-protokollok alapján), akkor használhat [hálózati biztonsági csoportokat](../../virtual-network/virtual-network-vnet-plan-design-arm.md). A hálózati biztonsági csoport (NSG) egy alapszintű, állapot-nyilvántartó csomagszűrő tűzfal, amely lehetővé teszi, hogy [5 rekord](https://www.techopedia.com/definition/28190/5-tuple)alapján vezérelje a hozzáférést.

Az Azure Networking lehetővé teszi az útválasztási viselkedés testreszabását az Azure-beli virtuális hálózatokon lévő hálózati forgalomhoz. Ezt úgy teheti meg, hogy a [felhasználó által megadott útvonalakat](../../virtual-network/virtual-networks-udr-overview.md) konfigurálja az Azure-ban.

A [kényszerített bújtatás](https://www.petri.com/azure-forced-tunneling) olyan mechanizmus, amellyel biztosítható, hogy a szolgáltatások ne kezdeményezzenek kapcsolatot az internethez csatlakozó eszközökhöz.

Az Azure támogatja a dedikált WAN-kapcsolatot a helyszíni hálózattal és egy Azure-Virtual Network a [ExpressRoute](../../expressroute/expressroute-introduction.md). Az Azure és a webhely közötti kapcsolat olyan dedikált kapcsolatot használ, amely nem a nyilvános interneten keresztül érhető el. Ha az Azure-alkalmazás több adatközpontban fut, az [azure Traffic Manager](../../traffic-manager/traffic-manager-overview.md) segítségével a felhasználóktól érkező kéréseket intelligens módon irányíthatja át az alkalmazás példányai között. Az Azure-ban nem futó szolgáltatásokra is átirányíthatja a forgalmat, ha azok elérhetők az internetről.

Az Azure a saját és biztonságos kapcsolatait is támogatja a Pásti-erőforrásokhoz (például Azure Storage és SQL Database) az Azure-Virtual Network az [Azure Private link](../../private-link/private-link-overview.md)használatával. A Pásti erőforrás a virtuális hálózatában lévő [privát végpontra](../../private-link/private-endpoint-overview.md) van leképezve. A virtuális hálózatban található privát végpont és a Péter-erőforrás közötti kapcsolat a Microsoft gerinc hálózatát használja, és nem a nyilvános interneten keresztül érhető el. A szolgáltatás nyilvános internetre való kimutatása már nem szükséges. Az Azure Private link használatával is elérheti az Azure-ban üzemeltetett felhasználói és partneri szolgáltatásokat a virtuális hálózaton.  Emellett az Azure Private link használatával saját [privát kapcsolati szolgáltatást](../../private-link/private-link-service-overview.md) hozhat létre a virtuális hálózatban, és a virtuális hálózatában saját ügyfelei számára is elérhetővé teheti azt. Az Azure Private link használatával történő beállítás és felhasználás konzisztens az Azure Pásti, az ügyfél és a megosztott partneri szolgáltatások között.

## <a name="virtual-machine-security"></a>Virtuális gépek biztonsága

Az [Azure Virtual Machines](../../virtual-machines/index.yml) számos számítástechnikai megoldás üzembe helyezését teszi lehetővé agilis módon. A Microsoft Windows, Linux, Microsoft SQL Server, Oracle, IBM, SAP és Azure BizTalk Services támogatásával szinte bármely operációs rendszeren üzembe helyezheti a számítási feladatokat és a nyelveket.

Az Azure-ban olyan biztonsági gyártóktól származó [antimalware szoftvereket](antimalware.md) használhat, mint a Microsoft, a Symantec, a Trend Micro és a Kaspersky, hogy megvédje a virtuális gépeket a kártékony fájlokkal, a reklámprogramokkal és más fenyegetésekkel szemben.

A Microsoft antimalware for Azure Cloud Services és Virtual Machines egy valós idejű védelmi képesség, amely segít azonosítani és eltávolítani a vírusokat, kémprogramokat és egyéb kártevő szoftvereket. A Microsoft antimalware konfigurálható riasztásokat biztosít, amikor az ismert kártékony vagy nemkívánatos szoftverek megkísérlik telepíteni vagy futtatni magukat az Azure-rendszereken.

A [Azure Backup](../../backup/backup-overview.md) egy méretezhető megoldás, amely az alkalmazás adatait nulla tőkebefektetéssel és minimális működési költségekkel védi. Az alkalmazások hibái sérültek az adataikat, és az emberi hibák hibákat okozhatnak az alkalmazásaiban. A Azure Backup a Windows és a Linux rendszerű virtuális gépek védettek.

[Azure site Recovery](../../site-recovery/site-recovery-overview.md) segíti a munkaterhelések és alkalmazások replikálásának, feladatátvételének és helyreállításának összehangolását, hogy azok másodlagos helyről is elérhetők legyenek, ha az elsődleges hely leáll.

## <a name="ensure-compliance-cloud-services-due-diligence-checklist"></a>Megfelelőség biztosítása: Cloud Services – átvilágítás – ellenőrzőlista

A Microsoft fejlesztette ki [a Cloud Services átvilágítás ellenőrzőlistáját](https://aka.ms/cloudchecklist.download) , amely segítséget nyújt a szervezetek számára, hogy a felhőbe való áttérést mérlegelő módon járjanak el. Struktúrát biztosít bármilyen méretű és típusú szervezet számára – a magánszektor és az állami szektorbeli szervezetek, beleértve a kormányzati szerveket minden szinten és nonprofit szervezetekben – a saját teljesítmény-, szolgáltatás-, adatkezelési és irányítási céljainak és követelményeinek azonosítása érdekében. Ez lehetővé teszi, hogy összehasonlítsa a különböző felhőalapú szolgáltatók ajánlatait, és végül egy felhőalapú szolgáltatási szerződés alapját képezi.

Az ellenőrzőlista olyan keretrendszert biztosít, amely a záradékok szerinti záradékot egy új, a Cloud Service-szerződések, az ISO/IEC 19086 nemzetközi szabványával igazítja. Ez a standard a szervezeteknek szóló, a felhőbe való bevezetéssel kapcsolatos döntések meghozatalára és a Cloud Service-ajánlatok összehasonlítására szolgáló közös terület létrehozására nyújt segítséget.

Az ellenőrzőlista elősegíti a felhőbe való átvilágítást, strukturált útmutatást és egységes, ismételhető megközelítést biztosít a felhőalapú szolgáltató kiválasztásához.

A felhőbe való bevezetést már nem csupán a technológiai döntések határozzák meg. Mivel a ellenőrzőlista követelményei egy szervezet minden aspektusában megérintik az összes kulcsfontosságú belső döntéshozót, az informatikai vezetőt és a CISO, valamint a jogi, kockázatkezelési, beszerzési és megfelelőségi szakembereket. Ez növeli a döntéshozatali folyamat hatékonyságát és az alapvető döntéseket a megfelelő indoklással, így csökkentve az előre nem látható útelzárások valószínűségét.

Emellett az Ellenőrzőlista:

- A felhőalapú bevezetési folyamat elején ismerteti a döntéshozók számára a legfontosabb témákat.

- Támogatja a szabályozásokkal kapcsolatos alapos üzleti megbeszéléseket és a szervezet saját adatvédelmi, személyes és adatbiztonságra vonatkozó célkitűzéseit.

- Segít a szervezeteknek azonosítani a Felhőbeli projekteket érintő esetleges problémákat.

- Konzisztens kérdéseket tesz fel az egyes szolgáltatókra vonatkozó feltételekkel, definíciókkal, metrikákkal és termékekkel kapcsolatban, hogy egyszerűbbé váljon a különböző felhőalapú szolgáltatók ajánlatai összehasonlításának folyamata.

## <a name="azure-infrastructure-and-application-security-validation"></a>Az Azure-infrastruktúra és az alkalmazások biztonságának ellenőrzése

Az [Azure Operational Security](operational-security.md) a felhasználók számára elérhető szolgáltatásokat, vezérlőket és szolgáltatásokat jelenti a Microsoft Azureban tárolt adatok, alkalmazások és egyéb eszközök védelmére.

![biztonsági ellenőrzés (észlelés)](./media/technical-capabilities/azure-security-technical-capabilities-fig7.png)

Az Azure Operational Security olyan keretrendszerre épül, amely magában foglalja a Microsoft számára egyedi, a Microsoft biztonsági fejlesztési életciklusát (SDL), a Microsoft Security Response Center programot, valamint a kiberbiztonsági fenyegetések tájképének részletes ismereteit.

### <a name="microsoft-azure-monitor"></a>Microsoft Azure figyelő

[Azure monitor](../../azure-monitor/index.yml) az informatikai felügyeleti megoldás a hibrid felhőhöz. Önmagában vagy a meglévő System Center-telepítés kiterjesztéséhez Azure Monitor naplók lehetővé teszi az infrastruktúra felhőalapú felügyeletének maximális rugalmasságát és szabályozását.

![Azure Monitor](./media/technical-capabilities/azure-security-technical-capabilities-fig8.png)

A Azure Monitor segítségével bármilyen Felhőbeli példányt kezelhet, beleértve a helyszíni, az Azure, az AWS, a Windows Server, a Linux, a VMware és a OpenStack szolgáltatást is, a versenyképes megoldásoknál alacsonyabb áron. A Felhőbeli első világra épülő Azure Monitor új megközelítést kínál a vállalata számára, amely a leggyorsabb és leghatékonyabb módja az új üzleti kihívásoknak, valamint az új munkaterhelések, alkalmazások és felhőalapú környezetek bevezetésének.

### <a name="azure-monitor-logs"></a>Azure Monitor-naplók

A [Azure monitor-naplók](https://azure.microsoft.com/documentation/services/log-analytics) figyelik a felügyeleti szolgáltatásokat azáltal, hogy a felügyelt erőforrások adatait egy központi tárházba gyűjtik. Ezek az adatok lehetnek események, teljesítményadatok vagy az API segítségével biztosított egyéni adatok. Az összegyűjtésüket követően az adatok használhatók riasztáshoz, elemzéshez vagy exportáláshoz.

![Azure Monitor-naplók](./media/technical-capabilities/azure-security-technical-capabilities-fig9.png)

Ezzel a módszerrel különböző forrásokból is összevonhatja az adatait, így az Azure-szolgáltatásokból származó adatok egyesíthetők a meglévő helyszíni környezettel. Továbbá egyértelműen elkülöníti az adatok gyűjtését az adatokon végzett műveletektől, így az összes művelet végrehajtható a különféle adatokon.

### <a name="azure-security-center"></a>Azure Security Center

[Az Azure Security Center](../../security-center/security-center-introduction.md) az Azure-erőforrások biztonsági felügyeletének átláthatóbbá és szabályozhatóbbá tételével megkönnyíti a fenyegetések megelőzését, észlelését és elhárítását. Az ügyfél összes előfizetésére kiterjedő, integrált biztonsági monitorozást és szabályzatkezelést biztosít, megkönnyíti a nehezen észlelhető fenyegetések azonosítását, és számos biztonsági megoldással együttműködik.

A Security Center a potenciális biztonsági hiányosságok azonosítása érdekében elemzi az Azure-erőforrások biztonsági állapotát. A javaslatok listája végigvezeti Önt a szükséges szabályozási folyamatok konfigurálásának folyamatán.

Példák:

- Kártevőirtók kiépítése a kártékony szoftverek azonosításához és eltávolításához

- Hálózati biztonsági csoportok és szabályok konfigurálása a virtuális gépek forgalmának vezérléséhez

- Webalkalmazási tűzfalak kiépítése a webalkalmazások ellen irányuló támadások elleni védelemre

- Hiányzó rendszerfrissítések telepítése

- Az operációs rendszer azon konfigurációinak kezelése, amelyek nem felelnek meg a javasolt alapkonfigurációknak

A Security Center automatikusan gyűjti, elemzi és integrálja az Azure-erőforrások, a hálózat és az olyan partnermegoldások, mint például a kártevőirtó-programok és a tűzfalak naplóadatait. Fenyegetések észlelése esetén a központ biztonsági riasztást hoz létre. Példák fenyegetés észlelésére:

- Ismert kártékony IP-címmel kommunikáló feltört virtuális gépek

- Windows hibajelentés által észlelt speciális kártevő

- Találgatásos támadás a virtuális gépek ellen

- Az integrált kártevőirtó programok és a tűzfalak biztonsági riasztásai

### <a name="azure-monitor"></a>Azure-figyelő

A [Azure monitor](../../azure-monitor/overview.md) az adott típusú erőforrásokra mutató tájékoztatókat biztosít. Vizualizációkat, lekérdezéseket, útválasztást, riasztásokat, automatikus méretezést és automatizálást biztosít az Azure-infrastruktúra (műveletnapló) és az egyes Azure-erőforrások (diagnosztikai naplók) esetében egyaránt.

A Felhőbeli alkalmazások számos mozgó részből összetettek. A figyelési szolgáltatással biztosítható, hogy az alkalmazás kifogástalan állapotban maradjon. Emellett segít elhárítani a lehetséges problémákat vagy a múltbeli hibák elhárítását.

![Diagram, amely bemutatja, hogy a figyelési adataival részletes elemzéseket kaphat az alkalmazásról.](./media/technical-capabilities/azure-security-technical-capabilities-fig10.png)
Emellett a figyelési adatok segítségével mélyebb elemzéseket nyerhet az alkalmazásról. Ezzel az ismerettel javíthatja az alkalmazások teljesítményét vagy karbantartását, vagy automatizálhatja azokat a műveleteket, amelyek egyébként manuális beavatkozást igényelnek.

A hálózati biztonság naplózása létfontosságú a hálózati sebezhetőségek észleléséhez, valamint az informatikai biztonsági és szabályozási irányítási modell megfelelőségének biztosításához. A biztonsági csoport nézetben lekérheti a konfigurált hálózati biztonsági csoport és biztonsági szabályok, valamint a hatályos biztonsági szabályok beolvasását. Az alkalmazott szabályok listájának használatával meghatározhatja a nyitott és az SS hálózati biztonsági rést tartalmazó portokat.

### <a name="network-watcher"></a>Network Watcher

[Network Watcher](../../network-watcher/network-watcher-monitoring-overview.md) egy regionális szolgáltatás, amely lehetővé teszi, hogy az Azure-ban hálózati szinten figyelje és diagnosztizálja a feltételeket. A Network Watcher elérhető hálózati diagnosztikai és vizualizációs eszközök segítségével megismerheti, diagnosztizálhatja és elemezheti a hálózatát az Azure-ban. Ez a szolgáltatás magában foglalja a csomagok rögzítését, a következő ugrást, az IP-forgalom ellenőrzését, a biztonsági csoport nézetét, a NSG folyamat naplóit. A forgatókönyvek szintjének figyelése lehetővé teszi a hálózati erőforrások teljes körű megtekintését az egyes hálózati erőforrások figyelésével szemben.

### <a name="storage-analytics"></a>Storage Analytics

A [Storage Analytics](/rest/api/storageservices/fileservices/storage-analytics) olyan metrikákat tárolhat, amelyek összesített tranzakciós statisztikát és kapacitási adatokat tartalmaznak a tárolási szolgáltatásokra irányuló kérések esetén. A tranzakciókat az API működési szintjén, valamint a tárolási szolgáltatás szintjén is jelenteni kell, és a kapacitást a tárolási szolgáltatás szintjén kell jelenteni. A metrikák adatai a tárolási szolgáltatások használatának elemzésére, a tárolási szolgáltatással kapcsolatos kérések diagnosztizálására, valamint a szolgáltatást használó alkalmazások teljesítményének javítására használhatók.

### <a name="application-insights"></a>Application Insights

A [Application Insights](../../azure-monitor/app/app-insights-overview.md) egy bővíthető Application Performance Management-(APM-) szolgáltatás, amely több platformon is használható webfejlesztőknek. Az élő webalkalmazásának figyelésére használhatja. Automatikusan felismeri a teljesítményanomáliákat. Hatékony elemzési eszközöket tartalmaz, amelyek segítségével diagnosztizálhatja a problémákat, és megtudhatja, hogy a felhasználók milyen műveleteket végeznek az alkalmazással. Úgy tervezték, hogy használatával folyamatosan javíthassa a teljesítményt és a használhatóságot. Számos platformon használható, többek között a .NET, a Node.js és a Java EE, a helyszínen vagy a felhőben üzemeltetett alkalmazások esetében. Integrálva van a DevOps-folyamattal, és a kapcsolódási pontok különböző fejlesztői eszközökhöz vannak társítva.

A szolgáltatás az alábbiakat figyeli:

- **Kérések sebessége, válaszidők és hibaarányok** – megtudhatja, hogy mely lapok, mely napszakokban a legnépszerűbbek, és hol találhatók a felhasználók. Megtekintheti, hogy mely lapok teljesítenek a legjobban. Ha több kérés esetén a válaszidők és a hibaarányok értéke megnő, valószínűleg erőforrás-gazdálkodási hibáról van szó.

- **Függőségi értékek, válaszidők és hibaarányok** – megtudhatja, hogy mely külső szolgáltatások okoznak lassulást.

- **Kivételek** – elemezze az összesített statisztikát, vagy válasszon ki konkrét példányokat, és részletezse a verem nyomkövetését és a kapcsolódó kérelmeket. A kiszolgálói és a böngészői kivételekről egyaránt készül jelentés.

- **Lapmegtekintések és betöltési teljesítmény** – a felhasználói böngészők jelentése alapján készül.

- **Weblapokról származó Ajax-hívások** – díjak, válaszidő és meghibásodási arányok.

- **A felhasználók és a munkamenetek száma.**

- Windows vagy Linux rendszerű kiszolgálói gépekről származó **teljesítményszámlálók**, például processzor-, memória- és hálózathasználat.

- Dockerből vagy Azure-ból származó **gazdadiagnosztika**.

- Alkalmazásból származó **nyomkövetési naplók diagnosztikája** – megállapíthatja a nyomkövetési események és a kérések korrelációját.

- **Egyéni események és metrikák** , amelyeket az ügyfél vagy a kiszolgáló kódjában írhat, hogy nyomon követhesse az üzleti eseményeket, például az eladott elemeket vagy a megnyert játékokat.

Az alkalmazás infrastruktúrája általában számos összetevőből áll – például egy virtuális gépből, tárfiókból és virtuális hálózatból, vagy egy webalkalmazásból, adatbázisból, adatbázis-kiszolgálóból és harmadik féltől származó szolgáltatásokból. Ezeket az összetevőket nem külön entitásokként látja, hanem egyetlen entitás kapcsolódó és egymással összefüggő részeiként. Csoportként érdemes telepíteni, kezelni és megfigyelni őket. A [Azure Resource Manager](../../azure-resource-manager/management/overview.md) lehetővé teszi, hogy csoportként működjön együtt a megoldás erőforrásaival.

A megoldás összes erőforrását egyetlen, koordinált műveletben telepítheti, frissítheti vagy törölheti. A telepítéshez egy sablon használatos, amely különböző, például tesztelési, átmeneti és üzemi környezetben is képes működni. A Resource Manager biztonsági, naplózási és címkézési szolgáltatásokat biztosít, hogy segítsen az erőforrások kezelésében a telepítést követően.

**A Resource Manager használatának előnyei**

A Resource Manager számos előnyt kínál:

- A megoldás összes erőforrását egy csoportként telepítheti, felügyelheti és figyelheti meg az erőforrások különálló kezelése helyett.

- A megoldást ismételten telepítheti a fejlesztési életciklus során, és biztos lehet abban, hogy az erőforrások telepítése konzisztens lesz.

- Az infrastruktúrát szkriptek helyett deklaratív sablonok segítségével is kezelheti.

- Megadhatja az erőforrások közötti függőségeket, hogy azok a megfelelő sorrendben legyenek telepítve.

- Hozzáférés-vezérlést alkalmazhat az erőforráscsoport összes szolgáltatására, mivel az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC) natív módon integrálva van a felügyeleti platformba.

- Címkékkel láthatja el az erőforrásokat, így logikusan rendszerezhető az előfizetés összes erőforrása.

- Az azonos címkén osztozó erőforrások csoportjának költségeit megtekintve jól átláthatók a szervezet számlái.

> [!Note]
> A Resource Manager egy új módot kínál a megoldások telepítésére és kezelésére. Ha a korábbi üzemi modellt használta, és szeretne többet megtudni a változásokról, tekintse meg a [Resource Manager-telepítés és a klasszikus üzembe helyezés ismertetése](../../azure-resource-manager/management/deployment-models.md)című témakört.

## <a name="next-step"></a>Következő lépés

Az [Azure Security benchmark](../benchmarks/introduction.md) program olyan biztonsági javaslatokat tartalmaz, amelyek segítségével biztonságossá teheti az Azure-ban használt szolgáltatásokat.