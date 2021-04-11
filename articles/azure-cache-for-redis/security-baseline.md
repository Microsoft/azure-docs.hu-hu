---
title: Azure-beli biztonsági alaptervek az Azure cache-hez a Redis
description: Az Redis biztonsági alapkonfigurációhoz készült Azure cache eljárási útmutatást és erőforrásokat biztosít az Azure biztonsági Teljesítménytesztben meghatározott biztonsági javaslatok megvalósításához.
author: msmbaldwin
ms.service: cache
ms.topic: conceptual
ms.date: 02/17/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: f07eece54bfe456e173e664b19777cfc98b71368
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105566863"
---
# <a name="azure-security-baseline-for-azure-cache-for-redis"></a>Azure-beli biztonsági alaptervek az Azure cache-hez a Redis

Ez a biztonsági alapkonfiguráció az [Azure biztonsági teljesítményteszt 1,0-es verziójáról](../security/benchmarks/overview-v1.md) az Azure cache-re vonatkozó útmutatót alkalmazza a Redis. Az Azure Security Benchmark ajánlásokat ad arra nézve, hogy hogyan tehetők biztonságossá a felhőalapú megoldások az Azure-ban.
A tartalom az Azure biztonsági teljesítményteszt által meghatározott **biztonsági vezérlők** szerint van csoportosítva, valamint a Redis-hez készült Azure cache-re vonatkozó kapcsolódó útmutatás. A Redis Azure cache-re nem alkalmazható **vezérlők** ki lettek zárva.

 
Ha szeretné megtekinteni, hogy az Azure cache hogyan Redis teljes mértékben az Azure biztonsági teljesítményteszttel, tekintse meg a teljes Azure cache-t a Redis biztonsági alapkonfiguráció- [hozzárendelési fájljához](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)

## <a name="network-security"></a>Hálózati biztonság

