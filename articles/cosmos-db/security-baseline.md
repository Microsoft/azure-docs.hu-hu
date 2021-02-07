---
title: Cosmos DB Azure biztonsági alapterve
description: Cosmos DB Azure biztonsági alapterve
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 03/16/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: b475e9c6b1a186d72a37d38633c500ee62e95ff6
ms.sourcegitcommit: 8245325f9170371e08bbc66da7a6c292bbbd94cc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/07/2021
ms.locfileid: "99805647"
---
# <a name="azure-security-baseline-for-cosmos-db"></a>Cosmos DB Azure biztonsági alapterve
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Az Cosmos DB Azure biztonsági alapkonfigurációja olyan javaslatokat tartalmaz, amelyek segítségével javíthatja az üzemelő példány biztonsági állapotát.

A szolgáltatás alapterve az [Azure Security Benchmark 1,0-es verziójából](../security/benchmarks/overview.md)származik, amely javaslatokat tesz arra vonatkozóan, hogy miként védheti meg felhőalapú megoldásait az Azure-ban az ajánlott eljárásokat ismertető útmutató segítségével.

További információ: [Azure Security](../security/benchmarks/security-baselines-overview.md)alapkonfigurációk áttekintése.

## <a name="network-security"></a>Hálózati biztonság

*További információkért lásd [: biztonsági ellenőrzés: hálózati biztonság](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1,1: az erőforrások védelme hálózati biztonsági csoportokkal vagy Azure Firewall a Virtual Network

**Útmutató**: az Azure Private link használatával privát végponton keresztül csatlakozhat egy Azure Cosmos-fiókhoz. A virtuális hálózat és a szolgáltatás közötti forgalom a Microsoft gerinchálózatán keresztül halad át, így kiküszöböli a nyilvános internet jelentette kitettséget. Emellett csökkentheti az adatkiszűrése kockázatát azáltal, hogy a kimenő szabályok szigorú készletét konfigurálja egy hálózati biztonsági csoportra (NSG), és társítja az adott NSG az alhálózattal.

Az Azure Cosmos-fiók biztonságossá tételéhez szolgáltatási végpontokat is használhat. A szolgáltatás-végpont engedélyezésével úgy konfigurálhatja az Azure Cosmos-fiókokat, hogy csak az Azure-beli virtuális hálózatok egy adott alhálózatáról engedélyezze a hozzáférést. Miután a Azure Cosmos DB szolgáltatás végpontja engedélyezve lett, korlátozhatja az Azure Cosmos-fiókhoz való hozzáférést egy virtuális hálózatban lévő alhálózatról érkező kapcsolattal.

Az Azure Cosmos-fiókban tárolt adatait IP-tűzfalak használatával is biztonságossá teheti. A Azure Cosmos DB támogatja az IP-alapú hozzáférés-vezérlést a bejövő tűzfalak támogatásához. Beállíthat egy IP-tűzfalat az Azure Cosmos-fiókban a Azure Portal, Azure Resource Manager sablonok vagy az Azure CLI vagy a Azure PowerShell használatával.

Az Azure Private link áttekintése: https://docs.microsoft.com/azure/private-link/private-link-overview

Privát végpont konfigurálása Azure Cosmos DBhoz:  https://docs.microsoft.com/azure/cosmos-db/how-to-configure-private-endpoints 

Hálózati biztonsági csoport létrehozása biztonsági konfigurációval:  https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic

Az IP-tűzfal konfigurálása a Cosmos DBban: https://docs.microsoft.com/azure/cosmos-db/how-to-configure-firewall

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>1,2: a virtuális hálózatok, alhálózatok és hálózati adapterek konfigurációjának és forgalmának figyelése és naplózása

**Útmutató**: a Azure Security Center használata és a hálózatvédelmi javaslatok követése az Azure Cosmos-fiókhoz kapcsolódó hálózati erőforrások biztonságossá tételéhez.

Ha a virtuális gépeket az Azure Cosmos-fiókkal megegyező virtuális hálózatban telepítik, akkor a hálózati biztonsági csoport (NSG) segítségével csökkentheti az adatkiszűrése kockázatát. NSG-adatforgalmi naplók engedélyezése és naplók küldése Azure Storage-fiókba forgalmi auditok esetén. NSG-naplókat is küldhet egy Log Analytics munkaterületre, és a Traffic Analytics használatával betekintést nyerhet az Azure-Felhőbeli forgalomba. A Traffic Analytics egyes előnyei lehetővé teszi a hálózati tevékenységek megjelenítését és a gyakori pontok azonosítását, a biztonsági fenyegetések azonosítását, a forgalomban rejlő minták értelmezését, valamint a hálózati helytelen konfigurációk meghatározását.

A Azure Security Center által biztosított hálózati biztonság ismertetése: https://docs.microsoft.com/azure/security-center/security-center-network-recommendations

A NSG folyamat naplófájljainak engedélyezése: https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

A Traffic Analytics engedélyezése és használata: https://docs.microsoft.com/azure/network-watcher/traffic-analytics

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="13-protect-critical-web-applications"></a>1,3: a kritikus webalkalmazások megóvása

**Útmutató**: az eltérő eredetű erőforrás-megosztás (CORS) szolgáltatással engedélyezheti, hogy az egyik tartományon futó webalkalmazások hozzáférhessenek egy másik tartomány erőforrásaihoz. A böngészők olyan biztonsági korlátozást valósítanak meg, amelyik azonos eredetű házirend, amely megakadályozza, hogy egy weblap egy másik tartományban lévő API-kat hívjon fel. A CORS azonban biztonságos módot biztosít ahhoz, hogy a forrás tartomány egy másik tartományban lévő API-kat hívjon fel. Miután engedélyezte az Azure Cosmos-fiók CORS-támogatását, a rendszer csak a hitelesített kérelmeket értékeli ki, hogy azok a megadott szabályoknak megfelelően engedélyezettek-e.

Az adatforrások közötti megosztás konfigurálása: https://docs.microsoft.com/azure/cosmos-db/how-to-configure-cross-origin-resource-sharing

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1,4: az ismert kártékony IP-címekkel folytatott kommunikáció megtagadása

**Útmutató**: a Azure Cosmos db komplex veszélyforrások elleni védelem (ATP) használata. Az ATP for Azure Cosmos DB egy további biztonsági intelligenciát biztosít, amely szokatlan és potenciálisan ártalmas kísérleteket észlel az Azure Cosmos-fiókok eléréséhez vagy kiaknázásához. Ez a védelmi réteg lehetővé teszi a fenyegetések kezelésére és a központi biztonsági figyelő rendszerekkel való integrálását.

A Azure Cosmos DB-példányokhoz társított virtuális hálózatokon DDoS Protection szabványt engedélyezheti a DDoS-támadások elleni védelemhez. A Azure Security Center integrált fenyegetési intelligencia használatával megtagadhatja a kommunikációt az ismert kártékony vagy nem használt internetes IP-címekkel.

Azure Cosmos DB komplex veszélyforrások elleni védelem konfigurálása: https://docs.microsoft.com/azure/cosmos-db/cosmos-db-advanced-threat-protection

A DDoS Protection konfigurálása: https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection

Az Azure Security Center integrált veszélyforrások felderítésének ismertetése: https://docs.microsoft.com/azure/security-center/security-center-alerts-service-layer

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="15-record-network-packets-and-flow-logs"></a>1,5: hálózati csomagok és adatforgalmi naplók rögzítése

**Útmutató**: engedélyezze a hálózati biztonsági csoport (NSG) folyamatának naplóit, és küldje el a naplókat a forgalom naplózásához a Storage-fiókba. NSG-naplókat küldhet egy Log Analytics munkaterületre, és a Traffic Analytics használatával betekintést nyerhet az Azure-Felhőbeli forgalomba. A Traffic Analytics egyes előnyei lehetővé teszi a hálózati tevékenységek megjelenítését és a gyakori pontok azonosítását, a biztonsági fenyegetések azonosítását, a forgalomban rejlő minták értelmezését, valamint a hálózati helytelen konfigurációk meghatározását.

A NSG folyamat naplófájljainak engedélyezése: https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

A Traffic Analytics engedélyezése és használata: https://docs.microsoft.com/azure/network-watcher/traffic-analytics

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1,6: hálózati alapú behatolás-észlelési/Behatolás-megelőzési rendszerek (AZONOSÍTÓk/IP-címek) üzembe helyezése

**Útmutató**: a Azure Cosmos db komplex veszélyforrások elleni védelem (ATP) használata. Az ATP for Azure Cosmos DB egy további biztonsági intelligenciát biztosít, amely szokatlan és potenciálisan ártalmas kísérleteket észlel az Azure Cosmos-fiókok eléréséhez vagy kiaknázásához. Ez a védelmi réteg lehetővé teszi a fenyegetések kezelésére és a központi biztonsági figyelő rendszerekkel való integrálását. 

Cosmos DB komplex veszélyforrások elleni védelem konfigurálása: https://docs.microsoft.com/azure/cosmos-db/cosmos-db-advanced-threat-protection

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="17-manage-traffic-to-web-applications"></a>1,7: webalkalmazások forgalmának kezelése

**Útmutató**: nem alkalmazható; a javaslat a Azure App Service vagy számítási erőforrásokon futó webalkalmazásokhoz készült.


**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: nem alkalmazható

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1,8: a hálózati biztonsági szabályok bonyolultságának és adminisztratív terhelésének csökkentése

**Útmutató**: az Azure Cosmos-fiókhoz hozzáféréssel rendelkező erőforrások esetében Virtual Network szolgáltatás-címkék használatával definiálhatja a hálózati biztonsági csoportokon vagy Azure Firewall a hálózati hozzáférés-vezérlést. Biztonsági szabályok létrehozása során szolgáltatáscímkéket használhat bizonyos IP-címek helyett. Ha egy szabály megfelelő forrás vagy cél mezőjében megadja a szolgáltatási címke nevét (például AzureCosmosDB), akkor engedélyezheti vagy megtagadhatja a megfelelő szolgáltatás forgalmát. A Microsoft kezeli a szolgáltatási címke által felölelt címek előtagjait, és automatikusan frissíti a szolgáltatási címkét a címek változásával.

További információ a szolgáltatási címkék használatáról: https://docs.microsoft.com/azure/virtual-network/service-tags-overview

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1,9: a hálózati eszközök szabványos biztonsági konfigurációinak fenntartása

**Útmutató**: hálózati erőforrások szabványos biztonsági konfigurációinak definiálása és implementálása Azure Policy használatával. Használjon Azure Policy aliasokat a "Microsoft.DocumentDB" és a "Microsoft. Network" névterekben, hogy egyéni házirendeket hozzon létre az Azure Cosmos DB példányok hálózati konfigurációjának naplózásához vagy érvénybe léptetéséhez. Használhatja a Azure Cosmos DB beépített szabályzat-definícióját is, például:

- A Cosmos DB-fiókok komplex veszélyforrások elleni védelmének üzembe helyezése

- Cosmos DB virtuális hálózati szolgáltatás végpontját kell használnia

Az Azure-tervrajzok segítségével leegyszerűsítheti a nagy léptékű Azure-környezeteket a főbb környezeti összetevők, például a Azure Resource Manager sablonok, az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC) és a házirendek egyetlen terv szerinti definícióban való kicsomagolásával. A tervet egyszerűen alkalmazhatja új előfizetésekre, környezetekre, valamint a verziószámozáson keresztül történő finomhangolásra és felügyeletre.

