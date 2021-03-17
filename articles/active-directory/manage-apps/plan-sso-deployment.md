---
title: Azure Active Directory egyszeri bejelentkezéses telepítés megtervezése
description: Útmutató az egyszeri bejelentkezés tervezéséhez, üzembe helyezéséhez és kezeléséhez a szervezetében.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.date: 06/10/2020
ms.author: baselden
ms.reviewer: jeedes
ms.collection: M365-identity-device-management
ms.custom: has-adal-ref
ms.openlocfilehash: a7949324cd4ad0459aa5ba111037b2f580da9cf8
ms.sourcegitcommit: 27cd3e515fee7821807c03e64ce8ac2dd2dd82d2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/16/2021
ms.locfileid: "103601712"
---
# <a name="plan-a-single-sign-on-deployment"></a>Egyszeri bejelentkezés üzembe helyezésének tervezése

Az egyszeri bejelentkezés (SSO) azt jelenti, hogy az összes alkalmazást és erőforrást csak egyszer kell bejelentkezni egy felhasználói fiók használatával. Egyszeri bejelentkezés esetén a felhasználók a szükséges alkalmazások eléréséhez anélkül férhetnek hozzá, hogy másodszor is hitelesíteniük kellene magukat.

## <a name="benefits-of-sso"></a>Az egyszeri bejelentkezés előnyei

Az egyszeri bejelentkezés (SSO) biztonsági és kényelmi funkciókat biztosít, amikor a felhasználók bejelentkeznek Azure Active Directory (Azure AD) alkalmazásaiba. 

Számos szervezet a szoftveres (SaaS) alkalmazásokra támaszkodik, például a Microsoft 365, a Box és a Salesforce a végfelhasználói hatékonyság érdekében. Az informatikai részlegnek az egyes SaaS-alkalmazásokban egyenként kell létrehoznia és frissítenie a felhasználói fiókokat, és a felhasználóknak meg kell emlékezniük a jelszóra.

Az Azure Marketplace-en több mint 3000 alkalmazás található előre integrált SSO-kapcsolattal, így könnyen integrálható a bérlőbe.

## <a name="licensing"></a>Licencek

