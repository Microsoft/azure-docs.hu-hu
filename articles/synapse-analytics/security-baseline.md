---
title: Az Azure biztonsági alapkonfigurációja Synapse Analytics
description: A Synapse Analytics alapkonfiguráció eljárásokkal kapcsolatos útmutatást és forrásforrásokat biztosít az Azure biztonsági teljesítménytesztben meghatározott biztonsági javaslatok megvalósításához.
author: msmbaldwin
ms.service: synapse-analytics
ms.subservice: security
ms.topic: conceptual
ms.date: 03/16/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: c2fad393479f0ffce81a272919b8d12024001991
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107565457"
---
# <a name="azure-security-baseline-for-synapse-analytics"></a>Az Azure biztonsági alapkonfigurációja Synapse Analytics

Ez a biztonsági alapkonfiguráció az Azure Biztonsági teljesítményteszt [1.0-s](../security/benchmarks/overview-v1.md) verziójának útmutatását alkalmazza a Synapse Analytics. Az Azure Security Benchmark ajánlásokat ad arra nézve, hogy hogyan tehetők biztonságossá a felhőalapú megoldások az Azure-ban.
A tartalmat az Azure  biztonsági teljesítményteszt által meghatározott biztonsági vezérlők és a kapcsolódó, az alkalmazásra vonatkozó Synapse Analytics. **A** Synapse Analytics nem alkalmazható vezérlők ki vannak zárva.

 
Ha meg Synapse Analytics, hogyan lehet teljes mértékben leképezni az Azure biztonsági teljesítménytesztre, tekintse meg a teljes Synapse Analytics alapkonfiguráció [leképezési fájlját.](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)

## <a name="network-security"></a>Hálózati biztonság

