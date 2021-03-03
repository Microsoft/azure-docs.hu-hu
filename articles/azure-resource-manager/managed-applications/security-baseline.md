---
title: Azure Managed Applications Azure biztonsági alapterve
description: A Azure Managed Applications biztonsági alapterve az Azure biztonsági Teljesítménytesztben meghatározott biztonsági javaslatok megvalósítására szolgáló eljárási útmutatást és erőforrásokat biztosít.
author: msmbaldwin
ms.service: managed-applications
ms.topic: conceptual
ms.date: 12/01/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 42aea886e38622c773ac1ca0ea2533d8aa1c47bb
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2021
ms.locfileid: "101717026"
---
# <a name="azure-security-baseline-for-azure-managed-applications"></a>Azure Managed Applications Azure biztonsági alapterve

Ez a biztonsági alapkonfiguráció az [Azure biztonsági teljesítményteszt 2,0-es verziójáról](../../security/benchmarks/overview.md) Azure Managed Applicationsre vonatkozó útmutatást alkalmaz. Az Azure Security Benchmark ajánlásokat ad arra nézve, hogy hogyan tehetők biztonságossá a felhőalapú megoldások az Azure-ban. A tartalom az Azure biztonsági teljesítményteszt által meghatározott **biztonsági vezérlők** szerint van csoportosítva, valamint a Azure Managed Applications vonatkozó kapcsolódó útmutatás. A Azure Managed Applications nem alkalmazható **vezérlők** ki vannak zárva.

