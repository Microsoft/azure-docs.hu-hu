---
title: Azure SQL Database Azure biztonsági alapterve
description: A Azure SQL Database biztonsági alapterve az Azure biztonsági Teljesítménytesztben meghatározott biztonsági javaslatok megvalósítására szolgáló eljárási útmutatást és erőforrásokat biztosít.
author: msmbaldwin
ms.service: sql-database
ms.topic: conceptual
ms.date: 02/17/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 87072ecc4bff054d64c3d8576f821e725959ea7e
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/02/2021
ms.locfileid: "101657791"
---
# <a name="azure-security-baseline-for-azure-sql-database"></a>Azure SQL Database Azure biztonsági alapterve

Ez a biztonsági alapkonfiguráció az [Azure biztonsági teljesítményteszt 1,0-es verziójáról](../../security/benchmarks/overview.md) Azure SQL Databasere vonatkozó útmutatást alkalmaz. Az Azure Security Benchmark ajánlásokat ad arra nézve, hogy hogyan tehetők biztonságossá a felhőalapú megoldások az Azure-ban. A tartalom az Azure biztonsági teljesítményteszt által meghatározott **biztonsági vezérlők** szerint van csoportosítva, valamint a Azure SQL Database vonatkozó kapcsolódó útmutatás. A Azure SQL Database nem alkalmazható **vezérlők** ki vannak zárva.

