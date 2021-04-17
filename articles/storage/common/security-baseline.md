---
title: Az Azure Biztonsági alapkonfigurációja az Azure Storage-hoz
description: Az Azure Storage biztonsági alapkonfigurációja eljárási útmutatást és erőforrásokat biztosít az Azure biztonsági teljesítménytesztben meghatározott biztonsági javaslatok megvalósításához.
author: msmbaldwin
ms.service: storage
ms.topic: conceptual
ms.date: 03/16/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 28d5bdc788e3b292545fd4c016fae36149f3a600
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/17/2021
ms.locfileid: "107589448"
---
# <a name="azure-security-baseline-for-azure-storage"></a>Az Azure Biztonsági alapkonfigurációja az Azure Storage-hoz

Ez a biztonsági alapkonfiguráció az Azure Biztonsági teljesítményteszt [1.0-s](../../security/benchmarks/overview-v1.md) verziójának útmutatását alkalmazza az Azure Storage-ra. Az Azure Security Benchmark ajánlásokat ad arra nézve, hogy hogyan tehetők biztonságossá a felhőalapú megoldások az Azure-ban.
A tartalom az Azure  biztonsági teljesítményteszt által meghatározott biztonsági vezérlők és az Azure Storage-hoz kapcsolódó útmutatás szerint van csoportosítva. **Az** Azure Storage-ban nem alkalmazható vezérlők ki vannak zárva.

 
Az Azure Storage azure-biztonsági teljesítményteszthez való teljes leképezését az Azure Storage teljes biztonsági alapkonfiguráció-leképezési fájljában [láthatja.](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)

## <a name="network-security"></a>Hálózati biztonság

