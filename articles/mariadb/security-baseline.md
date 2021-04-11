---
title: Azure Database for MariaDB Azure biztonsági alapterve
description: A Azure Database for MariaDB biztonsági alapterve az Azure biztonsági Teljesítménytesztben meghatározott biztonsági javaslatok megvalósítására szolgáló eljárási útmutatást és erőforrásokat biztosít.
author: msmbaldwin
ms.service: mariadb
ms.topic: conceptual
ms.date: 03/29/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 189fb95f9c4be4ddf9d75a8dc26a7bf403caaadd
ms.sourcegitcommit: f5448fe5b24c67e24aea769e1ab438a465dfe037
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105967702"
---
# <a name="azure-security-baseline-for-azure-database-for-mariadb"></a>Azure Database for MariaDB Azure biztonsági alapterve

Ez a biztonsági alapkonfiguráció az [Azure biztonsági teljesítményteszt 1,0-es verziójáról](../security/benchmarks/overview-v1.md) Azure Database for MariaDBre vonatkozó útmutatást alkalmaz. Az Azure Security Benchmark ajánlásokat ad arra nézve, hogy hogyan tehetők biztonságossá a felhőalapú megoldások az Azure-ban. A tartalom az Azure biztonsági teljesítményteszt által meghatározott **biztonsági vezérlők** szerint van csoportosítva, valamint a Azure Database for MariaDB vonatkozó kapcsolódó útmutatás. Az Azure Database for MariaDBre nem alkalmazható **vezérlők** , vagy amelyek esetében a felelősség a Microsoft által lett kizárva.

