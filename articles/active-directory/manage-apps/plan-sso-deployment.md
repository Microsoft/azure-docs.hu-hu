---
title: Egyszeri Azure Active Directory üzembe helyezésének megterve
description: Útmutató az SSO tervezéséhez, üzembe helyezéséhez és kezeléséhez a szervezetben.
services: active-directory
author: iantheninja
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.date: 06/10/2020
ms.author: iangithinji
ms.reviewer: jeedes
ms.collection: M365-identity-device-management
ms.custom: has-adal-ref
ms.openlocfilehash: 3f395eaf21dce0077c1239ef2251973aeb5faa70
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2021
ms.locfileid: "107375610"
---
# <a name="plan-a-single-sign-on-deployment"></a>Egyszeri bejelentkezés üzembe helyezésének tervezése

Az egyszeri bejelentkezés (SSO) azt jelenti, hogy a felhasználónak minden alkalmazáshoz és erőforráshoz csak egyszer kell bejelentkeznie egyetlen felhasználói fiókkal. Az SSO-val a felhasználók anélkül férhetnek hozzá az összes szükséges alkalmazáshoz, hogy másodszor is hitelesíteniük kell magukat.

## <a name="benefits-of-sso"></a>Az SSO előnyei

Az egyszeri bejelentkezés (SSO) növeli a biztonságot és a kényelmes használatot, amikor a felhasználók az Azure AD-Azure Active Directory jelentkeznek be az alkalmazásokba. 

Számos szervezet használ szolgáltatott szoftvereket (SaaS) a végfelhasználók termelékenységének növelése érdekében, például a Microsoft 365, a Box és a Salesforce alkalmazásokat. Az it-munkatársaknak korábban egyénileg kellett létrehoznia és frissíteniük a felhasználói fiókokat az egyes SaaS-alkalmazásokban, és a felhasználóknak mindegyikhez meg kellett jegyezniük a jelszót.

A Azure Marketplace több mint 3000 alkalmazással rendelkezik előre integrált SSO-kapcsolatokkal, így könnyen integrálhatja őket a bérlőbe.

## <a name="licensing"></a>Licencek

