---
title: A tevékenységjelentés használatával áthelyezi AD FS alkalmazásokat a Azure Active Directory | Microsoft Docs"
description: A Active Directory összevonási szolgáltatások (AD FS) (AD FS) alkalmazástevékenység-jelentéssel gyorsan minálhatja az alkalmazásokat a AD FS-Azure Active Directory (Azure AD) szolgáltatásba. Ez a migrálási eszköz AD FS azonosítja az Azure AD-kompatibilitást, és útmutatást nyújt a migráláshoz.
services: active-directory
author: iantheninja
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: how-to
ms.workload: identity
ms.date: 01/14/2019
ms.author: iangithinji
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6b9265a61b0879078332b8ccc2d10e711b4ac8f1
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2021
ms.locfileid: "107377420"
---
# <a name="use-the-ad-fs-application-activity-report-to-migrate-applications-to-azure-ad"></a>Alkalmazások mi AD FS Azure AD-be az alkalmazástevékenységi jelentés használatával

Számos szervezet Active Directory összevonási szolgáltatások (AD FS) (AD FS) a felhőalapú alkalmazásokba való egyszeri bejelentkezéshez. A hitelesítés szempontjából jelentős előnyökkel jár, ha AD FS alkalmazásait az Azure AD-be, különösen a költségkezelés, a kockázatkezelés, a hatékonyság, a megfelelőség és a szabályozás szempontjából. Azonban az Azure AD-val kompatibilis alkalmazások és a konkrét migrálási lépések azonosítása időigényes lehet.

A AD FS Azure Portal alkalmazástevékenység-jelentésével gyorsan azonosíthatja, hogy mely alkalmazásai migrálhatóak az Azure AD-be. Felméri az összes AD FS Azure AD-kompatibilitás érdekében, ellenőrzi az esetleges problémákat, és útmutatást nyújt az egyes alkalmazások migrálásra való előkészítéséhez. A AD FS jelentéssel a következőt teheti meg:

* **Fedezze fel AD FS alkalmazásokat, és hatókört a migrálás hatókörére.** Az AD FS tevékenységjelentés felsorolja a AD FS összes olyan alkalmazását, amely aktív felhasználói bejelentkezéssel volt bejelentkezve az elmúlt 30 napban. A jelentés azt jelzi, hogy az alkalmazások az Azure AD-be való migrálásra készek. A jelentés nem jeleníti meg a Microsofttal kapcsolatos függő AD FS, például az Office 365-ben. Ilyen lehet például az "urn:federation:MicrosoftOnline" nevű függő felek.

* **Alkalmazások rangsorolása a migráláshoz.** Az alkalmazásba az elmúlt 1, 7 vagy 30 nap során bejelentkezett egyedi felhasználók számának megállapítása az alkalmazás áttelepítésének kritikussága vagy kockázata alapján.
* **Migrálási tesztek futtatása és a problémák megoldása.** A jelentéskészítési szolgáltatás automatikusan futtat teszteket annak megállapításához, hogy az alkalmazás készen áll-e az áttelepítésre. Az eredmények a AD FS tevékenységjelentésben jelennek meg migrálási állapotként. Ha a AD FS konfigurációja nem kompatibilis az Azure AD-konfigurációval, konkrét útmutatást kap a konfiguráció Azure AD-beli kezelése során.

Az AD FS-tevékenység adatai a következő rendszergazdai szerepkörök bármelyikéhez hozzárendelt felhasználók számára érhetők el: globális rendszergazda, jelentésolvasó, biztonsági olvasó, alkalmazás-rendszergazda vagy felhőalkalmazás-rendszergazda.

## <a name="prerequisites"></a>Előfeltételek

* A szervezetnek jelenleg a AD FS kell használnia az alkalmazások eléréséhez.
* Azure AD Connect Health engedélyezni kell az Azure AD-bérlőben.
* A Azure AD Connect Health ügynök AD FS kell telepíteni.
   * [További információ a Azure AD Connect Health](../hybrid/how-to-connect-health-adfs.md)
   * [Első lépések a Azure AD Connect Health és a AD FS telepítésében](../hybrid/how-to-connect-health-agent-install.md)

