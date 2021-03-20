---
title: Azure Database for MariaDB Azure biztonsági alapterve
description: Azure Database for MariaDB Azure biztonsági alapterve
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 03/23/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 99c02644649e133a7634418a5edaaf228d52f0d3
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98201280"
---
# <a name="azure-security-baseline-for-azure-database-for-mariadb"></a>Azure Database for MariaDB Azure biztonsági alapterve

Az Azure Database for MariaDB Azure biztonsági alapkonfigurációja olyan javaslatokat tartalmaz, amelyek segítségével javíthatja az üzemelő példány biztonsági állapotát.

A szolgáltatás alapterve az [Azure Security Benchmark 1,0-es verziójából](../security/benchmarks/overview.md)származik, amely javaslatokat tesz arra vonatkozóan, hogy miként védheti meg felhőalapú megoldásait az Azure-ban az ajánlott eljárásokat ismertető útmutató segítségével.

További információ: [Azure Security](../security/benchmarks/security-baselines-overview.md)alapkonfigurációk áttekintése.

## <a name="network-security"></a>Hálózati biztonság

*További információkért lásd [: biztonsági ellenőrzés: hálózati biztonság](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1,1: az erőforrások védelme hálózati biztonsági csoportokkal vagy Azure Firewall a Virtual Network

**Útmutató**: privát hivatkozás konfigurálása Azure Database for MariaDB magánhálózati végpontokkal. A privát kapcsolat lehetővé teszi, hogy privát végponton keresztül csatlakozzon az Azure különböző PaaS-szolgáltatásaihoz. Az Azure Private Link lényegében elérhetővé teszi az Azure-szolgáltatásokat a virtuális magánhálózaton belül. A virtuális hálózat és a MariaDB-példány közötti forgalom a Microsoft gerinc hálózatán halad át.

Azt is megteheti, hogy Virtual Network szolgáltatás-végpontokat használ a Azure Database for MariaDB-implementációk hálózati hozzáférésének a megóvására és korlátozására. A virtuális hálózati szabályok egy tűzfal biztonsági funkciója, amely azt szabályozza, hogy a Azure Database for MariaDB fogadjon-e olyan kommunikációt, amelyet a virtuális hálózatok egyes alhálózatai továbbítanak.

A Azure Database for MariaDB is biztonságossá teheti a tűzfalszabályok használatával. A kiszolgáló tűzfala megakadályozza az adatbázis-kiszolgáló elérését, amíg meg nem adja, hogy mely számítógépek rendelkeznek engedéllyel. A tűzfal konfigurálásakor olyan tűzfalszabályokat adhat meg, amelyek meghatározzák az elfogadható IP-címtartományokat. A tűzfalszabályok a kiszolgáló szintjén hozhatók létre.

Privát hivatkozás konfigurálása Azure Database for MariaDBhoz: https://docs.microsoft.com/azure/mariadb/howto-configure-privatelink-portal

VNet-szolgáltatási végpontok és VNet-szabályok létrehozása és kezelése Azure Database for MariaDB-kiszolgálón: https://docs.microsoft.com/azure/mariadb/howto-manage-vnet-portal

Azure Database for MariaDB tűzfalszabályok konfigurálása: https://docs.microsoft.com/azure/mariadb/concepts-firewall-rules

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>1,2: a virtuális hálózatok, alhálózatok és hálózati adapterek konfigurációjának és forgalmának figyelése és naplózása

**Útmutató**: ha a Azure Database for MariaDB-kiszolgáló titkos végponthoz van csatlakoztatva, a virtuális gépeket ugyanabban a virtuális hálózatban helyezheti üzembe. Hálózati biztonsági csoport (NSG) használatával csökkentheti az adatkiszűrése kockázatát. Engedélyezze a NSG folyamat naplóit, és küldje el a naplókat egy Storage-fiókba a forgalom naplózása érdekében. NSG-naplókat is küldhet egy Log Analytics munkaterületre, és a Traffic Analytics használatával betekintést nyerhet az Azure-Felhőbeli forgalomba. A Traffic Analytics egyes előnyei lehetővé teszi a hálózati tevékenységek megjelenítését és a gyakori pontok azonosítását, a biztonsági fenyegetések azonosítását, a forgalomban rejlő minták értelmezését, valamint a hálózati helytelen konfigurációk meghatározását.

Privát hivatkozás konfigurálása Azure Database for MariaDBhoz: https://docs.microsoft.com/azure/mariadb/howto-configure-privatelink-portal

NSG flow-naplók engedélyezése: https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal Traffic Analytics engedélyezése és használata: https://docs.microsoft.com/azure/network-watcher/traffic-analytics



**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="13-protect-critical-web-applications"></a>1,3: a kritikus webalkalmazások megóvása

**Útmutató**: nem alkalmazható; Ez a javaslat Azure App Service vagy számítási erőforrásokon futó webalkalmazásokhoz készült.

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: nem alkalmazható

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1,4: az ismert kártékony IP-címekkel folytatott kommunikáció megtagadása

**Útmutató**: az Azure Database for MariaDB komplex veszélyforrások elleni védelme. A komplex veszélyforrások elleni védelem olyan rendellenes tevékenységeket észlel, amelyek szokatlan és potenciálisan ártalmas kísérleteket jeleznek az adatbázisok eléréséhez vagy kiaknázásához.

A Azure Database for MariaDB-példányokhoz társított virtuális hálózatokon DDoS Protection szabványt engedélyezheti a DDoS-támadások elleni védelemhez. A Azure Security Center integrált fenyegetési intelligencia használatával megtagadhatja a kommunikációt az ismert kártékony vagy nem használt internetes IP-címekkel.

Komplex veszélyforrások elleni védelem konfigurálása Azure Database for MariaDB esetén: https://docs.microsoft.com/azure/mariadb/howto-database-threat-protection-portal

A DDoS Protection konfigurálása: https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection



**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="15-record-network-packets-and-flow-logs"></a>1,5: hálózati csomagok és adatforgalmi naplók rögzítése

**Útmutató**: ha a Azure Database for MariaDB-kiszolgáló titkos végponthoz van csatlakoztatva, a virtuális gépeket ugyanabban a virtuális hálózatban helyezheti üzembe. Ezután konfigurálhat egy hálózati biztonsági csoportot (NSG) az adatkiszűrése kockázatának csökkentése érdekében. Engedélyezze a NSG folyamat naplóit, és küldje el a naplókat egy Storage-fiókba a forgalom naplózása érdekében. NSG-naplókat is küldhet egy Log Analytics munkaterületre, és a Traffic Analytics használatával betekintést nyerhet az Azure-Felhőbeli forgalomba. A Traffic Analytics egyes előnyei lehetővé teszi a hálózati tevékenységek megjelenítését és a gyakori pontok azonosítását, a biztonsági fenyegetések azonosítását, a forgalomban rejlő minták értelmezését, valamint a hálózati helytelen konfigurációk meghatározását.

NSG flow-naplók engedélyezése: https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal Traffic Analytics engedélyezése és használata: https://docs.microsoft.com/azure/network-watcher/traffic-analytics



**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1,6: hálózati alapú behatolás-észlelési/Behatolás-megelőzési rendszerek (AZONOSÍTÓk/IP-címek) üzembe helyezése

**Útmutató**: az Azure Database for MariaDB komplex veszélyforrások elleni védelme. A komplex veszélyforrások elleni védelem olyan rendellenes tevékenységeket észlel, amelyek szokatlan és potenciálisan ártalmas kísérleteket jeleznek az adatbázisok eléréséhez vagy kiaknázásához.
Komplex veszélyforrások elleni védelem konfigurálása Azure Database for MariaDB esetén: https://docs.microsoft.com/azure/mariadb/howto-database-threat-protection-portal


**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="17-manage-traffic-to-web-applications"></a>1,7: webalkalmazások forgalmának kezelése

**Útmutató**: nem alkalmazható; Ez a javaslat Azure App Service vagy számítási erőforrásokon futó webalkalmazásokhoz készült.


**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: nem alkalmazható

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1,8: a hálózati biztonsági szabályok bonyolultságának és adminisztratív terhelésének csökkentése

**Útmutató**: a Azure Database for MariaDB példányokhoz hozzáférést igénylő erőforrásokhoz használjon virtuális hálózati szolgáltatás címkéit a hálózati biztonsági csoportokon vagy Azure Firewall a hálózati hozzáférés-vezérlés definiálásához. Biztonsági szabályok létrehozása során szolgáltatáscímkéket használhat bizonyos IP-címek helyett. A szolgáltatási címke nevének (például: SQL) megadásával. WestUs) a szabály megfelelő forrás vagy cél mezőjében engedélyezheti vagy megtagadhatja a megfelelő szolgáltatás forgalmát. A Microsoft kezeli a szolgáltatási címke által felölelt címek előtagjait, és automatikusan frissíti a szolgáltatási címkét a címek változásával.
Megjegyzés: Azure Database for MariaDB a "Microsoft. SQL" szolgáltatás címkéjét használja.

