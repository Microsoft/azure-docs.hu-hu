---
title: Az Azure biztonsági alapkonfigurációja Azure Database for PostgreSQL egyetlen kiszolgálóhoz
description: Az Azure biztonsági alapkonfigurációja Azure Database for PostgreSQL egyetlen kiszolgálóhoz
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 03/23/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: a38bb12577fd646b2e7abf773dbb2a3f138f58fe
ms.sourcegitcommit: edccc241bc40b8b08f009baf29a5580bf53e220c
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/24/2020
ms.locfileid: "82127550"
---
# <a name="azure-security-baseline-for-azure-database-for-postgresql-single-server"></a>Az Azure biztonsági alapkonfigurációja Azure Database for PostgreSQL egyetlen kiszolgálóhoz

Az Azure Database for PostgreSQL-kiszolgáló Azure-beli biztonsági alapkonfigurációja olyan javaslatokat tartalmaz, amelyek segítségével javíthatja az üzemelő példány biztonsági állapotát.

A szolgáltatás alapterve az [Azure Security Benchmark 1,0-es verziójából](https://docs.microsoft.com/azure/security/benchmarks/overview)származik, amely javaslatokat tesz arra vonatkozóan, hogy miként védheti meg felhőalapú megoldásait az Azure-ban az ajánlott eljárásokat ismertető útmutató segítségével.

További információ: [Azure Security](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview)alapkonfigurációk áttekintése.

## <a name="network-security"></a>Hálózati biztonság

*További információkért lásd [: biztonsági ellenőrzés: hálózati biztonság](https://docs.microsoft.com/azure/security/benchmarks/security-control-network-security).*

### <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1,1: az erőforrások védelme hálózati biztonsági csoportokkal vagy Azure Firewall a Virtual Network

**Útmutató**: privát hivatkozás konfigurálása Azure Database for PostgreSQL magánhálózati végpontokkal. A privát hivatkozás lehetővé teszi, hogy egy privát végponton keresztül kapcsolódjon az Azure-beli különböző Pásti-szolgáltatásokhoz. Az Azure Private link lényegében az Azure-szolgáltatásokat a privát Virtual Networkon (VNet) belül hozza elérhetővé. A virtuális hálózat és a PostgreSQL-példány közötti forgalom a Microsoft gerinc hálózatán halad át.

Azt is megteheti, hogy Virtual Network szolgáltatás-végpontokat használ a Azure Database for PostgreSQL-implementációk hálózati hozzáférésének a megóvására és korlátozására. A virtuális hálózati szabályok egy tűzfal biztonsági funkciója, amely azt szabályozza, hogy a Azure Database for PostgreSQL-kiszolgáló fogadja-e a virtuális hálózatok egyes alhálózatai által továbbított kommunikációt.

A Azure Database for PostgreSQL-kiszolgáló a tűzfalszabályok használatával is biztonságossá tehető. A kiszolgáló tűzfala megakadályozza az adatbázis-kiszolgáló elérését, amíg meg nem adja, hogy mely számítógépek rendelkeznek engedéllyel. A tűzfal konfigurálásakor olyan tűzfalszabályokat adhat meg, amelyek meghatározzák az elfogadható IP-címtartományokat. A tűzfalszabályok a kiszolgáló szintjén hozhatók létre.

Privát hivatkozás konfigurálása Azure Database for PostgreSQLhoz:https://docs.microsoft.com/azure/postgresql/howto-configure-privatelink-portal

VNet-szolgáltatási végpontok és VNet szabályok létrehozása és kezelése a Azure Database for PostgreSQL-ben:https://docs.microsoft.com/azure/postgresql/howto-manage-vnet-using-portal

Azure Database for PostgreSQL tűzfalszabályok konfigurálása:https://docs.microsoft.com/azure/postgresql/howto-manage-firewall-using-portal

**Azure Security Center figyelés**: igen

**Felelősség**: ügyfél

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>1,2: a virtuális hálózatok, alhálózatok és hálózati adapterek konfigurációjának és forgalmának figyelése és naplózása

**Útmutató**: ha a Azure Database for PostgreSQL-példány védett egy privát végponthoz, a virtuális gépeket telepítheti ugyanabban a virtuális hálózatban. Hálózati biztonsági csoport (NSG) használatával csökkentheti az adatkiszűrése kockázatát. Engedélyezze a NSG folyamat naplóit, és küldje el a naplókat egy Storage-fiókba a forgalom naplózása érdekében. NSG-naplókat is küldhet egy Log Analytics munkaterületre, és a Traffic Analytics használatával betekintést nyerhet az Azure-Felhőbeli forgalomba. A Traffic Analytics egyes előnyei lehetővé teszi a hálózati tevékenységek megjelenítését és a gyakori pontok azonosítását, a biztonsági fenyegetések azonosítását, a forgalomban rejlő minták értelmezését, valamint a hálózati helytelen konfigurációk meghatározását.

Privát hivatkozás konfigurálása Azure Database for PostgreSQLhoz:https://docs.microsoft.com/azure/postgresql/howto-configure-privatelink-portal

A NSG folyamat naplófájljainak engedélyezése:https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

A Traffic Analytics engedélyezése és használata:https://docs.microsoft.com/azure/network-watcher/traffic-analytics

**Azure Security Center figyelés**: igen

**Felelősség**: ügyfél

### <a name="13-protect-critical-web-applications"></a>1,3: a kritikus webalkalmazások megóvása

**Útmutató**: nem alkalmazható; Ez a javaslat Azure App Service vagy számítási erőforrásokon futó webalkalmazásokhoz készült.

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: nem alkalmazható

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1,4: az ismert kártékony IP-címekkel folytatott kommunikáció megtagadása

**Útmutató**: az Azure Database for PostgreSQL komplex veszélyforrások elleni védelme. A komplex veszélyforrások elleni védelem olyan rendellenes tevékenységeket észlel, amelyek szokatlan és potenciálisan ártalmas kísérleteket jeleznek az adatbázisok eléréséhez vagy kiaknázásához.

A Azure Database for PostgreSQL-példányokhoz társított virtuális hálózatokon DDoS Protection szabványt engedélyezheti a DDoS-támadások elleni védelemhez. A Azure Security Center integrált fenyegetési intelligencia használatával megtagadhatja a kommunikációt az ismert kártékony vagy nem használt internetes IP-címekkel.

Komplex veszélyforrások elleni védelem konfigurálása Azure Database for PostgreSQL esetén:https://docs.microsoft.com/azure/postgresql/howto-database-threat-protection-portal

A DDoS Protection konfigurálása:https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection

**Azure Security Center figyelés**: igen

**Felelősség**: ügyfél

### <a name="15-record-network-packets-and-flow-logs"></a>1,5: hálózati csomagok és adatforgalmi naplók rögzítése

**Útmutató**: ha a Azure Database for PostgreSQL-példány védett egy privát végponthoz, a virtuális gépeket telepítheti ugyanabban a virtuális hálózatban. Ezután konfigurálhat egy hálózati biztonsági csoportot (NSG) az adatkiszűrése kockázatának csökkentése érdekében. Engedélyezze a NSG folyamat naplóit, és küldje el a naplókat egy Storage-fiókba a forgalom naplózása érdekében. NSG-naplókat is küldhet egy Log Analytics munkaterületre, és a Traffic Analytics használatával betekintést nyerhet az Azure-Felhőbeli forgalomba. A Traffic Analytics egyes előnyei lehetővé teszi a hálózati tevékenységek megjelenítését és a gyakori pontok azonosítását, a biztonsági fenyegetések azonosítását, a forgalomban rejlő minták értelmezését, valamint a hálózati helytelen konfigurációk meghatározását.

A NSG folyamat naplófájljainak engedélyezése:https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

A Traffic Analytics engedélyezése és használata:https://docs.microsoft.com/azure/network-watcher/traffic-analytics

**Azure Security Center figyelés**: igen

**Felelősség**: ügyfél

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1,6: hálózati alapú behatolás-észlelési/Behatolás-megelőzési rendszerek (AZONOSÍTÓk/IP-címek) üzembe helyezése

**Útmutató**: az Azure Database for PostgreSQL komplex veszélyforrások elleni védelme. A komplex veszélyforrások elleni védelem olyan rendellenes tevékenységeket észlel, amelyek szokatlan és potenciálisan ártalmas kísérleteket jeleznek az adatbázisok eléréséhez vagy kiaknázásához.

Komplex veszélyforrások elleni védelem konfigurálása Azure Database for PostgreSQL esetén:https://docs.microsoft.com/azure/postgresql/howto-database-threat-protection-portal

**Azure Security Center figyelés**: igen

**Felelősség**: ügyfél

### <a name="17-manage-traffic-to-web-applications"></a>1,7: webalkalmazások forgalmának kezelése

**Útmutató**: nem alkalmazható; Ez a javaslat Azure App Service vagy számítási erőforrásokon futó webalkalmazásokhoz készült.

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: nem alkalmazható

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1,8: a hálózati biztonsági szabályok bonyolultságának és adminisztratív terhelésének csökkentése

**Útmutató**: a Azure Database for PostgreSQL példányokhoz hozzáférést igénylő erőforrásokhoz használjon virtuális hálózati szolgáltatás címkéit a hálózati biztonsági csoportokon vagy Azure Firewall a hálózati hozzáférés-vezérlés definiálásához. Biztonsági szabályok létrehozása során szolgáltatáscímkéket használhat bizonyos IP-címek helyett. A szolgáltatási címke nevének (például: SQL) megadásával. WestUs) a szabály megfelelő forrás vagy cél mezőjében engedélyezheti vagy megtagadhatja a megfelelő szolgáltatás forgalmát. A Microsoft kezeli a szolgáltatási címke által felölelt címek előtagjait, és automatikusan frissíti a szolgáltatási címkét a címek változásával.

Megjegyzés: Azure Database for PostgreSQL a "Microsoft. SQL" szolgáltatás címkéjét használja.

További információ a szolgáltatási címkék használatáról:https://docs.microsoft.com/azure/virtual-network/service-tags-overview

A Azure Database for PostgreSQL szolgáltatás címkézési használatának ismertetése:https://docs.microsoft.com/azure/postgresql/concepts-data-access-and-security-vnet#terminology-and-description

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: ügyfél

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1,9: a hálózati eszközök szabványos biztonsági konfigurációinak fenntartása

**Útmutató**: szabványos biztonsági konfigurációk definiálása és implementálása a Azure Database for PostgreSQL-példányokhoz társított hálózati beállításokhoz és hálózati erőforrásokhoz Azure Policy. Használjon Azure Policy aliasokat a "Microsoft. DBforPostgreSQL" és a "Microsoft. Network" névterekben, hogy egyéni szabályzatokat hozzon létre a Azure Database for PostgreSQL példányok hálózati konfigurációjának naplózásához vagy érvénybe léptetéséhez. A hálózatkezeléssel vagy a Azure Database for PostgreSQL-példányokkal kapcsolatos beépített szabályzat-definíciókat is igénybe vehet, például:

- DDoS Protection a standardot engedélyezni kell

- Az SSL-kapcsolat betartatását engedélyezni kell a PostgreSQL-adatbázis-kiszolgálók esetében

Azure Policy konfigurálása és kezelése:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Azure Policy minták a hálózatkezeléshez:https://docs.microsoft.com/azure/governance/policy/samples/

Azure Blueprint létrehozása:https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: ügyfél

### <a name="110-document-traffic-configuration-rules"></a>1,10: a dokumentum forgalmának konfigurációs szabályai

**Útmutató**: a metaadatok és a logikai szervezet számára a Azure Database for PostgreSQL-példányok hálózati biztonságával és forgalmával kapcsolatos erőforrásokhoz használható címkék használata.

A címkézéshez kapcsolódó beépített Azure Policy-definíciók bármelyikét használhatja, például: "a címke és az érték megkövetelése", hogy az összes erőforrás címkével legyen létrehozva, és értesítse a meglévő címkézetlen erőforrásokról.

A Azure PowerShell vagy az Azure CLI használatával a címkék alapján kereshet vagy végezhet műveleteket az erőforrásokon.

Címkék létrehozása és használata:https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: ügyfél

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1,11: automatikus eszközök használata a hálózati erőforrások konfigurációjának figyelésére és a változások észlelésére

**Útmutató**: az Azure-tevékenység naplójának használata a hálózati erőforrás-konfigurációk figyelésére és a Azure Database for PostgreSQL-példányokhoz kapcsolódó hálózati erőforrások változásainak észlelésére. Hozzon létre riasztásokat Azure Monitoron belül, amelyek akkor lépnek életbe, amikor a kritikus hálózati erőforrásokra vonatkozó módosításokat végrehajtják

Az Azure Activity log eseményeinek megtekintése és beolvasása:https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view

Riasztások létrehozása a Azure Monitorban:https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: ügyfél

## <a name="logging-and-monitoring"></a>Naplózás és monitorozás

*További információ [: Security Control: naplózás és figyelés](https://docs.microsoft.com/azure/security/benchmarks/security-control-logging-monitoring).*

### <a name="21-use-approved-time-synchronization-sources"></a>2,1: a jóváhagyott idő-szinkronizálási források használata

**Útmutató**: a Microsoft fenntartja az Azure-erőforrásokhoz használt időforrást, például Azure Database for PostgreSQL a naplókban található időbélyegek esetében.


**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: Microsoft

### <a name="22-configure-central-security-log-management"></a>2,2: a központi biztonsági naplók felügyeletének konfigurálása

**Útmutató**: a diagnosztikai beállítások és a kiszolgálói naplók engedélyezése, valamint a naplók beolvasása a Azure Database for PostgreSQL példányai által generált biztonsági adatokat összesítve. A Azure Monitoron belül Log Analytics munkaterület (ek) használatával kérdezheti le és végezheti el az elemzéseket, és használhatja az Azure Storage-fiókokat a hosszú távú/archiválási tároláshoz. Alternatív megoldásként engedélyezheti és elvégezheti az Azure Sentinel vagy egy harmadik féltől származó SIEM-et.

A Azure Database for PostgreSQL kiszolgáló naplófájljainak konfigurálása és elérése:https://docs.microsoft.com/azure/postgresql/howto-configure-server-logs-in-portal

A Azure Database for PostgreSQL naplózási naplóinak konfigurálása és elérése:https://docs.microsoft.com/azure/postgresql/concepts-audit

Az Azure Sentinel előkészítése:https://docs.microsoft.com/azure/sentinel/quickstart-onboard

**Azure Security Center figyelés**: jelenleg nem érhető el

**Felelősség**: ügyfél

### <a name="23-enable-audit-logging-for-azure-resources"></a>2,3: az Azure-erőforrások naplózásának engedélyezése

**Útmutató**: a diagnosztikai beállítások engedélyezése a Azure Database for PostgreSQL példányokon a naplózási, biztonsági és erőforrás-naplókhoz való hozzáféréshez. Győződjön meg arról, hogy kifejezetten engedélyezi a PostgreSQL-naplót. A automatikusan elérhető tevékenység-naplók közé tartozik az eseményforrás, a dátum, a felhasználó, az időbélyeg, a forráscím, a célcím és más hasznos elemek. Engedélyezheti az Azure-műveletnapló diagnosztikai beállításait is, és elküldheti a naplókat ugyanarra a Log Analytics munkaterületre vagy Storage-fiókba.

A Azure Database for PostgreSQL kiszolgáló naplófájljainak konfigurálása és elérése:https://docs.microsoft.com/azure/postgresql/howto-configure-server-logs-in-portal

A Azure Database for PostgreSQL naplózási naplóinak konfigurálása és elérése:https://docs.microsoft.com/azure/postgresql/concepts-audit

Az Azure-beli tevékenység naplójának diagnosztikai beállításainak konfigurálása:https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings-legacy

**Azure Security Center figyelés**: jelenleg nem érhető el

**Felelősség**: ügyfél

### <a name="24-collect-security-logs-from-operating-systems"></a>2,4: biztonsági naplók gyűjtése az operációs rendszerekből

**Útmutató**: nem alkalmazható; Ez a javaslat számítási erőforrások számára készült.


**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: nem alkalmazható

### <a name="25-configure-security-log-storage-retention"></a>2,5: a biztonsági napló tárolási adatmegőrzésének konfigurálása

**Útmutató**: a Azure monitoron belül a Azure Database for PostgreSQL-naplók tárolására szolgáló log Analytics munkaterülethez a szervezet megfelelőségi szabályainak megfelelően állítsa be a megőrzési időszakot. Használja az Azure Storage-fiókokat hosszú távú/archiválási tároláshoz.

Log Analytics-munkaterületek naplózási megőrzési paramétereinek beállítása:https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period

Erőforrás-naplók tárolása egy Azure Storage-fiókban:https://docs.microsoft.com/azure/azure-monitor/platform/resource-logs-collect-storage

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: ügyfél

### <a name="26-monitor-and-review-logs"></a>2,6: naplók figyelése és áttekintése

**Útmutató**: az Azure Database for PostgreSQL-példányokból származó naplók elemzése és figyelése rendellenes viselkedés esetén. A naplók áttekintéséhez és a naplózási adatok lekérdezéséhez használja a Azure Monitor Log Analytics. Alternatív megoldásként engedélyezheti és elvégezheti az Azure Sentinel vagy egy harmadik fél SIEM-nek.

Az Azure Sentinel előkészítése:https://docs.microsoft.com/azure/sentinel/quickstart-onboard

További információ a Log Analyticsról:https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal

Egyéni lekérdezések végrehajtása a Azure Monitorban:https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-queries

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: ügyfél

### <a name="27-enable-alerts-for-anomalous-activity"></a>2,7: riasztások engedélyezése rendellenes tevékenységhez

**Útmutató**: a Azure Database for PostgreSQL komplex veszélyforrások elleni védelemének engedélyezése. A komplex veszélyforrások elleni védelem olyan rendellenes tevékenységeket észlel, amelyek szokatlan és potenciálisan ártalmas kísérleteket jeleznek az adatbázisok eléréséhez vagy kiaknázásához.

Emellett engedélyezheti a kiszolgáló naplófájljait és diagnosztikai beállításait a PostgreSQL-hez, és naplókat küldhet egy Log Analytics munkaterületre. A Log Analytics-munkaterületet az Azure Sentinelbe irányíthatja, mivel ez egy biztonsági előkészítési automatizált választ (felszárnyaló) megoldást biztosít. Ez lehetővé teszi a forgatókönyvek (automatizált megoldások) létrehozását és a biztonsági problémák megoldására való felhasználását.

A Azure Database for PostgreSQL komplex veszélyforrások elleni védelemének engedélyezése:https://docs.microsoft.com/azure/postgresql/howto-database-threat-protection-portal

A Azure Database for PostgreSQL kiszolgáló naplófájljainak konfigurálása és elérése:https://docs.microsoft.com/azure/postgresql/howto-configure-server-logs-in-portal

A Azure Database for PostgreSQL naplózási naplóinak konfigurálása és elérése:https://docs.microsoft.com/azure/postgresql/concepts-audit

Az Azure-beli tevékenység naplójának diagnosztikai beállításainak konfigurálása:https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings-legacy

Az Azure Sentinel előkészítése:https://docs.microsoft.com/azure/sentinel/quickstart-onboard

**Azure Security Center figyelés**: igen

**Felelősség**: ügyfél

### <a name="28-centralize-anti-malware-logging"></a>2,8: kártevő szoftverek közötti naplózás központosítása

**Útmutató**: nem alkalmazható; Azure Database for PostgreSQL nem dolgoz fel kártevő szoftverrel kapcsolatos naplókat, illetve nem hoz létre.

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: nem alkalmazható

### <a name="29-enable-dns-query-logging"></a>2,9: DNS-lekérdezések naplózásának engedélyezése

**Útmutató**: nem alkalmazható; A Azure Database for PostgreSQL nem dolgozza fel a DNS-sel kapcsolatos naplókat, és nem hoz létre.

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: nem alkalmazható

### <a name="210-enable-command-line-audit-logging"></a>2,10: parancssori naplózás engedélyezése

**Útmutató**: nem alkalmazható; Ez a javaslat számítási erőforrások számára készült.

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: nem alkalmazható

## <a name="identity-and-access-control"></a>Identitás- és hozzáférés-vezérlés

*További információ [: Security Control (identitás és Access Control](https://docs.microsoft.com/azure/security/benchmarks/security-control-identity-access-control)).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3,1: a felügyeleti fiókok leltárának karbantartása

**Útmutató**: a Azure Database for PostgreSQL példányok vezérlési síkjával (például Azure Portal) rendszergazdai hozzáféréssel rendelkező felhasználói fiókok leltárának fenntartása. Emellett tartson fenn egy leltárt azokról a rendszergazdai fiókokról, amelyek hozzáférnek a Azure Database for PostgreSQL példányainak adatsíkjával (az adatbázison belül). (A PostgreSQL-kiszolgáló létrehozásakor meg kell adnia egy rendszergazdai felhasználó hitelesítő adatait. Ez a rendszergazda további PostgreSQL-felhasználók létrehozására használható.)

A Azure Database for PostgreSQL nem támogatja a beépített szerepköralapú hozzáférés-vezérlést, de adott erőforrás-szolgáltatói műveletek alapján egyéni szerepköröket is létrehozhat.

Az Azure-előfizetés egyéni szerepköreinek megismerése:https://docs.microsoft.com/azure/role-based-access-control/custom-roles 

Azure Database for PostgreSQL erőforrás-szolgáltatói műveletek ismertetése:https://docs.microsoft.com/azure/role-based-access-control/resource-provider-operations#microsoftdbforpostgresql 

A Azure Database for PostgreSQL hozzáférés-kezelésének megismerése:https://docs.microsoft.com/azure/postgresql/concepts-security#access-management

**Azure Security Center figyelés**: igen

**Felelősség**: ügyfél

### <a name="32-change-default-passwords-where-applicable"></a>3,2: az alapértelmezett jelszavak módosítása, ha alkalmazható

**Útmutató**: a Azure Active Directory és a Azure Database for PostgreSQL nem rendelkezik az alapértelmezett jelszavak fogalmával.

A Azure Database for PostgreSQL erőforrás létrehozásakor az Azure erős jelszóval kényszeríti a rendszergazda felhasználó létrehozását. A PostgreSQL-példány létrehozása után azonban a létrehozott első kiszolgálói rendszergazdai fiók használatával további felhasználókat hozhat létre, és rendszergazdai hozzáférést biztosíthat számukra. A fiókok létrehozásakor ügyeljen arra, hogy az egyes fiókokhoz eltérő, erős jelszót állítson be.

További fiókok létrehozása Azure Database for PostgreSQLhoz:https://docs.microsoft.com/azure/postgresql/howto-create-users

Rendszergazdai jelszó frissítése:https://docs.microsoft.com/azure/postgresql/howto-create-manage-server-portal#update-admin-password

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: ügyfél

### <a name="33-use-dedicated-administrative-accounts"></a>3,3: dedikált rendszergazdai fiókok használata

**Útmutató**: szabványos üzemeltetési eljárások létrehozása a Azure Database for PostgreSQL példányokhoz hozzáféréssel rendelkező dedikált rendszergazdai fiókok használatával. A rendszergazdai fiókok számának figyeléséhez használja a Azure Security Center identitás-és hozzáférés-kezelés lehetőséget. 

Azure Security Center identitás és hozzáférés ismertetése:https://docs.microsoft.com/azure/security-center/security-center-identity-access 

Ismerje meg, hogyan hozhat létre rendszergazda felhasználókat a Azure Database for PostgreSQLban:https://docs.microsoft.com/azure/postgresql/howto-create-users#the-server-admin-account


**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: ügyfél

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3,4: egyszeri bejelentkezés (SSO) használata Azure Active Directory

**Útmutató**: az Azure Database for PostgreSQLba való bejelentkezés támogatott a közvetlenül az adatbázisban konfigurált Felhasználónév/jelszó használatával, valamint egy Azure Active Directory (ad) identitás használatával, valamint egy Azure ad-jogkivonat a kapcsolódáshoz való felhasználásával. Azure AD-token használatakor a rendszer különböző módszereket támogat, például egy Azure AD-felhasználót, egy Azure AD-csoportot vagy egy, az adatbázishoz csatlakozó Azure AD-alkalmazást.

Külön, az ellenőrző sík hozzáférése a PostgreSQL-hez REST APIon keresztül érhető el, és támogatja az egyszeri bejelentkezést. A hitelesítéshez állítsa be a kérések engedélyezési fejlécét egy Azure Active Directoryból beszerzett JSON Web Tokenra.

Azure Active Directory használata a Azure Database for PostgreSQL való hitelesítéshez:https://docs.microsoft.com/azure/postgresql/howto-configure-sign-in-aad-authentication

Azure Database for PostgreSQL REST API ismertetése:https://docs.microsoft.com/rest/api/postgresql/

Az SSO megismerése az Azure AD-vel:https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: ügyfél

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3,5: Multi-Factor Authentication használata az összes Azure Active Directory-alapú hozzáféréshez

**Útmutató**: a Azure Active Directory multi-Factor Authentication (MFA) engedélyezése és a Azure Security Center identitás-és hozzáférés-kezelési javaslatok követése. Ha Azure AD-jogkivonatokat használ az adatbázisba való bejelentkezéshez, ez lehetővé teszi a többtényezős hitelesítés megkövetelését az adatbázis-bejelentkezésekhez.

Az MFA engedélyezése az Azure-ban:https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

Azure Active Directory használata a Azure Database for PostgreSQL való hitelesítéshez:https://docs.microsoft.com/azure/postgresql/howto-configure-sign-in-aad-authentication

Identitás és hozzáférés figyelése Azure Security Centeron belül:https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Azure Security Center figyelés**: igen

**Felelősség**: ügyfél

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3,6: dedikált gépek (privilegizált hozzáférési munkaállomások) használata az összes felügyeleti feladathoz

**Útmutató**: az Azure-erőforrások bevezetésére és konfigurálására konfigurált, multi-Factor Authentication (MFA) rendszerjogosultságú hozzáférési munkaállomások használata.

További tudnivalók az emelt szintű hozzáférésű munkaállomásokról:https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations

Az MFA engedélyezése az Azure-ban:https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: ügyfél

### <a name="37-log-and-alert-on-suspicious-activity-from-administrative-accounts"></a>3,7: naplózás és riasztás a gyanús tevékenységekről a rendszergazdai fiókoktól

**Útmutató**: a Azure Database for PostgreSQL komplex veszélyforrások elleni védelemének engedélyezése a gyanús tevékenységekre vonatkozó riasztások létrehozásához.

Emellett Azure Active Directory (AD) Privileged Identity Management (PIM) is használható a naplók és riasztások generálásához, ha a környezetben gyanús vagy nem biztonságos tevékenység történik.

Az Azure AD-kockázati észlelések használatával a kockázatos felhasználói viselkedésre vonatkozó riasztásokat és jelentéseket tekinthet meg.

A komplex veszélyforrások elleni védelem beállítása Azure Database for PostgreSQL esetén:https://docs.microsoft.com/azure/postgresql/howto-database-threat-protection-portal

Privileged Identity Management (PIM) üzembe helyezése:https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-deployment-plan

Az Azure AD kockázati észlelések ismertetése:https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risk-events

**Azure Security Center figyelés**: igen

**Felelősség**: ügyfél

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3,8: az Azure-erőforrások kezelése csak jóváhagyott helyekről

**Útmutató**: a feltételes hozzáférés elnevezett helyeivel lehetővé teheti a portál és Azure Resource Manager hozzáférését az IP-címtartományok vagy országok/régiók adott logikai csoportjaiból.

Elnevezett helyszínek konfigurálása az Azure-ban:https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: ügyfél

### <a name="39-use-azure-active-directory"></a>3,9: a Azure Active Directory használata

**Útmutató**: a Azure Active Directory (ad) használata központi hitelesítési és engedélyezési rendszerrel. Az Azure AD az adatok védelme érdekében erős titkosítást használ a nyugalmi és a továbbítási adatokhoz. Az Azure AD emellett a felhasználó hitelesítő adatainak a sók, a kivonatok és a biztonságos tárolását is tartalmazza.

Az Azure Database for PostgreSQLba való bejelentkezéshez ajánlott az Azure AD használata, és egy Azure AD-jogkivonat használata a kapcsolódáshoz. Azure AD-token használatakor a rendszer különböző módszereket támogat, például egy Azure AD-felhasználót, egy Azure AD-csoportot vagy egy, az adatbázishoz csatlakozó Azure AD-alkalmazást.

Az Azure AD hitelesítő adatai a felügyeleti sík szintjén (például Azure Portal) is használhatók a PostgreSQL rendszergazdai fiókok vezérléséhez.

Azure Active Directory használata a Azure Database for PostgreSQL való hitelesítéshez:https://docs.microsoft.com/azure/postgresql/howto-configure-sign-in-aad-authentication

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: ügyfél

### <a name="310-regularly-review-and-reconcile-user-access"></a>3,10: a felhasználói hozzáférés rendszeres áttekintése és egyeztetése

**Útmutató**: Tekintse át a Azure Active Directory naplókat, hogy segítsen felderíteni az elavult fiókokat, amelyek magukban foglalhatják Azure Database for PostgreSQL rendszergazdai szerepköröket. Emellett az Azure Identity Access Reviews használatával hatékonyan kezelhetők a csoporttagságok, hozzáférhetnek a Azure Database for PostgreSQLhoz és a szerepkör-hozzárendelésekhez használható vállalati alkalmazásokhoz. A felhasználók hozzáférését rendszeresen felül kell vizsgálni, például 90 naponta, hogy csak a megfelelő felhasználók férhessenek hozzájuk.

Az Azure AD jelentéskészítés ismertetése:https://docs.microsoft.com/azure/active-directory/reports-monitoring/

Az Azure Identity hozzáférési felülvizsgálatok használata:https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview

A PostgreSQL-felhasználók és a hozzárendelt szerepkörök áttekintése:https://www.postgresql.org/docs/current/database-roles.html

**Azure Security Center figyelés**: igen

**Felelősség**: ügyfél

### <a name="311-monitor-attempts-to-access-deactivated-accounts"></a>3,11: az inaktivált fiókok elérésére irányuló kísérletek figyelése

**Útmutató**: a Azure Database for PostgreSQL és Azure Active Directory diagnosztikai beállításainak engedélyezése, az összes napló küldése egy log Analytics munkaterületre. Konfigurálja a kívánt riasztásokat (például sikertelen hitelesítési kísérleteket) Log Analyticson belül.

A Azure Database for PostgreSQL kiszolgáló naplófájljainak konfigurálása és elérése:https://docs.microsoft.com/azure/postgresql/howto-configure-server-logs-in-portal

A Azure Database for PostgreSQL naplózási naplóinak konfigurálása és elérése:https://docs.microsoft.com/azure/postgresql/concepts-audit

Azure-beli tevékenység-naplók integrálása a Azure Monitorba:https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics

**Azure Security Center figyelés**: jelenleg nem érhető el

**Felelősség**: ügyfél

### <a name="312-alert-on-account-login-behavior-deviation"></a>3,12: riasztás a fiók bejelentkezési viselkedésének eltérése esetén

**Útmutató**: a Azure Database for PostgreSQL komplex veszélyforrások elleni védelemének engedélyezése a gyanús tevékenységekre vonatkozó riasztások létrehozásához.

A Azure Active Directory Identity Protection és kockázati észlelési funkciói segítségével konfigurálhatja az észlelt gyanús műveletekre vonatkozó automatizált válaszokat. A szervezet biztonsági válaszainak megvalósításához az Azure Sentinel használatával engedélyezheti az automatikus válaszokat.

További vizsgálat céljából betöltheti a naplókat az Azure Sentinelbe.

A komplex veszélyforrások elleni védelem beállítása Azure Database for PostgreSQL esetén:https://docs.microsoft.com/azure/postgresql/howto-database-threat-protection-portal

A Azure AD Identity Protection áttekintése:https://docs.microsoft.com/azure/active-directory/identity-protection/overview-identity-protection

Az Azure AD kockázatos bejelentkezések megtekintése:https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risk-events

Az Azure Sentinel előkészítése:https://docs.microsoft.com/azure/sentinel/quickstart-onboard

**Azure Security Center figyelés**: igen

**Felelősség**: ügyfél

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3,13: a Microsoft számára elérhetővé teszi a megfelelő ügyféladatokat a támogatási forgatókönyvek során

**Útmutató**: jelenleg nem érhető el; Azure Database for PostgreSQL esetében Ügyfélszéf még nem támogatott.

Ügyfélszéf támogatott szolgáltatások listája:https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview#supported-services-and-scenarios-in-general-availability

**Azure Security Center figyelés**: jelenleg nem érhető el

**Felelősség**: ügyfél

## <a name="data-protection"></a>Adatvédelem

*További információkért lásd [: biztonsági ellenőrzés:](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-protection)adatvédelem.*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4,1: bizalmas információk leltárának fenntartása

**Útmutató**: a címkék használatával segítheti a Azure Database for PostgreSQL példányok és a bizalmas adatokat tároló vagy feldolgozó kapcsolódó erőforrások nyomon követését.

Címkék létrehozása és használata:https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: ügyfél

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4,2: bizalmas adatok tárolására vagy feldolgozására szolgáló rendszerek elkülönítése

**Útmutató**: különálló előfizetések és/vagy felügyeleti csoportok megvalósítása fejlesztési, tesztelési és éles környezetekhez. A magánhálózati kapcsolat, a szolgáltatási végpontok és/vagy a tűzfalszabályok együttes használatával elkülönítheti és korlátozhatja a Azure Database for PostgreSQL példányok hálózati hozzáférését.

További Azure-előfizetések létrehozása:https://docs.microsoft.com/azure/billing/billing-create-subscription

Management Groups létrehozása:https://docs.microsoft.com/azure/governance/management-groups/create

Privát hivatkozás konfigurálása Azure Database for PostgreSQLhoz:https://docs.microsoft.com/azure/postgresql/howto-configure-privatelink-portal

VNet-szolgáltatási végpontok és VNet szabályok létrehozása és kezelése a Azure Database for PostgreSQL-ben:https://docs.microsoft.com/azure/postgresql/howto-manage-vnet-using-portal

Azure Database for PostgreSQL tűzfalszabályok konfigurálása:https://docs.microsoft.com/azure/postgresql/concepts-firewall-rules

**Azure Security Center figyelés**: nem érhető el

**Felelősség**: ügyfél

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4,3: a bizalmas adatok jogosulatlan átvitelének figyelése és letiltása

**Útmutató**: Ha Azure-beli virtuális gépeket használ Azure Database for PostgreSQL példányok elérésére, használja a privát hivatkozásokat, a PostgreSQL hálózati konfigurációit, a hálózati biztonsági csoportokat és a szolgáltatás címkéit, hogy enyhítse az adatkiszűrése lehetőségét.

A Microsoft kezeli a Azure Database for PostgreSQL alapjául szolgáló infrastruktúrát, és szigorú ellenőrzéseket vezetett be az ügyféladatok elvesztésének vagy kihatásának megelőzésére.

A Azure Database for PostgreSQL adatkiszűréseának enyhítése:https://docs.microsoft.com/azure/postgresql/concepts-data-access-and-security-private-link

Az ügyfelek adatvédelem az Azure-ban:https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Azure Security Center figyelés**: igen

**Felelősség**: megosztott

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4,4: minden bizalmas adat titkosítása az átvitel során

**Útmutató**: a Azure Database for PostgreSQL támogatja a PostgreSQL-kiszolgáló és az ügyfélalkalmazások SSL (SSL) használatával történő összekapcsolását. Az adatbázis-kiszolgáló és az ügyfélalkalmazások közötti SSL-kapcsolatok kikényszerítése elősegíti a „köztes” támadások elleni védelmet, mert titkosítja a kiszolgáló és az alkalmazás közötti streameket. Az Azure Portalban alapértelmezés szerint az összes Azure Database for PostgreSQL példányra vonatkozóan engedélyezve van az "SSL-kapcsolat érvényesítése".

Jelenleg a Azure Database for PostgreSQL által támogatott TLS-verzió a TLS 1,0, a TLS 1,1, a TLS 1,2.

A titkosítás konfigurálása a Azure Database for PostgreSQL:https://docs.microsoft.com/azure/postgresql/concepts-ssl-connection-security

**Azure Security Center figyelés**: igen

**Felelősség**: megosztott

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4,5: aktív felderítési eszköz használata a bizalmas adatok azonosítására

**Útmutató**: az adatazonosítási, besorolási és veszteség-megelőzési funkciók még nem érhetők el Azure Database for PostgreSQL számára. Külső gyártótól származó megoldás implementálása, ha az szükséges a megfelelőség szempontjából.

A Microsoft által felügyelt mögöttes platform esetében a Microsoft az összes vásárlói tartalmat bizalmasként kezeli, és az ügyfelek adatvesztésével és a kitettséggel szembeni védelem érdekében nagy hosszúságú. Annak biztosítása érdekében, hogy az Azure-beli ügyféladatok biztonságban maradjanak, a Microsoft végrehajtotta és karbantartja a robusztus adatvédelmi szabályozást és képességeket.

Az ügyfelek adatvédelem az Azure-ban:https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Azure Security Center figyelés**: jelenleg nem érhető el

**Felelősség**: megosztott

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4,6: az erőforrásokhoz való hozzáférés szabályozása az Azure RBAC

**Útmutató**: az Azure szerepköralapú hozzáférés-vezérlés (RBAC) használata a Azure Database for PostgreSQL vezérlési síkon való hozzáférés vezérléséhez (például Azure Portal). Az adatsíkok eléréséhez (magán az adatbázison belül) használja az SQL-lekérdezéseket a felhasználók létrehozásához és a felhasználói engedélyek konfigurálásához. A RBAC nem befolyásolja a felhasználói engedélyeket az adatbázison belül.

A RBAC konfigurálása az Azure-ban:https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal

Felhasználói hozzáférés konfigurálása az SQL Azure Database for PostgreSQLhoz:https://docs.microsoft.com/azure/postgresql/howto-create-users

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: ügyfél

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4,7: a gazdagép-alapú adatvesztés-megelőzés használata a hozzáférés-vezérlés kikényszeríthető

**Útmutató**: nem alkalmazható; Ez a javaslat számítási erőforrások számára készült.

A Microsoft kezeli a Azure Database for PostgreSQL alapjául szolgáló infrastruktúrát, és szigorú ellenőrzéseket vezetett be az ügyféladatok elvesztésének vagy kihatásának megelőzésére.

Az ügyfelek adatvédelem az Azure-ban:https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: Microsoft

### <a name="48-encrypt-sensitive-information-at-rest"></a>4,8: bizalmas adatok titkosítása a nyugalmi állapotban

**Útmutató**: a Azure Database for PostgreSQL szolgáltatás az FIPS 140-2 ellenőrzött titkosítási modult használja a REST-alapú adattárolási titkosításhoz. Az adatokat, beleértve a biztonsági másolatokat, a lemezeken titkosítva, a lekérdezések futtatásakor létrehozott ideiglenes fájlok kivételével. A szolgáltatás az Azure Storage-titkosításban található AES 256 bites titkosítást használja, és a kulcsokat a rendszer felügyeli. A tárolótitkosítás mindig be van kapcsolva, és nem tiltható le.

Az ügyfél által felügyelt kulcsokkal (CMK) Azure Database for PostgreSQL egyetlen kiszolgálóval történő adattitkosítás lehetővé teszi a saját kulcs (BYOK) használatát a REST-alapú adatvédelem érdekében. Jelenleg a funkció használatához hozzáférést kell kérnie. Ehhez forduljon a következőhöz:

AskAzureDBforPostgreSQL@service.microsoft.com.

A Azure Database for PostgreSQL titkosításának megismerése:https://docs.microsoft.com/azure/postgresql/concepts-security

Az ügyfél által felügyelt kulcsok használata Azure Database for PostgreSQL a REST titkosítás megismerése:https://docs.microsoft.com/azure/postgresql/concepts-data-encryption-postgresql


**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: Microsoft

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4,9: a kritikus Azure-erőforrások változásainak naplózása és riasztása

**Útmutató**: a Azure monitor és az Azure-tevékenység naplójának használata riasztások létrehozásához, amikor a módosítások a Azure Database for PostgreSQL és más kritikus vagy kapcsolódó erőforrások éles példányain lépnek életbe.

Riasztások létrehozása az Azure Activity log-eseményekhez:https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: ügyfél

## <a name="vulnerability-management"></a>Biztonságirés-kezelés

*További információ [: Security Control: sebezhetőségi kezelés](https://docs.microsoft.com/azure/security/benchmarks/security-control-vulnerability-management).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5,1: automatikus biztonsági rések vizsgálatára szolgáló eszközök futtatása

**Útmutató**: jelenleg nem érhető el; A Azure Security Center még nem támogatja az Azure Database for PostgreSQL sebezhetőségi felmérését.

A Azure Security Center Azure Pásti szolgáltatásainak lefedettségi köre:https://docs.microsoft.com/azure/security-center/features-paas

**Azure Security Center figyelés**: jelenleg nem érhető el

**Felelősség**: ügyfél

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5,2: az operációs rendszer automatikus javításának felügyeleti megoldásának telepítése

**Útmutató**: nem alkalmazható; Ez a javaslat számítási erőforrások számára készült.

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: nem alkalmazható

### <a name="53-deploy-automated-third-party-software-patch-management-solution"></a>5,3: a harmadik féltől származó szoftveres javításokat kezelő megoldás telepítése

**Útmutató**: nem alkalmazható; Ez a javaslat számítási erőforrások számára készült.

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: nem alkalmazható

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5,4: a biztonsági rések keresésének összehasonlítása

**Útmutató**: nem alkalmazható; Ez a javaslat számítási erőforrások számára készült.

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: nem alkalmazható

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5,5: kockázatértékelési folyamat használatával rangsorolhatja a felderített biztonsági rések szervizelését

**Útmutató**: a Microsoft a sebezhetőségek kezelését a Azure Database for PostgreSQL támogató mögöttes rendszereken hajtja végre.

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: Microsoft

## <a name="inventory-and-asset-management"></a>Leltár-és eszközfelügyelet

*További információkért lásd [: biztonsági vezérlés: leltár és eszközkezelés](https://docs.microsoft.com/azure/security/benchmarks/security-control-inventory-asset-management).*

### <a name="61-use-azure-asset-discovery"></a>6,1: az Azure Asset Discovery használata

**Útmutató**: az Azure Resource Graph segítségével lekérdezheti és felderítheti az összes erőforrást (beleértve Azure Database for PostgreSQL példányokat is) az előfizetésében. Győződjön meg arról, hogy megfelelő (olvasási) engedélyekkel rendelkezik a bérlőben, és képes felsorolni az összes Azure-előfizetést, valamint az előfizetésében lévő erőforrásokat.

Lekérdezések létrehozása az Azure Resource Graph használatával:https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

Az Azure-előfizetések megtekintése:https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0

Az Azure RBAC ismertetése:https://docs.microsoft.com/azure/role-based-access-control/overview

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: ügyfél

### <a name="62-maintain-asset-metadata"></a>6,2: az eszköz metaadatainak fenntartása

**Útmutató**: címkéket alkalmazhat a Azure Database for PostgreSQL példányokra és egyéb kapcsolódó erőforrásokra, amelyek metaadatokat biztosítanak a besorolások logikai rendszerezéséhez.

Címkék létrehozása és használata:https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: ügyfél

### <a name="63-delete-unauthorized-azure-resources"></a>6,3: jogosulatlan Azure-erőforrások törlése

**Útmutató**: a címkézés, a felügyeleti csoportok és a különálló előfizetések használata, ahol szükséges, Azure Database for PostgreSQL példányok és kapcsolódó erőforrások rendszerezése és nyomon követése. Rendszeres időközönként egyeztetheti a leltárt, és gondoskodhat arról, hogy a jogosulatlan erőforrások törlése az előfizetésből időben történjen.

További Azure-előfizetések létrehozása:https://docs.microsoft.com/azure/billing/billing-create-subscription

Management Groups létrehozása:https://docs.microsoft.com/azure/governance/management-groups/create

Címkék létrehozása és használata:https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: ügyfél

### <a name="64-maintain-an-inventory-of-approved-azure-resources-and-software-titles"></a>6,4: a jóváhagyott Azure-erőforrások és-szoftverek leltárának fenntartása

**Útmutató**: nem alkalmazható; Ez a javaslat a számítási erőforrások és az Azure egészének fedezésére szolgál.

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: ügyfél

### <a name="65-monitor-for-unapproved-azure-resources"></a>6,5: a nem jóváhagyott Azure-erőforrások figyelése

**Útmutató**: az Azure Policy használatával korlátozásokat állíthat be az ügyfél-előfizetésekben létrehozható erőforrások típusára a következő beépített szabályzat-definíciók használatával:

- Nem engedélyezett erőforrástípusok

- Engedélyezett erőforrástípusok

Emellett az Azure Resource Graph használatával lekérdezheti vagy felderítheti az előfizetésben (k) belüli erőforrásokat.

Azure Policy konfigurálása és kezelése:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Lekérdezések létrehozása az Azure Graph használatával:https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: ügyfél

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6,6: a nem jóváhagyott szoftveralkalmazások figyelése a számítási erőforrásokon belül

**Útmutató**: nem alkalmazható; Ez a javaslat számítási erőforrások számára készült.

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: nem alkalmazható

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6,7: nem jóváhagyott Azure-erőforrások és szoftveralkalmazások eltávolítása

**Útmutató**: nem alkalmazható; Ez a javaslat a számítási erőforrások és az Azure egészének fedezésére szolgál.

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: nem alkalmazható

### <a name="68-use-only-approved-applications"></a>6,8: csak jóváhagyott alkalmazások használata

**Útmutató**: nem alkalmazható; Ez a javaslat számítási erőforrások számára készült.

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: nem alkalmazható

### <a name="69-use-only-approved-azure-services"></a>6,9: csak jóváhagyott Azure-szolgáltatások használata

**Útmutató**: az Azure Policy használatával korlátozásokat állíthat be az ügyfél-előfizetésekben létrehozható erőforrások típusára a következő beépített szabályzat-definíciók használatával:

- Nem engedélyezett erőforrástípusok

- Engedélyezett erőforrástípusok

Azure Policy konfigurálása és kezelése:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Adott erőforrástípus megtagadása a következővel: Azure Policy:https://docs.microsoft.com/azure/governance/policy/samples/not-allowed-resource-types

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: ügyfél

### <a name="610-implement-approved-application-list"></a>6,10: jóváhagyott alkalmazások listájának implementálása

**Útmutató**: nem alkalmazható; Ez a javaslat számítási erőforrások számára készült.

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: nem alkalmazható

### <a name="611-limit-users-ability-to-interact-with-azure-resources-manager-via-scripts"></a>6,11: a felhasználók az Azure Resources Managerrel való interakcióra való képességének korlátozása parancsfájlok használatával

**Útmutató**: az Azure feltételes hozzáférés használatával korlátozhatja, hogy a felhasználók képesek legyenek a Azure Resource Manager interakcióra az "Microsoft Azure felügyelet" alkalmazás "hozzáférés tiltása" beállításával. Ez megakadályozhatja az erőforrások létrehozását és módosítását a magas biztonsági környezetben, például a bizalmas adatokat tartalmazó Azure Database for PostgreSQL példányain.

A feltételes hozzáférés konfigurálása a Azure Resource Managerhoz való hozzáférés blokkolásához:https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: ügyfél

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6,12: korlátozza a felhasználók számára a parancsfájlok végrehajtásának lehetőségét a számítási erőforrásokon belül

**Útmutató**: nem alkalmazható; Ez a javaslat számítási erőforrások számára készült.

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: nem alkalmazható

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6,13: fizikailag vagy logikailag elkülöníthető a nagy kockázatú alkalmazások

**Útmutató**: nem alkalmazható; Ez a javaslat Azure App Service vagy számítási erőforrásokon futó webalkalmazásokhoz készült.

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: nem alkalmazható

## <a name="secure-configuration"></a>Biztonságos konfiguráció

*További információkért lásd [: biztonság-vezérlés: biztonságos konfiguráció](https://docs.microsoft.com/azure/security/benchmarks/security-control-secure-configuration).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7,1: biztonságos konfigurációk létrehozása az összes Azure-erőforráshoz

**Útmutató**: a Azure Database for PostgreSQL példányok szabványos biztonsági konfigurációinak meghatározása és implementálása Azure Policy használatával. Használjon Azure Policy aliasokat a "Microsoft. DBforPostgreSQL" névtérben egyéni szabályzatok létrehozásához a Azure Database for PostgreSQL példányok hálózati konfigurációjának naplózásához vagy érvénybe léptetéséhez. A Azure Database for PostgreSQL-példányokhoz kapcsolódó beépített szabályzat-definíciókat is használhatja, például:

- Az SSL-kapcsolat betartatását engedélyezni kell a PostgreSQL-adatbázis-kiszolgálók esetében

- A naplózási kapcsolatokat engedélyezni kell a PostgreSQL adatbázis-kiszolgálóinak

Az elérhető Azure Policy aliasok megtekintése:https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0

Azure Policy konfigurálása és kezelése:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: ügyfél

### <a name="72-establish-secure-operating-system-configurations"></a>7,2: biztonságos operációsrendszer-konfigurációk létrehozása

**Útmutató**: nem alkalmazható; Ez a javaslat számítási erőforrások számára készült.

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: nem alkalmazható

### <a name="73-maintain-secure-azure-resource-configurations"></a>7,3: biztonságos Azure-erőforrás-konfigurációk karbantartása

**Útmutató**: az Azure Policy [megtagadás] és a [telepítés, ha nem létezik] az Azure-erőforrások biztonságos beállításainak betartatására használható.

Azure Policy konfigurálása és kezelése:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Azure Policy effektusok ismertetése:https://docs.microsoft.com/azure/governance/policy/concepts/effects

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: ügyfél

### <a name="74-maintain-secure-operating-system-configurations"></a>7,4: az operációs rendszer biztonságos konfigurációjának fenntartása

**Útmutató**: nem alkalmazható; Ez a javaslat számítási erőforrások számára készült.

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: nem alkalmazható

### <a name="75-securely-store-configuration-of-azure-resources"></a>7,5: az Azure-erőforrások biztonságos tárolása

**Útmutató**: Ha egyéni Azure-szabályzat-definíciókat használ a Azure Database for PostgreSQL példányaihoz és a kapcsolódó erőforrásokhoz, az Azure Repos használatával biztonságosan tárolhatja és kezelheti a kódot.

Kód tárolása az Azure DevOps:https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops

Az Azure Repos dokumentációja:https://docs.microsoft.com/azure/devops/repos/index?view=azure-devops

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: ügyfél

### <a name="76-securely-store-custom-operating-system-images"></a>7,6: az egyéni operációsrendszer-lemezképek biztonságos tárolása

**Útmutató**: nem alkalmazható; Ez a javaslat számítási erőforrások számára készült.

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: nem alkalmazható

### <a name="77-deploy-system-configuration-management-tools"></a>7,7: rendszerkonfiguráció-felügyeleti eszközök telepítése

**Útmutató**: Azure Policy aliasok használata a "Microsoft. DBforPostgreSQL" névtérben egyéni szabályzatok létrehozásához a riasztáshoz, a naplózáshoz és a rendszer-konfigurációk kényszerítéséhez. Emellett dolgozzon ki egy folyamatot és egy folyamatot a házirend-kivételek kezeléséhez.

Azure Policy konfigurálása és kezelése:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: ügyfél

### <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7,8: rendszerkonfiguráció-felügyeleti eszközök központi telepítése operációs rendszerekhez

**Útmutató**: nem alkalmazható; Ez a javaslat számítási erőforrások számára készült.

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: nem alkalmazható

### <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7,9: az Azure-szolgáltatások automatizált konfigurációs figyelésének megvalósítása

**Útmutató**: Azure Policy aliasok használata a "Microsoft. DBforPostgreSQL" névtérben egyéni szabályzatok létrehozásához a riasztáshoz, a naplózáshoz és a rendszer-konfigurációk kényszerítéséhez. A Azure Database for PostgreSQL példányok és a kapcsolódó erőforrások konfigurációjának automatikus érvényesítéséhez használja az Azure Policy [audit], [megtagadás] és [üzembe helyezés ha nem létezik] beállítást.

Azure Policy konfigurálása és kezelése:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: ügyfél

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7,10: az operációs rendszerek automatikus konfiguráció-figyelésének megvalósítása

**Útmutató**: nem alkalmazható; Ez a javaslat számítási erőforrások számára készült.

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: nem alkalmazható

### <a name="711-manage-azure-secrets-securely"></a>7,11: az Azure-titkok biztonságos kezelése

**Útmutató**: az Azure Virtual Machines vagy Azure app Service rendszeren futó webalkalmazásokhoz, amelyek a Azure Database for PostgreSQL példányok elérésére szolgálnak, a Managed Service Identity a Azure Key Vaultekkel együtt egyszerűsítheti és biztonságossá teheti Azure Database for PostgreSQL titkos felügyeletét. Győződjön meg arról, Key Vault a Soft delete engedélyezve van.

Integráció az Azure felügyelt identitásokkal:https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity

Key Vault létrehozása:https://docs.microsoft.com/azure/key-vault/quick-create-portal

Felügyelt identitással rendelkező Key Vault hitelesítés biztosítása:https://docs.microsoft.com/azure/key-vault/managed-identity

**Azure Security Center figyelés**: igen

**Felelősség**: ügyfél

### <a name="712-manage-identities-securely-and-automatically"></a>7,12: az identitások biztonságos és automatikus kezelése

**Útmutató**: a Azure Database for PostgreSQL-kiszolgáló az adatbázisok eléréséhez támogatja a Azure Active Directory hitelesítést (előzetes verzióban).  A Azure Database for PostgreSQL-kiszolgáló létrehozásakor meg kell adnia egy rendszergazdai felhasználó hitelesítő adatait. Ennek a rendszergazdának a segítségével további adatbázis-felhasználók hozhatók létre.  

A Azure Database for PostgreSQL-kiszolgáló elérésére használt Azure App Service-on futó Azure-Virtual Machines vagy webalkalmazások esetén a Managed Service Identity a Azure Key Vault a Azure Database for PostgreSQL kiszolgáló hitelesítő adatainak tárolására és lekérésére használható. Győződjön meg arról, Key Vault a Soft delete engedélyezve van.

Felügyelt identitások használatával biztosíthatja az Azure-szolgáltatások automatikus felügyelt identitását Azure Active Directoryban (AD). A felügyelt identitások lehetővé teszik bármely olyan szolgáltatás hitelesítését, amely támogatja az Azure AD-hitelesítést, beleértve a Key Vault is, a kódban szereplő hitelesítő adatok nélkül.

Felügyelt identitások konfigurálása:https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm

Integráció az Azure felügyelt identitásokkal:https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity

**Azure Security Center figyelés**: igen

**Felelősség**: ügyfél

### <a name="713-eliminate-unintended-credential-exposure"></a>7,13: a hitelesítő adatok nem szándékolt expozíciójának megszüntetése

**Útmutató**: hitelesítő adatok beolvasása a programkódon belül a hitelesítő adatok azonosításához. A hitelesítő adatok beolvasása azt is javasolja, hogy a felderített hitelesítő adatokat biztonságosabb helyszínekre (például Azure Key Vault) helyezze.

A hitelesítőadat-olvasó beállítása:https://secdevtools.azurewebsites.net/helpcredscan.html

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: ügyfél

## <a name="malware-defense"></a>Kártevők elleni védelem

*További információkért lásd [: biztonsági ellenőrzés: kártevők elleni védelem](https://docs.microsoft.com/azure/security/benchmarks/security-control-malware-defense).*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8,1: központilag felügyelt kártevő szoftverek használata

**Útmutató**: nem alkalmazható; Ez a javaslat számítási erőforrások számára készült.

A Microsoft kártevő szoftveres verziója engedélyezve van az Azure-szolgáltatásokat támogató mögöttes gazdagépen (például Azure App Service), azonban az nem az ügyfél tartalmán fut.


**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: Microsoft

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8,2: a nem számítási Azure-erőforrásokra feltöltött fájlok előzetes vizsgálata

**Útmutató**: a Microsoft kártevő szoftverrel való ellátása engedélyezve van az Azure-szolgáltatásokat támogató mögöttes gazdagépen (például Azure Database for PostgreSQL), azonban nem az ügyfél tartalmán fut.

A nem számítási Azure-erőforrásokra feltöltött tartalom előzetes vizsgálata, például App Service, Data Lake Storage, Blob Storage, Azure Database for PostgreSQL stb. A Microsoft nem fér hozzá az adataihoz ezekben a példányokban.

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: megosztott

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8,3: a kártevő szoftverek és az aláírások frissítésének ellenőrzése

**Útmutató**: nem alkalmazható; Ez a javaslat számítási erőforrások számára készült.

A Microsoft kártevő szoftveres verziója engedélyezve van az Azure-szolgáltatásokat támogató mögöttes gazdagépen (például Azure Database for PostgreSQL), azonban az nem az ügyfél tartalmán fut.

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: Microsoft

## <a name="data-recovery"></a>Adat-helyreállítás

*További információkért lásd [: biztonsági ellenőrzés: adat-helyreállítás](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-recovery).*

### <a name="91-ensure-regular-automated-back-ups"></a>9,1: rendszeres automatizált biztonsági másolatok biztosítása

**Útmutató**: a Azure Database for PostgreSQL biztonsági másolatokat készít az adatfájlokról és a tranzakciónaplóról. A maximálisan támogatott tárterület méretétől függően teljes és különbözeti biztonsági mentéseket (4 TB-os maximális tárolási kiszolgálókat) vagy pillanatképes biztonsági mentést (legfeljebb 16 TB-os maximális tárolási kiszolgálót) használhat. Ezek a biztonsági másolatok lehetővé teszik a kiszolgálók visszaállítását bármely időpontra a beállított biztonsági mentési megőrzési időszakon belül. Az alapértelmezett biztonsági mentési megőrzési időszak hét nap. Opcionálisan akár 35 napig is beállíthatja. Az összes biztonsági mentés titkosítása AES 256 bites titkosítás használatával történik.

Kiszolgáló biztonsági mentése Azure Database for PostgreSQLban:https://docs.microsoft.com/azure/postgresql/howto-restore-server-portal

Azure Database for PostgreSQL kezdeti konfiguráció ismertetése:https://docs.microsoft.com/azure/postgresql/tutorial-design-database-using-azure-portal

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: megosztott

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: teljes rendszerbiztonsági mentés és biztonsági másolat készítése bármely ügyfél által felügyelt kulcsról

**Útmutató**: a Azure Database for PostgreSQL automatikusan létrehozza a kiszolgáló biztonsági másolatait, és helyileg redundáns vagy földrajzilag redundáns tárolóban tárolja őket a felhasználó választása szerint. A biztonsági másolatokkal a kiszolgáló adott időpontnak megfelelő állapotra állítható vissza. A biztonsági mentés és a visszaállítás fontos részét képezi az üzletmenet folytonossági stratégiájának, mivel ezek az adatok a véletlen sérüléstől vagy törléstől védve vannak.

Ha a Azure Database for PostgreSQL-példányok hitelesítő adatainak tárolására Azure Key Vault használ, ügyeljen arra, hogy a kulcsok rendszeres automatikus biztonsági mentéseit tárolja.

Kiszolgáló biztonsági mentése Azure Database for PostgreSQLban:https://docs.microsoft.com/azure/postgresql/howto-restore-server-portal

Key Vault kulcsok biztonsági mentése:https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey

**Azure Security Center figyelés**: jelenleg nem érhető el

**Felelősség**: megosztott

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: az összes biztonsági másolat ellenőrzése, beleértve az ügyfél által felügyelt kulcsokat

**Útmutató**: Azure Database for PostgreSQL a Restore művelettel egy új kiszolgáló jön létre az eredeti kiszolgáló biztonsági másolatai közül. Kétféle visszaállítás érhető el: az időponthoz tartozó visszaállítás és a Geo-visszaállítás. Az időponthoz való visszaállítás a Backup redundancia beállítással érhető el, és egy új kiszolgálót hoz létre ugyanabban a régióban, ahol az eredeti kiszolgáló található. A Geo-visszaállítás csak akkor érhető el, ha a kiszolgálót a földrajzilag redundáns tároláshoz konfigurálta, és lehetővé teszi a kiszolgáló másik régióba való visszaállítását.

A helyreállítás becsült ideje több tényezőtől függ, többek között az adatbázisok méretétől, a tranzakciós napló méretétől, a hálózati sávszélességtől és az azonos régióban lévő adatbázisok teljes számától. A helyreállítási idő általában kevesebb, mint 12 óra.

A Azure Database for PostgreSQL példányok visszaállításának rendszeres tesztelése.

Kiszolgáló biztonsági mentése Azure Database for PostgreSQLban:https://docs.microsoft.com/azure/postgresql/howto-restore-server-portal

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: ügyfél

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: a biztonsági másolatok és az ügyfél által felügyelt kulcsok védelmének biztosítása

**Útmutató**: a Azure Database for PostgreSQL teljes, differenciált és tranzakciós naplóbeli biztonsági mentést készít. Ezek a biztonsági másolatok lehetővé teszik a kiszolgálók visszaállítását bármely időpontra a beállított biztonsági mentési megőrzési időszakon belül. Az alapértelmezett biztonsági mentési megőrzési időszak hét nap. Opcionálisan akár 35 napig is beállíthatja. Az összes biztonsági mentés titkosítása AES 256 bites titkosítás használatával történik.

A Azure Database for PostgreSQL biztonsági mentésének és visszaállításának megismerése:https://docs.microsoft.com/azure/postgresql/concepts-backup

**Azure Security Center figyelés**: igen

**Felelősség**: ügyfél

## <a name="incident-response"></a>Incidensmegoldás

*További információ [: Security Control: incidens válasza](https://docs.microsoft.com/azure/security/benchmarks/security-control-incident-response).*

### <a name="101-create-an-incident-response-guide"></a>10,1: incidens-válaszi útmutató létrehozása

**Útmutató**: az incidensekre adott válaszokra vonatkozó útmutató kiépítése a szervezet számára. Győződjön meg arról, hogy van olyan írásos incidens-válasz, amely meghatározza a személyzet összes szerepkörét, valamint az incidensek kezelésének és kezelésének fázisait az észleléstől az incidens utáni felülvizsgálatig.

Munkafolyamat-automatizálás konfigurálása Azure Security Centeron belül:https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide

Útmutató a saját biztonsági incidensek megoldási folyamatának létrehozásához:https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/

A Microsoft Security Response Center egy Incidensének anatómiája:https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/

Az ügyfél a NIST számítógépes biztonsági incidensek kezelési útmutatóját is kihasználhatja a saját incidens-válasz tervének létrehozásához:https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: ügyfél

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10,2: incidensek pontozásának és rangsorolási eljárásának létrehozása

**Útmutató**: a Security Center súlyosságot rendel az egyes riasztásokhoz, hogy a prioritások alapján ki lehessen deríteni, hogy mely riasztásokat kell először megvizsgálni. A súlyosság azon alapul, hogy az Security Center milyen mértékben szerepel a riasztás kibocsátásához használt elemzésben vagy elemzésben, valamint azt a megbízhatósági szintet, amely a riasztáshoz vezető tevékenység mögött rosszindulatú szándékú volt.

Emellett egyértelműen megjelölheti az előfizetéseket (pl.: éles környezetben nem termelt), és hozzon létre egy elnevezési rendszert az Azure-erőforrások egyértelmű azonosításához és kategorizálásához.

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: ügyfél

### <a name="103-test-security-response-procedures"></a>10,3: biztonsági reagálási eljárások tesztelése

**Útmutató**: a rendszerek incidensek reagálási képességeinek rendszeres tesztelésére szolgáló gyakorlatok végrehajtása. Azonosítsa a gyenge pontokat és a réseket, és szükség szerint módosítsa a tervet.

Tekintse meg a NIST kiadványát: útmutató az IT-csomagok és-képességek teszteléséhez, betanításához és gyakorlatának megtervezéséhez:https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: ügyfél

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10,4: biztonsági incidensek elérhetőségének biztosítása és riasztási értesítések konfigurálása biztonsági incidensekhez

**Útmutató**: a Microsoft a biztonsági incidensek elérhetőségi adatait arra használja fel, hogy felvegye Önnel a kapcsolatot, ha a Microsoft Security Response Center (MSRC) felfedi, hogy az ügyfél adatait egy törvénytelen vagy jogosulatlan fél is hozzáférte.  A problémák megoldása érdekében tekintse át az incidenseket a tény után.

A Azure Security Center biztonsági kapcsolattartó beállítása:https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details

**Azure Security Center figyelés**: igen

**Felelősség**: ügyfél

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10,5: biztonsági riasztások beépítése az incidensek gyorsreagáló rendszerébe

**Útmutató**: az Azure Security Center-riasztások és-javaslatok exportálása a folyamatos exportálás funkció használatával. A folyamatos exportálás lehetővé teszi a riasztások és javaslatok manuális és folyamatos exportálását. Használhatja a Azure Security Center adatösszekötőt a riasztások Sentinel továbbításához.

Folyamatos exportálás konfigurálása:https://docs.microsoft.com/azure/security-center/continuous-export

Riasztások továbbítása az Azure Sentinelbe:https://docs.microsoft.com/azure/sentinel/connect-azure-security-center

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: ügyfél

### <a name="106-automate-the-response-to-security-alerts"></a>10,6: a biztonsági riasztásokra adott válasz automatizálása

**Útmutató**: a Azure Security Center munkafolyamat-automatizálási funkciója segítségével automatikusan aktiválhatja a válaszokat a "Logic apps" használatával a biztonsági riasztások és javaslatok esetében.

A Munkafolyamat-automatizálás és a Logic Apps konfigurálása:https://docs.microsoft.com/azure/security-center/workflow-automation

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: ügyfél

## <a name="penetration-tests-and-red-team-exercises"></a>Behatolási tesztek és Red Team-gyakorlatok

*További információkért lásd [: biztonsági ellenőrzés: behatolási tesztek és Red Team-gyakorlatok](https://docs.microsoft.com/azure/security/benchmarks/security-control-penetration-tests-red-team-exercises).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings-within-60-days"></a>11,1: rendszeres penetrációs tesztelést végez az Azure-erőforrásokon, és gondoskodik az összes kritikus biztonsági vizsgálat 60 napon belüli szervizeléséről

**Útmutató**: kövesse a Microsoft részvételi szabályait, hogy a behatolási tesztek ne sértsék a Microsoft-házirendeket:https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1

A Microsoft által felügyelt felhőalapú infrastruktúrával, szolgáltatásokkal és alkalmazásokkal kapcsolatos további információkért tekintse meg a következő témakört:https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: megosztott

## <a name="next-steps"></a>További lépések

- Lásd az [Azure biztonsági teljesítménytesztét](https://docs.microsoft.com/azure/security/benchmarks/overview)
- További információ az [Azure biztonsági Alaptervekről](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview)
