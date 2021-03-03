---
title: Security Center Azure biztonsági alapterve
description: A Security Center biztonsági alapterve az Azure biztonsági Teljesítménytesztben meghatározott biztonsági javaslatok megvalósítására szolgáló eljárási útmutatást és erőforrásokat biztosít.
author: msmbaldwin
ms.service: security-center
ms.topic: conceptual
ms.date: 08/05/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 330b466360cc5687f78db81fbb4545e1c3409098
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2021
ms.locfileid: "101729878"
---
# <a name="azure-security-baseline-for-security-center"></a>Security Center Azure biztonsági alapterve

Ez a biztonsági alapkonfiguráció az [Azure biztonsági teljesítménytesztre](../security/benchmarks/overview.md) vonatkozó útmutatást alkalmazza Azure Security Centerra. Az Azure Security Benchmark ajánlásokat ad arra nézve, hogy hogyan tehetők biztonságossá a felhőalapú megoldások az Azure-ban. A tartalom az Azure biztonsági teljesítményteszt által meghatározott **biztonsági vezérlők** szerint van csoportosítva, valamint a Azure Security Center vonatkozó kapcsolódó útmutatás. A Azure Security Center nem alkalmazható **vezérlők** ki vannak zárva. Ha szeretné megtekinteni, hogyan Azure Security Center teljes mértékben leképezni az Azure biztonsági Teljesítménytesztét, tekintse meg a [teljes Azure Security Center biztonsági alapterv-leképezési fájlt](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="network-security"></a>Hálózati biztonság

*További információt az [Azure biztonsági teljesítményteszt: hálózati biztonság](../security/benchmarks/security-control-network-security.md)című témakörben talál.*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1,1: Azure-erőforrások biztosítása virtuális hálózatokon belül

**Útmutató**: a Azure Security Center egy alapvető Azure-ajánlat. Virtuális hálózat, alhálózat vagy hálózati biztonsági csoport nem rendelhető hozzá közvetlenül Security Centerhoz. Ha engedélyezi az adatgyűjtést a számítási erőforrások számára, akkor a Security Center a Log Analytics munkaterületről gyűjtött adatokat tárolja, beállíthatja, hogy a munkaterület a munkaterület adataihoz a virtuális hálózat privát végpontján keresztül hozzáférjen. Emellett, ha az adatgyűjtési Security Center használata a kiszolgálókon üzembe helyezett Log Analytics-ügynökre támaszkodik a biztonsági adatok összegyűjtéséhez és a számítási erőforrások védelmének biztosításához. A Log Analytics ügynöknek szüksége van bizonyos portokra és protokollokra, hogy a megfelelő működéshez meg lehessen nyitni a Security Center. Zárolja a hálózatokat, hogy csak a szükséges portokat és protokollokat engedélyezze, és csak az alkalmazás által a hálózati biztonsági csoportokon keresztül történő működéshez szükséges további szabályokat adja meg.

- [Adatgyűjtés az Azure Security Centerben](security-center-enable-data-collection.md)

- [Hálózati adatforgalom hálózati biztonsági csoporttal](../virtual-network/tutorial-filter-network-traffic.md)

- [A Log Analytics ügynök használatára vonatkozó tűzfalszabályok](../azure-monitor/agents/log-analytics-agent.md#firewall-requirements)

- [Az Azure privát hivatkozásának megismerése](../private-link/private-link-overview.md) 

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1,9: a hálózati eszközök szabványos biztonsági konfigurációinak fenntartása

**Útmutató**: az Azure Security Center ajánlat nem integrálható közvetlenül egy virtuális hálózattal, de képes adatokat gyűjteni a hálózatokra telepített log Analytics ügynökkel konfigurált kiszolgálókról. Az adatküldésre konfigurált kiszolgálókon a Security Center bizonyos portok és protokollok megfelelő kommunikációjának engedélyezése szükséges. Megadhatja és kikényszerítheti ezeknek a hálózati erőforrásoknak a szabványos biztonsági konfigurációit Azure Policy.

Az Azure tervrajzai segítségével leegyszerűsítheti a nagy léptékű Azure-környezeteket a főbb környezeti összetevők, például Azure Resource Manager sablonok, szerepkör-hozzárendelések és Azure Policy-hozzárendelések egyetlen tervrajz-definícióban való kicsomagolásával. A terv az új előfizetésekre is alkalmazható Security Center konfigurációk és kapcsolódó hálózati erőforrások következetes és biztonságos üzembe helyezéséhez.

- [Adatgyűjtés az Azure Security Centerben](security-center-enable-data-collection.md)

- [A Log Analytics ügynök használatára vonatkozó tűzfalszabályok](../azure-monitor/agents/log-analytics-agent.md#firewall-requirements)

- [Az Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md) 

- [Azure Policy minták a hálózatkezeléshez](../governance/policy/samples/built-in-policies.md#network)

- [Azure Blueprint létrehozása](../governance/blueprints/create-blueprint-portal.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="110-document-traffic-configuration-rules"></a>1,10: a dokumentum forgalmának konfigurációs szabályai

**Útmutató**: az Azure Security Center ajánlat nem integrálható közvetlenül egy virtuális hálózattal, de képes adatokat gyűjteni a hálózatokra telepített log Analytics ügynökkel konfigurált kiszolgálókról. Az adatküldésre konfigurált kiszolgálókon a Security Center bizonyos portok és protokollok megfelelő kommunikációjának engedélyezése szükséges. Megadhatja és kikényszerítheti ezeknek a hálózati erőforrásoknak a szabványos biztonsági konfigurációit Azure Policy.

A hálózati biztonsági csoportokhoz és más erőforrásokhoz (például a hálózatokhoz tartozó kiszolgálókhoz) használjon erőforrás-címkéket, amelyek úgy vannak konfigurálva, hogy biztonsági naplókat küldjenek Azure Security Centerra. Az egyes hálózati biztonsági csoportokra vonatkozó szabályok esetében a "Leírás" mező használatával dokumentálhatja a hálózatra vagy hálózatra irányuló forgalmat engedélyező szabályokat. 

A címkézéshez kapcsolódó beépített Azure Policy definíciók bármelyikét használhatja, például a "címke és az érték megkövetelése" beállítást, hogy az összes erőforrás címkével legyen létrehozva, és értesítse a meglévő címkézetlen erőforrásokról.

A Azure PowerShell vagy az Azure CLI használatával a címkék alapján kereshet vagy végezhet műveleteket az erőforrásokon. 

- [Adatgyűjtés az Azure Security Centerben](security-center-enable-data-collection.md)

- [A Log Analytics ügynök használatára vonatkozó tűzfalszabályok](../azure-monitor/agents/log-analytics-agent.md#firewall-requirements)

- [Címkék létrehozása és használata](../azure-resource-manager/management/tag-resources.md) 

- [Azure-Virtual Network létrehozása](../virtual-network/quick-create-portal.md) 

- [Hálózati forgalom szűrése hálózati biztonsági csoport szabályaival](../virtual-network/tutorial-filter-network-traffic.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1,11: automatikus eszközök használata a hálózati erőforrások konfigurációjának figyelésére és a változások észlelésére

**Útmutató**: az Azure-tevékenység naplójának használata az erőforrás-konfigurációk figyelésére és a Azure Security Center kapcsolódó hálózati erőforrások változásainak észlelésére. Riasztásokat hozhat létre a Azure Monitorban, hogy értesítse a kritikus erőforrások módosításainak elvégzéséről.

- [Azure-Tevékenységnaplók eseményeinek megtekintése és lekérése](../azure-monitor/essentials/activity-log.md#view-the-activity-log) 

- [Riasztások létrehozása a Azure Monitorban](../azure-monitor/alerts/alerts-activity-log.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

## <a name="logging-and-monitoring"></a>Naplózás és figyelés

*További információt az [Azure biztonsági teljesítményteszt: naplózás és figyelés](../security/benchmarks/security-control-logging-monitoring.md)című témakörben talál.*

### <a name="22-configure-central-security-log-management"></a>2,2: a központi biztonsági naplók felügyeletének konfigurálása

**Útmutató**: a Azure Security Center és a csatlakoztatott források által generált biztonsági adatokat egy központi log Analytics munkaterülettel összesíti. 

Security Center adatgyűjtésének konfigurálásával biztonsági adatok és események küldhetők a csatlakoztatott Azure-beli számítási erőforrásokból egy központi Log Analytics munkaterületre. Az adatgyűjtés mellett a folyamatos exportálás funkcióval továbbíthatja a Security Center által generált biztonsági riasztásokat és javaslatokat a központi Log Analytics munkaterületre. Azure Monitor a Security Center és a csatlakoztatott Azure-erőforrások által generált biztonsági adatokon is lekérdezheti és elvégezheti az elemzést. 

Azt is megteheti, hogy a Security Center által készített adatküldés az Azure Sentinel vagy egy harmadik féltől származó SIEM-nek.

- [Security Center-adatfeldolgozás folyamatos exportálása](continuous-export.md)

- [Adatgyűjtés az Azure Security Centerben](security-center-enable-data-collection.md)

- [Az Azure Sentinel előkészítése](../sentinel/quickstart-onboard.md) 

- [Platform-naplók és-metrikák összegyűjtése Azure Monitor](../azure-monitor/essentials/diagnostic-settings.md) 

- [Az Azure-beli virtuális gépek belső gazdagép-naplóinak összegyűjtése Azure Monitor](../azure-monitor/vm/quick-collect-azurevm.md)

- [Ismerkedés a Azure Monitor és a harmadik féltől származó SIEM-integrációval](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="23-enable-audit-logging-for-azure-resources"></a>2,3: az Azure-erőforrások naplózásának engedélyezése

**Útmutató**: a Azure monitor tevékenységi naplók automatikusan elérhetők, ezek a naplók az erőforráshoz tartozó összes írási műveletet tartalmazzák, például a Azure Security centert, beleértve a művelet elindítását, a műveletet elindító műveleteket, illetve amikor történtek. Az Azure-tevékenységek naplóinak elküldése Log Analytics munkaterületre a naplózási konszolidáció és a megnövekedett megőrzés érdekében.

- [Platform-naplók és-metrikák összegyűjtése Azure Monitor](../azure-monitor/essentials/diagnostic-settings.md) 

- [A naplózás és a különböző naplózási típusok megismerése az Azure-ban](../azure-monitor/essentials/platform-logs-overview.md)

- [Tevékenységek naplóinak elküldése Log Analytics munkaterületre](../azure-monitor/essentials/activity-log.md#send-to-log-analytics-workspace)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="25-configure-security-log-storage-retention"></a>2,5: a biztonsági napló tárolási adatmegőrzésének konfigurálása

**Útmutató**: Azure monitor a szervezet megfelelőségi szabályainak megfelelően állítsa be a log Analytics munkaterület megőrzési időszakát. Azure Storage-fiókokat használhat hosszú távú és archiválási tároláshoz. 

- [Az adatmegőrzési időszak módosítása Log Analytics](../azure-monitor/logs/manage-cost-storage.md#change-the-data-retention-period) 

- [Adatmegőrzési szabályzat konfigurálása az Azure Storage-fiók naplóihoz](../storage/common/manage-storage-analytics-logs.md#configure-logging)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="26-monitor-and-review-logs"></a>2,6: naplók figyelése és áttekintése

**Útmutató**: a Azure Security Center és a kapcsolódó erőforrásai által előállított naplók elemzése és monitorozása a rendellenes viselkedés érdekében, valamint az eredmények rendszeres áttekintése. A naplók áttekintéséhez és a naplózási adatok lekérdezéséhez használja a Azure Monitor és egy Log Analytics munkaterületet.

Alternatív megoldásként engedélyezheti és elvégezheti az Azure Sentinel vagy egy harmadik féltől származó SIEM-et. 

- [Az Azure Sentinel előkészítése](../sentinel/quickstart-onboard.md) 

- [Log Analytics lekérdezések első lépései](../azure-monitor/logs/log-analytics-tutorial.md) 

- [Egyéni lekérdezések végrehajtása a Azure Monitorban](../azure-monitor/logs/get-started-queries.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="27-enable-alerts-for-anomalous-activities"></a>2,7: riasztások engedélyezése rendellenes tevékenységekhez

**Útmutató**: Azure monitor naplózási riasztások konfigurálása olyan nemkívánatos vagy rendellenes tevékenységek lekérdezéséhez, amelyeket a tevékenység naplója vagy a Azure Security Center által létrehozott adatai rögzítenek. Állítsa be a műveleti csoportokat úgy, hogy a szervezet értesítést kapjon, és akkor is tegyen lépéseket, ha a rendellenes tevékenységre vonatkozóan naplózási riasztást indít. Az Security Center munkafolyamat-automatizálási szolgáltatással aktiválhatja a logikai alkalmazásokat a biztonsági riasztások és javaslatok esetében. Security Center munkafolyamatok segítségével értesítheti a felhasználókat az incidensek megválaszolásáról, vagy műveleteket hajthat végre a riasztási információk alapján.

Alternatív megoldásként engedélyezheti és elvégezheti a Azure Security Center által az Azure Sentinelhez kapcsolódó és azokkal kapcsolatos adatkezelést. Az Azure Sentinel olyan forgatókönyveket támogat, amelyek lehetővé teszik az automatizált veszélyforrások számára a biztonsággal kapcsolatos problémákra való reagálást.

- [Munkafolyamat-automatizálás Azure Security Center](workflow-automation.md)

- [Riasztások kezelése Azure Security Centerban](security-center-managing-and-responding-alerts.md) 

- [Riasztás a log Analytics-naplófájlok adatkezeléséről](../azure-monitor/alerts/tutorial-response.md)

- [Fenyegetésre adott automatikus válaszok beállítása az Azure Sentinelben](../sentinel/tutorial-respond-threats-playbook.md)

- [Riasztások naplózása Azure Monitor](../azure-monitor/alerts/alerts-unified-log.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

## <a name="identity-and-access-control"></a>Identitás- és hozzáférés-vezérlés

*További információt az [Azure biztonsági teljesítményteszt: identitás-és hozzáférés-vezérlés](../security/benchmarks/security-control-identity-access-control.md)című témakörben talál.*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3,1: a felügyeleti fiókok leltárának karbantartása

**Útmutató**: az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC) lehetővé teszi az Azure-erőforrásokhoz való hozzáférés kezelését a szerepkör-hozzárendeléseken keresztül. Ezeket a szerepköröket hozzárendelheti a felhasználókhoz, a csoportok egyszerű szolgáltatásaihoz és a felügyelt identitásokhoz. Bizonyos erőforrásokhoz előre meghatározott beépített szerepkörök tartoznak, és ezek a szerepkörök olyan eszközökkel leltározhatók vagy kérdezhetők le, mint az Azure CLI, az Azure PowerShell vagy az Azure Portal. A Azure Security Center beépített szerepkörökkel rendelkezik a biztonsági olvasó vagy a biztonsági Admin', amely lehetővé teszi a felhasználóknak a biztonsági házirendek olvasását vagy frissítését, valamint a riasztások és javaslatok elutasítását.

- [Engedélyek az Azure Security Centerben](security-center-permissions.md)

- [Címtárbeli szerepkör beszerzése az Azure AD-ben a PowerShell-lel](/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0) 

- [Címtárbeli szerepkör tagjainak beszerzése az Azure AD-ben a PowerShell-lel](/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="33-use-dedicated-administrative-accounts"></a>3,3: dedikált rendszergazdai fiókok használata

**Útmutató**: szabványos működési eljárások létrehozása az Azure platform dedikált rendszergazdai fiókjainak használata vagy a Azure Security Center-ajánlat esetében. Azure Security Center identitás-és hozzáférés-kezelési szolgáltatással figyelheti a Azure Active Directoryban lévő rendszergazdai fiókok számát. A Security Center beépített szerepkörökkel rendelkezik a biztonsági Admin', amely lehetővé teszi a felhasználók számára a biztonsági házirendek frissítését, valamint a riasztások és javaslatok elutasítását, így minden olyan felhasználót, aki rendszeresen rendelkezik ezzel a szerepkör-hozzárendeléssel, áttekintheti és egyeztetheti azokat.

Emellett a dedikált rendszergazdai fiókok nyomon követésének elősegítése érdekében Azure Security Center vagy beépített Azure-szabályzatokból származó javaslatokat is használhat, például:

- Az előfizetéshez egynél több tulajdonos rendelhető hozzá
- A tulajdonosi engedélyekkel rendelkező elavult fiókokat el kell távolítani az előfizetésből
- A tulajdonosi engedélyekkel rendelkező külső fiókokat el kell távolítani az előfizetésből

- [Engedélyek az Azure Security Centerben](security-center-permissions.md)

- [Az identitás és a hozzáférés figyelésének Azure Security Center használata (előzetes verzió)](security-center-identity-access.md)

- [A Azure Policy használata](../governance/policy/tutorials/create-and-manage.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3,4: egyszeri bejelentkezés (SSO) használata Azure Active Directory

**Útmutató**: ha lehetséges, használjon Azure Active Directory SSO-t a különálló önálló hitelesítő adatok konfigurálása helyett. Azure Security Center identitás és hozzáférési javaslatok használata.

- [Az egyszeri bejelentkezés ismertetése az Azure AD-vel](../active-directory/manage-apps/what-is-single-sign-on.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3,5: Multi-Factor Authentication használata az összes Azure Active Directory-alapú hozzáféréshez

**Útmutató**: az Azure Active Directory MFA engedélyezése a Azure Security Center és a Azure Portal eléréséhez, Security Center identitás-és hozzáférési javaslatok követése. 

- [MFA engedélyezése az Azure-ban](../active-directory/authentication/howto-mfa-getstarted.md) 

- [Identitás és hozzáférés figyelése Azure Security Centeron belül](security-center-identity-access.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3,6: dedikált gépek (privilegizált hozzáférési munkaállomások) használata az összes felügyeleti feladathoz

**Útmutató**: emelt szintű jogosultságokat igénylő felügyeleti feladatokhoz használjon biztonságos, Azure által felügyelt munkaállomás (más néven privilegizált hozzáférési munkaállomás vagy Paw) használatát.

- [A biztonságos, Azure által felügyelt munkaállomások ismertetése](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

- [Az Azure AD MFA engedélyezése](../active-directory/authentication/howto-mfa-getstarted.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3,7: naplózás és riasztás a gyanús tevékenységekről a rendszergazdai fiókoktól

**Útmutató**: Azure Active Directory biztonsági jelentések és figyelés használata a környezetben előforduló gyanús vagy nem biztonságos tevékenységek észlelésére. A Azure Security Center használatával figyelheti az identitás-és hozzáférési tevékenységeket.

- [A kockázatos tevékenységek miatt megjelölt Azure AD-felhasználók azonosítása](../active-directory/identity-protection/overview-identity-protection.md) 

- [A felhasználók identitási és hozzáférési tevékenységeinek monitorozása az Azure Security Centerben](security-center-identity-access.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3,8: az Azure-erőforrások kezelése csak jóváhagyott helyekről

**Útmutató**: az Azure ad nevesített helyeinek használata az IP-címtartományok vagy országok/régiók adott logikai csoportjaihoz való hozzáférés engedélyezéséhez. 

- [Az Azure AD nevesített helyeinek konfigurálása](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="39-use-azure-active-directory"></a>3,9: a Azure Active Directory használata

**Útmutató**: a Azure Active Directory (Azure ad) használata központi hitelesítési és engedélyezési rendszeren a Azure Security Center használatakor. Az Azure AD az adatok védelme érdekében erős titkosítást használ a nyugalmi és a továbbítási adatokhoz. Az Azure AD emellett a felhasználó hitelesítő adatainak a sók, a kivonatok és a biztonságos tárolását is tartalmazza. Azure Security Center rendelkezik olyan beépített szerepkörökkel, amelyek például a biztonsági Admin', így a felhasználók frissíthetik a biztonsági házirendeket, és elérhetik a riasztásokat és a javaslatokat.

- [Engedélyek az Azure Security Centerben](security-center-permissions.md)

- [Azure AD-példány létrehozása és konfigurálása](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="310-regularly-review-and-reconcile-user-access"></a>3,10: a felhasználói hozzáférés rendszeres áttekintése és egyeztetése

**Útmutató**: a Azure Active Directory naplókat biztosít az elavult fiókok felderítéséhez. Emellett az Azure AD identitás-és hozzáférési felülvizsgálatok segítségével hatékonyan kezelheti a csoporttagságok kezelését, a vállalati alkalmazásokhoz való hozzáférést és a szerepkör-hozzárendeléseket. A Azure Security Centerhoz kapcsolódó felhasználói hozzáférés rendszeresen felülvizsgálható, hogy csak a megfelelő felhasználók férhessenek hozzájuk. 

- [Az Azure AD jelentéskészítés ismertetése](../active-directory/reports-monitoring/index.yml) 

- [Az Azure AD identitás- és hozzáférési felülvizsgálatainak használata](../active-directory/governance/access-reviews-overview.md)

**Az Azure Security Center monitorozása**: Nem alkalmazható

**Felelősség**: Ügyfél

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3,11: a figyelő megkísérli a deaktivált hitelesítő adatok elérését

**Útmutató**: hozzáférhet az Azure ad bejelentkezési tevékenységekhez, a naplózáshoz és a kockázatos Eseménynapló-forrásokhoz, amelyek lehetővé teszik bármely Siem/monitoring eszköz integrálását. 

Ezt a folyamatot leegyszerűsítheti, ha diagnosztikai beállításokat hoz létre az Azure AD felhasználói fiókjaihoz, és elküldi a naplókat és a bejelentkezési naplókat egy Log Analytics munkaterületre. Log Analytics munkaterületen belül konfigurálhatja a kívánt riasztásokat.  

- [Azure-beli Tevékenységnaplók integrálása a Azure Monitor](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="312-alert-on-account-login-behavior-deviation"></a>3,12: riasztás a fiók bejelentkezési viselkedésének eltérése esetén 

**Útmutató**: a Azure ad Identity Protection funkcióinak használatával konfigurálhatja a felhasználói identitásokkal kapcsolatos gyanús műveletekre vonatkozó automatizált válaszokat. További vizsgálat céljából az Azure Sentinelbe is betöltheti az adatmennyiséget. 

- [Az Azure AD kockázatos bejelentkezéseinek megtekintése](../active-directory/identity-protection/overview-identity-protection.md) 

- [Az Identity Protection kockázati házirendjeinek konfigurálása és engedélyezése](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md) 

- [Az Azure Sentinel előkészítése](../sentinel/quickstart-onboard.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

## <a name="data-protection"></a>Adatvédelem

*További információt az [Azure biztonsági teljesítményteszt: Adatvédelem](../security/benchmarks/security-control-data-protection.md)című témakörben talál.*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4,1: bizalmas információk leltárának fenntartása

**Útmutató**: az Azure-erőforrások nyomon követéséhez használható címkék használatával, például az log Analytics munkaterülettel, amely bizalmas biztonsági adatokat tárol Azure Security Centerről.

- [Címkék létrehozása és használata](../azure-resource-manager/management/tag-resources.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4,2: bizalmas adatok tárolására vagy feldolgozására szolgáló rendszerek elkülönítése

**Útmutató**: az elkülönítés megvalósítása különálló előfizetések és felügyeleti csoportok használatával az egyes biztonsági tartományokhoz, például a környezeti típusokhoz és az adatérzékeny szintekhez. Korlátozhatja az alkalmazásaihoz és a vállalati környezetekhez igénybe veheti az Azure-erőforrásokhoz való hozzáférés szintjét. Az Azure-erőforrásokhoz való hozzáférést az Azure RBAC segítségével szabályozhatja.

Alapértelmezés szerint a rendszer a Security Center háttér-szolgáltatásban tárolja Azure Security Center az adattárolást. Ha a szervezet a saját erőforrásaiban tárolja ezeket az adattárakat, konfigurálhat egy Log Analytics munkaterületet Security Center adatai, riasztásai és javaslatai tárolására. Ha saját munkaterületet használ, további elkülönítést is hozzáadhat a különböző munkaterületek konfigurálásával aszerint, hogy az adott környezet milyen környezetben származik.

- [Security Center-adatfeldolgozás folyamatos exportálása](continuous-export.md)

- [Adatgyűjtés az Azure Security Centerben](security-center-enable-data-collection.md)

- [További Azure-előfizetések létrehozása](../cost-management-billing/manage/create-subscription.md) 

- [Felügyeleti csoportok létrehozása](../governance/management-groups/create-management-group-portal.md) 

- [Címkék létrehozása és használata](../azure-resource-manager/management/tag-resources.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4,4: minden bizalmas adat titkosítása az átvitel során

**Útmutató**: az összes bizalmas adat titkosítása az átvitel során. Győződjön meg arról, hogy az Azure-erőforrásokhoz csatlakozó ügyfelek képesek a TLS 1,2 vagy újabb egyeztetésére. A Log Analytics-ügynökkel konfigurált és az Azure Security Centerbe való adatküldésre konfigurált virtuális gépeket a TLS 1,2 használatára kell konfigurálni.

Kövesse Azure Security Center a inaktív adatok titkosítására és az átvitel közbeni titkosításra vonatkozó ajánlásokat, ahol lehetséges. 

- [Adatok biztonságos küldése Log Analytics](../azure-monitor/logs/data-security.md#sending-data-securely-using-tls-12)

- [A titkosítás ismertetése az Azure-ban](../security/fundamentals/encryption-overview.md#encryption-of-data-in-transit)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Megosztott

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4,6: az erőforrásokhoz való hozzáférés szabályozása az Azure RBAC 

**Útmutató**: az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC) használatával kezelheti a Azure Security Center kapcsolódó adatokhoz és erőforrásokhoz való hozzáférést. A Azure Security Center beépített szerepkörökkel rendelkezik a biztonsági olvasó vagy a biztonsági Admin', amely lehetővé teszi a felhasználóknak a biztonsági házirendek olvasását vagy frissítését, valamint a riasztások és javaslatok elutasítását. A Security Center által gyűjtött adatokat tároló Log Analytics munkaterület olyan beépített szerepkörökkel is rendelkezik, mint például a "Log Analytics Reader", a "Log Analytics közreműködő" és mások. Rendelje hozzá a felhasználók számára a szükséges feladatok elvégzéséhez szükséges legkevésbé megengedő szerepkört. Például rendelje hozzá az olvasó szerepkört azokhoz a felhasználókhoz, akik csak az erőforrások biztonsági állapotával kapcsolatos információkat szeretnének megtekinteni, de nem végeznek műveleteket, például javaslatok alkalmazása vagy szerkesztési szabályzatok.

- [Azure Log Analytics-munkaterület engedélyei](../role-based-access-control/built-in-roles.md#log-analytics-reader)

- [Engedélyek az Azure Security Centerben](security-center-permissions.md)

- [Az Azure RBAC konfigurálása](../role-based-access-control/role-assignments-portal.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="48-encrypt-sensitive-information-at-rest"></a>4,8: bizalmas adatok titkosítása a nyugalmi állapotban

**Útmutató**: a Azure Security Center egy konfigurált log Analytics munkaterületet használ az általa generált adatkezelési, riasztások és javaslatok tárolásához. Konfiguráljon egy ügyfél által felügyelt kulcsot (CMK) a Security Center adatgyűjtéshez konfigurált munkaterülethez. A CMK lehetővé teszi, hogy a munkaterületre mentett vagy elküldhető összes adatfájlt egy Ön által létrehozott és birtokolt Azure Key Vault kulccsal titkosítsa. 

- [Azure Monitor – ügyfél által kezelt kulcs](../azure-monitor/logs/customer-managed-keys.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4,9: a kritikus Azure-erőforrások változásainak naplózása és riasztása

**Útmutató**: a Azure monitor használatával riasztásokat hozhat létre, amikor a módosítások a Azure Security Centerhoz kapcsolódó kritikus Azure-erőforrásokra változnak. Ezek a változások olyan műveleteket is tartalmazhatnak, amelyek a Security Centerhez kapcsolódó konfigurációkat módosítják, például a riasztások vagy javaslatok letiltását, illetve az adattárak frissítését vagy törlését.

- [Riasztások létrehozása az Azure-tevékenységek naplózási eseményeihez](../azure-monitor/alerts/alerts-activity-log.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

## <a name="vulnerability-management"></a>Biztonságirés-kezelés

*További információért lásd az [Azure biztonsági teljesítményteszt: biztonsági rés kezelése](../security/benchmarks/security-control-vulnerability-management.md)című témakört.*

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5,5: kockázatértékelési folyamat használatával rangsorolhatja a felderített biztonsági rések szervizelését

**Útmutató**: közös kockázati pontozási programot (például gyakori sebezhetőségi pontozási rendszer) vagy a harmadik féltől származó ellenőrzési eszköz által biztosított alapértelmezett kockázati minősítéseket használjon.

- [NIST-kiadvány – gyakori sebezhetőségi pontozási rendszerek](https://www.nist.gov/publications/common-vulnerability-scoring-system)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

## <a name="inventory-and-asset-management"></a>Leltár- és eszközfelügyelet

*További információt az [Azure biztonsági teljesítményteszt: leltár és eszközkezelés](../security/benchmarks/security-control-inventory-asset-management.md)című témakörben talál.*

### <a name="61-use-automated-asset-discovery-solution"></a>6,1: automatikus eszköz-felderítési megoldás használata

**Útmutató**: az Azure Resource Graph használatával kérdezheti le és derítheti fel az előfizetésekben Azure Security Center kapcsolódó összes erőforrást. Győződjön meg arról, hogy a bérlőben a megfelelő (olvasási) engedélyek szerepelnek, valamint az összes Azure-előfizetés enumerálása Security Center erőforrások felderítéséhez. 

- [Lekérdezések létrehozása az Azure Resource Graph Explorerrel](../governance/resource-graph/first-query-portal.md) 

- [Azure-előfizetések megtekintése](/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0) 

- [Az Azure RBAC ismertetése](../role-based-access-control/overview.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="62-maintain-asset-metadata"></a>6,2: az eszköz metaadatainak fenntartása

**Útmutató**: az Azure-erőforrások nyomon követéséhez használható címkék használatával, például az log Analytics munkaterülettel, amely bizalmas biztonsági adatokat tárol Azure Security Centerről.

- [Címkék létrehozása és használata](../azure-resource-manager/management/tag-resources.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="63-delete-unauthorized-azure-resources"></a>6,3: jogosulatlan Azure-erőforrások törlése

**Útmutató**: a címkézés, a felügyeleti csoportok és a különálló előfizetések használata, ha szükséges, a Azure Security Center erőforrások rendszerezéséhez és nyomon követéséhez. Rendszeres időközönként egyeztetheti a leltárt, és gondoskodhat arról, hogy a jogosulatlan erőforrások törlése az előfizetésből időben történjen.

Emellett az Azure Policy használatával korlátozásokat állíthat be az ügyfél-előfizetésekben létrehozható erőforrások típusára a következő beépített szabályzat-definíciók használatával:

- Nem engedélyezett erőforrástípusok
- Engedélyezett erőforrástípusok

- [További Azure-előfizetések létrehozása](../cost-management-billing/manage/create-subscription.md)

- [Management Groups létrehozása](../governance/management-groups/create-management-group-portal.md)

- [Címkék létrehozása és használata](../azure-resource-manager/management/tag-resources.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6,4: a jóváhagyott Azure-erőforrások leltárának meghatározása és karbantartása

**Útmutató**: a számítási erőforrásokhoz jóváhagyott Azure-erőforrások és jóváhagyott szoftverek leltárának létrehozása a szervezeti igényeknek megfelelően.

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="65-monitor-for-unapproved-azure-resources"></a>6,5: a nem jóváhagyott Azure-erőforrások figyelése

**Útmutató**: a Azure Policy használatával korlátozásokat állíthat be az előfizetésekben létrehozható erőforrásokra vonatkozóan. 

Az Azure Resource Graph használatával lekérdezheti és felderítheti az előfizetésükön belüli erőforrásokat.  Győződjön meg arról, hogy a környezetben lévő összes Azure-erőforrás jóvá van hagyva. 

- [Az Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md) 

- [Lekérdezések létrehozása az Azure Resource Graph Explorerrel](../governance/resource-graph/first-query-portal.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6,7: nem jóváhagyott Azure-erőforrások és szoftveralkalmazások eltávolítása

**Útmutató**: távolítsa el a Azure Security Center kapcsolódó Azure-erőforrásokat, ha már nincs szükségük a szervezet leltározási és felülvizsgálati folyamatának részeként.

- [Azure-erőforráscsoport és erőforrás-törlés](../azure-resource-manager/management/delete-resource-group.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="69-use-only-approved-azure-services"></a>6,9: csak jóváhagyott Azure-szolgáltatások használata

**Útmutató**: a Azure Policy használatával korlátozásokat állíthat be az előfizetésekben létrehozható erőforrások típusára a következő beépített szabályzat-definíciók használatával:

- Nem engedélyezett erőforrástípusok
- Engedélyezett erőforrástípusok

- [Az Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md)

- [Adott erőforrástípus megtagadása a következővel Azure Policy](../governance/policy/samples/index.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6,11: korlátozza a felhasználók képességét a Azure Resource Manager való interakcióra

**Útmutató**: az Azure feltételes hozzáférésének konfigurálása a felhasználók "Microsoft Azure felügyelet" alkalmazáshoz való hozzáférésének tiltása a Azure Resource Manager való interakcióra.

- [A feltételes hozzáférés konfigurálása a Azure Resource Managerhoz való hozzáférés blokkolásához](../role-based-access-control/conditional-access-azure-management.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

## <a name="secure-configuration"></a>Biztonságos konfiguráció

*További információt az [Azure biztonsági teljesítményteszt: biztonságos konfiguráció](../security/benchmarks/security-control-secure-configuration.md)című témakörben talál.*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7,1: biztonságos konfigurációk létrehozása az összes Azure-erőforráshoz

**Útmutató**: a Azure Security Center és a csatlakoztatott munkaterület szabványos biztonsági konfigurációinak meghatározása és implementálása Azure Policy használatával. Használjon Azure Policy aliasokat a "Microsoft. OperationalInsights" és a "Microsoft. Security" névterekben, hogy egyéni Azure Policy-definíciókat hozzon létre a Security Center és az Log Analytics munkaterület konfigurációjának naplózásához vagy érvényesítéséhez.

- [Az elérhető Azure Policy aliasok megtekintése](/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0)

- [Az Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="73-maintain-secure-azure-resource-configurations"></a>7,3: biztonságos Azure-erőforrás-konfigurációk karbantartása

**Útmutató**: az Azure-erőforrások biztonságos beállításainak érvénybe léptetéséhez használja a "megtagadás" és "üzembe helyezés, ha nem létezik" Azure Policy hatásait. Emellett Azure Resource Manager-sablonokkal is megőrizheti a szervezete által igényelt Azure-erőforrások biztonsági konfigurációját. 

- [Azure Policy effektusok ismertetése](../governance/policy/concepts/effects.md) 

- [Szabályzatok létrehozása és kezelése a megfelelőség kikényszerítése céljából](../governance/policy/tutorials/create-and-manage.md) 

- [Azure Resource Manager sablonok áttekintése](../azure-resource-manager/templates/overview.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="75-securely-store-configuration-of-azure-resources"></a>7,5: az Azure-erőforrások biztonságos tárolása

**Útmutató**: az Azure DevOps használatával biztonságosan tárolhatók és kezelhetők a kódok, például az egyéni Azure Policy-definíciók, Azure Resource Manager sablonok és a kívánt állapotú konfigurációs parancsfájlok. Az Azure DevOps felügyelt erőforrásainak eléréséhez engedélyeket adhat meg vagy tagadhat meg bizonyos felhasználók, beépített biztonsági csoportok vagy Azure Active Directory (Azure AD) által meghatározott csoportok számára, ha az integrálva van az Azure DevOps, vagy Active Directory, ha a TFS integrálva van. 

- [Kód tárolása az Azure DevOps](/azure/devops/repos/git/gitworkflow?view=azure-devops) 

- [Engedélyek és csoportok az Azure DevOps](/azure/devops/organizations/security/about-permissions)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7,7: az Azure-erőforrások konfigurációs felügyeleti eszközeinek üzembe helyezése

**Útmutató**: az Azure-erőforrások szabványos biztonsági konfigurációinak definiálása és implementálása Azure Policy használatával. Azure Policy-Aliasok használatával egyéni házirendeket hozhat létre a Azure Security Center kapcsolódó erőforrások konfigurálásának naplózásához vagy érvényesítéséhez. Emellett a Azure Automation használatával is telepítheti a konfigurációs módosításokat. 

- [Az Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md) 

- [Aliasok használata](../governance/policy/concepts/definition-structure.md#aliases)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7,9: automatikus konfigurációs monitorozás megvalósítása Azure-erőforrásokhoz

**Útmutató**: a beépített Azure Policy-definíciók, valamint a "Microsoft. OperationalInsights" és a "microsoft" Azure Policy aliasok használata. Biztonsági "névterek egyéni szabályzatok létrehozásához a riasztáshoz, a naplózáshoz és az Azure-erőforrás-konfigurációk érvényesítéséhez. Használja az Azure Policy Effects "audit", "megtagadás" és "telepítés ha nem létezik" használatát az Azure-erőforrások konfigurációjának automatikus érvényesítéséhez.

- [Az Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="711-manage-azure-secrets-securely"></a>7,11: az Azure-titkok biztonságos kezelése

**Útmutató**: a Azure Security Center egy konfigurált log Analytics munkaterületet használ az általa generált adatkezelési, riasztások és javaslatok tárolásához. Konfiguráljon egy ügyfél által felügyelt kulcsot (CMK) a Security Center adatgyűjtéshez konfigurált munkaterülethez. A CMK lehetővé teszi, hogy a munkaterületre mentett vagy elküldhető összes adatfájlt egy Ön által létrehozott és birtokolt Azure Key Vault kulccsal titkosítsa. 

- [Azure Monitor – ügyfél által kezelt kulcs](../azure-monitor/logs/customer-managed-keys.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="713-eliminate-unintended-credential-exposure"></a>7,13: a hitelesítő adatok nem szándékolt expozíciójának megszüntetése

**Útmutató**: hitelesítő adatok beolvasása a programkódon belül a hitelesítő adatok azonosításához. A Credential Scanner a felfedezett hitelesítő adatok biztonságosabb helyre, például az Azure Key Vaultba való áthelyezésére is javaslatot tesz.

- [A hitelesítő adatok beolvasójának beállítása](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

## <a name="malware-defense"></a>Kártevők elleni védelem

*További információt az [Azure biztonsági teljesítményteszt: kártevők elleni védelem](../security/benchmarks/security-control-malware-defense.md)című témakörben talál.*

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8,2: a nem számítási Azure-erőforrásokra feltöltött fájlok előzetes vizsgálata

**Útmutató**: a Azure Security Center nem a fájlok tárolására vagy feldolgozására szolgál. Az Ön felelőssége, hogy előzetesen beolvassa a nem számítási Azure-erőforrásokra feltöltött tartalmakat, beleértve a Log Analytics munkaterületet is.

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

## <a name="data-recovery"></a>Adat-helyreállítás

*További információt az [Azure biztonsági teljesítményteszt: adat-helyreállítás](../security/benchmarks/security-control-data-recovery.md)című témakörben talál.*

### <a name="91-ensure-regular-automated-back-ups"></a>9,1: rendszeres automatizált biztonsági másolatok biztosítása 

**Útmutató**: kövesse az infrastruktúra kód (IAC) megközelítését, és a Azure Resource Manager használatával telepítse a Azure Security Center kapcsolódó erőforrásokat egy JavaScript Object Notation-(JSON-) sablonba, amely az erőforrásokkal kapcsolatos konfigurációk biztonsági mentésére használható.

- [Egy-és többerőforrásos exportálás Azure Portal sablonba](../azure-resource-manager/templates/export-template-portal.md)

- [Biztonsági erőforráshoz Azure Resource Manager sablonok](/azure/templates/microsoft.security/allversions)

- [Tudnivalók Azure Automation](../automation/automation-intro.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: teljes rendszerbiztonsági mentés és minden ügyfél által felügyelt kulcs biztonsági mentése

**Útmutató**: a Azure Security Center egy log Analytics munkaterületet használ az általa létrehozott adatkezelési, riasztások és javaslatok tárolásához. Konfigurálhatja Azure Monitor és azt a munkaterületet, amelyet a Security Center használ az ügyfél által felügyelt kulcs engedélyezéséhez. Ha Key Vault használ az ügyfél által felügyelt kulcsok tárolására, gondoskodjon a kulcsok rendszeres automatikus biztonsági mentéséről.

- [Key Vault kulcsok biztonsági mentése](/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: az összes biztonsági másolat ellenőrzése, beleértve az ügyfél által felügyelt kulcsokat

**Útmutató**: a helyreállítás rendszeres végrehajtásának lehetősége Azure Resource Manager támogatott sablonfájlok használatával. Tesztelje az ügyfél által felügyelt kulcsok biztonsági mentésének visszaállítását.

- [Log Analytics munkaterület kezelése Azure Resource Manager sablonok használatával](../azure-monitor/logs/resource-manager-workspace.md)

- [Key Vault-kulcsok visszaállítása az Azure-ban](/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: a biztonsági másolatok és az ügyfél által felügyelt kulcsok védelmének biztosítása

**Útmutató**: az Azure DevOps használatával biztonságosan tárolhatók és kezelhetők a kódok, például az egyéni Azure Policy-definíciók és a Azure Resource Manager-sablonok. Az Azure DevOps felügyelt erőforrások védelme érdekében engedélyeket adhat meg vagy tagadhat meg bizonyos felhasználók, beépített biztonsági csoportok vagy Azure Active Directory (Azure AD) által meghatározott csoportok számára, ha az integrálva van az Azure DevOps, vagy Active Directory, ha a TFS integrálva van. Az Azure szerepköralapú hozzáférés-vezérlés használatával biztosíthatja az ügyfél által felügyelt kulcsok használatát.

Emellett engedélyezze a Soft-Delete és a védelem kiürítését Key Vault a kulcsok véletlen vagy rosszindulatú Törlés elleni védelme érdekében.  Ha az Azure Storage Azure Resource Manager sablonok biztonsági mentéseit tárolja, engedélyezze a Soft delete-et az adatok mentéséhez és helyreállításához, amikor blobokat vagy blob-pillanatképeket törölnek. 

- [Kód tárolása az Azure DevOps](/azure/devops/repos/git/gitworkflow?view=azure-devops)

- [Engedélyek és csoportok az Azure DevOps](/azure/devops/organizations/security/about-permissions)

- [A Soft-Delete engedélyezése és a védelem kiürítése Key Vault](../storage/blobs/soft-delete-blob-overview.md?tabs=azure-portal) 

- [Az Azure Storage-blobok helyreállítható törlése](../storage/blobs/soft-delete-blob-overview.md?tabs=azure-portal)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

## <a name="incident-response"></a>Incidensmegoldás

*További információt az [Azure biztonsági teljesítményteszt: incidens válasza](../security/benchmarks/security-control-incident-response.md)című témakörben talál.*

### <a name="101-create-an-incident-response-guide"></a>10,1: incidens-válaszi útmutató létrehozása

**Útmutató**: incidensek kifejlesztése útmutató a szervezet számára. Győződjön meg arról, hogy van olyan írásos incidens-válasz, amely meghatározza a személyzet összes szerepkörét, valamint az incidensek kezelésének és kezelésének fázisait az incidensek vizsgálatát követően. 

- [Útmutató a saját biztonsági incidensek megoldási folyamatának létrehozásához](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/) 

- [Microsoft Security Response Center – incidens anatómiája](https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/) 

- [A NIST számítógépes biztonsági incidensek kezelésével kapcsolatos útmutató a saját incidens-válasz tervének létrehozásához](https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10,2: incidensek pontozásának és rangsorolási eljárásának létrehozása

**Útmutató**: a Azure Security Center súlyosságot rendel az egyes riasztásokhoz, hogy a prioritások alapján ki lehessen deríteni, hogy mely riasztásokat kell először megvizsgálni. A súlyosság azon alapul, hogy az Security Center milyen mértékben szerepel a riasztás kibocsátásához használt elemzésben vagy elemzésben, valamint azt a megbízhatósági szintet, amely a riasztáshoz vezető tevékenység mögött rosszindulatú szándékú volt.

Emellett megadhatja a címkéket használó előfizetéseket, és létrehozhat egy elnevezési rendszert az Azure-erőforrások azonosításához és kategorizálásához, különösen a bizalmas adatok feldolgozásához.  Az Ön felelőssége, hogy rangsorolja a riasztások szervizelését az Azure-erőforrások és-környezet kritikus jellemzői alapján, ahol az incidens történt. 

- [Biztonsági riasztások az Azure Security Centerben](security-center-alerts-overview.md) 

- [Címkék használata az Azure-erőforrások rendszerezéséhez](../azure-resource-manager/management/tag-resources.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="103-test-security-response-procedures"></a>10,3: biztonsági reagálási eljárások tesztelése

**Útmutató**: az Azure-erőforrások védelmének biztosítása érdekében a rendszer az incidensek reagálási képességeinek rendszeres tesztelésére szolgáló gyakorlatokat hajt végre. Azonosítsa a gyenge pontokat és a hézagokat, majd szükség szerint módosítsa a választ. 

- [A NIST kiadványa – útmutató az IT-csomagok és-képességek teszteléséhez, betanításához és gyakorlatához](https://csrc.nist.gov/publications/detail/sp/800-84/final)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10,4: biztonsági incidensek elérhetőségének biztosítása és riasztási értesítések konfigurálása biztonsági incidensekhez

**Útmutató**: a Microsoft a biztonsági incidensek elérhetőségi adatait arra használja fel, hogy felvegye Önnel a kapcsolatot, ha a Microsoft Security Response Center (MSRC) felfedi, hogy az adatokat egy törvénytelen vagy jogosulatlan fél is hozzáférte. A problémák megoldása érdekében tekintse át az incidenseket a tény után. 

- [Az Azure Security Center biztonsági kapcsolattartójának beállítása](security-center-provide-security-contact-details.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10,5: biztonsági riasztások beépítése az incidensek gyorsreagáló rendszerébe

**Útmutató**: az Azure Security Center-riasztások és javaslatok exportálása a folyamatos exportálás funkcióval az Azure-erőforrásokkal kapcsolatos kockázatok azonosítása érdekében. A folyamatos exportálás lehetővé teszi a riasztások és javaslatok manuális és folyamatos exportálását. Az Azure Security Center adatösszekötővel továbbíthatja a riasztásokat az Azure Sentinel szolgáltatásba. 

- [Folyamatos exportálás konfigurálása](continuous-export.md) 

- [Riasztások streamelése az Azure Sentinelbe](../sentinel/connect-azure-security-center.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="106-automate-the-response-to-security-alerts"></a>10,6: a biztonsági riasztásokra adott válasz automatizálása

**Útmutató**: a munkafolyamat-automatizálási szolgáltatás használata Azure Security Center a biztonsági riasztásokra és az Azure-erőforrások védelmére vonatkozó ajánlásokra adott válaszok automatikus elindítására. 

- [A Munkafolyamat-automatizálás konfigurálása a Security Centerban](workflow-automation.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

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