Ha szeretné megtekinteni, hogyan Azure SQL Database teljes mértékben leképezni az Azure biztonsági Teljesítménytesztét, tekintse meg a [teljes Azure SQL Database biztonsági alapterv-leképezési fájlt](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="network-security"></a>Hálózati biztonság

*További információ: [Azure Security Benchmark: Hálózati biztonság](../../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1,1: Azure-erőforrások biztosítása virtuális hálózatokon belül

**Útmutató**: az Azure Private-hivatkozás engedélyezésével engedélyezheti az Azure Pásti-szolgáltatások (például SQL Database) és az Azure által üzemeltetett ügyfél/partner szolgáltatások elérését a virtuális hálózat privát végpontján keresztül. A virtuális hálózat és a szolgáltatás közötti forgalom a Microsoft gerinchálózatán keresztül halad át, így kiküszöböli a nyilvános internet jelentette kitettséget. 

Ha engedélyezni szeretné, hogy a forgalom elérje a Azure SQL Database, használja az SQL-szolgáltatás címkéit, hogy engedélyezze a kimenő forgalmat a hálózati biztonsági csoportokon keresztül.

A virtuális hálózati szabályok lehetővé teszik, hogy a Azure SQL Database csak a virtuális hálózaton belüli kijelölt alhálózatokból továbbított kommunikációt fogadják.

- [Privát hivatkozás beállítása Azure SQL Databasehoz](/azure/sql-database/sql-database-private-endpoint-overview#how-to-set-up-private-link-for-azure-sql-database)

- [A Virtual Network szolgáltatásbeli végpontok és az adatbázis-kiszolgálók szabályainak használata](/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás**: az [Azure biztonsági teljesítményteszt](/home/mbaldwin/docs/asb/azure-docs-pr/articles/governance/policy/samples/azure-security-benchmark.md) a Security Center alapértelmezett házirend-kezdeményezése, és a [Security Center ajánlásainak](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/security-center-recommendations.md)alapja. A vezérlőhöz kapcsolódó Azure Policy-definíciók Security Center automatikusan engedélyezve vannak. Az ehhez a vezérlőhöz kapcsolódó riasztásokhoz szükség lehet egy [Azure Defender](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/azure-defender.md) -csomagra a kapcsolódó szolgáltatásokhoz.

**Beépített definíciók Azure Policy – Microsoft. SQL**:

[!INCLUDE [Resource Policy for Microsoft.Sql 1.1](../../../includes/policy/standards/asb/rp-controls/microsoft.sql-1-1.md)]

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-network-interfaces"></a>1,2: a virtuális hálózatok, alhálózatok és hálózati adapterek konfigurációjának és forgalmának figyelése és naplózása

**Útmutató**: a Azure Security Center használata és a hálózati védelmi javaslatok szervizelése arra az alhálózatra, amelyen a Azure SQL Database-kiszolgáló telepítve van. 

Az Azure Virtual Machines (VM) esetében, amely csatlakozni fog a Azure SQL Database Server-példányhoz, engedélyezze a hálózati biztonsági csoport (NSG) folyamatábráit a virtuális gépek védelmét biztosító NSG, és küldjön naplókat egy Azure Storage-fiókba a forgalom naplózásához. 

NSG-naplókat is küldhet egy Log Analytics munkaterületre, és a Traffic Analytics használatával betekintést nyerhet az Azure-Felhőbeli forgalomba. A Traffic Analytics egyes előnyei lehetővé teszi a hálózati tevékenységek megjelenítését és a gyakori pontok azonosítását, a biztonsági fenyegetések azonosítását, a forgalomban rejlő minták értelmezését, valamint a hálózati helytelen konfigurációk meghatározását.

- [A NSG folyamat naplófájljainak engedélyezése](../../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [A Azure Security Center által biztosított hálózati biztonság ismertetése](../../security-center/security-center-network-recommendations.md)

- [A Traffic Analytics engedélyezése és használata](../../network-watcher/traffic-analytics.md)

- [A Azure Security Center által biztosított hálózati biztonság ismertetése](../../security-center/security-center-network-recommendations.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1,4: az ismert kártevő IP-címekkel folytatott kommunikáció megtagadása

**Útmutató**: az elosztott szolgáltatásmegtagadási támadások elleni védelem érdekében engedélyezze a SQL Server-példányokhoz társított virtuális hálózatok DDoS Protection szabványának engedélyezését. A Azure Security Center integrált fenyegetési intelligencia használatával megtagadhatja a kommunikációt az ismert kártékony vagy nem használt internetes IP-címekkel.

- [A DDoS Protection konfigurálása](/azure/virtual-network/manage-ddos-protection)

- [Az Azure Security Center integrált veszélyforrások felderítésének megismerése](/azure/security-center/security-center-alerts-data-services)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="15-record-network-packets"></a>1,5: hálózati csomagok rögzítése

**Útmutató**: az Azure SQL Database-példányhoz csatlakozó Azure Virtual Machines (VM-EK) esetében engedélyezze a hálózati biztonsági csoport (NSG) naplófájljait a virtuális gépek védelmét biztosító NSG, és küldjön naplókat egy Azure Storage-fiókba a forgalom naplózásához. Ha a rendellenes tevékenységek kivizsgálásához szükséges, engedélyezze Network Watcher csomagok rögzítését.

- [A NSG folyamat naplófájljainak engedélyezése](../../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [A Network Watcher engedélyezése](../../network-watcher/network-watcher-create.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1,6: hálózati behatolás-észlelési/Behatolás-megelőzési rendszerek (AZONOSÍTÓk/IP-címek) üzembe helyezése

**Útmutató**: a Azure SQL Database a komplex veszélyforrások elleni védelem (ATP) engedélyezése.  A felhasználók riasztást kapnak a gyanús adatbázis-tevékenységekről, a potenciális sebezhetőségekről és az SQL-injektálási támadásokról, valamint a rendellenes adatbázis-hozzáférési és lekérdezési mintákról. A komplex veszélyforrások elleni védelem a riasztásokat is integrálja Azure Security Centerokkal. 

- [A Azure SQL Database összetett veszélyforrások elleni védelmének megismerése és használata](/azure/sql-database/sql-database-threat-detection-overview)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1,8: a hálózati biztonsági szabályok bonyolultságának és adminisztratív terhelésének csökkentése

**Útmutató**: hálózati biztonsági csoportok vagy Azure Firewall hálózati hozzáférés-vezérlésének definiálásához használja a virtuális hálózati szolgáltatás címkéit. Biztonsági szabályok létrehozása során szolgáltatáscímkéket használhat bizonyos IP-címek helyett. Ha egy szabály megfelelő forrás vagy cél mezőjében megadja a szolgáltatási címke nevét (például ApiManagement), akkor engedélyezheti vagy megtagadhatja a megfelelő szolgáltatás forgalmát. A Microsoft kezeli a szolgáltatási címke által felölelt címek előtagjait, és automatikusan frissíti a szolgáltatási címkét a címek változásával.

Ha Azure SQL Database szolgáltatási végpontokat használ, a kimenő és a Azure SQL Database nyilvános IP-címek szükségesek: a hálózati biztonsági csoportokat (NSG) meg kell nyitni ahhoz, hogy a kapcsolat engedélyezze a Azure SQL Database IP-címeket. Ezt a Azure SQL Database NSG-szolgáltatásának címkéi segítségével teheti meg.

- [A szolgáltatás-címkék ismertetése a Azure SQL Database szolgáltatás-végpontokkal](/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview#limitations)

- [A szolgáltatási címkék megismerése és használata](../../virtual-network/service-tags-overview.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1,9: a hálózati eszközök szabványos biztonsági konfigurációinak fenntartása

**Útmutató**: hálózati biztonsági konfigurációk definiálása és implementálása a Azure SQL Database Server-példányokhoz Azure Policy használatával. A "Microsoft. SQL" névtér használatával egyéni házirend-definíciókat határozhat meg, vagy használhatja a Azure SQL Database Server Network Protection számára tervezett beépített szabályzat-definíciókat is. A Azure SQL Database-kiszolgáló megfelelő beépített hálózati biztonsági házirendjének példája a következő: "SQL Server virtuális hálózati szolgáltatás-végpontot használjon".

 

Az Azure-tervezetek segítségével leegyszerűsítheti a nagyméretű Azure-környezetek nagy léptékű üzembe helyezését, például az Azure Resource Management-sablonok, az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC) és a házirendek használatát egyetlen terv definíciójában. Egyszerűen alkalmazhatja a tervrajzot új előfizetésekre és környezetekre, és részletesen beállíthatja a vezérlést és a felügyeletet a verziószámozással.

- [Az Azure Policy konfigurálása és kezelése](../../governance/policy/tutorials/create-and-manage.md)

- [Azure Blueprint létrehozása](../../governance/blueprints/create-blueprint-portal.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="110-document-traffic-configuration-rules"></a>1,10: a dokumentum forgalmának konfigurációs szabályai

**Útmutató**: használja a hálózati biztonsági csoportokhoz (NSG) és a hálózati biztonsággal és a forgalommal kapcsolatos egyéb erőforrásokhoz tartozó címkéket. Az egyes NSG-szabályok esetében a "Leírás" mezővel adhatja meg az üzleti igényeket és/vagy időtartamot (stb.) minden olyan szabályhoz, amely engedélyezi a hálózatra irányuló adatforgalmat.

A címkézéshez kapcsolódó beépített Azure Policy definíciók bármelyikét használhatja, például a "címke és az érték megkövetelése" beállítást, hogy az összes erőforrás címkével legyen létrehozva, és értesítse a meglévő címkézetlen erőforrásokról.

A Azure PowerShell vagy az Azure CLI használatával a címkék alapján kereshet vagy végezhet műveleteket az erőforrásokon.

- [Címkék létrehozása és használata](/azure/azure-resource-manager/resource-group-using-tags)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1,11: automatikus eszközök használata a hálózati erőforrások konfigurációjának figyelésére és a változások észlelésére

**Útmutató**: az Azure-tevékenység naplójának használata a hálózati erőforrás-konfigurációk figyelésére és a Azure SQL Database kiszolgálói példányokhoz kapcsolódó hálózati erőforrások változásainak észlelésére. Hozzon létre riasztásokat Azure Monitoron belül, amelyek akkor lépnek életbe, amikor a kritikus hálózati erőforrásokra vonatkozó módosításokat végrehajtják

- [Azure-Tevékenységnaplók eseményeinek megtekintése és lekérése](/azure/azure-monitor/platform/activity-log-view)

- [Riasztások létrehozása a Azure Monitorban](/azure/azure-monitor/platform/alerts-activity-log)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

## <a name="logging-and-monitoring"></a>Naplózás és monitorozás

*További információt az [Azure biztonsági teljesítményteszt: naplózás és figyelés](../../security/benchmarks/security-control-logging-monitoring.md)című témakörben talál.*

### <a name="22-configure-central-security-log-management"></a>2,2: a központi biztonsági naplók felügyeletének konfigurálása

**Útmutató**: az Azure SQL Database naplózásának engedélyezése az adatbázis-események nyomon követéséhez és az Azure Storage-fiókba, log Analytics-munkaterülethez vagy Event Hubs naplóba való íráshoz.

Emellett az Azure SQL Diagnostics-telemetria Azure SQL Analyticsba, egy felhőalapú megoldásba is továbbíthatja, amely az Azure SQL Database-adatbázisok és az Azure SQL felügyelt példányainak teljesítményét figyeli a skálán és több előfizetésen keresztül. Segítséget nyújt Azure SQL Database teljesítmény-mérőszámok gyűjtéséhez és megjelenítéséhez, és beépített intelligenciával rendelkezik a teljesítménnyel kapcsolatos hibaelhárításhoz.

- [A Azure SQL Database naplózásának beállítása](/azure/sql-database/sql-database-auditing)

- [Platform-naplók és-metrikák összegyűjtése Azure Monitor](/azure/sql-database/sql-database-metrics-diag-logging)

- [A diagnosztika továbbítása a Azure SQL Analyticsba](/azure/sql-database/sql-database-metrics-diag-logging#stream-into-azure-sql-analytics)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="23-enable-audit-logging-for-azure-resources"></a>2,3: az Azure-erőforrások naplózásának engedélyezése

**Útmutatás**: engedélyezze a naplózást az Azure SQL Database Server-példányon, és válasszon egy tárolási helyet a naplók számára (Azure storage, log Analytics vagy Event hub).

- [Az Azure-SQL Server naplózásának engedélyezése](/azure/sql-database/sql-database-auditing)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás**: az [Azure biztonsági teljesítményteszt](/home/mbaldwin/docs/asb/azure-docs-pr/articles/governance/policy/samples/azure-security-benchmark.md) a Security Center alapértelmezett házirend-kezdeményezése, és a [Security Center ajánlásainak](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/security-center-recommendations.md)alapja. A vezérlőhöz kapcsolódó Azure Policy-definíciók Security Center automatikusan engedélyezve vannak. Az ehhez a vezérlőhöz kapcsolódó riasztásokhoz szükség lehet egy [Azure Defender](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/azure-defender.md) -csomagra a kapcsolódó szolgáltatásokhoz.

**Beépített definíciók Azure Policy – Microsoft. SQL**:

[!INCLUDE [Resource Policy for Microsoft.Sql 2.3](../../../includes/policy/standards/asb/rp-controls/microsoft.sql-2-3.md)]

### <a name="25-configure-security-log-storage-retention"></a>2,5: a biztonsági napló tárolási adatmegőrzésének konfigurálása

**Útmutató**: ha a Azure SQL Database-naplókat egy log Analytics munkaterületen tárolja, állítsa be a napló megőrzési időszakát a szervezet megfelelőségi szabályainak megfelelően.

- [Napló-megőrzési paraméterek beállítása](/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás**: az [Azure biztonsági teljesítményteszt](/home/mbaldwin/docs/asb/azure-docs-pr/articles/governance/policy/samples/azure-security-benchmark.md) a Security Center alapértelmezett házirend-kezdeményezése, és a [Security Center ajánlásainak](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/security-center-recommendations.md)alapja. A vezérlőhöz kapcsolódó Azure Policy-definíciók Security Center automatikusan engedélyezve vannak. Az ehhez a vezérlőhöz kapcsolódó riasztásokhoz szükség lehet egy [Azure Defender](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/azure-defender.md) -csomagra a kapcsolódó szolgáltatásokhoz.

**Beépített definíciók Azure Policy – Microsoft. SQL**:

[!INCLUDE [Resource Policy for Microsoft.Sql 2.5](../../../includes/policy/standards/asb/rp-controls/microsoft.sql-2-5.md)]

### <a name="26-monitor-and-review-logs"></a>2,6: naplók figyelése és áttekintése

**Útmutató**: a rendellenes viselkedésre vonatkozó naplók elemzése és figyelése, valamint az eredmények rendszeres áttekintése. A Azure Security Center komplex veszélyforrások elleni védelme a Azure SQL Database-példányhoz kapcsolódó szokatlan tevékenységgel kapcsolatos riasztások esetén használható. Azt is megteheti, hogy a riasztásokat metrikus értékek vagy az Azure SQL Database példányokhoz kapcsolódó Azure-műveletnapló bejegyzései alapján konfigurálja.

- [Az Azure-SQL Server komplex veszélyforrások elleni védelem és riasztások megismerése](/azure/sql-database/sql-database-threat-detection-overview)

- [Egyéni riasztások konfigurálása Azure SQL Databasehoz](alerts-insights-configure-portal.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="27-enable-alerts-for-anomalous-activities"></a>2,7: riasztások engedélyezése rendellenes tevékenységekhez

**Útmutató**: az Azure SQL Database-adatbázisok Azure Security Center komplex veszélyforrások elleni védelmének használata a rendellenes tevékenység figyelésére és riasztására. Engedélyezze az SQL-adatbázisokhoz készült Azure Defendert az SQL-adatbázisok számára. Az SQL-hez készült Azure Defender a bizalmas adatok felderítésére és osztályozására, a felszínre és a lehetséges adatbázis-sebezhetőségek enyhítésére, valamint az adatbázis fenyegetését jelző rendellenes tevékenységek észlelésére szolgáló funkciókat tartalmaz.

- [Az összetett veszélyforrások elleni védelem és a riasztások megismerése Azure SQL Database](/azure/sql-database/sql-database-threat-detection-overview)

- [Az Azure Defender for SQL engedélyezése Azure SQL Database](azure-defender-for-sql.md)

- [Riasztások kezelése Azure Security Centerban](../../security-center/security-center-managing-and-responding-alerts.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás**: az [Azure biztonsági teljesítményteszt](/home/mbaldwin/docs/asb/azure-docs-pr/articles/governance/policy/samples/azure-security-benchmark.md) a Security Center alapértelmezett házirend-kezdeményezése, és a [Security Center ajánlásainak](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/security-center-recommendations.md)alapja. A vezérlőhöz kapcsolódó Azure Policy-definíciók Security Center automatikusan engedélyezve vannak. Az ehhez a vezérlőhöz kapcsolódó riasztásokhoz szükség lehet egy [Azure Defender](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/azure-defender.md) -csomagra a kapcsolódó szolgáltatásokhoz.

**Beépített definíciók Azure Policy – Microsoft. SQL**:

[!INCLUDE [Resource Policy for Microsoft.Sql 2.7](../../../includes/policy/standards/asb/rp-controls/microsoft.sql-2-7.md)]

## <a name="identity-and-access-control"></a>Identitás- és hozzáférés-vezérlés

*További információt az [Azure biztonsági teljesítményteszt: Identity and Access Control](../../security/benchmarks/security-control-identity-access-control.md)című témakörben talál.*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3,1: a felügyeleti fiókok leltárának karbantartása

**Útmutató**: Azure Active Directory (Azure ad) beépített szerepkörökkel rendelkezik, amelyeket explicit módon kell hozzárendelni, és lekérdezhető. Az Azure AD PowerShell-modullal ad hoc lekérdezéseket hajthat végre a felügyeleti csoportok tagjait futtató fiókok felderítése érdekében.

- [Címtárbeli szerepkör beszerzése az Azure AD-ben a PowerShell-lel](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0&amp;preserve-view=true)

- [Címtárbeli szerepkör tagjainak beszerzése az Azure AD-ben a PowerShell-lel](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0&amp;preserve-view=true)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="32-change-default-passwords-where-applicable"></a>3,2: az alapértelmezett jelszavak módosítása, ha alkalmazható

**Útmutató**: Azure Active Directory (Azure ad) nem az alapértelmezett jelszavak fogalma. Azure SQL Database-példány kiépítésekor javasoljuk, hogy integrálja az Azure AD-vel való hitelesítést.

- [Azure AD-hitelesítés konfigurálása és kezelése az Azure SQL használatával](/azure/sql-database/azure-sql/database/authentication-aad-configure)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="33-use-dedicated-administrative-accounts"></a>3,3: dedikált rendszergazdai fiókok használata

**Útmutató**: szabályzatok és eljárások létrehozása a dedikált rendszergazdai fiókok használatával. A rendszergazdai fiókok számának figyeléséhez használja a Azure Security Center identitás-és hozzáférés-kezelés lehetőséget.

- [Azure Security Center identitás és hozzáférés ismertetése](../../security-center/security-center-identity-access.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3,5: Multi-Factor Authentication használata az összes Azure Active Directory-alapú hozzáféréshez

**Útmutató**: engedélyezze Azure Active Directory (Azure ad) többtényezős hitelesítését, és kövesse Azure Security Center identitás-és hozzáférés-kezelési javaslatait.

- [Többtényezős hitelesítés engedélyezése az Azure-ban](../../active-directory/authentication/howto-mfa-getstarted.md)

- [Identitás és hozzáférés figyelése Azure Security Centeron belül](../../security-center/security-center-identity-access.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="36-use-secure-azure-managed-workstations-for-administrative-tasks"></a>3,6: biztonságos, Azure által felügyelt munkaállomások használata felügyeleti feladatokhoz

**Útmutató**: az Azure-erőforrások bejelentkezéséhez és konfigurálásához konfigurált, többtényezős hitelesítéssel rendelkező emelt szintű hozzáférési munkaállomás (Paw) használata.

- [További tudnivalók a privilegizált hozzáférésű munkaállomásokról](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

- [Többtényezős hitelesítés engedélyezése az Azure-ban](../../active-directory/authentication/howto-mfa-getstarted.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3,7: naplózás és riasztás a gyanús tevékenységekről a rendszergazdai fiókoktól

**Útmutató**: Azure Active Directory (Azure ad) biztonsági jelentések használata naplók és riasztások generálásához, ha a környezetben gyanús vagy nem biztonságos tevékenység történik.

A Azure SQL Database komplex veszélyforrások elleni védelemmel észleli az adatbázisok eléréséhez vagy kiaknázásához szükséges szokatlan és potenciálisan ártalmas kísérleteket jelző rendellenes tevékenységeket.

- [A kockázatos tevékenységek miatt megjelölt Azure AD-felhasználók azonosítása](/azure/active-directory/reports-monitoring/concept-user-at-risk)

- [A felhasználók identitás-és hozzáférési tevékenységének figyelése Azure Security Center](../../security-center/security-center-identity-access.md)

- [Az összetett veszélyforrások elleni védelem és a lehetséges riasztások áttekintése](https://docs.microsoft.com/azure/azure-sql/database/threat-detection-overview#alerts)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3,8: az Azure-erőforrások kezelése csak jóváhagyott helyekről

**Útmutató**: a feltételes hozzáférés elnevezett helyeivel lehetővé teheti a portál és az Azure erőforrás-kezelési hozzáférését az IP-címtartományok vagy országok/régiók adott logikai csoportjaiból.

- [Elnevezett helyszínek konfigurálása az Azure-ban](../../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="39-use-azure-active-directory"></a>3,9: a Azure Active Directory használata

**Útmutató**: hozzon létre egy Azure Active Directory (Azure ad) rendszergazdát a Azure SQL Database Server-példányokhoz.

- [Azure AD-hitelesítés konfigurálása és kezelése az Azure SQL használatával](authentication-aad-configure.md)

- [Azure AD-példány létrehozása és konfigurálása](../../active-directory-domain-services/tutorial-create-instance.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás**: az [Azure biztonsági teljesítményteszt](/home/mbaldwin/docs/asb/azure-docs-pr/articles/governance/policy/samples/azure-security-benchmark.md) a Security Center alapértelmezett házirend-kezdeményezése, és a [Security Center ajánlásainak](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/security-center-recommendations.md)alapja. A vezérlőhöz kapcsolódó Azure Policy-definíciók Security Center automatikusan engedélyezve vannak. Az ehhez a vezérlőhöz kapcsolódó riasztásokhoz szükség lehet egy [Azure Defender](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/azure-defender.md) -csomagra a kapcsolódó szolgáltatásokhoz.

**Beépített definíciók Azure Policy – Microsoft. SQL**:

[!INCLUDE [Resource Policy for Microsoft.Sql 3.9](../../../includes/policy/standards/asb/rp-controls/microsoft.sql-3-9.md)]

### <a name="310-regularly-review-and-reconcile-user-access"></a>3,10: a felhasználói hozzáférés rendszeres áttekintése és egyeztetése

**Útmutató**: a Azure Active Directory (Azure ad) olyan naplókat biztosít, amelyek segítenek az elavult fiókok felderítésében. Emellett az Azure Identity Access Reviews használatával hatékonyan kezelheti a csoporttagságok kezelését, a vállalati alkalmazásokhoz való hozzáférést és a szerepkör-hozzárendeléseket. A felhasználók hozzáférését rendszeresen áttekintheti, hogy csak a megfelelő felhasználók férhessenek hozzá.

- [Az Azure Identity hozzáférési felülvizsgálatok használata](../../active-directory/governance/access-reviews-overview.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3,11: a figyelő megkísérli a deaktivált hitelesítő adatok elérését

**Útmutató**: Azure Active Directory (Azure ad) hitelesítés konfigurálása az Azure SQL használatával és diagnosztikai beállítások létrehozása az Azure ad felhasználói fiókjaihoz, a naplók és a bejelentkezési naplók elküldése egy log Analytics-munkaterületre. A kívánt riasztások konfigurálása Log Analytics munkaterületen belül.

- [Azure AD-hitelesítés konfigurálása és kezelése az Azure SQL használatával](authentication-aad-configure.md)

- [Azure-tevékenységnaplók integrálása az Azure Monitorba](../../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3,12: riasztás a fiók bejelentkezési viselkedésének eltérése esetén

**Útmutató**: a Azure Active Directory (Azure ad) Identity Protection és kockázati észlelések használatával konfigurálhatja a felhasználói identitásokkal kapcsolatos gyanús műveletekre vonatkozó automatizált válaszokat. Emellett további vizsgálatot is betöltheti az Azure Sentinelbe.

- [Az Azure AD kockázati bejelentkezések megtekintése](/azure/active-directory/reports-monitoring/concept-risky-sign-ins)

- [Az Identity Protection kockázati házirendjeinek konfigurálása és engedélyezése](../../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3,13: a Microsoft számára elérhetővé teszi a megfelelő ügyféladatokat a támogatási forgatókönyvek során

**Útmutató**: azon forgatókönyvek esetében, amelyekben a Microsoftnak hozzá kell férnie az ügyféladatok eléréséhez, az Azure Ügyfélszéf egy felületet biztosít az ügyfelek számára az ügyféladatok hozzáférési kérelmeinek áttekintéséhez és jóváhagyásához vagy elutasításához.

- [A Ügyfélszéf megismerése](../../security/fundamentals/customer-lockbox-overview.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

## <a name="data-protection"></a>Adatvédelem

*További információ: [Azure Security Benchmark: Adatvédelem](../../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4,1: bizalmas információk leltárának fenntartása

**Útmutató**: a címkék használatával segítheti a bizalmas adatokat tároló vagy feldolgozó Azure-erőforrások nyomon követését.

- [Címkék létrehozása és használata](/azure/azure-resource-manager/resource-group-using-tags)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás**: az [Azure biztonsági teljesítményteszt](/home/mbaldwin/docs/asb/azure-docs-pr/articles/governance/policy/samples/azure-security-benchmark.md) a Security Center alapértelmezett házirend-kezdeményezése, és a [Security Center ajánlásainak](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/security-center-recommendations.md)alapja. A vezérlőhöz kapcsolódó Azure Policy-definíciók Security Center automatikusan engedélyezve vannak. Az ehhez a vezérlőhöz kapcsolódó riasztásokhoz szükség lehet egy [Azure Defender](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/azure-defender.md) -csomagra a kapcsolódó szolgáltatásokhoz.

**Beépített definíciók Azure Policy – Microsoft. SQL**:

[!INCLUDE [Resource Policy for Microsoft.Sql 4.1](../../../includes/policy/standards/asb/rp-controls/microsoft.sql-4-1.md)]

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4,2: bizalmas adatok tárolására vagy feldolgozására szolgáló rendszerek elkülönítése

**Útmutató**: különálló előfizetések és/vagy felügyeleti csoportok megvalósítása fejlesztési, tesztelési és éles környezetekhez. Az erőforrásokat vnet/alhálózattal kell elválasztani, megfelelően címkézve, és egy NSG vagy Azure Firewallon belül kell biztosítani. A bizalmas adatokat tároló vagy feldolgozó erőforrások elkülönítését el kell különíteni. Privát hivatkozás használata; Telepítse az Azure SQL Servert a vnet belül, és privát végpontok használatával kapcsolódjon.

- [További Azure-előfizetések létrehozása](/azure/billing/billing-create-subscription)

- [Management Groups létrehozása](/azure/governance/management-groups/create)

- [Címkék létrehozása és használata](/azure/azure-resource-manager/resource-group-using-tags)

- [Privát hivatkozás beállítása Azure SQL Databasehoz](/azure/sql-database/sql-database-private-endpoint-overview#how-to-set-up-private-link-for-azure-sql-database)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4,3: a bizalmas adatok jogosulatlan átvitelének figyelése és letiltása

**Útmutató**: a bizalmas adatok tárolására vagy feldolgozására szolgáló Azure SQL Database adatbázisai esetében az adatbázis és a kapcsolódó erőforrások bizalmasként való megjelölése címkék használatával. Konfigurálja a privát hivatkozást a hálózati biztonsági csoport szolgáltatásbeli címkékkel együtt a Azure SQL Database példányokon a bizalmas adatok kiszűrése megakadályozása érdekében.

A Microsoft által felügyelt mögöttes platform esetében a Microsoft az összes vásárlói tartalmat bizalmasként kezeli, és az ügyfelek adatvesztésével és a kitettséggel szembeni védelem érdekében nagy hosszúságú. Annak biztosítása érdekében, hogy az Azure-beli ügyféladatok biztonságban maradjanak, a Microsoft végrehajtotta és karbantartja a robusztus adatvédelmi szabályozást és képességeket.

- [Privát hivatkozás és NSG konfigurálása az Azure SQL Database-példányokon lévő adatkiszűréseek megelőzésére](/azure/sql-database/sql-database-private-endpoint-overview)

- [Az ügyféladatok Azure-beli védelmének ismertetése](../../security/fundamentals/protection-customer-data.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4,5: aktív felderítési eszköz használata a bizalmas adatok azonosítására

**Útmutató**: használja a Azure SQL Database adatfelderítési és besorolási funkciót. Az adatfelderítés és-besorolás olyan speciális képességeket biztosít, amelyek a Azure SQL Databaseba vannak építve a &amp; bizalmas adatoknak az adatbázisokban való felderítéséhez, besorolásához, címkézéséhez.

- [Adatfelderítés és besorolás használata az Azure SQL Server](/azure/sql-database/sql-database-data-discovery-and-classification)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás**: az [Azure biztonsági teljesítményteszt](/home/mbaldwin/docs/asb/azure-docs-pr/articles/governance/policy/samples/azure-security-benchmark.md) a Security Center alapértelmezett házirend-kezdeményezése, és a [Security Center ajánlásainak](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/security-center-recommendations.md)alapja. A vezérlőhöz kapcsolódó Azure Policy-definíciók Security Center automatikusan engedélyezve vannak. Az ehhez a vezérlőhöz kapcsolódó riasztásokhoz szükség lehet egy [Azure Defender](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/azure-defender.md) -csomagra a kapcsolódó szolgáltatásokhoz.

**Beépített definíciók Azure Policy – Microsoft. SQL**:

[!INCLUDE [Resource Policy for Microsoft.Sql 4.5](../../../includes/policy/standards/asb/rp-controls/microsoft.sql-4-5.md)]

### <a name="46-use-role-based-access-control-to-control-access-to-resources"></a>4,6: szerepköralapú hozzáférés-vezérlés használata az erőforrásokhoz való hozzáférés szabályozásához

**Útmutató**: Azure Active Directory (Azure ad) használata a Azure SQL Database példányokhoz való hozzáférés hitelesítéséhez és szabályozásához.

- [Azure-SQL Server integrálása az Azure AD-vel hitelesítéshez](/azure/sql-database/sql-database-aad-authentication)

- [Hozzáférés vezérlése az Azure SQL Serverban](/azure/sql-database/sql-database-control-access)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="48-encrypt-sensitive-information-at-rest"></a>4,8: bizalmas adatok titkosítása a nyugalmi állapotban

**Útmutató**: a transzparens adattitkosítás (TDE) segít megvédeni Azure SQL Database, az Azure SQL felügyelt példányát és az Azure-adattárházat a rosszindulatú offline tevékenységek fenyegetésével szemben, ha titkosítja az inaktív adatok titkosítását. Valós időben titkosítja és fejti vissza az adatbázist, a hozzá tartozó biztonsági másolatokat és a tranzakciónapló-fájlokat anélkül, hogy ehhez módosítani kellene az alkalmazást. Alapértelmezés szerint a TDE engedélyezve van az összes újonnan telepített adatbázishoz SQL Database és SQL felügyelt példányban. A TDE-titkosítási kulcsot a Microsoft vagy az ügyfél is kezelheti.

- [Transzparens adattitkosítás kezelése és saját titkosítási kulcsok használata](https://docs.microsoft.com/azure/sql-database/transparent-data-encryption-azure-sql?tabs=azure-portal#manage-transparent-data-encryption)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás**: az [Azure biztonsági teljesítményteszt](/home/mbaldwin/docs/asb/azure-docs-pr/articles/governance/policy/samples/azure-security-benchmark.md) a Security Center alapértelmezett házirend-kezdeményezése, és a [Security Center ajánlásainak](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/security-center-recommendations.md)alapja. A vezérlőhöz kapcsolódó Azure Policy-definíciók Security Center automatikusan engedélyezve vannak. Az ehhez a vezérlőhöz kapcsolódó riasztásokhoz szükség lehet egy [Azure Defender](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/azure-defender.md) -csomagra a kapcsolódó szolgáltatásokhoz.

**Beépített definíciók Azure Policy – Microsoft. SQL**:

[!INCLUDE [Resource Policy for Microsoft.Sql 4.8](../../../includes/policy/standards/asb/rp-controls/microsoft.sql-4-8.md)]

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4,9: a kritikus Azure-erőforrások változásainak naplózása és riasztása

**Útmutató**: a Azure monitor és az Azure-tevékenység naplójának használata riasztások létrehozásához, amikor a módosítások a Azure SQL Database és más kritikus vagy kapcsolódó erőforrások éles példányain lépnek életbe.

- [Riasztások létrehozása az Azure-tevékenységek naplózási eseményeihez](/azure/azure-monitor/platform/alerts-activity-log)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

## <a name="vulnerability-management"></a>Biztonságirés-kezelés

*További információért lásd az [Azure biztonsági teljesítményteszt: biztonsági rés kezelése](../../security/benchmarks/security-control-vulnerability-management.md)című témakört.*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5,1: automatikus biztonsági rések vizsgálatára szolgáló eszközök futtatása

**Útmutató**: az Azure DEFENDER for SQL engedélyezése a Azure SQL Databasehoz, és Azure Security Center ajánlásainak követése az Azure SQL-kiszolgálókon a sebezhetőségi felmérések végrehajtásához.

- [Sebezhetőségi felmérések futtatása a Azure SQL Databaseon](/azure/sql-database/sql-vulnerability-assessment)

- [Az Azure Defender for SQL engedélyezése](azure-defender-for-sql.md)

- [Azure Security Center sebezhetőségi felméréssel kapcsolatos javaslatok megvalósítása](/azure/security-center/security-center-vulnerability-assessment-recommendations)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás**: az [Azure biztonsági teljesítményteszt](/home/mbaldwin/docs/asb/azure-docs-pr/articles/governance/policy/samples/azure-security-benchmark.md) a Security Center alapértelmezett házirend-kezdeményezése, és a [Security Center ajánlásainak](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/security-center-recommendations.md)alapja. A vezérlőhöz kapcsolódó Azure Policy-definíciók Security Center automatikusan engedélyezve vannak. Az ehhez a vezérlőhöz kapcsolódó riasztásokhoz szükség lehet egy [Azure Defender](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/azure-defender.md) -csomagra a kapcsolódó szolgáltatásokhoz.

**Beépített definíciók Azure Policy – Microsoft. SQL**:

[!INCLUDE [Resource Policy for Microsoft.Sql 5.1](../../../includes/policy/standards/asb/rp-controls/microsoft.sql-5-1.md)]

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5,4: a biztonsági rések keresésének összehasonlítása

**Útmutató**: a Azure SQL Database példányok rendszeres ismétlődő vizsgálatának engedélyezése; Ezzel beállítja a sebezhetőségi felmérést, hogy hetente egyszer automatikusan futtasson vizsgálatot az adatbázison. A rendszer az Ön által megadott e-mail-címre küldi el a vizsgálat eredményének összegzését. Hasonlítsa össze az eredményeket annak ellenőrzéséhez, hogy a biztonsági rések szervizelése megtörtént-e.

- [Sebezhetőségi felmérési jelentés exportálása Azure Security Center](/azure/sql-database/sql-vulnerability-assessment#implementing-vulnerability-assessment)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5,5: kockázatértékelési folyamat használatával rangsorolhatja a felderített biztonsági rések szervizelését

**Útmutató**: a Azure Security Center által biztosított alapértelmezett kockázati minősítések (biztonságos pontszám) használata.

- [A Azure Security Center biztonsági pontszámának megismerése](/azure/security-center/security-center-secure-score)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás**: az [Azure biztonsági teljesítményteszt](/home/mbaldwin/docs/asb/azure-docs-pr/articles/governance/policy/samples/azure-security-benchmark.md) a Security Center alapértelmezett házirend-kezdeményezése, és a [Security Center ajánlásainak](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/security-center-recommendations.md)alapja. A vezérlőhöz kapcsolódó Azure Policy-definíciók Security Center automatikusan engedélyezve vannak. Az ehhez a vezérlőhöz kapcsolódó riasztásokhoz szükség lehet egy [Azure Defender](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/azure-defender.md) -csomagra a kapcsolódó szolgáltatásokhoz.

**Beépített definíciók Azure Policy – Microsoft. SQL**:

[!INCLUDE [Resource Policy for Microsoft.Sql 5.5](../../../includes/policy/standards/asb/rp-controls/microsoft.sql-5-5.md)]

## <a name="inventory-and-asset-management"></a>Leltár-és eszközfelügyelet

*További információt az [Azure biztonsági teljesítményteszt: leltár és eszközkezelés](../../security/benchmarks/security-control-inventory-asset-management.md)című témakörben talál.*

### <a name="61-use-automated-asset-discovery-solution"></a>6,1: automatikus eszköz-felderítési megoldás használata

**Útmutató**: az Azure Resource Graph használatával lekérdezheti és felderítheti az összes erőforrást (beleértve az Azure SQL Server-példányokat) az előfizetés (ok) n belül. Győződjön meg arról, hogy megfelelő (olvasási) engedélyekkel rendelkezik a bérlőben, és képes felsorolni az összes Azure-előfizetést, valamint az előfizetésében lévő erőforrásokat.

Bár a klasszikus Azure-erőforrások felderítése az erőforrás-gráfon keresztül lehetséges, erősen ajánlott a Azure Resource Manager erőforrások létrehozása és használata.

- [Lekérdezések létrehozása az Azure Resource Graph használatával](../../governance/resource-graph/first-query-portal.md)

- [Azure-előfizetések megtekintése](https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-4.8.0&amp;preserve-view=true)

- [Az Azure RBAC ismertetése](../../role-based-access-control/overview.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="62-maintain-asset-metadata"></a>6,2: az eszköz metaadatainak fenntartása

**Útmutató**: címkéket alkalmazhat az Azure-erőforrásokra, így a metaadatok logikailag rendszerezve lesznek a besorolásban.

- [Címkék létrehozása és használata](/azure/azure-resource-manager/resource-group-using-tags)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="63-delete-unauthorized-azure-resources"></a>6,3: jogosulatlan Azure-erőforrások törlése

**Útmutató**: a címkézés, a felügyeleti csoportok és az elkülönített előfizetések használata, ha szükséges, az eszközök rendszerezéséhez és nyomon követéséhez. Rendszeres időközönként egyeztetheti a leltárt, és gondoskodhat arról, hogy a jogosulatlan erőforrások törlése az előfizetésből időben történjen.

- [További Azure-előfizetések létrehozása](/azure/billing/billing-create-subscription)

- [Management Groups létrehozása](/azure/governance/management-groups/create)

- [Címkék létrehozása és használata](/azure/azure-resource-manager/resource-group-using-tags)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="65-monitor-for-unapproved-azure-resources"></a>6,5: a nem jóváhagyott Azure-erőforrások figyelése

**Útmutató**: a Azure Policy használatával korlátozásokat állíthat be az ügyfél-előfizetésekben létrehozható erőforrások típusára a következő beépített szabályzat-definíciók használatával:

- Nem engedélyezett erőforrástípusok

- Engedélyezett erőforrástípusok

Az Azure Resource Graph használatával lekérdezheti vagy felderítheti az előfizetése (ke) n belüli erőforrásait. Győződjön meg arról, hogy a környezetben lévő összes Azure-erőforrás jóvá van hagyva.

- [Az Azure Policy konfigurálása és kezelése](../../governance/policy/tutorials/create-and-manage.md)

- [Lekérdezések létrehozása az Azure Resource Graph használatával](../../governance/resource-graph/first-query-portal.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="69-use-only-approved-azure-services"></a>6,9: csak jóváhagyott Azure-szolgáltatások használata

**Útmutató**: a Azure Policy használatával korlátozásokat helyezhet el az ügyfél-előfizetésekben létrehozható erőforrások típusára a következő beépített szabályzat-definíciók használatával:
- Nem engedélyezett erőforrástípusok
- Engedélyezett erőforrástípusok

Az Azure Resource Graph használatával lekérdezheti vagy felderítheti az előfizetése (ke) n belüli erőforrásait. Győződjön meg arról, hogy a környezetben lévő összes Azure-erőforrás jóvá van hagyva.

- [Az Azure Policy konfigurálása és kezelése](../../governance/policy/tutorials/create-and-manage.md)

- [Adott erőforrástípus megtagadása a következővel Azure Policy](https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#general)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6,11: korlátozza a felhasználók képességét a Azure Resource Manager való interakcióra

**Útmutató**: az Azure feltételes hozzáférés használatával korlátozhatja a felhasználók a Azure Resource Manager interakcióját a "Microsoft Azure felügyelet" alkalmazás "hozzáférés tiltása" beállításával.

- [A feltételes hozzáférés konfigurálása a Azure Resource Managerhoz való hozzáférés blokkolásához](../../role-based-access-control/conditional-access-azure-management.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

## <a name="secure-configuration"></a>Biztonságos konfiguráció

*További információt az [Azure biztonsági teljesítményteszt: biztonságos konfiguráció](../../security/benchmarks/security-control-secure-configuration.md)című témakörben talál.*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7,1: biztonságos konfigurációk létrehozása az összes Azure-erőforráshoz

**Útmutató**: az Azure SQL Server-kiszolgálókhoz/adatbázisokhoz kapcsolódó Azure Policy-vagy Azure Security Center-javaslatok használata az összes Azure-erőforrás biztonsági konfigurációjának fenntartásához.

- [Az Azure Policy konfigurálása és kezelése](../../governance/policy/tutorials/create-and-manage.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="73-maintain-secure-azure-resource-configurations"></a>7,3: biztonságos Azure-erőforrás-konfigurációk karbantartása

**Útmutató**: az Azure-erőforrások biztonságos beállításainak betartatásához használja a Azure Policy [deny] és a [telepítés ha nem létezik] lehetőséget.

- [Az Azure Policy konfigurálása és kezelése](../../governance/policy/tutorials/create-and-manage.md)

- [Azure Policy effektusok ismertetése](../../governance/policy/concepts/effects.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="75-securely-store-configuration-of-azure-resources"></a>7,5: az Azure-erőforrások biztonságos tárolása

**Útmutató**: ha egyéni Azure Policy-definíciókat használ, az Azure DevOps vagy az Azure Repos segítségével biztonságosan tárolhatja és kezelheti a kódot.

- [Kód tárolása az Azure DevOps](https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops&amp;preserve-view=true)

- [Az Azure Repos dokumentációja](https://docs.microsoft.com/azure/devops/repos/?view=azure-devops&amp;preserve-view=true)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7,7: az Azure-erőforrások konfigurációs felügyeleti eszközeinek üzembe helyezése

**Útmutató**: a "Microsoft. SQL" névtérben található Azure Policy Aliasok használatával egyéni szabályzatokat hozhat létre a riasztáshoz, a naplózáshoz és a rendszerkonfigurációk kényszerítéséhez. Emellett dolgozzon ki egy folyamatot és egy folyamatot a házirend-kivételek kezeléséhez.

- [Az Azure Policy konfigurálása és kezelése](../../governance/policy/tutorials/create-and-manage.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7,9: automatikus konfigurációs monitorozás megvalósítása Azure-erőforrásokhoz

**Útmutató**: a Azure Security Center kihasználása az Azure SQL-kiszolgálók és-adatbázisok alapkonfigurációjának vizsgálatához.

- [Javaslatok szervizelése Azure Security Center](/azure/security-center/security-center-sql-service-recommendations)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="711-manage-azure-secrets-securely"></a>7,11: az Azure-titkok biztonságos kezelése

**Útmutató**: a Azure Key Vault használatával tárolhatja Azure SQL Database TRANSZPARENS ADATTITKOSÍTÁS (TDE) titkosítási kulcsait.

- [Az Azure SQL Server tárolt bizalmas adatok védelme és a titkosítási kulcsok tárolása Azure Key Vault](/azure/sql-database/sql-database-always-encrypted-azure-key-vault)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="712-manage-identities-securely-and-automatically"></a>7,12: az identitások biztonságos és automatikus kezelése

**Útmutató**: felügyelt identitások használata az Azure-szolgáltatások automatikus felügyelt identitással való ellátásához Azure Active Directory (Azure ad). A felügyelt identitások lehetővé teszik bármely olyan szolgáltatás hitelesítését, amely támogatja az Azure AD-hitelesítést, beleértve a Azure Key Vault is, a kódban szereplő hitelesítő adatok nélkül.

- [Oktatóanyag: Hozzáférés az Azure SQL-hez egy Windows VM rendszer által hozzárendelt felügyelt identitásával](../../active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-sql.md)

- [Felügyelt identitások konfigurálása](../../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="713-eliminate-unintended-credential-exposure"></a>7,13: a hitelesítő adatok nem szándékolt expozíciójának megszüntetése

**Útmutató**: hitelesítő adatok beolvasása a kódban a hitelesítő adatok azonosításához. A Credential Scanner a felfedezett hitelesítő adatok biztonságosabb helyre, például az Azure Key Vaultba való áthelyezésére is javaslatot tesz. 

- [A hitelesítő adatok beolvasójának beállítása](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

## <a name="malware-defense"></a>Kártevők elleni védelem

*További információt az [Azure biztonsági teljesítményteszt: kártevők elleni védelem](../../security/benchmarks/security-control-malware-defense.md)című témakörben talál.*

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8,2: a nem számítási Azure-erőforrásokra feltöltött fájlok előzetes vizsgálata

**Útmutató**: a Microsoft kártevő szoftverrel való ellátása engedélyezve van az Azure-szolgáltatásokat támogató mögöttes gazdagépen (például Azure app Service), azonban nem az ügyfél tartalmán fut.

A nem számítási Azure-erőforrásokra feltöltött tartalmak előzetes vizsgálata, például App Service, Data Lake Storage, Blob Storage, Azure SQL Server stb. A Microsoft nem fér hozzá az adataihoz ezekben a példányokban.

- [Megismerheti a Microsoft antimalware-t az Azure Cloud Services és Virtual Machines](../../security/fundamentals/antimalware.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

## <a name="data-recovery"></a>Adat-helyreállítás

*További információt az [Azure biztonsági teljesítményteszt: adat-helyreállítás](../../security/benchmarks/security-control-data-recovery.md)című témakörben talál.*

### <a name="91-ensure-regular-automated-back-ups"></a>9,1: rendszeres automatizált biztonsági másolatok biztosítása

**Útmutató**: a vállalat adatvesztés elleni védelme érdekében a Azure SQL Database automatikusan létrehozza a teljes adatbázis biztonsági másolatait hetente, a különbözeti adatbázis biztonsági másolatait 12 óránként, a tranzakciós napló biztonsági mentése pedig 5-10 percenként történik. A biztonsági másolatok az RA-GRS tárolóban tárolódnak legalább 7 napig az összes szolgáltatási réteg esetében. Az összes szolgáltatási réteg, kivéve a alapszintű támogatás konfigurálható biztonsági mentés megőrzési időtartamát az időponthoz tartozó visszaállításhoz, legfeljebb 35 napig.

A különböző megfelelőségi követelmények kielégítése érdekében a heti, havi és/vagy éves biztonsági mentések esetében eltérő megőrzési időt választhat. A tárterület-használat a biztonsági mentések és a megőrzési időtartam (ok) kiválasztott gyakoriságával függ.

- [A biztonsági másolatok és az üzletmenet-folytonosság megismerése az Azure SQL Server](/azure/sql-database/sql-database-business-continuity)

**Felelősség**: Megosztott

**Azure Security Center monitorozás**: az [Azure biztonsági teljesítményteszt](/home/mbaldwin/docs/asb/azure-docs-pr/articles/governance/policy/samples/azure-security-benchmark.md) a Security Center alapértelmezett házirend-kezdeményezése, és a [Security Center ajánlásainak](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/security-center-recommendations.md)alapja. A vezérlőhöz kapcsolódó Azure Policy-definíciók Security Center automatikusan engedélyezve vannak. Az ehhez a vezérlőhöz kapcsolódó riasztásokhoz szükség lehet egy [Azure Defender](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/azure-defender.md) -csomagra a kapcsolódó szolgáltatásokhoz.

**Beépített definíciók Azure Policy – Microsoft. SQL**:

[!INCLUDE [Resource Policy for Microsoft.Sql 9.1](../../../includes/policy/standards/asb/rp-controls/microsoft.sql-9-1.md)]

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: teljes rendszerbiztonsági mentés és minden ügyfél által felügyelt kulcs biztonsági mentése

**Útmutató**: a Azure SQL Database automatikusan létrehozza az adatbázis 7 és 35 nap közötti biztonsági mentését, és az Azure olvasási hozzáférésű geo-redundáns tárolást (ra-GRS) használ annak érdekében, hogy a rendszer akkor is megőrizzék azokat, ha az adatközpont nem érhető el. Ezek a biztonsági másolatok automatikusan létrejönnek. Ha szükséges, engedélyezze a hosszú távú, földrajzilag redundáns biztonsági mentéseket a Azure SQL Database számára.

Ha transzparens adattitkosítás ügyfél által felügyelt kulcsokat használ, győződjön meg arról, hogy a kulcsok biztonsági mentése folyamatban van.

- [A biztonsági másolatok megismerése az Azure SQL Server](https://docs.microsoft.com/azure/sql-database/sql-database-automated-backups?tabs=single-database)

- [Key Vault-kulcsok biztonsági mentése az Azure-ban](https://docs.microsoft.com/powershell/module/az.keyvault/backup-azkeyvaultkey?view=azps-4.8.0&amp;preserve-view=true)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás**: az [Azure biztonsági teljesítményteszt](/home/mbaldwin/docs/asb/azure-docs-pr/articles/governance/policy/samples/azure-security-benchmark.md) a Security Center alapértelmezett házirend-kezdeményezése, és a [Security Center ajánlásainak](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/security-center-recommendations.md)alapja. A vezérlőhöz kapcsolódó Azure Policy-definíciók Security Center automatikusan engedélyezve vannak. Az ehhez a vezérlőhöz kapcsolódó riasztásokhoz szükség lehet egy [Azure Defender](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/azure-defender.md) -csomagra a kapcsolódó szolgáltatásokhoz.

**Beépített definíciók Azure Policy – Microsoft. SQL**:

[!INCLUDE [Resource Policy for Microsoft.Sql 9.2](../../../includes/policy/standards/asb/rp-controls/microsoft.sql-9-2.md)]

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: az összes biztonsági másolat ellenőrzése, beleértve az ügyfél által felügyelt kulcsokat

**Útmutató**: a Azure Backupon belüli tartalmak adatvisszaállításának rendszeres elvégzése. Ha szükséges, tesztelje a visszaállítási tartalmat egy elkülönített VLAN-ra. Tesztelje az ügyfél által felügyelt kulcsok biztonsági mentésének visszaállítását.

- [Key Vault-kulcsok visszaállítása az Azure-ban](https://docs.microsoft.com/powershell/module/az.keyvault/restore-azkeyvaultkey?view=azps-4.8.0&amp;preserve-view=true)

- [Azure SQL Database biztonsági mentések helyreállítása az időponthoz tartozó visszaállítással](/azure/sql-database/sql-database-recovery-using-backups#point-in-time-restore)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: a biztonsági másolatok és az ügyfél által felügyelt kulcsok védelmének biztosítása

**Útmutató**: a nem kötelező Törlés engedélyezése a Azure Key Vault a kulcsok véletlen vagy rosszindulatú törléssel szembeni védelemmel való ellátásához.

- [A Soft delete engedélyezése Key Vault](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

## <a name="incident-response"></a>Incidensmegoldás

*További információ: [Azure Security Benchmark: Incidensek kezelése](../../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10,1: incidens-válaszi útmutató létrehozása

**Útmutató**: Ellenőrizze, hogy vannak-e írásos incidensekre vonatkozó válaszok, amelyek meghatározzák a személyzet szerepköreit, valamint az incidensek kezelésének és kezelésének fázisait.

- [Munkafolyamat-automatizálás konfigurálása Azure Security Centeron belül](../../security-center/security-center-planning-and-operations-guide.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10,2: incidensek pontozásának és rangsorolási eljárásának létrehozása

**Útmutató**: a Security Center súlyosságot rendel a riasztásokhoz, így könnyebben rangsorolhatja az egyes riasztásokban való részvétel sorrendjét, hogy az erőforrások biztonsága esetén azonnal elérhető legyen. A súlyosság azon alapul, hogy az Security Center milyen mértékben szerepel a riasztás kibocsátásához használt elemzésben vagy elemzésben, valamint a riasztást eredményező tevékenység mögötti rosszindulatú szándékkal.

- [Biztonsági riasztások az Azure Security Centerben](../../security-center/security-center-alerts-overview.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="103-test-security-response-procedures"></a>10,3: biztonsági reagálási eljárások tesztelése

**Útmutató**: a rendszerek incidensek reagálási képességeinek rendszeres tesztelésére szolgáló gyakorlatok végrehajtása. Azonosítsa a gyenge pontokat és réseket, és szükség esetén dolgozza át a tervet.

- [Tekintse meg a NIST kiadványát: útmutató az IT-csomagok és-képességek teszteléséhez, betanításához és edzésprogramjának kidolgozásához](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10,4: biztonsági incidensek elérhetőségének biztosítása és riasztási értesítések konfigurálása biztonsági incidensekhez

**Útmutató**: a Microsoft a biztonsági incidensek elérhetőségi adatait arra használja fel, hogy felvegye Önnel a kapcsolatot, ha a Microsoft Security Response Center (MSRC) felfedi, hogy az adatokat egy törvénytelen vagy jogosulatlan fél is hozzáférte.

- [A Azure Security Center biztonsági kapcsolattartó beállítása](../../security-center/security-center-provide-security-contact-details.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10,5: biztonsági riasztások beépítése az incidensek gyorsreagáló rendszerébe

**Útmutató**: az Azure Security Center-riasztások és-javaslatok exportálása a folyamatos exportálás funkció használatával. A folyamatos exportálás lehetővé teszi a riasztások és javaslatok manuális és folyamatos exportálását. Az Azure Security Center adatösszekötővel továbbíthatja a riasztásokat az Azure Sentinel szolgáltatásba.

- [Folyamatos exportálás konfigurálása](../../security-center/continuous-export.md)

- [Riasztások streamelése az Azure Sentinelbe](../../sentinel/connect-azure-security-center.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="106-automate-the-response-to-security-alerts"></a>10,6: a biztonsági riasztásokra adott válasz automatizálása

**Útmutató**: a Azure Security Center munkafolyamat-automatizálási funkciója segítségével automatikusan aktiválhatja a válaszokat a "Logic apps" használatával a biztonsági riasztások és javaslatok esetében.

- [A Munkafolyamat-automatizálás és a Logic Apps konfigurálása](../../security-center/workflow-automation.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

## <a name="penetration-tests-and-red-team-exercises"></a>Behatolási tesztek és Red Team-gyakorlatok

*További információkért tekintse meg az [Azure biztonsági teljesítményteszt: behatolási tesztek és a Red Team gyakorlatok](../../security/benchmarks/security-control-penetration-tests-red-team-exercises.md)című témakört.*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11,1: az Azure-erőforrások rendszeres behatolásának tesztelése, valamint az összes kritikus biztonsági vizsgálat szervizelésének biztosítása

**Útmutató**: kövesse a Microsoft Cloud penetráció tesztelési szabályait, amelyekkel biztosíthatja, hogy a behatolási tesztek ne sértsék a Microsoft-házirendeket. A Microsoft által felügyelt felhőalapú infrastruktúrán, szolgáltatásokon és alkalmazásokon végzett riasztási és élő behatolási tesztek végrehajtásához használja a Microsoft stratégiáját és végrehajtási tervét. 

- [Behatolástesztelési beavatkozási szabályok](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) 

- [Riasztási tesztek a Microsoft-felhőben](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Felelősség**: Megosztott

**Azure Security Center figyelés**: nincs

## <a name="next-steps"></a>Következő lépések

- [Az Azure Security Benchmark v2 áttekintésének](/azure/security/benchmarks/overview) megtekintése
- További tudnivalók az [Azure biztonsági alapterveiről](/azure/security/benchmarks/security-baselines-overview)