A szolgáltatás-címkék használatával kapcsolatos további információkért tekintse meg a https://docs.microsoft.com/azure/virtual-network/service-tags-overview Azure Database for MariaDB szolgáltatás használati címkéjének használatát ismertető témakört: https://docs.microsoft.com/azure/mariadb/concepts-data-access-security-vnet#terminology-and-description



**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1,9: a hálózati eszközök szabványos biztonsági konfigurációinak fenntartása

**Útmutató**: szabványos biztonsági konfigurációk definiálása és implementálása a Azure Database for MariaDB-példányokhoz társított hálózati beállításokhoz és hálózati erőforrásokhoz Azure Policy. Használjon Azure Policy aliasokat a "Microsoft. DBforMariaDB" és a "Microsoft. Network" névterekben, hogy egyéni szabályzatokat hozzon létre a Azure Database for MariaDB példányok hálózati konfigurációjának naplózásához vagy érvénybe léptetéséhez. A hálózatkezeléssel vagy a Azure Database for MariaDB-példányokkal kapcsolatos beépített szabályzat-definíciókat is igénybe vehet, például:

- DDoS Protection a standardot engedélyezni kell

- A privát végpontot engedélyezni kell a MariaDB-kiszolgálókon

- A MariaDB-kiszolgálónak virtuális hálózati szolgáltatási végpontot kell használnia

Azure Policy konfigurálása és kezelése: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Azure Policy minták a hálózatkezeléshez: https://docs.microsoft.com/azure/governance/policy/samples/

Azure Blueprint létrehozása: https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal


**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="110-document-traffic-configuration-rules"></a>1,10: a dokumentum forgalmának konfigurációs szabályai

**Útmutató**: a MariaDB-példányok hálózati biztonságával és forgalmával kapcsolatos erőforrásokhoz használható címkék használatával metaadatokat és logikai szervezeteket biztosíthat.

A címkézéshez kapcsolódó beépített Azure Policy definíciók bármelyikét használhatja, például a "címke és az érték megkövetelése" beállítást, hogy az összes erőforrás címkével legyen létrehozva, és értesítse a meglévő címkézetlen erőforrásokról.

A Azure PowerShell vagy az Azure CLI használatával a címkék alapján kereshet vagy végezhet műveleteket az erőforrásokon.

Címkék létrehozása és használata: https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1,11: automatikus eszközök használata a hálózati erőforrások konfigurációjának figyelésére és a változások észlelésére

**Útmutató**: az Azure-tevékenység naplójának használata a hálózati erőforrás-konfigurációk figyelésére és a Azure Database for MariaDB-példányokhoz kapcsolódó hálózati erőforrások változásainak észlelésére. Hozzon létre riasztásokat Azure Monitoron belül, amelyek akkor lépnek életbe, amikor a kritikus hálózati erőforrásokra vonatkozó módosításokat végrehajtják
Az Azure-Tevékenységnaplók eseményeinek megtekintése és beolvasása: https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view riasztások létrehozása a Azure monitorban: https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log



**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

## <a name="logging-and-monitoring"></a>Naplózás és monitorozás

