---
title: Azure biztonsági alapkonfiguráció az Azure Cognitive Search
description: Az Azure Cognitive Search biztonsági alapterve az Azure biztonsági Teljesítménytesztben meghatározott biztonsági javaslatok megvalósítására szolgáló eljárási útmutatást és erőforrásokat biztosít.
author: msmbaldwin
ms.service: search
ms.topic: conceptual
ms.date: 09/25/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 8c32d06247516611fc79bd6f77b60fec6b73044d
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/17/2021
ms.locfileid: "100591803"
---
# <a name="azure-security-baseline-for-azure-cognitive-search"></a>Azure biztonsági alapkonfiguráció az Azure Cognitive Search

Ez a biztonsági alapkonfiguráció az [Azure biztonsági teljesítményteszt 1,0-es verziójáról](../security/benchmarks/overview.md) az Azure Cognitive Search-ra vonatkozó útmutatást alkalmazza. Az Azure Security Benchmark ajánlásokat ad arra nézve, hogy hogyan tehetők biztonságossá a felhőalapú megoldások az Azure-ban. A tartalom az Azure biztonsági teljesítményteszt által meghatározott **biztonsági vezérlők** szerint van csoportosítva, valamint az Azure Cognitive Search-ra vonatkozó kapcsolódó útmutatás. Az Azure Cognitive Searchra nem alkalmazható **vezérlők** , vagy az ügyfél ki lett zárva.