*További információ: [Azure Security Benchmark: Hálózati biztonság](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1,1: Azure-erőforrások biztosítása virtuális hálózatokon belül

**Útmutató**: az Azure cache üzembe helyezése a Redis-példányon egy virtuális hálózaton (VNet) belül. A VNet a felhőben található magánhálózat. Ha egy Azure cache for Redis-példány VNet van konfigurálva, nem nyilvánosan címezhető, és csak a VNet lévő virtuális gépekről és alkalmazásokról érhető el.

A tűzfalszabályok a kezdő és a záró IP-címtartomány is megadhatók. A tűzfalszabályok konfigurálásakor csak a megadott IP-címtartományok kapcsolatai kapcsolódhatnak a gyorsítótárhoz.

- [A prémium szintű Azure cache Virtual Network támogatásának konfigurálása a Redis-hez](cache-how-to-premium-vnet.md)

- [Az Azure cache konfigurálása Redis tűzfalszabályok esetén](./cache-configure.md#firewall)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-network-interfaces"></a>1,2: a virtuális hálózatok, alhálózatok és hálózati adapterek konfigurációjának és forgalmának figyelése és naplózása

**Útmutatás**: ha a Virtual Machines a Redis-példányhoz tartozó Azure cache-vel azonos virtuális hálózaton vannak telepítve, akkor a hálózati biztonsági csoportok (NSG-EK) segítségével csökkentheti az adatkiszűrése kockázatát. NSG-adatforgalmi naplók engedélyezése és naplók küldése Azure Storage-fiókba forgalom-ellenőrzés céljából. NSG-naplókat is küldhet egy Log Analytics munkaterületre, és a Traffic Analytics használatával betekintést nyerhet az Azure-Felhőbeli forgalomba. A Traffic Analytics egyes előnyei lehetővé teszi a hálózati tevékenységek megjelenítését és a gyakori pontok azonosítását, a biztonsági fenyegetések azonosítását, a forgalomban rejlő minták értelmezését, valamint a hálózati helytelen konfigurációk meghatározását.

- [A NSG folyamat naplófájljainak engedélyezése](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [A Traffic Analytics engedélyezése és használata](../network-watcher/traffic-analytics.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="13-protect-critical-web-applications"></a>1,3: a kritikus webalkalmazások megóvása

**Útmutató**: nem alkalmazható; Ez a javaslat Azure App Service vagy számítási erőforrásokon futó webalkalmazásokhoz készült.

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1,4: az ismert kártevő IP-címekkel folytatott kommunikáció megtagadása

**Útmutató**: az Azure Virtual Network (VNet) üzembe helyezése fokozott biztonságot és elkülönítést biztosít az Azure cache Redis, valamint az alhálózatok, hozzáférés-vezérlési házirendek és egyéb funkciók számára a hozzáférés további korlátozásához. A VNet-ben üzembe helyezett Azure cache nem nyilvánosan címezhető, és csak a VNet lévő virtuális gépekről és alkalmazásokról érhető el.

A Redis-példányok esetében az elosztott szolgáltatásmegtagadási (DDoS) támadások elleni védelem érdekében engedélyezze az Azure cache-hez társított virtuális hálózatok DDoS Protection szabványt. A Azure Security Center integrált fenyegetési intelligencia használatával megtagadhatja a kommunikációt az ismert kártékony vagy nem használt internetes IP-címekkel.

- [A prémium szintű Azure cache Virtual Network támogatásának konfigurálása a Redis-hez](cache-how-to-premium-vnet.md)

- [Azure DDoS Protection standard kezelése a Azure Portal használatával](../ddos-protection/manage-ddos-protection.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="15-record-network-packets"></a>1,5: hálózati csomagok rögzítése

**Útmutató**: Ha a virtuális gépeket ugyanabban a virtuális hálózatban telepítik, mint a Redis-példány Azure-gyorsítótárát, akkor a hálózati biztonsági csoportok (NSG-EK) használatával csökkentheti az adatkiszűrése kockázatát. NSG-adatforgalmi naplók engedélyezése és naplók küldése Azure Storage-fiókba forgalom-ellenőrzés céljából. NSG-naplókat is küldhet egy Log Analytics munkaterületre, és a Traffic Analytics használatával betekintést nyerhet az Azure-Felhőbeli forgalomba. A Traffic Analytics egyes előnyei lehetővé teszi a hálózati tevékenységek megjelenítését és a gyakori pontok azonosítását, a biztonsági fenyegetések azonosítását, a forgalomban rejlő minták értelmezését, valamint a hálózati helytelen konfigurációk meghatározását.

- [A NSG folyamat naplófájljainak engedélyezése](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [A Traffic Analytics engedélyezése és használata](../network-watcher/traffic-analytics.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1,6: hálózati behatolás-észlelési/Behatolás-megelőzési rendszerek (AZONOSÍTÓk/IP-címek) üzembe helyezése

**Útmutató**: Ha az Azure cache-t az Azure app Service-vagy számítási példányokon futó webalkalmazások Redis használja, helyezzen üzembe minden erőforrást egy azure-Virtual Network (VNet) belül, és gondoskodjon az Azure webalkalmazási TŰZFALLAL (WAF) a webes Application Gateway. Konfigurálja úgy a WAF, hogy "prevenciós módban" fusson. A megelőzési mód blokkolja a behatolásokat és a szabályok által észlelhető támadásokat. A támadó "403 jogosulatlan hozzáférés" kivételt kap, és a kapcsolat bezárult. A megelőzési mód rögzíti az ilyen támadásokat a WAF-naplókban.

Azt is megteheti, hogy az Azure piactéren olyan ajánlatot választ, amely támogatja az AZONOSÍTÓk/IP-címek funkciót a hasznos adatok ellenőrzése és/vagy anomália észlelési képességei alapján.

- [Az Azure WAF képességeinek megismerése](../web-application-firewall/ag/ag-overview.md)

- [Az Azure WAF üzembe helyezése](../web-application-firewall/ag/create-waf-policy-ag.md)

- [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/?term=Firewall)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="17-manage-traffic-to-web-applications"></a>1,7: webalkalmazások forgalmának kezelése

**Útmutató**: nem alkalmazható; Ez a javaslat Azure App Service vagy számítási erőforrásokon futó webalkalmazásokhoz készült.

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1,8: a hálózati biztonsági szabályok bonyolultságának és adminisztratív terhelésének csökkentése

**Útmutató**: hálózati biztonsági csoportok (NSG-EK) vagy Azure Firewall hálózati hozzáférés-vezérlésének definiálásához használja a Virtual Network szolgáltatás címkéit. Biztonsági szabályok létrehozása során szolgáltatáscímkéket használhat bizonyos IP-címek helyett. Ha egy szabály megfelelő forrás vagy cél mezőjében megadja a szolgáltatási címke nevét (például ApiManagement), akkor engedélyezheti vagy megtagadhatja a megfelelő szolgáltatás forgalmát. A Microsoft kezeli a szolgáltatási címke által felölelt címek előtagjait, és automatikusan frissíti a szolgáltatási címkét a címek változásával.

Az alkalmazás biztonsági csoportjai (ASG-EK) is használhatók az összetett biztonsági konfiguráció egyszerűsítéséhez. A ASG lehetővé teszi a hálózati biztonság konfigurálását az alkalmazások struktúrájának természetes kiterjesztéseként, lehetővé téve a virtuális gépek csoportosítását és a hálózati biztonsági házirendek definiálását ezen csoportok alapján.

- [Virtuális hálózati szolgáltatás címkéi](../virtual-network/service-tags-overview.md)

- [Alkalmazás biztonsági csoportjai](../virtual-network/network-security-groups-overview.md#application-security-groups)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1,9: a hálózati eszközök szabványos biztonsági konfigurációinak fenntartása

**Útmutató**: az Azure cache-hez kapcsolódó hálózati erőforrásokra vonatkozó szabványos biztonsági konfigurációk definiálása és implementálása Azure Policy Redis-példányokhoz. Használjon Azure Policy aliasokat a "Microsoft. cache" és a "Microsoft. Network" névterekben, hogy egyéni szabályzatokat hozzon létre az Azure cache Redis-példányok hálózati konfigurációjának naplózásához vagy érvénybe léptetéséhez. Olyan beépített szabályzat-definíciókat is használhat, mint például a következők:
- Csak a Redis Cache biztonságos kapcsolatai legyenek engedélyezve

- DDoS Protection a standardot engedélyezni kell

Az Azure-tervezetek segítségével leegyszerűsítheti a nagy léptékű Azure-környezetek kiépítését a főbb környezeti összetevők, például a Azure Resource Manager sablonok, az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC) és a házirendek alapján, egyetlen terv definíciójában. Egyszerűen alkalmazhatja a tervrajzot új előfizetésekre és környezetekre, és részletesen beállíthatja a vezérlést és a felügyeletet a verziószámozással.

- [Az Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md)

- [Azure Blueprint létrehozása](../governance/blueprints/create-blueprint-portal.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="110-document-traffic-configuration-rules"></a>1,10: a dokumentum forgalmának konfigurációs szabályai

**Útmutató**: a Redis üzembe helyezéséhez az Azure cache-hez társított hálózati erőforrásokhoz használjon címkéket, hogy logikailag szervezze őket a besorolásba.

- [Címkék létrehozása és használata](../azure-resource-manager/management/tag-resources.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1,11: automatikus eszközök használata a hálózati erőforrások konfigurációjának figyelésére és a változások észlelésére

**Útmutató**: az Azure-tevékenység naplójának használatával figyelheti a hálózati erőforrás-konfigurációkat, és azonosíthatja a Redis-példányok Azure cache-hez kapcsolódó hálózati erőforrásainak változásait. Hozzon létre riasztásokat Azure Monitoron belül, amelyek akkor lépnek életbe, amikor a kritikus hálózati erőforrásokra vonatkozó módosításokat végrehajtják

- [Azure-Tevékenységnaplók eseményeinek megtekintése és lekérése](../azure-monitor/essentials/activity-log.md#view-the-activity-log)

- [Riasztások létrehozása a Azure Monitorban](../azure-monitor/alerts/alerts-activity-log.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

## <a name="logging-and-monitoring"></a>Naplózás és monitorozás

*További információt az [Azure biztonsági teljesítményteszt: naplózás és figyelés](../security/benchmarks/security-control-logging-monitoring.md)című témakörben talál.*

### <a name="22-configure-central-security-log-management"></a>2,2: a központi biztonsági naplók felügyeletének konfigurálása

**Útmutató**: az Azure-műveletnapló diagnosztikai beállításainak engedélyezése és a naplók elküldése egy log Analytics-munkaterületre, Azure Event hub vagy Azure Storage-fiókba az archívumhoz. A tevékenységi naplók betekintést nyújtanak az Azure-gyorsítótárban végrehajtott műveletekre a Redis-példányok szintjén. Az Azure-tevékenység naplójának adatai segítségével meghatározhatja a "mi, ki és mikor" típusú írási műveleteket (PUT, POST, DELETE) az Azure cache Redis-példányok esetében végzett vezérlési sík szintjén.

- [Diagnosztikai beállítások engedélyezése az Azure-beli tevékenység naplójában](../azure-monitor/essentials/activity-log.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="23-enable-audit-logging-for-azure-resources"></a>2,3: az Azure-erőforrások naplózásának engedélyezése

**Útmutató**: az Azure-műveletnapló diagnosztikai beállításainak engedélyezése és a naplók elküldése egy log Analytics-munkaterületre, Azure Event hub vagy Azure Storage-fiókba az archívumhoz. A tevékenységi naplók betekintést nyújtanak az Azure-gyorsítótárban végrehajtott műveletekre a Redis-példányok szintjén. Az Azure-tevékenység naplójának adatai segítségével meghatározhatja a "mi, ki és mikor" típusú írási műveleteket (PUT, POST, DELETE) az Azure cache Redis-példányok esetében végzett vezérlési sík szintjén.

Bár a mérőszámok elérhetők a diagnosztikai beállítások engedélyezésével, a naplózás az adatsíkon még nem érhető el az Azure cache for Redis számára.

- [Diagnosztikai beállítások engedélyezése az Azure-beli tevékenység naplójában](../azure-monitor/essentials/activity-log.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="25-configure-security-log-storage-retention"></a>2,5: a biztonsági napló tárolási adatmegőrzésének konfigurálása

**Útmutató**: a Azure monitor az Azure cache-hez társított log Analytics-munkaterületek naplózási megőrzési időszakának beállítása a szervezet megfelelőségi előírásai szerint Redis-példányokhoz.

Vegye figyelembe, hogy az adatsíkon végzett naplózás még nem érhető el az Azure cache for Redis számára.

- [Napló-megőrzési paraméterek beállítása](../azure-monitor/logs/manage-cost-storage.md#change-the-data-retention-period)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="26-monitor-and-review-logs"></a>2,6: naplók figyelése és áttekintése

**Útmutató**: az Azure-műveletnapló diagnosztikai beállításainak engedélyezése és a naplók elküldése egy log Analytics munkaterületre. Lekérdezéseket hajthat végre Log Analytics a kifejezések kereséséhez, a trendek azonosításához, a mintázatok elemzéséhez, valamint számos más elemzéshez a Redis-hez készült Azure cache-hez gyűjtött tevékenység-naplózási adatok alapján.

Vegye figyelembe, hogy az adatsíkon végzett naplózás még nem érhető el az Azure cache for Redis számára.

- [Diagnosztikai beállítások engedélyezése az Azure-beli tevékenység naplójában](../azure-monitor/essentials/activity-log.md)

- [Azure-Tevékenységnaplók összegyűjtése és elemzése Log Analytics munkaterületen Azure Monitor](../azure-monitor/essentials/activity-log.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="27-enable-alerts-for-anomalous-activities"></a>2,7: riasztások engedélyezése rendellenes tevékenységekhez

**Útmutató**: beállíthatja, hogy a riasztásokat a Redis-példányokhoz tartozó Azure cache-hez kapcsolódó mérőszámok és tevékenységi naplók alapján fogadja. Azure Monitor lehetővé teszi a riasztások e-mail-értesítések küldését, egy webhook meghívását vagy egy Azure logikai alkalmazás meghívását.

Bár a mérőszámok elérhetők a diagnosztikai beállítások engedélyezésével, a naplózás az adatsíkon még nem érhető el az Azure cache for Redis számára.

- [Riasztások konfigurálása az Azure cache-hez a Redis-hez](./cache-how-to-monitor.md#alerts)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

## <a name="identity-and-access-control"></a>Identitás- és hozzáférés-vezérlés

*További információt az [Azure biztonsági teljesítményteszt: Identity and Access Control](../security/benchmarks/security-control-identity-access-control.md)című témakörben talál.*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3,1: a felügyeleti fiókok leltárának karbantartása

**Útmutató**: Azure Active Directory (Azure ad) beépített szerepkörökkel rendelkezik, amelyeket explicit módon kell hozzárendelni, és lekérdezhető. Az Azure AD PowerShell-modullal ad hoc lekérdezéseket hajthat végre a felügyeleti csoportok tagjait futtató fiókok felderítéséhez.

- [Címtárbeli szerepkör beszerzése az Azure AD-ben a PowerShell-lel](/powershell/module/azuread/get-azureaddirectoryrole?preserve-view=true&view=azureadps-2.0)

- [Címtárbeli szerepkör tagjainak beszerzése az Azure AD-ben a PowerShell-lel](/powershell/module/azuread/get-azureaddirectoryrolemember?preserve-view=true&view=azureadps-2.0)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="32-change-default-passwords-where-applicable"></a>3,2: az alapértelmezett jelszavak módosítása, ha alkalmazható

**Útmutató**: a Redis Azure cache-hez való hozzáférésének szabályozása Azure Active Directory (Azure ad) segítségével történik. Az Azure AD nem rendelkezik az alapértelmezett jelszavak fogalmával.

Az Azure cache Redis-hez való hozzáférését hozzáférési kulcsok vezérlik. Ezeket a kulcsokat a gyorsítótárhoz csatlakozó ügyfelek használják, és bármikor újra létrehozhatók.

Nem ajánlott alapértelmezett jelszavakat létrehozni az alkalmazásba. Ehelyett a jelszavakat Azure Key Vault, majd az Azure AD használatával is tárolhatja.

- [Az Azure cache újragenerálása a Redis hozzáférési kulcsaihoz](./cache-configure.md#settings)

**Felelősség**: Megosztott

**Azure Security Center figyelés**: nincs

### <a name="33-use-dedicated-administrative-accounts"></a>3,3: dedikált rendszergazdai fiókok használata

**Útmutató**: szabványos üzemeltetési eljárások létrehozása a dedikált rendszergazdai fiókok használata körül. A rendszergazdai fiókok számának figyeléséhez használja a Azure Security Center identitás-és hozzáférés-kezelés lehetőséget.

Emellett a dedikált rendszergazdai fiókok nyomon követésének elősegítése érdekében Azure Security Center vagy beépített Azure-szabályzatokból származó javaslatokat is használhat, például:

- Az előfizetéshez egynél több tulajdonos rendelhető hozzá

- A tulajdonosi engedélyekkel rendelkező elavult fiókokat el kell távolítani az előfizetésből

- A tulajdonosi engedélyekkel rendelkező külső fiókokat el kell távolítani az előfizetésből

További információkat az alábbi hivatkozásokon találhat:

- [Az identitás és a hozzáférés figyelésének Azure Security Center használata (előzetes verzió)](../security-center/security-center-identity-access.md)

- [A Azure Policy használata](../governance/policy/tutorials/create-and-manage.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="34-use-azure-active-directory-single-sign-on-sso"></a>3,4: Azure Active Directory egyszeri bejelentkezés (SSO) használata

**Útmutató**: a Redis készült Azure cache hozzáférési kulcsokat használ a felhasználók hitelesítéséhez, és nem támogatja az egyszeri bejelentkezést (SSO) az adatsík szintjén. Az Azure cache Redis-hez való hozzáférése REST APIon keresztül érhető el, és támogatja az egyszeri bejelentkezést. A hitelesítéshez állítsa be a kérések engedélyezési fejlécét egy Azure Active Directory (Azure AD) által beszerzett JSON Web Tokenra.

- [Az Azure cache ismertetése a Redis REST API](/rest/api/redis/)

- [Az egyszeri bejelentkezés ismertetése az Azure AD-vel](../active-directory/manage-apps/what-is-single-sign-on.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3,5: Multi-Factor Authentication használata az összes Azure Active Directory-alapú hozzáféréshez

**Útmutató**: engedélyezze Azure Active Directory (Azure ad) többtényezős hitelesítését, és kövesse Azure Security Center identitás-és hozzáférés-kezelési javaslatait.

- [Többtényezős hitelesítés engedélyezése az Azure-ban](../active-directory/authentication/howto-mfa-getstarted.md)

- [Identitás és hozzáférés figyelése Azure Security Centeron belül](../security-center/security-center-identity-access.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3,6: dedikált gépek (privilegizált hozzáférési munkaállomások) használata az összes felügyeleti feladathoz

**Útmutató**: az Azure-erőforrások bejelentkezéséhez és konfigurálásához konfigurált, többtényezős hitelesítéssel rendelkező emelt szintű hozzáférésű munkaállomások (Paw) használata. 

- [További tudnivalók a privilegizált hozzáférésű munkaállomásokról](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

- [Többtényezős hitelesítés engedélyezése az Azure-ban](../active-directory/authentication/howto-mfa-getstarted.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3,7: naplózás és riasztás a gyanús tevékenységekről a rendszergazdai fiókoktól

**Útmutatás**: Azure Active Directory (Azure AD) PRIVILEGED Identity Management (PIM) használata naplók és riasztások generálásához, ha a környezetben gyanús vagy nem biztonságos tevékenység történik.

Emellett az Azure AD kockázati észleléseit is használhatja a kockázatos felhasználói viselkedéssel kapcsolatos riasztások és jelentések megtekintéséhez.

- [Privileged Identity Management (PIM) üzembe helyezése](../active-directory/privileged-identity-management/pim-deployment-plan.md)

- [Az Azure AD kockázati észlelések ismertetése](../active-directory/identity-protection/overview-identity-protection.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3,8: az Azure-erőforrások kezelése csak jóváhagyott helyekről

**Útmutató**: az Azure Active Directory (Azure ad) feltételes hozzáférésének elnevezett helyeinek konfigurálása, hogy csak az IP-címtartományok vagy országok/régiók egyedi logikai csoportjaihoz férhessenek hozzá.

- [Elnevezett helyszínek konfigurálása az Azure-ban](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="39-use-azure-active-directory"></a>3,9: a Azure Active Directory használata

**Útmutató**: a Azure Active Directory (Azure ad) használata központi hitelesítési és engedélyezési rendszerrel. Az Azure AD az adatok védelme érdekében erős titkosítást használ a nyugalmi és a továbbítási adatokhoz. Az Azure AD emellett a felhasználó hitelesítő adatainak a sók, a kivonatok és a biztonságos tárolását is tartalmazza.

Az Azure AD-hitelesítés nem használható az Azure cache Redis adatsíkon való közvetlen elérésére, azonban az Azure AD hitelesítő adatai a vezérlési sík szintjén (például a Azure Portal) az Azure cache vezérléséhez használhatók az Redis hozzáférési kulcsaihoz.

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="310-regularly-review-and-reconcile-user-access"></a>3,10: a felhasználói hozzáférés rendszeres áttekintése és egyeztetése

**Útmutató**: a Azure Active Directory (Azure ad) olyan naplókat biztosít, amelyek segítenek az elavult fiókok felderítésében. Emellett az Azure Identity Access Reviews használatával hatékonyan kezelheti a csoporttagságok kezelését, a vállalati alkalmazásokhoz való hozzáférést és a szerepkör-hozzárendeléseket. A felhasználói hozzáférés rendszeresen felülvizsgálható, hogy csak a megfelelő felhasználók férhessenek hozzá.

- [Az Azure AD jelentéskészítés ismertetése](../active-directory/reports-monitoring/index.yml)

- [Az Azure Identity hozzáférési felülvizsgálatok használata](../active-directory/governance/access-reviews-overview.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3,11: a figyelő megkísérli a deaktivált hitelesítő adatok elérését

**Útmutató**: hozzáférhet Azure Active Directory (Azure ad) bejelentkezési tevékenységhez, a naplózási és kockázati Eseménynapló-forrásokhoz, amelyek lehetővé teszik az Azure Sentinel vagy egy harmadik féltől származó Siem integrálását.

Ezt a folyamatot leegyszerűsítheti, ha diagnosztikai beállításokat hoz létre az Azure AD felhasználói fiókjaihoz, és elküldi a naplókat és a bejelentkezési naplókat egy Log Analytics munkaterületre. Log Analytics belül is konfigurálhatja a kívánt naplózási riasztásokat.

- [Azure-tevékenységnaplók integrálása az Azure Monitorba](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

- [A fedélzeti Azure Sentinel ismertetése](../sentinel/quickstart-onboard.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3,12: riasztás a fiók bejelentkezési viselkedésének eltérése esetén

**Útmutató**: a fiókok bejelentkezésének viselkedése a vezérlési síkon a Azure Active Directory (Azure ad) Identity Protection és a kockázatkezelési funkciók használatával konfigurálhatja a felhasználói identitásokkal kapcsolatos gyanús műveletekre vonatkozó automatizált válaszokat. További vizsgálat céljából az Azure Sentinelbe is betöltheti az adatmennyiséget.

- [Az Azure AD kockázatos bejelentkezéseinek megtekintése](../active-directory/identity-protection/overview-identity-protection.md)

- [Az Identity Protection kockázati házirendjeinek konfigurálása és engedélyezése](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [Az Azure Sentinel előkészítése](../sentinel/quickstart-onboard.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

## <a name="data-protection"></a>Adatvédelem

*További információ: [Azure Security Benchmark: Adatvédelem](../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4,1: bizalmas információk leltárának fenntartása

**Útmutató**: a címkék használatával segítheti a bizalmas adatokat tároló vagy feldolgozó Azure-erőforrások nyomon követését.

- [Címkék létrehozása és használata](../azure-resource-manager/management/tag-resources.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4,2: bizalmas adatok tárolására vagy feldolgozására szolgáló rendszerek elkülönítése

**Útmutató**: különálló előfizetések és/vagy felügyeleti csoportok megvalósítása fejlesztési, tesztelési és éles környezetekhez. A Redis példányok Azure cache-t virtuális hálózat/alhálózat szerint kell elválasztani. Igény szerint a Redis tűzfal Azure cache szolgáltatásával definiálhat szabályokat úgy, hogy csak a megadott IP-címtartományok kapcsolatai kapcsolódhatnak a gyorsítótárhoz.

- [További Azure-előfizetések létrehozása](../cost-management-billing/manage/create-subscription.md)

- [Management Groups létrehozása](../governance/management-groups/create-management-group-portal.md)

- [Az Azure cache üzembe helyezése a Redis egy vnet](cache-how-to-premium-vnet.md)

- [Az Azure cache konfigurálása Redis tűzfalszabályok esetén](./cache-configure.md#firewall)

- [Címkék létrehozása és használata](../azure-resource-manager/management/tag-resources.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4,3: a bizalmas adatok jogosulatlan átvitelének figyelése és letiltása

**Útmutató**: még nem érhető el; az adatazonosítási, besorolási és veszteség-megelőzési funkciók még nem érhetők el az Azure cache for Redis esetében.

A Microsoft kezeli az Azure cache mögöttes infrastruktúráját a Redis számára, és szigorú ellenőrzéseket vezetett be az ügyféladatok elvesztésének vagy expozíciójának megakadályozására.

- [Az ügyféladatok Azure-beli védelmének ismertetése](../security/fundamentals/protection-customer-data.md)

**Felelősség**: Megosztott

**Azure Security Center figyelés**: nincs

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4,4: minden bizalmas adat titkosítása az átvitel során

**Útmutató**: a Redis-hez készült Azure cache ALAPÉRTELMEZÉS szerint TLS-titkosítású kommunikációt igényel. A TLS 1,0, 1,1 és 1,2 verziók jelenleg támogatottak. Azonban a TLS 1,0 és a 1,1 egy olyan útvonalon van, amely az iparágra kiterjedő elavult, ezért a TLS 1,2-et használja, ha ez egyáltalán lehetséges. Ha az ügyféloldali kódtár vagy eszköz nem támogatja a TLS-t, akkor a titkosítatlan kapcsolatok engedélyezése a Azure Portal vagy a felügyeleti API-kon keresztül végezhető el. Olyan esetekben, ahol a titkosított kapcsolatok nem lehetségesek, ajánlott a gyorsítótár és az ügyfélalkalmazás virtuális hálózatra helyezése.

- [Az Azure cache Redis való továbbításának ismertetése](cache-best-practices.md)

- [A vnet cache-forgatókönyvekben használt szükséges portok ismertetése](./cache-how-to-premium-vnet.md#outbound-port-requirements)

**Felelősség**: Megosztott

**Azure Security Center monitorozás**: az [Azure biztonsági teljesítményteszt](/azure/governance/policy/samples/azure-security-benchmark) a Security Center alapértelmezett házirend-kezdeményezése, és a [Security Center ajánlásainak](/azure/security-center/security-center-recommendations)alapja. A vezérlőhöz kapcsolódó Azure Policy-definíciók Security Center automatikusan engedélyezve vannak. Az ehhez a vezérlőhöz kapcsolódó riasztásokhoz szükség lehet egy [Azure Defender](/azure/security-center/azure-defender) -csomagra a kapcsolódó szolgáltatásokhoz.

**Beépített definíciók Azure Policy – Microsoft. cache**:

[!INCLUDE [Resource Policy for Microsoft.Cache 4.4](../../includes/policy/standards/asb/rp-controls/microsoft.cache-4-4.md)]

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4,5: aktív felderítési eszköz használata a bizalmas adatok azonosítására

**Útmutató**: az adatazonosítási, besorolási és veszteség-megelőzési funkciók még nem érhetők el az Azure cache for Redis esetében. A bizalmas adatokat tartalmazó példányok címkézése, valamint a harmadik féltől származó megoldás implementálása, ha az szükséges a megfelelőség szempontjából.

A Microsoft által felügyelt mögöttes platform esetében a Microsoft az összes vásárlói tartalmat bizalmasként kezeli, és az ügyfelek adatvesztésével és a kitettséggel szembeni védelem érdekében nagy hosszúságú. Annak biztosítása érdekében, hogy az Azure-beli ügyféladatok biztonságban maradjanak, a Microsoft végrehajtotta és karbantartja a robusztus adatvédelmi szabályozást és képességeket.

- [Az ügyféladatok Azure-beli védelmének ismertetése](../security/fundamentals/protection-customer-data.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="46-use-role-based-access-control-to-control-access-to-resources"></a>4,6: szerepköralapú hozzáférés-vezérlés használata az erőforrásokhoz való hozzáférés szabályozásához

**Útmutató**: az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC) használata az Azure cache-hez való hozzáférés vezérléséhez az Redis-vezérlési síkon (például Azure Portal).

- [Az Azure RBAC konfigurálása](../role-based-access-control/role-assignments-portal.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="48-encrypt-sensitive-information-at-rest"></a>4,8: bizalmas adatok titkosítása a nyugalmi állapotban

**Útmutató**: az Azure cache for Redis tárolja az ügyféladatokat a memóriában, és a Microsoft által megvalósított számos vezérlő erősen védi a memóriát, alapértelmezés szerint nincs titkosítva. Ha a szervezete megköveteli, a tartalom titkosítása az Azure cache-ben a Redis-hez való tárolás előtt.

Ha az Azure cache-t használja a "Redis adatmegőrzés" Redis-szolgáltatáshoz, a rendszer az Ön tulajdonában lévő és kezelt Azure Storage-fiókba küldi az adatgyűjtést. Az "új Azure cache for Redis" panel megőrzése a gyorsítótár létrehozásakor és a meglévő prémium gyorsítótárak erőforrás menüjében állítható be.

Az Azure Storage-ban tárolt adatai a 256 bites AES-titkosítással vannak titkosítva és visszafejtve, és az egyik legerősebb blokk titkosítási algoritmus, amely az FIPS 140-2-kompatibilis. Az Azure Storage-titkosítás nem tiltható le. A Microsoft által felügyelt kulcsokat használhatja a Storage-fiók titkosításához, vagy a titkosítást a saját kulcsaival is kezelheti.

- [Az adatmegőrzés konfigurálása az Azure cache-ben a Redis-hez](cache-how-to-premium-persistence.md)

- [Az Azure Storage-fiókok titkosításának megismerése](../storage/common/storage-service-encryption.md)

- [Az Azure Customer adatvédelem ismertetése](../security/fundamentals/protection-customer-data.md)

**Felelősség**: Megosztott

**Azure Security Center figyelés**: nincs

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4,9: a kritikus Azure-erőforrások változásainak naplózása és riasztása

**Útmutató**: a Azure monitor és az Azure-tevékenység naplójának használatával riasztásokat hozhat létre, amelyekkel a Redis és egyéb kritikus vagy kapcsolódó erőforrások esetében az Azure cache éles példányain végezheti el a módosításokat.

- [Riasztások létrehozása az Azure-tevékenységek naplózási eseményeihez](../azure-monitor/alerts/alerts-activity-log.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

## <a name="vulnerability-management"></a>Biztonságirés-kezelés

*További információért lásd az [Azure biztonsági teljesítményteszt: biztonsági rés kezelése](../security/benchmarks/security-control-vulnerability-management.md)című témakört.*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5,1: automatikus biztonsági rések vizsgálatára szolgáló eszközök futtatása

**Útmutató**: Azure Security Center ajánlásainak követése az Azure cache Redis-példányokhoz és kapcsolódó erőforrásokhoz való biztonságossá tételéhez.

A Microsoft a Redis-hez készült Azure cache-t támogató mögöttes rendszereken a biztonsági rések felügyeletét végzi.

- [Azure Security Center javaslatok ismertetése](../security-center/recommendations-reference.md)

**Felelősség**: Megosztott

**Azure Security Center figyelés**: nincs

## <a name="inventory-and-asset-management"></a>Leltár-és eszközfelügyelet

*További információt az [Azure biztonsági teljesítményteszt: leltár és eszközkezelés](../security/benchmarks/security-control-inventory-asset-management.md)című témakörben talál.*

### <a name="61-use-automated-asset-discovery-solution"></a>6,1: automatikus eszköz-felderítési megoldás használata

**Útmutató**: az Azure Resource Graph használatával lekérdezheti vagy felderítheti az összes erőforrást (például számítási, tárolási, hálózati, portok és protokollok stb.) az előfizetés (ok) n belül. Győződjön meg arról, hogy a bérlőben a megfelelő (olvasási) engedélyek szerepelnek, valamint az összes Azure-előfizetés, valamint az előfizetésekben lévő erőforrások számbavétele.

Bár a klasszikus Azure-erőforrások felderítése az erőforrás-gráfon keresztül lehetséges, erősen ajánlott a Azure Resource Manager erőforrások létrehozása és használata.

- [Lekérdezések létrehozása az Azure Resource Graph használatával](../governance/resource-graph/first-query-portal.md)

- [Azure-előfizetések megtekintése](/powershell/module/az.accounts/get-azsubscription?preserve-view=true&view=azps-4.8.0)

- [Az Azure RBAC ismertetése](../role-based-access-control/overview.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="62-maintain-asset-metadata"></a>6,2: az eszköz metaadatainak fenntartása

**Útmutató**: címkéket alkalmazhat az Azure-erőforrásokra, így a metaadatok logikailag rendszerezve lesznek a besorolásban.

- [Címkék létrehozása és használata](../azure-resource-manager/management/tag-resources.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="63-delete-unauthorized-azure-resources"></a>6,3: jogosulatlan Azure-erőforrások törlése

**Útmutató**: címkézés, felügyeleti csoportok és külön előfizetések használata, ahol szükséges, az Azure cache rendszerezése és nyomon követése Redis-példányokhoz és kapcsolódó erőforrásokhoz. Rendszeres időközönként egyeztetheti a leltárt, és gondoskodhat arról, hogy a jogosulatlan erőforrások törlése az előfizetésből időben történjen.

Emellett a Azure Policy használatával korlátozásokat állíthat be az ügyfél-előfizetésekben létrehozható erőforrások típusára a következő beépített szabályzat-definíciók használatával:

- Nem engedélyezett erőforrástípusok

- Engedélyezett erőforrástípusok

További információkat az alábbi hivatkozásokon találhat:

- [További Azure-előfizetések létrehozása](../cost-management-billing/manage/create-subscription.md)

- [Felügyeleti csoportok létrehozása](../governance/management-groups/create-management-group-portal.md)

- [Erőforrás-Címkék létrehozása és használata](../azure-resource-manager/management/tag-resources.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="65-monitor-for-unapproved-azure-resources"></a>6,5: a nem jóváhagyott Azure-erőforrások figyelése

**Útmutató**: a Azure Policy használatával korlátozásokat állíthat be az ügyfél-előfizetésekben létrehozható erőforrások típusára a következő beépített szabályzat-definíciók használatával:

- Nem engedélyezett erőforrástípusok
- Engedélyezett erőforrástípusok

Emellett az Azure Resource Graph használatával kérdezheti le és derítheti fel az előfizetéseken belüli erőforrásokat.

- [Az Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md)

- [Lekérdezések létrehozása az Azure Graph használatával](../governance/resource-graph/first-query-portal.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="69-use-only-approved-azure-services"></a>6,9: csak jóváhagyott Azure-szolgáltatások használata

**Útmutató**: a Azure Policy használatával korlátozásokat állíthat be az ügyfél-előfizetésekben létrehozható erőforrások típusára a következő beépített szabályzat-definíciók használatával:

- Nem engedélyezett erőforrástípusok

- Engedélyezett erőforrástípusok

További információkat az alábbi hivatkozásokon találhat:

- [Az Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md)

- [Adott erőforrástípus megtagadása a következővel Azure Policy](../governance/policy/samples/built-in-policies.md#general)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6,11: korlátozza a felhasználók képességét a Azure Resource Manager való interakcióra

**Útmutató**: az Azure feltételes hozzáférésének konfigurálása a felhasználók "Microsoft Azure felügyelet" alkalmazáshoz való hozzáférésének tiltása a Azure Resource Manager való interakcióra.

- [A feltételes hozzáférés konfigurálása a Azure Resource Managerhoz való hozzáférés blokkolásához](../role-based-access-control/conditional-access-azure-management.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

## <a name="secure-configuration"></a>Biztonságos konfiguráció

*További információt az [Azure biztonsági teljesítményteszt: biztonságos konfiguráció](../security/benchmarks/security-control-secure-configuration.md)című témakörben talál.*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7,1: biztonságos konfigurációk létrehozása az összes Azure-erőforráshoz

**Útmutató**: az Azure cache szabványos biztonsági konfigurációinak definiálása és implementálása a Redis-példányok Azure Policy használatával. Használjon Azure Policy aliasokat a "Microsoft. cache" névtérben egyéni szabályzatok létrehozásához az Azure cache Redis-példányok konfigurációjának naplózásához vagy érvényesítéséhez. Az Azure cache Redis-példányokhoz kapcsolódó beépített szabályzat-definíciókat is igénybe vehet, például:

- Csak a Redis Cache biztonságos kapcsolatai legyenek engedélyezve

További információkat az alábbi hivatkozásokon találhat:

- [Az elérhető Azure Policy aliasok megtekintése](/powershell/module/az.resources/get-azpolicyalias?preserve-view=true&view=azps-4.8.0)

- [Az Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="73-maintain-secure-azure-resource-configurations"></a>7,3: biztonságos Azure-erőforrás-konfigurációk karbantartása

**Útmutató**: az Azure-erőforrások biztonságos beállításainak betartatásához használja a Azure Policy [deny] és a [telepítés ha nem létezik] lehetőséget.

- [Az Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md)

- [Azure Policy effektusok ismertetése](../governance/policy/concepts/effects.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="75-securely-store-configuration-of-azure-resources"></a>7,5: az Azure-erőforrások biztonságos tárolása

**Útmutató**: Ha a Redis-példányok és a kapcsolódó erőforrások esetében egyéni Azure Policy definíciókat vagy Azure Resource Manager sablonokat használ az Azure cache-hez, az Azure Repos használatával biztonságosan tárolhatja és kezelheti a kódot.

- [Kód tárolása az Azure DevOps](/azure/devops/repos/git/gitworkflow?preserve-view=true&view=azure-devops)

- [Az Azure Repos dokumentációja](/azure/devops/repos/?preserve-view=true&view=azure-devops)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7,7: az Azure-erőforrások konfigurációs felügyeleti eszközeinek üzembe helyezése

**Útmutató**: Azure Policy aliasok használata a "Microsoft. cache" névtérben egyéni szabályzatok létrehozásához a riasztáshoz, a naplózáshoz és a rendszer-konfigurációk kényszerítéséhez. Emellett dolgozzon ki egy folyamatot és egy folyamatot a házirend-kivételek kezeléséhez.

- [Az Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7,9: automatikus konfigurációs monitorozás megvalósítása Azure-erőforrásokhoz

**Útmutató**: Azure Policy aliasok használata a "Microsoft. cache" névtérben egyéni szabályzatok létrehozásához a riasztáshoz, a naplózáshoz és a rendszer-konfigurációk kényszerítéséhez. A Redis-példányok és a kapcsolódó erőforrások esetében az Azure cache konfigurációinak automatikus érvényesítéséhez használja a Azure Policy [audit], [megtagadás] és [telepítés ha nem létezik] beállítást.

- [Az Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="711-manage-azure-secrets-securely"></a>7,11: az Azure-titkok biztonságos kezelése

**Útmutató**: az Azure-beli virtuális gépek vagy a Azure app Serviceon futó webalkalmazások számára a Redis-példányok Azure cache-hez való hozzáféréséhez használja a Managed Service Identityt a Azure Key Vault-mel, hogy egyszerűbbé és biztonságossá tegye az Azure cache-t a Redis titkos felügyeletéhez. Győződjön meg arról, Key Vault a Soft delete engedélyezve van.

- [Integráció az Azure felügyelt identitásokkal](../azure-app-configuration/howto-integrate-azure-managed-service-identity.md)

- [Key Vault létrehozása](../key-vault/general/quick-create-portal.md)

- [Hitelesítés Key Vault](../key-vault/general/assign-access-policy-portal.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="712-manage-identities-securely-and-automatically"></a>7,12: az identitások biztonságos és automatikus kezelése

**Útmutató**: az Azure-beli virtuális gépek vagy a Azure app Serviceon futó webalkalmazások számára a Redis-példányok Azure cache-hez való hozzáféréséhez használja a Managed Service Identityt a Azure Key Vault-mel, hogy egyszerűbbé és biztonságossá tegye az Azure cache-t a Redis titkos felügyeletéhez. Győződjön meg arról, Key Vault a Soft delete engedélyezve van.

Felügyelt identitások használatával biztosíthatja az Azure-szolgáltatások automatikus felügyelt identitását Azure Active Directoryban (Azure AD). A felügyelt identitások lehetővé teszik bármely olyan szolgáltatás hitelesítését, amely támogatja az Azure AD-hitelesítést, beleértve a Azure Key Vault is, a kódban szereplő hitelesítő adatok nélkül.

- [Felügyelt identitások konfigurálása](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)

- [Integráció az Azure felügyelt identitásokkal](../azure-app-configuration/howto-integrate-azure-managed-service-identity.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="713-eliminate-unintended-credential-exposure"></a>7,13: a hitelesítő adatok nem szándékolt expozíciójának megszüntetése

**Útmutató**: hitelesítő adatok beolvasása a programkódon belül a hitelesítő adatok azonosításához. A Credential Scanner a felfedezett hitelesítő adatok biztonságosabb helyre, például az Azure Key Vaultba való áthelyezésére is javaslatot tesz.

- [A hitelesítő adatok beolvasójának beállítása](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

## <a name="malware-defense"></a>Kártevők elleni védelem

*További információt az [Azure biztonsági teljesítményteszt: kártevők elleni védelem](../security/benchmarks/security-control-malware-defense.md)című témakörben talál.*

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8,2: a nem számítási Azure-erőforrásokra feltöltött fájlok előzetes vizsgálata

**Útmutató**: a Microsoft kártevő szoftverrel való ellátása engedélyezve van az Azure-szolgáltatásokat támogató mögöttes gazdagépen (például az Azure cache for Redis esetében), de nem fut az ügyfél tartalmán.

A nem számítási Azure-erőforrásokra feltöltött tartalom előzetes vizsgálata, például App Service, Data Lake Storage, Blob Storage, Azure Database for PostgreSQL stb. A Microsoft nem fér hozzá az adataihoz ezekben a példányokban.

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

## <a name="data-recovery"></a>Adat-helyreállítás

*További információt az [Azure biztonsági teljesítményteszt: adat-helyreállítás](../security/benchmarks/security-control-data-recovery.md)című témakörben talál.*

### <a name="91-ensure-regular-automated-back-ups"></a>9,1: rendszeres automatizált biztonsági másolatok biztosítása

**Útmutató**: a Redis megőrzésének engedélyezése. A Redis megőrzése lehetővé teszi a Redis-ben tárolt adatmegőrzést. Pillanatképeket és biztonsági mentést is készíthet, amelyeket hardverhiba esetén betölthet. Ez óriási előnyt jelent az alapszintű és a standard szint esetében, ahol az összes adat a memóriában tárolódik, és előfordulhat, hogy a gyorsítótár-csomópontok leállításakor hiba történt.

A Redis exportálásához használhatja az Azure cache-t is. Az Exportálás lehetővé teszi az Azure cache-ben tárolt adatexportálást a Redis-Redis kompatibilis RDB-fájl (ok) hoz. Ezzel a szolgáltatással áthelyezheti az adatok egyik Azure-gyorsítótárból a Redis-példányról egy másikra vagy egy másik Redis-kiszolgálóra. Az exportálási folyamat során létrejön egy ideiglenes fájl az Azure cache-t futtató virtuális gépen a Redis Server-példányhoz, és a fájl fel lesz töltve a kijelölt Storage-fiókba. Ha az exportálási művelet sikeres vagy sikertelen állapottal fejeződött be, a rendszer törli az ideiglenes fájlt.

- [A Redis megőrzésének engedélyezése](cache-how-to-premium-persistence.md)

- [Az Azure cache használata a Redis exportálásához](cache-how-to-import-export-data.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: teljes rendszerbiztonsági mentés és minden ügyfél által felügyelt kulcs biztonsági mentése

**Útmutató**: a Redis megőrzésének engedélyezése. A Redis megőrzése lehetővé teszi a Redis-ben tárolt adatmegőrzést. Pillanatképeket és biztonsági mentést is készíthet, amelyeket hardverhiba esetén betölthet. Ez óriási előnyt jelent az alapszintű és a standard szint esetében, ahol az összes adat a memóriában tárolódik, és előfordulhat, hogy a gyorsítótár-csomópontok leállításakor hiba történt.

A Redis exportálásához használhatja az Azure cache-t is. Az Exportálás lehetővé teszi az Azure cache-ben tárolt adatexportálást a Redis-Redis kompatibilis RDB-fájl (ok) hoz. Ezzel a szolgáltatással áthelyezheti az adatok egyik Azure-gyorsítótárból a Redis-példányról egy másikra vagy egy másik Redis-kiszolgálóra. Az exportálási folyamat során létrejön egy ideiglenes fájl az Azure cache-t futtató virtuális gépen a Redis Server-példányhoz, és a fájl fel lesz töltve a kijelölt Storage-fiókba. Ha az exportálási művelet sikeres vagy sikertelen állapottal fejeződött be, a rendszer törli az ideiglenes fájlt.

Ha a Redis-példányokhoz tartozó Azure gyorsítótárhoz tartozó hitelesítő adatok tárolására Azure Key Vault használ, ügyeljen arra, hogy a kulcsok rendszeres automatizált biztonsági mentéseit tárolja.

- [A Redis megőrzésének engedélyezése](cache-how-to-premium-persistence.md)

- [Az Azure cache használata a Redis exportálásához](cache-how-to-import-export-data.md)

- [Key Vault kulcsok biztonsági mentése](/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: az összes biztonsági másolat ellenőrzése, beleértve az ügyfél által felügyelt kulcsokat

**Útmutató**: az Azure cache használata a Redis importálásához. Az importálással bármilyen felhőben vagy környezetben futó Redis-kiszolgálóról készíthet Redis-kompatibilis RDB-fájlokat, beleértve a Linuxon, a Windowson vagy bármely olyan felhőalapú szolgáltatón futó Redis, mint például a Amazon Web Services és mások. Az adatok importálása egyszerű módszer a gyorsítótár előre feltöltött adatokkal való létrehozására. Az importálási folyamat során az Azure cache for Redis betölti a RDB-fájlokat az Azure Storage-ból a memóriába, majd beszúrja a kulcsokat a gyorsítótárba.

Rendszeresen tesztelje a Azure Key Vault titkos kódok adatvisszaállítását.

- [Az Azure cache használata a Redis-importáláshoz](cache-how-to-import-export-data.md)

- [Key Vault titkos kódok visszaállítása](/powershell/module/az.keyvault/restore-azkeyvaultsecret?preserve-view=true&view=azps-4.8.0)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

## <a name="incident-response"></a>Incidensmegoldás

*További információ: [Azure Security Benchmark: Incidensek kezelése](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10,1: incidens-válaszi útmutató létrehozása

**Útmutató**: Alakítson ki incidenskezelési útmutatót a vállalat számára. Gondoskodjon írásos incidenskezelési tervekről, amelyek definiálják az összes résztvevő szerepkörét, valamint az incidenskezelés fázisait az észleléstől az incidens utáni értékelésig.

- [Munkafolyamat-automatizálás konfigurálása Azure Security Centeron belül](../security-center/security-center-planning-and-operations-guide.md)

- [Útmutató a saját biztonsági incidensek megoldási folyamatának létrehozásához](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Microsoft Security Response Center – incidens anatómiája](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Az ügyfél a NIST számítógépes biztonsági incidensek kezelési útmutatóját is kihasználhatja a saját incidens-válasz tervének létrehozásával kapcsolatos támogatáshoz](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10,2: incidensek pontozásának és rangsorolási eljárásának létrehozása

**Útmutató**: a Security Center súlyosságot rendel az egyes riasztásokhoz, hogy a prioritások alapján ki lehessen deríteni, hogy mely riasztásokat kell először megvizsgálni. A súlyosság azon alapul, hogy az Security Center milyen mértékben szerepel a riasztás kibocsátásához használt elemzésben vagy elemzésben, valamint a riasztást eredményező tevékenység mögötti rosszindulatú szándékkal.

Emellett egyértelműen megjelölheti az előfizetéseket (pl.: éles környezetben nem termelt), és hozzon létre egy elnevezési rendszert az Azure-erőforrások egyértelmű azonosításához és kategorizálásához.

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="103-test-security-response-procedures"></a>10,3: biztonsági reagálási eljárások tesztelése

**Útmutató**: a rendszerek incidensek reagálási képességeinek rendszeres tesztelésére szolgáló gyakorlatok végrehajtása. Azonosítsa a gyenge pontokat és réseket, és szükség esetén dolgozza át a tervet.

- [Tekintse meg a NIST kiadványát: útmutató az IT-csomagok és-képességek teszteléséhez, betanításához és edzésprogramjának kidolgozásához](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10,4: biztonsági incidensek elérhetőségének biztosítása és riasztási értesítések konfigurálása biztonsági incidensekhez

**Útmutató**: a Microsoft a biztonsági incidensek elérhetőségi adatait arra használja fel, hogy felvegye Önnel a kapcsolatot, ha a Microsoft Security Response Center (MSRC) felfedi, hogy az ügyfél adatait egy törvénytelen vagy jogosulatlan fél is hozzáférte.  A problémák megoldása érdekében tekintse át az incidenseket a tény után.

- [A Azure Security Center biztonsági kapcsolattartó beállítása](../security-center/security-center-provide-security-contact-details.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10,5: biztonsági riasztások beépítése az incidensek gyorsreagáló rendszerébe

**Útmutató**: az Azure Security Center-riasztások és-javaslatok exportálása a folyamatos exportálás funkció használatával. A folyamatos exportálás lehetővé teszi a riasztások és javaslatok manuális és folyamatos exportálását. Az Azure Security Center adatösszekötővel továbbíthatja a riasztásokat az Azure Sentinel szolgáltatásba.

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

- [Az Azure Security Benchmark v2 áttekintésének](../security/benchmarks/overview.md) megtekintése
- További tudnivalók az [Azure biztonsági alapterveiről](../security/benchmarks/security-baselines-overview.md)