*További információ [: Security Control: naplózás és figyelés](../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="21-use-approved-time-synchronization-sources"></a>2,1: a jóváhagyott idő-szinkronizálási források használata

**Útmutató**: a Microsoft fenntartja az Azure-erőforrásokhoz használt időforrást, például Azure Database for MariaDB a naplókban található időbélyegek esetében.


**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Microsoft

### <a name="22-configure-central-security-log-management"></a>2,2: a központi biztonsági naplók felügyeletének konfigurálása

**Útmutató**: a diagnosztikai beállítások és a kiszolgálói naplók engedélyezése, valamint a naplók beolvasása a Azure Database for MariaDB példányai által generált biztonsági adatokat összesítve. A Azure Monitoron belül Log Analytics munkaterület (ek) használatával kérdezheti le és végezheti el az elemzéseket, és használhatja az Azure Storage-fiókokat a hosszú távú/archiválási tároláshoz. Alternatív megoldásként engedélyezheti és elvégezheti az Azure Sentinel vagy egy harmadik féltől származó SIEM-et.
A Azure Database for MariaDB kiszolgáló naplófájljainak konfigurálása és elérése: https://docs.microsoft.com/azure/mariadb/concepts-server-logs

Naplózási naplók konfigurálása és elérése Azure Database for MariaDB: az https://docs.microsoft.com/azure/mariadb/howto-configure-audit-logs-portal Azure Sentinel előkészítése: https://docs.microsoft.com/azure/sentinel/quickstart-onboard



**Azure Security Center figyelés**: nem érhető el

**Felelősség**: Ügyfél

### <a name="23-enable-audit-logging-for-azure-resources"></a>2,3: az Azure-erőforrások naplózásának engedélyezése

**Útmutató**: a diagnosztikai beállítások engedélyezése a Azure Database for MariaDB példányokon a naplózási, biztonsági és diagnosztikai naplókhoz való hozzáféréshez. Győződjön meg arról, hogy kifejezetten engedélyezi a MariaDB-naplót. A automatikusan elérhető tevékenység-naplók közé tartozik az eseményforrás, a dátum, a felhasználó, az időbélyeg, a forráscím, a célcím és más hasznos elemek. Engedélyezheti az Azure-műveletnapló diagnosztikai beállításait is, és elküldheti a naplókat ugyanarra a Log Analytics munkaterületre vagy Storage-fiókba.

Azure Database for MariaDB kiszolgáló naplófájljainak konfigurálása és elérése: a https://docs.microsoft.com/azure/mariadb/concepts-server-logs Azure Database for MariaDB naplózási naplóinak konfigurálása és elérése: https://docs.microsoft.com/azure/mariadb/howto-configure-audit-logs-portal Az Azure-beli tevékenység naplójának diagnosztikai beállításainak konfigurálása: https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings-legacy



**Azure Security Center figyelés**: nem érhető el

**Felelősség**: Ügyfél

### <a name="24-collect-security-logs-from-operating-systems"></a>2,4: biztonsági naplók gyűjtése az operációs rendszerekből

**Útmutató**: nem alkalmazható; Ez a javaslat számítási erőforrások számára készült.


**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: nem alkalmazható

### <a name="25-configure-security-log-storage-retention"></a>2,5: a biztonsági napló tárolási adatmegőrzésének konfigurálása

**Útmutató**: a Azure monitoron belül a Azure Database for MariaDB-naplók tárolására szolgáló log Analytics munkaterülethez a szervezet megfelelőségi szabályainak megfelelően állítsa be a megőrzési időszakot. Használja az Azure Storage-fiókokat hosszú távú/archiválási tároláshoz.
Log Analytics-munkaterületek naplózási megőrzési paramétereinek beállítása: https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period erőforrás-naplók tárolása Azure Storage-fiókban: https://docs.microsoft.com/azure/azure-monitor/platform/resource-logs-collect-storage



**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="26-monitor-and-review-logs"></a>2,6: naplók figyelése és áttekintése

**Útmutató**: az MariaDB-példányok naplóinak elemzése és figyelése rendellenes működéshez. Az Azure Monitor Log Analytics munkaterülete segítségével tekintheti át a naplókat, és lekérdezéseket végezhet a naplózási adatokon. Alternatív megoldásként engedélyezheti és elvégezheti az Azure Sentinel vagy egy harmadik fél SIEM-nek.

Az Azure Sentinel előkészítése: https://docs.microsoft.com/azure/sentinel/quickstart-onboard

További információ a Log Analytics munkaterületről: https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal

Egyéni lekérdezések végrehajtása a Azure Monitorban: https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-queries

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="27-enable-alerts-for-anomalous-activity"></a>2,7: riasztások engedélyezése rendellenes tevékenységhez

**Útmutató**: a MariaDB komplex veszélyforrások elleni védelemének engedélyezése. A Azure Database for MariaDB komplex veszélyforrások elleni védelme olyan rendellenes tevékenységeket észlel, amelyek szokatlan és potenciálisan ártalmas kísérleteket jeleznek az adatbázisok eléréséhez vagy kiaknázásához.

Emellett engedélyezheti a kiszolgálói naplókat és a diagnosztikai beállításokat a MariaDB, és naplókat küldhet egy Log Analytics munkaterületre. A Log Analytics-munkaterületet az Azure Sentinelbe irányíthatja, mivel ez egy biztonsági előkészítési automatizált választ (felszárnyaló) megoldást biztosít. Ez lehetővé teszi a forgatókönyvek (automatizált megoldások) létrehozását és a biztonsági problémák megoldására való felhasználását.

A MariaDB komplex veszélyforrások elleni védelemének engedélyezése: https://docs.microsoft.com/azure/mariadb/howto-database-threat-protection-portal

A MariDB konfigurálása és elérése a kiszolgálók naplófájljaiban: https://docs.microsoft.com/azure/mariadb/concepts-server-logs

A MariaDB naplózási naplóinak konfigurálása és elérése: https://docs.microsoft.com/azure/mariadb/howto-configure-audit-logs-portal

Az Azure Sentinel előkészítése: https://docs.microsoft.com/azure/sentinel/quickstart-onboard

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="28-centralize-anti-malware-logging"></a>2,8: kártevő szoftverek közötti naplózás központosítása

**Útmutató**: N/A; A MariaDB nem dolgoz fel és nem hoz létre kártevő-ellenes kapcsolódó naplókat.

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: nem alkalmazható

### <a name="29-enable-dns-query-logging"></a>2,9: DNS-lekérdezések naplózásának engedélyezése

**Útmutató**: N/A; A MariaDB nem dolgozza fel vagy hozza létre a DNS-sel kapcsolatos naplókat.

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: nem alkalmazható

### <a name="210-enable-command-line-audit-logging"></a>2,10: parancssori naplózás engedélyezése

**Útmutató**: N/A; a teljesítményteszt a számítási erőforrások számára készült.

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: nem alkalmazható

## <a name="identity-and-access-control"></a>Identitás- és hozzáférés-vezérlés

*További információ [: Security Control (identitás és Access Control](../security/benchmarks/security-control-identity-access-control.md)).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3,1: a felügyeleti fiókok leltárának karbantartása

**Útmutató**: a MariaDB-példányok felügyeleti síkjával (Azure Portal/Azure Resource Manager) rendszergazdai hozzáféréssel rendelkező felhasználói fiókok leltárának fenntartása. Emellett a MariaDB-példányok adatsíkja számára elérhető rendszergazdai fiókok leltárát is megtarthatja. (A MariaDB-kiszolgáló létrehozásakor meg kell adnia egy rendszergazdai felhasználó hitelesítő adatait. Ennek a rendszergazdának a használatával további MariaDB-felhasználók hozhatók létre.)

A MariaDB hozzáférés-kezelésének megismerése: https://docs.microsoft.com/azure/mariadb/concepts-security#access-management

Ismerje meg az Azure-előfizetések Azure-beli beépített szerepköreit: https://docs.microsoft.com/azure/role-based-access-control/built-in-roles


**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="32-change-default-passwords-where-applicable"></a>3,2: az alapértelmezett jelszavak módosítása, ha alkalmazható

**Útmutató**: a Azure Active Directory nem rendelkezik az alapértelmezett jelszavak fogalmával.

A MariaDB-erőforrás létrehozása után az Azure erős jelszóval kényszeríti a rendszergazda felhasználó létrehozását. A MariaDB-példány létrehozása után azonban a létrehozott első kiszolgálói rendszergazdai fiók használatával további felhasználókat hozhat létre, és rendszergazdai hozzáférést biztosíthat számukra. A fiókok létrehozásakor ügyeljen arra, hogy az egyes fiókokhoz eltérő, erős jelszót állítson be.

További fiókok létrehozása a MariaDB: https://docs.microsoft.com/azure/mariadb/howto-create-users


**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="33-use-dedicated-administrative-accounts"></a>3,3: dedikált rendszergazdai fiókok használata

**Útmutató**: szabványos működési eljárások létrehozása a MariaDB-példányokhoz hozzáféréssel rendelkező dedikált rendszergazdai fiókok használatával. A rendszergazdai fiókok számának figyeléséhez használja a Azure Security Center identitás-és hozzáférés-kezelés lehetőséget.

Azure Security Center identitás és hozzáférés ismertetése: https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3,4: egyszeri bejelentkezés (SSO) használata Azure Active Directory

**Útmutató**: az MariaDB-hez való adatsíkokat az adatbázisban tárolt identitások vezérlik, és nem támogatja az egyszeri bejelentkezést. A MariaDB-hozzáférés vezérlése REST APIon keresztül érhető el, és támogatja az egyszeri bejelentkezést. A hitelesítéshez állítsa be a kérések engedélyezési fejlécét egy Azure Active Directoryból beszerzett JSON Web Tokenra.

Azure Database for MariaDB REST API ismertetése: https://docs.microsoft.com/rest/api/mariadb/

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3,5: Multi-Factor Authentication használata az összes Azure Active Directory-alapú hozzáféréshez

**Útmutató**: az Azure ad MFA engedélyezése és a Azure Security Center identitás-és hozzáférés-kezelési javaslatok követése.

Az MFA engedélyezése az Azure-ban: https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

Identitás és hozzáférés figyelése Azure Security Centeron belül: https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3,6: dedikált gépek (privilegizált hozzáférési munkaállomások) használata az összes felügyeleti feladathoz

**Útmutató**: az Azure-erőforrások bejelentkezésére és konfigurálására szolgáló, az MFA használatára konfigurált, a mancsok (emelt szintű hozzáférési munkaállomások) használata.

További tudnivalók az emelt szintű hozzáférésű munkaállomásokról: https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/

Az MFA engedélyezése az Azure-ban: https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="37-log-and-alert-on-suspicious-activity-from-administrative-accounts"></a>3,7: naplózás és riasztás a gyanús tevékenységekről a rendszergazdai fiókoktól

**Útmutató**: a MariaDB komplex veszélyforrások elleni védelem engedélyezése a gyanús tevékenységekre vonatkozó riasztások létrehozásához.

Emellett a naplók és a riasztások generálásához Azure AD Privileged Identity Management (PIM) is használható, ha a környezetben gyanús vagy nem biztonságos tevékenység történik. Az Azure AD-kockázati észlelések használatával a kockázatos felhasználói viselkedésre vonatkozó riasztásokat és jelentéseket tekinthet meg.

Komplex veszélyforrások elleni védelem beállítása a MariaDB: https://docs.microsoft.com/azure/mariadb/howto-database-threat-protection-portal

Privileged Identity Management (PIM) üzembe helyezése: https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-deployment-plan

Az Azure AD kockázati észlelések ismertetése: https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risk-events

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3,8: az Azure-erőforrások kezelése csak jóváhagyott helyekről

**Útmutató**: a feltételes hozzáférés elnevezett helyeivel engedélyezheti a hozzáférést az IP-címtartományok vagy országok/régiók meghatározott logikai csoportjaiból, hogy korlátozza az Azure-erőforrásokhoz, például a MariaDB való hozzáférést.

Elnevezett helyszínek konfigurálása az Azure-ban: https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="39-use-azure-active-directory"></a>3,9: a Azure Active Directory használata

**Útmutató**: a Azure Active Directory (HRE) használata központi hitelesítési és engedélyezési rendszerrel. A HRE erős titkosítással védi az adatok védelmét a nyugalmi és átviteli állapotban lévő adatokhoz. A HRE a felhasználó hitelesítő adatait is sók, kivonatok és biztonságosan tárolja.

Az Azure AD-hitelesítés nem használható közvetlen hozzáféréshez a MariaDB adatsíkon, azonban az Azure AD-hitelesítő adatok a felügyeleti sík szintjén (például a Azure Portal) használhatók a MariaDB rendszergazdai fiókok vezérléséhez.

A MariaDB rendszergazdai jelszavának frissítése: https://docs.microsoft.com/azure/mariadb/howto-create-manage-server-portal#update-admin-password

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="310-regularly-review-and-reconcile-user-access"></a>3,10: a felhasználói hozzáférés rendszeres áttekintése és egyeztetése

**Útmutató**: Tekintse át a Azure Active Directory naplókat, hogy segítsen felderíteni az elavult fiókokat, amelyek magukban foglalhatják a MariaDB rendszergazdai szerepköröket. Emellett az Azure Identity Access Reviews használatával hatékonyan kezelhetők a csoporttagságok, hozzáférhetnek a vállalati alkalmazásokhoz, amelyek a MariaDB és a szerepkör-hozzárendelések elérésére használhatók. A felhasználók hozzáférését rendszeresen felül kell vizsgálni, például 90 naponta, hogy csak a megfelelő felhasználók férhessenek hozzájuk.

Az Azure AD jelentéskészítés ismertetése: https://docs.microsoft.com/azure/active-directory/reports-monitoring/

Az Azure Identity hozzáférési felülvizsgálatok használata: https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="311-monitor-attempts-to-access-deactivated-accounts"></a>3,11: az inaktivált fiókok elérésére irányuló kísérletek figyelése

**Útmutató**: a MariaDB és a Azure Active Directory diagnosztikai beállításainak engedélyezése, az összes napló küldése egy log Analytics-munkaterületre. Konfigurálja a kívánt riasztásokat (például sikertelen hitelesítési kísérleteket) Log Analytics munkaterületen belül.

A MariaDB konfigurálása és elérése a kiszolgálók naplófájljaiban: https://docs.microsoft.com/azure/mariadb/concepts-server-logs

A MariaDB naplózási naplóinak konfigurálása és elérése: https://docs.microsoft.com/azure/mariadb/howto-configure-audit-logs-portal

Azure-beli tevékenység-naplók integrálása a Azure Monitorba: https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics

**Azure Security Center figyelés**: nem érhető el

**Felelősség**: Ügyfél

### <a name="312-alert-on-account-login-behavior-deviation"></a>3,12: riasztás a fiók bejelentkezési viselkedésének eltérése esetén

**Útmutató**: a MariaDB komplex veszélyforrások elleni védelemének engedélyezése. A Azure Database for MariaDB komplex veszélyforrások elleni védelme olyan rendellenes tevékenységeket észlel, amelyek szokatlan és potenciálisan ártalmas kísérleteket jeleznek az adatbázisok eléréséhez vagy kiaknázásához.

A Azure Active Directory Identity Protection és kockázati észlelési funkciói segítségével konfigurálhatja az észlelt gyanús műveletekre vonatkozó automatizált válaszokat. A szervezet biztonsági válaszainak megvalósításához az Azure Sentinel használatával engedélyezheti az automatikus válaszokat.

A MariaDB komplex veszélyforrások elleni védelemének engedélyezése: https://docs.microsoft.com/azure/mariadb/howto-database-threat-protection-portal

Az Identity Protection kockázati házirendjeinek konfigurálása és engedélyezése: https://docs.microsoft.com/azure/active-directory/identity-protection/howto-identity-protection-configure-risk-policies

Az Azure AD kockázatos bejelentkezések megtekintése: https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risky-sign-ins

Az Azure Sentinel előkészítése: https://docs.microsoft.com/azure/sentinel/quickstart-onboard

**Azure Security Center figyelés**: nem érhető el

**Felelősség**: Ügyfél

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3,13: a Microsoft számára elérhetővé teszi a megfelelő ügyféladatokat a támogatási forgatókönyvek során

**Útmutató**: nem alkalmazható; Azure Database for MariaDB Ügyfélszéf még nem támogatott.

Ügyfélszéf támogatott szolgáltatások listája: https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview#supported-services-and-scenarios-in-general-availability

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: nem alkalmazható

## <a name="data-protection"></a>Adatvédelem

*További információkért lásd [: biztonsági ellenőrzés:](../security/benchmarks/security-control-data-protection.md)adatvédelem.*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4,1: bizalmas információk leltárának fenntartása

**Útmutató**: a címkék használatával segítheti a Azure Database for MariaDB példányok és a bizalmas adatokat tároló vagy feldolgozó kapcsolódó erőforrások nyomon követését.

Címkék létrehozása és használata: https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4,2: bizalmas adatok tárolására vagy feldolgozására szolgáló rendszerek elkülönítése

**Útmutató**: különálló előfizetések és/vagy felügyeleti csoportok megvalósítása fejlesztési, tesztelési és éles környezetekhez. A MariaDB-példányok hálózati hozzáférésének elkülönítéséhez és korlátozásához használja a magánhálózati kapcsolat, a szolgáltatási végpontok és/vagy a MariaDB kombinációját.

További Azure-előfizetések létrehozása: https://docs.microsoft.com/azure/billing/billing-create-subscription

Management Groups létrehozása: https://docs.microsoft.com/azure/governance/management-groups/create

Privát hivatkozás konfigurálása Azure Database for MariaDBhoz: https://docs.microsoft.com/azure/mariadb/concepts-data-access-security-private-link

Szolgáltatási végpontok konfigurálása Azure Database for MariaDBhoz: https://docs.microsoft.com/azure/mariadb/howto-manage-vnet-portal

Tűzfalszabályok konfigurálása Azure Database for MariaDB esetén: https://docs.microsoft.com/azure/mariadb/concepts-firewall-rules

**Azure Security Center figyelés**: nem érhető el

**Felelősség**: Ügyfél

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4,3: a bizalmas adatok jogosulatlan átvitelének figyelése és letiltása

**Útmutató**: Ha Azure-beli virtuális gépeket használ a MariaDB-példányok eléréséhez, használja a privát hivatkozásokat, a MariaDB hálózati konfigurációkat, a hálózati biztonsági csoportokat és a szolgáltatási címkéket, hogy enyhítse az adatkiszűrése lehetőségét.

A Microsoft kezeli a MariaDB alapul szolgáló infrastruktúrát, és szigorú ellenőrzéseket vezetett be az ügyféladatok elvesztésének vagy kihatásának megelőzésére.

A Azure Database for MariaDB adatkiszűréseának enyhítése: https://docs.microsoft.com/azure/mariadb/concepts-data-access-security-private-link

Az ügyfelek adatvédelem az Azure-ban: https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4,4: minden bizalmas adat titkosítása az átvitel során

**Útmutató**: a Azure Database for MariaDB támogatja a Azure Database for MariaDB-kiszolgáló TRANSPORT Layer Security (TLS), korábbi nevén SSL (SSL) használatával történő csatlakoztatását az ügyfélalkalmazások számára. Az adatbázis-kiszolgáló és az ügyfélalkalmazások közötti TLS-kapcsolatok érvényesítése segít megvédeni a "férfit a középső" támadásokkal szemben azáltal, hogy titkosítja az adatfolyamot a kiszolgáló és az alkalmazás között. Az Azure Portalban győződjön meg arról, hogy az összes MariaDB-példány esetében engedélyezve van az "SSL-kapcsolat kikényszerítve" beállítás.

Titkosítás konfigurálása a MariaDB-ben: https://docs.microsoft.com/azure/mariadb/howto-configure-ssl

**Azure Security Center figyelés**: nem érhető el

**Felelősség**: Megosztott

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4,5: aktív felderítési eszköz használata a bizalmas adatok azonosítására

**Útmutató**: az adatazonosítási, besorolási és veszteség-megelőzési funkciók még nem érhetők el Azure Database for MariaDB számára. Külső gyártótól származó megoldás implementálása, ha az szükséges a megfelelőség szempontjából.

A Microsoft által felügyelt mögöttes platform esetében a Microsoft az összes vásárlói tartalmat bizalmasként kezeli, és az ügyfelek adatvesztésével és a kitettséggel szembeni védelem érdekében nagy hosszúságú. Annak biztosítása érdekében, hogy az Azure-beli ügyféladatok biztonságban maradjanak, a Microsoft végrehajtotta és karbantartja a robusztus adatvédelmi szabályozást és képességeket.

Az ügyfelek adatvédelem az Azure-ban: https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Azure Security Center figyelés**: nem érhető el

**Felelősség**: Megosztott

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4,6: az erőforrásokhoz való hozzáférés szabályozása az Azure RBAC

**Útmutató**: az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC) használata az Azure-adatbázishoz való hozzáférés vezérléséhez a MariaDB felügyeleti síkon (Azure Portal/Azure Resource Manager). Az adatsíkok eléréséhez (magán az adatbázison belül) használja az SQL-lekérdezéseket a felhasználók létrehozásához és a felhasználói engedélyek konfigurálásához.

