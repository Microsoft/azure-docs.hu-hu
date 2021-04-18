---
title: Az Azure biztonsági alapkonfigurációja Azure Monitor
description: A Azure Monitor alapkonfigurációja eljárásokkal kapcsolatos útmutatást és forrásforrásokat biztosít az Azure biztonsági teljesítménytesztben meghatározott biztonsági javaslatok megvalósításához.
author: msmbaldwin
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 03/30/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: f002c7196b864d4a04beda1124d0519af612b716
ms.sourcegitcommit: 950e98d5b3e9984b884673e59e0d2c9aaeabb5bb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2021
ms.locfileid: "107600233"
---
# <a name="azure-security-baseline-for-azure-monitor"></a>Az Azure biztonsági alapkonfigurációja Azure Monitor

Ez a biztonsági alapkonfiguráció az Azure biztonsági teljesítményteszt [1.0-s](../security/benchmarks/overview-v1.md) verziójának útmutatását alkalmazza a Azure Monitor. Az Azure Security Benchmark ajánlásokat ad arra nézve, hogy hogyan tehetők biztonságossá a felhőalapú megoldások az Azure-ban. A tartalmat az Azure  biztonsági teljesítményteszt által meghatározott biztonsági vezérlők és a kapcsolódó, az azure-beli biztonsági Azure Monitor. **Az** olyan Azure Monitor, amelyekért a felelősség a Microsoft felelős, ki vannak zárva.

Ha meg Azure Monitor az Azure-biztonsági teljesítményteszt teljes körű leképezését, tekintse meg a teljes Azure Monitor alapkonfiguráció [leképezési fájlját.](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)

## <a name="network-security"></a>Hálózati biztonság

