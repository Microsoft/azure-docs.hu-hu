---
title: Data Lake Analytics Azure biztonsági alapterve
description: A Data Lake Analytics biztonsági alapterve az Azure biztonsági Teljesítménytesztben meghatározott biztonsági javaslatok megvalósítására szolgáló eljárási útmutatást és erőforrásokat biztosít.
author: msmbaldwin
ms.service: data-lake-analytics
ms.topic: conceptual
ms.date: 07/22/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 48df40d6f1e3030435a7ac1236d3dcda298920ba
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/17/2021
ms.locfileid: "100596915"
---
# <a name="azure-security-baseline-for-data-lake-analytics"></a>Data Lake Analytics Azure biztonsági alapterve

Az Data Lake Analytics Azure biztonsági alapkonfigurációja olyan javaslatokat tartalmaz, amelyek segítségével javíthatja az üzemelő példány biztonsági állapotát.

A szolgáltatás alapterve az [Azure Security Benchmark 1,0-es verziójából](../security/benchmarks/overview.md)származik, amely javaslatokat tesz arra vonatkozóan, hogy miként védheti meg felhőalapú megoldásait az Azure-ban az ajánlott eljárásokat ismertető útmutató segítségével.

További információ: [Azure Security](../security/benchmarks/security-baselines-overview.md)alapkonfigurációk áttekintése.

## <a name="network-security"></a>Hálózati biztonság

