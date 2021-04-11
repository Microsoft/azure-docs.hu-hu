---
title: Service Fabric Azure biztonsági alapterve
description: A Service Fabric biztonsági alapterve az Azure biztonsági Teljesítménytesztben meghatározott biztonsági javaslatok megvalósítására szolgáló eljárási útmutatást és erőforrásokat biztosít.
author: msmbaldwin
ms.service: service-fabric
ms.topic: conceptual
ms.date: 04/08/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: ee294cff85bb71b5c2a238fcf985fc870ed32618
ms.sourcegitcommit: c6a2d9a44a5a2c13abddab932d16c295a7207d6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/09/2021
ms.locfileid: "107285444"
---
# <a name="azure-security-baseline-for-service-fabric"></a>Service Fabric Azure biztonsági alapterve

Ez a biztonsági alapkonfiguráció az [Azure Security benchmark 1.0-s verziójának](../security/benchmarks/overview-v1.md) Service Fabricára vonatkozó útmutatást alkalmazza. Az Azure Security Benchmark ajánlásokat ad arra nézve, hogy hogyan tehetők biztonságossá a felhőalapú megoldások az Azure-ban. A tartalom az Azure biztonsági teljesítményteszt által meghatározott **biztonsági vezérlők** szerint van csoportosítva, valamint a Service Fabric vonatkozó kapcsolódó útmutatás.

