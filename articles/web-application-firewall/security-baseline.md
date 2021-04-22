---
title: Az Azure biztonsági alapkonfigurációja Azure Web Application Firewall
description: A Azure Web Application Firewall alapkonfiguráció eljárásokkal kapcsolatos útmutatást és forrásforrásokat biztosít az Azure biztonsági teljesítménytesztben meghatározott biztonsági javaslatok megvalósításához.
author: msmbaldwin
ms.service: web-application-firewall
ms.topic: conceptual
ms.date: 04/08/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 6aa3e2b84c4349e2134ddeb2a60fd193f56f84e8
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2021
ms.locfileid: "107875960"
---
# <a name="azure-security-baseline-for-azure-web-application-firewall"></a>Az Azure biztonsági alapkonfigurációja Azure Web Application Firewall

Ez a biztonsági alapkonfiguráció az Azure Biztonsági teljesítményteszt [1.0-s](../security/benchmarks/overview-v1.md) verziójának útmutatását alkalmazza a Azure Web Application Firewall. Az Azure Security Benchmark ajánlásokat ad arra nézve, hogy hogyan tehetők biztonságossá a felhőalapú megoldások az Azure-ban. A tartalmat az Azure  biztonsági teljesítményteszt által meghatározott biztonsági vezérlők és a kapcsolódó, az Azure Web Application Firewall. 

