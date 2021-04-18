---
title: Az Azure biztonsági alapkonfigurációja Azure SQL Database
description: A Azure SQL Database alapkonfigurációja eljárásokkal kapcsolatos útmutatást és forrásforrásokat biztosít az Azure biztonsági teljesítménytesztben meghatározott biztonsági javaslatok megvalósításához.
author: msmbaldwin
ms.service: sql-database
ms.topic: conceptual
ms.date: 03/30/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 29db5b82d73bf96465581ccd6a663455464bbeb9
ms.sourcegitcommit: 950e98d5b3e9984b884673e59e0d2c9aaeabb5bb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2021
ms.locfileid: "107599570"
---
# <a name="azure-security-baseline-for-azure-sql-database"></a>Az Azure biztonsági alapkonfigurációja Azure SQL Database

Ez a biztonsági alapkonfiguráció az Azure Biztonsági teljesítményteszt [1.0-s](../../security/benchmarks/overview-v1.md) verziójának útmutatását alkalmazza a Azure SQL Database. Az Azure Security Benchmark ajánlásokat ad arra nézve, hogy hogyan tehetők biztonságossá a felhőalapú megoldások az Azure-ban. A tartalmat az Azure  biztonsági teljesítményteszt által meghatározott biztonsági vezérlők és a kapcsolódó, az azure-beli biztonsági Azure SQL Database. **A** Azure SQL Database, illetve a Microsoft felelősségi útjára vonatkozó nem alkalmazható vezérlők ki vannak zárva. Ha meg Azure SQL Database, hogyan lehet teljes mértékben leképezni az Azure-biztonsági teljesítménytesztre, tekintse meg a teljes Azure Monitor alapkonfiguráció [leképezési fájlját.](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)

## <a name="network-security"></a>Hálózati biztonság