*További információ: [Azure Security Benchmark: Hálózati biztonság](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1.1: Azure-erőforrások védelme virtuális hálózatokon belül

**Útmutató:** Biztonságossá Azure SQL-kiszolgálót virtuális hálózatra a Private Link. Azure Private Link lehetővé teszi az Azure PaaS-szolgáltatások privát végponton keresztüli hozzáférését a virtuális hálózatban. A virtuális hálózat és a szolgáltatás közötti forgalom a Microsoft gerinchálózatán keresztüli.

Másik lehetőségként a Synapse SQL egy hálózati biztonsági csoport használatával szűkítse le az SQL-adatbázishoz kimenő kapcsolat hatókörét. Tiltsa le az SQL-adatbázisra a nyilvános végponton keresztül az Azure-szolgáltatások kikapcsolása beállítással az SQL Database-be történő összes Azure-szolgáltatásforgalmat. Győződjön meg arról, hogy a tűzfalszabályokban nem engedélyezettek nyilvános IP-címek.

- [A Azure Private Link](../private-link/private-link-overview.md)

- [Az SQL Private Link hez való Azure Synapse használata](../azure-sql/database/private-endpoint-overview.md)

- [Új Virtual Network](../virtual-network/quick-create-portal.md)

- [NSG létrehozása biztonsági konfigurációval](../virtual-network/tutorial-filter-network-traffic.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozása:** Az [Azure Biztonsági](/home/mbaldwin/docs/asb/azure-docs-pr/articles/governance/policy/samples/azure-security-benchmark.md) teljesítményteszt a Security Center alapértelmezett szabályzati kezdeményezése, amely a Security Center [javaslatainak alapja.](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/security-center-recommendations.md) A Azure Policy vezérlővel kapcsolatos összes definíciót automatikusan engedélyezi a Security Center. Az ehhez a vezérlőhez kapcsolódó riasztások esetében szükség [lehet Azure Defender](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/azure-defender.md) kapcsolódó szolgáltatásokra vonatkozó tervre.

**Azure Policy definíciók – Microsoft.Sql:**

[!INCLUDE [Resource Policy for Microsoft.Sql 1.1](../../includes/policy/standards/asb/rp-controls/microsoft.sql-1-1.md)]

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-network-interfaces"></a>1.2: Virtuális hálózatok, alhálózatok és hálózati adapterek konfigurációjának és forgalmának figyelése és naplózása

**Útmutató:** Ha dedikált SQL-készlethez csatlakozik, és engedélyezte a hálózati biztonsági csoport (NSG) forgalomnaplóit, a rendszer a naplókat egy Azure Storage-fiókba küldi a forgalom naplózása érdekében.

Az NSG-forgalom naplóit egy Log Analytics-munkaterületre is elküldheti, és a Traffic Analytics segítségével betekintést nyerhet az Azure-felhő adatforgalmába. A hálózati Traffic Analytics a hálózati tevékenységek vizualizációja és a gyors helyek azonosítása, a biztonsági fenyegetések azonosítása, a forgalmi minták azonosítása és a hálózati konfigurációk észlelése.

- [NSG-forgalom naplóinak engedélyezése](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [A hálózati biztonság Azure Security Center](../security-center/security-center-network-recommendations.md)

- [A Traffic Analytics](../network-watcher/traffic-analytics.md)

- [A hálózati biztonság Azure Security Center](../security-center/security-center-network-recommendations.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: Az ismert kártékony IP-címekkel való kommunikáció megtagadása

**Útmutató:** Az Advanced Threat Protection (ATP) használata Azure Synapse SQL-hez. Az ATP észleli azokat a rendellenes tevékenységeket, amelyek az adatbázisok hozzáférésére vagy kihasználására tett szokatlan és potenciálisan rosszindulatú kísérleteket jeleznek, és különböző riasztásokat aktiválhat, például "Potenciális SQL-injektálás" és "Hozzáférés szokatlan helyről". Az ATP az Advanced Data Security (ADS) ajánlat része, és a központi SQL ADS-portálon keresztül érhető el és kezelhető.

Engedélyezze DDoS Protection Standard szabványt a Azure Synapse SQL-hez társított virtuális hálózatokon az elosztott szolgáltatásmegtagadásos támadások elleni védelem érdekében. Az Azure Security Center veszélyforrás-felderítés használatával megtagadhatja az ismert rosszindulatú vagy nem használt internetes IP-címekkel való kommunikációt.

- [Az SQL-hez Azure Synapse ATP](../azure-sql/database/threat-detection-overview.md)

- [A Advanced Data Security engedélyezése Azure SQL Database](../azure-sql/database/azure-defender-for-sql.md)

- [Az ADS áttekintése](../azure-sql/database/azure-defender-for-sql.md)

- [A DDoS Protection konfigurálása](../ddos-protection/manage-ddos-protection.md)

- [Az Azure Security Center fenyegetésintelligencia](../security-center/azure-defender.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="15-record-network-packets"></a>1.5: Hálózati csomagok rekordja

**Útmutató:** Ha dedikált SQL-készlethez csatlakozik, és engedélyezte a hálózati biztonsági csoport (NSG) forgalmi naplóit, küldjön naplókat egy Azure Storage-fiókba a forgalom naplózásához. A folyamatnaplókat log Analytics-munkaterületre is küldheti, vagy streamelheti őket a Event Hubs.  Ha a rendellenes tevékenységek kivizsgálásához szükséges, engedélyezze a Network Watcher rögzítését.

- [NSG-forgalom naplóinak engedélyezése](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [A Network Watcher](../network-watcher/network-watcher-create.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6: Hálózatalapú behatolásészlelési/behatolásvédelmi rendszerek (IDS/IPS) üzembe helyezése

**Útmutató:** Az Advanced Threat Protection (ATP) használata Azure Synapse SQL-hez. Az ATP észleli a rendellenes tevékenységeket, amelyek az adatbázisok hozzáférésére vagy kihasználására tett szokatlan és potenciálisan rosszindulatú kísérletekre utalnak, és különböző riasztásokat aktiválhat, például "Potenciális SQL-injektálás" és "Hozzáférés szokatlan helyről". Az ATP az Advanced Data Security (ADS) ajánlat része, és a központi SQL ADS-portálon keresztül érhető el és kezelhető. Az ATP a riasztásokat a Azure Security Center.

- [Az SQL-hez Azure Synapse ATP](../azure-sql/database/threat-detection-overview.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: A hálózati biztonsági szabályok összetettségének és adminisztratív terhelésének minimalizálása

**Útmutató:** Hálózati biztonsági csoportok vagy hálózati biztonsági csoportok hálózati hozzáférés-vezérlésének meghatározása virtuális hálózati szolgáltatáscímkék Azure Firewall. Biztonsági szabályok létrehozása során szolgáltatáscímkéket használhat bizonyos IP-címek helyett. A szolgáltatáscímke nevének (például ApiManagement) a szabály megfelelő forrás- vagy célmezőben való megadásával engedélyezheti vagy megtagadhatja a megfelelő szolgáltatás forgalmát. A Microsoft kezeli a szolgáltatáscímke által felölelt címelőtagokat, és automatikusan frissíti a szolgáltatáscímkét a címek változásával.

Ha szolgáltatásvégpontot használ a dedikált SQL-készlethez Azure SQL, kimenő forgalomra van szükség egy adatbázis nyilvános IP-címére: A hálózati biztonsági csoportokat (NSG-ket) meg kell nyitni Azure SQL Database IP-címek számára a csatlakozás engedélyezése érdekében. Ezt NSG-szolgáltatáscímkék használatával is Azure SQL Database.

- [A szolgáltatáscímkék és a szolgáltatásvégpontokkal való Azure SQL Database](https://docs.microsoft.com/azure/azure-sql/database/vnet-service-endpoint-rule-overview#limitations)

- [A szolgáltatáscímkék használata és használata](../virtual-network/service-tags-overview.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: A hálózati eszközök szabványos biztonsági konfigurációjának fenntartása

**Útmutató:** A dedikált SQL-készlethez kapcsolódó erőforrások hálózati biztonsági konfigurációjának definiálása és megvalósítása Azure Policy. A "Microsoft.Sql" névtér használatával egyéni szabályzatdefiníciókat definiálhat, vagy használhatja az adatbázis-/kiszolgáló Azure SQL védelemhez tervezett bármelyik beépített szabályzatdefiníciót. A virtuális kiszolgálóra vonatkozó beépített hálózati biztonsági házirend például a Azure SQL Database következő lehet: "SQL Server virtuális hálózati szolgáltatásvégpontot kell használnia".

Az Azure Blueprints használatával leegyszerűsítheti a nagy méretű Azure-környezeteket, mivel egyetlen tervdefinícióba kell becsomagolásával az olyan kulcsfontosságú környezeti összetevők, mint az Azure Resource Management-sablonok, az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC) és a szabályzatok. Könnyedén alkalmazhatja a tervet az új előfizetésekhez és környezetekhez, és finomhangolhatja a vezérlést és a felügyeletet a verziószámozás segítségével.

- [Az Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md)

- [Azure Blueprints-terv létrehozása](../governance/blueprints/create-blueprint-portal.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="110-document-traffic-configuration-rules"></a>1.10: A forgalom konfigurációs szabályainak dokumentálása

**Útmutató:** Címkék használata hálózati biztonsági csoportokhoz (NSG-khez) és egyéb, a hálózati biztonsághoz és forgalomhoz kapcsolódó erőforrásokhoz. Egyéni NSG-szabályoknál a "Leírás" mezőben adhatja meg az üzleti igényeket és/vagy időtartamot (stb.) a hálózatra/hálózatról be- és vissza forgalmat engedélyező szabályokhoz.

A címkézéshez kapcsolódó beépített Azure Policy-definíciók (például "Címke és érték megkövetelése" ) használatával biztosíthatja, hogy az összes erőforrás címkékkel legyen létrehozva, és értesítést küld a meglévő, címkézetlen erőforrásokról.

A címkék alapján Azure PowerShell azure cli használatával erőforrásokat kereshet vagy hajthat végre műveleteken.

- [Címkék létrehozása és használata](../azure-resource-manager/management/tag-resources.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: Automatizált eszközök használata a hálózati erőforrások konfigurációjának figyelése és a változások észlelése érdekében

**Útmutató:** Az Azure-tevékenységnapló segítségével monitorozható a hálózati erőforrások konfigurációja, és észlelhetők a dedikált SQL-készlethez kapcsolódó hálózati erőforrások változásai. Hozzon létre riasztásokat a Azure Monitor, amelyek akkor aktiválódnak, ha a kritikus hálózati erőforrások módosulnak.

- [Az Azure-tevékenységnapló eseményeinek megtekintése és lekérése](https://docs.microsoft.com/azure/azure-monitor/essentials/activity-log#view-the-activity-log)

- [Riasztások létrehozása a Azure Monitor](../azure-monitor/alerts/alerts-activity-log.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

## <a name="logging-and-monitoring"></a>Naplózás és monitorozás

*További információ: [Azure biztonsági teljesítményteszt: Naplózás és monitorozás.](../security/benchmarks/security-control-logging-monitoring.md)*

### <a name="22-configure-central-security-log-management"></a>2.2: Központi biztonsági naplókezelés konfigurálása

**Útmutató:** Egy naplózási szabályzat definiálható egy adott adatbázishoz vagy az Azure alapértelmezett kiszolgálói házirendként (amely az azure-ban Azure Synapse). A kiszolgálói házirend a kiszolgálón lévő összes meglévő és újonnan létrehozott adatbázisra vonatkozik.

Ha a kiszolgáló naplózása engedélyezve van, az mindig az adatbázisra vonatkozik. A rendszer az adatbázis naplózási beállításaitól függetlenül naplót végez az adatbázison.

Ha engedélyezi a naplózást, kiírhatja őket egy auditnaplóba az Azure Storage-fiókban, a Log Analytics-munkaterületen vagy a Event Hubs.

Másik lehetőségként engedélyezheti és bevetheti az adatokat Azure Sentinel külső SIEM-hez.

- [Naplózás beállítása a Azure SQL erőforrásokhoz](https://docs.microsoft.com/azure/azure-sql/database/auditing-overview#server-vs-database-level)

- [A Azure Sentinel](../sentinel/quickstart-onboard.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Azure-erőforrások naplózásának engedélyezése

**Útmutató:** Engedélyezze a naplózást a Azure SQL SQL-készlet kiszolgálói szintjén, és válasszon egy tárolási helyet az auditnaplók számára (Azure Storage, Log Analytics vagy Event Hubs).

A naplózás az adatbázis vagy a kiszolgáló szintjén is engedélyezhető, és javasolt csak a kiszolgáló szintjén engedélyezni, kivéve, ha külön adat fogadót vagy megőrzést kell konfigurálni egy adott adatbázishoz.

- [Naplózás engedélyezése a Azure SQL Database](../azure-sql/database/auditing-overview.md)

- [Naplózás engedélyezése a kiszolgáló számára](https://docs.microsoft.com/azure/azure-sql/database/auditing-overview#setup-auditing)

- [A kiszolgálószintű és az adatbázisszintű naplózási házirendek közötti különbségek](https://docs.microsoft.com/azure/azure-sql/database/auditing-overview#server-vs-database-level)

**Felelősség**: Ügyfél

**Azure Security Center monitorozása:** Az [Azure Biztonsági](/home/mbaldwin/docs/asb/azure-docs-pr/articles/governance/policy/samples/azure-security-benchmark.md) teljesítményteszt a Security Center alapértelmezett szabályzati kezdeményezése, amely a Security Center [javaslatainak alapja.](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/security-center-recommendations.md) A Azure Policy vezérlővel kapcsolatos összes definíciót automatikusan engedélyezi a Security Center. Az ehhez a vezérlőhez kapcsolódó riasztások esetében szükség [lehet Azure Defender](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/azure-defender.md) kapcsolódó szolgáltatásokra vonatkozó tervre.

**Azure Policy definíciók – Microsoft.Sql:**

[!INCLUDE [Resource Policy for Microsoft.Sql 2.3](../../includes/policy/standards/asb/rp-controls/microsoft.sql-2-3.md)]

### <a name="25-configure-security-log-storage-retention"></a>2.5: Biztonsági naplók tárolási megőrzésének konfigurálása

**Útmutató:** Ha a dedikált SQL-készlethez kapcsolódó naplókat tárfiókban, Log Analytics-munkaterületen vagy eseményközpontban tárolja, állítsa be a naplómegőrzési megőrzési időszakot a szervezet megfelelőségi szabályozásának megfelelően.

- [Az Azure Blob Storage-életciklus felügyelete](../storage/blobs/storage-lifecycle-management-concepts.md)

- [Naplómegőrzési paraméterek beállítása Log Analytics-munkaterületen](https://docs.microsoft.com/azure/azure-monitor/logs/manage-cost-storage#change-the-data-retention-period)

- [Streamelési események rögzítése Event Hubs](../event-hubs/event-hubs-capture-overview.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozása:** Az [Azure Biztonsági](/home/mbaldwin/docs/asb/azure-docs-pr/articles/governance/policy/samples/azure-security-benchmark.md) teljesítményteszt a Security Center alapértelmezett szabályzati kezdeményezése, amely a Security Center [javaslatainak alapja.](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/security-center-recommendations.md) A Azure Policy vezérlővel kapcsolatos összes definíciót automatikusan engedélyezi a Security Center. Az ehhez a vezérlőhez kapcsolódó riasztások esetében szükség [lehet Azure Defender](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/azure-defender.md) kapcsolódó szolgáltatásokra vonatkozó tervre.

**Azure Policy definíciók – Microsoft.Sql:**

[!INCLUDE [Resource Policy for Microsoft.Sql 2.5](../../includes/policy/standards/asb/rp-controls/microsoft.sql-2-5.md)]

### <a name="26-monitor-and-review-logs"></a>2.6: Naplók figyelése és áttekintése

**Útmutató:** Naplók elemzése és figyelése rendellenes viselkedések kereséséhez és az eredmények rendszeres áttekintéséhez. Használja az Advanced Threat Protectiont Azure SQL Database és Azure Security Center az SQL Database-hez kapcsolódó szokatlan tevékenységekkel kapcsolatos riasztásokat. Másik lehetőségként az SQL Database-hez kapcsolódó metrikaértékek vagy Azure-tevékenységnapló-bejegyzések alapján is konfigurálhatja a riasztásokat.

Másik lehetőségként engedélyezheti és bevetheti az adatokat Azure Sentinel külső SIEM-hez.

- [Az Advanced Threat Protection és a riasztások Azure SQL Database](../azure-sql/database/threat-detection-overview.md)

- [A Advanced Data Security engedélyezése Azure SQL Database](../azure-sql/database/azure-defender-for-sql.md)

- [Egyéni riasztások konfigurálása a Azure SQL Database](../azure-sql/database/alerts-insights-configure-portal.md)

- [A Azure Sentinel](../sentinel/quickstart-onboard.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7: Riasztások engedélyezése rendellenes tevékenységekhez

**Útmutató:** Az Advanced Threat Protection (ATP) Azure SQL Database és Azure Security Center a rendellenes tevékenységek figyelése és riasztása. Az ATP a Advanced Data Security (ADS) ajánlat része, és a portálon a központi SQL ADS-en keresztül érhető el és kezelhető. Az ADS a bizalmas adatok felderítésére és besorolására, az adatbázisok lehetséges biztonsági réseit felderítő és enyhítő funkciókat, valamint az adatbázisra nézve fenyegetést jelző rendellenes tevékenységek észlelését is magában foglalja.

Másik lehetőségként engedélyezheti és be is adatokat lehet Azure Sentinel.

- [Az Advanced Threat Protection és a riasztások Azure SQL Database](../azure-sql/database/threat-detection-overview.md)

- [A Advanced Data Security engedélyezése Azure SQL Database](../azure-sql/database/azure-defender-for-sql.md)

- [Riasztások kezelése a Azure Security Center](../security-center/security-center-managing-and-responding-alerts.md)

- [A Azure Sentinel](../sentinel/quickstart-onboard.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozása:** Az [Azure Biztonsági](/home/mbaldwin/docs/asb/azure-docs-pr/articles/governance/policy/samples/azure-security-benchmark.md) teljesítményteszt a Security Center alapértelmezett szabályzati kezdeményezése, amely a Security Center [javaslatainak alapja.](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/security-center-recommendations.md) A Azure Policy vezérlővel kapcsolatos összes definíciót automatikusan engedélyezi a Security Center. Az ehhez a vezérlőhez kapcsolódó riasztások esetében szükség [lehet Azure Defender](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/azure-defender.md) kapcsolódó szolgáltatásokra vonatkozó tervre.

**Azure Policy definíciók – Microsoft.Sql:**

[!INCLUDE [Resource Policy for Microsoft.Sql 2.7](../../includes/policy/standards/asb/rp-controls/microsoft.sql-2-7.md)]

## <a name="identity-and-access-control"></a>Identitás- és hozzáférés-vezérlés

*További információ: [Azure Biztonsági teljesítményteszt: Identitás és Access Control.](../security/benchmarks/security-control-identity-access-control.md)*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: A rendszergazdai fiókok nyilvántartása

**Útmutató:** A felhasználók hitelesítése vagy Azure Active Directory (Azure AD) vagy SQL-hitelesítéssel történik.

Az első üzembe Azure SQL rendszergazdai bejelentkezési nevet és a hozzá tartozó jelszót kell megadnia. Ennek a rendszergazdai fióknak a neve Kiszolgálói rendszergazda. Az adatbázis rendszergazdai fiókjait úgy azonosíthatja, ha megnyitja a Azure Portal, és megnyitja a kiszolgáló vagy a felügyelt példány Tulajdonságok lapját. Teljes körű rendszergazdai engedélyekkel rendelkező Azure AD-rendszergazdai fiókot is konfigurálhat. Erre akkor van szükség, ha engedélyezni szeretné az Azure AD-hitelesítést.

Felügyeleti műveletekhez használja az Azure beépített szerepköreit, amelyeket explicit módon kell hozzárendelni. Az Azure AD PowerShell-modullal alkalmi lekérdezéseket hajt végre a rendszergazdai csoportok tagjainak felderítéséhez.

- [Hitelesítés SQL Database](https://docs.microsoft.com/azure/azure-sql/database/security-overview#authentication)

- [Fiókok létrehozása nem rendszergazda felhasználók számára](https://docs.microsoft.com/azure/azure-sql/database/logins-create-manage#create-accounts-for-non-administrator-users)

- [Azure AD-fiók használata hitelesítéshez](https://docs.microsoft.com/azure/azure-sql/database/logins-create-manage#create-additional-logins-and-users-having-administrative-permissions)

- [Címtárszerepk lekért szerepkör az Azure AD-ban a PowerShell használatával](/powershell/module/azuread/get-azureaddirectoryrole)

- [Címtárbeli szerepkör tagjainak lekérte az Azure AD-ban a PowerShell használatával](/powershell/module/azuread/get-azureaddirectoryrolemember)

- [Meglévő bejelentkezések és rendszergazdai fiókok kezelése a Azure SQL](https://docs.microsoft.com/azure/azure-sql/database/logins-create-manage#existing-logins-and-user-accounts-after-creating-a-new-database)

- [Beépített Azure-szerepkörök](../role-based-access-control/built-in-roles.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="32-change-default-passwords-where-applicable"></a>3.2: Az alapértelmezett jelszavak módosítása, ha vannak

**Útmutató:** Azure Active Directory (Azure AD) nem rendelkezik az alapértelmezett jelszavak fogalmával. Dedikált SQL-készlet kiépítésekor javasoljuk, hogy integrálja a hitelesítést az Azure AD-val. Ezzel a hitelesítési módszerrel a felhasználó elküld egy felhasználói fióknevet, és kéri, hogy a szolgáltatás használja az Azure AD-ban tárolt hitelesítő adatokat.

- [Azure AD-hitelesítés konfigurálása és kezelése Azure SQL](../azure-sql/database/authentication-aad-configure.md)

- [A hitelesítés Azure SQL](https://docs.microsoft.com/azure/azure-sql/database/logins-create-manage#existing-logins-and-user-accounts-after-creating-a-new-database)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: Dedikált rendszergazdai fiókok használata

**Útmutató:** Házirendek és eljárások létrehozása a dedikált rendszergazdai fiókok használatával kapcsolatban. Az Azure Security Center és hozzáférés-kezelés használatával figyelheti a felügyeleti fiókok számát, amelyek az Azure AD Azure Active Directory keresztül jelentkeznek be.

Az adatbázis rendszergazdai fiókjainak azonosításához nyissa meg a Azure Portal, és lépjen a kiszolgáló vagy a felügyelt példány Tulajdonságok lapra.

- [Az Azure Security Center és a hozzáférések](../security-center/security-center-identity-access.md)

- [Meglévő bejelentkezések és rendszergazdai fiókok kezelése a Azure SQL](https://docs.microsoft.com/azure/azure-sql/database/logins-create-manage#existing-logins-and-user-accounts-after-creating-a-new-database)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="34-use-azure-active-directory-single-sign-on-sso"></a>3.4: Egyszeri Azure Active Directory (SSO) használata

**Útmutató:** Egy Azure-alkalmazásregisztráció (szolgáltatásnév) használatával lekérhet egy jogkivonatot, amely api-hívásokkal kommunikálhat az adattárházával a vezérlősíkon (Azure Portal).

- [Az Azure REST API-k meghívása](/rest/api/azure/#how-to-call-azure-rest-apis-with-postman)

- [Ügyfélalkalmazás (szolgáltatásnév) regisztrálása a Azure Active Directory (Azure AD) használatával](/rest/api/azure/#register-your-client-application-with-azure-ad)

- [Azure Synapse SQL REST API információk](sql-data-warehouse/sql-data-warehouse-manage-compute-rest-api.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: Többtényezős hitelesítés használata minden Azure Active Directory-alapú hozzáféréshez

**Útmutató:** Engedélyezze Azure Active Directory (Azure AD) többtényezős hitelesítést, és kövesse Azure Security Center identitás- és hozzáférés-kezelésre vonatkozó javaslatokat.

- [Többtényezős hitelesítés engedélyezése az Azure-ban](../active-directory/authentication/howto-mfa-getstarted.md)

- [Identitás és hozzáférés figyelése a Azure Security Center](../security-center/security-center-identity-access.md)

- [A többtényezős hitelesítés Azure SQL](../azure-sql/database/authentication-mfa-ssms-overview.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="36-use-secure-azure-managed-workstations-for-administrative-tasks"></a>3.6: Biztonságos, Azure által felügyelt munkaállomások használata felügyeleti feladatokhoz

**Útmutató:** Az Azure-erőforrásokba való bejelentkezéshez és konfiguráláshoz többtényezős hitelesítéssel rendelkező emelt hozzáférési szintű munkaállomást (PAW) használjon.

- [Tudnivalók az emelt szintű hozzáférésű munkaállomásokkal kapcsolatban](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

- [Többtényezős hitelesítés engedélyezése az Azure-ban](../active-directory/authentication/howto-mfa-getstarted.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3.7: A rendszergazdai fiókokból származó gyanús tevékenységek naplózása és riasztása

**Útmutató:** Azure Active Directory (Azure AD) biztonsági jelentésekkel naplókat és riasztásokat generál, ha gyanús vagy nem biztonságos tevékenység történik a környezetben.

Az Advanced Threat Protection Azure SQL Database és a Azure Security Center együttes használatával észlelheti az adatbázisokhoz való szokatlan és potenciálisan kártékony kísérleteket jelző rendellenes tevékenységeket, és riasztásokat küld velük kapcsolatban.

SQL Server naplózási funkcióval kiszolgálói naplókat hozhat létre, amelyek kiszolgálószintű események kiszolgálói naplózási specifikációit és adatbázisszintű események adatbázis-naplózási specifikációit tartalmaznak. A naplók az eseménynaplókba vagy a naplófájlokba írhatóak.

- [A kockázatos tevékenységek miatt megjelölt Azure AD-felhasználók azonosítása](../active-directory/identity-protection/overview-identity-protection.md)

- [A felhasználók identitás- és hozzáférési tevékenységének figyelése a Azure Security Center](../security-center/security-center-identity-access.md)

- [Az Advanced Threat Protection és a lehetséges riasztások áttekintése](https://docs.microsoft.com/azure/azure-sql/database/threat-detection-overview#alerts)

- [A bejelentkezések és a felhasználói fiókok Azure SQL](../azure-sql/database/logins-create-manage.md)

- [A SQL Server naplózásának](/sql/relational-databases/security/auditing/sql-server-audit-database-engine)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: Azure-erőforrások kezelése csak jóváhagyott helyekről

**Útmutató:** A feltételes hozzáférés elnevezett helyeinek használatával engedélyezheti, hogy a portál és az Azure Resource Management csak az IP-címtartományok vagy országok/régiók meghatározott logikai csoportosításaiból elérést biztosítsa.

- [Elnevezett helyek konfigurálása az Azure-ban](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="39-use-azure-active-directory"></a>3.9: A Azure Active Directory

**Útmutató:** Hozzon létre Azure Active Directory (Azure AD) rendszergazdát a Azure SQL Database SQL-készletben található virtuális kiszolgálóhoz.

- [Azure AD-hitelesítés konfigurálása és kezelése Azure SQL](../azure-sql/database/authentication-aad-configure.md)

- [Azure AD-példány létrehozása és konfigurálása](../active-directory-domain-services/tutorial-create-instance.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozása:** Az [Azure Biztonsági](/home/mbaldwin/docs/asb/azure-docs-pr/articles/governance/policy/samples/azure-security-benchmark.md) teljesítményteszt a Security Center alapértelmezett szabályzati kezdeményezése, amely a Security Center [javaslatainak alapja.](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/security-center-recommendations.md) A Azure Policy vezérlővel kapcsolatos összes definíciót automatikusan engedélyezi a Security Center. Az ehhez a vezérlőhez kapcsolódó riasztások esetében szükség [lehet Azure Defender](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/azure-defender.md) kapcsolódó szolgáltatásokra vonatkozó tervre.

**Azure Policy definíciók – Microsoft.Sql:**

[!INCLUDE [Resource Policy for Microsoft.Sql 3.9](../../includes/policy/standards/asb/rp-controls/microsoft.sql-3-9.md)]

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: A felhasználói hozzáférés rendszeres áttekintése és egyeztetése

**Útmutató:** Azure Active Directory (Azure AD) naplókat biztosít az elavult fiókok felderítésének segítésekor. Emellett az Azure AD hozzáférési felülvizsgálatok használatával hatékonyan kezelheti a csoporttagságokat, a vállalati alkalmazásokhoz való hozzáférést és a szerepkör-hozzárendeléseket. A felhasználók hozzáférését rendszeresen áttekintheti, hogy csak a megfelelő felhasználók férnek hozzá.

SQL-hitelesítés használata esetén hozzon létre tartalmazottadatbázis-felhasználókat az adatbázisban. Ügyeljen arra, hogy egy vagy több adatbázis-felhasználót helyezzen el egy egyéni adatbázis-szerepkörben az adott felhasználói csoportnak megfelelő konkrét engedélyekkel.

- [Hozzáférési felülvizsgálatok használata](../active-directory/governance/access-reviews-overview.md)

- [A bejelentkezések és a felhasználói fiókok Azure SQL](../azure-sql/database/logins-create-manage.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3.11: Az inaktivált hitelesítő adatok elérésére tett kísérletek figyelése

**Útmutató:** Konfigurálja Azure Active Directory (Azure AD) hitelesítését az Azure SQL-val, és engedélyezze az Azure AD felhasználói fiókok diagnosztikai beállításait, az auditnaplókat és a bejelentkezési naplókat egy Log Analytics-munkaterületre küldve. Konfigurálja a kívánt riasztásokat a Log Analyticsben.

SQL-hitelesítés használata esetén hozzon létre tartalmazottadatbázis-felhasználókat az adatbázisban. Győződjön meg arról, hogy egy vagy több adatbázis-felhasználót egy egyéni adatbázis-szerepkörbe helyezzen, amely az adott felhasználócsoportnak megfelelő engedélyekkel rendelkezik.

- [Hozzáférési felülvizsgálatok használata](../active-directory/governance/access-reviews-overview.md)

- [Azure AD-hitelesítés konfigurálása és kezelése Azure SQL Database](../azure-sql/database/authentication-aad-configure.md)

- [Azure-tevékenységnaplók integrálása az Azure Monitorba](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

- [A bejelentkezések és a felhasználói fiókok Azure SQL](../azure-sql/database/logins-create-manage.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3.12: Riasztás a fiók bejelentkezési viselkedésének eltérése esetén

**Útmutató:** A Azure Active Directory (Azure AD) Identity Protection és kockázatészlelési funkciók használatával konfigurálhatja az automatikus válaszokat a felhasználói identitásokkal kapcsolatos gyanús műveletekre. Emellett a további vizsgálathoz be kell vennie és be kell Azure Sentinel adatokat a Azure Sentinel adatokba.

SQL-hitelesítés használata esetén hozzon létre tartalmazottadatbázis-felhasználókat az adatbázisban. Győződjön meg arról, hogy egy vagy több adatbázis-felhasználót egy egyéni adatbázis-szerepkörbe helyezzen, amely az adott felhasználócsoportnak megfelelő engedélyekkel rendelkezik.

- [Az Azure AD-beli kockázati bejelentkezések megtekintése](../active-directory/identity-protection/overview-identity-protection.md)

- [Az Identity Protection kockázati szabályzatok konfigurálása és engedélyezése](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [Az Azure Sentinel](../sentinel/connect-data-sources.md)

- [A bejelentkezések és a felhasználói fiókok Azure SQL](../azure-sql/database/logins-create-manage.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: Hozzáférés biztosítanak a Microsoft számára a releváns ügyféladatokhoz a támogatási forgatókönyvek során

**Útmutató:** Olyan támogatási forgatókönyvekben, ahol a Microsoftnak hozzá kell férni a dedikált SQL-készletben található Azure SQL Database-hoz kapcsolódó adatokhoz, az Azure Ügyfélszéf felületet biztosít az adatelérési kérelmek áttekintéséhez, jóváhagyásához vagy elutasításához.

- [A Ügyfélszéf](../security/fundamentals/customer-lockbox-overview.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

## <a name="data-protection"></a>Adatvédelem

*További információ: [Azure Security Benchmark: Adatvédelem](../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: Bizalmas adatok leltárának fenntartása

**Útmutató:** A címkék segítségével nyomon lehet követni a bizalmas adatokat tároló vagy feldolgozó Azure-erőforrásokat.

Az &amp; adatfelderítési besorolás az SQL Azure Synapse van beépítve. Magas szintű képességeket biztosít az adatbázisban lévő bizalmas adatok felderítéséhez, osztályozásához, címkézéséhez és jelentéséhez.

- [Címkék létrehozása és használata](../azure-resource-manager/management/tag-resources.md)

- [Az adatfelderítési &amp; besorolások](../azure-sql/database/data-discovery-and-classification-overview.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozása:** Az [Azure Biztonsági](/home/mbaldwin/docs/asb/azure-docs-pr/articles/governance/policy/samples/azure-security-benchmark.md) teljesítményteszt a Security Center alapértelmezett szabályzati kezdeményezése, amely a Security Center [javaslatainak alapja.](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/security-center-recommendations.md) A Azure Policy vezérlővel kapcsolatos összes definíciót automatikusan engedélyezi a Security Center. Az ehhez a vezérlőhez kapcsolódó riasztások esetében szükség [lehet Azure Defender](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/azure-defender.md) kapcsolódó szolgáltatásokra vonatkozó tervre.

**Azure Policy definíciók – Microsoft.Sql:**

[!INCLUDE [Resource Policy for Microsoft.Sql 4.1](../../includes/policy/standards/asb/rp-controls/microsoft.sql-4-1.md)]

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: Bizalmas adatokat tároló vagy feldolgozó rendszerek elkülönítése

**Útmutató:** Különálló előfizetéseket és/vagy felügyeleti csoportokat implementálja a fejlesztéshez, teszteléshez és éles környezethez. Az erőforrásokat virtuális hálózattal/alhálózattal kell elválasztani, megfelelően címkézni kell, és egy hálózati biztonsági csoporton vagy hálózaton belül kell Azure Firewall. A bizalmas adatokat tároló vagy feldolgozó erőforrásokat el kell különíteni. Használja a Private Link; üzembe helyezheti Azure SQL-kiszolgálót egy virtuális hálózaton belül, és biztonságosan csatlakozhat a Private Link.

- [További Azure-előfizetések létrehozása](../cost-management-billing/manage/create-subscription.md)

- [A Management Groups](../governance/management-groups/create-management-group-portal.md)

- [Címkék létrehozása és használata](../azure-resource-manager/management/tag-resources.md)

- [A Private Link beállítása Azure SQL Database](../azure-sql/database/private-endpoint-overview.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: Bizalmas adatok jogosulatlan átvitelének figyelése és blokkolása

**Útmutató:** A Azure SQL Database SQL-készlet bizalmas adatokat tároló vagy feldolgozó erőforrásaihoz címkék használatával jelölje meg az adatbázist és a kapcsolódó erőforrásokat bizalmasként. Konfigurálja Private Link hálózati biztonsági csoport (NSG) szolgáltatáscímkéivel együtt a Azure SQL Database-példányon, hogy megakadályozza a bizalmas adatok kiszivárgását.

Emellett az Advanced Threat Protection for Azure SQL Database, Azure SQL Managed Instance és Azure Synapse észleli azokat a rendellenes tevékenységeket, amelyek az adatbázisok hozzáférésére vagy kihasználására tett szokatlan és potenciálisan káros kísérletekre utalnak.

A Microsoft által felügyelt mögöttes platform esetén a Microsoft bizalmasként kezeli az összes ügyféltartalmat, és nagy terjedelmüknek megfelelő védelmet biztosít az ügyfelek adatvesztése és kitettsége ellen. Annak érdekében, hogy az Azure-beli ügyféladatok biztonságban maradnak, a Microsoft robusztus adatvédelmi vezérlőket és képességeket valósított meg és tart karban.

- [A Private Link NSG-k konfigurálása az adatok kiszivárgásának megakadályozására a Azure SQL Database példányon](../azure-sql/database/private-endpoint-overview.md)

- [Az Advanced Threat Protection for Azure SQL Database](../azure-sql/database/threat-detection-overview.md)

- [Az ügyféladatok Azure-beli védelmének ismertetése](../security/fundamentals/protection-customer-data.md)

**Felelősség**: Megosztott

**Azure Security Center:** Nincs

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: A bizalmas adatok azonosítása aktív felderítési eszközzel

**Útmutató:** A Azure Synapse SQL-adatfelderítés &amp; besorolása funkció használata. Az Adatfelderítési besorolás beépített fejlett képességeket Azure SQL Database az adatbázisokban található bizalmas adatok felderítéséhez, besorolásához &amp; és &amp; címkézéséhez.

Az Adatfelderítési besorolás a Advanced Data Security ajánlat része, amely a fejlett SQL biztonsági képességek egységes &amp; csomagja. Az adatfelderítési besorolás a központi SQL ADS-portálon keresztül érhető el és &amp; kezelhető.

Emellett dinamikus adatmaszkolási (DDM) szabályzatot is állíthat be a Azure Portal. A DDM javaslati motorja az adatbázis bizonyos mezőit potenciálisan bizalmas mezőkként megjelöli, amelyek maszkolásra megfelelő jelöltek lehetnek.

- [Adatfelderítés és -besorolás használata a Azure SQL Serverhez](../azure-sql/database/data-discovery-and-classification-overview.md)

- [Az SQL dinamikus adatmaszkolásának Azure Synapse használata](../azure-sql/database/dynamic-data-masking-overview.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozása:** Az [Azure Biztonsági](/home/mbaldwin/docs/asb/azure-docs-pr/articles/governance/policy/samples/azure-security-benchmark.md) teljesítményteszt a Security Center alapértelmezett szabályzati kezdeményezése, amely a Security Center [javaslatainak alapja.](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/security-center-recommendations.md) A Azure Policy vezérlővel kapcsolatos összes definíciót automatikusan engedélyezi a Security Center. Az ehhez a vezérlőhez kapcsolódó riasztások esetében szükség [lehet Azure Defender](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/azure-defender.md) kapcsolódó szolgáltatásokra vonatkozó tervre.

**Azure Policy definíciók – Microsoft.Sql:**

[!INCLUDE [Resource Policy for Microsoft.Sql 4.5](../../includes/policy/standards/asb/rp-controls/microsoft.sql-4-5.md)]

### <a name="46-use-azure-rbac-access-control-to-control-access-to-resources"></a>4.6: Az Azure RBAC hozzáférés-vezérlésének használata az erőforrásokhoz való hozzáférés szabályozása érdekében 

**Útmutató:** Az Azure szerepköralapú hozzáférés-vezérlésével (Azure RBAC) kezelheti a Azure SQL SQL-készletben található adatbázisokhoz való hozzáférést.

Az engedélyezést a felhasználói fiók adatbázis-szerepkörtagságai és objektumszintű engedélyei vezérlik. Ajánlott eljárásként csak a minimálisan szükséges engedélyeket adja meg a felhasználóknak.

- [A Azure SQL és Azure Active Directory (Azure AD) integrálása hitelesítéshez](../azure-sql/database/authentication-aad-overview.md)

- [Hozzáférés vezérlése a Azure SQL Serveren](../azure-sql/database/logins-create-manage.md)

- [A hitelesítés és a hitelesítés Azure SQL](../azure-sql/database/logins-create-manage.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: Bizalmas adatok titkosítása

**Útmutató:** A transzparens adattitkosítás (TDE) az adatok titkosítása révén Azure Synapse az SQL-t a rosszindulatú offline tevékenységekkel szemben. Valós időben titkosítja és fejti vissza az adatbázist, a hozzá tartozó biztonsági másolatokat és a tranzakciónapló-fájlokat anélkül, hogy ehhez módosítani kellene az alkalmazást. Az Azure-ban a TDE alapértelmezett beállítása az, hogy a dekkot egy beépített kiszolgálói tanúsítvány védi. Másik lehetőségként használhatja az ügyfél által felügyelt TDE-t, más néven a TDE Bring Your Own Key (BYOK) támogatását. Ebben a forgatókönyvben a DEK-t titkosító TDE-védő egy ügyfél által felügyelt aszimmetrikus kulcs, amely egy ügyfél tulajdonában lévő és felügyelt Azure Key Vault-ban (az Azure felhőalapú külső kulcskezelő rendszerében) van tárolva, és soha nem hagyja el a kulcstartót.

- [A szolgáltatás által felügyelt transzparens adattitkosítás](../azure-sql/database/transparent-data-encryption-tde-overview.md)

- [Az ügyfél által felügyelt transzparens adattitkosítás](https://docs.microsoft.com/azure/azure-sql/database/transparent-data-encryption-tde-overview#customer-managed-transparent-data-encryption---bring-your-own-key)

- [A TDE bekapcsolás saját kulccsal](../azure-sql/database/transparent-data-encryption-byok-configure.md)

**Felelősség**: Megosztott

**Azure Security Center monitorozása:** Az [Azure-biztonsági](/home/mbaldwin/docs/asb/azure-docs-pr/articles/governance/policy/samples/azure-security-benchmark.md) teljesítményteszt a Security Center alapértelmezett szabályzati kezdeményezése, amely a Security Center [javaslatainak alapja.](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/security-center-recommendations.md) A Azure Policy vezérlővel kapcsolatos összes definíciót automatikusan engedélyezi a Security Center. Az ehhez a vezérlőhez kapcsolódó riasztások esetében szükség [lehet Azure Defender](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/azure-defender.md) kapcsolódó szolgáltatásokra vonatkozó tervre.

**Azure Policy definíciók – Microsoft.Sql:**

[!INCLUDE [Resource Policy for Microsoft.Sql 4.8](../../includes/policy/standards/asb/rp-controls/microsoft.sql-4-8.md)]

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: Kritikus fontosságú Azure-erőforrások változásainak naplózása és riasztása

**Útmutató:** Azure Monitor Azure-tevékenységnaplóval használva riasztásokat hozhat létre, amikor változásokra kerül sor a Synapse SQL-készletek és más kritikus vagy kapcsolódó erőforrások éles példányaiban.

Emellett az SQL Synapse-készletben található adatbázisokhoz is állíthat be riasztásokat a Azure Portal. A riasztások küldhetnek e-mailt, vagy web hook, ha egy metrika (például az adatbázis mérete vagy a CPU-használat) eléri a küszöbértéket.

- [Riasztások létrehozása az Azure-tevékenységnapló eseményeihez](../azure-monitor/alerts/alerts-activity-log.md)

- [Riasztások létrehozása Azure SQL Synapse-hoz](../azure-sql/database/alerts-insights-configure-portal.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

## <a name="vulnerability-management"></a>Biztonságirés-kezelés

*További információ: [Azure biztonsági teljesítményteszt: Biztonsági rések kezelése.](../security/benchmarks/security-control-vulnerability-management.md)*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1: Automatizált biztonsági rések ellenőrzési eszközeinek futtatása

**Útmutató:** A Advanced Data Security engedélyezése és a javaslatok Azure Security Center biztonsági rések felmérésének végrehajtása a Azure SQL adatbázisokon.

- [Sebezhetőségi felmérések futtatása Azure SQL adatbázisokon](../azure-sql/database/sql-vulnerability-assessment.md)

- [A Advanced Data Security](../azure-sql/database/azure-defender-for-sql.md)

- [Biztonsági rések felmérésére Azure Security Center javaslatok megvalósítása](../security-center/deploy-vulnerability-assessment-vm.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozása:** Az [Azure Biztonsági](/home/mbaldwin/docs/asb/azure-docs-pr/articles/governance/policy/samples/azure-security-benchmark.md) teljesítményteszt a Security Center alapértelmezett szabályzati kezdeményezése, amely a Security Center [javaslatainak alapja.](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/security-center-recommendations.md) A Azure Policy vezérlővel kapcsolatos összes definíciót automatikusan engedélyezi a Security Center. Az ehhez a vezérlőhez kapcsolódó riasztások esetében szükség [lehet Azure Defender](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/azure-defender.md) kapcsolódó szolgáltatásokra vonatkozó tervre.

**Azure Policy definíciók – Microsoft.Sql:**

[!INCLUDE [Resource Policy for Microsoft.Sql 5.1](../../includes/policy/standards/asb/rp-controls/microsoft.sql-5-1.md)]

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4: A biztonsági rések biztonsági vizsgálatának összehasonlítása

**Útmutató:** A sebezhetőségi felmérés az SQL-be Azure Synapse beépített vizsgálati szolgáltatás. A szolgáltatás a biztonsági réseket jelző szabályok tudásbázisát alkalmazza. Kiemeli az ajánlott eljárásoktól való eltéréseket, például a helytelen konfigurációkat, a túlzott engedélyeket és a nem védett bizalmas adatokat.  A sebezhetőségi felmérés a központi SQL Advanced Data Security (ADS) portálon keresztül érhető el és kezelhető.

- [Sebezhetőségi felmérési vizsgálatok kezelése és exportálása az SQL ADS portálon](../azure-sql/database/sql-vulnerability-assessment.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5: Kockázatminősítési folyamat használata a felderített biztonsági rések szervizelésének rangsorolására

**Útmutató:** Használja az alapértelmezett kockázatminősítéseket (biztonsági pontszám), amelyet a Azure Security Center.

Az &amp; adatfelderítési besorolás az SQL Azure Synapse van beépítve. Magas szintű képességeket biztosít az adatbázisban lévő bizalmas adatok felderítéséhez, osztályozásához, címkézéséhez és jelentéséhez.

- [A Azure Security Center pontszámok](../security-center/secure-score-security-controls.md)

- [Az adatfelderítési &amp; besorolások](../azure-sql/database/data-discovery-and-classification-overview.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozása:** Az [Azure Biztonsági](/home/mbaldwin/docs/asb/azure-docs-pr/articles/governance/policy/samples/azure-security-benchmark.md) teljesítményteszt a Security Center alapértelmezett szabályzati kezdeményezése, amely a Security Center [javaslatainak alapja.](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/security-center-recommendations.md) A Azure Policy vezérlővel kapcsolatos összes definíciót automatikusan engedélyezi a Security Center. Az ehhez a vezérlőhez kapcsolódó riasztások esetében szükség [lehet Azure Defender](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/azure-defender.md) kapcsolódó szolgáltatásokra vonatkozó tervre.

**Azure Policy definíciók – Microsoft.Sql:**

[!INCLUDE [Resource Policy for Microsoft.Sql 5.5](../../includes/policy/standards/asb/rp-controls/microsoft.sql-5-5.md)]

## <a name="inventory-and-asset-management"></a>Leltár-és eszközfelügyelet

*További információ: [Azure biztonsági teljesítményteszt: Leltár és eszközkezelés.](../security/benchmarks/security-control-inventory-asset-management.md)*

### <a name="61-use-automated-asset-discovery-solution"></a>6.1: Automatizált eszközfelderítési megoldás használata

**Útmutató:** A Azure Resource Graph használatával lekérdezheti és felderítheti a dedikált SQL-készlethez kapcsolódó összes erőforrást az előfizetése(i) között. Győződjön meg arról, hogy rendelkezik a megfelelő (olvasási) engedélyekkel a bérlőben, és képes enumerálni az összes Azure-előfizetést és az előfizetésen belüli erőforrásokat.

Bár a klasszikus Azure-erőforrások a Azure Resource Graph is felderíthetőek, erősen ajánlott a Azure Resource Manager erőforrásokat létrehozni és használni.

- [Lekérdezések létrehozása Azure Resource Graph](../governance/resource-graph/first-query-portal.md)

- [Az Azure-előfizetések megtekintése](/powershell/module/az.accounts/get-azsubscription)

- [Az Azure RBAC](../role-based-access-control/overview.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="62-maintain-asset-metadata"></a>6.2: Objektum metaadatainak karbantartása

**Útmutató:** Címkék alkalmazása Olyan Azure-erőforrásokra, amelyek metaadatokat adva logikailag rendszerezik őket egy taxonóniában.

- [Címkék létrehozása és használata](../azure-resource-manager/management/tag-resources.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: Nem engedélyezett Azure-erőforrások törlése

**Útmutató:** Az eszközök rendszerezéséhez és nyomon követéséhez címkézést, felügyeleti csoportokat és különálló előfizetéseket használhat, ha szükséges. Rendszeresen egyeztetni kell a leltárt, és gondoskodni kell arról, hogy a jogosulatlan erőforrások időben törlődjenek az előfizetésből.

- [További Azure-előfizetések létrehozása](../cost-management-billing/manage/create-subscription.md)

- [Új Management Groups](../governance/management-groups/create-management-group-portal.md)

- [Címkék létrehozása és használata](../azure-resource-manager/management/tag-resources.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6.4: Jóváhagyott Azure-erőforrások meghatározása és leltározása

**Útmutató:** A dedikált SQL-készlethez kapcsolódó jóváhagyott Azure-erőforrások listájának meghatározása.

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: Nem jóváhagyott Azure-erőforrások figyelése

**Útmutató:** Azure Policy az alábbi beépített szabályzatdefiníciók használatával az ügyfél-előfizetésben létre lehet hozható erőforrások típusára vonatkozó korlátozásokat:

- Nem engedélyezett erőforrástípusok

- Engedélyezett erőforrástípusok

A Azure Resource Graph lekérdezheti/felderítheti az előfizetések erőforrásait. Győződjön meg arról, hogy a környezetben jelen van minden Azure-erőforrás jóvá van hagyva.

- [Az Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md)

- [Lekérdezések létrehozása Azure Resource Graph](../governance/resource-graph/first-query-portal.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="69-use-only-approved-azure-services"></a>6.9: Csak jóváhagyott Azure-szolgáltatások használata

**Útmutató:** Azure Policy az alábbi beépített szabályzatdefiníciók használatával az ügyfél-előfizetés(ök)ben létre lehet hozható erőforrások típusára vonatkozó korlátozásokat:
- Nem engedélyezett erőforrástípusok
- Engedélyezett erőforrástípusok

A Azure Resource Graph erőforrások lekérdezéséhez/felderítéséhez használhatja az előfizetés(ek)et. Győződjön meg arról, hogy a környezetben jelen van minden Azure-erőforrás jóvá van hagyva.

- [Az Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md)

- [Adott erőforrástípus megtagadása a Azure Policy](https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#general)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6.11: Korlátozza a felhasználók számára a Azure Resource Manager

**Útmutató:** Az Azure-beli feltételes hozzáféréssel korlátozhatja a felhasználók Azure Resource Manager-hozzáférését a "Hozzáférés blokkolása" beállításával a "Microsoft Azure Management" alkalmazáshoz.

- [Feltételes hozzáférés konfigurálása a hozzáférés letilt Azure Resource Manager](../role-based-access-control/conditional-access-azure-management.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

## <a name="secure-configuration"></a>Biztonságos konfiguráció

*További információ: [Azure biztonsági teljesítményteszt: Biztonságos konfiguráció.](../security/benchmarks/security-control-secure-configuration.md)*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: Biztonságos konfigurációk létrehozása az összes Azure-erőforráshoz

**Útmutató:** Azure Policy "Microsoft.Sql" névtérben található aliasok használatával egyéni szabályzatokat hozhat létre a dedikált SQL-készlethez kapcsolódó erőforrások konfigurációjának naplózására vagy kényszerítére. Az Azure-adatbázisokhoz/-kiszolgálóhoz beépített szabályzatdefiníciókat is használhat, például:

- Fenyegetésészlelés üzembe helyezése SQL-kiszolgálókon
- SQL Server hálózati szolgáltatásvégpontot kell használnia

- [Az elérhető aliasok Azure Policy megtekintése](/powershell/module/az.resources/get-azpolicyalias)

- [Az Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: Biztonságos Azure-erőforráskonfigurációk fenntartása

**Útmutató:** A Azure Policy [deny] és a [deploy if not exist] használatával kényszerítheti a biztonságos beállításokat az Azure-erőforrásokon.

- [Az Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md)

- [A Azure Policy hatásainak](../governance/policy/concepts/effects.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Az Azure-erőforrások konfigurációjának biztonságos tárolása

**Útmutató:** Ha egyéni Azure Policy használ, használja az Azure DevOpsot vagy az Azure Repost a kód biztonságos tárolására és kezelésére.

- [Kód tárolása az Azure DevOpsban](/azure/devops/repos/git/gitworkflow)

- [Az Azure Repos dokumentációja](/azure/devops/repos/)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7: Konfigurációkezelési eszközök üzembe helyezése Azure-erőforrásokhoz

**Útmutató:** Nem alkalmazható; Azure Synapse SQL nem rendelkezik konfigurálható biztonsági beállításokkal.

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9: Automatizált konfigurációfigyelés megvalósítása Azure-erőforrásokhoz

**Útmutató:** A Azure Security Center a dedikált SQL-készlethez kapcsolódó erőforrások alapkonfiguráció-vizsgálatának elvégzéséhez.

- [Javaslatok szervizlének Azure Security Center](../security-center/security-center-remediate-recommendations.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="711-manage-azure-secrets-securely"></a>7.11: Azure-beli titkos kulcsok biztonságos kezelése

**Útmutató:** transzparens adattitkosítás (TDE) az Azure Key Vault-ban ügyfél által kezelt kulcsokkal lehetővé teszi az automatikusan létrehozott adatbázis-titkosítási kulcs (DEK) titkosítását a TDE Protector nevű, ügyfél által felügyelt aszimmetrikus kulccsal. Ezt általában a Bring Your Own Key (BYOK) támogatásaként transzparens adattitkosítás. A BYOK-forgatókönyvben a TDE-védőt egy ügyfél tulajdonában lévő és felügyelt Azure Key Vault. Emellett győződjön meg arról, hogy a szoftverben engedélyezve van a Azure Key Vault.

- [A TDE engedélyezése ügyfél által felügyelt kulccsal Azure Key Vault](../azure-sql/database/transparent-data-encryption-byok-configure.md)

- [A szoftveres törlés engedélyezése a Azure Key Vault](../key-vault/general/key-vault-recovery.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: Identitások biztonságos és automatikus kezelése

**Útmutató:** Felügyelt identitások használata az Azure-szolgáltatások automatikus felügyelt identitásának Azure Active Directory (Azure AD) szolgáltatásban. A Managed Identities lehetővé teszi az Azure AD-hitelesítést támogató bármely szolgáltatásban való hitelesítést, beleértve a Azure Key Vault szolgáltatást is, anélkül, hogy a hitelesítő adatokat a kódban lenne.

- [Oktatóanyag: Hozzáférés az Azure SQL-hez egy Windows VM rendszer által hozzárendelt felügyelt identitásával](../active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-sql.md)

- [Felügyelt identitások konfigurálása](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: A hitelesítő adatok nem szándékolt kitettségének megszüntetése

**Útmutató:** A hitelesítőadat-ellenőrző implementálja a hitelesítő adatokat a kódban. A Credential Scanner a felfedezett hitelesítő adatok biztonságosabb helyre, például az Azure Key Vaultba való áthelyezésére is javaslatot tesz.

- [Hitelesítőadat-ellenőrző beállítása](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

## <a name="malware-defense"></a>Kártevők elleni védelem

*További információ: [Azure biztonsági teljesítményteszt: Kártevők elleni védelem.](../security/benchmarks/security-control-malware-defense.md)*

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2: A nem számítási Azure-erőforrásokba feltölteni szükséges fájlok előzetes beolvasása

**Útmutató:** A Microsoft kártevőirtó engedélyezve van az Azure-szolgáltatásokat támogató mögöttes gazdagépen (Azure Synapse SQL); az ügyféltartalomon azonban nem fut.

A nem számítási Azure-erőforrásokra ( például App Service, Data Lake Storage, Blob Storage, Azure SQL Server stb.) feltöltött tartalmak előzetes beolvasása. A Microsoft ezekben az esetekben nem fér hozzá az Ön adataihoz.

- [A Microsoft Antimalware és a Azure Cloud Services Virtual Machines](../security/fundamentals/antimalware.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

## <a name="data-recovery"></a>Adat-helyreállítás

*További információ: [Azure biztonsági teljesítményteszt: Adat-helyreállítás.](../security/benchmarks/security-control-data-recovery.md)*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: Rendszeres automatikus biztonságimentek biztosítása

**Útmutató:** A dedikált SQL-készletről a nap folyamán automatikusan pillanatképek készítve, amelyek hét napig elérhető visszaállítási pontokat hoznak létre. Ez a megőrzési időszak nem módosítható. A dedikált SQL-készlet egy nyolcórás helyreállítási időkorrektort (RPO) támogat. Az adattárházat visszaállíthatja az elsődleges régióban az elmúlt hét napban készült bármely pillanatképből. Vegye figyelembe, hogy szükség esetén manuálisan is aktiválhat pillanatképeket.

- [Biztonsági mentés és visszaállítás dedikált SQL-készletben](sql-data-warehouse/backup-and-restore.md)

**Felelősség**: Megosztott

**Azure Security Center monitorozása:** Az [Azure-biztonsági](/home/mbaldwin/docs/asb/azure-docs-pr/articles/governance/policy/samples/azure-security-benchmark.md) teljesítményteszt a Security Center alapértelmezett szabályzati kezdeményezése, amely a Security Center [javaslatainak alapja.](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/security-center-recommendations.md) A Azure Policy vezérlővel kapcsolatos összes definíciót automatikusan engedélyezi a Security Center. Az ehhez a vezérlőhez kapcsolódó riasztások esetében szükség [lehet Azure Defender](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/azure-defender.md) kapcsolódó szolgáltatásokra vonatkozó tervre.

**Azure Policy definíciók – Microsoft.Sql:**

[!INCLUDE [Resource Policy for Microsoft.Sql 9.1](../../includes/policy/standards/asb/rp-controls/microsoft.sql-9-1.md)]

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: Teljes rendszer-biztonsági mentések és ügyfél által kezelt kulcsok biztonsági mentése

**Útmutató:** Az adattárházról a nap folyamán automatikusan készít pillanatképeket, és hét napig elérhető visszaállítási pontokat hoz létre. Ez a megőrzési időszak nem módosítható. A dedikált SQL-készlet a helyreállítási időkorrekta (RPO) nyolcórás célkitűzését támogatja. Az adattárházat az elsődleges régióban az elmúlt hét napban készült bármely pillanatképből visszaállíthatja. Vegye figyelembe, hogy szükség esetén manuálisan is aktiválhat pillanatképeket.

Ha ügyfél által felügyelt kulcsot használ az adatbázistitkosítási kulcs titkosításához, győződjön meg arról, hogy a kulcs biztonsági mentése folyamatban van.

- [Biztonsági mentés és visszaállítás dedikált SQL-készletben](sql-data-warehouse/backup-and-restore.md)

- [Biztonsági másolat készítése Azure Key Vault kulcsokról](/powershell/module/az.keyvault/backup-azkeyvaultkey)

**Felelősség**: Megosztott

**Azure Security Center monitorozása:** Az [Azure Biztonsági](/home/mbaldwin/docs/asb/azure-docs-pr/articles/governance/policy/samples/azure-security-benchmark.md) teljesítményteszt a Security Center alapértelmezett szabályzati kezdeményezése, amely a Security Center [javaslatainak alapja.](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/security-center-recommendations.md) A Azure Policy vezérlővel kapcsolatos összes definíciót automatikusan engedélyezi a Security Center. Az ehhez a vezérlőhez kapcsolódó riasztások esetében szükség [lehet Azure Defender](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/azure-defender.md) kapcsolódó szolgáltatásokra vonatkozó tervre.

**Azure Policy definíciók – Microsoft.Sql:**

[!INCLUDE [Resource Policy for Microsoft.Sql 9.2](../../includes/policy/standards/asb/rp-controls/microsoft.sql-9-2.md)]

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: Az összes biztonsági másolat ellenőrzése, beleértve az ügyfél által kezelt kulcsokat is

**Útmutató:** Rendszeres időközönként tesztelje a visszaállítási pontokat, hogy a pillanatképek érvényesek-e. Meglévő dedikált SQL-készlet visszaállítási pontból való visszaállításához használhatja a Azure Portal Vagy a PowerShellt. Tesztelje az ügyfél által kezelt biztonságimentés-kulcsok visszaállítását.

- [Új kulcsok Azure Key Vault visszaállítása](/powershell/module/az.keyvault/restore-azkeyvaultkey)

- [Biztonsági mentés és visszaállítás dedikált SQL-készletben](sql-data-warehouse/backup-and-restore.md)

- [Meglévő dedikált SQL-készlet visszaállítása](sql-data-warehouse/sql-data-warehouse-restore-active-paused-dw.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: A biztonsági másolatok és az ügyfél által kezelt kulcsok védelmének biztosítása

**Útmutató:** A Azure SQL Database-ben konfigurálhat egy önálló vagy egy készletbekészletett adatbázist hosszú távú biztonsági mentési megőrzési szabályzat (LTR) használatával, hogy az adatbázis biztonsági másolatai automatikusan külön Azure Blob Storage-tárolókban őrizzék meg legfeljebb 10 évig. Az Azure Storage-ban tárolt adatok titkosítása és visszafejtése transzparens módon, 256 bites AES-titkosítással történik, amely az egyik legerősebb elérhető blokktitkosítás, és FIPS 140-2 szabványnak megfelelő.

Alapértelmezés szerint a tárfiókban található adatok a Microsoft által kezelt kulcsokkal vannak titkosítva. Az adatok titkosítása a Microsoft által felügyelt kulcsokkal, vagy a saját kulcsokkal is kezelhető. Ha saját kulcsait kezeli a Key Vault, győződjön meg arról, hogy a soft-delete engedélyezve van.

- [Biztonsági Azure SQL Database hosszú távú megőrzésének kezelése](../azure-sql/database/long-term-backup-retention-configure.md)

- [Inaktív adatok Azure Storage-titkosítása](../storage/common/storage-service-encryption.md)

- [A soft delete engedélyezése a Key Vault](../storage/blobs/soft-delete-blob-overview.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

## <a name="incident-response"></a>Incidensmegoldás

*További információ: [Azure Security Benchmark: Incidensek kezelése](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10.1: Incidensválasz-útmutató létrehozása

**Útmutató:** Győződjön meg arról, hogy vannak olyan írásos incidenskezelési tervek, amelyek meghatározzák a személyzet szerepköreit, valamint az incidenskezelés/-kezelés fázisát.

- [Munkafolyamat-automatizálások konfigurálása a Azure Security Center](../security-center/security-center-planning-and-operations-guide.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: Incidenspontozási és -rangsorolási eljárás létrehozása

**Útmutató:** Security Center súlyosságot rendel a riasztáshoz, hogy segítsen priorizálni az egyes riasztások kiosztásának sorrendjét, így ha egy erőforrás biztonsága sérül, azonnal elérhető lesz. A súlyosság azon alapul, hogy Security Center a riasztást kiadó eredményben vagy a metrikában, valamint a riasztáshoz vezető tevékenység mögötti rosszindulatú szándék megbízhatósági szinten alapul.

- [Biztonsági riasztások az Azure Security Centerben](../security-center/security-center-alerts-overview.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="103-test-security-response-procedures"></a>10.3: Biztonsági reagálási eljárások tesztelése

**Útmutató:** Gyakorlatok a rendszerek incidensválasz-képességeinek rendszeres teszteléséhez. Azonosítsa a gyenge pontokat és réseket, és szükség esetén dolgozza át a tervet.

- [Tekintse meg az NIST kiadványát: Útmutató az it-csomagokhoz és képességekhez: Útmutató a teszteléshez, a betanításhoz és a gyakorlathoz programokhoz](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: Adja meg a biztonsági incidens kapcsolattartási adatait, és konfigurálja a riasztási értesítéseket a biztonsági incidensek esetén

**Útmutató:** A Biztonsági incidens kapcsolattartási adatait a Microsoft arra használja, hogy kapcsolatba lépjen Önnel, ha a Microsoft Biztonsági reagálási központ (MSRC) azt tapasztalja, hogy illetéktelen vagy jogosulatlan fél fért hozzá adataihoz.

- [A biztonsági kapcsolattartó Azure Security Center beállítása](../security-center/security-center-provide-security-contact-details.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: Biztonsági riasztások beépítése az incidensválasz-rendszerbe

**Útmutató:** A Azure Security Center riasztások és javaslatok exportálása a Folyamatos exportálás funkcióval. A folyamatos exportálás lehetővé teszi, hogy manuálisan vagy folyamatosan exportálja a riasztásokat és javaslatokat. A riasztásokat a Azure Security Center adat-összekötővel streamelheti a Azure Sentinel.

- [Folyamatos exportálás konfigurálása](../security-center/continuous-export.md)

- [Riasztások streamelése az Azure Sentinelbe](../sentinel/connect-azure-security-center.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: A biztonsági riasztások válaszának automatizálása

**Útmutató:** A Azure Security Center munkafolyamat-automatizálási funkcióját használhatja a biztonsági riasztások és javaslatok "Logic Apps" funkcióval történő automatikus aktiválásához.

- [A munkafolyamat-automatizálás és -Logic Apps](../security-center/workflow-automation.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

## <a name="penetration-tests-and-red-team-exercises"></a>Behatolási tesztek és Red Team-gyakorlatok

*További információ: [Azure biztonsági teljesítményteszt: Behatolási tesztek és Red Team-gyakorlatok.](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md)*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11.1: Az Azure-erőforrások rendszeres behatolási tesztelése és az összes kritikus fontosságú biztonsági eredmény szervizelésének biztosítása

**Útmutató:** Kövesse a Microsoft Engagement-szabályokat annak biztosításához, hogy a behatolási tesztek ne sértsék meg a Microsoft szabályzatát: https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1 .

- [A Microsoft által a Microsoft által felügyelt felhőinfra infrastruktúrán, szolgáltatásokon és alkalmazásokon végzett red teaming- és élő webhely-behatolási tesztekre vonatkozó stratégiájáról és végrehajtásáról itt talál további információt.](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

## <a name="next-steps"></a>Következő lépések

- [Az Azure Security Benchmark v2 áttekintésének](/azure/security/benchmarks/overview) megtekintése
- További tudnivalók az [Azure biztonsági alapterveiről](/azure/security/benchmarks/security-baselines-overview)
