---
title: Site Recovery Azure biztonsági alapterve
description: A Site Recovery biztonsági alapterve az Azure biztonsági Teljesítménytesztben meghatározott biztonsági javaslatok megvalósítására szolgáló eljárási útmutatást és erőforrásokat biztosít.
author: msmbaldwin
ms.service: site-recovery
ms.topic: conceptual
ms.date: 10/26/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: bf91c0a6e533acb326fe815c3e3c1088c959a603
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "100576732"
---
# <a name="azure-security-baseline-for-site-recovery"></a>Site Recovery Azure biztonsági alapterve

Ez a biztonsági alapkonfiguráció az [Azure biztonsági teljesítményteszt 1,0-es verziójáról](../security/benchmarks/overview-v1.md) site Recoveryre vonatkozó útmutatást alkalmaz. Az Azure Security Benchmark ajánlásokat ad arra nézve, hogy hogyan tehetők biztonságossá a felhőalapú megoldások az Azure-ban. A tartalom az Azure biztonsági teljesítményteszt által meghatározott **biztonsági vezérlők** szerint van csoportosítva, valamint a site Recovery vonatkozó kapcsolódó útmutatás. A Site Recovery nem alkalmazható **vezérlők** ki vannak zárva. 

Ha szeretné megtekinteni, hogyan Site Recovery teljes mértékben leképezni az Azure biztonsági Teljesítménytesztét, tekintse meg a [teljes site Recovery biztonsági alapterv-leképezési fájlt](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="network-security"></a>Hálózati biztonság

*További információ: [Azure Security Benchmark: Hálózati biztonság](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1,1: Azure-erőforrások biztosítása virtuális hálózatokon belül

**Útmutató**: a Microsoft Azure site Recovery nem támogatja az Azure Virtual Network történő üzembe helyezést. Konfigurálja Site Recovery szolgáltatást egy Azure Private-végponttal a biztonságos kommunikáció kikényszerített hálózatán.

- [Azure Site Recovery privát hivatkozás támogatása](azure-to-azure-how-to-enable-replication-private-endpoints.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1,8: a hálózati biztonsági szabályok bonyolultságának és adminisztratív terhelésének csökkentése

**Útmutató**: site Recovery szolgáltatás támogatja a szolgáltatási címkéket, amelyek lehetővé teszik, hogy az ügyfelek csak bizonyos szolgáltatásokra és portokra nyissák meg a forgalmat. Az ügyfeleknek engedélyezniük kell a "AzureSiteRecovery" szolgáltatási címkét a tűzfalon vagy a hálózati biztonsági csoportban a Site Recovery szolgáltatáshoz való kimenő hozzáférés engedélyezéséhez.

- [Kimenő kapcsolat szolgáltatás-címkék használatával](azure-to-azure-about-networking.md#outbound-connectivity-using-service-tags)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="110-document-traffic-configuration-rules"></a>1,10: a dokumentum forgalmának konfigurációs szabályai

**Útmutató**: használjon erőforrás-címkéket a hálózati biztonsági csoportokhoz és a hálózati biztonsággal és a forgalommal kapcsolatos egyéb erőforrásokhoz. A hálózati biztonsági csoportok egyedi szabályaihoz a "Leírás" mező használatával dokumentálhatja a hálózatra irányuló és onnan érkező forgalmat engedélyező szabályokat. 

A címkézéssel kapcsolatos beépített Azure Policy-definíciók bármelyikét beépítheti, például a "címke és az érték megkövetelése" beállítással biztosíthatja, hogy minden erőforrást címkékkel hozzon létre, és értesítse a meglévő címkézetlen erőforrásokról. 

A Azure PowerShell vagy az Azure CLI használatával a címkék alapján kereshet vagy végezhet műveleteket az erőforrásokon. 

- [Címkék létrehozása és használata](../azure-resource-manager/management/tag-resources.md) 

- [Azure-Virtual Network létrehozása](../virtual-network/quick-create-portal.md) 

- [Hálózati forgalom szűrése hálózati biztonsági csoport szabályaival](../virtual-network/tutorial-filter-network-traffic.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1,11: automatikus eszközök használata a hálózati erőforrások konfigurációjának figyelésére és a változások észlelésére

**Útmutató**: a site Recovery szolgáltatáshoz kapcsolódó hálózati erőforrás-konfigurációk változásainak figyelése az Azure-tevékenység naplófájljainak használatával. Riasztásokat hozhat létre a Azure Monitorban, hogy értesítse a kritikus Site Recovery hálózati erőforrások megváltozásakor.

- [Azure-Tevékenységnaplók eseményeinek megtekintése és lekérése](../azure-monitor/essentials/activity-log.md#view-the-activity-log)

- [Műveletnapló-riasztások létrehozása, megtekintése és kezelése Azure Monitor használatával](../azure-monitor/alerts/alerts-activity-log.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

## <a name="logging-and-monitoring"></a>Naplózás és monitorozás

*További információt az [Azure biztonsági teljesítményteszt: naplózás és figyelés](../security/benchmarks/security-control-logging-monitoring.md)című témakörben talál.*

### <a name="22-configure-central-security-log-management"></a>2,2: a központi biztonsági naplók felügyeletének konfigurálása

**Útmutató**: az Azure-Tevékenységnaplók diagnosztikai beállításainak engedélyezése a naplózáshoz és a naplók elküldése egy log Analytics-munkaterületre, Azure Storage-fiókba vagy Azure Event hub archiválásra.

Az Azure-tevékenység naplójának adatai segítségével meghatározhatja az Azure-erőforrásokon végrehajtott írási műveletek (PUT, közzététel, törlés) esetében a "mi, ki és mikor" kifejezést.

Site Recovery-naplók betöltése Azure Monitor a generált biztonsági adatokat összesítve. Azure Monitoron belül Log Analytics munkaterületek használatával kérdezheti le és végezheti el az elemzéseket, és használhat tárolási fiókokat a hosszú távú vagy archiválási tároláshoz. Emellett engedélyezheti és elvégezheti az Azure Sentinel vagy egy harmadik féltől származó biztonsági incidensek és események kezelése (SIEM) megoldásait.

- [Diagnosztikai beállítások engedélyezése az Azure-beli tevékenység naplójában](../azure-monitor/essentials/activity-log.md)

- [A Site Recovery monitorozása az Azure Monitor naplóival](monitor-log-analytics.md)

- [Az Azure Sentinel előkészítése](../sentinel/quickstart-onboard.md)

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="23-enable-audit-logging-for-azure-resources"></a>2,3: az Azure-erőforrások naplózásának engedélyezése

**Útmutató**: az Azure-Tevékenységnaplók diagnosztikai beállításainak engedélyezése a naplózáshoz és a naplók elküldése egy log Analytics-munkaterületre, Azure Storage-fiókba vagy Azure Event hub archiválásra. 

Az Azure-tevékenység naplójának adatai segítségével meghatározhatja az Azure-erőforrásokon végrehajtott írási műveletek (PUT, közzététel, törlés) esetében a "mi, ki és mikor" kifejezést.

Betöltheti a Site Recovery naplókat, és Azure Monitor a generált biztonsági adatokat összesítve. A Azure Monitoron belül Log Analytics munkaterületek használatával kérdezheti le és végezheti el az elemzéseket, és használhat tárolási fiókokat a hosszú távú/archiválási tároláshoz. Az Azure Sentinel vagy egy harmadik féltől származó biztonsági incidens-és eseménykezelő (SIEM) megoldás engedélyezése és a fedélzeten való ellátása.

- [Diagnosztikai beállítások engedélyezése az Azure-beli tevékenység naplójában](../azure-monitor/essentials/activity-log.md)

- [A Site Recovery monitorozása az Azure Monitor naplóival](monitor-log-analytics.md)

- [Az Azure Sentinel előkészítése](../sentinel/quickstart-onboard.md)

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="25-configure-security-log-storage-retention"></a>2,5: a biztonsági napló tárolási adatmegőrzésének konfigurálása

**Útmutató**: az Azure Recovery Services-tárolóhoz társított log Analytics-munkaterületek naplózási megőrzési időtartamának beállítása a szervezet megfelelőségi előírásai szerint Azure monitor használatával. 

- [Napló-megőrzési paraméterek beállítása](../azure-monitor/logs/manage-cost-storage.md#change-the-data-retention-period)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="26-monitor-and-review-logs"></a>2,6: naplók figyelése és áttekintése

**Útmutató**: az Azure-műveletnapló diagnosztikai beállításainak engedélyezése és a naplók elküldése egy log Analytics munkaterületre. 

Log Analytics lekérdezéseket végezhet a kifejezések kereséséhez, azonosíthatja a trendeket, elemezheti a mintákat, és bepillantást nyerhet a Recovery Services-tárolóból összegyűjtött tevékenységi napló adataira.

- [A Site Recovery monitorozása](site-recovery-monitor-and-troubleshoot.md)

- [Diagnosztikai beállítások engedélyezése az Azure-beli tevékenység naplójában](../azure-monitor/essentials/activity-log.md)

- [Azure-Tevékenységnaplók összegyűjtése és elemzése Log Analytics munkaterületen Azure Monitor](../azure-monitor/essentials/activity-log.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="27-enable-alerts-for-anomalous-activities"></a>2,7: riasztások engedélyezése rendellenes tevékenységekhez

**Útmutató**: a Azure site Recovery által replikált gépek figyelése Azure monitor naplók és log Analytics használatával. A naplózási lekérdezések írásához és teszteléséhez, valamint a naplófájlok interaktív elemzéséhez használja a Log Analytics Azure Monitoron belül. Azure Monitor gyűjti a tevékenység-és erőforrás-naplókat, valamint az egyéb figyelési adatokat. 

Megjelenítheti és lekérdezheti a napló eredményeit, és konfigurálhatja a riasztásokat, hogy műveleteket lehessen végrehajtani a figyelt adatmennyiség alapján. Riasztásokat állíthat be egy Log Analytics munkaterületről az Azure Sentinel szolgáltatásba, mivel ez egy biztonsági előkészítési automatizált választ (felszárnyaló) megoldást biztosít. Ez lehetővé teszi az automatizált megoldások, például a forgatókönyvek létrehozását és a biztonsági problémák megoldására való felhasználását. Hozzon létre egyéni napló-riasztásokat a Log Analytics munkaterületen a Azure Monitor használatával. 

- [A Site Recovery monitorozása](site-recovery-monitor-and-troubleshoot.md)

- [Az Azure Sentinel előkészítése](../sentinel/quickstart-onboard.md)

- [Naplózási riasztások létrehozása, megtekintése és kezelése Azure Monitor használatával](../azure-monitor/alerts/alerts-log.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

## <a name="identity-and-access-control"></a>Identitás- és hozzáférés-vezérlés

*További információt az [Azure biztonsági teljesítményteszt: Identity and Access Control](../security/benchmarks/security-control-identity-access-control.md)című témakörben talál.*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3,1: a felügyeleti fiókok leltárának karbantartása

**Útmutató**: alapértelmezés szerint egyetlen szerepkör sincs hozzárendelve. Az üzleti igények alapján explicit módon kell hozzárendelni őket. A szerepkör-hozzárendelések a PowerShell CLI-vel vagy Azure Active Directory (Azure AD) ellenőrizhetők a felügyeleti csoportok tagjainak fiókjainak felderítéséhez.

- [Címtárbeli szerepkör beszerzése az Azure AD-ben a PowerShell-lel](/powershell/module/azuread/get-azureaddirectoryrole?preserve-view=true&view=azureadps-2.0)

- [Címtárbeli szerepkör tagjainak beszerzése az Azure AD-ben a PowerShell-lel](/powershell/module/azuread/get-azureaddirectoryrolemember?preserve-view=true&view=azureadps-2.0)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="33-use-dedicated-administrative-accounts"></a>3,3: dedikált rendszergazdai fiókok használata

**Útmutató**: szabványos üzemeltetési eljárások létrehozása a dedikált rendszergazdai fiókok használata körül. A rendszergazdai fiókok számának figyeléséhez használja a Security Center identitás-és hozzáférés-kezelési funkcióit.

Emellett a dedikált rendszergazdai fiókok nyomon követésének elősegítése érdekében Security Center vagy beépített Azure-szabályzatokból származó javaslatokat is használhat, például: 

- Az előfizetéshez egynél több tulajdonos rendelhető hozzá 
- A tulajdonosi engedélyekkel rendelkező elavult fiókokat el kell távolítani az előfizetésből 

- A tulajdonosi engedélyekkel rendelkező külső fiókokat el kell távolítani az előfizetésből

Hozzon létre egy folyamatot, amely nyomon követheti a rendszergazdai fiókok identitás-és hozzáférés-vezérlését, és rendszeres időközönként áttekintheti.

- [A Azure Security Center használata az identitás és a hozzáférés figyeléséhez](../security-center/security-center-identity-access.md)

- [A Azure Policy használata](../governance/policy/tutorials/create-and-manage.md)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3,4: egyszeri bejelentkezés (SSO) használata Azure Active Directory

**Útmutató**: az Azure-alkalmazás regisztrációjának használata egy egyszerű szolgáltatással, amely az API-hívásokon keresztül a Recovery Services-tárolókkal való interakcióhoz használható jogkivonatot kér le.

- [Az Azure REST API-k meghívása](/rest/api/azure/#how-to-call-azure-rest-apis-with-postman)

- [Az ügyfélalkalmazás (egyszerű szolgáltatásnév) regisztrálása az Azure AD-vel](/rest/api/azure/#register-your-client-application-with-azure-ad)

- [Azure Recovery Services API-információk](/rest/api/recoveryservices)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3,5: Multi-Factor Authentication használata az összes Azure Active Directory-alapú hozzáféréshez

**Útmutató**: az Azure ad és a többtényezős hitelesítés engedélyezése, valamint a Security Center identitás-és hozzáférési javaslatainak követése. 
- [Azure AD Multi-Factor Authentication üzemelő példány megtervezése](../active-directory/authentication/howto-mfa-getstarted.md)

- [Identitás és hozzáférés monitorozása](../security-center/security-center-identity-access.md)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3,6: dedikált gépek (privilegizált hozzáférési munkaállomások) használata az összes felügyeleti feladathoz

**Útmutató**: biztonságos, Azure által felügyelt munkaállomás (más néven privilegizált hozzáférési munkaállomás (Paw)) használata az Azure többtényezős hitelesítéssel a felügyeleti feladatokhoz és a jogosultsági szintű műveletek végrehajtásához site Recovery erőforrásokon.

- [Emelt hozzáférési szintű munkaállomások](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

- [Felhőalapú Azure AD Multi-Factor Authentication-telepítés megtervezése](../active-directory/authentication/howto-mfa-getstarted.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3,7: naplózás és riasztás a gyanús tevékenységekről a rendszergazdai fiókoktól

**Útmutató**: az Azure ad PRIVILEGED Identity Management (PIM) funkciójának használata naplók és riasztások generálásához, ha a környezetben gyanús vagy nem biztonságos tevékenység történik.
A kockázatos felhasználói viselkedéssel kapcsolatos riasztások és jelentések megtekintése az Azure AD kockázati észlelési funkciójával.

- [Privileged Identity Management (PIM) üzembe helyezése](../active-directory/privileged-identity-management/pim-deployment-plan.md)

- [Az Azure AD kockázati észlelések ismertetése](../active-directory/identity-protection/overview-identity-protection.md)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="38-manage-azure-resources-only-from-approved-locations"></a>3,8: csak a jóváhagyott helyekről származó Azure-erőforrások kezelése

**Útmutató**: a feltételes hozzáférés elnevezett helyeivel engedélyezheti a hozzáférést a Azure Portalhoz az IP-címtartományok, régiók vagy országok meghatározott logikai csoportjaiból.
- [Elnevezett helyszínek konfigurálása az Azure-ban](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="39-use-azure-active-directory"></a>3,9: a Azure Active Directory használata

**Útmutató**: az Azure ad használata központi hitelesítési és engedélyezési rendszerként site Recovery számára. Az Azure AD az adatok védelme érdekében erős titkosítást használ a REST-alapú adatokhoz, az átvitelhez, valamint a felhasználók hitelesítő adatainak és a sók, a kivonatok és a biztonságos tárolásához is. 

- [Azure AD-példány létrehozása és konfigurálása](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="310-regularly-review-and-reconcile-user-access"></a>3,10: a felhasználói hozzáférés rendszeres áttekintése és egyeztetése

**Útmutató**: az elavult fiókok felderítésének elősegítése az Azure ad-naplók használatával. 

A csoporttagságok hatékony kezelése, a vállalati alkalmazások és szerepkör-hozzárendelések elérése az Azure AD identitás-és hozzáférési felülvizsgálatával. 

Hozzon létre egy folyamatot, amely rendszeres időközönként ellenőrzi a felhasználók hozzáférését, hogy csak a befejezett hozzáférési felülvizsgálatokkal rendelkező felhasználók férhessenek hozzá. 

- [Az Azure AD jelentéskészítés ismertetése](../active-directory/reports-monitoring/index.yml)

- [Az Azure Identity hozzáférési felülvizsgálatok használata](../active-directory/governance/access-reviews-overview.md)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3,11: a figyelő megkísérli a deaktivált hitelesítő adatok elérését

**Útmutató**: az Azure ad használata központi hitelesítési és engedélyezési rendszerként site Recovery erőforrásokhoz. Az Azure AD az adatok védelme érdekében erős titkosítást használ a nyugalmi és a továbbítási adatokhoz, valamint a felhasználók hitelesítő adatainak tárolásához és kiszűréséhez is.

Hozzáférése van az Azure AD bejelentkezési tevékenységekhez, a naplózási és a kockázati Eseménynapló-forrásokhoz, amelyek lehetővé teszik az Azure Sentinel vagy az Azure Marketplace-en elérhető bármely SIEM vagy monitoring eszköz integrálását.

A folyamat további egyszerűsítéséhez hozzon létre diagnosztikai beállításokat az Azure AD felhasználói fiókjaihoz, és küldje el a naplózási és bejelentkezési naplókat egy Log Analytics munkaterületre. Log Analytics munkaterületen belül konfigurálhatja a kívánt riasztásokat.

- [Azure-tevékenységnaplók integrálása az Azure Monitorba](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

- [A fedélzeti Azure Sentinel ismertetése](../sentinel/quickstart-onboard.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="312-alert-on-account-login-behavior-deviation"></a>3,12: riasztás a fiók bejelentkezési viselkedésének eltérése esetén

**Útmutató**: az Azure ad használata központi hitelesítési és engedélyezési rendszerként a Recovery Services-tárolók számára. 

Az Azure AD Identity Protection-funkcióinak alkalmazása a fiók bejelentkezési viselkedésének észleléséhez és az észlelt gyanús műveletekre vonatkozó automatikus válaszok konfigurálásához a felhasználói identitásokhoz kapcsolódóan. További vizsgálat céljából az Azure Sentinelbe is betöltheti az adatmennyiséget.

- [Az Azure AD kockázatos bejelentkezésének megtekintése](../active-directory/identity-protection/overview-identity-protection.md)

- [Az Identity Protection kockázati házirendjeinek konfigurálása és engedélyezése](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [Az Azure Sentinel előkészítése](../sentinel/quickstart-onboard.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

## <a name="data-protection"></a>Adatvédelem

*További információ: [Azure Security Benchmark: Adatvédelem](../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4,1: bizalmas információk leltárának fenntartása

**Útmutató**: a címkék használatával segítheti a bizalmas adatokat tároló vagy feldolgozó Azure-erőforrások nyomon követését.

- [Címkék létrehozása és használata](../azure-resource-manager/management/tag-resources.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4,2: bizalmas adatok tárolására vagy feldolgozására szolgáló rendszerek elkülönítése

**Útmutató**: különálló előfizetések vagy felügyeleti csoportok implementálása fejlesztési, tesztelési és éles Recovery Services-tárolók számára. A megfelelő módon címkézett vagy egy hálózati biztonsági csoport vagy Azure Firewall által védett erőforrások elkülönítése virtuális hálózattal vagy alhálózattal. 

Kapcsolja ki a virtuális gépeket, amelyek a nem használatban lévő bizalmas adatokat tárolják vagy dolgozzák fel. Alkalmazza a házirendeket és eljárásokat az ismétlődő folyamat elvégzéséhez. 

- [További Azure-előfizetések létrehozása](../cost-management-billing/manage/create-subscription.md)

- [Management Groups létrehozása](../governance/management-groups/create-management-group-portal.md)

- [A Site Recovery áttekintése](site-recovery-overview.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4,3: a bizalmas adatok jogosulatlan átvitelének figyelése és letiltása

**Útmutató**: magánhálózati vagy magánhálózati végpontok, hálózati biztonsági csoportok és szolgáltatási címkék használata az site Recovery-kompatibilis virtuális gépekről származó adatok kiszűrése kapcsolatos lehetőségek enyhítésére.

A Microsoft felügyeli az Site Recovery által használt platformot, és az összes vásárlói tartalmat bizalmasként kezeli, és védelmet biztosít az ügyfelek adatvesztése és a kitettség ellen. A Microsoft robusztus adatvédelmi szabályozást és képességeket hozott létre és tart fenn, amelyekkel biztosítható, hogy az ügyfelek az Azure-ban is biztonságosak maradjanak. 

- [Az ügyféladatok Azure-beli védelmének ismertetése](../security/fundamentals/protection-customer-data.md)

- [Virtuális gépek replikálása Azure Private-végpontokkal](azure-to-azure-how-to-enable-replication-private-endpoints.md)

- [Virtuális gépek replikálása Azure Site Recovery Service-címkékkel](azure-to-azure-about-networking.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Megosztott

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4,4: minden bizalmas adat titkosítása az átvitel során

**Útmutató**: a site Recovery egy biztonságos HTTPS-csatornát használ, amely Advanced Encryption Standard (AES 256) használatával titkosított, az Azure-beli munkaterhelés-kiszolgálóktól a Recovery Services-tároló mögött üzemeltetett site Recovery szolgáltatásokhoz.

A Site Recovery által támogatott jelenlegi TLS-verziók a TLS 1,0, a TLS 1,1, a TLS 1,2 a régiókban, amelyek a 2019 végén voltak élőak. A TLS 1.2 az egyetlen támogatott TLS-verzió minden új régióban.

- [A Azure Site Recovery-átvitel titkosításának ismertetése](physical-azure-set-up-source.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Megosztott

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4,5: aktív felderítési eszköz használata a bizalmas adatok azonosítására

**Útmutató**: az adatazonosítási, besorolási és veszteség-megelőzési funkciók még nem érhetők el site Recovery számára. 

Szükség esetén egy harmadik féltől származó megoldást is megvalósíthat megfelelőségi célokból.

A Microsoft felügyeli az Site Recovery által használt platformot, és az összes vásárlói tartalmat bizalmasként kezeli, és védelmet biztosít az ügyfelek adatvesztése és a kitettség ellen. A szolgáltatás implementálta és karbantartja a robusztus adatvédelmi szabályozást és képességeket, amelyekkel biztosítható, hogy az ügyfelek az Azure-ban is biztonságosak maradnak. 

- [Az ügyféladatok Azure-beli védelmének ismertetése](../security/fundamentals/protection-customer-data.md)

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="46-use-azure-rbac-to-manage-access-to-resources"></a>4,6: az erőforrásokhoz való hozzáférés kezelése az Azure RBAC

**Útmutató**: az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC) használatával kezelheti site Recovery erőforrásokhoz kapcsolódó adatforrásokhoz és erőforrásokhoz való hozzáférést. 

Elkülönítheti a munkafeladatokat az Azure RBAC, és megadhatja számukra a szükséges hozzáférést. A beépített Site Recovery szerepkörökkel szabályozhatja Site Recovery felügyeleti műveleteit.

- [Az Azure RBAC konfigurálása](../role-based-access-control/role-assignments-portal.md)

- [Role-Based Access Control kezelése Azure Site Recovery](site-recovery-role-based-linked-access-control.md)

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="48-encrypt-sensitive-information-at-rest"></a>4,8: bizalmas adatok titkosítása a nyugalmi állapotban

**Útmutató**: a kettős titkosítás engedélyezése a platformmal és az ügyfél által felügyelt kulcsokkal. Ez a funkció Site Recovery érhető el. 

Site Recovery támogatja az adatok titkosítását. Az Azure IaaS számítási feladataihoz az adatok titkosítva vannak a Storage Service Encryption (SSE) használatával. 

Az ügyfél által felügyelt kulccsal titkosított Recovery Services-tároló használata esetén csak az ügyfél férhet hozzá a titkosítási kulcshoz. A Microsoft soha nem tart fenn másolatot, nem rendelkezik hozzáféréssel a kulcshoz, és az elsődlegestől a vész-helyreállítási helyre továbbított adatok bármely pontján nem fejthető vissza. 

- [Az ügyfél által felügyelt kulcsok támogatása Azure Site Recovery](azure-to-azure-how-to-enable-replication-cmk-disks.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Megosztott

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4,9: a kritikus Azure-erőforrások változásainak naplózása és riasztása

**Útmutató**: a Azure monitor Azure-beli tevékenység-naplók használatával riasztásokat hozhat létre, amikor a módosítások a kritikus erőforrásokra vonatkoznak. Ezek az erőforrások lehetnek Recovery Services tárolók üzemi példányai, Site Recovery szolgáltatás erőforrásai és kapcsolódó erőforrások.
- [Riasztások létrehozása az Azure-tevékenységek naplózási eseményeihez](../azure-monitor/alerts/alerts-activity-log.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

## <a name="inventory-and-asset-management"></a>Leltár-és eszközfelügyelet

*További információt az [Azure biztonsági teljesítményteszt: leltár és eszközkezelés](../security/benchmarks/security-control-inventory-asset-management.md)című témakörben talál.*

### <a name="61-use-automated-asset-discovery-solution"></a>6,1: automatikus eszköz-felderítési megoldás használata

**Útmutató**: az Azure Resource Graph használatával lekérdezheti vagy felderítheti az összes erőforrást, beleértve a Recovery Services-tárolókat is az előfizetései között. Ellenőrizze a megfelelő olvasási engedélyeket a bérlőben, és sorolja fel az összes Azure-előfizetést, valamint az előfizetésekben található erőforrásokat.

Bár a klasszikus Azure-erőforrások felderítése az erőforrás-gráfon keresztül lehetséges, erősen ajánlott a Azure Resource Manager erőforrások létrehozása és használata.

- [Lekérdezések létrehozása az Azure Graph használatával](../governance/resource-graph/first-query-portal.md)

- [Azure-előfizetések megtekintése](/powershell/module/az.accounts/get-azsubscription?preserve-view=true&view=azps-4.8.0)

- [Az Azure RBAC ismertetése](../role-based-access-control/overview.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="62-maintain-asset-metadata"></a>6,2: az eszköz metaadatainak fenntartása

**Útmutató**: címkéket alkalmazhat a Recovery Services-tárolók és más kapcsolódó erőforrások számára, amelyeket metaadatokkal site Recovery használ, hogy logikailag szervezze azokat a besorolásba.
- [Címkék létrehozása és használata](../azure-resource-manager/management/tag-resources.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="63-delete-unauthorized-azure-resources"></a>6,3: jogosulatlan Azure-erőforrások törlése

**Útmutató**: a címkézés, a felügyeleti csoportok és a különálló előfizetések használata, ha szükséges, a Site Recovery (Recovery Services-tárolók) és az egyéb kapcsolódó erőforrások rendszerezésére és nyomon követésére. 

Emellett a Azure Policy használatával korlátozásokat állíthat be az ügyfél-előfizetésekben létrehozható erőforrások típusára a következő beépített szabályzat-definíciók használatával: 

- Nem engedélyezett erőforrástípusok
- Engedélyezett erőforrástípusok

Rendszeres időközönként egyeztetheti a leltárt, és gondoskodhat arról, hogy a jogosulatlan erőforrások törlése az előfizetésből időben történjen.

- [További Azure-előfizetések létrehozása](../cost-management-billing/manage/create-subscription.md)

- [Management Groups létrehozása](../governance/management-groups/create-management-group-portal.md)

- [Címkék létrehozása és használata](../azure-resource-manager/management/tag-resources.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="64-define-and-maintain-an-inventory-of-approved-azure-resources"></a>6,4: jóváhagyott Azure-erőforrások leltárának meghatározása és karbantartása

**Útmutató**: a számítási erőforrásokhoz jóváhagyott Azure-erőforrások és jóváhagyott szoftverek leltárának létrehozása az ügyfél szervezeti követelményei alapján.

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="65-monitor-for-unapproved-azure-resources"></a>6,5: a nem jóváhagyott Azure-erőforrások figyelése

**Útmutató**: a Azure Policy használatával korlátozásokat állíthat be az ügyfél-előfizetésekben létrehozható erőforrások típusára a következő beépített szabályzat-definíciók használatával: 

- Nem engedélyezett erőforrástípusok 
- Engedélyezett erőforrástípusok

Az Azure Resource Graph segítségével lekérdezheti és felderítheti az előfizetéseken belüli erőforrásokat.

- [Az Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md)

- [Lekérdezések létrehozása az Azure Resource Graph használatával](../governance/resource-graph/first-query-portal.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="69-use-only-approved-azure-services"></a>6,9: csak jóváhagyott Azure-szolgáltatások használata

**Útmutató**: a Azure Policy használatával korlátozásokat állíthat be az ügyfél-előfizetésekben létrehozható erőforrások típusára a következő beépített szabályzat-definíciók használatával:

- Nem engedélyezett erőforrástípusok 
- Engedélyezett erőforrástípusok

Fontos megismernie, hogy hogyan hozhat létre és kezelhet szabályzatokat az Azure-ban annak érdekében, hogy megfeleljen a vállalati szabványoknak és a szolgáltatói szerződéseknek.

- [Az Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md)

- [Adott erőforrástípus megtagadása a következővel Azure Policy](../governance/policy/samples/index.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6,11: korlátozza a felhasználók képességét a Azure Resource Manager való interakcióra

**Útmutató**: az Azure feltételes hozzáférés használatával korlátozhatja a felhasználók a Azure Resource Manager interakcióját a "Microsoft Azure felügyelet" alkalmazás "hozzáférés tiltása" beállításával. Ez megakadályozhatja az erőforrások létrehozását és módosítását a magas biztonsági környezeten belül.

- [A feltételes hozzáférés konfigurálása a Azure Resource Managerhoz való hozzáférés blokkolásához](../role-based-access-control/conditional-access-azure-management.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

## <a name="secure-configuration"></a>Biztonságos konfiguráció

*További információt az [Azure biztonsági teljesítményteszt: biztonságos konfiguráció](../security/benchmarks/security-control-secure-configuration.md)című témakörben talál.*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7,1: biztonságos konfigurációk létrehozása az összes Azure-erőforráshoz

**Útmutató**: a Recovery Services-tár szabványos biztonsági konfigurációinak definiálása és implementálása Azure Policy használatával. 

A "Microsoft. Recoveryservices szolgáltatónál" névtérben Azure Policy Aliasok használatával egyéni szabályzatokat hozhat létre az Site Recovery szolgáltatás Recovery Services tár erőforrásainak naplózásához, illetve a konfiguráció érvényesítéséhez.
- [Az elérhető Azure Policy aliasok megtekintése](/powershell/module/az.resources/get-azpolicyalias?preserve-view=true&view=azps-4.8.0)

- [Az Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="73-maintain-secure-azure-resource-configurations"></a>7,3: biztonságos Azure-erőforrás-konfigurációk karbantartása

**Útmutató**: az Azure-erőforrások biztonságos beállításainak érvényesítéséhez használja a Azure Policy [deny] és a [telepítés ha nem létezik] hatásokat.
- [Azure Policy effektusok ismertetése](../governance/policy/concepts/effects.md)

- [Az Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="75-securely-store-configuration-of-azure-resources"></a>7,5: az Azure-erőforrások biztonságos tárolása

**Útmutató**: válassza az Azure reposs elemet a kód biztonságos tárolásához és kezeléséhez, ha egyéni Azure Policy-definíciókat használ a Recovery Services-tárolók és a kapcsolódó erőforrások számára.

- [Kód tárolása az Azure DevOps](/azure/devops/repos/git/gitworkflow?preserve-view=true&view=azure-devops)

- [Az Azure Repos dokumentációja](/azure/devops/repos/?preserve-view=true&view=azure-devops)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7,7: az Azure-erőforrások konfigurációs felügyeleti eszközeinek üzembe helyezése

**Útmutató**: a "Microsoft. recoveryservices szolgáltatónál" névtérben található beépített Azure Policy definíciók és Azure Policy Aliasok használatával egyéni szabályzatokat hozhat létre a riasztáshoz, a naplózáshoz és a rendszerkonfigurációk kényszerítéséhez. 

Emellett dolgozzon ki egy folyamatot és egy folyamatot a házirend-kivételek kezeléséhez.

- [Az Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7,9: automatikus konfigurációs monitorozás megvalósítása Azure-erőforrásokhoz

**Útmutató**: a "Microsoft. recoveryservices szolgáltatónál" névtérben található beépített Azure Policy definíciók és Azure Policy Aliasok használatával egyéni szabályzatokat hozhat létre a riasztáshoz, a naplózáshoz és a rendszerkonfigurációk kényszerítéséhez. 

Az Azure-erőforrások konfigurációinak automatikus érvényesítéséhez használja a Azure Policy [audit], [megtagadás] és [üzembe helyezés, ha nem léteznek] effektusokat.
- [Az Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="711-manage-azure-secrets-securely"></a>7,11: az Azure-titkok biztonságos kezelése

**Útmutató**: az ügyfélnek az Azure Key vaulttal integrált site Recovery-titkokat kell kezelnie, miközben a Azure Disk Encryption-kompatibilis virtuális gépek vész-helyreállítását is lehetővé teszi. 

- [Kulcstartó létrehozása](../key-vault/general/quick-create-portal.md)

- [Hitelesítés a Key vaultban](../key-vault/general/authentication.md)

- [Key Vault hozzáférési szabályzatának társítása](../key-vault/general/assign-access-policy-portal.md)

- [A DR Azure Disk Encryption-kompatibilis virtuális gépek engedélyezése Site Recovery használatával](azure-to-azure-how-to-enable-replication-ade-vms.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="712-manage-identities-securely-and-automatically"></a>7,12: az identitások biztonságos és automatikus kezelése

**Útmutató**: a site Recovery csak a rendszer által felügyelt identitást támogatja, ha az ügyfél engedélyezheti a rendszerfelügyelt identitást Recovery Services-tárolón. Ugyanez a módszer vonatkozik a vész-helyreállítási ajánlatban használt erőforrásokra, hogy meghatározza a hozzáférési határt. 

Felügyelt identitások használatával biztosíthatja az Azure-szolgáltatások automatikus felügyelt identitását az Azure AD-ben. 

A felügyelt identitások lehetővé teszik bármely olyan szolgáltatás hitelesítését, amely támogatja az Azure AD-hitelesítést, beleértve a Key Vault is, a kódban szereplő hitelesítő adatok nélkül.

- [Integráció az Azure felügyelt identitásokkal](../azure-app-configuration/howto-integrate-azure-managed-service-identity.md?tabs=core2x)

- [Felügyelt identitás engedélyezése Recovery Services-tárolón](azure-to-azure-how-to-enable-replication-private-endpoints.md#enable-the-managed-identity-for-the-vault)

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

**Útmutató**: a Microsoft antimalware engedélyezve van a mögöttes gazdagépen, amely támogatja az Azure-szolgáltatásokat (például site Recovery), de nem fut a tartalomon. A nem számítási Azure-erőforrásokra feltöltött fájlok előzetes vizsgálata, például App Service, Data Lake Storage és Blob Storage.

A Storage-fiókokba feltöltött kártevők észleléséhez használja a Security Center veszélyforrások észlelését az adatszolgáltatásokhoz.

- [Megismerheti a Microsoft antimalware-t az Azure Cloud Services és Virtual Machines](../security/fundamentals/antimalware.md)

- [Az adatszolgáltatások fenyegetés-észlelésének megismerése Azure Security Center](../security-center/azure-defender.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

## <a name="data-recovery"></a>Adat-helyreállítás

*További információt az [Azure biztonsági teljesítményteszt: adat-helyreállítás](../security/benchmarks/security-control-data-recovery.md)című témakörben talál.*

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: teljes rendszerbiztonsági mentés és minden ügyfél által felügyelt kulcs biztonsági mentése

**Útmutató**: site Recovery belsőleg egy Azure Storage-fiókot használ a vész-helyreállítási megoldás állapotának fenntartásához, amelyet az ügyfelek a számítási feladatokra konfiguráltak.

A Site Recovery Services metaadatainak által használt összes tárolási erőforrás a következő típusú konfigurációval: olvasási hozzáférés geo-redundáns tárolás (RA-GRS). A GRS fenti típusú Storage-fiókok (például a RAGRS, a RAG-ZRS) replikálják az adatait egy másodlagos régióba (több száz kilométerre a forrásadatok elsődleges helyétől), hogy továbbra is kiszolgálják az ügyfelek vész-helyreállítását az kimaradások során.

Ez az ügyfél hatókörén kívül esik, és a Site Recovery csapata belsőleg gondoskodik róla. Az ügyfél biztonsági mentést készíthet Key Vault kulcsokról az Azure-ban.

- [Key Vault-kulcsok biztonsági mentése az Azure-ban](/powershell/module/az.keyvault/backup-azkeyvaultkey?preserve-view=true&view=azps-4.8.0)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: az összes biztonsági másolat ellenőrzése, beleértve az ügyfél által felügyelt kulcsokat

**Útmutató**: az ügyfél által felügyelt kulcsok visszatárolásának rendszeres tesztelése.

- [Key Vault-kulcsok visszaállítása az Azure-ban](/powershell/module/az.keyvault/restore-azkeyvaultkey?preserve-view=true&view=azps-4.8.0)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: a biztonsági másolatok és az ügyfél által felügyelt kulcsok védelmének biztosítása

**Útmutató**: az adatok titkosítva vannak a Storage Service encryption (SSE) és az Azure infrastruktúra-szolgáltatás (IaaS) alapú Virtual Machines használatával. A kulcsok véletlen vagy rosszindulatú törléssel szembeni védelemének engedélyezéséhez engedélyezze a Key Vault törlését.

- [A Soft delete engedélyezése Key Vault](../storage/blobs/soft-delete-blob-overview.md?tabs=azure-portal)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

## <a name="incident-response"></a>Incidensmegoldás

*További információ: [Azure Security Benchmark: Incidensek kezelése](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10,1: incidens-válaszi útmutató létrehozása

**Útmutató**: Alakítson ki incidenskezelési útmutatót a vállalat számára. 

Győződjön meg arról, hogy van olyan írásos incidens-válasz, amely meghatározza a személyzet összes szerepkörét, valamint az incidensek kezelésének vagy kezelésének fázisait az incidensek vizsgálatát követően.

- [Munkafolyamat-automatizálás konfigurálása Azure Security Centeron belül](../security-center/security-center-planning-and-operations-guide.md)

- [Útmutató a saját biztonsági incidensek megoldási folyamatának létrehozásához](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process)

- [Microsoft Security Response Center – incidens anatómiája](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process)

- [Az ügyfél a NIST számítógépes biztonsági incidensek kezelési útmutatóját is kihasználhatja a saját incidens-válasz tervének létrehozásával kapcsolatos támogatáshoz](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10,2: incidensek pontozásának és rangsorolási eljárásának létrehozása

**Útmutató**: a riasztások fontosságának megállapítása a Security Center hozzárendelt riasztás súlyossága alapján. A súlyosság azon alapul, hogy az Security Center milyen mértékben szerepel a riasztás kibocsátásához használt elemzésben vagy elemzésben, valamint azt a megbízhatósági szintet, amely a riasztáshoz vezető tevékenység mögött rosszindulatú szándékú volt.

Az előfizetéseket egyértelműen megjelölheti (például éles környezetben, nem éles környezetben), és létrehozhat egy elnevezési rendszert az Azure-erőforrások egyértelmű azonosításához és kategorizálásához.

- [Biztonsági riasztások az Azure Security Centerben](../security-center/security-center-alerts-overview.md) 

- [Címkék használata az Azure-erőforrások rendszerezéséhez](../azure-resource-manager/management/tag-resources.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="103-test-security-response-procedures"></a>10,3: biztonsági reagálási eljárások tesztelése

**Útmutató**: a rendszerek incidensek reagálási képességeinek rendszeres tesztelésére szolgáló gyakorlatok végrehajtása. Azonosítsa a gyenge pontokat és a réseket, és szükség szerint módosítsa a tervet

- [Tekintse meg a NIST kiadványát, amely az IT-csomagokhoz és-képességekhez kapcsolódó programok tesztelését, betanítását és gyakorlatát ismerteti](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10,4: biztonsági incidensek elérhetőségének biztosítása és riasztási értesítések konfigurálása biztonsági incidensekhez

**Útmutató**: a Microsoft a biztonsági incidensek elérhetőségi adatait arra használja fel, hogy felvegye Önnel a kapcsolatot, ha a Microsoft Security Response Center (MSRC) felfedi, hogy az ügyfél adatait egy törvénytelen vagy jogosulatlan fél is hozzáférte. 

Hozzon létre egy folyamatot az incidensek áttekintéséhez, az események közzétételéhez, hogy a problémák megoldhatók legyenek.

- [A Azure Security Center biztonsági kapcsolattartó beállítása](../security-center/security-center-provide-security-contact-details.md)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10,5: biztonsági riasztások beépítése az incidensek gyorsreagáló rendszerébe

**Útmutató**: az Security Center-riasztások és-javaslatok exportálása a folyamatos exportálás funkció használatával. A folyamatos exportálás lehetővé teszi a riasztások és javaslatok manuális és folyamatos exportálását. 

Az Security Center adatösszekötővel szükség szerint továbbíthatja a riasztásokat az Azure Sentinel szolgáltatásba.
- [Folyamatos exportálás konfigurálása](../security-center/continuous-export.md)

- [Riasztások streamelése az Azure Sentinelbe](../sentinel/connect-azure-security-center.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="106-automate-the-response-to-security-alerts"></a>10,6: a biztonsági riasztásokra adott válasz automatizálása

**Útmutató**: a Security Center munkafolyamat-automatizálási funkciója segítségével automatikusan aktiválhatja a válaszokat a "Logic apps" használatával a biztonsági riasztások és javaslatok esetében.
- [A Munkafolyamat-automatizálás és a Logic Apps konfigurálása](../security-center/workflow-automation.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

## <a name="penetration-tests-and-red-team-exercises"></a>Behatolási tesztek és Red Team-gyakorlatok

*További információkért tekintse meg az [Azure biztonsági teljesítményteszt: behatolási tesztek és a Red Team gyakorlatok](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md)című témakört.*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11,1: az Azure-erőforrások rendszeres behatolásának tesztelése, valamint az összes kritikus biztonsági vizsgálat szervizelésének biztosítása

**Útmutató**: kövesse a Microsoft részvételi szabályait, hogy a behatolási tesztek ne sértsék a Microsoft-házirendeket: https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1

- [További információt a Microsoft által felügyelt felhőalapú infrastruktúrával, szolgáltatásokkal és alkalmazásokkal kapcsolatos, a Microsoft által felügyelt felhő-infrastruktúra,-szolgáltatások és-alkalmazások fejlesztésére vonatkozó stratégiáról és végrehajtásáról itt talál.](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Megosztott

## <a name="next-steps"></a>Következő lépések

- [Az Azure Security Benchmark v2 áttekintésének](../security/benchmarks/overview.md) megtekintése
- További tudnivalók az [Azure biztonsági alapterveiről](../security/benchmarks/security-baselines-overview.md)