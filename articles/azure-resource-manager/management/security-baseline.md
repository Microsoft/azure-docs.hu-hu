---
title: Azure Resource Manager Azure biztonsági alapterve
description: A Azure Resource Manager biztonsági alapterve az Azure biztonsági Teljesítménytesztben meghatározott biztonsági javaslatok megvalósítására szolgáló eljárási útmutatást és erőforrásokat biztosít.
author: msmbaldwin
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 02/17/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 996159f7014e735c8a4b3e5975c2716ce1ab6910
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2021
ms.locfileid: "101728487"
---
# <a name="azure-security-baseline-for-azure-resource-manager"></a>Azure Resource Manager Azure biztonsági alapterve

Ez a biztonsági alapterv az [Azure biztonsági teljesítményteszt 1,0-es verziójáról](../../security/benchmarks/overview-v1.md) Microsoft Azure Resource Managerre vonatkozó útmutatást alkalmaz. Az Azure Security Benchmark ajánlásokat ad arra nézve, hogy hogyan tehetők biztonságossá a felhőalapú megoldások az Azure-ban.
A tartalom az Azure biztonsági teljesítményteszt által meghatározott **biztonsági vezérlők** szerint van csoportosítva, valamint a Azure Resource Manager vonatkozó kapcsolódó útmutatás. A Azure Resource Manager nem alkalmazható **vezérlők** ki vannak zárva.

 
Ha szeretné megtekinteni, hogyan Azure Resource Manager teljes mértékben leképezni az Azure biztonsági Teljesítménytesztét, tekintse meg a [teljes Azure Resource Manager biztonsági alapterv-leképezési fájlt](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="logging-and-monitoring"></a>Naplózás és monitorozás

*További információt az [Azure biztonsági teljesítményteszt: naplózás és figyelés](../../security/benchmarks/security-control-logging-monitoring.md)című témakörben talál.*

### <a name="22-configure-central-security-log-management"></a>2,2: a központi biztonsági naplók felügyeletének konfigurálása

**Útmutató**: Azure Policy tevékenység-naplók betöltése Azure monitor használatával. A Azure Monitoron belül Log Analytics munkaterületek használatával kérdezheti le és végezheti el az elemzéseket, és használhatja az Azure Storage-fiókokat a hosszú távú vagy archiválási tároláshoz. Alternatív megoldásként engedélyezheti és elvégezheti az Azure Sentinel vagy egy harmadik féltől származó SIEM-et.

- [Az Azure Sentinel előkészítése](../../sentinel/quickstart-onboard.md) 

- [Platform-naplók és-metrikák összegyűjtése Azure Monitor](/azure/azure-monitor/platform/diagnostic-settings) 

- [Az Azure-beli virtuális gépek belső gazdagép-naplóinak összegyűjtése Azure Monitor](/azure/azure-monitor/learn/quick-collect-azurevm) 

- [Ismerkedés a Azure Monitor és a harmadik féltől származó SIEM-integrációval](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás**: az [Azure biztonsági teljesítményteszt](/home/mbaldwin/docs/asb/azure-docs-pr/articles/governance/policy/samples/azure-security-benchmark.md) a Security Center alapértelmezett házirend-kezdeményezése, és a [Security Center ajánlásainak](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/security-center-recommendations.md)alapja. A vezérlőhöz kapcsolódó Azure Policy-definíciók Security Center automatikusan engedélyezve vannak. Az ehhez a vezérlőhöz kapcsolódó riasztásokhoz szükség lehet egy [Azure Defender](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/azure-defender.md) -csomagra a kapcsolódó szolgáltatásokhoz.

**Azure Policy beépített definíciók – Microsoft. Resources**:

[!INCLUDE [Resource Policy for Microsoft.Resources 2.2](../../../includes/policy/standards/asb/rp-controls/microsoft.resources-2-2.md)]

### <a name="23-enable-audit-logging-for-azure-resources"></a>2,3: az Azure-erőforrások naplózásának engedélyezése

**Útmutató**: a Azure Resource Manager a tevékenység naplóit használja, amelyek automatikusan engedélyezve vannak az eseményforrás, a dátum, a felhasználó, az időbélyeg, a forráscím, a célhelyek és más hasznos elemek belefoglalásához.

- [Platform-naplók és-metrikák összegyűjtése Azure Monitor](/azure/azure-monitor/platform/diagnostic-settings) 

- [A naplózás és a különböző naplózási típusok megismerése az Azure-ban](/azure/azure-monitor/platform/platform-logs-overview)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="26-monitor-and-review-logs"></a>2,6: naplók figyelése és áttekintése

**Útmutató**: a rendellenes viselkedésre vonatkozó naplók elemzése és figyelése, valamint az eredmények rendszeres áttekintése. A naplók áttekintéséhez és a naplózási adatok lekérdezéséhez használja a Azure Monitor és egy Log Analytics munkaterületet. 

Alternatív megoldásként engedélyezheti és elvégezheti az Azure Sentinel vagy egy harmadik féltől származó SIEM-et. 

- [Az Azure Sentinel előkészítése](../../sentinel/quickstart-onboard.md) 

- [Log Analytics lekérdezések első lépései](/azure/azure-monitor/log-query/log-analytics-tutorial)

- [Egyéni lekérdezések végrehajtása a Azure Monitorban](/azure/azure-monitor/log-query/get-started-queries)

**Felelősség**: Megosztott

**Azure Security Center figyelés**: nincs

### <a name="27-enable-alerts-for-anomalous-activities"></a>2,7: riasztások engedélyezése rendellenes tevékenységekhez

**Útmutató**: a Azure Security Center és az log Analytics használata a tevékenységek naplóiban található rendellenes tevékenységek figyeléséhez és riasztásához. Alternatív megoldásként engedélyezheti és elvégezheti a fedélzeti adatfeldolgozást az Azure Sentinel szolgáltatásban. 

- [Az Azure Sentinel előkészítése](../../sentinel/quickstart-onboard.md) 

- [Riasztások kezelése Azure Security Centerban](../../security-center/security-center-managing-and-responding-alerts.md) 

- [Riasztás a Log Analytics naplózási információi alapján](/azure/azure-monitor/learn/tutorial-response)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

## <a name="identity-and-access-control"></a>Identitás- és hozzáférés-vezérlés

*További információt az [Azure biztonsági teljesítményteszt: Identity and Access Control](../../security/benchmarks/security-control-identity-access-control.md)című témakörben talál.*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3,1: a felügyeleti fiókok leltárának karbantartása

**Útmutató**: az Azure szerepköralapú hozzáférés-vezérlés (RBAC) lehetővé teszi az Azure-erőforrásokhoz való hozzáférés kezelését a szerepkör-hozzárendeléseken keresztül. Ezeket a szerepköröket hozzárendelheti a felhasználókhoz, a csoportok egyszerű szolgáltatásaihoz és a felügyelt identitásokhoz. Bizonyos erőforrásokhoz előre definiált beépített szerepkörök tartoznak, és ezeket a szerepköröket az Azure CLI, a Azure PowerShell vagy a Azure Portal eszközein keresztül leltározott vagy kérdezheti le.

- [Címtárbeli szerepkör beszerzése Azure Active Directoryban (Azure AD) a PowerShell-lel](/powershell/module/azuread/get-azureaddirectoryrole)

- [Címtárbeli szerepkör tagjainak beszerzése az Azure AD-ben a PowerShell-lel](/powershell/module/azuread/get-azureaddirectoryrolemember)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás**: az [Azure biztonsági teljesítményteszt](/home/mbaldwin/docs/asb/azure-docs-pr/articles/governance/policy/samples/azure-security-benchmark.md) a Security Center alapértelmezett házirend-kezdeményezése, és a [Security Center ajánlásainak](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/security-center-recommendations.md)alapja. A vezérlőhöz kapcsolódó Azure Policy-definíciók Security Center automatikusan engedélyezve vannak. Az ehhez a vezérlőhöz kapcsolódó riasztásokhoz szükség lehet egy [Azure Defender](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/azure-defender.md) -csomagra a kapcsolódó szolgáltatásokhoz.

**Azure Policy beépített definíciók – Microsoft. Resources**:

[!INCLUDE [Resource Policy for Microsoft.Resources 3.1](../../../includes/policy/standards/asb/rp-controls/microsoft.resources-3-1.md)]

### <a name="33-use-dedicated-administrative-accounts"></a>3,3: dedikált rendszergazdai fiókok használata

**Útmutató**: szabványos üzemeltetési eljárások létrehozása a dedikált rendszergazdai fiókok használata körül. A rendszergazdai fiókok számának figyeléséhez használja a Azure Security Center identitás-és hozzáférés-kezelés lehetőséget.

Emellett a dedikált rendszergazdai fiókok nyomon követésének elősegítése érdekében Azure Security Center vagy beépített Azure-szabályzatokból is használhat javaslatokat, például:
- Az előfizetéshez egynél több tulajdonos rendelhető hozzá
- A tulajdonosi engedélyekkel rendelkező elavult fiókokat el kell távolítani az előfizetésből
- A tulajdonosi engedélyekkel rendelkező külső fiókokat el kell távolítani az előfizetésből

Az igény szerinti hozzáférést Azure Active Directory (Azure AD) Privileged Identity Management és Azure Resource Manager használatával is engedélyezheti.

- [További információ a Privileged Identity Management](/azure/active-directory/privileged-identity-management/)

- [A Azure Policy használata](../../governance/policy/tutorials/create-and-manage.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás**: az [Azure biztonsági teljesítményteszt](/home/mbaldwin/docs/asb/azure-docs-pr/articles/governance/policy/samples/azure-security-benchmark.md) a Security Center alapértelmezett házirend-kezdeményezése, és a [Security Center ajánlásainak](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/security-center-recommendations.md)alapja. A vezérlőhöz kapcsolódó Azure Policy-definíciók Security Center automatikusan engedélyezve vannak. Az ehhez a vezérlőhöz kapcsolódó riasztásokhoz szükség lehet egy [Azure Defender](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/azure-defender.md) -csomagra a kapcsolódó szolgáltatásokhoz.

**Azure Policy beépített definíciók – Microsoft. Resources**:

[!INCLUDE [Resource Policy for Microsoft.Resources 3.3](../../../includes/policy/standards/asb/rp-controls/microsoft.resources-3-3.md)]

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3,4: egyszeri bejelentkezés (SSO) használata Azure Active Directory

**Útmutató**: ha lehetséges, használjon Azure Active Directory (Azure ad) SSO-t a különálló önálló hitelesítő adatok konfigurálása helyett. Azure Security Center identitás és hozzáférési javaslatok használata.

- [Az egyszeri bejelentkezés ismertetése az Azure AD-vel](../../active-directory/manage-apps/what-is-single-sign-on.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3,5: Multi-Factor Authentication használata az összes Azure Active Directory-alapú hozzáféréshez

**Útmutató**: engedélyezze Azure Active Directory (Azure ad) többtényezős hitelesítését, és kövesse Azure Security Center identitás-és hozzáférés-kezelési javaslatait.

- [Többtényezős hitelesítés engedélyezése az Azure-ban](../../active-directory/authentication/howto-mfa-getstarted.md)

- [Identitás és hozzáférés figyelése Azure Security Centeron belül](../../security-center/security-center-identity-access.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás**: az [Azure biztonsági teljesítményteszt](/home/mbaldwin/docs/asb/azure-docs-pr/articles/governance/policy/samples/azure-security-benchmark.md) a Security Center alapértelmezett házirend-kezdeményezése, és a [Security Center ajánlásainak](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/security-center-recommendations.md)alapja. A vezérlőhöz kapcsolódó Azure Policy-definíciók Security Center automatikusan engedélyezve vannak. Az ehhez a vezérlőhöz kapcsolódó riasztásokhoz szükség lehet egy [Azure Defender](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/azure-defender.md) -csomagra a kapcsolódó szolgáltatásokhoz.

**Azure Policy beépített definíciók – Microsoft. Resources**:

[!INCLUDE [Resource Policy for Microsoft.Resources 3.5](../../../includes/policy/standards/asb/rp-controls/microsoft.resources-3-5.md)]

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3,6: dedikált gépek (privilegizált hozzáférési munkaállomások) használata az összes felügyeleti feladathoz

**Útmutató**: emelt szintű jogosultságokat igénylő felügyeleti feladatokhoz használjon biztonságos, Azure által felügyelt munkaállomás (más néven privilegizált hozzáférési munkaállomás vagy Paw) használatát.

- [A biztonságos, Azure által felügyelt munkaállomások ismertetése](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

- [A Azure Active Directory (Azure AD) többtényezős hitelesítésének engedélyezése](../../active-directory/authentication/howto-mfa-getstarted.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3,7: naplózás és riasztás a gyanús tevékenységekről a rendszergazdai fiókoktól

**Útmutató**: a Azure Active Directory (Azure ad) biztonsági jelentéseinek és figyelésének használata annak észlelésére, hogy a környezetben gyanús vagy nem biztonságos tevékenységek történnek-e. A Azure Security Center használatával figyelheti az identitás-és hozzáférési tevékenységeket.

- [A kockázatos tevékenységek miatt megjelölt Azure AD-felhasználók azonosítása](../../active-directory/identity-protection/overview-identity-protection.md)

- [A felhasználók identitási és hozzáférési tevékenységeinek monitorozása az Azure Security Centerben](../../security-center/security-center-identity-access.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="38-manage-azure-resources-only-from-approved-locations"></a>3,8: csak a jóváhagyott helyekről származó Azure-erőforrások kezelése

**Útmutató**: az Azure Active Directory (Azure ad) nevű hely használata, amely lehetővé teszi, hogy csak az IP-címtartományok vagy országok/régiók adott logikai csoportjaihoz férhessenek hozzá.

- [Az Azure AD nevesített helyeinek konfigurálása](../../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="39-use-azure-active-directory"></a>3,9: a Azure Active Directory használata

**Útmutató**: az Azure Active Directory (Azure ad) nevű hely használata, amely lehetővé teszi, hogy csak az IP-címtartományok vagy országok/régiók adott logikai csoportjaihoz férhessenek hozzá.

- [Az Azure AD nevesített helyeinek konfigurálása](../../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="310-regularly-review-and-reconcile-user-access"></a>3,10: a felhasználói hozzáférés rendszeres áttekintése és egyeztetése

**Útmutató**: a Azure Active Directory (Azure ad) olyan naplókat biztosít, amelyek segítenek az elavult fiókok felderítésében. Emellett az Azure AD identitás-és hozzáférési felülvizsgálatok segítségével hatékonyan kezelheti a csoporttagságok kezelését, a vállalati alkalmazásokhoz való hozzáférést és a szerepkör-hozzárendeléseket. A felhasználói hozzáférés rendszeresen felülvizsgálható, hogy csak a megfelelő felhasználók férhessenek hozzá.

- [Az Azure AD jelentéskészítés ismertetése](/azure/active-directory/reports-monitoring/)

- [Az Azure AD identitás- és hozzáférési felülvizsgálatainak használata](../../active-directory/governance/access-reviews-overview.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás**: az [Azure biztonsági teljesítményteszt](/home/mbaldwin/docs/asb/azure-docs-pr/articles/governance/policy/samples/azure-security-benchmark.md) a Security Center alapértelmezett házirend-kezdeményezése, és a [Security Center ajánlásainak](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/security-center-recommendations.md)alapja. A vezérlőhöz kapcsolódó Azure Policy-definíciók Security Center automatikusan engedélyezve vannak. Az ehhez a vezérlőhöz kapcsolódó riasztásokhoz szükség lehet egy [Azure Defender](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/azure-defender.md) -csomagra a kapcsolódó szolgáltatásokhoz.

**Azure Policy beépített definíciók – Microsoft. Resources**:

[!INCLUDE [Resource Policy for Microsoft.Resources 3.10](../../../includes/policy/standards/asb/rp-controls/microsoft.resources-3-10.md)]

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3,11: a figyelő megkísérli a deaktivált hitelesítő adatok elérését

**Útmutató**: hozzáférhet Azure Active Directory (Azure ad) bejelentkezési tevékenységhez, naplózáshoz és kockázati Eseménynapló-forrásokhoz, amelyek lehetővé teszik bármely Siem/monitoring eszköz integrálását.

Ezt a folyamatot leegyszerűsítheti, ha diagnosztikai beállításokat hoz létre az Azure AD felhasználói fiókjaihoz, és elküldi a naplókat és a bejelentkezési naplókat egy Log Analytics munkaterületre. Log Analytics munkaterületen belül konfigurálhatja a kívánt riasztásokat.

- [Azure-beli Tevékenységnaplók integrálása a Azure Monitor](/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="312-alert-on-account-login-behavior-deviation"></a>3,12: riasztás a fiók bejelentkezési viselkedésének eltérése esetén

**Útmutató**: az Azure Active Directory (Azure ad) Identity Protection-funkciókkal konfigurálhatja a felhasználói identitásokkal kapcsolatos gyanús műveletekre vonatkozó automatizált válaszokat. További vizsgálat céljából az Azure Sentinelbe is betöltheti az adatmennyiséget.

- [Az Azure AD kockázatos bejelentkezéseinek megtekintése](../../active-directory/identity-protection/overview-identity-protection.md)

- [Az Identity Protection kockázati házirendjeinek konfigurálása és engedélyezése](../../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [Az Azure Sentinel előkészítése](../../sentinel/quickstart-onboard.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

## <a name="data-protection"></a>Adatvédelem

*További információ: [Azure Security Benchmark: Adatvédelem](../../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4,1: bizalmas információk leltárának fenntartása

**Útmutató**: a címkék használatával segítheti a bizalmas adatokat tároló vagy feldolgozó Azure-erőforrások nyomon követését.

- [Címkék létrehozása és használata](tag-resources.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="46-use-azure-rbac-to-manage-access-to-resources"></a>4,6: az erőforrásokhoz való hozzáférés kezelése az Azure RBAC

**Útmutató**: a Azure Active Directory (Azure ad) RBAC használatával szabályozhatja az adathozzáférést és az erőforrásokat, máskülönben a szolgáltatásra vonatkozó hozzáférés-vezérlési módszereket is használhat.

- [Az Azure RBAC konfigurálása](../../role-based-access-control/role-assignments-portal.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás**: az [Azure biztonsági teljesítményteszt](/home/mbaldwin/docs/asb/azure-docs-pr/articles/governance/policy/samples/azure-security-benchmark.md) a Security Center alapértelmezett házirend-kezdeményezése, és a [Security Center ajánlásainak](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/security-center-recommendations.md)alapja. A vezérlőhöz kapcsolódó Azure Policy-definíciók Security Center automatikusan engedélyezve vannak. Az ehhez a vezérlőhöz kapcsolódó riasztásokhoz szükség lehet egy [Azure Defender](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/azure-defender.md) -csomagra a kapcsolódó szolgáltatásokhoz.

**Beépített definíciók Azure Policy – Microsoft. Authorization**:

[!INCLUDE [Resource Policy for Microsoft.Authorization 4.6](../../../includes/policy/standards/asb/rp-controls/microsoft.authorization-4-6.md)]

### <a name="48-encrypt-sensitive-information-at-rest"></a>4,8: bizalmas adatok titkosítása a nyugalmi állapotban

**Útmutató**: a kiszolgálóoldali titkosításhoz a Azure Resource Manager támogatja a Microsoft által felügyelt kulcsokat.

- [A Azure Resource Manager adatvédelem ismertetése](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-resource-manager-security-controls#data-protection)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4,9: a kritikus Azure-erőforrások változásainak naplózása és riasztása

**Útmutató**: a Azure monitor és az Azure-tevékenység naplójának használata riasztások létrehozásához, amikor a módosítások a kritikus Azure-erőforrásokra vonatkoznak.

- [Riasztások létrehozása az Azure-tevékenységek naplózási eseményeihez](/azure/azure-monitor/platform/alerts-activity-log)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás**: az [Azure biztonsági teljesítményteszt](/home/mbaldwin/docs/asb/azure-docs-pr/articles/governance/policy/samples/azure-security-benchmark.md) a Security Center alapértelmezett házirend-kezdeményezése, és a [Security Center ajánlásainak](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/security-center-recommendations.md)alapja. A vezérlőhöz kapcsolódó Azure Policy-definíciók Security Center automatikusan engedélyezve vannak. Az ehhez a vezérlőhöz kapcsolódó riasztásokhoz szükség lehet egy [Azure Defender](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/azure-defender.md) -csomagra a kapcsolódó szolgáltatásokhoz.

**Azure Policy beépített definíciók – Microsoft. Resources**:

[!INCLUDE [Resource Policy for Microsoft.Resources 4.9](../../../includes/policy/standards/asb/rp-controls/microsoft.resources-4-9.md)]

## <a name="inventory-and-asset-management"></a>Leltár-és eszközfelügyelet

*További információt az [Azure biztonsági teljesítményteszt: leltár és eszközkezelés](../../security/benchmarks/security-control-inventory-asset-management.md)című témakörben talál.*

### <a name="61-use-automated-asset-discovery-solution"></a>6,1: automatikus eszköz-felderítési megoldás használata

**Útmutató**: az Azure Resource Graph használatával lekérdezheti és felderítheti az előfizetésekben lévő összes erőforrást (például a számítási, tárolási, hálózati, portok és protokollok stb.). Győződjön meg arról, hogy a bérlőben a megfelelő (olvasási) engedélyek szerepelnek, valamint az összes Azure-előfizetés enumerálása, valamint az előfizetések erőforrásai.

Bár a klasszikus Azure-erőforrások az Azure Resource Graph Explorerben is felderíthető, erősen ajánlott Azure Resource Manager erőforrások létrehozása és használata.

- [Lekérdezések létrehozása az Azure Resource Graph használatával](../../governance/resource-graph/first-query-portal.md)

- [Azure-előfizetések megtekintése](/powershell/module/az.accounts/get-azsubscription)

- [Az Azure RBAC ismertetése](../../role-based-access-control/overview.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="62-maintain-asset-metadata"></a>6,2: az eszköz metaadatainak fenntartása

**Útmutató**: a szabályzat nevének, leírásának és kategóriájának használata, amelyekkel logikailag rendezheti az eszközöket a besorolások alapján.

- [További információ az eszközök címkézéséről: erőforrás-elnevezési és címkézési döntési útmutató](https://docs.microsoft.com/azure/cloud-adoption-framework/decision-guides/resource-tagging/?toc=/azure/azure-resource-manager/management/toc.json)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="63-delete-unauthorized-azure-resources"></a>6,3: jogosulatlan Azure-erőforrások törlése

**Útmutató**: az Azure-erőforrások rendszerezéséhez és nyomon követéséhez használja a címkézést, a felügyeleti csoportokat és a különálló előfizetéseket, ahol szükséges. Rendszeres időközönként egyeztetheti a leltárt, és gondoskodhat arról, hogy a jogosulatlan erőforrások törlése az előfizetésből időben történjen.

Emellett a Azure Policy használatával korlátozásokat állíthat be az ügyfél-előfizetésekben létrehozható erőforrások típusára a következő beépített szabályzat-definíciók használatával:

- Nem engedélyezett erőforrástípusok
- Engedélyezett erőforrástípusok

További kapcsolódó részleteket az alábbi,

- [További Azure-előfizetések létrehozása](../../cost-management-billing/manage/create-subscription.md)

- [Felügyeleti csoportok létrehozása](../../governance/management-groups/create-management-group-portal.md)

- [Címkék létrehozása és használata](tag-resources.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="64-define-and-maintain-an-inventory-of-approved-azure-resources"></a>6,4: jóváhagyott Azure-erőforrások leltárának meghatározása és karbantartása

**Útmutató**: a számítási erőforrásokhoz jóváhagyott Azure-erőforrások és jóváhagyott szoftverek leltárának létrehozása a szervezeti igényeknek megfelelően.

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="65-monitor-for-unapproved-azure-resources"></a>6,5: a nem jóváhagyott Azure-erőforrások figyelése

**Útmutató**: a Azure Policy használatával korlátozásokat állíthat be az előfizetésekben létrehozható erőforrásokra vonatkozóan.

Az Azure Resource Graph használatával lekérdezheti és felderítheti az előfizetésükön belüli erőforrásokat.  Győződjön meg arról, hogy a környezetben lévő összes Azure-erőforrás jóvá van hagyva.

- [Az Azure Policy konfigurálása és kezelése](../../governance/policy/tutorials/create-and-manage.md)

- [Lekérdezések létrehozása az Azure Resource Graph használatával](../../governance/resource-graph/first-query-portal.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="69-use-only-approved-azure-services"></a>6,9: csak jóváhagyott Azure-szolgáltatások használata

**Útmutató**: a Azure Policy használatával korlátozásokat állíthat be az ügyfél-előfizetésekben létrehozható erőforrások típusára a következő beépített szabályzat-definíciók használatával:

- Nem engedélyezett erőforrástípusok
- Engedélyezett erőforrástípusok

További kapcsolódó részleteket az alábbi,

- [Az Azure Policy konfigurálása és kezelése](../../governance/policy/tutorials/create-and-manage.md)

- [Adott erőforrástípus megtagadása a következővel Azure Policy](https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#general)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6,11: korlátozza a felhasználók képességét a Azure Resource Manager való interakcióra

**Útmutató**: az Azure Active Directory (Azure ad) feltételes hozzáférés használatával korlátozhatja, hogy a felhasználók képesek legyenek az Azure Resources Managerrel együttműködni az "Microsoft Azure felügyelet" alkalmazás "hozzáférés letiltása" beállításával.

- [Feltételes hozzáférés konfigurálása az Azure Resources Manager elérésének blokkolásához](../../role-based-access-control/conditional-access-azure-management.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="612-limit-users-ability-to-execute-scripts-in-compute-resources"></a>6,12: korlátozza a felhasználók számára a parancsfájlok végrehajtásának lehetőségét a számítási erőforrásokban

**Útmutató**: nem alkalmazható; Ez a javaslat számítási erőforrások számára készült.

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

## <a name="secure-configuration"></a>Biztonságos konfiguráció

*További információt az [Azure biztonsági teljesítményteszt: biztonságos konfiguráció](../../security/benchmarks/security-control-secure-configuration.md)című témakörben talál.*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7,1: biztonságos konfigurációk létrehozása az összes Azure-erőforráshoz

**Útmutató**: Azure Policy-aliasok használata egyéni szabályzatok létrehozásához az Azure-erőforrások konfigurációjának naplózásához vagy érvényesítéséhez. A beépített Azure Policy-definíciókat is használhatja.

A Azure Resource Manager lehetővé teszi a sablon exportálását JavaScript Object Notation (JSON), amelyet át kell tekinteni annak érdekében, hogy a konfigurációk megfeleljenek a szervezete biztonsági követelményeinek.

A Azure Security Center javaslatai az Azure-erőforrások biztonságos alapkonfigurációja is használhatók.

- [Az elérhető Azure Policy aliasok megtekintése](/powershell/module/az.resources/get-azpolicyalias)

- [Oktatóanyag: szabályzatok létrehozása és kezelése a megfelelőség kikényszerítés érdekében](../../governance/policy/tutorials/create-and-manage.md)

- [Egy-és többerőforrásos exportálás Azure Portal sablonba](../templates/export-template-portal.md)

- [Biztonsági javaslatok – gyorsútmutató](../../security-center/recommendations-reference.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="73-maintain-secure-azure-resource-configurations"></a>7,3: biztonságos Azure-erőforrás-konfigurációk karbantartása

**Útmutató**: az Azure-erőforrások biztonságos beállításainak betartatásához használja a Azure Policy [deny] és a [telepítés ha nem létezik] lehetőséget.  Emellett Azure Resource Manager-sablonokkal is megőrizheti a szervezete által igényelt Azure-erőforrások biztonsági konfigurációját. 

Emellett előfordulhat, hogy rendszergazdaként le kell zárnia egy előfizetést, egy erőforráscsoportot vagy egy erőforrást, hogy megakadályozza a szervezet más felhasználói számára a kritikus erőforrások véletlen törlését vagy módosítását. A zárolási szintet CanNotDelete (nem törölhető) vagy ReadOnly (csak olvasható) értékre állíthatja be.

- [Azure Policy effektusok ismertetése](../../governance/policy/concepts/effects.md)

- [Szabályzatok létrehozása és kezelése a megfelelőség kikényszerítése céljából](../../governance/policy/tutorials/create-and-manage.md)

- [Azure Resource Manager sablonok áttekintése](../templates/overview.md)

- [Erőforrások zárolása a váratlan módosítások megelőzése érdekében](lock-resources.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="75-securely-store-configuration-of-azure-resources"></a>7,5: az Azure-erőforrások biztonságos tárolása

**Útmutató**: az Azure DevOps használatával biztonságosan tárolhatók és kezelhetők a kódok, például az egyéni Azure Policy-definíciók, Azure Resource Manager sablonok és a kívánt állapotú konfigurációs parancsfájlok. Az Azure DevOps felügyelt erőforrásainak eléréséhez engedélyeket adhat meg vagy tagadhat meg bizonyos felhasználók, beépített biztonsági csoportok vagy Azure Active Directory (Azure AD) által meghatározott csoportok számára, ha az integrálva van az Azure DevOps, vagy Active Directory, ha a TFS integrálva van.

- [Kód tárolása az Azure DevOps](/azure/devops/repos/git/gitworkflow)

- [Engedélyek és csoportok az Azure DevOps](/azure/devops/organizations/security/about-permissions)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7,7: az Azure-erőforrások konfigurációs felügyeleti eszközeinek üzembe helyezése

**Útmutató**: az Azure-erőforrások szabványos biztonsági konfigurációinak definiálása és implementálása Azure Policy használatával. Az Azure Policy Aliasok használatával egyéni szabályzatokat hozhat létre az Azure-erőforrások hálózati konfigurációjának naplózásához vagy érvénybe léptetéséhez. Használhatja az adott erőforrásokhoz kapcsolódó beépített szabályzat-definíciókat is.  Emellett a Azure Automation használatával is telepítheti a konfigurációs módosításokat.

- [Az Azure Policy konfigurálása és kezelése](../../governance/policy/tutorials/create-and-manage.md)

- [Aliasok használata](https://docs.microsoft.com/azure/governance/policy/concepts/definition-structure#aliases)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7,9: automatikus konfigurációs monitorozás megvalósítása Azure-erőforrásokhoz

**Útmutató**: beépített Azure Policy-definíciók és egyéni házirendek használata a rendszerkonfigurációk riasztásához, naplózásához és érvényesítéséhez. Az Azure-erőforrások konfigurációinak automatikus érvényesítéséhez használja a Azure Policy [audit], [megtagadás] és [üzembe helyezés, ha nem létezik] lehetőséget.

- [Az Azure Policy konfigurálása és kezelése](../../governance/policy/tutorials/create-and-manage.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="713-eliminate-unintended-credential-exposure"></a>7,13: a hitelesítő adatok nem szándékolt expozíciójának megszüntetése

**Útmutató**: az ARM-sablon összeállításakor ajánlott eljárások használata, ezek a javaslatok segítenek elkerülni a gyakori problémákat, amikor ARM-sablont használ a megoldás üzembe helyezéséhez.

Hitelesítő adatok beolvasása a kódban a hitelesítő adatok azonosításához. A Credential Scanner a felfedezett hitelesítő adatok biztonságosabb helyre, például az Azure Key Vaultba való áthelyezésére is javaslatot tesz. 

- [A sablonra vonatkozó ajánlott biztonsági eljárások implementálása](../templates/template-best-practices.md)

- [A hitelesítő adatok beolvasójának beállítása](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

## <a name="data-recovery"></a>Adat-helyreállítás

*További információt az [Azure biztonsági teljesítményteszt: adat-helyreállítás](../../security/benchmarks/security-control-data-recovery.md)című témakörben talál.*

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: az összes biztonsági másolat ellenőrzése, beleértve az ügyfél által felügyelt kulcsokat

**Útmutató**: az Azure Resource Manager-sablonok rendszeres időközönkénti üzembe helyezésének lehetősége az elkülönített előfizetésre, ha szükséges.

- [Erőforrások üzembe helyezése ARM-sablonokkal és Azure Portal](../templates/deploy-portal.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: a biztonsági másolatok és az ügyfél által felügyelt kulcsok védelmének biztosítása

**Útmutató**: az Azure DevOps használatával biztonságosan tárolhatók és kezelhetők a kódok, például az egyéni Azure Policy-definíciók, Azure Resource Manager sablonok és a kívánt állapotú konfigurációs parancsfájlok. Az Azure DevOps felügyelt erőforrásainak eléréséhez engedélyeket adhat meg vagy tagadhat meg bizonyos felhasználók, beépített biztonsági csoportok vagy Azure Active Directory (Azure AD) által meghatározott csoportok számára, ha az integrálva van az Azure DevOps, vagy Active Directory, ha a TFS integrálva van.

- [Kód tárolása az Azure DevOps](/azure/devops/repos/git/gitworkflow)

- [Engedélyek és csoportok az Azure DevOps](/azure/devops/organizations/security/about-permissions)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

## <a name="incident-response"></a>Incidensmegoldás

*További információ: [Azure Security Benchmark: Incidensek kezelése](../../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10,1: incidens-válaszi útmutató létrehozása

**Útmutató**: incidensek kifejlesztése útmutató a szervezet számára. Győződjön meg arról, hogy van olyan írásos incidens-válasz, amely meghatározza a személyzet összes szerepkörét, valamint az incidensek kezelésének és kezelésének fázisait az incidensek vizsgálatát követően. 

- [Útmutató a saját biztonsági incidensek megoldási folyamatának létrehozásához](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Microsoft Security Response Center – incidens anatómiája](https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/)

- [A NIST számítógépes biztonsági incidensek kezelésével kapcsolatos útmutató a saját incidens-válasz tervének létrehozásához](https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="103-test-security-response-procedures"></a>10,3: biztonsági reagálási eljárások tesztelése

**Útmutató**: az Azure-erőforrások védelmének biztosítása érdekében a rendszer az incidensek reagálási képességeinek rendszeres tesztelésére szolgáló gyakorlatokat hajt végre. Azonosítsa a gyenge pontokat és a hézagokat, majd szükség szerint módosítsa a választ.

- [A NIST kiadványa – útmutató az IT-csomagok és-képességek teszteléséhez, betanításához és gyakorlatához](https://csrc.nist.gov/publications/detail/sp/800-84/final)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10,5: biztonsági riasztások beépítése az incidensek gyorsreagáló rendszerébe

**Útmutató**: az Azure Security Center-riasztások és javaslatok exportálása a folyamatos exportálás funkcióval az Azure-erőforrásokkal kapcsolatos kockázatok azonosítása érdekében. A folyamatos exportálás lehetővé teszi a riasztások és javaslatok manuális és folyamatos exportálását. Az Azure Security Center adatösszekötővel továbbíthatja a riasztásokat az Azure Sentinel szolgáltatásba.

- [Folyamatos exportálás konfigurálása](../../security-center/continuous-export.md)

- [Riasztások streamelése az Azure Sentinelbe](../../sentinel/connect-azure-security-center.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="106-automate-the-response-to-security-alerts"></a>10,6: a biztonsági riasztásokra adott válasz automatizálása

**Útmutató**: a munkafolyamat-automatizálási szolgáltatás használata Azure Security Center a biztonsági riasztásokra és az Azure-erőforrások védelmére vonatkozó ajánlásokra adott válaszok automatikus elindítására.

- [A Munkafolyamat-automatizálás konfigurálása a Security Centerban](../../security-center/workflow-automation.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

## <a name="penetration-tests-and-red-team-exercises"></a>Behatolási tesztek és Red Team-gyakorlatok

*További információkért tekintse meg az [Azure biztonsági teljesítményteszt: behatolási tesztek és a Red Team gyakorlatok](../../security/benchmarks/security-control-penetration-tests-red-team-exercises.md)című témakört.*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11,1: az Azure-erőforrások rendszeres behatolásának tesztelése, valamint az összes kritikus biztonsági vizsgálat szervizelésének biztosítása

**Útmutató**: kövesse a Microsoft Cloud penetráció tesztelési szabályait, amelyekkel biztosíthatja, hogy a behatolási tesztek ne sértsék a Microsoft-házirendeket. A Microsoft által felügyelt felhőalapú infrastruktúrán, szolgáltatásokon és alkalmazásokon végzett riasztási és élő behatolási tesztek végrehajtásához használja a Microsoft stratégiáját és végrehajtási tervét.

- [Behatolástesztelési beavatkozási szabályok](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)

- [Riasztási tesztek a Microsoft-felhőben](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Felelősség**: Megosztott

**Azure Security Center figyelés**: nincs

## <a name="next-steps"></a>Következő lépések

- [Az Azure Security Benchmark v2 áttekintésének](/azure/security/benchmarks/overview) megtekintése
- További tudnivalók az [Azure biztonsági alapterveiről](/azure/security/benchmarks/security-baselines-overview)
