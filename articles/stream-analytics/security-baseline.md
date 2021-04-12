---
title: Stream Analytics Azure biztonsági alapterve
description: A Stream Analytics biztonsági alapterve az Azure biztonsági Teljesítménytesztben meghatározott biztonsági javaslatok megvalósítására szolgáló eljárási útmutatást és erőforrásokat biztosít.
author: msmbaldwin
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 04/08/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 88e27b839e3f8baf9a1df8a6a6d936aecd602b23
ms.sourcegitcommit: c6a2d9a44a5a2c13abddab932d16c295a7207d6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/09/2021
ms.locfileid: "107285113"
---
# <a name="azure-security-baseline-for-stream-analytics"></a>Stream Analytics Azure biztonsági alapterve

Ez a biztonsági alapkonfiguráció az [Azure Security benchmark 1.0-s verziójának](../security/benchmarks/overview-v1.md) stream Analyticsára vonatkozó útmutatást alkalmazza. Az Azure Security Benchmark ajánlásokat ad arra nézve, hogy hogyan tehetők biztonságossá a felhőalapú megoldások az Azure-ban. A tartalom az Azure biztonsági teljesítményteszt által meghatározott **biztonsági vezérlők** szerint van csoportosítva, valamint a stream Analytics vonatkozó kapcsolódó útmutatás. 

