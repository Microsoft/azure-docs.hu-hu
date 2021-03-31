---
title: Azure-beli biztonsági alapkonfiguráció Azure Storage-hoz
description: Az Azure Storage biztonsági alapterve az Azure biztonsági Teljesítménytesztben meghatározott biztonsági javaslatok megvalósítására szolgáló eljárási útmutatást és erőforrásokat biztosít.
author: msmbaldwin
ms.service: storage
ms.topic: conceptual
ms.date: 03/16/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 56f340a8d64346fd8934e9cfbae26079d4ee7f29
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "104576551"
---
# <a name="azure-security-baseline-for-azure-storage"></a>Azure-beli biztonsági alapkonfiguráció Azure Storage-hoz

Ez a biztonsági alapterv az [Azure biztonsági teljesítményteszt 1,0-es verziójáról](../../security/benchmarks/overview-v1.md) az Azure Storage-ra vonatkozó útmutatást alkalmazza. Az Azure Security Benchmark ajánlásokat ad arra nézve, hogy hogyan tehetők biztonságossá a felhőalapú megoldások az Azure-ban.
A tartalom az Azure biztonsági teljesítményteszt által meghatározott **biztonsági vezérlők** és az Azure Storage-ra vonatkozó kapcsolódó útmutatás szerint van csoportosítva. Az Azure Storage-ra nem alkalmazható **vezérlők** ki lettek zárva.

 
Ha szeretné megtudni, hogy az Azure Storage hogyan teljes mértékben leképezi az Azure biztonsági Teljesítménytesztét, tekintse meg az [Azure Storage biztonsági alapkonfiguráció-leképezési fájlját](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)

## <a name="network-security"></a>Hálózati biztonság

