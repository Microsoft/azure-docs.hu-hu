---
title: Azure Firewall Azure biztonsági alapterve
description: A Azure Firewall biztonsági alapterve az Azure biztonsági Teljesítménytesztben meghatározott biztonsági javaslatok megvalósítására szolgáló eljárási útmutatást és erőforrásokat biztosít.
author: msmbaldwin
ms.service: firewall
ms.topic: conceptual
ms.date: 08/05/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 24c9e5137e59fbd14a489b4d41709f39aa448ed8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "89231350"
---
# <a name="azure-security-baseline-for-azure-firewall"></a>Azure Firewall Azure biztonsági alapterve

Ez a biztonsági alapkonfiguráció az [Azure biztonsági teljesítménytesztre](../security/benchmarks/overview.md) vonatkozó útmutatást alkalmazza Azure Firewallra. Az Azure biztonsági teljesítményteszt az Azure-beli felhőalapú megoldások biztonságossá tételével kapcsolatos ajánlásokat tartalmaz. A tartalom csoportosítva van az Azure biztonsági teljesítményteszt által meghatározott **biztonsági vezérlőket** és a Azure Firewallre vonatkozó kapcsolódó útmutatást. A Azure Firewall nem alkalmazható **vezérlők** ki vannak zárva. Ha szeretné megtekinteni, hogyan Azure Firewall teljes mértékben leképezni az Azure biztonsági Teljesítménytesztét, tekintse meg a [teljes Azure Firewall biztonsági alapterv-leképezési fájlt](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="network-security"></a>Hálózati biztonság

*További információt az [Azure biztonsági teljesítményteszt: hálózati biztonság](/azure/security/benchmarks/security-control-network-security)című témakörben talál.*

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-network-interfaces"></a>1,2: a virtuális hálózatok, alhálózatok és hálózati adapterek konfigurációjának és forgalmának figyelése és naplózása

**Útmutató**: a Azure Firewall integrálva van Azure monitor a tűzfal által feldolgozott forgalom naplózásához.

Emellett a Azure Security Center és a hálózati védelmi javaslatok segítségével biztonságossá teheti a Azure Firewallhoz kapcsolódó hálózati erőforrásokat.

- [A Azure Security Center által biztosított hálózati biztonság ismertetése](../security-center/security-center-network-recommendations.md)

**Azure Security Center figyelés**: igen

**Felelősség**: ügyfél

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1,4: az ismert kártevő IP-címekkel folytatott kommunikáció megtagadása

**Útmutató**: a veszélyforrások felderítését lehetővé tevő szűréssel riasztást kaphat, és megtagadhatja a forgalmat az ismert kártékony IP-címekre és tartományokra. A fenyegetésekkel kapcsolatos intelligencián alapuló szűrés engedélyezhető a tűzfal számára, hogy riasztást kapjon, és megtagadja a forgalmat a/ról ismert kártékony IP-címekre és tartományokra.

- [Azure Firewall fenyegetés intelligencia-alapú szűrés](threat-intel.md)

- [Az Azure Security Center integrált veszélyforrások felderítésének megismerése](/azure/security-center/security-center-alerts-service-layer)

**Azure Security Center figyelés**: igen

**Felelősség**: ügyfél

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1,8: a hálózati biztonsági szabályok bonyolultságának és adminisztratív terhelésének csökkentése

**Útmutató**: a Azure Firewall egy szolgáltatási címke az IP-cím előtagjainak egy csoportját jelöli, amely a biztonsági szabályok létrehozásának összetettségét segíti elő.

Azure Firewall szolgáltatás címkéi a hálózati szabályok célhely mezőjében használhatók, és a hálózati hozzáférés-vezérlést is meghatározhatják Azure Firewallon. Biztonsági szabályok létrehozása során szolgáltatáscímkéket használhat bizonyos IP-címek helyett.

Emellett az ügyfél által definiált címkék, például az IP-csoportok is támogatottak, és használhatók egy hálózati szabályban vagy egy alkalmazás-szabályban is. Az alkalmazási szabályokban az FQDN-címkék támogatottak a tűzfalon keresztül a szükséges kimenő hálózati forgalom engedélyezéséhez.

Vegye figyelembe, hogy nem hozhat létre saját szolgáltatási címkét, és nem adhatja meg, hogy mely IP-címek szerepeljenek a címkén belül. A szolgáltatáscímkékben lévő címelőtagokat a Microsoft kezeli, és a címek változásával automatikusan frissíti a szolgáltatáscímkéket.

 

- [Azure Firewall szolgáltatás címkéi](service-tags.md)

- [Elérhető szolgáltatás címkéi](../virtual-network/service-tags-overview.md#available-service-tags)

- [IP-csoportok a Azure Firewall](ip-groups.md)

- [FQDN-címkék – áttekintés](fqdn-tags.md)

**Azure Security Center figyelés**: jelenleg nem érhető el

**Felelősség**: ügyfél

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1,9: a hálózati eszközök szabványos biztonsági konfigurációinak fenntartása

**Útmutató**: az Azure-szabályzatok még nem teljes mértékben támogatottak Azure Firewall számára. A Azure Firewall Manager használatával a biztonsági konfigurációk szabványosítása is elérhető.

Az Azure-tervrajzok segítségével a nagy léptékű Azure-környezeteket is leegyszerűsítheti a főbb környezeti összetevők (például az Azure Resources Manager-sablonok, az Azure RBAC-vezérlők és a házirendek) egyetlen tervezet-definícióban való csomagolásával. A tervrajzot új előfizetésekre alkalmazhatja, és az irányítás és felügyelet finomhangolását a verziószámozás segítségével végezheti el.

- [Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md)

- [Azure Policy minták a hálózatkezeléshez](/azure/governance/policy/samples/#network)

- [Azure Blueprint létrehozása](../governance/blueprints/create-blueprint-portal.md)

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: ügyfél

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1,11: automatikus eszközök használata a hálózati erőforrások konfigurációjának figyelésére és a változások észlelésére

**Útmutató**: az Azure-tevékenység naplójának használata az erőforrás-konfigurációk figyelésére és a Azure Firewall erőforrások változásainak észlelésére. Hozzon létre riasztásokat a Azure Monitoron belül, amelyek akkor lépnek életbe, amikor a kritikus erőforrások változásai megváltoznak.

- [Az Azure Firewall naplóinak és metrikáinak monitorozása](/azure/firewall/tutorial-diagnostics)

- [Azure-Tevékenységnaplók eseményeinek megtekintése és lekérése](/azure/azure-monitor/platform/activity-log-view)

- [Riasztások létrehozása a Azure Monitorban](../azure-monitor/platform/alerts-activity-log.md) 

**Azure Security Center figyelés**: igen

**Felelősség**: ügyfél

## <a name="logging-and-monitoring"></a>Naplózás és figyelés

*További információt az [Azure biztonsági teljesítményteszt: naplózás és figyelés](/azure/security/benchmarks/security-control-logging-monitoring)című témakörben talál.*

### <a name="21-use-approved-time-synchronization-sources"></a>2,1: a jóváhagyott idő-szinkronizálási források használata

**Útmutató**: a Microsoft a Azure Firewall Azure-erőforrásainak időforrásait fenntartja. Az ügyfeleknek létre kell hozniuk egy hálózati szabályt a hozzáférés engedélyezéséhez, illetve a környezetében használt időkiszolgálóhoz.

- [NTP-kiszolgáló elérése](protect-windows-virtual-desktop.md#additional-considerations)

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: megosztott

### <a name="22-configure-central-security-log-management"></a>2,2: a központi biztonsági naplók felügyeletének konfigurálása

**Útmutató**: a különböző naplók központi biztonsági naplózási kezeléséhez engedélyezheti és elvégezheti az Azure Sentinel vagy egy harmadik féltől származó Siem-hez való bejelentkezést.

A Tevékenységnaplók használhatók a Azure Firewallon végzett műveletek naplózására és az erőforrásokon végzett műveletek figyelésére. A tevékenység naplója az erőforrások összes írási műveletét (PUT, POST, DELETE) tartalmazza az olvasási műveletek (GET) kivételével. A Tevékenységnaplók segítségével hibát kereshet a hibaelhárítás során, vagy megfigyelheti, hogy a szervezet felhasználója hogyan módosított egy erőforrást.

A Azure Firewall a következő diagnosztikai naplókat is biztosítja az ügyfelekkel és a hálózati szabályokkal kapcsolatos információk biztosításához.

Alkalmazási szabály naplója: minden olyan új kapcsolódás, amely megfelel az egyik konfigurált alkalmazási szabálynak, az elfogadott/elutasított kapcsolatok naplóját eredményezi.

Hálózati szabály naplója: minden olyan új kapcsolódás, amely megfelel az egyik konfigurált hálózati szabálynak, egy naplót eredményez az elfogadott/elutasított kapcsolódáshoz.

Megjegyzés: mindkét napló menthető egy Storage-fiókba, adatfolyamként továbbítva az esemény-központba, és/vagy csak akkor Azure Monitor naplókba, ha a környezet minden Azure Firewall számára engedélyezve van.

- [Naplók Azure Firewall](logs-and-metrics.md)

A tevékenységi naplókban található erőforrás-műveletek listája: Azure Resource Manager erőforrás-szolgáltatói műveletek

- [Platform-naplók és-metrikák összegyűjtése Azure Monitor ](../azure-monitor/platform/diagnostic-settings.md)

- [Az Azure Sentinel előkészítése](../sentinel/quickstart-onboard.md)

- [Ismerkedés a Azure Monitor és a harmadik féltől származó SIEM-integrációval](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools)

**Azure Security Center figyelés**: jelenleg nem érhető el

**Felelősség**: ügyfél

### <a name="23-enable-audit-logging-for-azure-resources"></a>2,3: az Azure-erőforrások naplózásának engedélyezése

**Útmutató**: a tevékenység-naplók segítségével naplózhatja a műveleteket Azure Firewall és figyelheti a műveleteket az erőforrásokon. A tevékenység naplója az Azure-erőforrások összes írási műveletét (PUT, POST, DELETE) tartalmazza az olvasási műveletek (GET) kivételével. A Azure Firewall a következő diagnosztikai naplókat is biztosítja az ügyfelekkel és a hálózati szabályokkal kapcsolatos információk biztosításához. 

Alkalmazási szabály naplója: minden olyan új kapcsolódás, amely megfelel az egyik konfigurált alkalmazási szabálynak, az elfogadott/elutasított kapcsolatok naplóját eredményezi.

Hálózati szabály naplója: minden olyan új kapcsolódás, amely megfelel az egyik konfigurált hálózati szabálynak, egy naplót eredményez az elfogadott/elutasított kapcsolódáshoz.

Vegye figyelembe, hogy mindkét napló menthető egy Storage-fiókba, adatfolyamként továbbítva az esemény-központba, és/vagy elküldésre kerül Azure Monitor naplókba, de csak akkor, ha a környezet minden Azure Firewall számára engedélyezve van.

- [Naplók Azure Firewall](logs-and-metrics.md)

- [Az erőforrás-műveletek listája a tevékenység naplófájljaiban](../role-based-access-control/resource-provider-operations.md)

**Azure Security Center figyelés**: jelenleg nem érhető el

**Felelősség**: ügyfél

### <a name="25-configure-security-log-storage-retention"></a>2,5: a biztonsági napló tárolási adatmegőrzésének konfigurálása

**Útmutató**: log Analytics munkaterület megőrzési időszaka a szervezet megfelelőségi előírásai alapján állítható be Azure monitoron belül. Az adatmegőrzés a választott díjszabási csomagtól függően 30 – 730 nap (2 év) alapján konfigurálható az összes munkaterületre.

A naplózási tár megőrzésének tárolására 3 lehetőség áll rendelkezésre:

A Storage-fiókokat a rendszer a legjobb naplókhoz használja, ha a naplók hosszabb időtartamra vannak tárolva, és szükség esetén felül vannak értékelve.

Az Event hubok nagyszerű lehetőséget biztosít a más biztonsági információkkal és eseménykezelő (SEIM) eszközökkel való integrációra, hogy riasztásokat kapjon az erőforrásaihoz.

Azure Monitor naplók használata ajánlott az alkalmazás általános valós idejű figyeléséhez vagy trendek kereséséhez.

- [Az Azure Firewall naplói és metrikái](logs-and-metrics.md)

- [Az adatmegőrzési időszak módosítása Log Analytics](../azure-monitor/platform/manage-cost-storage.md)

- [Adatmegőrzési szabályzat konfigurálása az Azure Storage-fiók naplóihoz](../storage/common/storage-monitor-storage-account.md#configure-logging)

**Azure Security Center figyelés**: jelenleg nem érhető el

**Felelősség**: ügyfél

### <a name="26-monitor-and-review-logs"></a>2,6: naplók figyelése és áttekintése

**Útmutató**: a Azure Firewall integrálva van Azure monitor a tűzfal naplófájljainak megtekintéséhez és elemzéséhez. Naplók küldhetők Log Analyticsba, Azure Storage-ba vagy Event Hubsba. Ezek Log Analytics vagy különböző eszközök, például az Excel és a Power BI segítségével elemezhetők. Van néhány különböző típusú Azure Firewall-napló.

A Tevékenységnaplók használhatók a Azure Firewallon végzett műveletek naplózására és az erőforrásokon végzett műveletek figyelésére. A tevékenység naplója az összes írási műveletet (PUT, POST, DELETE) tartalmazza az erőforrásokhoz, kivéve az olvasási műveleteket (GET). A Tevékenységnaplók segítségével hibát kereshet a hibaelhárítás során, vagy megfigyelheti, hogy a szervezet felhasználója hogyan módosított egy erőforrást.

A Azure Firewall diagnosztikai naplókat is biztosít az ügyfél-alkalmazásokkal és a hálózati szabályokkal kapcsolatos információk biztosításához.

Az alkalmazási szabályok naplói akkor jönnek létre, amikor az egyik konfigurált alkalmazási szabálynak megfelelő új kapcsolatok naplót eredményeznek az elfogadott/elutasított kapcsolatok számára. 

A hálózati szabályok naplói akkor jönnek létre, amikor az egyik konfigurált hálózati szabálynak megfelelő új kapcsolatok naplót eredményeznek az elfogadott/elutasított kapcsolatok számára.

Vegye figyelembe, hogy mindkét napló menthető egy Storage-fiókba, adatfolyamként továbbítva az esemény-hubokba és/vagy Azure Monitor-naplókba, de csak akkor, ha a környezet minden Azure Firewall számára engedélyezve van.

Azure Monitor naplók használhatók az alkalmazás általános valós idejű figyeléséhez vagy trendek kereséséhez.

- [Az Azure Firewall naplói és metrikái](logs-and-metrics.md)

- [Diagnosztikai naplók](logs-and-metrics.md#diagnostic-logs)

**Azure Security Center figyelés**: jelenleg nem érhető el

**Felelősség**: ügyfél

### <a name="27-enable-alerts-for-anomalous-activities"></a>2,7: riasztások engedélyezése rendellenes tevékenységekhez

**Útmutató**: Azure Security Center használata log Analytics munkaterülettel a biztonsági naplókban és eseményekben észlelt rendellenes tevékenységekkel kapcsolatos figyeléshez és riasztáshoz. 

Alternatív megoldásként engedélyezheti és elvégezheti a fedélzeti adatfeldolgozást az Azure Sentinel szolgáltatásban. 

- [Az Azure Sentinel előkészítése](../sentinel/quickstart-onboard.md)

- [Riasztások kezelése Azure Security Centerban](../security-center/security-center-managing-and-responding-alerts.md)

- [Riasztás a log Analytics-naplófájlok adatkezeléséről](../azure-monitor/learn/tutorial-response.md)

**Azure Security Center figyelés**: igen

**Felelősség**: ügyfél

## <a name="identity-and-access-control"></a>Identitás- és hozzáférés-vezérlés

*További információt az [Azure biztonsági teljesítményteszt: identitás-és hozzáférés-vezérlés](/azure/security/benchmarks/security-control-identity-access-control)című témakörben talál.*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3,1: a felügyeleti fiókok leltárának karbantartása

**Útmutató**: az Azure ad olyan beépített szerepkörökkel rendelkezik, amelyeket explicit módon kell hozzárendelni, és lekérdezhető. Az Azure AD PowerShell-modullal ad hoc lekérdezéseket hajthat végre a felügyeleti csoportok tagjait futtató fiókok felderítéséhez.

- [Címtárbeli szerepkör beszerzése az Azure AD-ben a PowerShell-lel](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0)

- [Címtárbeli szerepkör tagjainak beszerzése az Azure AD-ben a PowerShell-lel](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0)

**Azure Security Center figyelés**: jelenleg nem érhető el

**Felelősség**: ügyfél

### <a name="33-use-dedicated-administrative-accounts"></a>3,3: dedikált rendszergazdai fiókok használata

**Útmutató**: szabványos üzemeltetési eljárások létrehozása a dedikált rendszergazdai fiókok használata körül. A rendszergazdai fiókok számának figyeléséhez használja a Azure Security Center identitás-és hozzáférés-kezelés lehetőséget.

A Microsoft-szolgáltatásokhoz igénybe vehet egy igény szerinti vagy elég hozzáférésű hozzáférést Azure AD Privileged Identity Management Kiemelt szerepkörökkel, és Azure Resource Manager is.

- [További információ a Privileged Identity Management](/azure/active-directory/privileged-identity-management)

**Azure Security Center figyelés**: jelenleg nem érhető el

**Felelősség**: ügyfél

### <a name="34-use-azure-active-directory-single-sign-on-sso"></a>3,4: Azure Active Directory egyszeri bejelentkezés (SSO) használata

**Útmutató**: ha lehetséges, használjon Azure Active Directory SSO-t a különálló önálló hitelesítő adatok konfigurálása helyett. Azure Security Center identitás-és hozzáférés-kezelési javaslatok használata.

- [Az egyszeri bejelentkezés ismertetése az Azure AD-vel](../active-directory/manage-apps/what-is-single-sign-on.md)

**Azure Security Center figyelés**: jelenleg nem érhető el

**Felelősség**: ügyfél

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3,5: Multi-Factor Authentication használata az összes Azure Active Directory-alapú hozzáféréshez

**Útmutató**: engedélyezze Azure Active Directory multi-Factor Authentication (MFA) használatát, és kövesse Azure Security Center identitás-és hozzáférés-kezelési javaslatait.

- [Az MFA engedélyezése az Azure-ban](../active-directory/authentication/howto-mfa-getstarted.md)

- [Identitás és hozzáférés figyelése Azure Security Centeron belül](../security-center/security-center-identity-access.md)

**Azure Security Center figyelés**: igen

**Felelősség**: ügyfél

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3,6: dedikált gépek (privilegizált hozzáférési munkaállomások) használata az összes felügyeleti feladathoz

**Útmutató**: a többtényezős hitelesítés (a Kiemelt jogosultságú hozzáférési munkaállomások) használata a Azure Firewall és a kapcsolódó erőforrások bevezetéséhez és konfigurálásához konfigurált multi-Factor Authentication (MFA) használatával. 

- [További tudnivalók a privilegizált hozzáférésű munkaállomásokról](/windows-server/identity/securing-privileged-access/privileged-access-workstations) 

- [Az MFA engedélyezése az Azure-ban](../active-directory/authentication/howto-mfa-getstarted.md)

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: ügyfél

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3,7: naplózás és riasztás a gyanús tevékenységekről a rendszergazdai fiókoktól

**Útmutató**: Azure Active Directory biztonsági jelentések használata naplók és riasztások generálásához, ha a környezetben gyanús vagy nem biztonságos tevékenység történik. A Azure Security Center használatával figyelheti az identitás-és hozzáférési tevékenységeket. 

- [A kockázatos tevékenységre megjelölt Azure AD-felhasználók azonosítása](/azure/active-directory/reports-monitoring/concept-user-at-risk) 

- [A felhasználók identitás-és hozzáférési tevékenységének figyelése Azure Security Center](../security-center/security-center-identity-access.md)

**Azure Security Center figyelés**: igen

**Felelősség**: ügyfél

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3,8: az Azure-erőforrások kezelése csak jóváhagyott helyekről

**Útmutató**: a feltételes hozzáférés elnevezett helyeivel engedélyezheti, hogy a hozzáférés csak az IP-címtartományok vagy országok/régiók adott logikai csoportjaihoz legyen elérhető. 

- [Elnevezett helyszínek konfigurálása az Azure-ban](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Azure Security Center figyelés**: jelenleg nem érhető el

**Felelősség**: ügyfél

### <a name="39-use-azure-active-directory"></a>3,9: a Azure Active Directory használata

**Útmutató**: a Azure Active Directory (Azure ad) használata központi hitelesítési és engedélyezési rendszerrel. Az Azure AD az adatok védelme érdekében erős titkosítást használ a nyugalmi és a továbbítási adatokhoz. Az Azure AD emellett a felhasználó hitelesítő adatainak a sók, a kivonatok és a biztonságos tárolását is tartalmazza. 

- [Azure AD-példány létrehozása és konfigurálása](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

**Azure Security Center figyelés**: jelenleg nem érhető el

**Felelősség**: ügyfél

### <a name="310-regularly-review-and-reconcile-user-access"></a>3,10: a felhasználói hozzáférés rendszeres áttekintése és egyeztetése

**Útmutató**: az Azure ad olyan naplókat biztosít, amelyek segítenek az elavult fiókok felderítésében. Emellett az Azure Identity Access Reviews használatával hatékonyan kezelheti a csoporttagságok kezelését, a vállalati alkalmazásokhoz való hozzáférést és a szerepkör-hozzárendeléseket. A felhasználói hozzáférés rendszeresen felülvizsgálható, hogy csak a megfelelő felhasználók férhessenek hozzá. 

- [Az Azure AD jelentéskészítés ismertetése](/azure/active-directory/reports-monitoring/)

- [Az Azure Identity hozzáférési felülvizsgálatok használata](../active-directory/governance/access-reviews-overview.md)

**Azure Security Center figyelés**: jelenleg nem érhető el

**Felelősség**: ügyfél

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3,11: a figyelő megkísérli a deaktivált hitelesítő adatok elérését

**Útmutató**: hozzáférhet az Azure ad bejelentkezési tevékenységeihez, a naplózási és a kockázati Eseménynapló-forrásokhoz, amelyek lehetővé teszik bármely Siem/monitoring eszköz integrálását. 

Ezt a folyamatot leegyszerűsítheti Azure Active Directory felhasználói fiókok diagnosztikai beállításainak létrehozásával, valamint a naplók és a bejelentkezési naplók Log Analytics munkaterületre való elküldésével. Log Analytics munkaterületen belül konfigurálhatja a kívánt riasztásokat. 

- [Azure-beli tevékenység-naplók integrálása a Azure Monitorba](/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)

**Azure Security Center figyelés**: jelenleg nem érhető el

**Felelősség**: ügyfél

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3,12: riasztás a fiók bejelentkezési viselkedésének eltérése esetén

**Útmutató**: az Azure ad kockázatkezelési és identitás-védelmi funkcióival konfigurálhatja a felhasználói identitásokkal kapcsolatos gyanús műveletekre vonatkozó automatizált válaszokat. További vizsgálat céljából az Azure Sentinelbe is betöltheti az adatmennyiséget. 

- [Az Azure AD kockázatos bejelentkezések megtekintése](/azure/active-directory/reports-monitoring/concept-risky-sign-ins)

- [Az Identity Protection kockázati házirendjeinek konfigurálása és engedélyezése](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [Az Azure Sentinel előkészítése](../sentinel/quickstart-onboard.md)

**Azure Security Center figyelés**: jelenleg nem érhető el

**Felelősség**: ügyfél

## <a name="data-protection"></a>Adatvédelem

*További információt az [Azure biztonsági teljesítményteszt: Adatvédelem](/azure/security/benchmarks/security-control-data-protection)című témakörben talál.*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4,1: bizalmas információk leltárának fenntartása

**Útmutató**: a címkék használatával segítheti a Azure Firewall és a bizalmas adatokat tároló vagy feldolgozó kapcsolódó erőforrások nyomon követését. 

- [Címkék létrehozása és használata](/azure/azure-resource-manager/resource-group-using-tags)

**Azure Security Center figyelés**: jelenleg nem érhető el

**Felelősség**: ügyfél

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4,2: bizalmas adatok tárolására vagy feldolgozására szolgáló rendszerek elkülönítése

**Útmutató**: az elkülönítés megvalósítása különálló előfizetések és felügyeleti csoportok használatával az egyes biztonsági tartományokhoz, például a környezeti típusokhoz és az adatérzékeny szintekhez. Korlátozhatja az alkalmazásokhoz és a vállalati környezetekhez igénybe veheti Azure Firewall erőforrásokhoz való hozzáférés szintjét. Az Azure-erőforrásokhoz való hozzáférést Azure Active Directory szerepköralapú hozzáférés-vezérlés segítségével szabályozhatja. 

- [További Azure-előfizetések létrehozása](/azure/billing/billing-create-subscription)

- [Management Groups létrehozása](../governance/management-groups/create.md)

- [Címkék létrehozása és használata](/azure/azure-resource-manager/resource-group-using-tags)

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: ügyfél

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4,3: a bizalmas adatok jogosulatlan átvitelének figyelése és letiltása

**Útmutató**: külső gyártótól származó megoldás kihasználása az Azure Marketplace-ről olyan hálózati környezetekben, amelyek figyelik a bizalmas adatok jogosulatlan átvitelét, és blokkolja az ilyen átviteleket, miközben riasztást küldenek az információs biztonsági szakemberek számára. 

A Microsoft által felügyelt mögöttes platform esetében a Microsoft az összes vásárlói tartalmat bizalmasként kezeli, és védelmet nyújt az ügyfelek adatvesztése és a kitettség ellen. Annak biztosítása érdekében, hogy az Azure-beli ügyféladatok biztonságban maradjanak, a Microsoft végrehajtotta és karbantartja a robusztus adatvédelmi szabályozást és képességeket. 

- [Az ügyfelek adatvédelmének megismerése az Azure-ban](../security/fundamentals/protection-customer-data.md)

**Azure Security Center figyelés**: jelenleg nem érhető el

**Felelősség**: megosztott

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4,4: minden bizalmas adat titkosítása az átvitel során

**Útmutató**: az összes bizalmas adat titkosítása az átvitel során. Győződjön meg arról, hogy a Azure Firewallhoz és a kapcsolódó erőforrásokhoz csatlakozó ügyfelek képesek a TLS 1,2-es vagy újabb egyeztetésére. 

Kövesse Azure Security Center a inaktív adatok titkosítására és az átvitel közbeni titkosításra vonatkozó ajánlásokat, ahol lehetséges. 

- [A titkosítás ismertetése az Azure-ban](../security/fundamentals/encryption-overview.md#encryption-of-data-in-transit)

**Azure Security Center figyelés**: igen

**Felelősség**: megosztott

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4,5: aktív felderítési eszköz használata a bizalmas adatok azonosítására

**Útmutató**: az Azure-erőforrásokban tárolt bizalmas adatok Azure Firewall és kapcsolódó erőforrásokkal való azonosításához, valamint a szervezet bizalmas információinak leltárának frissítéséhez használjon egy külső gyártótól származó aktív felderítési eszközt.

- [Az ügyfelek adatvédelmének megismerése az Azure-ban](../security/fundamentals/protection-customer-data.md)

**Azure Security Center figyelés**: jelenleg nem érhető el

**Felelősség**: megosztott

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4,6: az erőforrásokhoz való hozzáférés szabályozása az Azure RBAC

**Útmutató**: az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC) használata a Azure Firewall és a kapcsolódó erőforrásokhoz való hozzáférés szabályozásához.

- [Az Azure RBAC konfigurálása](../role-based-access-control/role-assignments-portal.md)

**Azure Security Center figyelés**: jelenleg nem érhető el

**Felelősség**: ügyfél

### <a name="48-encrypt-sensitive-information-at-rest"></a>4,8: bizalmas adatok titkosítása a nyugalmi állapotban

**Útmutató**: az összes Azure-erőforráson használja a titkosítást az Azure Firewall és a kapcsolódó erőforrások használatával. A Microsoft azt javasolja, hogy az Azure kezelje a titkosítási kulcsokat, azonban lehetőség van arra, hogy a saját kulcsait bizonyos példányokban kezelje. 

- [A inaktív adatok titkosításának megismerése az Azure-ban](../security/fundamentals/encryption-atrest.md)

- [Az ügyfél által felügyelt titkosítási kulcsok konfigurálása](../storage/common/storage-encryption-keys-portal.md)

**Azure Security Center figyelés**: jelenleg nem érhető el

**Felelősség**: ügyfél

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4,9: a kritikus Azure-erőforrások változásainak naplózása és riasztása

**Útmutató**: a Azure monitor és az Azure-tevékenység naplójának használatával riasztásokat hozhat létre, amikor a módosítások Azure Firewallban lépnek életbe.

- [Riasztások létrehozása az Azure-tevékenységek naplózási eseményeihez](../azure-monitor/platform/alerts-activity-log.md)

**Azure Security Center figyelés**: jelenleg nem érhető el

**Felelősség**: ügyfél

## <a name="inventory-and-asset-management"></a>Leltár- és eszközfelügyelet

*További információt az [Azure biztonsági teljesítményteszt: leltár és eszközkezelés](/azure/security/benchmarks/security-control-inventory-asset-management)című témakörben talál.*

### <a name="62-maintain-asset-metadata"></a>6,2: az eszköz metaadatainak fenntartása

**Útmutató**: címkék alkalmazása Azure Firewall és kapcsolódó erőforrásokhoz, amelyekkel a metaadatok logikailag rendezhetők a besorolásba. 

- [Címkék létrehozása és használata](/azure/azure-resource-manager/resource-group-using-tags)

**Azure Security Center figyelés**: jelenleg nem érhető el

**Felelősség**: ügyfél

### <a name="63-delete-unauthorized-azure-resources"></a>6,3: jogosulatlan Azure-erőforrások törlése

**Útmutató**: a címkézés, a felügyeleti csoportok és az elkülönített előfizetések használata, ahol szükséges, Azure Firewall és kapcsolódó erőforrások rendszerezése és nyomon követése. Rendszeres időközönként egyeztetheti a leltárt, és gondoskodhat arról, hogy a jogosulatlan erőforrások törlése az előfizetésből időben történjen.

- [További Azure-előfizetések létrehozása](/azure/billing/billing-create-subscription)

- [Management Groups létrehozása](../governance/management-groups/create.md)

- [Címkék létrehozása és használata](/azure/azure-resource-manager/resource-group-using-tags)

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: ügyfél

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6,4: a jóváhagyott Azure-erőforrások leltárának meghatározása és karbantartása

**Útmutató**: a jóváhagyott Azure Firewall erőforrások leltárának létrehozása, beleértve a szervezeti igényeknek megfelelő konfigurációt.

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: ügyfél

### <a name="65-monitor-for-unapproved-azure-resources"></a>6,5: a nem jóváhagyott Azure-erőforrások figyelése

**Útmutató**: a Azure Policy használatával korlátozásokat állíthat be az előfizetése (i) ban létrehozható erőforrások típusára.

Az Azure Resource Graph használatával lekérdezheti vagy felderítheti Azure Firewall erőforrásait az előfizetése (ke) n belül. Győződjön meg arról, hogy a környezetben lévő összes Azure Firewall és kapcsolódó erőforrás jóvá van hagyva.

- [Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md)

- [Lekérdezések létrehozása az Azure Graph használatával](../governance/resource-graph/first-query-portal.md)

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: ügyfél

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6,7: nem jóváhagyott Azure-erőforrások és szoftveralkalmazások eltávolítása

**Útmutató**: saját folyamat implementálása a jogosulatlan Azure Firewall és a kapcsolódó erőforrások eltávolításához. Harmadik féltől származó megoldást is használhat a nem jóváhagyott Azure Firewall és kapcsolódó erőforrások azonosítására

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: ügyfél

### <a name="69-use-only-approved-azure-services"></a>6,9: csak jóváhagyott Azure-szolgáltatások használata

**Útmutató**: a Azure Policy használatával korlátozhatja, hogy mely szolgáltatásokat lehet kiépíteni a környezetében.

- [Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md)

- [Adott erőforrástípus megtagadása a következővel Azure Policy](/azure/governance/policy/samples/not-allowed-resource-types)

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: ügyfél

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6,11: korlátozza a felhasználók képességét a Azure Resource Manager való interakcióra

**Útmutató**: az Azure feltételes hozzáférés használatával korlátozhatja, hogy a felhasználók képesek legyenek az Azure Resources Managerrel való interakcióra az "Microsoft Azure felügyelet" alkalmazás "hozzáférés letiltása" beállításával. 

- [Feltételes hozzáférés konfigurálása az Azure Resources Manager elérésének blokkolásához](../role-based-access-control/conditional-access-azure-management.md)

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: ügyfél

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6,13: fizikailag vagy logikailag elkülöníthető a nagy kockázatú alkalmazások

**Útmutató**: az üzleti műveletekhez szükséges alkalmazások, illetve a szervezet eltérő kockázati profiljaival rendelkező környezetek elkülönítése és elválasztása külön Azure Firewall példányokkal elválasztva.

- [Az Azure Firewall üzembe helyezése és konfigurálása az Azure Portalon](deploy-cli.md)

- [Virtuális hálózat létrehozása](../virtual-network/quick-create-portal.md)

- [NSG létrehozása biztonsági konfigurációval](../virtual-network/tutorial-filter-network-traffic.md)

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: ügyfél

## <a name="secure-configuration"></a>Biztonságos konfiguráció

*További információt az [Azure biztonsági teljesítményteszt: biztonságos konfiguráció](/azure/security/benchmarks/security-control-secure-configuration)című témakörben talál.*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7,1: biztonságos konfigurációk létrehozása az összes Azure-erőforráshoz

**Útmutató**: a Azure Resource Manager képes a sablon exportálására JavaScript Object Notation (JSON) alkalmazásban, amelyet felül kell vizsgálni, hogy a konfigurációk megfeleljenek a szervezet biztonsági követelményeinek, vagy meghaladják a szükséges lépéseket.

Azure Security Center javaslatait az Azure-erőforrások biztonságos alapkonfigurációjának megfelelően is használhatja.

Az Azure-szabályzat jelenleg nem teljes mértékben támogatott. 

- [Egy-és többerőforrásos exportálás Azure Portal sablonba](../azure-resource-manager/templates/export-template-portal.md)

- [Biztonsági javaslatok – útmutató](../security-center/recommendations-reference.md)

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: ügyfél

### <a name="73-maintain-secure-azure-resource-configurations"></a>7,3: biztonságos Azure-erőforrás-konfigurációk karbantartása

**Útmutató**: az Azure Policy [deny] és a [Deploy, ha nem létezik] használata a biztonságos beállítások betartatásához a Azure Firewall és a kapcsolódó erőforrások között.  Emellett Azure Resource Manager sablonokat is használhat a Azure Firewall és a szervezete számára szükséges kapcsolódó erőforrások biztonsági beállításainak fenntartásához.

- [Azure Policy effektusok ismertetése](../governance/policy/concepts/effects.md)

- [Szabályzatok létrehozása és kezelése a megfelelőség kikényszerítése céljából](../governance/policy/tutorials/create-and-manage.md)

- [Azure Resource Manager sablonok áttekintése](../azure-resource-manager/templates/overview.md)

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: ügyfél

### <a name="75-securely-store-configuration-of-azure-resources"></a>7,5: az Azure-erőforrások biztonságos tárolása

**Útmutató**: az Azure DevOps használatával biztonságosan tárolhatók és kezelhetők a kódok, például az egyéni Azure-házirendek és Azure Resource Manager-sablonok. Az Azure DevOps felügyelt erőforrásainak eléréséhez engedélyeket adhat meg vagy tagadhat meg bizonyos felhasználók, beépített biztonsági csoportok vagy Azure Active Directory (Azure AD) által meghatározott csoportok számára, ha az integrálva van az Azure DevOps, vagy Active Directory, ha a TFS integrálva van.

- [Kód tárolása az Azure DevOps](https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops)

- [Engedélyek és csoportok az Azure DevOps](/azure/devops/organizations/security/about-permissions)

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: ügyfél

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7,7: az Azure-erőforrások konfigurációs felügyeleti eszközeinek üzembe helyezése

**Útmutató**: a Azure Firewall és a kapcsolódó erőforrások szabványos biztonsági konfigurációinak meghatározása és implementálása Azure Policy használatával. Azure Policy-Aliasok használatával egyéni házirendeket hozhat létre a Azure Firewall-erőforrások hálózati konfigurációjának naplózásához vagy érvénybe léptetéséhez. Az adott erőforrásokhoz kapcsolódó beépített szabályzat-definíciókat is igénybe vehet.  

- [Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md)

- [Aliasok használata](../governance/policy/concepts/definition-structure.md#aliases)

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: ügyfél

### <a name="712-manage-identities-securely-and-automatically"></a>7,12: az identitások biztonságos és automatikus kezelése

**Útmutató**: felügyelt identitások használatával biztosíthatja az Azure-szolgáltatások automatikus felügyelt identitását az Azure ad-ben. A felügyelt identitások lehetővé teszik bármely olyan szolgáltatás hitelesítését, amely támogatja az Azure AD-hitelesítést a Azure Resource Manager és az API/Azure Portal/CLI/PowerShell segítségével használható.

- [Felügyelt identitások konfigurálása](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)

**Azure Security Center figyelés**: jelenleg nem érhető el

**Felelősség**: ügyfél

### <a name="713-eliminate-unintended-credential-exposure"></a>7,13: a hitelesítő adatok nem szándékolt expozíciójának megszüntetése

**Útmutató**: hitelesítő adatok beolvasása a programkódon belül a hitelesítő adatok azonosításához. A hitelesítő adatok beolvasása azt is javasolja, hogy a felderített hitelesítő adatokat biztonságosabb helyszínekre (például Azure Key Vault) helyezze. 

- [A hitelesítő adatok beolvasójának beállítása](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: ügyfél

## <a name="data-recovery"></a>Adat-helyreállítás

*További információt az [Azure biztonsági teljesítményteszt: adat-helyreállítás](/azure/security/benchmarks/security-control-data-recovery)című témakörben talál.*

### <a name="91-ensure-regular-automated-back-ups"></a>9,1: rendszeres automatizált biztonsági másolatok biztosítása

**Útmutató**: a Azure Resource Manager használatával exportálhatja a Azure Firewall és a kapcsolódó erőforrásokat egy JavaScript Object Notation (JSON) sablonban, amely biztonsági mentésként használható a Azure Firewall és a kapcsolódó konfigurációkhoz.  Azure Firewall konfiguráció exportálásához a Azure Portal exportálás sablon szolgáltatásával is exportálhatja Azure Firewall.  A Azure Automation használatával automatikusan futtathatja a biztonsági mentési parancsfájlokat.

- [Egy-és többerőforrásos exportálás Azure Portal sablonba](../azure-resource-manager/templates/export-template-portal.md)

- [Azure Firewall üzembe helyezése sablon használatával](deploy-template.md)

- [A Microsoft Network Azure tűzfalak sablonjának referenciája](/azure/templates/microsoft.network/azurefirewalls)

- [Tudnivalók Azure Automation](../automation/automation-intro.md)

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: ügyfél

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: teljes rendszerbiztonsági mentés és minden ügyfél által felügyelt kulcs biztonsági mentése

**Útmutató**: a Azure Resource Manager használatával exportálhatja a Azure Firewall és a kapcsolódó erőforrásokat egy JavaScript Object Notation (JSON) sablonban, amely biztonsági mentésként használható a Azure Firewall és a kapcsolódó konfigurációkhoz.  Azure Firewall konfiguráció exportálásához a Azure Portal exportálás sablon szolgáltatásával is exportálhatja Azure Firewall.

- [Azure Firewall üzembe helyezése sablon használatával](deploy-template.md)

- [A Microsoft Network Azure tűzfalak sablonjának referenciája](/azure/templates/microsoft.network/azurefirewalls)

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: ügyfél

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: az összes biztonsági másolat ellenőrzése, beleértve az ügyfél által felügyelt kulcsokat

**Útmutató**: a helyreállítás rendszeres végrehajtásának lehetősége Azure Resource Manager sablon által támogatott fájlok használatával.  

- [Azure Firewall üzembe helyezése sablon használatával](deploy-template.md)

- [A Microsoft Network Azure tűzfalak sablonjának referenciája](/azure/templates/microsoft.network/azurefirewalls)

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: ügyfél

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: a biztonsági másolatok és az ügyfél által felügyelt kulcsok védelmének biztosítása

**Útmutató**: az Azure DevOps segítségével biztonságosan tárolhatja és kezelheti a kódokat, például az egyéni Azure-szabályzatokat, Azure Resource Manager sablonokat. Az Azure DevOps felügyelt erőforrások védelme érdekében engedélyeket adhat meg vagy tagadhat meg bizonyos felhasználók, beépített biztonsági csoportok vagy Azure Active Directory (Azure AD) által meghatározott csoportok számára, ha az integrálva van az Azure DevOps, vagy Active Directory, ha a TFS integrálva van.

- [Kód tárolása az Azure DevOps](https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops)

- [Engedélyek és csoportok az Azure DevOps](/azure/devops/organizations/security/about-permissions)

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: ügyfél

## <a name="incident-response"></a>Incidensmegoldás

*További információt az [Azure biztonsági teljesítményteszt: incidens válasza](/azure/security/benchmarks/security-control-incident-response)című témakörben talál.*

### <a name="101-create-an-incident-response-guide"></a>10,1: incidens-válaszi útmutató létrehozása

**Útmutató**: az incidensekre adott válaszokra vonatkozó útmutató kiépítése a szervezet számára. Győződjön meg arról, hogy van olyan írásos incidens-válasz, amely meghatározza a személyzet összes szerepkörét, valamint az incidensek kezelésének és kezelésének fázisait az észleléstől az incidens utáni felülvizsgálatig. 

- [Útmutató a saját biztonsági incidensek megoldási folyamatának létrehozásához](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Microsoft Security Response Center – incidens anatómiája](https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/)

- [A NIST számítógépes biztonsági incidensek kezelésének útmutatója a saját incidensekre vonatkozó válaszadási terv létrehozásához nyújtott támogatáshoz](https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final)

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: ügyfél

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10,2: incidensek pontozásának és rangsorolási eljárásának létrehozása

**Útmutató**: a Security Center súlyosságot rendel az egyes riasztásokhoz, hogy a prioritások alapján ki lehessen deríteni, hogy mely riasztásokat kell először megvizsgálni. A súlyosság azon alapul, hogy az Security Center milyen mértékben szerepel a riasztás kibocsátásához használt elemzésben vagy elemzésben, valamint azt a megbízhatósági szintet, amely a riasztáshoz vezető tevékenység mögött rosszindulatú szándékú volt. 

Emellett egyértelműen megjelölheti az előfizetéseket (pl.: éles környezetben, nem gyártva) címkék használatával és elnevezési rendszer létrehozása az Azure-erőforrások egyértelmű azonosításához és kategorizálásához, különösen a bizalmas adatok feldolgozásához.  Az Ön felelőssége, hogy rangsorolja a riasztások szervizelését az Azure-erőforrások és-környezet kritikus jellemzői alapján, ahol az incidens történt. 

- [Biztonsági riasztások az Azure Security Centerben](../security-center/security-center-alerts-overview.md) 

- [Címkék használata az erőforrások rendszerezéséhez](/azure/azure-resource-manager/resource-group-using-tags)

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: ügyfél

### <a name="103-test-security-response-procedures"></a>10,3: biztonsági reagálási eljárások tesztelése

**Útmutató**: az Azure-erőforrások védelmének biztosítása érdekében a rendszer az incidensek reagálási képességeinek rendszeres tesztelésére szolgáló gyakorlatokat hajt végre. Azonosítsa a gyenge pontokat és a réseket, és szükség szerint módosítsa a tervet. 

- [A NIST kiadványa – útmutató az IT-csomagok és-képességek teszteléséhez, betanításához és alkalmazásához](https://csrc.nist.gov/publications/detail/sp/800-84/final)

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: ügyfél

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10,4: biztonsági incidensek elérhetőségének biztosítása és riasztási értesítések konfigurálása biztonsági incidensekhez

**Útmutató**: a Microsoft a biztonsági incidensek elérhetőségi adatait arra használja fel, hogy felvegye Önnel a kapcsolatot, ha a Microsoft Security Response Center (MSRC) felfedi, hogy az adatokat egy törvénytelen vagy jogosulatlan fél is hozzáférte. A problémák megoldása érdekében tekintse át az incidenseket a tény után. 

- [A Azure Security Center biztonsági kapcsolattartó beállítása](../security-center/security-center-provide-security-contact-details.md)

**Azure Security Center figyelés**: igen

**Felelősség**: ügyfél

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10,5: biztonsági riasztások beépítése az incidensek gyorsreagáló rendszerébe

**Útmutató**: az Azure Security Center-riasztások és javaslatok exportálása a folyamatos exportálás funkcióval az Azure-erőforrásokkal kapcsolatos kockázatok azonosítása érdekében.

A folyamatos exportálás lehetővé teszi a riasztások és javaslatok manuális és folyamatos exportálását. Az Azure Security Center adatösszekötővel továbbíthatja a riasztásokat az Azure Sentinel szolgáltatásba.

- [Folyamatos exportálás konfigurálása](../security-center/continuous-export.md)

- [Riasztások továbbítása az Azure Sentinelbe](../sentinel/connect-azure-security-center.md)

**Azure Security Center figyelés**: jelenleg nem érhető el

**Felelősség**: ügyfél

### <a name="106-automate-the-response-to-security-alerts"></a>10,6: a biztonsági riasztásokra adott válasz automatizálása

**Útmutató**: a Azure Security Center munkafolyamat-automatizálási funkciója segítségével automatikusan elindíthatja a válaszokat az "Logic apps" használatával a biztonsági riasztások és az Azure-erőforrások védelme érdekében javasolt javaslatok alapján. 

- [A Munkafolyamat-automatizálás és a Logic Apps konfigurálása](../security-center/workflow-automation.md)

**Azure Security Center figyelés**: jelenleg nem érhető el

**Felelősség**: ügyfél

## <a name="penetration-tests-and-red-team-exercises"></a>Behatolási tesztek és Red Team-gyakorlatok

*További információkért tekintse meg az [Azure biztonsági teljesítményteszt: behatolási tesztek és a Red Team gyakorlatok](/azure/security/benchmarks/security-control-penetration-tests-red-team-exercises)című témakört.*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11,1: az Azure-erőforrások rendszeres behatolásának tesztelése, valamint az összes kritikus biztonsági vizsgálat szervizelésének biztosítása

**Útmutató**: kövesse a Microsoft részvételi szabályait, hogy a behatolási tesztek ne sértsék a Microsoft-házirendeket. A Microsoft által felügyelt felhőalapú infrastruktúrával, szolgáltatásokkal és alkalmazásokkal kapcsolatban a Microsoft stratégiáját és a Red Teaming és a Live site penetráció tesztelését is használhatja. 

- [Behatolás-tesztelési szabályok engagement](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)

- [Microsoft Cloud vörös összevonása](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: megosztott

## <a name="next-steps"></a>Következő lépések

- Lásd az [Azure biztonsági teljesítménytesztét](/azure/security/benchmarks/overview)
- További információ az [Azure biztonsági alaptervekről](/azure/security/benchmarks/security-baselines-overview)