> [!NOTE]
> **A** nem alkalmazható Azure Web Application Firewall, vagy amelyekért a felelősség a Microsoft felelős, ki vannak zárva. Ha meg Azure Web Application Firewall, hogyan lehet teljes mértékben leképezni az Azure-biztonsági teljesítménytesztre, tekintse meg a teljes Azure Web Application Firewall **[referenciakonfiguráció-leképezési fájlt.](https://github.com/MicrosoftDocs/SecurityBenchmarks/raw/master/Azure%20Offer%20Security%20Baselines/1.1/azure-web-application-firewall-security-baseline-v1.1.xlsx)**

## <a name="network-security"></a>Hálózati biztonság

*További információ: [Azure Security Benchmark: Hálózati biztonság](../security/benchmarks/security-control-network-security.md).*

### <a name="13-protect-critical-web-applications"></a>1.3: Kritikus fontosságú webalkalmazások védelme

**Útmutató:** A Microsoft Azure Web Application Firewall (WAF) használata a webalkalmazások olyan gyakori biztonsági rések elleni központi védelmére, mint az SQL-injektálás és a webhelyek közötti parancsfájlok használata. 

- Észlelési mód: Ezzel a móddal megismerheti a hálózati forgalmat, valamint megismerheti és áttekintheti a téves riasztásokat. Figyeli és naplózza az összes fenyegetési riasztást. Ellenőrizze, hogy a Diagnosztikai és WAF-napló ki van-e választva és be van-e kapcsolva. Vegye figyelembe, hogy a WAF nem blokkolja a bejövő kéréseket, ha észlelési módban működik.
- Megelőzési mód: Letiltja a szabályok által észlelt behatolásokat és támadásokat. A támadó egy "403-as jogosulatlan hozzáférés" kivételt kap, és bezárja a kapcsolatot. A megelőzési mód rögzíti az ilyen támadásokat a WAF-naplókban.

A hálózati forgalom alapkonfigurálása a WAF észlelési módjával. Miután meghatározta a forgalmi igényeket, konfigurálja a WAF-ot Megelőzés módban a nemkívánatos forgalom kiolvassa.

Kövesse a waf által nem Security Center webes erőforrásokra vonatkozó magas súlyossági szintű javaslatokat.  

- [Web Application Firewall CRS-szabálycsoportok és -szabályok létrehozása](ag/application-gateway-crs-rulegroups-rules.md) 

- [WAF-módok a Application Gateway](https://docs.microsoft.com/azure/web-application-firewall/ag/ag-overview#waf-modes)

- [WAF-módok a Front Door](https://docs.microsoft.com/azure/web-application-firewall/afds/afds-overview#waf-modes)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: Az ismert kártékony IP-címekkel való kommunikáció megtagadása

**Útmutató:** Egyéni szabályok használata a Azure Web Application Firewall (WAF) a forgalom engedélyezése és blokkolása érdekében. Például egy IP-címtartományból érkező összes forgalom blokkolható. Konfigurálja az Azure WAF-et megelőzési módban való futtatásra, amely blokkolja a szabályok által észlelt behatolásokat és támadásokat. A támadó egy "403-as jogosulatlan hozzáférés" kivételt kap, és lezárja a kapcsolatot. A megelőzési mód rögzíti az ilyen támadásokat a WAF-naplókban.

- [WAF-módok a Application Gateway](https://docs.microsoft.com/azure/web-application-firewall/ag/ag-overview#waf-modes)

- [WAF-módok a Front Door](https://docs.microsoft.com/azure/web-application-firewall/afds/afds-overview#waf-modes)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Az [Azure-biztonsági](/azure/governance/policy/samples/azure-security-benchmark) teljesítményteszt a Security Center alapértelmezett szabályzati kezdeményezése, amely a Security Center [javaslatainak alapja.](/azure/security-center/security-center-recommendations) A Azure Policy vezérlővel kapcsolatos összes definíciót automatikusan engedélyezi a Security Center. Az ehhez a vezérlőhez kapcsolódó riasztások esetében szükség [lehet Azure Defender](/azure/security-center/azure-defender) kapcsolódó szolgáltatásokra vonatkozó tervre.

**Azure Policy definíciók – Microsoft.Network:**

[!INCLUDE [Resource Policy for Microsoft.Network 1.4](../../includes/policy/standards/asb/rp-controls/microsoft.network-1-4.md)]

### <a name="17-manage-traffic-to-web-applications"></a>1.7: Webalkalmazások forgalmának kezelése

**Útmutató:** Azure Web Application Firewall (WAF) az Azure webalkalmazás-védelmének alapvető összetevője. Az Azure WAF segítségével központosított védelmet nyújthat a webalkalmazások számára a gyakori biztonsági rések ellen az OWASP TOP 10 kategóriából származó, ismert támadási aláírásokkal szembeni előre konfigurált felügyelt szabálykészlet segítségével.

Szabja testre az Azure WAF-et szabályokkal és szabálycsoportokkal a webalkalmazás követelményeinek megfelelően, és szüntesse meg a téves riasztásokat. Társít egy Azure WAF-szabályzatot a WAF mögötti minden helyhez, hogy lehetővé tegye a helyspecifikus konfigurációt. Az Azure WAF támogatja a geoszűrés, a sebességkorlátozás és az Azure által felügyelt alapértelmezett szabálykészlet-szabályokat. és egyéni szabályok is létrehozhatóak az alkalmazás igényeinek megfelelően. 

Konfigurálja az Azure WAF-et megelőzési módban való futtatásra, miután meghatározott ideig az észlelési módban alapozta a hálózati forgalmat. Az Azure WAF megelőzési módban blokkolja a szabályok által észlelt behatolásokat és támadásokat. A támadó egy "403-as jogosulatlan hozzáférés" kivételt kap, és bezárja a kapcsolatot. A megelőzési mód rögzíti az ilyen támadásokat a WAF-naplókban.

- [WAF-módok a Application Gateway](https://docs.microsoft.com/azure/web-application-firewall/ag/ag-overview#waf-modes)

- [WAF-módok a Front Door](https://docs.microsoft.com/azure/web-application-firewall/afds/afds-overview#waf-modes)

- [Webalkalmazási tűzfal CRS-szabálycsoportai és szabályai](https://docs.microsoft.com/azure/web-application-firewall/ag/application-gateway-crs-rulegroups-rules?tabs=owasp31)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: A hálózati biztonsági szabályok összetettségének és adminisztratív terhelésének minimalizálása

**Útmutató:** Erőforrások létrehozása, társítása és logikai rendszerezése egy Azure-előfizetésben címkékkel a gyakori alkalmazáskonfigurációk (például az Apache és az IIS) észleléséhez. 

Szabályok és szabálycsoportok alkalmazása Azure Web Application Firewall (WAF) szabályzatra az alkalmazott címkemetaadatok alapján.

- [WAF-szabályzat Application Gateway](/cli/azure/network/application-gateway/waf-policy)

- [WAF-szabályzat Front Door](/cli/azure/network/front-door/waf-policy)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="110-document-traffic-configuration-rules"></a>1.10: A forgalom konfigurációs szabályainak dokumentálása

**Útmutató:** Címkék használata az Azure Application Gateway-alhálózatban található Azure Web Application Firewall (WAF) alhálózathoz társított hálózati biztonsági csoportokhoz, valamint a hálózati biztonsághoz és a forgalomhoz kapcsolódó egyéb erőforrásokhoz. Az egyes hálózati biztonsági csoportok szabályaihoz a "Leírás" mezőben adhatja meg az üzleti igényeket, az időtartamot stb. a hálózatokra vagy hálózatokról való forgalmat engedélyező szabályokra.

A címkézéshez kapcsolódó beépített Azure Policy-definíciók (például "Címke és érték megkövetelése" ) használatával biztosíthatja, hogy az összes erőforrás címkékkel legyen létrehozva, és értesítést küld a meglévő, címkézetlen erőforrásokról.

A Azure PowerShell vagy az Azure CLI használatával erőforrásokat keres vagy hajt végre a címkék alapján.

- [Címkék létrehozása és használata](../azure-resource-manager/management/tag-resources.md)

- [Új Virtual Network](../virtual-network/quick-create-portal.md)

- [NSG létrehozása biztonsági konfigurációval](../virtual-network/tutorial-filter-network-traffic.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: Automatizált eszközök használata a hálózati erőforrások konfigurációjának figyelése és a változások észlelése érdekében

**Útmutató:** Az Azure-tevékenységnapló segítségével monitorozhatja a hálózati erőforrás-konfigurációkat, és észlelheti a hálózati beállítások és az erőforrások változásait a Azure Web Application Firewall (WAF) üzemelő példányainál. Hozzon létre riasztásokat a Azure Monitor, amelyek a kritikus hálózati beállítások vagy erőforrások módosításakor aktiválódnak.

- [Az Azure-tevékenységnapló eseményeinek megtekintése és lekérése](https://docs.microsoft.com/azure/azure-monitor/essentials/activity-log#view-the-activity-log)

- [Riasztások létrehozása a Azure Monitor](../azure-monitor/alerts/alerts-activity-log.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

## <a name="logging-and-monitoring"></a>Naplózás és monitorozás

*További információ: [Azure biztonsági teljesítményteszt: Naplózás és monitorozás.](../security/benchmarks/security-control-logging-monitoring.md)*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1: Jóváhagyott időszinkronizálási források használata

**Útmutató:** Hozzon létre egy hálózati szabályt a Azure Web Application Firewall (WAF) számára, amely engedélyezi a megfelelő porttal és protokollal (például az UDP-n keresztül a 123-as porton) található NTP-kiszolgálóhoz való hozzáférést.

**Felelősség**: Megosztott

**Azure Security Center:** Nincs

### <a name="22-configure-central-security-log-management"></a>2.2: Központi biztonsági naplókezelés konfigurálása

**Útmutató:** Konfigurálja a Azure Web Application Firewall (WAF) naplóit, amelyek elküldendők egy központi biztonsági naplókezelési megoldásnak, például a Azure Sentinel vagy egy külső SIEM-nek. Ezek a naplók tartalmazzák az Azure-tevékenység-, diagnosztikai és valós idejű WAF-naplókat, ezeket a naplókat aztán különböző eszközökben lehet megtekinteni, például Azure Monitor, Excelben és Power BI. Azure Web Application Firewall naplók betekintést nyújtanak abba, hogy az Azure WAF milyen adatokat értékel, egyeztet és blokkol.

Azure Sentinel beépített Azure WAF-munkafüzetet tartalmaz, amely áttekintést nyújt az Azure WAF biztonsági eseményeiről. Ez a munkafüzet eseményeket, egyező és letiltott szabályokat, valamint minden mást tartalmaz, amely a tűzfalnaplókba kerül. Ezzel a telemetriával automatizálhatja a forgatókönyveket, és értesítheti őket, vagy szervizelési műveleteket is végre lehet venni a Azure Sentinel.

- [Tevékenységnaplók megtekintése](../azure-resource-manager/management/view-activity-logs.md)

- [Diagnosztikai naplók Application Gateway](../application-gateway/application-gateway-diagnostics.md)

- [Adatok csatlakoztatása a Microsoft webalkalmazási tűzfalról az Azure Sentinel](../sentinel/connect-azure-waf.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Azure-erőforrások naplózásának engedélyezése

**Útmutató:** Naplózás engedélyezése a Azure Web Application Firewall (WAF) erőforrásain a naplózási, biztonsági és diagnosztikai naplókhoz való hozzáféréshez. Azure Web Application Firewall részletes jelentéseket biztosít a konfigurált diagnosztikai naplókban elérhető összes észlelt fenyegetésről. Az automatikusan elérhető tevékenységnaplók közé tartozik az eseményforrás, a dátum, a felhasználó, az időbélyeg, a forráscímek, a célcímek és egyéb hasznos elemek.

- [Naplózás áttekintése](https://docs.microsoft.com/azure/web-application-firewall/ag/ag-overview#logging)

- [Azure Monitor naplólekérdezés áttekintése](../azure-monitor/logs/log-query-overview.md)

- [Az Azure Platform naplóinak áttekintése](../azure-monitor/essentials/platform-logs-overview.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="25-configure-security-log-storage-retention"></a>2.5: A biztonsági naplók tárolásának megőrzésének konfigurálása

**Útmutató:** A Azure Web Application Firewall (WAF) naplóit egy egyéni tárfiókba küldheti, és meghatározhatja a megőrzési szabályzatot. A Azure Monitor meg a Log Analytics-munkaterület megőrzési időszakát a szervezet megfelelőségi követelményei alapján.
- [Monitorozás konfigurálása tárfiókhoz](https://docs.microsoft.com/azure/storage/common/manage-storage-analytics-logs#configure-logging)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="26-monitor-and-review-logs"></a>2.6: Naplók figyelése és áttekintése

**Útmutató:** Azure Web Application Firewall (WAF) részletes jelentéseket biztosít az egyes észlelt fenyegetésekről. A naplózás integrálva van Azure Diagnostics naplókba, amelyek részletes információkat biztosítanak a naplózáshoz és a hibaelhárításhoz fontos műveletekről és hibákról. 

Az Azure WAF-példányok integrálva vannak a Security Center, hogy riasztásokat és állapotadatokat küldjenek a jelentéskészítéshez. A Security Center a nem védett webalkalmazások észlelésére és a sebezhető erőforrások védelmére vonatkozó javaslatait. 

Azure Sentinel WAF - tűzfalesemények munkafüzete, amely áttekintést nyújt a WAF biztonsági eseményeiről. Ezek közé tartoznak az események, az egyező és letiltott szabályok, valamint minden más, a tűzfalnaplókba naplózott adatok.

- [Diagnosztikai beállítások engedélyezése az Azure-tevékenységnaplóhoz](/azure/azure-monitor/platform/activity-log)

- [Diagnosztikai beállítások engedélyezése a Azure Application Gateway](../application-gateway/application-gateway-diagnostics.md)

- [Metrikák és naplók monitorozása a Azure Front Door](../frontdoor/front-door-diagnostics.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7: Riasztások engedélyezése rendellenes tevékenységekhez

**Útmutató:** Engedélyezze az Azure-tevékenységnapló diagnosztikai beállításait, valamint az Azure WAF diagnosztikai beállításait, és küldje el a naplókat egy Log Analytics-munkaterületre. Lekérdezések végrehajtása a Log Analyticsben kifejezések kereséséhez, trendek azonosításához, minták elemzéséhez, valamint az összegyűjtött adatokon alapuló számos további elemzéshez. Hozzon létre riasztásokat a rendellenes tevékenységekhez WAF-metrikák alapján. Ha például blokkolva van az X-et meghaladó kérelmek száma, akkor az "Y" lesz.

- [Diagnosztikai beállítások engedélyezése az Azure-tevékenységnaplóhoz](/azure/azure-monitor/essentials/diagnostic-settings-legacy)

- [Riasztások létrehozása az Azure-ban](../azure-monitor/alerts/tutorial-response.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="28-centralize-anti-malware-logging"></a>2.8: A kártevőirtó naplózás központosította

**Útmutató:** A Azure Web Application Firewall (WAF) üzembe helyezése kritikus webalkalmazások előtt a bejövő forgalom további vizsgálat céljából. 

Az Azure WAF központosított védelmet nyújt a webalkalmazások számára a gyakori biztonsági rések ellen, és a bejövő webes forgalom vizsgálatán keresztül blokkolja az olyan támadásokat, mint az SQL-injekting, a webhelyek közötti parancsfájlok, a kártevőfeltöltések és a DDoS-támadások.

- [Az Azure WAF üzembe helyezése](ag/create-waf-policy-ag.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

## <a name="identity-and-access-control"></a>Identitás- és hozzáférés-vezérlés

*További információ: [Azure Biztonsági teljesítményteszt: Identitás és Access Control.](../security/benchmarks/security-control-identity-access-control.md)*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: A rendszergazdai fiókok leltárának fenntartása

**Útmutató:** Azure Active Directory (Azure AD) beépített szerepkörei lekérdezhetőek, és explicit módon kell hozzárendelni. Az Azure AD PowerShell-modullal alkalmi lekérdezéseket hajt végre a rendszergazdai csoportok tagjainak felderítéséhez.

- [Címtárszerepkomó lekért szerepkör az Azure AD-ban a PowerShell használatával](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0&amp;preserve-view=true)

- [Címtárbeli szerepkör tagjainak lekérte az Azure AD-ban a PowerShell használatával](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0&amp;preserve-view=true)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: Dedikált rendszergazdai fiókok használata

**Útmutató:** A WAF-hez nem érhetők el helyi rendszergazdai hozzárendelések. Előfordulhatnak azonban olyan Azure Active Directory (Azure AD) rendszergazdai szerepkörök a környezetben, amelyek lehetővé tehetik az Azure WAF-erőforrások felügyeletének szabályozását.
Bevált gyakorlat szabványos üzemeltetési eljárásokat létrehozni az olyan dedikált rendszergazdai fiókok használatával kapcsolatban, amelyek hozzáféréssel Azure Web Application Firewall WAF-példányokhoz. A Security Center és hozzáférés-kezelési funkcióival figyelheti a rendszergazdai fiókok számát.

- [Az Azure Security Center és a hozzáférés](../security-center/security-center-identity-access.md)

- [Rendszergazdai felhasználók létrehozása a Azure Database for PostgreSQL](https://docs.microsoft.com/azure/postgresql/howto-create-users#the-server-admin-account)

- [A Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: Többtényezős hitelesítés használata minden Azure Active Directory-alapú hozzáféréshez

**Útmutató:** Engedélyezze Azure Active Directory (Azure AD) többtényezős hitelesítést, és kövesse Security Center identitás- és hozzáférés-kezelési javaslatait.

- [Többtényezős hitelesítés engedélyezése az Azure-ban](../active-directory/authentication/howto-mfa-getstarted.md)

- [Identitás és hozzáférés figyelése a Azure Security Center](../security-center/security-center-identity-access.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: Dedikált gépek (Emelt szintű hozzáférésű munkaállomások) használata az összes felügyeleti feladathoz

**Útmutató:** Emelt szintű hozzáférésű munkaállomás (PAW) használata többtényezős hitelesítéssel, amely az Azure Web Application Firewall (WAF) és a kapcsolódó erőforrásokba való bejelentkezésre és konfigurálásra van konfigurálva.

- [Tudnivalók a Privileged Access munkaállomásról](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

- [Többtényezős hitelesítés engedélyezése az Azure-ban](../active-directory/authentication/howto-mfa-getstarted.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3.7: A rendszergazdai fiókokból származó gyanús tevékenységek naplózása és riasztása

**Útmutató:** Azure Active Directory (Azure AD) biztonsági jelentések használata naplók és riasztások generálása érdekében, ha gyanús vagy nem biztonságos tevékenység történik a környezetben. A Security Center identitás- és hozzáférési tevékenységek figyelése.

- [A kockázatos tevékenységek miatt megjelölt Azure AD-felhasználók azonosítása](../active-directory/identity-protection/overview-identity-protection.md)

- [A felhasználók identitási és hozzáférési tevékenységeinek monitorozása az Azure Security Centerben](../security-center/security-center-identity-access.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: Azure-erőforrások kezelése csak jóváhagyott helyekről

**Útmutató:** Feltételes hozzáférési szabályzat hely feltételének konfigurálása és a nevestűs helyek kezelése.

Ip-címtartományok, országok és régiók logikai csoportosításának létrehozása elnevezett helyekkel. Korlátozza a bizalmas erőforrásokhoz ( például titkos Azure Key Vault – a konfigurált nevestűs helyekhez való hozzáférést.

- [Mi a hely feltétele a Azure Active Directory (Azure AD) feltételes hozzáférésében?](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="39-use-azure-active-directory"></a>3.9: A Azure Active Directory

**Útmutató:** A Azure Active Directory (Azure AD) használata központi hitelesítési és engedélyezési rendszerként. Az Azure AD erős titkosítással védi az adatokat az átvitel alatt és az átvitel alatt, valamint a sót, a hashe-eket, valamint biztonságosan tárolja a felhasználói hitelesítő adatokat.
- [Azure AD-példány létrehozása és konfigurálása](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: A felhasználói hozzáférés rendszeres áttekintése és egyeztetése

**Útmutató:** Azure Active Directory (Azure AD) naplókat biztosít az elavult fiókok felderítésének segítésekor. Az Azure Identity Access Reviews használatával hatékonyan kezelheti a csoporttagságokat, a vállalati alkalmazásokhoz való hozzáférést és a szerepkör-hozzárendeléseket. Rendszeresen tekintse át a felhasználói hozzáférést, hogy csak az aktív felhasználók férnek hozzá.

- [Az Azure AD-jelentéskészítés](/azure/active-directory/reports-monitoring)

- [Az Azure Identity Hozzáférési felülvizsgálatok használata](../active-directory/governance/access-reviews-overview.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3.11: Az inaktivált hitelesítő adatok elérésére tett kísérletek figyelése

**Útmutató:** Azure Active Directory (Azure AD) bejelentkezési tevékenység, naplózási és kockázati események naplózási forrásainak integrálása bármilyen SIEM- vagy monitorozási eszközzel, például Azure Sentinel.

Egyszerűsítheti a folyamatot az Azure AD felhasználói fiókok diagnosztikai beállításainak létrehozásával, valamint az auditnaplók és bejelentkezési naplók Log Analytics-munkaterületre való küldésével. Konfigurálja a kívánt riasztásokat a Log Analytics-munkaterületen.

- [Azure-tevékenységnaplók integrálása az Azure Monitorba](/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3.12: Riasztás a fiók bejelentkezési viselkedésének eltéréséről

**Útmutató:** Azure Active Directory (Azure AD) (Azure AD) kockázat- és identity Protection-funkcióival konfigurálhatja az automatikus válaszokat a felhasználói identitásokkal kapcsolatos gyanús műveletekre. A további vizsgálathoz Azure Sentinel adatokat a rendszernek.

- [Az Azure AD kockázatos bejelentkezéseinek megtekintése](../active-directory/identity-protection/overview-identity-protection.md)

- [Az Identity Protection kockázati szabályzatok konfigurálása és engedélyezése](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [A Azure Sentinel](../sentinel/quickstart-onboard.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

## <a name="data-protection"></a>Adatvédelem

*További információ: [Azure Security Benchmark: Adatvédelem](../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: Bizalmas adatok leltárának fenntartása

**Útmutató:** Címkék használata a bizalmas adatokat tároló Azure Web Application Firewall (WAF) és kapcsolódó erőforrások nyomon követéséhez.

- [Címkék létrehozása és használata](../azure-resource-manager/management/tag-resources.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: Bizalmas adatokat tároló vagy feldolgozó rendszerek elkülönítése

**Útmutató:** Elkülönítés implementálás különálló előfizetésekkel és felügyeleti csoportokkal az egyes biztonsági tartományokhoz, például a környezet típusához és az adatok bizalmasságához, például fejlesztési, tesztelési és éles környezetekhez. 

Az Azure-erőforrásokhoz való hozzáférés vezérlése Azure Active Directory (Azure AD) szerepköralapú hozzáférés-vezérléssel (Azure RBAC).

- [További Azure-előfizetések létrehozása](../cost-management-billing/manage/create-subscription.md)

- [A Management Groups](../governance/management-groups/create-management-group-portal.md)

- [Címkék létrehozása és használata](../azure-resource-manager/management/tag-resources.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: Az átvitel során bizalmas adatok titkosítása

**Útmutató:** Az átvitel során bizalmas információk titkosítása. Győződjön meg arról, hogy a Azure Web Application Firewall (WAF) példányokhoz és a kapcsolódó erőforrásokhoz csatlakozó ügyfelek képesek egyeztetni a TLS 1.2-t vagy annál nagyobbat.

Kövesse Security Center az átvitel közbeni, valamint az átvitel közbeni titkosításra vonatkozó javaslatokat.

- [Az átvitel során az Azure-ral történő titkosítás](https://docs.microsoft.com/azure/security/fundamentals/encryption-overview#encryption-of-data-in-transit)

**Felelősség**: Megosztott

**Azure Security Center:** Nincs

### <a name="46-use-azure-rbac-to-manage-access-to-resources"></a>4.6: Az erőforrásokhoz való hozzáférés kezelése az Azure RBAC használatával 

**Útmutató:** Az Azure szerepköralapú hozzáférés-vezérléssel (Azure RBAC Web Application Firewall) szabályozhatja az Azure-erőforrásokhoz való hozzáférést.

- [Az Azure RBAC konfigurálása az Azure-ban](../role-based-access-control/role-assignments-portal.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: Bizalmas adatok titkosítása

**Útmutató:** Titkosítás használata az összes Azure-erőforráshoz, beleértve a Azure Web Application Firewall (WAF) és a kapcsolódó erőforrásokat. A Microsoft azt javasolja, hogy az Azure kezelje a titkosítási kulcsokat, de bizonyos esetekben lehetősége van a saját kulcsainak kezelésére.

- [Inaktív adatok Azure-ban való titkosításának ismertetése](../security/fundamentals/encryption-atrest.md)

- [Ügyfél által felügyelt titkosítási kulcsok konfigurálása](https://docs.microsoft.com/azure/storage/common/customer-managed-keys-configure-key-vault?tabs=portal)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: Kritikus fontosságú Azure-erőforrások változásainak naplózása és riasztása

**Útmutató:** A Azure Web Application Firewall (WAF) konfigurálása megelőzési módban való futtatáshoz, miután a hálózati forgalmat észlelési módban alapozta egy előre meghatározott időre. 

Az Azure WAF Megelőzés módban blokkolja a szabályok által észlelt behatolásokat és támadásokat. A támadó egy "403-as jogosulatlan hozzáférés" kivételt kap, és bezárja a kapcsolatot. A megelőzési mód rögzíti az ilyen támadásokat a WAF-naplókban.

- [Az integráció áttekintése a Application Gateway és a Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-partner-integration#overview)

- [WAF-módok a Application Gateway](https://docs.microsoft.com/azure/web-application-firewall/ag/ag-overview#waf-modes)

- [WAF-módok a Front Door](https://docs.microsoft.com/azure/web-application-firewall/afds/afds-overview#waf-modes)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

## <a name="inventory-and-asset-management"></a>Leltár-és eszközfelügyelet

*További információ: [Azure biztonsági teljesítményteszt: Leltár és eszközkezelés.](../security/benchmarks/security-control-inventory-asset-management.md)*

### <a name="61-use-automated-asset-discovery-solution"></a>6.1: Automatizált eszközfelderítési megoldás használata

**Útmutató:** Azure Resource Graph használatával lekérdezheti vagy felderítheti az összes erőforrást, például a számítást, a tárolást, a hálózatot, a portokat és a protokollokat stb. az előfizetésen belül.

Győződjön meg arról, hogy megfelelő (olvasási) engedélyeket használ a bérlőben, és számba veszi az összes Azure-előfizetést, valamint az előfizetésen belüli erőforrásokat. Bár a klasszikus Azure-erőforrások a Resource Graph felderíthetőek, erősen ajánlott a Azure Resource Manager erőforrások létrehozása és használata.

- [Lekérdezések létrehozása Azure Resource Graph](../governance/resource-graph/first-query-portal.md)

- [Az Azure-előfizetések megtekintése](https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-4.8.0&amp;preserve-view=true)

- [Az Azure RBAC](../role-based-access-control/overview.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="62-maintain-asset-metadata"></a>6.2: Objektum metaadatainak karbantartása

**Útmutató:** Címkék használata a Azure Web Application Firewall (WAF) szabályzatok használatával. Címkék társíthatóak erőforrásokhoz, és logikailag alkalmazhatók az erőforrásokhoz való hozzáférés rendszerezéséhez egy ügyfél-előfizetésben. 

- [Címkék létrehozása és használata](../azure-resource-manager/management/tag-resources.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: Nem engedélyezett Azure-erőforrások törlése

**Útmutató:** Az Azure WAF és a kapcsolódó erőforrások rendszerezéséhez és nyomon követéséhez szükség esetén használjon címkézést, felügyeleti csoportokat és különálló előfizetéseket. Rendszeresen összeegyezteti a leltárat, és gondoskodik arról, hogy a jogosulatlan erőforrások időben törlődjenek az előfizetésből.

- [További Azure-előfizetések létrehozása](../cost-management-billing/manage/create-subscription.md)

- [Új Management Groups](../governance/management-groups/create-management-group-portal.md)

- [Címkék létrehozása és használata](../azure-resource-manager/management/tag-resources.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6.4: Jóváhagyott Azure-erőforrások meghatározása és leltározása

**Útmutató:** Leltár létrehozása a jóváhagyott erőforrásokról, beleértve a szervezeti igényeken alapuló konfigurációt is.

A Azure Policy az előfizetésben létrehozható erőforrások típusára vonatkozó korlátozásokat. Az Azure Resource Graph használatával lekérdezheti és felderítheti az előfizetésükön belüli erőforrásokat. Győződjön meg arról, hogy a környezetben jelen van minden Azure-erőforrás jóvá van hagyva.

- [Az Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md)

- [Lekérdezések létrehozása Azure Resource Graph](../governance/resource-graph/first-query-portal.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: Nem jóváhagyott Azure-erőforrások figyelése

**Útmutató:** A Azure Policy az előfizetésben létrehozható erőforrások típusára vonatkozó korlátozásokat.
A Azure Resource Graph használatával lekérdezheti vagy felderítheti Azure Web Application Firewall (WAF) erőforrásokat az előfizetéseikben. Győződjön meg arról, hogy a környezetben jelen van minden Azure WAF- és kapcsolódó erőforrás jóvá van hagyva.

- [Az Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md)

- [Lekérdezések létrehozása az Azure Graph használatával](../governance/resource-graph/first-query-portal.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7: Nem jóváhagyott Azure-erőforrások és -szoftveralkalmazások eltávolítása

**Útmutató:** Nem jóváhagyott Azure WAF-erőforrások figyelése és eltávolítása az Azure Policy használatával az Azure WAF vagy egy bizonyos típusú WAF, például az Azure WAF v1 vagy v2 üzembe helyezésének megtagadása érdekében.

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="69-use-only-approved-azure-services"></a>6.9: Csak jóváhagyott Azure-szolgáltatások használata

**Útmutató:** A Azure Policy korlátozhatja, mely szolgáltatásokat lehet kiépítni a környezetben.

- [Az Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md)

- [Adott erőforrástípus megtagadása a Azure Policy](/azure/governance/policy/samples/built-in-policies#general)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6.11: Korlátozza a felhasználók számára a Azure Resource Manager

**Útmutató:** Az Azure feltételes hozzáférés használatával korlátozhatja a felhasználók Azure Resources Managerrel való interakcióját a "Hozzáférés blokkolása" beállításával a "Microsoft Azure Management" alkalmazáshoz.

- [Feltételes hozzáférés konfigurálása az Azure Resources Managerhez való hozzáférés letiltása érdekében](../role-based-access-control/conditional-access-azure-management.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13: Magas kockázatú alkalmazások fizikai vagy logikai elkülönítése

**Útmutató:** Azure Web Application Firewall (WAF) különböző környezetekhez társítható hálózatok, erőforráscsoportok vagy előfizetések segítségével a magas kockázatú alkalmazások elkülönítése érdekében.

- [Az Azure Virtual Network áttekintése](../virtual-network/virtual-networks-overview.md)

- [Erőforrások rendszerezése az Azure-beli felügyeleti csoportokkal](../governance/management-groups/overview.md)

- [Útmutató az előfizetéssel kapcsolatos döntésekhez](/azure/cloud-adoption-framework/decision-guides/subscriptions/)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

## <a name="secure-configuration"></a>Biztonságos konfiguráció

*További információ: [Azure biztonsági teljesítményteszt: Biztonságos konfiguráció.](../security/benchmarks/security-control-secure-configuration.md)*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: Biztonságos konfigurációk létrehozása az összes Azure-erőforráshoz

**Útmutató:** Szabványos biztonsági konfigurációkat határozhat meg és implementálhat a vállalati (WAF Azure Web Application Firewall telepítésekkel kapcsolatos hálózati beállításokhoz.

Használjon Azure Policy aliasokat a "Microsoft.Network" névtérben egyéni szabályzatok létrehozásához az Azure Application Gatewayek, virtuális hálózatok és hálózati biztonsági csoportok hálózati konfigurációjának naplózásához vagy kikényszerításához, valamint beépített szabályzatdefiníciók használatára.

- [Az elérhető aliasok Azure Policy megtekintése](https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-4.8.0&amp;preserve-view=true)

- [Az Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: Biztonságos Azure-erőforrás-konfigurációk fenntartása

**Útmutató:** A Azure Policy [deny] és a [deploy if not exist] hatás használatával kényszerítheti a biztonságos beállításokat a Azure Web Application Firewall (WAF) és a kapcsolódó erőforrásokon. 

A Azure Resource Manager a szervezet által megkövetelt Azure WAF és kapcsolódó erőforrások biztonsági konfigurációjának fenntartásához.

- [A Azure Policy hatásainak](../governance/policy/concepts/effects.md)

- [Szabályzatok létrehozása és kezelése a megfelelőség kikényszerítése céljából](../governance/policy/tutorials/create-and-manage.md)

- [Azure Resource Manager sablonok áttekintése](../azure-resource-manager/templates/overview.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Az Azure-erőforrások konfigurációjának biztonságos tárolása

**Útmutató:** Az Azure DevOps használatával biztonságosan tárolhatja és kezelheti a kódot, például egyéni Azure-szabályzatokat és Azure Resource Manager sablonokat.

Engedélyek megadása vagy megtagadása adott felhasználóknak, beépített biztonsági csoportoknak vagy az Azure Active Directory-ben (Azure AD)-ben meghatározott csoportoknak, ha integrálva vannak az Azure DevOpsba vagy az Azure AD-be, ha integrálva vannak a Team Foundation Server (TFS) használatával.

- [Kód tárolása az Azure DevOpsban](https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops&amp;preserve-view=true)

- [Az Azure DevOps engedélyeinek és csoportjainak](/azure/devops/organizations/security/about-permissions)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7: Konfigurációkezelési eszközök üzembe helyezése Azure-erőforrásokhoz

**Útmutató:** Beépített Azure Policy és Azure Policy aliasok használata a "Microsoft.Network" névtérben egyéni szabályzatok létrehozásához a rendszerkonfigurációk riasztásához, naplózásához és kényszerítéséhez. Folyamat és folyamat fejlesztése szabályzati kivételek kezeléséhez.

- [Az Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md)

- [Az Azure Policy dokumentációja](/azure/governance/policy)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9: Automatizált konfigurációfigyelés megvalósítása Azure-erőforrásokhoz

**Útmutató:** Beépített Azure Policy és Azure Policy aliasok használata a "Microsoft.Network" névtérben egyéni szabályzatok létrehozásához a rendszerkonfigurációk riasztásához, naplózásához és kényszerítéséhez. 

A Azure Policy [audit], [deny] és [deploy if not exist] hatásokkal automatikusan kikényszeríteni tudja az Azure-erőforrások konfigurációit.

- [Az Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md)

- [Az Azure Policy dokumentációja](/azure/governance/policy)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="711-manage-azure-secrets-securely"></a>7.11: Azure-beli titkos kulcsok biztonságos kezelése

**Útmutató:** A Azure Key Vault tanúsítványok biztonságos tárolására használható. Azure Key Vault egy platform által felügyelt titkoskulcs-tároló, amely a titkos kulcsok, kulcsok és SSL-tanúsítványok védelmére használható. 

Azure Application Gateway támogatja az integrációt Key Vault HTTPS-kompatibilis figyelőkhöz csatolt kiszolgálótanúsítványok esetében. 

- [SSL-leállítva konfigurálása Key Vault tanúsítványokkal a Azure PowerShell](../application-gateway/configure-keyvault-ps.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: A hitelesítő adatok nem szándékolt kitettségének megszüntetése

**Útmutató:** A Hitelesítőadat-ellenőrző implementálja a hitelesítő adatokat a kódon belül, ami elősegíti a felderített hitelesítő adatok áthelyezését biztonságosabb helyekre, például Azure Key Vault.
- [Hitelesítőadat-ellenőrző beállítása](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

## <a name="data-recovery"></a>Adat-helyreállítás

*További információ: [Azure biztonsági teljesítményteszt: Adat-helyreállítás.](../security/benchmarks/security-control-data-recovery.md)*

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: A biztonsági másolatok és az ügyfél által kezelt kulcsok védelmének biztosítása

**Útmutató:** Győződjön meg arról, hogy a szoftveres törlés engedélyezve Azure Key Vault. A helyreállított törlés lehetővé teszi a törölt kulcstartók és tárolóobjektumok, például kulcsok, titkos kulcsok és tanúsítványok helyreállítását.

- [A Azure Key Vault törlési parancsának használata](https://docs.microsoft.com/azure/key-vault/general/key-vault-recovery?tabs=azure-powershell&amp;preserve-view=true)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

## <a name="incident-response"></a>Incidensmegoldás

*További információ: [Azure Security Benchmark: Incidensek kezelése](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10.1: Incidensválasz-útmutató létrehozása

**Útmutató:** Incidensválasz-útmutató kidolgozása a szervezet számára. Győződjön meg arról, hogy vannak olyan írásos incidenskezelési tervek, amelyek meghatározzák a személyzet összes szerepkörét, valamint az incidenskezelés és -kezelés fázisát az észleléstől az incidens utáni felülvizsgálaton át az incidensek áttekintéséhez. 

- [Útmutatás saját biztonsági incidensek válaszadási folyamatának építéshez](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/) 

- [Microsoft Biztonsági reagálási központ az incidensek felépítése](https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/) 

- [Az NIST számítógépes biztonsági incidenskezelési útmutatójának használata saját incidenskezelési terv létrehozásához](https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: Incidenspontozási és -rangsorolási eljárás létrehozása

**Útmutató:** Security Center súlyosságot rendel az egyes riasztásokhoz, így fontossági sorrendbe adhatja, hogy mely riasztásokat kell először megvizsgálni. A súlyosság azon alapul, Security Center mennyire magabiztos a riasztás kiadásához használt eredmény vagy metrika, valamint a riasztáshoz vezető tevékenység mögötti rosszindulatú szándék megbízhatósági szintje.

Egyértelműen jelölje meg az előfizetéseket (például éles, nem éles) és hozzon létre egy elnevezési rendszert az Azure-erőforrások egyértelmű azonosításához és kategorizálásához.

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="103-test-security-response-procedures"></a>10.3: Biztonsági reagálási eljárások tesztelése

**Útmutató:** Gyakorlatok a rendszerek incidensválasz-képességeinek rendszeres teszteléséhez. Azonosítsa a gyenge pontokat és réseket, és szükség esetén dolgozza át a tervet.
- [Tekintse meg az NIST Útmutató a teszteléshez, a betanításhoz és a gyakorlathoz programokkal kapcsolatos kiadványát az it-csomagokhoz és képességekhez](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: Adja meg a biztonsági incidens kapcsolattartási adatait, és konfigurálja a riasztási értesítéseket a biztonsági incidensek esetén

**Útmutató:** A Biztonsági incidensek kapcsolattartási adatait a Microsoft arra használja, hogy kapcsolatba lépjen Önnel, ha az Microsoft Biztonsági reagálási központ (MSRC) azt tapasztalja, hogy az ügyfél adatait illetéktelen vagy jogosulatlan fél férte hozzá. Tekintse át az incidenseket a tény után, és győződjön meg arról, hogy a problémák megoldódnak.
- [A biztonsági kapcsolattartó Azure Security Center beállítása](../security-center/security-center-provide-security-contact-details.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: Biztonsági riasztások beépítése az incidensválasz-rendszerbe

**Útmutató:** Az Security Center riasztások és javaslatok exportálása a Folyamatos exportálás funkcióval. A folyamatos exportálás lehetővé teszi, hogy manuálisan vagy folyamatosan exportálja a riasztásokat és javaslatokat. A Azure Security Center összekötővel streamelhet riasztásokat Azure Sentinel a szervezet igényeinek megfelelően.

- [Folyamatos exportálás konfigurálása](../security-center/continuous-export.md)

- [Riasztások streamelése az Azure Sentinelbe](../sentinel/connect-azure-security-center.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: A biztonsági riasztások válaszának automatizálása

**Útmutató:** A munkafolyamat-automatizálási szolgáltatás Security Center a biztonsági riasztások és javaslatok "Logic Apps" funkcióját használva automatikusan aktiválhatja a válaszokat.
- [A munkafolyamat-automatizálás és -Logic Apps](../security-center/workflow-automation.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

## <a name="penetration-tests-and-red-team-exercises"></a>Behatolási tesztek és Red Team-gyakorlatok

*További információ: [Azure biztonsági teljesítményteszt: Behatolási tesztek és Red Team-gyakorlatok.](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md)*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11.1: Az Azure-erőforrások rendszeres behatolástesztelése és az összes kritikus fontosságú biztonsági eredmény szervizelésének biztosítása

**Útmutató:** Kövesse a Microsoft Engagement-szabályokat annak biztosításához, hogy a behatolási tesztek ne sértsék meg a Microsoft szabályzatát. A Microsoft red teaming és élő webhely-behatolási tesztek a Microsoft által felügyelt felhőinfra infrastruktúrán, szolgáltatásokon és alkalmazásokon végzett stratégiájának és végrehajtásának használata. 

- [Behatolástesztelési beavatkozási szabályok](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) 

- [Riasztási tesztek a Microsoft-felhőben](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Felelősség**: Megosztott

**Azure Security Center monitorozás:** Nincs

## <a name="next-steps"></a>Következő lépések

- [Az Azure Security Benchmark v2 áttekintésének](/azure/security/benchmarks/overview) megtekintése
- További tudnivalók az [Azure biztonsági alapterveiről](/azure/security/benchmarks/security-baselines-overview)
