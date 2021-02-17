---
title: Azure biztonsági alapkonfiguráció az Azure Private linkhez
description: Az Azure Private link Security alapkonfigurációja eljárási útmutatást és erőforrásokat biztosít az Azure biztonsági Teljesítménytesztben meghatározott biztonsági javaslatok megvalósításához.
author: msmbaldwin
ms.service: private-link
ms.topic: conceptual
ms.date: 09/08/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 692b52e8ea6a88a119ca98985864cb9e1daa14c3
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/17/2021
ms.locfileid: "100595748"
---
# <a name="azure-security-baseline-for-azure-private-link"></a>Azure biztonsági alapkonfiguráció az Azure Private linkhez

Ez a biztonsági alapkonfiguráció az [Azure biztonsági teljesítménytesztből](../security/benchmarks/overview.md) az Azure Private linkre irányuló útmutatást alkalmazza. Az Azure Security Benchmark ajánlásokat ad arra nézve, hogy hogyan tehetők biztonságossá a felhőalapú megoldások az Azure-ban. A tartalom az Azure biztonsági teljesítményteszt által meghatározott **biztonsági vezérlők** és az Azure Private linkre vonatkozó kapcsolódó útmutatás szerint van csoportosítva. Az Azure Private-hivatkozásra nem alkalmazható **vezérlők** ki lettek zárva. Ha szeretné megtekinteni, hogy az Azure-beli privát kapcsolat hogyan teljes mértékben leképezi az Azure biztonsági Teljesítménytesztét, tekintse meg a [teljes azure Virtual Network biztonsági alapterv-megfeleltetés](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)

## <a name="network-security"></a>Hálózati biztonság

*További információt az [Azure biztonsági teljesítményteszt: hálózati biztonság](../security/benchmarks/security-control-network-security.md)című témakörben talál.*

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1,11: automatikus eszközök használata a hálózati erőforrások konfigurációjának figyelésére és a változások észlelésére

**Útmutató**: az Azure-tevékenység naplójának használatával figyelheti az erőforrás-konfigurációkat, és azonosíthatja a privát hivatkozáshoz kapcsolódó hálózati erőforrások módosításait. 

Hozzon létre riasztásokat a Azure Monitoron belül, amelyek akkor lépnek életbe, amikor a kritikus erőforrások változásai megváltoznak.