Ha szeretné megtekinteni, hogyan Azure Managed Applications teljes mértékben leképezni az Azure biztonsági Teljesítménytesztét, tekintse meg a [teljes Azure Managed Applications biztonsági alapterv-leképezési fájlt](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="network-security"></a>Hálózati biztonság

*További információ: [Azure Security Benchmark: Hálózati biztonság](../../security/benchmarks/security-controls-v2-network-security.md).*

### <a name="ns-6-simplify-network-security-rules"></a>NS-6: a hálózati biztonsági szabályok egyszerűsítése

**Útmutató**: az Azure Virtual Network szolgáltatás-címkék használatával meghatározhatja a hálózati biztonsági csoportokhoz vagy az Azure Managed Application-erőforrásokhoz konfigurált hálózati hozzáférés Azure Firewall-vezérlést. Biztonsági szabályok létrehozása során szolgáltatáscímkéket használhat bizonyos IP-címek helyett. A szolgáltatási címke nevének (például: AzureResourceManager) megadásával a szabály megfelelő forrás vagy cél mezőjében engedélyezheti vagy megtagadhatja a megfelelő szolgáltatás forgalmát. A Microsoft kezeli a szolgáltatási címke által felölelt címek előtagjait, és automatikusan frissíti a szolgáltatási címkét a címek változásával.

- [Azure Resource Manager szolgáltatásbeli címkék használata](../../virtual-network/service-tags-overview.md#available-service-tags)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

## <a name="identity-management"></a>Identitáskezelés

*További információ: [Azure Security Benchmark: Identitáskezelés](../../security/benchmarks/security-controls-v2-identity-management.md).*

### <a name="im-1-standardize-azure-active-directory-as-the-central-identity-and-authentication-system"></a>IM-1: Az Azure Active Directory, mint központi identitáskezelő és hitelesítési rendszer szabványosítása

**Útmutató**: a Azure Managed Applications az Azure Active Directoryt (Azure ad) használja alapértelmezett identitás-és hozzáférés-kezelési szolgáltatásként. Szabványosítás az Azure AD-ben a szervezet identitás-és hozzáférés-kezelésének szabályozása érdekében.

Az Azure az alábbi Azure beépített szerepköröket biztosítja a felügyelt alkalmazásokhoz való hozzáférés engedélyezéséhez az Azure AD és a OAuth használatával:

- Felügyelt alkalmazás közreműködői szerepköre: lehetővé teszi a felügyelt alkalmazások erőforrásainak létrehozását.

- Felügyelt alkalmazás-kezelő szerepkör: lehetővé teszi a felügyelt alkalmazás-erőforrásokra vonatkozó műveletek olvasását és végrehajtását

- Felügyelt alkalmazások olvasója: lehetővé teszi, hogy beolvassa az erőforrásokat egy felügyelt alkalmazásban, és kérjen JIT-hozzáférést.

További információkat az alábbi hivatkozásokon találhat:

- [Felügyelt alkalmazás közreműködői szerepköre](../../role-based-access-control/built-in-roles.md#managed-application-contributor-role)

- [Felügyelt alkalmazás operátori szerepköre](../../role-based-access-control/built-in-roles.md#managed-application-contributor-role)

- [Felügyelt alkalmazások olvasója](../../role-based-access-control/built-in-roles.md#managed-applications-reader)

- [Bérlői viszony az Azure Active Directoryban](../../active-directory/develop/single-and-multi-tenant-apps.md)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="im-2-manage-application-identities-securely-and-automatically"></a>IM-2: Alkalmazásidentitások biztonságos és automatikus kezelése

**Útmutató**: az Azure által felügyelt identitások használata a felügyelt alkalmazásokhoz való engedélyek megadásához. Azt javasoljuk, hogy az Azure Managed Identity szolgáltatást használja ahelyett, hogy hatékonyabb emberi fiókot hozzon létre az erőforrások eléréséhez vagy végrehajtásához, hogy korlátozza a további hitelesítő adatok kezelésének szükségességét. A Azure Managed Applications szolgáltatás olyan felügyelt identitást is hozzá tud rendelni, amely natív módon hitelesíti magát az Azure AD-hitelesítést támogató egyéb Azure-szolgáltatásokkal/erőforrásokkal. Ez akkor lehet hasznos, ha lehetővé szeretné tenni a Azure Managed Applications könnyű elérését, hogy Azure Key Vault a titkok beolvasása során. A felügyelt identitások használatakor az identitást az Azure platform felügyeli, és nem igényli semmilyen titok kiépítését és elforgatását.

Azure Managed Applications támogatja, hogy az alkalmazás két típusú identitást biztosítson:

- A rendszer által hozzárendelt identitás a konfigurációs erőforráshoz van kötve. A konfigurációs erőforrás törlése után törlődik. Egy konfigurációs erőforrásnak csak egy rendszerhez rendelt identitása lehet.

- A felhasználó által hozzárendelt identitás egy önálló Azure-erőforrás, amelyet a konfigurációs erőforráshoz rendelhet hozzá. A konfigurációs erőforrásokhoz több felhasználó által hozzárendelt identitás is tartozhat.

Ha a felügyelt identitásokat nem lehet kihasználni, hozzon létre egy egyszerű szolgáltatásnevet az Azure Managed Application erőforrás szintjén. Konfigurálja ezeket a egyszerű szolgáltatásokat a tanúsítvány hitelesítő adataival, és csak az ügyfél titkos kulcsaira térjen vissza. Mindkét esetben Azure Key Vault használható az Azure felügyelt identitásokkal együtt, így a futásidejű környezet (például egy Azure-függvény) beolvashatja a hitelesítő adatokat a kulcstartóból.

- [Felügyelt identitások használata Azure Managed Applicationshoz](publish-managed-identity.md)

- [Azure-beli felügyelt identitások](../../active-directory/managed-identities-azure-resources/overview.md)

- [Azure-szolgáltatásnév](/powershell/azure/create-azure-service-principal-azureps) 

- [Rendszerbiztonsági tag regisztrációjának Azure Key Vault használata](../../key-vault/general/authentication.md#app-identity-and-security-principals)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="im-3-use-azure-ad-single-sign-on-sso-for-application-access"></a>IM-3: Az Azure AD-beli egyszeri bejelentkezés használata alkalmazások eléréséhez

**Útmutató**: a Azure Managed Applications Azure Active Directory használatával biztosítanak identitás-és hozzáférés-kezelést az Azure-erőforrásokhoz, a Felhőbeli alkalmazásokhoz és a helyszíni alkalmazásokhoz. Ez vonatkozik az olyan nagyvállalati identitásokra, mint az alkalmazottak, valamint az olyan külső identitásokra is, mint a partnerek, szállítók és ellátók. Ez lehetővé teszi a vállalati adatok és erőforrások egyszeri bejelentkezéssel (SSO) megvalósított kezelését és védelmét a helyszínen és a felhőben. Az összes felhasználót, alkalmazást és eszközt beléptetheti az Azure AD-be a zökkenőmentes, biztonságos hozzáférés, valamint a jobb átláthatóság és vezérlés érdekében.

- [Az Azure AD-vel megvalósított alkalmazás-SSO ismertetése](../../active-directory/manage-apps/what-is-single-sign-on.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="im-4-use-strong-authentication-controls-for-all-azure-active-directory-based-access"></a>IM-4: Erős hitelesítési vezérlők használata minden Azure Active Directory-alapú hozzáféréshez

**Útmutató**: a Azure Managed Applications olyan Azure Active Directory használ, amely támogatja az erős hitelesítési vezérlőket többtényezős hitelesítéssel és erős, jelszóval nem rendelkező módszerekkel.
- Többtényezős hitelesítés – az Azure AD többtényezős hitelesítésének engedélyezése és a Azure Security Center identitás-és hozzáférés-kezelési javaslatok követése a többtényezős hitelesítés beállításában ajánlott eljárásokért. a többtényezős hitelesítés az összes, a felhasználók vagy a felhasználónkénti szinten a bejelentkezési feltételek és a kockázati tényezők alapján kényszeríthető ki.
- Jelszó nélküli hitelesítés – Három jelszó nélküli hitelesítési lehetőség érhető el: a Windows Hello for Business, a Microsoft Authenticator alkalmazás, és az olyan helyszíni hitelesítési módszerek, mint az intelligens kártyák.

A rendszergazda és a Kiemelt jogosultságú felhasználók számára győződjön meg arról, hogy a legmagasabb szintű erős hitelesítés van használatban.

- [Többtényezős hitelesítés engedélyezése az Azure-ban](../../active-directory/authentication/howto-mfa-getstarted.md) 

- [Az Azure Active Directory jelszó nélküli hitelesítési lehetőségeinek bemutatása](../../active-directory/authentication/concept-authentication-passwordless.md)

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="im-5-monitor-and-alert-on-account-anomalies"></a>IM-5: Fiókok rendellenességeinek monitorozása, és riasztás azok alapján

**Útmutató**: a Azure Managed Applications integrálva van a Azure Active Directory, amely a következő adatforrásokat biztosítja:
- Bejelentkezések – a bejelentkezési jelentés a felügyelt alkalmazások használatával és a felhasználók bejelentkezési tevékenységeivel kapcsolatos információkat biztosít.
- Auditnaplók – az Azure AD-n belül különböző szolgáltatások által végrehajtott összes módosításra vonatkozó nyomkövetési naplókat biztosít. A naplók rögzítik például az erőforrások módosításait az Azure AD-n belül, például a felhasználók, alkalmazások, csoportok, szerepkörök és szabályzatok hozzáadását vagy eltávolítását.
- Kockázatos bejelentkezések – A kockázatos bejelentkezés egy olyan bejelentkezési kísérletet jelöl, amelyet elképzelhető, hogy olyan személy hajtott végre, aki nem a felhasználói fiók jogos tulajdonosa.
- Kockázatosként megjelölt felhasználók – A kockázatos felhasználó egy olyan felhasználói fiókot jelöl, amelynek elképzelhető, hogy sérült a biztonsága.

Ezek az adatforrások a Azure Monitor, az Azure Sentinel vagy a harmadik féltől származó SIEM rendszerekkel integrálhatók.

Az Azure Security Center riasztásokat is küldhet bizonyos gyanús tevékenységek észlelésekor. Ezek közé tartozik például a sikertelen hitelesítési kísérletek kiugróan magas száma vagy az előfizetésben talált elavult fiókok.

Az Azure Advanced Threat Protection (ATP) olyan biztonsági megoldás, amely Active Directory-jelek használatával tudja azonosítani, észlelni és kivizsgálni a súlyos fenyegetéseket, a sérült identitásokat és a rosszindulatú belső műveleteket.

- [Naplózott tevékenységekre vonatkozó jelentések az Azure Active Directoryban](../../active-directory/reports-monitoring/concept-audit-logs.md)

- [Az Azure AD kockázatos bejelentkezéseinek megtekintése](../../active-directory/identity-protection/overview-identity-protection.md)

- [A felhasználók identitási és hozzáférési tevékenységeinek monitorozása az Azure Security Centerben](../../security-center/security-center-identity-access.md)

- [Azure-tevékenységnaplók integrálása az Azure Monitorba](../../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="im-6-restrict-azure-resource-access-based-on-conditions"></a>IM-6: Azure-erőforrásokhoz való hozzáférés korlátozása feltételek alapján

**Útmutató**: Azure Managed Applications támogatja az Azure ad feltételes hozzáférést egy részletesebb hozzáférés-vezérléshez a felhasználó által definiált feltételek alapján, például bizonyos IP-címtartományok felhasználói bejelentkezések esetén többtényezős hitelesítést kell használni a bejelentkezéshez. A részletes hitelesítési munkamenet-kezelési szabályzat többféle helyzetben is felhasználható.

- [Az Azure-beli feltételes hozzáférés áttekintése](../../active-directory/conditional-access/overview.md) 

- [Gyakori feltételes hozzáférési szabályzatok](../../active-directory/conditional-access/concept-conditional-access-policy-common.md) 

- [Hitelesítési munkamenet-kezelés konfigurálása feltételes hozzáféréssel](../../active-directory/conditional-access/howto-conditional-access-session-lifetime.md)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

## <a name="privileged-access"></a>Emelt szintű hozzáférés

*További információ: [Azure Security Benchmark: Emelt jogosultságú hozzáférés](../../security/benchmarks/security-controls-v2-privileged-access.md).*

### <a name="pa-1-protect-and-limit-highly-privileged-users"></a>PA-1: Emelt jogosultságú felhasználók védelme és korlátozása

Útmutató: a Azure Managed Applications Azure Active Directory (Azure AD) identitás-és hozzáférés- **vezérlést** használ. A legfontosabb beépített szerepkörök az Azure AD globális rendszergazda és a Kiemelt szerepkörű rendszergazda, mivel a két szerepkörhöz hozzárendelt felhasználók delegálhatja a rendszergazdai szerepköröket:
- Globális rendszergazda: az ehhez a szerepkörhöz tartozó felhasználók hozzáférhetnek az Azure AD összes felügyeleti funkciójához, valamint az Azure AD-identitásokat használó szolgáltatásokhoz.
- Kiemelt szerepkörű rendszergazda: az ezzel a szerepkörrel rendelkező felhasználók kezelhetik a szerepkör-hozzárendeléseket az Azure AD-ben, valamint a Azure AD Privileged Identity Managementon (PIM) belül is. Emellett ez a szerepkör lehetővé teszi a PIM és a felügyeleti egységek valamennyi aspektusának kezelését.

Megjegyzés: Előfordulhat, hogy más kritikus szerepkörökre is szükség van, amelyeket akkor kell alkalmaznia, ha egyéni szerepköröket használ bizonyos jogosultsági szintű engedélyekkel társítva. Emellett előfordulhat, hogy hasonló vezérlőket szeretne alkalmazni a kritikus fontosságú üzleti eszközök rendszergazdai fiókjára.

Az Azure AD Privileged Identity Management (PIM) használatával engedélyezheti az Azure-erőforrások és az Azure AD igény szerinti (just-in-time, JIT) jogosultságú hozzáférését. A JIT ideiglenes engedélyeket biztosít az érintett feladatok végrehajtásához, csak annyi időre, ameddig a felhasználóknak erre szükségük van. A PIM biztonsági riasztásokat is képes kiadni, amikor gyanús vagy nem biztonságos tevékenységeket észlel az Azure AD-szervezetben.

- [Rendszergazdai szerepköri engedélyek az Azure AD-ben](../../active-directory/roles/permissions-reference.md)

- [Az Azure Privileged Identity Management biztonsági riasztásainak használata](../../active-directory/privileged-identity-management/pim-how-to-configure-security-alerts.md)

- [Emelt szintű hozzáférés biztosítása Azure AD hibrid- és felhőkörnyezetekhez](../../active-directory/roles/security-planning.md)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="pa-2-restrict-administrative-access-to-business-critical-systems"></a>PA-2: Az üzletileg kritikus rendszerek rendszergazdai elérésének korlátozása

**Útmutató**: a Azure Managed Applications az Azure RBAC segítségével elkülöníti az üzleti szempontból kritikus rendszerek hozzáférését azáltal, hogy korlátozza, hogy mely fiókok kapnak privilegizált hozzáférést az előfizetésekhez és a felügyeleti csoportokhoz.

Győződjön meg arról, hogy az üzleti szempontból kritikus fontosságú rendszerekre telepített ügynökökkel való rendszergazdai hozzáféréssel rendelkező felügyeleti, identitási és biztonsági rendszerekhez is korlátozza a hozzáférést, például Active Directory-tartomány vezérlőket (DCs), biztonsági eszközöket és rendszerfelügyeleti eszközöket. Azok a támadók, akik veszélyeztetik ezeket a felügyeleti és biztonsági rendszereket, azonnal weaponize azokat az üzleti szempontból kritikus fontosságú eszközök biztonsága érdekében.

A hozzáférés-vezérlés összes típusát a vállalati szegmentálási stratégiához kell igazítani, hogy biztosítsa a konzisztens hozzáférés-vezérlést.

- [Azure-összetevők és-hivatkozási modell](/security/compass/microsoft-security-compass-introduction#azure-components-and-reference-model-2151)

- [Felügyeleti csoport elérése](../../governance/management-groups/overview.md#management-group-access)

- [Azure-előfizetés rendszergazdái](../../cost-management-billing/manage/add-change-subscription-administrator.md)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="pa-3-review-and-reconcile-user-access-regularly"></a>PA-3: Felhasználói hozzáférés rendszerességének áttekintése és egyeztetése

**Útmutató**: rendszeresen tekintse át a felhasználói fiókokat és a hozzáférés-hozzárendelést, hogy a fiókok és azok hozzáférési szintje érvényes legyen.

A Azure Managed Applications Azure Active Directory (HRE) fiókokat használ az erőforrásainak kezelésére, a fiókok és a hozzáférésük érvényességének biztosítása érdekében rendszeresen tekintse át a felhasználói fiókokat és a hozzáférés-hozzárendelést. Az Azure AD hozzáférési felülvizsgálatok segítségével áttekintheti a csoporttagság, a vállalati alkalmazásokhoz való hozzáférés és a szerepkör-hozzárendelések áttekintését. Az Azure AD jelentéskészítési szolgáltatása lehetővé teszi, hogy a naplók segítséget nyújtsanak az elavult fiókok felderítéséhez. A felülvizsgálati folyamat megkönnyítése érdekében a Azure AD Privileged Identity Management használatával hozzáférési felülvizsgálati jelentési munkafolyamatot is létrehozhat.

Emellett az Azure Privileged Identity Management is beállítható úgy, hogy riasztást hozzon létre túl sok rendszergazdai fiók létrehozásakor, valamint a elavult vagy helytelenül konfigurált rendszergazdai fiókok azonosításához.

Megjegyzés: egyes Azure-szolgáltatások támogatják a helyi felhasználókat és szerepköröket, amelyek nem az Azure AD-n keresztül kezelhetők. Ezeket a felhasználókat külön kell kezelnie.

- [Azure-beli erőforrás-szerepkörök hozzáférési felülvizsgálatának létrehozása Privileged Identity Management (PIM)](../../active-directory/privileged-identity-management/pim-resource-roles-start-access-review.md) 

- [Az Azure AD identitás- és hozzáférési felülvizsgálatainak használata](../../active-directory/governance/access-reviews-overview.md)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="pa-4-set-up-emergency-access-in-azure-ad"></a>PA-4: Vészhelyzeti hozzáférés beállítása az Azure AD-ben

**Útmutató**: a Azure Managed Applications a Azure Active Directory használja az erőforrások kezeléséhez. Ha nem szeretné megakadályozni, hogy véletlenül kizárja az Azure AD-szervezetét, állítson be egy vészhelyzeti hozzáférési fiókot a hozzáféréshez, ha a normál rendszergazdai fiókok nem használhatók. A vészhelyzeti hozzáférési fiókok általában magas szintű jogosultságokkal rendelkeznek, és nem ajánlott azokat egyes személyekhez társítani. A vészhelyzeti hozzáférési fiókok csak az olyan vészhelyzeti esetekre valók, amikor a normál rendszergazdai fiókok nem használhatók.

Érdemes biztosítani, hogy a vészhelyzeti hozzáférési fiókok hitelesítő adatai (például jelszó, tanúsítvány vagy intelligens kártya) biztonságos helyen vannak tárolva, amelyet csak azok ismernek, akik jogosultak azokat használni, kizárólag vészhelyzet esetén.

- [Vészhelyzeti hozzáférési fiókok kezelése az Azure AD-ben](../../active-directory/roles/security-emergency-access.md)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="pa-5-automate-entitlement-management"></a>PA-5: a jogosultságok kezelésének automatizálása 

**Útmutató**: a Azure Managed Applications integrálva van az Azure Active Directoryekkel az erőforrások kezeléséhez. Az Azure AD-jogosultságok kezelési funkciói segítségével automatizálhatja a hozzáférési kérelmek munkafolyamatait, beleértve a hozzáférési hozzárendeléseket, az értékeléseket és a lejáratokat. A kettős vagy többfázisú jóváhagyás is támogatott.

- [Az Azure AD hozzáférési felülvizsgálatai](../../active-directory/governance/access-reviews-overview.md)

- [Mi az Azure AD-jogosultságok kezelése?](../../active-directory/governance/entitlement-management-overview.md)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="pa-6-use-privileged-access-workstations"></a>PA-6: Emelt szintű hozzáférésű munkaállomások használata

**Útmutató**: A biztonságos, elkülönített munkaállomások kritikus fontosságúak az olyan bizalmas szerepkörök biztonsága szempontjából, mint a rendszergazdák, a fejlesztők vagy a kritikus fontosságú szolgáltatások üzemeltetői. A felügyelt alkalmazásokkal kapcsolatos adminisztratív feladatokhoz használjon magasan biztonságos felhasználói munkaállomásokat és/vagy az Azure-t. Az Azure Active Directory, a Microsoft Defender Advanced Threat Protection (ATP) és/vagy a Microsoft Intune használatával biztonságos és felügyelt felhasználói munkaállomásokat helyezhet üzembe a rendszergazdai tevékenységekhez. A védett munkaállomások központi kezelésével kikényszeríthető a biztonságos konfiguráció, beleértve az erős hitelesítést, a szoftveres és hardveres alapkonfigurációikat, valamint a korlátozott logikai és hálózati hozzáférést.

- [Az emelt szintű hozzáférésű munkaállomások ismertetése](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

- [Emelt szintű hozzáférésű munkaállomás üzembe helyezése](/security/compass/privileged-access-deployment)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="pa-7-follow-just-enough-administration-least-privilege-principle"></a>PA-7: A Just Enough Administration (legkisebb jogosultsági alapelv) követése 

**Útmutató**: a Azure Managed Applications integrálva van az Azure szerepköralapú hozzáférés-vezérléssel (RBAC) az erőforrások kezeléséhez. Az Azure RBAC lehetővé teszi, hogy szerepkörök hozzárendelésével felügyelje az Azure-erőforrások hozzáférését. Ezeket a szerepköröket hozzárendelheti a felhasználókhoz, a csoportok egyszerű szolgáltatásaihoz és a felügyelt identitásokhoz. Bizonyos erőforrásokhoz előre meghatározott beépített szerepkörök tartoznak, és ezek a szerepkörök olyan eszközökkel leltározhatók vagy kérdezhetők le, mint az Azure CLI, az Azure PowerShell vagy az Azure Portal. Az erőforrásokhoz az Azure RBAC-n keresztül hozzárendelt jogosultságokat mindig arra kell korlátozni, amit a szerepkörök megkövetelnek. Ez a megközelítés megfelel az Azure AD Privileged Identity Management (PIM) igény szerinti (JIT) módszerének, és a jogosultságok rendszeres felülvizsgálatával jár.

Ha lehetséges, a beépített szerepkörök használatával lefoglalhatja az engedélyt, és szükség esetén csak egyéni szerepkört hozhat létre.

Az Azure az alábbi Azure beépített szerepköröket biztosítja a felügyelt alkalmazásokhoz való hozzáférés engedélyezéséhez az Azure AD és a OAuth használatával:

- Felügyelt alkalmazás közreműködői szerepköre: lehetővé teszi a felügyelt alkalmazások erőforrásainak létrehozását.

- Felügyelt alkalmazás-kezelő szerepkör: lehetővé teszi a felügyelt alkalmazás-erőforrásokra vonatkozó műveletek olvasását és végrehajtását

- Felügyelt alkalmazások olvasója: lehetővé teszi, hogy beolvassa az erőforrásokat egy felügyelt alkalmazásban, és kérjen JIT-hozzáférést.

További információkat az alábbi hivatkozásokon találhat:

- [Felügyelt alkalmazás közreműködői szerepköre](../../role-based-access-control/built-in-roles.md#managed-application-contributor-role)

- [Felügyelt alkalmazás operátori szerepköre](../../role-based-access-control/built-in-roles.md#managed-application-contributor-role)

- [Felügyelt alkalmazások olvasója](../../role-based-access-control/built-in-roles.md#managed-applications-reader)

- [Mi az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC)?](../../role-based-access-control/overview.md)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="pa-8-choose-approval-process-for-microsoft-support"></a>PA-8: a Microsoft támogatási szolgálatának jóváhagyási folyamatának kiválasztása  

**Útmutató**: szervezeti jóváhagyási folyamat implementálása olyan támogatási forgatókönyvek esetén, ahol a Microsoft számára szükség lehet az Azure-beli felügyelt alkalmazás adataihoz való hozzáférésre. Ügyfélszéf jelenleg nem érhető el felügyelt alkalmazási forgatókönyvekhez.

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

## <a name="data-protection"></a>Adatvédelem

*További információ: [Azure Security Benchmark: Adatvédelem](../../security/benchmarks/security-controls-v2-data-protection.md).*

### <a name="dp-2-protect-sensitive-data"></a>DP-2: A bizalmas adatok védelme

**Útmutató**: Ha a titkosítást a saját kulcsaival szeretné használni, saját Storage-fiókot is használhat a felügyelt alkalmazás konfigurációs fájljainak tárolásához.

- [Felügyelt konfigurációs fájlok adatvédelme saját tároló használatával](./publish-service-catalog-app.md?tabs=azure-powershell#bring-your-own-storage-for-the-managed-application-definition)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="dp-5-encrypt-sensitive-data-at-rest"></a>DP-5: Inaktív bizalmas adatok titkosítása

**Útmutató**: a felügyelt alkalmazás-definíciók, amelyek meghatározzák az alkalmazást és annak erőforrásait a saját Storage-fiókjaiban tárolhatók, amelyeket az ügyfél által felügyelt titkosítási kulcsokkal lehet konfigurálni.

Olyan esetekben, ahol nem kívánja saját tárterületet használni a felügyelt alkalmazás-definíciók számára, az Azure alapértelmezés szerint biztosítja a REST-titkosítást.

- [Saját tárterület használata a felügyelt alkalmazások definíciói számára](./publish-service-catalog-app.md?tabs=azure-powershell#bring-your-own-storage-for-the-managed-application-definition)

- [Inaktív adatok Azure-ban való titkosításának ismertetése](../../security/fundamentals/encryption-atrest.md#encryption-at-rest-in-microsoft-cloud-services) 

- [Az ügyfél által felügyelt titkosítási kulcsok konfigurálása](../../storage/common/customer-managed-keys-configure-key-vault.md)

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Megosztott

## <a name="asset-management"></a>Asset Management (Eszközkezelés)

*További információ: [Azure Security Benchmark: Összetevők kezelése](../../security/benchmarks/security-controls-v2-asset-management.md).*

### <a name="am-1-ensure-security-team-has-visibility-into-risks-for-assets"></a>AM-1: Gondoskodás arról, hogy a biztonsági csapat átlássa az adategységek kockázatait

**Útmutató**: Győződjön meg arról, hogy a biztonsági csapatok biztonsági olvasó engedélyekkel rendelkeznek az Azure-bérlőben és -előfizetésekben, hogy monitorozni tudják a biztonsági kockázatokat az Azure Security Centerrel. 

A biztonsági kockázatok monitorozása a biztonsági csapat felelősségi köreinek struktúrájától függően egy központi biztonsági csapat vagy egy helyi csapat felelőssége lehet. A biztonsági megállapításokat és kockázatokat azonban mindig központilag kell összesíteni egy szervezeten belül. 

A biztonsági olvasó engedélyek széles körben alkalmazhatók egy teljes bérlőre (gyökérszintű felügyeleti csoport), vagy a hatókör alkalmazható adott felügyeleti csoportokra vagy előfizetésekre. 

Megjegyzés: A számítási feladatok és a szolgáltatások átláthatóvá tételéhez további engedélyek lehetnek szükségesek. 

- [A biztonsági olvasó szerepkör áttekintése](../../role-based-access-control/built-in-roles.md#security-reader)

- [Az Azure-beli felügyeleti csoportok áttekintése](../../governance/management-groups/overview.md)

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="am-2-ensure-security-team-has-access-to-asset-inventory-and-metadata"></a>AM-2: Az összetevőleltár és a metaadatok biztonsági csapat általi elérhetőségének biztosítása

**Útmutató**: címkék alkalmazása az Azure-erőforrásokra,-csoportokra és-előfizetésekre, amelyek logikailag rendszerezik őket egy besorolásban. Minden címke egy nevet és egy érték párokat tartalmaz. Alkalmazhatja például a „Környezet” nevet és az „Éles” értéket az összes éles üzemben használt erőforrásra.

A felügyelt alkalmazás létrehozási idején megadott címkéket is alkalmazza a rendszer a felügyelt erőforráscsoporthoz. Az alkalmazás közzétevője az üzembe helyezés után megadhatja a felügyelt erőforrások további címkézését.

- [Címkék erőforrás által felügyelt lekérdezéséhez felhasználói felületi elem](microsoft-common-tagsbyresource.md)

- [Lekérdezések létrehozása az Azure Resource Graph Explorerrel](../../governance/resource-graph/first-query-portal.md) 

- [További információ az eszközök címkézéséről: erőforrás-elnevezési és címkézési döntési útmutató](/azure/cloud-adoption-framework/decision-guides/resource-tagging/?toc=%2fazure%2fazure-resource-manager%2fmanagement%2ftoc.json)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="am-3-use-only-approved-azure-services"></a>AM-3: Csak jóváhagyott Azure-szolgáltatások használata

**Útmutató**: a Azure Managed Applications a Azure Policy használatával támogatja Azure Resource Manager-alapú telepítéseket és a konfiguráció kényszerítését. Az Azure Policy használatával ellenőrizheti és korlátozhatja a felhasználók által a környezetben kiépíthető szolgáltatások körét. Az Azure Resource Graph használatával lekérdezheti és felderítheti az előfizetésükön belüli erőforrásokat. Az Azure Monitort is használhatja olyan szabályok létrehozásához, amelyek riasztást aktiválnak nem jóváhagyott szolgáltatás észlelésekor.

- [Az Azure Policy konfigurálása és kezelése](../../governance/policy/tutorials/create-and-manage.md)

- [Adott erőforrástípus megtagadása a következővel Azure Policy](../../governance/policy/samples/built-in-policies.md#general)

- [Lekérdezések létrehozása az Azure Resource Graph Explorerrel](../../governance/resource-graph/first-query-portal.md)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="am-4-ensure-security-of-asset-lifecycle-management"></a>AM-4: Az objektuméletciklus-kezelés biztonságának garantálása

**Útmutató**: a felügyelt alkalmazás erőforrásai és a csatlakoztatott felügyelt erőforráscsoport törölhető a felügyelt alkalmazás erőforrásának törlésével. A felügyelt alkalmazási erőforrások törlése után a felügyelt erőforráscsoport és annak tartalma is törlődik. A további életciklus-képességeket az alkalmazás közzétevője adja meg, ahol a felhasználók további jogosultságokat biztosíthatnak az alapul szolgáló felügyelt erőforrások életciklusán keresztül az engedélyezett műveletekkel. Tekintse meg a felügyelt alkalmazás közzétevőjét, hogy a fogyasztó milyen erőforrásokat kezel.

- [Felügyelt alkalmazás erőforrásainak karbantartása](./tutorial-create-managed-app-with-custom-provider.md?tabs=azurecli-interactive#clean-up-resources)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="am-5-limit-users-ability-to-interact-with-azure-resource-manager"></a>AM-5: korlátozza a felhasználók képességét a Azure Resource Manager való interakcióra

**Útmutató**: az Azure feltételes hozzáférés használatával korlátozhatja, hogy a felhasználók képesek legyenek az Azure Resources Managerrel való interakcióra az "Microsoft Azure felügyelet" alkalmazás "hozzáférés letiltása" beállításával.

- [Feltételes hozzáférés konfigurálása az Azure Resources Manager elérésének blokkolásához](../../role-based-access-control/conditional-access-azure-management.md)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

## <a name="logging-and-threat-detection"></a>Naplózás és fenyegetésészlelés

*További információ: [Azure Security Benchmark: Naplózás és fenyegetésészlelés](../../security/benchmarks/security-controls-v2-logging-threat-detection.md).*

### <a name="lt-2-enable-threat-detection-for-azure-identity-and-access-management"></a>LT-2: Fenyegetések észlelésének engedélyezése az Azure-beli identitás- és hozzáférés-kezeléshez

**Útmutató**: az Azure ad a következő felhasználói naplókat jeleníti meg, amelyek megtekinthetők az Azure ad Reporting szolgáltatásban, illetve a Azure monitor, az Azure Sentinel vagy más Siem/monitoring eszközökkel is megtekinthetők a kifinomultabb monitorozási és elemzési használati esetek: bejelentkezések – a bejelentkezési jelentés a felügyelt alkalmazások és a felhasználói bejelentkezési tevékenységek használatáról nyújt információt.
Auditnaplók – az Azure AD-n belül különböző szolgáltatások által végrehajtott összes módosításra vonatkozó nyomkövetési naplókat biztosít. A naplók rögzítik például az erőforrások módosításait az Azure AD-n belül, például a felhasználók, alkalmazások, csoportok, szerepkörök és szabályzatok hozzáadását vagy eltávolítását.
Kockázatos bejelentkezések – A kockázatos bejelentkezés egy olyan bejelentkezési kísérletet jelöl, amelyet elképzelhető, hogy olyan személy hajtott végre, aki nem a felhasználói fiók jogos tulajdonosa.
Kockázatosként megjelölt felhasználók – A kockázatos felhasználó egy olyan felhasználói fiókot jelöl, amelynek elképzelhető, hogy sérült a biztonsága.

Az Azure Security Center riasztásokat is küldhet bizonyos gyanús tevékenységek észlelésekor. Ezek közé tartozik például a sikertelen hitelesítési kísérletek kiugróan magas száma vagy az előfizetésben talált elavult fiókok. A biztonsági állapot alapszintű monitorozása mellett Azure Security Center fenyegetésvédelmi modulja részletesebb biztonsági riasztásokat is gyűjthet az egyes Azure-beli számítási erőforrásokból (virtuális gépek, tárolók, App Service), adatforrásokból (SQL DB és Storage) és az Azure-szolgáltatásrétegekből. Ezzel a képességgel megtekintheti az egyes erőforrásokon belüli fiókok rendellenességeit.

- [Naplózott tevékenységekre vonatkozó jelentések az Azure Active Directoryban](../../active-directory/reports-monitoring/concept-audit-logs.md)

- [Az Azure Identity Protection engedélyezése](../../active-directory/identity-protection/overview-identity-protection.md)

- [Fenyegetésvédelem az Azure Security Centerben](../../security-center/azure-defender.md)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="lt-4-enable-logging-for-azure-resources"></a>LT-4: Naplózás engedélyezése Azure-erőforrásokhoz

**Útmutató**: az automatikusan elérhető tevékenység-naplók a felügyelt alkalmazás-erőforrások összes írási műveletét (Put, post, DELETE) tartalmazzák, kivéve az olvasási MŰVELETEKET (Get). A Tevékenységnaplók segítségével hibát kereshet a hibaelhárítás során, vagy megfigyelheti, hogy a szervezet felhasználója hogyan módosított egy erőforrást.

- [Platform-naplók és-metrikák összegyűjtése Azure Monitor](../../azure-monitor/essentials/diagnostic-settings.md) 

- [A naplózás és a különböző naplózási típusok megismerése az Azure-ban](../../azure-monitor/essentials/platform-logs-overview.md)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="lt-5-centralize-security-log-management-and-analysis"></a>LT-5: Biztonsági naplók kezelésének és elemzésének központosítása

**Útmutató**: a naplózás, a tárolás és az elemzés központosított kezelése a korreláció engedélyezéséhez. Minden naplózási forrás esetében ellenőrizze, hogy rendelkezik-e az adatok tulajdonosával, a hozzáférési útmutatással, a tárolási hellyel, az adatok feldolgozásához és eléréséhez, valamint az adatmegőrzési követelményekhez használt eszközökhöz.
Győződjön meg arról, hogy integrálja az Azure-tevékenységek naplóit a központi naplózásba. Naplók betöltése Azure Monitor használatával a végponti eszközök, a hálózati erőforrások és más biztonsági rendszerek által generált biztonsági adatokat összesítve. Azure Monitor a Log Analytics-munkaterületek használatával kérdezheti le és végezheti el az elemzéseket, és használhatja az Azure Storage-fiókokat hosszú távú és archiválási tároláshoz.

Emellett az Azure Sentinel vagy egy harmadik féltől származó SIEM-nek is engedélyezi az adatok bevezetését.

Számos szervezet úgy dönt, hogy a gyakran használt és az Azure-tárolót használja a ritkábban használt adatokhoz az Azure Sentinel használatával.

- [Platform-naplók és-metrikák összegyűjtése Azure Monitor](../../azure-monitor/essentials/diagnostic-settings.md)

- [Az Azure Sentinel előkészítése](../../sentinel/quickstart-onboard.md)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="lt-6-configure-log-storage-retention"></a>LT-6: Tárolt naplók megőrzésének konfigurálása

**Útmutató**: gondoskodjon arról, hogy a felügyelt alkalmazás erőforrásai által létrehozott naplók tárolására használt összes Storage-fiók vagy log Analytics-munkaterület a szervezet megfelelőségi szabályainak megfelelően állítsa be a napló megőrzési időtartamát.
Azure Monitor az Log Analytics munkaterület megőrzési időszakát a szervezet megfelelőségi előírásai szerint állíthatja be. Az Azure Storage, a Data Lake vagy a Log Analytics munkaterület-fiókok használata hosszú távú és archiválási tároláshoz.

- [Log Analytics munkaterület megőrzési időtartamának konfigurálása](../../azure-monitor/logs/manage-cost-storage.md)

- [Erőforrás-naplók tárolása Azure Storage-fiókban](../../azure-monitor/essentials/resource-logs.md#send-to-azure-storage)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

## <a name="incident-response"></a>Incidensmegoldás

*További információ: [Azure Security Benchmark: Incidensek kezelése](../../security/benchmarks/security-controls-v2-incident-response.md).*

### <a name="ir-1-preparation--update-incident-response-process-for-azure"></a>IR-1: Előkészítés – incidensválasz-folyamat frissítése az Azure-hoz

**Útmutató**: Gondoskodjon róla, hogy a vállalata rendelkezik a biztonsági incidensek kezelésére szolgáló folyamattal, ezeket az folyamatokat az Azure-hoz igazította, és rendszeresen gyakorlja a készenlét érdekében.

- [Biztonság implementálása a teljes nagyvállalati környezetben](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

- [Incidensválasz referencia-útmutató](/microsoft-365/downloads/IR-Reference-Guide.pdf)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="ir-2-preparation--setup-incident-notification"></a>IR-2: Előkészítés – incidensről szóló értesítés beállítása

**Útmutató**: Állítson be a biztonsági incidensekre vonatkozó kapcsolattartási adatokat az Azure Security Centerben. A Microsoft ezen kapcsolattartási adatok használatával keresi meg Önt, ha a Microsoft Security Response Center (MSCR) felfedezi, hogy az adataihoz törvénytelen vagy jogosulatlan módon fértek hozzá. Lehetősége van az incidensekkel kapcsolatos riasztások és értesítések testreszabására a különböző Azure-szolgáltatásokban az incidensválasz-igények alapján. 

- [Az Azure Security Center biztonsági kapcsolattartójának beállítása](../../security-center/security-center-provide-security-contact-details.md)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="ir-3-detection-and-analysis--create-incidents-based-on-high-quality-alerts"></a>IR-3: Észlelés és elemzés – incidensek létrehozása jó minőségű riasztások alapján

**Útmutató**: Gondoskodjon egy, a magas minőségű riasztások létrehozására és a riasztások minőségének mérésére szolgáló folyamatról. Ez lehetővé teszi a korábbi incidensek tanulságainak levonását és a riasztások rangsorolását az elemzők számára, hogy ne veszítsenek időt a téves riasztások miatt. 

A magas minőségű riasztások készítése épülhet a korábbi incidensek tapasztalataira, az ellenőrzött közösségi forrásokra, valamint azokra az eszközökre, amelyek a riasztások generálását és tisztítását a különböző jelforrások egyesítésével és egyeztetésével végzik. 

Azure Security Center kiváló minőségű riasztásokat biztosít számos Azure-eszközön. Az ASC adatösszekötővel streamelheti a riasztásokat az Azure Sentinelbe. Az Azure Sentinellel speciális riasztási szabályokat hozhat létre, amelyekkel automatikusan hozhat létre incidenseket a vizsgálatokhoz. 

Az exportálási funkcióval exportálhatja az Azure Security Center riasztásait és javaslatait, amelyek segítenek az Azure-erőforrások kockázatainak azonosításában. A riasztásokat és javaslatokat manuálisan vagy folyamatosan is exportálhatja.

- [Az exportálás konfigurálása](../../security-center/continuous-export.md)

- [Riasztások streamelése az Azure Sentinelbe](../../sentinel/connect-azure-security-center.md)

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="ir-4-detection-and-analysis--investigate-an-incident"></a>IR-4: Észlelés és elemzés – incidens vizsgálata

**Útmutató**: Győződjön meg arról, hogy az elemzők különböző adatforrásokat kérdezhetnek le és használhatnak a lehetséges incidensek vizsgálata során, és átfogó képet adhatnak a történtekről. A vakfoltok elkerülése érdekében különböző naplókat kell gyűjteni a potenciális támadók tevékenységének követéséhez a támadási útvonalon.  A megállapítások és eredmények rögzítéséről is érdemes gondoskodnia, hogy később más elemzők referenciaként használhassák őket.  

A vizsgálathoz használt adatforrások közé tartoznak azok a központi naplózási források, amelyeket a rendszer már gyűjt a hatókörbeli szolgáltatásokból és a futó rendszerekről, de a következők is lehetnek:

- Hálózati adatok – az Azure Network Watcher, az Azure Monitor és a hálózati biztonsági csoportok forgalmi naplóinak használata a hálózati forgalom naplóinak és más elemzési adatok rögzítéséhez. 

- A futó rendszerek pillanatképei: 

    - Az Azure-beli virtuális gép pillanatkép-készítési funkciójával pillanatképet készíthet a futó rendszer lemezéről. 

    - Az operációs rendszer natív memóriakép-készítési funkciójával pillanatképet készíthet a futó rendszer memóriájáról.

    - Az Azure-szolgáltatások vagy saját szoftvere pillanatkép-készítési funkciójával pillanatképeket készíthet a futó rendszerekről.

Az Azure Sentinel széles körű adatelemzést tesz lehetővé szinte bármely naplózási forráson és egy esetfelügyeleti portálon az incidensek teljes életciklusának kezeléséhez. A vizsgálatok során kapott információk társíthatók egy incidenssel nyomkövetés és jelentéskészítés céljából. 

- [Windows rendszerű gép lemezének pillanatképe](../../virtual-machines/windows/snapshot-copy-managed-disk.md)

- [Linux rendszerű gép lemezének pillanatképe](../../virtual-machines/linux/snapshot-copy-managed-disk.md)

- [Microsoft Azure-támogatás – diagnosztikai információk és memóriaképek gyűjtése](https://azure.microsoft.com/support/legal/support-diagnostic-information-collection/) 

- [Incidensek vizsgálata az Azure Sentinellel](../../sentinel/tutorial-investigate-cases.md)

**Az Azure Security Center monitorozása**: Nem alkalmazható

**Felelősség**: Ügyfél

### <a name="ir-5-detection-and-analysis--prioritize-incidents"></a>IR-5: Észlelés és elemzés – incidensek rangsorolása

**Útmutató**: Megadhat arra vonatkozó információkat az elemzők számára, hogy melyik incidensekre kell először összpontosítaniuk a súlyosság és az objektum bizalmassága alapján. 

Az Azure Security Center súlyossági szintet rendel az egyes riasztásokhoz, és segít rangsorolni, hogy melyik riasztásokat kell először kivizsgálni. A súlyosság azon alapul, hogy az Security Center milyen mértékben szerepel a riasztás kibocsátásához használt elemzésben vagy elemzésben, valamint azt a megbízhatósági szintet, amely a riasztáshoz vezető tevékenység mögött rosszindulatú szándékú volt.

Emellett a címkék használatával megjelölheti az erőforrásokat, és létrehozhat egy elnevezési rendszert az Azure-erőforrások, különösen a bizalmas adatok feldolgozását végző erőforrások azonosításához és kategorizálásához. Az Ön felelőssége, hogy rangsorolja a riasztások megoldását azon Azure-erőforrások és -környezetek kritikussága alapján, ahol az incidens történt.

- [Biztonsági riasztások az Azure Security Centerben](../../security-center/security-center-alerts-overview.md)

- [Címkék használata az Azure-erőforrások rendszerezéséhez](../management/tag-resources.md)

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="ir-6-containment-eradication-and-recovery--automate-the-incident-handling"></a>IR-6: Elszigetelés, kiiktatás és helyreállítás – az incidenskezelés automatizálása

**Útmutató**: Az ismétlődő manuális tevékenységek automatizálásával jobb reakcióidő érhető el, és csökkenthető az elemzők terhelése. A manuális tevékenységek végrehajtása több időt vesz igénybe, emiatt az elemzők kevesebb incidenst képesek kezelni. A manuális feladatok ki is merítik az elemzőket, ami növeli az emberi hiba lehetőségét, és az elemzők kevésbé tudnak hatékonyan összpontosítani az összetett feladatokra. 

Az Azure Security Center és az Azure Sentinel munkafolyamat-automatizálási funkcióival automatikusan indíthat el műveleteket és futtathat forgatókönyveket, hogy reagáljon a bejövő biztonsági riasztásokra. A forgatókönyv műveleteket hajt végre, például értesítéseket küld, fiókokat tilt le, és elkülöníti a problémás hálózatokat. 

- [Munkafolyamat-automatizálás konfigurálása a Security Centerben](../../security-center/workflow-automation.md)

- [Fenyegetésre adott automatikus válaszok beállítása az Azure Security Centerben](../../security-center/tutorial-security-incident.md#triage-security-alerts)

- [Fenyegetésre adott automatikus válaszok beállítása az Azure Sentinelben](../../sentinel/tutorial-respond-threats-playbook.md)

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

## <a name="posture-and-vulnerability-management"></a>A biztonsági állapot és a biztonsági rések kezelése

*További információ: [Azure Security Benchmark: A biztonsági állapot és a biztonsági rések kezelése](../../security/benchmarks/security-controls-v2-posture-vulnerability-management.md).*

### <a name="pv-1-establish-secure-configurations-for-azure-services"></a>PV-1: Biztonságos konfigurációk kialakítása Azure-szolgáltatásokhoz 

**Útmutató**: az infrastruktúra-és DevOps-csapatok biztonsági guardrails megadásával egyszerűen konfigurálhatja az általuk használt Azure-szolgáltatásokat.

Konfigurálja Azure Policy a felügyelt alkalmazások központi telepítésével kapcsolatos erőforrások konfigurációjának naplózásához és érvényesítéséhez.

Az Azure tervrajzai segítségével automatizálhatja a szolgáltatások és az alkalmazások környezetének üzembe helyezését és konfigurálását, beleértve a Azure Resource Manager sablonokat, az Azure RBAC-hozzárendeléseket és a Azure Policy-hozzárendeléseket egyetlen terv definíciójában.

- [Felhőalapú bevezetési keretrendszer – nagyvállalati szintű kirakodási zóna](/azure/cloud-adoption-framework/ready/enterprise-scale/architecture#landing-zone-expanded-definition)

- [Szabályzatok létrehozása és kezelése a megfelelőség kikényszerítése céljából](../../governance/policy/tutorials/create-and-manage.md)

- [Azure Blueprints](../../governance/blueprints/overview.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="pv-2-sustain-secure-configurations-for-azure-services"></a>PV-2: Biztonságos konfigurációk fenntartása Azure-szolgáltatásokhoz

**Útmutató**: a Azure Security Center segítségével figyelheti a Azure Managed Applicationshoz kapcsolódó erőforrásokat, és a Azure Policy [megtagadás] és a [telepítés, ha nem létezik] effektusokat használhatja a biztonságos konfigurációk fenntartásához.
- [Azure Policy effektusok ismertetése](../../governance/policy/concepts/effects.md)

- [Szabályzatok létrehozása és kezelése a megfelelőség kikényszerítése céljából](../../governance/policy/tutorials/create-and-manage.md)

- [Azure Blueprints](../../governance/blueprints/overview.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="pv-8-conduct-regular-attack-simulation"></a>PV-8: Rendszeres támadásszimulációk végrehajtása

**Útmutató**: Szükség esetén végezzen behatolási teszteket vagy riasztási gyakorlatokat az Azure-erőforrásokon, hogy biztosítva legyen az összes kritikus biztonsági találat megoldása.
A Microsoft-felhő behatolástesztelési beavatkozási szabályai szerint eljárva biztosíthatja, hogy a behatolási tesztek nem sértik a Microsoft szabályzatait. A Microsoft által felügyelt felhőalapú infrastruktúrán, szolgáltatásokon és alkalmazásokon végzett riasztási és élő behatolási tesztek végrehajtásához használja a Microsoft stratégiáját és végrehajtási tervét.

- [Behatolási tesztek az Azure-ban](../../security/fundamentals/pen-testing.md)

- [Behatolástesztelési beavatkozási szabályok](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) 

- [Riasztási tesztek a Microsoft-felhőben](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Megosztott

## <a name="backup-and-recovery"></a>Biztonsági másolat és helyreállítás

*További információ: [Azure Security Benchmark: Biztonsági mentés és helyreállítás](../../security/benchmarks/security-controls-v2-backup-recovery.md).*

### <a name="br-3-validate-all-backups-including-customer-managed-keys"></a>BR-3: Az összes biztonsági másolat és az ügyfelek által kezelt kulcsok ellenőrzése

**Útmutató**: Ha a felügyelt alkalmazás definícióit a saját Storage-fiókjában tárolja, ellenőrizze, hogy visszaállíthatja-e az adott fiók titkosításához használt összes társított ügyfél által felügyelt kulcsot, amelyeket Azure Key Vault tárol.

- [Saját tárterület használata a felügyelt alkalmazások definíciói számára](./publish-service-catalog-app.md?tabs=azure-powershell#bring-your-own-storage-for-the-managed-application-definition)

- [Key Vault kulcsok visszaállítása az Azure-ban](/powershell/module/az.keyvault/restore-azkeyvaultkey?amp;preserve-view=true&view=azps-5.1.0)

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="br-4-mitigate-risk-of-lost-keys"></a>BR-4: A kulcsok elvesztésével járó kockázat csökkentése

**Útmutató**: Ha saját tárolót hoz a felügyelt alkalmazás-definíciók számára, győződjön meg arról, hogy rendelkezik olyan mértékekkel, amelyek megakadályozzák a definíciók titkosításához használt kulcsok elvesztését és helyreállítását. Engedélyezze a Soft delete és Purge Protection szolgáltatást azon a Azure Key Vaulton, amely az ügyfél által felügyelt kulcsokat tárolja a kulcsok véletlen vagy rosszindulatú Törlés elleni védelme érdekében.  

- [Saját tárterület használata a felügyelt alkalmazások definíciói számára](./publish-service-catalog-app.md?tabs=azure-powershell#bring-your-own-storage-for-the-managed-application-definition)

- [Helyreállítható törlés és a végleges törléssel szembeni védelem engedélyezése kulcstartóban](../../storage/blobs/soft-delete-blob-overview.md?tabs=azure-portal)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

## <a name="governance-and-strategy"></a>Irányítás és stratégia

*További információ: [Azure Security Benchmark: Irányítás és stratégia](../../security/benchmarks/security-controls-v2-governance-strategy.md).*

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
- [Az Azure Security architektúrára vonatkozó ajánlásai – Tárolás, adatok és titkosítás](/azure/architecture/framework/security/storage-data-encryption?amp;bc=%2fsecurity%2fcompass%2fbreadcrumb%2ftoc.json&toc=%2fsecurity%2fcompass%2ftoc.json)

- [Az Azure Security alapjai – Azure-beli adatbiztonság, titkosítás és tárolás](../../security/fundamentals/encryption-overview.md)

- [Felhőadaptálási keretrendszer – Az Azure-beli adatbiztonsághoz és titkosításhoz ajánlott eljárások](../../security/fundamentals/data-encryption-best-practices.md?amp;bc=%2fazure%2fcloud-adoption-framework%2f_bread%2ftoc.json&toc=%2fazure%2fcloud-adoption-framework%2ftoc.json)

- [Azure Security Benchmark – Eszközkezelés](../../security/benchmarks/security-controls-v2-asset-management.md)

- [Azure Security Benchmark – Adatvédelem](../../security/benchmarks/security-controls-v2-data-protection.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="gs-2-define-enterprise-segmentation-strategy"></a>GS-2: Nagyvállalati szegmentálási stratégia definiálása 

**Útmutató**: Alakítson ki az egész vállalatra kiterjedő stratégiát, amely identitás, alkalmazás, előfizetés, felügyeleti csoport és más vezérlők kombinációjával szegmentálja az összetevőkhöz való hozzáférést.

Gondosan egyensúlyozza ki a biztonsági elkülönítés igényét azoknak a rendszereknek a mindennapos működésével, amelyeknek kommunikálniuk kell egymással, és hozzá kell férniük az adatokhoz.

Gondoskodjon róla, hogy a szegmentálási stratégia következetesen meg legyen valósítva az olyan vezérlőtípusokon, mint a hálózati biztonság, az identitás- és hozzáférés-modellek, az alkalmazások jogosultsági és hozzáférési modelljei és az emberi folyamatokra vonatkozó vezérlők.

- [Útmutató Azure-beli szegmentálási stratégiához (videó)](/security/compass/microsoft-security-compass-introduction#azure-components-and-reference-model-2151)

- [Útmutató Azure-beli szegmentálási stratégiához (dokumentum)](/security/compass/governance#enterprise-segmentation-strategy)

- [Hálózati szegmentálás vállalati szegmentálási stratégiához igazítása](/security/compass/network-security-containment#align-network-segmentation-with-enterprise-segmentation-strategy)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="gs-3-define-security-posture-management-strategy"></a>GS-3: Biztonsági állapotot felügyelő stratégiája definiálása

**Útmutató**: Folyamatosan mérje és mérsékelje a kockázatokat, amelyeknek az egyes összetevők, valamint az a környezet van kitéve, amelyben üzemeltetve vannak. Kezelje kiemelten az olyan nagy értékű összetevőket és leginkább elérhetővé tett támadási felületeket, mint a közzétett alkalmazások, a hálózat be- és kilépési pontjai, a felhasználói és rendszergazdai végpontok stb.

- [Azure Security Benchmark - Biztonsági állapot és biztonsági rések felmérése](../../security/benchmarks/security-controls-v2-posture-vulnerability-management.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="gs-4-align-organization-roles-responsibilities-and-accountabilities"></a>GS-4: A vállalati szerepkörök, a felelősség és az elszámoltathatóság összehangolása

**Útmutató**: Mindig dokumentálja és tegye közzé a biztonsági szervezet szerepköreire és felelősségi köreire vonatkozó egyértelmű stratégiát. Kezelje kiemelten a biztonsági döntések egyértelmű elszámoltathatóságát, a megosztott felelősségi modell oktatását mindenki számára, és a technikai csapatok oktatását a felhőbeli biztonsági technikákra.

- [Ajánlott Azure-biztonsági eljárások 1 – Személyek: Csapatok oktatása a felhőbeli biztonság kialakítására](/azure/cloud-adoption-framework/security/security-top-10#1-people-educate-teams-about-the-cloud-security-journey)

- [Ajánlott Azure-biztonsági eljárások 2 – Személyek: Csapatok oktatása a felhőbeli biztonsági technológiákra](/azure/cloud-adoption-framework/security/security-top-10#2-people-educate-teams-on-cloud-security-technology)

- [Ajánlott Azure-biztonsági eljárások 3 – Folyamat: Elszámoltathatóság hozzárendelése felhőbeli biztonsági döntésekhez](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

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

- [Azure Security Benchmark – Hálózati biztonság](../../security/benchmarks/security-controls-v2-network-security.md)

- [A nagyvállalati hálózati biztonság áttekintése](../../security/fundamentals/network-overview.md)

- [Nagyvállalati hálózati architektúrára vonatkozó stratégia](/azure/cloud-adoption-framework/ready/enterprise-scale/architecture)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="gs-6-define-identity-and-privileged-access-strategy"></a>GS-6: Identitáskezelési és emelt jogosultságú hozzáférési stratégia definiálása

**Útmutató**: Az identitások és az emelt jogosultságú hozzáférések kezelésének Azure-beli módszerét a vállalat átfogó biztonsági hozzáférés-vezérlési stratégiájának részeként alakíthat ki.  

Ennek a stratégiának magában kell foglalnia a dokumentált útmutatót, és az alábbi elemek szabványait: 

-   Központosított identitáskezelési és hitelesítési rendszer és annak kapcsolatai más belső és külső identitásrendszerekkel

-   Erős hitelesítési módszerek különböző használati helyzetek és feltételek esetén

-   Emelt jogosultságú felhasználók védelme

-   Rendellenes felhasználói tevékenységek monitorozása és kezelése  

-   Felhasználói identitások és hozzáférések felülvizsgálati és egyeztetési folyamata

További információkat az alábbi hivatkozásokon találhat:

- [Azure Security Benchmark – Identitáskezelés](../../security/benchmarks/security-controls-v2-identity-management.md)

- [Azure Security Benchmark – Emelt jogosultságú hozzáférés](../../security/benchmarks/security-controls-v2-privileged-access.md)

- [Ajánlott Azure-biztonsági eljárások 11 – Architektúra. Egyetlen egységes biztonsági stratégia](/azure/cloud-adoption-framework/security/security-top-10#11-architecture-establish-a-single-unified-security-strategy)

- [Az Azure identitáskezelésének biztonsági áttekintése](../../security/fundamentals/identity-management-overview.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

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

- [Azure Security Benchmark – Naplózás és fenyegetésészlelés](../../security/benchmarks/security-controls-v2-logging-threat-detection.md)

- [Azure Security Benchmark – Incidenskezelés](../../security/benchmarks/security-controls-v2-incident-response.md)

- [Ajánlott Azure-biztonsági eljárások 4 – Folyamat. Incidensválasz-folyamat frissítése a felhőhöz](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

- [Azure-adaptálási keretrendszer, útmutató naplózási és jelentéskészítési döntésekhez](/azure/cloud-adoption-framework/decision-guides/logging-and-reporting/)

- [Azure-beli nagyvállalati szintű skálázás, felügyelet és monitorozás](/azure/cloud-adoption-framework/ready/enterprise-scale/management-and-monitoring)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

## <a name="next-steps"></a>Következő lépések

- [Az Azure Security Benchmark v2 áttekintésének](../../security/benchmarks/overview.md) megtekintése
- További tudnivalók az [Azure biztonsági alapterveiről](../../security/benchmarks/security-baselines-overview.md)