> [!NOTE]
> Az Service Fabricre nem alkalmazható **vezérlők** , vagy amelyek esetében a felelősség a Microsoft által lett kizárva. Ha szeretné megtekinteni, hogyan Service Fabric teljes mértékben leképezni az Azure biztonsági Teljesítménytesztét, tekintse meg a **[teljes Service Fabric biztonsági alapterv-leképezési fájlt](https://github.com/MicrosoftDocs/SecurityBenchmarks/raw/master/Azure%20Offer%20Security%20Baselines/1.1/service-fabric-security-baseline-v1.1.xlsx)**.

## <a name="network-security"></a>Hálózati biztonság

*További információ: [Azure Security Benchmark: Hálózati biztonság](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1,1: Azure-erőforrások biztosítása virtuális hálózatokon belül

**Útmutató**: gondoskodjon arról, hogy az összes Virtual Network alhálózati üzemelő példány rendelkezzen egy hálózati biztonsági csoporttal, amely az alkalmazás megbízható portjaira és forrásaira jellemző hálózati hozzáférés-vezérléssel van alkalmazva. 

- [Azure Firewall üzembe helyezése sablon használatával](../firewall/deploy-template.md)

- [Peremhálózati hálózatok létrehozása Azure hálózati biztonsági csoportok (NSG) használatával](https://docs.microsoft.com/azure/security/fundamentals/service-fabric-best-practices#use-network-isolation-and-security-with-azure-service-fabric)

- [Azure Service Fabric-fürt integrálása meglévő virtuális hálózattal](service-fabric-patterns-networking.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-nics"></a>1,2: a virtuális hálózatok, alhálózatok és hálózati adapterek konfigurációjának és forgalmának figyelése és naplózása

**Útmutató**: az Azure Service Fabric-fürt biztonságossá tételéhez használt virtuális hálózatra, alhálózatra és hálózati biztonsági csoportra vonatkozó hálózati védelmi javaslatok Security Center használata és szervizelése. Engedélyezze a hálózati biztonsági csoport folyamatábráit, és küldje el a naplókat egy Azure Storage-fiókba a forgalmi naplóba. Hálózati biztonsági csoportokat is küldhet egy Azure Log Analytics-munkaterületre, és az Azure Traffic Analytics használatával betekintést nyerhet az Azure-Felhőbeli forgalomba. Az Azure Traffic Analytics egyes előnyei lehetővé teszi a hálózati tevékenységek megjelenítését és a gyakori pontok azonosítását, a biztonsági fenyegetések azonosítását, a forgalomban rejlő minták értelmezését, valamint a hálózati helytelen konfigurációk meghatározását.

- [A NSG folyamat naplófájljainak engedélyezése](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Az Azure Traffic Analytics engedélyezése és használata](../network-watcher/traffic-analytics.md)

- [A Azure Security Center által biztosított hálózati biztonság ismertetése](../security-center/security-center-network-recommendations.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="13-protect-critical-web-applications"></a>1,3: a kritikus webalkalmazások megóvása

**Útmutató**: adjon meg egy előtér-átjárót, amely egyetlen pontot biztosít a felhasználók, eszközök és egyéb alkalmazások számára. Az Azure API Management közvetlenül integrálható Service Fabricekkel, így biztosíthatja a háttér-szolgáltatásokhoz való hozzáférést, megakadályozhatja a DOS-támadások szabályozását, és ellenőrizheti az API-kulcsokat, az JWT tokeneket, a tanúsítványokat és az egyéb hitelesítő adatokat.

A bejövő forgalom további ellenőrzéséhez vegye fontolóra az Azure webalkalmazási tűzfal (WAF) üzembe helyezését a kritikus webalkalmazások előtt. Diagnosztikai beállítás engedélyezése a WAF és a naplók betöltéséhez egy Storage-fiókba, az Event hub-ba vagy a Log Analytics-munkaterületre.

- [Service Fabric és Azure API Management – áttekintés](service-fabric-api-management-overview.md)

- [Az API Management és az Application Gateway integrálása egy belső VNET-en](../api-management/api-management-howto-integrate-internal-vnet-appgateway.md)

- [Az Azure WAF üzembe helyezése](../web-application-firewall/ag/create-waf-policy-ag.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1,4: az ismert kártékony IP-címekkel folytatott kommunikáció megtagadása

**Útmutató**: a DDOS-támadások elleni védelem érdekében engedélyezze az Azure DDoS standard Protection szolgáltatást azon a virtuális hálózaton, amelyen az Azure Service Fabric-fürt üzembe lett helyezve. A Azure Security Center integrált fenyegetési intelligencia használatával megtagadhatja a kommunikációt az ismert kártékony vagy nem használt internetes IP-címekkel.

- [A DDoS Protection konfigurálása](../ddos-protection/manage-ddos-protection.md)

- [Az Azure Security Center integrált veszélyforrások felderítésének megismerése](../security-center/azure-defender.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="15-record-network-packets"></a>1,5: hálózati csomagok rögzítése

**Útmutató**: engedélyezze a hálózati biztonsági csoport folyamatábráit a Service Fabric-fürt védelméhez használt alhálózathoz csatolt hálózati biztonsági csoportokhoz. Jegyezze fel a NSG folyamat naplóit egy Azure Storage-fiókba a flow-rekordok létrehozásához. Ha a rendellenes tevékenységek kivizsgálásához szükséges, engedélyezze az Azure Network Watcher a csomagok rögzítését.

- [A NSG folyamat naplófájljainak engedélyezése](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [A Network Watcher engedélyezése](../network-watcher/network-watcher-create.md)

- [A Traffic Analytics használata a NSG-naplók megjelenítéséhez](../network-watcher/traffic-analytics.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1,6: hálózati behatolás-észlelési/Behatolás-megelőzési rendszerek (AZONOSÍTÓk/IP-címek) üzembe helyezése

**Útmutató**: válasszon ki egy olyan ajánlatot az Azure piactéren, amely támogatja az azonosítók/IP-címek funkciót a hasznos adatok ellenőrzésére szolgáló funkciókkal.  Ha a betörési észlelés és/vagy a tartalom-ellenőrzésen alapuló megelőzés nem követelmény, akkor az Azure Firewall a veszélyforrások felderítésére használható. Azure Firewall fenyegetés intelligencián alapuló szűréssel riasztást kaphat, és megtagadhatja az ismert kártékony IP-címek és tartományok felé irányuló forgalmat. Az IP-címek és -tartományok forrása a Microsoft veszélyforrás-felderítési hírcsatornája. 

Az Ön által választott tűzfal-megoldás üzembe helyezésével azonosíthatja és/vagy megtagadhatja a kártékony forgalmat. 

- [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/?term=Firewall) 

- [Azure Firewall üzembe helyezése](../firewall/tutorial-firewall-deploy-portal.md) 

- [Riasztások konfigurálása Azure Firewall](../firewall/threat-intel.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="17-manage-traffic-to-web-applications"></a>1,7: webalkalmazások forgalmának kezelése

**Útmutató**: Azure-Application Gateway üzembe helyezése a megbízható tanúsítványokhoz engedélyezett https/SSL-alapú webalkalmazásokhoz. 

- [Application Gateway üzembe helyezése](../application-gateway/quick-create-portal.md)

- [A Application Gateway konfigurálása a HTTPS használatára](../application-gateway/create-ssl-portal.md)

- [A 7. rétegbeli terheléselosztás és az Azure-webalkalmazás-átjárók ismertetése](../application-gateway/overview.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1,8: a hálózati biztonsági szabályok bonyolultságának és adminisztratív terhelésének csökkentése

**Útmutató**: a virtuális hálózati szolgáltatás címkéi segítségével definiálhatja azokat a hálózati biztonsági csoportokat (NSG), amelyek az Azure Service Fabric-fürt üzembe helyezéséhez használt alhálózathoz csatlakoznak. Biztonsági szabályok létrehozása során szolgáltatáscímkéket használhat bizonyos IP-címek helyett. Ha egy szabály megfelelő forrás vagy cél mezőjében megadja a szolgáltatási címke nevét (például ApiManagement), akkor engedélyezheti vagy megtagadhatja a megfelelő szolgáltatás forgalmát. A Microsoft kezeli a szolgáltatási címke által felölelt címek előtagjait, és automatikusan frissíti a szolgáltatási címkét a címek változásával.

- [Virtuális hálózati szolgáltatás címkéi](../virtual-network/service-tags-overview.md)

- [Service Fabric hálózatkezelés – ajánlott eljárások](service-fabric-best-practices-networking.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1,9: a hálózati eszközök szabványos biztonsági konfigurációinak fenntartása

**Útmutató**: az Azure Service Fabric-fürthöz kapcsolódó hálózati erőforrások szabványos biztonsági konfigurációinak meghatározása és implementálása. Használjon Azure Policy aliasokat a "Microsoft. ServiceFabric" és a "Microsoft. Network" névterekben egyéni szabályzatok létrehozásához az Azure Service Fabric-fürt hálózati konfigurációjának naplózásához vagy érvénybe léptetéséhez.

Az Azure-tervrajzok segítségével leegyszerűsítheti a nagyméretű Azure-alapú üzembe helyezéseket, például a Azure Resource Manager sablonokat, az Azure RBAC-vezérlőket és a házirendeket egyetlen terv definíciójában. Egyszerűen alkalmazhatja a tervrajzot új előfizetésekre és környezetekre, és részletesen beállíthatja a vezérlést és a felügyeletet a verziószámozással.

- [Az elérhető Azure Policy aliasok megtekintése](https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-4.8.0&amp;preserve-view=true)

- [Az Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md)

- [Azure Blueprint létrehozása](../governance/blueprints/create-blueprint-portal.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="110-document-traffic-configuration-rules"></a>1,10: a dokumentum forgalmának konfigurációs szabályai

**Útmutató**: a hálózati biztonsági csoportokhoz és a Service Fabric-fürthöz társított hálózati biztonsággal és forgalommal kapcsolatos egyéb erőforrásokhoz használható címkék használata. A hálózati biztonsági csoportok egyedi szabályaihoz a "Leírás" mező használatával adhatja meg az üzleti igényeket, az időtartamot és így tovább, minden olyan szabály esetében, amely engedélyezi a hálózatra irányuló adatforgalmat.

A címkézéshez kapcsolódó beépített Azure Policy definíciók bármelyikét használhatja, például a "címke és az érték megkövetelése" beállítást, hogy az összes erőforrás címkével legyen létrehozva, és értesítse a meglévő címkézetlen erőforrásokról.

A Azure PowerShell vagy az Azure parancssori felülete (CLI) használatával felkeresheti vagy végrehajthatja az erőforrásokon végzett műveleteket a címkék alapján.

- [Címkék létrehozása és használata](../azure-resource-manager/management/tag-resources.md)

- [Virtuális hálózat létrehozása](../virtual-network/quick-create-portal.md)

- [NSG létrehozása biztonsági konfigurációval](../virtual-network/tutorial-filter-network-traffic.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1,11: automatikus eszközök használata a hálózati erőforrások konfigurációjának figyelésére és a változások észlelésére

**Útmutató**: az Azure-tevékenység naplójának használata a hálózati erőforrás-konfigurációk figyelésére és az Azure Service Fabric üzemelő példányokhoz kapcsolódó hálózati erőforrások változásainak észlelésére. Hozzon létre riasztásokat Azure Monitoron belül, amelyek akkor lépnek életbe, amikor a kritikus hálózati erőforrásokra vonatkozó módosításokat végrehajtják

- [Azure-Tevékenységnaplók eseményeinek megtekintése és lekérése](/azure/azure-monitor/platform/activity-log#view-the-activity-log)

- [Riasztások létrehozása a Azure Monitorban](/azure/azure-monitor/platform/alerts-activity-log)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

## <a name="logging-and-monitoring"></a>Naplózás és monitorozás

*További információt az [Azure biztonsági teljesítményteszt: naplózás és figyelés](../security/benchmarks/security-control-logging-monitoring.md)című témakörben talál.*

### <a name="22-configure-central-security-log-management"></a>2,2: a központi biztonsági naplók felügyeletének konfigurálása

Útmutató: az Azure Service Fabric-fürt **Bevezetésével** Azure monitor a fürt által generált biztonsági adatokat összesítve. Lásd: diagnosztikai problémák és megoldások a Service Fabric.

- [Azure Monitor naplók konfigurálása Service Fabric-integrációval](service-fabric-diagnostics-oms-setup.md)

- [Azure Monitor naplók beállítása a tárolók figyeléséhez az Azure-ban Service Fabric](service-fabric-tutorial-monitoring-wincontainers.md)

- [Gyakori Service Fabric forgatókönyvek diagnosztizálása](service-fabric-diagnostics-common-scenarios.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="23-enable-audit-logging-for-azure-resources"></a>2,3: az Azure-erőforrások naplózásának engedélyezése

**Útmutató**: engedélyezze a Azure Monitor a Service Fabric-fürthöz, és irányítsa azt egy log Analytics munkaterületre. Ez naplózza a kapcsolódó fürt adatait és az operációsrendszer-metrikákat az összes Azure Service Fabric-fürtcsomópontok esetében.

- [Azure Monitor naplók konfigurálása Service Fabric-integrációval](service-fabric-diagnostics-oms-setup.md)

- [Azure Monitor naplók beállítása a tárolók figyeléséhez az Azure-ban Service Fabric](service-fabric-tutorial-monitoring-wincontainers.md)

- [A Log Analytics-ügynök üzembe helyezése a csomópontokon](service-fabric-diagnostics-oms-agent.md)

- [Log Analytics naplók keresése](/azure/azure-monitor/log-query/log-query-overview)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="24-collect-security-logs-from-operating-systems"></a>2,4: biztonsági naplók gyűjtése az operációs rendszerekből

**Útmutató**: az Azure Service Fabric-fürt előkészítése Azure Monitorre. Győződjön meg arról, hogy a használt Log Analytics munkaterületen a szervezet megfelelőségi előírásai szerint beállított napló-megőrzési időtartam szerepel.

- [Azure Monitor naplók konfigurálása Service Fabric-integrációval](service-fabric-diagnostics-oms-setup.md)

- [Azure Monitor naplók beállítása a tárolók figyeléséhez az Azure-ban Service Fabric](service-fabric-tutorial-monitoring-wincontainers.md)

- [A Log Analytics-ügynök üzembe helyezése a csomópontokon](service-fabric-diagnostics-oms-agent.md) 

- [Log Analytics munkaterület megőrzési időtartamának konfigurálása](/azure/azure-monitor/platform/manage-cost-storage)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="25-configure-security-log-storage-retention"></a>2,5: a biztonsági napló tárolási adatmegőrzésének konfigurálása

**Útmutató**: a Service Fabric-fürt beléptetése Azure monitorra. Győződjön meg arról, hogy a használt Log Analytics munkaterületen a szervezet megfelelőségi előírásai szerint beállított napló-megőrzési időtartam szerepel.

- [Azure Monitor naplók konfigurálása Service Fabric-integrációval](service-fabric-diagnostics-oms-setup.md)

- [Azure Monitor naplók beállítása a tárolók figyeléséhez az Azure-ban Service Fabric](service-fabric-tutorial-monitoring-wincontainers.md)

- [A Log Analytics-ügynök üzembe helyezése a csomópontokon](service-fabric-diagnostics-oms-agent.md)

- [Log Analytics munkaterület megőrzési időtartamának konfigurálása](/azure/azure-monitor/platform/manage-cost-storage)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="26-monitor-and-review-logs"></a>2,6: naplók figyelése és áttekintése

**Útmutató**: azure-log Analytics munkaterület-lekérdezések használata az Azure Service Fabric-naplók lekérdezéséhez.

- [Log Analytics naplók keresése](/azure/azure-monitor/log-query/log-query-overview)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="27-enable-alerts-for-anomalous-activities"></a>2,7: riasztások engedélyezése rendellenes tevékenységekhez

**Útmutató**: az Azure log Analytics munkaterületének használata az Azure Service Fabric-fürttel kapcsolatos rendellenes tevékenységekre vonatkozó figyeléshez és riasztáshoz.

- [Riasztások kezelése Azure Security Centerban](../security-center/security-center-managing-and-responding-alerts.md)

- [Riasztás a log Analytics-naplófájlok adatkezeléséről](/azure/azure-monitor/learn/tutorial-response)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="28-centralize-anti-malware-logging"></a>2,8: kártevő szoftverek közötti naplózás központosítása

**Útmutató**: alapértelmezés szerint a Windows Defender a windows Server 2016 rendszerre van telepítve. Ha nem a Windows Defendert használja, tekintse meg a Antimaleware dokumentációját a konfigurációs szabályokhoz. A Windows Defender Linux rendszeren nem támogatott.

- [Részletekért lásd: Windows Defender Antivirus Windows Server 2016](/windows/security/threat-protection/windows-defender-antivirus/windows-defender-antivirus-on-windows-server-2016)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="29-enable-dns-query-logging"></a>2,9: DNS-lekérdezések naplózásának engedélyezése

**Útmutató**: harmadik féltől származó megoldás implementálása a DNS-naplózáshoz.

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="210-enable-command-line-audit-logging"></a>2,10: parancssori naplózás engedélyezése

**Útmutató**: a konzol naplózásának manuális konfigurálása a csomópontok alapján.

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

## <a name="identity-and-access-control"></a>Identitás- és hozzáférés-vezérlés

*További információt az [Azure biztonsági teljesítményteszt: Identity and Access Control](../security/benchmarks/security-control-identity-access-control.md)című témakörben talál.*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3,1: a felügyeleti fiókok leltárának karbantartása

**Útmutató**: az Azure Service Fabric-fürt kiépítés során létrehozott helyi rendszergazdai fiók, valamint a létrehozott egyéb fiókok nyilvántartásának fenntartása. Továbbá, ha Azure Active Directory (Azure AD) integrációt használ, az Azure AD olyan beépített szerepköröket tartalmaz, amelyeket explicit módon kell hozzárendelni, és ezért lekérdezhető. Az Azure AD PowerShell-modul használatával ad hoc lekérdezéseket a felügyeleti csoportok tagjait futtató fiókok felderítéséhez.

Emellett Azure Security Center identitás-és hozzáférés-kezelési javaslatokat is használhat.

- [Címtárbeli szerepkör beszerzése az Azure AD-ben a PowerShell-lel](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0&amp;preserve-view=true)

- [Címtárbeli szerepkör tagjainak beszerzése az Azure AD-ben a PowerShell-lel](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0&amp;preserve-view=true)

- [Identitás és hozzáférés figyelése Azure Security Center](../security-center/security-center-identity-access.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="32-change-default-passwords-where-applicable"></a>3,2: az alapértelmezett jelszavak módosítása, ha alkalmazható

**Útmutató**: fürt kiépítés esetén az Azure-ban új jelszavakat kell létrehoznia a webes portálhoz. Nincsenek módosítható alapértelmezett jelszavak, azonban más jelszavakat is megadhat a webes portálhoz való hozzáféréshez.

- [Létrehozás az Azure Portalon](service-fabric-cluster-creation-via-portal.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="33-use-dedicated-administrative-accounts"></a>3,3: dedikált rendszergazdai fiókok használata

**Útmutató**: a Service Fabric hitelesítésének integrálása Azure Active Directory (Azure ad) használatával. Szabályzatokat és eljárásokat hozhat létre a dedikált rendszergazdai fiókok használata körül.

Emellett Security Center identitás-és hozzáférés-kezelési javaslatokat is használhat.

- [Az Azure AD-ügyfél hitelesítésének beállítása](https://docs.microsoft.com/azure/service-fabric/service-fabric-tutorial-create-vnet-and-windows-cluster#set-up-azure-active-directory-client-authentication)

- [Identitás és hozzáférés figyelése Azure Security Center](../security-center/security-center-identity-access.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3,4: egyszeri bejelentkezés (SSO) használata Azure Active Directory

**Útmutató**: ha lehetséges, használjon Azure Active Directory (Azure ad) SSO-t a különálló önálló hitelesítő adatok konfigurálása helyett. Security Center identitás-és hozzáférés-kezelési javaslatok használata. 

- [Az egyszeri bejelentkezés ismertetése az Azure AD-vel](../active-directory/manage-apps/what-is-single-sign-on.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3,5: Multi-Factor Authentication használata az összes Azure Active Directory-alapú hozzáféréshez

**Útmutató**: engedélyezze Azure Active Directory (Azure ad) többtényezős hitelesítését, és kövesse Security Center identitás-és hozzáférés-kezelési javaslatait.

- [Többtényezős hitelesítés engedélyezése az Azure-ban](../active-directory/authentication/howto-mfa-getstarted.md)

- [Identitás és hozzáférés figyelése Azure Security Centeron belül](../security-center/security-center-identity-access.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3,6: dedikált gépek (privilegizált hozzáférési munkaállomások) használata az összes felügyeleti feladathoz

**Útmutató**: a rendszerjogosultságú hozzáférési munkaállomás (Paw) használata a többtényezős hitelesítéssel, amely a Service Fabric-fürtök és a kapcsolódó erőforrások bejelentkezésére és konfigurálására van konfigurálva.

- [További tudnivalók a privilegizált hozzáférésű munkaállomásokról](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

- [Többtényezős hitelesítés engedélyezése az Azure-ban](../active-directory/authentication/howto-mfa-getstarted.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3,7: naplózás és riasztás a gyanús tevékenységekről a rendszergazdai fiókoktól

**Útmutatás**: Azure Active Directory (Azure AD) PRIVILEGED Identity Management (PIM) használata naplók és riasztások generálásához, ha a környezetben gyanús vagy nem biztonságos tevékenység történik. Emellett az Azure AD kockázati észleléseit is használhatja a kockázatos felhasználói viselkedéssel kapcsolatos riasztások és jelentések megtekintéséhez.

- [Privileged Identity Management (PIM) üzembe helyezése](../active-directory/privileged-identity-management/pim-deployment-plan.md)

- [Az Azure AD kockázati észlelések ismertetése](../active-directory/identity-protection/overview-identity-protection.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3,8: az Azure-erőforrások kezelése csak jóváhagyott helyekről

**Útmutató**: a feltételes hozzáférés elnevezett helyeivel engedélyezheti, hogy a hozzáférés csak az IP-címtartományok vagy országok/régiók adott logikai csoportjaihoz legyen elérhető. 

- [Elnevezett helyszínek konfigurálása az Azure-ban](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="39-use-azure-active-directory"></a>3,9: a Azure Active Directory használata

**Útmutató**: a Azure Active Directory (Azure ad) használata központi hitelesítési és engedélyezési rendszerrel a Service Fabric-fürtök felügyeleti végpontjai számára való hozzáférés biztosításához. Az Azure AD az adatok védelme érdekében erős titkosítást használ a nyugalmi és a továbbítási adatokhoz. Az Azure AD emellett a felhasználó hitelesítő adatainak a sók, a kivonatok és a biztonságos tárolását is tartalmazza.

- [Azure AD-példány létrehozása és konfigurálása](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

- [Az Azure AD beállítása Service Fabric ügyfél-hitelesítéshez](service-fabric-cluster-creation-setup-aad.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás**: az [Azure biztonsági teljesítményteszt](/azure/governance/policy/samples/azure-security-benchmark) a Security Center alapértelmezett házirend-kezdeményezése, és a [Security Center ajánlásainak](/azure/security-center/security-center-recommendations)alapja. A vezérlőhöz kapcsolódó Azure Policy-definíciók Security Center automatikusan engedélyezve vannak. Az ehhez a vezérlőhöz kapcsolódó riasztásokhoz szükség lehet egy [Azure Defender](/azure/security-center/azure-defender) -csomagra a kapcsolódó szolgáltatásokhoz.

**Azure Policy beépített definíciók – Microsoft. ServiceFabric**:

[!INCLUDE [Resource Policy for Microsoft.ServiceFabric 3.9](../../includes/policy/standards/asb/rp-controls/microsoft.servicefabric-3-9.md)]

### <a name="310-regularly-review-and-reconcile-user-access"></a>3,10: a felhasználói hozzáférés rendszeres áttekintése és egyeztetése

**Útmutató**: Azure Active Directory (Azure ad) hitelesítés használata a Service Fabric-fürttel. Az Azure AD olyan naplókat biztosít, amelyek segítenek az elavult fiókok felderítésében. Emellett az Azure Identity Access Reviews használatával hatékonyan kezelheti a csoporttagságok kezelését, a vállalati alkalmazásokhoz való hozzáférést és a szerepkör-hozzárendeléseket. A felhasználó hozzáférését rendszeresen felül lehet vizsgálni, hogy csak a megfelelő felhasználók férhessenek hozzájuk.

- [Az Azure Identity hozzáférési felülvizsgálatok használata](../active-directory/governance/access-reviews-overview.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="311-alert-on-account-login-behavior-deviation"></a>3,11: riasztás a fiók bejelentkezési viselkedésének eltérése esetén

**Útmutató**: a inaktivált fiókok elérésére irányuló kísérletek figyeléséhez használja a Azure Active Directory (Azure ad) bejelentkezési és naplózási naplókat. Ezek a naplók bármely harmadik féltől származó SIEM/monitoring eszközbe integrálhatók.

Ezt a folyamatot leegyszerűsítheti az Azure AD felhasználói fiókok diagnosztikai beállításainak létrehozásával, a naplók és a bejelentkezési naplók Azure Log Analytics-munkaterületre való elküldésével. A kívánt riasztások konfigurálása az Azure Log Analytics-munkaterületen belül.

- [Azure-tevékenységnaplók integrálása az Azure Monitorba](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3,12: riasztás a fiók bejelentkezési viselkedésének eltérése esetén

**Útmutató**: az Azure Active Directory (Azure ad) kockázati és Identity Protection-funkciókkal konfigurálhatja a felhasználói identitásokkal kapcsolatos gyanús műveletekre vonatkozó automatizált válaszokat. További vizsgálat céljából az Azure Sentinelbe is betöltheti az adatmennyiséget.

- [Az Azure AD kockázatos bejelentkezéseinek megtekintése](../active-directory/identity-protection/overview-identity-protection.md)

- [Az Identity Protection kockázati házirendjeinek konfigurálása és engedélyezése](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [Az Azure Sentinel előkészítése](../sentinel/quickstart-onboard.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

## <a name="data-protection"></a>Adatvédelem

*További információ: [Azure Security Benchmark: Adatvédelem](../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4,1: bizalmas információk leltárának fenntartása

**Útmutató**: címkék használata a Service Fabric-fürt üzembe helyezéséhez kapcsolódó erőforrásokon a bizalmas adatokat tároló vagy feldolgozó Azure-erőforrások nyomon követésének támogatásához.

- [Címkék létrehozása és használata](../azure-resource-manager/management/tag-resources.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4,2: bizalmas adatok tárolására vagy feldolgozására szolgáló rendszerek elkülönítése

**Útmutató**: különálló előfizetések és/vagy felügyeleti csoportok megvalósítása fejlesztési, tesztelési és éles környezetekhez. Az erőforrásokat Virtual Network vagy alhálózattal kell elválasztani, és a megfelelő címkével kell ellátni, és egy hálózati biztonsági csoport vagy Azure Firewall számára kell biztosítani őket. A bizalmas adatok tárolására vagy feldolgozására szolgáló erőforrásoknak elég elszigeteltnek kell lenniük. A bizalmas adatok tárolására és feldolgozására Virtual Machines a szabályzatokat és eljárásokat kell kikapcsolni, ha nincs használatban. 

- [További Azure-előfizetések létrehozása](../cost-management-billing/manage/create-subscription.md)

- [Management Groups létrehozása](../governance/management-groups/create-management-group-portal.md)

- [Címkék létrehozása és használata](../azure-resource-manager/management/tag-resources.md)

- [Virtual Network létrehozása](../virtual-network/quick-create-portal.md)

- [NSG létrehozása biztonsági konfigurációval](../virtual-network/tutorial-filter-network-traffic.md)

- [Azure Firewall üzembe helyezése](../firewall/tutorial-firewall-deploy-portal.md)

- [Riasztások vagy riasztások konfigurálása és a Azure Firewall megtagadása](../firewall/threat-intel.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4,3: a bizalmas adatok jogosulatlan átvitelének figyelése és letiltása

**Útmutató**: automatikus eszköz üzembe helyezése olyan hálózati peremhálózaton, amely figyeli a bizalmas adatok jogosulatlan átvitelét, és blokkolja az ilyen átviteleket, miközben riasztást küld az információs biztonsági szakemberek számára. 

A Microsoft által felügyelt mögöttes platform esetében a Microsoft az összes vásárlói tartalmat bizalmasként kezeli, és az ügyfelek adatvesztésével és a kitettséggel szembeni védelem érdekében nagy hosszúságú. Annak biztosítása érdekében, hogy az Azure-beli ügyféladatok biztonságban maradjanak, a Microsoft végrehajtotta és karbantartja a robusztus adatvédelmi szabályozást és képességeket. 

- [Az ügyféladatok Azure-beli védelmének ismertetése](../security/fundamentals/protection-customer-data.md)

**Felelősség**: Megosztott

**Azure Security Center figyelés**: nincs

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4,4: minden bizalmas adat titkosítása az átvitel során

**Útmutató**: az összes bizalmas adat titkosítása az átvitel során. Győződjön meg arról, hogy az Azure-erőforrásokhoz csatlakozó ügyfelek képesek a TLS 1,2 vagy újabb egyeztetésére. 

Service Fabric ügyfél – csomópont kölcsönös hitelesítéshez használjon X. 509 tanúsítványt a http-kommunikáció kiszolgáló-identitásához és TLS-titkosításához. A fürtön tetszőleges számú további tanúsítvány telepíthető az alkalmazások biztonsági célokra, beleértve az alkalmazások konfigurációs értékeinek és az adatoknak a csomópontok közötti titkosítását és visszafejtését a replikáció során.
Kövesse Security Center a inaktív adatok titkosítására és az átvitel közbeni titkosításra vonatkozó ajánlásokat, ahol lehetséges. 

- [A titkosítás ismertetése az Azure-ban](https://docs.microsoft.com/azure/security/fundamentals/encryption-overview#encryption-of-data-in-transit)

- [Service Fabric fürt biztonsági forgatókönyvei](service-fabric-cluster-security.md)

- [A TLS-konfiguráció Service Fabric hibaelhárítási útmutatója](https://github.com/Azure/Service-Fabric-Troubleshooting-Guides/blob/master/Security/TLS%20Configuration.md)

**Felelősség**: Megosztott

**Azure Security Center figyelés**: nincs

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4,5: aktív felderítési eszköz használata a bizalmas adatok azonosítására

**Útmutató**: az adatazonosítási, besorolási és veszteség-megelőzési funkciók még nem érhetők el az Azure Storage vagy a számítási erőforrások számára. Külső gyártótól származó megoldás implementálása, ha az szükséges a megfelelőség szempontjából.

A Microsoft által felügyelt mögöttes platform esetében a Microsoft az összes vásárlói tartalmat bizalmasként kezeli, és az ügyfelek adatvesztésével és a kitettséggel szembeni védelem érdekében nagy hosszúságú. Annak biztosítása érdekében, hogy az Azure-beli ügyféladatok biztonságban maradjanak, a Microsoft végrehajtotta és karbantartja a robusztus adatvédelmi szabályozást és képességeket.

- [Az ügyféladatok Azure-beli védelmének ismertetése](../security/fundamentals/protection-customer-data.md)

**Felelősség**: Megosztott

**Azure Security Center figyelés**: nincs

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4,7: a gazdagép-alapú adatvesztés-megelőzés használata a hozzáférés-vezérlés kikényszeríthető

**Útmutató**: a bizalmas adatokat tároló vagy feldolgozó Service Fabric fürtök esetében a címkével megjelölheti a fürtöt és a kapcsolódó erőforrásokat. Az adatazonosítási, besorolási és veszteség-megelőzési funkciók még nem érhetők el az Azure Storage vagy a számítási erőforrások számára. Külső gyártótól származó megoldás implementálása, ha az szükséges a megfelelőség szempontjából.

A Microsoft által felügyelt mögöttes platform esetében a Microsoft az összes vásárlói tartalmat bizalmasként kezeli, és az ügyfelek adatvesztésével és a kitettséggel szembeni védelem érdekében nagy hosszúságú. Annak biztosítása érdekében, hogy az Azure-beli ügyféladatok biztonságban maradjanak, a Microsoft végrehajtotta és karbantartja a robusztus adatvédelmi szabályozást és képességeket.

- [Az ügyféladatok Azure-beli védelmének ismertetése](../security/fundamentals/protection-customer-data.md)

**Felelősség**: Megosztott

**Azure Security Center figyelés**: nincs

### <a name="48-encrypt-sensitive-information-at-rest"></a>4,8: bizalmas adatok titkosítása a nyugalmi állapotban

**Útmutató**: a titkosítás használata az összes Azure-erőforráson. A Microsoft azt javasolja, hogy az Azure kezelje a titkosítási kulcsokat, azonban lehetőség van arra, hogy a saját kulcsait bizonyos példányokban kezelje. 

- [Inaktív adatok Azure-ban való titkosításának ismertetése](../security/fundamentals/encryption-atrest.md)  

- [Az ügyfél által felügyelt titkosítási kulcsok konfigurálása](../storage/common/customer-managed-keys-configure-key-vault.md)

- [Lemez titkosításának engedélyezése az Azure Service Fabric-fürtcsomópontok számára a Windows rendszerben](service-fabric-enable-azure-disk-encryption-windows.md)

- [Lemez titkosításának engedélyezése az Azure Service Fabric-fürtcsomópontok számára Linux rendszerben](service-fabric-enable-azure-disk-encryption-linux.md)

**Felelősség**: Ügyfél

**Azure Security Center monitorozás**: az [Azure biztonsági teljesítményteszt](/azure/governance/policy/samples/azure-security-benchmark) a Security Center alapértelmezett házirend-kezdeményezése, és a [Security Center ajánlásainak](/azure/security-center/security-center-recommendations)alapja. A vezérlőhöz kapcsolódó Azure Policy-definíciók Security Center automatikusan engedélyezve vannak. Az ehhez a vezérlőhöz kapcsolódó riasztásokhoz szükség lehet egy [Azure Defender](/azure/security-center/azure-defender) -csomagra a kapcsolódó szolgáltatásokhoz.

**Azure Policy beépített definíciók – Microsoft. ServiceFabric**:

[!INCLUDE [Resource Policy for Microsoft.ServiceFabric 4.8](../../includes/policy/standards/asb/rp-controls/microsoft.servicefabric-4-8.md)]

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4,9: a kritikus Azure-erőforrások változásainak naplózása és riasztása

**Útmutató**: a Azure monitor és az Azure-tevékenység naplójának használata riasztások létrehozásához, amikor a módosítások a kritikus Azure-erőforrásokra vonatkoznak. 

- [Riasztások létrehozása az Azure-tevékenységek naplózási eseményeihez](/azure/azure-monitor/platform/alerts-activity-log)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

## <a name="vulnerability-management"></a>Biztonságirés-kezelés

*További információért lásd az [Azure biztonsági teljesítményteszt: biztonsági rés kezelése](../security/benchmarks/security-control-vulnerability-management.md)című témakört.*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5,1: automatikus biztonsági rések vizsgálatára szolgáló eszközök futtatása

**Útmutató**: rendszeresen futtassa a Service Fabric fault Analysis Service-t és a Chaos-szolgáltatásokat a fürtben lévő hibák szimulálása érdekében, hogy felmérje a szolgáltatások megbízhatóságát és megbízhatóságát.

Az Azure Virtual Machines és a Container images szolgáltatásban a sebezhetőségi felmérések elvégzéséhez Security Center ajánlásokat követve. 

Külső gyártótól származó megoldás használata a sebezhetőségi felmérések hálózati eszközökön és webalkalmazásokon való elvégzéséhez. Távoli vizsgálatok végrehajtásakor ne használjon egyetlen, örökös rendszergazdai fiókot. Érdemes lehet a beolvasni a JIT üzembe helyezési módszertant alkalmazni. A vizsgálati fiók hitelesítő adatait védeni kell, figyelni kell, és csak a sebezhetőségi vizsgálathoz kell használni. 

- [A Service Fabric fault Analysis Service bemutatása](service-fabric-testability-overview.md)

- [Vezérelt káosz indukálása Service Fabric-fürtökben](service-fabric-controlled-chaos.md)

- [Azure Security Center sebezhetőségi felméréssel kapcsolatos javaslatok megvalósítása](../security-center/deploy-vulnerability-assessment-vm.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5,2: az operációs rendszer automatikus javításának felügyeleti megoldásának telepítése

**Útmutató**: engedélyezze az operációs rendszer rendszerképének automatikus frissítését a Service Fabric-fürt virtuálisgép-méretezési csoportjain. 

Az operációs rendszer javításait az éles környezetbe való helyezés előtt is tesztelheti, ha a manuális triggert használja a méretezési csoport operációsrendszer-lemezképének frissítéséhez. Vegye figyelembe, hogy a manuális trigger beállítás nem biztosít beépített visszaállítást. OPERÁCIÓSRENDSZER-javítások figyelése a Azure Automation Update Management használatával. 

- [A Service Fabric fürtcsomópontok javításának kezelése](https://docs.microsoft.com/azure/service-fabric/service-fabric-best-practices-infrastructure-as-code#azure-virtual-machine-operating-system-automatic-upgrade-configuration)

- [Az operációs rendszer rendszerképének automatikus frissítése Azure-beli virtuálisgép-méretezési csoportokkal](../virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade.md)

- [Virtuális gépek naprakészen tartása a legújabb méretezési csoport modelljével](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-upgrade-scale-set#how-to-bring-vms-up-to-date-with-the-latest-scale-set-model)

- [Azure Automation Update Management áttekintése](/azure/automation/update-management/update-mgmt-overview)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="53-deploy-automated-patch-management-solution-for-third-party-software-titles"></a>5,3: a harmadik féltől származó szoftverek címeihez készült automatizált javítási megoldás üzembe helyezése

**Útmutató**: az operációs rendszer rendszerképének automatikus verziófrissítésének engedélyezése az Azure Service Fabric-fürt virtuálisgép-méretezési csoportjain. A patch-előkészítési alkalmazás (POA) egy alternatív megoldás, amely az Azure-on kívül üzemeltetett Service Fabric-fürtökhöz készült. A POA használható Azure-fürtökkel, és további üzemeltetési terheléssel.

- [A Service Fabric fürtcsomópontok javításának kezelése](https://docs.microsoft.com/azure/service-fabric/service-fabric-best-practices-infrastructure-as-code#azure-virtual-machine-operating-system-automatic-upgrade-configuration)

- [Az operációs rendszer rendszerképének automatikus frissítése Azure-beli virtuálisgép-méretezési csoportokkal](../virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade.md)

- [Az operációsrendszer-javítási ütemterv konfigurálása Service Fabric fürtökhöz](service-fabric-patch-orchestration-application.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5,4: a biztonsági rések keresésének összehasonlítása

**Útmutató**: a vizsgálati eredmények konzisztens időközönkénti exportálása és az eredmények összehasonlítása annak ellenőrzéséhez, hogy a biztonsági rések szervizelése megtörtént-e. A Security Center által javasolt sebezhetőségi kezelési javaslatok használatakor a kiválasztott megoldás portálján megtekintheti a korábbi vizsgálati adataikat.

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5,5: kockázatértékelési folyamat használatával rangsorolhatja a felderített biztonsági rések szervizelését

**Útmutató**: általános kockázati pontozási programot (például gyakori sebezhetőségi pontozási rendszer) vagy a harmadik féltől származó ellenőrzési eszköz által biztosított alapértelmezett kockázati minősítéseket használjon.

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

## <a name="inventory-and-asset-management"></a>Leltár-és eszközfelügyelet

*További információt az [Azure biztonsági teljesítményteszt: leltár és eszközkezelés](../security/benchmarks/security-control-inventory-asset-management.md)című témakörben talál.*

### <a name="61-use-automated-asset-discovery-solution"></a>6,1: automatikus eszköz-felderítési megoldás használata

**Útmutató**: az Azure Resource Graph használatával lekérdezheti vagy felderítheti az összes erőforrást (például számítási, tárolási, hálózati, portok és protokollok stb.) az előfizetés (ok) n belül. Győződjön meg arról, hogy a bérlőben a megfelelő (olvasási) engedélyek szerepelnek, valamint az összes Azure-előfizetés, valamint az előfizetésekben lévő erőforrások számbavétele.

Bár a klasszikus Azure-erőforrások felderítése az erőforrás-gráfon keresztül lehetséges, erősen ajánlott a Azure Resource Manager erőforrások létrehozása és használata.

- [Lekérdezések létrehozása az Azure Resource Graph használatával](../governance/resource-graph/first-query-portal.md)

- [Azure-előfizetések megtekintése](https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-4.8.0&amp;preserve-view=true)

- [Az Azure RBAC ismertetése](../role-based-access-control/overview.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="62-maintain-asset-metadata"></a>6,2: az eszköz metaadatainak fenntartása

**Útmutató**: címkéket alkalmazhat az Azure-erőforrásokra, így a metaadatok logikailag rendszerezve lesznek a besorolásban.

- [Címkék létrehozása és használata](../azure-resource-manager/management/tag-resources.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="63-delete-unauthorized-azure-resources"></a>6,3: jogosulatlan Azure-erőforrások törlése

**Útmutató**: a címkézés, a felügyeleti csoportok és az elkülönített előfizetések használata, ha szükséges, az eszközök rendszerezéséhez és nyomon követéséhez. Rendszeres időközönként egyeztetheti a leltárt, és gondoskodhat arról, hogy a jogosulatlan erőforrások törlése az előfizetésből időben történjen.

- [További Azure-előfizetések létrehozása](../cost-management-billing/manage/create-subscription.md)

- [Management Groups létrehozása](../governance/management-groups/create-management-group-portal.md)

- [Címkék létrehozása és használata](../azure-resource-manager/management/tag-resources.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6,4: a jóváhagyott Azure-erőforrások leltárának meghatározása és karbantartása

**Útmutató**: jóváhagyott Azure-erőforrások és jóváhagyott szoftverek definiálása számítási erőforrásokhoz.

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="65-monitor-for-unapproved-azure-resources"></a>6,5: a nem jóváhagyott Azure-erőforrások figyelése

**Útmutató**: a Azure Policy használatával korlátozásokat állíthat be az ügyfél-előfizetésekben létrehozható erőforrások típusára a következő beépített szabályzat-definíciók használatával:

Nem engedélyezett erőforrástípusok

Engedélyezett erőforrástípusok

Az Azure Resource Graph használatával lekérdezheti vagy felderítheti az előfizetésében lévő erőforrásokat. Győződjön meg arról, hogy a környezetben lévő összes Azure-erőforrás jóvá van hagyva.

- [Az Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md)

- [Lekérdezések létrehozása az Azure Graph használatával](../governance/resource-graph/first-query-portal.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6,6: a nem jóváhagyott szoftveralkalmazások figyelése a számítási erőforrásokon belül

**Útmutató**: harmadik féltől származó megoldás implementálása a fürt csomópontjainak a nem jóváhagyott szoftveralkalmazások számára történő figyeléséhez.

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6,7: nem jóváhagyott Azure-erőforrások és szoftveralkalmazások eltávolítása

**Útmutató**: az Azure Resource Graph használatával lekérdezheti és felderítheti az összes erőforrást (például a számítási, tárolási, hálózati, portok és protokollok stb.), beleértve a Service Fabric-fürtöket az előfizetéseken belül. Távolítsa el a felderített nem jóváhagyott Azure-erőforrásokat. Service Fabric fürtcsomópontok esetében hozzon létre egy harmadik féltől származó megoldást a nem jóváhagyott szoftverek eltávolítására vagy riasztására.

- [Lekérdezések létrehozása az Azure Graph használatával](../governance/resource-graph/first-query-portal.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="68-use-only-approved-applications"></a>6,8: csak jóváhagyott alkalmazások használata

**Útmutató**: Service Fabric fürtcsomópontok esetében hozzon létre egy harmadik féltől származó megoldást, amely megakadályozza a jogosulatlan szoftverek futtatását.

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="69-use-only-approved-azure-services"></a>6,9: csak jóváhagyott Azure-szolgáltatások használata

**Útmutató**: a Azure Policy használatával korlátozhatja, hogy mely szolgáltatásokat lehet kiépíteni a környezetében.

- [Az Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md)

- [Adott erőforrástípus megtagadása a következővel Azure Policy](https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#general)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6,10: a jóváhagyott szoftverek leltárának fenntartása

**Útmutató**: az Azure Service Fabric fürtcsomópontok esetében egy harmadik féltől származó megoldás implementálása, amely megakadályozza a jogosulatlan fájltípusok végrehajtását.

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6,11: korlátozza a felhasználók képességét a Azure Resource Manager való interakcióra

**Útmutató**: az Azure feltételes hozzáférés használatával korlátozhatja, hogy a felhasználók képesek legyenek az Azure Resources Managerrel való interakcióra az "Microsoft Azure felügyelet" alkalmazás "hozzáférés letiltása" beállításával. 

- [Feltételes hozzáférés konfigurálása az Azure Resources Manager elérésének blokkolásához](../role-based-access-control/conditional-access-azure-management.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6,12: korlátozza a felhasználók számára a parancsfájlok végrehajtásának lehetőségét a számítási erőforrásokon belül

**Útmutató**: az operációs rendszerre jellemző konfigurációk vagy harmadik féltől származó erőforrások használata arra, hogy korlátozza a felhasználók számára az Azure számítási erőforrásokon belüli parancsfájlok végrehajtásának lehetőségét.

- [Például a PowerShell-parancsfájlok végrehajtásának szabályozása Windows-környezetekben](https://docs.microsoft.com/powershell/module/microsoft.powershell.security/set-executionpolicy?view=powershell-7&amp;preserve-view=true)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6,13: fizikailag vagy logikailag elkülöníthető a nagy kockázatú alkalmazások

**Útmutató**: az üzleti műveletekhez szükséges szoftverek, de nagyobb kockázatot jelenthetnek a szervezet számára, el kell különíteni a saját virtuális gépén és/vagy virtuális hálózatán belül, és megfelelően biztonságossá kell tennie egy Azure Firewall vagy hálózati biztonsági csoporttal. 

- [Virtuális hálózat létrehozása](../virtual-network/quick-create-portal.md) 

- [NSG létrehozása biztonsági konfigurációval](../virtual-network/tutorial-filter-network-traffic.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

## <a name="secure-configuration"></a>Biztonságos konfiguráció

*További információt az [Azure biztonsági teljesítményteszt: biztonságos konfiguráció](../security/benchmarks/security-control-secure-configuration.md)című témakörben talál.*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7,1: biztonságos konfigurációk létrehozása az összes Azure-erőforráshoz

**Útmutató**: Azure Policy aliasok használata a "Microsoft. ServiceFabric" névtérben egyéni szabályzatok létrehozásához a Service Fabric-fürt hálózati konfigurációjának naplózásához vagy érvényesítéséhez.

- [Az elérhető Azure Policy aliasok megtekintése](https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-4.8.0&amp;preserve-view=true)

- [Az Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="72-establish-secure-operating-system-configurations"></a>7,2: biztonságos operációsrendszer-konfigurációk létrehozása

**Útmutató**: Service Fabric operációs rendszer lemezképeit a Microsoft felügyeli és tartja karban. A fürtcsomópontok operációs rendszerének biztonságos konfigurációjának végrehajtásáért felelős ügyfél.

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="73-maintain-secure-azure-resource-configurations"></a>7,3: biztonságos Azure-erőforrás-konfigurációk karbantartása

**Útmutató**: az Azure Service Fabric-fürtök és a kapcsolódó erőforrások biztonságos beállításainak érvénybe léptetéséhez használja a Azure Policy [deny] és a [telepítés ha nem létezik] hatásokat.

- [Az Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md)

- [Azure Policy effektusok ismertetése](../governance/policy/concepts/effects.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="74-maintain-secure-operating-system-configurations"></a>7,4: az operációs rendszer biztonságos konfigurációjának fenntartása

**Útmutató**: a Microsoft által felügyelt és karbantartott fürt operációs rendszer lemezképének Service Fabric. Az ügyfél az operációsrendszer-szintű állapot konfigurációjának végrehajtásáért felelős.

**Felelősség**: Megosztott

**Azure Security Center figyelés**: nincs

### <a name="75-securely-store-configuration-of-azure-resources"></a>7,5: az Azure-erőforrások biztonságos tárolása

**Útmutató**: ha egyéni Azure Policy-definíciókat használ, az Azure DevOps vagy az Azure Repos segítségével biztonságosan tárolhatja és kezelheti a kódot.

- [Kód tárolása az Azure DevOps](https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops&amp;preserve-view=true)

- [Az Azure Repos dokumentációja](https://docs.microsoft.com/azure/devops/repos/?view=azure-devops&amp;preserve-view=true)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="76-securely-store-custom-operating-system-images"></a>7,6: az egyéni operációsrendszer-lemezképek biztonságos tárolása

**Útmutató**: Ha egyéni lemezképeket használ, az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC) használatával biztosíthatja, hogy csak a jogosult felhasználók férhessenek hozzá a lemezképekhez. A tárolói lemezképek esetében tárolja azokat Azure Container Registryban, és használja ki az Azure RBAC annak biztosítására, hogy csak a jogosult felhasználók férhessenek hozzá a lemezképekhez. 

- [Az Azure RBAC ismertetése](../role-based-access-control/rbac-and-directory-admin-roles.md) 

- [Az Azure-RBAC megismerése Container Registry](../container-registry/container-registry-roles.md) 

- [Az Azure RBAC konfigurálása](../role-based-access-control/quickstart-assign-role-user-portal.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7,7: az Azure-erőforrások konfigurációs felügyeleti eszközeinek üzembe helyezése

**Útmutató**: Azure Policy aliasok használata a "Microsoft. ServiceFabric" névtérben egyéni szabályzatok létrehozásához a riasztáshoz, a naplózáshoz és a rendszer-konfigurációk kényszerítéséhez. Emellett dolgozzon ki egy folyamatot és egy folyamatot a házirend-kivételek kezeléséhez.

- [Az Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7,9: automatikus konfigurációs monitorozás megvalósítása Azure-erőforrásokhoz

**Útmutató**: Azure Policy aliasok használata a "Microsoft. ServiceFabric" névtérben egyéni szabályzatok létrehozásához a Service Fabric-fürt konfigurációjának naplózásához vagy érvényesítéséhez.

- [Az elérhető Azure Policy aliasok megtekintése](https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-4.8.0&amp;preserve-view=true)

- [Az Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7,10: az operációs rendszerek automatikus konfiguráció-figyelésének megvalósítása

**Útmutató**: a Security Center használatával elvégezheti a tárolók operációs rendszerének és Docker-beállításainak alapkonfigurációját. 

- [Az Azure Security Center tárolókra vonatkozó ajánlásainak értelmezése](../security-center/container-security.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="711-manage-azure-secrets-securely"></a>7,11: az Azure-titkok biztonságos kezelése

**Útmutató**: a Managed Service Identity együttes használata a Azure Key Vault a Felhőbeli alkalmazások titkos felügyeletének egyszerűsítése és biztonságossá tétele érdekében. 

- [Felügyelt identitások használata az Azure-hoz a Service Fabric](concepts-managed-identity.md)

- [Felügyelt identitás támogatásának konfigurálása új Service Fabric-fürthöz](configure-new-azure-service-fabric-enable-managed-identity.md)

- [Felügyelt identitás használata Service Fabric alkalmazással](how-to-managed-identity-service-fabric-app-code.md)

- [Service Fabric alkalmazások KeyVaultReference támogatása](service-fabric-keyvault-references.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="712-manage-identities-securely-and-automatically"></a>7,12: az identitások biztonságos és automatikus kezelése

**Útmutató**: felügyelt identitások használhatók az Azure-ban telepített Service Fabric-fürtökben, valamint az Azure-erőforrásként üzembe helyezett alkalmazásokhoz. A felügyelt identitások lehetővé teszik bármely olyan szolgáltatás hitelesítését, amely támogatja a Azure Active Directory (Azure AD) hitelesítést, beleértve a Key Vault is, a kódban szereplő hitelesítő adatok nélkül.

- [Felügyelt identitások használata az Azure-hoz a Service Fabric](concepts-managed-identity.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="713-eliminate-unintended-credential-exposure"></a>7,13: a hitelesítő adatok nem szándékolt expozíciójának megszüntetése

**Útmutató**: Ha az Azure Service Fabric-telepítéshez kapcsolódó kódokat használ, a hitelesítő adatoknak a kódban való azonosításához a hitelesítőadat-olvasót is megvalósíthatja. A Credential Scanner a felfedezett hitelesítő adatok biztonságosabb helyre, például az Azure Key Vaultba való áthelyezésére is javaslatot tesz.

A Azure Key Vault használatával automatikusan elforgathatja Service Fabric-fürt tanúsítványait.

- [A hitelesítő adatok beolvasójának beállítása](https://secdevtools.azurewebsites.net/helpcredscan.html)

- [Tanúsítványkezelő Service Fabric-fürtökben](https://docs.microsoft.com/azure/service-fabric/cluster-security-certificate-management#certificate-rotation)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

## <a name="malware-defense"></a>Kártevők elleni védelem

*További információt az [Azure biztonsági teljesítményteszt: kártevők elleni védelem](../security/benchmarks/security-control-malware-defense.md)című témakörben talál.*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8,1: központilag felügyelt kártevő szoftverek használata

**Útmutató**: alapértelmezés szerint a Windows Defender víruskereső a windows Server 2016 rendszerre van telepítve. A felhasználói felület néhány SKU-ban alapértelmezés szerint telepítve van, de nem szükséges.

Ha nem a Windows Defendert használja, tekintse meg a konfigurációs szabályok antimalware-dokumentációját. A Windows Defender Linux rendszeren nem támogatott.

- [A Windows Defender víruskereső ismertetése Windows Server 2016 rendszeren](/windows/security/threat-protection/windows-defender-antivirus/windows-defender-antivirus-on-windows-server-2016)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

## <a name="data-recovery"></a>Adat-helyreállítás

*További információt az [Azure biztonsági teljesítményteszt: adat-helyreállítás](../security/benchmarks/security-control-data-recovery.md)című témakörben talál.*

### <a name="91-ensure-regular-automated-back-ups"></a>9,1: rendszeres automatizált biztonsági másolatok biztosítása

**Útmutató**: a Service Fabric biztonsági mentési és visszaállítási szolgáltatása lehetővé teszi az állapot-nyilvántartó szolgáltatásokban tárolt információk egyszerű és automatikus biztonsági mentését. Az alkalmazásadatok rendszeres biztonsági mentése alapvető fontosságú az adatvesztés és a szolgáltatás nem rendelkezésre állása elleni védelemhez. Service Fabric egy opcionális biztonsági mentési és visszaállítási szolgáltatást biztosít, amely lehetővé teszi az állapot-nyilvántartó Reliable Services (beleértve a Actor Servicest is) rendszeres biztonsági mentését anélkül, hogy további kódokat kellene írnia. Emellett elősegíti a korábban készített biztonsági mentések visszaállítását is.

- [Rendszeres biztonsági mentés és visszaállítás egy Azure Service Fabric-fürtben](service-fabric-backuprestoreservice-quickstart-azurecluster.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: teljes rendszerbiztonsági mentés és minden ügyfél által felügyelt kulcs biztonsági mentése

**Útmutató**: engedélyezze a Backup Restore Service szolgáltatást a Service Fabric-fürtön, és hozzon létre biztonsági mentési szabályzatokat az állapot-nyilvántartó szolgáltatások rendszeres és igény szerinti biztonsági mentéséhez. Az ügyfél által felügyelt kulcsok biztonsági mentése Azure Key Vaulton belül.

- [Rendszeres biztonsági mentés és visszaállítás egy Azure Service Fabric-fürtben](service-fabric-backuprestoreservice-quickstart-azurecluster.md)

- [Az Azure Service Fabric rendszeres biztonsági mentési konfigurációjának ismertetése](service-fabric-backuprestoreservice-configure-periodic-backup.md)

- [Key Vault-kulcsok biztonsági mentése az Azure-ban](https://docs.microsoft.com/powershell/module/az.keyvault/backup-azkeyvaultkey?view=azps-4.8.0&amp;preserve-view=true)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: az összes biztonsági másolat ellenőrzése, beleértve az ügyfél által felügyelt kulcsokat

**Útmutató**: a biztonsági mentési konfigurációs információk és a rendelkezésre álló biztonsági másolatok rendszeres áttekintésével gondoskodhat arról, hogy a visszaállítás a biztonsági mentési visszaállítási szolgáltatásból is elvégezhető legyen. Tesztelje az ügyfél által felügyelt kulcsok biztonsági mentésének visszaállítását.

- [Az Azure Service Fabric rendszeres biztonsági mentési konfigurációjának ismertetése](service-fabric-backuprestoreservice-configure-periodic-backup.md)

- [Biztonsági mentés visszaállítása az Azure-ban Service Fabric](service-fabric-backup-restore-service-trigger-restore.md)

- [Key Vault-kulcsok visszaállítása az Azure-ban](https://docs.microsoft.com/powershell/module/az.keyvault/restore-azkeyvaultkey?view=azps-4.8.0&amp;preserve-view=true)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: a biztonsági másolatok és az ügyfél által felügyelt kulcsok védelmének biztosítása

**Útmutató**: a Service Fabric Backup visszaállítási szolgáltatásból származó biztonsági másolatok Azure Storage-fiókot használnak az előfizetésben. Az Azure Storage minden olyan adattárolót titkosít, amely egy Storage-fiókban található. Alapértelmezés szerint az adattitkosítás a Microsoft által kezelt kulcsokkal történik. A titkosítási kulcsok további szabályozásához megadhatja az ügyfél által felügyelt kulcsokat a tárolási adat titkosításához.

Ha ügyfél által felügyelt kulcsokat használ, győződjön meg arról, hogy a Key Vaultban Soft-Delete engedélyezve van a kulcsok véletlen vagy rosszindulatú törléssel szembeni védelemhez.

- [Azure Storage-titkosítás inaktív állapotban](../storage/common/storage-service-encryption.md)

- [A Soft-Delete engedélyezése Key Vault](../storage/blobs/soft-delete-blob-overview.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

## <a name="incident-response"></a>Incidensmegoldás

*További információ: [Azure Security Benchmark: Incidensek kezelése](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10,1: incidens-válaszi útmutató létrehozása

**Útmutató**: incidensek kifejlesztése útmutató a szervezet számára. Győződjön meg arról, hogy van olyan írásos incidens-válasz, amely meghatározza a személyzet összes szerepkörét, valamint az incidensek kezelésének és kezelésének fázisait az incidensek vizsgálatát követően. 

- [Útmutató a saját biztonsági incidensek megoldási folyamatának létrehozásához](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/) 

- [Microsoft Security Response Center – incidens anatómiája](https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/) 

- [A NIST számítógépes biztonsági incidensek kezelésével kapcsolatos útmutató a saját incidens-válasz tervének létrehozásához](https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10,2: incidensek pontozásának és rangsorolási eljárásának létrehozása

**Útmutató**: a Security Center súlyosságot rendel az egyes riasztásokhoz, hogy a prioritások alapján ki lehessen deríteni, hogy mely riasztásokat kell először megvizsgálni. A súlyosság azon alapul, hogy az Security Center milyen mértékben szerepel a riasztások kijavításához használt mérőszámban, illetve a riasztást eredményező tevékenységen belül rosszindulatú szándékkal.

Emellett megadhatja a címkéket használó előfizetéseket, és létrehozhat egy elnevezési rendszert az Azure-erőforrások azonosításához és kategorizálásához, különösen a bizalmas adatok feldolgozásához.  Az Ön felelőssége, hogy rangsorolja a riasztások szervizelését az Azure-erőforrások és-környezet kritikus jellemzői alapján, ahol az incidens történt. 

- [Biztonsági riasztások az Azure Security Centerben](../security-center/security-center-alerts-overview.md) 

- [Címkék használata az Azure-erőforrások rendszerezéséhez](../azure-resource-manager/management/tag-resources.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="103-test-security-response-procedures"></a>10,3: biztonsági reagálási eljárások tesztelése

**Útmutató**: a rendszerek incidensek reagálási képességeinek rendszeres tesztelésére szolgáló gyakorlatok végrehajtása. Azonosítsa a gyenge pontokat és réseket, és szükség esetén dolgozza át a tervet. 

- [NIST-útmutató az IT-csomagok és-képességek teszteléséhez, betanításához és alkalmazásához](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10,4: biztonsági incidensek elérhetőségének biztosítása és riasztási értesítések konfigurálása biztonsági incidensekhez

**Útmutató**: a Microsoft a biztonsági incidensek elérhetőségi adatait arra használja fel, hogy felvegye Önnel a kapcsolatot, ha a Microsoft Security Response Center (MSRC) felfedi, hogy az adatokat egy törvénytelen vagy jogosulatlan fél is hozzáférte. A problémák megoldása érdekében tekintse át az incidenseket a tény után. 

- [Az Azure Security Center biztonsági kapcsolattartójának beállítása](../security-center/security-center-provide-security-contact-details.md)

**Felelősség**: Ügyfél

**Azure Security Center figyelés**: nincs

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10,5: biztonsági riasztások beépítése az incidensek gyorsreagáló rendszerébe

**Útmutató**: az Security Center-riasztások és-javaslatok exportálása a folyamatos exportálás funkció használatával. A folyamatos exportálás lehetővé teszi a riasztások és javaslatok manuális és folyamatos exportálását. Használhatja a Security Center adatösszekötőt a riasztások a Sentinelbe való továbbításához.

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

- [Az Azure Security Benchmark v2 áttekintésének](/azure/security/benchmarks/overview) megtekintése
- További tudnivalók az [Azure biztonsági alapterveiről](/azure/security/benchmarks/security-baselines-overview)
