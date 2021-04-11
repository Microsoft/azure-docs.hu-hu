---
title: Azure Active Directory Azure biztonsági alapterve
description: A Azure Active Directory biztonsági alapterve az Azure biztonsági Teljesítménytesztben meghatározott biztonsági javaslatok megvalósítására szolgáló eljárási útmutatást és erőforrásokat biztosít.
author: msmbaldwin
ms.service: active-directory
ms.topic: conceptual
ms.date: 04/07/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 8d4203b7b5d7742bf198778864fa4f42b7423596
ms.sourcegitcommit: c6a2d9a44a5a2c13abddab932d16c295a7207d6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/09/2021
ms.locfileid: "107286017"
---
# <a name="azure-security-baseline-for-azure-active-directory"></a>Azure Active Directory Azure biztonsági alapterve

Ez a biztonsági alapkonfiguráció az [Azure biztonsági teljesítményteszt 2,0-es verziójáról](../../security/benchmarks/overview.md) Azure Active Directoryre vonatkozó útmutatást alkalmaz. Az Azure Security Benchmark ajánlásokat ad arra nézve, hogy hogyan tehetők biztonságossá a felhőalapú megoldások az Azure-ban. A tartalom az Azure biztonsági teljesítményteszt által meghatározott **biztonsági vezérlők** szerint van csoportosítva, valamint a Azure Active Directory vonatkozó kapcsolódó útmutatás. 

