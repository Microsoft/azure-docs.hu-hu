---
title: Azure-alapú biztonsági alapkonfiguráció Azure webalkalmazási tűzfalhoz
description: Az Azure webalkalmazási tűzfal biztonsági alapterve az Azure biztonsági Teljesítménytesztben meghatározott biztonsági javaslatok megvalósítására szolgáló eljárási útmutatást és erőforrásokat biztosít.
author: msmbaldwin
ms.service: web-application-firewall
ms.topic: conceptual
ms.date: 10/13/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: b50ab8cae91aab56be287f517fa6e6a0b25db8e1
ms.sourcegitcommit: 8245325f9170371e08bbc66da7a6c292bbbd94cc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/07/2021
ms.locfileid: "99807869"
---
# <a name="azure-security-baseline-for-azure-web-application-firewall"></a>Azure-alapú biztonsági alapkonfiguráció Azure webalkalmazási tűzfalhoz

Ez a biztonsági alapkonfiguráció az [Azure biztonsági teljesítményteszt 1,0-es verziójáról](../security/benchmarks/overview-v1.md) az Azure webalkalmazási tűzfalra vonatkozó útmutatást alkalmazza. Az Azure Security Benchmark ajánlásokat ad arra nézve, hogy hogyan tehetők biztonságossá a felhőalapú megoldások az Azure-ban. A tartalmat az Azure biztonsági teljesítményteszt által meghatározott **biztonsági vezérlők** és az Azure webalkalmazási tűzfalra vonatkozó kapcsolódó útmutatások szerint csoportosítjuk. Az Azure webalkalmazási tűzfalra nem alkalmazható **vezérlők** ki lettek zárva. 

Ha szeretné megtekinteni, hogy az Azure webalkalmazás-tűzfal hogyan teljes mértékben leképezi az Azure biztonsági Teljesítménytesztét, tekintse meg a [teljes Azure-webalkalmazási tűzfal biztonsági](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)alapkonfiguráció

## <a name="network-security"></a>Hálózati biztonság

*További információt az [Azure biztonsági teljesítményteszt: hálózati biztonság](../security/benchmarks/security-control-network-security.md)című témakörben talál.*

### <a name="13-protect-critical-web-applications"></a>1,3: a kritikus webalkalmazások megóvása

**Útmutató**: a webalkalmazások központosított védelme érdekében Microsoft Azure webalkalmazási TŰZFAL (WAF) használata a gyakori biztonsági rések és sebezhetőségek, például az SQL-injektálás és a helyek közötti parancsfájlok használatával. 

- Észlelési mód: ezt a módot használhatja a hálózati forgalom megismeréséhez, valamint a téves pozitív állapotok megismeréséhez és áttekintéséhez. Figyeli és naplózza az összes veszélyforrás riasztást. Ellenőrizze, hogy a diagnosztika és a WAF napló ki van-e választva és be van-e kapcsolva. Vegye figyelembe, hogy a WAF nem blokkolja a bejövő kérelmeket, ha az észlelési módban működik.
- Megelőzési mód: blokkolja a szabályok által észlelt behatolásokat és támadásokat. Egy támadó "403 jogosulatlan hozzáférés" kivételt kap, és a kapcsolat bezárult. A megelőzési mód rögzíti az ilyen támadásokat a WAF-naplókban.

A hálózati forgalom alapkonfigurációja a WAF észlelési módjával. A forgalmi igények meghatározása után állítsa be a WAF megelőzési módban a Bock nemkívánatos forgalmára.

A WAF által nem védett webes erőforrások esetében a Security Center magas súlyosságú javaslataira vonatkozó további információk követése.  

- [Webalkalmazási tűzfal CRS-szabályok csoportjai és szabályai](ag/application-gateway-crs-rulegroups-rules.md) 

