---
title: Az Azure Security alapkonfigurációja Microsoft Azure társ-szolgáltatási szolgáltatáshoz
description: Az Microsoft Azure peering Service biztonsági alapkonfiguráció az Azure biztonsági Teljesítménytesztben meghatározott biztonsági javaslatok megvalósítására szolgáló eljárási útmutatást és erőforrásokat biztosít.
author: msmbaldwin
ms.service: peering-service
ms.topic: conceptual
ms.date: 04/09/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 854c669861c6aff71861481843bff97fdb7157ab
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2021
ms.locfileid: "107315606"
---
# <a name="azure-security-baseline-for-microsoft-azure-peering-service"></a>Az Azure Security alapkonfigurációja Microsoft Azure társ-szolgáltatási szolgáltatáshoz

Ez a biztonsági alapkonfiguráció az [Azure biztonsági teljesítményteszt 2,0-es verziójáról](../security/benchmarks/overview.md) Microsoft Azure a peering Service-re vonatkozó útmutatást alkalmazza. Az Azure Security Benchmark ajánlásokat ad arra nézve, hogy hogyan tehetők biztonságossá a felhőalapú megoldások az Azure-ban. A tartalom az Azure biztonsági teljesítményteszt által meghatározott **biztonsági vezérlők** szerint van csoportosítva, valamint a Microsoft Azure-társi szolgáltatásra vonatkozó kapcsolódó útmutatás.

> [!NOTE]
>  A nem alkalmazhatók a Microsoft Azure-társi szolgáltatásra, illetve a felelősség a Microsoft által kizárva. Ha szeretné megtekinteni, hogy a Microsoft Azure-társas szolgáltatás miként teljes mértékben leképezi az Azure biztonsági Teljesítménytesztét, tekintse meg a **[teljes Microsoft Azure peering Service biztonsági](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)** alapkonfiguráció-hozzárendelés

## <a name="network-security"></a>Hálózati biztonság

*További információ: [Azure Security Benchmark: Hálózati biztonság](/azure/security/benchmarks/security-controls-v2-network-security).*

### <a name="ns-5-deploy-intrusion-detectionintrusion-prevention-systems-idsips"></a>NS-5: behatolás-észlelési/Behatolás-megelőzési rendszerek (AZONOSÍTÓk/IP-címek) üzembe helyezése

**Útmutató**: az egyenrangú szolgáltatás nem nyújt natív behatolás-észlelési vagy-megelőzési szolgáltatásokat. Az ügyfeleknek az ajánlott biztonsági eljárásokat kell követniük, mint például a veszélyforrások felderítésére alapuló szűrés használata az Azure Firewallról a riasztásra, valamint a és a rendszertől az ismert kártékony IP-címekre és tartományokra való adatforgalom letiltására. Ezek az IP-címek és tartományok a Microsoft Threat-Intelligence-hírcsatornából származnak. 

Olyan esetekben, ahol szükség van a hasznos adatok vizsgálatára, helyezzen üzembe egy harmadik féltől származó behatolás-ellenőrzési vagy-megelőzési rendszert (azonosító/IP-cím), az Azure Marketplace-en.  
Másik lehetőségként egy gazdagép-alapú végpont-észlelési és-válasz (EDR) megoldást használhat a hálózat alapú behatolás-észlelési és-megelőzési rendszerek (vagy azok helyett) használatával.  

Ha a behatolás-észlelés vagy a behatolás-megelőzési rendszer használatára vonatkozó szabályozási követelmény, győződjön meg arról, hogy az mindig az igénybe vett és a magas színvonalú riasztásokat használja. 

- [Azure Firewall üzembe helyezése](../firewall/tutorial-firewall-deploy-portal.md)

- [Az Azure Marketplace harmadik féltől származó azonosító képességeket is tartalmaz](https://azuremarketplace.microsoft.com/marketplace?search=IDS)

- [Microsoft Defender ATP EDR-képesség](/windows/security/threat-protection/microsoft-defender-atp/overview-endpoint-detection-response)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

## <a name="identity-management"></a>Identitáskezelés

*További információ: [Azure Security Benchmark: Identitáskezelés](/azure/security/benchmarks/security-controls-v2-identity-management).*

### <a name="im-1-standardize-azure-active-directory-as-the-central-identity-and-authentication-system"></a>IM-1: Az Azure Active Directory, mint központi identitáskezelő és hitelesítési rendszer szabványosítása

**Útmutató**: a felhasználói fiókok leltárának fenntartása rendszergazdai hozzáféréssel a felügyeleti síkon (például Azure Portal) a Microsoft Azure társ-kezelési erőforrásokhoz.

Az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC) konfigurálásához használja az előfizetéshez tartozó Azure Portal identitás és hozzáférés-vezérlés (IAM) ablaktábláját. A szerepkörök a felhasználókra, csoportokra, egyszerű szolgáltatásokra és felügyelt identitásokra vonatkoznak Azure Active Directoryban (Azure AD).

