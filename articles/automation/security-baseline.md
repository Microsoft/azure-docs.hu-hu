---
title: Az Automation Azure biztonsági alapkonfigurációja
description: Az Automation biztonsági alapkonfigurációja eljárási útmutatást és erőforrásokat biztosít az Azure biztonsági teljesítménytesztben meghatározott biztonsági javaslatok megvalósításához.
author: msmbaldwin
ms.service: automation
ms.topic: conceptual
ms.date: 02/17/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark, devx-track-azurepowershell
ms.openlocfilehash: 7f87fcac0c1b07e108082c4b4f48bde046dc63c7
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2021
ms.locfileid: "107834497"
---
# <a name="azure-security-baseline-for-automation"></a>Az Automation Azure biztonsági alapkonfigurációja

Ez a biztonsági alapkonfiguráció az Azure Biztonsági teljesítményteszt [1.0-s](../security/benchmarks/overview-v1.md) verziójának útmutatását alkalmazza a Azure Automation. Az Azure Security Benchmark ajánlásokat ad arra nézve, hogy hogyan tehetők biztonságossá a felhőalapú megoldások az Azure-ban.
A tartalom az Azure  biztonsági teljesítményteszt által meghatározott biztonsági vezérlők és a kapcsolódó, a biztonsági Azure Automation. **A** Azure Automation nem alkalmazható vezérlők ki vannak zárva.

 
Ha meg Azure Automation, hogyan lehet teljes mértékben leképezni az Azure-biztonsági teljesítménytesztre, tekintse meg a teljes Azure Automation alapkonfiguráció [leképezési fájlját.](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)

## <a name="network-security"></a>Hálózati biztonság

