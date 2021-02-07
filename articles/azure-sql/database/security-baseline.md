---
title: Azure biztonsági alapterv
description: Azure Security alapkonfiguráció a Azure SQL Database és az Azure SQL felügyelt példányaihoz
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 09/21/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: d2513d822c182d4820728123b187e995da3179fe
ms.sourcegitcommit: 8245325f9170371e08bbc66da7a6c292bbbd94cc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/07/2021
ms.locfileid: "99806206"
---
# <a name="azure-security-baseline-for-azure-sql-database--sql-managed-instance"></a>Az Azure biztonsági alapterve Azure SQL Database & SQL felügyelt példányhoz
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Az Azure SQL Database Azure biztonsági alapkonfigurációja olyan javaslatokat tartalmaz, amelyek segítségével javíthatja az üzemelő példány biztonsági állapotát.

A szolgáltatás alapterve az [Azure Security Benchmark 1,0-es verziójából](../../security/benchmarks/overview.md)származik, amely javaslatokat tesz arra vonatkozóan, hogy miként védheti meg felhőalapú megoldásait az Azure-ban az ajánlott eljárásokat ismertető útmutató segítségével.

További információ: [Azure Security](../../security/benchmarks/security-baselines-overview.md)alapkonfigurációk áttekintése.

## <a name="network-security"></a>Hálózati biztonság

*További információkért lásd [: biztonsági ellenőrzés: hálózati biztonság](../../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1,1: az erőforrások védelme hálózati biztonsági csoportokkal vagy Azure Firewall a Virtual Network

**Útmutató**: az Azure Private-hivatkozás engedélyezésével engedélyezheti az Azure Pásti-szolgáltatások (például SQL Database) és az Azure által üzemeltetett ügyfél/partner szolgáltatások elérését a virtuális hálózat privát végpontján keresztül. A virtuális hálózat és a szolgáltatás közötti forgalom a Microsoft gerinchálózatán keresztül halad át, így kiküszöböli a nyilvános internet jelentette kitettséget.

Ha engedélyezni szeretné, hogy a forgalom elérje a Azure SQL Database, használja az SQL-szolgáltatás címkéit, hogy engedélyezze a kimenő forgalmat a hálózati biztonsági csoportokon keresztül.

A virtuális hálózati szabályok lehetővé teszik, hogy a Azure SQL Database csak a virtuális hálózaton belüli kijelölt alhálózatokból továbbított kommunikációt fogadják.

Privát hivatkozás beállítása Azure SQL Databasehoz:

https://docs.microsoft.com/azure/sql-database/sql-database-private-endpoint-overview#how-to-set-up-private-link-for-azure-sql-database

Virtuális hálózati szolgáltatás-végpontok és-szabályok használata a kiszolgálókon:

https://docs.microsoft.com/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>1,2: a virtuális hálózatok, alhálózatok és hálózati adapterek konfigurációjának és forgalmának figyelése és naplózása

**Útmutató**: a Azure Security Center használata és a hálózati védelmi javaslatok szervizelése arra az alhálózatra, amelyet a Azure SQL Database üzembe helyezett.

A Azure SQL Databasehoz csatlakozó Azure Virtual Machines (VM) esetében engedélyezze a hálózati biztonsági csoport (NSG) számára a virtuális gépek védelmét biztosító naplókat, és küldjön naplókat egy Azure Storage-fiókba a forgalom naplózásához.

NSG-naplókat is küldhet egy Log Analytics munkaterületre, és a Traffic Analytics használatával betekintést nyerhet az Azure-Felhőbeli forgalomba. A Traffic Analytics egyes előnyei lehetővé teszi a hálózati tevékenységek megjelenítését és a gyakori pontok azonosítását, a biztonsági fenyegetések azonosítását, a forgalomban rejlő minták értelmezését, valamint a hálózati helytelen konfigurációk meghatározását.

A NSG folyamat naplófájljainak engedélyezése:

https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

A Azure Security Center által biztosított hálózati biztonság ismertetése:

https://docs.microsoft.com/azure/security-center/security-center-network-recommendations

A Traffic Analytics engedélyezése és használata:

https://docs.microsoft.com/azure/network-watcher/traffic-analytics

A Azure Security Center által biztosított hálózati biztonság ismertetése:

https://docs.microsoft.com/azure/security-center/security-center-network-recommendations

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="13-protect-critical-web-applications"></a>1,3: a kritikus webalkalmazások megóvása

**Útmutató**: nem alkalmazható; Ez a javaslat az Azure apps szolgáltatáshoz vagy webalkalmazásokat üzemeltető számítási erőforrásokhoz készült.

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: nem alkalmazható

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1,4: az ismert kártékony IP-címekkel folytatott kommunikáció megtagadása

**Útmutató**: az elosztott szolgáltatásmegtagadási támadások elleni védelem érdekében a Azure SQL Databasehoz társított virtuális hálózatok DDoS Protection szabványának engedélyezése. A Azure Security Center integrált fenyegetési intelligencia használatával megtagadhatja a kommunikációt az ismert kártékony vagy nem használt internetes IP-címekkel.

A DDoS Protection konfigurálása:

https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection

Az Azure Security Center integrált veszélyforrások felderítésének ismertetése:

https://docs.microsoft.com/azure/security-center/security-center-alerts-data-services

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="15-record-network-packets-and-flow-logs"></a>1,5: hálózati csomagok és adatforgalmi naplók rögzítése

**Útmutató**: az Azure SQL Database-példányhoz csatlakozó Azure Virtual Machines (VM-EK) esetében engedélyezze a hálózati biztonsági csoport (NSG) naplófájljait a virtuális gépek védelmét biztosító NSG, és küldjön naplókat egy Azure Storage-fiókba a forgalom naplózásához. Ha a rendellenes tevékenységek kivizsgálásához szükséges, engedélyezze Network Watcher csomagok rögzítését.

A NSG folyamat naplófájljainak engedélyezése:

https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

A Network Watcher engedélyezése:

https://docs.microsoft.com/azure/network-watcher/network-watcher-create

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1,6: hálózati alapú behatolás-észlelési/Behatolás-megelőzési rendszerek (AZONOSÍTÓk/IP-címek) üzembe helyezése

**Útmutató**: a Azure SQL Database a komplex veszélyforrások elleni védelem (ATP) engedélyezése.  A felhasználók riasztást kapnak a gyanús adatbázis-tevékenységekről, a potenciális sebezhetőségekről és az SQL-injektálási támadásokról, valamint a rendellenes adatbázis-hozzáférési és lekérdezési mintákról. A komplex veszélyforrások elleni védelem a riasztásokat is integrálja Azure Security Centerokkal.

A Azure SQL Database komplex veszélyforrások elleni védelmének megismerése és használata: https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-overview

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="17-manage-traffic-to-web-applications"></a>1,7: webalkalmazások forgalmának kezelése

**Útmutató**: nem alkalmazható; Ez a javaslat az Azure apps szolgáltatáshoz vagy webalkalmazásokat üzemeltető számítási erőforrásokhoz készült.

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: nem alkalmazható

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1,8: a hálózati biztonsági szabályok bonyolultságának és adminisztratív terhelésének csökkentése

**Útmutató**: hálózati biztonsági csoportok vagy Azure Firewall hálózati hozzáférés-vezérlésének definiálásához használja a virtuális hálózati szolgáltatás címkéit. Biztonsági szabályok létrehozása során szolgáltatáscímkéket használhat bizonyos IP-címek helyett. Ha egy szabály megfelelő forrás vagy cél mezőjében megadja a szolgáltatási címke nevét (például ApiManagement), akkor engedélyezheti vagy megtagadhatja a megfelelő szolgáltatás forgalmát. A Microsoft kezeli a szolgáltatási címke által felölelt címek előtagjait, és automatikusan frissíti a szolgáltatási címkét a címek változásával.

Ha Azure SQL Database szolgáltatási végpontokat használ, a kimenő és a Azure SQL Database nyilvános IP-címek szükségesek: a hálózati biztonsági csoportokat (NSG) meg kell nyitni ahhoz, hogy a kapcsolat engedélyezze a Azure SQL Database IP-címeket. Ezt a Azure SQL Database NSG-szolgáltatásának címkéi segítségével teheti meg.

A szolgáltatás-címkék ismertetése a Azure SQL Database szolgáltatás-végpontokkal:

https://docs.microsoft.com/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview#limitations

A szolgáltatási címkék megismerése és használata:

https://docs.microsoft.com/azure/virtual-network/service-tags-overview

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1,9: a hálózati eszközök szabványos biztonsági konfigurációinak fenntartása

**Útmutató**: hálózati biztonsági konfigurációk definiálása és implementálása a Azure SQL Databasehoz Azure Policy használatával. A "Microsoft. SQL" névtér használatával egyéni házirend-definíciókat határozhat meg, vagy a kiszolgáló hálózati védelmére tervezett beépített szabályzat-definíciókat is használhat. Egy-kiszolgáló megfelelő beépített hálózati biztonsági házirendjének példája a következő: "SQL Database virtuális hálózati szolgáltatás-végpontot használjon".

Az Azure-tervezetek segítségével leegyszerűsítheti a nagyméretű Azure-környezetek nagy léptékű üzembe helyezését a főbb környezeti összetevők, például az Azure Resource Management-sablonok, az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC) és a házirendek alapján, egyetlen terv definíciójában. Egyszerűen alkalmazhatja a tervrajzot új előfizetésekre és környezetekre, és részletesen beállíthatja a vezérlést és a felügyeletet a verziószámozással.