> [!NOTE]
> Az Azure Active Directoryre nem alkalmazható **vezérlők** , vagy amelyek esetében a felelősség a Microsoft által lett kizárva ebből az alaptervből. Ha szeretné megtekinteni, hogyan Azure Active Directory teljes mértékben leképezni az Azure biztonsági Teljesítménytesztét, tekintse meg a [teljes Azure Active Directory biztonsági alapterv-leképezési fájlt](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="network-security"></a>Hálózati biztonság

*További információ: [Azure Security Benchmark: Hálózati biztonság](/azure/security/benchmarks/security-controls-v2-network-security).*

### <a name="ns-6-simplify-network-security-rules"></a>NS-6: a hálózati biztonsági szabályok egyszerűsítése

**Útmutató**: az Azure Virtual Network szolgáltatás-címkék használatával definiálhatja a hálózati biztonsági csoportokhoz vagy a Azure Active Directory erőforrásokhoz konfigurált Azure Firewallhoz szükséges hálózati hozzáférés-vezérlést. Biztonsági szabályok létrehozása során szolgáltatáscímkéket használhat bizonyos IP-címek helyett. A szolgáltatási címke nevének megadásával (például "AzureActiveDirectory") a szabály megfelelő forrás vagy cél mezőjében engedélyezheti vagy megtagadhatja a megfelelő szolgáltatás forgalmát. A Microsoft kezeli a szolgáltatási címke által felölelt címek előtagjait, és automatikusan frissíti a szolgáltatási címkét a címek változásával. 

- [A szolgáltatási címkék megismerése és használata](../../virtual-network/service-tags-overview.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

## <a name="identity-management"></a>Identitáskezelés

*További információ: [Azure Security Benchmark: Identitáskezelés](/azure/security/benchmarks/security-controls-v2-identity-management).*

### <a name="im-1-standardize-azure-active-directory-as-the-central-identity-and-authentication-system"></a>IM-1: Az Azure Active Directory, mint központi identitáskezelő és hitelesítési rendszer szabványosítása

**Útmutató**: az Azure Active Directory (Azure ad) használata alapértelmezett identitás-és hozzáférés-kezelési szolgáltatásként. Egységesítse az Azure AD-t a szervezet identitás-és hozzáférés-kezelésének szabályozásához a ben: 
- Microsoft Cloud erőforrások, például a Azure Portal, az Azure Storage, az Azure virtuális gép (Linux és Windows), a Azure Key Vault, a Pásti és az SaaS-alkalmazások. 
- a szervezet erőforrásaiban, például az Azure-on vagy a vállalati hálózat erőforrásain lévő alkalmazásokban. 
 

Az Azure AD biztonságának kiemelten fontosnak kell lennie a vállalat felhőbiztonsági gyakorlatában. Az Azure AD egy identitásbiztonsági pontszámmal segít felmérni az identitásbiztonsági helyzetet a Microsoft ajánlott eljárásaihoz viszonyítva. A pontszám alapján felmérheti, mennyire felel meg a konfiguráció az ajánlott eljárásoknak, és javíthatja a biztonság állapotát. 

Az Azure AD támogatja a külső identitást, amely lehetővé teszi a felhasználók számára, hogy Microsoft-fiók nélkül bejelentkezzenek alkalmazásaiba és erőforrásaiba külső identitásával. 

- [Bérlői viszony az Azure Active Directoryban](../develop/single-and-multi-tenant-apps.md)

- [Azure AD-példány létrehozása és konfigurálása](active-directory-access-create-new-tenant.md)

- [Külső identitásszolgáltatók használata alkalmazáshoz](/azure/active-directory/b2b/identity-providers)

- [Az identitásbiztonsági pontszám fogalma az Azure Active Directoryban](identity-secure-score.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="im-2-manage-application-identities-securely-and-automatically"></a>IM-2: Alkalmazásidentitások biztonságos és automatikus kezelése

**Útmutató**: felügyelt identitás használata az Azure-erőforrásokhoz a nem emberi fiókok, például a szolgáltatások vagy az automatizálás esetében ajánlott az Azure által felügyelt identitás funkció használata az erőforrások eléréséhez vagy végrehajtásához szükséges nagyobb teljesítményű emberi fiók létrehozása helyett. Natív módon végezhet hitelesítést olyan Azure-szolgáltatásokkal/erőforrásokkal, amelyek támogatják a Azure Active Directory (Azure AD) hitelesítést az előre definiált hozzáférési engedélyezési szabályon keresztül, és nem használja a forráskódban vagy a konfigurációs fájlokban rögzített hitelesítő adatokat. Az Azure-beli felügyelt identitások nem rendelhetők hozzá az Azure AD-erőforrásokhoz, de az Azure AD az a mechanizmus, amellyel a más szolgáltatás erőforrásaihoz rendelt felügyelt identitásokkal hitelesíthetők a hitelesítés.

- [Felügyelt identitás az Azure-erőforrásokhoz](../managed-identities-azure-resources/overview.md)

- [Az Azure-erőforrások felügyelt identitását támogató szolgáltatások](../managed-identities-azure-resources/services-support-managed-identities.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="im-3-use-azure-ad-single-sign-on-sso-for-application-access"></a>IM-3: Az Azure AD-beli egyszeri bejelentkezés használata alkalmazások eléréséhez

**Útmutató**: a Azure Active Directory használatával identitás-és hozzáférés-kezelést biztosíthat az Azure-erőforrásokhoz, a felhőalapú alkalmazásokhoz és a helyszíni alkalmazásokhoz. Ez vonatkozik az olyan nagyvállalati identitásokra, mint az alkalmazottak, valamint az olyan külső identitásokra is, mint a partnerek, szállítók és ellátók. Ez lehetővé teszi a vállalati adatok és erőforrások egyszeri bejelentkezéssel (SSO) megvalósított kezelését és védelmét a helyszínen és a felhőben. Az összes felhasználót, alkalmazást és eszközt beléptetheti az Azure AD-be a zökkenőmentes, biztonságos hozzáférés, valamint a jobb átláthatóság és vezérlés érdekében.

 
- [Az Azure AD-vel megvalósított alkalmazás-SSO ismertetése](../manage-apps/what-is-single-sign-on.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="im-4-use-strong-authentication-controls-for-all-azure-active-directory-based-access"></a>IM-4: Erős hitelesítési vezérlők használata minden Azure Active Directory-alapú hozzáféréshez

**Útmutató**: a többtényezős hitelesítéssel (MFA) és erős, jelszóval nem rendelkező metódusokkal támogatja az erős hitelesítési vezérlők használatát Azure Active Directory.
- Többtényezős hitelesítés – Engedélyezheti az Azure AD MFA-t, és az Azure Security Center identitás- és hozzáférés-kezelésre vonatkozó javaslatait követve megvalósíthat néhány, az MFA beállításához ajánlott eljárást. Az MFA megkövetelhető az összes kijelölt felhasználótól, vagy felhasználónként a bejelentkezési feltételek és a kockázati tényezők alapján.
- Jelszóval nem rendelkező hitelesítés – a Windows Hello for Business, az Microsoft Authenticator az alkalmazás és a helyszíni hitelesítési módszerek, például az intelligens kártyák.

A rendszergazda és a Kiemelt jogosultságú felhasználók számára győződjön meg arról, hogy az erős hitelesítési módszer legmagasabb szintje van használatban, majd ezt követi a megfelelő erős hitelesítési házirend kiosztása más felhasználók számára.

Az Azure AD támogatja a örökölt jelszó-alapú hitelesítést, például a csak felhőalapú fiókokat (az Azure AD-ben létrehozott felhasználói fiókokat), amelyek alapjelszó-házirendet vagy hibrid fiókokat (a helyszíni Active Directoryból származó felhasználói fiókokat) követik, és amelyek a helyszíni jelszóházirend alapján lesznek végrehajtva. A jelszó-alapú hitelesítés használatakor az Azure AD jelszavas védelmet biztosít, amely megakadályozza, hogy a felhasználók könnyen kitalált jelszavakat állítsanak be. A Microsoft globális listát biztosít a telemetria alapján frissített tiltott jelszavakról, és az ügyfelek igényeik alapján is kiterjeszthetik a listát (például branding, kulturális referenciák stb.). Ez a jelszavas védelem csak felhőalapú és hibrid fiókok esetében használható.

A jelszó-hitelesítő adatokon alapuló hitelesítés csak a népszerű támadási módszerekre van kitéve. A nagyobb biztonság érdekében használjon erős hitelesítést, például MFA-t és egy erős jelszóházirend-beállítást. A külső gyártóktól származó alkalmazások és Marketplace-szolgáltatások esetében, amelyek alapértelmezett jelszavakkal rendelkezhetnek, ezeket a szolgáltatás kezdeti beállításakor kell módosítania.

 
- [Az Azure AD MFA üzembe helyezése](../authentication/howto-mfa-getstarted.md) 

 

 
- [Az Azure Active Directory jelszó nélküli hitelesítési lehetőségeinek ismertetése](../authentication/concept-authentication-passwordless.md) 

 

 
- [Az Azure AD alapértelmezett jelszószabályzata](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-policy#password-policies-that-only-apply-to-cloud-user-accounts)

- [A nem megfelelő jelszavak használatának kiküszöbölése az Azure AD Password Protectionnel](../authentication/concept-password-ban-bad.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="im-5-monitor-and-alert-on-account-anomalies"></a>IM-5: Fiókok rendellenességeinek monitorozása, és riasztás azok alapján

**Útmutató**: a Azure Active Directory a következő adatforrásokat biztosítja:

 
- Bejelentkezések – a bejelentkezési jelentés a felügyelt alkalmazások használatával és a felhasználók bejelentkezési tevékenységeivel kapcsolatos információkat biztosít.

 
- Auditnaplók – az Azure AD-n belül különböző szolgáltatások által végrehajtott összes módosításra vonatkozó nyomkövetési naplókat biztosít. A naplók rögzítik például az erőforrások módosításait az Azure AD-n belül, például a felhasználók, alkalmazások, csoportok, szerepkörök és szabályzatok hozzáadását vagy eltávolítását.

 
- Kockázatos bejelentkezések – A kockázatos bejelentkezés egy olyan bejelentkezési kísérletet jelöl, amelyet elképzelhető, hogy olyan személy hajtott végre, aki nem a felhasználói fiók jogos tulajdonosa.

 
- Kockázatosként megjelölt felhasználók – A kockázatos felhasználó egy olyan felhasználói fiókot jelöl, amelynek elképzelhető, hogy sérült a biztonsága.

 

Ezek az adatforrások a Azure Monitor, az Azure Sentinel vagy a harmadik féltől származó SIEM rendszerekkel integrálhatók.

 

 
Az Azure Security Center riasztásokat is küldhet bizonyos gyanús tevékenységek észlelésekor. Ezek közé tartozik például a sikertelen hitelesítési kísérletek kiugróan magas száma vagy az előfizetésben talált elavult fiókok.

 

 
Az Azure Advanced Threat Protection (ATP) olyan biztonsági megoldás, amely Active Directory-jelek használatával tudja azonosítani, észlelni és kivizsgálni a súlyos fenyegetéseket, a sérült identitásokat és a rosszindulatú belső műveleteket.

 

 
- [Naplózott tevékenységekre vonatkozó jelentések az Azure Active Directoryban](../reports-monitoring/concept-audit-logs.md) 

 

 
- [Az Azure AD kockázatos bejelentkezéseinek megtekintése](/azure/active-directory/reports-monitoring/concept-risky-sign-ins) 

 

 
- [A kockázatos tevékenységek miatt megjelölt Azure AD-felhasználók azonosítása](/azure/active-directory/reports-monitoring/concept-user-at-risk) 

 

 
- [A felhasználók identitási és hozzáférési tevékenységeinek monitorozása az Azure Security Centerben](../../security-center/security-center-identity-access.md) 

 

 
- [Riasztások az Azure Security Center fenyegetések felderítésére szolgáló védelmi moduljában](../../security-center/alerts-reference.md) 

 

 
- [Azure-tevékenységnaplók integrálása az Azure Monitorba](../reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="im-6-restrict-azure-resource-access-based-on-conditions"></a>IM-6: Azure-erőforrásokhoz való hozzáférés korlátozása feltételek alapján

**Útmutató**: az Azure Active Directory (Azure ad) feltételes hozzáférés használata egy részletesebb hozzáférés-vezérléshez felhasználó által definiált feltételek alapján, például bizonyos IP-címtartományok felhasználói bejelentkezéséhez az MFA-t kell használnia a bejelentkezéshez. A részletes hitelesítési munkamenet-kezelési szabályzat többféle helyzetben is felhasználható.

 
- [Az Azure AD feltételes hozzáférésének áttekintése](../conditional-access/overview.md) 

 

 
- [Általános feltételes hozzáférési szabályzatok](../conditional-access/concept-conditional-access-policy-common.md) 

 

 
- [A hitelesítési munkamenetek kezelésének konfigurálása feltételes hozzáféréssel](../conditional-access/howto-conditional-access-session-lifetime.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="im-8-secure-user-access-to-legacy-applications"></a>IM-8: a felhasználók hozzáférésének biztosítása az örökölt alkalmazásokhoz

**Útmutató**: gondoskodjon arról, hogy modern hozzáférés-vezérlést és munkamenet-figyelést biztosítson az örökölt alkalmazásokhoz és az általuk tárolt és feldolgozott adatkezeléshez Habár a VPN-eket általában a régi alkalmazások elérésére használják, gyakran csak alapszintű hozzáférés-vezérléssel és korlátozott munkamenet-figyeléssel rendelkeznek.

 
Az Azure AD Application Proxy lehetővé teszi az örökölt helyszíni alkalmazások közzétételét egyszeri bejelentkezéssel a távoli felhasználók számára, miközben explicit módon ellenőrzi a távoli felhasználók és az eszközök megbízhatóságát az Azure AD feltételes hozzáféréssel.

 

 
Azt is megteheti, Microsoft Cloud App Security egy felhőalapú hozzáférés-vezérlési (CASB) szolgáltatás, amely lehetővé teszi a felhasználók alkalmazás-munkameneteinek és a blokkoló műveleteknek a figyelését (mind a régi helyszíni alkalmazások, mind a felhőalapú szoftverek (SaaS) alkalmazásai esetében).

 

 
- [Azure Active Directory Application Proxy](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy#what-is-application-proxy) 

 

 
- [Microsoft Cloud App Security ajánlott eljárások](/cloud-app-security/best-practices)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

## <a name="privileged-access"></a>Emelt jogosultságú hozzáférés

*További információ: [Azure Security Benchmark: Emelt jogosultságú hozzáférés](/azure/security/benchmarks/security-controls-v2-privileged-access).*

### <a name="pa-1-protect-and-limit-highly-privileged-users"></a>PA-1: Emelt jogosultságú felhasználók védelme és korlátozása

**Útmutató**: a legfontosabb beépített szerepkörök az Azure ad globális rendszergazda és a Kiemelt szerepkörű rendszergazda, mivel az ehhez a két szerepkörhöz hozzárendelt felhasználók delegálni tudják a rendszergazdai szerepköröket:

- Globális rendszergazda: az ehhez a szerepkörhöz tartozó felhasználók hozzáférhetnek az Azure AD összes felügyeleti funkciójához, valamint az Azure AD-identitásokat használó szolgáltatásokhoz.

- Kiemelt szerepkörű rendszergazda: az ezzel a szerepkörrel rendelkező felhasználók kezelhetik a szerepkör-hozzárendeléseket az Azure AD-ben, valamint a Azure AD Privileged Identity Managementon (PIM) belül is. Emellett ez a szerepkör lehetővé teszi a PIM és a felügyeleti egységek valamennyi aspektusának kezelését.

Előfordulhat, hogy más kritikus szerepkörökre is szükség van, amelyeket az egyéni szerepkörök hozzárendelésekor kell alkalmazni. Emellett előfordulhat, hogy hasonló vezérlőket szeretne alkalmazni a kritikus fontosságú üzleti eszközök rendszergazdai fiókjára.

Az Azure AD magas jogosultsági szintű fiókokkal rendelkezik: azok a felhasználók és szolgáltatások, amelyek közvetlenül vagy közvetve vannak hozzárendelve a globális rendszergazdai vagy Kiemelt szerepkörű rendszergazdai szerepkörökhöz, illetve az Azure AD-ben és az Azure-ban található egyéb magas jogosultsági szintű szerepkörökhöz.

Korlátozza a magas jogosultságú fiókok számát, és emelt szintű védelemmel látja el ezeket a fiókokat, mivel az ezzel a jogosultsággal rendelkező felhasználók közvetlenül vagy közvetve elolvashatják és módosíthatják az Azure-környezet minden erőforrását.

Az Azure AD Privileged Identity Management (PIM) használatával engedélyezheti az Azure-erőforrások és az Azure AD igény szerinti (just-in-time, JIT) jogosultságú hozzáférését. A JIT ideiglenes engedélyeket biztosít az érintett feladatok végrehajtásához, csak annyi időre, ameddig a felhasználóknak erre szükségük van. A PIM biztonsági riasztásokat is képes kiadni, amikor gyanús vagy nem biztonságos tevékenységeket észlel az Azure AD-szervezetben.

- [Rendszergazdai szerepköri engedélyek az Azure AD-ben](/azure/active-directory/users-groups-roles/directory-assign-admin-roles) 

- [Az Azure Privileged Identity Management biztonsági riasztásainak használata](../privileged-identity-management/pim-how-to-configure-security-alerts.md) 

- [Emelt szintű hozzáférés biztosítása Azure AD hibrid- és felhőkörnyezetekhez](/azure/active-directory/users-groups-roles/directory-admin-roles-secure)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="pa-2-restrict-administrative-access-to-business-critical-systems"></a>PA-2: Az üzletileg kritikus rendszerek rendszergazdai elérésének korlátozása

**Útmutató**: a Azure Active Directory Privileged Identity Management és a többtényezős hitelesítés használata az üzleti szempontból kritikus rendszerekhez való rendszergazdai hozzáférés korlátozására.

- [A szerepkör-aktiválási kérelmek jóváhagyása Privileged Identity Management](../privileged-identity-management/azure-ad-pim-approval-workflow.md)

- [Multi-Factor Authentication és feltételes hozzáférés](../conditional-access/howto-conditional-access-policy-admin-mfa.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="pa-3-review-and-reconcile-user-access-regularly"></a>PA-3: Felhasználói hozzáférés rendszerességének áttekintése és egyeztetése

**Útmutató**: a felhasználói fiókok hozzáférési hozzárendeléseinek rendszeres felülvizsgálata a fiókok és a hozzáférésük érvényességének biztosítása érdekében, különösen a magas jogosultsági szintű szerepkörök, például a globális rendszergazda és a Kiemelt szerepkörű rendszergazda számára. Az Azure AD-szerepkörökhöz és az Azure-szerepkörökhöz egyaránt használhatja Azure Active Directory (Azure AD) hozzáférési felülvizsgálatokat a csoporttagságok áttekintéséhez, a vállalati alkalmazásokhoz való hozzáféréshez és a szerepkör-hozzárendelésekhez. Az Azure AD jelentéskészítési szolgáltatása lehetővé teszi, hogy a naplók segítséget nyújtsanak az elavult fiókok felderítéséhez. A felülvizsgálati folyamat megkönnyítése érdekében a Azure AD Privileged Identity Management használatával hozzáférési felülvizsgálati jelentési munkafolyamatot is létrehozhat.

Emellett az Azure Privileged Identity Management is beállítható úgy, hogy riasztást hozzon létre túl sok rendszergazdai fiók létrehozásakor, valamint a elavult vagy helytelenül konfigurált rendszergazdai fiókok azonosításához.

- [Azure AD-szerepkörök hozzáférési felülvizsgálatának létrehozása Privileged Identity Management (PIM) szolgáltatásban](../privileged-identity-management/pim-how-to-start-security-review.md)

- [Azure-beli erőforrás-szerepkörök hozzáférési felülvizsgálatának létrehozása Privileged Identity Management (PIM)](../privileged-identity-management/pim-resource-roles-start-access-review.md)

- [Az Azure AD identitás- és hozzáférési felülvizsgálatainak használata](../governance/access-reviews-overview.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="pa-4-set-up-emergency-access-in-azure-ad"></a>PA-4: Vészhelyzeti hozzáférés beállítása az Azure AD-ben

**Útmutató**: Ha el szeretné kerülni az Azure ad-szervezetből való véletlen kizárást, állítson be egy vészhelyzeti hozzáférési fiókot a hozzáféréshez, ha a normál rendszergazdai fiókok nem használhatók. A vészhelyzeti hozzáférési fiókok általában magas szintű jogosultságokkal rendelkeznek, és nem ajánlott azokat egyes személyekhez társítani.
A vészhelyzeti hozzáférési fiókok csak az olyan vészhelyzeti esetekre valók, amikor a normál rendszergazdai fiókok nem használhatók.

Érdemes biztosítani, hogy a vészhelyzeti hozzáférési fiókok hitelesítő adatai (például jelszó, tanúsítvány vagy intelligens kártya) biztonságos helyen vannak tárolva, amelyet csak azok ismernek, akik jogosultak azokat használni, kizárólag vészhelyzet esetén.

- [Vészhelyzeti hozzáférési fiókok kezelése az Azure AD-ben](/azure/active-directory/users-groups-roles/directory-emergency-access)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="pa-5-automate-entitlement-management"></a>PA-5: a jogosultságok kezelésének automatizálása 

**Útmutató**: a hozzáférési kérelmek munkafolyamatainak automatizálásához használja az Azure ad jogosultság-kezelési szolgáltatásait, beleértve a hozzáférési hozzárendeléseket, az értékeléseket és a lejáratot. A kettős vagy többfázisú jóváhagyás is támogatott.

- [Mi az Azure AD-jogosultságok kezelése?](../governance/entitlement-management-overview.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="pa-6-use-privileged-access-workstations"></a>PA-6: Emelt szintű hozzáférésű munkaállomások használata

**Útmutató**: A biztonságos, elkülönített munkaállomások kritikus fontosságúak az olyan bizalmas szerepkörök biztonsága szempontjából, mint a rendszergazdák, a fejlesztők vagy a kritikus fontosságú szolgáltatások üzemeltetői. A biztonságos felhasználói munkaállomásokat és/vagy az Azure Bastion-t a felügyeleti feladatokhoz használhatja. Az Azure Active Directory, a Microsoft Defender Advanced Threat Protection (ATP) és/vagy a Microsoft Intune használatával biztonságos és felügyelt felhasználói munkaállomásokat helyezhet üzembe a rendszergazdai tevékenységekhez. A védett munkaállomások központi kezelésével kikényszeríthető a biztonságos konfiguráció, beleértve az erős hitelesítést, a szoftveres és hardveres alapkonfigurációikat, valamint a korlátozott logikai és hálózati hozzáférést.

- [Eszközök biztonságossá tétele a privilegizált hozzáférés részeként](/security/compass/privileged-access-devices)

- [Emelt szintű hozzáférés implementációja](/security/compass/privileged-access-deployment)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="pa-7-follow-just-enough-administration-least-privilege-principle"></a>PA-7: A Just Enough Administration (legkisebb jogosultsági alapelv) követése 

**Útmutató**: az ügyfelek a lehető legkevesebb jogosultsággal konfigurálhatják a Azure Active Directory (Azure ad) üzembe helyezését, ha a felhasználók számára a rendszergazdai feladatok elvégzéséhez szükséges minimális engedélyekkel rendelnek hozzá felhasználókat a szerepkörökhöz.

- [Rendszergazdai szerepköri engedélyek az Azure AD-ben](../roles/permissions-reference.md)

- [Rendszergazdai szerepkörök kiosztása az Azure AD-ben](../roles/manage-roles-portal.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="pa-8-choose-approval-process-for-microsoft-support"></a>PA-8: a Microsoft támogatási szolgálatának jóváhagyási folyamatának kiválasztása  

**Útmutató**: a Azure Active Directory nem támogatja a Customer kulcstárolót. A Microsoft az ügyfelek adataihoz való hozzáférés jóváhagyása érdekében a nem kulcstároló-metódusokon keresztül is működhet.

**Felelősség**: Megosztott

**Azure Security Center figyelés**: nincs

## <a name="data-protection"></a>Adatvédelem

*További információ: [Azure Security Benchmark: Adatvédelem](/azure/security/benchmarks/security-controls-v2-data-protection).*

### <a name="dp-2-protect-sensitive-data"></a>DP-2: A bizalmas adatok védelme

**Útmutató**: vegye figyelembe a következő útmutatást a bizalmas adatok védelmének megvalósításához:

- **Elkülönítés:** A címtár az az adathatár, amellyel a Azure Active Directory (Azure AD) szolgáltatások tárolják és dolgozzák fel az ügyfelekre vonatkozó adatmennyiséget. Az ügyfélnek meg kell határoznia, hogy az Azure AD-ügyféladatok nagy részét a címtárában lévő Country (ország) tulajdonság beállításával szeretné-e tárolni.

- **Szegmentálás:** A globális rendszergazda szerepkör teljes hozzáférést biztosít az összes címtár-adathoz, valamint a hozzáférési és feldolgozási utasításokat szabályozó szabályokhoz. A címtárak a felügyeleti egységekre oszthatók, és azok a felhasználók és csoportok számára kiépítve, amelyeket az adott egység rendszergazdája felügyel, a globális rendszergazdák a szervezet más alapelvei számára is kioszthatják a felelősséget úgy, hogy az előre meghatározott szerepkörökhöz vagy az általuk létrehozott egyéni szerepkörökhöz rendelik a feladatokat.

 
- **Hozzáférés:** Az engedélyek felhasználóra, csoportra, szerepkörre, alkalmazásra vagy eszközre is alkalmazhatók. A hozzárendelés Privileged Identity Management konfigurációjában állandó vagy időszakos lehet. 
  
- **Titkosítás:** Az Azure AD minden inaktív vagy átvitel alatt álló adatforgalmat titkosít. Az ajánlat nem teszi lehetővé, hogy az ügyfelek a saját titkosítási kulccsal titkosítsák a címtáradatokat. 

Annak megállapításához, hogy a kiválasztott ország hogyan képezi le a címtár fizikai helyét, tekintse meg a "hol található az adatai a cikkben" című részt.

- [Hol találhatók az adatai a cikkben](https://www.microsoft.com/trust-center/privacy/data-location)

Mivel az ügyfél különböző Azure AD-eszközöket, funkciókat és alkalmazásokat használ a címtárával, használja a Azure Active Directory – hol találhatók az adatai?

- [Hol található az adatai az irányítópulton](https://msit.powerbi.com/view?r=eyJrIjoiYzEyZTc5OTgtNTdlZS00ZTVkLWExN2ItOTM0OWU4NjljOGVjIiwidCI6IjcyZjk4OGJmLTg2ZjEtNDFhZi05MWFiLTJkN2NkMDExZGI0NyIsImMiOjV9)

- [Az Azure AD-szerepkörök dokumentációja](/azure/active-directory/roles/)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="dp-4-encrypt-sensitive-information-in-transit"></a>DP-4: Bizalmas információk átvitel közbeni titkosítása

**Útmutató**: a hozzáférés-vezérlés kiegészítéseként a forgalomban lévő adatoknak védelmet biztosítanak a "sávon kívüli" támadások (például a forgalmi rögzítés) ellen, hogy a támadók ne tudják egyszerűen beolvasni vagy módosítani az adatokat.

Az Azure AD a TLS 2.0-s vagy újabb verzióban támogatja az adatátvitel titkosítását.

Habár ez nem kötelező a magánhálózaton lévő forgalom esetében, ez kritikus fontosságú a külső és a nyilvános hálózatok forgalmában. HTTP-forgalom esetén győződjön meg arról, hogy az Azure-erőforrásokhoz csatlakozó összes ügyfél egyeztetheti a TLS v 1.2-es vagy újabb verzióját. A Távoli felügyelet érdekében titkosítatlan protokoll helyett használja az SSH-t (Linuxon) vagy RDP/TLS (Windows rendszeren). Az elavult SSL-, TLS-és SSH-verziók és protokollok, valamint a gyenge titkosítások le lesznek tiltva.

Alapértelmezés szerint az Azure titkosítást biztosít az Azure-adatközpontok közötti adatátvitelhez.

- [A titkosítás ismertetése az Azure-ban](https://docs.microsoft.com/azure/security/fundamentals/encryption-overview#encryption-of-data-in-transit) 

- [Információk a TLS biztonságáról](/security/engineering/solving-tls1-problem) 

- [Kettős titkosítás az Azure-beli adatforgalomban](https://docs.microsoft.com/azure/security/fundamentals/double-encryption#data-in-transit)

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

- [Az Azure-beli felügyeleti csoportok áttekintése](../../governance/management-groups/overview.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="am-5-limit-users-ability-to-interact-with-azure-resource-manager"></a>AM-5: korlátozza a felhasználók képességét a Azure Resource Manager való interakcióra

**Útmutató**: az Azure Active Directory (Azure ad) feltételes hozzáférés használatával korlátozhatja, hogy a felhasználók képesek legyenek az Azure ad-vel való interakcióra az Azure Portal segítségével a "Microsoft Azure felügyelet" alkalmazás "hozzáférés letiltása" beállításával.

- [Feltételes hozzáférés konfigurálása az Azure Resources Manager elérésének blokkolásához](../../role-based-access-control/conditional-access-azure-management.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

## <a name="logging-and-threat-detection"></a>Naplózás és fenyegetésészlelés

*További információ: [Azure Security Benchmark: Naplózás és fenyegetésészlelés](/azure/security/benchmarks/security-controls-v2-logging-threat-protection).*

### <a name="lt-1-enable-threat-detection-for-azure-resources"></a>LT-1: az Azure-erőforrások fenyegetés-észlelésének engedélyezése

**Útmutató**: az Azure ad-erőforrások beépített veszélyforrások észlelési funkciójának használata a Azure Active Directory (Azure ad) Identity Protection szolgáltatással. 
 
 
 

 
Az Azure AD olyan tevékenységi naplókat hoz létre, amelyeket gyakran használ a fenyegetések észlelésére és a fenyegetések vadászatára. Az Azure AD bejelentkezési naplói egy rekordot biztosítanak a felhasználók, szolgáltatások és alkalmazások hitelesítési és engedélyezési tevékenységeiről. Az Azure AD-naplók nyomon követik az Azure AD-bérlőn végrehajtott módosításokat, beleértve a biztonsági testhelyzetet javító vagy csökkentő változásokat is.

- [Mi az az Azure Active Directory Identity Protection?](../identity-protection/overview-identity-protection.md)

- [Azure AD Identity Protection-adatkapcsolatok összekötése az Azure Sentinel szolgáltatással](../../sentinel/connect-azure-ad-identity-protection.md)

- [Azure Active Directory-adatkapcsolatok összekötése az Azure Sentinel szolgáltatással](../../sentinel/connect-azure-active-directory.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="lt-2-enable-threat-detection-for-azure-identity-and-access-management"></a>LT-2: Fenyegetések észlelésének engedélyezése az Azure-beli identitás- és hozzáférés-kezeléshez

**Útmutató**: a Azure Active Directory (Azure ad) a következő felhasználói naplókat nyújtja, amelyek megtekinthetők az Azure ad jelentéskészítési szolgáltatásban, vagy a Azure monitor, az Azure Sentinel vagy más Siem/monitoring eszközökkel integráltan kifinomultabb monitorozási és elemzési felhasználási esetekhez: 
- Bejelentkezések – a bejelentkezési jelentés a felügyelt alkalmazások használatával és a felhasználók bejelentkezési tevékenységeivel kapcsolatos információkat biztosít. 
- Auditnaplók – az Azure AD-n belül különböző szolgáltatások által végrehajtott összes módosításra vonatkozó nyomkövetési naplókat biztosít. A naplók rögzítik például az erőforrások módosításait az Azure AD-n belül, például a felhasználók, alkalmazások, csoportok, szerepkörök és szabályzatok hozzáadását vagy eltávolítását. 
- Kockázatos bejelentkezések – A kockázatos bejelentkezés egy olyan bejelentkezési kísérletet jelöl, amelyet elképzelhető, hogy olyan személy hajtott végre, aki nem a felhasználói fiók jogos tulajdonosa. 
- Kockázatos felhasználók – A kockázatos felhasználó egy olyan felhasználói fiókhoz tartozó kijelző, amely esetleg sérült. 

Az Identity Protection kockázati észlelése alapértelmezés szerint engedélyezve van, és nincs szükség bevezetési folyamatra. A részletességi vagy kockázati adatokat a licenc SKU határozza meg. 

- [Naplózott tevékenységekre vonatkozó jelentések az Azure Active Directoryban](../reports-monitoring/concept-audit-logs.md)  

- [Az Azure Identity Protection engedélyezése](../identity-protection/overview-identity-protection.md)  

- [Fenyegetésvédelem az Azure Security Centerben](/azure/security-center/threat-protection)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="lt-4-enable-logging-for-azure-resources"></a>LT-4: Naplózás engedélyezése Azure-erőforrásokhoz

**Útmutató**: a Azure Active Directory (Azure ad) tevékenység-naplókat hoz létre. Néhány Azure-szolgáltatástól eltérően az Azure AD nem tesz különbséget a tevékenységek és az erőforrás-naplók között. A Tevékenységnaplók automatikusan elérhetők a Azure Portal Azure AD szakaszában, és exportálhatók Azure Monitor, Azure Event Hubs, Azure Storage, SIEM és más helyszínekre.
 
- Bejelentkezések – a bejelentkezési jelentés a felügyelt alkalmazások használatával és a felhasználók bejelentkezési tevékenységeivel kapcsolatos információkat biztosít.  
 
- Auditnaplók – az Azure AD-n belül különböző szolgáltatások által végrehajtott összes módosításra vonatkozó nyomkövetési naplókat biztosít. A naplók rögzítik például az erőforrások módosításait az Azure AD-n belül, például a felhasználók, alkalmazások, csoportok, szerepkörök és szabályzatok hozzáadását vagy eltávolítását.  
 
 

Az Azure AD olyan biztonsággal kapcsolatos naplókat is biztosít, amelyek az Azure AD jelentéskészítési szolgáltatásban tekinthetők meg, illetve a Azure Monitor, az Azure Sentinel vagy más SIEM/monitoring eszközökkel integráltan összetettebb monitorozási és elemzési felhasználási esetekhez: 
- Kockázatos bejelentkezések – A kockázatos bejelentkezés egy olyan bejelentkezési kísérletet jelöl, amelyet elképzelhető, hogy olyan személy hajtott végre, aki nem a felhasználói fiók jogos tulajdonosa.
- Kockázatos felhasználók – A kockázatos felhasználó egy olyan felhasználói fiókhoz tartozó kijelző, amely esetleg sérült. 

Az Identity Protection kockázati észlelése alapértelmezés szerint engedélyezve van, és nincs szükség bevezetési folyamatra. A részletességi vagy kockázati adatokat a licenc SKU határozza meg. 

 
- [Tevékenységek és biztonsági jelentések a Azure Active Directory](../reports-monitoring/overview-reports.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="lt-5-centralize-security-log-management-and-analysis"></a>LT-5: Biztonsági naplók kezelésének és elemzésének központosítása

**Útmutató**: a következő irányelveket javasoljuk, amikor az ügyfelek központilag szeretnék központosítani a biztonsági naplókat, hogy könnyebb legyen a veszélyforrások elleni vadászat és a biztonsági testhelyzet elemzése:
 
- Központosítsa a naplózási tárolást és elemzést a korreláció engedélyezéséhez. Az Azure AD-ben minden egyes naplózási forráshoz hozzá kell rendelni egy adattulajdonost, egy hozzáférési útmutatót, egy tárolási helyet, az adatok feldolgozásához és eléréséhez, valamint az adatmegőrzési követelményekhez használt eszközöket.  
 
- Győződjön meg arról, hogy integrálja az Azure-tevékenységek naplóit a központi naplózásba. Naplók betöltése Azure Monitor használatával a végponti eszközök, a hálózati erőforrások és más biztonsági rendszerek által generált biztonsági adatokat összesítve. Azure Monitor a Log Analytics-munkaterületek használatával kérdezheti le és végezheti el az elemzéseket, és használhatja az Azure Storage-fiókokat hosszú távú és archiválási tároláshoz.  
 
- Emellett az Azure Sentinel vagy egy harmadik féltől származó SIEM-nek is engedélyezi az adatok bevezetését.  
 

Számos szervezet úgy dönt, hogy a gyakran használt és az Azure-tárolót használja a ritkábban használt adatokhoz az Azure Sentinel használatával.  
 

- [Platform-naplók és-metrikák összegyűjtése Azure Monitor](/azure/azure-monitor/platform/diagnostic-settings)   
 

- [Az Azure Sentinel előkészítése](../../sentinel/quickstart-onboard.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="lt-6-configure-log-storage-retention"></a>LT-6: Tárolt naplók megőrzésének konfigurálása

**Útmutató**: gondoskodjon arról, hogy minden olyan Storage-fiók vagy log Analytics munkaterület, amely a Azure Active Directory bejelentkezési naplók, a naplók és a kockázati adatnaplók tárolására szolgál, a szervezet megfelelőségi előírásai szerint állítsa be a napló megőrzési időtartamát.

Azure Monitor az Log Analytics munkaterület megőrzési időszakát a szervezet megfelelőségi előírásai szerint állíthatja be. Az Azure Storage, a Data Lake vagy a Log Analytics munkaterület-fiókok használata hosszú távú és archiválási tároláshoz.

- [Log Analytics munkaterület megőrzési időtartamának konfigurálása](/azure/azure-monitor/platform/manage-cost-storage)  

- [Erőforrás-naplók tárolása Azure Storage-fiókban](/azure/azure-monitor/platform/resource-logs-collect-storage)

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

- [Az Azure Security Center biztonsági kapcsolattartójának beállítása](../../security-center/security-center-provide-security-contact-details.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="ir-3-detection-and-analysis--create-incidents-based-on-high-quality-alerts"></a>IR-3: észlelés és elemzés – incidensek létrehozása kiváló minőségű riasztások alapján 

**Útmutató**: Gondoskodjon egy, a magas minőségű riasztások létrehozására és a riasztások minőségének mérésére szolgáló folyamatról. Ez lehetővé teszi a korábbi incidensek tanulságainak levonását és a riasztások rangsorolását az elemzők számára, hogy ne veszítsenek időt a téves riasztások miatt. 

A magas minőségű riasztások készítése épülhet a korábbi incidensek tapasztalataira, az ellenőrzött közösségi forrásokra, valamint azokra az eszközökre, amelyek a riasztások generálását és tisztítását a különböző jelforrások egyesítésével és egyeztetésével végzik. 

Azure Security Center kiváló minőségű riasztásokat biztosít számos Azure-eszközön. Az ASC adatösszekötővel streamelheti a riasztásokat az Azure Sentinelbe. Az Azure Sentinellel speciális riasztási szabályokat hozhat létre, amelyekkel automatikusan hozhat létre incidenseket a vizsgálatokhoz. 

Az exportálási funkcióval exportálhatja az Azure Security Center riasztásait és javaslatait, amelyek segítenek az Azure-erőforrások kockázatainak azonosításában. A riasztásokat és javaslatokat manuálisan vagy folyamatosan is exportálhatja.

- [Az exportálás konfigurálása](../../security-center/continuous-export.md)

- [Riasztások streamelése az Azure Sentinelbe](../../sentinel/connect-azure-security-center.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

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

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="ir-5-detection-and-analysis--prioritize-incidents"></a>IR-5: Észlelés és elemzés – incidensek rangsorolása

**Útmutató**: Megadhat arra vonatkozó információkat az elemzők számára, hogy melyik incidensekre kell először összpontosítaniuk a súlyosság és az objektum bizalmassága alapján. 

Az Azure Security Center súlyossági szintet rendel az egyes riasztásokhoz, és segít rangsorolni, hogy melyik riasztásokat kell először kivizsgálni. A súlyosság azon alapul, hogy az Security Center milyen mértékben szerepel a riasztás kibocsátásához használt elemzésben vagy elemzésben, valamint azt a megbízhatósági szintet, amely a riasztáshoz vezető tevékenység mögött rosszindulatú szándékú volt.

Emellett a címkék használatával megjelölheti az erőforrásokat, és létrehozhat egy elnevezési rendszert az Azure-erőforrások, különösen a bizalmas adatok feldolgozását végző erőforrások azonosításához és kategorizálásához.  Az Ön felelőssége, hogy rangsorolja a riasztások megoldását azon Azure-erőforrások és -környezetek kritikussága alapján, ahol az incidens történt.

- [Biztonsági riasztások az Azure Security Centerben](../../security-center/security-center-alerts-overview.md)

- [Címkék használata az Azure-erőforrások rendszerezéséhez](/azure/azure-resource-manager/resource-group-using-tags)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="ir-6-containment-eradication-and-recovery--automate-the-incident-handling"></a>IR-6: Elszigetelés, kiiktatás és helyreállítás – az incidenskezelés automatizálása

**Útmutató**: Az ismétlődő manuális tevékenységek automatizálásával jobb reakcióidő érhető el, és csökkenthető az elemzők terhelése. A manuális tevékenységek végrehajtása több időt vesz igénybe, emiatt az elemzők kevesebb incidenst képesek kezelni. A manuális feladatok ki is merítik az elemzőket, ami növeli az emberi hiba lehetőségét, és az elemzők kevésbé tudnak hatékonyan összpontosítani az összetett feladatokra. Az Azure Security Center és az Azure Sentinel munkafolyamat-automatizálási funkcióival automatikusan indíthat el műveleteket és futtathat forgatókönyveket, hogy reagáljon a bejövő biztonsági riasztásokra. A forgatókönyv műveleteket hajt végre, például értesítéseket küld, fiókokat tilt le, és elkülöníti a problémás hálózatokat. 

- [Munkafolyamat-automatizálás konfigurálása a Security Centerben](../../security-center/workflow-automation.md)

- [Fenyegetésre adott automatikus válaszok beállítása az Azure Security Centerben](https://docs.microsoft.com/azure/security-center/tutorial-security-incident#triage-security-alerts)

- [Fenyegetésre adott automatikus válaszok beállítása az Azure Sentinelben](../../sentinel/tutorial-respond-threats-playbook.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

## <a name="posture-and-vulnerability-management"></a>A biztonsági állapot és a biztonsági rések kezelése

*További információ: [Azure Security Benchmark: A biztonsági állapot és a biztonsági rések kezelése](/azure/security/benchmarks/security-controls-v2-vulnerability-management).*

### <a name="pv-1-establish-secure-configurations-for-azure-services"></a>PV-1: Biztonságos konfigurációk kialakítása Azure-szolgáltatásokhoz 

**Útmutató**: a Microsoft identitás-és hozzáférés-kezelési megoldásai segítenek a helyszíni és a Felhőbeli alkalmazásokhoz és erőforrásokhoz való hozzáférés védelmében. Fontos, hogy a szervezetek az identitásuk és a hozzáférés-kezelés megvalósításának biztosítása érdekében az ajánlott biztonsági eljárásokat kövessék, és rugalmasabban használják a támadásokat. 

Az identitás-és hozzáférés-kezelési implementációs stratégia alapján a szervezetnek a Microsoft ajánlott eljárási útmutatóját kell követnie az identitás-infrastruktúra biztonságossá tételéhez. 

A külső partnerekkel együttműködve a biztonsági kockázatok csökkentése és a bizalmas erőforrások védelme érdekében a megfelelő irányítási, biztonsági és megfelelőségi konfigurációkat is fel kell mérnie és megvalósítani.

- [Az Azure-beli identitáskezelés és hozzáférés-vezérlés ajánlott biztonsági eljárásai](../../security/fundamentals/identity-management-best-practices.md)

- [Öt lépés a személyazonossági infrastruktúra biztonságossá tételéhez](../../security/fundamentals/steps-secure-identity.md)

- [A külső együttműködés biztonságossá tétele Azure Active Directory és Microsoft 365](secure-external-access-resources.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="pv-2-sustain-secure-configurations-for-azure-services"></a>PV-2: Biztonságos konfigurációk fenntartása Azure-szolgáltatásokhoz

**Útmutató**: a Microsoft biztonságos pontszáma biztosítja a szervezeteknek a biztonsági helyzetük és a fenyegetések elleni védekezést segítő javaslatok értékelését. Javasoljuk, hogy a szervezetek rutinszerűen tekintsék át a biztonságos pontszámot, hogy a javasolt fejlesztési műveletek során javítsák a személyazonosságuk biztonsági helyzetét. 

- [Mi az a Azure Active Directory identitások biztonságos pontszáma?](identity-secure-score.md)

- [Microsoft Biztonsági pontszám](/microsoft-365/security/mtp/microsoft-secure-score)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="pv-8-conduct-regular-attack-simulation"></a>PV-8: Rendszeres támadásszimulációk végrehajtása

**Útmutató**: Szükség esetén végezzen behatolási teszteket vagy riasztási gyakorlatokat az Azure-erőforrásokon, hogy biztosítva legyen az összes kritikus biztonsági találat megoldása.
A Microsoft-felhő behatolástesztelési beavatkozási szabályai szerint eljárva biztosíthatja, hogy a behatolási tesztek nem sértik a Microsoft szabályzatait. A Microsoft által felügyelt felhőalapú infrastruktúrán, szolgáltatásokon és alkalmazásokon végzett riasztási és élő behatolási tesztek végrehajtásához használja a Microsoft stratégiáját és végrehajtási tervét.

- [Behatolási tesztek az Azure-ban](../../security/fundamentals/pen-testing.md)

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
- [Az Azure Security architektúrára vonatkozó ajánlásai – Tárolás, adatok és titkosítás](/azure/architecture/framework/security/storage-data-encryption)
 

 
- [Az Azure Security alapjai – Azure-beli adatbiztonság, titkosítás és tárolás](../../security/fundamentals/encryption-overview.md) 

 
- [Felhőadaptálási keretrendszer – Az Azure-beli adatbiztonsághoz és titkosításhoz ajánlott eljárások](../../security/fundamentals/data-encryption-best-practices.md) 

 
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

- [A nagyvállalati hálózati biztonság áttekintése](../../security/fundamentals/network-overview.md)

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

- [Az Azure identitáskezelésének biztonsági áttekintése](../../security/fundamentals/identity-management-overview.md)

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