> [!NOTE]
> Az Stream Analyticsre nem alkalmazható **vezérlők** , vagy amelyek esetében a felelősség a Microsoft által lett kizárva. Ha szeretné megtekinteni, hogyan Stream Analytics teljes mértékben leképezni az Azure biztonsági Teljesítménytesztét, tekintse meg a **[teljes stream Analytics biztonsági alapterv-leképezési fájlt](https://github.com/MicrosoftDocs/SecurityBenchmarks/raw/master/Azure%20Offer%20Security%20Baselines/1.1/stream-analytics-security-baseline-v1.1.xlsx)**.

## <a name="network-security"></a>Hálózati biztonság

*További információ: [Azure Security Benchmark: Hálózati biztonság](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1,1: Azure-erőforrások biztosítása virtuális hálózatokon belül

**Útmutató**: a Azure stream Analytics nem támogatja a hálózati biztonsági csoportok (NSG) és a Azure Firewall használatát.

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-network-interfaces"></a>1,2: a virtuális hálózatok, alhálózatok és hálózati adapterek konfigurációjának és forgalmának figyelése és naplózása

**Útmutató**: a Azure stream Analytics nem támogatja a virtuális hálózatok és alhálózatok használatát.

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="13-protect-critical-web-applications"></a>1,3: a kritikus webalkalmazások megóvása 

**Útmutató**: nem alkalmazható; Ez a javaslat Azure App Service vagy számítási erőforrásokon futó webalkalmazásokhoz készült.

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1,4: az ismert kártevő IP-címekkel folytatott kommunikáció megtagadása

**Útmutató**: az Azure Security Center veszélyforrások elleni védelem használatával észlelheti és figyelmeztetheti a kommunikációt az ismert kártékony vagy nem használt internetes IP-címekkel.

- [Veszélyforrások elleni védelem az Azure szolgáltatási rétegében Azure Security Center](../security-center/azure-defender.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="15-record-network-packets"></a>1,5: hálózati csomagok rögzítése

**Útmutató**: a Azure stream Analytics nem használ hálózati biztonsági csoportokat, és a Azure Key Vault nem rögzítik a flow-naplókat.

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1,6: hálózati behatolás-észlelési/Behatolás-megelőzési rendszerek (AZONOSÍTÓk/IP-címek) üzembe helyezése

**Útmutató**: a Azure Security Center veszélyforrások elleni védelem használatával azonosíthatja az Azure-előfizetési környezet szokatlan vagy potenciálisan káros műveleteit.

- [Veszélyforrások elleni védelem az Azure szolgáltatási rétegében Azure Security Center](../security-center/azure-defender.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="17-manage-traffic-to-web-applications"></a>1,7: webalkalmazások forgalmának kezelése

**Útmutató**: nem alkalmazható; Ez a javaslat Azure App Service vagy számítási erőforrásokon futó webalkalmazásokhoz készült.

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1,8: a hálózati biztonsági szabályok bonyolultságának és adminisztratív terhelésének csökkentése

**Útmutató**: a Azure stream Analytics nem támogatja a virtuális hálózatok és a hálózati szabályok használatát.

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1,9: a hálózati eszközök szabványos biztonsági konfigurációinak fenntartása 

**Útmutató**: a Azure stream Analytics nem támogatja a virtuális hálózatok és a hálózati eszközök használatát.

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="110-document-traffic-configuration-rules"></a>1,10: a dokumentum forgalmának konfigurációs szabályai 

**Útmutató**: a Azure stream Analytics nem támogatja a virtuális hálózatok és a forgalmi konfigurációs szabályok használatát.

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1,11: automatikus eszközök használata a hálózati erőforrások konfigurációjának figyelésére és a változások észlelésére 

**Útmutató**: az Azure-tevékenység naplójának használata az erőforrás-konfigurációk figyelésére és a stream Analytics erőforrások változásainak észlelésére. Hozzon létre riasztásokat a Azure Monitoron belül, amelyek akkor lépnek életbe, amikor a kritikus erőforrások változásai megváltoznak.

- [Azure-Tevékenységnaplók eseményeinek megtekintése és lekérése](https://docs.microsoft.com/azure/azure-monitor/essentials/activity-log#view-the-activity-log)

- [Riasztások létrehozása a Azure Monitorban](../azure-monitor/alerts/alerts-activity-log.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

## <a name="logging-and-monitoring"></a>Naplózás és monitorozás

*További információt az [Azure biztonsági teljesítményteszt: naplózás és figyelés](../security/benchmarks/security-control-logging-monitoring.md)című témakörben talál.*

### <a name="22-configure-central-security-log-management"></a>2,2: a központi biztonsági naplók felügyeletének konfigurálása 

**Útmutató**: naplók beolvasása a Azure monitor használatával a biztonsági adatokat, például a naplózási eseményeket és a kérelmeket összesítve. Azure Monitoron belül Log Analytics munkaterületek használatával kérdezheti le és hajthatja végre az elemzéseket, és használhatja az Azure Storage accountyfor hosszú távú/archiválási tárolóját, opcionálisan olyan biztonsági funkciókkal, mint a megváltoztathatatlan tárolás és a kényszerített megőrzés. 

- [Platform-naplók és-metrikák összegyűjtése Azure Monitor](../azure-monitor/essentials/diagnostic-settings.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="23-enable-audit-logging-for-azure-resources"></a>2,3: az Azure-erőforrások naplózásának engedélyezése

**Útmutató**: a Azure stream Analytics diagnosztikai beállításainak engedélyezése a felügyeleti, biztonsági és diagnosztikai naplókhoz való hozzáféréshez. Engedélyezheti az Azure-műveletnapló diagnosztikai beállításait is, és elküldheti a naplókat ugyanarra a Log Analytics munkaterületre vagy Storage-fiókba.

- [A Azure Stream Analytics diagnosztikai naplókat és tevékenységeket biztosít a felülvizsgálathoz](stream-analytics-job-diagnostic-logs.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás**: az [Azure biztonsági teljesítményteszt](/azure/governance/policy/samples/azure-security-benchmark) a Security Center alapértelmezett házirend-kezdeményezése, és a [Security Center ajánlásainak](/azure/security-center/security-center-recommendations)alapja. A vezérlőhöz kapcsolódó Azure Policy-definíciók Security Center automatikusan engedélyezve vannak. Az ehhez a vezérlőhöz kapcsolódó riasztásokhoz szükség lehet egy [Azure Defender](/azure/security-center/azure-defender) -csomagra a kapcsolódó szolgáltatásokhoz.

**Azure Policy beépített definíciók – Microsoft. StreamAnalytics**:

[!INCLUDE [Resource Policy for Microsoft.StreamAnalytics 2.3](../../includes/policy/standards/asb/rp-controls/microsoft.streamanalytics-2-3.md)]

### <a name="24-collect-security-logs-from-operating-systems"></a>2,4: biztonsági naplók gyűjtése az operációs rendszerekből

**Útmutató**: nem alkalmazható; Ez a javaslat számítási erőforrások számára készült.

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="25-configure-security-log-storage-retention"></a>2,5: a biztonsági napló tárolási adatmegőrzésének konfigurálása 

**Útmutató**: Ha a biztonsági eseménynaplókat az Azure Storage-fiókban vagy log Analytics munkaterületen tárolja, az adatmegőrzési házirendet a szervezet követelményeinek megfelelően állíthatja be. 

- [A Azure Stream Analytics diagnosztikai naplókat és tevékenységeket biztosít a felülvizsgálathoz](stream-analytics-job-diagnostic-logs.md)

- [Adatmegőrzési szabályzat konfigurálása az Azure Storage-fiók naplóihoz](https://docs.microsoft.com/azure/storage/common/manage-storage-analytics-logs#configure-logging) 

- [Az adatmegőrzési időszak módosítása Log Analytics](https://docs.microsoft.com/azure/azure-monitor/logs/manage-cost-storage#change-the-data-retention-period)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="26-monitor-and-review-logs"></a>2,6: naplók figyelése és áttekintése

**Útmutató**: elemezze és figyelje a rendellenes viselkedés naplóit, és rendszeresen tekintse át a stream Analytics erőforrásainak eredményeit. Az Azure Monitor Log Analytics munkaterülete segítségével tekintheti át a naplókat, és lekérdezéseket végezhet a naplózási adatokon. Alternatív megoldásként engedélyezheti és elvégezheti az Azure Sentinel vagy egy harmadik féltől származó SIEM-et. 

- [Az Azure Sentinel előkészítése](../sentinel/quickstart-onboard.md) 

- [További információ a Log Analytics munkaterületről](../azure-monitor/logs/log-analytics-tutorial.md)

- [Egyéni lekérdezések végrehajtása a Azure Monitorban](../azure-monitor/logs/get-started-queries.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="27-enable-alerts-for-anomalous-activities"></a>2,7: riasztások engedélyezése rendellenes tevékenységekhez

**Útmutató**: a stream Analytics diagnosztikai beállításainak engedélyezése és naplók elküldése egy log Analytics-munkaterületre. A Log Analytics-munkaterületet az Azure Sentinelbe irányíthatja, mivel ez egy biztonsági előkészítési automatizált választ (felszárnyaló) megoldást biztosít. Ez lehetővé teszi a forgatókönyvek (automatizált megoldások) létrehozását és a biztonsági problémák megoldására való felhasználását. 

- [Az Azure Sentinel előkészítése](../sentinel/quickstart-onboard.md) 

- [Riasztás a log Analytics-naplófájlok adatkezeléséről](../azure-monitor/alerts/tutorial-response.md)  

- [A Azure Stream Analytics diagnosztikai naplókat és tevékenységeket biztosít a felülvizsgálathoz](stream-analytics-job-diagnostic-logs.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="28-centralize-anti-malware-logging"></a>2,8: kártevő szoftverek közötti naplózás központosítása 

**Útmutató**: nem alkalmazható; Stream Analytics nem dolgoz fel kártevő szoftverrel kapcsolatos naplókat, illetve nem hoz létre.

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="29-enable-dns-query-logging"></a>2,9: DNS-lekérdezések naplózásának engedélyezése 

**Útmutató**: Azure DNS Analytics (előzetes verzió) megoldás Azure monitor a DNS-infrastruktúrával kapcsolatos elemzéseket gyűjt a biztonság, a teljesítmény és a műveletek terén. Ez jelenleg nem támogatja Azure Stream Analytics azonban külső DNS-naplózási megoldást is használhat. 

- [A DNS-infrastruktúrával kapcsolatos információk gyűjtése a DNS Analytics előzetes verziójának megoldásával](../azure-monitor/insights/dns-analytics.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="210-enable-command-line-audit-logging"></a>2,10: parancssori naplózás engedélyezése 

**Útmutató**: nem alkalmazható; Ez a javaslat számítási erőforrások számára készült.

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

## <a name="identity-and-access-control"></a>Identitás- és hozzáférés-vezérlés

*További információt az [Azure biztonsági teljesítményteszt: Identity and Access Control](../security/benchmarks/security-control-identity-access-control.md)című témakörben talál.*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3,1: a felügyeleti fiókok leltárának karbantartása 

**Útmutató**: Azure Active Directory (Azure ad) beépített szerepkörökkel rendelkezik, amelyeket explicit módon hozzá kell rendelni. A szerepköröket a tagság felderítésére lehet lekérdezni. Az Azure AD PowerShell-modullal ad hoc lekérdezéseket hajthat végre a felügyeleti csoportok tagjait futtató fiókok felderítéséhez.

- [Címtárbeli szerepkör beszerzése az Azure AD-ben a PowerShell-lel](/powershell/module/azuread/get-azureaddirectoryrole)

- [Címtárbeli szerepkör tagjainak beszerzése az Azure AD-ben a PowerShell-lel](/powershell/module/azuread/get-azureaddirectoryrolemember)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="32-change-default-passwords-where-applicable"></a>3,2: az alapértelmezett jelszavak módosítása, ha alkalmazható 

**Útmutató**: a stream Analytics nem rendelkezik az alapértelmezett jelszavakkal, mint a hitelesítéshez Azure Active Directory (Azure ad), és az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC) biztosítja a szolgáltatás felügyeletét. A befecskendező stream-szolgáltatásokból és a kimeneti szolgáltatásokból függően el kell forgatnia a feladatokban konfigurált hitelesítő adatokat.

- [Bejelentkezési hitelesítő adatok elforgatása egy Stream Analytics-feladathoz tartozó bemenetekhez és kimenetekhez](stream-analytics-login-credentials-inputs-outputs.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="33-use-dedicated-administrative-accounts"></a>3,3: dedikált rendszergazdai fiókok használata 

**Útmutató**: hozzon létre egy Identitáskezelés-felügyeleti és szerepkör-biztonsági tervet, és kövesse az ajánlott eljárásokat, beleértve a rendszergazdai szerepkörökhöz legkevesebb jogosultsági szintű hozzáférés elvét. Az Azure Privileged Identity Management (PIM) használatával igény szerinti jogosultsági szintű hozzáférést biztosíthat az Azure Active Directory (Azure AD) és az Azure-erőforrásokhoz. A rendszergazdai fiókok tevékenységének figyeléséhez használja az Azure PIM-riasztásokat és a naplózási előzményeket. Az Azure AD biztonsági jelentéseivel azonosíthatja azokat a rendszergazdai fiókokat, amelyek biztonsága sérülhet.

- [További információ](/azure/active-directory/privileged-identity-management/)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="34-use-azure-active-directory-single-sign-on-sso"></a>3,4: Azure Active Directory egyszeri bejelentkezés (SSO) használata

**Útmutató**: ha lehetséges, használjon Azure Active Directory (Azure ad) SSO-t a különálló hitelesítő adatok konfigurálása helyett. A Azure Security Center Identity &amp; Access-javaslatok megvalósítása.

- [Az egyszeri bejelentkezés ismertetése az Azure AD-vel](../active-directory/manage-apps/what-is-single-sign-on.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3,5: Multi-Factor Authentication használata az összes Azure Active Directory-alapú hozzáféréshez

**Útmutató**: engedélyezze az Azure Active Directory (Azure ad) többtényezős hitelesítését, és kövesse Azure Security Center identitás-és hozzáférés-kezelési javaslatait a stream Analytics erőforrásainak védelméhez.

- [Többtényezős hitelesítés engedélyezése az Azure-ban](../active-directory/authentication/howto-mfa-getstarted.md)

- [Identitás és hozzáférés figyelése Azure Security Centeron belül](../security-center/security-center-identity-access.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="36-use-secure-azure-managed-workstations-for-administrative-tasks"></a>3,6: biztonságos, Azure által felügyelt munkaállomások használata felügyeleti feladatokhoz

**Útmutató**: a mancsok (emelt szintű hozzáférésű munkaállomások) használata többtényezős hitelesítéssel, amely a stream Analytics erőforrások bejelentkezésére és konfigurálására van konfigurálva.

- [További tudnivalók a privilegizált hozzáférésű munkaállomásokról](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

- [Többtényezős hitelesítés engedélyezése az Azure-ban](../active-directory/authentication/howto-mfa-getstarted.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3,7: naplózás és riasztás a gyanús tevékenységekről a rendszergazdai fiókoktól

**Útmutató**: Azure Active Directory (Azure ad) biztonsági jelentések használata naplók és riasztások generálásához, ha a környezetben gyanús vagy nem biztonságos tevékenység történik. A Azure Security Center használatával figyelheti az identitás-és hozzáférési tevékenységeket.

- [A kockázatos tevékenységek miatt megjelölt Azure AD-felhasználók azonosítása](../active-directory/identity-protection/overview-identity-protection.md)

- [A felhasználók identitási és hozzáférési tevékenységeinek monitorozása az Azure Security Centerben](../security-center/security-center-identity-access.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3,8: az Azure-erőforrások kezelése csak jóváhagyott helyekről

**Útmutató**: a feltételes hozzáférés elnevezett helyeivel engedélyezheti, hogy a hozzáférés csak az IP-címtartományok vagy országok/régiók adott logikai csoportjaihoz legyen elérhető. 

- [Elnevezett helyszínek konfigurálása az Azure-ban](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="39-use-azure-active-directory"></a>3,9: a Azure Active Directory használata

**Útmutató**: a Azure Active Directory (Azure ad) használata központi hitelesítési és engedélyezési rendszerrel. Az Azure AD szerepköralapú hozzáférés-vezérlést (Azure RBAC) biztosít az ügyfelek Stream Analytics erőforrásokhoz való hozzáférésének részletes szabályozásához.

- [Azure AD-példány létrehozása és konfigurálása](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="310-regularly-review-and-reconcile-user-access"></a>3,10: a felhasználói hozzáférés rendszeres áttekintése és egyeztetése

**Útmutató**: Tekintse át a Azure Active Directory (Azure ad) naplókat, amelyek segítenek felderíteni az elavult fiókokat, amelyek magukban foglalhatják a Storage-fiók felügyeleti szerepköreit. Emellett az Azure Identity Access Reviews használatával hatékonyan kezelheti a csoporttagságok kezelését, a vállalati alkalmazásokhoz való hozzáférést és a szerepkör-hozzárendeléseket. A felhasználók hozzáférését rendszeresen felül kell vizsgálni, hogy csak a megfelelő felhasználók férhessenek hozzájuk.

- [Az Azure AD jelentéskészítés ismertetése](/azure/active-directory/reports-monitoring/)

- [Az Azure Identity hozzáférési felülvizsgálatok használata](../active-directory/governance/access-reviews-overview.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3,11: a figyelő megkísérli a deaktivált hitelesítő adatok elérését

**Útmutató**: a Azure Stream Analytics és Azure Active Directory (Azure ad) diagnosztikai beállításainak engedélyezése, az összes napló küldése egy log Analytics munkaterületre. A kívánt riasztások (például a letiltott titkok elérésére tett kísérletek) konfigurálása Log Analyticson belül.

- [Azure AD-naplók integrálása Azure Monitor naplókkal](/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3,12: riasztás a fiók bejelentkezési viselkedésének eltérése esetén

**Útmutató**: az Azure Active Directory (Azure ad) kockázatkezelési és identitás-védelmi funkcióinak használatával konfigurálhatja az stream Analytics erőforrásaival kapcsolatos gyanús műveletekre vonatkozó automatizált válaszokat. A szervezet biztonsági válaszainak megvalósításához engedélyeznie kell az automatikus válaszokat az Azure Sentinel használatával.

- [Az Azure AD kockázatos bejelentkezéseinek megtekintése](../active-directory/identity-protection/overview-identity-protection.md)

- [Az Identity Protection kockázati házirendjeinek konfigurálása és engedélyezése](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [Az Azure Sentinel előkészítése](../sentinel/quickstart-onboard.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3,13: a Microsoft számára elérhetővé teszi a megfelelő ügyféladatokat a támogatási forgatókönyvek során

**Útmutató**: nem alkalmazható; A Ügyfélszéf Azure Stream Analytics esetében nem támogatott.

- [Támogatott szolgáltatások és forgatókönyvek általánosan elérhetővé](https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview#supported-services-and-scenarios-in-general-availability)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

## <a name="data-protection"></a>Adatvédelem

*További információ: [Azure Security Benchmark: Adatvédelem](../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4,1: bizalmas információk leltárának fenntartása

**Útmutató**: a címkék használatával segítheti a bizalmas adatokat tároló vagy feldolgozó stream Analytics erőforrások nyomon követését. 

- [Címkék létrehozása és használata](../azure-resource-manager/management/tag-resources.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4,2: bizalmas adatok tárolására vagy feldolgozására szolgáló rendszerek elkülönítése

**Útmutató**: a stream Analytics feladatok elkülönítése úgy, hogy bemeneteket, kimeneteket és Storage-fiókokat helyez el ugyanabba az előfizetésbe. A Stream Analytics korlátozásával szabályozhatja az alkalmazások és a vállalati környezetek igény szerinti Stream Analytics erőforrásaihoz való hozzáférés szintjét. Azure Stream Analytics Azure Active Directory (Azure AD) RBAC keresztül szabályozhatja a hozzáférést.

- [További Azure-előfizetések létrehozása](../cost-management-billing/manage/create-subscription.md)

- [Management Groups létrehozása](../governance/management-groups/create-management-group-portal.md)

- [Címkék létrehozása és használata](../azure-resource-manager/management/tag-resources.md)

- [Azure Stream Analytics-bemenetek ismertetése](stream-analytics-add-inputs.md)

- [A Azure Stream Analytics kimenetének megismerése](stream-analytics-define-outputs.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4,3: a bizalmas adatok jogosulatlan átvitelének figyelése és letiltása 

**Útmutató**: az adatvesztés-megelőzési funkciók még nem érhetők el Azure stream Analytics erőforrásokhoz. Külső gyártótól származó megoldás implementálása, ha az szükséges a megfelelőség szempontjából.

A Microsoft által felügyelt mögöttes platform esetében a Microsoft az összes vásárlói tartalmat bizalmasként kezeli, és védelmet nyújt az ügyfelek adatvesztése és a kitettség ellen. Annak biztosítása érdekében, hogy az Azure-beli ügyféladatok biztonságban maradjanak, a Microsoft végrehajtotta és karbantartja a robusztus adatvédelmi szabályozást és képességeket.

- [Az ügyféladatok Azure-beli védelmének ismertetése](../security/fundamentals/protection-customer-data.md)

- [Azure Storage-fiókok biztonságossá tétele](../storage/blobs/security-recommendations.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4,5: aktív felderítési eszköz használata a bizalmas adatok azonosítására

**Útmutató**: az adatazonosítási funkciók még nem érhetők el Azure stream Analytics erőforrásokhoz. Külső gyártótól származó megoldás implementálása, ha az szükséges a megfelelőség szempontjából. 

- [Az ügyféladatok Azure-beli védelmének ismertetése](../security/fundamentals/protection-customer-data.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="46-use-role-based-access-control-to-control-access-to-resources"></a>4,6: szerepköralapú hozzáférés-vezérlés használata az erőforrásokhoz való hozzáférés szabályozásához

**Útmutató**: az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC) használata annak szabályozására, hogy a felhasználók hogyan használják a szolgáltatást.

- [Az Azure RBAC konfigurálása](../role-based-access-control/role-assignments-portal.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4,7: a gazdagép-alapú adatvesztés-megelőzés használata a hozzáférés-vezérlés kikényszeríthető

**Útmutató**: nem alkalmazható; Ez a javaslat számítási erőforrások számára készült.

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="48-encrypt-sensitive-information-at-rest"></a>4,8: bizalmas adatok titkosítása a nyugalmi állapotban

**Útmutató**: a stream Analytics nem tárolja a bejövő adatmennyiséget, mivel az összes feldolgozás a memóriában történik.  Az Stream Analytics által megőrzött összes magánjellegű adat, beleértve a lekérdezéseket és a funkciókat is, a konfigurált Storage-fiók tárolja.  Az ügyfél által felügyelt kulcsok (CMK-EK) használatával titkosíthatja a kimeneti adatokat a Storage-fiókokban. Stream Analytics a CMK nélkül is automatikusan alkalmazza a legjobb titkosítási szabványokat az infrastruktúrájában, így titkosíthatja és biztonságossá teheti az adatait.

- [Adatvédelem a Azure Stream Analyticsban](data-protection.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4,9: a kritikus Azure-erőforrások változásainak naplózása és riasztása

**Útmutató**: a Azure monitor és az Azure-tevékenység naplójának használata riasztások létrehozásához, amikor a módosítások Azure stream Analytics erőforrások üzemi példányain lépnek életbe.

- [Riasztások létrehozása az Azure-tevékenységek naplózási eseményeihez](../azure-monitor/alerts/alerts-activity-log.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

## <a name="vulnerability-management"></a>Biztonságirés-kezelés

*További információért lásd az [Azure biztonsági teljesítményteszt: biztonsági rés kezelése](../security/benchmarks/security-control-vulnerability-management.md)című témakört.*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5,1: automatikus biztonsági rések vizsgálatára szolgáló eszközök futtatása

**Útmutató**: Azure Security Center ajánlásainak követése a Azure stream Analytics erőforrások biztonságossá tételéhez.

A Microsoft a biztonsági rések kezelését a Azure Stream Analytics támogató mögöttes rendszereken hajtja végre.

- [Azure Security Center javaslatok ismertetése](../security-center/recommendations-reference.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5,2: az operációs rendszer automatikus javításának felügyeleti megoldásának telepítése

**Útmutató**: nem alkalmazható; Ez a javaslat számítási erőforrások számára készült.

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="53-deploy-automated-patch-management-solution-for-third-party-software-titles"></a>5,3: a harmadik féltől származó szoftverek címeihez készült automatizált javítási megoldás üzembe helyezése

**Útmutató**: nem alkalmazható; Ez a javaslat számítási erőforrások számára készült.

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5,4: a biztonsági rések keresésének összehasonlítása

**Útmutató**: nem alkalmazható; Ez a javaslat számítási erőforrások számára készült.

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5,5: kockázatértékelési folyamat használatával rangsorolhatja a felderített biztonsági rések szervizelését

**Útmutató**: a Azure Security Center által biztosított alapértelmezett kockázati minősítések (biztonságos pontszám) használata. 

- [A Azure Security Center biztonsági pontszámának megismerése](../security-center/secure-score-security-controls.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

## <a name="inventory-and-asset-management"></a>Leltár-és eszközfelügyelet

*További információt az [Azure biztonsági teljesítményteszt: leltár és eszközkezelés](../security/benchmarks/security-control-inventory-asset-management.md)című témakörben talál.*

### <a name="61-use-automated-asset-discovery-solution"></a>6,1: automatikus eszköz-felderítési megoldás használata

**Útmutató**: az Azure Resource Graph használatával lekérdezheti vagy felderítheti az összes erőforrást (például számítási, tárolási, hálózati, portok és protokollok stb.) az előfizetés (ok) n belül. Győződjön meg arról, hogy a bérlőben a megfelelő (olvasási) engedélyek szerepelnek, valamint az összes Azure-előfizetés, valamint az előfizetésekben lévő erőforrások számbavétele.

Bár a klasszikus Azure-erőforrások felderítése az erőforrás-gráfon keresztül lehetséges, erősen ajánlott a Azure Resource Manager erőforrások létrehozása és használata.

- [Lekérdezések létrehozása az Azure Resource Graph használatával](../governance/resource-graph/first-query-portal.md)

- [Azure-előfizetések megtekintése](/powershell/module/az.accounts/get-azsubscription)

- [Az Azure RBAC ismertetése](../role-based-access-control/overview.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="62-maintain-asset-metadata"></a>6,2: az eszköz metaadatainak fenntartása

**Útmutató**: címkéket alkalmazhat az Azure-erőforrásokra, így a metaadatok logikailag rendszerezve lesznek a besorolásban.

- [Címkék létrehozása és használata](../azure-resource-manager/management/tag-resources.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="63-delete-unauthorized-azure-resources"></a>6,3: jogosulatlan Azure-erőforrások törlése

**Útmutató**: a címkézés, a felügyeleti csoportok és a különálló előfizetések használata, ha szükséges, a Azure stream Analytics erőforrások rendszerezéséhez és nyomon követéséhez. Rendszeres időközönként egyeztetheti a leltárt, és gondoskodhat arról, hogy a jogosulatlan erőforrások törlése az előfizetésből időben történjen.

Emellett a Azure Policy használatával korlátozásokat állíthat be az ügyfél-előfizetésekben létrehozható erőforrások típusára a következő beépített szabályzat-definíciók használatával:

- Nem engedélyezett erőforrástípusok

- Engedélyezett erőforrástípusok

- [További Azure-előfizetések létrehozása](../cost-management-billing/manage/create-subscription.md)

- [Management Groups létrehozása](../governance/management-groups/create-management-group-portal.md)

- [Címkék létrehozása és használata](../azure-resource-manager/management/tag-resources.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6,4: a jóváhagyott Azure-erőforrások leltárának meghatározása és karbantartása

**Útmutató**: nem alkalmazható; Ez a javaslat a számítási erőforrások és az Azure egészének fedezésére szolgál.

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="65-monitor-for-unapproved-azure-resources"></a>6,5: a nem jóváhagyott Azure-erőforrások figyelése

**Útmutató**: a Azure Policy használatával korlátozásokat állíthat be az ügyfél-előfizetésekben létrehozható erőforrások típusára a következő beépített szabályzat-definíciók használatával:

- Nem engedélyezett erőforrástípusok

- Engedélyezett erőforrástípusok

Emellett az Azure Resource Graph használatával lekérdezheti vagy felderítheti az előfizetésben (k) belüli erőforrásokat.

- [Az Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md)

- [Lekérdezések létrehozása az Azure Graph használatával](../governance/resource-graph/first-query-portal.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6,6: a nem jóváhagyott szoftveralkalmazások figyelése a számítási erőforrásokon belül

**Útmutató**: nem alkalmazható; Ez a javaslat számítási erőforrások számára készült.

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6,7: nem jóváhagyott Azure-erőforrások és szoftveralkalmazások eltávolítása

**Útmutató**: nem alkalmazható; Ez a javaslat a számítási erőforrások és az Azure egészének fedezésére szolgál.

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="68-use-only-approved-applications"></a>6,8: csak jóváhagyott alkalmazások használata

**Útmutató**: nem alkalmazható; Ez a javaslat számítási erőforrások számára készült.

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="69-use-only-approved-azure-services"></a>6,9: csak jóváhagyott Azure-szolgáltatások használata

**Útmutató**: a Azure Policy használatával korlátozásokat állíthat be az ügyfél-előfizetésekben létrehozható erőforrások típusára a következő beépített szabályzat-definíciók használatával:
- Nem engedélyezett erőforrástípusok
- Engedélyezett erőforrástípusok

- [Az Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md)

- [Adott erőforrástípus megtagadása a következővel Azure Policy](https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#general)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6,10: a jóváhagyott szoftverek leltárának fenntartása

**Útmutató**: nem alkalmazható; Ez a javaslat számítási erőforrások számára készült.

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6,11: korlátozza a felhasználók képességét a Azure Resource Manager való interakcióra

**Útmutató**: az Azure feltételes hozzáférésének konfigurálása a felhasználók "Microsoft Azure felügyelet" alkalmazáshoz való hozzáférésének tiltása a Azure Resource Manager való interakcióra.

- [A feltételes hozzáférés konfigurálása a Azure Resource Managerhoz való hozzáférés blokkolásához](../role-based-access-control/conditional-access-azure-management.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6,12: korlátozza a felhasználók számára a parancsfájlok végrehajtásának lehetőségét a számítási erőforrásokon belül

**Útmutató**: nem alkalmazható; Ez a javaslat számítási erőforrások számára készült.

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6,13: fizikailag vagy logikailag elkülöníthető a nagy kockázatú alkalmazások

**Útmutató**: nem alkalmazható; Ez a javaslat Azure App Service vagy számítási erőforrásokon futó webalkalmazásokhoz készült.

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

## <a name="secure-configuration"></a>Biztonságos konfiguráció

*További információt az [Azure biztonsági teljesítményteszt: biztonságos konfiguráció](../security/benchmarks/security-control-secure-configuration.md)című témakörben talál.*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7,1: biztonságos konfigurációk létrehozása az összes Azure-erőforráshoz

**Útmutató**: Azure Policy aliasok használata a "Microsoft. StreamAnalytics" névtérben egyéni szabályzatok létrehozásához a Azure stream Analytics konfigurációjának naplózásához vagy érvényesítéséhez. Használhatja a Azure Stream Analyticshoz kapcsolódó beépített szabályzat-definíciókat is, például a következőket:

– A Azure Stream Analytics diagnosztikai naplóit engedélyezni kell

- [Az elérhető Azure Policy aliasok megtekintése](/powershell/module/az.resources/get-azpolicyalias)

- [Azure Policy beépített szabályzat-definíciók](../governance/policy/samples/built-in-policies.md)

- [Az Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="72-establish-secure-operating-system-configurations"></a>7,2: biztonságos operációsrendszer-konfigurációk létrehozása

**Útmutató**: nem alkalmazható; Ez a javaslat számítási erőforrások számára készült.

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="73-maintain-secure-azure-resource-configurations"></a>7,3: biztonságos Azure-erőforrás-konfigurációk karbantartása

**Útmutató**: az Azure-erőforrások biztonságos beállításainak betartatásához használja a Azure Policy [deny] és a [telepítés ha nem létezik] lehetőséget.

- [Az Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md)

- [Azure Policy effektusok ismertetése](../governance/policy/concepts/effects.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="74-maintain-secure-operating-system-configurations"></a>7,4: az operációs rendszer biztonságos konfigurációjának fenntartása

**Útmutató**: nem alkalmazható; Ez az útmutató számítási erőforrások számára készült.

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="75-securely-store-configuration-of-azure-resources"></a>7,5: az Azure-erőforrások biztonságos tárolása

**Útmutató**: az Azure Repos használatával biztonságosan tárolhatók és kezelhetők a kódok, beleértve az egyéni Azure-házirendeket, a Azure Resource Manager sablonokat, a kívánt állapotú konfigurációs parancsfájlokat, a felhasználó által definiált függvényeket és lekérdezéseket. Az Azure DevOps-ben kezelt erőforrások eléréséhez engedélyeket adhat meg vagy tagadhat meg bizonyos felhasználók, beépített biztonsági csoportok vagy Azure Active Directory (Azure AD) által meghatározott csoportok számára, ha az Azure DevOps vagy az Azure AD-t integrálja a TFS-mel.

- [Kód tárolása az Azure DevOps](/azure/devops/repos/git/gitworkflow)

- [Engedélyek és csoportok az Azure DevOps](/azure/devops/organizations/security/about-permissions)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="76-securely-store-custom-operating-system-images"></a>7,6: az egyéni operációsrendszer-lemezképek biztonságos tárolása

**Útmutató**: nem alkalmazható; Ez a javaslat számítási erőforrások számára készült.

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7,7: az Azure-erőforrások konfigurációs felügyeleti eszközeinek üzembe helyezése

**Útmutató**: Azure Policy aliasok használata a "Microsoft. StreamAnalytics" névtérben egyéni szabályzatok létrehozásához a riasztáshoz, a naplózáshoz és a rendszer-konfigurációk kényszerítéséhez. Emellett dolgozzon ki egy folyamatot és egy folyamatot a házirend-kivételek kezeléséhez.

- [Az Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="78-deploy-configuration-management-tools-for-operating-systems"></a>7,8: konfigurációs felügyeleti eszközök telepítése operációs rendszerekhez

**Útmutató**: nem alkalmazható; Ez a javaslat számítási erőforrások számára készült.

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7,9: automatikus konfigurációs monitorozás megvalósítása Azure-erőforrásokhoz

**Útmutató**: Azure Policy aliasok használata a "Microsoft. StreamAnalytics" névtérben egyéni szabályzatok létrehozásához a riasztáshoz, a naplózáshoz és a rendszer-konfigurációk kényszerítéséhez. A Azure Stream Analytics erőforrásaihoz tartozó konfigurációk automatikus érvényesítéséhez használja a Azure Policy [audit], [megtagadás] és [üzembe helyezés ha nem létezik] lehetőséget.

- [Az Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7,10: az operációs rendszerek automatikus konfiguráció-figyelésének megvalósítása

**Útmutató**: nem alkalmazható; Ez a javaslat számítási erőforrások számára készült.

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="711-manage-azure-secrets-securely"></a>7,11: az Azure-titkok biztonságos kezelése

**Útmutató**: a stream Analytics-feladatban használt bemeneti vagy kimeneti erőforrások kapcsolati adatait a rendszer a konfigurált Storage-fiókban tárolja. Titkosítsa a Storage-fiókját az összes adatai védelméhez.  Emellett a hitelesítő adatokat rendszeresen el kell forgatni egy Stream Analytics-feladathoz tartozó bemenethez vagy kimenethez.

- [Adatvédelem a Azure Stream Analyticsban](data-protection.md)

- [Bejelentkezési hitelesítő adatok elforgatása egy Stream Analytics-feladathoz tartozó bemenetekhez és kimenetekhez](stream-analytics-login-credentials-inputs-outputs.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="712-manage-identities-securely-and-automatically"></a>7,12: az identitások biztonságos és automatikus kezelése

**Útmutató**: a felügyelt identitások hitelesítése lehetővé teszi stream Analytics feladatok közvetlen elérését a szolgáltatáshoz, beleértve a Power bi, a Storage-fiókot, a kapcsolati karakterlánc használata helyett.

- [Stream Analytics hitelesítése Azure Data Lake Storage Gen1 felügyelt identitások használatával](stream-analytics-managed-identities-adls.md)

- [Felügyelt identitás használata az Azure Stream Analytics-feladatok hitelesítéséhez az Azure Blob Storage kimenetében](blob-output-managed-identity.md)

- [Felügyelt identitás használata a Azure Stream Analytics-feladatok hitelesítéséhez Power BI](powerbi-output-managed-identity.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="713-eliminate-unintended-credential-exposure"></a>7,13: a hitelesítő adatok nem szándékolt expozíciójának megszüntetése

**Útmutató**: hitelesítő adatok beolvasása a programkódon belül a hitelesítő adatok azonosításához. A Credential Scanner a felfedezett hitelesítő adatok biztonságosabb helyre, például az Azure Key Vaultba való áthelyezésére is javaslatot tesz.

- [A hitelesítő adatok beolvasójának beállítása](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

## <a name="malware-defense"></a>Kártevők elleni védelem

*További információt az [Azure biztonsági teljesítményteszt: kártevők elleni védelem](../security/benchmarks/security-control-malware-defense.md)című témakörben talál.*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8,1: központilag felügyelt kártevő szoftverek használata

**Útmutató**: nem alkalmazható; Ez a javaslat számítási erőforrások számára készült.

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8,2: a nem számítási Azure-erőforrásokra feltöltött fájlok előzetes vizsgálata

**Útmutató**: a Microsoft kártevő szoftverrel való ellátása engedélyezve van az Azure-szolgáltatásokat támogató mögöttes gazdagépen (például Azure stream Analytics), azonban nem az ügyfél tartalmán fut.

Az Azure-erőforrásokra feltöltött tartalom előzetes vizsgálata, például App Service, Stream Analytics, Blob Storage stb. A Microsoft nem fér hozzá az adataihoz ezekben a példányokban.

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8,3: a kártevő szoftverek és az aláírások frissítésének ellenőrzése

**Útmutató**: nem alkalmazható; Ez a javaslat számítási erőforrások számára készült.

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

## <a name="data-recovery"></a>Adat-helyreállítás

*További információt az [Azure biztonsági teljesítményteszt: adat-helyreállítás](../security/benchmarks/security-control-data-recovery.md)című témakörben talál.*

### <a name="91-ensure-regular-automated-back-ups"></a>9,1: rendszeres automatizált biztonsági másolatok biztosítása

**Útmutató**: a kiválasztott kimeneti szolgáltatás típusa alapján. a kimeneti adatokra vonatkozó automatizált biztonsági mentést a kimeneti szolgáltatásra vonatkozó ajánlott irányelvek alapján végezheti el.  A belső adatok, beleértve a felhasználó által definiált függvényeket, a lekérdezéseket és az adatpillanatképeket a konfigurált Storage-fiókban tárolják, amelyekről rendszeresen készíthető biztonsági mentés.

A Microsoft Azure Storage-fiókban lévő adatai mindig automatikusan replikálódnak a tartósság és a magas rendelkezésre állás biztosítása érdekében. Az Azure Storage átmásolja az adatait, hogy védve legyen a tervezett és nem tervezett eseményekkel szemben, beleértve az átmeneti hardverhiba, a hálózati vagy áramkimaradások, valamint a súlyos természeti katasztrófákat. Dönthet úgy is, hogy ugyanazon az adatközponton belül replikálja az adatait, többek között az azonos régión belüli és a földrajzilag elkülönített régiók között. 

Az életciklus-kezelési szolgáltatással is elvégezheti az adatok biztonsági mentését az Archívum szintjére.  Emellett a Storage-fiókban tárolt biztonsági másolatok esetében engedélyezze a nem kötelező törlést.

- [Adatvédelem a Azure Stream Analyticsban](https://docs.microsoft.com/azure/stream-analytics/data-protection#private-data-assets-that-are-stored)

- [Az Azure Storage-redundancia és a Service-Level-szerződések ismertetése](../storage/common/storage-redundancy.md)

- [Az Azure Blob Storage-életciklus felügyelete](../storage/blobs/storage-lifecycle-management-concepts.md)

- [Az Azure Storage-blobok helyreállítható törlése](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: teljes rendszerbiztonsági mentés és minden ügyfél által felügyelt kulcs biztonsági mentése

**Útmutató**: a belső adatok, beleértve a felhasználó által definiált függvényeket, a lekérdezéseket, az adatpillanatképeket a konfigurált Storage-fiókban tárolják, amelyekről rendszeresen készíthető biztonsági mentés.

A Storage-fiók által támogatott szolgáltatásokból származó adatok biztonsági mentéséhez több módszer is rendelkezésre áll, beleértve a azcopy vagy a külső gyártótól származó eszközök használatát. Az Azure Blob Storage nem módosítható tárolója lehetővé teszi, hogy a felhasználók üzleti szempontból kritikus fontosságú adatobjektumokat tároljanak egy FÉREGben (egyszer írható, olvasható) állapot. Ez az állapot a felhasználó által megadott intervallumban nem törölhető és nem módosítható adatvesztést tesz lehetővé.

- [Adatvédelem a Azure Stream Analyticsban](https://docs.microsoft.com/azure/stream-analytics/data-protection#private-data-assets-that-are-stored)

- [Bevezetés az AzCopy használatába](../storage/common/storage-use-azcopy-v10.md)

- [BLOB Storage-módosíthatatlansági szabályzatok beállítása és kezelése](../storage/blobs/storage-blob-immutability-policies-manage.md)

Az ügyfél által felügyelt/biztosított kulcsok Azure Key Vault az Azure CLI vagy a PowerShell használatával is készíthetők.

- [Key Vault-kulcsok biztonsági mentése az Azure-ban](/powershell/module/az.keyvault/backup-azkeyvaultkey)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: az összes biztonsági másolat ellenőrzése, beleértve az ügyfél által felügyelt kulcsokat

**Útmutató**: az adatok integritásának teszteléséhez rendszeresen végezze el a biztonsági mentési adatok visszaállítását.

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: a biztonsági másolatok és az ügyfél által felügyelt kulcsok védelmének biztosítása

**Útmutató**: stream Analytics az Azure Storage-ban tárolt biztonsági mentések alapértelmezés szerint nem kapcsolhatók ki.  A biztonsági mentéseket bizalmas adatokként kell kezelni, és az alapkonfiguráció részeként alkalmazni kell a megfelelő hozzáférési és adatvédelmi vezérlőket.  

- [Adatvédelem a Azure Stream Analyticsban](https://docs.microsoft.com/azure/stream-analytics/data-protection#private-data-assets-that-are-stored)

- [Az Azure Storage-beli adathozzáférés engedélyezése](../storage/common/storage-auth.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

## <a name="incident-response"></a>Incidensmegoldás

*További információ: [Azure Security Benchmark: Incidensek kezelése](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10,1: incidens-válaszi útmutató létrehozása

**Útmutató**: Alakítson ki incidenskezelési útmutatót a vállalat számára. Gondoskodjon írásos incidenskezelési tervekről, amelyek definiálják az összes résztvevő szerepkörét, valamint az incidenskezelés fázisait az észleléstől az incidens utáni értékelésig.

- [Útmutató a saját biztonsági incidensek megoldási folyamatának létrehozásához](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Microsoft Security Response Center – incidens anatómiája](https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/)

- [Az ügyfél a NIST számítógépes biztonsági incidensek kezelési útmutatóját is kihasználhatja a saját incidens-válasz tervének létrehozásával kapcsolatos támogatáshoz](https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10,2: incidensek pontozásának és rangsorolási eljárásának létrehozása

**Útmutató**: a Security Center súlyosságot rendel az egyes riasztásokhoz, hogy a prioritások alapján ki lehessen deríteni, hogy mely riasztásokat kell először megvizsgálni. A súlyosság azon alapul, hogy az Security Center milyen mértékben szerepel a riasztások kijavításához használt mérőszámban, illetve a riasztást eredményező tevékenységen belül rosszindulatú szándékkal. 

Emellett egyértelműen megjelölheti az előfizetéseket (pl.: éles környezetben, nem gyártva) címkék használatával és elnevezési rendszer létrehozása az Azure-erőforrások egyértelmű azonosításához és kategorizálásához, különösen a bizalmas adatok feldolgozásához.  Az Ön felelőssége, hogy rangsorolja a riasztások megoldását azon Azure-erőforrások és -környezetek kritikussága alapján, ahol az incidens történt.

- [Biztonsági riasztások az Azure Security Centerben](../security-center/security-center-alerts-overview.md)

- [Címkék használata az Azure-erőforrások rendszerezéséhez](../azure-resource-manager/management/tag-resources.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="103-test-security-response-procedures"></a>10,3: biztonsági reagálási eljárások tesztelése

**Útmutató**: az Azure-erőforrások védelmének biztosítása érdekében a rendszer az incidensek reagálási képességeinek rendszeres tesztelésére szolgáló gyakorlatokat hajt végre. Azonosítsa a gyenge pontokat és réseket, és szükség esetén dolgozza át a tervet.

- [Tekintse meg a NIST kiadványát: útmutató az IT-csomagok és-képességek teszteléséhez, betanításához és edzésprogramjának kidolgozásához](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10,4: biztonsági incidensek elérhetőségének biztosítása és riasztási értesítések konfigurálása biztonsági incidensekhez

**Útmutató**: a Microsoft a biztonsági incidensek elérhetőségi adatait arra használja fel, hogy felvegye Önnel a kapcsolatot, ha a Microsoft Security Response Center (MSRC) felfedi, hogy az adatokat egy törvénytelen vagy jogosulatlan fél is hozzáférte. A problémák megoldása érdekében tekintse át az incidenseket a tény után.

- [A Azure Security Center biztonsági kapcsolattartó beállítása](../security-center/security-center-provide-security-contact-details.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10,5: biztonsági riasztások beépítése az incidensek gyorsreagáló rendszerébe

**Útmutató**: az Azure Security Center-riasztások és javaslatok exportálása a folyamatos exportálás funkcióval az Azure-erőforrásokkal kapcsolatos kockázatok azonosítása érdekében. A folyamatos exportálás lehetővé teszi a riasztások és javaslatok manuális és folyamatos exportálását. Az Azure Security Center adatösszekötővel továbbíthatja a riasztásokat az Azure Sentinel szolgáltatásba.

- [Folyamatos exportálás konfigurálása](../security-center/continuous-export.md)

- [Riasztások streamelése az Azure Sentinelbe](../sentinel/connect-azure-security-center.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="106-automate-the-response-to-security-alerts"></a>10,6: a biztonsági riasztásokra adott válasz automatizálása

**Útmutató**: a Azure Security Center munkafolyamat-automatizálási funkciója segítségével automatikusan elindíthatja a válaszokat az "Logic apps" használatával a biztonsági riasztások és az Azure-erőforrások védelme érdekében javasolt javaslatok alapján.

- [A Munkafolyamat-automatizálás és a Logic Apps konfigurálása](../security-center/workflow-automation.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

## <a name="penetration-tests-and-red-team-exercises"></a>Behatolási tesztek és Red Team-gyakorlatok

*További információkért tekintse meg az [Azure biztonsági teljesítményteszt: behatolási tesztek és a Red Team gyakorlatok](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md)című témakört.*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11,1: az Azure-erőforrások rendszeres behatolásának tesztelése, valamint az összes kritikus biztonsági vizsgálat szervizelésének biztosítása

**Útmutató**: kövesse a Microsoft részvételi szabályait, hogy a behatolási tesztek ne sértsék a Microsoft-házirendeket: https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1 

- [További információt a Microsoft által felügyelt felhőalapú infrastruktúrával, szolgáltatásokkal és alkalmazásokkal kapcsolatos, a Microsoft által felügyelt felhő-infrastruktúra,-szolgáltatások és-alkalmazások fejlesztésére vonatkozó stratégiáról és végrehajtásáról itt talál.](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Felelősség**: Megosztott

**Azure Security Center figyelés**: nincs

## <a name="next-steps"></a>Következő lépések

- [Az Azure Security Benchmark v2 áttekintésének](/azure/security/benchmarks/overview) megtekintése
- További tudnivalók az [Azure biztonsági alapterveiről](/azure/security/benchmarks/security-baselines-overview)
