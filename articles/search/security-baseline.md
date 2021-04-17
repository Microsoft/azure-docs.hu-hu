---
title: Az Azure biztonsági alapkonfigurációja Azure Cognitive Search
description: A Azure Cognitive Search alapkonfiguráció eljárásokkal kapcsolatos útmutatást és forrásforrásokat biztosít az Azure biztonsági teljesítménytesztben meghatározott biztonsági javaslatok megvalósításához.
author: msmbaldwin
ms.service: search
ms.topic: conceptual
ms.date: 03/16/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 313ac05d8e4fdc19736d0c35285c2b854ec26968
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/17/2021
ms.locfileid: "107589193"
---
# <a name="azure-security-baseline-for-azure-cognitive-search"></a>Az Azure biztonsági alapkonfigurációja Azure Cognitive Search

Ez a biztonsági alapkonfiguráció az Azure Biztonsági teljesítményteszt [1.0-s](../security/benchmarks/overview-v1.md) verziójának útmutatását alkalmazza a Azure Cognitive Search. Az Azure Security Benchmark ajánlásokat ad arra nézve, hogy hogyan tehetők biztonságossá a felhőalapú megoldások az Azure-ban. A tartalmat az Azure  biztonsági teljesítményteszt által meghatározott biztonsági vezérlők és a kapcsolódó, az alkalmazásra vonatkozó Azure Cognitive Search. **A** nem alkalmazható Azure Cognitive Search, vagy az ügyfél ki lett zárva.

Ha meg Azure Cognitive Search, hogyan lehet teljes mértékben leképezni az Azure-biztonsági teljesítménytesztre, tekintse meg a teljes Azure Cognitive Search [referenciakonfiguráció-leképezési fájlt.](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)

## <a name="network-security"></a>Hálózati biztonság

