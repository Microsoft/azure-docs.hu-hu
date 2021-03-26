---
title: Azure Cosmos DB Azure biztonsági alapterve
description: A Azure Cosmos DB biztonsági alapterve az Azure biztonsági Teljesítménytesztben meghatározott biztonsági javaslatok megvalósítására szolgáló eljárási útmutatást és erőforrásokat biztosít.
author: msmbaldwin
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 02/17/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: dead43f2e9f2e8913bcebde43d543b8df8d33ced
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2021
ms.locfileid: "105565673"
---
# <a name="azure-security-baseline-for-azure-cosmos-db"></a>Azure Cosmos DB Azure biztonsági alapterve

Ez a biztonsági alapkonfiguráció az [Azure biztonsági teljesítményteszt 1,0-es verziójáról](../security/benchmarks/overview-v1.md) Azure Cosmos DBre vonatkozó útmutatást alkalmaz. Az Azure Security Benchmark ajánlásokat ad arra nézve, hogy hogyan tehetők biztonságossá a felhőalapú megoldások az Azure-ban.
A tartalom az Azure biztonsági teljesítményteszt által meghatározott **biztonsági vezérlők** szerint van csoportosítva, valamint a Azure Cosmos db vonatkozó kapcsolódó útmutatás. A Azure Cosmos DB nem alkalmazható **vezérlők** ki vannak zárva.

 
Ha szeretné megtekinteni, hogyan Azure Cosmos DB teljes mértékben leképezni az Azure biztonsági Teljesítménytesztét, tekintse meg a [teljes Azure Cosmos db biztonsági alapterv-leképezési fájlt](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="network-security"></a>Hálózati biztonság

*További információ: [Azure Security Benchmark: Hálózati biztonság](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1,1: Azure-erőforrások biztosítása virtuális hálózatokon belül

**Útmutató**: az Azure Private link használatával privát végponton keresztül csatlakozhat egy Azure Cosmos-fiókhoz. A virtuális hálózat és a szolgáltatás közötti forgalom a Microsoft gerinchálózatán keresztül halad át, így kiküszöböli a nyilvános internet jelentette kitettséget. Emellett csökkentheti az adatkiszűrése kockázatát azáltal, hogy a kimenő szabályok szigorú készletét konfigurálja egy hálózati biztonsági csoportra (NSG), és társítja az adott NSG az alhálózattal.

Az Azure Cosmos-fiók biztonságossá tételéhez szolgáltatási végpontokat is használhat. A szolgáltatás-végpont engedélyezésével úgy konfigurálhatja az Azure Cosmos-fiókokat, hogy csak az Azure-beli virtuális hálózatok egy adott alhálózatáról engedélyezze a hozzáférést. Miután a Azure Cosmos DB szolgáltatás végpontja engedélyezve lett, korlátozhatja az Azure Cosmos-fiókhoz való hozzáférést egy virtuális hálózatban lévő alhálózatról érkező kapcsolattal.

Az Azure Cosmos-fiókban tárolt adatait IP-tűzfalak használatával is biztonságossá teheti. A Azure Cosmos DB támogatja az IP-alapú hozzáférés-vezérlést a bejövő tűzfalak támogatásához. Beállíthat egy IP-tűzfalat az Azure Cosmos-fiókban a Azure Portal, Azure Resource Manager sablonok vagy az Azure CLI vagy a Azure PowerShell használatával.

- [Az Azure Private Link áttekintése](../private-link/private-link-overview.md)

- [Privát végpont konfigurálása Azure Cosmos DBhoz](how-to-configure-private-endpoints.md) 

- [Hálózati biztonsági csoport létrehozása biztonsági konfigurációval](../virtual-network/tutorial-filter-network-traffic.md)

- [Az IP-tűzfal konfigurálása a Cosmos DBban](how-to-configure-firewall.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás**: az [Azure biztonsági teljesítményteszt](/azure/governance/policy/samples/azure-security-benchmark) a Security Center alapértelmezett házirend-kezdeményezése, és a [Security Center ajánlásainak](/azure/security-center/security-center-recommendations)alapja. A vezérlőhöz kapcsolódó Azure Policy-definíciók Security Center automatikusan engedélyezve vannak. Az ehhez a vezérlőhöz kapcsolódó riasztásokhoz szükség lehet egy [Azure Defender](/azure/security-center/azure-defender) -csomagra a kapcsolódó szolgáltatásokhoz.

**Azure Policy beépített definíciók – Microsoft.DocumentDB**:

[!INCLUDE [Resource Policy for Microsoft.DocumentDB 1.1](../../includes/policy/standards/asb/rp-controls/microsoft.documentdb-1-1.md)]

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-network-interfaces"></a>1,2: a virtuális hálózatok, alhálózatok és hálózati adapterek konfigurációjának és forgalmának figyelése és naplózása

**Útmutató**: a Azure Security Center használata és a hálózatvédelmi javaslatok követése az Azure Cosmos-fiókhoz kapcsolódó hálózati erőforrások biztonságossá tételéhez.

Ha a virtuális gépeket az Azure Cosmos-fiókkal megegyező virtuális hálózatban telepítik, akkor a hálózati biztonsági csoport (NSG) segítségével csökkentheti az adatkiszűrése kockázatát. NSG-adatforgalmi naplók engedélyezése és naplók küldése Azure Storage-fiókba forgalmi auditok esetén. NSG-naplókat is küldhet egy Log Analytics munkaterületre, és a Traffic Analytics használatával betekintést nyerhet az Azure-Felhőbeli forgalomba. A Traffic Analytics egyes előnyei lehetővé teszi a hálózati tevékenységek megjelenítését és a gyakori pontok azonosítását, a biztonsági fenyegetések azonosítását, a forgalomban rejlő minták értelmezését, valamint a hálózati helytelen konfigurációk meghatározását.

- [A Azure Security Center által biztosított hálózati biztonság ismertetése](../security-center/security-center-network-recommendations.md)

- [A NSG folyamat naplófájljainak engedélyezése](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [A Traffic Analytics engedélyezése és használata](../network-watcher/traffic-analytics.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="13-protect-critical-web-applications"></a>1,3: a kritikus webalkalmazások megóvása

**Útmutató**: az eltérő eredetű erőforrás-megosztás (CORS) szolgáltatással engedélyezheti, hogy az egyik tartományon futó webalkalmazások hozzáférhessenek egy másik tartomány erőforrásaihoz. A böngészők olyan biztonsági korlátozást valósítanak meg, amelyik azonos eredetű házirend, amely megakadályozza, hogy egy weblap egy másik tartományban lévő API-kat hívjon fel. A CORS azonban biztonságos módot biztosít ahhoz, hogy a forrás tartomány egy másik tartományban lévő API-kat hívjon fel. Miután engedélyezte az Azure Cosmos-fiók CORS-támogatását, a rendszer csak a hitelesített kérelmeket értékeli ki, hogy azok a megadott szabályoknak megfelelően engedélyezettek-e.

- [Az idegen eredetű erőforrások megosztásának konfigurálása](how-to-configure-cross-origin-resource-sharing.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1,4: az ismert kártevő IP-címekkel folytatott kommunikáció megtagadása

**Útmutató**: a Azure Cosmos db komplex veszélyforrások elleni védelem (ATP) használata. Az ATP for Azure Cosmos DB egy további biztonsági intelligenciát biztosít, amely szokatlan és potenciálisan ártalmas kísérleteket észlel az Azure Cosmos-fiókok eléréséhez vagy kiaknázásához. Ez a védelmi réteg lehetővé teszi a fenyegetések kezelésére és a központi biztonsági figyelő rendszerekkel való integrálását.

A Azure Cosmos DB-példányokhoz társított virtuális hálózatokon DDoS Protection szabványt engedélyezheti a DDoS-támadások elleni védelemhez. A Azure Security Center integrált fenyegetési intelligencia használatával megtagadhatja a kommunikációt az ismert kártékony vagy nem használt internetes IP-címekkel.

- [Azure Cosmos DB komplex veszélyforrások elleni védelem konfigurálása](cosmos-db-advanced-threat-protection.md)

- [A DDoS Protection konfigurálása](../ddos-protection/manage-ddos-protection.md)

- [Az Azure Security Center integrált veszélyforrások felderítésének megismerése](../security-center/azure-defender.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="15-record-network-packets"></a>1,5: hálózati csomagok rögzítése

**Útmutató**: engedélyezze a hálózati biztonsági csoport (NSG) folyamatának naplóit, és küldje el a naplókat a forgalom naplózásához a Storage-fiókba. NSG-naplókat küldhet egy Log Analytics munkaterületre, és a Traffic Analytics használatával betekintést nyerhet az Azure-Felhőbeli forgalomba. A Traffic Analytics egyes előnyei lehetővé teszi a hálózati tevékenységek megjelenítését és a gyakori pontok azonosítását, a biztonsági fenyegetések azonosítását, a forgalomban rejlő minták értelmezését, valamint a hálózati helytelen konfigurációk meghatározását.

- [A NSG folyamat naplófájljainak engedélyezése](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [A Traffic Analytics engedélyezése és használata](../network-watcher/traffic-analytics.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1,6: hálózati behatolás-észlelési/Behatolás-megelőzési rendszerek (AZONOSÍTÓk/IP-címek) üzembe helyezése

**Útmutató**: a Azure Cosmos db komplex veszélyforrások elleni védelem (ATP) használata. Az ATP for Azure Cosmos DB egy további biztonsági intelligenciát biztosít, amely szokatlan és potenciálisan ártalmas kísérleteket észlel az Azure Cosmos-fiókok eléréséhez vagy kiaknázásához. Ez a védelmi réteg lehetővé teszi a fenyegetések kezelésére és a központi biztonsági figyelő rendszerekkel való integrálását. 

- [Cosmos DB komplex veszélyforrások elleni védelem konfigurálása](cosmos-db-advanced-threat-protection.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1,8: a hálózati biztonsági szabályok bonyolultságának és adminisztratív terhelésének csökkentése

**Útmutató**: az Azure Cosmos-fiókhoz hozzáféréssel rendelkező erőforrások esetében Virtual Network szolgáltatás-címkék használatával definiálhatja a hálózati biztonsági csoportokon vagy Azure Firewall a hálózati hozzáférés-vezérlést. Biztonsági szabályok létrehozása során szolgáltatáscímkéket használhat bizonyos IP-címek helyett. Ha egy szabály megfelelő forrás vagy cél mezőjében megadja a szolgáltatási címke nevét (például AzureCosmosDB), akkor engedélyezheti vagy megtagadhatja a megfelelő szolgáltatás forgalmát. A Microsoft kezeli a szolgáltatási címke által felölelt címek előtagjait, és automatikusan frissíti a szolgáltatási címkét a címek változásával.

- [További információ a szolgáltatási címkék használatáról](../virtual-network/service-tags-overview.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1,9: a hálózati eszközök szabványos biztonsági konfigurációinak fenntartása

**Útmutató**: hálózati erőforrások szabványos biztonsági konfigurációinak definiálása és implementálása Azure Policy használatával. Használjon Azure Policy aliasokat a "Microsoft.DocumentDB" és a "Microsoft. Network" névterekben, hogy egyéni házirendeket hozzon létre az Azure Cosmos DB példányok hálózati konfigurációjának naplózásához vagy érvénybe léptetéséhez. Használhatja a Azure Cosmos DB beépített szabályzat-definícióját is, például:

- A Cosmos DB-fiókok komplex veszélyforrások elleni védelmének üzembe helyezése

- Cosmos DB virtuális hálózati szolgáltatás végpontját kell használnia

Az Azure-tervrajzok segítségével leegyszerűsítheti a nagy léptékű Azure-környezeteket a főbb környezeti összetevők, például a Azure Resource Manager sablonok, az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC) és a házirendek egyetlen terv szerinti definícióban való kicsomagolásával. A tervet egyszerűen alkalmazhatja új előfizetésekre, környezetekre, valamint a verziószámozáson keresztül történő finomhangolásra és felügyeletre.

- [Az Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md)

- [Azure Blueprint létrehozása](../governance/blueprints/create-blueprint-portal.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="110-document-traffic-configuration-rules"></a>1,10: a dokumentum forgalmának konfigurációs szabályai

**Útmutató**: a Azure Cosmos db üzembe helyezéséhez társított hálózati erőforrásokhoz használjon címkéket, hogy logikailag szervezze őket a besorolásba.

- [Címkék létrehozása és használata](../azure-resource-manager/management/tag-resources.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1,11: automatikus eszközök használata a hálózati erőforrások konfigurációjának figyelésére és a változások észlelésére

**Útmutató**: az Azure-tevékenység naplójának használata a hálózati erőforrás-konfigurációk figyelésére és a Azure Cosmos db-példányokhoz kapcsolódó hálózati erőforrások változásainak észlelésére. Hozzon létre riasztásokat Azure Monitoron belül, amelyek akkor lépnek életbe, amikor a kritikus hálózati erőforrásokra vonatkozó módosításokat végrehajtják 

- [Azure-Tevékenységnaplók eseményeinek megtekintése és lekérése](../azure-monitor/essentials/activity-log.md#view-the-activity-log)

- [Riasztások létrehozása a Azure Monitorban](../azure-monitor/alerts/alerts-activity-log.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

## <a name="logging-and-monitoring"></a>Naplózás és monitorozás

*További információt az [Azure biztonsági teljesítményteszt: naplózás és figyelés](../security/benchmarks/security-control-logging-monitoring.md)című témakörben talál.*

### <a name="22-configure-central-security-log-management"></a>2,2: a központi biztonsági naplók felügyeletének konfigurálása

**Útmutató**: naplók beolvasása Azure monitor segítségével a Azure Cosmos db által generált biztonsági adatokat összesítve. Azure Monitor a Log Analytics-munkaterületek használatával kérdezheti le és végezheti el az elemzéseket, és használhat tárolási fiókokat a hosszú távú/archiválási tároláshoz. Azt is megteheti, hogy az Azure Sentinel vagy egy harmadik féltől származó biztonsági incidens és rendezvényszervezés (SIEM) számára is elvégezheti az adatfeldolgozást. 

- [Diagnosztikai naplók engedélyezése Azure Cosmos DBhoz](./monitor-cosmos-db.md)

- [Az Azure Sentinel előkészítése](../sentinel/quickstart-onboard.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="23-enable-audit-logging-for-azure-resources"></a>2,3: az Azure-erőforrások naplózásának engedélyezése

**Útmutató**: engedélyezze a Azure Cosmos db diagnosztikai beállításait, és küldje el a naplókat egy log Analytics-munkaterületre vagy egy Azure Storage-fiókba. A Azure Cosmos DB diagnosztikai beállításai az erőforrás-naplók összegyűjtésére szolgálnak. Ezeket a naplókat a rendszer kérés alapján rögzíti, és az **adatsíkok naplóiként** is hivatkozik rá. Néhány példa az adatsík-műveletekre: törlés, Beszúrás és olvasás. 

Engedélyezheti az Azure-műveletnapló diagnosztikai beállításait is, és elküldheti ezeket a naplókat a Azure Cosmos DB naplókhoz használt Log Analytics munkaterületre.

- [A Azure Cosmos DB diagnosztikai beállításainak engedélyezése](./monitor-cosmos-db.md)

- [Diagnosztikai beállítások engedélyezése az Azure-beli tevékenység naplójában](../azure-monitor/essentials/activity-log.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="25-configure-security-log-storage-retention"></a>2,5: a biztonsági napló tárolási adatmegőrzésének konfigurálása

**Útmutató**: a Azure monitor a szervezet megfelelőségi előírásai alapján állítsa be a Azure Cosmos db-példányokhoz társított log Analytics-munkaterületek naplózásának megőrzési időtartamát.

- [Napló-megőrzési paraméterek beállítása](../azure-monitor/logs/manage-cost-storage.md#change-the-data-retention-period)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="26-monitor-and-review-logs"></a>2,6: naplók figyelése és áttekintése

**Útmutató**: lekérdezéseket végezhet log Analytics egy munkaterületen a kifejezések kereséséhez, a trendek azonosításához, a minták elemzéséhez és számos más elemzéshez az összegyűjtött Azure Cosmos db naplók alapján.

- [Azure Cosmos DB lekérdezések végrehajtása Log Analytics-munkaterületeken](monitor-cosmos-db.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="27-enable-alerts-for-anomalous-activities"></a>2,7: riasztások engedélyezése rendellenes tevékenységekhez

**Útmutató**: a Azure Security Centerban engedélyezze a komplex veszélyforrások elleni védelmet Azure Cosmos db a rendellenes tevékenységek figyeléséhez a biztonsági naplókban és eseményekben. A Azure Cosmos DB diagnosztikai beállításainak engedélyezése és a naplók elküldése egy Log Analytics munkaterületre.

 

A Log Analytics-munkaterületet az Azure Sentinelbe is felkészítheti, mivel ez egy biztonsági előkészítési automatizált választ (felszárnyaló) megoldást biztosít. Ez lehetővé teszi a forgatókönyvek (automatizált megoldások) létrehozását és a biztonsági problémák megoldására való felhasználását. Emellett Azure Monitor használatával is létrehozhat egyéni napló-riasztásokat a Log Analytics munkaterületen.

- [A veszélyforrások elleni védelmi riasztások listája Azure Cosmos DB](../security-center/alerts-reference.md#alerts-azurecosmos)

- [Az Azure Sentinel előkészítése](../sentinel/quickstart-onboard.md)

- [Naplózási riasztások létrehozása, megtekintése és kezelése Azure Monitor használatával](../azure-monitor/alerts/alerts-log.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

## <a name="identity-and-access-control"></a>Identitás- és hozzáférés-vezérlés

*További információt az [Azure biztonsági teljesítményteszt: Identity and Access Control](../security/benchmarks/security-control-identity-access-control.md)című témakörben talál.*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3,1: a felügyeleti fiókok leltárának karbantartása

**Útmutató**: a Azure Portal identitás-és hozzáférés-vezérlés (iam) paneljén konfigurálhatja a szerepköralapú hozzáférés-vezérlést (RBAC), és megtarthatja a leltárt Azure Cosmos db erőforrásokon. A szerepkörök a felhasználókra, csoportokra, egyszerű szolgáltatásokra és felügyelt identitásokra vonatkoznak Azure Active Directoryban (Azure AD). A felhasználók és csoportok számára beépített szerepköröket vagy egyéni szerepköröket is használhat.

A Azure Cosmos DB beépített Azure-RBAC biztosít a gyakori felügyeleti forgatókönyvekhez Azure Cosmos DBban. Az Azure AD-ben profilokkal rendelkező személy a felhasználókhoz, csoportokhoz, egyszerű szolgáltatásokhoz vagy felügyelt identitásokhoz rendelheti hozzá ezeket az Azure-szerepköröket a Azure Cosmos DB erőforrásokhoz és műveletekhez való hozzáférés megadásához vagy megtagadásához.

Az Azure AD PowerShell-modullal is elvégezheti az alkalmi lekérdezéseket a felügyeleti csoportok tagjait futtató fiókok felderítéséhez.

Emellett a Azure Cosmos DB egyes műveletei az Azure AD-vel és a fiók-specifikus főkulcsokkal is vezérelhetők. A "disableKeyBasedMetadataWriteAccess" fiók beállításával vezérelheti a kulcsokhoz való hozzáférést.

- [A szerepköralapú hozzáférés-vezérlés ismertetése Azure Cosmos DB](role-based-access-control.md)

- [Saját egyéni szerepkörök létrehozása Azure Cosmos DB műveletek használatával (Microsoft.DocumentDB-névtér)](../role-based-access-control/resource-provider-operations.md#microsoftdocumentdb)

- [Új szerepkör létrehozása az Azure AD-ben](../role-based-access-control/custom-roles.md)

- [Címtárbeli szerepkör beszerzése az Azure AD-ben a PowerShell-lel](/powershell/module/azuread/get-azureaddirectoryrole?preserve-view=true&view=azureadps-2.0)

- [Címtárbeli szerepkör tagjainak beszerzése az Azure AD-ben a PowerShell-lel](/powershell/module/azuread/get-azureaddirectoryrolemember?preserve-view=true&view=azureadps-2.0)

- [Felhasználói hozzáférés korlátozása az adatműveletekre](how-to-restrict-user-data.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="32-change-default-passwords-where-applicable"></a>3,2: az alapértelmezett jelszavak módosítása, ha alkalmazható

**Útmutató**: az alapértelmezett vagy az üres jelszavak fogalma nem létezik a Azure Active Directory (Azure ad) vagy Azure Cosmos db kapcsolatban. Ehelyett Azure Cosmos DB két típusú kulcsot használ a felhasználók hitelesítéséhez és az adataihoz és erőforrásaihoz való hozzáférés biztosításához. főkulcsok és erőforrás-tokenek. A kulcsok bármikor újra létrehozhatók.

- [A Azure Cosmos DBban tárolt adathozzáférés ismertetése](secure-access-to-data.md)

- [Azure Cosmos DB kulcsok újragenerálása](./manage-with-powershell.md#regenerate-keys)

- [Kulcsok programozott elérésének módja az Azure AD-vel](certificate-based-authentication.md)

**Felelősség**: Megosztott

**Azure Security Center figyelés**: nincs

### <a name="33-use-dedicated-administrative-accounts"></a>3,3: dedikált rendszergazdai fiókok használata

**Útmutató**: nem alkalmazható; A Azure Cosmos DB nem támogatja a rendszergazdai fiókokat. Minden hozzáférés integrálva van Azure Active Directory (Azure AD) és Azure szerepköralapú hozzáférés-vezérléssel (Azure RBAC).

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="34-use-azure-active-directory-single-sign-on-sso"></a>3,4: Azure Active Directory egyszeri bejelentkezés (SSO) használata

**Útmutató**: a Azure Cosmos db kétféle kulcsot használ a felhasználók engedélyezéséhez, és az adatsík szintjén nem támogatja az egyszeri Sign-Ont (SSO). A Cosmos DB vezérlési síkja elérhető a REST APIon keresztül, és támogatja az egyszeri bejelentkezést. A hitelesítéshez állítsa be a kérések engedélyezési fejlécét egy Azure Active Directory (Azure AD) által beszerzett JSON Web Tokenra.

- [Az Azure Database Cosmos DB REST API ismertetése](/rest/api/cosmos-db/)

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

- [További tudnivalók a privilegizált hozzáférésű munkaállomásokról](/security/compass/privileged-access-devices)
 
- [Többtényezős hitelesítés engedélyezése az Azure-ban](../active-directory/authentication/howto-mfa-getstarted.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3,7: naplózás és riasztás a gyanús tevékenységekről a rendszergazdai fiókoktól

**Útmutató**: a Azure Cosmos db komplex veszélyforrások elleni védelem (ATP) használata. Az ATP for Azure Cosmos DB egy további biztonsági intelligenciát biztosít, amely szokatlan és potenciálisan ártalmas kísérleteket észlel az Azure Cosmos-fiókok eléréséhez vagy kiaknázásához. Ez a védelmi réteg lehetővé teszi a fenyegetések kezelésére és a központi biztonsági figyelő rendszerekkel való integrálását.

Emellett Azure Active Directory (Azure AD) Privileged Identity Management (PIM) is használható a naplók és riasztások generálásához, ha gyanús vagy nem biztonságos tevékenység történik a környezetben.

Az Azure AD-kockázati észlelések használatával a kockázatos felhasználói viselkedésre vonatkozó riasztásokat és jelentéseket tekinthet meg.

- [Privileged Identity Management (PIM) üzembe helyezése](../active-directory/privileged-identity-management/pim-deployment-plan.md)

- [Az Azure AD kockázati észlelések ismertetése](../active-directory/identity-protection/overview-identity-protection.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3,8: az Azure-erőforrások kezelése csak jóváhagyott helyekről

**Útmutató**: a feltételes hozzáférési szabályzat tárolási állapotának konfigurálása és az elnevezett helyek kezelése. Az elnevezett helyszínekkel logikai csoportosításokat hozhat létre az IP-címtartományok vagy országok és régiók számára. Az érzékeny erőforrásokhoz, például a Azure Cosmos DB példányokhoz való hozzáférést a konfigurált elnevezett helyekre korlátozhatja.

- [Elnevezett helyszínek konfigurálása az Azure-ban](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="39-use-azure-active-directory"></a>3,9: a Azure Active Directory használata

**Útmutató**: a Azure Active Directory (Azure ad) használata központi hitelesítési és engedélyezési rendszerrel. Az Azure AD az adatok védelme érdekében erős titkosítást használ a nyugalmi és a továbbítási adatokhoz. Az Azure AD emellett a felhasználó hitelesítő adatainak a sók, a kivonatok és a biztonságos tárolását is tartalmazza.

- [Azure AD-példány létrehozása és konfigurálása](../active-directory-domain-services/tutorial-create-instance.md)

- [Azure AD-hitelesítés konfigurálása és kezelése az Azure SQL használatával](../azure-sql/database/authentication-aad-configure.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="310-regularly-review-and-reconcile-user-access"></a>3,10: a felhasználói hozzáférés rendszeres áttekintése és egyeztetése

**Útmutató**: a Azure Active Directory (Azure ad) olyan naplókat biztosít, amelyek segítenek az elavult fiókok felderítésében. Emellett az Azure Identity Access Reviews használatával hatékonyan kezelheti a csoporttagságok kezelését, a vállalati alkalmazásokhoz való hozzáférést és a szerepkör-hozzárendeléseket is. A felhasználó hozzáférését rendszeresen felül lehet vizsgálni, hogy csak a megfelelő felhasználók férhessenek hozzájuk.

- [Az Azure Identity hozzáférési felülvizsgálatok használata](../active-directory/governance/access-reviews-overview.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3,11: a figyelő megkísérli a deaktivált hitelesítő adatok elérését

**Útmutató**: diagnosztikai beállításokat hozhat létre Azure Active Directory (Azure ad) felhasználói fiókokhoz, elküldheti a naplókat és bejelentkezési naplókat egy log Analytics munkaterületre, ahol konfigurálhatja a kívánt riasztásokat.

- [Azure-tevékenységnaplók integrálása az Azure Monitorba](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3,12: riasztás a fiók bejelentkezési viselkedésének eltérése esetén

**Útmutató**: a Azure Cosmos db komplex veszélyforrások elleni védelem (ATP) használata. Az ATP for Azure Cosmos DB egy további biztonsági intelligenciát biztosít, amely szokatlan és potenciálisan ártalmas kísérleteket észlel az Azure Cosmos-fiókok eléréséhez vagy kiaknázásához. Ez a védelmi réteg lehetővé teszi a fenyegetések kezelésére és a központi biztonsági figyelő rendszerekkel való integrálását.

Az Azure Active Directory (Azure AD) Identity Protection és a kockázati észlelések szolgáltatással is konfigurálhatja a felhasználói identitásokkal kapcsolatos gyanús műveletekre vonatkozó automatizált válaszokat. Emellett további vizsgálat céljából betöltheti a naplókat az Azure Sentinelbe.

- [Az Azure AD kockázatos bejelentkezéseinek megtekintése](../active-directory/identity-protection/overview-identity-protection.md)

- [Az Identity Protection kockázati házirendjeinek konfigurálása és engedélyezése](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [Az Azure Sentinel előkészítése](../sentinel/quickstart-onboard.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

## <a name="data-protection"></a>Adatvédelem

*További információ: [Azure Security Benchmark: Adatvédelem](../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4,1: bizalmas információk leltárának fenntartása

**Útmutató**: a címkék használatával segítheti a bizalmas adatokat tároló vagy feldolgozó Azure Cosmos db-példányok nyomon követését.

- [Címkék létrehozása és használata](../azure-resource-manager/management/tag-resources.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4,2: bizalmas adatok tárolására vagy feldolgozására szolgáló rendszerek elkülönítése

**Útmutató**: különálló előfizetések és/vagy felügyeleti csoportok megvalósítása fejlesztési, tesztelési és éles környezetekhez. Azure Cosmos DB példányokat a virtuális hálózat/alhálózat választja el, megfelelően címkézve, és egy hálózati biztonsági csoporton (NSG) vagy Azure Firewalln belül biztonságossá tettük. A bizalmas adatokat tároló Azure Cosmos DB példányokat el kell különíteni. Az Azure Private link használatával privát végponton keresztül kapcsolódhat egy Azure Cosmos DB-példány fiókjához. A magánhálózati végpont a virtuális hálózaton belüli alhálózat magánhálózati IP-címeinek halmaza. Ezután korlátozhatja a hozzáférést a kiválasztott magánhálózati IP-címekhez. 

- [További Azure-előfizetések létrehozása](../cost-management-billing/manage/create-subscription.md)

- [Felügyeleti csoportok létrehozása](../governance/management-groups/create-management-group-portal.md)

- [Címkék létrehozása és használata](../azure-resource-manager/management/tag-resources.md)

- [Privát végpont konfigurálása Azure Cosmos DBhoz](how-to-configure-private-endpoints.md)

- [Hálózati biztonsági csoport létrehozása biztonsági konfigurációval](../virtual-network/tutorial-filter-network-traffic.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4,3: a bizalmas adatok jogosulatlan átvitelének figyelése és letiltása

**Útmutató**: a Azure Cosmos db komplex veszélyforrások elleni védelmét is felhasználhatja, amely észleli az adatbázisok eléréséhez vagy kiaknázásához szükséges szokatlan és vélhetően ártalmas tevékenységeket. Jelenleg a következő riasztásokat indítja el:

- Hozzáférés szokatlan helyekről

- Szokatlan adatbányászat

Emellett, ha virtuális gépeket használ a Azure Cosmos DB példányaihoz való hozzáféréshez, használja a privát hivatkozásokat, a tűzfalat, a hálózati biztonsági csoportokat és a szolgáltatási címkéket az adatkiszűrése lehetőségének enyhítése érdekében. A Microsoft kezeli a Azure Cosmos DB alapjául szolgáló infrastruktúrát, és szigorú ellenőrzéseket vezetett be az ügyféladatok elvesztésének vagy kihatásának megelőzésére.

- [Cosmos DB komplex veszélyforrások elleni védelem konfigurálása](cosmos-db-advanced-threat-protection.md)

- [Az ügyféladatok Azure-beli védelmének ismertetése](../security/fundamentals/protection-customer-data.md)

**Felelősség**: Megosztott

**Azure Security Center figyelés**: nincs

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4,5: aktív felderítési eszköz használata a bizalmas adatok azonosítására

**Útmutató**: az automatikus adatazonosítási, besorolási és veszteség-megelőzési funkciók még nem érhetők el Azure Cosmos db számára. A besorolási és adatelemzési szolgáltatáshoz azonban használhatja az Azure Cognitive Search integrációját. Egy külső gyártótól származó megoldást is megvalósíthat, ha az a megfelelőség szempontjából szükséges.

A Microsoft által felügyelt mögöttes platform esetében a Microsoft az összes vásárlói tartalmat bizalmasként kezeli, és az ügyfelek adatvesztésével és a kitettséggel szembeni védelem érdekében nagy hosszúságú. Annak biztosítása érdekében, hogy az Azure-beli ügyféladatok biztonságban maradjanak, a Microsoft végrehajtotta és karbantartja a robusztus adatvédelmi szabályozást és képességeket.

- [Azure Cosmos DB az Azure-beli adatCognitive Search](../search/search-howto-index-cosmosdb.md?bc=%2fazure%2fcosmos-db%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fcosmos-db%2ftoc.json)

- [Az ügyféladatok Azure-beli védelmének ismertetése](../security/fundamentals/protection-customer-data.md)

**Felelősség**: Megosztott

**Azure Security Center figyelés**: nincs

### <a name="46-use-role-based-access-control-to-control-access-to-resources"></a>4,6: szerepköralapú hozzáférés-vezérlés használata az erőforrásokhoz való hozzáférés szabályozásához

**Útmutató**: a Azure Cosmos db beépített Azure szerepköralapú hozzáférés-vezérlést (Azure RBAC) biztosít a Azure Cosmos db gyakori felügyeleti eseteihez. Az Azure Active Directory (Azure AD) profillal rendelkező személy az Azure-szerepköröket hozzárendelheti a felhasználókhoz, csoportokhoz, egyszerű szolgáltatásokhoz vagy felügyelt identitásokhoz, hogy hozzáférést biztosítson vagy megtagadjon az erőforrásokhoz és műveletekhez a Azure Cosmos DB erőforrásokon. A szerepkör-hozzárendelések hatóköre csak a csak vezérlőre vonatkozik, amely hozzáférést biztosít az Azure Cosmos-fiókok,-adatbázisok,-tárolók és-ajánlatok (átviteli sebesség) számára.

- [Az Azure RBAC megvalósítása Azure Cosmos DB](role-based-access-control.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="48-encrypt-sensitive-information-at-rest"></a>4,8: bizalmas adatok titkosítása a nyugalmi állapotban

**Útmutató**: a Cosmos DBban tárolt összes felhasználói adatok alapértelmezés szerint titkosítva maradnak. Nincsenek kikapcsolható vezérlők. A Azure Cosmos DB AES-256 titkosítást használ minden olyan régióban, ahol a fiók fut.

Alapértelmezés szerint a Microsoft kezeli az Azure Cosmos-fiókban tárolt adattitkosításhoz használt kulcsokat. Lehetőség van arra is, hogy egy második titkosítási réteget adjon hozzá a saját kulcsaihoz.

- [A inaktív adatok titkosításának megértése Azure Cosmos DB](database-encryption-at-rest.md)

- [A titkosítás a Azure Cosmos DB]()

- [Az ügyfél által felügyelt kulcsok konfigurálása a Azure Cosmos DB-fiókhoz](how-to-setup-cmk.md)

**Felelősség**: Megosztott

**Azure Security Center figyelés**: nincs

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4,9: a kritikus Azure-erőforrások változásainak naplózása és riasztása

**Útmutató**: a Azure monitor és az Azure-tevékenység naplójának használata riasztások létrehozásához, amikor a módosítások a Azure Cosmos db éles példányain lépnek életbe.

- [Riasztások létrehozása az Azure-tevékenységek naplózási eseményeihez](../azure-monitor/alerts/alerts-activity-log.md)

- [Riasztások létrehozása az Azure-tevékenységek naplózási eseményeihez](../azure-monitor/alerts/alerts-activity-log.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

## <a name="vulnerability-management"></a>Biztonságirés-kezelés

*További információért lásd az [Azure biztonsági teljesítményteszt: biztonsági rés kezelése](../security/benchmarks/security-control-vulnerability-management.md)című témakört.*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5,1: automatikus biztonsági rések vizsgálatára szolgáló eszközök futtatása

**Útmutató**: a Azure Cosmos db példányok Azure Security Center ajánlásainak követése. 

A Microsoft a Azure Cosmos DB példányokat támogató mögöttes gazdagépeken hajtja végre a rendszerjavítást és a biztonsági rések kezelését. Annak biztosítása érdekében, hogy az Azure-beli ügyféladatok biztonságban maradjanak, a Microsoft végrehajtotta és karbantartja a robusztus adatvédelmi szabályozást és képességeket.

- [Az Azure Security Centerben elérhető támogatott funkciók](../security-center/security-center-services.md?tabs=features-windows)

**Felelősség**: Megosztott

**Azure Security Center figyelés**: nincs

## <a name="inventory-and-asset-management"></a>Leltár-és eszközfelügyelet

*További információt az [Azure biztonsági teljesítményteszt: leltár és eszközkezelés](../security/benchmarks/security-control-inventory-asset-management.md)című témakörben talál.*

### <a name="61-use-automated-asset-discovery-solution"></a>6,1: automatikus eszköz-felderítési megoldás használata

**Útmutató**: az Azure Portal vagy az Azure Resource Graph használatával felderítheti az összes erőforrást (nem csak a Azure Cosmos DBra, hanem a számítási, egyéb tárolási, hálózati, portok és protokollok stb.) az előfizetése (ke) n belül. Győződjön meg arról, hogy rendelkezik megfelelő engedélyekkel a bérlőben, és képes az összes Azure-előfizetés, valamint az előfizetésében lévő erőforrások számbavételére.

Bár a klasszikus Azure-erőforrások felderítése az erőforrás-gráfon keresztül lehetséges, erősen ajánlott a Azure Resource Manager erőforrások létrehozása és használata.

- [Lekérdezések létrehozása az Azure Resource Graph használatával](../governance/resource-graph/first-query-portal.md)

- [Azure-előfizetések megtekintése](/powershell/module/az.accounts/get-azsubscription?preserve-view=true&view=azps-4.8.0)

- [Az Azure szerepköralapú hozzáférés-vezérlésének ismertetése](../role-based-access-control/overview.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="62-maintain-asset-metadata"></a>6,2: az eszköz metaadatainak fenntartása

**Útmutató**: címkéket alkalmazhat a Azure Cosmos db példányaira és a kapcsolódó erőforrásokra metaadatokkal, hogy logikailag szervezze őket a besorolásba.

- [Címkék létrehozása és használata](../azure-resource-manager/management/tag-resources.md)

- [A címkéket támogató Azure Cosmos DB erőforrások](../azure-resource-manager/management/tag-support.md#microsoftdocumentdb)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="63-delete-unauthorized-azure-resources"></a>6,3: jogosulatlan Azure-erőforrások törlése

**Útmutató**: a címkézés, a felügyeleti csoportok és az elkülönített előfizetések használata, ha szükséges, az eszközök rendszerezéséhez és nyomon követéséhez, beleértve a Azure Cosmos db erőforrásokat is. Rendszeres időközönként egyeztetheti a leltárt, és gondoskodhat arról, hogy a jogosulatlan erőforrások törlése az előfizetésből időben történjen.

- [További Azure-előfizetések létrehozása](../cost-management-billing/manage/create-subscription.md)

- [Management Groups létrehozása](../governance/management-groups/create-management-group-portal.md)

- [Címkék létrehozása és használata](../azure-resource-manager/management/tag-resources.md)

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

- [Az Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md)

- [Adott erőforrástípus megtagadása a következővel Azure Policy](../governance/policy/samples/built-in-policies.md#general)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6,11: korlátozza a felhasználók képességét a Azure Resource Manager való interakcióra

**Útmutató**: az Azure feltételes hozzáférés használatával korlátozhatja, hogy a felhasználók képesek legyenek a Azure Resource Manager interakcióra az "Microsoft Azure felügyelet" alkalmazás "hozzáférés tiltása" beállításával. Ez megakadályozhatja az erőforrások létrehozását és módosítását a magas biztonsági környezeten belül.

- [A feltételes hozzáférés konfigurálása a Azure Resource Managerhoz való hozzáférés blokkolásához](../role-based-access-control/conditional-access-azure-management.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

## <a name="secure-configuration"></a>Biztonságos konfiguráció

*További információt az [Azure biztonsági teljesítményteszt: biztonságos konfiguráció](../security/benchmarks/security-control-secure-configuration.md)című témakörben talál.*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7,1: biztonságos konfigurációk létrehozása az összes Azure-erőforráshoz

**Útmutató**: a Cosmos db példányok szabványos biztonsági konfigurációinak meghatározása és implementálása Azure Policy használatával. Használjon Azure Policy aliasokat a **Microsoft.DocumentDB** névtérben egyéni szabályzatok létrehozásához az Cosmos db példányok konfigurációjának naplózásához vagy érvényesítéséhez. Használhatja a Azure Cosmos DB beépített szabályzat-definícióját is, például:
- A Cosmos DB-fiókok komplex veszélyforrások elleni védelmének üzembe helyezése
- Cosmos DB virtuális hálózati szolgáltatás végpontját kell használnia

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

**Útmutató**: ha a Cosmos DBhoz vagy a kapcsolódó erőforrásokhoz egyéni Azure Policy-definíciókat használ, az Azure Repos használatával tárolhatja és kezelheti az egyéni szabályzat-definíciókat kódként.

- [Szabályzat tervezése kódmunkafolyamatokként](../governance/policy/concepts/policy-as-code.md)

- [Az Azure Repos dokumentációja](/azure/devops/repos/git/gitworkflow)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7,7: az Azure-erőforrások konfigurációs felügyeleti eszközeinek üzembe helyezése

**Útmutató**: a "Microsoft.DocumentDB" névtérben található Azure Policy Aliasok használatával egyéni szabályzatokat hozhat létre a riasztáshoz, a naplózáshoz és a rendszerkonfigurációk kényszerítéséhez. Emellett dolgozzon ki egy folyamatot és egy folyamatot a házirend-kivételek kezeléséhez.

- [Az Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7,9: automatikus konfigurációs monitorozás megvalósítása Azure-erőforrásokhoz

**Útmutató**: a "Microsoft.DocumentDB" névtérben található Azure Policy Aliasok használatával egyéni szabályzatokat hozhat létre a riasztáshoz, a naplózáshoz és a rendszerkonfigurációk kényszerítéséhez. A Azure Cosmos DB-példányok és a kapcsolódó erőforrások konfigurációjának automatikus érvényesítéséhez használja a Azure Policy [audit], [megtagadás] és [üzembe helyezés ha nem létezik] lehetőséget. 

- [Az Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="711-manage-azure-secrets-securely"></a>7,11: az Azure-titkok biztonságos kezelése

**Útmutató**: az Azure Cosmos db-példányok elérésére használt Azure app Serviceon futó Azure-beli virtuális gépek vagy webalkalmazások esetén a Managed Service Identity a Azure Key Vault együtt használva egyszerűsítheti és biztonságossá teheti Azure Cosmos db titkos felügyeletét. Győződjön meg arról, Key Vault a Soft delete engedélyezve van.

- [Integráció az Azure felügyelt identitásokkal](../azure-app-configuration/howto-integrate-azure-managed-service-identity.md)

- [Key Vault létrehozása](../key-vault/secrets/quick-create-portal.md)

- [Hitelesítés Key Vault](../key-vault/general/authentication.md)

- [Key Vault hozzáférési szabályzatok kiosztása](../key-vault/general/assign-access-policy-portal.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="712-manage-identities-securely-and-automatically"></a>7,12: az identitások biztonságos és automatikus kezelése

**Útmutató**: az Azure Cosmos db-példányok elérésére használt Azure app Serviceon futó Azure-beli virtuális gépek vagy webalkalmazások esetén a Managed Service Identity a Azure Key Vault együtt használva egyszerűsítheti és biztonságossá teheti Azure Cosmos db titkos felügyeletét.

Felügyelt identitások használatával biztosíthatja az Azure-szolgáltatások automatikus felügyelt identitását Azure Active Directoryban (Azure AD). A felügyelt identitások lehetővé teszik bármely olyan szolgáltatás hitelesítését, amely támogatja az Azure AD-hitelesítést, beleértve a Key Vault is, a kódban szereplő hitelesítő adatok nélkül.

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

**Útmutató**: a Microsoft antimalware engedélyezve van az olyan mögöttes gazdagépen, amely támogatja az Azure-szolgáltatásokat (például az Azure CosmosDB-t), de nem fut az ügyfél tartalmán.

Az Ön felelőssége a nem számítási Azure-erőforrásokra feltöltött fájlok előzetes vizsgálata, beleértve a Azure Cosmos DBt is. A Microsoft nem fér hozzá az ügyféladatok eléréséhez, így az Ön nevében nem végezhet kártevő-ellenőrzéseket az ügyfél-tartalmakon.

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

## <a name="data-recovery"></a>Adat-helyreállítás

*További információt az [Azure biztonsági teljesítményteszt: adat-helyreállítás](../security/benchmarks/security-control-data-recovery.md)című témakörben talál.*

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: teljes rendszerbiztonsági mentés és minden ügyfél által felügyelt kulcs biztonsági mentése

**Útmutató**: a Azure Cosmos db rendszeres időközönként automatikusan biztonsági másolatot készít az adatairól. Ha a rendszer törli az adatbázist vagy a tárolót, egy támogatási jegyet is betölthet, vagy meghívja az Azure-támogatást az adatok automatikus online biztonsági mentésből való visszaállításához. Az Azure-támogatás csak olyan kiválasztott csomagokhoz érhető el, mint a standard, a fejlesztői és a magasabb szintű csomagok. A biztonsági mentés egy adott pillanatképének visszaállításához a Azure Cosmos DB megköveteli, hogy az adatok elérhetők legyenek a pillanatkép biztonsági mentési ciklusának időtartamára. 

Ha a Cosmos DB-példányok hitelesítő adatainak tárolására Key Vault használ, ügyeljen arra, hogy a kulcsok rendszeres automatikus biztonsági mentéseit tárolja.

- [Azure Cosmos DB automatikus biztonsági mentések ismertetése](online-backup-and-restore.md)

- [Az Azure Cosmos DB-ban tárolt adatvisszaállítás](./online-backup-and-restore.md)

- [Key Vault kulcsok biztonsági mentése](/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey)

**Felelősség**: Megosztott

**Azure Security Center figyelés**: nincs

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: az összes biztonsági másolat ellenőrzése, beleértve az ügyfél által felügyelt kulcsokat

**Útmutató**: Ha a rendszer törli az adatbázist vagy a tárolót, egy támogatási jegyet is megadhat, vagy az Azure támogatási szolgálatával visszaállíthatja az adatokat az automatikus online biztonsági mentésből. Az Azure-támogatás csak olyan kiválasztott csomagokhoz érhető el, mint a standard, a fejlesztői és a magasabb szintű csomagok. A biztonsági mentés egy adott pillanatképének visszaállításához a Azure Cosmos DB megköveteli, hogy az adatok elérhetők legyenek a pillanatkép biztonsági mentési ciklusának időtartamára.

A Azure Key Vaultban tárolt titkok visszaállításának tesztelése a PowerShell használatával. A Restore-AzureKeyVaultKey parancsmag egy kulcsot hoz létre a megadott kulcstartóban. Ez a kulcs a bemeneti fájlban lévő biztonsági másolati kulcs replikája, és az eredeti kulccsal megegyező névvel rendelkezik.

- [Azure Cosmos DB automatikus biztonsági mentések ismertetése](online-backup-and-restore.md)

- [Az Azure Cosmos DB-ban tárolt adatvisszaállítás](./online-backup-and-restore.md)

- [Azure Key Vault titkos kódok visszaállítása](/powershell/module/az.keyvault/restore-azkeyvaultkey?preserve-view=true&view=azps-4.8.0)

**Felelősség**: Megosztott

**Azure Security Center figyelés**: nincs

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: a biztonsági másolatok és az ügyfél által felügyelt kulcsok védelmének biztosítása

**Útmutató**: mivel a Cosmos DBban tárolt összes felhasználói adatok titkosítva maradnak a nyugalmi és a szállítási forgalomban, nem kell semmilyen műveletet végrehajtania. A másik lehetőség az, hogy a titkosítás nyugalmi állapotban van, alapértelmezés szerint "on". Nincsenek olyan vezérlők, amelyekkel ki-vagy bekapcsolhatja. A Azure Cosmos DB AES-256 titkosítást használ minden olyan régióban, ahol a fiók fut.

A kulcsok véletlen vagy rosszindulatú törléssel szembeni védelemmel való ellátásához engedélyezze a Key Vault Soft-Delete.

- [Az adattitkosítás ismertetése Azure Cosmos DB](database-encryption-at-rest.md)

- [A Soft-Delete engedélyezése Key Vault](../storage/blobs/soft-delete-blob-overview.md?tabs=azure-portal)

**Felelősség**: Megosztott

**Azure Security Center figyelés**: nincs

## <a name="incident-response"></a>Incidensmegoldás

*További információ: [Azure Security Benchmark: Incidensek kezelése](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10,1: incidens-válaszi útmutató létrehozása

**Útmutató**: Alakítson ki incidenskezelési útmutatót a vállalat számára. Gondoskodjon írásos incidenskezelési tervekről, amelyek definiálják az összes résztvevő szerepkörét, valamint az incidenskezelés fázisait az észleléstől az incidens utáni értékelésig.

- [A NIST számítógépes biztonsági incidensek kezelési útmutatóját is kihasználhatja a saját incidens-válasz tervének létrehozásával kapcsolatos támogatáshoz](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf)

- [Munkafolyamat-automatizálás konfigurálása Azure Security Centeron belül](../security-center/security-center-planning-and-operations-guide.md)

- [Útmutató a saját biztonsági incidensek megoldási folyamatának létrehozásához](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Microsoft Security Response Center – incidens anatómiája](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10,2: incidensek pontozásának és rangsorolási eljárásának létrehozása

**Útmutató**: a Security Center súlyosságot rendel az egyes riasztásokhoz, hogy a prioritások alapján ki lehessen deríteni, hogy mely riasztásokat kell először megvizsgálni. A súlyosság attól függ, hogy a Security Center hol található, vagy a riasztás kibocsátásához használt elemzést, valamint azt a megbízhatósági szintet, amelyet a riasztáshoz vezető tevékenység mögött rosszindulatú szándék jelent.

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