- **Azure AD-licencelés** – Az előre integrált SaaS-alkalmazásokhoz ingyenes az SSO. A címtárban lévő objektumok száma és a telepíteni kívánt funkciók azonban további licenceket is igényelnek. A licenckövetelmények teljes listáját lásd: [Azure Active Directory díjszabása.](https://azure.microsoft.com/pricing/details/active-directory/)
- **Alkalmazáslicencelés** – Az üzleti igényeinek megfelelően szüksége lesz az SaaS-alkalmazásokhoz szükséges licencekre. Az alkalmazás tulajdonosával együtt állapítsa meg, hogy az alkalmazáshoz rendelt felhasználók megfelelő licenccel rendelkezik-e a szerepkörükhöz az alkalmazásban. Ha az Azure AD a szerepkörök alapján kezeli az automatikus kiépítést, az Azure AD-ban hozzárendelt szerepköröknek igazodniuk kell az alkalmazáson belüli licencek számához. Az alkalmazásban birtokolt licencek nem megfelelő száma hibákhoz vezethet a felhasználók kiépítése/frissítése során.

## <a name="plan-your-sso-team"></a>Az SSO-csapat megterve

- **A megfelelő érdekelt felek bevonását** – Ha a technológiai projektek meghiúsulnak, annak oka általában a hatásra, az eredményekre és a felelősségi körökre vonatkozó nem egyező elvárások. A buktatók elkerülése érdekében győződjön meg [arról,](../fundamentals/active-directory-deployment-plans.md) hogy a megfelelő érdekelt feleket és az érdekelt feleket is be fogja vonni a szerepükbe.
- **Kommunikáció megterve** – A kommunikáció kritikus fontosságú az új szolgáltatások sikerességéhez. Proaktív módon kommunikálhat a felhasználókkal arról, hogyan változik a felhasználói élmény, mikor változik, és hogyan lehet támogatást szerezni, ha problémákat tapasztalnak. Tekintse át a beállításokat, hogy a végfelhasználók hogyan férhetnek hozzá az [SSO-t](end-user-experiences.md)használó alkalmazásaikhoz, és a kiválasztott beállításoknak megfelelő kommunikációt készítse el. 

## <a name="plan-your-sso-protocol"></a>Az SSO protokoll megterve

Az összevonási protokollokra épülő SSO-implementáció javítja a biztonságot, a megbízhatóságot és a végfelhasználói élményt, és könnyebben megvalósítható. Számos alkalmazás előre integrálva van az Azure AD-be, és részletes útmutatók [érhetők el.](../saas-apps/tutorial-list.md) Ezeket az oldalon találja [Azure Marketplace.](https://azuremarketplace.microsoft.com/marketplace/) Az egyes SSO-metódusokkal kapcsolatos részletes információkat a következő cikkben talál: Egyszeri bejelentkezés az alkalmazásokba [a Azure Active Directory.](what-is-single-sign-on.md)

Két elsődleges módszer használhatja a felhasználókat arra, hogy egyszeri bejelentkezést engedélyezzenek az alkalmazásokba:

- **Összevont egyszeri bejelentkezéssel** Az Azure AD az Azure AD-fiókkal hitelesíti a felhasználót az alkalmazásban. Ez a módszer az olyan protokollokat támogató alkalmazások esetében támogatott, mint az SAML 2.0, a WS-Federation vagy a OpenID Connect, és ez az egyszeri bejelentkezés leggazdagabb módja. Javasoljuk, hogy a jelszóalapú SSO és az ADFS helyett használja az összevont SSO-t az Azure AD-val, ha azt egy alkalmazás támogatja.

- **A jelszóalapú egyszeri** bejelentkezési felhasználók az első alkalommal, felhasználónévvel és jelszóval jelentkeznek be az alkalmazásba. Az első bejelentkezés után az Azure AD adja meg a felhasználónevet és a jelszót az alkalmazásnak. A jelszóalapú egyszeri bejelentkezés biztonságos alkalmazásjelszó-tárolást és visszajátszást tesz lehetővé webböngészőbővítmény vagy mobilalkalmazás használatával. Ez a lehetőség kihasználja az alkalmazás által biztosított meglévő bejelentkezési folyamatot, lehetővé teszi a rendszergazda számára a jelszavak kezelését, és nem követeli meg, hogy a felhasználó tudja a jelszót.

### <a name="considerations-for-federation-based-sso"></a>Összevonás-alapú SSO-val kapcsolatos szempontok

- **Az OpenID Connect és az OAuth** használata – Ha az alkalmazás, amelyhez csatlakozik, támogatja azt, az OIDC/OAuth 2.0 metódussal engedélyezheti az SSO-t az alkalmazás számára. Ez a módszer kevesebb konfigurációt igényel, és gazdagabb felhasználói élményt tesz lehetővé. További információ: [OAuth 2.0](../develop/v2-oauth2-auth-code-flow.md), [OpenID Connect 1.0](../develop/v2-protocols-oidc.md), [Azure Active Directory fejlesztői útmutató.](../develop/index.yml)
- **Végpontkonfigurációk SAML-alapú SSO-hoz** – Ha SAML-t használ, a fejlesztőknek konkrét információkra lesz szükségük az alkalmazás konfigurálása előtt. További információ: [SAML-alapú egyszeri bejelentkezés konfigurálása.](configure-saml-single-sign-on.md)
- **Tanúsítványkezelés SAML-alapú SSO-hoz** – Ha engedélyezi az összevont SSO-t az alkalmazáshoz, az Azure AD létrehoz egy alapértelmezés szerint három évig érvényes tanúsítványt. Szükség esetén testre szabhatja a tanúsítvány lejárati dátumát. Győződjön meg arról, hogy a tanúsítványok lejáratuk előtt megújulnak folyamatok. További információ: [Az Azure AD tanúsítványok kezelése.](./manage-certificates-for-federated-single-sign-on.md)

### <a name="considerations-for-password-based-sso"></a>A jelszóalapú SSO-val kapcsolatos szempontok

Az Azure AD jelszóalapú SSO-hoz való használatához telepíteni kell egy böngészőbővítményt, amely biztonságosan lekéri a hitelesítő adatokat, és kitölti a bejelentkezési űrlapokat. Határozzon meg egy mechanizmust a bővítmény nagy léptékű üzembe helyezéséhez a [támogatott böngészőkkel.](../user-help/my-apps-portal-end-user-access.md) A lehetőségek a következők:

- [Csoportházirend a Internet Explorer](my-apps-deployment-plan.md)
- [Konfigurációkezelő a Internet Explorer](/configmgr/core/clients/deploy/deploy-clients-to-windows-computers)
- [Felhasználó által vezérelt letöltés és konfiguráció Chrome, Firefox, Microsoft Edge vagy IE böngészőhöz](../user-help/my-apps-portal-end-user-access.md)

További tudnivalókért lásd: Jelszavas egyszeri [bejelentkezés konfigurálása.](./configure-password-single-sign-on-non-gallery-applications.md)

#### <a name="capturing-login-forms-metadata-for-applications-that-arent-in-the-gallery"></a>A katalógusban nem található alkalmazások bejelentkezési űrlap-metaadatainak rögzítése

A Microsoft támogatja a metaadatok webalkalmazáson való rögzítését jelszótartóként (a felhasználónév és a jelszó mezők rögzítését). Az űrlap metaadatainak rögzítésére való konfigurálás során lépjen a bejelentkezési URL-címre. Kérdezze meg az alkalmazás tulajdonosától a pontos bejelentkezési URL-címet. Ezek az információk a bejelentkezési folyamat során használatosak, és az Azure AD-beli hitelesítő adatokat leképezik az alkalmazásra a bejelentkezés során.

További információ: Mi az alkalmazás-hozzáférés és az SSO az [Azure AD-val? – Jelszóalapú SSO.](./what-is-single-sign-on.md)

#### <a name="indications-that-metadata-in-forms-needs-to-be-recaptured"></a>Arra utaló jelek, hogy az űrlapok metaadatait újra be kell fogni

Amikor az alkalmazások módosítják a HTML-elrendezésüket, előfordulhat, hogy újra be kell állítania a metaadatokat, hogy igazodni tudjanak a módosításokhoz. A HTML-elrendezés változását jelző gyakori tünetek a következők:

- Azok a felhasználók, akik azt jelentik, hogy az alkalmazásra való kattintás "elakad" a bejelentkezési oldalon
- Felhasználók, akik azt jelentik, hogy a felhasználónév vagy a jelszó nincs feltöltve

#### <a name="shared-accounts"></a>Közös fiókok

A bejelentkezés szempontjából a megosztott fiókkal rendelkező alkalmazások nem különböznek az egyes felhasználók jelszavas SSO-t használó katalógusalkalmazástól. A megosztott fiókok használatára szánt alkalmazások tervezésekor és konfigurálásakor azonban további lépésekre is szükség van:

1. Az alkalmazás üzleti felhasználóival való munka során dokumentálja a következőket:
   1. A szervezetben az alkalmazást használó felhasználók halmaza
   1. Meglévő hitelesítő adatok a felhasználói csoporthoz társított alkalmazásban 
1. A felhasználói csoportok és a hitelesítő adatok minden kombinációjához hozzon létre egy biztonsági csoportot a felhőben vagy a helyszínen az igényeinek megfelelően.
1. Állítsa alaphelyzetbe a megosztott hitelesítő adatokat. Az alkalmazás Azure AD-beli üzembe helyezése után az egyéneknek nincs szükségük a megosztott fiók jelszavára. Mivel az Azure AD fogja tárolni a jelszót, érdemes lehet nagyon hosszúra és összetettre megadni. 
1. Konfigurálja a jelszó automatikusváltását, ha az alkalmazás támogatja. Így még a kezdeti beállítást el nem telepítő rendszergazda sem fogja tudni a megosztott fiók jelszavát. 

## <a name="plan-your-authentication-method"></a>A hitelesítési módszer megtervesítése

A megfelelő hitelesítési módszer kiválasztása kulcsfontosságú első döntés egy hibrid Azure AD-identitásmegoldás beállításakor. Implementálja a konfigurált hitelesítési módszert a Azure AD Connect, amely a felhasználókat is kiállítja a felhőben.

A hitelesítési módszer kiválasztásakor figyelembe kell vennie a választott megoldáshoz szükséges időt, meglévő infrastruktúrát, összetettséget és költségeket. Ezek a tényezők minden szervezetben eltérnek, és idővel változhatnak. Azt válassza, amely a leginkább megfelel az adott forgatókönyvnek. További információ: A hibrid [identitásmegoldáshoz](../hybrid/choose-ad-authn.md)megfelelő hitelesítési módszer Azure Active Directory kiválasztása.

## <a name="plan-your-security-and-governance"></a>A biztonság és a szabályozás megterve 

Az identitás az új elsődleges hely a biztonsági figyelés és a befektetések terén, mivel a szegélyhálózatok egyre nehezebben és kevésbé hatékonyak a BYOD-eszközök és felhőalapú alkalmazások robbanása miatt. 

### <a name="plan-access-reviews"></a>Hozzáférési felülvizsgálatok megtervezése

[A hozzáférési felülvizsgálatokkal](../governance/create-access-review.md) a szervezetek hatékonyan kezelhetik a csoporttagságokat, a vállalati alkalmazásokhoz való hozzáférést és a szerepkör-hozzárendeléseket. Érdemes rendszeresen áttekintenünk a felhasználói hozzáférést, hogy csak a megfelelő személyek férnek hozzá.

A hozzáférési felülvizsgálatok beállítása során megtervzen néhány kulcsfontosságú témakör:

1. Az üzleti igényeinek megfelelő hozzáférési felülvizsgálatok ütemének azonosítása. Ez lehet akár hetente egyszer, havonta, évente vagy igény szerinti gyakorlatként is.

1. Hozzon létre csoportokat, amelyek az alkalmazás-hozzáférési jelentések felülvizsgálóját képviselik. Gondoskodnia kell arról, hogy a hozzáférési felülvizsgálatok résztvevői a leginkább ismerik az alkalmazást és a célfelhasználókat és a felhasználói eseteket

1. A hozzáférési felülvizsgálat elvégzéséhez el kell kérnie az alkalmazás hozzáférési engedélyét az olyan felhasználóktól, akiknek már nincs szükségük hozzáférésre. Tervezze meg a megtagadott felhasználóktól származó lehetséges támogatási kérelmek kezelését. A törölt felhasználók 30 napig maradnak törölve az Azure AD-ben, amely idő alatt a rendszergazda szükség esetén visszaállíthatja őket. A felhasználók 30 nap után véglegesen törlődnek. A Azure Active Directory használatával a globális rendszergazda explicit módon véglegesen törölhet egy nemrégiben törölt felhasználót az időszak elérése előtt.

### <a name="plan-auditing"></a>Naplózás megtervezése

Az Azure AD műszaki és üzleti elemzéseket [tartalmazó jelentéseket biztosít.](../reports-monitoring/overview-reports.md) 

Mind a biztonsági, mind a tevékenységjelentések elérhetők. A biztonsági jelentések a kockázatosként megjelölt felhasználókat és a kockázatos bejelentkezéseket mutatják. A tevékenységjelentések segítenek megérteni a szervezet felhasználóinak viselkedését a bejelentkezési tevékenységek részletezése és az összes bejelentkezés naplózásának biztosítása által. A jelentések segítségével kezelheti a kockázatokat, növelheti a hatékonyságot és figyelheti a megfelelőséget.

| Jelentés típusa | Hozzáférési felülvizsgálat | Biztonsági jelentések | Bejelentkezési jelentés |
|-------------|---------------|------------------|----------------|
| Az áttekintéshez használható | Alkalmazásengedélyek és használat. | Potenciálisan sérült fiókok | Ki fér hozzá az alkalmazásokhoz? |
| Lehetséges műveletek | Hozzáférés naplózása; engedélyek visszavonása | Hozzáférés visszavonása; biztonsági visszaállítás kényszerítve | Hozzáférés visszavonása |

Az Azure AD 30 napig őrzi meg a legtöbb naplózási adatot, és elérhetővé teszi az adatokat az Azure felügyeleti portálon vagy egy API-n keresztül, hogy letöltsön az elemzési rendszerekbe.

### <a name="consider-using-microsoft-cloud-application-security"></a>Fontolja meg a Microsoft Cloud Application Security használatát

Microsoft Cloud App Security (MCAS) egy Cloud Access security broker (CASB) megoldás. Betekintést nyújt a felhőalkalmazásaiba és -szolgáltatásaiba, kifinomult elemzéseket biztosít a kibertámadások azonosításához és elleni támadásokhoz, és lehetővé teszi az adatok utazásának vezérlését.

Az MCAS üzembe helyezése a következőt teszi lehetővé:

- A Cloud Discovery leképezi és azonosíthatja a felhőkörnyezetet és a szervezet által használt felhőalkalmazásokat.
- A felhőbeli alkalmazások fel- és fel nem feledésbe való alkalmazása
- Egyszerűen üzembe helyezhető alkalmazás-összekötők használata, amelyek kihasználják a szolgáltatói API-k előnyeit a kapcsolódó alkalmazások láthatósága és irányítása érdekében
- A feltételes hozzáférést biztosító alkalmazás-vezérlő használata a felhőalkalmazások hozzáférésének és tevékenységeinek valós idejű átláthatósága és szabályozása érdekében
- Lehetővé teszi a szabályzatok beállításával, majd folyamatos finomhangolásával való folyamatos szabályozást.

A Microsoft Cloud Application Security (MCAS) munkamenet-vezérlése bármely böngészőben elérhető bármely nagyobb platformon, bármely operációs rendszeren. A mobilalkalmazások és asztali alkalmazások is letilthatóak vagy engedélyezettek. Az Azure AD natív integrálásával az SAML-sel konfigurált alkalmazások, illetve az Egyszeri bejelentkezéssel az Azure AD-ban open ID Connect-alkalmazások is támogatottak, beleértve számos kiemelt alkalmazást [is.](/cloud-app-security/proxy-intro-aad)

További információ az MCAS-ről: Microsoft Cloud App Security [áttekintése.](/cloud-app-security/what-is-cloud-app-security) Az MCAS egy felhasználóalapú előfizetési szolgáltatás. A licencelés részleteit az [MCAS licencelési adatlapon tudja áttekintni.](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RE2NXYO)

### <a name="use-conditional-access"></a>Feltételes hozzáférés használata

A feltételes hozzáféréssel automatizálhatja a felhőalkalmazások feltételeken alapuló hozzáférés-vezérlési döntéseit.

A feltételes hozzáférési szabályzatok az első tényezős hitelesítés befejezése után érvényesülnek. Ezért a feltételes hozzáférés nem tekinthető első vonalvédelemnek olyan forgatókönyvek esetén, mint a szolgáltatásmegtagadásos (DoS-) támadások, de ezek az események jeleket használhatnak a hozzáférés meghatározásához. Például a bejelentkezési kockázati szint, a kérés helye stb. használható. A feltételes hozzáféréssel kapcsolatos további információkért tekintse meg az [áttekintést](../conditional-access/plan-conditional-access.md) és az üzembe [helyezési tervet.](../conditional-access/plan-conditional-access.md)

## <a name="azure-sso-technical-requirements"></a>Az Azure SSO műszaki követelményei

A következő szakasz részletesen beállítja az adott alkalmazást, beleértve a szükséges környezet(eket), végpontokat, jogcím-leképezést, szükséges attribútumokat, tanúsítványokat és protokollokat. Erre az információra szüksége lesz az SSO konfiguráláshoz az [Azure AD portálon.](https://portal.azure.com/)

### <a name="authentication-mechanism-details"></a>Hitelesítési mechanizmus részletei

A Microsoft minden előre integrált SaaS-alkalmazáshoz biztosít egy oktatóanyagot, és nincs szüksége erre az információra. Ha az alkalmazás nincs az alkalmazás piacterén vagy katalógusában, előfordulhat, hogy a következő adatokat kell összegyűjtenie:

- **Az alkalmazás által az SSO-val használt** aktuális identitásszolgáltató, ha van ilyen – például: AD FS, PingFederate, Okta
- **A célalkalmazás által támogatott** protokollok – például SAML 2.0, OpenID Connect, OAuth, Forms-Based Auth, WS-Fed, WS-Trust
- **Az Azure AD-val** konfigurált protokoll – például SAML 2.0 vagy 1.1, OpenID Connect, OAuth, Forms-Based, WS-Fed

### <a name="attribute-requirements"></a>Attribútumkövetelmények

Az Azure AD felhasználói objektumai és az egyes SaaS-alkalmazások felhasználói objektumai között előre konfigurált attribútumok és attribútumleképezések vannak. Egyes alkalmazások más típusú objektumokat, például csoportokat kezelnek. Tervezze meg a felhasználói attribútumok leképezését az Azure AD-ről az alkalmazásra, és szabja testre az alapértelmezett [attribútumleképezéseket](../app-provisioning/customize-application-attributes.md) az üzleti igényeinek megfelelően.

### <a name="certificate-requirements"></a>Tanúsítványkövetelmények

Az alkalmazás tanúsítványának naprakésznek kell lennie, különben fennáll annak a kockázata, hogy a felhasználók nem tudnak hozzáférni az alkalmazáshoz. A legtöbb SaaS-alkalmazástanúsítvány 36 hónapig érvényes. Ezt a tanúsítványidőt az alkalmazás panelen módosíthatja. Mindenképpen dokumentálja a lejáratot, és tudja, hogyan fogja kezelni a tanúsítvány megújítását. 

A tanúsítványok kétféleképpen kezelhetők. 

- **Automatikus tanúsítványváltás** – A Microsoft támogatja [az aláírókulcs-visszaállítást az Azure AD-ban.](../develop/active-directory-signing-key-rollover.md) Bár ez a tanúsítványok kezelésének előnyben részesített módszere, nem minden is támogatja ezt a forgatókönyvet.

- **Manuális frissítés** – Minden alkalmazás saját tanúsítvánnyal rendelkezik, amely a definíciójuk alapján lejár. Mielőtt az alkalmazás tanúsítványa lejárna, hozzon létre egy új tanúsítványt, és küldje el a isV-nek. Ezek az információk az összevonási metaadatokból is lekért adatok. [Az összevonási metaadatokról itt olvashat bővebben.](../azuread-dev/azure-ad-federation-metadata.md)

## <a name="implement-sso"></a>SSO implementálja

A következő fázisokkal tervezheti meg és helyezheti üzembe a megoldást a szervezetben:

### <a name="user-configuration-for-sso"></a>Felhasználói konfiguráció az SSO-hez

- **A tesztfelhasználók azonosítása**

   Lépjen kapcsolatba az alkalmazás tulajdonosával, és kérje meg, hogy hozzon létre legalább három tesztfelhasználót az alkalmazásban. Győződjön meg arról, hogy az elsődleges azonosítóként használt adatok helyesen vannak kitöltve, és megegyezik az Azure AD-ban elérhető attribútummal. A legtöbb esetben ez az SAML-alapú alkalmazások "NameID" (Névazonosító) nevére lesz leképezve. A JWT-jogkivonatok "preferred_username"
   
   Hozza létre a felhasználót az Azure AD-ban manuálisan felhőalapú felhasználóként, vagy szinkronizálja a felhasználót a helyszínen a Azure AD Connect szinkronizálási motor használatával. Győződjön meg arról, hogy az adatok megegyeznek az alkalmazásnak küldött jogcímekkel.

- **Az SSO konfigurálása**

   Az alkalmazások [listájában](../saas-apps/tutorial-list.md)keresse meg és nyissa meg az alkalmazás SSO-oktatóanyagát, majd kövesse az oktatóanyag lépéseit az SaaS-alkalmazás sikeres konfigurálásához.

   Ha nem találja az alkalmazást, tekintse meg az [Egyéni alkalmazás dokumentációját.](./configure-saml-single-sign-on.md) Ez végigmutatja, hogyan adhat hozzá olyan alkalmazást, amely nem az Azure AD-katalógusban található.

   Másik lehetőségként használhatja az SAML-jogkivonatban kiadott jogcímeket a nagyvállalati alkalmazáshoz a [Microsoft útmutató dokumentációjában.](../develop/active-directory-claims-mapping.md) Győződjön meg arról, hogy ez az alkalmazás SAML-válaszában vártnak megfelelőre van leképezve. Ha problémákba ütközik a konfigurálás során, használja az [SSO-integráció hibakeresésével kapcsolatos útmutatót.](./debug-saml-sso-issues.md)

### <a name="provide-sso-change-communications-to-end-users"></a>Az SSO változáskommunikációja a végfelhasználók számára

Valósítsa meg a kommunikációs tervet. Mindenképpen tudtossa a végfelhasználókkal, hogy változás fog érkezni, amikor megérkezett, mit kell tenni, és hogyan kell segítséget kérni.

### <a name="verify-end-user-scenarios-for-sso"></a>Az SSO végfelhasználói forgatókönyvének ellenőrzése

A következő teszteseteket használhatja a vállalati és személyes eszközökön végzett tesztekhez, hogy az SSO-konfigurációk a várt módon működnek. Az alábbi forgatókönyvek feltételezik, hogy a felhasználó egy alkalmazás URL-címére navigál, és a szolgáltató által kezdeményezett hitelesítési folyamaton (SP által kezdeményezett hitelesítési folyamaton) keresztül megy keresztül.

| Eset | Az SP által kezdeményezett hitelesítési folyamat várt eredménye a felhasználó által |
|----------|---------------------------------------------------|
| Jelentkezzen be az alkalmazásba IE-val a vállalati hálózatban. | integrált Windows-hitelesítés (IWA) további kérések nélkül történik. |
| Jelentkezzen be az alkalmazásba IE-val a vállalati hálózatról egy új bejelentkezési kísérlettel. | Űrlapalapú parancssor a AD FS kiszolgálón. A felhasználó sikeresen bejelentkezik, és a böngésző kéri az MFA-t. |
| Jelentkezzen be az alkalmazásba IE-val a vállalati hálózatról egy aktuális munkamenettel, és még soha nem hajtott végre MFA-hitelesítést. | A felhasználó nem kap kérést az első tényezőre. A felhasználó MFA-kérést kap. |
| Jelentkezzen be az alkalmazásba IE-val a vállalati hálózatról egy aktuális munkamenettel, és ebben a munkamenetben már végrehajtottA az MFA-t. | A felhasználó nem kap kérést az első tényezőre. A felhasználó nem kapja meg az MFA-t. Felhasználói SSO-k az alkalmazásba. |
| Jelentkezzen be az alkalmazásba Chrome-/Firefox-/Safari-böngészővel, miközben a vállalati hálózatról van bejelentkezve egy aktuális munkamenettel, és már végrehajtott MFA-hitelesítést ebben a munkamenetben. | A felhasználó nem kap kérést az első tényezőre. A felhasználó nem kapja meg az MFA-t. Felhasználói SSO-i az alkalmazásba. |
| Jelentkezzen be az alkalmazásba a Chrome/Firefox/Safari böngészővel, miközben nem a vállalat hálózatán dolgozik új bejelentkezési kísérlettel. | Űrlapalapú parancssor a AD FS kiszolgálón. A felhasználó sikeresen bejelentkezik, és a böngésző kéri az MFA-t. |
| Jelentkezzen be az alkalmazásba a Chrome-ban/Firefoxban a vállalati hálózaton egy aktuális munkamenetben. | A felhasználó nem kapja meg az első tényezőre vonatkozó kérést. A felhasználó nem kapja meg az MFA-t. Felhasználói SSO-i az alkalmazásba. |
| Jelentkezzen be az alkalmazásba az alkalmazás mobilalkalmazásával egy új bejelentkezési kísérlettel. | Űrlapalapú parancssor a AD FS kiszolgálón. A felhasználó sikeresen bejelentkezik, és az ADAL-ügyfél kéri az MFA-t. |
| Jogosulatlan felhasználó bejelentkezési URL-cím használatával próbál meg bejelentkezni az alkalmazásba. | Űrlapalapú parancssor a AD FS kiszolgálón. A felhasználó nem tud bejelentkezni az első tényezővel. |
| Az engedéllyel rendelkező felhasználó megpróbál bejelentkezni, de helytelen jelszót ad meg. | A felhasználó az alkalmazás URL-címére navigál, és hibás felhasználónév/jelszó hibaüzenetet kap. |
| A jogosult felhasználó egy e-mailben a hivatkozásra kattint, és már hitelesítve van. | A felhasználó az URL-címre kattint, és további kérések nélkül bejelentkezik az alkalmazásba. |
| A jogosult felhasználó egy e-mailben a hivatkozásra kattint, és még nincs hitelesítve. | A felhasználó az URL-címre kattint, és a rendszer az első tényezővel való hitelesítést kéri. |
| Az engedélyezett felhasználó egy új bejelentkezési kísérlettel (SP által kezdeményezett) alkalmazás-mobilalkalmazással jelentkezik be az alkalmazásba. | Űrlapalapú parancssor a AD FS kiszolgálón. A felhasználó sikeresen bejelentkezik, és az ADAL-ügyfél kérni fogja az MFA-t. |
| Jogosulatlan felhasználó bejelentkezési URL-cím használatával próbál meg bejelentkezni az alkalmazásba (sp kezdeményezte). | Űrlapalapú parancssor a AD FS kiszolgálón. A felhasználó nem tud bejelentkezni az első tényezővel. |
| Az engedéllyel rendelkező felhasználó megpróbál bejelentkezni, de helytelen jelszót ad meg.| A felhasználó az alkalmazás URL-címére navigál, és hibás felhasználónév/jelszó hibaüzenetet kap. |
| Az engedéllyel rendelkező felhasználó kijelentkezik, majd ismét bejelentkezik. | Ha a Kijelentkezés URL-cím be van állítva, a felhasználó kijelentkezik az összes szolgáltatásból, és a rendszer kéri a hitelesítést. |
| Az engedéllyel rendelkező felhasználó kijelentkezik, majd ismét bejelentkezik. | Ha a bejelentkezési URL-cím nincs konfigurálva, a rendszer automatikusan bejelentkezik a meglévő Azure AD böngésző-munkamenetből származó meglévő jogkivonattal. |
| A jogosult felhasználó egy e-mailben a hivatkozásra kattint, és már hitelesítve van. | A felhasználó az URL-címre kattint, és további kérések nélkül bejelentkezik az alkalmazásba. |
| A jogosult felhasználó egy e-mailben a hivatkozásra kattint, és még nincs hitelesítve. | A felhasználó az URL-címre kattint, és a rendszer az első tényezővel való hitelesítést kéri. |

## <a name="manage-sso"></a>SSO kezelése

Ez a szakasz az SSO sikeres kezeléséhez szükséges követelményeket és javaslatokat ismerteti.

### <a name="required-administrative-roles"></a>Szükséges rendszergazdai szerepkörök

Mindig használja a szerepkört a rendelkezésre álló legrövidebb engedélyekkel a szükséges feladatok végrehajtásához a Azure Active Directory. A Microsoft azt [javasolja, hogy](../roles/permissions-reference.md) tekintse át a különböző elérhető szerepköröket, és válassza ki a megfelelőt az alkalmazás minden egyes személye igényeinek megoldásához. Előfordulhat, hogy egyes szerepköröket ideiglenesen kell alkalmazni, és el kell távolítani őket az üzembe helyezés befejezése után.

| Persona| Szerepkörök | Azure AD-szerepkör (ha szükséges) |
|--------|-------|-----------------------------|
| Ügyfélszolgálati rendszergazda | 1. rétegbeli támogatás | Nincsenek |
| Identitás-rendszergazda | Konfigurálás és hibakeresés, ha problémák befolyásolják az Azure AD-t | Globális rendszergazda |
| Alkalmazás-rendszergazda | Felhasználói igazolás az alkalmazásban, konfigurálás engedélyekkel rendelkező felhasználókon | Nincsenek |
| Infrastruktúra-rendszergazdák | Tanúsítvány-visszaállítás tulajdonosa | Globális rendszergazda |
| Üzlettulajdonos/érintett | Felhasználói igazolás az alkalmazásban, konfigurálás engedélyekkel rendelkező felhasználókon | Nincsenek |

Javasoljuk, [hogy Privileged Identity Management](../privileged-identity-management/pim-configure.md) (PIM) használatával kezelje a szerepköröket, hogy további naplózást, vezérlést és hozzáférési felülvizsgálatot biztosítson a címtárengedélyekkel rendelkező felhasználók számára.

### <a name="sso-certificate-lifecycle-management"></a>SSO-tanúsítványok életciklusának kezelése

Fontos azonosítani a megfelelő szerepköröket és e-mail-terjesztési listákat, amelyek az aláíró tanúsítvány életciklusának az Azure AD és az egyszeri bejelentkezéssel konfigurált alkalmazás közötti életciklusának kezelésével vannak feladatokkal. Íme néhány fontos szerepkör, amelyek használatát javasoljuk:

- Tulajdonos az alkalmazás felhasználói tulajdonságainak frissítéséhez
- Tulajdonosi hívás az alkalmazás-törés/javítás támogatásához
- A tanúsítvánnyal kapcsolatos változásértesítések szorosan figyelt e-mail-terjesztési listája

A tanúsítvány maximális élettartama három év. Javasoljuk, hogy az Azure AD és az alkalmazás közötti tanúsítványváltozások kezelésének folyamatával ássa le a folyamatot. Ez segíthet megelőzni vagy minimalizálni a tanúsítvány lejárata miatti kimaradást, vagy kényszerítheti a tanúsítványváltást.

### <a name="rollback-process"></a>Visszaállítási folyamat

Miután befejezte a tesztelést a tesztelési esetek alapján, ideje éles környezetbe lépnie az alkalmazással. Az éles környezetbe való áthelyezés azt jelenti, hogy implementálja a tervezett és tesztelt konfigurációkat az éles bérlőben, és kiadja azt a felhasználók számára. Fontos azonban megtervezni, hogy mi a helyzet, ha az üzembe helyezés nem a terv szerint működik. Ha az SSO-konfiguráció sikertelen az üzembe helyezés során, tisztában kell lennie a szolgáltatáskimaradások és a felhasználókra gyakorolt hatás csökkentésének mikéntjére.

Az alkalmazáson belüli hitelesítési módszerek rendelkezésre állása határozza meg a legjobb stratégiát. Mindig győződjön meg arról, hogy az alkalmazás tulajdonosainak részletes dokumentációja van arról, hogyan lehet visszahelyeni az eredeti bejelentkezési konfigurációs állapothoz arra az esetre, ha az üzembe helyezés problémát okozhat.

- **Ha az alkalmazás több** identitásszolgáltatót is támogat ( például LDAP és AD FS és Ping – a bevezetés során ne törölje a meglévő SSO-konfigurációt. Ehelyett tiltsa le a migrálás során arra az esetre, ha később vissza kellene váltania. 

- **Ha az alkalmazás** nem támogatja több idPs használatát, de lehetővé teszi a felhasználóknak, hogy űrlapalapú hitelesítéssel (felhasználónévvel/jelszóval) jelentkezzenek be, győződjön meg arról, hogy a felhasználók vissza tudnak lépni erre a megközelítésre, ha az új SSO-konfiguráció sikertelen lesz.

### <a name="access-management"></a>Hozzáférés-kezelés

Javasoljuk, hogy az erőforrásokhoz való hozzáférés kezelésekor skálázható megközelítést válasszon. A gyakori megközelítések közé tartozik a helyszíni csoportok használata az Azure AD Connect-n keresztüli szinkronizálással, dinamikus csoportok létrehozása az [Azure AD-ben](../enterprise-users/groups-dynamic-membership.md)felhasználói attribútumok alapján, vagy önkiszolgáló csoportok létrehozása az [erőforrás-tulajdonos](../enterprise-users/groups-self-service-management.md) által felügyelt Azure AD-ben.

### <a name="monitor-security"></a>Biztonság figyelése

Javasoljuk, hogy olyan rendszeres ütemet ad meg, amelyben áttekinti az SaaS-alkalmazásbiztonság különböző aspektusait, és végrehajtja a szükséges szervizműveleteket.

### <a name="troubleshooting"></a>Hibaelhárítás

Az alábbi hivatkozások hibaelhárítási forgatókönyveket tartalmaznak. Előfordulhat, hogy létre szeretne hozni egy külön útmutatót a támogatási munkatársak számára, amely tartalmazza ezeket a forgatókönyveket és a javításukhoz szükséges lépéseket.

#### <a name="consent-issues"></a>Hozzájárulással kapcsolatos problémák

- [Váratlan hozzájárulási hiba](./application-sign-in-unexpected-user-consent-prompt.md)

- [Hiba a felhasználói hozzájárulással](./application-sign-in-unexpected-user-consent-error.md)

#### <a name="sign-in-issues"></a>Bejelentkezési problémák

- [Egyéni portálról való bejelentkezés problémái](./application-sign-in-other-problem-access-panel.md)

- [Probléma a Saját alkalmazásokból való bejelentkezésnél](./application-sign-in-other-problem-access-panel.md)

- [Hiba az alkalmazás bejelentkezési oldalán](./application-sign-in-problem-application-error.md)

- [Probléma egy Microsoft-alkalmazásba való bejelentkezés során](./application-sign-in-problem-first-party-microsoft.md)

#### <a name="sso-issues-for-applications"></a>Alkalmazásokkal kapcsolatos SSO-problémák

- [Az alkalmazások jelszó-SSO-val való probléma](./troubleshoot-password-based-sso.md) 

- [Problémák az SAML-alapú egyszeri bejelentkezésre konfigurált alkalmazásokba való bejelentkezés során](/troubleshoot/azure/active-directory/troubleshoot-sign-in-saml-based-apps)   


## <a name="next-steps"></a>Következő lépések

[SAML-alapú SSO hibakeresése](./debug-saml-sso-issues.md)

[Jogcím-leképezés alkalmazásokhoz a PowerShell-en keresztül](../develop/active-directory-claims-mapping.md)

[SAML-jogkivonatban kiadott jogcímek testreszabása](../develop/active-directory-saml-claims-customization.md)

[Egyszeri bejelentkezéses SAML protokoll](../develop/single-sign-on-saml-protocol.md)

[Egy Sign-Out SAML protokoll](../develop/single-sign-out-saml-protocol.md)

[Azure AD B2B](../external-identities/what-is-b2b.md) (külső felhasználók, például partnerek és szállítók számára)

[Azure AD-beli feltételes hozzáférés](../conditional-access/overview.md)

[Azure Identity Protection](../identity-protection/overview-identity-protection.md)

[Egyszeri bejelentkezéses hozzáférés](./what-is-single-sign-on.md)

[Alkalmazás-SSO-oktatóanyag](../saas-apps/tutorial-list.md)

[Tanulmány letöltése – Egyszeri bejelentkezési üzembe helyezési terv](https://aka.ms/SSODeploymentPlan)