---
title: Az Azure biztonsági alapkonfigurációja Azure Cache for Redis
description: A Azure Cache for Redis alapkonfiguráció eljárásokkal kapcsolatos útmutatást és forrásforrásokat biztosít az Azure biztonsági teljesítménytesztben meghatározott biztonsági javaslatok megvalósításához.
author: msmbaldwin
ms.service: cache
ms.topic: conceptual
ms.date: 02/17/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark, devx-track-azurepowershell
ms.openlocfilehash: 0397f53c54f488f6840e35212de3e51624f360d7
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2021
ms.locfileid: "107829997"
---
# <a name="azure-security-baseline-for-azure-cache-for-redis"></a>Az Azure biztonsági alapkonfigurációja Azure Cache for Redis

Ez a biztonsági alapkonfiguráció az Azure Biztonsági teljesítményteszt [1.0-s](../security/benchmarks/overview-v1.md) verziójának útmutatását alkalmazza a Azure Cache for Redis. Az Azure Security Benchmark ajánlásokat ad arra nézve, hogy hogyan tehetők biztonságossá a felhőalapú megoldások az Azure-ban.
A tartalmat az Azure  biztonsági teljesítményteszt által meghatározott biztonsági vezérlők és a kapcsolódó, az alkalmazásra vonatkozó útmutatás Azure Cache for Redis. **A** Azure Cache for Redis nem alkalmazható vezérlők ki vannak zárva.

 
Ha meg Azure Cache for Redis, hogyan lehet teljes mértékben leképezni az Azure-biztonsági teljesítménytesztre, tekintse meg a teljes Azure Cache for Redis alapkonfiguráció [leképezési fájlját.](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)

## <a name="network-security"></a>Hálózati biztonság