*További információ: [Azure Security Benchmark: Hálózati biztonság](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1.1: Azure-erőforrások védelme virtuális hálózatokon belül

**Útmutató:** Azure Automation fiók még nem támogatja Azure Private Link szolgáltatáshoz való hozzáférés privát végpontokkal való korlátozását. Azok a runbookok, amelyek az Azure-beli erőforrásokon hitelesítik és futtatják őket, egy Azure-védőfalon futnak, és megosztott háttérerőforrásokat kihasználnak, amelyek egymástól való el vannak különülve a Microsoft feladata; hálózataik korlátlanok, és hozzáférhetnek a nyilvános erőforrásokhoz. Azure Automation jelenleg nem rendelkezik virtuális hálózati integrációval a magánhálózatok számára a hibrid runbook-dolgozók támogatásán túl. Ez a vezérlő nem alkalmazható, ha hibrid runbook-dolgozók nélkül használja az out-of-the-box szolgáltatást.

A runbookok további elkülönítéséhez használhatja az Azure-beli virtuális gépeken futó hibrid runbook-dolgozókat. Azure-beli virtuális gép létrehozásakor létre kell hoznia egy virtuális hálózatot (VNetet), vagy egy meglévő virtuális hálózatot kell használnia, és konfigurálnia kell a virtuális gépeket egy alhálózattal. Győződjön meg arról, hogy minden üzembe helyezett alhálózatra alkalmazva van egy hálózati biztonsági csoport, amely az alkalmazások megbízható portjaira és forrásaira jellemző hálózati hozzáférés-vezérléssel rendelkezik. A szolgáltatásspecifikus követelményekért tekintse meg az adott szolgáltatásra vonatkozó biztonsági javaslatot. Másik lehetőségként, ha egy adott követelmény teljesül, Azure Firewall is használható a követelménynek való megfelelőre.

- [Virtuális hálózatok és virtuális gépek az Azure-ban](../virtual-machines/network-overview.md)

- [Új Virtual Network](../virtual-network/quick-create-portal.md)

- [NSG létrehozása biztonsági konfigurációval](../virtual-network/tutorial-filter-network-traffic.md)

- [A virtuális gép üzembe helyezése és Azure Firewall](../firewall/tutorial-firewall-deploy-portal.md)

- [Runbook végrehajtási környezete](./automation-runbook-execution.md#runbook-execution-environment)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-network-interfaces"></a>1.2: Virtuális hálózatok, alhálózatok és hálózati adapterek konfigurációjának és forgalmának figyelése és naplózása

**Útmutató:** Azure Automation jelenleg nem rendelkezik virtuális hálózati integrációval a magánhálózatok számára a hibrid runbook-dolgozók támogatásán túl. Ez a vezérlő nem alkalmazható, ha hibrid runbook-dolgozók nélkül használja a használatra készen használt szolgáltatást.

Ha Azure-beli virtuális gépeken futó hibrid runbook-dolgozókat használ, győződjön meg arról, hogy a dolgozókat tartalmazó alhálózaton engedélyezve van egy hálózati biztonsági csoport (NSG), és konfigurálja a forgalmi naplókat, hogy a naplókat a tárfiókba továbbküldje a forgalom naplózásához. Emellett továbbíthatja az NSG-forgalom naplóit egy Log Analytics-munkaterületre, és a Traffic Analytics segítségével betekintést nyerhet az Azure-felhőben lévő forgalomba. A hálózati Traffic Analytics a hálózati tevékenységek vizualizációja és a gyors helyek azonosítása, a biztonsági fenyegetések azonosítása, a forgalmi minták azonosítása és a hálózati konfigurációk észlelése.

Bár az NSG-szabályok és a felhasználó által megadott útvonalak nem vonatkoznak a privát végpontokra, az NSG-forgalom naplói és a kimenő kapcsolatok figyelési információi továbbra is támogatottak és használhatók.

- [NSG-forgalom naplóinak engedélyezése](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [A Traffic Analytics](../network-watcher/traffic-analytics.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: Az ismert kártékony IP-címekkel való kommunikáció megtagadása

**Útmutató:** Azure Automation jelenleg nem rendelkezik virtuális hálózati integrációval a magánhálózatok számára a hibrid runbook-dolgozók támogatásán túl. Ez a vezérlő nem alkalmazható, ha hibrid runbook-dolgozók nélkül használja a használatra készen használt szolgáltatást.

Ha Azure-beli virtuális gépeken futó hibrid runbook-dolgozókat használ, engedélyezze az elosztott szolgáltatásmegtagadásos (DDoS) standard védelmet a hibrid runbook-dolgozókat üzemeltető virtuális hálózatokon a DDoS-támadások elleni védelem érdekében. Az integrált Azure Security Center intelligencia használatával megtagadhatja az ismert kártékony IP-címekkel való kommunikációt.  Konfigurálja Azure Firewall az egyes Virtual Network szegmensekre, a Fenyegetésintelligencia beállítást  pedig állítsa a Riasztásra, és tiltsa le a kártékony hálózati forgalmat.

A windowsos Azure Security Center hálózati hozzáférésével korlátozott időre korlátozhatja a Windows rendszerű virtuális gépeknek a jóváhagyott IP-címeknek való kitettségét.  Emellett az NSG Azure Security Center konfigurációk adaptív hálózatkorlátozási javaslataival korlátozhatja a portokat és a forrás IP-eket a tényleges forgalom és a fenyegetésekkel kapcsolatos intelligencia alapján.

- [A DDoS Protection konfigurálása](../ddos-protection/manage-ddos-protection.md)

- [A virtuális gép Azure Firewall](../firewall/tutorial-firewall-deploy-portal.md)

- [Az Azure Security Center fenyegetésintelligencia](../security-center/azure-defender.md)

- [Az Azure Security Center-erőforrások adaptív hálózat-megtartósodása](../security-center/security-center-adaptive-network-hardening.md)

- [A Azure Security Center hálózati kapcsolatok Access Control](../security-center/security-center-just-in-time.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="15-record-network-packets"></a>1.5: Hálózati csomagok rekordja

**Útmutató:** Azure Automation nem rendelkezik virtuális hálózati integrációval a magánhálózatok számára a hibrid runbook-dolgozók támogatásán túl, ez a vezérlő nem alkalmazható, ha a hibrid dolgozók nélküli használatra készen elérhető szolgáltatást használja.

Ha Azure-beli virtuális gépeken futó hibrid runbook-dolgozókat használ, akkor az NSG-forgalom naplóit rögzítheti egy tárfiókban, így folyamatrekordokat hozhat létre a runbook-dolgozókként működő Azure Virtual Machines-hez. Rendellenes tevékenységek vizsgálatakor engedélyezheti a Network Watcher- és csomagrögzítést, hogy a hálózati forgalom vizsgálható legyen szokatlan és váratlan tevékenységeket keresve.

- [NSG-forgalom naplóinak engedélyezése](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Az Network Watcher](../network-watcher/network-watcher-create.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6: Hálózatalapú behatolásészlelési/behatolásvédelmi rendszerek (IDS/IPS) üzembe helyezése

**Útmutató:** Azure Automation a hibrid runbook-dolgozók támogatásán túl jelenleg nincs virtuális hálózati integrációja a magánhálózatok számára. Ez a vezérlő nem alkalmazható, ha hibrid runbook-dolgozók nélkül használja a használatra készen használt szolgáltatást.

Ha Azure-beli virtuális gépeken üzemeltetett hibrid runbook-feldolgozókat használ, az Network Watcher által biztosított csomagrögzítéseket és a nyílt forráskódú IDS-eszközöket kombinálva hálózati behatolásészlelést hajthat végre a feldolgozó gépeket érintő fenyegetések széles körének észleléséhez. Emellett szükség szerint üzembe helyezheti a Azure Firewall- Virtual Network, ha a Fenyegetésintelligencia engedélyezve van, és "Riasztás és megtagadás" beállítással van konfigurálva a kártékony hálózati forgalomhoz.

- [Hálózati behatolásészlelés végrehajtása Network Watcher nyílt forráskódú eszközökkel](../network-watcher/network-watcher-intrusion-detection-open-source-tools.md)

- [A Azure Firewall](../firewall/tutorial-firewall-deploy-portal.md)

- [Riasztások konfigurálása Azure Firewall](../firewall/threat-intel.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: A hálózati biztonsági szabályok összetettségének és adminisztratív terhelésének minimalizálása

**Útmutató:** Virtual Network szolgáltatáscímkékkel hálózati hozzáférés-vezérlést határozhat meg a hálózati biztonsági csoportokhoz vagy Azure Firewall Azure-ban konfigurált olyan csoportokhoz, amelyek hozzáférést igényelnek az Automation-erőforrásokhoz. Biztonsági szabályok létrehozása során szolgáltatáscímkéket használhat bizonyos IP-címek helyett. A szolgáltatáscímke nevének (például GuestAndHybridManagement) egy szabály megfelelő forrás- vagy célmezőben való megadásával engedélyezheti vagy megtagadhatja a megfelelő szolgáltatás forgalmát. A Microsoft kezeli a szolgáltatáscímke által felölelt címelőtagokat, és automatikusan frissíti a szolgáltatáscímkéket a címek változásával.

- [A szolgáltatáscímkék használata és használata](../virtual-network/service-tags-overview.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: A hálózati eszközök szabványos biztonsági konfigurációjának fenntartása

**Útmutató:** Szabványos biztonsági konfigurációk definiálása és implementációja a Azure Automation és Azure Policy.

A nagy méretű Azure Azure Blueprints üzemelő példányok egyszerűsítésére is használhatja az olyan kulcsfontosságú környezeti összetevők egyetlen tervdefinícióba való csomagolásával, mint az Azure Resources Manager-sablonok, az Azure RBAC-vezérlők és -szabályzatok. Alkalmazhatja a tervet az új előfizetésekre, és finomhangolhatja a vezérlést és a felügyeletet a verziószámozáson keresztül.

- [Az Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md)

- [Azure Policy minták hálózattal való építéshez](../governance/policy/samples/built-in-policies.md#network)

- [Azure Blueprints-terv létrehozása](../governance/blueprints/create-blueprint-portal.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="110-document-traffic-configuration-rules"></a>1.10: A forgalom konfigurációs szabályainak dokumentálása

**Útmutató:** Címkék használata az NSG-khez és a hálózati biztonsággal és a forgalommal kapcsolatos egyéb erőforrásokhoz. Egyéni NSG-szabályoknál a "Leírás" mezőben adhatja meg az üzleti igényeket és/vagy időtartamot (stb.) a hálózatra/hálózatról be- és vissza forgalmat engedélyező szabályokhoz.

A címkézéshez kapcsolódó beépített Azure Policy-definíciók (például "Címke és érték megkövetelése" ) használatával biztosíthatja, hogy az összes erőforrás címkékkel legyen létrehozva, és értesítést küld a meglévő, címkézetlen erőforrásokról.

A címkék Azure PowerShell az Azure CLI használatával erőforrásokat kereshet vagy hajthat végre.

- [Címkék létrehozása és használata](../azure-resource-manager/management/tag-resources.md)

- [Új Virtual Network](../virtual-network/quick-create-portal.md)

- [NSG létrehozása biztonsági konfigurációval](../virtual-network/tutorial-filter-network-traffic.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: Automatizált eszközök használata a hálózati erőforrások konfigurációjának figyelése és a változások észlelése érdekében

**Útmutató:** Az Azure-tevékenységnapló segítségével figyelheti az erőforrás-konfigurációkat, és észlelheti a hálózati erőforrások változásait. Hozzon létre riasztásokat a Azure Monitor, amelyek akkor aktiválódnak, ha a kritikus erőforrások módosulnak.

- [Azure-tevékenységnapló eseményeinek megtekintése és lekérése](../azure-monitor/essentials/activity-log.md#view-the-activity-log)

- [Riasztások létrehozása a Azure Monitor](../azure-monitor/essentials/activity-log.md#view-the-activity-log)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

## <a name="logging-and-monitoring"></a>Naplózás és monitorozás

*További információ: [Azure biztonsági teljesítményteszt: Naplózás és monitorozás.](../security/benchmarks/security-control-logging-monitoring.md)*

### <a name="22-configure-central-security-log-management"></a>2.2: Központi biztonsági naplókezelés konfigurálása

**Útmutató:** Naplóadatok továbbítása Azure Monitor naplókba az erőforrások által létrehozott biztonsági Azure Automation összesítéséhez. A Azure Monitor naplólekérdezésekkel kereshet és végezhet elemzéseket, és használhatja az Azure Storage-fiókokat a hosszú távú/archiválási tárterülethez. Azure Automation runbook-feladat állapotát, feladatstreameket, Automation állapotkonfigurációs adatokat, frissítéskezelést és változáskövetési vagy leltárnaplókat küldhet a Log Analytics-munkaterületre. Ezek az információk a Azure Portal, Azure PowerShell és Azure Monitor Logs API-ból láthatók, amely lehetővé teszi egyszerű vizsgálatok elvégzését.

Másik lehetőségként engedélyezheti és bevetheti az adatokat Azure Sentinel külső SIEM-hez. 

- [A Azure Sentinel](../sentinel/quickstart-onboard.md)

- [Platformnaplók és -metrikák gyűjtése Azure Monitor](../azure-monitor/essentials/diagnostic-settings.md)

- [Első lépések a Azure Monitor külső SIEM-integrációval](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/)

- [Azure Automation-feladat adatainak továbbítása az Azure Monitor-naplói felé](automation-manage-send-joblogs-log-analytics.md)

- [A DSC integrálása Azure Monitor naplókba](automation-dsc-diagnostics.md)

- [A társított Log Analytics-munkaterület esetében támogatott régiók](how-to/region-mappings.md)

- [Lekérdezési Update Management naplók](update-management/query-logs.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Azure-erőforrások naplózásának engedélyezése

**Útmutató:** Engedélyezze Azure Monitor naplózási és tevékenységnaplókhoz való hozzáférést, beleértve az eseményforrást, a dátumot, a felhasználót, az időbélyeget, a forráscímeket, a célcímeket és más hasznos elemeket. 

- [Platformnaplók és -metrikák gyűjtése Azure Monitor](../azure-monitor/essentials/diagnostic-settings.md) 

- [Az Azure-tevékenységnapló eseményeinek megtekintése és lekérése](../azure-monitor/essentials/activity-log.md#view-the-activity-log)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="25-configure-security-log-storage-retention"></a>2.5: Biztonsági naplók tárolásának konfigurálása

**Útmutató:** A Azure Monitor meg a Log Analytics-munkaterület megőrzési időszakát a szervezet megfelelőségi szabályozásának megfelelően. Az Azure Storage-fiókokat hosszú távú/archiválási tárterülethez használhatja.

- [Az adatmegőrzési időtartam módosítása a Log Analyticsben](../azure-monitor/logs/manage-cost-storage.md#change-the-data-retention-period)

- [Az Automation-fiókok adatmegőrzési adatai](./automation-managing-data.md#data-retention)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="26-monitor-and-review-logs"></a>2.6: Naplók figyelése és áttekintése

**Útmutató:** Naplók elemzése és figyelése rendellenes viselkedések kereséséhez és az eredmények rendszeres áttekintéséhez. Naplólekérdezések Azure Monitor naplók áttekintéséhez és a naplóadatok lekérdezéséhez.

Másik lehetőségként engedélyezheti és bevetheti az adatokat Azure Sentinel külső SIEM-hez. 

- [A Azure Sentinel](../sentinel/quickstart-onboard.md)

- [A naplólekérdezések Azure Monitor](../azure-monitor/logs/log-analytics-tutorial.md)

- [Egyéni lekérdezések végrehajtása a Azure Monitor](../azure-monitor/logs/get-started-queries.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7: Riasztások engedélyezése rendellenes tevékenységekhez

**Útmutató:** A Azure Security Center és Azure Monitor biztonsági naplókban és eseményekben található rendellenes tevékenységek figyelése és riasztása.

Másik lehetőségként engedélyezheti és be is adatokat lehet Azure Sentinel.

- [A Azure Sentinel](../sentinel/quickstart-onboard.md)

- [Riasztások kezelése a Azure Security Center](../security-center/security-center-managing-and-responding-alerts.md)

- [Riasztási naplóadatok Azure Monitor riasztása](../azure-monitor/alerts/tutorial-response.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="29-enable-dns-query-logging"></a>2.9: DNS-lekérdezésnaplózás engedélyezése

**Útmutató:** Harmadik féltől származó megoldás megvalósítása a Azure Marketplace dns-naplózási megoldáshoz a szervezet által szükségesnek megfelelően.

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

## <a name="identity-and-access-control"></a>Identitás- és hozzáférés-vezérlés

*További információ: [Azure Biztonsági teljesítményteszt: Identitás és Access Control.](../security/benchmarks/security-control-identity-access-control.md)*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: A rendszergazdai fiókok leltárának fenntartása

**Útmutató:** Azure Active Directory (Azure AD) beépített rendszergazdai szerepköröket használjon, amelyek explicit módon hozzárendelhetőek és lekérdezhetőek. Az Azure AD PowerShell-modullal alkalmi lekérdezéseket hajt végre a rendszergazdai csoportok tagjainak felderítéséhez. Amikor Automation-fiókfuttatási fiókokat használ a runbookok számára, győződjön meg arról, hogy ezek a szolgáltatásnévk is nyomon vannak követve a leltárban, mivel gyakran emelt szintű engedélyekkel is rendelkezik. Törölje a nem használt run as-fiókokat a támadási felület minimalizálása érdekében.

- [Címtárszerepkomó lekért szerepkör az Azure AD-ban a PowerShell használatával](/powershell/module/azuread/get-azureaddirectoryrole)

- [Címtárbeli szerepkör tagjainak lekérte az Azure AD-ban a PowerShell használatával](/powershell/module/azuread/get-azureaddirectoryrolemember)

- [Futtató fiók vagy klasszikus futtató fiók törlése](delete-run-as-account.md)

- [Fiók Azure Automation kezelése](manage-runas-account.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="32-change-default-passwords-where-applicable"></a>3.2: Az alapértelmezett jelszavak módosítása, ha vannak

**Útmutató:** Azure Automation fiók nem rendelkezik az alapértelmezett jelszavak fogalmával.  Az ügyfelek felelősek az olyan külső alkalmazásokért és Marketplace-szolgáltatásokért, amelyek alapértelmezett jelszavakat használhatnak, amelyek a szolgáltatáson vagy annak hibrid runbook-dolgozókon futnak.

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: Dedikált rendszergazdai fiókok használata

**Útmutató:** Szabványos üzemeltetési eljárások létrehozása a dedikált rendszergazdai fiókok használatával kapcsolatban. Az Azure Security Center és hozzáférés-kezelés használatával figyelheti a rendszergazdai fiókok számát. Amikor Automation-fiókfuttatási fiókokat használ a runbookok számára, győződjön meg arról, hogy ezek a szolgáltatásnévk is nyomon vannak követve a leltárban, mivel gyakran emelt szintű engedélyekkel is rendelkezik. Ezeknek az identitásnak a hatókörét a szükséges legkevesebb jogosultsággal kell rendelkeznie ahhoz, hogy a runbookok sikeresen végrehajtsák az automatizált folyamatot. Törölje a nem használt run as fiókokat a támadási felület minimalizálása érdekében.

Az azure-beli Azure Active Directory (Azure AD) és a Microsoft-szolgáltatások Privileged Identity Management kiemelt szerepkörei és az Azure Resource Manager.

- [További információ a Privileged Identity Management](../active-directory/privileged-identity-management/index.yml)

- [Futtató fiók vagy klasszikus futtató fiók törlése](delete-run-as-account.md)

- [Fiók Azure Automation kezelése](manage-runas-account.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="34-use-azure-active-directory-single-sign-on-sso"></a>3.4: Egyszeri Azure Active Directory (SSO) használata

**Útmutató:** Amikor csak lehetséges, használjon SSO-t az Azure Active Directory (Azure AD) szolgáltatással az önálló hitelesítő adatok szolgáltatásonkénti konfigurálása helyett. Használja Azure Security Center Identity and Access Management (Identitás- és hozzáférés-kezelés) javaslatokat.

- [Egyszeri bejelentkezés alkalmazásokba az Azure AD-ban](../active-directory/manage-apps/what-is-single-sign-on.md)

- [Identitás és hozzáférés figyelése a Azure Security Center](../security-center/security-center-identity-access.md)

- [Az Azure AD használata az Azure-beli hitelesítéshez](automation-use-azure-ad.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: Többtényezős hitelesítés használata minden Azure Active Directory-alapú hozzáféréshez

**Útmutató:** Engedélyezze Azure Active Directory (Azure AD) többtényezős hitelesítést, és kövesse Azure Security Center identitás- és hozzáférés-kezelésre vonatkozó javaslatokat.

- [Többtényezős hitelesítés engedélyezése az Azure-ban](../active-directory/authentication/howto-mfa-getstarted.md)

- [Identitás és hozzáférés figyelése a Azure Security Center](../security-center/security-center-identity-access.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: Dedikált gépek (Emelt szintű hozzáférésű munkaállomások) használata az összes felügyeleti feladathoz

**Útmutató:** Többtényezős hitelesítéssel konfigurált, többtényezős hitelesítéssel használja az Azure Automation-fiókerőforrásokba való bejelentkezéshez és konfiguráláshoz éles környezetben. 

- [Tudnivalók a Privileged Access munkaállomásról](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

- [Többtényezős hitelesítés engedélyezése az Azure-ban](../active-directory/authentication/howto-mfa-getstarted.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3.7: A rendszergazdai fiókokból származó gyanús tevékenységek naplózása és riasztása 

**Útmutató:** A Azure Active Directory (Azure AD) kockázatészlelései segítségével megtekintheti a kockázatos felhasználói viselkedéssel kapcsolatos riasztásokat és jelentéseket. Az ügyfél továbbíthat kockázatészlelési Azure Security Center riasztásokat a Azure Monitor és konfigurálhat egyéni riasztásokat/értesítéseket a műveletcsoportok használatával.

- [A Azure Security Center észlelésének ismertetése (gyanús tevékenység)](../active-directory/identity-protection/overview-identity-protection.md)

- [Azure-tevékenységnaplók integrálása az Azure Monitorba](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

- [Műveletcsoportok konfigurálása egyéni riasztáshoz és értesítéshez](../azure-monitor/alerts/action-groups.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: Azure-erőforrások kezelése csak jóváhagyott helyekről 

**Útmutató:** Ajánlott a feltételes hozzáférés elnevezett helyei használatával engedélyezni a hozzáférést az IP-címtartományok vagy országok/régiók meghatározott logikai csoportosításaiból. 

- [Elnevezett helyek konfigurálása az Azure-ban](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="39-use-azure-active-directory"></a>3.9: A Azure Active Directory

**Útmutató:** A Azure Active Directory (Azure AD) használata központi hitelesítési és engedélyezési rendszerként. Az Azure AD erős titkosítással védi az adatokat az átvitel alatt és az átvitel alatt. Az Azure AD emellett a felhasználói hitelesítő adatokat is megsóz, hashekkel és biztonságosan tárolja. Hibrid runbook-dolgozók használata esetén a felügyelt identitásokat használhatja a futó fiókok helyett a zökkenőmentesebb biztonságos engedélyek érdekében.

- [Azure AD-példány létrehozása és konfigurálása](../active-directory-domain-services/tutorial-create-instance.md)

- [Runbook-hitelesítés használata felügyelt identitásokkal](./automation-hrw-run-runbooks.md#runbook-auth-managed-identities)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: A felhasználói hozzáférés rendszeres áttekintése és egyeztetése

**Útmutató:** Azure Active Directory (Azure AD) naplókat biztosít az elavult fiókok felderítésének segítésekor. Emellett az Azure identitás-hozzáférési felülvizsgálatok használatával hatékonyan kezelheti a csoporttagságokat, a vállalati alkalmazásokhoz való hozzáférést és a szerepkör-hozzárendeléseket. A felhasználói hozzáférés rendszeresen felülvizsgálható, hogy csak a megfelelő felhasználók férnek hozzá. Amikor Automation-fiókként futtatott fiókokat használ a runbookok számára, győződjön meg arról, hogy ezek a szolgáltatásnévk is nyomon vannak követve a leltárban, mivel gyakran emelt szintű engedélyekkel is rendelkezik. Törölje a nem használt run as fiókokat a támadási felület minimalizálása érdekében.

- [Az Azure AD-jelentéskészítés](../active-directory/reports-monitoring/index.yml)

- [Az Azure identitás-hozzáférési felülvizsgálatok használata](../active-directory/governance/access-reviews-overview.md)

- [Futtató fiók vagy klasszikus futtató fiók törlése](delete-run-as-account.md)

- [Fiók Azure Automation kezelése](manage-runas-account.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3.11: Az inaktivált hitelesítő adatok elérésére tett kísérletek figyelése

**Útmutató:** Hozzáféréssel rendelkezik Azure Active Directory (Azure AD) bejelentkezési tevékenységéhez, naplózási és kockázati eseménynapló-forrásaihoz, amelyek lehetővé teszik az integrációt bármely SIEM-/monitorozási eszközzel.

Ezt a folyamatot leegyszerűsítheti, ha diagnosztikai beállításokat hoz létre az Azure AD felhasználói fiókokhoz, és elküldi az auditnaplókat és bejelentkezési naplókat egy Log Analytics-munkaterületre. A kívánt riasztásokat a Log Analytics-munkaterületen konfigurálhatja.

- [Azure-tevékenységnaplók integrálása az Azure Monitorba](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3.12: Riasztás a fiók bejelentkezési viselkedésének eltéréséről

**Útmutató:** Az Azure Active Directory (Azure AD) kockázat- és identitásvédelmi szolgáltatásaival konfigurálhatja a hálózati erőforrás felhasználói identitásával kapcsolatos észlelt gyanús műveletekre adott automatikus válaszokat. Az adatokat be is Azure Sentinel vizsgálathoz.

- [Az Azure AD kockázatos bejelentkezéseinek megtekintése](../active-directory/identity-protection/overview-identity-protection.md)

- [Az Identity Protection kockázati szabályzatok konfigurálása és engedélyezése](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [A Azure Sentinel](../sentinel/quickstart-onboard.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: Hozzáférés biztosítanak a Microsoft számára a releváns ügyféladatokhoz a támogatási forgatókönyvek során

**Útmutató:** A Azure Automation fiókok esetében a Microsoft ügyfélszolgálata hozzáférhet a platformerőforrás metaadataihoz egy nyílt támogatási eset során, más eszköz használata nélkül.

Ha azonban Azure-beli virtuális gépeken futó hibrid runbook-dolgozókat használ, és egy külső félnek hozzá kell férnia az ügyféladatokhoz (például egy támogatási kérés során), az Azure-beli virtuális gépekhez használható Ügyfélszéf (előzetes verzió) használatával áttekinti és jóváhagyja vagy elutasítja az ügyféladatokhoz való hozzáférési kérelmeket.

- [A Ügyfélszéf](../security/fundamentals/customer-lockbox-overview.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

## <a name="data-protection"></a>Adatvédelem

*További információ: [Azure Security Benchmark: Adatvédelem](../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: Bizalmas adatok leltárának fenntartása

**Útmutató:** A címkék segítségével nyomon Azure Automation bizalmas adatokat tároló vagy feldolgozó erőforrások nyomon követését. 

- [Címkék létrehozása és használata](../azure-resource-manager/management/tag-resources.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: Bizalmas adatokat tároló vagy feldolgozó rendszerek elkülönítése

**Útmutató:** Különálló előfizetéseket és/vagy felügyeleti csoportokat implementálja a fejlesztéshez, teszteléshez és éles környezethez. Környezetek elkülönítése külön Automation-fiókerőforrások használatával. Az olyan erőforrásokat, mint a hibrid runbook-dolgozók, virtuális hálózattal/alhálózattal kell elválasztani, megfelelően címkézni, és egy hálózati biztonsági csoporton (NSG- vagy virtuális hálózaton) belül kell Azure Firewall. A bizalmas adatokat tároló vagy feldolgozó virtuális gépek esetén házirendet és eljárást alkalmazva kapcsolja ki őket, amikor nincs használatban.

- [További Azure-előfizetések létrehozása](../cost-management-billing/manage/create-subscription.md)

- [A Management Groups](../governance/management-groups/create-management-group-portal.md)

- [Címkék létrehozása és használata](../azure-resource-manager/management/tag-resources.md)

- [Új Virtual Network](../virtual-network/quick-create-portal.md)

- [NSG létrehozása biztonsági konfigurációval](../virtual-network/tutorial-filter-network-traffic.md)

- [A Azure Firewall](../firewall/tutorial-firewall-deploy-portal.md)

- [Riasztás vagy riasztás konfigurálása és megtagadás Azure Firewall](../firewall/threat-intel.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: Bizalmas adatok jogosulatlan átvitelének figyelése és blokkolása

**Útmutató:** A hibrid runbook-feldolgozó szolgáltatás használata esetén használja a Azure Marketplace egy külső megoldását a peremhálózaton, amely figyeli a bizalmas adatok jogosulatlan átvitelét, és blokkolja az ilyen átviteleket, miközben riasztja az információbiztonsági szakembereket.

A Microsoft által felügyelt mögöttes platform esetén a Microsoft minden ügyféltartalmat bizalmasként kezel, és védelmet biztosít az ügyfelek adatvesztése és kitettsége ellen. Annak érdekében, hogy az Azure-beli ügyféladatok biztonságban maradnak, a Microsoft robusztus adatvédelmi vezérlőket és képességeket valósított meg és tart karban.

- [Az ügyféladatok Azure-beli védelmének ismertetése](../security/fundamentals/protection-customer-data.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: Az átvitel során bizalmas adatok titkosítása

**Útmutató:** Az átvitel során bizalmas adatok titkosítása. Győződjön meg arról, hogy az Azure-erőforrásokhoz az Azure-beli virtuális hálózatokban csatlakozó ügyfelek képesek egyeztetni a TLS 1.2-es vagy újabb verzióját. Azure Automation teljes mértékben támogatja és kikényszerít az 1.2-es átviteli réteget (TLS), valamint minden ügyfélhívást vagy újabb verziót minden külső HTPS-végponthoz (webhookokon, DSC-csomópontokon, hibrid runbook-feldolgozón keresztül).

Kövesse Azure Security Center az átvitel közbeni, valamint az átvitel közbeni titkosításra vonatkozó javaslatokat.

- [Az átvitel során az Azure-ral történő titkosítás](../security/fundamentals/encryption-overview.md#encryption-of-data-in-transit)

- [Azure Automation TLS 1.2 kényszerítési eljárása](../active-directory/hybrid/reference-connect-tls-enforcement.md)

**Felelősség**: Megosztott

**Azure Security Center monitorozás:** Nincs

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: Aktív felderítési eszköz használata a bizalmas adatok azonosításához

**Útmutató:** Harmadik féltől származó aktív felderítési eszköz használatával azonosíthatja a szervezet technológiai rendszerei által tárolt, feldolgozott vagy továbbított összes bizalmas információt, beleértve a helyszínen vagy egy távoli szolgáltatónál található adatokat, és frissítheti a szervezet bizalmas információinak leltárát.

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="46-use-role-based-access-control-to-control-access-to-resources"></a>4.6: Az erőforrásokhoz való hozzáférés vezérlése szerepköralapú hozzáférés-vezérléssel

**Útmutató:** Az Azure szerepköralapú hozzáférés-vezérlésével (Azure RBAC) szabályozhatja az Azure Automation-erőforrásokhoz való hozzáférést a beépített szerepkör-definíciók használatával, valamint hozzáférést rendelhet az automation-erőforrásokhoz hozzáférő felhasználókhoz egy legkevésbé emelt szintű vagy "csak megfelelő" hozzáférési modellel. Hibrid runbook-dolgozók használata esetén a virtuális gépek felügyelt identitásai segítségével kerülje el a szolgáltatásnév-használatot. Ha több-bérlős vagy hibrid runbook-dolgozókat is használ, ügyeljen arra, hogy megfelelően hatókörrel rendelkező Azure RBAC-engedélyeket alkalmaz a runbook-dolgozók identitására.

- [Az Azure RBAC konfigurálása](../role-based-access-control/role-assignments-portal.md)

- [Runbook-engedélyek hibrid runbook-feldolgozóhoz](./automation-hybrid-runbook-worker.md#runbook-permissions-for-a-hybrid-runbook-worker)

- [Szerepköri engedélyek és biztonság kezelése](automation-role-based-access-control.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7: A hozzáférés-vezérlés kényszerítése gazdagépalapú adatveszteség-megelőzéssel

**Útmutató:** Azure Automation nem teszi elérhetővé a mögöttes több-bérlős runbook-feldolgozó virtuális gépeit, és ezt a platform kezeli. Ez a vezérlő nem alkalmazható, ha hibrid runbook-dolgozók nélkül használja a használatra készen használt szolgáltatást.

Ha Azure-beli virtuális gépeken futó hibrid runbook-feldolgozókat használ, akkor külső gazdagépalapú adatveszteség-megelőzési megoldást kell használnia a hozzáférés-vezérlés kényszerítése érdekében az üzemeltetett hibrid runbook-feldolgozó virtuális gépeken.

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: Bizalmas adatok titkosítása

**Útmutató:** Felhasználó által kezelt kulcsok használata Azure Automation. Azure Automation támogatja az ügyfél által kezelt kulcsok használatát az összes használt "biztonságos eszköz" (például hitelesítő adatok, tanúsítványok, kapcsolatok és titkosított változók) titkosításához. Használja ki a titkosított változókat a runbookokkal az állandó változókereséshez, hogy megakadályozza a nem szándékolt kitettséget.

Hibrid runbook-dolgozók használata esetén a virtuális gépeken lévő virtuális lemezek titkosítása kiszolgálóoldali titkosítással vagy Azure Disk Encryption (ADE) használatával történik. Az Azure Disk Encryption a Windows BitLocker szolgáltatását használja a felügyelt lemezek ügyfél által felügyelt kulcsokkal történő titkosításához a vendég virtuális gépen. Az ügyfél által kezelt kulcsokkal való kiszolgálóoldali titkosítás javítja az ADE-t azáltal, hogy lehetővé teszi, hogy bármilyen operációsrendszer-típust és rendszerképet használjon a virtuális gépekhez a Storage szolgáltatásban tárolt adatok titkosításával.

- [Azure-beli felügyelt lemezek kiszolgálóoldali titkosítása](../virtual-machines/disk-encryption.md)

- [Azure Disk Encryption Windows rendszerű virtuális gépekhez](../virtual-machines/windows/disk-encryption-overview.md)

- [Felhasználó által kezelt kulcsok használata Automation-fiókhoz](./automation-secure-asset-encryption.md#use-of-customer-managed-keys-for-an-automation-account)

- [Felügyelt változók a Azure Automation](shared-resources/variables.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozása:** Az [Azure Biztonsági](/azure/governance/policy/samples/azure-security-benchmark) teljesítményteszt a Security Center alapértelmezett szabályzati kezdeményezése, amely a Security Center [javaslatainak alapja.](/azure/security-center/security-center-recommendations) A Azure Policy vezérlővel kapcsolatos összes definíciót automatikusan engedélyezi a Security Center. Az ehhez a vezérlőhez kapcsolódó riasztások esetében szükség [lehet Azure Defender](/azure/security-center/azure-defender) kapcsolódó szolgáltatásokra vonatkozó tervre.

**Azure Policy definíciók – Microsoft.Automation:**

[!INCLUDE [Resource Policy for Microsoft.Automation 4.8](../../includes/policy/standards/asb/rp-controls/microsoft.automation-4-8.md)]

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: Kritikus fontosságú Azure-erőforrások változásainak naplózása és riasztása

**Útmutató:** A Azure Monitor azure-tevékenységnaplóval való használatával riasztásokat hozhat létre a kritikus fontosságú Azure-erőforrások( például hálózati összetevők, Azure Automation fiókok és runbookok) változásairól. 

- [Hálózati biztonsági csoport diagnosztikai naplózása](../private-link/private-link-overview.md#logging-and-monitoring)

- [Riasztások létrehozása az Azure-tevékenységnapló eseményeihez](../azure-monitor/alerts/alerts-activity-log.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

## <a name="vulnerability-management"></a>Biztonságirés-kezelés

*További információ: [Azure biztonsági teljesítményteszt: Biztonsági rések kezelése.](../security/benchmarks/security-control-vulnerability-management.md)*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1: Automatizált sebezhetőség-ellenőrzési eszközök futtatása

**Útmutató:** Kövesse a biztonsági rések felmérésére vonatkozó Azure Security Center az Azure-erőforrásokra vonatkozó útmutatóban található javaslatokat

- [Biztonsági javaslatok az Azure Security Centerben](../security-center/security-center-recommendations.md)

- [Security Center javaslatok referenciája](../security-center/recommendations-reference.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4: A biztonsági rések biztonsági rések biztonsági vizsgálatának összehasonlítása

**Útmutató:** A vizsgálati eredményeket konzisztens időközönként exportálja, és hasonlítsa össze az eredményeket a biztonsági rések orvoslásának ellenőrzéséhez. A biztonsági rések kezelésére vonatkozó, Azure Security Center javaslat használata esetén az ügyfél a kiválasztott megoldás portálján elfordulhat, hogy megtekintse az előzményvizsgálati adatokat.

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5: Kockázatminősítési folyamat használata a felderített biztonsági rések szervizelésének rangsorolására

**Útmutató:** Használja az Azure Security Center által biztosított alapértelmezett kockázatminősítéseket (Secure Score) a felderített biztonsági rések rangsorolásának priorizálása érdekében.

- [A Azure Security Center pontszámok](../security-center/secure-score-security-controls.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

## <a name="inventory-and-asset-management"></a>Leltár-és eszközfelügyelet

*További információ: [Azure biztonsági teljesítményteszt: Leltár és eszközkezelés.](../security/benchmarks/security-control-inventory-asset-management.md)*

### <a name="61-use-automated-asset-discovery-solution"></a>6.1: Automatizált eszközfelderítési megoldás használata

**Útmutató:** A Azure Resource Graph használatával lekérdezheti és felderítheti az Azure Automation összes erőforrását. Győződjön meg arról, hogy rendelkezik a megfelelő (olvasási) engedélyekkel a bérlőben, és képes enumerálni az összes Azure-előfizetést és az előfizetésen belüli erőforrásokat.

- [Lekérdezések létrehozása Azure Resource Graph](../governance/resource-graph/first-query-portal.md)

- [Az Azure-előfizetések megtekintése](/powershell/module/az.accounts/get-azsubscription)

- [Az Azure RBAC](../role-based-access-control/overview.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="62-maintain-asset-metadata"></a>6.2: Objektum metaadatainak karbantartása

**Útmutató:** Címkék alkalmazása olyan Azure-erőforrásokra, amelyek metaadatokat adva logikailag rendszerezik őket egy taxonóniában. 

- [Címkék létrehozása és használata](../azure-resource-manager/management/tag-resources.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: Nem engedélyezett Azure-erőforrások törlése

**Útmutató:** Az erőforrások rendszerezéséhez és nyomon követéséhez címkézést, felügyeleti csoportokat és különálló előfizetéseket használhat, Azure Automation használhatja. Rendszeresen összeegyezteti a leltárat, és gondoskodik arról, hogy a jogosulatlan erőforrások időben törlődjenek az előfizetésből. Törölje a nem használt run as fiókokat a támadási felület minimalizálása érdekében.

- [További Azure-előfizetések létrehozása](../cost-management-billing/manage/create-subscription.md)

- [Új Management Groups](../governance/management-groups/create-management-group-portal.md)

- [Címkék létrehozása és használata](../azure-resource-manager/management/tag-resources.md)

- [Futtató fiók vagy klasszikus futtató fiók törlése](delete-run-as-account.md)

- [Fiók Azure Automation kezelése](manage-runas-account.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6.4: Jóváhagyott Azure-erőforrások meghatározása és leltározása

**Útmutató:** A szervezeti igényeknek megfelelően létre kell hoznia egy leltárt a jóváhagyott Azure-erőforrásokról és a jóváhagyott szoftverekről a számítási erőforrásokhoz.

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: Nem jóváhagyott Azure-erőforrások figyelése

**Útmutató:** Azure Policy az alábbi beépített szabályzatdefiníciók használatával az ügyfél-előfizetésben létre lehet hozható erőforrások típusára vonatkozó korlátozásokat: 

- Nem engedélyezett erőforrástípusok 
- Engedélyezett erőforrástípusok 

Emellett a Azure Resource Graph erőforrásokat is lekérdezhet/felderíthet az előfizetésen belül. Ez segíthet a magas biztonságon alapuló környezetekben, például a Storage-fiókokkal rendelkezőkben. 

- [Az Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md) 

- [Lekérdezések létrehozása Azure Resource Graph](../governance/resource-graph/first-query-portal.md)

- [Azure Policy beépített mintaalkalmazások a Azure Automation](policy-reference.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7: Nem jóváhagyott Azure-erőforrások és -szoftveralkalmazások eltávolítása

**Útmutató:** Az ügyfél megakadályozhatja az erőforrás-létrehozást vagy a Azure Policy az ügyfél céges irányelveinek megfelelően. A jogosulatlan erőforrások eltávolítására saját folyamatot is megvalósíthat. A Azure Automation ajánlaton belül telepítheti, eltávolíthatja és kezelheti a PowerShell- vagy Python-modulokat, amelyekhez a runbookok a portálon vagy parancsmagokkal férhetnek hozzá. A nem jóváhagyott vagy a régi modult el kell távolítani vagy frissíteni kell a runbookok esetén.

- [Az Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md)

- [Modul kezelése a Azure Automation](shared-resources/modules.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="69-use-only-approved-azure-services"></a>6.9: Csak jóváhagyott Azure-szolgáltatások használata

**Útmutató:** Azure Policy az alábbi beépített szabályzatdefiníciók használatával az ügyfél-előfizetésben létre lehet hozható erőforrások típusára vonatkozó korlátozásokat:
- Nem engedélyezett erőforrástípusok
- Engedélyezett erőforrástípusok

- [Az Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md)

- [Adott erőforrástípus megtagadása a Azure Policy](../governance/policy/samples/built-in-policies.md#general)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6.11: Korlátozza a felhasználók számára a Azure Resource Manager

**Útmutató:** Az Azure feltételes hozzáférési szabályzatokkal korlátozhatja a felhasználók Azure Resource Manager-val való interakcióját a "hozzáférés blokkolása" beállításával a "Microsoft Azure Management" alkalmazáshoz nem biztonságos vagy nem jóváhagyott helyekről vagy eszközökről. 

- [Feltételes hozzáférés konfigurálása a hozzáférés letilt Azure Resource Manager](../role-based-access-control/conditional-access-azure-management.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

## <a name="secure-configuration"></a>Biztonságos konfiguráció

*További információ: [Azure biztonsági teljesítményteszt: Biztonságos konfiguráció.](../security/benchmarks/security-control-secure-configuration.md)*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: Biztonságos konfigurációk létrehozása az összes Azure-erőforráshoz

**Útmutató:** Azure Policy aliasok használatával egyéni szabályzatokat hozhat létre, amelyek naplót végeznek vagy kikényszeríteni a Azure Automation és a kapcsolódó erőforrások konfigurációját. Beépített definíciókat is Azure Policy használhat.

Emellett a Azure Resource Manager képes exportálni a sablont az JavaScript Object Notation-ban (JSON), amelyet át kell vizsgálni annak biztosítása érdekében, hogy a konfigurációk megfelelnek vagy meghaladják a szervezet biztonsági követelményeit.

Az Azure-erőforrások Azure Security Center alapkonfigurációjaként is használhatja az azure-erőforrásokra vonatkozó javaslatokat.

- [Az elérhető aliasok Azure Policy megtekintése](/powershell/module/az.resources/get-azpolicyalias)

- [Oktatóanyag: Szabályzatok létrehozása és kezelése a megfelelőség kikényszeríteni érdekében](../governance/policy/tutorials/create-and-manage.md)

- [Azure Policy beépített mintaalkalmazások a Azure Automation](policy-reference.md)

- [Egy- és többerőforrásos exportálás sablonba a Azure Portal](../azure-resource-manager/templates/export-template-portal.md)

- [Biztonsági javaslatok – gyorsútmutató](../security-center/recommendations-reference.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: Biztonságos Azure-erőforrás-konfigurációk fenntartása

**Útmutató:** A Azure Resource Manager és Azure Policy azure-erőforrások biztonságosan konfigurálhatóak a Azure Automation. Azure Resource Manager sablonok Azure-erőforrások üzembe helyezéséhez használt JSON-alapú fájlok, és az egyéni sablonokat biztonságosan kell tárolni és karbantartani egy kódtárban. A forrásvezérlés integrációs funkciójának használatával naprakészen tarthatja az Automation-fiókjában futó runbookokat a forrásvezérlő adattárban szkriptekkel. A Azure Policy [deny] és a [deploy if not exist] ([Üzembe helyezés, ha nem létezik]) beállításokkal kényszerítheti a biztonságos beállításokat az Azure-erőforrásokon.

- [Verziókövetés-integrálás használata](source-control-integration.md)

- [Információk a sablonsablonok Azure Resource Manager létrehozásáról](../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md) 

- [Az Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md) 

- [A Azure Policy hatásainak ismertetése](../governance/policy/concepts/effects.md)

- [Automation-fiók üzembe helyezése Azure Resource Manager sablonnal](/azure/automation/quickstart-create-automation-account-template)

- [Azure Policy beépített mintaalkalmazások a Azure Automation](policy-reference.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Az Azure-erőforrások konfigurációjának biztonságos tárolása

**Útmutató:** Az Azure DevOps használatával biztonságosan tárolhatja és kezelheti a kódot, például egyéni Azure-szabályzatokat, Azure Resource Manager sablonokat és Desired State Configuration szkripteket. Az Azure DevOpsban kezelett erőforrások eléréséhez engedélyeket adhat vagy tagadhat meg adott felhasználóknak, beépített biztonsági csoportoknak vagy az Azure Active Directory-ben (Azure AD)-ben meghatározott csoportoknak, ha integrálva vannak az Azure DevOps-ral, vagy a Active Directory,ha integrálva van a TFS-sel. A forrásvezérlés integrációs funkciójának használatával naprakészen tarthatja az Automation-fiókjában futó runbookokat a forrásvezérlő adattárban szkriptekkel.

- [Kód tárolása az Azure DevOpsban](/azure/devops/repos/git/gitworkflow)

- [Az Azure DevOps engedélyeinek és csoportjainak](/azure/devops/organizations/security/about-permissions)

- [Verziókövetés-integrálás használata](source-control-integration.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7: Konfigurációkezelési eszközök üzembe helyezése Azure-erőforrásokhoz

**Útmutató:** Standard biztonsági konfigurációk definiálása és megvalósítása Azure-erőforrásokhoz a Azure Policy. Az Azure Policy használatával egyéni szabályzatokat hozhat létre az Azure-erőforrások hálózati konfigurációjának naplózásához vagy kikényszerításához. Az egyes erőforrásokhoz kapcsolódó beépített szabályzatdefiníciókat is használhat. 

- [Az Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md)

- [Aliasok használata](../governance/policy/concepts/definition-structure.md#aliases)

- [Azure Policy beépített minta a Azure Automation](policy-reference.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9: Automatizált konfigurációfigyelés megvalósítása Azure-erőforrásokhoz

**Útmutató:** A Azure Policy az Azure-erőforráskonfigurációk riasztására és naplózására, a szabályzattal észlelheti azokat az erőforrásokat, amelyek nem privát végponttal vannak konfigurálva.

A hibrid runbook-feldolgozó szolgáltatás használata esetén Azure Security Center azure-beli virtuális gépek alapkonfiguráció-vizsgálatának elvégzéséhez.  Az automatikus konfigurálás további módszerei a következők: Azure Automation State Configuration.

- [Javaslatok szervizlének Azure Security Center](../security-center/security-center-remediate-recommendations.md)

- [Ismerkedés a Azure Automation State Configuration](automation-dsc-getting-started.md)

- [Az Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md)

- [Azure Policy beépített minta a Azure Automation](policy-reference.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: A hitelesítő adatok nem szándékolt kitettségének megszüntetése

**Útmutató:** Hitelesítőadat-ellenőrző implementálja a hitelesítő adatokat a kódon belül. A Credential Scanner a felfedezett hitelesítő adatok biztonságosabb helyre, például az Azure Key Vaultba való áthelyezésére is javaslatot tesz. 

- [Hitelesítőadat-ellenőrző beállítása](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

## <a name="data-recovery"></a>Adat-helyreállítás

*További információ: [Azure biztonsági teljesítményteszt: Adat-helyreállítás.](../security/benchmarks/security-control-data-recovery.md)*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: Rendszeres automatikus biztonságimentek biztosítása

**Útmutató:** Azure Resource Manager fiókok és Azure Automation erőforrások üzembe helyezéséhez. Azure Resource Manager lehetővé teszi olyan sablonok exportálását, amelyek biztonsági másolatként használhatók Azure Automation fiókok és a kapcsolódó erőforrások visszaállításához. A Azure Automation a Azure Resource Manager API-t.

A forrásvezérlés integrációs funkciójának használatával naprakészen tarthatja az Automation-fiókjában futó runbookokat a forrásvezérlő adattárban szkriptekkel.

- [Az Azure Resource Manager áttekintése](../azure-resource-manager/management/overview.md)

- [Azure Resource Manager referenciasablonok a Azure Automation erőforrásokhoz](/azure/templates/microsoft.automation/allversions)

- [Automation-fiók létrehozása Azure Resource Manager sablonnal](quickstart-create-automation-account-template.md)

- [Egy- és többerőforrásos exportálás sablonba a Azure Portal](../azure-resource-manager/templates/export-template-portal.md)

- [Erőforráscsoportok – Sablon exportálása](/rest/api/resources/resourcegroups/exporttemplate)

- [Bevezetés a Azure Automation](automation-intro.md)

- [Kulcstartókulcsok biztonsági mentése az Azure-ban](/powershell/module/az.keyvault/backup-azkeyvaultkey)

- [Felhasználó által kezelt kulcsok használata Automation-fiókhoz](./automation-secure-asset-encryption.md#use-of-customer-managed-keys-for-an-automation-account)

- [Verziókövetés-integrálás használata](source-control-integration.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: Teljes rendszer-biztonsági mentések és az ügyfelek által kezelt kulcsok biztonsági mentése

**Útmutató:** Azure Resource Manager fiókok és Azure Automation erőforrások üzembe helyezéséhez. Azure Resource Manager lehetővé teszi olyan sablonok exportálását, amelyek biztonsági másolatként használhatók Azure Automation fiókok és a kapcsolódó erőforrások visszaállításához. A Azure Automation a Azure Resource Manager API-t. A felhasználó által felügyelt kulcsok biztonsági Azure Key Vault. A runbookokat szkriptfájlokba exportálhatja a Azure Portal Vagy a PowerShell használatával.

- [Az Azure Resource Manager áttekintése](../azure-resource-manager/management/overview.md)

- [Azure Resource Manager referenciasablonok a Azure Automation erőforrásokhoz](/azure/templates/microsoft.automation/allversions)

- [Automation-fiók létrehozása Azure Resource Manager sablonnal](quickstart-create-automation-account-template.md)

- [Egy- és többerőforrásos exportálás sablonba a Azure Portal](../azure-resource-manager/templates/export-template-portal.md)

- [Erőforráscsoportok – Sablon exportálása](/rest/api/resources/resourcegroups/exporttemplate)

- [Bevezetés a Azure Automation](automation-intro.md)

- [Kulcstartókulcsok biztonsági mentése az Azure-ban](/powershell/module/az.keyvault/backup-azkeyvaultkey)

- [Felhasználó által kezelt kulcsok használata Automation-fiókhoz](./automation-secure-asset-encryption.md#use-of-customer-managed-keys-for-an-automation-account)

- [Azure-adatok biztonsági mentése Automation-fiókokhoz](./automation-managing-data.md#data-backup)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: Az összes biztonsági mentés ellenőrzése, beleértve az ügyfél által kezelt kulcsokat is

**Útmutató:** Szükség esetén győződjön meg arról, hogy rendszeresen el tudja végezni a Azure Resource Manager-sablonok üzembe helyezését egy elkülönített előfizetésen. Tesztelje az ügyfél által kezelt kulcsok biztonságimentett visszaállítását.

- [Erőforrások üzembe helyezése ARM-sablonokkal és Azure Portal](../azure-resource-manager/templates/deploy-portal.md)

- [Kulcstartókulcsok visszaállítása az Azure-ban](/powershell/module/az.keyvault/restore-azkeyvaultkey)

- [Felhasználó által kezelt kulcsok használata Automation-fiókhoz](./automation-secure-asset-encryption.md#use-of-customer-managed-keys-for-an-automation-account)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: A biztonsági másolatok és az ügyfél által kezelt kulcsok védelmének biztosítása

**Útmutató:** Az Azure DevOps használatával biztonságosan tárolhatja és kezelheti a kódot, például Azure Resource Manager sablonokat. Az Azure DevOpsban felügyelt erőforrások védelméhez engedélyeket adhat vagy tagadhat meg adott felhasználóknak, beépített biztonsági csoportoknak vagy az Azure Active Directory-ben (Azure AD)-ben meghatározott csoportoknak, ha integrálva vannak az Azure DevOps-ral, vagy a Active Directory,ha integrálva van a TFS-sel.

A forrásvezérlés integrációs funkciójának használatával naprakészen tarthatja az Automation-fiókjában futó runbookokat a forrásvezérlő adattárban szkriptekkel.

- [Kód tárolása az Azure DevOpsban](/azure/devops/repos/git/gitworkflow)

- [Az Azure DevOps engedélyeinek és csoportjainak](/azure/devops/organizations/security/about-permissions)

- [Verziókövetés-integrálás használata](source-control-integration.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

## <a name="incident-response"></a>Incidensmegoldás

*További információ: [Azure Security Benchmark: Incidensek kezelése](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10.1: Incidensválasz-útmutató létrehozása

**Útmutató**: Alakítson ki incidenskezelési útmutatót a vállalat számára. Gondoskodjon írásos incidenskezelési tervekről, amelyek definiálják az összes résztvevő szerepkörét, valamint az incidenskezelés fázisait az észleléstől az incidens utáni értékelésig.

- [Útmutatás saját biztonsági incidensek válaszadási folyamatának építéshez](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Microsoft Biztonsági reagálási központ az incidensek felépítése](https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/)

- [Az ügyfelek az NIST számítógépes biztonsági incidenskezelési útmutatóját is kihasználva segíthetnek a saját incidenskezelési tervük létrehozásában](https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: Incidenspontozási és -rangsorolási eljárás létrehozása

**Útmutató:** Security Center súlyosságot rendel az egyes riasztásokhoz, így fontossági sorrendbe adhatja, hogy mely riasztásokat kell először megvizsgálni. A súlyosság azon alapul, Security Center mennyire biztos a riasztás kiadásához használt eredményben vagy elemzésben, valamint hogy a riasztáshoz vezető tevékenység mögött rosszindulatú szándék volt-e. 

Emellett egyértelműen jelöljük meg az előfizetéseket (például: éles környezetben, nem éles környezetben) címkéket használ, és létrehoz egy elnevezési rendszert az Azure-erőforrások egyértelmű azonosításához és kategorizálásához, különösen a bizalmas adatokat feldolgozók számára.  Az Ön felelőssége, hogy rangsorolja a riasztások megoldását azon Azure-erőforrások és -környezetek kritikussága alapján, ahol az incidens történt.

- [Biztonsági riasztások az Azure Security Centerben](../security-center/security-center-alerts-overview.md)

- [Címkék használata az Azure-erőforrások rendszerezéséhez](../azure-resource-manager/management/tag-resources.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="103-test-security-response-procedures"></a>10.3: Biztonsági reagálási eljárások tesztelése

**Útmutató:** Gyakorlatok a rendszerek incidensválasz-képességeinek rendszeres teszteléséhez az Azure-erőforrások védelme érdekében. Azonosítsa a gyenge pontokat és réseket, és szükség esetén dolgozza át a tervet.

- [NIST-kiadvány – Útmutató az IT-tervekhez és -képességekhez szükséges tesztelési, képzési és gyakorlatprogramokhoz](https://csrc.nist.gov/publications/detail/sp/800-84/final)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás:** Nincs

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: Adja meg a biztonsági incidens kapcsolattartási adatait, és konfigurálja a riasztási értesítéseket a biztonsági incidensek esetén

**Útmutató:** A Biztonsági incidensek kapcsolattartási adatait a Microsoft arra használja, hogy kapcsolatba lépjen Önnel, ha a Microsoft Biztonsági reagálási központ (MSRC) azt tapasztalja, hogy illetéktelen vagy jogosulatlan fél fért hozzá adataihoz. Tekintse át az incidenseket a tény után, és győződjön meg arról, hogy a problémák megoldódnak.

- [A biztonsági kapcsolattartó Azure Security Center beállítása](../security-center/security-center-provide-security-contact-details.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: Biztonsági riasztások beépítése az incidensválasz-rendszerbe

**Útmutató:** A riasztások Azure Security Center és javaslatokat exportálhatja a Continuous Export (Folyamatos exportálás) funkcióval az Azure-erőforrásokat érintő kockázatok azonosítása érdekében. A folyamatos exportálás lehetővé teszi, hogy manuálisan vagy folyamatosan exportálja a riasztásokat és javaslatokat. A riasztásokat a Azure Security Center adat-összekötővel streamelheti a Azure Sentinel.

- [Folyamatos exportálás konfigurálása](../security-center/continuous-export.md)

- [Riasztások streamelése az Azure Sentinelbe](../sentinel/connect-azure-security-center.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: A biztonsági riasztások válaszának automatizálása

**Útmutató:** A Azure Security Center-munkafolyamat-automatizálási funkcióval automatikusan aktiválhatja a válaszokat a biztonsági riasztások Logic Apps javaslatokon keresztül az Azure-erőforrások védelme érdekében.

- [A munkafolyamat-automatizálás és -Logic Apps](../security-center/workflow-automation.md)

**Felelősség**: Ügyfél

**Azure Security Center:** Nincs

## <a name="penetration-tests-and-red-team-exercises"></a>Behatolási tesztek és Red Team-gyakorlatok

*További információ: [Azure biztonsági teljesítményteszt: Behatolási tesztek és Red Team-gyakorlatok.](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md)*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11.1: Az Azure-erőforrások rendszeres behatolástesztelése és az összes kritikus fontosságú biztonsági eredmény szervizelésének biztosítása 

**Útmutató:** Kövesse a Microsoft kötelezettségvállalási szabályait annak biztosításához, hogy a behatolási tesztek ne sértsen meg Microsoft-szabályzatokat. A Microsoft által felügyelt felhőalapú infrastruktúrán, szolgáltatásokon és alkalmazásokon végzett riasztási és élő behatolási tesztek végrehajtásához használja a Microsoft stratégiáját és végrehajtási tervét.

- [Behatolástesztelési beavatkozási szabályok](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)

- [Riasztási tesztek a Microsoft-felhőben](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Felelősség**: Megosztott

**Azure Security Center:** Nincs

## <a name="next-steps"></a>Következő lépések

- [Az Azure Security Benchmark v2 áttekintésének](../security/benchmarks/overview.md) megtekintése
- További tudnivalók az [Azure biztonsági alapterveiről](../security/benchmarks/security-baselines-overview.md)