*További információ: [Azure Security Benchmark: Hálózati biztonság](../../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1.1: Azure-erőforrások védelme virtuális hálózatokon belül

**Útmutató:** Engedélyezheti a Azure Private Link az Azure PaaS-szolgáltatások (például SQL Database) és az Azure által üzemeltetett ügyfél-/partnerszolgáltatások privát végponton keresztüli hozzáférését a virtuális hálózatban. A virtuális hálózat és a szolgáltatás közötti forgalom a Microsoft gerinchálózatán keresztül halad át, így kiküszöböli a nyilvános internet jelentette kitettséget. 

Ahhoz, hogy a forgalom elér Azure SQL Database, az SQL-szolgáltatáscímkék használatával engedélyezze a kimenő forgalmat a hálózati biztonsági csoportokon keresztül.

A virtuális hálózati szabályok lehetővé Azure SQL Database, hogy csak a virtuális hálózat kiválasztott alhálózatairól küldött kommunikációt fogadják el.

- [A Private Link beállítása Azure SQL Database](/azure/sql-database/sql-database-private-endpoint-overview#how-to-set-up-private-link-for-azure-sql-database)

- [Virtuális hálózati szolgáltatásvégpont és -szabályok használata adatbázis-kiszolgálókhoz](/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview)

**Felelősség**: Ügyfél

**Azure Security Center monitorozása:** Az [Azure-biztonsági](/azure/governance/policy/samples/azure-security-benchmark) teljesítményteszt a Security Center alapértelmezett szabályzati kezdeményezése, amely a Security Center [javaslatainak alapja.](/azure/security-center/security-center-recommendations) A Azure Policy vezérlővel kapcsolatos összes definíciót automatikusan engedélyezi a Security Center. Az ehhez a vezérlőhez kapcsolódó riasztások esetében szükség [lehet Azure Defender](/azure/security-center/azure-defender) kapcsolódó szolgáltatásokra vonatkozó tervre.

**Azure Policy definíciók – Microsoft.Sql:**

[!INCLUDE [Resource Policy for Microsoft.Sql 1.1](../../../includes/policy/standards/asb/rp-controls/microsoft.sql-1-1.md)]

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-network-interfaces"></a>1.2: Virtuális hálózatok, alhálózatok és hálózati adapterek konfigurációjának és forgalmának figyelése és naplózása

**Útmutató:** Használja Azure Security Center a hálózati védelemre vonatkozó javaslatokat azon alhálózathoz, Azure SQL Database a kiszolgáló telepítve van. 

Az Azure SQL Database Server-példányhoz csatlakozó Azure Virtual Machines (VM) számára engedélyezze a hálózati biztonsági csoport (NSG) forgalmi naplóit a virtuális gépeket védő NSG-k számára, és küldje el a naplókat egy Azure Storage-fiókba a forgalom naplózásához. 

Az NSG-forgalom naplóit egy Log Analytics-munkaterületre is elküldheti, és a Traffic Analytics segítségével betekintést nyerhet az Azure-felhő adatforgalmába. A hálózati Traffic Analytics néhány előnye a hálózati tevékenység vizualizációja és a gyorspontok azonosítása, a biztonsági fenyegetések azonosítása, a forgalmi minták azonosítása és a hálózati helytelen konfigurációk azonosítása.

- [NSG-forgalom naplóinak engedélyezése](../../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [A hálózati biztonság Azure Security Center](../../security-center/security-center-network-recommendations.md)

- [Az alkalmazás engedélyezése és Traffic Analytics](../../network-watcher/traffic-analytics.md)

- [A hálózati biztonság Azure Security Center](../../security-center/security-center-network-recommendations.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: Az ismert kártékony IP-címekkel való kommunikáció megtagadása

**Útmutató:** Engedélyezze DDoS Protection Standard szabványt a SQL Server-példányokhoz társított virtuális hálózatokon az elosztott szolgáltatásmegtagadásos támadások elleni védelem érdekében. Az Azure Security Center intelligencia használatával megtagadhatja az ismert rosszindulatú vagy nem használt internetes IP-címekkel való kommunikációt.

- [A DDoS Protection konfigurálása](/azure/virtual-network/manage-ddos-protection)

- [Az Azure Security Center fenyegetésintelligencia](/azure/security-center/security-center-alerts-data-services)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="15-record-network-packets"></a>1.5: Hálózati csomagok rekordja

**Útmutató:** A Azure SQL Database-példányhoz csatlakozó Azure Virtual Machines (virtuális gépek) számára engedélyezze a hálózati biztonsági csoport (NSG) forgalmi naplóit az NSG-k számára a virtuális gépek védelméhez, és küldje el a naplókat egy Azure Storage-fiókba a forgalom naplózásához. Ha a rendellenes tevékenységek kivizsgálásához szükséges, engedélyezze a Network Watcher rögzítését.

- [NSG-forgalom naplóinak engedélyezése](../../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [A Network Watcher](../../network-watcher/network-watcher-create.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6: Hálózatalapú behatolásészlelési/behatolásvédelmi rendszerek (IDS/IPS) üzembe helyezése

**Útmutató:** Az Advanced Threat Protection (ATP) engedélyezése Azure SQL Database.  A felhasználók riasztást kapnak a gyanús adatbázis-tevékenységekről, a lehetséges biztonsági résekről és az SQL-injektálási támadásokról, valamint a rendellenes adatbázis-hozzáférési és lekérdezési mintákról. Az Advanced Threat Protection a riasztásokat a Azure Security Center. 

- [Az Advanced Threat Protection for Azure SQL Database](/azure/sql-database/sql-database-threat-detection-overview)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: A hálózati biztonsági szabályok összetettségének és adminisztratív terhelésének minimalizálása

**Útmutató:** Hálózati biztonsági csoportok vagy hálózati biztonsági csoportok hálózati hozzáférés-vezérlésének meghatározása virtuális hálózati szolgáltatáscímkék Azure Firewall. Biztonsági szabályok létrehozása során szolgáltatáscímkéket használhat bizonyos IP-címek helyett. A szolgáltatáscímke nevének (például ApiManagement) a szabály megfelelő forrás- vagy célmezőben való megadásával engedélyezheti vagy megtagadhatja a megfelelő szolgáltatás forgalmát. A Microsoft kezeli a szolgáltatáscímke által felölelt címelőtagokat, és automatikusan frissíti a szolgáltatáscímkéket a címek változásával.

Ha szolgáltatásvégpontokat használ Azure SQL Database szolgáltatáshoz, Azure SQL Database nyilvános IP-címekre kimenő forgalom szükséges: A hálózati biztonsági csoportokat (NSG-ket) Azure SQL Database IP-címekre kell megnyitni a csatlakozás engedélyezése érdekében. Ezt az NSG-szolgáltatáscímkék használatával Azure SQL Database.

- [A szolgáltatáscímkék és a szolgáltatásvégpont Azure SQL Database](/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview#limitations)

- [A szolgáltatáscímkék használata és használata](../../virtual-network/service-tags-overview.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: A hálózati eszközök szabványos biztonsági konfigurációjának fenntartása

**Útmutató:** Hálózati biztonsági konfigurációk meghatározása és implementációja Azure SQL Database kiszolgálópéldányokhoz a Azure Policy. A "Microsoft.Sql" névtér használatával egyéni szabályzatdefiníciókat definiálhat, vagy használhatja a kiszolgálói hálózatvédelemhez Azure SQL Database beépített szabályzatdefiníciókat. A virtuális kiszolgálóra vonatkozó beépített hálózati biztonsági házirend például a Azure SQL Database következő lehet: "SQL Server virtuális hálózati szolgáltatásvégpontot kell használnia".

 

Az Azure Blueprints használatával leegyszerűsítheti a nagy méretű Azure-környezeteket, mivel egyetlen tervdefinícióba kell becsomagolásával az olyan kulcsfontosságú környezeti összetevők, mint az Azure Resource Management-sablonok, az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC) és a szabályzatok. Könnyedén alkalmazhatja a tervet az új előfizetésekhez és környezetekhez, és finomhangolhatja a vezérlést és a felügyeletet a verziószámozás segítségével.

- [Az Azure Policy konfigurálása és kezelése](../../governance/policy/tutorials/create-and-manage.md)

- [Azure Blueprints-terv létrehozása](../../governance/blueprints/create-blueprint-portal.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="110-document-traffic-configuration-rules"></a>1.10: A forgalom konfigurációs szabályainak dokumentálása

**Útmutató:** Címkék használata hálózati biztonsági csoportokhoz (NSG-khez) és egyéb, a hálózati biztonsághoz és forgalomhoz kapcsolódó erőforrásokhoz. Egyéni NSG-szabályoknál a "Leírás" mezőben adhatja meg az üzleti igényeket és/vagy időtartamot (stb.) a hálózatra/hálózatról be- és vissza forgalmat engedélyező szabályokhoz.

A címkézéshez kapcsolódó beépített Azure Policy-definíciók (például "Címke és érték megkövetelése" ) használatával biztosíthatja, hogy az összes erőforrás címkékkel legyen létrehozva, és értesítse Önt a meglévő, címkézetlen erőforrásokról.

A címkék alapján Azure PowerShell vagy Azure CLI használatával erőforrásokat kereshet vagy hajthat végre műveleteken.

- [Címkék létrehozása és használata](/azure/azure-resource-manager/resource-group-using-tags)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: Automatizált eszközök használata a hálózati erőforrások konfigurációjának figyelése és a változások észlelése érdekében

**Útmutató:** Az Azure-tevékenységnaplóval monitorozható a hálózati erőforrások konfigurációja, és észlelhetők a kiszolgálópéldányokhoz Azure SQL Database hálózati erőforrások változásai. Hozzon létre riasztásokat a Azure Monitor, amelyek akkor aktiválódnak, ha a kritikus hálózati erőforrások módosulnak.

- [Az Azure-tevékenységnapló eseményeinek megtekintése és lekérése](/azure/azure-monitor/platform/activity-log-view)

- [Riasztások létrehozása a Azure Monitor](/azure/azure-monitor/platform/alerts-activity-log)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

## <a name="logging-and-monitoring"></a>Naplózás és monitorozás

*További információ: [Azure biztonsági teljesítményteszt: Naplózás és monitorozás.](../../security/benchmarks/security-control-logging-monitoring.md)*

### <a name="22-configure-central-security-log-management"></a>2.2: Központi biztonsági naplókezelés konfigurálása

**Útmutató:** Naplózás engedélyezése a Azure SQL Database az adatbázis-események nyomon követéséhez és az Azure Storage-fiókban, a Log Analytics-munkaterületen vagy az Event Hubs.

Emellett streamelheti Azure SQL diagnosztikai telemetriát az Azure SQL Analytics felhőalapú megoldásba, amely monitorozza a Azure SQL Database-adatbázisok és a felügyelt Azure SQL-példányok teljesítményét nagy méretekben és több előfizetésben. Segítségével összegyűjtheti és vizualizálhatja a Azure SQL Database metrikákat, és beépített intelligenciával rendelkezik a teljesítmény-hibaelhárításhoz.

- [Naplózás beállítása a Azure SQL Database](/azure/sql-database/sql-database-auditing)

- [Platformnaplók és -metrikák gyűjtése Azure Monitor](/azure/sql-database/sql-database-metrics-diag-logging)

- [Diagnosztika streamelése a Azure SQL Analytics](/azure/sql-database/sql-database-metrics-diag-logging#stream-into-azure-sql-analytics)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Azure-erőforrások naplózásának engedélyezése

**Útmutató:** Engedélyezze a naplózást a Azure SQL Database-kiszolgálópéldányon, és válasszon egy tárolási helyet az auditnaplók számára (Azure Storage, Log Analytics vagy Event Hub).

- [Naplózás engedélyezése a Azure SQL kiszolgálón](/azure/sql-database/sql-database-auditing)

**Felelősség**: Ügyfél

**Azure Security Center monitorozása:** Az [Azure-biztonsági](/azure/governance/policy/samples/azure-security-benchmark) teljesítményteszt a Security Center alapértelmezett szabályzati kezdeményezése, amely a Security Center [javaslatainak alapja.](/azure/security-center/security-center-recommendations) A Azure Policy vezérlővel kapcsolatos összes definíciót automatikusan engedélyezi a Security Center. Az ehhez a vezérlőhez kapcsolódó riasztások esetében szükség [lehet Azure Defender](/azure/security-center/azure-defender) kapcsolódó szolgáltatásokra vonatkozó tervre.

**Azure Policy definíciók – Microsoft.Sql:**

[!INCLUDE [Resource Policy for Microsoft.Sql 2.3](../../../includes/policy/standards/asb/rp-controls/microsoft.sql-2-3.md)]

### <a name="25-configure-security-log-storage-retention"></a>2.5: A biztonsági naplók tárolásának megőrzésének konfigurálása

**Útmutató:** Ha a naplókat Azure SQL Database Log Analytics-munkaterületen tárolja, állítsa be a naplómegőrzési megőrzési időszakot a szervezet megfelelőségi szabályozásának megfelelően.

- [Naplómegőrzési paraméterek beállítása](/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period)

**Felelősség**: Ügyfél

**Azure Security Center monitorozása:** Az [Azure Biztonsági](/azure/governance/policy/samples/azure-security-benchmark) teljesítményteszt a Security Center alapértelmezett szabályzati kezdeményezése, amely a Security Center [javaslatainak alapja.](/azure/security-center/security-center-recommendations) A Azure Policy vezérlővel kapcsolatos összes definíciót automatikusan engedélyezi a Security Center. Az ehhez a vezérlőhez kapcsolódó riasztások esetében szükség [lehet Azure Defender](/azure/security-center/azure-defender) kapcsolódó szolgáltatásokra vonatkozó tervre.

**Azure Policy definíciók – Microsoft.Sql:**

[!INCLUDE [Resource Policy for Microsoft.Sql 2.5](../../../includes/policy/standards/asb/rp-controls/microsoft.sql-2-5.md)]

### <a name="26-monitor-and-review-logs"></a>2.6: Naplók figyelése és áttekintése

**Útmutató:** Naplók elemzése és figyelése rendellenes viselkedések kereséséhez és az eredmények rendszeres áttekintéséhez. Az Azure Security Center Komplex veszélyforrások elleni védelem használatával riasztást küld a Azure SQL Database-példányhoz kapcsolódó szokatlan tevékenységekről. Másik lehetőségként a metrikák értékei vagy az Azure-tevékenységnapló azon bejegyzései alapján is konfigurálhatja a riasztásokat, amelyek a Azure SQL Database kapcsolódnak.

- [Az Advanced Threat Protection és a riasztások Azure SQL Serverhez](/azure/sql-database/sql-database-threat-detection-overview)

- [Egyéni riasztások konfigurálása a Azure SQL Database](alerts-insights-configure-portal.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7: Riasztások engedélyezése rendellenes tevékenységekhez

**Útmutató:** Azure Security Center Komplex veszélyforrások elleni védelem használata Azure SQL adatbázisokhoz a rendellenes tevékenységek figyelése és riasztása érdekében. Sql Azure Defender adatbázisok sql-adatbázishoz való beállításának engedélyezése. Azure Defender SQL-hez elérhető funkciókkal észlelhetők és osztályozhatóak a bizalmas adatok, felderíthetőek és enyhíthetőek az adatbázisok lehetséges biztonsági rései, valamint észlelhetők azok a rendellenes tevékenységek, amelyek fenyegetést jelenthetnek az adatbázisra nézve.

- [Az Advanced Threat Protection és a riasztások Azure SQL Database](/azure/sql-database/sql-database-threat-detection-overview)

- [Sql-Azure Defender engedélyezése a Azure SQL Database](azure-defender-for-sql.md)

- [Riasztások kezelése a Azure Security Center](../../security-center/security-center-managing-and-responding-alerts.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozása:** Az [Azure-biztonsági](/azure/governance/policy/samples/azure-security-benchmark) teljesítményteszt a Security Center alapértelmezett szabályzati kezdeményezése, amely a Security Center [javaslatainak alapja.](/azure/security-center/security-center-recommendations) A Azure Policy vezérlővel kapcsolatos összes definíciót automatikusan engedélyezi a Security Center. Az ehhez a vezérlőhez kapcsolódó riasztások esetében szükség [lehet Azure Defender](/azure/security-center/azure-defender) kapcsolódó szolgáltatásokra vonatkozó tervre.

**Azure Policy definíciók – Microsoft.Sql:**

[!INCLUDE [Resource Policy for Microsoft.Sql 2.7](../../../includes/policy/standards/asb/rp-controls/microsoft.sql-2-7.md)]

## <a name="identity-and-access-control"></a>Identitás- és hozzáférés-vezérlés

*További információ: [Azure Biztonsági teljesítményteszt: Identitás és Access Control.](../../security/benchmarks/security-control-identity-access-control.md)*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: A rendszergazdai fiókok nyilvántartása

**Útmutató:** Azure Active Directory (Azure AD) beépített szerepkörökkel rendelkezik, amelyekhez explicit módon kell hozzárendelni és lekérdezhetőnek kell lennie. Az Azure AD PowerShell-modullal alkalmi lekérdezéseket hajt végre a rendszergazdai csoportok tagjainak felderítéséhez.

- [Címtárszerepk lekért szerepkör az Azure AD-ban a PowerShell használatával](/powershell/module/azuread/get-azureaddirectoryrole)

- [Címtárbeli szerepkör tagjainak lekérte az Azure AD-ban a PowerShell használatával](/powershell/module/azuread/get-azureaddirectoryrolemember)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="32-change-default-passwords-where-applicable"></a>3.2: Az alapértelmezett jelszavak módosítása, ha vannak

**Útmutató:** Azure Active Directory (Azure AD) nem rendelkezik az alapértelmezett jelszavak fogalmával. Egy virtuális Azure SQL Database kiépítésekor javasoljuk, hogy integrálja a hitelesítést az Azure AD-val.

- [Azure AD-hitelesítés konfigurálása és kezelése Azure SQL](/azure/azure-sql/database/authentication-aad-configure)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: Dedikált rendszergazdai fiókok használata

**Útmutató:** Házirendek és eljárások létrehozása a dedikált rendszergazdai fiókok használatával kapcsolatban. Az Azure Security Center és hozzáférés-kezelés használatával figyelheti a rendszergazdai fiókok számát.

- [Az Azure Security Center és a hozzáférések](../../security-center/security-center-identity-access.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: Többtényezős hitelesítés használata minden Azure Active Directory-alapú hozzáféréshez

**Útmutató:** Engedélyezze Azure Active Directory (Azure AD) többtényezős hitelesítést, és kövesse Azure Security Center identitás- és hozzáférés-kezelésre vonatkozó javaslatokat.

- [Többtényezős hitelesítés engedélyezése az Azure-ban](../../active-directory/authentication/howto-mfa-getstarted.md)

- [Identitás és hozzáférés figyelése a Azure Security Center](../../security-center/security-center-identity-access.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="36-use-secure-azure-managed-workstations-for-administrative-tasks"></a>3.6: Biztonságos, Azure által felügyelt munkaállomások használata felügyeleti feladatokhoz

**Útmutató:** Emelt szintű hozzáférésű munkaállomás (PAW) használata többtényezős hitelesítéssel, amely az Azure-erőforrásokba való bejelentkezésre és konfigurálásra van konfigurálva.

- [Tudnivalók a Privileged Access munkaállomásról](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

- [Többtényezős hitelesítés engedélyezése az Azure-ban](../../active-directory/authentication/howto-mfa-getstarted.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3.7: A rendszergazdai fiókokból származó gyanús tevékenységek naplózása és riasztása

**Útmutató:** Azure Active Directory (Azure AD) biztonsági jelentések használata naplók és riasztások generálása érdekében, ha gyanús vagy nem biztonságos tevékenység történik a környezetben.

Az Advanced Threat Protection Azure SQL Database észlelheti azokat a rendellenes tevékenységeket, amelyek az adatbázisok hozzáférésére vagy kihasználására tett szokatlan és potenciálisan káros kísérletekre utalnak.

- [A kockázatos tevékenységek miatt megjelölt Azure AD-felhasználók azonosítása](/azure/active-directory/reports-monitoring/concept-user-at-risk)

- [A felhasználók identitás- és hozzáférési tevékenységének figyelése a Azure Security Center](../../security-center/security-center-identity-access.md)

- [Az Advanced Threat Protection és a lehetséges riasztások áttekintése](https://docs.microsoft.com/azure/azure-sql/database/threat-detection-overview#alerts)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: Azure-erőforrások kezelése csak jóváhagyott helyekről

**Útmutató:** A feltételes hozzáférés elnevezett helyeinek használatával engedélyezheti, hogy a portál és az Azure Resource Management csak az IP-címtartományok vagy országok/régiók meghatározott logikai csoportosításaiból elérést biztosítson.

- [Elnevezett helyek konfigurálása az Azure-ban](../../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="39-use-azure-active-directory"></a>3.9: A Azure Active Directory

**Útmutató:** Hozzon létre Azure Active Directory (Azure AD) rendszergazdát a Azure SQL Database kiszolgálópéldányokhoz.

- [Azure AD-hitelesítés konfigurálása és kezelése Azure SQL](authentication-aad-configure.md)

- [Azure AD-példány létrehozása és konfigurálása](../../active-directory-domain-services/tutorial-create-instance.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozása:** Az [Azure-biztonsági](/azure/governance/policy/samples/azure-security-benchmark) teljesítményteszt a Security Center alapértelmezett szabályzati kezdeményezése, amely a Security Center [javaslatainak alapja.](/azure/security-center/security-center-recommendations) A Azure Policy vezérlővel kapcsolatos összes definíciót automatikusan engedélyezi a Security Center. Az ehhez a vezérlőhez kapcsolódó riasztások esetében szükség [lehet Azure Defender](/azure/security-center/azure-defender) kapcsolódó szolgáltatásokra vonatkozó tervre.

**Azure Policy definíciók – Microsoft.Sql:**

[!INCLUDE [Resource Policy for Microsoft.Sql 3.9](../../../includes/policy/standards/asb/rp-controls/microsoft.sql-3-9.md)]

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: A felhasználói hozzáférés rendszeres áttekintése és egyeztetése

**Útmutató:** Azure Active Directory (Azure AD) naplókat biztosít az elavult fiókok felderítésének segítésekor. Emellett az Azure Identity hozzáférési felülvizsgálatokkal hatékonyan kezelheti a csoporttagságokat, a vállalati alkalmazásokhoz való hozzáférést és a szerepkör-hozzárendeléseket. A felhasználók hozzáférését rendszeresen áttekintheti, hogy csak a megfelelő felhasználók férnek hozzá.

- [Az Azure Identity Access-felülvizsgálatok használata](../../active-directory/governance/access-reviews-overview.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3.11: Az inaktivált hitelesítő adatok elérésére tett kísérletek figyelése

**Útmutató:** Konfigurálhatja Azure Active Directory (Azure AD) hitelesítését az Azure SQL-val, és diagnosztikai beállításokat hozhat létre az Azure AD felhasználói fiókokhoz, elküldve az auditnaplókat és a bejelentkezési naplókat egy Log Analytics-munkaterületre. Konfigurálja a kívánt riasztásokat a Log Analytics-munkaterületen.

- [Azure AD-hitelesítés konfigurálása és kezelése Azure SQL](authentication-aad-configure.md)

- [Azure-tevékenységnaplók integrálása az Azure Monitorba](../../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3.12: Riasztás a fiók bejelentkezési viselkedésének eltéréséről

**Útmutató:** Azure Active Directory (Azure AD) Identity Protection és kockázatészlelések használatával konfigurálhatja az automatikus válaszokat a felhasználói identitásokkal kapcsolatos gyanús műveletekre. Emellett a további vizsgálathoz adatokat Azure Sentinel be az adatokba.

- [Az Azure AD kockázati bejelentkezések megtekintése](/azure/active-directory/reports-monitoring/concept-risky-sign-ins)

- [Az Identity Protection kockázati szabályzatok konfigurálása és engedélyezése](../../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: Hozzáférés a Microsoft számára a releváns ügyféladatokhoz a támogatási forgatókönyvek során

**Útmutató:** Olyan támogatási forgatókönyvekben, ahol a Microsoftnak ügyféladatokhoz kell hozzáférnie, az Azure Ügyfélszéf felületet biztosít az ügyfelek számára az ügyféladatok hozzáférési kérésének áttekintéséhez, jóváhagyásához vagy elutasításához.

- [A Ügyfélszéf](../../security/fundamentals/customer-lockbox-overview.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

## <a name="data-protection"></a>Adatvédelem

*További információ: [Azure Security Benchmark: Adatvédelem](../../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: Bizalmas adatok leltárának fenntartása

**Útmutató:** A címkék segítségével nyomon lehet követni a bizalmas adatokat tároló vagy feldolgozó Azure-erőforrásokat.

- [Címkék létrehozása és használata](/azure/azure-resource-manager/resource-group-using-tags)

**Felelősség**: Ügyfél

**Azure Security Center monitorozása:** Az [Azure Biztonsági](/azure/governance/policy/samples/azure-security-benchmark) teljesítményteszt a Security Center alapértelmezett szabályzati kezdeményezése, amely a Security Center [javaslatainak alapja.](/azure/security-center/security-center-recommendations) A Azure Policy vezérlővel kapcsolatos összes definíciót automatikusan engedélyezi a Security Center. Az ehhez a vezérlőhez kapcsolódó riasztások esetében szükség [lehet Azure Defender](/azure/security-center/azure-defender) kapcsolódó szolgáltatásokra vonatkozó tervre.

**Azure Policy definíciók – Microsoft.Sql:**

[!INCLUDE [Resource Policy for Microsoft.Sql 4.1](../../../includes/policy/standards/asb/rp-controls/microsoft.sql-4-1.md)]

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: Bizalmas adatokat tároló vagy feldolgozó rendszerek elkülönítése

**Útmutató:** Különálló előfizetéseket és/vagy felügyeleti csoportokat implementálja a fejlesztéshez, teszteléshez és éles környezethez. Az erőforrásokat Vnet/Alhálózat szerint kell elválasztani, megfelelően címkézni kell, és egy NSG-n vagy hálózati Azure Firewall. A bizalmas adatokat tároló vagy feldolgozó erőforrásokat el kell különíteni. Használja a Private Link; üzembe Azure SQL servert a virtuális hálózatán belül, és privát módon csatlakozhat privát végpontok használatával.

- [További Azure-előfizetések létrehozása](/azure/billing/billing-create-subscription)

- [Új Management Groups](/azure/governance/management-groups/create)

- [Címkék létrehozása és használata](/azure/azure-resource-manager/resource-group-using-tags)

- [A Private Link beállítása Azure SQL Database](/azure/sql-database/sql-database-private-endpoint-overview#how-to-set-up-private-link-for-azure-sql-database)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: Bizalmas adatok jogosulatlan átvitelének figyelése és blokkolása

**Útmutató:** A bizalmas Azure SQL Database tárolására vagy feldolgozására használt adatbázisok esetén az adatbázist és a kapcsolódó erőforrásokat címkékkel jelölje meg bizalmasként. Konfigurálja Private Link hálózati biztonsági csoport szolgáltatáscímkéivel együtt a Azure SQL Database-példányon, hogy megakadályozza a bizalmas adatok kiszivárgását.

A Microsoft által felügyelt mögöttes platform esetén a Microsoft bizalmasként kezeli az összes ügyféltartalmat, és nagy terjedelmüknek megfelelő védelmet biztosít az ügyfelek adatvesztése és kitettsége ellen. Annak érdekében, hogy az Azure-beli ügyféladatok biztonságban maradnak, a Microsoft robusztus adatvédelmi vezérlőket és képességeket valósított meg és tart karban.

- [A Private Link NSG-k konfigurálása az adatok kiszivárgásának megakadályozására a Azure SQL Database példányon](/azure/sql-database/sql-database-private-endpoint-overview)

- [Az ügyféladatok Azure-beli védelmének ismertetése](../../security/fundamentals/protection-customer-data.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: A bizalmas adatok azonosítása aktív felderítési eszközzel

**Útmutató:** A Azure SQL Database adatfelderítési és -besorolási funkció használata. Az adatfelderítés és -besorolás a Azure SQL Database beépített fejlett képességeket biztosít az adatbázisokban található bizalmas adatok felderítéséhez, besorolásához és &amp; címkézéséhez.

- [Adatfelderítés és -besorolás használata a Azure SQL Serverhez](/azure/sql-database/sql-database-data-discovery-and-classification)

**Felelősség**: Ügyfél

**Azure Security Center monitorozása:** Az [Azure-biztonsági](/azure/governance/policy/samples/azure-security-benchmark) teljesítményteszt a Security Center alapértelmezett szabályzati kezdeményezése, amely a Security Center [javaslatainak alapja.](/azure/security-center/security-center-recommendations) A Azure Policy vezérlővel kapcsolatos összes definíciót automatikusan engedélyezi a Security Center. Az ehhez a vezérlőhez kapcsolódó riasztások esetében szükség [lehet Azure Defender](/azure/security-center/azure-defender) kapcsolódó szolgáltatásokra vonatkozó tervre.

**Azure Policy definíciók – Microsoft.Sql:**

[!INCLUDE [Resource Policy for Microsoft.Sql 4.5](../../../includes/policy/standards/asb/rp-controls/microsoft.sql-4-5.md)]

### <a name="46-use-role-based-access-control-to-control-access-to-resources"></a>4.6: Az erőforrásokhoz való hozzáférés vezérlése szerepköralapú hozzáférés-vezérléssel

**Útmutató:** A Azure Active Directory (Azure AD) használatával hitelesíti és szabályozza a Azure SQL Database hozzáférését.

- [A Azure SQL integrálása az Azure AD-val a hitelesítéshez](/azure/sql-database/sql-database-aad-authentication)

- [Hozzáférés vezérlése a Azure SQL Serveren](/azure/sql-database/sql-database-control-access)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: Bizalmas adatok titkosítása

**Útmutató:** A transzparens adattitkosítás (TDE) segít megvédeni Azure SQL Database, Azure SQL felügyelt példányt és az Azure Data Warehouse-t az offline rosszindulatú tevékenységekkel szemben az adatok titkosítása révén. Valós időben titkosítja és fejti vissza az adatbázist, a hozzá tartozó biztonsági másolatokat és a tranzakciónapló-fájlokat anélkül, hogy ehhez módosítani kellene az alkalmazást. Alapértelmezés szerint a TDE engedélyezve van az összes újonnan telepített adatbázishoz a SQL Database és SQL Managed Instance. A TDE titkosítási kulcsot a Microsoft vagy az ügyfél is tudja kezelni.

- [Transzparens adattitkosítás kezelése és saját titkosítási kulcsok használata](https://docs.microsoft.com/azure/sql-database/transparent-data-encryption-azure-sql?tabs=azure-portal#manage-transparent-data-encryption)

**Felelősség**: Ügyfél

**Azure Security Center monitorozása:** Az [Azure Biztonsági](/azure/governance/policy/samples/azure-security-benchmark) teljesítményteszt a Security Center alapértelmezett szabályzati kezdeményezése, amely a Security Center [javaslatainak alapja.](/azure/security-center/security-center-recommendations) A Azure Policy vezérlővel kapcsolatos összes definíciót automatikusan engedélyezi a Security Center. Az ehhez a vezérlőhez kapcsolódó riasztások esetében szükség [lehet Azure Defender](/azure/security-center/azure-defender) kapcsolódó szolgáltatásokra vonatkozó tervre.

**Azure Policy definíciók – Microsoft.Sql:**

[!INCLUDE [Resource Policy for Microsoft.Sql 4.8](../../../includes/policy/standards/asb/rp-controls/microsoft.sql-4-8.md)]

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: Kritikus fontosságú Azure-erőforrások változásainak naplózása és riasztása

**Útmutató:** Azure Monitor Azure-tevékenységnaplóval való használatával riasztásokat hozhat létre, amikor változásokra kerül sor a Azure SQL Database kritikus vagy kapcsolódó erőforrások éles példányán.

- [Riasztások létrehozása az Azure-tevékenységnapló eseményeihez](/azure/azure-monitor/platform/alerts-activity-log)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

## <a name="vulnerability-management"></a>Biztonságirés-kezelés

*További információ: [Azure biztonsági teljesítményteszt: Biztonsági rések kezelése.](../../security/benchmarks/security-control-vulnerability-management.md)*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1: Automatizált biztonsági rések ellenőrzési eszközeinek futtatása

**Útmutató:** Engedélyezze a Azure Defender for SQL for Azure SQL Database, és kövesse Azure Security Center biztonsági rések felmérésére vonatkozó javaslatokat a Azure SQL kiszolgálókon.

- [Sebezhetőségi felmérések futtatása Azure SQL Database](/azure/sql-database/sql-vulnerability-assessment)

- [Sql-Azure Defender engedélyezése](azure-defender-for-sql.md)

- [Biztonsági rések felmérésére Azure Security Center javaslatok megvalósítása](/azure/security-center/security-center-vulnerability-assessment-recommendations)

**Felelősség**: Ügyfél

**Azure Security Center monitorozása:** Az [Azure-biztonsági](/azure/governance/policy/samples/azure-security-benchmark) teljesítményteszt a Security Center alapértelmezett szabályzati kezdeményezése, amely a Security Center [javaslatainak alapja.](/azure/security-center/security-center-recommendations) A Azure Policy vezérlővel kapcsolatos összes definíciót automatikusan engedélyezi a Security Center. Az ehhez a vezérlőhez kapcsolódó riasztások esetében szükség [lehet Azure Defender](/azure/security-center/azure-defender) kapcsolódó szolgáltatásokra vonatkozó tervre.

**Azure Policy definíciók – Microsoft.Sql:**

[!INCLUDE [Resource Policy for Microsoft.Sql 5.1](../../../includes/policy/standards/asb/rp-controls/microsoft.sql-5-1.md)]

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4: A biztonsági rések biztonsági rések biztonsági vizsgálatának összehasonlítása

**Útmutató:** Rendszeres időközönként ismétlődő vizsgálatok engedélyezése a Azure SQL Database példányokhoz; Ez egy biztonsági rések felmérését konfigurálja úgy, hogy hetente egyszer automatikusan vizsgálatot futtatassanak az adatbázison. A vizsgálat eredményének összegzése a megadott e-mail-címre/címekre lesz elküldve. Hasonlítsa össze az eredményeket annak ellenőrzéséhez, hogy a biztonsági rések ki lett-e szervizve.

- [Sebezhetőségi felmérési jelentés exportálása a Azure Security Center](/azure/sql-database/sql-vulnerability-assessment#implementing-vulnerability-assessment)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5: Kockázatminősítési folyamat használata a felderített biztonsági rések szervizelésének rangsorolására

**Útmutató:** Használja a biztonsági szolgáltatások által biztosított alapértelmezett Azure Security Center.

- [A Azure Security Center pontszámok](/azure/security-center/security-center-secure-score)

**Felelősség**: Ügyfél

**Azure Security Center monitorozása:** Az [Azure Biztonsági](/azure/governance/policy/samples/azure-security-benchmark) teljesítményteszt a Security Center alapértelmezett szabályzati kezdeményezése, amely a Security Center [javaslatainak alapja.](/azure/security-center/security-center-recommendations) A Azure Policy vezérlővel kapcsolatos összes definíciót automatikusan engedélyezi a Security Center. Az ehhez a vezérlőhez kapcsolódó riasztások esetében szükség [lehet Azure Defender](/azure/security-center/azure-defender) kapcsolódó szolgáltatásokra vonatkozó tervre.

**Azure Policy definíciók – Microsoft.Sql:**

[!INCLUDE [Resource Policy for Microsoft.Sql 5.5](../../../includes/policy/standards/asb/rp-controls/microsoft.sql-5-5.md)]

## <a name="inventory-and-asset-management"></a>Leltár-és eszközfelügyelet

*További információ: [Azure biztonsági teljesítményteszt: Leltár és eszközkezelés.](../../security/benchmarks/security-control-inventory-asset-management.md)*

### <a name="61-use-automated-asset-discovery-solution"></a>6.1: Automatizált eszközfelderítési megoldás használata

**Útmutató:** A Azure Resource Graph használatával lekérdezheti és felderítheti az előfizetés(Azure SQL)-ön belüli összes erőforrást (beleértve az Azure SQL Server-példányokat is). Győződjön meg arról, hogy rendelkezik a megfelelő (olvasási) engedélyekkel a bérlőben, és képes enumerálni az összes Azure-előfizetést és az előfizetésen belüli erőforrásokat.

Bár a klasszikus Azure-erőforrások a Resource Graph felderíthetőek, erősen ajánlott a Azure Resource Manager erőforrások létrehozása és használata.

- [Lekérdezések létrehozása Azure Resource Graph](../../governance/resource-graph/first-query-portal.md)

- [Az Azure-előfizetések megtekintése](/powershell/module/az.accounts/get-azsubscription)

- [Az Azure RBAC](../../role-based-access-control/overview.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="62-maintain-asset-metadata"></a>6.2: Objektum metaadatainak karbantartása

**Útmutató:** Címkék alkalmazása Azure-erőforrásokra, amelyek metaadatokat adva logikailag rendszerezik őket egy taxonómiában.

- [Címkék létrehozása és használata](/azure/azure-resource-manager/resource-group-using-tags)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: Nem engedélyezett Azure-erőforrások törlése

**Útmutató:** Az eszközök rendszerezéséhez és nyomon követéséhez szükség esetén használjon címkézést, felügyeleti csoportokat és különálló előfizetéseket. Rendszeresen összeegyezteti a leltárat, és gondoskodik arról, hogy a jogosulatlan erőforrások időben törlődjenek az előfizetésből.

- [További Azure-előfizetések létrehozása](/azure/billing/billing-create-subscription)

- [Új Management Groups](/azure/governance/management-groups/create)

- [Címkék létrehozása és használata](/azure/azure-resource-manager/resource-group-using-tags)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: Nem jóváhagyott Azure-erőforrások figyelése

**Útmutató:** Azure Policy a következő beépített szabályzatdefiníciók használatával korlátozásokat lehet kiépítve az ügyfél-előfizetés(ök)ben létretehető erőforrások típusára vonatkozóan:

- Nem engedélyezett erőforrástípusok

- Engedélyezett erőforrástípusok

A Azure Resource Graph használatával lekérdezheti/felderítheti az előfizetés(ek)hez elérhető erőforrásokat. Győződjön meg arról, hogy a környezetben jelen van minden Azure-erőforrás jóvá van hagyva.

- [Az Azure Policy konfigurálása és kezelése](../../governance/policy/tutorials/create-and-manage.md)

- [Lekérdezések létrehozása Azure Resource Graph](../../governance/resource-graph/first-query-portal.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="69-use-only-approved-azure-services"></a>6.9: Csak jóváhagyott Azure-szolgáltatások használata

**Útmutató:** Azure Policy az alábbi beépített szabályzatdefiníciók használatával korlátozásokat az ügyfél-előfizetés(ök)ben létre lehet hozható erőforrások típusára vonatkozóan:
- Nem engedélyezett erőforrástípusok
- Engedélyezett erőforrástípusok

A Azure Resource Graph használatával lekérdezheti/felderítheti az előfizetés(ek)hez elérhető erőforrásokat. Győződjön meg arról, hogy a környezetben jelen van minden Azure-erőforrás jóvá van hagyva.

- [Az Azure Policy konfigurálása és kezelése](../../governance/policy/tutorials/create-and-manage.md)

- [Adott erőforrástípus megtagadása a Azure Policy](https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#general)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6.11: Korlátozza a felhasználók számára a Azure Resource Manager

**Útmutató:** Az Azure feltételes hozzáférés használatával korlátozhatja a felhasználók Azure Resource Manager-hozzáférését a "hozzáférés blokkolása" beállításával a "Microsoft Azure Management" alkalmazáshoz.

- [Feltételes hozzáférés konfigurálása a hozzáférés letilt Azure Resource Manager](../../role-based-access-control/conditional-access-azure-management.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

## <a name="secure-configuration"></a>Biztonságos konfiguráció

*További információ: [Azure biztonsági teljesítményteszt: Biztonságos konfiguráció.](../../security/benchmarks/security-control-secure-configuration.md)*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: Biztonságos konfigurációk létrehozása az összes Azure-erőforráshoz

**Útmutató:** Azure Policy vagy Azure Security Center ajánlott Azure SQL kiszolgálókhoz/adatbázisokhoz az összes Azure-erőforrás biztonsági konfigurációjának fenntartásához.

- [Az Azure Policy konfigurálása és kezelése](../../governance/policy/tutorials/create-and-manage.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: Biztonságos Azure-erőforrás-konfigurációk fenntartása

**Útmutató:** A Azure Policy [megtagadás] és [üzembe helyezés, ha nem létezik] használatával kényszerítheti a biztonságos beállításokat az Azure-erőforrásokon.

- [Az Azure Policy konfigurálása és kezelése](../../governance/policy/tutorials/create-and-manage.md)

- [A Azure Policy hatásainak](../../governance/policy/concepts/effects.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Az Azure-erőforrások konfigurációjának biztonságos tárolása

**Útmutató:** Ha egyéni Azure Policy használ, az Azure DevOps vagy az Azure Repos használatával biztonságosan tárolhatja és kezelheti a kódot.

- [Kód tárolása az Azure DevOpsban](/azure/devops/repos/git/gitworkflow)

- [Az Azure Repos dokumentációja](/azure/devops/repos/)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7: Konfigurációkezelési eszközök üzembe helyezése Azure-erőforrásokhoz

**Útmutató:** Azure Policy a "Microsoft.SQL" névtérben létrehozott aliasokkal egyéni szabályzatokat hozhat létre a rendszerkonfigurációk riasztására, naplózására és kényszerítésére. Emellett dolgozzon ki egy folyamatot és folyamatot a szabályzati kivételek kezeléséhez.

- [Az Azure Policy konfigurálása és kezelése](../../governance/policy/tutorials/create-and-manage.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9: Automatizált konfigurációfigyelés megvalósítása az Azure-erőforrásokhoz

**Útmutató:** A Azure Security Center a kiszolgálók és adatbázisok alapkonfiguráció-Azure SQL végrehajtásához.

- [Javaslatok szervizlének Azure Security Center](/azure/security-center/security-center-sql-service-recommendations)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="711-manage-azure-secrets-securely"></a>7.11: Azure-beli titkos kulcsok biztonságos kezelése

**Útmutató:** A Azure Key Vault titkosítási kulcsok tárolják a Azure SQL Database transzparens adattitkosítás (TDE) számára.

- [A Azure SQL-kiszolgálón tárolt bizalmas adatok védelme és a titkosítási kulcsok tárolása a Azure Key Vault](/azure/sql-database/sql-database-always-encrypted-azure-key-vault)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: Identitások biztonságos és automatikus kezelése

**Útmutató:** Felügyelt identitások használata az Azure-szolgáltatások automatikus felügyelt identitással való Azure Active Directory (Azure AD) használatával. A felügyelt identitások lehetővé teszik az Azure AD-hitelesítést támogató bármely szolgáltatásban történő hitelesítést, beleértve a Azure Key Vault szolgáltatást is, anélkül, hogy hitelesítő adatokat ad meg a kódban.

- [Oktatóanyag: Hozzáférés az Azure SQL-hez egy Windows VM rendszer által hozzárendelt felügyelt identitásával](../../active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-sql.md)

- [Felügyelt identitások konfigurálása](../../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: A hitelesítő adatok nem szándékolt kitettségének megszüntetése

**Útmutató:** A hitelesítőadat-ellenőrző implementálja a hitelesítő adatokat a kódban. A Credential Scanner a felfedezett hitelesítő adatok biztonságosabb helyre, például az Azure Key Vaultba való áthelyezésére is javaslatot tesz. 

- [Hitelesítőadat-ellenőrző beállítása](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

## <a name="malware-defense"></a>Kártevők elleni védelem

*További információ: [Azure biztonsági teljesítményteszt: Kártevők elleni védelem.](../../security/benchmarks/security-control-malware-defense.md)*

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2: A nem számítási Azure-erőforrásokba feltölteni szükséges fájlok előzetes beolvasása

**Útmutató:** A Microsoft kártevőirtó engedélyezve van az Azure-szolgáltatásokat támogató mögöttes gazdagépen (például Azure App Service), de nem fut az ügyféltartalmakon.

A nem számítási Azure-erőforrásokra ( például App Service, Data Lake Storage, Blob Storage, Azure SQL Server stb.) feltöltött tartalmak előzetes beolvasása. A Microsoft ezekben az esetekben nem fér hozzá az Ön adataihoz.

- [A Microsoft Antimalware és a Azure Cloud Services Virtual Machines](../../security/fundamentals/antimalware.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

## <a name="data-recovery"></a>Adat-helyreállítás

*További információ: [Azure biztonsági teljesítményteszt: Adat-helyreállítás.](../../security/benchmarks/security-control-data-recovery.md)*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: Rendszeres automatikus biztonságimentek biztosítása

**Útmutató:** A Azure SQL Database 12 óránként automatikusan készít teljes biztonsági másolatokat az adatbázisokról, 12 óránként különbségi biztonsági másolatokat, 5–10 percenként pedig a tranzakciónaplók biztonsági mentését. A biztonsági másolatok legalább 7 napig ra-GRS tárolóban vannak tárolva az összes szolgáltatási szinten. Az időponthoz alapszintű támogatás biztonsági másolatok konfigurálható megőrzési időszakán kívül minden szolgáltatási szint, legfeljebb 35 napig.

A különböző megfelelőségi követelmények teljesítéséhez különböző megőrzési időtartamokat választhat a heti, havi és/vagy éves biztonsági mentéshez. A tárhelyfelhasználás a biztonsági mentések választott gyakoriságától és a megőrzési időtartam(ak)től függ.

- [A biztonsági mentések és az üzletmenet folytonosságának Azure SQL Serverrel](/azure/sql-database/sql-database-business-continuity)

**Felelősség**: Megosztott

**Azure Security Center monitorozása:** Az [Azure Biztonsági](/azure/governance/policy/samples/azure-security-benchmark) teljesítményteszt a Security Center alapértelmezett szabályzati kezdeményezése, amely a Security Center [javaslatainak alapja.](/azure/security-center/security-center-recommendations) A Azure Policy vezérlővel kapcsolatos összes definíciót automatikusan engedélyezi a Security Center. Az ehhez a vezérlőhez kapcsolódó riasztások esetében szükség [lehet Azure Defender](/azure/security-center/azure-defender) kapcsolódó szolgáltatásokra vonatkozó tervre.

**Azure Policy definíciók – Microsoft.Sql:**

[!INCLUDE [Resource Policy for Microsoft.Sql 9.1](../../../includes/policy/standards/asb/rp-controls/microsoft.sql-9-1.md)]

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: Teljes rendszer-biztonsági mentések és az ügyfelek által kezelt kulcsok biztonsági mentése

**Útmutató:** a Azure SQL Database automatikusan létrehozza az adatbázis 7 és 35 nap közötti biztonsági másolatát, és Azure írási hozzáférésű georedundáns tárolást (RA-GRS) használ annak biztosítására, hogy akkor is megőrizze azokat, ha az adatközpont nem érhető el. Ezek a biztonsági másolatok automatikusan létrejönnek. Szükség esetén engedélyezze a georedundáns biztonsági mentést a Azure SQL Database.

Ha felhasználó által felügyelt kulcsokat használ a transzparens adattitkosítás, győződjön meg arról, hogy a kulcsokról biztonságimentett.

- [A biztonsági másolatok Azure SQL Serveren](https://docs.microsoft.com/azure/sql-database/sql-database-automated-backups?tabs=single-database)

- [Kulcstartókulcsok biztonsági mentése az Azure-ban](/powershell/module/az.keyvault/backup-azkeyvaultkey)

**Felelősség**: Ügyfél

**Azure Security Center monitorozása:** Az [Azure-biztonsági](/azure/governance/policy/samples/azure-security-benchmark) teljesítményteszt a Security Center alapértelmezett szabályzati kezdeményezése, amely a Security Center [javaslatainak alapja.](/azure/security-center/security-center-recommendations) A Azure Policy vezérlővel kapcsolatos összes definíciót automatikusan engedélyezi a Security Center. Az ehhez a vezérlőhez kapcsolódó riasztások esetében szükség [lehet Azure Defender](/azure/security-center/azure-defender) kapcsolódó szolgáltatásokra vonatkozó tervre.

**Azure Policy definíciók – Microsoft.Sql:**

[!INCLUDE [Resource Policy for Microsoft.Sql 9.2](../../../includes/policy/standards/asb/rp-controls/microsoft.sql-9-2.md)]

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: Az összes biztonsági mentés ellenőrzése, beleértve az ügyfél által kezelt kulcsokat is

**Útmutató:** Az adatok rendszeres helyreállításának biztosítása a Azure Backup. Szükség esetén tesztelje a tartalom elkülönített VLAN-on való visszaállítását. Tesztelje az ügyfél által kezelt kulcsok biztonságimentett visszaállítását.

- [Kulcstartókulcsok visszaállítása az Azure-ban](/powershell/module/az.keyvault/restore-azkeyvaultkey)

- [Biztonsági másolatok Azure SQL Database időponthoz időben való visszaállítás használatával](/azure/sql-database/sql-database-recovery-using-backups#point-in-time-restore)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: A biztonsági másolatok és az ügyfél által kezelt kulcsok védelmének biztosítása

**Útmutató:** Engedélyezze a kulcstörlést a Azure Key Vault a kulcsok véletlen vagy rosszindulatú törléssel szembeni védelme érdekében.

- [A soft delete engedélyezése a Key Vault](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

## <a name="incident-response"></a>Incidensmegoldás

*További információ: [Azure Security Benchmark: Incidensek kezelése](../../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10.1: Incidensválasz-útmutató létrehozása

**Útmutató:** Győződjön meg arról, hogy vannak olyan írásos incidenskezelési tervek, amelyek meghatározzák a személyzet szerepköreit, valamint az incidenskezelés/-kezelés fázisát.

- [Munkafolyamat-automatizálások konfigurálása a Azure Security Center](../../security-center/security-center-planning-and-operations-guide.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: Incidenspontozási és -rangsorolási eljárás létrehozása

**Útmutató:** Security Center súlyosságot rendel a riasztáshoz, hogy segítsen az egyes riasztások sorrendjének rangsorolásában, így ha egy erőforrás biztonsága sérül, azonnal elérhető lesz. A súlyosság azon alapul, Security Center mennyire biztos a riasztás kiadásához használt eredményben vagy elemzésben, valamint hogy a riasztáshoz vezető tevékenység mögött rosszindulatú szándék volt-e.

- [Biztonsági riasztások az Azure Security Centerben](../../security-center/security-center-alerts-overview.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="103-test-security-response-procedures"></a>10.3: Biztonsági reagálási eljárások tesztelése

**Útmutató:** Gyakorlatok a rendszerek incidensválasz-képességeinek rendszeres teszteléséhez. Azonosítsa a gyenge pontokat és réseket, és szükség esetén dolgozza át a tervet.

- [Tekintse meg az NIST útmutatója az it-csomagokkal és képességekkel kapcsolatos teszt-, képzési és gyakorlatprogramokkal kapcsolatos kiadványát.](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: Adja meg a biztonsági incidens kapcsolattartási adatait, és konfigurálja a riasztási értesítéseket a biztonsági incidensek esetén

**Útmutató:** A Biztonsági incidens kapcsolattartási adatait a Microsoft arra használja, hogy kapcsolatba lépjen Önnel, ha a Microsoft Biztonsági reagálási központ (MSRC) azt tapasztalja, hogy illetéktelen vagy jogosulatlan fél fért hozzá adataihoz.

- [A biztonsági kapcsolattartó Azure Security Center beállítása](../../security-center/security-center-provide-security-contact-details.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: Biztonsági riasztások beépítése az incidensválasz-rendszerbe

**Útmutató:** A Azure Security Center riasztások és javaslatok exportálása a Folyamatos exportálás funkcióval. A folyamatos exportálás lehetővé teszi, hogy manuálisan vagy folyamatosan exportálja a riasztásokat és javaslatokat. A riasztásokat a Azure Security Center adat-összekötővel streamelheti a Azure Sentinel.

- [Folyamatos exportálás konfigurálása](../../security-center/continuous-export.md)

- [Riasztások streamelése az Azure Sentinelbe](../../sentinel/connect-azure-security-center.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: A biztonsági riasztások válaszának automatizálása

**Útmutató:** A Azure Security Center munkafolyamat-automatizálási funkcióját használhatja a biztonsági riasztások és javaslatok "Logic Apps" funkcióval történő automatikus aktiválásához.

- [A munkafolyamat-automatizálás és -Logic Apps](../../security-center/workflow-automation.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

## <a name="penetration-tests-and-red-team-exercises"></a>Behatolási tesztek és Red Team-gyakorlatok

*További információ: [Azure biztonsági teljesítményteszt: Behatolási tesztek és Red Team-gyakorlatok.](../../security/benchmarks/security-control-penetration-tests-red-team-exercises.md)*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11.1: Az Azure-erőforrások rendszeres behatolástesztelése és az összes kritikus fontosságú biztonsági eredmény szervizelésének biztosítása

**Útmutató:** Kövesse a Microsoft Cloud behatolástesztelési szabályait annak ellenőrzéséhez, hogy a behatolási tesztek nem sértik-e meg a Microsoft szabályzatát. A Microsoft által felügyelt felhőalapú infrastruktúrán, szolgáltatásokon és alkalmazásokon végzett riasztási és élő behatolási tesztek végrehajtásához használja a Microsoft stratégiáját és végrehajtási tervét. 

- [Behatolástesztelési beavatkozási szabályok](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) 

- [Riasztási tesztek a Microsoft-felhőben](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Felelősség**: Megosztott

**Azure Security Center:** Nincs

## <a name="next-steps"></a>Következő lépések

- [Az Azure Security Benchmark v2 áttekintésének](/azure/security/benchmarks/overview) megtekintése
- További tudnivalók az [Azure biztonsági alapterveiről](/azure/security/benchmarks/security-baselines-overview)