Az Azure RBAC konfigurálása: https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal

A felhasználói hozzáférés konfigurálása az SQL for MariaDB használatával: https://docs.microsoft.com/azure/mariadb/howto-create-users

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4,7: a gazdagép-alapú adatvesztés-megelőzés használata a hozzáférés-vezérlés kikényszeríthető

**Útmutató**: N/A; Ez az útmutató számítási erőforrások számára készült.

A Microsoft kezeli a MariaDB alapul szolgáló infrastruktúrát, és szigorú ellenőrzéseket vezetett be az ügyféladatok elvesztésének vagy kihatásának megelőzésére.

Az ügyfelek adatvédelem az Azure-ban: https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Microsoft

### <a name="48-encrypt-sensitive-information-at-rest"></a>4,8: bizalmas adatok titkosítása a nyugalmi állapotban

**Útmutató**: a Azure Database for MariaDB szolgáltatás az FIPS 140-2 ellenőrzött titkosítási modult használja a REST-alapú adattárolási titkosításhoz. Az adatokat, beleértve a biztonsági másolatokat, a lemezeken titkosítva, a lekérdezések futtatásakor létrehozott ideiglenes fájlok kivételével. A szolgáltatás az Azure Storage-titkosításban található AES 256 bites titkosítást használja, és a kulcsokat a rendszer felügyeli. A tárolótitkosítás mindig be van kapcsolva, és nem tiltható le.

