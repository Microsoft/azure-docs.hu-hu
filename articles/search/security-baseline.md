---
title: Azure biztonsági alapkonfiguráció az Azure Cognitive Search
description: Az Azure Cognitive Search biztonsági alapterve az Azure biztonsági Teljesítménytesztben meghatározott biztonsági javaslatok megvalósítására szolgáló eljárási útmutatást és erőforrásokat biztosít.
author: msmbaldwin
ms.service: search
ms.topic: conceptual
ms.date: 03/16/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: ebf948c76196224806afda21bd2f266b1b797f74
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "104604380"
---
# <a name="azure-security-baseline-for-azure-cognitive-search"></a>Azure biztonsági alapkonfiguráció az Azure Cognitive Search

Ez a biztonsági alapkonfiguráció az [Azure biztonsági teljesítményteszt 1,0-es verziójáról](../security/benchmarks/overview-v1.md) az Azure Cognitive Search-ra vonatkozó útmutatást alkalmazza. Az Azure Security Benchmark ajánlásokat ad arra nézve, hogy hogyan tehetők biztonságossá a felhőalapú megoldások az Azure-ban. A tartalom az Azure biztonsági teljesítményteszt által meghatározott **biztonsági vezérlők** szerint van csoportosítva, valamint az Azure Cognitive Search-ra vonatkozó kapcsolódó útmutatás. Az Azure Cognitive Searchra nem alkalmazható **vezérlők** , vagy az ügyfél ki lett zárva.

