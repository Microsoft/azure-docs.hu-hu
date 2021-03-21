---
title: Az Azure biztonsági alapterve a szinapszis Analyticshez
description: A szinapszis Analytics biztonsági alapterve eljárási útmutatást és erőforrásokat biztosít az Azure biztonsági Teljesítménytesztben meghatározott biztonsági javaslatok megvalósításához.
author: msmbaldwin
ms.service: synapse-analytics
ms.topic: conceptual
ms.date: 03/16/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 323ddfc7d595bd0d2321660e3b4141444db20518
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "104586855"
---
# <a name="azure-security-baseline-for-synapse-analytics"></a>Az Azure biztonsági alapterve a szinapszis Analyticshez

Ez a biztonsági alapterv az [Azure biztonsági teljesítményteszt 1,0-es verziójáról](../security/benchmarks/overview-v1.md) szóló útmutatót alkalmazza a szinapszis Analytics szolgáltatásra. Az Azure Security Benchmark ajánlásokat ad arra nézve, hogy hogyan tehetők biztonságossá a felhőalapú megoldások az Azure-ban.
A tartalmat az Azure biztonsági teljesítményteszt által meghatározott **biztonsági vezérlők** , valamint a szinapszis Analytics szolgáltatásra vonatkozó kapcsolódó útmutatás szerint csoportosítjuk. A szinapszis Analytics szolgáltatáshoz nem alkalmazható **vezérlők** ki lettek zárva.

 
Ha szeretné megtekinteni, hogy a szinapszis Analytics hogyan teljes mértékben leképezi az Azure biztonsági Teljesítménytesztét, tekintse meg a [teljes szinapszis Analytics biztonsági](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)alapkonfiguráció-hozzárendelés

## <a name="network-security"></a>Hálózati biztonság