- [WAF módok Application Gateway](ag/ag-overview.md#waf-modes)

- [WAF módok a bejárati ajtón](afds/afds-overview.md#waf-modes)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1,4: az ismert kártevő IP-címekkel folytatott kommunikáció megtagadása

**Útmutató**: egyéni szabályok használata az Azure Web Application Firewall (WAF) használatával a forgalom engedélyezéséhez és letiltásához. Például az IP-címtartományból érkező összes forgalom letiltható. Az Azure-WAF olyan megelőzési módban való futtatására konfigurálja, amely blokkolja a szabályok által észlelt behatolásokat és támadásokat. Egy támadó "403 jogosulatlan hozzáférés" kivételt kap, és a kapcsolat bezárult. A megelőzési mód rögzíti az ilyen támadásokat a WAF-naplókban.

- [WAF módok Application Gateway](ag/ag-overview.md#waf-modes)

- [WAF módok a bejárati ajtón](afds/afds-overview.md#waf-modes)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="17-manage-traffic-to-web-applications"></a>1,7: webalkalmazások forgalmának kezelése

**Útmutató**: az Azure webalkalmazási TŰZFAL (WAF) az Azure webalkalmazás-védelmi szolgáltatásának alapvető összetevője. Az Azure WAF használatával központosított védelmet biztosíthat a webes alkalmazások számára a gyakori biztonsági rések és sebezhetőségek az előre konfigurált felügyelt szabályokkal szemben az OWASP TOP 10 kategóriából származó ismert támadási aláírásokkal szemben.

Testre szabhatja az Azure WAF szabályait és szabályait, hogy megfeleljenek a webalkalmazásokra vonatkozó követelményeknek, és kiküszöböljék a téves pozitív eredményt. Rendeljen hozzá egy Azure WAF-házirendet minden WAF mögötti helyhez, hogy engedélyezze a helyhez tartozó konfigurációt. Az Azure WAF támogatja a Geo-szűrést, a ráta-korlátozást, az Azure által felügyelt alapértelmezett szabálykészlet-szabályokat. és egyéni szabályok is létrehozhatók az alkalmazások igényeinek megfelelően. 

Állítsa be úgy az Azure-WAF, hogy a megelőzési módban fusson, miután meghatározott időtartamra viszonyítási a hálózati forgalmat az észlelési módban. Az Azure WAF a szabályok által észlelt behatolásokat és támadásokat blokkolja a megelőzési módban. Egy támadó "403 jogosulatlan hozzáférés" kivételt kap, és a kapcsolat bezárult. A megelőzési mód rögzíti az ilyen támadásokat a WAF-naplókban.

- [WAF módok Application Gateway](ag/ag-overview.md#waf-modes)

- [WAF módok a bejárati ajtón](afds/afds-overview.md#waf-modes)

- [Webalkalmazási tűzfal CRS-szabályok csoportjai és szabályai](ag/application-gateway-crs-rulegroups-rules.md?tabs=owasp31)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1,8: a hálózati biztonsági szabályok bonyolultságának és adminisztratív terhelésének csökkentése

**Útmutató**: erőforrások létrehozása, hozzárendelése és logikus rendszerezése az Azure-előfizetések között a gyakori alkalmazások helytelen konfigurációinak (például az Apache és az IIS) észlelésére szolgáló címkékkel. 

Szabályok és szabályok alkalmazása az Azure webalkalmazási tűzfal (WAF) házirendjeire az alkalmazott címke metaadatai alapján.

- [WAF szabályzat Application Gateway](/cli/azure/network/application-gateway/waf-policy?view=azure-cli-latest) 

- [WAF szabályzat a bejárati ajtón](/cli/azure/ext/front-door/network/front-door/waf-policy?view=azure-cli-latest)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="110-document-traffic-configuration-rules"></a>1,10: a dokumentum forgalmának konfigurációs szabályai

**Útmutató**: az Application Gateway Azure webalkalmazási TŰZFALLAL (WAF) társított hálózati biztonsági csoportok, valamint a hálózati biztonsággal és a forgalommal kapcsolatos egyéb erőforrások használata címkék használatával. Az egyes hálózati biztonsági csoportokra vonatkozó szabályok esetében a "Leírás" mezővel adhatja meg az üzleti igényeket, az időtartamot és így tovább, minden olyan szabály esetében, amely engedélyezi a hálózatra irányuló vagy onnan érkező forgalmat.

A címkézéshez kapcsolódó beépített Azure Policy definíciók bármelyikét használhatja, például a "címke és az érték megkövetelése" beállítást, hogy az összes erőforrás címkével legyen létrehozva, és értesítse a meglévő címkézetlen erőforrásokról.

Válassza az Azure PowerShell vagy az Azure CLI lehetőséget a címkéken alapuló erőforrások kereséséhez, illetve műveletek végrehajtásához.

- [Címkék létrehozása és használata](../azure-resource-manager/management/tag-resources.md)

- [Virtual Network létrehozása](../virtual-network/quick-create-portal.md)

- [NSG létrehozása biztonsági konfigurációval](../virtual-network/tutorial-filter-network-traffic.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1,11: automatikus eszközök használata a hálózati erőforrások konfigurációjának figyelésére és a változások észlelésére

**Útmutató**: az Azure-Tevékenységnaplók használatával figyelheti a hálózati erőforrás-konfigurációkat, és felderítheti az Azure webalkalmazási TŰZFAL (WAF) üzembe helyezéséhez kapcsolódó hálózati beállítások és erőforrások változásait. Hozzon létre riasztásokat Azure Monitoron belül, amelyek akkor lépnek életbe, amikor a kritikus hálózati beállításokat vagy erőforrásokat módosítják.

- [Azure-Tevékenységnaplók eseményeinek megtekintése és lekérése](../azure-monitor/platform/activity-log.md#view-the-activity-log)

- [Riasztások létrehozása a Azure Monitorban](../azure-monitor/platform/alerts-activity-log.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

## <a name="logging-and-monitoring"></a>Naplózás és figyelés

*További információt az [Azure biztonsági teljesítményteszt: naplózás és figyelés](../security/benchmarks/security-control-logging-monitoring.md)című témakörben talál.*

### <a name="21-use-approved-time-synchronization-sources"></a>2,1: a jóváhagyott idő-szinkronizálási források használata

**Útmutató**: hozzon létre egy hálózati szabályt az Azure webalkalmazási TŰZFAL (WAF) számára, amely lehetővé teszi egy NTP-kiszolgáló elérését a megfelelő porttal és protokollal, például az 123-es porton keresztül, UDP-n keresztül

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Megosztott

### <a name="22-configure-central-security-log-management"></a>2,2: a központi biztonsági naplók felügyeletének konfigurálása

**Útmutató**: konfigurálja az Azure webalkalmazási TŰZFAL (WAF) naplóit, amelyeket egy központi biztonsági naplózási megoldásként, például az Azure Sentinel vagy egy harmadik féltől származó Siem-nek kell elküldeni. Ezek a naplók az Azure-tevékenységeket, a diagnosztikai és a valós idejű WAF-naplókat tartalmazzák, ezeket a naplókat különböző eszközök, például az Azure Monitor, az Excel és a Power BI is megtekintheti. Az Azure webalkalmazási tűzfal naplófájljai betekintést nyújtanak az Azure-WAF értékelésére, egyeztetésére és blokkolására szolgáló adatra.

Az Azure Sentinel egy beépített Azure WAF-munkafüzettel rendelkezik, amely áttekintést nyújt az Azure-WAF található biztonsági eseményekről. Ez a munkafüzet az eseményeket, a megfeleltetett és a blokkolt szabályokat tartalmazza, és minden más, ami naplózva lesz a tűzfal naplófájljaiban. Ez a telemetria a forgatókönyvek automatizálásának elindítására szolgál az Azure Sentinel által gyűjtött WAF események alapján történő értesítéshez vagy szervizelési műveletek elvégzéséhez.

- [Tevékenységek naplóinak megtekintése](../azure-resource-manager/management/view-activity-logs.md)

- [Diagnosztikai naplók a Application Gateway](../application-gateway/application-gateway-diagnostics.md)

- [Adatok összekapcsolása a Microsoft webalkalmazási tűzfallal az Azure Sentinel szolgáltatásba](../sentinel/connect-azure-waf.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="23-enable-audit-logging-for-azure-resources"></a>2,3: az Azure-erőforrások naplózásának engedélyezése

**Útmutató**: engedélyezze a naplózást az Azure webalkalmazási TŰZFAL (WAF) erőforrásai számára a naplózási, biztonsági és diagnosztikai naplókhoz való hozzáféréshez. Az Azure webalkalmazási tűzfal részletes jelentéskészítést biztosít az észlelt fenyegetésekről, amelyek a konfigurált diagnosztikai naplókban elérhetővé válnak. A automatikusan elérhető tevékenység-naplók közé tartozik az eseményforrás, a dátum, a felhasználó, az időbélyeg, a forráscím, a célcím és más hasznos elemek.

- [Naplózás – áttekintés](ag/ag-overview.md#logging)

- [Azure Monitor log lekérdezés áttekintése](../azure-monitor/log-query/log-query-overview.md)

- [Az Azure platform naplófájljainak áttekintése](../azure-monitor/platform/platform-logs-overview.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="25-configure-security-log-storage-retention"></a>2,5: a biztonsági napló tárolási adatmegőrzésének konfigurálása

**Útmutató**: küldje el az Azure webalkalmazási TŰZFAL (WAF) naplóit egy egyéni Storage-fiókba, és határozza meg az adatmegőrzési szabályt. A Azure Monitor használatával állíthatja be a Log Analytics munkaterület megőrzési időtartamát a szervezet megfelelőségi követelményei alapján.
- [Storage-fiók figyelésének konfigurálása](../storage/common/storage-monitor-storage-account.md#configure-logging)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="26-monitor-and-review-logs"></a>2,6: naplók figyelése és áttekintése

**Útmutató**: az Azure webalkalmazási TŰZFAL (WAF) részletes jelentéseket nyújt az észlelt fenyegetésekről. A naplózás integrálva van Azure Diagnostics naplókkal, amelyek részletes információkat nyújtanak a naplózáshoz és a hibaelhárításhoz fontos műveletekről és hibákról. 

Az Azure WAF-példányok integrálva vannak Security Center a riasztások és állapotadatok küldésére a jelentéskészítéshez. Security Center javaslatai alapján azonosíthatja a nem védett webalkalmazásokat, és védetté teheti ezeket a sebezhető erőforrásokat. 

Az Azure Sentinel egy beépített WAF-tűzfallal kapcsolatos esemény-munkafüzettel rendelkezik, amely áttekintést nyújt a WAF lévő biztonsági eseményekről. Ilyenek például az események, a megfeleltetett és a blokkolt szabályok, és minden más, ami bekerül a tűzfal naplófájljaiba.

- [Diagnosztikai beállítások engedélyezése az Azure-beli tevékenység naplójában](../azure-monitor/index.yml) 

- [Az Azure Application Gateway diagnosztikai beállításainak engedélyezése](../application-gateway/application-gateway-diagnostics.md)

- [Metrikák és naplók figyelése az Azure-beli bejárati ajtón](../frontdoor/front-door-diagnostics.md)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="27-enable-alerts-for-anomalous-activities"></a>2,7: riasztások engedélyezése rendellenes tevékenységekhez

**Útmutató**: az Azure-Tevékenységnaplók diagnosztikai beállításainak engedélyezése, valamint az Azure-WAF diagnosztikai beállításai, valamint a naplók elküldése egy log Analytics-munkaterületre. Lekérdezéseket hajthat végre Log Analytics a kifejezések kereséséhez, a trendek azonosításához, a mintázatok elemzéséhez és számos más elemzéshez az összegyűjtött adatok alapján. Riasztások létrehozása a rendellenes tevékenységekhez WAF metrikák alapján. Ha például a letiltott kérelmek száma meghaladja az "X" értéket, akkor tegye a következőt: "Y".

- [Diagnosztikai beállítások engedélyezése az Azure-beli tevékenység naplójában](../azure-monitor/platform/activity-log.md)

- [Riasztások létrehozása az Azure-ban](../azure-monitor/learn/tutorial-response.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="28-centralize-anti-malware-logging"></a>2,8: kártevő szoftverek közötti naplózás központosítása

**Útmutató**: az Azure webalkalmazási TŰZFAL (WAF) üzembe helyezése a kritikus webalkalmazások előtt a bejövő forgalom további ellenőrzéséhez. 

Az Azure WAF központosított védelmet biztosít a webalkalmazások számára a gyakori biztonsági rések és sebezhetőségek ellen, és a bejövő webes forgalom megvizsgálása révén blokkolja a támadásokat, például az SQL-injektálásokat, a helyközi parancsfájlokat, a kártevő-feltöltéseket és a DDoS-támadásokat.

- [Az Azure WAF üzembe helyezése](ag/create-waf-policy-ag.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

## <a name="identity-and-access-control"></a>Identitás- és hozzáférés-vezérlés

*További információt az [Azure biztonsági teljesítményteszt: identitás-és hozzáférés-vezérlés](../security/benchmarks/security-control-identity-access-control.md)című témakörben talál.*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3,1: a felügyeleti fiókok leltárának karbantartása

**Útmutató**: a Azure Active Directory (Azure ad) beépített szerepkörökkel rendelkezik, és explicit módon hozzá kell rendelni. Az Azure AD PowerShell-modullal ad hoc lekérdezéseket hajthat végre a felügyeleti csoportok tagjait futtató fiókok felderítéséhez.

- [Címtárbeli szerepkör beszerzése az Azure AD-ben a PowerShell-lel](/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0)

- [Címtárbeli szerepkör tagjainak beszerzése az Azure AD-ben a PowerShell-lel](/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="33-use-dedicated-administrative-accounts"></a>3,3: dedikált rendszergazdai fiókok használata

**Útmutató**: nem érhetők el helyi rendszergazdai hozzárendelések a WAF belül. A környezetben azonban lehetnek Azure Active Directory (Azure AD) rendszergazdai szerepkörök, amelyek lehetővé tehetik az Azure WAF erőforrásainak felügyeletét.
Az Azure Web Application Firewall-(WAF-) példányokhoz hozzáféréssel rendelkező dedikált rendszergazdai fiókok használata esetén célszerű szabványos működési eljárásokat létrehozni. A rendszergazdai fiókok számának figyeléséhez használja a Security Center identitás-és hozzáférés-kezelési funkcióit.

- [Azure Security Center identitás és hozzáférés ismertetése](../security-center/security-center-identity-access.md)

- [Ismerje meg, hogyan hozhat létre rendszergazdai felhasználókat a Azure Database for PostgreSQL](../postgresql/howto-create-users.md#the-server-admin-account)

- [A Azure Policy használata](../governance/policy/tutorials/create-and-manage.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3,5: Multi-Factor Authentication használata az összes Azure Active Directory-alapú hozzáféréshez

**Útmutató**: a Azure Active Directory (Azure AD) multi-Factor Authentication (MFA) engedélyezése és a Security Center identitás-és hozzáférés-kezelési javaslatainak követése.

- [MFA engedélyezése az Azure-ban](../active-directory/authentication/howto-mfa-getstarted.md)

- [Identitás és hozzáférés figyelése Azure Security Centeron belül](../security-center/security-center-identity-access.md)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3,6: dedikált gépek (privilegizált hozzáférési munkaállomások) használata az összes felügyeleti feladathoz

**Útmutató**: az Azure webalkalmazási TŰZFAL (WAF) és a kapcsolódó erőforrások bejelentkezni és konfigurálására konfigurált, multi-Factor Authentication (MFA) jogosultsággal rendelkező hozzáférési munkaállomás (Paw) használata. 

- [További tudnivalók a privilegizált hozzáférésű munkaállomásokról](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/) 

- [MFA engedélyezése az Azure-ban](../active-directory/authentication/howto-mfa-getstarted.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3,7: naplózás és riasztás a gyanús tevékenységekről a rendszergazdai fiókoktól

**Útmutató**: Azure Active Directory (Azure ad) biztonsági jelentések használata naplók és riasztások generálásához, ha a környezetben gyanús vagy nem biztonságos tevékenység történik. A Security Center használatával figyelheti az identitás-és hozzáférési tevékenységeket.

- [A kockázatos tevékenységek miatt megjelölt Azure AD-felhasználók azonosítása](../active-directory/identity-protection/overview-identity-protection.md)

- [A felhasználók identitási és hozzáférési tevékenységeinek monitorozása az Azure Security Centerben](../security-center/security-center-identity-access.md)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3,8: az Azure-erőforrások kezelése csak jóváhagyott helyekről

**Útmutató**: a feltételes hozzáférési szabályzat tárolási állapotának konfigurálása és az elnevezett helyek kezelése. 

Hozzon létre az IP-címtartományok vagy országok és régiók logikai csoportosítását névvel ellátott helyen. Korlátozza a bizalmas erőforrásokhoz való hozzáférést, például Azure Key Vault titkokat a konfigurált elnevezett helyekre.

- [Mi a hely feltétele a feltételes hozzáférés Azure Active Directory](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="39-use-azure-active-directory"></a>3,9: a Azure Active Directory használata

**Útmutató**: a Azure Active Directory (Azure ad) használata központi hitelesítési és engedélyezési rendszerrel. Az Azure AD az adatok védelme érdekében erős titkosítást használ a nyugalmi és a továbbítási adatokhoz, valamint a felhasználók hitelesítő adatainak tárolásához és kiszűréséhez is.
- [Azure AD-példány létrehozása és konfigurálása](../active-directory/fundamentals/active-directory-access-create-new-tenant.md) 

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="310-regularly-review-and-reconcile-user-access"></a>3,10: a felhasználói hozzáférés rendszeres áttekintése és egyeztetése

**Útmutató**: a Azure Active Directory (Azure ad) olyan naplókat biztosít, amelyek segítenek az elavult fiókok felderítésében. Az Azure Identity Access Reviews használatával hatékonyan kezelheti a csoporttagságok kezelését, a vállalati alkalmazásokhoz való hozzáférést és a szerepkör-hozzárendeléseket. Rendszeresen tekintse át a felhasználók hozzáférését annak biztosítására, hogy csak az aktív felhasználók férhessenek hozzá.

- [Az Azure AD jelentéskészítés ismertetése](../active-directory/reports-monitoring/index.yml)

- [Az Azure Identity hozzáférési felülvizsgálatok használata](../active-directory/governance/access-reviews-overview.md)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3,11: a figyelő megkísérli a deaktivált hitelesítő adatok elérését

**Útmutató**: Azure Active Directory (Azure ad) bejelentkezési tevékenység, naplózás és kockázati Eseménynapló-források integrálása bármely Siem vagy monitoring eszközzel, például az Azure Sentinel használatával.

Az Azure Active Directory (Azure AD) felhasználói fiókok diagnosztikai beállításainak létrehozásával, valamint a naplók és a bejelentkezési naplók Log Analytics munkaterületre való elküldésével egyszerűsítheti ezt a folyamatot. A kívánt riasztások konfigurálása a Log Analytics munkaterületen belül.

- [Azure-tevékenységnaplók integrálása az Azure Monitorba](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3,12: riasztás a fiók bejelentkezési viselkedésének eltérése esetén

**Útmutató**: a Azure Active Directory (Azure ad) kockázatkezelési és identitás-védelmi funkcióinak használatával konfigurálhatja a felhasználói identitásokkal kapcsolatos gyanús műveletekre vonatkozó automatizált válaszokat. További vizsgálat céljából betöltheti az Azure Sentinelbe.

- [Az Azure AD kockázatos bejelentkezéseinek megtekintése](../active-directory/identity-protection/overview-identity-protection.md)

- [Az Identity Protection kockázati házirendjeinek konfigurálása és engedélyezése](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [Az Azure Sentinel előkészítése](../sentinel/quickstart-onboard.md)

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

## <a name="data-protection"></a>Adatvédelem

*További információt az [Azure biztonsági teljesítményteszt: Adatvédelem](../security/benchmarks/security-control-data-protection.md)című témakörben talál.*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4,1: bizalmas információk leltárának fenntartása

**Útmutató**: az Azure webalkalmazási TŰZFAL (WAF) és a bizalmas adatokat tároló vagy feldolgozó kapcsolódó erőforrások nyomon követésében segítséget nyújtó címkék használatával.
- [Címkék létrehozása és használata](../azure-resource-manager/management/tag-resources.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4,2: bizalmas adatok tárolására vagy feldolgozására szolgáló rendszerek elkülönítése

**Útmutató**: az elkülönítés megvalósítása különálló előfizetések és felügyeleti csoportok használatával az egyes biztonsági tartományokhoz, például a környezeti típushoz és az adatérzékenységi szinthez, például fejlesztési, tesztelési és éles környezetekhez. 

Azure-erőforrásokhoz való hozzáférés szabályozása Azure szerepköralapú hozzáférés-vezérléssel (Azure RBAC).

- [További Azure-előfizetések létrehozása](../cost-management-billing/manage/create-subscription.md)

- [Management Groups létrehozása](../governance/management-groups/create-management-group-portal.md)

- [Címkék létrehozása és használata](../azure-resource-manager/management/tag-resources.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4,4: minden bizalmas adat titkosítása az átvitel során

**Útmutató**: az összes bizalmas adat titkosítása az átvitel során. Győződjön meg arról, hogy az Azure webalkalmazási tűzfal (WAF) példányaihoz és a kapcsolódó erőforrásokhoz csatlakozó ügyfelek képesek a TLS 1,2-es vagy újabb egyeztetésére.

Kövesse Security Center a inaktív adatok titkosítására és a titkosításra vonatkozó ajánlásokat, ha szükséges.

- [A titkosítás ismertetése az Azure-ban](../security/fundamentals/encryption-overview.md#encryption-of-data-in-transit)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Megosztott

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4,6: az erőforrásokhoz való hozzáférés szabályozása az Azure RBAC

**Útmutató**: az Azure-erőforrásokhoz való hozzáférés szabályozása az Azure szerepköralapú hozzáférés-vezérléssel (Azure RBAC).
- [Az Azure RBAC konfigurálása](../role-based-access-control/role-assignments-portal.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="48-encrypt-sensitive-information-at-rest"></a>4,8: bizalmas adatok titkosítása a nyugalmi állapotban

**Útmutató**: a REST titkosítás használata az összes Azure-erőforráshoz, beleértve az Azure Web Application Firewall (WAF) és a kapcsolódó erőforrásokat. A Microsoft azt javasolja, hogy az Azure kezelje a titkosítási kulcsokat, azonban lehetőség van arra, hogy a saját kulcsait bizonyos példányokban kezelje.

- [Inaktív adatok Azure-ban való titkosításának ismertetése](../security/fundamentals/encryption-atrest.md)

- [Az ügyfél által felügyelt titkosítási kulcsok konfigurálása](../storage/common/customer-managed-keys-configure-key-vault.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4,9: a kritikus Azure-erőforrások változásainak naplózása és riasztása

**Útmutató**: az Azure webalkalmazási TŰZFAL (WAF) konfigurálása megelőzési módban való futtatásra, miután viszonyítási a hálózati forgalmat az észlelési módban az előre meghatározott időtartamra. 

Az Azure-WAF megelőzési módban blokkolja a szabályok által észlelt behatolásokat és támadásokat. A támadó "403 jogosulatlan hozzáférés" kivételt kap, és a kapcsolat bezárult. A megelőzési mód rögzíti az ilyen támadásokat a WAF-naplókban.

- [Application Gateway és Azure Security Center közötti integráció áttekintése](../security-center/security-center-partner-integration.md)

- [WAF módok Application Gateway](ag/ag-overview.md#waf-modes)

- [WAF módok a bejárati ajtón](afds/afds-overview.md#waf-modes)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

## <a name="inventory-and-asset-management"></a>Leltár- és eszközfelügyelet

*További információt az [Azure biztonsági teljesítményteszt: leltár és eszközkezelés](../security/benchmarks/security-control-inventory-asset-management.md)című témakörben talál.*

### <a name="61-use-automated-asset-discovery-solution"></a>6,1: automatikus eszköz-felderítési megoldás használata

**Útmutató**: az Azure Resource Graph használatával lekérdezheti vagy felderítheti az összes erőforrást, például a számítást, a tárterületet, a hálózatot, a portokat és a protokollokat, és így tovább az előfizetéseken belül. 

Győződjön meg arról, hogy a bérlőben a megfelelő (olvasási) engedélyek szerepelnek, valamint az összes Azure-előfizetés, valamint az előfizetésében lévő erőforrások számbavétele. Bár a klasszikus Azure-erőforrások felderítése az erőforrás-gráfon keresztül lehetséges, erősen ajánlott a Azure Resource Manager erőforrások létrehozása és használata.

- [Lekérdezések létrehozása az Azure Resource Graph használatával](../governance/resource-graph/first-query-portal.md)

- [Azure-előfizetések megtekintése](/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0)

- [Az Azure RBAC ismertetése](../role-based-access-control/overview.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="62-maintain-asset-metadata"></a>6,2: az eszköz metaadatainak fenntartása

**Útmutató**: címkék használata az Azure Web Application Firewall (WAF) házirendjeiben. A címkék társítva vannak az erőforrásokhoz, és logikailag alkalmazhatók az erőforrásokhoz való hozzáférés megszervezésére egy ügyfél-előfizetésben. 

- [Címkék létrehozása és használata](../azure-resource-manager/management/tag-resources.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="63-delete-unauthorized-azure-resources"></a>6,3: jogosulatlan Azure-erőforrások törlése

**Útmutató**: címkézés, felügyeleti csoportok és külön előfizetések használata, ahol szükséges, az Azure-WAF és a kapcsolódó erőforrások rendszerezése és nyomon követése. Rendszeres időközönként egyeztetheti a leltárt, és gondoskodhat arról, hogy a jogosulatlan erőforrások törlése az előfizetésből időben történjen.

- [További Azure-előfizetések létrehozása](../cost-management-billing/manage/create-subscription.md)

- [Management Groups létrehozása](../governance/management-groups/create-management-group-portal.md)

- [Címkék létrehozása és használata](../azure-resource-manager/management/tag-resources.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6,4: a jóváhagyott Azure-erőforrások leltárának meghatározása és karbantartása

**Útmutató**: a jóváhagyott erőforrások leltárának létrehozása, beleértve a szervezeti igények alapján történő konfigurálást.

A Azure Policy használatával korlátozásokat állíthat be az előfizetésekben létrehozható erőforrások típusára. Az Azure Resource Graph használatával lekérdezheti és felderítheti az előfizetésükön belüli erőforrásokat. Győződjön meg arról, hogy a környezetben lévő összes Azure-erőforrás jóvá van hagyva.

- [Az Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md)

- [Lekérdezések létrehozása az Azure Resource Graph használatával](../governance/resource-graph/first-query-portal.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="65-monitor-for-unapproved-azure-resources"></a>6,5: a nem jóváhagyott Azure-erőforrások figyelése

**Útmutató**: a Azure Policy használatával korlátozásokat állíthat be az előfizetésekben létrehozható erőforrásokra vonatkozóan.
Az Azure Resource Graph használatával lekérdezheti vagy felderítheti az Azure webalkalmazási tűzfal (WAF) erőforrásait az előfizetéseken belül. Győződjön meg arról, hogy a környezetben lévő összes Azure-WAF és kapcsolódó erőforrás jóvá van hagyva.

- [Az Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md)

- [Lekérdezések létrehozása az Azure Graph használatával](../governance/resource-graph/first-query-portal.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6,7: nem jóváhagyott Azure-erőforrások és szoftveralkalmazások eltávolítása

**Útmutató**: a nem jóváhagyott Azure WAF-erőforrások figyelése és eltávolítása Azure Policy az Azure-WAF telepítésének megtagadásához, vagy bizonyos típusú WAF, például az Azure WAF v1 vs v2.

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="69-use-only-approved-azure-services"></a>6,9: csak jóváhagyott Azure-szolgáltatások használata

**Útmutató**: a Azure Policy használatával korlátozhatja, hogy mely szolgáltatásokat lehet kiépíteni a környezetében.

- [Az Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md)

- [Adott erőforrástípus megtagadása a következővel Azure Policy](../governance/policy/samples/index.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6,11: korlátozza a felhasználók képességét a Azure Resource Manager való interakcióra

**Útmutató**: az Azure feltételes hozzáférés használatával korlátozhatja, hogy a felhasználók hogyan kezelhetik az Azure Resources Managert az "Microsoft Azure felügyelet" alkalmazás "hozzáférés letiltása" beállításával.

- [Feltételes hozzáférés konfigurálása az Azure Resources Manager elérésének blokkolásához](../role-based-access-control/conditional-access-azure-management.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6,13: fizikailag vagy logikailag elkülöníthető a nagy kockázatú alkalmazások

**Útmutató**: az Azure webalkalmazási TŰZFAL (WAF) különböző környezetekhez társítható hálózatokon, erőforráscsoportokon vagy előfizetéseken keresztül a nagy kockázatú alkalmazások elkülönítéséhez.

- [Az Azure Virtual Network áttekintése](../virtual-network/virtual-networks-overview.md)

- [Erőforrások rendszerezése az Azure-beli felügyeleti csoportokkal](../governance/management-groups/overview.md)

- [Útmutató az előfizetéssel kapcsolatos döntésekhez](/azure/cloud-adoption-framework/decision-guides/subscriptions/)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

## <a name="secure-configuration"></a>Biztonságos konfiguráció

*További információt az [Azure biztonsági teljesítményteszt: biztonságos konfiguráció](../security/benchmarks/security-control-secure-configuration.md)című témakörben talál.*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7,1: biztonságos konfigurációk létrehozása az összes Azure-erőforráshoz

**Útmutató**: szabványos biztonsági konfigurációk definiálása és implementálása az Azure webalkalmazási TŰZFAL (WAF) üzembe helyezéséhez kapcsolódó hálózati beállításokhoz.
A "Microsoft. Network" névtérben Azure Policy Aliasok használatával egyéni szabályzatokat hozhat létre az Azure Application Gateway, a Virtual Networks és a hálózati biztonsági csoportok hálózati konfigurációjának naplózásához vagy betartatásához, valamint beépített szabályzat-definíciókat használhat.

- [Az elérhető Azure Policy aliasok megtekintése](/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0)

- [Az Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="73-maintain-secure-azure-resource-configurations"></a>7,3: biztonságos Azure-erőforrás-konfigurációk karbantartása

**Útmutató**: a Azure Policy [megtagadás] és [üzembe helyezés, ha nem létezik] hatása az Azure webalkalmazási TŰZFAL (WAF) és a kapcsolódó erőforrások biztonságos beállításainak betartatására. 

Azure Resource Manager-sablonok használatával megőrizheti az Azure-WAF és a szervezete által igényelt kapcsolódó erőforrások biztonsági konfigurációját.

- [Azure Policy effektusok ismertetése](../governance/policy/concepts/effects.md)

- [Szabályzatok létrehozása és kezelése a megfelelőség kikényszerítése céljából](../governance/policy/tutorials/create-and-manage.md)

- [Azure Resource Manager sablonok áttekintése](../azure-resource-manager/templates/overview.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="75-securely-store-configuration-of-azure-resources"></a>7,5: az Azure-erőforrások biztonságos tárolása

**Útmutató**: az Azure DevOps használatával biztonságosan tárolhatók és kezelhetők a kódok, például egyéni Azure-házirendek és Azure Resource Manager-sablonok. 

Engedélyezi vagy megtagadja az engedélyek megadását a Azure Active Directory (Azure AD) által meghatározott felhasználók, beépített biztonsági csoportok vagy csoportok számára, ha az integrálva van az Azure DevOps, vagy Active Directory, ha az integrálva van Team Foundation Server (TFS).

- [Kód tárolása az Azure DevOps](/azure/devops/repos/git/gitworkflow?view=azure-devops)

- [Engedélyek és csoportok az Azure DevOps](/azure/devops/organizations/security/about-permissions)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7,7: az Azure-erőforrások konfigurációs felügyeleti eszközeinek üzembe helyezése

**Útmutató**: a "Microsoft. Network" névtérben található beépített Azure Policy definíciók és Azure Policy Aliasok használatával egyéni szabályzatokat hozhat létre a riasztáshoz, a naplózáshoz és a rendszerkonfigurációk kényszerítéséhez. Dolgozzon ki egy folyamatot és folyamatot a házirend-kivételek kezeléséhez.

- [Az Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md)

- [Az Azure Policy dokumentációja](../governance/policy/index.yml)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7,9: automatikus konfigurációs monitorozás megvalósítása Azure-erőforrásokhoz

**Útmutató**: a "Microsoft. Network" névtérben található beépített Azure Policy definíciók és Azure Policy Aliasok használatával egyéni szabályzatokat hozhat létre a riasztáshoz, a naplózáshoz és a rendszerkonfigurációk kényszerítéséhez. 

Az Azure-erőforrások konfigurációinak automatikus érvényesítéséhez használja a Azure Policy [audit], [megtagadás] és [üzembe helyezés, ha nem léteznek] effektusokat.

- [Az Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md)

- [Az Azure Policy dokumentációja](../governance/policy/index.yml)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="711-manage-azure-secrets-securely"></a>7,11: az Azure-titkok biztonságos kezelése

**Útmutató**: az Azure Key Vault használata a tanúsítványok biztonságos tárolására. A Azure Key Vault egy platform által felügyelt titkos tároló, amely a titkok, kulcsok és SSL-tanúsítványok védelmére használható. 

Az Azure Application Gateway támogatja a HTTPS-kompatibilis figyelőkhöz csatolt kiszolgálói tanúsítványok Key Vault-integrációját. 

- [Az SSL-lezárás konfigurálása Key Vault tanúsítványokkal a Azure PowerShell használatával](../application-gateway/configure-keyvault-ps.md)

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="713-eliminate-unintended-credential-exposure"></a>7,13: a hitelesítő adatok nem szándékolt expozíciójának megszüntetése

**Útmutató**: a hitelesítő adatoknak a kódban való azonosítására szolgáló hitelesítő adatok beolvasása, amely a felderített hitelesítő adatok több biztonságos helyen való áthelyezését is javasolja, például Azure Key Vault
- [A hitelesítő adatok beolvasójának beállítása](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

## <a name="data-recovery"></a>Adat-helyreállítás

*További információt az [Azure biztonsági teljesítményteszt: adat-helyreállítás](../security/benchmarks/security-control-data-recovery.md)című témakörben talál.*

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: a biztonsági másolatok és az ügyfél által felügyelt kulcsok védelmének biztosítása

**Útmutató**: Ellenőrizze, hogy engedélyezve van-e a soft delete Azure Key Vault. A Soft delete lehetővé teszi a törölt kulcstartók és tároló objektumok, például kulcsok, titkos kódok és tanúsítványok helyreállítását.

- [A Azure Key Vault Soft delete használata](../key-vault/general/key-vault-recovery.md)

**Az Azure Security Center monitorozása**: Igen

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

**Útmutató**: a Security Center súlyosságot rendel az egyes riasztásokhoz, így rangsorolhatja, hogy a rendszer mely riasztásokat vizsgálja meg elsőként. A súlyosság azon alapul, hogy az Security Center milyen mértékben szerepel a riasztás kibocsátásához használt elemzésben vagy elemzésben, valamint azt a megbízhatósági szintet, amely a riasztáshoz vezető tevékenység mögött rosszindulatú szándékú volt.
Egyértelműen megjelölheti az előfizetéseket (például éles környezetben, nem éles környezetben), és létrehozhat egy elnevezési rendszert az Azure-erőforrások egyértelmű azonosításához és kategorizálásához.

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="103-test-security-response-procedures"></a>10,3: biztonsági reagálási eljárások tesztelése

**Útmutató**: a rendszerek incidensek reagálási képességeinek rendszeres tesztelésére szolgáló gyakorlatok végrehajtása. Azonosítsa a gyenge pontokat és réseket, és szükség esetén dolgozza át a tervet.
- [Az informatikai csomagok és képességek tesztelésére, betanítására és gyakorlatára vonatkozó publikációs útmutató a NIST kiadványról](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10,4: biztonsági incidensek elérhetőségének biztosítása és riasztási értesítések konfigurálása biztonsági incidensekhez

**Útmutató**: a Microsoft a biztonsági incidensek elérhetőségi adatait arra használja fel, hogy felvegye Önnel a kapcsolatot, ha a Microsoft Security Response Center (MSRC) felfedi, hogy az ügyfél adatait egy törvénytelen vagy jogosulatlan fél is hozzáférte. A problémák megoldása érdekében tekintse át az incidenseket a tény után.
- [A Azure Security Center biztonsági kapcsolattartó beállítása](../security-center/security-center-provide-security-contact-details.md)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10,5: biztonsági riasztások beépítése az incidensek gyorsreagáló rendszerébe

**Útmutató**: az Security Center-riasztások és-javaslatok exportálása a folyamatos exportálás funkció használatával. A folyamatos exportálás lehetővé teszi a riasztások és javaslatok manuális és folyamatos exportálását. A Azure Security Center adatösszekötővel továbbíthatja a riasztásokat az Azure Sentinelnek a szervezet igényeinek megfelelően.

- [Folyamatos exportálás konfigurálása](../security-center/continuous-export.md)

- [Riasztások streamelése az Azure Sentinelbe](../sentinel/connect-azure-security-center.md)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="106-automate-the-response-to-security-alerts"></a>10,6: a biztonsági riasztásokra adott válasz automatizálása

**Útmutató**: a Security Center munkafolyamat-automatizálási funkciója segítségével automatikusan aktiválhatja a válaszokat a "Logic apps" használatával a biztonsági riasztások és javaslatok esetében.
- [A Munkafolyamat-automatizálás és a Logic Apps konfigurálása](../security-center/workflow-automation.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

## <a name="penetration-tests-and-red-team-exercises"></a>Behatolási tesztek és Red Team-gyakorlatok

*További információkért tekintse meg az [Azure biztonsági teljesítményteszt: behatolási tesztek és a Red Team gyakorlatok](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md)című témakört.*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11,1: az Azure-erőforrások rendszeres behatolásának tesztelése, valamint az összes kritikus biztonsági vizsgálat szervizelésének biztosítása

**Útmutató**: kövesse a Microsoft részvételi szabályait, hogy a behatolási tesztek ne sértsék a Microsoft-házirendeket. A Microsoft által felügyelt felhőalapú infrastruktúrával, szolgáltatásokkal és alkalmazásokkal kapcsolatban a Microsoft stratégiáját és a Red Teaming és a Live site penetráció tesztelését is használhatja. 

- [Behatolástesztelési beavatkozási szabályok](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) 

- [Riasztási tesztek a Microsoft-felhőben](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Megosztott

## <a name="next-steps"></a>Következő lépések

- Lásd az [Azure biztonsági teljesítménytesztét](../security/benchmarks/overview.md)
- További tudnivalók az [Azure biztonsági alapterveiről](../security/benchmarks/security-baselines-overview.md)