A MariaDB titkosításának megismerése: https://docs.microsoft.com/azure/mariadb/concepts-security

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Microsoft

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4,9: a kritikus Azure-erőforrások változásainak naplózása és riasztása

**Útmutató**: a Azure monitor és az Azure-tevékenység naplójának használata riasztások létrehozásához, amikor a módosítások a Azure Database for MariaDB és más kritikus vagy kapcsolódó erőforrások éles példányain lépnek életbe.

Riasztások létrehozása az Azure Activity log-eseményekhez: https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

## <a name="vulnerability-management"></a>Biztonságirés-kezelés

*További információ [: Security Control: sebezhetőségi kezelés](../security/benchmarks/security-control-vulnerability-management.md).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5,1: automatikus biztonsági rések vizsgálatára szolgáló eszközök futtatása

**Útmutató**: jelenleg nem érhető el; A Azure Security Center még nem támogatja a Azure Database for MariaDB Server sebezhetőségi felmérését.


**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5,2: az operációs rendszer automatikus javításának felügyeleti megoldásának telepítése

**Útmutató**: nem alkalmazható; Ez a javaslat számítási erőforrások számára készült.


**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: nem alkalmazható

### <a name="53-deploy-automated-third-party-software-patch-management-solution"></a>5,3: a harmadik féltől származó szoftveres javításokat kezelő megoldás telepítése

**Útmutató**: nem alkalmazható; Ez a javaslat számítási erőforrások számára készült.

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: nem alkalmazható

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5,4: a biztonsági rések keresésének összehasonlítása

**Útmutató**: nem alkalmazható; Ez a javaslat számítási erőforrások számára készült.


**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: nem alkalmazható

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5,5: kockázatértékelési folyamat használatával rangsorolhatja a felderített biztonsági rések szervizelését

**Útmutató**: a Microsoft a biztonsági rések kezelését a Azure Database for MariaDB-kiszolgálót támogató mögöttes rendszereken hajtja végre.


**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Microsoft

## <a name="inventory-and-asset-management"></a>Leltár-és eszközfelügyelet