*További információ: [Azure Security Benchmark: Hálózati biztonság](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1,1: Azure-erőforrások biztosítása virtuális hálózatokon belül

**Útmutató**: az Azure-SQL Server védelme privát kapcsolaton keresztül virtuális hálózatra. Az Azure Private link lehetővé teszi, hogy a virtuális hálózat privát végpontján keresztül hozzáférjen az Azure Pásti-szolgáltatásokhoz. A virtuális hálózat és a szolgáltatás közötti forgalom a Microsoft gerinc hálózatán halad át.

Azt is megteheti, hogy a szinapszis SQL-készlethez való csatlakozáskor leszűkíti a kimenő kapcsolat hatókörét az SQL-adatbázishoz egy hálózati biztonsági csoport használatával. Az Azure-szolgáltatások kikapcsolásának engedélyezése beállítás megadásával tiltsa le az összes Azure-szolgáltatás forgalmát az SQL-adatbázishoz a nyilvános végponton keresztül. Győződjön meg arról, hogy a tűzfalszabályok nem engedélyezik nyilvános IP-címek beírását.

- [Az Azure privát hivatkozásának megismerése](../private-link/private-link-overview.md)

- [Az Azure szinapszis SQL privát hivatkozásának megismerése](../azure-sql/database/private-endpoint-overview.md)

- [Virtual Network létrehozása](../virtual-network/quick-create-portal.md)

- [Biztonsági konfigurációval rendelkező NSG létrehozása](../virtual-network/tutorial-filter-network-traffic.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás**: az [Azure biztonsági teljesítményteszt](/home/mbaldwin/docs/asb/azure-docs-pr/articles/governance/policy/samples/azure-security-benchmark.md) a Security Center alapértelmezett házirend-kezdeményezése, és a [Security Center ajánlásainak](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/security-center-recommendations.md)alapja. A vezérlőhöz kapcsolódó Azure Policy-definíciók Security Center automatikusan engedélyezve vannak. Az ehhez a vezérlőhöz kapcsolódó riasztásokhoz szükség lehet egy [Azure Defender](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/azure-defender.md) -csomagra a kapcsolódó szolgáltatásokhoz.

**Beépített definíciók Azure Policy – Microsoft. SQL**:

[!INCLUDE [Resource Policy for Microsoft.Sql 1.1](../../includes/policy/standards/asb/rp-controls/microsoft.sql-1-1.md)]

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-network-interfaces"></a>1,2: a virtuális hálózatok, alhálózatok és hálózati adapterek konfigurációjának és forgalmának figyelése és naplózása

**Útmutató**: Ha a dedikált SQL-készlethez csatlakozik, és engedélyezte a hálózati biztonsági csoport (NSG) folyamatábráit, a rendszer a naplókat egy Azure Storage-fiókba továbbítja a forgalom naplózásához.

NSG-naplókat is küldhet egy Log Analytics munkaterületre, és a Traffic Analytics használatával betekintést nyerhet az Azure-Felhőbeli forgalomba. A Traffic Analytics egyes előnyei lehetővé teszi a hálózati tevékenységek megjelenítését és a gyakori pontok azonosítását, a biztonsági fenyegetések azonosítását, a forgalomban rejlő minták értelmezését, valamint a hálózati helytelen konfigurációk meghatározását.

- [A NSG folyamat naplófájljainak engedélyezése](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [A Azure Security Center által biztosított hálózati biztonság ismertetése](../security-center/security-center-network-recommendations.md)

- [A Traffic Analytics engedélyezése és használata](../network-watcher/traffic-analytics.md)

- [A Azure Security Center által biztosított hálózati biztonság ismertetése](../security-center/security-center-network-recommendations.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1,4: az ismert kártevő IP-címekkel folytatott kommunikáció megtagadása

**Útmutató**: az Azure szinapszis SQL fejlett veszélyforrások elleni védelme (ATP) használata. Az ATP olyan rendellenes tevékenységeket észlel, amelyek szokatlan és potenciálisan ártalmas kísérleteket mutatnak az adatbázisok elérésére vagy kiaknázására, és különböző riasztásokat indíthatnak, például a "lehetséges SQL-injektálást" és "a szokatlan helyről való hozzáférést". Az ATP a speciális adatbiztonsági (ADS) ajánlat része, és a központi SQL ADS portálon keresztül érhető el és kezelhető.

Az elosztott szolgáltatásmegtagadási támadások elleni védelem érdekében engedélyezze az Azure szinapszis SQL-hez társított virtuális hálózatokon DDoS Protection szabványt. A Azure Security Center integrált fenyegetési intelligencia használatával megtagadhatja a kommunikációt az ismert kártékony vagy nem használt internetes IP-címekkel.

- [Az Azure szinapszis SQL-hez készült ATP ismertetése](../azure-sql/database/threat-detection-overview.md)

- [A Azure SQL Database speciális adatbiztonságának engedélyezése](../azure-sql/database/azure-defender-for-sql.md)

- [A hirdetések áttekintése](../azure-sql/database/azure-defender-for-sql.md)

- [A DDoS Protection konfigurálása](../ddos-protection/manage-ddos-protection.md)

- [Az Azure Security Center integrált veszélyforrások felderítésének megismerése](../security-center/azure-defender.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="15-record-network-packets"></a>1,5: hálózati csomagok rögzítése

**Útmutató**: Ha a dedikált SQL-készlethez csatlakozik, és engedélyezte a hálózati biztonsági csoport (NSG) folyamatának naplóit, naplókat küldhet egy Azure Storage-fiókba a forgalom naplózásához. A flow-naplókat Log Analytics munkaterületre is küldheti, vagy továbbíthatja őket a Event Hubsba.  Ha a rendellenes tevékenységek kivizsgálásához szükséges, engedélyezze Network Watcher csomagok rögzítését.

- [A NSG folyamat naplófájljainak engedélyezése](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [A Network Watcher engedélyezése](../network-watcher/network-watcher-create.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1,6: hálózati behatolás-észlelési/Behatolás-megelőzési rendszerek (AZONOSÍTÓk/IP-címek) üzembe helyezése

**Útmutató**: az Azure szinapszis SQL fejlett veszélyforrások elleni védelme (ATP) használata. Az ATP olyan rendellenes tevékenységeket észlel, amelyek szokatlan és potenciálisan ártalmas kísérleteket mutatnak az adatbázisok elérésére vagy kiaknázására, és különböző riasztásokat indíthatnak, például a "lehetséges SQL-injektálást" és "a szokatlan helyről való hozzáférést". Az ATP a speciális adatbiztonsági (ADS) ajánlat része, és a központi SQL ADS portálon keresztül érhető el és kezelhető. Az ATP a Azure Security Centerokkal is integrálja a riasztásokat.

- [Az Azure szinapszis SQL-hez készült ATP ismertetése](../azure-sql/database/threat-detection-overview.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1,8: a hálózati biztonsági szabályok bonyolultságának és adminisztratív terhelésének csökkentése

**Útmutató**: hálózati biztonsági csoportok vagy Azure Firewall hálózati hozzáférés-vezérlésének definiálásához használja a virtuális hálózati szolgáltatás címkéit. Biztonsági szabályok létrehozása során szolgáltatáscímkéket használhat bizonyos IP-címek helyett. Ha egy szabály megfelelő forrás vagy cél mezőjében megadja a szolgáltatási címke nevét (például ApiManagement), akkor engedélyezheti vagy megtagadhatja a megfelelő szolgáltatás forgalmát. A Microsoft kezeli a szolgáltatási címke által felölelt címek előtagjait, és automatikusan frissíti a szolgáltatási címkét a címek változásával.

Ha szolgáltatási végpontot használ a dedikált SQL-készlethez, az Azure SQL Database nyilvános IP-címeire kell kitöltenie a következőt: hálózati biztonsági csoportok (NSG-k) megnyitása a kapcsolat engedélyezéséhez Azure SQL Database IP-címekre. Ezt a Azure SQL Database NSG-szolgáltatásának címkéi segítségével teheti meg.

- [A szolgáltatás-címkék ismertetése a Azure SQL Database szolgáltatás-végpontokkal](https://docs.microsoft.com/azure/azure-sql/database/vnet-service-endpoint-rule-overview#limitations)

- [A szolgáltatási címkék megismerése és használata](../virtual-network/service-tags-overview.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1,9: a hálózati eszközök szabványos biztonsági konfigurációinak fenntartása

**Útmutató**: hálózati biztonsági konfigurációk definiálása és implementálása a dedikált SQL-készlethez kapcsolódó erőforrások Azure Policy használatával. A "Microsoft. SQL" névtér használatával egyéni szabályzat-definíciókat határozhat meg, illetve az Azure SQL Database/Server Network Protection szolgáltatáshoz tervezett beépített szabályzat-definíciókat is használhatja. A Azure SQL Database-kiszolgáló megfelelő beépített hálózati biztonsági házirendjének példája a következő: "SQL Server virtuális hálózati szolgáltatás-végpontot használjon".

Az Azure-tervezetek segítségével leegyszerűsítheti a nagyméretű Azure-környezetek nagy léptékű üzembe helyezését, például az Azure Resource Management-sablonok, az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC) és a házirendek használatát egyetlen terv definíciójában. Egyszerűen alkalmazhatja a tervrajzot új előfizetésekre és környezetekre, és részletesen beállíthatja a vezérlést és a felügyeletet a verziószámozással.

- [Az Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md)

- [Azure Blueprint létrehozása](../governance/blueprints/create-blueprint-portal.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="110-document-traffic-configuration-rules"></a>1,10: a dokumentum forgalmának konfigurációs szabályai

**Útmutató**: használja a hálózati biztonsági csoportokhoz (NSG) és a hálózati biztonsággal és a forgalommal kapcsolatos egyéb erőforrásokhoz tartozó címkéket. Az egyes NSG-szabályok esetében a "Leírás" mezővel adhatja meg az üzleti igényeket és/vagy időtartamot (stb.) minden olyan szabályhoz, amely engedélyezi a hálózatra irányuló adatforgalmat.

A címkézéshez kapcsolódó beépített Azure Policy definíciók bármelyikét használhatja, például a "címke és az érték megkövetelése" beállítást, hogy az összes erőforrás címkével legyen létrehozva, és értesítse a meglévő címkézetlen erőforrásokról.

A Azure PowerShell vagy az Azure CLI használatával a címkék alapján kereshet vagy végezhet műveleteket az erőforrásokon.

- [Címkék létrehozása és használata](../azure-resource-manager/management/tag-resources.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1,11: automatikus eszközök használata a hálózati erőforrások konfigurációjának figyelésére és a változások észlelésére

**Útmutató**: az Azure-Tevékenységnaplók használatával figyelheti a hálózati erőforrás-konfigurációkat, és azonosíthatja a dedikált SQL-készlethez kapcsolódó hálózati erőforrások változásait. Hozzon létre riasztásokat Azure Monitoron belül, amelyek akkor lépnek életbe, amikor a kritikus hálózati erőforrásokra vonatkozó módosításokat végrehajtják

- [Azure-Tevékenységnaplók eseményeinek megtekintése és lekérése](https://docs.microsoft.com/azure/azure-monitor/essentials/activity-log#view-the-activity-log)

- [Riasztások létrehozása a Azure Monitorban](../azure-monitor/alerts/alerts-activity-log.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

## <a name="logging-and-monitoring"></a>Naplózás és monitorozás

*További információt az [Azure biztonsági teljesítményteszt: naplózás és figyelés](../security/benchmarks/security-control-logging-monitoring.md)című témakörben talál.*

### <a name="22-configure-central-security-log-management"></a>2,2: a központi biztonsági naplók felügyeletének konfigurálása

**Útmutató**: a naplózási szabályzatok meghatározhatók egy adott adatbázishoz, vagy az Azure-ban alapértelmezett kiszolgálói házirendként (amely az Azure szinapszist tárolja). A kiszolgálói házirend a kiszolgálón lévő összes meglévő és újonnan létrehozott adatbázisra vonatkozik.

Ha a kiszolgáló naplózása engedélyezve van, az mindig az adatbázisra vonatkozik. A rendszer naplózza az adatbázist, az adatbázis naplózási beállításaitól függetlenül.

Ha engedélyezi a naplózást, megírhatja őket az Azure Storage-fiókjában, Log Analytics munkaterületen vagy Event Hubs.

Alternatív megoldásként engedélyezheti és elvégezheti az Azure Sentinel vagy egy harmadik féltől származó SIEM-et.

- [Az Azure SQL-erőforrások naplózásának beállítása](https://docs.microsoft.com/azure/azure-sql/database/auditing-overview#server-vs-database-level)

- [Az Azure Sentinel előkészítése](../sentinel/quickstart-onboard.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="23-enable-audit-logging-for-azure-resources"></a>2,3: az Azure-erőforrások naplózásának engedélyezése

**Útmutató**: engedélyezze a naplózást az Azure SQL Server-szinten a dedikált SQL-készlethez, és válasszon egy tárolási helyet a naplók számára (Azure storage, Log Analytics vagy Event Hubs).

A naplózás az adatbázison vagy a kiszolgáló szintjén is engedélyezhető, és azt javasoljuk, hogy csak a kiszolgáló szintjén engedélyezzen, csak akkor, ha külön adatfogadót vagy adatmegőrzést kíván konfigurálni egy adott adatbázis számára.

- [A Azure SQL Database naplózásának engedélyezése](../azure-sql/database/auditing-overview.md)

- [A kiszolgáló naplózásának engedélyezése](https://docs.microsoft.com/azure/azure-sql/database/auditing-overview#setup-auditing)

- [Különbségek a kiszolgálói szintű és az adatbázis szintű naplózási házirendek között](https://docs.microsoft.com/azure/azure-sql/database/auditing-overview#server-vs-database-level)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás**: az [Azure biztonsági teljesítményteszt](/home/mbaldwin/docs/asb/azure-docs-pr/articles/governance/policy/samples/azure-security-benchmark.md) a Security Center alapértelmezett házirend-kezdeményezése, és a [Security Center ajánlásainak](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/security-center-recommendations.md)alapja. A vezérlőhöz kapcsolódó Azure Policy-definíciók Security Center automatikusan engedélyezve vannak. Az ehhez a vezérlőhöz kapcsolódó riasztásokhoz szükség lehet egy [Azure Defender](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/azure-defender.md) -csomagra a kapcsolódó szolgáltatásokhoz.

**Beépített definíciók Azure Policy – Microsoft. SQL**:

[!INCLUDE [Resource Policy for Microsoft.Sql 2.3](../../includes/policy/standards/asb/rp-controls/microsoft.sql-2-3.md)]

### <a name="25-configure-security-log-storage-retention"></a>2,5: a biztonsági napló tárolási adatmegőrzésének konfigurálása

**Útmutató**: a dedikált SQL-készlethez kapcsolódó naplók tárolása egy Storage-fiókban, log Analytics-munkaterületen vagy az Event hub-ban a szervezet megfelelőségi szabályainak megfelelően állítsa be a napló megőrzési időszakát.

- [Az Azure Blob Storage-életciklus felügyelete](../storage/blobs/storage-lifecycle-management-concepts.md)

- [Naplózási adatmegőrzési paraméterek beállítása Log Analytics munkaterületen](https://docs.microsoft.com/azure/azure-monitor/logs/manage-cost-storage#change-the-data-retention-period)

- [Folyamatos átviteli események rögzítése Event Hubs](../event-hubs/event-hubs-capture-overview.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás**: az [Azure biztonsági teljesítményteszt](/home/mbaldwin/docs/asb/azure-docs-pr/articles/governance/policy/samples/azure-security-benchmark.md) a Security Center alapértelmezett házirend-kezdeményezése, és a [Security Center ajánlásainak](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/security-center-recommendations.md)alapja. A vezérlőhöz kapcsolódó Azure Policy-definíciók Security Center automatikusan engedélyezve vannak. Az ehhez a vezérlőhöz kapcsolódó riasztásokhoz szükség lehet egy [Azure Defender](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/azure-defender.md) -csomagra a kapcsolódó szolgáltatásokhoz.

**Beépített definíciók Azure Policy – Microsoft. SQL**:

[!INCLUDE [Resource Policy for Microsoft.Sql 2.5](../../includes/policy/standards/asb/rp-controls/microsoft.sql-2-5.md)]

### <a name="26-monitor-and-review-logs"></a>2,6: naplók figyelése és áttekintése

**Útmutató**: a rendellenes viselkedésre vonatkozó naplók elemzése és figyelése, valamint az eredmények rendszeres áttekintése. Az összetett veszélyforrások elleni védelem használata a Azure SQL Database Azure Security Center az SQL-adatbázissal kapcsolatos szokatlan tevékenységekre vonatkozó riasztások esetén. Azt is megteheti, hogy a riasztásokat metrikus értékeken vagy az SQL-adatbázishoz kapcsolódó Azure-tevékenységi naplóbejegyzések alapján konfigurálja.

Alternatív megoldásként engedélyezheti és elvégezheti az Azure Sentinel vagy egy harmadik féltől származó SIEM-et.

- [Az összetett veszélyforrások elleni védelem és a riasztások megismerése Azure SQL Database](../azure-sql/database/threat-detection-overview.md)

- [A Azure SQL Database speciális adatbiztonságának engedélyezése](../azure-sql/database/azure-defender-for-sql.md)

- [Egyéni riasztások konfigurálása Azure SQL Databasehoz](../azure-sql/database/alerts-insights-configure-portal.md)

- [Az Azure Sentinel előkészítése](../sentinel/quickstart-onboard.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="27-enable-alerts-for-anomalous-activities"></a>2,7: riasztások engedélyezése rendellenes tevékenységekhez

**Útmutató**: a komplex veszélyforrások elleni védelem (ATP) használata a rendellenes tevékenység figyelésére és riasztására Azure Security Centerekkel együtt Azure SQL Database. Az ATP a speciális adatbiztonsági (ADS) ajánlat része, amely a portálon elérhető központi SQL-hirdetések használatával érhető el és kezelhető. A hirdetések olyan funkciókat foglalnak magukban, mint a bizalmas adatok felderítése és besorolása, a felszínek és a lehetséges adatbázis-sebezhetőségek enyhítése, valamint a rendellenes tevékenységek észlelése, amelyek veszélyeztethetik az adatbázisát.

Alternatív megoldásként engedélyezheti és elvégezheti a fedélzeti adatfeldolgozást az Azure Sentinel szolgáltatásban.

- [Az összetett veszélyforrások elleni védelem és a riasztások megismerése Azure SQL Database](../azure-sql/database/threat-detection-overview.md)

- [A Azure SQL Database speciális adatbiztonságának engedélyezése](../azure-sql/database/azure-defender-for-sql.md)

- [Riasztások kezelése Azure Security Centerban](../security-center/security-center-managing-and-responding-alerts.md)

- [Az Azure Sentinel előkészítése](../sentinel/quickstart-onboard.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás**: az [Azure biztonsági teljesítményteszt](/home/mbaldwin/docs/asb/azure-docs-pr/articles/governance/policy/samples/azure-security-benchmark.md) a Security Center alapértelmezett házirend-kezdeményezése, és a [Security Center ajánlásainak](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/security-center-recommendations.md)alapja. A vezérlőhöz kapcsolódó Azure Policy-definíciók Security Center automatikusan engedélyezve vannak. Az ehhez a vezérlőhöz kapcsolódó riasztásokhoz szükség lehet egy [Azure Defender](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/azure-defender.md) -csomagra a kapcsolódó szolgáltatásokhoz.

**Beépített definíciók Azure Policy – Microsoft. SQL**:

[!INCLUDE [Resource Policy for Microsoft.Sql 2.7](../../includes/policy/standards/asb/rp-controls/microsoft.sql-2-7.md)]

## <a name="identity-and-access-control"></a>Identitás- és hozzáférés-vezérlés

*További információt az [Azure biztonsági teljesítményteszt: Identity and Access Control](../security/benchmarks/security-control-identity-access-control.md)című témakörben talál.*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3,1: a felügyeleti fiókok leltárának karbantartása

**Útmutató**: a felhasználók hitelesítése Azure Active Directory (Azure ad) vagy SQL-hitelesítéssel történik.

Amikor először telepíti az Azure SQL-t, a bejelentkezéshez meg kell adnia egy rendszergazdai bejelentkezési azonosítót és egy hozzá tartozó jelszót. A rendszergazdai fiók neve kiszolgáló-rendszergazda. Az adatbázisok rendszergazdai fiókjainak azonosításához nyissa meg a Azure Portal, és navigáljon a kiszolgáló vagy a felügyelt példány tulajdonságok lapjára. A teljes rendszergazdai engedélyekkel rendelkező Azure AD-rendszergazdai fiókot is konfigurálhatja, ha engedélyezni szeretné az Azure AD-hitelesítést.

Felügyeleti műveletek esetén használja az Azure beépített szerepköreit, amelyeket explicit módon hozzá kell rendelni. Az Azure AD PowerShell-modullal ad hoc lekérdezéseket hajthat végre a felügyeleti csoportok tagjait futtató fiókok felderítése érdekében.

- [SQL Database hitelesítése](https://docs.microsoft.com/azure/azure-sql/database/security-overview#authentication)

- [Fiókok létrehozása nem rendszergazda felhasználók számára](https://docs.microsoft.com/azure/azure-sql/database/logins-create-manage#create-accounts-for-non-administrator-users)

- [Azure AD-fiók használata a hitelesítéshez](https://docs.microsoft.com/azure/azure-sql/database/logins-create-manage#create-additional-logins-and-users-having-administrative-permissions)

- [Címtárbeli szerepkör beszerzése az Azure AD-ben a PowerShell-lel](/powershell/module/azuread/get-azureaddirectoryrole)

- [Címtárbeli szerepkör tagjainak beszerzése az Azure AD-ben a PowerShell-lel](/powershell/module/azuread/get-azureaddirectoryrolemember)

- [Meglévő bejelentkezések és rendszergazdai fiókok kezelése az Azure SQL-ben](https://docs.microsoft.com/azure/azure-sql/database/logins-create-manage#existing-logins-and-user-accounts-after-creating-a-new-database)

- [Beépített Azure-szerepkörök](../role-based-access-control/built-in-roles.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="32-change-default-passwords-where-applicable"></a>3,2: az alapértelmezett jelszavak módosítása, ha alkalmazható

**Útmutató**: Azure Active Directory (Azure ad) nem az alapértelmezett jelszavak fogalma. Dedikált SQL-készlet kiépítésekor javasolt a hitelesítés integrálása az Azure AD-vel. Ezzel a hitelesítési módszerrel a felhasználó elküld egy felhasználói fióknevet és kéréseket, hogy a szolgáltatás az Azure AD-ben tárolt hitelesítő adatokat használja.

- [Azure AD-hitelesítés konfigurálása és kezelése az Azure SQL használatával](../azure-sql/database/authentication-aad-configure.md)

- [Az Azure SQL-alapú hitelesítés ismertetése](https://docs.microsoft.com/azure/azure-sql/database/logins-create-manage#existing-logins-and-user-accounts-after-creating-a-new-database)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="33-use-dedicated-administrative-accounts"></a>3,3: dedikált rendszergazdai fiókok használata

**Útmutató**: szabályzatok és eljárások létrehozása a dedikált rendszergazdai fiókok használatával. Azure Security Center identitás-és hozzáférés-kezelési szolgáltatással figyelheti a Azure Active Directory (Azure AD) használatával bejelentkezett rendszergazdai fiókok számát.

Az adatbázishoz tartozó rendszergazdai fiókok azonosításához nyissa meg a Azure Portal, és keresse meg a kiszolgáló vagy a felügyelt példány Tulajdonságok lapját.

- [Azure Security Center identitás és hozzáférés ismertetése](../security-center/security-center-identity-access.md)

- [Meglévő bejelentkezések és rendszergazdai fiókok kezelése az Azure SQL-ben](https://docs.microsoft.com/azure/azure-sql/database/logins-create-manage#existing-logins-and-user-accounts-after-creating-a-new-database)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="34-use-azure-active-directory-single-sign-on-sso"></a>3,4: Azure Active Directory egyszeri bejelentkezés (SSO) használata

**Útmutató**: az Azure-alkalmazás regisztrálása (egyszerű szolgáltatásnév) használatával lekérheti az adattárházat a vezérlési síkon (Azure Portal) API-hívásokon keresztül felhasználható jogkivonatot.

- [Az Azure REST API-k meghívása](/rest/api/azure/#how-to-call-azure-rest-apis-with-postman)

- [Az ügyfélalkalmazás (egyszerű szolgáltatásnév) regisztrálása Azure Active Directory (Azure AD-vel)](/rest/api/azure/#register-your-client-application-with-azure-ad)

- [Azure szinapszis SQL REST API információk](sql-data-warehouse/sql-data-warehouse-manage-compute-rest-api.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3,5: Multi-Factor Authentication használata az összes Azure Active Directory-alapú hozzáféréshez

**Útmutató**: engedélyezze Azure Active Directory (Azure ad) többtényezős hitelesítését, és kövesse Azure Security Center identitás-és hozzáférés-kezelési javaslatait.

- [Többtényezős hitelesítés engedélyezése az Azure-ban](../active-directory/authentication/howto-mfa-getstarted.md)

- [Identitás és hozzáférés figyelése Azure Security Centeron belül](../security-center/security-center-identity-access.md)

- [Többtényezős hitelesítés megismerése az Azure SQL-ben](../azure-sql/database/authentication-mfa-ssms-overview.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="36-use-secure-azure-managed-workstations-for-administrative-tasks"></a>3,6: biztonságos, Azure által felügyelt munkaállomások használata felügyeleti feladatokhoz

**Útmutató**: az Azure-erőforrások bejelentkezéséhez és konfigurálásához konfigurált, többtényezős hitelesítéssel rendelkező emelt szintű hozzáférési munkaállomás (Paw) használata.

- [További tudnivalók a privilegizált hozzáférésű munkaállomásokról](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

- [Többtényezős hitelesítés engedélyezése az Azure-ban](../active-directory/authentication/howto-mfa-getstarted.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3,7: naplózás és riasztás a gyanús tevékenységekről a rendszergazdai fiókoktól

**Útmutató**: Azure Active Directory (Azure ad) biztonsági jelentések használata naplók és riasztások generálásához, ha a környezetben gyanús vagy nem biztonságos tevékenység történik.

A komplex veszélyforrások elleni védelem használata a Azure SQL Databasehoz együtt a Azure Security Center használatával észleli és figyelmezteti az olyan rendellenes tevékenységeket, amelyek szokatlan és potenciálisan ártalmas kísérleteket jeleznek az adatbázisok eléréséhez vagy kiaknázásához.

SQL Server naplózással kiszolgálói naplózásokat hozhat létre, amelyek tartalmazhatják a kiszolgálói naplózási specifikációkat a kiszolgálói szintű eseményekhez, valamint az adatbázis-szintű események adatbázis-naplózási specifikációit. A naplózott események az eseménynaplóba vagy a fájlok naplózására is írhatók.

- [A kockázatos tevékenységek miatt megjelölt Azure AD-felhasználók azonosítása](../active-directory/identity-protection/overview-identity-protection.md)

- [A felhasználók identitás-és hozzáférési tevékenységének figyelése Azure Security Center](../security-center/security-center-identity-access.md)

- [Az összetett veszélyforrások elleni védelem és a lehetséges riasztások áttekintése](https://docs.microsoft.com/azure/azure-sql/database/threat-detection-overview#alerts)

- [A bejelentkezések és a felhasználói fiókok megismerése az Azure SQL-ben](../azure-sql/database/logins-create-manage.md)

- [SQL Server naplózás ismertetése](/sql/relational-databases/security/auditing/sql-server-audit-database-engine)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3,8: az Azure-erőforrások kezelése csak jóváhagyott helyekről

**Útmutató**: a feltételes hozzáférés elnevezett helyeivel lehetővé teheti a portál és az Azure erőforrás-kezelési hozzáférését az IP-címtartományok vagy országok/régiók adott logikai csoportjaiból.

- [Elnevezett helyszínek konfigurálása az Azure-ban](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="39-use-azure-active-directory"></a>3,9: a Azure Active Directory használata

**Útmutató**: hozzon létre egy Azure Active Directory (Azure ad) rendszergazdát a dedikált SQL-készletben található Azure SQL Database-kiszolgálóhoz.

- [Azure AD-hitelesítés konfigurálása és kezelése az Azure SQL használatával](../azure-sql/database/authentication-aad-configure.md)

- [Azure AD-példány létrehozása és konfigurálása](../active-directory-domain-services/tutorial-create-instance.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás**: az [Azure biztonsági teljesítményteszt](/home/mbaldwin/docs/asb/azure-docs-pr/articles/governance/policy/samples/azure-security-benchmark.md) a Security Center alapértelmezett házirend-kezdeményezése, és a [Security Center ajánlásainak](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/security-center-recommendations.md)alapja. A vezérlőhöz kapcsolódó Azure Policy-definíciók Security Center automatikusan engedélyezve vannak. Az ehhez a vezérlőhöz kapcsolódó riasztásokhoz szükség lehet egy [Azure Defender](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/azure-defender.md) -csomagra a kapcsolódó szolgáltatásokhoz.

**Beépített definíciók Azure Policy – Microsoft. SQL**:

[!INCLUDE [Resource Policy for Microsoft.Sql 3.9](../../includes/policy/standards/asb/rp-controls/microsoft.sql-3-9.md)]

### <a name="310-regularly-review-and-reconcile-user-access"></a>3,10: a felhasználói hozzáférés rendszeres áttekintése és egyeztetése

**Útmutató**: a Azure Active Directory (Azure ad) olyan naplókat biztosít, amelyek segítenek az elavult fiókok felderítésében. Emellett az Azure AD hozzáférési felülvizsgálatok segítségével hatékonyan kezelheti a csoporttagságok kezelését, a vállalati alkalmazásokhoz való hozzáférést és a szerepkör-hozzárendeléseket. A felhasználók hozzáférését rendszeresen áttekintheti, hogy csak a megfelelő felhasználók férhessenek hozzá.

SQL-hitelesítés használatakor hozzon létre tárolt adatbázis-felhasználókat az adatbázisban. Ügyeljen arra, hogy egy vagy több adatbázis-felhasználót egy egyéni adatbázis-szerepkörbe helyezzen, amely az adott felhasználói csoportnak megfelelő engedélyekkel rendelkezik.

- [A hozzáférési felülvizsgálatok használata](../active-directory/governance/access-reviews-overview.md)

- [A bejelentkezések és a felhasználói fiókok megismerése az Azure SQL-ben](../azure-sql/database/logins-create-manage.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3,11: a figyelő megkísérli a deaktivált hitelesítő adatok elérését

**Útmutató**: Azure Active Directory (Azure ad) hitelesítés konfigurálása az Azure SQL használatával és az Azure ad felhasználói fiókjainak diagnosztikai beállításainak engedélyezése, a naplók és a bejelentkezési naplók elküldése egy log Analytics-munkaterületre. A kívánt riasztások konfigurálása Log Analyticson belül.

SQL-hitelesítés használatakor hozzon létre tárolt adatbázis-felhasználókat az adatbázisban. Ügyeljen arra, hogy egy vagy több adatbázis-felhasználót egy egyéni adatbázis-szerepkörbe helyezzen, amely az adott felhasználói csoportnak megfelelő engedélyekkel rendelkezik.

- [A hozzáférési felülvizsgálatok használata](../active-directory/governance/access-reviews-overview.md)

- [Azure AD-hitelesítés konfigurálása és kezelése a Azure SQL Database használatával](../azure-sql/database/authentication-aad-configure.md)

- [Azure-tevékenységnaplók integrálása az Azure Monitorba](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

- [A bejelentkezések és a felhasználói fiókok megismerése az Azure SQL-ben](../azure-sql/database/logins-create-manage.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3,12: riasztás a fiók bejelentkezési viselkedésének eltérése esetén

**Útmutató**: az Azure Active Directory (Azure ad) Identity Protection és a kockázati észlelési funkciók használatával konfigurálhatja a felhasználói identitásokkal kapcsolatos gyanús műveletekre vonatkozó automatizált válaszokat. Emellett a további vizsgálat érdekében a fedélzeti és az Azure Sentinelbe való betöltés is szükséges.

SQL-hitelesítés használatakor hozzon létre tárolt adatbázis-felhasználókat az adatbázisban. Ügyeljen arra, hogy egy vagy több adatbázis-felhasználót egy egyéni adatbázis-szerepkörbe helyezzen, amely az adott felhasználói csoportnak megfelelő engedélyekkel rendelkezik.

- [Az Azure AD kockázati bejelentkezések megtekintése](../active-directory/identity-protection/overview-identity-protection.md)

- [Az Identity Protection kockázati házirendjeinek konfigurálása és engedélyezése](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [A fedélzeti Azure Sentinel ismertetése](../sentinel/connect-data-sources.md)

- [A bejelentkezések és a felhasználói fiókok megismerése az Azure SQL-ben](../azure-sql/database/logins-create-manage.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3,13: a Microsoft számára elérhetővé teszi a megfelelő ügyféladatokat a támogatási forgatókönyvek során

**Útmutató**: olyan forgatókönyvek esetén, ahol a Microsoftnak a dedikált SQL-készlet Azure SQL Database kapcsolódó adatokhoz kell hozzáférnie, az Azure Ügyfélszéf felületet biztosít az adatelérési kérelmek áttekintéséhez és jóváhagyásához vagy elutasításához.

- [A Ügyfélszéf megismerése](../security/fundamentals/customer-lockbox-overview.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

## <a name="data-protection"></a>Adatvédelem

*További információ: [Azure Security Benchmark: Adatvédelem](../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4,1: bizalmas információk leltárának fenntartása

**Útmutató**: a címkék használatával segítheti a bizalmas adatokat tároló vagy feldolgozó Azure-erőforrások nyomon követését.

Az adatfelderítési &amp; besorolás az Azure SZINAPSZIS SQL-be van építve. Magas szintű képességeket biztosít az adatbázisban lévő bizalmas adatok felderítéséhez, osztályozásához, címkézéséhez és jelentéséhez.

- [Címkék létrehozása és használata](../azure-resource-manager/management/tag-resources.md)

- [Az adatfelderítési &amp; besorolás ismertetése](../azure-sql/database/data-discovery-and-classification-overview.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás**: az [Azure biztonsági teljesítményteszt](/home/mbaldwin/docs/asb/azure-docs-pr/articles/governance/policy/samples/azure-security-benchmark.md) a Security Center alapértelmezett házirend-kezdeményezése, és a [Security Center ajánlásainak](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/security-center-recommendations.md)alapja. A vezérlőhöz kapcsolódó Azure Policy-definíciók Security Center automatikusan engedélyezve vannak. Az ehhez a vezérlőhöz kapcsolódó riasztásokhoz szükség lehet egy [Azure Defender](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/azure-defender.md) -csomagra a kapcsolódó szolgáltatásokhoz.

**Beépített definíciók Azure Policy – Microsoft. SQL**:

[!INCLUDE [Resource Policy for Microsoft.Sql 4.1](../../includes/policy/standards/asb/rp-controls/microsoft.sql-4-1.md)]

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4,2: bizalmas adatok tárolására vagy feldolgozására szolgáló rendszerek elkülönítése

**Útmutató**: különálló előfizetések és/vagy felügyeleti csoportok megvalósítása fejlesztési, tesztelési és éles környezetekhez. Az erőforrásokat virtuális hálózattal/alhálózattal kell elválasztani, megfelelően címkézve, és egy hálózati biztonsági csoporton vagy Azure Firewallon belül kell biztosítani. A bizalmas adatokat tároló vagy feldolgozó erőforrások elkülönítését el kell különíteni. Privát hivatkozás használata; Azure-SQL Server üzembe helyezése virtuális hálózaton belül, és biztonságos kapcsolódás a privát kapcsolat használatával.

- [További Azure-előfizetések létrehozása](../cost-management-billing/manage/create-subscription.md)

- [Management Groups létrehozása](../governance/management-groups/create-management-group-portal.md)

- [Címkék létrehozása és használata](../azure-resource-manager/management/tag-resources.md)

- [Privát hivatkozás beállítása Azure SQL Databasehoz](../azure-sql/database/private-endpoint-overview.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4,3: a bizalmas adatok jogosulatlan átvitelének figyelése és letiltása

**Útmutató**: a dedikált SQL-készletben található, bizalmas adatok tárolására vagy feldolgozására szolgáló Azure SQL Database esetén az adatbázis és a kapcsolódó erőforrások bizalmasként való megjelölése címkék használatával. Konfigurálja a privát hivatkozást a hálózati biztonsági csoport (NSG) szolgáltatásbeli címkékkel a Azure SQL Database példányokon, hogy megakadályozza a bizalmas adatok kiszűrése.

Emellett a Azure SQL Database komplex veszélyforrások elleni védelme, az Azure SQL felügyelt példánya és az Azure szinapszis rendellenes tevékenységeket észlel, amelyekben szokatlan és potenciálisan ártalmas próbálkozások érhetők el az adatbázisok eléréséhez vagy kiaknázásához.

A Microsoft által felügyelt mögöttes platform esetében a Microsoft az összes vásárlói tartalmat bizalmasként kezeli, és az ügyfelek adatvesztésével és a kitettséggel szembeni védelem érdekében nagy hosszúságú. Annak biztosítása érdekében, hogy az Azure-beli ügyféladatok biztonságban maradjanak, a Microsoft végrehajtotta és karbantartja a robusztus adatvédelmi szabályozást és képességeket.

- [Privát hivatkozás és NSG konfigurálása az Azure SQL Database-példányokon lévő adatkiszűréseek megelőzésére](../azure-sql/database/private-endpoint-overview.md)

- [A Azure SQL Database komplex veszélyforrások elleni védelmének megismerése](../azure-sql/database/threat-detection-overview.md)

- [Az ügyféladatok Azure-beli védelmének ismertetése](../security/fundamentals/protection-customer-data.md)

**Felelősség**: Megosztott

**Azure Security Center figyelés**: nincs

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4,5: aktív felderítési eszköz használata a bizalmas adatok azonosítására

**Útmutató**: az Azure szinapszis SQL adatfelderítési &amp; besorolási funkciójának használata. Az adatfelderítési &amp; besorolás olyan speciális képességeket biztosít, amelyek a Azure SQL Databaseba vannak építve a &amp; bizalmas adatoknak az adatbázisokban való felderítéséhez, besorolásához, címkézéséhez.

Az adatfelderítési &amp; besorolás a speciális adatbiztonsági ajánlat részét képezi, amely a speciális SQL-alapú biztonsági funkciók egységes csomagja. Az adatfelderítési &amp; besorolás a központi SQL ADS portálon keresztül érhető el és kezelhető.

Emellett beállíthat egy dinamikus adatmaszkolási (DDM-) szabályzatot is a Azure Portal. A DDM-javaslatok motorja az adatbázis bizonyos mezőit olyan érzékeny mezőként adja meg, amely a maszkoláshoz jó választás lehet.

- [Adatfelderítés és besorolás használata az Azure SQL Server](../azure-sql/database/data-discovery-and-classification-overview.md)

- [Az Azure szinapszis SQL dinamikus adatmaszkolási szolgáltatásának ismertetése](../azure-sql/database/dynamic-data-masking-overview.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás**: az [Azure biztonsági teljesítményteszt](/home/mbaldwin/docs/asb/azure-docs-pr/articles/governance/policy/samples/azure-security-benchmark.md) a Security Center alapértelmezett házirend-kezdeményezése, és a [Security Center ajánlásainak](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/security-center-recommendations.md)alapja. A vezérlőhöz kapcsolódó Azure Policy-definíciók Security Center automatikusan engedélyezve vannak. Az ehhez a vezérlőhöz kapcsolódó riasztásokhoz szükség lehet egy [Azure Defender](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/azure-defender.md) -csomagra a kapcsolódó szolgáltatásokhoz.

**Beépített definíciók Azure Policy – Microsoft. SQL**:

[!INCLUDE [Resource Policy for Microsoft.Sql 4.5](../../includes/policy/standards/asb/rp-controls/microsoft.sql-4-5.md)]

### <a name="46-use-azure-rbac-access-control-to-control-access-to-resources"></a>4,6: az Azure RBAC hozzáférés-vezérlés használata az erőforrásokhoz való hozzáférés szabályozására 

**Útmutató**: az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC) használatával kezelheti az Azure SQL Database-adatbázisokhoz való hozzáférést a dedikált SQL-készletben.

Az engedélyezést a felhasználói fiók adatbázis-szerepköri tagsága és az objektum szintű engedélyek vezérlik. Ajánlott eljárásként csak a minimálisan szükséges engedélyeket adja meg a felhasználóknak.

- [Az Azure SQL Server és az Azure Active Directory (Azure AD) integrálása hitelesítésre](../azure-sql/database/authentication-aad-overview.md)

- [Hozzáférés vezérlése az Azure SQL Serverban](../azure-sql/database/logins-create-manage.md)

- [Az engedélyezés és a hitelesítés megismerése az Azure SQL-ben](../azure-sql/database/logins-create-manage.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="48-encrypt-sensitive-information-at-rest"></a>4,8: bizalmas adatok titkosítása a nyugalmi állapotban

**Útmutató**: a transzparens adattitkosítás (TDE) segít megvédeni az Azure szinapszis SQL-et a rosszindulatú offline tevékenységek fenyegetésével szemben, ha titkosítja az inaktív adatok mennyiségét. Valós időben titkosítja és fejti vissza az adatbázist, a hozzá tartozó biztonsági másolatokat és a tranzakciónapló-fájlokat anélkül, hogy ehhez módosítani kellene az alkalmazást. Az Azure-ban a TDE alapértelmezett beállítása az, hogy a ADATTITKOSÍTÁSI kulcsot egy beépített kiszolgálótanúsítvány védi. Alternatív megoldásként használhatja az ügyfél által felügyelt TDE, más néven a TDE-hez Bring Your Own Key (BYOK) támogatást. Ebben az esetben a ADATTITKOSÍTÁSI kulcsot titkosító TDE-védő egy ügyfél által felügyelt aszimmetrikus kulcs, amelyet az ügyfél és a felügyelt Azure Key Vault (az Azure felhőalapú külső kulcs-felügyeleti rendszere) tárol, és soha nem hagyja el a kulcstartót.

- [A szolgáltatás által kezelt transzparens adattitkosítás ismertetése](../azure-sql/database/transparent-data-encryption-tde-overview.md)

- [Az ügyfél által kezelt transzparens adattitkosítás ismertetése](https://docs.microsoft.com/azure/azure-sql/database/transparent-data-encryption-tde-overview#customer-managed-transparent-data-encryption---bring-your-own-key)

- [A TDE bekapcsolása saját kulcs használatával](../azure-sql/database/transparent-data-encryption-byok-configure.md)

**Felelősség**: Megosztott

**Azure Security Center monitorozás**: az [Azure biztonsági teljesítményteszt](/home/mbaldwin/docs/asb/azure-docs-pr/articles/governance/policy/samples/azure-security-benchmark.md) a Security Center alapértelmezett házirend-kezdeményezése, és a [Security Center ajánlásainak](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/security-center-recommendations.md)alapja. A vezérlőhöz kapcsolódó Azure Policy-definíciók Security Center automatikusan engedélyezve vannak. Az ehhez a vezérlőhöz kapcsolódó riasztásokhoz szükség lehet egy [Azure Defender](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/azure-defender.md) -csomagra a kapcsolódó szolgáltatásokhoz.

**Beépített definíciók Azure Policy – Microsoft. SQL**:

[!INCLUDE [Resource Policy for Microsoft.Sql 4.8](../../includes/policy/standards/asb/rp-controls/microsoft.sql-4-8.md)]

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4,9: a kritikus Azure-erőforrások változásainak naplózása és riasztása

**Útmutató**: a Azure monitor és az Azure-tevékenység naplójának használatával riasztásokat hozhat létre, amelyekkel a szinapszis SQL-készletek és egyéb kritikus vagy kapcsolódó erőforrások éles példányain végezheti el a módosításokat.

Emellett az SQL szinapszis-készletben lévő adatbázisokhoz is beállíthat riasztásokat az Azure Portal használatával. A riasztások e-mailt küldhetnek, vagy meghívhatnak egy webhookot, ha valamilyen metrika (például az adatbázis mérete vagy a CPU-használat) eléri a küszöbértéket.

- [Riasztások létrehozása az Azure-tevékenységek naplózási eseményeihez](../azure-monitor/alerts/alerts-activity-log.md)

- [Riasztások létrehozása az Azure SQL Szinapszishoz](../azure-sql/database/alerts-insights-configure-portal.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

## <a name="vulnerability-management"></a>Biztonságirés-kezelés

*További információért lásd az [Azure biztonsági teljesítményteszt: biztonsági rés kezelése](../security/benchmarks/security-control-vulnerability-management.md)című témakört.*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5,1: automatikus biztonsági rések vizsgálatára szolgáló eszközök futtatása

**Útmutató**: engedélyezze a speciális adatbiztonságot, és kövesse az Azure Security Center a sebezhetőségi felmérések Azure SQL Database-adatbázisokon való végrehajtásáról szóló ajánlásokat.

- [Sebezhetőségi felmérések futtatása az Azure SQL Database-adatbázisokban](../azure-sql/database/sql-vulnerability-assessment.md)

- [A speciális adatbiztonság engedélyezése](../azure-sql/database/azure-defender-for-sql.md)

- [Azure Security Center sebezhetőségi felméréssel kapcsolatos javaslatok megvalósítása](../security-center/deploy-vulnerability-assessment-vm.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás**: az [Azure biztonsági teljesítményteszt](/home/mbaldwin/docs/asb/azure-docs-pr/articles/governance/policy/samples/azure-security-benchmark.md) a Security Center alapértelmezett házirend-kezdeményezése, és a [Security Center ajánlásainak](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/security-center-recommendations.md)alapja. A vezérlőhöz kapcsolódó Azure Policy-definíciók Security Center automatikusan engedélyezve vannak. Az ehhez a vezérlőhöz kapcsolódó riasztásokhoz szükség lehet egy [Azure Defender](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/azure-defender.md) -csomagra a kapcsolódó szolgáltatásokhoz.

**Beépített definíciók Azure Policy – Microsoft. SQL**:

[!INCLUDE [Resource Policy for Microsoft.Sql 5.1](../../includes/policy/standards/asb/rp-controls/microsoft.sql-5-1.md)]

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5,4: a biztonsági rések keresésének összehasonlítása

**Útmutató**: a sebezhetőségi felmérés egy Azure szinapszis SQL-ben beépített keresési szolgáltatás. A szolgáltatás a biztonsági réseket jelző szabályok tudásbázisát alkalmazza. Kiemeli az ajánlott eljárásoktól való eltéréseket, például a helytelen konfigurációkat, a túlzott engedélyeket és a nem védett bizalmas adatokat.  A sebezhetőségi felmérés a központi SQL Advanced Security (ADS) portálon keresztül érhető el és kezelhető.

- [Sebezhetőség-felmérési vizsgálatok kezelése és exportálása az SQL ADS portálon](../azure-sql/database/sql-vulnerability-assessment.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5,5: kockázatértékelési folyamat használatával rangsorolhatja a felderített biztonsági rések szervizelését

**Útmutató**: a Azure Security Center által biztosított alapértelmezett kockázati minősítések (biztonságos pontszám) használata.

Az adatfelderítési &amp; besorolás az Azure SZINAPSZIS SQL-be van építve. Magas szintű képességeket biztosít az adatbázisban lévő bizalmas adatok felderítéséhez, osztályozásához, címkézéséhez és jelentéséhez.

- [A Azure Security Center biztonsági pontszámának megismerése](../security-center/secure-score-security-controls.md)

- [Az adatfelderítési &amp; besorolás ismertetése](../azure-sql/database/data-discovery-and-classification-overview.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás**: az [Azure biztonsági teljesítményteszt](/home/mbaldwin/docs/asb/azure-docs-pr/articles/governance/policy/samples/azure-security-benchmark.md) a Security Center alapértelmezett házirend-kezdeményezése, és a [Security Center ajánlásainak](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/security-center-recommendations.md)alapja. A vezérlőhöz kapcsolódó Azure Policy-definíciók Security Center automatikusan engedélyezve vannak. Az ehhez a vezérlőhöz kapcsolódó riasztásokhoz szükség lehet egy [Azure Defender](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/azure-defender.md) -csomagra a kapcsolódó szolgáltatásokhoz.

**Beépített definíciók Azure Policy – Microsoft. SQL**:

[!INCLUDE [Resource Policy for Microsoft.Sql 5.5](../../includes/policy/standards/asb/rp-controls/microsoft.sql-5-5.md)]

## <a name="inventory-and-asset-management"></a>Leltár-és eszközfelügyelet

*További információt az [Azure biztonsági teljesítményteszt: leltár és eszközkezelés](../security/benchmarks/security-control-inventory-asset-management.md)című témakörben talál.*

### <a name="61-use-automated-asset-discovery-solution"></a>6,1: automatikus eszköz-felderítési megoldás használata

**Útmutató**: az Azure Resource Graph használatával lekérdezheti és felderítheti a dedikált SQL-készlethez kapcsolódó összes erőforrást az előfizetése (i) n belül. Győződjön meg arról, hogy megfelelő (olvasási) engedélyekkel rendelkezik a bérlőben, és képes felsorolni az összes Azure-előfizetést, valamint az előfizetésében lévő erőforrásokat.

Bár a klasszikus Azure-erőforrások felderíthető az Azure Resource Graph használatával, erősen ajánlott Azure Resource Manager erőforrások létrehozása és használata.

- [Lekérdezések létrehozása az Azure Resource Graph használatával](../governance/resource-graph/first-query-portal.md)

- [Azure-előfizetések megtekintése](/powershell/module/az.accounts/get-azsubscription)

- [Az Azure RBAC ismertetése](../role-based-access-control/overview.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="62-maintain-asset-metadata"></a>6,2: az eszköz metaadatainak fenntartása

**Útmutató**: címkéket alkalmazhat az Azure-erőforrásokra, így a metaadatok logikailag rendszerezve lesznek a besorolásban.

- [Címkék létrehozása és használata](../azure-resource-manager/management/tag-resources.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="63-delete-unauthorized-azure-resources"></a>6,3: jogosulatlan Azure-erőforrások törlése

**Útmutató**: a címkézés, a felügyeleti csoportok és az elkülönített előfizetések használata, ha szükséges, az eszközök rendszerezéséhez és nyomon követéséhez. Rendszeres időközönként egyeztetheti a leltárt, és gondoskodhat arról, hogy a jogosulatlan erőforrások törlése az előfizetésből időben történjen.

- [További Azure-előfizetések létrehozása](../cost-management-billing/manage/create-subscription.md)

- [Management Groups létrehozása](../governance/management-groups/create-management-group-portal.md)

- [Címkék létrehozása és használata](../azure-resource-manager/management/tag-resources.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6,4: a jóváhagyott Azure-erőforrások leltárának meghatározása és karbantartása

**Útmutató**: a dedikált SQL-készlethez kapcsolódó jóváhagyott Azure-erőforrások listájának meghatározása.

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="65-monitor-for-unapproved-azure-resources"></a>6,5: a nem jóváhagyott Azure-erőforrások figyelése

**Útmutató**: a Azure Policy használatával korlátozásokat állíthat be az ügyfél-előfizetésekben létrehozható erőforrások típusára a következő beépített szabályzat-definíciók használatával:

- Nem engedélyezett erőforrástípusok

- Engedélyezett erőforrástípusok

Az Azure Resource Graph használatával lekérdezheti vagy felderítheti az előfizetésében lévő erőforrásokat. Győződjön meg arról, hogy a környezetben lévő összes Azure-erőforrás jóvá van hagyva.

- [Az Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md)

- [Lekérdezések létrehozása az Azure Resource Graph használatával](../governance/resource-graph/first-query-portal.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="69-use-only-approved-azure-services"></a>6,9: csak jóváhagyott Azure-szolgáltatások használata

**Útmutató**: a Azure Policy használatával korlátozásokat helyezhet el az ügyfél-előfizetésekben létrehozható erőforrások típusára a következő beépített szabályzat-definíciók használatával:
- Nem engedélyezett erőforrástípusok
- Engedélyezett erőforrástípusok

Az Azure Resource Graph használatával lekérdezheti vagy felderítheti az előfizetése (ke) n belüli erőforrásait. Győződjön meg arról, hogy a környezetben lévő összes Azure-erőforrás jóvá van hagyva.

- [Az Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md)

- [Adott erőforrástípus megtagadása a következővel Azure Policy](https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#general)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6,11: korlátozza a felhasználók képességét a Azure Resource Manager való interakcióra

**Útmutató**: az Azure feltételes hozzáférés használatával korlátozhatja a felhasználók a Azure Resource Manager interakcióját a "Microsoft Azure felügyelet" alkalmazás "hozzáférés tiltása" beállításával.

- [A feltételes hozzáférés konfigurálása a Azure Resource Managerhoz való hozzáférés blokkolásához](../role-based-access-control/conditional-access-azure-management.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

## <a name="secure-configuration"></a>Biztonságos konfiguráció

*További információt az [Azure biztonsági teljesítményteszt: biztonságos konfiguráció](../security/benchmarks/security-control-secure-configuration.md)című témakörben talál.*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7,1: biztonságos konfigurációk létrehozása az összes Azure-erőforráshoz

**Útmutató**: Azure Policy aliasok használata a "Microsoft. SQL" névtérben egyéni szabályzatok létrehozásához a dedikált SQL-készlethez kapcsolódó erőforrások konfigurációjának naplózásához vagy érvényesítéséhez. Az Azure-adatbázisok/-kiszolgálók beépített szabályzat-definícióit is használhatja, például:

- Veszélyforrások észlelésének üzembe helyezése SQL-kiszolgálókon
- SQL Server virtuális hálózati szolgáltatás végpontját kell használnia

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

**Útmutató**: ha egyéni Azure Policy-definíciókat használ, az Azure DevOps vagy az Azure Repos segítségével biztonságosan tárolhatja és kezelheti a kódot.

- [Kód tárolása az Azure DevOps](/azure/devops/repos/git/gitworkflow)

- [Az Azure Repos dokumentációja](/azure/devops/repos/)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7,7: az Azure-erőforrások konfigurációs felügyeleti eszközeinek üzembe helyezése

**Útmutató**: nem alkalmazható; Az Azure szinapszis SQL nem rendelkezik konfigurálható biztonsági beállításokkal.

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7,9: automatikus konfigurációs monitorozás megvalósítása Azure-erőforrásokhoz

**Útmutató**: a Azure Security Center kihasználása a dedikált SQL-készlethez kapcsolódó erőforrások alapkonfigurációjának vizsgálatához.

- [Javaslatok szervizelése Azure Security Center](../security-center/security-center-remediate-recommendations.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="711-manage-azure-secrets-securely"></a>7,11: az Azure-titkok biztonságos kezelése

**Útmutató**: TRANSZPARENS ADATTITKOSÍTÁS (TDE) az ügyfél által felügyelt kulcsokkal a Azure Key Vault lehetővé teszi az automatikusan generált adatbázis-titkosítási kulcs (adattitkosítási kulcsot) titkosítását az ügyfél által felügyelt, TDE Protector nevű aszimmetrikus kulccsal. Ezt általában a transzparens adattitkosítás Bring Your Own Key (BYOK) támogatása is említi. A BYOK forgatókönyvben a TDE-védőt az ügyfél és a felügyelt Azure Key Vault tárolja. Továbbá győződjön meg arról, hogy a Soft delete engedélyezve van a Azure Key Vaultban.

- [A TDE engedélyezése az ügyfél által felügyelt kulccsal Azure Key Vault](../azure-sql/database/transparent-data-encryption-byok-configure.md)

- [A Soft delete engedélyezése Azure Key Vault](../key-vault/general/key-vault-recovery.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="712-manage-identities-securely-and-automatically"></a>7,12: az identitások biztonságos és automatikus kezelése

**Útmutató**: felügyelt identitások használata az Azure-szolgáltatások automatikus felügyelt identitással való ellátásához Azure Active Directory (Azure ad). A felügyelt identitások lehetővé teszik bármely olyan szolgáltatás hitelesítését, amely támogatja az Azure AD-hitelesítést, beleértve a Azure Key Vault is, a kódban szereplő hitelesítő adatok nélkül.

- [Oktatóanyag: Hozzáférés az Azure SQL-hez egy Windows VM rendszer által hozzárendelt felügyelt identitásával](../active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-sql.md)

- [Felügyelt identitások konfigurálása](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="713-eliminate-unintended-credential-exposure"></a>7,13: a hitelesítő adatok nem szándékolt expozíciójának megszüntetése

**Útmutató**: hitelesítő adatok beolvasása a kódban a hitelesítő adatok azonosításához. A Credential Scanner a felfedezett hitelesítő adatok biztonságosabb helyre, például az Azure Key Vaultba való áthelyezésére is javaslatot tesz.

- [A hitelesítő adatok beolvasójának beállítása](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

## <a name="malware-defense"></a>Kártevők elleni védelem

*További információt az [Azure biztonsági teljesítményteszt: kártevők elleni védelem](../security/benchmarks/security-control-malware-defense.md)című témakörben talál.*

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8,2: a nem számítási Azure-erőforrásokra feltöltött fájlok előzetes vizsgálata

**Útmutató**: a Microsoft kártevő szoftverrel való ellátása engedélyezve van az Azure-szolgáltatásokat támogató mögöttes gazdagépen (például Azure szinapszis SQL); azonban nem fut az ügyfél tartalmán.

A nem számítási Azure-erőforrásokra feltöltött tartalmak előzetes vizsgálata, például App Service, Data Lake Storage, Blob Storage, Azure SQL Server stb. A Microsoft nem fér hozzá az adataihoz ezekben a példányokban.

- [Megismerheti a Microsoft antimalware-t az Azure Cloud Services és Virtual Machines](../security/fundamentals/antimalware.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

## <a name="data-recovery"></a>Adat-helyreállítás

*További információt az [Azure biztonsági teljesítményteszt: adat-helyreállítás](../security/benchmarks/security-control-data-recovery.md)című témakörben talál.*

### <a name="91-ensure-regular-automated-back-ups"></a>9,1: rendszeres automatizált biztonsági másolatok biztosítása

**Útmutató**: a dedikált SQL-készlet pillanatképeit a rendszer a nap folyamán automatikusan hozza létre a hét napig elérhető visszaállítási pontok létrehozásához. Ez a megőrzési időszak nem módosítható. A dedikált SQL-készlet támogatja a 8 órás helyreállítási időkorlátot (RPO). Az adattárházat az elsődleges régióban állíthatja vissza az elmúlt hét napban készített Pillanatképek közül. Vegye figyelembe, hogy szükség esetén manuálisan is aktiválhatja a pillanatképeket.

- [Biztonsági mentés és visszaállítás dedikált SQL-készletben](sql-data-warehouse/backup-and-restore.md)

**Felelősség**: Megosztott

**Azure Security Center monitorozás**: az [Azure biztonsági teljesítményteszt](/home/mbaldwin/docs/asb/azure-docs-pr/articles/governance/policy/samples/azure-security-benchmark.md) a Security Center alapértelmezett házirend-kezdeményezése, és a [Security Center ajánlásainak](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/security-center-recommendations.md)alapja. A vezérlőhöz kapcsolódó Azure Policy-definíciók Security Center automatikusan engedélyezve vannak. Az ehhez a vezérlőhöz kapcsolódó riasztásokhoz szükség lehet egy [Azure Defender](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/azure-defender.md) -csomagra a kapcsolódó szolgáltatásokhoz.

**Beépített definíciók Azure Policy – Microsoft. SQL**:

[!INCLUDE [Resource Policy for Microsoft.Sql 9.1](../../includes/policy/standards/asb/rp-controls/microsoft.sql-9-1.md)]

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: teljes rendszerbiztonsági mentés és minden ügyfél által felügyelt kulcs biztonsági mentése

**Útmutató**: az adattárház pillanatképeit a rendszer a nap folyamán automatikusan hozza létre a hét napig elérhető visszaállítási pontok létrehozásához. Ez a megőrzési időszak nem módosítható. A dedikált SQL-készlet támogatja a 8 órás helyreállítási időkorlátot (RPO). Az adattárházat az elsődleges régióban állíthatja vissza az elmúlt hét napban készített Pillanatképek közül. Vegye figyelembe, hogy szükség esetén manuálisan is aktiválhatja a pillanatképeket.

Ha ügyfél által felügyelt kulcsot használ az adatbázis-titkosítási kulcs titkosításához, győződjön meg arról, hogy a kulcs biztonsági mentése folyamatban van.

- [Biztonsági mentés és visszaállítás dedikált SQL-készletben](sql-data-warehouse/backup-and-restore.md)

- [Azure Key Vault kulcsok biztonsági mentése](/powershell/module/az.keyvault/backup-azkeyvaultkey)

**Felelősség**: Megosztott

**Azure Security Center monitorozás**: az [Azure biztonsági teljesítményteszt](/home/mbaldwin/docs/asb/azure-docs-pr/articles/governance/policy/samples/azure-security-benchmark.md) a Security Center alapértelmezett házirend-kezdeményezése, és a [Security Center ajánlásainak](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/security-center-recommendations.md)alapja. A vezérlőhöz kapcsolódó Azure Policy-definíciók Security Center automatikusan engedélyezve vannak. Az ehhez a vezérlőhöz kapcsolódó riasztásokhoz szükség lehet egy [Azure Defender](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/azure-defender.md) -csomagra a kapcsolódó szolgáltatásokhoz.

**Beépített definíciók Azure Policy – Microsoft. SQL**:

[!INCLUDE [Resource Policy for Microsoft.Sql 9.2](../../includes/policy/standards/asb/rp-controls/microsoft.sql-9-2.md)]

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: az összes biztonsági másolat ellenőrzése, beleértve az ügyfél által felügyelt kulcsokat

**Útmutató**: a visszaállítási pontok rendszeres tesztelése a pillanatképek érvényességének biztosítása érdekében. Ha egy meglévő dedikált SQL-készletet szeretne visszaállítani egy visszaállítási pontról, akkor a Azure Portal vagy a PowerShellt is használhatja. Tesztelje az ügyfél által felügyelt kulcsok biztonsági mentésének visszaállítását.

- [Azure Key Vault kulcsok visszaállítása](/powershell/module/az.keyvault/restore-azkeyvaultkey)

- [Biztonsági mentés és visszaállítás dedikált SQL-készletben](sql-data-warehouse/backup-and-restore.md)

- [Meglévő dedikált SQL-készlet visszaállítása](sql-data-warehouse/sql-data-warehouse-restore-active-paused-dw.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: a biztonsági másolatok és az ügyfél által felügyelt kulcsok védelmének biztosítása

**Útmutató**: a Azure SQL Database egyetlen vagy készletezett adatbázist is beállíthat hosszú távú biztonsági mentési adatmegőrzési szabályzattal (ltr), amellyel az adatbázis biztonsági másolatait külön Azure Blob Storage-tárolókban, akár 10 évig is megtarthatja. Az Azure Storage-ban tárolt adatai a 256 bites AES-titkosítással vannak titkosítva és visszafejtve, és az egyik legerősebb blokk titkosítási algoritmus, amely az FIPS 140-2-kompatibilis.

Alapértelmezés szerint a Storage-fiókokban lévő adatforgalom a Microsoft által felügyelt kulcsokkal van titkosítva. Hivatkozhat a Microsoft által felügyelt kulcsokra az adatok titkosításához, vagy kezelheti a titkosítást a saját kulcsaival. Ha Key Vault használatával felügyeli a saját kulcsait, győződjön meg arról, hogy a helyreállítható törlés engedélyezve van.

- [A biztonsági másolatok hosszú távú megőrzésének Azure SQL Database kezelése](../azure-sql/database/long-term-backup-retention-configure.md)

- [Inaktív adatok Azure Storage-titkosítása](../storage/common/storage-service-encryption.md)

- [A Soft delete engedélyezése Key Vault](../storage/blobs/soft-delete-blob-overview.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

## <a name="incident-response"></a>Incidensmegoldás

*További információ: [Azure Security Benchmark: Incidensek kezelése](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10,1: incidens-válaszi útmutató létrehozása

**Útmutató**: Ellenőrizze, hogy vannak-e olyan írásos incidensek, amelyek meghatározzák a személyzet szerepköreit, valamint az incidensek kezelésének és kezelésének fázisait.

- [Munkafolyamat-automatizálás konfigurálása Azure Security Centeron belül](../security-center/security-center-planning-and-operations-guide.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10,2: incidensek pontozásának és rangsorolási eljárásának létrehozása

**Útmutató**: a Security Center súlyosságot rendel a riasztásokhoz, így könnyebben rangsorolhatja az egyes riasztásokban való részvétel sorrendjét, hogy az erőforrások biztonsága esetén azonnal elérhető legyen. A súlyosság azon alapul, hogy az Security Center milyen mértékben szerepel a riasztások kijavításához használt mérőszámban, illetve a riasztást eredményező tevékenységen belül rosszindulatú szándékkal.

- [Biztonsági riasztások az Azure Security Centerben](../security-center/security-center-alerts-overview.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="103-test-security-response-procedures"></a>10,3: biztonsági reagálási eljárások tesztelése

**Útmutató**: a rendszerek incidensek reagálási képességeinek rendszeres tesztelésére szolgáló gyakorlatok végrehajtása. Azonosítsa a gyenge pontokat és réseket, és szükség esetén dolgozza át a tervet.

- [Tekintse meg a NIST kiadványát: útmutató az IT-csomagok és-képességek teszteléséhez, betanításához és edzésprogramjának kidolgozásához](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10,4: biztonsági incidensek elérhetőségének biztosítása és riasztási értesítések konfigurálása biztonsági incidensekhez

**Útmutató**: a Microsoft a biztonsági incidensek elérhetőségi adatait arra használja fel, hogy felvegye Önnel a kapcsolatot, ha a Microsoft Security Response Center (MSRC) felfedi, hogy az adatokat egy törvénytelen vagy jogosulatlan fél is hozzáférte.

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

**Útmutató**: kövesse a Microsoft részvételi szabályait, hogy a behatolási tesztek ne sértsék meg a Microsoft-házirendeket: https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1 .

- [A Microsoft által felügyelt felhő-infrastruktúrával,-szolgáltatásokkal és-alkalmazásokkal kapcsolatos, a Microsoft által kezelt felhőalapú infrastruktúra, szolgáltatások](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

## <a name="next-steps"></a>Következő lépések

- [Az Azure Security Benchmark v2 áttekintésének](/azure/security/benchmarks/overview) megtekintése
- További tudnivalók az [Azure biztonsági alapterveiről](/azure/security/benchmarks/security-baselines-overview)