*További információ: [Azure Security Benchmark: Hálózati biztonság](../../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1.1: Azure-erőforrások védelme virtuális hálózatokon belül

**Útmutató:** Konfigurálja a tárfiók tűzfalát úgy, hogy korlátozza az ügyfelek hozzáférését adott nyilvános IP-címtartományból, virtuális hálózatokat vagy adott Azure-erőforrásokat választ ki.  Privát végpontokat is konfigurálhat úgy, hogy a tárolószolgáltatásba a vállalattól származó forgalom kizárólag magánhálózaton keresztül ússzon.

Megjegyzés: A klasszikus tárfiókok nem támogatják a tűzfalakat és a virtuális hálózatokat.

- [Az Azure Storage-tűzfal konfigurálása](https://docs.microsoft.com/azure/storage/common/storage-network-security#change-the-default-network-access-rule)

- [Privát végpontok konfigurálása az Azure Storage-hoz](storage-private-endpoints.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozása:** Az [Azure-biztonsági](/azure/governance/policy/samples/azure-security-benchmark) teljesítményteszt a Security Center alapértelmezett szabályzati kezdeményezése, amely a Security Center [javaslatainak alapja.](/azure/security-center/security-center-recommendations) A Azure Policy vezérlővel kapcsolatos összes definíciót automatikusan engedélyezi a Security Center. Az ehhez a vezérlőhez kapcsolódó riasztások esetében szükség [lehet Azure Defender](/azure/security-center/azure-defender) kapcsolódó szolgáltatásokra vonatkozó tervre.

**Azure Policy definíciók – Microsoft.Storage:**

[!INCLUDE [Resource Policy for Microsoft.Storage 1.1](../../../includes/policy/standards/asb/rp-controls/microsoft.storage-1-1.md)]

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-network-interfaces"></a>1.2: Virtuális hálózatok, alhálózatok és hálózati adapterek konfigurációjának és forgalmának figyelése és naplózása

**Útmutató:** Az Azure Storage réteges biztonsági modellt biztosít. A tárfiókhoz való hozzáférést korlátozhatja a megadott IP-címekről, IP-tartományokból vagy egy Azure-beli virtuális gép alhálózati listájából Virtual Network. Használhatja a Azure Security Center, és a hálózatvédelmi javaslatokat követve biztosíthatja a hálózati erőforrások védelmét az Azure-ban. Emellett engedélyezze a hálózati biztonsági csoport forgalmi naplóit a Tárfiók tűzfalán keresztül konfigurált virtuális hálózatokhoz vagy alhálózathoz, és küldje el a naplókat egy tárfiókba a forgalom naplózásához. 

 
Vegye figyelembe, hogy ha privát végpontok vannak csatlakoztatva a tárfiókhoz, nem konfigurálhat hálózati biztonsági csoportszabályokat az alhálózatokra. 
 

 
- [Azure Storage-tűzfalak és virtuális hálózatok konfigurálása](storage-network-security.md) 
 

 
- [NSG-forgalom naplóinak engedélyezése](../../network-watcher/network-watcher-nsg-flow-logging-portal.md) 
 

 
- [A Azure Security Center](../../security-center/security-center-network-recommendations.md) 
 

 
- [Az Azure Storage privát végpontjainak ismertetése](https://docs.microsoft.com/azure/storage/common/storage-private-endpoints#known-issues)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="13-protect-critical-web-applications"></a>1.3: Kritikus fontosságú webalkalmazások védelme

**Útmutató:** Nem alkalmazható; A javaslat olyan webalkalmazások esetében ajánlott, amelyek Azure App Service számítási erőforrásokon futnak.

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: Az ismert kártékony IP-címekkel való kommunikáció megtagadása

**Útmutató:** Azure Defender Storage-fiókhoz való használatának engedélyezése. Az Azure Defender for Storage a biztonsági intelligencia újabb rétegét kínálja, amely észleli a tárfiókok elérésére vagy kihasználására tett szokatlan és esetleg kártékony próbálkozásokat.  Azure Security Center integrált riasztások olyan tevékenységeken alapulnak, amelyekhez a hálózati kommunikáció sikeresen feloldott IP-címhez lett társítva, függetlenül attól, hogy az IP-cím ismert kockázatos IP-cím (például ismert kriptográfiai cím), vagy olyan IP-cím, amelyet korábban nem ismertek el kockázatosként. A biztonsági riasztások akkor aktiválódnak, ha a tevékenységben rendellenességek történnek. 

- [Az Azure Defender tároláshoz konfigurálása](azure-defender-storage-configure.md) 

- [Az Azure Security Center fenyegetésintelligencia](../../security-center/azure-defender.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="15-record-network-packets"></a>1.5: Hálózati csomagok rekordja

**Útmutató:** Network Watcher csomagrögzítés lehetővé teszi, hogy rögzítési munkameneteket hozzon létre a Storage-fiók és a virtuális gép közötti forgalom nyomon követéséhez. A rögzítési munkamenethez szűrőket biztosítunk, hogy csak a kívánt forgalmat rögzítse. A csomagrögzítés reaktívan és proaktívan is segít diagnosztizálni a hálózati rendellenességeket. Egyéb felhasználási célok például a hálózati statisztikák gyűjtése, a hálózati behatolásokkal kapcsolatos információk gyűjtése, az ügyfél–kiszolgáló kommunikáció hibakeresése és még sok más. A csomagrögzítések távoli aktiválása megkönnyíti a csomagrögzítés manuális futtatásának terhét a kívánt virtuális gépen, ami értékes időt takarít meg. 

- [ Csomagrögzítések kezelése az Azure Network Watcher portál használatával](../../network-watcher/network-watcher-packet-capture-manage-portal.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6: Hálózatalapú behatolásészlelési/behatolásvédelmi rendszerek (IDS/IPS) üzembe helyezése

**Útmutató:** Azure Defender For Storage egy további biztonsági intelligenciát biztosít, amely észleli a tárfiókok hozzáférésére vagy kihasználására tett szokatlan és potenciálisan káros kísérleteket. A biztonsági riasztások akkor aktiválódnak, ha a tevékenységben rendellenességek történnek. Ezek a biztonsági riasztások integrálva vannak az Azure Security Centerrel, és az előfizetési rendszergazdák e-mailben is értesítést kapnak róluk, a gyanús tevékenységek részleteivel és a fenyegetések kivizsgálására és javítására vonatkozó ajánlásokkal együtt. 

- [Az Azure Defender tároláshoz konfigurálása](https://docs.microsoft.com/azure/storage/common/azure-defender-storage-configure?tabs=azure-security-center)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="17-manage-traffic-to-web-applications"></a>1.7: Webalkalmazások forgalmának kezelése

**Útmutató:** Nem alkalmazható; A javaslat olyan webalkalmazások esetében ajánlott, amelyek Azure App Service számítási erőforrásokon futnak.

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: A hálózati biztonsági szabályok összetettségének és adminisztratív terhelésének minimalizálása

**Útmutató:** A Storage-fiókhoz hozzáféréssel bíró virtuális hálózatokban lévő erőforrások esetében Virtual Network konfigurált Virtual Network-szolgáltatáscímkékkel határozhatja meg a hálózati biztonsági csoportok vagy hálózati Azure Firewall. Biztonsági szabályok létrehozása során szolgáltatáscímkéket használhat bizonyos IP-címek helyett. Ha megadja a szolgáltatáscímke nevét (például Storage) egy szabály megfelelő forrás- vagy célmezőben, engedélyezheti vagy megtagadhatja a megfelelő szolgáltatás forgalmát. A Microsoft kezeli a szolgáltatáscímke által felölelt címelőtagokat, és automatikusan frissíti a szolgáltatáscímkéket a címek változásával. 

Ha a hálózati hozzáférés hatókörét adott tárfiókokra kell kiterjedni, használja Virtual Network szolgáltatásvégpont-szabályzatokat.

- [További információ a szolgáltatáscímkék használatával kapcsolatban](../../virtual-network/service-tags-overview.md)

- [További információ az Azure Storage virtuális hálózati szolgáltatásvégpont-szabályzatjairól](../../virtual-network/virtual-network-service-endpoint-policies-overview.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: A hálózati eszközök szabványos biztonsági konfigurációjának fenntartása

**Útmutató:** Standard biztonsági konfigurációk definiálása és megvalósítása az Azure Storage-fiókhoz társított hálózati erőforrásokhoz a Azure Policy. A Azure Policy a "Microsoft.Storage" és a "Microsoft.Network" névterek aliasai segítségével egyéni szabályzatokat hozhat létre a Storage-fiók erőforrásainak hálózati konfigurációjának naplózásához vagy kikényszerításához. 

Használhatja a Storage-fiókhoz kapcsolódó beépített szabályzatdefiníciókat is, például: A tárfiókok virtuális hálózati szolgáltatásvégpontot kell használniuk 

- [Az Azure Policy konfigurálása és kezelése](../../governance/policy/tutorials/create-and-manage.md) 

- [Azure Policy Storage-minták](https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#storage) 

- [Azure Policy mintái a Network szolgáltatáshoz](https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#network) 

- [Azure Blueprints-terv létrehozása](../../governance/blueprints/create-blueprint-portal.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="110-document-traffic-configuration-rules"></a>1.10: A forgalom konfigurációs szabályainak dokumentálása

**Útmutató:** Címkék használata hálózati biztonsági csoportokhoz és más, a hálózati biztonsághoz és a forgalomhoz kapcsolódó erőforrásokhoz. A címkézéssel beépített és egyéni név-érték párokat társíthat egy adott hálózati erőforráshoz, így rendszerezheti a hálózati erőforrásokat, és az Azure-erőforrásokat a hálózat kialakításához társíthatja.

A címkézéshez kapcsolódó beépített Azure Policy-definíciók (például "Címke és érték megkövetelése" ) használatával biztosíthatja, hogy az összes erőforrás címkékkel legyen létrehozva, és értesítse Önt a meglévő, címkézetlen erőforrásokról. 

A hálózati biztonsági csoportok támogatják a címkéket, az egyes biztonsági szabályok azonban nem. A biztonsági szabályok tartalmaznak egy **Leírás** mezőt, amely a címkékbe általában berakott információk egy része tárolására használható.

- [Erőforráscímkék létrehozása és használata](../../azure-resource-manager/management/tag-resources.md)

- [Hálózati forgalom szűrése hálózati biztonsági csoportokkal](../../virtual-network/tutorial-filter-network-traffic.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: Automatizált eszközök használata a hálózati erőforrások konfigurációjának figyelése és a változások észlelése érdekében

**Útmutató:** A Azure Policy a hálózati erőforrások konfigurációs módosításainak naplózására szolgál.  Hozzon létre riasztásokat a Azure Monitor, amelyek akkor aktiválódnak, ha a kritikus hálózati erőforrások módosulnak.  

 
- [Az Azure Policy konfigurálása és kezelése](../../governance/policy/tutorials/create-and-manage.md)
 
 
- [Riasztások létrehozása a Azure Monitor](../../azure-monitor/alerts/alerts-activity-log.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

## <a name="logging-and-monitoring"></a>Naplózás és monitorozás

*További információ: [Azure biztonsági teljesítményteszt: Naplózás és monitorozás.](../../security/benchmarks/security-control-logging-monitoring.md)*

### <a name="22-configure-central-security-log-management"></a>2.2: Központi biztonsági naplókezelés konfigurálása

**Útmutató:** Naplók Azure Monitor a végponteszközök, hálózati erőforrások és más biztonsági rendszerek által létrehozott biztonsági adatok összesítéséhez. A Azure Monitor Log Analytics-munkaterület(ek) használatával lekérdezheti és elvégezheti az elemzéseket, az Azure Storage-fiókokat pedig használhatja hosszú távú/archiválási tároláshoz, opcionálisan olyan biztonsági funkciókkal, mint a nem módosítható tárolás és a kényszerített megőrzési visszatartás.

 
- [Platformnaplók és -metrikák gyűjtése Azure Monitor](../../azure-monitor/essentials/diagnostic-settings.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Azure-erőforrások naplózásának engedélyezése

**Útmutató:** Azure Storage Analytics a blobokhoz, üzenetsorokhoz és táblákhoz biztosít naplókat. A fiókhoz Azure Portal naplókat a következő módon konfigurálhatja: .   

 
- [Az Azure Storage-fiók monitorozásának konfigurálása](manage-storage-analytics-logs.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="25-configure-security-log-storage-retention"></a>2.5: Biztonsági naplók tárolásának konfigurálása

**Útmutató:** Ha biztonsági eseménynaplókat tárol az Azure Storage-fiókban vagy a Log Analytics-munkaterületen, a szervezet követelményeinek megfelelően beállíthatja a megőrzési szabályzatot.  

 
- [Adatmegőrzési szabályzat konfigurálása az Azure Storage-fiók naplóihoz](https://docs.microsoft.com/azure/storage/common/manage-storage-analytics-logs#configure-logging) 

 
- [Az adatmegőrzési időtartam módosítása a Log Analyticsben](https://docs.microsoft.com/azure/azure-monitor/logs/manage-cost-storage#change-the-data-retention-period)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="26-monitor-and-review-logs"></a>2.6: Naplók figyelése és áttekintése

**Útmutató:** Az Azure Storage-naplók áttekintéséhez rendelkezésre állnak a szokásos lehetőségek, például a Log Analytics-ajánlaton keresztüli lekérdezések, valamint a naplófájlok közvetlen megtekintésének egyedi lehetőségei. Az Azure Storage-ban a naplók blobokban vannak tárolva, amelyekhez közvetlenül a következő helyre kell hozzáférni: (A naplózási mappa alapértelmezés szerint rejtett, így közvetlenül kell `http://accountname.blob.core.windows.net/$logs` navigálnia. Nem jelenik meg a List commands (Listaparancsok) 

 
Emellett engedélyezze a Azure Defender storage-fiókhoz való engedélyezését. Az Azure Defender for Storage a biztonsági intelligencia újabb rétegét kínálja, amely észleli a tárfiókok elérésére vagy kihasználására tett szokatlan és esetleg kártékony próbálkozásokat. A biztonsági riasztások akkor aktiválódnak, ha a tevékenységben rendellenességek történnek. Ezek a biztonsági riasztások integrálva vannak az Azure Security Centerrel, és az előfizetési rendszergazdák e-mailben is értesítést kapnak róluk, a gyanús tevékenységek részleteivel és a fenyegetések kivizsgálására és javítására vonatkozó ajánlásokkal együtt. 
 

 
- [Adatok naplózása és áttekintése](https://docs.microsoft.com/azure/storage/common/storage-analytics-logging#how-logs-are-stored) 
 

 
- [Az Azure Defender tároláshoz konfigurálása](https://docs.microsoft.com/azure/storage/common/azure-defender-storage-configure?tabs=azure-portal)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7: Riasztások engedélyezése rendellenes tevékenységekhez

**Útmutató:** A Azure Security Center engedélyezze Azure Defender Storage-fiókhoz. Engedélyezze a Storage-fiók diagnosztikai beállításait, és küldje el a naplókat egy Log Analytics-munkaterületre. A Log Analytics-munkaterületet a Azure Sentinel, mivel biztonsági vezénylési automatizált választ (SOAR) kínál. Ez lehetővé teszi, hogy forgatókönyveket (automatizált megoldásokat) hoznak létre és használjanak a biztonsági problémák megoldásához.  

 
- [A Azure Sentinel](../../sentinel/quickstart-onboard.md)  

 
- [Riasztások kezelése a Azure Security Center](../../security-center/security-center-managing-and-responding-alerts.md)  

 
- [Naplóelemzési naplóadatokra vonatkozó riasztások](../../azure-monitor/alerts/tutorial-response.md)  

 
- [Azure Storage Analytics-naplózás](storage-analytics-logging.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="28-centralize-anti-malware-logging"></a>2.8: A kártevőirtó naplózás központosította

**Útmutató:** Használja az Azure Security Centert, és engedélyezze a Azure Defender for Storage-ot a kártevők Azure Storage-ba való feltöltésének észleléséhez kivonatoló hírnévelemzéssel és egy aktív Tor kilépési csomópontból (egy anonimizálási proxy) származó gyanús hozzáféréssel. 

- [Az Azure Defender tároláshoz konfigurálása](azure-defender-storage-configure.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="29-enable-dns-query-logging"></a>2.9: DNS-lekérdezésnaplózás engedélyezése

**Útmutató:** Azure DNS Analytics (előzetes verzió) megoldás a Azure Monitor a DNS-infrastruktúrával kapcsolatos információkat gyűjt a biztonságról, a teljesítményről és a műveletekről.  Ez jelenleg nem támogatja az Azure Storage-fiókokat, de külső DNS-naplózási megoldást is használhat.  

 
- [A DNS-infrastruktúrával kapcsolatos elemzések gyűjtése az előzetes verziójú DNS Analytics megoldással](../../azure-monitor/insights/dns-analytics.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

## <a name="identity-and-access-control"></a>Identitás- és hozzáférés-vezérlés

*További információ: [Azure Biztonsági teljesítményteszt: Identitás és Access Control.](../../security/benchmarks/security-control-identity-access-control.md)*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: A rendszergazdai fiókok nyilvántartása

**Útmutató:** Azure Active Directory (Azure AD) beépített szerepkörökkel rendelkezik, amelyekhez explicit módon kell hozzárendelni és lekérdezhetőnek kell lennie. Az Azure AD PowerShell-modullal alkalmi lekérdezéseket hajt végre a rendszergazdai csoportok tagjainak felderítéséhez.

- [Címtárszerepk lekért szerepkör az Azure AD-ban a PowerShell használatával](/powershell/module/azuread/get-azureaddirectoryrole)

- [Címtárbeli szerepkör tagjainak lekérte az Azure AD-ban a PowerShell használatával](/powershell/module/azuread/get-azureaddirectoryrolemember)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="32-change-default-passwords-where-applicable"></a>3.2: Az alapértelmezett jelszavak módosítása, ha vannak

**Útmutató:** Az Azure Storage-fiókok és Azure Active Directory (Azure AD) esetében az alapértelmezett és az üres jelszavak is használatban vannak. Az Azure Storage olyan hozzáférés-vezérlési modellt valósít meg, amely támogatja az Azure szerepköralapú hozzáférés-vezérlését (Azure RBAC), valamint a közös kulcsú és közös hozzáférésű jogosultságokat (SAS). A megosztott kulcsos és SAS-hitelesítés egyik jellemzője, hogy nincs identitás társítva a hívóhoz, ezért a rendszerbiztonsági tag engedélyalapú hitelesítése nem hajtható végre.

- [Az Azure Storage-beli adatokhoz való hozzáférés jogosultságának izing (Authorizing access to data in Azure Storage) (Az](storage-auth.md)

- [Az Azure Storage-fiók rendszerbiztonsági rendszerbiztonsági tagjának és hozzáférés-vezérlésének ismertetése](storage-introduction.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: Dedikált rendszergazdai fiókok használata

**Útmutató:** Szabványos üzemeltetési eljárásokat hozhat létre a Storage-fiókhoz hozzáféréssel rendelkező dedikált rendszergazdai fiókok használatával kapcsolatban. A Azure Security Center fiókok számának figyelése az identitás- és hozzáférés-kezelés használatával.

Az azure-beli Azure Active Directory (Azure AD) és a Microsoft-szolgáltatások emelt szintű szerepkörei Privileged Identity Management és a megfelelő Azure Resource Manager.

- [Az Azure Security Center és a hozzáférés](../../security-center/security-center-identity-access.md)

- [Privileged Identity Management áttekintés](/azure/active-directory/privileged-identity-management/)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="34-use-azure-active-directory-single-sign-on-sso"></a>3.4: Egyszeri Azure Active Directory (SSO) használata

**Útmutató:** Amikor csak lehetséges, használjon Azure Active Directory (Azure AD) SSO-t az önálló hitelesítő adatok szolgáltatásonkénti konfigurálása helyett. Használja Azure Security Center Identity and Access Management (Identitás- és hozzáférés-kezelés) javaslatokat.
 

 
- [Az SSO ismertetése az Azure AD-val](../../active-directory/manage-apps/what-is-single-sign-on.md)
 

 
- [Az Azure Storage-beli adatokhoz való hozzáférés jogosultságának izing (Authorizing access to data in Azure Storage) (Az](storage-auth.md)
 

 
- [Blobok és üzenetsorok elérésének jogosultsága az Azure AD használatával](storage-auth-aad.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: Többtényezős hitelesítés használata minden Azure Active Directory-alapú hozzáféréshez

**Útmutató:** Engedélyezze Azure Active Directory (Azure AD) többtényezős hitelesítést, és kövesse Azure Security Center Storage-fiók erőforrásainak védelmére vonatkozó identitás- és hozzáférés-kezelési javaslatokat.

- [Többtényezős hitelesítés engedélyezése az Azure-ban](../../active-directory/authentication/howto-mfa-getstarted.md)

- [Identitás és hozzáférés figyelése a Azure Security Center](../../security-center/security-center-identity-access.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="36-use-secure-azure-managed-workstations-for-administrative-tasks"></a>3.6: Biztonságos, Azure által felügyelt munkaállomások használata felügyeleti feladatokhoz

**Útmutató:** Emelt hozzáférési szintű munkaállomások (emelt szintű hozzáférésű munkaállomások) használata többtényezős hitelesítéssel, amely a Storage-fiók erőforrásainak bejelentkezésére és konfigurálására van konfigurálva.  

 
- [Tudnivalók a Privileged Access munkaállomásról](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/) 

 
- [Többtényezős hitelesítés engedélyezése az Azure-ban](../../active-directory/authentication/howto-mfa-getstarted.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3.7: A rendszergazdai fiókokból származó gyanús tevékenységek naplózása és riasztása

**Útmutató:** Riasztások Azure Security Center riasztások küldése a Azure Monitor és egyéni riasztások/értesítések konfigurálása műveletcsoportok használatával. Engedélyezze Azure Defender storage-fiókhoz a gyanús tevékenységekre vonatkozó riasztások létrehozásához. Emellett a Azure Active Directory (Azure AD) kockázatészlelései segítségével riasztásokat és jelentéseket is megtekintet a kockázatos felhasználók viselkedéséről.

- [Az Azure Defender tároláshoz konfigurálása](azure-defender-storage-configure.md)
 

- [Az Azure AD kockázatészlelései](../../active-directory/identity-protection/overview-identity-protection.md)
 

- [Műveletcsoportok konfigurálása egyéni riasztáshoz és értesítéshez](../../azure-monitor/alerts/action-groups.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: Azure-erőforrások kezelése csak jóváhagyott helyekről

**Útmutató:** A feltételes hozzáférés elnevezett helyekkel csak az IP-címtartományok vagy országok/régiók meghatározott logikai csoportosításai számára engedélyezi a hozzáférést. 

- [Elnevezett helyek konfigurálása az Azure-ban](../../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="39-use-azure-active-directory"></a>3.9: A Azure Active Directory

**Útmutató:** A Azure Active Directory (Azure AD) használata központi hitelesítési és engedélyezési rendszerként. Az Azure szerepköralapú hozzáférés-vezérlést (Azure RBAC) biztosít az ügyfelek tárfiókbeli erőforrásokhoz való hozzáférésének részletes szabályozására.   Ha lehetséges, használjon Azure AD-beli hitelesítő adatokat ajánlott biztonsági eljárásként a fiókkulcs használata helyett, amely könnyebben sérülhet. Ha az alkalmazás kialakításához közös hozzáférésű jogosultságokra van szükség a Blob Storage-hoz való hozzáféréshez, az Azure AD hitelesítő adataival hozzon létre egy felhasználódelegáláshoz használható közös hozzáférésű jogosultságokat (SAS-t), amikor csak lehetséges a nagyobb biztonság érdekében.

- [Azure AD-példány létrehozása és konfigurálása](../../active-directory/fundamentals/active-directory-access-create-new-tenant.md) 

- [Az Azure Storage erőforrás-szolgáltató használata a felügyeleti erőforrások eléréséhez](authorization-resource-provider.md)

- [Azure-blobok és -üzenetsorok adataihoz való hozzáférés konfigurálása az Azure RBAC használatával a Azure Portal](storage-auth-aad-rbac-portal.md)

- [Az Azure Storage-beli adatokhoz való hozzáférés jogosultságának izing (Authorizing access to data in Azure Storage) (Az](storage-auth.md)

- [Korlátozott hozzáférés megadása az Azure Storage-erőforrásokhoz közös hozzáférésű jogosultságok (SAS) használatával](storage-sas-overview.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: A felhasználói hozzáférés rendszeres áttekintése és egyeztetése

**Útmutató:** Tekintse át a Azure Active Directory (Azure AD) naplóit, hogy felderítsen olyan elavult fiókokat, amelyek a Storage-fiók rendszergazdai szerepkörével rendelkező fiókokat is tartalmazhatják. Emellett az Azure Identity Access Reviews használatával hatékonyan kezelheti a csoporttagságokat, hozzáférhet a Storage-fiók erőforrásaihoz való hozzáféréshez használható vállalati alkalmazásokhoz, valamint szerepkör-hozzárendeléseket. A felhasználói hozzáférést rendszeresen át kell vizsgálni, hogy csak a megfelelő felhasználók férnek hozzá. 
 

 
A közös hozzáférésű jogosultságok (SAS) használatával biztonságos delegált hozzáférést is nyújthat a tárfiókban az erőforrások biztonságának veszélyeztetése nélkül. Többek között szabályozhatja, hogy az ügyfél milyen erőforrásokhoz férhet hozzá, milyen engedélyekkel rendelkezik ezekhez az erőforrásokhoz, és mennyi ideig érvényes az SAS.
 

 
Emellett tekintse át a tárolók és blobok névtelen olvasási hozzáférését. Alapértelmezés szerint a tárolóhoz és az abban elhelyezkedő blobokhoz csak olyan felhasználó férhet hozzá, akinek megadta a megfelelő engedélyeket. A tárfiókok Azure Monitor a névtelen hitelesítési feltételt használó tárfiókok névtelen hozzáférésének riasztási szolgáltatásával.
 

 
A nem gyanús felhasználói fiókokhoz való hozzáférés kockázatának csökkentésének egyik hatékony módja a felhasználók számára megadott hozzáférés időtartamának korlátozása. Az időkorzisztens SAS URI-k hatékony módjai a tárfiók felhasználói hozzáférésének automatikus lejáratának. Emellett a tárfiókkulcsok gyakori rotása biztosítja, hogy a tárfiókkulcsokkal való váratlan hozzáférés korlátozott időtartamú legyen.
 

 
- [Az Azure AD Reporting szolgáltatás](/azure/active-directory/reports-monitoring/) 
 

 
- [Hozzáférés megtekintése és módosítása az Azure Storage-fiók szintjén](storage-auth-aad-rbac-portal.md)
 

 
- [Korlátozott hozzáférés megadása az Azure Storage-erőforrásokhoz közös hozzáférésű jogosultságok (SAS) használatával](storage-sas-overview.md)
 

 
- [Tárolók és blobok névtelen olvasási hozzáférésének kezelése](../blobs/anonymous-read-access-configure.md)
 

 
- [Tárfiók monitorozása az Azure Portalon](manage-storage-analytics-logs.md)
 

 
- [Tárfiók hozzáférési kulcsának kezelése](storage-account-keys-manage.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3.11: Az inaktivált hitelesítő adatok elérésére tett kísérletek figyelése

**Útmutató:** A Storage Analytics naplózza a társzolgáltatásnak lekért sikeres és sikertelen kérések részletes adatait. Minden napló blokkblobban van tárolva egy $logs nevű tárolóban, amelyek automatikusan létrejönnek, Storage Analytics engedélyezve van egy tárfiókhoz.
 

Diagnosztikai beállítások létrehozása Azure Active Directory (Azure AD) felhasználói fiókokhoz, és az auditnaplók és bejelentkezési naplók elküldése egy Log Analytics-munkaterületre. A kívánt riasztásokat a Log Analytics-munkaterületen konfigurálhatja.

Az Azure Storage-fiókok hitelesítési meghibásodásának figyelése érdekében riasztásokat hozhat létre, amelyek figyelmeztetik, ha elérte a tárolási erőforrások metrika bizonyos küszöbértékét. Emellett a Azure Monitor riasztást is használhat a tárfiókok névtelen hitelesítési feltételt használó névtelen hozzáféréséhez.

- [Azure Storage Analytics-naplózás](storage-analytics-logging.md)
 

- [Azure-tevékenységnaplók integrálása az Azure Monitorba](/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)
 

- [Metrikákra vonatkozó riasztások konfigurálása Azure Storage-fiókokhoz](manage-storage-analytics-logs.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3.12: Riasztás a fiók bejelentkezési viselkedésének eltérése esetén

**Útmutató:** A Azure Active Directory (Azure AD) Kockázat- és identity Protection-funkcióival konfigurálhatja a Storage-fiók erőforrásaival kapcsolatos gyanús műveletek automatikus válaszait. A szervezet biztonsági válaszait úgy Azure Sentinel, hogy automatizált válaszokat engedélyezzen a felhasználók számára.

- [Az Azure AD kockázatos bejelentkezéseinek megtekintése](../../active-directory/identity-protection/overview-identity-protection.md)

- [Az Identity Protection kockázati szabályzatok konfigurálása és engedélyezése](../../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [A Azure Sentinel](../../sentinel/quickstart-onboard.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: Hozzáférés biztosítanak a Microsoft számára a releváns ügyféladatokhoz a támogatási forgatókönyvek során

**Útmutató:** Olyan támogatási forgatókönyvekben, ahol a Microsoftnak hozzá kell férni az ügyféladatokhoz, a Ügyfélszéf (Storage-fiók előzetes verziója) felületet biztosít az ügyfelek számára az ügyféladat-hozzáférési kérelmek áttekintéséhez és jóváhagyásához vagy elutasításához. A Microsoft nem követeli meg és nem is kér hozzáférést a szervezet Storage-fiókban tárolt titkos kulcsokhoz.

- [A Ügyfélszéf](../../security/fundamentals/customer-lockbox-overview.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

## <a name="data-protection"></a>Adatvédelem

*További információ: [Azure Security Benchmark: Adatvédelem](../../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: Bizalmas adatok leltárának fenntartása

**Útmutató:** A címkék segítségével nyomon lehet követni a bizalmas adatokat tároló vagy feldolgozó tárfiók-erőforrásokat. 

- [Címkék létrehozása és használata](../../azure-resource-manager/management/tag-resources.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: Bizalmas adatokat tároló vagy feldolgozó rendszerek elkülönítése

**Útmutató:** Elkülönítés implementálás különálló előfizetésekkel, felügyeleti csoportokkal és tárfiókokkal az egyes biztonsági tartományokhoz, például a környezethez és az adatok bizalmasságához.  Korlátozhatja a Storage-fiókot, hogy a használt hálózatok típusa és részkészlete alapján szabályozni tudja, hogy az alkalmazások és a vállalati környezetek milyen szintű hozzáférést követelnek meg a tárfiókok számára. A hálózati szabályok konfigurálásakor csak a megadott hálózatokon adatokat kérő alkalmazások férhetnek hozzá a tárfiókhoz. Az Azure Storage-hoz való hozzáférést az Azure RBAC (Azure RBAC) használatával szabályozhatja.

Privát végpontokat is konfigurálhat a biztonság növelése érdekében, mivel a virtuális hálózat és a szolgáltatás közötti forgalom a Microsoft gerinchálózatán áthalad, így kiküszöbölve a nyilvános internetről való kitettséget.

- [További Azure-előfizetések létrehozása](../../cost-management-billing/manage/create-subscription.md)

- [Új Management Groups](../../governance/management-groups/create-management-group-portal.md)

- [Címkék létrehozása és használata](../../azure-resource-manager/management/tag-resources.md)

- [Azure Storage-tűzfalak és virtuális hálózatok konfigurálása](storage-network-security.md)

- [Virtuális hálózati szolgáltatásvégpontok](../../virtual-network/virtual-network-service-endpoints-overview.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: Bizalmas adatok jogosulatlan átvitelének figyelése és blokkolása

**Útmutató:** Bizalmas adatokat tároló vagy feldolgozó Storage-fiókerőforrások esetén az erőforrásokat bizalmasként jelölje meg címkék használatával. A kiszivárgással történő adatvesztés kockázatának csökkentése érdekében korlátozza az Azure Storage-fiókok kimenő hálózati forgalmát az Azure Firewall.  

Emellett virtuális hálózati szolgáltatásvégpont-szabályzatokkal szűrheti az Azure Storage-fiókok bejövő virtuális hálózati forgalmát a szolgáltatásvégponton, és engedélyezheti az adatok kiszivárgását csak adott Azure Storage-fiókokba.

- [Azure Storage-tűzfalak és virtuális hálózatok konfigurálása](../../virtual-network/virtual-network-service-endpoint-policies-overview.md)

- [Virtuális hálózati szolgáltatásvégpont-szabályzatok létrehozása az Azure Storage-hez](../../private-link/tutorial-private-endpoint-storage-portal.md)

- [Az ügyféladatok Azure-beli védelmének ismertetése](../../security/fundamentals/protection-customer-data.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: Az átvitel során bizalmas adatok titkosítása

**Útmutató:** A HTTPS használatát a tárfiók biztonságos átvitelre van szükség beállításának engedélyezésével kényszerítheti. A HTTP-t használó kapcsolatok ez követően el lesznek utasítva.  Emellett a tárfiók Azure Security Center és Azure Policy kényszeríti a biztonságos átvitelt.

- [Biztonságos átvitel megkövetelve az Azure Storage-ban](storage-require-secure-transfer.md)

- [Az azure-beli biztonsági szabályzatok figyelését Security Center](../../security-center/policy-reference.md)

**Felelősség**: Megosztott

**Azure Security Center monitorozása:** Az [Azure-biztonsági](/azure/governance/policy/samples/azure-security-benchmark) teljesítményteszt a Security Center alapértelmezett szabályzati kezdeményezése, amely a Security Center [javaslatainak alapja.](/azure/security-center/security-center-recommendations) A Azure Policy vezérlővel kapcsolatos összes definíciót automatikusan engedélyezi a Security Center. Az ehhez a vezérlőhez kapcsolódó riasztások esetében szükség [lehet Azure Defender](/azure/security-center/azure-defender) kapcsolódó szolgáltatásokra vonatkozó tervre.

**Azure Policy definíciók – Microsoft.Storage:**

[!INCLUDE [Resource Policy for Microsoft.Storage 4.4](../../../includes/policy/standards/asb/rp-controls/microsoft.storage-4-4.md)]

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: A bizalmas adatok azonosítása aktív felderítési eszközzel

**Útmutató:** Az Adatazonosítási funkciók még nem érhetők el az Azure Storage-fiókhoz és a kapcsolódó erőforrásokhoz. Implementálja a külső megoldásokat, ha megfelelőségi célokból szükség van rá. 

- [Az ügyféladatok Azure-beli védelmének ismertetése](../../security/fundamentals/protection-customer-data.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="46-use-role-based-access-control-to-control-access-to-resources"></a>4.6: Szerepköralapú hozzáférés-vezérlés használata az erőforrásokhoz való hozzáférés szabályozása érdekében

**Útmutató:** Azure Active Directory (Azure AD) az Azure szerepköralapú hozzáférés-vezérlésével (Azure RBAC) engedélyezi a biztonságos erőforrásokhoz való hozzáférési jogosultságokat. Az Azure Storage azure-beli beépített RBAC-szerepköröket definiál, amelyek a blob- vagy üzenetsoradatok eléréséhez használt általános engedélykészleteket foglalják magukban.

- [Azure RBAC-szerepkörök hozzárendelése Azure Storage-fiókhoz](https://docs.microsoft.com/azure/storage/common/storage-auth-aad-rbac-portal#assign-azure-roles-using-the-azure-portal)

- [Az Azure Storage erőforrás-szolgáltató használata a felügyeleti erőforrások eléréséhez](authorization-resource-provider.md)

- [Azure-blobok és -üzenetsorok adataihoz való hozzáférés konfigurálása az Azure RBAC használatával a Azure Portal](storage-auth-aad-rbac-portal.md)

- [Azure AD-példány létrehozása és konfigurálása](../../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

- [Az Azure Storage-beli adatokhoz való hozzáférés jogosultságának izing (Authorizing access to data in Azure Storage) (Az](storage-auth.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: Bizalmas adatok titkosítása

**Útmutató:** Az Azure Storage-titkosítás minden tárfiókhoz engedélyezve van, és nem tiltható le. Az Azure Storage automatikusan titkosítja az adatokat, amikor a felhőben vannak megőrzve. Amikor beolvassa az Azure Storage adatait, azok titkosítását az Azure Storage oldja fel. Az Azure Storage-titkosítással anélkül biztosíthatja az adatok biztonságát, hogy módosítania kell a kódot, vagy kódot kell hozzáadnia az alkalmazásokhoz. 

- [Az Azure Storage titkosításának ismertetése az azure-beli használatban](storage-service-encryption.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: Kritikus fontosságú Azure-erőforrások változásainak naplózása és riasztása

**Útmutató:** Azure Monitor Azure-tevékenységnaplóval való bejelentkezéssel riasztásokat hozhat létre a Storage-fiók erőforrásainak változásairól.  Az Azure Storage-naplózás engedélyezésével azt is nyomon követheti, hogy az Azure Storage-ba vonatkozó egyes kérések hogyan lett engedélyezve. A naplók jelzik, hogy a kérés névtelenül, OAuth 2.0-jogkivonattal, megosztott kulccsal vagy közös hozzáférésű jogosultságokkal (SAS) történt-e.  Emellett a Azure Monitor riasztást is használhat a tárfiókok névtelen hitelesítési feltételt használó névtelen hozzáféréséhez. 

 
- [Riasztások létrehozása az Azure-tevékenységnapló eseményeihez](../../azure-monitor/alerts/alerts-activity-log.md) 

 
- [Azure Storage Analytics-naplózás](storage-analytics-logging.md) 

 
- [Metrikákra vonatkozó riasztások konfigurálása Azure Storage-fiókokhoz](manage-storage-analytics-logs.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

## <a name="vulnerability-management"></a>Biztonságirés-kezelés

*További információ: [Azure biztonsági teljesítményteszt: Biztonsági rések kezelése.](../../security/benchmarks/security-control-vulnerability-management.md)*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1: Automatizált sebezhetőség-ellenőrzési eszközök futtatása

**Útmutató:** Kövesse a Azure Security Center a tárfiókok konfigurációjának folyamatos naplózására és figyelése érdekében.  

- [Biztonsági javaslatok – gyorsútmutató](../../security-center/recommendations-reference.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4: A biztonsági rések biztonsági vizsgálatának összehasonlítása

**Útmutató:** Nem alkalmazható; A Microsoft elvégzi a biztonsági rések kezelését a Tárfiókokat támogató mögöttes rendszereken.

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5: Kockázatminősítési folyamat használata a felderített biztonsági rések szervizelésének rangsorolására

**Útmutató:** Használja a biztonsági szolgáltatások által biztosított alapértelmezett Azure Security Center. 

- [A Azure Security Center pontszámok](/azure/security-center/security-center-secure-score)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

## <a name="inventory-and-asset-management"></a>Leltár-és eszközfelügyelet

*További információ: [Azure biztonsági teljesítményteszt: Leltár és eszközkezelés.](../../security/benchmarks/security-control-inventory-asset-management.md)*

### <a name="61-use-automated-asset-discovery-solution"></a>6.1: Automatizált eszközfelderítési megoldás használata

**Útmutató:** A Azure Resource Graph használatával lekérdezheti és felderítheti az előfizetés(ek) összes erőforrását (beleértve a Storage-fiókokat is). Győződjön meg arról, hogy rendelkezik a megfelelő (olvasási) engedélyekkel a bérlőben, és képes enumerálni az összes Azure-előfizetést és az előfizetésen belüli erőforrásokat.

- [Lekérdezések létrehozása az Azure Graph használatával](../../governance/resource-graph/first-query-portal.md)

- [Az Azure-előfizetések megtekintése](/powershell/module/az.accounts/get-azsubscription)

- [Az Azure RBAC](../../role-based-access-control/overview.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="62-maintain-asset-metadata"></a>6.2: Objektum metaadatainak karbantartása

**Útmutató:** Címkék alkalmazása a Tárfiók erőforrásaira, amelyek metaadatokat adva logikailag rendszerezik őket egy taxonóniában. 

- [Címkék létrehozása és használata](../../azure-resource-manager/management/tag-resources.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: Nem engedélyezett Azure-erőforrások törlése

**Útmutató:** A tárfiókok és a kapcsolódó erőforrások rendszerezéséhez és nyomon követéséhez használjon címkézést, felügyeleti csoportokat és különálló előfizetéseket, ha szükséges. Rendszeresen egyeztetni kell a leltárt, és gondoskodni kell arról, hogy a jogosulatlan erőforrások időben törlődjenek az előfizetésből. 

A Storage-Azure Defender is használhatja a jogosulatlan Azure-erőforrások észlelésére. 

- [További Azure-előfizetések létrehozása](../../cost-management-billing/manage/create-subscription.md) 

- [Új Management Groups](../../governance/management-groups/create-management-group-portal.md)

- [Címkék létrehozása és használata](../../azure-resource-manager/management/tag-resources.md)

- [Az Azure Defender tároláshoz konfigurálása](azure-defender-storage-configure.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6.4: Jóváhagyott Azure-erőforrások meghatározása és leltározása

**Útmutató:** Létre kell hoznia egy leltárt a jóváhagyott Azure-erőforrásokról a szervezeti igényeknek megfelelően.

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: Nem jóváhagyott Azure-erőforrások figyelése

**Útmutató:** Azure Policy az alábbi beépített szabályzatdefiníciók használatával az ügyfél-előfizetésben létre lehet hozható erőforrások típusára vonatkozó korlátozásokat: 

 
 - Nem engedélyezett erőforrástípusok 
 
 - Engedélyezett erőforrástípusok 
 

 
Emellett a Azure Resource Graph az előfizetések erőforrásainak lekérdezéséhez és felderítéséhez. Ez segíthet a magas biztonságon alapuló környezetekben, például a Storage-fiókokkal rendelkezőkben. 
 

 
- [Az Azure Policy konfigurálása és kezelése](../../governance/policy/tutorials/create-and-manage.md) 
 

 
- [Lekérdezések létrehozása az Azure Graph használatával](../../governance/resource-graph/first-query-portal.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7: Nem jóváhagyott Azure-erőforrások és -szoftveralkalmazások eltávolítása

**Útmutató:** Az ügyfél megakadályozhatja az erőforrás-létrehozást vagy Azure Policy az ügyfél vállalati szabályzata által előírtak szerint. 

- [Az Azure Policy konfigurálása és kezelése](../../governance/policy/tutorials/create-and-manage.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="69-use-only-approved-azure-services"></a>6.9: Csak jóváhagyott Azure-szolgáltatások használata

**Útmutató:** Azure Policy az alábbi beépített szabályzatdefiníciók használatával az ügyfél-előfizetésben létre lehet hozható erőforrások típusára vonatkozó korlátozásokat:

- Nem engedélyezett erőforrástípusok
- Engedélyezett erőforrástípusok

További információk a hivatkozott hivatkozásokon érhetők el.

- [Az Azure Policy konfigurálása és kezelése](../../governance/policy/tutorials/create-and-manage.md)

- [Adott erőforrástípus megtagadása a Azure Policy](https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#general)

**Felelősség**: Ügyfél

**Azure Security Center monitorozása:** Az [Azure Biztonsági](/azure/governance/policy/samples/azure-security-benchmark) teljesítményteszt a Security Center alapértelmezett szabályzati kezdeményezése, amely a Security Center [javaslatainak alapja.](/azure/security-center/security-center-recommendations) A Azure Policy vezérlővel kapcsolatos összes definíciót automatikusan engedélyezi a Security Center. Az ehhez a vezérlőhez kapcsolódó riasztások esetében szükség [lehet Azure Defender](/azure/security-center/azure-defender) kapcsolódó szolgáltatásokra vonatkozó tervre.

**Azure Policy definíciók – Microsoft.ClassicStorage:**

[!INCLUDE [Resource Policy for Microsoft.ClassicStorage 6.9](../../../includes/policy/standards/asb/rp-controls/microsoft.classicstorage-6-9.md)]

**Azure Policy definíciók – Microsoft.Storage:**

[!INCLUDE [Resource Policy for Microsoft.Storage 6.9](../../../includes/policy/standards/asb/rp-controls/microsoft.storage-6-9.md)]

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6.11: Korlátozza a felhasználók számára a Azure Resource Manager

**Útmutató:** Az Azure feltételes hozzáférés használatával korlátozhatja a felhasználók Azure Resource Manager-hozzáférését a "hozzáférés blokkolása" beállításával a "Microsoft Azure Management" alkalmazáshoz. Ez megakadályozhatja az erőforrások létrehozását és változásait a magas biztonsági szintű környezetben, például a Storage-fiókokkal rendelkezőkben. 

- [Feltételes hozzáférés konfigurálása a hozzáférés letilt Azure Resource Manager](../../role-based-access-control/conditional-access-azure-management.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

## <a name="secure-configuration"></a>Biztonságos konfiguráció

*További információ: [Azure biztonsági teljesítményteszt: Biztonságos konfiguráció.](../../security/benchmarks/security-control-secure-configuration.md)*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: Biztonságos konfigurációk létrehozása az összes Azure-erőforráshoz

**Útmutató:** Azure Policy **a Microsoft.Storage** névtérben az aliasok használatával egyéni szabályzatokat hozhat létre a Storage-fiókpéldányok konfigurációjának naplózására vagy kényszerítére. Az Azure Storage-fiók beépített Azure Policy is használható, például:

- Tárfiókok korlátlan hálózati hozzáférésének naplózása
- A Azure Defender for Storage üzembe helyezése
- A tárfiókokat át kell migrálni az új Azure Resource Manager erőforrásokba
- Engedélyezni kell a tárfiókokba történő biztonságos átvitelt

A tárfiókok Azure Security Center alapkonfigurációként használhatja a tárfiókok javaslatait.

- [Az elérhető aliasok Azure Policy megtekintése](/powershell/module/az.resources/get-azpolicyalias)

- [Az Azure Policy konfigurálása és kezelése](../../governance/policy/tutorials/create-and-manage.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: Biztonságos Azure-erőforráskonfigurációk fenntartása

**Útmutató:** A Azure Policy [deny] és a [deploy if not exist] használatával kényszerítheti a biztonságos beállításokat a tárfiók erőforrásain. 

- [Az Azure Policy konfigurálása és kezelése](../../governance/policy/tutorials/create-and-manage.md) 

- [A Azure Policy hatásainak ismertetése](../../governance/policy/concepts/effects.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Az Azure-erőforrások konfigurációjának biztonságos tárolása

**Útmutató:** Az Azure Repos használatával biztonságosan tárolhatja és kezelheti a kódot, például egyéni Azure-szabályzatokat, Azure Resource Manager sablonokat, Desired State Configuration szkripteket stb. Az Azure DevOpsban kezelett erőforrások eléréséhez engedélyeket adhat vagy tagadhat meg adott felhasználóknak, beépített biztonsági csoportoknak vagy az Azure Active Directory-ben (Azure AD) meghatározott csoportoknak, ha integrálva vannak az Azure DevOps-ral, vagy az Azure AD-nek, ha integrálva van a TFS-sel.

- [Kód tárolása az Azure DevOpsban](/azure/devops/repos/git/gitworkflow)

- [Az Azure DevOps engedélyeinek és csoportjainak](/azure/devops/organizations/security/about-permissions)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7: Konfigurációkezelési eszközök üzembe helyezése Azure-erőforrásokhoz

**Útmutató:** A Azure Policy a Storage-fiók rendszerkonfigurációinak riasztására, naplózására és kényszerítésére. Emellett dolgozzon ki egy folyamatot és folyamatot a szabályzati kivételek kezeléséhez. 

- [ Az alkalmazások konfigurálása és Azure Policy](../../governance/policy/tutorials/create-and-manage.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9: Automatizált konfigurációfigyelés megvalósítása az Azure-erőforrásokhoz

**Útmutató:** A Azure Security Center az Azure Storage-fiók erőforrásainak alapkonfiguráció-vizsgálatára. 

- [Javaslatok szervizlének Azure Security Center](../../security-center/security-center-remediate-recommendations.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="711-manage-azure-secrets-securely"></a>7.11: Azure-beli titkos kulcsok biztonságos kezelése

**Útmutató:** Az Azure Storage automatikusan titkosítja az adatokat, amikor a felhőben megőrzik őket. Használhatja a Microsoft által felügyelt kulcsokat a tárfiók titkosításhoz, vagy kezelheti a titkosítást a saját kulcsokkal. Ha ügyfél által biztosított kulcsokat használ, a Azure Key Vault biztonságosan tárolhatja őket. 

Emellett rendszeresen váltsa át a tárfiókkulcsokat, hogy korlátozza a tárfiókkulcsok elvesztésének vagy felfedésének hatását.

- [Inaktív adatok Azure Storage-titkosítása](storage-service-encryption.md)

- [Tárfiók hozzáférési kulcsának kezelése](storage-account-keys-manage.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: Identitások biztonságos és automatikus kezelése

**Útmutató:** Blobokhoz és üzenetsorokhoz való hozzáférés jogosultsága az Azure Storage-fiókokban Azure Active Directory (Azure AD) és felügyelt identitásokkal. Az Azure Blob and Queue Storage támogatja az Azure AD-hitelesítést az Azure-erőforrások felügyelt identitásával. 

Az Azure-erőforrások felügyelt identitásai Azure AD hitelesítő adatokkal engedélyezhetik a blob- és üzenetsoradatokhoz való hozzáférést az Azure Virtual Machines r-ban, függvényalkalmazásokban, virtuálisgép-méretezési készletekben és más szolgáltatásokban futó alkalmazásokból. Az Azure-erőforrások felügyelt identitásai és az Azure AD-hitelesítés együttes használatával elkerülheti a hitelesítő adatok tárolását a felhőben futó alkalmazásokban.

- [Azure-blob- és üzenetsoradatokhoz való hozzáférés megadása felügyelt identitás használatával](storage-auth-aad-rbac-portal.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: A hitelesítő adatok nem szándékolt kitettségének megszüntetése

**Útmutató:** Hitelesítőadat-ellenőrző implementálja a hitelesítő adatokat a kódban. A Credential Scanner a felfedezett hitelesítő adatok biztonságosabb helyre, például az Azure Key Vaultba való áthelyezésére is javaslatot tesz. 

- [ Hitelesítőadat-ellenőrző beállítása](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

## <a name="malware-defense"></a>Kártevők elleni védelem

*További információ: [Azure biztonsági teljesítményteszt: Kártevők elleni védelem.](../../security/benchmarks/security-control-malware-defense.md)*

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2: A nem számítási Azure-erőforrásokba feltölteni szükséges fájlok előzetes beolvasása

**Útmutató:** A Azure Defender for Storage használatával észlelheti az Azure Storage-ba történő kártevőfeltöltéseket a kivonatok jó hírnevének elemzésével és egy aktív Tor kilépési csomópontból (anonimizálási proxy) származó gyanús hozzáféréssel. 
 

 
A nem számítási Azure-erőforrásokra ( például App Service, Data Lake Storage, Blob Storage és mások) való feltöltés előtt előzetesen is átvizsgálhatja a kártevőket, hogy megfeleljen a szervezet követelményeinek.
 

 
- [Az Azure Defender tároláshoz konfigurálása](azure-defender-storage-configure.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

## <a name="data-recovery"></a>Adat-helyreállítás

*További információ: [Azure biztonsági teljesítményteszt: Adat-helyreállítás.](../../security/benchmarks/security-control-data-recovery.md)*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: Rendszeres automatikus biztonságimentek biztosítása

**Útmutató:** A tárfiókban Microsoft Azure adatok replikálása mindig automatikusan megtörténik a tartósság és a magas rendelkezésre állás biztosítása érdekében. Az Azure Storage úgy másolja az adatokat, hogy védve vannak a tervezett és nem tervezett eseményektől, beleértve az átmeneti hardverhibákat, a hálózati vagy áramkimaradásokat és a súlyos természeti katasztrófákat. Az adatokat replikálhatja ugyanazon az adatközponton belül, egy régión belüli zónaközi adatközpontokban vagy földrajzilag elkülönített régiókban. 

Azt is engedélyezheti, hogy az Azure Automation rendszeres pillanatképeket készít a blobokról.

- [Az Azure Storage redundanciával és az Service-Level szerződésekkel kapcsolatos tudnivalók](storage-redundancy.md)

- [Blob pillanatképének létrehozása](/rest/api/storageservices/creating-a-snapshot-of-a-blob)

- [Azure Automation áttekintés](../../automation/automation-intro.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: Teljes rendszer-biztonsági mentések és az ügyfelek által kezelt kulcsok biztonsági mentése

**Útmutató:** A Tárfiók támogatott szolgáltatásaiból származó adatok biztonságimásolat-ának többféle módja is van, például az azcopy vagy külső eszközök használata. Az Azure Blob Storage nem módosítható tárolója lehetővé teszi a felhasználók számára, hogy az üzleti szempontjából kritikus adatobjektumokat WORM (Egyszer írható, több olvasás) állapotban tárolják. Ez az állapot a felhasználó által megadott időtartamig nem törölhetővé és nem módosíthatóvé teszi az adatokat.
 

Az ügyfél által felügyelt/megadott kulcsok biztonsági Azure Key Vault Azure CLI vagy PowerShell használatával. 

 
- [Bevezetés az AzCopy használatába](storage-use-azcopy-v10.md)  

- [Nem módosíthatósági szabályzatok beállítása és kezelése a Blob Storage-hez](../blobs/storage-blob-immutability-policies-manage.md)
 

 
- [Kulcstartókulcsok biztonsági mentése az Azure-ban](/powershell/module/az.keyvault/backup-azkeyvaultkey)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: Az összes biztonsági másolat ellenőrzése, beleértve az ügyfél által kezelt kulcsokat is

**Útmutató:** Az alábbi PowerShell-parancsokkal rendszeresen végrehajtja a Key Vault tanúsítványok, kulcsok, felügyelt tárfiókok és titkos kulcsok adatainak visszaállítását:

Restore-AzKeyVaultCertificate

Restore-AzKeyVaultKey

Restore-AzKeyVaultManagedStorageAccount

Restore-AzKeyVaultSecret

- [A tanúsítványok Key Vault visszaállítása](/powershell/module/az.keyvault/restore-azkeyvaultcertificate)

- [Új kulcsok Key Vault visszaállítása](/powershell/module/az.keyvault/restore-azkeyvaultkey)

- [Felügyelt tárfiókok Key Vault visszaállítása](/powershell/module/az.keyvault/backup-azkeyvaultmanagedstorageaccount)

- [Titkos kulcsok Key Vault visszaállítása](/powershell/module/az.keyvault/restore-azkeyvaultsecret)

- [Az AzCopy egy parancssori segédprogram, amely blobok, fájlok és táblaadatok tárfiókba vagy tárfiókból való másolására használható](storage-use-azcopy-v10.md)

Megjegyzés: Ha adatokat szeretne másolni az Azure Table Storage szolgáltatásba és onnan másra, telepítse az AzCopy 7.3-as verzióját.

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: A biztonsági másolatok és az ügyfél által kezelt kulcsok védelmének biztosítása

**Útmutató:** Ha engedélyezni szeretné az ügyfél által felügyelt kulcsokat egy tárfiókon, a kulcsokat Azure Key Vault kell használnia. A kulcstartón engedélyeznie kell a Soft Delete és a Do Not Purge (Nem végleges törlés) tulajdonságokat is.  Key Vault helyreállító törlés funkciója lehetővé teszi a törölt tárolók és tárolóobjektumok, például kulcsok, titkos kulcsok és tanúsítványok helyreállítását.  Ha a Storage-fiók adatait Azure Storage-blobokba menti, engedélyezze a helyreállítható törlést az adatok mentéséhez és helyreállításához blobok vagy blob-pillanatképek törlésekor.  A biztonsági másolatokat bizalmas adatokként kell kezelnie, és ennek az alapkonfigurációnak a részeként alkalmaznia kell a megfelelő hozzáférési és adatvédelmi vezérlőket.  A nagyobb védelem érdekében emellett az üzletileg kritikus adatobjektumokat WORM (Egyszer írható, Több olvasás) állapotban is tárolhatja.

- [A Azure Key Vault törlési parancsának használata](../../key-vault/general/key-vault-recovery.md)

- [Az Azure Storage-blobok helyreállítható törlése](../blobs/soft-delete-blob-overview.md)

- [Az üzlet szempontjából létfontosságú blobadatok tárolása nem módosítható tárolással](../blobs/storage-blob-immutable-storage.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

## <a name="incident-response"></a>Incidensmegoldás

*További információ: [Azure Security Benchmark: Incidensek kezelése](../../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10.1: Incidensválasz-útmutató létrehozása

**Útmutató**: Alakítson ki incidenskezelési útmutatót a vállalat számára. Gondoskodjon írásos incidenskezelési tervekről, amelyek definiálják az összes résztvevő szerepkörét, valamint az incidenskezelés fázisait az észleléstől az incidens utáni értékelésig.

- [Útmutatás saját biztonsági incidensek válaszadási folyamatának építéshez](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Microsoft Biztonsági reagálási központ az incidensek felépítése](https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/)

- [Az NIST számítógépes biztonsági incidenskezelési útmutatója](https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: Incidenspontozási és -rangsorolási eljárás létrehozása

**Útmutató:** Security Center súlyosságot rendel az egyes riasztásokhoz, így fontossági sorrendbe adhatja, hogy mely riasztásokat kell először megvizsgálni. A súlyosság azon alapul, Security Center mennyire biztos a riasztás kiadásához használt eredményben vagy elemzésben, valamint hogy a riasztáshoz vezető tevékenység mögött rosszindulatú szándék volt-e. 

 
Emellett egyértelműen jelöljük meg az előfizetéseket (például: éles környezetben, nem éles környezetben) címkéket használ, és létrehoz egy elnevezési rendszert az Azure-erőforrások egyértelmű azonosításához és kategorizálásához, különösen a bizalmas adatokat feldolgozók számára.  Az Ön felelőssége, hogy rangsorolja a riasztások megoldását azon Azure-erőforrások és -környezetek kritikussága alapján, ahol az incidens történt.
 

 
- [Biztonsági riasztások az Azure Security Centerben](../../security-center/security-center-alerts-overview.md)
 

 
- [Címkék használata az Azure-erőforrások rendszerezéséhez](../../azure-resource-manager/management/tag-resources.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="103-test-security-response-procedures"></a>10.3: Biztonsági reagálási eljárások tesztelése

**Útmutató:** Gyakorlatok a rendszerek incidensválasz-képességeinek rendszeres teszteléséhez az Azure-erőforrások védelme érdekében. Azonosítsa a gyenge pontokat és réseket, és szükség esetén dolgozza át a tervet.

- [NIST-kiadvány – Útmutató az IT-tervekhez és -képességekhez szükséges tesztelési, képzési és gyakorlatprogramokhoz](https://csrc.nist.gov/publications/detail/sp/800-84/final)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: Adja meg a biztonsági incidens kapcsolattartási adatait, és konfigurálja a riasztási értesítéseket a biztonsági incidensek esetén

**Útmutató:** A Biztonsági incidens kapcsolattartási adatait a Microsoft arra használja, hogy kapcsolatba lépjen Önnel, ha a Microsoft Biztonsági reagálási központ (MSRC) azt tapasztalja, hogy illetéktelen vagy jogosulatlan fél fért hozzá adataihoz. Tekintse át az incidenseket a probléma megoldása érdekében.

- [A biztonsági kapcsolattartó Azure Security Center beállítása](../../security-center/security-center-provide-security-contact-details.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: Biztonsági riasztások beépítése az incidensválasz-rendszerbe

**Útmutató:** A riasztások Azure Security Center és javaslatokat exportálhatja a Continuous Export (Folyamatos exportálás) funkcióval az Azure-erőforrásokat érintő kockázatok azonosítása érdekében. A folyamatos exportálás lehetővé teszi, hogy manuálisan vagy folyamatosan exportálja a riasztásokat és javaslatokat. A riasztásokat a Azure Security Center adat-összekötővel streamelheti a Azure Sentinel.

- [Folyamatos exportálás konfigurálása](../../security-center/continuous-export.md)

- [Riasztások streamelése az Azure Sentinelbe](../../sentinel/connect-azure-security-center.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: A biztonsági riasztások válaszának automatizálása

**Útmutató:** A Azure Security Center-munkafolyamat-automatizálási funkcióval automatikusan aktiválhatja a válaszokat a biztonsági riasztások Logic Apps javaslatokon keresztül az Azure-erőforrások védelme érdekében.
    

- [ A munkafolyamat-automatizálás és -Logic Apps](../../security-center/workflow-automation.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

## <a name="penetration-tests-and-red-team-exercises"></a>Behatolási tesztek és Red Team-gyakorlatok

*További információ: [Azure biztonsági teljesítményteszt: Behatolási tesztek és Red Team-gyakorlatok.](../../security/benchmarks/security-control-penetration-tests-red-team-exercises.md)*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11.1: Az Azure-erőforrások rendszeres behatolástesztelése és az összes kritikus fontosságú biztonsági eredmény szervizelésének biztosítása

**Útmutató:** Kövesse a Microsoft kötelezettségvállalási szabályait annak biztosításához, hogy a behatolási tesztek ne sértsen meg Microsoft-szabályzatokat. Használja a Microsoft red teaming és élő webhely-behatolási tesztek Microsoft által felügyelt felhőinfra infrastruktúrája, szolgáltatásai és alkalmazásai elleni stratégiáját és végrehajtását.

- [Behatolástesztelési beavatkozási szabályok](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)

- [Riasztási tesztek a Microsoft-felhőben](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Felelősség**: Megosztott

**Azure Security Center:** Nincs

## <a name="next-steps"></a>Következő lépések

- [Az Azure Security Benchmark v2 áttekintésének](/azure/security/benchmarks/overview) megtekintése
- További tudnivalók az [Azure biztonsági alapterveiről](/azure/security/benchmarks/security-baselines-overview)