Azure Policy konfigurálása és kezelése: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Azure Blueprint létrehozása: https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="110-document-traffic-configuration-rules"></a>1,10: a dokumentum forgalmának konfigurációs szabályai

**Útmutató**: a Azure Cosmos db üzembe helyezéséhez társított hálózati erőforrásokhoz használjon címkéket, hogy logikailag szervezze őket a besorolásba.

Címkék létrehozása és használata: https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1,11: automatikus eszközök használata a hálózati erőforrások konfigurációjának figyelésére és a változások észlelésére

**Útmutató**: az Azure-tevékenység naplójának használata a hálózati erőforrás-konfigurációk figyelésére és a Azure Cosmos db-példányokhoz kapcsolódó hálózati erőforrások változásainak észlelésére. Hozzon létre riasztásokat Azure Monitoron belül, amelyek akkor lépnek életbe, amikor a kritikus hálózati erőforrásokra vonatkozó módosításokat végrehajtják 

Az Azure Activity log eseményeinek megtekintése és beolvasása: https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view

Riasztások létrehozása a Azure Monitorban: https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

## <a name="logging-and-monitoring"></a>Naplózás és monitorozás

*További információ [: Security Control: naplózás és figyelés](../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="21-use-approved-time-synchronization-sources"></a>2,1: a jóváhagyott idő-szinkronizálási források használata

**Útmutató**: a Microsoft fenntartja az Azure-erőforrásokhoz használt időforrást, például az Azure Cosmos db a naplókban található időbélyegek esetében.


**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Microsoft

### <a name="22-configure-central-security-log-management"></a>2,2: a központi biztonsági naplók felügyeletének konfigurálása

**Útmutató**: naplók beolvasása Azure monitor segítségével a Azure Cosmos db által generált biztonsági adatokat összesítve. A Azure Monitoron belül Log Analytics munkaterülettel kérdezheti le és végezheti el az elemzéseket, és használhat tárolási fiókokat a hosszú távú/archiválási tároláshoz. Alternatív megoldásként engedélyezheti és elvégezheti az Azure Sentinel vagy egy harmadik féltől származó biztonsági incidensek és események felügyeletét (SIEM). 

Diagnosztikai naplók engedélyezése Azure Cosmos DBhoz: https://docs.microsoft.com/azure/cosmos-db/logging

Az Azure Sentinel előkészítése: https://docs.microsoft.com/azure/sentinel/quickstart-onboard

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="23-enable-audit-logging-for-azure-resources"></a>2,3: az Azure-erőforrások naplózásának engedélyezése

**Útmutató**: engedélyezze a Azure Cosmos db diagnosztikai beállításait, és küldje el a naplókat egy log Analytics munkaterületre vagy egy Storage-fiókba. A Azure Cosmos DB diagnosztikai beállításai az erőforrás-naplók összegyűjtésére szolgálnak. Ezeket a naplókat a rendszer kérelem szerint rögzíti, és az úgynevezett "adatsíkok naplói" néven is ismert. Néhány példa az adatsík-műveletekre: törlés, Beszúrás és olvasás. Emellett engedélyezheti az Azure-műveletnapló diagnosztikai beállításait, és elküldheti azokat ugyanarra a Log Analytics munkaterületre.

A Azure Cosmos DB diagnosztikai beállításainak engedélyezése: https://docs.microsoft.com/azure/cosmos-db/logging

Az Azure-beli tevékenység naplójának diagnosztikai beállításainak engedélyezése: https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings-legacy

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="24-collect-security-logs-from-operating-systems"></a>2,4: biztonsági naplók gyűjtése az operációs rendszerekből

**Útmutató**: nem alkalmazható; Ez a javaslat számítási erőforrások számára készült.

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: nem alkalmazható

### <a name="25-configure-security-log-storage-retention"></a>2,5: a biztonsági napló tárolási adatmegőrzésének konfigurálása

**Útmutató**: a Azure monitor a szervezet megfelelőségi előírásai alapján állítsa be a Azure Cosmos db-példányokhoz társított log Analytics-munkaterületek naplózásának megőrzési időtartamát.

Napló-megőrzési paraméterek beállítása: https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="26-monitor-and-review-logs"></a>2,6: naplók figyelése és áttekintése

**Útmutató**: lekérdezéseket végezhet log Analytics egy munkaterületen a kifejezések kereséséhez, a trendek azonosításához, a minták elemzéséhez és számos más elemzéshez az összegyűjtött Azure Cosmos db naplók alapján.

Azure Cosmos DB lekérdezések végrehajtása Log Analytics-munkaterületeken: https://docs.microsoft.com/azure/cosmos-db/monitor-cosmos-db

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="27-enable-alerts-for-anomalous-activity"></a>2,7: riasztások engedélyezése rendellenes tevékenységhez

**Útmutató**: a Azure Security Centerban engedélyezze a komplex veszélyforrások elleni védelmet Azure Cosmos db a rendellenes tevékenységek figyeléséhez a biztonsági naplókban és eseményekben. A Azure Cosmos DB diagnosztikai beállításainak engedélyezése és a naplók elküldése egy Log Analytics munkaterületre.

 

A Log Analytics-munkaterületet az Azure Sentinelbe is felkészítheti, mivel ez egy biztonsági előkészítési automatizált választ (felszárnyaló) megoldást biztosít. Ez lehetővé teszi a forgatókönyvek (automatizált megoldások) létrehozását és a biztonsági problémák megoldására való felhasználását. Emellett Azure Monitor használatával is létrehozhat egyéni napló-riasztásokat a Log Analytics munkaterületen.

A veszélyforrások elleni védelmi riasztások listája Azure Cosmos DB esetén: https://docs.microsoft.com/azure/security-center/alerts-reference#alerts-azurecosmos

Az Azure Sentinel előkészítése: https://docs.microsoft.com/azure/sentinel/quickstart-onboard

Naplózási riasztások létrehozása, megtekintése és kezelése Azure Monitor használatával: https://docs.microsoft.com/azure/azure-monitor/platform/alerts-log

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="28-centralize-anti-malware-logging"></a>2,8: kártevő szoftverek közötti naplózás központosítása

**Útmutató**: nem alkalmazható; Azure Cosmos DB nem dolgoz fel kártevő szoftverrel kapcsolatos naplókat, illetve nem hoz létre.


**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: nem alkalmazható

### <a name="29-enable-dns-query-logging"></a>2,9: DNS-lekérdezések naplózásának engedélyezése

**Útmutató**: nem alkalmazható; A Azure Cosmos DB nem dolgozza fel a DNS-sel kapcsolatos naplókat, és nem hoz létre.


**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: nem alkalmazható

### <a name="210-enable-command-line-audit-logging"></a>2,10: parancssori naplózás engedélyezése

**Útmutató**: nem alkalmazható; Ez a javaslat számítási erőforrások számára készült.

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: nem alkalmazható

## <a name="identity-and-access-control"></a>Identitás- és hozzáférés-vezérlés

*További információ [: Security Control (identitás és Access Control](../security/benchmarks/security-control-identity-access-control.md)).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3,1: a felügyeleti fiókok leltárának karbantartása

**Útmutató**: a Azure Portal identitás és hozzáférés-vezérlés (iam) paneljén konfigurálhatja az Azure szerepköralapú hozzáférés-vezérlést (Azure RBAC), és megtarthatja a leltárt Azure Cosmos db erőforrásokon. A szerepköröket a rendszer a felhasználókra, csoportokra, egyszerű szolgáltatásokra és felügyelt identitásokra alkalmazza Active Directoryban. A felhasználók és csoportok számára beépített szerepköröket vagy egyéni szerepköröket is használhat.

A Azure Cosmos DB beépített Azure-RBAC biztosít a gyakori felügyeleti forgatókönyvekhez Azure Cosmos DBban. A Azure Active Directory (AD) profillal rendelkező személy ezeket az Azure-szerepköröket hozzárendelheti a felhasználókhoz, csoportokhoz, egyszerű szolgáltatásokhoz vagy felügyelt identitásokhoz, így biztosíthatja vagy megtagadhatja a hozzáférést az erőforrásokhoz és műveletekhez Azure Cosmos DB erőforrásokon.

Az Azure AD PowerShell-modullal is elvégezheti az alkalmi lekérdezéseket a felügyeleti csoportok tagjait futtató fiókok felderítéséhez. 

Emellett a Azure Cosmos DB egyes műveletei Azure Active Directory és fiók-specifikus elsődleges kulcsokkal is vezérelhetők.  A "disableKeyBasedMetadataWriteAccess" fiók beállításával vezérelheti a kulcsokhoz való hozzáférést.

Az Azure szerepköralapú hozzáférés-vezérlésének megismerése Azure Cosmos DBban: https://docs.microsoft.com/azure/cosmos-db/role-based-access-control

Saját egyéni szerepkörök létrehozása Azure Cosmos DB műveletek használatával (Microsoft.DocumentDB névtér): https://docs.microsoft.com/azure/role-based-access-control/resource-provider-operations#microsoftdocumentdb

Új szerepkör létrehozása a Azure Active Directoryban: https://docs.microsoft.com/azure/role-based-access-control/custom-roles

Címtárbeli szerepkör beszerzése Azure Active Directory a PowerShell használatával: https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0&preserve-view=true

Címtárbeli szerepkör tagjainak beszerzése Azure Active Directory a PowerShell-lel: https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0&preserve-view=true

Csak az adatműveletek felhasználói hozzáférésének korlátozása: https://docs.microsoft.com/azure/cosmos-db/how-to-restrict-user-data

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="32-change-default-passwords-where-applicable"></a>3,2: az alapértelmezett jelszavak módosítása, ha alkalmazható

**Útmutató**: az alapértelmezett vagy az üres jelszavak fogalma nem létezik az Azure ad-vel vagy a Azure Cosmos DBtel kapcsolatban. Ehelyett Azure Cosmos DB két típusú kulcsot használ a felhasználók hitelesítéséhez és az adataihoz és erőforrásaihoz való hozzáférés biztosításához. elsődleges kulcsok és erőforrás-tokenek. A kulcsok bármikor újra létrehozhatók.

A Azure Cosmos DB lévő adathozzáférés ismertetése: https://docs.microsoft.com/azure/cosmos-db/secure-access-to-data

Azure Cosmos DB kulcsok újragenerálása: https://docs.microsoft.com/azure/cosmos-db/manage-with-powershell#regenerate-keys

Kulcsok programozott elérésének módja a Azure Active Directory használatával: https://docs.microsoft.com/azure/cosmos-db/certificate-based-authentication

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Megosztott

### <a name="33-use-dedicated-administrative-accounts"></a>3,3: dedikált rendszergazdai fiókok használata

**Útmutató**: nem alkalmazható; A Azure Cosmos DB nem támogatja a rendszergazdai fiókokat.  Minden hozzáférés integrálva van Azure Active Directory és Azure szerepköralapú hozzáférés-vezérléssel (Azure RBAC).



**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3,4: egyszeri bejelentkezés (SSO) használata Azure Active Directory

**Útmutató**: a Azure Cosmos db kétféle kulcsot használ a felhasználók engedélyezéséhez, és az adatsík szintjén nem támogatja az egyszeri Sign-Ont (SSO). A Cosmos DB vezérlési síkja elérhető a REST APIon keresztül, és támogatja az egyszeri bejelentkezést. A hitelesítéshez állítsa be a kérések engedélyezési fejlécét egy Azure Active Directoryból beszerzett JSON Web Tokenra.

Az Azure Database Cosmos DB REST API ismertetése: https://docs.microsoft.com/rest/api/cosmos-db/

Az egyszeri bejelentkezés ismertetése Azure Active Directory: https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3,5: Multi-Factor Authentication használata az összes Azure Active Directory-alapú hozzáféréshez

**Útmutató**: a Azure Active Directory multi-Factor Authentication engedélyezése és a Azure Security Center identitás-és hozzáférés-kezelési javaslatok követése.

Az MFA engedélyezése az Azure-ban: https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

Identitás és hozzáférés figyelése Azure Security Centeron belül: https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3,6: dedikált gépek (privilegizált hozzáférési munkaállomások) használata az összes felügyeleti feladathoz

**Útmutató**: az Azure-erőforrások bejelentkezéséhez és konfigurálásához multi-Factor Authentication konfigurált emelt szintű hozzáférésű munkaállomások (Paw) használata.

További tudnivalók az emelt szintű hozzáférésű munkaállomásokról: https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/

Az MFA engedélyezése az Azure-ban: https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="37-log-and-alert-on-suspicious-activity-from-administrative-accounts"></a>3,7: naplózás és riasztás a gyanús tevékenységekről a rendszergazdai fiókoktól

**Útmutató**: a Azure Cosmos db komplex veszélyforrások elleni védelem (ATP) használata. Az ATP for Azure Cosmos DB egy további biztonsági intelligenciát biztosít, amely szokatlan és potenciálisan ártalmas kísérleteket észlel az Azure Cosmos-fiókok eléréséhez vagy kiaknázásához. Ez a védelmi réteg lehetővé teszi a fenyegetések kezelésére és a központi biztonsági figyelő rendszerekkel való integrálását. 

Emellett Azure Active Directory (AD) Privileged Identity Management (PIM) is használható a naplók és riasztások generálásához, ha a környezetben gyanús vagy nem biztonságos tevékenység történik.

Az Azure AD-kockázati észlelések használatával a kockázatos felhasználói viselkedésre vonatkozó riasztásokat és jelentéseket tekinthet meg.

Privileged Identity Management (PIM) üzembe helyezése: https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-deployment-plan

Az Azure AD kockázati észlelések ismertetése: https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risk-events

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3,8: az Azure-erőforrások kezelése csak jóváhagyott helyekről

**Útmutató**: a feltételes hozzáférési szabályzat tárolási állapotának konfigurálása és az elnevezett helyek kezelése. Az elnevezett helyszínekkel logikai csoportosításokat hozhat létre az IP-címtartományok vagy országok és régiók számára. Az érzékeny erőforrásokhoz, például a Azure Cosmos DB példányokhoz való hozzáférést a konfigurált elnevezett helyekre korlátozhatja.

Elnevezett helyszínek konfigurálása az Azure-ban: https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="39-use-azure-active-directory"></a>3,9: a Azure Active Directory használata

**Útmutató**: a Azure Active Directory (ad) használata központi hitelesítési és engedélyezési rendszerrel. Az Azure AD az adatok védelme érdekében erős titkosítást használ a nyugalmi és a továbbítási adatokhoz. Az Azure AD emellett a felhasználó hitelesítő adatainak a sók, a kivonatok és a biztonságos tárolását is tartalmazza.

Azure Active Directory példány létrehozása és konfigurálása: https://docs.microsoft.com/azure/active-directory-domain-services/tutorial-create-instance

Azure Active Directory hitelesítés konfigurálása és kezelése az Azure SQL használatával: https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication-configure

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="310-regularly-review-and-reconcile-user-access"></a>3,10: a felhasználói hozzáférés rendszeres áttekintése és egyeztetése

**Útmutató**: a Azure Active Directory naplókat biztosít az elavult fiókok felderítéséhez. Emellett az Azure Identity Access Reviews használatával hatékonyan kezelheti a csoporttagságok kezelését, a vállalati alkalmazásokhoz való hozzáférést és a szerepkör-hozzárendeléseket is. A felhasználó hozzáférését rendszeresen felül lehet vizsgálni, hogy csak a megfelelő felhasználók férhessenek hozzájuk.

Az Azure Identity hozzáférési felülvizsgálatok használata: https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="311-monitor-attempts-to-access-deactivated-accounts"></a>3,11: az inaktivált fiókok elérésére irányuló kísérletek figyelése

**Útmutató**: diagnosztikai beállításokat hozhat létre Azure Active Directory felhasználói fiókokhoz, elküldheti a naplókat és bejelentkezési naplókat egy log Analytics munkaterületre, ahol konfigurálhatja a kívánt riasztásokat.

Azure-beli tevékenység-naplók integrálása a Azure Monitorba: https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="312-alert-on-account-login-behavior-deviation"></a>3,12: riasztás a fiók bejelentkezési viselkedésének eltérése esetén

**Útmutató**: a Azure Cosmos db komplex veszélyforrások elleni védelem (ATP) használata. Az ATP for Azure Cosmos DB egy további biztonsági intelligenciát biztosít, amely szokatlan és potenciálisan ártalmas kísérleteket észlel az Azure Cosmos-fiókok eléréséhez vagy kiaknázásához. Ez a védelmi réteg lehetővé teszi a fenyegetések kezelésére és a központi biztonsági figyelő rendszerekkel való integrálását. 

Azure AD Identity Protection-és kockázati észlelési funkcióval is konfigurálhatja a felhasználói identitásokkal kapcsolatos gyanús műveletekre vonatkozó automatizált válaszokat. Emellett további vizsgálat céljából betöltheti a naplókat az Azure Sentinelbe.

Azure Active Directory kockázatos bejelentkezések megtekintése: https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risky-sign-ins

Az Identity Protection kockázati házirendjeinek konfigurálása és engedélyezése: https://docs.microsoft.com/azure/active-directory/identity-protection/howto-identity-protection-configure-risk-policies

Az Azure Sentinel előkészítése: https://docs.microsoft.com/azure/sentinel/quickstart-onboard

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3,13: a Microsoft számára elérhetővé teszi a megfelelő ügyféladatokat a támogatási forgatókönyvek során

**Útmutató**: a Ügyfélszéf Cosmos db esetében nem alkalmazható.  A Microsoft alkalmazottai nem férhetnek hozzá az adaterőforrásokhoz, még az ügyfél engedélyeivel sem.

Ügyfélszéf támogatott szolgáltatások listája: https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview#supported-services-and-scenarios-in-general-availability

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: nem alkalmazható

## <a name="data-protection"></a>Adatvédelem

*További információkért lásd [: biztonsági ellenőrzés:](../security/benchmarks/security-control-data-protection.md)adatvédelem.*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4,1: bizalmas információk leltárának fenntartása

**Útmutató**: a címkék használatával segítheti a bizalmas adatokat tároló vagy feldolgozó Azure Cosmos db-példányok nyomon követését.

Címkék létrehozása és használata: https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4,2: bizalmas adatok tárolására vagy feldolgozására szolgáló rendszerek elkülönítése

**Útmutató**: különálló előfizetések és/vagy felügyeleti csoportok megvalósítása fejlesztési, tesztelési és éles környezetekhez. Azure Cosmos DB példányokat a virtuális hálózat/alhálózat választja el, megfelelően címkézve, és egy hálózati biztonsági csoporton (NSG) vagy Azure Firewalln belül biztonságossá tettük. A bizalmas adatokat tároló Azure Cosmos DB példányokat el kell különíteni. Az Azure Private link használatával privát végponton keresztül kapcsolódhat egy Azure Cosmos DB-példány fiókjához. A magánhálózati végpont a virtuális hálózaton belüli alhálózat magánhálózati IP-címeinek halmaza. Ezután korlátozhatja a hozzáférést a kiválasztott magánhálózati IP-címekhez. 

További Azure-előfizetések létrehozása: https://docs.microsoft.com/azure/billing/billing-create-subscription

Felügyeleti csoportok létrehozása: https://docs.microsoft.com/azure/governance/management-groups/create

Címkék létrehozása és használata: https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

Privát végpont konfigurálása Azure Cosmos DBhoz: https://docs.microsoft.com/azure/cosmos-db/how-to-configure-private-endpoints

Hálózati biztonsági csoport létrehozása biztonsági konfigurációval: https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4,3: a bizalmas adatok jogosulatlan átvitelének figyelése és letiltása

**Útmutató**: a Azure Cosmos db komplex veszélyforrások elleni védelmét is felhasználhatja, amely észleli az adatbázisok eléréséhez vagy kiaknázásához szükséges szokatlan és vélhetően ártalmas tevékenységeket. Jelenleg a következő riasztásokat indítja el:

- Hozzáférés szokatlan helyekről

- Szokatlan adatbányászat

Emellett, ha virtuális gépeket használ a Azure Cosmos DB példányaihoz való hozzáféréshez, használja a privát hivatkozásokat, a tűzfalat, a hálózati biztonsági csoportokat és a szolgáltatási címkéket az adatkiszűrése lehetőségének enyhítése érdekében. A Microsoft kezeli a Azure Cosmos DB alapjául szolgáló infrastruktúrát, és szigorú ellenőrzéseket vezetett be az ügyféladatok elvesztésének vagy kihatásának megelőzésére.

Cosmos DB komplex veszélyforrások elleni védelem konfigurálása: https://docs.microsoft.com/azure/cosmos-db/cosmos-db-advanced-threat-protection

Az ügyfelek adatvédelem az Azure-ban: https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Megosztott

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4,4: minden bizalmas adat titkosítása az átvitel során

**Útmutató**: a Azure Cosmos db összes kapcsolata HTTPS-támogatással. A Azure Cosmos DB a TLS 1.2-es verzióját is támogatja. Egy minimális TLS-verzió kiszolgálóoldali kikényszeríthető. Ehhez vegye fel a kapcsolatot [azurecosmosdbtls@service.microsoft.com](mailto:azurecosmosdbtls@service.microsoft.com) .

A Cosmos DB biztonság áttekintése: https://docs.microsoft.com/azure/cosmos-db/database-security

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Microsoft

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4,5: aktív felderítési eszköz használata a bizalmas adatok azonosítására

**Útmutató**: az automatikus adatazonosítási, besorolási és veszteség-megelőzési funkciók még nem érhetők el Azure Cosmos db számára. A besorolási és adatelemzési szolgáltatáshoz azonban használhatja az Azure Cognitive Search integrációját. Egy külső gyártótól származó megoldást is megvalósíthat, ha az a megfelelőség szempontjából szükséges.

A Microsoft által felügyelt mögöttes platform esetében a Microsoft az összes vásárlói tartalmat bizalmasként kezeli, és az ügyfelek adatvesztésével és a kitettséggel szembeni védelem érdekében nagy hosszúságú. Annak biztosítása érdekében, hogy az Azure-beli ügyféladatok biztonságban maradjanak, a Microsoft végrehajtotta és karbantartja a robusztus adatvédelmi szabályozást és képességeket.

Azure Cosmos DB-adatindexek indexelése az Azure Cognitive Search: https://docs.microsoft.com/azure/search/search-howto-index-cosmosdb?toc=/azure/cosmos-db/toc.json&amp ; BC =/azure/cosmos-db/breadcrumb/toc.jsbe

Az ügyfelek adatvédelem az Azure-ban: https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Megosztott

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4,6: az erőforrásokhoz való hozzáférés szabályozása az Azure RBAC

**Útmutató**: a Azure Cosmos db beépített Azure szerepköralapú hozzáférés-vezérlést (Azure RBAC) biztosít a Azure Cosmos db gyakori felügyeleti eseteihez. A Azure Active Directory profillal rendelkező személy a felhasználókhoz, csoportokhoz, egyszerű szolgáltatásokhoz vagy felügyelt identitásokhoz rendelheti hozzá ezeket az Azure-szerepköröket az erőforrásokhoz és műveletekhez való hozzáférés engedélyezéséhez vagy megtagadásához Azure Cosmos DB erőforrásokon. A szerepkör-hozzárendelések hatóköre csak a csak vezérlőre vonatkozik, amely hozzáférést biztosít az Azure Cosmos-fiókok,-adatbázisok,-tárolók és-ajánlatok (átviteli sebesség) számára.

Az Azure RBAC megvalósítása Azure Cosmos DBban: https://docs.microsoft.com/azure/cosmos-db/role-based-access-control

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4,7: a gazdagép-alapú adatvesztés-megelőzés használata a hozzáférés-vezérlés kikényszeríthető

**Útmutató**: nem alkalmazható; Ez az útmutató számítási erőforrások számára készült.

A Microsoft kezeli a Cosmos DB alapjául szolgáló infrastruktúrát, és szigorú ellenőrzéseket vezetett be az ügyféladatok elvesztésének vagy kihatásának megelőzésére.

Az ügyfelek adatvédelem az Azure-ban: https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: nem alkalmazható

### <a name="48-encrypt-sensitive-information-at-rest"></a>4,8: bizalmas adatok titkosítása a nyugalmi állapotban

**Útmutató**: a Cosmos DBban tárolt összes felhasználói adatok alapértelmezés szerint titkosítva maradnak. Nincsenek kikapcsolható vezérlők. A Azure Cosmos DB AES-256 titkosítást használ minden olyan régióban, ahol a fiók fut.

Alapértelmezés szerint a Microsoft kezeli az Azure Cosmos-fiókban tárolt adattitkosításhoz használt kulcsokat. Lehetőség van arra is, hogy egy második titkosítási réteget adjon hozzá a saját kulcsaihoz.

A titkosítás inaktívként való megismerése Azure Cosmos DB: https://docs.microsoft.com/azure/cosmos-db/database-encryption-at-rest

A Azure Cosmos DB-ben való titkosítással kapcsolatos legfontosabb felügyelet ismertetése: https://docs.microsoft.com/azure/cosmos-db/cosmos-db-security-controls

Az ügyfél által felügyelt kulcsok konfigurálása a Azure Cosmos DB-fiókhoz: https://docs.microsoft.com/azure/cosmos-db/how-to-setup-cmk

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Megosztott

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4,9: a kritikus Azure-erőforrások változásainak naplózása és riasztása

**Útmutató**: a Azure monitor és az Azure-tevékenység naplójának használata riasztások létrehozásához, amikor a módosítások a Azure Cosmos db éles példányain lépnek életbe.

Riasztások létrehozása az Azure Activity log-eseményekhez: https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log

Riasztások létrehozása az Azure Activity log-eseményekhez: https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

## <a name="vulnerability-management"></a>Biztonságirés-kezelés

*További információ [: Security Control: sebezhetőségi kezelés](../security/benchmarks/security-control-vulnerability-management.md).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5,1: automatikus biztonsági rések vizsgálatára szolgáló eszközök futtatása

**Útmutató**: a Azure Cosmos db példányok Azure Security Center ajánlásainak követése. 

A Microsoft a Azure Cosmos DB példányokat támogató mögöttes gazdagépeken hajtja végre a rendszerjavítást és a biztonsági rések kezelését. Annak biztosítása érdekében, hogy az Azure-beli ügyféladatok biztonságban maradjanak, a Microsoft végrehajtotta és karbantartja a robusztus adatvédelmi szabályozást és képességeket.

A Azure Security Centerban elérhető támogatott funkciók: https://docs.microsoft.com/azure/security-center/security-center-services?tabs=features-windows

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Megosztott

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5,2: az operációs rendszer automatikus javításának felügyeleti megoldásának telepítése

**Útmutató**: nem alkalmazható; Ez az útmutató számítási erőforrások számára készült.


**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: nem alkalmazható

### <a name="53-deploy-automated-third-party-software-patch-management-solution"></a>5,3: a harmadik féltől származó szoftveres javításokat kezelő megoldás telepítése

**Útmutató**: nem alkalmazható; Ez az útmutató számítási erőforrások számára készült.


**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: nem alkalmazható

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5,4: a biztonsági rések keresésének összehasonlítása

**Útmutató**: nem alkalmazható; Ez az útmutató számítási erőforrások számára készült.


**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: nem alkalmazható

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5,5: kockázatértékelési folyamat használatával rangsorolhatja a felderített biztonsági rések szervizelését

**Útmutató**: nem alkalmazható; Ez az útmutató számítási erőforrások számára készült.


**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: nem alkalmazható

## <a name="inventory-and-asset-management"></a>Leltár-és eszközfelügyelet

*További információkért lásd [: biztonsági vezérlés: leltár és eszközkezelés](../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-azure-asset-discovery"></a>6,1: az Azure Asset Discovery használata

**Útmutató**: az Azure Portal vagy az Azure Resource Graph használatával felderítheti az összes erőforrást (nem csak a Azure Cosmos DBra, hanem a számítási, egyéb tárolási, hálózati, portok és protokollok stb.) az előfizetése (ke) n belül.  Győződjön meg arról, hogy rendelkezik megfelelő engedélyekkel a bérlőben, és képes az összes Azure-előfizetés, valamint az előfizetésében lévő erőforrások számbavételére.

Bár a klasszikus Azure-erőforrások felderítése az erőforrás-gráfon keresztül lehetséges, erősen ajánlott a Azure Resource Manager erőforrások létrehozása és használata.

Lekérdezések létrehozása az Azure Resource Graph használatával: https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

Az Azure-előfizetések megtekintése: https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0&preserve-view=true

Az Azure szerepköralapú hozzáférés-vezérlésének ismertetése: https://docs.microsoft.com/azure/role-based-access-control/overview

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="62-maintain-asset-metadata"></a>6,2: az eszköz metaadatainak fenntartása

**Útmutató**: címkéket alkalmazhat a Azure Cosmos db példányaira és a kapcsolódó erőforrásokra metaadatokkal, hogy logikailag szervezze őket a besorolásba.

Címkék létrehozása és használata: https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

Mely Azure Cosmos DB erőforrások támogatják a címkéket: https://docs.microsoft.com/azure/azure-resource-manager/management/tag-support#microsoftdocumentdb

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="63-delete-unauthorized-azure-resources"></a>6,3: jogosulatlan Azure-erőforrások törlése

**Útmutató**: a címkézés, a felügyeleti csoportok és az elkülönített előfizetések használata, ha szükséges, az eszközök rendszerezéséhez és nyomon követéséhez, beleértve a Azure Cosmos db erőforrásokat is. Rendszeres időközönként egyeztetheti a leltárt, és gondoskodhat arról, hogy a jogosulatlan erőforrások törlése az előfizetésből időben történjen.

További Azure-előfizetések létrehozása: https://docs.microsoft.com/azure/billing/billing-create-subscription

Management Groups létrehozása: https://docs.microsoft.com/azure/governance/management-groups/create

Címkék létrehozása és használata: https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="64-maintain-an-inventory-of-approved-azure-resources-and-software-titles"></a>6,4: a jóváhagyott Azure-erőforrások és-szoftverek leltárának fenntartása

**Útmutató**: nem alkalmazható; Ez az útmutató számítási erőforrásokhoz és az Azure egészéhez készült.

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: nem alkalmazható

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

**Útmutató**: nem alkalmazható; Ez az alapterv számítási erőforrások számára készült.


**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: nem alkalmazható

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6,7: nem jóváhagyott Azure-erőforrások és szoftveralkalmazások eltávolítása

**Útmutató**: nem alkalmazható; Ez az útmutató számítási erőforrásokhoz és az Azure egészéhez készült.


**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: nem alkalmazható

### <a name="68-use-only-approved-applications"></a>6,8: csak jóváhagyott alkalmazások használata

**Útmutató**: nem alkalmazható; Ez az útmutató számítási erőforrások számára készült.


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

**Útmutató**: nem alkalmazható; Ez az útmutató számítási erőforrások számára készült.


**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: nem alkalmazható

### <a name="611-limit-users-ability-to-interact-with-azureresources-manager-via-scripts"></a>6,11: korlátozza a felhasználók képességét a AzureResources-kezelővel való interakcióra parancsfájlok használatával

**Útmutató**: az Azure feltételes hozzáférés használatával korlátozhatja, hogy a felhasználók képesek legyenek a Azure Resource Manager interakcióra az "Microsoft Azure felügyelet" alkalmazás "hozzáférés tiltása" beállításával. Ez megakadályozhatja az erőforrások létrehozását és módosítását a magas biztonsági környezeten belül.

A feltételes hozzáférés konfigurálása a Azure Resource Managerhoz való hozzáférés blokkolásához: https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6,12: korlátozza a felhasználók számára a parancsfájlok végrehajtásának lehetőségét a számítási erőforrásokon belül

**Útmutató**: nem alkalmazható; Ez az útmutató számítási erőforrások számára készült.


**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: nem alkalmazható

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6,13: fizikailag vagy logikailag elkülöníthető a nagy kockázatú alkalmazások

**Útmutató**: nem alkalmazható; Ez az útmutató Azure App Service vagy számítási erőforrásokon futó webalkalmazásokhoz készült.


**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: nem alkalmazható

## <a name="secure-configuration"></a>Biztonságos konfiguráció

*További információkért lásd [: biztonság-vezérlés: biztonságos konfiguráció](../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7,1: biztonságos konfigurációk létrehozása az összes Azure-erőforráshoz

**Útmutató**: a Cosmos db példányok szabványos biztonsági konfigurációinak meghatározása és implementálása Azure Policy használatával. Használjon Azure Policy aliasokat a "Microsoft.DocumentDB" névtérben egyéni szabályzatok létrehozásához az Cosmos DB példányok konfigurációjának naplózásához vagy érvényesítéséhez. Használhatja a Azure Cosmos DB beépített szabályzat-definícióját is, például:

- A Cosmos DB-fiókok komplex veszélyforrások elleni védelmének üzembe helyezése

- Cosmos DB virtuális hálózati szolgáltatás végpontját kell használnia

Az elérhető Azure Policy aliasok megtekintése: https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0&preserve-view=true

Azure Policy konfigurálása és kezelése: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="72-establish-secure-operating-system-configurations"></a>7,2: biztonságos operációsrendszer-konfigurációk létrehozása

**Útmutató**: nem alkalmazható; Ez az útmutató számítási erőforrások számára készült.


**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: nem alkalmazható

### <a name="73-maintain-secure-azure-resource-configurations"></a>7,3: biztonságos Azure-erőforrás-konfigurációk karbantartása

**Útmutató**: az Azure-erőforrások biztonságos beállításainak betartatásához használja a Azure Policy [deny] és a [telepítés ha nem létezik] lehetőséget.

Azure Policy konfigurálása és kezelése: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Azure Policy effektusok ismertetése: https://docs.microsoft.com/azure/governance/policy/concepts/effects

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="74-maintain-secure-operating-system-configurations"></a>7,4: az operációs rendszer biztonságos konfigurációjának fenntartása

**Útmutató**: nem alkalmazható; Ez az útmutató számítási erőforrások számára készült.


**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: nem alkalmazható

### <a name="75-securely-store-configuration-of-azure-resources"></a>7,5: az Azure-erőforrások biztonságos tárolása

**Útmutató**: ha a Cosmos DBhoz vagy a kapcsolódó erőforrásokhoz egyéni Azure Policy definíciókat használ, az Azure Repos használatával biztonságosan tárolhatja és kezelheti a kódot.

Az Azure Repos dokumentációja: https://docs.microsoft.com/azure/devops/repos/index?view=azure-devops&preserve-view=truehttps://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops&preserve-view=true

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="76-securely-store-custom-operating-system-images"></a>7,6: az egyéni operációsrendszer-lemezképek biztonságos tárolása

**Útmutató**: nem alkalmazható; Ez az útmutató számítási erőforrások számára készült.

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: nem alkalmazható

### <a name="77-deploy-system-configuration-management-tools"></a>7,7: rendszerkonfiguráció-felügyeleti eszközök telepítése

**Útmutató**: a "Microsoft.DocumentDB" névtérben található Azure Policy Aliasok használatával egyéni szabályzatokat hozhat létre a riasztáshoz, a naplózáshoz és a rendszerkonfigurációk kényszerítéséhez. Emellett dolgozzon ki egy folyamatot és egy folyamatot a házirend-kivételek kezeléséhez.

Azure Policy konfigurálása és kezelése: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7,8: rendszerkonfiguráció-felügyeleti eszközök központi telepítése operációs rendszerekhez

**Útmutató**: nem alkalmazható; Ez az útmutató számítási erőforrások számára készült.

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: nem alkalmazható

### <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7,9: az Azure-szolgáltatások automatizált konfigurációs figyelésének megvalósítása

**Útmutató**: a "Microsoft.DocumentDB" névtérben található Azure Policy Aliasok használatával egyéni szabályzatokat hozhat létre a riasztáshoz, a naplózáshoz és a rendszerkonfigurációk kényszerítéséhez. A Azure Cosmos DB-példányok és a kapcsolódó erőforrások konfigurációjának automatikus érvényesítéséhez használja a Azure Policy [audit], [megtagadás] és [üzembe helyezés ha nem létezik] lehetőséget. 

Azure Policy konfigurálása és kezelése: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7,10: az operációs rendszerek automatikus konfiguráció-figyelésének megvalósítása

**Útmutató**: nem alkalmazható; Ez az útmutató számítási erőforrások számára készült.

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: nem alkalmazható

### <a name="711-manage-azure-secrets-securely"></a>7,11: az Azure-titkok biztonságos kezelése

**Útmutató**: az Azure Cosmos db-példányok elérésére használt Azure app Serviceon futó Azure-beli virtuális gépek vagy webalkalmazások esetén a Managed Service Identity a Azure Key Vault együtt használva egyszerűsítheti és biztonságossá teheti Azure Cosmos db titkos felügyeletét. Győződjön meg arról, Key Vault a Soft delete engedélyezve van.

Integráció az Azure felügyelt identitásokkal: https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity

Key Vault létrehozása: https://docs.microsoft.com/azure/key-vault/general/quick-create-portal

A Key Vault hitelesítése: https://docs.microsoft.com/azure/key-vault/general/authentication

Key Vault hozzáférési szabályzatok kiosztása: https://docs.microsoft.com/azure/key-vault/general/assign-access-policy-portal

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="712-manage-identities-securely-and-automatically"></a>7,12: az identitások biztonságos és automatikus kezelése

**Útmutató**: az Azure Cosmos db-példányok elérésére használt Azure app Serviceon futó Azure-beli virtuális gépek vagy webalkalmazások esetén a Managed Service Identity a Azure Key Vault együtt használva egyszerűsítheti és biztonságossá teheti Azure Cosmos db titkos felügyeletét.

Felügyelt identitások használatával biztosíthatja az Azure-szolgáltatások automatikus felügyelt identitását Azure Active Directoryban (AD). A felügyelt identitások lehetővé teszik bármely olyan szolgáltatás hitelesítését, amely támogatja az Azure AD-hitelesítést, beleértve a Key Vault is, a kódban szereplő hitelesítő adatok nélkül.

Felügyelt identitások konfigurálása: https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm

Integráció az Azure felügyelt identitásokkal: https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="713-eliminate-unintended-credential-exposure"></a>7,13: a hitelesítő adatok nem szándékolt expozíciójának megszüntetése

**Útmutató**: hitelesítő adatok beolvasása a programkódon belül a hitelesítő adatok azonosításához. A Credential Scanner a felfedezett hitelesítő adatok biztonságosabb helyre, például az Azure Key Vaultba való áthelyezésére is javaslatot tesz.

Hitelesítőadat-képolvasó beállítása: https://secdevtools.azurewebsites.net/helpcredscan.html

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

## <a name="malware-defense"></a>Kártevők elleni védelem

*További információkért lásd [: biztonsági ellenőrzés: kártevők elleni védelem](../security/benchmarks/security-control-malware-defense.md).*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8,1: központilag felügyelt kártevő szoftverek használata

**Útmutató**: nem alkalmazható; Ez az útmutató számítási erőforrások számára készült. A Microsoft antimalware engedélyezve van a mögöttes gazdagépen, amely támogatja az Azure-szolgáltatásokat (például Azure App Service), azonban nem az ügyfél tartalmán fut.


**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: nem alkalmazható

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8,2: a nem számítási Azure-erőforrásokra feltöltött fájlok előzetes vizsgálata

**Útmutató**: a Microsoft antimalware engedélyezve van a mögöttes gazdagépen, amely támogatja az Azure-szolgáltatásokat (például Azure app Service), azonban nem az ügyfél tartalmán fut.

Az Ön felelőssége a nem számítási Azure-erőforrásokra feltöltött fájlok előzetes vizsgálata, beleértve a Azure Cosmos DBt is. A Microsoft nem fér hozzá az ügyféladatok eléréséhez, így az Ön nevében nem végezhet kártevő-ellenőrzéseket az ügyfél-tartalmakon.


**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8,3: a kártevő szoftverek és az aláírások frissítésének ellenőrzése

**Útmutató**: nem alkalmazható; a teljesítményteszt a számítási erőforrások számára készült. A Microsoft antimalware engedélyezve van a mögöttes gazdagépen, amely támogatja az Azure-szolgáltatásokat, de nem fut az ügyfél tartalmán.


**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: nem alkalmazható

## <a name="data-recovery"></a>Adat-helyreállítás

*További információkért lásd [: biztonsági ellenőrzés: adat-helyreállítás](../security/benchmarks/security-control-data-recovery.md).*

### <a name="91-ensure-regular-automated-back-ups"></a>9,1: rendszeres automatizált biztonsági másolatok biztosítása

**Útmutató**: Azure Cosmos db négy óránként pillanatképeket küld az adatairól. Az összes biztonsági mentés külön tárolódik a tárolási szolgáltatásokban, és ezek a biztonsági másolatok globálisan replikálódnak a regionális katasztrófák elleni rugalmasságra. Egy adott időpontban csak az utolsó két pillanatképet őrzi meg a rendszer. Tároló vagy adatbázis törlése esetén azonban az Azure Cosmos DB 30 napig megőrzi az adott tároló vagy adatbázis meglévő pillanatképeit. A biztonsági másolatból való visszaállításhoz forduljon az Azure támogatási szolgálatához.

Azure Cosmos DB automatikus biztonsági mentések ismertetése: https://docs.microsoft.com/azure/cosmos-db/online-backup-and-restore

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Microsoft

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: teljes rendszerbiztonsági mentés és biztonsági másolat készítése bármely ügyfél által felügyelt kulcsról

**Útmutató**: a Azure Cosmos db rendszeres időközönként automatikusan biztonsági másolatot készít az adatairól. Ha a rendszer törli az adatbázist vagy a tárolót, egy támogatási jegyet is betölthet, vagy meghívja az Azure-támogatást az adatok automatikus online biztonsági mentésből való visszaállításához. Az Azure-támogatás csak olyan kiválasztott csomagokhoz érhető el, mint a standard, a fejlesztői és a magasabb szintű csomagok. A biztonsági mentés egy adott pillanatképének visszaállításához a Azure Cosmos DB megköveteli, hogy az adatok elérhetők legyenek a pillanatkép biztonsági mentési ciklusának időtartamára. 

Ha a Cosmos DB-példányok hitelesítő adatainak tárolására Key Vault használ, ügyeljen arra, hogy a kulcsok rendszeres automatikus biztonsági mentéseit tárolja.

Azure Cosmos DB automatikus biztonsági mentések ismertetése: https://docs.microsoft.com/azure/cosmos-db/online-backup-and-restore

Az Azure Cosmos DB-ban tárolt adatvisszaállítások: https://docs.microsoft.com/azure/cosmos-db/how-to-backup-and-restore

Key Vault kulcsok biztonsági mentése: https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Megosztott

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: az összes biztonsági másolat ellenőrzése, beleértve az ügyfél által felügyelt kulcsokat

**Útmutató**: Ha a rendszer törli az adatbázist vagy a tárolót, egy támogatási jegyet is megadhat, vagy az Azure támogatási szolgálatával visszaállíthatja az adatokat az automatikus online biztonsági mentésből. Az Azure-támogatás csak olyan kiválasztott csomagokhoz érhető el, mint a standard, a fejlesztői és a magasabb szintű csomagok. A biztonsági mentés egy adott pillanatképének visszaállításához a Azure Cosmos DB megköveteli, hogy az adatok elérhetők legyenek a pillanatkép biztonsági mentési ciklusának időtartamára.

A Azure Key Vaultban tárolt titkok visszaállításának tesztelése a PowerShell használatával. A Restore-AzureKeyVaultKey parancsmag egy kulcsot hoz létre a megadott kulcstartóban. Ez a kulcs a bemeneti fájlban lévő biztonsági másolati kulcs replikája, és az eredeti kulccsal megegyező névvel rendelkezik.

Azure Cosmos DB automatikus biztonsági mentések ismertetése:

https://docs.microsoft.com/azure/cosmos-db/online-backup-and-restore

Az Azure Cosmos DB-ban tárolt adatvisszaállítások:

https://docs.microsoft.com/azure/cosmos-db/how-to-backup-and-restore

Azure Key Vault titkos kódok visszaállítása:

https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0&preserve-view=true

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Megosztott

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: a biztonsági másolatok és az ügyfél által felügyelt kulcsok védelmének biztosítása

**Útmutató**: mivel a Cosmos DBban tárolt összes felhasználói adatok titkosítva maradnak a nyugalmi és a szállítási forgalomban, nem kell semmilyen műveletet végrehajtania. A másik lehetőség az, hogy a titkosítás nyugalmi állapotban van, alapértelmezés szerint "on". Nincsenek olyan vezérlők, amelyekkel ki-vagy bekapcsolhatja. A Azure Cosmos DB AES-256 titkosítást használ minden olyan régióban, ahol a fiók fut.

A kulcsok véletlen vagy rosszindulatú törléssel szembeni védelemmel való ellátásához engedélyezze a Key Vault Soft-Delete.

Az adattitkosítás ismertetése Azure Cosmos DBban: https://docs.microsoft.com/azure/cosmos-db/database-encryption-at-rest

Soft-Delete engedélyezése a Key Vaultban: https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Megosztott

## <a name="incident-response"></a>Incidensmegoldás

*További információ [: Security Control: incidens válasza](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10,1: incidens-válaszi útmutató létrehozása

**Útmutató**: Alakítson ki incidenskezelési útmutatót a vállalat számára. Gondoskodjon írásos incidenskezelési tervekről, amelyek definiálják az összes résztvevő szerepkörét, valamint az incidenskezelés fázisait az észleléstől az incidens utáni értékelésig.

A NIST számítógépes biztonsági incidensek kezelési útmutatóját is kihasználhatja a saját incidens-válasz tervének létrehozásához: https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf

Munkafolyamat-automatizálás konfigurálása Azure Security Centeron belül: https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide

Útmutató a saját biztonsági incidensek megoldási folyamatának létrehozásához: https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/

A Microsoft Security Response Center egy Incidensének anatómiája: https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/


**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10,2: incidensek pontozásának és rangsorolási eljárásának létrehozása

**Útmutató**: a Security Center súlyosságot rendel az egyes riasztásokhoz, hogy a prioritások alapján ki lehessen deríteni, hogy mely riasztásokat kell először megvizsgálni. A súlyosság attól függ, hogy a Security Center hol található, vagy a riasztás kibocsátásához használt elemzést, valamint azt a megbízhatósági szintet, amelyet a riasztáshoz vezető tevékenység mögött rosszindulatú szándék jelent.

Emellett egyértelműen megjelölheti az előfizetéseket (pl.: éles környezetben nem termelt), és hozzon létre egy elnevezési rendszert az Azure-erőforrások egyértelmű azonosításához és kategorizálásához.

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="103-test-security-response-procedures"></a>10,3: biztonsági reagálási eljárások tesztelése

**Útmutató**: a rendszerek incidensek reagálási képességeinek rendszeres tesztelésére szolgáló gyakorlatok végrehajtása. Azonosítsa a gyenge pontokat és réseket, és szükség esetén dolgozza át a tervet.

Tekintse meg a NIST kiadványát: útmutató az IT-csomagok és-képességek teszteléséhez, betanításához és gyakorlatának megtervezéséhez: https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10,4: biztonsági incidensek elérhetőségének biztosítása és riasztási értesítések konfigurálása biztonsági incidensekhez

**Útmutató**: a Microsoft a biztonsági incidensek elérhetőségi adatait arra használja fel, hogy felvegye Önnel a kapcsolatot, ha a Microsoft Security Response Center (MSRC) felfedi, hogy az ügyfél adatait egy törvénytelen vagy jogosulatlan fél is hozzáférte.  A problémák megoldása érdekében tekintse át az incidenseket a tény után.

A Azure Security Center biztonsági kapcsolattartó beállítása: https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10,5: biztonsági riasztások beépítése az incidensek gyorsreagáló rendszerébe

**Útmutató**: az Azure Security Center-riasztások és-javaslatok exportálása a folyamatos exportálás funkció használatával. A folyamatos exportálás lehetővé teszi a riasztások és javaslatok manuális és folyamatos exportálását. Az Azure Security Center adatösszekötővel továbbíthatja a riasztásokat az Azure Sentinel szolgáltatásba.

Folyamatos exportálás konfigurálása: https://docs.microsoft.com/azure/security-center/continuous-export

Riasztások továbbítása az Azure Sentinelbe: https://docs.microsoft.com/azure/sentinel/connect-azure-security-center

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="106-automate-the-response-to-security-alerts"></a>10,6: a biztonsági riasztásokra adott válasz automatizálása

**Útmutató**: a Azure Security Center munkafolyamat-automatizálási funkciója segítségével automatikusan aktiválhatja a válaszokat a "Logic apps" használatával a biztonsági riasztások és javaslatok esetében.

A Munkafolyamat-automatizálás és a Logic Apps konfigurálása: https://docs.microsoft.com/azure/security-center/workflow-automation

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

## <a name="penetration-tests-and-red-team-exercises"></a>Behatolási tesztek és Red Team-gyakorlatok

*További információkért lásd [: biztonsági ellenőrzés: behatolási tesztek és Red Team-gyakorlatok](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings-within-60-days"></a>11,1: rendszeres penetrációs tesztelést végez az Azure-erőforrásokon, és gondoskodik az összes kritikus biztonsági vizsgálat 60 napon belüli szervizeléséről

**Útmutató**: kövesse a Microsoft részvételi szabályait, hogy a behatolási tesztek ne sértsék a Microsoft-házirendeket: https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1

A Microsoft által felügyelt felhőalapú infrastruktúrával, szolgáltatásokkal és alkalmazásokkal kapcsolatos további információkért tekintse meg a következő témakört:  https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Megosztott

## <a name="next-steps"></a>Következő lépések

- Lásd az [Azure biztonsági teljesítménytesztét](../security/benchmarks/overview.md)
- További információ az [Azure biztonsági Alaptervekről](../security/benchmarks/security-baselines-overview.md)