- **Azure ad-licencelés** – SSO az előre integrált SaaS-alkalmazásokhoz ingyenes. A címtárban lévő objektumok száma és a telepíteni kívánt funkciók azonban további licenceket is igényelhetnek. A licencekre vonatkozó követelmények teljes listáját itt tekintheti meg: [Azure Active Directory díjszabása](https://azure.microsoft.com/pricing/details/active-directory/).
- **Alkalmazás licencelése** – szüksége lesz a megfelelő licencekre az SaaS-alkalmazásokhoz az üzleti igények kielégítése érdekében. Az alkalmazás tulajdonosával határozza meg, hogy az alkalmazáshoz hozzárendelt felhasználók rendelkeznek-e a megfelelő licenccel az alkalmazás szerepköreihez. Ha az Azure AD a szerepkörök alapján felügyeli az automatikus kiosztást, az Azure AD-ben hozzárendelt szerepköröknek meg kell egyezniük az alkalmazásban birtokolt licencek számával. Az alkalmazás tulajdonában lévő licencek helytelen száma hibát eredményezhet a felhasználó kiépítés/frissítése során.

## <a name="plan-your-sso-team"></a>Az SSO-csapat megtervezése

- **A megfelelő érintett felek bevonása** – ha a technológiai projektek meghiúsulnak, általában a hatás, az eredmények és a felelősségek eltérő elvárásai miatt. A buktatók elkerülése érdekében [Győződjön meg arról, hogy a megfelelő érintett feleket folytatja](../fundamentals/active-directory-deployment-plans.md) , és hogy az érintettek megértsék a szerepköreiket.
- **Kommunikáció tervezése** – a kommunikáció kritikus fontosságú az új szolgáltatások sikeressége szempontjából. Proaktív módon kommunikálhat a felhasználókkal arról, hogy a tapasztalatok Hogyan változnak, Mikor változnak, és hogyan szerezhetnek támogatást, ha problémákat tapasztalnak. Tekintse át, hogy a [végfelhasználók hogyan érhetik el az SSO-kompatibilis alkalmazásokat](end-user-experiences.md), és hogyan teheti a kommunikációt a választott lehetőségeknek megfelelően. 

## <a name="plan-your-sso-protocol"></a>Az SSO protokoll megtervezése

Az összevonási protokollok alapján történő egyszeri bejelentkezésen alapuló implementáció javítja a biztonságot, a megbízhatóságot és a végfelhasználói élményt, és megkönnyíti a megvalósítást. Számos alkalmazás előre integrálva van az Azure AD-ben, és [lépésenkénti útmutatók érhetők el](../saas-apps/tutorial-list.md). Az [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/)-en is megtalálhatja őket. Az egyes SSO-módszerekkel kapcsolatos részletes információkat az [egyszeri bejelentkezés a Azure Active Directory alkalmazásokban](what-is-single-sign-on.md)című cikkben találja.

Kétféleképpen engedélyezheti a felhasználók számára az alkalmazások egyszeri bejelentkezését:

- **Összevont egyszeri bejelentkezéssel** Az Azure AD hitelesíti a felhasználót az alkalmazásban az Azure AD-fiók használatával. Ez a módszer támogatott olyan alkalmazások esetében, amelyek támogatják az SAML 2,0, a WS-Federation vagy az OpenID Connect protokollt, és az egyszeri bejelentkezés legszélesebb módja. Azt javasoljuk, hogy az összevont egyszeri bejelentkezést az Azure AD-vel együtt használja, ha egy alkalmazás támogatja azt a jelszó-alapú egyszeri bejelentkezés és az ADFS helyett.

- Ha a **jelszó-alapú egyszeri bejelentkezést** használó felhasználók először bejelentkeznek az alkalmazásba, az első alkalommal be kell jelentkezniük a felhasználónévvel és a jelszóval. Az első bejelentkezés után az Azure AD megadja a felhasználónevet és a jelszót az alkalmazáshoz. A jelszó-alapú egyszeri bejelentkezés lehetővé teszi a biztonságos alkalmazások jelszavainak tárolását és a visszajátszás használatát webböngészős bővítmény vagy mobil alkalmazás használatával. Ez a beállítás kihasználja az alkalmazás által biztosított meglévő bejelentkezési folyamatot, lehetővé teszi a rendszergazdák számára a jelszavak kezelését, és nem igényli a felhasználótól a jelszó megismerését.

### <a name="considerations-for-federation-based-sso"></a>Az összevonási alapú SSO szempontjai

- Az **OpenID Connect és a OAuth használata** – ha a csatlakoztatni kívánt alkalmazás támogatja azt, használja az OIDC/OAuth 2,0 metódust, hogy engedélyezze az egyszeri bejelentkezést az adott alkalmazáshoz. Ez a módszer kevesebb konfigurálást igényel, és gazdagabb felhasználói élményt tesz lehetővé. További információ: [OAuth 2,0](../develop/v2-oauth2-auth-code-flow.md), [OpenID Connect 1,0](../develop/v2-protocols-oidc.md)és [Azure Active Directory fejlesztői útmutató](../develop/index.yml).
- Az **SAML-alapú SSO végpont-konfigurációi** – ha SAML-t használ, a fejlesztőknek az alkalmazás konfigurálása előtt egyedi információkra van szükségük. További információ: [SAML-alapú egyszeri bejelentkezés konfigurálása](configure-saml-single-sign-on.md).
- **Tanúsítványkezelő az SAML-alapú egyszeri bejelentkezéshez** – Ha engedélyezi az összevont SSO-t az alkalmazáshoz, az Azure ad alapértelmezés szerint három évig érvényes tanúsítványt hoz létre. Ha szükséges, testreszabhatja a tanúsítvány lejárati dátumát. Győződjön meg arról, hogy a tanúsítványok megújításához szükséges folyamatok vannak érvényben a lejárat előtt. További információ: [Azure ad-tanúsítványok kezelése](./manage-certificates-for-federated-single-sign-on.md).

### <a name="considerations-for-password-based-sso"></a>A jelszó-alapú egyszeri bejelentkezés szempontjai

Az Azure AD jelszó-alapú egyszeri bejelentkezéshez való használatához olyan böngésző-bővítményt kell telepíteni, amely biztonságosan kéri le a hitelesítő adatokat, és kitölti a bejelentkezési űrlapokat. Definiáljon egy mechanizmust a bővítmény a [támogatott böngészőkkel](../user-help/my-apps-portal-end-user-access.md)való üzembe helyezéséhez. A lehetőségek a következők:

- [Csoportházirend az Internet Explorerben](my-apps-deployment-plan.md)
- [Configuration Manager az Internet Explorerben](/configmgr/core/clients/deploy/deploy-clients-to-windows-computers)
- [A Chrome, a Firefox, a Microsoft Edge vagy az IE felhasználó által vezérelt letöltése és konfigurálása](../user-help/my-apps-portal-end-user-access.md)

További információ: [a jelszó egyszeri bejelentkezésének konfigurálása](./configure-password-single-sign-on-non-gallery-applications.md).

#### <a name="capturing-login-forms-metadata-for-applications-that-arent-in-the-gallery"></a>Bejelentkezési űrlapok metaadatainak rögzítése a katalógusban nem szereplő alkalmazásokhoz

A Microsoft támogatja a metaadatok rögzítését egy webalkalmazásban a jelszó-tárolóhoz (a Felhasználónév és a jelszó mezők rögzítése). Navigáljon a bejelentkezési URL-címre az alkalmazás konfigurálásának folyamata során az űrlap metaadatainak rögzítéséhez. Kérje meg az alkalmazás tulajdonosát a bejelentkezés pontos URL-címére. Ezeket az adatokat a rendszer a bejelentkezési folyamat során használja, és az Azure AD hitelesítő adatait az alkalmazáshoz a bejelentkezéskor rendeli hozzá.

További információ: [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure ad-vel? – jelszó-alapú egyszeri bejelentkezés](./what-is-single-sign-on.md).

#### <a name="indications-that-metadata-in-forms-needs-to-be-recaptured"></a>Azt jelzi, hogy az űrlapokon lévő metaadatokat újra kell rögzíteni

Amikor az alkalmazások megváltoztatják a HTML-elrendezést, előfordulhat, hogy újra rögzítenie kell a metaadatokat a módosítások módosításához. Gyakori tünetek, amelyek azt jelzik, hogy a HTML-elrendezés módosult:

- Azok a felhasználók, akik az alkalmazásra kattintva bejelentkeznek, "ragadt" a bejelentkezési oldalon
- A felhasználónévvel vagy jelszóval nem feltöltött felhasználók

#### <a name="shared-accounts"></a>Közös fiókok

A bejelentkezési perspektívából a megosztott fiókkal rendelkező alkalmazások nem különböznek egy olyan katalógus-alkalmazástól, amely a jelszó egyszeri bejelentkezését használja az egyes felhasználók számára. Azonban szükség van néhány további lépésre, ha egy alkalmazás tervezése és konfigurálása a megosztott fiókok használatát jelenti:

1. Az alkalmazás üzleti felhasználóival együttműködve dokumentálja a következőket:
   1. Azon felhasználók csoportja, akik az alkalmazást fogják használni
   1. A felhasználók készletéhez társított alkalmazásban meglévő hitelesítő adatok halmaza 
1. A felhasználói készlet és a hitelesítő adatok minden kombinációja esetében a követelmények alapján hozzon létre egy biztonsági csoportot a felhőben vagy a helyszínen.
1. A megosztott hitelesítő adatok alaphelyzetbe állítása. Miután az alkalmazást üzembe helyezte az Azure AD-ben, a felhasználóknak nincs szükségük a megosztott fiók jelszavára. Mivel az Azure AD tárolja a jelszót, érdemes lehet úgy beállítani, hogy nagyon hosszú és összetett legyen. 
1. Konfigurálja a jelszó automatikus átváltását, ha az alkalmazás támogatja azt. Így még a kezdeti telepítő rendszergazdája sem fogja tudni a megosztott fiók jelszavát. 

## <a name="plan-your-authentication-method"></a>A hitelesítési módszer megtervezése

A megfelelő hitelesítési módszer kiválasztása kulcsfontosságú első döntés az Azure AD Hybrid Identity megoldás létrehozásához. A Azure AD Connect használatával konfigurált hitelesítési módszer implementálása, amely a felhőben lévő felhasználókat is kiépíti.

A hitelesítési módszer kiválasztásához figyelembe kell vennie az időt, a meglévő infrastruktúrát, az összetettséget és a választott megvalósítás költségeit. Ezek a tényezők minden szervezet esetében eltérőek, és idővel változhatnak. Válassza ki azt, amelyik leginkább megfelel az adott forgatókönyvnek. További információ: [válassza ki a megfelelő hitelesítési módszert a Azure Active Directory Hybrid Identity megoldáshoz](../hybrid/choose-ad-authn.md).

## <a name="plan-your-security-and-governance"></a>A biztonság és irányítás megtervezése 

A biztonság és a beruházások új elsődleges feladata, hogy a hálózati környezetek egyre inkább porózusak és kevésbé hatékonyak legyenek a BYOD-eszközök és a felhőalapú alkalmazások robbanásával. 

### <a name="plan-access-reviews"></a>Hozzáférési felülvizsgálatok tervezése

A [hozzáférési felülvizsgálatok](../governance/create-access-review.md) lehetővé teszik a szervezetek számára a csoporttagságok hatékony kezelését, a vállalati alkalmazások elérését és a szerepkör-hozzárendeléseket. Érdemes rendszeresen megtervezni a felhasználói hozzáférés áttekintését, hogy csak a megfelelő személyek férhessenek hozzájuk.

A hozzáférési felülvizsgálatok beállítása során megtervezni kívánt főbb témakörök a következők:

1. Egy lépésszám azonosítása az üzleti igényeknek megfelelő hozzáférési felülvizsgálatokhoz. Ez akár hetente, havonta, évente vagy igény szerinti gyakorlatként is elvégezhető.

1. Az alkalmazás-hozzáférési jelentések véleményezőit képviselő csoportok létrehozása. Meg kell győződnie arról, hogy az érintettek többsége az alkalmazással és a hozzá tartozó felhasználói és használati esetekkel ismeri a hozzáférési felülvizsgálatok résztvevőit

1. A hozzáférési felülvizsgálat elvégzése magában foglalja az alkalmazás-hozzáférési engedélyek kiszámítását olyan felhasználók számára, akik már nem férnek hozzá. Tervezze meg a megtagadott felhasználók lehetséges támogatási kéréseinek kezelését. A törölt felhasználók az Azure AD-ben maradnak, és 30 napig törölve lesznek, amikor a rendszergazda szükség esetén visszaállíthatja őket. A felhasználók 30 nap után véglegesen törlődnek. A Azure Active Directory portál használatával a globális rendszergazda explicit módon véglegesen törölheti a nemrég törölt felhasználókat az adott időszak elérésekor.

### <a name="plan-auditing"></a>Terv naplózása

Az Azure AD olyan [jelentéseket biztosít, amelyek technikai és üzleti ismereteket tartalmaznak](../reports-monitoring/overview-reports.md). 

A biztonsági és a tevékenységgel kapcsolatos jelentések egyaránt elérhetők. A biztonsági jelentések megjelenítik a kockázatnak megjelölt felhasználókat és a kockázatos bejelentkezéseket. A tevékenységgel kapcsolatos jelentések segítenek megérteni a szervezet felhasználói viselkedését a bejelentkezési tevékenység részletes ismertetésével, valamint az összes bejelentkezés naplózási nyomvonalának biztosításával. A jelentések segítségével kezelheti a kockázatokat, növelheti a hatékonyságot és figyelheti a megfelelőséget.

| Jelentés típusa | Hozzáférési felülvizsgálat | Biztonsági jelentések | Bejelentkezési jelentés |
|-------------|---------------|------------------|----------------|
| Használat áttekintése | Alkalmazás engedélyei és használata. | Potenciálisan feltört fiókok | Ki fér hozzá az alkalmazásokhoz |
| Lehetséges műveletek | Hozzáférés naplózása; engedélyek visszavonása | Hozzáférés visszavonása; biztonsági beállítások visszaállításának kényszerítése | Hozzáférés visszavonása |

Az Azure AD 30 napig megőrzi a legtöbb naplózási adatát, és az Azure felügyeleti portálon vagy egy API-n keresztül elérhetővé teszi az adatelemzési rendszerekbe való letöltést.

### <a name="consider-using-microsoft-cloud-application-security"></a>Megfontolandó Microsoft Cloud alkalmazás biztonságának használata

A Microsoft Cloud App Security (MCAS) egy felhőalapú hozzáférési biztonsági közvetítő (CASB) megoldás. Lehetővé teszi a felhőalapú alkalmazások és szolgáltatások megismerését, és kifinomult elemzéseket biztosít a előforduló kiberfenyegetésekkel kapcsolatban azonosításához és leküzdéséhez, valamint az adattovábbítások szabályozását.

A MCAS üzembe helyezése a következőket teszi lehetővé:

- A Cloud Discovery használatával leképezheti és azonosíthatja a felhőalapú környezetét és a szervezete által használt felhőalapú alkalmazásokat.
- A felhőben lévő alkalmazások szankcionálása és az azokra való nem szankcionálás
- Használjon egyszerűen telepíthető alkalmazás-összekötőket, amelyek kihasználják a szolgáltatói API-kat, és megtekintheti és irányíthatja azokat az alkalmazásokat, amelyekhez csatlakozik
- A feltételes hozzáférést biztosító alkalmazás-vezérlő Protection használatával valós idejű láthatóságot érhet el, és szabályozhatja a Felhőbeli alkalmazások hozzáférését és tevékenységeit
- Segíti a folyamatos vezérlést a beállítással, majd folyamatosan pontosíthatja a szabályzatokat.

A Microsoft Cloud Application Security (MCAS) munkamenet-vezérlő bármely operációs rendszer bármely jelentős platformján elérhető bármely böngészőhöz. A Mobile apps és az asztali alkalmazások blokkolása vagy engedélyezése is lehetséges. Az Azure AD-vel való natív integrálással minden SAML-vel konfigurált alkalmazás, illetve az Azure AD egyszeri bejelentkezéssel rendelkező Open ID-alapú csatlakozási alkalmazások is támogatottak, beleértve [több kiemelt alkalmazást](/cloud-app-security/proxy-intro-aad)is.

További információ a MCAS: a [Microsoft Cloud app Security áttekintése](/cloud-app-security/what-is-cloud-app-security). A MCAS egy felhasználó-alapú előfizetési szolgáltatás. A licencelés részleteit a [MCAS licencelési adatlapjában](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RE2NXYO)tekintheti át.

### <a name="use-conditional-access"></a>Feltételes hozzáférés használata

A feltételes hozzáférés segítségével automatizálhatja a feltételeken alapuló hozzáférés-vezérlési döntéseket a felhőalapú alkalmazásokhoz.

A feltételes hozzáférési házirendeket az első tényezős hitelesítés befejeződése után kényszeríti ki a rendszer. Ezért a feltételes hozzáférés nem az első vonali védelem, mint például a szolgáltatásmegtagadási (DoS) támadások, de ezekből az eseményekről származó jelek használatával határozhatja meg a hozzáférést. Például a bejelentkezési kockázati szint, a kérelem helye, és így tovább használható. További információ a feltételes hozzáférésről: [Áttekintés](../conditional-access/plan-conditional-access.md) és [telepítési terv](../conditional-access/plan-conditional-access.md).

## <a name="azure-sso-technical-requirements"></a>Az Azure SSO műszaki követelményei

A következő szakasz részletesen ismerteti az adott alkalmazás konfigurálásának követelményeit, beleértve a szükséges környezet (eke) t, a végpontokat, a jogcímek leképezését, a szükséges attribútumokat, a tanúsítványokat és a használt protokollokat. Ezekre az információkra szüksége lesz az SSO konfigurálásához az [Azure ad-portálon](https://portal.azure.com/).

### <a name="authentication-mechanism-details"></a>Hitelesítési mechanizmus részletei

Az összes előre integrált SaaS-alkalmazáshoz a Microsoft egy oktatóanyagot biztosít, és nem lesz szüksége ezekre az információkra. Ha az alkalmazás nem szerepel az alkalmazás-piactéren/katalóguson, akkor előfordulhat, hogy az alábbi adatokat kell összegyűjtenie:

- **Az alkalmazás által az egyszeri bejelentkezéshez használt aktuális identitás-szolgáltató, ha van ilyen** , például: AD FS, PingFederate, okta
- **A cél alkalmazás által támogatott protokollok** – például SAML 2,0, OpenID Connect, OAuth, Forms-Based Auth, ws-Fed, WS-Trust
- A **protokoll az Azure ad-vel van konfigurálva** , például SAML 2,0 vagy 1,1, OpenID Connect, OAuth, Forms-Based, WS-Fed

### <a name="attribute-requirements"></a>Attribútumokra vonatkozó követelmények

Az Azure AD felhasználói objektumai és az egyes SaaS-alkalmazások felhasználói objektumai között van egy előre konfigurált attribútum és attribútum-hozzárendelés. Egyes alkalmazások más típusú objektumokat, például csoportokat kezelnek. Tervezze meg a felhasználói attribútumok leképezését az Azure AD-ből az alkalmazásba, és [szabja testre az alapértelmezett attribútum-hozzárendeléseket](../app-provisioning/customize-application-attributes.md) az üzleti igényeknek megfelelően.

### <a name="certificate-requirements"></a>Tanúsítványkövetelmények

Az alkalmazás tanúsítványának naprakésznek kell lennie, vagy fennáll a kockázata annak, hogy a felhasználók nem férhetnek hozzá az alkalmazáshoz. A legtöbb SaaS-alkalmazás tanúsítványa 36 hónapig jó. A tanúsítvány időtartamát az alkalmazás paneljén módosíthatja. Győződjön meg arról, hogy dokumentálja a lejáratot, és tudja, hogyan fogja kezelni a tanúsítvány megújítását. 

A tanúsítványok kezelésének két módja van. 

- **Automatikus tanúsítvány-rollover** – a Microsoft támogatja [Az Azure ad-ben az aláíró kulcsok átváltását](../develop/active-directory-signing-key-rollover.md). Habár a tanúsítványok kezelésének előnyben részesített módja, nem minden ISV támogatja ezt a forgatókönyvet.

- **Manuális frissítés** – minden alkalmazás saját tanúsítvánnyal rendelkezik, amely a definiálása alapján lejár. Az alkalmazás tanúsítványának lejárata előtt hozzon létre egy új tanúsítványt, és küldje el az ISV-nek. Ezeket az információkat az összevonási metaadatokból lehet kihúzni. [További információ az összevonási metaadatokról itt olvasható.](../azuread-dev/azure-ad-federation-metadata.md)

## <a name="implement-sso"></a>Egyszeri bejelentkezés implementálása

A következő fázisok segítségével tervezze meg és telepítse üzembe megoldását a szervezetében:

### <a name="user-configuration-for-sso"></a>Felhasználói konfiguráció egyszeri bejelentkezéshez

- **A teszt felhasználóinak azonosítása**

   Lépjen kapcsolatba az alkalmazás tulajdonosával, és kérje meg, hogy hozzon létre legalább három tesztelési felhasználót az alkalmazáson belül. Győződjön meg arról, hogy az elsődleges azonosítóként használni kívánt információ helyesen van feltöltve, és megfelel az Azure AD-ben elérhető attribútumnak. A legtöbb esetben ez az SAML-alapú alkalmazások "NameID"-ra lesz leképezve. JWT-tokenek esetén ez a "preferred_username."
   
   Hozza létre a felhasználót az Azure AD-ben manuálisan felhőalapú felhasználóként, vagy szinkronizálja a felhasználót a helyszínen a Azure AD Connect Sync Engine használatával. Győződjön meg arról, hogy az információ megegyezik az alkalmazásnak küldött jogcímekkel.

- **Egyszeri bejelentkezés konfigurálása**

   Az [alkalmazások listájából](../saas-apps/tutorial-list.md)keresse meg és nyissa meg az alkalmazás egyszeri bejelentkezésre vonatkozó oktatóanyagát, majd kövesse az oktatóanyag lépéseit a SaaS-alkalmazás sikeres konfigurálásához.

   Ha nem találja az alkalmazást, tekintse meg az [egyéni alkalmazás dokumentációját](./configure-saml-single-sign-on.md). Ez végigvezeti egy olyan alkalmazás hozzáadásán, amely nem az Azure AD-galériában található.

   Igény szerint a vállalati alkalmazás SAML-jogkivonatában kiállított jogcímeket a [Microsoft útmutatási dokumentációja](../develop/active-directory-claims-mapping.md)alapján is használhatja. Gondoskodjon arról, hogy az alkalmazáshoz tartozó SAML-válaszban milyen térképeket kapjon. Ha a konfigurálás során problémák merülnek fel, kövesse az [SSO-integráció hibakeresését ismertető](./debug-saml-sso-issues.md)útmutatót.

### <a name="provide-sso-change-communications-to-end-users"></a>Egyszeri bejelentkezéses változások kommunikációjának biztosítása a végfelhasználók számára

A kommunikációs terv megvalósítása. Győződjön meg arról, hogy a végfelhasználók tudják, hogy egy változás érkezik, amikor megérkezett, mi a teendő, és hogyan kérhet segítséget.

### <a name="verify-end-user-scenarios-for-sso"></a>Végfelhasználói forgatókönyvek ellenőrzése egyszeri bejelentkezéshez

Az alábbi tesztelési esetek használatával teszteket végezhet a vállalat által birtokolt és a személyes eszközökön, így biztosítva, hogy az SSO-konfigurációk a várt módon működnek. Az alábbi forgatókönyvek azt feltételezik, hogy egy felhasználó egy alkalmazás URL-címére navigál, és a szolgáltató által kezdeményezett hitelesítési folyamaton (SP által kezdeményezett hitelesítési folyamaton) keresztül zajlik.

| Eset | A várt eredmény az SP által kezdeményezett hitelesítési folyamathoz felhasználó szerint |
|----------|---------------------------------------------------|
| Jelentkezzen be az alkalmazásba az IE-vel a Corpnet-on. | Az integrált Windows-hitelesítés (IWA) további kérések nélkül történik. |
| Jelentkezzen be az alkalmazásba az IE-ben, miközben az új bejelentkezési kísérlettel Corpnet ki. | Űrlapalapú kérés a AD FS kiszolgálón. A felhasználó sikeresen bejelentkezik, és a böngésző kéri az MFA-t. |
| Jelentkezzen be az alkalmazásba az IE-ben, miközben a Corpnet egy aktuális munkamenettel rendelkezik, és soha nem végezte el az MFA-t | A felhasználó nem kap promptot az első tényezőhöz. A felhasználó kérést kap az MFA-hoz. |
| Jelentkezzen be az alkalmazásba az IE-ben, miközben a Corpnet egy aktuális munkamenettel rendelkezik, és már végrehajtotta az MFA-t ebben a munkamenetben. | A felhasználó nem kap promptot az első tényezőhöz. A felhasználó nem kap MFA-t. A felhasználó SSO az alkalmazásba. |
| Jelentkezzen be az alkalmazásba a Chrome/Firefox/Safari használatával, és kapcsolja ki a Corpnet egy aktuális munkamenettel, és már végrehajtotta az MFA-t ebben a munkamenetben. | A felhasználó nem kap promptot az első tényezőhöz. A felhasználó nem kap MFA-t. Felhasználói egyszeri bejelentkezés alkalmazása az alkalmazásba. |
| Jelentkezzen be az alkalmazásba a Chrome/Firefox/Safari használatával a Corpnet új bejelentkezési kísérlettel való kikapcsolásával. | Űrlapalapú kérés a AD FS kiszolgálón. A felhasználó sikeresen bejelentkezik, és a böngésző kéri az MFA-t. |
| Jelentkezzen be az alkalmazásba a Chrome/Firefox használatával a vállalati hálózaton egy aktuális munkamenettel. | A felhasználó nem kap promptot az első tényezőhöz. A felhasználó nem kap MFA-t. Felhasználói egyszeri bejelentkezés alkalmazása az alkalmazásba. |
| Jelentkezzen be az alkalmazásba az Application Mobile alkalmazással egy új bejelentkezési kísérlettel. | Űrlapalapú kérés a AD FS kiszolgálón. A felhasználó sikeresen bejelentkezett és ADAL az MFA-ra vonatkozó ügyfelekre vonatkozó kéréseket. |
| A jogosulatlan felhasználó megpróbál bejelentkezni az alkalmazásba bejelentkezési URL-címmel. | Űrlapalapú kérés a AD FS kiszolgálón. A felhasználó nem tud bejelentkezni az első tényezővel. |
| A jogosultsággal rendelkező felhasználó megpróbál bejelentkezni, de helytelen jelszót ad meg. | A felhasználó az alkalmazás URL-címére navigál, és helytelen felhasználónevet vagy jelszót kap. |
| A jogosult felhasználó egy e-mailben kattint a hivatkozásra, és már hitelesítve van. | A felhasználó az URL-címre kattint, és bejelentkezett az alkalmazásba további kérések nélkül. |
| A jogosult felhasználó egy e-mailben rákattint a hivatkozásra, és még nincs hitelesítve. | A felhasználó az URL-címre kattint, és az első tényezővel való hitelesítésre kéri. |
| A felhatalmazott felhasználó bejelentkezik az Application Mobile App (SP által kezdeményezett) alkalmazásba egy új bejelentkezési kísérlettel. | Űrlapalapú kérés a AD FS kiszolgálón. A felhasználó sikeresen bejelentkezett és ADAL az MFA-ra vonatkozó ügyfelekre vonatkozó kéréseket. |
| A jogosulatlan felhasználó megpróbál bejelentkezni az alkalmazásba bejelentkezési URL-címmel (SP-kezdeményezéssel). | Űrlapalapú kérés a AD FS kiszolgálón. A felhasználó nem tud bejelentkezni az első tényezővel. |
| A jogosultsággal rendelkező felhasználó megpróbál bejelentkezni, de helytelen jelszót ad meg.| A felhasználó az alkalmazás URL-címére navigál, és helytelen felhasználónevet vagy jelszót kap. |
| Az engedélyezve lévő felhasználó kijelentkezik, majd ismét bejelentkezik. | Ha a kijelentkezési URL-cím konfigurálva van, a felhasználó kijelentkezik az összes szolgáltatásból, és felszólítja a hitelesítést. |
| Az engedélyezve lévő felhasználó kijelentkezik, majd ismét bejelentkezik. | Ha nincs konfigurálva a kijelentkezési URL-cím, a rendszer automatikusan visszaküldi a felhasználót a meglévő, Azure AD böngésző-munkamenetből származó token használatával. |
| A jogosult felhasználó egy e-mailben kattint a hivatkozásra, és már hitelesítve van. | A felhasználó az URL-címre kattint, és bejelentkezett az alkalmazásba további kérések nélkül. |
| A jogosult felhasználó egy e-mailben rákattint a hivatkozásra, és még nincs hitelesítve. | A felhasználó az URL-címre kattint, és az első tényezővel való hitelesítésre kéri. |

## <a name="manage-sso"></a>Egyszeri bejelentkezés kezelése

Ez a szakasz az egyszeri bejelentkezés sikeres kezelésének követelményeit és javaslatait ismerteti.

### <a name="required-administrative-roles"></a>Szükséges rendszergazdai szerepkörök

Mindig a legkevesebb engedélyekkel rendelkező szerepkört használja a szükséges feladat végrehajtásához Azure Active Directoryon belül. A Microsoft azt javasolja, [hogy tekintse át az elérhető különböző szerepköröket](../roles/permissions-reference.md) , és válassza ki a megfelelőt az alkalmazás minden egyes Personának megoldásához. Előfordulhat, hogy bizonyos szerepköröket átmenetileg kell alkalmazni, és el kell távolítani az üzembe helyezés befejeződése után.

| Persona| Szerepkörök | Azure AD-szerepkör (ha szükséges) |
|--------|-------|-----------------------------|
| Ügyfélszolgálati rendszergazda | 1. szintű támogatás | Nincsenek |
| Identity admin | Konfigurálás és hibakeresés az Azure AD-t érintő problémák esetén | Globális rendszergazda |
| Alkalmazás-rendszergazda | Felhasználói igazolás az alkalmazásban, az engedélyekkel rendelkező felhasználók konfigurációja | Nincsenek |
| Infrastruktúra-rendszergazdák | Tanúsítvány-átváltási tulajdonos | Globális rendszergazda |
| Üzleti tulajdonos/érintett fél | Felhasználói igazolás az alkalmazásban, az engedélyekkel rendelkező felhasználók konfigurációja | Nincsenek |

Javasoljuk, hogy az [Privileged Identity Management](../privileged-identity-management/pim-configure.md) (PIM) használatával kezelje a szerepköröket, hogy további naplózási, vezérlési és hozzáférési felülvizsgálatot nyújtson a felhasználók számára a címtár-jogosultságokkal.

### <a name="sso-certificate-lifecycle-management"></a>SSO-tanúsítványok életciklusának kezelése

Fontos, hogy azonosítsa a megfelelő szerepköröket és e-mail-terjesztési listát, amely az aláíró tanúsítvány életciklusának kezelésével foglalkozik az Azure AD és az egyszeri bejelentkezéssel konfigurált alkalmazás között. Íme néhány, a következőkben javasolt kulcsfontosságú szerepkör:

- A felhasználó tulajdonságainak az alkalmazásban való frissítésének tulajdonosa
- A tulajdonos hívása az Application break/fix támogatáshoz
- Szigorúan figyelt e-mailek terjesztési listája a tanúsítványokkal kapcsolatos változások értesítéseihez

A tanúsítvány maximális élettartama három év. Javasoljuk, hogy hozzon létre egy folyamatot az Azure AD és az alkalmazás közötti tanúsítvány-változás kezelésével kapcsolatban. Ez segít megakadályozni vagy csökkenteni a leállást egy tanúsítvány lejárata vagy a tanúsítvány átváltásának kényszerítése miatt.

### <a name="rollback-process"></a>Visszaállítási folyamat

Miután elvégezte a tesztelést a tesztelési esetek alapján, itt az ideje, hogy éles környezetbe lépjen az alkalmazásával. Az éles környezetbe való áttéréssel megtervezheti a tervezett és tesztelt konfigurációkat az üzemi bérlőben, és elvégezheti a felhasználók számára. Fontos azonban megtervezni, hogy mi a teendő abban az esetben, ha a telepítés nem a tervezett módon történik. Ha az egyszeri bejelentkezés konfigurálása nem sikerül az üzembe helyezés során, tisztában kell lennie azzal, hogy miként csökkentheti az esetleges kimaradásokat, és csökkentheti a felhasználókra gyakorolt hatást.

Az alkalmazáson belüli hitelesítési módszerek rendelkezésre állása határozza meg a legjobb stratégiát. Mindig győződjön meg arról, hogy az alkalmazás tulajdonosainak részletes dokumentációja van, pontosan hogyan lehet visszatérni az eredeti bejelentkezési konfiguráció állapotához, ha a telepítés problémákba ütközik.

- **Ha az alkalmazás több identitás-szolgáltatót is támogat**, például az LDAP és a AD FS és a ping, ne törölje a meglévő SSO-konfigurációt a bevezetés során. Ehelyett tiltsa le az áttelepítés során, ha később újra kell váltania. 

- **Ha az alkalmazás nem támogatja több IDP** használatát, de lehetővé teszi a felhasználók számára az űrlapalapú hitelesítés (felhasználónév/jelszó) használatával történő bejelentkezést, ügyeljen arra, hogy a felhasználók visszatérhetnek ehhez a megközelítéshez abban az esetben, ha az új SSO-konfiguráció bevezetése meghiúsul.

### <a name="access-management"></a>Hozzáférés-kezelés

Azt javasoljuk, hogy az erőforrásokhoz való hozzáférés kezelésekor méretezhető megközelítést válasszon. A gyakori megközelítések közé tartozik a helyszíni csoportok használata Azure AD Connect használatával történő szinkronizálással, [dinamikus csoportok létrehozása az Azure ad-ben a felhasználói attribútumok alapján](../enterprise-users/groups-dynamic-membership.md), vagy az erőforrás-tulajdonos által kezelt önkiszolgáló csoportok létrehozása az Azure ad [-](../enterprise-users/groups-self-service-management.md) ben.

### <a name="monitor-security"></a>Biztonság figyelése

Javasoljuk, hogy állítson be egy normál lépésszám-t, amelyben áttekinti a SaaS app Security különböző szempontjait, és hajtson végre minden szükséges javító műveletet.

### <a name="troubleshooting"></a>Hibaelhárítás

A következő hivatkozások hibaelhárítási forgatókönyveket mutatnak be. Előfordulhat, hogy létre kell hoznia egy konkrét útmutatót a támogatási személyzetnek, amely tartalmazza ezeket a forgatókönyveket és a kijavításának lépéseit.

#### <a name="consent-issues"></a>Beleegyező problémák

- [Váratlan beleegyezett hiba](./application-sign-in-unexpected-user-consent-prompt.md)

- [Hiba a felhasználói hozzájárulással](./application-sign-in-unexpected-user-consent-error.md)

#### <a name="sign-in-issues"></a>Bejelentkezési problémák

- [Problémák az egyéni portálról való bejelentkezéskor](./application-sign-in-other-problem-access-panel.md)

- [Probléma a Saját alkalmazásokból való bejelentkezésnél](./application-sign-in-other-problem-access-panel.md)

- [Hiba az alkalmazás bejelentkezési oldalán](./application-sign-in-problem-application-error.md)

- [Probléma a Microsoft-alkalmazásba való bejelentkezéskor](./application-sign-in-problem-first-party-microsoft.md)

#### <a name="sso-issues-for-applications"></a>Egyszeri bejelentkezéssel kapcsolatos problémák az alkalmazásokhoz

- [Probléma a jelszó egyszeri bejelentkezésével az alkalmazásokhoz](./troubleshoot-password-based-sso.md) 

- [Problémák az SAML-alapú egyszeri bejelentkezésre konfigurált alkalmazásokba való bejelentkezés során](/troubleshoot/azure/active-directory/troubleshoot-sign-in-saml-based-apps)   


## <a name="next-steps"></a>Következő lépések

[SAML-alapú SSO hibakeresése](./debug-saml-sso-issues.md)

[Jogcím-hozzárendelés alkalmazások számára a PowerShell használatával](../develop/active-directory-claims-mapping.md)

[SAML-jogkivonatban kiadott jogcímek testreszabása](../develop/active-directory-saml-claims-customization.md)

[Egyszeri bejelentkezéses SAML protokoll](../develop/single-sign-on-saml-protocol.md)

[Egy Sign-Out SAML protokoll](../develop/single-sign-out-saml-protocol.md)

[Azure ad B2B](../external-identities/what-is-b2b.md) (külső felhasználóknak, például partnereknek és szállítóknak)

[Azure AD-beli feltételes hozzáférés](../conditional-access/overview.md)

[Azure Identity Protection](../identity-protection/overview-identity-protection.md)

[Egyszeri bejelentkezéses hozzáférés](./what-is-single-sign-on.md)

[Alkalmazás egyszeri bejelentkezésének oktatóanyaga](../saas-apps/tutorial-list.md)

[Tanulmány letöltése – egyszeri bejelentkezési telepítési terv](https://aka.ms/SSODeploymentPlan)