Ha szeretné megtekinteni, hogy az Azure hogyan Cognitive Search teljes mértékben az Azure biztonsági teljesítményteszttel, tekintse meg a [teljes azure Cognitive Search biztonsági alapterv-leképezési fájlt](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="network-security"></a>Hálózati biztonság

*További információ: [Azure Security Benchmark: Hálózati biztonság](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1,1: Azure-erőforrások biztosítása virtuális hálózatokon belül

**Útmutató**: gondoskodjon arról, hogy minden Microsoft Azure Virtual Network alhálózati üzemelő példány rendelkezzen olyan hálózati biztonsági csoporttal, amely a "legkevésbé privilegizált" hozzáférési séma megvalósítására vonatkozó szabályokkal van alkalmazva. Csak az alkalmazás megbízható portjaihoz és IP-címeihez való hozzáférés engedélyezése. Az Azure Cognitive Search üzembe helyezése egy Azure Private-végponttal, ahol lehetséges, a virtuális hálózatról való privát hozzáférés engedélyezése a szolgáltatásokhoz.

A Cognitive Search a hálózati hozzáférés-vezérlési listák felügyeletéhez is támogatja a további hálózati biztonsági funkciókat. Konfigurálja úgy a keresési szolgáltatást, hogy csak a megbízható forrásokkal való kommunikációt engedélyezze, ha korlátozza a hozzáférést az adott nyilvános IP-címtartományok számára a tűzfal funkciójának használatával.

- [Privát végpontok konfigurálása az Azure Cognitive Search](service-create-private-endpoint.md)

- [Az Azure Cognitive Search tűzfal konfigurálása](service-configure-firewall.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-nics"></a>1,2: a virtuális hálózatok, alhálózatok és hálózati adapterek konfigurációjának és forgalmának figyelése és naplózása

**Útmutató**: a Cognitive Search nem helyezhetők üzembe közvetlenül virtuális hálózatban. Ha azonban az ügyfélalkalmazás vagy az adatforrások egy virtuális hálózaton vannak, figyelheti és naplózhatja a hálózati összetevők forgalmát, beleértve a felhőben a keresési szolgáltatásnak küldött kéréseket is. A standard javaslatok közé tartozik a hálózati biztonsági csoport folyamatábrájának engedélyezése és a naplók küldése az Azure Storage-ba vagy egy Log Analytics-munkaterületre. Igény szerint Traffic Analytics is használhat a forgalmi minták elemzéséhez.

- [Hálózati biztonsági csoport folyamatábráinak engedélyezése](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [A Traffic Analytics engedélyezése és használata](../network-watcher/traffic-analytics.md)

- [A Azure Security Center által biztosított hálózati biztonság ismertetése](../security-center/security-center-network-recommendations.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1,4: az ismert kártékony IP-címekkel folytatott kommunikáció megtagadása

**Útmutató**: a Cognitive Search nem biztosít konkrét szolgáltatást az elosztott szolgáltatásmegtagadási támadások elleni védelemhez, de az általános védelmet biztosító Cognitive Search szolgáltatáshoz társított virtuális hálózatok esetében engedélyezheti a DDoS Protection standard beállítását.

- [A DDoS Protection konfigurálása](../ddos-protection/manage-ddos-protection.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="15-record-network-packets"></a>1,5: hálózati csomagok rögzítése

**Útmutató**: engedélyezze a hálózati biztonsági csoport folyamatábráit azon hálózati biztonsági csoportok számára, amelyek védik az Azure Virtual Machinest (VM), amely a Cognitive Search szolgáltatáshoz fog csatlakozni. Naplók küldése Azure Storage-fiókba forgalom naplózásához. 

Ha a rendellenes tevékenységek kivizsgálásához szükséges, engedélyezze Network Watcher csomagok rögzítését.

- [A NSG folyamat naplófájljainak engedélyezése](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [A Network Watcher engedélyezése](../network-watcher/network-watcher-create.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1,6: hálózati behatolás-észlelési/Behatolás-megelőzési rendszerek (AZONOSÍTÓk/IP-címek) üzembe helyezése

**Útmutató**: a Cognitive Search nem támogatja a hálózati behatolás észlelését, de a behatolás mérséklése lehetővé teszi a tűzfalszabályok konfigurálását a Cognitive Search szolgáltatás által elfogadott IP-címek megadásához. Konfiguráljon egy privát végpontot, hogy a forgalom a nyilvános internetről is elérhető legyen.

- [Az ügyfél által felügyelt kulcsok konfigurálása adattitkosításhoz](search-security-manage-encryption-keys.md)

- [Az ügyfelek által felügyelt legfontosabb információk beszerzése az indexekről és a szinonimák térképe](search-security-get-encryption-keys.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1,8: a hálózati biztonsági szabályok bonyolultságának és adminisztratív terhelésének csökkentése

**Útmutató**: a szolgáltatási címkék használata, ha indexelő és szakértelmével használ a Cognitive Searchban, hogy olyan IP-címtartományt képviseljék, amely jogosult a külső erőforrásokhoz való kapcsolódásra. 

Erőforrások forgalmának engedélyezése vagy megtagadása a szabály megfelelő forrás vagy cél mezőjében szereplő szolgáltatási címke nevének (például AzureCognitiveSearch) megadásával. 

- [Virtuális hálózati szolgáltatás címkéi](../virtual-network/service-tags-overview.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="110-document-traffic-configuration-rules"></a>1,10: a dokumentum forgalmának konfigurációs szabályai

**Útmutató**: Cognitive Search konfigurálható egy Azure privát végponttal a keresési szolgáltatás virtuális hálózatba való integrálásához.  Használjon erőforrás-címkéket a hálózati biztonsági csoportokhoz és a hálózati biztonsággal és a forgalommal kapcsolatos egyéb erőforrásokhoz. Az egyes hálózati biztonsági csoportokra vonatkozó szabályok esetében a "Leírás" mező használatával dokumentálhatja a hálózatra vagy hálózatra irányuló forgalmat engedélyező szabályokat. 
 

A címkézéshez kapcsolódó beépített Azure Policy definíciók bármelyikét használhatja, például a "címke és az érték megkövetelése" hatásokat, hogy az összes erőforrás címkével legyen létrehozva, és értesítse a meglévő címkézetlen erőforrásokról. 

A Azure PowerShell vagy az Azure CLI használatával a címkék alapján kereshet vagy végezhet műveleteket az erőforrásokon.  

 
- [Privát végpont létrehozása Cognitive Searchhoz](service-create-private-endpoint.md) 

 
 
- [Címkék létrehozása és használata](../azure-resource-manager/management/tag-resources.md)

- [Azure-Virtual Network létrehozása](../virtual-network/quick-create-portal.md) 

 
- [Hálózati forgalom szűrése hálózati biztonsági csoport szabályaival](../virtual-network/tutorial-filter-network-traffic.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

## <a name="logging-and-monitoring"></a>Naplózás és monitorozás

*További információt az [Azure biztonsági teljesítményteszt: naplózás és figyelés](../security/benchmarks/security-control-logging-monitoring.md)című témakörben talál.*

### <a name="22-configure-central-security-log-management"></a>2,2: a központi biztonsági naplók felügyeletének konfigurálása

**Útmutató**: Cognitive Searchhoz kapcsolódó naplók beolvasása Azure monitor használatával a végponti eszközök, a hálózati erőforrások és más biztonsági rendszerek által generált biztonsági adatokat összesítve. Azure Monitor a Log Analytics-munkaterületek használatával kérdezheti le és végezheti el az elemzéseket, és használhatja az Azure Storage-fiókokat hosszú távú és archiválási tároláshoz. Alternatív megoldásként engedélyezheti és elvégezheti ezeket az adattáblákat az Azure Sentinel vagy egy harmadik féltől származó SIEM-nek.
 

 
- [Ismerkedés a Azure Monitor és a harmadik féltől származó SIEM-integrációval](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/)
 

 
- [Platform-naplók és-metrikák összegyűjtése Azure Monitor](../azure-monitor/essentials/diagnostic-settings.md) 
 

 
- [Az Azure Sentinel előkészítése](../sentinel/quickstart-onboard.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="23-enable-audit-logging-for-azure-resources"></a>2,3: az Azure-erőforrások naplózásának engedélyezése

**Útmutató**: a diagnosztikai és üzemeltetési naplók betekintést nyújtanak Cognitive Search részletes műveleteibe, és hasznosak a szolgáltatás figyeléséhez és a szolgáltatáshoz hozzáférő munkaterhelésekhez.  A diagnosztikai adatok rögzítéséhez engedélyezze a naplózási adatok tárolási helyének megadását.
 

 
- [Az Azure-Cognitive Search naplózási adatainak összegyűjtése és elemzése](search-monitor-logs.md) 

 
- [Platform-naplók és-metrikák összegyűjtése Azure Monitor](../azure-monitor/essentials/diagnostic-settings.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás**: az [Azure biztonsági teljesítményteszt](/home/mbaldwin/docs/asb/azure-docs-pr/articles/governance/policy/samples/azure-security-benchmark.md) a Security Center alapértelmezett házirend-kezdeményezése, és a [Security Center ajánlásainak](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/security-center-recommendations.md)alapja. A vezérlőhöz kapcsolódó Azure Policy-definíciók Security Center automatikusan engedélyezve vannak. Az ehhez a vezérlőhöz kapcsolódó riasztásokhoz szükség lehet egy [Azure Defender](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/azure-defender.md) -csomagra a kapcsolódó szolgáltatásokhoz.

**Azure Policy beépített definíciók – Microsoft. Search**:

[!INCLUDE [Resource Policy for Microsoft.Search 2.3](../../includes/policy/standards/asb/rp-controls/microsoft.search-2-3.md)]

### <a name="25-configure-security-log-storage-retention"></a>2,5: a biztonsági napló tárolási adatmegőrzésének konfigurálása

**Útmutató**: a diagnosztikai mérőszámokra beérkező korábbi adatokat alapértelmezés szerint 30 napig Cognitive Search megőrizni. A hosszú megőrzés érdekében ügyeljen arra, hogy engedélyezze a tárolási lehetőséget a naplózott események és mérőszámok megőrzése érdekében.
 

 
Azure Monitor a szervezet megfelelőségi szabályainak megfelelően állítsa be a Log Analytics munkaterület megőrzési időszakát. Azure Storage-fiókokat használhat hosszú távú és archiválási tároláshoz. 
 

 
- [Az adatmegőrzési időszak módosítása Log Analytics](https://docs.microsoft.com/azure/azure-monitor/logs/manage-cost-storage#change-the-data-retention-period) 

 
- [Adatmegőrzési szabályzat konfigurálása az Azure Storage-fiók naplóihoz](https://docs.microsoft.com/azure/storage/common/manage-storage-analytics-logs#enable-logs)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="26-monitor-and-review-logs"></a>2,6: naplók figyelése és áttekintése

**Útmutató**: a Cognitive Search szolgáltatás naplófájljainak elemzése és figyelése rendellenes működéshez. A naplók áttekintéséhez és a naplózási adatok lekérdezéséhez használja a Azure Monitor Log Analytics. Alternatív megoldásként engedélyezheti és elvégezheti az Azure Sentinel vagy egy harmadik féltől származó SIEM-et. 

 
 
- [A Cognitive Search naplózási adatainak összegyűjtése és elemzése](search-monitor-logs.md)
 
- [Keresési naplóban tárolt adatPower BIek megjelenítése](search-monitor-logs-powerbi.md)
 

 
- [Az Azure Sentinel előkészítése](../sentinel/quickstart-onboard.md)
 

 
- [További tudnivalók a Log Analytics szolgáltatásról](../azure-monitor/logs/log-analytics-tutorial.md)
 

 
- [Egyéni lekérdezések végrehajtása a Azure Monitorban](../azure-monitor/logs/get-started-queries.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="27-enable-alerts-for-anomalous-activities"></a>2,7: riasztások engedélyezése rendellenes tevékenységekhez

**Útmutató**: Security Center használata log Analytics munkaterülettel a biztonsági naplókban és eseményekben észlelt rendellenes tevékenységekkel kapcsolatos figyeléshez és riasztáshoz. Alternatív megoldásként engedélyezheti és elvégezheti az Azure Sentinel szolgáltatásban való ellátását.
 

 
- [Az Azure Sentinel előkészítése](../sentinel/quickstart-onboard.md)
 

 
- [Riasztások kezelése Azure Security Centerban](../security-center/security-center-managing-and-responding-alerts.md)
 

 
- [Riasztás a log Analytics-naplófájlok adatkezeléséről](../azure-monitor/alerts/tutorial-response.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

## <a name="identity-and-access-control"></a>Identitás- és hozzáférés-vezérlés

*További információt az [Azure biztonsági teljesítményteszt: Identity and Access Control](../security/benchmarks/security-control-identity-access-control.md)című témakörben talál.*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3,1: a felügyeleti fiókok leltárának karbantartása

**Útmutató**: az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC) lehetővé teszi az Azure-erőforrásokhoz való hozzáférés kezelését a szerepkör-hozzárendeléseken keresztül. Ezeket a szerepköröket hozzárendelheti a felhasználókhoz, a csoportok egyszerű szolgáltatásaihoz és a felügyelt identitásokhoz. Bizonyos erőforrásokhoz előre meghatározott beépített szerepkörök tartoznak, és ezek a szerepkörök olyan eszközökkel leltározhatók vagy kérdezhetők le, mint az Azure CLI, az Azure PowerShell vagy az Azure Portal.

Cognitive Search szerepkörök olyan engedélyekkel vannak társítva, amelyek támogatják a szolgáltatási szint felügyeleti feladatait. Ezek a szerepkörök nem biztosítanak hozzáférést a szolgáltatás végpontjának. A végpontra irányuló műveletekhez való hozzáférés (például az indexelés kezelése, az indexek sokasága és a keresési adatok lekérdezése) az API-kulcsok használatával hitelesítheti a kérést.

- [Szerepkörök beállítása rendszergazdai hozzáféréshez az Azure Cognitive Search](search-security-rbac.md)

- [Azure Cognitive Search-szolgáltatás API-kulcsainak létrehozása és kezelése](search-security-api-keys.md)

- [Címtárbeli szerepkör beszerzése Azure Active Directoryban (Azure AD) a PowerShell-lel](/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0&amp;preserve-view=true)

- [Címtárbeli szerepkör tagjainak beszerzése az Azure AD-ben a PowerShell-lel](/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0&amp;preserve-view=true)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="33-use-dedicated-administrative-accounts"></a>3,3: dedikált rendszergazdai fiókok használata

**Útmutató**: a Cognitive Search nem rendelkezik olyan helyi szintű vagy Azure Active Directory (Azure ad) rendszergazdai fiókokkal, amelyek az indexek és műveletek kezelésére használhatók. 

Használja az Azure AD beépített szerepköreit, amelyeket explicit módon hozzá kell rendelni a felügyeleti műveletekhez. Az Azure AD PowerShell-modul meghívásával ad hoc lekérdezéseket hajthat végre a felügyeleti csoportok tagjait futtató fiókok felderítése érdekében.

- [Szerepkörök használata rendszergazdai hozzáféréshez Cognitive Search](search-security-rbac.md)

- [Címtárbeli szerepkör beszerzése az Azure AD-ben a PowerShell-lel](/powershell/module/azuread/get-azureaddirectoryrole)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3,4: egyszeri bejelentkezés (SSO) használata Azure Active Directory

**Útmutató**: az Azure Active Directory (Azure ad) szolgáltatással történő egyszeri bejelentkezéses hitelesítés használata a keresési szolgáltatási információk eléréséhez a Azure Resource Manager által támogatott felügyeleti műveletekhez. 

Hozzon létre egy folyamatot az identitások és a hitelesítő adatok számának csökkentéséhez azáltal, hogy engedélyezi az egyszeri bejelentkezést a szolgáltatásnak a szervezete már meglévő identitásával.

- [Az egyszeri bejelentkezés ismertetése az Azure AD-vel](../active-directory/manage-apps/what-is-single-sign-on.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3,5: Multi-Factor Authentication használata az összes Azure Active Directory-alapú hozzáféréshez

**Útmutató**: engedélyezze a Azure Active Directory (Azure ad) többtényezős hitelesítési funkcióját, és kövesse Security Center identitási és hozzáférési javaslatait.

- [Többtényezős hitelesítés engedélyezése az Azure-ban](../active-directory/authentication/howto-mfa-getstarted.md) 

- [Identitás és hozzáférés figyelése Azure Security Centeron belül](../security-center/security-center-identity-access.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3,6: dedikált gépek (privilegizált hozzáférési munkaállomások) használata az összes felügyeleti feladathoz

**Útmutató**: az Azure-erőforrásokhoz való bejelentkezéshez és hozzáféréshez konfigurált, többtényezős hitelesítéssel rendelkező emelt szintű hozzáférési munkaállomás (Paw) használata.
 

 
- [A biztonságos, Azure által felügyelt munkaállomások ismertetése](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)
 

 
- [A Azure Active Directory (Azure AD) többtényezős hitelesítésének engedélyezése](../active-directory/authentication/howto-mfa-getstarted.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3,7: naplózás és riasztás a gyanús tevékenységekről a rendszergazdai fiókoktól

**Útmutató**: a Azure Active Directory (Azure ad) biztonsági jelentéseinek és figyelésének használata annak észlelésére, hogy a környezetben gyanús vagy nem biztonságos tevékenységek történnek-e. A Security Center használatával figyelheti az identitás-és hozzáférési tevékenységeket.

- [A kockázatos tevékenységek miatt megjelölt Azure AD-felhasználók azonosítása](../active-directory/identity-protection/overview-identity-protection.md)

- [A felhasználók identitási és hozzáférési tevékenységeinek monitorozása az Azure Security Centerben](../security-center/security-center-identity-access.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="39-use-azure-active-directory"></a>3,9: a Azure Active Directory használata

**Útmutató**: a Azure Active Directory (Azure ad) használata központi hitelesítési és engedélyezési rendszerként az Azure Cognitive Search szolgáltatás szintű felügyeleti feladatokhoz. Az Azure AD-identitások nem biztosítanak hozzáférést a keresési szolgáltatás végpontjának.  Az API-kulcsokon keresztül elérhetők az olyan műveletek, mint az indexelés, az indexelés és a keresési adatok lekérdezései.

- [Azure AD-példány létrehozása és konfigurálása](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

- [Azure Cognitive Search-szolgáltatás API-kulcsainak létrehozása és kezelése](search-security-api-keys.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="310-regularly-review-and-reconcile-user-access"></a>3,10: a felhasználói hozzáférés rendszeres áttekintése és egyeztetése

**Útmutató**: a Azure Active Directory (Azure ad) olyan naplókat biztosít, amelyek segítenek az elavult fiókok felderítésében. Az Azure AD identitás-és hozzáférési felülvizsgálatait használva hatékonyan kezelheti a csoporttagságok kezelését, a vállalati alkalmazásokhoz való hozzáférést és a szerepkör-hozzárendeléseket. A felhasználói hozzáférés rendszeresen felülvizsgálható, hogy csak a megfelelő felhasználók férhessenek hozzá. 

Tekintse át a diagnosztikai naplókat a Cognitive Searchról a keresési szolgáltatás végpontjának tevékenységéhez, például az indexelés kezeléséhez, az indexelési populációhoz és a lekérdezésekhez.

- [Az Azure AD jelentéskészítés ismertetése](/azure/active-directory/reports-monitoring/)

- [Az Azure AD identitás- és hozzáférési felülvizsgálatainak használata](../active-directory/governance/access-reviews-overview.md)

- [Az Azure Cognitive Search működésének és tevékenységének figyelése](search-monitor-usage.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3,11: a figyelő megkísérli a deaktivált hitelesítő adatok elérését

**Útmutató**: Azure Active Directory-hozzáférés (Azure ad) bejelentkezési tevékenység, naplózás és kockázati Eseménynapló-források, lehetővé teszi az integrációt bármely Siem vagy monitoring eszközzel.

Az Azure AD-felhasználói fiókok diagnosztikai beállításainak létrehozásával és a naplók és bejelentkezési naplók Log Analytics munkaterületre való elküldésével egyszerűsítheti ezt a folyamatot. A kívánt riasztások konfigurálása Log Analytics munkaterületen belül.

- [Azure-beli Tevékenységnaplók integrálása a Azure Monitor](/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="312-alert-on-account-login-behavior-deviation"></a>3,12: riasztás a fiók bejelentkezési viselkedésének eltérése esetén

**Útmutató**: az Azure Active Directory (Azure ad) Identity Protection-funkciókkal konfigurálhatja a felhasználói identitásokkal kapcsolatos gyanús műveletekre vonatkozó automatizált válaszokat. Az Azure Sentinelbe való betöltéssel további vizsgálatra van szükség.

- [Az Azure AD kockázatos bejelentkezéseinek megtekintése](../active-directory/identity-protection/overview-identity-protection.md)

- [Az Identity Protection kockázati házirendjeinek konfigurálása és engedélyezése](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md) 

- [Az Azure Sentinel előkészítése](../sentinel/quickstart-onboard.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

## <a name="data-protection"></a>Adatvédelem

*További információ: [Azure Security Benchmark: Adatvédelem](../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4,1: bizalmas információk leltárának fenntartása

**Útmutató**: a címkék használatával segítheti a bizalmas adatokat tároló vagy feldolgozó Azure-erőforrások nyomon követését.

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4,2: bizalmas adatok tárolására vagy feldolgozására szolgáló rendszerek elkülönítése

**Útmutató**: különálló előfizetések és/vagy felügyeleti csoportok megvalósítása fejlesztési, tesztelési és éles környezetekhez. Az erőforrásokat virtuális hálózattal/alhálózattal kell elválasztani, megfelelően címkézve, és egy hálózati biztonsági csoporton vagy Azure Firewallon belül kell biztosítani. A bizalmas adatokat tároló vagy feldolgozó erőforrások elkülönítését el kell különíteni. A privát hivatkozás használatával konfigurálja a Cognitive Search a privát végpontot.

- [További Azure-előfizetések létrehozása](../cost-management-billing/manage/create-subscription.md)

- [Címkék létrehozása és használata](../azure-resource-manager/management/tag-resources.md)

- [Privát végpont létrehozása Cognitive Searchhoz](service-create-private-endpoint.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4,3: a bizalmas adatok jogosulatlan átvitelének figyelése és letiltása

**Útmutató**: külső féltől származó megoldás használata az Azure Marketplace-en a hálózati kerületekben a bizalmas információk jogosulatlan átvitelének figyelése és az ilyen átvitelek blokkolása az adatbiztonsági szakemberek értesítése mellett.

A Microsoft kezeli a mögöttes platformot, és az összes vásárlói tartalmat bizalmasként kezeli, és védelmet biztosít az ügyfelek adatvesztése és a kitettség ellen. Annak biztosítása érdekében, hogy az Azure-beli ügyféladatok biztonságban maradjanak, a Microsoft végrehajtotta és karbantartja a robusztus adatvédelmi szabályozást és képességeket.

- [Az ügyféladatok Azure-beli védelmének ismertetése](../security/fundamentals/protection-customer-data.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4,5: aktív felderítési eszköz használata a bizalmas adatok azonosítására

**Útmutató**: az adatazonosítási, besorolási és veszteség-megelőzési funkciók még nem érhetők el Cognitive Search számára. Szükség esetén hozzon létre egy külső gyártótól származó megoldást. 

A Microsoft kezeli a mögöttes platformot, és az összes vásárlói tartalmat bizalmasként kezeli, és védelmet biztosít az ügyfelek adatvesztése és a kitettség ellen. Annak biztosítása érdekében, hogy az Azure-beli ügyféladatok biztonságban maradjanak, a Microsoft végrehajtotta és karbantartja a robusztus adatvédelmi szabályozást és képességeket.

- [Az ügyféladatok Azure-beli védelmének ismertetése](../security/fundamentals/protection-customer-data.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="46-use-azure-rbac-to-manage-access-to-resources"></a>4,6: az erőforrásokhoz való hozzáférés kezelése az Azure RBAC

**Útmutató**: a szolgáltatás felügyeletéhez használja az Azure szerepköralapú hozzáférés-vezérlést (Azure RBAC) a kulcsok és a konfiguráció elérésének kezeléséhez. A tartalmi műveletekhez, például az indexeléshez és a lekérdezésekhez, Cognitive Search az identitás-alapú hozzáférés-vezérlési modell helyett kulcsokat használ. Az Azure RBAC segítségével szabályozhatja a kulcsokhoz való hozzáférést. 

 
- [Az Azure RBAC konfigurálása](../role-based-access-control/role-assignments-portal.md)  
 
 
- [Szerepkörök használata rendszergazdai hozzáféréshez Cognitive Search](search-security-rbac.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="48-encrypt-sensitive-information-at-rest"></a>4,8: bizalmas adatok titkosítása a nyugalmi állapotban

**Útmutató**: a Cognitive Search automatikusan titkosítja az indexelt tartalmat a Microsoft által felügyelt kulcsokkal. Ha további védelemre van szükség, egy második titkosítási réteggel kiegészítheti az alapértelmezett titkosítást a Azure Key Vault-ben létrehozott és kezelt kulcsok használatával.

- [Ügyfél által felügyelt kulcsok konfigurálása az adattitkosításhoz az Azure-ban Cognitive Search](search-security-manage-encryption-keys.md)

- [Inaktív adatok Azure-ban való titkosításának ismertetése](../security/fundamentals/encryption-atrest.md)

**Felelősség**: Megosztott

**Azure Security Center figyelés**: nincs

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4,9: a kritikus Azure-erőforrások változásainak naplózása és riasztása

**Útmutató**: a Azure monitor és az Azure-tevékenység naplójának használata riasztások létrehozásához, amikor a módosítások a Cognitive Search és más kritikus vagy kapcsolódó erőforrások éles példányain lépnek életbe. 

 
- [Riasztások létrehozása az Azure-tevékenységek naplózási eseményeihez](../azure-monitor/alerts/alerts-activity-log.md) 

 
- [Riasztások létrehozása Cognitive Search tevékenységekhez](search-monitor-logs.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

## <a name="inventory-and-asset-management"></a>Leltár-és eszközfelügyelet

*További információt az [Azure biztonsági teljesítményteszt: leltár és eszközkezelés](../security/benchmarks/security-control-inventory-asset-management.md)című témakörben talál.*

### <a name="61-use-automated-asset-discovery-solution"></a>6,1: automatikus eszköz-felderítési megoldás használata

**Útmutató**: az Azure Resource Graph használatával lekérdezheti és felderítheti az előfizetésekben lévő összes erőforrást (például a számítási, tárolási, hálózati, portokat, protokollokat stb.).

Győződjön meg arról, hogy a bérlőben a megfelelő (olvasási) engedélyek szerepelnek, valamint az összes Azure-előfizetés enumerálása, valamint az előfizetések erőforrásai.

- [Lekérdezések létrehozása az Azure Resource Graph Explorerrel](../governance/resource-graph/first-query-portal.md)

- [Azure-előfizetések megtekintése](https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-4.8.0&amp;preserve-view=true)

- [Az Azure RBAC ismertetése](../role-based-access-control/overview.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="62-maintain-asset-metadata"></a>6,2: az eszköz metaadatainak fenntartása

**Útmutató**: címkéket alkalmazhat az Azure-erőforrásokra metaadatok használatával, hogy logikailag szervezze őket a besorolásba.

- [Címkék létrehozása és használata](../azure-resource-manager/management/tag-resources.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="63-delete-unauthorized-azure-resources"></a>6,3: jogosulatlan Azure-erőforrások törlése

**Útmutató**: a címkézés, a felügyeleti csoportok és az elkülönített előfizetések használata, ha szükséges, az eszközök rendszerezéséhez és nyomon követéséhez. Rendszeres időközönként egyeztetheti a leltárt, és gondoskodhat arról, hogy a jogosulatlan erőforrások törlése az előfizetésből időben történjen.

- [További Azure-előfizetések létrehozása](../cost-management-billing/manage/create-subscription.md)

- [Felügyeleti csoportok létrehozása](../governance/management-groups/create-management-group-portal.md)

- [Címkék létrehozása és használata](../azure-resource-manager/management/tag-resources.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="64-define-and-maintain-an-inventory-of-approved-azure-resources"></a>6,4: jóváhagyott Azure-erőforrások leltárának meghatározása és karbantartása

**Útmutató**: az indexeléssel és a készségkészlet feldolgozással kapcsolatos jóváhagyott Azure-erőforrások listájának meghatározása Cognitive Searchban.

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="65-monitor-for-unapproved-azure-resources"></a>6,5: a nem jóváhagyott Azure-erőforrások figyelése

**Útmutató**: azt javasoljuk, hogy olyan Azure-erőforrások leltárát határozza meg, amelyeket korábban a szervezeti szabályzatok és szabványok alapján jóváhagyott, majd figyelje a nem jóváhagyott Azure-erőforrásokat Azure Policy vagy Azure Resource Graph használatával.

- [Az Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md) 

- [Lekérdezések létrehozása az Azure Graph használatával](../governance/resource-graph/first-query-portal.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="69-use-only-approved-azure-services"></a>6,9: csak jóváhagyott Azure-szolgáltatások használata

**Útmutató**: a Azure Policy használatával korlátozásokat helyezhet el az ügyfél-előfizetésekben létrehozható erőforrások típusától a következő beépített szabályzat-definíciók használatával:
- Nem engedélyezett erőforrástípusok
- Engedélyezett erőforrástípusok

Az Azure Resource Graph használatával kérdezheti le vagy derítheti fel az előfizetése (ke) n belüli erőforrásait. Győződjön meg arról, hogy a környezetben lévő összes Azure-erőforrás jóvá van hagyva.

- [Az Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md)

- [Adott erőforrástípus megtagadása a következővel Azure Policy](/azure/governance/policy/samples/)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6,11: korlátozza a felhasználók képességét a Azure Resource Manager való interakcióra

**Útmutató**: az Azure feltételes hozzáférés használatával korlátozhatja a felhasználók a Azure Resource Manager interakcióját a "Microsoft Azure felügyelet" alkalmazás "hozzáférés tiltása" beállításával.  

 
Az összes többi műveletre vonatkozó kérések hitelesítéséhez használt kulcsokhoz való hozzáférés szabályozása, különösen a Cognitive Searchsal kapcsolatos tartalomhoz kapcsolódóan.

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

## <a name="secure-configuration"></a>Biztonságos konfiguráció

*További információt az [Azure biztonsági teljesítményteszt: biztonságos konfiguráció](../security/benchmarks/security-control-secure-configuration.md)című témakörben talál.*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7,1: biztonságos konfigurációk létrehozása az összes Azure-erőforráshoz

**Útmutató**: Azure Policy aliasok használata a "Microsoft. Search" névtérben egyéni szabályzatok létrehozásához az Azure Cognitive Search-erőforrások konfigurációjának naplózásához vagy érvényesítéséhez. Használhat beépített Azure Policy-definíciókat is Cognitive Search-szolgáltatásokhoz, például:

Naplózás engedélyezése az Azure-erőforrásokhoz

A Azure Resource Manager lehetővé teszi a sablon exportálását JavaScript Object Notation (JSON), amelyet át kell tekinteni annak érdekében, hogy a konfigurációk megfeleljenek a szervezete biztonsági követelményeinek.

A Azure Security Center javaslatai az Azure-erőforrások biztonságos alapkonfigurációja is használhatók.

- [Azure Policy az Azure-ra vonatkozó szabályozási megfelelőségi szabályozások Cognitive Search](security-controls-policy.md)

- [Az elérhető Azure Policy aliasok megtekintése](https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-4.8.0&amp;preserve-view=true)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="73-maintain-secure-azure-resource-configurations"></a>7,3: biztonságos Azure-erőforrás-konfigurációk karbantartása

**Útmutató**: a Azure Policy [megtagadás] és [üzembe helyezés, ha nem léteznek] effektusok használata a Cognitive Search szolgáltatási erőforrásainak biztonságos beállításainak érvényesítéséhez. 

Azure Resource Manager sablonokat a szervezete számára szükséges Azure-erőforrások biztonsági beállításainak fenntartására használhatja. 

- [Azure Policy effektusok ismertetése](../governance/policy/concepts/effects.md)

- [Azure Policy az Azure-ra vonatkozó szabályozási megfelelőségi szabályozások Cognitive Search](security-controls-policy.md)

- [Szabályzatok létrehozása és kezelése a megfelelőség kikényszerítése céljából](../governance/policy/tutorials/create-and-manage.md)

- [Azure Resource Manager sablonok áttekintése](../azure-resource-manager/templates/overview.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="75-securely-store-configuration-of-azure-resources"></a>7,5: az Azure-erőforrások biztonságos tárolása

**Útmutató**: ha egyéni Azure Policy-definíciókat használ, az Azure DevOps vagy az Azure Repos segítségével biztonságosan tárolhatja és kezelheti a kódot.

- [Kód tárolása az Azure DevOps](/azure/devops/repos/git/gitworkflow)

- [Az Azure Repos dokumentációja](/azure/devops/repos/index)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7,7: az Azure-erőforrások konfigurációs felügyeleti eszközeinek üzembe helyezése

**Útmutató**: a Cognitive Search Service-erőforrások szabványos biztonsági konfigurációinak meghatározása és implementálása Azure Policy használatával. 

Az aliasok használatával egyéni házirendeket hozhat létre a hálózati konfigurációk naplózásához vagy érvényesítéséhez. Használhatja az adott erőforrásokhoz kapcsolódó beépített szabályzat-definíciókat is. 

Emellett a Azure Automation használatával is telepítheti a konfigurációs módosításokat, és kezelheti a házirendek kivételeit. 

- [Azure Policy az Azure-ra vonatkozó szabályozási megfelelőségi szabályozások Cognitive Search](security-controls-policy.md)

- [Az Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7,9: automatikus konfigurációs monitorozás megvalósítása Azure-erőforrásokhoz

**Útmutató**: a Security Center használatával végezheti el az Cognitive Search szolgáltatás erőforrásainak alapkonfigurációját.  Emellett a Azure Policy használatával figyelmeztesse és naplózhatja az erőforrás-konfigurációkat. 

- [Javaslatok szervizelése Azure Security Center](../security-center/security-center-remediate-recommendations.md)

- [Azure Policy az Azure-ra vonatkozó szabályozási megfelelőségi szabályozások Cognitive Search](security-controls-policy.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="711-manage-azure-secrets-securely"></a>7,11: az Azure-titkok biztonságos kezelése

**Útmutató**: az Azure felügyelt identitások használata a Azure Key Vaultekkel együtt a felhőalapú alkalmazások titkos felügyeletének egyszerűbbé tételéhez.

- [Felügyelt identitások használata Azure-erőforrásokhoz](../azure-app-configuration/howto-integrate-azure-managed-service-identity.md) 

- [Key Vault létrehozása](../key-vault/general/quick-create-portal.md)

- [Key Vault hitelesítés biztosítása felügyelt identitással](../key-vault/general/assign-access-policy-portal.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="712-manage-identities-securely-and-automatically"></a>7,12: az identitások biztonságos és automatikus kezelése

**Útmutató**: az Azure által felügyelt identitás használatával Cognitive Search hozzáférést biztosíthat más Azure-szolgáltatásokhoz, például a Key Vault és az indexelő adatforrásokhoz egy automatikusan felügyelt identitás használatával Azure Active Directory (Azure ad). A felügyelt identitások lehetővé teszik bármely olyan szolgáltatás hitelesítését, amely támogatja az Azure AD-hitelesítést, beleértve a Azure Key Vault is, a kódban szereplő hitelesítő adatok nélkül. 

- [Indexelő-kapcsolatok beállítása egy adatforráshoz felügyelt identitás használatával](search-howto-managed-identities-data-sources.md)

- [Az ügyfél által felügyelt kulcsok konfigurálása adattitkosításhoz felügyelt identitás használatával](search-security-manage-encryption-keys.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

## <a name="malware-defense"></a>Kártevők elleni védelem

*További információt az [Azure biztonsági teljesítményteszt: kártevők elleni védelem](../security/benchmarks/security-control-malware-defense.md)című témakörben talál.*

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8,2: a nem számítási Azure-erőforrásokra feltöltött fájlok előzetes vizsgálata

**Útmutató**: a nem számítási Azure-erőforrásokra feltöltött tartalmak előzetes vizsgálata, például Cognitive Search, Blob Storage, Azure SQL Database stb. 

Az Ön felelőssége, hogy előzetesen beszkennelje a nem számítási Azure-erőforrásokra feltöltött tartalmakat. A Microsoft nem fér hozzá az ügyféladatok eléréséhez, így az Ön nevében nem végezhet kártevő-ellenőrzéseket az ügyfél-tartalmakon.

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="83-ensure-antimalware-software-and-signatures-are-updated"></a>8,3: a kártevő szoftverek és az aláírások frissítésének ellenőrzése

**Útmutató**: nem alkalmazható a Cognitive Searchra. Nem teszi lehetővé, hogy a kártevők elleni megoldások telepítve legyenek az erőforrásaira. A Microsoft mögöttes platform kezeli a kártevő szoftverek és az aláírások frissítését.  

 
A szervezete tulajdonában lévő és a keresési megoldásban használt számítási erőforrások esetében kövesse az Security Center, a számítási alkalmazások című témakörben található javaslatokat, amelyekkel &amp; biztosítható, hogy az összes végpont naprakész legyen a legújabb aláírásokkal. Linux esetén használjon egy harmadik féltől származó kártevő-elhárító megoldást.

**Felelősség**: Megosztott

**Azure Security Center figyelés**: nincs

## <a name="data-recovery"></a>Adat-helyreállítás

*További információt az [Azure biztonsági teljesítményteszt: adat-helyreállítás](../security/benchmarks/security-control-data-recovery.md)című témakörben talál.*

### <a name="91-ensure-regular-automated-back-ups"></a>9,1: rendszeres automatizált biztonsági másolatok biztosítása

**Útmutató**: a keresési szolgáltatásokban tárolt tartalmakról nem készíthető biztonsági másolat Azure Backup vagy bármely más beépített mechanizmus használatával, de az alkalmazás forráskódja és az elsődleges adatforrások indexe is újraépíthető, vagy létrehozhat egy egyéni eszközt az indexelt tartalom lekéréséhez és tárolásához. 

 
- [GitHub-index – biztonsági másolat visszaállítása minta](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/index-backup-restore)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: teljes rendszerbiztonsági mentés és minden ügyfél által felügyelt kulcs biztonsági mentése

**Útmutató**: a Cognitive Search jelenleg nem támogatja az automatikus biztonsági mentést egy keresési szolgáltatásban lévő adatbiztonsági mentéshez, és manuális folyamattal kell biztonsági másolatot készíteni. Az ügyfél által felügyelt kulcsokról Azure Key Vault is készíthető biztonsági másolat.
 

- [Azure Cognitive Search index biztonsági mentése és visszaállítása](/samples/azure-samples/azure-search-dotnet-samples/azure-search-backup-restore-index/)

- [Key Vault kulcsok biztonsági mentése az Azure-ban](https://docs.microsoft.com/powershell/module/az.keyvault/backup-azkeyvaultkey?view=azps-4.8.0&amp;preserve-view=true)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: az összes biztonsági másolat ellenőrzése, beleértve az ügyfél által felügyelt kulcsokat

**Útmutató**: a Cognitive Search jelenleg nem támogatja az automatikus biztonsági mentést egy keresési szolgáltatásban található adatbiztonsági mentéshez, és manuális folyamattal kell biztonsági másolatot készíteni és visszaállítani. A biztonsági mentési folyamat végpontok közötti integritásának biztosítása érdekében rendszeresen végezze el a manuálisan biztonsági mentéssel ellátni kívánt tartalom adatvisszaállítását.

- [Azure Cognitive Search index biztonsági mentése és visszaállítása](/samples/azure-samples/azure-search-dotnet-samples/azure-search-backup-restore-index/)

- [Key Vault kulcsok visszaállítása az Azure-ban](https://docs.microsoft.com/powershell/module/az.keyvault/restore-azkeyvaultkey?view=azps-4.8.0&amp;preserve-view=true)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: a biztonsági másolatok és az ügyfél által felügyelt kulcsok védelmének biztosítása

**Útmutató**: a Cognitive Search jelenleg nem támogatja az automatikus biztonsági mentést egy keresési szolgáltatásban lévő adatbiztonsági mentéshez, és manuális folyamattal kell biztonsági másolatot készíteni.  Az ügyfél által felügyelt kulcsokról Azure Key Vault is készíthető biztonsági másolat.  

 
A kulcsok véletlen vagy rosszindulatú törléssel szembeni védelme érdekében engedélyezze a Key Vault a védelem lágy törlését és kiürítését. Ha az Azure Storage-t a manuális biztonsági másolatok tárolására használja, engedélyezze a Soft delete-et az adatok mentéséhez és helyreállításához, amikor blobokat vagy blob-pillanatképeket törölnek. 
 

 
- [Azure Cognitive Search index biztonsági mentése és visszaállítása](/samples/azure-samples/azure-search-dotnet-samples/azure-search-backup-restore-index/)
 

 
- [Helyreállítható törlés és a végleges törléssel szembeni védelem engedélyezése kulcstartóban](../storage/blobs/soft-delete-blob-overview.md) 

- [Soft DELETE az Azure Blob Storage-hoz](../storage/blobs/soft-delete-blob-overview.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

## <a name="incident-response"></a>Incidensmegoldás

*További információ: [Azure Security Benchmark: Incidensek kezelése](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10,1: incidens-válaszi útmutató létrehozása

**Útmutató**: incidensek kifejlesztése útmutató a szervezet számára. Győződjön meg arról, hogy van olyan írásos incidens-válasz, amely meghatározza a személyzet összes szerepkörét, valamint az incidensek kezelésének és kezelésének fázisait az incidensek vizsgálatát követően.

- [Útmutató a saját biztonsági incidensek megoldási folyamatának létrehozásához](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Microsoft Security Response Center – incidens anatómiája](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Az ügyfél a NIST számítógépes biztonsági incidensek kezelési útmutatóját is kihasználhatja a saját incidens-válasz tervének létrehozásával kapcsolatos támogatáshoz](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10,2: incidensek pontozásának és rangsorolási eljárásának létrehozása

**Útmutató**: a Security Center súlyosságot rendel az egyes riasztásokhoz, hogy a prioritások alapján ki lehessen deríteni, hogy mely riasztásokat kell először megvizsgálni. A súlyosság azon alapul, hogy a megbízhatósági Security Center hol van a megállapításban, illetve a riasztás kibocsátására használt analitikai módszer, valamint a riasztást eredményező tevékenység mögött rosszindulatú szándékkal rendelkező megbízhatósági szint.

Emellett megadhatja a címkéket használó előfizetéseket, és létrehozhat egy elnevezési rendszert az Azure-erőforrások azonosításához és kategorizálásához, különösen a bizalmas adatok feldolgozásához. Az Ön felelőssége, hogy rangsorolja a riasztások szervizelését az Azure-erőforrások és-környezet kritikus jellemzői alapján, ahol az incidens történt.

- [Címkék használata az Azure-erőforrások rendszerezéséhez](../azure-resource-manager/management/tag-resources.md)

- [Biztonsági riasztások az Azure Security Centerben](../security-center/security-center-alerts-overview.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="103-test-security-response-procedures"></a>10,3: biztonsági reagálási eljárások tesztelése

**Útmutató**: a rendszerek incidensek reagálási képességeinek rendszeres tesztelésére szolgáló gyakorlatok végrehajtása. Azonosítsa a gyenge pontokat és réseket, és szükség esetén dolgozza át a tervet.

- [Tekintse meg a NIST kiadványát, a "útmutató a teszteléshez, a képzéshez és az IT-csomagokhoz és-képességekhez.](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10,4: biztonsági incidensek elérhetőségének biztosítása és riasztási értesítések konfigurálása biztonsági incidensekhez

**Útmutató**: a Microsoft a biztonsági incidensek elérhetőségi adatait arra használja fel, hogy felvegye Önnel a kapcsolatot, ha a Microsoft Security Response Center (MSRC) felfedi, hogy az adatokat egy törvénytelen vagy jogosulatlan fél is hozzáférte. A problémák megoldása érdekében tekintse át az incidenseket a tény után.

- [A Azure Security Center biztonsági kapcsolattartó beállítása](../security-center/security-center-provide-security-contact-details.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10,5: biztonsági riasztások beépítése az incidensek gyorsreagáló rendszerébe

**Útmutató**: az Security Center-riasztások és-javaslatok exportálása a folyamatos exportálás funkció használatával. A folyamatos exportálás lehetővé teszi a riasztások vagy javaslatok manuális vagy folyamatos exportálását. Az Security Center adatösszekötővel továbbíthatja a riasztásokat az Azure Sentinel szolgáltatásba.

- [Folyamatos exportálás konfigurálása](../security-center/continuous-export.md)

- [Riasztások streamelése az Azure Sentinelbe](../sentinel/connect-azure-security-center.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="106-automate-the-response-to-security-alerts"></a>10,6: a biztonsági riasztásokra adott válasz automatizálása

**Útmutató**: a Azure Security Center munkafolyamat-automatizálási funkciója segítségével automatikusan aktiválhatja a válaszokat a "Logic apps" használatával a biztonsági riasztások és javaslatok esetében.

- [A Munkafolyamat-automatizálás és a Logic Apps konfigurálása](../security-center/workflow-automation.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

## <a name="penetration-tests-and-red-team-exercises"></a>Behatolási tesztek és Red Team-gyakorlatok

*További információkért tekintse meg az [Azure biztonsági teljesítményteszt: behatolási tesztek és a Red Team gyakorlatok](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md)című témakört.*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11,1: az Azure-erőforrások rendszeres behatolásának tesztelése, valamint az összes kritikus biztonsági vizsgálat szervizelésének biztosítása

**Útmutató**: kövesse a Microsoft Cloud penetráció tesztelési szabályait, amelyekkel biztosíthatja, hogy a behatolási tesztek ne sértsék a Microsoft-házirendeket. A Microsoft által felügyelt felhőalapú infrastruktúrán, szolgáltatásokon és alkalmazásokon végzett riasztási és élő behatolási tesztek végrehajtásához használja a Microsoft stratégiáját és végrehajtási tervét.

- [Behatolástesztelési beavatkozási szabályok](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)

- [Riasztási tesztek a Microsoft-felhőben](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Felelősség**: Megosztott

**Azure Security Center figyelés**: nincs

## <a name="next-steps"></a>Következő lépések

- [Az Azure Security Benchmark v2 áttekintésének](/azure/security/benchmarks/overview) megtekintése
- További tudnivalók az [Azure biztonsági alapterveiről](/azure/security/benchmarks/security-baselines-overview)