Azure Policy konfigurálása és kezelése: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Azure Blueprint létrehozása: https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="110-document-traffic-configuration-rules"></a>1,10: a dokumentum forgalmának konfigurációs szabályai

**Útmutató**: használja a hálózati biztonsági csoportokhoz (NSG) és a hálózati biztonsággal és a forgalommal kapcsolatos egyéb erőforrásokhoz tartozó címkéket. Az egyes NSG-szabályok esetében a "Leírás" mezővel adhatja meg az üzleti igényeket és/vagy időtartamot (stb.) minden olyan szabályhoz, amely engedélyezi a hálózatra irányuló adatforgalmat.

A címkézéshez kapcsolódó beépített Azure Policy definíciók bármelyikét használhatja, például a "címke és az érték megkövetelése" beállítást, hogy az összes erőforrás címkével legyen létrehozva, és értesítse a meglévő címkézetlen erőforrásokról.

A Azure PowerShell vagy az Azure CLI használatával a címkék alapján kereshet vagy végezhet műveleteket az erőforrásokon.

Címkék létrehozása és használata:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1,11: automatikus eszközök használata a hálózati erőforrások konfigurációjának figyelésére és a változások észlelésére

**Útmutató**: az Azure-tevékenység naplójának használata a hálózati erőforrás-konfigurációk figyelésére és a kiszolgálóhoz kapcsolódó hálózati erőforrások változásainak észlelésére. Hozzon létre riasztásokat Azure Monitoron belül, amelyek akkor lépnek életbe, amikor a kritikus hálózati erőforrásokra vonatkozó módosításokat végrehajtják

Az Azure Activity log eseményeinek megtekintése és beolvasása:

https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view

Riasztások létrehozása a Azure Monitorban:

https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

## <a name="logging-and-monitoring"></a>Naplózás és monitorozás