- [Azure-Tevékenységnaplók eseményeinek megtekintése és lekérése](../azure-monitor/essentials/activity-log.md#view-the-activity-log)

- [Riasztások létrehozása a Azure Monitorban](../azure-monitor/alerts/alerts-activity-log.md)

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

## <a name="logging-and-monitoring"></a>Naplózás és figyelés

*További információt az [Azure biztonsági teljesítményteszt: naplózás és figyelés](../security/benchmarks/security-control-logging-monitoring.md)című témakörben talál.*

### <a name="22-configure-central-security-log-management"></a>2,2: a központi biztonsági naplók felügyeletének konfigurálása

**Útmutató**: naplók beolvasása Azure monitor használatával a hálózati erőforrások, például a privát kapcsolati végpontok, a virtuális hálózatok és a hálózati biztonsági csoportok által generált biztonsági adatokat összesítve. 

A Azure Monitoron belül Log Analytics munkaterületek használatával kérdezheti le és végezheti el az elemzéseket, és használhatja az Azure Storage-fiókokat a hosszú távú/archiválási tároláshoz.

Emellett a szervezeti üzleti követelmények alapján engedélyezheti és elküldheti az Azure Sentinel vagy egy harmadik féltől származó SIEM-nek szánt adatait.

- [Az Azure Sentinel előkészítése](../sentinel/quickstart-onboard.md)

- [Platform-naplók és-metrikák összegyűjtése Azure Monitor](../azure-monitor/essentials/diagnostic-settings.md)

- [Naplózás és figyelés privát kapcsolathoz](private-link-overview.md#logging-and-monitoring)

- [Hálózati biztonsági csoport diagnosztikai naplózása](../virtual-network/virtual-network-nsg-manage-log.md)

- [Ismerkedés a Azure Monitor és a harmadik féltől származó SIEM-integrációval](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="23-enable-audit-logging-for-azure-resources"></a>2,3: az Azure-erőforrások naplózásának engedélyezése

**Útmutatás**: engedélyezze a Azure monitor tevékenységi naplókat, amelyek a privát kapcsolati erőforrásokon végrehajtott naplózási műveleteket, például a műveletet elindította, a művelet bekövetkeztekor, a művelet állapotát és más hasznos naplózási információkat. 

- [Platform-naplók és-metrikák összegyűjtése Azure Monitor](../azure-monitor/essentials/diagnostic-settings.md)

- [Azure-Tevékenységnaplók eseményeinek megtekintése és lekérése](../azure-monitor/essentials/activity-log.md#view-the-activity-log)

- [Naplózás és figyelés privát kapcsolathoz](private-link-overview.md#logging-and-monitoring)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="25-configure-security-log-storage-retention"></a>2,5: a biztonsági napló tárolási adatmegőrzésének konfigurálása

**Útmutató**: a privát hivatkozáshoz kapcsolódó naplók esetében állítsa be a log Analytics munkaterület megőrzési időtartamát a szervezet megfelelőségi szabályainak megfelelően Azure monitor belül. Használja az Azure Storage-fiókokat a naplók hosszú távú/archiválási tárolására.

- [Az adatmegőrzési időszak módosítása Log Analytics](../azure-monitor/logs/manage-cost-storage.md#change-the-data-retention-period)

- [Adatmegőrzési szabályzat konfigurálása az Azure Storage-fiók naplóihoz](../storage/common/storage-monitor-storage-account.md#configure-logging)

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="26-monitor-and-review-logs"></a>2,6: naplók figyelése és áttekintése

**Útmutató**: a rendellenes viselkedésre és a rendszeres eredményekre vonatkozó naplók elemzése és figyelése. Az Azure Monitor Log Analytics munkaterülete segítségével tekintheti át a naplókat, és lekérdezéseket végezhet a naplózási adatokon.

Egy másik lehetőség, hogy lehetővé tegye az Azure Sentinel vagy egy harmadik fél által készített SIEM-hez való adatfeldolgozást.

- [Az Azure Sentinel előkészítése](../sentinel/quickstart-onboard.md)

- [Log Analytics munkaterület ismertetése](../azure-monitor/logs/log-analytics-tutorial.md)

- [Egyéni lekérdezések végrehajtása a Azure Monitorban](../azure-monitor/logs/get-started-queries.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="27-enable-alerts-for-anomalous-activities"></a>2,7: riasztások engedélyezése rendellenes tevékenységekhez

**Útmutató**: a biztonsági naplókban és eseményekben talált rendellenes tevékenységek figyelésére és riasztására log Analytics munkaterülettel konfigurált Security Center használata.

A szervezeti üzleti követelmények alapján engedélyezheti és elküldheti az Azure Sentinel vagy egy harmadik féltől származó SIEM-et.

- [Az Azure Sentinel előkészítése](../sentinel/quickstart-onboard.md)

- [Riasztások kezelése Azure Security Centerban](../security-center/security-center-managing-and-responding-alerts.md)

- [Riasztás a log Analytics-naplófájlok adatkezeléséről](../azure-monitor/alerts/tutorial-response.md)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

## <a name="identity-and-access-control"></a>Identitás- és hozzáférés-vezérlés

*További információt az [Azure biztonsági teljesítményteszt: identitás-és hozzáférés-vezérlés](../security/benchmarks/security-control-identity-access-control.md)című témakörben talál.*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3,1: a felügyeleti fiókok leltárának karbantartása

**Útmutató**: használjon Azure Active Directory (Azure ad) beépített rendszergazdai szerepköröket, amelyek explicit módon rendelhetők hozzá és kérhetők le. Az Azure AD PowerShell-modul futtatásával ad hoc lekérdezéseket hajthat végre a felügyeleti csoportok tagjait futtató fiókok felderítéséhez.

- [Címtárbeli szerepkör beszerzése az Azure AD-ben a PowerShell-lel](/powershell/module/azuread/get-azureaddirectoryrole)

- [Címtárbeli szerepkör tagjainak beszerzése az Azure AD-ben a PowerShell-lel](/powershell/module/azuread/get-azureaddirectoryrolemember)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="33-use-dedicated-administrative-accounts"></a>3,3: dedikált rendszergazdai fiókok használata

**Útmutató**: szabványos üzemeltetési eljárások létrehozása a dedikált rendszergazdai fiókok használata körül. A rendszergazdai fiókok számának figyeléséhez használja a Security Center identitás-és hozzáférés-kezelési funkcióit.

Emellett engedélyezze az igény szerinti vagy elég egyszerűen elérhető hozzáférést a Azure Active Directory (Azure AD) Privileged Identity Management a Microsoft-szolgáltatásokhoz tartozó Kiemelt szerepkörök és a Azure Resource Manager használatával.

- [További információ a Privileged Identity Management](../active-directory/privileged-identity-management/index.yml)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="34-use-azure-active-directory-single-sign-on-sso"></a>3,4: Azure Active Directory egyszeri bejelentkezés (SSO) használata

**Útmutató**: ha lehetséges, használja az egyszeri bejelentkezést Azure Active Directory ahelyett, hogy egyéni, különálló hitelesítő adatokat konfiguráljon a szolgáltatáshoz.

- [Egyszeri bejelentkezés a Azure Active Directory alkalmazásaiba](../active-directory/manage-apps/what-is-single-sign-on.md)

- [Identitás és hozzáférés figyelése Azure Security Centeron belül](../security-center/security-center-identity-access.md)

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3,5: Multi-Factor Authentication használata az összes Azure Active Directory-alapú hozzáféréshez

**Útmutató**: engedélyezze az Azure Active Directory (Azure ad) multi-Factor Authentication (MFA) használatát, és kövesse Security Center identitás-és hozzáférés-kezelési javaslatait.

- [MFA engedélyezése az Azure-ban](../active-directory/authentication/howto-mfa-getstarted.md)

- [Identitás és hozzáférés figyelése Azure Security Centeron belül](../security-center/security-center-identity-access.md)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="36-use-secure-azure-managed-workstations-for-administrative-tasks"></a>3,6: biztonságos, Azure által felügyelt munkaállomások használata felügyeleti feladatokhoz

**Útmutató**: az Azure hálózati erőforrásainak bejelentkezéséhez és konfigurálásához multi-Factor Authentication konfigurált emelt szintű hozzáférési munkaállomás (Paw) használata.

- [További tudnivalók a privilegizált hozzáférésű munkaállomásokról](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

- [MFA engedélyezése az Azure-ban](../active-directory/authentication/howto-mfa-getstarted.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3,7: naplózás és riasztás a gyanús tevékenységekről a rendszergazdai fiókoktól

**Útmutató**: az Azure Active Directory (Azure ad) kockázati észlelési funkciójának használatával megtekintheti a kockázatos felhasználói viselkedésre vonatkozó riasztásokat és jelentéseket. Security Center kockázat észlelésével kapcsolatos riasztások betöltése Azure Monitor és egyéni riasztások/értesítések konfigurálása a műveleti csoportokkal.

- [Azure Security Center kockázati észlelések ismertetése (gyanús tevékenység)](../active-directory/identity-protection/overview-identity-protection.md)

- [Azure-tevékenységnaplók integrálása az Azure Monitorba](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

- [Műveleti csoportok konfigurálása egyéni riasztásokhoz és értesítésekhez](../azure-monitor/alerts/action-groups.md)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3,8: az Azure-erőforrások kezelése csak jóváhagyott helyekről

**Útmutató**: a feltételes hozzáférés elnevezett helyeivel engedélyezheti, hogy a hozzáférés csak az IP-címtartományok vagy országok/régiók adott logikai csoportjaihoz legyen elérhető.

- [Elnevezett helyszínek konfigurálása az Azure-ban](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="39-use-azure-active-directory"></a>3,9: a Azure Active Directory használata

**Útmutató**: a Azure Active Directory (Azure ad) használata központi hitelesítési és engedélyezési rendszerrel. Az Azure AD az adatok védelme érdekében erős titkosítást használ a nyugalmi és a továbbítási adatokhoz, valamint a felhasználók hitelesítő adatainak tárolásához és kiszűréséhez is.  

- [Azure AD-példány létrehozása és konfigurálása](../active-directory-domain-services/tutorial-create-instance.md)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="310-regularly-review-and-reconcile-user-access"></a>3,10: a felhasználói hozzáférés rendszeres áttekintése és egyeztetése

**Útmutató**: a Azure Active Directory naplókat biztosít az elavult fiókok felderítéséhez. Emellett az Azure Identity Access Reviews használatával hatékonyan kezelheti a csoporttagságok kezelését, a vállalati alkalmazásokhoz való hozzáférést és a szerepkör-hozzárendeléseket. A felhasználói hozzáférés rendszeresen felülvizsgálható, hogy csak a megfelelő felhasználók férhessenek hozzá.

- [Az Azure AD jelentéskészítés ismertetése](../active-directory/reports-monitoring/index.yml)

- [Az Azure Identity hozzáférési felülvizsgálatok használata](../active-directory/governance/access-reviews-overview.md)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3,11: a figyelő megkísérli a deaktivált hitelesítő adatok elérését

**Útmutató**: az Azure Active Directory (Azure ad) bejelentkezési tevékenység, naplózás és kockázati Eseménynapló-források használata az összes Siem/monitoring eszközzel való integráláshoz.

Az Azure AD-felhasználói fiókok diagnosztikai beállításainak létrehozásával és a naplók és bejelentkezési naplók Log Analytics munkaterületre való elküldésével egyszerűsítheti ezt a folyamatot. A kívánt riasztások konfigurálása Log Analytics munkaterületen belül.

- [Azure-tevékenységnaplók integrálása az Azure Monitorba](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3,12: riasztás a fiók bejelentkezési viselkedésének eltérése esetén

**Útmutató**: a Azure Active Directory (Azure ad) kockázati és Identity Protection-funkciókkal automatizált válaszokat konfigurálhat a hálózati erőforrások felhasználói identitásával kapcsolatos gyanús műveletekre. 

További vizsgálat céljából betöltheti az Azure Sentinelbe.

- [Az Azure AD kockázatos bejelentkezéseinek megtekintése](../active-directory/identity-protection/overview-identity-protection.md)

- [Az Identity Protection kockázati házirendjeinek konfigurálása és engedélyezése](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [Az Azure Sentinel előkészítése](../sentinel/quickstart-onboard.md)

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

## <a name="data-protection"></a>Adatvédelem

*További információt az [Azure biztonsági teljesítményteszt: Adatvédelem](../security/benchmarks/security-control-data-protection.md)című témakörben talál.*

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4,2: bizalmas adatok tárolására vagy feldolgozására szolgáló rendszerek elkülönítése

**Útmutató**: az elkülönítés megvalósítása különálló előfizetések és felügyeleti csoportok használatával az egyes biztonsági tartományokhoz, például a környezeti típusokhoz és az adatérzékeny szintekhez. 

Az üzleti követelmények alapján korlátozhatja az Azure-erőforrásokhoz való hozzáférés szintjét az alkalmazásokkal és a vállalati környezetekkel. 

Azure-erőforrásokhoz való hozzáférés szabályozása az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC) használatával.

- [További Azure-előfizetések létrehozása](../cost-management-billing/manage/create-subscription.md)

- [Management Groups létrehozása](../governance/management-groups/create-management-group-portal.md)

- [Címkék létrehozása és használata](../azure-resource-manager/management/tag-resources.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4,4: minden bizalmas adat titkosítása az átvitel során

**Útmutató**: az összes bizalmas adat titkosítása az átvitel során. Győződjön meg arról, hogy a virtuális hálózatokban lévő Azure-erőforrásokhoz csatlakozó ügyfelek képesek legyenek egyeztetni a TLS 1,2-es vagy újabb verzióit. A magánhálózati hivatkozás nem zavarja a mögöttes protokollokat. Ajánlott eljárások használata az ügyfelek által használt egyéb ajánlatokhoz.

Kövesse Security Center a inaktív adatok titkosítására és az átvitel közbeni titkosításra vonatkozó ajánlásokat, ahol lehetséges.

- [A titkosítás ismertetése az Azure-ban](../security/fundamentals/encryption-overview.md#encryption-of-data-in-transit)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Megosztott

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4,6: az erőforrásokhoz való hozzáférés szabályozása az Azure RBAC

**Útmutató**: az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC) használata az adat-és erőforrásokhoz való hozzáférés szabályozására, máskülönben a szolgáltatás-specifikus hozzáférés-vezérlési módszerek használata.

- [Ismerkedjen meg rövid leírással és az Azure beépített szerepköreinek egyedi azonosítójával](../role-based-access-control/built-in-roles.md)

A környezetében lévő szerepkörök listájának lekéréséhez hívja meg a "Get-AzRoleDefinition" vagy az "az role definition List" PowerShell-parancsot.

Tekintse át a lehetőségeket, hogy a szolgáltatás expozícióját a "láthatóság" beállítással szabályozhatja. a privát hivatkozásban. Ezek a láthatósági beállítások határozzák meg, hogy a fogyasztó tud-e csatlakozni a szolgáltatáshoz. 

A szolgáltatását saját maga is kihasználhatja a többi virtuális hálózatról (csak Azure RBAC engedélyek esetén). Korlátozza az expozíciót a megbízható előfizetések korlátozott készletére, vagy tegye közzé azt úgy, hogy az összes Azure-előfizetés kapcsolatot igényelhet a privát kapcsolati szolgáltatásban.

- [Az Azure RBAC konfigurálása](../role-based-access-control/role-assignments-portal.md)

- [A Private link Service tulajdonságai](private-link-service-overview.md)

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4,9: a kritikus Azure-erőforrások változásainak naplózása és riasztása

**Útmutató**: a Azure monitor műveletnapló riasztásai segítségével riasztásokat hozhat létre, amikor a módosítások olyan kritikus Azure-erőforrásokra vonatkoznak, mint például a Private link Services és a végpontok. 

- [Hálózati biztonsági csoport diagnosztikai naplózása](private-link-overview.md#logging-and-monitoring)

- [Riasztások létrehozása az Azure-tevékenységek naplózási eseményeihez](../azure-monitor/alerts/alerts-activity-log.md)

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

## <a name="inventory-and-asset-management"></a>Leltár- és eszközfelügyelet

*További információt az [Azure biztonsági teljesítményteszt: leltár és eszközkezelés](../security/benchmarks/security-control-inventory-asset-management.md)című témakörben talál.*

### <a name="61-use-automated-asset-discovery-solution"></a>6,1: automatikus eszköz-felderítési megoldás használata

**Útmutató**: az Azure Resource Graph segítségével lekérdezheti és felderítheti az összes hálózati erőforrást, például a privát kapcsolati szolgáltatásokat és az előfizetésekben található végpontokat. 

Győződjön meg arról, hogy megfelelő (olvasási) engedélyekkel rendelkezik a bérlőben, és képes felsorolni az összes Azure-előfizetést, valamint az előfizetésében lévő erőforrásokat.

- [Lekérdezések létrehozása az Azure Graph használatával](../governance/resource-graph/first-query-portal.md)

- [Azure-előfizetések megtekintése](/powershell/module/az.accounts/get-azsubscription)

- [Az Azure RBAC ismertetése](../role-based-access-control/overview.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="62-maintain-asset-metadata"></a>6,2: az eszköz metaadatainak fenntartása

**Útmutató**: címkék alkalmazása az Azure-erőforrásokra metaadatok használatával logikailag rendszerezheti őket egy besorolásban.

- [Címkék létrehozása és használata](../azure-resource-manager/management/tag-resources.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="63-delete-unauthorized-azure-resources"></a>6,3: jogosulatlan Azure-erőforrások törlése

**Útmutató**: a címkézés, a felügyeleti csoportok és a különálló előfizetések használata, ahol szükséges, megszervezheti és nyomon követheti a magánjellegű hivatkozásokat és a kapcsolódó erőforrásokat. 

Rendszeres időközönként egyeztetheti a leltárt, és gondoskodhat arról, hogy a jogosulatlan erőforrások törlése az előfizetésből időben történjen.

- [További Azure-előfizetések létrehozása](../cost-management-billing/manage/create-subscription.md)

- [Management Groups létrehozása](../governance/management-groups/create-management-group-portal.md)

- [Címkék létrehozása és használata](../azure-resource-manager/management/tag-resources.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6,4: a jóváhagyott Azure-erőforrások leltárának meghatározása és karbantartása

**Útmutató**: jóváhagyott Azure-erőforrások és-szoftverek leltárának létrehozása a számítási erőforrásokhoz a szervezeti igények alapján.

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="65-monitor-for-unapproved-azure-resources"></a>6,5: a nem jóváhagyott Azure-erőforrások figyelése

**Útmutató**: a Azure Policy használatával korlátozásokat állíthat be az ügyfél-előfizetésekben létrehozható erőforrások típusára a következő beépített szabályzat-definíciók használatával:

- Nem engedélyezett erőforrástípusok

- Engedélyezett erőforrástípusok

Az Azure Resource Graph használatával az előfizetéseken belüli erőforrásokat is lekérdezheti vagy felderítheti. Ez segíthet a magas biztonságú környezetekben, például a Storage-fiókokkal.

- [Az Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md)

- [Lekérdezések létrehozása az Azure Graph használatával](../governance/resource-graph/first-query-portal.md)

- [Azure Policy-minta beépített privát hivatkozás](../governance/policy/samples/built-in-policies.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="67-remove-unapproved-azure-resources"></a>6,7: nem jóváhagyott Azure-erőforrások eltávolítása

**Útmutató**: az ügyfél meggátolhatja az erőforrások létrehozását és használatát Azure Policy az ügyfél céges szabályzata által megkövetelt módon. A jogosulatlan erőforrások eltávolításához saját folyamatot is alkalmazhat.

- [Az Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="69-use-only-approved-azure-services"></a>6,9: csak jóváhagyott Azure-szolgáltatások használata

**Útmutató**: a Azure Policy használatával korlátozásokat állíthat be az ügyfél-előfizetésekben létrehozható erőforrások típusára a következő beépített szabályzat-definíciók használatával:

- Nem engedélyezett erőforrástípusok

- Engedélyezett erőforrástípusok

- [Az Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md)

- [Adott erőforrástípus megtagadása a következővel Azure Policy](../governance/policy/samples/index.md)

- [Azure Policy-minta beépített privát hivatkozás](../governance/policy/samples/built-in-policies.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6,11: korlátozza a felhasználók képességét a Azure Resource Manager való interakcióra

**Útmutató**: az Azure feltételes hozzáférés használatával korlátozhatja a felhasználók a Azure Resource Manager interakcióját a "Microsoft Azure felügyelet" alkalmazás "hozzáférés tiltása" beállításával.

- [A feltételes hozzáférés konfigurálása a Azure Resource Managerhoz való hozzáférés blokkolásához](../role-based-access-control/conditional-access-azure-management.md)

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

## <a name="secure-configuration"></a>Biztonságos konfiguráció

*További információt az [Azure biztonsági teljesítményteszt: biztonságos konfiguráció](../security/benchmarks/security-control-secure-configuration.md)című témakörben talál.*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7,1: biztonságos konfigurációk létrehozása az összes Azure-erőforráshoz

**Útmutató**: Azure Policy Aliasok használatával egyéni szabályzatokat hozhat létre az Azure-beli hálózati erőforrások konfigurációjának naplózásához vagy betartatásához a beépített Azure Policy-definíciókkal együtt.

A Azure Resource Manager képes a sablon exportálására JavaScript Object Notation (JSON). Ezt az összetevőt át kell tekinteni annak biztosítására, hogy a konfigurációk megfelelnek vagy meghaladják a szervezete biztonsági követelményeit.

A Security Center ajánlásainak megvalósítása az Azure-erőforrások biztonságos alapkonfigurációjának megfelelően.

- [Az elérhető Azure Policy aliasok megtekintése](/powershell/module/az.resources/get-azpolicyalias)

- [Oktatóanyag: szabályzatok létrehozása és kezelése a megfelelőség kikényszerítés érdekében](../governance/policy/tutorials/create-and-manage.md)

- [Azure Policy-minta beépített privát hivatkozás](../governance/policy/samples/built-in-policies.md)

- [Egy-és többerőforrásos exportálás Azure Portal sablonba](../azure-resource-manager/templates/export-template-portal.md)

- [Biztonsági javaslatok – gyorsútmutató](../security-center/recommendations-reference.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="73-maintain-secure-azure-resource-configurations"></a>7,3: biztonságos Azure-erőforrás-konfigurációk karbantartása

**Útmutató**: Azure Resource Manager sablonok és Azure Policy használata a privát kapcsolatokhoz és kapcsolódó erőforrásokhoz társított Azure-erőforrások biztonságos konfigurálásához.  

Azure Resource Manager sablonok az Azure-erőforrások üzembe helyezéséhez használt JavaScript Object Notation (JSON) alapú fájlok, minden egyéni sablont biztonságosan kell tárolni és karbantartani egy adattárban. 

Az Azure-szabályzat [deny] és a [telepítés, ha nem létezik] használatával kényszerítheti ki az Azure-erőforrások biztonságos beállításait.

- [Azure Resource Manager sablonok létrehozásával kapcsolatos információk](../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md)

- [Az Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md)

- [Azure Policy effektusok ismertetése](../governance/policy/concepts/effects.md)

- [A privát végpontokhoz Azure Resource Manager sablon mintái](/azure/templates/microsoft.network/2019-11-01/privateendpoints)

- [Azure Policy-minta beépített privát hivatkozás](../governance/policy/samples/built-in-policies.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="75-securely-store-configuration-of-azure-resources"></a>7,5: az Azure-erőforrások biztonságos tárolása

**Útmutató**: az Azure DevOps segítségével biztonságosan tárolhatja és kezelheti a kódokat, például az egyéni Azure-szabályzatokat, Azure Resource Manager sablonokat és a kívánt állapotú konfigurációs parancsfájlokat. 

Engedélyek megadása vagy megtagadása adott felhasználók, beépített biztonsági csoportok vagy Azure Active Directory (Azure AD) által definiált csoportok számára, ha az Azure-DevOps integrálva van a hozzáféréshez, vagy Active Directory, ha az integrálva van Team Foundation Server.

- [Kód tárolása az Azure DevOps](/azure/devops/repos/git/gitworkflow)

- [Engedélyek és csoportok az Azure DevOps](/azure/devops/organizations/security/about-permissions)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7,7: az Azure-erőforrások konfigurációs felügyeleti eszközeinek üzembe helyezése

**Útmutató**: az Azure-erőforrások szabványos biztonsági konfigurációinak definiálása és implementálása Azure Policy használatával. 

Az Azure Policy Aliasok használatával egyéni szabályzatokat hozhat létre az Azure-erőforrások hálózati konfigurációjának naplózásához vagy érvénybe léptetéséhez. 

Az előfizetések alatt felügyelt konkrét erőforrásokhoz kapcsolódó beépített szabályzat-definíciókat is használja.

- [Az Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md)

- [Aliasok használata](../governance/policy/concepts/definition-structure.md#aliases)

- [Azure Policy-minta beépített privát hivatkozás](../governance/policy/samples/built-in-policies.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7,9: automatikus konfigurációs monitorozás megvalósítása Azure-erőforrásokhoz

**Útmutató**: az Azure-erőforrások konfigurációinak naplózása Azure Policy használatával. A Azure Policy használható például olyan erőforrások észlelésére, amelyek nincsenek privát végponttal konfigurálva.

- [Az Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md)

- [Azure Policy-minta beépített privát hivatkozás](../governance/policy/samples/built-in-policies.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

## <a name="data-recovery"></a>Adat-helyreállítás

*További információt az [Azure biztonsági teljesítményteszt: adat-helyreállítás](../security/benchmarks/security-control-data-recovery.md)című témakörben talál.*

### <a name="91-ensure-regular-automated-back-ups"></a>9,1: rendszeres automatizált biztonsági másolatok biztosítása

**Útmutató**: a Azure Resource Manager használatával telepítheti a privát kapcsolati szolgáltatásokat, a végpontokat és a kapcsolódó erőforrásokat. A Azure Resource Manager lehetővé teszi a sablonok exportálását, amelyek biztonsági másolatként használhatók a privát kapcsolati végpontok és a kapcsolódó erőforrások visszaállításához. 

A Azure Automation használatával rendszeresen meghívhatja a Azure Resource Manager sablon exportálási API-ját.

- [Az Azure Resource Manager áttekintése](../azure-resource-manager/management/overview.md)

- [A privát végpontokhoz Azure Resource Manager sablon mintái](/azure/templates/microsoft.network/2019-11-01/privateendpoints)

- [Egy-és többerőforrásos exportálás Azure Portal sablonba](../azure-resource-manager/templates/export-template-portal.md)

- [Erőforráscsoportok – sablon exportálása](/rest/api/resources/resourcegroups/exporttemplate)

- [Bevezetés a Azure Automationba](../automation/automation-intro.md)

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: teljes rendszerbiztonsági mentés és minden ügyfél által felügyelt kulcs biztonsági mentése

**Útmutató**: a Azure Resource Manager használatával telepítheti a privát kapcsolati szolgáltatásokat, a végpontokat és a kapcsolódó erőforrásokat. A Azure Resource Manager lehetővé teszi a sablonok exportálását, amelyek biztonsági másolatként használhatók a privát kapcsolati végpontok és a kapcsolódó erőforrások visszaállításához.  

A Azure Automation használatával rendszeresen meghívhatja a Azure Resource Manager sablon exportálási API-ját.  

Az ügyfél által felügyelt kulcsok biztonsági mentése Azure Key Vaulton belül.

- [Az Azure Resource Manager áttekintése](../azure-resource-manager/management/overview.md)

- [A privát végpontokhoz Azure Resource Manager sablon mintái](/azure/templates/microsoft.network/2019-11-01/privateendpoints)

- [Egy-és többerőforrásos exportálás Azure Portal sablonba](../azure-resource-manager/templates/export-template-portal.md)

- [Erőforráscsoportok – sablon exportálása](/rest/api/resources/resourcegroups/exporttemplate)

- [Bevezetés a Azure Automationba](../automation/automation-intro.md)

- [Key Vault-kulcsok biztonsági mentése az Azure-ban](/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey)

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: az összes biztonsági másolat ellenőrzése, beleértve az ügyfél által felügyelt kulcsokat

**Útmutató**: Ellenőrizze, hogy az üzleti igényeknek megfelelően rendszeresen végrehajtja-e a Azure Resource Manager-sablonok üzembe helyezését az elkülönített előfizetésre. 

Emellett érvényesítheti az ügyfél által felügyelt kulcsok biztonsági másolatának visszaállítását is.

- [Erőforrások üzembe helyezése ARM-sablonokkal és Azure Portal](../azure-resource-manager/templates/deploy-portal.md)

- [Key Vault-kulcsok visszaállítása az Azure-ban](/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey)

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: a biztonsági másolatok és az ügyfél által felügyelt kulcsok védelmének biztosítása

**Útmutató**: az Azure DevOps segítségével biztonságosan tárolhatja és kezelheti a kódokat, például Azure Resource Manager sablonokat. 

Engedélyek megadása vagy megtagadása adott felhasználók, beépített biztonsági csoportok vagy Azure Active Directory (Azure AD) által definiált csoportok számára, ha az Azure DevOps-hez való integrációja az ilyen erőforrásokhoz való hozzáféréshez, illetve Active Directory, ha a Team Foundation Server integrálva van.

- [Kód tárolása az Azure DevOps](/azure/devops/repos/git/gitworkflow)

- [Engedélyek és csoportok az Azure DevOps](/azure/devops/organizations/security/about-permissions)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

## <a name="incident-response"></a>Incidensmegoldás

*További információt az [Azure biztonsági teljesítményteszt: incidens válasza](../security/benchmarks/security-control-incident-response.md)című témakörben talál.*

### <a name="101-create-an-incident-response-guide"></a>10,1: incidens-válaszi útmutató létrehozása

**Útmutató**: Alakítson ki incidenskezelési útmutatót a vállalat számára. 

Gondoskodjon arról, hogy az incidensek kezelésével és kezelésével kapcsolatos összes szerepkört definiáló írásos incidensek az incidensek áttekintése után legyenek.

- [Útmutató a saját biztonsági incidensek megoldási folyamatának létrehozásához](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Microsoft Security Response Center – incidens anatómiája](https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/)

- [Az ügyfél a NIST számítógépes biztonsági incidensek kezelési útmutatóját is kihasználhatja a saját incidens-válasz tervének létrehozásával kapcsolatos támogatáshoz](https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10,2: incidensek pontozásának és rangsorolási eljárásának létrehozása

**Útmutató**: a Security Center súlyosságot rendel az egyes riasztásokhoz, így rangsorolhatja, hogy a rendszer mely riasztásokat vizsgálja meg elsőként. A súlyosság azon alapul, hogy az Security Center milyen mértékben szerepel a riasztás kibocsátásához használt elemzésben vagy elemzésben, valamint a riasztást eredményező tevékenység mögötti rosszindulatú szándékkal.

Egyértelműen megjelölheti az előfizetéseket (például éles környezetben, nem éles környezetben) címkék használatával, és létrehozhat egy elnevezési rendszert az Azure-erőforrások egyértelmű azonosítására és kategorizálására, különösen a bizalmas adatok feldolgozására.  

Az ügyfél feladata, hogy rangsorolja a riasztások szervizelését az Azure-erőforrások és-környezet kritikus jellemzői alapján, ahol az incidens történt.

- [Biztonsági riasztások az Azure Security Centerben](../security-center/security-center-alerts-overview.md)

- [Címkék használata az Azure-erőforrások rendszerezéséhez](../azure-resource-manager/management/tag-resources.md)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="103-test-security-response-procedures"></a>10,3: biztonsági reagálási eljárások tesztelése

**Útmutató**: az Azure-erőforrások védelmének biztosítása érdekében a rendszer az incidensek reagálási képességeinek rendszeres tesztelésére szolgáló gyakorlatokat hajt végre. Azonosítsa a gyenge pontokat és réseket, és szükség esetén dolgozza át a tervet.

- [A NIST kiadványa – útmutató az IT-csomagok és-képességek teszteléséhez, betanításához és alkalmazásához](https://csrc.nist.gov/publications/detail/sp/800-84/final)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10,4: biztonsági incidensek elérhetőségének biztosítása és riasztási értesítések konfigurálása biztonsági incidensekhez

**Útmutató**: a Microsoft a biztonsági incidensek elérhetőségi adatait arra használja fel, hogy felvegye Önnel a kapcsolatot, ha a Microsoft Security Response Center (MSRC) felfedi, hogy az adatokat egy törvénytelen vagy jogosulatlan fél is hozzáférte. 

A problémák megoldása érdekében tekintse át az incidenseket, tegye az eseményt.

- [A Azure Security Center biztonsági kapcsolattartó beállítása](../security-center/security-center-provide-security-contact-details.md)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10,5: biztonsági riasztások beépítése az incidensek gyorsreagáló rendszerébe

**Útmutató**: Security Center riasztások és javaslatok exportálása a folyamatos exportálás funkcióval az Azure-erőforrásokkal kapcsolatos kockázatok azonosítása érdekében. A folyamatos exportálás lehetővé teszi a riasztások és javaslatok manuális és folyamatos exportálását. 

Emellett a Security Center adatösszekötővel is továbbíthatja a riasztásokat az Azure Sentinel szolgáltatásba az üzleti műveleteinek megfelelően.

- [Folyamatos exportálás konfigurálása](../security-center/continuous-export.md)

- [Riasztások streamelése az Azure Sentinelbe](../sentinel/connect-azure-security-center.md)

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="106-automate-the-response-to-security-alerts"></a>10,6: a biztonsági riasztásokra adott válasz automatizálása

**Útmutató**: a Security Center munkafolyamat-automatizálási funkciója segítségével automatikusan aktiválhatja a válaszokat az Logic apps biztonsági riasztásokkal és az Azure-erőforrások védelmére vonatkozó javaslatokkal.

- [A Munkafolyamat-automatizálás és a Logic Apps konfigurálása](../security-center/workflow-automation.md)

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

## <a name="penetration-tests-and-red-team-exercises"></a>Behatolási tesztek és Red Team-gyakorlatok

*További információkért tekintse meg az [Azure biztonsági teljesítményteszt: behatolási tesztek és a Red Team gyakorlatok](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md)című témakört.*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11,1: az Azure-erőforrások rendszeres behatolásának tesztelése, valamint az összes kritikus biztonsági vizsgálat szervizelésének biztosítása

**Útmutató**: kövesse a Microsoft részvételi szabályait, hogy a behatolási tesztek ne sértsék a Microsoft-házirendeket. 

A Microsoft által felügyelt felhőalapú infrastruktúrával, szolgáltatásokkal és alkalmazásokkal kapcsolatban a Microsoft stratégiáját és a Red Teaming és a Live site penetráció tesztelését is használhatja.

- [Behatolástesztelési beavatkozási szabályok](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)

- [Riasztási tesztek a Microsoft-felhőben](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Megosztott

## <a name="next-steps"></a>Következő lépések

- Lásd az [Azure biztonsági teljesítménytesztét](../security/benchmarks/overview.md)
- További tudnivalók az [Azure biztonsági alapterveiről](../security/benchmarks/security-baselines-overview.md)