*További információ: [Azure Security Benchmark: Hálózati biztonság](../../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1,1: Azure-erőforrások biztosítása virtuális hálózatokon belül

**Útmutató**: konfigurálja a Storage-fiók tűzfalát úgy, hogy korlátozza az ügyfelek hozzáférését adott nyilvános IP-címtartományok alapján, válassza a virtuális hálózatok vagy az adott Azure-erőforrások lehetőséget.  A privát végpontokat úgy is konfigurálhatja, hogy a vállalati tárolási szolgáltatás felé irányuló forgalom kizárólag a magánhálózatok között legyen.

Megjegyzés: a klasszikus Storage-fiókok nem támogatják a tűzfalakat és a virtuális hálózatokat.

- [Az Azure Storage-tűzfal konfigurálása](https://docs.microsoft.com/azure/storage/common/storage-network-security#change-the-default-network-access-rule)

- [Privát végpontok konfigurálása az Azure Storage-hoz](storage-private-endpoints.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás**: az [Azure biztonsági teljesítményteszt](/home/mbaldwin/docs/asb/azure-docs-pr/articles/governance/policy/samples/azure-security-benchmark.md) a Security Center alapértelmezett házirend-kezdeményezése, és a [Security Center ajánlásainak](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/security-center-recommendations.md)alapja. A vezérlőhöz kapcsolódó Azure Policy-definíciók Security Center automatikusan engedélyezve vannak. Az ehhez a vezérlőhöz kapcsolódó riasztásokhoz szükség lehet egy [Azure Defender](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/azure-defender.md) -csomagra a kapcsolódó szolgáltatásokhoz.

**Beépített definíciók Azure Policy – Microsoft. Storage**:

[!INCLUDE [Resource Policy for Microsoft.Storage 1.1](../../../includes/policy/standards/asb/rp-controls/microsoft.storage-1-1.md)]

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-network-interfaces"></a>1,2: a virtuális hálózatok, alhálózatok és hálózati adapterek konfigurációjának és forgalmának figyelése és naplózása

**Útmutató**: az Azure Storage többrétegű biztonsági modellt biztosít. A Storage-fiókhoz való hozzáférést korlátozhatja a megadott IP-címekről, IP-tartományokról vagy az Azure-Virtual Network alhálózatok listájáról érkező kérelmekre. Az Azure-ban a hálózati erőforrások biztonságossá tételéhez Azure Security Center és a hálózatvédelemre vonatkozó javaslatokat is követheti. Emellett engedélyezze a hálózati biztonsági csoportok adatfolyam-naplóit a Storage-fiók tűzfala szolgáltatással konfigurált virtuális hálózatok vagy alhálózatok számára, és küldje el a naplókat a forgalom naplózásához a Storage-fiókba. 

 
Vegye figyelembe, hogy ha a Storage-fiókjához privát végpontok vannak csatlakoztatva, akkor nem konfigurálhatja az alhálózatok hálózati biztonsági csoportjának szabályait. 
 

 
- [Azure Storage-tűzfalak és virtuális hálózatok konfigurálása](storage-network-security.md) 
 

 
- [A NSG folyamat naplófájljainak engedélyezése](../../network-watcher/network-watcher-nsg-flow-logging-portal.md) 
 

 
- [A Azure Security Center által biztosított hálózati biztonság ismertetése](../../security-center/security-center-network-recommendations.md) 
 

 
- [Privát végpontok megismerése az Azure Storage-ban](https://docs.microsoft.com/azure/storage/common/storage-private-endpoints#known-issues)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="13-protect-critical-web-applications"></a>1,3: a kritikus webalkalmazások megóvása

**Útmutató**: nem alkalmazható; a javaslat a Azure App Service vagy számítási erőforrásokon futó webalkalmazásokhoz készült.

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1,4: az ismert kártevő IP-címekkel folytatott kommunikáció megtagadása

**Útmutató**: az Azure Defender for Storage engedélyezése az Azure Storage-fiókhoz. Az Azure Defender for Storage a biztonsági intelligencia újabb rétegét kínálja, amely észleli a tárfiókok elérésére vagy kihasználására tett szokatlan és esetleg kártékony próbálkozásokat.  Azure Security Center integrált riasztások olyan tevékenységeken alapulnak, amelyekhez a hálózati kommunikáció sikeresen megoldott IP-címmel van társítva, függetlenül attól, hogy az IP-cím egy ismert kockázatos IP-cím (például ismert cryptominer) vagy egy olyan IP-cím, amelyet korábban nem ismer fel kockázatos. A biztonsági riasztások akkor aktiválódnak, ha a tevékenységben rendellenességek történnek. 

- [Az Azure Defender tároláshoz konfigurálása](azure-defender-storage-configure.md) 

- [Az Azure Security Center integrált veszélyforrások felderítésének megismerése](../../security-center/azure-defender.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="15-record-network-packets"></a>1,5: hálózati csomagok rögzítése

**Útmutató**: Network Watcher a csomagok rögzítése lehetővé teszi rögzítési munkamenetek létrehozását a Storage-fiók és a virtuális gép közötti forgalom nyomon követéséhez. A rögzítési munkamenethez szűrőket biztosít, hogy csak a kívánt forgalmat rögzítsen. A csomagok rögzítése segít az újraaktív és proaktív hálózati rendellenességek diagnosztizálásában. Más felhasználások közé tartozik a hálózati statisztikák összegyűjtése, a hálózati behatolásokkal kapcsolatos információk beszerzése, az ügyfél-kiszolgáló kommunikáció hibakeresése és sok más. A csomagok rögzítésének távoli elindítása lehetővé teszi, hogy a csomagok rögzítése manuálisan fusson a kívánt virtuális gépen, ami értékes időt takarít meg. 

- [ A csomagok rögzítésének kezelése az Azure Network Watcher a portál használatával](../../network-watcher/network-watcher-packet-capture-manage-portal.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1,6: hálózati behatolás-észlelési/Behatolás-megelőzési rendszerek (AZONOSÍTÓk/IP-címek) üzembe helyezése

**Útmutató**: az Azure Defender for Storage egy további biztonsági intelligenciát biztosít, amely szokatlan és potenciálisan ártalmas kísérleteket észlel a Storage-fiókok eléréséhez vagy kiaknázásához. A biztonsági riasztások akkor aktiválódnak, ha a tevékenységben rendellenességek történnek. Ezek a biztonsági riasztások integrálva vannak az Azure Security Centerrel, és az előfizetési rendszergazdák e-mailben is értesítést kapnak róluk, a gyanús tevékenységek részleteivel és a fenyegetések kivizsgálására és javítására vonatkozó ajánlásokkal együtt. 

- [Az Azure Defender tároláshoz konfigurálása](https://docs.microsoft.com/azure/storage/common/azure-defender-storage-configure?tabs=azure-security-center)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="17-manage-traffic-to-web-applications"></a>1,7: webalkalmazások forgalmának kezelése

**Útmutató**: nem alkalmazható; a javaslat a Azure App Service vagy számítási erőforrásokon futó webalkalmazásokhoz készült.

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1,8: a hálózati biztonsági szabályok bonyolultságának és adminisztratív terhelésének csökkentése

**Útmutató**: a virtuális hálózatokban lévő, a Storage-fiókhoz hozzáféréssel rendelkező erőforrásokhoz használja Virtual Network szolgáltatás címkéit a konfigurált Virtual Network számára a hálózati biztonsági csoportok vagy Azure Firewall hálózati hozzáférés-vezérlésének definiálásához. Biztonsági szabályok létrehozása során szolgáltatáscímkéket használhat bizonyos IP-címek helyett. A szolgáltatási címke nevének (például a tárolónak) a szabály megfelelő forrás vagy cél mezőjében való megadásával engedélyezheti vagy megtagadhatja a megfelelő szolgáltatás forgalmát. A Microsoft kezeli a szolgáltatási címke által felölelt címek előtagjait, és automatikusan frissíti a szolgáltatási címkét a címek változásával. 

Ha a hálózati hozzáférés hatókörét meghatározott tárolási fiókokra kell korlátozni, használja a Virtual Network szolgáltatás-végponti házirendeket.

- [További információ a szolgáltatási címkék használatáról](../../virtual-network/service-tags-overview.md)

- [További információ az Azure Storage-hoz készült Virtual Network szolgáltatás végpont-házirendjeiről](../../virtual-network/virtual-network-service-endpoint-policies-overview.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1,9: a hálózati eszközök szabványos biztonsági konfigurációinak fenntartása

**Útmutató**: az Azure Storage-fiókhoz társított hálózati erőforrásokra vonatkozó szabványos biztonsági konfigurációk definiálása és implementálása Azure Policy használatával. Használjon Azure Policy aliasokat a "Microsoft. Storage" és a "Microsoft. Network" névterekben, hogy egyéni szabályzatokat hozzon létre a Storage-fiók erőforrásainak hálózati konfigurációjának naplózásához vagy érvénybe léptetéséhez. 

A Storage-fiókhoz kapcsolódó beépített szabályzat-definíciókat is használhatja, például: a Storage-fiókoknak virtuális hálózati szolgáltatási végpontot kell használniuk. 

- [Az Azure Policy konfigurálása és kezelése](../../governance/policy/tutorials/create-and-manage.md) 

- [Azure Policy minták a Storage-hoz](https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#storage) 

- [A hálózat Azure Policy mintái](https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#network) 

- [Azure Blueprint létrehozása](../../governance/blueprints/create-blueprint-portal.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="110-document-traffic-configuration-rules"></a>1,10: a dokumentum forgalmának konfigurációs szabályai

**Útmutató**: hálózati biztonsági csoportokhoz és a hálózati biztonsággal és a forgalommal kapcsolatos egyéb erőforrásokhoz használható címkék használata. A címkézés lehetővé teszi, hogy a beépített és az egyéni név-érték párokat egy adott hálózati erőforráshoz társítsa, így segít a hálózati erőforrások rendszerezésében és az Azure-erőforrások visszakapcsolásában a hálózat kialakításához.

A címkézéshez kapcsolódó beépített Azure Policy definíciók bármelyikét használhatja, például a "címke és az érték megkövetelése" beállítást, hogy az összes erőforrás címkével legyen létrehozva, és értesítse a meglévő címkézetlen erőforrásokról. 

A hálózati biztonsági csoportok támogatják a címkéket, de az egyes biztonsági szabályok nem. A biztonsági szabályok olyan **leírási** mezővel rendelkeznek, amelyet a szokásos módon a címkébe helyezett információk tárolására használhat.

- [Erőforrás-Címkék létrehozása és használata](../../azure-resource-manager/management/tag-resources.md)

- [Hálózati adatforgalom szűrése hálózati biztonsági csoporttal](../../virtual-network/tutorial-filter-network-traffic.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1,11: automatikus eszközök használata a hálózati erőforrások konfigurációjának figyelésére és a változások észlelésére

**Útmutató**: a Azure Policy használatával naplózhatja a hálózati erőforrások konfigurációs változásait.  Hozzon létre riasztásokat Azure Monitoron belül, amelyek akkor lépnek életbe, amikor a kritikus hálózati erőforrásokra vonatkozó módosításokat végrehajtják  

 
- [Az Azure Policy konfigurálása és kezelése](../../governance/policy/tutorials/create-and-manage.md)
 
 
- [Riasztások létrehozása a Azure Monitorban](../../azure-monitor/alerts/alerts-activity-log.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

## <a name="logging-and-monitoring"></a>Naplózás és monitorozás

*További információt az [Azure biztonsági teljesítményteszt: naplózás és figyelés](../../security/benchmarks/security-control-logging-monitoring.md)című témakörben talál.*

### <a name="22-configure-central-security-log-management"></a>2,2: a központi biztonsági naplók felügyeletének konfigurálása

**Útmutató**: naplók beolvasása Azure monitor használatával a végpontok eszközei, hálózati erőforrásai és más biztonsági rendszerek által generált biztonsági adatokat összesítve. A Azure Monitoron belül Log Analytics munkaterület (ek) használatával kérdezheti le és végezheti el az elemzéseket, és használhatja az Azure Storage-fiókokat a hosszú távú/archiválási tároláshoz, opcionálisan olyan biztonsági funkciókkal, mint például a megváltoztathatatlan tárolás és a kényszerített megőrzés.

 
- [Platform-naplók és-metrikák összegyűjtése Azure Monitor](../../azure-monitor/essentials/diagnostic-settings.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="23-enable-audit-logging-for-azure-resources"></a>2,3: az Azure-erőforrások naplózásának engedélyezése

**Útmutató**: a Azure Storage Analytics a Blobok, várólisták és táblák naplóit biztosítja. A Azure Portal segítségével beállíthatja, hogy mely naplók legyenek rögzítve a fiókjához.   

 
- [Az Azure Storage-fiók figyelésének konfigurálása](manage-storage-analytics-logs.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="25-configure-security-log-storage-retention"></a>2,5: a biztonsági napló tárolási adatmegőrzésének konfigurálása

**Útmutató**: Ha a biztonsági eseménynaplókat az Azure Storage-fiókban vagy log Analytics munkaterületen tárolja, az adatmegőrzési házirendet a szervezet követelményeinek megfelelően állíthatja be.  

 
- [Adatmegőrzési szabályzat konfigurálása az Azure Storage-fiók naplóihoz](https://docs.microsoft.com/azure/storage/common/manage-storage-analytics-logs#configure-logging) 

 
- [Az adatmegőrzési időszak módosítása Log Analytics](https://docs.microsoft.com/azure/azure-monitor/logs/manage-cost-storage#change-the-data-retention-period)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="26-monitor-and-review-logs"></a>2,6: naplók figyelése és áttekintése

**Útmutató**: az Azure Storage-naplók áttekintéséhez léteznek a szokásos lehetőségek, például a log Analytics ajánlaton keresztüli lekérdezések, valamint a naplófájlok közvetlen megtekintésének egyedi lehetősége. Az Azure Storage-ban a naplók olyan blobokban vannak tárolva, amelyeknek közvetlenül elérhetőnek kell lenniük `http://accountname.blob.core.windows.net/$logs` (a naplózási mappa alapértelmezés szerint rejtett, ezért közvetlenül kell navigálnia. Nem jelenik meg a lista parancsaiban) 

 
Engedélyezze az Azure Defender számára a Storage-fiókhoz való tárolást is. Az Azure Defender for Storage a biztonsági intelligencia újabb rétegét kínálja, amely észleli a tárfiókok elérésére vagy kihasználására tett szokatlan és esetleg kártékony próbálkozásokat. A biztonsági riasztások akkor aktiválódnak, ha a tevékenységben rendellenességek történnek. Ezek a biztonsági riasztások integrálva vannak az Azure Security Centerrel, és az előfizetési rendszergazdák e-mailben is értesítést kapnak róluk, a gyanús tevékenységek részleteivel és a fenyegetések kivizsgálására és javítására vonatkozó ajánlásokkal együtt. 
 

 
- [Az adatnapló és-áttekintés](https://docs.microsoft.com/azure/storage/common/storage-analytics-logging#how-logs-are-stored) 
 

 
- [Az Azure Defender tároláshoz konfigurálása](https://docs.microsoft.com/azure/storage/common/azure-defender-storage-configure?tabs=azure-portal)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="27-enable-alerts-for-anomalous-activities"></a>2,7: riasztások engedélyezése rendellenes tevékenységekhez

**Útmutató**: Azure Security Centerban engedélyezze az Azure Defender számára a Storage-fiókot. Engedélyezze a Storage-fiók diagnosztikai beállításait, és küldje el a naplókat egy Log Analytics munkaterületre. A Log Analytics-munkaterületet az Azure Sentinelbe irányíthatja, mivel ez egy biztonsági előkészítési automatizált választ (felszárnyaló) megoldást biztosít. Ez lehetővé teszi a forgatókönyvek (automatizált megoldások) létrehozását és a biztonsági problémák megoldására való felhasználását.  

 
- [Az Azure Sentinel előkészítése](../../sentinel/quickstart-onboard.md)  

 
- [Riasztások kezelése Azure Security Centerban](../../security-center/security-center-managing-and-responding-alerts.md)  

 
- [Riasztás a log Analytics-naplófájlok adatkezeléséről](../../azure-monitor/alerts/tutorial-response.md)  

 
- [Azure Storage Analytics-naplózás](storage-analytics-logging.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="28-centralize-anti-malware-logging"></a>2,8: kártevő szoftverek közötti naplózás központosítása

**Útmutató**: az Azure Security Center és az Azure Defender for Storage használatának engedélyezése az Azure Storage-ba való kártevő-feltöltések észlelésére a kivonatoló hírnevének elemzése és az aktív Tor-kilépési csomópontról (anonimizálásával proxy) gyanús hozzáférés használatával. 

- [Az Azure Defender tároláshoz konfigurálása](azure-defender-storage-configure.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="29-enable-dns-query-logging"></a>2,9: DNS-lekérdezések naplózásának engedélyezése

**Útmutató**: Azure DNS Analytics (előzetes verzió) megoldás Azure monitor a DNS-infrastruktúrával kapcsolatos elemzéseket gyűjt a biztonság, a teljesítmény és a műveletek terén.  Ez jelenleg nem támogatja az Azure Storage-fiókokat, azonban külső DNS-naplózási megoldást is használhat.  

 
- [A DNS-infrastruktúrával kapcsolatos információk gyűjtése a DNS Analytics előzetes verziójának megoldásával](../../azure-monitor/insights/dns-analytics.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

## <a name="identity-and-access-control"></a>Identitás- és hozzáférés-vezérlés

*További információt az [Azure biztonsági teljesítményteszt: Identity and Access Control](../../security/benchmarks/security-control-identity-access-control.md)című témakörben talál.*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3,1: a felügyeleti fiókok leltárának karbantartása

**Útmutató**: Azure Active Directory (Azure ad) beépített szerepkörökkel rendelkezik, amelyeket explicit módon kell hozzárendelni, és lekérdezhető. Az Azure AD PowerShell-modullal ad hoc lekérdezéseket hajthat végre a felügyeleti csoportok tagjait futtató fiókok felderítéséhez.

- [Címtárbeli szerepkör beszerzése az Azure AD-ben a PowerShell-lel](/powershell/module/azuread/get-azureaddirectoryrole)

- [Címtárbeli szerepkör tagjainak beszerzése az Azure AD-ben a PowerShell-lel](/powershell/module/azuread/get-azureaddirectoryrolemember)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="32-change-default-passwords-where-applicable"></a>3,2: az alapértelmezett jelszavak módosítása, ha alkalmazható

**Útmutató**: az Azure Storage-fiókok és a Azure Active Directory (Azure ad) az alapértelmezett vagy az üres jelszavak fogalmával rendelkeznek. Az Azure Storage egy hozzáférés-vezérlési modellt valósít meg, amely támogatja az Azure szerepköralapú hozzáférés-vezérlést (Azure RBAC), valamint a megosztott kulcsokat és a közös hozzáférésű aláírásokat (SAS). A megosztott kulcs és az SAS hitelesítés jellemzője, hogy a hívó nem társít identitást, ezért nem hajtható végre a rendszerbiztonsági tag engedély-alapú engedélyezése.

- [Az Azure Storage-beli adathozzáférés engedélyezése](storage-auth.md)

- [A rendszerbiztonsági tag és az Azure Storage-fiók hozzáférés-vezérlésének ismertetése](storage-introduction.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="33-use-dedicated-administrative-accounts"></a>3,3: dedikált rendszergazdai fiókok használata

**Útmutató**: szabványos működési eljárások létrehozása a Storage-fiókhoz hozzáféréssel rendelkező dedikált rendszergazdai fiókok használatával. A rendszergazdai fiókok számának figyeléséhez használja a Azure Security Center identitás-és hozzáférés-kezelés lehetőséget.

A Azure Active Directory (Azure AD) Privileged Identity Management a Microsoft-szolgáltatásokhoz és a Azure Resource Managerokhoz igénybe vehet egy igény szerinti és elég hozzáférési jogosultságot is.

- [Azure Security Center identitás és hozzáférés ismertetése](../../security-center/security-center-identity-access.md)

- [Privileged Identity Management áttekintése](/azure/active-directory/privileged-identity-management/)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="34-use-azure-active-directory-single-sign-on-sso"></a>3,4: Azure Active Directory egyszeri bejelentkezés (SSO) használata

**Útmutató**: ha lehetséges, használjon Azure Active Directory (Azure ad) SSO-t a különálló önálló hitelesítő adatok konfigurálása helyett. Azure Security Center identitás-és hozzáférés-kezelési javaslatok használata.
 

 
- [Az egyszeri bejelentkezés ismertetése az Azure AD-vel](../../active-directory/manage-apps/what-is-single-sign-on.md)
 

 
- [Az Azure Storage-beli adathozzáférés engedélyezése](storage-auth.md)
 

 
- [Blobok és várólisták hozzáférésének engedélyezése az Azure AD-vel](storage-auth-aad.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3,5: Multi-Factor Authentication használata az összes Azure Active Directory-alapú hozzáféréshez

**Útmutatás**: engedélyezze Azure Active Directory (Azure ad) többtényezős hitelesítését, és kövesse a Azure Security Center identitás-és hozzáférés-kezelési javaslatainak használatát a Storage-fiók erőforrásainak védelméhez.

- [Többtényezős hitelesítés engedélyezése az Azure-ban](../../active-directory/authentication/howto-mfa-getstarted.md)

- [Identitás és hozzáférés figyelése Azure Security Centeron belül](../../security-center/security-center-identity-access.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="36-use-secure-azure-managed-workstations-for-administrative-tasks"></a>3,6: biztonságos, Azure által felügyelt munkaállomások használata felügyeleti feladatokhoz

**Útmutató**: használja a mancsok (emelt szintű hozzáférési munkaállomások) használatát többtényezős hitelesítésre konfigurálva a Storage-fiók erőforrásainak bejelentkezéséhez és konfigurálásához.  

 
- [További tudnivalók a privilegizált hozzáférésű munkaállomásokról](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/) 

 
- [Többtényezős hitelesítés engedélyezése az Azure-ban](../../active-directory/authentication/howto-mfa-getstarted.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3,7: naplózás és riasztás a gyanús tevékenységekről a rendszergazdai fiókoktól

**Útmutatás**: Azure Security Center kockázatkezelési riasztások küldése Azure monitor és egyéni riasztások/értesítések konfigurálása a műveleti csoportokkal. Engedélyezheti az Azure Defender számára a Storage-fiókot a gyanús tevékenységekre vonatkozó riasztások létrehozásához. Emellett Azure Active Directory (Azure AD) kockázati észleléseket is használhat a kockázatos felhasználói viselkedésre vonatkozó riasztások és jelentések megtekintéséhez.

- [Az Azure Defender tároláshoz konfigurálása](azure-defender-storage-configure.md)
 

- [Az Azure AD kockázati észlelések ismertetése](../../active-directory/identity-protection/overview-identity-protection.md)
 

- [Műveleti csoportok konfigurálása egyéni riasztásokhoz és értesítésekhez](../../azure-monitor/alerts/action-groups.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3,8: az Azure-erőforrások kezelése csak jóváhagyott helyekről

**Útmutató**: a feltételes hozzáférés elnevezett helyeivel engedélyezheti, hogy a hozzáférés csak az IP-címtartományok vagy országok/régiók adott logikai csoportjaihoz legyen elérhető. 

- [Elnevezett helyszínek konfigurálása az Azure-ban](../../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="39-use-azure-active-directory"></a>3,9: a Azure Active Directory használata

**Útmutató**: a Azure Active Directory (Azure ad) használata központi hitelesítési és engedélyezési rendszerrel. Az Azure Azure szerepköralapú hozzáférés-vezérlést (Azure RBAC) biztosít, amely részletesen szabályozza az ügyfelek hozzáférését egy Storage-fiók erőforrásaihoz.   Az Azure AD-hitelesítő adatok használata, ha lehetséges, biztonsági szempontból ajánlott a fiók kulcsa helyett, ami könnyebben sérülhet. Ha az alkalmazás kialakításához közös hozzáférésű aláírásokra van szükség a blob Storage-hoz való hozzáféréshez, az Azure AD hitelesítő adataival hozzon létre egy felhasználói delegálási közös hozzáférési aláírást (SAS), amikor lehetséges a kiváló biztonság.

- [Azure AD-példány létrehozása és konfigurálása](../../active-directory/fundamentals/active-directory-access-create-new-tenant.md) 

- [Az Azure Storage erőforrás-szolgáltató használata a kezelési erőforrások eléréséhez](authorization-resource-provider.md)

- [Az Azure Blob-és üzenetsor-információhoz való hozzáférés konfigurálása az Azure RBAC Azure Portal](storage-auth-aad-rbac-portal.md)

- [Az Azure Storage-beli adathozzáférés engedélyezése](storage-auth.md)

- [Korlátozott hozzáférés biztosítása az Azure Storage-erőforrásokhoz közös hozzáférésű aláírások (SAS) használatával](storage-sas-overview.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="310-regularly-review-and-reconcile-user-access"></a>3,10: a felhasználói hozzáférés rendszeres áttekintése és egyeztetése

**Útmutató**: Tekintse át a Azure Active Directory (Azure ad) naplókat, amelyek segítenek felderíteni az elavult fiókokat, amelyek magukban foglalhatják a Storage-fiók felügyeleti szerepköreit. Emellett az Azure Identity Access Reviews használatával hatékonyan kezelhetők a csoporttagságok, hozzáférhetnek a vállalati alkalmazásokhoz, amelyek a Storage-fiók erőforrásainak eléréséhez és a szerepkör-hozzárendelésekhez használhatók. A felhasználók hozzáférését rendszeresen felül kell vizsgálni, hogy csak a megfelelő felhasználók férhessenek hozzájuk. 
 

 
A közös hozzáférésű aláírás (SAS) használatával biztonságos delegált hozzáférést biztosíthat a Storage-fiók erőforrásaihoz anélkül, hogy veszélyeztetné az adatai biztonságát. Megadhatja, hogy az ügyfél milyen erőforrásokhoz férhet hozzá, milyen engedélyekkel rendelkezik ezekhez az erőforrásokhoz, és hogy mennyi ideig érvényes az SAS érvényessége, más paraméterek között.
 

 
Tekintse át a névtelen olvasási hozzáférés tárolók és Blobok számára című ismertetőt is. Alapértelmezés szerint a tárolóhoz és az abban elhelyezkedő blobokhoz csak olyan felhasználó férhet hozzá, akinek megadta a megfelelő engedélyeket. A névtelen hitelesítési feltétellel a Azure Monitor használatával riasztást használhat a Storage-fiókok névtelen eléréséről.
 

 
A felhasználók számára biztosított hozzáférés időtartamának korlátozására az egyik leghatékonyabb módszer a nem gyanús felhasználói fiókok hozzáférésének kockázata. Az időkorlátos SAS URI-k egy hatékony módszer egy Storage-fiókhoz való felhasználói hozzáférés automatikus lejáratára. Emellett a Storage-fiókok kulcsai gyakoriak, így biztosítható, hogy a Storage-fiókok kulcsain keresztül történő váratlan hozzáférés korlátozott időtartamú legyen.
 

 
- [Az Azure AD jelentéskészítés ismertetése](/azure/active-directory/reports-monitoring/) 
 

 
- [Hozzáférés megtekintése és módosítása az Azure Storage-fiók szintjén](storage-auth-aad-rbac-portal.md)
 

 
- [Korlátozott hozzáférés biztosítása az Azure Storage-erőforrásokhoz közös hozzáférésű aláírások (SAS) használatával](storage-sas-overview.md)
 

 
- [Tárolók és blobok névtelen olvasási hozzáférésének kezelése](../blobs/anonymous-read-access-configure.md)
 

 
- [Tárfiók monitorozása az Azure Portalon](manage-storage-analytics-logs.md)
 

 
- [A Storage-fiók hozzáférési kulcsainak kezelése](storage-account-keys-manage.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3,11: a figyelő megkísérli a deaktivált hitelesítő adatok elérését

**Útmutató**: a Storage Analytics használatával naplózza a tárolási szolgáltatás sikeres és sikertelen kéréseinek részletes adatait. Az összes napló egy $logs nevű tárolóban található blokk blobokban van tárolva, amelyek automatikusan létrejönnek, ha a Storage Analytics engedélyezve van egy Storage-fiókhoz.
 

Diagnosztikai beállítások létrehozása Azure Active Directory (Azure AD) felhasználói fiókokhoz, a naplók és a bejelentkezési naplók elküldése egy Log Analytics-munkaterületre. Log Analytics munkaterületen belül konfigurálhatja a kívánt riasztásokat.

Az Azure Storage-fiókokkal kapcsolatos hitelesítési hibák figyeléséhez riasztásokat hozhat létre, amelyekkel értesítheti, ha bizonyos küszöbértékek el lettek-e érni a tárolási erőforrások metrikái esetében. Emellett az Azure Monitor használatával a névtelen hitelesítési feltételt használó Storage-fiókok névtelen hozzáférésére vonatkozó riasztást is használhat.

- [Azure Storage Analytics-naplózás](storage-analytics-logging.md)
 

- [Azure-tevékenységnaplók integrálása az Azure Monitorba](/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)
 

- [Metrikai riasztások konfigurálása az Azure Storage-fiókokhoz](manage-storage-analytics-logs.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3,12: riasztás a fiók bejelentkezési viselkedésének eltérése esetén

**Útmutató**: az Azure Active Directory (Azure ad) kockázatkezelési és identitás-védelmi funkcióinak használatával konfigurálhatja a tárolási fiók erőforrásaival kapcsolatos gyanús műveletekre vonatkozó automatizált válaszokat. A szervezet biztonsági válaszainak megvalósításához engedélyeznie kell az automatikus válaszokat az Azure Sentinel használatával.

- [Az Azure AD kockázatos bejelentkezéseinek megtekintése](../../active-directory/identity-protection/overview-identity-protection.md)

- [Az Identity Protection kockázati házirendjeinek konfigurálása és engedélyezése](../../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [Az Azure Sentinel előkészítése](../../sentinel/quickstart-onboard.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3,13: a Microsoft számára elérhetővé teszi a megfelelő ügyféladatokat a támogatási forgatókönyvek során

**Útmutató**: azon forgatókönyvek esetében, amelyekben a Microsoftnak hozzá kell férnie az ügyféladatok eléréséhez, Ügyfélszéf (a Storage-fiók előzetes verziója) olyan felületet biztosít az ügyfeleknek, amely áttekinti és jóváhagyja az ügyféladatok-hozzáférési kérelmeket. A Microsoft nem követeli meg, és nem kér hozzáférést a szervezetnek a Storage-fiókban tárolt titkos kulcsaihoz.

- [A Ügyfélszéf megismerése](../../security/fundamentals/customer-lockbox-overview.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

## <a name="data-protection"></a>Adatvédelem

*További információ: [Azure Security Benchmark: Adatvédelem](../../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4,1: bizalmas információk leltárának fenntartása

**Útmutató**: a címkék használatával segítséget nyújthat a bizalmas adatokat tároló vagy feldolgozó tárolási fiók erőforrásainak nyomon követésében. 

- [Címkék létrehozása és használata](../../azure-resource-manager/management/tag-resources.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4,2: bizalmas adatok tárolására vagy feldolgozására szolgáló rendszerek elkülönítése

**Útmutató**: az elkülönítés megvalósítása különálló előfizetések, felügyeleti csoportok és Storage-fiókok használatával az egyes biztonsági tartományok, például a környezet és az adatérzékenység érdekében.  A Storage-fiók korlátozásával szabályozhatja, hogy az alkalmazások és a vállalati környezetek milyen mértékben férhetnek hozzá az alkalmazásokhoz és a vállalati környezetekhez, a használt hálózatok típusa és részhalmaza alapján. A hálózati szabályok konfigurálásakor csak a megadott hálózatokon adatokat kérő alkalmazások férhetnek hozzá egy Storage-fiókhoz. Az Azure Storage szolgáltatáshoz való hozzáférést az Azure RBAC (Azure RBAC) segítségével szabályozhatja.

A magánhálózati végpontokat úgy is konfigurálhatja, hogy a virtuális hálózat és a szolgáltatás közötti adatforgalom biztonságosabbá tételét a Microsoft gerinc hálózatán keresztül végezze el, így nem kell a nyilvános internetről bejárni.

- [További Azure-előfizetések létrehozása](../../cost-management-billing/manage/create-subscription.md)

- [Management Groups létrehozása](../../governance/management-groups/create-management-group-portal.md)

- [Címkék létrehozása és használata](../../azure-resource-manager/management/tag-resources.md)

- [Azure Storage-tűzfalak és virtuális hálózatok konfigurálása](storage-network-security.md)

- [Virtuális hálózati szolgáltatásvégpontok](../../virtual-network/virtual-network-service-endpoints-overview.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4,3: a bizalmas adatok jogosulatlan átvitelének figyelése és letiltása

**Útmutató**: a bizalmas adatok tárolására vagy feldolgozására szolgáló Storage-fiókok erőforrásai számára a címkék használatával bizalmasként jelölheti meg az erőforrásokat. A kiszűrése-on keresztüli adatvesztés kockázatának csökkentése érdekében a Azure Firewall használatával korlátozza az Azure Storage-fiókok kimenő hálózati forgalmát.  

Emellett a virtuális hálózati szolgáltatás végpont-házirendjeivel is szűrheti a kimenő virtuális hálózati forgalmat az Azure Storage-fiókokhoz a szolgáltatási végponton keresztül, és lehetővé teszi, hogy az adatok csak bizonyos Azure Storage-fiókokra kiszűrése.

- [Azure Storage-tűzfalak és virtuális hálózatok konfigurálása](../../virtual-network/virtual-network-service-endpoint-policies-overview.md)

- [Virtuális hálózati szolgáltatásvégpont-szabályzatok létrehozása az Azure Storage-hez](../../private-link/tutorial-private-endpoint-storage-portal.md)

- [Az ügyféladatok Azure-beli védelmének ismertetése](../../security/fundamentals/protection-customer-data.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4,4: minden bizalmas adat titkosítása az átvitel során

**Útmutató**: a https használatát a Storage-fiókhoz szükséges biztonságos átvitel engedélyezésével kényszerítheti ki. A HTTP-t használó kapcsolatok ez követően el lesznek utasítva.  Emellett Azure Security Center és Azure Policy használatával kényszerítheti ki a Storage-fiók biztonságos átvitelét.

- [Biztonságos átvitel megkövetelése az Azure Storage-ban](storage-require-secure-transfer.md)

- [Security Center által figyelt Azure biztonsági szabályzatok](../../security-center/policy-reference.md)

**Felelősség**: Megosztott

**Azure Security Center monitorozás**: az [Azure biztonsági teljesítményteszt](/home/mbaldwin/docs/asb/azure-docs-pr/articles/governance/policy/samples/azure-security-benchmark.md) a Security Center alapértelmezett házirend-kezdeményezése, és a [Security Center ajánlásainak](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/security-center-recommendations.md)alapja. A vezérlőhöz kapcsolódó Azure Policy-definíciók Security Center automatikusan engedélyezve vannak. Az ehhez a vezérlőhöz kapcsolódó riasztásokhoz szükség lehet egy [Azure Defender](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/azure-defender.md) -csomagra a kapcsolódó szolgáltatásokhoz.

**Beépített definíciók Azure Policy – Microsoft. Storage**:

[!INCLUDE [Resource Policy for Microsoft.Storage 4.4](../../../includes/policy/standards/asb/rp-controls/microsoft.storage-4-4.md)]

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4,5: aktív felderítési eszköz használata a bizalmas adatok azonosítására

**Útmutató**: az adatazonosítási funkciók még nem érhetők el az Azure Storage-fiókhoz és a kapcsolódó erőforrásokhoz. Külső gyártótól származó megoldás implementálása, ha az szükséges a megfelelőség szempontjából. 

- [Az ügyféladatok Azure-beli védelmének ismertetése](../../security/fundamentals/protection-customer-data.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="46-use-role-based-access-control-to-control-access-to-resources"></a>4,6: szerepköralapú hozzáférés-vezérlés használata az erőforrásokhoz való hozzáférés szabályozásához

**Útmutató**: a Azure Active Directory (Azure ad) az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC) segítségével engedélyezi a hozzáférési jogokat a biztonságos erőforrásokhoz. Az Azure Storage olyan beépített Azure-beli RBAC-szerepköröket határoz meg, amelyek a blob-vagy üzenetsor-adateléréshez használt engedélyek közös készleteit foglalják magukban.

- [Azure RBAC-szerepkörök kiosztása Azure Storage-fiókhoz](https://docs.microsoft.com/azure/storage/common/storage-auth-aad-rbac-portal#assign-azure-roles-using-the-azure-portal)

- [Az Azure Storage erőforrás-szolgáltató használata a kezelési erőforrások eléréséhez](authorization-resource-provider.md)

- [Az Azure Blob-és üzenetsor-információhoz való hozzáférés konfigurálása az Azure RBAC Azure Portal](storage-auth-aad-rbac-portal.md)

- [Azure AD-példány létrehozása és konfigurálása](../../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

- [Az Azure Storage-beli adathozzáférés engedélyezése](storage-auth.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="48-encrypt-sensitive-information-at-rest"></a>4,8: bizalmas adatok titkosítása a nyugalmi állapotban

**Útmutató**: az Azure Storage-titkosítás minden Storage-fiók esetében engedélyezve van, és nem tiltható le. Az Azure Storage automatikusan titkosítja az adatait, amikor a felhőben is megmarad. Amikor beolvassa az Azure Storage adatait, azok titkosítását az Azure Storage oldja fel. Az Azure Storage-titkosítás lehetővé teszi, hogy az inaktív adatok védelme programkód módosítása nélkül történjen, vagy programkódot adjon hozzá bármely alkalmazáshoz. 

- [Az Azure Storage titkosításának megismerése a nyugalmi állapotban](storage-service-encryption.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4,9: a kritikus Azure-erőforrások változásainak naplózása és riasztása

**Útmutató**: a Azure monitor és az Azure-tevékenység naplójának használata riasztások létrehozásához, amikor a módosítások a Storage-fiók erőforrásaira vonatkoznak.  Az Azure Storage naplózását is engedélyezheti az Azure Storage-ba irányuló kérelmek engedélyezésének nyomon követése érdekében. A naplók azt jelzik, hogy egy kérelem névtelenül történt-e egy OAuth 2,0-token használatával, megosztott kulcs használatával vagy közös hozzáférésű aláírás (SAS) használatával.  Emellett az Azure Monitor használatával a névtelen hitelesítési feltételt használó Storage-fiókok névtelen hozzáférésére vonatkozó riasztást is használhat. 

 
- [Riasztások létrehozása az Azure-tevékenységek naplózási eseményeihez](../../azure-monitor/alerts/alerts-activity-log.md) 

 
- [Azure Storage Analytics-naplózás](storage-analytics-logging.md) 

 
- [Metrikai riasztások konfigurálása az Azure Storage-fiókokhoz](manage-storage-analytics-logs.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

## <a name="vulnerability-management"></a>Biztonságirés-kezelés

*További információért lásd az [Azure biztonsági teljesítményteszt: biztonsági rés kezelése](../../security/benchmarks/security-control-vulnerability-management.md)című témakört.*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5,1: automatikus biztonsági rések vizsgálatára szolgáló eszközök futtatása

**Útmutató**: a Azure Security Center ajánlásainak követése a Storage-fiókok konfigurációjának folyamatos auditálásához és figyeléséhez.  

- [Biztonsági javaslatok – gyorsútmutató](../../security-center/recommendations-reference.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5,4: a biztonsági rések keresésének összehasonlítása

**Útmutató**: nem alkalmazható; A Microsoft a biztonsági rések kezelését a Storage-fiókokat támogató mögöttes rendszereken végzi.

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5,5: kockázatértékelési folyamat használatával rangsorolhatja a felderített biztonsági rések szervizelését

**Útmutató**: a Azure Security Center által biztosított alapértelmezett kockázati minősítések (biztonságos pontszám) használata. 

- [A Azure Security Center biztonsági pontszámának megismerése](/azure/security-center/security-center-secure-score)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

## <a name="inventory-and-asset-management"></a>Leltár-és eszközfelügyelet

*További információt az [Azure biztonsági teljesítményteszt: leltár és eszközkezelés](../../security/benchmarks/security-control-inventory-asset-management.md)című témakörben talál.*

### <a name="61-use-automated-asset-discovery-solution"></a>6,1: automatikus eszköz-felderítési megoldás használata

**Útmutató**: az Azure Resource Graph használatával lekérdezheti és felderítheti az összes erőforrást (beleértve a Storage-fiókokat) az előfizetés (ok) n belül. Győződjön meg arról, hogy megfelelő (olvasási) engedélyekkel rendelkezik a bérlőben, és képes felsorolni az összes Azure-előfizetést, valamint az előfizetésében lévő erőforrásokat.

- [Lekérdezések létrehozása az Azure Graph használatával](../../governance/resource-graph/first-query-portal.md)

- [Azure-előfizetések megtekintése](/powershell/module/az.accounts/get-azsubscription)

- [Az Azure RBAC ismertetése](../../role-based-access-control/overview.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="62-maintain-asset-metadata"></a>6,2: az eszköz metaadatainak fenntartása

**Útmutató**: címkék alkalmazása a Storage-fiók erőforrásaihoz, amelyekkel a metaadatok logikailag rendezhetők a besorolásba. 

- [Címkék létrehozása és használata](../../azure-resource-manager/management/tag-resources.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="63-delete-unauthorized-azure-resources"></a>6,3: jogosulatlan Azure-erőforrások törlése

**Útmutató**: címkézés, felügyeleti csoportok és külön előfizetések használata, ahol szükséges, a Storage-fiókok és a kapcsolódó erőforrások rendszerezése és nyomon követése. Rendszeres időközönként egyeztetheti a leltárt, és gondoskodhat arról, hogy a jogosulatlan erőforrások törlése az előfizetésből időben történjen. 

A jogosulatlan Azure-erőforrások észleléséhez az Azure Defender for Storage is használható. 

- [További Azure-előfizetések létrehozása](../../cost-management-billing/manage/create-subscription.md) 

- [Management Groups létrehozása](../../governance/management-groups/create-management-group-portal.md)

- [Címkék létrehozása és használata](../../azure-resource-manager/management/tag-resources.md)

- [Az Azure Defender tároláshoz konfigurálása](azure-defender-storage-configure.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6,4: a jóváhagyott Azure-erőforrások leltárának meghatározása és karbantartása

**Útmutató**: a szervezeti igényeknek megfelelően létre kell hoznia a jóváhagyott Azure-erőforrások leltárát.

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="65-monitor-for-unapproved-azure-resources"></a>6,5: a nem jóváhagyott Azure-erőforrások figyelése

**Útmutató**: a Azure Policy használatával korlátozásokat állíthat be az ügyfél-előfizetésekben létrehozható erőforrások típusára a következő beépített szabályzat-definíciók használatával: 

 
 - Nem engedélyezett erőforrástípusok 
 
 - Engedélyezett erőforrástípusok 
 

 
Emellett az Azure Resource Graph használatával kérdezheti le és derítheti fel az előfizetéseken belüli erőforrásokat. Ez segíthet a magas biztonságú környezetekben, például a Storage-fiókokkal. 
 

 
- [Az Azure Policy konfigurálása és kezelése](../../governance/policy/tutorials/create-and-manage.md) 
 

 
- [Lekérdezések létrehozása az Azure Graph használatával](../../governance/resource-graph/first-query-portal.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6,7: nem jóváhagyott Azure-erőforrások és szoftveralkalmazások eltávolítása

**Útmutató**: az ügyfél meggátolhatja az erőforrások létrehozását és használatát Azure Policy az ügyfél céges szabályzata által megkövetelt módon. 

- [Az Azure Policy konfigurálása és kezelése](../../governance/policy/tutorials/create-and-manage.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="69-use-only-approved-azure-services"></a>6,9: csak jóváhagyott Azure-szolgáltatások használata

**Útmutató**: a Azure Policy használatával korlátozásokat állíthat be az ügyfél-előfizetésekben létrehozható erőforrások típusára a következő beépített szabályzat-definíciók használatával:

- Nem engedélyezett erőforrástípusok
- Engedélyezett erőforrástípusok

A hivatkozott hivatkozásokon további információk is rendelkezésre állnak.

- [Az Azure Policy konfigurálása és kezelése](../../governance/policy/tutorials/create-and-manage.md)

- [Adott erőforrástípus megtagadása a következővel Azure Policy](https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#general)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás**: az [Azure biztonsági teljesítményteszt](/home/mbaldwin/docs/asb/azure-docs-pr/articles/governance/policy/samples/azure-security-benchmark.md) a Security Center alapértelmezett házirend-kezdeményezése, és a [Security Center ajánlásainak](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/security-center-recommendations.md)alapja. A vezérlőhöz kapcsolódó Azure Policy-definíciók Security Center automatikusan engedélyezve vannak. Az ehhez a vezérlőhöz kapcsolódó riasztásokhoz szükség lehet egy [Azure Defender](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/azure-defender.md) -csomagra a kapcsolódó szolgáltatásokhoz.

**Azure Policy beépített definíciók – Microsoft. ClassicStorage**:

[!INCLUDE [Resource Policy for Microsoft.ClassicStorage 6.9](../../../includes/policy/standards/asb/rp-controls/microsoft.classicstorage-6-9.md)]

**Beépített definíciók Azure Policy – Microsoft. Storage**:

[!INCLUDE [Resource Policy for Microsoft.Storage 6.9](../../../includes/policy/standards/asb/rp-controls/microsoft.storage-6-9.md)]

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6,11: korlátozza a felhasználók képességét a Azure Resource Manager való interakcióra

**Útmutató**: az Azure feltételes hozzáférés használatával korlátozhatja, hogy a felhasználók képesek legyenek a Azure Resource Manager interakcióra az "Microsoft Azure felügyelet" alkalmazás "hozzáférés tiltása" beállításával. Ez megakadályozhatja az erőforrások létrehozását és módosítását a magas biztonsági környezetben, például a Storage-fiókokkal. 

- [A feltételes hozzáférés konfigurálása a Azure Resource Managerhoz való hozzáférés blokkolásához](../../role-based-access-control/conditional-access-azure-management.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

## <a name="secure-configuration"></a>Biztonságos konfiguráció

*További információt az [Azure biztonsági teljesítményteszt: biztonságos konfiguráció](../../security/benchmarks/security-control-secure-configuration.md)című témakörben talál.*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7,1: biztonságos konfigurációk létrehozása az összes Azure-erőforráshoz

**Útmutató**: Azure Policy aliasok használata a **Microsoft. Storage** névtérben egyéni szabályzatok létrehozásához a Storage-fiókok példányainak konfigurálásához vagy érvényesítéséhez. Az Azure Storage-fiókokhoz beépített Azure Policy-definíciókat is használhat, például:

- Nem korlátozott hálózati hozzáférés naplózása a Storage-fiókokhoz
- Azure Defender üzembe helyezése a Storage-ban
- A Storage-fiókokat át kell telepíteni az új Azure Resource Manager erőforrásokra
- Engedélyezni kell a tárfiókokba történő biztonságos átvitelt

A Azure Security Center javaslatainak használata biztonságos alapkonfigurációként a Storage-fiókok számára.

- [Az elérhető Azure Policy aliasok megtekintése](/powershell/module/az.resources/get-azpolicyalias)

- [Az Azure Policy konfigurálása és kezelése](../../governance/policy/tutorials/create-and-manage.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="73-maintain-secure-azure-resource-configurations"></a>7,3: biztonságos Azure-erőforrás-konfigurációk karbantartása

**Útmutató**: használja a Azure Policy [megtagadás] és a [telepítés, ha nem létezik] lehetőséget a Storage-fiók erőforrásaiban található biztonságos beállítások betartatásához. 

- [Az Azure Policy konfigurálása és kezelése](../../governance/policy/tutorials/create-and-manage.md) 

- [Azure Policy effektusok ismertetése](../../governance/policy/concepts/effects.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="75-securely-store-configuration-of-azure-resources"></a>7,5: az Azure-erőforrások biztonságos tárolása

**Útmutató**: az Azure Repos használatával biztonságosan tárolhatja és kezelheti a kódokat, például az egyéni Azure-szabályzatokat, Azure Resource Manager sablonokat, a kívánt állapot-konfigurációs parancsfájlokat stb. Az Azure DevOps-ben kezelt erőforrások eléréséhez engedélyeket adhat meg vagy tagadhat meg bizonyos felhasználók, beépített biztonsági csoportok vagy Azure Active Directory (Azure AD) által meghatározott csoportok számára, ha az Azure DevOps vagy az Azure AD-t integrálja a TFS-mel.

- [Kód tárolása az Azure DevOps](/azure/devops/repos/git/gitworkflow)

- [Engedélyek és csoportok az Azure DevOps](/azure/devops/organizations/security/about-permissions)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7,7: az Azure-erőforrások konfigurációs felügyeleti eszközeinek üzembe helyezése

**Útmutató**: a Azure Policy kihasználása riasztásra, naplózásra és a Storage-fiók rendszerkonfigurációinak betartatására. Emellett dolgozzon ki egy folyamatot és egy folyamatot a házirend-kivételek kezeléséhez. 

- [ Azure Policy konfigurálása és kezelése](../../governance/policy/tutorials/create-and-manage.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7,9: automatikus konfigurációs monitorozás megvalósítása Azure-erőforrásokhoz

**Útmutató**: a Azure Security Center kihasználása az Azure Storage-fiók erőforrásaihoz tartozó alapkonfiguráció-ellenőrzések végrehajtásához. 

- [Javaslatok szervizelése Azure Security Center](../../security-center/security-center-remediate-recommendations.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="711-manage-azure-secrets-securely"></a>7,11: az Azure-titkok biztonságos kezelése

**Útmutató**: az Azure Storage automatikusan titkosítja az adatait, amikor azt a felhőben megőrzi. Használhatja a Microsoft által felügyelt kulcsokat a Storage-fiók titkosításához, vagy a titkosítást a saját kulcsaival is kezelheti. Ha ügyfél által biztosított kulcsokat használ, a kulcsok biztonságos tárolásához használhatja a Azure Key Vault. 

Emellett a Storage-fiók kulcsainak gyakori elforgatásával korlátozhatja a Storage-fiókok kulcsainak elvesztését vagy közzétételét.

- [Inaktív adatok Azure Storage-titkosítása](storage-service-encryption.md)

- [A Storage-fiók hozzáférési kulcsainak kezelése](storage-account-keys-manage.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="712-manage-identities-securely-and-automatically"></a>7,12: az identitások biztonságos és automatikus kezelése

**Útmutató**: az Azure Storage-fiókokban lévő blobokhoz és várólistákhoz való hozzáférés engedélyezése Azure Active Directory (Azure ad) és felügyelt identitások használatával. Az Azure Blob és a üzenetsor-tároló támogatja az Azure AD-hitelesítést az Azure-erőforrások felügyelt identitásával. 

Az Azure-erőforrások felügyelt identitásai engedélyezhetik a blob-és üzenetsor-adatokhoz való hozzáférést az Azure AD hitelesítő adataival az Azure Virtual Machines r, Function apps, virtuálisgép-méretezési csoportok és egyéb szolgáltatások használatával. Ha felügyelt identitásokat használ az Azure-erőforrásokhoz az Azure AD-hitelesítéssel együtt, elkerülheti a hitelesítő adatok tárolását a felhőben futó alkalmazásaival.

- [Azure Blob-és üzenetsor-adatok hozzáférésének engedélyezése felügyelt identitás használatával](storage-auth-aad-rbac-portal.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="713-eliminate-unintended-credential-exposure"></a>7,13: a hitelesítő adatok nem szándékolt expozíciójának megszüntetése

**Útmutató**: hitelesítő adatok beolvasása a programkódon belül a hitelesítő adatok azonosításához. A Credential Scanner a felfedezett hitelesítő adatok biztonságosabb helyre, például az Azure Key Vaultba való áthelyezésére is javaslatot tesz. 

- [ A hitelesítő adatok beolvasójának beállítása](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

## <a name="malware-defense"></a>Kártevők elleni védelem

*További információt az [Azure biztonsági teljesítményteszt: kártevők elleni védelem](../../security/benchmarks/security-control-malware-defense.md)című témakörben talál.*

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8,2: a nem számítási Azure-erőforrásokra feltöltött fájlok előzetes vizsgálata

**Útmutató**: az Azure Defender for Storage használata az Azure Storage-ba történő kártevő-feltöltések észlelésére a kivonatoló hírnév elemzése és a gyanús hozzáférés egy aktív Tor-kilépési csomópontból (egy anonimizálásával-proxy) használatával. 
 

 
A kártevő szoftverek előzetes vizsgálatát is megteheti, mielőtt a nem számítási Azure-erőforrásokra, például a App Servicere, a Data Lake Storagera, a Blob Storagere és másokra is felhasználja a szervezet követelményeinek való megfelelést.
 

 
- [Az Azure Defender tároláshoz konfigurálása](azure-defender-storage-configure.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

## <a name="data-recovery"></a>Adat-helyreállítás

*További információt az [Azure biztonsági teljesítményteszt: adat-helyreállítás](../../security/benchmarks/security-control-data-recovery.md)című témakörben talál.*

### <a name="91-ensure-regular-automated-back-ups"></a>9,1: rendszeres automatizált biztonsági másolatok biztosítása

**Útmutató**: a Microsoft Azure Storage-fiókban lévő adatai mindig automatikusan replikálódnak a tartósság és a magas rendelkezésre állás biztosítása érdekében. Az Azure Storage átmásolja az adatait, hogy védve legyen a tervezett és nem tervezett eseményekkel szemben, beleértve az átmeneti hardverhiba, a hálózati vagy áramkimaradások, valamint a súlyos természeti katasztrófákat. Dönthet úgy is, hogy ugyanazon az adatközponton belül replikálja az adatait, többek között az azonos régión belüli és a földrajzilag elkülönített régiók között. 

Az Azure Automation szolgáltatással is engedélyezheti a Blobok rendszeres pillanatképeit.

- [Az Azure Storage-redundancia és a Service-Level-szerződések ismertetése](storage-redundancy.md)

- [BLOB pillanatképének létrehozása](/rest/api/storageservices/creating-a-snapshot-of-a-blob)

- [Azure Automation áttekintése](../../automation/automation-intro.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: teljes rendszerbiztonsági mentés és minden ügyfél által felügyelt kulcs biztonsági mentése

**Útmutató**: a Storage-fiók által támogatott szolgáltatásokból származó adatok biztonsági mentéséhez több módszer is rendelkezésre áll, beleértve a azcopy vagy a külső gyártótól származó eszközöket is. Az Azure Blob Storage nem módosítható tárolója lehetővé teszi, hogy a felhasználók üzleti szempontból kritikus fontosságú adatobjektumokat tároljanak egy FÉREGben (egyszer írható, olvasható) állapot. Ez az állapot a felhasználó által megadott intervallumban nem törölhető és nem módosítható adatvesztést tesz lehetővé.
 

Az ügyfél által felügyelt/biztosított kulcsok Azure Key Vault az Azure CLI vagy a PowerShell használatával is készíthetők. 

 
- [Bevezetés az AzCopy használatába](storage-use-azcopy-v10.md)  

- [BLOB Storage-módosíthatatlansági szabályzatok beállítása és kezelése](../blobs/storage-blob-immutability-policies-manage.md)
 

 
- [Key Vault-kulcsok biztonsági mentése az Azure-ban](/powershell/module/az.keyvault/backup-azkeyvaultkey)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: az összes biztonsági másolat ellenőrzése, beleértve az ügyfél által felügyelt kulcsokat

**Útmutató**: a Key Vault tanúsítványok, kulcsok, felügyelt tárolási fiókok és titkos kódok adatvisszaállításának rendszeres időközönkénti végrehajtása a következő PowerShell-parancsokkal:

Restore-AzKeyVaultCertificate

Restore-AzKeyVaultKey

Restore-AzKeyVaultManagedStorageAccount

Restore-AzKeyVaultSecret

- [Key Vault tanúsítványok visszaállítása](/powershell/module/az.keyvault/restore-azkeyvaultcertificate)

- [Key Vault kulcsok visszaállítása](/powershell/module/az.keyvault/restore-azkeyvaultkey)

- [Key Vault felügyelt Storage-fiókok visszaállítása](/powershell/module/az.keyvault/backup-azkeyvaultmanagedstorageaccount)

- [Key Vault titkos kódok visszaállítása](/powershell/module/az.keyvault/restore-azkeyvaultsecret)

- [A AzCopy egy parancssori segédprogram, amellyel blobokat, fájlokat és táblákat másolhat a Storage-fiókba, illetve onnan átmásolhatja azokat.](storage-use-azcopy-v10.md)

Megjegyzés: Ha át szeretné másolni az adatait az Azure Table Storage szolgáltatásba, és onnan másolja, telepítse az AzCopy 7,3-es verzióját.

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: a biztonsági másolatok és az ügyfél által felügyelt kulcsok védelmének biztosítása

**Útmutató**: Ha az ügyfél által felügyelt kulcsokat egy Storage-fiókban szeretné engedélyezni, Azure Key Vault kell használnia a kulcsok tárolásához. Engedélyeznie kell a Soft delete és a No Purge tulajdonságot a Key vaulton.  Key Vault Soft delete funkciója lehetővé teszi a törölt tárolók és tároló objektumok, például kulcsok, titkok és tanúsítványok helyreállítását.  Ha a Storage-fiókadatok Azure Storage-blobokra történő biztonsági mentését engedélyezi, a Soft delete lehetővé teszi az adatok mentését és helyreállítását a Blobok vagy blob-Pillanatképek törlésekor.  A biztonsági mentéseket bizalmas adatokként kell kezelni, és az alapkonfiguráció részeként alkalmazni kell a megfelelő hozzáférési és adatvédelmi vezérlőket.  Emellett a jobb védelem érdekében az üzleti szempontból kritikus fontosságú adatobjektumokat egy féreg (egyszer írható, olvasás) állapotba is tárolhatja.

- [A Azure Key Vault Soft delete használata](../../key-vault/general/key-vault-recovery.md)

- [Az Azure Storage-blobok helyreállítható törlése](../blobs/soft-delete-blob-overview.md)

- [Az üzlet szempontjából létfontosságú blobadatok tárolása nem módosítható tárolással](../blobs/storage-blob-immutable-storage.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

## <a name="incident-response"></a>Incidensmegoldás

*További információ: [Azure Security Benchmark: Incidensek kezelése](../../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10,1: incidens-válaszi útmutató létrehozása

**Útmutató**: Alakítson ki incidenskezelési útmutatót a vállalat számára. Gondoskodjon írásos incidenskezelési tervekről, amelyek definiálják az összes résztvevő szerepkörét, valamint az incidenskezelés fázisait az észleléstől az incidens utáni értékelésig.

- [Útmutató a saját biztonsági incidensek megoldási folyamatának létrehozásához](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Microsoft Security Response Center – incidens anatómiája](https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/)

- [A NIST számítógép-biztonsági incidensek kezelési útmutatója](https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10,2: incidensek pontozásának és rangsorolási eljárásának létrehozása

**Útmutató**: a Security Center súlyosságot rendel az egyes riasztásokhoz, hogy a prioritások alapján ki lehessen deríteni, hogy mely riasztásokat kell először megvizsgálni. A súlyosság azon alapul, hogy az Security Center milyen mértékben szerepel a riasztás kibocsátásához használt elemzésben vagy elemzésben, valamint a riasztást eredményező tevékenység mögötti rosszindulatú szándékkal. 

 
Emellett egyértelműen megjelölheti az előfizetéseket (pl.: éles környezetben, nem gyártva) címkék használatával és elnevezési rendszer létrehozása az Azure-erőforrások egyértelmű azonosításához és kategorizálásához, különösen a bizalmas adatok feldolgozásához.  Az Ön felelőssége, hogy rangsorolja a riasztások megoldását azon Azure-erőforrások és -környezetek kritikussága alapján, ahol az incidens történt.
 

 
- [Biztonsági riasztások az Azure Security Centerben](../../security-center/security-center-alerts-overview.md)
 

 
- [Címkék használata az Azure-erőforrások rendszerezéséhez](../../azure-resource-manager/management/tag-resources.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="103-test-security-response-procedures"></a>10,3: biztonsági reagálási eljárások tesztelése

**Útmutató**: az Azure-erőforrások védelmének biztosítása érdekében a rendszer az incidensek reagálási képességeinek rendszeres tesztelésére szolgáló gyakorlatokat hajt végre. Azonosítsa a gyenge pontokat és réseket, és szükség esetén dolgozza át a tervet.

- [A NIST kiadványa – útmutató az IT-csomagok és-képességek teszteléséhez, betanításához és alkalmazásához](https://csrc.nist.gov/publications/detail/sp/800-84/final)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10,4: biztonsági incidensek elérhetőségének biztosítása és riasztási értesítések konfigurálása biztonsági incidensekhez

**Útmutató**: a Microsoft a biztonsági incidensek elérhetőségi adatait arra használja fel, hogy felvegye Önnel a kapcsolatot, ha a Microsoft Security Response Center (MSRC) felfedi, hogy az adatokat egy törvénytelen vagy jogosulatlan fél is hozzáférte. A problémák megoldása érdekében tekintse át az incidenseket a tény után.

- [A Azure Security Center biztonsági kapcsolattartó beállítása](../../security-center/security-center-provide-security-contact-details.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10,5: biztonsági riasztások beépítése az incidensek gyorsreagáló rendszerébe

**Útmutató**: az Azure Security Center-riasztások és javaslatok exportálása a folyamatos exportálás funkcióval az Azure-erőforrásokkal kapcsolatos kockázatok azonosítása érdekében. A folyamatos exportálás lehetővé teszi a riasztások és javaslatok manuális és folyamatos exportálását. Az Azure Security Center adatösszekötővel továbbíthatja a riasztásokat az Azure Sentinel szolgáltatásba.

- [Folyamatos exportálás konfigurálása](../../security-center/continuous-export.md)

- [Riasztások streamelése az Azure Sentinelbe](../../sentinel/connect-azure-security-center.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="106-automate-the-response-to-security-alerts"></a>10,6: a biztonsági riasztásokra adott válasz automatizálása

**Útmutató**: a Azure Security Center munkafolyamat-automatizálási funkciója segítségével automatikusan elindíthatja a válaszokat az "Logic apps" használatával a biztonsági riasztások és az Azure-erőforrások védelme érdekében javasolt javaslatok alapján.
    

- [ A Munkafolyamat-automatizálás és a Logic Apps konfigurálása](../../security-center/workflow-automation.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

## <a name="penetration-tests-and-red-team-exercises"></a>Behatolási tesztek és Red Team-gyakorlatok

*További információkért tekintse meg az [Azure biztonsági teljesítményteszt: behatolási tesztek és a Red Team gyakorlatok](../../security/benchmarks/security-control-penetration-tests-red-team-exercises.md)című témakört.*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11,1: az Azure-erőforrások rendszeres behatolásának tesztelése, valamint az összes kritikus biztonsági vizsgálat szervizelésének biztosítása

**Útmutató**: kövesse a Microsoft részvételi szabályait, hogy a behatolási tesztek ne sértsék a Microsoft-házirendeket. A Microsoft által felügyelt felhőalapú infrastruktúrával, szolgáltatásokkal és alkalmazásokkal kapcsolatban a Microsoft stratégiáját és a Red Teaming és a Live site penetráció tesztelését is használhatja.

- [Behatolástesztelési beavatkozási szabályok](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)

- [Riasztási tesztek a Microsoft-felhőben](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Felelősség**: Megosztott

**Azure Security Center figyelés**: nincs

## <a name="next-steps"></a>Következő lépések

- [Az Azure Security Benchmark v2 áttekintésének](/azure/security/benchmarks/overview) megtekintése
- További tudnivalók az [Azure biztonsági alapterveiről](/azure/security/benchmarks/security-baselines-overview)
