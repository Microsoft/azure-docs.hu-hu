---
title: Azure DevTest Labs Azure biztonsági alapterve
description: Azure DevTest Labs Azure biztonsági alapterve
ms.topic: conceptual
ms.date: 07/23/2020
ms.openlocfilehash: c192f881dd4a8544b9c833eece56fb3765cd07f8
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/17/2021
ms.locfileid: "100582030"
---
# <a name="azure-security-baseline-for-azure-devtest-labs"></a>Azure DevTest Labs Azure biztonsági alapterve

Az Azure DevTest Labs Azure biztonsági alapkonfigurációja olyan javaslatokat tartalmaz, amelyek segítségével javíthatja az üzemelő példány biztonsági állapotát.

A szolgáltatás alapterve az [Azure Security Benchmark 1,0-es verziójából](../security/benchmarks/overview.md)származik, amely javaslatokat nyújt az Azure-beli felhőalapú megoldások biztonságossá tételéhez.

További információ: [Azure Security](../security/benchmarks/security-baselines-overview.md)alapkonfigurációk áttekintése.

## <a name="logging-and-monitoring"></a>Naplózás és monitorozás

*További információ [: Security Control: naplózás és figyelés](../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="21-use-approved-time-synchronization-sources"></a>2,1: a jóváhagyott idő-szinkronizálási források használata
**Útmutató:** A Microsoft az Azure-erőforrások időforrásait is fenntartja. Azonban a számítási erőforrások időszinkronizálási beállításait is kezelheti.

A következő cikkből megtudhatja, hogyan konfigurálhatja az időszinkronizálást az Azure számítási erőforrásaihoz: [a Windows rendszerű virtuális gépek időszinkronizálása az Azure-ban](../virtual-machines/windows/time-sync.md). 

**Azure Security Center figyelés:** Jelenleg nem érhető el

**Felelősség:** Microsoft

### <a name="22-configure-central-security-log-management"></a>2,2: a központi biztonsági naplók felügyeletének konfigurálása
**Útmutató:** Engedélyezze az Azure-műveletnapló diagnosztikai beállításait, és küldje el a naplókat egy Log Analytics munkaterületre, az Azure Event hub vagy az Azure Storage-fiókba az archívumhoz. A tevékenységi naplók betekintést nyújtanak a Azure DevTest Labs példányain végrehajtott műveletekre a felügyeleti sík szintjén. Az Azure-beli tevékenység naplójának adatai alapján meghatározhatja, hogy a DevTest Labs-példányok felügyeleti síkon milyen műveleteket hajtson végre az írási műveletekhez (PUT, POST, DELETE).

További információ: [diagnosztikai beállítások létrehozása a platform naplófájljainak és metrikáinak különböző célhelyekre küldéséhez](../azure-monitor/essentials/diagnostic-settings.md).

**Azure Security Center figyelés:** Jelenleg nem érhető el

**Felelősség:** Ügyfél

### <a name="23-enable-audit-logging-for-azure-resources"></a>2,3: az Azure-erőforrások naplózásának engedélyezése
**Útmutató:** Engedélyezze az Azure-műveletnapló diagnosztikai beállításait, és küldje el a naplókat egy Log Analytics munkaterületre, az Azure Event hub vagy az Azure Storage-fiókba az archívumhoz. A tevékenységi naplók betekintést nyújtanak a Azure DevTest Labs példányain végrehajtott műveletekre a felügyeleti sík szintjén. Az Azure-tevékenység naplójának adatai alapján meghatározhatja a "mi, ki és mikor" minden írási művelethez (PUT, POST, DELETE) a DevTest Labs-példányok felügyeleti síkja szintjén.

További információ: [diagnosztikai beállítások létrehozása a platform naplófájljainak és metrikáinak különböző célhelyekre küldéséhez](../azure-monitor/essentials/diagnostic-settings.md).

**Azure Security Center figyelés:** Jelenleg nem érhető el

**Felelősség:** Ügyfél

### <a name="24-collect-security-logs-from-operating-systems"></a>2,4: biztonsági naplók gyűjtése az operációs rendszerekből
**Útmutató:** Azure DevTest Labs virtuális gépeket (VM) az ügyfél hozza létre és birtokolja. Tehát a szervezet feladata a figyelés. A számítási operációs rendszer figyeléséhez Azure Security Center is használhatja. Az operációs rendszer Security Center által összegyűjtött adatok közé tartozik az operációs rendszer típusa és verziója, az operációs rendszer (Windows-eseménynaplók), a futó folyamatok, a gép neve, az IP-címek és a bejelentkezett felhasználó. A Log Analytics ügynök az összeomlási memóriaképek fájljait is gyűjti.

További információért tekintse át a következő cikkeket: 

- [Az Azure-beli virtuális gépek belső gazdagép-naplóinak összegyűjtése Azure Monitor](../azure-monitor/vm/quick-collect-azurevm.md)
- [Az Azure Security Center adatgyűjtés ismertetése](../security-center/security-center-enable-data-collection.md)

**Azure Security Center figyelés:** igen

**Felelősség:** Ügyfél

### <a name="25-configure-security-log-storage-retention"></a>2,5: a biztonsági napló tárolási adatmegőrzésének konfigurálása
***Útmutató:** A Azure Monitor a szervezet megfelelőségi előírásai szerint állítsa be a Azure DevTest Labs példányokhoz társított Log Analytics-munkaterületek naplózásának megőrzési időtartamát.

További információ: a [napló megőrzési paramétereinek beállítása](../azure-monitor/logs/manage-cost-storage.md#change-the-data-retention-period) .

**Azure Security Center figyelés:** Nem alkalmazható

**Felelősség:** Ügyfél

### <a name="26-monitor-and-review-logs"></a>2,6: naplók figyelése és áttekintése
**Útmutató:** Engedélyezze az Azure-tevékenység naplójának diagnosztikai beállításait, és küldje el a naplókat egy Log Analytics munkaterületre. Log Analytics lekérdezéseket futtathat a kifejezésekben, azonosíthatja a trendeket, elemezheti a mintákat, és számos más elemzést is elvégezhet a Azure DevTest Labs számára összegyűjtött tevékenységi naplók alapján.

További információért tekintse át a következő cikkeket:

- [Diagnosztikai beállítások engedélyezése az Azure-beli tevékenység naplójában](../azure-monitor/essentials/diagnostic-settings.md)
- [Azure-Tevékenységnaplók összegyűjtése és elemzése Log Analytics munkaterületen Azure Monitor](../azure-monitor/essentials/activity-log.md)

**Azure Security Center figyelés:** Nem alkalmazható

**Felelősség:** Ügyfél

### <a name="27-enable-alerts-for-anomalous-activity"></a>2,7: riasztások engedélyezése rendellenes tevékenységhez
**Útmutató:** Az Azure Log Analytics munkaterület segítségével figyelheti és riasztást tehet a rendellenes tevékenységekről a biztonsági naplókban és a Azure DevTest Labs kapcsolatos eseményekben.

További információkért tekintse meg a következő cikket: [riasztás a log Analytics-naplózási adatokról](../azure-monitor/alerts/tutorial-response.md)

**Azure Security Center figyelés:** Jelenleg nem érhető el

**Felelősség:** Ügyfél

### <a name="28-centralize-anti-malware-logging"></a>2,8: kártevő szoftverek közötti naplózás központosítása
**Útmutató:** Nem alkalmazható. Azure DevTest Labs nem dolgoz fel kártevő szoftverrel kapcsolatos naplókat, illetve nem hoz létre.

**Azure Security Center figyelés:** Nem alkalmazható

**Felelősség:** Ügyfél

### <a name="29-enable-dns-query-logging"></a>2,9: DNS-lekérdezések naplózásának engedélyezése
**Útmutató:** Nem alkalmazható. A Azure DevTest Labs nem dolgozza fel a DNS-sel kapcsolatos naplókat, és nem hoz létre.

**Azure Security Center figyelés:** Nem alkalmazható

**Felelősség:** Ügyfél

### <a name="210-enable-command-line-audit-logging"></a>2,10: parancssori naplózás engedélyezése
**Útmutató:** A Azure DevTest Labs az ügyfél által birtokolt és felügyelt Azure számítási gépeket hoz létre. A folyamat-létrehozási esemény és a mező naplózásához használja a Microsoft monitoring agentet az összes támogatott Azure Windows-alapú virtuális gépen `CommandLine` . A támogatott Azure Linux rendszerű virtuális gépek esetében a konzol naplózását manuálisan is konfigurálhatja a csomópontok alapján, és a syslog használatával tárolhatja az adattárakat. Emellett a Azure Monitor Log Analytics munkaterülete segítségével tekintheti át a naplókat, és futtathat lekérdezéseket az Azure-beli virtuális gépekről naplózott adatokon.

- [Adatgyűjtés az Azure Security Centerben](../security-center/security-center-enable-data-collection.md#data-collection-tier)
- [Egyéni lekérdezések futtatása a Azure Monitorban](../azure-monitor/logs/get-started-queries.md)
- [Rendszernapló-adatforrások az Azure Monitorban](../azure-monitor/agents/data-sources-syslog.md)

**Azure Security Center figyelés:** igen

**Felelősség:** Ügyfél

## <a name="identity-and-access-control"></a>Identitás- és hozzáférés-vezérlés
*További információ [: Security Control (identitás és Access Control](../security/benchmarks/security-control-identity-access-control.md)).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3,1: a felügyeleti fiókok leltárának karbantartása
**Útmutató:** Azure Active Directory (Azure AD) beépített szerepkörökkel rendelkezik, amelyeket explicit módon kell hozzárendelni, és lekérdezhető. Az Azure AD PowerShell-modullal ad hoc lekérdezéseket futtathat a felügyeleti csoportok tagjait futtató fiókok felderítéséhez.

- [Címtárbeli szerepkör beszerzése az Azure AD-ben a PowerShell-lel](/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0)
- [Címtárbeli szerepkör tagjainak beszerzése az Azure AD-ben a PowerShell-lel](/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0)
- [Szerepkörök Azure DevTest Labs](devtest-lab-add-devtest-user.md)  

**Azure Security Center figyelés:** igen

**Felelősség:** Ügyfél

### <a name="32-change-default-passwords-where-applicable"></a>3,2: az alapértelmezett jelszavak módosítása, ha alkalmazható
**Útmutató:** Azure Active Directory (Azure AD) nem az alapértelmezett jelszavak fogalma. Más Azure-erőforrások, amelyek jelszó megadását igénylik a bonyolultsági követelményekkel és a jelszó minimális hosszával, amely a szolgáltatástól függően eltérő lehet. Ön felelős harmadik féltől származó alkalmazásokért és piactér-szolgáltatásért, amelyek az alapértelmezett jelszavakat használhatják.

A DevTest Labs nem rendelkezik az alapértelmezett jelszavak fogalmával. 

**Azure Security Center figyelés:** Nem alkalmazható

**Felelősség:** Ügyfél

### <a name="33-use-dedicated-administrative-accounts"></a>3,3: dedikált rendszergazdai fiókok használata
**Útmutató:** Hozzon létre szabványos működési eljárásokat a dedikált rendszergazdai fiókok használata körül. A rendszergazdai fiókok számának figyeléséhez használja a Azure Security Center identitás-és hozzáférés-kezelés lehetőséget.

Emellett a dedikált rendszergazdai fiókok nyomon követésének elősegítése érdekében Azure Security Center vagy beépített Azure-szabályzatokból származó javaslatokat is használhat, például:

- Az előfizetéshez egynél több tulajdonos rendelhető hozzá
- A tulajdonosi engedélyekkel rendelkező elavult fiókokat el kell távolítani az előfizetésből
- A tulajdonosi engedélyekkel rendelkező külső fiókokat el kell távolítani az előfizetésből

- [Az identitás és a hozzáférés figyelésének Azure Security Center használata (előzetes verzió)](../security-center/security-center-identity-access.md)  
- [A Azure Policy használata](../governance/policy/tutorials/create-and-manage.md)
- [Szerepkörök Azure DevTest Labs](devtest-lab-add-devtest-user.md)  

**Azure Security Center figyelés:** igen

**Felelősség:** Ügyfél

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3,4: egyszeri bejelentkezés (SSO) használata Azure Active Directory
**Útmutató:** A DevTest Labs az Azure AD szolgáltatást használja az Identitáskezelés kezelésére. Vegye figyelembe ezt a két fontos szempontot, amikor a felhasználók számára hozzáférést biztosít a DevTest Labs-alapú környezetekhez:

- **Erőforrás-kezelés:** Hozzáférést biztosít a Azure Portal az erőforrások kezeléséhez (virtuális gépek létrehozásához, környezetek létrehozásához, elindításához, leállításához, újraindításához, törléséhez és az összetevők alkalmazásához stb.). Az erőforrás-kezelés az Azure-ban az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC) használatával végezhető el. Szerepköröket rendelhet a felhasználókhoz, és beállíthatja az erőforrás-és hozzáférési szintű engedélyeket.
- **Virtual Machines (hálózati szintű)**: az alapértelmezett konfigurációban a virtuális gépek helyi rendszergazdai fiókot használnak. Ha van elérhető tartomány (Azure AD Domain Services, egy helyszíni tartomány vagy egy felhőalapú tartomány), a gépek csatlakoztathatók a tartományhoz. A felhasználók ezután használhatják a tartományon alapuló identitásokat a tartományhoz való csatlakozás használatával a gépekhez való csatlakozáshoz. 

- [A DevTest Labs hivatkozási architektúrája](devtest-lab-reference-architecture.md#architecture)
- [Az egyszeri bejelentkezés ismertetése az Azure AD-vel](../active-directory/manage-apps/what-is-single-sign-on.md)

**Azure Security Center figyelés:** Nem alkalmazható

**Felelősség:** Ügyfél

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3,5: Multi-Factor Authentication használata az összes Azure Active Directory-alapú hozzáféréshez
**Útmutató:** Engedélyezze Azure Active Directory (AD) Multi-Factor Authentication (MFA), és kövesse Azure Security Center identitás-és hozzáférés-kezelési javaslatait.

- [MFA engedélyezése az Azure-ban](../active-directory/authentication/howto-mfa-getstarted.md)  
- [Identitás és hozzáférés figyelése Azure Security Centeron belül](../security-center/security-center-identity-access.md)

**Azure Security Center figyelés:*** igen

**Felelősség:** Ügyfél


### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3,6: dedikált gépek (privilegizált hozzáférési munkaállomások) használata az összes felügyeleti feladathoz
**Útmutató:** Az Azure-erőforrások bejelentkezéséhez és konfigurálásához használja az MFA-t a privilegizált hozzáférésű munkaállomások (PAWs) használatára.

- [További tudnivalók a privilegizált hozzáférésű munkaállomásokról](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)  
- [MFA engedélyezése az Azure-ban](../active-directory/authentication/howto-mfa-getstarted.md)  

**Azure Security Center figyelés:** N/A

**Felelősség:** Ügyfél

### <a name="37-log-and-alert-on-suspicious-activity-from-administrative-accounts"></a>3,7: naplózás és riasztás a gyanús tevékenységekről a rendszergazdai fiókoktól
**Útmutató:** A naplók és a riasztások generálásához használjon Azure Active Directory (Azure AD) biztonsági jelentéseket, ha a környezetben gyanús vagy nem biztonságos tevékenység történik. A Azure Security Center használatával figyelheti az identitás-és hozzáférési tevékenységeket.

- [A kockázatos tevékenységek miatt megjelölt Azure AD-felhasználók azonosítása](../active-directory/identity-protection/overview-identity-protection.md)  
- [A felhasználók identitási és hozzáférési tevékenységeinek monitorozása az Azure Security Centerben](../security-center/security-center-identity-access.md)  

**Azure Security Center figyelés:** Jelenleg nem érhető el

**Felelősség:** Ügyfél

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3,8: az Azure-erőforrások kezelése csak jóváhagyott helyekről
**Útmutató:** A feltételes hozzáférés elnevezett helyeivel engedélyezheti a hozzáférést az IP-címtartományok vagy országok/régiók adott logikai csoportjaihoz.

- [Elnevezett helyszínek konfigurálása az Azure-ban](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)  

**Azure Security Center figyelés:** Jelenleg nem érhető el

**Felelősség:** Ügyfél

### <a name="39-use-azure-active-directory"></a>3,9: a Azure Active Directory használata
**Útmutató:** A Azure Active Directory (Azure AD) központi hitelesítési és engedélyezési rendszerrel használható. Az Azure AD az adatok védelme érdekében erős titkosítást használ a nyugalmi és a továbbítási adatokhoz. Az Azure AD emellett a felhasználó hitelesítő adatainak a sók, a kivonatok és a biztonságos tárolását is tartalmazza.

- [Azure AD-példány létrehozása és konfigurálása](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)  

**Azure Security Center figyelés:** Jelenleg nem érhető el

**Felelősség:** Ügyfél

### <a name="310-regularly-review-and-reconcile-user-access"></a>3,10: a felhasználói hozzáférés rendszeres áttekintése és egyeztetése
**Útmutató:** A Azure Active Directory (Azure AD) olyan naplókat biztosít, amelyek segítenek az elavult fiókok felderítésében. Emellett az Azure Identity Access Reviews használatával hatékonyan kezelheti a csoporttagságok kezelését, a vállalati alkalmazásokhoz való hozzáférést és a szerepkör-hozzárendeléseket. A felhasználói hozzáférés rendszeresen felülvizsgálható, hogy csak a megfelelő felhasználók férhessenek hozzá.

- [Az Azure AD jelentéskészítés ismertetése](../active-directory/reports-monitoring/overview-reports.md)  
- [Az Azure Identity hozzáférési felülvizsgálatok használata](../active-directory/governance/access-reviews-overview.md)  

**Azure Security Center figyelés:** Nem alkalmazható

**Felelősség:** Ügyfél

### <a name="311-monitor-attempts-to-access-deactivated-accounts"></a>3,11: az inaktivált fiókok elérésére irányuló kísérletek figyelése
**Útmutató:** Hozzáférése van Azure Active Directory (Azure AD) bejelentkezési tevékenységekhez, naplózási és kockázati Eseménynapló-forrásokhoz, amelyek lehetővé teszik, hogy integrálható legyen a biztonsági információkkal és az Event Management (SIEM)/monitoring eszközzel.

Ezt a folyamatot leegyszerűsítheti Azure Active Directory felhasználói fiókok diagnosztikai beállításainak létrehozásával, valamint a naplók és a bejelentkezési naplók Log Analytics munkaterületre való elküldésével. A riasztásokat Log Analytics munkaterületen belül is konfigurálhatja.

- [Azure-tevékenységnaplók integrálása az Azure Monitorba](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)  

**Azure Security Center figyelés:** Jelenleg nem érhető el

**Felelősség:** Ügyfél

### <a name="312-alert-on-account-login-behavior-deviation"></a>3,12: riasztás a fiók bejelentkezési viselkedésének eltérése esetén
**Útmutató:** A Azure Active Directory (Azure AD) kockázati és Identity Protection-funkciókkal konfigurálhatja az automatizált válaszokat a felhasználói identitásokkal kapcsolatos gyanús műveletekhez.

- [Az Azure AD kockázatos bejelentkezéseinek megtekintése](../active-directory/identity-protection/overview-identity-protection.md)  
- [Az Identity Protection kockázati házirendjeinek konfigurálása és engedélyezése](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)  

**Azure Security Center figyelés:** Jelenleg nem érhető el

**Felelősség:** Ügyfél

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3,13: a Microsoft számára elérhetővé teszi a megfelelő ügyféladatokat a támogatási forgatókönyvek során
**Útmutató:** Ügyfélszéf jelenleg nem támogatott a Azure DevTest Labs.

- [Ügyfélszéf támogatott szolgáltatások listája](../security/fundamentals/customer-lockbox-overview.md#supported-services-and-scenarios-in-general-availability) 

**Azure Security Center figyelés:** Nem alkalmazható

**Felelősség:** Ügyfél

## <a name="data-protection"></a>Adatvédelem
*További információkért lásd [: biztonsági ellenőrzés:](../security/benchmarks/security-control-data-protection.md)adatvédelem.*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4,1: bizalmas információk leltárának fenntartása
**Útmutató:** A címkék használatával segítheti a bizalmas adatokat tároló vagy feldolgozó Azure-erőforrások nyomon követését.

- [Címkék létrehozása és használata](../azure-resource-manager/management/tag-resources.md)

**Azure Security Center figyelés:** Nem alkalmazható

**Felelősség:** Ügyfél

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4,2: bizalmas adatok tárolására vagy feldolgozására szolgáló rendszerek elkülönítése
**Útmutató:** Külön előfizetések vagy felügyeleti csoportok implementálása fejlesztési, tesztelési és éles környezetekben. Azure DevTest Labs példányokat virtuális hálózat/alhálózat szerint kell elválasztani, és a címkét megfelelően címkézve kell megadni. 

- [További Azure-előfizetések létrehozása](../cost-management-billing/manage/create-subscription.md)
- [Felügyeleti csoportok létrehozása](../governance/management-groups/create-management-group-portal.md)
- [Virtuális hálózat konfigurálása a DevTest Labs szolgáltatáshoz](devtest-lab-configure-vnet.md)
- [Címkék létrehozása és használata](../azure-resource-manager/management/tag-resources.md)
- [Címkék létrehozása és használata a DevTest Labs szolgáltatásban](devtest-lab-add-tag.md)

**Azure Security Center figyelés:** Jelenleg nem érhető el

**Felelősség:** Ügyfél

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4,3: a bizalmas adatok jogosulatlan átvitelének figyelése és letiltása
**Útmutató:** Még nem érhető el; az adatazonosítási, besorolási és veszteség-megelőzési funkciók még nem érhetők el Azure DevTest Labs számára.

A Microsoft kezeli a Azure DevTest Labs alapjául szolgáló infrastruktúrát, és szigorú ellenőrzéseket vezetett be az ügyféladatok elvesztésének vagy kihatásának megelőzésére.

- [Az ügyféladatok Azure-beli védelmének ismertetése](../security/fundamentals/protection-customer-data.md)

**Azure Security Center figyelés:** Jelenleg nem érhető el

**Felelősség:** Megosztott

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4,4: minden bizalmas adat titkosítása az átvitel során
**Útmutató:** A Azure DevTest Labs alapértelmezés szerint TLS-titkosítású kommunikációt igényel. A 1,2-es TLS-verziók jelenleg támogatottak. Ha az ügyféloldali kódtár vagy eszköz nem támogatja a TLS-t, akkor a titkosítatlan kapcsolatok engedélyezése a Azure Portal vagy a felügyeleti API-kon keresztül végezhető el. Olyan esetekben, ahol a titkosított kapcsolatok nem lehetségesek, a tesztkörnyezet és az ügyfélalkalmazás virtuális hálózatra helyezése ajánlott.

[A DevTest Labs adatátviteli forgatókönyve titkosításának megismerése](https://techcommunity.microsoft.com/t5/azure-developer-community-blog/azure-devtest-labs-enforcing-tls-1-2-starting-may-01-2020/ba-p/1236279)

**Azure Security Center figyelés:** igen

**Felelősség:** Megosztott

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4,5: aktív felderítési eszköz használata a bizalmas adatok azonosítására
**Útmutató:** Az adatazonosítási, besorolási és veszteség-megelőzési funkciók még nem érhetők el Azure DevTest Labs számára. A bizalmas adatokat tartalmazó példányok címkézése, és ha szükséges, a harmadik féltől származó megoldás implementálása.

A Microsoft által felügyelt mögöttes platform esetében a Microsoft az összes vásárlói tartalmat bizalmasként kezeli, és az ügyfelek adatvesztésével és a kitettséggel szembeni védelem érdekében nagy hosszúságú. Annak biztosítása érdekében, hogy az Azure-beli ügyféladatok biztonságban maradjanak, a Microsoft végrehajtotta és karbantartja a robusztus adatvédelmi szabályozást és képességeket.

- [Az ügyféladatok Azure-beli védelmének ismertetése](../security/fundamentals/protection-customer-data.md)

**Azure Security Center figyelés:** Jelenleg nem érhető el

**Felelősség:** Ügyfél

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4,6: az erőforrásokhoz való hozzáférés szabályozása az Azure RBAC
**Útmutató:** Az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC) használatával szabályozhatja a Azure DevTest Labs-ban található laborokhoz való hozzáférést.

- [Az Azure RBAC konfigurálása](../role-based-access-control/role-assignments-portal.md)
- [A DevTest Labs szerepköreinek megismerése](devtest-lab-add-devtest-user.md)

**Azure Security Center figyelés:** Nem alkalmazható

**Felelősség:** Ügyfél

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4,7: a gazdagép-alapú adatvesztés-megelőzés használata a hozzáférés-vezérlés kikényszeríthető
**Útmutató:** Ha a DevTest Labs részeként létrehozott számítási erőforrások megfelelőségére szükség van, egy külső gyártótól származó eszközt, például egy automatizált gazdagép-alapú adatvesztés-megelőzési megoldást kell megvalósítani, hogy a hozzáférés-vezérlést az adatokra akkor is kényszerítse, ha az adatok a rendszerből vannak másolva.

A Microsoft által felügyelt mögöttes platform esetében a Microsoft az összes vásárlói tartalmat bizalmasként kezeli, és az ügyfelek adatvesztésével és a kitettséggel szembeni védelem érdekében nagy hosszúságú. Annak biztosítása érdekében, hogy az Azure-beli ügyféladatok biztonságban maradjanak, a Microsoft végrehajtotta és karbantartja a robusztus adatvédelmi szabályozást és képességeket.

- [Az ügyféladatok Azure-beli védelmének ismertetése](../security/fundamentals/protection-customer-data.md)

**Azure Security Center figyelés:** Nem alkalmazható

**Felelősség:** Nem alkalmazható

### <a name="48-encrypt-sensitive-information-at-rest"></a>4,8: bizalmas adatok titkosítása a nyugalmi állapotban
**Útmutató:** Azure DevTest Labs a következő ügyféladatokat tárolja:

- Az összetevők alkalmazásával létrehozott üzembe helyezési és kiterjesztési naplókat tartalmazó összetevők [eredményei](add-artifact-vm.md)
- A képletekben a virtuális gépek létrehozásához használt [dokumentumok](devtest-lab-manage-formulas.md)
- Az operációs rendszer és az adatlemezek a tesztkörnyezet virtuális gépei számára 

A rendszer az összetevők eredményeit és a képletek dokumentumait egy olyan Azure Storage-fiókba továbbítja, amely az összes tesztkörnyezetben üzemelő példány részeként jön létre. Az Azure Storage-ban tárolt adatai a 256 bites AES-titkosítással vannak titkosítva és visszafejtve, és az egyik legerősebb blokk titkosítási algoritmus, amely az FIPS 140-2-kompatibilis. Az Azure Storage-titkosítás nem tiltható le. A Microsoft által felügyelt kulcsokat használhatja a Storage-fiók titkosításához, vagy a titkosítást a saját kulcsaival is kezelheti. További információ: [a labor Storage-fiók titkosítása](encrypt-storage.md).

Alapértelmezés szerint az összes Lab operációs rendszer és adatlemez egy platform által felügyelt kulccsal van titkosítva. Az összes felügyelt lemez, pillanatkép, lemezkép és a meglévő felügyelt lemezekre írt adatok automatikusan titkosítva vannak a platform által felügyelt kulcsokkal. A labor tulajdonosaként beállíthatja, hogy a labor operációsrendszer-lemezei egy ügyfél által felügyelt kulccsal legyenek titkosítva. A labor adatlemezek ügyfél által felügyelt kulcsával történő titkosítás jelenleg nem konfigurálható a laborban. Egy előfizetés-rendszergazda azonban az előfizetésben lévő Lab-lemezekre is konfigurálhatja ezt a beállítást. További információ: a [labor DevTest Labs operációsrendszer-lemezek titkosítása az ügyfél által felügyelt kulcsokkal](encrypt-disks-customer-managed-keys.md).

**Azure Security Center figyelés:** Nem alkalmazható

**Felelősség:** Megosztott

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4,9: a kritikus Azure-erőforrások változásainak naplózása és riasztása
**Útmutató:** A Azure Monitor és az Azure-tevékenység naplójának használatával riasztásokat hozhat létre, amelyekkel a DevTest Labs-példányok és egyéb kritikus vagy kapcsolódó erőforrások módosíthatók.

- [Riasztások létrehozása az Azure-tevékenységek naplózási eseményeihez](../azure-monitor/alerts/alerts-activity-log.md)
- [Riasztások létrehozása a DevTest Labs tevékenység-naplózási eseményeihez](create-alerts.md)

**Azure Security Center figyelés:** Nem alkalmazható

**Felelősség:** Ügyfél



## <a name="vulnerability-management"></a>Biztonságirés-kezelés
*További információ [: Security Control: sebezhetőségi kezelés](../security/benchmarks/security-control-vulnerability-management.md).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5,1: automatikus biztonsági rések vizsgálatára szolgáló eszközök futtatása
**Útmutató:** A Azure DevTest Labs-példányok és a kapcsolódó erőforrások biztonságossá tételéhez kövesse Azure Security Center javaslatait.

A Microsoft a Azure DevTest Labst támogató mögöttes erőforrásokon hajtja végre a biztonsági rések kezelését.

- [Azure Security Center javaslatok ismertetése](../security-center/recommendations-reference.md) 

**Azure Security Center figyelés:** igen

**Felelősség:** Megosztott

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5,2: az operációs rendszer automatikus javításának felügyeleti megoldásának telepítése
**Útmutató:** Az Azure Update Management használatával biztosíthatja, hogy a legújabb biztonsági frissítések telepítve legyenek a DevTest Labs szolgáltatásban üzemeltetett Windows-és Linux-alapú virtuális gépekre. Windows rendszerű virtuális gépek esetén győződjön meg arról, hogy Windows Update engedélyezve van, és automatikus frissítésre van beállítva. Ez a beállítás jelenleg nem érhető el a DevTest Labs szolgáltatáson keresztül történő konfiguráláshoz, azonban a laboratóriumi rendszergazda/előfizetés-rendszergazda konfigurálhatja ezt a beállítást az előfizetése mögöttes számítási virtuális gépeken. 

- [Virtuális gépek Update Management konfigurálása az Azure-ban](../automation/update-management/overview.md)
- [A Security Center által figyelt Azure biztonsági házirendek ismertetése](../security-center/policy-reference.md)

**Azure Security Center figyelés:** Nem alkalmazható

**Felelősség:** Ügyfél

### <a name="53-deploy-automated-third-party-software-patch-management-solution"></a>5,3: a harmadik féltől származó szoftveres javításokat kezelő megoldás telepítése
***Útmutató:*** Labor-rendszergazdaként a [DevTest Labs](add-artifact-vm.md) -összetevőkkel automatizálhatja a tesztkörnyezet egyéni rendszerképeinek frissítéseit, beleértve a biztonsági javításokat és egyéb frissítéseket is. 

További információ a [DevTest Labs Image factoryről](image-factory-create.md), amely egy olyan konfigurációs programkódot használó megoldás, amely rendszeresen készít és forgalmaz képeket automatikusan az összes kívánt konfigurációval. 

Előfizetés-rendszergazdaként használhatja az Azure Update Management megoldást is a DevTest Labs-beli virtuális gépek frissítéseinek és javításának kezelésére. A Update Management a helyileg konfigurált frissítési tárházra támaszkodik a támogatott Windows-rendszerek javításához. Az olyan eszközök, mint a System Center Updates Publisher (a frissítések közzétevője) lehetővé teszik egyéni frissítések közzétételét Windows Server Update Services (WSUS) szolgáltatásban. Ez a forgatókönyv lehetővé teszi, hogy Update Management a harmadik féltől származó szoftverrel rendelkező frissítési adattárként Configuration Manager használó gépeket.

- [Update Management megoldás az Azure-ban](../automation/update-management/overview.md)
- [A virtuális gépek frissítéseinek és javításának kezelése](../automation/update-management/overview.md)

**Azure Security Center figyelés:** Nem alkalmazható

**Felelősség:** Ügyfél

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5,4: a biztonsági rések keresésének összehasonlítása
**Útmutató:** A vizsgálati eredményeket konzisztens időközönként exportálhatja, és összehasonlíthatja az eredményeket annak ellenőrzéséhez, hogy a biztonsági rések szervizelése megtörtént-e. Ha Azure Security Center által javasolt sebezhetőségi kezelési javaslatot használ, az ügyfél a kiválasztott megoldás portálján megtekintheti a korábbi vizsgálati adataikat.

**Azure Security Center figyelés:** Nem alkalmazható

**Felelősség:** Ügyfél

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5,5: kockázatértékelési folyamat használatával rangsorolhatja a felderített biztonsági rések szervizelését
**Útmutató:** Használja a Azure Security Center által biztosított alapértelmezett kockázati minősítéseket (biztonságos pontszám).

- [A Azure Security Center biztonsági pontszámának megismerése](../security-center/secure-score-security-controls.md)

**Azure Security Center figyelés:** igen

**Felelősség:** Ügyfél

## <a name="inventory-and-asset-management"></a>Leltár- és eszközfelügyelet
*További információkért lásd [: biztonsági vezérlés: leltár és eszközkezelés](../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-automated-asset-discovery-solution"></a>6,1: automatikus eszköz-felderítési megoldás használata
**Útmutató:** Az Azure Resource Graph segítségével lekérdezheti és felderítheti az összes erőforrást (beleértve az DevTest Labs-erőforrásokat is) az előfizetéseken belül. Győződjön meg arról, hogy megfelelő (olvasási) engedélyekkel rendelkezik a bérlőben, és képes enumerálni az előfizetések összes Azure-előfizetését és erőforrását.

- [Lekérdezések létrehozása az Azure Graph használatával](../governance/resource-graph/first-query-portal.md)
- [Azure-előfizetések megtekintése](/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0)
- [Az Azure RBAC ismertetése](../role-based-access-control/overview.md)

**Azure Security Center figyelés:** Nem alkalmazható

**Felelősség:** Ügyfél

### <a name="62-maintain-asset-metadata"></a>6,2: az eszköz metaadatainak fenntartása
**Útmutató:** Címkék alkalmazása az Azure-erőforrásokra, amelyek metaadatokat biztosítanak a besorolások alapján történő logikailag rendszerezve.

- [Címkék létrehozása és használata](../azure-resource-manager/management/tag-resources.md)
- [Címkék konfigurálása a DevTest Labs számára](devtest-lab-add-tag.md)

**Azure Security Center figyelés:** Nem érhető el

**Felelősség:** Ügyfél

### <a name="63-delete-unauthorized-azure-resources"></a>6,3: jogosulatlan Azure-erőforrások törlése
**Útmutató:** A laborok és a laborokkal kapcsolatos erőforrások rendszerezéséhez és nyomon követéséhez használja a címkézést, a felügyeleti csoportokat és a különálló előfizetéseket, valamint a különböző Labs-ket. Rendszeres időközönként egyeztetheti a leltárt, és gondoskodhat arról, hogy az előfizetés gyorsan törölje a jogosulatlan erőforrásokat.

- [További Azure-előfizetések létrehozása](../cost-management-billing/manage/create-subscription.md)
- [Management Groups létrehozása](../governance/management-groups/create-management-group-portal.md)
- [Labor létrehozása a DevTest Labs használatával](devtest-lab-create-lab.md)
- [Címkék létrehozása és használata](../azure-resource-manager/management/tag-resources.md)
- [Címkék konfigurálása laborhoz](devtest-lab-add-tag.md)

**Azure Security Center figyelés:** Nem alkalmazható

**Felelősség:** Ügyfél

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6,4: a jóváhagyott Azure-erőforrások leltárának meghatározása és karbantartása
**Útmutató:** Hozzon létre egy leltárt a jóváhagyott Azure-erőforrásokról és jóváhagyott szoftverekről a számítási erőforrásokhoz, szervezeti igények szerint. Előfizetés-rendszergazdaként használhat adaptív alkalmazás-vezérlőket is Azure Security Center, amelyek segítségével meghatározhatja azokat az alkalmazásokat, amelyek számára engedélyezett a laboratóriumi gépek konfigurált csoportjain való futtatás. Ez a szolgáltatás az Azure-ban és a nem Azure-beli Windowsban (minden verzió, klasszikus vagy Azure Resource Manager) és linuxos gépen is elérhető.

- [Az adaptív alkalmazások vezérlésének engedélyezése](../security-center/security-center-adaptive-application.md)
 
**Azure Security Center figyelés:** Nem alkalmazható **felelősség:** ügyfél

### <a name="65-monitor-for-unapproved-azure-resources"></a>6,5: a nem jóváhagyott Azure-erőforrások figyelése
**Útmutató:** Az Azure Policy használatával korlátozásokat állíthat be az ügyfél-előfizetésekben létrehozható erőforrások típusára a következő beépített szabályzat-definíciók használatával:

- Nem engedélyezett erőforrástípusok
- Engedélyezett erőforrástípusok

Emellett az Azure Resource Graph segítségével lekérdezheti vagy felderítheti az előfizetésen belüli erőforrásokat. Ez segíthet a magas biztonságon alapuló környezetekben, például a Storage-fiókokkal.

- [Az Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md)
- [Lekérdezések létrehozása az Azure Graph használatával](../governance/resource-graph/first-query-portal.md)

**Azure Security Center figyelés:** Nem alkalmazható

**Felelősség:** Ügyfél

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6,6: a nem jóváhagyott szoftveralkalmazások figyelése a számítási erőforrásokon belül
**Útmutató:** Azure Automation teljes körű vezérlést biztosít az üzembe helyezés, a műveletek és a számítási feladatok és erőforrások leszerelése során. Előfizetés-rendszergazdaként az Azure-beli virtuális gépek leltározásával automatizálhatja az előfizetésében található DevTest Labs-beli virtuális gépeken futó összes szoftver információinak gyűjtését. A szoftver neve, verziója, közzétevője és frissítési ideje tulajdonságok a Azure Portal érhetők el. A telepítés dátumának és egyéb információinak eléréséhez az ügyfélnek a vendég szintű diagnosztika engedélyezéséhez és a Windows-eseménynaplók Log Analytics munkaterületre való bekapcsolásához van szükség.

Amellett, hogy Change Trackingt használ a szoftveralkalmazások figyelésére, az adaptív alkalmazás-vezérlők a Azure Security Center a gépi tanulás segítségével elemzik a gépen futó alkalmazásokat, és létrehoznak egy engedélyezési listát ebből az intelligenciával. Ez a funkció nagy mértékben leegyszerűsíti az alkalmazások engedélyezési listájának házirend-szabályzatának konfigurálását és karbantartását, így elkerülhető, hogy a nemkívánatos szoftverek ne legyenek használatban a környezetben. Konfigurálhatja a naplózási módot vagy a kényszerített módot. A naplózási mód csak a védett virtuális gépeken lévő tevékenységeket naplózza. A kényszerített mód kikényszeríti a szabályokat, és ellenőrzi, hogy a nem engedélyezett alkalmazások le vannak-e tiltva. 

- [Bevezetés az Azure Automationbe](../automation/automation-intro.md)
- [Az Azure-beli virtuális gépek leltározásának engedélyezése](../automation/automation-tutorial-installed-software.md)
- [Az adaptív alkalmazások vezérlőinek ismertetése](../security-center/security-center-adaptive-application.md)

**Azure Security Center figyelés:** Nem alkalmazható

**Felelősség:** Ügyfél


### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6,7: nem jóváhagyott Azure-erőforrások és szoftveralkalmazások eltávolítása
**Útmutató:** Azure Automation teljes körű vezérlést biztosít az üzembe helyezés, a műveletek és a számítási feladatok és erőforrások leszerelése során. Előfizetés-rendszergazdaként a DevTest Labs szolgáltatásban üzemeltetett virtuális gépekre telepített összes szoftver azonosításához Change Tracking használható. A jogosulatlan szoftverek eltávolításához saját folyamatot alkalmazhat, vagy használhatja Azure Automation állapot konfigurációját.

- [Bevezetés az Azure Automationbe](../automation/automation-intro.md)
- [A környezet változásainak követése a Change Tracking megoldással](../automation/change-tracking/overview.md)
- [Azure Automation állapot konfigurációjának áttekintése](../automation/automation-dsc-overview.md)

**Azure Security Center figyelés:** Nem érhető el

**Felelősség:** Ügyfél

### <a name="68-use-only-approved-applications"></a>6,8: csak jóváhagyott alkalmazások használata
**Útmutató:** Előfizetés-rendszergazdaként Azure Security Center adaptív alkalmazás-vezérlőelemekkel biztosíthatja, hogy csak a hitelesített szoftverek fussanak, és az összes jogosulatlan szoftver le legyen tiltva a DevTest Labs szolgáltatásban üzemeltetett Azure-beli virtuális gépeken.

- [Azure Security Center adaptív alkalmazás-vezérlők használata](../security-center/security-center-adaptive-application.md)

**Azure Security Center figyelés:** igen

**Felelősség:** Ügyfél

### <a name="69-use-only-approved-azure-services"></a>6,9: csak jóváhagyott Azure-szolgáltatások használata
**Útmutató:** Az Azure Policy használatával korlátozásokat állíthat be az ügyfél-előfizetésekben létrehozható erőforrások típusára a következő beépített szabályzat-definíciók használatával: 

- Nem engedélyezett erőforrástípusok
- Engedélyezett erőforrástípusok

Lásd az alábbi cikkeket: 
- [Az Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md)
- [Adott erőforrástípus megtagadása a következővel Azure Policy](../governance/policy/samples/index.md)

**Azure Security Center figyelés:** igen

**Felelősség:** Ügyfél


### <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6,10: a jóváhagyott szoftverek leltárának fenntartása
**Útmutató:** Az adaptív alkalmazás-vezérlés intelligens, automatizált és teljes körű megoldást kínál a Azure Security Center, amely segítségével szabályozhatja, hogy mely alkalmazások futhatnak az Azure-beli és a nem Azure-beli (Windows és Linux) DevTest Labs szolgáltatásban üzemeltetett alkalmazásokkal. Megjegyzés: ezt a beállítást a DevTest Labs szolgáltatásban üzemeltetett számítási erőforrások esetében kell beállítania előfizetés-rendszergazdaként. Harmadik féltől származó megoldás implementálása, ha ez a beállítás nem felel meg a szervezet követelményének.

- [Azure Security Center adaptív alkalmazás-vezérlők használata](../security-center/security-center-adaptive-application.md)

**Azure Security Center figyelés:** Nem alkalmazható

**Felelősség:** Ügyfél

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6,11: korlátozza a felhasználók képességét a Azure Resource Manager való interakcióra
**Útmutató:** Az Azure feltételes hozzáférés használatával korlátozhatja, hogy a felhasználók képesek legyenek a Azure Resource Manager interakcióra, ha a Microsoft Azure Management alkalmazáshoz **letiltja a hozzáférés**- **vezérlést** .

- [A feltételes hozzáférés konfigurálása a Azure Resource Managerhoz való hozzáférés blokkolásához](../role-based-access-control/conditional-access-azure-management.md)

**Azure Security Center figyelés:** igen

**Felelősség:** Ügyfél

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6,12: korlátozza a felhasználók számára a parancsfájlok végrehajtásának lehetőségét a számítási erőforrásokon belül
**Útmutató:** A parancsfájlok típusától függően az operációs rendszerre jellemző konfigurációk vagy harmadik féltől származó erőforrások segítségével korlátozható, hogy a felhasználók képesek-e parancsfájlokat végrehajtani a DevTest Labs szolgáltatásban üzemeltetett virtuális gépeken belül. Azure Security Center adaptív alkalmazás-vezérlőelemekkel biztosíthatja, hogy csak a hitelesített szoftverek fussanak, és az összes jogosulatlan szoftver le legyen tiltva az alapul szolgáló Azure-beli virtuális gépeken.

- [A PowerShell-parancsfájlok végrehajtásának szabályozása Windows-környezetekben](/powershell/module/microsoft.powershell.security/set-executionpolicy?view=powershell-6)
- [Azure Security Center adaptív alkalmazás-vezérlők használata](../security-center/security-center-adaptive-application.md)

**Azure Security Center figyelés:** Nem érhető el

**Felelősség:** Ügyfél


### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6,13: fizikailag vagy logikailag elkülönítve a magas kockázatú alkalmazásokat
**Útmutató:** Az Azure-környezetben üzembe helyezett magas kockázatú alkalmazások elkülöníthetők a virtuális hálózat, az alhálózat, az előfizetések, a felügyeleti csoportok és így tovább. és megfelelően védett Azure Firewall, webalkalmazási tűzfallal (WAF) vagy hálózati biztonsági csoporttal (NSG).

- [A DevTest Labs virtuális hálózatának konfigurálása](devtest-lab-configure-vnet.md)
- [Azure Firewall áttekintése](../firewall/overview.md)
- [Webalkalmazási tűzfal – áttekintés](../web-application-firewall/overview.md)
- [Hálózati biztonság – áttekintés](../virtual-network/network-security-groups-overview.md)
- [Az Azure Virtual Network áttekintése]()
- [Erőforrások rendszerezése az Azure-beli felügyeleti csoportokkal](../governance/management-groups/overview.md)
- [Útmutató az előfizetéssel kapcsolatos döntésekhez](/azure/cloud-adoption-framework/decision-guides/subscriptions/)

**Azure Security Center figyelés:** Nem érhető el

**Felelősség:** Ügyfél

## <a name="secure-configuration"></a>Biztonságos konfiguráció
**További információkért lásd: biztonság-vezérlés: biztonságos konfiguráció.**

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7,1: biztonságos konfigurációk létrehozása az összes Azure-erőforráshoz
**Útmutató:** Azure Policy-Aliasok használatával egyéni szabályzatokat hozhat létre a DevTest Labs részeként létrehozott Azure-erőforrások naplózásához vagy érvényesítéséhez. A beépített Azure Policy-definíciókat is használhatja.

Emellett Azure Resource Manager lehetősége van a sablon exportálására JavaScript Object Notation (JSON), amelyet át kell tekinteni, hogy a konfigurációk megfeleljenek/túllépik a szervezete biztonsági követelményeit.

Az Azure-erőforrások biztonságos alapkonfigurációjának megfelelően Azure Security Center javaslatokat is alkalmazhat.

- [Az elérhető Azure Policy aliasok megtekintése](/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0)
- [Oktatóanyag: szabályzatok létrehozása és kezelése a megfelelőség kikényszerítés érdekében](../governance/policy/tutorials/create-and-manage.md)
- [Egy-és többerőforrásos exportálás Azure Portal sablonba](../azure-resource-manager/templates/export-template-portal.md)
- [Biztonsági javaslatok – gyorsútmutató](../security-center/recommendations-reference.md)

**Azure Security Center figyelés:** Nem alkalmazható

**Felelősség:** Ügyfél

### <a name="72-establish-secure-operating-system-configurations"></a>7,2: biztonságos operációsrendszer-konfigurációk létrehozása
**Útmutató:** A DevTest Labs részeként létrehozott összes mögöttes számítási erőforrás biztonsági konfigurációinak fenntartásához Azure Security Center ajánlásokat kell használnia. Emellett egyéni operációsrendszer-lemezképeket vagy Azure Automation állapot-konfigurációt vagy DevTest Labs-összetevőket is használhat a szervezete által igényelt operációs rendszer biztonsági konfigurációjának létrehozásához.

- [Azure Security Center javaslatok figyelése](../security-center/security-center-recommendations.md)
- [Biztonsági javaslatok – gyorsútmutató](../security-center/recommendations-reference.md)
- [Azure Automation állapot konfigurációjának áttekintése](../automation/automation-dsc-overview.md)
- [Virtuális merevlemez feltöltése és használata új Windows rendszerű virtuális gépek létrehozásához az Azure-ban](../virtual-machines/windows/upload-generalized-managed.md)
- [Linuxos virtuális gép létrehozása egyéni lemezről az Azure CLI-vel](../virtual-machines/linux/upload-vhd.md)
- [Egyéni lemezképek létrehozása és terjesztése több DevTest Labs-hoz](image-factory-save-distribute-custom-images.md)

**Azure Security Center figyelés:** Nem alkalmazható

**Felelősség:** Nem alkalmazható

### <a name="73-maintain-secure-azure-resource-configurations"></a>7,3: biztonságos Azure-erőforrás-konfigurációk karbantartása
**Útmutató:** **Ha nem léteznek** szabályok a DevTest Labs részeként létrehozott Azure-erőforrások biztonságos beállításainak betartatásához, használja Azure Policy **megtagadását** és telepítését. Emellett Azure Resource Manager-sablonok használatával is megőrizheti a szervezete által igényelt Azure-erőforrások biztonsági konfigurációját.

- [Azure Policy effektusok ismertetése](../governance/policy/concepts/effects.md)
- [Szabályzatok létrehozása és kezelése a megfelelőség kikényszerítése céljából](../governance/policy/tutorials/create-and-manage.md)
- [Azure Resource Manager sablonok áttekintése](../azure-resource-manager/templates/overview.md)

**Azure Security Center figyelés:** Nem alkalmazható

**Felelősség:** Ügyfél

### <a name="74-maintain-secure-operating-system-configurations"></a>7,4: az operációs rendszer biztonságos konfigurációjának fenntartása
**Útmutató:** A biztonsági rések felmérése a tesztkörnyezet részeként létrehozott, mögöttes Azure-beli számítási erőforrásokon Azure Security Center. Emellett a szervezete által igényelt operációs rendszer biztonsági konfigurációjának fenntartása érdekében Azure Resource Manager sablonokat, egyéni operációsrendszer-lemezképeket vagy Azure Automation állapot-konfigurációt is használhat. Használhatja a lemezkép-előállító megoldást is, amely egy olyan konfigurációs programkódot használó megoldás, amely rendszeresen készít és terjeszt képeket automatikusan az összes kívánt konfigurációval.

Emellett a Microsoft által közzétett Azure Marketplace virtuálisgép-rendszerképeket a Microsoft felügyeli és tartja karban.

- [Azure Security Center sebezhetőségi felméréssel kapcsolatos javaslatok megvalósítása](../security-center/deploy-vulnerability-assessment-vm.md)
- [Azure Automation állapot konfigurációjának áttekintése](../automation/automation-dsc-overview.md)
- [Példaszkript egy VHD Azure-ba történő feltöltéséhez és új virtuális gép létrehozásához](/previous-versions/azure/virtual-machines/scripts/virtual-machines-windows-powershell-upload-generalized-script)
- [Rendszerkép-előállító létrehozása a DevTest Labs szolgáltatásban](image-factory-create.md)

**Azure Security Center figyelés:** igen

**Felelősség:** Megosztott

### <a name="75-securely-store-configuration-of-azure-resources"></a>7,5: az Azure-erőforrások biztonságos tárolása
**Útmutató:** Az Azure DevOps segítségével biztonságosan tárolhatja és kezelheti a kódokat, például az egyéni Azure-szabályzatokat, Azure Resource Manager sablonokat és a kívánt állapotú konfigurációs parancsfájlokat. Az Azure DevOps felügyelt erőforrásainak eléréséhez engedélyeket adhat meg vagy tagadhat meg bizonyos felhasználók, beépített biztonsági csoportok vagy Azure Active Directory (Azure AD) által meghatározott csoportok számára az Azure DevOps-vel való integráció esetén.

- [Az Azure Repos git oktatóanyaga](/azure/devops/repos/git/gitworkflow)
- [Az engedélyek és a csoportok](/azure/devops/organizations/security/about-permissions?tabs=preview-page&view=azure-devops)
- [Integráció a Azure DevTest Labs és az Azure DevOps munkafolyamat között](devtest-lab-dev-ops.md)

**Azure Security Center figyelés:** Nem alkalmazható

**Felelősség:** Ügyfél

### <a name="76-securely-store-custom-operating-system-images"></a>7,6: az egyéni operációsrendszer-lemezképek biztonságos tárolása
**Útmutató:** Ha egyéni lemezképeket használ, az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC) használatával biztosíthatja, hogy csak a jogosult felhasználók férhessenek hozzá a lemezképekhez. A megosztott képkatalógus használatával megoszthatja a lemezképeket olyan meghatározott laborokkal, amelyeknek szükségük van rá. A tárolói lemezképek esetében tárolhatja azokat Azure Container Registry és az Azure RBAC használatával biztosíthatja, hogy csak a jogosult felhasználók férhessenek hozzá a lemezképekhez.

- [Az Azure RBAC ismertetése](../role-based-access-control/rbac-and-directory-admin-roles.md)
- [Az Azure RBAC konfigurálása](../role-based-access-control/quickstart-assign-role-user-portal.md)
- [Megosztott képgyűjtemény konfigurálása DevTest Labs számára](configure-shared-image-gallery.md)
- [Az Azure-RBAC megismerése Container Registry](../container-registry/container-registry-roles.md)

**Azure Security Center figyelés:** Nem alkalmazható

**Felelősség:** Ügyfél

### <a name="77-deploy-system-configuration-management-tools"></a>7,7: rendszerkonfiguráció-felügyeleti eszközök telepítése
**Útmutató:** Az Azure-erőforrások szabványos biztonsági konfigurációinak definiálása és implementálása Azure Policy használatával. Azure Policy-Aliasok használatával egyéni szabályzatokat hozhat létre a DevTest Labs szolgáltatásban létrehozott Azure-erőforrások hálózati konfigurációjának naplózásához vagy érvénybe léptetéséhez. Az adott erőforrásokhoz kapcsolódó beépített szabályzat-definíciókat is igénybe vehet. Emellett Azure Automation használatával is telepítheti a konfigurációs módosításokat.

- [Az Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md)
- [Aliasok használata](../governance/policy/concepts/definition-structure.md#aliases)

**Azure Security Center figyelés:** Nem alkalmazható

**Felelősség:** Ügyfél

### <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7,8: rendszerkonfiguráció-felügyeleti eszközök központi telepítése operációs rendszerekhez
**Útmutató:** Azure Automation állapot-konfiguráció a kívánt állapot-konfigurációs (DSC) csomópontok egyike a felhőben vagy a helyszíni adatközpontban. Könnyedén beépítheti a gépeket, hozzárendelheti a deklaratív konfigurációkat, és megtekintheti azokat a jelentéseket, amelyek az egyes gépek megfelelőségét a megadott kívánt állapotnak megfelelően mutatják. Olyan egyéni összetevőt is megírhat, amely az összes tesztkörnyezetben telepíthető, és így biztosíthatja, hogy azok a szervezeti házirendek követésével legyenek végrehajtva. 

- [Bevezetési gépek Azure Automation állapot-konfiguráció általi felügyelethez](../automation/automation-dsc-onboarding.md)
- [Egyéni összetevők létrehozása a DevTest Labs virtuális gépei számára](devtest-lab-artifact-author.md)

**Azure Security Center figyelés:** Nem alkalmazható

**Felelősség:** Ügyfél

### <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7,9: az Azure-szolgáltatások automatizált konfigurációs figyelésének megvalósítása
**Útmutató:** A DevTest Labs szolgáltatásban létrehozott Azure-erőforrások alapkonfigurációjának vizsgálatához használja a Azure Security Center. Emellett az Azure-erőforrások konfigurációjának riasztására és naplózására Azure Policy is használhatja.

- [Javaslatok szervizelése Azure Security Center](../security-center/security-center-remediate-recommendations.md)
 
**Azure Security Center figyelés:** Nem alkalmazható

**Felelősség:** Ügyfél

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7,10: az operációs rendszerek automatikus konfiguráció-figyelésének megvalósítása
**Útmutató:** A Azure Security Center használatával elvégezheti a tárolók operációsrendszer-és Docker-beállításainak alapkonfigurációját.

- [Az Azure Security Center tárolókra vonatkozó ajánlásainak értelmezése](../security-center/container-security.md)

**Azure Security Center figyelés:** Nem alkalmazható

**Felelősség:** Ügyfél

### <a name="711-manage-azure-secrets-securely"></a>7,11: az Azure-titkok biztonságos kezelése
**Útmutató:** A Felhőbeli alkalmazások titkos felügyeletének egyszerűbbé és biztonságossá tételéhez használja a Managed Service Identityt a Azure Key Vaultvel együtt.

- [Felügyelt identitás konfigurálása Azure Resource Manager környezetek telepítéséhez a DevTest Labs szolgáltatásban](use-managed-identities-environments.md)
- [Felügyelt identitás konfigurálása virtuális gépek üzembe helyezéséhez a DevTest Labs szolgáltatásban](enable-managed-identities-lab-vms.md)
- [Kulcstartó létrehozása](../key-vault/general/quick-create-portal.md)
- [Hitelesítés Key Vault](../key-vault/general/authentication.md)
- [Key Vault hozzáférési szabályzatok kiosztása](../key-vault/general/assign-access-policy-portal.md)

**Azure Security Center figyelés:** igen

**Felelősség:** Ügyfél

### <a name="712-manage-identities-securely-and-automatically"></a>7,12: az identitások biztonságos és automatikus kezelése
**Útmutató:** Felügyelt identitások használatával biztosíthatja az Azure-szolgáltatások automatikus felügyelt identitását az Azure AD-ben. A felügyelt identitások lehetővé teszik bármely olyan szolgáltatás hitelesítését, amely támogatja az Azure AD-hitelesítést, beleértve a Key Vault is, a kódban szereplő hitelesítő adatok nélkül.

- [Felügyelt identitás konfigurálása Azure Resource Manager környezetek telepítéséhez a DevTest Labs szolgáltatásban](use-managed-identities-environments.md)
- [Felügyelt identitás konfigurálása virtuális gépek üzembe helyezéséhez a DevTest Labs szolgáltatásban](enable-managed-identities-lab-vms.md)
 
**Azure Security Center figyelés:** igen

**Felelősség:** Ügyfél

### <a name="713-eliminate-unintended-credential-exposure"></a>7,13: a hitelesítő adatok nem szándékolt expozíciójának megszüntetése
**Útmutató:** Hitelesítő adatok beolvasása a kódban a hitelesítő adatok azonosításához. A Credential Scanner a felfedezett hitelesítő adatok biztonságosabb helyre, például az Azure Key Vaultba való áthelyezésére is javaslatot tesz.

- A hitelesítő adatok beolvasójának beállítása

**Azure Security Center figyelés:** Nem alkalmazható

**Felelősség:** Ügyfél


## <a name="malware-defense"></a>Kártevők elleni védelem
*További információkért lásd: biztonsági ellenőrzés: kártevők elleni védelem.*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8,1: központilag felügyelt kártevő szoftverek használata
**Útmutató:** A Microsoft antimalware for Azure Cloud Services és Virtual Machines segítségével folyamatosan figyelheti és védheti az erőforrásait. Linux esetén használjon harmadik féltől származó antimalware-megoldást. Emellett a Azure Security Center veszélyforrások észlelését is használhatja az adatszolgáltatásokhoz a Storage-fiókokba feltöltött kártevők észlelése érdekében.

- A Microsoft antimalware konfigurálása Cloud Services és Virtual Machines
- Fenyegetésvédelem az Azure Security Centerben

**Azure Security Center figyelés:** igen

**Felelősség:** Ügyfél

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8,2: a nem számítási Azure-erőforrásokra feltöltött fájlok előzetes vizsgálata
**Útmutató:** A Microsoft antimalware engedélyezve van azon a mögöttes gazdagépen, amely támogatja az Azure-szolgáltatásokat (például egy tesztkörnyezetben üzemeltetett Azure App Service), de nem fut a tartalomon.
A nem számítási Azure-erőforrásokra feltöltött fájlok előzetes vizsgálata, például App Service, Data Lake Storage, Blob Storage stb.

A Storage-fiókokba feltöltött kártevők észleléséhez használja a Azure Security Center veszélyforrások észlelését az adatszolgáltatásokhoz.

- Megismerheti a Microsoft antimalware-t az Azure Cloud Services és Virtual Machines
- Az adatszolgáltatások fenyegetés-észlelésének megismerése Azure Security Center

**Azure Security Center figyelés:** igen

**Felelősség:** Nem alkalmazható


### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8,3: a kártevő szoftverek és az aláírások frissítésének ellenőrzése
**Útmutató:** Telepítésekor az Azure-hoz készült Microsoft antimalware alapértelmezés szerint automatikusan telepíti a legújabb aláírást, platformot és motor-frissítéseket. Kövesse az Azure Security Center: "számítás & alkalmazások" című témakörben található javaslatokat, hogy a DevTest Labs mögöttes számítási erőforrások összes végpontja naprakész legyen a legújabb aláírásokkal. A Windows operációs rendszer további biztonsággal biztosítható, hogy a Microsoft Defender komplex veszélyforrások elleni védelmi szolgáltatásával, amely integrálható a Azure Security Centersal, a vírus-vagy kártevő-alapú támadások kockázatát korlátozza.

- A Microsoft antimalware telepítése Azure Cloud Services és Virtual Machines
- Microsoft Defender Advanced Threat Protection

**Azure Security Center figyelés:** igen

**Felelősség:** Ügyfél

## <a name="data-recovery"></a>Adat-helyreállítás
*További információkért lásd [: biztonsági ellenőrzés: adat-helyreállítás](../security/benchmarks/security-control-data-recovery.md).*

### <a name="91-ensure-regular-automated-back-ups"></a>9,1: rendszeres automatizált biztonsági másolatok biztosítása
**Útmutató:** A Azure DevTest Labs jelenleg nem támogatja a virtuális gépek biztonsági mentését és pillanatképeit. A DevTest Labs szolgáltatásban üzemeltetett Azure-beli virtuális gépeken azonban Azure Backup is engedélyezheti és konfigurálhatja. Emellett az automatikus biztonsági mentések esetében is konfigurálhatja a kívánt gyakoriságot és megőrzési időt, ha megfelelő hozzáféréssel rendelkezik a mögöttes számítási erőforrásokhoz. 

- [Az Azure virtuális gépek biztonsági mentésének áttekintése](../backup/backup-azure-vms-introduction.md)
- [Azure-beli virtuális gép biztonsági mentése a virtuális gép beállításaiból](../backup/backup-azure-vms-first-look-arm.md)

**Azure Security Center figyelés:** igen

**Felelősség:** Ügyfél

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: teljes rendszerbiztonsági mentés és minden ügyfél által felügyelt kulcs biztonsági mentése
**Útmutató:** A Azure DevTest Labs jelenleg nem támogatja a virtuális gépek biztonsági mentését és pillanatképeit. A DevTest Labs szolgáltatásban üzemeltetett mögöttes Azure-beli virtuális gépekről, illetve az ezekhez a példányokhoz csatolt felügyelt lemezekről a PowerShell vagy a REST API-k használatával készíthet pillanatképeket, feltéve, hogy megfelelő hozzáféréssel rendelkezik a mögöttes számítási erőforrásokhoz. A Azure Key Vaulton belüli ügyfelek által felügyelt kulcsok biztonsági mentését is elvégezheti.

Engedélyezze Azure Backup a célként megadott Azure-beli virtuális gépeken, valamint a kívánt gyakorisággal és megőrzési időtartamokkal. Magában foglalja a rendszerállapot teljes biztonsági mentését. Ha az Azure Disk Encryption szolgáltatást használja, az Azure virtuális gép biztonsági mentése automatikusan kezeli az ügyfél által felügyelt kulcsok biztonsági mentését.

- [Biztonsági mentés a titkosítást használó Azure-beli virtuális gépekről](../backup/backup-azure-vms-encryption.md)
- [Az Azure virtuális gépek biztonsági mentésének áttekintése](../backup/backup-azure-vms-introduction.md)
- [Az Azure virtuális gépek biztonsági mentésének áttekintése](../backup/backup-azure-vms-introduction.md)
- [Key Vault kulcsok biztonsági mentése az Azure-ban](/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?view=azurermps-6.13.0)

**Azure Security Center figyelés:** igen

**Felelősség:** Ügyfél

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: az összes biztonsági másolat ellenőrzése, beleértve az ügyfél által felügyelt kulcsokat
**Útmutató:** Győződjön meg arról, hogy a Azure Backupon belül rendszeresen végezhető el az adatok visszaállítása a tartalomban. Ha szükséges, tesztelje a tartalom visszaállítását egy elkülönített virtuális hálózatra vagy előfizetésre. Emellett tesztelheti az ügyfél által felügyelt kulcsok biztonsági mentésének visszaállítását is.

Ha az Azure Disk Encryption szolgáltatást használja, visszaállíthatja az Azure-beli virtuális gépet a lemez titkosítási kulcsaival. A lemezes titkosítás használata esetén visszaállíthatja az Azure-beli virtuális gépet a lemez titkosítási kulcsaival.

- [Biztonsági mentés a titkosítást használó Azure-beli virtuális gépekről](../backup/backup-azure-vms-encryption.md)
- [Fájlok helyreállítása az Azure-beli virtuális gépek biztonsági másolatából](../backup/backup-azure-restore-files-from-vm.md)
- [Key Vault-kulcsok visszaállítása az Azure-ban](/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0)
- [Titkosított virtuális gépek biztonsági mentése és visszaállítása](../backup/backup-azure-vms-encryption.md)

**Azure Security Center figyelés:** Nem alkalmazható

**Felelősség:** Ügyfél

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: a biztonsági másolatok és az ügyfél által felügyelt kulcsok védelmének biztosítása
**Útmutató:** A felügyelt lemezek Azure Backup-val történő biztonsági mentésekor a virtuális gépek Storage Service Encryption (SSE) inaktív állapotban vannak titkosítva. A Azure Backup a Azure Disk Encryption használatával titkosított Azure-beli virtuális gépek biztonsági mentését is elvégezheti. A Azure Disk Encryption a BitLocker titkosítási kulcsaival (BEKs) integrálható, amelyek titkos kulccsal rendelkeznek a Key vaultban. A Azure Disk Encryption Azure Key Vault kulcs-titkosítási kulcsokkal (KEK) is integrálva van. A kulcsok véletlen vagy rosszindulatú törléssel szembeni védelemmel való ellátásához engedélyezze a Key Vault Soft-Delete.

- [Soft Delete a virtuális gépekhez](../backup/soft-delete-virtual-machines.md)
- [Azure Key Vault – nem kötelező törlés – áttekintés](../key-vault/general/soft-delete-overview.md)

**Azure Security Center figyelés:** igen

**Felelősség:** Ügyfél

## <a name="incident-response"></a>Incidensmegoldás
*További információ [: Security Control: incidens válasza](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10,1: incidens-válaszi útmutató létrehozása
**Útmutató:** Hozzon létre egy incidensre vonatkozó választ a szervezet számára. Győződjön meg arról, hogy van olyan írásos incidens-válasz, amely meghatározza az incidensek kezelésének és kezelésének az észleléstől az incidens utáni felülvizsgálatig való összes szerepkörét.

- [Útmutató a saját biztonsági incidensek megoldási folyamatának létrehozásához](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)
- [Microsoft Security Response Center – incidens anatómiája](https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/)
- [A NIST számítógépes biztonsági incidensek kezelésével kapcsolatos útmutató a saját incidens-válasz tervének létrehozásához](https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final)

**Azure Security Center figyelés:** Nem alkalmazható

**Felelősség:** Ügyfél

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10,2: incidensek pontozásának és rangsorolási eljárásának létrehozása
**Útmutató:** A Azure Security Center az egyes riasztásokhoz súlyosságot rendel, így rangsorolhatja, hogy a rendszer mely riasztásokat vizsgálja meg először. A súlyosság azon alapul, hogy az Security Center milyen mértékben szerepel a riasztás kibocsátásához használt elemzésben vagy elemzésben, valamint a riasztást eredményező tevékenység mögötti rosszindulatú szándékkal.

Emellett egyértelműen megjelölheti az előfizetéseket (pl.: éles környezetben, nem gyártva) címkék használatával és elnevezési rendszer létrehozása az Azure-erőforrások egyértelmű azonosításához és kategorizálásához, különösen a bizalmas adatok feldolgozásához. Az Ön felelőssége, hogy rangsorolja a riasztások megoldását azon Azure-erőforrások és -környezetek kritikussága alapján, ahol az incidens történt.

- [Biztonsági riasztások az Azure Security Centerben](../security-center/security-center-alerts-overview.md)
- [Címkék használata az Azure-erőforrások rendszerezéséhez](../azure-resource-manager/management/tag-resources.md)

**Azure Security Center figyelés:** igen

**Felelősség:** Ügyfél

### <a name="103-test-security-response-procedures"></a>10,3: biztonsági reagálási eljárások tesztelése
**Útmutató:** Az Azure-erőforrások védelmének biztosítása érdekében a rendszer az incidensek reagálási képességeinek rendszeres tesztelésére szolgáló gyakorlatokat hajt végre. Azonosítsa a gyenge pontokat és réseket, és szükség esetén dolgozza át a tervet.

- [A NIST kiadványa – útmutató az IT-csomagok és-képességek teszteléséhez, betanításához és alkalmazásához](https://csrc.nist.gov/publications/detail/sp/800-84/final)

**Azure Security Center figyelés:** Nem alkalmazható

**Felelősség:** Ügyfél

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10,4: biztonsági incidensek elérhetőségének biztosítása és riasztási értesítések konfigurálása biztonsági incidensekhez
**Útmutató:** A Microsoft a biztonsági incidensek elérhetőségi adatait arra használja fel, hogy felvegye Önnel a kapcsolatot, ha a Microsoft Security Response Center (MSRC) felfedi, hogy az adatokat egy törvénytelen vagy jogosulatlan fél is hozzáférte. A problémák megoldása érdekében tekintse át az incidenseket a tény után.

- [A Azure Security Center biztonsági kapcsolattartó beállítása](../security-center/security-center-provide-security-contact-details.md)

**Azure Security Center figyelés:** Nem alkalmazható

**Felelősség:** Ügyfél

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10,5: biztonsági riasztások beépítése az incidensek gyorsreagáló rendszerébe
**Útmutató:** Az Azure Security Center-riasztások és-javaslatok exportálásával a folyamatos exportálás funkcióval azonosíthatja az Azure-erőforrásokkal kapcsolatos kockázatokat. A folyamatos exportálás lehetővé teszi a riasztások és javaslatok manuális és folyamatos exportálását. Az Azure Security Center adatösszekötővel továbbíthatja a riasztásokat az Azure Sentinel szolgáltatásba.

- [Folyamatos exportálás konfigurálása](../security-center/continuous-export.md)
- [Riasztások streamelése az Azure Sentinelbe](../sentinel/connect-azure-security-center.md)

**Azure Security Center figyelés:** Nem alkalmazható

**Felelősség:** Ügyfél

#### <a name="106-automate-the-response-to-security-alerts"></a>10,6: a biztonsági riasztásokra adott válasz automatizálása
**Útmutató:** A Azure Security Center munkafolyamat-automatizálási funkciója segítségével automatikusan aktiválhatja a válaszokat az "Logic Apps" használatával a biztonsági riasztások és az Azure-erőforrások védelme érdekében javasolt javaslatok alapján.

- [A Munkafolyamat-automatizálás és a Logic Apps konfigurálása](../security-center/workflow-automation.md)
 
Azure Security Center figyelés: * * * * nem alkalmazható

**Felelősség:** Ügyfél


## <a name="penetration-tests-and-red-team-exercises"></a>Behatolási tesztek és Red Team-gyakorlatok
*További információkért lásd: biztonsági ellenőrzés: [behatolási tesztek és Red Team-gyakorlatok](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md).*


### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings-within-60-days"></a>11,1: rendszeres penetrációs tesztelést végez az Azure-erőforrásokon, és gondoskodik az összes kritikus biztonsági vizsgálat 60 napon belüli szervizeléséről
**Útmutató:** Kövesse a Microsoft részvételi szabályait, hogy a behatolási tesztek ne sértsék a Microsoft-házirendeket. A Microsoft által felügyelt felhőalapú infrastruktúrán, szolgáltatásokon és alkalmazásokon végzett riasztási és élő behatolási tesztek végrehajtásához használja a Microsoft stratégiáját és végrehajtási tervét.

- [Behatolás-tesztelési szabályok engagement](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)
- [Microsoft Cloud Red-összevonás](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Azure Security Center figyelés:** Nem alkalmazható

**Felelősség:** Megosztott

## <a name="next-steps"></a>Következő lépések
Tekintse meg a következő cikket:

- [Biztonsági riasztások a Azure DevTest Labs környezetekben](environment-security-alerts.md)