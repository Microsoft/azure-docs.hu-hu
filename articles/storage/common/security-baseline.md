---
title: Azure-beli biztonsági alapkonfiguráció Azure Storage-hoz
description: Azure-beli biztonsági alapkonfiguráció Azure Storage-hoz
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 04/23/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 5367fbcb88e19253eead58d8f99933fdadc41898
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2021
ms.locfileid: "101714714"
---
# <a name="azure-security-baseline-for-azure-storage"></a>Azure-beli biztonsági alapkonfiguráció Azure Storage-hoz

Az Azure Storage-hoz készült Azure biztonsági alapkonfiguráció olyan javaslatokat tartalmaz, amelyek segítségével javíthatja az üzemelő példány biztonsági állapotát.

A szolgáltatás alapterve az [Azure Security Benchmark 1,0-es verziójából](../../security/benchmarks/overview.md)származik, amely javaslatokat tesz arra vonatkozóan, hogy miként védheti meg felhőalapú megoldásait az Azure-ban az ajánlott eljárásokat ismertető útmutató segítségével.

További információ: [Azure Security](../../security/benchmarks/security-baselines-overview.md)alapkonfigurációk áttekintése.

## <a name="network-security"></a>Hálózati biztonság

*További információkért lásd [: biztonsági ellenőrzés: hálózati biztonság](../../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1,1: az erőforrások védelme hálózati biztonsági csoportokkal vagy Azure Firewall a Virtual Network

**Útmutató**: konfigurálja a Storage-fiókja tűzfalát úgy, hogy korlátozza az ügyfelek hozzáférését adott nyilvános IP-címtartományok alapján, kiválaszthatja a virtuális hálózatokat (virtuális hálózatok) az Azure-ban, vagy adott Azure-erőforrásokhoz. A privát végpontokat úgy is konfigurálhatja, hogy a vállalati tárolási szolgáltatás felé irányuló forgalom kizárólag a magánhálózatok között legyen.

Megjegyzés: a klasszikus Storage-fiókok nem támogatják a tűzfalakat és a virtuális hálózatokat.

- [Az Azure Storage-tűzfal konfigurálása](./storage-network-security.md#change-the-default-network-access-rule)

- [Privát végpontok konfigurálása az Azure Storage-hoz](./storage-private-endpoints.md)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="12-monitor-and-log-vnet-subnet-and-nic-configuration-and-traffic"></a>1,2: figyelheti és naplózhatja a vnet, az alhálózatot és a hálózati adapterek konfigurációját és forgalmát

**Útmutató**: az Azure Storage többrétegű biztonsági modellt biztosít. A Storage-fiókhoz való hozzáférést korlátozhatja a megadott IP-címekről, IP-tartományokról vagy egy Azure-Virtual Network (VNet) lévő alhálózatok listájáról. Az Azure-ban a hálózati erőforrások biztonságossá tételéhez Azure Security Center és a hálózatvédelemre vonatkozó javaslatokat is követheti. Emellett engedélyezze a Storage-fiókok tűzfalán keresztül a Storage-fiókokhoz konfigurált virtuális hálózatokhoz/alhálózatokhoz tartozó NSG-naplókat, és küldje el a naplókat a forgalom naplózására szolgáló Storage-fiókba. 

Vegye figyelembe, hogy ha a Storage-fiókjához privát végpontok vannak csatlakoztatva, akkor nem konfigurálhatja az alhálózatok hálózati biztonsági csoport (NSG) szabályait. 

- [Azure Storage-tűzfalak és virtuális hálózatok konfigurálása](./storage-network-security.md)

- [A NSG folyamat naplófájljainak engedélyezése](../../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [A Azure Security Center által biztosított hálózati biztonság ismertetése](../../security-center/security-center-network-recommendations.md)

- [Privát végpontok megismerése az Azure Storage-ban](./storage-private-endpoints.md#known-issues)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="13-protect-critical-web-applications"></a>1,3: a kritikus webalkalmazások megóvása

**Útmutató**: nem alkalmazható; a javaslat a Azure App Service vagy számítási erőforrásokon futó webalkalmazásokhoz készült.

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1,4: az ismert kártékony IP-címekkel folytatott kommunikáció megtagadása

**Útmutató**: a komplex veszélyforrások elleni védelem engedélyezése az Azure Storage-fiókhoz. Az Azure Storage komplex veszélyforrások elleni védelme egy további biztonsági intelligenciát biztosít, amely szokatlan és potenciálisan ártalmas kísérleteket észlel a Storage-fiókok eléréséhez vagy kiaknázásához. Azure Security Center integrált riasztások olyan tevékenységeken alapulnak, amelyekhez a hálózati kommunikáció sikeresen megoldott IP-címmel van társítva, függetlenül attól, hogy az IP-cím egy ismert kockázatos IP-cím (például ismert cryptominer) vagy egy olyan IP-cím, amelyet korábban nem ismer fel kockázatos. A biztonsági riasztások akkor aktiválódnak, ha a tevékenységben rendellenességek történnek. 

- [A komplex veszélyforrások elleni védelem engedélyezése](./azure-defender-storage-configure.md?tabs=azure-portal)

- [Az Azure Security Center integrált veszélyforrások felderítésének megismerése](../../security-center/azure-defender.md)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="15-record-network-packets-and-flow-logs"></a>1,5: hálózati csomagok és adatforgalmi naplók rögzítése

**Útmutató**: Network Watcher a csomagok rögzítése lehetővé teszi rögzítési munkamenetek létrehozását a Storage-fiók és a virtuális gép közötti forgalom nyomon követéséhez. A rögzítési munkamenethez szűrőket biztosít, hogy csak a kívánt forgalmat rögzítsen. A csomagok rögzítése segít az újraaktív és proaktív hálózati rendellenességek diagnosztizálásában. Más felhasználások közé tartozik a hálózati statisztikák összegyűjtése, a hálózati behatolásokkal kapcsolatos információk beszerzése, az ügyfél-kiszolgáló kommunikáció hibakeresése és sok más. A csomagok rögzítésének távoli elindítása lehetővé teszi, hogy a csomagok rögzítése manuálisan fusson a kívánt virtuális gépen, ami értékes időt takarít meg. 

- [A csomagok rögzítésének kezelése az Azure Network Watcher a portál használatával](../../network-watcher/network-watcher-packet-capture-manage-portal.md)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems"></a>1,6: hálózati alapú behatolás-észlelési/Behatolás-megelőzési rendszerek üzembe helyezése

**Útmutató**: az Azure Storage komplex veszélyforrások elleni védelme további biztonsági intelligenciát biztosít, amely szokatlan és potenciálisan ártalmas kísérleteket észlel a Storage-fiókok eléréséhez vagy kiaknázásához. A biztonsági riasztások akkor aktiválódnak, ha a tevékenységben rendellenességek történnek. Ezek a biztonsági riasztások integrálva vannak az Azure Security Centerrel, és az előfizetési rendszergazdák e-mailben is értesítést kapnak róluk, a gyanús tevékenységek részleteivel és a fenyegetések kivizsgálására és javítására vonatkozó ajánlásokkal együtt. 

- [Komplex veszélyforrások elleni védelem konfigurálása az Azure Storage-hoz](./azure-defender-storage-configure.md)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="17-manage-traffic-to-your-web-applications"></a>1,7: a webes alkalmazások forgalmának kezelése

**Útmutató**: nem alkalmazható; a javaslat a Azure App Service vagy számítási erőforrásokon futó webalkalmazásokhoz készült.

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1,8: a hálózati biztonsági szabályok bonyolultságának és adminisztratív terhelésének csökkentése

**Útmutató**: a virtuális hálózatokban lévő, a Storage-fiókhoz hozzáféréssel rendelkező erőforrásokhoz használja a konfigurált virtuális hálózat Virtual Network szolgáltatás-címkéit a hálózati biztonsági csoportok vagy Azure Firewall hálózati hozzáférés-vezérlésének definiálásához. Biztonsági szabályok létrehozása során szolgáltatáscímkéket használhat bizonyos IP-címek helyett. A szolgáltatási címke nevének (például tárterületének) a szabály megfelelő forrás vagy cél mezőjében való megadásával engedélyezheti vagy megtagadhatja a megfelelő szolgáltatás forgalmát. A Microsoft kezeli a szolgáltatási címke által felölelt címek előtagjait, és automatikusan frissíti a szolgáltatási címkét a címek változásával. 

Ha a hálózati hozzáférés hatókörét meghatározott tárolási fiókokra kell korlátozni, használja a Virtual Network szolgáltatás-végponti házirendeket.

- [További információ a szolgáltatási címkék használatáról](../../virtual-network/service-tags-overview.md)

- [További információ az Azure Storage-hoz készült Virtual Network szolgáltatás végpont-házirendjeiről](../../virtual-network/virtual-network-service-endpoint-policies-overview.md)

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: szolgáltatás

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1,9: a hálózati eszközök szabványos biztonsági konfigurációinak fenntartása

**Útmutató**: az Azure Storage-fiókhoz társított hálózati erőforrásokra vonatkozó szabványos biztonsági konfigurációk definiálása és implementálása Azure Policy használatával. Használjon Azure Policy aliasokat a "Microsoft. Storage" és a "Microsoft. Network" névterekben, hogy egyéni szabályzatokat hozzon létre a Storage-fiók erőforrásainak hálózati konfigurációjának naplózásához vagy érvénybe léptetéséhez. 

A Storage-fiókhoz kapcsolódó beépített szabályzat-definíciókat is használhatja, például: a Storage-fiókoknak virtuális hálózati szolgáltatási végpontot kell használniuk. 

- [Az Azure Policy konfigurálása és kezelése](../../governance/policy/tutorials/create-and-manage.md)

- [Azure Policy minták a Storage-hoz](../../governance/policy/samples/built-in-policies.md#storage)

- [A hálózat Azure Policy mintái](../../governance/policy/samples/built-in-policies.md#network)

- [Azure Blueprint létrehozása](../../governance/blueprints/create-blueprint-portal.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="110-document-traffic-configuration-rules"></a>1,10: a dokumentum forgalmának konfigurációs szabályai

**Útmutató**: használja a hálózati biztonsági csoportokhoz (NSG) és a hálózati biztonsággal és a forgalommal kapcsolatos egyéb erőforrásokhoz tartozó címkéket. Az egyes NSG-szabályok esetében a "Leírás" mezővel adhatja meg az üzleti igényeket és/vagy időtartamot (stb.) minden olyan szabályhoz, amely engedélyezi a hálózatra irányuló adatforgalmat. A címkézéshez kapcsolódó beépített Azure Policy definíciók bármelyikét használhatja, például a "címke és az érték megkövetelése" beállítást, hogy az összes erőforrás címkével legyen létrehozva, és értesítse a meglévő címkézetlen erőforrásokról. A Azure PowerShell vagy az Azure CLI használatával a címkék alapján kereshet vagy végezhet műveleteket az erőforrásokon. 

- [Címkék létrehozása és használata](../../azure-resource-manager/management/tag-resources.md)

- [Virtual Network létrehozása](../../virtual-network/quick-create-portal.md)

- [NSG létrehozása biztonsági konfigurációval](../../virtual-network/tutorial-filter-network-traffic.md)

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1,11: automatikus eszközök használata a hálózati erőforrások konfigurációjának figyelésére és a változások észlelésére

**Útmutató**: a Azure Policy használatával naplózhatja a hálózati erőforrások konfigurációs változásait. Hozzon létre riasztásokat Azure Monitoron belül, amelyek akkor lépnek életbe, amikor a kritikus hálózati erőforrásokra vonatkozó módosításokat végrehajtják 

- [Az Azure Policy konfigurálása és kezelése](../../governance/policy/tutorials/create-and-manage.md)

- [Riasztások létrehozása a Azure Monitorban](../../azure-monitor/alerts/alerts-activity-log.md)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

## <a name="logging-and-monitoring"></a>Naplózás és monitorozás

*További információ [: Security Control: naplózás és figyelés](../../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="21-use-approved-time-synchronization-resource"></a>2,1: a jóváhagyott időszinkronizálási erőforrás használata

**Útmutató**: nem alkalmazható; A Microsoft fenntartja az Azure Storage-fiókok időforrásait.

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Microsoft

### <a name="22-configure-central-security-log-management"></a>2,2: a központi biztonsági naplók felügyeletének konfigurálása

**Útmutató**: naplók beolvasása Azure monitor használatával a végpontok eszközei, hálózati erőforrásai és más biztonsági rendszerek által generált biztonsági adatokat összesítve. A Azure Monitoron belül Log Analytics munkaterület (ek) használatával kérdezheti le és végezheti el az elemzéseket, és használhatja az Azure Storage-fiókokat a hosszú távú/archiválási tároláshoz, opcionálisan olyan biztonsági funkciókkal, mint például a megváltoztathatatlan tárolás és a kényszerített megőrzés.

- [Platform-naplók és-metrikák összegyűjtése Azure Monitor](../../azure-monitor/essentials/diagnostic-settings.md)

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="23-enable-audit-logging-for-azure-resources"></a>2,3: az Azure-erőforrások naplózásának engedélyezése

**Útmutató**: a Azure Storage Analytics a Blobok, várólisták és táblák naplóit biztosítja. A Azure Portal segítségével beállíthatja, hogy mely naplók legyenek rögzítve a fiókjához. 

- [Az Azure Storage-fiók figyelésének konfigurálása](./manage-storage-analytics-logs.md)

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="24-collect-security-logs-from-operating-systems"></a>2,4: biztonsági naplók gyűjtése az operációs rendszerekből

**Útmutató**: nem alkalmazható; Ez a javaslat számítási erőforrások számára készült.

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: nem alkalmazható

### <a name="25-configure-security-log-storage-retention"></a>2,5: a biztonsági napló tárolási adatmegőrzésének konfigurálása

**Útmutató**: Ha a biztonsági eseménynaplókat az Azure Storage-fiókban vagy log Analytics munkaterületen tárolja, az adatmegőrzési házirendet a szervezet követelményeinek megfelelően állíthatja be. 

- [Adatmegőrzési szabályzat konfigurálása az Azure Storage-fiók naplóihoz](./manage-storage-analytics-logs.md#configure-logging)

- [Az adatmegőrzési időszak módosítása Log Analytics](../../azure-monitor/logs/manage-cost-storage.md#change-the-data-retention-period)

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="26-monitor-and-review-logs"></a>2,6: naplók figyelése és áttekintése

**Útmutató**: az Azure Storage-naplók áttekintéséhez léteznek a szokásos lehetőségek, például a log Analytics ajánlaton keresztüli lekérdezések, valamint a naplófájlok közvetlen megtekintésének egyedi lehetősége. Az Azure Storage-ban a naplók olyan blobokban vannak tárolva, amelyeknek közvetlenül elérhetőnek kell lenniük `http://accountname.blob.core.windows.net/$logs` (a naplózási mappa alapértelmezés szerint rejtett, ezért közvetlenül kell navigálnia. Nem jelenik meg a lista parancsaiban) 

Emellett engedélyezze a komplex veszélyforrások elleni védelmet az Azure Storage-fiókhoz. Az Azure Storage komplex veszélyforrások elleni védelme egy további biztonsági intelligenciát biztosít, amely szokatlan és potenciálisan ártalmas kísérleteket észlel a Storage-fiókok eléréséhez vagy kiaknázásához. A biztonsági riasztások akkor aktiválódnak, ha a tevékenységben rendellenességek történnek. Ezek a biztonsági riasztások integrálva vannak az Azure Security Centerrel, és az előfizetési rendszergazdák e-mailben is értesítést kapnak róluk, a gyanús tevékenységek részleteivel és a fenyegetések kivizsgálására és javítására vonatkozó ajánlásokkal együtt. 

- [Az adatnapló és-áttekintés](./storage-analytics-logging.md#how-logs-are-stored)

- [A komplex veszélyforrások elleni védelem engedélyezése](./azure-defender-storage-configure.md?tabs=azure-portal)

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="27-enable-alerts-for-anomalous-activity"></a>2,7: riasztások engedélyezése rendellenes tevékenységhez

**Útmutató**: a Azure Security Center a komplex veszélyforrások elleni védelem engedélyezése a Storage-fiókhoz. Engedélyezze a Storage-fiók diagnosztikai beállításait, és küldje el a naplókat egy Log Analytics munkaterületre. A Log Analytics-munkaterületet az Azure Sentinelbe irányíthatja, mivel ez egy biztonsági előkészítési automatizált választ (felszárnyaló) megoldást biztosít. Ez lehetővé teszi a forgatókönyvek (automatizált megoldások) létrehozását és a biztonsági problémák megoldására való felhasználását. 

- [Az Azure Sentinel előkészítése](../../sentinel/quickstart-onboard.md)

- [Riasztások kezelése Azure Security Centerban](../../security-center/security-center-managing-and-responding-alerts.md)

- [Riasztás a log Analytics-naplófájlok adatkezeléséről](../../azure-monitor/alerts/tutorial-response.md)

- [Azure Storage Analytics-naplózás](./storage-analytics-logging.md)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="28-centralize-anti-malware-logging"></a>2,8: kártevő szoftverek közötti naplózás központosítása

**Útmutató**: az Azure Security Center használata és a veszélyforrások elleni védelem engedélyezése az Azure Storage-hoz az Azure Storage-ba való kártevő-feltöltések észleléséhez a kivonatoló hírnevének elemzése és a gyanús hozzáférés egy aktív Tor-kilépési csomópontból (anonimizálásával-proxy) 

- [Komplex veszélyforrások elleni védelem konfigurálása az Azure Storage-hoz](./azure-defender-storage-configure.md?tabs=azure-portal)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="29-enable-dns-query-logging"></a>2,9: DNS-lekérdezések naplózásának engedélyezése

**Útmutató**: Azure DNS Analytics (előzetes verzió) megoldás Azure monitor a DNS-infrastruktúrával kapcsolatos elemzéseket gyűjt a biztonság, a teljesítmény és a műveletek terén. Ez jelenleg nem támogatja az Azure Storage-fiókokat, azonban külső DNS-naplózási megoldást is használhat. 

- [A DNS-infrastruktúrával kapcsolatos információk gyűjtése a DNS Analytics előzetes verziójának megoldásával](../../azure-monitor/insights/dns-analytics.md)

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="210-enable-command-line-audit-logging"></a>2,10: parancssori naplózás engedélyezése

**Útmutató**: nem alkalmazható; a teljesítményteszt a számítási erőforrások számára készült.

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: nem alkalmazható

## <a name="identity-and-access-control"></a>Identitás- és hozzáférés-vezérlés

*További információ [: Security Control (identitás és Access Control](../../security/benchmarks/security-control-identity-access-control.md)).*

### <a name="31-maintain-inventory-of-administrative-accounts"></a>3,1: a rendszergazdai fiókok leltárának fenntartása

**Útmutató**: az Azure ad olyan beépített szerepkörökkel rendelkezik, amelyeket explicit módon kell hozzárendelni, és lekérdezhető. Az Azure AD PowerShell-modullal ad hoc lekérdezéseket hajthat végre a felügyeleti csoportok tagjait futtató fiókok felderítéséhez. 

- [Címtárbeli szerepkör beszerzése az Azure AD-ben a PowerShell-lel](/powershell/module/azuread/get-azureaddirectoryrole)

- [Címtárbeli szerepkör tagjainak beszerzése az Azure AD-ben a PowerShell-lel](/powershell/module/azuread/get-azureaddirectoryrolemember)

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="32-change-default-passwords-where-applicable"></a>3,2: az alapértelmezett jelszavak módosítása, ha alkalmazható

**Útmutató**: az Azure Storage-fiókok és a Azure Active Directory az alapértelmezett vagy az üres jelszavak fogalmával rendelkeznek. Az Azure Storage egy hozzáférés-vezérlési modellt valósít meg, amely támogatja az Azure szerepköralapú hozzáférés-vezérlést (Azure RBAC), valamint a megosztott kulcsokat és a közös hozzáférésű aláírásokat (SAS). A megosztott kulcs és az SAS hitelesítés jellemzője, hogy a hívó nem társít identitást, ezért nem hajtható végre a rendszerbiztonsági tag engedély-alapú engedélyezése. 

- [Az Azure Storage-beli adathozzáférés engedélyezése](./storage-auth.md)

- [A rendszerbiztonsági tag és az Azure Storage-fiók hozzáférés-vezérlésének ismertetése](./storage-introduction.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="33-use-dedicated-administrative-accounts"></a>3,3: dedikált rendszergazdai fiókok használata

**Útmutató**: szabványos működési eljárások létrehozása a Storage-fiókhoz hozzáféréssel rendelkező dedikált rendszergazdai fiókok használatával. A rendszergazdai fiókok számának figyeléséhez használja a Azure Security Center identitás-és hozzáférés-kezelés lehetőséget. 

A Microsoft-szolgáltatásokhoz és az Azure ARM-hoz Azure AD Privileged Identity Management Kiemelt szerepkörökkel is engedélyezheti az igény szerinti hozzáférést. 

- [Azure Security Center identitás és hozzáférés ismertetése](../../security-center/security-center-identity-access.md)

- [Privileged Identity Management áttekintése](../../active-directory/privileged-identity-management/index.yml)

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="34-use-azure-active-directory-single-sign-on-sso"></a>3,4: Azure Active Directory egyszeri Sign-On (SSO) használata

**Útmutató**: ha lehetséges, használja a Azure Active Directory SSO-t ahelyett, hogy egyéni, különálló hitelesítő adatokat konfiguráljon a szolgáltatáson kívül. Azure Security Center identitás-és hozzáférés-kezelési javaslatok használata. 

- [Az egyszeri bejelentkezés ismertetése az Azure AD-vel](../../active-directory/manage-apps/what-is-single-sign-on.md)

- [Az Azure Storage-beli adathozzáférés engedélyezése](./storage-auth.md)

- [Blobokhoz és várólistákhoz való hozzáférés engedélyezése Azure Active Directory használatával](./storage-auth-aad.md)

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="35-use-multifactor-authentication-for-all-azure-active-directory-based-access"></a>3,5: többtényezős hitelesítés használata az összes Azure Active Directory-alapú hozzáféréshez.

**Útmutató**: engedélyezze Azure Active Directory többtényezős hitelesítést, és kövesse Azure Security Center identitás-és hozzáférés-kezelési ajánlásokat a Storage-fiók erőforrásainak védelméhez. 

- [MFA engedélyezése az Azure-ban](../../active-directory/authentication/howto-mfa-getstarted.md)

- [Identitás és hozzáférés figyelése Azure Security Centeron belül](../../security-center/security-center-identity-access.md)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3,6: dedikált gépek (privilegizált hozzáférési munkaállomások) használata az összes felügyeleti feladathoz

**Útmutató**: a Storage-fiók erőforrásainak bejelentkezéséhez és konfigurálásához használja a mancsok (emelt szintű hozzáférésű munkaállomások) használatát az MFA használatára. 

- [További tudnivalók a privilegizált hozzáférésű munkaállomásokról](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

- [MFA engedélyezése az Azure-ban](../../active-directory/authentication/howto-mfa-getstarted.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="37-log-and-alert-on-suspicious-activity-from-administrative-accounts"></a>3,7: naplózás és riasztás a gyanús tevékenységekről a rendszergazdai fiókoktól

**Útmutatás**: Azure Security Center kockázatkezelési riasztások küldése Azure monitor és egyéni riasztások/értesítések konfigurálása a műveleti csoportokkal. Engedélyezze a komplex veszélyforrások elleni védelmet az Azure Storage-fiók számára a gyanús tevékenységekre vonatkozó riasztások létrehozásához. Emellett az Azure AD kockázati Észlelésekkel is megtekintheti a kockázatos felhasználói viselkedésre vonatkozó riasztásokat és jelentéseket. 

- [Komplex veszélyforrások elleni védelem beállítása az Azure Storage-fiókhoz](./azure-defender-storage-configure.md)

- [Az Azure AD kockázati észlelések ismertetése](../../active-directory/identity-protection/overview-identity-protection.md)

- [Műveleti csoportok konfigurálása egyéni riasztásokhoz és értesítésekhez](../../azure-monitor/alerts/action-groups.md)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="38-manage-azure-resource-from-only-approved-locations"></a>3,8: az Azure-erőforrások kezelése csak jóváhagyott helyekről

**Útmutató**: a feltételes hozzáférés elnevezett helyeivel engedélyezheti, hogy a hozzáférés csak az IP-címtartományok vagy országok/régiók adott logikai csoportjaihoz legyen elérhető. 

- [Elnevezett helyszínek konfigurálása az Azure-ban](../../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="39-use-azure-active-directory"></a>3,9: a Azure Active Directory használata

**Útmutató**: a Azure Active Directory (Azure ad) használata központi hitelesítési és engedélyezési rendszerrel. Az Azure Azure szerepköralapú hozzáférés-vezérlést (Azure RBAC) biztosít, amely részletesen szabályozza az ügyfelek hozzáférését egy Storage-fiók erőforrásaihoz.  Az Azure AD-hitelesítő adatok használata, ha lehetséges, biztonsági szempontból ajánlott a fiók kulcsa helyett, ami könnyebben sérülhet. Ha az alkalmazás kialakításához közös hozzáférésű aláírásokra van szükség a blob Storage-hoz való hozzáféréshez, az Azure AD hitelesítő adataival hozzon létre egy felhasználói delegálási közös hozzáférési aláírást (SAS), amikor lehetséges a kiváló biztonság.

- [Azure AD-példány létrehozása és konfigurálása](../../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

- [Az Azure Storage erőforrás-szolgáltató használata a kezelési erőforrások eléréséhez](./authorization-resource-provider.md)

- [Az Azure Blob-és üzenetsor-információhoz való hozzáférés konfigurálása az Azure RBAC Azure Portal](./storage-auth-aad-rbac-portal.md)

- [Az Azure Storage-beli adathozzáférés engedélyezése](./storage-auth.md)

- [Korlátozott hozzáférés biztosítása az Azure Storage-erőforrásokhoz közös hozzáférésű aláírások (SAS) használatával](./storage-sas-overview.md)

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="310-regularly-review-and-reconcile-user-access"></a>3,10: a felhasználói hozzáférés rendszeres áttekintése és egyeztetése

**Útmutató**: Tekintse át a Azure Active Directory naplókat, hogy segítsen felderíteni az elavult fiókokat, amelyek magukban foglalhatják a Storage-fiók felügyeleti szerepköreit. Emellett az Azure Identity Access Reviews használatával hatékonyan kezelhetők a csoporttagságok, hozzáférhetnek a vállalati alkalmazásokhoz, amelyek a Storage-fiók erőforrásainak eléréséhez és a szerepkör-hozzárendelésekhez használhatók. A felhasználók hozzáférését rendszeresen felül kell vizsgálni, hogy csak a megfelelő felhasználók férhessenek hozzájuk.  

A közös hozzáférésű aláírás (SAS) használatával biztonságos delegált hozzáférést biztosíthat a Storage-fiók erőforrásaihoz anélkül, hogy veszélyeztetné az adatai biztonságát. Megadhatja, hogy az ügyfél milyen erőforrásokhoz férhet hozzá, milyen engedélyekkel rendelkezik ezekhez az erőforrásokhoz, és hogy mennyi ideig érvényes az SAS érvényessége, más paraméterek között.

Tekintse át a névtelen olvasási hozzáférés tárolók és Blobok számára című ismertetőt is. Alapértelmezés szerint a tárolóhoz és az abban elhelyezkedő blobokhoz csak olyan felhasználó férhet hozzá, akinek megadta a megfelelő engedélyeket. A névtelen hitelesítési feltétellel a Azure Monitor használatával riasztást használhat a Storage-fiókok névtelen eléréséről.

A felhasználók számára biztosított hozzáférés időtartamának korlátozására az egyik leghatékonyabb módszer a nem gyanús felhasználói fiókok hozzáférésének kockázata. Az időkorlátos SAS URI-k egy hatékony módszer egy Storage-fiókhoz való felhasználói hozzáférés automatikus lejáratára. Emellett a Storage-fiókok kulcsai gyakoriak, így biztosítható, hogy a Storage-fiókok kulcsain keresztül történő váratlan hozzáférés korlátozott időtartamú legyen.

- [Az Azure AD jelentéskészítés ismertetése](../../active-directory/reports-monitoring/index.yml)

- [Hozzáférés megtekintése és módosítása az Azure Storage-fiók szintjén](./storage-auth-aad-rbac-portal.md)

- [Korlátozott hozzáférés biztosítása az Azure Storage-erőforrásokhoz közös hozzáférésű aláírások (SAS) használatával](./storage-sas-overview.md)

- [Tárolók és blobok névtelen olvasási hozzáférésének kezelése](../blobs/anonymous-read-access-configure.md)

- [Tárfiók monitorozása az Azure Portalon](./manage-storage-analytics-logs.md)

- [A Storage-fiók hozzáférési kulcsainak kezelése](./storage-account-keys-manage.md)

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="311-monitor-attempts-to-access-deactivated-accounts"></a>3,11: az inaktivált fiókok elérésére irányuló kísérletek figyelése

**Útmutató**: a Storage Analytics használatával naplózza a tárolási szolgáltatás sikeres és sikertelen kéréseinek részletes adatait. A rendszer az összes naplót blokkoló blobokban tárolja $logs nevű tárolóban, amely automatikusan létrejön, amikor a Storage Analytics engedélyezve van egy Storage-fiókhoz.

Diagnosztikai beállítások létrehozása Azure Active Directory felhasználói fiókok számára, a naplók és a bejelentkezési naplók elküldése egy Log Analytics-munkaterületre. Log Analytics munkaterületen belül konfigurálhatja a kívánt riasztásokat. Az Azure Storage-fiókokkal kapcsolatos hitelesítési hibák figyeléséhez riasztásokat hozhat létre, amelyekkel értesítheti, ha bizonyos küszöbértékek el lettek-e érni a tárolási erőforrások metrikái esetében. Emellett az Azure Monitor használatával a névtelen hitelesítési feltételt használó Storage-fiókok névtelen hozzáférésére vonatkozó riasztást is használhat.

- [Azure Storage Analytics-naplózás](./storage-analytics-logging.md)

- [Azure-tevékenységnaplók integrálása az Azure Monitorba](../../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

- [Metrikai riasztások konfigurálása az Azure Storage-fiókokhoz](./manage-storage-analytics-logs.md)

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="312-alert-on-account-login-behavior-deviation"></a>3,12: riasztás a fiók bejelentkezési viselkedésének eltérése esetén

**Útmutató**: a Azure Active Directory kockázatkezelési és identitás-védelmi funkcióinak használatával konfigurálhatja a tárolási fiók erőforrásaival kapcsolatos gyanús műveletekre vonatkozó automatizált válaszokat. A szervezet biztonsági válaszainak megvalósításához engedélyeznie kell az automatikus válaszokat az Azure Sentinel használatával. 

- [Az Azure AD kockázatos bejelentkezéseinek megtekintése](../../active-directory/identity-protection/overview-identity-protection.md)

- [Az Identity Protection kockázati házirendjeinek konfigurálása és engedélyezése](../../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [Az Azure Sentinel előkészítése](../../sentinel/quickstart-onboard.md)

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3,13: a Microsoft számára elérhetővé teszi a megfelelő ügyféladatokat a támogatási forgatókönyvek során

**Útmutató**: azon forgatókönyvek esetében, amelyekben a Microsoftnak hozzá kell férnie az ügyféladatok eléréséhez, Ügyfélszéf (a Storage-fiók előzetes verziója) olyan felületet biztosít az ügyfeleknek, amely áttekinti és jóváhagyja az ügyféladatok-hozzáférési kérelmeket. A Microsoft nem követeli meg, és nem kér hozzáférést a szervezetnek a Storage-fiókban tárolt titkos kulcsaihoz.

- [A Ügyfélszéf megismerése](../../security/fundamentals/customer-lockbox-overview.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

## <a name="data-protection"></a>Adatvédelem

*További információkért lásd [: biztonsági ellenőrzés:](../../security/benchmarks/security-control-data-protection.md)adatvédelem.*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4,1: bizalmas információk leltárának fenntartása

**Útmutató**: a címkék használatával segítséget nyújthat a bizalmas adatokat tároló vagy feldolgozó tárolási fiók erőforrásainak nyomon követésében. 

- [Címkék létrehozása és használata](../../azure-resource-manager/management/tag-resources.md)

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4,2: bizalmas adatok tárolására vagy feldolgozására szolgáló rendszerek elkülönítése

**Útmutató**: az elkülönítés megvalósítása különálló előfizetések, felügyeleti csoportok és Storage-fiókok használatával az egyes biztonsági tartományok, például a környezet, az adatérzékenység érdekében.  A Storage-fiók korlátozásával szabályozhatja, hogy az alkalmazások és a vállalati környezetek milyen mértékben férhetnek hozzá az alkalmazásokhoz és a vállalati környezetekhez, a használt hálózatok típusa és részhalmaza alapján. A hálózati szabályok konfigurálásakor csak a megadott hálózatokon adatokat kérő alkalmazások férhetnek hozzá egy Storage-fiókhoz. Az Azure Storage szolgáltatáshoz való hozzáférést az Azure RBAC keresztül szabályozhatja. A magánhálózati végpontokat úgy is konfigurálhatja, hogy a virtuális hálózat és a szolgáltatás közötti adatforgalom biztonságosabbá tételét a Microsoft gerinc hálózatán keresztül végezze el, így nem kell a nyilvános internetről bejárni. 

- [További Azure-előfizetések létrehozása](../../cost-management-billing/manage/create-subscription.md)

- [Management Groups létrehozása](../../governance/management-groups/create-management-group-portal.md)

- [Címkék létrehozása és használata](../../azure-resource-manager/management/tag-resources.md)

- [Azure Storage-tűzfalak és virtuális hálózatok konfigurálása](./storage-network-security.md)

- [Virtual Network szolgáltatási végpontok](../../virtual-network/virtual-network-service-endpoints-overview.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4,3: a bizalmas adatok jogosulatlan átvitelének figyelése és blokkolása.

**Útmutató**: a bizalmas adatok tárolására vagy feldolgozására szolgáló Storage-fiókok erőforrásai számára a címkék használatával bizalmasként jelölheti meg az erőforrásokat. A kiszűrése-on keresztüli adatvesztés kockázatának csökkentése érdekében a Azure Firewall használatával korlátozza az Azure Storage-fiókok kimenő hálózati forgalmát. 

Emellett a virtuális hálózati szolgáltatás végpont-házirendjeivel is szűrheti a kimenő virtuális hálózati forgalmat az Azure Storage-fiókokhoz a szolgáltatási végponton keresztül, és lehetővé teszi, hogy az adatok csak bizonyos Azure Storage-fiókokra kiszűrése.

- [Azure Storage-tűzfalak és virtuális hálózatok konfigurálása](../../virtual-network/virtual-network-service-endpoint-policies-overview.md)

- [Virtuális hálózati szolgáltatásvégpont-szabályzatok létrehozása az Azure Storage-hez](../../private-link/tutorial-private-endpoint-storage-portal.md)

- [Az ügyféladatok Azure-beli védelmének ismertetése](../../security/fundamentals/protection-customer-data.md)

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4,4: minden bizalmas adat titkosítása az átvitel során

**Útmutató**: a https használatát a Storage-fiókhoz szükséges biztonságos átvitel engedélyezésével kényszerítheti ki. A HTTP-t használó kapcsolatok ez követően el lesznek utasítva. Emellett Azure Security Center és Azure Policy használatával kényszerítheti ki a Storage-fiók biztonságos átvitelét.

- [Biztonságos átvitel megkövetelése az Azure Storage-ban](./storage-require-secure-transfer.md)

- [Security Center által figyelt Azure biztonsági szabályzatok](../../security-center/policy-reference.md)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Megosztott

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4,5: aktív felderítési eszköz használata a bizalmas adatok azonosítására

**Útmutató**: az adatazonosítási funkciók még nem érhetők el az Azure Storage-fiókhoz és a kapcsolódó erőforrásokhoz. Külső gyártótól származó megoldás implementálása, ha az szükséges a megfelelőség szempontjából. 

- [Az ügyféladatok Azure-beli védelmének ismertetése](../../security/fundamentals/protection-customer-data.md)

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4,6: az erőforrásokhoz való hozzáférés szabályozása az Azure RBAC

**Útmutató**: a Azure Active Directory (Azure ad) az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC) segítségével engedélyezi a hozzáférési jogokat a biztonságos erőforrásokhoz. Az Azure Storage egy beépített Azure-beli szerepkört határoz meg, amely magában foglalja a blob-vagy üzenetsor-adateléréshez használt engedélyek közös készleteit. 

- [Azure-szerepkörök kiosztása Azure Storage-fiókhoz](./storage-auth-aad-rbac-portal.md#assign-azure-roles-using-the-azure-portal)

- [Az Azure Storage erőforrás-szolgáltató használata a kezelési erőforrások eléréséhez](./authorization-resource-provider.md)

- [Az Azure Blob-és üzenetsor-információhoz való hozzáférés konfigurálása az Azure RBAC Azure Portal](./storage-auth-aad-rbac-portal.md)

- [HRE-példány létrehozása és konfigurálása](../../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

- [Az Azure Storage-beli adathozzáférés engedélyezése](./storage-auth.md)

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4,7: a gazdagép-alapú adatvesztés-megelőzés használata a hozzáférés-vezérlés kikényszeríthető

**Útmutató**: nem alkalmazható; Ez a javaslat számítási erőforrások számára készült.

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: nem alkalmazható

### <a name="48-encrypt-sensitive-information-at-rest"></a>4,8: bizalmas adatok titkosítása a nyugalmi állapotban

**Útmutató**: az Azure Storage-titkosítás minden Storage-fiók esetében engedélyezve van, és nem tiltható le. Az Azure Storage automatikusan titkosítja az adatait, amikor a felhőben is megmarad. Amikor beolvassa az Azure Storage adatait, azok titkosítását az Azure Storage oldja fel. Az Azure Storage-titkosítás lehetővé teszi, hogy az inaktív adatok védelme programkód módosítása nélkül történjen, vagy programkódot adjon hozzá bármely alkalmazáshoz. 

- [Az Azure Storage titkosításának megismerése a nyugalmi állapotban](./storage-service-encryption.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4,9: a kritikus Azure-erőforrások változásainak naplózása és riasztása

**Útmutató**: a Azure monitor és az Azure-tevékenység naplójának használata riasztások létrehozásához, amikor a módosítások a Storage-fiók erőforrásaira vonatkoznak. Az Azure Storage naplózását is engedélyezheti az Azure Storage-ba irányuló kérelmek engedélyezésének nyomon követése érdekében. A naplók azt jelzik, hogy egy kérelem névtelenül történt-e egy OAuth 2,0-token használatával, megosztott kulcs használatával vagy közös hozzáférésű aláírás (SAS) használatával. Emellett az Azure Monitor használatával a névtelen hitelesítési feltételt használó Storage-fiókok névtelen hozzáférésére vonatkozó riasztást is használhat.

- [Riasztások létrehozása az Azure-tevékenységek naplózási eseményeihez](../../azure-monitor/alerts/alerts-activity-log.md)

- [Azure Storage Analytics-naplózás](./storage-analytics-logging.md)

- [Metrikai riasztások konfigurálása az Azure Storage-fiókokhoz](./manage-storage-analytics-logs.md)

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

## <a name="vulnerability-management"></a>Biztonságirés-kezelés

*További információ [: Security Control: sebezhetőségi kezelés](../../security/benchmarks/security-control-vulnerability-management.md).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5,1: automatikus biztonsági rések vizsgálatára szolgáló eszközök futtatása

**Útmutató**: a Azure Security Center ajánlásainak követése a Storage-fiókok konfigurációjának folyamatos auditálásához és figyeléséhez. 

- [Biztonsági javaslatok – gyorsútmutató](../../security-center/recommendations-reference.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5,2: az operációs rendszer automatikus javításának felügyeleti megoldásának telepítése

**Útmutató**: nem alkalmazható; Ez a javaslat számítási erőforrások számára készült.

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: nem alkalmazható

### <a name="53-deploy-automated-third-party-software-patch-management-solution"></a>5,3: automatizált Third Party Software javítás-felügyeleti megoldás telepítése

**Útmutató**: nem alkalmazható; Ez a javaslat számítási erőforrások számára készült.

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: nem alkalmazható

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5,4: a biztonsági rések keresésének összehasonlítása

**Útmutató**: nem alkalmazható; A Microsoft a biztonsági rések kezelését a Storage-fiókokat támogató mögöttes rendszereken végzi.

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5,5: kockázatértékelési folyamat használatával rangsorolhatja a felderített biztonsági rések szervizelését.

**Útmutató**: a Azure Security Center által biztosított alapértelmezett kockázati minősítések (biztonságos pontszám) használata. 

- [A Azure Security Center biztonsági pontszámának megismerése](../../security-center/secure-score-security-controls.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

## <a name="inventory-and-asset-management"></a>Leltár-és eszközfelügyelet

*További információkért lásd [: biztonsági vezérlés: leltár és eszközkezelés](../../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-azure-asset-discovery"></a>6,1: az Azure Asset Discovery használata

**Útmutató**: az Azure Resource Graph használatával lekérdezheti és felderítheti az összes erőforrást (beleértve a Storage-fiókokat) az előfizetés (ok) n belül. Győződjön meg arról, hogy megfelelő (olvasási) engedélyekkel rendelkezik a bérlőben, és képes felsorolni az összes Azure-előfizetést, valamint az előfizetésében lévő erőforrásokat. 

- [Lekérdezések létrehozása az Azure Graph használatával](../../governance/resource-graph/first-query-portal.md)

- [Azure-előfizetések megtekintése](/powershell/module/az.accounts/get-azsubscription)

- [Az Azure RBAC ismertetése](../../role-based-access-control/overview.md)

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="62-maintain-asset-metadata"></a>6,2: az eszköz metaadatainak fenntartása

**Útmutató**: címkék alkalmazása a Storage-fiók erőforrásaihoz, amelyekkel a metaadatok logikailag rendezhetők a besorolásba. 

- [Címkék létrehozása és használata](../../azure-resource-manager/management/tag-resources.md)

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="63-delete-unauthorized-azure-resources"></a>6,3: jogosulatlan Azure-erőforrások törlése

**Útmutató**: címkézés, felügyeleti csoportok és külön előfizetések használata, ahol szükséges, a Storage-fiókok és a kapcsolódó erőforrások rendszerezése és nyomon követése. Rendszeres időközönként egyeztetheti a leltárt, és gondoskodhat arról, hogy a jogosulatlan erőforrások törlése az előfizetésből időben történjen. 

Emellett az Azure Storage komplex veszélyforrások elleni védelmének használatával észlelheti a jogosulatlan Azure-erőforrásokat. 

- [További Azure-előfizetések létrehozása](../../cost-management-billing/manage/create-subscription.md)

- [Management Groups létrehozása](../../governance/management-groups/create-management-group-portal.md)

- [Címkék létrehozása és használata](../../azure-resource-manager/management/tag-resources.md)

- [Komplex veszélyforrások elleni védelem konfigurálása az Azure Storage-hoz](./azure-defender-storage-configure.md?tabs=azure-portal)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="64-maintain-inventory-of-approved-azure-resources-and-software-titles"></a>6,4: a jóváhagyott Azure-erőforrások és-szoftverek leltárának karbantartása.

**Útmutató**: a szervezeti igényeknek megfelelően létre kell hoznia a jóváhagyott Azure-erőforrások leltárát. 


**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="65-monitor-for-unapproved-azure-resources"></a>6,5: a nem jóváhagyott Azure-erőforrások figyelése

**Útmutató**: a Azure Policy használatával korlátozásokat állíthat be az ügyfél-előfizetésekben létrehozható erőforrások típusára a következő beépített szabályzat-definíciók használatával: 

 - Nem engedélyezett erőforrástípusok 
 - Engedélyezett erőforrástípusok 

Emellett az Azure Resource Graph használatával lekérdezheti vagy felderítheti az előfizetésben (k) belüli erőforrásokat. Ez magas biztonsági alapú környezetekben, például a Storage-fiókokkal is segít. 

- [Az Azure Policy konfigurálása és kezelése](../../governance/policy/tutorials/create-and-manage.md)

- [Lekérdezések létrehozása az Azure Graph használatával](../../governance/resource-graph/first-query-portal.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6,6: a nem jóváhagyott szoftveralkalmazások figyelése a számítási erőforrásokon belül

**Útmutató**: nem alkalmazható; Ez a javaslat számítási erőforrások számára készült.

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: nem alkalmazható

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6,7: nem jóváhagyott Azure-erőforrások és szoftveralkalmazások eltávolítása

**Útmutató**: az ügyfél meggátolhatja az erőforrások létrehozását és használatát Azure Policy az ügyfél céges szabályzata által megkövetelt módon. 

- [Az Azure Policy konfigurálása és kezelése](../../governance/policy/tutorials/create-and-manage.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="68-use-only-approved-applications"></a>6,8: csak jóváhagyott alkalmazások használata

**Útmutató**: nem alkalmazható; Ez a javaslat számítási erőforrások számára készült.

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: nem alkalmazható

### <a name="69-use-only-approved-azure-services"></a>6,9: csak jóváhagyott Azure-szolgáltatások használata

**Útmutató**: a Azure Policy használatával korlátozásokat állíthat be az ügyfél-előfizetésekben létrehozható erőforrások típusára a következő beépített szabályzat-definíciók használatával: 

- Nem engedélyezett erőforrástípusok 
- Engedélyezett erőforrástípusok 

- [Az Azure Policy konfigurálása és kezelése](../../governance/policy/tutorials/create-and-manage.md)

- [Adott erőforrástípus megtagadása a következővel Azure Policy](../../governance/policy/samples/index.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="610-implement-approved-application-list"></a>6,10: jóváhagyott alkalmazások listájának implementálása

**Útmutató**: nem alkalmazható; Ez a javaslat számítási erőforrások számára készült.

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: nem alkalmazható

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager-via-scripts"></a>6,11: korlátozza a felhasználók azon képességét, hogy parancsfájlok használatával Azure Resource Manager kommunikálni

**Útmutató**: az Azure feltételes hozzáférés használatával korlátozhatja, hogy a felhasználók képesek legyenek a Azure Resource Manager interakcióra az "Microsoft Azure felügyelet" alkalmazás "hozzáférés tiltása" beállításával. Ez megakadályozhatja az erőforrások létrehozását és módosítását a magas biztonsági környezetben, például a Storage-fiókokkal. 

- [Feltételes hozzáférés konfigurálása az ARM-hozzáférés blokkolásához](../../role-based-access-control/conditional-access-azure-management.md)

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

*További információkért lásd [: biztonság-vezérlés: biztonságos konfiguráció](../../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7,1: biztonságos konfigurációk létrehozása az összes Azure-erőforráshoz

**Útmutató**: Azure Policy aliasok használata a "Microsoft. Storage" névtérben egyéni szabályzatok létrehozásához a Storage-fiókok példányainak konfigurálásához vagy érvényesítéséhez. Az Azure Storage-fiókokhoz beépített Azure Policy-definíciókat is használhat, például: 

Nem korlátozott hálózati hozzáférés naplózása a Storage-fiókokhoz  
Komplex veszélyforrások elleni védelem üzembe helyezése a Storage-fiókokon  
A Storage-fiókokat át kell telepíteni az új Azure Resource Manager erőforrásokra  
Engedélyezni kell a tárfiókokba történő biztonságos átvitelt  

A Azure Security Center javaslatainak használata biztonságos alapkonfigurációként a Storage-fiókok számára. 

- [Az elérhető Azure Policy aliasok megtekintése](/powershell/module/az.resources/get-azpolicyalias)

- [Az Azure Policy konfigurálása és kezelése](../../governance/policy/tutorials/create-and-manage.md)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="72-establish-secure-configurations-for-your-operating-system"></a>7,2: biztonságos konfigurációk létrehozása az operációs rendszerhez

**Útmutató**: nem alkalmazható; Ez a javaslat számítási erőforrások számára készült.

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: nem alkalmazható

### <a name="73-maintain-secure-configurations-for-all-azure-resources"></a>7,3: biztonságos konfigurációk karbantartása az összes Azure-erőforráshoz

**Útmutató**: használja a Azure Policy [megtagadás] és a [telepítés, ha nem létezik] lehetőséget a Storage-fiók erőforrásaiban található biztonságos beállítások betartatásához. 

- [Az Azure Policy konfigurálása és kezelése](../../governance/policy/tutorials/create-and-manage.md)

- [Azure Policy effektusok ismertetése](../../governance/policy/concepts/effects.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="74-maintain-secure-configurations-for-operating-systems"></a>7,4: biztonságos konfigurációk karbantartása operációs rendszerekhez

**Útmutató**: nem alkalmazható; Ez a javaslat számítási erőforrások számára készült.

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: nem alkalmazható

### <a name="75-securely-store-configuration-of-azure-resources"></a>7,5: az Azure-erőforrások biztonságos tárolása

**Útmutató**: az Azure Repos használatával biztonságosan tárolhatja és kezelheti a kódokat, például az egyéni Azure-szabályzatokat, Azure Resource Manager sablonokat, a kívánt állapot-konfigurációs parancsfájlokat stb. Az Azure DevOps felügyelt erőforrásainak eléréséhez engedélyeket adhat meg vagy tagadhat meg bizonyos felhasználók, beépített biztonsági csoportok vagy Azure Active Directory (Azure AD) által meghatározott csoportok számára, ha az integrálva van az Azure DevOps, vagy Active Directory, ha a TFS integrálva van.

- [Kód tárolása az Azure DevOps](/azure/devops/repos/git/gitworkflow)

- [Engedélyek és csoportok az Azure DevOps](/azure/devops/organizations/security/about-permissions)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="76-securely-store-custom-operating-system-images"></a>7,6: az egyéni operációsrendszer-lemezképek biztonságos tárolása

**Útmutató**: nem alkalmazható; Ez a javaslat számítási erőforrások számára készült.

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: nem alkalmazható

### <a name="77-deploy-system-configuration-management-tools"></a>7,7: rendszerkonfiguráció-felügyeleti eszközök telepítése

**Útmutató**: a Azure Policy kihasználása riasztásra, naplózásra és a Storage-fiók rendszerkonfigurációinak betartatására. Emellett dolgozzon ki egy folyamatot és egy folyamatot a házirend-kivételek kezeléséhez. 

- [Az Azure Policy konfigurálása és kezelése](../../governance/policy/tutorials/create-and-manage.md)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7,8: rendszerkonfiguráció-felügyeleti eszközök központi telepítése operációs rendszerekhez

**Útmutató**: nem alkalmazható; Ez a javaslat számítási erőforrások számára készült.

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: nem alkalmazható

### <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7,9: az Azure-szolgáltatások automatizált konfigurációs figyelésének megvalósítása

**Útmutató**: a Azure Security Center kihasználása az Azure Storage-fiók erőforrásaihoz tartozó alapkonfiguráció-ellenőrzések végrehajtásához. 

- [Javaslatok szervizelése Azure Security Center](../../security-center/security-center-remediate-recommendations.md)

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7,10: az operációs rendszerek automatikus konfiguráció-figyelésének megvalósítása

**Útmutató**: nem alkalmazható; Ez a javaslat számítási erőforrások számára készült.

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: nem alkalmazható

### <a name="711-securely-manage-azure-secrets"></a>7,11: biztonságos felügyelet az Azure Secrets-ben

**Útmutató**: az Azure Storage automatikusan titkosítja az adatait, amikor azt a felhőben megőrzi. Használhatja a Microsoft által felügyelt kulcsokat a Storage-fiók titkosításához, vagy a titkosítást a saját kulcsaival is kezelheti. Ha ügyfél által biztosított kulcsokat használ, a kulcsok biztonságos tárolásához használhatja a Azure Key Vault. 

Emellett a Storage-fiók kulcsainak gyakori elforgatásával korlátozhatja a Storage-fiókok kulcsainak elvesztését vagy közzétételét.

- [Inaktív adatok Azure Storage-titkosítása](./storage-service-encryption.md)

- [A Storage-fiók hozzáférési kulcsainak kezelése](./storage-account-keys-manage.md)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="712-securely-and-automatically-manage-identities"></a>7,12: az identitások biztonságos és automatikus kezelése

**Útmutató**: az Azure Storage-fiókokban lévő blobokhoz és várólistákhoz való hozzáférés engedélyezése Azure Active Directory és felügyelt identitásokkal. Az Azure Blob és a üzenetsor-tároló támogatja a Azure Active Directory (Azure AD) hitelesítést az Azure-erőforrások felügyelt identitásával. Az Azure-erőforrások felügyelt identitásai engedélyezhetik a blob-és üzenetsor-adatokhoz való hozzáférést az Azure-beli virtuális gépeken (VM-ben) futó alkalmazások Azure AD-beli hitelesítő adataival, a Function apps, a virtuálisgép-méretezési csoportokkal és más szolgáltatásokkal. Ha felügyelt identitásokat használ az Azure-erőforrásokhoz az Azure AD-hitelesítéssel együtt, elkerülheti a hitelesítő adatok tárolását a felhőben futó alkalmazásaival. 

- [Azure Blob-és üzenetsor-adatok hozzáférésének engedélyezése felügyelt identitás használatával](./storage-auth-aad-rbac-portal.md)

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="713-eliminate-unintended-credential-exposure"></a>7,13: a hitelesítő adatok nem szándékolt expozíciójának megszüntetése

**Útmutató**: hitelesítő adatok beolvasása a programkódon belül a hitelesítő adatok azonosításához. A Credential Scanner a felfedezett hitelesítő adatok biztonságosabb helyre, például az Azure Key Vaultba való áthelyezésére is javaslatot tesz. 

- [A hitelesítő adatok beolvasójának beállítása](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

## <a name="malware-defense"></a>Kártevők elleni védelem

*További információkért lásd [: biztonsági ellenőrzés: kártevők elleni védelem](../../security/benchmarks/security-control-malware-defense.md).*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8,1: központilag felügyelt kártevő szoftverek használata

**Útmutató**: nem alkalmazható; Ez a javaslat számítási erőforrások számára készült. A Microsoft kezeli a kártevő szoftvereket a mögöttes platformon.

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: nem alkalmazható

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8,2: a nem számítási Azure-erőforrásokra feltöltött fájlok előzetes vizsgálata

**Útmutató**: veszélyforrások elleni védelem használata az Azure Storage-ban a kártevők feltöltésének észlelése az Azure Storage-ban a kivonatok hírnevének elemzése és a gyanús hozzáférés egy aktív Tor-kilépési csomópontból (anonimizálásával-proxy). 

A kártevő szoftverek előzetes vizsgálatát is megteheti, mielőtt a nem számítási Azure-erőforrásokra, például a App Servicere, Data Lake Storage, Blob Storagere vagy a szervezet követelményeinek kielégítésére.

- [Komplex veszélyforrások elleni védelem konfigurálása az Azure Storage-hoz](./azure-defender-storage-configure.md?tabs=azure-portal)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8,3: a kártevő szoftverek és az aláírások frissítésének ellenőrzése

**Útmutató**: nem alkalmazható; Ez a javaslat számítási erőforrások számára készült. A Microsoft kezeli a kártevő szoftvereket a mögöttes platformon.

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: nem alkalmazható

## <a name="data-recovery"></a>Adat-helyreállítás

*További információkért lásd [: biztonsági ellenőrzés: adat-helyreállítás](../../security/benchmarks/security-control-data-recovery.md).*

### <a name="91-ensure-regular-automated-back-ups"></a>9,1: rendszeres automatizált biztonsági másolatok biztosítása

**Útmutató**: a Microsoft Azure Storage-fiókban lévő adatai mindig automatikusan replikálódnak a tartósság és a magas rendelkezésre állás biztosítása érdekében. Az Azure Storage átmásolja az adatait, hogy védve legyen a tervezett és nem tervezett eseményekkel szemben, beleértve az átmeneti hardverhiba, a hálózati vagy áramkimaradások, valamint a súlyos természeti katasztrófákat. Dönthet úgy is, hogy ugyanazon az adatközponton belül replikálja az adatait, többek között az azonos régión belüli és a földrajzilag elkülönített régiók között. 

Az Azure Automation szolgáltatással is engedélyezheti a Blobok rendszeres pillanatképeit.

- [Az Azure Storage-redundancia és a Service-Level-szerződések ismertetése](./storage-redundancy.md)

- [BLOB pillanatképének létrehozása](/rest/api/storageservices/creating-a-snapshot-of-a-blob)

- [Azure Automation áttekintése](../../automation/automation-intro.md)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: teljes rendszerbiztonsági mentés és biztonsági másolat készítése bármely ügyfél által felügyelt kulcsról

**Útmutató**: a Storage-fiók által támogatott szolgáltatásokból származó adatok biztonsági mentéséhez több módszer is rendelkezésre áll, például a azcopy vagy a külső gyártótól származó eszközök használatával. Az Azure Blob Storage nem módosítható tárolója lehetővé teszi, hogy a felhasználók üzleti szempontból kritikus fontosságú adatobjektumokat tároljanak egy FÉREGben (egyszer írható, olvasható) állapot. Ez az állapot a felhasználó által megadott intervallumban nem törölhető és nem módosítható adatvesztést tesz lehetővé.

- [Bevezetés az AzCopy használatába](./storage-use-azcopy-v10.md)

- [BLOB Storage-módosíthatatlansági szabályzatok beállítása és kezelése](../blobs/storage-blob-immutability-policies-manage.md?tabs=azure-portal)

Az ügyfél által felügyelt/biztosított kulcsok Azure Key Vault az Azure CLI vagy a PowerShell használatával is készíthetők. 

- [Key Vault-kulcsok biztonsági mentése az Azure-ban](/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: az összes biztonsági másolat ellenőrzése, beleértve az ügyfél által felügyelt kulcsokat

**Útmutató**: a Key Vault tanúsítványok, kulcsok, felügyelt tárolási fiókok és titkos kódok adatvisszaállításának rendszeres időközönkénti végrehajtása a következő PowerShell-parancsokkal: 

Restore-AzKeyVaultCertificate Restore-AzKeyVaultKey Restore-AzKeyVaultManagedStorageAccount Restore-AzKeyVaultSecret 

- [Key Vault tanúsítványok visszaállítása](/powershell/module/azurerm.keyvault/restore-azurekeyvaultcertificate)

- [Key Vault kulcsok visszaállítása](/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey)

- [Key Vault felügyelt Storage-fiókok visszaállítása](/powershell/module/az.keyvault/backup-azkeyvaultmanagedstorageaccount)

- [Key Vault titkos kódok visszaállítása](/powershell/module/azurerm.keyvault/restore-azurekeyvaultsecret)

- [A AzCopy egy parancssori segédprogram, amellyel blobokat, fájlokat és táblákat másolhat a Storage-fiókba, illetve onnan átmásolhatja azokat.](./storage-use-azcopy-v10.md)

Megjegyzés: Ha át szeretné másolni az adatait az Azure Table Storage szolgáltatásba, és onnan másolja, telepítse az AzCopy 7,3-es verzióját.


**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: a biztonsági másolatok és az ügyfél által felügyelt kulcsok védelmének biztosítása

**Útmutató**: Ha az ügyfél által felügyelt kulcsokat egy Storage-fiókban szeretné engedélyezni, Azure Key Vault kell használnia a kulcsok tárolásához. Engedélyeznie kell a Soft delete és a No Purge tulajdonságot a Key vaulton. Key Vault Soft delete funkciója lehetővé teszi a törölt tárolók és tároló objektumok, például kulcsok, titkok és tanúsítványok helyreállítását. Ha a Storage-fiókadatok Azure Storage-blobokra történő biztonsági mentését engedélyezi, a Soft delete lehetővé teszi az adatok mentését és helyreállítását a Blobok vagy blob-Pillanatképek törlésekor. A biztonsági mentéseket bizalmas adatokként kell kezelni, és az alapkonfiguráció részeként alkalmazni kell a megfelelő hozzáférési és adatvédelmi vezérlőket. Emellett a jobb védelem érdekében az üzleti szempontból kritikus fontosságú adatobjektumokat egy féreg (egyszer írható, olvasás) állapotba is tárolhatja.

- [A Azure Key Vault Soft delete használata](../../key-vault/general/key-vault-recovery.md)

- [Az Azure Storage-blobok helyreállítható törlése](../blobs/soft-delete-blob-overview.md?tabs=azure-portal)

- [Az üzlet szempontjából létfontosságú blobadatok tárolása nem módosítható tárolással](../blobs/storage-blob-immutable-storage.md)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

## <a name="incident-response"></a>Incidensmegoldás

*További információ [: Security Control: incidens válasza](../../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-incident-response-guide"></a>10,1: incidens-válasz létrehozási útmutatója

**Útmutató**: Alakítson ki incidenskezelési útmutatót a vállalat számára. Gondoskodjon írásos incidenskezelési tervekről, amelyek definiálják az összes résztvevő szerepkörét, valamint az incidenskezelés fázisait az észleléstől az incidens utáni értékelésig.

- [Útmutató a saját biztonsági incidensek megoldási folyamatának létrehozásához](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Microsoft Security Response Center – incidens anatómiája](https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/)

- [Az ügyfél a NIST számítógépes biztonsági incidensek kezelési útmutatóját is kihasználhatja a saját incidens-válasz tervének létrehozásával kapcsolatos támogatáshoz](https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="102-create-incident-scoring-and-prioritization-procedure"></a>10,2: incidens-pontozási és rangsorolási eljárás létrehozása

**Útmutató**: a Security Center súlyosságot rendel az egyes riasztásokhoz, hogy a prioritások alapján ki lehessen deríteni, hogy mely riasztásokat kell először megvizsgálni. A súlyosság azon alapul, hogy az Security Center milyen mértékben szerepel a riasztás kibocsátásához használt elemzésben vagy elemzésben, valamint azt a megbízhatósági szintet, amely a riasztáshoz vezető tevékenység mögött rosszindulatú szándékú volt. 

Emellett egyértelműen megjelölheti az előfizetéseket (pl.: éles környezetben, nem gyártva) címkék használatával és elnevezési rendszer létrehozása az Azure-erőforrások egyértelmű azonosításához és kategorizálásához, különösen a bizalmas adatok feldolgozásához. Az Ön felelőssége, hogy rangsorolja a riasztások megoldását azon Azure-erőforrások és -környezetek kritikussága alapján, ahol az incidens történt.

- [Biztonsági riasztások az Azure Security Centerben](../../security-center/security-center-alerts-overview.md)

- [Címkék használata az Azure-erőforrások rendszerezéséhez](../../azure-resource-manager/management/tag-resources.md)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="103-test-security-response-procedures"></a>10,3: biztonsági reagálási eljárások tesztelése

**Útmutató**: az Azure-erőforrások védelmének biztosítása érdekében a rendszer az incidensek reagálási képességeinek rendszeres tesztelésére szolgáló gyakorlatokat hajt végre. Azonosítsa a gyenge pontokat és réseket, és szükség esetén dolgozza át a tervet.

- [A NIST kiadványa – útmutató az IT-csomagok és-képességek teszteléséhez, betanításához és alkalmazásához](https://csrc.nist.gov/publications/detail/sp/800-84/final)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Ügyfél

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10,4: biztonsági incidensek elérhetőségének biztosítása és riasztási értesítések konfigurálása biztonsági incidensekhez

**Útmutató**: a Microsoft a biztonsági incidensek elérhetőségi adatait arra használja fel, hogy felvegye Önnel a kapcsolatot, ha a Microsoft Security Response Center (MSRC) felfedi, hogy az adatokat egy törvénytelen vagy jogosulatlan fél is hozzáférte. A problémák megoldása érdekében tekintse át az incidenseket a tény után.

- [A Azure Security Center biztonsági kapcsolattartó beállítása](../../security-center/security-center-provide-security-contact-details.md)

**Az Azure Security Center monitorozása**: Igen

**Felelősség**: Ügyfél

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10,5: biztonsági riasztások beépítése az incidensek gyorsreagáló rendszerébe

**Útmutató**: az Azure Security Center-riasztások és javaslatok exportálása a folyamatos exportálás funkcióval az Azure-erőforrásokkal kapcsolatos kockázatok azonosítása érdekében. A folyamatos exportálás lehetővé teszi a riasztások és javaslatok manuális és folyamatos exportálását. Az Azure Security Center adatösszekötővel továbbíthatja a riasztásokat az Azure Sentinel szolgáltatásba.

- [Folyamatos exportálás konfigurálása](../../security-center/continuous-export.md)

- [Riasztások streamelése az Azure Sentinelbe](../../sentinel/connect-azure-security-center.md)

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="106-automate-the-response-to-security-alerts"></a>10,6: a biztonsági riasztásokra adott válasz automatizálása

**Útmutató**: a Azure Security Center munkafolyamat-automatizálási funkciója segítségével automatikusan elindíthatja a válaszokat az "Logic apps" használatával a biztonsági riasztások és az Azure-erőforrások védelme érdekében javasolt javaslatok alapján.

- [A Munkafolyamat-automatizálás és a Logic Apps konfigurálása](../../security-center/workflow-automation.md)

**Az Azure Security Center monitorozása**: Jelenleg nem érhető el

**Felelősség**: Ügyfél

## <a name="penetration-tests-and-red-team-exercises"></a>Behatolási tesztek és Red Team-gyakorlatok

*További információkért lásd [: biztonsági ellenőrzés: behatolási tesztek és Red Team-gyakorlatok](../../security/benchmarks/security-control-penetration-tests-red-team-exercises.md).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources"></a>11,1: az Azure-erőforrások rendszeres behatolásának tesztelése

**Útmutató**: kövesse a Microsoft részvételi szabályait, hogy a behatolási tesztek ne sértsék a Microsoft-házirendeket. A Microsoft által felügyelt felhőalapú infrastruktúrával, szolgáltatásokkal és alkalmazásokkal kapcsolatban a Microsoft stratégiáját és a Red Teaming és a Live site penetráció tesztelését is használhatja.

- [Behatolástesztelési beavatkozási szabályok](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)

- [Riasztási tesztek a Microsoft-felhőben](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Azure Security Center – monitorozás**: Nem értelmezhető

**Felelősség**: Megosztott

## <a name="next-steps"></a>Következő lépések

- Lásd az [Azure biztonsági teljesítménytesztét](../../security/benchmarks/overview.md)
- További információ az [Azure biztonsági Alaptervekről](../../security/benchmarks/security-baselines-overview.md)