>[!IMPORTANT] 
>Több oka is van annak, hogy nem fogja látni az összes olyan alkalmazást, amely a telepítés után Azure AD Connect Health. Az AD FS tevékenységjelentés csak azokat a függő AD FS jeleníti meg, akik az elmúlt 30 napban jelentkeznek be felhasználói fiókkal. Emellett a jelentés nem jeleníti meg a Microsofthoz kapcsolódó függő felek, például az Office 365- et.

## <a name="discover-ad-fs-applications-that-can-be-migrated"></a>Az AD FS áttelepíthető alkalmazások felderítése 

Az AD FS tevékenységjelentés az Azure AD Azure Portal jelentéskészítési & **érhető** el. Az AD FS-tevékenységjelentés elemzi az AD FS, hogy az adott alkalmazás migrálható-e az adott ként, vagy további felülvizsgálatra van-e szükség. 

1. Jelentkezzen be [](https://portal.azure.com) az Azure Portal rendszergazdai szerepkörével, amely hozzáféréssel rendelkezik AD FS alkalmazástevékenységi adatokhoz (globális rendszergazda, jelentésolvasó, biztonsági olvasó, alkalmazás-rendszergazda vagy felhőalkalmazás-rendszergazda).

2. Válassza **Azure Active Directory** lehetőséget, majd válassza a Vállalati **alkalmazások lehetőséget.**

3. A **Tevékenység alatt** válassza a Usage &  **Insights** lehetőséget, majd válassza AD FS alkalmazástevékenység lehetőséget a szervezet összes AD FS listájának megnyitásához.

   ![AD FS alkalmazástevékenység](media/migrate-adfs-application-activity/adfs-application-activity.png)

4. Az alkalmazástevékenységek listájában AD FS összes alkalmazáshoz tekintse meg a **Migrálás állapotát:**

   * **A migrálható** azt jelenti, AD FS alkalmazáskonfiguráció teljes mértékben támogatott az Azure AD-ben, és a migrálható.

   * **Az Áttekintés** szükséges azt jelenti, hogy az alkalmazás egyes beállításai áttelepítve lesznek az Azure AD-be, de át kell vizsgálnia a beállításokat, amelyek nem migrálhatóak a megadott módon.

   * **A további szükséges lépések** azt jelentik, hogy az Azure AD nem támogatja az alkalmazás egyes beállításait, ezért az alkalmazás nem migrálható az aktuális állapotában.

## <a name="evaluate-the-readiness-of-an-application-for-migration"></a>Egy alkalmazás migrálásra való készenlétének kiértékelése 

1. A AD FS tevékenység listájában kattintson a Migrálás állapota oszlopban lévő állapotra a migrálás részleteinek megnyitásához.  Itt láthatja az elvégzett konfigurációs tesztek összegzését, valamint az esetleges migrálási problémákat.

   ![A migrálás részletei](media/migrate-adfs-application-activity/migration-details.png)

2. Kattintson egy üzenetre a további migrálási szabályok részleteinek megnyitásához. A tesztelt tulajdonságok teljes listáját az alábbi AD FS alkalmazáskonfigurációs tesztek [táblázatában](#ad-fs-application-configuration-tests) láthatja.

   ![Migrálási szabály részletei](media/migrate-adfs-application-activity/migration-rule-details.png)

### <a name="ad-fs-application-configuration-tests"></a>AD FS tesztek konfigurálása

Az alábbi táblázat az alkalmazásokon végrehajtott összes konfigurációs AD FS sorolja fel.

|Eredmény  |Pass/Warning/Fail  |Leírás  |
|---------|---------|---------|
|Test-ADFSRPAdditionalAuthenticationRules <br> A rendszer legalább egy nem áttelepíthető szabályt észlelt az AdditionalAuthentication esetén.       | Pass/Warning          | A függő fél olyan szabályokkal rendelkezik, amelyek kérik a többtényezős hitelesítést (MFA). Az Azure AD-be való áthelyezéshez ezeket a szabályokat feltételes hozzáférési szabályzatokként kell lefordítani. Ha helyszíni MFA-t használ, javasoljuk, hogy lépjen a Azure AD MFA. [További információ a feltételes hozzáférésről.](../authentication/concept-mfa-howitworks.md)        |
|Test-ADFSRPAdditionalWSFedEndpoint <br> A függő függőben az AdditionalWSFedEndpoint true (igaz) értékkel rendelkezik.       | Sikeres/sikertelen          | A szolgáltatásban a függő AD FS több WS-Fed helyességi feltételvégpontot is lehetővé tesz.Az Azure AD jelenleg csak egyet támogat.Ha van olyan forgatókönyve, amelyben ez az eredmény blokkolja a migrálást, [tudajuk meg velünk a következőt:](https://feedback.azure.com/forums/169401-azure-active-directory/suggestions/38695621-allow-multiple-ws-fed-assertion-endpoints).     |
|Test-ADFSRPAllowedAuthenticationClassReferences <br> A függő fél beállította az AllowedAuthenticationClassReferences osztályt.       | Sikeres/sikertelen          | Ez a beállítás AD FS megadhatja, hogy az alkalmazás csak bizonyos hitelesítési típusok számára legyen-e konfigurálva. Javasoljuk, hogy ezt a képességet feltételes hozzáféréssel érjük el. Ha van olyan forgatókönyve, amelyben ez az eredmény blokkolja a migrálást, [tudajuk meg velünk a következőt:](https://feedback.azure.com/forums/169401-azure-active-directory/suggestions/38695672-allow-in-azure-ad-to-specify-certain-authentication).  [További információ a feltételes hozzáférésről.](../authentication/concept-mfa-howitworks.md)          |
|Test-ADFSRPAlwaysRequireAuthentication <br> AlwaysRequireAuthenticationCheckResult      | Sikeres/sikertelen          | Ez a beállítás AD FS megadhatja, hogy az alkalmazás az SSO cookie-k figyelmen kívül hagyása és az Always Prompt for Authentication (Mindig hitelesítésre való kérés) **legyen-e konfigurálva.** Az Azure AD-ban a hitelesítési munkamenet feltételes hozzáférési szabályzatokkal kezelhető, így hasonló viselkedést érhet el. [További információ a hitelesítési munkamenetek feltételes hozzáféréssel való felügyeletének konfigurálásával kapcsolatban.](../conditional-access/howto-conditional-access-session-lifetime.md)          |
|Test-ADFSRPAutoUpdateEnabled <br> A függő fél AutoUpdateEnabled beállítása true (igaz)       | Pass/Warning          | Ezzel a AD FS megadhatja, hogy a AD FS az összevonási metaadatokon belüli változások alapján automatikusan frissítse-e az alkalmazást. Az Azure AD ezt jelenleg nem támogatja, de nem szabad blokkolni az alkalmazás Azure AD-be való migrálását.           |
|Test-ADFSRPClaimsProviderName <br> A függő függő félhez több jogcímprovider is engedélyezve van       | Sikeres/sikertelen          | Ez a beállítás AD FS hívja meg az identitásszolgáltatókat, amelyektől a függő fél jogcímeket fogad. Az Azure AD-ben külső együttműködést engedélyezhet az Azure AD B2B használatával. [További információ az Azure AD B2B-ről.](../external-identities/what-is-b2b.md)          |
|Test-ADFSRPDelegationAuthorizationRules      | Sikeres/sikertelen          | Az alkalmazáshoz egyéni delegálásengedélyezési szabályok vannak definiálva. Ez egy olyan WS-Trust, amelyet az Azure AD támogat a modern hitelesítési protokollok, például a OpenID Connect és az OAuth 2.0 használatával. [További információ a Microsoft Identity Platformról.](../develop/v2-protocols-oidc.md)          |
|Test-ADFSRPImpersonationAuthorizationRules       | Pass/Warning          | Az alkalmazáshoz egyéni megszemélyesítési engedélyezési szabályok vannak definiálva.Ez egy olyan WS-Trust, amelyet az Azure AD támogat a modern hitelesítési protokollok, például az OpenID Connect és az OAuth 2.0 használatával. [További információ a Microsoft Identity Platformról.](../develop/v2-protocols-oidc.md)          |
|Test-ADFSRPIssuanceAuthorizationRules <br> A rendszer legalább egy nem áttelepíthető szabályt észlelt a issuanceAuthorization esetén.       | Pass/Warning          | Az alkalmazás egyéni kiállításengedélyezési szabályokkal rendelkezik, amelyek a AD FS.Az Azure AD ezt a funkciót az Azure AD feltételes hozzáféréssel támogatja. [További információ a feltételes hozzáférésről.](../conditional-access/overview.md) <br> Az alkalmazáshoz való hozzáférést az alkalmazáshoz rendelt felhasználók vagy csoportok szerint is korlátozhatja. [További információ a felhasználók és csoportok alkalmazásokhoz való hozzárendeléséhez való hozzárendeléséhez.](./assign-user-or-group-access-portal.md)            |
|Test-ADFSRPIssuanceTransformRules <br> A rendszer legalább egy nem migrálható szabályt észlelt a IssuanceTransform számára.       | Pass/Warning          | Az alkalmazás egyéni kiállítási átalakítási szabályokkal rendelkezik, amelyek a AD FS. Az Azure AD támogatja a jogkivonatban kiadott jogcímek testreszabását. További tudnivalókért lásd [az SAML-jogkivonatban](../develop/active-directory-saml-claims-customization.md)kiadott jogcímek vállalati alkalmazásokhoz való testreszabását.           |
|Test-ADFSRPMonitoringEnabled <br> A függő függő félHez a MonitoringEnabled true (igaz) érték van beállítva.       | Pass/Warning          | Ezzel a AD FS megadhatja, hogy a AD FS az összevonási metaadatokon belüli módosítások alapján automatikusan frissítse-e az alkalmazást. Az Azure AD ezt jelenleg nem támogatja, de nem szabad blokkolni az alkalmazás Azure AD-be való migrálását.           |
|Test-ADFSRPNotBeforeSkew <br> NotBeforeSkewCheckResult      | Pass/Warning          | AD FS az SAML-jogkivonat NotBefore és NotOnOrAfter ideje alapján engedélyezi az időeltéréseket. Az Azure AD ezt alapértelmezés szerint automatikusan kezeli.          |
|Test-ADFSRPRequestMFAFromClaimsProviders <br> A függő fél RequestMFAFromClaimsProviders beállítása igaz.       | Pass/Warning          | Ez a beállítás AD FS MFA viselkedését határozza meg, ha a felhasználó egy másik jogcímszolgáltatótól származik. Az Azure AD-ben külső együttműködést engedélyezhet az Azure AD B2B használatával. Ezután feltételes hozzáférési szabályzatokat alkalmazhat a vendég hozzáférés védelmére. További információ az [Azure AD B2B-ről](../external-identities/what-is-b2b.md) és [a feltételes hozzáférésről.](../conditional-access/overview.md)          |
|Test-ADFSRPSignedSamlRequestsRequired <br> A függő fél SignedSamlRequestsRequired beállítása true (igaz)       | Sikeres/sikertelen          | Az alkalmazás úgy van konfigurálva a AD FS, hogy ellenőrizze az aláírást az SAML-kérelemben. Az Azure AD aláírt SAML-kérelmet fogad el; Az aláírást azonban nem ellenőrzi. Az Azure AD-nek különböző módszerei vannak a rosszindulatú hívások elleni védelemhez. Az Azure AD például az alkalmazásban konfigurált válasz URL-címeket használja az SAML-kérelem érvényesítéséhez. Az Azure AD csak az alkalmazáshoz konfigurált válasz URL-címekre küld jogkivonatot. Ha van olyan forgatókönyve, amelyben ez az eredmény blokkolja a migrálást, [tudajuk meg a következőt:](https://feedback.azure.com/forums/169401-azure-active-directory/suggestions/13394589-saml-signature).          |
|Test-ADFSRPTokenLifetime <br> TokenLifetimeCheckResult        | Pass/Warning         | Az alkalmazás egyéni jogkivonat-élettartamra van konfigurálva. Az AD FS alapértelmezett érték egy óra.Az Azure AD feltételes hozzáféréssel támogatja ezt a funkciót. További információ: [Hitelesítési munkamenet-kezelés konfigurálása feltételes hozzáféréssel.](../conditional-access/howto-conditional-access-session-lifetime.md)          |
|A függő fél a jogcímek titkosítására van beállítva. Ezt az Azure AD támogatja       | Sikeres          | Az Azure AD-val titkosíthatja az alkalmazásnak küldött jogkivonatot. További információ: [Configure Azure AD SAML token encryption (Az Azure AD SAML-jogkivonat titkosításának konfigurálása).](./howto-saml-token-encryption.md)          |
|EncryptedNameIdRequiredCheckResult      | Sikeres/sikertelen          | Az alkalmazás úgy van konfigurálva, hogy titkosítsa a nameID jogcímet az SAML-jogkivonatban.Az Azure AD-val titkosíthatja az alkalmazásnak küldött teljes jogkivonatot.Bizonyos jogcímek titkosítása még nem támogatott. További információ: [Configure Azure AD SAML token encryption (Az Azure AD SAML-jogkivonat titkosításának konfigurálása).](./howto-saml-token-encryption.md)         |

## <a name="check-the-results-of-claim-rule-tests"></a>Jogcímszabály-tesztek eredményeinek ellenőrzése

Ha konfigurált egy jogcímszabályt az alkalmazáshoz a AD FS, a felhasználói élmény részletes elemzést biztosít az összes jogcímszabályhoz. Látni fogja, hogy mely jogcímszabályokat lehet áthelyezni az Azure AD-be, és melyekhez van szükség további felülvizsgálatra.

1. A AD FS tevékenység listájában kattintson a Migrálás állapota oszlopban lévő állapotra a migrálás részleteinek megnyitásához.  Itt láthatja az elvégzett konfigurációs tesztek összegzését, valamint az esetleges migrálási problémákat.

2. Az **Áttelepítési szabály részletei lapon** bontsa ki az eredményeket a lehetséges migrálási problémák részleteinek megjelenítéséhez és további útmutatáshoz. Az összes tesztelt jogcímszabály részletes listáját az [alábbi, A](#check-the-results-of-claim-rule-tests) jogcímszabály-tesztek eredményeinek ellenőrzése táblázatban olvashatja.

   Az alábbi példa a IssuanceTransform szabály áttelepítési szabályának részleteit mutatja be. Felsorolja a jogcím azon részeit, amelyek felülvizsgálata és kezelése szükséges az alkalmazás Azure AD-be való áttelepítése előtt.

   ![A migrálási szabály részletesen tartalmaz további útmutatást](media/migrate-adfs-application-activity/migration-rule-details-guidance.png)

### <a name="claim-rule-tests"></a>Jogcímszabály-tesztek

Az alábbi táblázat az összes olyan jogcímszabály-tesztet felsorolja, amely a AD FS történik.

|Tulajdonság  |Leírás  |
|---------|---------|
|UNSUPPORTED_CONDITION_PARAMETER      | A condition utasítás reguláris kifejezésekkel értékeli ki, hogy a jogcím megfelel-e egy bizonyos mintának.Az Azure AD hasonló funkcióinak eléréséhez használhat többek között olyan előre definiált átalakításokat, mint például az IfEmpty(), a StartWith(), a Contains(). További információ: [Az SAML-jogkivonatban kiadott jogcímek testreszabása vállalati alkalmazásokhoz.](../develop/active-directory-saml-claims-customization.md)          |
|UNSUPPORTED_CONDITION_CLASS      | A condition utasítás több feltétellel rendelkezik, amelyek kiértékelése szükséges a kiállítási utasítás futtatása előtt.Az Azure AD támogathatja ezt a funkciót a jogcím átalakítási függvényeivel, ahol több jogcímértéket értékelhet ki.További információ: [Az SAML-jogkivonatban kiadott jogcímek testreszabása vállalati alkalmazásokhoz.](../develop/active-directory-saml-claims-customization.md)          |
|UNSUPPORTED_RULE_TYPE      | A jogcímszabályt nem sikerült felismerni. További információ a jogcímek Azure AD-beli konfigurálásról: Az SAML-jogkivonatban kibocsátott jogcímek testreszabása [vállalati alkalmazásokhoz.](../develop/active-directory-saml-claims-customization.md)          |
|CONDITION_MATCHES_UNSUPPORTED_ISSUER      | A condition utasítás olyan kiállítót használ, amelyet az Azure AD nem támogat.Az Azure AD jelenleg nem más tárolókból származó jogcímeket hoz Active Directory Azure AD-ból. Ha ez nem akadályozza meg az alkalmazások Azure AD-be való áttelepítését, [tudtossa velünk.](https://feedback.azure.com/forums/169401-azure-active-directory/suggestions/38695717-allow-to-source-user-attributes-from-external-dire)         |
|UNSUPPORTED_CONDITION_FUNCTION      | A condition utasítás egy összesítő függvénnyel ad ki vagy ad hozzá egyetlen jogcímet az egyezések számától függetlenül.Az Azure AD-ban kiértékelheti egy felhasználó attribútumát, hogy eldöntse, milyen értéket szeretne használni a jogcímhez többek között az IfEmpty(), a StartWith(), a Contains() függvényekkel.További információ: [Az SAML-jogkivonatban kiadott jogcímek testreszabása vállalati alkalmazásokhoz.](../develop/active-directory-saml-claims-customization.md)          |
|RESTRICTED_CLAIM_ISSUED      | A condition utasítás egy, az Azure AD-ban korlátozott jogcímet használ. Előfordulhat, hogy ki tud adni egy korlátozott jogcímet, de a forrását nem módosíthatja, és nem alkalmazhat átalakítást. További információ: Egy adott alkalmazás jogkivonatai által kibocsátott jogcímek testreszabása az [Azure AD-ban.](../develop/active-directory-claims-mapping.md)          |
|EXTERNAL_ATTRIBUTE_STORE      | A kiállítási utasítás egy eltérő attribútumtárat használ, Active Directory. Az Azure AD jelenleg nem olyan különböző tárolókból származó jogcímeket Active Directory az Azure AD-ból. Ha ez az eredmény akadályozza az alkalmazások Azure AD-be való áttelepítését, [tudajuk meg velünk a következőt:](https://feedback.azure.com/forums/169401-azure-active-directory/suggestions/38695717-allow-to-source-user-attributes-from-external-dire).          |
|UNSUPPORTED_ISSUANCE_CLASS      | A kiállítási utasítás az ADD segítségével adja hozzá a jogcímeket a bejövő jogcímkészlethez. Az Azure AD-ban ez több jogcím-átalakításként is konfigurálható.További információ: [Az SAML-jogkivonatban kiadott jogcímek testreszabása vállalati alkalmazásokhoz.](../develop/active-directory-claims-mapping.md)         |
|UNSUPPORTED_ISSUANCE_TRANSFORMATION      | A kiállítási utasítás reguláris kifejezésekkel alakítja át a kiveszni szánt jogcím értékét.Az Azure AD hasonló funkcióinak eléréséhez használhat többek között olyan előre definiált átalakításokat, mint például az Extract(), a Trim(), a ToLower. További információ: [Az SAML-jogkivonatban kiadott jogcímek testreszabása vállalati alkalmazásokhoz.](../develop/active-directory-saml-claims-customization.md)          |

## <a name="troubleshooting"></a>Hibaelhárítás

### <a name="cant-see-all-my-ad-fs-applications-in-the-report"></a>Nem látom az összes AD FS a jelentésben

 Ha telepítette az Azure AD Connect Health-t, de továbbra is a telepítésére vonatkozó kérést látja, vagy nem látja az összes AD FS-alkalmazást a jelentésben, előfordulhat, hogy nem aktív AD FS-alkalmazásokkal, vagy a AD FS-alkalmazások Microsoft-alkalmazások.
 
 Az AD FS tevékenységjelentés a szervezetben AD FS összes alkalmazás listáját tartalmazza, amelyekbe az aktív felhasználók bejelentkeztek az elmúlt 30 napban. Emellett a jelentés nem jeleníti meg a Microsofthoz kapcsolódó függő AD FS az Office 365-ben. Például az "urn:federation:MicrosoftOnline", "microsoftonline", "microsoft:winhello:cert:prov:server" nevű függő felek nem megjelenik a listában.





## <a name="next-steps"></a>Következő lépések

- [Videó: A AD FS-tevékenységjelentés használata alkalmazás áttelepítéséhez](https://www.youtube.com/watch?v=OThlTA239lU)
- [Alkalmazások kezelése az Azure Active Directoryval](what-is-application-management.md)
- [Alkalmazások hozzáférésének kezelése](what-is-access-management.md)
- [Azure AD Connect-összevonás](../hybrid/how-to-connect-fed-whatis.md)