- [Azure-beli szerepkör-hozzárendelés hozzáadása vagy eltávolítása az Azure Portal használatával](../role-based-access-control/role-assignments-portal.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="im-2-manage-application-identities-securely-and-automatically"></a>IM-2: Alkalmazásidentitások biztonságos és automatikus kezelése

**Útmutató**: a felügyelt identitások nem támogatottak a társ-felügyeleti szolgáltatásban. Olyan szolgáltatások esetén, amelyek nem támogatják a felügyelt identitásokat, például a társ-felügyeleti szolgáltatást, a Azure Active Directory (Azure AD) használatával hozzon létre egy egyszerű szolgáltatásnevet a korlátozott engedélyekkel az erőforrás szintjén. 

- [Azure-beli felügyelt identitások](../active-directory/managed-identities-azure-resources/overview.md)

- [Az Azure-erőforrások felügyelt identitásait támogató szolgáltatások](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md)

- [Azure-szolgáltatásnév](/powershell/azure/create-azure-service-principal-azureps)

- [Egyszerű szolgáltatás létrehozása tanúsítványokkal](../active-directory/develop/howto-authenticate-service-principal-powershell.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="im-3-use-azure-ad-single-sign-on-sso-for-application-access"></a>IM-3: Az Azure AD-beli egyszeri bejelentkezés használata alkalmazások eléréséhez

**Útmutató**: Microsoft Azure a társ-kezelési szolgáltatás Azure Active Directory (Azure ad) használatával biztosítja az identitás-és hozzáférés-kezelést az Azure-erőforrásokhoz. Ez vonatkozik az olyan nagyvállalati identitásokra, mint az alkalmazottak, valamint az olyan külső identitásokra is, mint a partnerek, szállítók és ellátók. 

Az egyszeri bejelentkezéssel felügyelheti és biztonságossá teheti a szervezet adataihoz és erőforrásaihoz való hozzáférést a helyszínen és a felhőben. Az összes felhasználót, alkalmazást és eszközt beléptetheti az Azure AD-be a zökkenőmentes, biztonságos hozzáférés, valamint a jobb átláthatóság és vezérlés érdekében.

- [Az Azure AD-vel megvalósított alkalmazás-SSO ismertetése](../active-directory/manage-apps/what-is-single-sign-on.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="im-4-use-strong-authentication-controls-for-all-azure-active-directory-based-access"></a>IM-4: Erős hitelesítési vezérlők használata minden Azure Active Directory-alapú hozzáféréshez

**Útmutató**: többtényezős hitelesítés engedélyezése Azure Active Directory (Azure ad) használatával és a Azure Security Center identitás-és hozzáférés-kezelési javaslatainak követése.

- [Többtényezős hitelesítés engedélyezése az Azure-ban](../active-directory/authentication/howto-mfa-getstarted.md)

- [Identitás és hozzáférés figyelése Azure Security Centeron belül](../security-center/security-center-identity-access.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="im-5-monitor-and-alert-on-account-anomalies"></a>IM-5: Fiókok rendellenességeinek monitorozása, és riasztás azok alapján

**Útmutató**: az PRIVILEGED Identity Management (PIM) használata a naplók és riasztások létrehozásához a Azure Active Directory (Azure ad) használatával, ha a környezetben gyanús vagy nem biztonságos tevékenység történik. Emellett az Azure AD kockázati észleléseit is használhatja a kockázatos felhasználói viselkedéssel kapcsolatos riasztások és jelentések megtekintéséhez.

- [Privileged Identity Management (PIM) üzembe helyezése](../active-directory/privileged-identity-management/pim-deployment-plan.md)

- [Az Azure AD kockázati észlelések ismertetése](../active-directory/identity-protection/overview-identity-protection.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="im-6-restrict-azure-resource-access-based-on-conditions"></a>IM-6: Azure-erőforrásokhoz való hozzáférés korlátozása feltételek alapján

**Útmutató**: a feltételes hozzáférés Azure Active Directory (Azure ad) használatával részletesebb hozzáférés-vezérlést használhat a felhasználó által definiált feltételek alapján, például bizonyos IP-címtartományok felhasználói bejelentkezésének megkövetelése többtényezős hitelesítés használatára. A részletes hitelesítési munkamenetek kezelése az Azure AD feltételes hozzáférési szabályzatán keresztül is felhasználható a különböző használati esetekben. 

- [Az Azure feltételes hozzáférés áttekintése](../active-directory/conditional-access/overview.md)

- [Általános feltételes hozzáférési szabályzatok](../active-directory/conditional-access/concept-conditional-access-policy-common.md)

- [A hitelesítési munkamenetek kezelésének konfigurálása feltételes hozzáféréssel](../active-directory/conditional-access/howto-conditional-access-session-lifetime.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="im-7-eliminate-unintended-credential-exposure"></a>IM-7: Hitelesítő adatok nem szándékos elérhetővé tételének kizárása

**Útmutató**: hitelesítő adatok beolvasása az Azure DevOps belül a kódban használt hitelesítő adatok azonosításához. A hitelesítő adatok képolvasó a felderített hitelesítő adatoknak a biztonságosabb helyszínekre (például Azure Key Vault) való áthelyezését is javasolja

A GitHubhoz használhatja a natív titkoskód-szűrési funkciót a kódon belüli hitelesítő adatok vagy másféle titkos kódok felismerésére.

- [A hitelesítő adatok beolvasójának beállítása](https://secdevtools.azurewebsites.net/helpcredscan.html)

- [Titkos kódok szűrése a GitHubban](https://docs.github.com/github/administering-a-repository/about-secret-scanning)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

## <a name="privileged-access"></a>Emelt jogosultságú hozzáférés

*További információ: [Azure Security Benchmark: Emelt jogosultságú hozzáférés](/azure/security/benchmarks/security-controls-v2-privileged-access).*

### <a name="pa-1-protect-and-limit-highly-privileged-users"></a>PA-1: Emelt jogosultságú felhasználók védelme és korlátozása

**Útmutató**: korlátozza a magas jogosultságú felhasználói fiókok számát, és emelt szintű védelemmel látja el ezeket a fiókokat. 

A Azure Active Directory (Azure AD) legjelentősebb beépített szerepkörei a globális rendszergazda és a Kiemelt szerepkörű rendszergazda, mert a két szerepkörhöz hozzárendelt felhasználók delegálni tudják a rendszergazdai szerepköröket. 

Ezekkel a jogosultságokkal a felhasználók közvetlenül vagy közvetve elolvashatják és módosíthatják az Azure-környezetben található összes erőforrást:

- Globális rendszergazda/vállalati rendszergazda: az ehhez a szerepkörhöz tartozó felhasználók hozzáférhetnek az Azure AD összes felügyeleti funkciójához, valamint az Azure AD-identitásokat használó szolgáltatásokhoz.

- Kiemelt szerepkörű rendszergazda: az ezzel a szerepkörrel rendelkező felhasználók kezelhetik a szerepkör-hozzárendeléseket az Azure AD-ben, valamint a Azure AD Privileged Identity Managementon (PIM) belül is. Emellett ez a szerepkör lehetővé teszi a PIM és a felügyeleti egységek valamennyi aspektusának kezelését.

Előfordulhat, hogy más kritikus szerepkörökre is szükség van, amelyek akkor érvényesek, ha egyéni szerepköröket használ a hozzájuk rendelt, bizonyos jogosultsági szintű engedélyekkel. Hasonló vezérlőket alkalmazhat a kritikus fontosságú üzleti eszközök rendszergazdai fiókjára.  

Az Azure-erőforrásokhoz és az Azure AD-hez való igény szerinti (JIT) jogosultságú hozzáférés engedélyezése Azure AD Privileged Identity Management (PIM) használatával. A JIT ideiglenes engedélyeket biztosít az érintett feladatok végrehajtásához, csak annyi időre, ameddig a felhasználóknak erre szükségük van. A PIM biztonsági riasztásokat is képes kiadni, amikor gyanús vagy nem biztonságos tevékenységeket észlel az Azure AD-szervezetben.

- [Rendszergazdai szerepköri engedélyek az Azure AD-ben](/azure/active-directory/users-groups-roles/directory-assign-admin-roles)

- [Az Azure Privileged Identity Management biztonsági riasztásainak használata](../active-directory/privileged-identity-management/pim-how-to-configure-security-alerts.md)

- [Emelt szintű hozzáférés biztosítása Azure AD hibrid- és felhőkörnyezetekhez](/azure/active-directory/users-groups-roles/directory-admin-roles-secure)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="pa-2-restrict-administrative-access-to-business-critical-systems"></a>PA-2: Az üzletileg kritikus rendszerek rendszergazdai elérésének korlátozása

**Útmutató**: az üzleti szempontból kritikus fontosságú rendszerekhez való hozzáférés elkülönítése a fiókokat, amelyek jogosultsági szintű hozzáférést kaptak a hozzájuk tartozó előfizetésekhez és felügyeleti csoportokhoz. 

Korlátozza azon felügyeleti, identitási és biztonsági rendszerekhez való hozzáférést, amelyek rendszergazdai hozzáféréssel rendelkeznek az üzleti szempontból kritikus fontosságú eszközökhöz, például a Active Directory-tartomány vezérlőkhöz (DC), a biztonsági eszközökhöz és a Rendszerfelügyeleti eszközökhöz, amelyek az üzleti szempontból kritikus rendszereken telepített ügynökökkel rendelkeznek. Azok a támadók, akik veszélyeztetik ezeket a felügyeleti és biztonsági rendszereket, azonnal weaponize azokat az üzleti szempontból kritikus fontosságú eszközök biztonsága érdekében. 

A hozzáférés-vezérlés összes típusát a vállalati szegmentálási stratégiához kell igazítani, hogy biztosítsa a konzisztens hozzáférés-vezérlést. 

Győződjön meg arról, hogy az e-mail-, a böngészési és a termelékenységi feladatokhoz használt normál felhasználói fiókoktól eltérő, különálló rendszerjogosultságú fiókokat rendel hozzá.

- [Azure-összetevők és-hivatkozási modell](/security/compass/microsoft-security-compass-introduction#azure-components-and-reference-model-2151)

- [Felügyeleti csoport elérése](https://docs.microsoft.com/azure/governance/management-groups/overview#management-group-access)

- [Azure-előfizetés rendszergazdái](../cost-management-billing/manage/add-change-subscription-administrator.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="pa-3-review-and-reconcile-user-access-regularly"></a>PA-3: Felhasználói hozzáférés rendszerességének áttekintése és egyeztetése

**Útmutató**: a Azure Active Directory (ad) olyan naplókat biztosít, amelyek segítenek az elavult fiókok felderítésében. Emellett az Azure Identity Access Reviews használatával hatékonyan kezelheti a csoporttagságok kezelését, a vállalati alkalmazásokhoz való hozzáférést és a szerepkör-hozzárendeléseket. A felhasználói hozzáférés rendszeresen felülvizsgálható, hogy csak a megfelelő felhasználók férhessenek hozzá.

- [Azure AD-tevékenységgel kapcsolatos jelentések keresése](../active-directory/reports-monitoring/howto-find-activity-reports.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="pa-4-set-up-emergency-access-in-azure-ad"></a>PA-4: Vészhelyzeti hozzáférés beállítása az Azure AD-ben

**Útmutató**: vészhelyzeti fiók beállítása a hozzáféréshez, ha a normál rendszergazdai fiókok nem érhetők el, nehogy véletlenül kizárja a Azure Active Directory (Azure ad) szervezetét. A vészhelyzeti hozzáférési fiókok magasabb szintű jogosultságokat tartalmazhatnak, és nem rendelhetők hozzá konkrét személyekhez. Korlátozza a sürgősségi hozzáférési fiókokat a vészhelyzet vagy az "üveg megszakítása" forgatókönyvek esetében.

Győződjön meg arról, hogy a vészhelyzeti hozzáférési fiókok hitelesítő adatai (például a jelszó, a tanúsítvány vagy az intelligens kártya) biztonságosak és ismertek, csak azokra a személyekre, akik jogosultak a kialakulóban lévő helyzetekben való használatra.

- [Vészhelyzeti hozzáférési fiókok kezelése az Azure AD-ben](/azure/active-directory/users-groups-roles/directory-emergency-access)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="pa-5-automate-entitlement-management"></a>PA-5: a jogosultságok kezelésének automatizálása 

**Útmutató**: a hozzáférési kérelmek munkafolyamatainak automatizálásához használja a Azure Active Directory (Azure ad) jogosultság-kezelési funkcióit, beleértve a hozzáférési hozzárendeléseket, a felülvizsgálatokat és a lejáratot. A kettős vagy többfázisú jóváhagyás is támogatott.

- [Mik azok a Azure Active Directory (Azure AD) hozzáférési felülvizsgálatok](../active-directory/governance/access-reviews-overview.md)

- [Mi az Azure Active Directory (Azure AD) jogosultságok kezelése](../active-directory/governance/entitlement-management-overview.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="pa-6-use-privileged-access-workstations"></a>PA-6: Emelt szintű hozzáférésű munkaállomások használata

**Útmutató**: a Azure Active Directory (Azure ad) többtényezős hitelesítésével rendelkező privilegizált hozzáférési munkaállomás (Paw) használata, amely lehetővé teszi, hogy bejelentkezzen az Azure Sentinel szolgáltatással kapcsolatos erőforrásaiba, és konfigurálja azokat.

- [Emelt hozzáférési szintű munkaállomások](/windows-server/identity/securing-privileged-access/privileged-access-workstations)

- [Felhőalapú Azure AD többtényezős hitelesítés üzembe helyezésének megtervezése](../active-directory/authentication/howto-mfa-getstarted.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="pa-7-follow-just-enough-administration-least-privilege-principle"></a>PA-7: A Just Enough Administration (legkisebb jogosultsági alapelv) követése 

**Útmutató**: az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC) lehetővé teszi az Azure-erőforrások hozzáférésének kezelését a szerepkör-hozzárendeléseken keresztül. Ezeket a szerepköröket hozzárendelheti a felhasználókhoz, a csoport egyszerű szolgáltatásaihoz és a felügyelt identitásokhoz. Bizonyos erőforrásokhoz előre definiált beépített szerepkörök tartoznak, és ezeket a szerepköröket leltározott vagy lekérdezheti olyan eszközökkel, mint például az Azure CLI, a Azure PowerShell és a Azure Portal.

Az erőforrásokhoz az Azure RBAC-on keresztül hozzárendelt jogosultságokat mindig a szerepkörök által igényelt értékre kell korlátozni. A korlátozott jogosultságok kiegészítik a Azure Active Directory (Azure AD) Privileged Identity Management (PIM) igény szerinti (JIT) megközelítését, és ezeket a jogosultságokat rendszeresen felül kell vizsgálni.

A beépített szerepkörökkel engedélyeket oszthat ki, és csak akkor kell egyéni szerepköröket létrehoznia, ha szükséges.

- [Mi az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC)?](../role-based-access-control/overview.md)

- [Az Azure RBAC konfigurálása](../role-based-access-control/role-assignments-portal.md)

- [Az Azure AD identitás- és hozzáférési felülvizsgálatainak használata](../active-directory/governance/access-reviews-overview.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

## <a name="asset-management"></a>Asset Management (Eszközkezelés)

*További információ: [Azure Security Benchmark: Összetevők kezelése](/azure/security/benchmarks/security-controls-v2-asset-management).*

### <a name="am-1-ensure-security-team-has-visibility-into-risks-for-assets"></a>AM-1: Gondoskodás arról, hogy a biztonsági csapat átlássa az adategységek kockázatait

**Útmutató**: Győződjön meg arról, hogy a biztonsági csapatok biztonsági olvasó engedélyekkel rendelkeznek az Azure-bérlőben és -előfizetésekben, hogy monitorozni tudják a biztonsági kockázatokat az Azure Security Centerrel. 

A biztonsági kockázatok monitorozása a biztonsági csapat felelősségi köreinek struktúrájától függően egy központi biztonsági csapat vagy egy helyi csapat felelőssége lehet. A biztonsági megállapításokat és kockázatokat azonban mindig központilag kell összesíteni egy szervezeten belül. 

A biztonsági olvasó engedélyek széles körben alkalmazhatók egy teljes bérlőre (gyökérszintű felügyeleti csoport), vagy a hatókör alkalmazható adott felügyeleti csoportokra vagy előfizetésekre. 

További engedélyek szükségesek lehetnek a számítási feladatok és szolgáltatások láthatóságának megismeréséhez. 

- [A biztonsági olvasó szerepkör áttekintése](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#security-reader)

- [Az Azure-beli felügyeleti csoportok áttekintése](../governance/management-groups/overview.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="am-2-ensure-security-team-has-access-to-asset-inventory-and-metadata"></a>AM-2: Az összetevőleltár és a metaadatok biztonsági csapat általi elérhetőségének biztosítása

**Útmutató**: gondoskodjon arról, hogy a biztonsági csapatok hozzáférjenek az Azure-beli eszközök folyamatosan frissített leltározásához. A biztonsági csapatoknak gyakran van szükségük erre a leltárra a vállalat új kockázatokkal szembeni kitettségének felméréséhez, és a folyamatos biztonsági fejlesztéséket szolgáló információként. 

A Azure Security Center Inventory szolgáltatás és az Azure Resource Graph az előfizetések összes erőforrását lekérdezheti és felderítheti, beleértve az Azure-szolgáltatásokat, az alkalmazásokat és a hálózati erőforrásokat is.  

Az összetevőket címkék, valamint más Azure-beli metaadatok (név, leírás és kategória) használatával rendszerezheti logikailag a vállalati elnevezéseknek megfelelően.  

- [Lekérdezések létrehozása az Azure Resource Graph Explorerrel](../governance/resource-graph/first-query-portal.md)

- [Azure Security Center Asset Inventory Management](../security-center/asset-inventory.md)

- [További információ az eszközök címkézéséről: erőforrás-elnevezési és címkézési döntési útmutató](https://docs.microsoft.com/azure/cloud-adoption-framework/decision-guides/resource-tagging/?toc=/azure/azure-resource-manager/management/toc.json)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="am-3-use-only-approved-azure-services"></a>AM-3: Csak jóváhagyott Azure-szolgáltatások használata

**Útmutató**: a Microsoft Azure társ-felügyeleti szolgáltatás a "Microsoft. peering/társais" névtér Azure Policy használatával támogatja a Azure Resource Manager-alapú telepítéseket és a konfiguráció kényszerítését. Az Azure Policy használatával ellenőrizheti és korlátozhatja a felhasználók által a környezetben kiépíthető szolgáltatások körét. Az Azure Resource Graph használatával lekérdezheti és felderítheti az előfizetésükön belüli erőforrásokat. Az Azure Monitort is használhatja olyan szabályok létrehozásához, amelyek riasztást aktiválnak nem jóváhagyott szolgáltatás észlelésekor.

- [Az Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md)

- [Adott erőforrástípus megtagadása a következővel Azure Policy](https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#general)

- [Lekérdezések létrehozása az Azure Resource Graph Explorerrel](../governance/resource-graph/first-query-portal.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="am-5-limit-users-ability-to-interact-with-azure-resource-manager"></a>AM-5: korlátozza a felhasználók képességét a Azure Resource Manager való interakcióra

**Útmutató**: az Azure feltételes hozzáférésének konfigurálása a felhasználók "Microsoft Azure felügyelet" alkalmazáshoz való hozzáférésének tiltása a Azure Resource Manager való interakcióra.

- [A feltételes hozzáférés konfigurálása a Azure Resource Managerhoz való hozzáférés blokkolásához](../role-based-access-control/conditional-access-azure-management.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

## <a name="logging-and-threat-detection"></a>Naplózás és fenyegetésészlelés

*További információ: [Azure Security Benchmark: Naplózás és fenyegetésészlelés](/azure/security/benchmarks/security-controls-v2-logging-threat-protection).*

### <a name="lt-1-enable-threat-detection-for-azure-resources"></a>LT-1: az Azure-erőforrások fenyegetés-észlelésének engedélyezése

**Útmutató**: Ellenőrizze, hogy a különböző típusú Azure-eszközöket figyeli-e a potenciális fenyegetések és rendellenességek tekintetében. A magas színvonalú riasztások beszerzésével csökkentheti a hamis pozitív értéket az elemzők számára a rendezéshez. A riasztások naplózási adatokból, ügynökökből vagy más adatokból is származnak.

Használja a Azure Security Center beépített veszélyforrások észlelésére szolgáló funkciót, amely az Azure-szolgáltatások telemetria figyelésén és a szolgáltatási naplók elemzésén alapul. Az adatok gyűjtése a Log Analytics ügynök használatával történik, amely beolvassa a különböző biztonsággal kapcsolatos konfigurációkat és eseménynaplókat a rendszerből, és az adatokat a munkaterületre másolja az elemzéshez. 

Emellett az Azure Sentinel használatával elemzési szabályokat hozhat létre, amelyek a környezetében meghatározott feltételeknek megfelelő veszélyforrásokat vadásznak. A szabályok a feltételek teljesülése esetén eredményeznek incidenseket, így az egyes incidensek megvizsgálható. Az Azure Sentinel a fenyegetések észlelési képességének növelése érdekében importálhatja a harmadik féltől származó fenyegetések felderítését is. 

- [Fenyegetésvédelem az Azure Security Centerben](/azure/security-center/threat-protection)

- [Azure Security Center biztonsági riasztások referenciája](../security-center/alerts-reference.md)

- [Egyéni elemzési szabályok létrehozása a fenyegetések észleléséhez](../sentinel/tutorial-detect-threats-custom.md)

- [A Cyber Threat Intelligence és az Azure Sentinel](/azure/architecture/example-scenario/data/sentinel-threat-intelligence)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="lt-2-enable-threat-detection-for-azure-identity-and-access-management"></a>LT-2: Fenyegetések észlelésének engedélyezése az Azure-beli identitás- és hozzáférés-kezeléshez

**Útmutató**: a Azure Active Directory (Azure ad) olyan felhasználói naplókat biztosít, amelyek az Azure ad-jelentéskészítéssel vagy az Azure monitor-vel való integrációval jeleníthetők meg. Ezek a naplók az Azure Sentinel vagy más biztonsági információ-és eseménykezelő (SIEM) megoldással és figyelési eszközökkel is integrálhatók a kifinomultabb monitorozási és elemzési felhasználási esetekben:

- Bejelentkezés – a bejelentkezési jelentés információt nyújt a felügyelt alkalmazások és a felhasználói bejelentkezési tevékenységek használatáról

- Naplók – az Azure AD-n belüli különböző szolgáltatások által végzett összes módosítás naplókban való nyomon követését teszi lehetővé. A naplók például a felhasználók, alkalmazások, csoportok, szerepkörök és házirendek hozzáadásával vagy eltávolításával kapcsolatos, az Azure AD-ban található összes erőforráson végrehajtott módosítások.

- Kockázatos bejelentkezés – A kockázatos bejelentkezés egy olyan bejelentkezési kísérletre utal, amelyet a felhasználói fiók tulajdonosának nem jogos tulajdonosa végez.

- Kockázatnak kitett felhasználók – A kockázatos felhasználók egy olyan felhasználói fiókra vonatkozó jelző, amely esetleg sérült

Azure Security Center riasztást is beállíthat bizonyos gyanús tevékenységekről, például az előfizetések elavult fiókjainak túlzott számú sikertelen hitelesítési kísérletéről. Az alapszintű biztonsági higiénia monitorozása mellett a Security Center veszélyforrások elleni védelmi modul is gyűjthet további részletes biztonsági riasztásokat az egyes Azure-beli számítási erőforrásokból (például a Virtual Machines, a containers, az App Service szolgáltatásból), az adatforrásokból (például az SQL-ADATBÁZISból és a tárolóból) és az Azure szolgáltatási rétegeiből. Ez a funkció lehetővé teszi, hogy az egyes erőforrásokon belül megtekintse a fiókok rendellenességeit.

- [Tevékenység-jelentések naplózása az Azure AD-ben](../active-directory/reports-monitoring/concept-audit-logs.md)

- [Az Azure Identity Protection engedélyezése](../active-directory/identity-protection/overview-identity-protection.md)

- [Fenyegetésvédelem az Azure Security Centerben](/azure/security-center/threat-protection)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="lt-3-enable-logging-for-azure-network-activities"></a>LT-3: Naplózás engedélyezése Azure-beli hálózati tevékenységekhez

**Útmutató**: a kapcsolat telemetria konfigurálása, amely betekintést nyújt a kapcsolódási hely és a

Microsoft Network Microsoft Azure peering Service használatával.

- [A kapcsolatok telemetria konfigurálása](measure-connection-telemetry.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="lt-4-enable-logging-for-azure-resources"></a>LT-4: Naplózás engedélyezése Azure-erőforrásokhoz

**Útmutató**: az Azure-tevékenység naplójának engedélyezése és a naplók elküldése egy log Analytics-munkaterületre, Azure Event hub vagy Azure Storage-fiókba archiválásra 

A tevékenységi naplók betekintést nyújtanak az Azure ExpressRoute-erőforrásokon végrehajtott műveletekre a vezérlési sík szintjén. Az Azure-tevékenység naplójának adatai alapján meghatározhatja a "mit, ki és mikor" típusú írási műveleteket (PUT, POST, DELETE) az erőforrások vezérlési síkja szintjén.

- [Azure-tevékenység naplója](/azure/azure-monitor/platform/activity-log)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="lt-5-centralize-security-log-management-and-analysis"></a>LT-5: Biztonsági naplók kezelésének és elemzésének központosítása

**Útmutató**: a korreláció engedélyezéséhez központosítsa a naplózási tárolást és az elemzést. Minden naplózási forrás esetében ellenőrizze, hogy rendelkezik-e az adatok tulajdonosával, a hozzáférési útmutatással, a tárolási hellyel, az adatok feldolgozásához és eléréséhez, valamint az adatmegőrzési követelményekhez használt eszközökhöz.

Győződjön meg arról, hogy integrálja az Azure-tevékenységek naplóit a központi naplózásba. Naplók betöltése Azure Monitor használatával a végponti eszközök, a hálózati erőforrások és más biztonsági rendszerek által generált biztonsági adatokat összesítve. Azure Monitor a Log Analytics-munkaterületek használatával kérdezheti le és végezheti el az elemzéseket, és használhatja az Azure Storage-fiókokat hosszú távú és archiválási tároláshoz.

Emellett az Azure Sentinel vagy egy harmadik féltől származó SIEM-nek is engedélyezi az adatok bevezetését.

Számos szervezet úgy dönt, hogy a gyakran használt és az Azure-tárolót használja a ritkábban használt adatokhoz az Azure Sentinel használatával.

- [Platform-naplók és-metrikák összegyűjtése Azure Monitor](../azure-monitor/essentials/diagnostic-settings.md)

- [Az Azure Sentinel előkészítése](../sentinel/quickstart-onboard.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="lt-6-configure-log-storage-retention"></a>LT-6: Tárolt naplók megőrzésének konfigurálása

**Útmutató**: Azure monitorban az Azure-erőforrásokhoz társított log Analytics-munkaterületek naplózási megőrzési időtartamát a szervezet megfelelőségi előírásai szerint állítsa be.

- [Napló-megőrzési paraméterek beállítása](/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

## <a name="incident-response"></a>Incidensmegoldás

*További információ: [Azure Security Benchmark: Incidensek kezelése](/azure/security/benchmarks/security-controls-v2-incident-response).*

### <a name="ir-1-preparation--update-incident-response-process-for-azure"></a>IR-1: Előkészítés – incidensválasz-folyamat frissítése az Azure-hoz

**Útmutató**: Gondoskodjon róla, hogy a vállalata rendelkezik a biztonsági incidensek kezelésére szolgáló folyamattal, ezeket az folyamatokat az Azure-hoz igazította, és rendszeresen gyakorlja a készenlét érdekében.

- [Biztonság implementálása a teljes nagyvállalati környezetben](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

- [Incidensválasz referencia-útmutató](/microsoft-365/downloads/IR-Reference-Guide.pdf)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="ir-2-preparation--setup-incident-notification"></a>IR-2: Előkészítés – incidensről szóló értesítés beállítása

**Útmutató**: Állítson be a biztonsági incidensekre vonatkozó kapcsolattartási adatokat az Azure Security Centerben. A Microsoft ezen kapcsolattartási adatok használatával keresi meg Önt, ha a Microsoft Security Response Center (MSCR) felfedezi, hogy az adataihoz törvénytelen vagy jogosulatlan módon fértek hozzá. Lehetősége van az incidensekkel kapcsolatos riasztások és értesítések testreszabására a különböző Azure-szolgáltatásokban az incidensválasz-igények alapján. 

- [Az Azure Security Center biztonsági kapcsolattartójának beállítása](../security-center/security-center-provide-security-contact-details.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="ir-3-detection-and-analysis--create-incidents-based-on-high-quality-alerts"></a>IR-3: észlelés és elemzés – incidensek létrehozása kiváló minőségű riasztások alapján 

**Útmutató**: Gondoskodjon egy, a magas minőségű riasztások létrehozására és a riasztások minőségének mérésére szolgáló folyamatról. Ez lehetővé teszi a korábbi incidensek tanulságainak levonását és a riasztások rangsorolását az elemzők számára, hogy ne veszítsenek időt a téves riasztások miatt. 

A magas minőségű riasztások készítése épülhet a korábbi incidensek tapasztalataira, az ellenőrzött közösségi forrásokra, valamint azokra az eszközökre, amelyek a riasztások generálását és tisztítását a különböző jelforrások egyesítésével és egyeztetésével végzik. 

Azure Security Center kiváló minőségű riasztásokat biztosít számos Azure-eszközön. Az ASC adatösszekötővel streamelheti a riasztásokat az Azure Sentinelbe. Az Azure Sentinellel speciális riasztási szabályokat hozhat létre, amelyekkel automatikusan hozhat létre incidenseket a vizsgálatokhoz. 

Az exportálási funkcióval exportálhatja az Azure Security Center riasztásait és javaslatait, amelyek segítenek az Azure-erőforrások kockázatainak azonosításában. A riasztásokat és javaslatokat manuálisan vagy folyamatosan is exportálhatja.

- [Az exportálás konfigurálása](../security-center/continuous-export.md)

- [Riasztások streamelése az Azure Sentinelbe](../sentinel/connect-azure-security-center.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="ir-4-detection-and-analysis--investigate-an-incident"></a>IR-4: Észlelés és elemzés – incidensek kivizsgálása

**Útmutató**: az elemzők a különböző adatforrások lekérdezésére és használatára szolgálnak, mivel megvizsgálják a lehetséges incidenseket, így teljes képet kaphatnak arról, hogy mi történt. A vakfoltok elkerülése érdekében különböző naplókat kell gyűjteni a potenciális támadók tevékenységének követéséhez a támadási útvonalon.  A megállapítások és eredmények rögzítéséről is érdemes gondoskodnia, hogy később más elemzők referenciaként használhassák őket.  

A vizsgálathoz használt adatforrások közé tartoznak azok a központi naplózási források, amelyeket a rendszer már gyűjt a hatókörbeli szolgáltatásokból és a futó rendszerekről, de a következők is lehetnek:

- Hálózati adatok – az Azure Network Watcher, az Azure Monitor és a hálózati biztonsági csoportok forgalmi naplóinak használata a hálózati forgalom naplóinak és más elemzési adatok rögzítéséhez. 

- A futó rendszerek pillanatképei: 

    - Az Azure-beli virtuális gép pillanatkép-készítési funkciójával pillanatképet készíthet a futó rendszer lemezéről. 

    - Az operációs rendszer natív memóriakép-készítési funkciójával pillanatképet készíthet a futó rendszer memóriájáról.

    - Az Azure-szolgáltatások vagy saját szoftvere pillanatkép-készítési funkciójával pillanatképeket készíthet a futó rendszerekről.

Az Azure Sentinel széles körű adatelemzést tesz lehetővé szinte bármely naplózási forráson és egy esetfelügyeleti portálon az incidensek teljes életciklusának kezeléséhez. A vizsgálatok során kapott információk társíthatók egy incidenssel nyomkövetés és jelentéskészítés céljából. 

- [Windows rendszerű gép lemezének pillanatképe](../virtual-machines/windows/snapshot-copy-managed-disk.md)

- [Linux rendszerű gép lemezének pillanatképe](../virtual-machines/linux/snapshot-copy-managed-disk.md)

- [Microsoft Azure-támogatás – diagnosztikai információk és memóriaképek gyűjtése](https://azure.microsoft.com/support/legal/support-diagnostic-information-collection/) 

- [Incidensek vizsgálata az Azure Sentinellel](../sentinel/tutorial-investigate-cases.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="ir-5-detection-and-analysis--prioritize-incidents"></a>IR-5: Észlelés és elemzés – incidensek rangsorolása

**Útmutató**: Megadhat arra vonatkozó információkat az elemzők számára, hogy melyik incidensekre kell először összpontosítaniuk a súlyosság és az objektum bizalmassága alapján. 

Az Azure Security Center súlyossági szintet rendel az egyes riasztásokhoz, és segít rangsorolni, hogy melyik riasztásokat kell először kivizsgálni. A súlyosság azon alapul, hogy az Security Center milyen mértékben szerepel a riasztás kibocsátásához használt elemzésben vagy elemzésben, valamint azt a megbízhatósági szintet, amely a riasztáshoz vezető tevékenység mögött rosszindulatú szándékú volt.

Emellett a címkék használatával megjelölheti az erőforrásokat, és létrehozhat egy elnevezési rendszert az Azure-erőforrások, különösen a bizalmas adatok feldolgozását végző erőforrások azonosításához és kategorizálásához.  Az Ön felelőssége, hogy rangsorolja a riasztások megoldását azon Azure-erőforrások és -környezetek kritikussága alapján, ahol az incidens történt.

- [Biztonsági riasztások az Azure Security Centerben](../security-center/security-center-alerts-overview.md)

- [Címkék használata az Azure-erőforrások rendszerezéséhez](/azure/azure-resource-manager/resource-group-using-tags)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="ir-6-containment-eradication-and-recovery--automate-the-incident-handling"></a>IR-6: Elszigetelés, kiiktatás és helyreállítás – az incidenskezelés automatizálása

**Útmutató**: Az ismétlődő manuális tevékenységek automatizálásával jobb reakcióidő érhető el, és csökkenthető az elemzők terhelése. A manuális tevékenységek végrehajtása több időt vesz igénybe, emiatt az elemzők kevesebb incidenst képesek kezelni. A manuális feladatok ki is merítik az elemzőket, ami növeli az emberi hiba lehetőségét, és az elemzők kevésbé tudnak hatékonyan összpontosítani az összetett feladatokra. Az Azure Security Center és az Azure Sentinel munkafolyamat-automatizálási funkcióival automatikusan indíthat el műveleteket és futtathat forgatókönyveket, hogy reagáljon a bejövő biztonsági riasztásokra. A forgatókönyv műveleteket hajt végre, például értesítéseket küld, fiókokat tilt le, és elkülöníti a problémás hálózatokat. 

- [Munkafolyamat-automatizálás konfigurálása a Security Centerben](../security-center/workflow-automation.md)

- [Fenyegetésre adott automatikus válaszok beállítása az Azure Security Centerben](https://docs.microsoft.com/azure/security-center/tutorial-security-incident#triage-security-alerts)

- [Fenyegetésre adott automatikus válaszok beállítása az Azure Sentinelben](../sentinel/tutorial-respond-threats-playbook.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

## <a name="posture-and-vulnerability-management"></a>A biztonsági állapot és a biztonsági rések kezelése

*További információ: [Azure Security Benchmark: A biztonsági állapot és a biztonsági rések kezelése](/azure/security/benchmarks/security-controls-v2-vulnerability-management).*

### <a name="pv-1-establish-secure-configurations-for-azure-services"></a>PV-1: Biztonságos konfigurációk kialakítása Azure-szolgáltatásokhoz 

**Útmutató**: az infrastruktúra-és DevOps-csapatok biztonsági guardrails megadásával egyszerűen konfigurálhatja az általuk használt Azure-szolgáltatásokat. 

Indítsa el az Azure-szolgáltatások biztonsági konfigurációját az Azure-beli biztonsági Teljesítménytesztben található szolgáltatási alaptervekkel, és szabja testre a szervezete igényei szerint. 

A Azure Security Center használatával konfigurálhatja az Azure-erőforrások konfigurációjának naplózását és érvényesítését Azure Policy. 

Az Azure tervrajzai segítségével automatizálhatja a szolgáltatások és az alkalmazások környezetének üzembe helyezését és konfigurálását, beleértve a Azure Resource Manager sablonokat, az Azure RBAC-vezérlőket és a házirendeket egyetlen terv definíciójában.

- [A guardrails megvalósításának szemléltetése a nagyvállalati szintű kirakodási zónában](/azure/cloud-adoption-framework/ready/enterprise-scale/architecture#landing-zone-expanded-definition)

- [A biztonsági szabályzatok használata a Azure Security Centerban](../security-center/tutorial-security-policy.md)

- [Szabályzatok létrehozása és kezelése a megfelelőség kikényszerítése céljából](../governance/policy/tutorials/create-and-manage.md)

- [Azure Blueprints](../governance/blueprints/overview.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="pv-2-sustain-secure-configurations-for-azure-services"></a>PV-2: Biztonságos konfigurációk fenntartása Azure-szolgáltatásokhoz

**Útmutató**: nem alkalmazható;  Ez a javaslat számítási erőforrások számára készült.

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="pv-8-conduct-regular-attack-simulation"></a>PV-8: Rendszeres támadásszimulációk végrehajtása

**Útmutató**: Szükség esetén végezzen behatolási teszteket vagy riasztási gyakorlatokat az Azure-erőforrásokon, hogy biztosítva legyen az összes kritikus biztonsági találat megoldása.
A Microsoft-felhő behatolástesztelési beavatkozási szabályai szerint eljárva biztosíthatja, hogy a behatolási tesztek nem sértik a Microsoft szabályzatait. A Microsoft által felügyelt felhőalapú infrastruktúrán, szolgáltatásokon és alkalmazásokon végzett riasztási és élő behatolási tesztek végrehajtásához használja a Microsoft stratégiáját és végrehajtási tervét.

- [Behatolási tesztek az Azure-ban](../security/fundamentals/pen-testing.md)

- [Behatolástesztelési beavatkozási szabályok](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) 

- [Riasztási tesztek a Microsoft-felhőben](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Felelősség**: Megosztott

**Azure Security Center figyelés**: nincs

## <a name="governance-and-strategy"></a>Irányítás és stratégia

*További információ: [Azure Security Benchmark: Irányítás és stratégia](/azure/security/benchmarks/security-controls-v2-governance-strategy).*

### <a name="gs-1-define-asset-management-and-data-protection-strategy"></a>GS-1: Eszközkezelési és adatvédelmi stratégia 

**Útmutató**: Mindig dokumentáljon és tegyen közzé a rendszerek és adatok folyamatos monitorozására és védelmére vonatkozó egyértelmű stratégiát. Állítsa fel az üzletileg kritikus adatok és rendszerek felmérésének, védelmének és monitorozásának fontossági sorrendjét. 

Ennek a stratégiának magában kell foglalnia a dokumentált útmutatót, és az alábbi elemek szabványait: 

-   Az üzleti kockázattal összhangban lévő adatbesorolási szabványok

-   A kockázatok és az eszközleltár biztonsági szervezet általi láthatósága 

-   A használandó Azure-szolgáltatások biztonsági szervezet általi jóváhagyása 

-   Az biztonsága azok teljes életciklusában

-   A vállalati adabesorolással összhangban szükséges hozzáférés-vezérlési stratégia

-   Natív Azure-beli és külső adatvédelmi képességek használata

-   Átvitel közbeni és inaktív adattitkosításra vonatkozó követelmények

-   A megfelelő titkosítási szabványok

További információkat az alábbi hivatkozásokon találhat:
- [Az Azure Security architektúrára vonatkozó ajánlásai – Tárolás, adatok és titkosítás](https://docs.microsoft.com/azure/architecture/framework/security/storage-data-encryption?toc=/security/compass/toc.json&amp;bc=/security/compass/breadcrumb/toc.json)

- [Az Azure Security alapjai – Azure-beli adatbiztonság, titkosítás és tárolás](../security/fundamentals/encryption-overview.md)

- [Felhőadaptálási keretrendszer – Az Azure-beli adatbiztonsághoz és titkosításhoz ajánlott eljárások](https://docs.microsoft.com/azure/security/fundamentals/data-encryption-best-practices?toc=/azure/cloud-adoption-framework/toc.json&amp;bc=/azure/cloud-adoption-framework/_bread/toc.json)

- [Azure Security Benchmark – Eszközkezelés](/azure/security/benchmarks/security-benchmark-v2-asset-management)

- [Azure Security Benchmark – Adatvédelem](/azure/security/benchmarks/security-benchmark-v2-data-protection)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="gs-2-define-enterprise-segmentation-strategy"></a>GS-2: Nagyvállalati szegmentálási stratégia definiálása 

**Útmutató**: Alakítson ki az egész vállalatra kiterjedő stratégiát, amely identitás, alkalmazás, előfizetés, felügyeleti csoport és más vezérlők kombinációjával szegmentálja az összetevőkhöz való hozzáférést.

Gondosan egyensúlyozza ki a biztonsági elkülönítés igényét azoknak a rendszereknek a mindennapos működésével, amelyeknek kommunikálniuk kell egymással, és hozzá kell férniük az adatokhoz.

Gondoskodjon róla, hogy a szegmentálási stratégia következetesen meg legyen valósítva az olyan vezérlőtípusokon, mint a hálózati biztonság, az identitás- és hozzáférés-modellek, az alkalmazások jogosultsági és hozzáférési modelljei és az emberi folyamatokra vonatkozó vezérlők.

- [Útmutató Azure-beli szegmentálási stratégiához (videó)](/security/compass/microsoft-security-compass-introduction#azure-components-and-reference-model-2151)

- [Útmutató Azure-beli szegmentálási stratégiához (dokumentum)](/security/compass/governance#enterprise-segmentation-strategy)

- [Hálózati szegmentálás vállalati szegmentálási stratégiához igazítása](/security/compass/network-security-containment#align-network-segmentation-with-enterprise-segmentation-strategy)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="gs-3-define-security-posture-management-strategy"></a>GS-3: Biztonsági állapotot felügyelő stratégiája definiálása

**Útmutató**: Folyamatosan mérje és mérsékelje a kockázatokat, amelyeknek az egyes összetevők, valamint az a környezet van kitéve, amelyben üzemeltetve vannak. Kezelje kiemelten az olyan nagy értékű összetevőket és leginkább elérhetővé tett támadási felületeket, mint a közzétett alkalmazások, a hálózat be- és kilépési pontjai, a felhasználói és rendszergazdai végpontok stb.

- [Azure Security Benchmark - Biztonsági állapot és biztonsági rések felmérése](/azure/security/benchmarks/security-benchmark-v2-posture-vulnerability-management)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="gs-4-align-organization-roles-responsibilities-and-accountabilities"></a>GS-4: A vállalati szerepkörök, a felelősség és az elszámoltathatóság összehangolása

**Útmutató**: Mindig dokumentálja és tegye közzé a biztonsági szervezet szerepköreire és felelősségi köreire vonatkozó egyértelmű stratégiát. Kezelje kiemelten a biztonsági döntések egyértelmű elszámoltathatóságát, a megosztott felelősségi modell oktatását mindenki számára, és a technikai csapatok oktatását a felhőbeli biztonsági technikákra.

- [Ajánlott Azure-biztonsági eljárások 1 – Személyek: Csapatok oktatása a felhőbeli biztonság kialakítására](/azure/cloud-adoption-framework/security/security-top-10#1-people-educate-teams-about-the-cloud-security-journey)

- [Ajánlott Azure-biztonsági eljárások 2 – Személyek: Csapatok oktatása a felhőbeli biztonsági technológiákra](/azure/cloud-adoption-framework/security/security-top-10#2-people-educate-teams-on-cloud-security-technology)

- [Ajánlott Azure-biztonsági eljárások 3 – Folyamat: Elszámoltathatóság hozzárendelése felhőbeli biztonsági döntésekhez](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="gs-5-define-network-security-strategy"></a>GS-5: Hálózati biztonsági stratégia definiálása

**Útmutató**: Azure-hálózati biztonsági módszert a vállalat átfogó biztonsági hozzáférés-vezérlési stratégiájának részeként alakíthat ki.  

Ennek a stratégiának magában kell foglalnia a dokumentált útmutatót, és az alábbi elemek szabványait: 

-   Központosított hálózatkezelési és biztonsági felelősség

-   A vállalati szegmentálási stratégiának megfelelő virtuális hálózati szegmentálási modell

-   Szervizelési stratégia különböző fenyegetési és támadási helyzetekre

-   Internetes peremhálózati, bejövő és kimenő forgalomra vonatkozó stratégia

-   Hibrid felhős és helyszíni kapcsolódási stratégia

-   Naprakész hálózati biztonsági összetevők (pl. hálózati diagramok, hálózati referencia-architektúra)

További információkat az alábbi hivatkozásokon találhat:
- [Ajánlott Azure-biztonsági eljárások 11 – Architektúra. Egyetlen egységes biztonsági stratégia](/azure/cloud-adoption-framework/security/security-top-10#11-architecture-establish-a-single-unified-security-strategy)

- [Azure Security Benchmark – Hálózati biztonság](/azure/security/benchmarks/security-benchmark-v2-network-security)

- [A nagyvállalati hálózati biztonság áttekintése](../security/fundamentals/network-overview.md)

- [Nagyvállalati hálózati architektúrára vonatkozó stratégia](/azure/cloud-adoption-framework/ready/enterprise-scale/architecture)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="gs-6-define-identity-and-privileged-access-strategy"></a>GS-6: Identitáskezelési és emelt jogosultságú hozzáférési stratégia definiálása

**Útmutató**: Az identitások és az emelt jogosultságú hozzáférések kezelésének Azure-beli módszerét a vállalat átfogó biztonsági hozzáférés-vezérlési stratégiájának részeként alakíthat ki.  

Ennek a stratégiának magában kell foglalnia a dokumentált útmutatót, és az alábbi elemek szabványait: 

-   Központosított identitáskezelési és hitelesítési rendszer és annak kapcsolatai más belső és külső identitásrendszerekkel

-   Erős hitelesítési módszerek különböző használati helyzetek és feltételek esetén

-   Emelt jogosultságú felhasználók védelme

-   Rendellenes felhasználói tevékenységek monitorozása és kezelése  

-   Felhasználói identitások és hozzáférések felülvizsgálati és egyeztetési folyamata

További információkat az alábbi hivatkozásokon találhat:

- [Azure Security Benchmark – Identitáskezelés](/azure/security/benchmarks/security-benchmark-v2-identity-management)

- [Azure Security Benchmark – Emelt jogosultságú hozzáférés](/azure/security/benchmarks/security-benchmark-v2-privileged-access)

- [Ajánlott Azure-biztonsági eljárások 11 – Architektúra. Egyetlen egységes biztonsági stratégia](/azure/cloud-adoption-framework/security/security-top-10#11-architecture-establish-a-single-unified-security-strategy)

- [Az Azure identitáskezelésének biztonsági áttekintése](../security/fundamentals/identity-management-overview.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="gs-7-define-logging-and-threat-response-strategy"></a>GS-7: Naplózási és veszélyforrás-kezelési stratégia definiálása

**Útmutató**: Naplózási és veszélyforrás-kezelési stratégia kidolgozásával gyorsan észlelheti és elháríthatja a fenyegetéseket, ugyanakkor betartva a megfelelőségi követelményeket. Kezelje kiemelten a jó minőségű riasztások biztosítását az elemzők számára, és a könnyen használható felületeket, hogy az integráció és a manuális lépések helyett a veszélyforrásokra összpontosíthassanak. 

Ennek a stratégiának magában kell foglalnia a dokumentált útmutatót, és az alábbi elemek szabványait: 

-   A biztonsági üzemeltetési (SecOps) szervezet szerepköre és feladatai 

-   Jól definiált incidenskezelési folyamat az NIST-vel vagy más iparági keretrendszerrel összhangban 

-   Naplórögzítés és -megőrzés a veszélyforrások észlelése, az incidensek kezelése és a megfelelőségi igények támogatására

-   A fenyegetésekkel kapcsolatos információk központosított láthatósága és összevetése SIEM, natív Azure-képességek és más források használatával 

-   Kommunikációs és értesítési terv az ügyfelek, szállítók és külső érdekelt felek számára

-   Natív Azure-beli és külső platformok használata incidensek kezelésére, például naplózásra és veszélyforrások észlelésére, kivizsgálásra és a támadások megfékezésére és megszüntetésére

-   Folyamatok az olyan incidenskezelési és incidens utáni tevékenységekhez, mint a tanulságok levonása és a nyomok megőrzése

További információkat az alábbi hivatkozásokon találhat:

- [Azure Security Benchmark – Naplózás és fenyegetésészlelés](/azure/security/benchmarks/security-benchmark-v2-logging-threat-detection)

- [Azure Security Benchmark – Incidenskezelés](/azure/security/benchmarks/security-benchmark-v2-incident-response)

- [Ajánlott Azure-biztonsági eljárások 4 – Folyamat. Incidensválasz-folyamat frissítése a felhőhöz](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

- [Azure-adaptálási keretrendszer, útmutató naplózási és jelentéskészítési döntésekhez](/azure/cloud-adoption-framework/decision-guides/logging-and-reporting/)

- [Azure-beli nagyvállalati szintű skálázás, felügyelet és monitorozás](/azure/cloud-adoption-framework/ready/enterprise-scale/management-and-monitoring)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

## <a name="next-steps"></a>Következő lépések

- [Az Azure Security Benchmark v2 áttekintésének](/azure/security/benchmarks/overview) megtekintése
- További tudnivalók az [Azure biztonsági alapterveiről](/azure/security/benchmarks/security-baselines-overview)