*További információ [: Security Control: naplózás és figyelés](../../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="21-use-approved-time-synchronization-sources"></a>2,1: a jóváhagyott idő-szinkronizálási források használata

**Útmutató**: a Microsoft fenntartja az Azure-erőforrások időforrásait. A számítási üzemelő példányok időszinkronizálása is frissíthető.

Az Azure számítási erőforrások időszinkronizálásának konfigurálása:

https://docs.microsoft.com/azure/virtual-machines/windows/time-sync

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Microsoft

### <a name="22-configure-central-security-log-management"></a>2,2: a központi biztonsági naplók felügyeletének konfigurálása

**Útmutató**: az Azure SQL Database naplózásának engedélyezése az adatbázis-események nyomon követéséhez és az Azure Storage-fiókba, log Analytics-munkaterülethez vagy Event Hubs naplóba való íráshoz.

Emellett az Azure SQL Diagnostics telemetria Azure SQL Analyticsba, egy felhőalapú megoldásba is továbbíthatja, amely figyeli a Azure SQL Database és az Azure SQL felügyelt példányának teljesítményét a skálán és több előfizetésen keresztül. Segítséget nyújt Azure SQL Database teljesítmény-mérőszámok gyűjtéséhez és megjelenítéséhez, és beépített intelligenciával rendelkezik a teljesítménnyel kapcsolatos hibaelhárításhoz.

A Azure SQL Database naplózásának beállítása:

https://docs.microsoft.com/azure/sql-database/sql-database-auditing

Platform-naplók és-metrikák összegyűjtése a Azure Monitor használatával:

https://docs.microsoft.com/azure/sql-database/sql-database-metrics-diag-logging

A diagnosztika továbbítása a Azure SQL Analyticsba:

https://docs.microsoft.com/azure/sql-database/sql-database-metrics-diag-logging#stream-into-azure-sql-analytics

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="23-enable-audit-logging-for-azure-resources"></a>2,3: az Azure-erőforrások naplózásának engedélyezése

**Útmutatás**: engedélyezze a naplózást a kiszolgálón, és válasszon tárolási helyet a naplók számára (Azure storage, log Analytics vagy Event hub).

A Azure SQL Database naplózásának engedélyezése:

https://docs.microsoft.com/azure/sql-database/sql-database-auditing

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="24-collect-security-logs-from-operating-systems"></a>2,4: biztonsági naplók gyűjtése az operációs rendszerekből

**Útmutató**: nem alkalmazható; Ez a teljesítményteszt számítási erőforrások számára készült.

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: nem alkalmazható

### <a name="25-configure-security-log-storage-retention"></a>2,5: a biztonsági napló tárolási adatmegőrzésének konfigurálása

**Útmutató**: ha a Azure SQL Database-naplókat egy log Analytics munkaterületen tárolja, állítsa be a napló megőrzési időszakát a szervezet megfelelőségi szabályainak megfelelően.

Napló-megőrzési paraméterek beállítása:

https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="26-monitor-and-review-logs"></a>2,6: naplók figyelése és áttekintése

**Útmutató**: a rendellenes viselkedésre vonatkozó naplók elemzése és figyelése, valamint az eredmények rendszeres áttekintése. A Azure Security Center komplex veszélyforrások elleni védelme a Azure SQL Database-példányhoz kapcsolódó szokatlan tevékenységgel kapcsolatos riasztások esetén használható. Azt is megteheti, hogy a riasztásokat metrikus értékek vagy az Azure SQL Database példányokhoz kapcsolódó Azure-műveletnapló bejegyzései alapján konfigurálja.

Az összetett veszélyforrások elleni védelem és a riasztások megismerése Azure SQL Database esetén:

https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-overview

Egyéni riasztások konfigurálása Azure SQL Databasehoz:

https://docs.microsoft.com/azure/sql-database/sql-database-insights-alerts-portal?view=azps-1.4.0

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="27-enable-alerts-for-anomalous-activity"></a>2,7: riasztások engedélyezése rendellenes tevékenységhez

**Útmutató**: a Azure Security Center komplex veszélyforrások elleni védelem használata a rendellenes tevékenység figyelésére és riasztására Azure SQL Database. Engedélyezze az SQL-adatbázisokhoz készült Azure Defendert az SQL-adatbázisok számára. Az SQL-hez készült Azure Defender funkciói felszínre és a lehetséges adatbázis-sebezhetőségek enyhítésére, valamint az adatbázis fenyegetését jelző rendellenes tevékenységek észlelésére szolgálnak.

Az összetett veszélyforrások elleni védelem és a riasztások megismerése Azure SQL Database esetén:

https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-overview

Az Azure Defender for SQL engedélyezése Azure SQL Database esetén:

https://docs.microsoft.com/azure/azure-sql/database/azure-defender-for-sql

Riasztások kezelése Azure Security Centerban:

https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="28-centralize-anti-malware-logging"></a>2,8: kártevő szoftverek közötti naplózás központosítása

**Útmutató**: nem alkalmazható; Azure SQL Database esetén a kártevő szoftvereket a Microsoft felügyeli a mögöttes platformon.

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: nem alkalmazható

### <a name="29-enable-dns-query-logging"></a>2,9: DNS-lekérdezések naplózásának engedélyezése

**Útmutató**: nem alkalmazható; A DNS-naplózás nem alkalmazható a Azure SQL Databasera.

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: nem alkalmazható

### <a name="210-enable-command-line-audit-logging"></a>2,10: parancssori naplózás engedélyezése

**Útmutató**: nem alkalmazható; a parancssori naplózás nem alkalmazható a Azure SQL Databasera.

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: nem alkalmazható

## <a name="identity-and-access-control"></a>Identitás- és hozzáférés-vezérlés

*További információ [: Security Control (identitás és Access Control](../../security/benchmarks/security-control-identity-access-control.md)).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3,1: a felügyeleti fiókok leltárának karbantartása

**Útmutató**: Azure Active Directory (Azure ad) beépített szerepkörökkel rendelkezik, amelyeket explicit módon kell hozzárendelni, és lekérdezhető. Az Azure AD PowerShell-modullal ad hoc lekérdezéseket hajthat végre a felügyeleti csoportok tagjait futtató fiókok felderítése érdekében.

Címtárbeli szerepkör beszerzése az Azure AD-ben a PowerShell-lel:

https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0

Címtárbeli szerepkör tagjainak beszerzése az Azure AD-ben a PowerShell-lel:

https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="32-change-default-passwords-where-applicable"></a>3,2: az alapértelmezett jelszavak módosítása, ha alkalmazható

**Útmutató**: a Azure Active Directory nem rendelkezik az alapértelmezett jelszavak fogalmával. Azure SQL Database példány kiépítésekor javasoljuk, hogy a hitelesítést a Azure Active Directory használatával integrálja.

Azure Active Directory hitelesítés konfigurálása és kezelése az Azure SQL használatával:

https://docs.microsoft.com/azure/azure-sql/database/authentication-aad-configure

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="33-use-dedicated-administrative-accounts"></a>3,3: dedikált rendszergazdai fiókok használata

**Útmutató**: szabályzatok és eljárások létrehozása a dedikált rendszergazdai fiókok használatával. A rendszergazdai fiókok számának figyeléséhez használja a Azure Security Center identitás-és hozzáférés-kezelés lehetőséget.

Azure Security Center identitás és hozzáférés ismertetése:

https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3,4: egyszeri bejelentkezés (SSO) használata Azure Active Directory

**Útmutató**: nem alkalmazható; Habár Azure Active Directory hitelesítés konfigurálásával integrálható a Azure SQL Database, az egyszeri bejelentkezés nem támogatott.

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: nem alkalmazható

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3,5: Multi-Factor Authentication használata az összes Azure Active Directory-alapú hozzáféréshez

**Útmutató**: a Azure Active Directory (Azure AD) multi-Factor Authentication (MFA) engedélyezése és a Azure Security Center identitás-és hozzáférés-kezelési javaslatainak követése.

Az MFA engedélyezése az Azure-ban:

https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

Identitás és hozzáférés figyelése Azure Security Centeron belül:

https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3,6: dedikált gépek (privilegizált hozzáférési munkaállomások) használata az összes felügyeleti feladathoz

**Útmutató**: az Azure-erőforrások bejelentkezéséhez és konfigurálásához konfigurált, multi-Factor Authentication MFA-t használó privilegizált hozzáférési munkaállomás (Paw) használata.

További tudnivalók az emelt szintű hozzáférésű munkaállomásokról:

https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/

Az MFA engedélyezése az Azure-ban:

https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="37-log-and-alert-on-suspicious-activity-from-administrative-accounts"></a>3,7: naplózás és riasztás a gyanús tevékenységekről a rendszergazdai fiókoktól

**Útmutató**: Azure Active Directory biztonsági jelentések használata naplók és riasztások generálásához, ha a környezetben gyanús vagy nem biztonságos tevékenység történik.

A Azure SQL Database komplex veszélyforrások elleni védelemmel észleli az adatbázisok eléréséhez vagy kiaknázásához szükséges szokatlan és potenciálisan ártalmas kísérleteket jelző rendellenes tevékenységeket.

A kockázatos tevékenységre megjelölt Azure AD-felhasználók azonosítása:

https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-user-at-risk

A felhasználók identitás-és hozzáférési tevékenységének figyelése Azure Security Centerban:

https://docs.microsoft.com/azure/security-center/security-center-identity-access

Az összetett veszélyforrások elleni védelem és a lehetséges riasztások áttekintése:

https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-overview#advanced-threat-protection-alerts

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3,8: az Azure-erőforrások kezelése csak jóváhagyott helyekről

**Útmutató**: a feltételes hozzáférés elnevezett helyeivel lehetővé teheti a portál és az Azure erőforrás-kezelési hozzáférését az IP-címtartományok vagy országok/régiók adott logikai csoportjaiból.

Elnevezett helyszínek konfigurálása az Azure-ban: https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="39-use-azure-active-directory"></a>3,9: a Azure Active Directory használata

**Útmutató**: hozzon létre egy Azure Active Directory (Azure ad) rendszergazdát a kiszolgálóhoz.

Azure Active Directory hitelesítés konfigurálása és kezelése az Azure SQL használatával:

https://docs.microsoft.com/azure/azure-sql/database/authentication-aad-configure

Azure AD-példány létrehozása és konfigurálása:

https://docs.microsoft.com/azure/active-directory-domain-services/tutorial-create-instance

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="310-regularly-review-and-reconcile-user-access"></a>3,10: a felhasználói hozzáférés rendszeres áttekintése és egyeztetése

**Útmutató**: a Azure Active Directory (Azure ad) olyan naplókat biztosít, amelyek segítenek az elavult fiókok felderítésében. Emellett az Azure Identity Access Reviews használatával hatékonyan kezelheti a csoporttagságok kezelését, a vállalati alkalmazásokhoz való hozzáférést és a szerepkör-hozzárendeléseket. A felhasználók hozzáférését rendszeresen áttekintheti, hogy csak a megfelelő felhasználók férhessenek hozzá.

Az Azure Identity hozzáférési felülvizsgálatok használata:

https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="311-monitor-attempts-to-access-deactivated-accounts"></a>3,11: az inaktivált fiókok elérésére irányuló kísérletek figyelése

**Útmutató**: Azure Active Directory (Azure ad) hitelesítés konfigurálása az Azure SQL használatával és diagnosztikai beállítások létrehozása Azure Active Directory felhasználói fiókokhoz, a naplók és a bejelentkezési naplók elküldése egy log Analytics munkaterületre. A kívánt riasztások konfigurálása Log Analytics munkaterületen belül.

Azure Active Directory hitelesítés konfigurálása és kezelése az Azure SQL használatával:

https://docs.microsoft.com/azure/azure-sql/database/authentication-aad-configure

Azure-beli tevékenység-naplók integrálása a Azure Monitorba:

https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="312-alert-on-account-login-behavior-deviation"></a>3,12: riasztás a fiók bejelentkezési viselkedésének eltérése esetén

**Útmutató**: a Azure Active Directory (Azure ad) Identity Protection és kockázati észlelések használatával konfigurálhatja a felhasználói identitásokkal kapcsolatos gyanús műveletekre vonatkozó automatizált válaszokat. Emellett további vizsgálatot is betöltheti az Azure Sentinelbe.

Az Azure AD kockázati bejelentkezések megtekintése:

https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risky-sign-ins

Az Identity Protection kockázati házirendjeinek konfigurálása és engedélyezése:

https://docs.microsoft.com/azure/active-directory/identity-protection/howto-identity-protection-configure-risk-policies

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3,13: a Microsoft számára elérhetővé teszi a megfelelő ügyféladatokat a támogatási forgatókönyvek során

**Útmutató**: azon forgatókönyvek esetében, amelyekben a Microsoftnak hozzá kell férnie az ügyféladatok eléréséhez, az Azure Ügyfélszéf egy felületet biztosít az ügyfelek számára az ügyféladatok hozzáférési kérelmeinek áttekintéséhez és jóváhagyásához vagy elutasításához.

Ügyfélszéf ismertetése:

https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

## <a name="data-protection"></a>Adatvédelem

*További információkért lásd [: biztonsági ellenőrzés:](../../security/benchmarks/security-control-data-protection.md)adatvédelem.*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4,1: bizalmas információk leltárának fenntartása

**Útmutató**: a címkék használatával segítheti a bizalmas adatokat tároló vagy feldolgozó Azure-erőforrások nyomon követését.

Címkék létrehozása és használata:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4,2: bizalmas adatok tárolására vagy feldolgozására szolgáló rendszerek elkülönítése

**Útmutató**: különálló előfizetések és/vagy felügyeleti csoportok megvalósítása fejlesztési, tesztelési és éles környezetekhez. Az erőforrásokat vnet/alhálózattal kell elválasztani, megfelelően címkézve, és egy NSG vagy Azure Firewallon belül kell biztosítani. A bizalmas adatokat tároló vagy feldolgozó erőforrások elkülönítését el kell különíteni. Privát hivatkozás használata; helyezzen üzembe Azure SQL Database a vnet belül, és privát végpontok használatával kapcsolódjon.

További Azure-előfizetések létrehozása:

https://docs.microsoft.com/azure/billing/billing-create-subscription

Management Groups létrehozása:

https://docs.microsoft.com/azure/governance/management-groups/create

Címkék létrehozása és használata:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

Privát hivatkozás beállítása Azure SQL Databasehoz:

https://docs.microsoft.com/azure/sql-database/sql-database-private-endpoint-overview#how-to-set-up-private-link-for-azure-sql-database

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4,3: a bizalmas adatok jogosulatlan átvitelének figyelése és letiltása

**Útmutató**: a bizalmas adatok tárolására vagy feldolgozására szolgáló Azure SQL Database adatbázisai esetében az adatbázis és a kapcsolódó erőforrások bizalmasként való megjelölése címkék használatával. Konfigurálja a privát hivatkozást a hálózati biztonsági csoport szolgáltatásbeli címkékkel együtt a Azure SQL Database példányokon a bizalmas adatok kiszűrése megakadályozása érdekében.

A Microsoft által felügyelt mögöttes platform esetében a Microsoft az összes vásárlói tartalmat bizalmasként kezeli, és az ügyfelek adatvesztésével és a kitettséggel szembeni védelem érdekében nagy hosszúságú. Annak biztosítása érdekében, hogy az Azure-beli ügyféladatok biztonságban maradjanak, a Microsoft végrehajtotta és karbantartja a robusztus adatvédelmi szabályozást és képességeket.

Privát hivatkozás és NSG konfigurálása az Azure SQL Database-példányokon lévő adatkiszűréseek megelőzésére:

https://docs.microsoft.com/azure/sql-database/sql-database-private-endpoint-overview

Az ügyfelek adatvédelem az Azure-ban:

https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4,4: minden bizalmas adat titkosítása az átvitel során

**Útmutató**: a Azure SQL Database a Transport Layer Security használatával a mozgásban lévő adattitkosítással védi az adatait. SQL Database az összes kapcsolat esetében mindig kikényszeríti a titkosítást (SSL/TLS). Ez biztosítja, hogy az összes adatforgalom titkosítva legyen az ügyfél és a kiszolgáló között, függetlenül attól, hogy a titkosítás vagy a TrustServerCertificate a kapcsolati sztringben van-e beállítva.

Az Azure SQL-titkosítás ismertetése az átvitel során:

https://docs.microsoft.com/azure/sql-database/sql-database-security-overview#information-protection-and-encryption

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Microsoft

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4,5: aktív felderítési eszköz használata a bizalmas adatok azonosítására

**Útmutató**: használja a Azure SQL Database adatfelderítési és besorolási funkciót. Az adatfelderítés és-besorolás olyan speciális képességeket biztosít, amelyek a Azure SQL Databaseba vannak építve a &amp; bizalmas adatoknak az adatbázisokban való felderítéséhez, besorolásához, címkézéséhez.

Adatfelderítés és besorolás használata Azure SQL Database esetén:

https://docs.microsoft.com/azure/sql-database/sql-database-data-discovery-and-classification

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4,6: az erőforrásokhoz való hozzáférés szabályozása az Azure RBAC

**Útmutató**: Azure Active Directory (Azure ad) használata a Azure SQL Database példányokhoz való hozzáférés hitelesítéséhez és szabályozásához.

Azure SQL Database integrálása Azure Active Directory hitelesítéssel:

https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication

Hozzáférés szabályozása Azure SQL Databaseban:

https://docs.microsoft.com/azure/sql-database/sql-database-control-access

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4,7: a gazdagép-alapú adatvesztés-megelőzés használata a hozzáférés-vezérlés kikényszeríthető

**Útmutató**: a Microsoft kezeli a Azure SQL Database alapjául szolgáló infrastruktúrát, és szigorú ellenőrzéseket vezetett be az ügyféladatok elvesztésének vagy kihatásának megelőzésére.

Az ügyfelek adatvédelem az Azure-ban:

https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: nem alkalmazható

### <a name="48-encrypt-sensitive-information-at-rest"></a>4,8: bizalmas adatok titkosítása a nyugalmi állapotban

**Útmutató**: a transzparens adattitkosítás (TDE) segít megvédeni Azure SQL Database, az Azure SQL felügyelt példányát és az Azure-adattárházat a rosszindulatú offline tevékenységek fenyegetésével szemben, ha titkosítja az inaktív adatok titkosítását. Valós időben titkosítja és fejti vissza az adatbázist, a hozzá tartozó biztonsági másolatokat és a tranzakciónapló-fájlokat anélkül, hogy ehhez módosítani kellene az alkalmazást. Alapértelmezés szerint a TDE engedélyezve van az összes újonnan telepített adatbázishoz SQL Database és SQL felügyelt példányban. A TDE-titkosítási kulcsot a Microsoft vagy az ügyfél is kezelheti.

Transzparens adattitkosítás kezelése és saját titkosítási kulcsok használata:

https://docs.microsoft.com/azure/sql-database/transparent-data-encryption-azure-sql?tabs=azure-portal#manage-transparent-data-encryption

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4,9: a kritikus Azure-erőforrások változásainak naplózása és riasztása

**Útmutató**: a Azure monitor és az Azure-tevékenység naplójának használata riasztások létrehozásához, amikor a módosítások a Azure SQL Database és más kritikus vagy kapcsolódó erőforrások éles példányain lépnek életbe.

Riasztások létrehozása az Azure Activity log-eseményekhez:

https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

## <a name="vulnerability-management"></a>Biztonságirés-kezelés

*További információ [: Security Control: sebezhetőségi kezelés](../../security/benchmarks/security-control-vulnerability-management.md).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5,1: automatikus biztonsági rések vizsgálatára szolgáló eszközök futtatása

**Útmutató**: az Azure DEFENDER for SQL engedélyezése a Azure SQL Databasehoz, és Azure Security Center ajánlásainak követése a kiszolgálókon a sebezhetőségi felmérések végrehajtásához.

Sebezhetőségi felmérések futtatása Azure SQL Databaseon:

https://docs.microsoft.com/azure/sql-database/sql-vulnerability-assessment

Az Azure Defender for SQL engedélyezése:

https://docs.microsoft.com/azure/azure-sql/database/azure-defender-for-sql

Azure Security Center sebezhetőségi felméréssel kapcsolatos javaslatok megvalósítása:

https://docs.microsoft.com/azure/security-center/security-center-vulnerability-assessment-recommendations

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5,2: az operációs rendszer automatikus javításának felügyeleti megoldásának telepítése

**Útmutató**: nem alkalmazható; Ez a javaslat számítási erőforrások számára készült.

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: nem alkalmazható

### <a name="53-deploy-automated-third-party-software-patch-management-solution"></a>5,3: a harmadik féltől származó szoftveres javításokat kezelő megoldás telepítése

**Útmutató**: nem alkalmazható; Ez a teljesítményteszt számítási erőforrások számára készült.

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: nem alkalmazható

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5,4: a biztonsági rések keresésének összehasonlítása

**Útmutató**: a Azure SQL Database példányok rendszeres ismétlődő vizsgálatának engedélyezése; Ezzel beállítja a sebezhetőségi felmérést, hogy hetente egyszer automatikusan futtasson vizsgálatot az adatbázison. A rendszer az Ön által megadott e-mail-címre küldi el a vizsgálat eredményének összegzését. Hasonlítsa össze az eredményeket annak ellenőrzéséhez, hogy a biztonsági rések szervizelése megtörtént-e.

Sebezhetőségi felmérési jelentés exportálása Azure Security Centerban:

https://docs.microsoft.com/azure/sql-database/sql-vulnerability-assessment#implementing-vulnerability-assessment

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5,5: kockázatértékelési folyamat használatával rangsorolhatja a felderített biztonsági rések szervizelését

**Útmutató**: a Azure Security Center által biztosított alapértelmezett kockázati minősítések (biztonságos pontszám) használata.

A Azure Security Center biztonsági pontszámának megismerése:

https://docs.microsoft.com/azure/security-center/security-center-secure-score

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

## <a name="inventory-and-asset-management"></a>Leltár-és eszközfelügyelet

*További információkért lásd [: biztonsági vezérlés: leltár és eszközkezelés](../../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-azure-asset-discovery"></a>6,1: az Azure Asset Discovery használata

**Útmutató**: az Azure Resource Graph használatával lekérdezheti és felderítheti az előfizetése (ke) n belül található összes erőforrást (beleértve Azure SQL Database).  Győződjön meg arról, hogy megfelelő (olvasási) engedélyekkel rendelkezik a bérlőben, és képes felsorolni az összes Azure-előfizetést, valamint az előfizetésében lévő erőforrásokat.

Bár a klasszikus Azure-erőforrások felderítése az erőforrás-gráfon keresztül lehetséges, erősen ajánlott a Azure Resource Manager erőforrások létrehozása és használata.

Lekérdezések létrehozása az Azure Resource Graph használatával: https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

Az Azure-előfizetések megtekintése: https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0

Az Azure RBAC ismertetése: https://docs.microsoft.com/azure/role-based-access-control/overview

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="62-maintain-asset-metadata"></a>6,2: az eszköz metaadatainak fenntartása

**Útmutató**: címkéket alkalmazhat az Azure-erőforrásokra, így a metaadatok logikailag rendszerezve lesznek a besorolásban.

Címkék létrehozása és használata:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="63-delete-unauthorized-azure-resources"></a>6,3: jogosulatlan Azure-erőforrások törlése

**Útmutató**: a címkézés, a felügyeleti csoportok és az elkülönített előfizetések használata, ha szükséges, az eszközök rendszerezéséhez és nyomon követéséhez. Rendszeres időközönként egyeztetheti a leltárt, és gondoskodhat arról, hogy a jogosulatlan erőforrások törlése az előfizetésből időben történjen.

További Azure-előfizetések létrehozása:

https://docs.microsoft.com/azure/billing/billing-create-subscription

Management Groups létrehozása:

https://docs.microsoft.com/azure/governance/management-groups/create

Címkék létrehozása és használata:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="64-maintain-an-inventory-of-approved-azure-resources-and-software-titles"></a>6,4: a jóváhagyott Azure-erőforrások és-szoftverek leltárának fenntartása

**Útmutató**: a számítási erőforrásokhoz jóváhagyott Azure-erőforrások és jóváhagyott szoftverek listájának meghatározása

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: nem alkalmazható

### <a name="65-monitor-for-unapproved-azure-resources"></a>6,5: a nem jóváhagyott Azure-erőforrások figyelése

**Útmutató**: a Azure Policy használatával korlátozásokat állíthat be az ügyfél-előfizetésekben létrehozható erőforrások típusára a következő beépített szabályzat-definíciók használatával:

- Nem engedélyezett erőforrástípusok

- Engedélyezett erőforrástípusok

Az Azure Resource Graph használatával lekérdezheti vagy felderítheti az előfizetése (ke) n belüli erőforrásait. Győződjön meg arról, hogy a környezetben lévő összes Azure-erőforrás jóvá van hagyva.

Azure Policy konfigurálása és kezelése: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Lekérdezések létrehozása az Azure Graph használatával: https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6,6: a nem jóváhagyott szoftveralkalmazások figyelése a számítási erőforrásokon belül

**Útmutató**: nem alkalmazható; Ez a javaslat számítási erőforrások számára készült.

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: nem alkalmazható

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6,7: nem jóváhagyott Azure-erőforrások és szoftveralkalmazások eltávolítása

**Útmutató**: nem alkalmazható; Ez a javaslat számítási erőforrások számára készült.

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: nem alkalmazható

### <a name="68-use-only-approved-applications"></a>6,8: csak jóváhagyott alkalmazások használata

**Útmutató**: nem alkalmazható; Ez a javaslat számítási erőforrások számára készült.

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: nem alkalmazható

### <a name="69-use-only-approved-azure-services"></a>6,9: csak jóváhagyott Azure-szolgáltatások használata

**Útmutató**: a Azure Policy használatával korlátozásokat helyezhet el az ügyfél-előfizetésekben létrehozható erőforrások típusára a következő beépített szabályzat-definíciók használatával:

- Nem engedélyezett erőforrástípusok

- Engedélyezett erőforrástípusok

Az Azure Resource Graph használatával lekérdezheti vagy felderítheti az előfizetése (ke) n belüli erőforrásait. Győződjön meg arról, hogy a környezetben lévő összes Azure-erőforrás jóvá van hagyva.

Azure Policy konfigurálása és kezelése: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Adott erőforrástípus megtagadása a következővel: Azure Policy: https://docs.microsoft.com/azure/governance/policy/samples/not-allowed-resource-types

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="610-implement-approved-application-list"></a>6,10: jóváhagyott alkalmazások listájának implementálása

**Útmutató**: nem alkalmazható; Ez a javaslat a számítási erőforrásokon futó alkalmazások számára készült.

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: nem alkalmazható

### <a name="611-limit-users-ability-to-interact-with-azure-resources-manager-via-scripts"></a>6,11: a felhasználók az Azure Resources Managerrel való interakcióra való képességének korlátozása parancsfájlok használatával

**Útmutató**: az Azure feltételes hozzáférés használatával korlátozhatja a felhasználók a Azure Resource Manager interakcióját a "Microsoft Azure felügyelet" alkalmazás "hozzáférés tiltása" beállításával.

A feltételes hozzáférés konfigurálása a Azure Resource Managerhoz való hozzáférés blokkolásához: https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6,12: korlátozza a felhasználók számára a parancsfájlok végrehajtásának lehetőségét a számítási erőforrásokon belül

**Útmutató**: nem alkalmazható; Ez a javaslat számítási erőforrások számára készült.

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: nem alkalmazható

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6,13: fizikailag vagy logikailag elkülöníthető a nagy kockázatú alkalmazások

**Útmutató**: nem alkalmazható; Ez a javaslat az asztali vagy webalkalmazásokat üzemeltető App Service vagy számítási erőforrások számára készült.

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: nem alkalmazható

## <a name="secure-configuration"></a>Biztonságos konfiguráció

*További információkért lásd [: biztonság-vezérlés: biztonságos konfiguráció](../../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7,1: biztonságos konfigurációk létrehozása az összes Azure-erőforráshoz

**Útmutató**: az Azure-erőforrások biztonsági konfigurációjának fenntartása érdekében Azure Policy vagy Azure Security Center javaslatok használata Azure SQL Databasehoz.

Azure Policy konfigurálása és kezelése:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="72-establish-secure-operating-system-configurations"></a>7,2: biztonságos operációsrendszer-konfigurációk létrehozása

**Útmutató**: nem alkalmazható; a számítási erőforrások kiszámítására szolgáló javaslat.

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: nem alkalmazható

### <a name="73-maintain-secure-azure-resource-configurations"></a>7,3: biztonságos Azure-erőforrás-konfigurációk karbantartása

**Útmutató**: az Azure-erőforrások biztonságos beállításainak betartatásához használja a Azure Policy [deny] és a [telepítés ha nem létezik] lehetőséget.

Azure Policy konfigurálása és kezelése:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Azure Policy effektusok ismertetése:

https://docs.microsoft.com/azure/governance/policy/concepts/effects

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="74-maintain-secure-operating-system-configurations"></a>7,4: az operációs rendszer biztonságos konfigurációjának fenntartása

**Útmutató**: nem alkalmazható; Ez a javaslat számítási erőforrások számára készült.

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: nem alkalmazható

### <a name="75-securely-store-configuration-of-azure-resources"></a>7,5: az Azure-erőforrások biztonságos tárolása

**Útmutató**: ha egyéni Azure Policy-definíciókat használ, az Azure DevOps vagy az Azure Repos segítségével biztonságosan tárolhatja és kezelheti a kódot.

Kód tárolása az Azure DevOps:

https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops

Az Azure Repos dokumentációja:

https://docs.microsoft.com/azure/devops/repos/index?view=azure-devops

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="76-securely-store-custom-operating-system-images"></a>7,6: az egyéni operációsrendszer-lemezképek biztonságos tárolása

**Útmutató**: nem alkalmazható; Ez a javaslat számítási erőforrások számára készült.

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: nem alkalmazható

### <a name="77-deploy-system-configuration-management-tools"></a>7,7: rendszerkonfiguráció-felügyeleti eszközök telepítése

**Útmutató**: a "Microsoft. SQL" névtérben található Azure Policy Aliasok használatával egyéni szabályzatokat hozhat létre a riasztáshoz, a naplózáshoz és a rendszerkonfigurációk kényszerítéséhez. Emellett dolgozzon ki egy folyamatot és egy folyamatot a házirend-kivételek kezeléséhez.

Azure Policy konfigurálása és kezelése:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7,8: rendszerkonfiguráció-felügyeleti eszközök központi telepítése operációs rendszerekhez

**Útmutató**: nem alkalmazható; Ez a javaslat számítási erőforrások számára készült.

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: nem alkalmazható

### <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7,9: az Azure-szolgáltatások automatizált konfigurációs figyelésének megvalósítása

**Útmutató**: a Azure Security Center kihasználása a Azure SQL Database alapkonfigurációjának megkereséséhez.

Javaslatok szervizelése Azure Security Centerban:

https://docs.microsoft.com/azure/security-center/security-center-sql-service-recommendations

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7,10: az operációs rendszerek automatikus konfiguráció-figyelésének megvalósítása

**Útmutató**: nem alkalmazható; Ez a javaslat számítási erőforrások számára készült.

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: nem alkalmazható

### <a name="711-manage-azure-secrets-securely"></a>7,11: az Azure-titkok biztonságos kezelése

**Útmutató**: a Azure Key Vault használatával tárolhatja Azure SQL Database TRANSZPARENS ADATTITKOSÍTÁS (TDE) titkosítási kulcsait.

A Azure SQL Database tárolt bizalmas adatok védelme és a titkosítási kulcsok tárolása a Azure Key Vaultban:

https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="712-manage-identities-securely-and-automatically"></a>7,12: az identitások biztonságos és automatikus kezelése

**Útmutató**: felügyelt identitások használata az Azure-szolgáltatások automatikus felügyelt identitással való ellátásához Azure Active Directory (Azure ad). A felügyelt identitások lehetővé teszik bármely olyan szolgáltatás hitelesítését, amely támogatja az Azure AD-hitelesítést, beleértve a Azure Key Vault is, a kódban szereplő hitelesítő adatok nélkül.

Oktatóanyag: a Windows rendszerű virtuális gépekhez rendelt felügyelt identitás használata az Azure SQL eléréséhez:

https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-sql

Felügyelt identitások konfigurálása:

https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="713-eliminate-unintended-credential-exposure"></a>7,13: a hitelesítő adatok nem szándékolt expozíciójának megszüntetése

**Útmutató**: hitelesítő adatok beolvasása a kódban a hitelesítő adatok azonosításához. A Credential Scanner a felfedezett hitelesítő adatok biztonságosabb helyre, például az Azure Key Vaultba való áthelyezésére is javaslatot tesz.

A hitelesítőadat-olvasó beállítása: https://secdevtools.azurewebsites.net/helpcredscan.html

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

## <a name="malware-defense"></a>Kártevők elleni védelem

*További információkért lásd [: biztonsági ellenőrzés: kártevők elleni védelem](../../security/benchmarks/security-control-malware-defense.md).*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8,1: központilag felügyelt kártevő szoftverek használata

**Útmutató**: nem alkalmazható; Ez a javaslat számítási erőforrások számára készült. A Microsoft kezeli a kártevő szoftvereket a mögöttes platformon.

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: nem alkalmazható

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8,2: a nem számítási Azure-erőforrásokra feltöltött fájlok előzetes vizsgálata

**Útmutató**: a Microsoft kártevő szoftverrel való ellátása engedélyezve van az Azure-szolgáltatásokat támogató mögöttes gazdagépen (például Azure app Service), azonban nem az ügyfél tartalmán fut.

A nem számítási Azure-erőforrásokra feltöltött tartalom előzetes vizsgálata, például App Service, Data Lake Storage, Blob Storage, Azure SQL Database stb. A Microsoft nem fér hozzá az adataihoz ezekben a példányokban.

Ismerje meg a Microsoft antimalware az Azure Cloud Services és Virtual Machines: https://docs.microsoft.com/azure/security/fundamentals/antimalware

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8,3: a kártevő szoftverek és az aláírások frissítésének ellenőrzése

**Útmutató**: nem alkalmazható; Ez a javaslat számítási erőforrások számára készült. A Microsoft kezeli a kártevő szoftvereket a mögöttes platformon.

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: nem alkalmazható

## <a name="data-recovery"></a>Adat-helyreállítás

*További információkért lásd [: biztonsági ellenőrzés: adat-helyreállítás](../../security/benchmarks/security-control-data-recovery.md).*

### <a name="91-ensure-regular-automated-back-ups"></a>9,1: rendszeres automatizált biztonsági másolatok biztosítása

**Útmutató**: a vállalat adatvesztés elleni védelme érdekében a Azure SQL Database automatikusan létrehozza a teljes adatbázis biztonsági másolatait hetente, a különbözeti adatbázis biztonsági másolatait 12 óránként, a tranzakciós napló biztonsági mentése pedig 5-10 percenként történik. A biztonsági másolatok az RA-GRS tárolóban tárolódnak legalább 7 napig az összes szolgáltatási réteg esetében. Az összes szolgáltatási réteg, kivéve a alapszintű támogatás konfigurálható biztonsági mentés megőrzési időtartamát az időponthoz tartozó visszaállításhoz, legfeljebb 35 napig.

A különböző megfelelőségi követelmények kielégítése érdekében a heti, havi és/vagy éves biztonsági mentések esetében eltérő megőrzési időt választhat. A tárterület-használat a biztonsági mentések és a megőrzési időtartam (ok) kiválasztott gyakoriságával függ.

A biztonsági másolatok és az üzletmenet-folytonosság megismerése Azure SQL Databaseekkel:

https://docs.microsoft.com/azure/sql-database/sql-database-business-continuity

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Megosztott

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: teljes rendszerbiztonsági mentés és biztonsági másolat készítése bármely ügyfél által felügyelt kulcsról

**Útmutató**: a Azure SQL Database automatikusan létrehozza az adatbázis 7 és 35 nap közötti biztonsági mentését, és az Azure olvasási hozzáférésű geo-redundáns tárolást (ra-GRS) használ annak érdekében, hogy a rendszer akkor is megőrizzék azokat, ha az adatközpont nem érhető el. Ezek a biztonsági másolatok automatikusan létrejönnek. Ha szükséges, engedélyezze az Azure SQL-adatbázisok hosszú távú, földrajzilag redundáns biztonsági mentését.

Ha transzparens adattitkosítás ügyfél által felügyelt kulcsokat használ, győződjön meg arról, hogy a kulcsok biztonsági mentése folyamatban van.

Azure SQL Database biztonsági mentések ismertetése:

https://docs.microsoft.com/azure/sql-database/sql-database-automated-backups?tabs=single-database

Key Vault-kulcsok biztonsági mentése az Azure-ban:

https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?view=azurermps-6.13.0

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: az összes biztonsági másolat ellenőrzése, beleértve az ügyfél által felügyelt kulcsokat

**Útmutató**: a Azure Backupon belüli tartalmak adatvisszaállításának rendszeres elvégzése. Ha szükséges, tesztelje a visszaállítási tartalmat egy elkülönített VLAN-ra. Tesztelje az ügyfél által felügyelt kulcsok biztonsági mentésének visszaállítását.

Key Vault-kulcsok visszaállítása az Azure-ban:

https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0

Azure SQL Database biztonsági mentések helyreállítása az időponthoz tartozó visszaállítással:

https://docs.microsoft.com/azure/sql-database/sql-database-recovery-using-backups#point-in-time-restore

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: a biztonsági másolatok és az ügyfél által felügyelt kulcsok védelmének biztosítása

**Útmutató**: a nem kötelező Törlés engedélyezése a Azure Key Vault a kulcsok véletlen vagy rosszindulatú törléssel szembeni védelemmel való ellátásához.

A Soft delete engedélyezése a Key Vaultban:

https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

## <a name="incident-response"></a>Incidensmegoldás

*További információ [: Security Control: incidens válasza](../../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10,1: incidens-válaszi útmutató létrehozása

**Útmutató**: Ellenőrizze, hogy vannak-e írásos incidensekre vonatkozó válaszok, amelyek meghatározzák a személyzet szerepköreit, valamint az incidensek kezelésének és kezelésének fázisait.

Munkafolyamat-automatizálás konfigurálása Azure Security Centeron belül:

https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10,2: incidensek pontozásának és rangsorolási eljárásának létrehozása

**Útmutató**: a Security Center súlyosságot rendel a riasztásokhoz, így könnyebben rangsorolhatja az egyes riasztásokban való részvétel sorrendjét, hogy az erőforrások biztonsága esetén azonnal elérhető legyen. A súlyosság azon alapul, hogy az Security Center milyen mértékben szerepel a riasztás kibocsátásához használt elemzésben vagy elemzésben, valamint azt a megbízhatósági szintet, amely a riasztáshoz vezető tevékenység mögött rosszindulatú szándékú volt.

Biztonsági riasztások a Azure Security Centerban: https://docs.microsoft.com/azure/security-center/security-center-alerts-overview

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="103-test-security-response-procedures"></a>10,3: biztonsági reagálási eljárások tesztelése

**Útmutató**: a rendszerek incidensek reagálási képességeinek rendszeres tesztelésére szolgáló gyakorlatok végrehajtása. Azonosítsa a gyenge pontokat és réseket, és szükség esetén dolgozza át a tervet.

Tekintse meg a NIST kiadványát: útmutató az IT-csomagok és-képességek teszteléséhez, betanításához, valamint a programok gyakorlatához:

https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10,4: biztonsági incidensek elérhetőségének biztosítása és riasztási értesítések konfigurálása biztonsági incidensekhez

**Útmutató**: a Microsoft a biztonsági incidensek elérhetőségi adatait arra használja fel, hogy felvegye Önnel a kapcsolatot, ha a Microsoft Security Response Center (MSRC) felfedi, hogy az adatokat egy törvénytelen vagy jogosulatlan fél is hozzáférte.

A Azure Security Center biztonsági kapcsolattartó beállítása:

https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10,5: biztonsági riasztások beépítése az incidensek gyorsreagáló rendszerébe

**Útmutató**: az Azure Security Center-riasztások és-javaslatok exportálása a folyamatos exportálás funkció használatával. A folyamatos exportálás lehetővé teszi a riasztások és javaslatok manuális és folyamatos exportálását. Az Azure Security Center adatösszekötővel továbbíthatja a riasztásokat az Azure Sentinel szolgáltatásba.

Folyamatos exportálás konfigurálása:

https://docs.microsoft.com/azure/security-center/continuous-export

Riasztások továbbítása az Azure Sentinelbe:

https://docs.microsoft.com/azure/sentinel/connect-azure-security-center

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="106-automate-the-response-to-security-alerts"></a>10,6: a biztonsági riasztásokra adott válasz automatizálása

**Útmutató**: a Azure Security Center munkafolyamat-automatizálási funkciója segítségével automatikusan aktiválhatja a válaszokat a "Logic apps" használatával a biztonsági riasztások és javaslatok esetében.

A Munkafolyamat-automatizálás és a Logic Apps konfigurálása:

https://docs.microsoft.com/azure/security-center/workflow-automation

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

## <a name="penetration-tests-and-red-team-exercises"></a>Behatolási tesztek és Red Team-gyakorlatok

*További információkért lásd [: biztonsági ellenőrzés: behatolási tesztek és Red Team-gyakorlatok](../../security/benchmarks/security-control-penetration-tests-red-team-exercises.md).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings-within-60-days"></a>11,1: rendszeres penetrációs tesztelést végez az Azure-erőforrásokon, és gondoskodik az összes kritikus biztonsági vizsgálat 60 napon belüli szervizeléséről

**Útmutató**: kövesse a Microsoft részvételi szabályait, hogy a behatolási tesztek ne sértsék meg a Microsoft-házirendeket:

https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1.

A Microsoft által felügyelt felhő-infrastruktúrával,-szolgáltatásokkal és-alkalmazásokkal kapcsolatos további információkért tekintse meg a következő témakört: https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Megosztott

## <a name="next-steps"></a>Következő lépések

- Lásd az [Azure biztonsági teljesítménytesztét](../../security/benchmarks/overview.md)
- További információ az [Azure biztonsági Alaptervekről](../../security/benchmarks/security-baselines-overview.md)