*További információkért lásd [: biztonsági vezérlés: leltár és eszközkezelés](../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-azure-asset-discovery"></a>6,1: az Azure Asset Discovery használata

**Útmutató**: az Azure Resource Graph segítségével lekérdezheti és felderítheti az összes erőforrást (beleértve az Azure Database for MariaDB-kiszolgálót) az előfizetése (ke) n belül. Győződjön meg arról, hogy megfelelő (olvasási) engedélyekkel rendelkezik a bérlőben, és képes felsorolni az összes Azure-előfizetést, valamint az előfizetésében lévő erőforrásokat.

Lekérdezések létrehozása az Azure Resource Graph használatával: https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

Az Azure-előfizetések megtekintése: https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription

Az Azure RBAC ismertetése: https://docs.microsoft.com/azure/role-based-access-control/overview

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="62-maintain-asset-metadata"></a>6,2: az eszköz metaadatainak fenntartása

**Útmutató**: címkéket alkalmazhat a Azure Database for MariaDB kiszolgálóra és más kapcsolódó erőforrásokra, amelyekkel a metaadatok logikailag rendezhetők a besorolásba.

Címkék létrehozása és használata: https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="63-delete-unauthorized-azure-resources"></a>6,3: jogosulatlan Azure-erőforrások törlése

**Útmutató**: a címkézés, a felügyeleti csoportok és a különálló előfizetések használata, ahol szükséges, Azure Database for MariaDB-kiszolgáló és a kapcsolódó erőforrások rendszerezése és nyomon követése. Rendszeres időközönként egyeztetheti a leltárt, és gondoskodhat arról, hogy a jogosulatlan erőforrások törlése az előfizetésből időben történjen.

További Azure-előfizetések létrehozása: https://docs.microsoft.com/azure/billing/billing-create-subscription

Management Groups létrehozása: https://docs.microsoft.com/azure/governance/management-groups/create

Címkék létrehozása és használata: https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="64-maintain-an-inventory-of-approved-azure-resources-and-software-titles"></a>6,4: a jóváhagyott Azure-erőforrások és-szoftverek leltárának fenntartása

**Útmutató**: nem alkalmazható; Ez a javaslat a számítási erőforrások és az Azure egészének fedezésére szolgál.

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="65-monitor-for-unapproved-azure-resources"></a>6,5: a nem jóváhagyott Azure-erőforrások figyelése

**Útmutató**: a Azure Policy használatával korlátozásokat állíthat be az ügyfél-előfizetésekben létrehozható erőforrások típusára a következő beépített szabályzat-definíciók használatával:

- Nem engedélyezett erőforrástípusok

- Engedélyezett erőforrástípusok

Emellett az Azure Resource Graph használatával lekérdezheti vagy felderítheti az előfizetésben (k) belüli erőforrásokat.

Azure Policy konfigurálása és kezelése: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Lekérdezések létrehozása az Azure Graph használatával: https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6,6: a nem jóváhagyott szoftveralkalmazások figyelése a számítási erőforrásokon belül

**Útmutató**: nem alkalmazható; Ez a javaslat számítási erőforrások számára készült.


**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: nem alkalmazható

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6,7: nem jóváhagyott Azure-erőforrások és szoftveralkalmazások eltávolítása

**Útmutató**: nem alkalmazható; Ez a javaslat a számítási erőforrások és az Azure egészének fedezésére szolgál.



**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: nem alkalmazható

### <a name="68-use-only-approved-applications"></a>6,8: csak jóváhagyott alkalmazások használata

**Útmutató**: nem alkalmazható; Ez a javaslat számítási erőforrások számára készült.


**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: nem alkalmazható

### <a name="69-use-only-approved-azure-services"></a>6,9: csak jóváhagyott Azure-szolgáltatások használata

**Útmutató**: a Azure Policy használatával korlátozásokat állíthat be az ügyfél-előfizetésekben létrehozható erőforrások típusára a következő beépített szabályzat-definíciók használatával:

- Nem engedélyezett erőforrástípusok

- Engedélyezett erőforrástípusok

Azure Policy konfigurálása és kezelése: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Adott erőforrástípus megtagadása a következővel: Azure Policy: https://docs.microsoft.com/azure/governance/policy/samples/not-allowed-resource-types



**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="610-implement-approved-application-list"></a>6,10: jóváhagyott alkalmazások listájának implementálása

**Útmutató**: nem alkalmazható; Ez a javaslat számítási erőforrások számára készült.


**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: nem alkalmazható

### <a name="611-divlimit-users-ability-to-interact-with-azure-resources-manager-via-scriptsdiv"></a>6,11: <div>A felhasználók az Azure Resources Managerrel való interakcióra való képességének korlátozása parancsfájlok használatával</div>

**Útmutató**: az Azure feltételes hozzáférés használatával korlátozhatja, hogy a felhasználók képesek legyenek a Azure Resource Manager interakcióra az "Microsoft Azure felügyelet" alkalmazás "hozzáférés tiltása" beállításával. Ez megakadályozhatja, hogy a magas biztonsági környezetben, például a bizalmas adatokat tartalmazó Azure Database for MariaDB kiszolgálón belül hozzanak létre és módosítsanak erőforrásokat.

A feltételes hozzáférés konfigurálása a Azure Resource Managerhoz való hozzáférés blokkolásához: https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management



**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6,12: korlátozza a felhasználók számára a parancsfájlok végrehajtásának lehetőségét a számítási erőforrásokon belül

**Útmutató**: nem alkalmazható; Ez a javaslat számítási erőforrások számára készült.

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: nem alkalmazható

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6,13: fizikailag vagy logikailag elkülöníthető a nagy kockázatú alkalmazások

**Útmutató**: nem alkalmazható; Ez a javaslat Azure App Service vagy számítási erőforrásokon futó webalkalmazásokhoz készült.


**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: nem alkalmazható

## <a name="secure-configuration"></a>Biztonságos konfiguráció

*További információkért lásd [: biztonság-vezérlés: biztonságos konfiguráció](../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7,1: biztonságos konfigurációk létrehozása az összes Azure-erőforráshoz

**Útmutató**: a Azure Database for MariaDB példányok szabványos biztonsági konfigurációinak meghatározása és implementálása Azure Policy használatával. Használjon Azure Policy aliasokat a "Microsoft. DBforMariaDB" névtérben egyéni szabályzatok létrehozásához a Azure Database for MariaDB-kiszolgálók hálózati konfigurációjának naplózásához vagy betartatásához. A Azure Database for MariaDB-kiszolgálókhoz kapcsolódó beépített szabályzat-definíciókat is használhatja, például:

- A Geo-redundáns biztonsági mentést engedélyezni kell a Azure Database for MariaDB

Az elérhető Azure Policy aliasok megtekintése: https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias

Azure Policy konfigurálása és kezelése: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage



**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="72-establish-secure-operating-system-configurations"></a>7,2: biztonságos operációsrendszer-konfigurációk létrehozása

**Útmutató**: nem alkalmazható; Ez a javaslat számítási erőforrások számára készült.

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: nem alkalmazható

### <a name="73-maintain-secure-azure-resource-configurations"></a>7,3: biztonságos Azure-erőforrás-konfigurációk karbantartása

**Útmutató**: az Azure-erőforrások biztonságos beállításainak betartatásához használja a Azure Policy [deny] és a [telepítés ha nem létezik] lehetőséget.

Azure Policy konfigurálása és kezelése: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Azure Policy effektusok ismertetése: https://docs.microsoft.com/azure/governance/policy/concepts/effects



**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="74-maintain-secure-operating-system-configurations"></a>7,4: az operációs rendszer biztonságos konfigurációjának fenntartása

**Útmutató**: nem alkalmazható; Ez a javaslat számítási erőforrások számára készült.

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: nem alkalmazható

### <a name="75-securely-store-configuration-of-azure-resources"></a>7,5: az Azure-erőforrások biztonságos tárolása

**Útmutató**: ha a Azure Database for MariaDB-kiszolgálókhoz és a kapcsolódó erőforrásokhoz egyéni Azure Policy-definíciókat használ, az Azure Repos segítségével biztonságosan tárolhatja és kezelheti a kódot.

[Kód tárolása az Azure DevOps](/azure/devops/repos/git/gitworkflow?view=azure-devops&preserve-view=true)

[Az Azure Repos dokumentációja](/azure/devops/repos/index?view=azure-devops&preserve-view=true)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="76-securely-store-custom-operating-system-images"></a>7,6: az egyéni operációsrendszer-lemezképek biztonságos tárolása

**Útmutató**: nem alkalmazható; Ez a javaslat számítási erőforrások számára készült.

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: nem alkalmazható

### <a name="77-deploy-system-configuration-management-tools"></a>7,7: rendszerkonfiguráció-felügyeleti eszközök telepítése

**Útmutató**: Azure Policy aliasok használata a "Microsoft. DBforMariaDB" névtérben egyéni szabályzatok létrehozásához a riasztáshoz, a naplózáshoz és a rendszer-konfigurációk kényszerítéséhez. Emellett dolgozzon ki egy folyamatot és egy folyamatot a házirend-kivételek kezeléséhez.

Azure Policy konfigurálása és kezelése: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage



**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7,8: rendszerkonfiguráció-felügyeleti eszközök központi telepítése operációs rendszerekhez

**Útmutató**: nem alkalmazható; Ez a javaslat számítási erőforrások számára készült.

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: nem alkalmazható

### <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7,9: az Azure-szolgáltatások automatizált konfigurációs figyelésének megvalósítása

**Útmutató**: Azure Policy aliasok használata a "Microsoft. DBforMariaDB" névtérben egyéni szabályzatok létrehozásához a riasztáshoz, a naplózáshoz és a rendszer-konfigurációk kényszerítéséhez. A Azure Database for MariaDB-példányok és a kapcsolódó erőforrások konfigurációjának automatikus érvényesítéséhez használja a Azure Policy [audit], [megtagadás] és [üzembe helyezés ha nem létezik] lehetőséget.

Azure Policy konfigurálása és kezelése: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage



**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7,10: az operációs rendszerek automatikus konfiguráció-figyelésének megvalósítása

**Útmutató**: nem alkalmazható; Ez a javaslat számítási erőforrások számára készült.

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: nem alkalmazható

### <a name="711-manage-azure-secrets-securely"></a>7,11: az Azure-titkok biztonságos kezelése

**Útmutató**: a Azure Database for MariaDB-kiszolgálók elérésére használt Azure app Service-on futó Azure-Virtual Machines vagy webalkalmazások esetében a Managed Service Identity használata Azure Key Vault a kiszolgáló titkos felügyeletének egyszerűsítése és biztonságossá tétele érdekében. Győződjön meg arról, Key Vault a Soft delete engedélyezve van.

Integráció az Azure felügyelt identitásokkal: https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity

Key Vault létrehozása: https://docs.microsoft.com/azure/key-vault/general/quick-create-portal

A Key Vault hitelesítése: https://docs.microsoft.com/azure/key-vault/general/authentication

Key Vault hozzáférési szabályzatok kiosztása: https://docs.microsoft.com/azure/key-vault/general/assign-access-policy-portal

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="712-manage-identities-securely-and-automatically"></a>7,12: az identitások biztonságos és automatikus kezelése

**Útmutató**: a Azure Database for MariaDB kiszolgáló jelenleg nem támogatja az adatbázisok elérését Azure Active Directory hitelesítéshez.  A Azure Database for MariaDB-kiszolgáló létrehozásakor meg kell adnia egy rendszergazdai felhasználó hitelesítő adatait. Ennek a rendszergazdának a segítségével további MariaDB-felhasználókat hozhat létre.  

A Azure Database for MariaDB-kiszolgáló elérésére használt Azure App Service-on futó Azure-Virtual Machines vagy webalkalmazások esetén a Managed Service Identity a Azure Key Vault a Azure Database for MariaDB kiszolgáló hitelesítő adatainak tárolására és lekérésére használható.  Győződjön meg arról, Key Vault a Soft delete engedélyezve van.

Felügyelt identitások használatával biztosíthatja az Azure-szolgáltatások automatikus felügyelt identitását Azure Active Directoryban (AD). A felügyelt identitások lehetővé teszik bármely olyan szolgáltatás hitelesítését, amely támogatja az Azure AD-hitelesítést, beleértve a Key Vault is, a kódban szereplő hitelesítő adatok nélkül. Felügyelt identitások konfigurálása: https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm . Integráció az Azure felügyelt identitásokkal: https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity .



**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="713-eliminate-unintended-credential-exposure"></a>7,13: a hitelesítő adatok nem szándékolt expozíciójának megszüntetése

**Útmutató**: hitelesítő adatok beolvasása a programkódon belül a hitelesítő adatok azonosításához. A Credential Scanner a felfedezett hitelesítő adatok biztonságosabb helyre, például az Azure Key Vaultba való áthelyezésére is javaslatot tesz. 

A hitelesítőadat-olvasó beállítása: https://secdevtools.azurewebsites.net/helpcredscan.html

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

## <a name="malware-defense"></a>Kártevők elleni védelem

*További információkért lásd [: biztonsági ellenőrzés: kártevők elleni védelem](../security/benchmarks/security-control-malware-defense.md).*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8,1: központilag felügyelt kártevő szoftverek használata

**Útmutató**: nem alkalmazható; Ez a javaslat számítási erőforrások számára készült.

A Microsoft kártevő szoftveres verziója engedélyezve van az Azure-szolgáltatásokat támogató mögöttes gazdagépen (például Azure App Service), azonban az nem az ügyfél tartalmán fut.


**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: nem alkalmazható

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8,2: a nem számítási Azure-erőforrásokra feltöltött fájlok előzetes vizsgálata

**Útmutató**: a Microsoft kártevő szoftverrel való ellátása engedélyezve van az Azure-szolgáltatásokat támogató mögöttes gazdagépen (például Azure Database for MariaDB-kiszolgáló), azonban nem az ügyfél tartalmán fut.

A nem számítási Azure-erőforrásokra feltöltött tartalom előzetes vizsgálata, például App Service, Data Lake Storage, Blob Storage, Azure Database for MariaDB-kiszolgáló stb. A Microsoft nem fér hozzá az adataihoz ezekben a példányokban.

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Megosztott

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8,3: a kártevő szoftverek és az aláírások frissítésének ellenőrzése

**Útmutató**: nem alkalmazható; Ez a javaslat számítási erőforrások számára készült.

A Microsoft kártevő szoftveres verziója engedélyezve van az Azure-szolgáltatásokat támogató mögöttes gazdagépen (például Azure Database for MariaDB-kiszolgáló), azonban nem az ügyfél tartalmán fut.


**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: nem alkalmazható

## <a name="data-recovery"></a>Adat-helyreállítás

*További információkért lásd [: biztonsági ellenőrzés: adat-helyreállítás](../security/benchmarks/security-control-data-recovery.md).*

### <a name="91-ensure-regular-automated-back-ups"></a>9,1: rendszeres automatizált biztonsági másolatok biztosítása

**Útmutató**: a Azure Database for MariaDB teljes, differenciált és tranzakciós naplóbeli biztonsági mentést készít.  Azure Database for MariaDB automatikusan létrehozza a kiszolgáló biztonsági másolatait, és a helyileg redundáns vagy földrajzilag redundáns tárolóban tárolja azokat. A biztonsági másolatokkal a kiszolgáló adott időpontnak megfelelő állapotra állítható vissza. A biztonsági mentés és helyreállítás minden üzletmenet-folytonossági stratégia elengedhetetlen része, hiszen ez védi meg az adatokat a véletlen sérülésektől és törléstől.  Az alapértelmezett biztonsági mentési megőrzési időszak hét nap. Opcionálisan akár 35 napig is beállíthatja. Minden biztonsági mentés AES 256 bites titkosítással van titkosítva.

A MariaDB biztonsági mentések ismertetése:  https://docs.microsoft.com/azure/mariadb/concepts-backup

A MariaDB kezdeti konfigurációjának ismertetése: https://docs.microsoft.com/azure/mariadb/tutorial-design-database-using-portal



**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Megosztott

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: teljes rendszerbiztonsági mentés és biztonsági másolat készítése bármely ügyfél által felügyelt kulcsról

**Útmutató**: a Azure Database for MariaDB automatikusan létrehozza a kiszolgáló biztonsági másolatait, és tárolja őket a helyileg redundáns vagy geo-redundáns tárolóban. A biztonsági másolatokkal a kiszolgáló adott időpontnak megfelelő állapotra állítható vissza.  A biztonsági mentés és helyreállítás minden üzletmenet-folytonossági stratégia elengedhetetlen része, hiszen ez védi meg az adatokat a véletlen sérülésektől és törléstől.

Ha a MariaDB-kiszolgálón tárolt adathoz Key Vault használ az ügyféloldali adattitkosításhoz, győződjön meg arról, hogy a kulcsok rendszeres automatizált biztonsági mentései vannak.

A MariaDB biztonsági mentések ismertetése:  https://docs.microsoft.com/azure/mariadb/concepts-backup

Key Vault kulcsok biztonsági mentése:  https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey


**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Megosztott

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: az összes biztonsági másolat ellenőrzése, beleértve az ügyfél által felügyelt kulcsokat

**Útmutató**: Azure Database for MariaDB a biztonsági mentések rendszeres teszteléséhez hajtson végre visszaállítást az eredeti kiszolgáló biztonsági másolatainak használatával. Kétféle visszaállítás érhető el: az időponthoz tartozó visszaállítás és a Geo-visszaállítás. Az időponthoz való visszaállítás a Backup redundancia beállítással érhető el, és egy új kiszolgálót hoz létre ugyanabban a régióban, ahol az eredeti kiszolgáló található. A Geo-visszaállítás csak akkor érhető el, ha a kiszolgálót a földrajzilag redundáns tároláshoz konfigurálta, és lehetővé teszi a kiszolgáló másik régióba való visszaállítását.

A helyreállítás becsült ideje több tényezőtől függ, többek között az adatbázisok méretétől, a tranzakciós napló méretétől, a hálózati sávszélességtől és az azonos régióban lévő adatbázisok teljes számától. A helyreállítási idő általában kevesebb, mint 12 óra.

A Azure Database for MariaDB biztonsági mentésének és visszaállításának megismerése:  https://docs.microsoft.com/azure/mariadb/concepts-backup#restore


**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: a biztonsági másolatok és az ügyfél által felügyelt kulcsok védelmének biztosítása

**Útmutató**: a Azure Database for MariaDB teljes, differenciált és tranzakciós naplóbeli biztonsági mentést készít. Ezek a biztonsági másolatok lehetővé teszik a kiszolgálók visszaállítását bármely időpontra a beállított biztonsági mentési megőrzési időszakon belül. Az alapértelmezett biztonsági mentési megőrzési időszak hét nap. Opcionálisan akár 35 napig is beállíthatja. Minden biztonsági mentés AES 256 bites titkosítással van titkosítva.

A Azure Database for MariaDB biztonsági mentésének és visszaállításának megismerése:  https://docs.microsoft.com/azure/mariadb/concepts-backup


**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

## <a name="incident-response"></a>Incidensmegoldás

*További információ [: Security Control: incidens válasza](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10,1: incidens-válaszi útmutató létrehozása

**Útmutató**: Alakítson ki incidenskezelési útmutatót a vállalat számára. Gondoskodjon írásos incidenskezelési tervekről, amelyek definiálják az összes résztvevő szerepkörét, valamint az incidenskezelés fázisait az észleléstől az incidens utáni értékelésig.

- Útmutató a saját biztonsági incidensek megoldási folyamatának létrehozásához: https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/
- A Microsoft Security Response Center egy Incidensének anatómiája: https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/
- Az ügyfél a NIST számítógépes biztonsági incidensek kezelési útmutatóját is kihasználhatja a saját incidens-válasz tervének létrehozásához: https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final 

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10,2: incidensek pontozásának és rangsorolási eljárásának létrehozása

**Útmutató**: a Security Center súlyosságot rendel az egyes riasztásokhoz, hogy a prioritások alapján ki lehessen deríteni, hogy mely riasztásokat kell először megvizsgálni. A súlyosság azon alapul, hogy az Security Center milyen mértékben szerepel a riasztás kibocsátásához használt elemzésben vagy elemzésben, valamint azt a megbízhatósági szintet, amely a riasztáshoz vezető tevékenység mögött rosszindulatú szándékú volt. 

Emellett egyértelműen megjelölheti az előfizetéseket (pl.: éles környezetben, nem gyártva) címkék használatával és elnevezési rendszer létrehozása az Azure-erőforrások egyértelmű azonosításához és kategorizálásához, különösen a bizalmas adatok feldolgozásához.  Az Ön felelőssége, hogy rangsorolja a riasztások megoldását azon Azure-erőforrások és -környezetek kritikussága alapján, ahol az incidens történt.

- Biztonsági riasztások a Azure Security Centerban: https://docs.microsoft.com/azure/security-center/security-center-alerts-overview

- Címkék használata az Azure-erőforrások rendszerezéséhez: https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="103-test-security-response-procedures"></a>10,3: biztonsági reagálási eljárások tesztelése

**Útmutató**: az Azure-erőforrások védelmének biztosítása érdekében a rendszer az incidensek reagálási képességeinek rendszeres tesztelésére szolgáló gyakorlatokat hajt végre. Azonosítsa a gyenge pontokat és réseket, és szükség esetén dolgozza át a tervet.

- Tekintse meg a NIST kiadványát: útmutató az IT-csomagok és-képességek teszteléséhez, betanításához és gyakorlatának megtervezéséhez: https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10,4: biztonsági incidensek elérhetőségének biztosítása és riasztási értesítések konfigurálása biztonsági incidensekhez

**Útmutató**: a Microsoft a biztonsági incidensek elérhetőségi adatait arra használja fel, hogy felvegye Önnel a kapcsolatot, ha a Microsoft Security Response Center (MSRC) felfedi, hogy az adatokat egy törvénytelen vagy jogosulatlan fél is hozzáférte. A problémák megoldása érdekében tekintse át az incidenseket a tény után.

- A Azure Security Center biztonsági kapcsolattartó beállítása: https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10,5: biztonsági riasztások beépítése az incidensek gyorsreagáló rendszerébe

**Útmutató**: az Azure Security Center-riasztások és javaslatok exportálása a folyamatos exportálás funkcióval az Azure-erőforrásokkal kapcsolatos kockázatok azonosítása érdekében. A folyamatos exportálás lehetővé teszi a riasztások és javaslatok manuális és folyamatos exportálását. Az Azure Security Center adatösszekötővel továbbíthatja a riasztásokat az Azure Sentinel szolgáltatásba.

- Folyamatos exportálás konfigurálása: https://docs.microsoft.com/azure/security-center/continuous-export
- Riasztások továbbítása az Azure Sentinelbe: https://docs.microsoft.com/azure/sentinel/connect-azure-security-center

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="106-automate-the-response-to-security-alerts"></a>10,6: a biztonsági riasztásokra adott válasz automatizálása

**Útmutató**: a Azure Security Center munkafolyamat-automatizálási funkciója segítségével automatikusan elindíthatja a válaszokat az "Logic apps" használatával a biztonsági riasztások és az Azure-erőforrások védelme érdekében javasolt javaslatok alapján.
    

A Munkafolyamat-automatizálás és a Logic Apps konfigurálása: https://docs.microsoft.com/azure/security-center/workflow-automation

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

## <a name="penetration-tests-and-red-team-exercises"></a>Behatolási tesztek és Red Team-gyakorlatok

*További információkért lásd [: biztonsági ellenőrzés: behatolási tesztek és Red Team-gyakorlatok](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings-within-60-days"></a>11,1: rendszeres penetrációs tesztelést végez az Azure-erőforrásokon, és gondoskodik az összes kritikus biztonsági vizsgálat 60 napon belüli szervizeléséről

**Útmutató**: kövesse a Microsoft részvételi szabályait, hogy a behatolási tesztek ne sértsék a Microsoft-házirendeket:

https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1

A Microsoft által felügyelt felhőalapú infrastruktúrával, szolgáltatásokkal és alkalmazásokkal kapcsolatos további információkért tekintse meg a következő témakört:  https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e


**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Megosztott

## <a name="next-steps"></a>Következő lépések

- Lásd az [Azure biztonsági teljesítménytesztét](../security/benchmarks/overview.md)
- További információ az [Azure biztonsági Alaptervekről](../security/benchmarks/security-baselines-overview.md)