Ha szeretné megtekinteni, hogyan Azure Database for MariaDB teljes mértékben leképezni az Azure biztonsági Teljesítménytesztét, tekintse meg a [teljes Azure Database for MariaDB biztonsági alapterv-leképezési fájlt](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="network-security"></a>Hálózati biztonság

*További információ: [Azure Security Benchmark: Hálózati biztonság](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1,1: Azure-erőforrások biztosítása virtuális hálózatokon belül

**Útmutató**: privát hivatkozás konfigurálása Azure Database for MariaDB magánhálózati végpontokkal. A privát kapcsolat lehetővé teszi, hogy privát végponton keresztül csatlakozzon az Azure különböző PaaS-szolgáltatásaihoz. Az Azure Private Link lényegében elérhetővé teszi az Azure-szolgáltatásokat a virtuális magánhálózaton belül. A virtuális hálózat és a MariaDB-példány közötti forgalom a Microsoft gerinc hálózatán halad át.

Azt is megteheti, hogy Virtual Network szolgáltatás-végpontokat használ a Azure Database for MariaDB-implementációk hálózati hozzáférésének a megóvására és korlátozására. A virtuális hálózati szabályok egy tűzfal biztonsági funkciója, amely azt szabályozza, hogy a Azure Database for MariaDB fogadjon-e olyan kommunikációt, amelyet a virtuális hálózatok egyes alhálózatai továbbítanak.

A Azure Database for MariaDB is biztonságossá teheti a tűzfalszabályok használatával. A kiszolgáló tűzfala megakadályozza az adatbázis-kiszolgáló elérését, amíg meg nem adja, hogy mely számítógépek rendelkeznek engedéllyel. A tűzfal konfigurálásakor olyan tűzfalszabályokat adhat meg, amelyek meghatározzák az elfogadható IP-címtartományokat. A tűzfalszabályok a kiszolgáló szintjén hozhatók létre.

- [Privát hivatkozás konfigurálása Azure Database for MariaDBhoz](howto-configure-privatelink-portal.md)

- [VNet-szolgáltatási végpontok és VNet-szabályok létrehozása és kezelése Azure Database for MariaDB-kiszolgálón](howto-manage-vnet-portal.md)

- [Azure Database for MariaDB tűzfalszabályok konfigurálása](concepts-firewall-rules.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás**: az [Azure biztonsági teljesítményteszt](/azure/governance/policy/samples/azure-security-benchmark) a Security Center alapértelmezett házirend-kezdeményezése, és a [Security Center ajánlásainak](/azure/security-center/security-center-recommendations)alapja. A vezérlőhöz kapcsolódó Azure Policy-definíciók Security Center automatikusan engedélyezve vannak. Az ehhez a vezérlőhöz kapcsolódó riasztásokhoz szükség lehet egy [Azure Defender](/azure/security-center/azure-defender) -csomagra a kapcsolódó szolgáltatásokhoz.

**Azure Policy beépített definíciók – Microsoft. DBforMariaDB**:

[!INCLUDE [Resource Policy for Microsoft.DBforMariaDB 1.1](../../includes/policy/standards/asb/rp-controls/microsoft.dbformariadb-1-1.md)]

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-network-interfaces"></a>1,2: a virtuális hálózatok, alhálózatok és hálózati adapterek konfigurációjának és forgalmának figyelése és naplózása

**Útmutató**: ha a Azure Database for MariaDB-kiszolgáló titkos végponthoz van csatlakoztatva, a virtuális gépeket ugyanabban a virtuális hálózatban helyezheti üzembe. Hálózati biztonsági csoport (NSG) használatával csökkentheti az adatkiszűrése kockázatát. Engedélyezze a NSG folyamat naplóit, és küldje el a naplókat egy Storage-fiókba a forgalom naplózása érdekében. NSG-naplókat is küldhet egy Log Analytics munkaterületre, és a Traffic Analytics használatával betekintést nyerhet az Azure-Felhőbeli forgalomba. A Traffic Analytics egyes előnyei lehetővé teszi a hálózati tevékenységek megjelenítését és a gyakori pontok azonosítását, a biztonsági fenyegetések azonosítását, a forgalomban rejlő minták értelmezését, valamint a hálózati helytelen konfigurációk meghatározását.

- [Privát hivatkozás konfigurálása Azure Database for MariaDBhoz](howto-configure-privatelink-portal.md)

- [A NSG folyamat naplófájljainak engedélyezése](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [A Traffic Analytics engedélyezése és használata](../network-watcher/traffic-analytics.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1,4: az ismert kártevő IP-címekkel folytatott kommunikáció megtagadása

**Útmutató**: az Azure Database for MariaDB komplex veszélyforrások elleni védelme. A komplex veszélyforrások elleni védelem olyan rendellenes tevékenységeket észlel, amelyek szokatlan és potenciálisan ártalmas kísérleteket jeleznek az adatbázisok eléréséhez vagy kiaknázásához.

A Azure Database for MariaDB-példányokhoz társított virtuális hálózatokon DDoS Protection szabványt engedélyezheti a DDoS-támadások elleni védelemhez. A Azure Security Center integrált fenyegetési intelligencia használatával megtagadhatja a kommunikációt az ismert kártékony vagy nem használt internetes IP-címekkel.

- [Összetett veszélyforrások elleni védelem konfigurálása Azure Database for MariaDB](howto-database-threat-protection-portal.md)

- [A DDoS Protection konfigurálása](/azure/virtual-network/manage-ddos-protection)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="15-record-network-packets"></a>1,5: hálózati csomagok rögzítése

**Útmutató**: ha a Azure Database for MariaDB-kiszolgáló titkos végponthoz van csatlakoztatva, a virtuális gépeket ugyanabban a virtuális hálózatban helyezheti üzembe. Ezután konfigurálhat egy hálózati biztonsági csoportot (NSG) az adatkiszűrése kockázatának csökkentése érdekében. Engedélyezze a NSG folyamat naplóit, és küldje el a naplókat egy Storage-fiókba a forgalom naplózása érdekében. NSG-naplókat is küldhet egy Log Analytics munkaterületre, és a Traffic Analytics használatával betekintést nyerhet az Azure-Felhőbeli forgalomba. A Traffic Analytics egyes előnyei lehetővé teszi a hálózati tevékenységek megjelenítését és a gyakori pontok azonosítását, a biztonsági fenyegetések azonosítását, a forgalomban rejlő minták értelmezését, valamint a hálózati helytelen konfigurációk meghatározását.

- [A NSG folyamat naplófájljainak engedélyezése](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [A Traffic Analytics engedélyezése és használata](../network-watcher/traffic-analytics.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1,6: hálózati behatolás-észlelési/Behatolás-megelőzési rendszerek (AZONOSÍTÓk/IP-címek) üzembe helyezése

**Útmutató**: az Azure Database for MariaDB komplex veszélyforrások elleni védelme. A komplex veszélyforrások elleni védelem olyan rendellenes tevékenységeket észlel, amelyek szokatlan és potenciálisan ártalmas kísérleteket jeleznek az adatbázisok eléréséhez vagy kiaknázásához.

- [Összetett veszélyforrások elleni védelem konfigurálása Azure Database for MariaDB](howto-database-threat-protection-portal.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1,8: a hálózati biztonsági szabályok bonyolultságának és adminisztratív terhelésének csökkentése

**Útmutató**: a Azure Database for MariaDB példányokhoz hozzáférést igénylő erőforrásokhoz használjon virtuális hálózati szolgáltatás címkéit a hálózati biztonsági csoportokon vagy Azure Firewall a hálózati hozzáférés-vezérlés definiálásához. Biztonsági szabályok létrehozása során szolgáltatáscímkéket használhat bizonyos IP-címek helyett. A szolgáltatási címke nevének (például: SQL) megadásával. WestUs) a szabály megfelelő forrás vagy cél mezőjében engedélyezheti vagy megtagadhatja a megfelelő szolgáltatás forgalmát. A Microsoft kezeli a szolgáltatási címke által felölelt címek előtagjait, és automatikusan frissíti a szolgáltatási címkét a címek változásával.

Azure Database for MariaDB a "Microsoft. SQL" szolgáltatás címkéjét használja.

- [További információ a szolgáltatási címkék használatáról](../virtual-network/service-tags-overview.md)

- [A Azure Database for MariaDB szolgáltatás használati felcímkézésének ismertetése](https://docs.microsoft.com/azure/mariadb/concepts-data-access-security-vnet#terminology-and-description)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1,9: a hálózati eszközök szabványos biztonsági konfigurációinak fenntartása

**Útmutató**: szabványos biztonsági konfigurációk definiálása és implementálása a Azure Database for MariaDB-példányokhoz társított hálózati beállításokhoz és hálózati erőforrásokhoz Azure Policy. Használjon Azure Policy aliasokat a "Microsoft. DBforMariaDB" és a "Microsoft. Network" névterekben, hogy egyéni szabályzatokat hozzon létre a Azure Database for MariaDB példányok hálózati konfigurációjának naplózásához vagy érvénybe léptetéséhez. A hálózatkezeléssel vagy a Azure Database for MariaDB-példányokkal kapcsolatos beépített szabályzat-definíciókat is igénybe vehet, például:

- DDoS Protection a standardot engedélyezni kell

- A privát végpontot engedélyezni kell a MariaDB-kiszolgálókon

- A MariaDB-kiszolgálónak virtuális hálózati szolgáltatási végpontot kell használnia

Dokumentáció:

- [Az Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md)

- [Azure Policy minták a hálózatkezeléshez](../governance/policy/samples/built-in-policies.md)

- [Azure Blueprint létrehozása](../governance/blueprints/create-blueprint-portal.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="110-document-traffic-configuration-rules"></a>1,10: a dokumentum forgalmának konfigurációs szabályai

**Útmutató**: a MariaDB-példányok hálózati biztonságával és forgalmával kapcsolatos erőforrásokhoz használható címkék használatával metaadatokat és logikai szervezeteket biztosíthat.

A címkézéshez kapcsolódó beépített Azure Policy definíciók bármelyikét használhatja, például a "címke és az érték megkövetelése" beállítást, hogy az összes erőforrás címkével legyen létrehozva, és értesítse a meglévő címkézetlen erőforrásokról.

A Azure PowerShell vagy az Azure CLI használatával a címkék alapján kereshet vagy végezhet műveleteket az erőforrásokon.

- [Címkék létrehozása és használata](/azure/azure-resource-manager/resource-group-using-tags)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1,11: automatikus eszközök használata a hálózati erőforrások konfigurációjának figyelésére és a változások észlelésére

**Útmutató**: az Azure-tevékenység naplójának használata a hálózati erőforrás-konfigurációk figyelésére és a Azure Database for MariaDB-példányokhoz kapcsolódó hálózati erőforrások változásainak észlelésére. Hozzon létre riasztásokat Azure Monitoron belül, amelyek akkor lépnek életbe, amikor a kritikus hálózati erőforrásokra vonatkozó módosításokat végrehajtják

- [Azure-Tevékenységnaplók eseményeinek megtekintése és lekérése](/azure/azure-monitor/platform/activity-log-view)

- [Riasztások létrehozása a Azure Monitorban](/azure/azure-monitor/platform/alerts-activity-log)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

## <a name="logging-and-monitoring"></a>Naplózás és monitorozás

*További információt az [Azure biztonsági teljesítményteszt: naplózás és figyelés](../security/benchmarks/security-control-logging-monitoring.md)című témakörben talál.*

### <a name="22-configure-central-security-log-management"></a>2,2: a központi biztonsági naplók felügyeletének konfigurálása

**Útmutató**: a diagnosztikai beállítások és a kiszolgálói naplók engedélyezése, valamint a naplók beolvasása a Azure Database for MariaDB példányai által generált biztonsági adatokat összesítve. A Azure Monitoron belül Log Analytics munkaterület (ek) használatával kérdezheti le és végezheti el az elemzéseket, és használhatja az Azure Storage-fiókokat a hosszú távú/archiválási tároláshoz. Alternatív megoldásként engedélyezheti és elvégezheti az Azure Sentinel vagy egy harmadik féltől származó SIEM-et.

- [A Azure Database for MariaDB kiszolgáló naplófájljainak konfigurálása és elérése](concepts-server-logs.md)

- [Naplózási naplók konfigurálása és elérése Azure Database for MariaDB](howto-configure-audit-logs-portal.md)

- [Az Azure Sentinel előkészítése](../sentinel/quickstart-onboard.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="23-enable-audit-logging-for-azure-resources"></a>2,3: az Azure-erőforrások naplózásának engedélyezése

**Útmutató**: a diagnosztikai beállítások engedélyezése a Azure Database for MariaDB példányokon a naplózási, biztonsági és diagnosztikai naplókhoz való hozzáféréshez. Győződjön meg arról, hogy kifejezetten engedélyezi a MariaDB-naplót. A automatikusan elérhető tevékenység-naplók közé tartozik az eseményforrás, a dátum, a felhasználó, az időbélyeg, a forráscím, a célcím és más hasznos elemek. Engedélyezheti az Azure-műveletnapló diagnosztikai beállításait is, és elküldheti a naplókat ugyanarra a Log Analytics munkaterületre vagy Storage-fiókba.

- [A Azure Database for MariaDB kiszolgáló naplófájljainak konfigurálása és elérése](concepts-server-logs.md)

- [Naplózási naplók konfigurálása és elérése Azure Database for MariaDB](howto-configure-audit-logs-portal.md)

- [Az Azure-tevékenység naplójának diagnosztikai beállításainak konfigurálása](/azure/azure-monitor/platform/diagnostic-settings-legacy)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="25-configure-security-log-storage-retention"></a>2,5: a biztonsági napló tárolási adatmegőrzésének konfigurálása

**Útmutató**: a Azure monitoron belül a Azure Database for MariaDB-naplók tárolására szolgáló log Analytics munkaterülethez a szervezet megfelelőségi szabályainak megfelelően állítsa be a megőrzési időszakot. Használja az Azure Storage-fiókokat hosszú távú/archiválási tároláshoz.

- [Log Analytics-munkaterületek naplózási megőrzési paramétereinek beállítása](/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period)

- [Erőforrás-naplók tárolása Azure Storage-fiókban](/azure/azure-monitor/platform/resource-logs#send-to-azure-storage)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="26-monitor-and-review-logs"></a>2,6: naplók figyelése és áttekintése

**Útmutató**: az MariaDB-példányok naplóinak elemzése és figyelése rendellenes működéshez. Az Azure Monitor Log Analytics munkaterülete segítségével tekintheti át a naplókat, és lekérdezéseket végezhet a naplózási adatokon. Alternatív megoldásként engedélyezheti és elvégezheti az Azure Sentinel vagy egy harmadik fél SIEM-nek.

- [Az Azure Sentinel előkészítése](../sentinel/quickstart-onboard.md)

- [További információ a Log Analytics munkaterületről](/azure/azure-monitor/log-query/get-started-portal)

- [Egyéni lekérdezések végrehajtása a Azure Monitorban](/azure/azure-monitor/log-query/get-started-queries)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="27-enable-alerts-for-anomalous-activities"></a>2,7: riasztások engedélyezése rendellenes tevékenységekhez

**Útmutató**: a MariaDB komplex veszélyforrások elleni védelemének engedélyezése. A Azure Database for MariaDB komplex veszélyforrások elleni védelme olyan rendellenes tevékenységeket észlel, amelyek szokatlan és potenciálisan ártalmas kísérleteket jeleznek az adatbázisok eléréséhez vagy kiaknázásához.

Emellett engedélyezheti a kiszolgálói naplókat és a diagnosztikai beállításokat a MariaDB, és naplókat küldhet egy Log Analytics munkaterületre. A Log Analytics-munkaterületet az Azure Sentinelbe irányíthatja, mivel ez egy biztonsági előkészítési automatizált választ (felszárnyaló) megoldást biztosít. Ez lehetővé teszi a forgatókönyvek (automatizált megoldások) létrehozását és a biztonsági problémák megoldására való felhasználását.

- [Komplex veszélyforrások elleni védelem engedélyezése a MariaDB](howto-database-threat-protection-portal.md)

- [A kiszolgálók naplófájljainak konfigurálása és elérése a MariDB](concepts-server-logs.md)

- [A MariaDB naplózási naplóinak konfigurálása és elérése](howto-configure-audit-logs-portal.md)

- [Az Azure Sentinel előkészítése](../sentinel/quickstart-onboard.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

## <a name="identity-and-access-control"></a>Identitás- és hozzáférés-vezérlés

*További információt az [Azure biztonsági teljesítményteszt: Identity and Access Control](../security/benchmarks/security-control-identity-access-control.md)című témakörben talál.*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3,1: a felügyeleti fiókok leltárának karbantartása

**Útmutató**: a MariaDB-példányok felügyeleti síkjával (Azure Portal/Azure Resource Manager) rendszergazdai hozzáféréssel rendelkező felhasználói fiókok leltárának fenntartása. Emellett a MariaDB-példányok adatsíkja számára elérhető rendszergazdai fiókok leltárát is megtarthatja. (A MariaDB-kiszolgáló létrehozásakor meg kell adnia egy rendszergazdai felhasználó hitelesítő adatait. Ennek a rendszergazdának a használatával további MariaDB-felhasználók hozhatók létre.)

- [A MariaDB hozzáférés-kezelésének megismerése](https://docs.microsoft.com/azure/mariadb/concepts-security#access-management)

- [Az Azure-előfizetések Azure-beli beépített szerepköreinek megismerése](../role-based-access-control/built-in-roles.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="32-change-default-passwords-where-applicable"></a>3,2: az alapértelmezett jelszavak módosítása, ha alkalmazható

**Útmutató**: Azure Active Directory (Azure ad) nem az alapértelmezett jelszavak fogalma.

A MariaDB-erőforrás létrehozása után az Azure erős jelszóval kényszeríti a rendszergazda felhasználó létrehozását. A MariaDB-példány létrehozása után azonban a létrehozott első kiszolgálói rendszergazdai fiók használatával további felhasználókat hozhat létre, és rendszergazdai hozzáférést biztosíthat számukra. A fiókok létrehozásakor ügyeljen arra, hogy az egyes fiókokhoz eltérő, erős jelszót állítson be.

- [További fiókok létrehozása a MariaDB-hez](howto-create-users.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="33-use-dedicated-administrative-accounts"></a>3,3: dedikált rendszergazdai fiókok használata

**Útmutató**: szabványos működési eljárások létrehozása a MariaDB-példányokhoz hozzáféréssel rendelkező dedikált rendszergazdai fiókok használatával. A rendszergazdai fiókok számának figyeléséhez használja a Azure Security Center identitás-és hozzáférés-kezelés lehetőséget.

- [Azure Security Center identitás és hozzáférés ismertetése](../security-center/security-center-identity-access.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="34-use-azure-active-directory-single-sign-on-sso"></a>3,4: Azure Active Directory egyszeri bejelentkezés (SSO) használata

**Útmutató**: az MariaDB-hez való adatsíkokat az adatbázisban tárolt identitások vezérlik, és nem támogatja az egyszeri bejelentkezést. A MariaDB-hozzáférés vezérlése REST APIon keresztül érhető el, és támogatja az egyszeri bejelentkezést. A hitelesítéshez állítsa be a kérések engedélyezési fejlécét egy Azure Active Directory (Azure AD) által beszerzett JSON Web Tokenra.

- [Azure Database for MariaDB REST API ismertetése](/rest/api/mariadb/)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3,5: Multi-Factor Authentication használata az összes Azure Active Directory-alapú hozzáféréshez

**Útmutató**: engedélyezze Azure Active Directory (Azure ad) többtényezős hitelesítését, és kövesse Azure Security Center identitás-és hozzáférés-kezelési javaslatait.

- [Többtényezős hitelesítés engedélyezése az Azure-ban](../active-directory/authentication/howto-mfa-getstarted.md)

- [Identitás és hozzáférés figyelése Azure Security Centeron belül](../security-center/security-center-identity-access.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="36-use-secure-azure-managed-workstations-for-administrative-tasks"></a>3,6: biztonságos, Azure által felügyelt munkaállomások használata felügyeleti feladatokhoz

**Útmutató**: a mancsok (emelt szintű hozzáférésű munkaállomások) használata többtényezős hitelesítéssel, amely az Azure-erőforrások bejelentkezésére és konfigurálására van konfigurálva. 

- [További tudnivalók a privilegizált hozzáférésű munkaállomásokról](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

- [Többtényezős hitelesítés engedélyezése az Azure-ban](../active-directory/authentication/howto-mfa-getstarted.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3,7: naplózás és riasztás a gyanús tevékenységekről a rendszergazdai fiókoktól

**Útmutató**: a MariaDB komplex veszélyforrások elleni védelem engedélyezése a gyanús tevékenységekre vonatkozó riasztások létrehozásához.

Emellett Azure Active Directory (Azure AD) Privileged Identity Management (PIM) is használható a naplók és riasztások generálásához, ha gyanús vagy nem biztonságos tevékenység történik a környezetben. Az Azure AD-kockázati észlelések használatával a kockázatos felhasználói viselkedésre vonatkozó riasztásokat és jelentéseket tekinthet meg.

- [Komplex veszélyforrások elleni védelem beállítása a MariaDB](howto-database-threat-protection-portal.md)

- [Privileged Identity Management (PIM) üzembe helyezése](../active-directory/privileged-identity-management/pim-deployment-plan.md)

- [Az Azure AD kockázati észlelések ismertetése](/azure/active-directory/reports-monitoring/concept-risk-events)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3,8: az Azure-erőforrások kezelése csak jóváhagyott helyekről

**Útmutató**: a feltételes hozzáférés elnevezett helyeivel engedélyezheti a hozzáférést az IP-címtartományok vagy országok/régiók meghatározott logikai csoportjaiból, hogy korlátozza az Azure-erőforrásokhoz, például a MariaDB való hozzáférést.

- [Elnevezett helyszínek konfigurálása az Azure-ban](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="39-use-azure-active-directory"></a>3,9: a Azure Active Directory használata

**Útmutató**: a Azure Active Directory (Azure ad) használata központi hitelesítési és engedélyezési rendszerrel. Az Azure AD az adatok védelme érdekében erős titkosítást használ a nyugalmi és a továbbítási adatokhoz. Az Azure AD emellett a felhasználó hitelesítő adatainak a sók, a kivonatok és a biztonságos tárolását is tartalmazza.

Az Azure AD-hitelesítés nem használható közvetlen hozzáféréshez a MariaDB adatsíkon, azonban az Azure AD-hitelesítő adatok a felügyeleti sík szintjén (például a Azure Portal) használhatók a MariaDB rendszergazdai fiókok vezérléséhez.

- [A MariaDB rendszergazdai jelszavának frissítése](https://docs.microsoft.com/azure/mariadb/howto-create-manage-server-portal#update-admin-password)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="310-regularly-review-and-reconcile-user-access"></a>3,10: a felhasználói hozzáférés rendszeres áttekintése és egyeztetése

**Útmutató**: Tekintse át a Azure Active Directory (Azure ad) naplókat, amelyek segítenek felderíteni az elavult fiókokat, amelyek magukban foglalhatják a MariaDB rendszergazdai szerepköröket. Emellett az Azure Identity Access Reviews használatával hatékonyan kezelhetők a csoporttagságok, hozzáférhetnek a vállalati alkalmazásokhoz, amelyek a MariaDB és a szerepkör-hozzárendelések elérésére használhatók. A felhasználók hozzáférését rendszeresen felül kell vizsgálni, például 90 naponta, hogy csak a megfelelő felhasználók férhessenek hozzájuk.

- [Az Azure AD jelentéskészítés ismertetése](/azure/active-directory/reports-monitoring/)

- [Az Azure Identity hozzáférési felülvizsgálatok használata](../active-directory/governance/access-reviews-overview.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3,11: a figyelő megkísérli a deaktivált hitelesítő adatok elérését

**Útmutató**: a MariaDB és a Azure Active Directory (Azure ad) diagnosztikai beállításainak engedélyezése, az összes napló küldése egy log Analytics-munkaterületre. Konfigurálja a kívánt riasztásokat (például sikertelen hitelesítési kísérleteket) Log Analytics munkaterületen belül.

- [A kiszolgálók naplófájljainak konfigurálása és elérése a MariaDB](concepts-server-logs.md)

- [A MariaDB naplózási naplóinak konfigurálása és elérése](howto-configure-audit-logs-portal.md)

- [Azure-tevékenységnaplók integrálása az Azure Monitorba](/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3,12: riasztás a fiók bejelentkezési viselkedésének eltérése esetén

**Útmutató**: a MariaDB komplex veszélyforrások elleni védelemének engedélyezése. A Azure Database for MariaDB komplex veszélyforrások elleni védelme olyan rendellenes tevékenységeket észlel, amelyek szokatlan és potenciálisan ártalmas kísérleteket jeleznek az adatbázisok eléréséhez vagy kiaknázásához.

A Azure Active Directory (Azure AD) identitás-és kockázati észlelési funkciói segítségével konfigurálhatja az észlelt gyanús műveletekre vonatkozó automatizált válaszokat. A szervezet biztonsági válaszainak megvalósításához az Azure Sentinel használatával engedélyezheti az automatikus válaszokat.

- [Komplex veszélyforrások elleni védelem engedélyezése a MariaDB](howto-database-threat-protection-portal.md)

- [Az Identity Protection kockázati házirendjeinek konfigurálása és engedélyezése](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [Az Azure AD kockázatos bejelentkezéseinek megtekintése](/azure/active-directory/reports-monitoring/concept-risky-sign-ins)

- [Az Azure Sentinel előkészítése](../sentinel/quickstart-onboard.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

## <a name="data-protection"></a>Adatvédelem

*További információ: [Azure Security Benchmark: Adatvédelem](../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4,1: bizalmas információk leltárának fenntartása

**Útmutató**: a címkék használatával segítheti a Azure Database for MariaDB példányok és a bizalmas adatokat tároló vagy feldolgozó kapcsolódó erőforrások nyomon követését.

- [Címkék létrehozása és használata](/azure/azure-resource-manager/resource-group-using-tags)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4,2: bizalmas adatok tárolására vagy feldolgozására szolgáló rendszerek elkülönítése

**Útmutató**: különálló előfizetések és/vagy felügyeleti csoportok megvalósítása fejlesztési, tesztelési és éles környezetekhez. A MariaDB-példányok hálózati hozzáférésének elkülönítéséhez és korlátozásához használja a magánhálózati kapcsolat, a szolgáltatási végpontok és/vagy a MariaDB kombinációját.

- [További Azure-előfizetések létrehozása](/azure/billing/billing-create-subscription)

- [Management Groups létrehozása](/azure/governance/management-groups/create)

- [Privát hivatkozás konfigurálása Azure Database for MariaDBhoz](concepts-data-access-security-private-link.md)

- [Szolgáltatási végpontok konfigurálása Azure Database for MariaDBhoz](howto-manage-vnet-portal.md)

- [Tűzfalszabályok konfigurálása Azure Database for MariaDBhoz](concepts-firewall-rules.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4,3: a bizalmas adatok jogosulatlan átvitelének figyelése és letiltása

**Útmutató**: Ha Azure-beli virtuális gépeket használ a MariaDB-példányok eléréséhez, használja a privát hivatkozásokat, a MariaDB hálózati konfigurációkat, a hálózati biztonsági csoportokat és a szolgáltatási címkéket, hogy enyhítse az adatkiszűrése lehetőségét.

A Microsoft kezeli a MariaDB alapul szolgáló infrastruktúrát, és szigorú ellenőrzéseket vezetett be az ügyféladatok elvesztésének vagy kihatásának megelőzésére.

- [A Azure Database for MariaDB adatkiszűréseának enyhítése](concepts-data-access-security-private-link.md)

- [Az ügyféladatok Azure-beli védelmének ismertetése](../security/fundamentals/protection-customer-data.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4,4: minden bizalmas adat titkosítása az átvitel során

**Útmutató**: a Azure Database for MariaDB támogatja a Azure Database for MariaDB-kiszolgáló TRANSPORT Layer Security (TLS), korábbi nevén SSL (SSL) használatával történő csatlakoztatását az ügyfélalkalmazások számára. Az adatbázis-kiszolgáló és az ügyfélalkalmazások közötti TLS-kapcsolatok érvényesítése segít megvédeni a "férfit a középső" támadásokkal szemben azáltal, hogy titkosítja az adatfolyamot a kiszolgáló és az alkalmazás között. A Azure Portalban győződjön meg arról, hogy az **SSL-kapcsolat kikényszerítve** lehetőség van az összes MariaDB-példányra.

- [Titkosítás konfigurálása a MariaDB-ben](howto-configure-ssl.md)

**Felelősség**: Megosztott

**Azure Security Center figyelés**: nincs

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4,5: aktív felderítési eszköz használata a bizalmas adatok azonosítására

**Útmutató**: az adatazonosítási, besorolási és veszteség-megelőzési funkciók még nem érhetők el Azure Database for MariaDB számára. Külső gyártótól származó megoldás implementálása, ha az szükséges a megfelelőség szempontjából.

A Microsoft által felügyelt mögöttes platform esetében a Microsoft az összes vásárlói tartalmat bizalmasként kezeli, és az ügyfelek adatvesztésével és a kitettséggel szembeni védelem érdekében nagy hosszúságú. Annak biztosítása érdekében, hogy az Azure-beli ügyféladatok biztonságban maradjanak, a Microsoft végrehajtotta és karbantartja a robusztus adatvédelmi szabályozást és képességeket.

- [Az ügyféladatok Azure-beli védelmének ismertetése](../security/fundamentals/protection-customer-data.md)

**Felelősség**: Megosztott

**Azure Security Center figyelés**: nincs

### <a name="46-use-role-based-access-control-to-control-access-to-resources"></a>4,6: szerepköralapú hozzáférés-vezérlés használata az erőforrásokhoz való hozzáférés szabályozásához

**Útmutató**: az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC) használatával kezelheti az Azure-adatbázishoz való hozzáférést a MariaDB felügyeleti síkon (Azure Portal/Azure Resource Manager). Az adatsíkok eléréséhez (magán az adatbázison belül) használja az SQL-lekérdezéseket a felhasználók létrehozásához és a felhasználói engedélyek konfigurálásához.

- [Az Azure RBAC konfigurálása](../role-based-access-control/role-assignments-portal.md)

- [A felhasználói hozzáférés konfigurálása az SQL for MariaDB](howto-create-users.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4,9: a kritikus Azure-erőforrások változásainak naplózása és riasztása

**Útmutató**: a Azure monitor és az Azure-tevékenység naplójának használata riasztások létrehozásához, amikor a módosítások a Azure Database for MariaDB és más kritikus vagy kapcsolódó erőforrások éles példányain lépnek életbe.

- [Riasztások létrehozása az Azure-tevékenységek naplózási eseményeihez](/azure/azure-monitor/platform/alerts-activity-log)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

## <a name="vulnerability-management"></a>Biztonságirés-kezelés

*További információért lásd az [Azure biztonsági teljesítményteszt: biztonsági rés kezelése](../security/benchmarks/security-control-vulnerability-management.md)című témakört.*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5,1: automatikus biztonsági rések vizsgálatára szolgáló eszközök futtatása

**Útmutató**: jelenleg nem érhető el; A Azure Security Center még nem támogatja a Azure Database for MariaDB Server sebezhetőségi felmérését.

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

## <a name="inventory-and-asset-management"></a>Leltár-és eszközfelügyelet

*További információt az [Azure biztonsági teljesítményteszt: leltár és eszközkezelés](../security/benchmarks/security-control-inventory-asset-management.md)című témakörben talál.*

### <a name="61-use-automated-asset-discovery-solution"></a>6,1: automatikus eszköz-felderítési megoldás használata

**Útmutató**: az Azure Resource Graph használatával lekérdezheti és felderítheti az előfizetéseken belüli összes erőforrást (beleértve Azure Database for MariaDB kiszolgálókat is). Győződjön meg arról, hogy megfelelő (olvasási) engedélyekkel rendelkezik a bérlőben, és képes felsorolni az összes Azure-előfizetést, valamint az előfizetésében lévő erőforrásokat.

- [Lekérdezések létrehozása az Azure Resource Graph használatával](../governance/resource-graph/first-query-portal.md)

- [Azure-előfizetések megtekintése](/powershell/module/az.accounts/get-azsubscription)

- [Az Azure RBAC ismertetése](../role-based-access-control/overview.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="62-maintain-asset-metadata"></a>6,2: az eszköz metaadatainak fenntartása

**Útmutató**: címkéket alkalmazhat a Azure Database for MariaDB kiszolgálóra és más kapcsolódó erőforrásokra, amelyekkel a metaadatok logikailag rendezhetők a besorolásba.

- [Címkék létrehozása és használata](/azure/azure-resource-manager/resource-group-using-tags)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="63-delete-unauthorized-azure-resources"></a>6,3: jogosulatlan Azure-erőforrások törlése

**Útmutató**: a címkézés, a felügyeleti csoportok és a különálló előfizetések használata, ahol szükséges, Azure Database for MariaDB-kiszolgáló és a kapcsolódó erőforrások rendszerezése és nyomon követése. Rendszeres időközönként egyeztetheti a leltárt, és gondoskodhat arról, hogy a jogosulatlan erőforrások törlése az előfizetésből időben történjen.

- [További Azure-előfizetések létrehozása](/azure/billing/billing-create-subscription)

- [Management Groups létrehozása](/azure/governance/management-groups/create)

- [Címkék létrehozása és használata](/azure/azure-resource-manager/resource-group-using-tags)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6,4: a jóváhagyott Azure-erőforrások leltárának meghatározása és karbantartása

**Útmutató**: nem alkalmazható; Ez a javaslat a számítási erőforrások és az Azure egészének fedezésére szolgál.

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="65-monitor-for-unapproved-azure-resources"></a>6,5: a nem jóváhagyott Azure-erőforrások figyelése

**Útmutató**: a Azure Policy használatával korlátozásokat állíthat be az ügyfél-előfizetésekben létrehozható erőforrások típusára a következő beépített szabályzat-definíciók használatával:

- Nem engedélyezett erőforrástípusok

- Engedélyezett erőforrástípusok

Emellett az Azure Resource Graph használatával lekérdezheti vagy felderítheti az előfizetéseken belüli erőforrásokat.

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

- [Adott erőforrástípus megtagadása a következővel Azure Policy](https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#general)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6,11: korlátozza a felhasználók képességét a Azure Resource Manager való interakcióra

**Útmutató**: az Azure feltételes hozzáférés használatával korlátozhatja, hogy a felhasználók képesek legyenek a Azure Resource Manager interakcióra az "Microsoft Azure felügyelet" alkalmazás "hozzáférés tiltása" beállításával. Ez megakadályozhatja, hogy a magas biztonsági környezetben, például a bizalmas adatokat tartalmazó Azure Database for MariaDB kiszolgálón belül hozzanak létre és módosítsanak erőforrásokat.

- [A feltételes hozzáférés konfigurálása a Azure Resource Managerhoz való hozzáférés blokkolásához](../role-based-access-control/conditional-access-azure-management.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

## <a name="secure-configuration"></a>Biztonságos konfiguráció

*További információt az [Azure biztonsági teljesítményteszt: biztonságos konfiguráció](../security/benchmarks/security-control-secure-configuration.md)című témakörben talál.*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7,1: biztonságos konfigurációk létrehozása az összes Azure-erőforráshoz

**Útmutató**: a Azure Database for MariaDB példányok szabványos biztonsági konfigurációinak meghatározása és implementálása Azure Policy használatával. Használjon Azure Policy aliasokat a "Microsoft. DBforMariaDB" névtérben egyéni szabályzatok létrehozásához a Azure Database for MariaDB-kiszolgálók hálózati konfigurációjának naplózásához vagy betartatásához. A Azure Database for MariaDB-kiszolgálókhoz kapcsolódó beépített szabályzat-definíciókat is használhatja, például:

- A Geo-redundáns biztonsági mentést engedélyezni kell a Azure Database for MariaDB

További információkat az alábbi hivatkozásokon találhat:

- [Az elérhető Azure Policy aliasok megtekintése](/powershell/module/az.resources/get-azpolicyalias)

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

**Útmutató**: ha a Azure Database for MariaDB-kiszolgálókhoz és a kapcsolódó erőforrásokhoz egyéni Azure Policy-definíciókat használ, az Azure Repos segítségével biztonságosan tárolhatja és kezelheti a kódot.

- [Kód tárolása az Azure DevOps](/azure/devops/repos/git/gitworkflow)

- [Az Azure Repos dokumentációja](/azure/devops/repos/)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7,7: az Azure-erőforrások konfigurációs felügyeleti eszközeinek üzembe helyezése

**Útmutató**: Azure Policy aliasok használata a "Microsoft. DBforMariaDB" névtérben egyéni szabályzatok létrehozásához a riasztáshoz, a naplózáshoz és a rendszer-konfigurációk kényszerítéséhez. Emellett dolgozzon ki egy folyamatot és egy folyamatot a házirend-kivételek kezeléséhez.

- [Az Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7,9: automatikus konfigurációs monitorozás megvalósítása Azure-erőforrásokhoz

**Útmutató**: Azure Policy aliasok használata a "Microsoft. DBforMariaDB" névtérben egyéni szabályzatok létrehozásához a riasztáshoz, a naplózáshoz és a rendszer-konfigurációk kényszerítéséhez. A Azure Database for MariaDB-példányok és a kapcsolódó erőforrások konfigurációjának automatikus érvényesítéséhez használja a Azure Policy [audit], [megtagadás] és [üzembe helyezés ha nem létezik] lehetőséget.

- [Az Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="711-manage-azure-secrets-securely"></a>7,11: az Azure-titkok biztonságos kezelése

**Útmutató**: a Azure Database for MariaDB-kiszolgálók elérésére használt Azure app Service-on futó Azure-Virtual Machines vagy webalkalmazások esetében a Managed Service Identity használata Azure Key Vault a kiszolgáló titkos felügyeletének egyszerűsítése és biztonságossá tétele érdekében. Győződjön meg arról, Key Vault a Soft delete engedélyezve van.

- [Integráció az Azure felügyelt identitásokkal](../azure-app-configuration/howto-integrate-azure-managed-service-identity.md)

- [Key Vault létrehozása](../key-vault/general/quick-create-portal.md)

- [Hitelesítés Key Vault](../key-vault/general/authentication.md)

- [Key Vault hozzáférési szabályzatok kiosztása](../key-vault/general/assign-access-policy-portal.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="712-manage-identities-securely-and-automatically"></a>7,12: az identitások biztonságos és automatikus kezelése

**Útmutató**: Azure Database for MariaDB a kiszolgáló jelenleg nem támogatja a Azure Active Directory (Azure ad) hitelesítését az adatbázisok eléréséhez. A Azure Database for MariaDB-kiszolgáló létrehozásakor meg kell adnia egy rendszergazdai felhasználó hitelesítő adatait. Ennek a rendszergazdának a segítségével további MariaDB-felhasználókat hozhat létre.

A Azure Database for MariaDB-kiszolgáló elérésére használt Azure App Service-on futó Azure-Virtual Machines vagy webalkalmazások esetében a felügyelt identitások használata a Azure Database for MariaDB-kiszolgáló hitelesítő adatainak tárolására és lekérésére Azure Key Vaultsal együtt. Győződjön meg arról, Key Vault a Soft delete engedélyezve van.

Felügyelt identitások használatával biztosíthatja az Azure-szolgáltatások automatikus felügyelt identitását az Azure AD-ben. A felügyelt identitások lehetővé teszik bármely olyan szolgáltatás hitelesítését, amely támogatja az Azure AD-hitelesítést, beleértve a Key Vault is, a kódban szereplő hitelesítő adatok nélkül.

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

**Útmutató**: a Microsoft kártevő szoftverrel való ellátása engedélyezve van az Azure-szolgáltatásokat támogató mögöttes gazdagépen (például Azure Database for MariaDB-kiszolgáló), azonban nem az ügyfél tartalmán fut.

A nem számítási Azure-erőforrásokra feltöltött tartalom előzetes vizsgálata, például App Service, Data Lake Storage, Blob Storage, Azure Database for MariaDB-kiszolgáló stb. A Microsoft nem fér hozzá az adataihoz ezekben a példányokban.

**Felelősség**: Megosztott

**Azure Security Center figyelés**: nincs

## <a name="data-recovery"></a>Adat-helyreállítás

*További információt az [Azure biztonsági teljesítményteszt: adat-helyreállítás](../security/benchmarks/security-control-data-recovery.md)című témakörben talál.*

### <a name="91-ensure-regular-automated-back-ups"></a>9,1: rendszeres automatizált biztonsági másolatok biztosítása

**Útmutató**: a Azure Database for MariaDB teljes, differenciált és tranzakciós naplóbeli biztonsági mentést készít.  Azure Database for MariaDB automatikusan létrehozza a kiszolgáló biztonsági másolatait, és a helyileg redundáns vagy földrajzilag redundáns tárolóban tárolja azokat. A biztonsági másolatokkal a kiszolgáló adott időpontnak megfelelő állapotra állítható vissza. A biztonsági mentés és helyreállítás minden üzletmenet-folytonossági stratégia elengedhetetlen része, hiszen ez védi meg az adatokat a véletlen sérülésektől és törléstől.  Az alapértelmezett biztonsági mentési megőrzési időszak hét nap. Opcionálisan akár 35 napig is beállíthatja. Minden biztonsági mentés AES 256 bites titkosítással van titkosítva.

- [A MariaDB biztonsági mentéseinak megismerése](concepts-backup.md)

- [A MariaDB kezdeti konfigurációjának ismertetése](tutorial-design-database-using-portal.md)

**Felelősség**: Megosztott

**Azure Security Center monitorozás**: az [Azure biztonsági teljesítményteszt](/azure/governance/policy/samples/azure-security-benchmark) a Security Center alapértelmezett házirend-kezdeményezése, és a [Security Center ajánlásainak](/azure/security-center/security-center-recommendations)alapja. A vezérlőhöz kapcsolódó Azure Policy-definíciók Security Center automatikusan engedélyezve vannak. Az ehhez a vezérlőhöz kapcsolódó riasztásokhoz szükség lehet egy [Azure Defender](/azure/security-center/azure-defender) -csomagra a kapcsolódó szolgáltatásokhoz.

**Azure Policy beépített definíciók – Microsoft. DBforMariaDB**:

[!INCLUDE [Resource Policy for Microsoft.DBforMariaDB 9.1](../../includes/policy/standards/asb/rp-controls/microsoft.dbformariadb-9-1.md)]

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: teljes rendszerbiztonsági mentés és minden ügyfél által felügyelt kulcs biztonsági mentése

**Útmutató**: a Azure Database for MariaDB automatikusan létrehozza a kiszolgáló biztonsági másolatait, és tárolja őket a helyileg redundáns vagy geo-redundáns tárolóban. A biztonsági másolatokkal a kiszolgáló adott időpontnak megfelelő állapotra állítható vissza.  A biztonsági mentés és helyreállítás minden üzletmenet-folytonossági stratégia elengedhetetlen része, hiszen ez védi meg az adatokat a véletlen sérülésektől és törléstől.

Ha a MariaDB-kiszolgálón tárolt adathoz Key Vault használ az ügyféloldali adattitkosításhoz, győződjön meg arról, hogy a kulcsok rendszeres automatizált biztonsági mentései vannak.

- [A MariaDB biztonsági mentéseinak megismerése](concepts-backup.md)

- [Key Vault kulcsok biztonsági mentése](/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey)

**Felelősség**: Megosztott

**Azure Security Center monitorozás**: az [Azure biztonsági teljesítményteszt](/azure/governance/policy/samples/azure-security-benchmark) a Security Center alapértelmezett házirend-kezdeményezése, és a [Security Center ajánlásainak](/azure/security-center/security-center-recommendations)alapja. A vezérlőhöz kapcsolódó Azure Policy-definíciók Security Center automatikusan engedélyezve vannak. Az ehhez a vezérlőhöz kapcsolódó riasztásokhoz szükség lehet egy [Azure Defender](/azure/security-center/azure-defender) -csomagra a kapcsolódó szolgáltatásokhoz.

**Azure Policy beépített definíciók – Microsoft. DBforMariaDB**:

[!INCLUDE [Resource Policy for Microsoft.DBforMariaDB 9.2](../../includes/policy/standards/asb/rp-controls/microsoft.dbformariadb-9-2.md)]

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: az összes biztonsági másolat ellenőrzése, beleértve az ügyfél által felügyelt kulcsokat

**Útmutató**: Azure Database for MariaDB a biztonsági mentések rendszeres teszteléséhez hajtson végre visszaállítást az eredeti kiszolgáló biztonsági másolatainak használatával. Kétféle visszaállítás érhető el: az időponthoz tartozó visszaállítás és a Geo-visszaállítás. Az időponthoz való visszaállítás a Backup redundancia beállítással érhető el, és egy új kiszolgálót hoz létre ugyanabban a régióban, ahol az eredeti kiszolgáló található. A Geo-visszaállítás csak akkor érhető el, ha a kiszolgálót a földrajzilag redundáns tároláshoz konfigurálta, és lehetővé teszi a kiszolgáló másik régióba való visszaállítását.

A helyreállítás becsült ideje több tényezőtől függ, többek között az adatbázisok méretétől, a tranzakciós napló méretétől, a hálózati sávszélességtől és az azonos régióban lévő adatbázisok teljes számától. A helyreállítási idő általában kevesebb, mint 12 óra.

- [A Azure Database for MariaDB biztonsági mentésének és visszaállításának ismertetése](https://docs.microsoft.com/azure/mariadb/concepts-backup#restore)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: a biztonsági másolatok és az ügyfél által felügyelt kulcsok védelmének biztosítása

**Útmutató**: a Azure Database for MariaDB teljes, differenciált és tranzakciós naplóbeli biztonsági mentést készít. Ezek a biztonsági másolatok lehetővé teszik a kiszolgálók visszaállítását bármely időpontra a beállított biztonsági mentési megőrzési időszakon belül. Az alapértelmezett biztonsági mentési megőrzési időszak hét nap. Opcionálisan akár 35 napig is beállíthatja. Minden biztonsági mentés AES 256 bites titkosítással van titkosítva.

- [A Azure Database for MariaDB biztonsági mentésének és visszaállításának ismertetése](concepts-backup.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

## <a name="incident-response"></a>Incidensmegoldás

*További információ: [Azure Security Benchmark: Incidensek kezelése](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10,1: incidens-válaszi útmutató létrehozása

**Útmutató**: Alakítson ki incidenskezelési útmutatót a vállalat számára. Gondoskodjon írásos incidenskezelési tervekről, amelyek definiálják az összes résztvevő szerepkörét, valamint az incidenskezelés fázisait az észleléstől az incidens utáni értékelésig.

- [Útmutató a saját biztonsági incidensek megoldási folyamatának létrehozásához](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Microsoft Security Response Center – incidens anatómiája](https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/)

- [Az ügyfél a NIST számítógépes biztonsági incidensek kezelési útmutatóját is kihasználhatja a saját incidens-válasz tervének létrehozásával kapcsolatos támogatáshoz](https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10,2: incidensek pontozásának és rangsorolási eljárásának létrehozása

**Útmutató**: a Azure Security Center súlyosságot rendel az egyes riasztásokhoz, hogy a prioritások alapján ki lehessen deríteni, hogy mely riasztásokat kell először megvizsgálni. A súlyosság azon alapul, hogy az Security Center milyen mértékben szerepel a riasztások kijavításához használt mérőszámban, illetve a riasztást eredményező tevékenységen belül rosszindulatú szándékkal.

Emellett egyértelműen megjelölheti a címkéket használó előfizetéseket, és létrehozhat egy elnevezési rendszert az Azure-erőforrások egyértelmű azonosításához és kategorizálásához, különösen a bizalmas adatok feldolgozásához. Az Ön felelőssége, hogy rangsorolja a riasztások megoldását azon Azure-erőforrások és -környezetek kritikussága alapján, ahol az incidens történt.

- [Biztonsági riasztások az Azure Security Centerben](../security-center/security-center-alerts-overview.md)

- [Címkék használata az Azure-erőforrások rendszerezéséhez](/azure/azure-resource-manager/resource-group-using-tags)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="103-test-security-response-procedures"></a>10,3: biztonsági reagálási eljárások tesztelése

**Útmutató**: az Azure-erőforrások védelmének biztosítása érdekében a rendszer az incidensek reagálási képességeinek rendszeres tesztelésére szolgáló gyakorlatokat hajt végre. Azonosítsa a gyenge pontokat és réseket, és szükség esetén dolgozza át a tervet.

- [Tekintse meg a NIST kiadványát: útmutató az IT-csomagok és-képességek teszteléséhez, betanításához és edzésprogramjának kidolgozásához](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10,4: biztonsági incidensek elérhetőségének biztosítása és riasztási értesítések konfigurálása biztonsági incidensekhez

**Útmutató**: a Microsoft a biztonsági incidensek elérhetőségi adatait arra használja fel, hogy felvegye Önnel a kapcsolatot, ha a Microsoft Security Response Center (MSRC) felfedi, hogy az adatokat egy törvénytelen vagy jogosulatlan fél is hozzáférte. A problémák megoldása érdekében tekintse át az incidenseket a tény után.

- [A Azure Security Center biztonsági kapcsolattartó beállítása](../security-center/security-center-provide-security-contact-details.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10,5: biztonsági riasztások beépítése az incidensek gyorsreagáló rendszerébe

**Útmutató**: az Azure Security Center-riasztások és javaslatok exportálása a folyamatos exportálás funkcióval az Azure-erőforrásokkal kapcsolatos kockázatok azonosítása érdekében. A folyamatos exportálás lehetővé teszi a riasztások és javaslatok manuális és folyamatos exportálását. Az Azure Security Center adatösszekötővel továbbíthatja a riasztásokat az Azure Sentinel szolgáltatásba.

- [Folyamatos exportálás konfigurálása](../security-center/continuous-export.md)

- [Riasztások streamelése az Azure Sentinelbe](../sentinel/connect-azure-security-center.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="106-automate-the-response-to-security-alerts"></a>10,6: a biztonsági riasztásokra adott válasz automatizálása

**Útmutató**: a Azure Security Center munkafolyamat-automatizálási funkciója segítségével automatikusan elindíthatja a válaszokat az "Logic apps" használatával a biztonsági riasztások és az Azure-erőforrások védelme érdekében javasolt javaslatok alapján.
    

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