*További információ: [Azure Security Benchmark: Hálózati biztonság](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1.1: Azure-erőforrások védelme virtuális hálózatokon belül

**Útmutató:** A Azure Private Link engedélyezheti az Azure SaaS-szolgáltatásokhoz (például Azure Monitor) és az Azure által üzemeltetett ügyfél-/partnerszolgáltatásokhoz való hozzáférést a virtuális hálózat egy privát végpontjára vonatkozóan. A virtuális hálózat és a szolgáltatás közötti forgalom a Microsoft gerinchálózatán keresztül halad át, így kiküszöböli a nyilvános internet jelentette kitettséget.

Ha engedélyezni Azure Monitor forgalmat, az "AzureMonitor" szolgáltatáscímkék használatával engedélyezze a hálózati biztonsági csoportokon keresztül bejövő és kimenő forgalmat. Ahhoz, hogy a rendelkezésre állási monitorozási teszt forgalma elér Azure Monitor, használja az "ApplicationInsightsAvailability" szolgáltatáscímkét a hálózati biztonsági csoportokon keresztül bejövő összes forgalomra. Ahhoz, hogy a riasztási értesítések elérjék az ügyfélvégpontokat, az "ActionGroup" szolgáltatáscímkével engedélyezheti a hálózati biztonsági csoportokon keresztül bejövő forgalmat.

A virtuális hálózati szabályok Azure Monitor, hogy csak a virtuális hálózaton belüli kijelölt alhálózatok által küldött kommunikációt fogadják.

A Log Analytics-átjáróval adatokat küldhet a Azure Monitor-ban található Log Analytics-munkaterületre olyan számítógépek nevében, amelyek nem tudnak közvetlenül csatlakozni az internethez, így a számítógépeknek nem kell csatlakoznia az internethez. 

- [A Private Link beállítása Azure Monitor](/azure/azure-monitor/platform/private-link-security)

- [Internet-hozzáférés nélküli számítógépek csatlakoztatása a Log Analytics-átjáróval a Azure Monitor](/azure/azure-monitor/platform/gateway)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-network-interfaces"></a>1.2: Virtuális hálózatok, alhálózatok és hálózati adapterek konfigurációjának és forgalmának figyelése és naplózása

**Útmutató:** Azure Monitor szolgáltatás alapvető szolgáltatás, és nem támogatja a virtuális hálózatba való közvetlen üzembe helyezést, a mögöttes infrastruktúrát a Microsoft kezeli. Az olyan erőforrások esetén azonban, amelyek hálózati kapcsolatot létesítnek az Azure Monitor ajánlattal, egy hálózati biztonsági csoporttal kell biztonságossá tenni a hálózatukat. Engedélyezze a hálózati biztonsági csoport forgalmi naplóit, és küldje el a naplókat egy tárfiókba a forgalom naplózásához. Folyamatnaplókat is küldhet egy Log Analytics-munkaterületre, és a Traffic Analytics segítségével betekintést nyerhet az Azure-felhő forgalomába. A hálózati Traffic Analytics néhány előnye a hálózati tevékenység vizualizációja és a gyorspontok azonosítása, a biztonsági fenyegetések azonosítása, a forgalmi minták azonosítása és a hálózati helytelen konfigurációk azonosítása.

A hálózati Azure Monitor Private Link hozzáféréssel rendelkezik a hálózati naplózáshoz, például: "A privát végpont által feldolgozott adatok (IN/OUT)".

- [Az Azure Monitor hálózati követelményei](/azure/azure-monitor/platform/log-analytics-agent#network-requirements)

- [Internet-hozzáférés nélküli számítógépek csatlakoztatása a Log Analytics-átjáróval a Azure Monitor](/azure/azure-monitor/platform/gateway)

- [Hálózati biztonsági csoport forgalomnaplóinak engedélyezése](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Az alkalmazás engedélyezése és Traffic Analytics](../network-watcher/traffic-analytics.md)

- [A hálózati biztonság Azure Security Center](../security-center/security-center-network-recommendations.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: A hálózati biztonsági szabályok összetettségének és adminisztratív terhelésének minimalizálása

**Útmutató:** Ha engedélyezni Azure Monitor forgalmat, az "AzureMonitor" szolgáltatáscímkék használatával engedélyezheti a bejövő és kimenő forgalmat a hálózati biztonsági csoportokon keresztül. Annak érdekében, hogy a rendelkezésre állási monitorozási teszt forgalma elér Azure Monitor, használja az "ApplicationInsightsAvailability" szolgáltatáscímkét a hálózati biztonsági csoportokon keresztül bejövő összes forgalomra. A Microsoft kezeli a szolgáltatáscímke által felölelt címelőtagokat, és automatikusan frissíti a szolgáltatáscímkéket a címek változásával.

- [A szolgáltatáscímkék használata és használata](../virtual-network/service-tags-overview.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="110-document-traffic-configuration-rules"></a>1.10: A forgalom konfigurációs szabályainak dokumentálása

**Útmutató:** Azure Monitor azure-alapszolgáltatások részét képezi, és nem helyezhetők üzembe külön szolgáltatásként. Azure Monitor összetevők, például a Azure Monitor Agent és az Application Insights SDK is üzembe helyezhetők az erőforrásokkal együtt, és ez hatással lehet ezen erőforrások biztonsági útjára.

- [Az Azure Monitor hálózati követelményei](/azure/azure-monitor/platform/log-analytics-agent#network-requirements)

- [Internet-hozzáférés nélküli számítógépek csatlakoztatása a Log Analytics-átjáróval a Azure Monitor](/azure/azure-monitor/platform/gateway) 

- [Lásd: Ismerkedés a Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview#get-started)

- [Rendelkezésre állási webes tesztek beállítása](app/monitor-web-app-availability.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: Automatizált eszközök használata a hálózati erőforrások konfigurációjának figyelése és a változások észlelése érdekében

**Útmutató:** Az Azure-tevékenységnapló segítségével figyelheti az erőforrás-konfigurációkat, és észlelheti a hálózati erőforrások változásait a Azure Monitor. Hozzon létre riasztásokat a Azure Monitor, amelyek akkor aktiválódnak, ha a kritikus hálózati erőforrások módosulnak.

- [Az Azure-tevékenységnapló eseményeinek megtekintése és lekérése](/azure/azure-monitor/platform/activity-log#view-the-activity-log)

- [Riasztások létrehozása a Azure Monitor](/azure/azure-monitor/platform/alerts-activity-log)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

## <a name="logging-and-monitoring"></a>Naplózás és monitorozás

*További információ: [Azure biztonsági teljesítményteszt: Naplózás és monitorozás.](../security/benchmarks/security-control-logging-monitoring.md)*

### <a name="22-configure-central-security-log-management"></a>2.2: Központi biztonsági naplókezelés konfigurálása

**Útmutató:** Azure Monitor tevékenységnaplók segítségével naplózza az erőforrások módosításait. Ezeket a naplókat exportálhatja az Azure Storage-ba, az Event Hubba vagy egy Log Analytics-munkaterületre. Naplók Azure Monitor a végponteszközök, hálózati erőforrások és más biztonsági rendszerek által létrehozott biztonsági adatok összesítéséhez. A Azure Monitor lekérdezheti és elemzéseket végezhet az adatokon, és az Azure Storage-fiókokat használhatja a naplók hosszú távú/archiválási tárolására.

Másik lehetőségként engedélyezheti és bevetheti az adatokat Azure Sentinel külső SIEM-hez.

- [Platformnaplók és -metrikák gyűjtése Azure Monitor](/azure/azure-monitor/platform/diagnostic-settings)

- [Azure-beli virtuális gép belső gazdagépnaplóinak gyűjtése a Azure Monitor](/azure/azure-monitor/learn/quick-collect-azurevm)

- [A Azure Sentinel](../sentinel/quickstart-onboard.md)

- [Első lépések a Azure Monitor külső SIEM-integrációval](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/)

**Felelősség**: Ügyfél

**Azure Security Center monitorozása:** Az [Azure Biztonsági](/azure/governance/policy/samples/azure-security-benchmark) teljesítményteszt a Security Center alapértelmezett szabályzati kezdeményezése, amely a Security Center [javaslatainak alapja.](/azure/security-center/security-center-recommendations) A Azure Policy vezérlővel kapcsolatos összes definíciót automatikusan engedélyezi a Security Center. Az ehhez a vezérlőhez kapcsolódó riasztások esetében szükség [lehet Azure Defender](/azure/security-center/azure-defender) kapcsolódó szolgáltatásokra vonatkozó tervre.

**Azure Policy definíciók – microsoft.insights:**

[!INCLUDE [Resource Policy for microsoft.insights 2.2](../../includes/policy/standards/asb/rp-controls/microsoft.insights-2-2.md)]

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Azure-erőforrások naplózásának engedélyezése

**Útmutató:** Azure Monitor tevékenységnaplókat használ, a tevékenységnapló automatikusan engedélyezve van, és naplózza az Azure Monitor-erőforrásokon végzett műveleteket, például hogy ki indította el a műveletet, mikor történt a művelet, a művelet állapota és egyéb hasznos naplózási információk. 

- [Platformnaplók és -metrikák gyűjtése Azure Monitor](/azure/azure-monitor/platform/diagnostic-settings)

- [A naplózás és a különböző naplótípusok az Azure-ban](/azure/azure-monitor/platform/platform-logs-overview)

**Felelősség**: Ügyfél

**Azure Security Center monitorozása:** Az [Azure Biztonsági](/azure/governance/policy/samples/azure-security-benchmark) teljesítményteszt a Security Center alapértelmezett szabályzati kezdeményezése, amely a Security Center [javaslatainak alapja.](/azure/security-center/security-center-recommendations) A Azure Policy vezérlővel kapcsolatos összes definíciót automatikusan engedélyezi a Security Center. Az ehhez a vezérlőhez kapcsolódó riasztások esetében szükség [lehet Azure Defender](/azure/security-center/azure-defender) kapcsolódó szolgáltatásokra vonatkozó tervre.

**Azure Policy definíciók – microsoft.insights:**

[!INCLUDE [Resource Policy for microsoft.insights 2.3](../../includes/policy/standards/asb/rp-controls/microsoft.insights-2-3.md)]

### <a name="25-configure-security-log-storage-retention"></a>2.5: Biztonsági naplók tárolásának konfigurálása

**Útmutató:** A Azure Monitor meg a Log Analytics-munkaterület megőrzési megőrzési időszakát a szervezet megfelelőségi szabályozásának megfelelően. Használja az Azure Storage-fiókokat a naplók hosszú távú/archiválási tárolásához.

- [Az adatmegőrzési időtartam módosítása a Log Analyticsben](/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period)

- [Adatmegőrzési szabályzat konfigurálása az Azure Storage-fiók naplóihoz](/azure/storage/common/storage-monitor-storage-account#configure-logging)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="26-monitor-and-review-logs"></a>2.6: Naplók figyelése és áttekintése

**Útmutató:** Naplók elemzése és figyelése a rendellenes viselkedés érdekében, és az eredmények rendszeres áttekintése. A Azure Monitor log Analytics-munkaterülettel áttekinthet naplókat, és lekérdezéseket hajthatja végre a naplóadatokon.

Másik lehetőségként engedélyezheti és be is használhatja az adatokat Azure Sentinel külső SIEM-hez.

- [A Azure Sentinel](../sentinel/quickstart-onboard.md)

- [Ismerkedés a Log Analytics-lekérdezésekkel](/azure/azure-monitor/log-query/log-analytics-tutorial)

- [Egyéni lekérdezések végrehajtása a Azure Monitor](/azure/azure-monitor/log-query/get-started-queries)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7: Riasztások engedélyezése rendellenes tevékenységekhez

**Útmutató:** A Azure Security Center Log Analytics-munkaterülettel való használatával monitorozást és riasztásokat használhat a biztonsági naplókban és eseményekben található rendellenes tevékenységekről. Másik lehetőségként engedélyezheti és be is használhatja az adatokat a Azure Sentinel.

- [A Azure Sentinel](../sentinel/quickstart-onboard.md)

- [Riasztások kezelése a Azure Security Center](../security-center/security-center-managing-and-responding-alerts.md)

- [Naplóelemzési naplóadatokra vonatkozó riasztások](/azure/azure-monitor/learn/tutorial-response)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

## <a name="identity-and-access-control"></a>Identitás- és hozzáférés-vezérlés

*További információ: [Azure Biztonsági teljesítményteszt: Identitás és Access Control.](../security/benchmarks/security-control-identity-access-control.md)*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: A rendszergazdai fiókok leltárának fenntartása

**Útmutató:** Az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC) szerepkör-hozzárendelésekkel teszi lehetővé az Azure-erőforrásokhoz való hozzáférés kezelését. Ezeket a szerepköröket felhasználókhoz, csoportokhoz, szolgáltatásnévhez és felügyelt identitásokhoz rendelheti hozzá. Bizonyos erőforrásokhoz előre meghatározott beépített szerepkörök tartoznak, és ezek a szerepkörök olyan eszközökkel leltározhatók vagy kérdezhetők le, mint az Azure CLI, az Azure PowerShell vagy az Azure Portal.

- [Címtárszerepkomó le Azure Active Directory (Azure AD) a PowerShell használatával](/powershell/module/azuread/get-azureaddirectoryrole)

- [Címtárbeli szerepkör tagjainak lekért használata az Azure AD-ban a PowerShell használatával](/powershell/module/azuread/get-azureaddirectoryrolemember)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: Dedikált rendszergazdai fiókok használata

**Útmutató:** Szabványos üzemeltetési eljárások létrehozása a dedikált rendszergazdai fiókok használatával kapcsolatban. A Azure Security Center és hozzáférés-kezelés használatával figyelheti a rendszergazdai fiókok számát.

Az azure-beli Azure Active Directory (Azure AD) és a Microsoft-szolgáltatások Privileged Identity Management kiemelt szerepkörei és az Azure Resource Manager.

- [Azure AD Privileged Identity Management áttekintés](../active-directory/privileged-identity-management/pim-configure.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="34-use-azure-active-directory-single-sign-on-sso"></a>3.4: Egyszeri Azure Active Directory (SSO) használata

**Útmutató:** Amikor csak lehetséges, használjon Azure Active Directory (Azure AD) SSO-t az önálló hitelesítő adatok szolgáltatásonkénti konfigurálása helyett. Használja Azure Security Center Identity and Access Management recommendations (Identitás- és hozzáférés-kezelés) javaslatokat.

- [Az SSO és az Azure AD](../active-directory/manage-apps/what-is-single-sign-on.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: Többtényezős hitelesítés használata minden Azure Active Directory-alapú hozzáféréshez

**Útmutató:** Engedélyezze a Azure Active Directory (Azure AD) többtényezős hitelesítést, és kövesse Azure Security Center identitással és hozzáféréssel kapcsolatos javaslatokat.

- [Többtényezős hitelesítés engedélyezése az Azure-ban](../active-directory/authentication/howto-mfa-getstarted.md)

- [Identitás és hozzáférés figyelése a Azure Security Center](../security-center/security-center-identity-access.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: Dedikált gépek (Emelt szintű hozzáférésű munkaállomások) használata az összes felügyeleti feladathoz

**Útmutató:** Használjon biztonságos, Azure által felügyelt munkaállomást (más néven emelt szintű hozzáférési szintű munkaállomást vagy emelt hozzáférési szintű munkaállomást) az emelt szintű jogosultságot igénylő felügyeleti feladatokhoz.

- [A biztonságos, Azure által felügyelt munkaállomások](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

- [Többtényezős Azure Active Directory (Azure AD) engedélyezése](../active-directory/authentication/howto-mfa-getstarted.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3.7: A rendszergazdai fiókokból származó gyanús tevékenységek naplózása és riasztása

**Útmutató:** Azure Active Directory (Azure AD) biztonsági jelentésekkel és monitorozással észlelheti, ha gyanús vagy nem biztonságos tevékenység történik a környezetben. A Azure Security Center identitás- és hozzáférési tevékenységek figyelése.

- [A kockázatos tevékenységek miatt megjelölt Azure AD-felhasználók azonosítása](../active-directory/identity-protection/overview-identity-protection.md)

- [A felhasználók identitási és hozzáférési tevékenységeinek monitorozása az Azure Security Centerben](../security-center/security-center-identity-access.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: Azure-erőforrások kezelése csak jóváhagyott helyekről

**Útmutató:** A feltételes hozzáférés elnevezett helyei segítségével csak az IP-címtartományok vagy országok/régiók meghatározott logikai csoportosításai számára engedélyezi a hozzáférést.

- [Elnevezett helyek konfigurálása az Azure-ban](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="39-use-azure-active-directory"></a>3.9: A Azure Active Directory

**Útmutató:** A Azure Active Directory (Azure AD) használata központi hitelesítési és engedélyezési rendszerként. Az Azure AD erős titkosítással védi az adatokat az átvitel alatt és az átvitel alatt. Az Azure AD emellett a felhasználói hitelesítő adatokat is megsokossa, hashes és biztonságosan tárolja.

- [Azure AD-példány létrehozása és konfigurálása](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: A felhasználói hozzáférés rendszeres áttekintése és egyeztetése

**Útmutató:** Azure Active Directory (Azure AD) naplókat biztosít az elavult fiókok felderítésének segítésekor. Emellett az Azure Identity Access Reviews használatával hatékonyan kezelheti a csoporttagságokat, a vállalati alkalmazásokhoz való hozzáférést és a szerepkör-hozzárendeléseket. A felhasználói hozzáférés rendszeresen felülvizsgálható, hogy csak a megfelelő felhasználók férnek hozzá.

- [Az Azure AD-jelentéskészítés](/azure/active-directory/reports-monitoring/)

- [Az Azure Identity Hozzáférési felülvizsgálatok használata](../active-directory/governance/access-reviews-overview.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3.11: Az inaktivált hitelesítő adatok elérésére tett kísérletek figyelése

**Útmutató:** Hozzáféréssel rendelkezik Azure Active Directory (Azure AD) bejelentkezési tevékenységéhez, naplózási és kockázati eseménynapló-forrásaihoz, amelyek lehetővé teszik az integrációt bármely SIEM-/monitorozási eszközzel. Ezt a folyamatot leegyszerűsítheti, ha diagnosztikai beállításokat hoz létre az Azure AD felhasználói fiókokhoz, és elküldi az auditnaplókat és bejelentkezési naplókat egy Log Analytics-munkaterületre. A kívánt riasztásokat a Log Analytics-munkaterületen konfigurálhatja.

- [Azure-tevékenységnaplók integrálása az Azure Monitorba](/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3.12: Riasztás a fiók bejelentkezési viselkedésének eltéréséről

**Útmutató:** A Azure Active Directory (Azure AD) kockázat- és identitásvédelmi szolgáltatásaival konfigurálhatja a felhasználói identitásokkal kapcsolatos észlelt gyanús műveletekre adott automatikus válaszokat. Az adatokat be is Azure Sentinel vizsgálathoz.

- [Az Azure AD kockázatos bejelentkezéseinek megtekintése](../active-directory/identity-protection/overview-identity-protection.md)

- [Az Identity Protection kockázati szabályzatok konfigurálása és engedélyezése](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [A Azure Sentinel](../sentinel/quickstart-onboard.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

## <a name="data-protection"></a>Adatvédelem

*További információ: [Azure Security Benchmark: Adatvédelem](../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: Bizalmas adatok leltárának fenntartása

**Útmutató:** Ha lehetséges, használjon címkéket az olyan Azure Monitor erőforrások nyomon követéséhez, amelyek bizalmas adatokat tárolnak vagy feldolgoznak, például a Log Analytics-munkaterületeket.

- [Címkék létrehozása és használata](../azure-resource-manager/management/tag-resources.md)

- [Naplóadatok és munkaterületek elérésének felügyelete az Azure Monitorban](/azure/azure-monitor/platform/manage-access)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: Bizalmas adatokat tároló vagy feldolgozó rendszerek elkülönítése

**Útmutató:** Elkülönítés implementálás különálló előfizetések és felügyeleti csoportok használatával az egyes biztonsági tartományokhoz, például a környezet típusához és az adatok bizalmasságához. Korlátozhatja az alkalmazások és a vállalati környezetek által Azure Monitor kapcsolódó erőforrásokhoz való hozzáférés szintjét. Az Azure szerepköralapú hozzáférés-Azure Monitor (Azure RBAC) használatával szabályozhatja a hozzáférés-vezérlést.

- [További Azure-előfizetések létrehozása](../cost-management-billing/manage/create-subscription.md)

- [Felügyeleti csoportok létrehozása](../governance/management-groups/create-management-group-portal.md)

- [Címkék létrehozása és használata](../azure-resource-manager/management/tag-resources.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: Az átvitel során bizalmas adatok titkosítása

**Útmutató:** Azure Monitor TLS 1.2-t egyezteti. Győződjön meg arról, hogy az Azure-erőforrásokhoz csatlakozó ügyfelek képesek egyeztetni a TLS 1.2-t vagy annál nagyobbat. 

Application Insights és a Log Analytics is lehetővé teszi a TLS 1.1 és A TLS 1.0 adatainak betöltését. Az adatok az ügyféloldalon való konfigurálásával a TLS 1.2-re korlátozhatóak.

- [Adatok biztonságos küldése a TLS 1.2 használatával](/azure/azure-monitor/platform/data-security#sending-data-securely-using-tls-12)

**Felelősség**: Megosztott

**Azure Security Center monitorozás:** Nincs

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: Aktív felderítési eszköz használata a bizalmas adatok azonosításához

**Útmutató:** Az adatazonosítási, besorolási és adatveszteség-megelőzési funkciók még nem érhetők el a Azure Monitor. Ha megfelelőségi célokból szükség van rá, implementálja a harmadik féltől származó megoldást.
A Microsoft által felügyelt mögöttes platform esetén a Microsoft minden ügyféltartalmat bizalmasként kezel, és nagy terjedelmüknek megfelelő védelmet biztosít az ügyfelek adatvesztése és kitettsége ellen. Annak érdekében, hogy az Azure-beli ügyféladatok biztonságban maradnak, a Microsoft robusztus adatvédelmi vezérlőket és képességeket valósított meg és tart karban.

- [Az ügyféladatok Azure-beli védelmének ismertetése](../security/fundamentals/protection-customer-data.md)

**Felelősség**: Megosztott

**Azure Security Center monitorozás:** Nincs

### <a name="46-use-role-based-access-control-to-control-access-to-resources"></a>4.6: Az erőforrásokhoz való hozzáférés vezérlése szerepköralapú hozzáférés-vezérléssel

**Útmutató:** Az Azure szerepköralapú hozzáférés-vezérlés (RBAC) használata a hozzáférések kezeléséhez Azure Monitor.

- [Szerepkörök, engedélyek és biztonság a Azure Monitor](/azure/azure-monitor/platform/roles-permissions-security)

- [Az Azure RBAC konfigurálása](../role-based-access-control/role-assignments-portal.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: Bizalmas adatok titkosítása

**Útmutató:** Azure Monitor, hogy a Microsoft által felügyelt kulcsok (MMK) használatával az összes adat és mentett lekérdezés titkosítva legyen. Azure Monitor lehetőséget biztosít a saját kulcsával való titkosításra is, amely a saját tárolójában Azure Key Vault, és amelyet a tároló rendszer által hozzárendelt felügyelt identitáshitelesítéssel használ. Ez az ügyfél által felügyelt kulcs (CMK) szoftveres vagy hardveres HSM-védelem alatt lehet.

- [Azure Monitor által kezelt kulcsok használata](/azure/azure-monitor/platform/customer-managed-keys)

- [A Log Analytics adatbiztonsága](/azure/azure-monitor/platform/data-security)

- [Adatgyűjtés, -megőrzés és -tárolás az Application Insightsban](app/data-retention-privacy.md)

- [Inaktív adatok Azure-ban való titkosításának ismertetése](../security/fundamentals/encryption-atrest.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: Kritikus fontosságú Azure-erőforrások változásainak naplózása és riasztása

**Útmutató:** A Azure Monitor Azure-tevékenységnaplóval használva riasztásokat hozhat létre, amikor változásokra kerül sor a Azure Monitor és a kapcsolódó erőforrásokban.

- [Riasztások létrehozása az Azure-tevékenységnapló eseményeihez](/azure/azure-monitor/platform/alerts-activity-log)

**Felelősség**: Ügyfél

**Azure Security Center monitorozása:** Az [Azure-biztonsági](/azure/governance/policy/samples/azure-security-benchmark) teljesítményteszt a Security Center alapértelmezett szabályzati kezdeményezése, amely a Security Center [javaslatainak alapja.](/azure/security-center/security-center-recommendations) A Azure Policy vezérlővel kapcsolatos összes definíciót automatikusan engedélyezi a Security Center. Az ehhez a vezérlőhez kapcsolódó riasztások esetében szükség [lehet Azure Defender](/azure/security-center/azure-defender) kapcsolódó szolgáltatásokra vonatkozó tervre.

**Azure Policy definíciók – microsoft.insights:**

[!INCLUDE [Resource Policy for microsoft.insights 4.9](../../includes/policy/standards/asb/rp-controls/microsoft.insights-4-9.md)]

## <a name="vulnerability-management"></a>Biztonságirés-kezelés

*További információ: [Azure biztonsági teljesítményteszt: Biztonsági rések kezelése.](../security/benchmarks/security-control-vulnerability-management.md)*

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5: Kockázatminősítési folyamat használata a felderített biztonsági rések szervizelésének rangsorolására

**Útmutató:** Használjon gyakori kockázatpontozó programot (például közös sebezhetőségi pontozási rendszert) vagy a külső beolvasó eszköz által biztosított alapértelmezett kockázatminősítéseket.

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

## <a name="inventory-and-asset-management"></a>Leltár-és eszközfelügyelet

*További információ: [Azure biztonsági teljesítményteszt: Leltár és eszközkezelés.](../security/benchmarks/security-control-inventory-asset-management.md)*

### <a name="61-use-automated-asset-discovery-solution"></a>6.1: Automatizált eszközfelderítési megoldás használata

**Útmutató:** Az Azure CLI használatával lekérdezheti és felderítheti az Azure Monitor erőforrásait. Győződjön meg arról, hogy a bérlő megfelelő (olvasási) engedélyeket használ, és számba veszi az összes Azure-előfizetést és az előfizetésen belüli erőforrásokat.

- [Azure Monitor CLI](https://docs.microsoft.com/cli/azure/monitor?view=azure-cli-latest&amp;preserve-view=true)

- [Az Azure-előfizetések megtekintése](https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-4.8.0&amp;preserve-view=true)

- [Az Azure RBAC](../role-based-access-control/overview.md)

- [Szerepkörök, engedélyek és biztonság a Azure Monitor](/azure/azure-monitor/platform/roles-permissions-security)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="62-maintain-asset-metadata"></a>6.2: Objektum metaadatainak karbantartása

**Útmutató:** Címkék alkalmazása Azure Monitor erőforrásokra, amelyek metaadatokat adva logikailag rendszerezik őket egy taxonómiába.

- [Címkék létrehozása és használata](../azure-resource-manager/management/tag-resources.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: Nem engedélyezett Azure-erőforrások törlése

**Útmutató:** Címkézéssel, felügyeleti csoportokkal és különálló előfizetésekkel rendszerezheti és nyomon követheti Azure Monitor kapcsolódó erőforrásokat. Rendszeresen összeegyezteti a leltárat, és gondoskodik arról, hogy a jogosulatlan erőforrások időben törlődjenek az előfizetésből.

- [További Azure-előfizetések létrehozása](../cost-management-billing/manage/create-subscription.md)

- [Felügyeleti csoportok létrehozása](../governance/management-groups/create-management-group-portal.md)

- [Címkék létrehozása és használata](../azure-resource-manager/management/tag-resources.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6.4: Jóváhagyott Azure-erőforrások meghatározása és leltározása

**Útmutató:** Leltár létrehozása a jóváhagyott Azure-erőforrásokról és a számítási erőforrásokhoz jóváhagyott szoftverekről a szervezeti igényeknek megfelelően.

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: Nem jóváhagyott Azure-erőforrások figyelése

**Útmutató:** A Azure Policy segítségével korlátozásokat korlátozhat az előfizetésében létrehozható erőforrások típusára vonatkozóan.

Az Azure Resource Graph használatával lekérdezheti és felderítheti az előfizetésükön belüli erőforrásokat.  Győződjön meg arról, hogy a környezetben jelen van minden Azure-erőforrás jóvá van hagyva.

- [Az Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md)

- [Lekérdezések létrehozása az Azure Resource Graph Explorerrel](../governance/resource-graph/first-query-portal.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7: Nem jóváhagyott Azure-erőforrások és -szoftveralkalmazások eltávolítása

**Útmutató:** A leltár rendszeres egyeztetése, és annak biztosítása, hogy Azure Monitor kapcsolódó erőforrások időben törlődjenek az előfizetésből.  

- [Azure Log Analytics-munkaterület törlése](/azure/azure-monitor/platform/delete-workspace)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="69-use-only-approved-azure-services"></a>6.9: Csak jóváhagyott Azure-szolgáltatások használata

**Útmutató:** A Azure Policy korlátozhatja, Azure Monitor mely kapcsolódó erőforrásokat lehet kiépítni a környezetben.

- [Az Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md)

- [Adott erőforrástípus megtagadása a Azure Policy](https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#general)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6.11: Korlátozza a felhasználók számára a Azure Resource Manager

**Útmutató:** Az Azure feltételes hozzáférés használatával korlátozhatja a felhasználók Azure Resources Managerrel való interakcióját a "Hozzáférés blokkolása" beállításával a "Microsoft Azure Management" alkalmazáshoz.

- [Feltételes hozzáférés konfigurálása az Azure Resources Managerhez való hozzáférés letiltása érdekében](../role-based-access-control/conditional-access-azure-management.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

## <a name="secure-configuration"></a>Biztonságos konfiguráció

*További információ: [Azure biztonsági teljesítményteszt: Biztonságos konfiguráció.](../security/benchmarks/security-control-secure-configuration.md)*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: Biztonságos konfigurációk létrehozása az összes Azure-erőforráshoz

**Útmutató:** Egyéni Azure Policy a kapcsolódó erőforrások konfigurációjának naplózására Azure Monitor kényszeríthető. A beépített definíciókat Azure Policy is használhatja.

Emellett a Azure Resource Manager képes exportálni a sablont az JavaScript Object Notation-ban (JSON), amelyet át kell vizsgálni annak biztosítása érdekében, hogy a konfigurációk megfelelnek vagy meghaladják a szervezet biztonsági követelményeit.

Az Azure-erőforrások Azure Security Center alapkonfigurációjaként is használhatja az azure-beli erőforrásokra vonatkozó javaslatokat.

Ha élő streamelési APM-képességeket használ, tegye biztonságossá a csatornát a rendszerkontraszt kulcs mellett egy titkos API-kulccsal.

- [Az APM-Élő metrikastream](https://docs.microsoft.com/azure/azure-monitor/app/live-stream#secure-the-control-channel)

- [Az elérhető aliasok Azure Policy megtekintése](https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-4.8.0&amp;preserve-view=true)

- [Oktatóanyag: Szabályzatok létrehozása és kezelése a megfelelőség kényszerítéséhez](../governance/policy/tutorials/create-and-manage.md)

- [Egy- és többerőforrásos exportálás sablonba a Azure Portal](../azure-resource-manager/templates/export-template-portal.md)

- [Biztonsági javaslatok – gyorsútmutató](../security-center/recommendations-reference.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: Biztonságos Azure-erőforráskonfigurációk fenntartása

**Útmutató:** Az Azure Policy [megtagadás] és [üzembe helyezés, ha nem létezik] használatával kényszerítheti a biztonságos beállításokat a Azure Monitor kapcsolódó erőforrásokon.  Emellett a Azure Resource Manager is használhatja a szervezet által Azure Monitor erőforrások biztonsági konfigurációjának fenntartásához.

- [A Azure Policy hatásainak](../governance/policy/concepts/effects.md)

- [Szabályzatok létrehozása és kezelése a megfelelőség kikényszerítése céljából](../governance/policy/tutorials/create-and-manage.md)

- [Azure Resource Manager sablonok áttekintése](../azure-resource-manager/templates/overview.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Az Azure-erőforrások konfigurációjának biztonságos tárolása

**Útmutató:** Az Azure DevOps használatával biztonságosan tárolhatja és kezelheti a kódot, például egyéni Azure-szabályzatokat és Azure Resource Manager sablonokat. Az Azure DevOpsban kezelett erőforrások eléréséhez engedélyeket adhat vagy tagadhat meg adott felhasználóknak, beépített biztonsági csoportoknak vagy az Azure Active Directory-ben (Azure AD)-ben meghatározott csoportoknak, ha integrálva vannak az Azure DevOps-ral, vagy a Active Directory,ha integrálva van a TFS-sel.

- [Kód tárolása az Azure DevOpsban](/azure/devops/repos/git/gitworkflow)

- [Az Azure DevOps engedélyeinek és csoportjainak](/azure/devops/organizations/security/about-permissions)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7: Konfigurációkezelési eszközök üzembe helyezése Azure-erőforrásokhoz

**Útmutató:** Standard biztonsági konfigurációk definiálása és megvalósítása Azure Monitor kapcsolódó erőforrásokhoz a Azure Policy. Egyéni Azure Policy a kapcsolódó erőforrások biztonsági konfigurációjának naplózása vagy kényszerít Azure Monitor érdekében. Használhatja az adott erőforrásokhoz kapcsolódó beépített szabályzatdefiníciókat is.

- [Az Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md)

- [Azure Policy aliasok](https://docs.microsoft.com/azure/governance/policy/concepts/definition-structure#aliases)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9: Automatizált konfigurációfigyelés megvalósítása az Azure-erőforrásokhoz

**Útmutató:** A Azure Security Center használatával alapkonfiguráció-vizsgálatot végezhet a Azure Monitor kapcsolódó erőforrásokon.  Emellett az Azure Azure Policy konfigurációk riasztása és naplózása is használható.

- [Javaslatok szervizlének Azure Security Center](../security-center/security-center-remediate-recommendations.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="711-manage-azure-secrets-securely"></a>7.11: Azure-beli titkos kulcsok biztonságos kezelése

**Útmutató:** A Felügyeltszolgáltatás-identitás és a Azure Key Vault segítségével egyszerűsítheti és biztosíthatja a támogatott Azure Monitorhoz kapcsolódó erőforrások titkosságkezelését.

- [Az Azure Managed Identities integrálása](../azure-app-configuration/howto-integrate-azure-managed-service-identity.md)

- [Az Azure-erőforrások felügyelt identitását támogató szolgáltatások](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md)

- [Új Key Vault](../key-vault/secrets/quick-create-portal.md)

- [Felügyelt identitással Key Vault hitelesítésének engedélyezése](/azure/active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-nonaad)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: Identitások biztonságos és automatikus kezelése

**Útmutató:** Felügyelt identitások használata az Azure-szolgáltatások automatikus felügyelt identitással való Azure Active Directory (Azure AD) használatával. A felügyelt identitások lehetővé teszik a hitelesítést bármely olyan szolgáltatásban, amely támogatja az Azure AD-hitelesítést, beleértve Azure Monitor erőforrásokat is, anélkül, hogy hitelesítő adatokat ad meg a kódban.

- [Felügyelt identitások konfigurálása Azure-erőforrásokhoz](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: A hitelesítő adatok nem szándékolt kitettségének megszüntetése

**Útmutató:** Hitelesítőadat-ellenőrző implementálja a hitelesítő adatokat a kódban. A Credential Scanner a felfedezett hitelesítő adatok biztonságosabb helyre, például az Azure Key Vaultba való áthelyezésére is javaslatot tesz.

- [Hitelesítőadat-olvasó beállítása](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

## <a name="malware-defense"></a>Kártevők elleni védelem

*További információ: [Azure biztonsági teljesítményteszt: Kártevővédelem.](../security/benchmarks/security-control-malware-defense.md)*

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2: A nem számítási Azure-erőforrásokba feltölteni szükséges fájlok előzetes beolvasása

**Útmutató:** A Microsoft kártevőirtó engedélyezve van az Azure-szolgáltatásokat (például Azure Monitor kapcsolódó erőforrásokat) támogató mögöttes gazdagépen, de nem fut a tartalomon. 

Vizsgálja meg előre az összes olyan fájlt, amely Azure Monitor kapcsolódó erőforrásokra, például a Log Analytics-munkaterületre.

Használja Azure Security Center adatszolgáltatások fenyegetésészlelése funkcióját a tárfiókba feltöltött kártevők észleléséhez. 

- [A Microsoft kártevőirtó szoftveres Azure Cloud Services és Virtual Machines](../security/fundamentals/antimalware.md)

- [Az Azure Security Center fenyegetésészlelése az adatszolgáltatásokban](../security-center/azure-defender.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

## <a name="data-recovery"></a>Adat-helyreállítás

*További információ: [Azure biztonsági teljesítményteszt: Adat-helyreállítás.](../security/benchmarks/security-control-data-recovery.md)*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: Rendszeres automatikus biztonságimentek biztosítása

**Útmutató:** A Azure Resource Manager segítségével exportálhatja a Azure Monitor és a kapcsolódó erőforrásokat egy JavaScript Object Notation-sablonba (JSON), amely biztonsági másolatként használható Azure Monitor és a kapcsolódó konfigurációkhoz.  A Azure Automation a biztonsági mentési szkriptek automatikus futtatásához. 

- [Log Analytics-munkaterület kezelése Azure Resource Manager sablonokkal](/azure/azure-monitor/samples/resource-manager-workspace)

- [Egy- és többerőforrásos exportálás sablonba a Azure Portal](../azure-resource-manager/templates/export-template-portal.md)

- [A Azure Automation](../automation/automation-intro.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: Teljes rendszer-biztonsági mentések és az ügyfelek által kezelt kulcsok biztonsági mentése

**Útmutató:** A Azure Resource Manager segítségével exportálhatja a Azure Monitor és a kapcsolódó erőforrásokat egy JavaScript Object Notation- (JSON-) sablonba, amely biztonsági másolatként használható a Azure Monitor és a kapcsolódó konfigurációkhoz. Ügyfél által felügyelt kulcsok biztonsági mentése a Azure Key Vault, Azure Monitor kapcsolódó erőforrások ügyfél által felügyelt kulcsokat használnak,

- [Log Analytics-munkaterület kezelése Azure Resource Manager sablonokkal](/azure/azure-monitor/platform/template-workspace-configuration)

- [Egy- és többerőforrásos exportálás sablonba a Azure Portal](../azure-resource-manager/templates/export-template-portal.md)

- [Kulcstartókulcsok biztonsági mentése az Azure-ban](/powershell/module/az.keyvault/backup-azkeyvaultkey)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: Az összes biztonsági mentés ellenőrzése, beleértve az ügyfél által kezelt kulcsokat is

**Útmutató:** A visszaállítás rendszeres időközönkénti végrehajtása Azure Resource Manager sablonfájlok használatával. Tesztelje az ügyfél által kezelt kulcsok biztonságimentett visszaállítását.

- [Log Analytics-munkaterület kezelése Azure Resource Manager sablonokkal](/azure/azure-monitor/samples/resource-manager-workspace)

- [Kulcstartókulcsok visszaállítása az Azure-ban](/powershell/module/az.keyvault/restore-azkeyvaultkey)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: A biztonsági másolatok és az ügyfél által kezelt kulcsok védelmének biztosítása

**Útmutató:** Az Azure DevOps használatával biztonságosan tárolhatja és kezelheti a kódot, például egyéni Azure-szabályzatokat vagy Azure Resource Manager sablonokat. Az Azure DevOpsban felügyelt erőforrások védelméhez engedélyeket adhat vagy tagadhat meg adott felhasználóknak, beépített biztonsági csoportoknak vagy az Azure Active Directory-ben (Azure AD)-ben meghatározott csoportoknak, ha azok integrálva vannak az Azure DevOps-ral, vagy a Active Directory,ha integrálva vannak a TFS-sel.  Az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC) használata az ügyfelek által kezelt kulcsok védelmére.

Emellett engedélyezze a Soft-Delete és véglegesen töröljék a védelmet a Key Vault a kulcsok véletlen vagy rosszindulatú törléssel szembeni védelme érdekében. Ha az Azure Storage a sablon biztonsági Azure Resource Manager tárolására szolgál, engedélyezze a helyreállítható törlést az adatok mentéséhez és helyreállításához blobok vagy blob-pillanatképek törlésekor.

- [Kód tárolása az Azure DevOpsban](/azure/devops/repos/git/gitworkflow)

- [Az Azure DevOps engedélyeinek és csoportjainak](/azure/devops/organizations/security/about-permissions)

- [Az Soft-Delete és a véglegesen kiürítés elleni védelem engedélyezése a Key Vault](../storage/blobs/soft-delete-blob-overview.md)

- [Az Azure Storage-blobok helyreállítható törlése](../storage/blobs/soft-delete-blob-overview.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

## <a name="incident-response"></a>Incidensmegoldás

*További információ: [Azure Security Benchmark: Incidensek kezelése](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10.1: Incidensválasz-útmutató létrehozása

**Útmutató**: Alakítson ki incidenskezelési útmutatót a vállalat számára. Gondoskodjon írásos incidenskezelési tervekről, amelyek definiálják az összes résztvevő szerepkörét, valamint az incidenskezelés fázisait az észleléstől az incidens utáni értékelésig.

- [Útmutatás saját biztonsági incidensek válaszadási folyamatának építéshez](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Microsoft Biztonsági reagálási központ az incidensek felépítése](https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/)

- [Az NIST számítógépes biztonsági incidenskezelési útmutatójának segítségével segítséget nyújt saját incidenskezelési tervének létrehozásában](https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: Incidenspontozási és -rangsorolási eljárás létrehozása

**Útmutató:** Security Center súlyosságot rendel az egyes riasztásokhoz, így fontossági sorrendbe adhatja, hogy mely riasztásokat kell először megvizsgálni. A súlyosság azon alapul, Security Center mennyire biztos a riasztás kiadásához használt eredményben vagy elemzésben, valamint hogy a riasztáshoz vezető tevékenység mögött rosszindulatú szándék volt-e.

Emellett egyértelműen jelöljük meg az előfizetéseket (például: éles környezetben, nem éles környezetben) címkéket használ, és létrehoz egy elnevezési rendszert az Azure-erőforrások egyértelmű azonosításához és kategorizálásához, különösen a bizalmas adatokat feldolgozók számára.  Az Ön felelőssége, hogy rangsorolja a riasztások megoldását azon Azure-erőforrások és -környezetek kritikussága alapján, ahol az incidens történt.

- [Biztonsági riasztások az Azure Security Centerben](../security-center/security-center-alerts-overview.md)

- [Címkék létrehozása és használata](../azure-resource-manager/management/tag-resources.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="103-test-security-response-procedures"></a>10.3: Biztonsági reagálási eljárások tesztelése

**Útmutató:** Gyakorlatok a rendszerek incidensválasz-képességeinek rendszeres teszteléséhez az Azure-erőforrások védelme érdekében. Azonosítsa a gyenge pontokat és réseket, és szükség esetén dolgozza át a tervet.

- [NIST-kiadvány – Útmutató az IT-tervekhez és -képességekhez szükséges tesztelési, képzési és gyakorlatprogramokhoz](https://csrc.nist.gov/publications/detail/sp/800-84/final)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: Adja meg a biztonsági incidens kapcsolattartási adatait, és konfigurálja a riasztási értesítéseket a biztonsági incidensek esetén

**Útmutató:** A Biztonsági incidens kapcsolattartási adatait a Microsoft arra használja, hogy kapcsolatba lépjen Önnel, ha a Microsoft Biztonsági reagálási központ (MSRC) azt tapasztalja, hogy az Ön adatait illetéktelen vagy illetéktelen fél férte hozzá. Tekintse át az incidenseket a probléma megoldása érdekében.

- [A biztonsági kapcsolattartó Azure Security Center beállítása](../security-center/security-center-provide-security-contact-details.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: Biztonsági riasztások beépítése az incidensválasz-rendszerbe

**Útmutató:** A riasztások Azure Security Center és javaslatokat exportálhatja a Continuous Export (Folyamatos exportálás) funkcióval az Azure-erőforrásokat érintő kockázatok azonosítása érdekében. A folyamatos exportálás lehetővé teszi, hogy manuálisan vagy folyamatosan exportálja a riasztásokat és javaslatokat. A riasztásokat a Azure Security Center adat-összekötővel streamelheti a Azure Sentinel. 

- [Folyamatos exportálás konfigurálása](../security-center/continuous-export.md) 

- [Riasztások streamelése az Azure Sentinelbe](../sentinel/connect-azure-security-center.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: A biztonsági riasztások válaszának automatizálása

**Útmutató:** A Azure Security Center-munkafolyamat-automatizálási funkcióval automatikusan aktiválhatja a válaszokat a biztonsági riasztások Logic Apps javaslatokon keresztül az Azure-erőforrások védelme érdekében.

- [A munkafolyamat-automatizálás és -Logic Apps](../security-center/workflow-automation.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

## <a name="penetration-tests-and-red-team-exercises"></a>Behatolási tesztek és Red Team-gyakorlatok

*További információ: [Azure biztonsági teljesítményteszt: Behatolási tesztek és Red Team-gyakorlatok.](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md)*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11.1: Az Azure-erőforrások rendszeres behatolástesztelése és az összes kritikus fontosságú biztonsági eredmény szervizelésének biztosítása

**Útmutató:** Kövesse a Microsoft kötelezettségvállalási szabályait annak biztosításához, hogy a behatolási tesztek ne sértsen meg Microsoft-szabályzatokat. Használja a Microsoft red teaming és élő webhely-behatolási tesztek Microsoft által felügyelt felhőinfra infrastruktúrája, szolgáltatásai és alkalmazásai elleni stratégiáját és végrehajtását.

- [Behatolástesztelési beavatkozási szabályok](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)

- [Riasztási tesztek a Microsoft-felhőben](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Felelősség**: Megosztott

**Azure Security Center:** Nincs

## <a name="next-steps"></a>Következő lépések

- [Az Azure Security Benchmark v2 áttekintésének](/azure/security/benchmarks/overview) megtekintése
- További tudnivalók az [Azure biztonsági alapterveiről](/azure/security/benchmarks/security-baselines-overview)