*További információkért lásd [: biztonsági ellenőrzés: hálózati biztonság](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1,1: Azure-erőforrások biztosítása virtuális hálózatokon belül

**Útmutató**: Data Lake Analytics a tűzfalbeállítások használatával korlátozza a külső IP-tartományokat, hogy engedélyezze a hozzáférést a helyi ügyfelektől és a harmadik féltől származó szolgáltatásokhoz. A tűzfalbeállítások konfigurálása a portálon, a REST API-kon vagy a PowerShellen keresztül érhető el.

* [Tűzfalszabályok](/rest/api/datalakeanalytics/firewallrules)

* [Az Azure Data Lake Analytics kezelése az Azure PowerShell-lel](./data-lake-analytics-manage-use-powershell.md)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-network-interfaces"></a>1,2: a virtuális hálózatok, alhálózatok és hálózati adapterek konfigurációjának és forgalmának figyelése és naplózása

**Útmutató**: nem alkalmazható; Azure Data Lake Analytics nem fut virtuális hálózatban, és az összevont lekérdezések használatakor a kimenő hívások nem konfigurálhatók az ügyfél virtuális hálózatán keresztüli útvonalra.

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: nem alkalmazható

### <a name="13-protect-critical-web-applications"></a>1,3: a kritikus webalkalmazások megóvása

**Útmutató**: nem alkalmazható; Ez a vezérlő Azure App Service-vagy IaaS-példányokon futó webalkalmazásokhoz készült.

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: nem alkalmazható

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1,4: az ismert kártevő IP-címekkel folytatott kommunikáció megtagadása

**Útmutató**: Data Lake Analytics a tűzfalbeállítások használatával korlátozza a külső IP-tartományokat, hogy engedélyezze a hozzáférést a helyi ügyfelektől és a harmadik féltől származó szolgáltatásokhoz. A tűzfalbeállítások konfigurálása a portálon, a REST API-kon vagy a PowerShellen keresztül érhető el.

* [Tűzfalszabályok](/rest/api/datalakeanalytics/firewallrules)

* [Az Azure Data Lake Analytics kezelése az Azure PowerShell-lel](./data-lake-analytics-manage-use-powershell.md)

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="15-record-network-packets"></a>1,5: hálózati csomagok rögzítése

**Útmutató**: nem alkalmazható; A Data Lake Analytics nem fut az ügyfél virtuális hálózatán, és nem használhat hálózati biztonsági csoportokat (NSG-ket) a hálózati folyamat naplófájljainak rögzítéséhez.

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: nem alkalmazható

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1,6: hálózati behatolás-észlelési/Behatolás-megelőzési rendszerek (AZONOSÍTÓk/IP-címek) üzembe helyezése

**Útmutató**: nem alkalmazható; Data Lake Analytics egy olyan Pásti-ajánlat, amely nem az ügyfél-hálózatokra kerül üzembe.

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: nem alkalmazható

### <a name="17-manage-traffic-to-web-applications"></a>1,7: webalkalmazások forgalmának kezelése

**Útmutató**: nem alkalmazható; Ez a javaslat Azure App Service vagy számítási erőforrásokon futó webalkalmazásokhoz készült.

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: nem alkalmazható

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1,8: a hálózati biztonsági szabályok bonyolultságának és adminisztratív terhelésének csökkentése

**Útmutató**: nem alkalmazható; Data Lake Analytics nem fut az ügyfél virtuális hálózatán, és nem használhat hálózati biztonsági csoportokat (NSG).

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: nem alkalmazható

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1,9: a hálózati eszközök szabványos biztonsági konfigurációinak fenntartása

**Útmutató**: nem alkalmazható; Data Lake Analytics nem fut az ügyfél virtuális hálózatán, és nem használhat hálózati biztonsági csoportokat (NSG).

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: nem alkalmazható

### <a name="110-document-traffic-configuration-rules"></a>1,10: a dokumentum forgalmának konfigurációs szabályai

**Útmutató**: nem alkalmazható; Data Lake Analytics nem fut az ügyfél virtuális hálózatán, és nem használhat hálózati biztonsági csoportokat (NSG).

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: nem alkalmazható

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1,11: automatikus eszközök használata a hálózati erőforrások konfigurációjának figyelésére és a változások észlelésére

**Útmutató**: nem alkalmazható; Data Lake Analytics nem fut az ügyfél virtuális hálózatán, és nem használhat hálózati biztonsági csoportokat (NSG).

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: nem alkalmazható

## <a name="logging-and-monitoring"></a>Naplózás és figyelés

*További információ [: Security Control: naplózás és figyelés](../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="21-use-approved-time-synchronization-sources"></a>2,1: a jóváhagyott idő-szinkronizálási források használata

**Útmutató**: nem alkalmazható; A Microsoft megőrzi a Data Lake Analytics időforrását.

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Microsoft

### <a name="22-configure-central-security-log-management"></a>2,2: a központi biztonsági naplók felügyeletének konfigurálása

**Útmutató**: naplók beolvasása Azure monitor használatával az olyan biztonsági adatokat összesítve, mint a Data Lake Analytics "audit" és a "requests" diagnosztika. A Azure Monitoron belül egy Log Analytics munkaterülettel kérdezheti le és végezheti el az elemzéseket, és használhatja az Azure Storage-fiókokat a hosszú távú/archiválási tároláshoz, opcionálisan olyan biztonsági funkciókkal, mint a megváltoztathatatlan tárolás és a kényszerített megőrzés.

Alternatív megoldásként engedélyezheti és elvégezheti az Azure Sentinel vagy egy harmadik féltől származó SIEM-et.

* [Az Azure Data Lake Analytics diagnosztikai naplóinak elérése](./data-lake-analytics-diagnostic-logs.md)

* [Az Azure Sentinel előkészítése](../sentinel/quickstart-onboard.md)

* [Platform-naplók és-metrikák összegyűjtése Azure Monitor](../azure-monitor/essentials/diagnostic-settings.md)

* [Az Azure-beli virtuális gépek belső gazdagép-naplóinak összegyűjtése Azure Monitor](../azure-monitor/vm/quick-collect-azurevm.md)

* [Ismerkedés a Azure Monitor és a harmadik féltől származó SIEM-integrációval](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/)

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="23-enable-audit-logging-for-azure-resources"></a>2,3: az Azure-erőforrások naplózásának engedélyezése

**Útmutató**: a Data Lake Analytics diagnosztikai beállításainak engedélyezése a naplózási és a kérelmek naplóihoz való hozzáféréshez. Ilyenek például az eseményforrás, a dátum, a felhasználó, az időbélyeg és más hasznos elemek.

* [Platform-naplók és-metrikák összegyűjtése Azure Monitor](../azure-monitor/essentials/diagnostic-settings.md)

* [A naplózás és a különböző naplózási típusok megismerése az Azure-ban](../azure-monitor/essentials/platform-logs-overview.md)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="24-collect-security-logs-from-operating-systems"></a>2,4: biztonsági naplók gyűjtése az operációs rendszerekből

**Útmutató**: nem alkalmazható; Ez a javaslat számítási erőforrások számára készült.

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: nem alkalmazható

### <a name="25-configure-security-log-storage-retention"></a>2,5: a biztonsági napló tárolási adatmegőrzésének konfigurálása

**Útmutató**: Azure monitor a szervezet megfelelőségi szabályainak megfelelően állítsa be a log Analytics munkaterület megőrzési időszakát. Azure Storage-fiókokat használhat hosszú távú és archiválási tároláshoz.

* [Az adatmegőrzési időszak módosítása Log Analytics](../azure-monitor/logs/manage-cost-storage.md#change-the-data-retention-period)

* [Adatmegőrzési szabályzat konfigurálása az Azure Storage-fiók naplóihoz](../storage/common/storage-monitor-storage-account.md#configure-logging)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="26-monitor-and-review-logs"></a>2,6: naplók figyelése és áttekintése

**Útmutató**: a rendellenes viselkedésre vonatkozó naplók elemzése és figyelése, valamint a Data Lake Analytics erőforrások eredményeinek rendszeres áttekintése. Az Azure Monitor Log Analytics munkaterülete segítségével tekintheti át a naplókat, és lekérdezéseket végezhet a naplózási adatokon. Alternatív megoldásként engedélyezheti és elvégezheti az Azure Sentinel vagy egy harmadik fél SIEM-nek.

* [Az Azure Sentinel előkészítése](../sentinel/quickstart-onboard.md)

* [További információ a Log Analytics munkaterületről](../azure-monitor/logs/log-analytics-tutorial.md)

* [Egyéni lekérdezések végrehajtása a Azure Monitorban](../azure-monitor/logs/get-started-queries.md)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="27-enable-alerts-for-anomalous-activities"></a>2,7: riasztások engedélyezése rendellenes tevékenységekhez

**Útmutató**: a Data Lake Analytics diagnosztikai beállításainak engedélyezése és naplók elküldése egy log Analytics-munkaterületre. A Log Analytics-munkaterületet az Azure Sentinelbe irányíthatja, mivel ez egy biztonsági előkészítési automatizált választ (felszárnyaló) megoldást biztosít. Ez lehetővé teszi a forgatókönyvek (automatizált megoldások) létrehozását és a biztonsági problémák megoldására való felhasználását.

* [Az Azure Sentinel előkészítése](../sentinel/quickstart-onboard.md)

* [Riasztás a log Analytics-naplófájlok adatkezeléséről](../azure-monitor/alerts/tutorial-response.md)

* [Az Azure Data Lake Analytics diagnosztikai naplóinak elérése](./data-lake-analytics-diagnostic-logs.md)

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="28-centralize-anti-malware-logging"></a>2,8: kártevő szoftverek közötti naplózás központosítása

**Útmutató**: nem alkalmazható; Data Lake Analytics nem dolgoz fel kártevő szoftverrel kapcsolatos naplókat, illetve nem hoz létre.

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: nem alkalmazható

### <a name="29-enable-dns-query-logging"></a>2,9: DNS-lekérdezések naplózásának engedélyezése

**Útmutató**: harmadik féltől származó megoldás megvalósítása az Azure Marketplace-ről a DNS-naplózási megoldáshoz, amelyet a szervezeteknek szüksége van.

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: nem alkalmazható

### <a name="210-enable-command-line-audit-logging"></a>2,10: parancssori naplózás engedélyezése

**Útmutató**: nem alkalmazható; Ez a vezérlő olyan számítási erőforrások elérésére szolgál, amelyekhez az ügyfél hozzáfér a mögöttes operációs rendszerhez.

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: nem alkalmazható

## <a name="identity-and-access-control"></a>Identitás- és hozzáférés-vezérlés

*További információkért lásd: a [biztonság szabályozása: identitás-és hozzáférés-vezérlés](../security/benchmarks/security-control-identity-access-control.md).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3,1: a felügyeleti fiókok leltárának karbantartása

**Útmutató**: az Azure ad olyan beépített szerepkörökkel rendelkezik, amelyeket explicit módon kell hozzárendelni, és lekérdezhető. Az Azure AD PowerShell-modullal ad hoc lekérdezéseket hajthat végre a felügyeleti csoportok tagjait futtató fiókok felderítéséhez.

* [Címtárbeli szerepkör beszerzése az Azure AD-ben a PowerShell-lel](/powershell/module/azuread/get-azureaddirectoryrole)

* [Címtárbeli szerepkör tagjainak beszerzése az Azure AD-ben a PowerShell-lel](/powershell/module/azuread/get-azureaddirectoryrolemember)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="32-change-default-passwords-where-applicable"></a>3,2: az alapértelmezett jelszavak módosítása, ha alkalmazható

**Útmutató**: a Data Lake Analytics nem rendelkezik az alapértelmezett jelszavak fogalmával, mivel a hitelesítés Azure Active Directory és az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC) által biztosított.

* [Azure Data Lake Analytics áttekintése](./data-lake-analytics-overview.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="33-use-dedicated-administrative-accounts"></a>3,3: dedikált rendszergazdai fiókok használata

**Útmutató**: szabványos üzemeltetési eljárások létrehozása a dedikált rendszergazdai fiókok használata körül.

Azure AD Privileged Identity Management és Azure Resource Manager használatával is engedélyezheti az igény szerinti hozzáférést.

* [További információ a Privileged Identity Management](../active-directory/privileged-identity-management/index.yml)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="34-use-azure-active-directory-single-sign-on-sso"></a>3,4: Azure Active Directory egyszeri bejelentkezés (SSO) használata

**Útmutató**: ha lehetséges, használjon Azure Active Directory SSO-t a különálló önálló hitelesítő adatok konfigurálása helyett. Azure Security Center identitás és hozzáférési javaslatok használata.

* [Az egyszeri bejelentkezés ismertetése az Azure AD-vel](../active-directory/manage-apps/what-is-single-sign-on.md)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3,5: Multi-Factor Authentication használata az összes Azure Active Directory-alapú hozzáféréshez

**Útmutató**: engedélyezze Azure Active Directory multi-Factor Authentication (MFA) használatát, és kövesse Azure Security Center identitás-és hozzáférés-kezelési javaslatait a Data Lake Analytics erőforrások védelméhez.

* [MFA engedélyezése az Azure-ban](../active-directory/authentication/howto-mfa-getstarted.md)

* [Identitás és hozzáférés figyelése Azure Security Centeron belül](../security-center/security-center-identity-access.md)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="36-use-secure-azure-managed-workstations-for-administrative-tasks"></a>3,6: biztonságos, Azure által felügyelt munkaállomások használata felügyeleti feladatokhoz

**Útmutató**: emelt szintű jogosultságokat igénylő felügyeleti feladatokhoz használjon biztonságos, Azure által felügyelt munkaállomás (más néven privilegizált hozzáférési munkaállomás vagy Paw) használatát.

* [A biztonságos, Azure által felügyelt munkaállomások ismertetése](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

* [Az Azure AD MFA engedélyezése](../active-directory/authentication/howto-mfa-getstarted.md)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3,7: naplózás és riasztás a gyanús tevékenységekről a rendszergazdai fiókoktól

**Útmutató**: Azure Active Directory biztonsági jelentések használata naplók és riasztások generálásához, ha a környezetben gyanús vagy nem biztonságos tevékenység történik. A Azure Security Center használatával figyelheti az identitás-és hozzáférési tevékenységeket.

* [A kockázatos tevékenységek miatt megjelölt Azure AD-felhasználók azonosítása](../active-directory/identity-protection/overview-identity-protection.md)

* [A felhasználók identitási és hozzáférési tevékenységeinek monitorozása az Azure Security Centerben](../security-center/security-center-identity-access.md)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3,8: az Azure-erőforrások kezelése csak jóváhagyott helyekről

**Útmutató**: az Azure ad nevesített helyeinek használata az IP-címtartományok vagy országok/régiók adott logikai csoportjaihoz való hozzáférés engedélyezéséhez.

* [Az Azure AD nevesített helyeinek konfigurálása](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="39-use-azure-active-directory"></a>3,9: a Azure Active Directory használata

**Útmutató**: a Azure Active Directory (Azure ad) használata központi hitelesítési és engedélyezési rendszerrel. Az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC) részletesen szabályozza az ügyfelek hozzáférését Data Lake Analytics erőforrásokhoz.

* [Azure AD-példány létrehozása és konfigurálása](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="310-regularly-review-and-reconcile-user-access"></a>3,10: a felhasználói hozzáférés rendszeres áttekintése és egyeztetése

**Útmutató**: az Azure ad olyan naplókat biztosít, amelyek segítenek az elavult fiókok felderítésében. Emellett az Azure AD identitás-és hozzáférési felülvizsgálatok segítségével hatékonyan kezelheti a csoporttagságok kezelését, a vállalati alkalmazásokhoz való hozzáférést és a szerepkör-hozzárendeléseket. A felhasználói hozzáférés rendszeresen felülvizsgálható, hogy csak a megfelelő felhasználók férhessenek hozzá.

* [Az Azure AD jelentéskészítés ismertetése](../active-directory/reports-monitoring/index.yml)

* [Az Azure AD identitás- és hozzáférési felülvizsgálatainak használata](../active-directory/governance/access-reviews-overview.md)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3,11: a figyelő megkísérli a deaktivált hitelesítő adatok elérését

**Útmutató**: a Data Lake Analytics és Azure Active Directory diagnosztikai beállításainak engedélyezése, az összes napló küldése egy log Analytics munkaterületre. A kívánt riasztások (például a letiltott titkok elérésére tett kísérletek) konfigurálása Log Analyticson belül.

* [Azure AD-naplók integrálása Azure Monitor naplókkal](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3,12: riasztás a fiók bejelentkezési viselkedésének eltérése esetén

**Útmutató**: a Azure Active Directory kockázatkezelési és identitás-védelmi funkcióinak használatával konfigurálhatja az Data Lake Analytics erőforrásaival kapcsolatos gyanús műveletekre vonatkozó automatizált válaszokat. A szervezet biztonsági válaszainak megvalósításához engedélyeznie kell az automatikus válaszokat az Azure Sentinel használatával.

* [Az Azure AD kockázatos bejelentkezéseinek megtekintése](../active-directory/identity-protection/overview-identity-protection.md)

* [Az Identity Protection kockázati házirendjeinek konfigurálása és engedélyezése](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

* [Az Azure Sentinel előkészítése](../sentinel/quickstart-onboard.md)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3,13: a Microsoft számára elérhetővé teszi a megfelelő ügyféladatokat a támogatási forgatókönyvek során

**Útmutató**: nem alkalmazható; A Ügyfélszéf Azure Data Lake Analytics esetében nem támogatott.

* [Támogatott szolgáltatások és forgatókönyvek általánosan elérhetővé](../security/fundamentals/customer-lockbox-overview.md#supported-services-and-scenarios-in-general-availability)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: nem alkalmazható

## <a name="data-protection"></a>Adatvédelem

*További információkért lásd [: biztonsági ellenőrzés:](../security/benchmarks/security-control-data-protection.md)adatvédelem.*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4,1: bizalmas információk leltárának fenntartása

**Útmutató**: a címkék használatával segítheti a bizalmas adatokat tároló vagy feldolgozó Data Lake Analytics erőforrások nyomon követését.

* [Címkék létrehozása és használata](../azure-resource-manager/management/tag-resources.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4,2: bizalmas adatok tárolására vagy feldolgozására szolgáló rendszerek elkülönítése

**Útmutató**: elkülönítés implementálása különálló előfizetések használatával, különálló biztonsági tartományok felügyeleti csoportjai, például a környezet, az adatérzékenység. A Data Lake Analytics korlátozásával szabályozhatja az alkalmazások és a vállalati környezetek igény szerinti Data Lake Analytics erőforrásaihoz való hozzáférés szintjét. A tűzfalszabályok konfigurálásakor csak a megadott hálózatokon keresztül adatokat kérő alkalmazások férhetnek hozzá a Data Lake Analytics erőforrásaihoz. Az Azure RBAC segítségével szabályozhatja Azure Data Lake Analytics hozzáférését.

* [További Azure-előfizetések létrehozása](../cost-management-billing/manage/create-subscription.md)

* [Management Groups létrehozása](../governance/management-groups/create-management-group-portal.md)

* [Címkék létrehozása és használata](../azure-resource-manager/management/tag-resources.md)

* [Azure szerepköralapú hozzáférés-vezérlés kezelése](./data-lake-analytics-manage-use-portal.md#manage-azure-role-based-access-control)

* [Tűzfalszabályok](/rest/api/datalakeanalytics/firewallrules)

* [Az Azure Data Lake Analytics kezelése az Azure PowerShell-lel](./data-lake-analytics-manage-use-powershell.md)

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4,3: a bizalmas adatok jogosulatlan átvitelének figyelése és letiltása

**Útmutató**: az adatvesztés-megelőzési funkciók még nem érhetők el Azure Data Lake Analytics erőforrásokhoz. Külső gyártótól származó megoldás implementálása, ha az szükséges a megfelelőség szempontjából.

A Microsoft által felügyelt mögöttes platform esetében a Microsoft az összes vásárlói tartalmat bizalmasként kezeli, és védelmet nyújt az ügyfelek adatvesztése és a kitettség ellen. Annak biztosítása érdekében, hogy az Azure-beli ügyféladatok biztonságban maradjanak, a Microsoft végrehajtotta és karbantartja a robusztus adatvédelmi szabályozást és képességeket.

* [Az ügyféladatok Azure-beli védelmének ismertetése](../security/fundamentals/protection-customer-data.md)

* [Azure Storage-fiókok biztonságossá tétele](../storage/blobs/security-recommendations.md)

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4,4: minden bizalmas adat titkosítása az átvitel során

**Útmutató**: Microsoft Azure az erőforrások alapértelmezés szerint a TLS 1,2-et fogják egyeztetni. Győződjön meg arról, hogy a Data Lake Analyticshoz csatlakozó ügyfelek a TLS 1,2-es vagy újabb használatával is megegyeznek.

* [Példa műveleti lista](/rest/api/datalakeanalytics/operations/list)

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Megosztott

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4,5: aktív felderítési eszköz használata a bizalmas adatok azonosítására

**Útmutató**: az adatazonosítási funkciók még nem érhetők el Azure Data Lake Analytics erőforrásokhoz. Külső gyártótól származó megoldás implementálása, ha az szükséges a megfelelőség szempontjából.

* [Az ügyféladatok Azure-beli védelmének ismertetése](../security/fundamentals/protection-customer-data.md)

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4,6: az erőforrásokhoz való hozzáférés szabályozása az Azure RBAC

**Útmutató**: az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC) használata annak szabályozására, hogy a felhasználók hogyan használják a szolgáltatást.

* [Azure-RBAC kezelése](./data-lake-analytics-manage-use-portal.md#manage-azure-role-based-access-control)

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4,7: a gazdagép-alapú adatvesztés-megelőzés használata a hozzáférés-vezérlés kikényszeríthető

**Útmutató**: nem alkalmazható; Ez a javaslat számítási erőforrások számára készült.

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: nem alkalmazható

### <a name="48-encrypt-sensitive-information-at-rest"></a>4,8: bizalmas adatok titkosítása a nyugalmi állapotban

**Útmutató**: az adattárolás az alapértelmezett Data Lake Storage Gen1 fiókban történik. Az inaktív adatok esetében Data Lake Storage Gen1 támogatja a "bekapcsolva, alapértelmezés szerint", transzparens titkosítást.

* [Azure Data Lake Storage Gen1ban lévő adattitkosítás](../data-lake-store/data-lake-store-encryption.md)

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Megosztott

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4,9: a kritikus Azure-erőforrások változásainak naplózása és riasztása

**Útmutató**: a Azure monitor és az Azure-tevékenység naplójának használata riasztások létrehozásához, amikor a módosítások Azure Data Lake Analytics erőforrások üzemi példányain lépnek életbe.

* [Riasztások létrehozása az Azure-tevékenységek naplózási eseményeihez](../azure-monitor/alerts/alerts-activity-log.md)

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

## <a name="vulnerability-management"></a>Biztonságirés-kezelés

*További információ [: Security Control: sebezhetőségi kezelés](../security/benchmarks/security-control-vulnerability-management.md).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5,1: automatikus biztonsági rések vizsgálatára szolgáló eszközök futtatása

**Útmutató**: Azure Security Center ajánlásainak követése a Azure Data Lake Analytics erőforrások biztonságossá tételéhez.

A Microsoft a biztonsági rések kezelését a Azure Data Lake Analytics támogató mögöttes rendszereken hajtja végre.

* [Azure Security Center javaslatok ismertetése](../security-center/recommendations-reference.md)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5,2: az operációs rendszer automatikus javításának felügyeleti megoldásának telepítése

**Útmutató**: nem alkalmazható; Ez a vezérlő számítási erőforrások számára készült.

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: nem alkalmazható

### <a name="53-deploy-automated-patch-management-solution-for-third-party-software-titles"></a>5,3: a harmadik féltől származó szoftverek címeihez készült automatizált javítási megoldás üzembe helyezése

**Útmutató**: nem alkalmazható; Ez a vezérlő számítási erőforrások számára készült.

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: nem alkalmazható

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5,4: a biztonsági rések keresésének összehasonlítása

**Útmutató**: nem alkalmazható; Ez a vezérlő számítási erőforrások számára készült.

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: nem alkalmazható

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5,5: kockázatértékelési folyamat használatával rangsorolhatja a felderített biztonsági rések szervizelését

**Útmutató**: közös kockázati pontozási programot (például gyakori sebezhetőségi pontozási rendszer) vagy a harmadik féltől származó ellenőrzési eszköz által biztosított alapértelmezett kockázati minősítéseket használjon.

* [NIST-kiadvány – gyakori sebezhetőségi pontozási rendszerek](https://www.nist.gov/publications/common-vulnerability-scoring-system)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

## <a name="inventory-and-asset-management"></a>Leltár- és eszközfelügyelet

*További információkért lásd [: biztonsági vezérlés: leltár és eszközkezelés](../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-automated-asset-discovery-solution"></a>6,1: automatikus eszköz-felderítési megoldás használata

**Útmutató**: az Azure Resource Graph használatával lekérdezheti és felderítheti az előfizetésekben lévő összes erőforrást (például a számítási, tárolási, hálózati, portok és protokollok stb.). Győződjön meg arról, hogy a bérlőben a megfelelő (olvasási) engedélyek szerepelnek, valamint az összes Azure-előfizetés enumerálása, valamint az előfizetések erőforrásai.

Bár a klasszikus Azure-erőforrások az Azure Resource Graph Explorerben is felderíthető, erősen ajánlott Azure Resource Manager erőforrások létrehozása és használata.

* [Lekérdezések létrehozása az Azure Resource Graph Explorerrel](../governance/resource-graph/first-query-portal.md)

* [Azure-előfizetések megtekintése](/powershell/module/az.accounts/get-azsubscription)

* [Az Azure RBAC ismertetése](../role-based-access-control/overview.md)

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="62-maintain-asset-metadata"></a>6,2: az eszköz metaadatainak fenntartása

**Útmutató**: címkéket alkalmazhat az Azure-erőforrásokra, így a metaadatok logikailag rendszerezve lesznek a besorolásban.

* [Címkék létrehozása és használata](../azure-resource-manager/management/tag-resources.md)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="63-delete-unauthorized-azure-resources"></a>6,3: jogosulatlan Azure-erőforrások törlése

**Útmutató**: a címkézés, a felügyeleti csoportok és a különálló előfizetések használata, ha szükséges, a Azure Data Lake Analytics erőforrások rendszerezéséhez és nyomon követéséhez. Rendszeres időközönként egyeztetheti a leltárt, és gondoskodhat arról, hogy a jogosulatlan erőforrások törlése az előfizetésből időben történjen.

Emellett az Azure Policy használatával korlátozásokat állíthat be az ügyfél-előfizetésekben létrehozható erőforrások típusára a következő beépített szabályzat-definíciók használatával:
- Nem engedélyezett erőforrástípusok
- Engedélyezett erőforrástípusok

* [További Azure-előfizetések létrehozása](../cost-management-billing/manage/create-subscription.md)

* [Management Groups létrehozása](../governance/management-groups/create-management-group-portal.md)

* [Címkék létrehozása és használata](../azure-resource-manager/management/tag-resources.md)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6,4: a jóváhagyott Azure-erőforrások leltárának meghatározása és karbantartása

**Útmutató**: a számítási erőforrásokhoz jóváhagyott Azure-erőforrások és jóváhagyott szoftverek leltárának létrehozása a szervezeti igényeknek megfelelően.

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: nem alkalmazható

### <a name="65-monitor-for-unapproved-azure-resources"></a>6,5: a nem jóváhagyott Azure-erőforrások figyelése

**Útmutató**: az Azure Policy használatával korlátozásokat állíthat be az ügyfél-előfizetésekben létrehozható erőforrások típusára a következő beépített szabályzat-definíciók használatával:
- Nem engedélyezett erőforrástípusok
- Engedélyezett erőforrástípusok

Emellett az Azure Resource Graph használatával lekérdezheti vagy felderítheti az előfizetésben (k) belüli erőforrásokat.

* [Az Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md)

* [Lekérdezések létrehozása az Azure Graph használatával](../governance/resource-graph/first-query-portal.md)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6,6: a nem jóváhagyott szoftveralkalmazások figyelése a számítási erőforrásokon belül

**Útmutató**: nem alkalmazható; Ez a vezérlő számítási erőforrások számára készült.

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: nem alkalmazható

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6,7: nem jóváhagyott Azure-erőforrások és szoftveralkalmazások eltávolítása

**Útmutató**: nem alkalmazható; Ez a vezérlő számítási erőforrások számára készült.

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: nem alkalmazható

### <a name="68-use-only-approved-applications"></a>6,8: csak jóváhagyott alkalmazások használata

**Útmutató**: nem alkalmazható; Ez a vezérlő számítási erőforrások számára készült.

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: nem alkalmazható

### <a name="69-use-only-approved-azure-services"></a>6,9: csak jóváhagyott Azure-szolgáltatások használata

**Útmutató**: a Azure Policy használatával korlátozásokat állíthat be az ügyfél-előfizetésekben létrehozható erőforrások típusára a következő beépített szabályzat-definíciók használatával:
- Nem engedélyezett erőforrástípusok
- Engedélyezett erőforrástípusok

* [Az Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md)

* [Adott erőforrástípus megtagadása a következővel Azure Policy](../governance/policy/samples/index.md)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6,10: a jóváhagyott szoftverek leltárának fenntartása

**Útmutató**: nem alkalmazható; Ez a vezérlő számítási erőforrások számára készült.

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: nem alkalmazható

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6,11: korlátozza a felhasználók képességét a Azure Resource Manager való interakcióra

**Útmutató**: az Azure feltételes hozzáférésének konfigurálása a felhasználók "Microsoft Azure felügyelet" alkalmazáshoz való hozzáférésének tiltása a Azure Resource Manager való interakcióra.

* [Feltételes hozzáférés konfigurálása az ARM-hozzáférés blokkolásához](../role-based-access-control/conditional-access-azure-management.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6,12: korlátozza a felhasználók számára a parancsfájlok végrehajtásának lehetőségét a számítási erőforrásokon belül

**Útmutató**: nem alkalmazható; Ez a vezérlő számítási erőforrások számára készült.

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: nem alkalmazható

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6,13: fizikailag vagy logikailag elkülöníthető a nagy kockázatú alkalmazások

**Útmutató**: nem alkalmazható; Ez a javaslat Azure App Service vagy számítási erőforrásokon futó webalkalmazásokhoz készült.

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: nem alkalmazható

## <a name="secure-configuration"></a>Biztonságos konfiguráció

*További információkért lásd [: biztonság-vezérlés: biztonságos konfiguráció](../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7,1: biztonságos konfigurációk létrehozása az összes Azure-erőforráshoz

**Útmutató**: Azure Policy aliasok használata a "Microsoft. DataLakeAnalytics" névtérben egyéni szabályzatok létrehozásához a Azure Data Lake Analytics konfigurációjának naplózásához vagy érvényesítéséhez. Használhatja a Azure Data Lake Analyticshoz kapcsolódó beépített szabályzat-definíciókat is, például a következőket:
- A Data Lake Analytics lévő diagnosztikai naplókat engedélyezni kell

* [Az elérhető Azure Policy aliasok megtekintése](/powershell/module/az.resources/get-azpolicyalias)

* [Az Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="72-establish-secure-operating-system-configurations"></a>7,2: biztonságos operációsrendszer-konfigurációk létrehozása

**Útmutató**: nem alkalmazható; Ez a vezérlő számítási erőforrások számára készült.

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: nem alkalmazható

### <a name="73-maintain-secure-azure-resource-configurations"></a>7,3: biztonságos Azure-erőforrás-konfigurációk karbantartása

**Útmutató**: az Azure Policy [megtagadás] és a [telepítés, ha nem létezik] az Azure-erőforrások biztonságos beállításainak betartatására használható.

* [Az Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md)

* [Azure Policy effektusok ismertetése](../governance/policy/concepts/effects.md)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="74-maintain-secure-operating-system-configurations"></a>7,4: az operációs rendszer biztonságos konfigurációjának fenntartása

**Útmutató**: nem alkalmazható; Ez a vezérlő számítási erőforrások számára készült.

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: nem alkalmazható

### <a name="75-securely-store-configuration-of-azure-resources"></a>7,5: az Azure-erőforrások biztonságos tárolása

**Útmutató**: az Azure Repos használatával biztonságosan tárolhatja és kezelheti a kódokat, például az egyéni Azure-szabályzatokat, Azure Resource Manager sablonokat, a kívánt állapot-konfigurációs parancsfájlokat stb. Az Azure DevOps felügyelt erőforrásainak eléréséhez engedélyeket adhat meg vagy tagadhat meg bizonyos felhasználók, beépített biztonsági csoportok vagy Azure Active Directory (Azure AD) által meghatározott csoportok számára, ha az integrálva van az Azure DevOps, vagy Active Directory, ha a TFS integrálva van.

* [Kód tárolása az Azure DevOps](/azure/devops/repos/git/gitworkflow?view=azure-devops&preserve-view=true)

* [Engedélyek és csoportok az Azure DevOps](/azure/devops/organizations/security/about-permissions)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="76-securely-store-custom-operating-system-images"></a>7,6: az egyéni operációsrendszer-lemezképek biztonságos tárolása

**Útmutató**: nem alkalmazható; Ez a vezérlő számítási erőforrások számára készült.

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: nem alkalmazható

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7,7: az Azure-erőforrások konfigurációs felügyeleti eszközeinek üzembe helyezése

**Útmutató**: nem alkalmazható; Ez a vezérlő számítási erőforrások számára készült.

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: nem alkalmazható

### <a name="78-deploy-configuration-management-tools-for-operating-systems"></a>7,8: konfigurációs felügyeleti eszközök telepítése operációs rendszerekhez

**Útmutató**: nem alkalmazható; Ez a vezérlő számítási erőforrások számára készült.

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: nem alkalmazható

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7,9: automatikus konfigurációs monitorozás megvalósítása Azure-erőforrásokhoz

**Útmutató**: Azure Policy aliasok használata a "Microsoft. DataLakeAnalytics" névtérben egyéni szabályzatok létrehozásához a riasztáshoz, a naplózáshoz és a rendszer-konfigurációk kényszerítéséhez. Az Azure Policy [audit], [megtagadás] és [telepítés ha nem létezik] használatával automatikusan kényszerítheti ki a Azure Data Lake Analytics-erőforrások konfigurációit.

* [Az Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7,10: az operációs rendszerek automatikus konfiguráció-figyelésének megvalósítása

**Útmutató**: nem alkalmazható; Ez a vezérlő számítási erőforrások számára készült.

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: nem alkalmazható

### <a name="711-manage-azure-secrets-securely"></a>7,11: az Azure-titkok biztonságos kezelése

**Útmutató**: nem alkalmazható; A Data Lake Analytics nem tesz elérhetővé olyan titkot, amelyet az ügyfélnek kezelnie kell.

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: nem alkalmazható

### <a name="712-manage-identities-securely-and-automatically"></a>7,12: az identitások biztonságos és automatikus kezelése

**Útmutató**: nem alkalmazható; A Data Lake Analytics nem támogatja az Azure által felügyelt identitásokat.

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: nem alkalmazható

### <a name="713-eliminate-unintended-credential-exposure"></a>7,13: a hitelesítő adatok nem szándékolt expozíciójának megszüntetése

**Útmutató**: hitelesítő adatok beolvasása a programkódon belül a hitelesítő adatok azonosításához. A Credential Scanner a felfedezett hitelesítő adatok biztonságosabb helyre, például az Azure Key Vaultba való áthelyezésére is javaslatot tesz.

* [A hitelesítő adatok beolvasójának beállítása](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

## <a name="malware-defense"></a>Kártevők elleni védelem

*További információkért lásd [: biztonsági ellenőrzés: kártevők elleni védelem](../security/benchmarks/security-control-malware-defense.md).*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8,1: központilag felügyelt kártevő szoftverek használata

**Útmutató**: nem alkalmazható; Ez a vezérlő számítási erőforrások számára készült.

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: nem alkalmazható

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8,2: a nem számítási Azure-erőforrásokra feltöltött fájlok előzetes vizsgálata

**Útmutató**: a Microsoft kártevő szoftverrel való ellátása engedélyezve van az Azure-szolgáltatásokat támogató mögöttes gazdagépen (például Azure Data Lake Analytics), azonban nem az ügyfél tartalmán fut.

Az Azure-erőforrásokra feltöltött tartalom előzetes vizsgálata, például App Service, Data Lake Analytics, Blob Storage stb. A Microsoft nem fér hozzá az adataihoz ezekben a példányokban.

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8,3: a kártevő szoftverek és az aláírások frissítésének ellenőrzése

**Útmutató**: nem alkalmazható; Ez a vezérlő számítási erőforrások számára készült.

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: nem alkalmazható

## <a name="data-recovery"></a>Adat-helyreállítás

*További információkért lásd [: biztonsági ellenőrzés: adat-helyreállítás](../security/benchmarks/security-control-data-recovery.md).*

### <a name="91-ensure-regular-automated-back-ups"></a>9,1: rendszeres automatizált biztonsági másolatok biztosítása

**Útmutató**: Data Lake Analytics a feladatok naplóit és az adatok kimenetét az alapul szolgáló Data Lake Storage Gen1 szolgáltatás tárolja. Különböző módszerekkel másolhatja az Adatmásolást, beleértve a ADLCopy, a Azure PowerShell vagy a Azure Data Factoryt is. A Azure Automation használatával automatikusan biztonsági másolatot készíthet az adatbázisokról.

* [Azure Data Lake Storage Gen1 erőforrások kezelése Storage Explorer használatával](../data-lake-store/data-lake-store-in-storage-explorer.md)

* [Adatok másolása az Azure Storage-Blobokból a Azure Data Lake Storage Gen1ba](../data-lake-store/data-lake-store-copy-data-azure-storage-blob.md)

* [Azure Automation áttekintése](../automation/automation-intro.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: teljes rendszerbiztonsági mentés és minden ügyfél által felügyelt kulcs biztonsági mentése

**Útmutató**: Data Lake Analytics a feladatok naplóit és az adatok kimenetét az alapul szolgáló Data Lake Storage Gen1 szolgáltatás tárolja. Különböző módszerekkel másolhatja az Adatmásolást, beleértve a ADLCopy, a Azure PowerShell vagy a Azure Data Factoryt is.

* [Azure Data Lake Storage Gen1 erőforrások kezelése Storage Explorer használatával](../data-lake-store/data-lake-store-in-storage-explorer.md)

* [Adatok másolása az Azure Storage-Blobokból a Azure Data Lake Storage Gen1ba](../data-lake-store/data-lake-store-copy-data-azure-storage-blob.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: az összes biztonsági másolat ellenőrzése, beleértve az ügyfél által felügyelt kulcsokat

**Útmutató**: az adatok integritásának teszteléséhez rendszeresen végezze el a biztonsági mentési adatok visszaállítását.

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: a biztonsági másolatok és az ügyfél által felügyelt kulcsok védelmének biztosítása

**Útmutató**: Data Lake Analytics a Data Lake Storage Gen1 vagy az Azure Storage-ban tárolt biztonsági mentések alapértelmezés szerint támogatják a titkosítást, és nem kapcsolhatók ki. A biztonsági mentéseket bizalmas adatokként kell kezelni, és az alapkonfiguráció részeként alkalmazni kell a megfelelő hozzáférési és adatvédelmi vezérlőket.

* [Az Azure Data Lake Storage Gen1-ben tárolt adatok védelme](../data-lake-store/data-lake-store-secure-data.md)

* [Az Azure Storage-beli adathozzáférés engedélyezése](../storage/common/storage-auth.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

## <a name="incident-response"></a>Incidensmegoldás

*További információ [: Security Control: incidens válasza](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10,1: incidens-válaszi útmutató létrehozása

**Útmutató**: Alakítson ki incidenskezelési útmutatót a vállalat számára. Gondoskodjon írásos incidenskezelési tervekről, amelyek definiálják az összes résztvevő szerepkörét, valamint az incidenskezelés fázisait az észleléstől az incidens utáni értékelésig.

* [Útmutató a saját biztonsági incidensek megoldási folyamatának létrehozásához](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

* [Microsoft Security Response Center – incidens anatómiája](https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/)

* [Az ügyfél a NIST számítógépes biztonsági incidensek kezelési útmutatóját is kihasználhatja a saját incidens-válasz tervének létrehozásával kapcsolatos támogatáshoz](https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10,2: incidensek pontozásának és rangsorolási eljárásának létrehozása

**Útmutató**: a Security Center súlyosságot rendel az egyes riasztásokhoz, hogy a prioritások alapján ki lehessen deríteni, hogy mely riasztásokat kell először megvizsgálni. A súlyosság azon alapul, hogy az Security Center milyen mértékben szerepel a riasztás kibocsátásához használt elemzésben vagy elemzésben, valamint azt a megbízhatósági szintet, amely a riasztáshoz vezető tevékenység mögött rosszindulatú szándékú volt.

Emellett egyértelműen megjelölheti az előfizetéseket (pl.: éles környezetben, nem gyártva) címkék használatával és elnevezési rendszer létrehozása az Azure-erőforrások egyértelmű azonosításához és kategorizálásához, különösen a bizalmas adatok feldolgozásához. Az Ön felelőssége, hogy rangsorolja a riasztások megoldását azon Azure-erőforrások és -környezetek kritikussága alapján, ahol az incidens történt.

* [Biztonsági riasztások az Azure Security Centerben](../security-center/security-center-alerts-overview.md)

* [Címkék használata az Azure-erőforrások rendszerezéséhez](../azure-resource-manager/management/tag-resources.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="103-test-security-response-procedures"></a>10,3: biztonsági reagálási eljárások tesztelése

**Útmutató**: az Azure-erőforrások védelmének biztosítása érdekében a rendszer az incidensek reagálási képességeinek rendszeres tesztelésére szolgáló gyakorlatokat hajt végre. Azonosítsa a gyenge pontokat és a hézagokat, majd szükség szerint módosítsa a választ.

* [A NIST kiadványa – útmutató az IT-csomagok és-képességek teszteléséhez, betanításához és gyakorlatához](https://csrc.nist.gov/publications/detail/sp/800-84/final)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10,4: biztonsági incidensek elérhetőségének biztosítása és riasztási értesítések konfigurálása biztonsági incidensekhez

**Útmutató**: a Microsoft a biztonsági incidensek elérhetőségi adatait arra használja fel, hogy felvegye Önnel a kapcsolatot, ha a Microsoft Security Response Center (MSRC) felfedi, hogy az adatokat egy törvénytelen vagy jogosulatlan fél is hozzáférte. A problémák megoldása érdekében tekintse át az incidenseket a tény után.

* [A Azure Security Center biztonsági kapcsolattartó beállítása](../security-center/security-center-provide-security-contact-details.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10,5: biztonsági riasztások beépítése az incidensek gyorsreagáló rendszerébe

**Útmutató**: az Azure Security Center-riasztások és javaslatok exportálása a folyamatos exportálás funkcióval az Azure-erőforrásokkal kapcsolatos kockázatok azonosítása érdekében. A folyamatos exportálás lehetővé teszi a riasztások és javaslatok manuális és folyamatos exportálását. Az Azure Security Center adatösszekötővel továbbíthatja a riasztásokat az Azure Sentinel szolgáltatásba.

* [Folyamatos exportálás konfigurálása](../security-center/continuous-export.md)

* [Riasztások streamelése az Azure Sentinelbe](../sentinel/connect-azure-security-center.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="106-automate-the-response-to-security-alerts"></a>10,6: a biztonsági riasztásokra adott válasz automatizálása

**Útmutató**: a Azure Security Center munkafolyamat-automatizálási funkciója segítségével automatikusan elindíthatja a válaszokat az "Logic apps" használatával a biztonsági riasztások és az Azure-erőforrások védelme érdekében javasolt javaslatok alapján.

* [A Munkafolyamat-automatizálás és a Logic Apps konfigurálása](../security-center/workflow-automation.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

## <a name="penetration-tests-and-red-team-exercises"></a>Behatolási tesztek és Red Team-gyakorlatok

*További információkért lásd [: biztonsági ellenőrzés: behatolási tesztek és Red Team-gyakorlatok](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11,1: az Azure-erőforrások rendszeres behatolásának tesztelése, valamint az összes kritikus biztonsági vizsgálat szervizelésének biztosítása

**Útmutató**: kövesse a Microsoft Cloud penetráció tesztelési szabályait, amelyekkel biztosíthatja, hogy a behatolási tesztek ne sértsék a Microsoft-házirendeket. A Microsoft által felügyelt felhőalapú infrastruktúrán, szolgáltatásokon és alkalmazásokon végzett riasztási és élő behatolási tesztek végrehajtásához használja a Microsoft stratégiáját és végrehajtási tervét.

* [Behatolástesztelési beavatkozási szabályok](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)

* [Riasztási tesztek a Microsoft-felhőben](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Megosztott

## <a name="next-steps"></a>Következő lépések

- Lásd az [Azure biztonsági teljesítménytesztét](../security/benchmarks/overview.md)
- További tudnivalók az [Azure biztonsági alapterveiről](../security/benchmarks/security-baselines-overview.md)