Ha szeretné megtekinteni, hogy az Azure hogyan Cognitive Search teljes mértékben az Azure biztonsági teljesítményteszttel, tekintse meg a [teljes azure Cognitive Search biztonsági alapterv-leképezési fájlt](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="network-security"></a>Hálózati biztonság

*További információt az [Azure biztonsági teljesítményteszt: hálózati biztonság](../security/benchmarks/security-control-network-security.md)című témakörben talál.*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1,1: Azure-erőforrások biztosítása virtuális hálózatokon belül

**Útmutató**: gondoskodjon arról, hogy minden Microsoft Azure Virtual Network alhálózati üzemelő példány rendelkezzen olyan hálózati biztonsági csoporttal, amely a "legkevésbé privilegizált" hozzáférési séma megvalósítására vonatkozó szabályokkal van alkalmazva. Csak az alkalmazás megbízható portjaihoz és IP-címeihez való hozzáférés engedélyezése. Az Azure Cognitive Search üzembe helyezése egy Azure Private-végponttal, ahol lehetséges, a virtuális hálózatról való privát hozzáférés engedélyezése a szolgáltatásokhoz.

A Cognitive Search a hálózati hozzáférés-vezérlési listák felügyeletéhez is támogatja a további hálózati biztonsági funkciókat. Konfigurálja úgy a keresési szolgáltatást, hogy csak a megbízható forrásokkal való kommunikációt engedélyezze, ha korlátozza a hozzáférést az adott nyilvános IP-címtartományok számára a tűzfal funkciójának használatával.

- [Privát végpontok konfigurálása az Azure Cognitive Search](./service-create-private-endpoint.md)

- [Az Azure Cognitive Search tűzfal konfigurálása](./service-configure-firewall.md)

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-nics"></a>1,2: a virtuális hálózatok, alhálózatok és hálózati adapterek konfigurációjának és forgalmának figyelése és naplózása

**Útmutató**: a Cognitive Search nem helyezhetők üzembe közvetlenül virtuális hálózatban. Ha azonban az ügyfélalkalmazás vagy az adatforrások egy virtuális hálózaton vannak, figyelheti és naplózhatja a hálózati összetevők forgalmát, beleértve a felhőben a keresési szolgáltatásnak küldött kéréseket is. A standard javaslatok közé tartozik a hálózati biztonsági csoport folyamatábrájának engedélyezése és a naplók küldése az Azure Storage-ba vagy egy Log Analytics-munkaterületre. Igény szerint Traffic Analytics is használhat a forgalmi minták elemzéséhez.

- [Hálózati biztonsági csoport folyamatábráinak engedélyezése](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [A Traffic Analytics engedélyezése és használata](../network-watcher/traffic-analytics.md)

- [A Azure Security Center által biztosított hálózati biztonság ismertetése](../security-center/security-center-network-recommendations.md)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="13-protect-critical-web-applications"></a>1,3: a kritikus webalkalmazások megóvása

**Útmutató**: nem alkalmazható a Cognitive Searchra. Ez a javaslat Azure App Service vagy számítási erőforrásokon futó webalkalmazásokhoz készült.

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: nem alkalmazható

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1,4: az ismert kártékony IP-címekkel folytatott kommunikáció megtagadása

**Útmutató**: a Cognitive Search nem biztosít konkrét szolgáltatást az elosztott szolgáltatásmegtagadási támadások elleni védelemhez, de az általános védelmet biztosító Cognitive Search szolgáltatáshoz társított virtuális hálózatok esetében engedélyezheti a DDoS Protection standard beállítását.

- [A DDoS Protection konfigurálása](../ddos-protection/manage-ddos-protection.md)

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="15-record-network-packets"></a>1,5: hálózati csomagok rögzítése

**Útmutató**: engedélyezze a hálózati biztonsági csoport folyamatábráit azon hálózati biztonsági csoportok számára, amelyek védik az Azure Virtual Machinest (VM), amely a Cognitive Search szolgáltatáshoz fog csatlakozni. Naplók küldése Azure Storage-fiókba forgalom naplózásához. 

Ha a rendellenes tevékenységek kivizsgálásához szükséges, engedélyezze Network Watcher csomagok rögzítését.

- [A NSG folyamat naplófájljainak engedélyezése](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [A Network Watcher engedélyezése](../network-watcher/network-watcher-create.md)

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1,6: hálózati behatolás-észlelési/Behatolás-megelőzési rendszerek (AZONOSÍTÓk/IP-címek) üzembe helyezése

**Útmutató**: a Cognitive Search nem támogatja a hálózati behatolás észlelését, de a behatolás mérséklése lehetővé teszi a tűzfalszabályok konfigurálását a Cognitive Search szolgáltatás által elfogadott IP-címek megadásához. Konfiguráljon egy privát végpontot, hogy a forgalom a nyilvános internetről is elérhető legyen.

- [Az ügyfél által felügyelt kulcsok konfigurálása adattitkosításhoz](./search-security-manage-encryption-keys.md)

- [Az ügyfelek által felügyelt legfontosabb információk beszerzése az indexekről és a szinonimák térképe](./search-security-get-encryption-keys.md)

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="17-manage-traffic-to-web-applications"></a>1,7: webalkalmazások forgalmának kezelése

**Útmutató**: nem alkalmazható a Cognitive Searchra. Ez a javaslat Azure App Service vagy számítási erőforrásokon futó webalkalmazásokhoz készült.

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: nem alkalmazható

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1,8: a hálózati biztonsági szabályok bonyolultságának és adminisztratív terhelésének csökkentése

**Útmutató**: a szolgáltatási címkék használata, ha indexelő és szakértelmével használ a Cognitive Searchban, hogy olyan IP-címtartományt képviseljék, amely jogosult a külső erőforrásokhoz való kapcsolódásra. 

Erőforrások forgalmának engedélyezése vagy megtagadása a szabály megfelelő forrás vagy cél mezőjében szereplő szolgáltatási címke nevének (például AzureCognitiveSearch) megadásával. 

- [Virtuális hálózati szolgáltatás címkéi](../virtual-network/service-tags-overview.md)

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1,9: a hálózati eszközök szabványos biztonsági konfigurációinak fenntartása

**Útmutató**: a Cognitive Search nem rendelkezik a hálózati erőforrásokhoz, vagy nem függ a tervezéstől. Előfordulhat, hogy a keresési alkalmazáshoz kapcsolódó ügyfélalkalmazások és adatforrások egy virtuális hálózaton vannak, de a keresési szolgáltatás nem a hálózatban van telepítve. 

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: nem alkalmazható

### <a name="110-document-traffic-configuration-rules"></a>1,10: a dokumentum forgalmának konfigurációs szabályai

**Útmutató**: Cognitive Search konfigurálható egy Azure privát végponttal a keresési szolgáltatás virtuális hálózatba való integrálásához.  Használjon erőforrás-címkéket a hálózati biztonsági csoportokhoz és a hálózati biztonsággal és a forgalommal kapcsolatos egyéb erőforrásokhoz. Az egyes hálózati biztonsági csoportokra vonatkozó szabályok esetében a "Leírás" mező használatával dokumentálhatja a hálózatra vagy hálózatra irányuló forgalmat engedélyező szabályokat. 

A címkézéshez kapcsolódó beépített Azure Policy definíciók bármelyikét használhatja, például a "címke és az érték megkövetelése" hatásokat, hogy az összes erőforrás címkével legyen létrehozva, és értesítse a meglévő címkézetlen erőforrásokról. 

A Azure PowerShell vagy az Azure CLI használatával a címkék alapján kereshet és végezhet műveleteket az erőforrásokon. 

- [Privát végpont létrehozása Cognitive Searchhoz](./service-create-private-endpoint.md)

- [Címkék létrehozása és használata](../azure-resource-manager/management/tag-resources.md)

- [Azure-Virtual Network létrehozása](../virtual-network/quick-create-portal.md)

- [Hálózati forgalom szűrése hálózati biztonsági csoport szabályaival](../virtual-network/tutorial-filter-network-traffic.md)

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1,11: automatikus eszközök használata a hálózati erőforrások konfigurációjának figyelésére és a változások észlelésére

**Útmutató**: Cognitive Search nem rendelkezik vagy függ a hálózati összetevőktől, így az erőforrások konfigurációit nem lehet figyelni.

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: nem alkalmazható

## <a name="logging-and-monitoring"></a>Naplózás és figyelés

*További információt az [Azure biztonsági teljesítményteszt: naplózás és figyelés](../security/benchmarks/security-control-logging-monitoring.md)című témakörben talál.*

### <a name="21-use-approved-time-synchronization-sources"></a>2,1: a jóváhagyott idő-szinkronizálási források használata

**Útmutató**: a Cognitive Search nem támogatja a saját időszinkronizálási források konfigurálását. A keresési szolgáltatás a Microsoft időszinkronizálási forrásaira támaszkodik, és nem teszi elérhetővé az ügyfelek számára a konfigurálást.

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Microsoft

### <a name="22-configure-central-security-log-management"></a>2,2: a központi biztonsági naplók felügyeletének konfigurálása

**Útmutató**: Cognitive Searchhoz kapcsolódó naplók beolvasása Azure monitor használatával a végponti eszközök, a hálózati erőforrások és más biztonsági rendszerek által generált biztonsági adatokat összesítve. Azure Monitor a Log Analytics-munkaterületek használatával kérdezheti le és végezheti el az elemzéseket, és használhatja az Azure Storage-fiókokat hosszú távú és archiválási tároláshoz.
Alternatív megoldásként engedélyezheti és elvégezheti ezeket az adattáblákat az Azure Sentinel vagy egy harmadik féltől származó SIEM-nek.

- [Ismerkedés a Azure Monitor és a harmadik féltől származó SIEM-integrációval](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/)

- [Platform-naplók és-metrikák összegyűjtése Azure Monitor](../azure-monitor/essentials/diagnostic-settings.md)

- [Az Azure Sentinel előkészítése](../sentinel/quickstart-onboard.md)

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="23-enable-audit-logging-for-azure-resources"></a>2,3: az Azure-erőforrások naplózásának engedélyezése

**Útmutató**: a diagnosztikai és üzemeltetési naplók betekintést nyújtanak Cognitive Search részletes műveleteibe, és hasznosak a szolgáltatás figyeléséhez és a szolgáltatáshoz hozzáférő munkaterhelésekhez.  A diagnosztikai adatok rögzítéséhez engedélyezze a naplózási adatok tárolási helyének megadását.

- [Az Azure-Cognitive Search naplózási adatainak összegyűjtése és elemzése](./search-monitor-logs.md)

- [Platform-naplók és-metrikák összegyűjtése Azure Monitor](../azure-monitor/essentials/diagnostic-settings.md) 

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="24-collect-security-logs-from-operating-systems"></a>2,4: biztonsági naplók gyűjtése az operációs rendszerekből

**Útmutató**: nem alkalmazható a Cognitive Searchra. Ez a javaslat számítási erőforrások számára készült.

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: nem alkalmazható

### <a name="25-configure-security-log-storage-retention"></a>2,5: a biztonsági napló tárolási adatmegőrzésének konfigurálása

**Útmutató**: a diagnosztikai mérőszámokra beérkező korábbi adatokat alapértelmezés szerint 30 napig Cognitive Search megőrizni. A hosszú megőrzés érdekében ügyeljen arra, hogy engedélyezze a tárolási lehetőséget a naplózott események és mérőszámok megőrzése érdekében.

Azure Monitor a szervezet megfelelőségi szabályainak megfelelően állítsa be a Log Analytics munkaterület megőrzési időszakát. Azure Storage-fiókokat használhat hosszú távú és archiválási tároláshoz. 

- [Az adatmegőrzési időszak módosítása Log Analytics](../azure-monitor/logs/manage-cost-storage.md#change-the-data-retention-period)

- [Adatmegőrzési szabályzat konfigurálása az Azure Storage-fiók naplóihoz](../storage/common/storage-monitor-storage-account.md#configure-logging)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="26-monitor-and-review-logs"></a>2,6: naplók figyelése és áttekintése

**Útmutató**: a Cognitive Search szolgáltatás naplófájljainak elemzése és figyelése rendellenes működéshez. A naplók áttekintéséhez és a naplózási adatok lekérdezéséhez használja a Azure Monitor Log Analytics. Alternatív megoldásként engedélyezheti és elvégezheti az Azure Sentinel vagy egy harmadik fél SIEM-nek.

- [A Cognitive Search naplózási adatainak összegyűjtése és elemzése](./search-monitor-logs.md)

- [Keresési naplóban tárolt adatPower BIek megjelenítése](./search-monitor-logs-powerbi.md)

- [Az Azure Sentinel előkészítése](../sentinel/quickstart-onboard.md)

- [További tudnivalók a Log Analytics szolgáltatásról](../azure-monitor/logs/log-analytics-tutorial.md)

- [Egyéni lekérdezések végrehajtása a Azure Monitorban](../azure-monitor/logs/get-started-queries.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="27-enable-alerts-for-anomalous-activities"></a>2,7: riasztások engedélyezése rendellenes tevékenységekhez

**Útmutató**: Security Center használata log Analytics munkaterülettel a biztonsági naplókban és eseményekben észlelt rendellenes tevékenységekkel kapcsolatos figyeléshez és riasztáshoz. Alternatív megoldásként engedélyezheti és elvégezheti az Azure Sentinel szolgáltatásban való ellátását.

- [Az Azure Sentinel előkészítése](../sentinel/quickstart-onboard.md)

- [Riasztások kezelése Azure Security Centerban](../security-center/security-center-managing-and-responding-alerts.md)

- [Riasztás a log Analytics-naplófájlok adatkezeléséről](../azure-monitor/alerts/tutorial-response.md)

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="28-centralize-anti-malware-logging"></a>2,8: kártevő szoftverek közötti naplózás központosítása

**Útmutató**: nem alkalmazható a Cognitive Searchra. A Microsoft kezeli az alapul szolgáló platform kártevő-elhárító megoldását.

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: nem alkalmazható

### <a name="29-enable-dns-query-logging"></a>2,9: DNS-lekérdezések naplózásának engedélyezése

**Útmutató**: nem alkalmazható a Cognitive Searchra. A DNS-naplókat nem hozza létre és nem használja fel.

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: nem alkalmazható

### <a name="210-enable-command-line-audit-logging"></a>2,10: parancssori naplózás engedélyezése

**Útmutató**: nem alkalmazható a Cognitive Searchra. A parancssori naplózás Cognitive Search esetén nem érhető el.

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: nem alkalmazható

## <a name="identity-and-access-control"></a>Identitás- és hozzáférés-vezérlés

*További információt az [Azure biztonsági teljesítményteszt: identitás-és hozzáférés-vezérlés](../security/benchmarks/security-control-identity-access-control.md)című témakörben talál.*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3,1: a felügyeleti fiókok leltárának karbantartása

**Útmutató**: az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC) lehetővé teszi az Azure-erőforrásokhoz való hozzáférés kezelését a szerepkör-hozzárendeléseken keresztül. Ezeket a szerepköröket hozzárendelheti a felhasználókhoz, a csoportok egyszerű szolgáltatásaihoz és a felügyelt identitásokhoz. Bizonyos erőforrásokhoz előre meghatározott beépített szerepkörök tartoznak, és ezek a szerepkörök olyan eszközökkel leltározhatók vagy kérdezhetők le, mint az Azure CLI, az Azure PowerShell vagy az Azure Portal.

Cognitive Search szerepkörök olyan engedélyekkel vannak társítva, amelyek támogatják a szolgáltatási szint felügyeleti feladatait.  Ezek a szerepkörök nem biztosítanak hozzáférést a szolgáltatás végpontjának. A végpontra irányuló műveletekhez való hozzáférés (például az indexelés kezelése, az indexek sokasága és a keresési adatok lekérdezése) az API-kulcsok használatával hitelesítheti a kérést.

- [Szerepkörök beállítása rendszergazdai hozzáféréshez az Azure Cognitive Search](./search-security-rbac.md)

- [Azure Cognitive Search-szolgáltatás API-kulcsainak létrehozása és kezelése](./search-security-api-keys.md)

- [Címtárbeli szerepkör beszerzése az Azure AD-ben a PowerShell-lel](/powershell/module/azuread/get-azureaddirectoryrole)
- [Címtárbeli szerepkör tagjainak beszerzése az Azure AD-ben a PowerShell-lel](/powershell/module/azuread/get-azureaddirectoryrolemember)

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="32-change-default-passwords-where-applicable"></a>3,2: az alapértelmezett jelszavak módosítása, ha alkalmazható

**Útmutató**: nem alkalmazható a Cognitive Searchra. Nem rendelkezik az alapértelmezett jelszavak fogalmával.

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: nem alkalmazható

### <a name="33-use-dedicated-administrative-accounts"></a>3,3: dedikált rendszergazdai fiókok használata

**Útmutató**: a Cognitive Search nem rendelkezik olyan helyi szintű vagy Azure Active Directory (Azure ad) rendszergazdai fiókokkal, amelyek az indexek és műveletek kezelésére használhatók. 

Használja az Azure AD beépített szerepköreit, amelyeket explicit módon hozzá kell rendelni a felügyeleti műveletekhez. Az Azure AD PowerShell-modul meghívásával ad hoc lekérdezéseket hajthat végre a felügyeleti csoportok tagjait futtató fiókok felderítése érdekében.

- [Szerepkörök használata rendszergazdai hozzáféréshez Cognitive Search](./search-security-rbac.md)

- [Címtárbeli szerepkör beszerzése az Azure AD-ben a PowerShell-lel](/powershell/module/azuread/get-azureaddirectoryrole)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3,4: egyszeri bejelentkezés (SSO) használata Azure Active Directory

**Útmutató**: az Azure Active Directory (Azure ad) szolgáltatással történő egyszeri bejelentkezéses hitelesítés használata a keresési szolgáltatási információk eléréséhez a Azure Resource Manager által támogatott felügyeleti műveletekhez. 

Hozzon létre egy folyamatot az identitások és a hitelesítő adatok számának csökkentéséhez azáltal, hogy engedélyezi az egyszeri bejelentkezést a szolgáltatásnak a szervezete már meglévő identitásával.

- [Az egyszeri bejelentkezés ismertetése az Azure AD-vel](../active-directory/manage-apps/what-is-single-sign-on.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3,5: Multi-Factor Authentication használata az összes Azure Active Directory-alapú hozzáféréshez

**Útmutató**: a Azure Active Directory (Azure AD) multi-Factor Authentication (MFA) funkciójának engedélyezése és a Security Center identitás-és hozzáférési javaslatainak követése.

- [MFA engedélyezése az Azure-ban](../active-directory/authentication/howto-mfa-getstarted.md) 

- [Identitás és hozzáférés figyelése Azure Security Centeron belül](../security-center/security-center-identity-access.md) 

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3,6: dedikált gépek (privilegizált hozzáférési munkaállomások) használata az összes felügyeleti feladathoz

**Útmutató**: az Azure-erőforrásokhoz való bejelentkezéshez és az Azure-erőforrások eléréséhez konfigurált multi-Factor Authentication (MFA) rendelkező, rendszerjogosultságú hozzáférési munkaállomás (Paw) használata.

- [A biztonságos, Azure által felügyelt munkaállomások ismertetése](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)
 

- [Az Azure AD MFA engedélyezése](../active-directory/authentication/howto-mfa-getstarted.md)
 

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3,7: naplózás és riasztás a gyanús tevékenységekről a rendszergazdai fiókoktól

**Útmutató**: a Azure Active Directory (Azure ad) biztonsági jelentéseinek és figyelésének használata annak észlelésére, hogy a környezetben gyanús vagy nem biztonságos tevékenységek történnek-e. A Security Center használatával figyelheti az identitás-és hozzáférési tevékenységeket.

- [A kockázatos tevékenységek miatt megjelölt Azure AD-felhasználók azonosítása](../active-directory/identity-protection/overview-identity-protection.md)

- [A felhasználók identitási és hozzáférési tevékenységeinek monitorozása az Azure Security Centerben](../security-center/security-center-identity-access.md)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="38-manage-azure-resources-only-from-approved-locations"></a>3,8: csak a jóváhagyott helyekről származó Azure-erőforrások kezelése

**Útmutató**: nem alkalmazható a Cognitive Searchra. A hozzáférés feltétele nem támogatja a jóváhagyott hely használatát.

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: nem alkalmazható

### <a name="39-use-azure-active-directory"></a>3,9: a Azure Active Directory használata

**Útmutató**: a Azure Active Directory (Azure ad) használata központi hitelesítési és engedélyezési rendszerként az Azure Cognitive Search szolgáltatás szintű felügyeleti feladatokhoz. Az Azure AD-identitások nem biztosítanak hozzáférést a keresési szolgáltatás végpontjának.  Az API-kulcsokon keresztül elérhetők az olyan műveletek, mint az indexelés, az indexelés és a keresési adatok lekérdezései.

- [Azure AD-példány létrehozása és konfigurálása](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

- [Azure Cognitive Search-szolgáltatás API-kulcsainak létrehozása és kezelése](./search-security-api-keys.md)

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="310-regularly-review-and-reconcile-user-access"></a>3,10: a felhasználói hozzáférés rendszeres áttekintése és egyeztetése

**Útmutató**: a Azure Active Directory (Azure ad) olyan naplókat biztosít, amelyek segítenek az elavult fiókok felderítésében. Az Azure AD identitás-és hozzáférési felülvizsgálatait használva hatékonyan kezelheti a csoporttagságok kezelését, a vállalati alkalmazásokhoz való hozzáférést és a szerepkör-hozzárendeléseket. A felhasználói hozzáférés rendszeresen felülvizsgálható, hogy csak a megfelelő felhasználók férhessenek hozzá. 

Tekintse át a diagnosztikai naplókat a Cognitive Searchról a keresési szolgáltatás végpontjának tevékenységéhez, például az indexelés kezeléséhez, az indexelési populációhoz és a lekérdezésekhez.

- [Az Azure AD jelentéskészítés ismertetése](../active-directory/reports-monitoring/index.yml)

- [Az Azure AD identitás- és hozzáférési felülvizsgálatainak használata](../active-directory/governance/access-reviews-overview.md)

- [Az Azure Cognitive Search működésének és tevékenységének figyelése](./search-monitor-usage.md)

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3,11: a figyelő megkísérli a deaktivált hitelesítő adatok elérését

**Útmutató**: Azure Active Directory-hozzáférés (Azure ad) bejelentkezési tevékenység, naplózás és kockázati Eseménynapló-források, lehetővé teszi az integrációt bármely Siem vagy monitoring eszközzel.

Az Azure AD-felhasználói fiókok diagnosztikai beállításainak létrehozásával és a naplók és bejelentkezési naplók Log Analytics munkaterületre való elküldésével egyszerűsítheti ezt a folyamatot. A kívánt riasztások konfigurálása Log Analytics munkaterületen belül.

- [Azure-beli Tevékenységnaplók integrálása a Azure Monitor](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md) 

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="312-alert-on-account-login-behavior-deviation"></a>3,12: riasztás a fiók bejelentkezési viselkedésének eltérése esetén

**Útmutató**: az Azure Active Directory (Azure ad) Identity Protection-funkciókkal konfigurálhatja a felhasználói identitásokkal kapcsolatos gyanús műveletekre vonatkozó automatizált válaszokat. Az Azure Sentinelbe való betöltéssel további vizsgálatra van szükség.

- [Az Azure AD kockázatos bejelentkezéseinek megtekintése](../active-directory/identity-protection/overview-identity-protection.md) 

- [Az Identity Protection kockázati házirendjeinek konfigurálása és engedélyezése](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md) 

- [Az Azure Sentinel előkészítése](../sentinel/quickstart-onboard.md) 

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3,13: a Microsoft számára elérhetővé teszi a megfelelő ügyféladatokat a támogatási forgatókönyvek során

**Útmutató**: nem alkalmazható a Cognitive Searchra. A Ügyfélszéf nem támogatja a Cognitive Search.

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: nem alkalmazható

## <a name="data-protection"></a>Adatvédelem

*További információt az [Azure biztonsági teljesítményteszt: Adatvédelem](../security/benchmarks/security-control-data-protection.md)című témakörben talál.*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4,1: bizalmas információk leltárának fenntartása

**Útmutató**: a címkék használatával segítheti a bizalmas adatokat tároló vagy feldolgozó Azure-erőforrások nyomon követését.

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4,2: bizalmas adatok tárolására vagy feldolgozására szolgáló rendszerek elkülönítése

**Útmutató**: különálló előfizetések és/vagy felügyeleti csoportok megvalósítása fejlesztési, tesztelési és éles környezetekhez. Az erőforrásokat virtuális hálózattal/alhálózattal kell elválasztani, megfelelően címkézve, és egy hálózati biztonsági csoporton vagy Azure Firewallon belül kell biztosítani. A bizalmas adatokat tároló vagy feldolgozó erőforrások elkülönítését el kell különíteni. A privát hivatkozás használatával konfigurálja a Cognitive Search a privát végpontot.

- [További Azure-előfizetések létrehozása](../cost-management-billing/manage/create-subscription.md) 

- [Címkék létrehozása és használata](../azure-resource-manager/management/tag-resources.md)

- [Privát végpont létrehozása Cognitive Searchhoz](./service-create-private-endpoint.md)

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4,3: a bizalmas adatok jogosulatlan átvitelének figyelése és letiltása

**Útmutató**: külső féltől származó megoldás használata az Azure Marketplace-en a hálózati kerületekben a bizalmas információk jogosulatlan átvitelének figyelése és az ilyen átvitelek blokkolása az adatbiztonsági szakemberek értesítése mellett.

A Microsoft kezeli a mögöttes platformot, és az összes vásárlói tartalmat bizalmasként kezeli, és védelmet biztosít az ügyfelek adatvesztése és a kitettség ellen. Annak biztosítása érdekében, hogy az Azure-beli ügyféladatok biztonságban maradjanak, a Microsoft végrehajtotta és karbantartja a robusztus adatvédelmi szabályozást és képességeket.

- [Az ügyféladatok Azure-beli védelmének ismertetése](../security/fundamentals/protection-customer-data.md) 

**Az Azure Security Center monitorozása**: Nem alkalmazható

**Felelősség**: Ügyfél

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4,4: minden bizalmas adat titkosítása az átvitel során

**Útmutató**: a Cognitive Search az Transport Layer Security 1,2-as adatforgalomban titkosítja az adatátvitelt, és minden kapcsolat esetében minden alkalommal kikényszeríti a titkosítást (SSL/TLS). Ez biztosítja, hogy az összes adatforgalom titkosítva legyen az ügyfél és a szolgáltatás között.

- [A titkosítás ismertetése az Azure-ban](../security/fundamentals/encryption-overview.md#encryption-of-data-in-transit) 

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Microsoft

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4,5: aktív felderítési eszköz használata a bizalmas adatok azonosítására

**Útmutató**: az adatazonosítási, besorolási és veszteség-megelőzési funkciók még nem érhetők el Cognitive Search számára. Szükség esetén hozzon létre egy külső gyártótól származó megoldást. 

A Microsoft kezeli a mögöttes platformot, és az összes vásárlói tartalmat bizalmasként kezeli, és védelmet biztosít az ügyfelek adatvesztése és a kitettség ellen. Annak biztosítása érdekében, hogy az Azure-beli ügyféladatok biztonságban maradjanak, a Microsoft végrehajtotta és karbantartja a robusztus adatvédelmi szabályozást és képességeket.

- [Az ügyféladatok Azure-beli védelmének ismertetése](../security/fundamentals/protection-customer-data.md)

**Az Azure Security Center monitorozása**: Nem alkalmazható

**Felelősség**: Ügyfél

### <a name="46-use-azure-rbac-to-manage-access-to-resources"></a>4,6: az erőforrásokhoz való hozzáférés kezelése az Azure RBAC

**Útmutató**: a szolgáltatás felügyeletéhez használja az Azure szerepköralapú hozzáférés-vezérlést (Azure RBAC) a kulcsok és a konfiguráció elérésének kezeléséhez. A tartalmi műveletekhez, például az indexeléshez és a lekérdezésekhez, Cognitive Search az identitás-alapú hozzáférés-vezérlési modell helyett kulcsokat használ. Az Azure RBAC segítségével szabályozhatja a kulcsokhoz való hozzáférést.
- [Az RBAC konfigurálása az Azure-ban](../role-based-access-control/role-assignments-portal.md) 

 
- [Szerepkörök használata rendszergazdai hozzáféréshez Cognitive Search](./search-security-rbac.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4,7: a gazdagép-alapú adatvesztés-megelőzés használata a hozzáférés-vezérlés kikényszeríthető

**Útmutató**: nem alkalmazható a Cognitive Searchra. Ez az útmutató számítási erőforrások számára készült. 

A Microsoft kezeli a Cognitive Search alapjául szolgáló infrastruktúrát, és szigorú ellenőrzéseket vezetett be az ügyféladatok elvesztésének vagy kihatásának megelőzésére.

- [Az ügyféladatok Azure-beli védelmének ismertetése](../security/fundamentals/protection-customer-data.md)

**Az Azure Security Center monitorozása**: Nem alkalmazható

**Felelősség**: Microsoft

### <a name="48-encrypt-sensitive-information-at-rest"></a>4,8: bizalmas adatok titkosítása a nyugalmi állapotban

**Útmutató**: a Cognitive Search automatikusan titkosítja az indexelt tartalmat a Microsoft által felügyelt kulcsokkal. Ha további védelemre van szükség, egy második titkosítási réteggel kiegészítheti az alapértelmezett titkosítást a Azure Key Vault-ben létrehozott és kezelt kulcsok használatával.

- [Ügyfél által felügyelt kulcsok konfigurálása az adattitkosításhoz az Azure-ban Cognitive Search](./search-security-manage-encryption-keys.md)

- [Inaktív adatok Azure-ban való titkosításának ismertetése](../security/fundamentals/encryption-atrest.md)

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Megosztott

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4,9: a kritikus Azure-erőforrások változásainak naplózása és riasztása

**Útmutató**: a Azure monitor és az Azure-tevékenység naplójának használata riasztások létrehozásához, amikor a módosítások a Cognitive Search és más kritikus vagy kapcsolódó erőforrások éles példányain lépnek életbe.

- [Riasztások létrehozása az Azure-tevékenységek naplózási eseményeihez](../azure-monitor/alerts/alerts-activity-log.md)

- [Riasztások létrehozása Cognitive Search tevékenységekhez](./search-monitor-logs.md)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

## <a name="vulnerability-management"></a>Biztonságirés-kezelés

*További információért lásd az [Azure biztonsági teljesítményteszt: biztonsági rés kezelése](../security/benchmarks/security-control-vulnerability-management.md)című témakört.*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5,1: automatikus biztonsági rések vizsgálatára szolgáló eszközök futtatása

**Útmutató**: a Cognitive Search jelenleg nem érhető el.  A keresési szolgáltatás tartalmát tároló fürtök esetében a Microsoft felelős a fürtök sebezhetőségének kezelésében.

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Microsoft

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5,2: az operációs rendszer automatikus javításának felügyeleti megoldásának telepítése

**Útmutató**: nem alkalmazható a Cognitive Searchra. Ez a javaslat számítási erőforrások számára készült.

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: nem alkalmazható

### <a name="53-deploy-an-automated-patch-management-solution-for-third-party-software-titles"></a>5,3: automatikus javítási megoldás üzembe helyezése harmadik féltől származó szoftverek címeihez

**Útmutató**: nem alkalmazható a Cognitive Searchra. Ez a javaslat számítási erőforrások számára készült.

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: nem alkalmazható

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5,4: a biztonsági rések keresésének összehasonlítása

**Útmutató**: nem alkalmazható a Cognitive Searchra. A Microsoft a biztonsági rések kezelését a Cognitive Search szolgáltatásokat támogató mögöttes rendszereken végzi.

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Microsoft

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5,5: kockázatértékelési folyamat használatával rangsorolhatja a felderített biztonsági rések szervizelését

**Útmutató**: nem alkalmazható a Cognitive Searchra. A biztonsági rések vizsgálatának eredményeihez nem tartozik standard minősítési vagy pontozási rendszer.

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: nem alkalmazható

## <a name="inventory-and-asset-management"></a>Leltár- és eszközfelügyelet

*További információt az [Azure biztonsági teljesítményteszt: leltár és eszközkezelés](../security/benchmarks/security-control-inventory-asset-management.md)című témakörben talál.*

### <a name="61-use-automated-asset-discovery-solution"></a>6,1: automatikus eszköz-felderítési megoldás használata

**Útmutató**: az Azure Resource Graph használatával lekérdezheti és felderítheti az előfizetésekben lévő összes erőforrást (például a számítási, tárolási, hálózati, portokat, protokollokat stb.).  

Győződjön meg arról, hogy a bérlőben a megfelelő (olvasási) engedélyek szerepelnek, valamint az összes Azure-előfizetés enumerálása, valamint az előfizetések erőforrásai.  

- [Lekérdezések létrehozása az Azure Resource Graph Explorerrel](../governance/resource-graph/first-query-portal.md) 

- [Azure-előfizetések megtekintése](/powershell/module/az.accounts/get-azsubscription) 

- [Az Azure RBAC ismertetése](../role-based-access-control/overview.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="62-maintain-asset-metadata"></a>6,2: az eszköz metaadatainak fenntartása

**Útmutató**: címkéket alkalmazhat az Azure-erőforrásokra metaadatok használatával, hogy logikailag szervezze őket a besorolásba.

- [Címkék létrehozása és használata](../azure-resource-manager/management/tag-resources.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="63-delete-unauthorized-azure-resources"></a>6,3: jogosulatlan Azure-erőforrások törlése

**Útmutató**: a címkézés, a felügyeleti csoportok és az elkülönített előfizetések használata, ha szükséges, az eszközök rendszerezéséhez és nyomon követéséhez. Rendszeres időközönként egyeztetheti a leltárt, és gondoskodhat arról, hogy a jogosulatlan erőforrások törlése az előfizetésből időben történjen.
- [További Azure-előfizetések létrehozása](../cost-management-billing/manage/create-subscription.md) 

- [Management Groups létrehozása](../governance/management-groups/create-management-group-portal.md) 

- [Címkék létrehozása és használata](../azure-resource-manager/management/tag-resources.md) 

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="64-define-and-maintain-an-inventory-of-approved-azure-resources"></a>6,4: jóváhagyott Azure-erőforrások leltárának meghatározása és karbantartása

**Útmutató**: az indexeléssel és a készségkészlet feldolgozással kapcsolatos jóváhagyott Azure-erőforrások listájának meghatározása Cognitive Searchban.

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="65-monitor-for-unapproved-azure-resources"></a>6,5: a nem jóváhagyott Azure-erőforrások figyelése

**Útmutató**: azt javasoljuk, hogy olyan Azure-erőforrások leltárát határozza meg, amelyeket korábban a szervezeti szabályzatok és szabványok alapján jóváhagyott, majd figyelje a nem jóváhagyott Azure-erőforrásokat Azure Policy vagy Azure Resource Graph használatával.

- [Az Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md) 

- [Lekérdezések létrehozása az Azure Graph használatával](../governance/resource-graph/first-query-portal.md) 

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6,6: a nem jóváhagyott szoftveralkalmazások figyelése a számítási erőforrásokon belül

**Útmutató**: nem alkalmazható a Cognitive Searchra. Ez az útmutató számítási erőforrások számára készült.

Azt javasoljuk, hogy leltárt készítsen az Azure-beli számítási erőforrásokra telepített, jóvá nem hagyott szoftverekről a szervezeti szabályzatok és a biztonsági szabványok alapján jóváhagyott szoftveres alkalmazásokról.

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: nem alkalmazható

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6,7: nem jóváhagyott Azure-erőforrások és szoftveralkalmazások eltávolítása

**Útmutató**: nem alkalmazható a Cognitive Searchra. Ez a javaslat számítási erőforrások számára készült.

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: nem alkalmazható

### <a name="68-use-only-approved-applications"></a>6,8: csak jóváhagyott alkalmazások használata

**Útmutató**: nem alkalmazható a Cognitive Searchra. Semmilyen számítási erőforrást nem tesz elérhetővé, vagy lehetővé teszi szoftveralkalmazások telepítését bármely erőforráson.

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: nem alkalmazható

### <a name="69-use-only-approved-azure-services"></a>6,9: csak jóváhagyott Azure-szolgáltatások használata

**Útmutató**: a Azure Policy használatával korlátozásokat helyezhet el az ügyfél-előfizetésekben létrehozható erőforrások típusától a következő beépített szabályzat-definíciók használatával:

- Nem engedélyezett erőforrástípusok
- Engedélyezett erőforrástípusok

Az Azure Resource Graph használatával kérdezheti le vagy derítheti fel az előfizetése (ke) n belüli erőforrásait. Győződjön meg arról, hogy a környezetben lévő összes Azure-erőforrás jóvá van hagyva.

- [Az Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md) 

- [Adott erőforrástípus megtagadása a következővel Azure Policy](../governance/policy/samples/index.md) 

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6,10: a jóváhagyott szoftverek leltárának fenntartása

**Útmutató**: nem alkalmazható a Cognitive Searchra. Ez a javaslat a számítási erőforrásokon futó alkalmazások számára készült.

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: nem alkalmazható

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6,11: korlátozza a felhasználók képességét a Azure Resource Manager való interakcióra

**Útmutató**: a szolgáltatások kezeléséhez az Azure feltételes hozzáférés használatával korlátozhatja a felhasználók interakcióját a Azure Resource Manager az "Microsoft Azure felügyelet" alkalmazás "hozzáférés tiltása" beállításával. 

Az összes többi műveletre vonatkozó kérések hitelesítéséhez használt kulcsokhoz való hozzáférés szabályozása, különösen a Cognitive Searchsal kapcsolatos tartalomhoz kapcsolódóan.

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="612-limit-users-ability-to-execute-scripts-in-compute-resources"></a>6,12: korlátozza a felhasználók számára a parancsfájlok végrehajtásának lehetőségét a számítási erőforrásokban

**Útmutató**: nem alkalmazható a Cognitive Searchra. Ez a javaslat számítási erőforrások számára készült.

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: nem alkalmazható

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6,13: fizikailag vagy logikailag elkülöníthető a nagy kockázatú alkalmazások

**Útmutató**: nem alkalmazható a Cognitive Searchra. Ez a javaslat a Azure App Service vagy számítási erőforrásokon futó webalkalmazásokhoz készült.

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: nem alkalmazható

## <a name="secure-configuration"></a>Biztonságos konfiguráció

*További információt az [Azure biztonsági teljesítményteszt: biztonságos konfiguráció](../security/benchmarks/security-control-secure-configuration.md)című témakörben talál.*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7,1: biztonságos konfigurációk létrehozása az összes Azure-erőforráshoz

**Útmutató**: Azure Policy aliasok használata a "Microsoft. Search" névtérben egyéni szabályzatok létrehozásához az Azure Cognitive Search-erőforrások konfigurációjának naplózásához vagy érvényesítéséhez. Használhat beépített Azure Policy-definíciókat is Cognitive Search-szolgáltatásokhoz, például:

- Naplózás engedélyezése az Azure-erőforrásokhoz

A Azure Resource Manager lehetővé teszi a sablon exportálását JavaScript Object Notation (JSON), amelyet át kell tekinteni annak érdekében, hogy a konfigurációk megfeleljenek a szervezete biztonsági követelményeinek. 

A Azure Security Center javaslatai az Azure-erőforrások biztonságos alapkonfigurációja is használhatók. 

- [Azure Policy az Azure-ra vonatkozó szabályozási megfelelőségi szabályozások Cognitive Search](./security-controls-policy.md)

- [Az elérhető Azure Policy aliasok megtekintése](/powershell/module/az.resources/get-azpolicyalias)

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="72-establish-secure-operating-system-configurations"></a>7,2: biztonságos operációsrendszer-konfigurációk létrehozása

**Útmutató**: nem alkalmazható a Cognitive Searchra. Ez a javaslat számítási erőforrások számára készült.

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: nem alkalmazható

### <a name="73-maintain-secure-azure-resource-configurations"></a>7,3: biztonságos Azure-erőforrás-konfigurációk karbantartása

**Útmutató**: a Azure Policy [megtagadás] és [üzembe helyezés, ha nem léteznek] effektusok használata a Cognitive Search szolgáltatási erőforrásainak biztonságos beállításainak érvényesítéséhez. 

Azure Resource Manager sablonokat a szervezete számára szükséges Azure-erőforrások biztonsági beállításainak fenntartására használhatja. 

- [Azure Policy effektusok ismertetése](../governance/policy/concepts/effects.md)

- [Azure Policy az Azure-ra vonatkozó szabályozási megfelelőségi szabályozások Cognitive Search](./security-controls-policy.md)

- [Szabályzatok létrehozása és kezelése a megfelelőség kikényszerítése céljából](../governance/policy/tutorials/create-and-manage.md)

- [Azure Resource Manager sablonok áttekintése](../azure-resource-manager/templates/overview.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="74-maintain-secure-operating-system-configurations"></a>7,4: az operációs rendszer biztonságos konfigurációjának fenntartása

**Útmutató**: nem alkalmazható a Cognitive Searchra. Ez a javaslat számítási erőforrások számára készült.

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: nem alkalmazható

### <a name="75-securely-store-configuration-of-azure-resources"></a>7,5: az Azure-erőforrások biztonságos tárolása

**Útmutató**: ha egyéni Azure Policy-definíciókat használ, az Azure DevOps vagy az Azure Repos segítségével biztonságosan tárolhatja és kezelheti a kódot.

- [Kód tárolása az Azure DevOps](/azure/devops/repos/git/gitworkflow)

- [Az Azure Repos dokumentációja](/azure/devops/repos/index)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="76-securely-store-custom-operating-system-images"></a>7,6: az egyéni operációsrendszer-lemezképek biztonságos tárolása

**Útmutató**: nem alkalmazható a Cognitive Searchra. Ez a javaslat számítási erőforrások számára készült.

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: nem alkalmazható

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7,7: az Azure-erőforrások konfigurációs felügyeleti eszközeinek üzembe helyezése

**Útmutató**: a Cognitive Search Service-erőforrások szabványos biztonsági konfigurációinak meghatározása és implementálása Azure Policy használatával. 

Az aliasok használatával egyéni házirendeket hozhat létre a hálózati konfigurációk naplózásához vagy érvényesítéséhez. Használhatja az adott erőforrásokhoz kapcsolódó beépített szabályzat-definíciókat is. 

Emellett a Azure Automation használatával is telepítheti a konfigurációs módosításokat, és kezelheti a házirendek kivételeit. 

- [Azure Policy az Azure-ra vonatkozó szabályozási megfelelőségi szabályozások Cognitive Search](./security-controls-policy.md)

- [Az Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="78-deploy-configuration-management-tools-for-operating-systems"></a>7,8: konfigurációs felügyeleti eszközök telepítése operációs rendszerekhez

**Útmutató**: nem alkalmazható a Cognitive Searchra. Ez a javaslat számítási erőforrások számára készült.

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: nem alkalmazható

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7,9: automatikus konfigurációs monitorozás megvalósítása Azure-erőforrásokhoz

**Útmutató**: a Security Center használatával végezheti el az Cognitive Search szolgáltatás erőforrásainak alapkonfigurációját.  Emellett a Azure Policy használatával figyelmeztesse és naplózhatja az erőforrás-konfigurációkat. 

- [Javaslatok szervizelése Azure Security Center](../security-center/security-center-remediate-recommendations.md)

- [Azure Policy az Azure-ra vonatkozó szabályozási megfelelőségi szabályozások Cognitive Search](./security-controls-policy.md)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7,10: az operációs rendszerek automatikus konfiguráció-figyelésének megvalósítása

**Útmutató**: nem alkalmazható a Cognitive Searchra. Ez a javaslat számítási erőforrások számára készült.

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: nem alkalmazható

### <a name="711-manage-azure-secrets-securely"></a>7,11: az Azure-titkok biztonságos kezelése

**Útmutató**: az Azure felügyelt identitások használata a Azure Key Vaultekkel együtt a felhőalapú alkalmazások titkos felügyeletének egyszerűbbé tételéhez.
- [Felügyelt identitások használata Azure-erőforrásokhoz](../azure-app-configuration/howto-integrate-azure-managed-service-identity.md) 
- [Key Vault létrehozása](../key-vault/general/quick-create-portal.md) 

- [Key Vault hitelesítés biztosítása felügyelt identitással](../key-vault/general/assign-access-policy-portal.md) 

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="712-manage-identities-securely-and-automatically"></a>7,12: az identitások biztonságos és automatikus kezelése

**Útmutató**: az Azure által felügyelt identitás használatával Cognitive Search hozzáférést biztosíthat más Azure-szolgáltatásokhoz, például a Key Vault és az indexelő adatforrásokhoz egy automatikusan felügyelt identitás használatával Azure Active Directory (Azure ad). A felügyelt identitások lehetővé teszik bármely olyan szolgáltatás hitelesítését, amely támogatja az Azure AD-hitelesítést, beleértve a Azure Key Vault is, a kódban szereplő hitelesítő adatok nélkül. 

- [Indexelő-kapcsolatok beállítása egy adatforráshoz felügyelt identitás használatával](./search-howto-managed-identities-data-sources.md)

- [Az ügyfél által felügyelt kulcsok konfigurálása adattitkosításhoz felügyelt identitás használatával](./search-security-manage-encryption-keys.md)

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="713-eliminate-unintended-credential-exposure"></a>7,13: a hitelesítő adatok nem szándékolt expozíciójának megszüntetése

**Útmutató**: nem alkalmazható a Cognitive Searchra. Nem futtatja a kódot, és nem rendelkezik az azonosításhoz szükséges hitelesítő adatokkal.

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: nem alkalmazható

## <a name="malware-defense"></a>Kártevők elleni védelem

*További információt az [Azure biztonsági teljesítményteszt: kártevők elleni védelem](../security/benchmarks/security-control-malware-defense.md)című témakörben talál.*

### <a name="81-use-centrally-managed-antimalware-software"></a>8,1: központilag felügyelt kártevő szoftver használata

**Útmutató**: nem alkalmazható a Cognitive Searchra. Ez a javaslat számítási erőforrások számára készült.

A Microsoft kártevő szoftveres verziója engedélyezve van az Azure-szolgáltatásokat támogató mögöttes gazdagépen (például Azure Cognitive Search), azonban nem az ügyfél tartalmán fut.

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: nem alkalmazható

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8,2: a nem számítási Azure-erőforrásokra feltöltött fájlok előzetes vizsgálata

**Útmutató**: a nem számítási Azure-erőforrásokra feltöltött tartalmak előzetes vizsgálata, például Cognitive Search, Blob Storage, Azure SQL Database stb. 

Az Ön felelőssége, hogy előzetesen beszkennelje a nem számítási Azure-erőforrásokra feltöltött tartalmakat. A Microsoft nem fér hozzá az ügyféladatok eléréséhez, így az Ön nevében nem végezhet kártevő-ellenőrzéseket az ügyfél-tartalmakon.

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="83-ensure-antimalware-software-and-signatures-are-updated"></a>8,3: a kártevő szoftverek és az aláírások frissítésének ellenőrzése

**Útmutató**: nem alkalmazható a Cognitive Searchra. Nem teszi lehetővé, hogy a kártevők elleni megoldások telepítve legyenek az erőforrásokra. A Microsoft mögöttes platform kezeli a kártevő szoftverek és az aláírások frissítését. 

A szervezete tulajdonában lévő és a keresési megoldásban használt számítási erőforrások esetében kövesse az Security Center, a számítási alkalmazások című témakörben található javaslatokat, amelyekkel &amp; biztosítható, hogy az összes végpont naprakész legyen a legújabb aláírásokkal. Linux esetén használjon egy harmadik féltől származó kártevő-elhárító megoldást.

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Megosztott

## <a name="data-recovery"></a>Adat-helyreállítás

*További információt az [Azure biztonsági teljesítményteszt: adat-helyreállítás](../security/benchmarks/security-control-data-recovery.md)című témakörben talál.*

### <a name="91-ensure-regular-automated-back-ups"></a>9,1: rendszeres automatizált biztonsági másolatok biztosítása

**Útmutató**: a keresési szolgáltatásokban tárolt tartalmakról nem készíthető biztonsági másolat Azure Backup vagy bármely más beépített mechanizmus használatával, de az alkalmazás forráskódja és az elsődleges adatforrások indexe is létrehozható, vagy létrehozhat egy egyéni eszközt az indexelt tartalom lekéréséhez és tárolásához.

- [GitHub-index – biztonsági másolat visszaállítása minta](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/index-backup-restore)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: teljes rendszerbiztonsági mentés és minden ügyfél által felügyelt kulcs biztonsági mentése

**Útmutató**: a Cognitive Search jelenleg nem támogatja az automatikus biztonsági mentést egy keresési szolgáltatásban lévő adatbiztonsági mentéshez, és manuális folyamattal kell biztonsági másolatot készíteni.  Az ügyfél által felügyelt kulcsokat a Azure Key Vault is biztonsági másolatot készíthet. 

- [Azure Cognitive Search index biztonsági mentése és visszaállítása](/samples/azure-samples/azure-search-dotnet-samples/azure-search-backup-restore-index/)

- [Key Vault kulcsok biztonsági mentése az Azure-ban](/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: az összes biztonsági másolat ellenőrzése, beleértve az ügyfél által felügyelt kulcsokat

**Útmutató**: a Cognitive Search jelenleg nem támogatja az automatikus biztonsági mentést egy keresési szolgáltatásban található adatbiztonsági mentéshez, és manuális folyamattal kell biztonsági másolatot készíteni és visszaállítani.  A biztonsági mentési folyamat végpontok közötti integritásának biztosítása érdekében rendszeresen végezze el a manuálisan biztonsági mentéssel ellátni kívánt tartalom adatvisszaállítását.

- [Azure Cognitive Search index biztonsági mentése és visszaállítása](/samples/azure-samples/azure-search-dotnet-samples/azure-search-backup-restore-index/)

- [Key Vault kulcsok visszaállítása az Azure-ban](/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: a biztonsági másolatok és az ügyfél által felügyelt kulcsok védelmének biztosítása

**Útmutató**: a Cognitive Search jelenleg nem támogatja az automatikus biztonsági mentést egy keresési szolgáltatásban lévő adatbiztonsági mentéshez, és manuális folyamattal kell biztonsági másolatot készíteni.  Az ügyfél által felügyelt kulcsokat a Azure Key Vault is biztonsági másolatot készíthet. 

A kulcsok véletlen vagy rosszindulatú törléssel szembeni védelme érdekében engedélyezze a Key Vault a védelem lágy törlését és kiürítését. Ha az Azure Storage-t a manuális biztonsági másolatok tárolására használja, engedélyezze a Soft delete-et az adatok mentéséhez és helyreállításához, amikor blobokat vagy blob-pillanatképeket törölnek. 

- [Azure Cognitive Search index biztonsági mentése és visszaállítása](/samples/azure-samples/azure-search-dotnet-samples/azure-search-backup-restore-index/)

- [Helyreállítható törlés és a végleges törléssel szembeni védelem engedélyezése kulcstartóban](../storage/blobs/soft-delete-blob-overview.md?tabs=azure-portal)

- [Soft DELETE az Azure Blob Storage-hoz](../storage/blobs/soft-delete-blob-overview.md?tabs=azure-portal)

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

## <a name="incident-response"></a>Incidensmegoldás

*További információt az [Azure biztonsági teljesítményteszt: incidens válasza](../security/benchmarks/security-control-incident-response.md)című témakörben talál.*

### <a name="101-create-an-incident-response-guide"></a>10,1: incidens-válaszi útmutató létrehozása

**Útmutató**: incidensek kifejlesztése útmutató a szervezet számára. Győződjön meg arról, hogy van olyan írásos incidens-válasz, amely meghatározza a személyzet összes szerepkörét, valamint az incidensek kezelésének és kezelésének fázisait az incidensek vizsgálatát követően.

- [Útmutató a saját biztonsági incidensek megoldási folyamatának létrehozásához](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Microsoft Security Response Center – incidens anatómiája](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Az ügyfél a NIST számítógépes biztonsági incidensek kezelési útmutatóját is kihasználhatja a saját incidens-válasz tervének létrehozásával kapcsolatos támogatáshoz](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10,2: incidensek pontozásának és rangsorolási eljárásának létrehozása

**Útmutató**: a Security Center súlyosságot rendel az egyes riasztásokhoz, hogy a prioritások alapján ki lehessen deríteni, hogy mely riasztásokat kell először megvizsgálni. A súlyosság azon alapul, hogy a megbízhatósági Security Center hol van a megállapításban, illetve a riasztás kibocsátására használt analitikai módszer, valamint a riasztást eredményező tevékenység mögött rosszindulatú szándékkal rendelkező megbízhatósági szint.

Emellett megadhatja a címkéket használó előfizetéseket, és létrehozhat egy elnevezési rendszert az Azure-erőforrások azonosításához és kategorizálásához, különösen a bizalmas adatok feldolgozásához. Az Ön felelőssége, hogy rangsorolja a riasztások szervizelését az Azure-erőforrások és-környezet kritikus jellemzői alapján, ahol az incidens történt.

- [Címkék használata az Azure-erőforrások rendszerezéséhez](../azure-resource-manager/management/tag-resources.md)

- [Biztonsági riasztások az Azure Security Centerben](../security-center/security-center-alerts-overview.md)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="103-test-security-response-procedures"></a>10,3: biztonsági reagálási eljárások tesztelése

**Útmutató**: a rendszerek incidensek reagálási képességeinek rendszeres tesztelésére szolgáló gyakorlatok végrehajtása. Azonosítsa a gyenge pontokat és réseket, és szükség esetén dolgozza át a tervet.

- [Tekintse meg a NIST kiadványát, a "útmutató a teszteléshez, a képzéshez és az IT-csomagokhoz és-képességekhez.](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10,4: biztonsági incidensek elérhetőségének biztosítása és riasztási értesítések konfigurálása biztonsági incidensekhez

**Útmutató**: a Microsoft a biztonsági incidensek elérhetőségi adatait arra használja fel, hogy felvegye Önnel a kapcsolatot, ha a Microsoft Security Response Center (MSRC) felfedi, hogy az adatokat egy törvénytelen vagy jogosulatlan fél is hozzáférte. A problémák megoldása érdekében tekintse át az incidenseket a tény után.

- [A Azure Security Center biztonsági kapcsolattartó beállítása](../security-center/security-center-provide-security-contact-details.md)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10,5: biztonsági riasztások beépítése az incidensek gyorsreagáló rendszerébe

**Útmutató**: az Security Center-riasztások és-javaslatok exportálása a folyamatos exportálás funkció használatával. A folyamatos exportálás lehetővé teszi a riasztások vagy javaslatok manuális vagy folyamatos exportálását. Az Security Center adatösszekötővel továbbíthatja a riasztásokat az Azure Sentinel szolgáltatásba.

- [Folyamatos exportálás konfigurálása](../security-center/continuous-export.md)

- [Riasztások streamelése az Azure Sentinelbe](../sentinel/connect-azure-security-center.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="106-automate-the-response-to-security-alerts"></a>10,6: a biztonsági riasztásokra adott válasz automatizálása

**Útmutató**: a Azure Security Center munkafolyamat-automatizálási funkciója segítségével automatikusan aktiválhatja a válaszokat a "Logic apps" használatával a biztonsági riasztások és javaslatok esetében.

- [A Munkafolyamat-automatizálás és a Logic Apps konfigurálása](../security-center/workflow-automation.md)

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

## <a name="penetration-tests-and-red-team-exercises"></a>Behatolási tesztek és Red Team-gyakorlatok

*További információkért tekintse meg az [Azure biztonsági teljesítményteszt: behatolási tesztek és a Red Team gyakorlatok](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md)című témakört.*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11,1: az Azure-erőforrások rendszeres behatolásának tesztelése, valamint az összes kritikus biztonsági vizsgálat szervizelésének biztosítása

**Útmutató**: kövesse a Microsoft Cloud penetráció tesztelési szabályait, amelyekkel biztosíthatja, hogy a behatolási tesztek ne sértsék a Microsoft-házirendeket. A Microsoft által felügyelt felhőalapú infrastruktúrán, szolgáltatásokon és alkalmazásokon végzett riasztási és élő behatolási tesztek végrehajtásához használja a Microsoft stratégiáját és végrehajtási tervét.
- [Behatolástesztelési beavatkozási szabályok](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)
- [Riasztási tesztek a Microsoft-felhőben](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Megosztott

## <a name="next-steps"></a>Következő lépések

- Lásd az [Azure biztonsági teljesítménytesztét](../security/benchmarks/overview.md)
- További tudnivalók az [Azure biztonsági alapterveiről](../security/benchmarks/security-baselines-overview.md)