*További információ: [Azure Security Benchmark: Hálózati biztonság](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1.1: Azure-erőforrások védelme virtuális hálózatokon belül

**Útmutató:** Azure Cache for Redis virtuális hálózaton (VNeten) belül üzembe helyezheti a saját virtuális gépét. A VNet egy magánhálózat a felhőben. Ha egy Azure Cache for Redis-példány VNettel van konfigurálva, az nyilvánosan nem címezhető, és csak a virtuális hálózaton belüli virtuális gépekről és alkalmazásokból érhető el.

Megadhat kezdő és záró IP-címtartományú tűzfalszabályokat is. Tűzfalszabályok konfigurálásakor csak a megadott IP-címtartományból származó ügyfélkapcsolatok csatlakozhatnak a gyorsítótárhoz.

- [Prémium szintű Virtual Network támogatási szolgáltatásának Azure Cache for Redis](cache-how-to-premium-vnet.md)

- [Tűzfalszabályok Azure Cache for Redis konfigurálása](./cache-configure.md#firewall)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-network-interfaces"></a>1.2: Virtuális hálózatok, alhálózatok és hálózati adapterek konfigurációjának és forgalmának figyelése és naplózása

**Útmutató:** Ha Virtual Machines a virtuális hálózatban vannak telepítve, mint a Azure Cache for Redis-példány, hálózati biztonsági csoportokkal (NSG)csökkentheti az adatok kiszivárgásának kockázatát. Engedélyezze az NSG forgalmi naplóit, és küldje el a naplókat egy Azure Storage-fiókba a forgalom naplózása érdekében. Az NSG-forgalom naplóit egy Log Analytics-munkaterületre is elküldheti, és a Traffic Analytics segítségével betekintést nyerhet az Azure-felhő adatforgalmába. A hálózati Traffic Analytics a hálózati tevékenységek vizualizációja és a gyors helyek azonosítása, a biztonsági fenyegetések azonosítása, a forgalmi minták azonosítása és a hálózati konfigurációk észlelése.

- [NSG-forgalom naplóinak engedélyezése](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [A Traffic Analytics](../network-watcher/traffic-analytics.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="13-protect-critical-web-applications"></a>1.3: Kritikus fontosságú webalkalmazások védelme

**Útmutató:** Nem alkalmazható; Ez a javaslat a számítási erőforrásokon futó webalkalmazások Azure App Service szánt.

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: Az ismert kártékony IP-címekkel való kommunikáció megtagadása

**Útmutató:** Az Azure Virtual Network (VNet) üzemelő példánya fokozott biztonságot és elkülönítést biztosít a Azure Cache for Redis számára, valamint alhálózatokat, hozzáférés-vezérlési szabályzatokat és egyéb szolgáltatásokat biztosít a hozzáférés további korlátozására. A virtuális hálózatban üzembe helyezett virtuális Azure Cache for Redis nyilvános címek nem érhetők el, és csak a virtuális hálózaton belüli virtuális gépekről és alkalmazásokból érhetők el.

Engedélyezze DDoS Protection Standard szolgáltatást a Azure Cache for Redis-példányokhoz társított virtuális hálózatokon, hogy védelmet nyújtson az elosztott szolgáltatásmegtagadásos (DDoS)-támadások ellen. Az Azure Security Center intelligencia használatával megtagadhatja az ismert rosszindulatú vagy nem használt internetes IP-címekkel való kommunikációt.

- [Prémium szintű Virtual Network támogatás konfigurálása Azure Cache for Redis](cache-how-to-premium-vnet.md)

- [A Azure DDoS Protection Standard kezelése a Azure Portal](../ddos-protection/manage-ddos-protection.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="15-record-network-packets"></a>1.5: Hálózati csomagok rekordja

**Útmutató:** Ha a virtuális gépeket a Azure Cache for Redis-példányával azonos virtuális hálózaton telepítik, hálózati biztonsági csoportokkal (NSG-ket) használhat az adatok kiszivárgásának kockázatának csökkentése érdekében. Engedélyezze az NSG forgalmi naplóit, és küldjön naplókat egy Azure Storage-fiókba a forgalom naplózása érdekében. Az NSG-forgalom naplóit egy Log Analytics-munkaterületre is elküldheti, és a Traffic Analytics segítségével betekintést nyerhet az Azure-felhő adatforgalmába. A hálózati Traffic Analytics néhány előnye a hálózati tevékenység vizualizációja és a gyorspontok azonosítása, a biztonsági fenyegetések azonosítása, a forgalmi minták azonosítása és a hálózati helytelen konfigurációk azonosítása.

- [NSG-forgalom naplóinak engedélyezése](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [A Traffic Analytics](../network-watcher/traffic-analytics.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6: Hálózatalapú behatolásészlelési/behatolásvédelmi rendszerek (IDS/IPS) üzembe helyezése

**Útmutató:** Ha Azure App Service-on vagy számítási példányon futó webalkalmazásokkal használja az Azure Cache for Redis-t, telepítsen minden erőforrást egy Azure Virtual Network-on (VNet) belül, és a webalkalmazások Azure Web Application Firewall (WAF) használatával biztonságossá Application Gateway. Konfigurálja a WAF-et úgy, hogy "Megelőzési módban" fusson. A Megelőzés mód blokkolja a szabályok által észlelt behatolásokat és támadásokat. A támadó egy "403-as jogosulatlan hozzáférés" kivételt kap, és bezárja a kapcsolatot. A megelőzési mód rögzíti az ilyen támadásokat a WAF-naplókban.

Másik lehetőségként választhat egy olyan ajánlatot a Azure Marketplace, amely támogatja az IDS/IPS funkciót hasznos adatok vizsgálatával és/vagy anomáliadetektálási képességekkel.

- [Az Azure WAF képességeinek](../web-application-firewall/ag/ag-overview.md)

- [Az Azure WAF üzembe helyezése](../web-application-firewall/ag/create-waf-policy-ag.md)

- [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/?term=Firewall)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="17-manage-traffic-to-web-applications"></a>1.7: Webalkalmazások forgalmának kezelése

**Útmutató:** Nem alkalmazható; ez a javaslat a számítási erőforrásokon futó webalkalmazások Azure App Service ajánlott.

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: A hálózati biztonsági szabályok összetettségének és adminisztratív terhelésének minimalizálása

**Útmutató:** Hálózati biztonsági csoportok (NSG-k) vagy hálózati biztonsági csoportok hálózati hozzáférés-vezérlésének meghatározása virtuális hálózati szolgáltatáscímkék Azure Firewall. Biztonsági szabályok létrehozása során szolgáltatáscímkéket használhat bizonyos IP-címek helyett. A szolgáltatáscímke nevének (például ApiManagement) a szabály megfelelő forrás- vagy célmezőben való megadásával engedélyezheti vagy megtagadhatja a megfelelő szolgáltatás forgalmát. A Microsoft kezeli a szolgáltatáscímke által felölelt címelőtagokat, és automatikusan frissíti a szolgáltatáscímkéket a címek változásával.

Alkalmazásbiztonsági csoportokat (ASG) is használhat az összetett biztonsági konfiguráció egyszerűsítésére. Az ASG-k lehetővé teszik, hogy a hálózati biztonságot az alkalmazás struktúrájának természetes kiterjesztéseként konfigurálja, így csoportosíthatja a virtuális gépeket, és ezen csoportok alapján hálózati biztonsági házirendeket határozhat meg.

- [Virtuális hálózati szolgáltatáscímkék](../virtual-network/service-tags-overview.md)

- [Alkalmazásbiztonsági csoportok](../virtual-network/network-security-groups-overview.md#application-security-groups)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: A hálózati eszközök szabványos biztonsági konfigurációjának fenntartása

**Útmutató:** Standard biztonsági konfigurációk definiálása és implementációja a Azure Cache for Redis-példányokhoz kapcsolódó hálózati Azure Policy. A Azure Policy a "Microsoft.Cache" és a "Microsoft.Network" névterek aliasait használva egyéni szabályzatokat hozhat létre, amelyek naplózza vagy kikényszeríteni a Azure Cache for Redis konfigurációját. Beépített szabályzatdefiníciókat is használhat, például:
- Csak a saját Redis Cache kell engedélyezni

- DDoS Protection standard beállítást engedélyezni kell

Az Azure Blueprints használatával leegyszerűsítheti a nagy méretű Azure-beli üzembe helyezéseket, mivel egyetlen tervdefinícióba csomagolásával egyetlen tervdefinícióba teheti a legfontosabb környezeti összetevők, például az Azure Resource Manager-sablonok, az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC) és a szabályzatok használatát. Könnyedén alkalmazhatja a tervet az új előfizetésekhez és környezetekhez, és finomhangolhatja a vezérlést és a felügyeletet a verziószámozás segítségével.

- [Az Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md)

- [Azure Blueprints-terv létrehozása](../governance/blueprints/create-blueprint-portal.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="110-document-traffic-configuration-rules"></a>1.10: A forgalom konfigurációs szabályainak dokumentálása

**Útmutató:** Az üzembe helyezéshez társított hálózati erőforrások címkéinek Azure Cache for Redis, hogy logikailag rendszerezze őket egy taxonómiába.

- [Címkék létrehozása és használata](../azure-resource-manager/management/tag-resources.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: Automatizált eszközök használata a hálózati erőforrások konfigurációjának figyelése és a változások észlelése érdekében

**Útmutató:** Az Azure-tevékenységnaplóval monitorozható a hálózati erőforrások konfigurációja, és észlelhetők az erőforráspéldányokhoz kapcsolódó hálózati Azure Cache for Redis változásai. Hozzon létre riasztásokat a Azure Monitor, amelyek akkor aktiválódnak, ha a kritikus hálózati erőforrások módosulnak.

- [Azure-tevékenységnapló eseményeinek megtekintése és lekérése](../azure-monitor/essentials/activity-log.md#view-the-activity-log)

- [Riasztások létrehozása a Azure Monitor](../azure-monitor/alerts/alerts-activity-log.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

## <a name="logging-and-monitoring"></a>Naplózás és monitorozás

*További információ: [Azure biztonsági teljesítményteszt: Naplózás és monitorozás.](../security/benchmarks/security-control-logging-monitoring.md)*

### <a name="22-configure-central-security-log-management"></a>2.2: Központi biztonsági naplókezelés konfigurálása

**Útmutató:** Engedélyezze az Azure-tevékenységnapló diagnosztikai beállításait, és küldje el a naplókat egy Log Analytics-munkaterületre, egy Azure-eseményközpontba vagy egy Azure Storage-fiókba archiválás céljából. A tevékenységnaplók betekintést nyújtanak a Azure Cache for Redis a vezérlősík szintjén végrehajtott műveletekbe. Az Azure-tevékenységnapló adataival meghatározhatja a vezérlősík szintjén végrehajtott írási műveletek (PUT, POST, DELETE) "mit, ki és mikor" Azure Cache for Redis számára.

- [Diagnosztikai beállítások engedélyezése az Azure-tevékenységnaplóhoz](../azure-monitor/essentials/activity-log.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Azure-erőforrások naplózásának engedélyezése

**Útmutató:** Engedélyezze az Azure-tevékenységnapló diagnosztikai beállításait, és küldje el a naplókat egy Log Analytics-munkaterületre, egy Azure-eseményközpontba vagy egy Azure Storage-fiókba archiválás céljából. A tevékenységnaplók betekintést nyújtanak a Azure Cache for Redis a vezérlősík szintjén végrehajtott műveletekbe. Az Azure-tevékenységnapló adataival meghatározhatja a vezérlősík szintjén végrehajtott írási műveletek (PUT, POST, DELETE) "mit, ki és mikor" Azure Cache for Redis számára.

Bár a metrikák a Diagnosztikai beállítások engedélyezésével érhetők el, az adatsíkon végzett naplózás még nem érhető el a Azure Cache for Redis.

- [Diagnosztikai beállítások engedélyezése az Azure-tevékenységnaplóhoz](../azure-monitor/essentials/activity-log.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="25-configure-security-log-storage-retention"></a>2.5: Biztonsági naplók tárolásának konfigurálása

**Útmutató:** A Azure Monitor a vállalat megfelelőségi szabályzatainak megfelelően állítsa be az Azure Cache for Redis példányokhoz társított Log Analytics-munkaterületek naplómegőrzési megőrzési időszakát.

Vegye figyelembe, hogy az adatsíkon végzett naplózás még nem érhető el a Azure Cache for Redis.

- [Naplómegőrzési paraméterek beállítása](../azure-monitor/logs/manage-cost-storage.md#change-the-data-retention-period)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="26-monitor-and-review-logs"></a>2.6: Naplók figyelése és áttekintése

**Útmutató:** Engedélyezze az Azure-tevékenységnapló diagnosztikai beállításait, és küldje el a naplókat egy Log Analytics-munkaterületre. Végezzen lekérdezéseket a Log Analyticsben a kifejezések kereséséhez, a trendek azonosításához, a minták elemzéséhez, valamint számos más, a naplózási szolgáltatáshoz gyűjtött tevékenységnapló-adatokon alapuló Azure Cache for Redis.

Vegye figyelembe, hogy az adatsíkon végzett naplózás még nem érhető el a Azure Cache for Redis.

- [Diagnosztikai beállítások engedélyezése az Azure-tevékenységnaplóhoz](../azure-monitor/essentials/activity-log.md)

- [Azure-tevékenységnaplók gyűjtése és elemzése a Log Analytics-munkaterületen a Azure Monitor](../azure-monitor/essentials/activity-log.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7: Riasztások engedélyezése rendellenes tevékenységekhez

**Útmutató:** Konfigurálhatja úgy a konfigurációt, hogy riasztásokat fogad a saját példányaihoz kapcsolódó metrikák és tevékenységnaplók Azure Cache for Redis alapján. Azure Monitor konfigurálhat egy riasztást, amely e-mailes értesítést küld, meghív egy webhookot vagy meghív egy Azure Logic App-alkalmazást.

Bár a metrikák a Diagnosztikai beállítások engedélyezésével érhetők el, az adatsíkon végzett naplózás még nem érhető el a Azure Cache for Redis.

- [Riasztások konfigurálása a Azure Cache for Redis](./cache-how-to-monitor.md#alerts)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

## <a name="identity-and-access-control"></a>Identitás- és hozzáférés-vezérlés

*További információ: [Azure Biztonsági teljesítményteszt: Identitás és Access Control.](../security/benchmarks/security-control-identity-access-control.md)*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: A rendszergazdai fiókok leltárának fenntartása

**Útmutató:** Azure Active Directory (Azure AD) beépített szerepkörökkel rendelkezik, amelyekhez explicit módon kell hozzárendelni és lekérdezhetőnek kell lennie. Az Azure AD PowerShell-modullal alkalmi lekérdezéseket hajt végre a rendszergazdai csoportok tagjainak felderítéséhez.

- [Címtárszerepkepkreta az Azure AD-ban a PowerShell használatával](/powershell/module/azuread/get-azureaddirectoryrole?preserve-view=true&view=azureadps-2.0)

- [Címtárbeli szerepkör tagjainak lekért használata az Azure AD-ban a PowerShell használatával](/powershell/module/azuread/get-azureaddirectoryrolemember?preserve-view=true&view=azureadps-2.0)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="32-change-default-passwords-where-applicable"></a>3.2: Az alapértelmezett jelszavak módosítása, ha vannak

**Útmutató:** A vezérlősík hozzáférése Azure Cache for Redis vezérlése Azure Active Directory (Azure AD) használatával történik. Az Azure AD nem rendelkezik az alapértelmezett jelszavak fogalmával.

Az adatsík hozzáférése Azure Cache for Redis hozzáférési kulcsokkal szabályozható. Ezeket a kulcsokat a gyorsítótárhoz csatlakozó ügyfelek használják, és bármikor újragenerálhatóak.

Nem ajánlott alapértelmezett jelszavakat építeni az alkalmazásba. Ehelyett a jelszavakat tárolhatja a Azure Key Vault, majd az Azure AD használatával lekérheti őket.

- [Hozzáférési kulcsok Azure Cache for Redis újragenerálásának mikéntje](./cache-configure.md#settings)

**Felelősség**: Megosztott

**Azure Security Center monitorozás:** Nincs

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: Dedikált rendszergazdai fiókok használata

**Útmutató:** Szabványos üzemeltetési eljárások létrehozása a dedikált rendszergazdai fiókok használatával kapcsolatban. A Azure Security Center és hozzáférés-kezelés használatával figyelheti a rendszergazdai fiókok számát.

Emellett a dedikált rendszergazdai fiókok nyomon követéséhez használhatja a Azure Security Center vagy beépített Azure-szabályzatok javaslatait, például:

- Az előfizetéshez egynél több tulajdonosnak kell hozzárendelve lennie

- A tulajdonosi engedélyekkel rendelkező elavult fiókokat el kell távolítani az előfizetésből

- A tulajdonosi engedélyekkel rendelkező külső fiókokat el kell távolítani az előfizetésből

További információkat az alábbi hivatkozásokon találhat:

- [A Azure Security Center identitás és hozzáférés figyelése (előzetes verzió)](../security-center/security-center-identity-access.md)

- [A Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="34-use-azure-active-directory-single-sign-on-sso"></a>3.4: Egyszeri Azure Active Directory (SSO) használata

**Útmutató:** Azure Cache for Redis hozzáférési kulcsokat használ a felhasználók hitelesítéséhez, és nem támogatja az egyszeri bejelentkezést (SSO) az adatsík szintjén. A vezérlősíkhoz való hozzáférés a Azure Cache for Redis elérhető a REST API támogatja az SSO-t. A hitelesítéshez állítsa be a kérések Engedélyezési fejlécét egy olyan JSON-webtoken, amely az Azure AD Azure Active Directory beszerezhető.

- [A Azure Cache for Redis REST API](/rest/api/redis/)

- [Az SSO és az Azure AD](../active-directory/manage-apps/what-is-single-sign-on.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: Többtényezős hitelesítés használata minden Azure Active Directory-alapú hozzáféréshez

**Útmutató:** Engedélyezze Azure Active Directory (Azure AD) többtényezős hitelesítést, és kövesse Azure Security Center identitás- és hozzáférés-kezelésre vonatkozó javaslatokat.

- [Többtényezős hitelesítés engedélyezése az Azure-ban](../active-directory/authentication/howto-mfa-getstarted.md)

- [Identitás és hozzáférés figyelése a Azure Security Center](../security-center/security-center-identity-access.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: Dedikált gépek (Emelt szintű hozzáférésű munkaállomások) használata az összes felügyeleti feladathoz

**Útmutató:** Emelt szintű hozzáférésű munkaállomások (PAW) használata az Azure-erőforrásokba való bejelentkezéshez és konfiguráláshoz konfigurált többtényezős hitelesítéssel. 

- [Tudnivalók a Privileged Access munkaállomásról](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

- [Többtényezős hitelesítés engedélyezése az Azure-ban](../active-directory/authentication/howto-mfa-getstarted.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3.7: A rendszergazdai fiókokból származó gyanús tevékenységek naplózása és riasztása

**Útmutató:** A Azure Active Directory (Azure AD) Privileged Identity Management (PIM) használatával naplókat és riasztásokat generál, ha gyanús vagy nem biztonságos tevékenység történik a környezetben.

Emellett az Azure AD kockázatészlelései segítségével megtekintheti a kockázatos felhasználói viselkedéssel kapcsolatos riasztásokat és jelentéseket.

- [A Privileged Identity Management (PIM) üzembe helyezése](../active-directory/privileged-identity-management/pim-deployment-plan.md)

- [Az Azure AD kockázatészlelései](../active-directory/identity-protection/overview-identity-protection.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: Azure-erőforrások kezelése csak jóváhagyott helyekről

**Útmutató:** Elnevezett helyek konfigurálása a Azure Active Directory (Azure AD) feltételes hozzáférésében, hogy csak az IP-címtartományok vagy országok/régiók meghatározott logikai csoportosításai számára engedélyezze a hozzáférést.

- [Elnevezett helyek konfigurálása az Azure-ban](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="39-use-azure-active-directory"></a>3.9: A Azure Active Directory

**Útmutató:** A Azure Active Directory (Azure AD) használata központi hitelesítési és engedélyezési rendszerként. Az Azure AD erős titkosítással védi az adatokat az átvitel alatt és az átvitel alatt. Az Azure AD emellett a felhasználói hitelesítő adatokat is megsokossa, hashes és biztonságosan tárolja.

Az Azure AD-hitelesítés nem használható az Azure Cache for Redis adatsíkhoz való közvetlen hozzáféréshez, azonban az Azure AD hitelesítő adatai a vezérlősík szintjén (például az Azure Portal) használhatók az Azure Cache for Redis hozzáférési kulcsok vezérléséhez.

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: A felhasználói hozzáférés rendszeres áttekintése és egyeztetése

**Útmutató:** Azure Active Directory (Azure AD) naplókat biztosít, amelyek segítenek az elavult fiókok felderítésében. Emellett az Azure Identity Access Reviews használatával hatékonyan kezelheti a csoporttagságokat, a vállalati alkalmazásokhoz való hozzáférést és a szerepkör-hozzárendeléseket. A felhasználói hozzáférés rendszeresen felülvizsgálható, hogy csak a megfelelő felhasználók tudjanak folyamatos hozzáférést biztosítani.

- [Az Azure AD-jelentéskészítés](../active-directory/reports-monitoring/index.yml)

- [Az Azure Identity Access-felülvizsgálatok használata](../active-directory/governance/access-reviews-overview.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3.11: Az inaktivált hitelesítő adatok elérésére tett kísérletek figyelése

**Útmutató:** Hozzáféréssel rendelkezik az Azure Active Directory (Azure AD) bejelentkezési tevékenységéhez, naplózási és kockázati eseménynapló-forrásaihoz, amelyek lehetővé teszik az Azure Sentinel vagy egy külső SIEM integrálását.

Leegyszerűsítheti ezt a folyamatot, ha diagnosztikai beállításokat hoz létre az Azure AD felhasználói fiókokhoz, és elküldi az auditnaplókat és a bejelentkezési naplókat egy Log Analytics-munkaterületre. A kívánt naplóriasztásokat a Log Analyticsben konfigurálhatja.

- [Azure-tevékenységnaplók integrálása az Azure Monitorba](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

- [Az Azure Sentinel](../sentinel/quickstart-onboard.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3.12: Riasztás a fiók bejelentkezési viselkedésének eltérése esetén

**Útmutató:** A fiók bejelentkezési viselkedésének eltérései a vezérlősíkon az Azure Active Directory (Azure AD) Identity Protection és kockázatészlelési funkciók használatával konfigurálhatja a felhasználói identitásokkal kapcsolatos észlelt gyanús műveletekre adott automatikus válaszokat. Az adatokat be is Azure Sentinel vizsgálathoz.

- [Az Azure AD kockázatos bejelentkezéseinek megtekintése](../active-directory/identity-protection/overview-identity-protection.md)

- [Az Identity Protection kockázati szabályzatok konfigurálása és engedélyezése](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [A Azure Sentinel](../sentinel/quickstart-onboard.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

## <a name="data-protection"></a>Adatvédelem

*További információ: [Azure Security Benchmark: Adatvédelem](../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: Bizalmas adatok leltárának fenntartása

**Útmutató:** A címkék segítségével nyomon lehet követni a bizalmas adatokat tároló vagy feldolgozó Azure-erőforrásokat.

- [Címkék létrehozása és használata](../azure-resource-manager/management/tag-resources.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: Bizalmas adatokat tároló vagy feldolgozó rendszerek elkülönítése

**Útmutató:** Különálló előfizetéseket és/vagy felügyeleti csoportokat implementálja a fejlesztéshez, teszteléshez és éles környezethez. Azure Cache for Redis példányokat virtuális hálózattal/alhálózattal kell elválasztani, és megfelelően kell címkézni. Ha szükséges, a Azure Cache for Redis is használhatja a szabályokat, hogy csak a megadott IP-címtartományból származó ügyfélkapcsolatok csatlakoz tudjanak a gyorsítótárhoz.

- [További Azure-előfizetések létrehozása](../cost-management-billing/manage/create-subscription.md)

- [Új Management Groups](../governance/management-groups/create-management-group-portal.md)

- [Virtuális hálózat Azure Cache for Redis üzembe helyezése](cache-how-to-premium-vnet.md)

- [Tűzfalszabályok Azure Cache for Redis konfigurálása](./cache-configure.md#firewall)

- [Címkék létrehozása és használata](../azure-resource-manager/management/tag-resources.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: Bizalmas adatok jogosulatlan átvitelének figyelése és blokkolása

**Útmutató:** Még nem érhető el; az adatazonosítási, besorolási és adatveszteség-megelőzési funkciók még nem érhetők el Azure Cache for Redis.

A Microsoft kezeli a mögöttes infrastruktúrát a Azure Cache for Redis és szigorú vezérlőket valósított meg az ügyféladatok elvesztésének vagy kitettségének megakadályozására.

- [Az ügyféladatok Azure-beli védelmének ismertetése](../security/fundamentals/protection-customer-data.md)

**Felelősség**: Megosztott

**Azure Security Center monitorozás:** Nincs

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: Az átvitel során bizalmas adatok titkosítása

**Útmutató:** Azure Cache for Redis TLS-titkosítású kommunikációra van szükség alapértelmezés szerint. A TLS 1.0-s, 1.1-es és 1.2-es verziói jelenleg támogatottak. A TLS 1.0 és 1.1 azonban az iparági szintű elalasztás útvonalán halad, ezért ha lehetséges, használja a TLS 1.2-t. Ha az ügyféloldali kódtár vagy eszköz nem támogatja a TLS-t, akkor a titkosítatlan kapcsolatok engedélyezése a Azure Portal vagy felügyeleti API-kon keresztül történik. Ilyen esetekben, ha a titkosított kapcsolatok nem lehetségesek, a gyorsítótár és az ügyfélalkalmazás virtuális hálózatba való elhelyezése ajánlott.

- [Az átvitel közbeni titkosítás Azure Cache for Redis](cache-best-practices.md)

- [A virtuális hálózatok gyorsítótár-forgatókönyvében használt szükséges portok](./cache-how-to-premium-vnet.md#outbound-port-requirements)

**Felelősség**: Megosztott

**Azure Security Center monitorozás:** Az [Azure-biztonsági](/azure/governance/policy/samples/azure-security-benchmark) teljesítményteszt a Security Center alapértelmezett szabályzati kezdeményezése, amely a Security Center [javaslatainak alapja.](/azure/security-center/security-center-recommendations) A Azure Policy vezérlővel kapcsolatos összes definíciót automatikusan engedélyezi a Security Center. Az ehhez a vezérlőhez kapcsolódó riasztások esetében szükség [lehet Azure Defender](/azure/security-center/azure-defender) kapcsolódó szolgáltatásokra vonatkozó tervre.

**Azure Policy definíciók – Microsoft.Cache:**

[!INCLUDE [Resource Policy for Microsoft.Cache 4.4](../../includes/policy/standards/asb/rp-controls/microsoft.cache-4-4.md)]

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: A bizalmas adatok azonosítása aktív felderítési eszközzel

**Útmutató:** Az adatazonosítási, besorolási és adatveszteség-megelőzési funkciók még nem érhetők el a Azure Cache for Redis. A bizalmas adatokat tartalmazó példányok címkézése, és ha megfelelőségi célokból szükséges, harmadik féltől származó megoldás megvalósítása.

A Microsoft által felügyelt mögöttes platform esetén a Microsoft bizalmasként kezeli az összes ügyféltartalmat, és nagy terjedelmüknek megfelelő védelmet biztosít az ügyfelek adatvesztése és kitettsége ellen. Annak érdekében, hogy az Azure-beli ügyféladatok biztonságban maradnak, a Microsoft robusztus adatvédelmi vezérlőket és képességeket valósított meg és tart karban.

- [Az ügyféladatok Azure-beli védelmének ismertetése](../security/fundamentals/protection-customer-data.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="46-use-role-based-access-control-to-control-access-to-resources"></a>4.6: Szerepköralapú hozzáférés-vezérlés használata az erőforrásokhoz való hozzáférés szabályozása érdekében

**Útmutató:** Az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC) használatával szabályozhatja Azure Cache for Redis vezérlősíkhoz (pl. Azure Portal).

- [Az Azure RBAC konfigurálása](../role-based-access-control/role-assignments-portal.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: Bizalmas adatok titkosítása

**Útmutató:** Azure Cache for Redis ügyféladatokat tárol a memóriában, és bár a Microsoft által megvalósított számos vezérlő erősen védi, a memória alapértelmezés szerint nincs titkosítva. Ha a szervezet ezt megköveteli, titkosítsa a tartalmat, mielőtt a Azure Cache for Redis.

A "Redis-Azure Cache for Redis" funkció használata esetén a rendszer az adatokat egy Ön tulajdonában és kezeléséhez használt Azure Storage-fiókba küldi. Az adatmegőrzést a "New Azure Cache for Redis" panelről konfigurálhatja a gyorsítótár létrehozása során, illetve az Erőforrás menüben a meglévő prémium szintű gyorsítótárakhoz.

Az Azure Storage-ban tárolt adatok titkosítása és visszafejtése transzparens módon, 256 bites AES-titkosítással történik, amely az egyik legerősebb elérhető blokktitkosítás, és FIPS 140-2 szabványnak felel meg. Az Azure Storage-titkosítás nem tiltható le. A tárfiók titkosítása a Microsoft által felügyelt kulcsokkal, vagy a saját kulcsokkal is kezelhető.

- [Adatmegőrzés konfigurálása a Azure Cache for Redis](cache-how-to-premium-persistence.md)

- [Az Azure Storage-fiókok titkosításának a használata](../storage/common/storage-service-encryption.md)

- [Az Azure-ügyfelek adatainak védelme](../security/fundamentals/protection-customer-data.md)

**Felelősség**: Megosztott

**Azure Security Center monitorozás:** Nincs

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: Kritikus fontosságú Azure-erőforrások változásainak naplózása és riasztása

**Útmutató:** Azure Monitor Azure-tevékenységnaplóval használva riasztásokat hozhat létre, amikor változásokra kerül sor az éles példányok és Azure Cache for Redis kritikus vagy kapcsolódó erőforrások esetén.

- [Riasztások létrehozása az Azure-tevékenységnapló eseményeihez](../azure-monitor/alerts/alerts-activity-log.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

## <a name="vulnerability-management"></a>Biztonságirés-kezelés

*További információ: [Azure biztonsági teljesítményteszt: Biztonsági rések kezelése.](../security/benchmarks/security-control-vulnerability-management.md)*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1: Automatizált sebezhetőség-ellenőrzési eszközök futtatása

**Útmutató:** Kövesse a Azure Security Center a Azure Cache for Redis és a kapcsolódó erőforrások biztonságossá tétele érdekében.

A Microsoft a biztonsági rések kezelését a biztonsági réseket támogató mögöttes Azure Cache for Redis.

- [A Azure Security Center javaslatokat](../security-center/recommendations-reference.md)

**Felelősség**: Megosztott

**Azure Security Center:** Nincs

## <a name="inventory-and-asset-management"></a>Leltár-és eszközfelügyelet

*További információ: [Azure biztonsági teljesítményteszt: Leltár és eszközkezelés.](../security/benchmarks/security-control-inventory-asset-management.md)*

### <a name="61-use-automated-asset-discovery-solution"></a>6.1: Automatizált eszközfelderítési megoldás használata

**Útmutató:** A Azure Resource Graph az előfizetés(ek)beli összes erőforrás (pl. számítás, tárolás, hálózat, portok és protokollok stb.) lekérdezésére/felderítésére használható. Győződjön meg arról, hogy megfelelő (olvasási) engedélyeket ad meg a bérlőben, és számba veszi az összes Azure-előfizetést és az előfizetésen belüli erőforrásokat.

Bár a klasszikus Azure-erőforrások a Resource Graph felderíthetőek, erősen ajánlott a Azure Resource Manager erőforrások létrehozása és használata.

- [Lekérdezések létrehozása Azure Resource Graph](../governance/resource-graph/first-query-portal.md)

- [Az Azure-előfizetések megtekintése](/powershell/module/az.accounts/get-azsubscription?preserve-view=true&view=azps-4.8.0)

- [Az Azure RBAC](../role-based-access-control/overview.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="62-maintain-asset-metadata"></a>6.2: Objektum metaadatainak karbantartása

**Útmutató:** Címkék alkalmazása olyan Azure-erőforrásokra, amelyek metaadatokat adva logikailag rendszerezik őket egy taxonóniában.

- [Címkék létrehozása és használata](../azure-resource-manager/management/tag-resources.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: Nem engedélyezett Azure-erőforrások törlése

**Útmutató:** A példányok és a kapcsolódó erőforrások rendszerezéséhez és nyomon követéséhez használjon címkézést, felügyeleti csoportokat és különálló előfizetéseket Azure Cache for Redis, ha szükséges. Rendszeresen egyeztetni kell a leltárt, és gondoskodni kell arról, hogy a jogosulatlan erőforrások időben törlődjenek az előfizetésből.

Emellett a Azure Policy az alábbi beépített szabályzatdefiníciók használatával korlátozásokat is érvényben hagyhat az ügyfél-előfizetésben létretehető erőforrások típusára:

- Nem engedélyezett erőforrástípusok

- Engedélyezett erőforrástípusok

További információkat az alábbi hivatkozásokon találhat:

- [További Azure-előfizetések létrehozása](../cost-management-billing/manage/create-subscription.md)

- [Felügyeleti csoportok létrehozása](../governance/management-groups/create-management-group-portal.md)

- [Erőforráscímkék létrehozása és használata](../azure-resource-manager/management/tag-resources.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: Nem jóváhagyott Azure-erőforrások figyelése

**Útmutató:** Azure Policy az alábbi beépített szabályzatdefiníciók használatával az ügyfél-előfizetésben létre lehet hozható erőforrások típusára vonatkozó korlátozásokat:

- Nem engedélyezett erőforrástípusok
- Engedélyezett erőforrástípusok

Emellett a Azure Resource Graph az előfizetések erőforrásainak lekérdezéséhez és felderítéséhez.

- [Az Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md)

- [Lekérdezések létrehozása az Azure Graph használatával](../governance/resource-graph/first-query-portal.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="69-use-only-approved-azure-services"></a>6.9: Csak jóváhagyott Azure-szolgáltatások használata

**Útmutató:** Azure Policy az alábbi beépített szabályzatdefiníciók használatával az ügyfél-előfizetés(ök)ben létre lehet hozható erőforrások típusára vonatkozó korlátozásokat:

- Nem engedélyezett erőforrástípusok

- Engedélyezett erőforrástípusok

További információkat az alábbi hivatkozásokon találhat:

- [Az Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md)

- [Adott erőforrástípus megtagadása a Azure Policy](../governance/policy/samples/built-in-policies.md#general)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6.11: Korlátozza a felhasználók számára a Azure Resource Manager

**Útmutató:** Az Azure feltételes hozzáférés konfigurálásával korlátozhatja a felhasználók Azure Resource Manager-hozzáférését a "hozzáférés blokkolása" beállításával a "Microsoft Azure Management" alkalmazáshoz.

- [Feltételes hozzáférés konfigurálása a hozzáférés letilt Azure Resource Manager](../role-based-access-control/conditional-access-azure-management.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

## <a name="secure-configuration"></a>Biztonságos konfiguráció

*További információ: [Azure biztonsági teljesítményteszt: Biztonságos konfiguráció.](../security/benchmarks/security-control-secure-configuration.md)*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: Biztonságos konfigurációk létrehozása az összes Azure-erőforráshoz

**Útmutató:** Standard biztonsági konfigurációkat határozhat meg és valósíthat meg a Azure Cache for Redis példányokhoz a Azure Policy. Használja Azure Policy aliasokat a "Microsoft.Cache" névtérben egyéni szabályzatok létrehozásához, amelyek naplózza vagy kikényszeríteni a Azure Cache for Redis konfigurációját. A saját példányokhoz kapcsolódó beépített szabályzatdefiníciókat is Azure Cache for Redis, például:

- Csak a saját Redis Cache kell engedélyezni

További információkat az alábbi hivatkozásokon találhat:

- [Az elérhető aliasok Azure Policy megtekintése](/powershell/module/az.resources/get-azpolicyalias?preserve-view=true&view=azps-4.8.0)

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

**Útmutató:** Ha egyéni Azure Policy vagy Azure Resource Manager-sablonokat használ a Azure Cache for Redis-példányokhoz és a kapcsolódó erőforrásokhoz, az Azure Repos használatával biztonságosan tárolhatja és kezelheti a kódot.

- [Kód tárolása az Azure DevOpsban](/azure/devops/repos/git/gitworkflow?preserve-view=true&view=azure-devops)

- [Az Azure Repos dokumentációja](/azure/devops/repos/?preserve-view=true&view=azure-devops)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7: Konfigurációkezelési eszközök üzembe helyezése Azure-erőforrásokhoz

**Útmutató:** A Azure Policy a "Microsoft.Cache" névtérben található aliasok használatával egyéni szabályzatokat hozhat létre a rendszerkonfigurációk riasztására, naplózására és kényszerítése érdekében. Emellett dolgozzon ki egy folyamatot és folyamatot a szabályzati kivételek kezeléséhez.

- [Az Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9: Automatizált konfigurációfigyelés megvalósítása Azure-erőforrásokhoz

**Útmutató:** A Azure Policy a "Microsoft.Cache" névtérben található aliasok használatával egyéni szabályzatokat hozhat létre a rendszerkonfigurációk riasztására, naplózására és kényszerítése érdekében. Az Azure Policy [audit], [deny] és [deploy if not exist] használatával automatikusan kényszerítheti a konfigurációkat a Azure Cache for Redis példányokhoz és a kapcsolódó erőforrásokhoz.

- [Az Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="711-manage-azure-secrets-securely"></a>7.11: Azure-beli titkos kulcsok biztonságos kezelése

**Útmutató:** Az Azure App Service Azure Cache for Redis-példányok elérésére használt Azure-beli virtuális gépek vagy webalkalmazások esetében a Managed Service Identity és az Azure Key Vault együttes használatával egyszerűsítheti és biztosíthatja a titkos Azure Cache for Redis kezelését. Győződjön Key Vault, hogy a soft delete engedélyezve van.

- [Az Azure Managed Identities integrálása](../azure-app-configuration/howto-integrate-azure-managed-service-identity.md)

- [Új Key Vault](../key-vault/general/quick-create-portal.md)

- [Hitelesítés a Key Vault](../key-vault/general/assign-access-policy-portal.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: Identitások biztonságos és automatikus kezelése

**Útmutató:** Az Azure App Service Azure Cache for Redis-példányok elérésére használt Azure-beli virtuális gépek vagy webalkalmazások esetében a Managed Service Identity és az Azure Key Vault együttes használatával egyszerűsítheti és biztosíthatja a titkos Azure Cache for Redis kezelését. Győződjön meg Key Vault, hogy a Soft Delete engedélyezve van.

A Managed Identities használatával automatikusan felügyelt identitást nyújthat az Azure-szolgáltatásoknak a Azure Active Directory (Azure AD) szolgáltatásban. A felügyelt identitások lehetővé teszik az Azure AD-hitelesítést támogató bármely szolgáltatásban történő hitelesítést, beleértve a Azure Key Vault szolgáltatást is, anélkül, hogy a hitelesítő adatokat a kódban lenne.

- [Felügyelt identitások konfigurálása](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)

- [Az Azure Managed Identities integrálása](../azure-app-configuration/howto-integrate-azure-managed-service-identity.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: A hitelesítő adatok nem szándékolt kitettségének megszüntetése

**Útmutató:** Hitelesítőadat-ellenőrző implementálja a hitelesítő adatokat a kódban. A Credential Scanner a felfedezett hitelesítő adatok biztonságosabb helyre, például az Azure Key Vaultba való áthelyezésére is javaslatot tesz.

- [Hitelesítőadat-ellenőrző beállítása](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

## <a name="malware-defense"></a>Kártevők elleni védelem

*További információ: [Azure biztonsági teljesítményteszt: Kártevők elleni védelem.](../security/benchmarks/security-control-malware-defense.md)*

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2: A nem számítási Azure-erőforrásokba feltölteni szükséges fájlok előzetes beolvasása

**Útmutató:** A Microsoft kártevőirtó engedélyezve van az Azure-szolgáltatásokat támogató mögöttes gazdagépen (például Azure Cache for Redis), de nem fut az ügyféltartalmakon.

Előzetesen vizsgálja meg a nem számítási Azure-erőforrásokra ( például App Service, Data Lake Storage, Blob Storage, Azure Database for PostgreSQL stb. feltöltött tartalmakat. A Microsoft ezekben az esetekben nem fér hozzá az Ön adataihoz.

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

## <a name="data-recovery"></a>Adat-helyreállítás

*További információ: [Azure biztonsági teljesítményteszt: Adat-helyreállítás.](../security/benchmarks/security-control-data-recovery.md)*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: Rendszeres automatikus biztonságimentek biztosítása

**Útmutató:** A Redis-adatmegőrzés engedélyezése. A Redis-adatmegőrzés lehetővé teszi a Redisben tárolt adatok megőrzését. Emellett pillanatképeket is készíthet, és biztonságimentheti az adatokat, amelyeket hardverhiba esetén betölthet. Ez óriási előnyt jelent az Alapszintű vagy Standard szinttel szemben, ahol az összes adat a memóriában van tárolva, és adatvesztést okozhat, ha a gyorsítótár-csomópontok leállnak.

Használhatja az Exportálás Azure Cache for Redis is. Az exportálással redis-kompatibilis RDB-Azure Cache for Redis exportálhatja a Azure Cache for Redis tárolt adatokat. Ezzel a funkcióval adatokat mozgathat egy Azure Cache for Redis-példányról egy másikra vagy egy másik Redis-kiszolgálóra. Az exportálási folyamat során a rendszer létrehoz egy ideiglenes fájlt a Azure Cache for Redis-kiszolgálópéldányt tároló virtuális gépen, és feltölti a fájlt a kijelölt tárfiókba. Ha az exportálási művelet sikeres vagy sikertelen állapottal fejeződik be, az ideiglenes fájl törlődik.

- [A Redis-adatmegőrzés engedélyezése](cache-how-to-premium-persistence.md)

- [A Azure Cache for Redis használata](cache-how-to-import-export-data.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: Teljes rendszer-biztonsági mentések és ügyfél által kezelt kulcsok biztonsági mentése

**Útmutató:** A Redis-adatmegőrzés engedélyezése. A Redis-adatmegőrzés lehetővé teszi a Redisben tárolt adatok megőrzését. Emellett pillanatképeket is készíthet, és biztonsági adatokat készíthet az adatokról, amelyeket hardverhiba esetén betölthet. Ez óriási előnnyel rendelkezik az Alapszintű vagy Standard szinttel szemben, ahol az összes adat a memóriában van tárolva, és adatvesztést okozhat, ha a gyorsítótár-csomópontok leállnak.

Használhatja az Exportálás Azure Cache for Redis is. Az exportálással redis-kompatibilis RDB-fájlokba exportálhatja Azure Cache for Redis-ban tárolt adatokat. Ezzel a funkcióval adatokat mozgathat egy Azure Cache for Redis-példányról egy másikra vagy egy másik Redis-kiszolgálóra. Az exportálási folyamat során a rendszer létrehoz egy ideiglenes fájlt a Azure Cache for Redis-kiszolgálópéldányt tároló virtuális gépen, és feltölti a fájlt a kijelölt tárfiókba. Ha az exportálási művelet sikeres vagy sikertelen állapottal fejeződik be, az ideiglenes fájl törlődik.

Ha a Azure Key Vault hitelesítő adatokat tárol a Azure Cache for Redis példányokhoz, gondoskodjon a kulcsok rendszeres, automatikus biztonsági mentésének biztosításáról.

- [A Redis-adatmegőrzés engedélyezése](cache-how-to-premium-persistence.md)

- [A Azure Cache for Redis használata](cache-how-to-import-export-data.md)

- [Biztonsági másolat készítése Key Vault kulcsokról](/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: Az összes biztonsági másolat ellenőrzése, beleértve az ügyfél által kezelt kulcsokat is

**Útmutató:** A Azure Cache for Redis használata. Az importálás segítségével Redis-kompatibilis RDB-fájlokat hozhat létre bármely, bármilyen felhőben vagy környezetben futó Redis-kiszolgálóról, beleértve a Linuxon, Windowson futó Redist, vagy bármely felhőszolgáltatót, például az Amazon Web Services-t és másokat. Az adatok importálásával egyszerűen hozhat létre gyorsítótárat előre kitöltve adatokkal. Az importálási folyamat során a Azure Cache for Redis az RDB-fájlokat az Azure-tárolóból a memóriába, majd beszúrja a kulcsokat a gyorsítótárba.

Rendszeresen tesztelje az adatok visszaállítását a titkos Azure Key Vault érdekében.

- [Az importálási Azure Cache for Redis használata](cache-how-to-import-export-data.md)

- [Titkos kulcsok Key Vault visszaállítása](/powershell/module/az.keyvault/restore-azkeyvaultsecret?preserve-view=true&view=azps-4.8.0)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

## <a name="incident-response"></a>Incidensmegoldás

*További információ: [Azure Security Benchmark: Incidensek kezelése](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10.1: Incidensválasz-útmutató létrehozása

**Útmutató**: Alakítson ki incidenskezelési útmutatót a vállalat számára. Gondoskodjon írásos incidenskezelési tervekről, amelyek definiálják az összes résztvevő szerepkörét, valamint az incidenskezelés fázisait az észleléstől az incidens utáni értékelésig.

- [Munkafolyamat-automatizálások konfigurálása a Azure Security Center](../security-center/security-center-planning-and-operations-guide.md)

- [Útmutató saját biztonsági incidensek válaszadási folyamatának építéshez](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Microsoft Biztonsági reagálási központ az incidensek felépítése](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Az ügyfél az NIST számítógépes biztonsági incidenskezelési útmutatóját is felhasználhatja, hogy segítséget nyújt a saját incidenskezelési tervének létrehozásában](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: Incidenspontozási és -rangsorolási eljárás létrehozása

**Útmutató:** Security Center súlyosságot rendel az egyes riasztásokhoz, így priorizálhatja, hogy mely riasztásokat kell először megvizsgálni. A súlyosság azon alapul, Security Center mennyire magabiztos a riasztás kiadásához használt eredmény vagy elemzés, valamint hogy a riasztáshoz vezető tevékenység mögött rosszindulatú szándék volt-e.

Emellett egyértelműen jelöljük meg az előfizetéseket (például: éles, nem éles), és hozzon létre egy elnevezési rendszert az Azure-erőforrások egyértelmű azonosításához és kategorizálásához.

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="103-test-security-response-procedures"></a>10.3: Biztonsági reagálási eljárások tesztelése

**Útmutató:** Gyakorlatok a rendszerek incidensválasz-képességeinek rendszeres teszteléséhez. Azonosítsa a gyenge pontokat és réseket, és szükség esetén dolgozza át a tervet.

- [Tekintse meg az NIST kiadványát: Útmutató az IT-tervekhez és -képességekhez: Útmutató a tesztelési, képzési és gyakorlatprogramokhoz](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: Adja meg a biztonsági incidens kapcsolattartási adatait, és konfigurálja a riasztási értesítéseket a biztonsági incidensek esetén

**Útmutató:** A Biztonsági incidens kapcsolattartási adatait a Microsoft arra használja, hogy kapcsolatba lépjen Önnel, ha az Microsoft Biztonsági reagálási központ (MSRC) azt tapasztalja, hogy az ügyfél adatait illetéktelen vagy illetéktelen fél férte hozzá.  Tekintse át az incidenseket a probléma megoldása érdekében.

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

**Útmutató:** Kövesse a Microsoft Felhőbevezetési tesztelési szabályait annak ellenőrzéséhez, hogy a behatolási tesztek nem sértik-e meg a Microsoft szabályzatát. A Microsoft által felügyelt felhőalapú infrastruktúrán, szolgáltatásokon és alkalmazásokon végzett riasztási és élő behatolási tesztek végrehajtásához használja a Microsoft stratégiáját és végrehajtási tervét. 

- [Behatolástesztelési beavatkozási szabályok](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) 

- [Riasztási tesztek a Microsoft-felhőben](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Felelősség**: Megosztott

**Azure Security Center monitorozás:** Nincs

## <a name="next-steps"></a>Következő lépések

- [Az Azure Security Benchmark v2 áttekintésének](../security/benchmarks/overview.md) megtekintése
- További tudnivalók az [Azure biztonsági alapterveiről](../security/benchmarks/security-baselines-overview.md)