*További információ: [Azure Security Benchmark: Hálózati biztonság](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1.1: Azure-erőforrások védelme virtuális hálózatokon belül

**Útmutató:** Győződjön meg arról, Microsoft Azure Virtual Network összes alhálózati telepítésre alkalmazva van egy hálózati biztonsági csoport, amely szabályokat alkalmaz a "legkevésbé emelt szintű" hozzáférési séma megvalósításához. Csak az alkalmazás megbízható portjaihoz és IP-címtartományához engedélyezze a hozzáférést. A Azure Cognitive Search azure-beli privát végponttal helyezheti üzembe, ahol ez megvalósítható, hogy privát hozzáférést biztosítson a szolgáltatásokhoz a virtuális hálózatról.

Cognitive Search további hálózati biztonsági funkciókat is támogat a hálózati hozzáférés-vezérlési listák kezeléséhez. Konfigurálja a keresési szolgáltatást úgy, hogy csak megbízható forrásokkal való kommunikációt engedélyezzen úgy, hogy a tűzfalképességével korlátozza az adott nyilvános IP-címtartományok hozzáférését.

- [Privát végpontok konfigurálása a Azure Cognitive Search](service-create-private-endpoint.md)

- [A tűzfal Azure Cognitive Search konfigurálása](service-configure-firewall.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-nics"></a>1.2: Virtuális hálózatok, alhálózatok és hálózati hálózatok konfigurációjának és forgalmának figyelése és naplózása

**Útmutató:** Cognitive Search nem helyezhetők üzembe közvetlenül virtuális hálózatban. Ha azonban az ügyfélalkalmazás vagy az adatforrások egy virtuális hálózatban vannak, figyelheti és naplózhatja a hálózaton lévő összetevők forgalmát, beleértve a felhőbeli keresési szolgáltatásnak küldött kéréseket is. A standard javaslatok közé tartozik a hálózati biztonsági csoport folyamatnaplójának engedélyezése és a naplók küldése az Azure Storage-ba vagy egy Log Analytics-munkaterületre. Igény szerint használhatja a Traffic Analytics a forgalmi minták elemzéshez.

- [Hálózati biztonsági csoport forgalomnaplóinak engedélyezése](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [A Traffic Analytics](../network-watcher/traffic-analytics.md)

- [A hálózati biztonság Azure Security Center](../security-center/security-center-network-recommendations.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: Az ismert kártékony IP-címekkel való kommunikáció megtagadása

**Útmutató:** Cognitive Search nem biztosít konkrét funkciót az elosztott szolgáltatásmegtagadásos támadások elleni védelemhez, de az általános védelem érdekében engedélyezheti a DDoS Protection Standardot az Cognitive Search-szolgáltatáshoz társított virtuális hálózatokon.

- [A DDoS Protection konfigurálása](../ddos-protection/manage-ddos-protection.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="15-record-network-packets"></a>1.5: Hálózati csomagok rekordja

**Útmutató:** Engedélyezze a hálózati biztonsági csoport forgalomnaplóit az Azure Virtual Machines (VM) szolgáltatást védő hálózati biztonsági csoportok számára, amelyek csatlakozni fognak a Cognitive Search szolgáltatáshoz. Naplók küldése egy Azure Storage-fiókba a forgalom naplózása érdekében. 

Engedélyezze Network Watcher a csomagrögzítést, ha szükséges a rendellenes tevékenységek kivizsgálásához.

- [NSG-forgalom naplóinak engedélyezése](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [A Network Watcher](../network-watcher/network-watcher-create.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6: Hálózatalapú behatolásészlelési/behatolásvédelmi rendszerek (IDS/IPS) üzembe helyezése

**Útmutató:** Cognitive Search nem támogatja a hálózati behatolásészlelést, de a behatolások elhárítása érdekében a tűzfalszabályok konfigurálhatóak úgy, hogy meghatározzák az Cognitive Search által elfogadott IP-címeket. Konfigurál egy privát végpontot, hogy a keresési forgalom ne legyen elérhető a nyilvános internetről.

- [Ügyfél által kezelt kulcsok konfigurálása adattitkosításhoz](search-security-manage-encryption-keys.md)

- [Ügyfél által kezelt kulcsadatok lekért adatai az indexek és szinonimatérképek alapján](search-security-get-encryption-keys.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: A hálózati biztonsági szabályok összetettségének és adminisztratív terhelésének minimalizálása

**Útmutató:** Ha az Cognitive Search-ben indexelőket és képességkészleteket használ, szolgáltatáscímkék használatával olyan IP-címtartományokat képviselhet, amelyek engedéllyel rendelkezik a külső erőforrásokhoz való csatlakozáshoz. 

Engedélyezze vagy tiltsa le az erőforrásokhoz való forgalmat a szolgáltatáscímke nevének (például AzureCognitiveSearch) a szabály megfelelő forrás- vagy célmezőjében való megadásával. 

- [Virtuális hálózati szolgáltatáscímkék](../virtual-network/service-tags-overview.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="110-document-traffic-configuration-rules"></a>1.10: A forgalom konfigurációs szabályainak dokumentálása

**Útmutató:** Konfigurálhatja a Cognitive Search privát Azure-végponttal a keresési szolgáltatás virtuális hálózattal való integrálásához.  Használjon erőforráscímkéket a hálózati biztonsági csoportokhoz és a hálózati biztonsággal és a forgalommal kapcsolatos egyéb erőforrásokhoz. Az egyes hálózati biztonsági csoportok szabályaihoz használja a "Leírás" mezőt azon szabályok dokumentálása érdekében, amelyek engedélyezik a hálózatokról és hálózatokról származó forgalmat. 
 

A címkézéssel kapcsolatos beépített Azure Policy-definíciók (például a "Címke és az érték megkövetelése" hatás) használatával biztosíthatja, hogy az összes erőforrás címkékkel legyen létrehozva, és értesítse Önt a meglévő, címkézetlen erőforrásokról. 

A címkék alapján Azure PowerShell azure cli használatával erőforrásokat kereshet vagy hajthat végre műveleteken.  

 
- [Privát végpont létrehozása a Cognitive Search](service-create-private-endpoint.md) 

 
 
- [Címkék létrehozása és használata](../azure-resource-manager/management/tag-resources.md)

- [Azure-fiók Virtual Network](../virtual-network/quick-create-portal.md) 

 
- [Hálózati forgalom szűrése a hálózati biztonsági csoport szabályaival](../virtual-network/tutorial-filter-network-traffic.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

## <a name="logging-and-monitoring"></a>Naplózás és monitorozás

*További információ: [Azure biztonsági teljesítményteszt: Naplózás és monitorozás.](../security/benchmarks/security-control-logging-monitoring.md)*

### <a name="22-configure-central-security-log-management"></a>2.2: Központi biztonsági naplókezelés konfigurálása

**Útmutató:** A végponteszközök, hálózati erőforrások Cognitive Search más biztonsági rendszerek által generált biztonsági adatok Azure Monitor kapcsolatos naplók Azure Monitor be. A Azure Monitor Log Analytics-munkaterületek használatával végezhet lekérdezést és elemzést, valamint Azure Storage-fiókokat használhat a hosszú távú és archiválási tárterülethez. Azt is teheti, hogy engedélyezi és beveszi az adatokat, Azure Sentinel külső SIEM-et.
 

 
- [Első lépések a Azure Monitor külső SIEM-integrációval](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/)
 

 
- [Platformnaplók és -metrikák gyűjtése Azure Monitor](../azure-monitor/essentials/diagnostic-settings.md) 
 

 
- [A Azure Sentinel](../sentinel/quickstart-onboard.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Azure-erőforrások naplózásának engedélyezése

**Útmutató:** A diagnosztikai és működési naplók betekintést nyújtanak a Cognitive Search részletes műveleteibe, és hasznosak lehetnek a szolgáltatás monitorozásához és a szolgáltatáshoz hozzáférő számítási feladatokhoz.  A diagnosztikai adatok rögzítéséhez engedélyezze a naplózást a naplózási információk tároló helyének megadásával.
 

 
- [Naplóadatok gyűjtése és elemzése Azure Cognitive Search](search-monitor-logs.md) 

 
- [Platformnaplók és -metrikák gyűjtése Azure Monitor](../azure-monitor/essentials/diagnostic-settings.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Az [Azure-biztonsági](/azure/governance/policy/samples/azure-security-benchmark) teljesítményteszt a Security Center alapértelmezett szabályzati kezdeményezése, amely a Security Center [javaslatainak alapja.](/azure/security-center/security-center-recommendations) A Azure Policy vezérlővel kapcsolatos összes definíciót automatikusan engedélyezi a Security Center. Az ehhez a vezérlőhez kapcsolódó riasztások esetében szükség [lehet Azure Defender](/azure/security-center/azure-defender) kapcsolódó szolgáltatásokra vonatkozó tervre.

**Azure Policy definíciók – Microsoft.Search:**

[!INCLUDE [Resource Policy for Microsoft.Search 2.3](../../includes/policy/standards/asb/rp-controls/microsoft.search-2-3.md)]

### <a name="25-configure-security-log-storage-retention"></a>2.5: Biztonsági naplók tárolásának konfigurálása

**Útmutató:** A diagnosztikai metrikákba betáplált előzményadatokat alapértelmezés szerint 30 Cognitive Search őrzi meg a rendszer. A hosszabb megőrzés érdekében engedélyezze azt a beállítást, amely tárolási beállítást ad meg a naplózott események és metrikák megőrzéséhez.
 

 
A Azure Monitor meg a Log Analytics-munkaterület megőrzési időszakát a szervezet megfelelőségi szabályozásának megfelelően. Az Azure Storage-fiókok hosszú távú és archiválási tárterülethez használhatók. 
 

 
- [Az adatmegőrzési időtartam módosítása a Log Analyticsben](https://docs.microsoft.com/azure/azure-monitor/logs/manage-cost-storage#change-the-data-retention-period) 

 
- [Adatmegőrzési szabályzat konfigurálása az Azure Storage-fióknaplókhoz](https://docs.microsoft.com/azure/storage/common/manage-storage-analytics-logs#enable-logs)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="26-monitor-and-review-logs"></a>2.6: Naplók figyelése és áttekintése

**Útmutató:** Naplók elemzése és monitorzása a Cognitive Search szolgáltatásból rendellenes viselkedést keresve. A Azure Monitor Log Analytics használatával áttekinthet naplókat, és lekérdezéseket hajthatja végre a naplóadatokon. Másik lehetőségként engedélyezheti és bevetheti az adatokat Azure Sentinel külső SIEM-hez. 

 
 
- [Naplóadatok gyűjtése és elemzése Cognitive Search](search-monitor-logs.md)
 
- [Keresési napló adatainak vizualizációja a Power BI](search-monitor-logs-powerbi.md)
 

 
- [A Azure Sentinel](../sentinel/quickstart-onboard.md)
 

 
- [További tudnivalók a Log Analytics szolgáltatásról](../azure-monitor/logs/log-analytics-tutorial.md)
 

 
- [Egyéni lekérdezések végrehajtása a Azure Monitor](../azure-monitor/logs/get-started-queries.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7: Riasztások engedélyezése rendellenes tevékenységekhez

**Útmutató:** Az Security Center Log Analytics-munkaterülettel való használatával figyelhet és riasztásokat észlelhet a biztonsági naplókban és eseményekben található rendellenes tevékenységekről. Másik lehetőségként engedélyezheti és be is használhatja az Azure Sentinel.
 

 
- [A Azure Sentinel](../sentinel/quickstart-onboard.md)
 

 
- [Riasztások kezelése a Azure Security Center](../security-center/security-center-managing-and-responding-alerts.md)
 

 
- [Riasztás a Log Analytics-naplóadatokról](../azure-monitor/alerts/tutorial-response.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

## <a name="identity-and-access-control"></a>Identitás- és hozzáférés-vezérlés

*További információ: [Azure Biztonsági teljesítményteszt: Identitás és Access Control.](../security/benchmarks/security-control-identity-access-control.md)*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: A rendszergazdai fiókok nyilvántartása

**Útmutató:** Az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC) szerepkör-hozzárendelésekkel teszi lehetővé az Azure-erőforrásokhoz való hozzáférés kezelését. Ezeket a szerepköröket felhasználókhoz, csoportokhoz, szolgáltatásnévhez és felügyelt identitásokhoz rendelheti hozzá. Bizonyos erőforrásokhoz előre meghatározott beépített szerepkörök tartoznak, és ezek a szerepkörök olyan eszközökkel leltározhatók vagy kérdezhetők le, mint az Azure CLI, az Azure PowerShell vagy az Azure Portal.

Cognitive Search szerepkör olyan engedélyekkel van társítva, amelyek támogatják a szolgáltatásszintű felügyeleti feladatokat. Ezek a szerepkörök nem ják meg a hozzáférést a szolgáltatásvégponthoz. A végponton (például indexkezelés, indexsokaját és a keresési adatokon lekérdezések) található műveletekhez való hozzáférés API-kulcsokkal hitelesíti a kérést.

- [Szerepkörök beállítása rendszergazdai hozzáféréshez Azure Cognitive Search](search-security-rbac.md)

- [API-kulcsok létrehozása és kezelése egy Azure Cognitive Search szolgáltatáshoz](search-security-api-keys.md)

- [Címtárszerepkomó le Azure Active Directory (Azure AD) a PowerShell használatával](/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0&amp;preserve-view=true)

- [Címtárbeli szerepkör tagjainak lekérte az Azure AD-ban a PowerShell használatával](/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0&amp;preserve-view=true)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: Dedikált rendszergazdai fiókok használata

**Útmutató:** Cognitive Search nem rendelkezik olyan helyi szintű vagy Azure Active Directory- (Azure AD-) rendszergazdai fiókokkal, amelyek indexek és műveletek kezelésére használhatók. 

Használja az Azure AD beépített szerepköreit, amelyeket kifejezetten hozzá kell rendelni a felügyeleti műveletekhez. Az Azure AD PowerShell-modul meghívása alkalmi lekérdezések elvégzéséhez a rendszergazdai csoportok tagjainak felderítéséhez.

- [Szerepkörök használata rendszergazdai hozzáféréshez a Cognitive Search](search-security-rbac.md)

- [Címtárszerepkomó lekért szerepkör az Azure AD-ban a PowerShell használatával](/powershell/module/azuread/get-azureaddirectoryrole)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4: Egyszeri bejelentkezés (SSO) használata Azure Active Directory

**Útmutató:** SSO-hitelesítés használata Azure Active Directory (Azure AD) használatával a keresési szolgáltatás adataihoz való hozzáféréshez a Azure Resource Manager. 

Hozzon létre egy folyamatot, amely csökkenti az identitások és hitelesítő adatok számát azáltal, hogy engedélyezi az SSO-t a szolgáltatáshoz a szervezet meglévő identitásai számára.

- [Az SSO azure aD-val való használatának a](../active-directory/manage-apps/what-is-single-sign-on.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: Többtényezős hitelesítés használata minden Azure Active Directory hozzáféréshez

**Útmutató:** Engedélyezze Azure Active Directory (Azure AD) többtényezős hitelesítési funkcióját, és kövesse Security Center identitással és hozzáféréssel kapcsolatos javaslatait.

- [Többtényezős hitelesítés engedélyezése az Azure-ban](../active-directory/authentication/howto-mfa-getstarted.md) 

- [Identitás és hozzáférés figyelése a Azure Security Center](../security-center/security-center-identity-access.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: Dedikált gépek (Emelt szintű hozzáférésű munkaállomások) használata az összes felügyeleti feladathoz

**Útmutató:** Emelt szintű hozzáférésű munkaállomás (PAW) használata többtényezős hitelesítéssel, amely az Azure-erőforrásokba való bejelentkezésre és az ahhoz való hozzáférésre van konfigurálva.
 

 
- [A biztonságos, Azure által felügyelt munkaállomások](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)
 

 
- [Többtényezős Azure Active Directory (Azure AD) engedélyezése](../active-directory/authentication/howto-mfa-getstarted.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3.7: A rendszergazdai fiókokból származó gyanús tevékenységek naplózása és riasztása

**Útmutató:** Azure Active Directory (Azure AD) biztonsági jelentésekkel és monitorozással észlelheti, ha gyanús vagy nem biztonságos tevékenység történik a környezetben. A Security Center identitás- és hozzáférési tevékenységek figyelése.

- [A kockázatos tevékenységek miatt megjelölt Azure AD-felhasználók azonosítása](../active-directory/identity-protection/overview-identity-protection.md)

- [A felhasználók identitási és hozzáférési tevékenységeinek monitorozása az Azure Security Centerben](../security-center/security-center-identity-access.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="39-use-azure-active-directory"></a>3.9: A Azure Active Directory

**Útmutató:** A Azure Active Directory (Azure AD) használata központi hitelesítési és engedélyezési rendszerként a szolgáltatásszint-kezelési feladatokhoz a Azure Cognitive Search. Az Azure AD-identitások nem hálózati hozzáférést szolgáltatásokat stb.  Az olyan műveletekhez, mint az indexkezelés, az indexek populációja és a keresési adatok lekérdezései API-kulcsokon keresztül érhetők el.

- [Azure AD-példány létrehozása és konfigurálása](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

- [API-kulcsok létrehozása és kezelése egy Azure Cognitive Search szolgáltatáshoz](search-security-api-keys.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: A felhasználói hozzáférés rendszeres áttekintése és egyeztetése

**Útmutató:** Azure Active Directory (Azure AD) naplókat biztosít az elavult fiókok felderítésének segítésekor. Az Azure AD identitás- és hozzáférési felülvizsgálatok használatával hatékonyan kezelheti a csoporttagságokat, a vállalati alkalmazásokhoz való hozzáférést és a szerepkör-hozzárendeléseket. A felhasználói hozzáférés rendszeresen felülvizsgálható, hogy csak a megfelelő felhasználók férnek hozzá. 

Tekintse át a keresési szolgáltatásvégponton Cognitive Search naplókat, például az indexkezelést, az indexek sokaságát és a lekérdezéseket.

- [Az Azure AD-jelentéskészítés](/azure/active-directory/reports-monitoring/)

- [Az Azure AD identitás- és hozzáférési felülvizsgálatainak használata](../active-directory/governance/access-reviews-overview.md)

- [A tevékenységek és tevékenységek figyelése Azure Cognitive Search](search-monitor-usage.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3.11: Az inaktivált hitelesítő adatok elérésére tett kísérletek figyelése

**Útmutató:** Azure Active Directory-hozzáférés (Azure AD) bejelentkezési tevékenységének, naplózásának és kockázati eseménynaplóinak forrásai lehetővé teszik a SIEM- vagy monitorozási eszközök integrálását.

Egyszerűsítheti a folyamatot az Azure AD felhasználói fiókok diagnosztikai beállításainak létrehozásával, valamint az auditnaplók és bejelentkezési naplók Log Analytics-munkaterületre való küldésével. Konfigurálja a kívánt riasztásokat a Log Analytics-munkaterületen.

- [Azure-tevékenységnaplók integrálása Azure Monitor](/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12: Riasztás a fiók bejelentkezési viselkedésének eltéréséről

**Útmutató:** A Azure Active Directory (Azure AD) Identity Protection funkcióival konfigurálhatja a felhasználói identitásokkal kapcsolatos észlelt gyanús műveletekre adott automatikus válaszokat. Szükség szerint az adatokat Azure Sentinel be az adatokba további vizsgálathoz.

- [Az Azure AD kockázatos bejelentkezéseinek megtekintése](../active-directory/identity-protection/overview-identity-protection.md)

- [Az Identity Protection kockázati szabályzatok konfigurálása és engedélyezése](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md) 

- [A Azure Sentinel](../sentinel/quickstart-onboard.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

## <a name="data-protection"></a>Adatvédelem

*További információ: [Azure Security Benchmark: Adatvédelem](../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: Bizalmas adatok leltárának fenntartása

**Útmutató:** A címkék segítségével nyomon lehet követni a bizalmas adatokat tároló vagy feldolgozó Azure-erőforrásokat.

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: Bizalmas adatokat tároló vagy feldolgozó rendszerek elkülönítése

**Útmutató:** Különálló előfizetéseket és/vagy felügyeleti csoportokat implementálja a fejlesztéshez, teszteléshez és éles környezethez. Az erőforrásokat virtuális hálózattal/alhálózattal kell elválasztani, megfelelően címkézni kell, és egy hálózati biztonsági csoporton vagy hálózaton belül kell Azure Firewall. A bizalmas adatokat tároló vagy feldolgozó erőforrásokat el kell különíteni. A Private Link privát végpont konfigurálható a Cognitive Search.

- [További Azure-előfizetések létrehozása](../cost-management-billing/manage/create-subscription.md)

- [Címkék létrehozása és használata](../azure-resource-manager/management/tag-resources.md)

- [Privát végpont létrehozása a Cognitive Search](service-create-private-endpoint.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: Bizalmas adatok jogosulatlan átvitelének figyelése és blokkolása

**Útmutató:** A peremhálózaton található Azure Marketplace külső megoldással figyelje a bizalmas adatok jogosulatlan átvitelét, és blokkolja az ilyen átviteleket, miközben riasztja az információbiztonsági szakembereket.

A Microsoft kezeli a mögöttes platformot, és minden ügyféltartalmat bizalmasként kezel, és védelmet biztosít az ügyfelek adatvesztése és kitettsége ellen. Annak érdekében, hogy az Azure-beli ügyféladatok biztonságban maradnak, a Microsoft robusztus adatvédelmi vezérlőket és képességeket valósított meg és tart karban.

- [Az ügyféladatok Azure-beli védelmének ismertetése](../security/fundamentals/protection-customer-data.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: A bizalmas adatok azonosítása aktív felderítési eszközzel

**Útmutató:** Az adatazonosítási, besorolási és adatveszteség-megelőzési funkciók még nem érhetők el a Cognitive Search. Implementálja a külső megoldásokat, ha megfelelőségi célokból szükség van rá. 

A Microsoft kezeli a mögöttes platformot, és minden ügyféltartalmat bizalmasként kezel, és védelmet biztosít az ügyfelek adatvesztése és kitettsége ellen. Annak érdekében, hogy az Azure-beli ügyféladatok biztonságban maradnak, a Microsoft robusztus adatvédelmi vezérlőket és képességeket valósított meg és tart karban.

- [Az ügyféladatok Azure-beli védelmének ismertetése](../security/fundamentals/protection-customer-data.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="46-use-azure-rbac-to-manage-access-to-resources"></a>4.6: Az erőforrásokhoz való hozzáférés kezelése az Azure RBAC használatával

**Útmutató:** Szolgáltatásfelügyelethez használja az Azure szerepköralapú hozzáférés-vezérlését (Azure RBAC) a kulcsokhoz és konfigurációkhoz való hozzáférés kezeléséhez. A tartalomműveletekhez, például az indexeléshez és a lekérdezésekhez Cognitive Search kulcsokat használ identitásalapú hozzáférés-vezérlési modell helyett. Az Azure RBAC használatával szabályozhatja a kulcsokhoz való hozzáférést. 

 
- [Az Azure RBAC konfigurálása](../role-based-access-control/role-assignments-portal.md)  
 
 
- [Szerepkörök használata rendszergazdai hozzáféréshez a Cognitive Search](search-security-rbac.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: Bizalmas adatok titkosítása

**Útmutató:** Cognitive Search automatikusan titkosítja az indexelt tartalmat a Microsoft által felügyelt kulcsokkal. Ha nagyobb védelemre van szükség, az alapértelmezett titkosítást kiegészítheti egy második titkosítási réteggel, a biztonsági beállításokban létrehozott és Azure Key Vault.

- [Felhasználó által kezelt kulcsok konfigurálása adattitkosításhoz a Azure Cognitive Search](search-security-manage-encryption-keys.md)

- [Inaktív adatok Azure-ban való titkosításának ismertetése](../security/fundamentals/encryption-atrest.md)

**Felelősség**: Megosztott

**Azure Security Center monitorozás:** Nincs

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: Kritikus fontosságú Azure-erőforrások változásainak naplózása és riasztása

**Útmutató:** Azure Monitor Azure-tevékenységnaplóval való használatával riasztásokat hozhat létre, amikor változásokra kerül sor az éles példányok és Cognitive Search kritikus vagy kapcsolódó erőforrások esetén. 

 
- [Riasztások létrehozása az Azure-tevékenységnapló eseményeihez](../azure-monitor/alerts/alerts-activity-log.md) 

 
- [Riasztások létrehozása a Cognitive Search tevékenységekhez](search-monitor-logs.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

## <a name="inventory-and-asset-management"></a>Leltár-és eszközfelügyelet

*További információ: [Azure biztonsági teljesítményteszt: Leltár és eszközkezelés.](../security/benchmarks/security-control-inventory-asset-management.md)*

### <a name="61-use-automated-asset-discovery-solution"></a>6.1: Automatizált eszközfelderítési megoldás használata

**Útmutató:** A Azure Resource Graph használatával lekérdezheti és felderítheti az előfizetésében található összes erőforrást (például számítás, tárolás, hálózat, portok, protokollok stb.).

Győződjön meg arról, hogy megfelelő (olvasási) engedélyeket használ a bérlőben, és számba veszi az összes Azure-előfizetést, valamint az előfizetések erőforrásait.

- [Lekérdezések létrehozása az Azure Resource Graph Explorerrel](../governance/resource-graph/first-query-portal.md)

- [Az Azure-előfizetések megtekintése](https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-4.8.0&amp;preserve-view=true)

- [Az Azure RBAC](../role-based-access-control/overview.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="62-maintain-asset-metadata"></a>6.2: Objektum metaadatainak karbantartása

**Útmutató:** Címkék alkalmazása azure-erőforrásokra metaadatokkal, hogy logikailag rendszerezze őket egy taxonómiába.

- [Címkék létrehozása és használata](../azure-resource-manager/management/tag-resources.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: Nem engedélyezett Azure-erőforrások törlése

**Útmutató:** Az eszközök rendszerezéséhez és nyomon követéséhez használjon címkézést, felügyeleti csoportokat és különálló előfizetéseket, ha szükséges. Rendszeresen összeegyezteti a leltárat, és gondoskodik arról, hogy a jogosulatlan erőforrások időben törlődjenek az előfizetésből.

- [További Azure-előfizetések létrehozása](../cost-management-billing/manage/create-subscription.md)

- [Felügyeleti csoportok létrehozása](../governance/management-groups/create-management-group-portal.md)

- [Címkék létrehozása és használata](../azure-resource-manager/management/tag-resources.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="64-define-and-maintain-an-inventory-of-approved-azure-resources"></a>6.4: Jóváhagyott Azure-erőforrások meghatározása és nyilvántartása

**Útmutató:** A jóváhagyott Azure-erőforrások listájának meghatározása az indexeléshez és a képességkészlet-feldolgozáshoz a Cognitive Search.

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: Nem jóváhagyott Azure-erőforrások figyelése

**Útmutató:** Javasoljuk, hogy határozza meg azoknak az Azure-erőforrásoknak a leltárát, amelyeket a szervezeti szabályzatok és szabványok alapján jóváhagyott a használathoz, majd figyelje a nem jóváhagyott Azure-erőforrásokat a Azure Policy vagy Azure Resource Graph.

- [Az Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md) 

- [Lekérdezések létrehozása az Azure Graph használatával](../governance/resource-graph/first-query-portal.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="69-use-only-approved-azure-services"></a>6.9: Csak jóváhagyott Azure-szolgáltatások használata

**Útmutató:** Azure Policy az alábbi beépített szabályzatdefiníciók használatával az ügyfél-előfizetésben létre lehet hozható erőforrások típusára vonatkozó korlátozásokat:
- Nem engedélyezett erőforrástípusok
- Engedélyezett erőforrástípusok

A Azure Resource Graph használatával lekérdezheti vagy felderítheti az előfizetése(i) erőforrásait. Győződjön meg arról, hogy a környezetben jelen van minden Azure-erőforrás jóvá van hagyva.

- [Az Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md)

- [Adott erőforrástípus megtagadása a Azure Policy](/azure/governance/policy/samples/)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6.11: Korlátozza a felhasználók számára a Azure Resource Manager

**Útmutató:** Az Azure feltételes hozzáférés használatával korlátozhatja a felhasználók Azure Resource Manager-hozzáférését a "hozzáférés blokkolása" beállításával a "Microsoft Azure Management" alkalmazáshoz.  

 
Szabályozhatja a hozzáférést az összes többi műveletre vonatkozó kérelmek hitelesítéséhez használt kulcsokhoz, különösen a tartalommal és Cognitive Search.

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

## <a name="secure-configuration"></a>Biztonságos konfiguráció

*További információ: [Azure biztonsági teljesítményteszt: Biztonságos konfiguráció.](../security/benchmarks/security-control-secure-configuration.md)*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: Biztonságos konfigurációk létrehozása az összes Azure-erőforráshoz

**Útmutató:** A Azure Policy a "Microsoft.Search" névtérben létrehozott aliasokkal egyéni szabályzatokat hozhat létre a hálózati erőforrások konfigurációjának naplózására vagy Azure Cognitive Search kényszeríteni. Beépített definíciókat is Azure Policy a Cognitive Search, például a következő szolgáltatásokhoz:

Naplózás engedélyezése Azure-erőforrásokhoz

Azure Resource Manager képes exportálni a sablont az JavaScript Object Notation -ban (JSON), amelyet át kell vizsgálni annak biztosítása érdekében, hogy a konfigurációk megfelelnek a szervezet biztonsági követelményeinek.

Az Azure-erőforrások biztonságos alapkonfigurációjaként Azure Security Center az azure-erőforrásokra vonatkozó javaslatokat is használhatja.

- [Azure Policy megfelelőségi vezérlők a Azure Cognitive Search](security-controls-policy.md)

- [Az elérhető aliasok Azure Policy megtekintése](https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-4.8.0&amp;preserve-view=true)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: Biztonságos Azure-erőforráskonfigurációk fenntartása

**Útmutató:** A Azure Policy [deny] és a [deploy if not exist] hatás használatával kényszerítheti a biztonságos beállításokat a Cognitive Search szolgáltatás erőforrásain. 

Azure Resource Manager sablonokkal megőrizheti a szervezet által megkövetelt Azure-erőforrások biztonsági konfigurációját. 

- [A Azure Policy hatásainak](../governance/policy/concepts/effects.md)

- [Azure Policy megfelelőségi vezérlők a Azure Cognitive Search](security-controls-policy.md)

- [Szabályzatok létrehozása és kezelése a megfelelőség kikényszerítése céljából](../governance/policy/tutorials/create-and-manage.md)

- [Azure Resource Manager sablonok áttekintése](../azure-resource-manager/templates/overview.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Az Azure-erőforrások konfigurációjának biztonságos tárolása

**Útmutató:** Ha egyéni Azure Policy használ, az Azure DevOps vagy az Azure Repos használatával biztonságosan tárolhatja és kezelheti a kódot.

- [Kód tárolása az Azure DevOpsban](/azure/devops/repos/git/gitworkflow)

- [Az Azure Repos dokumentációja](/azure/devops/repos/index)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7: Konfigurációkezelési eszközök üzembe helyezése Azure-erőforrásokhoz

**Útmutató:** Szabványos biztonsági konfigurációkat definiálhat és implement Cognitive Search szolgáltatás erőforrásaihoz a Azure Policy. 

Aliasok használatával hozzon létre egyéni házirendeket a hálózati konfigurációk naplózásához vagy kikényszerításához. Az egyes erőforrásokhoz kapcsolódó beépített szabályzatdefiníciókat is használhat. 

Emellett a konfigurációs módosítások telepítéséhez Azure Automation a szabályzat kivételeit is használhatja. 

- [Azure Policy megfelelőségi vezérlők a Azure Cognitive Search](security-controls-policy.md)

- [Az Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9: Automatizált konfigurációfigyelés megvalósítása Azure-erőforrásokhoz

**Útmutató:** A Security Center a szolgáltatás erőforrásainak alapkonfiguráció-Cognitive Search végrehajtásához.  Emellett a Azure Policy az erőforrás-konfigurációk riasztása és naplózása. 

- [Javaslatok szervizlének Azure Security Center](../security-center/security-center-remediate-recommendations.md)

- [Azure Policy megfelelőségi vezérlők a Azure Cognitive Search](security-controls-policy.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="711-manage-azure-secrets-securely"></a>7.11: Azure-beli titkos kulcsok biztonságos kezelése

**Útmutató:** Az Azure Managed Identities és a Azure Key Vault segítségével egyszerűsítheti a felhőalkalmazások titkos adatok kezelését.

- [Felügyelt identitások használata Azure-erőforrásokhoz](../azure-app-configuration/howto-integrate-azure-managed-service-identity.md) 

- [Új Key Vault](../key-vault/general/quick-create-portal.md)

- [Felügyelt identitással Key Vault hitelesítés engedélyezése](../key-vault/general/assign-access-policy-portal.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: Identitások biztonságos és automatikus kezelése

**Útmutató:** Azure-beli felügyelt identitás használatával hozzáférést adhat Cognitive Search más Azure-szolgáltatásokhoz, például Key Vault- és indexelő-adatforráshoz egy automatikusan felügyelt identitással az Azure Active Directory -ban (Azure AD). A felügyelt identitások lehetővé teszik a hitelesítést bármely olyan szolgáltatásban, amely támogatja az Azure AD-hitelesítést, beleértve a Azure Key Vault is, anélkül, hogy a hitelesítő adatokat a kódban lenne. 

- [Indexelői kapcsolat beállítása egy adatforráshoz felügyelt identitás használatával](search-howto-managed-identities-data-sources.md)

- [Felhasználó által kezelt kulcsok konfigurálása adattitkosításhoz felügyelt identitás használatával](search-security-manage-encryption-keys.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

## <a name="malware-defense"></a>Kártevők elleni védelem

*További információ: [Azure biztonsági teljesítményteszt: Kártevővédelem.](../security/benchmarks/security-control-malware-defense.md)*

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2: A nem számítási Azure-erőforrásokba feltölteni szükséges fájlok előzetes beolvasása

**Útmutató:** A nem számítási Azure-erőforrásokra (például Cognitive Search, Blob Storage, Azure SQL Database stb.) feltöltött tartalmak előzetes beolvasása. 

Az Ön felelőssége, hogy előzetesen megvizsgálja a nem számítási Azure-erőforrásokra feltöltött tartalmakat. A Microsoft nem fér hozzá az ügyféladatokhoz, ezért nem tudja az Ön nevében kártevőirtó vizsgálatot végezni az ügyféltartalmakban.

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="83-ensure-antimalware-software-and-signatures-are-updated"></a>8.3: Kártevőirtó szoftverek és aláírások frissítésének biztosítása

**Útmutató:** Nem alkalmazható a Cognitive Search. Nem engedélyezi kártevőirtó megoldások telepítését az erőforrásaira. A mögöttes platformhoz a Microsoft kezeli a kártevőirtó szoftverek és aláírások frissítését.  

 
A szervezet tulajdonában lévő és a keresési megoldásban használt számítási erőforrások esetében kövesse az Security Center Compute Apps javaslatait, hogy az összes végpont naprakész legyen a legújabb &amp; aláírásokkal. Linux rendszeren használjon külső kártevőirtó megoldást.

**Felelősség**: Megosztott

**Azure Security Center:** Nincs

## <a name="data-recovery"></a>Adat-helyreállítás

*További információ: [Azure biztonsági teljesítményteszt: Adat-helyreállítás.](../security/benchmarks/security-control-data-recovery.md)*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: Rendszeres automatikus biztonságimentás-frissítések biztosítása

**Útmutató:** A keresési szolgáltatásban tárolt tartalmakról nem lehet biztonságimenteni az Azure Backup-ban vagy más beépített mechanizmusban, de újraépíthet egy indexet az alkalmazás forráskódja és elsődleges adatforrása alapján, vagy létrehozhat egy egyéni eszközt az indexelt tartalmak lekéréséhez és tárolására. 

 
- [GitHub index-biztonsági mentés-visszaállítási minta](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/index-backup-restore)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: Teljes rendszer-biztonsági mentések és ügyfél által kezelt kulcsok biztonsági mentése

**Útmutató:** Cognitive Search jelenleg nem támogatja a keresési szolgáltatásban lévő adatok automatikus biztonsági mentését, és manuális folyamattal kell biztonsági másolatot készíteni. Az ügyfelek által felügyelt kulcsokról a biztonsági Azure Key Vault.
 

- [Biztonsági mentés és visszaállítás Azure Cognitive Search indexben](/samples/azure-samples/azure-search-dotnet-samples/azure-search-backup-restore-index/)

- [Biztonsági másolat készítése Key Vault kulcsokról az Azure-ban](https://docs.microsoft.com/powershell/module/az.keyvault/backup-azkeyvaultkey?view=azps-4.8.0&amp;preserve-view=true)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: Az összes biztonsági mentés ellenőrzése, beleértve az ügyfél által kezelt kulcsokat is

**Útmutató:** Cognitive Search nem támogatja a keresési szolgáltatásban lévő adatok automatikus biztonsági mentését, és manuális folyamattal kell biztonsági másolatot készíteni és visszaállítani. Rendszeres időközönként végezze el a manuálisan biztonsági mentéssel biztonsági mentést készítő tartalmak adatainak visszaállítását, hogy biztosítsa a biztonsági mentési folyamat teljes integritását.

- [Biztonsági mentés és visszaállítás Azure Cognitive Search indexben](/samples/azure-samples/azure-search-dotnet-samples/azure-search-backup-restore-index/)

- [Új kulcsok visszaállítása Key Vault Azure-ban](https://docs.microsoft.com/powershell/module/az.keyvault/restore-azkeyvaultkey?view=azps-4.8.0&amp;preserve-view=true)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: A biztonsági másolatok és az ügyfél által kezelt kulcsok védelmének biztosítása

**Útmutató:** Cognitive Search nem támogatja a keresési szolgáltatásban lévő adatok automatikus biztonsági mentését, és manuális folyamattal kell biztonsági másolatot készíteni.  Az ügyfelek által felügyelt kulcsokról a biztonsági Azure Key Vault.  

 
A kulcsok véletlen vagy rosszindulatú törléssel szembeni védelme érdekében engedélyezze a Key Vault és a végleges törlés elleni védelmet a kulcsban. Ha az Azure Storage a manuális biztonsági mentések tárolására szolgál, engedélyezze a helyreállítható törlést az adatok mentéséhez és helyreállításához blobok vagy blob-pillanatképek törlésekor. 
 

 
- [Biztonsági mentés és visszaállítás egy Azure Cognitive Search indexről](/samples/azure-samples/azure-search-dotnet-samples/azure-search-backup-restore-index/)
 

 
- [Helyreállítható törlés és a végleges törléssel szembeni védelem engedélyezése kulcstartóban](../storage/blobs/soft-delete-blob-overview.md) 

- [Az Azure Blob Storage soft delete parancsa](../storage/blobs/soft-delete-blob-overview.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

## <a name="incident-response"></a>Incidensmegoldás

*További információ: [Azure Security Benchmark: Incidensek kezelése](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10.1: Incidensválasz-útmutató létrehozása

**Útmutató:** Incidensválasz-útmutató kidolgozása a szervezet számára. Győződjön meg arról, hogy vannak olyan írásos incidenskezelési tervek, amelyek meghatározzák a személyzet összes szerepkörét, valamint az incidenskezelés és -kezelés fázisát az észleléstől az incidens utáni felülvizsgálaton át az incidensek áttekintéséhez.

- [Útmutató saját biztonsági incidensek válaszadási folyamatának építéshez](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Microsoft Biztonsági reagálási központ az incidensek felépítése](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Az ügyfelek az NIST számítógépes biztonsági incidenskezelési útmutatóját is kihasználva segíthetnek a saját incidenskezelési tervük létrehozásában](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: Incidenspontozási és -rangsorolási eljárás létrehozása

**Útmutató:** Security Center súlyosságot rendel az egyes riasztásokhoz, így fontossági sorrendbe adhatja, hogy mely riasztásokat kell először megvizsgálni. A súlyosság azon alapul, hogy Security Center mennyire magabiztos a riasztás kiadásához használt eredményben vagy analitikus módon, valamint az alapján, hogy a riasztáshoz vezető tevékenység mögött rosszindulatú szándék volt-e.

Emellett címkék használatával jelölje meg az előfizetéseket, és hozzon létre egy elnevezési rendszert az Azure-erőforrások azonosításához és kategorizálásához, különösen a bizalmas adatokat feldolgozók számára. Az Ön felelőssége, hogy rangsorolja a riasztások szervizelését azon Azure-erőforrások és -környezet kritikussága alapján, ahol az incidens történt.

- [Címkék használata az Azure-erőforrások rendszerezéséhez](../azure-resource-manager/management/tag-resources.md)

- [Biztonsági riasztások az Azure Security Centerben](../security-center/security-center-alerts-overview.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="103-test-security-response-procedures"></a>10.3: Biztonsági reagálási eljárások tesztelése

**Útmutató:** Gyakorlatok a rendszerek incidensválasz-képességeinek rendszeres teszteléséhez. Azonosítsa a gyenge pontokat és réseket, és szükség esetén dolgozza át a tervet.

- [Tekintse meg az NIST "Útmutató a teszteléshez, a betanításhoz és a gyakorlathoz programok it-csomagokhoz és képességekhez" kiadványát](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: Adja meg a biztonsági incidens kapcsolattartási adatait, és konfigurálja a riasztási értesítéseket a biztonsági incidensek esetén

**Útmutató:** A Biztonsági incidensek kapcsolattartási adatait a Microsoft arra használja, hogy kapcsolatba lépjen Önnel, ha a Microsoft Biztonsági reagálási központ (MSRC) azt tapasztalja, hogy illetéktelen vagy jogosulatlan fél fért hozzá adataihoz. Tekintse át az incidenseket a tény után, és győződjön meg arról, hogy a problémák megoldódnak.

- [A biztonsági kapcsolattartó Azure Security Center beállítása](../security-center/security-center-provide-security-contact-details.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: Biztonsági riasztások beépítése az incidensválasz-rendszerbe

**Útmutató:** Az Security Center riasztások és javaslatok exportálása a Folyamatos exportálás funkcióval. A folyamatos exportálás lehetővé teszi a riasztások és javaslatok manuális vagy folyamatos exportálását. A riasztásokat a Security Center adat-összekötővel streamelheti a Azure Sentinel.

- [Folyamatos exportálás konfigurálása](../security-center/continuous-export.md)

- [Riasztások streamelése az Azure Sentinelbe](../sentinel/connect-azure-security-center.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: A biztonsági riasztások válaszának automatizálása

**Útmutató:** A munkafolyamat-automatizálási szolgáltatás Azure Security Center a biztonsági riasztások és javaslatok "Logic Apps" funkcióját használva automatikusan aktiválhatja a válaszokat.

- [A munkafolyamat-automatizálás és -Logic Apps](../security-center/workflow-automation.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

## <a name="penetration-tests-and-red-team-exercises"></a>Behatolási tesztek és Red Team-gyakorlatok

*További információ: [Azure biztonsági teljesítményteszt: Behatolási tesztek és Red Team-gyakorlatok.](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md)*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11.1: Az Azure-erőforrások rendszeres behatolástesztelése és az összes kritikus fontosságú biztonsági eredmény szervizelésének biztosítása

**Útmutató:** Kövesse a Microsoft Cloud behatolástesztelési szabályait annak ellenőrzéséhez, hogy a behatolási tesztek nem sértik-e meg a Microsoft szabályzatát. A Microsoft által felügyelt felhőalapú infrastruktúrán, szolgáltatásokon és alkalmazásokon végzett riasztási és élő behatolási tesztek végrehajtásához használja a Microsoft stratégiáját és végrehajtási tervét.

- [Behatolástesztelési beavatkozási szabályok](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)

- [Riasztási tesztek a Microsoft-felhőben](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Felelősség**: Megosztott

**Azure Security Center:** Nincs

## <a name="next-steps"></a>Következő lépések

- [Az Azure Security Benchmark v2 áttekintésének](/azure/security/benchmarks/overview) megtekintése
- További tudnivalók az [Azure biztonsági alapterveiről](/azure/security/benchmarks/security-baselines-overview)
